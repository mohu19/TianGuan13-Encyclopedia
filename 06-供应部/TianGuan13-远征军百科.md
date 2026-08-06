# TianGuan13 远征军百科

> 模块 ID: `EXP_CORPS`。已解散的先锋远征军团（Vanguard Expeditionary Corps）遗留服装与装备。
> 文件: `code/clothing.dm`（280 行）+ `code/expeditionary_trooper.dm`（32 行）+ `code/gear.dm`（231 行）+ `code/tomahawk.dm`（33 行），共 576 行。
> 图标: `icons/backpack.dmi`、`mob_backpack.dmi`、`bonesaw.dmi`/`bonesaw_l.dmi`/`bonesaw_r.dmi`、`exp_crate.dmi`、`closet.dmi`、`riot.dmi`/`riot_left.dmi`/`riot_right.dmi`、`survival_pack.dmi`、`throwing.dmi`、`tomahawk.dmi`/`tomahawk_l.dmi`/`tomahawk_r.dmi`/`tomahawk_worn.dmi`（部分服装图标位于 `modular_nova/master_files/icons/`）。

## 1.1 模块总览

| 文件 | 行数 | 内容 |
|---|---|---|
| `code/clothing.dm` | 280 | 制服/胸挂/靴子/手套/背包/防弹背心/夜视头盔 |
| `code/gear.dm` | 231 | 医疗包/骨锯/尖兵盾/飞刀/刀囊 |
| `code/tomahawk.dm` | 33 | 远征战斧 |
| `code/expeditionary_trooper.dm` | 32 | 军用 PDA/远征生存包/神射手箱子 |

## 1.2 服装全录（clothing.dm）

### 1.2.1 远征军制服（`/obj/item/clothing/under/rank/expeditionary_corps`）

- 名称: "expeditionary corps uniform"（远征军制服）；描述: "A rugged uniform for those who see the worst at the edges of the galaxy."（为那些在银河边缘见证最糟糕境况的人准备的耐用制服）。
- `icon_state = "exp_corps"`；图标位于 `modular_nova/master_files/icons/obj/clothing/uniforms.dmi` 与 `.../mob/clothing/uniform.dmi`。
- 护甲 `/datum/armor/clothing_under/rank_expeditionary_corps`: **fire 15 / acid 15**。
- `strip_delay = 7 SECONDS`；`alt_covers_chest = TRUE`；`sensor_mode = SENSOR_COORDS`（默认坐标传感器）；`random_sensor = FALSE`。

### 1.2.2 胸挂（`/obj/item/storage/belt/military/expeditionary_corps`）

- 名称: "expeditionary corps chest rig"（远征军胸挂）；描述: "A set of tactical webbing worn by the now-defunct Vanguard Expeditionary Corps."（已解散的先锋远征军团穿过的战术织带）。
- `icon_state / worn_icon_state = "webbing_exp_corps"`。
- **可换肤（reskin）**: `setup_reskins()` 添加 `/datum/component/reskinable_item` + `/datum/atom_skin/expeditionary_corps_chest_rig`:
  - `webbing`: 预览名 "Webbing"，`new_icon_state = "webbing_exp_corps"`；
  - `belt`: 预览名 "Belt"，`new_icon_state = "belt_exp_corps"`。

**四个职业变体及默认内容（PopulateContents）**:

| 变体 | 名称 | 内容 |
|---|---|---|
| `/combat_tech` | "combat tech's chest rig"（战斗技师胸挂） | 螺丝刀、扳手、焊枪、撬棍、剪线钳、万用表、线缆卷 |
| `/field_medic` | "field medic's chest rig"（野战医护胸挂） | 手术刀、`circular_saw/field_medic`（骨锯）、止血钳、拉钩、烧灼器、手术单、正骨器 |
| `/pointman` | "pointman's chest rig"（尖兵胸挂） | 威士忌瓶、**塑钢 ×5**、吗啡瓶 |
| `/marksman` | "marksman's chest rig"（神射手胸挂） | 双筒望远镜、稳健牌香烟、打火机、骷髅头巾 |

### 1.2.3 靴子与手套

**远征军靴**（`/obj/item/clothing/shoes/combat/expeditionary_corps`）:
- "expeditionary corps boots"，描述 "High speed, low drag combat boots."（高速低阻力战斗靴）。
- `icon_state = "exp_corps"`，`inhand_icon_state = "jackboots"`。

**黑色手套**（`/obj/item/clothing/gloves/color/black/expeditionary_corps`）:
- 名称 "expeditionary corps gloves"；`icon_state = "exp_corps"`。
- 寒冷保护 HANDS / `GLOVES_MIN_TEMP_PROTECT`；炎热保护 HANDS / `GLOVES_MAX_TEMP_PROTECT`；`resistance_flags = FIRE_PROOF`。

**绝缘手套**（`/obj/item/clothing/gloves/chief_engineer/expeditionary_corps`）:
- 名称 "expeditionary corps insulated gloves"（绝缘）；`icon_state = "exp_corps_eng"`，`worn_icon_state = "exp_corps"`。
- 护甲 `/datum/armor/chief_engineer_expeditionary_corps`: **fire 80 / acid 50**。
- `clothing_traits = list(TRAIT_FAST_CUFFING)`（快速上铐，与其他黑色手套一致）。

**医用丁腈手套**（`/obj/item/clothing/gloves/latex/nitrile/expeditionary_corps`）:
- 名称 "expeditionary corps medic gloves"（医用）；`icon_state = "exp_corps_med"`，`worn_icon_state = "exp_corps"`。
- 冷/热保护 HANDS 全开；`FIRE_PROOF`。
- 护甲 `/datum/armor/nitrile_expeditionary_corps`: **fire 80 / acid 50**。

### 1.2.4 远征军袋（`/obj/item/storage/backpack/duffelbag/expeditionary_corps`）

- 名称 "expeditionary corps bag"（远征军袋）；描述 "A large bag for holding extra tactical supplies."（装额外战术物资的大包）。
- `icon_state = "exp_corps"`，`inhand_icon_state = "backpack"`；图标在模块 `icons/backpack.dmi` 与 `icons/mob_backpack.dmi`。
- **可换肤**: `/datum/atom_skin/expeditionary_corps_bag`:
  - `backpack`: 预览名 "Backpack"，`new_icon_state = "exp_corps"`；
  - `belt`: 预览名 "Belt"，`new_icon_state = "exp_corps_satchel"`（挎包样式）。

### 1.2.5 防弹背心（`/obj/item/clothing/suit/armor/vest/expeditionary_corps`）

- 名称 "expeditionary corps armor vest"；描述: "An armored vest that provides okay protection against most types of damage. Includes concealable sleeves for your arms."（对大多数伤害提供尚可防护的背心，带可隐藏的臂套）。
- `icon_state = "exp_corps"`；`body_parts_covered = CHEST|GROIN|ARMS`；冷/热保护同覆盖范围；`dog_fashion = null`。

**护甲数值 `/datum/armor/vest_expeditionary_corps`**:

| 类型 | 值 |
|---|---|
| melee（近战） | **30** |
| bullet（子弹） | **30** |
| laser（激光） | **30** |
| energy（能量） | **30** |
| bomb（爆炸） | **40** |
| fire（火焰） | **80** |
| acid（酸） | **100** |
| wound（伤口） | **10** |

**可容纳（allowed）**: `/obj/item/melee`、`/obj/item/ammo_box`、`/obj/item/ammo_casing`、`/obj/item/flashlight`、`/obj/item/gun`、`/obj/item/knife`、`/obj/item/reagent_containers`、`/obj/item/restraints/handcuffs`、`/obj/item/tank/internals/emergency_oxygen`、`/obj/item/tank/internals/plasmaman`、`/obj/item/storage/belt/holster`、`/obj/item/storage/belt/machete`。

### 1.2.6 夜视头盔（`/obj/item/clothing/head/helmet/expeditionary_corps`）

- 名称 "expeditionary corps helmet"；描述: "A robust helmet worn by Expeditionary Corps troopers. **Alt+click it to toggle the NV system.**"（远征军士兵佩戴的坚固头盔。Alt+点击可切换夜视系统）。
- `icon_state = "exp_corps"`；`supports_variations_flags = CLOTHING_SNOUTED_VARIATION_NO_NEW_ICON`。
- 变量: `nightvision = FALSE`、`current_user`（当前佩戴者）；`actions_types = list(/datum/action/item_action/toggle_nv_helmet)`（"Toggle Nightvision" 技能按钮）。

**护甲数值 `/datum/armor/helmet_expeditionary_corps`**: melee **20** / bullet **20** / laser **20** / energy **20** / bomb **30** / fire **80** / acid **100** / wound **10**。

**夜视系统（NV）**:
- 开启（`enable_nv()`）: 佩戴者眼睛器官 `color_cutoffs = list(10, 30, 10)`（绿色调偏移）、`flash_protect = FLASH_PROTECTION_SENSITIVE`（闪盲敏感）；添加客户端滤镜 `/datum/client_colour/glass_colour/lightgreen`（浅绿玻璃）。
- 关闭（`disable_nv()`）: 还原 `initial()` 值、移除滤镜、`update_sight()`。
- 切换入口: 技能按钮（`/datum/action/item_action/toggle_nv_helmet/Trigger`）、**Alt+点击**（`click_alt`，返回 `CLICK_ACTION_SUCCESS`）；切换时 `to_chat` 开启/关闭提示。
- 生命周期: `equipped` 记录 `current_user`；`dropped` / `Destroy` 时自动 `disable_nv()` 并清空 `current_user`（防止残留滤镜）。
- `update_icon_state()`: 夜视开启时 `icon_state = "exp_corps_on"`。

## 1.3 装备全录（gear.dm）

### 1.3.1 远征医疗包（`/obj/item/storage/medkit/expeditionary`）

- 名称 "expeditionary medical kit"（远征医疗包）；描述 "Now with 100% less bullshit."（废话含量减少 100%）；`icon_state = "medkit_tactical"`；`damagetype_healed = "all"`（可治疗所有伤害类型）。
- 注释: "Gateway Medkit, no more combat defibs!"（网关医疗包，不再有战斗除颤器！）
- **内容**: 纱布卷、`defibrillator/compact/loaded`（紧凑型已装载除颤器）、`hypospray/combat`（战斗注射笔）、高级医用网、含药缝合线、健康 HUD 眼镜。

### 1.3.2 库存过剩医疗包（`/obj/item/storage/medkit/expeditionary/surplus`）

- 描述: "Now with less bullshit. And more dust. But mainly less bullshit. If you have to use this, there's no way you've got insurance."（废话少点，灰尘多点，主要废话少。要是你不得不用它，那你肯定没买保险）。
- **内容**: 十二卷纱布、`hypospray/combat`（注释: **epi/atro + lepo + omnizine**，即肾上腺素/阿托品 + 左旋麻黄碱 + 万能药）、**含药缝合线 ×2**、**高级医用网 ×2**、健康 HUD 眼镜。

### 1.3.3 骨锯（`/obj/item/circular_saw/field_medic`）

- 名称 "bone saw"（骨锯）；描述: "An ancient medical instrument used for surgery and amputations, still being used in the 26th century. Well, what are you waiting for? Let's go practice medicine."（26 世纪仍在使用的古老手术与截肢器械。还等什么？去实践医学吧）。
- 数值: `force = 20`（`/lowforce` 变体 **9**）；`toolspeed = 2`；`throw_range = 3`；`w_class = WEIGHT_CLASS_SMALL`；`hitsound = bladeslice.ogg`；攻击动词 "saws/slashes"（锯/劈）；图标 `icons/bonesaw.dmi` + 左右手持图标。

**主动截肢攻击（`attack()`）** —— 非战斗模式下手持骨锯攻击碳基生物（非胸口部位）:
1. 目标无 `TRAIT_NODISMEMBER`（不可肢解）→ 拒绝。
2. **精确部位=腹股沟（BODY_ZONE_PRECISE_GROIN）**: 寻找外部尾巴器官（`ORGAN_SLOT_EXTERNAL_TAIL`），有尾巴 → 目标为"切尾巴"；无尾巴 → 拒绝。
3. **其他部位**: 取对应身体部位（`get_bodypart`），无 → 拒绝。
4. 速度修正（基础 `toolspeed × 15 SECONDS = 30 秒`）:
   - 目标**无意识/瘫痪/硬直/濒死/死亡**（`stat >= UNCONSCIOUS` 或 `TRAIT_INCAPACITATED`）→ **×0.5**（容易对齐下刀，15 秒）；
   - 目标**痉挛（jitter）**且未死 → **×1.5**（"15×0.5×1.5=11.25，被电棍电到痉挛的人不够时间被你切头，但没痉挛的人可以"）；
   - 使用者有 **TRAIT_MORBID（病态）** → **×0.7**（"its morbin time"）。
5. `do_after(user, 30s × 修正)` 成功 → 尾巴 `Remove + forceMove` 到脚下，或肢体 `dismember()`；双方日志 `LOG_GAME`；病态者获得心情事件 `morbid_dismemberment`。

**自杀动作（`suicide_act`）**: 除胸口（CHEST）外的**所有身体部位每隔 1 秒依次肢解**，播放 `bladeslice.ogg`（70 音量），结束返回 `BRUTELOSS`（致命钝伤）。

### 1.3.4 尖兵盾（`/obj/item/shield/riot/pointman`）

- 名称 "pointman shield"（尖兵盾）；描述: "A shield fit for those that want to sprint headfirst into the unknown. Its heavy, unwieldy nature makes its defensive performance suffer when in the off-hand; wielding will provide best results at the cost of reduced mobility."（适合想一头冲进未知的人。沉重笨重，副手持握时防御性能下降；双手持握效果最佳但牺牲机动性）。
- 数值: `force = 10`；`throwforce = 5`；`throw_speed = 1`；`throw_range = 1`；`block_chance = 15`；`w_class = WEIGHT_CLASS_BULKY`；`transparent = FALSE`；`max_integrity = 200`；`shield_break_leftover = /obj/item/pointman_broken`。
- 材料（`custom_materials`）: **塑钢 ×3 片、玻璃 ×3 片、铁 ×1.5 片**。
- 双手组件（`/datum/component/two_handed`）: 副手 `force = 10`，双手 `force = 20`。
  - `shield_wield()`（双手）: `item_flags |= SLOWS_WHILE_IN_HAND`；`block_chance *= 5`（**15 → 75**）；`slowdown = 0.6`。
  - `shield_unwield()`（放下）: 移除减速旗标；`block_chance /= 5`；`slowdown = 0`。

**损坏品（`/obj/item/pointman_broken`）**:
- 名称 "broken pointman shield"；描述 "Enough of it is still intact that you could probably just weld more bits on."（完整部分足够让你焊点东西上去）；`icon_state = "riot_broken"`；BULKY。
- `Initialize`: 添加 `/datum/element/slapcrafting`（拍打合成）→ 配方 `/datum/crafting_recipe/pointman_repair`。

**修复配方（`/datum/crafting_recipe/pointman_repair`）**:

| 项目 | 值 |
|---|---|
| 名称 | "pointman shield (repaired)"（尖兵盾（修复）） |
| 结果 | `/obj/item/shield/riot/pointman` |
| 需求 | 破损尖兵盾 ×1 + **塑钢片 ×3** + **强化玻璃（rglass）×3** |
| 时间 | **5 SECONDS** |
| 分类 | `CAT_MISC` |
| 工具 | `TOOL_WELDER`（焊枪） |

### 1.3.5 飞刀（`/obj/item/knife/combat/throwing`）

- 名称 "throwing knife"（飞刀）；描述: "While very well weighted for throwing, the distribution of mass makes it unwieldy for use in melee."（投掷配重极佳，但质量分布使其近战笨拙）。
- 数值: `force = 12`（"别拿它捅人"）；`throwforce = 30`（嵌入时 **38** 伤害，"与手里剑对比一下"）；`throw_speed = 4`；`embed_type = /datum/embedding/combat_knife/throwing`。
- 嵌入数据: `embed_chance = parent(combat_knife 的 65) + 10 = **75%**`。

### 1.3.6 神射手刀囊（`/obj/item/storage/pouch/ammo/marksman`）

- 名称 "marksman's knife pouch"（神射手刀囊）。
- `setup_reskins()` → return（**禁皮肤**）。
- 储物格 `/datum/storage/marksman`: `max_total_storage = 60`；`max_slots = 10`；`numerical_stacking = TRUE`（同种堆叠）；`quickdraw = TRUE`（快速抽出）；`can_hold = typecacheof(/obj/item/knife/combat)`（只装战斗刀）。
- 默认内容: **5 把 `knife/combat/throwing`**（"5 把刀能干掉大多数基础敌人，不过神射手不该单挑"）。

## 1.4 远征战斧（tomahawk.dm）

**代码**: `modular_nova/modules/exp_corps/code/tomahawk.dm`（33 行）

`/obj/item/melee/tomahawk` — 名称 "expeditionary tomahawk"（远征战斧）;描述 "A decently sharp axe blade upon a short fibremetal handle."（短纤维金属柄上装着一片相当锋利的斧刃）。

| 属性 | 值 |
|---|---|
| `obj_flags` | `CONDUCTS_ELECTRICITY`（导电） |
| `force` | **15**（"等同于生存刀"） |
| `w_class` | `WEIGHT_CLASS_SMALL` |
| `throwforce` | **18** |
| `throw_speed` | **4** |
| `throw_range` | **8** |
| `embed_type` | `/datum/embedding/tomahawk` |
| 材料 | 铁 ×**7.5** 片（`SHEET_MATERIAL_AMOUNT*7.5`） |
| 攻击动词 | 砍/撕裂/划伤/切割（chops/tears/lacerates/cuts） |
| `hitsound` | `bladeslice.ogg` |
| `sharpness` | `SHARP_EDGED`（利刃） |

**嵌入数据 `/datum/embedding/tomahawk`**: `pain_mult = 6`（疼痛倍率 6）、`embed_chance = 60`（嵌入率 60%）、`fall_chance = 10`（掉落率 10%）。

**屠宰组件（`/datum/component/butchering`）**: `speed = 7 SECONDS`、`effectiveness = 100`（可当屠宰工具用，7 秒 100% 效率）。

## 1.5 远征步兵配套（expeditionary_trooper.dm）

**代码**: `modular_nova/modules/exp_corps/code/expeditionary_trooper.dm`（32 行）

### 军用 PDA（`/obj/item/modular_computer/pda/expeditionary_corps`）

- 名称 "surplus military PDA"（军用剩余 PDA）；`greyscale_colors = "#891417#000099"`（深红 + 深蓝双色）。

### 远征生存包（`/obj/item/storage/box/expeditionary_survival`）

- 名称 "expedition survival pack"（远征生存包）；描述 "A box filled with useful items for your expedition!"（装满远征实用物品的盒子）；`icon_state = "survival_pack"`；`illustration = null`。
- **内容**:
  1. 甜甜圈盒（`storage/box/donkpockets`）
  2. 荧光棒（`flashlight/glowstick`）
  3. 双罐应急氧气（`tank/internals/emergency_oxygen/double`）
  4. 水瓶（`cup/glass/waterbottle`）
  5. O 型阴性血袋（`reagent_containers/blood/o_minus`）
  6. 注射器（`reagent_containers/syringe`）
  7. 万能药药瓶（`storage/pill_bottle/multiver`）

### 神射手保险箱（`/obj/structure/closet/crate/secure/exp_corps/marksman`）

- 注释: "edgy loner with knives AND A FUKKEN gun"（带着刀还有把枪的中二独狼）。
- **内容**:
  1. 常规医疗包（`storage/medkit/regular`）
  2. 远征生存包
  3. 无线电（`radio`）
  4. 神射手刀囊（`storage/pouch/ammo/marksman`）
  5. 远征军黑色手套
  6. 远征军夜视头盔
  7. 远征军防弹背心
  8. 神射手胸挂（`belt/military/expeditionary_corps/marksman`）
  9. 远征军袋（`backpack/duffelbag/expeditionary_corps`）
  10. 手枪枪盒 `storage/toolbox/guncase/nova/pistol/trappiste_small_case/skild`（Skild 手枪）

---

# 附录 · 代码路径索引


---

## 附录 · 代码索引

## 远征军（Expeditionary Corps）— `modular_nova/modules/exp_corps/`

| 文件 | 行数 | 内容 |
|---|---|---|
| `code/clothing.dm` | 280 | 制服/胸挂（4 变体）/靴/手套（3 种）/袋/背心/夜视头盔 |
| `code/gear.dm` | 231 | 医疗包（2 种）/骨锯/尖兵盾+修复/飞刀/刀囊 |
| `code/tomahawk.dm` | 33 | 远征战斧（嵌入 + 屠宰组件） |
| `code/expeditionary_trooper.dm` | 32 | 军用 PDA/远征生存包/神射手箱子 |
| `icons/` | — | backpack / mob_backpack / bonesaw(×3) / exp_crate / closet / riot(×3) / survival_pack / throwing / tomahawk(×4) 图标 |

---

*本百科依据 NovaSector 分支源码逐行整理，数值均直接取自代码默认值。*
