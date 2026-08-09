# TianGuan13 · 叛徒（Traitor）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/traitor/`（7 文件 1,100 行）+ `code/modules/uplink/`（20 文件 3,746 行）
> **类型**: 单人潜伏型反派（最经典）｜**难度**: ★★★（目标+Uplink 搭配决定强度）
> **一句话**: 你是辛迪加叛徒——用 **TC 电信水晶**买 Uplink 装备（见 [Uplink 系统百科](TianGuan13-上行链路Uplink系统百科.md)），完成**叛徒目标**（刺杀/偷窃/破坏/终局 3 选）。
> **本版全量审计**: 旧版目标系统仅一句"任务目标+终局目标"——**本版从源码全量提取目标生成机制（概率/类型/终局判定）**，并已将共用 Uplink 部分拆出为独立篇。

---

## 目录

- [一、核心机制](#一核心机制)
- [二、叛徒目标系统（全量）](#二叛徒目标系统全量)
- [三、目标生成流程](#三目标生成流程)
- [四、目标类型全录](#四目标类型全录)
  - [4.1 偷窃目标物品池全录](#41-偷窃目标物品池全录steal-物品池)
- [五、终局目标详解](#五终局目标详解)
- [六、承包商契约](#六承包商契约)
  - [6.1 合同生成与赎金](#61-合同生成与赎金)
  - [6.2 提取与受害者体验](#62-提取与受害者体验)
- [七、暗号与风味](#七暗号与风味)
  - [7.1 辛迪加暗号系统](#71-辛迪加暗号系统codewords)
  - [7.2 雇主系统](#72-雇主系统flavor)
  - [7.3 其他风味](#73-其他风味)
- [八、Uplink 解锁与安全机制](#八uplink-解锁与安全机制)
- [九、声誉与全局进度系统](#九声誉与全局进度系统)
- [十、职业目标与特殊目标](#十职业目标与特殊目标)
- [十一、宣传组件（Demoraliser）](#十一宣传组件demoraliser)
- [十二、对战攻略](#十二对战攻略)
- [十三、数值速查表](#十三数值速查表)

> **Uplink 系统**（TC 货币/395 件物品全录/购买规则）已独立成篇：[上行链路 Uplink 系统百科](TianGuan13-上行链路Uplink系统百科.md)

---

## 一、核心机制

| 机制 | 值 |
|---|---|
| 目标 | 叛徒目标（职业/通用）+ **终局目标** |
| 终局目标 | **劫机**（hijack）/ **殉道**（martyr）/ **逃脱**（escape）|
| 雇主 | 75% 辛迪加 / 25% Nanotrasen（flavor 系统）|
| Uplink | 授予（UPLINK_TRAITORS 标志，见 Uplink 百科）|
| 折扣销售 | 每局 4-6 件折扣物品 |
| 进度系统 | 声誉（progression）解锁高级物品 |
| 自杀口号 | "FOR THE SYNDICATE!!" |
| 暗号 | 辛迪加暗号（蓝/红识别）|

**NOVA EDIT**：`can_assign_self_objectives = FALSE`（原版 TRUE，NOVA 收紧——叛徒不能自己换目标，需管理员批准）

---

## 二、叛徒目标系统（全量）

**代码**: `datum_traitor.dm`（354 行）forge_traitor_objectives / forge_ending_objective

### 2.1 目标数量

| 项 | 值 |
|---|---|
| 目标上限 | `traitor_objectives_amount`（默认 **2**，min 0）|
| 劫机追加 | 满足条件时劫机目标占 1 个名额 |
| 职业目标 | 1 个名额可被职业目标占用（40% 概率）|

### 2.2 目标生成概率

| 概率 | 值 | 说明 |
|---|---|---|
| **JOB_PROB** | **40%** | 生成职业目标（assigned_role.generate_traitor_objective）|
| **KILL_PROB** | **50%** | 生成杀人系目标（刺杀/流放/摧毁 AI）|
| **DESTROY_AI_PROB** | 100/人数 | 有活跃 AI 时摧毁 AI 概率 |
| **MAROON_PROB** | 30% | 流放目标（在杀人系内）|
| **HIJACK_PROB** | **10%** | 劫机目标（玩家≥30 时）|
| **MARTYR_PROB** | **0%** | 殉道目标（NOVA 改为 0，原版 20%）|

> **NOVA 关键改动**：`MARTYR_PROB 0`（原版 20）——NOVA 下叛徒不会获得殉道终局。

### 2.3 生成流程

```
forge_traitor_objectives:
① 玩家数 ≥30 且 10% 概率 → 劫机者（is_hijacker=TRUE，占 1 名额）
② objective_limit = 目标上限（默认 2）
③ 先尝试职业目标（JOB_PROB 40%）
④ 循环生成通用目标直到满上限：
   - 40% 职业目标（若有）
   - 50% 杀人系：→ 有 AI 时 100/人数 摧毁 AI
              → 否则 30% 流放
              → 否则刺杀
   - 否则：偷窃目标
forge_ending_objective:
⑤ 劫机者 → 劫机终局
⑥ 否则：目标全部 martyr_compatible 且 MARTYR_PROB（NOVA=0）→ 殉道
⑦ 否则 → 逃脱终局
```

---

## 三、目标生成流程（逐步）

### 3.1 劫机判定（HIJACK）

| 条件 | 值 |
|---|---|
| 玩家人数 | ≥ **30**（HIJACK_MIN_PLAYERS）|
| 概率 | **10%**（HIJACK_PROB）|

满足 → `is_hijacker = TRUE`，终局 = 劫机

### 3.2 通用目标生成（forge_single_generic_objective）

| 分支 | 概率 | 目标 |
|---|---|---|
| 职业目标 | 40%（JOB_PROB）| 职业专属目标（若存在）|
| 杀人系 | 50%（KILL_PROB）| 见 3.3 |
| 其他 | 50% | **偷窃**（steal）|

### 3.3 杀人系分支（KILL_PROB 内）

| 分支 | 概率 | 目标 |
|---|---|---|
| 摧毁 AI | 100/人数（有活跃 AI 时）| **摧毁**（destroy）|
| 流放 | 30%（MAROON_PROB）| **流放**（maroon）|
| 默认 | — | **刺杀**（assassinate）|

### 3.4 终局生成（forge_ending_objective）

| 条件 | 终局 |
|---|---|
| is_hijacker | **劫机**（hijack）|
| 全部目标 martyr_compatible + MARTYR_PROB（NOVA 0%）| **殉道**（martyr）|
| 默认 | **逃脱**（escape）|

---

## 四、目标类型全录

> 目标类型基于 `/datum/objective` 体系（`code/game/gamemodes/objective.dm`）

| # | 目标 | 说明 |
|---|---|---|
| 1 | **Assassinate 刺杀** | 杀掉指定目标（经典叛徒目标）|
| 2 | **Steal 偷窃** | 偷指定物品（objective_items）|
| 3 | **Maroon 流放** | 把目标流放到无法返回的地方（如小行星）|
| 4 | **Destroy 摧毁** | 摧毁 AI 核心（AI 目标）|
| 5 | **Job 职业目标** | 按职业生成的专属目标（assigned_role.generate_traitor_objective）|
| 6 | **Hijack 劫机**（终局）| 独自劫持穿梭机逃离 |
| 7 | **Martyr 殉道**（终局）| 带着目标一起死（NOVA 关闭）|
| 8 | **Escape 逃脱**（终局）| 活着逃离空间站 |

### 4.1 偷窃目标物品池全录（Steal 物品池）

> **代码**: `code/game/gamemodes/objective_items.dm`（`/datum/objective_item/steal/*`，共 72 定义）

**叛徒专属偷窃目标（23 种）**（`/datum/objective_item/steal/traitor/*`）:

| # | 目标 | 物品 |
|---|---|---|
| 1 | 调酒师的霰弹枪 | 双管霰弹枪（bartender_shotgun）|
| 2 | 消防斧 | 消防斧（fireaxe）|
| 3 | 机甲拆卸工具 | 机械拆卸撬棍（big_crowbar）|
| 4 | 牧师的圣杖 | 空杖（nullrod）|
| 5 | 小丑的鞋 | 小丑鞋（clown_shoes）|
| 6 | 哑剧的面具 | 哑剧面具（mime_mask）|
| 7 | 原动动能加速器 | PKA 矿枪（pka）|
| 8 | 花式假胡子 | 意大利假胡子（chef_moustache）|
| 9 | 侦探的左轮 | 侦探.38 左轮（det_revolver）|
| 10 | 律师的徽章 | 律师徽章（lawyers_badge）|
| 11 | 总工程师的腰带 | 总工工具腰带（chief_engineer_belt）|
| 12 | 主管的伸缩警棍 | 伸缩警棍（telebaton）|
| 13 | 货运部门预算 | 货运部门预算卡（cargo_budget）|
| 14 | 船长的 MAGNATE MOD 控制单元 | 船长 MOD 服（captain_modsuit）|
| 15 | 船长的备用 ID | 金色备用 ID（captain_spare）|
| 16 | 绝缘手套 | 黄色绝缘手套（insuls）|
| 17 | 可爱的飞蛾毛绒 | 飞蛾毛绒玩具（moth_plush）|
| 18 | 可爱的蜥蜴毛绒 | 蜥蜴毛绒玩具（lizard_plush）|
| 19 | 货运的 DENIED 印章 | DENIED 印章（denied_stamp）|
| 20 | 货运的 GRANTED 印章 | GRANTED 印章（granted_stamp）|
| 21 | 太空法书籍 | 太空法书（space_law）|
| 22 | 快速管道分配器 | RPD 管道枪（rpd）|
| 23 | 一盒珍贵甜甜圈 | 甜甜圈盒（donut_box）|

**通用偷窃目标（44 种）**（`/datum/objective_item/steal/*`）:

| # | 目标 | 物品 |
|---|---|---|
| 1 | 船长的古董激光枪 | 船长激光枪（caplaser）|
| 2 | 安保主管的个人激光枪 | HOS 激光枪（hoslaser）|
| 3 | 典狱长的个人紧凑霰弹枪 | 战斗霰弹枪（compactshotty）|
| 4 | 手持传送器 | 手持传送器（handtele）|
| 5 | 船长的喷气背包 | 船长喷气包（jetpack）|
| 6 | 总工程师的高级磁力靴 | 高级磁靴（magboots）|
| 7 | 舰长勋章 | 金色勋章（capmedal）|
| 8 | 注射器 | CMO 注射器（hypo）|
| 9 | 核弹认证磁盘 | 核弹磁盘（nukedisc）|
| 10 | 烧蚀风衣 | 烧蚀风衣（ablative）|
| 11 | 反应传送护甲 | 反应装甲（reactive）|
| 12 | 秘密文件 | 任何组织秘密文件（documents）|
| 13 | 自毁核心的钚芯 | 钚核心（nuke_core）|
| 14 | Project Goon 源代码 | R&D 主服务器硬盘（hdd_extraction）|
| 15 | 超物质晶体碎片 | 超物质碎片（supermatter）|
| 16 | 功能 AI | AI 卡（functionalai）|
| 17 | 空间站蓝图 | 蓝图（blueprints）|
| 18 | 黑匣子 | 黑匣子（blackbox）|
| 19 | 防暴霰弹枪 | 防暴霰弹枪（shotgun）|
| 20 | 安保温度枪 | 温度枪（temp_gun）|
| 21 | 主管的印章 | 主管印章（stamp）|
| 22 | 太阳镜 | 太阳镜（sunglasses）|
| 23 | 总工程师的高级 MOD | 高级 MOD（ce_modsuit）|
| 24 | 研发总监的科研 MOD | 科研 MOD（rd_modsuit）|
| 25 | 首席医疗官的救援 MOD | 救援 MOD（cmo_modsuit）|
| 26 | CMO 的紧凑除颤器 | 紧凑除颤器（cmo_defib）|
| 27 | 安保主管的守护 MOD | 守护 MOD（hos_modsuit）|
| 28 | 眩晕警棍 | 安保警棍（stun_baton）|
| 29 | 侦探的警棍 | 侦探警棍（det_baton）|
| 30 | 船长的军刀鞘 | 军刀鞘（captain_sabre_sheathe）|

> 另有间谍专属 steal 子类（lamarr 等 4 种，见间谍百科）。通用池 + 叛徒池 = **67 种可生成偷窃目标**（72 定义含抽象类/子类）。

**目标特性**:
| 特性 | 说明 |
|---|---|
| martyr_compatible | 目标是否与殉道兼容（影响殉道终局）|
| find_target | 自动选目标（dupe_search_range 去重）|
| give_special_equipment | 部分目标给特殊装备 |

---

## 五、终局目标详解

### 5.1 劫机 Hijack

| 项 | 值 |
|---|---|
| 条件 | 玩家≥30 + 10% 概率 |
| 判定 | 独自/小团队劫持穿梭机（emergency shuttle）|
| 玩法 | 抢先到穿梭机 → 挡其他乘客 → 起飞 |

### 5.2 殉道 Martyr（NOVA 关闭）

| 项 | 值 |
|---|---|
| 概率 | **0%**（NOVA；原版 20%）|
| 判定 | 目标全部 martyr_compatible 时触发 |
| 玩法 | 带着任务目标同归于尽 |

### 5.3 逃脱 Escape

| 项 | 值 |
|---|---|
| 概率 | 默认（非劫机非殉道）|
| 判定 | 活着逃离空间站（穿梭机/逃生舱）|

---

## 六、承包商契约

**代码**: `contractor/`（5 文件 608 行：contractor_hub 69 / syndicate_contract 271 / contract_teammate 45 / contractor_items 43）

### 6.1 合同生成与赎金

**合同池生成**（contractor_hub.create_contracts）:
| 项 | 值 |
|---|---|
| 初始合同 | **6 个**（1 大 + 2 中 + 3 小；NOVA 把 1 个小的改为中的）|
| 目标去重 | assigned_targets（不重复目标）|
| 最低 TC 阈值 | 30（总奖金不足时补贴最低合同）|
| 目标选择 | find_target（船员名册随机，黑名单排除已有目标）|

**赎金与奖金**:
| 项 | 值 |
|---|---|
| 赎金 | **7,500-15,000 信用**（100 × rand(75,150)；NOVA 从 18-45 上调）|
| 大单奖金 | +9-13 TC |
| 中单奖金 | +6-8 TC |
| 小单奖金 | +2-4 TC |
| 基础奖金 | +0-2 TC |
| 承包商分成 | **35%**（NOVA 常量 CONTRACTOR_RANSOM_CUT 0.35）|
| 通缉文案 | wanted_message（基础+动词+名词+地点组合生成）|

**合同状态机**:
| 状态 | 含义 |
|---|---|
| INACTIVE | 未开始 |
| ACTIVE | 进行中 |
| COMPLETE | 完成（目标入舱）|
| ABORTED | 中止（送错人=中止）|

### 6.2 提取与受害者体验

**提取舱机制**（launch_extraction_pod）:
| 项 | 值 |
|---|---|
| 条件 | 目标在投放点（dropoff_check）+ 周围 3 格无障碍 |
| 舱体 | 补给舱（extractionpod）|
| 爆炸 | 0,0,0,1（最小）|
| 判定 | 目标入舱 = COMPLETE；**非目标入舱 = ABORTED**（送错人中止）|
| 活人判定 | 目标**活着**入舱 → +奖金；**死了**入舱 → 只拿基础奖金 |
| 报酬到账 | contract_TC_to_redeem（可兑换 TC）|

**受害者体验**（handle_victim_experience，5 阶段）:
| 阶段 | 效果 |
|---|---|
| 0 START | 注入 20u 全能药（救出重伤）+ 闪光 + 1s 混乱 + 5s 模糊 |
| 1 FIRST HIT | 3.5s 头晕 |
| 2 SECOND HIT | 闪光 + 2s 混乱 + 3s 模糊 |
| 3 THIRD HIT | 等待 |
| 4 LAST HIT | 闪光 + 昏迷 200 分秒 + 10s 模糊 + 催眠语 |

**归还流程**（return_victim）:
| 项 | 值 |
|---|---|
| 归还时间 | COME_BACK_FROM_CAPTURE_TIME（定时）|
| 归还舱 | 回站补给舱 |
| 随身物 | 只留制服+鞋（其余扣押后归还）|
| 副作用 | 闪光 + 模糊 3s + 头晕 3.5s + 混乱 2s |
| 黑市 | 受害者可被卖黑市（ransom × 1.1-1.3）|

**承包商队友**（contract_teammate.dm）:
- 购买后可获得**承包商支援单位**（contractor support）
- 回合报告显示："[key] played [name], their contractor support unit."

---

## 七、暗号与风味

### 7.1 辛迪加暗号系统（Codewords）

**代码**: `code/controllers/subsystem/traitor.dm`（暗号生成）+ `code/datums/components/codeword_hearing.dm`（组件）+ `code/__HELPERS/names.dm` L209（generate_code_phrase）

**双暗号机制**:
| 暗号 | 颜色 | 用途 |
|---|---|---|
| **Phrase 暗语** | 蓝 | 叛徒先说（隐藏身份接头的暗号）|
| **Response 回应** | 红 | 另一叛徒回应（确认身份）|

**生成规则**（generate_code_phrase）:
| 规则 | 值 |
|---|---|
| 词数 | **2-5 个词**（3 词最常见：权重 200/325；2 和 4 词各 50；5 词 25）|
| 词源 | 船员名（70% 概率）/随机名 / 职业名（**反转显示**）/ 食物 / 饮品 / 空间站区域名 |
| 组合规则 | 至少 1 个"特定词"（人名/职业/食物/饮品/地点）+ 抽象名词/物体/形容词/威胁词 |
| 生成时机 | SS_traitor 初始化时全局生成（全服同用）|
| NOVA I18N | 中文服暗号本地化（lang_reverse_text 反查，显示/说出/高亮同源）|

**暗号组件**（codeword_hearing）:
- 叛徒获得 2 个组件：phrase（蓝高亮）+ response（红高亮）
- 听到暗号 → 对应词**高亮显示**（蓝/红）
- 战斗中（战斗模式）暗号不生效
- 用途：叛徒之间互相识别 + 确认接头（说暗号→对方回应对应→确认队友）

**实战用法**:
```
① 叛徒 A 在频道里说暗语（phrase）——比如 "Banana, 总工程师, 咖啡"
② 叛徒 B 听到后看到蓝色高亮 → 知道 A 是叛徒
③ B 回应（response）——红色高亮 → 双方确认
④ 接头成功 → 商量合作/交换情报
```

**暗号示例**（生成可能结果）:
- "John Doe, Chief Engineer, coffee"（人名+职业+饮品）
- "station, bananas, clown"（地点+食物+职业）
- "the void, firewall, red"（名词+物体+形容词）

### 7.2 雇主系统（flavor）

| 机制 | 值 |
|---|---|
| 雇主 | 75% 辛迪加 / 25% Nanotrasen（flavor 系统）|
| 影响 | 仅 flavor 文案（雇主名/主题），不影响目标 |
| 劫机排除 | 劫机目标时排除劫机雇主（GLOB.hijack_employers）|

### 7.3 其他风味

| 机制 | 说明 |
|---|---|
| 伪装 | 部分角色可伪装成其他阵营 |
| 自杀口号 | "FOR THE SYNDICATE!!" |
| 终局音效 | final_objective.ogg（提交目标时播放）|
| 小丑转化 | handle_clown_mutation（小丑叛徒克服武器自伤）|

---

## 八、Uplink 解锁与安全机制

**代码**: `code/datums/components/uplink.dm`（439 行，/datum/component/uplink）

### 8.1 Uplink 载体（4 种）

Uplink 组件可附着在 4 种载体上（RegisterSignal 分派）:
| 载体 | 触发信号 | 解锁方式 |
|---|---|---|
| **植入体** | 植入（COMSIG_IMPLANT_IMPLANTING）| 手术植入 |
| **PDA/模块电脑** | 更换铃声（COMSIG_TABLET_CHANGE_ID）| 铃声设为解锁码 |
| **收音机** | 换频（COMSIG_RADIO_NEW_MESSAGE）| 频率设为解锁码 |
| **笔** | 旋转（COMSIG_PEN_ROTATED）| 转笔 |

### 8.2 解锁码机制

| 项 | 值 |
|---|---|
| **unlock_code 解锁码** | 每个 Uplink 独立生成（generate_code，去重）|
| **failsafe_code 保险码** | 独立生成（与解锁码不同——防撞码）|
| 解锁显示 | examine 显示 "the code to unlock it is [解锁码]"（只有持有者能看）|
| 锁定状态 | locked=TRUE 初始，输入正确解锁码 → 打开 Uplink 界面 |

### 8.3 解锁方式（3 种触发）

| 方式 | 操作 | 判定 |
|---|---|---|
| **PDA 铃声** | 改铃声为解锁码 | 匹配 → 解锁；匹配保险码 → 引爆 |
| **收音机频率** | 调频到解锁码 | 匹配 → 解锁；匹配保险码 → 引爆 |
| **收音机消息** | Uplink 频道发消息含解锁码 | 匹配 → 解锁；含保险码 → 引爆 |

### 8.4 Failsafe 保险引爆

| 项 | 值 |
|---|---|
| 触发 | 输入保险码（failsafe_code）|
| 效果 | **爆炸**（销毁 Uplink + 可能杀死持有者）|
| 用途 | 防叛徒被捕获后 Uplink 落入敌手——紧急自毁 |
| 防止撞码 | generate_code 去重逻辑确保保险码 ≠ 解锁码 |

### 8.5 防 PDA 爆炸

- `check_detonate`：返回 COMPONENT_TABLET_NO_DETONATE（PDA 载体不会被引爆）

---

## 九、声誉与全局进度系统

**代码**: `code/controllers/subsystem/traitor.dm`（SStraitor）+ `uplink_handler.dm`

### 9.1 全局进度（current_global_progression）

| 项 | 值 |
|---|---|
| 计算 | `station_time × traitor_scaling_multiplier`（配置）|
| 缩放 | current_progression_scaling = 1 分钟 × 缩放系数 |
| 保底 | **所有叛徒的进度 ≥ 全局进度**（不能落后）|
| 新加入 | newjoin_progression_coeff = 1（新叛徒按全局进度起步）|

### 9.2 声誉（progression_points）

| 机制 | 说明 |
|---|---|
| 来源 | 跟随全局进度自动增长（每 tick 补齐到当前全局值）|
| 用途 | 购买需要 progression_minimum 的高级 Uplink 物品 |
| 判定 | not_enough_reputation = progression_points < 物品要求 |
| 意义 | **时间越久解锁越多**——叛徒越到后期越强 |

### 9.3 Badass 彩蛋

- 回合结束：叛徒获胜且 **使用 0 TC** → 显示 **Badass 图标**（badass.dmi）+ "badass" 标记
- 条件：traitor_won + used_telecrystals == 0

---

## 十、职业目标与特殊目标

**代码**: `code/modules/jobs/job_types/`（generate_traitor_objective）

### 10.1 通用机制

| 项 | 值 |
|---|---|
| 触发 | JOB_PROB 40% + 职业有自定义目标 |
| 调用 | assigned_role.generate_traitor_objective()（默认返回 null = 无专属目标）|
| 占用 | 生成后占用 1 个目标名额（job_objective）|

### 10.2 人事部长的专属目标（唯一有职业目标的职业）

**`captain_replacement` 船长夺权**（head_of_personnel.dm L52-86）:
| 项 | 值 |
|---|---|
| 目标 | **刺杀船长 → 拿走 ID → 成为新船长** |
| 类型 | 特殊刺杀（assassinate/captain_replacement）|
| 判定 | 船长死亡 + 目标成为船长 |
| 意义 | 唯一有专属叛徒目标的职业（人事部长）|

> 其他职业：generate_traitor_objective 默认返回 null → 无职业专属目标，用通用池（刺杀/偷窃/流放/摧毁 AI）。

---

## 十一、宣传组件（Demoraliser）

**代码**: `code/modules/antagonists/traitor/components/demoraliser.dm`（180 行）

**机制**: 叛徒放置的宣传物品（海报/涂鸦/模块）对看到的人施加**基于身份的心情**：
- 视野内看到（proximity monitor）+ examine 触发
- 昏迷/失明/无法阅读 → 不生效
- 已有同类别心情 → 不重复

### 11.1 三种宣传品

| 宣传品 | 类别 | 需求 |
|---|---|---|
| **邪恶海报**（poster）| evil poster | 识字 + 光线 |
| **辛迪加涂鸦**（graffiti）| evil graffiti | 识字 + 光线 |
| **心理模块**（module）| module | 仅光线 |

### 11.2 心情效果（按身份）

| 身份 | 海报 | 涂鸦 | 模块 |
|---|---|---|---|
| **船员** | -2（"那份海报让我对工作感到难过..."）| -2（"辛迪加标志？我安全吗？"）| **-4**（"他们就在空间站上！"）|
| **权威**（安保/指挥）| **-3**（"那张海报最好别给船员灌输怪想法..."）| -3（"哪个懒鬼画的辛迪加标志？！"）| **-5**（"没人站在我这边...我必须采取更极端措施"）|
| **反派**（叛徒）| **+2**（"我在做正确的事。"）| +2（"辛迪加标志？真够大胆。"）| +1（"我故意要找麻烦。"）|

| 项 | 值 |
|---|---|
| 持续时间 | 2 分钟（全部）|
| 隐藏 | hidden=TRUE（心情不显示来源）|
| 通知 | "Nice poster." / "Hey! Who put up that poster?" 等 |

---

## 十二、对战攻略

### 怎么玩叛徒（推荐流程）

```
① 确认目标（职业/通用 + 终局）+ Uplink 位置（偏好设定）
② 起始 TC 买核心装备（武器/潜行/逃生）
③ 完成任务目标（刺杀/偷窃/破坏）
④ 用声誉系统解锁高级物品（progression）
⑤ 终局：劫机 / 逃脱（NOVA 下无殉道）
```

### 怎么防叛徒（船员对策）

| 方法 | 说明 |
|---|---|
| **搜 Uplink** | 叛徒的 PDA/笔/收音机可能是 Uplink——检查可疑物品 |
| **盯贵重物** | 叛徒目标常是磁盘/武器库/引擎 |
| **安保巡逻** | 叛徒单人——保持警惕+结伴 |
| **反潜行** | 变色套件/特工 ID 是常见潜行手段——查监控 |
| **保护目标** | 被刺杀的指定目标应被重点保护 |

---

## 十三、数值速查表

| 项 | 值 |
|---|---|
| 源码 | traitor/ 7 文件 1,100 行 + uplink/ 3,746 行 + uplink 组件 439 行 |
| 目标上限 | 2（traitor_objectives_amount）|
| JOB_PROB | 40% |
| KILL_PROB | 50% |
| MAROON_PROB | 30% |
| DESTROY_AI_PROB | 100/人数 |
| HIJACK_PROB | 10%（玩家≥30）|
| MARTYR_PROB | **0%**（NOVA，原版 20）|
| 终局 | 劫机/殉道(NOVA 关)/逃脱 |
| 目标类型 | 刺杀/偷窃/流放/摧毁/职业/劫机/殉道/逃脱 |
| 承包商赎金 | 7,500-15,000（NOVA）|
| 承包商分成 | 35% |
| 合同池 | 6 个（1 大+2 中+3 小，NOVA）|
| 最低 TC 阈值 | 30 |
| 活人奖金 | 入舱活着 +奖金 / 死了只拿基础 |
| Uplink 载体 | 4 种（植入体/PDA/收音机/笔）|
| 解锁码 | unlock_code + failsafe_code（独立生成）|
| 声誉 | 全局进度（station_time × 缩放）|
| Badass | 0 TC 获胜彩蛋 |
| 职业目标 | 仅人事部长（船长夺权）|
| 宣传组件 | 3 类 × 3 身份（-5 ~ +2 心情，2 分钟）|
| Uplink | 见 [Uplink 系统百科](TianGuan13-上行链路Uplink系统百科.md)（395 件）|

---

*本文档数值全部实测自 `code/modules/antagonists/traitor/`（datum_traitor 354 行 + uplink_handler 138 行 + contractor 608 行 + demoraliser 180 行）+ `code/datums/components/uplink.dm`（439 行）+ `code/controllers/subsystem/traitor.dm` + `code/__DEFINES/antagonists.dm` 源码，无推测。*
