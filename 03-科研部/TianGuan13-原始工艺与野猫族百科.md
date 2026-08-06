# TianGuan13 原始工艺与野猫族百科

> **项目**: TianGuan13 (Nova Sector → /tg/station) · **分支**: NovaSector
> **代码**: `modular_nova/modules/primitive_production/`（966 行，4 个 .dm）+ `modular_nova/modules/primitive_catgirls/`（916 行，9 个 .dm）+ `modular_nova/modules/primitive_structures/`（595 行，7 个 .dm），合计 **2,477 行 / 20 个 .dm**
> **范围**: **原始工艺**（陶瓷 / 玻璃吹制 / 生产技能）+ **野猫族 Hearthkin**（种族 / 器官 / 语言 / 服装 / 重生点 / 阵营）+ **原始建筑**（栅栏 / 储物 / 茅草 / 火把 / 门窗）
>
> **定位**: 本卷为《TianGuan13 百科》系列的**原始生活（Primitive Lifestyle）专卷**，服务对象为冰月（Icemoon）野猫族、灰烬行者（Ashwalker）及一切原始生存玩法。三模块均由 Jake Park 创作（primitive_production / primitive_catgirls 与 primitive 系列同源），无原版 TG Proc/File 改动，全部为模块内新定义。
>
> **关联文档**: 锻炉（reagent forge）烧制陶瓷/熔融玻璃的机制见《TianGuan13-锻造系统百科》（`modular_nova/modules/reagent_forging/`）；野猫族可用的 13 个阵营配方（符文武器/骨制武器等）中，`handcrafted_hearthkin_armor`、`hearthkin_ship_fragment_inactive`、`skeleton_key`、`anointing_oil` 等定义于其他模块；冰月温泉钓鱼表为独立数据（见第二卷 2.5）。

---

## 目录

- [第一卷 · 原始工艺（Primitive Production）](#第一卷--原始工艺primitive-production)
  - [1.1 模块总览与生产技能（Production Skill）](#11-模块总览与生产技能production-skill)
  - [1.2 粘土与制砖（Clay & Bricks）](#12-粘土与制砖clay--bricks)
  - [1.3 陶瓷全录（Ceramics）](#13-陶瓷全录ceramics)
  - [1.4 玻璃吹制全录（Glassblowing）](#14-玻璃吹制全录glassblowing)
  - [1.5 碎玻璃回收与出口（Shard Salvage & Exports）](#15-碎玻璃回收与出口shard-salvage--exports)
- [第二卷 · 野猫族（Primitive Catgirls / Hearthkin）](#第二卷--野猫族primitive-catgirls--hearthkin)
  - [2.1 种族总览：原始类人（Primitive Demihuman）](#21-种族总览原始类人primitive-demihuman)
  - [2.2 器官（Organs）](#22-器官organs)
  - [2.3 语言：Ættmál 与 Siik'Tajr](#23-语言ættmál-与-siiktajr)
  - [2.4 服装全录（Clothing）](#24-服装全录clothing)
  - [2.5 衣柜售货机（Wardrobe Vendor）](#25-衣柜售货机wardrobe-vendor)
  - [2.6 地洞重生点、职业与阵营（Spawner / Job / Team / Antag）](#26-地洞重生点职业与阵营spawner--job--team--antag)
  - [2.7 杂项物品（嗅盐 / 翻译项链）](#27-杂项物品嗅盐--翻译项链)
  - [2.8 地图物件（Map Items）](#28-地图物件map-items)
- [第三卷 · 原始建筑（Primitive Structures）](#第三卷--原始建筑primitive-structures)
  - [3.1 栅栏与门（Fencing & Gates）](#31-栅栏与门fencing--gates)
  - [3.2 储物结构（Storage Structures）](#32-储物结构storage-structures)
  - [3.3 茅草屋顶与茅草块（Thatch Roofing & Flooring）](#33-茅草屋顶与茅草块thatch-roofing--flooring)
  - [3.4 壁挂火把（Wall Torch）](#34-壁挂火把wall-torch)
  - [3.5 窗户 / 木梯 / 家具](#35-窗户--木梯--家具)
- [附录 · 代码路径索引](#附录--代码路径索引)

---

# 第一卷 · 原始工艺（Primitive Production）

**代码**: `modular_nova/modules/primitive_production/code/`（966 行，4 个 .dm）——ceramics.dm（368 行）/ glassblowing.dm（545 行）/ misc.dm（35 行）/ production_skill.dm（18 行）
**模块简介**: 添加各种"原始"方式生产物品（readme: *"Adds a variety of 'primitive' ways to produce items"*），作者 Jake Park。核心玩法闭环：**玻璃矿石 + 水 → 粘土 → 拉坯机成型 → 锻炉烧制 → 陶瓷**；**玻璃碎片锤碎回收 → 锻炉熔融 → 吹杆塑形 → 玻璃器皿**。全流程受**生产技能（Production Skill）**加成。

## 1.1 模块总览与生产技能（Production Skill）

**源码**: `production_skill.dm`（18 行）

### /datum/skill/production —— 生产技能

| 属性 | 值 | 说明 |
|---|---|---|
| 技能名 | **Production / 生产** | 称号（title）"Producer / 生产者" |
| 描述 | "The artist who finds themselves using multiple mediums in which to express their creativity." | 用多种媒介表达创造力的艺术家 |
| SKILL_SPEED_MODIFIER | `1 / 0.95 / 0.9 / 0.85 / 0.75 / 0.6 / 0.5` | 等级 1-7 的速度倍率（越低越快） |
| SKILL_PROBS_MODIFIER | `10 / 15 / 20 / 25 / 30 / 35 / 40` | 概率类加成（%） |
| 奖励物品 | `/obj/item/clothing/neck/cloak/skill_reward/production` | 传奇生产者斗篷 |

**经验来源**（本模块内）：水坑/水域/水槽搓粘土 +1；拉坯机成型 +50；玻璃吹制每步 +10、成品 +30。

### /obj/item/clothing/neck/cloak/skill_reward/production —— 传奇生产者斗篷（Legendary Producer's Cloak）

- 描述: *"Worn by the most skilled producers, this legendary cloak is only attainable by knowing how to create the best products. This status symbol represents a being who has crafted some of the finest glass and ceramic works."*（只有最熟练的生产者才能获得，象征曾打造出最精美玻璃与陶瓷作品的造物者）
- 图标: `icons/cloaks.dmi` + `icons/neck.dmi`，icon_state = "productioncloak"
- 关联技能: `/datum/skill/production`（达到宗师级解锁的声望披风）

## 1.2 粘土与制砖（Clay & Bricks）

**源码**: `ceramics.dm`（368 行）第 1-89 行

### /datum/material/clay —— 粘土材料

| 属性 | 值 | 说明 |
|---|---|---|
| 名称/描述 | "clay" / "It's clay."（就是粘土） | 颜色 `#757575`（灰） |
| mat_flags | `MATERIAL_CLASS_RIGID \| MATERIAL_BASIC_RECIPES` | 刚性材料 + 基础配方 |
| DENSITY / HARDNESS / FLEXIBILITY | 4 / 1 / 9 | 密度 4、硬度 1（极软）、柔韧 9 |
| REFLECTIVITY / ELECTRICAL / THERMAL / CHEMICAL | 2 / 1 / 8 / 3 | 反射 2、导电 1、导热 8、化学 3 |
| 板材 | `/obj/item/stack/sheet/mineral/clay` | 粘土砖 |
| 价值 | `5 / SHEET_MATERIAL_AMOUNT`（每单位） | 极廉价 |
| 踏地音效 | `FOOTSTEP_PLATING` | 纹理层 "brick"（砖纹） |

### /obj/item/stack/sheet/mineral/clay —— 粘土砖（Clay Brick）

- 描述: "A heavy clay brick."（一块沉重的粘土砖）；单数名 "clay brick"
- 图标: `prim_fun.dmi`，icon_state = "sheet-clay"（无手持图标）
- 投掷速度 3 / 投掷距离 5；掉落音 `SFX_BRICK_DROP`、拾取音 `SFX_BRICK_PICKUP`
- 材料: `/datum/material/clay` × SHEET_MATERIAL_AMOUNT；可与同型堆叠（merge_type 自身）

### GLOB.clay_recipes —— 粘土配方（10 块砖起建）

| 配方 | 产物 | 消耗 | 耗时 | 制作旗标 | 分类 |
|---|---|---|---|---|---|
| clay range（粘土炉灶） | `/obj/machinery/primitive_stove/clay` | 10 砖 | 5 秒 | 密度检查 / 每格一个 / 实心地面 | CAT_MISC |
| clay oven（粘土烤炉） | `/obj/machinery/oven/primitive/clay` | 10 砖 | 5 秒 | 同上 | CAT_MISC |

> 注: `get_main_recipes()` 将 clay_recipes 合并进砖堆的主配方列表。

### 取水制粘土（Glass Ore + Water = Clay）

手持 **玻璃矿石**（`/obj/item/stack/ore/glass`）对以下三种水源交互，消耗 1 块矿石，原地生成 **1 个粘土堆**（`/obj/item/stack/clay`），并给玩家 **+1 生产经验**：

| 水源 | 路径 | 额外条件 |
|---|---|---|
| 水坑（Puddle） | `/obj/structure/water_source/puddle` | 无 |
| 开放水域（Open Water） | `/turf/open/water` | 无 |
| 水槽（Sink） | `/obj/structure/sink` | 槽内试剂必须是水（dispensedreagent == water）且剩余体积 > 0 |

### /obj/item/stack/clay —— 粘土堆（Clay）

- 描述: "A pile of clay that can be used to create ceramic artwork."（一堆可用于制作陶瓷艺术的粘土）
- 单数名: "glob of clay"（一团粘土）；图标 "clay"
- 用途: ① 投入拉坯机（见 1.3）；② 作为未烧制陶瓷的原料（经拉坯机成型）

## 1.3 陶瓷全录（Ceramics）

**源码**: `ceramics.dm`（368 行）第 93-368 行

### 1.3.1 拉坯机（Throwing Wheel）—— /obj/structure/throwing_wheel

- 名称 "throwing wheel"（拉坯机/陶轮）；描述: "A machine that allows you to throw clay."（一台让你"甩"粘土的机器）
- 属性: density = TRUE（实体）、anchored = TRUE（默认锚定，可用扳手切换）、材料 10 块铁板
- 状态: `has_clay`（是否有粘土）、`in_use`（是否使用中）

**可生产列表（production_list，6 种）**:

| 选项 | 产物（未烧制） | 最终成品（锻炉烧制后） |
|---|---|---|
| Cup（杯） | `/obj/item/ceramic/cup` | `/obj/item/reagent_containers/cup/beaker/large/ceramic`（120 单位容量） |
| Plate（盘） | `/obj/item/ceramic/plate` | `/obj/item/plate/ceramic` |
| Bowl（碗） | `/obj/item/ceramic/bowl` | `/obj/item/reagent_containers/cup/bowl/ceramic` |
| Pot（罐） | `/obj/item/ceramic/pot` | `/obj/item/clay_pot` |
| Tray（托盘） | `/obj/item/ceramic/tray` | `/obj/item/plate/oven_tray/material/ceramic` |
| Brick（砖） | `/obj/item/ceramic/brick` | `/obj/item/stack/sheet/mineral/clay` |

**操作流程**:
1. **放粘土**: 手持粘土堆点击 → 消耗 1 个粘土，`has_clay = TRUE`，图标变 "throw_wheel_full"
2. **徒手使用**: tgui 弹窗选择 **Create（制作）** 或 **Remove（取出）**
   - Create → 列表选择制品 → **4 轮 do_after**（每轮 = 生产技能速度倍率 × 4 秒），依次显示 4 条进度消息（"你慢慢开始转动拉坯机……" → "你把手放在粘土上，慢慢塑形……" → "你开始对自己做出的东西感到满意……" → "你停下拉坯机，欣赏你的新作品……"）；完成后生成所选未烧制陶瓷，**+50 生产经验**
   - Remove → 单轮 do_after 后把粘土取回手中
3. **拆解/锚定**: 撬棍（crowbar_act）→ 返还 10 块铁板 +（若有粘土）1 个粘土堆；扳手（wrench_act）→ 切换锚定

### 1.3.2 未烧制陶瓷基类（/obj/item/ceramic）

- 所有未烧制陶瓷共有 `forge_item` 变量（指向烧制成品类型）
- **蜡笔上色**: 手持蜡笔（`/obj/item/toy/crayon`）点击未烧制陶瓷 → 若蜡笔有颜色，则陶瓷整体染上蜡笔颜色（color = paint_color），烧制后成品保留该颜色
- 所有未烧制陶瓷的描述均注明"Requires heat treatment in a forge"（需在锻炉中热处理）

### 1.3.3 陶瓷制品逐项全录

| 未烧制（Path） | 名称 | 描述要点 | 烧制成品（Path） | 成品名 | 成品特性 |
|---|---|---|---|---|---|
| `/obj/item/ceramic/plate` | ceramic plate 陶盘 | 扁平盘状粘土 | `/obj/item/plate/ceramic` | ceramic plate 陶瓷盘 | — |
| `/obj/item/ceramic/pot` | ceramic pot 陶罐 | 向上弯曲成罐状 | `/obj/item/clay_pot` | clay pot 陶罐 | 可种盆栽（见下） |
| `/obj/item/ceramic/tray` | ceramic tray 陶托盘 | 扁平托盘状 | `/obj/item/plate/oven_tray/material/ceramic` | ceramic oven tray 陶瓷烤盘 | — |
| `/obj/item/ceramic/bowl` | ceramic bowl 陶碗 | 带凸缘的碗状 | `/obj/item/reagent_containers/cup/bowl/ceramic` | ceramic bowl 陶瓷碗 | custom_materials = null（无材料） |
| `/obj/item/ceramic/cup` | ceramic cup 陶杯 | 高壁杯状，**可容纳 120 单位** | `/obj/item/reagent_containers/cup/beaker/large/ceramic` | ceramic cup 陶瓷杯 | 描述 "A cup that is made from ceramic."；custom_materials = null |
| `/obj/item/ceramic/brick` | ceramic brick 陶砖 | 致密粘土块，"ready to be fired into a brick!" | `/obj/item/stack/sheet/mineral/clay` | clay brick 粘土砖 | 见 1.2 |

### 1.3.4 陶罐种盆栽（Clay Pot + Sand + Seed = Kirby Plant）

`/obj/item/clay_pot`（"A very empty clay pot. Put some sand and a seed in and call it a day."——一个空空的陶罐，放点沙子和种子就能收工）：

1. **加沙**: 手持玻璃矿石点击 → 若已有沙则拒绝；3 秒 do_after → 消耗 1 玻璃矿石，`has_sand = TRUE`
2. **播种**: 手持种子（`/obj/item/seeds`）点击 → 若没沙则警告；3 秒 do_after → **消耗种子，生成一盆盆栽**（`/obj/item/kirbyplants`），陶罐本身消失

### 1.3.5 锻炉烧制（Forge Firing）

> 烧制逻辑位于 `modular_nova/modules/reagent_forging/code/forge.dm`（`handle_ceramics` proc，锻炉温度需 ≥ MIN_FORGE_TEMP = 50）：
> 把未烧制陶瓷放入锻炉 → do_after（生产技能速度倍率 × 4 秒基准）→ 生成 `forge_item` 成品，**保留蜡笔染色**，未烧制体消失。粘土炉灶（clay range）与粘土烤炉（clay oven）同为原始烧制设施。

## 1.4 玻璃吹制全录（Glassblowing）

**源码**: `glassblowing.dm`（545 行）
**宏**: DEFAULT_TIMED = 4 秒；步骤宏 STEP_BLOW（吹）/ STEP_SPIN（转）/ STEP_PADDLE（拍）/ STEP_SHEAR（剪）/ STEP_JACKS（夹）

### 1.4.1 玻璃吹制工具（每件 5 块铁板；统一配方基类 5 铁板 / CAT_MISC）

| 工具 | 路径 | 名称 | 描述 | 用途 |
|---|---|---|---|---|
| 吹杆 | `/obj/item/glassblowing/blowing_rod` | blowing rod 吹杆 | "A tool that is used to hold the molten glass as well as help shape it."（用于托持并塑形熔融玻璃） | tool_behaviour = TOOL_BLOWROD；装载熔融玻璃（图标变 "blow_pipe_full"）；吹/转步骤徒手即可，拍/剪/夹需副手工具 |
| 玻璃夹钳 | `/obj/item/glassblowing/jacks` | jacks 玻璃夹钳 | "A tool that helps shape glass during the art process."（玻璃艺术塑形工具） | STEP_JACKS 步骤（配方 "Glass-blowing Jacks"） |
| 玻璃刮板 | `/obj/item/glassblowing/paddle` | paddle 玻璃刮板 | 同上 | STEP_PADDLE 步骤（配方 "Glass-blowing Paddle"） |
| 玻璃剪 | `/obj/item/glassblowing/shears` | shears 玻璃剪 | 同上 | STEP_SHEAR 步骤（配方 "Glass-blowing Shears"） |
| 金属杯 | `/obj/item/glassblowing/metal_cup` | metal cup 金属杯 | 同上（工具） | 装沙后入锻炉熔出熔融玻璃（见下） |

- **金属杯装沙**: 手持玻璃矿石点击金属杯 → 消耗 1 矿石，`has_sand = TRUE`，图标变 "metal_cup_full"（满杯）
- **锻炉熔炼**（forge.dm）: ① 玻璃板直接熔炼（`handle_glass_sheet_melting`，消耗 1 玻璃板）或 ② 满沙金属杯熔炼（`handle_metal_cup_melting`，金属杯恢复空）→ 生成 **熔融玻璃**；耗时 = 技能速度倍率 × 4 秒；受热时长 = 25 秒 ÷ 技能速度倍率；+10 生产经验

### 1.4.2 熔融玻璃（Molten Glass）—— /obj/item/glassblowing/molten_glass

- 描述: "A glob of molten glass, ready to be shaped into art."（一团熔融玻璃，准备塑成艺术）
- 状态: `remaining_heat`（余热冷却计时）、`chosen_item`（选定制品）、`steps_remaining`（剩余步骤表）、`total_time`（总受热时长）、`is_finished`（是否完成）
- **灼伤机制（try_burn_user）**: 未冷却（冷却计时未结束）时拾起 → 警告"它还是滚烫的！"、玩家自动 **scream 尖叫表情**、玻璃掉回地面、持握肢体受到 **15 点灼伤**（wound_bonus = CANT_WOUND，不会造成伤口），并记入 investigate log（INVESTIGATE_CRAFTING）
- **冷却**: 受热时长（total_time）耗尽后玻璃变冷——变冷后**无法进行任何塑形步骤**（需放回锻炉重新加热？实际为步骤被拒：COOLDOWN_FINISHED 时 balloon_alert 并拒绝执行），检查时提示"已经凉透了"
- 查看（examine）: 显示剩余步骤数量与冷却状态

### 1.4.3 吹杆 TGUI 界面（GlassBlowing）

吹杆提供 tgui 界面，数据: inUse、glass.timeLeft / totalTime / chosenItem / stepsRemaining / isFinished。

**选型（6 种制品）与步骤需求**:

| 制品 | 产物路径 | 吹 Blow | 转 Spin | 拍 Paddle | 剪 Shear | 夹 Jacks |
|---|---|---|---|---|---|---|
| Plate 玻璃盘 | `/obj/item/plate/blowing_glass` | 3 | 3 | 3 | 0 | 0 |
| Bowl 玻璃碗 | `/obj/item/reagent_containers/cup/bowl/blowing_glass` | 2 | 2 | 2 | 0 | 3 |
| Globe 玻璃球 | `/obj/item/glassblowing/glass_globe` | **6** | 3 | 0 | 0 | 0 |
| Cup 玻璃杯 | `/obj/item/reagent_containers/cup/beaker/large/blowing_glass` | 3 | 3 | 3 | 0 | 0 |
| Lens 玻璃透镜 | `/obj/item/glassblowing/glass_lens` | 0 | 0 | 3 | 3 | 3 |
| Bottle 玻璃瓶 | `/obj/item/reagent_containers/cup/glass/bottle/small` | 3 | 2 | 3 | 0 | 0 |

**界面动作**: 未选型时可选择上述 6 种；已选型后可执行 Blow / Spin / Paddle / Shear / Jacks / Cancel（取消清空选型与步骤）；Remove（取下）——未完成则取下熔融玻璃，已完成则生成成品。

### 1.4.4 塑形步骤机制（do_glass_step）

- **前置检查**: 玻璃必须仍有余热（冷却则拒绝）；非使用中
- **桌子要求（check_valid_table）**: 1 格范围内必须有**不可燃桌子**（无 FLAMMABLE 抗性）；**生产技能达到 MASTER（大师）级可无视桌子直接塑形**
- **工具检查（check_valid_tool）**:
  - Blow / Spin: 徒手即可，但玩家会获得 `TRAIT_CURRENTLY_GLASSBLOWING`（防止重复动作），用完移除
  - Paddle / Shear / Jacks: 必须手持对应工具（副手），工具同样加 `TRAIT_CURRENTLY_GLASSBLOWING` 防连点
- **执行**: do_after（技能速度倍率 × 4 秒）→ 对应步骤计数 -1（不低于 0）→ 全部归零则 `is_finished = TRUE`；每步 **+10 生产经验**
- 中断: "You interrupt an action!"（你打断了一个动作！）并移除 trait

### 1.4.5 成品玻璃器皿（含玻璃球/透镜）

| 制品 | 路径 | 描述 | 材料/特性 |
|---|---|---|---|
| 玻璃球 | `/obj/item/glassblowing/glass_globe` | "A glass bowl that is capable of carrying things."（可盛物的玻璃球） | 玻璃 × 半张（HALF_SHEET_MATERIAL_AMOUNT）、MATERIAL_COLOR |
| 玻璃碗 | `/obj/item/reagent_containers/cup/bowl/blowing_glass` | 同上（可盛物） | 玻璃 × 1 张、MATERIAL_EFFECTS \| MATERIAL_COLOR |
| 玻璃杯 | `/obj/item/reagent_containers/cup/beaker/large/blowing_glass` | "A glass cup that is capable of carrying liquids."（可盛液体的玻璃杯） | MATERIAL_EFFECTS \| MATERIAL_COLOR |
| 玻璃盘 | `/obj/item/plate/blowing_glass` | "A glass plate that is capable of carrying things."（可盛物的玻璃盘） | 玻璃 × 1 张、MATERIAL_EFFECTS \| MATERIAL_COLOR |
| 玻璃透镜 | `/obj/item/glassblowing/glass_lens` | "A convex glass lens that would make an excellent magnifying glass if it were attached to a handle."（凸透镜，装上把手就是完美的放大镜） | — |

## 1.5 碎玻璃回收与出口（Shard Salvage & Exports）

**源码**: `misc.dm`（35 行）+ 各文件中的 export datum

### 1.5.1 碎玻璃锤碎回收（Shard → Colored Glass Ore）

`/obj/item/shard` 用**锤类工具**（tool_behaviour == TOOL_HAMMER，如异星考古锤、锻造锤）敲击:

| 碎片类型 | 染色（FIXED_COLOUR_PRIORITY） |
|---|---|
| `/obj/item/shard`（普通玻璃） | `#88cdf1`（淡蓝） |
| `/obj/item/shard/plasma`（等离子玻璃） | `#ff80f4`（粉紫） |
| `/obj/item/shard/plastitanium`（塑钛） | `#5d3369`（深紫） |
| `/obj/item/shard/titanium`（钛） | `#cfbee0`（淡紫） |

结果: 生成 1 个**零价值彩色玻璃矿石**（`/obj/item/stack/ore/glass/zero_cost`）+ 1 处玻璃碎屑污渍（`/obj/effect/decal/cleanable/glass`）+ 玻璃碎裂音效（SFX_SHATTER，音量 30）。零价值矿石 `points = 0`，单独堆叠类型；查看时提示"这矿石一文不值"（因 points == 0）。彩色玻璃矿石正是**玻璃吹制的原料来源**（1.4.1 锻炉熔炼）。

### 1.5.2 出口单据（Export Datums）

| 出口 | 单价 | 单位名 | 收货物种类 |
|---|---|---|---|
| `/datum/export/ceramics` | CARGO_CRATE_VALUE × 2 | ceramic product（陶瓷制品） | 陶瓷盘、陶瓷烤盘、陶瓷碗、陶罐、陶瓷杯（大烧杯） |
| `/datum/export/ceramics_unfinished` | CARGO_CRATE_VALUE × 0.5 | unfinished ceramic product（未烧制陶瓷） | 未烧制 plate / bowl / pot / tray / cup |
| `/datum/export/glassblowing` | CARGO_CRATE_VALUE × 5 | glassblowing product（玻璃吹制制品） | 玻璃透镜、玻璃球、玻璃碗、玻璃杯（大烧杯）、玻璃盘 |

> 作者注释: "I really dont want them to feel gimped"（不想让玩家觉得被削）——陶瓷与玻璃制品出口**不套用弹性价格惩罚**（apply_elastic = FALSE / 直接返回父结果）。

---

# 第二卷 · 野猫族（Primitive Catgirls / Hearthkin）

**代码**: `modular_nova/modules/primitive_catgirls/code/`（916 行，9 个 .dm）——clothing.dm（174）/ clothing_vendor.dm（47）/ language.dm（28）/ map_items.dm（63）/ organs.dm（71）/ smelling_salts.dm（73）/ spawner.dm（319）/ species.dm（120）/ translator.dm（21）
**模块简介**: 冰猫（icecats）与灰烬行者（ashwalker）风格的原始内容集（readme: *"Contains various items of primitive style for icecats and sometimes even ashwalkers"*）。核心种族为 **Hearthkin（炉族）**，也称 **Primitive Demihuman（原始类人）**、**Icewalker（冰行者）**。

## 2.1 种族总览：原始类人（Primitive Demihuman）

**源码**: `species.dm`（120 行）

### /datum/species/human/felinid/primitive

| 属性 | 值 | 说明 |
|---|---|---|
| 种族名 | **Primitive Demihuman 原始类人** | id = SPECIES_FELINE_PRIMITIVE |
| 默认肺 | `/obj/item/organ/lungs/icebox_adapted` | 耐寒肺（见 2.2） |
| 默认眼 | `/obj/item/organ/eyes/low_light_adapted` | 低光适应眼（见 2.2） |
| 默认舌 | `/obj/item/organ/tongue/cat/primitive` | 原始猫舌（见 2.2） |
| 语言 | `/datum/language_holder/primitive_felinit` | 理解/会说 Ættmál + Siik'Tajr，默认选中 Ættmál；语言白名单含 primitive_catgirl |
| 常温（bodytemp_normal） | **270 K（约 -3.15°C）** | 普通人抱一下会觉得冷 |
| 高温伤害阈值 | **283 K（约 9.85°C）** | 空间站常温对他们来说闷热难耐 |
| 低温伤害阈值 | **213 K（约 -60.15°C）** | 冰月环境毫无压力 |
| 固有特质 | TRAIT_CATLIKE_GRACE（猫之优雅）、TRAIT_VIRUSIMMUNE（病毒免疫）、TRAIT_RESISTCOLD（抗寒）、TRAIT_USES_SKINTONES（肤色）、TRAIT_NO_SLIP_ICE（冰上不滑） | 共 5 项 |
| always_customizable | TRUE | 始终可自定义外观 |
| 获得种族时 | 获得**嗅觉突变**（/datum/mutation/olfaction，突变来源 SPECIES_INNATE，instability = 0 无副作用） | 失去种族时移除 |
| 预览外观 | 发型 "Blunt Bangs Alt"、发色 `#323442`、肤色 mediterranean、猫尾 + 猫耳 | — |

### 2.1.1 物种描述（get_species_description）

> "Genetically modified humanoids believed to be descendants of a now centuries old colony ship from the pre-bluespace travel era. Still having at least some human traits, they are most comparable to today's felinids with most sporting features likely spliced from the icemoon's many fauna."
>
> 基因改造人形生物，据信是**前蓝移（bluespace）航行时代**一艘数世纪前殖民船的直系后裔。仍保留部分人类特征，最接近今天的 felinid 猫人族，多数动物特征疑似取自冰月众多本土 fauna（动物群）。

### 2.1.2 种族传说（get_species_lore）——Hearthkin 炉族五段史

1. **星龙起源**: Hearthkin 是一群斯堪的纳维亚后裔文化的集合体，共同祖先为登船于 **Stjarndrakkr（Star Dragon / 星龙）** 号上的类人基因改造者——一艘**近 40 公里高**的巨型殖民船。该船于 **300 年前**（蓝移技术问世前）抵达其最后安息之地的轨道，来自 Hearthkin 口中的 **'Asgard'（阿斯加德）**。进入冰月（他们称为 **'Niflheim' / 尼福尔海姆**）大气层后，飞船因未知原因在低轨道爆炸，星龙号的大段船体碎裂并自行密封，散落月球各处。
2. **艰难求生**: 殖民者最初生活极其艰难，世代极短，能修理飞船的人员大多在撞击或之后死去。基因改造与对冰封气候的先天适应帮了大忙；据说祖先们最后孤注一掷，调动全部资源将自身**永久改造并完全适应尼福尔海姆的气候**。
3. **今日炉族**: 如今的 Hearthkin 已脱离祖先的原始文化，建立起自己的文化——星龙号最大的一些残骸段埋在冰雪之下，他们发展出**分居独栋住宅以保守秘密**的文化。居住长屋（longhouse）、睡在尼福尔海姆温暖的地下；狩猎本土生物与来自通往该月所在行星——**Muspelheim（穆斯贝尔海姆）**——传送门的生物。异教信仰数百年来不断强化：从偶尔祈求暴风雪早日结束，到如今对诸神的完整崇拜与献祭。Hearthkin 对祖先仍怀有极大敬畏，但对祖先究竟是何模样、为何来到尼福尔海姆，各部落看法不一、猜测纷纭。
4. **命名习俗**: 名字分两部分——**出生名 + 称号**。出生名仍保留'阿斯加德'风格，典型北欧名如 'Solveig Helgasdottir'（索尔薇格·赫尔加斯多蒂尔）、'Bjorn Lukasson'（比约恩·卢卡森）。当 Hearthkin 不再"未经证明"（Unproven）时，姓氏被替换为**称号（Title）**——通常两段式，基于伟大功绩、尴尬时刻或性格特征。例如某位 Hearthkin 女孩喝得半死的那一晚后被称为 **'Soul-Drowner'（溺魂者）**；也有人因体型异常庞大而被称为 **'Glacier-Shaped'（冰川塑形）**。称号**永远由族人授予**。
5. **炉堂与外族观**: 炉堂（The Hearth）是族人心目中的神圣之地；他们主要出于**务实理由**仇视外来者——认为自己先来此地，许多人相信祖先曾将尼福尔海姆"许诺"给他们。与穆斯贝尔海姆的灰烬行者（Ashwalker）不同，Hearthkin 是**科技更先进的社会**：不仅使用金属，还用金银制作饰品。他们使用被认为是其卫星所绕行星的造物或祖先遗留的**神器**；但因种种原因（因人而异），他们倾向于回避现代人类科技。
6. **生理与观念**: 身体上 Hearthkin 永远是类人形态——与普通地球人相似，但带有各种**北极动物的尾巴、耳朵甚至肢体**（狼、熊、猫等）。他们完全适应冰雪迷雾之地，却觉得纳努崔森空间站温和的恒温环境**闷热难耐**。他们对"空间站"基因改造者的看法是"**半身人（halflings）**"：拥有祖先的躯体，却流着 Midgard 人类之血与精神——比起其他外星人，他们更看不起这些"半身人"。

## 2.2 器官（Organs）

**源码**: `organs.dm`（71 行）；宏 GAS_TOLERANCE = 5（气体分压容差）

### /obj/item/organ/lungs/icebox_adapted —— 耐寒肺（Hardy Lungs）

- 描述: "Lungs adapted to frozen environments that would be otherwise inhospitable to most races. Feels cold."（适应冰封环境的肺，对多数种族而言那里不宜居。摸起来冰凉）
- 图标: `organs.dmi`，icon_state "hardylungs"
- **初始化时按冰月标准大气（ICEMOON_DEFAULT_ATMOS）校准耐受**:
  - `safe_oxygen_min / safe_nitro_min / safe_plasma_min` = 大气对应分压 − 5（容差）
  - `safe_plasma_max` 增加大气中血浆分压（对等离子耐受更高）
  - `safe_co2_max` = 大气 CO₂ 分压 + 10（容差 ×2；人类仅耐受 10 pp）
  - BZ 致幻阈值（BZ_trip_balls_min）与脑损伤阈值（BZ_brain_damage_min）均随大气 BZ 分压提高
  - **高 miasma（瘴气）大气**: `suffers_miasma = FALSE`——不处理瘴气，直接呼出

### /obj/item/organ/eyes/low_light_adapted —— 低光适应眼

- `color_cutoffs = list(30, 15, 15)`——暗处视觉色差补偿（红色偏移 30 / 绿 15 / 蓝 15）

### /obj/item/organ/tongue/cat/primitive —— 原始猫舌

- `liked_foodtypes = SEAFOOD \| MEAT \| GORE`——偏爱海鲜、肉食与生鲜内脏

## 2.3 语言：Ættmál 与 Siik'Tajr

**源码**: `language.dm`（28 行）

### /datum/language/primitive_catgirl —— Ættmál（埃特莫尔）

- 描述: "A liturgical language passed through three centuries of Hearthkin culture, the only tongue which their literature is allowed to be spoken in; especially relating to their pagan practices. While Siik'Tajr is used as a trade language with outsiders, Ættmál remains sacred and mostly unknown to those outside the Hearth."（一种历经 Hearthkin 三百年文化传承的**礼拜语言**，是其文学唯一允许使用的语言，尤其用于异教仪式。Siik'Tajr 是对外贸易语，而 Ættmál 神圣且炉堂之外几乎无人知晓）
- 按键: "H"；`LANGUAGE_TONGUELESS_SPEECH`（无舌可说的语言）
- 生成参数: 空格率 70、句率 25、词间句率 10、词间空格率 75；附加音节 0/0
- **音节表（约 80 个）**: al/an/ar/að/eg/en/er/ha/he/il/in/ir/ið/ki/le/na/nd/ng/nn/og/ra/ri/se/st/ta/ur/ði/va/ve/sem/sta/til/tur/var/ver/við/ður/það/þei/með/ega/ann/tur/egr/eda/eva/ada/the/tre/tai/thor/thur/ohd/din/gim/per/ger/héð/bur/kóp/vog/bar/dar/akur/jer/bær/múl/fjörð/jah/dah/dim/din/dir/dur/nya/**miau**/**mjau**/ný/kt/hø（含猫叫拟声 miau / mjau）
- 图标: `language_icon.dmi`，icon_state = "omgkittyhiii"
- `default_priority = 94`、`secret = TRUE`（隐藏语言）
- **互相理解度**: 通用语（Common）50%、非通用语（Uncommon）33%

### /datum/language_holder/primitive_felinid

理解 + 会说: `primitive_catgirl`（Ættmál）与 `siiktajr`（灰烬行者贸易语）；默认选中语言 Ættmál。

## 2.4 服装全录（Clothing）

**源码**: `clothing.dm`（174 行）。全部服装图标位于 `icons/clothing_greyscale.dmi`，绝大多数带 **IS_PLAYER_COLORABLE_1（玩家可染色）** 旗标与 greyscale 配置；无传感器（has_sensor = FALSE）。

### 2.4.1 默认套装（/datum/outfit/primitive_catgirl —— "Icemoon Dweller 冰月居民"）

| 部位 | 物品 |
|---|---|
| 制服 | 裹身布（body wraps） |
| 鞋 | 原始徒步靴（primitive hiking boots） |
| 手套 | 臂裹布（arm wraps） |
| 外套 | 原始毛皮大衣（primitive fur coat） |
| 颈 | 围巾（scarf） |
| 背 | 假铜斧（`/obj/item/forging/reagent_weapon/axe/fake_copper`，材料 copporcitite × 1 张——外观是铜斧的演示用品） |

### 2.4.2 下身（Under）

| 物品 | 路径 | 覆盖部位 | 描述 | 默认染色 |
|---|---|---|---|---|
| 裹身布 body wraps | `/obj/item/clothing/under/dress/skirt/primitive_catgirl_body_wraps` | GROIN | "Some pretty simple wraps to cover up your lower bits."（几块遮住下半身的简单布条） | `#cec8bf#364660` |
| 定制连衣裙 tailored dress | `/obj/item/clothing/under/dress/skirt/primitive_catgirl_tailored_dress` | GROIN \| CHEST | "A handmade dress, tailored to fit perfectly to its wearer's body measurements."（量身定制的手工连衣裙） | `#cec8bf#364660` |
| 手制束腰外衣 handmade tunic | `/obj/item/clothing/under/dress/skirt/primitive_catgirl_tunic` | GROIN \| CHEST | 从肩到膝上的简朴衣物，附腰带 | `#cec8bf#faece4#594032` |

### 2.4.3 手部（Hands）

| 物品 | 路径 | 描述 | 默认染色 |
|---|---|---|---|
| 臂裹布 arm wraps | `/obj/item/clothing/gloves/fingerless/primitive_catgirl_armwraps` | "Simple cloth to wrap around one's arms."（缠在手臂上的简单布料） | `#cec8bf` |
| 臂铠 gauntlets | `/obj/item/clothing/gloves/fingerless/primitive_catgirl_gauntlets` | "Simple cloth arm wraps with overlying metal protection."（布制臂裹布 + 外层金属防护） | `#cec8bf#c55a1d` |

### 2.4.4 外套（Suit）

| 物品 | 路径 | 覆盖部位 | 描述 | 默认染色 |
|---|---|---|---|---|
| 原始毛皮大衣 primitive fur coat | `/obj/item/clothing/suit/jacket/primitive_catgirl_coat` | CHEST | "A large piece of animal hide stuffed with fur, likely from the same animal."（一大块塞满毛皮、很可能取自同一只动物的兽皮） | `#594032#cec8bf` |
| 皮革围裙 leather apron | `/obj/item/clothing/suit/apron/chef/colorable_apron/primitive_catgirl_leather` | — | 厨师可染色围裙的原始变体 | `#594032` |

> 毛皮大衣特性: 胸部防寒（cold_protection = CHEST），**最低防寒温度 = FIRE_SUIT_MIN_TEMP_PROTECT**（防火服级别）；支持趾行（digitigrade）变体且不换图标。

### 2.4.5 鞋（Shoes）

| 物品 | 路径 | 描述 | 默认染色 |
|---|---|---|---|
| 原始徒步靴 primitive hiking boots | `/obj/item/clothing/shoes/winterboots/ice_boots/primitive_catgirl_boots` | "A pair of heavy boots lined with fur and with soles special built to prevent slipping on ice."（毛皮衬里的重型靴，鞋底专为冰上防滑设计） | `#594032#cec8bf` |

### 2.4.6 颈部（Neck）——染色变体全录

| 物品 | 路径 | 默认染色 |
|---|---|---|
| 围巾 scarf | `/obj/item/clothing/neck/scarf/primitive_catgirl_scarf` | `#cec8bf#cec8bf` |
| 米白大围巾 large scarf (off-white) | `/obj/item/clothing/neck/large_scarf/primitive_catgirl_off_white` | `#cec8bf#cec8bf` |
| 蓝色无限围巾 infinity scarf (blue) | `/obj/item/clothing/neck/infinity_scarf/primitive_catgirl_blue` | `#364660` |
| 米白披肩 mantle (off-white) | `/obj/item/clothing/neck/mantle/recolorable/primitive_catgirl_off_white` | `#cec8bf` |
| 皮革游侠斗篷 ranger poncho (leather) | `/obj/item/clothing/neck/ranger_poncho/primitive_catgirl_leather` | `#594032#594032` |

### 2.4.7 面具（Mask）

| 物品 | 路径 | 默认染色 |
|---|---|---|
| 脖套面罩 neck gaiter | `/obj/item/clothing/mask/neck_gaiter/primitive_catgirl_gaiter` | `#364660` |

### 2.4.8 头部（Head）

| 物品 | 路径 | 描述 | 默认染色 |
|---|---|---|---|
| 兜帽 standalone hood | `/obj/item/clothing/head/standalone_hood/primitive_catgirl_colors` | 独立兜帽 | `#594032#364660` |
| 额饰 Ferroniere | `/obj/item/clothing/head/primitive_catgirl_ferroniere` | "A style of headband that encircles the wearer's forehead, with a small jewel suspended in the centre."（环绕额头的头带，中央悬挂一颗小宝石） | `#f1f6ff#364660`；重量 WEIGHT_CLASS_TINY |

## 2.5 衣柜售货机（Wardrobe Vendor）

**源码**: `clothing_vendor.dm`（47 行）

### /obj/machinery/vending/primitive_catgirl_clothing_vendor —— 衣柜（Wardrobe）

- 描述: "It's a big wardrobe filled up with all sorts of clothing."（装满各种衣服的大衣柜）
- 属性: 图标 `closet.dmi` "cabinet"；**不用电**（use_power = FALSE）、**静音**（shut_up = TRUE，speak() 被覆写为空）、**onstation = FALSE**（不会被品牌智能事件锁定）、**all_products_free = TRUE**（全部免费——"it's a wardrobe"）

**商品全录（31 种）**（商品 = 库存数量）:

| # | 商品 | 数量 | # | 商品 | 数量 |
|---|---|---|---|---|---|
| 1 | body wraps 裹身布 | 15 | 17 | mantle (off-white) 披肩 | 5 |
| 2 | tailored dress 定制连衣裙 | 15 | 18 | ranger poncho 皮革斗篷 | 5 |
| 3 | handmade tunic 束腰外衣 | 15 | 19 | wide cape 宽斗篷 | 5 |
| 4 | loincloth 缠腰布 | 5 | 20 | robe cape 长袍披风 | 5 |
| 5 | loincloth alt 缠腰布（变体） | 5 | 21 | long cape 长披风 | 5 |
| 6 | primitive fur coat 毛皮大衣 | 15 | 22 | eyepatch (wrap) 眼罩 | 5 |
| 7 | arm wraps 臂裹布 | 15 | 23 | Ferroniere 额饰 | 5 |
| 8 | hiking boots 徒步靴 | 15 | 24 | snow tiger pelt 雪虎皮帽 | 5 |
| 9 | gauntlets 臂铠 | 10 | 25 | pelt 兽皮帽 | 5 |
| 10 | neck gaiter 脖套面罩 | 10 | 26 | black pelt 黑兽皮帽 | 5 |
| 11 | leather apron 皮革围裙 | 10 | 27 | white pelt 白兽皮帽 | 5 |
| 12 | standalone hood 兜帽 | 10 | 28 | wolf pelt 狼皮帽 | 5 |
| 13 | scarf 围巾 | 5 | 29 | white wolf pelt 白狼皮帽 | 5 |
| 14 | face scarf 蒙面围巾 | 5 | 30 | papakha 帕帕哈帽 | 5 |
| 15 | large scarf 大围巾 | 5 | 31 | hair tie 发圈 | 5 |
| 16 | infinity scarf 无限围巾 | 5 | | | |

## 2.6 地洞重生点、职业与阵营（Spawner / Job / Team / Antag）

**源码**: `spawner.dm`（319 行）

### 2.6.1 地洞重生点 —— /obj/effect/mob_spawn/ghost_role/human/primitive_catgirl

- 名称: "hole in the ground"（地上的洞）；描述: "A clearly hand dug hole in the ground that appears to lead into a small cave of some kind? It's pretty dark in there."（一个明显是手挖的洞，似乎通向某个小洞穴？里面很黑）
- 幽灵提示名: "icemoon dweller"（冰月居民）；职业: `/datum/job/primitive_catgirl`（"Icemoon Dweller"）
- 种族: primitive felinid；装备: 默认套装（2.4.1）；density = FALSE（可穿过）
- **you_are_text**: "You are an icemoon dweller."（你是一名冰月居民）
- **flavour_text**: "For as long as you can remember, the icemoon has been your home. It's been the home of your ancestors, and their ancestors, and the ones before them. Currently, you and your kin live in uneasy tension with your nearby human-and-otherwise neighbors. Keep your village and your Kin safe, but bringing death on their heads from being reckless with the outsiders will not have the Gods be so kind."（自记事起冰月就是你的家，也是你祖先、祖先的祖先的家。眼下你与族人正和附近的人类等邻居处于紧张共存之中。保护好你的村庄与族人；但若因对外来者鲁莽而招致死亡降临族人头上，诸神可不会仁慈）
- 参数: `restricted_species` = 仅原始类人；`quirks_enabled = TRUE`；`allow_custom_character = GHOSTROLE_TAKE_PREFS_APPEARANCE`（采用玩家外观预设）；`loadout_enabled = FALSE`；**uses = 12（12 个名额）**；`deletes_on_zero_uses_left = FALSE`（用尽不消失）；`interaction_flags_mouse_drop = NEED_DEXTERITY`
- 每个 ckey **只能重生一次**（allow_spawn 检查 team.players_spawned）
- important_text: 完整政策链接（配置项 `icecats_policy_link`）

**队伍与日志**: 每个地洞持有 `/datum/team/primitive_catgirls`；`went_back_to_sleep`（回洞休眠者名单）、`join_and_leave_log_cache`（进出日志缓存）、`ssd_time = 30 分钟`（离线下限）。
- 查看（examine）: 显示剩余名额（"还有 N 次使用" / "已无剩余"）；原始类人或观察者可看更多
- 深入查看（examine_more，仅原始类人/观察者）: "Everyone still seems to be sleeping peacefully in the hole."（洞里的人似乎都在安睡）；有成员离开时 "You smell that the following kin are missing from the hole:"（你闻到以下族人不在洞里:）列出名单；有人回洞时 "You catch the scent of the following kin having recently went back to sleep:"（你闻到以下族人刚回去睡了:）

### 2.6.2 拖入地洞（put_back_in 回洞机制）

鼠标拖拽原始类人（或自己）到地洞:
1. 目标必须: 是原始类人、未骑乘（unbuckled）、未死亡
2. **拖别人**: 对方必须有意识判定——若无脑、或（有 mind 且无 SSD 指示器）则拒绝；**下线不足 30 分钟**拒绝（提示剩余分钟数，并记 admin 日志）；tgui 确认 "Yes/No"
3. **拖自己**: tgui 确认
4. 3 秒 do_after → `put_back_in`:
   - 剥离身上所有物品（**出生装备黑名单**保留：制服/鞋/手套/外套/围巾/背上的斧，ABSTRACT 与 NODROP 物品保留；其余全部掉地）
   - 从 team 移除成员、`players_spawned` 减去 ckey（可再次重生）、名字加入 `went_back_to_sleep`
   - 移除幽灵记录（ghost_records）→ 灵魂强制移到洞口 → ghostize → qdel 身体 → **uses + 1（名额返还）**

### 2.6.3 队伍与反派（Team & Antagonist）

| Datum | 名称 | 说明 |
|---|---|---|
| `/datum/job/primitive_catgirl` | Icemoon Dweller | 地洞职业 |
| `/datum/team/primitive_catgirls` | **Icewalkers 冰行者**（成员名 Icewalker） | show_roundend_report = FALSE；roundend 报告: 有成员则列出玩家列表，无成员则 "But none of its members woke up!"（但没有任何成员醒来！） |
| `/datum/antagonist/primitive_catgirl` | **Icewalker 冰行者** | pref_flag = **ROLE_LAVALAND**（被灰烬行者封禁者同样不能玩此角色）；antagpanel_category = "Icemoon Dwellers"；antag_flags = ANTAG_FAKE \| ANTAG_SKIP_GLOBAL_LIST；show_in_roundend = FALSE、show_in_antagpanel = FALSE、show_to_ghosts = TRUE；持有队伍引用（feline_team），用于幽灵轨道菜单 |

**阵营解锁配方（antag_recipes，13 个）**:

| # | 配方 | 译名 |
|---|---|---|
| 1 | anointing_oil | 涂油（仪式用油） |
| 2 | black_pelt_bed | 黑兽皮床 |
| 3 | boneaxe | 骨斧 |
| 4 | bonedagger | 骨匕首 |
| 5 | bonespear | 骨矛 |
| 6 | frozen_breath | 冻结吐息 |
| 7 | handcrafted_hearthkin_armor | 手制炉族盔甲 |
| 8 | hearthkin_ship_fragment_inactive | 炉族飞船碎片（未激活） |
| 9 | runic_greataxe | 符文巨斧 |
| 10 | runic_greatsword | 符文巨剑 |
| 11 | runic_spear | 符文长矛 |
| 12 | skeleton_key | 骷髅钥匙 |
| 13 | white_pelt_bed | 白兽皮床 |

> 配方本体（所需材料/产物）定义于其他模块（tribal_extended / 符文武器 / 家具等），本模块仅授予冰行者阵营。

## 2.7 杂项物品（嗅盐 / 翻译项链）

### 2.7.1 嗅盐 —— /obj/item/smelling_salts

**源码**: `smelling_salts.dm`（73 行）
- 名称 "smelling salts"（嗅盐）；描述: "A small pile of a salt-like substance that smells absolutely repulsive. Rumor has it that the smell is so pungent that even the dead will come back to life to escape it."（一小堆气味令人作呕的盐状物。据说气味之刺鼻，连死人都要活过来逃离它）
- 属性: WEIGHT_CLASS_TINY、FLAMMABLE、NOBLUDGEON（不能当钝器）
- **使用**: 攻击（attack）一名**死亡**的碳基生物（不能对自己、不能对活物）→ `try_revive`:
  1. 目标收到"You are being brought back to life!"（你正在被带回人世！）并 grab_ghost（拉回灵魂）
  2. 3 秒 do_after（目标旁）
  3. **复活判定（can_defib，同除颤器规则）**，失败原因分类:
     - 无反应（无法回来）: 自杀 / DNR（拒绝复苏）/ 黑名单 / 无智力
     - 有点反应但不对劲: 无心脏 / 心脏衰竭 / 脑衰竭
     - 身体损坏过重: 组织损伤 / 干尸（husk）
     - 无脑: "head looks like it's missing something important"（脑袋里少了什么重要的东西）
     - 健康值 ≤ FULLCRIT 阈值: 身体损坏过重
  4. 成功: 缺氧伤害 +60、播放男性/女性嗅声（male_sniff.ogg / female_sniff.ogg）、**治愈心脏病发作**（set_heartattack(FALSE)）、`revive()` 复活、播放 blackoutpolicy 黑屏政策文案、记 log_combat

### 2.7.2 古董翻译项链 —— /obj/item/clothing/neck/necklace/translator/hearthkin

**源码**: `translator.dm`（21 行）
- 名称 "antique necklace"（古董项链）；描述: "A necklace with a old, strange device as its pendant. Symbols constantly seem to appear on its screen, as noises happen around it, but its purpose is not immediately apparent."（挂着古老奇特装置的项链。屏幕不断浮现符号，周围响起噪声，用途一时不明）
- 图标: `translator.dmi` + `translator_worn.dmi`，icon_state "translator"
- **语言授予**: `language_granted = /datum/language/siiktajr`（灰烬行者贸易语）
- 装备反馈: 提示获得 Siik'Tajr；卸下反馈: 提醒失去该语言

## 2.8 地图物件（Map Items）

**源码**: `map_items.dm`（63 行）

### 2.8.1 预装烤架篝火 —— /obj/structure/bonfire/grill_pre_attached

- 篝火变体: `grill = TRUE`，生成时直接叠加 "bonfire_grill" 烤架层（开局即可烧烤）

### 2.8.2 冰月可耕泥土 —— /turf/open/misc/dirt/icemoon

- 基底 turf = icemoon openspace；气体 = "ICEMOON_ATMOS"
- 生成时附加 `/datum/component/simple_farm`（set_plant = TRUE）——**可种植**的冰月泥土

### 2.8.3 冰月温泉 —— /turf/open/water/hot_spring/icemoon

- 描述: "Water kept warm through some unknown heat source, possibly a geothermal heat source far underground. Whatever it is, it feels pretty damn nice to swim in given the rest of the environment around here, and you can even catch a glimpse of the odd fish darting through the water."（水被某种未知热源保温，可能是深层地热。不管是什么，在这片环境里泡着可太舒服了，还能瞥见怪鱼在水中穿梭）
- 基底 turf = icemoon openspace；气体 = "ICEMOON_ATMOS"；钓鱼源 = `/datum/fish_source/icecat_hot_spring`
- **浸泡（dip_in）定制效果**（无负面心情）: 清洗（CLEAN_RAD \| CLEAN_WASH）→ 活物获得 `hot_spring` 再生状态效果（washing_regen/hot_spring）+ **心情事件 +2**（"The water was enjoyably warm!" 水暖得舒服！）

### 2.8.4 温泉钓鱼表 —— /datum/fish_source/icecat_hot_spring

| 渔获 | 权重 | 目录名 |
|---|---|---|
| 矮月鱼 moonfish/dwarf | 5 | "Icemoon Hot Springs"（冰月温泉） |
| 针鱼 needlefish | 10 | |
| 盔甲鱼 armorfish | 10 | |
| 冰裂隙蟹 chasm_crab/ice | 5 | |
| 骨板 bone sheet | 5 | |

### 2.8.5 营地区域 —— /area/ruin/unpowered/primitive_catgirl_den

- 名称: "**Icewalker Camp**（冰行者营地）"——无电力废墟区域，野猫族聚落

---

# 第三卷 · 原始建筑（Primitive Structures）

**代码**: `modular_nova/modules/primitive_structures/code/`（595 行，7 个 .dm）——fencing.dm（108）/ furniture.dm（20）/ storage_structures.dm（166）/ totally_thatch_roof.dm（68）/ wall_torch.dm（206）/ windows.dm（20）/ wooden_ladder.dm（7）
**模块简介**: 冰猫与灰烬行者风格的原始建筑与结构（readme 同 primitive 系）。木制结构普遍 **FLAMMABLE（易燃）**。

## 3.1 栅栏与门（Fencing & Gates）

**源码**: `fencing.dm`（108 行）

### 3.1.1 木栅栏 —— /obj/structure/railing/wooden_fencing

- 名称 "wooden fence"（木栅栏）；描述: "A basic wooden fence meant to prevent people like you either in or out of somewhere."（用来把人拦在某处内或外的简易木栅栏）
- 属性: FLAMMABLE、ON_BORDER_1（边缘物）、材料 5 块木板；`random_icons = TRUE`——生成时从 **"fence" / "fence_2" / "fence_3"** 随机外观
- **wirecutter 无法拆除**（tool_blocker TOOL_WIRECUTTER）；撬棍拆除（crowbar_act）→ 拆解返还 **5 块木板**（atom_deconstruct）
- 图标: `wooden_fence.dmi`

### 3.1.2 木栅栏门 —— /obj/structure/railing/wooden_fencing/gate

- 名称 "wooden fence gate"（木栅栏门）；描述: "A basic wooden gate meant to prevent animals like you escaping."（防止像你这样的动物逃出去的简易木门）
- `opened` 状态；**徒手点击切换开/关**: 开门 → density = FALSE、图标 "gate_open"；关门 → density = TRUE、图标 "gate"
- 音效: 木质衣柜开门/关门声（wooden_closet_open.ogg / wooden_closet_close.ogg，音量 100）

### 3.1.3 大型木门 —— /obj/structure/mineral_door/wood/large_gate

- 名称 "large wooden gate"（大型木门）——用于营地大门/入口
- 图标: `wooden_gate.dmi`；开关音效同上
- **Open**: 不透明 → 透明、density FALSE、图层 OPEN_DOOR_LAYER、更新大气（air_update_turf）
- **Close**: 若门格内有活物阻挡则拒绝关闭；恢复不透明 + density TRUE、还原图层、更新大气
- 细节: 朝东（EAST）打开时图层升到 ABOVE_MOB_LAYER（人物可从门后穿过）

## 3.2 储物结构（Storage Structures）

**源码**: `storage_structures.dm`（166 行）

### 3.2.1 木架 —— /obj/structure/rack/wooden

- 名称 "shelf"（架子）；FLAMMABLE、NEED_DEXTERITY、材料 2 块木板；图标 `storage.dmi` "shelf_wood"
- **拖放摆位**: 把手中物品拖到架子上 → 物品落架并按鼠标落点定位像素偏移（pixel_x 钳制在 ±16 内、pixel_y 上半区 10 / 下半区 −4），实现"陈列"效果
- 扳手无法移动（tool_blocker TOOL_WRENCH 次要动作）；撬棍 2 秒拆除 → 返还 **2 块木板**

### 3.2.2 储物桶 —— /obj/structure/closet/crate/wooden/storage_barrel

- 名称 "storage barrel"（储物桶）；描述: "This barrel can't hold liquids, it can just hold things inside of it however!"（这桶装不了液体，但能装东西！）
- 属性: FLAMMABLE、材料 4 块木板；撬棍开合（cutting_tool = crowbar）；拆解返还 4 块木板；图标 "barrel"（木桶造型，行为同板条箱）

### 3.2.3 木质智能冰箱基类 —— /obj/machinery/smartfridge/wooden（"debug wooden smartfridge"）

- 描述: "You should not be seeing this!"（你不该看到这个！）——**纯基类，直接生成会被 qdel**（INITIALIZE_HINT_QDEL）
- 属性: 不用电（NO_POWER_USE / light_power 0 / idle 0）、无电路板（circuit = null）、无发光（has_emissive = FALSE）、integrity_failure = 0、可透气（ATMOS_PASS_YES）、内容可见（visible_contents = TRUE）、不可焊接固定、无出货音
- 螺丝刀/撬棍锁定（tool_blocker）；撬棍 2 秒拆解 → 返还 **10 块木板**；structure_examine: "The whole rack can be pried apart."（整个架子可以撬开）
- **四种实用变体（各 10 块木板）**:

| 变体 | 名称 | 描述 | 收纳规则 | 图标/覆盖层 |
|---|---|---|---|---|
| `produce_bin` | produce bin 农产品箱 | "A wooden hamper, used to hold plant products and try to keep them safe from pests."（装农产品、防虫害的木筐） | 只收 `/obj/item/food/grown`、`/obj/item/grown`、`/obj/item/graft` | "producebin" / "produce" |
| `seed_shelf` | seed shelf 种子架 | "A wooden shelf, used to hold seeds, preventing them from germinating early."（存放种子、防止提前发芽的木架） | 只收 `/obj/item/seeds` | "seedshelf" / "seed" |
| `ration_shelf` | ration shelf 口粮架 | "A wooden shelf, used to store food... Preferably preserved."（存放食物……最好是腌制的） | 可食用物，或**装有试剂**的碗（bowl 且有试剂） | "rationshelf" / "ration" |
| `produce_display` | produce display 农产品展示台 | "A wooden table with awning, used to display produce items."（带遮阳篷的展示木桌） | 花式食物（food/grown 且 slot_flags ≠ NONE，如花束）+ 手工艺品: grown / bouquet / garland / cloth / durathread / leather / wood / bamboo | "producedisplay" / "nonfood" |

## 3.3 茅草屋顶与茅草块（Thatch Roofing & Flooring）

**源码**: `totally_thatch_roof.dm`（68 行）

### 3.3.1 茅草屋顶 —— /turf/open/misc/grass/roofing

- 名称 "thatched roof"（茅草屋顶）；描述: "A collection of various dried greens, not so green anymore, that makes a passable roof material."（一堆各种干枯的绿色植物——已经不怎么绿了——凑合能当屋顶材料）
- 基底 turf = icemoon openspace、气体 "ICEMOON_ATMOS"；图标 `thatch.dmi`（可平滑）

### 3.3.2 茅草块地板 —— /turf/open/floor/grass/thatch

- 名称 "thatch patch"（茅草块）；描述: 同上但作地板材料
- 属性: SMOOTH_BITMASK 平滑、可与草地组 + 封闭 turf 平滑衔接（canSmoothWith）、图层 HIGH_TURF_LAYER、掉落的铺地砖 = 茅草瓦片
- 损坏/烧毁贴图: "grass_damaged"（`damaged.dmi`）；无子弹弹跳声
- 生成细节: 变换矩阵平移 (−9, −9) 对齐图标

### 3.3.3 茅草瓦片 —— /obj/item/stack/tile/grass/thatch

- 名称 "thatch tile"（茅草瓦片）、单数 "thatch floor tile"（茅草铺地砖）；描述: "A patch of thatch like in those old-school barns."（像老式谷仓那样的茅草块）
- 属性: FLAMMABLE；turf_type = thatch 地板；可堆叠；手持图标 `tile_lefthand.dmi` / `tile_righthand.dmi`

### 3.3.4 茅草（作物）—— /obj/item/food/grown/grass/thatch

- 名称 "thatch"（茅草）、描述 "Yellow and dry."（又黄又干）
- 转化: 普通草（`/obj/item/food/grown/grass`）带**可干燥元素**（dryable）→ 干燥后变为茅草（stacktype = 茅草瓦片）——**茅草瓦片的核心来源**

## 3.4 壁挂火把（Wall Torch）

**源码**: `wall_torch.dm`（206 行）

### 3.4.1 壁挂火把 —— /obj/structure/wall_torch

- 名称 "mounted torch"（壁挂火把）；描述: "A simple torch mounted to the wall, for lighting and such."（挂在墙上的简易火把，用于照明等）
- 属性: 锚定、无密度（density = FALSE）、火光色 LIGHT_COLOR_FIRE；图标 `lighting.dmi` "walltorch"
- 状态: `mounted_torch`（内装火把，默认 TRUE）、`burning`（是否点燃）、`spawns_lit`（是否生成即点燃）、`wallmount_item_type` = torch_mount 墙框
- 地图方向助手: MAPPING_DIRECTIONAL_HELPERS（28 像素偏移）
- 名称/描述随状态变化: 有火把 = "mounted torch"；无火把 = "torch mount"（"A simple torch mount, torches go here." 火把放这儿的简单挂座）

**交互全录**:
| 动作 | 效果 |
|---|---|
| 空挂座 + 火把点击 | 装上火把（forceMove 进挂座、注册 qdel 信号）；若火把点着则立即点燃挂座，否则熄灭并 turn_off |
| 点燃的挂座 + 高温工具（温度 ≥ FIRE_MINIMUM_TEMPERATURE_TO_EXIST） | 点燃（light_it_up） |
| 火焰接触（fire_act） | 点燃 |
| 徒手点击（attack_hand） | 取下火把（remove_torch，尝试放入手中） |
| 扳手（wrench_act） | 取下火把 → 生成 1 个**火把挂座墙框**（`/obj/item/wallframe/torch_mount`）→ 挂座本体消失 |
| 销毁（Destroy） | 火把掉落地面（drop_torch） |

**点燃细节**: `burning = TRUE`、光照 set_light(4)、图标追加 "_on"；熄灭则 set_light(0)。内装火把类型为 `/obj/item/flashlight/flare/torch`。

### 3.4.2 变体

| 变体 | 说明 |
|---|---|
| `/obj/structure/wall_torch/mount_only` | 空挂座（mounted_torch = null），名称 "torch mount" |
| `/obj/structure/wall_torch/spawns_lit` | 生成即点燃（spawns_lit = TRUE） |

### 3.4.3 火把挂座墙框 —— /obj/item/wallframe/torch_mount

- 名称 "torch mount"（火把挂座）；描述: "Used to attach torches to walls."（用于把火把挂到墙上）
- 材料 1 块铁板；result_path = mount_only；pixel_shift = 28
- **建造检查（try_build）**: 距墙 ≤ 1 格；玩家必须面向基本方向（cardinal）；目标处无同类墙体物品（check_wall_item）；否则分别提示"太远 / 必须面向墙 / 那里已有东西"

## 3.5 窗户 / 木梯 / 家具

### 3.5.1 绿玻璃窗 —— /obj/structure/window/green_glass_pane

**源码**: `windows.dm`（20 行）
- 名称 "green glass window"（绿玻璃窗）；描述: "A handcrafted green glass window. At least you can still see through it."（手工打造的绿玻璃窗——至少还能看穿它）
- 属性: 全格窗（fulltile = TRUE）、PREVENT_CLICK_UNDER_1、拆除无碎片（NO_DEBRIS_AFTER_DECONSTRUCTION）、材料 2 张玻璃板；图标 `windows.dmi` "green_glass"
- **配方**: 铁试剂 × 5 + 玻璃板 × 2，耗时 0.2 秒，分类 CAT_STRUCTURE

### 3.5.2 木梯 —— /obj/structure/ladder/wood

**源码**: `wooden_ladder.dm`（7 行）
- 名称 "wooden ladder"（木梯）；描述: "Up or down, whatever your mood you sure wood find what you're looking for with this ladder."（上或下，随你心情，用这梯子你肯定"木"有所获——wood/wood 双关）
- 行为与普通梯子完全相同，仅多 **FLAMMABLE（易燃）**；图标 `wooden_ladder.dmi`

### 3.5.3 弓箭靶 —— /obj/item/target/archery

**源码**: `furniture.dm`（20 行）
- 名称 "archery target"（弓箭靶）；描述: "A shooting target, specifically for bows."（专为弓设计的射击靶）
- 子弹命中音 = 木制命中（SFX_BULLET_IMPACT_WOOD）；材料 4 块木板；图标来自 `tribal_extended/icons/items_and_weapons.dmi`
- **配方**: 茅草瓦片 × 4 + 木板 × 4，分类 CAT_FURNITURE，旗标 CRAFT_CHECK_DENSITY \| CRAFT_ONE_PER_TURF（每格一个）

---

## 附录 · 代码路径索引

## 原始工艺（Primitive Production）— `modular_nova/modules/primitive_production/`

| 文件 | 行数 | 内容 |
|---|---|---|
| `code/ceramics.dm` | 368 | 粘土材料/粘土砖、取水制粘土、拉坯机、6 种未烧制陶瓷、陶罐盆栽、出口单据 |
| `code/glassblowing.dm` | 545 | 熔融玻璃、吹杆 + TGUI、5 件吹制工具、6 种制品与步骤表、出口单据 |
| `code/misc.dm` | 35 | 碎玻璃锤碎回收（4 色玻璃矿石）、零价值矿石提示 |
| `code/production_skill.dm` | 18 | 生产技能 /datum/skill/production、传奇生产者斗篷 |
| `icons/` | — | cloaks.dmi / neck.dmi / prim_fun.dmi |

## 野猫族（Primitive Catgirls）— `modular_nova/modules/primitive_catgirls/`

| 文件 | 行数 | 内容 |
|---|---|---|
| `code/species.dm` | 120 | 原始类人种族（温度/特质/嗅觉突变）、炉族五段史传说 |
| `code/organs.dm` | 71 | 耐寒肺（按冰月大气校准）、低光眼、原始猫舌 |
| `code/language.dm` | 28 | Ættmál 语言（音节表/互相理解度） |
| `code/clothing.dm` | 174 | 默认套装 + 15 件服装（染色变体全录） |
| `code/clothing_vendor.dm` | 47 | 衣柜售货机（31 种免费商品） |
| `code/spawner.dm` | 319 | 地洞重生点（12 名额）、回洞机制、职业/队伍/反派、13 个阵营配方 |
| `code/smelling_salts.dm` | 73 | 嗅盐复活物品（除颤规则判定） |
| `code/translator.dm` | 21 | 古董翻译项链（授予 Siik'Tajr） |
| `code/map_items.dm` | 63 | 烤架篝火、冰月耕土、冰月温泉 + 钓鱼表、冰行者营地 |
| `icons/` | — | clothing_greyscale.dmi / gods_statue.dmi / language_icon.dmi / objects.dmi / organs.dmi / salts.dmi / translator.dmi / translator_worn.dmi |

## 原始建筑（Primitive Structures）— `modular_nova/modules/primitive_structures/`

| 文件 | 行数 | 内容 |
|---|---|---|
| `code/fencing.dm` | 108 | 木栅栏（3 随机外观）、栅栏门、大型木门 |
| `code/furniture.dm` | 20 | 弓箭靶 |
| `code/storage_structures.dm` | 166 | 木架、储物桶、木质智能冰箱基类 + 4 变体 |
| `code/totally_thatch_roof.dm` | 68 | 茅草屋顶/地板/瓦片、草→茅草干燥转化 |
| `code/wall_torch.dm` | 206 | 壁挂火把（点燃/取下/扳手拆卸）、2 变体、挂座墙框 |
| `code/windows.dm` | 20 | 绿玻璃窗 + 配方 |
| `code/wooden_ladder.dm` | 7 | 木梯（易燃） |
| `icons/` | — | lighting.dmi / storage.dmi / thatch.dmi / thatch_obj.dmi / tile_lefthand.dmi / tile_righthand.dmi / windows.dmi / wooden_fence.dmi / wooden_gate.dmi / wooden_ladder.dmi |

## 跨模块联动（非本卷代码）

| 联动点 | 所在模块 | 说明 |
|---|---|---|
| 锻炉烧陶瓷 / 熔玻璃 | `modular_nova/modules/reagent_forging/code/forge.dm` | handle_ceramics / handle_glass_sheet_melting / handle_metal_cup_melting；MIN_FORGE_TEMP = 50、基准动作 4 秒、加热时长 25 秒 |
| 粘土炉灶 / 粘土烤炉 | `modular_nova/modules/primitive_cooking_additions/` | stone_stove / stone_oven 系的原始炊具（clay 变体由本卷配方产出） |
| 弓箭靶图标 | `modular_nova/modules/tribal_extended/` | items_and_weapons.dmi |
| 灰烬行者贸易语 Siik'Tajr | `modular_nova/`（语言系） | 野猫族默认掌握 + 翻译项链授予 |
| 13 个阵营配方本体 | 各模块（tribal_extended / 符文 / 家具等） | 由 `/datum/antagonist/primitive_catgirl` 的 antag_recipes 授予 |
