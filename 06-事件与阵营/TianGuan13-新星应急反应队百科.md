# TianGuan13 新星应急反应队百科

> 本文档基于 NovaSector 分支源码 `modular_nova/modules/novaya_ert/`（共 17 个 `.dm`，1,935 行）全量整理。
> 覆盖：**新星应急反应队（Novaya ERT / HC — High Command 高等指挥部）全套装备、货币系统、机甲与配装**。
> 双语：英文原文 + 中文释义；数值均取自源码，精确到原值。

## 目录

- [1. 模块总览](#1-模块总览-module-overview)
  - [1.1 模块信息与 Credits](#11-模块信息与-credits)
  - [1.2 源码文件清单](#12-源码文件清单)
- [2. 货币系统（Currency）](#2-货币系统-currency)
- [3. 背包（Back）](#3-背包-back)
- [4. 腰带（Belt）](#4-腰带-belt)
- [5. 头部与制服（Head & Uniform）](#5-头部与制服-head-uniform)
- [6. ID 证件与职务标签（ID & Trims）](#6-id-证件与职务标签-id-trims)
- [7. 盾牌（Shield）](#7-盾牌-shield)
- [8. 太空装甲：Voskhod-P（Space Suit）](#8-太空装甲voskhod-p-space-suit)
- [9. 生存包（Survival Pack）](#9-生存包-survival-pack)
- [10. 武器：电击与眩晕（Stun Weapons）](#10-武器电击与眩晕-stun-weapons)
- [11. 弹药箱（Ammo Boxes）](#11-弹药箱-ammo-boxes)
- [12. 联盟警察制服（Coalition Police Outfit）](#12-联盟警察制服-coalition-police-outfit)
- [13. CIN 剩余装甲（CIN Surplus Armor）](#13-cin-剩余装甲-cin-surplus-armor)
- [14. MOD 装甲（MOD Suits）](#14-mod-装甲-mod-suits)
- [15. 机甲（Mecha）](#15-机甲-mecha)
- [16. 机甲武器（Mecha Weapons）](#16-机甲武器-mecha-weapons)
- [17. 配装与套装组合（Loadouts & Sets）](#17-配装与套装组合-loadouts-sets)
- [18. 数值速查表（Cheat Sheet）](#18-数值速查表-cheat-sheet)

---

## 1. 模块总览 (Module Overview)

源码路径：`modular_nova/modules/novaya_ert/readme.md`

### 1.1 模块信息与 Credits

| 项目 | 内容 |
|---|---|
| 模块名 | Novaya Rossiyskaya Imperiya Module（新罗西亚帝国模块） |
| MODULE ID | HC STUFF |
| 描述 | 一切与 HC（高等指挥部 / High Command）相关的内容：从各类外观服装、枪械到 ERT（应急反应队） |
| 贡献者 | Gandalf2k15（代码与部分图标）、Stalkeros（代码与部分图标）、Zydras（HC 警察服装、电击枪与 HC 全息路障图标）、Ramirez（图标与创意）、Flavrius（图标与杂项） |

### 1.2 源码文件清单

| 文件 | 行数 | 内容 |
|---|---|---|
| `code/back.dm` | 35 | 帝国突击背包（4 变体 + 普通版） |
| `code/belt.dm` | 70 | 战术腰带（4 色 + 6 种预装填变体） |
| `code/currency.dm` | 138 | 联盟货币系统（刨屑/比特/马克/王冠/熔炉马克） |
| `code/head.dm` | 14 | 指挥官贝雷帽（含装甲数据） |
| `code/id.dm` | 51 | HC ID 卡与 9 种职务标签 |
| `code/uniform.dm` | 28 | 先进帝国作战服（4 变体） |
| `code/shield.dm` | 21 | 重型医护兵盾 + 破损盾 |
| `code/space_suit.dm` | 65 | Voskhod-P 停能战斗装甲（衣+盔） |
| `code/survival_pack.dm` | 41 | HC 生存包 + 注射器盒 + 信号棒盒 |
| `code/taser.dm` | 51 | Mírotvůrce 手摇充电电击枪 |
| `code/toolbox.dm` | 37 | 5 种预装填弹药箱 |
| `code/stun_gun.dm` | 109 | Kopřiva 电击枪 + 简易电击刀 + 合成配方 |
| `code/police_outfit.dm` | 149 | 联盟警察全套制服（10 件） |
| `code/surplus_armor.dm` | 276 | CIN 剩余装甲（头盔/制服/背心/胸挂/背包） |
| `code/mech.dm` | 231 | M/TACS-1-LF 典狱官机甲 + WUNK 仲裁者升级 |
| `code/mech_weapons.dm` | 310 | 机甲武器（熔炉炮/等离子机枪/自动炮荚/制导火箭） |
| `code/mod_suit.dm` | 309 | 边境世界督察 MOD 装甲 + 自动医疗模块 + Voskhod 改装 |

---

## 2. 货币系统 (Currency)

源码路径：`code/currency.dm`

联盟（Coalition / 赫利奥静态联盟）货币体系，全部由 **碳化钨（tungsten carbide）** 铸造，由 **KMIF（Kemppainen-Morozov 工业制造）** 制造，面值锚定于 **莫罗佐夫矩阵（Morozov Matrix）**。进制：**1 马克 = 128 刨屑**；1 比特 = 4 刨屑；1 王冠 = 4096 刨屑（= 32 马克）；1 熔炉马克 = 16,384 刨屑（= 128 马克）。

### 2.1 刨屑 — Shaving

- **路径**: `/obj/item/stack/spacecash/shaving`（`/full` 变体 `amount = 128`）
- **面值**: `value = 1`；堆叠上限 `max_amount = 128`
- **材质**: 纯碳化钨六角形薄片，每片精确 **0.3 克**，由 KMIF 制造机激光切割
- **特性**: `LAVA_PROOF | FIRE_PROOF`（碳化钨熔点极高）；材料：钛 ×2（`COIN_MATERIAL_AMOUNT * 0.1` = 20×0.1）
- **EN**: "A tiny hexagonal flake of pure tungsten carbide. The smallest legal tender in Coalition space, serving as the atomic unit of the Mark for rounding adjustments and micro-transactions."
- **CN**: 纯碳化钨的六角形微片，联盟空间内最小法定货币，是马克的原子单位，用于找零调整与微交易。
- **功能/设定**: 马克可整除为 128 等份；六角形状防"剪币"（物理削边囤金属）作弊；如今由 Soft Mark 数字账本处理分数交易，实体刨屑几乎不见天日。边境俚语："A Shaving in the hand is worth a Soft Mark in the cloud"（手中一片刨屑胜过云上一枚软马克）。

### 2.2 比特 — Bit

- **路径**: `/obj/item/coin/mark/bit`
- **面值**: `value = 4`
- **材质**: 八边形碳化钨硬币，边缘滚花，冲压 KMIF 印章（实际上只是个"K"）；材料：钛 ×20（`COIN_MATERIAL_AMOUNT`）
- **EN**: "A small octagonal tungsten carbide coin, edge-milled and stamped with KMIF seal (really, just a 'K'). One Bit is roughly the cost of a cup of coffee and a bun from a food replicator — a working-class business lunch, in pure feedstock and power alone."
- **CN**: 一枚小型八边形碳化钨硬币，边缘滚花并冲压 KMIF 印章（其实只是个"K"）。一比特大约等于从食品复制机买一杯咖啡加一个面包——纯原料与电力成本意义上的工薪阶层工作午餐。
- **功能/设定**: 正反两面为"比特正面/比特反面"（`bit's heads` / `bit's tails`）；实际咖啡价为 1 比特 3 刨屑，多数售货机直接按 2 比特四舍五入。"差不多就行"（close enough）的货币；边铣工艺为自动售货机提供抓握并增加伪造难度。设计百年未变，是稳定性的宣言。

### 2.3 公会马克 — Guild Mark

- **路径**: `/obj/item/coin/mark/mark`（基类 `/obj/item/coin/mark` 为抽象类型）
- **面值**: `value = 128`
- **材质**: 2cm 圆形碳化钨硬币，锯齿边缘，带联盟徽记；正面为联盟太阳，背面为面值与可追溯的 KMIF 制造批号；材料：钛 ×10（`SMALL_MATERIAL_AMOUNT`）
- **EN**: "A 2cm round tungsten carbide coin with serrated edges and the Coalition seal. One Mark is the price of a decent meal at a sit-down Mid-world restaurant, as proper as they can get, when that 'proper' sells experience more than the food itself."
- **CN**: 一枚 2cm 圆形碳化钨硬币，锯齿边缘与联盟徽记。一马克等于中世界（Mid-world）一家像样餐厅一顿正餐的价格——那种"像样"卖的是体验而非食物本身。
- **功能/设定**: 锯齿边缘是可触摸识别的标志，黑暗中商人与士兵都能立即辨认；以碳化钨储备与认证劳动工时双重背书，花马克如同交换一小块真实的物理价值。一马克可买：有桌服务的晚餐、KMIF 授权手工工具、经济型酒店一夜、边境跳蚤市场上的标准八联装弹药匣。"How much do you make?" "Eighty Marks a week."（一周八十马克）——这是有意义的数字。

### 2.4 王冠 — Crown

- **路径**: `/obj/item/coin/mark/crown`
- **面值**: `value = 4096`
- **材质**: 3cm 碳化钨硬币，**中央穿孔**（可穿绳携带），边缘带**红色示踪带**（特殊处理合金，可被常见防伪探测棒识别）；材料：钛 ×40（`SMALL_MATERIAL_AMOUNT * 4`）
- **EN**: "A 3cm tungsten carbide coin with a central hole and a red trace band. One Crown buys three Zaibas plasma rifles on the Rim. A significant purchase, the kind of money that changes a frontier settlement's defensive capabilities."
- **CN**: 一枚 3cm 碳化钨硬币，中央穿孔并带红色示踪带。在边境（Rim）一王冠可买三把 Zaibas 等离子步枪——这是一笔足以改变前沿定居点防御能力的重大购买。
- **功能/设定**: 联盟核心区：一王冠买一把 Zaibas 步枪并找回 **22.60Ɱ** 零钱（够买弹药、一把刀、一包薯片和一顿丰盛晚餐）；边境：同一王冠买三把 Zaibas 步枪。边境俚语：一串王冠（a 'string' of Crowns）= 一大笔钱；"松动的王冠"（loose Crown）= 意外开销；"数王冠"（Counting Crowns）= 盘点自身处境。EPF 外勤手册记载：巡逻队亮出一串王冠即可化解紧张局势。

### 2.5 熔炉马克 — Forge Mark

- **路径**: `/obj/item/coin/mark/forge_mark`
- **面值**: `value = 16384`（= 128 马克 = 16,384 刨屑）
- **材质**: **5 × 3 × 1 cm 碳化钨条**（非硬币），核心内嵌 **KMIF 罐装芯片（canister chip）**——微型量子密封认证装置，移除即毁条；材料：钛 ×160（`SMALL_MATERIAL_AMOUNT * 16`）
- **EN**: "A 5x3x1cm bar of tungsten carbide with an embedded KMIF canister chip, worth 128 Marks. One Forge Mark is approximately one deployment's pre-bonus, pre-tax salary for an Expeditionary Police Force officer. The wage that buys competence, loyalty, and the willingness to face the Rim's dangers."
- **CN**: 一块 5×3×1cm 的碳化钨条，内嵌 KMIF 罐装芯片，价值 128 马克。一熔炉马克约等于远征警察部队（EPF）军官一次部署、扣税前、扣奖金前的薪水——这份薪酬买到的是能力、忠诚与面对边境危险的意愿。
- **功能/设定**: 可买：租赁二手货船（小型、二手、大概率要修）、小行星采矿权首付、使边境家庭免于破产的紧急医疗、技术大学一年学费、中世界城市小公寓、**十三把 Zaibas 步枪**；核心区用 Soft Mark 量子加密数字转账，边境则靠实体硬通货。EPF 不成文规定：绝不要在酒吧里亮出熔炉马克。正反两面图标可翻转（`attack_self` 切换 "forge_mark_up" / "forge_mark_down"）。

---

## 3. 背包 (Back)

源码路径：`code/back.dm`

### 3.1 帝国突击背包 — Imperial Assault Pack（绿色，辛迪加帆布包系）

- **路径**: `/obj/item/storage/backpack/duffelbag/syndie/nri`
- **EN**: "A large green backpack for holding extra tactical supplies. It appears to be made from lighter yet sturdier materials."
- **CN**: 大型绿色背包，用于存放额外战术补给。似乎由更轻却更坚固的材料制成。
- **属性**: 抗性 `FIRE_PROOF`（防火）；手持图标 "securitypack"；图标状态 `russian_green_backpack`。
- **变体**（同父系，仅换色与描述）：
  - `/captain` 队长版：黑色（`russian_black_backpack`）
  - `/medic` 医护版：白色（`russian_white_backpack`）
  - `/engineer` 工程师版：棕色（`russian_brown_backpack`）

### 3.2 帝国突击背包 — Imperial Assault Pack（普通背包系）

- **路径**: `/obj/item/storage/backpack/nri`
- **EN**: "A large green backpack for holding extra tactical supplies."
- **CN**: 大型绿色背包，用于存放额外战术补给。
- **属性**: 同上款图标，但无"更轻更坚固"描述、无 FIRE_PROOF 抗性声明。

---

## 4. 腰带 (Belt)

源码路径：`code/belt.dm`

### 4.1 战术腰带 — Tactical Belts（军事腰带系）

| 变体 | 路径 | 名称（EN/CN） | 图标状态 |
|---|---|---|---|
| 基础 | `/obj/item/storage/belt/military/nri` | green tactical belt / 绿色战术腰带 | `russian_green_belt` |
| 队长 | `.../nri/captain` | black tactical belt / 黑色战术腰带 | `russian_black_belt` |
| 医护 | `.../nri/medic` | blue tactical belt / 蓝色战术腰带 | `russian_white_belt`（图标为白） |
| 工程师 | `.../nri/engineer` | brown tactical belt / 棕色战术腰带 | `russian_brown_belt` |

- **描述（EN）**: "A [颜色] tactical belt made for storing military grade hardware."
- **描述（CN）**: 一条为存放军规级硬件而设计的[颜色]战术腰带。
- 全部为 `/obj/item/storage/belt/military` 子类（军用存储腰带）。

### 4.2 预装填腰带变体（PopulateContents 配装）

| 变体 | 预装内容 |
|---|---|
| `/nri/plus_mre`（督察版） | 1× HC 生存包（`/obj/item/storage/box/nri_survival_pack/inspector`） |
| `/nri/soldier`（士兵版） | 4× Lanca 战斗步枪弹匣 + 1× 战斗刀 + 1× 烟雾弹 + 1× 破片手雷 |
| `/nri/heavy`（重装版） | 4× Stechkin APS 弹匣（m9mm_aps）+ 1× 战斗刀 + 1× 烟雾弹 + 1× 破片手雷 |
| `/nri/captain/full`（队长满配版） | 4× Lanca 弹匣 + 1× 战斗刀 + 1× 烟雾弹 + 1× 破片手雷 |
| `/nri/medic/full`（医护满配版） | 4× Miecz 冲锋枪弹匣 + 1× 战斗刀 + 1× 烟雾弹 + 1× 破片手雷 |
| `/nri/engineer/full`（工程师满配版） | 4× Miecz 弹匣 + 1× 战斗刀 + 1× 烟雾弹 + 1× 破片手雷 |

---

## 5. 头部与制服 (Head & Uniform)

源码路径：`code/head.dm`、`code/uniform.dm`

### 5.1 指挥官贝雷帽 — Commander's Beret

- **路径**: `/obj/item/clothing/head/beret/sec/nri`
- **描述**: "Za rodinu!!"（俄语：为了祖国!!）
- **装甲** `/datum/armor/sec_nri`：

| melee 近战 | bullet 子弹 | laser 激光 | energy 能量 | bomb 爆炸 | fire 火焰 | acid 酸蚀 | wound 创伤 |
|---|---|---|---|---|---|---|---|
| 40 | 35 | 30 | 40 | 25 | 20 | 50 | 20 |

### 5.2 先进帝国作战服 — Advanced Imperial Fatigues

- **路径**: `/obj/item/clothing/under/costume/nri`（源自俄军制服代码）
- **EN**: "The latest in tactical and comfortable russian military outfits."
- **CN**: 最新一代兼具战术性与舒适性的俄国军用服装。
- **属性**: 装甲 `/datum/armor/clothing_under/costume_nri`：**melee 10 / fire 30 / acid 30**；剥取延迟 `strip_delay = 50`；传感器模式 `SENSOR_COORDS`（坐标传感，默认开启，`random_sensor = FALSE`）；不可调整（`can_adjust = FALSE`）；图标 `nri_soldier`。
- **变体**（仅换图标）：
  - `/captain` 队长版：`nri_captain`
  - `/medic` 医护版：`nri_medic`
  - `/engineer` 工程师版：`nri_engineer`

---

## 6. ID 证件与职务标签 (ID & Trims)

源码路径：`code/id.dm`

### 6.1 HC ID — 高等指挥部证件

- **路径**: `/obj/item/card/id/advanced/centcom/ert/nri`
- **名称**: "HC ID"（高等指挥部 ID）
- **描述**: "An ID straight from the HC." / 一张直接来自高等指挥部的 ID。
- **属性**: 黑色卡片（`card_black`），已分配图标 `assigned_centcom`；中央指挥部级高级 ID。

### 6.2 职务标签 — ID Trims

| 标签路径 | 职务（EN） | 职务（CN） | 备注 |
|---|---|---|---|
| `/datum/id_trim/nri` | HC Soldier | HC 士兵 | 基础版；威胁修正 `threat_modifier = 2`（与 nri_police 一致）；权限 = 中央指挥部区域 + 全站区域（`REGION_CENTCOM + REGION_ALL_STATION`） |
| `.../commander` | HC Platoon Commander | HC 排指挥官 | 指挥官样式（红部色 + 指挥蓝副色，专属 HUD 图标） |
| `.../heavy` | HC Heavy Soldier | HC 重装士兵 | — |
| `.../medic` | HC Corpsman | HC 医护兵 | — |
| `.../engineer` | HC Combat Engineer | HC 战斗工程师 | — |
| `.../diplomat` | HC Diplomat | HC 外交官 | 使用指挥官样式 trim |
| `.../diplomat/major` | HC Major | HC 少校 | — |
| `.../diplomat/scientist` | HC Research Inspector | HC 研究督察 | — |
| `.../diplomat/doctor` | HC Medical Inspector | HC 医疗督察 | — |

- 通用样式：`trim_nri`（指挥官/外交官为 `trim_nri_commander`）；部门色 `COLOR_RED_LIGHT`（浅红），副部门色 `COLOR_COMMAND_BLUE`（指挥蓝）；安保 HUD 图标 `hud_nri`（指挥官 `hud_nri_commander`）。

---

## 7. 盾牌 (Shield)

源码路径：`code/shield.dm`

### 7.1 重型医护兵盾 — Heavy Corpsman Shield

- **路径**: `/obj/item/shield/riot/pointman/nri`
- **EN**: "A shield designed for people that have to sprint to the rescue. Cumbersome as hell. Repair with plasteel."
- **CN**: 为必须飞奔救援的人设计的盾牌。笨重得要命。可用塑料钢（plasteel）修复。
- **属性**: 防暴盾（riot）类型；**不透明**（`transparent = FALSE`）；**损坏后残留物**：`/obj/item/corpsman_broken`（破损医护兵盾）。

### 7.2 破损医护兵盾 — Broken Corpsman Shield

- **路径**: `/obj/item/corpsman_broken`
- **名称**: "broken corpsman shield" / 破损的医护兵盾
- **描述**: "Might be able to be repaired with a welder." / 也许能用焊枪修好。
- **属性**: 重量等级 `WEIGHT_CLASS_BULKY`（笨重）；**焊枪交互（welder_act）**：使用焊枪可当场将其修复为新盾牌（`/obj/item/shield/riot/pointman/nri`）。

---

## 8. 太空装甲：Voskhod-P (Space Suit)

源码路径：`code/space_suit.dm`

### 8.1 Voskhod-P 停能战斗装甲 — Voskhod-P Depowered Combat Armor

- **路径**: `/obj/item/clothing/suit/space/voskhod`
- **EN**: "A hybrid set of space-resistant armor built on a modified mass-produced 'Dawn' space suit, polyurea coated durathread-lined light plasteel plates hinder mobility as little as possible while the onboard life support system aids the user in combat. The power cell is what makes the armor work without hassle, a sticker in the power supply unit warns anyone reading to responsibly manage battery levels. These 'paralyzed', marketable variations of the suit come with most of their main features removed: from the infamous wound-tending systems, to the less appreciated death alarms."
- **CN**: 一套基于改装量产型"黎明"（Dawn）太空服的混合太空抗性装甲：聚脲涂层的杜拉纤维衬里轻质塑料钢装甲板将机动性影响降到最低，机载生命维持系统在战斗中辅助使用者。让装甲无碍运转的关键是动力电池，供电单元上的贴纸警告阅读者负责任地管理电量。这些"瘫痪"的可销售型号删除了大部分核心功能：从臭名昭著的伤口护理系统，到鲜为人知的死亡警报。
- **属性**: 允许槽位：枪械 / 警棍 / 能量剑（saber）/ 手铐 / 内置气罐（`allowed` 列表）；装甲 `/datum/armor/space_syndicate`（上游定义，见 §18 速查表）；`supports_variations_flags = NONE`；挂载 `dawn_branding` 组件（examine_more 增加"黎明"背景说明）；**Teshari（鸟人种）无法装备**（`mob_can_equip` 拒绝并提示）。

### 8.2 Voskhod-P 停能战斗头盔 — Voskhod-P Depowered Combat Helmet

- **路径**: `/obj/item/clothing/head/helmet/space/voskhod`
- **EN**: "A composite graphene-plasteel helmet with a ballistic nylon inner padding, complete with a deployable airtight polycarbonate visor and respirator system. This particular unit's rebreathers have been salvaged off; unable to resynthesize any more breathable air for the user."
- **CN**: 复合石墨烯-塑料钢头盔，内衬防弹尼龙，配可展开的气密聚碳酸酯面罩与呼吸器系统。本件的循环呼吸装置已被拆除，无法再为使用者合成可呼吸空气。
- **属性**: 装甲 `/datum/armor/space_syndicate`；`supports_variations_flags = NONE`；挂载 `dawn_branding` 组件；**Teshari 无法装备**。

### 8.3 黎明品牌组件 — Dawn Branding Component

- **路径**: `/datum/component/dawn_branding`
- **功能**: 向物品的 `examine_more`（深度检视）列表追加"黎明"系列设定说明文本（`LANG("datum.39a524e4")`），挂在 Voskhod 装甲与头盔上。

---

## 9. 生存包 (Survival Pack)

源码路径：`code/survival_pack.dm`

### 9.1 HC 生存包 — HC Survival Pack

- **路径**: `/obj/item/storage/box/nri_survival_pack`
- **EN**: "A box filled with useful emergency items, supplied by the HC."
- **CN**: 一箱由 HC 供应的实用应急物品。
- **预装内容**（PopulateContents 全量）：
  1. 1× 氧气蜡烛（`/obj/item/oxygen_candle`）
  2. 1× 双联紧急氧气罐（`/obj/item/tank/internals/emergency_oxygen/double`）
  3. 1× 1000 信用点现金堆（`/obj/item/stack/spacecash/c1000`）
  4. 1× 铁片药瓶（`/obj/item/storage/pill_bottle/iron`）
  5. 1× 殖民地口粮盒（`/obj/item/storage/box/colonial_rations`）
  6. 1× 注射器盒（`/obj/item/storage/box/nri_pens`）
  7. 1× 信号棒盒（`/obj/item/storage/box/nri_flares`）
  8. 1× 红色撬棍（`/obj/item/crowbar/red`）

### 9.2 注射器盒 — Box of Injectors

- **路径**: `/obj/item/storage/box/nri_pens`
- **EN**: "A box full of first aid and combat MediPens."
- **CN**: 一盒急救与战斗医疗笔（MediPen）。
- **预装内容**（9 支）：
  1. 急救包医疗笔（ekit）
  2. 兴奋剂医疗笔（stimpack/traitor，叛徒版）
  3. 氧甲氢龙（oxandrolone，烧伤药）医疗笔
  4. 水杨酸（salacid，挫伤药）医疗笔 ×2
  5. 青霉酸（penacid，毒素药）医疗笔
  6. 沙丁胺醇（salbutamol，缺氧药）医疗笔
  7. 阿托品（atropine，濒死救急）医疗笔
  8. 失血（blood_loss）医疗笔

### 9.3 信号棒盒 — Box of Flares

- **路径**: `/obj/item/storage/box/nri_flares`
- **EN**: "A box full of red emergency flares."
- **CN**: 一盒红色应急信号棒。
- **预装内容**: 7× 红色信号棒（`/obj/item/flashlight/flare`）。

---

## 10. 武器：电击与眩晕 (Stun Weapons)

源码路径：`code/taser.dm`、`code/stun_gun.dm`

### 10.1 Mírotvůrce 个人安抚器 — Mírotvůrce Personal Pacifier（手摇电击枪）

- **路径**: `/obj/item/gun/energy/taser/crank`
- **EN**: "A low-capacity, electrode-based taser, outfitted with an efficient dynamo machine to replenish its charge. Utilised first as a means of first line of defense among colonial militiamen, now outclassed by more modern variations; thus, resold in the civilian market as a cheap way of deterring assistants. Or was it 'assailants'?"
- **CN**: 低容量电极泰瑟枪，配备高效发电机制（dynamo）以补充电荷。最初作为殖民地民兵的第一道防线使用，如今已被更现代的型号超越，因而在民用市场廉价转售，成为威慑"助理"的便宜手段——还是说"袭击者"？
- **属性**:
  - 射击延迟 `fire_delay = 2 SECONDS`
  - 弹药：电极弹壳（crank_taser）
  - 电池：专用曲柄电池（见下），`charge_sections = 3`（3 格充能显示）
  - `ammo_x_offset = 2`
  - 制造商：**ZCM**（`COMPANY_ZCM`，深检视可见）
  - 背景设定（lore_blurb）："米罗特夫采"曾预期成为殖民定居初期的居民主要防线，产量惊人；民兵评价其容量与电压太小，只能制服一两个人，"不适合现役"，但内置曲柄发电机可持续性极高。

**子部件**：
- 电极弹壳 `/obj/item/ammo_casing/energy/electrode/crank_taser` → 投射物 `/obj/projectile/energy/electrode/crank_taser`：**耐力伤害 `tase_stamina = 15`**
- 专用电池 `/obj/item/stock_parts/power_store/cell/crank_taser`："Mírotvůrce power cell" / 米罗特夫采动力电池；**容量 `maxcharge = STANDARD_CELL_CHARGE × 0.4` = 4,000 J（4 kJ，标准电池 10 kJ 的 40%）**
- 曲柄充能组件：每次充能 **5% 标准电池容量（500 J）**，冷却 1 秒，充能音效 crank.ogg，可边移动边充（`charge_move = IGNORE_USER_LOC_CHANGE`）

### 10.2 Kopřiva 电击枪 — Kopřiva Stun Gun

- **路径**: `/obj/item/melee/baton/security/stun_gun`
- **EN**: "A compact, remote-sized stun gun for deterring people with. While its voltage and size leave it unable to knock someone down, its best means of use is deterrence or dissuasion - give them a reason to back off, or hit a troublemaker and hope they don't follow."
- **CN**: 紧凑的遥控器大小的电击枪，用于威慑他人。其电压与体积无法击倒人，最佳用法是威慑与劝阻——给对方一个退让的理由，或者打一下闹事者并祈祷对方不跟上来。
- **操作说明**: "Left click to stun, right click to 'harm'." / 左键电击，右键"伤害"。
- **属性**:
  - 伤害 `force = 10`；投掷力 `throwforce = 0`
  - **耐力伤害 `stamina_damage = 25`**；眩晕判定护甲类型 `ENERGY`（能量抗性）
  - 击倒时间 `knockdown_time = null`（不击倒）；笨拙者击倒时间亦为 null
  - 冷却 `cooldown = 0.7 SECONDS`
  - **每次电击耗电 `cell_hit_cost = STANDARD_CELL_CHARGE × 0.75` = 7,500 J**
  - 投掷眩晕概率 `throw_stun_chance = 15`
  - 灯光：电光青 `LIGHT_COLOR_ELECTRIC_CYAN`，功率 0.25
  - 效果（baton_effect）：抖动 5 秒 / 混乱 4 秒 / 口吃 3 秒 / 视力模糊 5 秒（目标有 BATON_RESISTANCE 天赋则减半）；触发 COMSIG_LIVING_MINOR_SHOCK；被电目标无论是否抵抗都吃 25 耐力伤害（护甲减免）
  - 不可转换（`convertible = FALSE`）；激活时切换手持图标
  - 制造商：ZCM；深度设定：Zvirdnyn（兹维尔丁恩）核心世界警官标配，新型神经受体通过痛觉反应实现前所未有的安抚水平。
- **`/loaded` 变体**: 预装**高容量电池**（`/obj/item/stock_parts/power_store/cell/high`）。

### 10.3 简易电击刀 — Makeshift Stun Knife

- **路径**: `/obj/item/melee/baton/security/stun_gun/stun_knife`
- **EN**: "A Kopřiva stun gun haphazardly attached to a standard survival knife, making an odd combination of a lethally non-lethal weapon. Not the best for standing your ground, but it's better then nothing!"
- **CN**: 一把 Kopřiva 电击枪草率地绑在标准生存刀上，构成一种"致命地非致命"的古怪组合。不适合坚守阵地，但聊胜于无！
- **操作说明**: "Left click to stun, right click to slash." / 左键电击，右键劈砍。
- **属性**:
  - 刀刃锋利度 `SHARP_EDGED`；伤害 `force = 15`；投掷力 `throwforce = 15`
  - 创伤加成 `wound_bonus = 5`；暴露创伤加成 `exposed_wound_bonus = 15`
  - 嵌入类型 `/datum/embedding/combat_knife/weak`（弱化战斗刀嵌入）
  - 工具行为 `TOOL_KNIFE`（可作为刀工具）
  - 攻击动词：slash/slice/dice/cut；**备用戳刺模式**（`make_stabby()` 添加 `SHARP_POINTY` 备用锋利度组件）：stab/pierce/shank
  - 继承电击枪全部电击属性（耐力 25、耗电 7,500 J 等）
- **`/loaded` 变体**: 预装高容量电池；材料：铁 ×600（`SHEET_MATERIAL_AMOUNT × 6`）。

### 10.4 合成配方：自制电击刀 — Makeshift Stunknife

- **路径**: `/datum/crafting_recipe/knife_and_shocky`
- **名称**: "Makeshift Stunknife" / 自制电击刀
- **描述**: "Cobble together an abomination against both man and god." / 拼凑出一件亵渎人与神的造物。
- **需求**: 1× 生存战斗刀（`/obj/item/knife/combat/survival`）+ 1× Kopřiva 电击枪
- **工具**: 螺丝刀（`TOOL_SCREWDRIVER`）；耗时 **10 秒**；分类 `CAT_WEAPON_MELEE`（近战武器）
- 产物为 `/loaded` 版本（会吃掉电击枪内原有电池）。

---

## 11. 弹药箱 (Ammo Boxes)

源码路径：`code/toolbox.dm`

基类 `/obj/item/storage/toolbox/ammobox/full`：按 `amount` 数量生成 `ammo_to_spawn` 指定弹药。以下全部 **每箱 7 份**：

| 变体 | 名称 | 内容（每箱 7） |
|---|---|---|
| `/sakhno` | ammo box (Sakhno) / 弹药箱（萨赫诺） | Sakhno 精密步枪桥夹 `strilka310`（"标签若准确，应装 Sakhno 精密步枪或变体的桥夹"） |
| `/lanca` | ammo box (Lanca) / 弹药箱（兰卡） | Lanca 战斗步枪弹匣 `lanca` |
| `/nri_smg` | ammo box (Miecz) / 弹药箱（米奇） | Miecz 冲锋枪弹匣 `miecz` |
| `/l6_saw` | ammo box (L6 SAW) / 弹药箱（L6 班用机枪） | L6 班用自动武器弹箱 `m7mm` |
| `/aps` | ammo box (Stechkin APS) / 弹药箱（斯捷奇金 APS） | Stechkin APS 冲锋手枪弹匣 `m9mm_aps` |

---

## 12. 联盟警察制服 (Coalition Police Outfit)

源码路径：`code/police_outfit.dm`（HC 警察 / 联盟警察全套，制造商均为 ZCM）

### 12.1 联盟警察制服 — Coalition Police Outfit

- **路径**: `/obj/item/clothing/under/colonial/hc_police`
- **EN**: "Fancy blue durathread shirt and a pair of cotton-blend pants with a black synthleather belt. A time-tested design first employed by the HC police's precursor organisation, Rim-world Colonial Militia, now utilised by them as a tribute."
- **CN**: 华丽的蓝色杜拉纤维衬衫与棉混纺长裤，配黑色合成革腰带。这套久经考验的设计最早由 HC 警察的前身组织——边境世界殖民地民兵——采用，如今被其沿用以示致敬。
- **属性**: 装甲 `/datum/armor/clothing_under/rank_security`（上游，见 §18）；剥取延迟 5 秒；坐标传感（默认开，不可随机）；不可调整。

### 12.2 联盟警察裙装制服 — Coalition Police Skirt Outfit

- **路径**: `/obj/item/clothing/under/colonial/hc_police/skirt`
- **描述**: 同款蓝衬衫 + 棉混纺**铅笔裙**；可调整（`can_adjust = TRUE`）；女性精灵标志 `FEMALE_UNIFORM_TOP_ONLY`。

### 12.3 联盟警察斗篷 — Coalition Police Cloak

- **路径**: `/obj/item/clothing/neck/cloak/colonial/hc_police`
- **EN**: "A cloak made from heavy tarpaulin. Nigh wind- and waterproof thanks to its design. The signature white rectangle of the HC police covers the garment's back."
- **CN**: 厚重防水帆布制成的斗篷，设计使其近乎防风防水。HC 警察的标志性白色矩形覆盖衣物背面。
- **属性**: 允许额外存放：手铐 / 弹药盒 / 弹壳（`allowed` 扩展）。

### 12.4 联盟警察帽 — Coalition Police Cap

- **路径**: `/obj/item/clothing/head/hats/colonial/hc_police`
- **EN**: "A puffy cap made out of tarpaulin covered by some textile. It is sturdy and comfortable, and seems to retain its form very well. Silver HC police insignia is woven right above its visor."
- **CN**: 帆布覆纺织物的蓬松帽。坚固舒适，保形性极佳。帽檐上方织有银色 HC 警察徽记。
- **属性**: 装甲 `/datum/armor/cosmetic_sec`（外观安保，见 §18）。

### 12.5 联盟警察面具 — Coalition Police Mask

- **路径**: `/obj/item/clothing/mask/gas/hc_police`
- **描述**: "A close-fitting tactical mask." / 贴合面部的战术面具。
- **属性**: 隐藏面部毛发/面部/口鼻部（`HIDEFACIALHAIR|HIDEFACE|HIDESNOUT`）；遮盖口部+眼部（`MASKCOVERSMOUTH|MASKCOVERSEYES`）；**防胡椒喷雾（PEPPERPROOF）**（面罩与护目镜状态均生效）。

### 12.6 联盟警察头盔 — Coalition Police Helmet

- **路径**: `/obj/item/clothing/head/helmet/hc_police`
- **EN**: "Thick-looking tactical helmet made out of shaped Plasteel. Colored dark blue, similar to one coalition police is commonly using."
- **CN**: 造型厚实的塑料钢（Plasteel）战术头盔，深蓝色，与联盟警察常用款式一致。

### 12.7 联盟警察防弹插板背心 — Coalition Police Plate Carrier

- **路径**: `/obj/item/clothing/suit/armor/vest/hc_police`
- **EN**: "A reasonably heavy, yet comfortable armor vest comprised of a bunch of dense plates. Colored dark blue and bears a reflective stripe on the front and back."
- **CN**: 相当沉重但舒适的装甲背心，由一组致密插板构成。深蓝色，前后带有反光条。

### 12.8 联盟警察航空静压飞行夹克 — Coalition Police Aerostatic Bomber Jacket

- **路径**: `/obj/item/clothing/suit/armor/vest/hc_police_jacket`
- **EN**: "A jacket design worn by the more dynamic officers. There are quite a few pockets on the inside, mostly for storing notebooks and compasses."
- **CN**: 更活跃的警官穿着的夹克设计。内侧有相当多的口袋，主要用于存放笔记本和罗盘。
- **属性**: 装甲 `/datum/armor/armor_secjacket`（见 §18）；覆盖 胸部|裆部|双臂；冷/热防护 胸部|裆部|双臂|双手；**易燃（FLAMMABLE）**；额外允许存放：相机 / 剪贴板 / 文件夹 / 录音机 / 磁带（笔记本类）/ GPS（罗盘类）。

### 12.9 联盟警察正式夹克 — Coalition Police Official Jacket

- **路径**: `/obj/item/clothing/suit/armor/vest/hc_police_jacket/suit`
- **EN**: "A black uniform jacket with Zvirdnyan Colonial Militia's signature white rectangle on its right sleeve and backside. Letters inside the collar read: %RANK-%KINK. The jacket is of exceptional quality."
- **CN**: 黑色制服夹克，右袖与背面带兹维尔丁恩殖民地民兵的标志性白色矩形。领内字母写着：%RANK-%KINK。夹克品质出众。
- **随机化**: 衣领内编号随机生成——军衔 `RANK`：POF / LTN / SGT / DET / CPT / MSL（分别对应 警员/中尉/中士/探员/上尉/军士长）；`KINK`：20% 概率为 JFR / 2JFR / STL / 2STL，否则 "N/A"。

### 12.10 联盟警察棒球帽 — Coalition Police Baseball Cap

- **路径**: `/obj/item/clothing/head/soft/hc_police`
- **EN**: "It's a robust baseball hat in tasteless washed out blue colour. Hey, this one's round!"
- **CN**: 一顶结实的棒球帽，乏味的褪色蓝。嘿，这顶是圆的！
- **属性**: 装甲 `/datum/armor/cosmetic_sec`；剥取延迟 60。

---

## 13. CIN 剩余装甲 (CIN Surplus Armor)

源码路径：`code/surplus_armor.dm`

> CIN（联盟军事力量）剩余装备：CIN 与 SolFed 边境战争前设计、直至 VOSKHOD 动力装甲列装前一直服役的老式装备。通过货运订购时**多数会以随机"迷彩"配色生成**。

### 13.0 配色定义与共享装甲

| 迷彩 | 主色 | 副色（COMPLIMENT） |
|---|---|---|
| 冬季 CIN_WINTER | `#bbbbc9` | `#838392` |
| 山地沙漠 CIN_MOUNTAIN_DESERT | `#aa6d4c` | `#a37e45` |
| 森林 CIN_FOREST | `#6D6D51` | `#474734` |
| 海军 CIN_MARINE | `#51517b` | `#39394d` |
| 邪恶 CIN_EVIL | `#5d5d66` | `#3d3d46` |

**共享装甲** `/datum/armor/cin_surplus_armor`（注释：对子弹与创伤防护扎实，但激光防护薄弱——本就不是为防激光设计）：

| melee 近战 | bullet 子弹 | laser 激光 | energy 能量 | bomb 爆炸 | fire 火焰 | acid 酸蚀 | wound 创伤 |
|---|---|---|---|---|---|---|---|
| 30 | 40 | 10 | 10 | 40 | 50 | 50 | 20 |

### 13.1 GZ-03 战斗头盔 — GZ-03 Combat Helmet

- **路径**: `/obj/item/clothing/head/helmet/cin_surplus_helmet`
- **EN**: "An outdated service helmet previously used by CIN military forces. The design dates back to the years leading up to CIN - SolFed border war, and was in service until the advent of VOSKHOD powered armor becoming standard issue."
- **CN**: 曾服役于 CIN 军事力量的过时制式头盔。设计可追溯至 CIN–SolFed 边境战争爆发前数年，一直服役到 VOSKHOD 动力装甲成为标准装备为止。
- **属性**: 装甲 `cin_surplus_armor`；支持口鼻部变体（`CLOTHING_SNOUTED_VARIATION_NO_NEW_ICON`）。
- **随机配件**（初始化时按权重抽取图标后缀）：
  - 素面 plain（权重 15）——不遮眼
  - 下巴带 strap（权重 10）——不遮眼
  - 玻璃护目镜 glass（权重 10）——**遮眼（HEADCOVERSEYES）**
  - 两者都要 both（权重 5）——**遮眼（HEADCOVERSEYES）**
- **变体**: `/desert`（山地沙漠色）、`/forest`（森林色）、`/marine`（海军色）、`/random_color`（从 5 种迷彩中随机）。
- 深度检视含设定文本（examine_more）。

### 13.2 CIN 战斗制服 — CIN Combat Uniform

- **路径**: `/obj/item/clothing/under/syndicate/rus_army/cin_surplus`
- **EN**: "A CIN designed combat uniform that can come in any number of camouflauge variations. Despite this particular design being developed in the years leading up to the CIN-SolFed border war, the uniform is still in use by many member states to this day."
- **CN**: CIN 设计的战斗制服，可有任意数量的迷彩变体。尽管该设计在 CIN–SolFed 边境战争前数年才定型，至今仍被许多成员国使用。
- **属性**: 三色灰阶（两肢色 + 躯干深色 `#34343a`）；有传感器（`HAS_SENSORS`）；**玩家可染色（`IS_PLAYER_COLORABLE_1`）**。
- **变体**: `/desert` `#aa6d4c#aa6d4c#34343a`、`/forest` `#6D6D51#6D6D51#34343a`、`/marine` `#51517b#51517b#34343a`、`/random_color`（四肢从 4 色中随机两次 + 躯干固定 `CIN_EVIL_COLORS`）。

### 13.3 GZ-03 装甲背心 — GZ-03 Armor Vest

- **路径**: `/obj/item/clothing/suit/armor/vest/cin_surplus_vest`
- **EN**: "An outdated armor vest previously used by CIN military forces. The design dates back to the years leading up to CIN - SolFed border war, and was in service until the advent of VOSKHOD powered armor becoming standard issue."
- **CN**: 曾服役于 CIN 军事力量的过时装甲背心。设计可追溯至 CIN–SolFed 边境战争爆发前数年，一直服役到 VOSKHOD 动力装甲列装为止。
- **属性**: 装甲 `cin_surplus_armor`；**30% 概率生成加装外插板版**（`vest_extra`，覆盖 胸部+裆部），否则基础版（`vest_basic`，仅覆盖胸部）。

### 13.4 CIN 胸挂 — CIN Chest Rig（战术织带）

- **路径**: `/obj/item/storage/belt/military/cin_surplus`
- **描述**: "A tactical webbing often used by the CIN's military forces." / CIN 军事力量常用的战术织带。
- **属性**: 存储类型 `/datum/storage/loadout_belt`（配装腰带式存储）；**玩家可染色**。
- **变体**: `/desert`、`/forest`、`/marine`、`/random_color`（5 种副色随机）。

### 13.5 CIN 军用背包 — CIN Military Backpack

- **路径**: `/obj/item/storage/backpack/industrial/cin_surplus`
- **EN**: "A rugged backpack often used by the CIN's military forces."
- **CN**: CIN 军事力量常用的耐用背包。
- **变体**: `/desert`、`/forest`、`/marine`、`/random_color`（5 种副色随机）。

---

## 14. MOD 装甲 (MOD Suits)

源码路径：`code/mod_suit.dm`

### 14.1 主题：边境世界督察 — Rim-World Inspector

- **路径**: `/datum/mod_theme/rim_inspector`
- **EN**: "A Heliostatic Coalition Internal Affairs-Apadyne joint border patrol suit. Designed for maximum protection in frontier environments where medical support is scarce."
- **CN**: 赫利奥静态联盟内务部与 Apadyne 联合研制的边境巡逻服。专为缺乏医疗支援的边境环境提供最大防护而设计。
- **装甲** `/datum/armor/mod_theme_rim_inspector`：

| melee | bullet | laser | energy | bomb | bio 生物 | fire | acid | wound |
|---|---|---|---|---|---|---|---|---|
| 40 | 50 | 30 | 30 | 60 | 100 | 75 | 75 | 20 |

- **属性**: 复杂度上限 `DEFAULT_MAX_COMPLEXITY - 1` = **14**；耗电率 `DEFAULT_CHARGE_DRAIN × 1.5` = **75 J/s**（标准 50 J/s 的 1.5 倍）。
- **允许存入装甲储物槽**: 手电 / 内置气罐 / 弹药盒 / 弹壳 / 手铐 / 闪光弹 / 警棍 / 战斗刀 / 防暴盾 / 枪械。
- **设计设定（extended_desc）**: 多层复合装甲（塑料钢 / 自修复陶瓷 / CNT 编织防碎片衬里），正面防护可挡从海盗轻武器（含 .60 SAPHEI）到研究站暴走炮兵的炮弹破片；厚实面甲内置单摄像头系统，置于超厚染色等离子玻璃后，直击也不会失去视力。联盟认为把巡逻员改造成"行走堡垒"比给他们装 Voskhod 式再生系统更划算。
- **密封特性**: 头盔密封后 隐藏面部毛发/面具/耳朵/眼睛/脸/头发/口鼻部 + 遮盖口眼 + 防胡椒喷雾；胸甲/手套/靴子密封后 `STOPSPRESSUREDAMAGE`（抗压差），手套/靴子可外叠穿戴（`CAN_OVERSLOT`）。

### 14.2 预装配边境督察 MOD — Pre-Equipped Rim Inspector

- **路径**: `/obj/item/mod/control/pre_equipped/rim_inspector`
- **预装模块**（applied_modules，7 个）：大容量储物 / 热调节器 / **运行状态读出模块（operational）** / 系绳（tether）/ 手电 / 纸张分发器 / 磁力挂载（magnetic_harness）
- **默认引脚**（default_pins）：系绳（tether）、磁力靴（magboot）
- 制造商：ZCM。

### 14.3 MOD 运行状态读出模块 — Operational Status Readout Module

- **路径**: `/obj/item/mod/module/status_readout/operational`
- **EN**: "A once-common module, this technology unfortunately went out of fashion in the safer regions of space; however, it remained in use everywhere else. This particular unit hooks into the suit's spine, capable of capturing and displaying all possible biometric data of the wearer; sleep, nutrition, fitness, fingerprints, and even useful information such as their overall health and wellness. The vitals monitor also comes with a speaker, loud enough to alert anyone nearby that someone has, in fact, died. This specific unit has a clock and operational ID readout."
- **CN**: 一种曾经常见的模块，可惜在太空安全区域已过时；但在其他地方仍在使用。本件接入装甲脊柱，可采集并显示穿戴者全部生物特征数据：睡眠、营养、体能、指纹，乃至整体健康状况等实用信息。生命体征监视器还带扬声器，音量足以让附近所有人知道"有人确实死了"。本件额外带时钟与执勤 ID 显示。
- **属性**: `display_time = TRUE`（显示时间）；死亡音效 `flatline.ogg`（心电停跳音）。
- **`/voskhod` 变体**: `removable = FALSE`（不可拆卸，Voskhod 原装）。

### 14.4 MOD 自动医疗模块 — Automatic Paramedical Module (Auto-Doc)

- **路径**: `/obj/item/mod/module/auto_doc`
- **EN**: "The reverse-engineered and redesigned medical assistance system, previously used by the now decommissioned Voskhod combat armor. The technology it uses is very similar to the one of the N-URSEI suites, yet miniaturised and lacking self-synthesis capabilities. Using a built-in storage of chemical compounds and a miniature chemical mixer, it's capable of injecting its user with a plethora of drugs, assisting them with their restoration. However, this system heavily relies on some rarely combat-available chemical compounds to prepare its injections, mainly Protozine, which appear in the user's bloodstream from time to time, and its trivial damage assessment systems are prone to kicking in only when you're moderately wounded."
- **CN**: 逆向工程并重新设计的医疗辅助系统，曾用于现已退役的 Voskhod 战斗装甲。其技术非常接近 N-URSEI 套件，但已小型化且缺乏自我合成能力。利用内置化合物储存与微型化学混合器，可向使用者注射多种药物辅助恢复。但该系统严重依赖战斗中难以获取的化合物（主要是 Protozine 原蛋白素，有时会出现在使用者血液中），且其简陋的伤情评估系统往往只在中等伤势时触发。
- **属性**:
  - 模块类型 `MODULE_TOGGLE`（开关式）；复杂度 **4**；**不可拆卸**（`removable = FALSE`）
  - 每次激活耗电 `DEFAULT_CHARGE_DRAIN × 10` = **500 J**
  - 燃料：**Protozine 原蛋白素**；每次补充需 10u；储量上限 **100u**
  - 治疗阈值：生命值 < **85** 才启动；各治疗冷却 **20 秒**
  - 治疗效果（每 tick 2.5u 对应药物）：
    - 血量不足（< BLOOD_VOLUME_OKAY）：注入 25u 血液 + 凝血剂
    - 缺氧（无沙丁胺醇时）：沙丁胺醇 salbutamol
    - 挫伤（无 sal_acid 时）：水杨酸 sal_acid + 矿用软膏 mine_salve
    - 烧伤（无 oxandrolone 时）：氧甲氢龙 oxandrolone + 矿用软膏
    - 毒素（无 pen_acid 时）：青霉酸 pen_acid
    - 耐力透支（> 85，无可卡因时）：吗啡 + 可卡因
  - **EMP 故障**: 受 EMP 触发 `heal_aftereffects`——以 5%（满原蛋白素）~ 20%（低/空）概率向使用者自发注射最多 10u 原蛋白素
  - 兼容性：与肾上腺素增强/自动医疗/胡椒肩/罪犯捕捉/矿石袋/钻头/夹具/健康分析仪/快速搬运/建造器/注射器/整理器/病人运输/拆线器/手术处理器/快速铐/灰烬沉积 等模块**互斥**
  - 补给方式：用开口容器（如原蛋白素瓶）点击模块注入。

### 14.5 原蛋白素瓶 — Bottle of Protozine

- **路径**: `/obj/item/reagent_containers/cup/glass/waterbottle/large/protozine`
- **名称**: "bottle of protozine" / 原蛋白素瓶
- **描述**: "Nothing screams 'Budget cuts' like a plastic bottle of autodoc refills." / 没有什么比一瓶塑料装的自动医疗补充液更能说明"预算削减"了。
- **内容**: **100u Protozine**（`/datum/reagent/medicine/omnizine/protozine`）。

### 14.6 Voskhod 停能装甲 MOD 改装套件 — Voskhod Refit Kit

- **路径**: `/obj/item/crafting_conversion_kit/voskhod_refit`
- **名称**: "Voskhod depowered armor MOD refit kit" / 沃斯霍德停能装甲 MOD 改装套件
- **EN**: "A metallic case of various tubes, sensors and spare materials required to reuse Voskhod's components in the making of a next-generation MODed version."
- **CN**: 一个金属箱，装有复用 Voskhod 组件制造下一代 MOD 版本所需的各种导管、传感器与备用材料。
- **属性**: 伤害 `force = 10`。

### 14.7 合成配方：停能 Voskhod → 翻新 MOD 转换

- **路径**: `/datum/crafting_recipe/voskhod_to_mod`
- **名称**: "Depowered Voskhod-To-Refurbished Voskhod MOD Conversion" / 停能 Voskhod 到翻新 Voskhod MOD 的转换
- **需求**:
  1. 1× Voskhod 停能战斗装甲（`/obj/item/clothing/suit/space/voskhod`）
  2. 1× Voskhod 停能战斗头盔（`/obj/item/clothing/head/helmet/space/voskhod`）
  3. 1× Voskhod 改装套件（`/obj/item/crafting_conversion_kit/voskhod_refit`）
  4. 1× CIN 军用背包（`/obj/item/storage/backpack/industrial/cin_surplus`）
  5. 1× MOD 核心（`/obj/item/mod/core`）
  6. 1× 高容量电池（`/obj/item/stock_parts/power_store/cell/high`）
  7. 10× 塑料钢（plasteel）
  8. 15× 电缆（cable_coil）
  9. 1× 健康组件（`/obj/item/assembly/health`）
- **工具**: 焊枪 + 万能工具（`TOOL_WELDER, TOOL_MULTITOOL`）；耗时 **30 秒**；分类 `CAT_CLOTHING`；跳过材料对等校验。
- **产物**: `/obj/effect/spawner/random/voskhod_refit` → 生成 **1× `/obj/item/mod/control/pre_equipped/voskhod`**（"MODskhod"，即 Voskhod 主题 MOD 装甲，主体定义在本模块之外）。

---

## 15. 机甲 (Mecha)

源码路径：`code/mech.dm`

### 15.1 M/TACS-1-LF "典狱官" — M/TACS-1-LF "Warden"

- **路径**: `/obj/vehicle/sealed/mecha/warden`
- **EN**: "A frontier-optimized combat exosuit and the product of a rare collaboration between Kemppainen-Morozov Industrial Fabrication and Szot Dynamica. KMIF's unshakable chassis provides Durand-level resilience, while SŻD's responsive myomer systems grant it Gygax-like agility. Stripped of complex jump jets and grapples for ease of maintenance, it excels as a mobile firing platform and boarding-assault anchor."
- **CN**: 为边境优化的战斗外骨骼，是 Kemppainen-Morozov 工业制造（KMIF）与 Szot Dynamica 罕见合作的产物。KMIF 坚不可摧的底盘提供杜兰德级（Durand）韧性，SŻD 灵敏的人造肌系统赋予其吉加克斯级（Gygax）敏捷。为便于维护拆除了复杂的跳跃喷口与抓钩，擅长充当机动火力平台与登舰突击锚点。
- **属性**:
  - 移动延迟 `movedelay = 3.5`；**最大结构 325**；撞击力 `force = 35`
  - 出舱延迟 4 秒；类型 `EXOSUIT_MODULE_COMBAT`（战斗模块）
  - 装备槽：左臂 1 / 右臂 1 / 通用 2 / 动力 1 / 装甲 2
  - 乘客动作：烟雾弹（mech_smoke）、缩放（mech_zoom）
  - 残骸：`/obj/structure/mecha_wreckage/warden`（"Warden wreckage"，焊枪可回收钛/铁/金属棒）
- **装甲** `/datum/armor/warden`：

| melee | bullet | laser | energy | bomb | fire | acid |
|---|---|---|---|---|---|---|
| 65 | 60 | 45 | 35 | 40 | 100 | 100 |

- **`/loaded` 满配变体**: 左臂 = 熔炉制造炮（Forge cannon），右臂 = 斯特雷勒等离子机枪（Zaibas LMG）；部件：**超级电池（super cell）** + 相位扫描模块 + 超级电容 + 皮可伺服。

### 15.2 M/TACS-1A-LF "仲裁者"（WUNK）— M/TACS-1A-LF "Arbiter" (WUNK)

- **路径**: `/obj/vehicle/sealed/mecha/warden/wunk`
- **EN**: "The Warden Urban Necessity Kit transforms the already formidable frontier combat exosuit into a dedicated urban-breaching asset. Reinforced chassis now incorporates supplemental armor pauldrons, a groin plate, and a rear skirt, while myomer systems are recalibrated to compensate. Its sensor suite provides wall-penetrating radar and 360° awareness. Heavier and slower than its predecessor, but unmatched in shipboard assaults and built-up environments."
- **CN**: 典狱官城市必要套件（WUNK）将本就强大的边境战斗外骨骼改造为专门的城区破门资产。强化底盘现在包含附加装甲肩甲、裆甲板与后裙甲，人造肌系统重新校准以补偿。传感器套件提供穿墙雷达与 360° 态势感知。比前身更重更慢，但在舰船突击与建筑密集环境中无出其右。
- **属性**:
  - 移动延迟 `movedelay = 3.75`；**最大结构 375**
  - 装备槽：左臂 1 / 右臂 1 / 通用 2 / 动力 1 / **装甲 1**（较典狱官少 1）
  - **热视觉**: 乘员（人类或 MMI 脑）进入即获得 `TRAIT_THERMAL_VISION`，离开时移除
  - 残骸：`/obj/structure/mecha_wreckage/warden/wunk`（"Arbiter wreckage"）
- **装甲** `/datum/armor/warden_wunk`：

| melee | bullet | laser | energy | bomb | fire | acid |
|---|---|---|---|---|---|---|
| 75 | 70 | 55 | 45 | 55 | 100 | 100 |

- **`/loaded` 满配变体**: 左臂 = 塞克利斯自动炮荚（gunpod），右臂 = 斯米尔加斯反坦克制导火箭巢（DAGR）；通用 = 维修机器人 + 离子推进器；动力 = 发电机；装甲 = 反 EMP 装甲增强器；部件：**蓝空间电池（bluespace cell）** + 三相位扫描模块 + 二次方电容 + 飞秒伺服。

### 15.3 WUNK 转换套件 — Warden Urban Necessity Kit (WUNK) Conversion

- **路径**: `/obj/item/mecha_parts/mecha_equipment/wardenupgrade`
- **名称**: "Warden Urban Necessity Kit (WUNK) Conversion" / 典狱官城市必要套件（WUNK）转换
- **EN**: "A comprehensive modernization package for the M/TACS-1-LF 'Warden' exosuit. The WUNK (Warden Urban Necessity Kit) conversion adds supplemental armor pauldrons, a groin plate, and rear skirt protection, the Argus-U distributed aperture sensor suite with wall-penetrating radar, and the distributed frontal camera system. The conversion is non-reversible and requires an open maintenance panel and an unoccupied mech with a cell installed. Slightly reduces top speed in exchange for dramatically improved survivability in urban and shipboard environments."
- **CN**: 面向 M/TACS-1-LF "典狱官"外骨骼的全面现代化套件。WUNK 转换添加附加装甲肩甲、裆甲板与后裙甲防护、带穿墙雷达的 Argus-U 分布式孔径传感器套件与分布式前向摄像系统。转换不可逆，需要打开维护面板、机甲内无人且已装电池。以略微降低最高速度为代价，大幅提升城市与舰船环境中的生存能力。
- **使用条件**（can_attach）：目标必须是典狱官（不能是仲裁者）；维护面板已打开；舱内无乘员；已装电池；装甲槽装备 ≤1 件。
- **转换过程**（attach）：生成仲裁者，完整转移电池/扫描模块/电容/伺服/全部装备（保留左右手区分）/DNA 锁/机旗/横移模式/自定义名称，按比例转移结构值（integrity），旧机甲不产生残骸，播放棘轮音效。

---

## 16. 机甲武器 (Mecha Weapons)

源码路径：`code/mech_weapons.dm`

弹药类型宏：`MECHA_AMMO_CANNON = "Cannon fodder"`（炮食）、`MECHA_AMMO_MISSILE_DAGR = "Semi-guided rocket"`（半制导火箭）、`MECHA_AMMO_AUTOCANNON = "35mm Multipurpose"`（35mm 多用途）。

### 16.1 M/FC-8-LF "熔炉"制造炮 — M/FC-8-LF "Forge" Fabrication Cannon

- **路径**: `/obj/item/mecha_parts/mecha_equipment/weapon/ballistic/cannon`
- **EN**: "A heavy 76mm cannon for mechs, integrated with an onboard nanoforge, produced by KMIF. It fabricates specialized rounds on-demand from a generic fodder canister, allowing for sustained fire support without conventional ammunition logistics. The system automatically identifies targets and selects the optimal round type."
- **CN**: KMIF 生产的 76mm 重型机甲炮，集成车载纳米熔炉。可从通用"炮食"料罐按需制造专用弹药，无需常规弹药后勤即可持续火力支援。系统自动识别目标并选择最优弹种。
- **属性**:
  - 射击冷却 `equip_cooldown = 2 SECONDS`；装填时间 `loading_time = 2 SECONDS`
  - 弹舱 **7 发**；缓存 **28 发**（`projectiles_cache = 28`，上限 28）
  - 发射音 `shell_out_med.ogg`、装填音 `shell_in_med.ogg`、伺服音 `servo.ogg`
  - 弹药类型：炮食（Cannon fodder）
- **智能选弹机制**（独特机制）：开火前先 `scan_area` 扫描目标 3×3 区域：
  - 有碳基/基础生物（`SOFT TARGET` 软目标）→ **爆炸破片弹**（Explosive Fragmentation）
  - 有机甲/硅基（`HARD TARGET` 硬目标）→ **电击脱壳弹**（Electric Penetrator）
  - 无目标 → **区域封锁腐蚀烟雾弹**（AREA DENIAL → Caustic Obscurant）
  - 指挥官式呼叫流程：TARGET → FORGE SPOOLING → FORGE ACTIVE → ROUND READY → [弹种] UP!，全程对乘员播报，总计约 4 秒沉浸式延迟。

### 16.2 M/HP-22 "斯特雷勒"同轴等离子脉冲机枪 — M/HP-22 "Strele" Coaxial Plasma Pulse Machinegun

- **路径**: `/obj/item/mecha_parts/mecha_equipment/weapon/energy/zaibas_lmg`
- **EN**: "A weapon for combat exosuits, produced by KMIF-SŻD joint initiative. Shoots assault rifle caliber-proportionate plasma-saboted tungsten penetrators. The preloaded amounts of plasma and tungsten allow it to sustain fire indefinitely within regular combat scenarios."
- **CN**: KMIF–SŻD 联合研制的战斗外骨骼武器。发射与突击步枪口径相当的等离子脱壳钨穿甲弹。预装的等离子与钨储量使其在常规战斗场景中可无限持续射击。
- **属性**: 冷却 10；**每发 4 个投射物**（`projectiles_per_shot = 4`）；散布 `variance = 5`；随机扩散 `randomspread = 1`；弹间隔 `projectile_delay = 2`；**每次射击耗能 250 J**；投射物 `/obj/projectile/bullet/pulse`（脉冲弹）；`harmful = TRUE`。

### 16.3 M/AC-41 "塞克利斯"自适应自动炮荚 — M/AC-41 "Seklys" Flex-Mount Autocannon Gunpod

- **路径**: `/obj/item/mecha_parts/mecha_equipment/weapon/ballistic/gunpod`
- **EN**: "A weapon for combat exosuits, produced by KMIF. Shoots a rapid, three shot burst of 35mm multipurpose shells. Jettisons itself automatically if it ever runs dry."
- **CN**: KMIF 生产的战斗外骨骼武器。以 35mm 多用途炮弹快速三连发。弹药耗尽时自动弹射弃置自身。
- **属性**: 冷却 12；弹量 **150 发**；**每轮三连发**（`projectiles_per_shot = 3`）；弹间隔 4；散布 2；`harmful = TRUE`；弹药类型 35mm 多用途；发射音 `amr_fire.ogg`。
- **机制**: 弹尽后向乘员播报 "Gunpod exhausted. Ejecting." 并自动 `detach()` 弃置。

### 16.4 M/RP-66 "斯米尔加斯"反坦克制导火箭巢 — M/RP-66 "Smilgas" Anti-Tank Guided Rocket Pod

- **路径**: `/obj/item/mecha_parts/mecha_equipment/weapon/ballistic/missile_rack/dagr`
- **EN**: "A weapon for combat exosuits, produced by KMIF. Launches anti-tank guided missiles with optical and infrared guidance designed to lock-on after launch and track targets autonomously. Due to the nature of their design, turn rates remain subpar, and its design will do nothing to soft targets."
- **CN**: KMIF 生产的战斗外骨骼武器。发射带光学与红外制导的反坦克制导导弹，发射后锁定并自主追踪目标。由于设计特性，转弯率欠佳，且对软目标毫无作用。
- **属性**: 火箭 **7 枚**；弹药类型 半制导火箭；投射物 `/obj/projectile/bullet/rocket/pep/dagr`（"precision guided rocket" 精确制导火箭，`homing_turn_speed = 10`，初始化即开启追踪 `homing = TRUE` 并锁定初始目标）。

### 16.5 熔炉炮弹药 — Forge Cannon Projectiles

| 弹种 | 路径 | 名称 | 伤害 | 特殊属性 |
|---|---|---|---|---|
| 基类 | `/obj/projectile/bullet/tank_cannon` | nonexistent tank shell | **60** | 通用坦克炮弹（基类） |
| 腐蚀烟雾弹 | `.../tank_cannon/smoke` | corrosive smoke shot / 腐蚀烟雾弹 | **20** | 命中非矿物地面时生成**酸液化学烟雾**（半径 1.25，酸 45u） |
| 破片弹 | `.../tank_cannon/heap` | heap shot / 破片弹 | **20** | 速度 1.25；射程 11；引爆：爆炸（重创 1 / 轻创 3 / 火焰 2 / 闪光 1）+ 弹片（`shrapnel/short_range/piercing`：**穿甲 15、穿透 1、可穿全部**，半径 7）；落地或命中均引爆 |
| 电击脱壳弹 | `.../tank_cannon/sabot` | electric sabot shot / 电击脱壳穿甲弹 | **45** | 速度 2.5；**穿甲 50、穿透 2、可穿全部**；对机甲额外 **85 反装甲伤害**（穿甲 50）；对硅基 75% 整体 + 25% 燃烧伤害；对生物触发 **30 级电击**（无手套/无眩晕标志）；火花特效 |

### 16.6 自动炮荚弹药与火箭 — Gunpod Ammo & Rockets

| 投射物 | 名称 | 伤害 | 特殊属性 |
|---|---|---|---|
| `/obj/projectile/bullet/autocannon` | 35mm multipurpose autocannon shell / 35mm 多用途炮弹 | **45** | **穿甲 35**；命中引发小型爆炸（轻创 1 / 火焰 1 / 闪光 1，无声） |
| `/obj/projectile/bullet/rocket/pep/dagr` | precision guided rocket / 精确制导火箭 | — | 追踪转弯速度 10；发射即锁敌追踪 |

### 16.7 机甲弹药容器 — Mech Ammo Containers

| 容器 | 名称 | 弹量 | 备注 |
|---|---|---|---|
| `/obj/item/mecha_ammo/cannon` | fabricator canister container / 制造器料罐容器 | **28 发** | 供熔炉炮使用的"炮食"料罐 |
| `/obj/item/mecha_ammo/dagr` | guided rocket container / 制导火箭容器 | **7 发** | 可直接装载（`direct_load = TRUE`），装填音效弹匣插入声 |

---

## 17. 配装与套装组合 (Loadouts & Sets)

> 本模块未定义独立 `/datum/outfit`，配装以"预装填物品 + 满配机甲 + 预装配件"方式实现，以下为全量组合。

### 17.1 ERT 士兵系配装（腰带预装填）

| 角色 | 腰带变体 | 弹药 | 通用件 |
|---|---|---|---|
| 士兵 Soldier | `/nri/soldier` | 4× Lanca 弹匣 | 战斗刀 + 烟雾弹 + 破片手雷 |
| 重装 Heavy | `/nri/heavy` | 4× Stechkin APS 弹匣 | 战斗刀 + 烟雾弹 + 破片手雷 |
| 队长 Captain | `/nri/captain/full` | 4× Lanca 弹匣 | 战斗刀 + 烟雾弹 + 破片手雷 |
| 医护 Medic | `/nri/medic/full` | 4× Miecz 弹匣 | 战斗刀 + 烟雾弹 + 破片手雷 |
| 工程师 Engineer | `/nri/engineer/full` | 4× Miecz 弹匣 | 战斗刀 + 烟雾弹 + 破片手雷 |
| 督察 Inspector | `/nri/plus_mre` | — | 1× HC 生存包 |

### 17.2 单兵标准套件（按角色对应颜色）

| 部位 | 士兵 | 队长 | 医护 | 工程师 |
|---|---|---|---|---|
| 背包 | 绿色帝国突击背包 | 黑色 | 白色 | 棕色 |
| 腰带 | 绿色战术腰带 | 黑色 | 蓝色（图标白） | 棕色 |
| 制服 | 先进帝国作战服（nri_soldier） | nri_captain | nri_medic | nri_engineer |
| 弹药箱 | Sakhno / Lanca / Miecz / L6 SAW / APS 任选（每箱 7 份） | 同左 | 同左 | 同左 |

### 17.3 HC 生存包（随身应急组合）

氧气蜡烛 + 双联紧急氧气罐 + 1,000 信用点现金 + 铁片药瓶 + 殖民地口粮 + 9 支医疗笔（急救/兴奋剂/氧甲氢龙/水杨酸×2/青霉酸/沙丁胺醇/阿托品/失血）+ 7 支红色信号棒 + 红色撬棍。

### 17.4 机甲满配组合

| 机甲 | 左臂 | 右臂 | 通用/动力/装甲 | 部件 |
|---|---|---|---|---|
| 典狱官 Warden（loaded） | M/FC-8-LF 熔炉制造炮 | M/HP-22 斯特雷勒等离子机枪 | — | 超级电池 / 相位扫描 / 超级电容 / 皮可伺服 |
| 仲裁者 Arbiter（loaded） | M/AC-41 塞克利斯自动炮荚 | M/RP-66 斯米尔加斯制导火箭巢 | 维修机器人 + 离子推进器 / 发电机 / 反 EMP 装甲 | 蓝空间电池 / 三相位扫描 / 二次方电容 / 飞秒伺服 |

升级路径：典狱官 + WUNK 转换套件（面板打开、舱内无人、有电池、装甲槽 ≤1 件）→ 仲裁者（不可逆，全部组件/装备/命名/结构按比例转移）。

### 17.5 边境世界督察 MOD（预装配件）

大容量储物 + 热调节器 + 运行状态读出（时钟/ID/死亡警报）+ 系绳 + 手电 + 纸张分发器 + 磁力挂载；默认引脚：系绳 + 磁力靴。可加装自动医疗模块（Auto-Doc，需 Protozine 原蛋白素供能）。

### 17.6 警察制服套装（联盟警察 / HC 警察）

制服或裙装 + 斗篷 + 大檐帽 + 战术面具（防胡椒喷雾）+ 塑料钢头盔 + 插板背心 + 飞行夹克/正式夹克 + 棒球帽（全套 10 件，制造商 ZCM）。

### 17.7 制作/转换配方汇总

| 配方 | 需求 | 工具/耗时 | 产物 |
|---|---|---|---|
| 自制电击刀 Makeshift Stunknife | 生存战斗刀 + Kopřiva 电击枪 | 螺丝刀 / 10 秒 | 满电电击刀 |
| Voskhod → MODskhod 转换 | Voskhod 装甲+头盔 + 改装套件 + CIN 背包 + MOD 核心 + 高电池 + 10 塑料钢 + 15 电缆 + 健康组件 | 焊枪+万能工具 / 30 秒 | 预装 Voskhod 主题 MOD 装甲 |

---

## 18. 数值速查表 (Cheat Sheet)

### 18.1 货币面值

| 货币 | 面值（刨屑） | 相对马克 | 材质/备注 |
|---|---|---|---|
| 刨屑 Shaving | 1 | 1/128 马克 | 六角形碳化钨片，0.3g，堆叠 128 |
| 比特 Bit | 4 | 1/32 马克 | 八边形，KMIF"K"章 |
| 公会马克 Guild Mark | 128 | 1 马克 | 2cm 锯齿圆币，联盟徽 |
| 王冠 Crown | 4,096 | 32 马克 | 3cm 中孔红带，穿绳携带 |
| 熔炉马克 Forge Mark | 16,384 | 128 马克 | 5×3×1cm 条，量子芯片 |

### 18.2 本模块定义的装甲数据

| 装甲数据 | melee | bullet | laser | energy | bomb | bio | fire | acid | wound |
|---|---|---|---|---|---|---|---|---|---|
| `sec_nri`（贝雷帽） | 40 | 35 | 30 | 40 | 25 | — | 20 | 50 | 20 |
| `clothing_under/costume_nri`（帝国作战服） | 10 | — | — | — | — | — | 30 | 30 | — |
| `cin_surplus_armor`（CIN 剩余） | 30 | 40 | 10 | 10 | 40 | — | 50 | 50 | 20 |
| `mod_theme_rim_inspector`（督察 MOD） | 40 | 50 | 30 | 30 | 60 | 100 | 75 | 75 | 20 |
| `warden`（典狱官机甲） | 65 | 60 | 45 | 35 | 40 | — | 100 | 100 | — |
| `warden_wunk`（仲裁者机甲） | 75 | 70 | 55 | 45 | 55 | — | 100 | 100 | — |

### 18.3 引用的上游装甲数据（供参考）

| 装甲数据 | melee | bullet | laser | energy | bomb | bio | fire | acid | wound | 用于 |
|---|---|---|---|---|---|---|---|---|---|---|
| `space_syndicate` | 40 | 50 | 30 | 40 | 30 | 30 | 80 | 85 | — | Voskhod 装甲/头盔 |
| `clothing_under/rank_security` | 10 | — | — | — | — | — | 30 | 30 | 10 | 警察制服 |
| `armor_secjacket` | 25 | 25 | 25 | 35 | 20 | — | 30 | 30 | 5 | 警察飞行夹克 |
| `cosmetic_sec` | 30 | 25 | 25 | 35 | 25 | — | 20 | 50 | 5 | 警察帽/棒球帽 |

### 18.4 关键常量换算

| 常量 | 值 | 应用 |
|---|---|---|
| `STANDARD_CELL_CHARGE` | 10 kJ | 曲柄电池 4 kJ（40%）；曲柄充能 500 J/秒；电击枪每次耗电 7.5 kJ（75%） |
| `DEFAULT_CHARGE_DRAIN` | 50 J/s | 督察 MOD 耗电 75 J/s（1.5×）；Auto-Doc 每次 500 J（10×） |
| `DEFAULT_MAX_COMPLEXITY` | 15 | 督察 MOD 复杂度上限 14 |
| `COIN_MATERIAL_AMOUNT` | 20 | 比特 20 钛；刨屑 2 钛（0.1×） |
| `SMALL_MATERIAL_AMOUNT` | 10 | 马克 10 钛；王冠 40 钛（4×）；熔炉马克 160 钛（16×） |
| `SHEET_MATERIAL_AMOUNT` | 100 | 电击刀 loaded 铁 600（6×） |

---

*数据来源：TianGuan13 / NovaSector 分支 `modular_nova/modules/novaya_ert/`（17 个 .dm，1,935 行）。所有数值均直接取自源码。*
