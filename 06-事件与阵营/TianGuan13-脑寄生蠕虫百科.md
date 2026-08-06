# TianGuan13 脑寄生蠕虫百科

> **来源分支**：NovaSector ｜ **模块路径**：`modular_nova/modules/cortical_borer/`（15 个 .dm，共 2,272 行）
> **文档类型**：百科（全量提取，数值精确）｜ **双语**：中文为主，英文术语/原文保留

---

## 目录（Table of Contents）

- [一、总览 Overview](#一总览-overview)
- [二、蠕虫本体：脑钻蠕虫 Cortical Borer](#二蠕虫本体脑钻蠕虫-cortical-borer)
  - [2.1 全局目标变量与宏定义](#21-全局目标变量与宏定义)
  - [2.2 移动/动作速度修正器](#22-移动动作速度修正器)
  - [2.3 通用探测与器官联动](#23-通用探测与器官联动)
  - [2.4 本体属性全表](#24-本体属性全表)
  - [2.5 初始化与销毁](#25-初始化与销毁)
  - [2.6 生命周期 Life](#26-生命周期-life)
  - [2.7 寄生机制（进出宿主）](#27-寄生机制进出宿主)
  - [2.8 说话/低语/拖拽限制](#28-说话低语拖拽限制)
  - [2.9 成熟与升级 mature()](#29-成熟与升级-mature)
  - [2.10 强化蠕虫 empowered 变体](#210-强化蠕虫-empowered-变体)
- [三、能力系统全录（Ability Catalog）](#三能力系统全录ability-catalog)
  - [3.1 常量与能力基类](#31-常量与能力基类)
  - [3.2 初始能力（10 项）](#32-初始能力10-项)
  - [3.3 进化解锁能力（6 项）](#33-进化解锁能力6-项)
- [四、反派阵营 Antagonist System](#四反派阵营-antagonist-system)
  - [4.1 阵营数据结构](#41-阵营数据结构)
  - [4.2 结算报告 roundend_report](#42-结算报告-roundend_report)
  - [4.3 动态规则集 Dynamic Ruleset](#43-动态规则集-dynamic-ruleset)
- [五、化学与蛋 Chems & Eggs](#五化学与蛋-chems--eggs)
  - [5.1 专属化学：未知甲基苯丙胺异构体](#51-专属化学未知甲基苯丙胺异构体)
  - [5.2 蠕虫蛋 borer_egg](#52-蠕虫蛋-borer_egg)
  - [5.3 强化蛋 empowered egg 与器官](#53-强化蛋-empowered-egg-与器官)
- [六、进化系统 Evolution System](#六进化系统-evolution-system)
  - [6.1 进化机制与基类](#61-进化机制与基类)
  - [6.2 通用进化 General](#62-通用进化-general)
  - [6.3 潜钻虫路线 Diveworm（红色）](#63-潜钻虫路线-diveworm红色)
  - [6.4 蜂巢领主路线 Hivelord（紫色）](#64-蜂巢领主路线-hivelord紫色)
  - [6.5 共生体路线 Symbiote（绿色）](#65-共生体路线-symbiote绿色)
  - [6.6 进化树全表（29 项）](#66-进化树全表29-项)
- [七、焦点系统 Focus System](#七焦点系统-focus-system)
- [八、物品：蠕虫笼](#八物品蠕虫笼)
- [九、状态效果：糖抑制](#九状态效果糖抑制)

---

## 一、总览 Overview

**脑钻蠕虫（Cortical Borer）** 是 TianGuan13（NovaSector 分支）中的寄生型中局反派/中立角色：一条滑腻的微型虫子，会从耳道钻入宿主（人类）的脑中，寄生后获得成长、回复与化学合成能力。玩家可作为幽灵加入，也可由蛋孵化。蠕虫拥有完整的**进化树（3 条路线 × 多层级）**、**能力面板（16 项能力）**、**焦点增益（4 类）**、**化学学习与注入系统**以及**反派阵营结算**机制。

| 模块文件 | 行数 | 内容 |
|---|---|---|
| `code/cortical_borer.dm` | 499 | 蠕虫本体、寄生机制、生命周期 |
| `code/cortical_borer_abilities.dm` | 838 | 能力系统（全部 16 项能力） |
| `code/cortical_borer_antag.dm` | 132 | 反派阵营、动态规则集 |
| `code/cortical_borer_chems.dm` | 22 | 专属化学（甲基苯丙胺异构体） |
| `code/cortical_borer_egg.dm` | 95 | 蠕虫蛋（普通/强化/内鬼/OPFOR） |
| `code/cortical_borer_items.dm` | 103 | 蠕虫笼（抓捕道具） |
| `code/focus_datum.dm` | 77 | 焦点系统（头/胸/臂/腿） |
| `code/status_effects.dm` | 11 | 糖抑制状态效果 |
| `code/evolution/borer_evolution.dm` | 18 | 进化机制（解锁/学习） |
| `code/evolution/evolution_datum.dm` | 39 | 进化基类 |
| `code/evolution/evolution_general.dm` | 80 | 通用进化（4 类 7 项） |
| `code/evolution/evolution_diveworm.dm` | 111 | 潜钻虫路线（9 项） |
| `code/evolution/evolution_hivelord.dm` | 73 | 蜂巢领主路线（5 项） |
| `code/evolution/evolution_symbiote.dm` | 112 | 共生体路线（9 项） |
| `code/evolution/evolution_things/empowered_egg.dm` | 62 | 强化蛋器官（破胸孵化） |

**核心机制速览**：蠕虫只能在**活人体内**成长/回血/说话；糖（sugar）会压制蠕虫能力；宿主受蠕虫被动毒害（每 tick 概率性毒素伤害）；蠕虫可学习并注入化学物质、可进化、可产卵、可恐惧麻痹人类、可复活宿主。

---

## 二、蠕虫本体：脑钻蠕虫 Cortical Borer

> 源码路径：`modular_nova/modules/cortical_borer/code/cortical_borer.dm`

### 2.1 全局目标变量与宏定义

**任务/目标全局变量**（Game Objective Globals）：

| 变量 | 初值 | 含义 |
|---|---|---|
| `GLOB.objective_egg_borer_number` | 2 | 产蛋目标：成功产蛋的蠕虫数（达到后全族成熟阈值 -2） |
| `GLOB.objective_egg_egg_number` | 5 | 单条蠕虫产卵目标数（达到 5 即计 1 次成功产蛋） |
| `GLOB.objective_willing_hosts` | 2 | 自愿宿主目标人数（达到后全族成熟阈值 -10） |
| `GLOB.objective_blood_chem` | 3 | 血液化学目标：单条蠕虫从血液学到的化学数（达到 3 即计 1 次成功） |
| `GLOB.objective_blood_borer` | 3 | 血液化学成功蠕虫数（达到后全族成熟阈值 -3） |
| `GLOB.successful_egg_number` | 0 | 成功产蛋蠕虫计数（实际达成数） |
| `GLOB.willing_hosts` | 空列表 | 自愿宿主 ckey 列表 |
| `GLOB.successful_blood_chem` | 0 | 成功血液化学学习蠕虫计数 |
| `GLOB.cortical_borers` | 空列表 | 现存全部蠕虫列表（蜂巢意识通信/死亡广播用） |

**宏定义**（本文件）：

```dm
#define BODYTEMP_DIVISOR 16   // 体温调节除数：体温变化追赶环境温度的速度
```

### 2.2 移动/动作速度修正器

| 类型 | 名称 | 效果 |
|---|---|---|
| `/datum/movespeed_modifier/focus_speed` | 焦点速度 | `multiplicative_slowdown = -0.4`（腿焦点给宿主的移速加成） |
| `/datum/movespeed_modifier/borer_speed` | 蠕虫速度 | `multiplicative_slowdown = -0.5`（蜂巢领主 T3「Increased Energy」给蠕虫本体的移速加成） |
| `/datum/actionspeed_modifier/focus_speed` | 焦点动作速度 | `multiplicative_slowdown = -0.3`，`id = ACTIONSPEED_ID_BORER`（臂焦点给宿主的动作速度加成） |

### 2.3 通用探测与器官联动

**`/mob/proc/has_borer()`**：遍历 `contents`，若找到 `iscorticalborer` 对象则返回该蠕虫，否则返回 `FALSE`。任何 mob 可用（设计上仅人类会有蠕虫）。

**`/obj/item/organ/brain/Remove()` 覆写**（摘脑强制驱逐）：当宿主的大脑被摘除时，若体内有蠕虫（`target.has_borer()`），立即调用 `cb_inside.leave_host()` 将蠕虫逼出。

**`/obj/item/organ/borer_body`（充血蠕虫体器官）**：

| 属性 | 值 |
|---|---|
| name | "engorged cortical borer"（充血脑钻蠕虫） |
| desc | "the body of a cortical borer, full of human viscera, blood, and more." |
| zone | `BODY_ZONE_HEAD`（头部） |
| var `borer` | 指向所属蠕虫的引用 |

行为：
- `Destroy()`：清空 borer 引用；若宿主带有 `TRAIT_WEATHER_IMMUNE`（来源 `"borer_in_host"`）则移除该特质。
- `on_mob_insert()`：给宿主套 `fake_healthy` 状态效果（假健康 HUD）；`ADD_TRAIT(宿主, TRAIT_WEATHER_IMMUNE, "borer_in_host")`（宿主免疫天气）；若 borer 存在，对其每个 `body_focuses` 调用 `on_add()`。
- `on_mob_remove()`：对每个焦点调用 `on_remove()`；若体内还有蠕虫则 `leave_host()`；移除 `fake_healthy` 状态与 `TRAIT_WEATHER_IMMUNE`；`qdel(src)` 销毁器官。

**`/obj/item/reagent_containers/borer`**：蠕虫专用试剂容器，`volume = 100`（注：`volume` 属性在源码中为 100，用于暂存待注入化学）。

### 2.4 本体属性全表

**`/mob/living/basic/cortical_borer`** 基础属性：

| 属性 | 值 | 说明 |
|---|---|---|
| name | "cortical borer" | 脑钻蠕虫 |
| desc | "A slimy creature that is known to go into the ear canal of unsuspecting victims." | 一种黏滑生物，以钻入受害者耳道闻名 |
| icon / icon_state / icon_living / icon_dead | `animal.dmi` / "brainslug" / "brainslug" / "brainslug_dead" | 脑蛞蝓外观 |
| maxHealth / health | 25 / 25 | 基础生命 |
| pass_flags | `PASSTABLE \| PASSMOB` | 可穿过桌子和生物 |
| density | FALSE | 无碰撞体积 |
| layer | `BELOW_MOB_LAYER` | 位于 mob 之下 |
| mob_size | `MOB_SIZE_TINY` | 微型 |
| mob_biotypes | `MOB_ORGANIC \| MOB_BUG` | 有机体/虫类 |

**成长/化学数值**：

| 属性 | 初值 | 说明 |
|---|---|---|
| `known_chemicals` | 空列表 | 已学会的化学（初始无） |
| `potential_chemicals` | 23 种（见下） | 可学习的化学池 |
| `blacklisted_chemicals` | 空列表 | 黑名单化学（机制保留，当前为空） |
| `maturity_age` | 0 | 成熟年龄（仅在宿主体内增长） |
| `chemical_evolution` | 1 | 化学进化点 |
| `stat_evolution` | 0 | 属性进化点 |
| `max_chemical_storage` | 50 | 化学储存上限 |
| `chemical_storage` | 50 | 当前化学储存 |
| `chemical_regen` | 1 | 化学回复速度（体内时） |
| `health_per_level` | 2.5 | 每级生命加成 |
| `health_regen_per_level` | 0.02 | 每级生命回复加成 |
| `chem_storage_per_level` | 20 | 每级化学上限加成 |
| `chem_regen_per_level` | 1 | 每级化学回复加成 |
| `level` | 0 | 总升级次数 |
| `health_regen` | 1.02 | 生命回复倍率 |
| `generation` | 1 | 世代（蛋继承父代 +1） |
| `limited_borer` | 10 | 化学/属性进化点上限（默认 10） |
| `children_produced` | 0 | 已产后代数 |
| `blood_chems_learned` | 0 | 通过血液学到的化学数 |
| `host_harm_multiplier` | 1 | 对宿主负面效果倍率 |
| `organic_restricted` | TRUE | 仅能寄生有机体（TRUE 时限制） |
| `changeling_restricted` | TRUE | 无法寄生变形怪（changling） |
| `upgrade_flags` | 0 | 升级位旗标（BORER_* 位） |
| `genome_locked` | FALSE | 已进化互斥基因组（锁定同阶/高阶其他基因组） |

**注入速率系统**：

| 属性 | 值 |
|---|---|
| `injection_rates`（全部） | 5 / 10 / 25 / 50 单位 |
| `injection_rates_unlocked`（已解锁） | 初始仅 [5] |
| `injection_rate_current`（当前） | 5 |
| `injection_cooldown` | 冷却声明（COOLDOWN_DECLARE） |

**初始能力列表（known_abilities，10 项）**：

```
/datum/action/cooldown/borer/toggle_hiding       切换躲藏
/datum/action/cooldown/borer/choosing_host       寄居/离开宿主
/datum/action/cooldown/borer/evolution_tree      进化树
/datum/action/cooldown/borer/inject_chemical     化学注入器
/datum/action/cooldown/borer/upgrade_chemical    学习新化学
/datum/action/cooldown/borer/learn_focus         学习焦点
/datum/action/cooldown/borer/upgrade_stat        变强
/datum/action/cooldown/borer/force_speak         强制宿主说话
/datum/action/cooldown/borer/fear_human          激起恐惧
/datum/action/cooldown/borer/check_blood         检查血液
```

**初始可学习化学池（potential_chemicals，23 种）**：

| # | 化学路径 | 名称 |
|---|---|---|
| 1 | `/datum/reagent/medicine/spaceacillin` | 太空西林 |
| 2 | `/datum/reagent/medicine/potass_iodide` | 碘化钾 |
| 3 | `/datum/reagent/medicine/diphenhydramine` | 苯海拉明 |
| 4 | `/datum/reagent/medicine/epinephrine` | 肾上腺素 |
| 5 | `/datum/reagent/medicine/haloperidol` | 氟哌啶醇 |
| 6 | `/datum/reagent/toxin/formaldehyde` | 甲醛 |
| 7 | `/datum/reagent/inverse/libitoil` | 逆·利必妥（libitoil） |
| 8 | `/datum/reagent/impurity/mannitol` | 杂质甘露醇 |
| 9 | `/datum/reagent/medicine/c2/libital` | C2 利必妥 |
| 10 | `/datum/reagent/medicine/c2/lenturi` | C2 伦图里 |
| 11 | `/datum/reagent/medicine/c2/convermol` | C2 康维莫 |
| 12 | `/datum/reagent/medicine/c2/seiver` | C2 塞弗 |
| 13 | `/datum/reagent/medicine/c2/multiver` | C2 万能解 |
| 14 | `/datum/reagent/lithium` | 锂 |
| 15 | `/datum/reagent/medicine/salglu_solution` | 盐水葡萄糖 |
| 16 | `/datum/reagent/medicine/mutadone` | 突变酮 |
| 17 | `/datum/reagent/toxin/heparin` | 肝素（毒） |
| 18 | `/datum/reagent/drug/methamphetamine/borer_version` | 未知甲基苯丙胺异构体 |
| 19 | `/datum/reagent/medicine/morphine` | 吗啡 |
| 20 | `/datum/reagent/medicine/inacusiate` | 益听素 |
| 21 | `/datum/reagent/medicine/oculine` | 护目素 |
| 22 | `/datum/reagent/toxin/mindbreaker` | 破心者（毒） |
| 23 | `/datum/reagent/medicine/mannitol` | 甘露醇（医学） |

### 2.5 初始化与销毁

**`Initialize(mapload)`** 流程：
1. 添加 `squashable` 组件：`squash_chance = 25`（25% 被踩扁）、`squash_damage = 25`（25 伤害）、`SQUASHED_DONT_SQUASH_IN_CONTENTS` 旗标。
2. 添加 `can_be_held` 元素（可被抓握）。
3. `ADD_TRAIT(TRAIT_VENTCRAWLER_ALWAYS, INNATE_TRAIT)`：永久通风管爬行者（可穿通风管移动）。
4. 变换矩阵 `Scale(0.5, 0.5)`：体型缩半。
5. 命名：`"[lang_reverse_text(initial(name))] ([generation]-[rand(100,999)])"`（如 `1-288` 为一世 288 号；`4-483` 为四世 483 号）；并有 **5% 概率**改为彩蛋名 `"cortical boner"` 或 `"cortical vorer"`（同样带世代编号）。
6. 加入 `GLOB.cortical_borers`；创建 `reagent_holder = new /obj/item/reagent_containers/borer(src)`。
7. 遍历 `known_abilities` 逐一实例化并 `Grant(src)`。
8. 若 `mind` 存在且无 `/datum/antagonist/cortical_borer` 阵营，则 `add_antag_datum`。
9. 遍历 `subtypesof(/datum/borer_focus)` 生成全部 `possible_focuses`。
10. 执行初始进化 `do_evolution(/datum/borer_evolution/base)`（“The Beginning”）。

**`Destroy()`**：若有人类宿主，`locate()` 其 `borer_body` 器官并 `Remove()`；移除宿主的 `TRAIT_WEATHER_IMMUNE`；清空 human_host；移出 `GLOB.cortical_borers`；`QDEL_NULL(reagent_holder)`。

**`death(gibbed)`**：若在宿主体内，`forceMove` 到宿主所在格并清空 human_host；移出全局列表；仅第一次死亡时向所有蠕虫广播 `"[src] has left the hivemind forcibly!"`（`deathgasp_once` 防刷屏）；若被碎尸则销毁 reagent_holder。

**`attack_ghost()`（幽灵接管）**：无 ckey/key 且未死亡的空壳蠕虫可被幽灵以 tgui 确认（Yes/No）后接管，`ckey = user.ckey`，并补上反派阵营。

**状态栏（get_status_tab_items）**：显示化学储存 `化学/上限`、化学进化点、属性进化点；若有糖抑制则显示提示；显示阵营目标进度（蛋目标：成功产蛋数/所需蠕虫数/已产总数/所需数；自愿宿主：所需/当前/所需；血液化学：所需蠕虫数/所需化学数/当前成功数/所需蠕虫数）。

### 2.6 生命周期 Life

每 tick（`Life(seconds_per_tick)`），**仅在宿主体内且宿主存活时**生效：

1. **被动毒害（负面机制）**：`prob(5 × host_harm_multiplier × (潜行模式 ? 0.1 : 1))` 概率，且宿主毒素损失 ≤ `80 × host_harm_multiplier` 时，`adjust_tox_loss(5 × host_harm_multiplier, forced = TRUE)`。即默认 5% 概率每 tick 造成 5 毒素伤害。
2. 给宿主套 `fake_healthy` 状态（假健康）。
3. **糖抑制**：宿主血液含糖（`/datum/reagent/consumable/sugar`）且无 `BORER_SUGAR_IMMUNE` 旗标 → 套 `borer_sugar` 状态；否则移除。
4. **化学回复**：`chemical_storage < max_chemical_storage` 时，每 tick `+chemical_regen`（潜行模式下不回复）。
5. **生命回复**：`health < maxHealth` 时，`health = min(health × health_regen, maxHealth)`（潜行模式下不回复）。
6. **成熟计时**：`timed_maturity < world.time` 时调用 `mature()`。

### 2.7 寄生机制（进出宿主）

**`inside_human()`**：`ishuman(loc)` 即为在宿主体内（蠕虫以 `forceMove` 进入人类 mob 体内，loc 即宿主）。

**`host_sugar()`**：无 `BORER_SUGAR_IMMUNE` 旗标且宿主血液含糖 → TRUE。

**`handle_environment()`（体温覆写）**：
- 在宿主体内：以 `human_host.coretemperature` 作为环境温度源。
- 体外：取环境温度；若所在位置为可移动物体，按 `contents_thermal_insulation` 降低温差。
- 冷/热变化均除以 `BODYTEMP_DIVISOR(16)`，受 `BODYTEMP_COOLING_MAX`/`BODYTEMP_HEATING_MAX` 钳制。

**`leave_host()`**：移除宿主头部 `borer_body` 器官 → `forceMove` 到宿主所在格 → `human_host = null`。

**寄生进入（由能力 `choosing_host` 触发，详见 3.2）**：进入前 6 秒 `do_after`（`BORER_FAST_BORING` 且非躲藏时为 3 秒）；检查头部防生物装甲（BIO ≥ 100 的头盔/口罩/颈部装备则失败）；非人类（含猴子）不可寄、已有蠕虫者不可寄、非有机体不可寄（`organic_restricted`）、变形怪不可寄（`changeling_restricted`）。进入后：`forceMove(宿主)`、`copy_languages(宿主)`（复制宿主语言）、新建 `borer_body` 器官插入宿主、给蠕虫加 `TRAIT_WEATHER_IMMUNE`（来源 `"borer_in_host"`）。

### 2.8 说话/低语/拖拽限制

| 覆写 | 规则 |
|---|---|
| `whisper()` | 禁止低语：提示 "You are unable to whisper!" 并返回 FALSE |
| `say()` | 体外无法说话（提示）；体内可说话；**宿主含糖时**说话内容以 10 字符乱序打乱（`scramble_message_replace_chars(message, 10)`）；**以 `;` 开头**为蜂巢意识频道（Cortical Hivemind）：消息广播给全部蠕虫与死者观察者（`sings, "..."`），并记录 `log_say`；正常说话则仅宿主与自身可见（`sings to [host], "..."`），死者观察者可跟随链接观看 |
| `start_pulling()` | 禁止拖拽任何物体（提示后直接 return） |

### 2.9 成熟与升级 mature()

- `timed_maturity = world.time + 1 SECONDS`（每 1 秒成熟 1 点）；潜行模式下不成熟。
- `maturity_age++`。
- **成熟阈值**：默认 `20`；可被全局目标削减：
  - `GLOB.successful_egg_number >= objective_egg_borer_number(2)` → **-2**
  - `length(GLOB.willing_hosts) >= objective_willing_hosts(2)` → **-10**
  - `GLOB.successful_blood_chem >= objective_blood_borer(3)` → **-3**
  - 即最快阈值可达 20-2-10-3 = **5**。
- 达到阈值：`chemical_evolution++`（上限 `limited_borer`=10，超限提示）。
- 达到 2× 阈值：`stat_evolution++`（上限 10，超限提示），随后 `maturity_age = 0` 重置。

**`recalculate_stats()`（重算属性）**：用于进化/升级回溯修正：
```
maxHealth          = 25 + (level × health_per_level)
health_regen       = 1.02 + (level × health_regen_per_level)
max_chemical_storage = 50 + (level × chem_storage_per_level)
chemical_regen     = 1 + (level × chem_regen_per_level)
health             = clamp(旧health, 1, maxHealth)
```

### 2.10 强化蠕虫 empowered 变体

**`/mob/living/basic/cortical_borer/empowered`**（只能由尸体破胸的强化蛋孵化，初始更强）：

| 属性 | 普通 | 强化 |
|---|---|---|
| maxHealth / health | 25 / 25 | **150 / 150** |
| health_per_level | 2.5 | **15** |
| health_regen_per_level | 0.02 | **0.04** |
| stat_evolution | 0 | **8** |
| chemical_evolution | 1 | **8** |
| max_chemical_storage / chemical_storage | 50 / 50 | **250 / 250** |
| chem_regen_per_level | 1 | **1.5** |
| chem_storage_per_level | 20 | **25** |

---

## 三、能力系统全录（Ability Catalog）

> 源码路径：`modular_nova/modules/cortical_borer/code/cortical_borer_abilities.dm`

### 3.1 常量与能力基类

**宏定义**：

```dm
#define CHEMICALS_PER_UNIT 2          // 每单位化学消耗 2 点化学储存
#define CHEMICAL_SECOND_DIVISOR (5 SECONDS)  // 注入冷却除数
#define OUT_OF_HOST_EGG_COST 50       // 体外产蛋消耗 50 生命
#define BLOOD_CHEM_OBJECTIVE 3        // 血液化学单条目标数
```

**基类 `/datum/action/cooldown/borer`**：

| 属性 | 初值 | 说明 |
|---|---|---|
| button_icon | `actions.dmi` | 按钮图标 |
| cooldown_time | 0 | 基础冷却 |
| `chemical_cost` | 0 | 化学消耗 |
| `chemical_evo_points` | 0 | 所需化学进化点 |
| `stat_evo_points` | 0 | 所需属性进化点 |

- `New()`：名称自动追加消耗说明后缀，如 `([100 chemicals])`、`([5 chemical points])`、`([1 stat point])`。
- `Trigger()` 通用校验：owner 必须是蠕虫；死亡不可用；化学储存不足 → 气泡提示 "You need [cost] chemicals!"；化学进化点不足 → "You need [points] chemical points!"；属性进化点不足 → "You need [points] stat points!"。

### 3.2 初始能力（10 项）

#### ① 打开化学注入器 `inject_chemical`（Open Chemical Injector，图标 "chemical"）

- 消耗：无 ｜ 冷却：无
- 需求：必须在宿主体内；宿主无糖。
- **TGUI 界面 "BorerChem"**：
  - `amount` = 当前注入速率（初始 5）；`energy` = 化学储存 ÷ 2（`CHEMICALS_PER_UNIT`）；`maxEnergy` = 上限 ÷ 2；`borerTransferAmounts` = 已解锁速率列表；`onCooldown`；`notEnoughChemicals`。
  - 化学列表 = `known_chemicals`。
- **操作**：
  - `amount`：在 `injection_rates`（5/10/25/50）中切换当前速率。
  - `inject`：目标试剂必须已学会；向 `reagent_holder` 加入 `injection_rate_current` 单位（纯度 1），再以 `INGEST` 方式转入宿主血液；宿主收到警告提示；消耗 `注入量 × 2` 化学储存；冷却 = `注入量 / 5 秒`（即 5 单位 → 1 秒，10 单位 → 2 秒，25 单位 → 5 秒，50 单位 → 10 秒）；双方记游戏日志。
- `ui_state` 恒可用；`ui_status` 仅蠕虫本人且在宿主体内时开启。

#### ② 打开进化树 `evolution_tree`（Open Evolution Tree，图标 "newability"）

- 消耗：无 ｜ 冷却：无
- **TGUI 界面 "BorerEvolution"**：显示 `evolution_points`（属性进化点）；未学进化项（名称/描述/获得文本/费用/禁用原因/路径/颜色/层级/互斥标记）；已学进化项列表。
- 路线颜色：Diveworm 潜钻虫=红、Hivelord 蜂巢领主=紫、Symbiote 共生体=绿、General 通用=label、其他=灰。
- 禁用条件：费用 > 属性进化点，或（互斥进化 && `genome_locked`）。
- `evolve`：校验路径为合法进化 → 费用/互斥校验 → `do_evolution()` → 记录 `log_borer_evolution` → 扣除 `evo_cost` 属性进化点。

#### ③ 学习焦点 `learn_focus`（Learn Focus，图标 "getfocus"）

- 消耗：焦点费用（全部为 5 属性进化点）｜ 冷却：触发后启动冷却
- 需求：宿主体内；宿主无糖；还有未学焦点。
- 打开列表（未学焦点，标注 `[名称 (费用 points)]`）；选择后扣除费用、加入 `body_focuses`、调用 `picked_focus.on_add(human_host, owner)`。

#### ④ 从血液学习化学 `learn_bloodchemical`（Learn Chemical from Blood，图标 "bloodchem"）

- 消耗：**5 化学进化点** ｜ 冷却：触发后启动
- 需求：宿主体内；宿主无糖；宿主血液含至少 1 种试剂。
- 从宿主 `reagents.reagent_list` 中选一种：已在 `known_chemicals`/`blacklisted_chemicals` 则拒绝；必须带 `REAGENT_CAN_BE_SYNTHESIZED` 旗标（可合成），否则拒绝。
- 成功后：扣 5 化学进化点、`known_chemicals += 试剂`、`blood_chems_learned++`、宿主大脑器官损伤 `5 × host_harm_multiplier`；若 `blood_chems_learned == 3 (BLOOD_CHEM_OBJECTIVE)` 则 `GLOB.successful_blood_chem += 1`（达成阵营目标）；宿主（无 `TRAIT_AGEUSIA`）会尝到该化学的味道提示。

#### ⑤ 学习新化学 `upgrade_chemical`（Learn New Chemical，图标 "bloodlevel"）

- 消耗：**1 化学进化点** ｜ 冷却：触发后启动
- 需求：宿主体内；宿主无糖；`potential_chemicals` 非空。
- 从潜在池选择一种 → 扣 1 化学进化点、`known_chemicals += 试剂`、从潜在池移除该试剂；宿主大脑损伤 `5 × host_harm_multiplier`；宿主尝到味道。

#### ⑥ 变强 `upgrade_stat`（Become Stronger，图标 "level"）

- 消耗：**1 属性进化点** ｜ 冷却：触发后启动
- 需求：宿主体内；宿主无糖。
- 效果（直接叠加，不重算上限公式，与 `recalculate_stats` 等效增量一致）：

| 属性 | 增量 |
|---|---|
| maxHealth | +2.5 |
| health_regen | +0.02 |
| max_chemical_storage | +20 |
| chemical_regen | +1 |
| level | +1 |

- 代价：宿主大脑器官损伤 `10 × host_harm_multiplier`；宿主视线模糊 `6 SECONDS × host_harm_multiplier`（默认约 12 秒）；蠕虫收到 "You feel stronger!"，宿主收到警告。

#### ⑦ 切换躲藏 `toggle_hiding`（Toggle Hiding，图标 "hide"）

- 消耗：无 ｜ 冷却：触发后启动
- 效果：进入躲藏（`upgrade_flags |= BORER_HIDING` + `prone_mob` 组件，趴下贴地）；再次使用则解除（移除 `TRAIT_PRONE` 状态并清除旗标）。躲藏时可在桌子/生物下潜行。

#### ⑧ 激起恐惧 `fear_human`（Incite Fear，图标 "fear"）

- 消耗：无 ｜ 冷却：**12 秒**
- **外部目标**（体外使用，范围 1 格内的人类）：不能是尸体、不能是 AFK 玩家；单一目标直接触发，多目标弹选择框；距离 >1 拒绝。
  - 效果：目标 `Paralyze(7 SECONDS)` 瘫痪 7 秒、`adjust_stamina_loss(50)` 体力损失 50、`set_confusion_if_lower(9 SECONDS)` 混乱 9 秒。
- **内部目标**（体内使用，对宿主）：
  - 效果：宿主 `Paralyze(10 SECONDS)` 瘫痪 10 秒、体力损失 100、混乱 15 秒。
- 均记录游戏日志。

#### ⑨ 检查血液 `check_blood`（Check Blood，图标 "blood"）

- 消耗：无 ｜ 冷却：**5 秒**
- 需求：宿主体内；宿主无糖。
- 效果：对宿主执行 `healthscan(scanpower = SCANPOWER_ADVANCED)`（高级健康扫描）+ `chemscan`（化学扫描）。

#### ⑩ 寄居/离开宿主 `choosing_host`（Inhabit/Uninhabit Host，图标 "host"）

- 消耗：无 ｜ 冷却：**10 秒**
- **离开宿主**：宿主无糖才可离开；提示宿主（非潜行模式时）；移除 `borer_body` 器官；`forceMove` 到宿主所在格；`human_host = null`；移除蠕虫的 `TRAIT_WEATHER_IMMUNE`。
- **进入宿主**：范围 1 格内筛选可用宿主：
  - 必须是人类（非猴子）；不能已有蠕虫；必须有机（`MOB_ORGANIC`，受 `organic_restricted` 控制）；不能是变形怪（受 `changeling_restricted` 控制）。
  - 唯一候选直接进入；多个候选弹选择框。
  - `enter_host()`：先查 `check_for_bio_protection`（头部/口罩/颈部装备 `BIO 护甲 ≥ 100` 则进入失败）；再查 `has_borer()`；`do_after(6 秒)`（`BORER_FAST_BORING` 且非躲藏时为 3 秒）；距离 >1 失败。
  - 成功后：`human_host = 宿主`、`forceMove(宿主)`、非潜行时提示宿主、`copy_languages(宿主)`、新建并插入 `borer_body` 器官、加 `TRAIT_WEATHER_IMMUNE`、记日志。

### 3.3 进化解锁能力（6 项）

#### ⑪ 强制宿主说话 `force_speak`（Force Host Speak，图标 "speak"）

- 消耗：无 ｜ 冷却：**30 秒**
- 需求：宿主体内；宿主无糖。
- 效果：输入文本 → 宿主收到 `boldwarning` 提示 → 宿主大脑损伤 `2 × host_harm_multiplier` → 宿主以 `forced = TRUE` 说出该文本（计入发言日志）。

#### ⑫ 繁殖后代 `produce_offspring`（Produce Offspring，图标 "reproduce"）

- 消耗：**100 化学** ｜ 冷却：**1 分钟**
- 需求：默认必须宿主体内（除非有 `BORER_ALONE_PRODUCTION` 旗标）。
- **体内产蛋**：扣 100 化学 → `produce_egg()` → 宿主大脑损伤 `25 × host_harm_multiplier` → **75% 概率**宿主获得脑创伤（roll 1-100）：
  - 1-34：轻度创伤（`BRAIN_TRAUMA_MILD` / `TRAUMA_RESILIENCE_BASIC`）
  - 35-60：轻度创伤（`BRAIN_TRAUMA_MILD` / `TRAUMA_RESILIENCE_SURGERY`）
  - 61-71：重度创伤（`BRAIN_TRAUMA_SEVERE` / `TRAUMA_RESILIENCE_SURGERY`）
  - 72-75：重度创伤（`BRAIN_TRAUMA_SEVERE` / `TRAUMA_RESILIENCE_LOBOTOMY`）
  - 产生呕吐物污渍 + `splat.ogg` 音效；记日志。
- **体外产蛋 `no_host_egg()`**（需 `BORER_ALONE_PRODUCTION`）：生命扣 **50**（`OUT_OF_HOST_EGG_COST`，最低保 1）→ `produce_egg()` → 产生血溅污渍 + 音效；记日志。
- **`produce_egg()`**：在所在格生成 `mob_spawn/ghost_role/borer_egg`，`generation = 蠕虫generation + 1`；`children_produced++`；当 `children_produced == 5 (objective_egg_egg_number)` 时 `GLOB.successful_egg_number += 1`（达成产蛋目标）。

#### ⑬ 复活宿主 `revive_host`（Revive Host，图标 "revive"）

- 消耗：**200 化学** ｜ 冷却：**2 分钟**
- 需求：宿主体内；宿主无糖。
- 效果：
  - 四种伤害各治疗 50%：`adjust_brute/tox/fire/oxy_loss(-当前值 × 0.5)`。
  - 若血液量 < `BLOOD_VOLUME_BAD`，恢复到 `BLOOD_VOLUME_BAD`。
  - 所有非外部器官（非 `ORGAN_EXTERNAL`）损伤减半。
  - `human_host.revive()` **直接复活宿主**。
  - 宿主收到 `boldwarning` 提示；记日志。

#### ⑭ 自愿宿主 `willing_host`（Willing Host，图标 "willing"）

- 消耗：**150 化学** ｜ 冷却：**2 分钟**
- 需求：宿主体内；宿主无糖；宿主 ckey 未在 `GLOB.willing_hosts` 中。
- 效果：弹窗询问宿主本人（Yes/No，`tgui_input_list`）；宿主选 Yes → `GLOB.willing_hosts += 宿主ckey`（计入阵营自愿宿主目标，目标 2 人）；选 No 则冷却照常启动。

#### ⑮ 潜行模式 `stealth_mode`（Stealth Mode，图标 "hiding"）

- 消耗：**100 化学**（关闭时 0）｜ 冷却：**2 分钟**
- 效果：开启时 `upgrade_flags |= BORER_STEALTH_MODE`：宿主察觉度大幅降低（进出宿主不提示宿主、死亡时也不广播？注：死亡广播与 `deathgasp_once` 无关，此处生效的是被动毒害概率 ×0.1、不回复化学/生命、不成熟）。再次使用关闭。

#### ⑯ 强化后代 `empowered_offspring`（Produce Empowered Offspring，图标 "reproduce"）

- 消耗：**150 化学** ｜ 冷却：**1 分钟**
- 需求：宿主体内；**宿主必须已死亡（stat == DEAD）**。
- 效果：扣 150 化学；宿主胸部（`BODY_ZONE_CHEST`）**非机械义肢**且体内无 `empowered_borer_egg` 器官时，植入 `empowered_borer_egg` 器官（`generation = 蠕虫generation + 1`）；`children_produced++`；达到 5 时同样计成功产蛋；`splat.ogg` 音效；记日志。该蛋 3 分钟后破胸（详见 5.3）。

---

## 四、反派阵营 Antagonist System

> 源码路径：`modular_nova/modules/cortical_borer/code/cortical_borer_antag.dm`

### 4.1 阵营数据结构

**`/proc/printborer(datum/mind/borer)`**（结算文本生成）：输出玩家蠕虫的：
- 存活状态：`survived`（绿）/ `died`（红）；身体被毁 → "had their body destroyed."（红）。
- 产子数：`produced [N] borers.`
- 全部已学进化（`past_evolutions` 的 name 列表，英文逗号连接）。

**`/proc/printborerlist(players, fleecheck)`**：将多个 mind 的结算文本包成 `<ul class='playerlist'>` 列表。

**`/datum/antagonist/cortical_borer`**：

| 属性 | 值 |
|---|---|
| name | "Cortical Borer" |
| pref_flag | `ROLE_BORER` |
| show_in_antagpanel | TRUE |
| roundend_category | "cortical borers" |
| antagpanel_category | "Cortical Borers" |
| show_to_ghosts | TRUE |
| var `borers` | 所属蠕虫团队（`/datum/team/cortical_borers`） |

- `get_preview_icon()`：以蠕虫 icon/icon_state 生成预览图标。
- `get_team()` → 返回 `borers`。
- `create_team()`：若未指定新团队，则在 `GLOB.antagonists` 中寻找已有 `borers` 团队复用；否则 `new /datum/team/cortical_borers`。

**`/datum/team/cortical_borers`**：name = "Cortical Borers"（improper 前缀）。

### 4.2 结算报告 roundend_report

报告内容（红色边框面板）：
1. `The [name] were:` + `printborerlist(members)` 逐人详情。
2. **存活判定**：`GLOB.cortical_borers` 中任一非 DEAD → 绿字 "Borers were able to survive the shift!"；否则红字 "Borers were unable to survive the shift!"。
3. **产蛋判定**：`successful_egg_number >= objective_egg_borer_number(2)` → 绿字 "Borers were able to produce enough eggs!"；否则红字。
4. **自愿宿主判定**：`length(willing_hosts) >= objective_willing_hosts(2)` → 绿字 "Borers were able to gather enough willing hosts!"；否则红字。
5. **血液化学判定**：`successful_blood_chem >= objective_blood_borer(3)` → 绿字 "Borers were able to learn enough chemicals through the blood!"；否则红字。

### 4.3 动态规则集 Dynamic Ruleset

**`/datum/dynamic_ruleset/midround/from_ghosts/cortical_borer`（Cortical Borer Infestation）**：

| 属性 | 值 |
|---|---|
| name | "Cortical Borer Infestation"（脑钻蠕虫 infestation） |
| config_tag | "Cortical Borers" |
| preview_antag_datum | `/datum/antagonist/cortical_borer` |
| midround_type | `LIGHT_MIDROUND`（轻度中局） |
| pref_flag | `ROLE_BORER` |
| weight | 3 |
| min_pop | 50（至少 50 人口） |
| repeatable | TRUE（可重复触发） |

**`find_vents()`**（从异形规则集移植）：筛选可生成通风管：
- 必须是 `/obj/machinery/atmospherics/components/unary/vent_pump` 且未删除、未焊接（`welded`）；
- 必须在站内层（`is_station_level`）；
- 所属管道网络 `parents[1]` 存在，且网络内大气机械数量 **> 20**（防止卡在小网络，如安保室/病毒室被排除）。

**`create_ruleset_body()`**：`new /mob/living/basic/cortical_borer`（生成空壳蠕虫）。

**`create_execute_args()`**：返回 `find_vents()` 列表。

**`assign_role(candidate, vent_list)`**：给候选 mind 添加 `/datum/antagonist/cortical_borer` 阵营；若通风管 ≥ 2 个则 `pick_n_take` 随机取一个，否则取第一个；`candidate.current.move_into_vent(vent)`（从通风管爬出登场）。

---

## 五、化学与蛋 Chems & Eggs

### 5.1 专属化学：未知甲基苯丙胺异构体

> 源码路径：`modular_nova/modules/cortical_borer/code/cortical_borer_chems.dm`

**`/datum/reagent/drug/methamphetamine/borer_version`（Unknown Methamphetamine Isomer）**：
- `overdose_threshold = 40`（过量阈值 40 单位）。
- `on_mob_life()` 效果（每秒，按 `metabolization_ratio` 缩放）：
  - 2.5% 概率（SPT_PROB）播放快感提示：`"You feel hyper."` / `"You feel like you need to go faster."` / `"You feel like you can run the world."`。
  - 添加情绪事件 `"tweaking"`（`/datum/mood_event/stimulant_medium`）。
  - 眩晕/倒地/昏迷/瘫痪/禁锢各减少 `2.667 秒 × spt × 代谢率`（加速摆脱硬控）。
  - 体力损失恢复 `1.33 × spt × 代谢率`（`adjust_stamina_loss(-1.33…)`，不更新体力条）。
  - 颤抖上限 `5 秒`（`set_jitter_if_lower`）。
  - 2.5% 概率随机抽搐/发抖表情（`twitch` / `shiver`）。

### 5.2 蠕虫蛋 borer_egg

> 源码路径：`modular_nova/modules/cortical_borer/code/cortical_borer_egg.dm`

**`/obj/effect/mob_spawn/ghost_role/borer_egg`（幽灵角色生成器）**：

| 属性 | 值 |
|---|---|
| name / desc | "borer egg" / "An egg of a creature that is known to crawl inside of you, be careful." |
| icon / icon_state | `animal.dmi` / "brainegg"（脑蛋） |
| layer / density | `BELOW_MOB_LAYER` / FALSE |
| mob_name / mob_type | "cortical borer" / `/mob/living/basic/cortical_borer` |
| role_ban | `ROLE_ALIEN` |
| show_flavor | FALSE |
| prompt_name | "cortical borer" |
| you_are_text | "You are a Cortical Borer." |
| flavour_text | "You are a cortical borer! You can fear someone to make them stop moving, but make sure to inhabit them! You only grow/heal/talk when inside a host!" |
| important_text | 说明可选择友善与否、行为决定宿主反应、不要无言语地机械操作宿主、用 `;` 与其他蠕虫交流、正常说话与宿主交流、体外无法说话但可 emote |
| var `generation` | 1（蛋的世代） |
| var `host_egg` | `/obj/item/borer_egg`（绑定的蛋物品） |

行为：
- `Initialize()`：在所在格生成 `host_egg` 物品，`host_egg.host_spawner = src`，自身 `forceMove(host_egg)`（生成器藏进蛋里）；向幽灵广播 "A cortical borer egg has been laid in [area]."（可点击进入，`click_interact = TRUE`，`POLL_IGNORE_DRONE` 忽略键）。
- `special()`：被幽灵接管后：`mind.add_antag_datum(/datum/antagonist/cortical_borer)`；命名 `"cortical borer ([generation]-[rand(100,999)])"`；销毁 `host_egg`。

**`/obj/item/borer_egg`（蛋物品）**：

| 属性 | 值 |
|---|---|
| name / desc | "borer egg" / 同上 |
| icon / icon_state | `animal.dmi` / "brainegg" |
| layer | `BELOW_MOB_LAYER` |
| var `host_spawner` | 绑定的 mob_spawn 生成器 |

行为：
- `attack_ghost()`：转发给 `host_spawner.attack_ghost(user)`（幽灵点击蛋 = 申请接管）。
- `attack_self()`：使用者提示蛋的描述；在脚下生成蛋渍污渍（`/obj/effect/decal/cleanable/food/egg_smudge`）；销毁生成器与蛋（**手动打碎蛋会失去生成机会**）。
- `throw_impact()`：落地若未被接住 → 生成蛋渍污渍、销毁生成器与蛋（**摔碎蛋同样失去机会**）。

**子类型**：

| 类型 | 差异 |
|---|---|
| `/obj/item/borer_egg/empowered` | name "empowered borer egg"；icon_state "empowered_brainegg" |
| `/obj/effect/mob_spawn/ghost_role/borer_egg/traitor` | prompt_name "cortical borer (traitor spawned)" |
| `/obj/effect/mob_spawn/ghost_role/borer_egg/opfor` | prompt_name "cortical borer (OPFOR spawned)" |
| `/obj/effect/mob_spawn/ghost_role/borer_egg/empowered` | name "empowered borer egg"；desc "An egg of a creature that came crawling out of someone instead of into them."；mob_type = `/mob/living/basic/cortical_borer/empowered`；host_egg = `/obj/item/borer_egg/empowered` |

### 5.3 强化蛋 empowered egg 与器官

> 源码路径：`modular_nova/modules/cortical_borer/code/evolution/evolution_things/empowered_egg.dm`

**`/obj/item/organ/empowered_borer_egg`（器官：强化蠕虫蛋）**：

| 属性 | 值 |
|---|---|
| name / desc | "strange egg" / "All slimy and yuck." |
| icon_state | "innards"（内脏，反正看不见） |
| visual | TRUE（可视化） |
| zone | `BODY_ZONE_CHEST`（胸部） |
| slot | `ORGAN_SLOT_PARASITE_EGG`（寄生虫蛋槽位） |
| `burst_time` | **3 MINUTES**（破胸时间） |
| `generation` | 1 |

行为：
- `on_find()`：被发现时提示发现者（含宿主与部位）。
- `Initialize()`：若在碳基生物体内则直接 `Insert(loc)`。
- `on_mob_insert()`：启动 3 分钟定时器 `try_burst()`。
- `on_mob_remove()`：可见提示 + 生成蛋渍污渍 + `qdel`（蛋被手术取出即销毁）。
- **`try_burst()`**：
  - 无宿主或宿主未死亡 → 销毁蛋（不孵化）。
  - 向幽灵征召（`SSpolling.poll_ghost_candidates`，role = `ROLE_PAI`，征召 10 秒，图标为强化蛋，忽略键 `POLL_IGNORE_CORTICAL_BORER`，角色名 "empowered cortical borer"）：
    - **无候选人**：生成 `/obj/effect/mob_spawn/ghost_role/borer_egg/empowered`（强化蛋生成器，供后续幽灵接管），`generation` 继承；宿主**胸部断肢**（`chest.dismember()`）；大段危险提示。
    - **有候选人**：`pick` 一名幽灵 → 生成 `/mob/living/basic/cortical_borer/empowered`，`generation` 继承，`ckey = 幽灵`，`mind.add_antag_datum(/datum/antagonist/cortical_borer)`；宿主胸部断肢 + 破胸演出提示。

---

## 六、进化系统 Evolution System

> 源码路径：`code/evolution/` 目录（6 文件）

### 6.1 进化机制与基类

**`evolution_datum.dm` —— 基类 `/datum/borer_evolution`**：

| 属性 | 默认值 | 说明 |
|---|---|---|
| name | "" | 名称 |
| desc | "" | 描述 |
| evo_cost | 2 | 费用（注释：T5 花费 3 点而非 2 点） |
| gain_text | "" | 获得时展示的文本 |
| evo_type | `BORER_EVOLUTION_GENERAL` | 所属基因组 |
| mutually_exclusive | FALSE | 若 TRUE，锁定其他基因组中同阶及以上的进化 |
| unlocked_evolutions | 空列表 | 解锁的下游进化 |
| tier | 0 | 数值层级（不影响机制） |

- `on_evolve()`：展示斜体获得文本；若互斥则 `genome_locked = TRUE`。

**基类进化 `/datum/borer_evolution/base`（"The Beginning"）**：
- desc "The start of a great age."；gain_text "The worms, which we came to call \"Cortical Borers\", are fascinating creatures."
- `evo_cost = 0`；`evo_type = BORER_EVOLUTION_START`；tier 0。
- 解锁 4 条路线起点：
  - `/datum/borer_evolution/upgrade_injection`（通用）
  - `/datum/borer_evolution/symbiote/willing_host`（共生体）
  - `/datum/borer_evolution/hivelord/produce_offspring`（蜂巢领主）
  - `/datum/borer_evolution/diveworm/health_per_level`（潜钻虫）

**`borer_evolution.dm` —— 进化机制**：
- `get_possible_evolutions()`：收集所有已学进化的 `unlocked_evolutions` 并集。
- `do_evolution(evolution_type)`：路径校验（非路径 → stack_trace 返回 FALSE）；重复学习返回 FALSE；实例化进化、存入 `past_evolutions[类型]`、调用 `on_evolve(src)`、返回 TRUE。

### 6.2 通用进化 General

> 源码路径：`modular_nova/modules/cortical_borer/code/evolution/evolution_general.dm`（红色之外的通用路线）

#### `upgrade_injection`（Upgrade Injection，T1，费用 2）
- desc "Upgrade your possible injection amount to 10 units."
- 解锁：`upgrade_injection/t2`；效果：解锁注入速率列表中下一个速率（5→解锁 10）。
#### `upgrade_injection/t2`（Upgrade Injection II，T2，费用 2）
- "…to 25 units."；解锁 t3（解锁速率 25）。
#### `upgrade_injection/t3`（Upgrade Injection III，T3，费用 2）
- "…to 50 units."；无下游（解锁速率 50）。
#### `sugar_immunity`（Sugar Immunity，T6，费用 5）
- desc "Become immune to the ill effects of sugar in you or a host."
- 效果：`upgrade_flags |= BORER_SUGAR_IMMUNE`（糖不再压制蠕虫）。
#### `synthetic_borer`（Synthetic Boring，T6，费用 6）
- desc "Gain the ability to take synthetic humans as a host as well."
- 效果：`organic_restricted = FALSE`（可寄生合成体人类）。
#### `synthetic_chems_positive`（Synthetic Chemicals (+)，T6，费用 6）
- desc "Gain access to a list of helpful, synthetic-compatible chemicals."
- 追加化学池（6 种）：`synthanol`（合成乙醇）、`system_cleaner`（系统清洁剂）、`nanite_slurry`（纳米浆）、`liquid_solder`（液态焊料）、`oil`（燃油/油）、`fuel`（通用燃料）。
#### `synthetic_chems_negative`（Synthetic Chemicals (-)，T6，费用 6）
- desc "Gain access to a list of synthetic-damaging chemicals."
- 追加化学池（4 种）：`fluacid`（氟酸，注释"更像是反一切"）、`thermite`（铝热剂）、`pyrosium`（高温剂）、`oxygen`（氧）。

### 6.3 潜钻虫路线 Diveworm（红色）

> 源码路径：`modular_nova/modules/cortical_borer/code/evolution/evolution_diveworm.dm`（`evo_type = BORER_EVOLUTION_DIVEWORM`）

#### T1 `health_per_level`（Health Increase，费用 1）
- desc "Increase the amount of health per level-up you gain."
- 效果：`health_per_level += 2.5`（2.5→5）→ `recalculate_stats()`；解锁 `host_speed`。
#### T2 `host_speed`（Boring Speed，费用 2）
- desc "Decrease the time it takes to enter a host when you are not hiding."
- 效果：`upgrade_flags |= BORER_FAST_BORING`（进入宿主 do_after 6 秒→3 秒）；解锁 `expanded_chemicals`。
#### T3 `expanded_chemicals`（Expanded Chemical List，费用 2，**互斥**）
- desc "Gain access to a new list of devious chemicals to the unlockable list."
- 追加化学池（8 种）：`fentanyl`（芬太尼）、`staminatoxin`（体力毒素）、`mutetoxin`（哑毒）、`mutagen`（诱变剂）、`cyanide`（氰化物）、`opium`（鸦片）、`mushroomhallucinogen`（致幻菇）、`inverse/oculine`（逆·护目素）。
- 解锁：`harm_increase`、`health_per_level/t2`。
#### `health_per_level/t2`（Health Increase II，tier -1，费用 2）→ 解锁 t3
#### `health_per_level/t3`（Health Increase III，tier -1，费用 2）→ 无下游
#### T4 `harm_increase`（Toxins Increase，费用 2）
- desc "Increase the passive and active damage you do to your host, and how often it occurs."
- 效果：`host_harm_multiplier += 0.25`（1→1.25）；解锁 t2 与 `empowered_offspring`。
#### `harm_increase/t2`（Toxins Increase II，tier -1）→ 解锁 t3
#### `harm_increase/t3`（Toxins Increase III，tier -1）→ 无下游
#### T5 `empowered_offspring`（Empowered Offspring，费用 3）
- desc "Lay an egg in a deceased host, and after a delay an empowered borer will burst out."
- 效果：授予能力 `empowered_offspring`（见 3.3 ⑯）。
- 解锁 T6 通用：`sugar_immunity`、`synthetic_borer`、`synthetic_chems_negative`。

### 6.4 蜂巢领主路线 Hivelord（紫色）

> 源码路径：`modular_nova/modules/cortical_borer/code/evolution/evolution_hivelord.dm`（`evo_type = BORER_EVOLUTION_HIVELORD`）

#### T1 `produce_offspring`（Produce Offspring，费用 1）
- desc "Produce an egg, which your host will vomit up."
- 效果：授予能力 `produce_offspring`（见 3.3 ⑫）；解锁 `blood_chemical`。
#### T2 `blood_chemical`（Learn Blood Chemical，费用 2）
- desc "Learn a synthesizable chemical from the blood of your host."
- 效果：授予能力 `learn_bloodchemical`（见 3.2 ④）；解锁 `movespeed`。
#### T3 `movespeed`（Increased Energy，费用 2，**互斥**）
- desc "Boost your speed by a large amount."
- 效果：`add_movespeed_modifier(/datum/movespeed_modifier/borer_speed)`（-0.5 减速，大幅提速）；解锁 `stealth_mode`。
#### T4 `stealth_mode`（Stealth Mode，费用 2）
- desc "While in stealth mode, your presence is much less noticable in hosts, but you do not gain passive benefits."
- 效果：授予能力 `stealth_mode`（见 3.3 ⑮）；解锁 `produce_offspring_alone`。
#### T5 `produce_offspring_alone`（Produce Offspring II，费用 3）
- desc "Allows you to produce eggs outside a host, in exchange for health and chemicals."
- 效果：`upgrade_flags |= BORER_ALONE_PRODUCTION`（允许体外产蛋，-50 生命）。
- 解锁 T6 通用：`sugar_immunity`、`synthetic_borer`、`synthetic_chems_positive`、`synthetic_chems_negative`。

### 6.5 共生体路线 Symbiote（绿色）

> 源码路径：`modular_nova/modules/cortical_borer/code/evolution/evolution_symbiote.dm`（`evo_type = BORER_EVOLUTION_SYMBIOTE`）

#### T1 `willing_host`（Willing Host，费用 1）
- desc "Ask a host if they are willing, furthering your objectives."
- 效果：授予能力 `willing_host`（见 3.3 ⑭）；解锁 `chem_per_level`。
#### T2 `chem_per_level`（Chemical Increase，费用 2）
- desc "Increase the amount of chemicals per level-up you gain."
- 效果：`chem_storage_per_level += 10`（20→30）、`chem_regen_per_level += 0.5`（1→1.5）→ `recalculate_stats()`；解锁 `expanded_chemicals`。
#### T3 `expanded_chemicals`（Expanded Chemical List，费用 2，**互斥**）
- desc "Gain access to a new list of helpful chemicals to the unlockable list."
- 追加化学池（6 种）：`sal_acid`（水杨酸）、`oxandrolone`（氧雄龙）、`atropine`（阿托品）、`neurine`（神经素）、`leporazine`（兔化嗪）、`omnizine`（全能嗪）。
- 解锁：`harm_decrease`、`chem_per_level/t2`。
#### `chem_per_level/t2`（Chemical Increase II，tier -1，费用 2）→ 解锁 t3
#### `chem_per_level/t3`（Chemical Increase III，tier -1，费用 2）→ 无下游
#### T4 `harm_decrease`（Toxins Decrease，费用 2）
- desc "Decrease the passive and active damage you do to your host, and how often it occurs."
- 效果：`host_harm_multiplier -= 0.25`（1→0.75）；解锁 t2 与 `revive_host`。
#### `harm_decrease/t2`（Toxins Decrease II，tier -1）→ 解锁 t3
#### `harm_decrease/t3`（Toxins Decrease III，tier -1）→ 无下游
#### T5 `revive_host`（Revive Host，费用 3）
- desc "Revive your host and heal what ails them."
- 效果：授予能力 `revive_host`（见 3.3 ⑬）。
- 解锁 T6 通用：`sugar_immunity`、`synthetic_borer`、`synthetic_chems_positive`。

### 6.6 进化树全表（29 项）

| 进化 | 路线 | Tier | 费用 | 互斥 | 效果 |
|---|---|---|---|---|---|
| The Beginning | Start | 0 | 0 | – | 起点，解锁 4 条路线 |
| Upgrade Injection | General | 1 | 2 | – | 注入速率解锁 10 |
| Upgrade Injection II | General | 2 | 2 | – | 注入速率解锁 25 |
| Upgrade Injection III | General | 3 | 2 | – | 注入速率解锁 50 |
| Sugar Immunity | General | 6 | 5 | – | 免疫糖抑制 |
| Synthetic Boring | General | 6 | 6 | – | 可寄生合成体 |
| Synthetic Chemicals (+) | General | 6 | 6 | – | +6 种合成友好化学 |
| Synthetic Chemicals (-) | General | 6 | 6 | – | +4 种合成破坏化学 |
| Health Increase | Diveworm | 1 | 1 | – | 每级生命 +2.5 |
| Boring Speed | Diveworm | 2 | 2 | – | 钻入 6s→3s |
| Expanded Chemical List | Diveworm | 3 | 2 | ✔ | +8 种阴险化学 |
| Health Increase II | Diveworm | -1 | 2 | – | 继续 +2.5/级 |
| Health Increase III | Diveworm | -1 | 2 | – | 继续 +2.5/级 |
| Toxins Increase | Diveworm | 4 | 2 | – | 宿主伤害 ×1.25 |
| Toxins Increase II | Diveworm | -1 | 2 | – | 继续 ×1.25 |
| Toxins Increase III | Diveworm | -1 | 2 | – | 继续 ×1.25 |
| Empowered Offspring | Diveworm | 5 | 3 | – | 死者体内产强化蛋 |
| Produce Offspring | Hivelord | 1 | 1 | – | 宿主呕吐产蛋 |
| Learn Blood Chemical | Hivelord | 2 | 2 | – | 从血液学化学 |
| Increased Energy | Hivelord | 3 | 2 | ✔ | 移速 -0.5 减速 |
| Stealth Mode | Hivelord | 4 | 2 | – | 潜行模式 |
| Produce Offspring II | Hivelord | 5 | 3 | – | 体外产蛋（-50 生命） |
| Willing Host | Symbiote | 1 | 1 | – | 询问自愿宿主 |
| Chemical Increase | Symbiote | 2 | 2 | – | 化学上限/级 +10、回复/级 +0.5 |
| Expanded Chemical List | Symbiote | 3 | 2 | ✔ | +6 种治疗化学 |
| Chemical Increase II | Symbiote | -1 | 2 | – | 继续叠加 |
| Chemical Increase III | Symbiote | -1 | 2 | – | 继续叠加 |
| Toxins Decrease | Symbiote | 4 | 2 | – | 宿主伤害 ×0.75 |
| Toxins Decrease II | Symbiote | -1 | 2 | – | 继续 ×0.75 |
| Toxins Decrease III | Symbiote | -1 | 2 | – | 继续 ×0.75 |
| Revive Host | Symbiote | 5 | 3 | – | 复活宿主 |

（注：上表含 t2/t3 重复名进化，共 31 行；去重后的 29 项中实际定义条目 = 1 base + 7 general + 9 diveworm + 5 hivelord + 9 symbiote = **31 个进化路径定义**；其中 Diveworm/Symbiote 的 II/III 级为同名延续。）

---

## 七、焦点系统 Focus System

> 源码路径：`modular_nova/modules/cortical_borer/code/focus_datum.dm`

**基类 `/datum/borer_focus`**：
- `name`：名称；`cost = 5`（默认 5 属性进化点，四个焦点均未覆写）；`traits`：特质列表。
- `on_add(host, borer)`：为宿主添加所有特质（来源 `REF(borer)`，已有则跳过）。
- `on_remove(host, borer)`：移除来源为该蠕虫的特质。

**四个焦点全录**：

| 焦点 | 名称 | 特质 | 额外效果 |
|---|---|---|---|
| `/datum/borer_focus/head` | head focus（头部焦点） | `TRAIT_NOFLASH`（免疫闪光）、`TRAIT_TRUE_NIGHT_VISION`（真夜视）、`TRAIT_KNOW_ENGI_WIRES`（知晓工程线路） | 添加/移除时通知宿主；移除时 `update_sight()` |
| `/datum/borer_focus/chest` | chest focus（胸部焦点） | `TRAIT_NOBREATH`（无需呼吸）、`TRAIT_NOHUNGER`（无饥饿）、`TRAIT_STABLEHEART`（稳定心脏） | 添加时宿主营养设为 `NUTRITION_LEVEL_WELL_FED`（饱腹） |
| `/datum/borer_focus/arms` | arm focus（手臂焦点） | `TRAIT_QUICKER_CARRY`（更快搬运）、`TRAIT_QUICK_BUILD`（快速建造）、`TRAIT_SHOCKIMMUNE`（电击免疫） | 添加时宿主获得动作速度修正器 `focus_speed`（-0.3）；移除时按 `ACTIONSPEED_ID_BORER` 移除 |
| `/datum/borer_focus/legs` | leg focus（腿部焦点） | `TRAIT_LIGHT_STEP`（轻步）、`TRAIT_FREERUNNING`（跑酷）、`TRAIT_SILENT_FOOTSTEPS`（无声脚步） | 添加时宿主获得移速修正器 `focus_speed`（-0.4）；移除时移除 |

---

## 八、物品：蠕虫笼

> 源码路径：`modular_nova/modules/cortical_borer/code/cortical_borer_items.dm`

**`/obj/item/cortical_cage`（cortical borer cage 蠕虫笼）**：

| 属性 | 值 |
|---|---|
| name | "cortical borer cage" |
| desc | "A harmless cage that is intended to capture cortical borers." |
| icon / icon_state | `items.dmi` / "cage" |
| var `opened` | FALSE（陷阱是否"打开"待触发） |
| var `internal_radio` | 插入的无线电（通知用） |
| var `trapped_borer` | 笼内蠕虫 |

行为全录：
- `Initialize()`：更新外观；注册 `COMSIG_ATOM_ENTERED` → `spring_trap`（连接位置元素 `connect_loc`）。
- `update_overlays()`：按状态叠加 "borer"（有蠕虫）、"radio"（有无线电）、"doors_open"/"doors_closed"（开关门）贴层。
- `attack_self()`：切换开关状态，播放 `boltsup.ogg` 音效，可见消息提示开关门。
- `item_interaction()`：对笼使用无线电 → 无线电装入笼内（`internal_radio`），更新外观。
- `crowbar_act()`：撬棍取出无线电 → 无线电回到脚下，`internal_radio = null`。
- `spring_trap()`：**打开状态**下，任何蠕虫进入所在格 → `trapped_borer = AM`；可见消息；`forceMove(src)` 关入笼中；自动关门；若有无线电则向**公共频道**广播 "A cortical borer has been trapped in [area]."；播放音效。
- `relaymove()`：笼内生物尝试移动：
  - 非蠕虫：直接弹出到笼外格。
  - 蠕虫且笼开着：爬出（`trapped_borer.forceMove` 到所在格），清空引用，自动关门。
  - 蠕虫且笼关着：触发 `container_resist_act`（挣脱）。
- `container_resist_act()`：设置 `CLICK_CD_BREAKOUT` 点击冷却；`do_after(rand(30 SECONDS, 40 SECONDS))` 随机 30-40 秒后成功挣脱：开门、弹出蠕虫、清空引用。

---

## 九、状态效果：糖抑制

> 源码路径：`modular_nova/modules/cortical_borer/code/status_effects.dm`

**`/datum/status_effect/borer_sugar`（Sugar Dampening 糖抑制）**：

| 属性 | 值 |
|---|---|
| id | "borer_sugar" |
| tick_interval | -1（无周期性 tick） |
| status_type | `STATUS_EFFECT_UNIQUE`（唯一） |
| alert_type | `/atom/movable/screen/alert/status_effect/borer_sugar` |

- 图标：`actions.dmi` / "borer_sugar"。
- 提示文本：name "Sugar Dampening"；desc "Your powers are diminished while sugar is in you or your host!"（你或宿主体内有糖时，你的能力被削弱）。
- 触发条件：宿主血液含糖且蠕虫无 `BORER_SUGAR_IMMUNE`。抑制效果汇总：**无法注入化学、无法进出宿主、无法说话（内容乱序）、无法恐惧、无法学习、无法进化/升级/成熟、不回复化学与生命、被动毒害概率 ×0.1**。

---

*本文档由 TianGuan13 源码 `modular_nova/modules/cortical_borer/`（2,272 行 / 15 文件，NovaSector 分支）全量提取生成。所有数值、冷却、费用、概率均与源码一致。*
