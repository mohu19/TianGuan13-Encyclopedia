# TianGuan13 冬眠舱与污染系统百科

> **项目**: TianGuan13 (Nova Sector → /tg/station)
> **代码**: `modular_nova/modules/cryosleep/`（9 个 .dm，834 行）+ `modular_nova/modules/pollution/`（15 个 .dm，886 行）
> **范围**: 低温休眠系统（冬眠舱/控制台/脱出流程/AI 休眠/职业槽释放）+ 大气污染系统（污染物/扩散/净化/香水/蜡烛/篝火）
>
> **关联文档**: 冬眠舱与幽灵咖啡馆联动见《反派系统百科》；太空睡眠症（Space Sleep Disorder）为原版精神疾病；香水/蜡烛产出渠道见《供应系统百科》。

---

## 目录

- [第一卷 · 冬眠舱系统（Cryosleep）](#第一卷--冬眠舱系统cryosleep)
  - [1.1 模块总览与配置](#11-模块总览与配置)
  - [1.2 冬眠舱本体（Cryopod）](#12-冬眠舱本体cryopod)
  - [1.3 冬眠舱变体](#13-冬眠舱变体)
  - [1.4 进出舱流程与判定](#14-进出舱流程与判定)
  - [1.5 脱出执行（Despawn）全流程](#15-脱出执行despawn全流程)
  - [1.6 控制台（Cryogenic Oversight Console）](#16-控制台cryogenic-oversight-console)
  - [1.7 广播公告系统（AAS）](#17-广播公告系统aas)
  - [1.8 AI 休眠与职业槽管理](#18-ai-休眠与职业槽管理)
  - [1.9 辅助修改（管理/心情/部门频道）](#19-辅助修改管理心情部门频道)
- [第二卷 · 大气污染系统（Pollution）](#第二卷--大气污染系统pollution)
  - [2.1 子系统 SSpollution](#21-子系统-sspollution)
  - [2.2 污染物框架（Pollutant Datum）](#22-污染物框架pollutant-datum)
  - [2.3 污染数据（Pollution Datum）](#23-污染数据pollution-datum)
  - [2.4 污染扩散与消散机制](#24-污染扩散与消散机制)
  - [2.5 污染物全表](#25-污染物全表)
  - [2.6 污染发射器（Emitters）](#26-污染发射器emitters)
  - [2.7 污染接口（pollute_turf）](#27-污染接口pollute_turf)
  - [2.8 净化设备（空气清新剂/污染洗涤器）](#28-净化设备空气清新剂污染洗涤器)
  - [2.9 篝火（Bonfire）](#29-篝火bonfire)
  - [2.10 香水全录（Perfumes）](#210-香水全录perfumes)
  - [2.11 香薰蜡烛与包装](#211-香薰蜡烛与包装)
  - [2.12 管理指令](#212-管理指令)
  - [2.13 视觉效果（Overlay）](#213-视觉效果overlay)
- [附录 · 代码路径索引](#附录--代码路径索引)

---

# 第一卷 · 冬眠舱系统（Cryosleep）

> 模块 ID: `CRYOSLEEP`。为想以 IC（游戏内）方式退出回合的玩家提供一台绿色睡眠舱式机器。
> 移植/重构: Azarak。

## 1.1 模块总览与配置

**代码**: `modular_nova/modules/cryosleep/readme.md`、`modular_nova/modules/cryosleep/code/config.dm`

| 配置项 | 类型 | 默认值 | 说明 |
|---|---|---|---|
| `cryo_min_ssd_time` | number | **15**（分钟） | 玩家被他人放入冬眠舱前必须离线（SSD）的最短时间（注释建议以 `cryo_min_ssd_time` 配置替换硬编码的 30 分钟） |
| `cryo_policy` | string | `"You may rejoin as the same role, or as an assistant with the cryo'd character."` | 玩家自行进入冬眠舱前显示的阵营政策文本（Cryo Policy） |

- **全局列表**: `GLOB.cryopod_computers`（所有冬眠舱控制台）、`GLOB.ghost_records`（幽灵角色记录）、`GLOB.valid_cryopods`（所有非 quiet 冬眠舱，供 VV 动作 "Send to Cryogenic Storage" 使用）。
- 图标资源: `modular_nova/modules/cryosleep/icons/cryogenics.dmi`。
- 说明文案: 冬眠舱"适用于生化人与类人生物，是受太空睡眠症（Space Sleep Disorder）困扰的人员的安全休息处"。

## 1.2 冬眠舱本体（Cryopod）

**代码**: `modular_nova/modules/cryosleep/code/cryopod.dm`（700 行，模块核心）

`/obj/machinery/cryopod` — 低温冷冻舱（cryogenic freezer），本质是一个"脱出器"（despawner），概念大量借鉴 sleeper。

### 基础属性

| 属性 | 值 |
|---|---|
| `name` | "cryogenic freezer"（低温冷冻舱） |
| `icon_state` / `base_icon_state` | `cryopod-open` / `cryopod` |
| `use_power` / `density` / `anchored` | FALSE / TRUE / TRUE |
| `state_open` | TRUE（初始开启） |
| `interaction_flags_mouse_drop` | `FORBID_TELEKINESIS_REACH`（禁止念力远程拖入） |

### 关键变量（数值精确）

| 变量 | 默认值 | 说明 |
|---|---|---|
| `allow_timer_override` | FALSE | 是否允许管理员开启"跳过 SSD 时间检查"（VV 修改） |
| `ssd_time` | **30 MINUTES**（30 分钟） | 玩家可被他人放入冬眠舱所需的最短离线时间（注释：可被 `cryo_min_ssd_time` 配置替换） |
| `time_till_despawn` | **30 SECONDS**（30 秒） | 生物进入冬眠舱后直到被脱出（despawn）的等待时间；除非目标处于紧张症（catatonic）状态，否则不能把他人放入舱内 |
| `control_computer_weakref` | null | 关联控制台的弱引用 |
| `last_no_computer_message` | 冷却 5 MINUTES | 找不到控制台时的管理员消息冷却 |
| `quiet` | FALSE | 为 TRUE 时不播放脱出广播 |
| `tucked` | FALSE | 乘员是否已被盖好被子 |
| `despawn_to_ghostcafe` | null | 是否把乘员脱出到幽灵咖啡馆（Ghost Cafe） |
| `timerid` | null | 脱出倒计时 timer 的 ID（乘员离开舱时停止） |

### 初始化与生命周期

- `Initialize`: 非 quiet 的舱加入 `GLOB.valid_cryopods`；返回 `INITIALIZE_HINT_LATELOAD`（先等控制台 GLOB 填充完毕）。
- `post_machine_initialize`: `update_icon()` + `find_control_computer()`。
- `Destroy`: 移出 `valid_cryopods`，清空控制台弱引用。
- `find_control_computer(urgent)`: 遍历 `GLOB.cryopod_computers`，找**同一区域（area）**的控制台；找不到且 urgent 时每 5 分钟 log/message_admins 一次。

### 交互与操作

| 操作 | 说明 |
|---|---|
| `close_machine(target)` | 关闭舱门：要求 `isliving`、`state_open` 且面板关闭；移入乘员 → `set_occupant` → 存活者收到提示；人类乘员保存个人持久化数据（`save_individual_persistence`）；启动 `time_till_despawn` 倒计时；注册 `COMSIG_MACHINERY_SET_OCCUPANT` 与乘员的 `COMSIG_MOB_GHOSTIZED` 信号 |
| `open_machine()` | 开门：重置 `density=TRUE`、`tucked=FALSE`、名字还原 |
| `container_resist_act` / `relaymove` | 乘员挣脱：显示提示并 `open_machine()` |
| `item_interaction`（床单） | 用床单（bedsheet）给乘员盖被子：消耗床单、给使用者添加 `tucked_in` 心情事件（+3 心情，2 分钟）、`tucked=TRUE`；已盖过则警告 |
| `blob_act` | 直接 return——**冬眠舱不可被 blob 摧毁** |
| `mouse_drop_receive(target, user)` | 拖放进入流程（见 1.4） |

### 盖被子心情（mood.dm）

**代码**: `modular_nova/modules/cryosleep/code/mood.dm`

`/datum/mood_event/tucked_in` — "帮某人盖好被子睡个好觉让我感觉更好！"
- `mood_change = 3`（+3 心情）
- `timeout = 2 MINUTES`
- 触发后描述动态填入被盖被子者名字。

## 1.3 冬眠舱变体

| 变体 | 代码位置 | 差异 |
|---|---|---|
| `/obj/machinery/cryopod/quiet` | cryopod.dm | `quiet = TRUE`（不广播），仍计入 `valid_cryopods` 排除列表（即不被 VV 发送目标） |
| `/obj/machinery/cryopod/despawn_to_ghostcafe` | cryopod.dm | "Ghost Cafe Pod"：`icon_state` 为 `ghostcafepod(-open)`，`despawn_to_ghostcafe = TRUE`，**`time_till_despawn = 4 SECONDS`**（4 秒即脱出）；描述附加"这台会将乘员送往幽灵咖啡馆" |
| `/obj/machinery/cryopod/prison` | cryopod.dm | 监狱壁挂式：`density = FALSE`（`set_density` 强制永为 FALSE）、图标复用 ghostcafepod、进入时 flick `prisonpod-open`；配 `MAPPING_DIRECTIONAL_HELPERS(..., 18)` |

## 1.4 进出舱流程与判定

**代码**: `modular_nova/modules/cryosleep/code/cryopod.dm`（`mouse_drop_receive`）

拖放（mouse drop）进入冬眠舱的完整判定链：

1. **前置检查**: 目标必须是 mob（非动物 `isanimal`）、使用者站在 turf 上、目标未被束缚（`buckled`）。
2. **舱内已有人** → 提示"舱内已有乘员"。
3. **目标死亡**（`stat == DEAD`）→ 拒绝，提示"你不能把尸体放进冬眠舱"。
4. **管理员覆盖**（`allow_timer_override`）: tgui 确认后直接放入（管理员日志/消息 + `ADMIN_JMP`）。
5. **他人放入判定**（`target.key && user != target`）:
   - 目标必须有**大脑**器官；
   - 目标须"空"（无 mind 或 `ssd_indicator` 为真，即 SSD/AI 托管）；
   - `target.lastclienttime + ssd_time >= world.time` → **离线不足 30 分钟**：提示剩余分钟数并拒绝（log/message_admins 记录尝试）；
   - 离线足够 → tgui 确认；若目标职业 `req_admin_notify` 为真，先弹 AHELP 提示：*"Please adminhelp before leaving the round, even if there are no administrators online!"*（`AHELP_FIRST_MESSAGE`）；
   - 生化人（cyborg）目标 → 提示"你不能把生化人放进冬眠舱"；无大脑目标 → 提示"他们看起来没有大脑"。
6. **自行进入**（`target == user`）:
   - 提示文本 "Enter cryosleep?" +（非 ghostcafe/非 quiet 时）追加 `cryo_policy` 配置文本；
   - 确认后若职业 `req_admin_notify` → AHELP 提示；
   - 若为反派（antag）→ 额外弹窗提示其反派身份与 AHELP 要求；
   - **束缚他人**（`buckled_mobs` 非空）→ 拒绝。
7. **最终校验**（重跑全部检查防作弊）→ 广播消息（`span_infoplain`）→ 目标收到 `time_till_despawn/10` 秒警告 → log/message_admins → `close_machine(target)` → 舱名变为 `"[name] ([target.name])"`。

### 脱出计时与中途离开

- `on_set_occupant`（乘员更换信号）→ `stop_cryo_timer()`：删除 timerid、注销信号。
- `on_occupant_ghosted`（乘员提前变鬼）→ 立即停止计时并**立刻** `initiate_despawn_occupant()`。

## 1.5 脱出执行（Despawn）全流程

**代码**: `modular_nova/modules/cryosleep/code/cryopod.dm`

`initiate_despawn_occupant()`（由 addtimer 或变鬼时调用）：
- 停止计时器；无乘员则返回；乘员死亡则 `open_machine()`（不开舱脱出）；无 client 时强制寻找控制台（urgent）。

`despawn_occupant()` — **不可撤销**，按序执行：

1. **释放职业槽**: `SSjob.FreeRole(occupant_rank)`。
2. **圣职继承者清理**: 若乘员在 `GLOB.holy_successors` 中则移除（`list_holy_successors()` 检查）。
3. **反派/目标清理**（`handle_objectives()`）:
   - 叛变（mutiny）目标 → 删除该目标，通知队伍成员；
   - 合同（contract）目标 → 合同状态置 `CONTRACT_STATUS_ABORTED`，自动**重掷新合同**（通知"Contract target out of reach. Contract rerolled."）；
   - 普通目标 → `find_target()` 重找目标，找不到则从反派目标列表中移除；更新目标文本（"Time for Plan A/B/C/D/X/Y/Z. Objectives updated!"），团队共享更新。
4. **高牧师释放**: `mind.holy_role == HOLY_ROLE_HIGHPRIEST` 或 `GLOB.current_highpriest` 指向该 mob → `reset_religion()`。
5. **数据核心清理**: 遍历 `GLOB.manifest.general` 删除匹配名字+职级（或 off-duty 助理 trim）的记录；否则在 `GLOB.ghost_records` 中删除；生化人单独处理 `announce_rank = "[designation] Cyborg"`。
6. **记录与广播**: 控制台 `frozen_crew` 追加 `{name, job}`；非 quiet 时 `control_computer.announce("CRYO_LEAVE", ...)`。
7. **物品转移**: 非 `TRAIT_FREE_GHOST`（幽灵咖啡馆角色不存物品）时遍历乘员物品，跳过 `TRAIT_NODROP`、`ABSTRACT|DROPDEL` 物品旗标、`HOLOGRAM_1`、`NO_CRYO_FREEZE`（Nova 物品旗标）、已删除物品；生化人的 MMI 不转移；有控制台时模块电脑（modular computer）的 messenger 程序置 `invisible = TRUE` 后物品移入控制台并 `freeze_item()`；无控制台则掉落在舱位 turf。
8. **生化人烧杯处理**: 生化人 qdel 时会溅出烧杯试剂——提前 `clear_reagents()` 防止。
9. **玩家列表**: `GLOB.joined_player_list -= occupant_ckey`。
10. **变鬼/幽灵咖啡馆**: 提前变鬼则从 `GLOB.dead_player_list` 找幽灵；否则 `ghostize()`；若 `despawn_to_ghostcafe` 为真，用对应 spawner（生化人→`robot/ghostcafe`，人类→`human/ghostcafe`）`create_from_ghost(ghost, apply_prefs = TRUE)`。
11. **销毁**: `QDEL_NULL(occupant)` → `open_machine()` → 名字还原。

`freeze_item(item)` / `unfreeze_item(item)`: 冻结物品加入控制台 `frozen_items` 并注册 `COMSIG_QDELETING` 信号（防止硬删除）；取回时注销信号并移除。

### 幽灵角色唤醒广播（ghost_role 挂钩）

`/obj/effect/mob_spawn/ghost_role` 新增 `computer_area` 变量；`create()` 后：
- 新角色加入 `GLOB.ghost_records`（name + 备用职名 `get_spawner_outfit_name()`，即 outfit.name）；
- 找到本地控制台后：若职业 `head_announce` 非空 → 发 `newhead` AAS 公告（首领上任）；否则 `control_computer.announce("CRYO_JOIN", ...)`（"Awakening" 唤醒广播）。
- 示例: `/obj/effect/mob_spawn/ghost_role/human/lavaland_syndicate` 的 `computer_area = /area/ruin/syndicate_lava_base/dormitories`（拉瓦兰辛迪加基地宿舍区）。

## 1.6 控制台（Cryogenic Oversight Console）

**代码**: `modular_nova/modules/cryosleep/code/cryopod.dm`、`cryo_console_return.dm`

`/obj/machinery/computer/cryopod` — "cryogenic oversight console"（低温存储监管控制台），船员与低温存储监管系统之间的界面。

| 属性 | 值 |
|---|---|
| `icon_state` | `cellconsole_1`（断电/损坏时 `cellconsole`） |
| `use_power` / `density` | FALSE / FALSE |
| `interaction_flags_machine` | `INTERACT_MACHINE_OFFLINE`（离线可交互） |
| `req_one_access` | `ACCESS_COMMAND`（指挥）**或** `ACCESS_ARMORY`（军械库）——部门主管或典狱长可取回本部门成员冬眠时携带的物品 |
| `verb_say / ask / exclaim` | "coldly states" / "queries" / "alarms"（冷漠地陈述/询问/警报） |
| `connectable` | FALSE（不可接入线缆） |
| `announcement_channel` | `RADIO_CHANNEL_COMMON`（通用频道） |
| `frozen_crew` | 进入冬眠人员及其重要物品的日志列表 |
| `frozen_items` | 控制台面板中当前存储的物品 |

- 初始化: 加入 `GLOB.cryopod_computers`；Destroy 时移除。
- **TGUI 界面**（`CryopodConsole`）: `ui_data` 输出 `frozen_crew`、物品引用列表（`item_ref_list` + `item_ref_name`）、`item_retrieval_allowed`（`allowed(user)` 访问判定）。
- **取回物品**（`ui_act` "item_get"）: 通过 REF 引用定位物品，`forceMove` 到控制台位置 → 放入使用者手中 → `unfreeze_item()` → 可见消息 + `message_admins`（含 `ADMIN_COORDJMP`）；非法 REF/动作直接 `CRASH`。
- **Nova 扩展**（`return_items_to_console(items_to_send)`，挂在 `/mob/living/carbon/human`）: 把指定类型列表的物品送回**空间站上**（`is_station_level`）的控制台——遍历 `get_contents()`，匹配 `is_type_in_list` 的物品转移并加入 `frozen_items`（用于回航/接取场景返还装备）。

## 1.7 广播公告系统（AAS）

**代码**: `modular_nova/modules/cryosleep/code/cryopod.dm`

`/obj/machinery/computer/cryopod/proc/announce(message_type, user, rank, occupant_job)`:

| 类型 | 行为 |
|---|---|
| `CRYO_JOIN`（唤醒） | 在 `announcement_channel`（默认通用频道）发 `cryopod_announcement` 公告："Awakening" |
| `CRYO_LEAVE`（移除） | 遍历乘员职业的 `departments_list`：对**指挥部门**或**部门主管职位**，向该部门默认无线电频道发公告（"Removing"，主管职位标记 `is_department_head = TRUE`）；若已在控制台频道广播过则去重；否则回落到通用频道 |

`/datum/aas_config_entry/cryopod_announcement`（AAS 配置条目）:
- name: "Departmental Alert: Cryogenic Sleeper Announcement"
- 文案: `"Awakening"` = "%PERSON, %RANK has woken up from cryo storage."；`"Removing"` = "%PERSON, %RANK has been moved to cryo storage."
- 变量: `PERSON`（姓名）、`RANK`（职级）。

## 1.8 AI 休眠与职业槽管理

**代码**: `modular_nova/modules/cryosleep/code/ai.dm`、`job.dm`、`jobs.dm`

### AI 低温休眠（ai_cryo）

`/mob/living/silicon/ai/verb/ai_cryo()` — 命令名 **"AI 低温休眠"**（中文），分类 "AI Commands"，描述 "Puts the current AI personality into cryogenic stasis, freeing the space for another."（将当前 AI 人格送入低温休眠，为下一个 AI 腾出位置）：
1. 失去行动能力（incapacitated）时不可用；
2. 确认后 `ghostize(FALSE)`；
3. `minor_announce`："Station AI has disconnected from system networks and moved to remote storage. Preparing for new AI personality upload."；
4. 生成 `/obj/structure/ai_core/latejoin_inactive`（供新 AI 加入的核心）；
5. 若职业为 `JOB_AI` → `SSjob.FreeRole(JOB_AI)`；清空 `special_roles`；`qdel(src)`。

### 职业槽释放/占用（job.dm）

| Proc | 行为 |
|---|---|
| `SSjob.FreeRole(rank)` | 释放职业槽：`current_positions = max(0, current_positions - 1)`；rank 空或映射失败时 `stack_trace` |
| `SSjob.OccupyRole(rank)` | 重新占用此前释放的职业槽（"打卡回来"）：无空位（`current_positions >= total_positions`）时返回 FALSE；成功 `current_positions + 1` 并返回 TRUE |

### 部门默认无线电频道（jobs.dm）

为 `/datum/job_department` 增加 `default_radio_channel`（上游尚未实现），用于 1.7 的部门公告：

| 部门 | 频道 |
|---|---|
| command | `RADIO_CHANNEL_COMMAND`（指挥） |
| security | `RADIO_CHANNEL_SECURITY`（安保） |
| engineering | `RADIO_CHANNEL_ENGINEERING`（工程） |
| medical | `RADIO_CHANNEL_MEDICAL`（医疗） |
| science | `RADIO_CHANNEL_SCIENCE`（科研） |
| cargo | `RADIO_CHANNEL_SUPPLY`（补给） |
| service | `RADIO_CHANNEL_SERVICE`（服务） |

## 1.9 辅助修改（管理/心情/部门频道）

**代码**: `modular_nova/modules/cryosleep/code/admin.dm`、`mind.dm`

### 发送到冬眠舱（send_to_cryo）

`/mob/proc/send_to_cryo(with_paper = FALSE)` — 把玩家送入非 quiet 冬眠舱（管理员/VV 用）：
1. 特效: `playsound`（Repulse.ogg，100 音量）+ `do_sparks(10, TRUE, ...)`（量子火花 `spark_spread/quantum`）；
2. `with_paper = TRUE` 时在脚下生成通知纸：名称 "Notification - [name]"，正文以 **Nanotrasen 反突发睡眠障碍局**（*Nanotrasen Anti-Sudden Sleep Disorder Agency*）名义致歉，说明"因暂无法详述的原因被送回低温存储"；
3. 遍历 `GLOB.valid_cryopods`：找**空闲、开启、面板关闭**的舱，解除目标自身束缚与被束缚者，`close_machine(src)` 放入。

### mind 扩展（mind.dm）

`/datum/mind` 的 `objectives` 列表声明（上游 `Cut()` 等操作依赖此声明，防止硬删除）。

---

# 第二卷 · 大气污染系统（Pollution）

> 模块 ID: `POLLUTION`。为烟雾、灰尘、气味等大气污染物提供统一的产生、扩散、感知与净化框架。
> 关键宏定义位于 `code/__DEFINES/~nova_defines/pollution.dm`（41 行）。

## 2.1 子系统 SSpollution

**代码**: `modular_nova/modules/pollution/code/pollution_subsystem.dm` + `code/__DEFINES/~nova_defines/pollution.dm`

| 参数 | 值 |
|---|---|
| `name` | "Pollution" |
| `dependents` | `/datum/controller/subsystem/atoms`（发射器需先知道单例） |
| `runlevels` | `RUNLEVEL_GAME \| RUNLEVEL_POSTGAME`（游戏与游戏后） |
| `wait` | **2 SECONDS**（每 2 秒处理一批） |
| 列表 | `active_pollution`（活跃污染）、`all_polution`（世界全部污染，注意拼写）、`current_run`（本批处理）、`processed_this_run`（本次已处理）、`singletons`（污染物类型→单例实例） |

### 任务轮换（pollution_task）

| 任务 | 值 | 行为 |
|---|---|---|
| `POLLUTION_TASK_PROCESS` | 1 | 遍历活跃污染执行 `process_cell()` 扩散；每轮 `dissapation_ticker++` |
| `POLLUTION_TASK_DISSIPATE` | 2 | 每 **20 tick**（`TICKS_TO_DISSIPATE`，即 20×2=40 秒）切换到消散任务：遍历全部污染 `scrub_amount(POLLUTION_HEIGHT_DIVISOR, FALSE, TRUE)` 消散 10 单位（行星大气下 ×4） |

- `Initialize`: 遍历 `subtypesof(/datum/pollutant)`，**跳过无 `name` 的抽象类型**，实例化所有具名污染物为单例存入 `singletons`。
- `stat_entry`: 显示 `|AT:[active_pollution.len]|P:[all_polution.len]`。
- MC tick 检查：每批处理受 `MC_TICK_CHECK` 保护，超时让出。

### 关键宏值（精确）

| 宏 | 值 | 说明 |
|---|---|---|
| `POLLUTION_HEIGHT_DIVISOR` | **10** | 高度计算除数；也是消散任务每 tick 的消散量 |
| `TICKS_TO_DISSIPATE` | **20** | 处理任务运行多少 tick 后切到消散任务 |
| `POLLUTION_DISSIPATION_PLANETARY_MULTIPLIER` | **4** | 行星大气（planetary_atmos）下消散速度 ×4 |
| `POLLUTANT_SMELL_THRESHOLD` | **3.5** | 可嗅闻的最低气味强度（smell_intensity × 量） |
| `POLLUTANT_SMELL_NORMAL` | **20** | 正常气味的强度上限 |
| `POLLUTANT_SMELL_STRONG` | **40** | 强烈气味的强度上限 |
| `SMELL_COOLDOWN` | **1 MINUTES** | 嗅闻冷却 |
| `POLLUTANT_APPEARANCE_THICKNESS_THRESHOLD` | **30** | 可见污染的最小总厚度 |
| `THICKNESS_ALPHA_COEFFICIENT` | **0.0025** | 厚度→透明度（alpha）系数 |
| `POLLUTION_ACTIVE_EMITTER_CAP` | **200** | 长期活跃发射器的污染量上限 |
| `POLLUTION_PASSIVE_EMITTER_CAP` | **70** | 被动发射器的污染量上限 |

### 污染物旗标（bitflags）

| 旗标 | 位 | 含义 |
|---|---|---|
| `POLLUTANT_APPEARANCE` | 1<<0 | 有可见外观 |
| `POLLUTANT_SMELL` | 1<<1 | 可被嗅闻 |
| `POLLUTANT_TOUCH_ACT` | 1<<2 | 接触未防护者时调用 `touch_act()` |
| `POLLUTANT_BREATHE_ACT` | 1<<3 | 吸入时调用 `breathe_act()` |

### 气味描述符（SCENT_DESC）

| 宏 | 值 | 中文 |
|---|---|---|
| `SCENT_DESC_ODOR` | "odour" | 臭味（警告色消息） |
| `SCENT_DESC_SMELL` | "smell" | 气味 |
| `SCENT_DESC_FRAGRANCE` | "fragrance" | 香味 |

### 污染注册宏

`REGISTER_POLLUTION` / `UNREGISTER_POLLUTION`（all_polution）、`SET_ACTIVE_POLLUTION` / `SET_UNACTIVE_POLLUTION`（active_pollution）、`SET_PROCESSED_THIS_RUN`、`REMOVE_POLLUTION_CURRENTRUN`。

## 2.2 污染物框架（Pollutant Datum）

**代码**: `modular_nova/modules/pollution/code/pollutant_datum.dm`

`/datum/pollutant` — 每类污染物的定义（单例）：

| 变量 | 说明 |
|---|---|
| `name` | 污染物名；**null 视为抽象类型，不会初始化为单例** |
| `pollutant_flags` | 行为旗标（见 2.1） |
| `color` | 外观颜色（可空） |
| `alpha` | 期望透明度（默认 255） |
| `thickness` | "厚度"系数：越大越快达到期望 alpha，且混色时更占优势（默认 1） |
| `smell_intensity` | 每单位的气味强度（可嗅闻时必须填写） |
| `descriptor` | 气味描述词（odour/smell/fragrance） |
| `scent` | 气味内容文本 |

行为虚方法（子类覆写）:
- `touch_act(mob/living/carbon/victim, amount)` — 污染物接触未防护碳基生物时；
- `breathe_act(mob/living/carbon/victim, amount)` — 碳基生物吸入时。

## 2.3 污染数据（Pollution Datum）

**代码**: `modular_nova/modules/pollution/code/pollution.dm`（246 行）

`/datum/pollution` — 单个 turf 上的污染堆：

| 变量 | 说明 |
|---|---|
| `my_turf` | 所在 turf（`turf/open`） |
| `pollutants` | 关联列表：污染物类型 → 数量 |
| `total_amount` | 总量 |
| `height` | 污染高度（用于"沙堆"扩散效果） |
| `managed_overlay` | 管理的可见 overlay（`/obj/effect/abstract/pollution`） |

### 核心 Proc

| Proc | 行为 |
|---|---|
| `New(turf)` | 绑定 turf 并写回 `my_turf.pollution`，注册到子系统 |
| `touch_act(victim)` | 遍历污染物，对带 `POLLUTANT_TOUCH_ACT` 旗标者调用 `pollutant.touch_act(victim, amount)`；`victim.can_inject()` 失败则跳过（防护判定） |
| `breathe_act(victim)` | 同上，旗标为 `POLLUTANT_BREATHE_ACT` |
| `smell_act(sniffer)` | 嗅闻（见下） |
| `scrub_amount(amount, update_active, planetary_multiplier)` | 净化：`amount >= total_amount` 时直接 `qdel`；行星大气下 `amount ×= 4`；按比例从各污染物扣除；更新高度/overlay；可选刷新相邻 turf |
| `add_pollutant(type, amount)` | 增加单种污染物：累加、`total_amount += amount`、更新高度/overlay、`SET_ACTIVE_POLLUTION` |
| `add_pollutant_list(list)` | 批量增加（同上） |
| `update_height` / `calculate_height` | `height = ceil(amount / POLLUTION_HEIGHT_DIVISOR)`（每 10 单位 1 层） |
| `process_cell()` | 扩散处理（见 2.4） |
| `can_share_with(shareto)` | 可共享判定：对方无污染 → TRUE；对方高度 +1 < 自身高度 → TRUE（向低处流）；否则 FALSE |
| `assert_pollution(turf)` | 目标 turf 无污染时新建 |
| `handle_overlay()` / `get_overlay(list, total)` | 外观层管理（见 2.13） |
| `Destroy()` | 移除 overlay 与注册、清空 `my_turf.pollution` |

### 嗅闻判定（smell_act）

- 有 `TRAIT_ANOSMIA`（嗅觉缺失）特质者无法嗅闻。
- 对每个带 `POLLUTANT_SMELL` 旗标的污染物计算 `smell_power = smell_intensity × 数量`；低于 **3.5**（`POLLUTANT_SMELL_THRESHOLD`）跳过；取**最强气味**为 dominant。
- 三档强度文案（机械体 `MOB_ROBOTIC` 用传感器措辞）：
  - 0~20（NORMAL）: "The subtle [descriptor] of [scent] tickles your nose..."（微量）；
  - 20~40（STRONG）: "The [descriptor] of [scent] fills the air."（弥漫）；
  - 40+（无限）: "The unmistakable [descriptor] of [scent] bombards your nostrils."（浓烈）。
- `descriptor == SCENT_DESC_ODOR`（臭味）→ 警告色消息；其余 → 普通提示色。

## 2.4 污染扩散与消散机制

**代码**: `modular_nova/modules/pollution/code/pollution.dm` + `turf_open.dm`

### 扩散（process_cell）

1. `height <= 1` 的污染不扩散（`SET_UNACTIVE_POLLUTION`）。
2. 遍历 `my_turf.atmos_adjacent_turfs`（大气相邻 turf）：未处理过的，`can_share_with()` 判定为共享 turf 或潜在激活者。
3. 无可共享 turf → 转为非活跃。
4. 收集共享 turf（含自身）的所有污染物总量 → **均分**（`/= sharing_turfs.len`）。
5. 计算新高度与共享 overlay（`get_overlay`）。
6. 每个共享 turf（**太空 turf 除外——太空会"吞掉"污染**）：`assert_pollution` → 替换 pollutants/总量/高度/overlay → `SET_ACTIVE_POLLUTION`。
7. 潜在激活者若现在可与本 turf 共享 → 激活。

### 消散（scrub_amount + 子系统轮换）

- 每 20 个处理 tick（40 秒）执行一轮消散：每个污染堆减 `POLLUTION_HEIGHT_DIVISOR`（10）单位，行星大气下等效 **40** 单位。
- 消散同时调用 `update_adjacent_pollutants()`（turf 的 atmos 相邻关系变化时激活相关污染）。

### 太空与行星大气

- `/turf/open/space/pollute_turf` / `pollute_turf_list`：**直接 return——太空不产生污染**。
- 行星大气（`planetary_atmos`）：消散 ×4；篝火在行星大气/中央司令部（CentCom）层级**不产生**污染（见 2.9）。

## 2.5 污染物全表

**代码**: `modular_nova/modules/pollution/code/pollutants_generic.dm`（178 行）

> 分类列出全部 23 种具名污染物；数值精确。

### 烟雾类（/datum/pollutant/smoke）

| 污染物 | 名称 | 旗标 | smell_intensity | thickness | scent | 行为 |
|---|---|---|---|---|---|---|
| `smoke` | Smoke（烟雾） | 外观+气味+吸入 | 1 | — | "smoke" | 吸入 `amount > 50` 时 20% 概率咳嗽（`emote("cough")`） |
| `smoke/cannabis` | Cannabis（大麻烟） | 同上 | **2**（强于普通烟） | — | "cannabis" | 同上 |
| `smoke/vape` | Vape Cloud（电子烟云） | 同上 | 1 | **2** | "pleasant and soft vapour"（宜人柔和的蒸汽） | 同上 |

### 粉尘与化学品

| 污染物 | 名称 | 旗标 | thickness | color | scent | 行为 |
|---|---|---|---|---|---|---|
| `dust` | Dust（尘土） | 外观+吸入 | **2** | `#ffed9c`（米黄） | — | 吸入 `amount > 10` 时 40% 概率 `losebreath += 3` + 咳嗽 |
| `sulphur` | Sulphur（硫磺） | 气味 | — | — | "sulphur"，强度 **5**（极明显） | 火柴点燃产生 |
| `metallic_scent` | Metallic Scent（金属气味） | 气味 | — | — | "a metallic scent"，强度 1 | 溅血产生微量 |
| `bright_cosmos` | Cosmic Smoke（宇宙烟） | 气味 | — | — | "refreshing mint and inoffensive smoke"（清爽薄荷与无害烟雾），强度 1 | 特殊雪茄的"快速消散"烟雾 |
| `chemical_vapors` | Chemical Vapors（化学蒸汽） | 气味 | — | — | "chemicals"，强度 1 | 绿色粘液堆与药物化学反应产生 |
| `carbon_air_pollution` | Carbon Air Pollution（含碳空气污染） | 吸入 | — | — | — | 见下 |

**carbon_air_pollution 吸入效果**（危险火灾焚烧废物产生）：
- 目标**躺卧**（`LYING_DOWN`）时吸入量 ×0.35（约三分之一）；
- `amount <= 10` 无效果；
- 氧损伤 `adjust_oxy_loss(rand(5,10))` + 毒素损伤 `adjust_tox_loss(1)`；
- `prob(amount)` 概率 `losebreath += 3`。

### 食物气味类（/datum/pollutant/food，基类强度 0.5）

> 基类 `food`: `pollutant_flags = POLLUTANT_SMELL`，`smell_intensity = 0.5`（低强度以便远距离传播），descriptor = smell。

| 污染物 | 名称 | scent |
|---|---|---|
| `food/fried_meat` | Fried Meat | "fried meat"（炸肉） |
| `food/fried_bacon` | Fried Bacon | "fried bacon"（炸培根） |
| `food/fried_fish` | Fried Fish | "fried fish"（炸鱼） |
| `food/pancakes` | Pancakes | "pancakes"（煎饼） |
| `food/coffee` | Coffee | "coffee"（咖啡） |
| `food/tea` | Tea | "tea"（茶） |
| `food/chocolate` | Chocolate | "chocolate"（巧克力） |
| `food/spicy_noodles` | Spicy Noodles | "spicy noodles"（辣面） |

### 香氛类（/datum/pollutant/fragrance）

> 基类 `fragrance`: `pollutant_flags = POLLUTANT_SMELL`，`smell_intensity = 1`，descriptor = fragrance（香味）。

| 污染物 | 名称 | scent | smell_intensity |
|---|---|---|---|
| `fragrance/air_refresher` | Air Refresher（空气清新剂） | "a strong flowery scent"（浓郁花香） | **3** |
| `fragrance/cologne` | Cologne Water（古龙水） | "cologne" | 1 |
| `fragrance/wood` | Wood Perfume（木质香水） | "aging wood"（陈年木香） | 1 |
| `fragrance/rose` | Rose Perfume（玫瑰香水） | "roses"（玫瑰） | 1 |
| `fragrance/jasmine` | Jasmine Perfume（茉莉香水） | "jasmine"（茉莉） | 1 |
| `fragrance/mint` | Mint Perfume（薄荷香水） | "mint"（薄荷） | 1 |
| `fragrance/vanilla` | Vanilla Perfume（香草香水） | "vanilla"（香草） | 1 |
| `fragrance/pear` | Pear Perfume（梨香香水） | "pear"（梨） | 1 |
| `fragrance/strawberry` | Strawberry Perfume（草莓香水） | "strawberries"（草莓） | 1 |
| `fragrance/cherry` | Cherry Perfume（樱桃香水） | "cherries"（樱桃） | 1 |
| `fragrance/amber` | Amber Perfume（琥珀香水） | "a sweet and powdery scent"（甜美粉感） | 1 |

## 2.6 污染发射器（Emitters）

**代码**: `pollution_emitters.dm`、`pollution_initializations.dm`、`temporary_pollution_emission_component.dm`

### 发射器子系统（SSpollution_emitters）

`PROCESSING_SUBSYSTEM_DEF(pollution_emitters)`:
- `name` = "Pollution Emitters"；`priority = FIRE_PRIORITY_OBJ`；`ss_flags = SS_NO_INIT`；`wait = 10 SECONDS`；`init_stage = INITSTAGE_LAST`。

`/datum/element/pollution_emitter`（元素）:
- `element_flags = ELEMENT_DETACH_ON_HOST_DESTROY | ELEMENT_BESPOKE`，`argument_hash_start_idx = 2`；
- 参数: `pollutant_type`（污染物类型）、`pollutant_amount`（每次产生量）；
- `Attach(target, pollutant_type, pollutant_amount)`: 仅限 atom（否则 `ELEMENT_INCOMPATIBLE`）；加入 `affected` 并开始处理；
- `Detach`: 移出 `affected`，空列表时停止处理；
- `process`: 每 10 秒对每个受影响 atom 所在 turf 执行 `pollute_turf(pollutant_type, pollutant_amount)`。

### 内建发射器（pollution_initializations.dm）

| 对象 | 发射器 | 参数 |
|---|---|---|
| `/obj/effect/decal/cleanable/greenglow`（绿色粘液堆） | `AddElement(pollution_emitter, ...)` | `/datum/pollutant/chemical_vapors`，**10**/次（永久） |
| `/obj/item/reagent_containers/cup/glass/coffee`（咖啡杯） | `AddComponent(temporary_pollution_emission, ...)` | `/datum/pollutant/food/coffee`，**5**/次，**3 MINUTES** |
| `.../mug/tea`（茶杯） | 同上 | `/datum/pollutant/food/tea`，**5**，**3 MINUTES** |
| `.../mug/coco`（可可杯） | 同上 | `/datum/pollutant/food/chocolate`，**5**，**3 MINUTES** |

### 临时污染发射组件（temporary_pollution_emission）

`/datum/component/temporary_pollution_emission`:
- `dupe_mode = COMPONENT_DUPE_ALLOWED`（可叠加）；
- 参数: `pollutant_type`、`pollutant_amount`、`expiry_time`（= world.time + 传入时长）；
- 注册 `COMSIG_COMPONENT_CLEAN_ACT`（被清洗即移除，`wash_off`，mob 收到提示）→ `START_PROCESSING(SSobj, src)`；
- `process`: 到达过期时间或离开 turf 则 `qdel`；否则每 tick `pollute_turf(pollutant_type, pollutant_amount × seconds_per_tick)`；
- **香水喷射**会给目标物体附加该组件（`fragrance_type`，5/次，10 分钟）——见 2.10。

## 2.7 污染接口（pollute_turf）

**代码**: `modular_nova/modules/pollution/code/turf_open.dm`

| Proc | 基类 `/turf` | `/turf/open` | `/turf/open/space` |
|---|---|---|---|
| `pollute_turf(type, amount, cap)` | return（无操作） | 子系统关闭（`!SSpollution.can_fire`）则跳过；无污染则新建；`cap` 存在且 `total_amount >= cap` 则跳过；`add_pollutant` | **return（太空不产生污染）** |
| `pollute_turf_list(list, cap)` | return | 同上，`add_pollutant_list` | **return** |

## 2.8 净化设备（空气清新剂/污染洗涤器）

**代码**: `modular_nova/modules/pollution/code/air_refresher.dm`（62 行）

### 空气清新剂（/obj/item/air_refresher）

- name "air refresher"；描述"一瓶充满浓烈香气、带易用加压释放喷嘴的瓶子"；
- 图标 `pollution/icons/air_refresher.dmi`；`w_class = WEIGHT_CLASS_TINY`；`item_flags = NOBLUDGEON`；
- **`uses_remaining = 20`**（20 次使用）；
- 远程交互（`ranged_interact_with_atom`）: 剩余次数 >0 时消耗 1 次，对目标 turf `pollute_turf(/datum/pollutant/fragrance/air_refresher, 200)`；攻击间隔 ×2（`CLICK_CD_RANGE*2`）；播放 spray2.ogg（50 音量，-6 音高偏移）；消息/音效同步。
- 空瓶提示"已用完"。

### 污染洗涤器（/obj/machinery/pollution_scrubber）

- name "Pollution Scrubber"；描述"会处理空气并过滤任何污染物的洗涤器"；
- 图标 `pollution/icons/pollution_scrubber.dmi`（`scrubber` / 开启时 `scrubber_on`）；
- **`scrub_amount = 2`**（每次净化 2 单位）；`on = FALSE`（默认关闭）；
- `attack_hand`: 切换开关 + balloon_alert；
- `process`: 无机械故障（`machine_stat`）、开启且所在 turf 为 `/turf/open` 且有污染时 → `pollution.scrub_amount(2)`，**耗电 100 WATTS**。

## 2.9 篝火（Bonfire）

**代码**: `modular_nova/modules/pollution/code/bonfire.dm`

`/obj/structure/bonfire` 增加 `produces_smoke` 变量（默认 FALSE）；**只有玩家建造的篝火**（`/obj/structure/bonfire/player_made`，`produces_smoke = TRUE`）产生烟雾。

`process` 中: 燃烧中（`burning`）且 `produces_smoke` 时，若 turf 为 `/turf/open` 且**非行星大气**且**非 CentCom 层级**：
- `pollute_turf_list(list(/datum/pollutant/smoke = 15, /datum/pollutant/carbon_air_pollution = 5), POLLUTION_ACTIVE_EMITTER_CAP)`——每次产生 **15 单位烟雾 + 5 单位含碳空气污染**，受 200 上限（`POLLUTION_ACTIVE_EMITTER_CAP`）约束。

## 2.10 香水全录（Perfumes）

**代码**: `modular_nova/modules/pollution/code/perfumes.dm`（109 行）

### 基类（/obj/item/perfume）

- 描述"一瓶气味宜人的香水"；图标 `pollution/icons/perfume.dmi`；`w_class = WEIGHT_CLASS_TINY`；`item_flags = NOBLUDGEON`；
- `fragrance_type`（所用污染物类型）、**`uses_remaining = 10`**（10 次）、`cap = TRUE`（盖子状态）、`has_cap = TRUE`（是否有盖子）；
- 图标状态: 有盖且盖上时 `[icon_state]_cap`；
- 检查（examine）: 剩余次数；有盖时提示"take the cap off / put the cap on"；
- **开关盖**（`click_alt` / `attack_self` → `toggle_cap`）: 需 `NEED_DEXTERITY`；
- **喷射**（`interact_with_atom`）: 目标须为 movable；有盖且盖上 → 警告；次数 >0 时消耗 1 次，对**使用者所在 turf** `pollute_turf(fragrance_type, 20)`（20 单位）；攻击间隔 ×2；spray2.ogg；**给目标物体附加临时污染发射组件**（`fragrance_type`，5/次，**10 MINUTES**）——让被喷物体持续散发香味。

### 香水全表（10 种）

| 物品 | 名称 | fragrance_type（对应污染物） |
|---|---|---|
| `/obj/item/perfume/cologne` | cologne bottle（古龙水瓶） | `/datum/pollutant/fragrance/cologne` |
| `/obj/item/perfume/wood` | wood perfume bottle（木质香水瓶） | `fragrance/wood` |
| `/obj/item/perfume/rose` | rose perfume bottle（玫瑰香水瓶） | `fragrance/rose` |
| `/obj/item/perfume/jasmine` | jasmine perfume bottle（茉莉香水瓶） | `fragrance/jasmine` |
| `/obj/item/perfume/mint` | mint perfume bottle（薄荷香水瓶） | `fragrance/mint` |
| `/obj/item/perfume/vanilla` | vanilla perfume bottle（香草香水瓶） | `fragrance/vanilla` |
| `/obj/item/perfume/pear` | pear perfume bottle（梨香香水瓶） | `fragrance/pear` |
| `/obj/item/perfume/strawberry` | strawberry perfume bottle（草莓香水瓶） | `fragrance/strawberry` |
| `/obj/item/perfume/cherry` | cherry perfume bottle（樱桃香水瓶） | `fragrance/cherry` |
| `/obj/item/perfume/amber` | amber perfume bottle（琥珀香水瓶） | `fragrance/amber` |

> 古龙水描述彩蛋: "This one is sure to attract ladies."（这瓶肯定能吸引女士。）

## 2.11 香薰蜡烛与包装

**代码**: `scented_candles.dm`、`fancy_storage_items.dm`

### 香薰蜡烛（5 种，继承 `/obj/item/flashlight/flare/candle`）

| 物品 | 名称 | scented_type |
|---|---|---|
| `candle/vanilla` | vanilla scented candle（香草香薰蜡烛） | `/datum/pollutant/fragrance/vanilla` |
| `candle/pear` | pear scented candle（梨香香薰蜡烛） | `fragrance/pear` |
| `candle/amber` | amber scented candle（琥珀香薰蜡烛） | `fragrance/amber` |
| `candle/jasmine` | jasmine scented candle（茉莉香薰蜡烛） | `fragrance/jasmine` |
| `candle/mint` | mint scented candle（薄荷香薰蜡烛） | `fragrance/mint` |

### 蜡烛盒包装（5 种，继承 `/obj/item/storage/fancy/candle_box`）

| 物品 | 名称 | spawn_type |
|---|---|---|
| `candle_box/vanilla` | vanilla candle pack（香草蜡烛盒） | `/obj/item/flashlight/flare/candle/vanilla` |
| `candle_box/pear` | pear candle pack（梨香蜡烛盒） | `candle/pear` |
| `candle_box/amber` | amber candle pack（琥珀蜡烛盒） | `candle/amber` |
| `candle_box/jasmine` | jasmine candle pack（茉莉蜡烛盒） | `candle/jasmine` |
| `candle_box/mint` | mint candle pack（薄荷蜡烛盒） | `candle/mint` |

## 2.12 管理指令

**代码**: `modular_nova/modules/pollution/code/admin_spawn_pollution.dm`

`ADMIN_VERB(spawn_pollution, R_ADMIN, "生成污染", "Spawns an amount of chosen pollutant at your current location.", ADMIN_CATEGORY_FUN)`:
1. tgui 列表选择污染物类型（来自 `SSpollution.singletons`）；
2. 输入数量（`input as null|num`）；
3. 在管理员当前位置 turf 执行 `pollute_turf(choice, amount_choice)`；
4. `message_admins` + `log_admin` 记录（含 `ADMIN_LOOKUPFLW`）。

## 2.13 视觉效果（Overlay）

**代码**: `modular_nova/modules/pollution/code/pollution_effect.dm`（10 行）+ `pollution.dm`

`/obj/effect/abstract/pollution`（视觉层）:
- 图标 `icons/effects/96x96.dmi`，`icon_state = "smoke"`；`pixel_x/y = -32`（居中 96×96）；
- `anchored = TRUE`；`plane = ABOVE_GAME_PLANE`；`layer = FLY_LAYER`；`mouse_opacity = MOUSE_OPACITY_TRANSPARENT`；`appearance_flags = KEEP_APART|RESET_TRANSFORM|RESET_COLOR`。

`get_overlay(pollutant_list, total_amount)` 生成规则:
- 单一污染物: 取该污染物（须带 `POLLUTANT_APPEARANCE` 旗标），`total_thickness = total_amount × thickness`；
- 多种污染物: 逐项计算 `amount × thickness`，**取厚度最大者**作为主导外观；
- 总厚度 < **30**（`POLLUTANT_APPEARANCE_THICKNESS_THRESHOLD`）→ 无 overlay；
- `overlay.alpha = floor(pollutant.alpha × total_thickness × 0.0025)`（THICKNESS_ALPHA_COEFFICIENT）；`overlay.color = pollutant.color`。

---

# 附录 · 代码路径索引

## 冬眠舱（Cryosleep）— `modular_nova/modules/cryosleep/`

| 文件 | 行数 | 内容 |
|---|---|---|
| `code/cryopod.dm` | 700 | 冬眠舱本体、控制台、公告、幽灵角色挂钩、目标处理 |
| `code/cryo_console_return.dm` | 23 | 人类 proc `return_items_to_console`（物品送回控制台） |
| `code/config.dm` | 5 | `cryo_min_ssd_time`（15 分钟）、`cryo_policy` 配置 |
| `code/ai.dm` | 20 | AI 动词 `ai_cryo`（AI 低温休眠） |
| `code/admin.dm` | 24 | `send_to_cryo`（送玩家入舱 + 通知纸） |
| `code/job.dm` | 27 | `FreeRole` / `OccupyRole` 职业槽管理 |
| `code/jobs.dm` | 24 | 部门默认无线电频道 |
| `code/mind.dm` | 2 | `/datum/mind` objectives 声明 |
| `code/mood.dm` | 9 | `tucked_in` 盖被子心情（+3 / 2 分钟） |
| `readme.md` | 23 | 模块说明（ID: CRYOSLEEP，作者 Azarak） |
| `icons/cryogenics.dmi` | — | 图标资源 |

## 污染（Pollution）— `modular_nova/modules/pollution/`

| 文件 | 行数 | 内容 |
|---|---|---|
| `code/pollution.dm` | 246 | `/datum/pollution` 数据与扩散/净化/嗅闻核心 |
| `code/pollutants_generic.dm` | 178 | 23 种具名污染物定义 |
| `code/perfumes.dm` | 109 | 香水基类 + 10 种香水 |
| `code/air_refresher.dm` | 62 | 空气清新剂 + 污染洗涤器 |
| `code/pollution_subsystem.dm` | 61 | SSpollution 子系统（处理/消散轮换） |
| `code/pollution_emitters.dm` | 40 | SSpollution_emitters + `pollution_emitter` 元素 |
| `code/temporary_pollution_emission_component.dm` | 36 | 临时污染发射组件（可被清洗移除） |
| `code/turf_open.dm` | 33 | `pollute_turf` / `pollute_turf_list` 接口 |
| `code/pollutant_datum.dm` | 27 | `/datum/pollutant` 框架 |
| `code/bonfire.dm` | 19 | 玩家篝火烟雾排放 |
| `code/fancy_storage_items.dm` | 19 | 5 种香薰蜡烛盒 |
| `code/scented_candles.dm` | 19 | 5 种香薰蜡烛 |
| `code/pollution_initializations.dm` | 15 | 粘液堆/咖啡/茶/可可内建发射器 |
| `code/admin_spawn_pollution.dm` | 12 | 管理指令"生成污染" |
| `code/pollution_effect.dm` | 10 | 视觉 overlay 对象 |
| `icons/` | — | air_refresher.dmi / perfume.dmi / pollution_scrubber.dmi |

## 宏定义 — `code/__DEFINES/~nova_defines/pollution.dm`（41 行）

污染旗标、气味阈值（3.5/20/40）、高度除数（10）、消散周期（20 tick）、行星大气乘数（4）、发射器上限（200/70）、透明度系数（0.0025）、SCENT 描述符、注册宏。
