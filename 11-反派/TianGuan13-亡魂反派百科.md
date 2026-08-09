# TianGuan13 · 亡魂（Revenant）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/revenant/`（4 文件 364 行）+ `code/modules/mob/living/basic/space_fauna/revenant/`（6 文件 1,207 行）+ `code/datums/components/revenant_prison.dm`（68 行）+ NOVA 模块 `modular_nova/modules/revenant_buffs/`（4 文件 177 行）+ `modular_nova/modules/aesthetics/revenant/`（2 文件 108 行）
> **类型**: 中局幽灵型单人反派（midround from-ghosts）｜**难度**: ★★★（隐形时无敌，但每次施法/收割都会暴露）
> **一句话**: 你是一只**亡魂（Revenant）**——由死者怨念凝聚的幽灵，**隐形且免疫一切伤害**；通过**收割（harvest）人类灵魂获取精华（essence）**——精华既是你的血条也是你的法力，用它解锁并施放 8 种能力，搅得空间站不得安宁。
> **本版全量审计**: 从源码全量提取精华系统、收获机制、全部能力、antag datum 全属性、目标系统、生成条件、NOVA 改动，数值全部实测自源码。

---

## 目录

- [一、核心概念（本体与基础属性）](#一核心概念本体与基础属性)
- [二、精华系统（essence 全量）](#二精华系统essence-全量)
- [三、收获机制（harvest 全量）](#三收获机制harvest-全量)
- [四、生成条件与登录流程](#四生成条件与登录流程)
- [五、能力全录（8 能力 + 1 外观动作）](#五能力全录8-能力--1-外观动作)
- [六、核心属性（antag datum 全属性）](#六核心属性antag-datum-全属性)
- [七、目标系统（全量）](#七目标系统全量)
- [八、状态效果与克制机制](#八状态效果与克制机制)
- [九、死亡与重聚（灵质 / 镜子监狱）](#九死亡与重聚灵质--镜子监狱)
- [十、风味彩蛋](#十风味彩蛋)
- [十一、NOVA 专属改动清单](#十一nova-专属改动清单)
- [十二、对战攻略（双向）](#十二对战攻略双向)
- [十三、数值速查表](#十三数值速查表)

---

## 一、核心概念（本体与基础属性）

**代码**: `code/modules/mob/living/basic/space_fauna/revenant/_revenant.dm`（553 行，亡魂本体）

亡魂不是普通幽灵——它是**生物型 mob**（`/mob/living/basic/revenant`），只是"像幽灵一样隐形移动"。源码注释：*"Can hear deadchat, but are NOT normal ghosts and do NOT have x-ray vision"*（能听死语频道，但不是普通幽灵、没有 X 光视觉）。

### 1.1 本体基础属性

| 属性 | 值 | 说明 |
|---|---|---|
| 名称 | "revenant" | 描述 "A malevolent spirit."；`unique_name = TRUE`，Init 时 `generate_random_mob_name()` 随机起名 |
| 生物类型 | `MOB_SPIRIT \| MOB_UNDEAD` | 灵体 + 不死 |
| 生命值 | `health = INFINITY`、`maxHealth = INFINITY` | **不用血量，用精华**（essence）代替 |
| 移动 | `incorporeal_move = INCORPOREAL_MOVE_JAUNT` | 幽灵穿墙式移动 |
| 隐身 | `invisibility = INVISIBILITY_REVENANT`（= **50**，`code/__DEFINES/sight.dm`） | 隐形，其他幽灵可见 |
| 平面/视觉 | `plane = GHOST_PLANE`、`sight = SEE_SELF` | 幽灵平面；无 X 光 |
| 光影切割 | R20 / G15 / B35 | 淡紫色幽光外观 |
| 伤害系数 | BRUTE 1 / BURN 1 / TOX 0 / STAMINA 0 / OXY 0 | 毒素/耐力/缺氧完全免疫（但反正隐形时无伤） |
| 大气 | `habitable_atmos = null`、耐温 0 ~ INFINITY、`unsuitable_atmos_damage = 0` | 任何大气/温度环境都无伤 |
| 密度/体型 | `density = FALSE`、`mob_size = MOB_SIZE_TINY` | 不占格 |
| 穿行 | `pass_flags = PASSTABLE \| PASSGRILLE \| PASSMOB` | 可穿桌/格栅/生物 |
| 抗推 | `move_resist = MOVE_FORCE_OVERPOWERING` | 几乎不可被推动（`code/__DEFINES/move_force.dm` L13） |
| 速度 | `speed = 1` | |
| 状态标志 | `status_flags = NONE` | |
| 朝向动词 | friendly "touches" / help "passes through" / disarm "swings through" / harm "punches through" | |
| HUD | `hud_possible = list(ANTAG_HUD)`、`hud_type = /datum/hud/revenant` | 专属 HUD（`code/_onclick/hud/revenanthud.dm`） |

### 1.2 初始化获得的特质与元素

`Initialize()`（L99-120）：
- `AddElement(/datum/element/simple_flying)`（简单飞行）
- 6 个 INNATE 特质：`TRAIT_COMBAT_MODE_LOCK`（锁战斗模式）、`TRAIT_SPACEWALK`（太空行走）、`TRAIT_SIXTHSENSE`（第六感）、`TRAIT_FREE_HYPERSPACE_MOVEMENT`、`TRAIT_SEE_BLESSED_TILES`（**能看见祝福地板**）、`TRAIT_IGNORE_ELEVATION`
- `grant_actions_by_list(abilities)` —— 授予全部能力（见第五章）
- 注册信号：被圣物攻击（`COMSIG_LIVING_BANED`）、移动前（`COMSIG_MOVABLE_PRE_MOVE`）、Life 心跳（`COMSIG_LIVING_LIFE`）、镜像倒影更新（`COMSIG_REFLECTION_UPDATED`）、外观相关特质增减
- 加入 `GLOB.revenant_relay_mobs`（死语中继列表，Destroy 时移除）

### 1.3 无敌规则（核心）

| 伤害来源 | 行为 | 源码 |
|---|---|---|
| 直接伤害 | `adjust_health()` 未暴露（无 `TRAIT_REVENANT_REVEALED`）时直接 `return 0`——**隐形时完全无伤**；暴露后受伤扣**精华**（`essence = max(0, essence - amount)`），精华归 0 → `death()` | L261-273 |
| 爆炸 | `ex_act()` 恒 `return FALSE`——免疫 | L311 |
| 菌潮 | `blob_act()` 空实现——免疫 | L314 |
| 奇点 | `singularity_act()` 空实现——免疫 | L317 |
| Nar'Sie | `narsie_act()` 空实现——免疫 | L320 |
| 子弹 | `projectile_hit()`：未暴露或休眠时 `BULLET_ACT_FORCE_PIERCE`（子弹直接穿过） | L323-326 |
| 重力 | `mob_negates_gravity()` 恒 TRUE | L500 |
| 粉尘/碎尸 | `dust()` / `gib()` 都只调用 `death()` | L302-306 |
| 世界交互 | `can_perform_action()` 恒 FALSE；`throwforce = 0` | L308、L20 |

**隐身 vs 暴露**：被 `TRAIT_REVENANT_REVEALED`（暴露）标记时不可穿墙（`incorporeal_move = FALSE`）、可见、可被攻击；暴露状态精华 ≤ 0 时在 Life 心跳里直接 `death()`（L153-155）。

---

## 二、精华系统（essence 全量）

**代码**: `_revenant.dm`（变量 L82-97；机制 L147-162、L420-498）

### 2.1 精华变量表

| 变量 | 上游值 | **NOVA 值** | 作用 |
|---|---|---|---|
| `essence`（当前精华） | 75 | **200** | 既是资源也是血条；施法消耗、受伤抵扣 |
| `max_essence`（回复上限） | 75 | **100** | 自然回复到此封顶；收割可永久提升 |
| `essence_regenerating` | TRUE | TRUE | 是否自然回复 |
| `essence_regen_amount`（每秒回复） | 2.5 | 2.5 | 每 Life tick 按 `2.5 × Δt` 回复 |
| `essence_accumulated`（累计窃取） | 0 | 0 | 只增不减，**目标判定依据** |
| `essence_excess`（可支配盈余） | 0 | **100** | **只用于解锁能力**（unlock），不会被施法消耗 |
| `perfectsouls`（完美灵魂数） | 0 | 0 | 收割 >90 精华的灵魂计数（TODO 注释：未来做目标用） |

> **NOVA 改动**：`modular_nova/modules/revenant_buffs/_revenant.dm` L3-9 将 essence 75→**200**、max_essence 75→**100**、essence_excess 0→**100**（注释原文："Buffed from 75 -> 200" / "Buffed from 75 -> 100" / "Some starting essence so you aren't sitting there puppy-eyed during greenshift"）。开局即带 100 盈余，可直接解锁 Defile+Overload+Haunt Object（25+10+30=65 点）。

### 2.2 回复规则（`on_life`）

- 条件：`essence_regenerating && 未被抑制(TRAIT_REVENANT_INHIBITED) && essence < max_essence`
- 速率：`essence_regen_amount × Δt` = **2.5/秒**
- 被抑制（inhibited，如被圣物攻击）时**完全停止回复**
- 休眠（dormant，被灵质囚禁）时直接取消整个 Life 处理

### 2.3 收支规则（`change_essence_amount` / `cast_check` / `unlock`）

- **收入**（收割成功等正数变动）：同时增加 `essence`、`essence_accumulated`、`essence_excess`（都从 0 起算）
- **施法扣费**（`cast_check` 负数）：只扣 `essence`，`essence + 扣费 < 0` 则拒绝施法（`SPELL_CANCEL_CAST`）
- **解锁扣费**（`unlock`）：只扣 `essence_excess`，盈余不足拒绝
- 施法前置（`cast_check(essence_cost)` 全部不满足即失败）：不在封闭格（closed turf）、不在致密物体内部、非休眠（dormant）、非抑制（inhibited）
- HUD 显示：屏幕血条位显示 `[essence]E`；颜色 `#8F48C6`（正常）/ `#9A5ACB`（超过 max_essence）/ `#1D2953`（≤0 濒死）；状态栏显示 当前精华/累计窃取/盈余/完美灵魂 四项
- `vv_edit_var` 编辑精华变量时同步刷新 HUD 与技能按钮（方便管理员调试）

### 2.4 开局经济（NOVA 视角）

- 开局 essence 200（超上限 100）、excess 100 → 可立刻解锁 3 个便宜能力（共 65 盈余），之后靠收割攒 125 解锁 Malfunction
- 自然回复 2.5/s，满 100 上限需要 ~40 秒（从 0 起）

---

## 三、收获机制（harvest 全量）

**代码**: `code/modules/mob/living/basic/space_fauna/revenant/revenant_harvest.dm`（148 行）

亡魂唯一的精华收入来源：**点击范围内的人类**触发 `attempt_harvest()` → `harvest_soul()`，从目标身上抽取灵魂精华。

### 3.1 前置检查（`attempt_harvest`）

| 检查 | 结果 |
|---|---|
| 目标已在 `drained_mobs` 列表（**每个 mob 只能被收割一次**） | 拒绝 |
| `cast_check(0)` 失败（休眠/被抑制/在封闭格内） | 拒绝 |
| 已在收割中（`draining`） | 拒绝 |
| 目标是全息投影（`HOLOGRAM_1`） | 气球提示，拒绝 |
| 目标 **CONSCIOUS（清醒）** | 拒绝（10% 概率会提示目标"有什么东西在盯着你"） |

### 3.2 收割数值（精华收益）

```
基础收益            essence_drained = rand(15, 20)
目标有 client 或 ckey（"智能灵魂"）  + rand(20, 30)   → 35~50
目标未死亡 且 非弱魂(TRAIT_WEAK_SOUL) + rand(40, 50)   → 最高 75~100
目标无 client 且 弱魂                 essence_drained = 5（固定）
```

| 目标状态 | 收益区间 |
|---|---|
| 尸体（无 ckey） | 15~20 |
| 尸体（有 ckey 的玩家尸体） | 35~50 |
| 活人（无客户端，如 AI 控制/弱魂外） | 55~70 |
| 活人（有客户端） | **75~100** |
| 弱魂且无客户端（`TRAIT_WEAK_SOUL`） | 5 |

### 3.3 收割时间线（总耗时约 8.6~11.1 秒）

| 阶段 | 耗时 | 说明 |
|---|---|---|
| 1. 开始 | — | `log_combat` 记录；`face_atom(target)` 面对目标 |
| 2. 第一次 do_after | `rand(10, 20)` 分秒（1~2 秒） | |
| 3. 判定智能/存活/弱魂 | — | 修正收益 |
| 4. 第二次 do_after | `rand(15, 20)` 分秒（1.5~2 秒） | 中途失败目标不受伤 |
| 5. 收益分档播报 | — | 1~30 / 30~70 / 70~90 / 90+ 四档消息 |
| 6. 第三次 do_after | `rand(15, 25)` 分秒（1.5~2.5 秒） | |
| 7. 最终检查 | — | 目标若已清醒 → 中止（目标收到 bolddanger 警告） |
| 8. 暴露+麻痹 | — | 亡魂获得 revealed **5 秒** + paralyzed **5 秒**（此刻起可见可打） |
| 9. 圣物抗性检查 | — | 目标 `can_block_magic(MAGIC_RESISTANCE_HOLY)` → 收割失败 |
| 10. 灵魂光束 | `do_after 4.6 SECONDS`（可忽略 incapacitated） | 紫色 `drain_life` 光束 |
| 11. 入账 | — | `change_essence_amount(essence_drained)` → 精华/累计/盈余全加 |

### 3.4 收割结果

- **目标死亡**：`target.death(FALSE)`；若目标收割前活着，记入 `investigate_log("has died from revenant harvest.")`
- **max_essence 成长**：
  - 收益 ≤ 90 且目标未死且非弱魂 → `max_essence += 5`
  - 收益 **> 90（完美灵魂）** → `max_essence += 15`、`perfectsouls++`（只有"存活+有客户端"的 91~100 档可达）
- 目标记录进 `drained_mobs`（本局不可再收割同一人）
- 目标侧体验：目标为 SOFT_CRIT（濒死临界）时先被 `Stun(46)`；被收割者全程只听到自己逐渐衰弱的警告文本
- 若中途被打断：`log_combat(src, target, "stopped the harvest of")`

### 3.5 收割的防御侧

- **圣物/圣水抗性**：目标有 `MAGIC_RESISTANCE_HOLY` 抗性 → 最后一刻收割失败（牧师/圣物持有者难收）
- **打断**：任何阶段把目标弄醒（恢复 CONSCIOUS）即可中止收割
- **全息投影**无法被收割

---

## 四、生成条件与登录流程

**代码**: `code/controllers/subsystem/dynamic/dynamic_ruleset_midround.dm`（L651-697，中局规则集）

### 4.1 中局规则集（`/datum/dynamic_ruleset/midround/from_ghosts/revenant`）

| 参数 | 值 |
|---|---|
| 类型 | `midround / from_ghosts`（从幽灵池轮询玩家）、`midround_type = LIGHT_MIDROUND` |
| 标志 | `RULESET_INVADER`（入侵者类） |
| 权重 | `weight = 5` |
| 最低人口 | `min_pop = 10` |
| 最大反派数 | `max_antag_cap = 1`（单局最多 1 只） |
| 可重复 | `repeatable = FALSE` |
| 偏好槽 | `pref_flag = ROLE_REVENANT`（= "Revenant"，`code/__DEFINES/role_preferences.dm` L79） |
| 预览 | `preview_antag_datum = /datum/antagonist/revenant`、`signup_atom_appearance = /mob/living/basic/revenant` |

### 4.2 生成条件（`can_be_selected`）

- **必要条件：站内尸体数 > 10**（`required_station_corpses = 10`，统计 `GLOB.dead_mob_list` 中位于站内 z 层的所有类型尸体——注意停尸房开局通常自带 2~3 具，源码注释提醒过）
- 规则集注释：亡魂从死者怨念诞生，尸体越多越容易刷

### 4.3 生成点位（`create_ruleset_body` / `get_revenant_spawns`）

1. 首选：站内所有**尸体所在格**（≥12 个才够用）
2. 不足 12 个：追加**停尸柜/棺材等 bodycontainer** 所在格（`GLOB.bodycontainers`）
3. 仍不足 4 个：追加**鲤鱼刷新点（carpspawn）地标**
4. `new /mob/living/basic/revenant(随机点位)` —— 幽灵候选人由 from_ghosts 父类轮询，`assign_role` 直接 `return`（"revenant new() handles everything"）

### 4.4 登录流程（`Login()`，L126-144）

```
玩家进入 mob →
  boxed_message 播放引导文本（"You are a revenant." 等 7 行）
  → mind.set_assigned_role(/datum/job/revenant)   // 职业 datum（job_types/antagonists/revenant.dm，title = ROLE_REVENANT）
  → SEND_SOUND ghost.ogg（出场音效）
  → mind.add_antag_datum(/datum/antagonist/revenant)
  → on_gain → forge_objectives（1 主目标 + 1 花絮目标）
  → greet → announce_objectives（宣布目标）
```

- `generated_objectives_and_spells` 标志保证只初始化一次
- 管理员也可直接 spawn（源码注释："Admin-spawn or random event"）
- 登录后 NOVA 额外弹改名框（见第十一章）

---

## 五、能力全录（8 能力 + 1 外观动作）

**代码**: `code/modules/mob/living/basic/space_fauna/revenant/revenant_abilities.dm`（309 行）+ `modular_nova/modules/revenant_buffs/ghostwriting.dm`（87 行）+ `modular_nova/modules/revenant_buffs/_revenant.dm`（Manifest）

### 5.0 共用机制（`/datum/component/revenant_ability`，`revenant_skill.dm` 125 行）

所有 `/aoe/revenant` 子型能力在 `New()` 时自动挂载该组件，实现**锁/解锁/扣费/暴露/麻痹**一体化：

| 组件参数 | 默认值 | 说明 |
|---|---|---|
| `locked` | TRUE | 初始锁定，按钮带锁覆盖层（`icons/mob/actions/actions_revenant.dmi` "locked"） |
| `unlock_amount` | 100 | **用 essence_excess 解锁**所需盈余 |
| `cast_amount` | 50 | **用 essence 施放**所需当前精华 |
| `reveal_duration` | 8 秒 | 施放后暴露时长（`/datum/status_effect/revenant/revealed`） |
| `stun_duration` | 2 秒 | 施放后麻痹时长（`/datum/status_effect/incapacitating/paralyzed/revenant`） |

- 按钮名称动态显示：锁定 = `能力名 (XXSE)`，解锁 = `能力名 (XXE)`
- 施法判定顺序：锁定 → 盈余够解锁 → 扣盈余解锁；未锁 → `cast_check` 扣精华 → 施放后挂暴露+麻痹状态
- 所有能力 `spell_requirements = NONE`（无特殊施法条件）、`antimagic_flags = MAGIC_RESISTANCE_HOLY`
- `vv_edit_var` 支持管理员在线改消耗/时长（组件同步更新）

### 5.1 能力总表

| # | 能力 | 类型 | 解锁(盈余) | 施放(精华) | 冷却 | 范围 | 暴露/麻痹 | NOVA |
|---|---|---|---|---|---|---|---|---|
| 1 | Revenant Transmit（心灵传讯） | list_target/telepathy | — | **0** | 继承 | 半径 7（休眠时 2） | — | |
| 2 | Overload Lights（过载灯光） | aoe | **25** | **40** | **20 秒** | 半径 **5** | 8 秒 / **3 秒** | |
| 3 | Defile（亵渎） | aoe | **10** | **30** | **15 秒** | 半径 **4** | **4 秒** / 2 秒 | |
| 4 | Malfunction（故障） | aoe | **125** | **60** | **20 秒** | 半径 **4** | 8 秒 / 2 秒 | |
| 5 | Blight（枯萎） | aoe | **75** | **50** | **20 秒** | 半径 **3** | 8 秒 / 2 秒 | |
| 6 | Haunt Object（附身物品） | aoe（目标上限 7） | **30** | **50** | 0（未覆写） | 半径 **5** | **6 秒** / **3 秒** | |
| 7 | Ghostwriting（鬼画符） | pointed | — | **0** | **0.2 秒** | 点击格 | — | ✅ NOVA |
| 8 | Manifest（显形） | 独立法术 | — | **0** | 1（≈0.1 秒） | 自身 | 永久显形（可关） | ✅ NOVA |
| — | Revenant's Visage（亡魂容颜） | 外观动作 | — | — | 10 秒 | 自身 | — | ✅ NOVA（美学） |

> 注：Transmit / Ghostwriting / Manifest 不挂 `revenant_ability` 组件，因此**无精华消耗、不触发暴露**。能力总数 **8 个施法能力 + 1 个外观动作**。

### 5.2 各能力详解

#### ① Revenant Transmit（心灵传讯）—— 免费
- 亡魂**唯一直接交流手段**：向单个目标静默发送一条消息（只送达不广播）
- 专属语色 span：`revennotice` / `revenboldnotice`
- `antimagic_flags = MAGIC_RESISTANCE_HOLY | MAGIC_RESISTANCE_MIND`（圣物/心智抗性都能挡）
- 目标半径 7；**休眠（dormant）时只剩半径 2**（灵质里也能悄悄说）

#### ② Overload Lights（过载灯光）—— 解锁 25 / 施放 40 / CD 20s
- 把范围内亮着的灯注入精华，灯会"骤然爆亮并开始迸溅火花"（`do_sparks(4)`），**2 秒后**对周围放电
- `shock_range = 2`（灯周围 2 格）、`shock_damage = 15`（触电伤害，`electrocute_act`，`SHOCK_NOGLOVES` 无视手套绝缘）
- 紫色闪电光束（`purple_lightning` 0.5 秒）+ `defib_zap.ogg` 音效；目标有 `MAGIC_RESISTANCE_HOLY|MIND` 抗性则不受电
- 施放后暴露 8 秒（继承默认）+ 麻痹 **3 秒**

#### ③ Defile（亵渎）—— 解锁 10 / 施放 30 / CD 15s
一次性"诅咒整片区域"，对范围内每格按类型处理（概率均为源码值）：

| 目标 | 效果 | 概率 |
|---|---|---|
| 祝福效果（`/obj/effect/blessing`） | **直接删除**（破除圣地板） | 100% |
| 普通地板（非裸板/非邪教地板） | 变成裸板（`make_plating`，掉落原地板砖） | **15%** |
| 普通墙（非强化墙） | 附加锈蚀元素 `TRAIT_RUSTY` | **15%** |
| 强化墙（r_wall） | 附加锈蚀元素 | **10%** |
| 淋浴间 | 水回收器关闭、试剂清空换成**满血**、50% 概率自动打开喷血 | 100% / 50% |
| 盐堆（`/obj/effect/decal/cleanable/food/salt`） | 删除 | 100% |
| 储物柜/衣柜 | 打开 | 100% |
| 尸体容器（connected 状态） | 打开 | 100% |
| DNA 扫描仪 | 开门 | 100% |
| 窗户 | 造成 `rand(30, 50)` 伤害（`REVENANT_DEFILE_MIN_DAMAGE 30 / MAX 50`；完整度 ≤50 的窗户不会二次损坏）；整块窗附加裂纹特效 | 100% |
| 灯 | 闪烁 `rand(3, 5)` 次（"吓人"） | 100% |
| 镜子（非魔法镜） | 直接击碎（`atom_break("magic")`） | 100% |
| 地面特效 | 每处理一格生成 `/obj/effect/temp_visual/revenant` 紫色特效 | — |

- 施放后暴露 **4 秒** + 麻痹 2 秒

#### ④ Malfunction（故障）—— 解锁 125 / 施放 60 / CD 20s
对机器人/机械大破坏（源码注释警告：**不要改成 EMP，会 wreck 叛变 AI**）：

| 目标 | 效果 |
|---|---|
| 机器人 bot | 解锁面板（`BOT_COVER_LOCKED` → `BOT_COVER_MAINTS_OPEN`）+ 直接 `emag_act`（已 EMAG 的跳过） |
| 人类 | 心智干扰文本（方向感消失/头部刺痛/脑中充满静电）+ `emp_act(EMP_HEAVY)`（重型 EMP；有圣物抗性则跳过） |
| 任意物体 | **20% 概率** `emag_act`（其中 50% 附带紫色特效）；**APC 和 SMES 明确跳过**（防事故） |
| 机器人 cyborg | `warning-buzzer.ogg` 警报 + 火花 + `emp_act(EMP_HEAVY)` |
| **AI** | **完全无效**（只对 cyborg 生效，不对 AI） |

- 施放后暴露 8 秒 + 麻痹 2 秒（继承默认）

#### ⑤ Blight（枯萎）—— 解锁 75 / 施放 50 / CD 20s
让范围内的活物"衰败"，对每种目标：

| 目标 | 效果 |
|---|---|
| 人类 | 染上**枯萎病（Unnatural Wasting，见第十章）**；已染病则 **stage +1**（最高 5）；NOVA 改动：**只有非病毒免疫者**头发被染成深蓝紫 `#1d2953`（治愈后恢复）；首次感染提示"突然恶心/一阵反胃/皮肤不对劲" |
| 其他碳基生物 | 注入 **5u 等离子毒素**（`/datum/reagent/toxin/plasma`） |
| 其他生物 | 直接 **5 点毒素伤害** |
| 太空藤蔓（spacevine） | 染紫 `#823abb`，**10 秒后删除**（"搞植物学家专用技能"） |
| 荧光菇（glowshroom） | 同上，10 秒后删除 |
| 水培托盘（hydroponics） | 害虫 `rand(8, 10)`、杂草 `rand(8, 10)`、毒素 `rand(45, 55)` |

- 施放后暴露 8 秒 + 麻痹 2 秒（继承默认）

#### ⑥ Haunt Object（附身物品）—— 解锁 30 / 施放 50 / CD 0
- 半径 5 内选择最多 **7 个**物品（`max_targets = 7`）赋予"鬼魂"AI，让它们**自己飞起来砸人**
- 筛选条件：非锚定、非致密、在地板上、非抽象物品、隐身度低于 `INVISIBILITY_REVENANT`、未被附身（`ai_controller` 非 haunted）
- 附身概率公式：**`chance_of_haunting = 150 × (1 / 与亡魂距离)`** —— 离你越近越容易被附身（距离 1 格 = 100%，距离 3 格 = 50%）
- 附身效果（`/datum/component/haunted_item`，见第十章 10.4）：发光紫 `#823abb`、持续 **rand(1 分钟, 3 分钟)**、仇恨半径 = 5-1 = **4 格**、抛掷力 +3（上限 15）、出生/消散各有醒目广播文案
- 施放后暴露 **6 秒** + 麻痹 **3 秒**

#### ⑦ Ghostwriting（鬼画符）—— NOVA 专属，免费，CD 0.2 秒
- `modular_nova/modules/revenant_buffs/ghostwriting.dm`——**在地上写字，任何颜色都行，不只是紫色！！**（描述原文）
- 激活后生成一支无限耐久（`charges = INFINITE_CHARGES`）的亡魂蜡笔（`/obj/item/toy/crayon/revenant`），弹出颜色/图案选择 UI（`can_change_colour = TRUE`，默认紫色 `LIGHT_COLOR_PURPLE`，`instant = TRUE` 即点即画，强制识字）
- 点击任意地板格子作画；`is_valid_target` = 必须是 turf；不进入冷却就画不了
- 不挂精华组件 → 无消耗、不暴露

#### ⑧ Manifest（显形）—— NOVA 专属，免费，可切换
- `modular_nova/modules/revenant_buffs/_revenant.dm` L31-67——**主动现身**，用于"想被看见时对众人演讲/阴森跟踪"
- 施放后挂永久 `revealed/manifest` 状态（`STATUS_EFFECT_PERMANENT`），持续可见**直到再次施放取消**（区别于其他能力 8 秒时限的 reveal）
- 休眠/被抑制时无法施放；冷却 1（≈0.1 秒，纯防连点）

#### ⑨ Revenant's Visage（亡魂容颜）—— NOVA 美学，10 秒冷却
- `modular_nova/modules/aesthetics/revenant/code/revenant_fashion.dm`——外观切换动作（不消耗精华）：打开径向菜单从 `subtypesof(/datum/revenant_fashion)` 选外观
- 内置两款：**modern**（新式像素幽灵，默认，`allow_random = TRUE` 开局随机穿）与 **classic**（经典旧图标，`allow_random = FALSE` 只能手动选）
- 每款定义 idle/revealed/stun/draining 四个 icon_state；切换后动作按钮消失（一次性造型）

---

## 六、核心属性（antag datum 全属性）

**代码**: `code/modules/antagonists/revenant/revenant_antag.dm`（25 行）+ 基类 `code/modules/antagonists/_common/antag_datum.dm` + NOVA `modular_nova/modules/revenant_buffs/revenant_antag.dm`（21 行）

### 6.1 亡魂自身覆写（仅 5 项——极简型 datum）

| 属性 | 值 | 说明 |
|---|---|---|
| `name` | `"\improper Revenant"` | |
| `show_in_antagpanel` | **FALSE** | 不出现在反派人列表面板 |
| `show_name_in_check_antagonists` | TRUE | 检查反派时显示名字 |
| `show_to_ghosts` | TRUE | 幽灵可见该反派 |
| `antagpanel_category` | `ANTAG_GROUP_HORRORS`（= "Eldritch Horrors"，`code/__DEFINES/antagonists.dm` L429） | 与邪教构造体/异端怪物同组 |

### 6.2 基类继承属性（未覆写 = 用基类默认值）

| 属性 | 基类默认 | 亡魂实际 | 结论 |
|---|---|---|---|
| `roundend_category` | `"other antagonists"` | "other antagonists" | **未覆写**（无专属回合结算报告分类） |
| `pref_flag` | null | null | **未覆写**——偏好槽在规则集层用 `ROLE_REVENANT`（"Revenant"） |
| `antag_hud_name` | null | null | **未覆写**——HUD 图标用基类 `hud_icon`（`icons/mob/huds/antag_hud.dmi`）默认 |
| `antag_moodlet` | null | **无** | **不存在**（源码 grep 0 命中）——亡魂没有心情加成 |
| `stinger_sound` | null | **无** | **不存在**——不出场警报音，改用登录时 `ghost.ogg`（见第十章） |
| `suicide_cry` | `""` | 无 | **不存在**（无自杀口号） |
| `hijack_speed` | 0 | 0 | **未覆写**（无劫机速度加成） |
| `hardcore_random_bonus` | FALSE | FALSE | **未覆写** |
| `can_assign_self_objectives` | FALSE | FALSE | **未覆写**（基类默认即不允许自选目标；与叛徒 NOVA 的 FALSE 一致） |
| `silent` | FALSE | FALSE | 未覆写 |
| `preview_outfit` | null | null | 未覆写（预览用 `get_preview_icon()` 画亡魂 idle 图标） |
| `view_exploitables` | — | **不存在** | 基类无此字段（源码 grep 0 命中） |

> **说明**：亡魂的 antag datum 是全库最简之一——**无 moodlet / 无 stinger / 无 suicide_cry / 无 hijack_speed / 无 hardcore**，力量完全来自生物本体与精华机制。此为源码事实，非漏写。

### 6.3 附加行为

- `get_preview_icon()`：用 `icons/mob/simple/mob.dmi` 的 `revenant_idle` 生成预览图标
- `greet()`：宣布目标
- NOVA `on_gain()` 追加：异步弹窗让玩家**给自己改名**（`pick_name`，tgui_input_text 校验后 `fully_replace_character_name`，原名保留确认流程，写入 LOG_OWNERSHIP）

---

## 七、目标系统（全量）

**代码**: `code/modules/mob/living/basic/space_fauna/revenant/revenant_objectives.dm`（40 行）

### 7.1 目标构成（`forge_objectives`，2 个目标固定）

```
/datum/antagonist/revenant/forge_objectives():
  ① /datum/objective/revenant       （主目标：吸精华）
  ② /datum/objective/revenant_fluff （花絮目标：随机恶作剧）
```

### 7.2 主目标：`/datum/objective/revenant`

| 字段 | 值 |
|---|---|
| `target_amount` | **`rand(350, 600)`**（New 时随机） |
| `explanation_text` | "Absorb [target_amount] points of essence from humans."（吸收 N 点人类精华） |
| `check_completion` | 必须是亡魂（`isrevenant`）且未死亡且 `essence_accumulated >= target_amount` —— **按累计窃取精华判定**（不是当前余额） |

### 7.3 花絮目标：`/datum/objective/revenant_fluff`

- `New()` 从 9 条基础文案中 `pick()` 一条；**站内有船长**时追加 "Make the captain as miserable as possible."（让船长尽可能痛苦）、**有在职小丑**（`current_positions`）时追加 "Make the clown as miserable as possible."（让小丑尽可能痛苦）→ 实际池 **9~11 条**
- 基础 9 条全录（英文原文 + 中文释义）：
  1. *Assist and exacerbate existing threats at critical moments.* —— 在关键时刻助长现有威胁
  2. *Cause as much chaos and anger as you can without being killed.* —— 在保命前提下制造尽可能多的混乱与愤怒
  3. *Damage and render as much of the station rusted and unusable as possible.* —— 让站内尽量多的区域锈蚀报废
  4. *Disable and cause malfunctions in as many machines as possible.* —— 瘫痪尽量多的机器
  5. *Ensure that any holy weapons are rendered unusable.* —— 确保圣武器全部失效
  6. *Heed and obey the requests of the dead, provided that carrying them out wouldn't be too inconvenient or self-destructive.* —— 在不至于太麻烦/自毁的前提下听从死者们的请求
  7. *Impersonate or be worshipped as a God.* —— 假冒或被崇拜为神
  8. *Make the crew as miserable as possible.* —— 让船员尽可能痛苦
  9. *Prevent the use of energy weapons where possible.* —— 尽量阻止能量武器的使用
- `check_completion` 恒 **TRUE**（花絮目标自动完成，纯扮演导向）

### 7.4 目标宣布

`greet()` → `owner.announce_objectives()`（登录时即宣布）；目标无概率分支、无终局判定——亡魂是"自由反派"，只有 1 个硬指标（350~600 精华）+ 1 个扮演指标。

---

## 八、状态效果与克制机制

**代码**: `code/modules/mob/living/basic/space_fauna/revenant/revenant_effects.dm`（76 行）+ `_revenant.dm` 移动检查

### 8.1 三个专属状态效果

| 状态效果 | id | 效果 | 触发 |
|---|---|---|---|
| `revealed`（暴露） | "revenant_revealed" | 挂 `TRAIT_REVENANT_REVEALED`、完全可见（`SetInvisibility(INVISIBILITY_NONE)`）、**不可穿墙**（`incorporeal_move = FALSE`）、结束当前环绕；移除时恢复穿墙（NOVA 改为按特质判断） | 施法后、收割后、踩盐 |
| `inhibited`（抑制） | "revenant_inhibited" | 挂 `TRAIT_REVENANT_INHIBITED`、**精华停止回复**、禁止施法/收割/灵应板，气球提示"你的力量被压制了" | 被圣物（bane）击中 |
| `paralyzed/revenant`（麻痹） | "revenant_paralyzed" | 挂 `TRAIT_NO_TRANSFORM`（无法移动/变换，用 stun/drain 图标） | 施法后、收割后、踩盐 |

### 8.2 移动阻挡（`incorporeal_move_check`，L399-418）

亡魂穿墙式移动会被三类东西挡住：

| 阻挡物 | 效果 |
|---|---|
| `NOJAUNT` 地砖（禁穿行区域） | 仅提示，无法通过 |
| **盐堆**（`/obj/effect/decal/cleanable/food/salt`） | 气球提示 + **强制暴露 2 秒 + 麻痹 2 秒**（踩上去就现形挨打） |
| **祝福地板**（`/obj/effect/blessing`） | 提示"你被圣力挡住了"，无法通过 |

### 8.3 圣物克制（bane）

- 亡魂注册 `COMSIG_LIVING_BANED`（`code/datums/components/bane.dm` 发射：圣物近战/投掷/射击命中）→ `on_baned`：**抑制 3 秒**（`inhibited 3 SECONDS`），带警告播报
- 即：牧师圣物（nullrod 等）打中亡魂 = 3 秒内无法施法/收割/回复精华

### 8.4 镜像倒影

- 亡魂**永远在镜中可见**（`on_reflect`）：即使隐形，倒影也会显示（隐形态是扭曲水波纹 `wibbly` 滤镜；魔法镜例外）
- 被囚禁在镜中时倒影同样显示水波纹（`shift_reflection`）
- 玩家可用镜子确认亡魂是否潜伏在旁——这是亡魂少数几个"隐形但可被侦查"的破绽

### 8.5 其他限制

- `TRAIT_NO_TRANSFORM` 时点击世界完全无效（NOVA 在 `ClickOn` 加了 `check_click_intercept` 检查）
- 隐形+清醒时不能使用任何与物理世界交互的动作；Alt+点击打开 loot 面板（拾取视野内物品，NOVA 无此限制）

---

## 九、死亡与重聚（灵质 / 镜子监狱）

**代码**: `_revenant.dm`（death/reform/death_reset/get_new_user，L328-355、L464-552）+ `code/modules/mob/living/basic/space_fauna/revenant/revenant_items.dm`（81 行）+ `code/datums/components/revenant_prison.dm`（68 行）

### 9.1 死亡条件（`death()`）

- **只有"暴露状态"才会死**：`!TRAIT_REVENANT_REVEALED || dormant` → 直接 return（隐形亡魂不可杀）
- 死亡流程：挂 `TRAIT_NO_TRANSFORM`（stunned trait）→ `dormant = TRUE` → 完全可见 → 图标切 `revenant_draining` → **`screech.ogg`（100 音量）** → 3 秒渐隐动画 → `move_to_ectoplasm`

### 9.2 灵质（`/obj/item/ectoplasm/revenant`，"glimmering residue 微光残渣"）

| 属性 | 值 |
|---|---|
| 图标 | `icons/effects/effects.dmi` "revenantEctoplasm"（细蓝灰粉末+紫雾旋绕） |
| 重量 | `WEIGHT_CLASS_SMALL` |
| `inert` | 惰性标志（无亡魂时 = 普通残渣） |

- 亡魂死亡后在原地生成灵质，内部挂 `revenant_prison` 组件把亡魂 mob 塞进去（`dormant = TRUE`、记录 `old_ckey`）
- **1 分钟计时器**（`addtimer(reform, 1 MINUTES)`）：灵质 1 分钟没被销毁 → 亡魂自动重聚（`message_admins` 通报 + `COMSIG_REVENANT_RELEASE` → `release_revenant` → `reform(old_ckey)` → `death_reset` 清空全部负面状态 → 原地复活）
- 复活后若 mob 无玩家（原玩家早已 ghost 走）→ `get_new_user`：**向幽灵轮询 5 秒**（`poll_ghosts_for_target`，jobban 检查 `ROLE_REVENANT`），选中者直接 PossessByPlayer；无人响应 → 生成惰性灵质 + `qdel` 本体
- 玩家操作灵质：
  - **主动使用**（attack_self）：5 格内找**可诅咒镜子**（`mirror.cursable` 且未囚禁亡魂）→ 把 `revenant_prison` 组件**转移进镜子**（亡魂被囚入镜中）→ 灵质消失；找不到镜子则提示并留在原地
  - **投掷**（throw_impact）：落点 3 格内找镜子转移；没有则提示
  - **自杀**（suicide_act）：吃掉/使用灵质自杀（返回 OXYLOSS）——彩蛋行为
  - **检查**（examine）：active 状态显示危险警告；inert 显示普通提示

### 9.3 镜子监狱（`/datum/component/revenant_prison`）

| 行为 | 效果 |
|---|---|
| 囚禁时 | 亡魂 mob `forceMove` 进镜子/灵质内部（休眠）；组件转移时 `PostTransfer` 跟着移动 |
| 查看镜子 | 附加警告文本（"镜子里的倒影不对劲……"） |
| **打碎镜子**（`COMSIG_ATOM_BREAK`） | `ahaha.ogg` 笑声 + 释放亡魂（`release_revenant` → `reform`） |
| 组件销毁（灵质被扔进太空销毁等） | 亡魂被强制 `ghostize(can_reenter_corpse = FALSE)`（玩家变幽灵，无法回归该 mob） |
| 倒影 | 镜子里的影像带水波纹滤镜（`shift_reflection`） |

> **实战意义**：打碎灵质/镜子 ≠ 杀亡魂——**打碎镜子反而立即释放它**；只有"销毁灵质本身"（丢太空/grinder）或"让它 1 分钟重聚"才是正解。想延长封印就把灵质丢进镜子（但要防镜子被砸）。

---

## 十、风味彩蛋

### 10.1 音效表

| 音效 | 文件 | 场景 | 音量 |
|---|---|---|---|
| 登录出场 | `sound/effects/ghost.ogg` | 玩家登录亡魂时播放（`Login()`） | 默认 |
| 死亡尖叫 | `sound/effects/screech.ogg` | 亡魂死亡渐隐时 | 100 |
| 过载电击 | `sound/machines/defib/defib_zap.ogg` | Overload Lights 电到人 | 50 |
| 故障警报 | `sound/machines/warning-buzzer.ogg` | Malfunction 电 cyborg | 50 |
| 镜子破碎笑 | `sound/effects/chemistry/ahaha.ogg` | 囚禁亡魂的镜子被打碎 | 100 |

### 10.2 名字生成

- `generate_random_mob_name()`：`spirit_type` + " of " + `adverb` + `theme`（词库 `strings/revenant_names.json`，路径常量 `REVENANT_NAME_FILE`，`code/__DEFINES/antagonists.dm` L417）
- NOVA：登录后可弹窗**自拟名字**（六章 6.3）

### 10.3 心情

- **无**：antag datum 无 `antag_moodlet`；亡魂 mob 也无心情系统接入（源码 grep 0 命中）——不死不活的存在没有情绪

### 10.4 附身物品（haunted_item）全机制

**代码**: `code/modules/antagonists/revenant/haunted_item.dm`（140 行）+ `code/datums/ai/hauntium/haunted_controller.dm` + `code/datums/elements/haunted.dm`

- 附身 = 挂 `haunted_item` 组件 + `haunted` 元素（紫色描边滤镜 `#f8f8ff` 默认）+ `haunted` AI 控制器（黑板上 `BB_TO_HAUNT_LIST` 仇恨名单）
- **仇恨等级**：批量附身时对范围内所有清醒、非灵体、可见生物写入仇恨值 **5**（源码注释：捡起来只给 **2**——被捡起来仇恨减半的设定）
- **抛掷攻击**：物品靠"把自己扔向目标"攻击；`throw_force_bonus = 3`，上限 `throw_force_max = 15`
- **解除方式**：
  1. 用圣物打（`/obj/item/nullrod`、`/obj/item/book/bible` 类型，或带 `TRAIT_NULLROD_ITEM` 特质的武器）→ 立即解除并播报
  2. 持续时间结束（Haunt Object 附身 1~3 分钟）
- **`haunt_outburst(epicenter, range, haunt_chance, duration)` 全局函数**——亡魂模块提供的"万圣节爆发"，被 3 处复用：
  - 灵质异常（ectoplasm anomaly）：半径内 45% 概率、持续 2 分钟
  - 反应装甲（reactive armour）：半径 5、85% 概率、30 秒
  - 异常核心（anomaly core）：半径 2、33% 概率、30 秒

### 10.5 枯萎病（Blight 疾病 `/datum/disease/revblight`，"Unnatural Wasting 超自然衰败"）

**代码**: `code/modules/antagonists/revenant/revenant_blight.dm`（74 行）

| 属性 | 值 |
|---|---|
| 类型 | 状态型（form = "Condition"） |
| 阶段数 | `max_stages = 5`；阶段推进概率 `stage_prob = 5` |
| 传播 | `DISEASE_SPREAD_NON_CONTAGIOUS`（**不传染**）；`spreading_modifier = 1` |
| 治愈 | **圣水**（`/datum/reagent/water/holywater`）或**休息**；`cure_chance = 30`（注释：因为亡魂是混蛋所以治愈率调高）；`cure_text` = "holy water or rest" |
| 病原 | agent = "Unholy Forces"（邪恶力量） |
| 宿主 | 仅人类（`viable_mobtypes = list(/mob/living/carbon/human)`） |
| 严重度 | `DISEASE_SEVERITY_HARMFUL`（有害） |

阶段效果（`stage_act`）：
- 任意阶段：**躺下休息**（LYING_DOWN）每 tick 3×stage% 概率**直接自愈**
- 症状：1.5×stage% 概率眩晕（confusion 8 秒）+ 20 耐力损失 + 随机不适文案（"又累又困/方向错乱/又累又困惑/恶心/头晕/虚弱"）
- 毒性：每次升 stage 造成 `1 × stage` 毒素伤害（注释：总计约 30 点）
- 25% 概率额外耐力损失（数值 = 当前 stage）
- 阶段 2/3/4：渐强的情感动作（2.5% 苍白 → 5% 苍白/发抖 → 7.5% 苍白/发抖/哭泣）
- **阶段 5 终局**：绝望宣言（"什么都不值得了/从来没人需要你的帮助/你做的事毫无意义/你尝试的一切都一文不值"）、22.5 耐力损失、**头发永久染成 `#1d2953` 深蓝紫 + 全身变色**、10 秒后自动治愈
- 治愈时：移除变色、恢复发色

### 10.6 其他彩蛋

- 登录引导文本 7 行（"You are a revenant." / "你不死不活，介于两者之间，能与两个世界有限互动" / "你无敌且除幽灵外无人可见，多数能力会暴露你" / "你必须吸取人类的生命精华……" / "你不记得前世，死后也不会记得这一世" + tgstation 官方 Wiki 链接 `https://tgstation13.org/wiki/Revenant`）
- 死亡播报随机文案："breaking apart（崩解）/ drifting away（飘散）"
- 精华 HUD 三色变化（第二章 2.3）
- 收割收益四档播报（1~30 / 30~70 / 70~90 / 90+）
- 环绕动画：右键目标可环绕飞行（`ranged_secondary_attack`，非暴露/非抑制/非 NO_TRANSFORM 且相邻），绕圈半径按目标图标尺寸动态计算
- 灵应板（spirit board）：隐形亡魂可以像幽灵一样点灵应板参与通灵会（占卜字母）
- 死语频道：`say()` 以 "**UNDEAD: [名字]** says" 格式中继给所有亡魂与观察者（`GLOB.revenant_relay_mobs`）

---

## 十一、NOVA 专属改动清单

### 11.1 主树内 NOVA EDIT（非 I18N 噪音）

| 位置 | 改动 |
|---|---|
| `_revenant.dm` L79 | 能力列表**新增 Ghostwriting**（`//NOVA EDIT ADDITION`） |
| `_revenant.dm` L222-225 | `ClickOn` 增加 `check_click_intercept` + `TRAIT_NO_TRANSFORM` 拦截（`//NOVA ADDITION`） |
| `revenant_abilities.dm` L229 | Blight 染发仅对**非病毒免疫**者生效（`// NOVA EDIT - No more funny purple hair`，原版无脑染紫） |
| `revenant_effects.dm` L30 | 暴露解除时 `incorporeal_move` 按当前特质恢复 JAUNT（`// NOVA EDIT CHANGE`，原版直接恢复穿墙） |
| 全部 8 个文件 | I18N CODEMOD：所有玩家可见字符串改 `LANG()` 键（`// NOVA EDIT - I18N CODEMOD` 文件头） |

### 11.2 modular_nova 模块覆写/新增

**`modules/revenant_buffs/`（4 文件 177 行，RP 强化包）**：
1. `_revenant.dm`（67 行）：**精华三改** essence 75→200 / max_essence 75→100 / essence_excess 0→100；新增 **Manifest 显形法术**（永久可见切换）；**授予全部非秘密语言**（`GLOB.all_languages` 遍历，secret 跳过）——亡魂能与任何船员交谈
2. `ghostwriting.dm`（87 行）：**Ghostwriting 鬼画符**能力 + 无限蜡笔（见 5.2⑦）
3. `revenant_antag.dm`（21 行）：on_gain 弹窗**自拟名字**
4. `revenant_effects.dm`（2 行）：新增 `revealed/manifest` 状态（id "revenant_revealed_manifest"）

**`modules/aesthetics/revenant/`（2 文件 108 行，外观包）**：
5. `code/_revenant.dm`（2 行）：mob 图标整体换成 `modular_nova/modules/aesthetics/revenant/icons/revenant.dmi`
6. `code/revenant_fashion.dm`（106 行）：**Revenant's Visage 外观切换**动作（modern/classic 双造型，开局随机 modern）

### 11.3 行为差异总览（上游 vs NOVA）

| 维度 | 上游 TG | NOVA |
|---|---|---|
| 开局精华 | 75 / 上限 75 / 盈余 0 | **200 / 100 / 100** |
| 能力 | 6 个 | **8 个**（+Ghostwriting、+Manifest） |
| 语言 | 无 | 全部非秘密语言 |
| 外观 | 固定 | 双造型可换 + 新图标 |
| 改名 | 随机名固定 | 登录可自拟 |
| Blight 染发 | 所有人类 | 仅非病毒免疫 |
| 出场音 | ghost.ogg | ghost.ogg（无 stinger） |

---

## 十二、对战攻略（双向）

### 12.1 玩亡魂

**阶段一：攒钱（0~100 盈余）**
- 开局 200 精华 + 100 盈余，先解锁 **Defile(10) → Overload(25) → Haunt Object(30)**（共 65），留 35 盈余备用
- 去停尸房/战斗现场**收割尸体**：普通尸体 15~20、玩家尸体 35~50——先挑有 ckey 的尸体（收益翻倍）
- 收割全程 8.6~11 秒且结束暴露 5 秒：**先清场再收**，被围观时收手

**阶段二：搅局（100~125+ 盈余）**
- 优先 **Blight(75)**：人群/植物园/厨房放枯萎病，5 阶段折磨 + 终局染发羞辱；对非人类用等离子/毒素
- **Defile** 15 秒冷却、10 解锁，是性价比之王：拆祝福地板→断牧师防线、碎镜子、锈墙壁、开柜子制造混乱
- **Overload**：灯多的地方（走廊/酒吧）放电 15 点，无视手套
- 攒到 **Malfunction(125)**：对 bot 全图 EMAG、对人类重型 EMP——配合叛变 AI 时段效果炸裂
- **Haunt Object**：往人群丢附身物品雨（距离 1 格 100% 附身）

**阶段三：求完美**
- 完美灵魂（>90 精华）：抓**有客户端的活人**收割（75~100 档）→ 每次 max_essence +15
- 目标 350~600 精华：约 5~15 次收割（视质量），累计即可完成，无需同时持有

**保命铁律**：施法=暴露+麻痹（8 秒/2 秒），被围殴先跑；绕开**盐堆**（踩到强制暴露 2 秒）与**祝福地板**；别在镜子前晃（倒影暴露）；被圣物打中 3 秒抑制期别挣扎

### 12.2 防亡魂

| 手段 | 效果 |
|---|---|
| **圣盐**（撒一地） | 亡魂无法通过，踩上强制**暴露 2 秒 + 麻痹 2 秒**——全员集火窗口 |
| **祝福地板**（牧师） | 彻底封路（配合盐构建封锁线） |
| **NOJAUNT 禁区**（工程/灵异地板） | 无法进入 |
| **圣水** | 治愈枯萎病（30% 概率/次），对抗 Blight 核心解药 |
| **圣物（nullrod/圣经）** | 近战/投掷/射击命中 → 亡魂**抑制 3 秒**（无法施法/回复） |
| **护魂/圣印**（MAGIC_RESISTANCE_HOLY） | 免疫收割（最后一刻失败）、免疫 Transmit/过载/故障 |
| **打碎灵质** | ⚠️ **别打镜子**（打碎=立即释放）；正确做法：把灵质**丢太空/销毁**（组件销毁=亡魂强制 ghostize）或丢进镜子拖时间（防砸） |
| **别堆尸体** | 站内尸体 >10 才会刷亡魂——及时收尸进冷库（冷库也算 bodycontainer 但至少挪离现场） |
| **收割打断** | 被收割者保持清醒/唤醒即可中止；收割者暴露 5 秒期间是击杀窗口 |
| **镜子侦查** | 路过镜子瞥一眼——隐形亡魂在镜中有倒影 |

---

## 十三、数值速查表

| 项 | 值 |
|---|---|
| 源码 | antagonists/revenant 4 文件 364 行 + space_fauna/revenant 6 文件 1,207 行 + revenant_prison 68 行 + NOVA 模块 285 行 |
| 能力总数 | **8 个**（上游 6 + NOVA 2）+ 1 个外观动作 |
| 开局精华 | 200（NOVA；上游 75） |
| 精华上限（回复封顶） | 100（NOVA；上游 75） |
| 开局盈余 | 100（NOVA；上游 0） |
| 自然回复 | 2.5/秒（被抑制时停止） |
| 收割基础 | rand(15, 20) |
| 智能灵魂加成 | +rand(20, 30)（有 ckey） |
| 存活加成 | +rand(40, 50)（非死非弱魂） |
| 弱魂（无客户端） | 5 固定 |
| 完美灵魂 | 收益 >90 → max_essence +15、perfectsouls++；非完美存活 +5 |
| 收割总耗时 | ~8.6~11.1 秒（含 4.6 秒光束） |
| 收割后暴露/麻痹 | 5 秒 / 5 秒 |
| 每 mob 收割次数 | 1（drained_mobs） |
| 主目标 | 吸收 rand(350, 600) 精华（essence_accumulated 判定） |
| 花絮目标 | 9~11 条随机（船长/小丑在场追加），恒完成 |
| 生成 | midround LIGHT_MIDROUND，weight 5，min_pop 10，cap 1，repeatable FALSE |
| 尸体门槛 | 站内尸体 >10 |
| 生成点 | 尸体格 → 停尸容器 → carpspawn 地标（分级回退） |
| 能力消耗（解锁/施放） | Overload 25/40 · Defile 10/30 · Malfunction 125/60 · Blight 75/50 · Haunt 30/50 · Transmit 0/0 · Ghostwriting 0/0 · Manifest 0/0 |
| 冷却 | Overload 20s · Defile 15s · Malfunction 20s · Blight 20s · Haunt 0 · Ghostwriting 0.2s |
| 范围 | Overload 5 · Defile 4 · Malfunction 4 · Blight 3 · Haunt 5（7 目标） |
| 暴露/麻痹 | Overload 8/3 · Defile 4/2 · Malfunction 8/2 · Blight 8/2 · Haunt 6/3 |
| 过载电击 | 半径 2、伤害 15、无视手套 |
| 亵渎概率 | 地板/普通墙 15%、强化墙 10%、盐/祝福 100%、灯闪 rand(3,5) |
| 窗户伤害 | rand(30, 50) |
| 故障 EMP | 人类/cyborg EMP_HEAVY、bot emag、物体 20% emag（APC/SMES/AI 免疫） |
| 枯萎病 | 5 阶段、圣水或休息治愈、cure_chance 30、不传染、毒素约 30 |
| 附身物品 | +3 抛掷力（上限 15）、持续 rand(1,3) 分钟、仇恨半径 4、距离 1 格 100% 附身 |
| 隐身等级 | INVISIBILITY_REVENANT = 50 |
| 重聚 | 灵质 1 分钟无人销毁自动复活；幽灵轮询 5 秒 |
| 盐踩踏 | 暴露 2 秒 + 麻痹 2 秒 |
| 圣物命中 | 抑制 3 秒 |
| 死亡条件 | 仅暴露状态可死 |
| 心情/stinger/suicide/hijack/hardcore | **均无**（源码 grep 0 命中/基类默认） |

---

*本文档数值全部实测自 `code/modules/mob/living/basic/space_fauna/revenant/`（_revenant 553 行 + abilities 309 行 + harvest 148 行 + effects 76 行 + items 81 行 + objectives 40 行）+ `code/modules/antagonists/revenant/`（antag 25 行 + blight 74 行 + skill 125 行 + haunted_item 140 行）+ `code/datums/components/revenant_prison.dm`（68 行）+ `code/controllers/subsystem/dynamic/dynamic_ruleset_midround.dm`（L651-697）+ `code/modules/antagonists/_common/antag_datum.dm` + `modular_nova/modules/revenant_buffs/` + `modular_nova/modules/aesthetics/revenant/` 源码，无推测。*
