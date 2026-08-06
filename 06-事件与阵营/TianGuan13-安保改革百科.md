# TianGuan13 安保改革百科

> **模块 (Module)**：GoofSec —— "Fixing Security for Nova"（为 Nova 修复安保体系）
> **源码路径 (Source)**：`modular_nova/modules/goofsec/`
> **规模 (Scale)**：7 个 `.dm` 文件，共 **2,816 行**
> **作者 (Credits)**：Iamgoofball
> **文档说明**：本百科全量收录 goofsec 模块全部内容——部门警卫系统、手机执法系统、储物柜改革、配装、SolFed 911 应急响应系统及全部服装装备。数值均与源码逐字核对。

## 目录 (Table of Contents)

- [模块总览](#模块总览-module-overview)
- [1. 部门警卫系统 Department Guards](#1-部门警卫系统-department-guards)
  - [1.1 设计理念](#11-设计理念-design-notes)
  - [1.2 制服 UNIFORMS](#12-制服-uniforms)
  - [1.3 颈部饰品 NECK](#13-颈部饰品-neck)
  - [1.4 外套与防弹衣 SUITS](#14-外套与防弹衣-suits)
  - [1.5 头部装备 HEAD](#15-头部装备-head)
  - [1.6 腰带 BELT](#16-腰带-belt)
  - [1.7 手套 GLOVES](#17-手套-gloves)
  - [1.8 出生点 LANDMARKS](#18-出生点-landmarks)
  - [1.9 科学警卫 Science Guard](#19-科学警卫-science-guard)
  - [1.10 秩序员 Orderly](#110-秩序员-orderly)
  - [1.11 工程警卫 Engineering Guard](#111-工程警卫-engineering-guard)
  - [1.12 海关专员 Customs Agent](#112-海关专员-customs-agent)
  - [1.13 保安 Bouncer](#113-保安-bouncer)
  - [1.14 部门电击棒 Departmental Batons](#114-部门电击棒-departmental-batons)
  - [1.15 补给箱 Supply Packs](#115-补给箱-supply-packs)
  - [1.16 服装袋 Garment Bags](#116-服装袋-garment-bags)
- [2. 手机执法系统 Cellphone Law Enforcement](#2-手机执法系统-cellphone-law-enforcement)
- [3. 储物柜改革 Lockers Reform](#3-储物柜改革-lockers-reform)
- [4. 配装与杂项 Outfits & Misc](#4-配装与杂项-outfits--misc)
  - [4.1 配装 Outfits](#41-配装-outfits)
  - [4.2 杂项 solfed_bloat](#42-杂项-solfed_bloat)
- [5. SolFed 911 应急响应系统 SolFed 911 Response](#5-solfed-911-应急响应系统-solfed-911-response)
  - [5.1 常量与全局变量](#51-常量与全局变量)
  - [5.2 呼叫流程 call_911](#52-呼叫流程-call_911)
  - [5.3 警长 Marshal](#53-警长-marshal)
  - [5.4 高级大气组 Advanced Atmospherics](#54-高级大气组-advanced-atmospherics)
  - [5.5 急救员 EMT](#55-急救员-emt)
  - [5.6 SWAT 与军事镇压 SWAT & Military](#56-swat-与军事镇压-swat--military)
  - [5.7 SolFed 通讯器 solfed_reporter](#57-solfed-通讯器-solfed_reporter)
  - [5.8 披萨事件 Dogginos Pizza](#58-披萨事件-dogginos-pizza)
  - [5.9 光束撤离工具 Beam-out Tool](#59-光束撤离工具-beam-out-tool)
- [6. SolFed 服装与装备 SolFed Clothing & Gear](#6-solfed-服装与装备-solfed-clothing--gear)
  - [6.1 制服 Uniforms](#61-制服-uniforms)
  - [6.2 护甲数值 Armor Values](#62-护甲数值-armor-values)
  - [6.3 头盔与防弹衣 Helmets & Vests](#63-头盔与防弹衣-helmets--vests)
  - [6.4 配饰与护目镜 Accessories & Goggles](#64-配饰与护目镜-accessories--goggles)
  - [6.5 军衔绶带 Rank Ribbons](#65-军衔绶带-rank-ribbons)
  - [6.6 自动贩卖机与耳机 Vending & Headsets](#66-自动贩卖机与耳机-vending--headsets)

---

## 模块总览 Module Overview

> 源码路径 (Source)：`modular_nova/modules/goofsec/readme.md` + 目录结构

| 文件 (File) | 行数 (Lines) | 内容 (Content) |
|---|---|---|
| `code/department_guards.dm` | 1,274 | 部门警卫系统：5 个警卫职业、全套制服、部门电击棒、服装袋 |
| `code/cellphone.dm` | 61 | 手机执法系统：NOVA 安保手机（黑帮手机复用） |
| `code/lockers.dm` | 75 | 储物柜改革：5 个部门警卫储物柜 + 囚犯储物柜 |
| `code/outfits.dm` | 46 | 配装：SolFed 官方官员配装 |
| `code/solfed_bloat.dm` | 76 | 杂项：Marine 防弹衣/护目镜/头盔/迷彩制服配色 |
| `code/sol_fed.dm` | 878 | SolFed 911 应急响应系统：呼叫流程、Marshal/EMT/Atmos/SWAT/军队、通讯器、光束撤离 |
| `code/solfed_clothing.dm` | 406 | SolFed 服装装备：制服、护甲数值、头盔、绶带、贩卖机、耳机 |
| **合计 (Total)** | **2,816** | |

**模块描述 (Module Description)**：修复 Nova 的安保体系。包含安保枪支的定义、新的部门警卫（Departmental Guards），以及新的 SSC 警长/副警长（SolFed Marshal/Deputies）系统。

**设计理念 (Design Philosophy)**（department_guards.dm 头部注释）：
- 与其给每个部门指派一名安保警员，Nova 各部门改为拥有**自己的盟友警卫**（allied Guards）！
- 大部分相关代码在本文件；制服图标位于相应部门的 `.dmi` 文件中。
- 排序顺序 (SORT ORDER)：**Sci → Generic → Med → Engi → Cargo → Serv**。

---

## 1. 部门警卫系统 Department Guards

> 源码路径 (Source)：`modular_nova/modules/goofsec/code/department_guards.dm`（1,274 行）

### 1.1 设计理念 Design Notes

Nova 不再为各部门派普通安保警员，而是为每个部门配备专属警卫（Departmental Guard），共 **5 个职业**：科学警卫、秩序员、工程警卫、海关专员、保安。他们隶属于各自部门（薪资/部门账户/上司均挂在对应部门），但拥有部分安保权限（ACCESS_SECURITY、ACCESS_BRIG_ENTRANCE、ACCESS_WEAPONS），并使用带**部门区域锁**的电击棒（离开本部门区域只能使用 3 次）。

### 1.2 制服 UNIFORMS

**基础制服（5 件）**：

| 源码路径 (Path) | 英文名 (EN) | 中文 (CN) | 说明 (Notes) |
|---|---|---|---|
| `/obj/item/clothing/under/rank/security/officer/blueshirt/nova` | science guard's uniform | 科学警卫制服 | 与 TG 蓝衫完全相同（含图标）；`/nova` 路径便于排序；`setup_reskins()` 直接 `return`（禁用改色） |
| `/nova/orderly` | orderly uniform | 秩序员制服 | "白色刷手服配灰色裤子。警告：穿着者可能只把希波克拉底誓言当建议。" 图标 `orderly_uniform`，医疗部 `.dmi` |
| `/nova/engineering_guard` | engineering guard uniform | 工程警卫制服 | "加厚的高可见度连体工作服，在安全帽区域内外都管用。" 图标 `engineering_guard_uniform` |
| `/nova/customs_agent` | customs agent uniform | 海关专员制服 | "货运棕短袖衬衫 + 权威炭灰色的工装短裤。只为 FTU 最强壮的手。" 图标 `customs_uniform` |
| `/nova/bouncer` | bouncer uniform | 保安制服 | "短袖加牛仔裤，那股酷劲儿让醉汉乖乖听话。" 图标 `bouncer`，民用 `.dmi` |

**灰阶改色变体（25 件）**——均为 `/obj/item/clothing/under/rank/security/nova/` 下路径，`greyscale_colors` 精确值：

| 类型 (Type) | 部门 (Dept) | 英文名 (EN) | 中文 (CN) | 颜色 (Greyscale Colors) |
|---|---|---|---|---|
| turtleneck 高领 | cargo | customs agent turtleneck | 海关专员高领衫 | `#39393F#ba832f#ba832f` |
| turtleneck | engineering | engineering guard turtleneck | 工程警卫高领衫 | `#ee7900#a78962#FFE12F` |
| turtleneck | science | science guard turtleneck | 科学警卫高领衫 | `#daeaf0#66748c#830085` |
| turtleneck | medical | orderly turtleneck | 秩序员高领衫 | `#CBCDD1#39393F#16313D` |
| turtleneck | service | bouncer turtleneck | 保安高领衫 | `#39393F#57852A#57852A` |
| skirt 短裙 | cargo | customs agent skirt | 海关专员短裙 | `#39393F#ba832f#ba832f#39393F` |
| skirt | engineering | engineering guard skirt | 工程警卫短裙 | `#ee7900#a78962#FFE12F#FFE12F` |
| skirt | science | science guard skirt | 科学警卫短裙 | `#daeaf0#66748c#830085#830085` |
| skirt | medical | orderly skirt | 秩序员短裙 | `#CBCDD1#39393F#16313D#16313D` |
| skirt | service | bouncer skirt | 保安短裙 | `#39393F#57852A#57852A#EBEBEB` |
| plainskirt 素裙 | cargo | customs agent plain skirt | 海关专员素裙 | `#39393F#ba832f#ba832f#39393F` |
| plainskirt | engineering | engineering guard plain skirt | 工程警卫素裙 | `#ee7900#a78962#FFE12F#FFE12F` |
| plainskirt | science | science guard plain skirt | 科学警卫素裙 | `#daeaf0#66748c#830085#830085` |
| plainskirt | medical | orderly plain skirt | 秩序员素裙 | `#CBCDD1#39393F#16313D#16313D` |
| plainskirt | service | bouncer plain skirt | 保安素裙 | `#39393F#57852A#57852A#EBEBEB` |
| dress 连衣裙 | cargo | customs agent dress | 海关专员连衣裙 | `#ba832f#39393F#ba832f` |
| dress | engineering | engineering guard dress | 工程警卫连衣裙 | `#ee7900#a78962#FFE12F` |
| dress | science | science guard dress | 科学警卫连衣裙 | `#daeaf0#66748c#830085` |
| dress | medical | orderly dress | 秩序员连衣裙 | `#CBCDD1#39393F#16313D` |
| dress | service | bouncer dress | 保安连衣裙 | `#57852A#39393F#EBEBEB` |
| shorts 短裤 | cargo | customs agent shorts | 海关专员短裤 | `#39393F` |
| shorts | engineering | engineering guard shorts | 工程警卫短裤 | `#a78962` |
| shorts | science | science guard shorts | 科学警卫短裤 | `#66748c` |
| shorts | medical | orderly shorts | 秩序员短裤 | `#16313D` |
| shorts | service | bouncer shorts | 保安短裤 | `#57852A` |

### 1.3 颈部饰品 NECK

`/obj/item/clothing/neck/security_cape/armplate_caped/` 下 5 件"披甲护腕披风"（caped X gauntlet）：

| 路径后缀 (Suffix) | 英文名 (EN) | 中文 (CN) | 颜色 (Greyscale) |
|---|---|---|---|
| `/engineer` | caped engineer guard gauntlet | 工程警卫披甲护腕披风 | `#ee7900` |
| `/science` | caped science guard gauntlet | 科学警卫披甲护腕披风 | `#830085` |
| `/medical` | caped orderly gauntlet | 秩序员披甲护腕披风 | `#16313D` |
| `/cargo` | caped cargo guard gauntlet | 货运警卫披甲护腕披风 | `#ba832f` |
| `/service` | caped service guard gauntlet | 服务警卫披甲护腕披风 | `#57852A` |

### 1.4 外套与防弹衣 SUITS

**防弹背心（5 件）**，基类 `/obj/item/clothing/suit/armor/vest/blueshirt/nova`（与 TG 蓝衫相同，图标来自 Nova 主文件 `suits/armor.dmi`，支持 `CLOTHING_DIGITIGRADE_VARIATION_NO_NEW_ICON`，`setup_reskins()` 直接 `return`）：

| 路径 (Path) | 英文名 (EN) | 中文 (CN) | 说明 (Notes) |
|---|---|---|---|
| `/nova`（基类） | — | 蓝衫背心（科学警卫用） | 与 TG 蓝衫相同含图标 |
| `/nova/guard` | — | 无徽章版蓝衫背心 | 图标 `guard_armor`（保安用） |
| `/nova/orderly` | armored orderly coat | 秩序员装甲外套 | "深医护蓝的装甲外套。应付难缠病人时能保护你。" 图标 `medical_coat`，`CLOTHING_DIGITIGRADE_VARIATION` |
| `/nova/engineering_guard` | armored engineering guard coat | 工程警卫装甲外套 | "警示条纹已磨损到形同虚设的装甲外套。至少清理危险区时能保护你。" 图标 `engineering_coat` |
| `/nova/customs_agent` | armored customs agent coat | 海关专员装甲外套 | "织纹精巧繁复的装甲外套，帮你远离难缠的顾客。" 图标 `customs_coat` |

**冬大衣 depgag（5 件）**，`/obj/item/clothing/suit/hooded/wintercoat/security/depgag/`：

| 路径后缀 (Suffix) | 英文名 (EN) | 中文 (CN) | 颜色 (Greyscale) |
|---|---|---|---|
| `/cargo` | customs agent winter coat | 海关专员冬大衣 | `#ba832f#CEC8BF#39393F#39393F` |
| `/engineering` | engineering guard winter coat | 工程警卫冬大衣 | `#ee7900#CEC8BF#a78962#FFE12F` |
| `/science` | science guard winter coat | 科学警卫冬大衣 | `#daeaf0#cec8bf#39393f#830085` |
| `/medical` | orderly winter coat | 秩序员冬大衣 | `#16313D#cec8bf#39393f#cbcdd1` |
| `/service` | bouncer winter coat | 保安冬大衣 | `#57852A#CEC8BF#39393F#39393F` |

**马甲夹克 depgag_vested_jacket（5 件）**，`.../depgag/depgag_vested_jacket/`：

| 路径后缀 (Suffix) | 英文名 (EN) | 中文 (CN) | 颜色 (Greyscale) |
|---|---|---|---|
| `/cargo` | customs agent vested jacket | 海关专员马甲夹克 | `#39393F#39393F#ba832f` |
| `/engineering` | engineering guard vested jacket | 工程警卫马甲夹克 | `#EE7900#39393F#CEC8BF` |
| `/science` | science guard vested jacket | 科学警卫马甲夹克 | `#830085#39393F#daeaf0` |
| `/medical` | orderly vested jacket | 秩序员马甲夹克 | `#16313D#39393F#CBCDD1` |
| `/service` | bouncer vested jacket | 保安马甲夹克 | `#57852a#39393F#ebebeb` |

**警示马甲 depgag_hazard（5 件）**，`/obj/item/clothing/suit/armor/vest/depgag_hazard/`：

| 路径后缀 (Suffix) | 英文名 (EN) | 中文 (CN) | 颜色 (Greyscale) |
|---|---|---|---|
| `/cargo` | customs agent hazard vest | 海关专员警示马甲 | `#ba832f#EBEBEB` |
| `/engineering` | engineering guard hazard vest | 工程警卫警示马甲 | `#ee7900#EBEBEB` |
| `/science` | science guard hazard vest | 科学警卫警示马甲 | `#830085#EBEBEB` |
| `/medical` | orderly hazard vest | 秩序员警示马甲 | `#16313D#EBEBEB` |
| `/service` | bouncer hazard vest | 保安警示马甲 | `#57852A#EBEBEB` |

### 1.5 头部装备 HEAD

**头盔（2 件）**，基类 `/obj/item/clothing/head/helmet/blueshirt/nova`（与 TG 蓝衫头盔相同，**基类即科学警卫使用**，图标不变）：

| 路径 (Path) | 说明 (Notes) |
|---|---|
| `/nova`（基类） | 科学警卫头盔，蓝衫头盔原图标 |
| `/nova/guard` | 无蓝色线条版蓝衫头盔（当前所有部门警卫通用），图标 `mallcop_helm` |

**贝雷帽 beret/sec（5 件）**：

| 路径后缀 (Suffix) | 英文名 (EN) | 中文 (CN) | 徽章 (Insignia) | 颜色 (Greyscale) |
|---|---|---|---|---|
| `/science` | science guard beret | 科学警卫贝雷帽 | 锥形瓶徽章 | `#8D008F#F2F2F2` |
| `/medical` | medical officer beret | 医疗官员贝雷帽 | 医疗徽章（副医护蓝以搭配马甲） | `#16313D#F2F2F2` |
| `/engineering` | engineer officer beret | 工程师官员贝雷帽 | 危险符号徽章 | `#ee7900#F2F2F2` |
| `/cargo` | cargo officer beret | 货运官员贝雷帽 | 板条箱徽章 | `#c99840#F2F2F2` |
| `/service` | bouncer beret | 保安贝雷帽 | 简约徽章 | `#5E8F2D#F2F2F2` |

均为"加固织物制成、提供足够防护的结实贝雷帽"（robust beret ... Uses reinforced fabric to offer sufficient protection），`post_init_icon_state = "beret_badge"`。

**安保帽 security_cap（5 件）**：

| 路径后缀 (Suffix) | 英文名 (EN) | 中文 (CN) | 颜色 (Greyscale) |
|---|---|---|---|
| `/cargo` | customs agent cap | 海关专员帽 | `#ba832f#EBEBEB#39393F` |
| `/engineering` | engineering guard cap | 工程警卫帽 | `#ee7900#EBEBEB#ee7900` |
| `/science` | science guard cap | 科学警卫帽 | `#830085#daeaf0#830085` |
| `/medical` | orderly cap | 秩序员帽 | `#16313D#CBCDD1#39393F` |
| `/service` | bouncer cap | 保安帽 | `#57852A#EBEBEB#57852A` |

### 1.6 腰带 BELT

基类 `/obj/item/storage/belt/security/department_guard`：默认图标 `engine`，图标来自本模块 `icons/obj/belts.dmi` 与 `icons/mob/belts.dmi`，`setup_reskins()` 直接 `return`。

**5 条部门腰带及其 `PopulateContents()` 标准内容**：

| 路径后缀 (Suffix) | 英文名 (EN) | 中文 (CN) | 图标 (icon_state) | 内容物 (Contents) |
|---|---|---|---|---|
| `/science` | science guard belt | 科学警卫腰带 | `science` | 粉色线缆手铐 + 手持闪光灯 + 胡椒弹弹匣 + 胡椒弹手枪 + 科学部门电击棒 |
| `/medical` | medical guard belt | 医疗警卫腰带 | `medical` | 蓝色线缆手铐 + 手持闪光灯 + 胡椒弹弹匣 + 胡椒弹手枪 + 医疗部门电击棒 |
| `/engineering` | engineer guard belt | 工程警卫腰带 | `engine` | 黄色线缆手铐 + 手持闪光灯 + 胡椒弹弹匣 + 胡椒弹手枪 + 工程部门电击棒 |
| `/cargo` | cargo guard belt | 货运警卫腰带 | `cargo` | 橙色线缆手铐 + 手持闪光灯 + 胡椒弹弹匣 + 胡椒弹手枪 + 货运部门电击棒 |
| `/service` | service guard belt | 服务警卫腰带 | `service` | 绿色线缆手铐 + 手持闪光灯 + 胡椒弹弹匣 + 胡椒弹手枪 + 服务部门电击棒 |

内容物源码路径（每腰带 5 件）：
- 手铐：`/obj/item/restraints/handcuffs/cable/` + `pink`（科学）/ `blue`（医疗）/ `yellow`（工程）/ `orange`（货运）/ `green`（服务）
- `/obj/item/assembly/flash/handheld`（手持闪光灯）
- `/obj/item/ammo_box/magazine/pepperball`（胡椒弹弹匣）
- `/obj/item/gun/ballistic/automatic/pistol/pepperball`（胡椒弹手枪）
- `/obj/item/melee/baton/security/loaded/departmental/` + `science` / `medical` / `engineering` / `cargo` / `service`

### 1.7 手套 GLOVES

`/obj/item/clothing/gloves/color/black/security/depgag/` 下 5 件：

| 路径后缀 (Suffix) | 英文名 (EN) | 中文 (CN) | 颜色 (Greyscale) |
|---|---|---|---|
| `/cargo` | customs agent gloves | 海关专员手套 | `#39393F#ba832f` |
| `/engineering` | engineering guard gloves | 工程警卫手套 | `#a78962#ee7900` |
| `/science` | science guard gloves | 科学警卫手套 | `#daeaf0#830085` |
| `/medical` | orderly gloves | 秩序员手套 | `#CBCDD1#16313D` |
| `/service` | bouncer gloves | 保安手套 | `#39393F#57852A` |

### 1.8 出生点 LANDMARKS

`/obj/effect/landmark/start/` 下 5 个职业出生点（图标 `landmarks.dmi`）：

| 路径后缀 (Suffix) | 名称 (name) | 图标状态 (icon_state) |
|---|---|---|
| `/science_guard` | Science Guard | `Science Guard` |
| `/orderly` | Orderly | `Orderly` |
| `/engineering_guard` | Engineering Guard | `Engineering Guard` |
| `/customs_agent` | Customs Agent | `Customs Agent` |
| `/bouncer` | **Service Guard** | `Bouncer` |

### 1.9 科学警卫 Science Guard

**职业 `/datum/job/science_guard`**：

| 字段 (Field) | 值 (Value) | 中文 (CN) |
|---|---|---|
| title | JOB_SCIENCE_GUARD | 科学警卫 |
| description | "Figure out why the emails aren't working, keep an eye on the eggheads, protect them from their latest mistakes." | "弄清楚邮件为什么不好使，盯着那些书呆子，保护他们免受自己最新失误的伤害。" |
| faction | FACTION_STATION | 空间站派系 |
| total_positions / spawn_positions | 2 / 2 | 总职位 / 出生职位各 2 |
| supervisors | SUPERVISOR_RD | 上司：研发总监 |
| exp_granted_type | EXP_TYPE_CREW | 经验类型：船员 |
| config_tag | "SCIENCE_GUARD" | 配置标签 |
| outfit | /datum/outfit/job/science_guard | 配装 |
| plasmaman_outfit | /datum/outfit/plasmaman/science |  plasmaman 配装 |
| paycheck / paycheck_department | PAYCHECK_CREW / ACCOUNT_SCI | 薪资：船员级 / 科学部门账户 |
| display_order | JOB_DISPLAY_ORDER_SCIENCE_GUARD | 显示顺序 |
| bounty_types | CIV_JOB_SCI | 赏金类型：科研 |
| departments_list | /datum/job_department/science | 隶属部门：科学 |
| family_heirlooms | 太空法手册 + `/obj/item/clothing/head/beret/sec/science` | 家族传家宝：太空法手册、科学警卫贝雷帽 |
| mail_goodies | 焦糖甜甜圈×10、抹茶甜甜圈×10、南瓜甜甜圈×5、哨子×5、回旋镖电击棒（满电）×1 | 邮件赠品（5 个警卫职业完全相同） |
| job_flags | STATION_JOB_FLAGS \| JOB_ANTAG_PROTECTED | 职业标志 |

**配装 `/datum/outfit/job/science_guard`**（"Science Guard"）：

| 槽位 (Slot) | 物品 (Item) | 中文 (CN) |
|---|---|---|
| belt | storage/belt/security/department_guard/science | 科学警卫腰带 |
| ears | radio/headset/headset_sci | 科研耳机 |
| uniform | under/rank/security/officer/blueshirt/nova | 科学警卫制服 |
| shoes | clothing/shoes/jackboots | 军靴 |
| head | head/helmet/blueshirt/nova | 蓝衫头盔（基类） |
| suit | suit/armor/vest/blueshirt/nova | 蓝衫背心（基类） |
| r_pocket | /obj/item/flashlight | 手电筒 |
| l_pocket | /obj/item/modular_computer/pda/science | 科研 PDA |
| backpack / satchel / duffelbag / messenger | science / satchel science / duffelbag science / messenger science | 科研背包系列 |
| id_trim | /datum/id_trim/job/science_guard | 证件装饰 |
| pda_slot | ITEM_SLOT_LPOCKET | PDA 槽位：左口袋 |

**ID 装饰 `/datum/id_trim/job/science_guard`**：

| 字段 (Field) | 值 (Value) |
|---|---|
| assignment | "Science Guard" |
| trim_icon / trim_state | `master_files/icons/obj/card.dmi` / `trim_calhoun` |
| department_color / subdepartment_color | COLOR_SCIENCE_PINK（科学粉）双项 |
| sechud_icon_state | SECHUD_SCIENCE_GUARD |
| extra_access = minimal_access（14 项） | ACCESS_AUX_BASE、ACCESS_BRIG_ENTRANCE、ACCESS_GENETICS、ACCESS_MECH_SCIENCE、ACCESS_MINERAL_STOREROOM、ACCESS_ORDNANCE、ACCESS_ORDNANCE_STORAGE、ACCESS_RESEARCH、ACCESS_ROBOTICS、ACCESS_SCIENCE、ACCESS_SECURITY、ACCESS_TECH_STORAGE、ACCESS_WEAPONS、ACCESS_XENOBIOLOGY |
| template_access | ACCESS_CAPTAIN、ACCESS_RD、ACCESS_CHANGE_IDS |

### 1.10 秩序员 Orderly

**职业 `/datum/job/orderly`**：

| 字段 (Field) | 值 (Value) | 中文 (CN) |
|---|---|---|
| title | JOB_ORDERLY | 秩序员 |
| description | "Defend the medical department, hold down idiots who refuse the vaccine, assist medical with prep and/or cleanup." | "保卫医疗部，按住拒绝接种疫苗的蠢货，协助医疗部做准备工作或清理工作。" |
| supervisors | SUPERVISOR_CMO | 上司：首席医疗官 |
| total_positions / spawn_positions | 2 / 2 | |
| config_tag | "ORDERLY" | |
| paycheck_department | ACCOUNT_MED | 医疗部门账户 |
| bounty_types | CIV_JOB_MED | 赏金类型：医疗 |
| departments_list | /datum/job_department/medical | 隶属：医疗 |
| family_heirlooms | 太空法手册 + beret/sec/medical | 传家宝：太空法手册、医疗官员贝雷帽 |
| plasmaman_outfit | /datum/outfit/plasmaman/medical | |
| job_flags | STATION_JOB_FLAGS \| JOB_ANTAG_PROTECTED | |

（mail_goodies 与 1.9 相同：甜甜圈三款 + 哨子 + 回旋镖电击棒）

**配装 `/datum/outfit/job/orderly`**（"Orderly"）：

| 槽位 (Slot) | 物品 (Item) | 中文 (CN) |
|---|---|---|
| belt | department_guard/medical | 医疗警卫腰带 |
| ears | headset_med | 医疗耳机 |
| uniform | blueshirt/nova/orderly | 秩序员制服 |
| shoes | shoes/sneakers/white | 白色运动鞋 |
| head | head/helmet/blueshirt/nova/guard | 无蓝线头盔（mallcop_helm） |
| suit | suit/armor/vest/blueshirt/nova/orderly | 秩序员装甲外套 |
| r_pocket | flashlight | 手电筒 |
| l_pocket | pda/medical | 医疗 PDA |
| backpack 系列 | medic / satchel/med / duffelbag/med / messenger/med | 医疗背包系列 |
| box | box/survival/medical | 医疗生存盒 |
| id_trim | /datum/id_trim/job/orderly | |
| pda_slot | ITEM_SLOT_LPOCKET | |

**ID 装饰 `/datum/id_trim/job/orderly`**：assignment "Orderly"；trim_state `trim_orderly`；COLOR_MEDICAL_BLUE（医疗蓝）双项；SECHUD_ORDERLY；extra = minimal（11 项）：ACCESS_BRIG_ENTRANCE、ACCESS_MECH_MEDICAL、ACCESS_MEDICAL、ACCESS_MINERAL_STOREROOM、ACCESS_MORGUE、ACCESS_PHARMACY、ACCESS_PLUMBING、ACCESS_SECURITY、ACCESS_SURGERY、ACCESS_VIROLOGY、ACCESS_WEAPONS；template_access：ACCESS_CAPTAIN、ACCESS_CMO、ACCESS_CHANGE_IDS。

### 1.11 工程警卫 Engineering Guard

**职业 `/datum/job/engineering_guard`**：

| 字段 (Field) | 值 (Value) | 中文 (CN) |
|---|---|---|
| title | JOB_ENGINEERING_GUARD | 工程警卫 |
| description | "Monitor the supermatter, keep an eye on atmospherics, make sure everyone is wearing Proper Protective Equipment." | "监视超物质，盯着大气系统，确保每个人都穿着合适的防护装备。" |
| supervisors | SUPERVISOR_CE | 上司：总工程师 |
| total_positions / spawn_positions | 2 / 2 | |
| config_tag | "ENGINEERING_GUARD" | |
| paycheck_department | ACCOUNT_ENG | 工程部门账户 |
| bounty_types | CIV_JOB_ENG | 赏金：工程 |
| departments_list | /datum/job_department/engineering | 隶属：工程 |
| family_heirlooms | 太空法手册 + beret/sec/engineering | 传家宝：太空法手册、工程师官员贝雷帽 |
| plasmaman_outfit | /datum/outfit/plasmaman/engineering | |
| job_flags | STATION_JOB_FLAGS \| JOB_ANTAG_PROTECTED | |

**配装 `/datum/outfit/job/engineering_guard`**（"Engineering Guard"）：belt `department_guard/engineering`；ears `headset_eng`；shoes `workboots`（工装靴）；uniform `blueshirt/nova/engineering_guard`；head `helmet/blueshirt/nova/guard`；suit `blueshirt/nova/engineering_guard`（工程警卫装甲外套）；r_pocket 手电筒；l_pocket `pda/engineering`；背包系列 `industrial / satchel/eng / duffelbag/engineering / messenger/eng`；box `box/survival/engineer`；id_trim 工程警卫。

**ID 装饰 `/datum/id_trim/job/engineering_guard`**：assignment "Engineering Guard"；trim_state `trim_engiguard`；COLOR_ENGINEERING_ORANGE（工程橙）双项；SECHUD_ENGINEERING_GUARD；extra = minimal（14 项）：ACCESS_ATMOSPHERICS、ACCESS_AUX_BASE、ACCESS_BRIG_ENTRANCE、ACCESS_CONSTRUCTION、ACCESS_ENGINEERING、ACCESS_ENGINE_EQUIP、ACCESS_EXTERNAL_AIRLOCKS、ACCESS_MAINT_TUNNELS、ACCESS_MINERAL_STOREROOM、ACCESS_MECH_ENGINE、ACCESS_SECURITY、ACCESS_TCOMMS、ACCESS_TECH_STORAGE、ACCESS_WEAPONS；template_access：ACCESS_CAPTAIN、ACCESS_CE、ACCESS_CHANGE_IDS。

### 1.12 海关专员 Customs Agent

**职业 `/datum/job/customs_agent`**：

| 字段 (Field) | 值 (Value) | 中文 (CN) |
|---|---|---|
| title | JOB_CUSTOMS_AGENT | 海关专员 |
| description | "Inspect the packages coming to and from the station, protect the cargo department, beat the shit out of people trying to ship Cocaine to the Spinward Stellar Coalition." | "检查进出空间站的包裹，保卫货运部，把想往 Spinward 星盟走私可卡因的人揍个半死。" |
| supervisors | SUPERVISOR_QM | 上司：军需官 |
| total_positions / spawn_positions | 2 / 2 | |
| config_tag | "CUSTOMS_AGENT" | |
| paycheck_department | ACCOUNT_CAR | 货运部门账户 |
| bounty_types | **CIV_JOB_RANDOM** | 赏金：随机 |
| departments_list | /datum/job_department/cargo | 隶属：货运 |
| family_heirlooms | 太空法手册 + beret/sec/cargo | 传家宝：太空法手册、货运官员贝雷帽 |
| plasmaman_outfit | /datum/outfit/plasmaman/cargo | |
| job_flags | STATION_JOB_FLAGS \| JOB_ANTAG_PROTECTED | |

**配装 `/datum/outfit/job/customs_agent`**（"Customs Agent"）：belt `department_guard/cargo`；ears `headset_cargo`；shoes `sneakers/black`（黑色运动鞋）；uniform `blueshirt/nova/customs_agent`；head `helmet/blueshirt/nova/guard`；suit `blueshirt/nova/customs_agent`（海关专员装甲外套）；**glasses `glasses/hud/gun_permit`（持枪许可 HUD 眼镜）**；r_pocket 手电筒；l_pocket `pda/cargo`；背包系列 通用 `backpack / satchel / duffelbag / messenger`；id_trim 海关专员；pda_slot LPOCKET。

**ID 装饰 `/datum/id_trim/job/customs_agent`**：assignment "Customs Agent"；trim_state `trim_customs`；COLOR_CARGO_BROWN（货运棕）双项；SECHUD_CUSTOMS_AGENT；extra = minimal（10 项）：ACCESS_BRIG_ENTRANCE、ACCESS_CARGO、ACCESS_MAINT_TUNNELS、ACCESS_MECH_MINING、ACCESS_MINERAL_STOREROOM、ACCESS_MINING、ACCESS_MINING_STATION、ACCESS_SECURITY、ACCESS_SHIPPING、ACCESS_WEAPONS；template_access：ACCESS_CAPTAIN、ACCESS_QM、ACCESS_CHANGE_IDS。

### 1.13 保安 Bouncer

**职业 `/datum/job/bouncer`**：

| 字段 (Field) | 值 (Value) | 中文 (CN) |
|---|---|---|
| title | JOB_BOUNCER | 保安 |
| description | "Make sure people don't jump the kitchen counter, stop Chapel vandalism, check bargoer's IDs, prevent the dreaded \"food fight\"." | "确保没人翻越厨房柜台，制止教堂涂鸦破坏，检查酒吧客人的证件，阻止可怕的'食物大战'。" |
| supervisors | SUPERVISOR_HOP | 上司：人事主管 |
| total_positions / spawn_positions | 2 / 2 | |
| config_tag | "BOUNCER" | |
| paycheck_department | ACCOUNT_SRV | 服务部门账户 |
| bounty_types | CIV_JOB_DRINK | 赏金：饮酒 |
| departments_list | /datum/job_department/service | 隶属：服务 |
| family_heirlooms | 太空法手册 + beret/sec/service | 传家宝：太空法手册、保安贝雷帽 |
| plasmaman_outfit | **/datum/outfit/plasmaman/party_bouncer** | 派对保安等离子人 |
| job_flags | STATION_JOB_FLAGS \| JOB_ANTAG_PROTECTED | |

**配装 `/datum/outfit/job/bouncer`**（"Bouncer"）：belt `department_guard/service`；ears `headset_srv`；uniform `blueshirt/nova/bouncer`；shoes `sneakers/black`；head `helmet/blueshirt/nova/guard`；suit `suit/armor/vest/blueshirt/nova/guard`（无徽章背心）；l_pocket `pda/bar`（酒吧 PDA）；r_pocket 手电筒；**glasses `glasses/sunglasses`（墨镜）**；背包系列 通用四件套；id_trim 保安；pda_slot LPOCKET。

**ID 装饰 `/datum/id_trim/job/bouncer`**：assignment **"Service Guard"**（服务警卫）；trim_state `trim_bouncer`；COLOR_SERVICE_LIME（服务青柠绿）双项（源码注释：*"我个人会想要一个带安保配色的，但我讲究原汁原味"*）；SECHUD_BOUNCER；extra = minimal（11 项）：ACCESS_BAR、ACCESS_JANITOR、ACCESS_SERVICE、ACCESS_BRIG_ENTRANCE、ACCESS_HYDROPONICS、ACCESS_KITCHEN、ACCESS_MINERAL_STOREROOM、ACCESS_MORGUE、ACCESS_SECURITY、ACCESS_THEATRE、ACCESS_WEAPONS；template_access：ACCESS_CAPTAIN、ACCESS_HOP、ACCESS_CHANGE_IDS。

### 1.14 部门电击棒 Departmental Batons

**基类 `/obj/item/melee/baton/security/loaded/departmental`**：

| 字段 (Field) | 值 (Value) | 中文 (CN) |
|---|---|---|
| name | departmental stun baton | 部门电击棒 |
| desc | "A stun baton fitted with a departmental area-lock, based off the station's blueprint layout - outside of its department, it only has three uses." | "装有部门区域锁的电击棒，基于空间站蓝图布局——离开本部门区域只能使用三次。" |
| icon / icon_state | `goofsec/icons/departmental_batons.dmi` / `prison_stunbaton` | 图标 |
| inside_belt_icon_state | "stunbaton" | 腰带内图标 |
| department_icon_state | "prison"（默认） | 部门图标状态 |
| valid_areas | list()（子类定义） | 有效区域列表 |
| emagged | FALSE | 是否被黑客破解 |
| non_departmental_uses_left | **4**（即区域外可用 3 次） | 区域外剩余次数 |

**核心机制**：
- `update_icon_state()`：图标状态拼接为 `"[department_icon_state]_[icon_state]"`。
- `try_stun()`：激活且未破解且冷却结束时——若使用者所在区域不在 `valid_areas` 内：扣减 `non_departmental_uses_left`（4→3→2→1 时语音播报剩余次数，归零时播报"电击棒已锁定"），归零后：目标可见消息 + 使用者警告消息 + 关闭电击棒 + 气泡提示 + 火花音效，返回 FALSE 拒绝电击。
- `attack_self()`：在有效区域内重新开启且剩余次数 < 4 时，语音提示并**重置回 4 次**（回部门"充电"）。
- `emag_act()`：破解后 `emagged = TRUE`、`obj_flags |= EMAGGED`，**无视区域锁**，火花音效，返回 TRUE。

**6 个变体（部门区域锁精确列表）**：

| 变体 (Variant) | 英文名 (EN) | 中文 (CN) | valid_areas（有效区域） |
|---|---|---|---|
| `/medical` | medical stun baton | 医疗电击棒 | `/area/station/medical`、`/area/station/maintenance/department/medical`、`/area/shuttle/escape`（逃生梭） |
| `/engineering` | engineering stun baton | 工程电击棒 | `/area/station/engineering`、`/area/station/maintenance/department/engine`、逃生梭 |
| `/science` | science stun baton | 科学电击棒 | `/area/station/science`、`/area/station/maintenance/department/science`、逃生梭 |
| `/cargo` | cargo stun baton | 货运电击棒 | `/area/station/cargo`、`/area/station/maintenance/department/cargo`、逃生梭 |
| `/service` | service stun baton | 服务电击棒 | `/area/station/service`、`/area/station/hallway/primary/fore`、`/area/station/commons/lounge`、`/area/station/maintenance/department/chapel`、`/area/station/maintenance/department/crew_quarters`、逃生梭 |
| `/prison` | prison stun baton | 监狱电击棒 | `/area/station/security/prison`、`/area/station/security/processing`、逃生梭 |

各变体描述统一为："不会在 X 部门之外运作的电击棒，基于空间站蓝图布局。可在 X 部门外使用最多三次，之后需要返回！"（`department_icon_state` = medical / engineering / science / cargo / service / prison）

### 1.15 补给箱 Supply Packs

6 个补给箱（`cost = CARGO_CRATE_VALUE * 2`，`access_view = ACCESS_SECURITY`，`access = ACCESS_SECURITY`）：

| 类型 (Type) | 名称 (Name) | 中文 (CN) | 内容 (Contains) | 说明 (Desc) |
|---|---|---|---|---|
| `/datum/supply_pack/security/baton_prison` | Prison Baton Crate | 监狱电击棒箱 | 监狱电击棒×1 | "为管教警员准备的一根额外电击棒。万一你讨厌他们手里有普通电击棒这个主意。" |
| `/datum/supply_pack/service/baton_service` | Service Baton Crate | 服务电击棒箱 | 服务电击棒×1 | "为服务警卫准备的一根额外电击棒。" |
| `/datum/supply_pack/medical/baton_medical` | Medical Baton Crate | 医疗电击棒箱 | 医疗电击棒×1 | "为秩序员准备的一根额外电击棒。" |
| `/datum/supply_pack/engineering/baton_engineering` | Engineering Baton Crate | 工程电击棒箱 | 工程电击棒×1 | "为工程警卫准备的一根额外电击棒。" |
| `/datum/supply_pack/science/baton_science` | Science Baton Crate | 科学电击棒箱 | 科学电击棒×1 | "为科学警卫准备的一根额外电击棒。" |
| `/datum/supply_pack/misc/baton_cargo` | Cargo Baton Crate | 货运电击棒箱 | 货运电击棒×1 | "为海关专员准备的一根额外电击棒。" |

### 1.16 服装袋 Garment Bags

`/obj/item/storage/bag/garment/` 下 5 个警卫服装袋（"A bag for storing extra clothes and shoes. This one belongs to the X."——"存放备用衣物和鞋子的袋子。这条属于 X。"）：

**science_guard's garments（科学警卫服装袋，18 件）**：科研耳机×1、黑色运动鞋×1、科学警卫制服×1、蓝衫头盔（基类）×1、科学贝雷帽×1、科学安保帽×1、蓝衫背心（基类）×1、科学警示马甲×1、科学马甲夹克×1、科学冬大衣×1、安保 HUD 眼镜×1、科学警卫手套×1、科学短裤×1、科学高领衫×1、科学短裙×1、科学素裙×1、科学连衣裙×1、科学冬大衣×1（重复项）、科学披甲护腕披风×1。

**orderly's garments（秩序员服装袋，18 件）**：医疗耳机×1、白色运动鞋×1、秩序员制服×1、无蓝线头盔×1、医疗贝雷帽×1、秩序员帽×1、秩序员装甲外套×1、医疗警示马甲×1、医疗马甲夹克×1、医疗冬大衣×1、安保 HUD 眼镜×1、秩序员手套×1、秩序员短裤×1、秩序员高领衫×1、秩序员短裙×1、秩序员素裙×1、秩序员连衣裙×1、医疗冬大衣×1（重复项）、秩序员披甲护腕披风×1。

**engineering guard's garments（工程警卫服装袋，18 件）**：工程耳机×1、工装靴×1、工程警卫制服×1、无蓝线头盔×1、工程贝雷帽×1、工程警卫帽×1、工程警卫装甲外套×1、工程警示马甲×1、工程马甲夹克×1、工程冬大衣×1、安保 HUD 眼镜×1、工程警卫手套×1、工程短裤×1、工程高领衫×1、工程短裙×1、工程素裙×1、工程连衣裙×1、工程冬大衣×1（重复项）、工程警卫披甲护腕披风×1。

**customs agent's garments（海关专员服装袋，19 件）**：货运耳机×1、黑色运动鞋×1、海关专员制服×1、无蓝线头盔×1、货运贝雷帽×1、海关专员帽×1、海关专员装甲外套×1、货运警示马甲×1、货运马甲夹克×1、货运冬大衣×1、安保 HUD 眼镜×1、**持枪许可 HUD 眼镜×1**、海关专员手套×1、海关短裤×1、海关高领衫×1、海关短裙×1、海关素裙×1、海关连衣裙×1、货运冬大衣×1（重复项）、海关披甲护腕披风×1。

**the service guard's garments（服务警卫服装袋，18 件）**：服务耳机×1、黑色运动鞋×1、保安制服×1、无蓝线头盔×1、保安贝雷帽×1、保安帽×1、无徽章背心×1、**警示马甲（无部门后缀 `depgag_hazard` 基类）×1**、服务马甲夹克×1、服务冬大衣×1、安保 HUD 眼镜×1、保安手套×1、保安短裤×1、保安高领衫×1、保安短裙×1、保安素裙×1、保安连衣裙×1、服务警卫披甲护腕披风×1。

---

## 2. 手机执法系统 Cellphone Law Enforcement

> 源码路径 (Source)：`modular_nova/modules/goofsec/code/cellphone.dm`（61 行）

NOVA 安保手机的底层实现复用了黑帮（gang）手机的"帮派手机"逻辑：**同频手机之间可跨房间实时语音转发**（按住说话 → 同频道手机播放），并同步给观察者（ghost）收听。本模块通过 `gang_id` 将 911/SWAT/军队/披萨队等响应队伍的成员手机编入同一频道。

**全局 (Global)**：`GLOBAL_LIST_EMPTY(gangster_cell_phones)` —— 全部黑帮手机列表。

**物品 `/obj/item/gangster_cellphone`（cell phone，手机）**：

| 字段 (Field) | 值 (Value) | 中文 (CN) |
|---|---|---|
| name | cell phone | 手机 |
| desc | "TODO: funny joke about the 80s, brick phones" | "待办：关于 80 年代砖头手机的搞笑笑话" |
| icon / icon_state | `icons/obj/antags/gang/cell_phone.dmi` / `phone_off` | 图标 / 关机状态 |
| throwforce | **15** | 投掷伤害 15（"这些玩意儿密实得离谱"） |
| gang_id | "Grove Street Families"（默认） | 帮派/频道 ID（911 手机会被覆写为 "911"、"Dogginos" 等） |
| activated | FALSE | 是否已激活（开机） |

**机制 (Mechanics)**：
- `Initialize(mapload)`：加入 `GLOB.gangster_cell_phones` 全局列表，并 `become_hearing_sensitive()`（成为听觉敏感物，可接收语音）。
- `Destroy()`：从全局列表移除。
- `attack_self()`：切换激活状态——开机：提示语 + `icon_state = "phone_on"`、`activated = TRUE`；关机：反向操作（提示语走本地化键 `obj.11cd7563` / `obj.833a0800`）。
- `Hear()`（语音接收）：仅当已激活、且说话者与手机**同处一格**（`get_turf(speaker) == get_turf(src)`）时，调用 `broadcast_message()` 转发。
- `broadcast_message(message, speaker)`：遍历全局所有手机——**同 `gang_id` 的手机调用 `say_message()` 播放**；同时向所有玩家中的观察者（ghost）转发：距离 > 7 格或不同 Z 层时仅在开启"幽灵顺风耳"（CHAT_GHOSTEARS）时收到；格式：`[FOLLOW_LINK] <b>[说话者名]</b> \[CELL: 频道ID\] says, "[消息]"`（`span_gangradio` 帮派电台样式）。
- `say_message(message, speaker)`：对手机所在格的每个碳基人类播放；`TRAIT_DEAF`（耳聋）特质者跳过；格式同上 `\[CELL: 频道ID\]`。

**应用 (Usage)**：911 响应队伍成员出生即获得一部 `[911] branded cell phone`（"911 牌手机"，`WEIGHT_CLASS_SMALL` 紧凑型）——见第 5 章；SWAT/军队/披萨队同样通过 `cell_phone_number` 编组频道。

---

## 3. 储物柜改革 Lockers Reform

> 源码路径 (Source)：`modular_nova/modules/goofsec/code/lockers.dm`（75 行）

**部门警卫储物柜（5 个）**——`/obj/structure/closet/secure_closet/security/` 下，均要求 **ACCESS_BRIG_ENTRANCE + 本部门权限**双权限：

| 路径后缀 (Suffix) | 名称 (Name) | 中文 (CN) | req_access | 图标 (icon_state) |
|---|---|---|---|---|
| `/cargo` | customs agent's locker | 海关专员的储物柜 | ACCESS_BRIG_ENTRANCE + ACCESS_CARGO | `qm` |
| `/engine` | engineering guard's locker | 工程警卫的储物柜 | ACCESS_BRIG_ENTRANCE + ACCESS_ENGINEERING | `eng_secure` |
| `/science` | science guard's locker | 科学警卫的储物柜 | ACCESS_BRIG_ENTRANCE + ACCESS_RESEARCH | `science` |
| `/med` | orderly's locker | 秩序员的储物柜 | ACCESS_BRIG_ENTRANCE + ACCESS_MEDICAL | `med_secure` |
| `/service` | the service guard's locker | 服务警卫的储物柜 | ACCESS_BRIG_ENTRANCE + ACCESS_SERVICE | `serviceguard` |

**PopulateContents() 标准内容（每柜 5 件）**：
1. `/obj/item/ammo_box/advanced/pepperballs`（高级胡椒弹弹药盒）
2. `/obj/item/restraints/handcuffs/cable/` + 部门色（pink 科学 / yellow 工程 / orange 货运 / blue 医疗 / green 服务）
3. `/obj/item/assembly/flash/handheld`（手持闪光灯）
4. `/obj/item/storage/bag/garment/<职业>`（服装袋）×**2**

> 注：与腰带内容物一致但弹药为**高级胡椒弹弹药盒**（belt 里是弹匣），且服装袋给双份。

**囚犯储物柜（Prisoner Lockers）**——重写 `/obj/structure/closet/secure_closet/brig/PopulateContents()`：调用父类后追加 3 件囚犯"兔女郎"套装：
- `/obj/item/clothing/head/playbunnyears/prisoner`（囚犯兔子耳朵头饰）
- `/obj/item/clothing/under/rank/security/prisoner_bunnysuit`（囚犯兔女郎连体衣）
- `/obj/item/clothing/neck/tie/bunnytie/prisoner`（囚犯兔子领结）

---

## 4. 配装与杂项 Outfits & Misc

### 4.1 配装 Outfits

> 源码路径 (Source)：`modular_nova/modules/goofsec/code/outfits.dm`（46 行）

**`/datum/outfit/solfed`（SolFed Official，SolFed 官方官员）**：

| 槽位 (Slot) | 物品 (Item) | 中文 (CN) |
|---|---|---|
| name | SolFed Official | SolFed 官方官员 |
| uniform | `/obj/item/clothing/under/solfed/officer` | SolFed 高官制服 |
| gloves | `/obj/item/clothing/gloves/combat` | 战斗手套 |
| shoes | `/obj/item/clothing/shoes/jackboots` | 军靴 |
| accessory | `/obj/item/clothing/accessory/nova/solfedribbon` | SolFed 军衔绶带 |
| neck | `/obj/item/clothing/neck/mantle/solfed` | Sol 联邦披肩 |
| back | `/obj/item/storage/backpack` | 背包 |
| ears | `/obj/item/radio/headset/headset_solfed/officials` | SolFed 官员耳机 |
| l_pocket | `/obj/item/restraints/handcuffs` | 手铐 |
| r_pocket | `/obj/item/flashlight/seclite` | 安保手电 |
| id | `/obj/item/card/id/advanced/solfed` | SolFed 高级身份证 |
| r_hand | `/obj/item/clipboard` | 写字板 |
| backpack_contents | `stamp/solfed`（SolFed 印章）+ `stamp/denied`（拒绝章）+ `stamp/granted`（批准章） | 背包内容 |
| id_trim | `/datum/id_trim/solfed/official` | 证件装饰 |

**`post_equip()`**：耳机设置为 **FREQ_SOLFED** 频率并 `freqlock = TRUE`（锁定频率不可调）；身份证登记真实姓名并更新标签。

**3 个变体**：
- `/datum/outfit/solfed/lowrank`——**SolFed Official (Low Rank)**（低阶官员）：`under/solfed/officer_lowrnk`（低阶制服）+ `acc_medal/neckpin/solfed/official`（官员颈针）。
- `/datum/outfit/solfed/civil`——**SolFed Official (Civil Services)**（民政服务）：`under/solfed/official_civil`（民政制服）+ 官员颈针。
- `/datum/outfit/solfed/social`——**SolFed Official (Social Services)**（社会服务）：`under/solfed/official_social`（社会服务制服）+ 官员颈针。

（三者均继承基类全部装备，仅替换制服与配饰）

### 4.2 杂项 solfed_bloat

> 源码路径 (Source)：`modular_nova/modules/goofsec/code/solfed_bloat.dm`（76 行）

**Marine 防弹背心配色（8 件）**——`/obj/item/clothing/suit/armor/vest/sol/marine/`：

| 路径 (Path) | 配色 (Greyscale) | 中文 (CN) |
|---|---|---|
| `/desert` | `#e0dab7` | 沙漠 |
| `/winter` | `#eaeaea` | 冬季 |
| `/ocean` | `#53638f` | 海洋 |
| `/forest` | `#008000` | 森林 |
| `/mk2/desert` | `#e0dab7` | MK II 沙漠 |
| `/mk2/winter` | `#eaeaea` | MK II 冬季 |
| `/mk2/ocean` | `#53638f` | MK II 海洋 |
| `/mk2/forest` | `#008000` | MK II 森林 |

**护目镜配色（4 件）**——`/obj/item/clothing/glasses/sunglasses/solfed/`：`/winter` `#eaeaea`（冬季）、`/forest` `#008000`（森林）、`/ocean` `#53638f`（海洋）、`/desert` `#e8dd9b`（沙漠）。

**SolFed 战斗头盔（6 件）**——`/obj/item/clothing/head/helmet/solfed/`：

| 路径 (Path) | 名称 (Name) | 中文 (CN) | 配色 (Greyscale) |
|---|---|---|---|
| `/mk2/corpsman` | SolFed Corpsman MK II Combat Helmet | SolFed 医护兵 MK II 战斗头盔 | `#1dd2ff` |
| `/mk2/engineer` | SolFed Engineer MK II Combat Helmet | SolFed 工程师 MK II 战斗头盔 | `#ff1d1d` |
| `/mk2/squadlead` | SolFed Squad Leader MK II Combat Helmet | SolFed 队长 MK II 战斗头盔 | `#ffe81d` |
| `/corpsman` | SolFed Corpsman MK I Combat Helmet | SolFed 医护兵 MK I 战斗头盔 | `#1dd2ff` |
| `/engineer` | SolFed Engineer MK I Combat Helmet | SolFed 工程师 MK I 战斗头盔 | `#ff1d1d` |
| `/squadlead` | SolFed Squad Leader MK I Combat Helmet | SolFed 队长 MK I 战斗头盔 | `#ffe81d` |

**Espatier 迷彩制服 Multi-Cam（4 件）**——`/obj/item/clothing/under/solfed/marines/`：

| 路径 (Path) | 配色 (Greyscale) | 中文 (CN) |
|---|---|---|
| `/winter` | `#eaeaea#5f5f5f#969696` | 冬季 |
| `/forest` | `#008000#663300#333333` | 森林 |
| `/ocean` | `#53638f#374c75#145779` | 海洋 |
| `/desert` | `#e8dd9b#d6c76d#afa984` | 沙漠 |

---

## 5. SolFed 911 应急响应系统 SolFed 911 Response

> 源码路径 (Source)：`modular_nova/modules/goofsec/code/sol_fed.dm`（878 行）

**系统总览**：空间站通讯电脑（communications console）新增"呼叫 911"按钮——船长/硅基可呼叫 **Sol 联邦**应急服务（Marshal 警长 / 高级大气组 / EMT 急救员），幽灵玩家投票响应后以 ERT 身份降临；一线响应者可用 **S.W.A.T. 备用呼叫器**投票召唤 SWAT（$20,000），SWAT 可用**叛国举报器**召唤军队镇压叛国；另有彩蛋"Dogginos 披萨"假呼叫。所有响应队伍通过**品牌手机**（见第 2 章）内部通讯，用**光束撤离工具**撤离。

### 5.1 常量与全局变量

**宏定义 (Defines)**：

| 宏 (Define) | 值 (Value) | 中文 (CN) |
|---|---|---|
| SOLFED_AMT / SOLFED_VOTES / SOLFED_DECLARED | "amount" / "votes" / "declared" | 数量/票数/已宣告 键名 |
| SOLFED_FINE_AMOUNT | **-20000** | 罚款金额（从货运账户扣 $20,000） |
| EMERGENCY_RESPONSE_POLICE | "WOOP WOOP THAT'S THE SOUND OF THE POLICE" | 呼叫类型：警察 |
| EMERGENCY_RESPONSE_ATMOS | "DISCO INFERNO" | 呼叫类型：大气 |
| EMERGENCY_RESPONSE_EMT | "AAAAAUGH, I'M DYING, I NEEEEEEEEEED A MEDIC BAG" | 呼叫类型：急救 |
| EMERGENCY_RESPONSE_EMAG | "AYO THE PIZZA HERE" | 呼叫类型：披萨（假呼叫彩蛋） |
| MESSAGE_SOLFED | "Sol Federation" | 消息署名 |

**全局变量 (Globals)**：
- `GLOBAL_VAR(caller_of_911)`（911 呼叫者姓名）、`GLOBAL_VAR(call_911_msg)`（呼叫内容）、`GLOBAL_VAR(pizza_order)`（披萨订单名）、`GLOBAL_VAR(fedmessage)`（联系联邦留言）。
- `GLOBAL_VAR_INIT(solfed_tech_charge, -15000)`：每名高级大气响应者向空间站货运账户收取 **$15,000**。
- `GLOBAL_LIST_INIT(pizza_names, ...)`：**26 个**恶搞姓名（Dixon Buttes、I. C. Weiner、Seymour Butz、I. P. Freely、Pat Myaz、Vye Agra、Harry Balsack、Lee Nover、Maya Buttreeks、Amanda Hugginkiss、Bwight K. Brute、John Nanotrasen、Mike Rotch、Hugh Jass、Oliver Closeoff、Hugh G. Recktion、Phil McCrevis、Willie Lickerbush、Ben Dover、Steve、Avery Goodlay、Anne Fetamine、Amanda Peon、Tara Newhole、Penny Tration、Joe Mama）。
- `GLOBAL_LIST_INIT(emergency_responders, list())`：响应者列表。
- `GLOBAL_LIST_INIT(solfed_responder_info, ...)`：5 个响应组（`911_responders`、`swat`、`national_guard`、`dogginos`、`dogginos_manager`），每组含 `amount / votes / declared` 三项。
- `GLOBAL_LIST_INIT(call911_do_and_do_not, ...)`：911 呼叫"应与不应"清单——EMT（大量尸体/空间站无法处理的紧急医疗状况应呼叫；船长崴脚/一两具尸体/轻微疫情不应呼叫）、Marshals（安保无视指挥/侵犯公民权/叛变/违反 Sol 联邦公民权应呼叫；公司事务/追捕/调解纠纷不应呼叫）、Atmos（全站失压/超物质解体级维修/走廊火海/部门级破洞且工程大气无法处理应呼叫；图书馆垃圾桶着火/单间破洞/暖气问题不应呼叫，**每个响应者收费 15,000 信用点**）。

### 5.2 呼叫流程 call_911

**`/obj/machinery/computer/communications/proc/call_911(ordered_team)`**（内部；按警戒级别向幽灵投票并派出一队太空警察，附带公告）：

| 呼叫类型 (Team) | 人数 (Team Size) | 反派人设 (Antag) | 公告者 (Announcer) | 手机频道 (Phone) |
|---|---|---|---|---|
| EMERGENCY_RESPONSE_POLICE | **8** | `/datum/antagonist/ert/request_911/police` | Sol Federation Marshal Department | "911" |
| EMERGENCY_RESPONSE_ATMOS | tgui 输入（默认 **3**，上限 3，下限 1） | `/datum/antagonist/ert/request_911/atmos` | Sol Federation 811 Dispatch - Advanced Atmospherics | "911"（保持与 SWAT 通讯） |
| EMERGENCY_RESPONSE_EMT | **8** | `/datum/antagonist/ert/request_911/emt` | Sol Federation EMTs | "911" |
| EMERGENCY_RESPONSE_EMAG | **8** | `/datum/antagonist/ert/pizza/false_call` | Dogginos | "Dogginos"（列表 `dogginos`） |

- 公告内容（`priority_announce`，`families_police.ogg` 音效，黄色，重要消息）：Marshal/EMT 版本说明"若一线响应者要求 SWAT 支援或报告虚假 911 呼叫，将额外向空间站收取 **$20,000**"，并附呼叫录音（`[GLOB.call_911_msg]`）；Atmos 版本由 811 调度播报；披萨版本由 Dogginos 播报"距您选择的 Dogginos：70,000 光年"，订单 $35,000。
- 幽灵投票：`SSpolling.poll_ghost_candidates(poll_question, check_jobban = ROLE_DEATHSQUAD, alert_pic = /obj/item/solfed_reporter, role_name_text = 反派人设名)`。
- 生成：实际人数 = `min(team_size, 候选数)`；出生点循环 `GLOB.emergencyresponseteamspawn`；`safe_transfer_prefs_to(is_antag = TRUE)` + `PossessByPlayer` + `add_antag_datum` + `set_assigned_role` + `send_to_late_join` + 授予通用语（LANGUAGE_SPAWNER）。
- 收费/装备：Atmos 响应者→每名扣货运账户 $15,000（`GLOB.solfed_tech_charge`）；其余→发 **品牌手机**（`gang_id = cell_phone_number`，名称 `"[号码] branded cell phone"`，`WEIGHT_CLASS_SMALL`），装备失败则掉落脚下。
- 日志：`log_game` 记录被选中者与扣费；结束时 `GLOB.cops_arrived = TRUE`。

**权限校验（3 个 proc）**：
- `pre_911_check(user)`：需硅基或船长认证（`authenticated_as_silicon_or_captain`）；`GLOB.cops_arrived` 为真则拒绝（"已经叫过了"）；有机体必须手持含 **ACCESS_CAPTAIN** 的 ID 卡；硅基直接拒绝（"硅基无法呼叫 911"）→ 返回 FALSE。
- `message_federation(user)`（联系联邦按钮，仅有机体）：同样需船长 ID 卡权限；硅基拒绝（"AI 不能联系联邦"）。
- `finalizing_solfedmessage(user)`（发送联邦消息最终确认）：先通知管理员（`message_admins`"正在考虑联系 Sol 联邦地区指挥部"）；确认弹窗"不必要的通讯可能导致巨额罚款或 **25 年联邦监狱**"；输入原因（`stripped_input`，上限 MAX_MESSAGE_LEN）→ 存入 `GLOB.fedmessage`；管理员广播（黄色 SOLFED 标签 + 管理员回复链接），开着通讯通知偏好的管理员播放 `server-ready.ogg`；`deadchat_broadcast` 播报；写入日志；播放确认音。

**`calling_911(mob/user, called_group_pretty = "EMTs", called_group = EMERGENCY_RESPONSE_EMT)`**：通知管理员 → 确认弹窗"**虚假 911 呼叫罚款 $20,000 + 5 年超级监狱**" → 展示"应与不应"清单确认 → 输入呼叫原因 → 置位 `GLOB.cops_arrived = TRUE`、`GLOB.call_911_msg`、`GLOB.caller_of_911` → 日志 + 管理员通知 + deadchat → 执行 `call_911(called_group)` → 确认音效。

**`/datum/antagonist/ert/request_911`（911 响应者基类）**：name "911 Responder"；`antag_hud_name = "hud_spacecop"`；`suicide_cry = "FOR THE SOL FEDERATION!!"`（自杀呐喊："为了 Sol 联邦！！"）；`department` 变量。
- `greet()` 任务书（**5 条**）：① 你不是 Nanotrasen 员工，你为 Sol 联邦工作，职务 [role]；② 正在响应空间站的 SolFed [department] 紧急呼叫；③ 用背包里的手机与一线响应者商议；④ 附 911 通话记录；⑤ 任务：**1.** 联系 [GLOB.caller_of_911] 协助解决问题；**2.** 保护、确保并维护空间站上 Sol 联邦公民的权利；**3.** 若身处险境/无法履职/认为 911 呼叫有误，可用背包里的 **S.W.A.T. 备用呼叫器**投票召唤 SWAT；**4.** 任务完成后用背包里的**光束撤离工具**与拖拽的人一同撤离。播放 `families_police.ogg`。

**`/datum/outfit/request_911`（基类配装）**：back `duffelbag/cops`（警察行李袋）；backpack_contents `solfed_reporter/swat_caller`×1；id_trim `space_police`。
- `post_equip()`：**打乱 ID 卡 access 列表**（`shuffle_inplace`，使 NTNet 通行密钥难以预测）；登记真实姓名与年龄；更新标签与图标。

### 5.3 警长 Marshal

- **`/datum/antagonist/ert/request_911/police`**：name "Marshal"；role "Marshal"；department "Marshal"。
- **`/datum/outfit/request_911/police`（911 Response: Marshal）**：

| 槽位 (Slot) | 物品 (Item) | 中文 (CN) |
|---|---|---|
| back | `storage/backpack/satchel` | 挎包 |
| uniform | `clothing/under/solfed` | SolFed 警长制服 |
| suit | `clothing/suit/armor/vest/sol` | 'Gordyn' 防破片背心 |
| shoes | `clothing/shoes/jackboots` | 军靴 |
| glasses | `clothing/glasses/sunglasses` | 墨镜 |
| ears | `radio/headset/headset_sec/alt` | 安保耳机（备用） |
| head | `clothing/head/soft/black` | 黑色软帽 |
| suit_store | `gun/energy/disabler` | 消解枪 |
| belt | `melee/baton/security/loaded` | 满电电击棒 |
| r_pocket / l_pocket | `flashlight/seclite` / `restraints/handcuffs` | 安保手电 / 手铐 |
| id | `card/id/advanced/solfed` | SolFed 高级身份证 |
| backpack_contents | `gun/ballistic/automatic/pistol/sol`×1、`ammo_box/magazine/c35sol_pistol`×**4**、`box/survival`×1、`box/handcuffs`×1、`solfed_reporter/swat_caller`×1、`beamout_tool`×1 | Sol 手枪 + 4 弹匣、生存盒、手铐盒、SWAT 呼叫器、光束撤离工具 |
| id_trim | `/datum/id_trim/solfed` | |

### 5.4 高级大气组 Advanced Atmospherics

- **`/datum/antagonist/ert/request_911/atmos`**：name "Adv. Atmos Tech"；role "Adv. Atmospherics Technician"；department "Advanced Atmospherics"。
- **`/datum/outfit/request_911/atmos`（811 Response: Advanced Atmospherics）**：

| 槽位 (Slot) | 物品 (Item) | 中文 (CN) |
|---|---|---|
| back | `mod/control/pre_equipped/advanced/atmos` | 高级大气 MOD 套装 |
| uniform | `clothing/under/solfed/emergencyfire` | SolFed 应急大气制服 |
| shoes | `clothing/shoes/jackboots` | 军靴 |
| ears | `radio/headset/headset_solfed/atmos` | SolFed 高级大气耳机 |
| mask | `clothing/mask/gas/atmos/glass` | 大气玻璃防毒面具 |
| belt | `storage/belt/utility/full/powertools/ircd` | 电力工具/IRC 腰带 |
| suit_store | `tank/internals/oxygen/yellow` | 黄色氧气罐 |
| id | `card/id/advanced/solfed` | SolFed 身份证 |
| backpack_contents | `box/rcd_ammo`×1、`box/smart_metal_foam`×1、`multitool`×1、`extinguisher/advanced`×1、`rwd/loaded`×1、`beamout_tool`×1、`solfed_reporter/swat_caller`×1 | RCD 弹药、智能金属泡沫、万用工具、高级灭火器、RWD、光束撤离工具、SWAT 呼叫器 |
| id_trim | `/datum/id_trim/solfed/atmos` | |

**耳机与加密钥匙（3 套）**——`/obj/item/radio/headset/headset_solfed/`：

| 耳机 (Headset) | 加密钥匙 (Key) | 频道 (Channels) | 中文 (CN) |
|---|---|---|---|
| `/atmos`（SolFed adv. atmos headset） | `/obj/item/encryptionkey/headset_solfed/atmos` | RADIO_CHANNEL_SOLFED + ENGINEERING + COMMAND | SolFed 高级大气耳机/钥匙 |
| `/sec`（SolFed adv. Security headset） | `/encryptionkey/headset_solfed/sec` | SOLFED + SECURITY + COMMAND | SolFed 高级安保耳机/钥匙 |
| `/med`（SolFed adv. Medical headset） | `/encryptionkey/headset_solfed/med` | SOLFED + MEDICAL + COMMAND | SolFed 高级医疗耳机/钥匙 |

全部：`special_channels = RADIO_SPECIAL_CENTCOM`（中央指挥部专用频道）、`radio_talk_sound = radiosound/radio/security.ogg`、图标 `med_headset`、钥匙配色 `#ebebeb#2b2793`。

### 5.5 急救员 EMT

- **`/datum/antagonist/ert/request_911/emt`**：name "Emergency Medical Technician"；role "EMT"；department "EMT"。
- **`/datum/outfit/request_911/emt`（911 Response: EMT）**：

| 槽位 (Slot) | 物品 (Item) | 中文 (CN) |
|---|---|---|
| back | `storage/backpack/medic` | 医疗背包 |
| uniform | `clothing/under/solfed/emergencymed` | SolFed 应急医护制服 |
| shoes | `clothing/shoes/jackboots` | 军靴 |
| ears | `radio/headset/headset_solfed/med` | SolFed 高级医疗耳机 |
| mask | `clothing/mask/gas/alt` | 备用防毒面具 |
| glasses | `clothing/glasses/hud/health` | 健康 HUD 眼镜 |
| head | `clothing/head/helmet/toggleable/sf_hardened/emt` | SF 加固 EMT 头盔 |
| id | `card/id/advanced/solfed` | SolFed 身份证 |
| suit | `clothing/suit/armor/sf_hardened/emt` | SF 加固 EMT 护甲 |
| gloves | `clothing/gloves/latex/nitrile` | 丁腈手套 |
| belt | `storage/backpack/duffelbag/deforest_paramedic/stocked` | Deforest 医护行李袋（满装） |
| suit_store | `tank/internals/emergency_oxygen/engi` | 工程应急氧气罐 |
| r_pocket / l_pocket | `flashlight/seclite` / `storage/medkit/civil_defense` | 安保手电 / 民防医疗包 |
| backpack_contents | `box/survival`×1、`emergency_bed`×1、`box/medipens`×1、`solfed_reporter/swat_caller`×1、`beamout_tool`×1 | 生存盒、应急床、医疗笔盒、SWAT 呼叫器、光束撤离工具 |
| id_trim | `/datum/id_trim/solfed/med` | |

### 5.6 SWAT 与军事镇压 SWAT & Military

**SWAT 武装警员 `/datum/antagonist/ert/request_911/condom_destroyer`**：
- name "Armed S.W.A.T. Officer"；role "S.W.A.T. Officer"；department "Police"。
- `greet()` 任务书（**5 条**）：① 你不是 Nanotrasen 员工，为 Sol 联邦工作；② 前来支援 911 一线响应者；③ 任务：**1.** 用背包里的手机联系一线响应者了解情况；**2.** 逮捕任何妨碍一线响应者工作的人；**3.** 若嫌疑人不服从或空间站拒绝配合，可在逮捕中**使用致命武力**；**4.** 若认为空间站叛国并向一线响应者与 SWAT 开火，用背包里的**叛国举报器**召唤军队；**5.** 完成后用光束撤离工具撤离自己和拖拽者。
- **`/datum/outfit/request_911/condom_destroyer`（911 Response: Armed S.W.A.T. Officer）**：

| 槽位 (Slot) | 物品 (Item) | 中文 (CN) |
|---|---|---|
| back | `storage/backpack` | 背包 |
| uniform | `clothing/under/solfed` | SolFed 警长制服 |
| shoes | `clothing/shoes/jackboots` | 军靴 |
| glasses | `clothing/glasses/sunglasses` | 墨镜 |
| ears | `radio/headset/headset_solfed/sec` | SolFed 高级安保耳机 |
| head | `clothing/head/helmet/sf_peacekeeper` | SF 维和者头盔 |
| belt | `gun/energy/disabler` | 消解枪 |
| suit | `clothing/suit/armor/sf_peacekeeper` | SF 维和者护甲 |
| r_pocket / l_pocket | `flashlight/seclite` / `restraints/handcuffs` | 安保手电 / 手铐 |
| id | `card/id/advanced/solfed` | SolFed 身份证 |
| l_hand | `gun/ballistic/shotgun/riot/sol` | Sol 防暴霰弹枪 |
| backpack_contents | `box/survival`×1、`box/handcuffs`×1、`melee/baton/security/loaded`×1、`box/lethalshot`×**2**、`solfed_reporter/treason_reporter`×1、`beamout_tool`×1 | 生存盒、手铐盒、满电电击棒、致命霰弹×2 盒、叛国举报器、光束撤离工具 |
| id_trim | `/datum/id_trim/solfed` | |

**Sol 联邦军队 `/datum/antagonist/ert/request_911/treason_destroyer`**：
- name "Sol Federation Military"；role "Private"（列兵）；department "Military"。
- `greet()` 任务书（**4 条**）：① 你不是 Nanotrasen 员工，为 Sol 联邦工作；② 因空间站内人员按我方 SWAT 队报告实施叛国，前来接管 [station_name()]；③ 任务：**1.** 用手机联系 SWAT 队与一线响应者了解情况；**2.** 逮捕所有参与叛国企图的嫌疑人；**3.** 为 Sol 联邦接管空间站，启动疏散程序让未犯罪公民远离现场；**4.** 必要时可使用致命武力，但只在万不得已时。
- **`/datum/outfit/request_911/treason_destroyer`（911 Response: SolFed Military）**：

| 槽位 (Slot) | 物品 (Item) | 中文 (CN) |
|---|---|---|
| uniform | `clothing/under/solfed/marines` | SolFed Espatier 迷彩制服 |
| head | `clothing/head/helmet/solfed` | SolFed MK I 战斗头盔 |
| mask | `clothing/mask/gas/alt` | 备用防毒面具 |
| gloves | `clothing/gloves/combat` | 战斗手套 |
| suit | `clothing/suit/armor/vest/sol/marine` | 'Hephaestus' 轻型护甲 |
| shoes | `clothing/shoes/jackboots` | 军靴 |
| belt | `storage/belt/military` | 军用腰带 |
| neck | `clothing/neck/mantle/solfed` | Sol 联邦披肩 |
| back | `storage/backpack` | 背包 |
| glasses | `clothing/glasses/sunglasses/solfed` | SolFed 军用护目镜 |
| ears | `radio/headset/headset_solfed/sec` | SolFed 高级安保耳机 |
| l_pocket / r_pocket | `restraints/handcuffs` / `flashlight/seclite` | 手铐 / 安保手电 |
| id | `card/id/advanced/solfed` | SolFed 身份证 |
| r_hand | `gun/ballistic/automatic/sol_rifle` | Sol 突击步枪 |
| backpack_contents | `box/handcuffs`×1、`melee/baton/security/loaded`×1、`ammo_box/magazine/c40sol_rifle/standard`×**4** | 手铐盒、满电电击棒、c40 步枪弹匣×4 |
| id_trim | `/datum/id_trim/solfed` | |

### 5.7 SolFed 通讯器 solfed_reporter

**基类 `/obj/item/solfed_reporter`（SolFed reporter，SolFed 通讯器）**：
- desc："Use this in-hand to vote to call SolFed backup. If half your team votes for it, SWAT will be dispatched."（"手持使用以投票呼叫 SolFed 增援。若半数队友投票，将派出 SWAT。"）
- icon `goofsec/icons/reporter.dmi`、icon_state `reporter_off`、`WEIGHT_CLASS_SMALL`。

| 变量 (Var) | 默认值 (Default) | 中文 (CN) |
|---|---|---|
| activated | FALSE | 是否已启用 |
| type_to_check | /datum/antagonist/ert/request_911 | 使用所需的身份 |
| type_of_callers | "911_responders" | 计票/核对的响应组 |
| announcement_source | "Sol Federation S.W.A.T." | 公告来源 |
| fine_station | TRUE | 投票完成是否罚款空间站 |
| ghost_poll_msg | "example crap" | 幽灵投票消息 |
| amount_to_summon | **4** | 召唤人数 |
| type_to_summon | /datum/antagonist/ert/request_911/condom_destroyer | 召唤的反派人设 |
| summoned_type | "swat" | 计数表 |
| cell_phone_number | "911" | 手机频道名 |
| jobban_to_check | ROLE_DEATHSQUAD | 投票封禁检查 |
| announcement_message | "Example announcement message" | 成功公告 |

**机制**：
- `pre_checks(user)`：对应响应组 `amount` 为 0 → 拒绝（"没有可投票的响应者"）；使用者没有对应 `type_to_check` 反派人设 → 拒绝（"只有响应者能使用"）。
- `attack_self()`：校验通过、未启用、组未 `declared` 时 → 启用（`reporter_on` 图标）→ 该组 `votes++` → 提示"当前票数/响应者数"→ 当 `votes >= amount * 0.5`（**半数阈值**）时：置 `declared = TRUE`；`fine_station` 为真则从货运账户扣 **$20,000**（`SOLFED_FINE_AMOUNT`，"给联邦付开到边疆的油钱"）；`priority_announce` 公告（黄色 + families_police.ogg）；幽灵投票（`jobban_to_check`、alert_pic 通讯器、`role_name_text = summoned_type`）；生成流程与 call_911 相同（出生点轮转、手机 `gang_id = cell_phone_number`、日志）。

**3 个变体**：

**① S.W.A.T. 备用呼叫器 `/obj/item/solfed_reporter/swat_caller`**：desc "投票呼叫 SolFed S.W.A.T. 增援。若半数队友投票，将派出 SWAT。"；`amount_to_summon = 6`；`type_to_summon = condom_destroyer`；`summoned_type = "swat"`；ghost_poll_msg："Sol-Fed 911 服务请求 SWAT 增援。你想成为 SWAT 成员吗？"；公告："……我们已向空间站开出 **$20,000** 账单，以支付第二次应急响应的飞行费用。请服从 SWAT 成员的所有要求。"；`questions()`（**3 项**）：情况是否需要额外 SWAT 增援 / 空间站是否阻碍你履职 / 空间站是否虚假 911 呼叫需要逮捕呼叫者——任一答"否"即取消投票。

**② 叛国举报器 `/obj/item/solfed_reporter/treason_reporter`**：desc "手持使用以投票认定空间站正在叛国。若半数队友投票，军队将接管局面。"；`type_to_check = condom_destroyer`（仅 SWAT 可用）；`type_of_callers = "swat"`；`announcement_source = "Sol Federation National Guard"`（国民警卫队）；`fine_station = FALSE`；`amount_to_summon = **12**`；`type_to_summon = treason_destroyer`；`summoned_type = "national_guard"`；ghost_poll_msg："空间站决定叛国。你想加入 Sol 联邦军队吗？"；公告全文："……我们将以**叛国罪**起诉肇事者，刑罚为死刑或不少于 **25 年超级监狱**。军队正前往空间站，将直接接管并疏散平民……放下武器投降，拒绝服从可能遭遇致命武力。"
- `questions()`（**5 项**连问，全部答"是"才生效）：① 叛国是攻击所效忠国家权威的罪行——空间站位于 Sol 联邦空间内、虽归 Nanotrasen 所有但对联邦负有忠诚，空间站今天是否实施叛国？② 空间站船员是否在安保/指挥的指使下袭击了你或 SWAT 队？③ 空间站船员是否在安保/指挥的指使下积极阻止你与 SWAT 完成目标？④ 你与 SWAT 队友是否确实无法独自处理此事？⑤ 你是否绝对确定要宣告空间站叛国？滥用将导致对你的账户采取管理措施。任何一项答"否"即取消并提示"你决定不宣告空间站叛国"。

**③ Dogginos 不付款客户举报器 `/obj/item/solfed_reporter/pizza_managers`**：desc "手持使用以在空间站拒付披萨款时投票呼叫 Dogginos 区域经理。若半数配送队投票，将派出区域经理。"；`type_to_check = /datum/antagonist/ert/pizza/false_call`；`type_of_callers = "dogginos"`；`amount_to_summon = 8`；`type_to_summon = /datum/antagonist/ert/pizza/leader/false_call`；`summoned_type = "dogginos_manager"`；`cell_phone_number = "Dogginos"`；`fine_station = FALSE`；公告："……我们派出了最优秀的区域经理处理此事。希望你们喜欢披萨，并尽快支付 **$35,000** 账单加上司机 **15% 小费**！"；ghost_poll_msg："Dogginos 正派区域经理来收披萨欠款。你准备好做客户关系了吗？"

### 5.8 披萨事件 Dogginos Pizza

**`/datum/antagonist/ert/pizza/false_call`（披萨外卖员）**：
- outfit `/datum/outfit/centcom/ert/pizza/false_call`：backpack_contents `box/survival`（生存盒）+ `knife`（刀）+ `box/ingredients/italian`（意大利食材盒）+ `solfed_reporter/pizza_managers`（披萨经理举报器）；r_hand `pizzabox/meat`（肉披萨盒）；l_hand `pizzabox/vegetable`（蔬菜披萨盒）。
- `greet()` 任务书（**3 条**）：① 你不是 Nanotrasen 员工，你是 Dogginos 的配送员；② 前来配送 Dogginos 披萨；③ 任务：**1.** 配送 [GLOB.pizza_order] 订购的披萨；**2.** 收取账单，总计 **$35,000** 外加配送司机 **15% 小费**；**3.** 若他们拒付，可召唤 Dogginos 区域经理解决。

**`/datum/antagonist/ert/pizza/leader/false_call`（区域经理）**：
- `greet()` 任务书（**2 条**）：① 你是 Dogginos 区域经理；② 任务：**1.** 收取 [GLOB.pizza_order] 欠款（$35,000 + 15% 小费）；**2.** 使用一切必要手段收款——**你背包里那把一千度的刀**会派上用场。

### 5.9 光束撤离工具 Beam-out Tool

**`/obj/item/beamout_tool`（beam-out tool，光束撤离工具）**：
- desc："Use this to begin the lengthy beam-out process to return to Sol Federation space. It will bring anyone you are pulling with you."（"使用它以开始漫长的光束撤离过程，返回 Sol 联邦空间。它会带走你拖拽的任何人与你一同撤离。"）源码注释：TODO 找办法把它做成空投舱（视觉上更酷）。
- icon `goofsec/icons/reporter.dmi`、icon_state `beam_me_up_scotty`、`WEIGHT_CLASS_SMALL`。
- `attack_self()`：使用者必须有 `/datum/antagonist/ert` 反派人设（否则警告"只有响应者能使用"）；通知管理员；`do_after(user, **30 SECONDS**)` 引导条——成功：通知管理员"已光束撤离"，拖拽者是人类则单独记录，对双方位置播放 `Repulse.ogg` 音效 + **10 个量子火花**（`do_sparks(10, TRUE, spark_type = quantum)`），随后 `qdel` 拖拽者与使用者（本体消失）；失败（引导中断）：气泡提示"光束撤离中断"。

---

## 6. SolFed 服装与装备 SolFed Clothing & Gear

> 源码路径 (Source)：`modular_nova/modules/goofsec/code/solfed_clothing.dm`（406 行）

### 6.1 制服 Uniforms

| 路径 (Path) | 名称 (Name) | 中文 (CN) | 说明 (Desc) | 护甲 (Armor) |
|---|---|---|---|---|
| `/obj/item/clothing/under/sol_peacekeeper` | sol peacekeeper uniform | Sol 维和者制服 | "军级制服配军级舒适度（完全没有），常见于 SolFed 各维和部队，通常配蓝色头盔。" 图标 `peacekeeper` | /datum/armor/clothing_under/rank_security |
| `/obj/item/clothing/under/sol_emt` | sol emergency medical uniform | Sol 应急医疗制服 | "SolFed 维和制服的复刻，为医护人员重新配色改造。" 图标 `emt` | /datum/armor/clothing_under/rank_medical |
| `/obj/item/clothing/under/solfed` | SolFed marshal's uniform | SolFed 警长制服 | "SolFed 维和制服的现代化版本，为联邦人员量身打造，时髦又实用。" 图标 `solpolice` | /datum/armor/clothing_under/rank_security |
| `/obj/item/clothing/under/solfed/emergencyfire` | SolFed emergency atmospherics uniform | SolFed 应急大气制服 | "Sol 联邦官方应急响应制服，标识其空间站破洞控制队成员，防护大气或火灾危害。" 图标 `atmosrescue` | /datum/armor/clothing_under/atmos_adv |
| `/obj/item/clothing/under/solfed/emergencymed` | SolFed emergency paramedic uniform | SolFed 应急医护制服 | "Sol 联邦官方应急响应制服，标识其创伤医疗队成员，防护病毒或化学危害。" 图标 `medrescue` | —（继承） |
| `/obj/item/clothing/under/solfed/officer` | SolFed high-ranking official uniform | SolFed 高官制服 | "Sol 联邦武装部队高级官员穿着的制服。" 图标 `solfed_official` | — |
| `/obj/item/clothing/under/solfed/officer_lowrnk` | SolFed low-ranking official uniform | SolFed 低阶官员制服 | "Sol 联邦武装部队低级官员穿着的制服。" 图标 `solfed_enl` | — |
| `/obj/item/clothing/under/solfed/official_civil` | SolFed civil services uniform | SolFed 民政服务制服 | "Sol 联邦民政服务部官员穿着的制服。" 图标 `solfed_civil` | — |
| `/obj/item/clothing/under/solfed/official_social` | SolFed social services uniform | SolFed 社会服务制服 | "Sol 联邦社会服务部官员穿着的制服。" 图标 `solfed_social` | — |

除注明外均：`HAS_SENSORS`（有传感器）、`random_sensor = FALSE`（随机传感器关闭）、`inhand_icon_state = null`；图标来自本模块 `icons/obj/uniforms.dmi` 与 `icons/mob/uniforms*.dmi`（含数字化 `_digi` 变体）。

### 6.2 护甲数值 Armor Values

| 护甲数据 (Armor Datum) | melee | bullet | laser | energy | bomb | bio | fire | acid | wound | 中文 (CN) |
|---|---|---|---|---|---|---|---|---|---|---|
| `/datum/armor/clothing_under/solfed_response_standard` | 50 | 50 | 50 | 50 | 50 | 25 | 25 | 25 | **30** | 标准响应（NT 安保 ERT 防护级变体：护甲提升、无 EVA） |
| `/datum/armor/clothing_under/solfed_response_grand` | 80 | 80 | 70 | 60 | 80 | 20 | 50 | 50 | **45** | 高级响应（NT 资产保护级变体：护甲提升、无 EVA） |

### 6.3 头盔与防弹衣 Helmets & Vests

**头盔**：

| 路径 (Path) | 名称 (Name) | 中文 (CN) | 关键属性 (Key Stats) |
|---|---|---|---|
| `/obj/item/clothing/head/helmet/solfed` | SolFed MK I Combat helmet | SolFed MK I 战斗头盔 | 集成灯（OVERLAY_LIGHT_DIRECTIONAL，**射程 4 / 功率 1 / 色 #fff9f3**）、`TRAIT_HEAD_INJURY_BLOCKED`（防头部受伤）、`SNUG_FIT`、**无狗用版本**（dog_fashion = null，"纯粹为了气 Ian"）、护甲 = 标准响应级、灰阶 `solfed_goggles` 配置 `#808080`、`mark_one_helmet` 图标、"没带子，廉价量产的，适合低级别威胁任务" |
| `/obj/item/clothing/head/helmet/solfed/mk2` | SolFed MK II Combat helmet | SolFed MK II 战斗头盔 | 比 MK I 更结实、保留集成灯、**这次有带子了**、护甲 = 高级响应级、`mark_two_helmet` 图标 |

- 灯控 proc：`toggle_helmet_light()` / `turn_on()` / `turn_off()`，`attack_self()` 即开关灯（`set_light_on(TRUE/FALSE)`）。

**防弹衣与披肩**：

| 路径 (Path) | 名称 (Name) | 中文 (CN) | 说明 (Desc) | 护甲 (Armor) |
|---|---|---|---|---|
| `/obj/item/clothing/suit/armor/vest/sol` | 'Gordyn' flak vest | 'Gordyn' 防破片背心 | "轻量装甲夹克，常见于需要护甲但觉得全装背心太笨重/不必要的 SolFed 人员。" 图标 `flak`，警长用 | 继承 rank_security |
| `/obj/item/clothing/suit/armor/vest/sol/marine` | 'Hephaestus' light armor | 'Hephaestus' 轻型护甲 | "穿越太空、雪原、海洋与崎岖地形，'Hephaestus' 是 Sol 联邦最独特的战斗背心之一，rimworlds 战争年代的老装备，实用但过时。" 图标 `hephaestus`，灰阶 `vestcam` `#4d4d4d` | 标准响应级 |
| `/obj/item/clothing/suit/armor/vest/sol/marine/mk2` | 'Hercules' heavy armor | 'Hercules' 重型护甲 | "Sol 联邦最全能、最结实的重型装甲背心与衬垫，在最陌生的环境中保护士兵免受最危险的威胁。" 图标 `hercules`（含数字化 `_digi`） | 高级响应级 |
| `/obj/item/clothing/neck/mantle/solfed` | Sol Federation mantle | Sol 联邦披肩 | "采用最先进的发光衬里，便于在恶劣环境中发现倒下的 SolFed 人员。戴着也好看。" 图标 `recovermantle`；`worn_overlays()` 非手持时叠加**自发光（emissive）图层** `[icon_state]-emissive` | /datum/armor/clothing_under/rank_security |

### 6.4 配饰与护目镜 Accessories & Goggles

- **`/obj/item/clothing/glasses/sunglasses/solfed`（robust military goggles，结实军用护目镜）**：desc "一种奇怪的老技术，现代化后变得结实得多。" 图标 `federal_goggles`；灰阶 `solfed_goggles` 配置 `#4d4d4d`；`glass_colour_type = gray`（灰色镜片染色）。
- **`/obj/item/clothing/under/solfed/marines`（SolFed Espatier uniform，SolFed Espatier 制服）**：desc "SolFed Espatier 军团的迷彩制服，通常担任星舰舰队（SFSF）与太空卫队（SFSG）的舰船安保；同时充当简单的太空步兵，被许多太空人戏称为'太空陆战队'。" 图标 `solfed_camo`；灰阶 `solfedcamo` 配置 `#4d4d4d#333333#292929`；**`can_adjust = FALSE`（不可调整）**。

### 6.5 军衔绶带 Rank Ribbons

**`/obj/item/clothing/accessory/nova/solfedribbon`（SolFed rank ribbon，SolFed 军衔绶带）**："一条普通的军用绶带。" 灰阶 `#FFD700`（金色），配置 `solfedribbons`，`minimize_when_attached = TRUE`（佩戴时缩小）。图标状态链：

| 变体 (Variant) | post_init_icon_state | 变体 (Variant) | post_init_icon_state |
|---|---|---|---|
| 基类 rank1 | `star_arr_ribbon_1` | `/rank7` | `star_ribbon_3` |
| `/rank2` | `star_arr_ribbon_2` | `/rank8` | `arr_ribbon_1` |
| `/rank3` | `star_sw_ribbon_1` | `/rank9` | `arr_ribbon_2` |
| `/rank4` | `star_sw_ribbon_2` | `/rank10` | `arr_ribbon_3` |
| `/rank5` | `star_ribbon_1` | `/rank11` | `sw_ribbon_1` |
| `/rank6` | `star_ribbon_2` | `/rank12` | `sw_ribbon_2` |
| — | — | `/rank13` | `sw_ribbon_3` |

> 注：源码中 rank9 的 icon_state 误写为 `/rank8`、rank10 误写为 `/rank9`、rank11 误写为 `/rank10`、rank12 误写为 `/rank11`、rank13 误写为 `/rank12`（源码原样，疑似笔误）。

**`/obj/item/clothing/accessory/nova/acc_medal/neckpin/solfed/official`（SolFed Official neckpin，SolFed 官员颈针）**："一枚特殊的金色颈针，以示对联邦的绝对忠诚。" 灰阶 `#ffff66#0099ff`。

### 6.6 自动贩卖机与耳机 Vending & Headsets

**SolFed 服装贩卖机 `/obj/machinery/vending/access/solfed`（Solfed Outfitting Station，SolFed 装备站）**：
- desc "为联邦成员提供特种服装的自动贩卖机。"；广告语："穿出风格办文书！；光荣属于联邦！；你有权时尚！；现在你也能成为一直想当的时尚警察！；记住，时尚不是犯罪！"
- 图标 `command_vendor/icons/vending.dmi` / `solfeddrobe`，拒绝动画 `solfeddrobe-deny`、灯罩 `wardrobe-light-mask`；`vend_reply = "Thank you for using the CommDrobe!"`；`auto_build_products = TRUE`；`payment_department = null`；`shut_up = TRUE`；灯光 COLOR_BRIGHT_BLUE；补充罐 `/obj/item/vending_refill/wardrobe/solfed_wardrobe`（机器名 "SolfedDrobe"）。
- **货物清单 `build_access_list`（ACCESS_CENT_CAPTAIN 专属，最高权限，只有联邦人能拿到）**：
  - 军衔绶带 基类 + rank2~rank13 各 **4** 件（13×4）
  - SolFed 制服 4 款（officer / officer_lowrnk / official_civil / official_social）各 **4** 件
  - 官员颈针 **8** 件
  - 闪光弹盒 `box/flashbangs` ×**2**、手铐盒 `box/handcuffs` ×**4**、NRI 信号弹盒 `box/nri_flares` ×**16**

**耳机与加密钥匙**：

| 耳机 (Headset) | 名称 (Name) | 中文 (CN) | 钥匙 (Key) | 备注 (Notes) |
|---|---|---|---|---|
| `/obj/item/radio/headset/headset_solfed/officials` | SolFed Officials Headset | SolFed 官员耳机 | `encryptionkey/headset_solfed/squadleader` | 图标 `com_headset` |
| `/obj/item/radio/headset/headset_solfed/espatier` | SolFed Espatier headset | SolFed Espatier 耳机 | `encryptionkey/headset_solfed/sec` | 图标 `com_headset_alt`；**`Initialize()` 附加耳部防护组件**（wearertargeting/earprotection，ITEM_SLOT_EARS）；安保通话音 |
| `/espatier/corpsman` | Solfed Espatier Corpsman Headset | Espatier 医护兵耳机 | `encryptionkey/headset_solfed/med` | 医疗频道 |
| `/espatier/engineer` | Solfed Espatier Engineer Headset | Espatier 工程师耳机 | `encryptionkey/headset_solfed/atmos` | 工程/大气频道 |
| `/espatier/squadleader` | Solfed Espatier Squadleader Headset | Espatier 队长耳机 | `encryptionkey/headset_solfed/squadleader` | 全频道 |
| `/obj/item/encryptionkey/headset_solfed/squadleader` | SolFed grand encryption key | SolFed 大密钥 | — | `RADIO_SPECIAL_CENTCOM`；频道 SOLFED + ENGINEERING + MEDICAL + SECURITY + COMMAND（**5 频道全通**）；图标 `cypherkey_syndicate`，配色 `#ebebeb#2b2793` |

**军用胸挂 `/obj/item/storage/belt/military/solfed`（solfed chest rig，SolFed 胸挂）**："联邦军事人员穿戴的一套战术织带。"；存储类型 `/datum/storage/military_belt/solfed`，`max_specific_storage = WEIGHT_CLASS_NORMAL`；`PopulateContents()`：**c40sol 步枪弹匣 ×4** + **满电电击棒 ×1**。

---

*文档完。全量覆盖 goofsec 模块全部 7 个 .dm / 2,816 行源码；数值、路径、颜色代码均与源码逐字核对。*
