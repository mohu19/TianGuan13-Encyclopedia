# TianGuan13 NOVA 售货机扩展百科

> **项目**: TianGuan13 (Nova Sector → /tg/station) · **分支**: NovaSector
> **代码**: `modular_nova/modules/modular_vending/`（1,015 行，17 个 .dm）+ `modular_nova/modules/imported_vendors/`（949 行）
> **范围**: **NOVA 售货机改造**（35 种机型）+ **导入贩售机**（4 种新机型 + 全量商品）
>
> **定位**: 本卷为《TianGuan13-售货机与宗教百科》的 **NOVA 扩展补充卷**，专注 modular_vending（NOVA 对原版售货机的定制改造）与 imported_vendors（新品牌/新机型导入贩售机）。原版 66 种售货机见基础百科，本卷只列 NOVA 新增/改动。
>
> **经济常量**: PAYCHECK_LOWER = 25 信用点（crew 下级工资）、PAYCHECK_CREW = 50、PAYCHECK_COMMAND = 100。

---

## 目录

- [第一卷 · NOVA 售货机改造全录](#第一卷--nova-售货机改造全录)
  - [1.0 模块框架（products_nova 四类追加）](#10-模块框架products_nova-四类追加)
  - [1.1 AutoDrobe 自动服装机](#11-autodrobe-自动服装机)
  - [1.2 ClothesMate 服装伙伴](#12-clothesmate-服装伙伴)
  - [1.3 Booze-O-Mat 酒吧机（+Cafe）](#13-booze-o-mat-酒吧机cafe)
  - [1.4 Cigarette 香烟机](#14-cigarette-香烟机)
  - [1.5 Cola 可乐机](#15-cola-可乐机)
  - [1.6 Dinnerware 餐具机](#16-dinnerware-餐具机)
  - [1.7 Drugs 药品机（NanoDrug Plus）](#17-drugs-药品机nanodrug-plus)
  - [1.8 Engi-Vend 工程机](#18-engi-vend-工程机)
  - [1.9 Games 游戏机（Good Clean Fun）](#19-games-游戏机good-clean-fun)
  - [1.10 Medical 医疗机（NanoMed Plus）](#110-medical-医疗机nanomed-plus)
  - [1.11 MegaSeed 种子机](#111-megaseed-种子机)
  - [1.12 ModularPC 模块电脑机（Deluxe Silicate）](#112-modularpc-模块电脑机deluxe-silicate)
  - [1.13 Security 安保机（SecTech）](#113-security-安保机sectech)
  - [1.14 Snack 零食机（Getmore Chocolate）](#114-snack-零食机getmore-chocolate)
  - [1.15 Tool 工具机（YouTool/Robco）](#115-tool-工具机youtoolrobco)
  - [1.16 衣柜改造全录（wardrobes.dm 20 机型）](#116-衣柜改造全录wardrobesdm-20-机型)
- [第二卷 · 导入贩售机全录](#第二卷--导入贩售机全录)
  - [2.0 模块框架（imported 基类）](#20-模块框架imported-基类)
  - [2.1 NT Sustenance Supplier（NT 补给机）](#21-nt-sustenance-suppliernt-补给机)
  - [2.2 Fudobenda（阳渔 Fudobenda）](#22-fudobenda阳渔-fudobenda)
  - [2.3 Nomad Fleet Ration Chit Exchange（游牧舰队配给机）](#23-nomad-fleet-ration-chit-exchange游牧舰队配给机)
  - [2.4 Tiziran Imported Delicacies（提兹拉进口机）](#24-tiziran-imported-delicacies提兹拉进口机)
  - [2.5 商品全录 · 通用与阳渔](#25-商品全录--通用与阳渔)
  - [2.6 商品全录 · 莫蒂克](#26-商品全录--莫蒂克)
  - [2.7 商品全录 · 提兹拉](#27-商品全录--提兹拉)
  - [2.8 套餐盒全录（foodpack 容器）](#28-套餐盒全录foodpack-容器)
  - [2.9 随机生成与补给整合](#29-随机生成与补给整合)
- [附录 · 代码路径索引](#附录--代码路径索引)

---

# 第一卷 · NOVA 售货机改造全录

**代码**: `modular_nova/modules/modular_vending/code/`（1,015 行，17 个 .dm）——NOVA 对原版售货机的模块化追加（不改原文件，全部通过 `*_nova` 列表注入）

## 1.0 模块框架（products_nova 四类追加）

**源码**: `modular_vending/code/vending.dm`（93 行）

NOVA 为 `/obj/machinery/vending` 新增 4 个模块化追加列表，全部在 `Initialize()` 时合并进原版列表（重复项会被覆盖以防精灵图错乱），合并后 `Cut()` 释放内存：

| 追加列表 | 合并目标 | 说明 |
|---|---|---|
| `products_nova` | `products` | 常规商品追加 |
| `product_categories_nova` | `product_categories` | 分类追加（同名分类合并商品，新分类直接追加） |
| `premium_nova` | `premium` | 高级商品追加 |
| `contraband_nova` | `contraband` | 违禁商品追加（EMAG 解锁） |

**衣物最低库存机制（MINIMUM_CLOTHING_STOCK = 5）**：所有衣柜类售货机中的 `/obj/item/clothing` 商品，若库存 < 5 且通过 `allow_increase()` 检查，则强制提升到 5。这是为了配合 NOVA 大量新增衣物。`allow_increase()` 拒绝提升的情形：

| 拒绝类型 | 原因 |
|---|---|
| 护甲（suit/armor） | 防开局白嫖防弹衣 |
| 头盔（head/helmet） | 防白嫖防暴头盔 |
| 所有手套（gloves） | 几乎无法检查其效果（防刷绝缘手套） |
| 焊工级护目（flash_protect == FLASH_PROTECTION_WELDER） | 太阳镜/焊工盔 |
| 耳罩类（TRAIT_DEAF） | 防白嫖免疫耳聋装备 |

**其他改动**：
- `spawn_frame(disassembled)`：若售货机被 AI 控制器附身（"售货机起义" vendor uprising），拆解时不锚定（会跳的售货机不能被锚住）。
- 新增 `var/list/excluded_products`：构建售货机库存时可排除的商品列表。
- `vending.dm` 中无商品增删的纯机制性改动见各机型节。

## 1.1 AutoDrobe 自动服装机

**源码**: `modular_vending/code/autodrobe.dm`（139 行）· **机型**: `/obj/machinery/vending/autodrobe`（AutoDrobe 自动服装机）

NOVA 通过 `product_categories_nova` 新增 **5 个分类（共 85 种商品）** + 违禁 14 种 + 高级 1 种。

### Costumes 戏服分类（27 种，icon: mask）

| 商品 | 库存 | 说明 |
|---|---|---|
| 骑兵服 `under/costume/nova/cavalry` | 5 | NOVA 骑兵 |
| 道格·迪马多姆服 `under/doug_dimmadome` | 5 | 帽商梗 |
| 兜鍪 `head/costume/kabuto` | 5 | 武士头盔 |
| 武士甲 `suit/costume/samurai` | 5 | 武士 |
| 荷兰夹克 `suit/dutchjacketsr` | 5 | 红死帮 |
| 墨西哥阔边帽 `head/costume/sombrero` | 5 | |
| 兔女郎耳 `head/playbunnyears` | 6 | |
| 兔女郎耳·英 `head/playbunnyears/british` | 6 | |
| 兔女郎耳·共 `head/playbunnyears/communist` | 6 | |
| 兔女郎耳·美 `head/playbunnyears/usa` | 6 | |
| 兔尾领结 `neck/tie/bunnytie` | 6 | |
| 兔尾领结·蓝 `neck/tie/bunnytie/blue` | 6 | |
| 兔尾领结·共 `neck/tie/bunnytie/communist` | 6 | |
| 兔尾领结·魔术师 `neck/tie/bunnytie/magician` | 6 | |
| 燕尾服 `suit/jacket/tailcoat` | 6 | |
| 燕尾服·英 `suit/jacket/tailcoat/british` | 6 | |
| 燕尾服·共 `suit/jacket/tailcoat/communist` | 6 | |
| 燕尾服·魔术师 `suit/jacket/tailcoat/magician` | 6 | |
| 燕尾服·等离子人 `suit/jacket/tailcoat/plasmaman` | 6 | |
| 燕尾服·美 `suit/jacket/tailcoat/usa` | 6 | |
| 兔女郎装·英 `under/costume/playbunny/british` | 6 | |
| 兔女郎装·共 `under/costume/playbunny/communist` | 6 | |
| 兔女郎装·灰阶 `under/costume/playbunny/greyscale` | 6 | |
| 兔女郎装·魔术师 `under/costume/playbunny/magician` | 6 | |
| 兔女郎装·美 `under/costume/playbunny/usa` | 6 | |
| 玻璃穹顶盔 `head/helmet/glassdome` | 6 | |
| 迷彩 `under/camo/gags` | 6 | |

### Entertainers 演艺人员分类（17 种，icon: masks-theater）

| 商品 | 库存 |
|---|---|
| 秃头小丑面具 `mask/gas/clownbald` | 1 |
| 可染色小丑面具 `mask/gas/clown_colourable` | 1 |
| 弄臣帽 `head/costume/jesteralt` | 1 |
| 粉色小丑假发 `mask/gas/pink_clown_wig` | 5 |
| 小丑贝雷帽 `head/beret/clown` | 5 |
| 弄臣服 `under/rank/civilian/clown/jesteralt` | 1 |
| 粉色小丑装 `under/rank/civilian/clown/pink_clown_outfit` | 5 |
| 粉色高跟小丑鞋 `shoes/clown_shoes/pink/heels` | 5 |
| 粉色小丑手套 `gloves/pink_clown` | 5 |
| 兔女郎耳·小丑 `head/playbunnyears/clown` | 3 |
| 兔女郎耳·哑剧 `head/playbunnyears/mime` | 3 |
| 兔尾领结·小丑 `neck/tie/clown` | 3 |
| 高跟小丑鞋 `shoes/clown_shoes/heeled` | 3 |
| 燕尾服·小丑 `suit/jacket/tailcoat/clown` | 3 |
| 燕尾服·哑剧 `suit/jacket/tailcoat/mime` | 3 |
| 小丑兔女郎装 `under/rank/civilian/clown/clown_bunnysuit` | 3 |
| 哑剧兔女郎装 `under/rank/civilian/mime_bunnysuit` | 3 |

### Fancy 华丽分类（14 种，icon: user-tie）

| 商品 | 库存 |
|---|---|
| 背心裤 `under/pants/nova/vicvest` | 5 |
| 裃 `under/costume/nova/kamishimo` | 5 |
| 浴衣 `under/costume/nova/yukata` | 5 |
| 和服 `under/costume/nova/kimono` | 5 |
| 四尺襷 `under/costume/nova/shihakusho` | 5 |
| 徽章贝雷帽 `head/beret/badge` | 5 |
| 假面舞会面具 `mask/masquerade` | 25 |
| 双色面具 `mask/masquerade/two_colors` | 25 |
| 羽毛面具 `mask/masquerade/feathered` | 25 |
| 双色羽毛面具 `mask/masquerade/two_colors/feathered` | 25 |
| 灰阶围巾 `neck/greyscaled` | 5 |
| 海斗篷 `neck/greyscaled/seecloak` | 5 |
| 主母斗篷 `neck/greyscaled/matroncloak` | 5 |
| 西利克斯斗篷 `neck/greyscaled/xylixcloak` | 5 |

### Service 服务分类（17 种，icon: kitchen-set）

| 商品 | 库存 |
|---|---|
| 纽扣衬衫·服务（裤） `under/costume/buttondown/slacks/service` | 1 |
| 纽扣衬衫·服务（裙） `under/costume/buttondown/skirt/service` | 1 |
| 蝴蝶结 `neck/bowtie` | 2 |
| 马甲 `accessory/waistcoat` | 1 |
| 侍者服 `under/suit/waiter` | 1 |
| 围裙 `suit/apron` | 1 |
| 工装围裙 `suit/apron/overalls` | 1 |
| 女仆发带 `head/costume/maid_headband` | 1 |
| 女仆装 `under/costume/maid` | 1 |
| NOVA 女仆装 `under/costume/nova/maid_uniform` | 1 |
| NOVA 女仆装·改 `under/costume/nova/maid_uniform_alt` | 1 |
| 清洁工女仆装 `under/rank/civilian/janitor/maid` | 1 |
| 女仆围裙 `accessory/maidapron` | 1 |
| 女仆臂套 `gloves/maid_arm_covers` | 1 |
| 女仆颈罩 `neck/maid_neck_cover` | 1 |
| 女仆装（基础） `under/maid_costume` | 1 |
| 女仆发带（基础） `head/maid_headband` | 1 |

### Other 其他分类（10 种，icon: star）

| 商品 | 库存 |
|---|---|
| 骑手墨镜 `glasses/biker` | 5 |
| 黑色风衣 `suit/trenchblack` | 5 |
| 棕色风衣 `suit/trenchbrown` | 5 |
| 圣诞装 `under/costume/nova/christmas` | 5 |
| 圣诞装·露脐 `under/costume/nova/christmas/croptop` | 5 |
| 圣诞冬大衣 `suit/hooded/wintercoat/nova/christmas` | 5 |
| 圣诞冬大衣·绿 `suit/hooded/wintercoat/nova/christmas/green` | 5 |
| 圣诞冬靴 `shoes/winterboots/christmas` | 5 |
| 战术裤 `under/pants/tactical` | 5 |
| 圣诞老人装 `head/costume/santa/gags` | 5 |

### Contraband 违禁（14 种，EMAG 解锁）

| 商品 | 库存 | 说明 |
|---|---|---|
| 可调巴拉克拉瓦 `mask/balaclava/adjustable` | 5 | |
| 三孔巴拉克拉瓦 `mask/balaclava/threehole` | 5 | |
| 三孔巴拉克拉瓦·绿 `mask/balaclava/threehole/green` | 5 | |
| 帝国帽 `head/hats/imperial` | 5 | 辛迪加帝国 |
| 帝国制服 `under/imperial` | 5 | |
| 帝国裙服 `under/imperialskirt` | 5 | |
| 辛迪加女仆发带 `head/costume/maid_headband/syndicate/loadout_headband` | 5 | |
| 战术女仆手套 `gloves/tactical_maid` | 5 | |
| 辛迪加女仆装 `under/syndicate/nova/maid/loadout_maid` | 5 | |
| 辛迪加女仆束腰 `accessory/maidcorset/syndicate/loadout_corset` | 5 | |
| 辛迪加兔女郎耳（假） `head/playbunnyears/syndicate/fake` | 3 | |
| 辛迪加兔尾领结 `neck/tie/bunnytie/syndicate` | 3 | 带护甲（仅火/酸抗，非平衡问题） |
| 辛迪加燕尾服（假） `suit/jacket/tailcoat/syndicate/fake` | 3 | |
| 辛迪加兔女郎装（假） `under/syndicate/syndibunny/fake` | 3 | |

### Premium 高级（1 种）

| 商品 | 库存 |
|---|---|
| 丝带婚纱 `under/dress/wedding_dress/ribbon` | 1 |

## 1.2 ClothesMate 服装伙伴

**源码**: `modular_vending/code/clothesmate.dm`（239 行）· **机型**: `/obj/machinery/vending/clothing`（ClothesMate 通用服装机）

NOVA 新增 **6 个分类（共 178 种商品）** + 高级 9 种 + 违禁 1 种。所有商品库存 5（享受 MINIMUM_CLOTHING_STOCK 保护）。

### Head 头部（20 种，icon: hat-cowboy）

| 商品 | 库存 |
|---|---|
| 徽章贝雷帽 `head/beret/badge` | 5 |
| 可染色平顶帽 `head/colourable_flatcap` | 5 |
| 打手帽 `head/henchmen_hat` | 5 |
| 牛仔帽·卡特尔曼 `head/cowboy/nova/cattleman` | 5 |
| 牛仔帽·卡特尔曼宽沿 `head/cowboy/nova/cattleman/wide` | 5 |
| 牛仔帽·宽沿 `head/cowboy/nova/wide` | 5 |
| 牛仔帽·宽沿带羽 `head/cowboy/nova/wide/feathered` | 5 |
| 牛仔帽·平顶 `head/cowboy/nova/flat` | 5 |
| 牛仔帽·平顶带兜 `head/cowboy/nova/flat/cowl` | 5 |
| 牛仔帽·警长 `head/cowboy/nova/flat/sheriff` | 5 |
| 牛仔帽·副警长 `head/cowboy/nova/flat/deputy` | 5 |
| 牛仔帽·警长带兜 `head/cowboy/nova/flat/cowl/sheriff` | 5 |
| 棕色软呢帽 `head/fedora/brown` | 5 |
| 独立兜帽 `head/standalone_hood` | 5 |
| 小蝴蝶结 `head/small_bow` | 5 |
| 大蝴蝶结 `head/large_bow` | 5 |
| 背式大蝴蝶结 `head/large_bow/back_bow` | 5 |
| 甜美大蝴蝶结 `head/large_bow/sweet_bow` | 5 |
| 帕帕哈帽 `head/costume/nova/papakha` | 5 |
| 不良少年帽 `head/costume/delinquent` | 5 |

### Accessories 配饰（21 种，icon: glasses）

| 商品 | 库存 |
|---|---|
| 大腿挎包 `storage/belt/thigh_satchel` | 5 |
| 游侠斗篷 `neck/ranger_poncho` | 5 |
| 可染色斗篷 `neck/cloak/colourable` | 5 |
| 面纱斗篷 `neck/cloak/colourable/veil` | 5 |
| 裹身斗篷 `neck/cloak/colourable/shroud` | 5 |
| 船形斗篷 `neck/cloak/colourable/boat` | 5 |
| 可染色披肩 `neck/mantle/recolorable` | 5 |
| 特沙里安披肩 `neck/tesharian_mantle` | 5 |
| 长披风 `neck/long_cape` | 5 |
| 宽披风 `neck/wide_cape` | 5 |
| 现代眼镜 `glasses/regular/modern` | 5 |
| 细框眼镜 `glasses/regular/thin` | 5 |
| AR 投影眼镜 `glasses/hud/ar/projector` | 5 |
| 面巾 `neck/face_scarf` | 5 |
| 脖套 `mask/neck_gaiter` | 5 |
| 护腕绷带 `gloves/bracer/wraps` | 5 |
| 可染色手套 `gloves/recolorable` | 5 |
| 无指手套 `gloves/recolorable/fingerless` | 5 |
| 长手套 `gloves/recolorable/long` | 5 |
| 长无指手套 `gloves/recolorable/fingerless/long` | 5 |
| 飞行员太阳镜 `glasses/fake_sunglasses/aviator` | 5 |

### Under 下装/内衣（42 种，icon: shirt）

| 商品 | 库存 |
|---|---|
| 破洞牛仔裤 `under/pants/nova/jeans_ripped` | 5 |
| 肥大裤 `under/pants/nova/big_pants` | 5 |
| 宽松裤 `under/pants/nova/loose_pants` | 5 |
| 袴 `under/pants/nova/hakama` | 5 |
| 双层裙裤 `under/pants/nova/double_skirt_dress` | 5 |
| 缠腰布 `under/dress/skirt/nova/loincloth` | 5 |
| 缠腰布·改 `under/dress/skirt/nova/loincloth/loincloth_alt` | 5 |
| 破洞短裤 `under/shorts/nova/shorts_ripped` | 5 |
| 瑜伽裤 `under/pants/nova/yoga` | 5 |
| 阔腿裤 `under/pants/nova/wide_leg` | 5 |
| 技工服 `under/misc/nova/mechanic` | 5 |
| 蓝色运动服 `under/misc/bluetracksuit` | 5 |
| 可染色西装 `under/suit/nova/recolorable` | 5 |
| 可染色西装裙 `under/suit/nova/recolorable/skirt` | 5 |
| 苏格兰裙 `under/pants/nova/kilt` | 5 |
| 德州装 `under/texas` | 5 |
| 毛衣 `under/sweater` | 5 |
| 夏威夷衫 `under/tachawaiian` | 5 |
| 夏威夷衫·紫 `under/tachawaiian/purple` | 5 |
| 夏威夷衫·绿 `under/tachawaiian/green` | 5 |
| 夏威夷衫·蓝 `under/tachawaiian/blue` | 5 |
| 黑尖领西装 `under/suit/nova/black_really_collared` | 3 |
| 黑尖领西装裙 `under/suit/nova/black_really_collared/skirt` | 3 |
| 铅笔裙套装 `under/suit/nova/pencil` | 3 |
| 铅笔裙·真黑 `under/suit/nova/pencil/black_really` | 3 |
| 铅笔裙·炭灰 `under/suit/nova/pencil/charcoal` | 3 |
| 铅笔裙·海军蓝 `under/suit/nova/pencil/navy` | 3 |
| 铅笔裙·酒红 `under/suit/nova/pencil/burgundy` | 3 |
| 铅笔裙·格纹 `under/suit/nova/pencil/checkered` | 3 |
| 铅笔裙·棕褐 `under/suit/nova/pencil/tan` | 3 |
| 铅笔裙·绿 `under/suit/nova/pencil/green` | 3 |
| 炼狱西装 `under/suit/nova/inferno` | 3 |
| 炼狱西装裙 `under/suit/nova/inferno/skirt` | 3 |
| 中长裙 `under/dress/skirt/nova/medium` | 5 |
| 长裙 `under/dress/skirt/nova/long` | 5 |
| 迷你裙 `under/dress/skirt/nova/mini_skirt` | 5 |
| 巨型围巾裙 `under/dress/nova/giant_scarf` | 5 |
| 短背带裤 `under/pants/nova/shorted_overall` | 5 |
| 齿轮背带 `under/misc/nova/gear_harness` | 5 |
| 模块皮肤衣 `under/misc/nova/modskin` | 5 |
| 超短裤 `under/shorts/nova/shortershorts` | 5 |
| 迷彩 `under/camo/gags` | 5 |

### Suits & Skirts 外套与裙装（70 种，icon: vest）

| 商品 | 库存 |
|---|---|
| 独裙 `under/dress/skirt/nova/lone_skirt` | 5 |
| 短连衣裙 `under/dress/nova/short_dress` | 5 |
| 粉红芭蕾裙 `under/dress/nova/pinktutu` | 5 |
| 牛仔裤裙 `under/dress/skirt/nova/jean` | 5 |
| 花裙 `under/dress/nova/flower` | 5 |
| 无肩带裙 `under/dress/nova/strapless` | 5 |
| 五角星裙 `under/dress/nova/pentagram` | 5 |
| 午夜长裙 `under/dress/nova/midnight_gown` | 5 |
| 开窗裙 `under/dress/nova/windowed_dress` | 5 |
| 开衩裙 `under/dress/nova/slit_dress` | 5 |
| 更短裙 `under/dress/nova/shorter_dress` | 5 |
| 正装铅笔裙 `under/dress/nova/formal_pen_skirt` | 5 |
| 铅笔裙（裙） `under/dress/nova/pen_skirt` | 5 |
| 校服夹克 `suit/varsity` | 5 |
| NOVA 夹克 `suit/toggle/jacket/nova` | 5 |
| 法兰绒（gags） `suit/toggle/jacket/nova/flannel/gags` | 5 |
| 法兰绒 `suit/toggle/jacket/nova/flannel` | 5 |
| 法兰绒·红 `suit/toggle/jacket/nova/flannel/red` | 5 |
| 法兰绒·水蓝 `suit/toggle/jacket/nova/flannel/aqua` | 5 |
| 法兰绒·棕 `suit/toggle/jacket/nova/flannel/brown` | 5 |
| 连帽衫 `suit/toggle/jacket/nova/hoodie` | 5 |
| 镶边连帽衫 `suit/toggle/jacket/nova/hoodie/trim` | 5 |
| 镶边连帽衫·改 `suit/toggle/jacket/nova/hoodie/trim/alt` | 5 |
| 品牌连帽衫 `suit/toggle/jacket/nova/hoodie/branded` | 5 |
| 品牌连帽衫·CTI `suit/toggle/jacket/nova/hoodie/branded/cti` | 5 |
| 品牌连帽衫·MU `suit/toggle/jacket/nova/hoodie/branded/mu` | 5 |
| 品牌连帽衫·SMW `suit/toggle/jacket/nova/hoodie/branded/smw` | 5 |
| 品牌连帽衫·NRTI `suit/toggle/jacket/nova/hoodie/branded/nrti` | 5 |
| 开襟羊毛衫 `suit/toggle/jacket/nova/cardigan` | 5 |
| 露脐飞行员夹克 `suit/toggle/jacket/croptop_bomber_jacket` | 5 |
| 条纹露脐夹克 `suit/toggle/jacket/croptop_bomber_jacket/stripe` | 5 |
| 徽章露脐夹克 `suit/toggle/jacket/croptop_bomber_jacket/badge` | 5 |
| 素面露脐夹克 `suit/toggle/jacket/croptop_bomber_jacket/plain` | 5 |
| 定制夹克 `suit/tailored_jacket` | 5 |
| 短定制夹克 `suit/tailored_jacket/short` | 5 |
| 打手大衣 `suit/jacket/henchmen_coat` | 5 |
| 水手大衣 `suit/toggle/peacoat` | 5 |
| 田径夹克 `suit/toggle/trackjacket` | 5 |
| 都市装 `suit/urban` | 5 |
| 除尘大衣 `suit/duster` | 5 |
| 秋季冲锋衣 `suit/fallsparka` | 5 |
| 露脐夹克 `suit/jacket/croptop` | 5 |
| 长袍 `suit/jacket/long_robe` | 5 |
| 羽织 `suit/jacket/haori` | 5 |
| 现代冬装 `suit/modernwintercoatthing` | 5 |
| 可染色冬大衣 `suit/hooded/wintercoat/colourable` | 5 |
| NOVA 冬大衣 `suit/hooded/wintercoat/nova` | 5 |
| 可染色厨师围裙 `suit/apron/chef/colorable_apron` | 5 |
| 保暖外套 `suit/warm_coat` | 5 |
| 保暖毛衣 `suit/warm_sweater` | 5 |
| 爱心毛衣 `suit/heart_sweater` | 5 |
| 短款夹克 `suit/crop_jacket` | 5 |
| 短袖短款夹克 `suit/crop_jacket/shortsleeve` | 5 |
| 无袖短款夹克 `suit/crop_jacket/sleeveless` | 5 |
| 长款短夹克 `suit/crop_jacket/long` | 5 |
| 长袖短款夹克 `suit/crop_jacket/shortsleeve/long` | 5 |
| 长无袖短夹克 `suit/crop_jacket/sleeveless/long` | 5 |
| 宽大夹克 `suit/big_jacket` | 5 |
| 匕首披肩 `suit/dagger_mantle` | 5 |
| 灰阶高领毛衣 `under/greyscale/turtleneck` | 5 |
| 灰阶高领裙 `under/greyscale/turtleneck/skirt` | 5 |
| 灰阶工装裤 `under/greyscale/overalls` | 5 |
| 灰阶工装裙 `under/greyscale/overalls/skirt` | 5 |
| 独裙工装围裙 `suit/apron/overalls_loneskirt` | 5 |
| NOVA 毛衣 `suit/nova/sweater` | 5 |
| 蝴蝶结毛衣 `suit/nova/sweater/bow` | 5 |
| NOVA 大衣 `suit/nova/overcoat` | 5 |
| 韩式大衣 `suit/koreacoat` | 5 |
| 哥萨克大衣 `suit/cossack` | 5 |
| 可染色飞行员夹克 `suit/toggle/jacket/nova/colorable_bomber` | 5 |

### Shoes 鞋类（15 种，icon: socks）

| 商品 | 库存 |
|---|---|
| 可染色皮鞋 `shoes/colorable_laceups` | 5 |
| 可染色凉鞋 `shoes/colorable_sandals` | 5 |
| 运动鞋 `shoes/sports` | 5 |
| 可染色裹脚布 `shoes/wraps/colourable` | 5 |
| 布裹脚 `shoes/wraps/cloth` | 5 |
| 丛林靴 `shoes/jungleboots` | 5 |
| 运动靴 `shoes/sport_boots` | 5 |
| 可染色牛仔靴 `shoes/cowboy/laced/recolorable` | 5 |
| 过膝军靴 `shoes/jackboots/knee` | 5 |
| 可染色过膝军靴 `shoes/jackboots/knee/recolorable` | 5 |
| 可染色军靴 `shoes/jackboots/recolorable` | 5 |
| 鸭靴 `shoes/jackboots/duckboots` | 5 |
| 高跟鞋 `shoes/high_heels` | 5 |
| 无趾军靴 `shoes/jackboots/toeless` | 5 |
| 花式高跟鞋 `shoes/fancy_heels` | 5 |

### Special 特殊（10 种，icon: star）——仅放家族装或极度违和的衣物

| 商品 | 库存 |
|---|---|
| 黑客装·改 `under/costume/deckers/alt` | 5 |
| 浴袍 `under/costume/nova/bathrobe` | 5 |
| 防毒面具 `mask/gas/respirator` | 5 |
| 灰阶外科口罩 `mask/surgical/greyscale` | 5 |
| 彩色眼罩 `glasses/blindfold/color` | 5 |
| UDC 背包 `storage/backpack/udc` | 5 |
| 小背包 A `storage/backpack/tinypaka` | 5 |
| 小背包 B `storage/backpack/tinypakb` | 5 |
| 小背包 C `storage/backpack/tinypakc` | 5 |
| 虎抱枕 `toy/pillow/torapillow` | 5 |

### Premium 高级（9 种，icon: star，注释"在这里 = 人为稀有，恭喜"）

| 商品 | 库存 |
|---|---|
| 添柏岚靴 `shoes/jackboots/timbs` | 2 |
| 扬基帽 `head/soft/yankee` | 3 |
| 棕色战斗大衣 `suit/brownbattlecoat` | 1 |
| 黑色毛皮大衣 `suit/blackfurrich` | 1 |
| 法式风衣 `suit/frenchtrench` | 1 |
| 拉露恩马甲 `suit/toggle/labcoat/nova/lalunevest` | 2 |
| 设计师手套 `gloves/designer` | 2 |
| 斯凯尔项链 `head/skrell_chain` | 3 |
| 银斯凯尔项链 `head/skrell_chain/silver` | 3 |

### Contraband 违禁（1 种）

| 商品 | 库存 |
|---|---|
| 辛迪加战术脖套 `mask/neck_gaiter/syndicate/tacticool` | 2 |

## 1.3 Booze-O-Mat 酒吧机（+Cafe）

**源码**: `modular_vending/code/boozeomat.dm`（21 行）· **机型**: `/obj/machinery/vending/boozeomat`（Booze-O-Mat 酒机）与 `/obj/machinery/vending/boozeomat/cafe`（咖啡馆酒机）

### 基础 Booze-O-Mat

| 追加 | 内容 |
|---|---|
| 分类 Alcoholic（icon: wine-bottle） | 合成乙醇罐 `soda_cans/nova/synthanolcan` ×6（无酒精乙醇替代品） |
| Premium 高级 | 服务召唤盘 `disk/nifsoft_uploader/job/summoner/service` ×2（NIFSoft 服务职业程序盘） |

### Cafe 咖啡馆变体

| 追加 | 内容 |
|---|---|
| Premium 高级 | 铀矿板 `stack/sheet/mineral/uranium` ×5、等离子矿板 `/plasma` ×5、银矿板 `/silver` ×5、金矿板 `/gold` ×5 |
| 机制改动 | `extended_inventory = TRUE`（扩展库存容量）；`age_restrictions = FALSE`（取消年龄限制，未成年也可买酒） |

## 1.4 Cigarette 香烟机

**源码**: `modular_vending/code/cigarette.dm`（14 行）· **机型**: `/obj/machinery/vending/cigarette`（ShadyCigs Deluxe 香烟机）

| 追加 | 内容 |
|---|---|
| Products 常规 | 电子烟 `vape` ×5、电子烟油弹 `vapecart` ×10、皇冠雾香烟 `storage/fancy/cigarettes/crownhaze` ×5 |
| Contraband 违禁 | 空烟弹 `vapecart/empty` ×5、蓝库什烟弹 `vapecart/bluekush` ×4、红柴油烟弹 `vapecart/reddiesel` ×4、能量游戏烟弹 `vapecart/pwrgame` ×4、奶酪烟弹 `vapecart/cheese` ×4、KHI 香烟 `storage/fancy/cigarettes/khi` ×3 |

## 1.5 Cola 可乐机

**源码**: `modular_vending/code/cola.dm`（13 行）· **机型**: `/obj/machinery/vending/cola`（Space Cola 可乐机）

| 追加 | 内容 |
|---|---|
| Products 常规 | 润滑油可乐 `soda_cans/nova/lubricola` ×10、焊接气泡水 `soda_cans/nova/welding_fizz` ×10、THC 可乐 `soda_cans/thc` ×10 |
| 节日（4·20 大麻节） | `Initialize` 检测 Four-Twenty 节日 → 追加 THC 可乐 `soda_cans/thc` ×10（与常规重复无碍，覆盖后仍为 10） |

## 1.6 Dinnerware 餐具机

**源码**: `modular_vending/code/dinnerware.dm`（2 行）· **机型**: `/obj/machinery/vending/dinnerware`（Plasteel Chef's 餐具机）

| 追加 | 内容 |
|---|---|
| Premium 高级 | 服务召唤盘 `disk/nifsoft_uploader/job/summoner/service` ×2 |

## 1.7 Drugs 药品机（NanoDrug Plus）

**源码**: `modular_vending/code/drugs.dm`（14 行）· **机型**: `/obj/machinery/vending/drugs`（NanoDrug Plus 药品机）

| 追加 | 内容 |
|---|---|
| Products 常规 | 神经软件重置盘 `disk/neuroware/reset` ×2、大脑盘 `disk/neuroware/brain` ×2（治脑损伤）、吗啡盘 `disk/neuroware/morphine` ×4、利多卡因盘 `disk/neuroware/lidocaine` ×4 |
| Premium 高级 | 奋乃静盘 `disk/neuroware/synaptizine` ×2、精神安定盘 `disk/neuroware/psicodine` ×2、快乐盘 `disk/neuroware/happiness` ×2、迷思破碎盘 `disk/neuroware/mindbreaker` ×2、抽搐盘 `disk/neuroware/twitch` ×2 |

## 1.8 Engi-Vend 工程机

**源码**: `modular_vending/code/engivend.dm`（8 行）· **机型**: `/obj/machinery/vending/engivend`（Engi-Vend 工程用品机）

| 追加 | 内容 |
|---|---|
| Products 常规 | 工程用 meson 眼镜 `glasses/meson/engine` ×5、装填型 RCD `construction/rcd/loaded` ×3（基础百科曾删除，NOVA 重新加入）、材料袋 `storage/pouch/material` ×2、建筑袋 `storage/bag/construction` ×2 |
| Premium 高级 | 工具召唤盘 `disk/nifsoft_uploader/summoner/tools` ×4 |

## 1.9 Games 游戏机（Good Clean Fun）

**源码**: `modular_vending/code/games.dm`（39 行）· **机型**: `/obj/machinery/vending/games`（Good Clean Fun 游戏机）

### Toys 玩具分类（9 种，icon: hat-wizard）

| 商品 | 库存 |
|---|---|
| 战棋套装 `storage/briefcase/secure/white/wargame_kit` | 3 |
| 限位激光笔 `laser_pointer/limited` | 3 |
| 网球 `toy/tennis` | 2 |
| 网球·红 `toy/tennis/red` | 2 |
| 网球·黄 `toy/tennis/yellow` | 2 |
| 网球·绿 `toy/tennis/green` | 2 |
| 网球·青 `toy/tennis/cyan` | 2 |
| 网球·蓝 `toy/tennis/blue` | 2 |
| 网球·紫 `toy/tennis/purple` | 2 |

### Other 其他分类（8 种，icon: star）

| 商品 | 库存 |
|---|---|
| 发刷 `hairbrush` | 3 |
| 发圈 `head/hair_tie/scrunchie` | 3 |
| 全息香烟 `holocigarette` | 5 |
| 可吸附灵魂捕捉器 `attachable_soulcatcher` | 5 |
| 合成器盘·铜管 `disk/neuroware/synthesizer/brass` | 2 |
| 合成器盘·吉他 `disk/neuroware/synthesizer/guitar` | 2 |
| 合成器盘·打击乐 `disk/neuroware/synthesizer/percussion` | 2 |
| 合成器盘·钢琴 `disk/neuroware/synthesizer/piano` | 2 |

### 其他追加

| 追加 | 内容 |
|---|---|
| Premium 高级 | 异星考古工具腰带（满装） `storage/belt/utility/xenoarch/full` ×3 |
| Initialize 覆写 | **从违禁品中移除俄罗斯左轮手枪** `contraband -= /obj/item/gun/ballistic/revolver/russian`（防止游戏机刷出真枪） |

## 1.10 Medical 医疗机（NanoMed Plus）

**源码**: `modular_vending/code/medical.dm`（9 行）· **机型**: `/obj/machinery/vending/medical`（NanoMed Plus 医疗机）

| 追加 | 内容 |
|---|---|
| Products 常规 | 语音合成器 `ttsdevice` ×3（TTS 装置）、彩色眼罩 `glasses/blindfold/color` ×2 |
| Premium 高级 | 手术召唤盘 `disk/nifsoft_uploader/job/summoner/surgery` ×4、赛博修复膏 `cybernetic_repair_paste` ×2 |

## 1.11 MegaSeed 种子机

**源码**: `modular_vending/code/megaseed.dm`（20 行）· **机型**: `/obj/machinery/vending/hydroseeds`（MegaSeed Servitor 种子机，源码注释 "//sneed"）

### Miscellaneous 杂项分类（4 种，icon: question）

| 商品 | 库存 |
|---|---|
| 皮鲁种子 `seeds/piru` | 3 |
| 基里种子 `seeds/kiri` | 3 |
| 穆利种子 `seeds/muli` | 3 |
| 纳卡提种子 `seeds/nakati` | 3 |

### 其他追加

| 追加 | 内容 |
|---|---|
| Contraband 违禁 | 古柯叶种子 `seeds/cocaleaf` ×3、罂粟种子（鸦片罂粟） `seeds/poppy/opiumpoppy` ×3、猫薄荷茶种子 `seeds/tea/catnip` ×3、蜘蛛香蕉种子 `seeds/banana/spider_banana` ×1 |
| Premium 高级 | 服务召唤盘 `disk/nifsoft_uploader/job/summoner/service` ×2 |

## 1.12 ModularPC 模块电脑机（Deluxe Silicate）

**源码**: `modular_vending/code/modularpc.dm`（36 行）· **机型**: `/obj/machinery/vending/modularpc`（Deluxe Silicate 模块电脑配件机）

**描述改为**: "All the parts you need for custom modular computers, personal AIs, and neurocomputing programs!"（定制模块电脑、个人 AI 与神经计算程序所需的全部零件！）

### Products 常规（22 种）

| 商品 | 库存 |
|---|---|
| 神经软件重置盘 `disk/neuroware/reset` | 2 |
| 大脑盘 `disk/neuroware/brain` | 2 |
| 奋乃静盘 `disk/neuroware/synaptizine` | 3 |
| 精神安定盘 `disk/neuroware/psicodine` | 3 |
| 合成器盘·铜管 `disk/neuroware/synthesizer/brass` | 2 |
| 合成器盘·吉他 `disk/neuroware/synthesizer/guitar` | 2 |
| 合成器盘·打击乐 `disk/neuroware/synthesizer/percussion` | 2 |
| 合成器盘·钢琴 `disk/neuroware/synthesizer/piano` | 2 |
| 复古 PDA `modular_computer/pda/ancient` | 5 |
| 超薄 PDA `modular_computer/pda/ultraslim` | 5 |
| 鲸豚 PDA `modular_computer/pda/ceti` | 5 |
| 虚拟 PDA `modular_computer/pda/virtual` | 5 |
| 结实 PDA `modular_computer/pda/robust` | 5 |
| 猫娘 PDA `modular_computer/pda/neko` | 5 |
| 旧式 PDA `modular_computer/pda/old` | 5 |
| 全息甲板 PDA `modular_computer/pda/holodeck` | 5 |
| 全息 PDA `modular_computer/pda/hologram` | 5 |
| 平板 PDA `modular_computer/pda/tablet` | 5 |
| 流线 PDA `modular_computer/pda/slimline` | 5 |
| 加固 PDA `modular_computer/pda/rugged` | 5 |
| 三代 PDA `modular_computer/pda/g3` | 5 |
| 全息系统 PDA `modular_computer/pda/holosystem` | 5 |

### Contraband 违禁（8 种）

| 商品 | 库存 |
|---|---|
| 维护盘 `disk/neuroware/maintenance` | 2 |
| 泵起盘 `disk/neuroware/pumpup` | 2 |
| 快乐盘 `disk/neuroware/happiness` | 2 |
| 太空毒品盘 `disk/neuroware/space_drugs` | 2 |
| THC 盘 `disk/neuroware/thc` | 2 |
| 蘑菇致幻盘 `disk/neuroware/mushroomhallucinogen` | 2 |
| 迷思破碎盘 `disk/neuroware/mindbreaker` | 3 |
| 抽搐盘 `disk/neuroware/twitch` | 2 |

## 1.13 Security 安保机（SecTech）

**源码**: `modular_vending/code/security.dm`（14 行）· **机型**: `/obj/machinery/vending/security`（SecTech 安保机）

### Products 常规（6 种）

| 商品 | 库存 |
|---|---|
| 弹药袋 `storage/pouch/ammo` | 2 |
| 路障 `storage/barricade` | 4 |
| 动能球弹匣 `ammo_box/magazine/kineticballs` | 10 |
| 高级动能球 `ammo_box/advanced/kineticballs` | 5 |
| 胡椒球弹匣 `ammo_box/magazine/pepperball` | 4 |
| 高级胡椒球 `ammo_box/advanced/pepperballs` | 2 |

### Premium 高级（3 种）

| 商品 | 库存 |
|---|---|
| Type-207 手枪 `gun/ballistic/automatic/pistol/type207` | 5 |
| 胡椒球手枪 `gun/ballistic/automatic/pistol/pepperball` | 2 |
| NIFSoft 移除器 `nifsoft_remover` | 3 |

## 1.14 Snack 零食机（Getmore Chocolate）

**源码**: `modular_vending/code/snack.dm`（8 行）· **机型**: `/obj/machinery/vending/snack`（Getmore Chocolate 零食机）

| 追加 | 内容 |
|---|---|
| 节日（4·20 大麻节） | `Initialize` 检测 Four-Twenty 节日 → 追加 THC 布朗尼 `food/brownie/thc` ×6、THC 软糖 `food/thcgummies` ×6、THC 曲奇 `food/cookie/thc` ×6 |

## 1.15 Tool 工具机（YouTool/Robco）

**源码**: `modular_vending/code/tool.dm`（11 行）· **机型**: `/obj/machinery/vending/tool`（YouTool 工具机）

### Premium 高级（5 种）

| 商品 | 库存 |
|---|---|
| 工具召唤盘 `disk/nifsoft_uploader/summoner/tools` | 4 |
| Fock 多功能工具 `multitool/fock` | 2 |
| 全能钻螺丝刀 `screwdriver/omni_drill` | 1 |
| 电弧焊枪 `weldingtool/electric/arc_welder` | 1 |
| 紧凑钻镐 `pickaxe/drill/compact` | 1 |

### Contraband 违禁（1 种）

| 商品 | 库存 |
|---|---|
| 破门撬棍 `crowbar/large/doorforcer` | 2 |

## 1.16 衣柜改造全录（wardrobes.dm 20 机型）

**源码**: `modular_vending/code/wardrobes.dm`（335 行）——NOVA 为 19 个职业衣柜 + MagiVend 追加商品（兔子装主题 + 职业专属物品）。

### 1.16.1 MediDrobe 医疗衣柜（medi_wardrobe，25 种）

| 商品 | 库存 |
|---|---|
| 医疗耳机 `radio/headset/headset_med` | 3 |
| 丁腈手套 `gloves/latex/nitrile` | 2 |
| NOVA 手术袍 `suit/toggle/labcoat/nova/surgical_gown` | 5 |
| 医疗弹药带 `storage/belt/medbandolier` | 2 |
| 急救化学员 EMT 服 `under/rank/engineering/engineer/nova/hazard_chem/emt` | 2 |
| 红色刷手服 `under/rank/medical/scrubs/nova/red` | 4 |
| 白色刷手服 `under/rank/medical/scrubs/nova/white` | 4 |
| 医生工作服 `under/rank/medical/doctor/nova/utility` | 4 |
| 护士服 `under/rank/medical/doctor/nurse` | 4 |
| 严肃护士服 `under/rank/medical/doctor/nurse/seriouser` | 4 |
| 医用白大褂 `suit/toggle/labcoat/medical` | 4 |
| 医疗信使包 `storage/backpack/messenger/med` | 4 |
| 兔女郎耳·医生 `head/playbunnyears/doctor` | 3 |
| 兔女郎耳·急救员 `head/playbunnyears/paramedic` | 3 |
| 兔女郎耳·心理医生 `head/playbunnyears/psychologist` | 3 |
| 兔尾领结·医生 `neck/tie/bunnytie/doctor` | 3 |
| 兔尾领结·急救员 `neck/tie/bunnytie/paramedic` | 3 |
| 燕尾服·心理医生 `suit/jacket/tailcoat/psychologist` | 3 |
| 心理医生兔女郎装 `under/rank/civilian/psychologist_bunnysuit` | 3 |
| 医生兔女郎装 `under/rank/medical/doctor_bunnysuit` | 3 |
| 急救员兔女郎装 `under/rank/medical/paramedic_bunnysuit` | 3 |
| 长丁腈手套 `gloves/latex/nitrile/long` | 2 |

### 1.16.2 MortiDrobe 验尸官衣柜（coroner_wardrobe，4 种）

| 商品 | 库存 |
|---|---|
| 兔女郎耳·验尸官 `head/playbunnyears/coroner` | 2 |
| 兔尾领结·验尸官 `neck/tie/bunnytie/coroner` | 2 |
| 验尸官燕尾白大褂 `suit/toggle/labcoat/coroner/doctor_tailcoat` | 2 |
| 验尸官兔女郎装 `under/rank/medical/coroner_bunnysuit` | 2 |

### 1.16.3 EngiDrobe 工程衣柜（engi_wardrobe，22 种）

| 商品 | 库存 |
|---|---|
| 工程耳机 `radio/headset/headset_eng` | 3 |
| 工程师长裤 `under/rank/engineering/engineer/nova/trouser` | 3 |
| 工程师工作服 `under/rank/engineering/engineer/nova/utility` | 3 |
| 危险化工服 `under/rank/engineering/engineer/nova/hazard_chem` | 3 |
| 电信工作服 `under/rank/engineering/engineer/nova/utility/telecomm` | 3 |
| 工装裤 `under/misc/overalls` | 3 |
| 无趾工靴 `shoes/workboots/toeless` | 3 |
| 工程可染色飞行员夹克 `suit/toggle/jacket/nova/colorable_bomber/engi` | 3 |
| 电信飞行员夹克 `suit/toggle/jacket/nova/colorable_bomber/tcomm` | 3 |
| 橙色安全帽 `head/utility/hardhat/orange` | 2 |
| 橙色焊工安全帽 `head/utility/hardhat/welding/orange` | 2 |
| 深蓝安全帽 `head/utility/hardhat/dblue` | 2 |
| 深蓝焊工安全帽 `head/utility/hardhat/welding/dblue` | 2 |
| 红色安全帽 `head/utility/hardhat/red` | 2 |
| 工程信使包 `storage/backpack/messenger/eng` | 3 |
| 兔女郎耳·工程师 `head/playbunnyears/engineer` | 3 |
| 兔尾领结·工程师 `neck/tie/bunnytie/engineer` | 3 |
| 高跟工靴 `shoes/workboots/heeled` | 3 |
| 燕尾服·工程师 `suit/jacket/tailcoat/engineer` | 3 |
| 工程师兔女郎装 `under/rank/engineering/engineer_bunnysuit` | 3 |

### 1.16.4 AtmosDrobe 大气衣柜（atmos_wardrobe，10 种）

| 商品 | 库存 |
|---|---|
| 工程 meson 眼镜 `glasses/meson/engine` | 2 |
| 大气贝雷帽 `head/beret/atmos` | 4 |
| 工程信使包 `storage/backpack/messenger/eng` | 3 |
| 兔女郎耳·大气技师 `head/playbunnyears/atmos_tech` | 3 |
| 兔尾领结·大气技师 `neck/tie/bunnytie/atmos_tech` | 3 |
| 高跟工靴 `shoes/workboots/heeled` | 3 |
| 大气技师燕尾消防服 `suit/utility/fire/atmos_tech_tailcoat` | 3 |
| 大气技师兔女郎装 `under/rank/engineering/atmos_tech_bunnysuit` | 3 |
| 防毒面具（gags） `mask/gas/gags` | 3 |

### 1.16.5 CargoDrobe 货运衣柜（cargo_wardrobe，22 种 + 违禁 2）

| 商品 | 库存 |
|---|---|
| 货运长裤 `under/rank/cargo/tech/nova/long` | 3 |
| 戈尔卡装 `under/rank/cargo/tech/nova/gorka` | 3 |
| 货运高领衫 `under/rank/cargo/tech/nova/turtleneck` | 3 |
| 货运高领裙 `under/rank/cargo/tech/nova/turtleneck/skirt` | 3 |
| 货运工作服 `under/rank/cargo/tech/nova/utility` | 3 |
| 休闲货运装 `under/rank/cargo/tech/nova/casualman` | 3 |
| 货运飞行员夹克 `suit/toggle/jacket/nova/colorable_bomber/supply` | 3 |
| 持枪证 HUD 眼镜 `glasses/hud/gun_permit` | 5（来自 company imports 模块） |
| 信使包 `storage/backpack/messenger` | 3 |
| 兔女郎耳·比特跑者 `head/playbunnyears/bitrunner` | 3 |
| 兔女郎耳·货运 `head/playbunnyears/cargo` | 3 |
| 兔女郎耳·邮差 `head/playbunnyears/mailman` | 1 |
| 兔尾领结·比特跑者 `neck/tie/bunnytie/bitrunner` | 3 |
| 兔尾领结·货运 `neck/tie/bunnytie/cargo` | 3 |
| 兔尾领结·邮差 `neck/tie/bunnytie/mailman` | 1 |
| 燕尾服·比特跑者 `suit/jacket/tailcoat/bitrunner` | 3 |
| 燕尾服·货运 `suit/jacket/tailcoat/cargo` | 3 |
| 比特跑者兔女郎装 `under/rank/cargo/bitrunner/bunnysuit` | 3 |
| 货运兔女郎装 `under/rank/cargo/cargo_bunnysuit` | 3 |
| 邮差兔女郎装 `under/rank/cargo/mailman_bunnysuit` | 1 |

**Contraband 违禁（2 种）**：疤面西装 `under/suit/nova/scarface` ×2、邪恶货运装 `under/rank/cargo/tech/nova/evil` ×2。

### 1.16.6 RoboDrobe 机器人学衣柜（robo_wardrobe，36 种 + 违禁 8）——NOVA 将其改造为"机器人急救站"

| 商品 | 库存 |
|---|---|
| 机器人学贝雷帽 `head/beret/science/fancy/robo` | 2 |
| 麻醉气罐 `tank/internals/anesthetic` | 2 |
| 呼吸面罩 `mask/breath` | 2 |
| 神经软件重置盘 `disk/neuroware/reset` | 2 |
| 大脑盘 `disk/neuroware/brain` | 2（治脑损伤） |
| 吗啡盘 `disk/neuroware/morphine` | 4 |
| 利多卡因盘 `disk/neuroware/lidocaine` | 4 |
| 奋乃静盘 `disk/neuroware/synaptizine` | 2 |
| 精神安定盘 `disk/neuroware/psicodine` | 2 |
| 吗啡瓶 `reagent_containers/cup/bottle/morphine` | 2 |
| 注射器 `reagent_containers/syringe` | 2 |
| 合成修复片 `stack/medical/synth_repair` | 4 |
| 机器人系统清洁笔 `hypospray/medipen/deforest/robot_system_cleaner` | 3 |
| 机器人液态焊料笔 `hypospray/medipen/deforest/robot_liquid_solder` | 3 |
| 冷赫库里喷雾 `spray/hercuri/chilled` | 2 |
| 二氮等离子体喷雾 `spray/dinitrogen_plasmide` | 2 |
| 机器人泡沫 `stack/medical/wound_recovery/robofoam` | 2 |
| 超级机器人泡沫 `stack/medical/wound_recovery/robofoam_super` | 1 |
| 黑色手套 `gloves/color/black` | 2（耐火，便于机器人无痛塑形金属；属治疗物品故放此） |
| 接骨器 `bonesetter` | 2（治脱臼） |
| 赛博修复膏 `cybernetic_repair_paste` | 2（治器官损伤） |
| 纱布卷 `stack/medical/wrap/gauze` | 4（治所有伤口） |
| 禁用医疗机器人分析仪 `healthanalyzer/no_medibot` | 2（禁止医疗机器人使用，避免浪费） |
| 简易分析仪 `healthanalyzer/simple` | 2 |
| 机器人学家白大褂 `suit/toggle/labcoat/nova/fancy/roboticist` | 3 |
| 机器人学背包 `storage/backpack/science/robo` | 2 |
| 机器人学挎包 `storage/backpack/satchel/science/robo` | 2 |
| 机器人学行李袋 `storage/backpack/duffelbag/science/robo` | 2 |
| 机器人学信使包 `storage/backpack/messenger/science/robo` | 2 |
| 兔女郎耳·机器人学家 `head/playbunnyears/roboticist` | 2 |
| 兔尾领结·机器人学家 `neck/tie/bunnytie/roboticist` | 2 |
| 机器人学家燕尾白大褂 `suit/toggle/labcoat/roboticist/doctor_tailcoat` | 2 |
| 机器人学家兔女郎装 `under/rank/rnd/scientist/roboticist_bunnysuit` | 2 |
| 机器人鲨鱼玩偶 `toy/plush/nova/robotics_shark` | 1 |

**Contraband 违禁（8 种）**：蜥蜴机器人舌 `organ/tongue/lizard/robot` ×2、泵起盘 `disk/neuroware/pumpup` ×2、维护盘 `disk/neuroware/maintenance` ×2、太空毒品盘 `disk/neuroware/space_drugs` ×2、快乐盘 `disk/neuroware/happiness` ×2、THC 盘 `disk/neuroware/thc` ×2、蘑菇致幻盘 `disk/neuroware/mushroomhallucinogen` ×2、迷思破碎盘 `disk/neuroware/mindbreaker` ×2。

### 1.16.7 SciDrobe 科研衣柜（science_wardrobe，10 种）

| 商品 | 库存 |
|---|---|
| 高智商科研服 `under/rank/rnd/scientist/nova/hlscience` | 3 |
| 科研工作服 `under/rank/rnd/scientist/nova/utility` | 3 |
| 常规白大褂 `suit/toggle/labcoat/nova/fancy/regular` | 3 |
| 科研飞行员夹克 `suit/toggle/jacket/nova/colorable_bomber/sci` | 3 |
| 科研信使包 `storage/backpack/messenger/science` | 3 |
| 兔女郎耳·科学家 `head/playbunnyears/scientist` | 3 |
| 兔尾领结·科学家 `neck/tie/bunnytie/scientist` | 3 |
| 科学家燕尾白大褂 `suit/toggle/labcoat/science/doctor_tailcoat` | 3 |
| 科学家兔女郎装 `under/rank/rnd/scientist/bunnysuit` | 3 |

### 1.16.8 GeneDrobe 基因学衣柜（gene_wardrobe，5 种）

| 商品 | 库存 |
|---|---|
| 遗传学家白大褂 `suit/toggle/labcoat/nova/fancy/geneticist` | 3 |
| 兔女郎耳·遗传学家 `head/playbunnyears/geneticist` | 2 |
| 兔尾领结·遗传学家 `neck/tie/bunnytie/geneticist` | 2 |
| 遗传学燕尾白大褂 `suit/toggle/labcoat/genetics/doctor_tailcoat` | 2 |
| 遗传学家兔女郎装 `under/rank/rnd/geneticist/bunnysuit` | 2 |

### 1.16.9 Hydrobe 水培衣柜（hydro_wardrobe，5 种 + 违禁 3）

| 商品 | 库存 |
|---|---|
| 水培信使包 `storage/backpack/messenger/hyd` | 3 |
| 兔女郎耳·植物学家 `head/playbunnyears/botanist` | 2 |
| 兔尾领结·植物学家 `neck/tie/bunnytie/botanist` | 2 |
| 燕尾服·植物学家 `suit/jacket/tailcoat/botanist` | 2 |
| 植物学家兔女郎装 `under/rank/civilian/hydroponics/bunnysuit` | 2 |

**Contraband 违禁（3 种）**：疤面西装 `under/suit/nova/scarface` ×2、亚当齿轮背带 `under/misc/nova/gear_harness/adam` ×2、夏娃齿轮背带 `under/misc/nova/gear_harness/eve` ×2。

### 1.16.10 BarDrobe 调酒师衣柜（bar_wardrobe，8 种）

| 商品 | 库存 |
|---|---|
| 香草蜡烛盒 `storage/fancy/candle_box/vanilla` | 1 |
| 梨香蜡烛盒 `storage/fancy/candle_box/pear` | 1 |
| 琥珀蜡烛盒 `storage/fancy/candle_box/amber` | 1 |
| 茉莉蜡烛盒 `storage/fancy/candle_box/jasmine` | 1 |
| 薄荷蜡烛盒 `storage/fancy/candle_box/mint` | 1 |
| 调酒师冬大衣 `suit/hooded/wintercoat/nova/bartender` | 2 |
| 兔女郎耳·调酒师 `head/playbunnyears/bartender` | 2 |
| 兔尾领结·调酒师 `neck/tie/bunnytie/bartender` | 2 |
| 燕尾服·调酒师 `suit/jacket/tailcoat/bartender` | 2 |
| 调酒师兔女郎装 `under/rank/civilian/bartender_bunnysuit` | 2 |

### 1.16.11 ChapDrobe 牧师衣柜（chap_wardrobe，7 种 + 违禁 3）

| 商品 | 库存 |
|---|---|
| 尼梅斯服 `suit/costume/nemes` | 1 |
| 尼梅斯头饰 `head/costume/nemes` | 1 |
| 法老头饰 `head/costume/pharaoh` | 1 |
| 兔子吊坠 `neck/bunny_pendant` | 6 |
| 兔女郎耳·牧师 `head/playbunnyears/chaplain` | 1 |
| 燕尾服·牧师 `suit/jacket/tailcoat/chaplain` | 1 |
| 牧师兔女郎装 `under/rank/civilian/chaplain_bunnysuit` | 1 |

**Contraband 违禁（3 种）**：牧师颈饰 `neck/chaplain` ×1、黑色牧师颈饰 `neck/chaplain/black` ×1、死亡植入器 `implanter/mortis` ×1。

### 1.16.12 ChefDrobe 厨师衣柜（chef_wardrobe，4 种）

| 商品 | 库存 |
|---|---|
| 兔女郎耳·厨师 `head/playbunnyears/cook` | 2 |
| 兔尾领结·厨师 `neck/tie/bunnytie/cook` | 2 |
| 燕尾服·厨师 `suit/jacket/tailcoat/cook` | 2 |
| 厨师兔女郎装 `under/rank/civilian/cook_bunnysuit` | 2 |

### 1.16.13 服务推车（cart，1 种）

| 商品 | 库存 |
|---|---|
| 服务耳机 `radio/headset/headset_srv` | 3 |

### 1.16.14 ChemDrobe 化学师衣柜（chem_wardrobe，10 种）

| 商品 | 库存 |
|---|---|
| 化学师正装 `under/rank/medical/chemist/nova/formal` | 2 |
| 化学师正装裙 `under/rank/medical/chemist/nova/formal/skirt` | 2 |
| 药剂师白大褂 `suit/toggle/labcoat/nova/fancy/pharmacist` | 2 |
| 化学师贝雷帽 `head/beret/medical/chemist` | 2 |
| 化学信使包 `storage/backpack/messenger/chem` | 2 |
| 兔女郎耳·化学师 `head/playbunnyears/chemist` | 2 |
| 兔尾领结·化学师 `neck/tie/bunnytie/chemist` | 2 |
| 化学师燕尾白大褂 `suit/toggle/labcoat/chemist/doctor_tailcoat` | 2 |
| 化学师兔女郎装 `under/rank/medical/chemist/bunnysuit` | 2 |

### 1.16.15 ViroDrobe 病毒学衣柜（viro_wardrobe，6 种）

| 商品 | 库存 |
|---|---|
| 病毒学家贝雷帽 `head/beret/medical/virologist` | 2 |
| 病毒学信使包 `storage/backpack/messenger/vir` | 2 |
| 兔女郎耳·病理学家 `head/playbunnyears/pathologist` | 2 |
| 兔尾领结·病理学家 `neck/tie/bunnytie/pathologist` | 2 |
| 病毒学燕尾白大褂 `suit/toggle/labcoat/virologist/doctor_tailcoat` | 2 |
| 病理学家兔女郎装 `under/rank/medical/pathologist_bunnysuit` | 2 |

### 1.16.16 DetDrobe 侦探衣柜（det_wardrobe，20 种 + 高级 1）

| 商品 | 库存 |
|---|---|
| 米色软呢帽 `head/fedora/beige` | 2 |
| 白色软呢帽 `head/fedora/white` | 2 |
| 赛博 sleek 装 `under/costume/cybersleek` | 2 |
| 赛博 sleek 长款 `under/costume/cybersleek/long` | 2 |
| 赛博护目侦探帽 `head/fedora/det_hat/cybergoggles` | 2 |
| 德卡德风衣 `suit/toggle/deckard` | 2 |
| 跑者防弹背心 `suit/armor/vest/det_suit/runner` | 2 |
| 跑者侦探装 `under/rank/security/detective/runner` | 2 |
| 高可见度白大褂 `suit/toggle/labcoat/nova/highvis` | 2 |
| 牛仔侦探装 `under/rank/security/detective/cowboy` | 2 |
| 牛仔马甲风衣 `suit/toggle/jacket/nova/det_trench/cowboyvest` | 2 |
| 安保信使包 `storage/backpack/messenger/sec` | 2 |
| 兔女郎耳·侦探 `head/playbunnyears/detective` | 2 |
| 兔女郎耳·黑色侦探 `head/playbunnyears/detective/noir` | 2 |
| 兔尾领结·侦探 `neck/tie/bunnytie/detective` | 2 |
| 侦探燕尾服 `suit/jacket/det_suit/tailcoat` | 2 |
| 黑色侦探燕尾服 `suit/jacket/det_suit/tailcoat/noir` | 2 |
| 侦探兔女郎装 `under/rank/security/detective_bunnysuit` | 2 |
| 黑色侦探兔女郎装 `under/rank/security/detective_bunnysuit/noir` | 2 |

**Premium 高级（1 种）**：侦探召唤盘 `disk/nifsoft_uploader/job/summoner/detective` ×2。

### 1.16.17 LawDrobe 律师衣柜（law_wardrobe，20 种）

| 商品 | 库存 |
|---|---|
| 黑尖领西装 `under/suit/nova/black_really_collared` | 3 |
| 黑尖领西装裙 `under/suit/nova/black_really_collared/skirt` | 3 |
| 炼狱西装 `under/suit/nova/inferno` | 3 |
| 炼狱西装裙 `under/suit/nova/inferno/skirt` | 3 |
| 兔女郎耳·黑律师 `head/playbunnyears/lawyer_black` | 3 |
| 兔女郎耳·蓝律师 `head/playbunnyears/lawyer_blue` | 3 |
| 兔女郎耳·好律师 `head/playbunnyears/lawyer_good` | 3 |
| 兔女郎耳·红律师 `head/playbunnyears/lawyer_red` | 3 |
| 兔尾领结·黑律师 `neck/tie/bunnytie/lawyer_black` | 3 |
| 兔尾领结·蓝律师 `neck/tie/bunnytie/lawyer_blue` | 3 |
| 兔尾领结·好律师 `neck/tie/bunnytie/lawyer_good` | 3 |
| 兔尾领结·红律师 `neck/tie/bunnytie/lawyer_red` | 3 |
| 燕尾服·黑律师 `suit/jacket/tailcoat/lawyer_black` | 3 |
| 燕尾服·蓝律师 `suit/jacket/tailcoat/lawyer_blue` | 3 |
| 燕尾服·好律师 `suit/jacket/tailcoat/lawyer_good` | 3 |
| 燕尾服·红律师 `suit/jacket/tailcoat/lawyer_red` | 3 |
| 黑律师兔女郎装 `under/rank/civilian/lawyer_bunnysuit_black` | 3 |
| 蓝律师兔女郎装 `under/rank/civilian/lawyer_bunnysuit_blue` | 3 |
| 好律师兔女郎装 `under/rank/civilian/lawyer_bunnysuit_good` | 3 |
| 红律师兔女郎装 `under/rank/civilian/lawyer_bunnysuit_red` | 3 |

### 1.16.18 JaniDrobe 清洁工衣柜（jani_wardrobe，5 种 + 高级 2）

| 商品 | 库存 |
|---|---|
| 高跟胶鞋 `shoes/galoshes/heeled` | 3 |
| 兔女郎耳·清洁工 `head/playbunnyears/janitor` | 3 |
| 兔尾领结·清洁工 `neck/tie/bunnytie/janitor` | 3 |
| 燕尾服·清洁工 `suit/jacket/tailcoat/janitor` | 3 |
| 清洁工兔女郎装 `under/rank/civilian/janitor/bunnysuit` | 3 |

**Premium 高级（2 种）**：服务召唤盘 `disk/nifsoft_uploader/job/summoner/service` ×2、垃圾压缩机平板套件 `flatpack/trash_compactor` ×1。

### 1.16.19 CuraDrobe 策展人衣柜（curator_wardrobe，9 种）

| 商品 | 库存 |
|---|---|
| 兔女郎耳·绿策展人 `head/playbunnyears/curator_green` | 2 |
| 兔女郎耳·红策展人 `head/playbunnyears/curator_red` | 2 |
| 兔女郎耳·青策展人 `head/playbunnyears/curator_teal` | 2 |
| 燕尾服·绿策展人 `suit/jacket/tailcoat/curator_green` | 2 |
| 燕尾服·红策展人 `suit/jacket/tailcoat/curator_red` | 2 |
| 燕尾服·青策展人 `suit/jacket/tailcoat/curator_teal` | 2 |
| 绿策展人兔女郎装 `under/rank/civilian/curator_bunnysuit_green` | 2 |
| 红策展人兔女郎装 `under/rank/civilian/curator_bunnysuit_red` | 2 |
| 青策展人兔女郎装 `under/rank/civilian/curator_bunnysuit_teal` | 2 |

### 1.16.20 MagiVend 巫师机（magivend，3 种）

| 商品 | 库存 |
|---|---|
| 兔尾领结·魔术师 `neck/tie/bunnytie/magician` | 3 |
| 魔术师巫师袍 `suit/wizrobe/magician` | 3 |
| 魔术师兔女郎装 `under/costume/playbunny/magician` | 3 |

---

# 第二卷 · 导入贩售机全录

**代码**: `modular_nova/modules/imported_vendors/`（949 行）——四种"文化导入"售货机品牌，可随机替换地图上的零食/汽水机（MODULE ID: CulturalVendors）。含图标文件 `icons/imported_vendors.dmi` + `icons/imported_quick_foods.dmi`。

## 2.0 模块框架（imported 基类）

**源码**: `imported_vendors/code/vendors.dm`（233 行）第 24–31 行

`/obj/machinery/vending/imported` 基类属性：

| 属性 | 值 |
|---|---|
| 图标 | `imported_vendors.dmi` |
| 面板 | panel15（15 键面板） |
| 默认价格 | `default_price = PAYCHECK_CREW × 0.5` = **25 信用点** |
| 高级价格 | `extra_price = PAYCHECK_COMMAND` = **100 信用点** |
| 付款部门 | `NO_FREEBIES`（不免费） |
| 随机生成 | 加入 `/obj/effect/spawner/random/vending/snackvend` 与 `/colavend` 掉落池（与 `/obj/machinery/vending/deforest_medvend` 并列） |
| 补给 | `/datum/supply_pack/vending/imported` 的 `fill()` 会向板条箱塞入所有 `/obj/item/vending_refill/snack/imported` 子类型补货罐 |

各机型均 `allow_custom = TRUE`（允许玩家自定义商品）。

## 2.1 NT Sustenance Supplier（NT 补给机）

**源码**: `vendors.dm` 第 32–73 行 · **机型**: `/obj/machinery/vending/imported/nt` · **icon_state**: `nt_food`

- **名称**: NT Sustenance Supplier（NT 生存补给供应机）
- **描述**: "A vending machine serving up only the finest of human college student food."（供应人类大学生级"最优质"食物的售货机。）
- **灯光**: 亮青色（LIGHT_COLOR_LIGHT_CYAN），光罩 `nt_food-light-mask`
- **广告语**: "Caution, contents may be selling hot!;Look at these low prices!;Hungry? Me too- Wait, no, you didn't hear that!"
- **补货罐**: `/obj/item/vending_refill/snack/imported/nt`（machine_name = "NT Sustenance Supplier"）

### Snacks 零食分类（9 种 × 6，icon: cookie）

| 商品 | 库存 |
|---|---|
| 花生（随机） `food/peanuts/random` | 6 |
| 玉米片（随机） `food/cnds/random` | 6 |
| 开心果 `food/pistachios` | 6 |
| 玉米片（随机） `food/cornchips/random` | 6 |
| 苏斯肉干 `food/sosjerky` | 6 |
| 可乐 `soda_cans/cola` | 6 |
| 柠檬青柠汽水 `soda_cans/lemon_lime` | 6 |
| 星吻汽水 `soda_cans/starkist` | 6 |
| 能量游戏汽水 `soda_cans/pwr_game` | 6 |

### Meals 套餐分类（6 种 × 6，icon: pizza-slice）

| 商品 | 库存 |
|---|---|
| NT 套餐盒·索尔兹伯里牛排 `storage/box/foodpack/nt` | 6 |
| NT 套餐盒·芝士汉堡 `storage/box/foodpack/nt/burger` | 6 |
| NT 套餐盒·辣鸡三明治 `storage/box/foodpack/nt/chicken_sammy` | 6 |
| NT 配菜·扁面包花生酱 `food/vendor_tray_meal/side` | 6 |
| NT 配菜·扁面包浆果酱 `food/vendor_tray_meal/side/crackers_and_jam` | 6 |
| NT 配菜·扁面包奶酪酱 `food/vendor_tray_meal/side/crackers_and_cheese` | 6 |

## 2.2 Fudobenda（阳渔 Fudobenda）

**源码**: `vendors.dm` 第 75–130 行 · **机型**: `/obj/machinery/vending/imported/yangyu` · **icon_state**: `yangyu_food`

- **名称**: Fudobenda（源自 Sol 东方"福田便当"）
- **描述**: "A vendor selling traditional Sol eastern foods of dubious quality."（售卖品质存疑的传统太阳系东方食物的贩售机。）
- **灯光**: 亮橙色/火焰色（LIGHT_COLOR_FLARE），光罩 `yangyu_food-light-mask`
- **广告语**: "Fresh farmed space carp from local space!;Imitation lobstrocity sushi choices availible!;Made with traditional recipes and care!"
- **语言**: 默认语言持有人 `yangyu_vendor`（能听/说阳渔语 yangyu，LANGUAGE_ATOM 原子级）
- **examine_more**: 追加隐藏文本（LANG 键 obj.c9c5e613）
- **补货罐**: `/obj/item/vending_refill/snack/imported/yangyu`（machine_name = "Fudobenda"）

### Snacks 零食分类（9 种 × 6，icon: cookie）

| 商品 | 库存 |
|---|---|
| 杯面（已泡好） `cup/glass/dry_ramen/prepared` | 6 |
| 辣杯面（已泡好） `cup/glass/dry_ramen/prepared/hell` | 6 |
| 米饼 `food/vendor_snacks/rice_crackers` | 6 |
| 麻糬冰淇淋球·香草 `food/vendor_snacks/mochi_ice_cream` | 6 |
| 麻糬冰淇淋球·抹茶 `food/vendor_snacks/mochi_ice_cream/matcha` | 6 |
| 瓶装茶 `cup/glass/waterbottle/tea` | 6 |
| 瓶装茶·阿斯特拉 `cup/glass/waterbottle/tea/astra` | 6 |
| 瓶装草莓茶 `cup/glass/waterbottle/tea/strawberry` | 6 |
| 瓶装猫薄荷茶 `cup/glass/waterbottle/tea/nip` | 6 |

### Meals 套餐分类（6 种 × 6，icon: pizza-slice）

| 商品 | 库存 |
|---|---|
| 阳渔套餐盒·家常面 `storage/box/foodpack/yangyu` | 6 |
| 阳渔套餐盒·鲤鱼寿司卷 `storage/box/foodpack/yangyu/sushi` | 6 |
| 阳渔套餐盒·牛肉饭 `storage/box/foodpack/yangyu/beef_rice` | 6 |
| 阳渔配菜·味噌汤 `food/vendor_tray_meal/side/miso` | 6 |
| 阳渔配菜·白米饭 `food/vendor_tray_meal/side/rice` | 6 |
| 阳渔配菜·酱菜 `food/vendor_tray_meal/side/pickled_vegetables` | 6 |

## 2.3 Nomad Fleet Ration Chit Exchange（游牧舰队配给机）

**源码**: `vendors.dm` 第 132–182 行 · **机型**: `/obj/machinery/vending/imported/mothic` · **icon_state**: `moth_food`

- **名称**: Nomad Fleet Ration Chit Exchange（游牧舰队配给券兑换机）
- **描述**: "One of the Nomad Fleet's own ration vendors; in spite of the name engraved into it, it's been fitted to accept credits."（游牧舰队自己的配给机；尽管刻着这个名字，已被改装为接受信用点。）
- **灯光**: 卤素白光（LIGHT_COLOR_HALOGEN），光罩 `moth_food-light-mask`
- **广告语**: "Support the fleet, conserve rations today!;Some options in reduced portion and cost!;Do your part to keep the fleet flying!"
- **语言**: 默认语言持有人 `moffic_vendor`（能听/说莫蒂克语 moffic）
- **补货罐**: `/obj/item/vending_refill/snack/imported/mothic`（machine_name = "Nomad Fleet Ration Chit Exchange"）

### Snacks 零食分类（9 种 × 6，icon: cookie）

| 商品 | 库存 |
|---|---|
| 蛾糖 `food/vendor_snacks/mothmallow` | 6 |
| 引擎饲料 `food/vendor_snacks/moth_bag` | 6 |
| 燃料杰克零食 `food/vendor_snacks/moth_bag/fuel_jack` | 6 |
| 巧克力芝士蛋糕块 `food/vendor_snacks/moth_bag/cheesecake` | 6 |
| 蜂蜜芝士蛋糕块 `food/vendor_snacks/moth_bag/cheesecake/honey` | 6 |
| 柠檬水（Gyárhajó 1023） `soda_cans/nova/lemonade` | 6 |
| 海军朗姆（Gyárhajó 1506） `soda_cans/nova/navy_rum` | 6 |
| 苏打水（Gyárhajó 1023） `soda_cans/nova/soda_water_moth` | 6 |
| 姜汁啤酒（Gyárhajó 1023） `soda_cans/nova/ginger_beer` | 6 |

### Meals 套餐分类（6 种 × 6，icon: pizza-slice）

| 商品 | 库存 |
|---|---|
| 莫蒂克套餐盒·香蒜披萨 `storage/box/foodpack/moth` | 6 |
| 莫蒂克套餐盒·烤饭烤芝士 `storage/box/foodpack/moth/baked_rice` | 6 |
| 莫蒂克套餐盒·燃料杰克盛宴 `storage/box/foodpack/moth/fuel_jack` | 6 |
| 莫蒂克配菜·莫芬 `food/vendor_tray_meal/side/moffin` | 6 |
| 莫蒂克配菜·玉米面包 `food/vendor_tray_meal/side/cornbread` | 6 |
| 莫蒂克配菜·烤种子 `food/vendor_tray_meal/side/roasted_seeds` | 6 |

## 2.4 Tiziran Imported Delicacies（提兹拉进口机）

**源码**: `vendors.dm` 第 184–233 行 · **机型**: `/obj/machinery/vending/imported/tiziran` · **icon_state**: `tizira_food`

- **名称**: Tiziran Imported Delicacies（提兹拉进口珍馐）
- **描述**: "A vendor serving a fine collection of what is very likely knock-offs of popular Tiziran brands."（售卖一系列极可能是热门提兹拉品牌仿冒品的贩售机。）
- **灯光**: 火焰橙（LIGHT_COLOR_FIRE），光罩 `tizira_food-light-mask`
- **广告语**: "Real imports from the capital itself, we promise!;Rare selections of salt water catch!;Moonfish glaze included with all meat options!"
- **语言**: 默认语言持有人 `draconic_vendor`（能听/说龙语 draconic）
- **补货罐**: `/obj/item/vending_refill/snack/imported/tiziran`（machine_name = "Tiziran Imported Delicacies"）

### Snacks 零食分类（8 种 × 6，icon: cookie）

| 商品 | 库存 |
|---|---|
| 虾片 `food/chips/shrimp` | 6 |
| 蜜渍蘑菇 `food/vendor_snacks/lizard_bag` | 6 |
| 月鱼干 `food/vendor_snacks/lizard_bag/moon_jerky` | 6 |
| 提兹拉饺子 `food/vendor_snacks/lizard_box` | 6 |
| 蜜卷 `food/vendor_snacks/lizard_box/sweet_roll` | 6 |
| 穆希康普茶 `cup/glass/bottle/mushi_kombucha` | 6 |
| 瓶装蘑菇茶 `cup/glass/waterbottle/tea/mushroom` | 6 |
| 科尔塔拉酒 `soda_cans/nova/kortara` | 6 |

### Meals 套餐分类（6 种 × 6，icon: pizza-slice）

| 商品 | 库存 |
|---|---|
| 提兹拉套餐盒·月鱼尼扎亚 `storage/box/foodpack/tizira` | 6 |
| 提兹拉套餐盒·帝王卷 `storage/box/foodpack/tizira/roll` | 6 |
| 提兹拉套餐盒·蘑菇炒菜 `storage/box/foodpack/tizira/stir_fry` | 6 |
| 提兹拉配菜·根面包饼干肉酱 `food/vendor_tray_meal/side/root_crackers` | 6 |
| 提兹拉配菜·科尔塔脆饼 `food/vendor_tray_meal/side/korta_brittle` | 6 |
| 提兹拉配菜·脆头奶酪 `food/vendor_tray_meal/side/crispy_headcheese` | 6 |

## 2.5 商品全录 · 通用与阳渔

**源码**: `vendor_snacks.dm`（302 行）+ `vendor_food.dm`（325 行）+ `vendor_containers.dm`（89 行）

### 2.5.1 通用基底

| 物品 | 说明 |
|---|---|
| **God's Strongest Snacks** `/obj/item/food/vendor_snacks` | 调试/基底零食。name="God's Strongest Snacks"，描述调侃"你不该看到这个物品请上报"。图标 `imported_quick_foods.dmi`。营养无限（nutriment = INFINITY），垃圾食品度 10，自定义价格 PAYCHECK_LOWER × INFINITY，口味"太阳的无匹之力"，foodtypes 含 JUNKFOOD/CLOTH/GORE/NUTS/FRIED/FRUIT（"你不会想知道调试零食里有什么"），小体积。产生垃圾 `vendor_trash` |
| **God's Weakest Snacks** `/obj/item/trash/vendor_trash` | 基底垃圾袋（"曾经是美味零食的残骸"），塑料材质半片 |
| **空塑料餐盘** `/obj/item/trash/empty_food_tray` | 套餐餐盘垃圾，塑料半片 |
| **空配菜包装** `/obj/item/trash/empty_side_pack` | 配菜垃圾，塑料半片；NT/阳渔/莫蒂克/提兹拉四色变体（`empty_side_pack/nt`、`/yangyu`、`/moth`、`/tizira`） |
| **随机主菜生成器** `/obj/effect/spawner/random/vendor_meal_mains` | 抽象型随机主菜生成器 |
| **随机配菜生成器** `/obj/effect/spawner/random/vendor_meal_sides` | 配菜随机生成器（各品牌子类在 Initialize 填入 loot） |

**零食垃圾类型索引（vendor_trash 子类型全录）**：空米饼袋 `trash/vendor_trash/rice_crackers`（可吹爆 FOOD_TRASH_POPABLE）、空麻糬托盘 `trash/vendor_trash/mochi_ice_cream`、空蛾糖袋 `trash/vendor_trash/mothmallow`、空莫蒂克零食袋 `trash/vendor_trash/moth_bag`、空提兹拉零食袋 `trash/vendor_trash/lizard_bag`、空提兹拉零食盒 `trash/vendor_trash/lizard_box`。

### 2.5.2 阳渔零食（Yangyu Snacks）

| 商品 | 代码 | 成分/数据 | 价格 |
|---|---|---|---|
| 杯面 cup ramen | `cup/glass/dry_ramen/prepared` | 热拉面 15u + 盐 3u；"这碗自带水，神奇！" | — |
| 辣杯面 spicy cup ramen | `cup/glass/dry_ramen/prepared/hell` | 地狱拉面 15u + 盐 3u；"自带水，外加一个安检站量的辣椒素！" | — |
| 米饼 rice crackers | `food/vendor_snacks/rice_crackers` | 营养 2 + 米 2；口味"难以理解的风味"+"米饼"；JUNKFOOD\|GRAIN；垃圾：空米饼袋（可吹爆 FOOD_TRASH_POPABLE） | PAYCHECK_LOWER × 0.8 = 20 |
| 麻糬冰淇淋球·香草 | `food/vendor_snacks/mochi_ice_cream` | 营养 3 + 冰 3；"米糕"+"香草"；JUNKFOOD\|DAIRY\|GRAIN；六颗装+塑料签；垃圾：空麻糬托盘 | PAYCHECK_LOWER = 25 |
| 麻糬冰淇淋球·抹茶 | `food/vendor_snacks/mochi_ice_cream/matcha` | 营养 3 + 冰 1 + 茶 2；"米糕"+"苦抹茶"；examine_more 有隐藏文本 | PAYCHECK_LOWER × 1.2 = 30 |
| 瓶装茶 bottle of tea | `cup/glass/waterbottle/tea` | 茶 40u；瓶盖可翻转（flip_chance 5，"我赌你不敢"） | PAYCHECK_LOWER × 1.2 = 30 |
| 瓶装茶·阿斯特拉 | `cup/glass/waterbottle/tea/astra` | 茶 25u + 盐水葡萄糖液 10u（磨粉产物即此药）+ 维生素 5u；"25XX 年的茶" | PAYCHECK_LOWER × 2 = 50 |
| 瓶装草莓茶 | `cup/glass/waterbottle/tea/strawberry` | 粉茶 40u；"不含任何真草莓" | PAYCHECK_LOWER × 2 = 50 |
| 瓶装猫薄荷茶 | `cup/glass/waterbottle/tea/nip` | 猫薄荷茶 20u + 粉奶 20u（"居然给我兑水！"）；SFDA 规定浓度≤50% | PAYCHECK_LOWER × 2.5 = 62.5 |

### 2.5.3 通用/基底套餐盒（foodpack）

| 套餐盒 | 代码 | 主菜 | 配菜 | 酱料 | 价格 |
|---|---|---|---|---|---|
| 纸包餐盒（基底） | `storage/box/foodpack` | 空餐盘（占位） | 基底零食（占位） | 番茄酱包 | PAYCHECK_CREW × 1.8 = 90 |

**包装垃圾四色变体**：`empty_side_pack/nt`（foodpack_nt_trash）、`/yangyu`、`/moth`、`/tizira`。

## 2.6 商品全录 · 莫蒂克

### 2.6.1 莫蒂克零食（Mothic Snacks）

| 商品 | 代码 | 成分/数据 | 价格 |
|---|---|---|---|
| 蛾糖 mothmallow | `food/vendor_snacks/mothmallow` | 营养 2 + 糖 4；"香草"+"棉花"+"巧克力"；VEGETABLES\|SUGAR；真空袋装压扁的蛾糖（"救救他！"）；垃圾：空袋（"他终于自由了"） | PAYCHECK_LOWER = 25 |
| 引擎饲料 engine fodder | `food/vendor_snacks/moth_bag` | 糖 3 + 营养 2 + 盐 2；口味六合一（种子/坚果/巧克力/盐/爆米花/土豆）；GRAIN\|NUTS\|VEGETABLES\|SUGAR；垃圾：空莫蒂克零食袋 | PAYCHECK_LOWER × 1.2 = 30 |
| 燃料杰克零食 | `food/vendor_snacks/moth_bag/fuel_jack` | 营养 3 + 蛋白质 1；"卷心菜/土豆/洋葱/辣椒/奶酪"；DAIRY\|VEGETABLES；燃料杰克午餐的迷你版 | PAYCHECK_LOWER × 1.2 = 30 |
| 巧克力芝士蛋糕块 | `food/vendor_snacks/moth_bag/cheesecake` | 蛋白质 2 + 糖 4；"芝士蛋糕"+"巧克力"；SUGAR\|FRIED\|DAIRY\|GRAIN | PAYCHECK_LOWER × 1.4 = 35 |
| 蜂蜜芝士蛋糕块 | `food/vendor_snacks/moth_bag/cheesecake/honey` | 同上但蜂蜜版；"芝士蛋糕"+"蜂蜜" | 继承 35 |

### 2.6.2 莫蒂克罐装饮料（Gyárhajó 船厂系列，`soda_cans/nova/`）

| 商品 | 代码 | 成分 | 类型 |
|---|---|---|---|
| 柠檬水 "Gyárhajó 1023: Lemonade" | `soda_cans/nova/lemonade` | 柠檬水 30u | FRUIT；examine_more 隐藏文本 |
| 海军朗姆 "Gyárhajó 1506: Navy Rum" | `soda_cans/nova/navy_rum` | 海军朗姆酒精 30u | ALCOHOL；examine_more 隐藏文本 |
| 苏打水 "Gyárhajó 1023: Soda Water" | `soda_cans/nova/soda_water_moth` | 苏打水 30u | SUGAR；examine_more 隐藏文本 |
| 姜汁啤酒 "Gyárhajó 1023: Ginger Beer" | `soda_cans/nova/ginger_beer` | sol_dry 30u | SUGAR；完全无酒精（"别被啤酒二字骗了"） |

### 2.6.3 莫蒂克套餐盒与主菜/配菜

**套餐盒**：`foodpack/moth`（配菜随机：莫芬/玉米面包/烤种子；酱料：astrotame 代糖包）→ 子类 `moth/baked_rice`、`moth/fuel_jack`。价格 PAYCHECK_CREW × 1.8 = 90。

**主菜（vendor_tray_meal）**：

| 主菜 | 代码 | 数据 |
|---|---|---|
| 香蒜披萨 "Main Course - Type M: Pesto Pizza" | `vendor_tray_meal/pesto_pizza` | 营养 8 基底；"番茄？/奶酪？/香草"；GRAIN\|DAIRY\|VEGETABLES；出厂即冒蒸汽（hot_and_steamy） |
| 烤饭烤芝士 "Main Course - Type M: Baked Rice and Grilled Cheese" | `vendor_tray_meal/baked_rice` | 舰队风米饭+烤焦芝士；"米饭/辣椒/焦芝士"；GRAIN\|DAIRY\|VEGETABLES |
| 燃料杰克餐盘 "Main Course - Type M: Fueljack's Tray" | `vendor_tray_meal/fueljack` | 燃料杰克午餐扁平版；"土豆/卷心菜/奶酪？"；DAIRY\|VEGETABLES |

**配菜（vendor_tray_meal/side）**：

| 配菜 | 代码 | 数据 |
|---|---|---|
| 莫芬 moffin | `side/moffin` | 被压扁成威化状的莫芬；"布料？/糖"；CLOTH\|GRAIN\|SUGAR |
| 玉米面包 cornbread | `side/cornbread` | 甜玉米面包切片；"玉米面包/甜味"；GRAIN\|SUGAR |
| 烤种子 roasted_seeds | `side/roasted_seeds` | 混合烤箱烤种子；"种子/焦香"；NUTS |

## 2.7 商品全录 · 提兹拉

### 2.7.1 提兹拉零食（Tiziran Snacks）

| 商品 | 代码 | 成分/数据 | 价格 |
|---|---|---|---|
| 蜜渍蘑菇 candied mushroom | `food/vendor_snacks/lizard_bag` | 营养 3 + 焦糖 2；"咸香"+"甜"；SUGAR\|VEGETABLES；焦糖未完全凝固就装袋；垃圾：空提兹拉零食袋 | PAYCHECK_LOWER × 1.4 = 35（提兹拉进口整体偏贵） |
| 月鱼干 moonfish jerky | `food/vendor_snacks/lizard_bag/moon_jerky` | 蛋白质 2 + 烧烤酱 2；"鱼"+"烟熏酱"；MEAT | PAYCHECK_LOWER × 1.6 = 40 |
| 提兹拉饺子 tiziran dumplings | `food/vendor_snacks/lizard_box` | 营养 3；"土豆"+"土味热"；VEGETABLES\|NUTS；三只装、无馅；垃圾：空提兹拉零食盒 | PAYCHECK_LOWER × 1.6 = 40 |
| 蜜卷 honey roll | `food/vendor_snacks/lizard_box/sweet_roll` | 营养 2 + 蜂蜜 2；"面包/蜂蜜/水果"；VEGETABLES\|NUTS\|FRUIT；"千万别让守卫发现你偷了最后一个" | PAYCHECK_LOWER × 1.8 = 45 |
| 瓶装蘑菇茶 | `cup/glass/waterbottle/tea/mushroom` | 蘑菇茶 40u；"提兹拉帝国的最爱" | PAYCHECK_LOWER × 2 = 50 |
| 科尔塔拉酒 kortara | `soda_cans/nova/kortara` | 科尔塔拉酒精 30u（korta 种子酿制，胡椒辛辣味） | ALCOHOL |

### 2.7.2 提兹拉套餐盒与主菜/配菜

**套餐盒**：`foodpack/tizira`（主菜月鱼尼扎亚；配菜随机：根面包饼干肉酱/科尔塔脆饼/脆头奶酪；酱料：烧烤酱；**价格 PAYCHECK_CREW × 2 = 100**，examine_more 有隐藏文本，外包装盖提兹拉出口办公室真品章）→ 子类 `tizira/roll`（帝王卷）、`tizira/stir_fry`（蘑菇炒菜）。

**主菜**：

| 主菜 | 代码 | 数据 |
|---|---|---|
| 月鱼尼扎亚 "Tizira Imports: Moonfish and Nizaya" | `vendor_tray_meal/moonfish_nizaya` | 合成感月鱼+尼扎亚意面；"鱼？/廉价面条"；VEGETABLES\|NUTS\|SEAFOOD |
| 帝王卷 "Tizira Imports: Emperor Roll" | `vendor_tray_meal/emperor_roll` | 鱼子酱显然不在预算内；"面包/奶酪？/肝？"；VEGETABLES\|NUTS\|MEAT\|GORE |
| 蘑菇炒菜 "Tizira Imports: Mushroom Stirfry" | `vendor_tray_meal/mushroom_fry` | 低质蘑菇轻炸装盒；"蘑菇/发财"；VEGETABLES |

**配菜**：

| 配菜 | 代码 | 数据 |
|---|---|---|
| 根面包饼干肉酱 rootbread crackers and pate | `side/root_crackers` | "硬根面包/肉酱"；VEGETABLES\|NUTS\|MEAT |
| 科尔塔脆饼 korta brittle | `side/korta_brittle` | 无糖科尔塔脆饼（完美矩形）；"胡椒热"；NUTS |
| 脆头奶酪 crisped headcheese | `side/crispy_headcheese` | 面包糠裹头奶酪块；"奶酪/油"；MEAT\|VEGETABLES\|NUTS\|GORE |

## 2.8 套餐盒全录（foodpack 容器）

**源码**: `vendor_containers.dm`（89 行）

通用机制：`/obj/item/storage/box/foodpack` 在 `PopulateContents()` 时生成 **主菜 + 配菜 + 酱料包** 三件套；外观棕色纸包（`foodpack_generic_big`），自定义价格 PAYCHECK_CREW × 1.8 = 90。

### 各品牌套餐盒总表

| 套餐盒 | 代码 | 名称 | 主菜 | 配菜（随机） | 酱料包 | 价格 |
|---|---|---|---|---|---|---|
| NT 牛排套餐 | `foodpack/nt` | NT-Combo Meal - Salisbury Steak | 索尔兹伯里牛排 | NT 三选一 | 番茄酱 | 90 |
| NT 汉堡套餐 | `foodpack/nt/burger` | NT-Combo Meal - Cheeseburger | 芝士汉堡 | 同上 | 番茄酱 | 90 |
| NT 辣鸡套餐 | `foodpack/nt/chicken_sammy` | NT-Combo Meal - Spicy Chicken Sandwich | 辣鸡三明治 | 同上 | 番茄酱 | 90 |
| 阳渔家常面 | `foodpack/yangyu` | Atatakai shokuji - Homestyle Noodles | 家常面 | 阳渔三选一 | 辣酱 | 90 |
| 阳渔寿司卷 | `foodpack/yangyu/sushi` | Atatakai shokuji - Carp Sushi Rolls | 鲤鱼寿司卷 | 同上 | 辣酱 | 90 |
| 阳渔牛肉饭 | `foodpack/yangyu/beef_rice` | Atatakai shokuji - Beef and Rice | 牛肉饭 | 同上 | 辣酱 | 90 |
| 莫蒂克香蒜披萨 | `foodpack/moth` | Ration Type M - Pesto Pizza | 香蒜披萨 | 莫蒂克三选一 | 代糖包 | 90 |
| 莫蒂克烤饭 | `foodpack/moth/baked_rice` | Ration Type M - Baked Rice and Grilled Cheese | 烤饭烤芝士 | 同上 | 代糖包 | 90 |
| 莫蒂克燃料杰克 | `foodpack/moth/fuel_jack` | Ration Type M - Fueljack's Feast | 燃料杰克餐盘 | 同上 | 代糖包 | 90 |
| 提兹拉月鱼 | `foodpack/tizira` | Tizira Imports Pack - Moonfish Nizaya | 月鱼尼扎亚 | 提兹拉三选一 | 烧烤酱 | **100** |
| 提兹拉帝王卷 | `foodpack/tizira/roll` | Tizira Imports Pack - Emperor Roll | 帝王卷 | 同上 | 烧烤酱 | 100 |
| 提兹拉蘑菇炒 | `foodpack/tizira/stir_fry` | Tizira Imports Pack - Mushroom Stirfry | 蘑菇炒菜 | 同上 | 烧烤酱 | 100 |

**配菜随机生成器 loot 表**：
- NT（`vendor_meal_sides/nt`）：扁面包花生酱 / 扁面包浆果酱 / 扁面包奶酪酱
- 阳渔（`vendor_meal_sides/yangyu`）：味噌汤 / 白米饭 / 酱菜
- 莫蒂克（`vendor_meal_sides/moth`）：莫芬 / 玉米面包 / 烤种子
- 提兹拉（`vendor_meal_sides/tizira`）：根面包饼干肉酱 / 科尔塔脆饼 / 脆头奶酪

### NT 主菜/配菜细节

**主菜**（全部出厂冒蒸汽 hot_and_steamy=TRUE，examine_more 显示"警告：工厂内含所有已知过敏原"+"内容物可能很烫"+"每 200g：钠 8g/脂肪 25g（饱和 22g）/糖 2g"）：

| 主菜 | 代码 | 数据 |
|---|---|---|
| 牛排通心粉 "NT-Meal: Steak and Macaroni" | `vendor_tray_meal`（基底） | 营养 8；"肉？/奶酪？/懒惰"；MEAT\|GRAIN\|DAIRY；手指食物；垃圾：空塑料餐盘 |
| 芝士汉堡 "NT-Meal: Cheeseburger" | `vendor_tray_meal/burger` | 沮丧汉堡+荧光黄芝士+湿底面包；"面包/肉？/奶酪？/懒惰"；MEAT\|GRAIN\|DAIRY |
| 辣鸡三明治 "NT-Meal: Spicy Chicken Sandwich" | `vendor_tray_meal/chicken_sandwich` | 营养 5 + 辣椒素 10u；"面包/鸡？/压倒性香料/懒惰"；MEAT\|GRAIN\|DAIRY |

**配菜**（hot_and_steamy=FALSE 不冒蒸汽，价格 PAYCHECK_LOWER × 2.5 = 62.5）：

| 配菜 | 代码 | 数据 |
|---|---|---|
| 扁面包花生酱 "NT-Side: Flatbread and Peanut Butter" | `side`（基底） | 营养 5；"硬面包/花生酱"；GRAIN |
| 扁面包浆果酱 "NT-Side: Flatbread and Berry Jelly" | `side/crackers_and_jam` | "硬面包/浆果"；GRAIN\|FRUIT |
| 扁面包奶酪酱 "NT-Side: Flatbread and Cheese Spread" | `side/crackers_and_cheese` | "硬面包/奶酪"；GRAIN\|DAIRY |

### 阳渔主菜/配菜细节

| 主菜 | 代码 | 数据 |
|---|---|---|
| 家常面 "Meinkosu: Homestyle Noodles" | `vendor_tray_meal/ramen` | "廉价面条/懒惰"；GRAIN\|VEGETABLES（工厂拉面砖+再水化蔬菜） |
| 鲤鱼寿司卷 "Meinkosu: Fresh Carp Rolls" | `vendor_tray_meal/sushi` | "仿太空鲤鱼/陈米/懒惰"；GRAIN\|SEAFOOD（标签在骗你） |
| 牛肉炒饭 "Meinkosu: Beef and Fried Rice" | `vendor_tray_meal/beef_rice` | "廉价牛肉/米饭/懒惰"；GRAIN\|MEAT |

| 配菜 | 代码 | 数据 |
|---|---|---|
| 味噌汤 "Fukusai: Miso Soup" | `side/miso` | 一塑料包味噌汤；"味噌"；VEGETABLES |
| 白米饭 "Fukusai: White Rice" | `side/rice` | "陈米"；GRAIN |
| 酱菜 "Fukusai: Pickled Vegetables" | `side/pickled_vegetables` | "醋"；VEGETABLES |

## 2.9 随机生成与补给整合

**源码**: `vendors.dm` 第 1–23 行

| 整合点 | 说明 |
|---|---|
| 零食机随机池 `spawner/random/vending/snackvend` | loot：NT / 阳渔 / 莫蒂克 / 提兹拉 / Deforest 医疗机（deforest_medvend）五选一 |
| 汽水机随机池 `spawner/random/vending/colavend` | 同上五选一（"这些机器既能卖零食也能卖饮料，所以两个池都进"） |
| 补给包 `datum/supply_pack/vending/imported/fill()` | 向板条箱塞入全部 `/obj/item/vending_refill/snack/imported` 子类型补货罐（NT/阳渔/莫蒂克/提兹拉 4 种） |
| 补货罐 `vending_refill/snack/imported/*` | 4 种，各自 `machine_name` 对应机型 |

---

# 附录 · 代码路径索引

## modular_vending 模块（第一卷）

| 源码文件 | 行数 | 内容 |
|---|---|---|
| `modular_vending/code/vending.dm` | 93 | 框架：products_nova 四类注入 / MINIMUM_CLOTHING_STOCK=5 / allow_increase / spawn_frame / excluded_products |
| `modular_vending/code/autodrobe.dm` | 139 | AutoDrobe：5 分类 85 商品 + 违禁 14 + 高级 1 |
| `modular_vending/code/clothesmate.dm` | 239 | ClothesMate：6 分类 178 商品 + 高级 9 + 违禁 1 |
| `modular_vending/code/boozeomat.dm` | 21 | Booze-O-Mat：合成乙醇罐 + Cafe 变体（矿石高级/无限库存/无年龄限制） |
| `modular_vending/code/cigarette.dm` | 14 | 香烟机：电子烟 + 违禁烟弹 |
| `modular_vending/code/cola.dm` | 13 | 可乐机：润滑油可乐/焊接气泡水/THC + 4·20 节日 |
| `modular_vending/code/dinnerware.dm` | 2 | 餐具机：服务召唤盘 |
| `modular_vending/code/drugs.dm` | 14 | 药品机：神经软件盘 |
| `modular_vending/code/engivend.dm` | 8 | 工程机：RCD 回归 + 工具 |
| `modular_vending/code/games.dm` | 39 | 游戏机：玩具/其他分类 + 移除俄罗斯左轮 |
| `modular_vending/code/medical.dm` | 9 | 医疗机：TTS 装置 + 手术召唤盘 |
| `modular_vending/code/megaseed.dm` | 20 | 种子机：4 新种子 + 违禁种子 |
| `modular_vending/code/modularpc.dm` | 36 | 模块电脑机：22 PDA/神经盘 + 违禁 8 |
| `modular_vending/code/security.dm` | 14 | 安保机：动能球/胡椒球 + Type-207 |
| `modular_vending/code/snack.dm` | 8 | 零食机：4·20 节日 THC 食品 |
| `modular_vending/code/tool.dm` | 11 | 工具机：Fock 工具 + 破门撬棍 |
| `modular_vending/code/wardrobes.dm` | 335 | 20 机型衣柜改造（含 RoboDrobe 机器人急救站） |

## imported_vendors 模块（第二卷）

| 源码文件 | 行数 | 内容 |
|---|---|---|
| `imported_vendors/readme.md` | 25 | 模块说明（MODULE ID: CulturalVendors） |
| `imported_vendors/code/vendors.dm` | 233 | imported 基类 + 4 种机型 + 语言持有人 + 随机池/补给整合 |
| `imported_vendors/code/vendor_snacks.dm` | 302 | 零食/饮料商品全定义（阳渔/莫蒂克/提兹拉） |
| `imported_vendors/code/vendor_food.dm` | 325 | 套餐主菜/配菜全定义 + 随机生成器 |
| `imported_vendors/code/vendor_containers.dm` | 89 | foodpack 套餐盒容器（主菜+配菜+酱料） |
| `imported_vendors/icons/imported_vendors.dmi` | — | 售货机机体图标 |
| `imported_vendors/icons/imported_quick_foods.dmi` | — | 商品/垃圾图标 |

---

**统计**: 第一卷 35 种机型（15 种基础机 + 20 种衣柜/MagiVend/推车）、第二卷 4 种导入机型 + 60+ 种商品与 12 种套餐盒全录。双语全量，数值取自源码。
