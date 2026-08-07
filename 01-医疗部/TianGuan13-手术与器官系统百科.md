# 天关 — 手术与器官系统百科

> **项目**: TianGuan13 (Nova Sector → /tg/station)
> **代码**: `code/modules/surgery/`（约 25,400 行）+ `code/datums/wounds/`（伤口系统）
> **范围**: 手术架构与操作、手术工具、状态机、器官系统（伤害模型/槽位/物种变体）、身体部位、断肢、伤口治疗、自动手术器
> **来源**: 由《手术系统百科》+《器官与身体部位系统百科》合并去重而成（2026-08）

## 目录

- [第一卷 · 系统架构与执行流程](#第一卷--系统架构与执行流程)
- [第二卷 · 手术状态机](#第二卷--手术状态机)
- [第三卷 · 手术工具](#第三卷--手术工具)
- [第四卷 · 手术时间与成功率](#第四卷--手术时间与成功率)
- [第五卷 · 标准手术流程（状态链）](#第五卷--标准手术流程状态链)
- [第六卷 · 全部手术操作全录](#第六卷--全部手术操作全录)
- [第七卷 · 器官基础（ORGAN_* 标志/伤害模型/槽位）](#第七卷--器官基础)
- [第八卷 · 身体部位（bodypart/BODYTYPE/手术状态位）](#第八卷--身体部位)
- [第九卷 · 主要器官列表](#第九卷--主要器官)
- [第十卷 · 物种器官变体](#第十卷--物种器官)
- [第十一卷 · 义肢/机械器官与修复](#第十一卷--义肢机械器官)
- [第十二卷 · 断肢机制](#第十二卷--断肢机制)
- [第十三卷 · 伤口系统](#第十三卷--伤口系统)
- [第十四卷 · 自动手术器](#第十四卷--自动手术器)
- [第十五卷 · NOVA EDIT 修改点汇总](#第十五卷--nova-edit)
- [附录A · 关键常量速查](#附录a--关键常量速查)
- [附录B · 代码路径索引](#附录b--代码路径索引)

---
# 第一卷 · 系统架构与执行流程

## 1.1 核心架构

手术系统是**新式模块化设计**（`/datum/surgery_operation` 单例数据型）。每个手术是一个 datum，全局单例由 `GLOB.operations`（`/datum/operation_holder`）管理。

**operation_holder** 在启动时实例化所有手术：
- `operations_by_typepath`：按类型路径索引全部手术单例
- `unlocked`：默认解锁的手术（无 OPERATION_LOCKED 旗标）
- `locked`：被锁定的手术（需手术碟/科研解锁）

**操作替换机制**（replaced_by）：一个手术可被另一个替代（如普通切开被绑架者切开替代）——两个都可用时只显示替代版。

## 1.2 执行流程（perform_surgery）

```
玩家手持工具点击目标（患者或断肢）
  → get_available_operations() 收集所有可用手术
     - 基础: GLOB.operations.unlocked 副本
     - 信号 COMSIG_LIVING_OPERATING_ON / COMSIG_ATOM_BEING_OPERATED_ON 可解锁额外手术
       （手术处理器 surgical_processor 通过此机制注入已下载手术）
  → show_radial_menu 径向菜单（按名称排序，单选自动执行）
  → try_perform() → start_operation() → do_after(时间×修饰)
  → 成功: success() → on_success()
  → 失败: failure() → on_failure()（按有效时间分级反馈）
```

**交互保护**：
- `DOING_INTERACTION` 防重复手术（希波克拉底誓言 TRAIT_HIPPOCRATIC_OATH 允许同时多台手术）
- `surgery_check` 回调保持菜单打开（工具必须仍在手中）
- 手术中防攻击：误操作不会捅患者（`ITEM_INTERACT_BLOCKING`）

## 1.3 三种操作基类

| 基类 | 目标 | 适用 |
|---|---|---|
| `/datum/surgery_operation/basic` | mob 本体（不限碳基） | 非人类也可手术（如泰坦巨兽、灵长类） |
| `/datum/surgery_operation/limb` | 指定身体部位 | 人类肢体手术 |
| `/datum/surgery_operation/organ` | 指定器官类型 | 器官手术（按 target_type 定位） |

- **basic**：检查 `required_biotype`（默认 ~MOB_ROBOTIC 即非机械体），非碳基用状态效果（basic_surgery_state）模拟手术状态
- **limb**：`required_bodytype`、`allow_stumps`（是否可对残肢手术）；腹股沟目标重定向到胸部
- **organ**：`required_organ_flag`（默认有机体）、`target_type`（器官类型）；眼部手术不会出现在头部手术菜单中（zone 检查）

## 1.4 患者要求

| 检查 | 说明 |
|---|---|
| 必须躺下 | 除 OPERATION_STANDING_ALLOWED 外，患者必须躺着 |
| 部位可及 | 衣物遮挡阻止手术（OPERATION_IGNORE_CLOTHES 例外） |
| 自手术 | 默认禁止；OPERATION_SELF_OPERABLE 或 TRAIT_SELF_SURGERY 特质允许 |
| 恒温床 | **NOVA 新增**：开启的恒温床（stasis）禁止手术 |
| 准备状态 | 部位必须 TRAIT_READY_TO_OPERATE |

---

# 第二卷 · 手术状态机

## 2.1 手术状态位（10 种）

部位上的手术状态用位标志存储（`surgery_state`），决定哪些手术可用：

| 状态位 | 含义 | 消除条件 |
|---|---|---|
| `SURGERY_SKIN_CUT` | 皮肤已切开 | 撑开皮肤时消除 |
| `SURGERY_SKIN_OPEN` | 皮肤已撑开 | 缝合时消除 |
| `SURGERY_VESSELS_UNCLAMPED` | 血管未夹闭（流血） | 夹闭血管 |
| `SURGERY_VESSELS_CLAMPED` | 血管已夹闭 | 松开血管 |
| `SURGERY_ORGANS_CUT` | 器官组织已切开 | 缝合时消除 |
| `SURGERY_BONE_SAWED` | 骨头已锯开 | 修骨/缝合时消除 |
| `SURGERY_BONE_DRILLED` | 骨头已钻孔 | 修骨时消除 |
| `SURGERY_PROSTHETIC_UNSECURED` | 假肢未固定 | 固定假肢 |
| `SURGERY_PLASTIC_APPLIED` | 已涂塑料层 | 修复时消除 |
| `SURGERY_CAVITY_WIDENED` | 胸腔已撑大 | 植腔/关腔时消除 |

**皮肤闭合状态组**（ALL_SURGERY_STATES_UNSET_ON_CLOSE）：缝合时一次性清除所有与手术相关的状态。

**无骨/无血管/无皮肤部位**：相应状态自动视为已满足（BONELESS/VESSELLESS/SKINLESS_SURGERY_STATES）。

## 2.2 状态检查逻辑

- `all_surgery_states_required`：必须**全部**具备
- `any_surgery_states_required`：具备**任一**即可
- `any_surgery_states_blocked`：**不可**具备任一
- 伤口（wound）也可以附加手术状态（如复合骨折产生 BONE_SAWED 需求）

## 2.3 手术自我检查（get_surgery_state_as_list）

玩家检查自己/他人可看到可读状态："皮肤已切开"、"血管未夹闭且流血"、"骨头已锯开"等。无医生时可用缝合线/电烙器**自行缝合**（try_manual_cauterize）取消手术状态。

---

# 第三卷 · 手术工具

## 3.1 标准手术工具

| 工具 | 工具行为 | toolspeed | 材料 | 说明 |
|---|---|---|---|---|
| **手术刀 Scalpel** | TOOL_SCALPEL | 1 | 铁2+玻璃0.5 | 锐利(EDGED)，可屠宰 |
| **止血钳 Hemostat** | TOOL_HEMOSTAT | 1 | 铁2.5+玻璃1.25 | |
| **电烙器 Cautery** | TOOL_CAUTERY | 1 | 铁1.25+玻璃 | heat=500 可点火 |
| **骨钻 Surgical Drill** | TOOL_DRILL | 1 | 铁5+玻璃3 | force=15，可刺眼 |
| **圆锯 Circular Saw** | TOOL_SAW | 1 | 铁5+玻璃3 | force=15，可屠宰 |
| **牵开器 Retractor** | TOOL_RETRACTOR | 1 | 铁3+玻璃1.5 | |
| **接骨器 Bonesetter** | TOOL_BONESET | 1 | 铁2.5+玻璃1.25 | |
| **血液过滤器 Blood Filter** | TOOL_BLOODFILTER | 1 | 铁2+玻璃0.75+银0.5 | TGUI 选择过滤药剂 |
| **截肢剪 Shears** | — | 1 | 铁4+钛3 | 直接截肢（15秒），可剪尾巴 |
| **手术巾 Drapes** | — | — | 塑料1 | 手术辅助（surgery_aid） |
| **手术处理器 Processor** | — | — | — | 从碟/电脑下载手术到内存 |

## 3.2 高级工具（augment / advanced / cyborg）

| 工具 | toolspeed | 说明 |
|---|---|---|
| **Augment 系列**（义体手术工具） | **0.5** | 所有标准工具的义体版 |
| **激光手术刀 Laser Scalpel** | **0.7** | TOOL_SAW+TOOL_SCALPEL 双模式切换，无血残留 |
| **灼烧器 Searing Tool** | **0.7** | TOOL_CAUTERY+TOOL_DRILL 双模式，红光 |
| **机械镊 Mechanical Pinches** | **0.7** | TOOL_HEMOSTAT+TOOL_RETRACTOR 双模式 |
| **外星万用工具 Alien Omnitool** | **0.25** | **全手术行为**（血滤/接骨/烙/钻/夹/拉/锯/刀）+工程行为，径向切换，游戏最快手术工具 |
| **Cyborg 系列** | 1 | 医疗机器人自带 |

**截肢剪 Shears**：`force=12`、锐利；直接攻击可截肢/断尾（`do_after(15s×修正)`：患者失能×0.5、抽搐×1.5、病态×0.7）；**截肢手术中 multiplier=0.33（最快）**。
**骨锯 Circular Saw**：可 **slapcraft 手搓电锯**（chainsaw 配方）。

## 3.3 残酷工具（Cruel Tools）

`CRUEL_IMPLEMENT` 旗标版本（扭曲牵开器/残酷止血钳/野蛮电烙器/饥饿手术刀/撕裂骨钻/锯齿骨锯/严厉接骨器/恶性血滤器）。**病态者（TRAIT_MORBID）用残酷工具手术速度 ×0.7**。骷髅验尸盘全套配残酷工具。

## 3.4 手术盘（Surgery Tray）

- DeForest 折叠医疗车：可折叠携带（BULKY、减速1）或展开成车
- **LMB 随机取工具、RMB 取指定工具**
- 覆盖层按工具速度显示最佳工具（更快工具覆盖慢工具）
- 满装盘含：血滤器/接骨器/电烙器/圆锯/手术口罩/手术袍(NOVA)/止血钳/剃刀/牵开器/手术刀/骨胶/外科胶带/手术巾/骨钻
- 验尸盘（morgue）：**全套残酷工具**（停尸房专用）
- 螺丝刀可拆解（铁棍2+银片）

## 3.5 手术碟（Surgery Disks）

| 碟 | 解锁手术 |
|---|---|
| **高级整形碟** | add_plastic（涂塑料） |
| **洗脑碟** | brainwash + brainwash/mechanic（**NOVA 移除标签防泄密**，仅医生/CMO/机器人技师可见说明） |
| **可疑手术碟（休眠协议）** | sleeper agent brainwash + mechanic（辛迪加休眠特工指令） |
| **遗弃船高级碟** | lobotomy + mechanic（脑叶切除）、vein_threading + mechanic（静脉编织）、nerve_splicing + mechanic（神经拼接） |
| **太空IRS碟** | lobotomy + vein_threading + nerve_splicing（含 mechanic）+ tend_wounds/combo/upgraded + **pacify（安抚）** + mechanic |
| **调试碟** | 全部手术 |

> 其他 OPERATION_LOCKED 手术（解剖/病毒/僵尸/翼修复/healing 系/绑架者系）通过科研网络或手术电脑解锁。碟 → 手术电脑（1秒读碟）或手术处理器加载。

---

# 第四卷 · 手术时间与成功率

## 4.1 时间公式

```
实际时间 = 基础时间(time) × min(总修饰因子, SURGERY_MODIFIER_FAILURE_THRESHOLD)
```

**超出失败阈值的时间转为失败率**（"有效时间"而非真实时间——手术时间不变，但失败率上升）。

## 4.2 总修饰因子 = 工具质量 × 医生状态 × 位置 × 患者状态

### 工具质量
`工具速度(toolspeed) × 该工具在 implements 表中的倍率`。未列出的工具不能用（质量=0）。

### 位置倍率（get_location_modifier）
| 位置 | 倍率 |
|---|---|
| 绑架者手术台（abductor） | **0.85** |
| 手术台（optable） | **1.0** |
| 恒温床（stasis） | 1.15 |
| 普通桌子 | 1.25 |
| 床 / 厨房挂肉钩 | 1.5 |
| 地面（无桌） | 2.0 |

### 医生状态（get_surgeon_surgery_speed_mod）
| 状态 | 效果 |
|---|---|
| **巴尔默峰**（drunkness 在 BALLMER_PEAK_LOW_END~HIGH_END） | ×0.8（适度饮酒手更稳） |
| 醉酒（一般） | ×(1 + drunkness^1.5/90)（最高 ~12× 惩罚） |
| 病态者+残酷工具 | ×0.7 |
| **安静环境**（3 格内无其他人类）NOVA | ×0.8 |
| 自手术（无 SELF_OPERABLE） | ×1.5（有效时间再 +1.5） |

### 患者状态（get_mob_surgery_speed_mod）
| 状态 | 效果 |
|---|---|
| 麻醉（TRAIT_ANALGESIA） | ×0.8 |
| 已解剖（TRAIT_SURGICALLY_ANALYZED） | ×0.8 |
| 部位特殊修饰（mob_surgery_speed_mods） | 乘法叠加 |

### TRAIT_IGNORE_SURGERY_MODIFIERS
无视所有修饰（有效时间=0 → 不可能失败），除非 OPERATION_ALWAYS_FAILABLE。

## 4.3 失败机制

- **失败率公式**：`失败率 = 10% × ((总倍率×基础时间 − 2.5×基础时间) / 1秒)`，clamp 0~99%
  - 例：1 秒手术 ×4 倍率 → 15% 失败率
  - 倍率 ≤2.5 时失败率为 0
- **失败消息分级**（按有效时间）：
  - 2.5~3：差点成了
  - 3~4：（无额外消息）
  - 4~5：这条件很难做对...
  - 5+：这条件几乎不可能...
- 失败有对应心情惩罚（患者长期负面心情）；成功短正面心情
- 死亡患者手术成功可获"睡神"成就（Sandman）
- **失血速率**：血管未夹闭 1.5/秒；夹闭后或器官切开 0.2/秒

## 4.4 疼痛与清醒

- `display_pain`：患者清醒时收到疼痛消息（30% 概率尖叫）
- 醉酒患者：按醉酒度 0~90% 概率无视疼痛
- 麻醉（ANALGESIA）患者无视疼痛与心情变化

---

# 第五卷 · 标准手术流程（状态链）

> 有机体标准流程。每一步的工具为推荐（implements[1]），括号内是 ghetto 替代工具。

## 5.1 完整状态链

```
① 切开皮肤 incise_skin (1.6s, 手术刀)
   ├ 替代: 光剑1.33 / 刀1.5 / 碎片2.25 / 螺丝刀5 / 笔5
   └ → 状态: SKIN_CUT + VESSELS_UNCLAMPED（切破血管，开始流血）
② 撑开皮肤 retract_skin (2.4s, 牵开器)
   ├ 替代: 螺丝刀2.25 / 剪线钳2.85 / 铁棍2.85 / 叉子2.85
   └ → 状态: SKIN_OPEN（清除 SKIN_CUT）
   ↓
   ├─ 夹闭血管 clamp_bleeders (2.4s, 止血钳) ← 防失血
   │   ├ 替代: 剪线钳1.67 / 包装纸2.85 / 电缆6.67
   │   └ → 状态: VESSELS_CLAMPED
   │
   ├─ 锯骨 saw_bones (5.4s, 圆锯) ← 开颅/开胸需锯骨
   │   ├ 替代: 锯齿铲1.33 / 臂刃1.33 / 消防斧2 / 手斧2.85 / 屠刀2.85
   │   └ → 状态: BONE_SAWED（+50 伤害）
   │
   ├─ 钻骨 drill_bones (3s, 骨钻) ← 牙科植入
   │   ├ 替代: 电动螺丝刀1.25 / 镐钻1.67 / 螺丝刀4 / 汤匙5
   │   └ → 状态: BONE_DRILLED
   │
   └─ 切器官 incise_organs (2.4s, 手术刀) ← 器官手术入口
       ├ 替代: 同切开（+10 伤害）
       └ → 状态: ORGANS_CUT
   ↓
   器官手术（见第六卷）
   ↓
⑤ 修复骨头 fix_bones (4s, 骨胶/外科胶带) ← 如锯过骨
   └ → 清除 BONE_SAWED/BONE_DRILLED（+40 治疗）
⑥ 缝合皮肤 close_skin (2.4s, 电烙器/缝合线)
   ├ 替代: 激光枪1.15 / 焊枪1.5 / 任意热源
   └ → 清除全部手术状态（+40 治疗如锯过骨）
```

## 5.2 机械体流程（operation_generic_mechanic）

合成体（赛博格/机器人）用螺丝刀流程：

```
① 拧开外壳 unscrew shell (2.4s, 螺丝刀)          → SKIN_CUT
② 打开舱盖 open hatch (1s, 手/撬棍)             → SKIN_OPEN+VESSELS_CLAMPED（一步到位）
③ 准备电子元件 prepare electronics (2.4s, 万用表) → ORGANS_CUT
④ 松开内骨骼 mechanic_unwrench (2.4s, 扳手)      → BONE_SAWED
   / 拧紧 mechanic_wrench (2.4s, 扳手)           → 清除 BONE_SAWED
⑤ 拧回外壳 screw shell (2.4s, 螺丝刀)            → 清全部状态
```

全部标记 OPERATION_SELF_OPERABLE（合成体可自我手术）| OPERATION_MECHANIC。

## 5.3 非碳基流程（operation_generic_basic）

简单生物（猴子等）：切开(1.6s) → 锯骨(5.4s) → 缝合(2.4s)，用 status_effect 模拟状态。

---

# 第六卷 · 全部手术操作全录

> 时间=基础时间；需=all_surgery_states_required；阻=any_surgery_states_blocked；任一=any_surgery_states_required；🔒=OPERATION_LOCKED（需碟/科研）；🩸=OPERATION_MORBID（病态者加成）

## 6.1 通用/治疗

| 手术 | 时间 | 工具 | 需求 | 效果 |
|---|---|---|---|---|
| **处理伤口 tend_wounds** 🔄 | 2.5s | 止血钳1/螺丝刀1.5/剪线钳1.67/笔1.8 | 任一皮肤状态 | 选治淤伤或烧伤：**固定 5 伤 + 现存伤×7%**；尸体×0.2；有衣物×0.55；LOOPING 自动循环；失败造成 0.8×治疗量+现存伤×3.5% 伤害。升级版+（🔒）倍率0.1、master 0.2；连招版 combo（🔒）1s 同时治淤+烧：固定3+3、倍率0.07 |
| **清创 debride** 🔄 | 3s | 止血钳1/手术刀1.25/锯1.66/剪线钳2.5 | 需二度以上烧伤+感染>0 | LOOPING：感染−4/次、消毒+0.5/次；+3 伤害；失败 4–8 伤 |
| **血液过滤 filter_blood** 🔄 | 2.5s | 血滤器1 | SKIN_OPEN，阻 UNCLAMPED | LOOPING：移除血液中每种非白名单化学 22%（clamp 0.4–10u）；NOVA：神经武器不可滤；失败 5 伤 |
| **洗胃 pump stomach** | 2s | 手1 | SKIN_OPEN+ORGANS_CUT | 强制呕吐：清胃 67% 化学，营养−20 |
| **取出植入体 implant_removal** | 6.4s | 止血钳1/撬棍1.5/餐叉2.85 | SKIN_OPEN，阻 UNCLAMPED | 移除第一个植入物（有植入盒则收入盒中） |
| **验尸 autopsy** | 10s | 验尸扫描仪1 | SKIN_OPEN（胸部，死者） | 加 TRAIT_DISSECTED+**TRAIT_SURGICALLY_ANALYZED**（后续手术×0.8）；记录死因到手术电脑；只能一次 |
| **血管对齐 realign blood vessels** | 3s | 止血钳1/手术刀1.15/剪线钳2.5 | SKIN_OPEN+ORGANS_CUT | 穿刺流血−0.25/次+mend_state=TRUE；+3 伤；失败 4–8 伤+可致伤口 |
| **密封血管 seal veins** | 3.2s | 烙器1/激光枪1.12/焊枪1.5 | SKIN_OPEN+ORGANS_CUT | 穿刺流血−0.5/次，清除 mend_state |

## 6.2 截肢/义肢

| 手术 | 时间 | 需求 | 效果 |
|---|---|---|---|
| **截肢 amputate** | 6.4s | SKIN_OPEN+BONE_SAWED，阻 UNCLAMPED | 截肢剪 **0.33 最快**/锯1/手术刀1/臂刃1.25/锯齿铲1.33/消防斧2/手斧2.5/屠夫刀4；整个肢体切下；胸部不可截；不可移除肢体/无肢解者免疫 |
| **拆解肢体 disassemble**（机械） | 2s | SKIN_OPEN | 巨型扳手0.33/扳手1/撬棍1/手术刀2；头部必须已锯骨（防误拆） |
| **取下木腿 detach wooden** | 3s | 无 | 锯1/锯齿铲1/消防斧1.15/手斧1.33/手术刀4 |
| **假肢替换 prosthetic_replacement** | 3.2s | SKIN_OPEN，阻 UNCLAMPED | 任意肢体=1/任意物品=1；用于残肢；机械肢体**无排斥**、有机肢体排斥**10 毒伤**、弗兰肯斯坦肢体**30 毒伤**；任意物品只能装手臂（w_class NORMAL-BULKY），加 PROSTHETIC_UNSECURED |
| **加固假肢 secure_prosthetic** | 4.8s | PROSTHETIC_UNSECURED | 缝合线1/手术胶带1.25/普通胶带2；清除状态，掉落概率归零；可自助 |
| **替换肢体 augment limb** | 3.2s | SKIN_OPEN | 用机械肢体（须 ROBOTIC）替换；肢体内有东西则拒绝；TRAIT_NO_AUGMENTS/不可移除肢体不可用 |

## 6.2b 自手术（Self Surgery）

- **TRAIT_SELF_SURGERY**：来自图书馆技能芯片（self_surgery）；无此特质时对自己做未标记 SELF_OPERABLE 的手术直接不可用
- **惩罚**（有特质但手术本身不允许自手术）：实际时间 ×1.5 + 有效倍率额外 +1.5（标准条件下≈3x 有效时间）
- **天生可自手术**（OPERATION_SELF_OPERABLE，无惩罚）：机械体全套状态链 6 个 + 机械器官操作 + secure prosthetic
- **手动自救缝合**（try_manual_cauterize）：无可用手术时用烧灼器/缝合线/热源对自己烧灼：耗时=2.4s×2×工具速度；缝合线耗 1 根、热源造成 5 烧伤；清除全部 UNSET_ON_CLOSE 状态

## 6.3 骨骼修复（operation_bone_repair）

| 手术 | 时间 | 需求 | 效果 |
|---|---|---|---|
| **复位脱臼 reset dislocation** | 2.4s | 任一（需脱臼伤口） | 正骨器1/撬棍2/手5；删除脱臼伤口；失败 25 伤；可站立、无视衣物、扫描过伤口×0.5 |
| **修复发丝骨折 repair hairline** | 4s | 任一皮肤状态 | 正骨器1/骨胶1/手术胶带1/超级胶带2/普通胶带3.33；删除发丝骨折 |
| **复位复合骨折 reset compound** | 6s | SKIN_OPEN，阻 UNCLAMPED | 正骨器1/手术胶带1.66/超级2.5/普通5；骨折 reset=TRUE（准备修复） |
| **修复复合骨折 repair compound** | 4s | 任一皮肤状态 | 骨胶1/手术胶带1/超级2/普通3.33；删除已复位骨折 |
| **丢弃颅骨碎片 discard debris** | 2.4s | 需颅裂伤口 | 止血钳1/剪线钳2.5/螺丝刀2.5；fissure.prepped=TRUE |
| **修复颅骨 repair cranium** | 4s | 需已清理颅裂 | 骨胶1/手术胶带1/超级2/普通3.33；删除颅裂伤口 |

## 6.4 器官手术（operation_organ_repair）

> 基类 `heal_to_percent=0.6`（器官伤降到 60%），成功后加 TRAIT_ORGAN_OPERATED_ON（不可重复除非 LOOPING）。失败额外伤害。

| 手术 | 时间 | 需求 | 效果 | 失败惩罚 |
|---|---|---|---|---|
| **修复器官 repair organ** | 视器官 | SKIN_OPEN+ORGANS_CUT+BONE_SAWED | 器官伤害恢复到 maxHealth×60%，清除 EMP | — |
| **肺叶切除 lobectomy** | 4.2s | 同上 | 肺伤降到 60%（heal_to 0.6） | +10% 肺伤+憋气+4 |
| **肝切除 hepatectomy** | 5.2s | 同上 | 肝伤降到 **10%** | +15% 肝伤 |
| **冠状动脉搭桥 coronary bypass** | 9s | 同上 | 心伤降到 60% | +20% 心伤+流血30 |
| **胃切除 gastrectomy** | 5.2s | 同上 | 胃伤降到 **20%** | +15% 胃伤 |
| **耳部手术 ear surgery** | 6.4s | SKIN_OPEN（骨须完整） | 临时耳聋归零（heal_to 0，可重复） | **脑伤 70**（扎穿大脑） |
| **眼部手术 eye surgery** | 6.4s | SKIN_OPEN（骨须完整） | 治愈临时失明，致盲减至 70s | **脑伤 70** |
| **脑部手术 brain surgery** 🔄 | 10s | SKIN_OPEN+BONE_SAWED，阻 UNCLAMPED | **每次治愈 25% 脑伤+清全部脑创伤+解除洗脑**（可循环） | +30% 脑伤+严重脑创伤 |
| **器官操作 organ manipulation** | **1s（默认）** | 内部: SKIN_OPEN+ORGANS_CUT | **取出/插入器官**（取出止血钳1/撬棍1.8/餐叉2.85；放入=器官本身1）；脑与胸腔器官需 BONE_SAWED | — |
| **特征操作 feature manipulation** | — | SKIN_OPEN+BONE_SAWED，阻 UNCLAMPED | 外部特征（尾巴/翅膀等）增删 | — |
| **哮喘气切口 bypass** | 8s | SKIN_OPEN+ORGANS_CUT | 哮喘炎症 **−75** | −75 但+憋气、30% 割伤、肢体10伤 |

## 6.4b 失血与血液数值

- **失血速率**：血管未夹闭 **1.5/秒**；夹闭后或器官切开 **0.2/秒**
- **洗胃 pump stomach**（2s）：清胃内化学 **67%**，营养 −20
- **血液透析 filter_blood**（2.5s，LOOPING）：每轮移除每种非白名单化学 **22%**（clamp 0.4–10u）；NOVA 神经药物不可滤；失败 5 伤
- **尸检研究点数**（实验解剖）：人类 10 / 猴子 5 / 绑架者 ×4 / 傀儡·僵尸 ×3 / 果冻·豆人 ×2 / 异形王 ×10 / 异形 ×5 / 其他 ÷6；尸体胸部 +80 伤，只能一次

## 6.5 头部/大脑手术

| 手术 | 时间 | 需求 | 效果 |
|---|---|---|---|
| **脑叶切除 lobotomy** 🔒🩸 | **1s（默认）** | SKIN_OPEN+BONE_SAWED，阻 UNCLAMPED | 能量剑 **0.55 最快**/手术刀 1.15/刀 2.85；治愈全部脑创伤+解除洗脑；**75% 概率获得新永久创伤**（1/3 轻度、1/3 重度或特殊、1/3 特殊，砍脑叶级抗性）；失败脑伤+80+必得新创伤 |
| **洗脑 brainwash** 🔒🩸 | 20s | SKIN_OPEN+ORGANS_CUT+BONE_SAWED | 止血钳 1.15/剪线钳 2/包装纸 2.85/电缆 6.67；植入指令成为患者首要目标（不可转换者免疫）；mindshield 可清除；失败脑伤+40 |
| **休眠特工植入 sleeper** 🔒 | 20s | 同上 | 随机选 **12 条阴谋指令**之一+获得阴谋恐惧症创伤 |
| **安抚 pacification** 🔒🩸 | 4s | SKIN_OPEN+BONE_SAWED，阻 UNCLAMPED | 止血钳1/螺丝刀2.85/笔6.67；永久获得严重级和平主义脑创伤 |
| **病毒结合 viral_bonding** 🔒🩸 | 10s | SKIN_OPEN+ORGANS_CUT | 患者需≥1u 太空灵/病毒食物/甲醛+携带不可治愈疾病；成功后病毒 carrier=TRUE（免疫但传播） |
| **诱导生物坏死 bionecrosis** 🔒🩸 | 5s | SKIN_OPEN+BONE_SAWED，阻 UNCLAMPED | 注射器1/笔3.33；需工具或患者含>1u 瑞扎多/僵尸粉；植入 zombie_infection 器官（罗梅罗肿瘤） |
| **大脑除颤 revival** 🩸 | 5s | SKIN_OPEN+BONE_SAWED（头部） | 除颤器1/电击手1/**警棍1.33**/能量枪1.67；氧损−50、停心脏骤停、复活；成功脑伤+15（180s消退）、失败+50；机械版=全系统重启 |
| **神经重编程 reprogram**（机械） | 20s | 同上 | 机器人洗脑 |

## 6.5b 生物武器精确效果（operation_bioware，全部🔒🩸）

> 12.5s 基础（SKIN_OPEN+BONE_SAWED+ORGANS_CUT），手=1；同组互斥。

| 手术 | 时间 | 成功效果 |
|---|---|---|
| **静脉编织 thread veins** | 12.5s | threaded_veins 状态：流血减少 |
| **肌肉静脉 muscled veins** | 12.5s | muscled_veins：**无心脏也能泵血** |
| **神经拼接 splice nerves** | **15.5s** | spliced：抗眩晕/硬直 |
| **神经接地 ground nerves** | **15.5s** | grounded：抗电击 |
| **韧带重塑 ligament hook** | 12.5s | hooked：断肢可手动接回（也更容易掉） |
| **韧带强化 ligament reinforcement** | 12.5s | reinforced：更难被肢解（神经更易断） |
| **皮质折叠 cortex folding**（脑） | 12.5s | 脑加 TRAIT_SPECIAL_TRAUMA_BOOST；失败脑伤+60+严重创伤 |
| **皮质印记 cortex imprinting**（脑） | 12.5s | imprinted：脑伤抗性；失败脑伤+60+严重创伤 |

## 6.7 其他手术

| 手术 | 时间 | 需求 | 效果 |
|---|---|---|---|
| **抽脂 lipoplasty** | 6.4s | SKIN_OPEN，阻 UNCLAMPED | 锯1/手术刀1.25/锯齿铲1.33/能量剑1.33/手斧3.33/刀3.33；仅胸部+需肥胖+营养≥饱腹；营养降至饱腹值，掉一块"肥肉"（含被抽走的营养） |
| **整形 plastic surgery** | 6.4s | SKIN_OPEN | 手术刀1/刀2/剪线钳2.85/笔5；修复毁容或改名（10 随机名/涂塑料后可用照片名）；失败=毁容 |
| **涂塑料 add_plastic** 🔒 | 4.8s | SKIN_OPEN，阻 PLASTIC_APPLIED+UNCLAMPED | 塑料板1；给头部涂塑料层（SURGERY_PLASTIC_APPLIED 状态） |
| **植腔 cavity implant** | 3.2s | SKIN_OPEN+ORGANS_CUT+CAVITY_WIDENED | 任意物品1（w_class≤NORMAL，除 transfer_valve）；胸腔藏物，自动清 CAVITY_WIDENED |
| **移除腔体植入 undo cavity** | 3.2s | 同上 | 手1/止血钳2/撬棍2.5/餐叉5；取出藏物 |
| **撑大胸腔 widen cavity** | 4.8s | SKIN_OPEN+ORGANS_CUT，阻 WIDENED | 牵开器1/撬棍1.5；准备植腔 |
| **合拢胸腔 close cavity** | 4.8s | 同上+WIDENED | 关闭植腔 |
| **牙科植入 add dental implant** | 1.6s | SKIN_OPEN+BONE_DRILLED，阻 UNCLAMPED | 药丸1；牙里藏药（患者获"激活药丸"技能） |
| **移除牙植体 remove dental** | 3.2s | 同上 | 止血钳1/手1；随机取一颗药丸（手电时×0.8） |
| **修复翅膀 fix wings** 🔒 | 20s | SKIN_OPEN，阻 UNCLAMPED | 止血钳1.15/螺丝刀2.85/笔6.67；需烧焦翅膀+体内≥5u 合成肉；翅膀+触角全修 |
| **大脑除颤 revival** 🩸 | 5s | SKIN_OPEN+BONE_SAWED | 见 6.5 |
| **实验解剖 dissection** 🔒 | 12s | 任一皮肤状态 | 尸检扫描仪1/手术刀1.66/刀5/碎玻璃10；研究点数见 6.4b；ALWAYS_FAILABLE |
| **提取核心 core removal** | 1.6s | 任一皮肤状态 | 止血钳1/撬棍1；提取泰坦/史莱姆核心 |

> **机械体变体命名规则**：几乎所有有机体手术都有 `/mechanic` 机械版，名称变为工程术语（如 器官修复→维护 maintenance、洗胃→排营养处理器、血液过滤→排液压、冠脉搭桥→访问引擎内部、脑手术→神经调试、脑叶切除→神经碎片整理、洗脑→重编程、安抚→删除攻击编程、复活→全系统重启）。机械变体额外标记 OPERATION_SELF_OPERABLE（合成体可自助）| OPERATION_MECHANIC。
> **绑架者变体**（abductor）：所有状态链手术的绑架者版（OPERATION_IGNORE_CLOTHES | OPERATION_LOCKED），用激光刀免开骨锯即可切器官。
> **研究笔记 research notes**：解剖手术的副产品，可在科研终端换取研究点数。

---



---


## 6.8 外星手术（Abductor Surgery）★ 新增

> **来源**: `code/modules/antagonists/abductor/abductor.dm`（外星人阵营）+ `code/modules/surgery/operations/operation_generic.dm`（外星步骤变体）+ `operation_organ_manip.dm`（外星器官操作）
> **核心**：外星手术是**绑架者（Abductor）阵营专属**的完整手术体系——外星科学家在母舰手术台上执行，普通船员无法使用。

### 6.8.1 解锁机制（谁能做）

| 条件 | 详情 |
|---|---|
| **阵营** | 绑架者（Abductor）反派——特工/科学家/独行 3 角色 |
| **训练特质** | 外星科学家获得 `TRAIT_ABDUCTOR_SCIENTIST_TRAINING`（`abductor.dm` on_gain 授予） |
| **手术台** | 母舰上的外星手术台（teleport 至外星母舰 landmark） |
| **普通玩家** | 无法使用外星手术——除非通过科技树（见 6.8.3） |

### 6.8.2 外星科学家手术清单（19 项，源码全录）

**代码**: `abductor.dm:139-155`（`ayy_operations` 列表）

| # | 手术 | 类型 | 说明 |
|---|---|---|---|
| 1 | tend_wounds/combo/upgraded/**master** | 治疗连招 | 处理伤口组合升级版（大师级） |
| 2 | viral_bonding | 病毒结合 | 病毒载体化（免疫但传播） |
| 3 | add_plastic | 涂塑料 | 高级整形 |
| 4 | **bionecrosis** | 生物坏死 | **罗梅罗肿瘤植入**（丧尸制造） |
| 5 | clamp_bleeders/**abductor** | 夹闭血管（外星版） | 外星工具步骤 |
| 6 | close_skin/**abductor** | 闭合皮肤（外星版） | 外星工具步骤 |
| 7 | incise_organs/**abductor** | 切开器官（外星版） | 外星工具步骤 |
| 8 | incise_skin/**abductor** | 切开皮肤（外星版） | 外星工具步骤 |
| 9 | organ_manipulation/external/**abductor** | 特征操作（外星版） | 外部特征增删 |
| 10 | organ_manipulation/internal/**abductor** | 器官操作（外星版） | 器官取放 |
| 11 | retract_skin/**abductor** | 拉开皮肤（外星版） | 外星工具步骤 |
| 12 | unclamp_bleeders/**abductor** | 松开血管（外星版） | 外星工具步骤 |
| 13 | fix_wings | 修复翅膀 | 翅膀+触角修复 |
| 14+ | bioware 全套（typesof 全类） | 生物武器 | 静脉编织/肌肉静脉/神经拼接/神经接地/韧带重塑/韧带强化/皮质折叠/皮质印记 |
| 15+ | brainwash 全套（typesof 全类） | 洗脑 | 指令植入成为首要目标 |
| 16+ | lobotomy 全套（typesof 全类） | 脑叶切除 | 治愈脑伤+解除洗脑 |
| 17+ | pacify 全套（typesof 全类） | 安抚 | 永久和平主义脑创伤 |

> **清单结构**：源码 `ayy_operations` 共 **13 项直接 + 4 个 typesof 类**（bioware/brainwash/lobotomy/pacify 全子类型），展开后约 19-20 个手术类型全解锁。

> **外星版基础步骤**（abductor 变体）：与普通步骤同功能，但标记为外星专属——只能由外星科学家使用，继承父类时间（如 incise_skin 1.6s / retract_skin 2.4s）。

### 6.8.3 科技树联动（Alien Surgery 节点）

> 外星手术的"平民版"通过**科技树隐藏节点**解锁——详见《罗梅洛丧尸与感染系统百科》2.2a 专章。

```
Applied Bluespace → Bluespace Travel → Alien Technology
                                          └→ Alien Surgery（5 级点数 + 隐藏节点）
                                              └→ 解锁：bionecrosis + brainwash + bioware
                                                   + 外星手术工具全套（15 种扫描物品）
```

**与外星阵营的区别**：
| 维度 | 外星科学家（Abductor） | 科技树解锁（Alien Surgery 节点） |
|---|---|---|
| 方式 | 天生训练特质 | 科研网络研究 |
| 工具 | 外星母舰工具 | 外星工具（复制品） |
| 位置 | 母舰 | 站内手术台 |
| 对象 | 绑架的受害者 | 任意患者 |

### 6.8.4 外星工具（母舰专属）

| 工具 | 用途 | 特点 |
|---|---|---|
| **外星万用工具 Alien Omnitool** | 全手术行为 | **0.25 倍率**（游戏最快），径向切换手术/工程行为 |
| 外星手术刀/锯/钻/止血钳/牵开器/烧灼器 | 对应手术步骤 | 绑定 abductor 变体步骤 |
| 外星圆锯/撬棍/电击棒/多功能工具等 | 支援 | 解锁扫描用（15 种物品） |

---

## 第七卷 · 器官基础：ORGAN_* 标志位与失效机制

### 1.1 定义位置

`code/__DEFINES/surgery.dm` L1-56（器官标志）、`code/__DEFINES/DNA.dm` L139-142（阈值常量）。

### 1.2 ORGAN_* 标志位（共 20 个，含 NOVA 新增 3 个）

| 标志位 | 位值 | 含义 |
|---|---|---|
| `ORGAN_ORGANIC` | 1<<0 | 有机器官（默认）。不受 EMP 影响 |
| `ORGAN_ROBOTIC` | 1<<1 | 合成/义体器官。受 EMP 影响；**不会自然腐烂，也不会自然愈合** |
| `ORGAN_MINERAL` | 1<<2 | 矿物器官（golem、plasmaman）。特例处理 |
| `ORGAN_FROZEN` | 1<<3 | 冷冻器官，不腐烂（`on_death` 直接跳过） |
| `ORGAN_FAILING` | 1<<4 | 衰竭器官：持续产生负面效果，直到被修复或替换；典型地其功能也失效 |
| `ORGAN_EMP` | 1<<5 | 被 EMP 永久击坏的合成器官。持续按 decay_factor 恶化，必须修复/更换 |
| `ORGAN_VITAL` | 1<<6 | **移除即杀死亡体**（目前只有脑）。`mob_remove` 中若移除且非 special 且未开无敌 → `death()` |
| `ORGAN_EDIBLE` | 1<<7 | 可食用（自动挂 `edible` 组件；被吃后加 `ORGAN_UNUSABLE`） |
| `ORGAN_UNREMOVABLE` | 1<<8 | 无法通过手术等常规手段移除 |
| `ORGAN_HIDDEN` | 1<<9 | 扫描仪不可见；不激怒"身体纯净主义者" |
| `ORGAN_VIRGIN` | 1<<10 | 从未被植入过任何人。首次植入时记录血型 DNA 并清除该标志 |
| `ORGAN_PROMINENT` | 1<<11 | 高级扫描仪即使完全健康也总是显示它 |
| `ORGAN_HAZARDOUS` | 1<<12 | 体内危险异物（扫描显示 "Harmful Foreign Body"，需手术取出） |
| `ORGAN_EXTERNAL` | 1<<13 | 外部器官（尾巴/翅膀等），多处检查用 |
| `ORGAN_GHOST` | 1<<14 | 幽灵器官，可穿墙（`movement_type = PHASING`） |
| `ORGAN_MUTANT` | 1<<15 | 突变器官：健康分析仪将其标记为突变体 |
| `ORGAN_UNUSABLE` | 1<<16 | 被咬坏/无法使用（吃掉的器官） |
| `ORGAN_SYNTHETIC_FROM_SPECIES` | 1<<17 | **NOVA**：物种赋予的合成器官（用于跨物种器官替换判定） |
| `ORGAN_TUMOR_CORRUPTED` | 1<<18 | **NOVA**：血族(hémophage)来源器官，或被血族肿瘤感染的器官 |
| `ORGAN_NANOMACHINE` | 1<<19 | **NOVA**：纳米机器器官（protean 变形人种族使用，见 modular_nova） |

**辅助宏**：
- `ORGAN_TYPE_FLAGS = ORGANIC | ROBOTIC | MINERAL | GHOST`（器官"类型"四联）
- `IS_ORGANIC_ORGAN(organ)` / `IS_ROBOTIC_ORGAN(organ)`
- `ORGAN_BIOSCRAMBLE_INCOMPATIBLE = ROBOTIC | MINERAL`；`ORGAN_CAN_BE_BIOSCRAMBLED()`
- `IS_SYNTHETIC_ORGAN(organ)`（NOVA：`organ_flags & ORGAN_SYNTHETIC_FROM_SPECIES`，定义于 `~nova_defines/synth_defines.dm`）

### 1.3 器官伤害模型（核心数值）

| 参数 | 值 | 说明 |
|---|---|---|
| `STANDARD_ORGAN_THRESHOLD`（maxHealth 默认） | **100** | 通用器官最大伤害 |
| `high_threshold` | **45**（45%） | 重度损伤阈值 |
| `low_threshold` | **10**（10%） | 轻度损伤阈值 |
| `STANDARD_ORGAN_HEALING` | **50/100000 = 0.0005**（maxHealth 的 0.05%/秒） | 通用治愈因子（比例制，非固定数值） |
| `STANDARD_ORGAN_DECAY` | **111/100000 = 0.00111**（maxHealth 的 0.111%/秒） | 通用腐烂因子；设计目标 ≈ **15 分钟**衰竭（100 ÷ 0.111 ≈ 900s） |
| 衰竭判定 | `damage >= maxHealth` | 通过 `check_damage_thresholds()` 置 `ORGAN_FAILING` |

**各主要器官腐烂速度（"死后多久开始坏"）**：

| 器官 | decay_factor | 约等于 | 设计目标 |
|---|---|---|---|
| 心脏 | 2.5 × STANDARD | 0.2775%/s | ~6 分钟后衰竭（最早坏） |
| 胃 | 1.15 × STANDARD | 0.1277%/s | ~13 分钟 |
| 肝 | 1.0 × STANDARD | 0.111%/s | ~15 分钟（居中） |
| 肺 | 0.9 × STANDARD | 0.0999%/s | ~16.5 分钟（最晚坏的普通器官之一） |
| 脑 | 0.5 × STANDARD | 0.0555%/s | ~30 分钟（平衡考虑） |
| 声带/眼球等 | 0 或 STANDARD | — | 声带 decay=0（永不烂）；眼=标准 |

**治愈机制（`on_life`，`_organ.dm` L174-198）**：
- 未衰竭时：`healing_amount = healing_factor + 饱腹加成`；饱腹度 `satiety > 0` 时额外 `4 × healing_factor × satiety / MAX_SATIETY`（MAX_SATIETY=600），即最多 5 倍基础治愈。
- 修复量为 `healing_amount × maxHealth × seconds_per_tick`，通过 `apply_organ_damage(负数)` 实现。
- **机器人器官不自然治愈**（`IS_ROBOTIC_ORGAN` 直接 return）。
- 体外腐烂（`on_death`）：有主人时按 `owner.bodytemperature`，无主人按环境气体温度；温度因子 `min((温度-293.15)/20, 1)`，即体温越高烂得越快。

**衰竭机制（`handle_failing_organs` → `organ_failure`）**：
- `failure_time += seconds_per_tick`；每器官自定义 `organ_failure()`（见第 3 章各器官）。
- 通过阈值时向主人播报提示（`low_threshold_passed` / `high_threshold_passed` / `now_failing` / `now_fixed` 等）。
- 恢复：`set_organ_damage(0)` 或修复手术可清除 `ORGAN_FAILING`。

**伤害入口**：`apply_organ_damage(damage_amount, maximum, required_organ_flag)`（钳制在 [0, maxHealth]，发 `COMSIG_ORGAN_ADJUST_DAMAGE` 信号，返回净变化）；`set_organ_damage()` 为绝对设定（管理 heal 用）。

**扫描显示分级**（`get_status_text`）：
- `ORGAN_HAZARDOUS` → 红色 "Harmful Foreign Body"
- `ORGAN_EMP` → 红色 "EMP-Derived Failure"
- `ORGAN_FAILING` → 红色 "Non-Functional"（有 technetium 试剂时显示精确 % 伤害）
- >45 → 橙色 "Severely Damaged"；>10 → 黄色 "Mildly Damaged"
- `show_on_condensed_scans()`：只有 PROMINENT/HAZARDOUS/FAILING/VITAL 显示在精简扫描中

### 1.4 器官槽位（ORGAN_SLOT_*，`code/__DEFINES/DNA.dm` L88-136）

内部槽：`adamantine_resonator`、`appendix`、`brain`、`brain_motorcontrol`(小脑)、`brain_cns`、`brain_memory`(海马体)、`breathing_tube`、`ears`、`eye_sight`、`heart`、`heartdrive`(心脏辅助)、`eye_hud`、`liver`、`lungs`、`parasite_egg`、`monstercore`、`r_arm_device`/`l_arm_device`(手臂植入物)、`r_arm_muscle`/`l_arm_muscle`(手臂肌肉)、`spine`、`stomach`、`stomach_aid`、`thrusters`、`tongue`、`vocal_cords`、`zombie_infection`、外部槽（`tail`/`spines`/`snout`/`frills`/`horns`/`wings`/`antennae`/`pod_hair`）、异形槽（acid_gland/eggsac/hivenode/neurotoxingland/plasma_vessel/resin_spinner）。

**器官处理顺序**（`organ_process_order`，L160-192）：脑 → 阑尾 → 左右臂植入物 → 左右臂肌肉 → 胃 → 胃辅助 → 呼吸管 → 耳 → 眼 → 肺 → 心脏 → 僵尸感染 → 推进器 → HUD → 肝 → 舌 → 声带 → 精金共鸣器 → 心脏辅助 → 脑植入件 → 异形器官。

### 1.5 器官插入/移除（`organ_movement.dm`）

- 双层结构：**mob 层**（`organs`/`organs_slot` 列表）与**肢体层**（`bodypart_owner`，器官物理存放在肢体物品内）。拔脑 = 从 mob 和头骨都移除；砍头 = 只从 mob 移除；从断头里取脑 = 只从肢体移除。
- `Insert(mob, special, movement_flags)`：`mob_insert` → `bodypart_insert`。同槽已有器官会被 `Remove(special=TRUE)` 换出（`DELETE_IF_REPLACED` 标志直接删除旧器官）。
- `Remove()`：mob 与肢体同时移除；`ORGAN_VITAL` 且非 special → 直接 `death()`；移除后器官带上 `TRAIT_ORGAN_USED_BY_PLAYER`。
- 疾病传播：移除器官时其携带的病毒（特殊/非传染除外）会通过 `infective` 组件传染；机器人器官除非疾病带 `MOB_ROBOTIC` 生物类型否则免疫。
- `valid_zones`：部分器官可换区植入（如手臂植入物可放任意手臂，`swap_zone()`）。

---

## 第八卷 · 身体部位 bodypart 结构

### 2.1 基础结构与部位类型

**6 个默认部位**（`BODYPARTS_DEFAULT_MAXIMUM = 6`）：头 / 胸 / 左臂 / 右臂 / 左腿 / 右腿。

| 部位 | body_zone | max_damage | body_damage_coeff | 备注 |
|---|---|---|---|---|
| 头 head | BODY_ZONE_HEAD | `LIMB_MAX_HP_CORE` = **250**（NOVA 原 200） | 1（TOTAL） | 有 head_flags；可断头（掉落全部头部装备+牙植体） |
| 胸 chest | BODY_ZONE_CHEST | 250 | 1（TOTAL） | 腔体（cavity）存放处；`acceptable_bodytype`/`acceptable_bodyshape` 校验外接肢体 |
| 手臂 arm ×2 | BODY_ZONE_L_ARM/R_ARM | `LIMB_MAX_HP_DEFAULT` = **60**（NOVA 原 50） | 0.75（DEFAULT） | `can_be_disabled=TRUE`；持有手持物品/手铐逻辑 |
| 腿 leg ×2 | BODY_ZONE_L_LEG/R_LEG | 60 | 0.75 | `can_be_disabled=TRUE`；脚装备/脚镣逻辑 |

**血量常量**（`code/__DEFINES/bodyparts.dm` L8-26）：
- `LIMB_MAX_HP_PROSTHESIS` = 20（剩余物资义肢）
- `LIMB_MAX_HP_DEFAULT` = 60（默认；**NOVA 由 50 上调**）
- `LIMB_MAX_HP_ADVANCED` = 75（高级机器人肢体）
- `LIMB_MAX_HP_CORE` = 250（头/躯干；**NOVA 由 200 上调**）
- 异形：幼虫 50 / 异形肢体 100 / 异形核心 500；异形灼伤伤害 ×2
- 伤害系数：高级义肢 0.5、默认 0.75、头/躯干 1、剩余物资义肢 **2.5**（剩余义肢受伤对全身健康影响最大）
- 肢体伤害对 mob 总血量的贡献 = 存储伤害 × 系数（例：50 伤害 × 0.5 = 全身 25 伤害）

### 2.2 BODYTYPE_* 类型（`code/__DEFINES/mobs.dm` L156-182，12 个）

| 标志 | 位值 | 含义 |
|---|---|---|
| `BODYTYPE_ORGANIC` | 1<<0 | 有机肢体（默认） |
| `BODYTYPE_ROBOTIC` | 1<<1 | 机器人/义体肢体（如 cyborg 部件） |
| `BODYTYPE_LARVA_PLACEHOLDER` | 1<<2 | 异形幼虫占位，不能装到任何东西上 |
| `BODYTYPE_ALIEN` | 1<<3 | 异形（xenomorph）肢体 |
| `BODYTYPE_GOLEM` | 1<<4 | 魔像肢体 |
| `BODYTYPE_PEG` | 1<<5 | 木钉假肢 |
| `BODYTYPE_PLANT` | 1<<6 | 植物肢体（光合作用时再生） |
| `BODYTYPE_SHADOW` | 1<<7 | 阴影肢体（shadowheal 时再生） |
| `BODYTYPE_GHOST` | 1<<8 | 幽灵肢体，可穿墙 |
| `BODYTYPE_DIGITIGRADE` | 1<<9 | 趾行（digitigrade）肢体 |
| `BODYTYPE_SYNTHETIC` | 1<<10 | **NOVA**：合成肢体，用于额外手术判定 |
| `BODYTYPE_GHOUL` | 1<<11 | **NOVA**：食尸鬼肢体，可拆卸 |

**BODYSHAPE_*（着装形态，L184-218）**：`HUMANOID`(1<<0) / `MONKEY`(1<<1) / `DIGITIGRADE`(1<<2) / `SNOUTED`(1<<3) / `GOLEM`(1<<4)；**NOVA 新增**：`CUSTOM`(1<<15)、`HIDE_SHOES`(1<<16)、`ALT_FACEWEAR_LAYER`(1<<17)、taur 系列（`TAUR_GENERIC/SNAKE/PAW/HOOF/BIG_LEGS/BIG_LEGS_STANCED`，1<<18~23，汇总宏 `BODYSHAPE_TAUR`）。

**bodypart_flags（`code/__DEFINES/surgery.dm` L74-85）**：
- `BODYPART_UNREMOVABLE` (1<<0) — 不可被截肢/切除
- `BODYPART_PSEUDOPART` (1<<1) — 伪肢体（如电锯臂），掉落时直接销毁并掉出内容
- `BODYPART_IMPLANTED` (1<<2) — 手术植入且 limb_id 与主人默认不符（弗兰肯斯坦肢体）
- `BODYPART_UNHUSKABLE` (1<<3) — 永不显示为干尸
- `BODYPART_VIRGIN` (1<<4) — 从未装到 mob 上
- `BODYPART_STUMP` (1<<5) — 残肢（缺失肢体的一部分）

**生物状态 biological_state（BIO_*）**：决定伤口类型与手术状态自动推导。`BIO_STANDARD_UNJOINTED` = 肉+骨+血默认组合。
- 无皮肤（非 肉/金属/几丁质）→ 自动获得 `SURGERY_SKIN_OPEN`
- 无骨骼（非 骨/金属）→ 自动获得 `SURGERY_BONE_DRILLED|SURGERY_BONE_SAWED`（钻/锯可同时进行）
- 无血管（非 血/线缆）→ 自动获得 `SURGERY_VESSELS_CLAMPED|SURGERY_ORGANS_CUT`

### 2.3 手术状态位 surgery_state（`code/__DEFINES/bodyparts.dm` L72-133，10 个）

| 状态位 | 位值 | 含义 | 玩家可见描述（SURGERY_STATE_READABLE） |
|---|---|---|---|
| `SURGERY_SKIN_CUT` | 1<<0 | 皮肤已切开（切口） | "Skin is cut" |
| `SURGERY_SKIN_OPEN` | 1<<1 | 皮肤已翻开——**99% 手术必需** | "Skin is open" |
| `SURGERY_VESSELS_UNCLAMPED` | 1<<2 | 血管已暴露、切开、**正在流血** | "Blood vessels are unclamped" |
| `SURGERY_VESSELS_CLAMPED` | 1<<3 | 血管已暴露但被夹住 | "Blood vessels are clamped" |
| `SURGERY_ORGANS_CUT` | 1<<4 | 器官已切开/器官与肢体分离的切口 | "Organs are cut" |
| `SURGERY_BONE_DRILLED` | 1<<5 | 骨已钻孔（与锯互斥） | "Bone is drilled" |
| `SURGERY_BONE_SAWED` | 1<<6 | 骨已锯开 | "Bone is sawed" |
| `SURGERY_PLASTIC_APPLIED` | 1<<7 | 高级整容手术：塑料已敷上 | "Plastic is applied" |
| `SURGERY_PROSTHETIC_UNSECURED` | 1<<8 | 义肢手术：义肢未固定 | "Prosthetic is unsecured" |
| `SURGERY_CAVITY_WIDENED` | 1<<9 | 胸腔已扩开（腔体植入用） | "Cavity is opened wide" |

**分组宏**：`ALL_SURGERY_SKIN_STATES`(cut|open)、`ALL_SURGERY_VESSEL_STATES`(unclamped|clamped)、`ALL_SURGERY_BONE_STATES`(drilled|sawed)、`ALL_SURGERY_ORGAN_STATES`(organs_cut)。

**自动清理**：手术收尾时清除 `ALL_SURGERY_STATES_UNSET_ON_CLOSE`（皮肤/血管/骨/器官/腔体全部）。

**流血量**：血管未夹 = **1.5 血/秒**；血管夹住或器官切开 = **0.2 血/秒**（`UNCLAMPED_VESSELS_BLEEDING` / `CLAMPED_VESSELS_BLEEDING`）。

**判定宏**：`HAS_SURGERY_STATE` / `LIMB_HAS_SURGERY_STATE` / `LIMB_HAS_ANY_SURGERY_STATE`；`LIMB_HAS_SKIN` / `LIMB_HAS_BONES` / `LIMB_HAS_VESSELS`（按生物状态推导）。

### 2.4 肢体禁用与 EMP

- `can_be_disabled`：只有人类附属肢体（臂/腿）默认 TRUE；头/躯干不可禁用。
- `disabling_threshold_percentage`：默认 `LIMB_NO_DISABLE = -1`（不可因伤害禁用）；机器人肢体 = 1（100% 最大伤害才禁用）；部分特殊肢体 = 0（随时可禁用）。`LIMB_NO_DISABLE` 时伤口致残逻辑替代。
- **EMP 对义体肢体**（`code/__DEFINES/bodyparts.dm` L31-46，重 EMP 翻倍）：
  - 义体肢体受 EMP：brute +2、burn +1.5；伤害超阈值 → 瘫痪 3 秒
  - 义腿：击倒 3 秒；义胸：硬晕 3 秒 + 摇晃 5 秒；义头：视觉错乱 6 秒
  - 瘫痪判定阈值：`robotic_emp_paralyze_damage_percent_threshold = 0.3`（30% 伤害）

### 2.5 关键手术前置状态（operations 目录）

| 手术 | 必需状态 | 工具（倍率）/ 时间 |
|---|---|---|
| 截肢 amputate limb | SKIN_OPEN + BONE_SAWED | 手术刀 1.05（能量剑 1.5/刀 2.25/碎片 2.85）；6.4 秒 |
| 机器人拆解 disassemble | SKIN_OPEN | 扳手 1.05；2 秒 |
| 器官插入/取出 | SKIN_OPEN + ORGANS_CUT | 插入=器官本身(1.0)；取出=止血钳 1.0 / 撬棍 1.8 / 叉子 2.85 |
| 器官修复（有机物） | SKIN_OPEN + ORGANS_CUT + BONE_SAWED | 见 5.2 章 |
| 腔体植入（打开腔体） | SKIN_OPEN + ORGANS_CUT（阻塞 CAVITY_WIDENED） | 手术刀 |
| 腔体植入（放入物品） | SKIN_OPEN + ORGANS_CUT + CAVITY_WIDENED | 物品本身 |
| 更换/装配肢体 augment | SKIN_OPEN | 目标肢体本身 |
| 耳手术 | SKIN_OPEN（阻塞 VESSELS_UNCLAMPED；有骨部位须骨完好） | 止血钳 1.05 / 螺丝刀 2.25 / 笔 4；6.4 秒 |

---

## 第九卷 · 主要器官列表

> 未注明均为：zone=胸、maxHealth=100、healing=STANDARD、decay=STANDARD、`ORGAN_ORGANIC|ORGAN_EDIBLE|ORGAN_VIRGIN`。

### 3.1 脑 Brain（`code/modules/mob/living/brain/brain_item.dm`）

| 项目 | 值 |
|---|---|
| 位置 | 头（BODY_ZONE_HEAD），槽 `brain` |
| 血量 | `maxHealth = BRAIN_DAMAGE_DEATH = 200`；low=45、high=120 |
| 腐烂 | 0.5 × STANDARD → **~30 分钟** |
| 标志 | `ORGAN_ORGANIC | ORGAN_VITAL | ORGAN_PROMINENT`（**唯一 vital 器官**：移除即死） |
| 自带特质 | TRAIT_ADVANCEDTOOLUSER、TRAIT_LITERATE、TRAIT_CAN_STRIP |
| 功能 | 灵魂容器（brainmob）、脑伤（brain trauma）系统、技能芯片（最多 5 槽 / 复杂度上限 3） |
| 损伤后果 | 20 轻度 / 60 眨眼失同步 / 100 重度 / 200 死亡；`apply_organ_damage` 时按伤害滚动脑伤（基础概率=伤害值，超过阈值每点+1%） |
| 修复 | 曼尼托（mannitol）点滴：每 1u 治愈 2 点伤害（仅有机脑，`set_organ_damage` 同时清除衰竭）；脑损伤达 200 → 死亡（除非 `TRAIT_BRAIN_DAMAGE_NODEATH`） |
| 可替换性 | 可手术换脑（带 brainmob 的脑插入会转移灵魂）；被移除的头颅可配 MMI/正脑等 |

### 3.2 心脏 Heart（`_heart.dm`）

| 项目 | 值 |
|---|---|
| 位置 | 胸，槽 `heart`；`healing_factor`=STANDARD；**decay=2.5×STANDARD → ~6 分钟衰竭（最脆弱）** |
| 功能 | 泵血：`blood_regeneration_multiplier`（默认 1，基础 0.25u 血/tick）；心率音效；心跳状态机（Stop/Restart） |
| 失效后果 | 心跳停止 → 心脏骤停（Cardiac Arrest）；`get_blood_regeneration_multiplier()` 在 FAILING/EMP/停跳时 = 0；可电击除颤 |
| 伤害提示 | low "胸口刺痛"；high "胸口剧痛、呼吸变快" |
| 可替换性 | 手术更换；体外有机心脏 12 秒后自动停跳（`stop_if_unowned`），可手动重启 8 秒 |
| 特殊变体 | cursed heart（手动泵血组件，泵延迟 3 秒、每次泵血损失 20% 血量）、freedom（机器人旗标+肾上腺素）、pod mitochondria、evolved（10% 概率自愈+硬濒危给阿托品）、sacred（挡魔法，每次挡 50 器官伤害） |

### 3.3 肺 Lungs（`_lungs.dm`，1156 行，气体处理核心）

| 项目 | 值 |
|---|---|
| 位置 | 胸，槽 `lungs`；decay=**0.9×STANDARD → ~16.5 分钟** |
| 呼吸安全阈值（分压 kPa） | O₂ 最低 **16**；CO₂ 最高 **10**；等离子最高 **0.05**（>0 即有伤害）；N₂O 检测 0.08 / 麻痹 1 / 睡眠 5；BZ 致幻 1 / 脑伤 10；healium 昏迷 3 / 深睡 6；helium 变声 5；tritium 辐射 1~15 mol（概率 10~60% 线性插值） |
| 窒息伤害 | `SUFFOCATION_OXYLOSS = 3` 氧损/秒（按缺压比例缩放）；硬濒危时 ×0.22 |
| 特殊气体 | pluoxium 按 8 倍 O₂ 处理并代谢为试剂；freon 灼伤（>40pp 时 15 火伤+沉默 6s）；halon 转试剂；zauker 转试剂；miasma 恶心（0.1×pp 反感/tick） |
| 失效后果 | 咳嗽（low 2.5%/tick、high 5%/tick）、衰竭时 "无法呼吸" 并 `failed=TRUE`；`received_pressure_mult` 可被支气管扩张/收缩（≤0 完全闭锁需手术） |
| 修复 | 肺叶切除术（lobectomy）4.2 秒；哮喘旁路手术 |

### 3.4 肝 Liver（`_liver.dm`）

| 项目 | 值 |
|---|---|
| 位置 | 胸，槽 `liver`；decay=STANDARD（~15 分钟） |
| 代谢参数 | `toxTolerance = 3`（可忽略的毒素量）、`liver_resistance = 1`（越低毒素越伤肝）、`alcohol_tolerance = ALCOHOL_RATE`、`filterToxins = TRUE` |
| 功能 | 全身化学代谢（`metabolize` 主入口）；没有肝 → `liverless` 代谢；`TRAIT_STABLELIVER`/`TRAIT_LIVERLESS_METABOLISM` 免疫衰竭 |
| 衰竭后果 | **NOVA：每阶段 180 秒**（原 60 秒），5 个阶段渐进：① 毒损 0.2/s+恶心 → ② 0.4/s+嗜睡 → ③ 0.6/s+随机器官 0.2/s+流涎 → ④ 0.8/s+器官 0.5/s+昏厥 2.5s → ⑤ 呕血+尖叫+昏厥 5s；另有对应呕吐/尖叫演出 |
| 修复 | 肝部分切除术（hepatectomy）5.2 秒，可修到 10% 伤害（heal_to_percent=0.1） |
| 可替换性 | 手术更换；研磨产出 peptides 5u |

### 3.5 胃 Stomach（`_stomach.dm`）

| 项目 | 值 |
|---|---|
| 位置 | 胸，槽 `stomach`；decay=**1.15×STANDARD → ~13 分钟** |
| 消化 | `metabolism_efficiency = 0.05`（最低 0.025）；`STOMACH_METABOLISM_CONSTANT = 0.25`；食物试剂按 `max(0.05×体积+0.25, 代谢率)` 每秒滴注给身体 |
| 饥饿 | `HUNGER_FACTOR = 0.05` 营养/秒；饱腹/饥饿改变 `metabolism_efficiency`（1.25 饱腹 / 0.8 濒饿）；**NOVA：躺下或昏迷时饥饿速率 ×0.5**；TRAIT_FAT 机制（overeatduration ≥ 200 秒 → 肥胖） |
| 恶心 | `disgust_metabolism = 1`；呕吐概率公式 `2.5 + 0.025×disgust`/tick |
| 胃内容物 | 吞下的物品被酸蚀；`TRAIT_STRONG_STOMACH` 酸力 10；锐利物品每 w_class 造成 1 器官伤害，导致 cut_open_damage（=50% maxHealth），可用手术刀/烧灼器处理（3 秒） |
| 失效后果 | 损伤>10 时胃内营养物触发呕吐（低阈值概率 `0.0125×伤害×营养²`/tick，高阈值 0.05×）；被打（伤害≥9）可能呕吐出物品 |
| 修复 | 胃部分切除术（gastrectomy）5.2 秒，可修到 20% 伤害 |

### 3.6 阑尾 Appendix（`_appendix.dm`）

| 项目 | 值 |
|---|---|
| 位置 | **腹股沟（BODY_ZONE_PRECISE_GROIN）**，槽 `appendix` |
| 阑尾炎概率 | `APPENDICITIS_PROB = 100 × (0.1 × 1/25 / 3600)` ≈ 0.000111%/秒（设计：25 人群体每小时约 1 人发病）；**NOVA 排除 TRAIT_TEMPORARY_BODY** |
| 炎症阶段 | 0→1（发病，获得 TRAIT_DISEASELIKE_SEVERITY_MEDIUM）；阶段 1（咳嗽 2.5%/tick）→ 2（剧痛+器官 5 伤+晕 40-60 帧+毒 1）→ 3（呕吐+器官 15 伤）；升级概率 2%/tick |
| 失效后果 | 衰竭时强制 **2 毒损/秒** |
| 可替换性 | 手术切除（发炎时扫描显示 "Inflamed"，移除即愈）；研磨产出 bad_food 5u |

### 3.7 眼 Eyes（`_eyes.dm` + `eyes_species.dm`）

| 项目 | 值 |
|---|---|
| 位置 | 眼（BODY_ZONE_PRECISE_EYES），槽 `eye_sight` |
| 血量 | **maxHealth = 50**（标准的一半）；high=30、low=20 |
| 损伤后果 | >20 近视（等级 2）、>30 重度近视（等级 3）、50 → **失明**（on_begin_failure 永久致盲）；`oculine` 药物治疗、手术修复、眼罩保护 |
| 功能参数 | `sight_flags`、`tint`、`flash_protect`、`see_invisible`、`lighting_cutoff`（夜视）、`color_cutoffs`、`pepperspray_protect` |
| 疤痕系统 | 子弹击中头部 10%×伤害概率造成单眼疤痕：每只眼疤 -15 上限血量+近视，双眼疤 → 疤痕性失明（`EYE_SCARRING_TRAIT`）；`enter_wardrobe` 时自动修复 |
| 眨眼动画 | 基础间隔 5 秒±1 秒、眨眼 0.15 秒；脑伤 ≥60 时双眼不同步 |
| 物种变体 | 见第 4 章 |

### 3.8 耳 Ears（`_ears.dm`）

| 项目 | 值 |
|---|---|
| 位置 | 头，槽 `ears`；decay=STANDARD |
| 损伤机制 | `temporary_deafness` 临时失聪（秒）；`damage_multiplier` 声伤倍率（默认 1，猫耳 2、义耳 1.2）；`bang_protect` 防爆音 |
| 失效后果 | 衰竭（on_begin_failure）→ 永久失聪（TRAIT_DEAF）；损伤>10 时随机耳鸣 4 秒+铃声（概率 damage/60）/tick；失聪者说话变大喊大叫 |
| 修复 | 耳手术（ototomy）6.4 秒，可重复，修复后临时失聪降至 40 秒；inacusiate 药物；耳罩 |

### 3.9 舌 Tongue（`_tongue.dm`）

| 项目 | 值 |
|---|---|
| 位置 | 嘴（BODY_ZONE_PRECISE_MOUTH），槽 `tongue` |
| 功能 | 语言列表（`languages_possible`：common/uncommon/spinwarder/draconic/codespeak/monkey/kobold(NOVA)/narsie/beachbum/aphasia/piratespeak/moffic/sylvan/shadowtongue/terrum/nekomimetic）；味觉（`taste_sensitivity` 默认 15）、`say_mod` 动词 |
| 失效后果 | 衰竭 → 失去 TRAIT_SPEAKS_CLEARLY（口齿不清）+ 失味症（TRAIT_AGEUSIA） |
| 移除 | 无舌 → 失味（NO_TONGUE_TRAIT 来源）；机器人语音盒（tongue/robot）可说所有语言（"电子的魔法"）并附 SPAN_ROBOT |

### 3.10 声带 Vocal Cords（`_vocal_cords.dm`）

- 槽 `voice`；**decay=0、healing=0**（永不衰减/修复，反正没伤害效果）。
- 基类无效果；子类通过 `:v` 频道触发：`adamantine`（魔像广播：所有带精金共鸣器的碳基+观察者收到）。

---

## 第十卷 · 物种器官变体

### 4.1 蜥蜴 Lizard（human species `mutanttongue/eyes`）

| 器官 | 变体 | 差异 |
|---|---|---|
| 舌 | forked tongue | 说 "hisses"；母语 draconic + **ashtongue(NOVA)**；s 变 sss、x→kss 替换；味觉更敏感（10）；双重音轨滤波 |
| 眼 | reptile eyes | 竖瞳；**双眼不同步眨眼**（synchronized_blinking=FALSE） |

### 4.2 蛾 Moth

| 器官 | 变体 | 差异 |
|---|---|---|
| 眼 | moth eyes | `flash_protect = FLASH_PROTECTION_SENSITIVE`（怕闪光）；无眨眼动画；复眼无虹膜 |
| 翅膀 | moth wings（外部器官） | 纯外观（普通款）；**functional 款可飞行**：jetpack 组件、推力 2.25N、`TRAIT_MOVE_FLOATING`；飞行条件：清醒、未躺、未被连体衣遮挡（HIDEJUMPSUIT）、气压 > 危险低压+10 kPa；飞行时 stun_mod ×2，落地恢复；撞击滑倒会甩出手持物 |
| 舌 | moth tongue | "flutters"；讨厌水果/虫子/内脏；**肉/生食/海鲜视为有毒**；母语 moffic |
| 肺 | （无蛾专属肺） | — |

### 4.3 骷髅 Skeleton

| 器官 | 变体 | 差异 |
|---|---|---|
| 肝 | mass of bones（liver/bone） | `TRAIT_STABLELIVER`（永不衰竭）；**牛奶治愈伤口**：brute 2.5 + burn 2.5（×0.5/tick），并等效 xadone 治疗伤口；**骨痛果汁**：3.25 体力+0.25 钝伤/s，"oof ouch my bones"，过量时随机肢体 200 钝伤 |
| 胃 | mass of bones（stomach/bone） | `TRAIT_NOHUNGER`；代谢效率 0.025（很差） |
| 舌 | bone "tongue" | "rattles"；无味觉；sans 或 papyrus 字体（随机）；会说 calcic |
| 心/肺/眼 | 无专门变体（仍用人类器官，但物种 flag 使其无血/无呼吸需求） | — |

### 4.4 玻璃人 Plasmaman

| 器官 | 变体 | 差异 |
|---|---|---|
| 肺 | plasma filter | **不吸氧**（safe_oxygen_min=0）；**吸等离子**（safe_plasma_min=4，max=0 无伤害）；自带 `TRAIT_NOHUNGER`；老烟枪版 maxHealth=75、healing×0.75 |
| 肝 | reagent processing crystal（liver/bone/plasmaman） | `ORGAN_MINERAL`；`TRAIT_PLASMA_LOVER_METABOLISM`；**等离子/热冰治愈伤口**（xadone 2/s）；**火药当致幻剂**（drugginess 15s + 幻觉） |
| 胃 | digestive crystal（stomach/bone/plasmaman） | 代谢效率 0.06；无 NOHUNGER（玻璃人也进食） |
| 舌 | plasma bone "tongue" | 会说 calcic；母语 calcic |
| 燃烧机制 | 物种层：接触明火自燃（不属器官系统） | — |

### 4.5 魔像 Golem

| 器官 | 变体 | 差异 |
|---|---|---|
| 眼 | resonating crystal | `ORGAN_MINERAL`；无眨眼无虹膜；**矿石共振**技能（10 秒 CD 矿物扫描脉冲）；color_cutoffs(10,15,5) |
| 舌 | golem tongue | `ORGAN_MINERAL`；"rumbles"；只爱 STONE 食物；**什么都吃（连铀都可以）**；母语 terrum |
| 肝 | porous rock | `ORGAN_MINERAL`；把非矿物营养试剂 nutriment_factor 归零（只能吃矿物） |
| 胃 | silicate grinder | `ORGAN_MINERAL`；`TRAIT_ROCK_EATER`；**hunger_modifier=10**（燃料消耗快）；拒绝非 golem_food 进食；饥饿减速 0.5~4（LERP）；饥饿=1 时石化（TRAIT_IMMOBILIZED 等全套） |
| 声带 | adamantine vocal cords + adamantine resonator（头部器官） | 全图广播给所有魔像 |
| 心 | 无专门（golem 物种不需心跳？由 mutantheart 决定） | — |

### 4.6 以太体 Ethereal（NOVA Ethereal Rework 2024）

| 器官 | 变体 | 差异 |
|---|---|---|
| 心 | crystal core | 死亡后**结晶复活**：死亡→TRAIT_CORPSELOCKED→结晶化（推搡重置计时、150 钝伤可打断）→水晶（100 耐久、防火）5 秒引导后 `revive(HEAL_ALL)`；复活后 10% 重度脑伤/90% 轻度脑伤 + `vulnerable_to_damage` 5 分钟（NOVA 新增）；冷却 **5 分钟（NOVA 原 120 秒）** |
| 胃 | biological battery | 内置电池（ethereal cell）当"饥饿"；放电速率 ETHEREAL_DISCHARGE_RATE；电击充电（伤害×2 焦耳并吸收伤害）；充电器充电速度 1/3.5；过充→闪电释放（2 格特斯拉电弧，10% 概率 5 分钟内心脏病） |
| 肺 | aeration reticulum | 耐热（1 级阈值 = 150℃/433K）；**水蒸气电解**：吸 H₂O 呼出 O₂+2H₂ |
| 舌 | electric discharger | "crackles"；味觉更敏感（10，气体光谱仪原理）；母语 voltaic |

### 4.7 其他种族

| 种族 | 器官差异 |
|---|---|
| **僵尸** | undead eyes（color_cutoffs(25,35,5)，视力反而好）；rotting tongue（"moans"，英语→僵尸语词典，eiou→r） |
| **史莱姆/果冻人** | slime vacuole 肺（**吸等离子回血** 0.2×pp 血/tick）；jelly eyes（三只眼）；jelly tongue（chirps，母语 slime，银食物→喜欢） |
| **植物人 Pod** | 全套 pod 器官：pod mitochondria 心、pod vacuole 肺、pod peroxisome 肝（plantbgone 毒 1.5/s）、pod chloroplast 胃、pod eyes/ears/tongue/appendix（"pod thingy"）；食物类型 PODPERSON_ORGAN_FOODTYPES |
| **蜗牛** | snail liver（lube_walking 元素+移动减速 6；盐=灼伤 1/s 并中和）、radula 舌（atempo=0.5 慢速说话）、snail eyes |
| **猫科 felinid** | felinid eyes（反光+竖瞳）；cat tongue（meows，咬击增强：头攻击 +4~7 伤害/+10 效率/锐利；可 feral 模式）；cat ears（伤害倍率 2，可换义体猫耳系列） |
| **Android/机器人** | **完全无器官**：`mutantheart/mutantlungs/mutantliver/mutantstomach = null`（android.dm L33-37）；使用 robot tongue 语音盒；物种层为 BODYTYPE_SYNTHETIC 肢体 |
| **蘑菇人** | fung-eye（夜视三档 0,15,20 / 0,20,35 / 0,40,50）、mush-tongue-room（poofs，母语 mushroom） |
| **异形 Alien** | alien liver（toxTolerance 15、抗性 -66%）、alien eyes（SEE_MOBS）、alien tongue（第二张嘴） |
| **幽灵器官**（ghost 系列） | ghost lungs/liver/stomach/eyes/ears/tongue：`ORGAN_GHOST` + `movement_type = PHASING`（可穿墙） |
| **Ashwalker** | blackened frilled lungs：按 Lavaland 大气自动适配 O₂/N₂/等离子耐受（±5 kPa 容差，CO₂ 容差 ×2） |
| **血族 Hemophage**（NOVA） | `ORGAN_TUMOR_CORRUPTED` 标志；血族肿瘤感染器官（modular_nova） |

---

## 第十一卷 · 义肢/机械器官与修复

### 5.1 机械器官（cybernetic organs）分级

每类（心/肺/肝/胃/耳/眼/舌）都有 4 档：

| 档位 | 血量 | 特点 | EMP 永久损坏概率 |
|---|---|---|---|
| 基本（tier1） | 0.5×100 = **50** | 勉强模拟功能 | **80%/severity** |
| 标准（tier2） | **150** | 略强于有机（如肺需 O₂ 13kPa） | 40%/severity |
| 升级（tier3） | **200** | 显著强化+滤毒（肺 CO₂/等离子 max=20） | 20%/severity |
| 剩余物资 surplus | 35~50（心 50/肺 35/肝 35/胃 35） | 极差+**移除时爆炸** | **100%/severity** |

**各机械器官 EMP 效果**：
- 机械心：晕眩 20 秒+呼吸停止 10（冷却 20 秒）；永久损坏时停跳 10 秒后重启
- 机械肺：losebreath +20（冷却 30 秒）
- 机械肝：毒损 10（冷却 10 秒）
- 机械胃：呕吐（冷却 10 秒）
- 机械耳：直接 20/severity 器官伤害
- 机械眼：闪烁（basic 10×severity% 概率 20×severity 伤害+火花）

**特点**：
- `organ_flags = ORGAN_ROBOTIC`：**不自然愈合、不腐烂**；对疾病免疫（除非疾病含 MOB_ROBOTIC）
- 永不心脏停跳（机械心 `beating` 一直 TRUE）；但 EMP 可临时击停
- 血液再生倍率：机械心 tier2 `blood_regeneration_multiplier = 9`（2.25u 血/tick，默认 0.25u）
- 防 EMP 心（anomalock，NOVA）：伏打战斗义心，需要通量异常核心供能；EMP 免疫+致命状态触发 5 分钟生存模式

### 5.2 机械器官修复（合成体维护）

**有机物与机械器官修复分开**（`operation_organ_repair.dm`）：
- 有机器官修复：`required_organ_flag = ORGAN_TYPE_FLAGS & ~ORGAN_ROBOTIC & ~ORGAN_SYNTHETIC_FROM_SPECIES`（NOVA 加入了排除 SYNTHETIC_FROM_SPECIES）
- 机械器官修复：每个手术都有 `/mechanic` 变体（`required_organ_flag = ORGAN_ROBOTIC`、`OPERATION_MECHANIC` 旗标），使用**扳手**（1.05）等工具 + 卡扣音效：
  - 肺叶切除术/mechanic = "perform maintenance"（空气过滤诊断）
  - 肝切除术/mechanic = 杂质管理系统诊断
  - 冠状动脉搭桥/mechanic = "access engine internals"（撬棍 1.05）
  - 胃切除术/mechanic = 营养处理系统诊断
- 通用修复手术：`heal_to_percent = 0.6`（把伤害设到 maxHealth×0.6），失败 +20% 伤害；**每器官只能修复一次**（`TRAIT_ORGAN_OPERATED_ON`），除非 `repeatable`（耳手术可重复，修复到 0% 并重置临时失聪至 40 秒）
- 专用数值：肺叶切除 4.2s（修到 60%）、肝切除 5.2s（修到 **10%**）、胃切除 5.2s（修到 **20%**）、冠脉搭桥 9s（默认 60%）、耳手术 6.4s（可重复，修到 0%）
- 修复成功同时清除 `ORGAN_EMP`（`organ_flags &= ~ORGAN_EMP`）

**机械肢体**（`robot_bodyparts.dm`）：
- cyborg 肢体：`BODYTYPE_ROBOTIC`；剩余义肢 max_damage=20、伤害系数 2.5、禁用阈值 100%；高级义肢 75/0.5
- 义肢安装：`augment limb` 手术（SKIN_OPEN，用目标肢体）或普通接肢手术；NOVA 增加 `TRAIT_ROBOTIC_LIMBATTACHMENT`（机器人肢体轻松装卸 quirk）
- EMP 对义肢：见 2.4

### 5.3 危险器官移除（surplus 器官爆炸）

`/datum/element/dangerous_organ_removal`（`code/datums/elements/dangerous_surgical_removal.dm`）：
- 挂载于全部 surplus 器官（心/肺/肝/胃）与 lost_crew 的爆脑
- 触发：`COMSIG_ORGAN_SURGICALLY_REMOVED`（surgical=TRUE）或 `COMSIG_ORGAN_REMOVED`
- **正在衰竭（FAILING/EMP）的 surplus 器官手术取出不会爆炸**（`if(surgical && FAILING|EMP) return`）
- 爆炸：light_impact_range=1；旁边的人 15 钝伤；器官自身销毁

### 5.4 纳米机器人修复（NOVA ORGAN_NANOMACHINE）

- `ORGAN_NANOMACHINE`(1<<19) 是 NOVA 专属标志，用于 **protean 变形人**（modular_nova/modules/protean/）：protean 的心/肺/肝/胃/眼/耳/舌/脑全部 `ORGAN_ROBOTIC | ORGAN_NANOMACHINE`（肝还带 UNREMOVABLE）
- 判定：protean 物种器官替换时，纳米器官与机器人器官同等对待（见 protean_species.dm L115/131）
- 纳米器官可通过 protean 特有的液态重塑机制自我重组（不依赖常规手术）

---

## 第十二卷 · 断肢机制 Dismemberment

### 6.1 断肢条件（`dismemberment.dm` + `_bodyparts.dm` L860-894）

**两套判定，满足其一即可**：

**A. 伤口致残法（flesh-and-bone 肢体）**：
- 前提：`can_dismember()`（无 `BODYPART_UNREMOVABLE`、主人无 `TRAIT_NODISMEMBER`/GODMODE）
- 肢体必须同时满足：**外部被毁**（BODYPART_MANGLED_EXTERIOR：临界级切割/穿刺伤口撕开皮肤）+ **内部被毁**（BODYPART_MANGLED_INTERIOR：至少重度骨裂）
- 单次打击伤害 ≥ `DISMEMBER_MINIMUM_DAMAGE = 10`
- 概率：`base_chance = 打击伤害 + (已有伤害/max_damage)×50 + 各伤口加成`；`prob(base_chance)` → 成功则生成 `loss` 伤口并 `apply_dismember`
- 伤害类型：必须钝/切/刺类（brute）

**B. 备选公式（无伤口系统/不可致残时）**：
- `hp_percent_to_dismemberable = 0.8`：总伤害 ≥ **最大伤害的 80%** 且（`use_alternate_dismemberment_calc_even_if_mangleable` 或 无任何伤口能致残内外）
- 例：普通手臂 60 血 → 48 伤害时可切；剩余义肢 20 血 → 16；头/躯干 250 → 200

### 6.2 断肢过程与后果（`dismember()`）

1. 胸腔受到反冲伤害：`clamp(brute/2×coeff, 15, 50)` 钝 + `clamp(burn/2×coeff, 0, 50)` 灼
2. 尖叫+断肢音效；添加"被截肢"情绪事件与记忆
3. `drop_limb(dismembered=TRUE)`；随后 `bleed(20~40)` 出血堆叠
4. 断肢被抛飞（随机方向 2~max(throw_range/2,2) 格，撞墙停）
5. 灼烧致断 → 肢体 `burn()` 烧毁
6. 手臂：手持物品、手套、手铐全部掉落；腿：脚镣、脚部装备掉落
7. 头部：掉落眼/耳/口罩/头盔全部装备；名字改为 "X's head"；**脑（vital）随头移除 → 死亡**
8. 胸腔 `dismember()`（被开膛）：腔内器官按 `drop_when_organ_spilling`（NOVA 标志）掉出 + 腔体物品掉出

**接肢/替换**：`try_attach_limb()`（需 `acceptable_bodytype`/`acceptable_bodyshape` 匹配）；成功清除断肢情绪、添加"幻肢痛"情绪（假体/重新接上时）；断肢处会保留疤痕（`regenerate_limb` 用 phantom loss 疤痕模拟"还记得被怎么断的"）。

### 6.3 截肢手术

- `amputate limb`：手术刀 1.05（能量剑 1.5 / 刀 2.25 / 碎片 2.85），**6.4 秒**，需要 SKIN_OPEN + BONE_SAWED
- `disassemble limb`（机器人）：扳手，2 秒，仅需 SKIN_OPEN
- 木钉肢体 `detach wooden limb` 等特例

### 6.4 再生能力

- `regenerate_limbs(excluded_zones)` / `regenerate_limb(zone)`：重建缺失肢体并重新插入该区突变器官；重建部位会带断肢疤痕
- 物种级再生：**dullahan**（无头骑士，头可再生）、**jellypeople/史莱姆**（再生）；`TRAIT_REGENORGANS` 类机制
- 肢体生长机（limbgrower）可产出离体肢体；植物肢体（BODYTYPE_PLANT）在光合作用时自愈、阴影肢体（BODYTYPE_SHADOW）在 shadowheal 时自愈
- 常规人类**没有**自然断肢再生——必须手术接回/装义肢

---

---

# 第十三卷 · 伤口系统

**代码**: `code/datums/wounds/`（6 系列 181 种子类型）

## 9.1 创伤判定公式

```
base_roll = rand(1, damage^WOUND_DAMAGE_EXPONENT)   // 伤害指数放大
injury_roll = base_roll + wound_bonus + Σ伤口阈值惩罚 + 部位固有抗性 - 护甲消融%
```

- **装甲消融**：护甲 WOUND 评级按百分比降低伤害（无甲则加 exposed_wound_bonus）
- **系列惩罚**：同系列伤口叠加惩罚（伤口越多越易再伤）
- **伤口晋升**：新伤比旧伤重时替换（同系列只能有一个同级）
- 决定断肢：`injury_roll > 断肢阈值` 且部位伤害比例概率

## 9.2 伤口系列（6 大类）

| 系列 | 类型 | 代表伤口 | 手术/治疗 |
|---|---|---|---|
| **骨折 Blunt(Bone)** | 4 级 | 脱臼 → 发丝骨折 → 复合骨折（含皮肤破裂） | 接骨器复位 / 骨胶 / 手术复位 |
| **割伤 Slash** | 6 级 | 擦伤 → 裂伤 → 撕脱伤（weeping avulsion） | 清创 / 缝合 |
| **穿刺 Pierce** | 9 级 | 针扎 → 刀刺 → **弹孔** → **眼球穿刺** → **鼓膜破裂** → 腔体破裂 | 血管重排 / 封血管 |
| **烧伤 Burn** | 6 级 | 二度 → 三度 → 灾难性烧伤 → 圣印/古印 | 清创 |
| **颅裂 Cranial Fissure** | 1 级 | 颅骨裂缝 | 颅骨修复 |
| **断肢 Loss** | 1 级 | 肢体损失 | 接肢手术 |

## 9.3 伤口状态联动

- 伤口可附加手术状态（如复合骨折强制 BONE_SAWED 需求）
- 未进行手术时隐藏伤口附加状态（保持界面简洁）
- 伤口产生阈值惩罚、伤害倍率惩罚、流血速率

---

---

## 第十四卷 · 自动手术器（Autosurgeon）

### 7.1 基本功能（`autosurgeon.dm`）

- **一键植入**：将器官/植入体/技能芯片装进使用者或目标体内，无需手术流程
- 加载：对 autosurgeon 使用器官物品（`item_interaction`）；可设 `organ_whitelist` 限制
- 取出：螺丝刀拆卸（消耗 1 次使用次数）
- 使用：
  - 对自己：`attack_self` → 立即植入（无等待）
  - 对他人：`attack` → **8 秒 do_after × surgery_speed**（植入日志记入战斗记录）
- `uses`：默认 INFINITY；一次性变体 uses=1；用尽后描述变为"已报废"
- 植入成功音效：circsaw
- 多区器官：`valid_zones` 存在时按持握手自动选臂（右手持 → 右臂/右腿，左手持 → 左臂/左腿）
- 失败保护：正在手术的 mob 不能吃器官（`block_nom`）

### 7.2 已知变体

| 变体 | 速度 | 次数 | 内含 |
|---|---|---|---|
| `medical_hud` | 1.0 | 1 | 医疗 HUD 眼植入体 |
| `syndicate`（suspicious） | **0.75** | ∞ | 通用（可换装）；TRAIT_CONTRABAND |
| └ laser_arm | 0.75 | 1 | 战斗激光臂 |
| └ thermal_eyes / xray_eyes | 0.75 | ∞/1 | 热成像 / X 光眼 |
| └ anti_stun | 0.75 | ∞/1 | 抗晕脑植入体 |
| └ reviver | 0.75 | ∞/1 | 复苏者（胸腔） |
| └ commsagent | 0.75 | ∞ | **只接受舌头**（恐怖特化） |
| └ emaggedsurgerytoolset | 0.75 | 1 | 强化手术工具臂 |
| └ contraband_sechud | 0.75 | 1 | 违法 SecHUD（扫描不可见） |

---

## 第十五卷 · NOVA EDIT 修改点汇总

### 8.1 定义层（__DEFINES）

| 位置 | 改动 |
|---|---|
| `surgery.dm` L46-53 | **新增 3 个器官标志**：`ORGAN_SYNTHETIC_FROM_SPECIES`(1<<17)、`ORGAN_TUMOR_CORRUPTED`(1<<18)、`ORGAN_NANOMACHINE`(1<<19) |
| `bodyparts.dm` L9 | `LIMB_MAX_HP_DEFAULT` 50 → **60** |
| `bodyparts.dm` L11 | `LIMB_MAX_HP_CORE` 200 → **250** |
| `bodyparts.dm` L61 | 新增 `LIMB_COLOR_SYNTH = 100`（合成体高优先级着色） |
| `mobs.dm` L177-182 | 新增 `BODYTYPE_SYNTHETIC`(1<<10)、`BODYTYPE_GHOUL`(1<<11) |
| `mobs.dm` L195-218 | 新增 `BODYSHAPE_CUSTOM`/`HIDE_SHOES`/`ALT_FACEWEAR_LAYER`/taur 全套（1<<15~23） |
| `mobs.dm` L222 | `BODYPART_CAN_BE_BIOSCRAMBLED` 排除清单追加 `BODYTYPE_GHOUL` |
| `mobs.dm` L402 | 以太结晶冷却 120 秒 → **5 分钟**（Ethereal Rework 2024） |
| `~nova_defines/synth_defines.dm` | 新增 `IS_SYNTHETIC_ORGAN()` 宏 |
| `~nova_defines/mobs.dm` | 灰阶肢体图标替换为 modular_nova 版本（human/humanoid parts） |

### 8.2 器官核心（organs/）

| 位置 | 改动 |
|---|---|
| `_eyes.dm` L58 | `native_fov` 默认 `FOV_90_DEGREES` → **NONE**（关闭原生视野限制） |
| `_eyes.dm` L132-139 | 夜视：不再强制 lighting_cutoff，改为按夜视 quirk 颜色计算 `color_cutoffs` |
| `_eyes.dm` L143-146, 354-372 | 发光眼（emissive）支持；`eye_icon_state == "None"` 时隐藏眼球（合成体无眼） |
| `_eyes.dm` L589 | 新增 `disable_blinking` 服务器配置开关 |
| `_liver.dm` L4 | `LIVER_FAILURE_STAGE_SECONDS` 60 → **180**（肝衰竭节奏放慢 3 倍） |
| `_liver.dm` L398 | 保留 `LIVER_DEFAULT_TOX_RESISTANCE`（modular 层仍引用，不 #undef） |
| `_stomach.dm` L173-176 | **躺下/昏迷时饥饿速率 ×0.5** |
| `_tongue.dm` L92 | 语言列表新增 kobold |
| `_tongue.dm` L186 | 蜥蜴舌母语新增 Ashtongue（Ashwalker） |
| `_tongue.dm` L198-216 | 蜥蜴舌新增俄语替换规则（`russian_text_formation` 配置） |
| `_tongue.dm` L613-626 | 蜗牛舌改为慢速音轨（atempo=0.5），移除字母三连重复；新增开局蜗牛食物偏好（蔬菜/水果/生食喜欢；乳制品/柑橘/糖讨厌） |
| `_ears.dm` L156/174 | 猫耳：移除旧 dna_block，接入 NOVA 定制外观系统 |
| `heart_ethereal.dm` 多处 | Ethereal Rework 2024：复活演出文本、`vulnerable_to_damage` 5 分钟副作用、火花特效 |
| `stomach_ethereal.dm` | 电击吸收伤害、过充火花（10%/tick）、电量恰当时治愈毒损 |
| `_bodyparts.dm` 相关 | 器官 Insert/Remove 时同步 `dna.mutant_bodyparts`（定制外观系统） |
| `heart_anomalock.dm` | 移除自植入（attack）、EMP 保护改为 PRE_EMP_ACT、移除时不再删除自身 |
| `organ_movement.dm` L42-45 | Remove 时清理 `mutant_bodyparts` 条目（NOVA ADDITION） |

### 8.3 身体部位（bodyparts/）

| 位置 | 改动 |
|---|---|
| `_bodyparts.dm` L620-625 | 接肢判定新增 `TRAIT_ROBOTIC_LIMBATTACHMENT`（机器人肢体装卸 quirk；放行 peg 与 android） |
| `_bodyparts.dm` L794-807 | 医疗相关 NOVA 附加逻辑（MEDICAL 标记段） |
| `_bodyparts.dm` L948-953 | `heal_damage`：死亡且 `TRAIT_REVIVES_BY_HEALING` 时血量>50 自动复活 |
| `_bodyparts.dm` L1239, 1253-1273, 1385-1388 | 肢体着色/Alpha/隐形肢体缓存修复 |
| `_bodyparts.dm` L1422-1425 | 肢体颜色附加 alpha 通道 |
| `_bodyparts.dm` L1461-1516 | 肢体花纹（markings）系统 |
| `_bodyparts.dm` L1912 | 趾行肢体图标 key（`_digi` 后缀） |
| `_bodyparts.dm` L1957-1958 | 平衡：部分伤害路径 brute/burn ×1.3 |
| `_bodyparts.dm` L394-441 | i18n：损伤描述改中文拼接（LANG 反查） |
| `dismemberment.dm` L70-73 | **器官溢出开关 `drop_when_organ_spilling`**（NOVA：开膛不再自动掉出全部器官） |
| `ghetto_parts.dm` | 土制肢体禁用阈值 1（满伤才瘫痪） |

### 8.4 手术操作层

| 位置 | 改动 |
|---|---|
| `operation_organ_repair.dm` L7 | 有机器官修复排除 `ORGAN_SYNTHETIC_FROM_SPECIES`（物种合成器官不能用有机手术修） |
| 全目录 I18N CODEMOD | 所有玩家可见字符串改写为 `LANG()` 哈希键（`modular_nova/modules/i18n/`）；手术工具名经 `lang_reverse_text` 反查中文化 |

### 8.5 modular 层（器官相关）

| 位置 | 改动 |
|---|---|
| `modular_nova/modules/protean/` | protean 变形人：全套 `ORGAN_NANOMACHINE` 器官（脑/心/肺/肝/胃/眼/耳/舌），肝 UNREMOVABLE |
| `modular_nova/modules/customization/` | 血族器官（ORGAN_TUMOR_CORRUPTED）、夜视 quirk 颜色、合成体定制 |
| `modular_nova/modules/bodyparts/` | 灰阶肢体图标/纹理资源替换 |
| `~nova_defines/medical_defines.dm` 等 | 医疗/肢体相关常量微调 |

---

## 附录A · 关键常量速查

| 常量 | 值 |
|---|---|
| STANDARD_ORGAN_THRESHOLD | 100 |
| STANDARD_ORGAN_HEALING | 50/100000（0.05%/s） |
| STANDARD_ORGAN_DECAY | 111/100000（0.111%/s ≈ 15min 衰竭） |
| BRAIN_DAMAGE_MILD / ASYNC_BLINKING / SEVERE / DEATH | 20 / 60 / 100 / 200 |
| 心脏/肺/胃/脑 decay | 2.5×/0.9×/1.15×/0.5× STANDARD |
| 眼 maxHealth / high / low | 50 / 30 / 20 |
| 肝 toxTolerance / liver_resistance / 衰竭阶段 | 3 / 1 / 每阶段 180s（NOVA） |
| 胃 metabolism_efficiency / 常量 | 0.05 / 0.25 |
| 肺安全阈值 | O₂≥16、CO₂≤10、等离子≤0.05 kPa；N₂O 0.08/1/5；BZ 1/10；healium 3/6；helium 5 |
| 窒息伤害 | 3 OXY/s（硬濒危 ×0.22） |
| BREATH_VOLUME | 1.99 L |
| LIMB_MAX_HP（默认/核心/高级/剩余） | 60 / 250 / 75 / 20 |
| 肢体伤害系数（默认/核心/高级/剩余） | 0.75 / 1 / 0.5 / 2.5 |
| 断肢最小伤害 / 备选百分比 | 10 / 80% |
| 血管流血（未夹/夹住） | 1.5 / 0.2 血/s |
| 义肢 EMP 伤害 / 瘫痪 | brute 2 + burn 1.5 / 3s（重 EMP 翻倍） |
| 器官修复（通用） | 修到 60% 伤害、失败 +20%、每器官 1 次 |
| 器官修复（肝/胃/耳） | 10% / 20% / 0%（可重复） |
| 截肢手术 | 6.4s，SKIN_OPEN+BONE_SAWED |
| Autosurgeon | 他人 8s×速度；自用即时；surgery_speed 1.0（黑市 0.75） |
| 阑尾炎概率 | ~0.000111%/s（25 人/小时约 1 例） |

## 附录B · 代码路径索引

| 系统 | 文件 |
|---|---|
| 器官基类/伤害/衰竭 | `code/modules/surgery/organs/_organ.dm` |
| 器官插入/移除/肢体层 | `code/modules/surgery/organs/organ_movement.dm` |
| 器官标志位 | `code/__DEFINES/surgery.dm` L1-56 |
| 器官槽位/阈值 | `code/__DEFINES/DNA.dm` L88-192 |
| 心脏 | `organs/internal/heart/_heart.dm`、`heart_ethereal.dm`、`heart_anomalock.dm` |
| 肺 | `organs/internal/lungs/_lungs.dm` |
| 肝 | `organs/internal/liver/_liver.dm`、`liver_skeleton.dm`、`liver_plasmaman.dm`、`liver_golem.dm` |
| 胃 | `organs/internal/stomach/_stomach.dm`、`stomach_ethereal.dm`、`stomach_golem.dm` |
| 眼/耳/舌/声带 | `eyes/_eyes.dm`、`eyes/eyes_species.dm`、`eyes/eyes_augments.dm`、`ears/_ears.dm`、`tongue/_tongue.dm`、`vocal_cords/_vocal_cords.dm` |
| 阑尾 | `organs/internal/appendix/_appendix.dm` |
| 脑 | `code/modules/mob/living/brain/brain_item.dm` |
| 义体植入物 | `organs/internal/cyberimp/`（brain/chest/eyes/arms/internal） |
| 外部器官 | `organs/external/_visual_organs.dm`、`tails.dm`、`spines.dm`、`wings/`（functional_wings、moth_wings） |
| Autosurgeon | `organs/autosurgeon.dm` |
| 身体部位基类 | `code/modules/surgery/bodyparts/_bodyparts.dm`（2110 行） |
| 断肢 | `bodyparts/dismemberment.dm`、`_bodyparts.dm` L860-894 |
| 肢体类型 | `bodyparts/default_parts.dm`、`head.dm`、`robot_bodyparts.dm`、`ghetto_parts.dm`、`stumps.dm` |
| 手术状态位/肢体血量 | `code/__DEFINES/bodyparts.dm` |
| 手术操作 | `code/modules/surgery/operations/`（organ_repair、organ_manip、amputation、cavity_implant、replace_limb…） |
| 危险器官爆炸 | `code/datums/elements/dangerous_surgical_removal.dm` |
| NOVA protean 纳米器官 | `modular_nova/modules/protean/code/organs/` |


---