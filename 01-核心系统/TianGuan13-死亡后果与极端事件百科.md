# TianGuan13 死亡后果与极端事件百科

> **项目**: TianGuan13 (Nova Sector → /tg/station)
> **代码**: `modular_nova/modules/death_consequences_perk/`（2 个 .dm，660 行）+ `modular_nova/modules/delam_emergency_stop/`（4 个 .dm，638 行）+ `modular_nova/modules/exp_corps/`（4 个 .dm，576 行）
> **范围**: 死亡后果特质（Death Consequences / 死亡衰减紊乱）+ 超物质熔毁紧急停止（Delam SCRAM 抑制系统）+ 远征军（Expeditionary Corps / 先锋远征军团）全套服装与装备
>
> **关联文档**: 管理指令用法见《管理员参考百科》；健康扫描仪改动见《医疗装备扩展百科》；作战用药品（肾上腺素/阿托品/左旋麻黄碱/万能药）见《化学系统百科》。

---

## 目录

- [第一卷 · 死亡后果特质（Death Consequences）](#第一卷--死亡后果特质death-consequences)
  - [1.1 模块总览与外部定义](#11-模块总览与外部定义)
  - [1.2 特质本体（Quirk）](#12-特质本体quirk)
  - [1.3 创伤对象与核心变量](#13-创伤对象与核心变量)
  - [1.4 衰减等级与提醒消息](#14-衰减等级与提醒消息)
  - [1.5 衰减增减机制（被动/死亡/药物）](#15-衰减增减机制被动死亡药物)
  - [1.6 效果系统（昏迷阈值与耐力伤害）](#16-效果系统昏迷阈值与耐力伤害)
  - [1.7 永久死亡（and_so_your_story_ends）](#17-永久死亡and_so_your_story_ends)
  - [1.8 健康扫描仪联动](#18-健康扫描仪联动)
  - [1.9 偏好设置全表（外部文件）](#19-偏好设置全表外部文件)
  - [1.10 玩家动词与韧性机制](#110-玩家动词与韧性机制)
- [第二卷 · 超物质熔毁紧急停止（Delam SCRAM）](#第二卷--超物质熔毁紧急停止delam-scram)
  - [2.1 模块总览与宏定义](#21-模块总览与宏定义)
  - [2.2 抑制系统机器（delam_scram）](#22-抑制系统机器delam_scram)
  - [2.3 紧急停止按钮（delam_scram button）](#23-紧急停止按钮delam_scram-button)
  - [2.4 管理指令（Admin Verbs）](#24-管理指令admin-verbs)
  - [2.5 熔毁通知与自动干预（delam.dm）](#25-熔毁通知与自动干预delamdm)
  - [2.6 配套物件与联动](#26-配套物件与联动)
- [第三卷 · 远征军（Expeditionary Corps）](#第三卷--远征军expeditionary-corps)
  - [3.1 模块总览](#31-模块总览)
  - [3.2 服装全录（clothing.dm）](#32-服装全录clothingdm)
  - [3.3 装备全录（gear.dm）](#33-装备全录geardm)
  - [3.4 远征战斧（tomahawk.dm）](#34-远征战斧tomahawkdm)
  - [3.5 远征步兵配套（expeditionary_trooper.dm）](#35-远征步兵配套expeditionary_trooperdm)
- [附录 · 代码路径索引](#附录--代码路径索引)

---

# 第一卷 · 死亡后果特质（Death Consequences）

> 模块 ID: `death_consequences`。一个高度可自定义的特质（quirk），旨在让玩家畏惧死亡，并提供 DNR 特质无法提供的更公平的死亡率。
> 来源: [Skyrat PR #23733](https://github.com/Skyrat-SS13/Skyrat-tg/pull/23733)，原作者 Niko。
> 文件: `death_consequences.dm`（87 行）+ `death_consequences_trauma.dm`（573 行）+ `readme.md`（48 行）。

## 1.1 模块总览与外部定义

**代码**: `modular_nova/modules/death_consequences_perk/readme.md`、`code/__DEFINES/~nova_defines/quirks.dm`（27 行）、`code/__DEFINES/~nova_defines/signals.dm`、`code/__DEFINES/~nova_defines/events.dm`

- **特质名（name）**: `DEATH_CONSEQUENCES_QUIRK_NAME` = **"Death Degradation Disorder"（死亡衰减紊乱）**
- **病历文本（medical_record_text / desc）**: `DEATH_CONSEQUENCES_QUIRK_DESC` = **"Patient is unusually susceptable to mortality."**（患者对死亡异常敏感）
- **图标**: `FA_ICON_DNA`（DNA 图标）
- **特质点数（value）**: **0**——"由于高度可自定义，你可以把它调得几乎无足轻重"
- **TG 文件改动**: `healthscanner.dm` 的 `/proc/healthscan()` 增加特质文本；`species_features.tsx` 用于偏好 UI
- **模块外包含文件**: `modular_nova/master_files/code/modules/client/preferences/quirks/death_consequences.dm`（全部 18 项偏好设置，见 1.9）

### 外部宏定义全表（`~nova_defines/quirks.dm`）

| 宏 | 值 | 说明 |
|---|---|---|
| `DEATH_CONSEQUENCES_QUIRK_NAME` | `"Death Degradation Disorder"` | 特质与创伤显示名 |
| `DEATH_CONSEQUENCES_QUIRK_DESC` | `"Patient is unusually susceptable to mortality."` | 病历/描述文本 |
| `DEATH_CONSEQUENCES_BASE_DEGRADATION_ON_DEATH` | **50** | 基础死亡衰减（与 DEFAULT 同值，见下） |
| `DEATH_CONSEQUENCES_MINIMUM_VICTIM_CRIT_THRESHOLD` | `(MAX_LIVING_HEALTH) - 1` = **99** | 受害者昏迷阈值可被压到的最低值（MAX_LIVING_HEALTH=100） |
| `DEATH_CONSEQUENCES_REAGENT_FLAT_AMOUNT` | `"dc_flat_reagent_amount"` | 试剂平量键（供外部试剂联动） |
| `DEATH_CONSEQUENCES_REAGENT_MULT_AMOUNT` | `"dc_mult_reagent_amount"` | 试剂倍量键 |
| `DEATH_CONSEQUENCES_REAGENT_METABOLIZE` | `"dc_reagent_should_be_metabolizing"` | 试剂代谢要求键 |
| `DEATH_CONSEQUENCES_REAGENT_CHECK_PROCESSING_FLAGS` | `"dc_check_reagent_processing_flags"` | 试剂处理旗标检查键 |
| `DEATH_CONSEQUENCES_MAXIMUM_THEORETICAL_DEGRADATION` | **10000** | 理论最大衰减值（所有数值上限） |
| `DEATH_CONSEQUENCES_DEFAULT_MAX_DEGRADATION` | **500** | 默认最大衰减（"arbitrary" 注释） |
| `DEATH_CONSEQUENCES_DEFAULT_LIVING_DEGRADATION_RECOVERY` | **0.01** | 存活时默认每秒恢复 |
| `DEATH_CONSEQUENCES_DEFAULT_DEGRADATION_ON_DEATH` | **50** | 默认死亡惩罚 |
| `DEATH_CONSEQUENCES_DEFAULT_REZADONE_DEGRADATION_REDUCTION` | **0.4** | 默认瑞佐酮每秒减衰 |
| `DEATH_CONSEQUENCES_DEFAULT_STRANGE_REAGENT_DEGRADATION_REDUCTION` | **0.25** | 默认奇异试剂减衰（预留） |
| `DEATH_CONSEQUENCES_DEFAULT_EIGENSTASIUM_DEGRADATION_REDUCTION` | **5** | 默认本征态（eigenstasium）减衰——"这么稀有的化学物，当然要给足" |
| `DEATH_CONSEQUENCES_DEFAULT_SANSUFENTANYL_DEGRADATION_REDUCTION` | **1** | 默认三舒芬太尼减衰（预留） |
| `DEATH_CONSEQUENCES_SHOW_HEALTH_ANALYZER_DATA` | `"dc_show_health_analyzer_data"` | 健康扫描仪 href 参数名 |
| `DEATH_CONSEQUENCES_TIME_BETWEEN_REMINDERS` | **5 MINUTES** | 提醒消息间隔 |

外部信号: `COMSIG_MAIN_SM_DELAMINATING`（signals.dm:43，第二卷使用，非本卷）。

## 1.2 特质本体（Quirk）

**代码**: `modular_nova/modules/death_consequences_perk/death_consequences.dm`（87 行）

### `/datum/quirk/death_consequences`

| 变量 | 值 |
|---|---|
| `name` | "Death Degradation Disorder"（死亡衰减紊乱） |
| `desc` | "Every time you die, your body suffers long-term damage that can't easily be repaired."（每次死亡，你的身体都会承受难以轻易修复的长期损伤） |
| `medical_record_text` | DEATH_CONSEQUENCES_QUIRK_DESC |
| `icon` | `FA_ICON_DNA` |
| `value` | **0** |

### 常量数据（`/datum/quirk_constant_data/death_consequences`）

- `associated_typepath = /datum/quirk/death_consequences`
- `customization_options = subtypesof(/datum/preference/numeric/death_consequences) + subtypesof(/datum/preference/toggle/death_consequences)` —— 所有数值型与开关型死亡后果偏好（共 16 数值 + 2 开关，见 1.9）都出现在特质自定义 UI 中。

### `add(client/client_source)` 挂载流程

1. 对持有人执行 `gain_trauma(/datum/brain_trauma/severe/death_consequences, TRAUMA_RESILIENCE_ABSOLUTE)` —— 以**绝对韧性**挂载创伤（无法被常规手段治愈/移除，只能靠特质移除或 aheal）。
2. 通过 `get_death_consequences_trauma()` 取得刚挂载的创伤并调用 `update_variables(client_source)` —— 从玩家的**角色偏好**读取全部参数（起始衰减、最大衰减、各种速率等）。
3. 向持有人发送红色危险提示（`span_danger`，LANG "datum.75e124ab"）。

### `remove()` 卸载流程

- `cure_trauma_type(/datum/brain_trauma/severe/death_consequences, TRAUMA_RESILIENCE_ABSOLUTE)` —— 只有移除特质才能解除创伤；绝对韧性保证医疗手段无法治愈。

### 玩家动词（/mob/verb，均设 `instant = TRUE`）

| 动词 | 名称 | 类别 | 说明 |
|---|---|---|---|
| `adjust_degradation(increment)` | **调整死亡衰减**（"Adjust Death Degradation"） | IC | 直接增减衰减值；无 mind、找不到关联创伤、`increment` 非数字时拒绝；若 `permakill_if_at_max_degradation` 且 `(current + increment) >= max` 会弹出 **tgui 确认框（7 秒超时）**，拒绝则不执行；成功执行 `linked_trauma.adjust_degradation(increment)` 并提示 |
| `refresh_death_consequences()` | **刷新死亡后果变量**（"Refresh Death Consequences Variables"） | IC | 调用 `update_variables(client)` 重新从偏好读取参数（例如改完偏好后同步） |

两个动词都做同一套 sanity 检查：`mind` 非空 → `get_death_consequences_trauma()` 非空 → 创伤 `owner` 必须等于 `mind.current`（防止借他人身体作弊）。

### `/mob/proc/get_death_consequences_trauma()`

- `RETURN_TYPE(/datum/brain_trauma/severe/death_consequences)`，返回 null 或创伤。
- 遍历 `mind.current`（`iscarbon` 时）的 `get_traumas()`，匹配 `istype(trauma, /datum/brain_trauma/severe/death_consequences)`。
- 注释说明: 该 proc 可同时对**幽灵与活体**生效，用于查找其关联创伤（挂在 mind 上而非 mob 上）。

## 1.3 创伤对象与核心变量

**代码**: `modular_nova/modules/death_consequences_perk/death_consequences_trauma.dm`（573 行，模块核心）

`/datum/brain_trauma/severe/death_consequences` — 严重级脑创伤，承载全部死亡后果逻辑。

| 基础字段 | 值 |
|---|---|
| `name` / `desc` | DEATH_CONSEQUENCES_QUIRK_NAME / DEATH_CONSEQUENCES_QUIRK_DESC |
| `scan_desc` | "death degradation"（扫描描述） |
| `gain_text` | `span_warning` "For a brief moment, you completely disassociate."（有那么一瞬间，你彻底解离了） |
| `lose_text` | `span_notice` "You feel like you have a firm grasp on your consciousness again!"（你感觉自己重新牢牢握住了意识！） |
| `random_gain` | FALSE（不随机获得） |

### 核心变量全表（默认值精确）

| 变量 | 默认值 | 说明 |
|---|---|---|
| `current_degradation` | **0** | 当前衰减值，越高越糟，永不为负 |
| `max_degradation` | **500**（`DEATH_CONSEQUENCES_DEFAULT_MAX_DEGRADATION`） | 最大衰减；若 `permakill_if_at_max_degradation` 为 TRUE 则到达即永久死亡 |
| `base_degradation_reduction_per_second_while_alive` | **0.01** | 存活时每秒恢复量 |
| `base_degradation_per_second_while_alive` | **0** | 存活时每秒自然恶化量（"想活着慢慢死，随你"） |
| `base_degradation_on_death` | **50** | 每次死亡立即惩罚量（受 2 分钟间隔限制） |
| `base_degradation_per_second_while_dead` | **0** | 死亡时每秒恶化量（可被停滞与甲醛削减） |
| `last_time_degraded_on_death` | **-2 MINUTES** | 上次死亡衰减时刻（预设 -2 分钟以避免回合开始时的 bug） |
| `time_required_between_deaths_to_degrade` | **2 MINUTES** | 两次可触发死亡衰减的间隔——防止医生反复复活 PK |
| `formaldehyde_death_degradation_mult` | **0** | 死亡时体内含甲醛 → 被动恶化量 × 此系数（默认 0 = 完全停止） |
| `rezadone_degradation_decrease` | **0.4** | 存活代谢瑞佐酮时每秒减衰量 |
| `eigenstasium_degradation_decrease` | **5** | 本征态存在时每秒减衰量 |
| `stasis_passive_degradation_multiplier` | **0** | 停滞（stasis）时恶化量 × 此系数（默认 0 = 完全停止） |
| `permakill_if_at_max_degradation` | FALSE | 到达 max 时是否 DNR + 送走受害者 |
| `force_death_if_permakilled` | FALSE | 永久死亡时是否**直接杀死**（否则只 ghostize） |
| `final_death_delivered` | FALSE | 是否已执行永久死亡 |
| `crit_threshold_min_degradation` | **0** | 开始削减昏迷阈值（crit threshold）的衰减起点 |
| `crit_threshold_max_degradation` | **200** | 削减到顶的衰减值 |
| `max_crit_threshold_reduction` | **100** | 在 `crit_threshold_max_degradation` 处削减的昏迷阈值量（会被偏好覆盖为 30，见 1.9） |
| `stamina_damage_minimum_degradation` | **100** | 开始施加耐力伤害的衰减起点 |
| `stamina_damage_max_degradation` | **500** | 耐力伤害封顶时的衰减值 |
| `max_stamina_damage` | **80** | 在 `stamina_damage_max_degradation` 处的最大耐力伤害 |
| `current_minimum_stamina_damage` | null（初始化时计算） | 当前最小耐力伤害值 |
| `crit_threshold_currently_reduced_by` | **0** | 当前已削减的昏迷阈值量（用于差值还原） |
| `time_of_last_message_sent` | `-DEATH_CONSEQUENCES_TIME_BETWEEN_REMINDERS` | 上次提醒时刻 |
| `time_between_reminders` | **5 MINUTES** | 提醒间隔 |
| `current_degradation_level` | `DEGRADATION_LEVEL_NONE` | 当前衰减等级 |
| `time_til_scan_expires` | `list()` | 健康扫描仪额外数据查看窗口（mob → 过期时间） |
| `time_to_view_extra_data_after_scan` | **5 SECONDS** | 扫描后可查看额外数据的时间窗口 |

### 静态恢复倍率表（`buckled_to_recovery_mult_table`）

被动衰减恢复量的**束缚（buckled）倍率**，按类型路径顺序匹配（越靠前越优先匹配）：

| 束缚对象 | 倍率 |
|---|---|
| `/obj/structure/bed/medical`（医疗床） | **5** |
| `/obj/structure/bed`（普通床） | **3** |
| `/obj/structure/chair/comfy`（舒适椅） | **2** |
| `/obj/structure/chair/sofa`（沙发） | **2** |
| `/obj/structure/chair`（椅子） | **1.5** |
| `/mob/living`（被背着/抬着） | **1.25** |
| 未在表中的对象（`buckled_to_default_mult`） | **1.15** |

### 生命周期

- **`on_gain()`**: 注册 `COMSIG_LIVING_POST_FULLY_HEAL`（→ `victim_ahealed`）与 `COMSIG_LIVING_RECEIVED_STAMINA_DAMAGE`（→ `on_received_stamina_damage`）两个信号；调用 `update_variables()`；加入 `SSprocessing` 每 tick 处理。
- **`on_lose(silent)`**: 将 `crit_threshold_currently_reduced_by` 归还给 `owner.crit_threshold`；停止处理；若 `final_death_delivered` 则移除 `TRAIT_DNR`；注销两个信号。
- **`Destroy()`**: 遍历 `time_til_scan_expires` 注销 `COMSIG_QDELETING` 并清空列表。

## 1.4 衰减等级与提醒消息

### 等级宏（trauma.dm 文件内定义）

| 宏 | 值 | 阈值（current/max 比值） |
|---|---|---|
| `DEGRADATION_LEVEL_NONE` | `"dc_level_none"` | 0 ～ **0.2** |
| `DEGRADATION_LEVEL_LOW` | `"dc_level_low"` | 0.2 ～ **0.4** |
| `DEGRADATION_LEVEL_MEDIUM` | `"dc_level_medium"` | 0.4 ～ **0.6** |
| `DEGRADATION_LEVEL_HIGH` | `"dc_level_high"` | 0.6 ～ **0.8** |
| `DEGRADATION_LEVEL_CRITICAL` | `"dc_level_critical"` | 0.8 ～ 1.0 |

阈值宏: `DEGRADATION_LEVEL_NONE_THRESHOLD 0.2` / `LOW_THRESHOLD 0.4` / `MEDIUM_THRESHOLD 0.6` / `HIGH_THRESHOLD 0.8`（均为 max_degradation 的比例）。

### `update_degradation_level(send_reminder_if_changed = TRUE)`

- `switch (current_degradation / max_degradation)` 按上述区间设置 `current_degradation_level`。
- 若等级改变且 `!final_death_delivered`，调用 `send_reminder(FALSE)`（不刷新冷却，保证换级必提醒）。

### 提醒消息全表（`degradation_messages`，权重制 `pick_weight`）

| 等级 | 消息（原文） | 权重 |
|---|---|---|
| LOW | "Your body aches a little."（身体微微酸痛） | 10 |
| LOW | "You feel a little detached from yourself."（感觉有点脱离自我） | 10 |
| LOW | "You feel a little tired."（感觉有点疲惫） | 10 |
| MEDIUM | "Your whole body aches..."（全身都在痛……） | 10 |
| MEDIUM | "You're starting to feel disassociated from yourself..."（开始感到与自己解离……） | 10 |
| MEDIUM | "You're having a little difficulty thinking..."（思考开始有点困难……） | 10 |
| HIGH | "Your entire body throbs!"（全身都在抽痛！） | 10 |
| HIGH | "You feel like you're losing your grip on yourself!"（感觉自己正在失去对自我的掌控！） | 10 |
| HIGH | "Your consciousness feels as fragile as a sheet of glass!"（意识脆弱如一片玻璃！） | 10 |
| HIGH | "You feel exhausted in every single possible way!"（你在每个方面都精疲力竭！） | 10 |
| CRITICAL | "**Everything hurts... It hurts so bad...**"（一切都在痛……痛得如此厉害……） | 10 |
| CRITICAL | "**It's so hard to think... It's so hard... So hard...**"（思考好难……好难……好难……） | 10 |
| CRITICAL | "**Your body feels alien, like you don't belong in it...**"（身体陌生得像不属于自己……） | 10 |
| CRITICAL | "**... Who am I?**"（……我是谁？） | **1** |
| CRITICAL | "**... Where am I?**"（……我在哪？） | **1** |
| CRITICAL | "**... What am I?**"（……我是什么？） | **1** |

`send_reminder(update_cooldown = TRUE)`: 从 `degradation_messages[current_degradation_level]` 中 `pick_weight` 抽取一条发给 owner；找不到消息列表/消息则跳过；默认刷新 `time_of_last_message_sent = world.time`。

## 1.5 衰减增减机制（被动/死亡/药物）

### `process(seconds_per_tick)` 每 tick 主循环

1. 若持有 `TRAIT_GODMODE` 直接返回（无敌者不衰减）。
2. `is_dead = (owner.stat == DEAD)`。
3. `degradation_increase = get_passive_degradation_increase(is_dead) * seconds_per_tick`。
4. `degradation_reduction = get_passive_degradation_decrease(is_dead) * seconds_per_tick`。
5. `adjust_degradation(increase - reduction)`。
6. 未死亡时调用 `damage_stamina(seconds_per_tick)`（保证耐力不低于最小阈值）。
7. 距上次提醒超过 `time_between_reminders`（5 分钟）时 `send_reminder()`。

### `get_passive_degradation_increase(is_dead)` — 每秒恶化量

- **死亡时**: 基础 `+ base_degradation_per_second_while_dead`；若体内含**甲醛**（`has_reagent(/datum/reagent/toxin/formaldehyde, needs_metabolizing = FALSE)` 且 `reagent_process_flags_valid`）→ 恶化量 **× `formaldehyde_death_degradation_mult`**（默认 0，即死亡+甲醛=衰减完全冻结）。
- **存活时**: `+ base_degradation_per_second_while_alive`（默认 0）。
- 任何情况下持有 `TRAIT_STASIS` → 恶化量 **× `stasis_passive_degradation_multiplier`**（默认 0，停滞=完全冻结）。

### `get_passive_degradation_decrease(is_dead)` — 每秒恢复量

- **存活时**: 若 `base_degradation_reduction_per_second_while_alive > 0` → `+ 0.01`（默认）。
- **瑞佐酮**: `has_reagent(/datum/reagent/medicine/rezadone, needs_metabolizing = TRUE)`（需正在代谢）且 **purity ≥ `DEATH_CONSEQUENCES_REZADONE_MINIMUM_PURITY`（100）** 且处理旗标合法 → `+ rezadone_degradation_decrease`（默认 0.4）。纯度门槛是为防止 Borg 合成器产出低纯度瑞佐酮滥用。
- **本征态（eigenstate）**: 体内含 `/datum/reagent/eigenstate`（不要求代谢中）→ `+ eigenstasium_degradation_decrease`（默认 5）。
- 最终 `× get_passive_degradation_decrease_mult()`。

### `get_passive_degradation_decrease_mult()` — 恢复倍率

| 条件 | 倍率 |
|---|---|
| 睡眠中（`IsSleeping()`） | **×3**（`DEGRADATION_REDUCTION_SLEEPING_MULT`） |
| 休息中（`resting`） | **×1.5**（`DEGRADATION_REDUCTION_RESTING_MULT`） |
| 束缚倍率 | 按 1.3 表（医疗床 ×5 … 默认 ×1.15），未束缚 ×1 |

### `on_death()` — 死亡惩罚

- 仅当 `base_degradation_on_death != 0` 时触发。
- **2 分钟冷却**: 若 `!last_time_degraded_on_death || (world.time - time_required_between_deaths_to_degrade) <= last_time_degraded_on_death` 则跳过（回合开始时 last = -2 MINUTES 恰好处于冷却中，避免开局即衰减）。
- 通过冷却 → `adjust_degradation(base_degradation_on_death)`（默认 +50）。
- 若尚未永久死亡（避免与永久死亡消息重复）: 周围人看到 `span_revenwarning` "\[owner] writhes for a brief moment, before going limp. You get the sense that you might want to **prevent them from dying again...**"（短暂抽搐后瘫软。你有种感觉：最好**别再让他们死一次**……）；本人（幽灵时发给幽灵）收到 "As your mind reels from the shock of death, you feel the ethereal tether that binds you to your body strain..."（死亡的冲击令你意识恍惚，你感到束缚你与肉体的无形之绳绷紧了……）。
- `last_time_degraded_on_death = world.time`。

### `adjust_degradation(adjustment)` — 唯一写入入口

- `current_degradation = clamp(current + adjustment, 0, max_degradation)`。
- 值变化时依次调用 `update_degradation_level()` 与 `update_effects()`。

## 1.6 效果系统（昏迷阈值与耐力伤害）

### `update_effects()`

1. `threshold_adjustment = get_crit_threshold_adjustment()`；`owner.crit_threshold = (owner.crit_threshold - crit_threshold_currently_reduced_by) + threshold_adjustment`；记录 `crit_threshold_currently_reduced_by`。
2. 若 `permakill_if_at_max_degradation && current_degradation >= max_degradation` → `and_so_your_story_ends()`。

### `get_crit_threshold_adjustment()` — 昏迷阈值削减公式（SHOULD_BE_PURE）

```
clamped_degradation = clamp(current - crit_threshold_min_degradation, 0, crit_threshold_max_degradation)
percent_to_max      = clamped_degradation / crit_threshold_max_degradation
proposed_alteration = max_crit_threshold_reduction * percent_to_max
proposed_threshold  = (owner.crit_threshold - crit_threshold_currently_reduced_by) + proposed_alteration
overflow            = max(proposed_threshold - DEATH_CONSEQUENCES_MINIMUM_VICTIM_CRIT_THRESHOLD(99), 0)
final_alteration    = proposed_alteration - overflow   // 永不把昏迷阈值压到 99 以下
```

即: 默认偏好下衰减达到 max（500）时昏迷阈值削减 **30**（`max_crit_threshold_reduction` 偏好默认值），且最低被压到 **99**（MAX_LIVING_HEALTH 100 − 1）。

### `damage_stamina(seconds_per_tick)` — 耐力保底

- `stamina_damage_max_degradation` 为 0 或 `victim_properly_resting()`（休息/睡眠/束缚于舒适物）时直接返回。
- `clamped = clamp(current - stamina_damage_minimum_degradation, 0, stamina_damage_max_degradation)`；`percent = min(clamped / stamina_damage_max_degradation, 1)`；`current_minimum_stamina_damage = max_stamina_damage * percent`（默认偏好下从衰减 100 起步、500 封顶、最大 80）。
- 若当前耐力损失 > 最小值 + 1 → 不管（高于保底线）；若在 ±1 范围内 → `adjust_stamina_loss(0)` 仅重置耐力恢复计时器；否则 `adjust_stamina_loss(最小值 − 当前损失)` 拉回保底线（用 adjust 而非 set，以兼容恢复计时器）。
- 注释: 因衰减持续恢复会使最小值持续下降，必须检查区间而非只判断"高于最小值"。

### `on_received_stamina_damage`（信号 `COMSIG_LIVING_RECEIVED_STAMINA_DAMAGE`）

- `SIGNAL_HANDLER`；当 `current_level <= current_minimum_stamina_damage` 时返回 `COMPONENT_LIVING_BLOCK_STAMINA_REGEN_TIMER`（阻止耐力恢复计时器启动，使耐力被锁在保底线）。

### `victim_properly_resting()`

- `owner.resting || owner.IsSleeping()` → TRUE；束缚对象命中恢复倍率表 → TRUE。

## 1.7 永久死亡（and_so_your_story_ends）

**触发条件**: `permakill_if_at_max_degradation = TRUE` 且 `current_degradation >= max_degradation`。

### 执行流程

1. `ADD_TRAIT(owner, TRAIT_DNR, TRAUMA_TRAIT)` —— 添加 **DNR 特质**（不得复活）；`final_death_delivered = TRUE`。
2. **按状态分支**:
   - **已死亡（stat == DEAD）**: 周围可见 "The air around [owner] seems to ripple for a moment."（周围的空气泛起一阵涟漪）；本人/幽灵收到 "The metaphorical \"tether\" binding you to your body finally gives way..."（隐喻的"绳索"终于断裂，你坠入深邃黑暗的深渊……）；日志 "has been permanently ghosted by their resonance instability quirk."。
   - **存活 + `force_death_if_permakilled = TRUE`**（暴力痛苦的死法）: 周围可见 "[owner] suddenly lets out a harrowing gasp and falls to one knee, clutching their head! The remainder of their body goes limp soon after, failing to stand back up."（发出一声骇人的喘息，单膝跪地抱头，随后身体瘫软再也站不起来）；执行 `owner.death(gibbed = FALSE)`；日志 "has been permanently killed by their resonance instability quirk."。
   - **存活 + 非 force_death**（只是永远停止思考）: 周围可见 "[owner] jerkily arches their head upwards, untensing and going slackjawed with dilated pupils. They cease all action and simply stand there, swaying."（头猛地仰起，瞳孔散大、下颌松弛，停止一切动作只是站着摇晃）；执行 `owner.ghostize(can_reenter_corpse = FALSE)`（无法再回到尸体）；日志 "has been permanently ghosted by their resonance instability quirk."。
3. 本人消息（任一分支）: "Your mind suddenly clouds, and you lose control of all thought and function..."（意识突然蒙上迷雾，失去一切思维与机能……）。
4. 追加可见消息 " **You sense something terrible has happened.**"（你感觉到有可怕的事发生了）；本人追加 " You have been killed by your death degradation, which prevents you from returning to your body or even being revived. You may roleplay this however you wish - this death may be temporary, permanent - you may or may not appear in soulcatchers - it's all up to you."（你已被死亡衰减杀死，无法回到身体也无法被复活。你可以自由扮演此死亡——暂时或永久、是否出现在灵魂捕捉器——都由你决定）。
5. `owner.investigate_log(log_message)`；发送可见消息（排除本人/幽灵）；`balloon_alert_to_viewers`；向本人/幽灵发送最终消息。

### `victim_ahealed(datum/signal_source, heal_flags)` — 管理员逆转

信号: `COMSIG_LIVING_POST_FULLY_HEAL`（admin aheal 时触发）。**永久死亡只能由管理员 aheal 逆转**。

- `heal_flags` 含 `HEAL_AFFLICTIONS` → `adjust_degradation(-INFINITY)`（衰减清零，"一根再生提取物就能治好你"）。
- `heal_flags` 含 `ADMIN_HEAL_ALL` → `final_death_delivered = FALSE` 并 `REMOVE_TRAIT(owner, TRAIT_DNR, TRAUMA_TRAIT)`（"但只有神才能真的复活你"）。

## 1.8 健康扫描仪联动

### `get_health_analyzer_link_text(mob/user)` — 扫描链接文本

- 基础: `span_bolddanger` "**Subject suffers from death degradation disorder.**"（受检者患有死亡衰减紊乱）。
- 已永久死亡（`final_death_delivered`）: 追加紫色斜体 "Neural patterns are equivalent to the consciousness zero-point. Subject has likely succumbed."（神经模式等同于意识零点，受检者很可能已消亡）并直接返回。
- 正常: "Current degradation/max: **X**/**Y**."；追加 href `byond://?src=[REF(src)];[DEATH_CONSEQUENCES_SHOW_HEALTH_ANALYZER_DATA]=1`（"View degradation specifics?" 查看衰减详情）；若 `permakill_if_at_max_degradation` 追加 revenwarning "**SUBJECT WILL BE PERMANENTLY KILLED IF DEGRADATION REACHES MAXIMUM!**"（衰减达到上限受检者将被永久杀死！）。
- 记录 `time_til_scan_expires[user] = world.time + 5 SECONDS`，并注册 `COMSIG_QDELETING`（→ `scanning_user_qdeleting` 清理条目）。

### `Topic(href, href_list)`

- `DEATH_CONSEQUENCES_SHOW_HEALTH_ANALYZER_DATA` 命中时: `world.time <= time_til_scan_expires[usr]` → `to_chat(boxed_message(get_specific_data()), type = MESSAGE_TYPE_INFO)`；过期则警告。

### `get_specific_data()` — 完整详情报告（逐条对应变量）

| 条目 | 内容 |
|---|---|
| 标题 | "Subject suffers from death degradation disorder." |
| 当前/最大 | Current degradation/max: **X**/**Y** |
| 存活变化率 | 若恢复或恶化非零: "While alive, subject will **recover from / suffer** degradation at a rate of **N per second**." |
| 复合明细 | 若恢复与恶化同时非零: 注明由恶化速率 N 与恢复速率 N 复合而成 |
| 死亡恶化率 | 若非零: "While dead, subject will suffer degradation at a rate of **N per second**." |
| 甲醛系数 | 若为有机体（`reagent_flags & PROCESS_ORGANIC`）且 mult ≠ 1: "formaldehyde will alter the degradation by **N**x." |
| 停滞提示 | 若 `stasis_passive_degradation_multiplier < 1`: "Stasis may be effective in slowing (or even stopping) degradation." |
| 死亡惩罚 | 若非零: "Death will incur a **N** degradation penalty." |
| 瑞佐酮 | 若为有机体且减衰非零: "Rezadone of purity at or above **100**% will reduce degradation by **N** per second when metabolized." |
| 本征态 | 若减衰非零: "Eigenstasium will reduce degradation by **N** per second when present." |
| 加速提示 | "All degradation reduction can be **expedited** by **resting, sleeping, or being buckled to something comfortable**." |
| 永久死亡警告 | 若开启: "**SUBJECT WILL BE PERMANENTLY KILLED IF DEGRADATION REACHES MAXIMUM!**" |

## 1.9 偏好设置全表（外部文件）

**代码**: `modular_nova/master_files/code/modules/client/preferences/quirks/death_consequences.dm`（模块外包含文件）

基类 `/datum/preference/numeric/death_consequences`: `abstract_type`、`category = PREFERENCE_CATEGORY_MANUALLY_RENDERED`、`savefile_identifier = PREFERENCE_CHARACTER`（按角色存档）、`step = 0.01`；`apply_to_human()` 恒返回 FALSE（不直接作用肉体，仅供创伤读取）。

### 数值型偏好（16 项，default = 无偏好时的值）

| 偏好类型路径 | savefile_key | 最小值 | 最大值 | 默认值 |
|---|---|---|---|---|
| `starting_degradation` | `dc_starting_degradation` | 0 | 10000 | **0** |
| `max_degradation` | `dc_max_degradation` | 0 | 10000 | **500** |
| `living_degradation_recovery_per_second` | `dc_living_degradation_recovery_per_second` | 0 | 1000 | **0.01** |
| `living_degradation_per_second` | `dc_living_degradation_per_second` | 0 | 1000 | **0** |
| `dead_degradation_per_second` | `dc_dead_degradation_per_second` | 0 | 1000 | **0** |
| `degradation_on_death` | `dc_degradation_on_death` | 0 | 1000 | **50** |
| `formeldahyde_dead_degradation_mult` | `dc_formeldahyde_dead_degradation_mult` | 0 | 1 | **0** |
| `stasis_dead_degradation_mult` | `dc_stasis_dead_degradation_mult` | 0 | 1 | **0** |
| `rezadone_living_degradation_reduction` | `dc_rezadone_living_degradation_reduction` | 0 | 500 | **0.4** |
| `eigenstasium_degradation_reduction` | `dc_eigenstasium_degradation_reduction` | 0 | 5000 | **5** |
| `crit_threshold_reduction_min_percent_of_max` | `dc_crit_threshold_reduction_min_percent_of_max` | 0 | 100 | **0**（%） |
| `crit_threshold_reduction_percent_of_max` | `dc_crit_threshold_reduction_percent_of_max` | 0 | 100 | **100**（%） |
| `max_crit_threshold_reduction` | `dc_max_crit_threshold_reduction` | 0 | 100（MAX_LIVING_HEALTH） | **30** |
| `stamina_damage_min_percent_of_max` | `dc_stamina_damage_min_percent_of_max` | 0 | 100 | **20**（%） |
| `stamina_damage_percent_of_max` | `dc_stamina_damage_percent_of_max` | 0 | 100 | **100**（%） |
| `max_stamina_damage` | `dc_max_stamina_damage` | 0 | 150 | **80** |

### 开关型偏好（2 项）

| 偏好类型路径 | savefile_key | 默认值 |
|---|---|---|
| `permakill_at_max` | `dc_permakill_at_max` | **FALSE**（"别太残忍"） |
| `force_death_if_permakilled` | `dc_force_death_if_permakilled` | **FALSE** |

### `update_variables(client/source = owner.client)` 读取映射

- `max_degradation`、`current_degradation = clamp(starting_degradation, 0, max - 1)`（**不允许开局即满衰减暴毙**）。
- 存活恢复/存活恶化/死亡恶化/死亡惩罚四项速率直接读取。
- `crit_threshold_min_degradation = max × min_percent / 100`（默认 500×0% = **0**）；`crit_threshold_max_degradation = max × max_percent / 100`（默认 500×100% = **500**）；`max_crit_threshold_reduction` 直接读取（默认 **30**）。
- `stamina_damage_minimum_degradation = max × 20% = 100`；`stamina_damage_max_degradation = max × 100% = 500`；`max_stamina_damage` 直接读取（默认 **80**）。
- `permakill_if_at_max_degradation` / `force_death_if_permakilled` 两个开关。
- `rezadone_degradation_decrease`（0.4）、`eigenstasium_degradation_decrease`（5）。
- 注意: 偏好的 `formeldahyde_dead_degradation_mult` 与 `stasis_dead_degradation_mult` **未被 update_variables 读取**，创伤使用其默认值 0。
- `current_minimum_stamina_damage` 首次初始化（同 damage_stamina 公式）。
- 结尾调用 `update_effects()`。
- sanity 检查: `source` 非空、`ckey(owner.mind?.key) == source.ckey`（防止 aghost 后 owner.client 为 null 而误操作他人）。

## 1.10 玩家动词与韧性机制

- **韧性**: 创伤以 `TRAUMA_RESILIENCE_ABSOLUTE` 挂载——除移除特质外不可治愈、不可用脑部手术清除；`lose_text` 只在特质移除时出现。
- **verb 权限**: 两个 IC 动词（`adjust_degradation` / `refresh_death_consequences`）均要求 `mind` 非空、创伤存在且 `trauma_holder == mind.current`。
- **GODMODE**: `process()` 对 `TRAIT_GODMODE` 持有者完全跳过。
- **与 DNR 的区别**: 本模块的"永久死亡"是可选（`permakill_at_max` 默认关）、可调、可被 aheal 逆转的；而 DNR 特质是不可逆的硬规则。

---

# 第二卷 · 超物质熔毁紧急停止（Delam SCRAM）

> 模块 ID: `DELAM_SCRAM`。为超物质引擎添加"紧急停止"（Suppression System）——在回合开始前 30 分钟内可操作，让工程部在搞砸时不必等管理员删水晶。
> 来源: [Skyrat PR #22145](https://github.com/Skyrat-SS13/Skyrat-tg/pull/22145)，作者 LT3。
> 文件: `code/scram.dm`（499 行）+ `code/admin_scram.dm`（64 行）+ `code/delam.dm`（62 行）+ `code/supermatter_replacement.dm`（13 行）+ `README.md`（43 行）+ `icons/scram.dmi`。

## 2.1 模块总览与宏定义

### 外部定义

| 文件 | 定义 | 值 |
|---|---|---|
| `code/__DEFINES/~nova_defines/signals.dm`（第 43 行） | `COMSIG_MAIN_SM_DELAMINATING` | `"delam_time"`（主超物质熔毁全局信号） |
| `code/__DEFINES/~nova_defines/events.dm`（第 16 行） | `DIVINE_INTERVENTION` | **3**（管理员干预触发原因） |

### TG Proc/File 改动（NOVA EDIT 标记）

- `code/modules/power/supermatter/supermatter.dm` → `/obj/machinery/power/supermatter_crystal/proc/count_down`: 当 `is_main_engine` 时 `SEND_GLOBAL_SIGNAL(COMSIG_MAIN_SM_DELAMINATING, final_countdown)`（final_countdown = TRUE = 1，等价 `AUTOMATIC_SAFETIES`）。
- `code/modules/power/supermatter/supermatter_delamination/_sm_delam.dm` → `/datum/sm_delam/proc/delam_progress`: 每次熔毁推进时调用 `notify_delam_suppression(sm)`（伤害 > warning_point 时）。

### scram.dm 本地宏全表

| 宏 | 值 | 说明 |
|---|---|---|
| `SM_PREVENT_EXPLOSION_THRESHOLD` | **100** | 干预后水晶伤害被压到的上限 |
| `SM_COOLING_MIXTURE_MOLES` | **16000** | 机器内预装制冷混合物（氟利昂 freon）摩尔数 |
| `SM_COOLING_MIXTURE_TEMP` | **170**（K） | 制冷混合物温度 |
| `DAMAGED_SUPERMATTER_COLOR` | 4×5 色彩矩阵 `list(1,0.1,0.2,0, 0,0.9,0.1,0, 0.1,-0.05,0.85,0, 0,0,0,0.9, 0,0,0,0)` | 受损超物质的红/绿/蓝调色 |
| `MISTAKES_WERE_MADE` | **0** | "犯了错"（内部能量清零用） |
| `MANUAL_INTERVENTION` | **0** | 手动干预触发原因（按钮） |
| `AUTOMATIC_SAFETIES` | **1** | 自动安全触发原因（= TRUE = count_down 信号） |
| `BUTTON_PUSHED` | **0** | 按钮已按下（终态） |
| `BUTTON_IDLE` | **1** | 按钮初始态 |
| `BUTTON_AWAKE` | **2** | 按钮已"唤醒"（第一下点击后） |
| `BUTTON_ARMED` | **3** | 按钮已武装（第二下点击后） |
| `SM_DAMAGED_EXPLOSION_POWER` | **41** | 干预后水晶的爆炸威力（"再搞砸一次，哎哟"） |
| `SHATTER_DEVASTATION_RANGE` | **0** | 窗破爆炸毁灭半径 |
| `SHATTER_HEAVY_RANGE` | **0** | 窗破爆炸重伤害半径 |
| `SHATTER_LIGHT_RANGE` | **0** | 窗破爆炸轻伤害半径 |
| `SHATTER_FLAME_RANGE` | **3** | 窗破爆炸火焰半径 |
| `SHATTER_FLASH_RANGE` | **5** | 窗破爆炸闪光半径 |
| `SHATTER_MIN_TIME` | **17 SECONDS** | 窗户破碎最短延迟 |
| `SHATTER_MAX_TIME` | **19 SECONDS** | 窗户破碎最长延迟 |
| `EVAC_WARNING_TIMER` | **3 SECONDS** | 警告后开演（put_on_a_show）延迟 |
| `POWER_CUT_MIN_DURATION_SECONDS` | **21** | 停电最短时长（秒） |
| `POWER_CUT_MAX_DURATION_SECONDS` | **23** | 停电最长时长（秒） |
| `AIR_INJECT_RATE` | **175** | 机器气体注入速率（L/s，volume_rate） |
| `BUTTON_SOUND_RANGE` | **7** | 按钮音效范围 |
| `BUTTON_SOUND_FALLOFF_DISTANCE` | **7** | 按钮音效衰减距离 |
| `MACHINE_SOUND_RANGE` | **15** | 机器音效范围 |
| `MACHINE_RUMBLE_SOUND_RANGE` | **30** | 机器轰鸣音效范围 |
| `MACHINE_SOUND_FALLOFF_DISTANCE` | **10** | 机器音效衰减距离 |

## 2.2 抑制系统机器（delam_scram）

**代码**: `modular_nova/modules/delam_emergency_stop/code/scram.dm`

`/obj/machinery/atmospherics/components/unary/delam_scram` — 一台使用极冷空气尝试紧急关闭超物质引擎的大气设备（"解离抑制系统" / delamination suppression system）。

### 基础属性

| 属性 | 值 |
|---|---|
| `icon` / `icon_state` | `modular_nova/modules/delam_emergency_stop/icons/scram.dmi` / `"dispenser-idle"` |
| `name` | "\improper delamination suppression system"（解离抑制系统） |
| `desc` | Nakamura Engineering 最新款解离抑制系统；"你不想在它启动时待在反应室里！<br>说实话，CentCom 宁愿你根本别启动它。<br>这玩意儿很贵的！" |
| `use_power` | `IDLE_POWER_USE` |
| `idle_power_usage` | `BASE_MACHINE_IDLE_CONSUMPTION * 4` |
| `can_unwrench` | FALSE（喜剧选项：试图拧走它会把你自己扔进水晶变成灰） |
| `shift_underlay_only` / `hide` | FALSE / TRUE |
| `piping_layer` | `PIPING_LAYER_MAX` |
| `pipe_flags` | NONE；`pipe_state` = `"injector"` |
| `resistance_flags` | `FIRE_PROOF \| FREEZE_PROOF \| UNACIDABLE` |

### 关键变量

| 变量 | 默认值 | 说明 |
|---|---|---|
| `volume_rate` | `AIR_INJECT_RATE` = **175**（L/s） | 设备运行速率 |
| `my_sm` | null | 指向主超物质的 weakref |
| `radio` / `radio_key` | 内置无线电 / `/obj/item/encryptionkey/headset_eng`（工程耳机密钥） | 内置对讲机（`set_listening(FALSE)`，只发不听） |
| `emergency_channel` | **null**（公共频道 Common） | 紧急广播频道 |
| `warning_channel` | `RADIO_CHANNEL_ENGINEERING` | 警告频道 |
| `admin_disabled` | FALSE | 是否被管理员禁用（"有人真的想让 SM 爆炸"） |
| `station_notified` | FALSE（定义于 delam.dm，挂在 supermatter_crystal 上） | 是否已通知全站 |

### 初始化与生命周期

- **`Initialize(mapload)`（第一段）**: 返回 `INITIALIZE_HINT_LATELOAD`。
- **`Initialize(mapload)`（第二段，第 253 行起）**: 构造 `delam_juice` 气体混合物——**氟利昂（freon）16000 摩尔，温度 170K**，写入 `airs[1]`（这就是机器的"弹药"）。
- **`post_machine_initialize()`**: `id_tag` 为空时设为 `"SCRAM"`；创建无线电并装工程密钥、关闭监听、重算频道；`marry_sm()`（`my_sm = WEAKREF(GLOB.main_supermatter_engine)`）；`RegisterSignal(SSdcs, COMSIG_MAIN_SM_DELAMINATING, PROC_REF(panic_time))`。
- **`Destroy()`**: `QDEL_NULL(radio)`、`my_sm = null`。
- **`update_icon_nopipes()`**: 直接 return（不绘制管道）。

### `process_atmos()` — 大气注入（与 outlet_injector 功能相同，但为无法篡改控制的一体化加固单元）

1. `!on || !is_operational` 直接返回。
2. 所在 turf 为封闭地形（`isclosedturf`）返回。
3. `air_contents = airs[1]`；`temperature > 0` 时:
   - `transfer_moles = (air_contents.return_pressure() * volume_rate) / (air_contents.temperature * R_IDEAL_GAS_EQUATION)`
   - `air_contents.remove(transfer_moles)` → `location.assume_air(removed)` → `update_parents()`。

即: 每秒把 175 L/s 体积流量对应的极冷氟利昂灌入周围大气，为水晶降温。

### 触发链路

**`panic_time(source, trigger_reason)`**（信号处理器）: `prereq_check()` 通过后 → `send_warning(source, trigger_reason)`。

**`prereq_check(source, trigger_reason)`** — 前置检查:

| 条件 | 结果 |
|---|---|
| `on == TRUE`（已启动过） | 拒绝 |
| `admin_disabled == TRUE` | 拒绝；investigate_log "Delam SCRAM tried to activate but an admin disabled it"；播放 `compiler-failure.ogg`（100 音量，范围 15）；无线电在工程频道广播故障（`_radio.delam_fault`）；`audible_message` 危险提示 |
| 回合时间 > **60 MINUTES** 且 `trigger_reason != DIVINE_INTERVENTION` | 拒绝；同样的失败音效 + 危险提示 + investigate_log "signal was received but failed precondition check. (Round time or trigger reason)" |

**`send_warning(source, trigger_reason)`**:

- `DIVINE_INTERVENTION`（管理员）: investigate_log "was activated by admin intervention"；`notify_ghosts` 标题 **"Divine Intervention"（神之干预）**，音效 `warning-buzzer.ogg`，音量 75。
- 其他: 按触发原因取文案——`AUTOMATIC_SAFETIES` → "automatic safeties"（自动安全），`MANUAL_INTERVENTION` → "manual intervention"（手动干预）；`notify_ghosts` 标题 **"Mistakes Were Made"（错误已成）**，同样音效/音量。
- 无线电在**公共频道**广播启动消息（`_radio.delam_firing`）。
- `addtimer(put_on_a_show, EVAC_WARNING_TIMER = 3 SECONDS)`（"以力对力"）。
- 播放 `bloblarm.ogg`（100 音量，轰鸣范围 30）。
- `INVOKE_ASYNC(power_fail, 23.3, 23.3)` —— 全局停电约 **23.3 秒**（`EVAC_WARNING_TIMER/10 + POWER_CUT_MAX_DURATION_SECONDS = 0.3 + 23`）。

**`put_on_a_show()`** — 烟花开场:

1. 解析 `my_sm`，无水晶则返回。
2. `on = TRUE`（一次性使用）；`SSpersistence.reset_delam_counter()`；播放 `earth_rumble_distant3.ogg` 环境音（override_volume）；`update_appearance()`。
3. 水晶外观: `modify_filter("ray", color = SUPERMATTER_TESLA_COLOUR)`；`color = DAMAGED_SUPERMATTER_COLOR`；`set_light_color(SUPERMATTER_TESLA_COLOUR)`；`update_appearance()`（"逃得过熔毁，逃不过双关语"）。
4. **停用反应室内通风**: 半径 3 格内所有 `vent_scrubber` 与 `vent_pump` 全部 `on = FALSE` + `update_appearance()`（别把制冷气体排走）。
5. **粉碎强化等离子窗**: 半径 3 格内 `/obj/structure/window/reinforced/plasma` 在 **17～19 秒随机延迟**后 `shatter_window()`。
6. `addtimer(prevent_explosion, 9 SECONDS)` —— 让气体工作几秒冷却水晶；若伤害已不可救，也回一点血。

**`shatter_window()`**（强化等离子窗 proc）: `visible_message`（危险提示）+ `explosion(src, 0, 0, 0, 3, 5)`（毁灭 0 / 重 0 / 轻 0 / 火焰 3 / 闪光 5）+ `qdel(src)`。

**`prevent_explosion()`** — 收尾:

- 水晶改名 **"partially delaminated supermatter crystal"（部分解离的超物质水晶）**，描述改为 "This crystal has seen better days, the glow seems off and the shards look brittle. Central says it's still \"relatively safe.\" They'd never lie to us, right?"（这水晶风光不再，光芒黯淡、碎片易碎。中央说它仍"相对安全"。他们不会骗我们的，对吧？）
- `explosion_power = SM_DAMAGED_EXPLOSION_POWER`（**41**）——再炸就没这么温柔了。
- `damage > 100` → 压回 **100**（`SM_PREVENT_EXPLOSION_THRESHOLD`）。
- `internal_energy = MISTAKES_WERE_MADE`（**0**）。
- 半径 3 格内所有特斯拉线圈（`tesla_coil`）`stored_energy = 0`。

**`goodbye_friends()`** — 壮烈自毁: 播放 `compiler-failure.ogg`；`visible_message` 危险提示 + 盲人消息 "[src] beeps a sorrowful melody!"（奏响哀伤的旋律）；`deconstruct(FALSE)`。**一次性设备，用后即毁**。

**映射辅助**: `MAPPING_DIRECTIONAL_HELPERS(/obj/machinery/atmospherics/components/unary/delam_scram, 0)`。

## 2.3 紧急停止按钮（delam_scram button）

**代码**: `modular_nova/modules/delam_emergency_stop/code/scram.dm`

`/obj/machinery/button/delam_scram` — "一个可以砸下去停止超物质引擎的大红按钮，多么诱人！"

### 基础属性

| 属性 | 值 |
|---|---|
| `name` | "\improper supermatter emergency stop button"（超物质紧急停止按钮） |
| `desc` | "Your last hope to try and save the crystal during a delamination.<br>While it is indeed a big red button, pressing it outside of an emergency will probably get the engineering department out for your blood."（熔毁期间拯救水晶的最后希望；虽然它确实是个大红按钮，非紧急情况下按它大概会被工程部追杀） |
| `can_alter_skin` | FALSE（不可改皮肤） |
| `silicon_access_disabled` | TRUE（AI/机器人不可按） |
| `resistance_flags` | `FREEZE_PROOF \| LAVA_PROOF \| FIRE_PROOF \| UNACIDABLE \| ACID_PROOF` |
| `use_power` | `NO_POWER_USE`（不耗电） |
| `light_color` / `light_power` | `LIGHT_COLOR_INTENSE_RED`（亮红）/ **0.7** |
| `button_stage` | `BUTTON_IDLE`（1） |
| `radio` / `radio_key` | 内置无线电 / 工程耳机密钥 |
| 冷却 | `COOLDOWN_DECLARE(scram_button)`（武装后 15 秒） |

### 抗干扰

- `screwdriver_act` → `TRUE`（螺丝刀无法拆解）。
- `emag_act` → `return`（**电磁卡免疫**）。

### `attack_hand` 点击流程（按状态机推进）

1. `machine_stat & BROKEN` → 无响应。
2. 冷却未结束（`!COOLDOWN_FINISHED(scram_button)`）→ `balloon_alert` 提示。
3. **`validate_suppression_status()` 失败**（机器不存在/已被用/有多个）→ 播放 `buzz-sigh.ogg`（50 音量，范围 7）+ 危险提示 "Look through the window, buddy"（往窗外看看，伙计）+ **`burn_out()`**（按钮自毁）。
4. **`BUTTON_IDLE`（第一次点击）**: 发放**操作说明纸条** `/obj/item/paper/paperslip/corporate/fluff/delam_procedure`（"就给你一张！弄丢了就好好反省你的工程水平"）；`button_stage = BUTTON_AWAKE`；返回。
5. **`BUTTON_AWAKE`（第二次点击）**: 启动 15 秒冷却（`COOLDOWN_START`）；**回合时间 > 60 分钟** → 播放失败音 + 危险提示 + `burn_out()`（"只有开局阶段才免费，之后自求多福"）；否则 → `button_stage = BUTTON_ARMED` + `update_appearance()` + 无线电在**工程频道**广播 "SUPERMATTER EMERGENCY STOP BUTTON ARMED!"（按钮已武装）+ 可见危险提示 + `message_admins("[ADMIN_LOOKUPFLW(user)] just uncovered [src].")` + investigate_log → `confirm_action(user)`。
6. 其他状态（如已 PUSHED）→ 返回。

### `confirm_action(user)` — 最终确认

- `tgui_alert("No"/"Yes")` 拒绝 → 退回 `BUTTON_AWAKE` + 可见提示 + `update_appearance()`。
- 确认 → 播放 `high_tech_confirm.ogg`（50 音量）；`button_stage = BUTTON_PUSHED`；可见提示；`message_admins` + investigate_log "[user] pushed [src]!"；`flick_overlay_view("[base_icon_state]-overlay-active", 20 SECONDS)`（20 秒激活闪烁）；**`SEND_GLOBAL_SIGNAL(COMSIG_MAIN_SM_DELAMINATING, MANUAL_INTERVENTION)`**（0）——触发整条抑制流程。
- 同时**临时开启逃生通道**: 半径 **7** 格内非指挥（`/obj/machinery/door/airlock/command`）气闸执行 `temp_emergency_exit` **45 秒**（"在反应室变得火辣之前，暂时放所有人出去"）。

### `burn_out()` — 烧毁

- 未损坏时: `desc` 追加警告 "The light is off, indicating it is not currently functional."（灯灭了，表明它已无法工作）；`set_machine_stat(machine_stat | BROKEN)`；`update_appearance()`。

### `update_icon_state()`

- `icon_state = "[base_icon_state][skin]"`；`BUTTON_ARMED` 或 `BUTTON_PUSHED` → 追加 `"-armed"`；`NOPOWER|BROKEN` → 追加 `"-nopower"`。

## 2.4 管理指令（Admin Verbs）

**代码**: `modular_nova/modules/delam_emergency_stop/code/admin_scram.dm`（64 行）

### `ADMIN_VERB try_stop_delam`（R_ADMIN，"解离紧急停止"）

描述: "Activate the delam suppression system."（激活解离抑制系统），分类 `ADMIN_CATEGORY_EVENTS`。

流程:
1. `validate_suppression_status()` —— 无效则直接返回。
2. **回合 < 30 分钟**时弹出警告: "你正在干预神的工程"（`tgui_alert`，No/Yes，LANG "datum.9bde39b3"）；拒绝 → 返回 FALSE。
3. 二次确认（`tgui_alert` No/Yes）；拒绝 → 返回 FALSE。
4. `log_admin` + `message_admins`（含 `ADMIN_LOOKUPFLW` 与 `ADMIN_COORDJMP` 跳转链接）+ `investigate_log(INVESTIGATE_ATMOS)`。
5. `SEND_GLOBAL_SIGNAL(COMSIG_MAIN_SM_DELAMINATING, DIVINE_INTERVENTION)`（**3**）——绕过 60 分钟时限与管理员禁用之外的检查（注意: `admin_disabled` 仍会拦下）。
6. 半径 **14** 格内非指挥气闸 `temp_emergency_exit` **45 秒**（"因为布局位置，多给点空间"）。

### `ADMIN_VERB toggle_delam_suppression`（R_FUN，"解离抑制开关"）

描述: "Disable/enable the delam suppression system."（禁用/启用解离抑制系统），分类 `ADMIN_CATEGORY_EVENTS`。

- 调用 `/client/proc/toggle_delam_suppression()`: `validate_suppression_status()` → `suppression_system.admin_disabled = !admin_disabled` → `log_admin` + `message_admins`（"toggled Delam suppression OFF/ON"）。

### `/proc/validate_suppression_status()` — 全局唯一性校验

遍历 `SSmachines.get_machines_by_type_and_subtypes(/obj/machinery/atmospherics/components/unary/delam_scram)`:

| 情况 | 结果 |
|---|---|
| 找到 **>1 台** | message_admins "Delam suppression request FAILED: Multiple Delam SCRAM units found on map!..."（删除多余的那台再试）+ `stack_trace`（"要么有人刷了重复机器，要么该去吼地图佬了"）；返回 FALSE |
| 找到 **0 台** | message_admins "No active delam SCRAM units found on map! Either it's not mapped or it's already been used!"（没找到：要么没画进地图，要么已经用掉了）；返回 FALSE |
| 唯一一台且 `on == TRUE` | message_admins "[my_one_and_only] can't fire, it's already been triggered!"（已经触发过了）；返回 FALSE |
| 正常 | 返回该机器 |

## 2.5 熔毁通知与自动干预（delam.dm）

**代码**: `modular_nova/modules/delam_emergency_stop/code/delam.dm`（62 行）

### `/obj/machinery/power/supermatter_crystal`

- 新增变量 `station_notified = FALSE` —— "根据解离抑制功能，管理员与全站是否已被通知"。

### `/datum/sm_delam/proc/notify_delam_suppression(obj/machinery/power/supermatter_crystal/sm)`

调用点: TG `delam_progress()`（伤害 > warning_point 时每次调用）。

触发条件（全部满足才通知）:
1. `sm.is_main_engine`（仅主引擎）；
2. `!sm.station_notified`；
3. `world.time - SSticker.round_start_time <= 30 MINUTES`（**回合 30 分钟内**才会自动干预）。

流程:
- **骨架班组检测**（`SSjob.is_skeleton_engineering(3)` 返回 TRUE，即无 CE 且工程师+大气技师 ≤ 3 人）:
  - `SSsecurity_level.minimum_security_level(SEC_LEVEL_ORANGE, TRUE, FALSE)` —— 全站安全等级升到**橙色**（给骨架班组一个警告）。
  - 公告系统（`get_announcement_system(null, sm)`）在**指挥频道**连播三条:
    1. "The supermatter delamination early warning system has been triggered due to anomalous conditions. Please investigate the engine as soon as possible."（超物质解离预警系统因异常状况被触发，请尽快检查引擎。）
    2. "In the event of uncontrolled delamination, please consult the documentation packet regarding usage of the supermatter emergency stop button."（若发生失控解离，请查阅关于超物质紧急停止按钮使用的文档包。）
    3. "Failure to stabilise the engine may result in an automatic deployment of the suppression system."（未能稳定引擎将可能导致抑制系统自动部署。）
- 无论是否骨架: `log_admin("DELAM: Round timer under 30 minutes! Supermatter will perform an automatic delam suppression at strength 0%.")`；向所有开启了 `comms_notification` 偏好的管理员播放 `server-ready.ogg`；`message_admins`（粉色）"DELAM: Round timer under 30 minutes! [ADMIN_VERBOSEJMP(sm)] will perform an automatic delam suppression once integrity reaches 0%. (**TOGGLE AUTOMATIC INTERVENTION**)" —— 附 `togglesuppression=yes` 的 href 开关。
- `sm.station_notified = TRUE`（只通知一次）。

### `/datum/sm_delam/Topic(href, href_list)`

- `togglesuppression` href 命中 → `check_rights(R_FUN)` 通过后调用 `usr.client?.toggle_delam_suppression()`（管理员可从通知直接禁用自动干预）。

### `/datum/controller/subsystem/job/proc/is_skeleton_engineering(crew_threshold)`

遍历 `GLOB.manifest.general` 的船员档案:
- 存在 **CE（JOB_CHIEF_ENGINEER）** → 直接返回 FALSE（不算骨架）。
- `JOB_STATION_ENGINEER` 或 `JOB_ATMOSPHERIC_TECHNICIAN` 每人计数 +1。
- 工程师数 **> crew_threshold（3）** → FALSE；否则 TRUE（骨架班组）。

## 2.6 配套物件与联动

**代码**: `modular_nova/modules/delam_emergency_stop/code/scram.dm`（配套物件）+ `code/supermatter_replacement.dm`

### 发射器紧急停机（`/obj/machinery/power/emitter`）

- `post_machine_initialize()`: 注册 `COMSIG_MAIN_SM_DELAMINATING` → `emergency_stop()`。
- `emergency_stop()`（SIGNAL_HANDLER）: 仅当所在区域为 `/area/station/engineering` 时 `active = FALSE` + `update_appearance()`（熔毁期间工程区发射器全部停火，防止继续打水晶）。

### 操作说明纸条（`/obj/item/paper/paperslip/corporate/fluff/delam_procedure`）

- 名称: "NT-approved delam emergency procedure"（NT 批准的熔毁紧急处置流程）。
- `examine` / `item_interaction` → 打开 tgui 界面 `"DelamProcedure"`（`autoupdate = FALSE`）。
- `item_interaction`: 用**剪贴板（clipboard）/文件夹（folder）/纸盒（paper_bin）**点击纸条可将其拾起（`base_item_interaction`，返回 `ITEM_INTERACT_SUCCESS`）。

### 安全蛾标牌（`/obj/structure/sign/delam_procedure`）

- 名称: "Safety Moth - Delamination Emergency Procedure"（安全蛾——解离紧急处置流程）。
- 描述: "This informational sign uses Safety Moth™ to tell the viewer how to use the emergency stop button if the Supermatter Crystal is delaminating."
- `icon_state = "moff-poster"`（scram.dmi），`pixel_y = 4`，`anchored = TRUE`。
- 护甲 `/datum/armor/sign_delam`: `melee 60` / `acid 70` / `fire 90`。
- `examine` → tgui `"DelamProcedure"`；`ui_status`: **盲人返回 `UI_CLOSE`**（不可读）。
- 映射: `MAPPING_DIRECTIONAL_HELPERS(/obj/structure/sign/delam_procedure, 32)`。

### 安全事件计数器重置（`/datum/controller/subsystem/persistence/proc/reset_delam_counter()`）

- `delam_highscore = rounds_since_engine_exploded`（记录历史最高安全轮数）；`rounds_since_engine_exploded = -1`（熔毁事件发生）；遍历 `GLOB.map_incident_displays` 更新事故显示屏的熔毁计数。

### 爆炸落点辅助（`/datum/component/supermatter_crystal/proc/find_blast_destination(atom/source)`）

- `modular_nova/modules/delam_emergency_stop/code/supermatter_replacement.dm`（13 行）——"把某人炸出 SM 反应室、送到相对安全处的辅助 proc"。
- 在 `RANGE_TURFS(12, atom_source)` 中筛选: 与源距离 **≥ 4** 格、且 `!candidate.is_blocked_turf()` 的候选 turf；随机 `pick` 一个返回；无候选返回 null。

---

# 第三卷 · 远征军（Expeditionary Corps）

> 模块 ID: `EXP_CORPS`。已解散的先锋远征军团（Vanguard Expeditionary Corps）遗留服装与装备。
> 文件: `code/clothing.dm`（280 行）+ `code/expeditionary_trooper.dm`（32 行）+ `code/gear.dm`（231 行）+ `code/tomahawk.dm`（33 行），共 576 行。
> 图标: `icons/backpack.dmi`、`mob_backpack.dmi`、`bonesaw.dmi`/`bonesaw_l.dmi`/`bonesaw_r.dmi`、`exp_crate.dmi`、`closet.dmi`、`riot.dmi`/`riot_left.dmi`/`riot_right.dmi`、`survival_pack.dmi`、`throwing.dmi`、`tomahawk.dmi`/`tomahawk_l.dmi`/`tomahawk_r.dmi`/`tomahawk_worn.dmi`（部分服装图标位于 `modular_nova/master_files/icons/`）。

## 3.1 模块总览

| 文件 | 行数 | 内容 |
|---|---|---|
| `code/clothing.dm` | 280 | 制服/胸挂/靴子/手套/背包/防弹背心/夜视头盔 |
| `code/gear.dm` | 231 | 医疗包/骨锯/尖兵盾/飞刀/刀囊 |
| `code/tomahawk.dm` | 33 | 远征战斧 |
| `code/expeditionary_trooper.dm` | 32 | 军用 PDA/远征生存包/神射手箱子 |

## 3.2 服装全录（clothing.dm）

### 3.2.1 远征军制服（`/obj/item/clothing/under/rank/expeditionary_corps`）

- 名称: "expeditionary corps uniform"（远征军制服）；描述: "A rugged uniform for those who see the worst at the edges of the galaxy."（为那些在银河边缘见证最糟糕境况的人准备的耐用制服）。
- `icon_state = "exp_corps"`；图标位于 `modular_nova/master_files/icons/obj/clothing/uniforms.dmi` 与 `.../mob/clothing/uniform.dmi`。
- 护甲 `/datum/armor/clothing_under/rank_expeditionary_corps`: **fire 15 / acid 15**。
- `strip_delay = 7 SECONDS`；`alt_covers_chest = TRUE`；`sensor_mode = SENSOR_COORDS`（默认坐标传感器）；`random_sensor = FALSE`。

### 3.2.2 胸挂（`/obj/item/storage/belt/military/expeditionary_corps`）

- 名称: "expeditionary corps chest rig"（远征军胸挂）；描述: "A set of tactical webbing worn by the now-defunct Vanguard Expeditionary Corps."（已解散的先锋远征军团穿过的战术织带）。
- `icon_state / worn_icon_state = "webbing_exp_corps"`。
- **可换肤（reskin）**: `setup_reskins()` 添加 `/datum/component/reskinable_item` + `/datum/atom_skin/expeditionary_corps_chest_rig`:
  - `webbing`: 预览名 "Webbing"，`new_icon_state = "webbing_exp_corps"`；
  - `belt`: 预览名 "Belt"，`new_icon_state = "belt_exp_corps"`。

**四个职业变体及默认内容（PopulateContents）**:

| 变体 | 名称 | 内容 |
|---|---|---|
| `/combat_tech` | "combat tech's chest rig"（战斗技师胸挂） | 螺丝刀、扳手、焊枪、撬棍、剪线钳、万用表、线缆卷 |
| `/field_medic` | "field medic's chest rig"（野战医护胸挂） | 手术刀、`circular_saw/field_medic`（骨锯）、止血钳、拉钩、烧灼器、手术单、正骨器 |
| `/pointman` | "pointman's chest rig"（尖兵胸挂） | 威士忌瓶、**塑钢 ×5**、吗啡瓶 |
| `/marksman` | "marksman's chest rig"（神射手胸挂） | 双筒望远镜、稳健牌香烟、打火机、骷髅头巾 |

### 3.2.3 靴子与手套

**远征军靴**（`/obj/item/clothing/shoes/combat/expeditionary_corps`）:
- "expeditionary corps boots"，描述 "High speed, low drag combat boots."（高速低阻力战斗靴）。
- `icon_state = "exp_corps"`，`inhand_icon_state = "jackboots"`。

**黑色手套**（`/obj/item/clothing/gloves/color/black/expeditionary_corps`）:
- 名称 "expeditionary corps gloves"；`icon_state = "exp_corps"`。
- 寒冷保护 HANDS / `GLOVES_MIN_TEMP_PROTECT`；炎热保护 HANDS / `GLOVES_MAX_TEMP_PROTECT`；`resistance_flags = FIRE_PROOF`。

**绝缘手套**（`/obj/item/clothing/gloves/chief_engineer/expeditionary_corps`）:
- 名称 "expeditionary corps insulated gloves"（绝缘）；`icon_state = "exp_corps_eng"`，`worn_icon_state = "exp_corps"`。
- 护甲 `/datum/armor/chief_engineer_expeditionary_corps`: **fire 80 / acid 50**。
- `clothing_traits = list(TRAIT_FAST_CUFFING)`（快速上铐，与其他黑色手套一致）。

**医用丁腈手套**（`/obj/item/clothing/gloves/latex/nitrile/expeditionary_corps`）:
- 名称 "expeditionary corps medic gloves"（医用）；`icon_state = "exp_corps_med"`，`worn_icon_state = "exp_corps"`。
- 冷/热保护 HANDS 全开；`FIRE_PROOF`。
- 护甲 `/datum/armor/nitrile_expeditionary_corps`: **fire 80 / acid 50**。

### 3.2.4 远征军袋（`/obj/item/storage/backpack/duffelbag/expeditionary_corps`）

- 名称 "expeditionary corps bag"（远征军袋）；描述 "A large bag for holding extra tactical supplies."（装额外战术物资的大包）。
- `icon_state = "exp_corps"`，`inhand_icon_state = "backpack"`；图标在模块 `icons/backpack.dmi` 与 `icons/mob_backpack.dmi`。
- **可换肤**: `/datum/atom_skin/expeditionary_corps_bag`:
  - `backpack`: 预览名 "Backpack"，`new_icon_state = "exp_corps"`；
  - `belt`: 预览名 "Belt"，`new_icon_state = "exp_corps_satchel"`（挎包样式）。

### 3.2.5 防弹背心（`/obj/item/clothing/suit/armor/vest/expeditionary_corps`）

- 名称 "expeditionary corps armor vest"；描述: "An armored vest that provides okay protection against most types of damage. Includes concealable sleeves for your arms."（对大多数伤害提供尚可防护的背心，带可隐藏的臂套）。
- `icon_state = "exp_corps"`；`body_parts_covered = CHEST|GROIN|ARMS`；冷/热保护同覆盖范围；`dog_fashion = null`。

**护甲数值 `/datum/armor/vest_expeditionary_corps`**:

| 类型 | 值 |
|---|---|
| melee（近战） | **30** |
| bullet（子弹） | **30** |
| laser（激光） | **30** |
| energy（能量） | **30** |
| bomb（爆炸） | **40** |
| fire（火焰） | **80** |
| acid（酸） | **100** |
| wound（伤口） | **10** |

**可容纳（allowed）**: `/obj/item/melee`、`/obj/item/ammo_box`、`/obj/item/ammo_casing`、`/obj/item/flashlight`、`/obj/item/gun`、`/obj/item/knife`、`/obj/item/reagent_containers`、`/obj/item/restraints/handcuffs`、`/obj/item/tank/internals/emergency_oxygen`、`/obj/item/tank/internals/plasmaman`、`/obj/item/storage/belt/holster`、`/obj/item/storage/belt/machete`。

### 3.2.6 夜视头盔（`/obj/item/clothing/head/helmet/expeditionary_corps`）

- 名称 "expeditionary corps helmet"；描述: "A robust helmet worn by Expeditionary Corps troopers. **Alt+click it to toggle the NV system.**"（远征军士兵佩戴的坚固头盔。Alt+点击可切换夜视系统）。
- `icon_state = "exp_corps"`；`supports_variations_flags = CLOTHING_SNOUTED_VARIATION_NO_NEW_ICON`。
- 变量: `nightvision = FALSE`、`current_user`（当前佩戴者）；`actions_types = list(/datum/action/item_action/toggle_nv_helmet)`（"Toggle Nightvision" 技能按钮）。

**护甲数值 `/datum/armor/helmet_expeditionary_corps`**: melee **20** / bullet **20** / laser **20** / energy **20** / bomb **30** / fire **80** / acid **100** / wound **10**。

**夜视系统（NV）**:
- 开启（`enable_nv()`）: 佩戴者眼睛器官 `color_cutoffs = list(10, 30, 10)`（绿色调偏移）、`flash_protect = FLASH_PROTECTION_SENSITIVE`（闪盲敏感）；添加客户端滤镜 `/datum/client_colour/glass_colour/lightgreen`（浅绿玻璃）。
- 关闭（`disable_nv()`）: 还原 `initial()` 值、移除滤镜、`update_sight()`。
- 切换入口: 技能按钮（`/datum/action/item_action/toggle_nv_helmet/Trigger`）、**Alt+点击**（`click_alt`，返回 `CLICK_ACTION_SUCCESS`）；切换时 `to_chat` 开启/关闭提示。
- 生命周期: `equipped` 记录 `current_user`；`dropped` / `Destroy` 时自动 `disable_nv()` 并清空 `current_user`（防止残留滤镜）。
- `update_icon_state()`: 夜视开启时 `icon_state = "exp_corps_on"`。

## 3.3 装备全录（gear.dm）

### 3.3.1 远征医疗包（`/obj/item/storage/medkit/expeditionary`）

- 名称 "expeditionary medical kit"（远征医疗包）；描述 "Now with 100% less bullshit."（废话含量减少 100%）；`icon_state = "medkit_tactical"`；`damagetype_healed = "all"`（可治疗所有伤害类型）。
- 注释: "Gateway Medkit, no more combat defibs!"（网关医疗包，不再有战斗除颤器！）
- **内容**: 纱布卷、`defibrillator/compact/loaded`（紧凑型已装载除颤器）、`hypospray/combat`（战斗注射笔）、高级医用网、含药缝合线、健康 HUD 眼镜。

### 3.3.2 库存过剩医疗包（`/obj/item/storage/medkit/expeditionary/surplus`）

- 描述: "Now with less bullshit. And more dust. But mainly less bullshit. If you have to use this, there's no way you've got insurance."（废话少点，灰尘多点，主要废话少。要是你不得不用它，那你肯定没买保险）。
- **内容**: 十二卷纱布、`hypospray/combat`（注释: **epi/atro + lepo + omnizine**，即肾上腺素/阿托品 + 左旋麻黄碱 + 万能药）、**含药缝合线 ×2**、**高级医用网 ×2**、健康 HUD 眼镜。

### 3.3.3 骨锯（`/obj/item/circular_saw/field_medic`）

- 名称 "bone saw"（骨锯）；描述: "An ancient medical instrument used for surgery and amputations, still being used in the 26th century. Well, what are you waiting for? Let's go practice medicine."（26 世纪仍在使用的古老手术与截肢器械。还等什么？去实践医学吧）。
- 数值: `force = 20`（`/lowforce` 变体 **9**）；`toolspeed = 2`；`throw_range = 3`；`w_class = WEIGHT_CLASS_SMALL`；`hitsound = bladeslice.ogg`；攻击动词 "saws/slashes"（锯/劈）；图标 `icons/bonesaw.dmi` + 左右手持图标。

**主动截肢攻击（`attack()`）** —— 非战斗模式下手持骨锯攻击碳基生物（非胸口部位）:
1. 目标无 `TRAIT_NODISMEMBER`（不可肢解）→ 拒绝。
2. **精确部位=腹股沟（BODY_ZONE_PRECISE_GROIN）**: 寻找外部尾巴器官（`ORGAN_SLOT_EXTERNAL_TAIL`），有尾巴 → 目标为"切尾巴"；无尾巴 → 拒绝。
3. **其他部位**: 取对应身体部位（`get_bodypart`），无 → 拒绝。
4. 速度修正（基础 `toolspeed × 15 SECONDS = 30 秒`）:
   - 目标**无意识/瘫痪/硬直/濒死/死亡**（`stat >= UNCONSCIOUS` 或 `TRAIT_INCAPACITATED`）→ **×0.5**（容易对齐下刀，15 秒）；
   - 目标**痉挛（jitter）**且未死 → **×1.5**（"15×0.5×1.5=11.25，被电棍电到痉挛的人不够时间被你切头，但没痉挛的人可以"）；
   - 使用者有 **TRAIT_MORBID（病态）** → **×0.7**（"its morbin time"）。
5. `do_after(user, 30s × 修正)` 成功 → 尾巴 `Remove + forceMove` 到脚下，或肢体 `dismember()`；双方日志 `LOG_GAME`；病态者获得心情事件 `morbid_dismemberment`。

**自杀动作（`suicide_act`）**: 除胸口（CHEST）外的**所有身体部位每隔 1 秒依次肢解**，播放 `bladeslice.ogg`（70 音量），结束返回 `BRUTELOSS`（致命钝伤）。

### 3.3.4 尖兵盾（`/obj/item/shield/riot/pointman`）

- 名称 "pointman shield"（尖兵盾）；描述: "A shield fit for those that want to sprint headfirst into the unknown. Its heavy, unwieldy nature makes its defensive performance suffer when in the off-hand; wielding will provide best results at the cost of reduced mobility."（适合想一头冲进未知的人。沉重笨重，副手持握时防御性能下降；双手持握效果最佳但牺牲机动性）。
- 数值: `force = 10`；`throwforce = 5`；`throw_speed = 1`；`throw_range = 1`；`block_chance = 15`；`w_class = WEIGHT_CLASS_BULKY`；`transparent = FALSE`；`max_integrity = 200`；`shield_break_leftover = /obj/item/pointman_broken`。
- 材料（`custom_materials`）: **塑钢 ×3 片、玻璃 ×3 片、铁 ×1.5 片**。
- 双手组件（`/datum/component/two_handed`）: 副手 `force = 10`，双手 `force = 20`。
  - `shield_wield()`（双手）: `item_flags |= SLOWS_WHILE_IN_HAND`；`block_chance *= 5`（**15 → 75**）；`slowdown = 0.6`。
  - `shield_unwield()`（放下）: 移除减速旗标；`block_chance /= 5`；`slowdown = 0`。

**损坏品（`/obj/item/pointman_broken`）**:
- 名称 "broken pointman shield"；描述 "Enough of it is still intact that you could probably just weld more bits on."（完整部分足够让你焊点东西上去）；`icon_state = "riot_broken"`；BULKY。
- `Initialize`: 添加 `/datum/element/slapcrafting`（拍打合成）→ 配方 `/datum/crafting_recipe/pointman_repair`。

**修复配方（`/datum/crafting_recipe/pointman_repair`）**:

| 项目 | 值 |
|---|---|
| 名称 | "pointman shield (repaired)"（尖兵盾（修复）） |
| 结果 | `/obj/item/shield/riot/pointman` |
| 需求 | 破损尖兵盾 ×1 + **塑钢片 ×3** + **强化玻璃（rglass）×3** |
| 时间 | **5 SECONDS** |
| 分类 | `CAT_MISC` |
| 工具 | `TOOL_WELDER`（焊枪） |

### 3.3.5 飞刀（`/obj/item/knife/combat/throwing`）

- 名称 "throwing knife"（飞刀）；描述: "While very well weighted for throwing, the distribution of mass makes it unwieldy for use in melee."（投掷配重极佳，但质量分布使其近战笨拙）。
- 数值: `force = 12`（"别拿它捅人"）；`throwforce = 30`（嵌入时 **38** 伤害，"与手里剑对比一下"）；`throw_speed = 4`；`embed_type = /datum/embedding/combat_knife/throwing`。
- 嵌入数据: `embed_chance = parent(combat_knife 的 65) + 10 = **75%**`。

### 3.3.6 神射手刀囊（`/obj/item/storage/pouch/ammo/marksman`）

- 名称 "marksman's knife pouch"（神射手刀囊）。
- `setup_reskins()` → return（**禁皮肤**）。
- 储物格 `/datum/storage/marksman`: `max_total_storage = 60`；`max_slots = 10`；`numerical_stacking = TRUE`（同种堆叠）；`quickdraw = TRUE`（快速抽出）；`can_hold = typecacheof(/obj/item/knife/combat)`（只装战斗刀）。
- 默认内容: **5 把 `knife/combat/throwing`**（"5 把刀能干掉大多数基础敌人，不过神射手不该单挑"）。

## 3.4 远征战斧（tomahawk.dm）

**代码**: `modular_nova/modules/exp_corps/code/tomahawk.dm`（33 行）

`/obj/item/melee/tomahawk` — 名称 "expeditionary tomahawk"（远征战斧）;描述 "A decently sharp axe blade upon a short fibremetal handle."（短纤维金属柄上装着一片相当锋利的斧刃）。

| 属性 | 值 |
|---|---|
| `obj_flags` | `CONDUCTS_ELECTRICITY`（导电） |
| `force` | **15**（"等同于生存刀"） |
| `w_class` | `WEIGHT_CLASS_SMALL` |
| `throwforce` | **18** |
| `throw_speed` | **4** |
| `throw_range` | **8** |
| `embed_type` | `/datum/embedding/tomahawk` |
| 材料 | 铁 ×**7.5** 片（`SHEET_MATERIAL_AMOUNT*7.5`） |
| 攻击动词 | 砍/撕裂/划伤/切割（chops/tears/lacerates/cuts） |
| `hitsound` | `bladeslice.ogg` |
| `sharpness` | `SHARP_EDGED`（利刃） |

**嵌入数据 `/datum/embedding/tomahawk`**: `pain_mult = 6`（疼痛倍率 6）、`embed_chance = 60`（嵌入率 60%）、`fall_chance = 10`（掉落率 10%）。

**屠宰组件（`/datum/component/butchering`）**: `speed = 7 SECONDS`、`effectiveness = 100`（可当屠宰工具用，7 秒 100% 效率）。

## 3.5 远征步兵配套（expeditionary_trooper.dm）

**代码**: `modular_nova/modules/exp_corps/code/expeditionary_trooper.dm`（32 行）

### 军用 PDA（`/obj/item/modular_computer/pda/expeditionary_corps`）

- 名称 "surplus military PDA"（军用剩余 PDA）；`greyscale_colors = "#891417#000099"`（深红 + 深蓝双色）。

### 远征生存包（`/obj/item/storage/box/expeditionary_survival`）

- 名称 "expedition survival pack"（远征生存包）；描述 "A box filled with useful items for your expedition!"（装满远征实用物品的盒子）；`icon_state = "survival_pack"`；`illustration = null`。
- **内容**:
  1. 甜甜圈盒（`storage/box/donkpockets`）
  2. 荧光棒（`flashlight/glowstick`）
  3. 双罐应急氧气（`tank/internals/emergency_oxygen/double`）
  4. 水瓶（`cup/glass/waterbottle`）
  5. O 型阴性血袋（`reagent_containers/blood/o_minus`）
  6. 注射器（`reagent_containers/syringe`）
  7. 万能药药瓶（`storage/pill_bottle/multiver`）

### 神射手保险箱（`/obj/structure/closet/crate/secure/exp_corps/marksman`）

- 注释: "edgy loner with knives AND A FUKKEN gun"（带着刀还有把枪的中二独狼）。
- **内容**:
  1. 常规医疗包（`storage/medkit/regular`）
  2. 远征生存包
  3. 无线电（`radio`）
  4. 神射手刀囊（`storage/pouch/ammo/marksman`）
  5. 远征军黑色手套
  6. 远征军夜视头盔
  7. 远征军防弹背心
  8. 神射手胸挂（`belt/military/expeditionary_corps/marksman`）
  9. 远征军袋（`backpack/duffelbag/expeditionary_corps`）
  10. 手枪枪盒 `storage/toolbox/guncase/nova/pistol/trappiste_small_case/skild`（Skild 手枪）

---

# 附录 · 代码路径索引

## 死亡后果（Death Consequences）— `modular_nova/modules/death_consequences_perk/`

| 文件 | 行数 | 内容 |
|---|---|---|
| `death_consequences.dm` | 87 | 特质本体、add/remove、两个 IC 动词、`get_death_consequences_trauma` |
| `death_consequences_trauma.dm` | 573 | 创伤对象：全部衰减/效果/永久死亡/扫描仪/偏好读取逻辑 |
| `readme.md` | 48 | 模块说明（ID: death_consequences，作者 Niko，PR #23733） |

**外部文件**:

| 文件 | 内容 |
|---|---|
| `code/__DEFINES/~nova_defines/quirks.dm`（1～27 行） | 特质名/描述/全部默认值与键名宏（见 1.1 表） |
| `modular_nova/master_files/code/modules/client/preferences/quirks/death_consequences.dm` | 18 项角色偏好（16 数值 + 2 开关，见 1.9 表） |
| `code/modules/medical/health_analysis.dm`（TG） | `healthscan()` 增加死亡衰减扫描文本 |
| `tgui/packages/tgui/interfaces/PreferencesMenu/...species_features.tsx`（TG） | 特质自定义 UI 渲染 |

## 熔毁紧急停止（Delam SCRAM）— `modular_nova/modules/delam_emergency_stop/`

| 文件 | 行数 | 内容 |
|---|---|---|
| `code/scram.dm` | 499 | 抑制机器、大红按钮、纸条/标牌、发射器停机、持久化计数、全部宏 |
| `code/admin_scram.dm` | 64 | 管理指令"解离紧急停止"/"解离抑制开关"、`validate_suppression_status` |
| `code/delam.dm` | 62 | `notify_delam_suppression` 自动干预、`is_skeleton_engineering`、Topic 开关 |
| `code/supermatter_replacement.dm` | 13 | `find_blast_destination` 爆炸落点辅助 |
| `README.md` | 43 | 模块说明（ID: DELAM_SCRAM，作者 LT3，PR #22145） |
| `icons/scram.dmi` | — | 机器/按钮/标牌图标 |

**TG 改动点**: `code/modules/power/supermatter/supermatter.dm`（`count_down` 发 `COMSIG_MAIN_SM_DELAMINATING` 信号）、`code/modules/power/supermatter/supermatter_delamination/_sm_delam.dm`（`delam_progress` 调 `notify_delam_suppression`）；定义: `~nova_defines/signals.dm:43`（`COMSIG_MAIN_SM_DELAMINATING "delam_time"`）、`~nova_defines/events.dm:16`（`DIVINE_INTERVENTION 3`）。

## 远征军（Expeditionary Corps）— `modular_nova/modules/exp_corps/`

| 文件 | 行数 | 内容 |
|---|---|---|
| `code/clothing.dm` | 280 | 制服/胸挂（4 变体）/靴/手套（3 种）/袋/背心/夜视头盔 |
| `code/gear.dm` | 231 | 医疗包（2 种）/骨锯/尖兵盾+修复/飞刀/刀囊 |
| `code/tomahawk.dm` | 33 | 远征战斧（嵌入 + 屠宰组件） |
| `code/expeditionary_trooper.dm` | 32 | 军用 PDA/远征生存包/神射手箱子 |
| `icons/` | — | backpack / mob_backpack / bonesaw(×3) / exp_crate / closet / riot(×3) / survival_pack / throwing / tomahawk(×4) 图标 |

---

*本百科依据 NovaSector 分支源码逐行整理，数值均直接取自代码默认值；被偏好设置覆盖的运行时数值以 1.9 节偏好表为准。*
