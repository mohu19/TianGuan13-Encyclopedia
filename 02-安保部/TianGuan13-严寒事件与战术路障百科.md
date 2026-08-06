# TianGuan13 严寒事件与战术路障百科

> **项目**: TianGuan13 (Nova Sector → /tg/station)
> **代码**: `modular_nova/modules/ices_events/`（11 个 .dm，881 行）+ `modular_nova/modules/barricades/`（1 个 .dm，842 行）
> **范围**: ICES 严寒事件系统（Intensity Credit Events System，强度信用点事件系统——事件权重/频率/强度信用点/自定义事件全录）+ 战术路障系统（雪/护栏/木/金属/塑钢五种路障、建造状态、升级、快速部署 C.U.C.K.S）
>
> **关联文档**: 事件子系统基类为 /tg/station 原版加权随机事件系统；气象事件联动见《天气与远征百科》；流星/异常/沙尘暴等事件地图行为见《随机事件百科》。

---

## 目录

- [第一卷 · ICES 严寒事件系统（Intensity Credit Events System）](#第一卷--ices-严寒事件系统intensity-credit-events-system)
  - [1.1 模块总览](#11-模块总览)
  - [1.2 事件频率宏定义](#12-事件频率宏定义)
  - [1.3 事件子系统扩展（SSevents）](#13-事件子系统扩展ssevents)
  - [1.4 强度信用点调整（change_intensity_credits）](#14-强度信用点调整change_intensity_credits)
  - [1.5 处理逻辑与调试（ICES_procs）](#15-处理逻辑与调试ices_procs)
  - [1.6 安保等级挂钩（ICES_seclevel）](#16-安保等级挂钩ices_seclevel)
  - [1.7 管理面板与全局变量（ICES_tgui / globalvars）](#17-管理面板与全局变量ices_tgui--globalvars)
  - [1.8 事件配置全表（ICES_event_config）](#18-事件配置全表ices_event_config)
  - [1.9 自定义事件：生物扰乱异常（Bioscrambler）](#19-自定义事件生物扰乱异常bioscrambler)
  - [1.10 自定义事件：流星波（Meteor Wave: ICES）](#110-自定义事件流星波meteor-wave-ices)
  - [1.11 自定义事件：清洗器溢出（Scrubber Overflow: ICES）](#111-自定义事件清洗器溢出scrubber-overflow-ices)
  - [1.12 清洗器泡沫特效（ef_foam）](#112-清洗器泡沫特效ef_foam)
  - [1.13 角色扮演参与检查（engaged_role_play_check）](#113-角色扮演参与检查engaged_role_play_check)
- [第二卷 · 战术路障系统（Deployable Barricades）](#第二卷--战术路障系统deployable-barricades)
  - [2.1 模块总览与基类属性](#21-模块总览与基类属性)
  - [2.2 基类交互与机制](#22-基类交互与机制)
  - [2.3 雪路障（Snow Barricade）](#23-雪路障snow-barricade)
  - [2.4 护栏（Fencing / Guard Rail）](#24-护栏fencing--guard-rail)
  - [2.5 木路障（Wooden Barricade）](#25-木路障wooden-barricade)
  - [2.6 金属路障（Metal Barricade）](#26-金属路障metal-barricade)
  - [2.7 塑钢路障（Plasteel Barricade）](#27-塑钢路障plasteel-barricade)
  - [2.8 快速部署物品（Quickdeploy / C.U.C.K.S）](#28-快速部署物品quickdeploy--cucks)
  - [2.9 路障收纳盒（C.U.C.K.S Box）](#29-路障收纳盒cucks-box)
- [附录 · 代码路径索引](#附录--代码路径索引)

---

# 第一卷 · ICES 严寒事件系统（Intensity Credit Events System）

> 模块 ID: `ICES_EVENTS`。ICES = **I**ntensity **C**redit **E**vents **S**ystem（强度信用点事件系统）。
> 基于 TG 原版加权随机事件系统（TG weighted random events system）构建的模块化随机事件系统：用一套"权重 + 强度"体系来运行随机事件，并附带数个 Skyrat 定制事件。
> 上游 PR: https://github.com/Skyrat-SS13/Skyrat-tg/pull/19505
> 设计文档: https://hackmd.io/@lessthanthree/HJKVFH0yn
> 作者（Credits）: **LT3**（Discord: lessthanthree#0003，ID 122553868985827329）

---

## 1.1 模块总览

**代码**: `modular_nova/modules/ices_events/readme.md`、`modular_nova/modules/ices_events/code/_ICES_globalvars.dm`

文件结构（11 个 .dm，共 881 行）：

| 文件 | 行数 | 内容 |
|---|---|---|
| `code/ICES_event_config.dm` | 497 | 事件配置全表（所有原版事件的权重/次数覆盖） |
| `code/ICES_intensity_credits.dm` | 52 | 强度信用点增减逻辑 |
| `code/ICES_procs.dm` | 20 | 事件排程调试检查 |
| `code/ICES_seclevel.dm` | 21 | 事件安保等级挂钩 |
| `code/ICES_tgui.dm` | 42 | 管理员 ICES 面板（tgui） |
| `code/_ICES_globalvars.dm` | 1 | 全局变量 `intense_event_credits` |
| `code/effects/ef_foam.dm` | 12 | 清洗器泡沫特效变体 |
| `code/events/ev_bioscrambler.dm` | 60 | 定制事件：生物扰乱异常 |
| `code/events/ev_meteors.dm` | 71 | 定制事件：流星波（ICES 版） |
| `code/events/ev_scrubbers.dm` | 87 | 定制事件：清洗器溢出（ICES 版） |
| `code/events/ev_roleplay_check.dm` | 18 | 事件目标 RP 参与检查 |

- **全局变量**: `GLOB.intense_event_credits` — 当前强度信用点池（初始 0），由 `GLOBAL_VAR_INIT(intense_event_credits, 0)` 声明。
- **核心概念**: 事件子系统以固定速率向信用点池注入"强度信用点"（intensity credits）；`intensity_restriction = TRUE` 的事件只在池中有足够信用时才能触发（由系统外部逻辑消费/注入信用点，如管理员面板或定时器）。

---

## 1.2 事件频率宏定义

**代码**: `modular_nova/modules/ices_events/code/ICES_event_config.dm`（第 1–6 行）

六个频率宏（即默认权重档位）：

| 宏 | 值 | 含义 |
|---|---|---|
| `VERY_HIGH_EVENT_FREQ` | **64** | 极高事件频率 |
| `HIGH_EVENT_FREQ` | **32** | 高事件频率 |
| `MED_EVENT_FREQ` | **16** | 中事件频率 |
| `LOW_EVENT_FREQ` | **8** | 低事件频率 |
| `VERY_LOW_EVENT_FREQ` | **4** | 极低事件频率 |
| `MIN_EVENT_FREQ` | **2** | 最小事件频率 |

（文件末尾全部 `#undef` 清理。）

---

## 1.3 事件子系统扩展（SSevents）

**代码**: `modular_nova/modules/ices_events/code/ICES_event_config.dm`（第 21–45 行）

`/datum/controller/subsystem/events` 扩展变量（覆盖原版 min/max 开始时间以适配更长的回合）：

| 变量 | 默认值 | 说明 |
|---|---|---|
| `intensity_credit_rate` | **27000** | 向信用点池添加信用点的速率间隔 |
| `intensity_credit_last_time` | **8400** | 上次添加信用点的世界时间 |
| `active_intensity_multiplier` | `EVENT_MIDPOP_TIMER_MULTIPLIER` | 当前生效的 ICES 乘数 |
| `intensity_low_players` | `EVENT_LOWPOP_THRESHOLD` | LOWPOP（低在线）玩家阈值 |
| `intensity_low_multiplier` | `EVENT_LOWPOP_TIMER_MULTIPLIER` | LOWPOP 乘数（越低 = 事件越多） |
| `intensity_mid_players` | `EVENT_MIDPOP_THRESHOLD` | MIDPOP 玩家阈值 |
| `intensity_mid_multiplier` | `EVENT_MIDPOP_TIMER_MULTIPLIER` | MIDPOP 乘数（越低 = 事件越多） |
| `intensity_high_players` | `EVENT_HIGHPOP_THRESHOLD` | HIGHPOP 玩家阈值 |
| `intensity_high_multiplier` | `EVENT_HIGHPOP_TIMER_MULTIPLIER` | HIGHPOP 乘数（越低 = 事件越多） |

`Initialize()` 覆写（从配置读取）：
- `frequency_lower = CONFIG_GET(number/event_frequency_lower)`（事件频率下限）
- `frequency_upper = CONFIG_GET(number/event_frequency_upper)`（事件频率上限）
- `intensity_credit_rate = CONFIG_GET(number/intensity_credit_rate)`（信用点速率，默认 27000）

---

## 1.4 强度信用点调整（change_intensity_credits）

**代码**: `modular_nova/modules/ices_events/code/ICES_intensity_credits.dm`（52 行）

三个动作宏：

| 宏 | 值 | 动作 |
|---|---|---|
| `REMOVE_INTENSITY_CREDIT` | **1** | 从池中扣除信用点 |
| `ADD_INTENSITY_CREDIT` | **2** | 向池中添加信用点 |
| `STATIC_INTENSITY_CREDIT` | **3** | 把池设置为指定值 |

`/datum/round_event_control` 新增变量：
- `intensity_restriction` = FALSE —— 该事件是否"强度过高"需要特殊处理规则（为 TRUE 的事件由 ICES 强度体系限制触发）。

`/datum/controller/subsystem/events/proc/change_intensity_credits(credit_action = 0, credit_amount = 0, check_timer = FALSE, reset_timer = FALSE, notify_admins = TRUE)`：
1. **前置检查**（`check_timer = TRUE` 时）：若 `world.time - intensity_credit_last_time < intensity_credit_rate`（距上次添加不足 27000 tick），拒绝请求：
   - `message_admins` + `log_game`："ICES: Request for Intensity Credit rejected due to precondition check. Reason: [差值] is less than [intensity_credit_rate]. Current intensity credits: [GLOB.intense_event_credits]"；
   - 返回 FALSE。
2. **动作分发**（switch）：
   - `ADD_INTENSITY_CREDIT`：`GLOB.intense_event_credits += credit_amount`；
   - `REMOVE_INTENSITY_CREDIT`：`GLOB.intense_event_credits -= credit_amount`；
   - `STATIC_INTENSITY_CREDIT`：`GLOB.intense_event_credits = credit_amount`；
   - 三种动作均写 `log_game`，且 `notify_admins = TRUE` 时 `message_admins`（"Adding/Removing/Setting intensity credit to events system. Current intensity credits: [值]"）；未知动作返回 FALSE。
3. **计时器重置**（`reset_timer = TRUE` 时）：`intensity_credit_last_time = world.time`，并记录 "ICES: Intensity Credit timer reset to [world.time]"。
4. 成功返回 TRUE。

---

## 1.5 处理逻辑与调试（ICES_procs）

**代码**: `modular_nova/modules/ices_events/code/ICES_procs.dm`（20 行）

`/datum/controller/subsystem/events/proc/debug_event_schedule()` —— 检查事件是否会因穿梭机（shuttle）相关事件而暂停，向管理员输出全套排程诊断：
- 输出：真实时间 `world.realtime`、自动转移目标时间 `SSautotransfer.targettime`、世界时间 `world.time`、回合开始时间 `SSticker.round_start_time`、计时器差值 `SSautotransfer.targettime - world.realtime`、投票期 `15 MINUTES + CONFIG_GET(number/vote_period)`、穿梭机加油延迟 `CONFIG_GET(number/shuttle_refuel_delay)`。
- **计时器前置检查**：若 `(SSautotransfer.targettime - world.realtime) <= 15 MINUTES + vote_period` → **FAILED**（值低于 15 分钟 + 投票期）；否则 **PASSED**。
- **穿梭机前置检查**：若 `world.time - SSticker.round_start_time <= CONFIG_GET(number/shuttle_refuel_delay)` → **FAILED（穿梭机正在加油 refuelling）**，直接 return；
- 若 `!SSshuttle.canEvac()`（不可撤离）→ **FAILED**；否则 **PASSED**。

---

## 1.6 安保等级挂钩（ICES_seclevel）

**代码**: `modular_nova/modules/ices_events/code/ICES_seclevel.dm`（21 行）

`/datum/round_event/proc/event_minimum_security_level(min_level = SEC_LEVEL_ORANGE, eng_access = TRUE, maint_access = FALSE)` —— 由事件调用，检查/提升安保等级：
1. `sec_level = SSsecurity_level.get_current_level_as_number()`；若当前等级 `< min_level`（默认橙色 SEC_LEVEL_ORANGE）→ `SSsecurity_level.set_level(min_level)`。
2. `eng_access = TRUE` 时：`GLOB.force_eng_override = TRUE`，并发送全局信号 `COMSIG_GLOB_FORCE_ENG_OVERRIDE`（TRUE）——向气闸发送工程覆盖信号以启用额外门禁权限。
3. `maint_access = TRUE` 时：`make_maint_all_access()`（全维修通道通行）。

---

## 1.7 管理面板与全局变量（ICES_tgui / globalvars）

**代码**: `modular_nova/modules/ices_events/code/ICES_tgui.dm`（42 行）+ `code/_ICES_globalvars.dm`（1 行）

**管理动词**：
```
ADMIN_VERB(intensity_credits_panel, R_FUN, "ICES 事件面板", "Opens up the ICES panel.", ADMIN_CATEGORY_EVENTS)
```
- 权限 `R_FUN`，分类 `ADMIN_CATEGORY_EVENTS`；调用 `user.holder?.intensity_credits_panel()`。

**`/datum/admins/proc/intensity_credits_panel()`**：`check_rights(R_FUN)` 校验后创建 `/datum/intensity_credits_panel` 并 `ui_interact(usr)`。

**`/datum/intensity_credits_panel`**（tgui 界面 `IntensityCredits`）：
- `ui_state` = `ADMIN_STATE(R_FUN)`（仅管理员）。
- `ui_data` 字段（`get_active_player_count(alive_check = TRUE, afk_check = TRUE, human_check = TRUE)` 作为活跃玩家数）：

| 字段 | 来源 | 说明 |
|---|---|---|
| `current_credits` | `GLOB.intense_event_credits` | 当前强度信用点 |
| `next_run` | `DisplayTimeText(SSevents.scheduled - world.time, 1)` | 距下次事件运行的倒计时 |
| `active_players` | `filter_threshold` | 活跃玩家数（存活 + 非 AFK + 人类） |
| `active_multiplier` | `SSevents.active_intensity_multiplier` | 当前乘数 |
| `lowpop_players` / `lowpop_multiplier` | `SSevents.intensity_low_*` | LOWPOP 阈值 / 乘数 |
| `midpop_players` / `midpop_multiplier` | `SSevents.intensity_mid_*` | MIDPOP 阈值 / 乘数 |
| `highpop_players` / `highpop_multiplier` | `SSevents.intensity_high_*` | HIGHPOP 阈值 / 乘数 |

**全局变量**: `GLOB.intense_event_credits`（初始 0）——强度信用点池，全系统唯一信用来源。

---

## 1.8 事件配置全表（ICES_event_config）

**代码**: `modular_nova/modules/ices_events/code/ICES_event_config.dm`（497 行）

以下为对所有 `/datum/round_event_control` 的覆盖全录（共 70 个条目，按源码分组）。权重值为宏数值：HIGH=32 / MED=16 / LOW=8 / VERY_LOW=4 / MIN=2。`intensity_restriction` 仅列出为 TRUE 者。

### 绑架者（Abductors）

| 类型 | weight | max_occurrences | 说明 |
|---|---|---|---|
| `/datum/round_event_control/abductor` | VERY_LOW(4) | **0** | 禁用（权重保留） |

### 外星感染（Alien Infestation）

| 类型 | weight | max_occurrences | 其他 |
|---|---|---|---|
| `/datum/round_event_control/alien_infestation` | MED(16) | **0** | `min_players = 999`、`intensity_restriction = TRUE`（注释：Enable: Disable from config——从配置禁用） |

### 异常（Anomalies）

| 类型 | weight | max_occurrences | 其他 |
|---|---|---|---|
| `anomaly/anomaly_bioscrambler` | LOW(8) | 1 | （自定义行为见 1.9） |
| `anomaly/anomaly_bluespace` | MED(16) | 2 | |
| `anomaly/anomaly_dimensional` | MED(16) | 2 | |
| `anomaly/anomaly_ectoplasm` | MED(16) | 1 | |
| `anomaly/anomaly_flux` | MED(16) | 1 | |
| `anomaly/anomaly_grav` | MED(16) | 2 | 注释：We have other intensities（我们另有其他强度档） |
| `anomaly/anomaly_grav/high` | LOW(8) | 1 | `min_players = 45` |
| `anomaly/anomaly_hallucination` | HIGH(32) | 2 | |
| `anomaly/anomaly_pyro` | MED(16) | 1 | `min_players = 40` |
| `anomaly/anomaly_vortex` | VERY_LOW(4) | 1 | |

### 奥罗拉·凯勒斯（Aurora Caelus）

| 类型 | weight | max_occurrences | 说明 |
|---|---|---|---|
| `/datum/round_event_control/aurora_caelus` | HIGH(32) | 3 | TODO: 让它不消耗事件槽位 |

### 脑损伤（Brain Trauma）

| 类型 | max_occurrences | 说明 |
|---|---|---|
| `/datum/round_event_control/brain_trauma` | **0** | 禁用：会打断场景、通过与医疗舱互动阻碍角色扮演 |

### 品牌智能（Brand Intelligence）

| 类型 | weight | max_occurrences |
|---|---|---|
| `/datum/round_event_control/brand_intelligence` | VERY_LOW(4) | 1 |

### 官僚错误（Bureaucratic Error）

| 类型 | max_occurrences | 说明 |
|---|---|---|
| `/datum/round_event_control/bureaucratic_error` | **0** | 禁用：过于侵入性，应仅限工作人员（staff-only） |

### 摄像头故障（Camera Failure）

| 类型 | weight | max_occurrences | 说明 |
|---|---|---|---|
| `/datum/round_event_control/camera_failure` | LOW(8) | 5 | TODO: 让它不消耗事件槽位 |

### 鲤鱼迁徙（Carp Migration）

| 类型 | weight | max_occurrences |
|---|---|---|
| `/datum/round_event_control/carp_migration` | HIGH(32) | 2 |

### 通信事件（Communications）— 组合权重 16

| 类型 | weight | max_occurrences | 其他 |
|---|---|---|---|
| `communications_blackout` | MED(16) | 3 | |
| `processor_overload` | LOW(8) | 1 | `intensity_restriction = TRUE` |

### 动态微调（Dynamic Tweak）

| 类型 | weight | max_occurrences |
|---|---|---|
| `/datum/round_event_control/dynamic_tweak` | VERY_LOW(4) | **0** |

### 医疗事件（Medical）

| 类型 | weight | max_occurrences | 其他 |
|---|---|---|---|
| `disease_outbreak` | HIGH(32) | 2 | `min_players = 20`、`intensity_restriction = TRUE` |
| `disease_outbreak/advanced` | MED(16) | 1 | `min_players = 25`、`intensity_restriction = TRUE` |
| `fake_virus` | LOW(8) | 1 | |
| `heart_attack` | MED(16) | 4 | |

### 地震（Earthquakes）

| 类型 | max_occurrences | 说明 |
|---|---|---|
| `/datum/round_event_control/earthquake` | **0** | 禁用（源码注释原文）："Yeah lol as if we'd run an event with the sole purpose of griefing the station with no way to prevent it. Nice try."（哈，我们可不会跑一个纯粹为了给空间站添乱、且无法预防的事件。想得美。） |

### 电力事件（Electricity Events）

| 类型 | weight | max_occurrences |
|---|---|---|
| `electrical_storm` | MED(16) | 1 |
| `grid_check` | MED(16) | 1 |

### 虚假警报（False Alarm）

| 类型 | weight | max_occurrences |
|---|---|---|
| `/datum/round_event_control/falsealarm` | LOW(8) | 4 |

### 重力发生器断电（Gravity Generator Blackout）

| 类型 | weight | max_occurrences |
|---|---|---|
| `/datum/round_event_control/gravity_generator_blackout` | MED(16) | 1 |

### 灰潮（Grey Tide）

| 类型 | weight | max_occurrences |
|---|---|---|
| `/datum/round_event_control/grey_tide` | LOW(8) | 2 |

### 离子风暴（Ion Storm）

| 类型 | weight | max_occurrences |
|---|---|---|
| `/datum/round_event_control/ion_storm` | MED(16) | 1 |

### 不可移动之杆（Immovable Rod）

| 类型 | weight | max_occurrences | 其他 |
|---|---|---|---|
| `/datum/round_event_control/immovable_rod` | LOW(8) | 1 | `intensity_restriction = TRUE` |

### 市场崩盘（Market Crash）

| 类型 | weight | max_occurrences | 说明 |
|---|---|---|---|
| `/datum/round_event_control/market_crash` | MED(16) | 1 | TODO: 让它不消耗事件槽位 |

### 群体幻觉（Mass Hallucination）

| 类型 | weight | max_occurrences | 说明 |
|---|---|---|---|
| `/datum/round_event_control/mass_hallucination` | LOW(8) | 1 | TODO: 让它不消耗事件槽位 |

### 流星 / 太空尘埃（Meteors / Space Dust）

| 类型 | weight | max_occurrences | 其他 |
|---|---|---|---|
| `meteor_wave` | MIN(2) | **0** | `intensity_restriction = TRUE` |
| `meteor_wave/ices` | LOW(8) | 1 | 定制版（见 1.10） |
| `meteor_wave/threatening` | MIN(2) | **0** | 注释：无预设，我们有自己的自定义预设（见上） |
| `meteor_wave/catastrophic` | MIN(2) | **0** | |
| `meteor_wave/meaty` | MIN(2) | **0** | |
| `stray_meteor` | MIN(2) | **0** | `intensity_restriction = TRUE` |
| `space_dust` | LOW(8) | 1 | |
| `space_dust/major_dust` | LOW(8) | 1 | `intensity_restriction = TRUE` |
| `sandstorm` | MED(16) | 1 | `min_players = 45`、`intensity_restriction = TRUE` |

### 老鼠迁徙（Mice Migration）

| 类型 | weight | max_occurrences |
|---|---|---|
| `/datum/round_event_control/mice_migration` | MED(16) | 1 |

### 霉菌（Moldies / Mold）

| 类型 | weight | max_occurrences | 其他 |
|---|---|---|---|
| `/datum/round_event_control/mold` | LOW(8) | 1 | `intensity_restriction = TRUE` |

### 孤狼行动（Lone Op / Operative）

| 类型 | max_occurrences | 说明 |
|---|---|---|
| `/datum/round_event_control/operative` | **0** | 禁用：没有对应政策（policy），加入政策后会重新启用 |

### 辛迪加传送门风暴（Syndicate Portal Storm）

| 类型 | weight | max_occurrences |
|---|---|---|
| `portal_storm_syndicate` | MED(16) | 2 |
| `portal_storm_narsie` | MED(16) | 1 |

### 辐射（Radiation）

| 类型 | weight | max_occurrences | 其他 |
|---|---|---|---|
| `radiation_leak` | MED(16) | 2 | 注释：禁用：设计不直观且与本站不兼容（数值仍保留） |
| `radiation_storm` | — | **0** | `intensity_restriction = TRUE` |

### 清洗器堵塞（Scrubber Clogs / Vent Clog）

| 类型 | weight | max_occurrences |
|---|---|---|
| `vent_clog` | HIGH(32) | 1 |
| `vent_clog/major` | HIGH(32) | 1 |
| `vent_clog/critical` | MED(16) | 1 |
| `vent_clog/strange` | MED(16) | 1 |

### 清洗器溢出（Scrubber Overflow）

| 类型 | weight | max_occurrences | 其他 |
|---|---|---|---|
| `scrubber_overflow` | MED(16) | 1 | |
| `scrubber_overflow/threatening` | LOW(8) | 1 | |
| `scrubber_overflow/catastrophic` | — | **0** | |
| `scrubber_overflow/ices` | MED(16) | — | 定制版（见 1.11） |

### 人类级智能（Human-level Intelligence / Sentience）

| 类型 | weight | max_occurrences |
|---|---|---|
| `/datum/round_event_control/sentience` | MED(16) | 2 |

### 穿梭机事件（Shuttle Events）

| 类型 | weight | max_occurrences |
|---|---|---|
| `shuttle_catastrophe` | MED(16) | 1 |
| `shuttle_insurance` | HIGH(32) | 1 |
| `shuttle_loan` | MED(16) | 2 |

### 太空藤蔓（Spess Vines / Spacevine）

| 类型 | weight | max_occurrences |
|---|---|---|
| `/datum/round_event_control/spacevine` | MED(16) | 2 |

### 流浪货舱（Stray Cargo Pods）— 组合权重 24

| 类型 | weight | max_occurrences |
|---|---|---|
| `stray_cargo` | MED(16) | 3 |
| `stray_cargo/syndicate` | HIGH(32) | 3 |

### 超物质激涌（Supermatter Surge）

| 类型 | weight | max_occurrences |
|---|---|---|
| `/datum/round_event_control/supermatter_surge` | MED(16) | 1 |

### 电车故障（Tram Malfunction）

| 类型 | weight | 说明 |
|---|---|---|
| `/datum/round_event_control/tram_malfunction` | HIGH(32) | 仅在 Tramstation 地图运行，否则掷出另一事件 |

### 智慧奶牛（Wisdom Cow）

| 类型 | weight | max_occurrences | 说明 |
|---|---|---|---|
| `/datum/round_event_control/wisdomcow` | LOW(8) | 1 | TODO: 让它不消耗事件槽位 |

### 虫洞（Wormholes）

| 类型 | weight | max_occurrences |
|---|---|---|
| `/datum/round_event_control/wormholes` | MED(16) | 2 |

---

## 1.9 自定义事件：生物扰乱异常（Bioscrambler）

**代码**: `modular_nova/modules/ices_events/code/events/ev_bioscrambler.dm`（60 行）

对标准生物扰乱异常（bioscrambler anomaly）事件的修改：**异常无法穿过墙壁，且生成点被限制在工程部可快速抵达的公共区域**。

`/obj/effect/anomaly/bioscrambler`（异常本体修改）：
- `pass_flags = PASSTABLE | PASSGLASS | PASSMACHINE | PASSDOORS`（可穿过桌子/玻璃/机器/门，但不可穿墙）
- `range = 4`（影响范围 4 格）
- `pulse_delay = 20 SECONDS`（脉冲间隔 20 秒）

`/datum/round_event/anomaly/anomaly_bioscrambler` 覆写：
- `setup()`：有 `spawn_location` 时 `impact_area = get_area(spawn_location)`；否则 `impact_area = placer.find_bioscrambler_area()`。
- `announce(fake)`：`impact_area` 为空时重新调用 `find_bioscrambler_area()`；`priority_announce(LANG("datum.23757fbf", ...), "Anomaly Alert", ANNOUNCER_ANOMALIES)`（异常警报公告）。

`/datum/anomaly_placer/proc/find_bioscrambler_area()` —— 选取安全的生物扰乱异常生成区域：
- **包含清单**（`bioscrambler_inclusions`）：`/area/station/commons`、`/area/station/hallway/primary`、`/area/station/hallway/secondary`（及其子类型）。
- **排除清单**（`bioscrambler_exclusions`）：
  - `/area/station/commons/dorms`（宿舍）
  - `/area/station/commons/storage`（仓库）
  - `/area/station/commons/toilet`（厕所）
  - `/area/station/commons/vacant_room`（空置房间）
  - `/area/station/hallway/secondary/command`（指挥走廊）
  - `/area/station/hallway/secondary/construction`（施工走廊）
  - `/area/station/hallway/secondary/dock`（泊位走廊）
  - `/area/station/hallway/secondary/exit/escape_pod`（逃生舱出口走廊）
  - `/area/station/hallway/secondary/recreation`（娱乐走廊）
  - `/area/station/hallway/secondary/service`（服务走廊）
  - `/area/station/hallway/secondary/spacebridge`（太空桥走廊）
- 流程：`bioscrambler_areas = inclusions - exclusions`；`log_game` 记录缓存区域数；`typecache_filter_list(GLOB.areas, ...)` 过滤出实际存在的区域；无有效区域 → `CRASH("No valid areas for anomaly found.")`；`pick()` 随机选取一个；`log_game` 记录选中区域名；`get_area_turfs()` 无有效 turf → `CRASH`；返回该区域。

---

## 1.10 自定义事件：流星波（Meteor Wave: ICES）

**代码**: `modular_nova/modules/ices_events/code/events/ev_meteors.dm`（71 行）

常量宏：

| 宏 | 值 |
|---|---|
| `METEOR_WAVE_MIN_NOTICE` | 180 EVENT_SECONDS（最少预警 180 秒） |
| `METEOR_WAVE_MAX_NOTICE` | 300 EVENT_SECONDS（最多预警 300 秒） |
| `METEOR_WAVE_DURATION` | 76 EVENT_SECONDS（事件持续 76 秒） |
| `METEOR_WAVE_NORMAL_WEIGHT` | 85（普通波权重） |
| `METEOR_WAVE_THREATENING_WEIGHT` | 15（威胁波权重） |
| `METEOR_TICKS_BETWEEN_WAVES` | 3（每 3 tick 一波） |
| `METEORS_PER_WAVE` | 5（每波 5 颗流星） |

`/datum/round_event_control/meteor_wave/ices`（事件控制）：
- `name = "Meteor Wave: ICES"`；`typepath = /datum/round_event/meteor_wave/ices`
- `weight = 16`；`min_players = 40`；`max_occurrences = 1`
- `earliest_start = 75 MINUTES`（最早 75 分钟开始）
- `category = EVENT_CATEGORY_SPACE`；`map_flags = EVENT_SPACE_ONLY`（仅太空地图）
- `description = "A meteor wave, severity is a surprise!"`（一场流星波，强度是个惊喜！）

`/datum/round_event/meteor_wave`：`announce_when = 1`（触发后 1 tick 即公告）。

`/datum/round_event/meteor_wave/ices`（事件本体）：
- `wave_name = null`（初始未定）。
- **`determine_wave_type()` 强度判定**：
  1. `filter_threshold = get_active_player_count(alive_check = FALSE, afk_check = TRUE, human_check = FALSE)`；
  2. 活跃玩家 `< 75` → `wave_name = "normal"`（普通波）；
  3. 若当天为万圣节（`check_holidays(HALLOWEEN)`）→ `wave_name = "spooky"`（幽灵波），`log_game` "EVENT: Meteor Wave: ICES is spookier than usual!"，并向死灵频道广播 "Something feels awfully spooky today!"；
  4. 仍未确定 → `pick_weight(normal = 85, threatening = 15)` 随机（85% 普通 / 15% 威胁）；
  5. switch 映射波型：`normal` → `GLOB.meteors_normal`；`threatening` → `GLOB.meteors_threatening`；`spooky` → `GLOB.meteorsSPOOKY`；未知名 → `stack_trace` + `kill()`（终止事件）；
  6. `log_game` 记录请求的强度。
- **`New()`**：`start_when = rand(180, 300)`；`end_when = start_when + 76`（即预警期随机 3–5 分钟 + 76 秒持续）。
- **`announce(fake)`**：`priority_announce(LANG("datum.e261218e", list(start_when * 2)), "Meteor Alert", ANNOUNCER_METEORS)`；若为 `threatening` 或 `spooky` → 异步调用 `SSsecurity_level.minimum_security_level(SEC_LEVEL_ORANGE, TRUE, FALSE)`（安保等级升到橙色 + 工程覆盖）。
- **`tick()`**：`ISMULTIPLE(activeFor, 3)` 时 `spawn_meteors(5, wave_type)`（每 3 tick 生成一波 5 颗流星）。

---

## 1.11 自定义事件：清洗器溢出（Scrubber Overflow: ICES）

**代码**: `modular_nova/modules/ices_events/code/events/ev_scrubbers.dm`（87 行）

`/datum/round_event_control/scrubber_overflow/ices`（事件控制）：
- `name = "Scrubber Overflow: ICES"`；`typepath = /datum/round_event/scrubber_overflow/ices`
- `weight = 10`；`max_occurrences = 2`；`earliest_start = 20 MINUTES`
- `description = "The scrubbers release a tide of moderately harmless froth, custom reagent set."`（清洗器释放出一波中等无害的泡沫，自定义试剂组。）
- `min_wizard_trigger_potency = 0`；`max_wizard_trigger_potency = 4`（可被巫师法术以 0–4 威力触发）

`/datum/round_event/scrubber_overflow/ices`（事件本体）：
- `danger_chance = 0`（无危险试剂）；`reagents_amount = 40`（每台清洗器 40 单位）；`overflow_probability = 70`（每台清洗器 70% 概率溢出）。

**试剂白名单**（`reagent_whitelist`，共 37 种）：

| 类别 | 试剂 |
|---|---|
| 通用 | 血（blood）、蓝空间（bluespace）、碳（carbon）、五彩试剂（colorful_reagent）、本征态（eigenstate）、引力素（gravitum）、染发剂（hair_dye）、过氧化氢（hydrogen_peroxide）、润滑剂（lube）、pax、塑料聚合物（plastic_polymers）、太空清洁剂（space_cleaner）、太空毒品（drug/space_drugs）、隐生质（cryptobiolin）、随机闪光剂（glitter/random） |
| 霓虹地毯 | 青（carpet/neon/simple_cyan）、粉（simple_pink）、白（simple_white）、黄（simple_yellow） |
| 食物/饮品 | 阿斯巴甜（astrotame）、焦糖（caramel）、炭（char）、浓缩辣椒素（condensedcapsaicin）、奶油（cream）、医生快乐（doctor_delight）、面粉（flour）、热可可（hot_coco）、欢笑气体（laughter）、营养物（nutriment）、盐（salt）、苏打水（sodawater）、锡光（tinlux）、酸奶（yoghurt） |
| 酒精 | 防冻剂（ethanol/antifreeze）、比普西粉碎（ethanol/beepsky_smash）、费内特可乐（ethanol/fernet_cola）、糖冲（ethanol/sugar_rush） |

`setup()`（筛选溢出的清洗器）：
1. 遍历 `SSmachines` 中所有 `/obj/machinery/atmospherics/components/unary/vent_scrubber`（含子类型）；
2. 跳过：无 turf 的、不在站内层（`!is_station_level(z)`）的、已焊接（`welded`）的、位于超物质室区域（`/area/station/engineering/supermatter/room`、`/area/station/engineering/supermatter`）的；
3. `prob(70)` 未通过则跳过；通过则加入 `scrubbers` 列表；
4. 列表为空 → `kill()`（事件自我终止）。

`start()`（释放泡沫）：
- 对每台入选清洗器：新建 `/datum/reagents(reagents_amount = 40)`，`my_atom = vent`；
- 有 `forced_reagent_type` 时强制注入该试剂 40 单位，否则从白名单 `pick()` 随机一种注入 40 单位；
- `create_foam(/datum/effect_system/fluid_spread/foam/scrubber, 40)` 生成 40 单位清洗器泡沫（变体见 1.12）。

---

## 1.12 清洗器泡沫特效（ef_foam）

**代码**: `modular_nova/modules/ices_events/code/effects/ef_foam.dm`（12 行）

- `SCRUBBER_REAGENT_SCALE = 0.75`（试剂缩放系数）。
- `/obj/effect/particle_effect/fluid/foam/intermediate_life` —— 中间寿命泡沫：`lifetime = 4 SECONDS`（持续 4 秒后消散）。
- `/datum/effect_system/fluid_spread/foam/scrubber` —— 清洗器泡沫扩散效果：`reagent_scale = 0.75`，`effect_type = /obj/effect/particle_effect/fluid/foam/intermediate_life`。

---

## 1.13 角色扮演参与检查（engaged_role_play_check）

**代码**: `modular_nova/modules/ices_events/code/events/ev_roleplay_check.dm`（18 行）

`/proc/engaged_role_play_check(mob/living/carbon/human/player, station = TRUE, dorms = TRUE)` —— 检查玩家是否满足条件以被排除出事件目标选择；返回 FALSE 表示该玩家不适合作为事件目标：
1. 取 `player_turf = get_turf(player)`、`player_area = get_area(player_turf)`。
2. `station = TRUE` 时：`player_turf` 为 null 则检查 `player.z`，否则检查 `player_turf.z` —— 不在站内层（`!is_station_level`）→ 返回 FALSE。
3. `dorms = TRUE` 时：玩家位于 `/area/station/commons/dorms`（宿舍区）→ 返回 FALSE（不打扰休息中的玩家）。
4. 否则返回 TRUE。

---

# 第二卷 · 战术路障系统（Deployable Barricades）

> 五种路障：雪（snow）、护栏（fencing/guard rail）、木（wooden）、金属（metal）、塑钢（plasteel）。
> 核心机制：边界路障（ON_BORDER）、可攀爬（climbable）、可旋转、可接线（金属）、三阶段建造状态（金属/塑钢）、装甲升级（金属）、快速部署物品 C.U.C.K.S。
> 图标资源: `modular_nova/modules/barricades/icons/barricade.dmi`。

---

## 2.1 模块总览与基类属性

**代码**: `modular_nova/modules/barricades/code/barricade.dm`（842 行，唯一代码文件）

`/obj/structure/deployable_barricade`（所有路障的基类）：

| 属性 | 值 | 说明 |
|---|---|---|
| `icon` | `'modular_nova/modules/barricades/icons/barricade.dmi'` | 图标 |
| `anchored` | TRUE | 初始固定 |
| `density` | TRUE | 初始阻挡 |
| `layer` | `BELOW_OBJ_LAYER` | 物件下层（朝南时提升，见 2.2） |
| `flags_1` | `ON_BORDER_1` | 边界物件（只占所在格子的一个方向） |
| `obj_flags` | `CAN_BE_HIT \| BLOCKS_CONSTRUCTION_DIR \| IGNORE_DENSITY` | 可被击中 / 阻挡该方向的建造 / 忽略密度 |
| `max_integrity` | 100 | 基础最大完整度 |
| `pass_flags_self` | `PASSSTRUCTURE \| LETPASSTHROW` | 允许结构穿过、允许投掷物穿过自己 |

基类变量：

| 变量 | 默认值 | 说明 |
|---|---|---|
| `stack_type` | null | 拆除时掉落的板材堆类型（无则不掉落） |
| `stack_amount` | 5 | 满血拆除时掉落的板材数量 |
| `destroyed_stack_amount` | 0 | 被摧毁时掉落非零板材数（0 表示按血量比例） |
| `barricade_type` | `"barricade"` | 图标名段（"metal"/"plasteel"/"snow"/"railing"/"wooden"） |
| `can_change_dmg_state` | TRUE | 是否有损坏贴图状态 |
| `closed` | FALSE | 是否关闭（可开关的路障可越过） |
| `can_wire` | FALSE | 该类型可否接线（通电） |
| `is_wired` | FALSE | 当前是否已接线 |

---

## 2.2 基类交互与机制

**代码**: `modular_nova/modules/barricades/code/barricade.dm`（基类部分，第 3–236 行）

### 初始化与生命周期
- `Initialize(mapload)`：`update_icon()`；`AddElement(/datum/element/connect_loc, {COMSIG_ATOM_EXIT → on_try_exit})`；`AddElement(/datum/element/climbable)`（可攀爬）；注册 `COMSIG_ATOM_INTEGRITY_CHANGED → run_integrity`。
- `run_integrity()`：信号处理器，`update_appearance()`（刷新外观）。
- `Destroy()`：注销 `COMSIG_ATOM_INTEGRITY_CHANGED`。

### 检查（Examine）
- 未接线且 `can_wire = TRUE`：提示可以用电缆接线。
- 已接线（`is_wired`）：提示已接线。

### 阻挡离开（on_try_exit）
`COMSIG_ATOM_EXIT` 处理：以下情况不阻挡——
1. 离开者就是路障本身；
2. 离开方向不含路障方向（`!(direction & dir)`）；
3. 路障无密度；
4. 离开者在投掷中（`throwing`）；
5. 离开者移动类型含 `PHASING | FLYING | FLOATING`（相位/飞行/漂浮）；
6. 离开者推力 `move_force >= MOVE_FORCE_EXTREMELY_STRONG`（极强推力，如机甲）。
否则：`leaving.Bump(src)` + 返回 `COMPONENT_ATOM_BLOCK_EXIT`（阻挡离开）。

### 通行判定（CanPass）
- **投射物**（`/obj/projectile`）：未固定 → 通过；已关闭（`closed`）→ 通过；射手与路障相邻（`Adjacent(firer)`）→ 通过；`prob(25)`（25% 概率）→ 通过；否则阻挡。
- **其他移动体**：方向匹配（`border_dir & dir`）且未关闭 → 仅当 `mover.throwing` 或移动类型含 `FLYING | FLOATING` 时通过；方向不匹配 → 直接通过。

### 接线（Wire）
- `item_interaction` 电缆（`/obj/item/stack/cable_coil`）且 `can_wire`：消耗 **5 格**电缆 → `wire()`，返回成功；电缆不足返回 `ITEM_INTERACT_BLOCKING`。
- `wire()`：`can_wire = FALSE`、`is_wired = TRUE`、`modify_max_integrity(max_integrity + 50)`（**最大完整度 +50**）、刷新图标。
- `wirecutter_act`（剪线钳）：未接线返回 FALSE；`do_after(2 SECONDS)` 后播放 wirecutter.ogg；`modify_max_integrity(-50)`（完整度 -50 还原）、`can_wire = TRUE`、`is_wired = FALSE`、刷新图标。

### 拆除与掉落（atom_deconstruct）
- 参数 `disassembled`：非拆解（被摧毁）且 `destroyed_stack_amount` 非零 → 掉落 `destroyed_stack_amount`；否则掉落 `round(stack_amount × (当前完整度 / 最大完整度))`（按剩余血量比例，全毁为 0）。
- 有掉落量时生成 `new stack_type(loc, stack_amt)`。

### 爆炸抗性（ex_act，基类）
| 爆炸等级 | 伤害 |
|---|---|
| `EXPLODE_DEVASTATE`（毁灭） | 提示危险消息 + `deconstruct(FALSE)`（直接摧毁） |
| `EXPLODE_HEAVY`（重） | `take_damage(rand(33, 66))`（33–66 随机） |
| `EXPLODE_LIGHT`（轻） | `take_damage(rand(10, 33))`（10–33 随机） |

### 图标与层级（update_icon / update_overlays）
- 损坏状态按血量百分比：≤25% → `3`；25–50% → `2`；50–75% → `1`；>75% → `0`。
- 未关闭：`can_change_dmg_state` → `"[barricade_type]_[状态]"`，否则 `"[barricade_type]"`；层级：朝 **SOUTH** → `ABOVE_MOB_LAYER`（压过人物），朝 **NORTH** → `初始层 - 0.01`，其他 → 初始层；未固定 → 初始层。
- 已关闭：`"[barricade_type]_closed_[状态]"` 或 `"[barricade_type]_closed"`；层级 = `OBJ_LAYER`。
- 叠加层（overlays）：已接线 → `"[barricade_type]_wire"`（未关闭）/ `"[barricade_type]_closed_wire"`（关闭）。
- `setDir(newdir)`：转向后刷新图标。

### 旋转（Rotate）
| 动词 | 名称 | 动作 |
|---|---|---|
| `rotate` | 逆时针旋转路障 < | `setDir(turn(dir, 90))` |
| `revrotate` | 顺时针旋转路障 > | `setDir(turn(dir, 270))` |

- 均在 `oview(1)`（邻格）可用，分类 "Object"；已固定（`anchored`）时提示"无法旋转"并拒绝。
- `attack_hand_secondary`（副手攻击）＝顺时针旋转（`turn(dir, 270)`），同样要求未固定。

### 动物/异形攻击（attack_animal）

- `attack_animal(mob/user)`：**转发给 `attack_alien(user)`**——动物/异形攻击路障的入口（`barricade.dm` L108-109）。
- 本模块无特化处理，行为委托 TG 基类（动物攻击按常规伤害计算）。

---

## 2.3 雪路障（Snow Barricade）

**代码**: `modular_nova/modules/barricades/code/barricade.dm`（第 242–252 行）

`/obj/structure/deployable_barricade/snow`：

| 属性 | 值 |
|---|---|
| `name` | "snow barricade"（雪路障） |
| `desc` | "A snowdrift, carefully rammed with palms up to a relatively solid state. The architect in your head believes that it is better than nothing. In principle, you agree with him."（一堆雪堆，用手掌仔细夯实到相对结实的状态。你脑海中的建筑师认为有总比没有好。原则上你同意他。） |
| `icon_state` | `snow_0` |
| `barricade_type` | `"snow"` |
| `max_integrity` | **75** |
| `stack_type` | `/obj/item/stack/sheet/mineral/snow`（雪块） |
| `stack_amount` | **2** |
| `destroyed_stack_amount` | 0（按血量比例掉落） |
| `can_wire` | FALSE（不可接线） |
| `custom_materials` | 雪 × 2 块（`SHEET_MATERIAL_AMOUNT * 2`） |

特点：最脆的路障，可被雪块板材建造/回收。

---

## 2.4 护栏（Fencing / Guard Rail）

**代码**: `modular_nova/modules/barricades/code/barricade.dm`（第 258–281 行）

`/obj/structure/deployable_barricade/guardrail`：

| 属性 | 值 |
|---|---|
| `name` | "fencing"（围栏） |
| `desc` | "A small barricade made from metal posting, designed to stop you from going places you aren't supposed to."（用金属立柱制成的小路障，用于阻止你去不该去的地方。） |
| `icon_state` | `railing_0` |
| `barricade_type` | `"railing"` |
| `max_integrity` | **150** |
| `armor_type` | `/datum/armor/deployable_barricade_guardrail` |
| `stack_type` | `/obj/item/stack/rods`（金属杆） |
| `destroyed_stack_amount` | **2**（被摧毁时固定掉 2 根） |
| `stack_amount` | 5（继承基类） |
| `pass_flags_self` | `PASSSTRUCTURE`（仅结构，不放行投掷物） |
| `can_wire` | FALSE |
| `custom_materials` | 铁 × 1 块 |

`/datum/armor/deployable_barricade_guardrail` 装甲：

| 类型 | 值 |
|---|---|
| melee（近战） | 35 |
| bullet（子弹） | 50 |
| laser（激光） | 50 |
| energy（能量） | 100 |
| bomb（爆炸） | 10 |

- `update_icon` 特化：朝 **NORTH** 时 `pixel_y = 11`（贴图向上偏移，贴合墙体感）。

---

## 2.5 木路障（Wooden Barricade）

**代码**: `modular_nova/modules/barricades/code/barricade.dm`（第 287–323 行）

`/obj/structure/deployable_barricade/wooden`：

| 属性 | 值 |
|---|---|
| `name` | "wooden barricade"（木路障） |
| `desc` | "A wall hammered out of wooden planks may not even look very strong, but it still provides some protection."（用木板钉成的墙也许看起来并不结实，但它仍能提供一些保护。） |
| `icon_state` | `wooden` |
| `barricade_type` | `"wooden"` |
| `max_integrity` | **100** |
| `layer` | `OBJ_LAYER`（物件层） |
| `stack_type` | `/obj/item/stack/sheet/mineral/wood`（木板） |
| `stack_amount` | **2** |
| `destroyed_stack_amount` | **1**（摧毁时固定掉 1 块） |
| `can_change_dmg_state` | FALSE（无损坏贴图，只显示 `wooden`） |
| `can_wire` | FALSE |
| `custom_materials` | 木 × 5 块 |

**木板修复**（`item_interaction` 特化）：
1. 用木板（`/obj/item/stack/sheet/mineral/wood`）右键交互；
2. 已满血（`get_integrity() >= max_integrity`）→ 直接返回；
3. 木板不足 1 块 → 警告；
4. `do_after(20)`（20 tick / 2 秒）后再次检查满血；
5. 消耗 **1 块木板** → `repair_damage(max_integrity)`（**直接回满**）。

---

## 2.6 金属路障（Metal Barricade）

**代码**: `modular_nova/modules/barricades/code/barricade.dm`（第 329–660 行）

### 状态宏与升级宏

| 宏 | 值 |
|---|---|
| `BARRICADE_METAL_LOOSE` | **0**（松散/未固定） |
| `BARRICADE_METAL_ANCHORED` | **1**（锚定/拆了保护板） |
| `BARRICADE_METAL_FIRM` | **2**（牢固/完整建造） |
| `BARRICADE_TYPE_BOMB` | `"explosion-proof armor"`（防爆装甲） |
| `BARRICADE_TYPE_MELEE` | `"ballistic armor"`（弹道装甲） |
| `BARRICADE_TYPE_ACID` | `"anti-acid armor"`（防酸装甲） |
| `BARRICADE_UPGRADE_REQUIRED_SHEETS` | **2**（升级所需铁板数） |

### 基础属性

`/obj/structure/deployable_barricade/metal`：

| 属性 | 值 |
|---|---|
| `name` | "metal barricade"（金属路障） |
| `desc` | "A durable and easily mounted barricade made from metal plates, often used for rapid fortification. Repairing it requires a welder."（由金属板制成的耐用且易于架设的路障，常用于快速筑垒。修复它需要焊枪。） |
| `icon_state` | `metal_0` |
| `barricade_type` | `"metal"` |
| `max_integrity` | **200** |
| `armor_type` | `/datum/armor/deployable_barricade_metal` |
| `stack_type` | `/obj/item/stack/sheet/iron`（铁板） |
| `stack_amount` | **2** |
| `destroyed_stack_amount` | **1** |
| `can_wire` | TRUE（可接线） |
| `portable_type` | `/obj/item/quickdeploy/barricade`（折叠后的可携带物） |
| `build_state` | `BARRICADE_METAL_FIRM`（初始：完整建造） |
| `barricade_upgrade_type` | null（无升级） |
| `repair_amount` | **2**（铁板修复消耗） |
| `can_upgrade` | TRUE（可升级） |
| `custom_materials` | 铁 × 2 块 |

`/datum/armor/deployable_barricade_metal` 装甲：

| 类型 | 值 |
|---|---|
| bio（生化） | 80 |
| fire（火焰） | 80 |
| acid（酸） | 40 |
| bomb（爆炸） | 20 |

### 折叠收纳（click_alt）
条件：有 `portable_type`、未固定、无升级、满血（否则分别警告）；`do_after(5 SECONDS)` 后再次校验，播放 ratchet.ogg → `fold_up()`：在脚下生成 `new portable_type` 并 `qdel(src)`（路障变成可携带的 C.U.C.K.S 物品）。

### 升级装甲（attempt_barricade_upgrade）
前提：无现有升级、满血、铁板 ≥ 2。
1. 弹出径向菜单（`show_radial_menu`）三选一：防爆（`explosive_obj` 图标）/ 弹道（`brute_obj`）/ 防酸（`burn_obj`）；
2. `do_after(2 SECONDS)`；消耗 **2 块铁板**；
3. 效果：
   - **防爆**（BOMB）：`set_armor_rating(BOMB, min(当前 + 50, 100))`（爆炸抗性 +50，上限 100）；
   - **弹道**（MELEE）：`MELEE + 30` 且 `BULLET + 30`（近战与子弹各 +30）；
   - **防酸**（ACID）：`ACID + 20`；
4. `barricade_upgrade_type = choice`；播放 screwdriver.ogg；刷新图标。
5. 升级叠加层（按损坏状态）：防爆 → `+explosive_upgrade_[状态]`；弹道 → `+brute_upgrade_[状态]`；防酸 → `+burn_upgrade_[状态]`。

### 修复
- **铁板修复**（`item_interaction`）：满血时不修复；血量 > 30% 且有升级资格时优先走升级；铁板 < 2 警告；`do_after(2 SECONDS)` → 消耗 **2 块铁板** → `repair_damage(max_integrity × 0.3)`（修复 30% 最大完整度）。
- **焊枪修复**（`welder_act`）：焊枪必须开启；血量 ≤ 30% → "损坏太严重"警告；满血 → "无需修复"；`do_after(5 SECONDS)` → 消耗 **2 单位焊枪燃料**（`welding_tool.use(2)`）→ `repair_damage(150)`（固定修复 150 点）。播放 welder2.ogg。

### 三阶段建造状态（Build States）
完整状态机（金属与塑钢共用 `BARRICADE_METAL_*` 宏）：

| 状态 | 值 | 特征 |
|---|---|---|
| `BARRICADE_METAL_FIRM` | 2 | 完整建造（保护板盖住螺栓） |
| `BARRICADE_METAL_ANCHORED` | 1 | 保护板已拆（螺栓裸露） |
| `BARRICADE_METAL_LOOSE` | 0 | 螺栓已松（可拆解/搬运） |

**螺丝刀（screwdriver_act）**：
- `ANCHORED` → 装回保护板 → `FIRM`（do_after 1 秒）；
- `FIRM` → 拆下保护板露出螺栓 → `ANCHORED`（do_after 1 秒）。

**扳手（wrench_act）**：
- `ANCHORED` → 松开锚定螺栓 → `LOOSE`，`anchored = FALSE`，`modify_max_integrity(初始 max_integrity × 0.5)`（金属为 **100**），刷新图标（未固定改变层级）；
- `LOOSE` → 重新紧固 → 要求脚下为开放 turf（`isopenturf`）且同格同向无其他路障；`ANCHORED`，`anchored = TRUE`，`modify_max_integrity(初始 max_integrity)`（金属还原 **200**）。

**撬棍（crowbar_act）**：
- `LOOSE` → 拆开整个路障：do_after 5 秒 → `deconstruct(TRUE)`（按血量掉落铁板）；
- `FIRM` 且**有升级** → 移除升级：防爆 `BOMB - 50`（下限 0）；弹道 `MELEE - 30`、`BULLET - 30`；防酸 `ACID - 20`；返还 **2 块铁板**（`new /obj/item/stack/sheet/iron(loc, 2)`）；`barricade_upgrade_type = null`。do_after 5 秒，播放 crowbar.ogg / deconstruct.ogg。
- `FIRM` 且无升级 → 提示"没有可移除的升级"。

### 爆炸抗性（metal 特化）

| 爆炸等级 | 伤害 |
|---|---|
| `EXPLODE_DEVASTATE` | `rand(400, 600)` |
| `EXPLODE_HEAVY` | `rand(150, 350)` |
| `EXPLODE_LIGHT` | `rand(50, 100)` |

（金属路障不会被直接摧毁，而是承受高额随机伤害。）

### 检查（examine 特化）
- 按 `build_state` 提示当前建造状态（牢固 / 锚定 / 松散）；
- 有升级时提示升级类型；
- 有 `portable_type` 时提示可折叠。

---

## 2.7 塑钢路障（Plasteel Barricade）

**代码**: `modular_nova/modules/barricades/code/barricade.dm`（第 667–758 行）

`/obj/structure/deployable_barricade/metal/plasteel`（金属路障子类）：

| 属性 | 值 |
|---|---|
| `name` | "plasteel barricade"（塑钢路障） |
| `desc` | "A strong plasteel barricade, it can be lowered if necessary. Use a welder to repair it."（坚固的塑钢路障，必要时可以放下。用焊枪修复。） |
| `icon_state` | `plasteel_closed_0` |
| `barricade_type` | `"plasteel"` |
| `max_integrity` | **500**（全路障最高） |
| `stack_type` | `/obj/item/stack/sheet/plasteel`（塑钢板） |
| `density` | FALSE（初始不阻挡） |
| `closed` | TRUE（初始关闭） |
| `can_upgrade` | FALSE（不可装甲升级） |
| `portable_type` | `/obj/item/quickdeploy/barricade/plasteel` |
| `linked` | FALSE（是否与相邻塑钢路障联动开合） |
| `toggle_delay` | **2 SECONDS**（开合耗时） |
| `custom_materials` | 塑钢 × 2 块 |

### 开合机制（toggle_open）
- `attack_hand`（空手）：`do_after(toggle_delay = 2 SECONDS)` 后 `toggle_open(null, user)`。
- `toggle_open(state, user)`：`state == closed` 时直接返回；播放 ratchet.ogg；`closed = !closed`、`density = !density`；可见消息（"lowers/raises"放下/升起）。
- **联动**（`linked = TRUE` 时）：遍历四个基本方向（`GLOB.cardinals`），对满足以下条件的相邻塑钢路障同步开合：
  - 方向垂直（本路障朝南北 `dir & (NORTH|SOUTH)` 且对方在东西方向，或反之）；
  - 方向相同（`dir == cade.dir`）；
  - 对方也处于 `linked` 状态。
- **联动叠加层**：满足联动条件且开合状态一致的相邻路障，绘制连接贴图 `"[barricade_type]_[closed|open]_connection_[方向]"`。

### 联动开关（crowbar_act 特化）
- `LOOSE` → 同金属：do_after 5 秒 → `deconstruct(TRUE)` 拆解；
- `FIRM` → `linked = !linked`（切换联动开关），刷新自身及相邻塑钢路障图标。

### 爆炸抗性（plasteel 特化）

| 爆炸等级 | 伤害 |
|---|---|
| `EXPLODE_DEVASTATE` | `rand(450, 650)` |
| `EXPLODE_HEAVY` | `rand(200, 400)` |
| `EXPLODE_LIGHT` | `rand(50, 150)` |

---

## 2.8 快速部署物品（Quickdeploy / C.U.C.K.S）

**代码**: `modular_nova/modules/barricades/code/barricade.dm`（第 760–825 行）

`/obj/item/quickdeploy` —— 快速部署物品模板（"C.U.C.K.S"）：

| 属性 | 值 |
|---|---|
| `name` | "C.U.C.K.S" |
| `desc` | "Compact Universal Complex Kinetic Self-expanding Barricade. Great for deploying quick fortifications."（紧凑型通用复合动能自展开路障。非常适合快速部署防御工事。） |
| `icon` | barricade.dmi |
| `w_class` | `WEIGHT_CLASS_SMALL`（小体积） |
| `delay` | 0（部署耗时） |
| `thing_to_deploy` | null（要部署的物件类型路径） |

机制：
- `examine`：提示可部署为 `initial(thing_to_deploy.name)`（用 `initial()` 读取类型路径名）。
- `attack_self`（右键使用）：提示 + ratchet.ogg → `do_after(delay)` → `can_place(user)` 通过后：在脚下生成 `new thing_to_deploy(get_turf(user))`、`O.setDir(user.dir)`（面向使用者方向）、播放音效、`qdel(src)`（物品消失）。
- `can_place(user)`：`thing_to_deploy` 为 null（错误生成）→ 警告并拒绝；否则返回 TRUE。

**`/obj/item/quickdeploy/barricade`**（金属路障版）：
- `thing_to_deploy = /obj/structure/deployable_barricade/metal`
- `icon_state = "metal"`；`delay = 3 SECONDS`（部署耗时 3 秒）

放置校验（`can_place` 特化）：
1. 脚下必须为开放 turf（`isopenturf`）；
2. `placement_loc.density`（不可放置）→ 拒绝；
3. 遍历所在格物件：密度物件中，非边界（无 `ON_BORDER_1`）→ "没有空间部署"拒绝；边界物件方向 ≠ 使用者方向 → 继续检查；方向相同 → 拒绝；
4. 全部通过 → 成功消息。

**`/obj/item/quickdeploy/barricade/plasteel`**（塑钢路障版）：
- `thing_to_deploy = /obj/structure/deployable_barricade/metal/plasteel`
- `icon_state = "plasteel"`；`delay` 继承 3 SECONDS。

---

## 2.9 路障收纳盒（C.U.C.K.S Box）

**代码**: `modular_nova/modules/barricades/code/barricade.dm`（第 827–842 行）

`/obj/item/storage/barricade`（收纳盒）：

| 属性 | 值 |
|---|---|
| `name` | "C.U.C.K.S box" |
| `desc` | "Contains several deployable barricades."（内含数个可部署路障。） |
| `icon_state` | `box_metal` |
| `w_class` | `WEIGHT_CLASS_NORMAL`（常规体积） |
| `storage_type` | `/datum/storage/barricade` |

`/datum/storage/barricade`：`max_total_storage = 21`（容量 21 格）。

**`PopulateContents()` 出厂内容**（12 件）：
- **3 ×** `/obj/item/quickdeploy/barricade/plasteel`（塑钢快速部署）
- **9 ×** `/obj/item/quickdeploy/barricade`（金属快速部署）

即一盒 = 3 面塑钢路障 + 9 面金属路障的快速部署包。

---

# 附录 · 代码路径索引

## ICES 严寒事件系统 — `modular_nova/modules/ices_events/`

| 文件 | 行数 | 内容 |
|---|---|---|
| `code/ICES_event_config.dm` | 497 | 事件频率宏（64/32/16/8/4/2）、SSevents 子系统扩展（信用点速率 27000）、70 个 round_event_control 覆盖全表 |
| `code/ICES_intensity_credits.dm` | 52 | `change_intensity_credits` 信用点增减/设置（ADD=2/REMOVE=1/STATIC=3）、`intensity_restriction` 变量 |
| `code/ICES_procs.dm` | 20 | `debug_event_schedule` 排程调试（穿梭机/投票期前置检查） |
| `code/ICES_seclevel.dm` | 21 | `event_minimum_security_level` 安保等级挂钩（橙色默认 + 工程覆盖 + 维修通道） |
| `code/ICES_tgui.dm` | 42 | 管理动词"ICES 事件面板"（R_FUN）+ `intensity_credits_panel` tgui 界面 |
| `code/_ICES_globalvars.dm` | 1 | `GLOB.intense_event_credits`（初始 0） |
| `code/effects/ef_foam.dm` | 12 | 清洗器泡沫特效（寿命 4 秒、试剂缩放 0.75） |
| `code/events/ev_bioscrambler.dm` | 60 | 生物扰乱异常定制版（不可穿墙、公共区域生成、范围 4 / 脉冲 20 秒） |
| `code/events/ev_meteors.dm` | 71 | ICES 流星波（预警 180–300 秒、持续 76 秒、每 3 tick 5 颗、85/15 普通/威胁权重、万圣节幽灵波） |
| `code/events/ev_scrubbers.dm` | 87 | ICES 清洗器溢出（40 单位、70% 概率、37 种试剂白名单、最早 20 分钟、权重 10） |
| `code/events/ev_roleplay_check.dm` | 18 | `engaged_role_play_check` 事件目标 RP 检查（站内层 + 排除宿舍） |
| `readme.md` | 16 | 模块说明（ID: ICES_EVENTS、上游 PR #19505、作者 LT3） |

## 战术路障 — `modular_nova/modules/barricades/`

| 文件 | 行数 | 内容 |
|---|---|---|
| `code/barricade.dm` | 842 | 基类 + 雪（75 血）/护栏（150 血）/木（100 血）/金属（200 血）/塑钢（500 血）五种路障、建造状态机、接线（+50 血）、装甲升级（防爆 +50 上限 100 / 弹道 +30/+30 / 防酸 +20）、快速部署 C.U.C.K.S、收纳盒（3 塑钢 + 9 金属） |
| `icons/barricade.dmi` | — | 全部路障图标资源 |
