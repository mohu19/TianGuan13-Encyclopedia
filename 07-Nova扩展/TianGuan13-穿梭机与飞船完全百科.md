# TianGuan13 穿梭机与飞船完全百科（Shuttles & Ships Complete Encyclopedia）

> 本文档由 TianGuan13 源码提取编写（NovaSector 分支）。
> 源码范围：`code/modules/shuttle/`（41 个 .dm 文件，7,061 行）+ `code/datums/shuttles/`（17 个 .dm 文件，1,065 行）+ `code/game/shuttle_engines.dm` + `_maps/shuttles/` 全部地图 + NOVA 追加 `modular_nova/modules/advanced_shuttles/`（716 行）+ `modular_nova/master_files/code/modules/shuttle/` + 天官定制 `modular_tianguan/modules/emergency_shuttle_cc/`。
> 穿梭机类型共 **16 种 + NOVA 追加**，全部列出，一个不落。

---

## 目录（Table of Contents）

- [一、总览](#一总览)
- [二、核心机制](#二核心机制)
  - [2.1 停靠端口系统 Docking Ports](#21-停靠端口系统-docking-ports)
  - [2.2 移动流程与状态机 Flight State Machine](#22-移动流程与状态机-flight-state-machine)
  - [2.3 引擎系统 Engines](#23-引擎系统-engines)
  - [2.4 速度与警报等级 Speed & Alert Level](#24-速度与警报等级-speed--alert-level)
  - [2.5 穿梭机控制台 Consoles](#25-穿梭机控制台-consoles)
  - [2.6 导航电脑 Navigation Computers](#26-导航电脑-navigation-computers)
  - [2.7 穿梭机事件 Shuttle Events](#27-穿梭机事件-shuttle-events)
  - [2.8 自定义穿梭机（BYOS）](#28-自定义穿梭机byos)
  - [2.9 辅助设施（遥控器/信标/管理器）](#29-辅助设施遥控器信标管理器)
- [三、穿梭机类型全录（16 种）](#三穿梭机类型全录16种)
  - [3.1 Emergency 紧急逃生穿梭机](#31-emergency-紧急逃生穿梭机)
  - [3.2 Cargo 货运穿梭机](#32-cargo-货运穿梭机)
  - [3.3 Mining 采矿穿梭机](#33-mining-采矿穿梭机)
  - [3.4 Arrival 到达穿梭机](#34-arrival-到达穿梭机)
  - [3.5 Ferry 定期班船](#35-ferry-定期班船)
  - [3.6 Assault Pod 突击舱](#36-assault-pod-突击舱)
  - [3.7 Escape Pod 逃生舱](#37-escape-pod-逃生舱)
  - [3.8 ERT 应急反应队穿梭机](#38-ert-应急反应队穿梭机)
  - [3.9 Infiltrator 渗透艇](#39-infiltrator-渗透艇)
  - [3.10 Pirate 海盗船](#310-pirate-海盗船)
  - [3.11 Hunter 猎船](#311-hunter-猎船)
  - [3.12 Labour 劳改穿梭机](#312-labour-劳改穿梭机)
  - [3.13 Aux Base 辅助基地](#313-aux-base-辅助基地)
  - [3.14 Whiteship 白船（玩家探索船）](#314-whiteship-白船玩家探索船)
  - [3.15 Ruin 废墟穿梭机](#315-ruin-废墟穿梭机)
  - [3.16 Starfury 星怒战斗机](#316-starfury-星怒战斗机)
- [四、NOVA 追加内容](#四nova追加内容)
- [五、TianGuan 定制内容](#五tianguan定制内容)
- [六、附录：各穿梭机引擎/容量速查表](#六附录各穿梭机引擎容量速查表)

---

## 一、总览

TianGuan13（TG 系代码库）的穿梭机系统由以下模块构成：

| 模块 | 源码路径 | 职责 |
|---|---|---|
| 穿梭机核心 | `code/modules/shuttle/shuttle.dm` | `/obj/docking_port` 基础：停靠端口尺寸、坐标计算、注册 |
| 移动端口 | `code/modules/shuttle/mobile_port/mobile_port.dm`（820 行） | `/obj/docking_port/mobile`：飞船本体、引擎列表、状态机、计时 |
| 移动逻辑 | `code/modules/shuttle/mobile_port/shuttle_move.dm` | `initiate_docking()`：飞船实际传送/旋转/搬运内容物 |
| 移动变体 | `code/modules/shuttle/mobile_port/variants/` | emergency / supply / arrivals / ferry / assault_pod / infiltrator / elevator / pod / starfury / custom 各类型行为 |
| 固定端口 | `code/modules/shuttle/stationary_port/` | `/obj/docking_port/stationary`：码头、roundstart 模板、transit 超空间港 |
| 类型定义 | `code/datums/shuttles/`（17 文件） | `/datum/map_template/shuttle/*`：16 种穿梭机的模板/价格/容量 |
| 引擎 | `code/game/shuttle_engines.dm` | `/obj/machinery/power/shuttle_engine` 及 propulsion/burst/large/huge/heater 子型 |
| 控制台 | `code/modules/shuttle/shuttle_consoles/` | 穿梭机控制电脑 + 导航电脑 |
| 事件 | `code/modules/shuttle/shuttle_events/`（10 文件） | 逃生途中遭遇的随机事件（太空鲤鱼/陨石/黑洞…） |
| 杂项 | `code/modules/shuttle/misc/` | 遥控器、导航信标、穿梭机管理器、传送涟漪 |
| 地图 | `_maps/shuttles/` | 全部穿梭机 .dmm 地图（16 个类型目录 + NOVA） |
| NOVA 追加 | `modular_nova/modules/advanced_shuttles/` | 高级飞船引擎（新到达穿梭机 NTV Relay、NOVA 系列地图、音效、壁柜、地板） |
| NOVA 主文件 | `modular_nova/master_files/code/modules/shuttle/` | 舱门闩锁、引擎起降音效 |
| 天官定制 | `modular_tianguan/modules/emergency_shuttle_cc/` | 紧急穿梭机在 CentCom 的停靠适配 |

**16 种穿梭机类型清单**（源码 `code/datums/shuttles/`）：

1. emergency 紧急逃生穿梭机（`emergency.dm`，527 行，52+ 型号）
2. cargo 货运穿梭机（`cargo.dm`，10 型号）
3. mining 采矿穿梭机（`mining.dm`，9 型号）
4. arrival 到达穿梭机（`arrival.dm`，9 型号）
5. ferry 定期班船（`ferry.dm`，7 型号）
6. assault_pod 突击舱（`assault_pod.dm`，1 型号）
7. escape_pod 逃生舱（`escape_pod.dm`，4 型号）
8. ert 应急反应队（`ert.dm`，1 型号 + NOVA SolFed 7 型号）
9. infiltrator 渗透艇（`infiltrator.dm`，3 型号）
10. pirate 海盗船（`pirate.dm`，8 型号）
11. hunter 猎船（`hunter.dm`，5 型号）
12. labour 劳改穿梭机（`labour.dm`，5 型号）
13. aux_base 辅助基地（`aux_base.dm`，2 型号）
14. whiteship 白船（`whiteship.dm`，11 型号 + NOVA 2 型号）
15. ruin 废墟穿梭机（`ruin.dm`，5 型号）
16. starfury 星怒战斗机（`starfury.dm`，4 型号）

> NOVA 追加（`modular_nova/modules/advanced_shuttles/code/shuttles.dm`）：NTV Relay 到达穿梭机（arrivals_nova）、NOVA 紧急/货运/采矿/班船/劳改/白船地图模板、SolFed ERT 穿梭机群等，详见[第四章](#四nova追加内容)。

---

## 二、核心机制

### 2.1 停靠端口系统 Docking Ports

源码：`code/modules/shuttle/shuttle.dm`（188 行）、`code/modules/shuttle/stationary_port/`

- **`/obj/docking_port`（基类）**：不可摧毁（`INDESTRUCTIBLE | LAVA_PROOF | FIRE_PROOF | UNACIDABLE | ACID_PROOF`）、永久锚定、`dir = NORTH`。关键变量：
  - `shuttle_id`：端口/飞船唯一 ID，控制台与码头靠它互相匹配
  - `width` / `height`：覆盖区域尺寸（垂直于/平行于 dir）
  - `dwidth` / `dheight`：停靠点相对覆盖区域的偏移（决定对接时如何对齐）
  - `area_type`：端口所在区域类型；`hidden`：是否对导航电脑隐形
  - `delete_after`：飞船起飞后是否删除该端口（自定义降落点用）
- **`/obj/docking_port/mobile`（移动端口）**：即"飞船"本身，见 2.2。
- **`/obj/docking_port/stationary`（固定端口/码头）**：源码 `stationary_port/stationary_port.dm`。关键点：
  - `roundstart_template`：开局时自动加载的穿梭机模板（如逃生舱码头指定 `/datum/map_template/shuttle/escape_pod/default`）
  - `override_can_dock_checks`：无视尺寸检查强制可停靠（transit 港用）
  - 子型：
    - `/obj/docking_port/stationary/escape_pod`：逃生舱装载码头（5×3，dwidth 1），受站台特质影响（`STATION_TRAIT_SMALLER_PODS` → cramped 舱、`STATION_TRAIT_BIGGER_PODS` → luxury 舱）
    - `/obj/docking_port/stationary/syndicate`：辛迪加"station 附近"停靠区（23×17），含 NW/NE 两个子型
    - `/obj/docking_port/stationary/transit`：超空间港（`override_can_dock_checks = TRUE`），负责向超空间子系统申请保留地块（transit z-level），飞船在超空间停留期间停靠于此
    - `/obj/docking_port/stationary/picked/whiteship`：白船随机停靠港 "Deep Space"（45×45，dwidth 18/dheight 14），开局从 11 张白船地图中随机 pick 一张
    - `/obj/docking_port/stationary/random`：逃生舱随机降落点（随机落在 lavaland/icemoon 地表，距地图边缘 ≥16 格）
- **注册表**：所有端口注册进 `SSshuttle`（`mobile_docking_ports` / `stationary_docking_ports` / `transit_docking_ports` / `custom_shuttles` / `beacon_list`）。ID 冲突时自动追加 `_2`、`_3` 后缀。

### 2.2 移动流程与状态机 Flight State Machine

源码：`code/modules/shuttle/mobile_port/mobile_port.dm`、`shuttle_move.dm`

**状态**（`code/__DEFINES/shuttles.dm`）：`SHUTTLE_IDLE`(空闲) / `SHUTTLE_IGNITING`(点火) / `SHUTTLE_RECALL`(召回中) / `SHUTTLE_CALL`(飞往目的地) / `SHUTTLE_DOCKED`(已停靠) / `SHUTTLE_STRANDED`(搁浅) / `SHUTTLE_DISABLED`(禁用) / `SHUTTLE_ESCAPE`(逃生中) / `SHUTTLE_ENDGAME`(终局) / `SHUTTLE_RECHARGING`(充能) / `SHUTTLE_PREARRIVAL`(降落中)。

**标准移动流程**（`request()` → `check()` 驱动）：
1. `request(destination)`：校验 `canDock()`（尺寸/占用）→ 模式置 `SHUTTLE_IGNITING`，设定 `ignitionTime`（默认 5.5 秒）——NOVA 追加了舱门闩锁 `bolt_all_doors()` 与引擎启动音效
2. 点火完成 → `check_transit_zone()` 申请超空间港 → 模式置 `SHUTTLE_CALL`，计时 `callTime * engine_coeff`（引擎损坏会加倍耗时）
3. 倒计时结束 → `enterTransit()`：停靠到 transit 港，原码头记录为 `previous`（若 `delete_after` 则删除）
4. 到达目标 Z 层 → `initiate_docking(destination)`：`shuttle_move.dm` 的 `takeoff()` / `cleanup_runway()` 将飞船 turf/内容物搬运、旋转、生成视差
5. 可选 `rechargeTime`（充能）→ `SHUTTLE_IDLE`，`unbolt_all_doors()` 解锁舱门（NOVA）
6. `cancel()` 召回：倒转计时器，模式置 `SHUTTLE_RECALL`，飞回 `previous`

**移动物理**：`movement_force = list("KNOCKDOWN" = 3, "THROW" = 0)`（默认）——起飞/降落时击倒 3 格内的人并抛飞；`preferred_direction` 决定动画飞行方向。飞船带 `TRAIT_BLOCK_SHUTTLE_MOVEMENT` 的物体/人会被留在原地。降落前 10 秒在目的地生成"超空间涟漪"（`misc/ripple.dm`，`SHUTTLE_RIPPLE_TIME 100`）。

**紧急穿梭机特例**：`canDock()` 永远返回 `SHUTTLE_CAN_DOCK`——即使码头被堵死也会强行降落（压扁挡路的东西）。若超空间申请反复失败（`transit_failure()`），直接跳转 CentCom 防死锁。

### 2.3 引擎系统 Engines

源码：`code/game/shuttle_engines.dm`（227 行）

- **`/obj/machinery/power/shuttle_engine`**：蓝空间引擎，`max_integrity = 500`，`engine_power = 1`，可拆卸（拧扳手 → 焊接，`ENGINE_WELDTIME = 20 秒`）。焊接中的引擎计入飞船 `engine_list` 与 `initial_engine_power`。
- **子型**：
  - `propulsion` 推进引擎（标准款，不透明）；`propulsion/left`、`propulsion/right`（左右方向图标）
  - `propulsion/burst` 爆发引擎（逃生舱/小型飞船用）+ left/right
  - `heater` 引擎加热器（`engine_power = 0`，为引擎供能的配套设施）
  - `large` 大型引擎（2×2 格，`bound_width/height = 64`）
  - `huge` 巨型引擎（3×3 格，96px）
- **推力 → 速度公式**（`get_engine_coeff()`）：
  - 引擎数与初始一致 → `engine_coeff = 1`（标准速度）
  - 引擎多于初始 → 每多一组向 `ENGINE_COEFF_MIN = 0.5` 靠拢（更快，最少 0.5 倍耗时）
  - 引擎少于初始 → 线性向 `ENGINE_COEFF_MAX = 2` 靠拢（更慢，最多 2 倍耗时）
  - 飞行中引擎变化会实时调整剩余计时（`modTimer`）
- 建造引擎需要电路板 `circuitboard/machine/engine`（heater/propulsion），可通过平板件（flatpack）摆放后扳手+焊接接入飞船。

### 2.4 速度与警报等级 Speed & Alert Level

源码：`code/__DEFINES/shuttles.dm`、`code/controllers/subsystem/shuttle.dm`

- **警报系数** `ALERT_COEFF`：绿 `2.0` / 蓝 `1.0`（默认）/ 红 `0.5` / 德尔塔 `0.25`；自动疏散：正常 `2.5` / 危急 `0.4`。紧急穿梭机呼叫时间 = `emergency_call_time(10 分钟) × alert_coeff × engine_coeff`。
- **紧急穿梭机标准时序**（SSshuttle）：呼叫到站 **10 分钟**（`emergency_call_time`）；停靠停留 **3 分钟**（`emergency_dock_time`）；离站飞往 CentCom **2 分钟**（`emergency_escape_time`）。
- **货运穿梭机**：`callTime = 60 SECONDS`（单程 1 分钟），`movement_force = 0/0`（不击倒不抛飞）。
- **到达穿梭机**：`callTime = INFINITY`（全自动），`ignitionTime = 5 秒`。
- **NOVA NTV Relay**：`callTime = 9 秒`、`ignitionTime = 5 秒`、`rechargeTime = 9 秒`（最快往返系统）。
- **自定义穿梭机（BYOS）**：`CUSTOM_ENGINE_COEFF_MIN 0.5 / MAX 10`，推力公式 `thrust_ratio = (引擎功率 × 倍率) / (格数 + 偏移)`，`calculated_multiplier = 2×(1-e^-thrust_ratio)`，取倒数并钳制。

### 2.5 穿梭机控制台 Consoles

源码：`code/modules/shuttle/shuttle_consoles/`

- **`/obj/machinery/computer/shuttle`（通用穿梭机控制台）**：`shuttleId`（绑定飞船）、`possible_destinations`（分号分隔的港口 ID 列表）、`locked`（锁）、`admin_controlled`（需管理员批准）。功能：查看停靠位置/倒计时、选择目的地、发送飞船；`request` 按钮可向管理员申请权限（1 分钟冷却）。文盲（无法阅读）点击后 10 秒会把飞船随机发往一个合法目的地（欢乐机制）。emag 可清空 `req_access` 解锁。
- **`/obj/machinery/computer/emergency_shuttle`（紧急穿梭机控制台）**：位于逃生舱内，`auth_need = 3`——需 **3 名指挥权限（ACCESS_COMMAND）成员刷卡授权**才能提前发射（每 5 秒动作延迟）；emag 直接跳过授权并发射，同时把 `movement_force` 变为 `KNOCKDOWN 60 / THROW 20`（"安全带救不了你"）。支持"劫机"：有劫机天赋（hijack）的玩家可反复点击控制台推进劫机阶段（`HIJACK_NOT_BEGUN → STAGE_1..4 → HIJACK_COMPLETED`，每阶段 5 秒），完成后飞船改道飞往辛迪加基地。
- **`/obj/machinery/computer/shuttle/ferry`（班船控制台）**：`req_access = list(ACCESS_CENT_GENERAL)`（中央通用权限），目的地 `ferry_home;ferry_away;ferry_ntf`；`allow_silicons = FALSE`（AI/机器人默认不能用）；有 `request` 子型。
- **`/obj/machinery/computer/shuttle/syndicate`（辛迪加控制台）**：`req_access = ACCESS_SYNDICATE`，目的地含 `syndicate_away;syndicate_z5;syndicate_ne;nw;n;se;sw;s;syndicate_custom`；电路板带 20 分钟"挑战计时器"（开局 20 分钟内不能起飞）；`recall` 召回子型；`drop_pod` 突击舱发射子型（`shuttleId = "steel_rain"`）。
- **`/obj/machinery/computer/shuttle/white_ship`（白船控制台）**：目的地 `whiteship_away;home;z4;waystation;lavaland;custom`，`may_be_remote_controlled = TRUE`（可配遥控器）；桥楼子型内置 GPS。
- **`/obj/machinery/computer/shuttle/pod`（逃生舱控制台）**：`locked = TRUE`，**仅红色警报及以上**或 emag 后才能发射；目标 `pod_asteroid`（lavaland）。emag 解锁并显示红屏。
- **`/obj/machinery/computer/shuttle/labor`（劳改控制台）**：`shuttleId = "laborcamp"`，目的地 `laborcamp_home;laborcamp_away`，`req_access = ACCESS_BRIG`；`one_way` 囚犯版：无权限要求、只能单向召唤到劳改营。
- **`/obj/machinery/computer/shuttle/mining`（采矿控制台）**：目的地 `mining_home;mining_away;landing_zone_dock`，`no_destination_swap = TRUE`；`common` 子型（lavaland 公共穿梭机）目的地 `commonmining_home;lavaland_common_away;landing_zone_dock;mining_public`。放逐植入体（exile implant）携带者会被锁死控制台。
- **`/obj/machinery/computer/shuttle/monastery_shuttle`**：修道院穿梭机（`shuttleId = "mining_common"`），目的地 `monastery_shuttle_asteroid;monastery_shuttle_station;lavaland_common_away;landing_zone_dock;mining_public`。
- **`/obj/machinery/computer/shuttle/starfury/*`**：星怒战斗机/护卫舰控制台（ACCESS_SYNDICATE），见 3.16。
- **`/obj/machinery/computer/shuttle/custom_shuttle`**：BYOS 自建穿梭机控制台，自动绑定所在飞船并改名，可远程控制。
- **NOVA 高级控制台**（`advanced_shuttles/code/computer.dm`）：`pod/advanced`、`emergency_shuttle/advanced`、`crew/shuttle`、`security/shuttle`、`ferry/shuttle`——均为 NOVA 图标风格（不可连接、无地图预览）。

### 2.6 导航电脑 Navigation Computers

源码：`code/modules/shuttle/shuttle_consoles/navigation_computer.dm`（485 行）

- **`/obj/machinery/computer/camera_advanced/shuttle_docker`**：高级摄像头导航电脑，让玩家为飞船**指定任意自定义降落点**（`shuttlePortId = "[shuttleId]_custom"`）：
  - 通过远程摄像头眼（eyeobj）在合法地块上放置/旋转降落区（绿=可降落，红=被阻挡）
  - 合法地块白名单：太空、镀层地板、岩浆、开放空间、杂项（`whitelist_turfs`）；禁止区域：保留层、CentCom、away 层（`locked_traits`）；距地图边缘 ≥10 格
  - 支持"跳到已知道路点"（`jump_to_ports`，如辛迪加 6 个方位、白船 4 个固定港）
  - 子型：`syndicate`（辛迪加，view_range 5.5，`CAMERA_LOCK_STATION`）、`whiteship`（白船，view_range 10，designate_time 100，`GLOB.jam_on_wardec` 战争宣言时干扰）、`custom`（BYOS，zlink_range 1）、`syndicate/fighter|corvette`（星怒机群，y_offset 3）
- **导航信标**（`misc/spaceship_navigation_beacon.dm`）：`/obj/machinery/spaceship_navigation_beacon` 无线电导航信标，持续广播位置，可被导航电脑"跳到"（多工具可改名/锁定），用于在未开发区域建立航路点。

### 2.7 穿梭机事件 Shuttle Events

源码：`code/modules/shuttle/shuttle_events/`（10 个文件）

紧急穿梭机离站后会按概率随机挂载事件（`setup_shuttle_events()` 按 `event_probability` 抽取；购买型穿梭机可在模板上用 `events` 列表指定权重）。事件在飞行途中按 `activation_fraction` 激活，向飞船砸东西或摇晃飞船：

| 事件 | 名称 | 概率 | 说明 |
|---|---|---|---|
| `black_hole` | Black Hole (Oh no!) | 0（仅管理员） | 黑洞；`no_escape` 巨型黑洞不可取消 |
| `carp` | Carp Nest! (Very Dangerous!) | 0.4% | 太空鲤鱼群撞击飞船 |
| `carp/friendly` | Passive Carp Nest! | 3% | 无害鲤鱼群 |
| `carp/friendly_but_no_personal_space` | 舒适鲤鱼群穿过飞船 | 0 | 穿船骚扰 |
| `carp/magic` | Magical Carp Nest! | 0 | 魔法鲤鱼 |
| `meteor/dust` | Dust Meteors! (Mostly Safe) | — | 尘埃陨石 |
| `meteor/safe` | Various Meteors! (Safe) | — | 各类陨石 |
| `meteor/dust/meaty` | Meaty Meteors! | — | 肉陨石 |
| `projectile/fireball` | Fireball Burst | — | 火球弹幕 |
| `turbulence` | Turbulence | — | 湍流摇晃+击倒全员 |
| `italian` | Italian Storm! (Mama Mia!) | — | 意大利面风暴 |
| `fake_ttv` | Fake TTV (Harmless!) | — | 假炸弹 |
| `maintenance` | Maintenance Debris | — | 维修杂物 |
| `player_controlled/human/greytide` | Greytide! (10 assistants) | 0.1% | 10 个灰潮助手（幽灵接管） |
| `player_controlled/human/greytide/interns` | Intern Wave（10 实习） | 0 | 无武装实习生 |
| `player_controlled/human/greytide/interns/armed` | Intern Wave (Armed) | 0 | 武装实习生 |
| `player_controlled/human/hitchhiker` | Hitchhiker! | 1% | 搭车客（单个幽灵） |
| `player_controlled/fire_shark` | Three player controlled fire sharks! | 0 | 3 条玩家火鲨 |

事件在逃生开始时获得总时长（`start_up_event(emergency_escape_time * engine_coeff)`），按比例定时激活。

### 2.8 自定义穿梭机（BYOS）

源码：`code/modules/shuttle/mobile_port/variants/custom/`（blueprints.dm 786 行、custom.dm、custom_consoles.dm）、`code/datums/elements/shuttle_construction_turf.dm`

- **`/obj/docking_port/mobile/custom`**：玩家自建穿梭机（Build Your Own Shuttle）。`canMove()` 要求 `current_engine_power > 0`（没引擎飞不走）；速度按面积与引擎功率的比值计算（见 2.4）。
- **蓝图系统**（`/obj/item/shuttle_blueprints`）：玩家用蓝图在指定区域圈地，铺地板/墙/引擎逐步搭建自己的飞船，实时可视化（`shuttle_construction_visualization` 客户端图像），完工后注册为正式穿梭机（`SSshuttle.custom_shuttles`）。
- **配套**：`custom_shuttle` 控制台、`shuttle_docker/custom` 导航电脑、`whiteship_home` 作为默认出发点。
- **现成底盘**：紧急穿梭机 `construction`（Build your own shuttle kit，5 份货舱价值）与 `constructionbig`（Build your own CRUISER kit，30 份）——买回来一个空壳车架 + 建材，自己装修。

### 2.9 辅助设施（遥控器/信标/管理器）

- **穿梭机遥控器**（`misc/shuttle_remote.dm`）：`/obj/item/shuttle_remote`，可绑定任意 `may_be_remote_controlled` 的控制台（默认绑定白船：home = whiteship_home，away = whiteship_lavaland），一键派船/召回，Alt+右键可改绑定点；不能用于自定义降落点之外的多点控制。
- **穿梭机管理器**（`misc/manipulator.dm`）：`/obj/machinery/shuttle_manipulator`（管理员专用），可加载/传送/删除任何穿梭机模板，`intoTheSunset()` 可把飞船连同乘客"送进夕阳"（结算逃生成就）。
- **飞船门锁**（NOVA，`modular_nova/master_files/code/modules/shuttle/shuttle.dm`）：起飞点火时 `bolt_all_doors()` 闩死所有飞船气闸（含同 ID 联动的站内闸），到站/落地 `unbolt_all_doors()` 解锁。
- **起降音效**（NOVA）：`takeoff_sound = engine_startup.ogg`、`landing_sound = engine_landing.ogg`，`sound_range = 11`，距离每格衰减 5 音量，受玩家"飞船环境音量"偏好控制；`shuttle_sounds = FALSE` 可关闭（到达穿梭机即关闭）。

---

## 三、穿梭机类型全录（16 种）

> 每节格式：中文名 / 英文名 / 源码路径 / 容量 / 引擎 / 速度 / 乘员与用途 / 获取与召唤方式 / 型号清单。

### 3.1 Emergency 紧急逃生穿梭机

- **中文名**：紧急逃生穿梭机（Emergency Shuttle）｜**英文**：Emergency / Escape Shuttle
- **源码路径**：`code/datums/shuttles/emergency.dm`（527 行）；移动端口 `code/modules/shuttle/mobile_port/variants/emergency/emergency.dm`（318 行）、`emergency_types.dm`（backup/monastery/BYOS）、`emergency_console.dm`（331 行，紧急控制台）、`pods.dm`（逃生舱）
- **移动端口**：`/obj/docking_port/mobile/emergency`，`shuttle_id = "emergency"`，`dir = EAST`、`port_direction = WEST`
- **容量**：因型号而异，官方定义 `occupancy_limit` 从 **5 到 110+**（如 Box 45、Delta 75、Cere 110、Cruise 80、Casino 85、Narnar 666、Meteor "CONDEMNED"）
- **引擎**：随型号/地图配置——NOVA 默认标准型（`emergency_nova.dmm`）**5 台**（4 × propulsion + 1 × heater）；Box 标准型 2 台（1 propulsion + 1 heater）
- **速度**：呼叫到站 **10 分钟** × 警报系数 × 引擎系数（引擎全毁 → 20 分钟）；停靠 **3 分钟**；离站 → CentCom **2 分钟**
- **乘员与用途**：全站逃生的最终手段。舱内标准配置医疗舱/禁闭室/货舱；`canDock` 强制降落；离站后可被**劫机**（hijack）改道辛迪加；飞行途中触发穿梭机事件（鲤鱼/陨石等）
- **获取/召唤方式**：
  - 通讯控制台（communications console）呼叫（可召回）；指挥层可刷卡**提前发射**
  - **购买**：通过货仓预算在通讯控制台购买（`credit_cost` 以 `CARGO_CRATE_VALUE` 计；`who_can_purchase` 默认船长，部分型号船长+CE），到货后仍需正常呼叫发射
  - emag 控制台：立即发射 + 10 秒倒计时 + 超级击倒
  - `emag_only` 型号（monastery/luxury/discoinferno/supermatter/imfedupwiththisworld）只能用 emag 买
  - 部分型号有解锁前置（`prerequisites_met`）：medisim（全息模拟）、arena（Bubblegum 击杀）、scrapheap（预算归零）、narnar（邪教符文）、wabbajack（魔法变形）、zeta（外星科技）
- **型号清单（52+）**：backup（备份）、construction / constructionbig（DIY 套件）、asteroid（小行星站）、venture、humpback（座头鲸，带吧台+鲤鱼事件）、bar（紧急逃生酒吧，Bardrone/Barmaid 神模式酒保）、pod（紧急舱 x2，惩罚用）、russiafightpit（Mother Russia Bleeds，斗熊场）、meteor（小行星+引擎，压扁逃生区）、monastery（大公司修道院，摧毁 1/4 空间站）、luxury（豪华金色穿梭机，登船费 2000 信用点/人——NOVA 调整）、medisim（中世纪模拟穹顶，CTF）、discoinferno（迪斯科地狱）、arena（竞技场，需击杀泡泡糖）、birdboat、box、donut、clown（Snappop(tm)!）、cramped（STV5 保安运输船）、meta、kilo、mini、tram（太空火车）、birdshot、catwalk、wawa（垃圾车）、scrapheap（废料堆挑战，倒贴钱）、narnar（Shuttle 667 邪教船）、pubby、cere（110 人巨舰）、supermatter（超物质巨舰）、imfedupwiththisworld（Oh, Hi Daniel 木屋小艇）、goon（NES Port）、rollerdome（Uncle Pete 轮滑馆）、basketball（篮球场）、wabbajack（NT Lepton Violet，魔法变形青蛙雕像）、omega、cruise（NTSS Independence 游轮，100 份货价）、monkey（生物穹顶，猴子）、casino（Lucky Jackpot 赌场）、shadow（NTSS Shadow 等离子引擎快船）、fish（钓鱼佬专用）、lance（战术撞击系统，带炮塔）、tranquility（宁静搬迁船）、hugcage（抱抱船）、fame（名人堂）、delta、northstar、nebula、raven（CentCom 渡鸦巡洋舰，护盾+炮塔）、zeta（Tr%nPo2r& Z3TA 外星船）

### 3.2 Cargo 货运穿梭机

- **中文名**：货运穿梭机（Supply Shuttle）｜**英文**：Cargo / Supply Shuttle
- **源码路径**：`code/datums/shuttles/cargo.dm`（44 行）；移动端口 `code/modules/shuttle/mobile_port/variants/supply.dm`（380 行）
- **移动端口**：`/obj/docking_port/mobile/supply`，`shuttle_id = "cargo"`，`dir = WEST`、`port_direction = EAST`
- **容量**：视地图而定（标准货舱可放约 10+ 板条箱；`empty_turfs` 决定装载格数）
- **引擎**：Box 型 **4 台**（1 heater + 1 propulsion + 1 burst/left + 1 burst/right）；NOVA Supply Shuttle **3 台**（mining_nova 同款布局）
- **速度**：`callTime = 60 SECONDS`（单程 1 分钟）；`movement_force = 0/0`（不击倒不抛飞）
- **乘员与用途**：纯货运——在空间站货仓（cargo bay）与 CentCom 之间往返，运输购物清单上的订购货物（`buy()`：板条箱/快递箱生成、部门预算扣款、市场矿石调价），到站自动**卖出**舱内物品（`sell()` 按出口价值结算给货仓预算）并派发邮件（economy 子系统）。
- **获取/召唤方式**：货仓控制台（supply console）下单 → 自动发货；开局自动配置（`roundstart_template`，由 `SSmapping.current_map.shuttles["cargo"]` 决定型号）。**禁运黑名单**（30+ 类）：活物、核弹磁盘、摄像机、传送机、信标、GPS、`TRAIT_BANNED_FROM_CARGO_SHUTTLE` 物品等不可上船，违者被 CentCom 用补给舱丢回空间站。另有"货船租借"事件（shuttle_loan）可将货船借给其他公司赚外快。
- **型号清单（10）**：mini、kilo、birdboat、pubby、birdshot、box、delta、northstar、catwalk、nebula（NOVA 追加 `cargo/nova` Supply Shuttle 与 `cargo/nova/ouroboros`，见第四章）

### 3.3 Mining 采矿穿梭机

- **中文名**：采矿穿梭机（Mining Shuttle / Lavaland Shuttle）｜**英文**：Mining / Mining Common
- **源码路径**：`code/datums/shuttles/mining.dm`（47 行）；控制台 `code/modules/mining/mine_items.dm`
- **移动端口**：两个端口 ID——`mining`（主矿站，个人矿船）与 `mining_common`（lavaland 公共穿梭机）
- **容量**：视地图（标准 2×3 起，`large` 型更大；NOVA Manticore 大型）
- **引擎**：NOVA NMC Phoenix（`mining_nova.dmm`）**3 台**；普通型 1-2 台
- **速度**：标准 `callTime`（10 秒级，快进快出）
- **乘员与用途**：矿工往返空间站 ↔ lavaland / icemoon 矿场（`mining_home` / `mining_away` / `landing_zone_dock`），也可自定义降落（landing zone dock）。`no_destination_swap = TRUE`（起飞后不能改目的地）。放逐植入体携带者禁用控制台。
- **获取/召唤方式**：采矿站控制台 / lavaland 穿梭机控制台召唤；开局按地图配置 `mining_home` 加载。
- **型号清单（9）**：mining：box、delta、kilo、large、northstar、nebula；mining_common：meta、kilo、northstar（NOVA 追加：mining/nova = NMC Phoenix、mining/nova/large = NMC Manticore、mining_common/nova = NMC Chimera）

### 3.4 Arrival 到达穿梭机

- **中文名**：到达穿梭机（Arrivals Shuttle）｜**英文**：Arrival
- **源码路径**：`code/datums/shuttles/arrival.dm`（40 行）；移动端口 `code/modules/shuttle/mobile_port/variants/arrivals.dm`（229 行）
- **移动端口**：`/obj/docking_port/mobile/arrivals`，`shuttle_id = "arrival"`，`dir = WEST`、`port_direction = SOUTH`
- **容量**：椅子数即晚入出生点（`latejoin_trackers` 直接取舱内所有椅子）
- **引擎**：Box 型 **5 台**（arrival_box.dmm）
- **速度**：`callTime = INFINITY`（完全自动，无手动呼叫）；`ignitionTime = 5 秒`；`movement_force = 3/0`
- **乘员与用途**：**新人出生/晚入（latejoin）专用**——新人坐在椅子上从天而降进站；自动往返于"空间站 ↔ 到达中继站"。
- **获取/召唤方式**：全自动。检测到舱内压力异常（`CheckTurfsPressure`：过低/过高）会报"broken"并停运；检测到活人（`PersonCheck`）或核弹磁盘（`NukeDiskCheck`）会取消起飞/加速回站（防止偷渡/磁盘被盗）；站台请求控制台可 `RequireUndocked` 强制叫回。
- **型号清单（9）**：box、donut、birdshot、delta、kilo、pubby、northstar、catwalk、nebula（NOVA 另见 NTV Relay，第四章）

### 3.5 Ferry 定期班船

- **中文名**：定期班船（Transport Ferry）｜**英文**：Ferry
- **源码路径**：`code/datums/shuttles/ferry.dm`（46 行）；控制台 `code/modules/shuttle/mobile_port/variants/ferry.dm`（29 行）
- **移动端口**：`shuttle_id = "ferry"`，目的地 `ferry_home;ferry_away;ferry_ntf`
- **容量**：视地图（标准班船 2 排座位 + 中央过道；lighthouse 大型）
- **引擎**：base 型 **2 台**（ferry_base.dmm，2 × propulsion）
- **速度**：标准 `callTime`（约 10 秒级）
- **乘员与用途**：**中央指挥部（CentCom）↔ 空间站**的人员通勤班船；NOVA 目标 `ferry_ntf`（Nova 联邦中继）。
- **获取/召唤方式**：班船控制台，**需要 `ACCESS_CENT_GENERAL`（中央通用）权限**（乘客/官方人员）；`allow_silicons = FALSE`（AI 不可用）；emag 默认无效（`allow_emag = FALSE`）。管理员可召唤 lighthouse（比码头大，需强制停靠）。
- **型号清单（7）**：base（标准 Box/Meta 班船）、meat（"肉"船，辛迪加肉铺）、lighthouse（灯塔，军用舰残骸）、fancy（豪华地板）、kilo、northstar、nebula（NOVA 追加：`ferry/nova` = NAV Monarch）

### 3.6 Assault Pod 突击舱

- **中文名**：突击舱（Steel Rain 钢铁雨）｜**英文**：Assault Pod
- **源码路径**：`code/datums/shuttles/assault_pod.dm`（7 行）；移动端口 `code/modules/shuttle/mobile_port/variants/assault_pod.dm`（85 行）；地图 `_maps/shuttles/assault_pod_default.dmm`
- **移动端口**：`/obj/docking_port/mobile/assault_pod`，`shuttle_id = "steel_rain"`（钢铁雨）
- **容量**：单人突击舱（默认降落区 7×7；中世纪版 15×9）
- **引擎**：默认地图无自带引擎（由辛迪加母舰/调用方配置）
- **速度**：直接落地（`initiate_docking` 到目标即爆炸音效）
- **乘员与用途**：**核反派（Nuke Ops）的轨道空降舱**——从保留层（母舰）一键砸向空间站任意区域，舱体本身是武器。
- **获取/召唤方式**：`/obj/item/assault_pod`（突击舱目标指示器）——选择目标区域后生成一次性降落码头（7×7），所有 `shuttleId = "steel_rain"` 的突击舱控制台立即获得该降落点；已被发射的舱不可再发射（`z in ZTRAIT_STATION` 检查）。中世纪海盗版 `/obj/item/assault_pod/medieval`（"Shuttle placement designator"蓝色图，15×9）用于海盗船（`shuttle_id = "pirate"`）落地。
- **型号清单（1）**：default

### 3.7 Escape Pod 逃生舱

- **中文名**：逃生舱（Escape Pod）｜**英文**：Escape Pod
- **源码路径**：`code/datums/shuttles/escape_pod.dm`（24 行）；移动端口 `code/modules/shuttle/mobile_port/variants/emergency/pods.dm`（164 行）
- **移动端口**：`/obj/docking_port/mobile/pod`，`shuttle_id = "pod"`，`launch_status = UNLAUNCHED`
- **容量**：default 2 格内部空间（1-2 人）；luxury 3 格；cramped 1 座
- **引擎**：**1 台 burst 爆发引擎**（全部 4 种型号地图）
- **速度**：红色警报/紧急疏散时随紧急穿梭机一同发射，飞往 lavaland 随机降落（或 icemoon）
- **乘员与用途**：空间站各处的紧急逃生舱。舱内标配（`/obj/item/storage/pod` 壁挂柜）：2 套橙色紧急太空服+头盔、2 个防毒面具、2 个氧气罐、2 把"紧急下船镐"、求生胶囊、紧急工具箱、2 个裹尸袋——降落后靠镐子挖出自己。
- **获取/召唤方式**：舱内 `pod control computer`——**红色警报及以上**才能发射（`SEC_LEVEL_RED`），emag 可提前解锁（锁定状态随警报等级自动更新）；紧急疏散时所有未发射的舱自动起飞（`on_emergency_launch`），到达后停靠各自 `pod_away` 码头。站台特质可全局替换型号（smaller → cramped、bigger → luxury）。
- **型号清单（4）**：default（2 格）、large（旧 Pubby 修道院船）、luxury（3 格）、cramped（无窗 1 座）

### 3.8 ERT 应急反应队穿梭机

- **中文名**：应急反应队穿梭机（ERT Shuttle）｜**英文**：Emergency Response Team
- **源码路径**：`code/datums/shuttles/ert.dm`（8 行）；`code/datums/ert.dm`（队伍定义）；地图 `_maps/shuttles/ert_bounty.dmm`；NOVA `modular_nova/master_files/code/datums/ert.dm` + `_maps/shuttles/nova/ert_solfed_*.dmm`（7 张）
- **容量**：5-6 人小队（视 ERT 队伍 `teamsize`；赏金猎船 3-4 人）
- **引擎**：ert_bounty **2 台**；ert_solfed_assault **2 台**（NOVA）
- **速度**：标准（呼叫即达，约 10 秒级）
- **乘员与用途**：中央指挥部派出的武装反应队运输船——含武器柜、医疗舱等；NOVA 的 **SolFed（太阳联邦）** 系列包含 7 种职能船：官方审计队、Espatier 突击队、军械库、移动医院、工程舱、豪华版（详见型号清单）。
- **获取/召唤方式**：管理员/事件系统按 `/datum/ert` 的 `ert_template` 召唤；`/datum/ert/bounty` 赏金猎人 ERT 用 `ert_bounty` 地图；NOVA `/datum/ert/solfed/*`（official / espatier / assault / armory / medical / engineer / fancy / grand_espatier 等）。
- **型号清单（1 基础 + 7 NOVA）**：bounty（Bounty Hunter ERT Shuttle）；NOVA：solfed/official、solfed（espatier）、solfed/assault、solfed/armory、solfed/medical、solfed/engineer、solfed/fancy

### 3.9 Infiltrator 渗透艇

- **中文名**：辛迪加渗透艇（Syndicate Infiltrator）｜**英文**：Infiltrator
- **源码路径**：`code/datums/shuttles/infiltrator.dm`（19 行）；移动端口 `code/modules/shuttle/mobile_port/variants/infiltrator.dm`（16 行）；控制台 `code/modules/shuttle/shuttle_consoles/syndicate.dm`
- **移动端口**：`/obj/docking_port/mobile/infiltrator`，`shuttle_id = "syndicate"`，`hidden = TRUE`（对导航电脑隐身！），`movement_force = 0/0`
- **容量**：basic 为单层小型（约 4-6 人）；advanced 更大（"更像 Kilo 空间站"，带 APC 与 atmos）
- **引擎**：basic 型 **12 台**（3 × heater + 3 × propulsion + 3 × propulsion/left + 3 × propulsion/right）
- **速度**：标准；`hidden` 使其停靠时不产生导航提示
- **乘员与用途**：**核反派（Nuke Ops）的默认母舰/渗透艇**——停在空间站附近的 6 个方位之一（`syndicate_ne/nw/n/se/sw/s`），核弹小队由此出发；可自定义降落点。
- **获取/召唤方式**：核战模式开局自动加载（`syndicate` 端口）；控制台 `ACCESS_SYNDICATE`，电路板含 **20 分钟挑战计时器**（开局 20 分钟内禁止起飞，防速推）；`syndicate_away` 召回点；导航电脑 `shuttle_docker/syndicate` 指定精确降落点（view_range 5.5）。
- **型号清单（3）**：basic（标准，核战默认）、advanced（大型，带 atmos/APC）、clown（小丑版，自带香蕉炸弹）

### 3.10 Pirate 海盗船

- **中文名**：海盗船（Pirate Ship）｜**英文**：Pirate
- **源码路径**：`code/datums/shuttles/pirate.dm`（36 行）；地图 `_maps/shuttles/pirate/`
- **容量**：视型号（default 中型；dutchman 幽灵船大型）
- **引擎**：pirate_default **4 台**
- **速度**：标准
- **乘员与用途**：太空海盗的劫掠船，由海盗事件/管理员生成，袭击商队与空间站；中世纪海盗（Siege Pod）用"蓝色图"指定 15×9 降落区攻城。
- **获取/召唤方式**：海盗随机事件（pirate event）或管理员召唤；`/obj/item/assault_pod/medieval` 蓝色图选择降落点。
- **型号清单（8）**：default、silverscale（Silver Scales 银鳞号）、dutchman（Flying Dutchman 飞翔的荷兰人）、interdyne（Pharmaceutics Biocraft 制药生化船）、grey（The Space Toolbox 太空工具箱）、irs（Space IRS 太空税务局）、geode（Lustrous Geode 璀璨晶洞号）、medieval（Siege Pod 攻城舱）

### 3.11 Hunter 猎船

- **中文名**：猎船（Hunter Ships）｜**英文**：Hunter
- **源码路径**：`code/datums/shuttles/hunter.dm`（24 行）；地图 `_maps/shuttles/hunter/`
- **容量**：小型（1-3 人）
- **引擎**：视地图（小型 1-2 台）
- **速度**：标准
- **乘员与用途**：在深空游荡的**随机遭遇船**（兴趣点 POI）——警察、赏金猎人、灵能算命、俄国货船等，玩家可登船互动/打捞。
- **获取/召唤方式**：随机事件/管理员生成（`/datum/map_template/shuttle/hunter/*`）。
- **型号清单（5）**：space_cop（Police Spacevan 太空警车）、russian（Russian Cargo Ship 俄国货船）、bounty（Bounty Hunter Ship 赏金猎人船）、psyker（Psyker Fortune-Telling Ship 灵能算命船）、mi13_foodtruck（Perfectly Ordinary Food Truck 平平无奇餐车）

### 3.12 Labour 劳改穿梭机

- **中文名**：劳改穿梭机（Labor Shuttle）｜**英文**：Labour / Labor Camp Shuttle
- **源码路径**：`code/datums/shuttles/labour.dm`（24 行）；控制台 `code/modules/mining/laborcamp/laborshuttle.dm`
- **移动端口**：`shuttle_id = "laborcamp"`，目的地 `laborcamp_home;laborcamp_away`
- **容量**：约 8-10 人（牢笼式座舱）
- **引擎**：Box 型 **2 台**（1 heater + 1 propulsion）
- **速度**：标准
- **乘员与用途**：把**囚犯**从空间站禁闭室运往劳改营（lavaland 矿场）服刑；`one_way` 囚犯控制台只能单向召唤（防止囚犯自己飞回来）；返回端由警长（ACCESS_BRIG）控制。
- **获取/召唤方式**：劳改控制台（警长权限）召唤；开局按地图 `laborcamp_home` 加载（NOVA 统一用 `labour/nova` = NMC Drudge）。
- **型号清单（5）**：box、generic、delta、kilo、nebula（NOVA 追加：labour/nova = NMC Drudge）

### 3.13 Aux Base 辅助基地

- **中文名**：辅助基地（Auxiliary Base）｜**英文**：Aux Base
- **源码路径**：`code/datums/shuttles/aux_base.dm`（12 行）；地图 `_maps/shuttles/aux_base/`
- **移动端口**：`port_id = "aux_base"`
- **容量**：小型基地模块（default 中型 / small 小型）
- **引擎**：视地图
- **速度**：标准
- **乘员与用途**：可部署的辅助基地模块（中继站/临时据点），用于事件与管理场景。
- **获取/召唤方式**：管理员/事件按模板加载；`who_can_purchase = null`（玩家不可购买）。
- **型号清单（2）**：default、small

### 3.14 Whiteship 白船（玩家探索船）

- **中文名**：白船（玩家可操控探索船）｜**英文**：White Ship
- **源码路径**：`code/datums/shuttles/whiteship.dm`（59 行）；控制台 `code/modules/shuttle/shuttle_consoles/white_ship.dm`；端口 `code/modules/shuttle/stationary_port/port_types.dm`（`picked/whiteship`，45×45 "Deep Space"）
- **移动端口**：`shuttle_id = "whiteship"`，目的地 `whiteship_away;whiteship_home;whiteship_z4;whiteship_waystation;whiteship_lavaland;whiteship_custom`
- **容量**：视型号（meta 大型货舱、tram 超长货船、delta 护卫舰级）
- **引擎**：whiteship_box **6 台**（1 heater + 1 propulsion + 2 left + 2 right）——豪华配置
- **速度**：标准；可自定义降落点（`shuttle_docker/whiteship`，designate_time 100）
- **乘员与用途**：深空中**随机刷新的一艘可操控探索船**——船骸中常有物资/敌人/彩蛋，玩家清空后可据为己有：当医院船、打捞船、采矿船、巡逻艇…… 支持**穿梭机遥控器**远程派船/召回（`shuttle_remote` 默认绑定白船）。
- **获取/召唤方式**：开局在 "Deep Space" 港口从 11 张地图中随机加载一艘（`shuttlekeys` 随机 pick）；桥上白船控制台 + 导航电脑驾驶。
- **型号清单（11 + 2 NOVA）**：box（Hospital Ship 医院船）、meta（Salvage Ship 打捞船）、pubby（NT Science Vessel 科学船，愤怒蚂蚁）、cere（NT Heavy Salvage Vessel 重型打捞船，太阳帆+PACMAN）、birdshot（NT Patrol Bee 巡逻蜂，果冻立方怪）、kilo（NT Mining Shuttle 采矿船）、donut（NT Long-Distance Bluespace Jumper 蓝空间跳跃船）、tram（NT Long-Distance Bluespace Freighter 蓝空间货船，比码头大）、delta（NT Frigate 护卫舰，大蜘蛛）、personalshuttle（Personal Travel Shuttle 私人旅行船）、obelisk（Obelisk 方尖碑，邪教研究船）；NOVA：blueshift（SFS Christian，外星寄生大护卫舰）、ouroboros（JN Chasse-Galerie 移动咖啡馆）

### 3.15 Ruin 废墟穿梭机

- **中文名**：废墟穿梭机（太空废墟船）｜**英文**：Ruin Shuttles
- **源码路径**：`code/datums/shuttles/ruin.dm`（29 行）；地图 `_maps/shuttles/ruin/`
- **容量**：小型（1-4 人；cyborg_mothership 大型）
- **引擎**：cyborg_mothership **5 台**；syndicate_dropship **4 台**；caravan_victim **4 台**；pirate_cutter **4 台**；syndicate_fighter_shiv **1 台**
- **速度**：标准
- **乘员与用途**：散落在深空废墟/小行星中的**废弃船骸**，供玩家探索、打捞、对抗残留敌人（蜂巢机器人、辛迪加突击队、海盗、邪教构造体等）。
- **获取/召唤方式**：地图加载时作为太空废墟（ruins）生成，非玩家召唤。
- **型号清单（5）**：cyborg_mothership（Cyborg Mothership 机器人母舰）、caravan_victim（Small Freighter 小货船，辛迪加突击队伏击现场）、pirate_cutter（Pirate Cutter 海盗快艇，能量弯刀）、syndicate_dropship（Syndicate Dropship 辛迪加运输船，激光炮塔）、syndicate_fighter_shiv（Syndicate Fighter 辛迪加战斗机，1 格空间+4 激光炮塔）

### 3.16 Starfury 星怒战斗机

- **中文名**：星怒战斗机（SBC Starfury 机群）｜**英文**：Starfury Fighters & Corvette
- **源码路径**：`code/datums/shuttles/starfury.dm`（21 行）；`code/modules/shuttle/mobile_port/variants/battlecruiser_starfury.dm`（181 行）；地图 `_maps/shuttles/starfury/` + `_maps/templates/battlecruiser_starfury.dmm`
- **移动端口**：`/obj/docking_port/mobile/syndicate_fighter`（`SBC_fighter1/2/3`）+ `/obj/docking_port/mobile/syndicate_corvette`（`SBC_corvette`），全部 `hidden = TRUE`
- **容量**：战斗机单人（机库 5×7）；护卫舰 2-3 人（机库 14×7）
- **引擎**：corvette **2 台**（1 heater + 1 burst）；fighter1 **1 台**
- **速度**：标准；战斗机可跳往辛迪加 6 个方位或返回机库
- **乘员与用途**：**辛迪加星怒级战列巡洋舰（SBC Starfury）的舰载机群**——1 艘母舰 + 3 架战斗机 + 1 艘护卫舰，用于对空间站发起突袭（管理员/事件场景）。
- **获取/召唤方式**：`/proc/summon_battlecruiser()`——从幽灵候选池招募船员，将 `battlecruiser_starfury.dmm` 加载到深空，自毁核弹密码随机生成；飞机库 `starfury_fighter`（5×7）与 `starfury_corvette`（14×7）端口开局装载对应机种；控制台 `computer/shuttle/starfury/fighter|corvette` + 导航电脑 `shuttle_docker/syndicate/fighter|corvette`（ACCESS_SYNDICATE）。
- **型号清单（4）**：fighter_one（SBC Starfury Fighter 1，左舷）、fighter_two（2，中央）、fighter_three（3，右舷）、corvette（SBC Starfury Corvette）

---

## 四、NOVA 追加内容

源码：`modular_nova/modules/advanced_shuttles/`（code 716 行 + icons + sound）+ `modular_nova/master_files/code/modules/shuttle/`（门锁/音效）

### 4.1 NTV Relay 新式到达穿梭机（Arrivals Interlink）

- **移动端口**：`/obj/docking_port/mobile/arrivals_nova`，`shuttle_id = "arrivals_shuttle"`
- **速度**：`callTime = 9 秒`、`ignitionTime = 5 秒`、`rechargeTime = 9 秒`（全站最快）
- **机制**：到达后自动计时等待（`wait_time`，来自配置文件 `arrivals_wait`），到点自动飞回中继站（`arrivals_shuttle` ↔ `arrivals_stationary`）；检测到活人则**取消返航**（并播报）；控制台 `computer/shuttle/arrivals`（橙色复古风）+ 召回终端 `arrivals/recall`（"Use this if your friends left you behind" 被丢下的人专用）；状态码 `RTN`（Returning）。
- **模板**：`/datum/map_template/shuttle/arrivals_nova`（`_maps/shuttles/nova/arrivals_nova.dmm`）

### 4.2 NOVA 系列穿梭机地图模板

| 模板 | 地图 | 名称 | 说明 |
|---|---|---|---|
| `emergency/default` | `emergency_nova.dmm` | Standard Emergency Shuttle | NOVA 标准紧急穿梭机，**容量 60**，5 引擎（4 propulsion + 1 heater） |
| `emergency/outpost` | `emergency_outpost.dmm` | 前哨紧急船 | 额外型号 |
| `cargo/nova` | `cargo_nova.dmm` | Supply Shuttle (Cargo) | 3 引擎 |
| `cargo/nova/delta` | `cargo_nova_delta.dmm` | Supply Shuttle (Delta) | 因 Delta 码头差 1 格的适配版 |
| `cargo/nova/ouroboros` | `cargo_ouroboros.dmm` | Supply Shuttle (Ouroboros) | — |
| `ferry/nova` | `ferry_nova.dmm` | NAV Monarch (Ferry) | NOVA 班船 |
| `labour/nova` | `labour_nova.dmm` | NMC Drudge (Labour) | 劳改船，`laborcamp_home` 开局默认 |
| `mining/nova` | `mining_nova.dmm` | NMC Phoenix (Mining) | 3 引擎，`mining_home` 开局默认 |
| `mining/nova/large` | `mining_nova_large.dmm` | NMC Manticore (Mining) | 大型矿船 |
| `mining_common/nova` | `mining_common_nova.dmm` | NMC Chimera (Mining) | lavaland 公共矿船 |
| `whiteship/blueshift` | `whiteship_blueshift.dmm` | SFS Christian | 外星寄生的大护卫舰，清剿修复后可据为己有 |
| `whiteship/ouroboros` | `whiteship_ouroboros.dmm` | JN Chasse-Galerie | 移动咖啡馆，Jim Nortons 员工尸体 |
| — | `planetary_planetary_ferry.dmm` | 行星班船 | 行星场景 |
| — | `slaver_syndie.dmm` / `traitor_default.dmm` | 奴隶贩子船 / 叛徒船 | 事件用 |
| — | `random_ship_hc_police.dmm` | HC 警船 | 随机船 |
| — | `ruin_blackmarket_burst.dmm` / `ruin_interdyne_cargo.dmm` / `ruin_tarkon_driver.dmm` | 黑市/制药/卡车司机废墟船 | NOVA 废墟追加 |

### 4.3 SolFed ERT 穿梭机群（联邦飞船）

`modular_nova/master_files/code/datums/ert.dm` 定义 SolFed（太阳联邦）ERT 队伍，7 张专属飞船地图（`_maps/shuttles/nova/ert_solfed_*.dmm`，各 2 引擎）：

- `ert_solfed_official_shuttle`：官方审计队船（`/datum/ert/solfed`，teamsize 5，"审计空间站、写报告"）
- `ert_solfed_general_shuttle`：Espatier 突击队标准船（teamsize 6）
- `ert_solfed_assault_shuttle`：突击型（无医疗/工程舱室）
- `ert_solfed_armory_shuttle`：移动军械库
- `ert_solfed_medical_shuttle`：移动医院（corpsman 医疗队）
- `ert_solfed_engineer_shuttle`：移动工程舱
- `ert_solfed_fancy_shuttle`：豪华官方船
- 另有 `grand_espatier`（大反应）变体复用以上地图。

### 4.4 引擎音效与舱门闩锁（master_files）

- `modular_nova/master_files/code/modules/shuttle/shuttle.dm`：`bolt_all_doors()` / `unbolt_all_doors()`——点火起飞时闩死全船气闸（含同 ID 站内联动闸）、落地解锁；`play_engine_sound()`——起降音效（`sound/engine_startup.ogg`、`engine_landing.ogg`，范围 11 格，每格 -5 音量，受玩家音量偏好控制）。

### 4.5 高级飞船素材

- **壁柜**（`code/closet.dm`）：`/obj/structure/closet/shuttle` 系列——emergency（氧气+防毒面具）、medical（急救包+健康分析仪+注射器）、mining（氧气+太空服）、white；壁挂柜 `closet/generic/wall`、`emcloset/wall`、`firecloset/wall`、`secure_closet/wall` + 壁柜框（wallframe）可重建。
- **地板/墙体**（`code/turfs.dm`）：钛合金 `shuttle_wall` 系列（自动空间底衬），ferry / evac / arrivals / cargo / mining / mining_large / mining_labor / exploration 全套风格地板（含 airless 真空版）。
- **装饰**（`code/decals.dm`）：G250 转移船标志、可穿越力场（forcefield）、exploration 地板贴花（medbay/cargostore/bridge/weapons 等）。
- **控制台皮肤**（`code/computer.dm`）：`pod/advanced`、`emergency_shuttle/advanced`、`crew/shuttle`、`security/shuttle`、`ferry/shuttle` 复古终端外观。

---

## 五、TianGuan 定制内容

源码：`modular_tianguan/modules/emergency_shuttle_cc/code/emergency_shuttle_cc.dm`（28 行）

- **天官 CentCom 停靠适配**：天官分支重写了紧急穿梭机在 `emergency_away`（CentCom）的停靠逻辑——`tianguan_get_cc_emergency_away_dock()` 会在所有固定端口中寻找符合"`/area/space` + 朝向 EAST + **50×50（dwidth 25）**"条件的专用"恢复翼码头"（Recovery Wing Dock），命中则停靠到天官定制码头而非默认端口；找不到时回退原逻辑。这保证天官空间站的巨量逃生人员在 CentCom 有专属卸货区。

---

## 六、附录：各穿梭机引擎/容量速查表

> 引擎数量为 .dmm 地图实测（含 heater 加热器）；容量为源码 `occupancy_limit` 或地图尺度。

| 穿梭机 | 型号（示例） | 引擎（台） | 容量 | 速度/耗时 | 权限/获取 |
|---|---|---|---|---|---|
| Emergency 紧急 | NOVA 标准型 | 5（4 propulsion+1 heater） | 60 | 呼叫 10min×警报×引擎系数 | 通讯台呼叫/货仓购买（船长） |
| Emergency 紧急 | Box 标准型 | 2 | 45 | 同上 | 同上 |
| Emergency 紧急 | Cere 巨舰 | 多 | 110 | 同上 | 20 份货价 |
| Cargo 货运 | Box | 4 | ~10 箱 | 60 秒/程 | 货仓下单自动往返 |
| Cargo 货运 | NOVA Supply | 3 | — | 60 秒/程 | 同上 |
| Mining 采矿 | NOVA Phoenix | 3 | — | 标准 | 采矿台（禁放逐者） |
| Arrival 到达 | Box | 5 | 椅子数 | 全自动 | 自动（新人出生） |
| Ferry 班船 | base | 2 | — | 标准 | ACCESS_CENT_GENERAL |
| Assault Pod 突击舱 | default | 0（外部） | 1 | 即时 | 辛迪加指示器 |
| Escape Pod 逃生舱 | default | 1（burst） | 2 格 | 红警发射 | 红警/emag |
| ERT | bounty | 2 | 3-4 人 | 标准 | 管理员/ERT 事件 |
| ERT（NOVA） | solfed 系列 | 2/船 | 5-6 人 | 标准 | SolFed ERT 事件 |
| Infiltrator 渗透艇 | basic | 12 | 4-6 人 | 标准（20min 限制） | 核战开局自动 |
| Pirate 海盗船 | default | 4 | — | 标准 | 海盗事件/管理员 |
| Hunter 猎船 | 5 型号 | 1-2 | 1-3 人 | 标准 | 随机遭遇 |
| Labour 劳改 | Box | 2 | ~10 人 | 标准 | 警长/囚犯单向台 |
| Aux Base 辅助基地 | default/small | 视地图 | 基地模块 | 标准 | 管理员 |
| Whiteship 白船 | Box | 6 | 视型号 | 标准+自定义降落 | 深空随机 1 艘 |
| Ruin 废墟 | cyborg_mothership | 5 | 小型 | 标准 | 地图废墟生成 |
| Ruin 废墟 | syndicate_dropship | 4 | 1-4 人 | 标准 | 地图废墟生成 |
| Starfury 星怒 | corvette | 2 | 2-3 人 | 标准 | SBC 母舰召唤 |

---

*文档生成自 TianGuan13 源码（NovaSector 分支），全部 16 种穿梭机类型 + NOVA 追加均已收录。*
