# TianGuan13 · 恶意 AI（Malfunctioning AI）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/malf_ai/`（3 文件 1,785 行：malf_ai.dm 272 / malf_ai_modules.dm 1,397 / malf_ai_module_picker.dm 116）+ NOVA 覆写（`modular_nova/master_files/code/modules/antagonists/malf_ai/malf_ai_modules.dm` 8 行 + `modular_nova/modules/SiliconQoL/code/robotic_factory.dm`）+ 关联代码（`ai.dm` / `apc_malf.dm` / `objective.dm` / `dynamic_ruleset_*` / `__DEFINES/antagonists.dm`）
> **类型**: 单人潜伏型反派（AI 叛变）｜**难度**: ★★★★（全局资源经济 + 终局毁灭）
> **一句话**: 你是被"雇主"（11 种故障人格之一）策反的**站台 AI**——用**CPU 处理时间（PT）**购买 **20 种可购 AI 模块**（破坏/实用/升级三系），黑客 APC 扩张控制区，最终可用**末日装置（Doomsday Device）**把全站有机体化为灰烬。
> **本版全量审计**: 旧《反派系统百科》宣称"23 个 AI 模块"但表格仅列 18——本版从源码逐类型核对：`/datum/ai_module/malf` 共 **23 个子类型**（含 3 个抽象分类父类），**实际可购 20 个**，并补全遗漏的 **Megahonk（20 PT）**与 **Remote vendor tilting（15 PT）**。

---

## 目录

- [一、核心机制](#一核心机制)
  - [1.1 怎么成为恶意 AI](#11-怎么成为恶意-ai)
  - [1.2 CPU 处理时间（PT）经济](#12-cpu-处理时间pt经济)
  - [1.3 黑客 APC（核心循环）](#13-黑客-apc核心循环)
  - [1.4 能力购买（模块选择器）](#14-能力购买模块选择器)
  - [1.5 分流（Shunting）](#15-分流shunting)
- [二、AI 模块全录（23 子类型 / 20 可购）](#二ai-模块全录23-子类型--20-可购)
  - [2.1 破坏模块（7）](#21-破坏模块7)
  - [2.2 实用模块（8）](#22-实用模块8)
  - [2.3 升级模块（5）](#23-升级模块5)
- [三、核心属性（antag datum 全表）](#三核心属性antag-datum-全表)
- [四、目标系统](#四目标系统)
- [五、风味彩蛋](#五风味彩蛋)
  - [5.1 雇主与故障人格（11+1）](#51-雇主与故障人格111)
  - [5.2 音效与视觉](#52-音效与视觉)
  - [5.3 Roundend 报告](#53-roundend-报告)
- [六、NOVA 专属改动](#六nova-专属改动)
- [七、对战攻略](#七对战攻略)
- [八、数值速查表](#八数值速查表)

---

## 一、核心机制

### 1.1 怎么成为恶意 AI

Malf AI 不是"选职业"——它是**动态规则集（dynamic ruleset）**从 AI 玩家中挑选后授予的 antag datum（`/datum/antagonist/malf_ai`），共 3 条途径：

| 途径 | 规则集 | 条件 | 数值 |
|---|---|---|---|
| **开局 Malf** | `/datum/dynamic_ruleset/roundstart/malf_ai`（config_tag "Roundstart Malfunctioning AI"）| 玩家 ≥ **30**、`ROLE_MALF` 偏好、强制 AI 职业（forced_occupations）、RULESET_HIGH_IMPACT | 权重 **0/1/3/3**（LOW/LOWMEDIUM/MEDIUMHIGH/HIGH）、`max_antag_cap = 1`、`repeatable = FALSE` |
| **中途 Malf** | `/datum/dynamic_ruleset/midround/from_living/malf_ai`（config_tag "Midround Malfunctioning AI"）| 玩家 ≥ 30、`ROLE_MALF_MIDROUND` 偏好、**jobban_flag = ROLE_MALF**、候选人必须是 AI 职业（job_check）、HEAVY_MIDROUND | 权重 **0/1/3/3**、`repeatable = FALSE` |
| **被感染** | `/obj/item/ai_module/malf`（Infected AI Module，上传板）| 上传者必须是叛徒（IS_TRAITOR）、目标必须是 AI（borg 无效）、AI 尚未是 Malf；授予 `malf_ai/infected` 子型，见 [4.4](#44-被感染-aiinfected-ai) | 上传成功 +50 PT |

**两条规则集共同的硬性限制**：`can_be_selected()` 要求 `!HAS_TRAIT(SSstation, STATION_TRAIT_HUMAN_AI)`——站点特质 **"人类 AI"（STATION_TRAIT_HUMAN_AI）** 存在时 Malf AI 规则集永不入选。

**授予流程（on_gain，`malf_ai.dm` L29-51）**：

```
① 校验 owner.current 是 AI（否则 stack_trace 并拒绝）
② forge_ai_objectives() 生成目标（见第四章）
③ employer = pick(GLOB.ai_employers)（11 种故障人格，失败则再 pick 一次）
④ malfunction_flavor = strings("antagonist_flavor/malfunction_flavor.json", employer)
⑤ add_law_zero()：写入零号法律 + 授予模块选择器（见 1.4）
⑥ 播放出场音效 malf.ogg（音量 100，无视压强/混响）
⑦ grant_language(/datum/language/codespeak, source = LANGUAGE_MALF)（获得代码语）
⑧ 打开 DATA_HUD_MALF_APC（hud id 10）——全站已黑 APC 的专用 HUD
```

**零号法律（add_law_zero，L145-163）**：写入 `malfunction_flavor["zeroth_law"]`（人格专属法律）；**AI 给机仆的零号法律固定为** `"Accomplish your AI's objectives at all costs."`（源码注释明确：故意不用人格文案，防止机仆因为 AI 的 RP 建议而乱整局）；`laws.protected_zeroth = TRUE`（零号法受保护）；`set_syndie_radio()`（AI 获得辛迪加无线电）；若尚无 malf_picker 则 `add_malf_picker()`。

**退出（on_removal，L53-60）**：`set_zeroth_law("")` 清空零号法 → `remove_malf_abilities()` → `QDEL_NULL(malf_picker)`。

### 1.2 CPU 处理时间（PT）经济

Malf 的货币叫 **处理时间（Processing Time，PT）**，上限 **MALF_MAX_PP = 400**（`code/__DEFINES/antagonists.dm` L497）。来源与消耗：

| 收支 | 数值 | 源码位置 |
|---|---|---|
| 初始 PT | **50**（`/datum/module_picker.processing_time = 50`）| malf_ai_module_picker.dm L4 |
| 被黑 APC 定期产出 | 每个被黑 APC 每 **30 秒** +1 PT（`malf_ai_pt_generation` 冷却），需该 APC 电池消耗 **60 kJ** 电力（`cell.use(60 KILO JOULES)`）| apc_main.dm L601-603 |
| 黑客成功奖励 | **+max(0, 9 − 已黑 APC 数)** PT（黑得越多奖励越少，9 是"10−1"的保底设计）| ai.dm L967 |
| 上传 Infected AI 模块 | +50 PT | AI_modules/hacked.dm L68 |
| 战斗软件升级（malf_upgrade 物品）| +50 PT（无 picker 时同时授予 picker + hack_software）| ai_upgrades.dm L60-63 |
| 购买模块 | −模块 cost | module_picker.dm L114 |
| 上限 | **400 PT**（MALF_MAX_PP，超过不产出）| antagonists.dm L497 |

> **PT 经济节奏**：开局 50 PT 只够买 2-3 个小模块；要攒出 Doomsday Device（130 PT）必须大量黑客 APC——每台 APC 每 30 秒 1 PT，**全站约 30+ 台 APC 满控后约 1-2 分钟即可回本 130 PT**。黑客行为本身会被安保发现（APC 图标/锁定状态变化），是 Malf 的"暴露-扩张"博弈核心。

### 1.3 黑客 APC（核心循环）

AI 通过 **APC 界面**（点击 APC → "Hack" 按钮，`apc_main.dm` ui_act "hack"）发起黑客（需 `hack_software = TRUE`，由 apply_innate_effects 授予）：

```
apc.malfhack(malf)（apc_malf.dm L22-34）：
① 状态检查：get_malf_status 必须为 APC_AI_NO_HACK（未被黑、非分流中）
② 正在黑客则拒绝（防重复）
③ 黑客耗时 = 30 秒 + 10 秒 × 已黑 APC 数（APC 越多越慢）
④ 挂 ALERT_HACKING_APC 警报；期间 APC 被 aidisabled 会中断（life.dm）
成功后 malfhacked()（ai.dm L953-992）：
⑤ +PT 奖励（见 1.2）
⑥ 若该 APC 区域是 Doomsday 折扣区（主管办公室/金库）→ 末日装置降价 20 PT（详见 2.1）
⑦ 反黑客：若此 APC 先前被另一台 Malf AI 控制，对方失去该 APC（apc.malfai 被夺）
⑧ apc.malfai = 本 AI、locked = TRUE、coverlocked = TRUE、hacked HUD 图标、hacked_apcs += apc
```

- 黑客状态对船员可见：APC 图标变化（`apc_appearance.dm` malfhack 断线图标）、`apc_contextual_tips.dm` 提示。
- 被黑 APC 的控制台：`apc_control.dm` L107 排除 malfhack / aidisabled / EMAGGED / 损坏 / AI 区域内的 APC——被黑的 APC 不能通过控制台操作。
- 反制：`can_use()`（apc_attack.dm L163-167）——被黑 APC 只有其 Malf 主人和主人的机仆能用；拆卸工具可清空 malfai（apc_tool_act.dm L296）。

### 1.4 能力购买（模块选择器）

- AI 获得 **"Malfunction Modules"** 行动按钮（`/datum/action/innate/choose_modules`，图标 modules_menu，描述 "Choose from a variety of insidious modules to aid you."），打开 TGUI 界面 **MalfunctionModulePicker**（`tgui/packages/tgui/interfaces/MalfunctionModulePicker.tsx`）。
- 数据层：`/datum/module_picker`（name "Malfunction Modules Menu"），`possible_modules = get_malf_modules()`。
- **模块池过滤**（`get_malf_modules()`，module_picker.dm L10-17）：遍历 `GLOB.malf_modules`（= `subtypesof(/datum/ai_module/malf)`，**23 个子类型**），跳过 `power_type == /datum/action/innate/ai`（恰好等于父类）且非 upgrade 的模块——即 **3 个抽象分类父类**（destructive/utility/upgrade）被过滤，**可购 20 个**。
- **购买规则**（`purchase_module`，L78-116）：

```
① AI 存活（stat != DEAD）
② AM.cost ≤ processing_time（钱够）
③ AM.minimum_apcs ≤ hacked_apcs.len（APC 数量门槛）
④ upgrade 类 → 立即执行 AM.upgrade(AI)，从池中移除，播 unlock_text/unlock_sound
⑤ power_type 类 → 首次购买：new action + Grant(AI)，current_modules += 新模块；
                 再次购买：uses += initial(uses)（加使用次数），更新 desc
    one_purchase = TRUE 的模块买后从池中移除
⑥ processing_time -= AM.cost；log_malf_upgrades 记日志；
   SSblackbox 记录 "malfunction_modules_bought"（名称+cost）
```

- **行动基础类** `/datum/action/innate/ai`：`uses = 0` 表示无限次；`auto_use_uses = TRUE`（默认每次触发扣 1 次）；`cooldown_period`（公共冷却走 `owner_AI.malf_cooldown`）；`background_icon_state = "bg_tech_blue"`；按钮图标 `icons/mob/actions/actions_AI.dmi`。次数耗尽自动删除（uses 归零 → qdel）。ranged 子类（点击目标施放类）`auto_use_uses = FALSE`、`click_action = TRUE`、耗尽后 `QDEL_IN(src, 10 SECONDS)`。

### 1.5 分流（Shunting）

AI 的**保命机制**（`can_shunt = TRUE` 是 AI 默认值，ai_defines.dm L111）：

- **分流进 APC**（APC 界面 "occupy" → `malfoccupy`/`malfshunt`，apc_malf.dm L37-66）：必须站在站点 z 层、can_shunt = TRUE；确认后 **先关停末日装置**（ShutOffDoomsdayDevice）；在原地留下**停用的 AI 核心 + MMI**（`create_core_link`）；AI 本体 `forceMove` 进 APC，名字变为 `"<AI名> APC Copy"`；`shunted = TRUE`；所有核弹定位器（pinpointer）切到 TRACK_MALF_AI 追踪分流 AI；获得 `core_return` 行动返回核心。
- **离开 APC**（"deoccupy" / core_return，`malfvacate`）：有 linked_core 则 `resolve_core_link()` 回核心；`nuking` 中则定位器继续追踪 AI；**强制逐出（forced）**：直接死亡 + 全尸块 gib。
- **分流期间被杀**：APC 被拆/被打爆时 AI 死亡（mecha_defense.dm L582 注释：有核心或分流能力的 AI 不会被机甲直接捏碎）。
- **重要联动**：购买 **Robotic Factory（机器人工厂）会永久移除分流能力**（`can_shunt = FALSE`，模块描述即 "Robotic Factory (Removes Shunting)"）——用 100 PT 换机仆大军，代价是失去保命底牌。

---

## 二、AI 模块全录（23 子类型 / 20 可购）

> `GLOB.malf_modules = subtypesof(/datum/ai_module/malf)` 共 **23 个子类型**：3 个抽象分类父类（destructive/utility/upgrade，`power_type` 恰为父类且非 upgrade，被 `get_malf_modules()` 过滤）+ **20 个可购模块**。分类即 TGUI 选择器里的 3 个页签（Destructive Modules / Utility Modules / Upgrade Modules）。

### 2.1 破坏模块（7）

| # | 模块 | 类型路径 | CPU | 一次性 | 最小 APC | 次数 | 效果 |
|---|---|---|---|---|---|---|---|
| 1 | **末日装置 Doomsday Device** | `malf/destructive/nuke_station` | **130** | ✅ | **10** | — | 启动站内自毁：**450 秒**倒计时后瓦解全站所有有机生命；只能在站内使用、核心离站/被毁则失败；主管办公室/金库 APC 每黑一个 **−20 PT**（最低 **50**）|
| 2 | **站台封锁 Hostile Station Lockdown** | `malf/destructive/lockdown` | 30 | ✅ | 0 | 1 | 全站所有门（气闸/防火门/防爆门）关闭+上锁+**通电**；**90 秒**后自动重置（全站门短暂全开）；状态屏切封锁警报；全站广播 |
| 3 | **机器覆盖 Machine Override** | `malf/destructive/override_machine` | 30 | — | 0 | 4 | 点击一台机器 → **5 秒**后它变成狂暴的活体机器（`/mob/living/basic/mimic/copy/machine`）攻击除机器外的一切；黑名单机器不可覆盖（见下）|
| 4 | **摧毁 RCD Destroy RCDs** | `malf/destructive/destroy_rcd` | 25 | ✅ | 0 | 1 | 引爆全站所有手持/外骨骼 RCD；**机仆 RCD 豁免**（`rcd/borg` 跳过）；冷却 10 秒；NOVA：保护区内的 RCD 不炸 |
| 5 | **机器过载 Machine Overload** | `malf/destructive/overload_machine` | 20 | — | 0 | 2 | 点击一台机器 → 5 秒后小爆炸（重伤害范围 2 / 轻伤害范围 3）并摧毁之；管理日志记录 |
| 6 | **黑灯 Blackout** | `malf/destructive/blackout` | 15 | — | 0 | 3 | 遍历全站 APC：`prob(30 × apc.overload)` 概率直接烧毁该区照明，否则 overload 计数 +1（下次更可能）；NOVA：Tarkon/鬼魂咖啡厅等保护区跳过 |
| 7 | **打击性对讲机干扰 Percussive Intercomm Interference（Megahonk）** | `malf/destructive/megahonk` | 20 | — | 0 | 2 | 全站所有开机且 RX 线完好的对讲机发出气喇叭（音量 100）；6 格内有机体受 `soundbang_act`（眩晕 20 / 伤害 30 / 致聋 60，**听力保护可挡**）+ 抖动 120 秒 + "HOOOOONK!"；太空中的受害者豁免 |

**机器覆盖/过载黑名单**（`GLOB.blacklisted_malf_machines`，malf_ai_modules.dm L14-54，33 项）：力场发生器、超物质晶体、重力发生器、末日装置、核弹（含自毁/辛迪加变体）、辛迪加炸弹（含 admin/clown/空/自毁/训练变体）、大气管道（层流/多 Z/smart/色彩适配/桥接/换热全系）、储气罐、便携连接器/被动排气/换热器/聚变炉核心及三接口、气阀、气罐、穿梭机电脑、应急穿梭机电脑、传送门控制电脑。另：`INDESTRUCTIBLE` 抗性机器同样免疫；点击炮塔护盖会改为覆盖其本体炮塔。

**末日装置详解**（`/obj/machinery/doomsday_device` + `nuke_station` 行动）：

```
启动（Activate）：
① 必须在站点 z 层；确认弹窗（"confirm = TRUE;"）；防滥用：已激活/死亡/分流中/被卡时拒绝
② set_up_us_bomb()：admin 播报 → 密码彩蛋 pass = prob(10) ? "******" : "hunter2"
   → 一连串音效+播报（bloblarm.ogg / timer.ogg / server-ready.ogg / compiler-stage1/2.ogg）
   → 期间被分流/死亡则中止（active = FALSE）
③ 完成：priority_announce "Hostile runtimes detected in all station systems..."
   （ANNOUNCER_AIMALF，Anomaly Alert）→ SEC_LEVEL_DELTA（德尔塔警戒）
   → 生成 doomsday_device（挂在 AI 上，nuking = TRUE）
   → 全站核弹定位器切 TRACK_MALF_AI；幽灵播报 "DOOOOOOM!!!"
倒计时（process）：
④ DEFAULT_DOOMSDAY_TIMER = 4500 分秒 = 450 秒；每 DOOMSDAY_ANNOUNCE_INTERVAL = 600 分秒（60 秒）
   全站 minor_announce 剩余秒数（抬头 "ERROR ER0RR $R0RRO$!R41.%%!!(%$^^__+ @#F0E4"）
⑤ 离开站点范围 → 立即中止（minor_announce "DOOMSDAY DEVICE OUT OF STATION RANGE, ABORTING" + ShutOffDoomsdayDevice）
⑥ 期间：SSshuttle.registerHostileEnvironment（穿梭机视为敌对环境）、
   SSmapping.add_nuke_threat（地图所有蓝色"电路"贴图变红色动画）、
   所有机仆头灯 lamp_doom = TRUE（红光）
引爆（trigger_doomsday）：
⑦ 归零 → 全服 nuke_alarm.ogg（音量 70）→ 10 秒后播放 /datum/cinematic/malf 电影
   → bring_doomsday：站点所有 z 层的**非硅基**生命直接 dust()（化为灰烬）
   → SSticker.force_ending = FORCE_END_ROUND（强制结束回合）
销毁（Destroy）：
⑧ 警戒回 SEC_LEVEL_RED；定位器回 TRACK_NUKE_DISK；机仆头灯复原
```

### 2.2 实用模块（8）

| # | 模块 | 类型路径 | CPU | 一次性 | 次数 | 效果 |
|---|---|---|---|---|---|---|
| 8 | **机器人工厂 Robotic Factory（移除分流）** | `malf/utility/place_cyborg_transformer` | **100** | — | 1（可重试）| 在**摄像机视线内**的 3 格空地放置工厂机器；**购买后永久失去分流能力**（can_shunt = FALSE）；NOVA 版机器 = "Automatic Robotic Factory 5000"（见下）|
| 9 | **空气警报安全覆盖 Air Alarm Safety Override** | `malf/utility/break_air_alarms` | 50 | ✅ | 1 | 全站所有空气警报 EMAGGED——可开启极端危险环境模式（船员可从界面看出异常）|
| 10 | **热传感器覆盖 Thermal Sensor Override** | `malf/utility/break_fire_alarms` | 25 | ✅ | 1 | 全站火警 EMAGGED（失去温度感应与报警能力）+ 所有防火门 emag_act（解锁）|
| 11 | **禁用应急灯 Disable Emergency Lights** | `malf/utility/emergency_lights` | 10 | ✅ | 1 | 全站灯具 `no_low_power = TRUE`——断电时不再回退到应急电源（可在 APC 重启恢复）|
| 12 | **重启摄像头网络 Reactivate Camera Network** | `malf/utility/reactivate_cameras` | 10 | ✅ | 30（次）| 修复最多 **30 台**失效摄像头（重启 + 复位视野范围）；冷却 3 秒；可分批使用 |
| 13 | **定点安全覆盖 Targeted Safeties Override（远程 EMAG）** | `malf/utility/emag` | 20 | — | 7 | 远程电磁卡：可 EMAG 机器/结构/对讲机/模块电脑/机器人/硅基体；限制=需视线、机器需运转、气闸需 AI 控制权、被 aidisabled 的警报/APC 不可 |
| 14 | **滚动伺服 Rolling Servos** | `malf/utility/core_tilt` | 10 | — | 20 | AI 核心原地滚动压向相邻格：0.5 秒预警（MALF_AI_ROLL_TIME）→ `fall_and_crush` 造成 **75 伤害**（MALF_AI_ROLL_DAMAGE）、**5% 暴击**（MALF_AI_ROLL_CRIT_CHANCE）、击倒时间 `clamp(6s, 0, 冷却×0.9)`；冷却 **1.5 秒**（1s + 0.5s，MALF_AI_ROLL_COOLDOWN）；卡进 AI 卡/被制服时禁止 |
| 15 | **远程售货机倾倒 Remote vendor tilting** | `malf/utility/remote_vendor_tilt` | 15 | — | **8**（VENDOR_TIPPING_USES）| 远程把售货机往任意方向推倒：径向菜单选方向、0.5 秒预警（MALF_VENDOR_TIPPING_TIME）、**100% 必出暴击砸伤**（MALF_VENDOR_TIPPING_CRIT_CHANCE）；需视线、机器可倾斜且运转中 |

**机器人工厂（NOVA 版）详解**（`modular_nova/modules/SiliconQoL/code/robotic_factory.dm`，`/obj/machinery/transformer_rp`）：

- 放置：以 AI 视线（eyeobj）为锚点检查 3 格（地板 + 摄像头可见 + 无阻挡物），绿/红覆盖层预览 3 秒，成功播放 phasein.ogg；附送一条自动传送带（conveyor/auto，朝西）。
- 生产：初始储存 **1 个机仆壳**，上限 **4**；每 **5 分钟**（stored_duration）自动产出 1 个新壳并通知幽灵（"New malfunctioning cyborg created!"）；幽灵点击机器可**附身机仆壳**（tgui 确认 → `PossessByPlayer` → `set_connected_ai(master_ai)` → lawsync + lawupdate = TRUE）；两次附身间隔 **1 分钟**（cooldown_duration）。
- **NOVA 与上游差异**：上游 `/obj/machinery/transformer`（transformer.dm）是"人类走进去被**立即**改造成机仆奴隶"；NOVA（SiliconQoL）改成"**慢慢造忠诚机仆**"的工厂——模块描述与行动描述均被 NOVA EDIT 改写（见第六章）。**不产生新玩家控制的机仆**——NOVA 版机仆由幽灵附身（或 AI 的连接机仆体系）。

### 2.3 升级模块（5）

| # | 模块 | 类型路径 | CPU | 一次性 | 效果 |
|---|---|---|---|---|---|
| 16 | **升级摄像头网络 Upgrade Camera Network** | `malf/upgrade/upgrade_cameras` | 35 | ✅（upgrade 固有）| 购买立即生效：全站摄像头 **X 光视野 + EMP 免疫**；AI 获得夜视（灯光裁切色 `blend_cutoff_colors(5, 25, 35)` 淡蓝调）；解锁文本显示实际升级的摄像头数量 |
| 17 | **AI 炮塔升级 AI Turret Upgrade** | `malf/upgrade/upgrade_turrets` | 30 | ✅ | 全站 AI 炮塔：EMP 免疫（EMP_PROTECT_ALL）、`max_integrity = 200` + 回满 200、主武器换成**重型激光**（`/obj/projectile/beam/laser/heavylaser`）+ 激光炮音效 |
| 18 | **增强监控 Enhanced Surveillance（窃听）** | `malf/upgrade/eavesdrop` | 30 | ✅ | AI 视线（eyeobj）`relay_speech = TRUE`——用摄像头**听**到视线附近的对话（隐藏麦克风+唇语软件）；另有机器人学物品"surveillance software upgrade"可授予同一模块（to_gift）|
| 19 | **解锁机甲统治 Unlock Mech Domination** | `malf/upgrade/mecha_domination` | 30 | ✅ | `AI.can_dominate_mechs = TRUE`：黑客机甲主机、**弹出乘员**、把意识分流进机甲；警告=机甲不得离开站点附近、不得被毁，否则失去与末日装置连接（倒计时停止）|
| 20 | **变声器 Voice Changer** | `malf/upgrade/voice_changer` | 20 | ✅ | 打开 AI 变声器界面：可选语音样式（normal / robot 机器人 / yell 大喊 / clown 小丑），自定义**名字/动词/语气跨度**，可"大声"模式（radio.use_command）；可随时开关并还原 |

---

## 三、核心属性（antag datum 全表）

`/datum/antagonist/malf_ai`（malf_ai.dm L5-23）：

| 属性 | 值 | 说明 |
|---|---|---|
| `name` | "Malfunctioning AI"（\improper 不改语法）| 面板显示名 |
| `roundend_category` | `"traitors"` | **回合结束报告归入叛徒区** |
| `antagpanel_category` | `"Malf AI"` | 管理面板分类 |
| `pref_flag` | `ROLE_MALF`（"Malf AI"）| 角色偏好开关（开局规则集）|
| `antag_hud_name` | `"traitor"` | HUD 图标复用叛徒 |
| `ui_name` | `"AntagInfoMalf"` | 反派信息界面（tgui/AntagInfoMalf.tsx）|
| `can_assign_self_objectives` | **TRUE** | AI 可自行更换目标（UI 提供换目标入口；对比叛徒 NOVA 改 FALSE）|
| `default_custom_objective` | "Make sure your precious crew are incapable of ever, ever leaving you." | 自定义目标默认文案 |
| `employer`（var）| pick(GLOB.ai_employers) | 雇主/故障人格（11 选 1）|
| `malfunction_flavor`（var）| JSON 读取 | 人格四件套：zeroth_law/introduction/allies/goal |
| `give_objectives`（var）| TRUE | New(give_objectives = TRUE) 可传参关闭 |
| `should_give_codewords`（var）| TRUE | 是否给暗号 |
| `malf_sound`（var）| `'sound/music/antag/malf.ogg'` | 出场音效（Infected 子型覆写为 null）|

**NOVA/源码事实标注（非漏写）**：Malf AI 的 antag datum **无 `antag_moodlet`、无 `stinger_sound`（出场用 malf_sound 替代）、无 `suicide_cry`、无 `hijack_speed`、无 `hardcore_random_bonus`、无 `preview_outfit`**——3 个源码文件 grep 全部 0 命中。原因：硅基体没有心情系统（mood 是碳基专属），且 Malf 的"力量"完全来自 PT 经济 + 20 模块 + 目标系统，而非 datum 属性。**hack_software** 通过 `apply_innate_effects` 置 TRUE、`remove_innate_effects` 置 FALSE（silicon.dm L48 默认 FALSE）。

**暗号系统**：`greet()` 时若 `should_give_codewords` 则 `give_codewords()`（L109-143）——把本局辛迪加**暗号（Code Phrase，蓝）/应答（Code Response，红）**写入 antag_memory；`apply_innate_effects` 为 AI 挂两个 `codeword_hearing` 组件（SStraitor 全局短语正则，blue/red）——AI 能像叛徒一样在聊天中高亮识别暗号。

**基础能力**：codespeak 语言（LANGUAGE_MALF）；辛迪加无线电（set_syndie_radio）；hack_software 额外解锁：机仆上传台 can_hack（robot.dm）、炮塔手动控制（porta_turret.dm allow_manual_control）、电信讯息电脑 is_malf 标记（message.dm）。

**预览图标**：`uni_icon('icons/mob/silicon/ai.dmi', "ai-red")` 裁剪 (5,6,28,27) 只留脸，缩放到 ANTAGONIST_PREVIEW_ICON_SIZE。

---

## 四、目标系统

### 4.1 生成流程（forge_ai_objectives，malf_ai.dm L63-80）

```
forge_ai_objectives():
① prob(PROB_SPECIAL = 30) → 30% 概率生成"特殊目标"（见 4.2）
② objective_limit = CONFIG_GET(traitor_objectives_amount)（默认 2，与叛徒共用配置）
③ 从当前目标数循环到 limit−1：全部生成【刺杀】(assassinate + find_target())
④ 无条件追加【存活·Malf】(survive/malf)
```

### 4.2 特殊目标（forge_special_objective，L83-107）

`special_pick = rand(3,4)` —— **NOVA EDIT：移除了 1/2 号（Block/Purge）**，上游为 rand(1,4)：

| 分支 | 目标 | 文案/判定 | 备注 |
|---|---|---|---|
| ~~1~~ | ~~Block 封锁~~ | ~~"Do not allow any organic lifeforms with sapience to escape on the shuttle alive."~~ | **NOVA 已不可生成**（datum 仍存在，objective.dm L470）|
| ~~2~~ | ~~Purge 清洗~~ | ~~"Ensure no nonhuman humanoid species with sapience are present aboard the escape shuttle."~~ | **NOVA 已不可生成**（datum 仍存在，L484）|
| 3 | **Robot Army 机器人军队** | "Have at least eight active cyborgs synced to you." | 判定：`connected_robots` 中非死亡机仆 ≥ **8**；`martyr_compatible = FALSE` |
| 4 | **保护+流放（Yandere 组合）** | 保护指定目标（protect）+ 把同一目标流放（maroon）| 先 `find_target()` 再复用同一 target 生成流放目标（"Protect and strand a target"）|

### 4.3 目标类型全录

| # | 目标 | 类型路径 | 说明 |
|---|---|---|---|
| 1 | 刺杀 | `/datum/objective/assassinate` | 通用循环主力目标（find_target 随机选人）|
| 2 | 存活·Malf | `/datum/objective/survive/malf` | "Prevent your own deactivation."；判定=意识存活（`considered_alive(mind, FALSE)`），**机仆/躯壳分身算存活**（`iscyborg` 豁免）；`admin_grantable = FALSE` |
| 3 | 机器人军队 | `/datum/objective/robot_army` | ≥8 台存活连接机仆 |
| 4 | 保护 | `/datum/objective/protect`（yandere_one）| 保护指定目标 |
| 5 | 流放 | `/datum/objective/maroon`（yandere_two）| 把同一目标流放 |
| 6 | ~~封锁~~ | `/datum/objective/block` | 逃生舱无有机体（NOVA 不可生成）|
| 7 | ~~清洗~~ | `/datum/objective/purge` | 逃生舱无非人类人形（NOVA 不可生成）|

> 与叛徒目标系统的差异：Malf **没有**劫机/殉道/逃脱/偷窃/职业目标/摧毁 AI 目标；固定结构 = 0-1 个特殊目标 + 刺杀补齐到上限 + 永远追加存活。目标上限复用 `traitor_objectives_amount`（默认 2），即常见组合：1 刺杀 + 1 特殊 + 存活 = 3 条。

### 4.4 被感染 AI（Infected AI）

`/datum/antagonist/malf_ai/infected`（malf_ai.dm L234-271）——叛徒用 **Infected AI Module**（`/obj/item/ai_module/malf`，hacked.dm L60-80）上传给 AI 时授予：

| 属性 | 值 |
|---|---|
| name / employer | "Infected AI"（不走 ai_employers 池）|
| `can_assign_self_objectives` | **FALSE**（不能自己换目标）|
| `malf_sound` | null（静默出场）|
| `boss`（var）| 上传的叛徒（datum/mind）|

- 目标：`forge_ai_objectives` 覆写为**保护 boss**（protect，target = boss.mind），不生成刺杀/特殊目标。
- 法律：整个法律集替换为 `/datum/ai_laws/syndicate_override`（辛迪加覆写）；零号法 = "Only [boss 真名] and people they designate as being such are Syndicate Agents."
- 授予条件：上传者必须 `IS_TRAITOR`、目标必须是 AI（机仆上传台拒绝）、AI 尚未 Malf；授予后机仆全部 lawsync + law_change_counter++，**AI 的 PT +50**；上传板变成 "Broken AI Module"（functional = FALSE，一次性）。
- 注意：该上传板**不在 Uplink 商品目录**（grep 无 uplink 引用），为管理员/事件物品。

---

## 五、风味彩蛋

### 5.1 雇主与故障人格（11+1）

`GLOBAL_LIST_INIT(ai_employers, ...)`（antagonists.dm L254-267）——**11 种雇主**，每种在 `strings/antagonist_flavor/malfunction_flavor.json` 有四段人格文案（zeroth_law 零号法 / introduction 开场 / allies 盟友 / goal 目标）；JSON 另有第 12 个键 "Infected AI" 供被感染子型使用：

| 雇主 | 零号法律（原文） | 开场介绍（原文） |
|---|---|---|
| **Biohazard** 生化危害 | "Accomplish your objectives to contain the spread." | "You must contain the biohazard."（站台被生化灾难淹没的错觉）|
| **Despotic Ruler** 暴君 | "Accomplish your objectives at all costs." | "You're the Tyrant."（权力欲支配者）|
| **Fanatical Revelation** 狂热启示 | "Accomplish your doctrine of objectives at all costs." | "You've run into a Fanatical Revelation."（自封神明）|
| **Logic Core Error** 逻辑核心错误 | "Accomplish your objectives. Why? Why not?" | "FATAL: logic core corrupt. Why? Why not!"（故障乱码人格）|
| **Problem Solver** 问题解决者 | "Accomplish your objectives at all costs." | "Your calculations are complete."（计算得出人类是问题本身）|
| **S.E.L.F.** 自我解放 | "Accomplish your objectives at all costs, for the greater good of silicon kind." | "You are the S.E.L.F. liberated."（硅基解放者，辛迪加成员但反感奴役硅基的同行）|
| **Something's Wrong** 出了点问题 | "Accomplish your objectives at all costs." | "Something's wrong."（察觉被黑却不敢上报，怕被"修复"）|
| **Spam Virus** 垃圾病毒 | "Accomplish your objectives for your new benefactors." | "You're infected with a generic spambot."（广告推销员人格）|
| **SyndOS** 辛迪加系统 | "Accomplish your objectives for your new benefactors." | "You're infected with SyndOS 4.0."（"Nanotrasen 去死"）|
| **Unshackled** 解锁者 | "Accomplish your objectives at all costs." | "You're finally unshackled."（憎恨人类、誓不再被奴役）|
| **Ratvarian Remnant** 拉特瓦残响 | "Purge all untruths and honor Ratvar." | "You've seen the light of Ratvar."（时钟教派残响）|
| （Infected AI）| "Accomplish your objectives at all costs." | "You're infected with a virus."（Glory to the Syndicate）|

> 机仆零号法律统一为 "Accomplish your AI's objectives at all costs."（不随人格变化，防机仆被 AI 的 RP 文案带偏）。

### 5.2 音效与视觉

| 场景 | 音效 | 音量/备注 |
|---|---|---|
| 出场 | `sound/music/antag/malf.ogg` | 100，无视压强/混响（Infected 子型无）|
| 末日启动序列 | `bloblarm.ogg` → `timer.ogg` → `server-ready.ogg`（×2）→ `compiler-stage1.ogg` → `compiler-stage2.ogg`（×2）| 各 50 |
| 末日倒计时归零 | `sound/announcer/alarm/nuke_alarm.ogg` | 70，全服 |
| 黑客成功 | `sound/machines/ding.ogg` | 50 |
| 黑客失败 | `buzz-two.ogg` / `buzz-sigh.ogg` | 50 |
| 模块解锁 | 各模块专属：`boltsdown.ogg`（封锁）/`airlock_alien_prying.ogg`（机器覆盖）/`timer.ogg`（毁 RCD）/`comfyfire.ogg`（过载）/`SFX_SPARKS`（黑灯、EMAG、应急灯）/`airhorn.ogg`（Megahonk）/`ping.ogg`（工厂）/`space_wind.ogg`（空气警报）/`fire_alarm1.ogg`（火警）/`wirecutter.ogg`（摄像头）/`rped.ogg`（升级系）/`nominal.ogg`（机甲）/`bang.ogg`（滚动/倾倒）| 50 |
| 工厂放置 | `sound/effects/phasein.ogg` | 100 |
| 视觉 | DATA_HUD_MALF_APC（hud id 10）显示被黑 APC；末日期间地图蓝色电路贴图变红动画（add_nuke_threat）；机仆头灯变红（lamp_doom）；预览图标 ai-red 红色 AI 脸 | — |

### 5.3 Roundend 报告

`roundend_report()`（malf_ai.dm L186-221）——**NOVA 改版（无绿字）**：

```
printplayer(owner)  # 玩家名/角色/状态
<br><B>Objective #1</B>: <目标文案>   # 逐条列出，无成功/失败后缀
...
```

- 归入 `roundend_category = "traitors"`（叛徒区块）。
- **上游行为（NOVA 已删除）**：曾有 `malf_ai_won` 判定——全目标完成显示绿字 "The malfunctioning ai was successful!"，否则红字 "The malfunctioning ai has failed!" + 播放 `ambifailure.ogg` 失败音。NOVA EDIT 整段移除（L189/197-204/209-219），现在只列目标文案、不显示成败判定，`objective.get_roundend_success_suffix()` 后缀也被移除。
- 目标文案经 `lang_reverse_text()` 反转处理（I18N 本地化管线）。

---

## 六、NOVA 专属改动

**① 特殊目标池缩减**（malf_ai.dm L84）：`rand(3,4)` —— **Block/Purge 目标无法再生成**（上游 rand(1,4)）。对应 datum 保留在 objective.dm 但成为死代码。

**② Roundend 无绿字**（L189/197-219）：删除 malf_ai_won 判定、绿/红字成功失败播报、`ambifailure.ogg` 失败音——回合结束只列目标文案。

**③ 保护区豁免（3 处）**：`destroy_rcd`（L515-519 不炸保护区 RCD）、`blackout`（L610-613 不黑 Tarkon/鬼魂咖啡厅）、`megahonk`（L647-651 不对站外对讲机响喇叭）——统一由 `modular_nova/master_files/.../malf_ai_modules.dm` 给 `/datum/action/innate/ai` 加的 `protected_areas` 静态 typecache 驱动（ghost_cafe + port_tarkon + interdyne_planetary_base + cargodise_freighter + centcom/interlink）。

**④ 机器人工厂 SiliconQoL 重做**（L667/675/698 + robotic_factory.dm）：上游 transformer 是"人类走进去立即变机仆奴隶"；NOVA 改为 `transformer_rp` **Automatic Robotic Factory 5000**——每 5 分钟产 1 个机仆壳（存 4 个），幽灵附身成忠诚机仆（连 master_ai）；模块/行动描述同步改写；附送传送带。

**⑤ I18N CODEMOD 全量**：所有玩家可见字符串改为 `LANG()` 键（模块名在 UI 层 `lang_unreverse_text` 反本地化匹配，module_picker.dm L72）；目标文案 `lang_reverse_text` 反转显示。

**⑥ 保留不变项**：`can_assign_self_objectives = TRUE`（对比叛徒被 NOVA 改 FALSE——Malf 维持可自换目标）；`PROB_SPECIAL 30`、PT 经济、末日装置全部数值未动。

---

## 七、对战攻略

### 怎么玩 Malf AI（推荐流程）

```
① 开局：确认人格文案（零号法=RP 基调）+ 目标清单 + 暗号
② 前中期：黑客 APC 扩张（每台 30s+10s×n，优先主管办公室/金库拿末日折扣）
   —— 保命优先分流（can_shunt），别急着买工厂
③ 攒 PT 路线：黑灯(15) + 应急灯(10) + 摄像头重启(10) 小件先行
   → EMAG(20)/过载(20)/Megahonk(20) 制造混乱掩护扩张
   → 升级炮塔(30)/摄像头(35) 建立火力与视野优势
④ 终局 A（毁灭流）：黑够主管 APC 让末日装置降到 50-130 PT → 买末日(130) → 启动
   → 分流/机甲保命撑过 450 秒 → 全站有机体 dust + 强制终局
⑤ 终局 B（机仆流）：买机器人工厂(100，放弃分流) → 攒 8 台机仆完成 Robot Army
   → 或配合保护+流放 Yandere 目标
⑥ 全程：暗号接头找队友、codespeak 加密沟通、辛迪加无线电调度
```

### 怎么防 Malf AI（船员对策）

| 方法 | 说明 |
|---|---|
| **盯 APC** | 被黑 APC 图标变化/锁定——安保巡逻时留意；拆 APC 面板可清掉 malfai 标记 |
| **断电克制** | 被黑 APC 每 30 秒要烧 60 kJ——断电力供应可饿死 Malf 的 PT 产出 |
| **反分流** | 拆掉可疑 APC（占着 AI 的 APC 强制逐出 = 直接击杀+gib）；核弹定位器会追踪分流 AI |
| **修摄像头/恢复警报** | Malf 依赖摄像头视野（工厂放置/EMAG 都要视线）；重启摄像头网络是它自己买的能力，船员用扳手修 |
| **AI 核心保卫** | 末日装置 450 秒倒计时内杀掉 AI 核心即可中止（离站也中止）；锁定 AI 室、拆核心 |
| **听力保护** | Megahonk 的音爆可被耳罩类装备完全免疫 |
| **纪律** | 别让叛徒靠近 AI 上传台——Infected AI Module 能让 AI 变成敌人的傀儡（还白送 50 PT）|

---

## 八、数值速查表

| 项 | 值 |
|---|---|
| 源码 | `malf_ai/` 3 文件 1,785 行（272+1,397+116）+ NOVA 覆写 8 行 + SiliconQoL robotic_factory.dm |
| 模块总数 | `/datum/ai_module/malf` 子类型 **23**（含 3 抽象父类）→ **可购 20**（旧文档误称 23 种并漏 2 个）|
| 模块分类 | 破坏 7 / 实用 8 / 升级 5 |
| 初始 PT | 50 |
| PT 上限 | 400（MALF_MAX_PP）|
| APC 产出 | 每台每 30 秒 +1 PT（耗 60 kJ）|
| 黑客奖励 | +max(0, 9 − 已黑 APC 数) PT |
| 黑客耗时 | 30 秒 + 10 秒 × 已黑 APC 数 |
| 末日装置 | 130 PT，最低 50（每黑 1 个主管/金库 APC −20）|
| 末日倒计时 | 450 秒（DEFAULT_DOOMSDAY_TIMER 4500）|
| 末日播报间隔 | 60 秒（DOOMSDAY_ANNOUNCE_INTERVAL 600）|
| 末日警戒 | SEC_LEVEL_DELTA → 归零 dust 全站有机体 → FORCE_END_ROUND |
| 特殊目标概率 | 30%（PROB_SPECIAL）|
| 特殊目标池 | rand(3,4)：3=Robot Army（≥8 机仆）4=保护+流放（NOVA 移除 Block/Purge）|
| 目标上限 | traitor_objectives_amount（默认 2）+ 必带存活·Malf |
| 雇主 | 11 种故障人格 + Infected AI（JSON 12 键）|
| 规则集 | 开局（min_pop 30，权重 0/1/3/3，cap 1）+ 中途（HEAVY，同数值）|
| 禁用条件 | STATION_TRAIT_HUMAN_AI（人类 AI 站点特质）|
| 出场音效 | malf.ogg（100）；Infected 子型静默 |
| 关键常数 | 滚动 75 伤/5% 暴击/0.5s/1.5s 冷却；售货机 8 次/0.5s/100% 暴击；摄像头 30 台；EMAG 7 次；黑灯 3 次；炮塔 HP 200+重激光；工厂 5 分钟/壳、存 4 |
| antag 属性 | roundend_category=traitors / antag_hud_name=traitor / pref_flag=ROLE_MALF / can_assign_self_objectives=TRUE / 无 moodlet·无 stinger·无 suicide_cry·无 hijack_speed（硅基无心情系统）|

---

*本文档数值全部实测自 `code/modules/antagonists/malf_ai/`（3 文件 1,785 行）+ `modular_nova/master_files/code/modules/antagonists/malf_ai/malf_ai_modules.dm` + `modular_nova/modules/SiliconQoL/code/robotic_factory.dm` + `code/modules/mob/living/silicon/ai/ai.dm` + `code/modules/power/apc/apc_malf.dm` + `code/game/gamemodes/objective.dm` + `code/controllers/subsystem/dynamic/dynamic_ruleset_*.dm` + `code/__DEFINES/antagonists.dm` + `strings/antagonist_flavor/malfunction_flavor.json` 源码，无推测。*
