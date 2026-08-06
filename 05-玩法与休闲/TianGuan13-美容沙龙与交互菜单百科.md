# TianGuan13 美容沙龙与交互菜单百科 / Beauty Salon & Interaction Menu Encyclopedia

> **版本 / Version**：NovaSector 分支（TianGuan13-master）
> **数据来源 / Data source**：源码逐文件提取，非文档转述。所有数值、类型路径、机制均直接取自 DM 源码。
> **覆盖范围 / Scope**：美容沙龙模块（`modular_nova/modules/salon/`，12 个 DM 文件，811 行）+ 交互菜单系统模块（`modular_nova/modules/interaction_menu/`，2 个 DM 文件，653 行）。含理发师职业、理发椅、Fab-O-Vend 售货机、全部理发美容物品，以及交互组件 / 交互 datum 全系统。

---

## 目录 / Table of Contents

- [一、美容沙龙模块总览 / Salon Module Overview](#一美容沙龙模块总览--salon-module-overview)
- [二、理发师职业 / Barber Job](#二理发师职业--barber-job)
- [三、理发椅 / Barber Chair](#三理发椅--barber-chair)
- [四、Fab-O-Vend 美容售货机 / Fab-O-Vend BarberVend](#四fab-o-vend-美容售货机--fab-o-vend-barbervend)
- [五、理发师制服与衣装 / Barber Uniform & Clothing](#五理发师制服与衣装--barber-uniform--clothing)
- [六、电动毛皮染色器 / Electric Fur Dyer](#六电动毛皮染色器--electric-fur-dyer)
- [七、发圈 / Hair Ties](#七发圈--hair-ties)
- [八、烘手器 / Hand Dryer](#八烘手器--hand-dryer)
- [九、杂项物品全录 / Misc Items](#九杂项物品全录--misc-items)
- [十、精密移液管 / Precision Pipette](#十精密移液管--precision-pipette)
- [十一、理发剪 / Barber's Scissors](#十一理发剪--barbers-scissors)
- [十二、美容喷雾 / Salon Sprays](#十二美容喷雾--salon-sprays)
- [十三、直剃刀 / Straight Razor](#十三直剃刀--straight-razor)
- [十四、材质与音效资源 / Icons & Sounds](#十四材质与音效资源--icons--sounds)
- [十五、交互菜单系统总览 / Interaction Menu Overview](#十五交互菜单系统总览--interaction-menu-overview)
- [十六、交互组件 / Interaction Component](#十六交互组件--interaction-component)
- [十七、交互 Datum / Interaction Datum](#十七交互-datum--interaction-datum)
- [十八、内置 JSON 交互预设 / Built-in JSON Interactions](#十八内置-json-交互预设--built-in-json-interactions)
- [十九、核心常量与联动定义 / Core Defines & Related Systems](#十九核心常量与联动定义--core-defines--related-systems)
- [二十、源码路径索引 / Source Path Index](#二十源码路径索引--source-path-index)

---

# 第一部分：美容沙龙 / Part 1 — Beauty Salon

## 一、美容沙龙模块总览 / Salon Module Overview

> 源码目录：`modular_nova/modules/salon/`（`code/` 12 个 DM 文件共 811 行 + `icons/` 5 个 DMI + `sound/` 2 个 OGG）

美容沙龙模块为空间站提供完整的理发美容玩法：新增**理发师职业**（2 个岗位）、**理发椅**、**Fab-O-Vend 美容售货机**以及一整套理发美容物品（电剃刀、直剃刀、理发剪、发圈、毛皮染色器、量子染发剂、秃顶喷雾、理发师之助等）。所有文案均走 `LANG()` 本地化键（`obj.*` 命名空间），源码中的实际显示文本由 i18n 配置提供。

**文件清单 / File list**：

| # | 文件 / File | 行数 / Lines | 内容 / Content |
|---|---|---|---|
| 1 | `code/barber.dm` | 68 | 理发师职业、初始装备、储物柜、出生点 |
| 2 | `code/barber_chair.dm` | 5 | 理发椅（舒适椅变体） |
| 3 | `code/barbervend.dm` | 38 | Fab-O-Vend 售货机 + 补货罐 |
| 4 | `code/clothing.dm` | 6 | 理发师制服 |
| 5 | `code/fur_dyer.dm` | 130 | 电动毛皮染色器（标记色/整体色双模式） |
| 6 | `code/hair_tie.dm` | 135 | 发圈（4 变体）+ 发圈弹丸（2 种） |
| 7 | `code/hand_dryer.dm` | 29 | 烘手器（蜥蜴之息 3000） |
| 8 | `code/misc_items.dm` | 206 | 口红盒/量子口红/梳子/发型杂志/碎发/电剃刀/理发店招牌/香水盒 |
| 9 | `code/pipette.dm` | 9 | 精密移液管 |
| 10 | `code/scissors.dm` | 88 | 理发剪（发型/胡型双操作） |
| 11 | `code/sprays.dm` | 39 | 4 种美容喷雾 |
| 12 | `code/straight_razor.dm` | 58 | 直剃刀 |

**共享资源 / Shared assets**：`icons/`：`vendor.dmi`（售货机）、`mixer.dmi`、`items.dmi`（物品）、`dryer.dmi`（烘手器）、`chair.dmi`（理发椅）；`sound/`：`haircut.ogg`（剪发声）、`drying.ogg`（烘干声）。

**关联访问权限 / Related access**：`ACCESS_BARBER`（理发师权限，储物柜与售货机均需）。

**关键机制速览 / Key mechanics**：

- 理发流程：电剃刀刮头/刮脸（5 秒）、直剃刀刮脸（10 秒）、理发剪换发型（60 秒，专家 45 秒）/换胡型（20 秒，专家 15 秒）、发圈换发型（即时，脱下恢复）。
- 毛皮染色器：20 秒 `do_after`，需电池组件供电（`/datum/component/cell`），可染 3 个突变色槽位或单块身体标记。
- 发圈可当远程武器弹射（普通发圈 0 伤害 + 1 秒击倒；辛迪加发圈 10 伤害 + 30 耐力 + 2 秒眼糊 + 8 秒抽搐）。
- 理发产生 `/obj/effect/decal/cleanable/hair` 碎发污渍（理发剪普通模式成功时）。

---

## 二、理发师职业 / Barber Job

> 源码：`modular_nova/modules/salon/code/barber.dm`（68 行）

### 2.1 职业定义 / Job Datum — `/datum/job/barber`

| 字段 / Field | 值 / Value | 说明 / Notes |
|---|---|---|
| `title` | `JOB_BARBER` | 职业名常量 |
| `description` | "Run your salon and meet the crews sanitary needs, such as hair cutting, massaging and more!" | 经营你的沙龙，满足船员们的卫生需求——理发、按摩等等！ |
| `faction` | `FACTION_STATION` | 空间站阵营 |
| `total_positions` | **2** | 总岗位数 2 |
| `spawn_positions` | **2** | 出生岗位数 2 |
| `supervisors` | `SUPERVISOR_HOP` | 上级：人事主管（HoP） |
| `exp_granted_type` | `EXP_TYPE_CREW` | 授予船员经验类型 |
| `config_tag` | `"BARBER"` | 配置标签 |
| `outfit` | `/datum/outfit/job/barber` | 初始装备 |
| `plasmaman_outfit` | `/datum/outfit/plasmaman` | 等离子人替换装备 |
| `paycheck` | `PAYCHECK_CREW` | 船员级薪资 |
| `paycheck_department` | `ACCOUNT_SRV` | 服务部账户 |
| `display_order` | `JOB_DISPLAY_ORDER_BARBER` | 显示顺序 |
| `bounty_types` | `CIV_JOB_BASIC` | 赏金类型：民用基础 |
| `departments_list` | `/datum/job_department/service` | 隶属服务部 |
| `family_heirlooms` | `/obj/item/hairbrush/comb`、`/obj/item/razor` | 家族传家宝：梳子、电剃刀 |
| `job_flags` | `JOB_ANNOUNCE_ARRIVAL \| JOB_CREW_MANIFEST \| JOB_EQUIP_RANK \| JOB_CREW_MEMBER \| JOB_NEW_PLAYER_JOINABLE \| JOB_REOPEN_ON_ROUNDSTART_LOSS \| JOB_ASSIGN_QUIRKS \| JOB_CAN_BE_INTERN` | 到岗播报 / 船员名册 / 授予军衔 / 船员成员 / 新玩家可加入 / 回合开始损失后重开 / 分配怪癖 / 可当实习生 |

### 2.2 初始装备 / Job Outfit — `/datum/outfit/job/barber`

| 槽位 / Slot | 物品 / Item | 说明 / Notes |
|---|---|---|
| `name` | "Barber" | 装备名 |
| `jobtype` | `/datum/job/barber` | 关联职业 |
| `glasses` | `/obj/item/clothing/glasses/sunglasses` | 太阳镜 |
| `belt` | `/obj/item/modular_computer/pda` | PDA |
| `ears` | `/obj/item/radio/headset/headset_srv` | 服务部耳机 |
| `uniform` | `/obj/item/clothing/under/rank/civilian/barber` | 理发师制服 |
| `shoes` | `/obj/item/clothing/shoes/laceup` | 系带皮鞋 |
| `id_trim` | `/datum/id_trim/job/barber` | ID 装饰 |
| `skillchips` | `/obj/item/skillchip/hair_expert` | 技能芯片：发型专家（缩短理发时间） |

### 2.3 理发师储物柜 / Secure Locker — `/obj/structure/closet/secure_closet/barber`

- `name`："barber's locker" / 理发师储物柜
- `icon_state`：`"barber"`；`icon`：`modular_nova/master_files/icons/obj/closet.dmi`
- `req_access`：`ACCESS_BARBER`（需理发师权限）
- **生成内容 / PopulateContents()**（共 19 件）：外科口罩、蓝色医疗刷手服、外科围裙、马甲、紫色律师制服、紫色律师夹克（toggle）、电剃刀、直剃刀、梳子（hairbrush/comb）、理发剪、毛皮染色器、染色喷枪（dyespray）、口红盒、量子染发剂喷雾、理发师之助喷雾、清洁剂喷雾、抹布、医疗包。

### 2.4 出生点 / Spawn Landmark — `/obj/effect/landmark/start/barber`

- `name`："Barber"；`icon_state`："Barber"
- `icon`：`modular_nova/master_files/icons/mob/landmarks.dmi`

---

## 三、理发椅 / Barber Chair

> 源码：`modular_nova/modules/salon/code/barber_chair.dm`（5 行）

### 3.1 `/obj/structure/chair/comfy/barber_chair`

| 字段 / Field | 值 / Value |
|---|---|
| `name` | "barber's chair" / 理发椅 |
| `desc` | "You sit in this, and your hair shall be cut." / 你坐进这里，你的头发将被修剪。 |
| `icon` | `modular_nova/modules/salon/icons/chair.dmi` |
| `icon_state` | `"barber_chair"` |

为舒适椅（comfy chair）的子类，仅定义外观与名称，无额外逻辑——理发师可让顾客坐在椅上接受服务。

---

## 四、Fab-O-Vend 美容售货机 / Fab-O-Vend BarberVend

> 源码：`modular_nova/modules/salon/code/barbervend.dm`（38 行）

### 4.1 售货机 / Vending Machine — `/obj/machinery/vending/barbervend`

| 字段 / Field | 值 / Value | 说明 / Notes |
|---|---|---|
| `name` | "Fab-O-Vend" | — |
| `desc` | "It would seem it vends dyes, and other stuff to make you pretty." / 它似乎在出售染料和其他让你变漂亮的东西。 | — |
| `icon` | `modular_nova/modules/salon/icons/vendor.dmi` | — |
| `icon_state` | `"barbervend"` | — |
| `req_access` | `ACCESS_BARBER` | 需理发师权限 |
| `refill_canister` | `/obj/item/vending_refill/barbervend` | 补货罐 |
| `default_price` | `PAYCHECK_CREW` | 普通商品价格 |
| `extra_price` | `PAYCHECK_COMMAND` | 高级商品价格 |
| `payment_department` | `ACCOUNT_SRV` | 服务部账户 |
| `allow_custom` | `TRUE` | 允许自定义价格 |

**标语 / Slogans**（4 条）："Spread the colour, like butter, onto toast... Onto their hair."（像抹黄油一样把颜色抹到吐司上……抹到他们的头发上。）/ "Sometimes, I dream about dyes..."（有时我梦到染料……）/ "Paint 'em up and call me Mr. Painter."（涂好他们，叫我油漆先生。）/ "Look brother, I'm a vendomat, I solve practical problems."（看啊兄弟，我是台售货机，我解决实际问题。）

**广告 / Ads**（5 条）："Cut 'em all!"（全剪了！）/ "To sheds!"（剪成秃瓢！）/ "Hair be gone!"（头发消失！）/ "Prettify!"（变美！）/ "Beautify!"（美化！）

**售货回复 / Vend replies**（3 条）："Come again!"（欢迎再来！）/ "Buy another!"（再买一个！）/ "Dont you love your new look?"（你难道不爱你的新造型吗？）

**普通商品 / Products**（8 种）：

| 物品 / Item | 数量 / Count |
|---|---|
| `/obj/item/reagent_containers/spray/quantum_hair_dye`（量子染发剂） | 3 |
| `/obj/item/reagent_containers/spray/baldium`（秃顶喷雾） | 3 |
| `/obj/item/reagent_containers/spray/barbers_aid`（理发师之助） | 3 |
| `/obj/item/clothing/head/hair_tie`（发圈） | 3 |
| `/obj/item/dyespray`（染色喷枪） | 5 |
| `/obj/item/hairbrush`（发刷） | 3 |
| `/obj/item/hairbrush/comb`（梳子） | 3 |
| `/obj/item/fur_dyer`（毛皮染色器） | 1 |

**高级商品 / Premium**（7 种）：

| 物品 / Item | 数量 / Count |
|---|---|
| `/obj/item/scissors`（理发剪） | 3 |
| `/obj/item/reagent_containers/spray/super_barbers_aid`（超级理发师之助） | 3 |
| `/obj/item/storage/box/lipsticks`（口红盒） | 3 |
| `/obj/item/lipstick/quantum`（量子口红） | 1 |
| `/obj/item/razor`（电剃刀） | 1 |
| `/obj/item/storage/box/perfume`（香水盒） | 1 |
| `/obj/item/skillchip/hair_expert`（发型专家芯片） | 2 |

### 4.2 补货罐 / Refill Canister — `/obj/item/vending_refill/barbervend`

- `machine_name`："barber vend resupply" / 理发售货机补给
- `icon_state`：`"refill_snack"`（源码注释：因暂无专属贴图，使用通用物品补货罐贴图）

---

## 五、理发师制服与衣装 / Barber Uniform & Clothing

> 源码：`modular_nova/modules/salon/code/clothing.dm`（6 行）

### 5.1 `/obj/item/clothing/under/rank/civilian/barber`

| 字段 / Field | 值 / Value |
|---|---|
| `name` | "barber's uniform" / 理发师制服 |
| `desc` | "Stark white pants and a fancy hot-pink shirt with a low neckline, though it's preferred to wear it even lower. It's the hottest new trend, darling!" / 纯白裤子和一件低领口的时髦艳粉色衬衫，不过大家更喜欢穿得再低一点。这可是当下最火的潮流，亲爱的！ |
| `icon` | `modular_nova/master_files/icons/obj/clothing/under/civilian.dmi` |
| `worn_icon` | `modular_nova/master_files/icons/mob/clothing/under/civilian.dmi` |
| `icon_state` | `"barber"` |

---

## 六、电动毛皮染色器 / Electric Fur Dyer

> 源码：`modular_nova/modules/salon/code/fur_dyer.dm`（130 行）

### 6.1 模式定义 / Mode Defines

| 定义 / Define | 值 / Value | 说明 / Notes |
|---|---|---|
| `COLOR_MODE_SPECIFIC` | `"Specific Marking"` | 指定标记模式（染单块身体标记） |
| `COLOR_MODE_GENERAL` | `"General Color"` | 整体颜色模式（染突变色槽位） |

### 6.2 `/obj/item/fur_dyer`

| 字段 / Field | 值 / Value |
|---|---|
| `name` | "electric fur dyer" / 电动毛皮染色器 |
| `desc` | "Dye that is capable of recoloring fur in a mostly permanent way." / 能以近乎永久的方式重新给毛皮染色的染料。 |
| `icon` | `modular_nova/modules/salon/icons/items.dmi` |
| `icon_state` | `"fur_sprayer"` |
| `w_class` | `WEIGHT_CLASS_TINY`（微型） |
| `mode` | 默认 `COLOR_MODE_SPECIFIC` |

**机制 / Mechanics**：

- **Initialize**：附加 `/datum/component/cell`（电池组件），染色需耗电（`item_use_power`）。
- **attack_self（右键切换模式）**：在 `COLOR_MODE_SPECIFIC` ↔ `COLOR_MODE_GENERAL` 之间切换，`balloon_alert` 提示当前模式。
- **attack（对目标使用）**：仅对人类（`ishuman`）生效，按当前模式分发到 `dye_marking` 或 `dye_general`。

### 6.3 整体染色 / `dye_general()` proc

1. `tgui_alert` 选择突变色槽位：**"One" / "Two" / "Three"**（对应 `FEATURE_MUTANT_COLOR` / `_TWO` / `_THREE`）。
2. 电源检查：`item_use_power(power_use_amount, user, TRUE) & COMPONENT_POWER_SUCCESS`，失败提示"电量不足"类错误。
3. `tgui_color_picker` 选色（默认白色），`sanitize_hexcolor` 清洗。
4. 广播消息 → `do_after(user, 20 SECONDS, target_human)`。
5. 成功后写入对应 DNA 特征槽位 → `regenerate_icons()` 刷新外观 → 扣除电量 → 播放 `sound/effects/spray2.ogg`（音量 50）。

### 6.4 指定标记染色 / `dye_marking()` proc

1. 复制目标 `dna.body_markings`；若无任何标记则报错。
2. 电源检查同上。
3. 取用户当前瞄准部位 `zone_selected`；该部位无标记则报错。
4. `tgui_input_list` 在该部位标记列表中选标记 ID。
5. `tgui_color_picker` 选色 + `sanitize_hexcolor`。
6. 广播消息 → `do_after(user, 20 SECONDS, target_human)`。
7. 成功后将 `body_markings[部位][标记ID] = 颜色` 写回 → `regenerate_icons()` → 扣电 → 播放喷雾声。

---

## 七、发圈 / Hair Ties

> 源码：`modular_nova/modules/salon/code/hair_tie.dm`（135 行）

### 7.1 基础发圈 / `/obj/item/clothing/head/hair_tie`

| 字段 / Field | 值 / Value | 说明 / Notes |
|---|---|---|
| `name` | "hair tie" / 发圈 | — |
| `desc` | "An elastic hair tie, made to hold your hair up!" / 一条弹力发圈，用来把头发扎起来！ | — |
| `icon` / `worn_icon` | `modular_nova/modules/salon/icons/items.dmi` | — |
| `icon_state` | `"hairtie"` | — |
| `worn_icon_state` | `"hair_tie_worn_no_icon"` | 佩戴无贴图（隐藏） |
| `lefthand_file` / `righthand_file` | `items.dmi` | — |
| `inhand_icon_state` | `"hair_tie_worn_no_icon"` | — |
| `w_class` | `WEIGHT_CLASS_TINY` | 微型 |
| `custom_price` | `PAYCHECK_CREW * 0.2` | 自定义售价 = 船员薪资 × 0.2 |

**变量 / Vars**：

| 变量 / Var | 默认值 / Default | 说明 / Notes |
|---|---|---|
| `picked_hairstyle` | null | 选定的发型字符串（`set_hairstyle()` 读取） |
| `actual_hairstyle` | null | 佩戴者原始发型存储 |
| `projectile_to_fire` | `/obj/projectile/bullet/hair_tie` | 弹射时使用的弹丸类型 |
| `fire_speed` | **3 SECONDS** | 弹射前 `do_after` 时长 |
| `projectile_aim_radius` | **30** | 弹射随机瞄准半径（度） |

**机制 / Mechanics**：

- **装备限制 `mob_can_equip`**：若用户发型为 `"Bald"`（光头）则**无法装备**。
- **attack_self（选择发型）**：`tgui_input_list` 从 `SSaccessories.hairstyles_list` 选发型；拒绝 `"Bald"`；选择后 `balloon_alert` 显示发型名并存入 `picked_hairstyle`。
- **equipped（佩戴生效）**：保存佩戴者原发型到 `actual_hairstyle`，立即 `set_hairstyle(picked_hairstyle, update = TRUE)` 并广播消息。
- **dropped（脱下恢复）**：将发型恢复为 `actual_hairstyle` 并广播。
- **click_alt（Alt 点击弹射）**：仅当发圈在手中（`ITEM_SLOT_HANDS`）时可用，否则 `CLICK_ACTION_BLOCKING`；调用 `flick_hair_tie(user)`。
- **`flick_hair_tie()` proc**：`do_after(user, fire_speed, src)` → 创建弹丸 → 克隆名称/贴图 → 附加 `/datum/element/projectile_drop`（落地生成新发圈）→ 以 `dir2angle(user.dir) + rand(-projectile_aim_radius, projectile_aim_radius)` 角度发射 → 播放 `sound/items/weapons/effects/batreflect.ogg`（音量 25）→ `qdel(src)` 销毁本体（由 projectile_drop 生成新发圈代替）。

### 7.2 变体 / Variants

| 类型 / Type | name | desc | 差异 / Differences |
|---|---|---|---|
| `/obj/item/clothing/head/hair_tie/scrunchie` | "scrunchie" / 发圈（绒球款） | "An elastic hair tie, its fabric is velvet soft." / 弹力发圈，面料如天鹅绒般柔软。 | `icon_state` = `"hairtie_scrunchie"` |
| `/obj/item/clothing/head/hair_tie/plastic_beads` | "colorful hair tie" / 彩色发圈 | "An elastic hair tie, adornished with colorful plastic beads." / 缀满彩色塑料珠的弹力发圈。 | `icon_state` = `"hairtie_beads"`；`custom_materials` = 塑料 `HALF_SHEET_MATERIAL_AMOUNT` |
| `/obj/item/clothing/head/hair_tie/syndicate` | "Syndicate hair tie" / 辛迪加发圈 | "An elastic hair tie with a metal clip, brandishing the logo of the Syndicate." / 带金属夹的弹力发圈，印着辛迪加的标志。 | `fire_speed` = **1.5 SECONDS**；弹丸 = `/obj/projectile/bullet/hair_tie/syndicate`；`projectile_aim_radius` = **0**（精准瞄准） |

### 7.3 发圈弹丸 / Projectiles

**`/obj/projectile/bullet/hair_tie`**（普通）：

| 字段 / Field | 值 / Value |
|---|---|
| `icon` | `items.dmi`，`icon_state` = `"hairtie"` |
| `hitsound` | `sound/items/weapons/genhit.ogg` |
| `damage` | **0**（纯击倒） |
| `sharpness` | `NONE` |
| `shrapnel_type` | `NONE`（不嵌体） |
| `impact_effect_type` | null |
| `ricochet_chance` | **0** |
| `range` | **7** |
| `knockdown` | **1 SECONDS**（击倒 1 秒） |

**`/obj/projectile/bullet/hair_tie/syndicate`**（辛迪加）：`damage` = **10**；`stamina` = **30**；`eyeblur` = **2 SECONDS**；`jitter` = **8 SECONDS**（源码注释：被这玩意儿打中可太惨了）。

---

## 八、烘手器 / Hand Dryer

> 源码：`modular_nova/modules/salon/code/hand_dryer.dm`（29 行）

### 8.1 `/obj/machinery/dryer`

| 字段 / Field | 值 / Value |
|---|---|
| `name` | "hand dryer" / 烘手器 |
| `desc` | "The Breath Of Lizards-3000, an experimental dryer." / 蜥蜴之息 3000，一款实验性烘干机。 |
| `icon` | `modular_nova/modules/salon/icons/dryer.dmi` |
| `icon_state` | `"dryer"` |
| `density` | `FALSE`（不挡路） |
| `anchored` | `TRUE`（锚定） |
| `busy` | `FALSE`（忙碌标记） |

**机制 / Mechanics（attack_hand）**：

- 机械人（cyborg）与 AI 无法使用。
- `can_interact` 校验；忙碌时提示"请稍候"。
- 使用中播放 `modular_nova/modules/salon/sound/drying.ogg`（音量 50），留下指纹。
- `busy = TRUE` → `do_after(user, 4 SECONDS, src)` → 成功则广播烘干完成消息；无论成败最终复位 `busy = FALSE`。

---

## 九、杂项物品全录 / Misc Items

> 源码：`modular_nova/modules/salon/code/misc_items.dm`（206 行）

### 9.1 口红盒 / Lipstick Box — `/obj/item/storage/box/lipsticks`

- `name`："lipstick box" / 口红盒
- **内容 / PopulateContents()**：`/obj/item/lipstick`（红）、`/obj/item/lipstick/purple`（紫）、`/obj/item/lipstick/jade`（玉）、`/obj/item/lipstick/black`（黑）。

### 9.2 量子口红 / Quantum Lipstick — `/obj/item/lipstick/quantum`

- `name`："quantum lipstick" / 量子口红
- **机制（attack）**：须口红已打开（`open`）且目标为 mob；非人类报错。`INVOKE_ASYNC` 异步执行 `async_set_color`：
  1. `tgui_color_picker` 选色（默认白色）。
  2. 目标嘴部被遮住（`is_mouth_covered()`）→ 报错。
  3. 目标已有口红（`lip_style` 非空）→ 报错。
  4. **对自己**：立即广播并 `update_lips("lipstick", new_color, lipstick_trait)`。
  5. **对他人**：先播警告消息 → `do_after(user, 2 SECONDS, target)` → 成功广播并 `update_lips`。

### 9.3 梳子 / Comb — `/obj/item/hairbrush/comb`

- `name`："comb" / 梳子；`desc`："A rather simple tool, used to straighten out hair and knots in it." / 一个相当简单的工具，用来梳顺头发和发结。
- `icon_state`：`"blackcomb"`（黑色梳子）。

### 9.4 发型预览杂志 / Hairstyle Magazine — `/obj/item/hairstyle_preview_magazine`

- `name`："hip hairstyles magazine" / 时尚发型杂志；`desc`："A magazine featuring a magnitude of hairsytles!" / 一本收录了大量发型的杂志！
- `ui_interact` 为 TGUI 界面占位（源码注释：一个带发型列表和预览的简易 GUI，供玩家挑选发型）。

### 9.5 碎发污渍 / Hair Cuttings — `/obj/effect/decal/cleanable/hair`

- `name`："hair cuttings" / 剪下的碎发
- `icon_state`：`"cut_hair"`（理发剪普通模式成功剪发后生成于原地）。

### 9.6 电剃刀 / Electric Razor — `/obj/item/razor`

| 字段 / Field | 值 / Value |
|---|---|
| `name` | "electric razor" / 电剃刀 |
| `desc` | "The latest and greatest power razor born from the science of shaving." / 由剃须科学孕育的最新一代强力剃刀。 |
| `icon_state` | `"razor"` |
| `obj_flags` | `CONDUCTS_ELECTRICITY`（导电） |
| `w_class` | `WEIGHT_CLASS_TINY` |
| `custom_materials` | 铁 `SMALL_MATERIAL_AMOUNT * 0.7` |
| `shaving_time` | **5 SECONDS**（剃发/剃须耗时） |

**机制 / Mechanics**：

- **自杀 `suicide_act`**：广播自杀消息 → 剃嘴部 + 剃头部 → 返回 `BRUTELOSS`（钝器伤死亡）。
- **`shave()` proc**：嘴部（`BODY_ZONE_PRECISE_MOUTH`）→ `set_facial_hairstyle("Shaved")`；其他部位 → `set_hairstyle("Bald")`；播放 `sound/items/unsheath.ogg`（音量 20）。
- **attack**（仅人类目标，部位检查）：
  - **嘴部**：需头部肢体存在、`HEAD_FACIAL_HAIR` 标志、嘴未被遮住、无 `TRAIT_SHAVED`、胡型非 `"Shaved"`。
  - **头部**：需 `HEAD_HAIR` 标志、部位可触及、发型非 `"Bald"` / `"Balding Hair"` / `"Skinhead"`、无 `TRAIT_SHAVED`。
  - 其他部位：非战斗模式（`combat_mode`）时报错。
  - 支持**自剃**（目标 == 使用者，代词自动切换）；`do_after(user, shaving_time, target)` 成功后执行 `shave()`，全程可见消息。

### 9.7 理发店招牌 / Barbershop Sign — `/obj/structure/sign/barber`

| 字段 / Field | 值 / Value |
|---|---|
| `name` | "barbershop sign" / 理发店招牌 |
| `desc` | "A glowing red-blue-white stripe you won't mistake for any other!" / 一条发光的红蓝白条纹，你不会认错！ |
| `icon_state` | `"barber"` |
| `buildable_sign` | `FALSE`（源码注释：不希望被拆走，它们看起来太寒碜） |

**机制 / Mechanics**：`MAPPING_DIRECTIONAL_HELPERS(/obj/structure/sign/barber, 13)` 注册 13 个方向辅助；`Initialize` 在 `mapload` 时自动挂墙（`find_and_mount_on_atom()`）；`get_turfs_to_mount_on` 返回 `get_step(src, dir)`（挂在朝向格）。

### 9.8 香水盒 / Perfume Box — `/obj/item/storage/box/perfume`

- `name`："box of perfumes" / 香水盒
- **内容 / PopulateContents()**（10 瓶）：古龙水（cologne）、木香（wood）、玫瑰（rose）、茉莉（jasmine）、薄荷（mint）、香草（vanilla）、梨（pear）、草莓（strawberry）、樱桃（cherry）、琥珀（amber）。

---

## 十、精密移液管 / Precision Pipette

> 源码：`modular_nova/modules/salon/code/pipette.dm`（9 行）

### 10.1 `/obj/item/reagent_containers/dropper/precision`

| 字段 / Field | 值 / Value |
|---|---|
| `name` | "pipette" / 移液管 |
| `desc` | "A high precision pippette. Holds 1 unit." / 高精度移液管，容量 1 单位。（原文拼写 pippette） |
| `icon_state` | `"pipette1"` |
| `amount_per_transfer_from_this` | **1** |
| `possible_transfer_amounts` | `list(0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1)`（10 档微调） |
| `volume` | **1** |
| `w_class` | `WEIGHT_CLASS_TINY` |

---

## 十一、理发剪 / Barber's Scissors

> 源码：`modular_nova/modules/salon/code/scissors.dm`（88 行）

### 11.1 `/obj/item/scissors`

| 字段 / Field | 值 / Value | 说明 / Notes |
|---|---|---|
| `name` | "barber's scissors" / 理发剪 | — |
| `desc` | "Some say a barbers best tool is his electric razor, that is not the case. These are used to cut hair in a professional way!" / 有人说理发师最好的工具是电剃刀，其实不然。这些剪刀才是专业剪发的工具！ | — |
| `icon_state` | `"scissors"` | — |
| `w_class` | `WEIGHT_CLASS_TINY` | — |
| `sharpness` | `SHARP_EDGED` | 锋利边缘 |
| `haircut_duration` | **1 MINUTES** | 换发型耗时（普通） |
| `facial_haircut_duration` | **20 SECONDS** | 换胡型耗时（普通） |
| `haircut_duration_expert` | **45 SECONDS** | 换发型耗时（发型专家特质） |
| `facial_haircut_duration_expert` | **15 SECONDS** | 换胡型耗时（发型专家特质） |

**机制 / Mechanics（attack）**：

1. 仅人类目标；部位限定 `BODY_ZONE_PRECISE_MOUTH` / `BODY_ZONE_HEAD`（非战斗模式时）。
2. 目标发型 `"Bald"` 且胡型 `"Shaved"` → 气球提示"没什么可剪的"。
3. 非头部部位 → 交给父类（`..()`，即普通近战攻击）。
4. `tgui_alert` 三选一：**"Hair"（头发）/ "Facial Hair"（胡须）/ "Cancel"（取消）**。

**剪发流程（Hair）**：目标非光头但戴着头盔（`head` 槽）→ 提示移除；`tgui_input_list` 从 `SSaccessories.hairstyles_list` 选发型；选 `"Bald"` 时警告目标；播放 `modular_nova/modules/salon/sound/haircut.ogg`（音量 100）；有 `TRAIT_HAIR_EXPERT` 特质则 `do_after` **45 秒**，否则 **60 秒**；成功后 `set_hairstyle(hair_id, update = TRUE)` + 可见消息 + **生成碎发污渍**（仅普通模式）。

**剪胡流程（Facial Hair）**：目标胡型非 `"Shaved"` 但戴着面具（`wear_mask`）→ 提示移除；`tgui_input_list` 从 `SSaccessories.facial_hairstyles_list` 选胡型；选 `"Shaved"` 时警告目标；播放剪发声；专家 `do_after` **15 秒**，普通 **20 秒**；成功后 `set_facial_hairstyle` + 消息 + **生成碎发污渍**（仅普通模式）。

---

## 十二、美容喷雾 / Salon Sprays

> 源码：`modular_nova/modules/salon/code/sprays.dm`（39 行）

4 种喷雾均为 `/obj/item/reagent_containers/spray` 子类，`amount_per_transfer_from_this = 1`，`possible_transfer_amounts = list(1, 5)`。

| # | 类型 / Type | name | desc | icon_state | 试剂 / Reagents | 容量 / Volume |
|---|---|---|---|---|---|---|
| 1 | `/obj/item/reagent_containers/spray/quantum_hair_dye` | "quantum hair dye" / 量子染发剂 | "Changes hair colour RANDOMLY! Don't forget to read the label!" / **随机**改变发色！别忘了看标签！ | `"hairspraywhite"` | `/datum/reagent/hair_dye` × **30** | **50** |
| 2 | `/obj/item/reagent_containers/spray/baldium` | "baldium spray" / 秃顶喷雾 | "Causes baldness, exessive use may cause customer disatisfaction." / 导致秃顶，过量使用可能引起顾客不满。 | `"hairremoval"` | `/datum/reagent/baldium` × **30** | **50** |
| 3 | `/obj/item/reagent_containers/spray/barbers_aid` | "barber's aid" / 理发师之助 | "Causes rapid hair and facial hair growth!" / 促使头发和胡须快速生长！ | `"hairaccelerator"` | `/datum/reagent/barbers_aid` × **50** | **50** |
| 4 | `/obj/item/reagent_containers/spray/super_barbers_aid` | "super barber's aid" / 超级理发师之助 | "Causes SUPER rapid hair and facial hair growth!" / 促使头发和胡须**超快速**生长！ | `"hairaccelerator"` | `/datum/reagent/concentrated_barbers_aid` × **30** | **50** |

---

## 十三、直剃刀 / Straight Razor

> 源码：`modular_nova/modules/salon/code/straight_razor.dm`（58 行）

### 13.1 `/obj/item/straight_razor`

| 字段 / Field | 值 / Value | 说明 / Notes |
|---|---|---|
| `name` | "straight razor" / 直剃刀 | — |
| `desc` | "A very sharp blade, mostly used for shaving faces..." / 非常锋利的刀片，主要用于刮脸…… | — |
| `force` | **12** | 近战伤害 |
| `throw_speed` | **3** | 投掷速度 |
| `throw_range` | **9** | 投掷距离 |
| `w_class` | `WEIGHT_CLASS_TINY` | 微型 |
| `attack_verb_simple` | `list("cut", "stabbed", "chebbed")` | 攻击动词：切 / 刺 / 削 |
| `sharpness` | `SHARP_EDGED` | 锋利边缘 |
| `hitsound` | `sound/items/weapons/bladeslice.ogg` | 命中声 |
| `wound_bonus` | **10** | 伤口加成 |
| `exposed_wound_bonus` | **15** | 暴露伤口加成 |
| `tool_behaviour` | `TOOL_KNIFE` | 可作刀具工具 |
| `shaving_time` | **10 SECONDS** | 刮脸耗时 |

**机制 / Mechanics**：

- **`shave()` proc**：直接置 `facial_hairstyle = "Shaved"` → `update_body_parts()` → 播放 `sound/items/unsheath.ogg`（音量 20）。
- **attack**：仅人类目标；部位限定 `BODY_ZONE_PRECISE_MOUTH`（非战斗模式时）；校验头部肢体存在、`HEAD_FACIAL_HAIR`、嘴未遮住、胡型非 `"Shaved"`；支持自刮；`do_after(user, 10 SECONDS, target)` 成功后 `shave(target_human)`；其余情况交回父类（`..()`）普通攻击。

---

## 十四、材质与音效资源 / Icons & Sounds

> 源码目录：`modular_nova/modules/salon/icons/`、`modular_nova/modules/salon/sound/`

**图标 / Icons（5 个 DMI）**：

| 文件 / File | 用途 / Used by |
|---|---|
| `chair.dmi` | 理发椅（`barber_chair`） |
| `vendor.dmi` | Fab-O-Vend 售货机（`barbervend`） |
| `items.dmi` | 发圈、毛皮染色器、直剃刀、电剃刀、梳子、口红、碎发、理发店招牌、移液管、理发剪、喷雾等全部物品 |
| `dryer.dmi` | 烘手器（`dryer`） |
| `mixer.dmi` | （预留） |

**音效 / Sounds（2 个 OGG）**：

| 文件 / File | 用途 / Used by |
|---|---|
| `haircut.ogg` | 理发剪剪发/剪胡（音量 100） |
| `drying.ogg` | 烘手器烘干（音量 50） |

**模块外引用 / External assets**：理发店招牌、理发师制服、储物柜、出生点图标引用 `modular_nova/master_files/icons/`（`obj/closet.dmi`、`obj/clothing/under/civilian.dmi`、`mob/clothing/under/civilian.dmi`、`mob/landmarks.dmi`）。

---

# 第二部分：交互菜单系统 / Part 2 — Interaction Menu

## 十五、交互菜单系统总览 / Interaction Menu Overview

> 源码目录：`modular_nova/modules/interaction_menu/`（`code/` 2 个 DM 文件共 653 行：`interaction_component.dm` 343 行 + `interaction_datum.dm` 310 行）

交互菜单系统（NOVA 特色模块）为人类玩家之间提供**可扩展的社交互动面板**：按住 **Ctrl+Shift 点击**目标人物打开 TGUI `InteractionPanel`，从面板中选择互动动作（握手、击掌、摸头、敬礼等）。系统由两层构成：

1. **交互组件 `/datum/component/interactable`**：附加在人类 mob 上的组件，负责捕获 Ctrl+Shift 点击信号、构建/过滤互动列表、渲染 TGUI 界面、执行互动与情趣物品（sextoy）的插入/取出。
2. **交互 datum `/datum/interaction`**：单个互动的数据与行为定义，支持**代码定义**与 **JSON 配置**两种来源（`config/nova/interactions/` 目录动态加载）。

**关键机制 / Key mechanics**：

- **触发方式**：`COMSIG_CLICK_CTRL_SHIFT`（Ctrl+Shift 点击）→ `open_interaction_menu` → TGUI `InteractionPanel`。
- **互动列表构建**：遍历全局 `GLOB.interaction_instances`，按 ERP 偏好与性取向偏好过滤（`lewd` 互动需玩家开启 ERP 偏好；`sexuality` 需与玩家选择的性取向一致）。
- **距离控制**：`distance_allowed = FALSE` 的互动要求目标与使用者相邻（`Adjacent`）；`can_interact` 统一裁决。
- **冷却**：每次执行互动后写入 `interact_last`，`interact_next = interact_last + INTERACTION_COOLDOWN`（1 秒），冷却期间 UI 显示 `block_interact`。
- **隐私（subtler）**：`use_subtler` 开关（默认 TRUE）——开启时暧昧互动以 `subtler` 表情（`/datum/emote/living/subtler`）发送，仅附近开启 ERP 偏好的玩家可见。
- **i18n**：所有消息模板经 `lang_reverse_text()` 反转后替换 `%USER%` / `%TARGET%` 等代词令牌（NOVA I18N 编辑，英文 locale 下为无操作）。
- **管理员工具**：`ADMIN_VERB(reload_interactions, ...)`（中文名"重新加载互动"）可热重载全部互动实例。

---

## 十六、交互组件 / Interaction Component

> 源码：`modular_nova/modules/interaction_menu/code/interaction_component.dm`（343 行）

### 16.1 `/datum/component/interactable` 变量

| 变量 / Var | 默认值 / Default | 说明 / Notes |
|---|---|---|
| `self` | null | 对父对象（人类 mob）的硬引用 |
| `interactions` | list | 可用的互动 datum 列表 |
| `interact_last` | **0** | 上次互动时间（world.time） |
| `interact_next` | **0** | 下次可互动时间 |
| `use_subtler` | `TRUE` | 是否使用 subtler（私密）表情 |
| `has_erp_interaction` | `FALSE` | 当前是否有可用 ERP 互动 |

### 16.2 生命周期 / Lifecycle

- **Initialize**：父对象已删除则 `qdel` 自身；非人类（`!ishuman(parent)`）返回 `COMPONENT_INCOMPATIBLE`；否则 `self = parent` 并调用 `build_interactions_list()`。
- **RegisterWithParent**：注册信号 `COMSIG_CLICK_CTRL_SHIFT` → `open_interaction_menu`。
- **UnregisterFromParent**：注销上述信号。
- **Destroy**：清空 `self` 与 `interactions` 引用。

### 16.3 互动列表构建 / `build_interactions_list()`

遍历 `GLOB.interaction_instances` 全部实例：

- `interaction.lewd` 为真时：玩家未开启 `/datum/preference/toggle/erp` 偏好 → **跳过**；`interaction.sexuality` 非空且与玩家 `/datum/preference/choiced/erp_sexuality` 偏好不符 → **跳过**。
- 其余全部加入 `interactions`。

### 16.4 菜单打开与过滤 / `open_interaction_menu()` & `can_interact()`

- **open_interaction_menu**：非人类使用者直接返回；每次打开都**重建互动列表**；`INVOKE_ASYNC` 异步打开 UI；返回 `CLICK_ACTION_SUCCESS`。
- **can_interact(interaction, target)** 判定链：
  1. `interaction.allow_act(target, self)` 必须为真；
  2. 暧昧互动且目标未开启 ERP 偏好 → 拒绝；
  3. `!interaction.distance_allowed && !target.Adjacent(self)` → 拒绝（要求相邻）；
  4. `interaction.category == INTERACTION_CAT_HIDE`（`"hide"`）→ 拒绝（隐藏类别）；
  5. 对自身使用且 `usage == INTERACTION_OTHER` → 拒绝。

### 16.5 TGUI 界面 / UI

- **ui_interact**：标准 TGUI 流程，界面 ID `"InteractionPanel"`。
- **ui_status**：人类使用者返回 `UI_INTERACTIVE`（始终可交互，距离由 `can_interact` 处理）。
- **ui_static_data**：`arousalLimit` = `AROUSAL_LIMIT`（100）；生殖器可见性/分层/兴奋选项键列表（`GLOB.genital_visibility_options`、`genital_layering_options`、`genital_arousal_options`）。
- **ui_data** 输出字段：

| 字段 / Field | 内容 / Content |
|---|---|
| `categories` / `interactions` | 按类别分组的互动名列表（内容与类别名均排序） |
| `descriptions` / `colors` | 每个互动的描述与按钮颜色（`interaction.name` 为键） |
| `ref_user` / `ref_self` | 使用者与目标的人类引用（REF） |
| `self` | 目标名 |
| `block_interact` | 冷却中（`interact_next >= world.time`） |
| `use_subtler` | 私密表情开关 |
| `erp_interaction` | 使用者 ERP 偏好 |
| `has_erp_interaction` | 是否存在可用暧昧互动 |
| `isTargetSelf` | 是否对自身打开面板 |
| `pleasure` / `arousal` / `pain` | 使用者三项数值 |
| `theirPleasure` / `theirArousal` / `theirPain` | 目标三项数值（仅当非自身时） |
| `lewd_slots` | 情趣物品槽位列表（见 16.6） |
| `genital_config` | 自身生殖器分层配置（仅自身面板） |

- **ui_data 构建细节**：遍历 `interactions`，经 `can_interact` 过滤；`lewd` 互动置 `has_erp_interaction = TRUE`；类别内容 `sort_list` 排序。`lewd_slots` 仅在 `ishuman(user) && can_lewd_strip(user, self)` 且目标开启 `/datum/preference/toggle/erp/sex_toy` 偏好时生成，按 `has_vagina()/has_penis()/has_anus()` 追加阴道/阴茎/肛门槽位，并恒追加乳头槽位（nipples）。`genital_config` 仅 `user == self` 时由 `get_configurable_genitals()` 逐个生成分层 UI 条目。

### 16.6 槽位条目生成 / `generate_strip_entry()`

`generate_strip_entry(name, target, source, item)`：返回 `list("name" = 槽位名, "img" = ...)`——槽位有物品且 `can_lewd_strip(source, target, name)` 通过时，用 `icon2base64(icon(item.icon, item.icon_state, SOUTH, 1))` 生成图标；否则 `img = null`（TGUI 显示占位贴图）。

### 16.7 面板动作 / `ui_act()`

| action | 行为 / Behavior |
|---|---|
| `toggle_subtler` | 翻转 `use_subtler` |
| `set_genital_visibility` / `set_genital_layering` / `set_genital_arousal` | 仅限自身（`actor != self` 拒绝）；`locate(params["ref"]) in actor.get_configurable_genitals()` 白名单定位器官，调用 `apply_visibility_label` / `apply_layering_label` / `apply_arousal_label` |
| `interaction` | 校验 `GLOB.interaction_instances[interaction_id]` 存在且 `can_interact` 通过 → `interaction.act(user, target, use_subtler)` → 更新双方冷却（`interact_last`、`interact_next = interact_last + INTERACTION_COOLDOWN`） |
| `item_slot` | 情趣物品插入/取出（见 16.8） |

未处理动作 → `message_admins("Unhandled interaction '...'. Inform coders.")`。

### 16.8 情趣物品插入/取出 / Sextoy Insert & Remove

流程（`ui_act` 的 `item_slot` 分支）：

1. 取使用者手中物品 `source.get_active_held_item()` 与目标槽位现有物品 `target.vars[item_index]`。
2. 两者皆空 → 提示 "No item to insert or remove!"；手中物品非 `/obj/item/clothing/sextoy` → 提示 "The item you're holding is not a toy!"。
3. `can_lewd_strip(source, target, item_index) && is_toy_compatible(new_item, item_index)` 通过后：
   - `internal = (item_index in list(ORGAN_SLOT_VAGINA, ORGAN_SLOT_ANUS))`（阴道/肛门为"插入 insert"，其余为"附着 attach"）。
   - **消息隐私**：收集视野内（`SAMETILE_MESSAGE_RANGE`）未开启 ERP 偏好的旁观者加入 `ignoring_mobs`，紫色消息（`span_purple`）对他们隐藏；目标本人也加入忽略列表（另有直接提示）。
   - 目标收到警告："[source.name] is trying to ... your [item_index]!"。
   - `do_after(source, 5 SECONDS, target, interaction_key = "interaction_[item_index]")` 成功后：
     - **取出**（已有物品）：广播 → `target.dropItemToGround(existing_item, force = TRUE)`（force 强制，nodrop 不生效于情趣物品）→ `target.vars[item_index] = null`。
     - **插入**（新物品）：广播 → `target.vars[item_index] = new_item` → `new_item.forceMove(target)` → `new_item.lewd_equipped(target, item_index)`。
   - 最后 `target.update_inv_lewd()` 刷新外观。
4. 校验失败 → 提示 "Failed to adjust [target.name]'s toys!"。

### 16.9 辅助判定 / `can_lewd_strip()` & `is_toy_compatible()`

**can_lewd_strip(source, target, slot_index)**：

1. 目标未开启 `/datum/preference/toggle/erp/sex_toy` → 拒绝。
2. 源与目标不同格且不相邻（`source.loc == target.loc || source.Adjacent(target)`）→ 拒绝。
3. 源无手臂（`!source.has_arms()`）→ 拒绝。
4. `!slot_index` → 返回 TRUE（供 UI 决定是否显示按钮）。
5. 槽位判定：**乳头** → `target.has_breasts(required_state = REQUIRE_GENITAL_EXPOSED)` 或 `target.is_topless()`（无胸时）；**阴茎** → `has_penis(REQUIRE_GENITAL_EXPOSED)`；**阴道** → `has_vagina(REQUIRE_GENITAL_EXPOSED)`；**肛门** → `has_anus(REQUIRE_GENITAL_EXPOSED)`（均要求暴露状态）。

**is_toy_compatible(item, slot_index)**：`!item` 返回 TRUE（UI 用）；按槽位检查 `item.lewd_slot_flags & LEWD_SLOT_VAGINA / LEWD_SLOT_PENIS / LEWD_SLOT_ANUS / LEWD_SLOT_NIPPLES`；其他槽位返回 FALSE。

---

## 十七、交互 Datum / Interaction Datum

> 源码：`modular_nova/modules/interaction_menu/code/interaction_datum.dm`（310 行）

### 17.1 全局注册表 / Global Registry

```dm
GLOBAL_LIST_EMPTY_TYPED(interaction_instances, /datum/interaction)
```

所有互动实例（代码定义 + JSON 加载）以**互动名称为键**存入 `GLOB.interaction_instances`。

### 17.2 `/datum/interaction` 变量全表

| 变量 / Var | 默认值 / Default | 说明 / Notes |
|---|---|---|
| `name` | `"broken interaction"` | 菜单显示名 |
| `description` | `"broken"` | 互动描述 |
| `distance_allowed` | `FALSE` | 是否允许远距离使用 |
| `message` | list | 主消息模板列表（JSON 加载） |
| `user_messages` | list | 直接发给使用者的消息列表 |
| `target_messages` | list | 直接发给目标的消息列表 |
| `category` | `INTERACTION_CAT_HIDE`（`"hide"`） | 菜单类别 |
| `usage` | `INTERACTION_OTHER`（`"other"`） | 使用对象：`self`（仅自身）/ `other`（仅他人） |
| `sound_use` | `FALSE` | 是否播放音效 |
| `sound_vary` | `TRUE` | 音效是否随机变调 |
| `sound_range` | **1** | 音效传播距离（配合全局 `INTERACTION_SOUND_RANGE_MODIFIER`） |
| `sound_cache` | null | 音效缓存 |
| `lewd` | `FALSE` | 是否暧昧互动 |
| `user_required_parts` | list | 使用者必需器官槽位（ORGAN SLOT） |
| `target_required_parts` | list | 目标必需器官槽位（ORGAN SLOT） |
| `target_pleasure` / `target_arousal` / `target_pain` | **0** | 目标获得的快感/兴奋/疼痛 |
| `user_pleasure` / `user_arousal` / `user_pain` | **0** | 使用者获得的快感/兴奋/疼痛 |
| `sound_possible` | list | 可选音效列表 |
| `interaction_requires` | list | 需求条件（见 17.3） |
| `color` | `"blue"` | 按钮颜色 |
| `sexuality` | `""` | 性取向显示条件（空 = 不限） |

### 17.3 可用性判定 / `allow_act(user, target)`

1. `target == user && usage == INTERACTION_OTHER` → 拒绝（"other" 类不能对自己用）。
2. `target != user && usage == INTERACTION_SELF` → 拒绝（"self" 类不能对他人用）。
3. `user_required_parts`：逐一 `user.get_organ_slot(part)`，器官缺失或未暴露（`!is_exposed()`）→ 拒绝。
4. `target_required_parts`：同上检查目标。
5. `interaction_requires`：`INTERACTION_REQUIRE_SELF_HAND`（`"self_hand"`）→ 使用者无空手（`!get_active_hand()`）拒绝；`INTERACTION_REQUIRE_TARGET_HAND`（`"target_hand"`）→ 目标无空手拒绝；未知需求 → `CRASH("Unimplemented interaction requirement '...'")`。

### 17.4 执行 / `act(user, target, use_subtler)`

1. `allow_act` 失败直接返回。
2. `message` 为 null → 管理员告警；为字符串（旧格式）→ 转列表并告警（仅提示一次）。
3. `pick(message)` 随机取一条 → `lang_reverse_text()`（I18N 反转模板）→ 令牌替换：

| 令牌 / Token | 替换为 / Replaced with |
|---|---|
| `%TARGET%` | `[target]`（目标名） |
| `%USER%` | 空（`manual_emote` 已自动前置使用者名） |
| `%TARGET_PRONOUN_THEIR%` / `%TARGET_PRONOUN_THEIRS%` | `target.p_their()` / `target.p_theirs()` |
| `%USER_PRONOUN_THEIR%` / `%USER_PRONOUN_THEIRS%` | `user.p_their()` / `user.p_theirs()` |
| `%TARGET_PRONOUN_THEM%` / `%USER_PRONOUN_THEM%` | `target.p_them()` / `user.p_them()` |
| `%TARGET_PRONOUN_THEY%` / `%USER_PRONOUN_THEY%` | `target.p_they()` / `user.p_they()` |

   最终 `trim(..., INTERACTION_MAX_CHAR)`（上限 **255** 字符）。

4. **消息发送**：
   - `lewd`：`use_subtler` 为真 → `user.emote("subtler", type_override = /datum/emote/living/subtler::emote_type \| EMOTE_LEWD, message = msg, intentional = TRUE)`；否则收集视野内（`DEFAULT_MESSAGE_RANGE`）未开 ERP 偏好的旁观者为 `ignoring_mobs`，`user.visible_message(span_purple("[user] [msg]"), ignored_mobs)` 并 `log_message(msg, LOG_EMOTE)`。
   - 非 `lewd`：`user.manual_emote(msg)`。
5. **user_messages / target_messages**：各自 `pick` + 反转 + 令牌替换后 `to_chat` 给对应方。
6. **音效**：`sound_use` 为真时——`sound_possible` 为空告警；字符串旧格式转列表；`sound_cache = pick(sound_possible)`；暧昧互动用 `playsound_if_pref(target.loc, sound_cache, 50, sound_vary, max(0, -SOUND_RANGE + sound_range), pref_to_check = /datum/preference/toggle/erp/sounds)`（需 ERP 音效偏好）；普通互动 `playsound(target.loc, sound_cache, 50, sound_vary, max(0, -SOUND_RANGE + sound_range))`。
7. `INVOKE_ASYNC(src, PROC_REF(apply_effects), user, target)` 异步施加效果。

### 17.5 效果施加 / `apply_effects(user, target)`

- `user_pain` → `user.adjust_pain(user_pain)`；`target_pain` → `target.adjust_pain(target_pain)`。
- 非 `lewd` → 直接返回（普通互动不产生快感/兴奋）。
- `lewd`：`user.adjust_pleasure(user_pleasure)`、`user.adjust_arousal(user_arousal)`、`target.adjust_pleasure(target_pleasure)`、`target.adjust_arousal(target_arousal)`。

### 17.6 JSON 加载与保存 / `load_from_json()` & `json_save()`

**load_from_json(path)**（逐字段清洗，非法值回退默认）：

| 字段 / Field | 清洗 / Sanitize | 回退默认 / Default |
|---|---|---|
| `name` / `description` / `category` / `usage` / `color` / `sexuality` | `sanitize_text` | — |
| `distance_allowed` / `sound_use` / `sound_vary` / `lewd` | `sanitize_integer(0..1)` | 0 |
| `sound_range` | `sanitize_integer(1..7)` | 1 |
| `message` / `sound_possible` | `sanitize_islist` | `list("json error")` |
| `interaction_requires` / `user_messages` / `user_required_parts` / `target_messages` / `target_required_parts` | `sanitize_islist` | `list()` |
| `user_arousal` / `user_pleasure` / `user_pain` / `target_arousal` / `target_pleasure` / `target_pain` | `sanitize_integer(0..100)` | 0 |

文件不存在 → 管理员告警并 `qdel(src)` 返回 FALSE；成功返回 TRUE。

**json_save(path)**：删除旧文件 → 组装 24 字段 JSON → `json_encode` 写入。

### 17.7 全局加载流程 / Global Loading

- **`populate_interaction_instances()`**：遍历 `/datum/interaction` 全部子类型实例化，以 `interaction.name` 为键注册；随后 `populate_interaction_jsons(INTERACTION_JSON_FOLDER)`（`"config/nova/interactions/"`）。
- **`populate_interaction_jsons(directory)`**：递归 `flist`；子目录递归；`.master.json` 结尾 → `populate_interaction_jsons_master`；其余文件 → 新建 datum `load_from_json` 注册，失败告警 "Error loading interaction from file: ..."。
- **`populate_interaction_jsons_master(path)`**：加载主 JSON（`{互动名: 配置}` 结构）；重名 → 告警跳过；`ijson["name"] != iname` → 告警跳过；其余按同款清洗规则实例化并注册。

### 17.8 管理员指令 / Admin Verb

```dm
ADMIN_VERB(reload_interactions, R_DEBUG, "重新加载互动", "Force reload interactions.", ADMIN_CATEGORY_DEBUG)
```

调试权限、中文名"重新加载互动"、调试类别；调用 `populate_interaction_instances()` 全量重建互动注册表。

---

## 十八、内置 JSON 交互预设 / Built-in JSON Interactions

> 配置目录：`config/nova/interactions/`（9 个文件）

| # | 文件 / File | 互动名 / Name | usage | category | distance_allowed | message 示例 / Example | interaction_requires |
|---|---|---|---|---|---|---|---|
| 1 | `beckon.json` | Beckon / 招手 | other | — | 0 | — | — |
| 2 | `cheer.json` | Cheer / 欢呼 | other | — | 0 | — | — |
| 3 | `example_interaction.json` | （示例模板） | usage（示例） | hide | 0 | `["message1", "message2"]` | `["self_hand", "target_hand"]` |
| 4 | `fistbump.json` | Fistbump / 碰拳 | other | — | 0 | — | — |
| 5 | `handshake.json` | Handshake / 握手 | other | — | 0 | — | — |
| 6 | `headpat.json` | Headpat / 摸头 | other | Miscellaneous（杂项） | 0 | `"%USER% pats %TARGET%'s head!"`（%USER% 轻抚 %TARGET% 的头！） | `["self_hand"]` |
| 7 | `highfive.json` | Highfive / 击掌 | other | — | 0 | — | — |
| 8 | `pat.json` | Pat / 轻拍 | other | — | 0 | — | — |
| 9 | `salute.json` | Salute / 敬礼 | other | — | 0 | — | — |

**示例模板字段 / Example template fields**：`name`、`description`、`usage`、`category`（`"hide"` = 菜单中隐藏）、`distance_allowed`（0/1）、`message`（消息数组）、`sound_use`（0/1）、`sound_possible`（音效路径数组）、`sound_range`（1–7，示例 7）、`interaction_requires`（`["self_hand", "target_hand"]`）。

---

## 十九、核心常量与联动定义 / Core Defines & Related Systems

> 常量源码：`code/__DEFINES/~nova_defines/interactions.dm`、`code/__DEFINES/~nova_defines/lewd_defines.dm`、`code/__DEFINES/sound.dm`

### 19.1 交互系统常量 / Interaction Defines

| 定义 / Define | 值 / Value | 说明 / Notes |
|---|---|---|
| `INTERACTION_JSON_FOLDER` | `"config/nova/interactions/"` | JSON 互动配置目录 |
| `INTERACTION_MAX_CHAR` | **255** | 消息最大字符数 |
| `INTERACTION_COOLDOWN` | **1 SECONDS** | 互动冷却时间 |
| `INTERACTION_CAT_HIDE` | `"hide"` | 隐藏类别（菜单不显示） |
| `INTERACTION_REQUIRE_SELF_HAND` | `"self_hand"` | 需求：使用者空手 |
| `INTERACTION_REQUIRE_TARGET_HAND` | `"target_hand"` | 需求：目标空手 |
| `INTERACTION_SELF` | `"self"` | usage：仅自身 |
| `INTERACTION_OTHER` | `"other"` | usage：仅他人 |
| `INTERACTION_SOUND_RANGE_MODIFIER` | **-3** | 音效距离全局修正（`code/__DEFINES/sound.dm`） |

### 19.2 兴奋/快感/疼痛系统常量 / Arousal Defines

| 定义 / Define | 值 / Value | 说明 / Notes |
|---|---|---|
| `AROUSAL_MINIMUM` | **0** | 数值下限 |
| `AROUSAL_MINIMUM_DETECTABLE` | **10** | 可察觉阈值 |
| `AROUSAL_LIMIT` | **100** | 数值上限（UI 的 `arousalLimit`） |

### 19.3 联动偏好 / Linked Preferences

| 偏好 / Preference | 作用 / Effect |
|---|---|
| `/datum/preference/toggle/erp` | ERP 总开关：关闭则暧昧互动从列表过滤、消息/音效对其隐藏 |
| `/datum/preference/choiced/erp_sexuality` | 性取向：与 `interaction.sexuality` 不符的互动被过滤 |
| `/datum/preference/toggle/erp/sex_toy` | 情趣物品：关闭则 `lewd_slots` 不生成、`can_lewd_strip` 拒绝 |
| `/datum/preference/toggle/erp/sounds` | 暧昧音效：`playsound_if_pref` 的检查偏好 |

### 19.4 联动器官系统 / Linked Organ Systems

- 器官槽位：`ORGAN_SLOT_VAGINA` / `ORGAN_SLOT_PENIS` / `ORGAN_SLOT_ANUS` / `ORGAN_SLOT_NIPPLES`（`get_organ_slot()` 获取）。
- 暴露状态：`REQUIRE_GENITAL_EXPOSED`（`has_breasts()` / `has_penis()` / `has_vagina()` / `has_anus()`）。
- 情趣物品：`/obj/item/clothing/sextoy`，槽位兼容标志 `LEWD_SLOT_VAGINA` / `LEWD_SLOT_PENIS` / `LEWD_SLOT_ANUS` / `LEWD_SLOT_NIPPLES`，接入钩子 `lewd_equipped()`、`update_inv_lewd()`。
- 生殖器配置：`get_configurable_genitals()`、`apply_visibility_label()` / `apply_layering_label()` / `apply_arousal_label()`、全局选项表 `GLOB.genital_visibility_options` / `genital_layering_options` / `genital_arousal_options`。
- 数值系统：`pleasure` / `arousal` / `pain`（`adjust_pleasure()` / `adjust_arousal()` / `adjust_pain()`，clamp 于 `AROUSAL_MINIMUM`–`AROUSAL_LIMIT`）。
- 表情系统：`/datum/emote/living/subtler`（私密表情）、`EMOTE_LEWD` 标志、`manual_emote()`。
- 信号：`COMSIG_CLICK_CTRL_SHIFT`（Ctrl+Shift 点击）。
- 发型/胡型子系统：`SSaccessories.hairstyles_list` / `facial_hairstyles_list`、`set_hairstyle()` / `set_facial_hairstyle()`、`TRAIT_HAIR_EXPERT`（发型专家特质，理发剪加速）、`TRAIT_SHAVED`、`HEAD_HAIR` / `HEAD_FACIAL_HAIR` 头部标志（沙龙模块联动）。

---

## 二十、源码路径索引 / Source Path Index

### 美容沙龙模块 / Salon Module（`modular_nova/modules/salon/`）

| 文件 / File | 行数 / Lines | 内容 / Content |
|---|---|---|
| `code/barber.dm` | 68 | 理发师职业 / 装备 / 储物柜 / 出生点 |
| `code/barber_chair.dm` | 5 | 理发椅 |
| `code/barbervend.dm` | 38 | Fab-O-Vend 售货机 + 补货罐 |
| `code/clothing.dm` | 6 | 理发师制服 |
| `code/fur_dyer.dm` | 130 | 电动毛皮染色器 |
| `code/hair_tie.dm` | 135 | 发圈 + 弹丸 |
| `code/hand_dryer.dm` | 29 | 烘手器 |
| `code/misc_items.dm` | 206 | 口红 / 梳子 / 杂志 / 电剃刀 / 招牌 / 香水 |
| `code/pipette.dm` | 9 | 精密移液管 |
| `code/scissors.dm` | 88 | 理发剪 |
| `code/sprays.dm` | 39 | 4 种美容喷雾 |
| `code/straight_razor.dm` | 58 | 直剃刀 |
| `icons/chair.dmi` | — | 理发椅贴图 |
| `icons/vendor.dmi` | — | 售货机贴图 |
| `icons/items.dmi` | — | 物品贴图 |
| `icons/dryer.dmi` | — | 烘手器贴图 |
| `icons/mixer.dmi` | — | 预留贴图 |
| `sound/haircut.ogg` | — | 剪发声 |
| `sound/drying.ogg` | — | 烘干声 |

### 交互菜单模块 / Interaction Menu Module（`modular_nova/modules/interaction_menu/`）

| 文件 / File | 行数 / Lines | 内容 / Content |
|---|---|---|
| `code/interaction_component.dm` | 343 | 交互组件（Ctrl+Shift 面板 / 过滤 / TGUI / 情趣物品插入取出） |
| `code/interaction_datum.dm` | 310 | 交互 datum（变量 / allow_act / act / JSON 加载与全局注册） |

### 常量与配置 / Defines & Config

| 路径 / Path | 内容 / Content |
|---|---|
| `code/__DEFINES/~nova_defines/interactions.dm` | 交互系统常量（JSON 目录 / 冷却 / 类别 / 需求） |
| `code/__DEFINES/~nova_defines/lewd_defines.dm` | 兴奋/快感/疼痛上下限 |
| `code/__DEFINES/sound.dm` | `INTERACTION_SOUND_RANGE_MODIFIER` |
| `config/nova/interactions/` | 9 个 JSON 互动预设 |

---

> **统计 / Statistics**：美容沙龙模块 **12** 个 DM 文件 **811** 行（物品/职业/机器条目 **32** 项），交互菜单模块 **2** 个 DM 文件 **653** 行（组件 1 + datum 1 + 全局 proc 3 + 管理指令 1 + JSON 预设 9），合计 **14** 个 DM 文件 **1464** 行。本文档条目覆盖：理发师职业 1、初始装备 1、储物柜 1、出生点 1、理发椅 1、售货机 1、补货罐 1、制服 1、毛皮染色器 1、发圈 4 + 弹丸 2、烘手器 1、杂项 9、移液管 1、理发剪 1、喷雾 4、直剃刀 1、交互组件 1、交互 datum 1、JSON 预设 9。
