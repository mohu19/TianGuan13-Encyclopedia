# TianGuan13 毒品与成瘾扩展百科

> 天关 (TianGuan13 / NovaSector 分支) — More Narcotics 扩展毒品系统完全解说
>
> **模块**: `modular_nova/modules/morenarcotics/`（12 个 `.dm` 文件，共 1,016 行）
> **源码路径**: `modular_nova/modules/morenarcotics/code/*.dm`（物品图标在 `icons/`：`crack.dmi`、`mask.dmi`、`thcsnacks.dmi`）
> **依赖核心**: 成瘾机制本体位于 `code/modules/reagents/withdrawal/generic_addictions.dm`（`/datum/addiction/opioids`、`/datum/addiction/stimulants`）
> **数值基准**: `CARGO_CRATE_VALUE = 200`（`code/__DEFINES/cargo.dm`）；`PAYCHECK_LOWER = 25`、`PAYCHECK_CREW = 50`（`code/__DEFINES/economy.dm`）

---

## 目录

- **第一部分 · 总览与机制**
  - [一、模块总览](#一模块总览)
  - [二、成瘾机制扩展（Addiction）](#二成瘾机制扩展addiction)
- **第二部分 · 毒品全录（每种毒品独立条目）**
  - [三、阿片类（Opium / Heroin / Black Tar）](#三阿片类opium--heroin--black-tar)
  - [四、可卡因类（Cocaine / Powder / Freebase Crack）](#四可卡因类cocaine--powder--freebase-crack)
  - [五、大麻类（THC / Hash / Dab）](#五大麻类thc--hash--dab)
  - [六、PCP 苯环利定](#六pcp-苯环利定)
  - [七、Quaalude 安眠酮](#七quaalude-安眠酮)
- **第三部分 · 原料与道具**
  - [八、毒品植物来源（Coca / Opium Poppy）](#八毒品植物来源coca--opium-poppy)
  - [九、吸毒道具（Crack Pipe / Vape Carts）](#九吸毒道具crack-pipe--vape-carts)
  - [十、大麻零食与消费品](#十大麻零食与消费品)
- **第四部分 · 附录**
  - [十一、出口经济价值（Exports）](#十一出口经济价值exports)
  - [十二、快速参考总表](#十二快速参考总表)
  - [十三、代码路径索引](#十三代码路径索引)

---

## 一、模块总览

**More Narcotics**（更多毒品）是 NovaSector 的扩展毒品模块，在 TG 原版毒品系统之上新增/强化了 5 大类毒品、2 种毒品植物、2 类吸毒道具和 1 套大麻零食线。所有毒品均接入核心成瘾系统（`addiction_types`），并带有完整的化学合成配方（`/datum/chemical_reaction`）与出口经济价值（`/datum/export`）。

**模块文件清单（12 个 `.dm`，共 1,016 行）：**

| # | 文件 | 行数 | 内容 |
|---|---|---|---|
| 01 | `blacktar.dm` | 8 | 黑焦油海洛因物品 |
| 02 | `cocaine.dm` | 88 | 可卡因试剂 + 合成配方 |
| 03 | `cocaine_item.dm` | 133 | 快克/可卡因物品 + 砖块 + 出口 |
| 04 | `cocoleaves.dm` | 24 | 古柯叶种子/植物 |
| 05 | `crackpipe.dm` | 60 | 快克烟斗 |
| 06 | `opium.dm` | 209 | 鸦片/海洛因/黑焦油试剂 + 物品 + 出口 |
| 07 | `opiumpoppy.dm` | 26 | 罂粟种子/植物 |
| 08 | `pcp.dm` | 100 | PCP/PCC 试剂 + 合成配方 |
| 09 | `quaalude.dm` | 60 | 安眠酮试剂 + 配方 |
| 10 | `thc.dm` | 90 | THC/大麻脂/大麻油试剂 |
| 11 | `thc_item.dm` | 124 | 大麻物品 + 零食线 + 出口 |
| 12 | `vapecarts.dm` | 94 | 电子烟烟弹（尼古丁/THC/自定义） |

---

## 二、成瘾机制扩展（Addiction）

**代码**: `code/modules/reagents/withdrawal/generic_addictions.dm`（核心） + 各毒品 `.dm` 中的 `addiction_types` 挂载点

本模块的毒品通过 `addiction_types = list(/datum/addiction/xxx = 阈值)` 接入核心成瘾系统。**成瘾阈值（addiction threshold）** 指体内该试剂达到该剂量时开始计算成瘾进度。

### 2.1 本模块毒品成瘾挂载表

| 毒品 | 成瘾类型 | 阈值 | 源码 |
|---|---|---|---|
| **Opium 鸦片** | `/datum/addiction/opioids`（阿片类） | **30** | `opium.dm` |
| **Heroin 海洛因** | 继承自 opium（`opioids`） | 30（继承） | `opium.dm` |
| **Black Tar Heroin 黑焦油海洛因** | 继承自 opium（`opioids`） | 30（继承） | `opium.dm` |
| **Cocaine 可卡因** | `/datum/addiction/stimulants`（兴奋剂类） | **40** | `cocaine.dm` |
| **Freebase / Powder Cocaine** | 继承自 cocaine（`stimulants`） | 40（继承） | `cocaine.dm` |
| **THC / Hash / Dab** | 无成瘾（无 `addiction_types`） | — | `thc.dm` |
| **PCP** | 无成瘾 | — | `pcp.dm` |
| **Quaalude** | 无成瘾 | — | `quaalude.dm` |
| **Nicotine（电子烟烟弹）** | `/datum/addiction/nicotine`（核心已有） | 0.01（`MIN_NICOTINE_ADDICTION_REAGENT_AMOUNT`） | `vapecarts.dm` |

### 2.2 阿片类成瘾 — Opioid Addiction

**代码**: `generic_addictions.dm` → `/datum/addiction/opioids`

- **诊断描述**: "Patient has developed a dependence on opioid substances."（患者已对阿片类物质产生依赖）
- **戒断症状**: "Expresses a desire for painkillers, and when in withdrawal, experiences drowsiness, high blood pressure, and nausea."（渴望止痛药；戒断时出现嗜睡、高血压、恶心）
- **阶段台词**（3 阶段逐级递进）:
  1. "I feel aches in my bodies.."（我浑身酸痛……）
  2. "I need some pain relief..."（我需要止痛……）
  3. "It aches all over...I need some opioids!"（全身都痛……我需要阿片类！）

**戒断机制（分阶段）：**

| 阶段 | 效果 | 数值 |
|---|---|---|
| 阶段 1 进行中 | 打哈欠 | `SPT_PROB(10)` 概率 emote "yawn" |
| 进入阶段 2 | 施加**高血压**状态 | `/datum/status_effect/high_blood_pressure` |
| 阶段 3 进行中 | 增加恶心值（disgust） | 若 `disgust < DISGUST_LEVEL_DISGUSTED`，每 tick `+12.5`，概率 `SPT_PROB(7.5)` |
| 戒断结束 | 移除高血压、恶心值减半 | `remove_status_effect(high_blood_pressure)`；`disgust * 0.5` |

### 2.3 兴奋剂类成瘾 — Stimulant Addiction

**代码**: `generic_addictions.dm` → `/datum/addiction/stimulants`

- **诊断描述**: "Patient has developed a dependence on stimulant substances."（患者已对兴奋剂物质产生依赖）
- **戒断症状**: "Expresses a desire for stimulants, and when in withdrawal, experiences fatigue, slowness, and difficulty concentrating."（渴望兴奋剂；戒断时出现疲劳、行动迟缓、难以集中注意力）
- **阶段台词**:
  1. "You feel a bit tired...You could really use a pick me up."（有点累了……真该来一剂提神的）
  2. "You are getting a bit woozy..."（开始有点晕乎……）
  3. "So...Tired..."（好……累……）

**戒断机制（分阶段）：**

| 阶段 | 效果 | 数值/标识 |
|---|---|---|
| 进入阶段 1 | 施加**行动速度惩罚** | `add_actionspeed_modifier(/datum/actionspeed_modifier/stimulants)`（`ACTIONSPEED_ID_STIMULANTS`） |
| 进入阶段 2 | 施加**头晕恍惚**状态 | `/datum/status_effect/woozy` |
| 进入阶段 3 | 施加**移动速度惩罚** | `add_movespeed_modifier(/datum/movespeed_modifier/stimulants)`（`MOVESPEED_ID_STIMULANTS`） |
| 戒断结束 | 移除以上全部 | `remove_actionspeed_modifier` + `remove_status_effect(woozy)` + `remove_movespeed_modifier` |

> 注：`generic_addictions.dm` 还定义了 alcohol（酒精）、hallucinogens（致幻剂）、maintenance_drugs（维护药物）、medicine（药物依赖）、nicotine（尼古丁）等核心成瘾，其中尼古丁与 `vapecarts.dm` 的电子烟烟弹直接相关。

---

## 三、阿片类（Opium / Heroin / Black Tar）

**代码**: `opium.dm`（209 行）+ `blacktar.dm`（8 行）

阿片类家族共 **4 种试剂** + **3 种物品** + **3 个合成配方** + **3 个出口条目**，全部继承 `/datum/reagent/drug/opium`，成瘾类型为 opioids（阈值 30）。

### 3.1 Opium 鸦片

**试剂**: `/datum/reagent/drug/opium`（`opium.dm`）

| 属性 | 值 |
|---|---|
| 名称 | opium（鸦片） |
| 描述 (EN) | "A extract from opium poppies. Puts the user in a slightly euphoric state." |
| 描述 (中) | 从罂粟中提取的提取物，让使用者进入轻度欣快状态。 |
| 颜色 | `#ffe669`（淡黄） |
| **过量阈值** | **30** |
| pH | 8 |
| 味道 | flowers（花香） |
| 成瘾 | `/datum/addiction/opioids = 30` |

**效果（on_mob_life，每 tick）：**
- 随机欣快台词（`SPT_PROB(2.5)`）："You feel euphoric."（你感到欣快。）/ "You feel on top of the world."（你感觉站在世界之巅。）
- 心情事件 "smacked out" → `/datum/mood_event/narcotic_heavy`
- **钝击伤治疗**：`-0.1 × SPT × 代谢率`（可当**劣质止痛药**）
- **灼烧伤治疗**：`-0.1 × SPT × 代谢率`
- 全屏滤镜 "heroin_euphoria"（颜色 `#444444`，视觉变暗）

**过量（overdose_process，阈值 30 以上）：**
- 大脑器官损伤：`+0.5 × SPT × 代谢率`
- 毒素伤害：`+1 × SPT × 代谢率`
- 困倦：`+1 秒 × 纯度 × SPT × 代谢率`

**代谢附加：** 代谢开始时施加 `/datum/status_effect/grouped/screwy_hud/fake_healthy`（假健康 HUD）；代谢结束时移除并清除全屏滤镜。

**来源**: 罂粟植物（见 §8.2），每株含 opium 0.3 + fentanyl 0.075。

### 3.2 Heroin 海洛因

**试剂**: `/datum/reagent/drug/opium/heroin`（`opium.dm`）

| 属性 | 值 |
|---|---|
| 名称 | heroin（海洛因） |
| 描述 (EN) | "She's like heroin to me, she's like heroin to me! She cannot... miss a vein!" |
| 描述 (中) | 她对我来说就像海洛因！她不能……错过一条血管！（歌词梗） |
| 颜色 | `#ffe669` |
| **过量阈值** | **20**（比鸦片更低） |
| pH | 6 |
| 味道 | flowers（花香） |
| 可合成 | `REAGENT_CAN_BE_SYNTHESIZED` |
| 逆反应 | `inverse_chem = /datum/reagent/drug/opium/blacktar/liquid`（防止自复制，混合时降一级） |

**效果（on_mob_life，叠加在 opium 基础上）：**
- 随机高台词（`SPT_PROB(2.5)`）："You feel like nothing can stop you."（你感觉没什么能阻止你。）/ "You feel like God."（你感觉像上帝。）
- **钝击伤治疗**：`-0.4 × SPT × 代谢率`（**强效止痛药**，注释：对医疗可能真正有用）
- **灼烧伤治疗**：`-0.4 × SPT × 代谢率`

**过量**：继承 opium 的过量逻辑（阈值 20）。

**配方 — 海洛因合成**（`/datum/chemical_reaction/heroin`）:

| 参数 | 值 |
|---|---|
| 产物 | heroin × 4 |
| 原料 | **opium × 2 + acetone × 2** |
| 温度要求 | `required_temp = 480` |
| pH 区间 | `optimal_ph_min = 8`，`optimal_ph_max = 12` |
| H 离子释放 | `-0.04` |
| 速率上限 | `rate_up_lim = 12.5` |
| 最低纯度 | `purity_min = 0.5` |
| 标签 | `REACTION_TAG_CHEMICAL` |

**配方 — 海洛因粉末化**（`/datum/chemical_reaction/powder_heroin`）:

| 参数 | 值 |
|---|---|
| 类型 | 冷配方（`is_cold_recipe = TRUE`） |
| 原料 | **heroin × 8** |
| 温度要求 | `required_temp = 250`（冻结） |
| 反应 | `REACTION_INSTANT`（瞬时） |
| 混合信息 | "The solution freezes into a powder!"（溶液冻结成粉末！） |
| 产物 | 每单位体积生成 1 个 `/obj/item/reagent_containers/heroin` |

**物品 — 海洛因袋**（`/obj/item/reagent_containers/heroin`）:

| 属性 | 值 |
|---|---|
| 名称 | heroin（海洛因） |
| 描述 (EN) | "Take a line and take some time off, man."（来一条，歇一会儿，老兄。） |
| 容量 | 4 单位 |
| 内含 | heroin × 4 |
| 图标 | `icons/crack.dmi`，icon_state "heroin" |
| 固定转移量 | `has_variable_transfer_amount = FALSE` |

**吸食方式 — snort() 鼻吸流程**：
1. 必须为碳基生物（`iscarbon`）；头部/口罩遮挡口部时拒绝（提示 "headgear"/"mask"）
2. 必须拥有**生物肺**（无肺或合成肺 `synth` 均拒绝）
3. `do_after(user, 30)` 后，全部试剂以 `INGEST` 方式转移给使用者，物品销毁
4. 触发方式：`attack_self`（主动使用）或 `attack_hand_secondary`（副手点击，需在范围内且主手为空）

**物品 — 海洛因砖**（`/obj/item/reagent_containers/heroinbrick`）:

| 属性 | 值 |
|---|---|
| 名称 | heroin brick（海洛因砖） |
| 描述 (EN) | "A brick of heroin. Good for transport!"（一砖海洛因，方便运输！） |
| 容量 | 20 单位 |
| 内含 | heroin × 20 |
| 拆解 | 主动使用 → `do_after 10` → 拆成 **5 袋海洛因** |

**制作配方 — 海洛因砖**（`/datum/crafting_recipe/heroinbrick`）: 5 袋海洛因 → 1 砖，耗时 20，分类 `CAT_CHEMISTRY`。

### 3.3 Black Tar Heroin 黑焦油海洛因

**试剂**: `/datum/reagent/drug/opium/blacktar`（`opium.dm`）

| 属性 | 值 |
|---|---|
| 名称 | black tar heroin（黑焦油海洛因） |
| 描述 (EN) | "An impure, freebase form of heroin. Probably not a good idea to take this..." |
| 描述 (中) | 一种不纯的海洛因游离碱形态。服用它大概不是什么好主意…… |
| 颜色 | `#242423`（近黑） |
| **过量阈值** | **10**（比海洛因更容易过量） |
| pH | 8 |
| 味道 | flowers（花香） |
| 可合成 | `REAGENT_CAN_BE_SYNTHESIZED` |

**效果（on_mob_life，叠加在 opium 基础上）：**
- 随机台词（`SPT_PROB(2.5)`）："You feel like tar."（你感觉像焦油。）/ "The blood in your veins feel like syrup."（你血管里的血像糖浆。）
- **致幻**：`set_drugginess(20 秒 × SPT × 代谢率)`
- **毒素伤害**：`+0.5 × SPT × 代谢率`

**变体 — 液态黑焦油海洛因**（`/datum/reagent/drug/opium/blacktar/liquid`）:
- 名称 "liquid black tar heroin"（液态黑焦油海洛因）
- 用途：防止自我复制——混合时降一级（海洛因的 `inverse_chem` 指向它）

**配方 — 黑焦油固化**（`/datum/chemical_reaction/blacktar`）:

| 参数 | 值 |
|---|---|
| 原料 | **liquid black tar heroin × 5**（液态黑焦油） |
| 温度要求 | `required_temp = 480` |
| 反应 | `REACTION_INSTANT` |
| 产物 | 每单位体积生成 1 个 `/obj/item/reagent_containers/blacktar` |

**物品 — 黑焦油块**（`/obj/item/reagent_containers/blacktar`，`blacktar.dm`）:

| 属性 | 值 |
|---|---|
| 名称 | black tar heroin（黑焦油海洛因） |
| 描述 (EN) | "A rock of black tar heroin, an impure, freebase form of heroin."（一块黑焦油海洛因，不纯的海洛因游离碱形态。） |
| 容量 | 5 单位 |
| 内含 | blacktar × 5 |
| 图标 | `icons/crack.dmi`，icon_state "blacktar" |

**注意**: 黑焦油块可以直接塞进快克烟斗吸食（见 §9.1 烟斗打包逻辑：`crackpipe.item_interaction` 接受 `/obj/item/reagent_containers/crack` 与 `/obj/item/reagent_containers/blacktar`）。

### 3.4 阿片类出口价值

| 出口条目 | 物品 | 价格（× CARGO_CRATE_VALUE=200） |
|---|---|---|
| `/datum/export/heroin` | 海洛因袋 | 0.5 × 200 = **100** |
| `/datum/export/heroinbrick` | 海洛因砖 | 2.5 × 200 = **500** |
| `/datum/export/blacktar` | 黑焦油块 | 0.4 × 200 = **80** |

---

## 四、可卡因类（Cocaine / Powder / Freebase Crack）

**代码**: `cocaine.dm`（88 行）+ `cocaine_item.dm`（133 行）

可卡因家族共 **3 种试剂** + **4 种物品** + **2 个合成配方** + **4 个出口条目**，成瘾类型为 stimulants（阈值 40）。

### 4.1 Cocaine 可卡因

**试剂**: `/datum/reagent/drug/cocaine`（`cocaine.dm`）

| 属性 | 值 |
|---|---|
| 名称 | cocaine（可卡因） |
| 描述 (EN) | "A powerful stimulant extracted from coca leaves. Reduces stun times, but causes drowsiness and severe brain damage if overdosed." |
| 描述 (中) | 从古柯叶中提取的强效兴奋剂。缩短眩晕时间，但过量会导致困倦和严重脑损伤。 |
| 颜色 | `#ffffff`（白） |
| **过量阈值** | **20** |
| pH | 9 |
| 味道 | bitterness（苦味） |
| 成瘾 | `/datum/addiction/stimulants = 40` |

**代谢开始（on_mob_metabolize）**：
- 施加移动速度修正 `/datum/movespeed_modifier/reagent/stimulants`（加速）
- 获得特质 **`TRAIT_BATON_RESISTANCE`**（警棍抗性——缩短被警棍眩晕的时间）

**效果（on_mob_life，每 tick）：**
- 随机台词（`SPT_PROB(2.5)`）："You feel jittery."（你感到坐立不安。）/ "You feel like you gotta go fast."（你感觉必须加速前进。）/ "You feel like you need to step it up."（你感觉必须加把劲。）
- 心情事件 "zoinked" → `/datum/mood_event/stimulant_heavy`
- **减少眩晕/击倒/昏迷/禁锢/瘫痪**（各 `-1.5 秒 × SPT × 代谢率`）：
  - `AdjustStun(-1.5s)`、`AdjustKnockdown(-1.5s)`、`AdjustUnconscious(-1.5s)`、`AdjustImmobilized(-1.5s)`、`AdjustParalyzed(-1.5s)`
- **体力回复**：`-2 点体力损失 × SPT × 代谢率`
- 打寒颤 emote（`SPT_PROB(2.5)` → "shiver"）

**过量开始（overdose_start）**：对玩家显示 `span_userdanger` 红色警告。

**过量（overdose_process，阈值 20 以上）：**
- 毒素伤害：`+1 × SPT × 代谢率`
- **心脏器官损伤**：`rand(10, 20) / 10 × SPT × 代谢率`（即每次 **1.0 ~ 2.0** 随机）
- 抖动：`set_jitter_if_lower(5 秒)`
- 抽搐/流口水 emote（`SPT_PROB(2.5)` → "twitch"/"drool"）
- **倒地**：若未处于 `TRAIT_FLOORED`，`SPT_PROB(1.5)` 概率 → 公开消息 + **`Paralyze(135, TRUE)`**（瘫痪 13.5 秒）+ **掉落所有手持物品**

### 4.2 Freebase Cocaine 游离碱可卡因（快克）

**试剂**: `/datum/reagent/drug/cocaine/freebase_cocaine`

| 属性 | 值 |
|---|---|
| 名称 | freebase cocaine（游离碱可卡因） |
| 描述 (EN) | "A smokable form of cocaine."（可吸食形态的可卡因。） |
| 颜色 | `#f0e6bb`（米黄） |
| 可合成 | `REAGENT_CAN_BE_SYNTHESIZED` |
| 继承 | 全部可卡因效果 + 成瘾（stimulants / 40） |

### 4.3 Powder Cocaine 粉末可卡因

**试剂**: `/datum/reagent/drug/cocaine/powder_cocaine`

| 属性 | 值 |
|---|---|
| 名称 | powder cocaine（粉末可卡因） |
| 描述 (EN) | "The powder form of cocaine."（粉末形态的可卡因。） |
| 颜色 | `#ffffff` |
| 可合成 | `REAGENT_CAN_BE_SYNTHESIZED` |
| 继承 | 全部可卡因效果 + 成瘾（stimulants / 40） |

### 4.4 合成配方

**配方 — 可卡因粉末化**（`/datum/chemical_reaction/powder_cocaine`）:

| 参数 | 值 |
|---|---|
| 类型 | 冷配方（`is_cold_recipe = TRUE`） |
| 原料 | **cocaine × 10** |
| 温度要求 | `required_temp = 250`（冻结） |
| 反应 | `REACTION_INSTANT` |
| 混合信息 | "The solution freezes into a powder!" |
| 产物 | 每单位体积生成 1 个 `/obj/item/reagent_containers/cocaine` |

**配方 — 游离碱化（快克）**（`/datum/chemical_reaction/freebase_cocaine`）:

| 参数 | 值 |
|---|---|
| 原料 | **cocaine × 10 + water × 5 + ash × 10**（源码注释：即 20 可卡因 + 10 水 + 20 灰） |
| 温度要求 | `required_temp = 480`（加热） |
| 反应 | `REACTION_INSTANT` |
| 产物 | 每单位体积生成 1 个 `/obj/item/reagent_containers/crack` |

### 4.5 物品

**物品 — 快克石**（`/obj/item/reagent_containers/crack`，`cocaine_item.dm`）:

| 属性 | 值 |
|---|---|
| 名称 | crack（快克） |
| 描述 (EN) | "A rock of freebase cocaine, otherwise known as crack."（一块游离碱可卡因，俗称快克。） |
| 容量 | 10 单位 |
| 内含 | freebase_cocaine × 10 |
| 图标 | `icons/crack.dmi`，icon_state "crack" |
| 用法 | 塞入快克烟斗吸食（见 §9.1） |

**物品 — 快克砖**（`/obj/item/reagent_containers/crackbrick`）:

| 属性 | 值 |
|---|---|
| 名称 | crack brick（快克砖） |
| 描述 (EN) | "A brick of crack cocaine."（一砖快克可卡因。） |
| 容量 | 40 单位 |
| 内含 | freebase_cocaine × 40 |
| 切割 | 用**尖锐工具**（`get_sharpness()`）点击 → 切为 **4 块快克石**，砖销毁 |

**制作配方 — 快克砖**（`/datum/crafting_recipe/crackbrick`）: 4 块快克石 → 1 砖，耗时 20，分类 `CAT_CHEMISTRY`（注释：也许以后会专门做个毒品制作分类）。

**物品 — 可卡因袋**（`/obj/item/reagent_containers/cocaine`）:

| 属性 | 值 |
|---|---|
| 名称 | cocaine（可卡因） |
| 描述 (EN) | "Reenact your favorite scenes from Scarface!"（重现《疤面煞星》里你最爱的场景吧！） |
| 容量 | 5 单位 |
| 内含 | cocaine × 5 |
| 图标 | `icons/crack.dmi`，icon_state "cocaine" |
| 吸食 | 同海洛因 snort() 流程（30 tick do_after → INGEST 全部转移 → 销毁）；`attack_self` / `attack_hand_secondary` 触发 |

**物品 — 可卡因砖**（`/obj/item/reagent_containers/cocainebrick`）:

| 属性 | 值 |
|---|---|
| 名称 | cocaine brick（可卡因砖） |
| 描述 (EN) | "A brick of cocaine. Good for transport!"（一砖可卡因，方便运输！） |
| 容量 | 25 单位 |
| 内含 | cocaine × 25 |
| 拆解 | 主动使用 → `do_after 10` → 拆成 **5 袋可卡因** |

**制作配方 — 可卡因砖**（`/datum/crafting_recipe/cocainebrick`）: 5 袋可卡因 → 1 砖，耗时 20，分类 `CAT_CHEMISTRY`。

> 源码注释（经济提示）: "if you want money, convert it into crackbricks"（想赚钱就把它做成快克砖——快克砖单价比可卡因砖高）。

### 4.6 可卡因类出口价值

| 出口条目 | 物品 | 价格（× 200） |
|---|---|---|
| `/datum/export/crack` | 快克石 | 0.5 × 200 = **100** |
| `/datum/export/crack/crackbrick` | 快克砖 | 2.5 × 200 = **500** |
| `/datum/export/cocaine` | 可卡因袋 | 0.4 × 200 = **80** |
| `/datum/export/cocainebrick` | 可卡因砖 | 2.0 × 200 = **400** |

---

## 五、大麻类（THC / Hash / Dab）

**代码**: `thc.dm`（90 行）+ `thc_item.dm`（124 行）

大麻家族共 **3 种试剂** + **3 种物品** + **2 个合成配方** + **1 个心情事件/警报** + **6 种零食消费品**。大麻类**无成瘾**（无 `addiction_types`）。

### 5.1 THC 四氢大麻酚

**试剂**: `/datum/reagent/drug/thc`（`thc.dm`）

| 属性 | 值 |
|---|---|
| 名称 | THC（四氢大麻酚） |
| 描述 (EN) | "A chemical found in cannabis that serves as its main psychoactive component." |
| 描述 (中) | 大麻中的化学物质，是其主要的致幻活性成分。 |
| 颜色 | `#cfa40c`（金黄） |
| **过量阈值** | **30**（注释：只产生搞笑效果，不伤害你——THC 没有已知的致死过量） |
| pH | 6 |
| 味道 | skunk（臭鼬味） |
| 成瘾 | 无 |

**效果（on_mob_life，每 tick）：**
- 随机台词（`SPT_PROB(2.5)`）："You feel relaxed."（你感到放松。）/ "You feel fucked up."（你感觉嗨翻了。）/ "You feel totally wrecked..."（你感觉彻底废了……）
- **屏幕模糊**：游戏画面叠加 `angular_blur_filter(0, 0, 0.45)`（"weed_blur" 滤镜）
- 心情事件 "stoned" → `/datum/mood_event/stoned`
- 警报 "Stoned"（图标 "high"）
- 声音环境覆盖为 `SOUND_ENVIRONMENT_DRUGGED`（吸毒音效环境）
- 眩晕：`set_dizzy_if_lower(5 × SPT × 代谢率 × 2 秒)`
- **饥饿**（munchies 想吃零食）：营养值 `-1 × SPT × 代谢率`
- 大笑/傻笑 emote（`SPT_PROB(3.5)` → "laugh"/"giggle"）

**代谢结束（on_mob_end_metabolize）**：移除 weed_blur 滤镜、清除 "stoned" 警报、恢复声音环境为 `SOUND_ENVIRONMENT_NONE`。

**过量（overdose_process，阈值 30 以上，仅娱乐效果）：**
- 随机台词（`SPT_PROB(1.5)`）："It's major..."（这可太猛了……）/ "Oh my goodness..."（哦我的天……）
- 困倦：`+0.2 秒 × 纯度 × SPT × 代谢率`
- 咳嗽（`SPT_PROB(3.5)`）：播放肺咳音效 `lungbust_cough1.ogg` / `lungbust_cough2.ogg`（音量 50）+ emote "cough"

**心情事件 / 警报（附带定义）：**

| 定义 | 内容 |
|---|---|
| `/datum/mood_event/stoned` | 描述 "You're totally baked right now...\n"（你现在完全嗨了……）；**心情 +6**；持续 **3 分钟** |
| `/atom/movable/screen/alert/stoned` | 名称 "Stoned"；描述 "You're stoned out of your mind! Woaaahh..."（你嗨到神志不清！哇哦……）；图标 "high" |

### 5.2 Concentrated THC 浓缩 THC

**试剂**: `/datum/reagent/drug/thc/concentrated`

| 属性 | 值 |
|---|---|
| 名称 | Concentrated THC（浓缩 THC） |
| 描述 (EN) | "TCH in pure concentrated form"（纯浓缩形态的 THC，原文拼写即 TCH） |
| 继承 | 全部 THC 效果 |

### 5.3 Hashish 大麻脂（哈希什）

**试剂**: `/datum/reagent/drug/thc/hash`

| 属性 | 值 |
|---|---|
| 名称 | hashish（大麻脂） |
| 描述 (EN) | "Concentrated cannabis extract. Delivers a much better high when used in a bong." |
| 描述 (中) | 浓缩大麻提取物。用水烟筒吸食时带来更好的快感。 |
| 颜色 | `#cfa40c` |
| 用途 | 仅用于生成 hash 物品（源码注释："only exists to generate hash object"） |

**衍生转化（`thc.dm`）**：对**干燥**的大麻（`TRAIT_DRIED`）研磨（`on_grind()`）时，把试剂中的 THC 转化为 hash（`convert_reagent(thc, thc/hash, 1)`）。

### 5.4 合成配方

**配方 — 大麻脂固化**（`/datum/chemical_reaction/hash`）:

| 参数 | 值 |
|---|---|
| 原料 | **thc/hash × 10** |
| 反应 | `REACTION_INSTANT` |
| 产物 | 每单位体积生成 1 个 `/obj/item/reagent_containers/hash` |

**配方 — 大麻油（Dab）**（`/datum/chemical_reaction/dabs`）:

| 参数 | 值 |
|---|---|
| 原料 | **thc × 20** |
| 温度要求 | `required_temp = 420`（源码注释："haha very funny"——420 梗） |
| 反应 | `REACTION_INSTANT` |
| 产物 | 每单位体积生成 1 个 `/obj/item/reagent_containers/hash/dabs` |

### 5.5 物品

**物品 — 大麻脂块**（`/obj/item/reagent_containers/hash`，`thc_item.dm`）:

| 属性 | 值 |
|---|---|
| 名称 | hash（大麻脂） |
| 描述 (EN) | "Concentrated cannabis extract. Delivers a much better high when used in a bong." |
| 容量 | 20 单位 |
| 内含 | **thc × 15 + lipolicide（脂肪溶解剂/毒素）× 5** |
| 图标 | `icons/crack.dmi`，icon_state "hash" |

**物品 — 大麻油 Dab**（`/obj/item/reagent_containers/hash/dabs`）:

| 属性 | 值 |
|---|---|
| 名称 | dab（大麻油） |
| 描述 (EN) | "Oil extract from cannabis plants. Just delivers a different type of hit." |
| 描述 (中) | 大麻植物油提取物。带来一种不同的冲击感。 |
| 容量 | 40 单位 |
| 内含 | **浓缩 THC × 40**（源码注释："horrendously powerful"——恐怖级别的强） |
| 图标 | `icons/crack.dmi`，icon_state "dab" |

**物品 — 大麻脂砖**（`/obj/item/reagent_containers/hashbrick`）:

| 属性 | 值 |
|---|---|
| 名称 | hash brick（大麻脂砖） |
| 描述 (EN) | "A brick of hash. Good for transport!"（一砖大麻脂，方便运输！） |
| 容量 | 80 单位 |
| 内含 | **thc × 60 + lipolicide × 20** |
| 拆解 | 主动使用 → `do_after 10` → 拆成 **4 块大麻脂** |

**制作配方 — 大麻脂砖**（`/datum/crafting_recipe/hashbrick`）: 4 块大麻脂 → 1 砖，耗时 20，分类 `CAT_CHEMISTRY`。

### 5.6 大麻类出口价值

| 出口条目 | 物品 | 价格（× 200） |
|---|---|---|
| `/datum/export/hash` | 大麻脂 | 0.35 × 200 = **70** |
| `/datum/export/crack/hashbrick` | 大麻脂砖 | 2.0 × 200 = **400** |
| `/datum/export/dab` | 大麻油 | 0.4 × 200 = **80** |

---

## 六、PCP 苯环利定

**代码**: `pcp.dm`（100 行）

PCP（苯环利定，"液态愤怒"）是本模块**过量阈值最低**的毒品，附带完整前体化学链（PCC）。共 **2 种试剂** + **2 个合成配方** + 全套狂怒创伤/滤镜/倒地机制。**无成瘾条目**。

### 6.1 PCC 前体

**试剂**: `/datum/reagent/pcc`（`pcp.dm`）

| 属性 | 值 |
|---|---|
| 名称 | PCC（PCP 化学前体） |
| 描述 (EN) | "A chemical precursor to PCP."（PCP 的化学前体。） |
| 颜色 | `#ffea2e`（黄） |
| 味道 | satiated rage（满足的愤怒） |
| pH | 7.3 |
| 可合成 | `REAGENT_CAN_BE_SYNTHESIZED` |

**配方 — PCC 合成**（`/datum/chemical_reaction/pcc`）:

| 参数 | 值 |
|---|---|
| 产物 | pcc × 1 |
| 原料 | **cyanide（氰化物）× 1 + fluacid（氟酸）× 1 + multiver（多功能解毒剂）× 2**（注释：现在获取它更费劲了） |
| pH 区间 | `optimal_ph_min = 1`，`optimal_ph_max = 4` |
| H 离子释放 | `+0.04` |
| 最低纯度 | `purity_min = 0.5` |
| 温度指数 | `temp_exponent_factor = 1.5` |
| 热常数 | `thermic_constant = 200` |
| 速率上限 | `rate_up_lim = 10` |
| 温度要求 | `required_temp = 250` |
| 标签 | `REACTION_TAG_CHEMICAL` |

### 6.2 PCP 试剂

**试剂**: `/datum/reagent/drug/pcp`

| 属性 | 值 |
|---|---|
| 名称 | PCP（苯环利定） |
| 描述 (EN) | "Pure rage put into chemical form."（纯粹的愤怒被灌入化学形态。） |
| 描述 (中) | 源码注释：某种程度上就是超级浴盐（bath salts） |
| 颜色 | `#ffea2e` |
| **过量阈值** | **10**（注释：非常低，防止玩家滥用） |
| pH | 8 |
| 味道 | rage（愤怒） |
| 成瘾 | 无 |

**内部变量**：
- `pcp_rage`：`/datum/brain_trauma/special/psychotic_brawling/bath_salts`（浴盐式狂怒精神病创伤）
- `pcp_tenacity`：`/datum/brain_trauma/special/tenacity`（韧性创伤——免疫疼痛/击倒）
- `pcp_lifetime`：累计 PCP 活跃时长（秒），用于结束时倒地结算

**代谢开始（on_mob_metabolize）**：
- 施加两个脑创伤（`TRAUMA_RESILIENCE_ABSOLUTE` 绝对韧性）：**狂怒格斗** + **韧性**（不惧疼痛）

**效果（on_mob_life，每 tick）：**
- 随机狂怒台词（`SPT_PROB(2.5)`）："You feel like KILLING!"（你想杀人！）/ "Someone's about to fucking die!"（有人要他妈的去死了！）/ "Rip and tear!"（撕裂与扯碎！——DOOM 梗）
- 屏幕模糊：`angular_blur_filter(0, 0, 0.7)`（"pcp_blur" 滤镜，比 THC 更重）
- **减少击倒/禁锢**：`AdjustKnockdown(-2 秒)`、`AdjustImmobilized(-2 秒)`
- **体力回复**：`-10 点体力损失 × SPT × 代谢率`（非常强力）
- **减少眩晕**：`AdjustStun(-1 秒)`（源码注释："this is absolutely rediculous"——这绝对离谱）
- 全屏红色滤镜 "pcp_rage"（`#550000`，一片血红）
- 声音环境覆盖 `SOUND_ENVIRONMENT_DRUGGED`
- 尖叫/抽搐 emote（`SPT_PROB(3.5)` → "scream"/"twitch"）
- `pcp_lifetime += 3 × SPT`（累计活跃时间）

**代谢结束（on_mob_end_metabolize）**：
- 移除 pcp_blur 滤镜、清除 pcp_rage 全屏、恢复声音环境
- 删除两个脑创伤（`QDEL_NULL`）
- **强制倒地**：公开危险消息 + **`Paralyze(pcp_lifetime, TRUE)`**（瘫痪时长 = 累计 PCP 活跃时间，即你嗨了多久就瘫多久）+ **掉落所有手持物品**（"you pretty much pass out"——你基本就是昏过去了）

**过量（overdose_process，阈值 10 以上）：**
- 毒素伤害：`+2 × SPT × 代谢率`
- **心脏器官损伤**：`+2 × SPT × 代谢率`
- **大脑器官损伤**：`+2 × SPT × 代谢率`
- **体力消耗逆转**：`+15 点体力损失 × SPT × 代谢率`（反向——过量时体力快速流失）
- 抖动：`set_jitter_if_lower(5 秒)`
- 抽搐/流口水 emote（`SPT_PROB(2.5)`）
- 掉物（`SPT_PROB(1.5)` → 公开消息 + `drop_all_held_items()`）

### 6.3 合成配方

**配方 — PCP 合成**（`/datum/chemical_reaction/pcp`）:

| 参数 | 值 |
|---|---|
| 产物 | pcp × 1 |
| 原料 | **pcc × 1 + iron（铁）× 2**（注释：铁只是替代镁） |
| 催化剂 | **plasma（等离子体）× 5**（`required_catalysts`） |
| 标签 | `REACTION_TAG_EASY | REACTION_TAG_CHEMICAL` |

> 完整 PCP 生产链：氰化物 + 氟酸 + 多效解毒剂 →（250K）→ PCC →（铁 + 等离子体催化）→ PCP。注意 PCC 配方**没有** `REACTION_TAG_EASY`，属于较难合成。

---

## 七、Quaalude 安眠酮

**代码**: `quaalude.dm`（60 行）

安眠酮（Methaqualone，70 年代布鲁克林小孩的最爱）是催眠型毒品。共 **1 种试剂** + **1 个合成配方**。**无成瘾条目**。

### 7.1 Quaalude 试剂

**试剂**: `/datum/reagent/drug/quaalude`

| 属性 | 值 |
|---|---|
| 名称 | Quaalude（安眠酮） |
| 描述 (EN) | "Relaxes the user, putting them in a hypnotic, drugged state. A favorite drug of kids from Brooklyn." |
| 描述 (中) | 让使用者放松，进入催眠般的嗑药状态。布鲁克林孩子们最爱的毒品。（源码注释：那可是史上最棒的神药） |
| 颜色 | `#ffe669` |
| **过量阈值** | **20** |
| pH | 8 |
| 味道 | lemons（柠檬） |
| 可合成 | `REAGENT_CAN_BE_SYNTHESIZED` |
| 成瘾 | 无 |

**代谢开始（on_mob_metabolize）**：
- 屏幕波浪滤镜：`wave_filter(300, 300, 3, 0, WAVE_SIDEWAYS)`（"quaalude_wave" 侧向波浪）

**效果（on_mob_life，每 tick）：**
- 随机台词（`SPT_PROB(2.5)`）："You feel relaxed."（你感到放松。）/ "You feel like you're on the moon."（你感觉像在月球上。）/ "You feel like you could walk 20 miles for a quaalude."（你感觉自己能为了一颗安眠酮走 20 英里。）
- **致幻**：`set_drugginess(1 分钟 × SPT × 代谢率)`
- **口齿不清**：`adjust_slurring_up_to(30 秒, 2 分钟)`（加到最多 2 分钟）
- 眩晕：`set_dizzy_if_lower(5 × SPT × 代谢率 × 2 秒)`
- **体力回复**：`-5 点体力损失 × SPT × 代谢率`
- 大笑/流口水 emote（`SPT_PROB(3.5)` → "laugh"/"drool"）
- **随机倒地**：若未处于 `TRAIT_FLOORED`，`SPT_PROB(1)` 概率 → 公开消息 + **`Knockdown(90, TRUE)`**（击倒 9 秒）+ **掉落所有手持物品**

**代谢结束（on_mob_end_metabolize）**：移除 "quaalude_wave" 滤镜。

**过量（overdose_process，阈值 20 以上）：**
- 随机布鲁克林台词（`SPT_PROB(1.5)` 说出）：
  - "BRING BACK THE FUCKING QUAALUDES!"（把那该死的安眠酮还回来！）
  - "I'd walk 20 miles for a quaalude, let me tell ya'!"（我跟你说，我愿意为安眠酮走 20 英里！）
  - "There's nothing like a fuckin' quaalude!"（没什么比得上他妈的安眠酮！）
- **大脑器官损伤**：`+0.25 × SPT × 代谢率`
- 毒素伤害：`+0.25 × SPT × 代谢率`
- 困倦：`+0.5 秒 × 纯度 × SPT × 代谢率`
- 抽搐 emote（`SPT_PROB(3.5)` → "twitch"）

### 7.2 合成配方

**配方 — 安眠酮合成**（`/datum/chemical_reaction/quaalude`）:

| 参数 | 值 |
|---|---|
| 产物 | quaalude × 4 |
| 原料 | **lemonjuice（柠檬汁）× 2 + hydrogen（氢）× 1 + chlorine（氯）× 1** |
| 标签 | `REACTION_TAG_EASY | REACTION_TAG_DRUG`（注意是 DRUG 标签而非 CHEMICAL） |

---

## 八、毒品植物来源（Coca / Opium Poppy）

**代码**: `cocoleaves.dm`（24 行）+ `opiumpoppy.dm`（26 行）

两种可水培种植的毒品植物，种子包可在水培/农场系统使用。

### 8.1 古柯叶 Coca Leaf

**种子**（`/obj/item/seeds/cocaleaf`）:

| 属性 | 值 |
|---|---|
| 名称 | coca leaf seed pack（古柯叶种子包） |
| 描述 (EN) | "These seeds grow into coca shrubs. They make you feel energized just looking at them..." |
| 描述 (中) | 这些种子会长成古柯灌木。光是看着它们就让你觉得充满能量…… |
| 物种 | "cocoleaf" |
| 植物名 | "Coca Leaves" |
| 成熟时间 | `maturation = 8` |
| 药效 | `potency = 20` |
| 生长阶段 | `growthstages = 1` |
| 产物 | `/obj/item/food/grown/cocaleaf` |
| 突变列表 | 空（不可突变） |
| 收获试剂 | **cocaine × 0.3 + nutriment（营养）× 0.15** |

**作物**（`/obj/item/food/grown/cocaleaf`）:

| 属性 | 值 |
|---|---|
| 名称 | coca leaf（古柯叶） |
| 描述 (EN) | "A leaf of the coca shrub, which contains a potent psychoactive alkaloid known as 'cocaine'." |
| 描述 (中) | 古柯灌木的叶子，含有强效精神活性生物碱"可卡因"。 |
| 食物类型 | FRUIT（水果——注释：毕竟它长在树上） |
| 味道 | leaves（叶子） |
| **蒸馏产物** | `sins_delight`（罪恶之悦鸡尾酒，乙醇类——注释："Cocaine is one hell of a sin." 可卡因可是个天大的罪过） |

### 8.2 罂粟 Opium Poppy

**种子**（`/obj/item/seeds/poppy/opiumpoppy`）:

| 属性 | 值 |
|---|---|
| 名称 | opium poppy seed pack（罂粟种子包） |
| 描述 (EN) | "These seeds grow into real opium poppies."（这些种子会长成真正的罂粟。） |
| 物种 | "opiumpoppy" |
| 植物名 | "Opium Poppy Plants" |
| 生长图标 | "opiumpoppy-grow" / 枯萎 "opiumpoppy-dead" |
| 产物 | `/obj/item/food/grown/poppy/opiumpoppy` |
| 收获试剂 | **opium × 0.3 + fentanyl（芬太尼）× 0.075 + nutriment × 0.05** |
| 插槽 | `slot_flags = null`（不可佩戴） |

**作物**（`/obj/item/food/grown/poppy/opiumpoppy`）:

| 属性 | 值 |
|---|---|
| 名称 | opium poppy seedpod（罂粟蒴果） |
| 描述 (EN) | "The seedpod of the opium poppy plant, which contains opium latex." |
| 描述 (中) | 罂粟植物的蒴果，含有鸦片乳胶。 |
| 佩戴状态 | "map_flower"；灰度配色 `#01690f`（绿） |
| **蒸馏产物** | `turbo`（涡轮伏特加，乙醇类——注释："How can a slow drug make fast drink? Don't question it." 慢药怎么能酿出快酒？别问。） |

> ⚠️ 注意：罂粟收获自带 **芬太尼（fentanyl）0.075/株**——一种强效阿片毒素，直接食用蒴果会摄入微量芬太尼。

---

## 九、吸毒道具（Crack Pipe / Vape Carts）

**代码**: `crackpipe.dm`（60 行）+ `vapecarts.dm`（94 行）

### 9.1 快克烟斗 Crack Pipe

**物品**: `/obj/item/cigarette/pipe/crackpipe`

| 属性 | 值 |
|---|---|
| 名称 | crack pipe（快克烟斗） |
| 描述 (EN) | "A slick, glass pipe made for smoking one thing: crack." |
| 描述 (中) | 一根光滑的玻璃烟斗，只为抽一样东西而造：快克。 |
| 化学容量 | `chem_volume = 20` |
| 图标 | `icons/crack.dmi`（玻璃烟斗 off/on 两态）；佩戴图标 `icons/mask.dmi`（口罩位） |
| 材料 | 铁 `SHEET_MATERIAL_AMOUNT × 5.05` + 玻璃 `SHEET_MATERIAL_AMOUNT × 1.05` |

**吸烟循环（process，每 tick）**：
- `smoketime -= SPT`；归零 → 熄火（`lit = FALSE`）、恢复熄火图标、名称变为 "empty crack pipe"（空的快克烟斗）、停止处理
- 点燃状态：`open_flame()` + 若烟斗内有试剂则 `handle_reagents()`（把试剂送进肺里）

**装填与点燃（item_interaction）**：
- 用 **快克石（crack）或黑焦油块（blacktar）** 点击烟斗 → 装填：`smoketime = 2 × 60`（**120 秒**），名称变为 "[物品名]-packed crack pipe"，试剂全部转移进烟斗，原料销毁
- 用打火机/点火工具点击 → 若 `smoketime > 0` 则点燃（`light()`）；未装填则提示 "It's empty."

**制作配方 — 快克烟斗**（`/datum/crafting_recipe/crackpipe`）:

| 参数 | 值 |
|---|---|
| 原料 | **电缆线缆 × 5 + 玻璃碎片（shard）× 1 + 金属杆 × 10** |
| 部件消耗 | shard × 1 |
| 耗时 | 20 |
| 分类 | `CAT_CHEMISTRY` |

### 9.2 电子烟烟弹 Vape Carts

**物品**: `/obj/item/reagent_containers/vapecart`（基础版）

| 属性 | 值 |
|---|---|
| 名称 | vape cart（电子烟烟弹） |
| 描述 (EN) | "A vape cart filled with nicotine."（装满尼古丁的电子烟烟弹。） |
| 容量 | 50 单位 |
| 内含 | **nicotine × 50**（尼古丁） |
| 填充图标阈值 | `fill_icon_thresholds = list(0, 5, 20, 40)` |
| 售价 | `custom_price = PAYCHECK_CREW`（**50 信用点**） |

**与电子烟交互（interact_with_atom）**：
- 对电子烟（`/obj/item/vape`）使用：
  - 电子烟**已卸下螺丝**（`screw == TRUE`）且**为空** → 全部试剂注入电子烟，烟弹销毁
  - 未卸螺丝 → 警告（需要先拆螺丝）
  - 电子烟非空 → 警告（需要先清空）

**物品 — 自定义空烟弹**（`/obj/item/reagent_containers/vapecart/empty`）:

| 属性 | 值 |
|---|---|
| 名称 | customizable vape cart（可自定义电子烟烟弹） |
| 描述 (EN) | "Fill with whatever hazardous concoction of chemicals you desire!"（想灌什么危险的化学混合物都随你！） |
| 内含 | 空（`list_reagents = list()`） |
| 容器类型 | `OPENCONTAINER`（开放容器，可自行灌装） |
| 清空 | 主动使用 → 清空全部试剂 |
| **贴标签** | 用笔（pen）或蜡笔（crayon）点击 → 输入最多 **53 字符**的标签（需识字 `is_literate`）；已贴标签的烟弹名称替换为自定义名 |

**THC 主题预装烟弹（5 种）：**

| 烟弹 | 名称 | 描述 | 内含 | 售价 |
|---|---|---|---|---|
| `/obj/item/reagent_containers/vapecart/bluekush` | Dr. Breen's Blue Kush Reserve cart（布林博士的 Blue Kush 珍藏烟弹） | "Don't smoke the carts... They put something in it... t-to make you forget! I don't even remember how I got here..."（别抽这些烟弹……他们在里面放了东西……让-让你失忆！我都不记得自己怎么来这的……） | **thc × 20 + berryjuice（莓果汁）× 10** | `PAYCHECK_LOWER` = **25** |
| `/obj/item/reagent_containers/vapecart/reddiesel` | Resistance Red Diesel cart（反抗军 Red Diesel 烟弹） | "Seems to be endorsed by a real scientist!"（似乎得到了真正的科学家背书！） | **thc × 20 + dr_gibb（Dr. Gibb 汽水）× 10** | **25** |
| `/obj/item/reagent_containers/vapecart/pwrgame` | Pwr Haze cart（Pwr Haze 烟弹） | "When did Pwr Game get into the cart business?"（Pwr Game 什么时候进军烟弹行业了？） | **thc × 20 + pwr_game（Pwr Game 饮料）× 10** | **25** |
| `/obj/item/reagent_containers/vapecart/cheese` | Cheesie Honker OG Kush cart（Cheesie Honker 的 OG Kush 烟弹） | "*Contains no real cheese."（*不含真奶酪。） | **thc × 20 + nutriment × 1 + sugar × 3** | **25** |
| `/obj/item/reagent_containers/vapecart/syndicate` | Syndi-Kush Green Crack cart（辛迪加 Syndi-Kush Green Crack 烟弹） | "Green Crack is a strain of sativa, not actual crack."（Green Crack 是苜蓿大麻的品种名，不是真的快克。） | **thc × 20 + stimulants（医用兴奋剂）× 10** | **25** |

> 全部 THC 烟弹均为 20 单位 THC + 10 单位调味剂，售价 25 信用点（廉价路边货）。

---

## 十、大麻零食与消费品

**代码**: `thc_item.dm`（124 行）

THC 食品线（图标 `icons/thcsnacks.dmi`），全部为现成消费品。

### 10.1 食物类

**哈希布朗尼**（`/obj/item/food/brownie/thc`）:

| 属性 | 值 |
|---|---|
| 名称 | hash brownie（哈希布朗尼） |
| 描述 (EN) | "A square slice of delicious, chewy brownie infused with THC. A favorite among cannabis enthusiasts." |
| 描述 (中) | 一块注入 THC 的美味耐嚼方块布朗尼，大麻爱好者的最爱。 |
| 内含 | nutriment × 5 + sugar（糖）× 3 + **thc × 10**（源码注释："These brownies are no joke." 这些布朗尼可不是闹着玩的） |

**酸苹果 THC 软糖**（`/obj/item/food/thcgummies`）:

| 属性 | 值 |
|---|---|
| 名称 | sour-apple THC gummies（酸苹果 THC 软糖） |
| 描述 (EN) | "Just a little bit too hard to chew comfortably, but with all the right flavors. This Product contains THC." |
| 描述 (中) | 稍微有点硬得咬不动，但味道全对。本产品含 THC。 |
| 咬食量 | 1 |
| 内含 | nutriment × 1 + sugar × 3 + **thc × 8** |
| 垃圾食品值 | `junkiness = 20` |
| 味道 | cannabis（大麻）×1 / sour... apple?（酸……苹果？）×1 / something sweet（甜）×1 |
| 食物类型 | FRUIT \| JUNKFOOD（"Fruity Twist" 水果口味） |
| 重量 | `WEIGHT_CLASS_SMALL` |

**THC 曲奇**（`/obj/item/food/cookie/thc`）:

| 属性 | 值 |
|---|---|
| 名称 | THC cookie（THC 曲奇） |
| 描述 (EN) | "COOKIE!!! But with a twist!"（曲奇！！！但加了个料！） |
| 咬食量 | 1 |
| 内含 | nutriment × 6 + **thc × 10** |
| 味道 | cookie（曲奇）×1 / cannabis（大麻）×1 |
| 食物类型 | GRAIN \| SUGAR（谷物/糖） |
| 食物标志 | `FOOD_FINGER_FOOD`（可手抓） |
| 重量 | `WEIGHT_CLASS_SMALL` |
| 制作复杂度 | `FOOD_COMPLEXITY_2` |

### 10.2 饮品类

**Orchard Green 汽水**（`/obj/item/reagent_containers/cup/soda_cans/thc`）:

| 属性 | 值 |
|---|---|
| 名称 | Orchard Green（果园绿） |
| 描述 (EN) | "The taste of a star in liquid form. Spiked with an orange-flavored THC blend to make the day go by a little easier." |
| 描述 (中) | 液态的星辰之味。掺入橙味 THC 混合物，让日子好过一点。 |
| 内含 | space_cola（太空可乐）× 11 + orangejuice（橙汁）× 11 + **thc × 8** |
| 饮品类型 | `SUGAR \| FRUIT \| JUNKFOOD`（糖/水果/垃圾食品） |

### 10.3 烟草类

**Crown Smoke King's Haze 香烟包**（`/obj/item/storage/fancy/cigarettes/crownhaze`）:

| 属性 | 值 |
|---|---|
| 名称 | Crown Smoke King's Haze（皇冠烟雾"王者之霾"） |
| 描述 (EN) | "Ethically sourced from the finest cannabis plants, these pre-rolls are sure to leave you feeling like royalty. Please smoke responsibly." |
| 描述 (中) | 从最优质大麻植株道德采购，这些预卷烟保证让你有王者般的感觉。请负责任地吸烟。 |
| 生成物 | `/obj/item/cigarette/rollie/thc` |

**THC 手卷烟**（`/obj/item/cigarette/rollie/thc`）: 内含 **thc × 15**。

---

## 十一、出口经济价值（Exports）

**代码**: 各物品 `.dm` 内的 `/datum/export/*` 条目（价值基准 `CARGO_CRATE_VALUE = 200`）

| 出口条目 | 物品 | 倍数 | 实际价格 |
|---|---|---|---|
| `/datum/export/heroin` | 海洛因袋（4u） | 0.5 | **100** |
| `/datum/export/heroinbrick` | 海洛因砖（20u） | 2.5 | **500** |
| `/datum/export/blacktar` | 黑焦油块（5u） | 0.4 | **80** |
| `/datum/export/crack` | 快克石（10u） | 0.5 | **100** |
| `/datum/export/crack/crackbrick` | 快克砖（40u） | 2.5 | **500** |
| `/datum/export/cocaine` | 可卡因袋（5u） | 0.4 | **80** |
| `/datum/export/cocainebrick` | 可卡因砖（25u） | 2.0 | **400** |
| `/datum/export/hash` | 大麻脂（20u） | 0.35 | **70** |
| `/datum/export/crack/hashbrick` | 大麻脂砖（80u） | 2.0 | **400** |
| `/datum/export/dab` | 大麻油（40u） | 0.4 | **80** |

**经济换算（每单位试剂价格对比）**：
- 海洛因袋：100 / 4u = **25/单位**
- 黑焦油块：80 / 5u = **16/单位**
- 快克石：100 / 10u = **10/单位**
- 可卡因袋：80 / 5u = **16/单位**
- 大麻脂：70 / 20u = **3.5/单位**
- 大麻油：80 / 40u = **2/单位**

> 源码提示："if you want money, convert it into crackbricks"（想赚钱就做成快克砖）——砖块形态单价最高（海洛因砖/快克砖 500 信用点）。

---

## 十二、快速参考总表

### 12.1 试剂总表（含阈值与成瘾）

| 试剂 | 中文名 | 过量阈值 | 成瘾 | 成瘾阈值 | 味道 | pH | 颜色 |
|---|---|---|---|---|---|---|---|
| `/datum/reagent/drug/opium` | 鸦片 | **30** | opioids | 30 | flowers | 8 | `#ffe669` |
| `/datum/reagent/drug/opium/heroin` | 海洛因 | **20** | opioids（继承） | 30 | flowers | 6 | `#ffe669` |
| `/datum/reagent/drug/opium/blacktar` | 黑焦油海洛因 | **10** | opioids（继承） | 30 | flowers | 8 | `#242423` |
| `/datum/reagent/drug/opium/blacktar/liquid` | 液态黑焦油海洛因 | — | 继承 | — | — | — | — |
| `/datum/reagent/drug/cocaine` | 可卡因 | **20** | stimulants | 40 | bitterness | 9 | `#ffffff` |
| `/datum/reagent/drug/cocaine/freebase_cocaine` | 游离碱可卡因（快克） | 20（继承） | stimulants（继承） | 40 | — | — | `#f0e6bb` |
| `/datum/reagent/drug/cocaine/powder_cocaine` | 粉末可卡因 | 20（继承） | stimulants（继承） | 40 | — | — | `#ffffff` |
| `/datum/reagent/drug/thc` | THC | **30**（仅娱乐效果） | 无 | — | skunk | 6 | `#cfa40c` |
| `/datum/reagent/drug/thc/concentrated` | 浓缩 THC | 30（继承） | 无 | — | — | — | — |
| `/datum/reagent/drug/thc/hash` | 大麻脂 | 30（继承） | 无 | — | — | — | `#cfa40c` |
| `/datum/reagent/drug/pcp` | PCP | **10**（最低） | 无 | — | rage | 8 | `#ffea2e` |
| `/datum/reagent/pcc` | PCC 前体 | — | 无 | — | satiated rage | 7.3 | `#ffea2e` |
| `/datum/reagent/drug/quaalude` | 安眠酮 | **20** | 无 | — | lemons | 8 | `#ffe669` |
| `/datum/reagent/drug/nicotine` | 尼古丁 | （核心） | nicotine | 0.01 | — | — | — |

### 12.2 合成配方总表

| 配方 | 产物 | 原料 | 温度 | 其他条件 | 标签 |
|---|---|---|---|---|---|
| `heroin` | heroin ×4 | opium ×2 + acetone ×2 | 480 | pH 8~12，纯度≥0.5 | CHEMICAL |
| `powder_heroin` | 海洛因袋 ×N | heroin ×8 | 250（冷） | 瞬时 | EASY \| CHEMICAL |
| `blacktar` | 黑焦油块 ×N | liquid blacktar ×5 | 480 | 瞬时 | EASY \| CHEMICAL |
| `powder_cocaine` | 可卡因袋 ×N | cocaine ×10 | 250（冷） | 瞬时 | EASY \| CHEMICAL |
| `freebase_cocaine` | 快克石 ×N | cocaine ×10 + water ×5 + ash ×10 | 480 | 瞬时 | EASY \| CHEMICAL |
| `hash` | 大麻脂 ×N | thc/hash ×10 | — | 瞬时 | EASY \| CHEMICAL |
| `dabs` | 大麻油 ×N | thc ×20 | 420 | 瞬时 | EASY \| CHEMICAL |
| `pcc` | pcc ×1 | cyanide ×1 + fluacid ×1 + multiver ×2 | 250 | pH 1~4，纯度≥0.5 | CHEMICAL |
| `pcp` | pcp ×1 | pcc ×1 + iron ×2 | — | 催化剂 plasma ×5 | EASY \| CHEMICAL |
| `quaalude` | quaalude ×4 | lemonjuice ×2 + hydrogen ×1 + chlorine ×1 | — | — | EASY \| DRUG |

### 12.3 制作配方总表（crafting）

| 配方 | 结果 | 原料 | 耗时 | 分类 |
|---|---|---|---|---|
| `heroinbrick` | 海洛因砖 | 海洛因袋 ×5 | 20 | CAT_CHEMISTRY |
| `crackbrick` | 快克砖 | 快克石 ×4 | 20 | CAT_CHEMISTRY |
| `cocainebrick` | 可卡因砖 | 可卡因袋 ×5 | 20 | CAT_CHEMISTRY |
| `hashbrick` | 大麻脂砖 | 大麻脂 ×4 | 20 | CAT_CHEMISTRY |
| `crackpipe` | 快克烟斗 | 电缆 ×5 + 玻璃碎片 ×1 + 金属杆 ×10 | 20 | CAT_CHEMISTRY |

### 12.4 物品总表

| 物品 | 容量 | 内含 | 拆解/用途 |
|---|---|---|---|
| `/obj/item/reagent_containers/heroin` | 4u | heroin ×4 | 鼻吸（30 tick） |
| `/obj/item/reagent_containers/heroinbrick` | 20u | heroin ×20 | 拆 5 袋 |
| `/obj/item/reagent_containers/blacktar` | 5u | blacktar ×5 | 烟斗吸食 |
| `/obj/item/reagent_containers/crack` | 10u | freebase_cocaine ×10 | 烟斗吸食 |
| `/obj/item/reagent_containers/crackbrick` | 40u | freebase_cocaine ×40 | 刀切 4 石 |
| `/obj/item/reagent_containers/cocaine` | 5u | cocaine ×5 | 鼻吸（30 tick） |
| `/obj/item/reagent_containers/cocainebrick` | 25u | cocaine ×25 | 拆 5 袋 |
| `/obj/item/reagent_containers/hash` | 20u | thc ×15 + lipolicide ×5 | 水烟筒 |
| `/obj/item/reagent_containers/hash/dabs` | 40u | 浓缩 THC ×40 | 高强度 |
| `/obj/item/reagent_containers/hashbrick` | 80u | thc ×60 + lipolicide ×20 | 拆 4 块 |
| `/obj/item/reagent_containers/vapecart` | 50u | nicotine ×50 | 电子烟 |
| `/obj/item/reagent_containers/vapecart/empty` | 50u | 空（OPENCONTAINER） | 自定义灌装/贴标 |
| 5 × THC 烟弹 | 50u | thc ×20 + 调味 ×10 | 电子烟 |
| `/obj/item/cigarette/pipe/crackpipe` | 20u（chem_volume） | 装填 crack/blacktar | 120 秒吸食 |
| `/obj/item/food/brownie/thc` | — | nutriment 5 + sugar 3 + thc 10 | 食用 |
| `/obj/item/food/thcgummies` | — | nutriment 1 + sugar 3 + thc 8 | 食用 |
| `/obj/item/food/cookie/thc` | — | nutriment 6 + thc 10 | 食用 |
| `/obj/item/reagent_containers/cup/soda_cans/thc` | — | cola 11 + oj 11 + thc 8 | 饮用 |
| `/obj/item/cigarette/rollie/thc` | — | thc ×15 | 点燃吸食 |
| `/obj/item/storage/fancy/cigarettes/crownhaze` | — | 生成 THC 手卷烟 | 香烟包 |

### 12.5 植物总表

| 种子 | 成熟 | 药效 | 产物 | 收获试剂 | 蒸馏 |
|---|---|---|---|---|---|
| `/obj/item/seeds/cocaleaf` | 8 | 20 | 古柯叶 | cocaine 0.3 + nutriment 0.15 | sins_delight（罪恶之悦） |
| `/obj/item/seeds/poppy/opiumpoppy` | — | — | 罂粟蒴果 | opium 0.3 + **fentanyl 0.075** + nutriment 0.05 | turbo（涡轮伏特加） |

---

## 十三、代码路径索引

| 内容 | 路径 |
|---|---|
| 模块根目录 | `modular_nova/modules/morenarcotics/` |
| 黑焦油物品 | `modular_nova/modules/morenarcotics/code/blacktar.dm` |
| 可卡因试剂/配方 | `modular_nova/modules/morenarcotics/code/cocaine.dm` |
| 快克/可卡因物品/出口 | `modular_nova/modules/morenarcotics/code/cocaine_item.dm` |
| 古柯叶种子/作物 | `modular_nova/modules/morenarcotics/code/cocoleaves.dm` |
| 快克烟斗 | `modular_nova/modules/morenarcotics/code/crackpipe.dm` |
| 鸦片/海洛因/黑焦油试剂与物品 | `modular_nova/modules/morenarcotics/code/opium.dm` |
| 罂粟种子/作物 | `modular_nova/modules/morenarcotics/code/opiumpoppy.dm` |
| PCP/PCC | `modular_nova/modules/morenarcotics/code/pcp.dm` |
| 安眠酮 | `modular_nova/modules/morenarcotics/code/quaalude.dm` |
| THC/大麻脂/大麻油试剂 | `modular_nova/modules/morenarcotics/code/thc.dm` |
| 大麻物品/零食/出口 | `modular_nova/modules/morenarcotics/code/thc_item.dm` |
| 电子烟烟弹 | `modular_nova/modules/morenarcotics/code/vapecarts.dm` |
| 图标资源 | `modular_nova/modules/morenarcotics/icons/`（`crack.dmi`、`mask.dmi`、`thcsnacks.dmi`） |
| 成瘾机制（阿片/兴奋剂） | `code/modules/reagents/withdrawal/generic_addictions.dm` |
| 经济常量 | `code/__DEFINES/cargo.dm`（`CARGO_CRATE_VALUE = 200`）、`code/__DEFINES/economy.dm`（`PAYCHECK_LOWER = 25`、`PAYCHECK_CREW = 50`） |
| 核心毒品试剂（THC 等原版） | `code/modules/reagents/chemistry/reagents/drug_reagents.dm` |

---

*文档生成自 TianGuan13 (NovaSector) 源码，`modular_nova/modules/morenarcotics/`（12 个 `.dm`，1,016 行）。所有数值均直接取自源码，未做推算改动。*
