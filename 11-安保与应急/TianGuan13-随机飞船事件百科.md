# TianGuan13 随机飞船事件百科

> 随机飞船事件（Random Ship Event）模块完整资料。随机飞船会在轮次中尝试联系空间站，由站长（或其代表）决定接受或拒绝；接受后飞船将驶入空间站附近执行其任务（检查、贸易、敌对行动等）。
> 源码根目录：`modular_nova/modules/random_ship_event/`（共 **19 个 .dm 文件，1,984 行**，含 `random_ships/heliostatic_inspectors/` 子模块 1,562 行）。

## 目录

- [1 模块总览](#1-模块总览-module-overview)
- [2 事件流程 Event Flow](#2-事件流程-event-flow)
  - [2.1 事件核心 datum/random_ship_event](#21-事件核心-datumrandom_ship_event)
  - [2.2 回答回调 random_ship_event_answered](#22-回答回调-random_ship_event_answered)
  - [2.3 飞船生成 spawn_random_ship](#23-飞船生成-spawn_random_ship)
  - [2.4 轮次事件 datum/round_event/random_ship_event](#24-轮次事件-datumround_eventrandom_ship_event)
  - [2.5 事件控制 datum/round_event_control/random_ship_event](#25-事件控制-datumround_event_controlrandom_ship_event)
  - [2.6 管理员设置 admin_setupdm](#26-管理员设置-admin_setupdm)
- [3 飞船全录 Ship Registry](#3-飞船全录-ship-registry)
  - [3.1 基础模板 datum/map_template/shuttle/random_ship](#31-基础模板-datummap_templateshuttlerandom_ship)
  - [3.2 HC 巡逻艇模板 hc_police](#32-hc-巡逻艇模板-hc_police)
  - [3.3 HC 舰船区域 Areas](#33-hc-舰船区域-areas)
  - [3.4 舰船设备 Ship Equipment](#34-舰船设备-ship-equipment)
- [4 船员与身份 Crew amp Roles](#4-船员与身份-crew--roles)
  - [4.1 幽灵船员生成器 ship_rolesdm](#41-幽灵船员生成器-ship_rolesdm)
  - [4.2 装备预设 ship_outfitsdm](#42-装备预设-ship_outfitsdm)
  - [4.3 职业 ship_jobdm](#43-职业-ship_jobdm)
  - [4.4 反派数据 ship_antagonistdm](#44-反派数据-ship_antagonistdm)
- [5 HC 检查队 Heliostatic Inspectors](#5-hc-检查队-heliostatic-inspectors)
  - [5.1 事件本体 hc_policedm](#51-事件本体-hc_policedm)
  - [5.2 军官生成器 ghost_spawnerdm](#52-军官生成器-ghost_spawnerdm)
  - [5.3 军官装备 outfitdm](#53-军官装备-outfitdm)
  - [5.4 反派数据 antag_datumdm](#54-反派数据-antag_datumdm)
  - [5.5 检查长主机 inspector_mainframedm](#55-检查长主机-inspector_mainframedm)
  - [5.6 SOP 文档 documentsdm](#56-sop-文档-documentsdm)
  - [5.7 表单模板 templatesdm](#57-表单模板-templatesdm)
  - [5.8 平板包装 flatpackdm](#58-平板包装-flatpackdm)
- [6 常量与名称库 Constants amp Name Pools](#6-常量与名称库-constants--name-pools)

---

## 1 模块总览 Module Overview

模块路径：`modular_nova/modules/random_ship_event/`

| 文件 File | 行数 Lines | 内容 Contents |
|---|---|---|
| `random_ship_event.dm` | 116 | 事件核心 datum、回答回调、飞船生成 proc |
| `random_ship_event_round.dm` | 41 | 轮次事件 `/datum/round_event/random_ship_event` |
| `random_ship_control.dm` | 15 | 事件控制 `/datum/round_event_control/random_ship_event` |
| `admin_setup.dm` | 13 | 管理员事件配置 `/datum/event_admin_setup/listed_options/random_ship` |
| `ships.dm` | 4 | 随机飞船穿梭机模板基类 |
| `ship_roles.dm` | 66 | 幽灵船员生成器（船组/不法商人） |
| `ship_outfits.dm` | 63 | 船员装备预设 + ID 装饰 |
| `ship_job.dm` | 3 | 职业 `/datum/job/ship_crew` |
| `ship_equipment.dm` | 24 | 穿梭机控制台、导航电脑、停靠点 |
| `ship_antagonist.dm` | 77 | 船员反派 datum + 队伍 + 任务目标 |
| `random_ships/heliostatic_inspectors/code/templates.dm` | 391 | HC-IAD 七种表单纸张 |
| `random_ships/heliostatic_inspectors/code/ship.dm` | 151 | HC 巡逻艇模板、区域、炮塔、控制台 |
| `random_ships/heliostatic_inspectors/code/outfit.dm` | 110 | HC 军官装备、PDA、ID、腰带、背包 |
| `random_ships/heliostatic_inspectors/code/inspector_mainframe.dm` | 237 | 检查长主机（货物封锁/警戒状态/SOS） |
| `random_ships/heliostatic_inspectors/code/hc_police.dm` | 55 | HC 检查队事件本体 |
| `random_ships/heliostatic_inspectors/code/ghost_spawner.dm` | 68 | HC 军官幽灵生成器 |
| `random_ships/heliostatic_inspectors/code/antag_datum.dm` | 142 | HC 警察反派 + 7 种目标 |
| `random_ships/heliostatic_inspectors/code/documents.dm` | 405 | SOP 手册、战场指南、词典 |
| `random_ships/heliostatic_inspectors/code/flatpack.dm` | 3 | 量子传送垫平板包装 |

---

## 2 事件流程 Event Flow

### 2.1 事件核心 datum/random_ship_event

源码：`modular_nova/modules/random_ship_event/random_ship_event.dm`（116 行）

**全局列表 Global List**（第 1–2 行）

```dm
GLOBAL_LIST_INIT(random_ship_events, init_random_ship_events())
```

- `GLOB.random_ship_events`：当天（本轮）可能出现所有随机飞船事件的全局列表。**已生成的飞船会被移出列表，防止重复出现（dupes cannot spawn）**。

**宏定义 Macros**（第 4–5 行）

| 宏 | 值 | 含义 |
|---|---|---|
| `POSITIVE_ANSWER` | 1 | 肯定回答（接受） |
| `NEGATIVE_ANSWER` | 2 | 否定回答（拒绝） |

**初始化 proc `init_random_ship_events()`**（第 8–17 行）

- 遍历 `/datum/random_ship_event` 的所有子类型（`subtypesof`）。
- 每个子类型实例化后调用 `can_roll()`；返回 FALSE 则 `qdel` 销毁（整轮无法出现）；TRUE 则加入全局列表。

**核心变量 Vars**（第 20–47 行）

| 变量 | 默认值 | 说明 |
|---|---|---|
| `name` | `"Random Ship Event"` | 事件名（生成反馈用） |
| `ship_name` | — | 从 `random_ships_nova.json` 随机挑选的船名 |
| `ship_template_id` | `"ERROR"` | 加载的飞船模板 ID |
| `ship_name_pool` | `"some_json_key"` | 船名 JSON 列表的键 |
| `message_title` | `"Incoming Transmission"` | 空间站收到的消息标题 |
| `message_content` | `"This is the %SHIPNAME. We are approaching your station."` | 消息内容，`%SHIPNAME` 会被替换为船名 |
| `arrival_announcement` | `"We have arrived at the station."` | 飞船生成后空间站收到的公告 |
| `possible_answers` | `list("Permission granted, you may dock.", "Permission denied, stay away.")` | 空间站可回复的两个选项（第一项接受，第二项拒绝） |
| `response_accepted` | `"Thank you for allowing us to dock."` | 接受后飞船的回复 |
| `response_rejected` | `"Understood, we will not approach."` | 拒绝后飞船的回复 |
| `accepted` | `FALSE` | 是否已被接受 |
| `announcement_color` | `"blue"` | 公告颜色 |

**New()**（第 49–51 行）：`ship_name = pick(strings("random_ships_nova.json", ship_name_pool))` —— 从 JSON 名称池随机取一个船名。

**can_roll()**（第 55–56 行）：是否可在今天掷出。全局列表初始化时调用；返回 FALSE 意味着整轮不会出现。默认返回 `TRUE`。

**generate_message()**（第 59–63 行）：返回一个新的 `datum/comm_message`：

- `built_message_content` = 将 `message_content` 中的 `%SHIPNAME` 替换为 `ship_name`。
- `message.answer_callback = CALLBACK(GLOBAL_PROC, GLOBAL_PROC_REF(random_ship_event_answered), message, src)`。

### 2.2 回答回调 random_ship_event_answered

源码：`random_ship_event.dm` 第 65–75 行

```dm
/proc/random_ship_event_answered(datum/comm_message/message, datum/random_ship_event/event)
```

| 条件 | 行为 |
|---|---|
| `!message?.answered` | 直接 return（未回答） |
| `message.answered == POSITIVE_ANSWER`（=1，接受） | ① `event.accepted = TRUE`；② `priority_announce(event.response_accepted, sender_override = event.ship_name, color_override = event.announcement_color)`；③ 调用 `event.on_accept()`；④ `addtimer(CALLBACK(GLOBAL_PROC, GLOBAL_PROC_REF(spawn_random_ship), event), 1 MINUTES)` —— **1 分钟后生成飞船** |
| 其他（=2，拒绝） | ① 公告 `response_rejected`；② 调用 `event.on_refuse()`（飞船不会生成） |

### 2.3 飞船生成 spawn_random_ship

源码：`random_ship_event.dm` 第 78–101 行

```dm
/proc/spawn_random_ship(datum/random_ship_event/event)
```

1. 前置检查：`if(!event || !event.accepted) return`。
2. 模板查找：`template_key = "random_ship_[event.ship_template_id]"`，从 `SSmapping.shuttle_templates` 取 `datum/map_template/shuttle/random_ship`。
   - 找不到则回退 `"random_ship_default"`；仍找不到则 `CRASH("No valid ship template found for random ship event!")`。
3. 坐标计算：
   - `x = rand(TRANSITIONEDGE, world.maxx - TRANSITIONEDGE - ship.width)`
   - `y = rand(TRANSITIONEDGE, world.maxy - TRANSITIONEDGE - ship.height)`
   - `z = SSmapping.empty_space.z_value`（空置空间 Z 层）
   - turf 不存在则 `CRASH("Random ship event found no turf to load in")`。
4. `ship.load(T)` 加载失败则 `CRASH("Loading random ship failed!")`。
5. 调用 `event.on_ship_spawn()`，随后 `priority_announce(event.arrival_announcement, sender_override = event.ship_name, color_override = event.announcement_color)`。

**可覆写钩子 Hook procs**（第 104–113 行）：

| proc | 触发时机 | 默认行为 |
|---|---|---|
| `on_accept()` | 飞船被接受时 | 无 |
| `on_refuse()` | 飞船被拒绝时 | 无 |
| `on_ship_spawn()` | 飞船实际生成时 | 无 |

### 2.4 轮次事件 datum/round_event/random_ship_event

源码：`modular_nova/modules/random_ship_event/random_ship_event_round.dm`（41 行）

**变量**：

| 变量 | 说明 |
|---|---|
| `ship_type` | 要生成的随机飞船事件类型 |
| `ship_event` | 实例化后的飞船事件 |
| `fakeable` | `FALSE`（不可伪造） |

**announce(fake)**（第 8–9 行）：`priority_announce("An unknown ship is attempting to contact the station.", sender_override = "Automated Traffic Control System")` —— 公告：一艘未知飞船正试图联系空间站（发送方：自动化交通控制系统 Automated Traffic Control System）。

**start()**（第 11–32 行）：
1. 若 `!ship_type`（管理员未指定），遍历 `/datum/random_ship_event` 全部子类型，筛选 `can_roll()` 为 TRUE 的类型加入 `possible_ships`。
2. `ship_type = pick(possible_ships)`；若无可用飞船则**回退默认 `ship_type = /datum/random_ship_event/hc_police`**。
3. `ship_event = new ship_type`。
4. `GLOB.communications_controller.send_message(message, print = TRUE, unique = TRUE)` —— 发送初始通信消息给空间站（`print=TRUE`、`unique=TRUE`）。

**end()**（第 34–37 行）：飞船生成由 `start()` 中设置的定时器处理，**此处不删除 ship_event（定时器仍需使用）**。

**kill()**（第 39–41 行）：同上，**不删除 ship_event**。

### 2.5 事件控制 datum/round_event_control/random_ship_event

源码：`modular_nova/modules/random_ship_event/random_ship_control.dm`（15 行）

| 字段 | 值 | 说明 |
|---|---|---|
| `name` | `"Random Ship Event"` | 事件名 |
| `typepath` | `/datum/round_event/random_ship_event` | 事件类型 |
| `weight` | `10` | 权重 |
| `max_occurrences` | `1` | 每轮最多 1 次 |
| `min_players` | `15` | 最少 15 名玩家 |
| `category` | `EVENT_CATEGORY_SPACE` | 太空类事件 |
| `description` | `"A random ship will attempt to contact the station with unknown intentions."` | 描述：一艘随机飞船将以未知意图尝试联系空间站 |
| `admin_setup` | `list(/datum/event_admin_setup/listed_options/random_ship)` | 管理员配置 |
| `map_flags` | `EVENT_SPACE_ONLY` | 仅限太空地图 |

**preRunEvent()**（第 12–15 行）：`if (SSmapping.is_planetary()) return EVENT_CANT_RUN` —— **行星地图（planetary）无法运行此事件**；否则走默认逻辑。

### 2.6 管理员设置 admin_setup.dm

源码：`modular_nova/modules/random_ship_event/admin_setup.dm`（13 行）

`/datum/event_admin_setup/listed_options/random_ship`：

| 成员 | 值/行为 |
|---|---|
| `input_text` | `"Select Random Ship Event"`（选择随机飞船事件） |
| `normal_run_option` | `"Random Random Ship Event"`（随机随机飞船事件） |
| `get_list()` | 返回 `subtypesof(/datum/random_ship_event)`（全部子类型） |
| `apply_to_event()` | `chosen` 为 null（未选）→ `event.ship_type = null`（由事件随机挑选）；否则 `event.ship_type = chosen` |

**完整事件流程总结**：

```
轮次开始 → 全局列表初始化（can_roll 筛选）
→ 事件控制按权重 10 / 最少 15 人 / 仅太空图 / 每轮 1 次 掷出
→ announce：未知飞船尝试联系空间站（自动化交通控制系统）
→ start：随机挑选事件类型（无有效则默认 hc_police）
→ 发送通信消息（标题/内容/两个回答选项）
→ 站长回答：
   接受(1) → 公告应答 → on_accept() → 1 分钟后 spawn_random_ship
   拒绝(2) → 公告应答 → on_refuse()（不生成飞船）
→ spawn_random_ship：查模板 random_ship_<id>（回退 random_ship_default）
→ 随机坐标（TRANSITIONEDGE 内，空置空间 Z 层）→ load 飞船
→ on_ship_spawn() → 公告到达消息
```

---

## 3 飞船全录 Ship Registry

### 3.1 基础模板 datum/map_template/shuttle/random_ship

源码：`modular_nova/modules/random_ship_event/ships.dm`（4 行）

```dm
/datum/map_template/shuttle/random_ship
	prefix = "_maps/shuttles/nova/"
	port_id = "random_ship"
	who_can_purchase = null
```

| 字段 | 值 |
|---|---|
| `prefix` | `"_maps/shuttles/nova/"`（地图文件前缀） |
| `port_id` | `"random_ship"`（停靠端口 ID） |
| `who_can_purchase` | `null`（不可购买） |

所有随机飞船模板命名规则：`random_ship_<ship_template_id>`（如 `random_ship_hc_police`），实际模板 ID 由 `random_ship_event.ship_template_id` 决定。

### 3.2 HC 巡逻艇模板 hc_police

源码：`random_ships/heliostatic_inspectors/code/ship.dm`（151 行）

**模板定义**：

```dm
/datum/map_template/shuttle/random_ship/hc_police
	suffix = "hc_police"
	name = "random ship (HC Ship)"
```

**停靠点 `/obj/docking_port/mobile/hc_ship`**：

| 字段 | 值 |
|---|---|
| `name` | `"HC LTPV 'Icebreaker'"`（HC 长程巡逻艇“破冰船”） |
| `shuttle_id` | `"hc_ship"` |
| `port_direction` | `NORTH` |
| `preferred_direction` | `NORTH` |
| `callTime` | `1 MINUTES`（呼叫时间 1 分钟） |
| `rechargeTime` | `3 MINUTES`（充能时间 3 分钟） |
| `movement_force` | `list("KNOCKDOWN"=0,"THROW"=0)` |
| `takeoff_sound` | `.../sounds/engine_ignit_int.ogg` |
| `landing_sound` | `.../sounds/env_ship_down.ogg` |

**控制台 `/obj/machinery/computer/shuttle/hc`**（警察穿梭机控制台 police shuttle console）：

| 字段 | 值 |
|---|---|
| `name` | `"police shuttle console"` |
| `icon_screen` | `"syndishuttle"` |
| `icon_keyboard` | `"syndie_key"` |
| `light_color` | `COLOR_SOFT_RED` |
| `shuttleId` | `"hc_ship"` |
| `possible_destinations` | `"hc_ship_custom"` |

**导航电脑 `/obj/machinery/computer/camera_advanced/shuttle_docker/syndicate/pirate/hc`**：

| 字段 | 值 |
|---|---|
| `name` | `"police shuttle navigation computer"`（警察穿梭机导航电脑） |
| `desc` | `"Used to designate a precise transit location for the police shuttle."`（用于指定警察穿梭机的精确过境位置） |
| `lock_override` | `CAMERA_LOCK_STATION` |
| `view_range` | `8.5` |
| `x_offset` | `6` |
| `y_offset` | `15` |
| `shuttleId` | `"hc_ship"` |
| `shuttlePortId` | `"hc_ship_custom"` |
| `rotateLandingSpot()` | 覆写：提示 `LANG("obj.21387008")` 并返回 `FALSE`（**禁止旋转着陆点**） |

**反投射炮塔 `/obj/machinery/porta_turret/syndicate/hc_police`**：

| 字段 | 值 |
|---|---|
| `name` | `"anti-projectile turret"`（反投射物炮塔） |
| `desc` | 自动点防御炮塔，“靠近它可能不是明智之举” |
| `scan_range` | `9` |
| `shot_delay` | `15` |
| `faction` | `list(FACTION_NEUTRAL)`（中立阵营） |
| `icon` | `.../icons/turrets.dmi`（`gun_turret`） |
| `max_integrity` | `250` |
| `stun_projectile` | `/obj/projectile/bullet/ciws` |
| `lethal_projectile` | `/obj/projectile/bullet/ciws` |
| 射击音效 | `.../sounds/shell_out_tiny.ogg`（两种均用） |

`target()` 覆写：即使无法射击也会转向目标（`setDir(get_dir(base, target))`），并连发 3 轮（`shot_delay`、`shot_delay * 2`、`shot_delay * 3` 定时），返回 TRUE。

**弹体 `/obj/projectile/bullet/ciws`**：

| 字段 | 值 |
|---|---|
| `name` | `"anti-projectile salvo"`（反投射齐射） |
| `icon_state` | `"guardian"` |
| `damage` | `15` |
| `armour_penetration` | `10` |

**纪念牌 `/obj/structure/plaque/static_plaque/golden/commission/ks13/hc_police`**：铭文为“HC Sector 09-G-17 Asteroidal Anomaly Orbital Shipworks, Ship OSTs-05 'Hunter Strider' Class Frigate Commissioned 16/01/2566 'Keeping Promises'”（HC 09-G-17 星区小行星异常轨道船坞，OSTs-05“猎人行猎者”级护卫舰，2566 年 1 月 16 日服役，“信守承诺”）。

**检查公告控制台 `/obj/machinery/computer/centcom_announcement/hc_police`**：

| 字段 | 值 |
|---|---|
| `name` | `"inspection announcement console"`（检查公告控制台） |
| `desc` | 用于发布安全检查公告的控制台 |
| `req_access` | `null`（无权限要求） |
| `circuit` | `null` |
| `command_name` | `"Heliostatic Coalition Safety Inspection Team Announcement"`（日静联盟安全检查队公告） |
| `report_sound` | `ANNOUNCER_HC_POLICE` |

**宇航服存储单元 `/obj/machinery/suit_storage_unit/hc_police`**：

| 字段 | 值 |
|---|---|
| `mod_type` | `/obj/item/mod/control/pre_equipped/rim_inspector` |
| `storage_type` | `/obj/item/tank/internals/oxygen/yellow` |

### 3.3 HC 舰船区域 Areas

源码：`random_ships/heliostatic_inspectors/code/ship.dm`（第 5–73 行）

基类 `/area/shuttle/hc_cops`：`name = "HC Ship"`、`forced_ambience = TRUE`、`requires_power = TRUE`、`area_limited_icon_smoothing = /area/shuttle/hc_cops`；环境音 `amb_ship_01.ogg`（音量 15），环境声列表含 `alarm_radio.ogg`、`alarm_small_09.ogg`、`gear_loop.ogg`、`gear_start.ogg`、`gear_stop.ogg`、`intercom_loop.ogg`。

| 区域 Area | 名称 Name | icon_state |
|---|---|---|
| `/area/shuttle/hc_cops/engineering` | HC Ship Reactor Room（反应堆室） | `engie` |
| `/area/shuttle/hc_cops/ready_room` | HC Ship Ready Room（待命室） | `security_sub` |
| `/area/shuttle/hc_cops/cargo_hold` | HC Ship Cargo Hold（货舱） | `cargo_bay` |
| `/area/shuttle/hc_cops/infirmary` | HC Ship Infirmary（医务室） | `med_central` |
| `/area/shuttle/hc_cops/recreation` | HC Ship Recreation（娱乐室） | `rec` |
| `/area/shuttle/hc_cops/armory` | HC Ship Armory（军械库） | `security` |
| `/area/shuttle/hc_cops/command` | HC Ship Bridge（舰桥） | `command` |
| `/area/shuttle/hc_cops/canteen` | HC Ship Canteen（食堂） | `cafeteria` |
| `/area/shuttle/hc_cops/hydroponics` | HC Ship Hydroponics（水培室） | `hydro` |
| `/area/shuttle/hc_cops/isolation` | HC Ship Isolation Room（隔离室） | `virology_isolation` |
| `/area/shuttle/hc_cops/conference` | HC Ship Conference Hall（会议厅） | `courtroom` |
| `/area/shuttle/hc_cops/lounge` | HC Ship Lounge（休息室） | `lounge` |
| `/area/shuttle/hc_cops/checkpoint` | HC Ship Teleporter Checkpoint（传送器检查站） | `checkpoint_arr` |
| `/area/shuttle/hc_cops/teleporter` | HC Ship Teleporter（传送器） | `teleporter` |

共 **14 个区域**（1 基类 + 13 子区域）。

### 3.4 舰船设备 Ship Equipment

源码：`modular_nova/modules/random_ship_event/ship_equipment.dm`（24 行）

**穿梭机控制台 `/obj/machinery/computer/shuttle/random_ship`**（通用随机船）：

| 字段 | 值 |
|---|---|
| `name` | `"ship shuttle console"`（飞船穿梭机控制台） |
| `shuttleId` | `"random_ship"` |
| `icon_screen` | `"shuttle"` |
| `icon_keyboard` | `"tech_key"` |
| `light_color` | `COLOR_FRENCH_BLUE` |
| `possible_destinations` | `"random_ship_away;random_ship_home;random_ship_custom"`（三个目的地） |

**导航电脑 `/obj/machinery/computer/camera_advanced/shuttle_docker/syndicate/random_ship`**：

| 字段 | 值 |
|---|---|
| `name` | `"ship navigation computer"`（飞船导航电脑） |
| `desc` | `"Used to designate a precise transit location for the ship."` |
| `shuttleId` | `"random_ship"` |
| `lock_override` | `CAMERA_LOCK_STATION` |
| `shuttlePortId` | `"random_ship_custom"` |
| `x_offset` | `9` |
| `y_offset` | `0` |
| `see_hidden` | `FALSE` |

**移动停靠点 `/obj/docking_port/mobile/random_ship`**：

| 字段 | 值 |
|---|---|
| `name` | `"random ship"` |
| `shuttle_id` | `"random_ship"` |
| `rechargeTime` | `3 MINUTES`（充能 3 分钟） |

---

## 4 船员与身份 Crew & Roles

### 4.1 幽灵船员生成器 ship_roles.dm

源码：`modular_nova/modules/random_ship_event/ship_roles.dm`（66 行）

**基类 `/obj/effect/mob_spawn/ghost_role/human/ship_crew`**：

| 字段 | 值 |
|---|---|
| `name` | `"ship crew sleeper"`（船员冷冻睡眠舱） |
| `desc` | `"A cryo sleeper for ship crew."` |
| `icon` | `'icons/obj/machines/sleeper.dmi'`（`sleeper`） |
| `prompt_name` | `"a ship crew member"` |
| `outfit` | `/datum/outfit/ship_crew` |
| `anchored` | `TRUE` |
| `density` | `FALSE` |
| `show_flavor` | `FALSE`（风味文本仅用于生成器菜单） |
| `you_are_text` | `"You are a member of a ship crew."`（你是一名船员） |
| `flavour_text` | `"You are part of a ship crew. Follow your captain's orders and complete your mission."`（听从船长命令完成使命） |
| `spawner_job_path` | `/datum/job/ship_crew` |
| `rank` | `"Crewman"`（军衔，用于生成姓名） |
| `fluff_spawn` | `/obj/structure/showcase/machinery/oldpod/used`（生成后产生的装饰物） |
| `name_beginnings` | `"generic_beginnings"`（姓名开头 JSON 键） |
| `name_endings` | `"generic_endings"`（姓名结尾 JSON 键） |

**special()**：为生成角色改名 `generate_crew_name(spawned_mob.gender)`，并添加反派 datum `/datum/antagonist/ship_crew`。

**generate_crew_name()**：`rank + " " + pick(beggings) + pick(endings)`（如“Crewman Cometfish”），名称来自 `PIRATE_NAMES_FILE` 的 `name_beginnings`/`name_endings`。

**create()**：若 `fluff_spawn` 存在，先在原地生成装饰物再生成角色。

**子类生成器（7 个）**：

| 生成器 | rank | outfit | 特性 |
|---|---|---|---|
| `/ship_crew/captain` | `"Captain"`（船长） | `/datum/outfit/ship_crew/captain` | — |
| `/ship_crew/engineer` | `"Engineer"`（工程师） | `/datum/outfit/ship_crew` | — |
| `/ship_crew/gunner` | `"Gunner"`（炮手） | 继承基类 | — |
| `/ship_crew/rogue_trader` | 无（基类） | `/datum/outfit/ship_crew/rogue_trader` | `name = "rogue trader sleeper"`；`desc = "A cryo sleeper smelling faintly of rum."`（散发朗姆酒气味的冷冻舱）；`prompt_name = "a rogue trader"`；`you_are_text = "You are a rogue trader."`；`flavour_text = "You are a rogue trader looking to make a profit. Trade with the station or take what you need by force."`（不法商人：与空间站交易或武力夺取） |
| `/ship_crew/rogue_trader/captain` | `"Trader Captain"`（商人船长） | `/datum/outfit/ship_crew/rogue_trader` | — |
| `/ship_crew/rogue_trader/crew` | `"Trader"`（商人） | 继承 | — |

### 4.2 装备预设 ship_outfits.dm

源码：`modular_nova/modules/random_ship_event/ship_outfits.dm`（63 行）

**`/datum/outfit/ship_crew`（Ship Crew）**：

| 槽位 | 物品 |
|---|---|
| `id` | `/obj/item/card/id/advanced` |
| `id_trim` | `/datum/id_trim/ship` |
| `uniform` | `/obj/item/clothing/under/color/random` |
| `suit` | `/obj/item/clothing/suit/armor/vest` |
| `ears` | `/obj/item/radio/headset` |
| `glasses` | `/obj/item/clothing/glasses/sunglasses` |
| `head` | `/obj/item/clothing/head/helmet` |
| `shoes` | `/obj/item/clothing/shoes/workboots` |
| `back` | `/obj/item/storage/backpack` |

`post_equip()`：耳机调到公共频率 `FREQ_COMMON` 且 `RADIO_FREQENCY_UNLOCKED`；ID 登记姓名并刷新外观；制服传感器设为 `SENSOR_OFF`（关闭）。

**`/datum/outfit/ship_crew/captain`（Ship Captain）**：

| 槽位 | 物品 |
|---|---|
| `id_trim` | `/datum/id_trim/ship/captain` |
| `head` | `/obj/item/clothing/head/hats/caphat/parade` |
| `suit` | `/obj/item/clothing/suit/armor/vest/capcarapace/syndicate` |
| `glasses` | `/obj/item/clothing/glasses/sunglasses` |
| `l_pocket` | `/obj/item/melee/energy/sword/saber/purple`（紫色能量军刀） |

**`/datum/outfit/ship_crew/rogue_trader`（Rogue Trader）**：

| 槽位 | 物品 |
|---|---|
| `id_trim` | `/datum/id_trim/ship/rogue` |
| `uniform` | `/obj/item/clothing/under/costume/pirate` |
| `suit` | `/obj/item/clothing/suit/costume/pirate/armored` |
| `ears` | `/obj/item/radio/headset/syndicate` |
| `glasses` | `/obj/item/clothing/glasses/eyepatch`（眼罩） |
| `head` | `/obj/item/clothing/head/costume/pirate/bandana/armored` |
| `shoes` | `/obj/item/clothing/shoes/pirate/armored` |
| `back` | `/obj/item/storage/backpack/satchel` |

`post_equip()`：添加派系 `FACTION_PIRATE`；耳机调到辛迪加频率 `FREQ_SYNDICATE` 且 `RADIO_FREQENCY_LOCKED`（锁定）。

**ID 装饰（3 个）**：`/datum/id_trim/ship`、`/datum/id_trim/ship/captain`、`/datum/id_trim/ship/rogue`（均无额外配置）。

### 4.3 职业 ship_job.dm

源码：`modular_nova/modules/random_ship_event/ship_job.dm`（3 行）

```dm
/datum/job/ship_crew
	title = "Random Ship Crew"
	policy_index = "Random Ship Crew"
```

### 4.4 反派数据 ship_antagonist.dm

源码：`modular_nova/modules/random_ship_event/ship_antagonist.dm`（77 行）

**`/datum/antagonist/ship_crew`**：

| 字段 | 值 |
|---|---|
| `name` | `"\improper Ship Crew"` |
| `roundend_category` | `"ship crew"` |
| `antagpanel_category` | `"Random Ship Crew"` |
| `show_in_antagpanel` | `FALSE` |
| `show_to_ghosts` | `TRUE` |
| `suicide_cry` | `"I don't want to die!!"`（我不想死！！） |
| `team` | `/datum/team/ship_crew`（追踪所有玩家的队伍 datum） |

- `greet()`：发送 `LANG("datum.f3e5d435")` 消息并 `announce_objectives()`。
- `get_team()` → `team`。
- `create_team()`：无现成队伍时遍历 `GLOB.antagonists` 寻找已有 `team`（无 owner 的报 `stack_trace`）；都没有则新建 `/datum/team/ship_crew` 并 `forge_objectives()`。
- `on_gain()`：`objectives |= team.objectives`。

**`/datum/team/ship_crew`**：`name = "\improper Ship Crew"`。

**forge_objectives()**：创建主要任务目标 `/datum/objective/mission`（`primary_mission.team = src`），并为所有成员同步目标。

**`/datum/objective/mission`**：

| 字段 | 值 |
|---|---|
| `explanation_text` | `"Complete your ship's mission."`（完成你飞船的使命） |
| `update_explanation_text()` | 保持同一文本 |

**roundend_report()**（第 61–77 行）：

- 输出 `"Ship Crew were:"` 标题 + `printplayerlist(members)` 玩家名单。
- 全员死亡（`all_dead = TRUE`）→ `<span class='redtext big'>The ship crew has failed.</span>`（船组失败了）。
- 否则 → `<span class='greentext big'>The ship crew completed their mission!</span>`（船组完成了他们的使命！）。
- 以 `<div class='panel redborder'>` 面板输出。

---

## 5 HC 检查队 Heliostatic Inspectors

子模块路径：`modular_nova/modules/random_ship_event/random_ships/heliostatic_inspectors/`（9 个 .dm，1,562 行）

背景：日静联盟（Heliostatic Coalition）内务部（Internal Affairs Department，HC-IAD）的远征巡逻队（Expeditionary Patrol），以“自愿安全检查”为名对空间站进行标准合规检查，可没收违禁品、控制警戒状态、封锁货运穿梭机。

### 5.1 事件本体 hc_police.dm

源码：`random_ships/heliostatic_inspectors/code/hc_police.dm`（55 行）

**`/datum/random_ship_event/hc_police`（HC Safety Inspection Team，HC 安全检查队）**：

| 字段 | 值 |
|---|---|
| `name` | `"HC Safety Inspection Team"` |
| `ship_template_id` | `"hc_police"` |
| `ship_name_pool` | `"hc_police_prefix"`（实际 New() 中自行组合前后缀） |
| `message_title` | `"HC Voluntary Inspection Request"`（HC 自愿检查请求） |
| `announcement_color` | `"purple"`（紫色） |

**message_content**（`%SHIPNAME` 船名 / `%STATION` 空间站名 / `%FOCUS` 检查重点 会被替换）：

> “Greetings %STATION, this is the %SHIPNAME patrol vessel. The Heliostatic Coalition is conducting routine safety inspections in this sector, with a focus on %FOCUS. We would like to offer your station a voluntary inspection to ensure compliance with Coalition safety standards. Participation is completely optional, and stations that volunteer receive a complimentary funding package. Please let us know if you would like to schedule an inspection. Heliostatic Coalition departmental secretary out.”
>
> 译文：%STATION 你好，这里是 %SHIPNAME 巡逻艇。日静联盟正在本星区进行例行安全检查，重点关注 %FOCUS。我们愿为贵站提供一次自愿检查，以确保符合联盟安全标准。参与完全自愿，自愿参与的站点将获得一笔赠送资金包。如需安排检查请告知。日静联盟部门秘书结束通话。

**arrival_announcement**（`%NUMBER1/%NUMBER2/%NUMBER3` 会被替换为 `GLOB.phonetic_alphabet` 随机呼号字）：

> “Inspection vessel approaching. Vessel ID tag is %NUMBER1-%NUMBER2-%NUMBER3. Vessel Model: Strider, Flight ETA: three minutes minimal. Vessel is authorized to perform inspection duties. We're clear for close orbit.”
>
> 译文：检查船正在接近。飞船 ID 标签为 %NUMBER1-%NUMBER2-%NUMBER3。飞船型号：Strider（猎行者），预计飞行时间最少三分钟。飞船获准执行检查任务。我们已获准进入近轨道。

**possible_answers**：

| 选项 | 文本 |
|---|---|
| 1（接受） | `"Accept the inspection."`（接受检查） |
| 2（拒绝） | `"Decline the inspection at this time."`（此时拒绝检查） |

**response_accepted**：

> “Thank you for your cooperation. As a token of appreciation for participating in our voluntary inspection program, a bonus of 3 Crowns has been deposited to your station's account. Heliostatic Coalition departmental secretary out.”
>
> 译文：感谢您的合作。作为参与我们自愿检查计划的谢礼，3 克朗（Crowns）奖金已存入贵站账户。日静联盟部门秘书结束通话。

**response_rejected**：

> “Understood. We respect your decision. Should you change your mind, please feel free to contact us at a later time.”
>
> 译文：明白。我们尊重您的决定。若您改变主意，欢迎日后随时联系我们。

**New()**（第 22–29 行）：从 `random_ships_nova.json` 的 `hc_police_prefix`（90 个前缀）和 `hc_police_suffix`（60 个后缀）各取一个组合船名，如 `"Adjudicator of Allegiance"`（裁判官·效忠）。

**generate_message()**（第 31–47 行）：
- `%FOCUS` 从 4 个检查重点随机：`"workplace safety protocols"`（工作场所安全规程）、`"emergency response procedures"`（应急响应程序）、`"hazardous material storage"`（危险材料储存）、`"life support system maintenance"`（生命维持系统维护）。
- `%STATION` → `station_name()`；`%NUMBER1-3` → `pick(GLOB.phonetic_alphabet)`（3 个随机呼号字）。

**on_accept()**（第 49–55 行）：**向空间站货运账户 `ACCOUNT_CAR` 发放 12,288 信用点奖金**（`bonused_account.adjust_money(12288)`）——即公告所称“3 克朗”的对应信用点。

### 5.2 军官生成器 ghost_spawner.dm

源码：`random_ships/heliostatic_inspectors/code/ghost_spawner.dm`（68 行）

**`/obj/effect/mob_spawn/ghost_role/human/hc_officer`（HC Patrol Officer）**：

| 字段 | 值 |
|---|---|
| `name` | `"HC Patrol Officer"` |
| `desc` | 带有日静联盟内务部徽记的舒适睡眠单元 |
| `prompt_name` | `"an HC Expeditionary Patrol Officer"` |
| `icon` | `modular_nova/modules/cryosleep/icons/cryogenics.dmi`（`cryopod`） |
| `mob_species` | `/datum/species/human` |
| `faction` | `list(FACTION_NEUTRAL)` |
| `you_are_text` | `"You are an officer of the Heliostatic Coalition Expeditionary Patrol."`（你是日静联盟远征巡逻队军官） |
| `flavour_text` | 你的巡逻艇正在此偏远设施执行标准合规与检查行动。你的授权源自联盟协定，可检查、没收违禁品，并为保护联盟利益使用必要武力。警惕至上：这些空间站自治且并非天然可信。始终遵守标准作业程序。 |
| `important_text` | 服从指挥链。你的巡逻队长呼号后缀为“Actual”。保持专业纪律，随时准备按情况适当升级。 |
| `outfit` | `/datum/outfit/hc_officer` |
| `allow_custom_character` | `GHOSTROLE_TAKE_PREFS_APPEARANCE` |
| `quirks_enabled` | `TRUE` |
| `show_flavor` | `TRUE` |
| `first_officer` | `static` 变量，记录第一位军官的 REF（决定谁当 Actual） |

**apply_codename()**（第 19–25 行）：呼号 = `pick(GLOB.callsigns_nri)` + `pick(GLOB.phonetic_alphabet_numbers)`（如“Dagger Three”）；若为第一位军官（`first_officer == REF(spawned_mob)`）则在名字后追加 `" Actual"`。

**post_transfer_prefs()**（第 27–50 行）：
- 添加反派 datum `/datum/antagonist/cop`。
- 授予语言：`spinwarder`（顺风语）、`uncommon`（通用语）、`yangyu`（洋语）、`akulan`（阿库兰语），来源 `LANGUAGE_SPAWNER`。
- 第一位军官：记录 `first_officer`，发送 `LANG("obj.cb61ec08")` 加粗消息。
- 发送任务简报（大段 to_chat，含 OOC 提示：目标是叙事引导，非机械“绿字”目标，鼓励与管理员和其他玩家沟通创意）。

**equip()**（第 52–62 行）：根据是否为第一位军官从 `HC_LEADER_JOB_LIST`（领导职务）或 `HC_JOB_LIST`（普通职务）随机分配 ID 上的 `assignment`；领导 `sechud_icon_state = "hud_hc_police_lead"`，普通为 `"hud_hc_police"`。

**Destroy()**（第 64–68 行）：在原地生成 `/obj/structure/showcase/machinery/oldpod/used` 装饰物。

### 5.3 军官装备 outfit.dm

源码：`random_ships/heliostatic_inspectors/code/outfit.dm`（110 行）

**`/datum/outfit/hc_officer`（HC Inspector）**：

| 槽位 | 物品 |
|---|---|
| `head` | `/obj/item/clothing/head/hats/colonial/hc_police` |
| `glasses` | `/obj/item/clothing/glasses/sunglasses` |
| `ears` | `/obj/item/radio/headset/guild/command` |
| `mask` | `null` |
| `neck` | `/obj/item/clothing/neck/cloak/colonial/hc_police` |
| `uniform` | `/obj/item/clothing/under/colonial/hc_police` |
| `suit` | `null` |
| `gloves` | `/obj/item/clothing/gloves/combat` |
| `shoes` | `/obj/item/clothing/shoes/combat` |
| `belt` | `/obj/item/storage/belt/security/nri` |
| `back` | `/obj/item/storage/backpack/satchel/leather` |
| `l_pocket` | `/obj/item/folder/blue/hc_cop`（HC 警察 SOP 文件夹） |
| `r_pocket` | `/obj/item/storage/pouch/ammo` |
| `id` | `/obj/item/card/id/advanced/hc_police` |
| `id_trim` | `/datum/id_trim/hc_police` |
| `implants` | `list(/obj/item/implant/mindshield)`（心灵护盾植入体） |

**backpack_contents（背包内容，5 件）**：

| 数量 | 物品 |
|---|---|
| 1 | `/obj/item/storage/box/nri_survival_pack/inspector`（HC 检查员生存包） |
| 2 | `/obj/item/ammo_box/magazine/recharge/plasma_battery`（等离子电池弹匣） |
| 1 | `/obj/item/gun/ballistic/automatic/pistol/plasma_marksman`（等离子射手手枪） |
| 1 | `/obj/item/clothing/mask/gas/hc_police` |
| 1 | `/obj/item/modular_computer/pda/hc_police` |
| 1 | `/obj/item/coin/mark/crown`（克朗硬币） |

**post_equip()**：添加派系 `"coalition"`；ID 登记姓名并刷新外观；`handlebank(equipped)`。

**`/obj/item/modular_computer/pda/hc_police`**：

| 字段 | 值 |
|---|---|
| `name` | `"\improper HC police PDA"` |
| `device_theme` | `PDA_THEME_TERMINAL` |
| `greyscale_colors` | `"#363655#7878f7"` |
| `comp_light_luminosity` | `6.3`（相当于手电筒） |
| `comp_light_color` | `"#5c20aa"`（“UV”紫光） |
| `starting_programs` | `/datum/computer_file/program/records/security`（安全档案）、`/datum/computer_file/program/crew_manifest`（船员名册）、`/datum/computer_file/program/robocontrol`（机器人控制） |
| `inserted_item` | `/obj/item/pen/fourcolor` |

**`/obj/item/card/id/advanced/hc_police`**：复古外观卡片，适配现代识别系统；`icon = 'modular_nova/master_files/icons/obj/card.dmi'`，`card_hc_police` / `assigned_hc_police` 图标。

**`/datum/id_trim/hc_police`**：

| 字段 | 值 |
|---|---|
| `assignment` | `"HC Field Officer"`（HC 外勤军官） |
| `trim_icon` / `trim_state` | `card.dmi` / `trim_hc_police` |
| `department_color` | `COLOR_HC_POLICE_BLUE`（`#1f3347`） |
| `subdepartment_color` | `COLOR_HC_POLICE_SILVER`（`#c0c0c0`） |
| `sechud_icon_state` | `"hud_hc_police"` |
| `access` | `list(ACCESS_SYNDICATE, ACCESS_MAINT_TUNNELS)`（辛迪加权限 + 维护隧道权限） |
| `threat_modifier` | `2`（威胁值 2，不如辛迪加高但可能危害空间站） |

**`/obj/item/storage/belt/security/nri` PopulateContents()**（3 件）：`/obj/item/knife/combat`（战斗刀）×1、`/obj/item/grenade/smokebomb`（烟雾弹）×1、`/obj/item/grenade/flashbang`（闪光弹）×1。

**`/obj/item/storage/box/nri_survival_pack/inspector`**（检查员生存包，7 件）：

| 物品 |
|---|
| `/obj/item/oxygen_candle`（氧气蜡烛） |
| `/obj/item/tank/internals/emergency_oxygen`（应急氧气瓶） |
| `/obj/item/clothing/mask/breath`（呼吸面罩） |
| `/obj/item/reagent_containers/hypospray/medipen/blood_loss`（失血医疗笔） |
| `/obj/item/reagent_containers/hypospray/medipen`（医疗笔） |
| `/obj/item/flashlight/flare`（信号棒） |
| `/obj/item/crowbar/red`（红色撬棍） |

**`/obj/item/folder/blue/hc_cop`（HC police SOPs）** Initialize() 装入 **8 份文件**：`hc_document`（SOP 手册）、`cheat_sheet`（战场指南）、`lexicon`（词典）、`property_seizure_receipt`、`incident_report`、`shore_leave_request`、`acquired_asset_register`、`biological_examination_report`、`pre_action_assessment`（实际共 9 项 new，前 3 项 + 6 份表单，见下方 5.6/5.7 节）。

### 5.4 反派数据 antag_datum.dm

源码：`random_ships/heliostatic_inspectors/code/antag_datum.dm`（142 行）

**`/datum/antagonist/cop`（HC Police Officer）**：

| 字段 | 值 |
|---|---|
| `name` | `"\improper HC Police Officer"` |
| `roundend_category` | `"hc cops"` |
| `antagpanel_category` | `"HC Police"` |
| `show_in_antagpanel` | `FALSE` |
| `show_to_ghosts` | `TRUE` |
| `crew` | `/datum/team/cop`（管理员追踪用队伍 datum） |

- `greet()`：`owner.announce_objectives()`。
- `create_team()`：遍历 `GLOB.antagonists` 找已有队伍，否则新建 `/datum/team/cop` 并 `forge_objectives()`。
- `apply_innate_effects()`：授予 3 种语言（`uncommon` 通用语、`spinwarder` 顺风语、`yangyu` 洋语，来源 `LANGUAGE_PIRATE`）；`remove_innate_effects()` 移除。

**`/datum/team/cop`（HC police patrol）**。

**forge_objectives() 创建 6 个目标**（第 62–76 行）：

| 顺序 | 目标 | name | explanation_text |
|---|---|---|---|
| 1 | `/datum/objective/policing` | `"safety inspection"`（安全检查） | “Conduct a voluntary safety inspection of the station. Delegate responsibilities among the inspection team. Maintain professional and courteous demeanor at all times.”（对空间站进行自愿安全检查。在检查队中分配职责。始终保持专业礼貌的风度。）`martyr_compatible = TRUE` |
| 2 | `/datum/objective/inspect_area` | `"inspect area"`（检查区域） | New() 从 `INSPECTION_LIST` 随机选部门；文本“Inspect [部门] department for safety compliance. Provide constructive feedback and recommendations for improvement.”（检查[部门]的安全合规情况，提供建设性反馈与改进建议）；`martyr_compatible = TRUE` |
| 3 | `/datum/objective/survey` | `"safety survey"`（安全调查） | New() 从 `INSPECTION_LIST` 随机选部门；文本“Conduct a safety survey over [部门] department. Gather feedback from staff and identify potential safety concerns.”（对[部门]进行安全调查，收集员工反馈并识别潜在安全隐患）；`martyr_compatible = TRUE`、`admin_grantable = TRUE` |
| 4 | `/datum/objective/steal_n_of_type/contraband` | `"secure hazardous materials"`（封存危险材料） | New()：`amount = rand(CONFISCATE_LOWER, CONFISCATE_HIGHER)` = **5–15 随机件数**；文本“Secure at least [amount] potentially hazardous items for safekeeping or disposal.”（封存至少 [amount] 件潜在危险物品）；`check_completion()` 直接返回 `completed`（允许角色扮演完成） |
| 5 | `/datum/objective/dock` | `"remain docked"`（保持停靠） | New()：文本“Dock to the [station_name()] to conduct the voluntary safety inspection. Remain in the sector until the inspection is complete.”（停靠至[空间站名]进行自愿安全检查，在检查完成前留在本星区）；`martyr_compatible = TRUE` |
| 6 | `/datum/objective/survive` | （标准生存目标） | — |

### 5.5 检查长主机 inspector_mainframe.dm

源码：`random_ships/heliostatic_inspectors/code/inspector_mainframe.dm`（237 行）

**`/obj/machinery/inspector_mainframe`（Inspector Mainframe，检查长主机）**：

| 字段 | 值 |
|---|---|
| `name` | `"Inspector Mainframe"` |
| `desc` | 一台精密的机器，能锁定货运穿梭机并控制飞船警戒状态，通过安全界面访问其功能 |
| `icon` | `'icons/obj/machines/dominator.dmi'`（`dominator`） |
| `density` | `TRUE` |
| `interaction_flags_machine` | `INTERACT_MACHINE_REQUIRES_LITERACY`（需要识字） |
| `cargo_disruption_active` | `FALSE`（货物干扰是否激活） |
| `tracked` | `FALSE`（是否被 GPS 信号追踪） |
| `current_alert_level` | `"Status Marble"`（当前警戒状态） |
| `radio` | 公会频道无线电（发送行会消息） |
| `sos_active` | `FALSE`（SOS 信标是否激活） |
| `sos_timer_id` | SOS 信号定时器 ID |

**径向菜单选项 radial_options（3 项）**：

| 选项 | 图标 |
|---|---|
| `"Cargo Disruption"`（货物干扰） | `dominator-Blue` |
| `"Ship Alert Status"`（飞船警戒状态） | `status_display.dmi` `frame` |
| `"SOS Beacon"`（SOS 信标） | `status_display.dmi` `lockdown` |

**警戒状态选项 alert_options（6 级）**：

| 选项 | 图标 |
|---|---|
| `"Status Marble"` | `outline` |
| `"Status Silver"` | `greenalert` |
| `"Status Cobalt"` | `bluealert` |
| `"Status Pearl"` | `redalert` |
| `"Status Onyx"` | `deltaalert` |
| `"Status Obsidian"` | `lockdown` |

**Initialize()**：新建无线电，插入加密钥匙 `/obj/item/encryptionkey/headset_syndicate/guild`，`set_listening(FALSE)`，`recalculateChannels()`。

**attack_ghost()**：幽灵可 `examine`。

**open_options_menu()**：`NOPOWER|BROKEN` 时不可用；`sos_active` 时菜单只剩 `"SOS Beacon"`；选择后分发到三个 proc。

**toggle_on() / 货物干扰激活**（第 86–96 行）：
- `SSshuttle.registerTradeBlockade(src)` —— **注册贸易封锁（阻止货运穿梭机）**。
- `cargo_disruption_active = TRUE`；首次激活且未追踪时添加 GPS 组件 `"HC Starship"` 并警告玩家（**激活后飞船会出现在 GPS 上**）。
- 未开 SOS 则 `START_PROCESSING(SSobj, src)`。
- `send_notification()`：`priority_announce("Signal interference detected; source registered on local GPS units. Cargo shuttle systems have been locked down.")` —— 公告检测到信号干扰，来源已登记于本地 GPS，货运穿梭机系统已被锁定。

**toggle_off()**：`SSshuttle.clearTradeBlockade(src)`，`cargo_disruption_active = FALSE`，停止处理，更新外观。

**broadcast_sos_signal()**（第 108–128 行）：
- 播放警报音 `alarm_small_09.ogg`（音量 75）。
- `Status Obsidian` 时电台发送：`"ENCRYPTED BURST: OBSIDIAN. Self-destruct and denial protocols initiated. All assets to be denied to enemy."`（加密爆发传输：OBSIDIAN。自毁与否认协议已启动。所有资产将拒绝交予敌人）。
- 其他状态发送：`"DISTRESS SIGNAL REPEATING: Requesting immediate assistance. Patrol vessel under sustained combat operations. All available units respond."`（重复求救信号：请求立即支援。巡逻艇处于持续战斗状态。所有可用单位回应）。
- 每 **30 秒** 重复一次（`addtimer(..., 30 SECONDS, TIMER_STOPPABLE)`，先取消旧定时器防堆叠）。

**select_alert_level()**：`NOPOWER|BROKEN` 不可用；径向菜单选择后调 `set_alert_level()`。

**六种警戒状态完整描述**（`set_alert_level` 经 `RADIO_CHANNEL_GUILD` 向全员播报）：

| 状态 | 中文名 | 要点 |
|---|---|---|
| `Status Marble`（大理石） | 待命/行政 | 安全非敌对环境（联盟星港停靠、全安保联盟领空过境、友好港口维护补给）；威胁评估可忽略；所有个人武器锁入军械库；舰载防御系统可降为待机省电 |
| `Status Silver`（白银） | 例行巡逻/标准检查 | 正在接近或开始接触非联盟设施（标准检查规程）；设施推定合规但不可信；武器入套但**可立即拔出**，保险由军官自行判断；舰载防御激活扫描 |
| `Status Cobalt`（钴蓝） | 威胁升级/违反规程 | 出现违反标准程序的行为模式（如过度护卫、未经许可接近飞船、收到未筛查包裹）或异常活动；全员**拔枪低姿戒备**；推进器随时点着；护盾升至战术强度；可中止检查召回队伍 |
| `Status Pearl`（珍珠） | 敌对意图确认 | 有直接明确证据（向巡逻人员开火、企图登船、被蓝空炮兵加农炮瞄准、公开威胁）；**武器自由**；所有防御满功率；抢占战术优势；全员战备；授权先发自卫 |
| `Status Onyx`（黑曜石） | 持续战斗行动 | 全面战斗，逃跑可能不现实，必须杀出血路；总动员；损管队激活；目标是消灭机会目标并创造撤离窗口 |
| `Status Obsidian`（曜黑） | 断箭/资产否认 | 巡逻艇不可挽回地被突破，被俘或毁灭迫近；主任务失败；新目标：确保无任何联盟人员、技术、情报或飞船本身被敌人利用；清除所有机密数据；无投降，战斗至舰毁人亡 |

**set_alert_level(level_name, level_description, user)**（第 174–184 行）：
- 同等级重复设置 → `balloon_alert` 提示 `LANG("obj.0d44aabb")` 并返回。
- 更新 `current_alert_level`，玩家消息提示，`radio.talk_into` 播报 `"ALERT LEVEL CHANGED: [等级名] - [完整描述]"`，播放 `terminal_prompt.ogg`（音量 50），更新外观。

**toggle_sos_beacon()**（第 187–219 行）：
- **仅允许在 `Status Onyx` 或 `Status Obsidian` 下激活**，否则 `balloon_alert` + 警告消息。
- 激活：`sos_active = TRUE`；未开货物干扰则开始处理；播放警报音；Obsidian 发加密爆发消息（OBSIDIAN），否则发 `"EMERGENCY DISTRESS SIGNAL ACTIVATED. Requesting immediate assistance. Patrol vessel under sustained combat operations. All available units respond."`；30 秒后开始重复广播。
- 关闭：停止处理；取消 SOS 定时器；播放提示音；电台发送 `"Distress signal deactivated."`（求救信号已停用）。

**update_icon_state()**：`cargo_disruption_active` 时图标为 `dominator-Blue`，否则 `dominator`。

**Destroy()**：`toggle_off()`；取消 SOS 定时器；`QDEL_NULL(radio)`。

**examine()**：近处或观察者可见状态行：货物干扰 `ACTIVE/INACTIVE`、当前警戒状态、SOS `ACTIVE/INACTIVE`。

### 5.6 SOP 文档 documents.dm

源码：`random_ships/heliostatic_inspectors/code/documents.dm`（405 行）

**`/obj/item/paper/fluff/hc_document`（HC Police SOPs）** —— 《日静联盟内务部边陲世界巡逻标准作业程序》（Rim-World Patrol Standard Operation Procedures），含 **12 章**：

| 章 | 标题 | 要点 |
|---|---|---|
| I | Annotation and Introduction（注解与导言） | 远征巡逻警察的法律基础、与中央警察/政治警察的区别、自我防卫的应用；独立支队（dispatches）的自治权 |
| II | Command Protocol and Designation（指挥协议与代号） | 巡逻队长拥有最终决定权，呼号加后缀 **"Actual"**（如 “Dagger Three” → “Dagger Three Actual”）；所有正式请求与最后通牒必须由 Actual 发出才具约束力 |
| III | General Patrol Alert Status Codes（通用巡逻警戒状态代码） | 六档状态（见 5.5 节）：MARBLE 待命 / SILVER 例行检查 / COBALT 升级威胁 / PEARL 敌对意图确认 / ONYX 持续战斗 / OBSIDIAN 断箭资产否认；每档含态势概览、内部纪律、武器状态、对外通信语气/规程、交战规则 ROE |
| IV | Standard Station Approach and Inspection Procedures（标准进站与检查程序） | “Big 5” 安全条件：① 护卫限制——每 2 名部门主管最多 1 名保镖，全员出席最多 3 人（含蓝盾/红盾、安保、雇佣兵、武装船员、武装动物）；② 护卫须站在正前方、留出逃生路线；③ 空间站安保不得未经许可接近/检查飞船，留一名舰船监督员（Ship Overseer）；④ 空间站不得利用船载摄像头控制台筹备突袭；⑤ 不得接收未声明内容的包裹（防炸弹/传送/恐怖袭击），违规包裹立即销毁并上报 |
| V | Standard Station Inspection and Contraband Seizure Procedures（标准检查与违禁品没收程序） | 按部门检查（由空间站指挥人员陪同）；**七大部门检查重点与安全调查**：Cargo/Supply（不明货物、伪造舱单、无证材料、未登记通信设备）、Science/Research（未注册 AI、无证实验体、异种动物、军事应用研究如自燃/模因剂/奇点生成）、Engineering（武器化系统改造、未登记核材料、爆炸物制造设备、超结构武器蓝图）、Medical（无证基因改造设备、无解病原体、刑讯工具、超量管制物质）、Service/Catering（隐藏隔间、秘密通信枢纽、伪装监控设备、群体麻醉物质）、Security（需指挥官在场；军用穿甲武器、无差别区域拒止系统、违反感智生命条约的审讯装置）、Command（数据截获设备、向非隶属势力的加密信道、私掠活动证据）；**没收协议**：立即登记没收，向站长开具正式没收收据（Form 14-C），违禁品装载回巡逻艇作为证据 |
| VI | Post-Inspection Presence and Cooperative Engagement Protocol（检查后驻留与协作参与协议） | 检查满意完成后可继续驻留（停靠或近轨道），维持低功率待命；可批准轮换小组上岸休假；可提供被动协助：补充巡逻、医疗/工程支援、共享非机密情报、搜救、参与本地经济（民事合同/赏金）；**禁止**：接管指挥、独立执法、干预内政、泄露机密、无直接威胁而承诺持续战斗；驻留结束条件：接到新命令 / 设施正式要求离开 / 指挥官认定不再符合联盟利益 / 设施退役销毁 |
| VII | Shore Leave Conduct and Recall Procedures（上岸休假行为与召回程序） | 休假需向舰船监督员申请（时长、目的地）；值班军官简报；离岗登记；行为标准：遵守当地法律、专业举止、信息保密、避免冲突；**立即召回情形**：警戒升至 Cobalt 或以上、收到威胁情报、站方因内部紧急事件要求撤员、有违规报告；召回广播模板 + 点名核实（未回应按失踪处理） |
| VIII | Local Law Enforcement Protocol（本地执法协议） | 核心原则：**观察报告优先、非干涉**；仅在 4 种条件下可介入（① 直接威胁巡逻人员/联盟人员生命；② 直接威胁巡逻艇/联盟财产；③ 站安保明显缺席/失效；④ 站指挥正式请求且警戒低于 Pearl）；按罪行等级分级响应：Grade 1（如 101 殴打 NBH）观察报告 / Grade 2（如 201 斗殴）口头降级 / Grade 3（如 302 纵火）报告+遏制 / Grade 4（如 401 殴打 ABH）报告+准备介入（可拔枪示令，禁止致命武力）/ Grade 5-6（501 GBH、601 谋杀）立即介入、可致命 / Grade 7（701 歼灭）极端威胁、隔离消灭、警戒至少升 Pearl；介入后移交嫌犯与证据给站安保，巡逻队不提出控告 |
| IX | Jurisdictional Protocol and Non-Interference Directive（管辖权协议与不干涉指令） | 尊重空间站内部主权；不干涉其安保的内务逮捕、调查、纪律处分；唯一例外是站指挥（站长/安保主管）正式请求协助且警戒未超 Pearl，此时依据站方法律框架行事，巡逻权限是请求方权限的延伸而非替代 |
| X | Threat Identification: Bluespace Artillery Cannon（威胁识别：蓝空炮兵加农炮 BSA） | Nanotrasen 的高精度高速度粒子加速器，频率锁定于船体内置 GPS；能轻松摧毁小型战斗机/巡逻护卫舰，可将标准护卫舰劈成两半；**A. 闪避程序**：导航台锁定稳定空间位置 → BSA 充能瞬间启动推进器 → 其设计为位置锁定、对静止/极慢目标有效，机动即可避开；**B. 敌对意图识别**：确认朝我方开火、巡逻期间出现未授权 BSA 火力、威胁使用 BSA、星区级 BSA 开火公告 → 视为直接侵略通知；**C. 中和程序**：主炮台为脆弱机械，**1 块标准 C4 复合炸药**定向爆炸即可永久瘫痪——靠近主炮台 → 放置 C4（建议接引爆器或短定时）→ 撤离爆炸半径（附带破坏其他部件、拖延重建）；之后空间站视为敌对地面，升 Pearl 后执行先发自卫 |
| XI | Authorized Response to Hostile Action（对敌对行动的授权响应） | 可请求指挥官授权先发自卫；可禁用并逮捕站安保；升级决定权仅在 Actual，按第三章状态码评估；敌对确认（Pearl 及以上）后适用对应 ROE |
| XII | Conclusion and Jurisdictional Limitations（结论与管辖权限制） | 独立支队的建立使警察专注于打击普通犯罪；支队与行星警察享有同等刑事调查权；但活动范围仍限于所辖空间站，不能在其外进行侦查行动；违反 SOP 将受纪律处分、调任或联盟军法起诉 |

**`/obj/item/paper/fluff/hc_document/cheat_sheet`（HC Police Field Guide，战场速查指南）** 7 节：

1. **Alert Status Cheat Sheet**：六档状态速查（MARBLE 待命武器锁定 / SILVER 例行检查 / COBALT 低姿戒备·需违规模式而非单次失误 / PEARL 武器自由 / ONYX 为生存而战·信标激活 / OBSIDIAN 断箭·自毁一切·不投降·信标激活）。
2. **First Contact & Approach（“别被伏击”清单）**：进港前经安全信道发送预检包；确保“Big 5”：护卫上限（每 1 名主管最多 2 名武装护卫）、站位（对方在前、我方有逃生路线）、飞船禁区（站安保不得未经许可靠近，留舰船监督员）、无突袭筹备（摄像头发现其安保集结就在 PA 系统点名）、无惊喜包裹（未声明内容一律拒收并上报）。
3. **Rules of Engagement 速查**：永远不要第一个拔枪/第一个瞄准/第一个开火（除非敌对武器已瞄准你且警戒在 Cobalt 以上）；对方一旦开火，所有 ROE 限制解除。
4. **BSA 三命令**：**DODGE**（充能即全速推进，打不中移动目标）/ **CONFIRM**（朝我们开火 = 确认宣战，立即升 PEARL）/ **NEUTRALIZE**（主炮台 1 块 C4 摧毁）。
5. **Shore Leave & Conduct**：你是外交官；可社交、可消费提振本地经济、可接民事赏金（经值班军官批准）；召回立即生效、无例外。
6. **Law Enforcement on Station（看见就说）**：默认观察报告；仅当生命直接危险 / 站安保缺席 / 站长正式请求时可介入；示例分级：斗殴→喊安保别拔枪；纵火→按警报拿灭火器、遏制不追；持刀伤人→可拔枪以相称武力介入；炸弹恐怖分子→任何手段中和，属 Pearl 情况。
7. **The "Actual's" Discretion**：巡逻队长（“呼号 Actual”）拥有一切最终决定权；可放宽规则以利任务；可授权购买本地物资；其首要职责是把大家安全带回家。

**`/obj/item/paper/fluff/hc_document/lexicon`（HC Police Field Lexicon，战场词典与通信协议）** —— 禁止“Ten-Codes”数字代码，强制 **Clear-Tongue Protocol（明语协议）**，四类术语：

- **A. 巡逻状态与态势术语（9 个）**：`Actual`（巡逻队长，呼号后缀）、`Marble/Silver/Cobalt/Pearl/Onyx/Obsidian`（六档警戒，禁说“Status Two”要说“Cobalt”）、`Static`（安全非敌对但可能不合作的默认状态）、`Churn`（非直接威胁但需注意的异常活动）、`Aperture`（任何脆弱点：开启的防爆门、无人监控的气闸、被入侵的摄像头）、`Axiom`（局势核心事实，常由 Actual 确立）、`Bluesky`（空域无 BSA 威胁或加农炮已毁）/ `Bluestatic`（BSA 存在但未激活）/ `Going Blue`（BSA 正在充能）、`Echo`（已被中和的问题）、`Reverb`（问题的持续次级影响）。
- **B. 人员与装备术语（7 个）**：`Overseer`（留守舰船监督员）、`Shield`（任何个人保护人员，如 Blueshield）、`Local Script`（空间站法律与公司规章）、`Stamped/Not Stamped`（Actual 批准/否决）、`Coalition Script`（我方交战规则与程序）、`Kit`（制式装备）、`Acquired Asset`（本地购置的非标准装备，须受检）。
- **C. 行动与指挥术语（10 个）**：`Hold Fast`（原地驻守）、`Make Static`（肃清区域/控制局面）、`Verify`（核实信息）、`Clamp`（锁死、逮捕）、`Scan`（彻底搜查）、`Acknowledge / Confirm`（唯二允许的回答，禁说“copy”“roger”）、`Final Query`（升级前的最后正式质询）、`Apply Pressure`（非致命/外交施压）、`Sanction`（授权致命武力）。
- **D. 俚语与非正式用语（8 个）**：`The Department's Long Arm`（巡逻队本身，联盟法律的触手）、`Rim-World Welcome`（敌对接待：抵达即被射击）、`Shorting the Airlock`（鲁莽非理性行为）、`Bag a Bounty`（完成民事合同赚信用点）、`A Good Patch`（和平合作的站点）、`Hard Place / Rock`（难缠、不合作或敌对的站点）、`Echo-Chamber`（舰上食堂/公共区，谣言放大器）、`Cognitive Saturation`（信息/压力过载）。

### 5.7 表单模板 templates.dm

源码：`random_ships/heliostatic_inspectors/code/templates.dm`（391 行）

**HC-IAD 七种标准表单**（均为 `/obj/item/paper/fluff/hc_document` 子类）：

| 表单 | 名称 | 表单编号 | 主要字段 |
|---|---|---|---|
| `property_seizure_receipt` | HC-IAD Property Seizure Receipt（财产没收收据） | **HC FORM 14-C**（Rev. 09/2565） | 巡逻艇/呼号、签发军官、接收设施/代表、没收日期时间地点、没收通知、没收物清单（类别+数量，逐项明细机密）、没收原因勾选（未授权武器/受限黑市技术/危险生物化学剂/敌对实体关联资产/犯罪证据/其他）、签字确认、白联留存巡逻艇/灰联交设施代表 |
| `incident_report` | HC-IAD Incident Report（事件报告） | **HC FORM 22-A**（Rev. 09/2565） | 巡逻艇、报告军官、事件地点/时间、事件类型勾选（规程偏差 Sect.IV/违禁品发现 Sect.V/本地执法互动 Sect.VIII/敌对行动 Sect.XI/设备故障/人员受伤/其他）、事件时警戒状态（Marble/Silver/Cobalt/Pearl/Onyx/Obsidian）、事件叙述、涉事方（HC-IAD 人员呼号 + 外部人员全名职务）、采取行动勾选（口头警告/正式传票附 14-C/没收违禁品附 14-C/相称武力/致命武力/警戒升级/机动规避/通知站安保/爆发传输通知联盟指挥部/无需行动观察报告/其他）、结果勾选（和平解决/站方控制/巡逻脱离/敌对消灭/事件持续/其他）、审批（报告军官/巡逻 Actual 复核） |
| `shore_leave_request` | HC-IAD Shore Leave Request（上岸休假申请） | **HC FORM 9-D**（Rev. 09/2565） | 巡逻艇、申请军官、当前警戒状态（Marble/Silver）、设施状态（Cooperative/Static）、申请时长（标准周期内最多 5 小时，超时需 Actual 批准）、目的地勾选（民用大厅/维护隧道、招待区、娱乐设施、宗教冥想室、零售商业区、私人住宅区、其他）、目的勾选（娱乐士气/民用差事/民事合同赏金附 12-F/非正式联络外交/人际交往/其他）、规章确认、**人际交往风险声明**（3 项：自负风险、遵守站法、OPSEC 保密、防诱捕/胁迫风险）、值班军官/舰船监督员复核（简报/威胁记录/通信检查/装备检查）、巡逻 Actual 授权（可带副武器/缩短时长/限制目的地/强制消毒体检/其他）、返回与汇报（出发/返回时间、延误事件报告附 22-A、医疗筛查、与何人汇报） |
| `acquired_asset_register` | HC-IAD Acquired Asset Register（购置资产登记） | **HC FORM 8-G**（Rev. 09/2565） | 巡逻艇、购置军官、购置方式（购买/贸易/赏金支付/打捞/其他）、来源、资产名称、描述、预期巡逻用途勾选（战斗效能升级/医疗工程用途/情报收集/贿赂外交筹码/士气娱乐/其他）、检查与风险评估（安全检查、检查发现：干净/需改装/危险建议安全销毁/其他；风险：低/中/高）、Actual 授权（批准使用/批准安全储存/拒绝销毁）、使用条件、最终处置（已发给军官/在军械库/安全储存/已处置；处置方式：抛掷/引爆/出售/其他） |
| `biological_examination_report` | HC-IAD Biological Examination Report（生物检验报告） | **HC FORM 13-M**（Rev. 09/2565） | 检查军医、标本名称/物种（人类/IPC/异星动物/未知）/年龄/来源/发现时状态（新鲜/腐烂/残缺/烧焦/冰冻）、外部检查（体征、衣物装备、创伤勾选：钝器/贯穿/撕裂/切口/烧伤/枪伤/爆炸/窒息/毒素/基因操纵/未知异常）、内部检查与死因、生物异常（寄生虫/未知病原/义体改造）、死因（确定/不确定）、标本威胁评估（低/生物危害建议焚化/放射性建议铅衬隔离/易爆/认知危害）、情报法证（指纹/DNA/外来物/潜在情报价值）、处置（移交站方/巡逻冷库归档/安全焚化/抛入太空/留作研究）、组织血液样本归档、上报联盟指挥部 |
| `pre_action_assessment` | HC-IAD Pre-Action Assessment（行动前评估） | **HC FORM 1-A**（Rev. 09/2565） | I. 态势分析；II. 拟议行动勾选（口头告诫/施压/拘留逮捕/财产没收附 14-C 草稿/制裁致命武力/武器自由/提升警戒状态/其他）、III. 行动参数与理由（即时目标、交战规则/限制、法律依据：HC 协定条款/SOP 章节/站规、预期结果与风险）、IV. 巡逻 Actual 制裁（批准/附修改批准/拒绝、计划修改、替代指令、授权码：呼号-时间-随机数、Actual 签字）——**TOP SECRET 仅限目视** |

### 5.8 平板包装 flatpack.dm

源码：`random_ships/heliostatic_inspectors/code/flatpack.dm`（3 行）

```dm
/obj/item/flatpack/quantum_pad
	name = "quantum pad"
	board = /obj/item/circuitboard/machine/quantumpad
```

量子传送垫（quantum pad）平板包装，板卡为 `/obj/item/circuitboard/machine/quantumpad`。

---

## 6 常量与名称库 Constants & Name Pools

**HC 相关常量**（源码：`code/__DEFINES/~nova_defines/antagonists.dm` 第 48–61 行）：

| 常量 | 值 |
|---|---|
| `INSPECTION_LIST` | `list("Cargo","Command","Engineering","Medical","Science","Service","Security")`（7 个可检查部门） |
| `HC_JOB_LIST` | `list("HC Safety Inspector","HC Compliance Officer","HC Station Surveyor","HC Junior Inspector","HC Safety Consultant","HC Field Assessor","HC Standards Examiner")`（7 个装饰性普通职务） |
| `HC_LEADER_JOB_LIST` | `list("HC Lead Inspector","HC Senior Compliance Officer","HC Chief Surveyor","HC Inspection Supervisor","HC Safety Director")`（5 个装饰性领导职务） |
| `CONFISCATE_LOWER` | `5`（没收物品数下限） |
| `CONFISCATE_HIGHER` | `15`（没收物品数上限） |

**HC 颜色常量**（源码：`code/__DEFINES/~nova_defines/colors.dm` 第 4–5 行）：

| 常量 | 值 |
|---|---|
| `COLOR_HC_POLICE_BLUE` | `"#1f3347"` |
| `COLOR_HC_POLICE_SILVER` | `"#c0c0c0"` |

**飞船名称库**（源码：`strings/random_ships_nova.json`）：

| 键 | 数量 | 示例 |
|---|---|---|
| `hc_police_prefix` | **90 个**前缀 | Adjudicator, Advocate, Aegis, Agent, Arbiter, Banner… |
| `hc_police_suffix` | **60 个**后缀 | of Allegiance, of Audacity, of Authority, of Battle, of Benevolence, of Conquest… |
| `rogue_trader_names` | **55 个**完整船名 | Fortune's Favor, Starlight Merchant, Void Trader, Cosmic Exchange, Galactic Barter, Nebula Nomad… |

HC 巡逻艇船名 = 随机前缀 + 随机后缀（如 “Aegis of Authority”）；不法商人船名可直接从 `rogue_trader_names` 选取。

---

*本文档依据 TianGuan13（NovaSector 分支）源码 `modular_nova/modules/random_ship_event/` 全部 19 个 .dm（1,984 行）整理，双语对照，数值与源码一致。*
