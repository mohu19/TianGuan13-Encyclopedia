# TianGuan13 弹匣炮塔与车载武器百科

> **项目**: TianGuan13（NovaSector 分支）
> **代码**: `modular_nova/modules/magfed_turret/`（1,454 行，6 个代码文件）+ `modular_nova/modules/mounted_machine_gun/`（501 行，2 个代码文件）
> **范围**: **弹匣供弹炮塔**（Magazine-Fed Turrets：组装 / 设计器 / 生成器 / 框架 / 各类炮塔）+ **车载机枪**（Mounted Machine Gun：T90 重机枪 / .50 BMG 弹药盒）
> **关联文档**: 弹药（c35sol / c40sol / miecz / s12gauge / m12g）联动《Nova 特色模块》；炮塔声音联动 `modular_weapons` 模块。

---

## 目录（Table of Contents）

- [第一卷 · 弹匣供弹炮塔（Mag-Fed Turrets）](#第一卷弹匣供弹炮塔mag-fed-turrets)
  - [1.1 模块总览](#11-模块总览)
  - [1.2 炮塔框架 turret_framework.dm](#12-炮塔框架-turret_frameworkdm)
    - [1.2.1 定义与标志（Defines & Flags）](#121-定义与标志defines--flags)
    - [1.2.2 炮塔套件工具箱（Turret Kit Toolbox）](#122-炮塔套件工具箱turret-kit-toolbox)
    - [1.2.3 目标指示器（Target Designator）](#123-目标指示器target-designator)
    - [1.2.4 弹匣炮塔本体（Mag-Fed Turret）](#124-弹匣炮塔本体mag-fed-turret)
    - [1.2.5 供弹与射击流程（Chambering & Firing）](#125-供弹与射击流程chambering--firing)
    - [1.2.6 操作与互动（Operations & Interactions）](#126-操作与互动operations--interactions)
  - [1.3 组装流程 assembly.dm](#13-组装流程-assemblydm)
  - [1.4 生成器 spawners.dm](#14-生成器-spawnersdm)
  - [1.5 设计器变体 designators.dm](#15-设计器变体-designatorsdm)
  - [1.6 炮塔类型全录 turrets/](#16-炮塔类型全录-turrets)
    - [1.6.1 Cargo · 玩具炮塔（cargo.dm）](#161-cargo--玩具炮塔cargodm)
    - [1.6.2 废墟 · 前哨炮塔 Outpost（ruins.dm）](#162-废墟--前哨炮塔-outpostruinsdm)
    - [1.6.3 废墟 · 殖民者炮塔 Colonist](#163-废墟--殖民者炮塔-colonist)
    - [1.6.4 废墟 · 蜘蛛炮塔 Spider](#164-废墟--蜘蛛炮塔-spider)
    - [1.6.5 废墟 · 双牙炮塔 Twin-Fang](#165-废墟--双牙炮塔-twin-fang)
    - [1.6.6 废墟 · 尘暴炮塔 Duster](#166-废墟--尘暴炮塔-duster)
- [第二卷 · 车载机枪（Mounted Machine Gun）](#第二卷车载机枪mounted-machine-gun)
  - [2.1 模块总览](#21-模块总览)
  - [2.2 .50 BMG 弹药盒 ammobox.dm](#22-50-bmg-弹药盒-ammoboxdm)
  - [2.3 T90 车载机枪 mounted_machine_gun.dm](#23-t90-车载机枪-mounted_machine_gundm)
    - [2.3.1 基本属性](#231-基本属性)
    - [2.3.2 乘员绑定与视角控制](#232-乘员绑定与视角控制)
    - [2.3.3 射击机制](#233-射击机制)
    - [2.3.4 枪管过热机制](#234-枪管过热机制)
    - [2.3.5 弹药盒装卸](#235-弹药盒装卸)
    - [2.3.6 维护与拆装](#236-维护与拆装)
    - [2.3.7 折叠形态与部署](#237-折叠形态与部署)
    - [2.3.8 水冷联动](#238-水冷联动)
- [附录 · 代码路径索引](#附录代码路径索引)

---

# 第一卷 · 弹匣供弹炮塔（Mag-Fed Turrets）

**代码**: `modular_nova/modules/magfed_turret/code/`（1,454 行，6 文件）

| 文件 | 行数 | 职责 |
|---|---|---|
| `assembly.dm` | 197 | 炮塔组装流程（turret_assembly 状态机） |
| `designators.dm` | 14 | 设计器变体（Shot Caller） |
| `spawners.dm` | 49 | 随机生成器（5 种） |
| `turret_framework.dm` | 935 | 框架：工具箱 / 设计器 / 炮塔本体 / 射击 / 操作 |
| `turrets/cargo.dm` | 47 | Cargo 可购玩具炮塔 |
| `turrets/ruins.dm` | 212 | 废墟炮塔（Outpost / Colonist / Spider / Twin-Fang / Duster） |

> 设计意图（readme）：弹匣供弹炮塔用于太空废墟的临时威胁应对（废墟清理或幽灵角色），配套目标指示器用于操控炮塔。`cargo.dm` 为可通过 Cargo 获得的型号；`ruins.dm` 为太空 / 拉瓦兰 / 冰月等区域或幽灵角色废墟中的型号。

## 1.1 模块总览

| 能力 | 说明 |
|---|---|
| **弹种特效** | 龙息弹（Dragonsbreath）、霰弹弹幕（Pellet clouds）、可回收泡沫镖（Recyclability） |
| **点射 Burst-fire** | 可模块化点射速率与齐射数（可选） |
| **无工具部署/回收** | Tool-less deployment and retraction（可选） |
| **投掷部署** | 扔出后短暂延迟自动展开，不可像手雷一样"烹饪"（可选） |
| **目标评估三模式** | 不射人类 / 无差别射击 / 按炮塔标志（flags）射击 |
| **弹匣供弹** | 用弹匣攻击炮塔即可补弹 |
| **脆弱结构 Fragile** | 非拆解破坏时碎裂成组装件而非掉落工具箱 |
| **设计器联动** | 通过目标指示器链接并强制指定目标（原型阶段） |

## 1.2 炮塔框架 turret_framework.dm

### 1.2.1 定义与标志（Defines & Flags）

**射击模式**：

| 定义 | 值 | 含义 |
|---|---|---|
| `TURRET_STUN` | 0 | 眩晕模式（保留，本模块未用） |
| `TURRET_LETHAL` | 1 | 致命模式（保留，本模块未用） |

**炮塔标志位 turret_flags**（按位）：

| 定义 | 值 | 含义 |
|---|---|---|
| `TURRET_FLAG_SHOOT_ALL_REACT` | 1<<0 | 被激怒后射击附近人员（有安保权限者除外） |
| `TURRET_FLAG_AUTH_WEAPONS` | 1<<1 | 射击持有未授权武器者 |
| `TURRET_FLAG_SHOOT_CRIMINALS` | 1<<2 | 射击通缉犯 |
| `TURRET_FLAG_SHOOT_ALL` | 1<<3 | 射击附近人员（有安保权限者除外） |
| `TURRET_FLAG_SHOOT_ANOMALOUS` | 1<<4 | 射击未知生命体（异形等） |
| `TURRET_FLAG_SHOOT_UNSHIELDED` | 1<<5 | 射击未植入心灵护盾且非部门主管者 |
| `TURRET_FLAG_SHOOT_BORGS` | 1<<6 | 射击机器人（cyborg） |
| `TURRET_FLAG_SHOOT_HEADS` | 1<<7 | 射击部门主管 |

**目标评估模式 target_assessment**：

| 定义 | 值 | 含义 |
|---|---|---|
| `TURRET_FLAG_OBEY_FLAGS` | 2 | 按炮塔标志行事 |
| `TURRET_FLAG_SHOOT_NOONE` | 3 | 不射击任何玩家型生物 |
| `TURRET_FLAG_SHOOT_EVERYONE` | 4 | 射击所有玩家型生物 |

**威胁等级 Threat Levels**：

| 定义 | 值 | 含义 |
|---|---|---|
| `TURRET_THREAT_PASSIVE` | 0 | 无威胁 |
| `TURRET_THREAT_LOW` | 2 | 低威胁 |
| `TURRET_THREAT_MEDIUM` | 4 | 中威胁 |
| `TURRET_THREAT_HIGH` | 6 | 高威胁 |
| `TURRET_THREAT_SEVERE` | 8 | 严重威胁 |
| `TURRET_THREAT_PRIORITY` | 10 | 优先目标 |

### 1.2.2 炮塔套件工具箱（Turret Kit Toolbox）

`/obj/item/storage/toolbox/emergency/turret/mag_fed` —— 弹匣炮塔套件"mag-fed turret kit"，部署后即成为炮塔本体。

| 属性 | 默认值 | 说明 |
|---|---|---|
| `has_latches` | FALSE | 无锁扣 |
| `setting_change` | TRUE | 是否可调整炮塔设置（拆解/制造后默认可调） |
| `turret_safety` | FALSE | 部署后是否忽略人类（安全模式） |
| `flags_on` | FALSE | 部署后是否按炮塔标志行事 |
| `easy_deploy` | FALSE | 是否无需扳手即可部署（收起仍需扳手） |
| `easy_deploy_timer` | 2 秒 | 简易部署耗时 |
| `quick_deployable` | FALSE | 是否可投掷部署 |
| `quick_deploy_timer` | 1 秒 | 投掷部署耗时 |
| `turret_type` | `.../mag_fed` | 部署出的炮塔类型 |
| `mag_slots` | 2 | 可容纳弹匣数 |
| `mag_types_allowed` | `c35sol_pistol` | 允许的弹匣类型列表 |

- **Initialize**：`max_specific_storage = WEIGHT_CLASS_NORMAL`、`max_slots = mag_slots`、`can_hold = typecacheof(mag_types_allowed)`；有 `greyscale_config` 时挂 GAGS 可换色元素。
- **Examine**：显示安全模式 ON/OFF（绿/红）、目标模式"OBEYING LAWS / FREE TARGETING"、是否可简易部署、设置可否调整。
- **`get_mag(keep)`**：从箱内取出最后放入的弹匣；`keep=TRUE` 时强制插回（用于检查）。
- **`set_faction(turret, user)`**：若用户不在炮塔阵营中，将用户阵营及盟友复制给炮塔。
- **item_interaction 设置**：
  - **螺丝刀**（2 秒）：切换 `turret_safety`（安全模式开/关）。
  - **多功能工具**（2 秒）：切换 `flags_on`（按标志行事开/关）。
  - **扳手 + 战斗模式**（2 秒）：部署炮塔到当前格（`deploy_turret`）。
- **attack_self（激活）**：仅 `easy_deploy=TRUE` 时可用——部署到面前一格（`get_step(user, user.dir)`），格子被阻挡则提示失败；耗时 `easy_deploy_timer`。
- **`deploy_turret(user, chosen_spot)`**：生成 `turret_type` 炮塔；设置阵营；`turret.mag_box = WEAKREF(src)`（工具箱成为炮塔的弹匣容器）；安全模式→`target_assessment = SHOOT_NOONE`；标志模式→`OBEY_FLAGS`；工具箱 forceMove 进炮塔内部；`setState(TRUE)` 升起；传递 GAGS 颜色。
- **throw_impact**：`quick_deployable=TRUE` 时，落地后 `quick_deploy_timer` 延迟自动部署（投掷者可为 null，防运行时错误）。

**pre_filled 变体**：`/obj/item/storage/toolbox/emergency/turret/mag_fed/pre_filled` 生成时填入 **2× c35sol_pistol 弹匣**。

### 1.2.3 目标指示器（Target Designator）

`/obj/item/target_designator` —— "Turret Target Designator"炮塔目标指示器：覆盖炮塔目标软件，指定一个目标或把某人标记为"友军"。

| 属性 | 默认值 | 说明 |
|---|---|---|
| `w_class` | WEIGHT_CLASS_SMALL | 小型 |
| `obj_flags` | CONDUCTS_ELECTRICITY | 导电 |
| `item_flags` | NOBLUDGEON | 不能当钝器 |
| `slot_flags` | ITEM_SLOT_BELT | 可挂腰带 |
| `throwforce` | 0 / 速度 3 / 距离 7 | 投掷属性 |
| `custom_materials` | 铁×5、玻璃×2、等离子×1、金×1、钛×0.5、银×0.5（片材量） | 材料 |
| `scan_range` | 10 | 扫描距离 |
| `turret_limit` | 3 | 可链接炮塔上限 |
| `linked_turrets` | 列表 | 已链接炮塔 |
| `acquired_target` | weakref | 当前指定目标 |
| `acquisition_duration` | 5 秒 | 目标锁定持续时长 |
| `target_all` | TRUE | 是否射击玩家型生物 |
| `follow_flags` | FALSE | 是否按炮塔标志行事（覆盖其他模式） |

- **attack_self**：切换 `target_all` 并同步炮塔。
- **ShiftClick**：切换 `follow_flags` 并同步炮塔。
- **主手远程点击原子（ranged_interact_with_atom）**：目标必须在 `scan_range` 视野内；不可点击已链接炮塔；已有目标时阻止；`designate_enemy` 指定敌人，`acquisition_duration` 后 `clear_target` 清除。
- **副手远程点击（secondary）**：点击活体生物时对所有已链接炮塔执行 `toggle_ally`（友军标记）。
- **`designate_enemy(target, user)`**：记录 `acquired_target`，所有已链接炮塔执行 `override_target`。
- **`clear_target(user)`**：清空目标，所有已链接炮塔执行 `clear_override`。
- **`sync_turrets()`**：统一所有炮塔的 `target_assessment`——
  - `target_all=TRUE` 且 `follow_flags=FALSE` → `SHOOT_EVERYONE`（"unrestricting targeting!"）
  - `follow_flags=TRUE` → `OBEY_FLAGS`（"obeying laws!"）
  - 两者皆否 → `SHOOT_NOONE`（"restricting targeting!"）
  - 随后 `setState(TRUE)` 刷新。

### 1.2.4 弹匣炮塔本体（Mag-Fed Turret）

`/obj/machinery/porta_turret/syndicate/toolbox/mag_fed` —— "Mag-fed Turret"基础弹匣炮塔。

| 属性 | 默认值 | 说明 |
|---|---|---|
| `integrity_failure` / `max_integrity` | 0 / 200 | 耐久 200，不因故障停机 |
| `shot_delay` | 2 秒 | 射击间隔（影响点射） |
| `uses_stored` / `stored_gun` | FALSE / null | 不用内置武器（改用弹匣） |
| 射击音效 | pistol shot.ogg（两种） | 默认枪声 |
| `subsystem_type` | 弹道处理子系统 | 参与弹道处理 |
| `turret_flags` | `SHOOT_ALL \| SHOOT_ANOMALOUS` | 射击所有人+异形 |
| `ignore_faction` | TRUE | 忽略阵营（子弹继承炮塔阵营） |
| `armor_type` | `/datum/armor/mobile_turret` | 见下表 |
| `req_access` | 空 | 用阵营/盟友系统代替权限 |
| `faction` | `FACTION_TURRET` | 默认炮塔阵营 |

**护甲（比辛迪加炮塔弱）**：近战 40 / 子弹 30 / 激光 40 / 能量 30 / 炸弹 20 / 火焰 80 / 酸 80。

**可调战斗变量**：

| 变量 | 默认值 | 说明 |
|---|---|---|
| `faction_targeting` | TRUE | 是否按阵营评估（否则只看用户） |
| `turret_damage_multiplier` | 1 | 子弹伤害倍率（0.5=半伤，1.5=+50%） |
| `turret_wound_bonus` | 0 | 伤口加成（10=更易造成伤口） |
| `burst_fire` | FALSE | 是否点射 |
| `burst_volley` | 3 | 点射齐射数 |
| `volley_count` | 0 | 当前齐射进度 |
| `burst_delay` | 2 秒 | 点射间隔 |
| `burst_target` / `last_burst` | — | 点射目标 weakref / 上次点射时间 |
| `quick_retract` | FALSE | 可否无工具回收 |
| `smart_retract` | TRUE | 快速回收是否需要阵营标记 |
| `retract_timer` | 1 秒 | 回收耗时 |
| `adjustable_magwell` | TRUE | 可否装多种弹药（主要影响音效） |
| `mag_drop_collect` | FALSE | 空弹匣自动收回箱内（预留） |
| `target_override` | weakref | 手动指定目标（立即覆盖） |
| `target_assessment` | `SHOOT_EVERYONE` | 目标评估模式 |
| `claptrap_moment` | TRUE | 是否静音（测试用） |
| `casing_ejector` | TRUE | 是否抛壳 |
| `mag_box_type` | `pre_filled` | 地图生成时自带的弹匣箱 |
| `staminaloss` / `combat_mode` | 0 / TRUE | 防运行时错误 + 允许卧姿射击 |
| `mag_box` / `magazine_ref` / `chambered` | weakref | 弹匣箱 / 膛内弹匣 / 已上膛子弹 |
| `linkage` | weakref | 已链接的目标指示器 |
| `fragile` | FALSE | 破坏时是否碎裂成框架 |
| `receiver_check` | 70 | 碎裂时掉落 receiver 的几率 % |
| `sensor_check` | 30 | 碎裂时掉落 prox_sensor 的几率 % |
| `turret_frame` | — | 碎裂成的组装件类型 |

**Initialize**：无 `mag_box` 时自动生成 `mag_box_type` 作为弹匣箱；`!raised` 时异步 `popUp` 升起；注册右键菜单。

**外观**：`update_appearance` 在底层叠加 `[base_icon_state]_frame` 底框。

**右键菜单（add_context）**——持有对应物品且 `in_faction(user)`：
- 扳手 + 左键 = "Repair Turret" 维修；扳手 + 右键 = "Retract Turret" 回收
- 弹匣 + 左键 = "Feed Turret" 供弹
- 目标指示器 + 左键 = "Link Turret" 链接；+ 右键 = "Unlink Turret" 解除链接
- `quick_retract=TRUE` 时（智能模式下需同阵营）：Alt+右键 = "Retract Turret"

**Examine**：移除原版"用战斗扳手维修/折叠"提示；同阵营/盟友可见：耐久、供弹、弹匣、点射、回收、碎裂、设计器链接等信息。

### 1.2.5 供弹与射击流程（Chambering & Firing）

**`handle_chamber(chamber_next_round)`**（供弹主流程）：无弹匣→`load_mag()`；弹匣空→`handle_mag()`（换弹）；膛内有弹→`eject_cartridge()` 抛壳；需要且 `max_ammo > 1`→`chamber_round()` 上膛。

**`chamber_round()`**：从弹匣取一发装入弹膛（`mag.get_round()`→forceMove 进炮塔），播放 `bolt_rack.ogg` 拉栓音；`replace_new_round` 时回填同型弹（边缘情况预留）。

**`handle_mag()`**：空弹匣处理——`mag_drop_collect=TRUE` 时插回工具箱，否则丢到地上；解除移动信号；随后 `load_mag()`，播放 `chunkyrack.ogg` 换弹音。

**`load_mag()`**：从工具箱 `get_mag()` 取弹匣；无弹匣时提示"需要弹药"并 `toggle_on(FALSE)` 关闭防止空转；成功则弹匣 forceMove 进炮塔。

**`eject_cartridge()`**：有膛弹且 `casing_ejector=TRUE` 时抛壳落地、`bounce_away`、发 `COMSIG_CASING_EJECTED` 信号。

**`insert_mag(magaroni, guy)`**：部署状态下直接插入弹匣——类型必须在 `can_hold` 中，否则拒绝；插入后 `toggle_on(TRUE)` 重启。

**`process()`**（每帧）：有 `linkage` 时——先处理 `burst_target`（点射中，向点射目标 `trytoshootfucker`），再处理 `target_override`（强制目标射击）；两者皆无则走父类常规索敌。

**`assess_perp(perp)`**（威胁评估）：
- **EMAGGED** → 恒为 `TURRET_THREAT_PRIORITY`（10）。
- `SHOOT_EVERYONE` 模式 → 直接 10（不评估阵营/盟友内的人）。
- `SHOOT_NOONE` 模式 → 0（不会主动瞄准你，但挡在弹道上仍可能被击中）。
- `SHOOT_ALL`/`SHOOT_ALL_REACT` 且无权限 → 10。
- 不射主管标志（`SHOOT_HEADS` 未设）且目标为部门主管 → 0。
- `AUTH_WEAPONS`：手持枪/电击棒 +4；腰挂枪/电击棒 +2；变装 ID 除外。
- `SHOOT_CRIMINALS`：无记录或记录为"逮捕"（WANTED_ARREST）+4。
- `SHOOT_UNSHIELDED`：无 `TRAIT_MINDSHIELD` 特质 +4。

**`in_faction(target)`**：`faction_targeting=FALSE` 时只看盟友；否则快速阵营检查（含盟友列表）。

**`toggle_ally(target)`**：在盟友系统中添加/移除目标并提示。

**`target(target)`**（开火决策）：`burst_fire` 且无点射目标→`do_burst_fire` 开启点射；普通模式→转向目标并 `shootAt`。

**点射系统**：`do_burst_fire` 记录 `burst_target`、`volley_count = burst_volley`，受 `last_burst + burst_delay` 冷却约束；`end_burst` 清空点射目标并记录时间。`shootAt` 内每次射击 `volley_count -= 1`，归零即 `end_burst`。

**设计器强制目标**：`override_target` 把 `target_override` 设为目标，**射击间隔减半**（`shot_delay`/`burst_delay` ÷2），并提示"目标已被指定"；`clear_override` 恢复原速。

**索敌优化 `tryToShootAt`**：从候选列表按距离取最近活体（`get_closest_atom(/mob/living, ...)`）逐个尝试。

**`trytoshootfucker(weakref)`**：对单个指定目标（weakref）循环射击，目标消失则清空覆盖/点射目标。

**`shootAt(target)`**：无膛弹→先 `handle_chamber(TRUE)` 启动供弹；未升起不开火；非 EMAGGED 时受 `last_fired + shot_delay` 冷却；点射计数递减；转向目标；膛弹有弹头→`handle_firing`，否则重新供弹。

**`handle_firing(casing, target)`**：`ignore_faction=TRUE` 时把阵营/盟友从炮塔复制到子弹；伤害与耐力 ×`turret_damage_multiplier`；伤口/暴露伤口加成 +`turret_wound_bonus`；`fire_casing` 朝胸口射击；播放音效。

**`changeNext_move()`**：弹壳发射流程回调→自动重新供弹。

**`play_fire_sound(soundmaker)`**（按弹种切换枪声，`adjustable_magwell=TRUE` 时）：

| 弹壳类型 | 音效 |
|---|---|
| `c35sol`（.35 Sol 手枪弹） | `pistol_light.ogg` |
| `c585trappiste` | `pistol_heavy.ogg` |
| `c40sol`（.40 Sol 步枪弹） | `rifle_heavy.ogg` |
| `strilka310` | `battle_rifle.ogg` |
| `c27_54cesarzowa`（.27-54 切萨佐瓦） | `smg_light.ogg` |
| 其他 / `adjustable_magwell=FALSE` | 默认 `lethal_projectile_sound` |

### 1.2.6 操作与互动（Operations & Interactions）

**item_interaction**（左键）：
- 手持可容纳弹匣→1 秒 `do_after` 后 `insert_mag` 供弹（"正在供弹…"）。
- ID 卡→同阵营才放行。
- 同阵营 + 目标指示器→链接：`linkage = WEAKREF(controller)`，加入 `controller.linked_turrets`，注册 `COMSIG_QDELETING` 信号；指示器已满（`turret_limit`）或炮塔已被链接则拒绝。

**attackby_secondary**（右键）：
- 同阵营 + 目标指示器→解除链接（必须是同一个指示器）。
- 扳手→5 秒 `do_after` 后 `deconstruct(TRUE)` 拆解回收。

**wrench_act**（维修）：耐久不满时用扳手反复维修，每次 2 秒、修复 **25 耐久**，直至满。

**click_alt_secondary**（Alt+右键快速回收）：`quick_retract=TRUE` 且（非智能模式或同阵营）→1 秒 `do_after` 后拆解。

**attack_hand_secondary**（空手右键）：无膛弹时手动 `handle_chamber(TRUE)` 拉栓上膛。

**`on_qdeleted`**：设计器被删除时自动从 `linked_turrets` 移除该炮塔。

**on_deconstruction（拆解/破坏全流程）**：
1. 膛内子弹：有弹头则清空弹头；无弹匣时丢地，有弹匣时 `mag.give_round(casing)` 还弹回匣。
2. 弹匣：非拆解（被破坏）时丢地；拆解时插回工具箱。
3. 解除设计器链接并注销信号。
4. **fragile=TRUE 且被破坏（非拆解）**：把工具箱内物品全丢出，生成 `turret_frame` 组装件；`prob(receiver_check)=70%` 掉 receiver，`prob(sensor_check)=30%` 掉 prox_sensor；**伺服器必坏**（直接删除工具箱）；销毁计时器；`qdel(src)` + 机器人碎尸特效，**返回**。
5. 正常拆解：工具箱（含剩余弹匣）掉出，销毁计时器，`qdel(src)`。

## 1.3 组装流程 assembly.dm

`/obj/item/turret_assembly` —— "turret plate assembly"炮塔底板组装件。**8 步状态机**，需要模块化 receiver、伺服器（servo）、接近传感器（prox sensor）与工具施工。

| 步骤宏 | 值 | 描述（Examine 提示） |
|---|---|---|
| `TURRET_ASSEMBLY_START` | "start" | 缺少 **模块化 receiver** |
| `TURRET_ASSEMBLY_RECEIVER` | "receiver" | 连接螺栓**松动** |
| `TURRET_ASSEMBLY_SEC_1` | "secured_receiver" | 缺少 **servo 伺服器** |
| `TURRET_ASSEMBLY_SERVO` | "servo" | 主底盘**未固定** |
| `TURRET_ASSEMBLY_SEC_2` | "secured_servo" | 缺少 **proximity sensor 接近传感器** |
| `TURRET_ASSEMBLY_SENSOR` | "sensor" | 传感器**未固定** |
| `TURRET_ASSEMBLY_SEC_3` | "secured_sensor" | 支架螺栓**松动** |
| `TURRET_ASSEMBLY_WRAPUP` | "finished_assembly" | 电路板 CPU 需要**激活** |

**内置变量**：`receiver` / `sensor` / `servo`（物品引用）、`design`（产出炮塔套件类型，默认 outpost 套件）、`step`（当前步骤）。

**组装操作**（item_interaction / 工具 act，每步播放 `click.ogg`）：
- **装 receiver**：START 步放入 `/obj/item/weaponcrafting/receiver` → RECEIVER。
- **螺丝刀**：RECEIVER→SEC_1（上紧）；SEC_1→RECEIVER（松开）；SERVO→SEC_2；SEC_2→SERVO；SENSOR→SEC_3；SEC_3→SENSOR。
- **装伺服器**：SEC_1 步放入 `/obj/item/stock_parts/servo` → SERVO。
- **装传感器**：SEC_2 步放入 `/obj/item/assembly/prox_sensor` → SENSOR。
- **扳手**：SEC_3→WRAPUP（上紧支架）；WRAPUP→SEC_3（松开）。
- **多功能工具**：WRAPUP 步激活 CPU → 生成 `design` 类型套件放入手中，销毁组装件。
- **撬棍（拆解）**：RECEIVER→START（取出 receiver）；SERVO→SEC_1（取出 servo）；SENSOR→SEC_2（取出 sensor）。

**Destroy / Exited**：正确清理 receiver / servo / sensor 引用（防止悬空引用）。

**变体**：

| 组装件 | 名称 | 产出套件 |
|---|---|---|
| `/obj/item/turret_assembly` | turret plate assembly（基础） | outpost 套件 |
| `/obj/item/turret_assembly/twin_fang` | twin_fang plate assembly | spider/twin_fang 套件 |
| `/obj/item/turret_assembly/duster` | duster plate assembly | duster 套件 |

## 1.4 生成器 spawners.dm

| 生成器 | 名称 | 战利品表（权重） |
|---|---|---|
| `/obj/effect/spawner/random/magturret` | Random Magazine Turret 随机弹匣炮塔 | outpost/malf **80**；duster **20** |
| `/obj/effect/spawner/random/throwturret` | Random Throwable Turret 随机投掷炮塔 | spider **80**；spider/twin_fang **20** |
| `/obj/effect/spawner/random/turretkit` | Random Magazine Turret Kit 随机炮塔套件 | outpost/pre_filled **45**；duster/pre_filled **20**；spider/pre_filled **20**；spider/twin_fang/pre_filled **10**；toy **5** |
| `/obj/effect/spawner/random/throwturretkit` | Random Throwable Turret Kit 随机投掷套件 | spider/pre_filled **70**；spider/twin_fang/pre_filled **25**；toy **5** |
| `/obj/effect/spawner/random/turretassembly` | Random Turret Assembly 随机组装件 | turret_assembly **50**；twin_fang **20**；duster **30** |

## 1.5 设计器变体 designators.dm

| 属性 | 基础设计器 | **Shot Caller**（`/obj/item/target_designator/sniper`） |
|---|---|---|
| 名称 | Turret Target Designator | Shot Caller（狙击型） |
| 描述 | 简单目标指定系统 | 获取时间更短、炮塔上限 1 个，可标记更远目标 |
| `scan_range` | 10 | **15** |
| `turret_limit` | 3 | **1** |
| `acquisition_duration` | 5 秒 | **0.2 秒** |
| `target_all` | TRUE | **FALSE** |
| 图标 | designator.dmi "designator" | designator.dmi "shot_caller" |

## 1.6 炮塔类型全录 turrets/

### 1.6.1 Cargo · 玩具炮塔（cargo.dm）

**套件** `/obj/item/storage/toolbox/emergency/turret/mag_fed/toy` —— "toy turret kit"办公战争玩具炮塔：

| 属性 | 值 |
|---|---|
| `flags_1` | IS_PLAYER_COLORABLE_1（玩家可染色） |
| `w_class` | NORMAL |
| `greyscale_config` | `/datum/greyscale_config/turret/toolbox`，颜色 `#E0C14F#C67A4B` |
| `throw_speed` | 2 |
| `turret_type` | `.../mag_fed/toy` |
| `mag_slots` | 1 |
| `quick_deployable` / `quick_deploy_timer` | TRUE / **0.5 秒** |
| `easy_deploy` / `easy_deploy_timer` | TRUE / **0.5 秒** |
| `turret_safety` | FALSE |
| `mag_types_allowed` | `/obj/item/ammo_box/magazine/toy`（玩具弹匣） |

**pre_filled**：装入 **1× toy/smg 玩具冲锋枪弹匣**。

**炮塔** `/obj/machinery/porta_turret/syndicate/toolbox/mag_fed/toy` —— "Cubicle Point-Defense Turret"隔间点防御炮塔（发射泡沫飞镖）：

| 属性 | 值 |
|---|---|
| `max_integrity` | **10**（脆弱的玩具） |
| `base_icon_state` | "toy" |
| `greyscale_config` | `/datum/greyscale_config/turret`，颜色 `#E0C14F#C67A4B` |
| `quick_retract` / `retract_timer` | TRUE / 1 秒 |
| `shot_delay` | **0.5 秒**（射速快） |
| `faction_targeting` | **FALSE**（不看阵营） |
| `mag_box_type` | toy/pre_filled |

### 1.6.2 废墟 · 前哨炮塔 Outpost（ruins.dm）

**套件** `/obj/item/storage/toolbox/emergency/turret/mag_fed/outpost` —— "outpost defense turret kit"前哨防御炮塔套件：

| 属性 | 值 |
|---|---|
| `turret_type` | `.../mag_fed/outpost` |
| `mag_slots` | 2 |
| `mag_types_allowed` | `/obj/item/ammo_box/magazine/c40sol_rifle`（**.40 Sol 步枪弹**） |

**pre_filled**：2× c40sol_rifle 弹匣。

**炮塔** `/obj/machinery/porta_turret/syndicate/toolbox/mag_fed/outpost` —— "Outpost Point-Defense Turret"前哨点防御炮塔：

| 属性 | 值 |
|---|---|
| `max_integrity` | **120** |
| `shot_delay` | **1.5 秒** |
| `faction` | FACTION_TURRET |
| `fragile` / `turret_frame` | TRUE / `/obj/item/turret_assembly`（基础组装件） |
| `mag_box_type` | outpost/pre_filled |

**malf 变体** `/obj/machinery/.../outpost/malf` —— "Malfunctioning Outpost Turret"故障前哨炮塔：`faction = FACTION_MALF_TURRET`、`shot_delay = **1 秒**（射速更快）——用于太空废墟。

### 1.6.3 废墟 · 殖民者炮塔 Colonist

**套件** `/obj/item/storage/toolbox/emergency/turret/mag_fed/colonist` —— "colonist defense turret kit"殖民者防御炮塔套件（幽灵角色友好版前哨炮塔）：

| 属性 | 值 |
|---|---|
| `turret_type` | `.../mag_fed/colonist` |
| `mag_slots` | 2 |
| `easy_deploy` | **TRUE**（无需工具部署） |
| `turret_safety` | **TRUE**（默认不射人类） |
| `mag_types_allowed` | `c40sol_rifle`（.40 Sol） |

**pre_filled**：2× c40sol_rifle 弹匣。

**炮塔** `/obj/machinery/.../mag_fed/colonist` —— "Colonist Point-Defense Turret"殖民者点防御炮塔：

| 属性 | 值 |
|---|---|
| `max_integrity` | **150**（更耐打，只针对怪物） |
| `shot_delay` | 1.5 秒 |
| `quick_retract` | TRUE |
| `faction` | FACTION_TURRET |
| `mag_box_type` | colonist/pre_filled |

**malf 变体** `colonist/malf` —— "Malfunctioning Colonist Point-Defense Turret"：`FACTION_MALF_TURRET`。

### 1.6.4 废墟 · 蜘蛛炮塔 Spider

**套件** `/obj/item/storage/toolbox/emergency/turret/mag_fed/spider` —— "spider offensive turret capsule"蜘蛛进攻炮塔胶囊（投掷部署、真弹药）：

| 属性 | 值 |
|---|---|
| `throw_speed` | 2 |
| `w_class` | NORMAL |
| `quick_deployable` / `quick_deploy_timer` | TRUE / **1 秒** |
| `turret_type` | `.../mag_fed/spider` |
| `mag_slots` | 1 |
| `turret_safety` | TRUE |
| `mag_types_allowed` | `/obj/item/ammo_box/magazine/c35sol_pistol`（**.35 Sol 手枪弹**） |

**pre_filled**：1× c35sol_pistol 弹匣。

**炮塔** `/obj/machinery/.../mag_fed/spider` —— "Stinger Spider Turret"毒刺蜘蛛炮塔：

| 属性 | 值 |
|---|---|
| `max_integrity` | **80** |
| `shot_delay` | 1.5 秒 |
| `faction` | FACTION_TURRET |
| `mag_box_type` | spider/pre_filled |

**malf 变体** `spider/malf` —— "Malfunctioning Twin-fang Turret"（注意名称如此）：`FACTION_MALF_TURRET`；对应套件 `spider/malf` "odd spider turret kit"（闪光怪异，装 1× c35sol_pistol）。

### 1.6.5 废墟 · 双牙炮塔 Twin-Fang

**套件** `/obj/item/storage/toolbox/emergency/turret/mag_fed/spider/twin_fang` —— "twin-fang offensive turret capsule"双牙进攻炮塔胶囊（蜘蛛炮塔更凶的表亲，.27-54）：

| 属性 | 值 |
|---|---|
| `turret_type` | `.../spider/twin_fang` |
| `mag_slots` | 1 |
| `turret_safety` | **FALSE** |
| `mag_types_allowed` | `/obj/item/ammo_box/magazine/miecz`（**.27-54 Cesarzowa 弹匣**） |

**pre_filled**：1× miecz 弹匣。

**炮塔** `/obj/machinery/.../spider/twin_fang` —— "Twin-Fang Spider Turret"双牙蜘蛛炮塔：

| 属性 | 值 |
|---|---|
| `max_integrity` | **50**（更凶但更脆） |
| `fragile` / `turret_frame` | TRUE / `/obj/item/turret_assembly/twin_fang` |
| `quick_retract` | TRUE |
| `shot_delay` | **0.1 秒**（极快） |
| `burst_fire` / `burst_delay` / `burst_volley` | **TRUE** / **1.5 秒** / **2 连发** |
| `faction` | FACTION_TURRET |
| `mag_box_type` | twin_fang/pre_filled |

**malf 变体** `spider/twin_fang/malf` —— "Malfunctioning Twin-fang Turret"：`FACTION_MALF_TURRET`；套件 `spider/twin_fang/malf` "odd twinfang turret kit"（装 1× miecz）。

### 1.6.6 废墟 · 尘暴炮塔 Duster

**套件** `/obj/item/storage/toolbox/emergency/turret/mag_fed/duster` —— "duster emergent turret kit"尘暴应急炮塔套件（霰弹枪，警告语"请勿站在枪口前，友军伤害不算"）：

| 属性 | 值 |
|---|---|
| `easy_deploy` | TRUE |
| `turret_type` | `.../mag_fed/duster` |
| `mag_slots` | **3**（多数霰弹匣 ≤8 发，共 24 发） |
| `turret_safety` | FALSE |
| `mag_types_allowed` | `/obj/item/ammo_box/magazine/ammo_stack/s12gauge`（散装霰弹堆）+ `/obj/item/ammo_box/magazine/m12g`（M12 霰弹匣） |

**pre_filled**：**3× s12gauge/prefilled/buckshot 鹿弹堆**。

**炮塔** `/obj/machinery/.../mag_fed/duster` —— "Duster Emergent Turret"尘暴应急炮塔：

| 属性 | 值 |
|---|---|
| `max_integrity` | **100** |
| `fragile` / `turret_frame` | TRUE / `/obj/item/turret_assembly/duster` |
| `ignore_faction` | **FALSE**（霰弹弹幕无法智能绕友军） |
| `quick_retract` | TRUE |
| `shot_delay` | **2 秒** |
| `faction` | FACTION_TURRET |
| `mag_box_type` | duster/pre_filled |

**malf 变体** `duster/malf` —— "Malfunctioning Duster Turret"：`FACTION_MALF_TURRET`。

---

# 第二卷 · 车载机枪（Mounted Machine Gun）

**代码**: `modular_nova/modules/mounted_machine_gun/code/`（501 行，2 文件）

| 文件 | 行数 | 职责 |
|---|---|---|
| `ammobox.dm` | 26 | .50 BMG 弹药盒 / 弹壳 / 子弹 |
| `mounted_machine_gun.dm` | 475 | T90 车载机枪本体（射击 / 过热 / 乘员绑定 / 折叠） |

## 2.1 模块总览

T90 车载机枪是**高口径压制火力**武器：可捆绑（buckle）乘员操作、按住鼠标持续射击、带枪管过热机制（可水冷）、可折叠收放、弹药盒供弹。配套音效 6 个（50cal_box_01 / insert_ammobox / remove_ammobox / open_lid / close_lid / cock_bolt）。

**核心定义**：

| 定义 | 值 | 含义 |
|---|---|---|
| `BARREL_HEAT_THRESHOLD_LOW` | 50 | 枪管"热"状态阈值 |
| `BARREL_HEAT_THRESHOLD_HIGH` | 75 | 枪管"过热"状态阈值 |
| `REPAIR_WELDER_COST` | 10 | 每次焊修耗焊料量 |

## 2.2 .50 BMG 弹药盒 ammobox.dm

**弹药盒** `/obj/item/ammo_box/magazine/mmg_box` —— ".50 BMG ammo box"：

| 属性 | 值 |
|---|---|
| `multiple_sprites` | AMMO_BOX_FULL_EMPTY（满/空双图标） |
| `max_ammo` | **50 发** |
| `ammo_type` | `/obj/item/ammo_casing/b50cal` |
| `caliber` | `CALIBER_50BMG` |

**弹壳** `/obj/item/ammo_casing/b50cal` —— ".50 BMG bullet casing"：口径 .50 BMG，弹头类型 `/obj/projectile/bullet/c50cal`。

**子弹** `/obj/projectile/bullet/c50cal` —— ".50 BMG bullet"：

| 属性 | 值 |
|---|---|
| `damage` | **30** |
| `light_system` | OVERLAY_LIGHT（自身发光） |
| `light_range` / `light_power` / `light_color` | 1 / 1.4 / 柔和红光 COLOR_SOFT_RED |
| `ricochets_max` / `ricochet_chance` | **4 次** / **30%**（跳弹） |
| `icon_state` | "redtrac"（红色曳光） |

## 2.3 T90 车载机枪 mounted_machine_gun.dm

### 2.3.1 基本属性

`/obj/machinery/mounted_machine_gun` —— "T90 Mounted Machine Gun"：

| 属性 | 值 | 说明 |
|---|---|---|
| `can_buckle` | TRUE | 可捆绑乘员 |
| `anchored` / `density` | FALSE / TRUE | 初始未锚定 |
| `max_integrity` | **250** | 耐久 |
| `buckle_lying` | 0 | 乘员不躺倒 |
| `SET_BASE_PIXEL(-8, -8)` | — | 基础像素偏移 |
| `layer` | ABOVE_MOB_LAYER | 图层 |
| `view_range` | **2.5** | 乘员视角额外加成 |
| `overheatsound` | `sizzle2.ogg` | 过热音效 |
| `firesound` | `50cal_box_01.ogg` | 射击音效 |
| `undeploy_time` | 3 秒 | 扳手收起耗时 |
| `ammo_box_type` | `mmg_box` | 弹药盒类型 |
| `fire_delay` | **0.2 秒** | 射击间隔（极快） |
| `spread` | **5** | 弹道散布 |
| `bolt` | TRUE | 枪机状态（TRUE=待击可射，FALSE=复位） |
| `undeployed_type` | `/obj/item/mounted_machine_gun_folded` | 收起后掉落的折叠物 |

**射击相关变量**：

| 变量 | 默认值 | 说明 |
|---|---|---|
| `barrel_heat_per_shot` | **5** | 每发增加枪管热量 |
| `barrel_heat` | 0 | 当前枪管热量 |
| `overheated` | FALSE | 是否已过热锁死 |
| `cooldown_time` | **10 秒** | 过热冷却时间 |
| `passive_barrel_cooldown_rate` | **2** | 自然散热速率（/秒） |
| `max_barrel_heat` | **100** | 过热阈值 |
| `last_target_atom` | weakref | 最后瞄准目标 |
| `trigger_cooldown` | COOLDOWN | 扳机冷却 |

**Destroy**：清理弹药盒、粒子、目标引用；注销乘员。

**process（每帧）**：`barrel_heat` 按 `passive_barrel_cooldown_rate` 自然降温并刷新外观。

**update_overlays**：有弹药盒→显示 "ammo_box" 叠加；热量 50–75→`[base]_barrel_hot`（枪管红热）；75+→`[base]_barrel_overheat`（过热）。

**Examine**：显示弹药盒剩余数（或无弹药盒红色警告）、护盖开/关状态与操作提示、枪管热度警告、过热警告。

### 2.3.2 乘员绑定与视角控制

- **user_buckle_mob**：乘员不能失能；强制移到机枪格；`register_user` 注册；转向 SOUTH；`set_anchored(TRUE)`；播放 mechmove 音；`update_positioning`。
- **register_user**（信号注册）：
  - 乘员**所有手持物品**被替换为 `/obj/item/gun_control` 机枪控制器（空手也补发）。
  - 注册 `COMSIG_MOB_LOGIN`（重连后重挂扳机信号）、`COMSIG_CLIENT_MOUSEDOWN`（扳机按下）、`COMSIG_CLIENT_MOUSEUP`（扳机松开）、`COMSIG_CLIENT_MOUSEDRAG`（拖拽更新目标）。
  - 乘员视角 `view_size.setTo(view_range)` 扩展，`pixel_y = 14`。
- **unbuckle_mob**：删除乘员手中所有 `gun_control`；恢复乘员像素偏移与默认视角；`set_anchored(FALSE)`；注销信号。
- **reregister_trigger**：客户端重连后重新注册扳机/松开/拖拽信号（"我真的很讨厌 BYOND"——作者注）。
- **update_target_drag**：拖拽鼠标时记录 `last_target_atom = WEAKREF(over_object)`（屏幕 UI 除外）。

### 2.3.3 射击机制

**trigger_pulled（扳机按下）**：
- 校验点击修饰键（Shift / Ctrl / 中键 / 右键 / Alt 点击一律忽略）；`throw_mode` 投掷模式忽略；屏幕 UI 忽略；已有射击计时器忽略；扳机冷却中忽略；必须本人操作。
- 切换鼠标指针为 `weapon_pointer.dmi` 准星。
- 记录目标→`INVOKE_ASYNC(process_fire)`。

**process_fire → fire_at（持续射击循环）**：
- `fire_at` 失败即停；成功则用 `fire_delay`（0.2 秒）的 `TIMER_STOPPABLE` 计时器递归续射——**按住鼠标持续射击**。
- `fire_at` 校验：乘员存在、目标有效（非屏幕 UI、非机枪自身）、`update_positioning` 转向、`can_fire()` 通过、从弹药盒 `get_round()` 取弹。
- 发射：`casing.fire_casing(target, current_user, params, 0, suppressed, null, spread, src)`（散布 5）→ 播放 `firesound` → 弹壳落地弹开 → `barrel_heat += 5` → 达到 100 触发过热 → 刷新外观。
- **trigger_released（松开扳机）**：删除射击计时器，恢复鼠标指针。

**can_fire（开火检查，失败播 dry_fire 空枪音）**：
- 无弹药盒→`drop_bolt` 枪机复位，失败。
- 弹药盒空→`drop_bolt`，失败。
- 护盖打开→提示，失败。
- 过热中→提示，失败。
- 一切正常但枪机未待击→`cock_bolt` 拉栓（播 cock_bolt.ogg）。

**check_click_modifiers**：Shift/Ctrl/中键/右键/Alt 点击均不触发扳机（防止误操作）。

**update_positioning / direction_track（8 向转向 + 乘员位移）**：

| 方向 | 图层 | 平面 | 乘员 pixel_x | 乘员 pixel_y |
|---|---|---|---|---|
| NORTH 北 | BELOW_MOB_LAYER | GAME_PLANE | 0 | -14 |
| NORTHEAST 东北 | BELOW_MOB_LAYER | GAME_PLANE | -18 | -8 |
| EAST 东 | ABOVE_MOB_LAYER | — | -22 | 0 |
| SOUTHEAST 东南 | BELOW_MOB_LAYER | GAME_PLANE | -18 | 14 |
| SOUTH 南 | ABOVE_MOB_LAYER | — | 0 | 22 |
| SOUTHWEST 西南 | BELOW_MOB_LAYER | GAME_PLANE | 18 | 14 |
| WEST 西 | ABOVE_MOB_LAYER | — | 22 | 0 |
| NORTHWEST 西北 | BELOW_MOB_LAYER | GAME_PLANE | 18 | -8 |

（乘员失能时无法转向；机枪与乘员同步 `setDir`。）

### 2.3.4 枪管过热机制

- 每发 +5 热量；热量 ≥50 枪管红热（图标），≥75 显示过热图标。
- 热量达到 **100（max_barrel_heat）**→ `overheated = TRUE`：播放 sizzle 音、生成 `particles/smoke()` 烟雾、10 秒后 `reset_overheat` 恢复（清除烟雾与过热状态）。
- 过热期间 `can_fire` 拒绝开火（热锁 Heatlock）。
- 自然散热：`process` 每秒最多降 2 点。
- **水冷**（见 2.3.8）：泼水瞬间大额降温。

### 2.3.5 弹药盒装卸

- **装入**（item_interaction）：手持 `ammo_box_type` 且机枪无盒→装入，播 `insert_ammobox.ogg`；已有盒则拒绝。
- **取出**（attack_hand_secondary）：必须先 `click_alt` 打开护盖；打开后右键取出，弹药盒放入手中，播 `remove_ammobox.ogg`。
- **护盖**（click_alt / toggle_cover）：切换开关，播 `open_lid.ogg` / `close_lid.ogg`；护盖打开时无法开火。

### 2.3.6 维护与拆装

- **焊修（welder_act）**：非战斗模式、耐久未满时，焊枪 4 秒、耗 **10 焊料**，修复至满耐久。
- **收起（wrench_act）**：非战斗模式；必须无弹药盒（防弹药丢失）；限人类；3 秒 `do_after` 后生成 `undeployed_type` 折叠物并销毁机枪。

### 2.3.7 折叠形态与部署

`/obj/item/mounted_machine_gun_folded` —— "folded T-90 mounted machine gun"：

| 属性 | 值 |
|---|---|
| `max_integrity` | 250 |
| `w_class` | WEIGHT_CLASS_BULKY（笨重） |
| `slot_flags` | ITEM_SLOT_BACK（可背） |
| `type_to_deploy` | `/obj/machinery/mounted_machine_gun` |
| `deploy_time` | **5 秒** |

- Initialize 时挂载 `/datum/component/deployable` 组件：激活后 5 秒部署为完整机枪。

### 2.3.8 水冷联动

`/datum/reagent/water/expose_obj` 对机枪特判：泼水时若枪管有热量——
- `reset_overheat()` 立即解除过热；
- `barrel_heat` 一次性减去最多 **7 发当量**（`barrel_heat_per_shot × 7` = 35 点）；
- 播放 sizzle 音并向旁观者提示。

---

## 附录 · 代码路径索引

| 内容 | 源码路径 |
|---|---|
| 组装流程 | `modular_nova/modules/magfed_turret/code/assembly.dm` |
| 设计器变体 | `modular_nova/modules/magfed_turret/code/designators.dm` |
| 生成器 | `modular_nova/modules/magfed_turret/code/spawners.dm` |
| 炮塔框架 | `modular_nova/modules/magfed_turret/code/turret_framework.dm` |
| Cargo 玩具炮塔 | `modular_nova/modules/magfed_turret/code/turrets/cargo.dm` |
| 废墟炮塔（5 型） | `modular_nova/modules/magfed_turret/code/turrets/ruins.dm` |
| 模块说明 | `modular_nova/modules/magfed_turret/readme.md` |
| 弹药盒 / .50 BMG | `modular_nova/modules/mounted_machine_gun/code/ammobox.dm` |
| T90 车载机枪 | `modular_nova/modules/mounted_machine_gun/code/mounted_machine_gun.dm` |
| 机枪音效（6） | `modular_nova/modules/mounted_machine_gun/sound/` |
| 图标 | `magfed_turret/icons/`（assembly / designator / spawners / turrets / mob / inhands）；`mounted_machine_gun/icons/`（turret / turret_objects） |
| 外部依赖（readme 声明） | `modular_nova/module/GAGS/json_configs/turret.json`、`turret_toolbox.json`；`modular_nova/module/tarkon/code/misc_fluff/turret.dm` 及 tarkon 图标（部分） |

**制作人员（readme）**：ZenithEevee（主作者）、CliffracerX（GAGS）、VinylSpider（代码协助）、Paxil（部分美术风格）。
