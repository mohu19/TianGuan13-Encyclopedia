# TianGuan13 锻造与试剂加工百科 (Reagent Forging Encyclopedia)

> **项目**: TianGuan13（NovaSector 分支，基于 /tg/station）
> **模块**: `reagent_forging`（MODULE ID: `REAGENT_FORGING`）· 作者: Jake Park
> **源码路径**: `modular_nova/modules/reagent_forging/`（12 个 .dm，共 **2,989 行**）
> **文档性质**: 双语全量百科，铁砧/锻炉/铸造台/配方全录/锻造成品无省略
> **相关图标**: `icons/obj/forge_structures.dmi`、`forge_items.dmi`、`forge_clothing.dmi`、`cloaks.dmi`；`icons/mob/forge_weapon_{worn,l,r}.dmi`、`neck.dmi`、`clothing/forge_clothing*.dmi`、`hud/forge_radials.dmi`
> **音效**: `sound/forge.ogg`（锤击）、`sound/hot_hiss.ogg`（淬火）

**模块简介**：试剂锻造（Reagent Forging）是一种锻造玩法，玩家可打造**可注入（imbue）试剂**的武器与衣物。已注入的物品能对目标**永久注射**所注入的试剂——武器每次命中注入 1u，衣物每 3 秒注入 0.5u。

---

## 目录 (Table of Contents)

- [卷一 · 系统总览与核心常量](#卷一--系统总览与核心常量)
- [卷二 · 锻炉 Forge](#卷二--锻炉-forge)
  - [2.1 锻炉结构与燃料](#21-锻炉结构与燃料-forge-structure--fuel)
  - [2.2 温度与烟雾机制](#22-温度与烟雾机制-temperature--smoke)
  - [2.3 锻炉等级与升级](#23-锻炉等级与升级-forge-levels--upgrades)
  - [2.4 锻炉交互全录](#24-锻炉交互全录-interactions)
  - [2.5 试剂注入（附魔）机制](#25-试剂注入附魔机制-reagent-imbuing)
  - [2.6 烘焙与窑烧](#26-烘焙与窑烧-baking--ceramics)
- [卷三 · 铁砧 Anvil](#卷三--铁砧-anvil)
  - [3.1 铁砧结构与锤击流程](#31-铁砧结构与锤击流程-structure--hammering)
  - [3.2 铁砧修复与坠落伤害](#32-铁砧修复与坠落伤害-repair--falling-hazard)
- [卷四 · 铸造台与配方全录](#卷四--铸造台与配方全录)
  - [4.1 铸造台机制](#41-铸造台机制-crafting-bench-mechanics)
  - [4.2 配方全录（19 配方）](#42-配方全录19-配方-all-recipes)
- [卷五 · 锻造工具与中间产物](#卷五--锻造工具与中间产物)
  - [5.1 允许锻造材料全录（20 种）](#51-允许锻造材料全录20-种-allowed-forging-materials)
  - [5.2 锻造工具](#52-锻造工具-tools)
  - [5.3 半成品（incomplete）全录（14 种）](#53-半成品incomplete全录14-种)
  - [5.4 成品（complete）全录（13 种）](#54-成品complete全录13-种)
  - [5.5 其他锻造物品](#55-其他锻造物品-misc-forge-items)
- [卷六 · 锻造成品武器全录](#卷六--锻造成品武器全录)
  - [6.1 基础属性与通用组件](#61-基础属性与通用组件-base--shared-components)
  - [6.2 武器全录（11 种）](#62-武器全录11-种-weapons)
  - [6.3 盾牌（2 种）](#63-盾牌2-种-shields)
  - [6.4 工具武器与箭头转换](#64-工具武器与箭头转换-tool-weapons--arrows)
- [卷七 · 锻造成品护甲与服装全录](#卷七--锻造成品护甲与服装全录)
  - [7.1 护甲数据模板（4 个）](#71-护甲数据模板4-个-armor-datums)
  - [7.2 服装全录（8 件）](#72-服装全录8-件-clothing)
- [卷八 · 试剂注入组件系统](#卷八--试剂注入组件系统)
  - [8.1 常量与衣物组件 reagent_clothing](#81-常量与衣物组件-reagent_clothing)
  - [8.2 武器组件 reagent_weapon](#82-武器组件-reagent_weapon)
- [卷九 · 水槽 Water Basin](#卷九--水槽-water-basin)
- [卷十 · 锻造技能与前置配方](#卷十--锻造技能与前置配方)
- [附录 A · 代码路径索引](#附录-a--代码路径索引)

---

# 卷一 · 系统总览与核心常量

**源码**: `code/forge.dm`（1,014 行，本模块核心）+ `code/tool_override.dm`（37 行，新工具行为钩子）

## 1.1 核心常量全表

| 常量 | 值 | 说明 |
|---|---|---|
| `BASELINE_ACTION_TIME` | 4 秒 | 锻炉基础动作时间（玻璃加热/陶瓷定型等） |
| `BASELINE_HEATING_DURATION` | 25 秒 | 熔融玻璃加热后保持可加工的基础时长 |
| `FORGE_DEFAULT_TEMPERATURE_CHANGE` | 5 | 锻炉每进程温度变化量 |
| `MAX_FORGE_TEMP` | 100 | 锻炉最高温度 |
| `MIN_FORGE_TEMP` | 50 | 锻炉最低可用温度（低于此温度无法冶炼/加热/定型） |
| `FORGE_HEATING_DURATION` | 1 分钟 | 锻炉加热物品的可加工持续时间 |
| `MAX_TEMPERATURE_LOSS_DECREASE` | 5 | 升级可带来的最大降温减免 |
| `CHARCOAL_CHANCE` | 45（%） | 每根木材添加后未来形成木炭的几率 |
| `MINIMUM_IMBUING_REAGENT_AMOUNT` | 100（u） | 武器/护甲注入试剂所需的最少单位 |
| `WEAPON_COMPLETION_WOOD_AMOUNT` | 2 | 完成一件武器所需木材数（crafting_bench.dm） |
| `BAD_HIT_PENALTY` | 3 | 坏锤击的惩罚（回退锤击数，crafting_bench.dm） |
| `MAX_IMBUE_STORAGE` | 250（u） | 注入物品试剂容量上限（reagent_component.dm） |
| `REAGENT_CLOTHING_INJECT_AMOUNT` | 0.5（u） | 衣物每次注入量（reagent_component.dm） |
| `REAGENT_WEAPON_INJECT_AMOUNT` | 1（u） | 武器每次命中注入量（reagent_component.dm） |
| `REAGENT_WEAPON_DAMAGE_MULTIPLIER` | 2 | 武器每次命中因注入受到的耐久损耗倍数（reagent_component.dm） |
| `FAUNA_MULTIPLIER` / `MEGAFAUNA_MULTIPLIER` | 2 | 对采矿/野兽类生物（MOB_MINING\|MOB_BEAST）伤害倍率（forge_weapons.dm） |

## 1.2 锻炉等级常量（forge.dm）

| 常量 | 值 | 对应技能等级 |
|---|---|---|
| `FORGE_LEVEL_YOU_PLAY_LIKE_A_NOOB` | 1 | 无等级（新手） |
| `FORGE_LEVEL_NOVICE` | 2 | 学徒级 Novice |
| `FORGE_LEVEL_APPRENTICE` | 3 | 见习级 Apprentice |
| `FORGE_LEVEL_JOURNEYMAN` | 4 | 熟练级 Journeyman |
| `FORGE_LEVEL_EXPERT` | 5 | 专家级 Expert |
| `FORGE_LEVEL_MASTER` | 6 | 大师级 Master |
| `FORGE_LEVEL_LEGENDARY` | 7 | 传说级 Legendary |

## 1.3 烟雾状态常量（forge.dm）

| 常量 | 值 | 含义 |
|---|---|---|
| `SMOKE_STATE_NONE` | 0 | 无燃料/熄灭（图标 `forge_inactive`，关灯） |
| `SMOKE_STATE_GOOD` | 1 | 烘焙良好（淡蒸汽颗粒 `/particles/smoke/steam/mild`） |
| `SMOKE_STATE_NEUTRAL` | 2 | 烘焙中等（蒸汽颗粒 `/particles/smoke/steam`） |
| `SMOKE_STATE_BAD` | 3 | 烘焙烧焦（浓烟颗粒 `/particles/smoke`） |
| `SMOKE_STATE_NOT_COOKING` | 4 | 有燃料但无烤盘（淡烟颗粒 `/particles/smoke/mild`） |

> 有燃料且激活时图标 `forge_active` 并亮火色光（`set_light(3, 1, LIGHT_COLOR_FIRE)`）；熄灭时 `forge_inactive` 并关灯。

## 1.4 新工具行为钩子（tool_override.dm）

为 `/atom` 新增 5 种工具交互 proc（左右键各一）：`billow_act`（风箱）、`tong_act`（钳子）、`hammer_act`（锤子）、`blowrod_act`（吹管）、`shovel_act`（铲子），默认空实现，由各结构覆写。

---

# 卷二 · 锻炉 Forge

**源码**: `code/forge.dm`（`/obj/structure/reagent_forge`）

## 2.1 锻炉结构与燃料 (Forge Structure & Fuel)

| 项目 | 数值/说明 |
|---|---|
| 对象 | `/obj/structure/reagent_forge`，名称 "forge"（锻炉） |
| 描述 | 砖石结构，用于加热金属、玻璃、陶瓷或食物 |
| 材质 | 铁 ×10 片（`/datum/material/iron = SHEET_MATERIAL_AMOUNT * 10`） |
| 属性 | `anchored = TRUE`、`density = TRUE` |
| 燃料类型 | **弱燃料=木材**（wood，目标温度 50）；**强燃料=煤**（coal，目标温度 100，优先消耗） |
| 燃料单位 | 每添加 1 片木材/煤 = 燃料计时 +5 分钟（`forge_fuel_weak/strong += 5 MINUTES`） |
| 燃料上限 | 单一燃料类型最多 5 分钟（"forge is full on coal/wood"） |
| 燃料消耗 | 每 5 秒进程消耗 5 秒燃料（`forge_fuel_strong -= 5 SECONDS`） |
| 加燃料耗时 | `do_after` 3 秒，成功 +5 锻造经验 |
| 木炭生成 | 加木材有 45% 几率：1 分钟后在锻炉旁生成 1 块改名"charcoal"的煤 |
| 拆解 | 扳手 2 秒 → 掉落铁 ×10 片 |
| 固定/移动 | 副手攻击切换 `anchored`（"secured/unsecured"） |

**冶炼矿石**（`smelt_ore`）：温度 ≥50 时，消耗任意矿石堆，每 1 单位矿石 → 1 单位 `refined_type`（精炼产物）；耗时 `技能修正 × 3 秒`。无 `refined_type` 的矿石报 "cannot smelt"。

**冶炼铁棒**（`smelt_iron_rods`）：温度 ≥50；铁棒 ≥2 根时，`round(数量/2)` 根铁棒 → 1 片铁（奇数根自动少用 1 根）；耗时 `技能修正 × 3 秒`。

## 2.2 温度与烟雾机制 (Temperature & Smoke)

- **升温**：有燃料且 `forge_temperature < target_temperature` 时，每进程 +5（`FORGE_DEFAULT_TEMPERATURE_CHANGE`）。
- **降温**：`forge_temperature > target_temperature` 时，每进程 −(5 − `temperature_loss_reduction`)。
- **无燃料**：`target_temperature` 回落至 `minimum_target_temperature`（默认 0；JOURNEYMAN 及以上为 25，便于更快重新加热）。
- **进程循环**：每 5 秒（`forging_cooldown`）依次执行 `check_fuel()` → `check_temp()` → `check_in_use()`（若附近 1 格无活物且无烤盘则解除占用）→ 烘焙处理。
- **烟雾判定**：无烤盘+有燃料=`SMOKE_STATE_NOT_COOKING`；无燃料=`SMOKE_STATE_NONE`；有烤盘时按最差烘焙结果取 GOOD/NEUTRAL/BAD。

## 2.3 锻炉等级与升级 (Forge Levels & Upgrades)

锻炉等级跟随使用者**锻造技能（smithing）等级**自动升级（空手点击锻炉）。每次升级播放 `parry.ogg` 反馈音。各等级效果累加：

| 等级 | 温度流失减免 `temperature_loss_reduction` | 最低目标温度 `minimum_target_temperature` | 备注 |
|---|---|---|---|
| 无等级 SKILL_LEVEL_NONE | — | — | 无法升级（"you don't know how to use this forge"） |
| NOVICE（2） | 1 | 0 | |
| APPRENTICE（3） | 2 | 0 | |
| JOURNEYMAN（4） | 3 | 25 | 无燃料时降温到 25 即停，再加热更快 |
| EXPERT（5） | 4 | 25 | |
| MASTER（6） | 5（MAX） | 25 | |
| LEGENDARY（7） | 5（MAX） | 25 | **灰行者(ashwalker)/原始猫娘(primitive felinid) 升级至此自动获得试剂锻造能力**（`create_reagent_forge()`），获得金色镶边图标 `forge_masterwork_trim` |

**预制等级子类型**（地图/管理员用）：

| 子类型 | 等级 |
|---|---|
| `/obj/structure/reagent_forge/tier2` | NOVICE |
| `/obj/structure/reagent_forge/tier3` | APPRENTICE |
| `/obj/structure/reagent_forge/tier4` | JOURNEYMAN |
| `/obj/structure/reagent_forge/tier5` | EXPERT |
| `/obj/structure/reagent_forge/tier6` | MASTER |
| `/obj/structure/reagent_forge/tier7` | LEGENDARY |
| `/obj/structure/reagent_forge/tier7/imbuing` | LEGENDARY + 直接启用试剂锻造 |

## 2.4 锻炉交互全录 (Interactions)

| 交互 | 工具/物品 | 效果 |
|---|---|---|
| 空手点击 | — | 有烤盘→取出烤盘；否则→升级锻炉（按技能等级） |
| 添加烤盘 | 烤箱盘 `/obj/item/plate/oven_tray` | 放入锻炉（`in_use = TRUE`），发送 `COMSIG_ITEM_OVEN_PLACED_IN` |
| 加燃料 | 木材堆 | +5 分钟弱燃料，目标 50；45% 几率 1 分钟后产木炭 |
| 加燃料 | 煤堆 | +5 分钟强燃料，目标 100 |
| 冶炼 | 任意矿石堆 | 1 矿石 → 1 精炼片（温度 ≥50） |
| 冶炼 | 铁棒堆（≥2） | 每 2 根铁棒 → 1 片铁 |
| 试剂注入（武器） | 带 `reagent_weapon` 组件物品 | 见 2.5 |
| 试剂注入（衣物） | 带 `reagent_clothing` 组件物品 | 见 2.5 |
| 陶瓷定型 | 陶瓷件 `/obj/item/ceramic` | 温度 ≥50，`陶瓷.forge_item` 路径产物生成；+50 生产经验 |
| 玻璃熔化 | 玻璃片堆 | 1 片玻璃 → 1 团熔融玻璃（`/obj/item/glassblowing/molten_glass`），可加工时长 25s÷技能速度修正；+10 生产经验 |
| 玻璃熔化 | 装沙金属杯 `/obj/item/glassblowing/metal_cup` | 杯内有沙 → 熔融玻璃（杯变 `metal_cup_empty`）；+10 生产经验 |
| 风箱 | 风箱 `/obj/item/forging/billow` | 有燃料且温度<100 时：每次 `技能修正×工具速度` 秒 +10 温度，直到 91+；每次 +5 锻造经验；无燃料/满温报错 |
| 钳子加热 | 锻造钳（内含半成品） | 温度 ≥50；`heating_remainder` 冷却结束则重新加热 1 分钟；+5 锻造经验 |
| 钳子塑形 | 锻造钳（内含材料堆） | 弹出**径向选择菜单**（14 种半成品），消耗 1 片材料生成对应半成品，继承材料，加热 1 分钟；+5 锻造经验 |
| 吹管 | 玻璃吹管（内含熔融玻璃） | 重新加热熔融玻璃（时长 25s÷技能速度修正）；+5 锻造 +10 生产经验 |
| 扳手 | 扳手 | 2 秒拆解 → 铁 ×10 片 |

**径向塑形可选半成品全录（14 种，`choice_list`）**：

| 选项名 | 生成对象 |
|---|---|
| Chain | `/obj/item/forging/incomplete/chain` |
| Plate | `/obj/item/forging/incomplete/plate` |
| Sword | `/obj/item/forging/incomplete/sword` |
| Katana | `/obj/item/forging/incomplete/katana` |
| Dagger | `/obj/item/forging/incomplete/dagger` |
| Staff | `/obj/item/forging/incomplete/staff` |
| Spear | `/obj/item/forging/incomplete/spear` |
| Axe | `/obj/item/forging/incomplete/axe` |
| Hammer | `/obj/item/forging/incomplete/hammer` |
| Pickaxe | `/obj/item/forging/incomplete/pickaxe` |
| Shovel | `/obj/item/forging/incomplete/shovel` |
| Arrowhead | `/obj/item/forging/incomplete/arrowhead` |
| Rail Nail | `/obj/item/forging/incomplete/rail_nail` |
| Rail Cart | `/obj/item/forging/incomplete/rail_cart` |

## 2.5 试剂注入（附魔）机制 (Reagent Imbuing)

- **种族限定**：仅**灰行者（ashwalker）**与**原始猫娘（primitive felinid / "icecat"）**可注入（`is_species` 检查；人类/其他种族提示拒绝）。
- **流程**：对锻炉使用带注入组件的物品 → 10 秒 `do_after` → 遍历物品试剂列表：
  - 试剂量 < 100u：直接清除该试剂；
  - 试剂在禁用表：提示 "cannot imbue with [试剂名]" 并清除；
  - 通过：加入 `imbued_reagent` 列表，物品名前缀改为 "[试剂名] [原名]"，整体颜色变为试剂混合色（`mix_color_from_reagents`）。
- **奖励**：成功注入 +60 锻造经验，播放 `demon_consume.ogg`。
- **禁用试剂全录（17 种，`disallowed_reagents`）**：

| # | 试剂路径 | 中文 |
|---|---|---|
| 1 | `/datum/reagent/inverse/`（含全部子类型） | 逆试剂类 |
| 2 | `/datum/reagent/consumable/entpoly` | 迷幻剂 Entropic Polypnium |
| 3 | `/datum/reagent/pax` | 和平剂 Pax |
| 4 | `/datum/reagent/consumable/liquidelectricity/enriched` | 浓缩液电 |
| 5 | `/datum/reagent/teslium` | 特斯拉姆 |
| 6 | `/datum/reagent/eigenstate` | 特征态 |
| 7 | `/datum/reagent/drug/pcp` | PCP |
| 8 | `/datum/reagent/consumable/cum` | — |
| 9 | `/datum/reagent/consumable/femcum` | — |
| 10 | `/datum/reagent/consumable/breast_milk` | 母乳 |
| 11 | `/datum/reagent/toxin/acid` | 强酸 |
| 12 | `/datum/reagent/phlogiston` | 燃素 |
| 13 | `/datum/reagent/napalm` | 凝固汽油 |
| 14 | `/datum/reagent/thermite` | 铝热剂 |
| 15 | `/datum/reagent/medicine/earthsblood` | 大地之血 |
| 16 | `/datum/reagent/medicine/ephedrine` | 麻黄碱 |
| 17 | `/datum/reagent/medicine/epinephrine` | 肾上腺素 |

## 2.6 烘焙与窑烧 (Baking & Ceramics)

- **烘焙**：烤箱盘放入锻炉后（`used_tray`），每进程对盘内每个物品发送 `COMSIG_ITEM_OVEN_PROCESS`；未处理 `COMPONENT_HANDLED_BAKING` 的物品视为烤焦：`fire_act(1000)` 点燃，附近无嗅盲（TRAIT_ANOSMIA）者看到"闻到烧焦味"提示。烟雾状态取盘内最差结果（GOOD/NEUTRAL/BAD）。温度 <50 不烘焙。
- **陶瓷定型**：生陶瓷（`/obj/item/ceramic`，含 `forge_item` 路径）在温度 ≥50 的锻炉中定型，耗时 `生产技能修正 × 4 秒`，生成成品并继承颜色；+50 生产经验。
- **熔融玻璃**：玻璃片/装沙金属杯 → 熔融玻璃，可加工时长 = 25 秒 ÷ 生产技能速度修正（存于 `remaining_heat` 冷却与 `total_time`）。

---

# 卷三 · 铁砧 Anvil

**源码**: `code/anvil.dm`（`/obj/structure/reagent_anvil`）

## 3.1 铁砧结构与锤击流程 (Structure & Hammering)

| 项目 | 数值/说明 |
|---|---|
| 对象 | `/obj/structure/reagent_anvil`，名称 "smithing anvil"（锻造铁砧） |
| 描述 | 一大块金属，可在其上锤打其他金属，手工金属加工的必需品 |
| 材质 | 铁 ×10 片；`anchored = TRUE`、`density = TRUE` |
| 拆解 | 扳手 2 秒 → 铁 ×10 片 |
| 固定/移动 | 副手攻击切换 `anchored`（"secured/unsecured"） |
| 外观 | 铁砧上放有物品时显示叠加图层（缩放 0.8） |

**钳子交互（`tong_act`）**：铁砧与钳子之间来回转移物品——铁砧上有物品且钳子空 → 物品进钳子（钳图标 `tong_full`）；铁砧空且钳子内有物品 → 物品放上铁砧（钳图标 `tong_empty`）。

**锤击半成品（`hammer_act` → `do_hammer`）**：铁砧上存在 `incomplete` 半成品时，进入锤击循环：

1. 每次锤击耗时 = `技能速度修正(SKILL_SPEED_MODIFIER) × 半成品.average_wait × 锤子.toolspeed`；
2. 锤击成功：若半成品 `heating_remainder` 冷却已到期（金属凉了）→ `times_hit -= 3`（坏击惩罚），若 `times_hit ≤ -average_hits` 则**物品彻底报废**（qdel，"piece has cooled down too much"）；
3. 正常锤击：`times_hit++`，每次 +1 锻造经验；
4. 当 `times_hit ≥ average_hits`（默认 30 击）→ "finished" 完成提示。
5. 每次锤击播放 `forge.ogg`（音量 50）。

## 3.2 铁砧修复与坠落伤害 (Repair & Falling Hazard)

- **铁砧修复**：铁砧上放有带 `ANVIL_REPAIR` 旗标的物品且耐久不满时，锤击可修复——每 1 秒恢复 **+10 耐久**（`repair_damage`），每次 +5 锻造经验，播放 `forge.ogg`；耐久已满提示 "doesn't need repairs"。
- **坠落危险组件**：铁砧 `AddElement(/datum/element/falling_hazard, damage = 40, wound_bonus = 10, hardhat_safety = FALSE, crushes = TRUE)`。
- **砸中人（`onZImpact`）**：
  - 直接伤害 `60 × 坠落层数`（强制伤害）；
  - 碳基生物（carbon）：两次肢体伤害各 `40 × 层数`、伤口加成 `5 × 层数`；
  - 附加 `squish`（压扁）30 秒、`Paralyze`（麻痹）5 秒、强制尖叫（emote "scream"）、播放 `fellowship_armory.ogg`；
  - 7 格内目击者获得「目击铁砧压人」记忆（`/datum/memory/witness_vendor_crush`）。

---

# 卷四 · 铸造台与配方全录

**源码**: `code/crafting_bench.dm`（391 行）+ `code/crafting_bench_recipes.dm`（166 行）

## 4.1 铸造台机制 (Crafting Bench Mechanics)

| 项目 | 数值/说明 |
|---|---|
| 对象 | `/obj/structure/reagent_crafting_bench`，名称 "forging workbench"（锻造工作台） |
| 描述 | 配备工具、固定机构与平稳台面的锻造工作台 |
| 材质 | 木 ×5 片；`anchored = TRUE`、`density = TRUE` |
| 拆解 | 扳手 2 秒 → 木 ×5 片 |
| 完成武器开关 | `finishes_forging_weapons = TRUE`（可关闭以做非锻造台） |
| 原料检索范围 | 台子周围 **1 格**（`range(1, src)`，跳过全息影像）内的物品/堆 |
| 径向菜单 | 空手点击 → 18 种配方选择（半径 38，带 tooltips） |
| 配方取消 | 台上有配方时空手点击 → 清除配方与锤击进度 |

**锤击配方流程（`hammer_act` → `do_hammer`）**：

1. 台上有 `/obj/item/forging/complete`（武器头）→ **武器完成**：检查环境内木材 ≥2（`WEAPON_COMPLETION_WOOD_AMOUNT`），消耗后生成 `spawning_item` 对应成品，武器头自定义材料一并转移；+30 锻造经验；播放 `forge.ogg`。
2. 已选配方 → 检查环境内配方原料是否齐全 → 循环锤击：每次 `技能速度修正 × 1 秒`，直到命中数 ≥ `required_good_hits`（每次命中 + `relevant_skill_reward / 15` 经验）→ 消耗原料、生成成品。
3. 原料为堆时按需消耗数量；`transfers_materials = TRUE` 时把组件（含武器头/锻件）的自定义材料转移给成品。
4. 若成品生成失败会向管理员发消息（不应发生）。

**检查环境（`get_environment`）**：收集台子 1 格范围内的所有可移动物（`/atom/movable`，排除 `HOLOGRAM_1` 旗标）。

## 4.2 配方全录（19 配方） (All Recipes)

配方基类 `/datum/crafting_bench_recipe`：默认 `required_good_hits = 6`、`relevant_skill = smithing`（锻造）、`relevant_skill_reward = 30`、`transfers_materials = TRUE`。

| # | 配方路径 | 配方名 | 原料（数量） | 成品 | 所需好击数 |
|---|---|---|---|---|---|
| 01 | `weapon_completion_recipe` | weapon completion（武器完成，隐藏） | 木 ×2 | 取决于武器头 `spawning_item` | 依武器头 |
| 02 | `plate_helmet` | **plate helmet** 板甲头盔 | 板 ×4 | `/obj/item/clothing/head/helmet/forging_plate_helmet` | 8 |
| 03 | `plate_vest` | **plate vest** 板甲背心 | 板 ×6 | `/obj/item/clothing/suit/armor/forging_plate_armor` | 12 |
| 04 | `chain_shirt` | **chain shirt** 锁子衫 | 链 ×6 | `/obj/item/clothing/suit/armor/forging_chain_shirt` | 12 |
| 05 | `plate_gloves` | **plate gloves** 板甲手套 | 板 ×2 | `/obj/item/clothing/gloves/forging_plate_gloves` | 4 |
| 06 | `plate_boots` | **plate boots** 板甲靴 | 板 ×4 | `/obj/item/clothing/shoes/forging_plate_boots` | 8 |
| 07 | `ring` | **ring** 戒指 | 链 ×2 | `/obj/item/clothing/gloves/ring/reagent_clothing` | 4 |
| 08 | `collar` | **collar** 项圈 | 链 ×3 | `/obj/item/clothing/neck/collar/reagent_clothing` | 6 |
| 09 | `handcuffs` | **handcuffs** 手铐 | 链 ×5 | `/obj/item/restraints/handcuffs/reagent_clothing` | 10 |
| 10 | `borer_cage` | **cortical borer cage** 皮层钻虫笼 | 板 ×6 | `/obj/item/cortical_cage` | 12 |
| 11 | `pavise` | **pavise** 巨盾（帕维斯） | 板 ×8 | `/obj/item/shield/buckler/reagent_weapon/pavise` | 16 |
| 12 | `buckler` | **buckler** 小圆盾 | 板 ×5 | `/obj/item/shield/buckler/reagent_weapon` | 10 |
| 13 | `coil` | **coil** 线圈 | 链 ×2 | `/obj/item/forging/coil` | 4 |
| 14 | `seed_mesh` | **seed mesh** 种子网 | 板 ×1 + 链 ×2 | `/obj/item/seed_mesh` | 10 |
| 15 | `centrifuge` | **centrifuge** 离心机 | 板 ×1 | `/obj/item/reagent_containers/cup/primitive_centrifuge` | 4 |
| 16 | `soup_pot` | **soup pot** 汤锅 | 板 ×4 | `/obj/item/reagent_containers/cup/soup_pot` | 10 |
| 17 | `bokken` | **bokken** 木刀 | 木 ×4 | `/obj/item/forging/reagent_weapon/bokken` | 8 |
| 18 | `bow` | **bow** 弓（未上弦） | 木 ×4 | `/obj/item/forging/incomplete_bow` | 8 |
| 19 | `fishing_rod` | **fishing rod** 钓鱼竿 | 杖头(complete/staff) ×1 | `/obj/item/fishing_rod/material` | 8 |

---

# 卷五 · 锻造工具与中间产物

**源码**: `code/forge_items.dm`（347 行）

## 5.1 允许锻造材料全录（20 种） (Allowed Forging Materials)

全局列表 `GLOB.allowed_forging_materials`——只有这些材料的材料堆才能被钳子夹起并塑形：

| # | 材料 | 中文 |
|---|---|---|
| 1 | `/datum/material/iron` | 铁 |
| 2 | `/datum/material/silver` | 银 |
| 3 | `/datum/material/gold` | 金 |
| 4 | `/datum/material/uranium` | 铀 |
| 5 | `/datum/material/bananium` | 香蕉合金 |
| 6 | `/datum/material/titanium` | 钛 |
| 7 | `/datum/material/runite` | 符文铁（Runite） |
| 8 | `/datum/material/adamantine` | 精金（Adamantine） |
| 9 | `/datum/material/mythril` | 秘银（Mythril） |
| 10 | `/datum/material/metalhydrogen` | 金属氢 |
| 11 | `/datum/material/runedmetal` | 符文金属 |
| 12 | `/datum/material/bronze` | 青铜 |
| 13 | `/datum/material/hauntium` | 幽魂金属（Hauntium） |
| 14 | `/datum/material/alloy/plasteel` | 塑钢（合金） |
| 15 | `/datum/material/alloy/plastitanium` | 塑钛（合金） |
| 16 | `/datum/material/alloy/alien` | 外星合金 |
| 17 | `/datum/material/cobolterium` | 钴钴合金（Cobolterium） |
| 18 | `/datum/material/copporcitite` | 铜锡矿合金（Copporcitite） |
| 19 | `/datum/material/tinumium` | 钛钛合金（Tinumium） |
| 20 | `/datum/material/brussite` | 铅锰合金（Brussite） |

## 5.2 锻造工具 (Tools)

| 工具 | 名称 | 说明 | 属性 |
|---|---|---|---|
| `/obj/item/forging/tongs` | forging tongs 锻造钳 | "I lift things up and put them down."（举起来放下去） | `TOOL_TONG`；空手点击丢弃夹持物（图标 `tong_empty`） |
| `/obj/item/forging/tongs/primitive` | primitive forging tongs 原始锻造钳 | 简陋版 | `toolspeed = 2`，铁 ×5 |
| `/obj/item/forging/hammer` | forging mallet 锻造木槌 | 缓慢塑形金属 | `TOOL_HAMMER`；对铁砧/铸造台攻击为**快速攻击**（`CLICK_CD_RAPID`） |
| `/obj/item/forging/hammer/primitive` | primitive forging hammer 原始锻造锤 | 简陋版 | 铁 ×5 |
| `/obj/item/forging/billow` | forging billow 锻造风箱 | 鼓风助燃 | `TOOL_BILLOW` |
| `/obj/item/forging/billow/primitive` | primitive forging billow 原始风箱 | 简陋版 | `toolspeed = 2`，木 ×5 |

## 5.3 半成品（incomplete）全录（14 种）

基类 `/obj/item/forging/incomplete`：默认 `average_hits = 30` 击、`average_wait = 1 秒`/击、带 `heating_remainder`（加热余量）与 `striking_cooldown` 冷却；材质旗标 `MATERIAL_EFFECTS | MATERIAL_ADD_PREFIX | MATERIAL_COLOR`（"谁不想要等离子剑呢"）。

| # | 对象 | 名称 | 图标 | 需击数 | 击间隔 | 完成产物 `spawn_item` |
|---|---|---|---|---|---|---|
| 1 | `incomplete/chain` | incomplete chain 未完成链条 | hot_chain | 10 | 0.5s | `/obj/item/forging/complete/chain` |
| 2 | `incomplete/plate` | incomplete plate 未完成板 | hot_plate | 10 | 0.5s | `/obj/item/forging/complete/plate` |
| 3 | `incomplete/sword` | incomplete sword blade 未完成剑刃 | hot_blade | 30 | 1s | `/obj/item/forging/complete/sword` |
| 4 | `incomplete/katana` | incomplete katana blade 未完成刀身 | hot_katanablade | 30 | 1s | `/obj/item/forging/complete/katana` |
| 5 | `incomplete/dagger` | incomplete dagger blade 未完成匕首刃 | hot_daggerblade | 30 | 1s | `/obj/item/forging/complete/dagger` |
| 6 | `incomplete/staff` | incomplete staff head 未完成杖头 | hot_staffhead | 30 | 1s | `/obj/item/forging/complete/staff` |
| 7 | `incomplete/spear` | incomplete spear head 未完成矛头 | hot_spearhead | 30 | 1s | `/obj/item/forging/complete/spear` |
| 8 | `incomplete/axe` | incomplete axe head 未完成斧头 | hot_axehead | 30 | 1s | `/obj/item/forging/complete/axe` |
| 9 | `incomplete/hammer` | incomplete hammer head 未完成锤头 | hot_hammerhead | 30 | 1s | `/obj/item/forging/complete/hammer` |
| 10 | `incomplete/pickaxe` | incomplete pickaxe head 未完成镐头 | hot_pickaxehead | 30 | 1s | `/obj/item/forging/complete/pickaxe` |
| 11 | `incomplete/shovel` | incomplete shovel head 未完成铲头 | hot_shovelhead | 30 | 1s | `/obj/item/forging/complete/shovel` |
| 12 | `incomplete/arrowhead` | incomplete arrowhead 未完成箭头 | hot_arrowhead | 12 | 0.5s | `/obj/item/forging/complete/arrowhead` |
| 13 | `incomplete/rail_nail` | incomplete rail nail 未完成铁轨钉 | hot_nail | 10 | 0.5s | `/obj/item/forging/complete/rail_nail` |
| 14 | `incomplete/rail_cart` | incomplete rail cart 未完成轨道矿车 | hot_cart | 30 | 1s | `/obj/vehicle/ridden/rail_cart`（直接成车） |

## 5.4 成品（complete）全录（13 种）

基类 `/obj/item/forging/complete`：带 `spawning_item`（完成武器所需物品路径），材质旗标同上；检查时提示"可拿到铸造台上装柄"。

| # | 对象 | 名称 | 图标 | 武器/产物路径 `spawning_item` |
|---|---|---|---|---|
| 1 | `complete/chain` | chain 链条 | chain | —（组装原料） |
| 2 | `complete/plate` | plate 板 | plate | —（组装原料） |
| 3 | `complete/sword` | sword blade 剑刃 | blade | `/obj/item/forging/reagent_weapon/sword` |
| 4 | `complete/katana` | katana blade 刀身 | katanablade | `/obj/item/forging/reagent_weapon/katana` |
| 5 | `complete/dagger` | dagger blade 匕首刃 | daggerblade | `/obj/item/forging/reagent_weapon/dagger` |
| 6 | `complete/staff` | staff head 杖头 | staffhead | `/obj/item/forging/reagent_weapon/staff` |
| 7 | `complete/spear` | spear head 矛头 | spearhead | `/obj/item/forging/reagent_weapon/spear` |
| 8 | `complete/axe` | axe head 斧头 | axehead | `/obj/item/forging/reagent_weapon/axe` |
| 9 | `complete/hammer` | hammer head 锤头 | hammerhead | `/obj/item/forging/reagent_weapon/hammer` |
| 10 | `complete/pickaxe` | pickaxe head 镐头 | pickaxehead | `/obj/item/pickaxe/reagent_weapon` |
| 11 | `complete/shovel` | shovel head 铲头 | shovelhead | `/obj/item/shovel/reagent_weapon` |
| 12 | `complete/arrowhead` | arrowhead 箭头 | arrowhead | `/obj/item/arrow_spawner`（生成 4 支箭后自毁） |
| 13 | `complete/rail_nail` | rail nail 铁轨钉 | nail | `/obj/item/stack/rail_track/ten`（铁轨 ×10） |

## 5.5 其他锻造物品 (Misc Forge Items)

| 物品 | 名称 | 说明 |
|---|---|---|
| `/obj/item/forging/coil` | coil 线圈 | "由卷绕铁棒制成的简单线圈"；用于改造电池（见下） |
| `/obj/item/forging/incomplete_bow` | incomplete longbow 未上弦长弓 | 与丝弦 `/obj/item/weaponcrafting/silkstring` 合成 → `/obj/item/gun/ballistic/bow/longbow` 长弓 |
| `/obj/item/arrow_spawner` | arrow spawner 箭头生成器 | 生成 4 支箭（`/obj/item/ammo_casing/arrow`）后自毁 |
| 电池改造 | — | 任意电池 + 线圈 → 手摇电池 `crank` 电池（保留原 `maxcharge`/`charge`） |
| 材料堆钳取 | — | `tong_act` 覆写：仅 `allowed_forging_materials` 内材料可被钳子夹起 |
| 任意物体钳取 | — | `obj/tong_act` 覆写：带 `ANVIL_REPAIR` 旗标的物品可被钳子夹起（可搬去铁砧修复） |
| 精金片修正 | — | `/obj/item/stack/sheet/mineral/adamantine` 补设 `material_type = /datum/material/adamantine` |

---

# 卷六 · 锻造成品武器全录

**源码**: `code/forge_weapons.dm`（387 行）

## 6.1 基础属性与通用组件 (Base & Shared Components)

- **基类** `/obj/item/forging/reagent_weapon`：`toolspeed = 0.9`、投掷力 5、投掷速度 3、投掷距离 7、`FIRE_PROOF | LAVA_PROOF`、`UNIQUE_RENAME | CONDUCTS_ELECTRICITY`、`ANVIL_REPAIR`、材质旗标（前缀/颜色/效果）。
- **通用组件**（Initialize）：`/datum/component/reagent_weapon`（试剂注入）+ `/datum/component/bane`（对 `MOB_MINING | MOB_BEAST` 生物伤害 ×2，即采矿与野兽类）。
- **通用检查文本**：提示"可注入试剂"与"对野兽更致命"。

## 6.2 武器全录（11 种） (Weapons)

| # | 对象 | 名称 | 伤害 force | 穿甲 AP | 格挡 | 关键属性 |
|---|---|---|---|---|---|---|
| 1 | `reagent_weapon/sword` | forged sword 锻造剑 | 20 | 20 | 20% | 单手剑，`SHARP_EDGED`，耐久上限 150，腰带/背部槽，笨重；屠宰组件 4 秒/105 效率 |
| 2 | `reagent_weapon/katana` | forged katana 锻造刀 | 20 | 40 | 10% | 高穿甲，`SHARP_EDGED`，拔刀音 `unsheath.ogg`，腰带/背部槽，笨重 |
| 3 | `reagent_weapon/dagger` | forged dagger 锻造匕首 | 13 | — | — | 投掷力 17、投掷速度 4、拆解修正 0.75；`TOOL_KNIFE`（可作刀用）；嵌入：几率 50%、掉落 10%、痛感 ×4、无视投掷速度阈值；屠宰 8 秒/100 效率 + 力量+7 加成；小巧 |
| 4 | `reagent_weapon/staff` | forged staff 锻造杖 | 0 | — | — | 伤害为 0 的"无害"杖，专为试剂注入设计；含骑枪冲锋组件（jousting）；投掷力 0；正常体型 |
| 5 | `reagent_weapon/spear` | forged spear 锻造矛 | 13 | 15 | — | 双手 13/23；投掷力 24、投掷速度 4；伤口加成 −15、暴露伤口 +15；**攻击距离 2**；`SHARP_POINTY`；拆解修正 0.75；骑枪冲锋；屠宰 10 秒/70 |
| 6 | `reagent_weapon/axe` | forged axe 锻造斧 | 15 | 10 | — | 专为投掷平衡：投掷力 20、投掷速度 4；嵌入：几率 40%、掉落 10%、痛感 ×2；屠宰 7 秒/100；`SHARP_EDGED` |
| 7 | `reagent_weapon/hammer` | forged hammer 锻造锤 | 10 | 10 | — | 双手 10/25（**强制双手**）；投掷力 10、速度 2、距离 5；`TOOL_HAMMER`；膝跳反应组件（kneejerk）；命中击退：非锚定目标被抛飞 2 格（和平主义/未双手持握时无效） |
| 8 | `reagent_weapon/bokken` | bokken 木刀 | 15 | 40 | 20%（双手 40%） | 伤害类型 **STAMINA（耐力）**，避免永久伤害；双手 15/25；`FLAMMABLE`；骑枪冲锋；格挡音 `parry.ogg`；攻击命中碳基目标播放随机 `genhit` 音 |
| 9 | `pickaxe/reagent_weapon` | forged pickaxe 锻造镐 | — | — | — | `toolspeed = 0.75`（采矿工具速度）+ 试剂武器/克兽组件 |
| 10 | `shovel/reagent_weapon` | forged shovel 锻造铲 | — | — | — | `toolspeed = 0.60` + 试剂武器/克兽组件 |
| 11 | `forging/complete/pickaxe` 镐头等 | — | — | — | — | 见卷五（半成品/成品链路） |

**锻造匕首嵌入数据 `/datum/embedding/forged_dagger`**：嵌入几率 50%、掉落几率 10%、痛感倍数 4、`ignore_throwspeed_threshold = TRUE`。
**锻造斧嵌入数据 `/datum/embedding/forged_axe`**：嵌入几率 40%、掉落几率 10%、痛感倍数 2。

## 6.3 盾牌（2 种） (Shields)

| # | 对象 | 名称 | 格挡 | 关键属性 |
|---|---|---|---|---|
| 1 | `/obj/item/shield/buckler/reagent_weapon` | forged buckler shield 锻造小圆盾 | 30% | 铁 0.5 片；耐久上限 150；`MATERIAL_AFFECT_STATISTICS`；`FIRE_PROOF | LAVA_PROOF`；`ANVIL_REPAIR`；**破裂残留 = 板 ×1**（`shield_break_leftover`），可回炉；破裂音 `bang.ogg`；可用锻造锤直接敲击修复（每击 +5 耐久、+5 锻造经验）；试剂武器/克兽组件 |
| 2 | `/obj/item/shield/buckler/reagent_weapon/pavise` | forged pavise shield 锻造巨盾 | 45%（双手 65%） | 手持减速（`SLOWS_WHILE_IN_HAND`）；伤害 12（双手 15）；耐久上限 300；巨体型；背部槽；双手组件（wield/unwield 切换格挡率） |

## 6.4 工具武器与箭头转换 (Tool Weapons & Arrows)

- **箭头转换**（`ammo_casing/arrow` 交互，消耗 1 单位原料）：

| 原料 | 产物 |
|---|---|
| 筋 `/obj/item/stack/sheet/sinew` | `/obj/item/ammo_casing/arrow/ash` 灰烬箭 |
| 骨 `/obj/item/stack/sheet/bone` | `/obj/item/ammo_casing/arrow/bone` 骨箭 |
| 青铜砖 `/obj/item/stack/tile/bronze` | `/obj/item/ammo_casing/arrow/bronze` 青铜箭 |

---

# 卷七 · 锻造成品护甲与服装全录

**源码**: `code/forge_clothing.dm`（200 行）

## 7.1 护甲数据模板（4 个） (Armor Datums)

| 模板 | 近战 melee | 子弹 bullet | 激光 laser | 能量 energy | 炸弹 bomb | 火焰 fire | 酸 acid | 生物 bio | 伤口 wound |
|---|---|---|---|---|---|---|---|---|---|
| `/datum/armor/armor_forging_hard`（硬） | 40 | 30 | — | 10 | 40 | 50 | 20 | — | 20 |
| `/datum/armor/armor_forging_medium`（中） | 30 | 20 | 10 | 20 | 70 | 50 | 40 | — | 20 |
| `/datum/armor/armor_forging_light`（轻） | 25 | 15 | 15 | 15 | 35 | — | 20 | 20 | 15 |
| `/datum/armor/armor_forging_upgrade`（升级加成） | +5 | +5 | +5 | +5 | +5 | — | — | — | +5 |

> 升级加成通过 `/datum/component/armor_plate`（甲片）叠加：背心/锁子衫/头盔各 2 片，手套/靴各 1 片。

## 7.2 服装全录（8 件） (Clothing)

| # | 对象 | 名称 | 槽位/覆盖 | 护甲 | 组件与特性 |
|---|---|---|---|---|---|
| 1 | `suit/armor/forging_plate_armor` | plate vest 板甲背心 | 外衣槽，GROIN\|CHEST | 硬 40/30/10/40/50/20/20 | 甲片 ×2、`reagent_clothing`(OCLOTHING)、钓鱼难度 +4、`FIRE_PROOF`、`ANVIL_REPAIR`；可装锻造武器与动能碎骨器（kinetic_crusher） |
| 2 | `suit/armor/forging_chain_shirt` | chain mail 锁子甲 | 外衣槽，GROIN\|CHEST | 中 30/20/10/20/70/50/40/20 | 甲片 ×2、`reagent_clothing`(OCLOTHING)、`FIRE_PROOF`、`ANVIL_REPAIR`；可装锻造武器与动能碎骨器 |
| 3 | `gloves/forging_plate_gloves` | plate gloves 板甲手套 | 手套槽，HANDS\|ARMS | 轻 25/15/15/15/35/20/20/15 | 甲片 ×1、`reagent_clothing`(GLOVES)、钓鱼难度 +4、`FIRE_PROOF`、`ANVIL_REPAIR` |
| 4 | `head/helmet/forging_plate_helmet` | plate helmet 板甲头盔 | 头盔槽 | 中 30/20/10/20/70/50/40/20 | 甲片 ×2、`reagent_clothing`(HEAD)、`FIRE_PROOF`、`ANVIL_REPAIR`、`flags_inv = null`（不遮脸）、有吻部变体 |
| 5 | `shoes/forging_plate_boots` | plate boots 板甲靴 | 鞋槽，FEET\|LEGS | 轻 25/15/15/15/35/20/20/15 | 甲片 ×1、`reagent_clothing`(FEET)、钓鱼难度 +2、`FIRE_PROOF`、`ANVIL_REPAIR`、一脚蹬（slipon）、趾行变体 |
| 6 | `gloves/ring/reagent_clothing` | ring 戒指 | 手套槽（戒指位） | — | `reagent_clothing`(GLOVES)、钓鱼难度 **−4**（钓鱼加成）、`ANVIL_REPAIR`、材质前缀/颜色 |
| 7 | `neck/collar/reagent_clothing` | collar 项圈 | 颈部槽 NECK | — | `reagent_clothing`(NECK)、`ANVIL_REPAIR`、小体型、材质前缀/颜色 |
| 8 | `restraints/handcuffs/reagent_clothing` | handcuffs 手铐 | 束缚槽 HANDCUFFED | — | `reagent_clothing`(HANDCUFFED)、`ANVIL_REPAIR`、材质前缀/颜色 |

---

# 卷八 · 试剂注入组件系统

**源码**: `code/reagent_component.dm`（117 行）

## 8.1 常量与衣物组件 reagent_clothing

- **常量**：`MAX_IMBUE_STORAGE = 250u`（物品试剂容量）；`REAGENT_CLOTHING_INJECT_AMOUNT = 0.5u`/次。
- **组件** `/datum/component/reagent_clothing`（仅用于衣物）：构造时 `create_reagents(250, INJECTABLE | REFILLABLE)`，创建内部注射容器。
- **佩戴检测**：注册装备/卸下信号跟踪穿戴者；每 **3 秒**（`imbue_cooldown`）检查一次——若物品正穿在正确槽位（构造时传入 `checking_slot`：OCLOTHING/GLOVES/HEAD/FEET/NECK/HANDCUFFED 等）且已注入试剂，则对穿戴者**注射 0.5u × 每种注入试剂**（`trans_to`，方法 INJECT）。
- **检查文本**："可注入试剂，佩戴时向穿戴者缓慢注射"。

## 8.2 武器组件 reagent_weapon

- **常量**：`REAGENT_WEAPON_INJECT_AMOUNT = 1u`/命中；`REAGENT_WEAPON_DAMAGE_MULTIPLIER = 2`。
- **组件** `/datum/component/reagent_weapon`（仅用于武器）：`create_reagents(250, INJECTABLE | REFILLABLE)`。
- **命中注射**：注册 `COMSIG_ITEM_ATTACK`；每次攻击命中活体目标，对目标直接添加 **1u × 每种注入试剂**（`add_reagent`）。
- **耐久损耗**：每次命中后武器承受 `注入试剂种类数 × 2` 耐久伤害——注入 1 种约可命中 100 次、2 种约 50 次后损坏（耐久可上铁砧/用锤修复）。

---

# 卷九 · 水槽 Water Basin

**源码**: `code/water_basin.dm`（111 行）· 对象 `/obj/structure/reagent_water_basin`（water basin 水槽）

| 项目 | 数值/说明 |
|---|---|
| 材质 | 木 ×5 片；`anchored = TRUE`、`density = TRUE` |
| 描述 | 装满水的水槽，用于淬火热金属 |
| 拆解 | 扳手 2 秒 → 木 ×5 片 |

**淬火（`tong_act`）**：用钳子（内含半成品）点击水槽：

- 半成品 `times_hit < average_hits`（还没锤完）→ 报"它还不够热/没锤好"，**重置加热余量**（`COOLDOWN_RESET heating_remainder`，必须回炉重热）；
- 半成品 `times_hit ≥ average_hits`（锤完）→ 播放 `hot_hiss.ogg` 淬火声，生成 `spawn_item` 成品（继承自定义材料），+10 锻造经验，钳子清空。

**制作黏土**：对水槽使用玻璃矿石 `/obj/item/stack/ore/glass`（1 单位）→ 生成 1 块黏土（`/obj/item/stack/clay`），+1 生产经验。

**钓鱼点（fishing spot）**：两种方式启用：

1. 空手点击：锻造技能 ≥ **JOURNEYMAN（熟练级）**时自动添加钓鱼组件（`/datum/component/fishing_spot` + `/datum/fish_source/water_basin`）；
2. 使用蓝空间水晶 `/obj/item/stack/ore/bluespace_crystal` 1 单位 → 直接启用钓鱼点。

**鱼获表 `/datum/fish_source/water_basin`**（"无底水槽"）：小丑鱼 15、河豚 10、红衣主教鱼 15、绿蓝魔 15、灯笼鱼 5、矮月鱼 15、炮手水母 15、针鱼 10、铠甲鱼 10、随机维护物品 10、随机垃圾 15。

---

# 卷十 · 锻造技能与前置配方

**源码**: `code/smith_skill.dm`（18 行）+ `code/forge_recipes.dm`（37 行）

## 10.1 锻造技能 (Smithing Skill)

| 项目 | 数值/说明 |
|---|---|
| 技能 | `/datum/skill/smithing`，名称 "Smithing"（锻造），称号 "Smithy"（铁匠） |
| 描述 | "追随锻炉火焰的绝望艺术家" |
| 速度修正 `SKILL_SPEED_MODIFIER` | [1, 0.95, 0.9, 0.85, 0.75, 0.6, 0.5]（7 个等级：无级→传说） |
| 概率修正 `SKILL_PROBS_MODIFIER` | [10, 15, 20, 25, 30, 35, 40] |
| 技能奖励物品 | `/obj/item/clothing/neck/cloak/skill_reward/smithing` |

**传说铁匠披风**（legendary smithy's cloak）："只有熟谙铁匠锻炉每一寸的顶级铁匠才能获得"，图标 `smithingcloak`；达成传说级锻造技能后获得。

**经验获取途径汇总**：加燃料 +5 / 风箱鼓风 +5 / 锻炉加热半成品 +5 / 铁砧锤击 +1 每次 / 铁砧修复 +5 每次 / 水槽淬火 +10 / 铸造台配方锤击（奖励/15 每次）+ 完成 +30 / 武器注入 +60 / 冶炼 +0（无）。

## 10.2 前置手工配方 (Primitive Crafting Recipes)

| 配方 | 名称 | 原料 | 产物 | 分类 |
|---|---|---|---|---|
| `/datum/crafting_recipe/primitive_billow` | Primitive Forging Billow 原始风箱 | 木 ×5 | `/obj/item/forging/billow/primitive` | CAT_TOOLS 工具 |
| `/datum/crafting_recipe/primitive_tong` | Primitive Forging Tong 原始钳 | 铁 ×5 | `/obj/item/forging/tongs/primitive` | CAT_TOOLS 工具 |
| `/datum/crafting_recipe/primitive_hammer` | Primitive Forging Hammer 原始锤 | 铁 ×5 | `/obj/item/forging/hammer/primitive` | CAT_TOOLS 工具 |

## 10.3 补给箱 (Cargo Supply Pack)

**"Forging Starter Item Pack" 锻造入门补给箱**（`/datum/supply_pack/service/forging_items`）：

- 价格：`CARGO_CRATE_VALUE × 2.5`（2.5 倍标准货箱价）；
- 内容全录：锻造钳 ×1、锻造锤 ×1、锻造风箱 ×1、铁片 ×20、煤 ×5、木材 ×30；
- 货箱：`/obj/structure/closet/crate/forging_items`（"forging starter items"）。

---

# 附录 A · 代码路径索引

| 文件 | 行数 | 内容 |
|---|---|---|
| `code/forge.dm` | 1,014 | 锻炉结构、燃料/温度/烟雾、等级升级、全部交互（冶炼/注入/陶瓷/玻璃/风箱/钳/吹管）、烘焙、tier2-7 子类型 |
| `code/anvil.dm` | 164 | 铁砧结构、锤击半成品、铁砧修复、坠落伤害 |
| `code/crafting_bench.dm` | 391 | 铸造台结构、径向配方菜单、锤击流程、原料环境检索、材料转移 |
| `code/crafting_bench_recipes.dm` | 166 | 配方基类 + 19 配方全录 |
| `code/forge_weapons.dm` | 387 | 锻造武器/盾牌/木刀/镐铲/箭头转换（11 武器 + 2 盾） |
| `code/forge_items.dm` | 347 | 允许材料列表（20）、工具、半成品（14）、成品（13）、线圈/弓/箭头生成器 |
| `code/forge_clothing.dm` | 200 | 护甲数据（4 模板）+ 服装 8 件 |
| `code/reagent_component.dm` | 117 | 注入组件（衣物 0.5u/3s、武器 1u/命中 + 耐久损耗） |
| `code/water_basin.dm` | 111 | 水槽、淬火、黏土、钓鱼点与鱼获表 |
| `code/forge_recipes.dm` | 37 | 原始工具配方（3）+ 补给箱 |
| `code/tool_override.dm` | 37 | billow/tong/hammer/blowrod/shovel 工具行为钩子 |
| `code/smith_skill.dm` | 18 | 锻造技能定义 + 传说铁匠披风 |
| `readme.md` | 28 | 模块说明（MODULE ID、作者 Jake Park） |

> 全模块 **2,989 行** = 上述 12 个 .dm 合计（不含 icons/sound）。
