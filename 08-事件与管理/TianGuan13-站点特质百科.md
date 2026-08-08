# TianGuan13 站点特质百科 (Station Traits Encyclopedia)

> 基于 TianGuan13 NovaSector 分支源码全量整理。核心：`code/datums/station_traits/`（positive 443 行 + negative 781 行 + neutral 584 行 + job 253 行 + 基类 127 行 + admin 130 行）+ NOVA 扩展（station_traits/birthday_opt_out/job_traits 模块 + master_files 覆写）。
> **范围**：站点特质机制（每局开局抽选、管理层在纳米指挥部报告"Identified shift divergencies"中收到）、56 个可选特质全录（含权重/花费/报告/互斥/效果）、NOVA 修改、彩蛋。

---

## 一、核心机制

### 1.1 什么是站点特质

**站点特质（Station Trait）** 是每局开局时由系统按**预算制**随机抽取的空间站"特性"——影响当局的游戏规则（正面/中性/负面）。管理层（指挥/通讯）在开局后的**纳米指挥部报告**里能看到被识别的特质（"Identified shift divergencies"）。

**代码**: `code/datums/station_traits/_station_trait.dm`（基类 127 行）

### 1.2 抽取机制（预算制）

**代码**: `code/controllers/subsystem/processing/station.dm` L100-159

| 步骤 | 机制 |
|---|---|
| **分类** | 正面/中性/负面三类分别抽 |
| **预算** | 每类从 config 加权抽取预算值（见 1.3） |
| **pick_traits** | 预算内按 weight 加权随机抽，抽中后扣 cost，直到预算花完或无可选 |
| **排除** | 行星站不能有太空绑定特质（反之亦然）；需 AI 特质在无 AI 服跳过；事件有效性检查 |
| **force** | force=TRUE 的特质（调试用）直接启用不抽 |
| **黑名单** | 抽中后，其黑名单内的互斥特质从候选池移除 |

### 1.3 预算配置（config 默认）

**代码**: `code/controllers/configuration/entries/game_options.dm` L463-476

| 类别 | 预算分布（key=预算值, value=权重） | 解读 |
|---|---|---|
| 正面 | 0→8 / 1→4 / 2→2 / 3→1 | **最常见：0 点**（67% 无正面特质） |
| 负面 | 0→8 / 1→4 / 2→2 / 3→1 | 同上 |
| 中性 | 0→10 / 1→10 / 2→3 / 2.5→1 | 0 或 1 点最常见 |

> 默认配置下，每局**大概率没有或只有 1 个**正面/负面特质，中性特质略多。

### 1.4 花费常量

| 常量 | 值 | 含义 |
|---|---|---|
| STATION_TRAIT_COST_FULL | 1 | 满额花费 |
| STATION_TRAIT_COST_LOW | 0.5 | 半额 |
| STATION_TRAIT_COST_MINIMAL | 0.3 | 最低 |

### 1.5 绑定标志（trait_flags）

| 标志 | 含义 |
|---|---|
| STATION_TRAIT_MAP_UNRESTRICTED | 行星+太空均可（默认） |
| STATION_TRAIT_PLANETARY | 仅行星站 |
| STATION_TRAIT_SPACE_BOUND | 仅太空站 |
| STATION_TRAIT_REQUIRES_AI | 需要 AI 才能生效 |

### 1.6 纳米指挥部报告（centcom report）

**代码**: `code/datums/communications.dm` L122-128

- 开局报告含 **"Identified shift divergencies:"**（识别到的轮次偏差）区块
- 只列出 `show_in_report = TRUE` 的特质，每条用 `get_report()` 生成（报告原文）
- 无报告特质不显示——所以玩家/管理层看到的"站点特质"只是**部分**特质（可公开的）

### 1.7 管理员面板

**代码**: `code/datums/station_traits/admin_panel.dm`（130 行）

- **修改空间站特质**（ADMIN_VERB，R_FUN 权限）：设置/回退/清除下局特质
- 可强制指定下局的站点特质列表

---

## 二、特质全录（76 定义 / 70 可选 + 6 抽象父类）

> 全量 76 个 `/datum/station_trait/` 定义（含抽象父类），按类型分组。每个卡片含：类型/权重/花费/报告/黑名单/效果机制。


### 正面特质 Positive（26 个）

<details open>
<summary><b>#1</b> Lucky winner <small>(`)</small> · <b>正</b> · 权重 1 · 花费 1（默认）</summary>

- **类型**: 正 | **权重**: 1 | **花费**: 1（默认）
- **报告**: ✅<br>"Your station has won the grand prize of the annual station charity event. Free snacks will be delivered to the bar every now and then."（站点赢得年度慈善活动大奖，酒吧会时不时收到免费零食）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: `trait_processes=TRUE`；每 6–12 分钟（随机冷却）向随机酒吧区域空投补给舱：随机口味披萨 + 6 瓶啤酒
</details>


<details open>
<summary><b>#2</b> Galactic grant <small>(`)</small> · <b>正</b> · 权重 5 · 花费 1</summary>

- **类型**: 正 | **权重**: 5 | **花费**: 1
- **报告**: ✅<br>"Your station has been selected for a special grant. Some extra funds has been made available to your cargo department."（站点获得特别拨款，货舱部门获得额外资金）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: on_round_start：货舱账户（ACCOUNT_CAR）随机获得 2000–5000 信用点
</details>


<details open>
<summary><b>#3</b> Premium internals boxes <small>(`)</small> · <b>正</b> · 权重 5 · 花费 1</summary>

- **类型**: 正 | **权重**: 5 | **花费**: 1
- **报告**: ✅<br>"The internals boxes for your crew have been upsized and filled with bonus equipment."（乘员内呼吸盒升级扩容并附赠装备）
- **trait_to_give**: `STATION_TRAIT_PREMIUM_INTERNALS`
- **黑名单**: 无
- **效果**: 纯 trait_to_give，由其他系统读取
</details>


<details open>
<summary><b>#4</b> Bountiful bounties <small>(`)</small> · <b>正</b> · 权重 5 · 花费 0.5（LOW）</summary>

- **类型**: 正 | **权重**: 5 | **花费**: 0.5（LOW）
- **报告**: ✅<br>"It seems collectors in this system are extra keen to on bounties, and will pay more to see their completion."（本星系收藏家更热衷赏金，完成赏金报酬更高）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: on_round_start：`SSeconomy.bounty_modifier *= 1.2`（赏金 +20%）
</details>


<details open>
<summary><b>#5</b> Glowsticks party <small>(`)</small> · <b>正</b> · 权重 2 · 花费 1</summary>

- **类型**: 正 | **权重**: 2 | **花费**: 1
- **报告**: ✅<br>"We've glowsticks upon glowsticks to spare, so we scattered some around maintenance (plus a couple floor lights)."（荧光棒太多，散布在维护通道里，外加几盏地灯）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: pregame 阶段异步执行：在约 11.5% 的维护通道地板格子上生成随机颜色荧光棒（已点亮，续航 10–45 分钟），3.4% 概率生成地灯（`/obj/machinery/light/floor`）；避开有密度物体（如窗户）的格子
</details>


<details open>
<summary><b>#6</b> Strong supply lines <small>(`)</small> · <b>正</b> · 权重 5 · 花费 1</summary>

- **类型**: 正 | **权重**: 5 | **花费**: 1
- **报告**: ✅<br>"Prices are low in this system, BUY BUY BUY!"（本系统物价低廉，买买买！）
- **trait_to_give**: 无
- **黑名单**: `distant_supply_lines`（遥远补给线）
- **效果**: on_round_start：`SSeconomy.pack_price_modifier *= 0.8`（补给箱价格 -20%）
</details>


<details open>
<summary><b>#7</b> Filled up maintenance <small>(`)</small> · <b>正</b> · 权重 5 · 花费 0.5（LOW）</summary>

- **类型**: 正 | **权重**: 5 | **花费**: 0.5（LOW）
- **报告**: ✅<br>"Our workers accidentally forgot more of their personal belongings in the maintenance areas."（工人们把更多私人物品落在维护区域）
- **trait_to_give**: `STATION_TRAIT_FILLED_MAINT`
- **黑名单**: `empty_maint`（空维护通道）
- **效果**: `can_revert=FALSE`（管理员不可回退，因战利品生成时已初始化）
</details>


<details open>
<summary><b>#8</b> Quick Shuttle <small>(`)</small> · <b>正</b> · 权重 5 · 花费 1</summary>

- **类型**: 正 | **权重**: 5 | **花费**: 1
- **报告**: ✅<br>"Due to proximity to our supply station, the cargo shuttle will have a quicker flight time to your cargo department."（因靠近补给站，货舱穿梭艇飞行时间更短）
- **trait_to_give**: 无
- **黑名单**: `slow_shuttle`（慢速穿梭艇）
- **效果**: on_round_start：`SSshuttle.supply.callTime *= 0.5`（补给艇往返时间减半）
</details>


<details open>
<summary><b>#9</b> deathrattled department <small>(`)</small> · <b>正</b> · 权重 10（默认，抽象不参与抽取） · 花费 1</summary>

- **类型**: 正 | **权重**: 10（默认，抽象不参与抽取） | **花费**: 1
- **报告**: ✅（report_message 在 New() 中按部门生成）
- **trait_to_give**: 无
- **黑名单**: `deathrattle_all` + 自动加入所有同类部门子类（除自身）
- **效果**: `abstract_type`；New()：创建部门死亡响铃组；`COMSIG_GLOB_JOB_AFTER_SPAWN` 后给该部门每位成员植入死亡响铃植入体（成员死亡时互相通知）
</details>


<details open>
<summary><b>#10</b> Deathrattled Service <small>(`)</small> · <b>正</b> · 权重 1 · 花费 1</summary>

- **类型**: 正 | **权重**: 1 | **花费**: 1
- **报告**: ✅ "All members of Service have received an implant to notify each other if one of them dies. This should help improve job-safety!"
- **trait_to_give**: 无
- **黑名单**: （继承父类）
- **效果**: 服务部门全员死亡响铃植入体；department_to_apply_to=DEPARTMENT_BITFLAG_SERVICE
</details>


<details open>
<summary><b>#11</b> Deathrattled Cargo <small>(`)</small> · <b>正</b> · 权重 1 · 花费 1</summary>

- **类型**: 正 | **权重**: 1 | **花费**: 1
- **报告**: ✅ 同上（Cargo）
- **trait_to_give**: 无
- **黑名单**: （继承父类）
- **效果**: 货舱部门；DEPARTMENT_BITFLAG_CARGO
</details>


<details open>
<summary><b>#12</b> Deathrattled Engineering <small>(`)</small> · <b>正</b> · 权重 1 · 花费 1</summary>

- **类型**: 正 | **权重**: 1 | **花费**: 1
- **报告**: ✅ 同上（Engineering）
- **trait_to_give**: 无
- **黑名单**: （继承父类）
- **效果**: 工程部门；DEPARTMENT_BITFLAG_ENGINEERING
</details>


<details open>
<summary><b>#13</b> Deathrattled Command <small>(`)</small> · <b>正</b> · 权重 1 · 花费 1</summary>

- **类型**: 正 | **权重**: 1 | **花费**: 1
- **报告**: ✅ 同上（Command）
- **trait_to_give**: 无
- **黑名单**: （继承父类）
- **效果**: 指挥部门；DEPARTMENT_BITFLAG_COMMAND
</details>


<details open>
<summary><b>#14</b> Deathrattled Science <small>(`)</small> · <b>正</b> · 权重 1 · 花费 1</summary>

- **类型**: 正 | **权重**: 1 | **花费**: 1
- **报告**: ✅ 同上（Science）
- **trait_to_give**: 无
- **黑名单**: （继承父类）
- **效果**: 科研部门；DEPARTMENT_BITFLAG_SCIENCE
</details>


<details open>
<summary><b>#15</b> Deathrattled Security <small>(`)</small> · <b>正</b> · 权重 1 · 花费 1</summary>

- **类型**: 正 | **权重**: 1 | **花费**: 1
- **报告**: ✅ 同上（Security）
- **trait_to_give**: 无
- **黑名单**: （继承父类）
- **效果**: 安保部门；DEPARTMENT_BITFLAG_SECURITY
</details>


<details open>
<summary><b>#16</b> Deathrattled Medical <small>(`)</small> · <b>正</b> · 权重 1 · 花费 1</summary>

- **类型**: 正 | **权重**: 1 | **花费**: 1
- **报告**: ✅ 同上（Medical）
- **trait_to_give**: 无
- **黑名单**: （继承父类）
- **效果**: 医疗部门；DEPARTMENT_BITFLAG_MEDICAL
</details>


<details open>
<summary><b>#17</b> Deathrattled Station <small>(`)</small> · <b>正</b> · 权重 1 · 花费 1</summary>

- **类型**: 正 | **权重**: 1 | **花费**: 1
- **报告**: ✅<br>"All members of the station have received an implant to notify each other if one of them dies. This should help improve job-safety!"（全站成员植入死亡通知植入体）
- **trait_to_give**: 无
- **黑名单**: 所有 `deathrattle_department` 子类
- **效果**: New()：创建全站死亡响铃组；全员（含后加入者）植入死亡响铃植入体
</details>


<details open>
<summary><b>#18</b> Cybernetic Revolution <small>(`)</small> · <b>正</b> · 权重 1（**Nova 禁用：weight=0，改名 "Cybernetic Revolution (DISABLED)"，New() 仅记日志**） · 花费 1</summary>

- **类型**: 正 | **权重**: 1（**Nova 禁用：weight=0，改名 "Cybernetic Revolution (DISABLED)"，New() 仅记日志**） | **花费**: 1
- **报告**: ✅<br>"The new trends in cybernetics have come to the station! Everyone has some form of cybernetic implant."（赛博新风潮降临，每人都有某种赛博植入体）
- **trait_to_give**: `STATION_TRAIT_CYBERNETIC_REVOLUTION`
- **黑名单**: 无
- **效果**: 出生后按职业发放专属赛博器官（35+ 职业映射表，含 Nova 模块化职业 virologist/blueshield/nanotrasen_consultant/barber 等）；`body_purist` 怪癖玩家跳过；AI 获得监控升级（eyeobj.relay_speech=TRUE）。**Nova 分支实际禁用**
</details>


<details open>
<summary><b>#19</b> Luxury Escape Pods <small>(`)</small> · <b>正</b> · 权重 5 · 花费 1</summary>

- **类型**: 正 | **权重**: 5 | **花费**: 1
- **报告**: ✅<br>"Due to good performance, we've provided your station with luxury escape pods."（因表现优秀，配备豪华逃生舱）
- **trait_to_give**: `STATION_TRAIT_BIGGER_PODS`
- **黑名单**: `cramped_escape_pods`（拥挤逃生舱）
- **效果**: 纯 trait_to_give
</details>


<details open>
<summary><b>#20</b> Advanced Medbots <small>(`)</small> · <b>正</b> · 权重 5 · 花费 0.5（LOW）</summary>

- **类型**: 正 | **权重**: 5 | **花费**: 0.5（LOW）
- **报告**: ✅<br>"Your station's medibots have received a hardware upgrade, enabling expanded healing capabilities."（医疗机器人硬件升级，治疗能力增强）
- **trait_to_give**: `STATION_TRAIT_MEDBOT_MANIA`
- **黑名单**: 无
- **效果**: 纯 trait_to_give
</details>


<details open>
<summary><b>#21</b> Loaner Shuttle <small>(`)</small> · <b>正</b> · 权重 4 · 花费 1</summary>

- **类型**: 正 | **权重**: 4 | **花费**: 1
- **报告**: ✅（继承父类 show_in_report=TRUE）<br>"Due to an uptick in pirate attacks around your sector, there are few supply vessels in nearby space willing to assist with special requests. Expect to receive more shuttle loan opportunities, with slightly higher payouts."（海盗袭击增多，穿梭艇借贷机会更多且报酬略高）
- **trait_to_give**: `STATION_TRAIT_LOANER_SHUTTLE`
- **黑名单**: 无
- **效果**: 父类为抽象 `random_event_weight_modifier`（见 negative_traits.dm:296）；on_round_start：`shuttle_loan` 事件权重 ×2.5、最大发生次数 +5
</details>


<details open>
<summary><b>#22</b> Wise Cow Invasion <small>(`)</small> · <b>正</b> · 权重 1 · 花费 1</summary>

- **类型**: 正 | **权重**: 1 | **花费**: 1
- **报告**: ✅（继承父类 show_in_report=TRUE）<br>"Bluespace harmonic readings show unusual interpolative signals between your sector and agricultural sector MMF-D-02. Expect an increase in cow encounters. Encownters, if you will."（蓝空间谐波读数异常，奶牛遭遇增加）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: `wisdomcow` 事件权重 ×3、最大发生次数 +10；`get_pulsar_message()` 自定义星云报告："Cow Planet" 级别
</details>


<details open>
<summary><b>#23</b> Bright Day <small>(`)</small> · <b>正</b> · 权重 5 · 花费 1</summary>

- **类型**: 正 | **权重**: 5 | **花费**: 1
- **报告**: ✅<br>"The stars shine bright and the clouds are scarcer than usual. It's a bright day here on the Ice Moon's surface."（冰月表面星光灿烂、云层稀少）
- **trait_to_give**: `STATION_TRAIT_BRIGHT_DAY`
- **黑名单**: 无
- **效果**: **`trait_flags = STATION_TRAIT_PLANETARY`（仅行星站）**；纯 trait_to_give
</details>


<details open>
<summary><b>#24</b> Shuttle Firesale <small>(`)</small> · <b>正</b> · 权重 4 · 花费 1</summary>

- **类型**: 正 | **权重**: 4 | **花费**: 1
- **报告**: ✅<br>"The Nanotrasen Emergency Dispatch team is celebrating a record number of shuttle calls in the recent quarter. Some of your emergency shuttle options have been discounted!"（紧急调度创纪录，部分紧急穿梭艇选项打折）
- **trait_to_give**: `STATION_TRAIT_SHUTTLE_SALE`
- **黑名单**: 无
- **效果**: 纯 trait_to_give
</details>


<details open>
<summary><b>#25</b> Misplaced Wallet <small>(`)</small> · <b>正</b> · 权重 5 · 花费 0.5（LOW）</summary>

- **类型**: 正 | **权重**: 5 | **花费**: 0.5（LOW）
- **报告**: ✅<br>"A repair technician left their wallet in a locker somewhere. They would greatly appreciate if you could locate and return it to them when the shift has ended."（维修技工把钱包落在某储物柜里）35% 概率追加位置线索到报告
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: on_round_start：随机储物柜生成钱包（500 信用点，25% 额外 1000 点）+ 维修技工 ID（外部气闸+维护通道权限，ID 与 trim 定义于同文件）；通知管理员位置
</details>


<details open>
<summary><b>#26</b> Geared Assistants Pilot <small>(`)</small> · <b>正</b> · 权重 3 · 花费 1</summary>

- **类型**: 正 | **权重**: 3 | **花费**: 1
- **报告**: ✅<br>"The Nanotrassen Assistant Affairs division is performing a pilot to see if different assistant equipment helps improve productivity!"（助手事务部试点不同助手装备）
- **trait_to_give**: `STATION_TRAIT_ASSISTANT_GIMMICKS`
- **黑名单**: `colored_assistants`（彩色助手）
- **效果**: 纯 trait_to_give；**代码缺陷：`get_pulsar_message()` 定义在错误路径 `/datum/station_trait/random_event_weight_modifier/assistant_gimmicks` 上（死代码，不会生效）**
</details>




### 中性特质 Neutral（18 个）

<details open>
<summary><b>#1</b> Bananium Shipment <small>(`)</small> · <b>中</b> · 权重 5 · 花费 0.5（LOW）</summary>

- **类型**: 中 | **权重**: 5 | **花费**: 0.5（LOW）
- **报告**: ❌（未设，默认 FALSE）<br>"Rumors has it that the clown planet has been sending support packages to clowns in this system."（传闻小丑星球向本星系小丑寄送支援包）
- **trait_to_give**: `STATION_TRAIT_BANANIUM_SHIPMENTS`
- **黑名单**: 无
- **效果**: 给小丑（及深空/熔岩地所有小丑）5 片香蕉矿板；`get_pulsar_message()` 自定义 "Clown Planet" 星云报告
</details>


<details open>
<summary><b>#2</b> Unnatural atmospherical properties <small>(`)</small> · <b>中</b> · 权重 5 · 花费 0.5（LOW）</summary>

- **类型**: 中 | **权重**: 5 | **花费**: 0.5（LOW）
- **报告**: ✅<br>"System's local planet has irregular atmospherical properties."（本系统行星大气性质异常）
- **trait_to_give**: `STATION_TRAIT_UNNATURAL_ATMOSPHERE`
- **黑名单**: 无
- **效果**: `can_revert=FALSE`（大气在开局前已修改，管理员不可回退）
</details>


<details open>
<summary><b>#3</b> Spider Infestation <small>(`)</small> · <b>中</b> · 权重 5 · 花费 1</summary>

- **类型**: 中 | **权重**: 5 | **花费**: 1
- **报告**: ❌（未设，默认 FALSE）<br>"We have introduced a natural countermeasure to reduce the number of rodents on board your station."（引入自然天敌来减少站内啮齿动物）
- **trait_to_give**: `STATION_TRAIT_SPIDER_INFESTATION`
- **黑名单**: 无
- **效果**: 纯 trait_to_give（实际是蜘蛛，文案用"天敌"委婉说法）
</details>


<details open>
<summary><b>#4</b> Unique AI <small>(`)</small> · <b>中</b> · 权重 5 · 花费 1</summary>

- **类型**: 中 | **权重**: 5 | **花费**: 1
- **报告**: ✅<br>"For experimental purposes, this station AI might show divergence from default lawset. Do not meddle with this experiment, we've removed access to your set of alternative upload modules because we know you're already thinking about meddling with this experiment."（实验性 AI 可能偏离默认法律模块，已移除备用上传模块）
- **trait_to_give**: `STATION_TRAIT_UNIQUE_AI`
- **黑名单**: 无
- **效果**: **`trait_flags = 父类标志 \
</details>


<details open>
<summary><b>#5</b> Ian's Adventure <small>(`)</small> · <b>中</b> · 权重 5 · 花费 0.5（LOW）</summary>

- **类型**: 中 | **权重**: 5 | **花费**: 0.5（LOW）
- **报告**: ❌（show_in_report=FALSE）<br>"Ian has gone exploring somewhere in the station."（伊恩去站内某处探险了）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: on_round_start：伊恩获得死chat 众控模式（民主投票，3 秒间隔）+ 2 条额外生命，被传送到安全地点（烟幕特效）；重生保留装备/帽子并继续传送
</details>


<details open>
<summary><b>#6</b> PDA glitch <small>(`)</small> · <b>中</b> · 权重 5 · 花费 0.3（MINIMAL）</summary>

- **类型**: 中 | **权重**: 5 | **花费**: 0.3（MINIMAL）
- **报告**: ✅<br>"Something seems to be wrong with the PDAs issued to you all this shift. Nothing too bad though."（本班 PDA 似乎有些问题，但不严重）
- **trait_to_give**: `STATION_TRAIT_PDA_GLITCHED`
- **黑名单**: 无
- **效果**: 纯 trait_to_give
</details>


<details open>
<summary><b>#7</b> Announcement Intern <small>(`)</small> · <b>中</b> · 权重 1 · 花费 1</summary>

- **类型**: 中 | **权重**: 1 | **花费**: 1
- **报告**: ✅<br>"Please be nice to him."（请对他好一点）
- **trait_to_give**: 无
- **黑名单**: `announcement_medbot`、`birthday`
- **效果**: New()：`SSstation.announcer = /datum/centcom_announcer/intern`（中央指挥部播报员换成实习生，播报自带口误效果）；`get_pulsar_message()` 为占位符 "(TITLE HERE)"
</details>


<details open>
<summary><b>#8</b> Announcement \ <small>(`)</small> · <b>中</b> · 权重 1 · 花费 1</summary>

- **类型**: 中 | **权重**: 1 | **花费**: 1
- **报告**: ✅<br>"Our announcement system is under scheduled maintanance at the moment. Thankfully, we have a backup."（播报系统例行维护中，幸好我们有备用）
- **trait_to_give**: 无
- **黑名单**: `announcement_intern`、`birthday`
- **效果**: New()：`SSstation.announcer = /datum/centcom_announcer/medbot`（播报员换成医疗机器人）
</details>


<details open>
<summary><b>#9</b> Colored Assistants <small>(`)</small> · <b>中</b> · 权重 10 · 花费 0.3（MINIMAL）</summary>

- **类型**: 中 | **权重**: 10 | **花费**: 0.3（MINIMAL）
- **报告**: ✅<br>"Due to a shortage in standard issue jumpsuits, we have provided your assistants with one of our backup supplies."（标准制服短缺，助手改穿备用制服）
- **trait_to_give**: 无
- **黑名单**: `assistant_gimmicks`（装备助手试点）
- **效果**: New()：随机挑选一个非当前配置的 `colored_assistant` 子类型设为全站助手外观
</details>


<details open>
<summary><b>#10</b> Employee Birthday <small>(`)</small> · <b>中</b> · 权重 2（**Nova 改为 3**） · 花费 1</summary>

- **类型**: 中 | **权重**: 2（**Nova 改为 3**） | **花费**: 1
- **报告**: ✅<br>"We here at Nanotrasen would all like to wish Employee Name a very happy birthday"（全体祝贺员工生日）
- **trait_to_give**: `STATION_TRAIT_BIRTHDAY`
- **黑名单**: `announcement_intern`、`announcement_medbot`（覆盖播报员会隐藏寿星信息）
- **效果**: on_round_start：随机选一名乘员为寿星（TRAIT_BIRTHDAY_BOY，支持 admin 指定 ckey，Nova 增加 opt-out 偏好），10 秒后播报 + 派对喇叭 + 心情加成；全员出生发派对帽/玩具，后加入者得生日邀请卡；`revert()` 清理派对垃圾
</details>


<details open>
<summary><b>#11</b> Scryers <small>(`)</small> · <b>中</b> · 权重 2 · 花费 0.5（LOW）</summary>

- **类型**: 中 | **权重**: 2 | **花费**: 0.5（LOW）
- **报告**: ✅<br>"Nanotrasen has chosen your station for an experiment - everyone has free scryers! Use these to talk to other people easily and privately."（实验项目：全员免费千里眼，可私密通话）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: 出生后每个人类获得链接千里眼项链（link_scryer，刻名），原有围巾/领带挪到口袋/背包
</details>


<details open>
<summary><b>#12</b> Wallets! <small>(`)</small> · <b>中</b> · 权重 5 · 花费 0.3（MINIMAL）</summary>

- **类型**: 中 | **权重**: 5 | **花费**: 0.3（MINIMAL）
- **报告**: ✅<br>"It has become temporarily fashionable to use a wallet, so everyone on the station has been issued one."（全员配发钱包，暂时流行）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: 出生后把 ID 卡装入钱包，账户余额转为钱包内全息币，钱包内随机娱乐内容生成器，所有物品印指纹
</details>


<details open>
<summary><b>#13</b> Forested <small>(`)</small> · <b>中</b> · 权重 10 · 花费 1</summary>

- **类型**: 中 | **权重**: 10 | **花费**: 1
- **报告**: ✅<br>"There sure are a lot of trees out there."（外面树真多）
- **trait_to_give**: `STATION_TRAIT_FORESTED`
- **黑名单**: 无
- **效果**: **`trait_flags = STATION_TRAIT_PLANETARY`（仅行星站）**；让地图生成器增加树木
</details>


<details open>
<summary><b>#14</b> Closet Anomaly <small>(`)</small> · <b>中</b> · 权重 1 · 花费 1</summary>

- **类型**: 中 | **权重**: 1 | **花费**: 1
- **报告**: ✅<br>"We've reports of loose bluespace streams affecting your station's lockers and closets. You might lose some of your belongings... or gain some new ones!"（蓝空间流影响储物柜，物品可能丢失或增加）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: on_round_start：约 4–4.3%（220 个柜约 22 个）的开局储物柜按 2–3 个一组通过 closet_teleport_controller 建立传送链接（内容互换）
</details>


<details open>
<summary><b>#15</b> The Great Skub Contention <small>(`)</small> · <b>中</b> · 权重 2（**Nova 改为 0，禁用**） · 花费 1</summary>

- **类型**: 中 | **权重**: 2（**Nova 改为 0，禁用**） | **花费**: 1
- **报告**: ❌（show_in_report=FALSE）<br>（无报告文案）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: `sign_up_button=TRUE`：大厅报名按钮循环选择 pro-skub / anti-skub / 中立 / 随机；出生时发对应贴纸盒 + 旧衬衫（pro 版/anti 版）；on_round_start 保留按钮供大厅玩家继续选择
</details>


<details open>
<summary><b>#16</b> Station-Wide Background Checks <small>(`)</small> · <b>中</b> · 权重 1 · 花费 1</summary>

- **类型**: 中 | **权重**: 1 | **花费**: 1
- **报告**: ✅<br>"We replaced the intern doing your crew's background checks with a trained screener for this shift! That said, our enemies may just find another way to infiltrate the station, so be careful."（本班改用专业审查员做背景审查）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: `can_revert=FALSE`；`dynamic_threat_id="Background Checks"`；New()：开局前把所有非"入侵者"类动态规则集权重清零（船员不会出生为敌对阵营；obsessed/孢体感染/太空变形虫等仍可能）
</details>


<details open>
<summary><b>#17</b> Bring Your Pet To Work Day <small>(`)</small> · <b>中</b> · 权重 2 · 花费 1</summary>

- **类型**: 中 | **权重**: 2 | **花费**: 1
- **报告**: ❌（show_in_report=FALSE）<br>（无报告文案）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: `sign_up_button=TRUE`：大厅按钮打开宠物自定义界面（GLOB.customized_pets）；出生时生成自定义宠物
</details>


<details open>
<summary><b>#18</b> GMM Economic Spotlight <small>(`)</small> · <b>中</b> · 权重 2 · 花费 0.5（LOW）</summary>

- **类型**: 中 | **权重**: 2 | **花费**: 0.5（LOW）
- **报告**: ✅<br>"This shift, the Galactic Mineral Market is doing a showcase on your crew's affulence! Every paycheck, the station newscasters will alert the crew who has the most credits."（银河矿物市场展示船员财富，每次发薪播报最富有者）
- **trait_to_give**: `STATION_TRAIT_ECONOMY_ALERTS`
- **黑名单**: 无
- **效果**: `dynamic_threat_id="GMM Econ Spotlight"`；纯 trait_to_give
</details>




### 负面特质 Negative（26 个）

<details open>
<summary><b>#1</b> Carp infestation <small>(`)</small> · <b>负</b> · 权重 5 · 花费 1（默认）</summary>

- **类型**: 负 | **权重**: 5 | **花费**: 1（默认）
- **报告**: ✅<br>"Dangerous fauna is present in the area of this station."（本站区域存在危险动物群）
- **trait_to_give**: `STATION_TRAIT_CARP_INFESTATION`
- **黑名单**: 无
- **效果**: 纯 trait_to_give（开局生成大量太空鲤鱼）
</details>


<details open>
<summary><b>#2</b> Distant supply lines <small>(`)</small> · <b>负</b> · 权重 3 · 花费 1</summary>

- **类型**: 负 | **权重**: 3 | **花费**: 1
- **报告**: ✅<br>"Due to the distance to our normal supply lines, cargo orders are more expensive."（距常规补给线太远，货舱订单更贵）
- **trait_to_give**: 无
- **黑名单**: `strong_supply_lines`（强大补给线）
- **效果**: on_round_start：`SSeconomy.pack_price_modifier *= 1.2`（补给箱价格 +20%）
</details>


<details open>
<summary><b>#3</b> Postal workers strike <small>(`)</small> · <b>负</b> · 权重 2 · 花费 1</summary>

- **类型**: 负 | **权重**: 2 | **花费**: 1
- **报告**: ✅<br>"Due to an ongoing strike announced by the postal workers union, mail won't be delivered this shift."（邮政工会罢工，本班不送信）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: on_round_start：若 `SSeconomy.mail_blocked` 已为真（节假日/周日）则**反转**为 "Postal system overtime"（邮政加班送信，改 name+report_message）；否则把 `mail_strike` 穿梭艇借贷情境从不可用列表中移除并翻转 mail_blocked；`revert()` 恢复
</details>


<details open>
<summary><b>#4</b> Late Arrivals <small>(`)</small> · <b>负</b> · 权重 2 · 花费 1</summary>

- **类型**: 负 | **权重**: 2 | **花费**: 1
- **报告**: ✅<br>"Sorry for that, we didn't expect to fly into that vomiting goose while bringing you to your new station."（抱歉，送你们来的时候撞上了一只会呕吐的鹅）
- **trait_to_give**: `STATION_TRAIT_LATE_ARRIVALS`
- **黑名单**: `random_spawns`、`hangover`
- **效果**: 纯 trait_to_give（迟到者空投舱晚到）
</details>


<details open>
<summary><b>#5</b> Drive-by landing <small>(`)</small> · <b>负</b> · 权重 2 · 花费 1</summary>

- **类型**: 负 | **权重**: 2 | **花费**: 1
- **报告**: ✅<br>"Sorry for that, we missed your station by a few miles, so we just launched you towards your station in pods. Hope you don't mind!"（错过站点几英里，直接把你塞进逃生舱射过去）
- **trait_to_give**: `STATION_TRAIT_RANDOM_ARRIVALS`
- **黑名单**: `late_arrivals`、`hangover`
- **效果**: 纯 trait_to_give（出生点随机散布全站）
</details>


<details open>
<summary><b>#6</b> Hangover <small>(`)</small> · <b>负</b> · 权重 2 · 花费 1</summary>

- **类型**: 负 | **权重**: 2 | **花费**: 1
- **报告**: ✅<br>"Ohh....Man....That mandatory office party from last shift...God that was awesome..I woke up in some random toilet 3 sectors away..."（上次的强制办公室派对太棒了，我在 3 个星区外的随机厕所醒来）
- **trait_to_give**: `STATION_TRAIT_HANGOVER`
- **黑名单**: `late_arrivals`、`random_spawns`
- **效果**: New() 注册 `COMSIG_GLOB_JOB_AFTER_LATEJOIN_SPAWN`；**35% 概率给迟到加入者随机戴一顶搞笑帽子**（绿色阔边帽/软呢帽/头套/ushanka/纸箱头/海盗帽/路障锥）；`revert()` 清理宿醉遗留物（landmark 里的 debris）
</details>


<details open>
<summary><b>#7</b> Blackout <small>(`)</small> · <b>负</b> · 权重 3 · 花费 1</summary>

- **类型**: 负 | **权重**: 3 | **花费**: 1
- **报告**: ✅<br>"Station lights seem to be damaged, be safe when starting your shift today."（站内灯光似乎损坏，开工小心）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: on_round_start：遍历全站所有 APC，**60% 概率 `overload_lighting()`**（烧毁灯光）
</details>


<details open>
<summary><b>#8</b> Cleaned out maintenance <small>(`)</small> · <b>负</b> · 权重 5 · 花费 0.5（LOW）</summary>

- **类型**: 负 | **权重**: 5 | **花费**: 0.5（LOW）
- **报告**: ✅<br>"Our workers cleaned out most of the junk in the maintenance areas."（工人们清掉了维护区大部分垃圾）
- **trait_to_give**: `STATION_TRAIT_EMPTY_MAINT`
- **黑名单**: `filled_maint`（塞满的维护通道）
- **效果**: **`can_revert=FALSE`**（战利品在开局前已初始化，管理员无法回退）；纯 trait_to_give
</details>


<details open>
<summary><b>#9</b> Overflow bureaucracy mistake <small>(`)</small> · <b>负</b> · 权重 5（**Nova：weight=0**） · 花费 1</summary>

- **类型**: 负 | **权重**: 5（**Nova：weight=0**） | **花费**: 1
- **报告**: ✅（动态 get_report()）<br>"[name] - It seems for some reason we put out the wrong job-listing for the overflow role this shift...I hope you like [chosen_job_name]s."（本班溢出职业挂错了招聘信息……希望你喜欢 XX 们）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: New() 注册 `COMSIG_SUBSYSTEM_POST_INITIALIZE`；开局随机挑一个合法溢出职业设为溢出角色（chosen_job_name 转小写写入报告）。**Nova 覆写：weight=0，且改为从 `SSjob.joinable_occupations` 挑选并跳过 `nova_stars_only` 职业**
</details>


<details open>
<summary><b>#10</b> Slow Shuttle <small>(`)</small> · <b>负</b> · 权重 5 · 花费 1</summary>

- **类型**: 负 | **权重**: 5 | **花费**: 1
- **报告**: ✅<br>"Due to distance to our supply station, the cargo shuttle will have a slower flight time to your cargo department."（距补给站远，货舱穿梭艇飞行更慢）
- **trait_to_give**: 无
- **黑名单**: `quick_shuttle`（快速穿梭艇）
- **效果**: New() 注册 `COMSIG_SUBSYSTEM_POST_INITIALIZE`；`SSshuttle.supply.callTime *= 1.5`（补给艇往返时间 +50%）
</details>


<details open>
<summary><b>#11</b> Bot Language Matrix Malfunction <small>(`)</small> · <b>负</b> · 权重 4 · 花费 0.5（LOW）</summary>

- **类型**: 负 | **权重**: 4 | **花费**: 0.5（LOW）
- **报告**: ✅（动态报告）<br>"Your station's friendly bots have had their language matrix fried due to [随机事件源], resulting in some strange and unfamiliar speech patterns."（友好机器人语言矩阵被烤坏，说话变得奇怪）
- **trait_to_give**: `STATION_TRAIT_BOTS_GLITCHED`
- **黑名单**: 无
- **效果**: New() 随机事件源：离子风暴/辛迪加黑客/故障/船上 AI 问题/实习生失误/预算削减；on_round_start：所有在站（或逃生艇上）机器人 `randomize_language_if_on_station()`（语言随机化）
</details>


<details open>
<summary><b>#12</b> Machine Language Matrix Malfunction <small>(`)</small> · <b>负</b> · 权重 2 · 花费 1（FULL）</summary>

- **类型**: 负 | **权重**: 2 | **花费**: 1（FULL）
- **报告**: ✅（动态报告）<br>"Your station's machinery have had their language matrix fried due to [随机事件源], resulting in some strange and unfamiliar speech patterns."（站内机器的语言矩阵被烤坏）
- **trait_to_give**: `STATION_TRAIT_MACHINES_GLITCHED`
- **黑名单**: 无
- **效果**: New() 随机事件源：离子风暴/故障/软件更新/电涌/电脑病毒/被镇压的机器起义/小丑恶作剧（比 bot 版更全）
</details>


<details open>
<summary><b>#13</b> Revenge of Pun Pun <small>(`)</small> · <b>负</b> · 权重 2 · 花费 0.5（LOW）</summary>

- **类型**: 负 | **权重**: 2 | **花费**: 0.5（LOW）
- **报告**: ❌（未设，默认 FALSE）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: **`can_revert=FALSE`**；New() 构建带权重武器表（椅子20/尾巴棍10/棒球棒10/尾鞭10/猫咪尾鞭10/酒瓶20/孔酒瓶5/伸缩弹簧刀10/随机告示牌10/**手枪1**）并注册 `COMSIG_SUBSYSTEM_POST_INITIALIZE`；`arm_monke()`：给潘潘发武器（手满了就删武器用手中的）、人和武器涂血、删除 AI 控制器换愤怒猴子 AI、传送到所在区域随机空地、生成 10–40 摊血迹 + 最长 30 步×10 次尝试的血迹路径（80% 血迹/50% 喷溅或内脏），终点放一具血迹躯干（gibs/torso）
</details>


<details open>
<summary><b>#14</b> Random Event Modifier <small>(`)</small> · <b>负（抽象）</b> · 权重 0 · 花费 1</summary>

- **类型**: 负（抽象） | **权重**: 0 | **花费**: 1
- **报告**: ✅<br>"A random event has been modified this shift! Someone forgot to set this!"（本班某随机事件被修改了！有人忘了设置！）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: `abstract_type`；变量：`event_control_path`（要修改的事件控制路径）、`weight_multiplier=1`、`max_occurrences_modifier=0`；on_round_start：找到事件控制器后 `weight *= 乘数`、`max_occurrences += 修正值`；找不到则 CRASH。**批 1 的 shuttle_loans/wise_cows 正面特质也继承此类**
</details>


<details open>
<summary><b>#15</b> Ionic Stormfront <small>(`)</small> · <b>负</b> · 权重 3 · 花费 1</summary>

- **类型**: 负 | **权重**: 3 | **花费**: 1
- **报告**: ✅（继承父类）<br>"An ionic stormfront is passing over your station's system. Expect an increased likelihood of ion storms afflicting your station's silicon units."（离子风暴前锋过境，硅基单位遭离子风暴概率上升）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: `event_control_path=/datum/round_event_control/ion_storm`、`weight_multiplier=2`；自定义 `get_pulsar_message()`：Advisory Level **ERROR** + 乱码混淆的官方公告（35% 字符替换）
</details>


<details open>
<summary><b>#16</b> Radiation Stormfront <small>(`)</small> · <b>负</b> · 权重 2（**Nova：weight=0**） · 花费 1</summary>

- **类型**: 负 | **权重**: 2（**Nova：weight=0**） | **花费**: 1
- **报告**: ✅（继承父类）<br>"A radioactive stormfront is passing through your station's system. Expect an increased likelihood of radiation storms passing over your station, as well the potential for multiple radiation storms to occur during your shift."（放射性风暴前锋过境，辐射风暴概率上升且可能多发）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: `event_control_path=/datum/round_event_control/radiation_storm`、`weight_multiplier=1.5`、`max_occurrences_modifier=2`（**Nova 覆写：weight=0 且 max_occurrences_modifier=0，完全禁用**）
</details>


<details open>
<summary><b>#17</b> Dust Stormfront <small>(`)</small> · <b>负</b> · 权重 2 · 花费 0.5（LOW）</summary>

- **类型**: 负 | **权重**: 2 | **花费**: 0.5（LOW）
- **报告**: ✅（继承父类）<br>"The space around your station is clouded by heavy pockets of space dust. Expect an increased likelihood of space dust storms damaging the station hull."（站周空间布满浓密太空尘埃，船体受尘埃风暴损伤概率上升）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: `event_control_path=/datum/round_event_control/meteor_wave/dust_storm`、`weight_multiplier=2`、`max_occurrences_modifier=3`
</details>


<details open>
<summary><b>#18</b> Cramped Escape Pods <small>(`)</small> · <b>负</b> · 权重 5 · 花费 1</summary>

- **类型**: 负 | **权重**: 5 | **花费**: 1
- **报告**: ✅<br>"Due to budget cuts, we have downsized your escape pods."（因预算削减，逃生舱缩水了）
- **trait_to_give**: `STATION_TRAIT_SMALLER_PODS`
- **黑名单**: `luxury_escape_pods`（豪华逃生舱）
- **效果**: 纯 trait_to_give
</details>


<details open>
<summary><b>#19</b> Post-Revolutionary Fervor <small>(`)</small> · <b>负</b> · 权重 2 · 花费 1</summary>

- **类型**: 负 | **权重**: 2 | **花费**: 1
- **报告**: ✅<br>"Your station was recently reclaimed from a revolutionary commune. We couldn't clean up after them in time."（本站刚从革命公社手中夺回，没来得及打扫）
- **trait_to_give**: `STATION_TRAIT_REVOLUTIONARY_TRASHING`
- **黑名单**: 无
- **效果**: on_round_start 异步 `trash_this_place()`：对全部指挥区（command）地皮——25% 随机蜡笔涂鸦（21 种设计 ID：amyjon/antilizard/body/cyka/danger/electricdanger/face/guy/matt/peace/prolizard/radiation/revolution/shotgun/skull/splatter/star/stickman/toilet/toolbox/uboa）、0.01% 生成助手尸体、40% 打碎灯管、15% 窗户受损 30–90、40% 拆桌、60% 拆椅、30% 电脑受损 160（**跳过通信电脑防开局自动叫穿梭艇**）、45% 售货机（50% 倾斜/50% 受损 150）、消防斧柜受损 90、船长床单换成革命床单；CHECK_TICK 防卡
</details>


<details open>
<summary><b>#20</b> Nebula <small>(`)</small> · <b>负（抽象）</b> · 权重 0 · 花费 1</summary>

- **类型**: 负（抽象） | **权重**: 0 | **花费**: 1
- **报告**: ✅
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: `abstract_type`；变量：`nebula_layer`（默认随机太空气体视差层）、`carp_color_override`；New()：`SSparallax.swap_out_random_parallax_layer(nebula_layer)` 换掉一层背景视差；若有颜色覆写则改 `GLOB.carp_colors`（鲤鱼配色融入星云）
</details>


<details open>
<summary><b>#21</b> /datum/station_trait/nebula/hostilebr（抽象父类） <small>(`)</small> · <b>负（抽象）</b> · 权重 0 · 花费 1</summary>

- **类型**: 负（抽象） | **权重**: 0 | **花费**: 1
- **报告**: ✅（继承）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: `abstract_type`；`trait_processes=TRUE`（每 tick 处理）；强度体系：`nebula_intensity`（私有，按 `STATION_TIME_PASSED()/intensity_increment_time` 计算，上限 `maximum_nebula_intensity=2 小时`）、`intensity_increment_time=30 分钟`；护盾系统 `shielding` 列表 + `add_shielder/remove_shielder`（全局 `/proc/add_to_nebula_shielding` 供护盾机注册）；**process() 含 NOVA EDIT：若 `storms_enabled` 为假则只调 `get_shielding_level()`（让护盾仍产氚）并返回，不施加风暴效果**；on_round_start 30 秒后回调 `send_instructions()`（抽象，子类实现播报）
</details>


<details open>
<summary><b>#22</b> Radioactive Nebula <small>(`)</small> · <b>负</b> · 权重 1 · 花费 1</summary>

- **类型**: 负 | **权重**: 1 | **花费**: 1
- **报告**: ✅<br>"This station is located inside a radioactive nebula. Setting up nebula shielding is top-priority."（本站位于放射性星云内，搭建星云护盾是第一要务）
- **trait_to_give**: `STATION_TRAIT_RADIOACTIVE_NEBULA`
- **黑名单**: `random_event_weight_modifier/rad_storms`
- **效果**: **`trait_flags=STATION_TRAIT_SPACE_BOUND`（仅太空站）**；`dynamic_threat_id="Radioactive Nebula"`；参数：`intensity_increment_time=10 分钟`（**NOVA EDIT：原 5 分钟，延长护盾持续时间**）、`maximum_nebula_intensity=1 小时 40 分`、护理包冷却 5 分钟、放射性区域=/area/space、绿色氚辉光 #66ff33、鲤鱼配色四种绿。on_round_start：开放工程补给包 `rad_nebula_shielding_kit`（ORDER_SPECIAL_ENABLED）；空投护盾套件到工程（失败则舰桥）、modsuit 辐射防护模块到机器人部、给病毒学发传真、**禁用辐射风暴事件（weight=0）**。机制：进/出太空区域触发 `fake_irradiate/fake_unirradiate`；动态刷出的怪获得 `radiation_immunity/radnebula` 状态；护盾不足时启动 `/datum/weather/rad_storm/nebula` 风暴，每 5 分钟空投应急辐射护盾（优先公告+10 秒后空投），护盾足够则平息风暴。send_instructions() 公告**被 NOVA 精简**（原长篇"护盾时限/数量计算"公告删除，仅保留"标准太空服无法防护，强烈建议不要使用"），并把状态屏切到辐射警报。get_decal_color：走廊涂绿
</details>


<details open>
<summary><b>#23</b> /datum/station_trait/stormbr（抽象父类） <small>(`)</small> · <b>负（抽象）</b> · 权重 0 · 花费 1</summary>

- **类型**: 负（抽象） | **权重**: 0 | **花费**: 1
- **报告**: ❌
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: `abstract_type`；变量 `storm_type`（天气类型）；on_round_start：`SSweather.run_weather(storm_type)` 直接启动风暴
</details>


<details open>
<summary><b>#24</b> Forever Storm <small>(`)</small> · <b>负</b> · 权重 3 · 花费 1</summary>

- **类型**: 负 | **权重**: 3 | **花费**: 1
- **报告**: ✅<br>"It looks like the storm is not gonna calm down anytime soon, stay safe out there."（风暴一时半会停不了，注意安全）
- **trait_to_give**: 无
- **黑名单**: 无
- **效果**: **`trait_flags=STATION_TRAIT_PLANETARY`（仅行星站）**；`storm_type=/datum/weather/snow_storm/forever_storm`（无尽暴风雪）；自定义 `get_pulsar_message()`：Advisory Level **Ice Giant** + 暴风雪干扰监控公告
</details>


<details open>
<summary><b>#25</b> Spiked Drinks <small>(`)</small> · <b>负</b> · 权重 3 · 花费 0.5（LOW）</summary>

- **类型**: 负 | **权重**: 3 | **花费**: 0.5（LOW）
- **报告**: ✅<br>"Due to a mishap at the Robust Softdrinks Megafactory, some drinks may contain traces of ethanol or psychoactive chemicals."（Robust 软饮工厂事故，部分饮料含乙醇或精神药物残留）
- **trait_to_give**: `STATION_TRAIT_SPIKED_DRINKS`
- **黑名单**: 无
- **效果**: 纯 trait_to_give（酒吧饮料随机掺酒精/致幻剂）
</details>


<details open>
<summary><b>#26</b> Structural Weaknesses <small>(`)</small> · <b>负</b> · 权重 5 · 花费 1</summary>

- **类型**: 负 | **权重**: 5 | **花费**: 1
- **报告**: ✅<br>"Our station subdivision informed us that this station may have been built with a number of structural weaknesses due to defective construction materials. Be on the lookout for them and try not to let anything explode."（施工材料缺陷导致多处结构弱点，小心别让东西爆炸）
- **trait_to_give**: `STATION_TRAIT_SPAWN_WEAKPOINTS`
- **黑名单**: 无
- **效果**: 纯 trait_to_give（地图生成结构弱点，爆炸更容易破坏船体）
</details>




### 职业特质 Job（6 个）

<details open>
<summary><b>#1</b> /datum/station_trait/jobbr（抽象父类，见上） <small>(`)</small> · <b>职业（抽象）</b> · 权重 - · 花费 1</summary>

- **类型**: 职业（抽象） | **权重**: - | **花费**: 1
- **报告**: ❌
- **trait_to_give**: `/datum/job/clown`（默认）
- **黑名单**: 自动加入全部 job 子类（除自身）
- **效果**: `abstract_type`；大厅报名按钮机制；`button_desc`="报名获得某种不寻常的职业，大多数局没有"
</details>


<details open>
<summary><b>#2</b> Cargo Gorilla <small>(`)</small> · <b>职业</b> · 权重 1 · 花费 1</summary>

- **类型**: 职业 | **权重**: 1 | **花费**: 1
- **报告**: ❌（**show_in_report=FALSE**，注释："选择性注意测试。你看到大猩猩了吗？"）
- **trait_to_give**: `/datum/job/cargo_gorilla`
- **黑名单**: （继承）
- **效果**: 报名按钮图标 gorilla_on/gorilla_off；`replace_cargo()`：**删掉货舱树懒和货舱机甲（ripley）**，由大猩猩顶替搬箱子（若开局没有树懒则取消按钮）；"monkey carries the crates, the age of robot is over"
</details>


<details open>
<summary><b>#3</b> Bridge Assistant <small>(`)</small> · <b>职业</b> · 权重 2（**Nova：weight=0，禁用**） · 花费 1</summary>

- **类型**: 职业 | **权重**: 2（**Nova：weight=0，禁用**） | **花费**: 1
- **报告**: ✅<br>"We have installed a Bridge Assistant on your station."（本站已配备舰桥助理）
- **trait_to_give**: `/datum/job/bridge_assistant`
- **黑名单**: （继承）
- **效果**: `add_coffeemaker()`：在舰桥（或会议室）找空桌子生成**意式咖啡机 impressa + 咖啡壶 + 咖啡包**（已有咖啡机则不重复生成）。**Nova 覆写：weight=0 注释 "Bridge assistant is a permanent job here, so we disable the trait."（舰桥助理在本服已是常驻职业，故禁用该特质）**
</details>


<details open>
<summary><b>#4</b> Veteran Advisor <small>(`)</small> · <b>职业</b> · 权重 2 · 花费 1</summary>

- **类型**: 职业 | **权重**: 2 | **花费**: 1
- **报告**: ✅<br>"Veteran Security Advisor has been assigned to your station to help with Security matters."（安保老兵顾问已派往本站协助安保事务）
- **trait_to_give**: `/datum/job/veteran_advisor`
- **黑名单**: （继承）
- **效果**: `button_desc`="报名成为**残障**但久经沙场的 NT 安保部队老兵顾问：给 HOS 和船长提建议、训练警官，同时对抗你的 PTSD"；报名按钮图标 veteran_advisor
</details>


<details open>
<summary><b>#5</b> Human AI <small>(`)</small> · <b>职业</b> · 权重 1 · 花费 1</summary>

- **类型**: 职业 | **权重**: 1 | **花费**: 1
- **报告**: ✅<br>"Our recent technological advancements in machine Artificial Intelligence has proven futile. In the meantime, we're sending an Intern to help out."（机器 AI 技术被证明无用，改派一名实习生来帮忙）
- **trait_to_give**: `/datum/job/human_ai`
- **黑名单**: （继承）
- **效果**: **`trait_flags = 父类标志 \
</details>


<details open>
<summary><b>#6</b> Pun Pun is a Crewmember <small>(`)</small> · <b>职业</b> · 权重 0（**默认不可roll，猴日全天可用**） · 花费 1</summary>

- **类型**: 职业 | **权重**: 0（**默认不可roll，猴日全天可用**） | **花费**: 1
- **报告**: ✅<br>"We've evaluated the bartender's monkey to have the mental capacity of the average crewmember. As such, we made them one."（评估显示调酒师的猴子有普通船员的心智水平，于是让它当船员）
- **trait_to_give**: `/datum/job/pun_pun`
- **黑名单**: （继承）
- **效果**: New()：若局未开始且潘潘存在，在潘潘位置生成 pun_pun 出生点 landmark 并**删除原潘潘**（防双潘潘）；报名按钮图标 pun_pun_on/off
</details>


---

## 三、NOVA 修改与彩蛋

### 3.1 NOVA 独有/修改

| 项目 | 说明 |
|---|---|
| **storms_enabled** | 放射性星云风暴开关（`toggle_storms()` 管理员可开），默认关——护盾只产氚不刮风暴 |
| **birthday_opt_out** | 角色偏好可退出生日特质 |
| **bridge_assistant 禁用** | weight=0（舰桥助理已成常驻职业） |
| **overflow_job_bureaucracy 禁用** | weight=0（且覆写跳过 nova_stars_only 职业） |
| **rad_storms 禁用** | weight=0 |
| **cybernetic_revolution 禁用** | weight=0、改名 "(DISABLED)" |
| **skub 禁用** | weight=0 |
| **birthday 权重** | 2→3（更常见） |
| **护盾时长** | 放射性星云护盾 5→10 分钟 |

### 3.2 彩蛋

- **潘潘复仇（revenge_of_pun_pun）**：武器表藏 1 权重手枪
- **代码缺陷**：assistant_gimmicks 的 "Grey Sky" 星云报告挂错路径（死代码，永不生效）
- **互斥链**：late_arrivals↔random_spawns↔hangover 三者互黑；quick↔slow_shuttle；strong↔distant_supply_lines；filled↔empty_maint；luxury↔cramped_escape_pods；announcement 三件套互斥；deathrattle 系内部互斥；全部职业特质互相排斥

### 3.3 不可回退（can_revert=FALSE）

filled_maint / unnatural_atmosphere / background_checks / empty_maint / revenge_of_pun_pun

---

## 附录 · 代码路径索引

| 文件 | 行数 | 内容 |
|---|---|---|
| `code/datums/station_traits/_station_trait.dm` | 127 | 基类（name/weight/cost/report/blacklist/flags） |
| `code/datums/station_traits/positive_traits.dm` | 443 | 正面特质（26 定义） |
| `code/datums/station_traits/negative_traits.dm` | 781 | 负面特质（26 定义） |
| `code/datums/station_traits/neutral_traits.dm` | 584 | 中性特质（18 定义） |
| `code/datums/station_traits/job_traits.dm` | 253 | 职业特质（6 定义） |
| `code/datums/station_traits/admin_panel.dm` | 130 | 管理员面板 |
| `code/controllers/subsystem/processing/station.dm` | 180 | 抽取机制（预算制） |
| `code/controllers/configuration/entries/game_options.dm` | 532 | 预算配置 |
| `code/datums/communications.dm` | 180 | 纳米指挥部报告 |
| `code/__DEFINES/station.dm` | — | 类型/花费/标志常量 |
| `modular_nova/modules/station_traits/code/station_traits.dm` | 26 | NOVA 覆写（禁用/权重调整） |
| `modular_nova/modules/station_traits/code/birthday_opt_out.dm` | 10 | 生日退出偏好 |
| `modular_nova/modules/station_traits/code/job_traits.dm` | 3 | NOVA 职业覆写 |
| `modular_nova/master_files/code/datums/station_traits/negative_traits.dm` | 8 | NOVA 星云风暴开关 |

---

> **索引**：本页共 1 篇，覆盖站点特质系统全量（机制 + 76 定义全录 + NOVA 修改 + 彩蛋）。
