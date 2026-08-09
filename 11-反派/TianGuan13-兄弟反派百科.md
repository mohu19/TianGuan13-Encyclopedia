# TianGuan13 · 兄弟（Brother / Blood Brother）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/brother/brother.dm`（1 文件 331 行）
> **类型**: 团队型转化反派（结拜制）｜**难度**: ★★（人数即力量，转化链决定上限）
> **一句话**: 你是辛迪加血亲兄弟——开局自带 **闪光弹**，用 **从背后闪光** 把船员**转化**成你的兄弟，共享同一套目标；**兄弟不死目标不完**（convert_brother 目标要求队里 ≥2 人）。
> **本版全量审计**: 从源码全量提取结拜机制、antag datum 属性、共享目标生成公式、NOVA 改动与风味彩蛋，数值全部实测自 `brother.dm` 331 行。

---

## 目录

- [一、核心概念](#一核心概念)
- [二、Antag Datum 核心属性全录](#二antag-datum-核心属性全录)
- [三、结拜机制（转化链全流程）](#三结拜机制转化链全流程)
  - [3.1 转化检查链（8 道门槛）](#31-转化检查链8-道门槛)
  - [3.2 转化成功效果](#32-转化成功效果)
  - [3.3 转化技能授予/收回](#33-转化技能授予收回)
- [四、兄弟团队（brother_team）](#四兄弟团队brother_team)
  - [4.1 队伍生命周期](#41-队伍生命周期)
  - [4.2 招募槽（brothers_left）](#42-招募槽brothers_left)
  - [4.3 队伍改名机制](#43-队伍改名机制)
- [五、共享目标系统（全量）](#五共享目标系统全量)
  - [5.1 目标生成公式](#51-目标生成公式)
  - [5.2 通用目标分支](#52-通用目标分支)
  - [5.3 convert_brother 目标](#53-convert_brother-目标)
- [六、对战攻略](#六对战攻略)
- [七、风味彩蛋](#七风味彩蛋)
- [八、NOVA 专属改动](#八nova-专属改动)
- [九、数值速查表](#九数值速查表)

---

## 一、核心概念

| 机制 | 值 |
|---|---|
| 阵营 | 辛迪加（Syndicate）系团队反派 |
| 核心玩法 | 开局 1 名兄弟 + 2 个招募槽 → 闪光转化船员入伙 |
| 共享目标 | 全队共享同一套目标（首个兄弟生成时铸造，见第五章） |
| 强制目标 | **convert_brother**（转化目标，必给）——`check_completion: length(team.members) > 1` |
| 团队 HUD | 队友可见（`add_team_hud`，has_antagonist 替代外观） |
| 转化武器 | `/obj/item/assembly/flash`（手持闪光弹，开局**必给** 1 枚） |
| 转化限制 | 不能转化：兄弟自己的目标/已入伙者/心灵护盾者（TRAIT_UNCONVERTABLE） |
| 生死与共 | 队员离队（remove_member）→ 移除其兄弟 datum + 全员警告"X is no longer your brother!"；队空 → 队伍 qdel |

**NOVA EDIT**：`view_exploitables = TRUE`（`modular_nova/master_files/code/modules/antagonists/_common/antag_datum.dm` L46-47，**无标记覆写**——管理员面板可查看兄弟的可利用信息）

---

## 二、Antag Datum 核心属性全录

`/datum/antagonist/brother`（brother.dm L2-15）：

| 属性 | 值 | 说明 |
|---|---|---|
| `name` | `\improper Brother` | 显示名 |
| `antagpanel_category` | `"Brother"` | 管理面板分类 |
| `pref_flag` | `ROLE_BROTHER` | 即 `"Blood Brother"`（role_preferences.dm L12）——玩家偏好开关 |
| `special_role` | `ROLE_BROTHER` | 特殊角色标识 |
| `antag_hud_name` | `"brother"` | 管理 HUD 标签 |
| `hijack_speed` | **0.5** | 劫机速度系数（`hijack_speed()` proc：目标覆写 `hijack_speed_override` 优先，否则用此值） |
| `ui_name` | `"AntagInfoBrother"` | 玩家信息 UI |
| `suicide_cry` | `"FOR MY BROTHER!!"` | 自杀口号 |
| `antag_moodlet` | `/datum/mood_event/focused` | 心情事件（见第七章：**+8**、hidden=TRUE） |
| `hardcore_random_bonus` | **TRUE** | hardcore 模式随机奖励 |
| `stinger_sound` | `'sound/music/antag/traitor/tatoralert.ogg'` | 出场警报音效（与叛徒同款） |
| `VAR_PRIVATE team` | `/datum/team/brother_team/team` | 队伍引用（私有） |
| `view_exploitables` | **TRUE**（NOVA 覆写） | 管理员面板可见可利用信息（master_files 无标记覆写） |

### 队伍生命周期钩子

| Proc | 行为 |
|---|---|
| `create_team(new_team)` | 无参则 `team = new()`；类型错误 stack_trace |
| `get_team()` | 返回 team 引用 |
| `on_gain()` | ① `objectives += team.objectives`（**继承共享目标**）② `finalize_brother()`（放 stinger + 改队名）③ `brothers_left <= 0` 则跳过转化技能 ④ `grant_conversion_skills()` ⑤ **`equip_conspicuous_item(new /obj/item/assembly/flash)`**（必给闪光弹，口袋→背包，失败删除）⑥ 非首兄弟收到提示（见第七章） |
| `on_removal()` | `remove_conversion_skills()` 后走父类 |
| `apply_innate_effects()` | `add_team_hud`——给全队加队友 HUD（has_antagonist 替代外观） |
| `greet()` | `span_alertsyndie`"你是一名血亲兄弟。" + `announce_objectives()` 播报目标 |
| `ui_static_data()` | 返回 `antag_name` + `objectives`（目标列表实时同步给 UI） |
| `antag_panel_data()` | `"Conspirators : [队友名列表] | Remaining: [brothers_left]"` |
| `get_admin_commands()` | 追加 **"Adjust Remaining Conversions"**（调整剩余转化次数） |
| `update_recruitments_remaining(admin)` | tgui 数字输入，**default=1, min_value=0** → `team.set_brothers_left()` |
| `admin_add()` | 管理员直接拉人入伙：新建 team + add_member + 双日志 |

### 预览图标（get_preview_icon，L133-165）

| 角色 | 种族 | 外观 | 制服 |
|---|---|---|---|
| brother1 | 以太体（ethereal）| 以太色 **Faint Red** | 军需官制服（quartermaster）+ 脸部血渍（maskblood） |
| brother2 | 蛾人（moth）| NOVA 定制（见第八章）| 科学家制服（scientist/consistent）+ 制服血渍（uniformblood） |

血渍图标以 `BLOOD_COLOR_RED` 乘算叠加，两兄弟图标分别向西/东偏移 **8 像素** 后合并。

---

## 三、结拜机制（转化链全流程）

转化由信号 `COMSIG_MOB_SUCCESSFUL_FLASHED_MOB` 触发（`on_mob_successful_flashed_mob`，L68-117）——**兄弟用闪光弹成功闪到目标后**，走以下判定链。

### 3.1 转化检查链（8 道门槛）

按顺序判定，任一命中即终止（返回）：

| # | 检查 | 源码 | 结果（气球提示） |
|---|---|---|---|
| 1 | 目标已死 | `flashed.stat == DEAD` | 静默返回 |
| 2 | 目标是硅基/无人机 | `issilicon(flashed) || isdrone(flashed)` | 静默返回 |
| 3 | 目标非清醒 | `flashed.stat != CONSCIOUS` | "昏迷！" |
| 4 | 目标无 mind 或非玩家 | `isnull(flashed.mind) || !GET_CLIENT(flashed)`（TESTING 下仅查 mind） | "{0}的思想是空的！" |
| 5 | **目标是兄弟自己的刺杀目标** | 遍历 `source.mind.get_all_objectives()`，`flashed == brother_objective.target?.current` | "that's your target!"（不可转化自己的目标） |
| 6 | 目标已是兄弟 | `flashed.mind.has_antag_datum(/datum/antagonist/brother)` | "{0}忠于别人了！" |
| 7 | 目标不可转化 | `HAS_MIND_TRAIT(flashed, TRAIT_UNCONVERTABLE)` | "{0}抵抗{1}！"（心灵护盾植入体授予 `TRAIT_MINDSHIELD + TRAIT_UNCONVERTABLE`，implant_mindshield.dm L28） |
| 8 | 招募失败兜底 | `team.add_brother()` 返回 FALSE | "失败！" |

### 3.2 转化成功效果

1. **日志三连**：`source.log_message("converted [X] to blood brother", LOG_ATTACK)` + 被转化方日志 + `log_game()`（含 converted/converted by 字段）。
2. **`flash.burn_out()`**——转化用的闪光弹**当场烧毁**（burnt_out = TRUE，flash.dm L75）。
3. **记忆**：被转化方 mind 添加 `/datum/memory/recruited_by_blood_brother`（general_memories.dm L960）——2 条起始文案 + 1 条名字文案：
   - "[X] acts just a bit too friendly with [Y], moments away from converting them into a blood brother."
   - "[Y] is brought into [X]'s life of crime and espionage."
4. **气球提示**"已转化"。
5. 全队广播：`"[新兄弟名字] has been converted to aid you as your brother!"`；若 `brothers_left <= 0` 追加"You cannot recruit any more brothers."

> **转化后**：新兄弟自动获得完整兄弟 datum（`add_antag_datum(/datum/antagonist/brother, src)`）→ 走 `on_gain()`（继承共享目标 + 拿自己的闪光弹 + 转化技能）。**转化是滚雪球式的：每个兄弟都能再转化下一个**，直到招募槽耗尽。

### 3.3 转化技能授予/收回

| Proc | 动作 |
|---|---|
| `grant_conversion_skills()` | ① `AddComponentFrom(REF(src), /datum/component/can_flash_from_behind)` ② `RegisterSignal(COMSIG_MOB_SUCCESSFUL_FLASHED_MOB, on_mob_successful_flashed_mob)` |
| `remove_conversion_skills()` | 反向：RemoveComponentSource + UnregisterSignal |

**can_flash_from_behind 组件**（`code/datums/components/can_flash_from_behind.dm`）：`dupe_mode = COMPONENT_DUPE_SOURCES`；监听 `COMSIG_MOB_PRE_FLASHED_MOB`，把**正面全闪强制降级为部分闪**（`DEVIATION_FULL → DEVIATION_OVERRIDE_PARTIAL`，其余返回 NONE）——即兄弟**只能从背后/侧面成功闪晕目标**，正面闪光无效（部分闪），这是"从背后转化"机制的核心。

**招募槽归零联动**（`set_brothers_left`）：`brothers_left` 从 0→正 时全员 `grant_conversion_skills()`；正→0 时全员 `remove_conversion_skills()`——**槽耗尽 = 全员失去转化能力**（防止越过槽位继续转化）。

---

## 四、兄弟团队（brother_team）

`/datum/team/brother_team`（L207-322）：

| 属性 | 值 |
|---|---|
| `name` | `\improper Blood Brothers` |
| `member_name` | `"blood brother"` |
| `brothers_left` | **2**（初始招募槽） |

### 4.1 队伍生命周期

| 事件 | 行为 |
|---|---|
| `New()` | **10% 概率** `brothers_left += 1`（即 10% 局开局 3 槽） |
| `add_member(new_member)` | ① 目标为空则 `forge_brother_objectives()`（**首个成员触发目标铸造**）② 若新成员没有兄弟 datum → `add_brother(new_member.current)`（roundstart 首兄弟路径，不花槽）③ 否则（已带 datum 的加入者）→ `set_brothers_left(brothers_left - 1)` **花 1 个招募槽** |
| `remove_member(member)` | ① 移除其兄弟 datum ② 队空 → `qdel(src)` ③ 否则全员警告"X is no longer your brother!" ④ `update_name()` |
| `add_brother(new_brother, source)` | 前置检查（null/mind/无客户端/已是兄弟）→ `add_antag_datum`（经 add_member **花 1 槽**）→ 全员广播；失败返回 FALSE |

> **槽位消耗点**：只有一条——`add_member` 中"已带兄弟 datum 的加入者"。roundstart 首兄弟与转化入伙者都经 `add_brother()`，但前者在 add_member 里不花槽，后者（`add_antag_datum` → 内部 add_member）才花。源码注释原文："the only place a joining member spends a conversion slot; converts get here via add_brother()"。

### 4.2 招募槽（brothers_left）

- 初始 **2**，10% 概率 **3**。
- 每转化 1 人消耗 1 槽。
- 槽 = 0 → 全员失去转化能力（见 3.3）+ 全队提示"不能再招募"。
- 管理员经"Adjust Remaining Conversions"可任意改（default 1, min 0），改回正数重新授予转化技能。

### 4.3 队伍改名机制

`update_name()`（L264-281，**NOVA i18n 重写**）：

- 取全员**姓氏**（`splittext(name," ")` 末段）拼接。
- **单人**：模板 `nametmpl_brother_team_solo` → 中文 **"{0}的孤立起义"** / 英文 "{0}'s Isolated Intifada"。
- **多人**：模板 `nametmpl_brother_team` → 中文 **"{1}的{0}"**（如 "张三 & 李四的Blood Brothers"）/ 英文 "{0} of {1}"（"Blood Brothers of Zhang & Li"）。
- NOVA 刻意用 `replacetext` 而非 `lang_interpolate`：避免姓氏里的职业词（Cook/Baker 等）被本地化链误译；`locale==en` 时模板即英文，输出逐字不变。
- 原版（ORIGINAL 注释）：`name = "[last_names[1]]'s Isolated Intifada"` / `name = "[initial(name)] of " + last_names.Join(" & ")`——NOVA 仅改拼接方式，语义等价。

---

## 五、共享目标系统（全量）

**目标铸造时机**：`add_member` 首次调用时（首个兄弟入队）→ `forge_brother_objectives()`。此后**全队共享同一份 objectives 列表**（每个兄弟 `on_gain()` 里 `objectives += team.objectives` 继承）。

### 5.1 目标生成公式

`forge_brother_objectives()`（L283-295）：

```
① 清空 objectives，必加 1 个 convert_brother（转化目标）
② is_hijacker = prob(10)                    ← 10% 概率劫机者
③ 普通目标数 N = max(1, brother_objectives_amount + (brothers_left > 2) - is_hijacker)
        brother_objectives_amount 默认 2（config：game_options.dm L90-92，min 0）
        brothers_left > 2 → +1（3 槽局多 1 个目标）
        is_hijacker       → -1（劫机占 1 个名额）
        → 典型 2 个；3 槽局 3 个；劫机者 1 个；最少保底 1 个
④ 循环 N 次 forge_single_objective()
⑤ 终局：劫机者 → 加 hijack（若已有则不加）；否则 → 加 escape（若已有则不加）
```

### 5.2 通用目标分支

`forge_single_objective()`（L297-306）：

| 分支 | 概率 | 条件 | 目标 |
|---|---|---|---|
| 杀人系 | **prob(50)** | — | 进入子分支 |
| ├ 摧毁 AI | 100/玩家数 | 有活跃 AI（`LAZYLEN(active_ais())`）且 `prob(100/GLOB.joined_player_list.len)` | `/datum/objective/destroy`（needs_target=TRUE） |
| ├ 流放 | **prob(30)**（子分支内） | 无 AI 时 | `/datum/objective/maroon`（needs_target=TRUE） |
| └ 刺杀 | 兜底 | 其余 | `/datum/objective/assassinate`（needs_target=TRUE） |
| 偷窃 | **50%** | 非杀人系 | `/datum/objective/steal`（needs_target=TRUE） |

> 与叛徒对比：兄弟**没有**职业目标/殉道终局；终局只在 **劫机（10%）/ 逃脱（否则）** 二选一，且**必含转化目标**——这是兄弟目标系统与叛徒最大的差异。

### 5.3 convert_brother 目标

`/datum/objective/convert_brother`（L324-331）：

| 属性 | 值 |
|---|---|
| `name` | `"convert brother"` |
| `explanation_text` | "Convert a brainwashable person using your flash on them directly. Any handheld flash will work if you lose or break your starting flash."（用闪光弹直接转化可洗脑的人；丢了/坏了开局闪光弹的话**任何手持闪光弹都行**） |
| `admin_grantable` | FALSE（管理员不可手动授予） |
| `martyr_compatible` | TRUE |
| `check_completion()` | `length(team?.members) > 1`——**队里 ≥2 人即完成** |

> **生死与共语义**：只要队伍还有 ≥2 名成员（无论开局首兄弟 + 至少 1 名转化者，还是后期补员），转化目标就视为完成；**孤家寡人（只剩 1 人）目标未完成**——兄弟必须保住/发展队伍。

---

## 六、对战攻略

### 怎么玩兄弟（推荐流程）

```
① 开局：确认共享目标（convert_brother 必做 + 2 个普通目标）+ 拿开局闪光弹
② 选目标：从背后接近船员 → 闪光（正面闪光会被 can_flash_from_behind 降级为部分闪）
③ 转化：目标被闪晕后自动触发转化链（避开自己的刺杀目标/已有兄弟/心灵护盾者）
④ 滚雪球：新兄弟自带闪光弹 + 转化技能 → 继续转化，直到招募槽（初始 2/10% 3）耗尽
⑤ 终局：劫机（10% 局）或逃脱——带着 ≥2 人队伍完成目标
```

### 怎么防兄弟（船员对策）

| 方法 | 说明 |
|---|---|
| **背对墙走** | 兄弟只能从背后/侧面成功闪晕（正面全闪被强制降级部分闪） |
| **护目镜/墨镜** | 防闪光道具（flash 对抗常规手段） |
| **心灵护盾植入体** | 授予 TRAIT_UNCONVERTABLE——直接免疫转化（"抵抗！"） |
| **盯闪光弹** | 兄弟开局必带 1 枚闪光弹，转化后烧毁——发现烧毁的闪光弹 = 附近有兄弟 |
| **防滚雪球** | 转化者自己也会变兄弟——发现转化现场立即击杀/逮捕，别让链条蔓延 |
| **盯队友异常** | 兄弟有队友 HUD（has_antagonist）——被转化者会"突然"加入某人团伙 |

---

## 七、风味彩蛋

| 彩蛋 | 值 |
|---|---|
| **出场警报** | `tatoralert.ogg`（`sound/music/antag/traitor/tatoralert.ogg`）——`finalize_brother()` 里 `play_stinger()`，与叛徒同款警报 |
| **心情事件** | `/datum/mood_event/focused`：**mood_change = +8**、**hidden = TRUE**、描述 "I have a goal, and I will reach it, whatever it takes!"（generic_positive_events.dm L210-213；叛徒/核弹队等同款） |
| **自杀口号** | `"FOR MY BROTHER!!"` |
| **问候语** | 红色警报样式 "你是一名血亲兄弟。"（zh）/ "You are a blood brother."（en，LANG key `datum.aef74aab`） |
| **次兄弟提示** | 非首兄弟收到："辛迪加对你的期望比对其他人更高。他们授予了你额外一枚闪光弹，用来转化另一个人。"（`datum.f59bc4b2`） |
| **转化记忆** | 被转化者生成 `/datum/memory/recruited_by_blood_brother`（2 起始文案 + 1 名字文案，见 3.2） |
| **队名彩蛋** | "X的孤立起义"（单人）/ "{0}的{1}"（多人）——NOVA 中文模板 |
| **管理员面板** | "Conspirators : A and B | Remaining: 2" 格式 |
| **预览图** | 以太体血脸军需官 × 蛾人血衣科学家（见第二章） |

---

## 八、NOVA 专属改动

| # | 位置 | 改动 | 类型 |
|---|---|---|---|
| 1 | brother.dm L1 | 文件头 I18N CODEMOD——玩家可见字符串全部改写为 `LANG()`（key 不可手改） | 标记头（噪音） |
| 2 | brother.dm L140 | brother2 蛾人 `FEATURE_MUTANT_COLOR = "#E5CD99"`（NOVA ADDITION） | 实质 |
| 3 | brother.dm L141-143 | 蛾人外观改 `build_mutant_part()` 体系（触角 Plain / 花纹 SPRITE_ACCESSORY_NONE / 翅膀 Moth (Plain)）——原版用 `dna.features` 直接赋值（NOVA CHANGE ×3，ORIGINAL 注释在案） | 实质 |
| 4 | brother.dm L144 | `set_eye_color(COLOR_WHITE)` 白眼（NOVA ADDITION） | 实质 |
| 5 | brother.dm L270-281 | `update_name()` i18n 整句模板重写——中文语序（"张三 & 李四的Blood Brothers"）与英文相反，逐词反查会出病句；`replacetext` 规避姓氏职业词误译（NOVA CHANGE，ORIGINAL 注释在案） | 实质 |
| 6 | **master_files** `_common/antag_datum.dm` L46-47 | **`view_exploitables = TRUE`**（**无 NOVA EDIT 标记**——master_files 覆写惯例）——兄弟的管理员面板可查看可利用信息 | 实质（易漏） |
| 7 | strings/i18n/zh-Hans/ | 全套中文文案（`_name_suffixes.json` 队名模板 + `datum.json` 10 个 LANG key：aef74aab/f59bc4b2/dc8b5a42/96a90739/9e06faa2/5b91a8f9/31bf8acd/72ad5488/dcd666e6/4f1d1b63） | 实质（i18n） |

> **全查法说明**：`grep -rn "NOVA EDIT" code/modules/antagonists/brother/` 共 6 处（含文件头），实质改动 5 处（#2-5）；第 6 项在 modular_nova/master_files 无标记覆写——**master_files 目录必须单独查**，只信 code/ 里的标记会漏。

---

## 九、数值速查表

| 项 | 值 | 源码 |
|---|---|---|
| 源码规模 | brother.dm 1 文件 **331 行** | — |
| 招募槽初始 | **2** | `brothers_left = 2`（L210） |
| 3 槽概率 | **10%**（New() `prob(10)` → +1） | L214-215 |
| 转化目标 | convert_brother **必给**，≥2 名成员完成 | L286 / L331 |
| 普通目标数 | **max(1, 2 + (槽>2) − 劫机)**：典型 2 / 3 槽局 3 / 劫机者 1 | L289 |
| brother_objectives_amount | 默认 **2**（min 0） | game_options.dm L90-92 |
| 劫机概率 | **10%**（`prob(10)`） | L288 |
| 杀人系概率 | **50%**（`prob(50)`） | L298 |
| 摧毁 AI 概率 | **100/玩家人数**（需有活跃 AI） | L299 |
| 流放概率 | **30%**（杀人系子分支） | L301 |
| 刺杀 | 杀人系兜底 | L304 |
| 偷窃概率 | **50%**（非杀人系） | L306 |
| 终局 | 劫机（10%）/ **逃脱**（否则），互斥去重 | L291-295 |
| hijack_speed | **0.5** | L8 |
| 心情 | focused **+8**、hidden=TRUE | generic_positive_events.dm L212 |
| 自杀口号 | "FOR MY BROTHER!!" | L10 |
| stinger | tatoralert.ogg | L13 |
| hardcore_random_bonus | TRUE | L12 |
| pref_flag | ROLE_BROTHER（"Blood Brother"） | role_preferences.dm L12 |
| view_exploitables | **TRUE**（NOVA master_files 覆写） | antag_datum.dm L47 |
| 管理员改槽 | default **1**、min **0** | L128 |
| 预览图偏移 | 左右各 **8 像素** | L151/157 |
| 转化武器 | `/obj/item/assembly/flash`（成功后 burn_out 烧毁） | L40 / L111 |

---

*本文档数值全部实测自 `code/modules/antagonists/brother/brother.dm`（331 行）+ `code/datums/components/can_flash_from_behind.dm` + `code/datums/mood_events/generic_positive_events.dm` + `code/controllers/configuration/entries/game_options.dm` + `code/__DEFINES/role_preferences.dm` + `code/datums/memory/general_memories.dm` + `modular_nova/master_files/code/modules/antagonists/_common/antag_datum.dm` + `strings/i18n/zh-Hans/` 源码，无推测。*
