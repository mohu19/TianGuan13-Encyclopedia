# TianGuan13 · 邪教（Cult of Nar'Sie）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/cult/`（19 文件 **5,498 行**）+ `code/modules/mob/living/basic/cult/`（构造体）+ `code/modules/power/singularity/narsie.dm`（Nar'Sie 本体 336 行）+ `code/game/objects/items/stacks/sheets/runed_metal.dm`（配方）
> **类型**: 团队型转化反派（符文仪式流）｜**难度**: ★★★★（符文+血魔法+构造体三线运营）
> **一句话**: 邪教献祭 Nar'Sie 古神——用**符文转化**船员、用**血魔法**战斗、用**构造体**碾压，最终目标：**在 3 个弱界点召唤 Nar'Sie** 毁灭空间站。
> **本版全量审计**: 旧版仅覆盖符文/血魔法/构造体清单——**本版从源码全量提取 datum 核心属性、目标系统全流程（生成/判定/胜负）、成长机制精确公式、领袖四能力完整参数**，并修正 5 处旧版数值错误（见 §13 审计修正）。所有数值 grep/read 自源码，无推测。

---

## 目录

- [一、核心机制（Datum 全属性）](#一核心机制datum-全属性)
- [二、目标系统全量](#二目标系统全量)
- [三、成长机制（Risen / Ascendent）](#三成长机制risen--ascendent)
- [四、领袖系统全录](#四领袖系统全录)
- [五、符文全录（11 种：10 可画 + 1 畸形）](#五符文全录11-种10-可画--1-畸形)
- [六、血魔法全录（10 种）](#六血魔法全录10-种)
- [七、构造体全录（6 种本体）](#七构造体全录6-种本体)
- [八、Nar'Sie 召唤与本体](#八narsie-召唤与本体)
- [九、邪教物品与结构](#九邪教物品与结构)
- [十、风味与彩蛋](#十风味与彩蛋)
- [十一、NOVA 专属改动](#十一nova-专属改动)
- [十二、对战攻略](#十二对战攻略)
- [十三、数值速查表](#十三数值速查表)

---

## 一、核心机制（Datum 全属性）

| 机制 | 值 |
|---|---|
| 转化 | Offer 符文：2 教徒转化（治疗 75%+10s 昏迷）/3 人活祭 |
| 初始装备 | 仪式匕首+**符文金属 ×10**+血感 HUD+血魔法 |
| 成长 | 教徒/非教徒存活比 >20% Risen 红眼发光 / >40% Ascendent 全光环（见 §三）|
| 领袖 | Final Reckoning 全体传送/血标 90s/Eldritch Pulse 15s CD（见 §四）|
| **目标** | ①献祭指定目标（3 灵魂解锁 1 次复活）②**3 弱界点召唤 Nar'Sie**（9 教徒+50s 绘制+40.1 自伤/次）|
| 语言 | 邪教语（narsie，LANGUAGE_CULTIST 来源）+ Communion 邪教通信 |

### 1.1 `/datum/antagonist/cult` 全属性（`datums/cultist.dm` 288 行）

| 属性 | 值 | 说明 |
|---|---|---|
| `name` | "Cultist" | 反派人名 |
| `roundend_category` | `"cultists"` | roundend 分类 |
| `antagpanel_category` | `"Cult"` | 管理面板分类 |
| `antag_moodlet` | `/datum/mood_event/cult` | 心情事件：**mood +12**（"I have seen the truth..."）|
| `suicide_cry` | `"FOR NAR'SIE!!"` | 自杀口号 |
| `preview_outfit` | `/datum/outfit/cultist` | 预览套装（黑制服+邪教袍 alt+邪教鞋+Stun 血魔法）|
| `pref_flag` | `ROLE_CULTIST` | 玩家偏好开关 |
| `antag_hud_name` | `"cult"`（领袖 `"cultmaster"`）| 队友 HUD 标签 |
| `stinger_sound` | `'sound/music/antag/bloodcult/bloodcult_gain.ogg'` | 获得反派音效 |
| `desensitized_modifier` | `DESENSITIZED_THRESHOLD` | 脱敏阈值修正 |
| `give_equipment` | FALSE（默认）| 管理员添加时不给装备 |
| `cult_team` | 所属 `/datum/team/cult` | 团队引用 |
| `cult_recipes` | static 列表 | CAT_CULT 分类配方，转化时 `teach_crafting_recipe` 全教 |

> **不存在字段**（与叛徒不同，全库 grep 0 命中）：`hijack_speed`、`hardcore_random_bonus`、`can_assign_self_objectives`——邪教目标由团队统一生成，不走叛徒的 self-objective 系统。

**on_gain 流程**: 目标并入团队目标 → （可选）发装备 → 授予 Communion + 血魔法 action → 日志 → 教 CAT_CULT 全部配方。**on_removal**: 遗忘配方 + 播报 + 删除全部 cult action。

**apply_innate_effects**: 小丑基因修正（可正常用武器）→ 加 `FACTION_CULT` 阵营 → 授 narsie 语言 → 血感 HUD alert → 血标图像（若有）→ Risen 红眼 / Ascendent 光环 → `TRAIT_HEALS_FROM_CULT_PYLONS` → 团队 HUD。**on_mindshield**: 仅弹警告提示（不主动移除 datum）。

**可转化判定**（`is_convertable_to_cult`，cult_other.dm）: 排除：无 mind/无客户端（挂机）、**圣职**（holy_role）、**献祭目标**、被非教徒奴役、**异端**（IS_HERETIC_OR_MONSTER）、`TRAIT_UNCONVERTABLE`、**硅基**（borg/bot/drone）。

### 1.2 `/datum/team/cult` 全属性（`datums/cult_team.dm` 205 行）

| 属性 | 值 | 说明 |
|---|---|---|
| `blood_target` | atom | 当前血标目标 |
| `blood_target_reset_timer` | timer | 血标过期计时（默认 90s）|
| `leader_passed_on` | FALSE | 领袖是否已传位（Mantle 仅 1 次）|
| `cult_leader_datum` | 引用 | 现任领袖 |
| `reckoning_complete` | FALSE | Final Reckoning 是否已用（单次）|
| `cult_risen` / `cult_ascendent` | FALSE | 成长阶段标记 |
| `unlocked_heretic_items` | 3 项 | 献祭异端解锁：Cursed Blade / Crimson Medallion / Proteon Orb |
| `narsie_summoned` | FALSE | Nar'Sie 是否已召唤 |
| `size_at_maximum` | int | 团队历史最大规模（add_member 时计数）|
| `true_cultists` | list | 召唤前一刻的教徒 mind 列表（roundend 展示）|

**核心常量**（`code/__DEFINES/cult.dm`）:

| 常量 | 值 | 含义 |
|---|---|---|
| MAX_BLOODCHARGE | 4 | 血魔法最大充能（Empower 符文旁）|
| ENHANCED_BLOODCHARGE | 5 | 强化充能（Crimson Medallion）|
| RUNELESS_MAX_BLOODCHARGE | **1** | 无符文旁最大充能 |
| CULT_RISEN | 0.2 | 教徒/非教徒比 >20% → Risen |
| CULT_ASCENDENT | 0.4 | >40% → Ascendent |
| BLOOD_HALBERD_COST | 150 | 血长戟消耗 |
| BLOOD_BARRAGE_COST | 300 | 血弹幕消耗 |
| BLOOD_BEAM_COST | 500 | 血光束消耗 |
| SOULS_TO_REVIVE | 3 | 3 灵魂复活 1 次 |
| IRON_TO_CONSTRUCT_SHELL_CONVERSION | 50 | 铁→构造壳 |
| sacrifices_used | 初始 **-3** | 开局免费 1 次复活（GLOBAL_VAR_INIT）|
| BLOODCULT_EYE | COLOR_RED | 邪教红眼 |
| CULT_VICTORY / CULT_LOSS | 1 / 0 | 胜负判定 |
| CULT_VICTORY_MASS_CONVERSION / CULT_FAILURE_NARSIE_KILLED / CULT_VICTORY_NUKE | 2 / 1 / 0 | 结局演出枚举 |
| GLOBAL_DATUM(cult_narsie) | — | 全局 Nar'Sie 引用 |

---

## 二、目标系统全量

**代码**: `cult_objectives.dm`（121 行）。邪教目标由**团队**统一生成（`setup_objectives()`），每局固定 **2 个目标**：

1. **献祭目标**（`/datum/objective/sacrifice`）
2. **召唤 Nar'Sie 目标**（`/datum/objective/eldergod`）

### 2.1 目标生成流程

```
create_team → cult_team = new /datum/team/cult
           → cult_team.setup_objectives():
① sacrifice_objective = new /datum/objective/sacrifice
   sacrifice_objective.find_target()   → 随机选 1 名船员为祭品
   objectives += sacrifice_objective
② summon_objective = new /datum/objective/eldergod
   objectives += summon_objective      → New() 中生成 3 个召唤点
```

所有教徒 `on_gain` 时 `objectives |= cult_team.objectives`——**同一团队共享同一组目标**。

### 2.2 献祭目标详解（`/datum/objective/sacrifice`）

**目标选择**（`find_target`）:

| 优先级 | 候选池 | 条件 |
|---|---|---|
| ①（首选）| **不可转化者** | `!IS_CULTIST` + `!is_convertable_to_cult`（圣职/异端/硅基等）+ 未死 + 站内（station z 层）|
| ②（兜底）| **可转化者** | 同上但去掉不可转化要求（无不可转化者时）|

**排除条件**（NOVA 添加）: 回合进行中位于 `/area/centcom/interlink`（中央指挥区）者不可选；未开启反派 opt-in 偏好者不可选（除非 `disable_antag_opt_in_preferences` 配置）。

**目标保护机制**: 对目标 mind 注册 `COMSIG_MIND_TRANSFERRED`、对目标身体注册 `COMSIG_QDELETING`/`COMSIG_MOB_MIND_TRANSFERRED_INTO`——目标死亡/被删/换脑/灵魂交换都会自动**重选目标**，防软锁。全部无候选时 `sacced = TRUE`（全员皆教徒的极端情况）。

**判定**: `check_completion()` = `sacced || completed`——目标被 Offer 符文献祭时 `sacced = TRUE`（`do_sacrifice` 中匹配 `sac_objective.target == sacrificial.mind`）。

**目标提示**: 团队 HUD 显示献祭目标照片（`make_image` 用目标职业/外观生成头像）。

### 2.3 召唤 Nar'Sie 目标详解（`/datum/objective/eldergod`）

**召唤点生成**（New）: 从 `GLOB.areas` 随机 **3 个**（`SUMMON_POSSIBILITIES 3`）满足条件者：站内层 + `VALID_TERRITORY` 旗标（有 sanity 上限 100 防死循环）。

**完成判定**: `check_completion()` = `summoned || completed`；若 Nar'Sie 被杀死则返回 `CULT_NARSIE_KILLED`（"失败到代码都倒退了"）。

### 2.4 目标判定与胜负（`check_cult_victory` / roundend）

```
for objective in objectives:
    if O.check_completion() == CULT_NARSIE_KILLED → CULT_NARSIE_KILLED（古神被杀）
    else if !O.check_completion()                 → CULT_LOSS（任一目标未完成）
return CULT_VICTORY
```

**Roundend 报告**（3 分支）:

| 结果 | 报告文本 |
|---|---|
| Nar'Sie 被杀 | `Nar'sie has been killed! The cult will haunt the universe no longer!` |
| 邪教胜利 | `The cult has succeeded! Nar'Sie has snuffed out another torch in the void!` |
| 邪教失败 | `The staff managed to stop the cult! Dark words and heresy are no match for Nanotrasen's finest!` |

+ 目标列表（含成功/失败后缀）+ 教徒名单（优先 `true_cultists`，即召唤前一刻的教徒）。

**血标系统联动**（team proc）: `set_blood_target(new_target, marker, duration = 90 SECONDS)`——Nar'Sie 降临后自动对古神设**无限期**血标（`duration = INFINITY`）。

---

## 三、成长机制（Risen / Ascendent）

**代码**: `cult_team.dm` 的 `check_size()`——每次 Offer 转化/献祭后调用（`runes.dm` convert invoke 末尾）。

**精确公式**（关键修正——旧版"信徒比"表述不精确）:

```
ratio = 存活教徒数(cultplayers) / 存活非教徒数(alive)
ratio > 0.2 → Risen（未触发过）
ratio > 0.4 → Ascendent（未触发过）
```

> 换算成"教徒占总存活人数"约 **>16.7%**（Risen）/**>28.6%**（Ascendent）。旧版直接写"信徒 20%/40%"是近似，本版修正为源码公式。

| 阶段 | 触发 | 效果（全员即时生效）| 音效 |
|---|---|---|---|
| 基础 | 初始 | 血魔法+符文+血感 HUD | — |
| **Risen 复苏** | ratio > 0.2 | `cult_eyes` 元素：**红眼发光**（TRAIT_UNNATURAL_RED_GLOWY_EYES）+ 播报 "The veil weakens..." | `bloodcult_eyes.ogg` |
| **Ascendent 升天** | ratio > 0.4 | `cult_halo` 状态：**光环**+ 播报 "the red harvest approaches - you cannot hide your true nature..." | `bloodcult_halos.ogg` |

**隐藏代价（重要）**: 阶段越高，**Stun 血魔法越弱**（`effect_coef`）:

| 阶段 | Stun 效果系数 | 实际麻痹 | 实际沉默 |
|---|---|---|---|
| 基础 | ×1 | 16s | 12s |
| Risen | ×0.4 | 6.4s | 4.8s |
| Ascendent | ×0.1 | **1.6s** | **1.2s** |

（来源 `blood_magic.dm` stun/cast_spell：`effect_coef = 1 → risen 0.4 → ascendent 0.1`）——邪教越壮大，眩晕手段越被"天道平衡"，必须靠构造体/人数碾压。

---

## 四、领袖系统全录

**授予**（`make_cult_leader`，首个拥有者的 datum 或传位）: HUD 变 `"cultmaster"`、注册死亡播报、授予 4 个专属 action（Final Reckoning 仅在未用尽时给）+ 全队播报 "is your cult's Master!"。管理员可 `Make Cult Leader` / `Demote From Leader`。

### 4.1 Final Reckoning（终局清算）

| 参数 | 值 |
|---|---|
| 类型 | **单次使用**（`reckoning_complete = TRUE` 后移除）|
| 咏唱 | 4 段 × **3s** do_after（共 **12s**，期间有火花/相位特效与 `narsie_attack.ogg` 音量 50/75/100 递进）|
| 效果 | 全体**存活**教徒传送至领袖身边（含灵魂石内的 Shade——`release_shades`）|
| 限制 | **召唤点区域内禁用**（防滥用，需全员自己站圈）|
| 前提 | `GLOB.cult_narsie` 不存在（Nar'Sie 已降临则不可用）|

### 4.2 血标 Mark Target（cultmark）

| 参数 | 值 |
|---|---|
| 标记时长 | **90s**（`cult_mark_duration`）|
| 冷却 | **2 分钟**（`cult_mark_cooldown_duration`）|
| 施法距离 | 视线 **7 格**（`view(7)`）|
| 效果 | 全队收到 "top priority" 播报 + `SFX_HALLUCINATION_OVER_HERE` 音效 + 目标**红色箭头 HUD**（navigate_arrow，`HUD_CULTIST_ARROW`）+ 血标发光图像 |
| 目标消失 | 目标被删自动清除（`unset_blood_target_and_timer`）|

**幽灵版**（`ghostmark`，教徒死后可用）: 时长 **60s**、冷却 **60s**、标记当前环绕目标/脚下，可清除已有血标。

### 4.3 Eldritch Pulse（异界脉冲）

| 参数 | 值 |
|---|---|
| 冷却 | **15s**（`pulse_cooldown_duration`）|
| 选择距离 | 视线 **7 格** |
| 传送上限 | 目标与落点距离 **< 16 格** |
| 目标 | 点击**教徒**（living 且 IS_CULTIST）或**邪教结构**（`/obj/structure/destructible/cult`）|
| 效果 | 两段式：先"抓取"再点击落点传送（`do_teleport`，`TELEPORT_CHANNEL_CULT`）+ 火花/光束特效 |
| 音效 | `thudswoosh.ogg`（抓取）/`exit_blood.ogg`（传送）|

### 4.4 Pass the Mantle（传位）与死亡播报

- **Pass the Mantle**: **全场仅 1 次**（`leader_passed_on`），传位后原领袖被 demote（收回全部能力），目标需同意（`tgui_alert` + magic.ogg 33 音量提示）。
- **deathrattle（死亡播报）**: 领袖死亡且 **Nar'Sie 未被召唤**时，全队收到 `veryfar_noise.ogg` + "The Cult's Master, [名字], has fallen in [区域]!"——暴露位置，逼迫队员救援/复仇。

---

## 五、符文全录（11 种：10 可画 + 1 畸形）

> 全部符文是 `/obj/effect/rune/` 类型（`runes.dm` 1,214 行），由 `generate_cult_rune_types()` 收集 `can_be_scribed=TRUE` 者（malformed 不可画）。基础绘制 **4s**（`scribe_delay = 4 SECONDS`），可在邪教地板上加速（构造体版 create_rune 在 cult 地板上 ×0.5）。咏唱者需能说话（mime 除外）、清醒、在 1 格内（多教徒时）。

| # | 符文 | 效果 | 需求 | 咏唱 | 关键数值 |
|---|---|---|---|---|---|
| 1 | **Offer 献祭** | 转化/活祭/祭品进魂石 | 转化 2 / 活祭 3 | Mah'weyh pleggh at e'ntrath! | 转化：治疗 75% 钝/灼伤+10s 昏迷+给匕首；活祭：Barhah hra zar'garis! |
| 2 | **Empower 强化** | 血魔法更便宜/更快 | 1 | Ra'sha yoka! | 血魔法雕刻 10s→4s、失血 40→8、充能 1→4 |
| 3 | **Teleport 传送** | 全站符文网络互传 | 1 | —（keyword 命名）| 网络成员登记 GLOB.teleport_runes |
| 4 | **Nar'Sie 召唤** | 召唤古神（只能在对的地点）| **9** | TOK-LYR RQA-NAP G'OLT-ULOFT!! | 绘制 **50s**、自伤 **40.1/次**、擦除 5s、每局 1 次、不可隐藏、96×96 大符文 |
| 5 | **Revive 复活** | 复活教徒（每 3 献祭 1 次）| 1 | Pasnar val'keriam... | 开局免费 1 次；可复活死亡/AFK/无客户端教徒；AFK 者触发幽灵投票接管（5s）|
| 6 | **Barrier 屏障** | 不可穿过屏障 | 1 | Khari'd! Eske'te tannin! | 生成 `emergency_shield/cult/barrier`，再咏唱解除；施法者手臂 -2 钝伤 |
| 7 | **Summon Cultist 召唤教徒** | 指定教徒传送至符文 | 2 | N'ath reth sh'yro eth d'rekkathnor! | 施法者 **10 自伤**；失败条件：死亡/被拖拽/被铐/离开站内层 |
| 8 | **Boil Blood 血沸** | 视线内非教徒血沸 | 3 | Dedo ol'btoh! | 施法者 10 自伤；**25 伤 ×3 波**（乘数 0.5/1/1.5）+ 火场；magic 免疫可挡 |
| 9 | **Spirit Realm 灵魂界** | 幽灵复制体教徒（限 3）| 1 | Gal'h'rfikk... | 幽灵以复制体行动 |
| 10 | **Apocalypse 天启** | 消耗召唤点：全站幻影+EMP+灾难 | 3 | Ta'gh fara'qha fel d'amar det! | 绘制 **100 tick（10s）**；消耗 1 个召唤点；EMP 0.42×强度；强度 = max(60, 360−360×(教徒占比+0.3)²)；≥285（教徒 <15%）触发灾难表 |
| 11 | **Malformed 畸形** | **不可画**（彩蛋/管理员）| — | Ra'sha yoka! | **30 自伤**，纯害无益 |

**咏唱通用规则**（base `invoke`）: 所有咏唱者齐喊 invocation（`forced = "cult invocation"`）；有 `invoke_damage` 者全员承受 BRUTE 伤害（"saps your strength!"）；`fail_invoke` 红光闪烁 + fizzle。持有 **narplush 玩偶**的咏唱者只会"吱吱叫"。

> **审计修正**：旧版宣称"12 符文"错误——`generate_cult_rune_types()` 只收可画符文，真值 **10 可画** + malformed 不可画 = 11 定义。

---

## 六、血魔法全录（10 种）

> 血魔法是 `blood_magic.dm`（971 行）中的 `/datum/action/innate/cult/blood_spell/*`——新一代"护身符"系统，耗血施法。**雕刻**（Prepare Blood Magic）: 默认 **10s**（`do_after`），Empower 符文旁 **4s**，Crimson Medallion 强化再 **×0.5**；完成后**失血 40**（符文旁 8）。

| # | 血魔法 | 效果 | 充能/代价 | 咏唱 |
|---|---|---|---|---|
| 1 | **Stun 眩晕** | 接触麻痹+沉默+口吃 | 1 次/10 手臂伤 | Fuu ma'jin! |
| 2 | **Teleport 传送** | 接触教徒→选传送符文 | 1 次/7 手臂伤 | Sas'so c'arta forbici! |
| 3 | **EMP 电磁脉冲** | 大范围 EMP | 1 次/10 手臂伤 | Ta'gh fara'qha fel d'amar det! |
| 4 | **Shadow Shackles 暗影枷锁** | 接触上手铐+沉默 | **4 次** | In'totum Lig'abis! |
| 5 | **Twisted Construction 扭曲建造** | 铁 50→构造壳 / 塑料→符文金属 1:1 / 活 borg→构造体（9s）/ borg 壳→构造壳 / 气闸→符文气闸（5s）/ 净化魂石→邪教魂石 | 1 次/12 手臂伤 | Ethra p'ni dedol! |
| 6 | **Summon Combat Equipment 召唤战斗装备** | 黑制服+邪教袍 alt+邪教鞋+邪教背包+匕首+邪教 Bola | 1 次 | — |
| 7 | **Summon Ritual Dagger 召唤仪式匕首** | 再召一把仪式匕首 | 1 次 | Wur d'dai leev'mai k'sagan! |
| 8 | **Hallucinations 幻觉** | 7 格内目标 **240s 幻觉**+血光外观（4 分钟）| **4 次** | 无声无影 |
| 9 | **Conceal Presence 隐藏存在** | 隐藏/显现符文（5/7 格）与结构（5/6 格）| **10 次** | Kla'atu barada nikt'o! |
| 10 | **Blood Rites 血之仪式** | 吸/补血+治疗+高级仪式 | **5 次不销毁** | Fel'th Dol Ab'orod! |

**Stun 详细数值**: 麻痹 `16s × 系数`（基础 1/Risen 0.4/Ascendent 0.1，见 §三）、沉默 `12s × 系数`、口吃+邪教口音 30s、抖动 30s；对**异端**仅 0.5s 击倒+眩晕 3s（弱化）；对**硅基**重 EMP；可被 `can_block_magic()` 免疫（NOVA 追加：对**时钟教徒**无效）。

**Shackles 详细数值**: 3s do_after 上手铐（`/obj/item/restraints/handcuffs/cult`）+ **10s 沉默**。

**Blood Rites（manipulator）机制**（高级血魔法核心）:

| 行为 | 数值 |
|---|---|
| 充能换算 | **1 充能 = 2 血单位**（`USES_TO_BLOOD 2`）|
| 吸活人血 | 每次 **-100 血量**、**+50 充能**（`BLOOD_DRAIN_GAIN 50`）|
| 吸地上血 | 血渍 bloodiness ×0.6 转充能（2 格内）|
| 治疗教徒 | 先补血至安全线，再 1:1 治伤（按伤型比例分配）|
| **自疗惩罚** | 效率 ÷**1.65**（`SELF_HEAL_PENALTY`）|
| 治疗构造体 | 1 充能 = 1 HP |
| **血长戟** | 消耗 **150** 充能 → 邪教长戟（挥舞 17/双手 24）|
| **血弹幕** | 消耗 **300** → arcane barrage 血魔杖 |
| **血光束** | 消耗 **500** → blood_beam 光束武器 |

**充能上限**: 无符文 **1** / Empower 旁 **4** / 强化 **5**。上限满时可选删除旧法术。

---

## 七、构造体全录（6 种本体）

> 构造体本体在 `code/modules/mob/living/basic/cult/constructs/`（+`shade.dm`），灵魂石捕获灵魂注入铁壳生成。铁 **50**（IRON_TO_CONSTRUCT_SHELL_CONVERSION）→ 构造壳 → 注入灵魂 → 构造体。borg 可被扭曲建造直接转化（9s 引导）。**Nar'Sie 本体点击**：幽灵可自愿成为 Harvester。

| # | 构造体 | HP | 近战 | 定位 |
|---|---|---|---|---|
| 1 | **Juggernaut 巨人** | **150** | **25** | 坦克/推线——重型护盾，拆墙主力 |
| 2 | **Wraith 幽灵** | 65 | **20** | 刺客——穿墙潜行背刺 |
| 3 | **Artificer 工匠** | 50 | **5** | 基建——造墙/修构造体/召唤符文（create_rune：1800 tick 冷却、60 tick 绘制、cult 地板 ×0.5）|
| 4 | **Harvester 收割者** | 40 | **15-20** | 收割——吸魂/处决 |
| 5 | **Proteon 变体** | 35 | **8-10** | 万金油——多变形态 |
| 6 | **Shade 阴影** | **40** | **5-12** | 基础幽灵（shade.dm；邪教版 `shade_cult` 外观）|

> **审计修正**：旧版 Harvester 近战 15 / Proteon 8 / Shade 无数据——源码实为 **15-20 / 8-10 / 40HP、5-12 近战**。

**生成链**: 铁 50 → 构造壳 → 灵魂石注入灵魂 → 构造体；亦可献祭 borg 直接选类目生成（`make_new_construct_from_class`，THEME_CULT）。

---

## 八、Nar'Sie 召唤与本体

**代码**: `narsie.dm`（336 行）+ 召唤符文（`runes.dm` 580-696）。

### 8.1 召唤条件（全参数）

| 参数 | 值 |
|---|---|
| 弱界点 | **3 个**（`SUMMON_POSSIBILITIES`，VALID_TERRITORY 站内区）|
| 咏唱者 | **9 教徒**（req_cultists = 9）|
| 绘制时间 | **50s**（scribe_delay，`no_scribe_boost` 不可加速）|
| 绘制自伤 | **40.1/次**（scribe_damage）|
| 擦除时间 | 5s（erase_time）|
| 限制 | 每局 **1 次**（used 标记）；仅限召唤点；Nar'Sie 已在场则失败；不可被 conceal |

**召唤过程**: 绘制开始 → 全站星光/视差变暗（9 段渐变至 `#c21d57`，每段 8s）→ `set_narsie_count` 全局计数（多队并行时叠加）→ 成功 `dimensional_rend.ogg` → 4s 后符文变红 → **`/obj/narsie` 生成**（即使符文被拆也生成）→ `start_ending_the_round()`。失败则星光回退（4 段×8s）。

### 8.2 Nar'Sie 本体数值

| 属性 | 值 |
|---|---|
| 吞噬半径 | **12**（NARSIE_CONSUME_RANGE）|
| 引力 | **10**（NARSIE_GRAV_PULL）|
| 奇点尺寸 | **12**（NARSIE_SINGULARITY_SIZE）|
| 魅惑 | 每 tick **25%** 概率，12 格内非教徒 **60 眩晕**（6s）|
| 换目标 | **5%**/tick 概率重选（教徒优先被吃）|
| 防御 | **INDESTRUCTIBLE**（不可摧毁，但 `CAN_BE_HIT`）|
| 灵魂目标 | `soul_goal = round(1 + 非教徒存活数 × 0.75)`——喂够灵魂才开始终局 |
| 降临播报 | `NAR'SIE HAS RISEN` + `narsie_rises.ogg` |
| 幽灵互动 | 点击 Nar'Sie → 自愿成为 **Harvester**（禁 ROLE_CULTIST 者）|
| 死亡 | `fall_of_the_harbinger`：`Nooooo.../Not die. How-/Die. Mort-/Sas tyen re-` + `demon_dies.ogg` → 目标判定 **CULT_NARSIE_KILLED**（大失败）|

**降临后团队效果**: Nar'Sie 成为**无限期血标**；所有教徒 `narsie_act()`；`narsie_summoned = TRUE` 时全队获得 **narsupreme 成就**（第二次召唤时发）。

---

## 九、邪教物品与结构

### 9.1 符文金属配方（`runed_metal.dm`，6 配方 + 1 平台）

| 结构 | 符文金属消耗 | 施工 | 说明 |
|---|---|---|---|
| **神龛 Pylon** | **4 片** | 4s | 治疗+回血附近教徒/构造体；地板变符文地面（加速画符）|
| **祭坛 Altar** | **3 片** | 4s | 制作 Eldritch 磨刀石/构造壳/不圣水烧瓶 |
| **档案 Archives** | **3 片** | 4s | 制作狂热者蒙眼布/穿梭机诅咒球/Veil Walker 装备；发光 |
| **恶魔熔炉 Daemon Forge** | **3 片** | 4s | 制作 Nar'Sien 硬化护甲/异界长剑；发光 |
| **符文门 Runed Door** | — | 5s | 弱门，非教徒触碰被眩晕 |
| **符文梁 Runed Girder** | — | 5s | 弱梁，匕首一击可拆（不推荐）|
| 符文石台 Runed Platform | 2 片 | 3s | CAT_STRUCTURE 平台 |

### 9.2 其他结构

| 结构 | 说明 |
|---|---|
| 屏障发生器（shieldgen）| 邪教屏障 |
| 邪教气闸（airlock）| 专属气闸（扭曲建造转化）|
| 血感 HUD（bloodsense alert）| 显示血标方位箭头+被血魔法污染的血 |
| 构造壳（constructshell）| 铁 50 转化 / 祭坛制作 |

### 9.3 武器数值（`cult_items.dm`）

| 武器 | 近战 | 投掷 | 备注 |
|---|---|---|---|
| 仪式匕首（dagger）| **15** | **25** | 转化工具；画符 |
| 邪教长剑（cultblade）| **30** | 10 | 预览图标用 |
| 邪教长剑（挥舞型）| 30 | 25 | 需手部 |
| 血长戟（halberd）| 17（双手 **24**）| — | 血之仪式 150 充能 |
| 符文狼牙棒/其他 | 19 / 17 | — | — |

### 9.4 邪教护甲（cult_armor.dm 271 行）

- 邪教徒护甲套装（祭司袍/头盔等），血之仪式装备法术直接配发。

### 9.5 邪教通信（cult_comms.dm 447 行）

- **Communion（圣餐）**: 全教徒私密频道（附近非教徒可偷听！警告文案明示）；说话前自动喊 "O bidai nabora se'sma!"；标题按身份 Acolyte/Master/Construct。**Spiritual Communion**: 幽灵版。
- 其余为领袖能力（见 §四）。

---

## 十、风味与彩蛋

### 10.1 音效全录

| 事件 | 音效 |
|---|---|
| 获得反派 | `bloodcult_gain.ogg`（stinger_sound）|
| Risen 红眼 | `bloodcult_eyes.ogg` |
| Ascendent 光环 | `bloodcult_halos.ogg` |
| 召唤成功 | `dimensional_rend.ogg` |
| Nar'Sie 降临 | `narsie_rises.ogg` |
| Nar'Sie 被杀 | `demon_dies.ogg` |
| 领袖死亡 | `veryfar_noise.ogg`（全队）|
| 血标 | `SFX_HALLUCINATION_OVER_HERE`（75 音量）|
| 复活 | `bloodcult_gain.ogg`（被复活者）|
| 天启 | 随机 `bloodcult_gain/ghost_whisper/ghosty_wind` + `pope_entry.ogg`（教徒侧）|

### 10.2 心情与播报

- 教徒心情 **+12**（`/datum/mood_event/cult`，hidden）——"I have seen the truth, praise the almighty one!"
- 自杀口号: `FOR NAR'SIE!!`
- 转化播报: "even as their wounds heal and close"（有伤时）
- 献祭反馈: 目标祭品 → `"Yes! This is the one I desire! You have done well."`；人类/borg → `"I accept this sacrifice."`；其他 → `"I accept this meager sacrifice."`

### 10.3 彩蛋

- **Herbert West**: 名为 "Herbert West" 的教徒咏唱复活符文时台词变为 "To life, to life, I bring them!"（致敬《活跳尸》）
- **April Fools**: 4 月 1 日转化有 **10%** 概率触发 "That son of a bitch! They're in. (April Fools)" + 10s 麻痹（替代昏迷）
- **narplush 玩偶**: 咏唱者持有 Nar'Sie 玩偶时符文"吱吱叫"不咏唱
- **narsupreme 成就**: 邪教成功召唤 Nar'Sie 时全员解锁
- **管理员彩蛋**: VV 面板可直接 "Begin Nar'Sie Roundender"（警告：几分钟内结束回合）

---

## 十一、NOVA 专属改动

> 注：旧版 8.3 的"NOVA EDIT"条目（蜘蛛/绑架者改动）经查属于**其他反派系统**，与邪教无关——本版不采信。

| 改动 | 位置 | 内容 |
|---|---|---|
| **I18N CODEMOD** | 全 cult 目录 | 玩家可见字符串全部 `LANG()` 中文化（modular_nova/modules/i18n/）|
| **ANTAG OPT-IN** | cult_objectives.dm | 献祭目标候选必须 `opt_in_valid(player)`（未开启反派偏好者不可被选为祭品），除非配置 `disable_antag_opt_in_preferences` |
| **Interlink 排除** | cult_objectives.dm | 回合进行中位于中央指挥区（interlink）的玩家不可成为祭品 |
| **时钟免疫** | blood_magic.dm | 血魔法 Stun 对时钟教徒（IS_CLOCK）无效（金色变色反馈）|
| **I18N roundend** | cult_team.dm | 目标文本 lang_reverse_text 反转处理 |

---

## 十二、对战攻略

### 怎么玩邪教（推荐流程）

```
① 开局：仪式匕首+符文金属×10 → 画 Offer 符文（基础绘制 4s）
② 转化：抓落单船员 → 2 教徒 Offer 转化（治疗 75%+10s 昏迷）→ 滚雪球
③ 建结构：神龛(4)/熔炉(3)/档案(3)/祭坛(3)
④ 血魔法：Empower 符文旁雕刻（4s 而非 10s）→ 召唤装备 → 武装教徒
⑤ 献祭 3 次 → 解锁复活符文（开局有 1 次免费复活！）
⑥ 构造体：铁 50 → 壳 → 灵魂石 → Juggernaut(150HP) 推线
⑦ 成长：教徒占比冲 16.7%→28.6% 触发红眼/光环（注意 Stun 会变弱）
⑧ 终极目标：3 弱界点画 Nar'Sie 符文（9 人、50s、40.1 自伤/次）→ 召唤
⑨ 领袖工具链：血标(90s) 集火 → Final Reckoning 全员集结 → Eldritch Pulse 救人
```

### 怎么防邪教（船员对策）

| 方法 | 说明 |
|---|---|
| **盯符文** | 看到地上的符文立刻破坏——邪教命脉（召唤符文 5s 可擦除）|
| **拆结构** | 神龛/熔炉/祭坛是邪教基建——优先拆 |
| **别落单** | Offer 转化专抓落单——结伴行动 |
| **灵魂石警惕** | 队友突然失踪=可能被抓去献祭 |
| **反召唤** | 看到 9 人咏唱/星光变暗=召唤 Nar'Sie——立即打断（50s 引导，且每次绘制自伤 40.1——他们撑不久）|
| **血魔法反制** | 教徒受伤=血魔法燃料——压制血量；魔法免疫（antimagic）可挡 Stun/血沸 |
| **圣职/异端不可转化** | 牧师、异端、borg 不会被 Offer 转化——可当反邪教中坚 |
| **杀领袖** | 领袖死亡全队收到播报+失去四能力（Nar'Sie 未降临前）|
| **Nar'Sie 本体** | 降临后不可摧毁（INDESTRUCTIBLE）——只能靠喂灵魂拖延？不：阻止它喂够灵魂（soul_goal = 1+75% 非教徒数），或直接结束回合判定 |

---

## 十三、数值速查表

| 项 | 值 |
|---|---|
| 源码 | cult/ 19 文件 5,498 行 + constructs 722 行 + narsie 336 行 |
| Datum | roundend="cultists"｜HUD="cult"(领袖 "cultmaster")｜mood +12｜suicide "FOR NAR'SIE!!"｜pref ROLE_CULTIST |
| 目标 | 每局 2 个：献祭 1 名随机船员 + 3 召唤点召唤 Nar'Sie |
| 召唤点 | 3 个（VALID_TERRITORY 站内区）|
| 献祭目标池 | 首选不可转化者（圣职/异端/硅基），兜底可转化者；interlink/opt-in 排除 |
| 符文 | 10 可画 + 1 malformed；基础绘制 4s |
| 召唤符文 | 9 教徒｜50s 绘制｜40.1 自伤/次｜5s 擦除｜每局 1 次 |
| 血魔法 | 10 种；雕刻 10s（Empower 4s，强化 2s）；失血 40（符文旁 8）|
| 充能上限 | 无符文 1 / Empower 4 / 强化 5 |
| 血之仪式 | 1 充能=2 血；吸活人 -100 血/+50 充能；自疗惩罚 ×1.65 |
| 高级仪式 | 血长戟 150 / 血弹幕 300 / 血光束 500 充能 |
| Risen | 教徒/非教徒比 >0.2（约全员 16.7%）→ 红眼 |
| Ascendent | >0.4（约 28.6%）→ 光环 |
| Stun 削弱 | Risen ×0.4（6.4s）/ Ascendent ×0.1（1.6s）|
| 血标 | 90s 标记 + 2min 冷却 + 7 格；幽灵版 60s/60s |
| Eldritch Pulse | 15s CD + 7 格选择 + 16 格传送上限 |
| Final Reckoning | 单次 + 4×3s 咏唱 + 召唤点禁用 |
| 复活 | 开局免费 1 次，之后每 3 献祭 1 次（sacrifices_used 起始 -3）|
| 构造体 | 巨人 150HP/25｜幽灵 65/20｜工匠 50/5｜收割者 40/15-20｜变体 35/8-10｜阴影 40/5-12 |
| 铁→壳 | 50 铁（扭曲建造/祭坛）|
| Nar'Sie | 吞噬 12｜引力 10｜奇点 12｜魅惑 25%/60 眩晕｜灵魂 1+75% 非教徒｜INDESTRUCTIBLE |
| 天启符文 | 3 教徒｜10s 绘制｜耗 1 召唤点｜强度≥285（教徒<15%）触发灾难表 |
| 武器 | 匕首 15/25｜长剑 30｜血长戟 17（双手 24）|
| 心情 | +12（hidden）|
| **审计修正** | ①"信徒比 20%/40%"→实为教徒/非教徒存活比（≈16.7%/28.6%）②Shade 有 40HP ③收割者 15-20 ④变体 8-10 ⑤"12 符文"→10 可画+1 malformed |

---

*本文档数值全部实测自 `code/modules/antagonists/cult/`（19 文件 5,498 行）+ constructs/ + narsie.dm + runed_metal.dm 源码，无推测。*
