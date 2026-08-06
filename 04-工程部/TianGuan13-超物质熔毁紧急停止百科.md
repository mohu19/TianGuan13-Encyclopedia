# TianGuan13 超物质熔毁紧急停止百科

> 模块 ID: `DELAM_SCRAM`。为超物质引擎添加"紧急停止"（Suppression System）——在回合开始前 30 分钟内可操作，让工程部在搞砸时不必等管理员删水晶。
> 来源: [Skyrat PR #22145](https://github.com/Skyrat-SS13/Skyrat-tg/pull/22145)，作者 LT3。
> 文件: `code/scram.dm`（499 行）+ `code/admin_scram.dm`（64 行）+ `code/delam.dm`（62 行）+ `code/supermatter_replacement.dm`（13 行）+ `README.md`（43 行）+ `icons/scram.dmi`。

## 1.1 模块总览与宏定义

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

## 1.2 抑制系统机器（delam_scram）

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

## 1.3 紧急停止按钮（delam_scram button）

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

## 1.4 管理指令（Admin Verbs）

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

## 1.5 熔毁通知与自动干预（delam.dm）

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

## 1.6 配套物件与联动

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


---

## 附录 · 代码索引

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

