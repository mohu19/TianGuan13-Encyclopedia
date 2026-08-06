# 天关 — 食物·饮料·酒精系统完全百科

> **项目**: TianGuan13 (Nova Sector → /tg/station)
> **代码**: `code/modules/food_and_drinks/recipes/`（276 配方）+ `code/modules/reagents/chemistry/reagents/{food_reagents,drinks/}.dm`（668 试剂）
> **范围**: 本篇章覆盖餐饮系统的**化学配方层**（混合饮料/发酵/食物混合）与**试剂效用层**。注：实体食物（汉堡/披萨/蛋糕等成品菜的桌台合成 crafting_recipe）属另一体系，本篇章聚焦 `/datum/chemical_reaction` 定义的部分。

## 目录

- [第一卷 · 系统架构](#第一卷--系统架构)
- [第二卷 · 基酒与发酵](#第二卷--基酒与发酵)
- [第三卷 · 经典鸡尾酒（威士忌/朗姆/伏特加系）](#第三卷--经典鸡尾酒威士忌朗姆伏特加系)
- [第四卷 · 无酒精饮料](#第四卷--无酒精饮料)
- [第五卷 · 种族特色饮品（蜥蜴/蛾）](#第五卷--种族特色饮品蜥蜴蛾)
- [第六卷 · 食物混合与调味品](#第六卷--食物混合与调味品)
- [第七卷 · 试剂效用速查（饮料/酒精效果数值）](#第七卷--试剂效用速查饮料酒精效果数值)
- [第八卷 · 实体食物桌台合成](#第八卷--实体食物桌台合成crafting_recipe)
- [第九卷 · 食物试剂效果](#第九卷--食物试剂效果)
- [附录A · 代码路径索引](#附录a--代码路径索引)

---

# 第一卷 · 系统架构

## 1.1 饮品配方基类（/datum/chemical_reaction/drink）

所有饮品配方继承同一基类，自带以下默认反应条件：

| 属性 | 默认值 | 说明 |
|---|---|---|
| `optimal_temp` | 250K | 最佳反应温度 |
| `temp_exponent_factor` | 1 | 温度指数因子 |
| `optimal_ph_min` / `optimal_ph_max` | 2 / 10 | 最佳 pH 范围 |
| `H_ion_release` | 0 | 不释放氢离子 |
| `rate_up_lim` | 60 | 反应速率上限 |
| `reaction_tags` | DRINK + EASY | 反应标签（酒吧菜单分类用） |

**反应标签系统**（reaction_tags）：`REACTION_TAG_DRINK`（饮品）/ `REACTION_TAG_FOOD`（食物）/ `REACTION_TAG_EASY`（简单）/ `REACTION_TAG_BRUTE`（治外伤）/ `REACTION_TAG_BURN`（治烧伤）/ `REACTION_TAG_TOXIN`（治毒素）/ `REACTION_TAG_OXY`（治缺氧）/ `REACTION_TAG_OTHER`（其他）。调酒机与化学大师界面用这些标签给玩家分类。

## 1.2 酒精核心机制（Ethanol 基类）

所有酒精试剂继承 `/datum/reagent/consumable/ethanol`，核心变量：

| 变量 | 作用 |
|---|---|
| `boozepwr` | **酒精强度**。越高越容易醉：`喝醉量 += 1 × booze_power × ALCOHOL_RATE × 代谢率` |
| `drink_size` | 单次摄入量 |
| `quality` | 酒品质量（影响 mood 加成） |

**醉酒状态（Drunk）**：
- 醉酒值随 boozepwr × 体积增长
- 高醉酒：眩晕（`set_dizzy_if_lower`）、口齿不清（slurring）、行动变慢
- 极端醉酒：呕吐、失禁、摔倒
- **清醒剂**：`Haloperidol`（清毒品）、`Antihol`（清乙醇）可解酒

## 1.3 酿造机制（发酵）

蒸馏酒（伏特加/威士忌/朗姆等）不是从配方直接"混合"出来的，而是**发酵反应**：原料 + **酶（Enzyme）催化剂** → 酒精。

**酶 (Enzyme)**：`/datum/reagent/consumable/enzyme`，几乎所有发酵配方都需要 5u 作为催化剂。酶本身由 `Universal Enzyme` 配方生产（Egg1 + Enzyme1 → 2u 或类似）。

**发酵配方结构**：
```dm
required_reagents = list(原料 = X)          // 发酵底物
required_catalysts = list(/datum/reagent/consumable/enzyme = 5)  // 酶
results = list(/datum/reagent/consumable/ethanol/XXX = 产量)
```

## 1.4 食物反应基类（/datum/chemical_reaction/food）

食物反应默认条件：`optimal_temp = 400K`、pH 2-10、标签 `FOOD + EASY`。

**关键区别**：食物反应往往生成**实体食物物品**而非试剂——`resulting_food_path` 定义产出物，`REACTION_INSTANT` 标记即时生成，按 `created_volume` 循环生成对应数量的食物物品。产物继承输入材料的 foodtypes（食物类型位标志）。

---

# 第二卷 · 基酒与发酵

**代码**: `recipes/drinks/drinks_alcoholic.dm` L28-42, L250-252, L338-341, L428-431 等

> 基酒 = 直接发酵/蒸馏产出的单一酒精，是后续鸡尾酒的原料。

| 基酒 | 配方 | 产量 | boozepwr | 备注 |
|---|---|---|---|---|
| **啤酒 Beer** | Flour10（催化 酶5） | 10u | 25 | 最基础发酵 |
| **月光酒 Moonshine** | Nutriment5 + Sugar5（催化 酶5） | 10u | 95 | 土制烈酒 |
| **伏特加 Vodka** | Potato Juice10（催化 酶5） | 10u | 65 | 土豆蒸馏 |
| **卡鲁瓦 Kahlua** | Coffee5 + Sugar5（催化 酶5） | 5u | 45 | 咖啡利口酒 |
| **清酒 Sake** | Rice10（催化 酶5） | 10u | 70 | 米酒 |
| **蜂蜜酒 Mead** | Honey2（催化 酶5） | 2u | 30 | 蜂蜜发酵 |
| **格拉帕 Grappa** | Wine10（催化 酶10） | 10u | 60 | 葡萄酒蒸馏 |
| **私酿 Hooch** | Ethanol2 + Fuel1（催化 酶1） | 3u | 100 | 燃料级烈酒 |

**从原料直接发酵但无固定化学配方**（由植物/作物提取）：威士忌 Whiskey(75)、朗姆 Rum(60)、金酒 Gin(45)、龙舌兰 Tequila(70)、葡萄酒 Wine(35)、白兰地/干邑 Cognac(75)、苦艾酒 Absinthe(80)、苹果白兰地 Applejack(20) 等——这些由酒吧初始存货或作物加工获得。

**特殊基酒变体**：
- **淡啤酒 Light Beer**：boozepwr=5（太空欧洲人讨厌它）
- **麦芽酒 Malt Liquor**：boozepwr=35
- **陈年朗姆 Aged Rum**：boozepwr=70
- **绿啤酒 Green Beer**：啤酒10 + 绿色粉末1 → 绿啤酒10（圣帕特里克节彩蛋，含蜡笔版本 greenbeer2）

---

# 第三卷 · 经典鸡尾酒（威士忌/朗姆/伏特加系）

**代码**: `recipes/drinks/drinks_alcoholic.dm`（170 配方，本卷列全部）

> 鸡尾酒 = 基酒 + 果汁/汽水/糖浆等混合。下表按风格分组。boozepwr 为成品酒精强度。

## 3.1 金酒 (Gin) 系

| 酒 | 配方 | 产量 | boozepwr | 特殊效果 |
|---|---|---|---|---|
| **金汤力 Gin & Tonic** | Gin1 + Tonic2 | 3u | 20 | |
| **马提尼 Martini** | Gin2 + Vermouth1 | 3u | 60 | |
| **金费兹 Gin Fizz** | Gin1 + Sodawater2 + Limejuice1 | 4u | 25 | |
| **干马提尼 Driest Martini** | Nothing1 + Gin1 | 2u | 65 | 哑剧主题 |
| **金花园 Gin Garden** | Lime1 + Sugar1 + Gin3 + Cucumber3 + Sol Dry5 + Ice2 | 15u | 20 | 清凉 |
| **布兰迪·克鲁斯塔 Brandy Crusta** | Sidecar4 + Maraschino1 + Sugar1 | 6u | 30 | |
| **赌场 Casino** | Gin8 + Lemonjuice1 + Maraschino1 | 10u | 45 | **复活抗性**（rev_resilience） |
| **最后的话 Last Word** | Gin1 + Herbal Liqueur1 + Lime1 + Maraschino1 | 4u | 50 | 接触眩晕5s |
| **青草蜢 Grasshopper** | Cream5 + Creme de Menthe5 + Creme de Cacao5 | 15u | 15 | |

## 3.2 威士忌 (Whiskey) 系

| 酒 | 配方 | 产量 | boozepwr | 特殊效果 |
|---|---|---|---|---|
| **威士忌可乐 Whiskey Cola** | Whiskey1 + Space Cola2 | 3u | 40 | |
| **威士忌苏打 Whiskey Soda** | Whiskey2 + Sodawater1 | 3u | 40 | |
| **威士忌酸 Whiskey Sour** | Whiskey1 + Lemon1 + Sugar1 | 3u | — | |
| **曼哈顿 Manhattan** | Whiskey2 + Vermouth1 | 3u | 50 | |
| **曼哈顿计划 Manhattan Project** | Manhattan10 + Uranium1 | 10u | 60 | 辐射幻觉100s |
| **爱尔兰咖啡 Irish Coffee** | Irish Cream1 + Coffee1 | 2u | 30 | |
| **爱尔兰奶油 Irish Cream** | Whiskey2 + Cream1 | 3u | 35 | |
| **老式 Old Fashioned** | Whiskey25 + Sugar5 + Bitters2 | 30u | 60 | |
| **萨泽拉克 Sazerac** | Old Fashioned10 + Absinthe1 | 10u | 65 | |
| **改进威士忌 Improved Whiskey** | Sazerac6 + Maraschino1 | 7u | 65 | |
| **蓝火焰 Blue Blazer** | Whiskey4 + Water4 + Sugar1（**365K 自燃**） | 9u | 25 | 蓝色火焰特效 |
| **Nar'Sour** | Blood1 + Lemon1 + Demon's Blood1 | 1u | 10 | 自残/囤积副作用 |

## 3.3 朗姆 (Rum) 系

| 酒 | 配方 | 产量 | boozepwr | 特殊效果 |
|---|---|---|---|---|
| **朗姆可乐 Rum & Coke** | Rum1 + Space Cola2 | 3u | 30 | |
| **自由古巴 Cuba Libre** | Rum Coke3 + Lime1 | 4u | 50 | 全伤-1/慢 |
| **莫吉托 Mojito** | Rum1 + Sugar1 + Lime1 + Soda1 + Menthol1 | 5u | 20 | |
| **代基里 Daiquiri** | Rum3 + Lime1 + Sugar1 + Ice1 | 6u | 35 | |
| **核代基里 Nuclear Daiquiri** | Navy Rum4 + Herbal Liqueur3 + Lime2 + Sugar1 | 10u | 65 | |
| **热核代基里 Thermonuclear Daiquiri** | Nuclear Daiquiri10 + Uranium1 + Hydrogen9 | 10u | 80 | 幻觉100s/辐射20s |
| **凤梨可乐达 Pina Colada** | Creme de Coconut1 + Pineapple3 + Rum1 + Lime1 | 5u | 40 | |
| **Piña Olivada** | Pineapple3 + Rum1 + Olive Oil1 | 5u | 20 | 油膜阻止酒精吸收 |
| **止痛药 Painkiller** | Creme de Coconut5 + Pineapple4 + Orange1 | 10u | 20 | |
| **巴哈马妈妈 Bahama Mama** | Creme de Coconut1 + Kahlua1 + Rum2 + Pineapple1 | 5u | 35 | |
| **硬苹果酒 Hard Cider** | 苹果汁系 | — | 25 | "成年人的苹果汁" |
| **Aged 系** | — | — | 70 | 陈年朗姆 |
| **海盗鸡尾酒（蛾族）** | 见第五卷 | — | — | |

## 3.4 伏特加 (Vodka) 系

| 酒 | 配方 | 产量 | boozepwr | 特殊效果 |
|---|---|---|---|---|
| **螺丝起子 Screwdriver** | Vodka1 + Orange Juice2 | 3u | 40 | |
| **伏特加马提尼 Vodka Martini** | Vodka2 + Vermouth1 | 3u | 65 | |
| **血玛丽 Bloody Mary** | Vodka1 + Tomato2 + Lime1 | 4u | 55 | 治疗与醉酒成正比 |
| **黑俄罗斯 Black Russian** | Vodka3 + Kahlua2 | 5u | 60 | |
| **白俄罗斯 White Russian** | Black Russian5 + Cream3 | 8u | 50 | |
| **伏特加汤力 Vodka Tonic** | Vodka1 + Tonic2 | 3u | 40 | |
| **莫斯科骡子 Moscow Mule** | Sol Dry5 + Vodka5 + Lime1 + Ice1 | 10u | 30 | 气泡音效 |
| **十三狼 Thirteen Loko** | Vodka1 + Coffee1 + Lime1 | 3u | 80 | 咖啡因+酒精混合；过量失明 |
| **防冻剂 Anti-freeze** | Vodka2 + Cream1 + Ice1 | 4u | 35 | 加热20/tick |
| **斯比特 Sbiten** | Vodka10 + Capsaicin1 | 10u | 70 | 加热50/tick（辣伏特加） |
| **盟友鸡尾酒 Allies Cocktail** | Martini1 + Vodka1 | 2u | 50 | |
| **神风 Kamikaze** | Vodka1 + Triple Sec1 + Lime1 | 3u | 35 | |
| **变色龙之刺 Changeling Sting** | Screwdriver1 + Lemon Lime2 | 5u | 50 | 灼烧感 |

## 3.5 龙舌兰 (Tequila) 系

| 酒 | 配方 | 产量 | boozepwr | 特殊效果 |
|---|---|---|---|---|
| **玛格丽特 Margarita** | Tequila2 + Lime1 + Triple Sec1 | 4u | 35 | |
| **龙舌兰日出 Tequila Sunrise** | Tequila2 + Orange2 + Grenadine1 | 5u | 45 | |
| **勇者公牛 Brave Bull** | Tequila2 + Kahlua1 | 3u | 60 | |
| **Patron** | Tequila10 + Silver1 | 10u | 60 | 银杯 |
| **尤雅基塔 Yūyakita** | Tequila2 + Lime1 + Yūyake1 | 4u | 40 | 日式 |

## 3.6 咖啡/奶油/利口酒系

| 酒 | 配方 | 产量 | boozepwr | 特殊效果 |
|---|---|---|---|---|
| **B-52** | Irish Cream1 + Kahlua1 + Cognac1 | 3u | 85 | |
| **原子弹 Atomic Bomb** | B52-10 + Uranium1 | 10u | 0* | *自定义醉酒：眩晕100s+幻觉20s |
| **亚历山大 Alexander** | Cognac1 + Creme de Cacao1 + Cream1 | 3u | 50 | |
| **阿玛雷托亚历山大 Amaretto Alexander** | Amaretto1 + Creme de Cacao1 + Cream1 | 3u | 35 | |
| **佩珀敏特·帕蒂 Peppermint Patty** | Hot Coco6 + Cacao1 + Menthe1 + Vodka1 + Menthol1 | 10u | 25 | 咽喉舒缓 |
| **蛋酒 Eggnog** | Rum5 + Cream5 + Eggyolk2 | 15u | 1 | 节日特饮 |

## 3.7 高难度/危险特调

| 酒 | 配方 | 产量 | boozepwr | 特殊效果 |
|---|---|---|---|---|
| **全银河漱口剂 Gargle Blaster** | Vodka1+Gin1+Whiskey1+Cognac1+Lemon1 | 5u | 0* | *自定义：眩晕3s×3+110s醉酒 |
| **神经毒素 Neurotoxin** | Gargle Blaster1 + Morphine1 | 2u | 50 | **昏迷50s+脑伤**（危险！） |
| **嬉皮士喜悦 Hippie's Delight** | Mushroom Hallucinogen1 + Gargle Blaster1 | 2u | 0* | *自定义：幻觉150s+眩晕 |
| **辛迪加炸弹 Syndicate Bomb** | Beer1 + Whiskey Cola1 | 2u | 90 | "尝起来像恐怖主义" |
| **Bacchus 祝福 Bacchus' Blessing** | Hooch1+Absinthe1+Manly Dorf1+Syndicate Bomb1 | 4u | — | 恶心泡沫 |
| **心之拳 Hearty Punch** | Brave Bull5+Syndicate Bomb5+Absinthe5（**315K 热**） | **1u** | 90 | 全伤-3.75/-5（超级疗愈酒，量极少） |
| **三重岛冰茶 Three Mile Island** | Long Island10 + Uranium1 | 10u | 10 | 幻觉100s |
| **曼哈顿计划** | 见威士忌系 | — | 60 | 辐射 |
| **恶魔之血 Demon's Blood** | Rum1+Space Wind1+Blood1+Dr.Gibb1 | 4u | 75 | |
| **魔鬼之吻 Devil's Kiss** | Blood1+Kahlua1+Rum1 | 3u | 70 | |
| **Nar'Sour** | Blood1+Lemon1+Demon's Blood1 | 1u | 10 | 自残 |
| **酸吐 Acid Spit** | Acid1 + Wine5（pH≤2 酸性反应） | 6u | 70 | 酸+酒 |
| **毒药特调 Toxins Special** | Rum2+Vermouth1+Plasma2 | 5u | 25 | **加热15/tick（等离子燃烧）** |
| **量子斯宾格 Singulo** | Vodka5+Liquid Dark Matter1+Wine5 | 10u | 35 | 蓝空间酒 |
| **女王的赎金 King's Ransom** | Rice Beer5+Gin2+Berry2+Bitters1 | 10u | 26 | |
| **心灵橡皮擦 Hivemind Eraser** | Black Russian2+Thirteen Loko1+Grenadine1 | 4u | 40 | |
| **狂欢者之选 Booger** | Cream1+Banana1+Rum1+Watermelon1 | 4u | 45 | |

## 3.8 日式酒系（酒藏）

| 酒 | 配方 | 产量 | boozepwr |
|---|---|---|---|
| **烧酒 Shochu** | 米类发酵 | — | 45 |
| **夕烧 Yūyake** | 日式利口酒 | — | 40 |
| **柚夜塔 Yūyakita** | Tequila2+Lime1+Yūyake1 | 4u | 40 |
| **赛马三 Banzai-Tī** | Yūyake1+Triple Sec1+Gin1+Rum1+Tequila1+Vodka1+Triple Citrus2+Soda2 | 10u | 40 |
| **赛巴山 Saibāsan** | Shochu2+Yūyake2+Triple Citrus3+Cherry Jelly3 | 10u | 20 |
| **三雷祖苏打 Sanraizusōda** | Yūyake1+Soda2+Ice1+Cream1 | 5u | 6 |
| **熊町 Kumichō** | Godfather2+Shochu1+Bitters1 | 4u | 62 |
| **红色星球 Red Planet** | Shochu2+Triple Sec2+Vermouth2+Grenadine1+Bitters1 | 8u | 45 |
| **天照 Amaterasu** | Shochu1+Vodka1+Grenadine1+Berry2+Soda5 | 10u | 54 |
| **猫耳含羞草 Nekomimosa** | Yūyake2+Watermelon2+Champagne1 | 5u | 17 |
| **战队昆茶 Sentai Quencha** | Shochu1+Curacao1+Triple Citrus1+Melon Soda2 | 5u | — |
| **暴走族 Bōsōzoku** | Rice Beer1+Lemonade1 | 2u | 6 |
| **替代者 Ersatzche** | Rice Beer5+Pineapple3+Capsaicin1+Sugar1 | 10u | 6 |
| **红色城市AM Red City AM** | Rice Beer5+Lime1+Red Bay1+Soy Sauce1+Tomato2 | 10u | 5 |
| **四比特 Four Bit** | Rum2+Hakka Mate2+Lime1 | 5u | 26 |
| **米酒 Rice Beer** | 发酵 | — | 5 |

## 3.9 白/甜/汽酒系

| 酒 | 配方 | 产量 | boozepwr | 特殊效果 |
|---|---|---|---|---|
| **香槟 Champagne** | 起泡酒（酒吧存货） | — | — | |
| **水果酒 Fruit Wine** | 水果发酵 | — | 35 | |
| **梅酒 Plum Wine** | 梅子发酵 | — | 20 | |
| **苦艾 Absinthe** | 草药蒸馏 | — | 80 | 幻觉8s/周期 |
| **费内特 Fernet** | 苦味利口酒 | — | 80 | 毒+1但清宿醉-5 |
| **费内特可乐 Fernet Cola** | Fernet1 + Space Cola1 | 2u | 25 | |
| **芬奇乌利 Fanciulli** | Manhattan1 + Fernet1 | 2u | -10 | **清醒剂**（负酒劲） |
| **布兰卡门塔 Branca Menta** | Fernet1 + Creme de Menthe1 + Ice1 | 3u | 35 | 降温20 |
| **Wizz Fizz** | Triple Sec1+Soda1+Champagne1 | 3u | 50 | 全伤-1 |
| **Bug Spray** | Triple Sec2+Lemon Lime1+Rum2+Vodka1 | 5u | 45 | 毒+1 |
| **苹果杰克 Jack Rose** | Grenadine1+Applejack2+Lime1 | 4u | 15 | |
| **涡轮 Turbo** | Moonshine2+N₂O1+Sugar Rush1+Pwr Game1 | 5u | 85 | 笑气+烈酒 |
| **老计时器 Old Timer** | Whiskey Soda3+Parsnip2+Alexander1 | 6u | 35 | 变灰白长胡子 |
| **橡胶颈 Rubberneck** | Ethanol4+Grey Bull5+Astrotame1 | 10u | 60 | |
| **双联 Duplex** | Hard Cider2+Apple1+Berry1 | 4u | 25 | |
| **特拉普啤酒 Trappist** | Ale2+Holy Water2+Sugar1 | 5u | 40 | 圣水啤酒 |
| **布拉赞 Blazaam** | Gin2+Peach1+Bluespace1 | 3u | 70 | 蓝空间随机 |
| **星球破碎者 Planet Cracker** | Champagne10+Lizard Wine10+Eggyolk2+Gold5 | 20u | 50 | 纳米金 |
| **莫纳罗亚 Mauna Loa** | Capsaicin2+Kahlua1+Bahama Mama2 | 5u | 40 | 加热25 |
| **教父 Godfather** | Amaretto1+Whiskey1 | 2u | 50 | |
| **教母 Godmother** | Amaretto1+Vodka1 | 2u | 50 | |
| **姜汁阿玛雷托 Ginger Amaretto** | Amaretto1+Sol Dry1+Ice1+Lemon1 | 4u | 30 | |
| **果汁 The Juice** | Mushroom Tea1+Bluespace1+Mindbreaker1+Neurotoxin1+Morphine1 | 5u | 50 | 深层紫，致幻 |
| **太阳花 Helianthus** | Absinthe1+Sugar1+Mindbreaker1 | 5u | 75 | 幻觉4s |
| **帽子 The Hat** | Ethanol1+Water1+Plum Wine1 | 1u | 80 | 香水味 |
| **电报杆 Telepole** | Wine Voltaic1+Dark and Stormy2+Sake1 | 5u | 50 | 触电放电 |
| **特斯拉荚 Pod Tesla** | Telepole5+Brave Bull3+Admiralty5 | 15u | 80 | 强放电+闪电音效 |
| **白酒 White Tiziran** | Black Russian5+Kortara3 | 8u | 50 | 蜥蜴族 |

## 3.10 含蛋/奶泡特调

| 酒 | 配方 | 产量 | boozepwr | 特殊效果 |
|---|---|---|---|---|
| **波士顿酸 Boston Sour** | Whiskey Sour15+Eggwhite2+Bitters1 | 15u | 35 | 泡沫头 |
| **阿玛雷托酸 Amaretto Sour** | Amaretto10+Lemon5+Eggwhite2 | 15u | 15 | 泡沫头 |
| **拉莫斯金费兹 Ramos Gin Fizz** | Ginfizz12+Lemon3+Sugar3+Eggwhite3+Cream3+Triple Sec1 | 25u | 35 | 泡沫溢出 |
| **翻杯 Flip Cocktail** | Cognac15+Sugar5+Eggwhite4+Eggyolk2 | 25u | 30 | 蛋乳化 |
| **萨菲林·巴斯达 Suffering Bastard** | Sol Dry10+Cognac3+Gin3+Lime2+Bitters1+Sugar1 | 20u | 20 | 头痛舒缓+脑伤-0.5 |
| **热托迪 Hot Toddy** | Water5+Cognac3+Sugar1+Lemon1（**320K 热**） | 10u | 25 | 加热25（感冒药） |
| **法国75 French 75** | Champagne5+Gin3+Lemon1+Sugar1 | 10u | 35 | |
| **桑格利亚 Sangria** | Wine10+Cognac3+Triple Citrus3+Soda3+Sugar1 | 20u | 20 | |
| **西班牙凉菜酒 Gazpacho 系** | — | — | — | |

## 3.11 特殊机制酒

| 酒 | 特殊机制 |
|---|---|
| **Crevice Spike** | boozepwr=-10：**负酒劲**（越喝越清醒）+ 外伤惩罚 |
| **Fringe Weaver** | 乙醇9+Sugar1，奶油色泡沫（9:1 卡摩丁/阿德海德） |
| **Sugar Rush** | Sugar2+Lemon1+Wine1，粉红色气泡 |
| **Quadruple Sec** | Triple Sec5+Triple Citrus5+Grenadine5 → 15u；泰瑟枪音效 |
| **Quintuple Sec** | Quadruple Sec5+小丑泪5+Syndicate Bomb5 → 15u；空气喇叭音效 |
| **Pousse Cafe** | 10 种利口酒分层（按密度从下到上：Herbal/Bitters/Branca Menta/Irish Cream/Yuyake/Aperitivo/Amaretto/Curacao/Maraschino/Toechtauese） |
| **Vieux Carré** | Manhattan6+Cognac4+Vermouth2+Herbal Liqueur2+Bitters1 → 15u |
| **Bartender's Handshake** | Aperitivo1+Fernet1 → 2u；治疗+2耐力 |
| **Grand Marnier 系** | — | — | — |

## 3.12 补遗（核查增补）

| 酒 | 配方 | 产量 | boozepwr | 特殊效果 |
|---|---|---|---|---|
| **拾取费兹 Fetching Fizz** | Nuka Cola1 + Iron1 | 3u | — | 磁力震动（仅采矿站可制） |
| **爱尔兰汽车炸弹 Irish Car Bomb** | Ale1 + Irish Cream1 | 2u | 25 | 艾尔+奶油炸弹 |
| **茂宜日出 Maui Sunrise** | Coconut Rum2+Pineapple2+Yūyake1+Triple Citrus1+Lemon Lime4 | 10u | — | 夏威夷风 |
| **斯奎特苹果酒 Squirt Cider** | Salt Water2 + Tomato2 + Nutriment1 | 4u | 40 | 苹果红 |
| **蜇人 Stinger** | Cognac10 + Creme de Menthe5 | 15u | 55 | 薄荷干邑 |
| **提兹里安酸 Tizirian Sour** | Bitters3+Lemon2+Korta Nectar2+Sugar1 | 8u | 35 | 蜥蜴族酸酒 |

---

# 第四卷 · 无酒精饮料

**代码**: `recipes/drinks/drinks_non-alcoholic.dm`（43 配方）

> 软饮/果汁/奶昔/咖啡茶系。效果数值见第七卷。

## 4.1 咖啡与茶

| 饮料 | 配方 | 产量 | 效果 |
|---|---|---|---|
| **咖啡 Coffee** | Coffeepowder1 + Water5 | 5u | 清醒：减困5s/解睡2s/眩晕+12.5 |
| **茶 Tea** | Teapowder1 + Water5 | 5u | 减困2s/解晕1s |
| **冰咖啡 Iced Coffee** | Ice1 + Coffee3 | 4u | 同咖啡+降温 |
| **热冰咖啡 Hot Ice Coffee** | Hot Ice1 + Coffee2 | 3u | 同咖啡+强降温 |
| **冰茶 Iced Tea** | Ice1 + Tea3 | 4u | 同茶+治氧伤 |
| **阿诺德·帕尔默 Arnold Palmer** | Ice Tea1 + Lemonade1 | 2u | 高尔夫主题 |
| **豆奶拿铁 Soy Latte** | Coffee1 + Soy Milk1 | 2u | 咖啡+奶泡 |
| **咖啡拿铁 Cafe Latte** | Coffee1 + Milk1 | 2u | 同 |
| **南瓜拿铁 Pumpkin Latte** | Pumpkin Juice5 + Coffee5 + Cream5 | 15u | |
| **热可可 Hot Coco** | Milk5 + Coco1（**320K 热**） | 6u | 加热+治外伤0.5 |
| **意式热巧克力 Italian Coco** | Hot Coco5 + Corn Starch1 + Whipped Cream4 | 10u | 加热 |

## 4.2 果汁与汽水

| 饮料 | 配方 | 产量 | 效果 |
|---|---|---|---|
| **可乐 Cola** | 基础软饮 | — | 解困5s+降温 |
| **核子可乐 Nuka Cola** | Uranium1 + Cola6 | 6u | 亢奋20s+幻觉30s |
| **Grey Bull** | 能量饮料 | — | 亢奋20s+解睡 |
| **太空山风 SM Wind** | 汽水 | — | 解困7s+眩晕5s |
| **Dr. Gibb** | 42 种风味 | — | 解困6s |
| **太空充气 Space-Up** | 柠檬汽水 | — | 降温 |
| **柠檬青柠 Lemon Lime** | 0.5% 天然柑橘 | — | 降温 |
| **Pwr Game** | 玩家能量饮料 | — | **+5 游戏技能** |
| **Shambler's Juice** | 神秘果汁 | — | 降温 |
| **苏打水 Soda Water** | 基础 | — | 解困5s+解睡 |
| **汤力水 Tonic Water** | 奎宁 | — | 治痢疾主题 |
| **葡萄苏打 Grape Soda** | Grape Juice1 + Soda Water1 | 2u | 降温 |
| **奶油苏打 Cream Soda** | Sugar2 + Soda2 + Vanilla1 | 4u | 降温 |
| **柠檬水 Lemonade** | Lemon2 + Water2 + Sugar1 + Ice1 | 5u | 怀旧 |
| **三重柑橘 Triple Citrus** | Lemon1+Lime1+Orange1（强酸 pH≤2） | 3u | |
| **黄瓜柠檬水 Cucumber Lemonade** | Lemon Lime3 + Cucumber2 + Ice1 | 5u | |
| **水果潘趣 Fruit Punch** | 混合果汁 | — | 全伤-0.3 |
| **24V 能量 24-Volt** | 电能饮料 | — | 精灵族放电 |

## 4.3 奶昔与奶饮

| 饮料 | 配方 | 产量 | 效果 |
|---|---|---|---|
| **樱桃奶昔 Cherryshake** | Cherry Jelly1 + Ice1 + Cream1 | 3u | |
| **蓝樱桃奶昔 Blueberryshake** | Blue Cherry Jelly1 + Ice1 + Cream1 | 3u | |
| **香草奶昔 Vanillashake** | Vanilla1 + Ice1 + Cream1 | 3u | |
| **焦糖奶昔 Caramelshake** | Caramel1 + Ice1 + Cream1 | 3u | |
| **巧克力奶昔 Choccyshake** | Coco1 + Ice1 + Cream1 | 3u | |
| **草莓奶昔 Strawberryshake** | Berry Juice1 + Ice1 + Cream1 | 3u | |
| **香蕉奶昔 Bananashake** | Banana1 + Ice1 + Cream1 | 3u | |
| **巧克力牛奶 Chocolate Milk** | Hot Coco3 + Coco2（300K 冷配方） | 5u | |
| **草莓香蕉 Strawberry Banana** | Berry1 + Milk1 + Banana1 | 3u | |
| **莓果冲击 Berry Blast** | Berry1 + Milk1 + Cream1 | 3u | |
| **猴力 Funky Monkey** | Coco1 + Milk1 + Banana1 | 3u | |
| **绿巨人 Green Giant** | Creme de Menthe1 + Milk1 + Lime1 | 3u | |
| **蜜瓜球 Melon Baller** | Watermelon1 + Creme de Menthe1 + Milk1 | 3u | |
| **香草梦 Vanilla Dream** | Vanilla1 + Milk1 + Cream1 | 3u | |
| **吉布漂浮 Gibbfloats** | Dr.Gibb5 + Ice5 + Cream5 | 15u | |
| **红皇后 Red Queen** | Tea6 + Mercury2 + Blackpepper1 + Growthserum1 | 10u | 变巨人！ |
| **密西西比女王 Mississippi Queen** | Tomato15 + Mayo10 + Soy Sauce5 + Vinegar2 + Capsaicin10 + Coco2 | 50u | 眩晕+狂暴 |
| **罗伊·罗杰斯 Roy Rogers** | Cola2 + Grenadine1 | 3u | 亢奋6s |
| **雪莉·坦普尔 Shirley Temple** | Sol Dry2 + Grenadine1 | 3u | 解困 |
| **灰姑娘 Cinderella** | Pineapple10+Orange10+Lemon5+Ice5+Sol Dry20+Bitters2 | 50u | |
| **柠檬汽水柠檬汁** | 见上 | — | |

## 4.4 特色无酒精

| 饮料 | 配方 | 产量 | 效果 |
|---|---|---|---|
| **医生喜悦 Doctor's Delight** | Lime1+Tomato1+Orange1+Cream1+Cryoxadone1 | 5u | **全伤-0.25×4**（医疗饮料） |
| **薄荷糖浆 Menthol** | 薄荷提取 | — | 咽喉舒缓 |
| **石榴糖浆 Grenadine** | 石榴 | — | 亢奋5s+营养 |
| **姜汁 Sol Dry** | 姜 | — | 解困2.5s |
| **苦艾汽水 Bitters and Soda** | Soda10+Ice5+Bitters1 | 15u | 解困 |
| **柠檬水** | 见上 | — | |
| **柠檬精（Lean）** | 咳嗽糖浆汽水 | — | 嗜睡+疲惫 |
| **蘑菇茶 Mushroom Tea** | Mushroom Powder1 + Water5 | 5u | 毒-0.25 |
| **芦荟汁 Aloe Juice** | 芦荟 | — | 毒-0.5 |
| **阿瓜弗莱斯卡 Agua Fresca** | Watermelon4+Ice1+Water2+Lime2+Menthol1 | 10u | 降温+毒-0.25 |
| **T 饮料（T-Letter）** | Nothing1 + Tea1 | 2u | 哑剧治疗 |
| **红皇后** | 见上 | — | 巨化 |
| **甜味茶 Toechtauese Syrup** | Toechtauese Juice6 + Sugar4 | 10u | 蜥蜴族 |

---

# 第五卷 · 种族特色饮品（蜥蜴/蛾）

**代码**: `recipes/drinks/drinks_lizard.dm`（7 配方）+ `drinks_moth.dm`（8 配方）

## 5.1 蜥蜴族（Tiziran）饮品

| 饮品 | 配方 | 产量 | boozepwr |
|---|---|---|---|
| **蘑菇茶 Mushroom Tea** | Mushroom Powder1 + Water5 | 5u | 0 |
| **虫康布茶 Mushi Kombucha** | Mushroom Tea3 + Korta Nectar2（催化 酶1） | 5u | 10 |
| **醉蜥蜴 Espatier** | Mushi Kombucha2 + Moonshine2 + Berry1 | 5u | 65 |
| **蛋白质混合 Protein Blend** | Yuck1 + Korta Flour1 + Blood1 + Ethanol2 | 5u | 65 |
| **海风 Sea Breeze** | Kortara3 + Creme de Menthe1 + Creme de Cacao1 | 5u | 15 |
| **凯旋门 Triumphal Arch** | Mushi Kombucha5 + Grappa2 + Lemon2 + Gold1 | 10u | 60 |
| **白提兹兰 White Tiziran** | Black Russian5 + Kortara3 | 8u | 50 |

## 5.2 蛾族（Mothic）饮品

| 饮品 | 配方 | 产量 | boozepwr |
|---|---|---|---|
| **海军上将 Admiralty** | Navy Rum3 + Vermouth1 + Fernet1 | 5u | — |
| **黑暗风暴 Dark & Stormy** | Rum3 + Sol Dry7 | 10u | — |
| **长途跋涉 Long Haul** | Navy Rum4+Curacao3+Sugar2+Bitters1+Soda15 | 25u | — |
| **长约翰银 Long John Silver** | Navy Rum4 + Bitters1 + Lemonade5 | 10u | — |
| **盐与浪 Salt and Swell** | Navy Rum4+Toechtauese Syrup3+Eggwhite2+Salglu1 | 10u | — |
| **提尔塔伦 Tiltaellen** | Yoghurt8 + Vinegar1 + Salt1 | 10u | — |
| **提克托赫 Tich Toch** | Tiltaellen6 + Toechtauese Syrup2 + Vodka2 | 10u | 75 |
| **热带风暴 Tropical Storm** | Rum2+Curacao2+Triple Citrus4+Pineapple2 | 10u | — |

---

# 第六卷 · 食物混合与调味品

**代码**: `recipes/food_mixtures.dm`（48 配方）

> 生成实体食物的化学反应。注：`resulting_food_path` 表示反应生成物品而非试剂。

## 6.1 基础食材生成

| 食物 | 配方 | 生成物 |
|---|---|---|
| **豆腐 Tofu** | Soy Milk10（催化 酶5） | 豆腐块 |
| **糖果玉米 Candy Corn** | Oil5（催化 Sugar5） | 糖果玉米 |
| **巧克力棒 Chocolate Bar** | Soy Milk2+Coco2+Sugar2 | 巧克力棒（×3 路径） |
| **芝士轮 Cheesewheel** | Milk40（催化 酶5） | 芝士轮 |
| **合成肉 Synthmeat** | Blood5 + Cryoxadone1 | 合成肉排 |
| **仿鲤鱼排 Imitation Carpmeat** | Carpotoxin5（容器: 豆腐） | 仿鲤鱼排 |
| **面团 Dough** | Water10 + Flour15 | 面团 |
| **米面团 Rice Dough** | Rice Flour20 + Water10 | 米面团 |
| **蛋糕糊 Cakebatter** | Eggyolk6+Eggwhite12+Flour15+Sugar5 | 蛋糕糊 |
| **素蛋糕糊 Cakebatter (vegan)** | Soy Milk15+Flour15+Sugar5 | 素食蛋糕糊 |
| **生米 Uncooked Rice** | Rice10 + Water10 | 生米 |
| **马提安面糊 Martian Batter** | Flour5 + Dashi5 | 马提安面糊 |
| **摩西比萨面团 Mothic Pizza Dough** | Milk5+Olive Oil2+Salglu5+Cornmeal10+Flour5 | 摩西比萨面团 |
| **凝乳芝士 Curd Cheese** | Milk15+Vinegar5+Cream5（353K 热） | 凝乳芝士 |
| **马苏里拉 Mozzarella** | Milk10+Cream10（催化 酶1，353K 热） | 马苏里拉 |
| **玉米面包 Cornbread** | Cornmeal Batter25（473K 烘焙） | 玉米面包 |
| **燕麦奶昔系** | — | — |

## 6.2 调味品与酱料

| 酱料 | 配方 | 产量 | 说明 |
|---|---|---|---|
| **酱油 Soy Sauce** | Soy Milk4 + Acid1 | 5u | |
| **玉米糖浆 Corn Syrup** | Corn Starch1 + Acid1（374K） | 5u | |
| **米面粉 Rice Flour** | Flour5 + Rice5 | 10u | |
| **焦糖 Caramel** | Sugar1（413K） | 1u | 糖→焦糖 |
| **烧焦碳 Burnt Caramel** | Caramel1（483K） | 碳1 | 烤糊 |
| **烧烤酱 BBQ Sauce** | Ash1+Tomato1+Salglu3+Blackpepper1 | 5u | |
| **肉汁 Gravy** | Milk1+Nutriment1+Flour1 | 3u | |
| **酸奶 Yoghurt** | Cream10 + Virus Food2 | 10u | 病毒食物发酵 |
| **橄榄油 Olive Oil** | Olive Paste4 + Water1 | 2u | |
| **橄榄油升档** | Olive Oil1（催化）+ Oil2 | 橄榄油2 | 普通油→橄榄油 |
| **葡萄酒醋 Wine Vinegar** | Wine1 + Water1 + Sugar1 | 醋5 | |
| **葡萄醋 Grape Vinegar** | Grape Juice5（催化 酶5） | 醋5 | |
| **马提安面糊** | 见上 | — | |

## 6.3 汤/饮品反应

| 反应 | 配方 | 结果 | 说明 |
|---|---|---|---|
| **热拉面 Hot Ramen** | Water1 + Dry Ramen3 | 热拉面3u | |
| **地狱拉面 Hell Ramen** | Capsaicin1 + Hot Ramen6 | 地狱拉面6u | 辣 |
| **巧克力布丁** | Cream5+Coco5+Eggyolk2 | 20u | 凌晨彩蛋（4:00 触发"人生失控"mood） |
| **香草布丁** | Vanilla5+Cream5+Eggyolk2 | 20u | |
| **煎饼糊 Pancake Batter** | Eggyolk6+Eggwhite12+Milk10+Flour5 | 15u | |
| **玉米糊 Cornmeal Batter** | Cornmeal20+Yoghurt10+Eggyolk5 | 35u | |
| **营养转化** | Nutriment0.5（催化 Metafactor0.5） | 肽0.5 | 食物→肽 |
| **蛋白转化** | Protein0.5（催化 Metafactor0.5） | 肽0.5 | |
| **失败转化** | Nutriment+Probital Failed | 失败肽 | thermic+100 信号 |
| **接地中和** | Enriched Liquid Electricity2 + Grounding Solution1 | 盐2 | 电击中和 |

## 6.4 特殊反应

| 反应 | 机制 |
|---|---|
| **孢子解毒 Spore Detoxification** | Spore1 + Eggwhite0.5（350K）→ 维生素1 |
| **橄榄油升档** | 见上 |
| **摩西芝士/面食** | 见上 |

---

# 第七卷 · 试剂效用速查（饮料/酒精效果数值）

**代码**: `reagents/drinks/drink_reagents.dm`（1,501行）+ `reagents/drinks/alcohol_reagents.dm`（356 定义）

## 7.1 果汁与奶类（有实际效果的饮料试剂）

| 试剂 | 每tick效果 |
|---|---|
| **橙汁 Orange Juice** | 16%概率氧伤-0.5 |
| **番茄汁 Tomato Juice** | 10%概率烧伤-0.5 |
| **青柠汁 Lime Juice** | 10%概率毒-0.5 |
| **胡萝卜汁 Carrot Juice** | 眼模糊-1s+临时失明-1s；周期21+眼伤-1；周期110+持续治眼 |
| **毒莓汁 Poison Berry Juice** | 毒+0.5 |
| **香蕉汁 Banana Juice** | 喜剧代谢/猿类：外伤+烧伤各-0.5 |
| **泡菜汁 Pickle Juice** | 恶心-0.5 |
| **牛奶 Milk** | 外伤-0.5（无烧伤） |
| **豆浆 Soy Milk** | 外伤-1 |
| **奶油 Cream** | 外伤-1 |
| **Nothing（空）** | 哑剧者：沉默+外伤/烧伤-0.5 |
| **大笑 Laughter** | 强制大笑+快乐mood（代谢∞即时） |
| **超大笑 Super Laughter** | 大笑+16%概率昏迷5s |
| **冰 Ice** | 体温-2.5 |

## 7.2 咖啡/茶/能量饮料效果

| 试剂 | 每tick效果 | 过量 |
|---|---|---|
| **咖啡 Coffee** | 减困5s+解睡2s+眩晕12.5+体温+12.5 | 抖动5s |
| **茶 Tea** | 减困2s+解晕1s+眩晕-3s+解睡1s | |
| **冰咖啡 Iced Coffee** | 减困5s+解睡2s+降温2.5 | 抖动 |
| **热冰咖啡 Hot Ice Coffee** | 减困5s+解睡3s+强降温3.5 | |
| **冰茶 Iced Tea** | 减困2s+治氧伤0.5+解睡2s | |
| **可乐 Cola** | 减困5s+降温2.5 | |
| **核子可乐 Nuka Cola** | 亢奋20s+幻觉30s+眩晕1.5s+解睡2s | |
| **Root Beer** | 眩晕1s+抖动0.5+恶心0.75 | |
| **Grey Bull** | 亢奋20s+眩晕1s+解睡2s+降温 | |
| **SM Wind** | 减困7s+解睡1s+眩晕5s+降温 | |
| **Dr. Gibb** | 减困6s+降温 | |
| **Space-Up** | 降温4 | |
| **Lemon Lime** | 降温4 | |
| **Pwr Game** | 降温4+**游戏技能+5** | |
| **Monkey Energy** | 亢奋40s+眩晕1s+解睡2s（猴族能量） | |
| **Soy Latte** | 减困5s+解睡3s+加热2.5 | 抖动 |
| **Cafe Latte** | 减困5s+眩晕-6s+加热2.5 | |
| **Pumpkin Latte** | 减困5s+解睡3s+加热2.5 | |

## 7.3 治疗性/特殊饮料效果

| 试剂 | 每tick效果 |
|---|---|
| **医生喜悦 Doctor's Delight** | 外伤/烧伤/毒/氧各-0.25 |
| **灰姑娘 Cinderella** | 毒-2.5 |
| **热可可 Hot Coco** | 加热2.5+外伤-0.5 |
| **意式热巧克力** | 加热2.5 |
| **薄荷 Menthol** | 咽喉舒缓（throat_soothed） |
| **石榴糖浆 Grenadine** | 亢奋5s+营养12 |
| **Sol Dry（姜汁）** | 减困2.5s |
| **雪莉·坦普尔** | 减困1.5s |
| **芦荟汁 Aloe Juice** | 毒-0.5 |
| **阿瓜弗莱斯卡** | 降温4+毒-0.25 |
| **蘑菇茶** | 毒-0.25 |
| **黄瓜柠檬水** | 降温4+毒-0.25 |
| **密西西比女王** | 眩晕2s+恶心2s+亢奋30s（猛女饮） |
| **T 饮料** | 哑剧沉默+解睡2s+氧-1 |
| **24V 能量** | 接触放电 vol×20×精灵放电率 |
| **水果潘趣 Fruit Punch** | 毒/氧/外伤各-0.3+烧伤0.75 |
| **Lean（紫水）** | 眩晕2.5s+困2.25s+颤抖2s+幻觉15s（含可待因） |
| **苦艾汽水 Bitters Soda** | 减困2.5s |
| **红皇后 Red Queen** | **生长激素**（巨人化，周期逻辑） |
| **阿诺德·帕尔默** | 概率治伤（周期逻辑） |

## 7.4 酒精效果核心数值（boozepwr 全表）

> 醉酒公式：`喝醉量 += booze_power × ALCOHOL_RATE × 代谢率 × 秒`。boozepwr 越高越易醉。标 * 为自定义醉酒（无视酒劲）。

| 酒 | boozepwr | 酒 | boozepwr |
|---|---|---|---|
| Grog 格罗格 | 1 | Eggnog 蛋酒 | 1 |
| Dreadnog | 1 | Light Beer 淡啤 | 5 |
| Rice Beer 米酒 | 5 | Red City AM | 5 |
| Sanraizusōda | 6 | Ersatzche | 6 |
| Bōsōzoku 暴走族 | 6 | Three Mile Island | 10 |
| Nekomimosa 猫耳含羞草 | 17 | Amaretto Sour | 15 |
| Mushi Kombucha | 10 | Jack Rose | 15 |
| Sea Breeze 海风 | 15 | Iced Beer 冰啤 | 15 |
| Bilk 啤酒牛奶 | 15 | Grasshopper 青草蜢 | 15 |
| Gin Fizz | 25 | Mojito 莫吉托 | 20 |
| Gin & Tonic 金汤力 | 20 | Gin Garden 金花园 | 20 |
| Piña Olivada | 20 | Cream de Coconut | 20 |
| Applejack 苹果白兰地 | 20 | Creme de Menthe/Cacao | 20 |
| Painkiller 止痛药 | 20 | Blank Paper | 20 |
| Sangria 桑格利亚 | 20 | Suffering Bastard | 20 |
| Plum Wine 梅酒 | 20 | Hivemind Eraser | 40 |
| Red Planet 红星球 | 45 | Bug Spray | 45 |
| Jack Rose→ | 15 | Curaçao 蓝橙 | 30 |
| Kahlua 卡鲁瓦 | 45 | Gin & Tonic→ | 20 |
| Whiskey Cola | 40 | Screwdriver 螺丝刀 | 40 |
| Whiskey Soda | 40 | Vodka Tonic | 40 |
| Black Russian | 60 | White Russian | 50 |
| Manhattan | 50 | Rum & Coke | 30 |
| Cuba Libre | 50 | Margarita | 35 |
| Tequila Sunrise | 45 | Irish Cream | 35 |
| Irish Coffee | 30 | Brave Bull | 60 |
| Snow White | 20 | Amasec | 45 |
| Changeling Sting | 50 | Aloe | 30 |
| Andalusia | 45 | Devil's Kiss | 70 |
| Demon's Blood | 75 | Erika Surprise | 35 |
| Driest Martini | 65 | Banana Honk | 60 |
| Silencer | 59 | Drunken Blumpkin | 30 |
| Hard Cider | 25 | Singulo | 35 |
| Sbiten 斯比特 | 70 | Red Mead | 31 |
| Mead 蜂蜜酒 | 30 | Anti-freeze 防冻剂 | 35 |
| Barefoot | 30 | Gargle Blaster* | 0* |
| Neurotoxin* | 50* | Hippie's Delight* | 0* |
| Atomic Bomb* | 0* | Hearty Punch | 90 |
| Syndicate Bomb | 90 | Hooch 私酿 | 100 |
| Manly Dorf | 100 | Moonshine 月光酒 | 95 |
| B-52 | 85 | Absinthe 苦艾 | 80 |
| Thirteen Loko | 80 | Cognac 干邑 | 75 |
| Whiskey 威士忌 | 75 | Ale 艾尔 | 65 |
| Vodka 伏特加 | 65 | Rum 朗姆 | 60 |
| Aged Rum 陈朗姆 | 70 | Tequila 龙舌兰 | 70 |
| Gin 金酒 | 45 | Vermouth 味美思 | 45 |
| Wine 葡萄酒 | 35 | Lizard Wine 蜥蜴酒 | 45 |
| Grappa 格拉帕 | 60 | Amaretto 阿玛雷托 | 25 |
| Patron | 60 | Goldschlager | 25 |
| Sake 清酒 | 70 | Trappist | 40 |
| Fernet 费内特 | 80 | Fanciulli | **-10**（清醒） |
| Crevice Spike | **-10**（清醒） | Fringe Weaver | 90 |
| Turbo 涡轮 | 85 | Blazaam | 70 |
| Planet Cracker | 50 | Mauna Loa | 40 |
| The Hat | 80 | Helianthus | 75 |
| Tich Toch | 75 | White Tiziran | 50 |
| Drunken Espatier | 65 | Protein Blend | 65 |
| Triumphal Arch | 60 | Ritual Wine 仪式酒 | 90 |
| The Juice | 50 | Pod Tesla | 80 |
| Telepole | 50 | Kumichō | 62 |
| Amaterasu | 54 | Godfather/Godmother | 50 |
| Ginger Amaretto | 30 | Old Fashioned | 60 |
| Sazerac | 65 | Improved Whiskey | 65 |
| Boston Sour | 35 | Star | 40 |
| Daiquiri | 35 | Flip Cocktail | 30 |
| Aperitivo | 40 | Herbal Liqueur | 75 |
| Maraschino | 50 | Bartender's Handshake | 50 |
| Brandy Crusta | 30 | Casino | 45 |
| Jungle Bird | 25 | Last Word | 50 |
| Mary Pickford | 35 | Nuclear Daiquiri | 65 |
| Thermonuclear Daiquiri | 80 | Poet's Dream | 50 |
| Pousse Cafe | 50 | Vieux Carré | 60 |
| Mausi Sunrise | — | Boston Sour | 35 |
| White Hawaiian | — | Imperial Mai Tai | — |
| Konococo Rumtini | — | Blue Hawaiian | — |

## 7.5 酒精特殊效果数值（自定义醉酒/附加效果）

| 酒 | 附加效果 |
|---|---|
| **Kahlua** | 亢奋10s+减困6s+解睡4s |
| **Whiskey Candycorn** | 眩晕4s |
| **Thirteen Loko** | 减困14s+解睡4s+加热5+亢奋10s；过量：幻觉20s+毒15+眼伤+失明700s |
| **Bilk** | 外伤-1（啤酒牛奶疗伤） |
| **Three Mile Island** | 幻觉100s（辐射） |
| **Absinthe** | 幻觉8s |
| **Cuba Libre** | 全伤-1×4 |
| **Bloody Mary** | 治疗与醉酒成正比（越醉越补） |
| **Toxins Special** | 加热15（等离子） |
| **Beepsky Smash** | 亢奋4s+外伤-4 |
| **Manly Dorf** | 外伤-2+烧伤-2 |
| **Screwdriver** | 毒-2 |
| **Manhattan Project** | 幻觉60s（辐射） |
| **Anti-freeze** | 加热20 |
| **Barefoot** | 外伤-3 |
| **Sbiten** | 加热50（辣） |
| **Iced Beer** | 降温20 |
| **Changeling Sting** | 灼烧 |
| **Banana Honk** | 治疗（heal 变量） |
| **Silencer** | 哑剧沉默+治疗 |
| **Hearty Punch** | 全伤-3.75×3+毒-5（神药） |
| **Atomic Bomb** | 眩晕100s+幻觉20s+醉酒20s |
| **Gargle Blaster** | 眩晕3s×3+110s醉酒 |
| **Neurotoxin** | 昏迷50s+眩晕2s+脑伤0.5+耐力5 |
| **Hippie's Delight** | 幻觉150s+眩晕 |
| **Nar'Sour** | 恶心6s+自残 |
| **Quadruple Sec** | 治疗（law 主题） |
| **Quintuple Sec** | 治疗2+外伤-5 |
| **Bastion Bourbon** | 全伤-0.25~-0.75（温和治疗） |
| **Between the Sheets** | 嗜睡+全伤-0.25 |
| **Fernet** | 毒+1+清宿醉-5 |
| **Fernet Cola** | 毒+0.5+解困3 |
| **Fanciulli** | 解困5s（清醒酒） |
| **Branca Menta** | 降温20 |
| **Wizz Fizz** | 全伤-1×4（魔法气泡） |
| **Bug Spray** | 毒+1 |
| **Turbo** | 醉酒-0.5×drunk（笑气中和） |
| **Old Timer** | 变灰白+长胡子 |
| **Trappist** | 外伤-2.5+解困2s |
| **Mauna Loa** | 加热25 |
| **Piña Olivada** | 油膜阻止酒精吸收 |
| **Kortara** | 外伤-1（蜥蜴疗伤） |
| **Protein Blend** | 毒+2+营养5+耐力2 |
| **Helianthus** | 幻觉4s（48s上限） |
| **Gin Garden** | 降温5 |
| **Voltaic Yellow Wine** | 接触放电×10 |
| **Telepole** | 接触放电×20 |
| **Pod Tesla** | 接触放电×30 |
| **Suffering Bastard** | 头痛舒缓+脑伤-0.5 |
| **Blue Blazer** | 加热25（点火） |
| **Hot Toddy** | 加热25（感冒药） |
| **Aperitivo** | 睡眠-1×REM |
| **Bartender's Handshake** | 全伤-1+耐力2 |
| **Casino** | 复活抗性 |
| **Last Word** | 接触眩晕5s |
| **Thermonuclear Daiquiri** | 幻觉100s+眩晕20s+抖动10s |

---

# 第八卷 · 实体食物桌台合成（crafting_recipe）

**代码**: `code/modules/food_and_drinks/recipes/tablecraft/`（19 文件，571 配方）

> 这些是玩家在料理台/炉灶用食物物品合成的成品菜。材料列的 任意食物 表示配方接受任意食物物品（foodtypes 匹配）。
> 注：另有 recipes_guide.dm（173 条）是**非合成指引**（如用布包玻璃碎片做匕首），不属于食物合成，未纳入。


## 8.1 面包类（23 种）

| 食物 | 材料需求 | 成品 |
|---|---|---|
| **Meat bread** | breadx1, cutletx3, cheese wedgex3 | meatbread loaf |
| **Xenomeat bread** | breadx1, xeno cutletx3, cheese wedgex3 | xenomeatbread loaf |
| **Spidermeat bread** | breadx1, spider cutletx3, cheese wedgex3 | spider meat loaf |
| **Sausage bread** | breadx1, sausagex2 | sausagebread loaf |
| **Banana nut bread** | milkx5, breadx1, boiled eggx3, 任意食物x1 | banana-nut bread |
| **Tofu bread** | breadx1, tofux3, cheese wedgex3 | tofubread |
| **Cream cheese bread** | milkx5, breadx1, cheese wedgex2 | cream cheese bread |
| **Mimana bread** | soymilkx5, breadx1, tofux3, 任意食物x1 | mimana bread |
| **Garlic Bread** | 任意食物x1, bread slicex1, butter slicex1 | garlic bread |
| **Butter Biscuit** | bunx1, butter slicex1 | butter biscuit |
| **Butterdog** | bunx1, stick of butterx1 | butterdog |
| **Baguette** | saltx1, blackpepperx1, dough slicex2 | baguette |
| **Raw breadstick** | dough slicex1, saltx1, butter slicex1 | raw breadstick |
| **Raw croissant** | dough slicex1, sugarx1, butter slicex1 | raw croissant |
| **Throwing croissant** | 牛角包x1, rodsx1 | croissant |
| **Living dog/bread hybrid** | brainx1, heartx1, breadx2, meatx3, 血液x30, tesliumx1 | breaddog |
| **Slime toast** | slimejellyx5, bread slicex1 | jellied toast |
| **Jellied toast** | cherryjellyx5, bread slicex1 | jellied toast |
| **Buttered Toast** | bread slicex1, butter slicex1 | buttered toast |
| **Two bread** | winex5, bread slicex2 | two bread |
| **Moldy Bread** | bread slicex1, 任意食物x1 | moldy 'bread' slice |
| **Bread cat/bread hybrid** | breadx1, catx1, catx1, meatx3, 血液x50, strange reagentx5 | breadcat |
| **Raw french toast** | bread slicex1, eggx2, milkx5 | raw french toast |

## 8.2 汉堡类（46 种）

| 食物 | 材料需求 | 成品 |
|---|---|---|
| **Human burger** | bunx1, strange pattyx1 | human burger |
| **Plain Burger** | pattyx1, bunx1 | plain burger |
| **Corgi burger** | corgi pattyx1, bunx1 | corgi burger |
| **Appendix burger** | appendixx1, bunx1 | appendix burger |
| **Brain burger** | brainx1, bunx1 | brainburger |
| **Xeno burger** | xenomorph pattyx1, bunx1 | xenoburger |
| **Bearger** | bear pattyx1, bunx1 | bearger |
| **Fish burger** | fish filletx1, bunx1, cheese wedgex1 | fillet -o- carp sandwich |
| **Tofu burger** | tofux1, bunx1 | tofu burger |
| **Ghost burger** | ectoplasmx1, saltx2, bunx1 | ghost burger |
| **Clown burger** | clown hatx1, bunx1 | clown burger |
| **Mime burger** | mimex1, bunx1 | mime burger |
| **Red burger** | pattyx1, redx1, bunx1 | red burger |
| **Orange burger** | pattyx1, orangex1, bunx1 | orange burger |
| **Yellow burger** | pattyx1, yellowx1, bunx1 | yellow burger |
| **Green burger** | pattyx1, greenx1, bunx1 | green burger |
| **Blue burger** | pattyx1, bluex1, bunx1 | blue burger |
| **Purple burger** | pattyx1, purplex1, bunx1 | purple burger |
| **Black burger** | pattyx1, blackx1, bunx1 | black burger |
| **White burger** | pattyx1, whitex1, bunx1 | white burger |
| **Spell burger** | wizardx1, bunx1 | spell burger |
| **Big bite burger** | pattyx3, bunx1, cheese wedgex2 | big bite burger |
| **Super bite burger** | saltx5, blackpepperx5, pattyx5, 任意食物x4, cheese wedgex3, boiled eggx1, piece of baconx1, bunx1, picklex1 | super bite burger |
| **Jelly burger** | slimejellyx5, bunx1 | jelly burger |
| **Jelly burger** | cherryjellyx5, bunx1 | jelly burger |
| **Five alarm burger** | pattyx1, 任意食物x2, bunx1 | five alarm burger |
| **Rat burger** | 任意食物x1, bunx1 | rat burger |
| **Home run baseball burger** | baseball batx1, bunx1 | home run baseball burger |
| **Bacon Burger** | piece of baconx3, bunx1 | bacon burger |
| **Empowered Burger** | plasmax2, bunx1 | empowered burger |
| **Cat burger** | bunx1, pattyx1, catx1, catx1 | catburger |
| **Crab Burger** | crab meatx2, bunx1 | crab burger |
| **Cheese Burger** | pattyx1, bunx1, cheese wedgex1 | cheese burger |
| **Soylent Burger** | \improper Soylent Greenx1, bunx1, cheese wedgex2 | soylent burger |
| **McRib** | bbq ribsx1, 任意食物x1, bunx1 | mcrib |
| **McGuffin** | fried eggx1, piece of baconx2, bunx1 | mcguffin |
| **Chicken Sandwich** | chicken pattyx1, mayonnaisex5, bunx1 | chicken sandwich |
| **Crazy hamburger** | pattyx2, bunx1, cheese wedgex2, 任意食物x1, 任意食物x1, greenx1, flarex1, oilx15 | crazy hamburger |
| **Sloppy moe** | bunx1, cutletx2, 任意食物x1, bbqsaucex5 | sloppy moe |
| **Plain Rootburger** | pattyx1, rootrollx1 | plain rootburger |
| **Rat Rootburger** | 任意食物x1, rootrollx1 | rat rootburger |
| **Root-Guffin** | fried eggx1, piece of baconx2, rootrollx1 | root-guffin |
| **RootRib** | bbq ribsx1, 任意食物x1, rootrollx1 | rootrib |
| **Chicken Rootwich** | chicken pattyx1, mayonnaisex5, rootrollx1 | chicken rootwich |
| **Fish rootburger** | fish filletx1, rootrollx1 | fish rootwich |
| **Sssloppy moe** | rootrollx1, cutletx2, 任意食物x1, bbqsaucex5 | sssloppy moe |

## 8.3 蛋糕类（27 种）

| 食物 | 材料需求 | 成品 |
|---|---|---|
| **Carrot cake** | plain cakex1, 任意食物x2 | carrot cake |
| **Cheese cake** | plain cakex1, cheese wedgex2 | cheese cake |
| **Apple cake** | plain cakex1, 任意食物x2 | apple cake |
| **Orange cake** | plain cakex1, 任意食物x2 | orange cake |
| **Lime cake** | plain cakex1, 任意食物x2 | lime cake |
| **Lemon cake** | plain cakex1, 任意食物x2 | lemon cake |
| **Chocolate cake** | plain cakex1, chocolate barx2 | chocolate cake |
| **Birthday cake** | plain cakex1, 蜡烛x1, sugarx5, caramelx2 | birthday cake |
| **Energy cake** | birthday cakex1, swordx1 | energy cake |
| **Brain cake** | brainx1, plain cakex1 | brain cake |
| **Slime cake** | slime extractx1, plain cakex1 | slime cake |
| **Pumpkin spice cake** | plain cakex1, 任意食物x2 | pumpkin spice cake |
| **Angel food cake** | holywaterx15, plain cakex1 | angel food cake |
| **Pound cake** | plain cakex4 | pound cake |
| **Hardware cake** | plain cakex1, 电路板x2, acidx5 | hardware cake |
| **strawberry chocolate cake** | plain cakex1, chocolate barx2, 任意食物x5 | strawberry chocolate cake |
| **Pavlova with cream** | eggwhitex12, sugarx15, whipped creamx10, 任意食物x5 | pavlova |
| **Pavlova with korta cream** | eggwhitex12, sugarx15, korta milkx10, 任意食物x5 | pavlova with nuts |
| **blackberry and strawberry vanilla cake** | plain cakex1, 任意食物x5 | blackberry and strawberry vanilla cake |
| **clown cake** | plain cakex1, sundaex2, 任意食物x5 | clown cake |
| **vanilla cake** | plain cakex1, 任意食物x2 | vanilla cake |
| **Spaceman's Cake** | plain cakex1, 任意食物x2, creamx5, berryjuicex5 | spaceman's cake |
| **Living cat/cake hybrid** | brainx1, heartx1, birthday cakex1, meatx3, 血液x30, sprinklesx5, tesliumx1 | cak |
| **English Fruitcake** | plain cakex1, \improper 4no raisinsx1, 任意食物x1, rumx5 | english fruitcake |
| **Plum cake** | plain cakex1, 任意食物x2 | plum cake |
| **Wedding cake** | plain cakex4, sugarx120 | wedding cake |
| **Pineapple cream cake** | plain cakex1, 任意食物x1, creamx20 | pineapple cream cake |

## 8.4 饮品小食（15 种）

| 食物 | 材料需求 | 成品 |
|---|---|---|
| **Lizard Wine** | lizardx1, ethanolx100 | lizardwine |
| **Moonshine Jug** | 瓶子x1, moonshinex100 | moonshine |
| **Hooch Bottle** | 瓶子x1, papersackx1, hoochx100 | hooch |
| **Blazaam Bottle** | 瓶子x1, blazaamx100 | blazaam |
| **Champagne Bottle** | 瓶子x1, champagnex100 | champagne |
| **Trappist Bottle** | smallx1, trappistx50 | trappist |
| **Goldschlager Bottle** | 瓶子x1, goldschlagerx100 | goldschlager |
| **Patron Bottle** | 瓶子x1, patronx100 | patron |
| **Holy Water Flask** | 瓶子x1, holywaterx100 | holywater |
| **Nothing Bottle** | 瓶子x1, nothingx100 | bottleofnothing |
| **Small Carton** | 纸板x1 | smallcarton |
| **candy corn liquor** | whiskeyx100, candy cornx1, 瓶子x1 | candycornliquor |
| **Kong** | whiskeyx100, monkey cubex1, 瓶子x1 | kong |
| **pruno mix** | trashx1, moldy 'bread' slicex1, 任意食物x4, candy cornx2, waterx15 | pruno |
| **lean** | 杯x1, gumballx2, morphinex5, space upx15 | lean |

## 8.5 蛋类（7 种）

| 食物 | 材料需求 | 成品 |
|---|---|---|
| **Egg with sausage** | sausagex1, fried eggx1 | egg with sausage |
| **Omelette du fromage** | eggx2, cheese wedgex2 | omelette du fromage |
| **Chocolate egg** | boiled eggx1, chocolate barx1 | chocolate egg |
| **Eggs benedict** | fried eggx1, steakx1, bread slicex1 | eggs benedict |
| **Egg bowl** | 碗x1, boiled ricex1, boiled eggx1, 任意食物x1, 任意食物x1 | egg bowl |
| **Egg Wrap** | soysaucex10, fried eggx1, 任意食物x1 | egg wrap |
| **Chawanmushi** | waterx5, soysaucex5, boiled eggx2, 任意食物x1 | chawanmushi |

## 8.6 冷冻甜点（29 种）

| 食物 | 材料需求 | 成品 |
|---|---|---|
| **Icecream sandwich** | creamx5, icex5, waffle conex1 | ice cream sandwich |
| **Strawberry ice cream sandwich** | creamx5, icex5, 任意食物x2, waffle conex1 | strawberry ice cream sandwich |
| **space freezy** | bluecherryjellyx5, spacemountainwindx15, waffle conex1 | space freezy |
| **sundae** | creamx5, 任意食物x1, 任意食物x1, waffle conex1 | sundae |
| **honkdae** | creamx5, clown hatx1, 任意食物x1, 任意食物x2, waffle conex1 | honkdae |
| **Cornuto** | chocolate barx1, creamx4, icex2, sugarx4, waffle conex1 | cornuto |
| **Flavorless snowcone** | sillycupx1, icex15 | flavorless snowcone |
| **Pineapple snowcone** | sillycupx1, icex15, pineapplejuicex5 | pineapple snowcone |
| **Lime snowcone** | sillycupx1, icex15, limejuicex5 | lime snowcone |
| **Lemon snowcone** | sillycupx1, icex15, lemonjuicex5 | lemon snowcone |
| **Apple snowcone** | sillycupx1, icex15, applejuicex5 | apple snowcone |
| **Grape snowcone** | sillycupx1, icex15, grapejuicex5 | grape snowcone |
| **Orange snowcone** | sillycupx1, icex15, orangejuicex5 | orange snowcone |
| **Bluecherry snowcone** | sillycupx1, icex15, bluecherryjellyx5 | bluecherry snowcone |
| **Cherry snowcone** | sillycupx1, icex15, cherryjellyx5 | cherry snowcone |
| **Berry snowcone** | sillycupx1, icex15, berryjuicex5 | berry snowcone |
| **Fruit Salad snowcone** | sillycupx1, waterx5, icex15, orangejuicex5, limejuicex5, lemonjuicex5 | fruit salad snowcone |
| **Mime snowcone** | sillycupx1, icex15, nothingx5 | mime snowcone |
| **Clown snowcone** | sillycupx1, icex15, laughterx5 | clown snowcone |
| **Space Cola snowcone** | sillycupx1, icex15, space colax5 | space cola snowcone |
| **Space Mountain Wind snowcone** | sillycupx1, icex15, spacemountainwindx5 | space mountain wind snowcone |
| **Pwrgame snowcone** | sillycupx1, icex15, pwr gamex15 | pwrgame snowcone |
| **Honey snowcone** | sillycupx1, icex15, honeyx5 | honey snowcone |
| **Rainbow snowcone** | sillycupx1, icex15, colorful reagentx1 | rainbow snowcone |
| **Orange popsicle** | popsicle stickx1, orangejuicex4, icex2, creamx2, vanillax2, sugarx2 | orange creamsicle |
| **Berry popsicle** | popsicle stickx1, berryjuicex4, icex2, creamx2, vanillax2, sugarx2 | berry creamsicle |
| **Jumbo icecream** | popsicle stickx1, chocolate barx1, icex2, creamx2, vanillax3, sugarx2 | jumbo ice cream |
| **Licorice popsicle** | popsicle stickx1, blumpkinjuicex4, saltx2, icex2, creamx2, vanillax2, sugarx2 | \improper Void Bar™ |
| **Meatsicle** | popsicle stickx1, meatx1, icex2, sugarx2 | meatsicle |

## 8.7 蜥蜴族料理（43 种）

| 食物 | 材料需求 | 成品 |
|---|---|---|
| **Raw Tiziran blood sausage** | raw cutletx1, raw piece of baconx1, 血液x5, saltx2 | raw Tiziran blood sausage |
| **Raw headcheese** | meatx1, saltx10, blackpepperx5 | raw headcheese block |
| **Crispy shredded lung stirfry** | 任意食物x1, 任意食物x1, 任意食物x1, lungsx1, 碗x1 | crispy shredded lung stirfry |
| **Tsatsikh** | heartx1, liverx1, lungsx1, stomachx1, saltx2, blackpepperx2 | tsatsikh |
| **Liver pate** | liverx1, raw cutletx1, 任意食物x1 | liver pate |
| **Moonfish caviar paste** | moonfish eggsx1, saltx2 | moonfish caviar paste |
| **Desert snail cocleas** | canned desert snailsx1, 任意食物x1, lemonjuicex3, blackpepperx2, olivex3, 碗x1 | desert snail cocleas |
| **Fried blood sausage** | raw Tiziran blood sausagex1, korta flourx5, waterx5 | fried blood sausage |
| **Loaded poms-franzisks** | space friesx1, cutletx2, bbqsaucex5 | loaded poms-franzisks |
| **Eyeball-and-brain pate** | brainx1, eyesx1, 任意食物x1, saltx3 | eyeball-and-brain pate |
| **Crispy breaded headcheese** | headcheese slicex1, rootbread slicex1 | crispy breaded headcheese |
| **Picoss skewers** | cleaned armorfishx2, 任意食物x1, 任意食物x1, rodsx1, vinegarx5 | picoss skewer |
| **Nectar larvae** | canned bee larvax1, 任意食物x1, 任意食物x1, korta nectarx5 | nectar larvae |
| **Mushroomy Stirfry** | steeped seraka mushroomsx1, 任意食物x1, 任意食物x1, olivex5 | mushroomy stirfry |
| **Moonfish demiglace** | grilled moonfishx1, 任意食物x1, 任意食物x1, korta milkx5, winex5 | moonfish demiglace |
| **Zagosk surf n turf smorgasbord** | grilled moonfishx1, picoss skewerx2, steakx1, bbq ribsx1 | \improper Zagosk surf 'n' turf smorgasbord |
| **Rootdough (Without Eggs)** | 任意食物x2, soymilkx15, korta flourx5, waterx10 | root dough |
| **Rootdough (With Eggs)** | 任意食物x2, eggx1, korta flourx5, waterx10 | root dough |
| **Desert snail nizaya** | canned desert snailsx1, nizaya pastax1, 任意食物x1, winex5 | desert snail nizaya |
| **Garlic nizaya** | nizaya pastax1, 任意食物x1, 任意食物x1, olivex5 | garlic-and-oil nizaya |
| **Demit nizaya** | nizaya pastax1, 任意食物x1, 任意食物x1, 任意食物x1, korta milkx5, korta nectarx5 | demit nizaya |
| **Mushroom nizaya** | nizaya pastax1, steeped seraka mushroomsx1, 任意食物x1, olivex5 | mushroom nizaya |
| **Rustic flatbread** | root flatbreadx1, 任意食物x1, lemonjuicex2, olivex3 | rustic flatbread |
| **Italic flatbread** | root flatbreadx1, 任意食物x1, 任意食物x1, meatballx2, olivex3 | \improper Italic flatbread |
| **Imperial flatbread** | root flatbreadx1, liver patex1, sauerkrautx1, headcheese blockx1 | \improper Imperial flatbread |
| **Meatlovers flatbread** | root flatbreadx1, meatx1 | meatlovers flatbread |
| **Stinging flatbread** | root flatbreadx1, canned bee larvax1, canned gunner jellyfishx1 | \improper Stinging flatbread |
| **Zmorgast flatbread** | root flatbreadx1, 任意食物x2, eggx1, liverx1 | \improper Zmorgast flatbread |
| **BBQ fish flatbread** | root flatbreadx1, fish filletx2, bbqsaucex5 | \improper BBQ fish flatbread |
| **Mushroom and tomato flatbread** | root flatbreadx1, 任意食物x1, 任意食物x3, olivex3 | mushroom and tomato flatbread |
| **Nut paste flatbread** | root flatbreadx1, korta flourx5, korta milkx5 | nut paste flatbread |
| **Emperor roll** | rootrollx1, liver patex1, headcheese slicex2, moonfish caviar pastex1 | emperor roll |
| **Honey sweetroll** | rootrollx1, 任意食物x1, 任意食物x1, honeyx5 | honey sweetroll |
| **Black scrambled eggs** | eggx2, 血液x5, vinegarx2 | black scrambled eggs |
| **Patzikula** | 任意食物x2, 任意食物x1, 任意食物x1, eggx2 | patzikula |
| **Korta brittle slab** | 任意食物x2, korta nectarx5, sugarx5, oilx3, saltx2 | korta brittle slab |
| **Korta ice** | sillycupx1, icex15, korta nectarx5, 任意食物x1 | korta ice |
| **Candied mushrooms** | rodsx1, steeped seraka mushroomsx1, caramelx5, saltx1 | candied mushrooms |
| **Sauerkraut** | 任意食物x2, saltx10 | sauerkraut |
| **Tiziran dumplings** | 任意食物x1, korta flourx5 | \improper Tiziran dumplings |
| **Steeped mushrooms** | 任意食物x1, lyex5 | steeped seraka mushrooms |
| **Peanut butter and jelly rootwich** | rootbread slicex2, peanut butterx5, cherryjellyx5 | peanut butter and jelly rootwich |
| **Peanut butter and banana rootwich** | rootbread slicex2, peanut butterx5, 任意食物x1 | peanut butter and banana rootwich |

## 8.8 火星族料理（76 种）

| 食物 | 材料需求 | 成品 |
|---|---|---|
| **Kimchi** | 任意食物x1, 任意食物x1, saltx5 | kimchi |
| **Inferno kimchi** | 任意食物x1, 任意食物x1, saltx5 | inferno kimchi |
| **Garlic kimchi** | 任意食物x1, 任意食物x1, 任意食物x1, saltx5 | garlic kimchi |
| **Surimi** | fish filletx1 | surimi |
| **Sambal** | 任意食物x1, 任意食物x1, 任意食物x1, sugarx3, limejuicex3, 碗x1 | sambal |
| **Katsu fillet** | raw cutletx1, reispan slicex1 | katsu fillet |
| **Rice dough** | flourx10, ricex10, waterx10 | rice dough |
| **Hurricane fried rice** | boiled ricex1, eggx1, 任意食物x1, 任意食物x1, cutletx1, pineapple slicex1, soysaucex3, 碗x1 | hurricane fried rice |
| **Hua Mulan congee** | rice porridgex10, piece of baconx1, fried eggx2, 碗x1 | \improper Hua Mulan congee |
| **Ikareis** | boiled ricex1, canned squid inkx1, 任意食物x1, 任意食物x1, sausagex1, 任意食物x1, 碗x1 | ikareis |
| **Hawaiian fried rice** | boiled ricex1, slice of chapx1, 任意食物x1, pineapple slicex1, 任意食物x1, soysaucex5, 碗x1 | \improper Hawaiian fried rice |
| **Ketchup fried rice** | boiled ricex1, 任意食物x1, american sausagex1, 任意食物x1, 任意食物x1, ketchupx5, worcestershirex2, 碗x1 | ketchup fried rice |
| **Mediterranean fried rice** | boiled ricex1, 任意食物x1, 任意食物x1, firm cheese slicex1, 任意食物x1, meatballx1, 碗x1 | mediterranean fried rice |
| **Egg fried rice** | boiled ricex1, eggx1, soysaucex3, 碗x1 | egg fried rice |
| **Bibimbap** | boiled ricex1, 任意食物x1, 任意食物x1, cutletx1, kimchix1, eggx1, 碗x1 | bibimbap |
| **Bulgogi noodles** | cooked noodlesx1, cutletx1, 任意食物x1, 任意食物x1, 任意食物x1, teriyakix4, 碗x1 | bulgogi noodles |
| **Yakisoba katsu** | cooked noodlesx1, 任意食物x1, 任意食物x1, 任意食物x1, katsu filletx1, worcestershirex3, 碗x1 | yakisoba katsu |
| **Martian fried noodles** | cooked noodlesx1, 任意食物x2, cutletx1, 任意食物x1, eggx1, soysaucex3, red bayx3, 碗x1 | \improper Martian fried noodles |
| **Simple fried noodles** | cooked noodlesx1, soysaucex3, 碗x1 | simple fried noodles |
| **Setagaya curry** | boiled ricex1, 任意食物x1, honeyx3, ketchupx3, chocolate barx1, coffeex3, winex3, curry powderx3, meatx1, 任意食物x1, 任意食物x1, 任意食物x1, 碗x1 | \improper Setagaya curry |
| **Big Blue Burger** | bunx1, pattyx2, 任意食物x1, cheese wedgex1, piece of baconx1, pineapple slicex1, teriyakix4 | \improper Big Blue burger |
| **Chappy Patty** | bunx1, grilled slice of chapx2, fried eggx1, cheese wedgex1, ketchupx3 | \improper Chappy patty |
| **King Katsu sandwich** | reispan slicex2, katsu filletx1, piece of baconx1, kimchix1, 任意食物x1, 任意食物x1 | \improper King Katsu sandwich |
| **Marte Cubano sandwich** | reispan slicex2, piece of baconx1, picklex2, cheese wedgex1 | \improper Marte Cubano sandwich |
| **Little Shiro sandwich** | reispan slicex2, cutletx1, fried eggx1, garlic kimchix1, mozzarella cheesex1, 任意食物x1 | \improper Little Shiro sandwich |
| **Croque-Martienne sandwich** | reispan slicex2, cutletx1, cheese wedgex1, pineapple slicex1, fried eggx1 | croque-martienne |
| **Prospect Sunrise sandwich** | reispan slicex2, piece of baconx1, cheese wedgex1, omelette du fromagex1, picklex1 | \improper Prospect Sunrise |
| **Takoyaki** | octopus tentaclex1, 任意食物x1, martian batterx6, worcestershirex3 | takoyaki |
| **Russian takoyaki** | octopus tentaclex1, 任意食物x1, martian batterx6, capsaicinx3 | russian takoyaki |
| **Tacoyaki** | meatballx1, 任意食物x1, martian batterx6, red bayx3, cheese wedgex1 | tacoyaki |
| **Okonomiyaki** | martian batterx6, worcestershirex3, mayonnaisex3, 任意食物x1, 任意食物x1 | okonomiyaki |
| **Brat-kimchi** | sausagex1, kimchix1, sugarx3 | brat-kimchi |
| **Tonkatsuwurst** | sausagex1, space friesx1, worcestershirex3, red bayx2 | tonkatsuwurst |
| **Ti hoeh koe** | boiled ricex1, \improper Gallery's salt reserves peanutsx1, 任意食物x1, 血液x5 | ti hoeh koe skewer |
| **Kitzushi** | boiled ricex1, tofux1, cheese wedgex1, 任意食物x1 | kitzushi |
| **Epok-epok** | dough slicex1, chicken cutletx1, 任意食物x1, boiled eggx1, curry powderx3 | epok-epok |
| **Roti John** | 法棍x1, raw meatballx1, eggx1, 任意食物x1, capsaicinx3, mayonnaisex3 | roti john |
| **Izakaya fries** | space friesx1, 任意食物x1, red bayx3, mayonnaisex3 | izakaya fries |
| **Kurry-OK subsando** | 法棍x1, izakaya friesx1, katsu filletx1, curry saucex5 | kurry-ok subsando |
| **Loco moco** | boiled ricex1, pattyx1, 任意食物x1, fried eggx1, gravyx5 | loco moco |
| **Wild duck fries** | izakaya friesx1, cutletx1, ketchupx3 | wild duck fries |
| **Little Hawaii hotdog** | hotdogx1, pineapple slicex1, 任意食物x1, teriyakix3 | \improper Little Hawaii hotdog |
| **Salt n' chilli fries** | space friesx1, 任意食物x1, 任意食物x1, 任意食物x1, saltx3 | salt n' chilli fries |
| **Steak croquette** | steakx1, mashed potatoesx1, reispan slicex1 | steak croquette |
| **Chapsilog** | grilled slice of chapx2, fried eggx1, boiled ricex1, 任意食物x1 | chapsilog |
| **Chap hash** | slice of chapx2, eggx1, 任意食物x1, 任意食物x1, 任意食物x1 | chap hash |
| **Agedashi tofu** | tofux1, 任意食物x1, dashix20, 碗x1 | agedashi tofu |
| **Po kok gai** | boiled ricex1, chicken meatx1, coconut milkx5, curry powderx3, 碗x1 | po kok gai |
| **Huoxing tofu** | tofux1, raw meatballx1, 任意食物x1, 任意食物x1, 碗x1 | \improper Huoxing tofu |
| **Fēizhōu jī** | chicken meatx1, 任意食物x1, 任意食物x1, vinegarx5 | fēizhōu jī |
| **Galinha de cabidela** | chicken meatx1, 任意食物x1, uncooked ricex1, 血液x5, 碗x1 | galinha de cabidela |
| **Katsu curry** | katsu filletx1, boiled ricex1, curry saucex5, 碗x1 | katsu curry |
| **Beef bowl** | cutletx1, 任意食物x1, boiled ricex1, dashix5, 碗x1 | beef bowl |
| **Salt n' chilli octopus bowl** | grilled octopus tentaclex1, 任意食物x1, 任意食物x1, boiled ricex1, saltx2, curry saucex5, 碗x1 | salt n' chilli octopus bowl |
| **Kansai bowl** | kamaboko slicex2, boiled eggx1, 任意食物x1, boiled ricex1, dashix5, 碗x1 | \improper Kansai bowl |
| **Eigamudo curry** | 任意食物x1, kimchix1, fish filletx1, boiled ricex1, cafe lattex5, 碗x1 | \improper Eigamudo curry |
| **Çilbir** | 任意食物x1, fried eggx1, 任意食物x1, yoghurtx5, olivex2 | çilbir |
| **Peking duck crepes a l'orange** | pancakex1, cutletx1, 任意食物x1, cognacx2 | \improper Peking duck crepes a l'orange |
| **Vulgaris spekkoek** | plain cakex1, 任意食物x1, butter slicex2 | vulgaris spekkoek |
| **Pineapple foster** | pineapple slicex1, caramelx2, waffle conex1, rumx2, 碗x1 | pineapple foster |
| **Pastel de nata** | pastry basex1, 任意食物x1, eggx1, sugarx2 | pastel de nata |
| **Boh loh yah** | dough slicex1, butter slicex1, sugarx5 | boh loh yah |
| **Banana fritter** | 任意食物x1, martian batterx2 | banana fritter |
| **Pineapple fritter** | pineapple slicex1, martian batterx2 | pineapple fritter |
| **Kasei dango** | rodsx1, sugarx5, ricex5, orangejuicex2, grenadinex2 | kasei dango |
| **Peanut-butter ice cream mochi** | sugarx5, ricex5, peanut butterx2, waffle conex1 | peanut butter ice cream mochi |
| **Frozen pineapple pop** | pineapple slicex1, chocolate barx1, popsicle stickx1 | frozen pineapple pop |
| **Sea-salt ice cream bar** | creamx5, sugarx5, saltx3, popsicle stickx1 | sea salt ice-cream bar |
| **Berry topsicle** | tofux1, berryjuicex5, sugarx5, popsicle stickx1 | berry topsicle |
| **Banana topsicle** | tofux1, bananax5, sugarx5, popsicle stickx1 | banana topsicle |
| **Pineapple topsicle** | tofux1, pineapplejuicex5, sugarx5, popsicle stickx1 | pineapple topsicle |
| **Plasma Dog Supreme** | hotdogx1, pineapple slicex1, sambalx1, 任意食物x1 | \improper Plasma Dog Supreme |
| **Frickles** | picklex1, martian batterx2, red bayx1 | frickles |
| **Raw ballpark pretzel** | dough slicex1, saltx2 | raw pretzel |
| **Raw ballpark tsukune** | raw chicken meatballx1, teriyakix2, rodsx1 | raw tsukune |
| **Sprout bowl** | pickled voltvinex1, fish filletx1, boiled ricex1, dashix5, 碗x1 | \improper Sprout bowl |

## 8.9 肉类（34 种）

| 食物 | 材料需求 | 成品 |
|---|---|---|
| **Human kebab** | rodsx1, steakx2 | human-kebab |
| **Kebab** | rodsx1, steakx2 | meat-kebab |
| **Tofu kebab** | rodsx1, tofux2 | tofu-kebab |
| **Lizard tail kebab** | rodsx1, lizardx1 | lizard-tail kebab |
| **Fiesta Skewer** | rodsx1, 任意食物x1, cutletx1, 任意食物x1, 任意食物x1 | fiesta skewer |
| **Spider eggs ham** | saltx1, spider eggsx1, spider cutletx2 | green eggs and ham |
| **Tempeh starter** | 任意食物x5, plumpx1 | tempeh starter |
| **Corned beef** | saltx5, steakx1, 任意食物x2 | corned beef and cabbage |
| **Filet migrawr** | manly dorfx5, bear steakx1 | filet migrawr |
| **Stewed soymeat** | soy dopex2, 任意食物x1, 任意食物x1 | stewed soy meat |
| **Raw sausage** | raw meatballx1, raw cutletx2 | raw sausage |
| **Chicken nugget** | cutletx1 | chicken nugget |
| **Raw Khinkali** | dough slicex1, 任意食物x1, meatballx1 | raw khinkali |
| **Meat bun** | soysaucex5, bunx1, meatballx1, 任意食物x1 | meat bun |
| **Pig in a Blanket** | bunx1, butter slicex1, cutletx1 | pig in a blanket |
| **Rat Kebab** | rodsx1, 任意食物x1 | rat-kebab |
| **Double Rat Kebab** | rodsx1, 任意食物x2 | double rat-kebab |
| **Rice and Pork** | 碗x1, boiled ricex1, cutletx2 | rice and pork |
| **BBQ Ribs** | bbqsaucex5, steakx2, rodsx2 | bbq ribs |
| **Meat Clown** | steakx1, 任意食物x1 | meat clown |
| **Lasagna** | cutletx2, 任意食物x1, cheese wedgex2, spaghettix1 | lasagna |
| **Black eyed gumbo** | 碗x1, boiled ricex1, 任意食物x1, 任意食物x1, cutletx1 | black eyed gumbo |
| **Fried Chicken** | chicken meatx1, flourx5, corn starchx5 | fried chicken |
| **Beef Stroganoff** | flourx5, milkx5, saltx2, blackpepperx2, 任意食物x2, 任意食物x1, 任意食物x1, steakx1, 碗x1 | beef stroganoff |
| **Beef Wellington** | steakx1, 任意食物x1, 任意食物x1, piece of baconx1, flat doughx1, creamx5, saltx2, blackpepperx2 | beef wellington |
| **Korta Wellington** | steakx1, 任意食物x1, 任意食物x1, piece of baconx1, flat rootdoughx1, korta milkx5, saltx2, blackpepperx2 | korta wellington |
| **Roast Chicken Dinner** | chicken steakx2, roast parsnipx1, 任意食物x1, 任意食物x1, 任意食物x1, 任意食物x1, 任意食物x1, flourx5, gravyx15, saltx2, blackpepperx2 | roast dinner |
| **Grain-Free Roast Chicken Dinner** | chicken steakx2, roast parsnipx1, 任意食物x1, 任意食物x1, 任意食物x1, 任意食物x1, 任意食物x1, korta flourx25, nutrimentx5, 血液x5, saltx2, blackpepperx2 | grain-free roast dinner |
| **Meat-Free Roast Dinner** | tofux6, roast parsnipx1, 任意食物x1, 任意食物x1, 任意食物x1, 任意食物x1, 任意食物x1, flourx15, soymilkx15, saltx2, blackpepperx2 | tofu roast dinner |
| **Full English Breakfast** | sausagex1, fried eggx2, piece of baconx1, 任意食物x1, 任意食物x1, tin of beansx1, buttered toastx1 | full english breakfast |
| **Envirochow** | corgi meatx2, vitaminx5 | dog eat dog envirochow |
| **Meatloaf** | meatx2, 任意食物x1, 任意食物x1, ketchupx10 | raw meatloaf |
| **Sweet and sour meatballs** | meatballx3, pineapple slicex1, 任意食物x1, sugarx5 | sweet and sour meatballs |
| **Pineapple skewer** | rodsx1, pineapple slicex2, cutletx2 | pineapple skewer |

## 8.10 墨西哥料理（19 种）

| 食物 | 材料需求 | 成品 |
|---|---|---|
| **burrito** | tortillax1, 任意食物x2 | burrito |
| **cheesy burrito** | cheese wedgex2, tortillax1, 任意食物x1 | cheesy burrito |
| **Carne de asada burrito** | tortillax1, cutletx2, 任意食物x1 | carne asada burrito |
| **Fuego plasma burrito** | tortillax1, 任意食物x2, 任意食物x1 | fuego plasma burrito |
| **Nachos** | saltx1, tortillax1 | nachos |
| **Cheesy nachos** | saltx1, cheese wedgex1, tortillax1 | cheesy nachos |
| **Cuban nachos** | ketchupx5, 任意食物x2, tortillax1 | cuban nachos |
| **classic taco** | tortillax1, cheese wedgex1, cutletx1, 任意食物x1 | classic taco |
| **plain taco** | tortillax1, cheese wedgex1, cutletx1 | plain taco |
| **Enchiladas** | cutletx2, 任意食物x2, tortillax2 | enchiladas |
| **Stuffed legion** | goliath steakx1, legionx1, ketchupx2, capsaicinx2 | stuffed legion |
| **Chips and salsa** | \improper Boritos corn chipsx1, 任意食物x1, 任意食物x1, 任意食物x1, 碗x1 | chips and salsa |
| **Classic Chimichanga** | tortillax1, cutletx2, cheese wedgex1, 任意食物x1 | classic chimichanga |
| **Vegetarian Chimichanga** | tortillax1, 任意食物x1, 任意食物x1, 任意食物x1 | vegetarian chimichanga |
| **Classic Hard-Shell Taco** | hard taco shellx1, cutletx1, cheese wedgex1, 任意食物x1, 任意食物x1 | classic hard-shell taco |
| **Plain Hard-Shell Taco** | hard taco shellx1, cutletx1 | plain hard-shell taco |
| **Refried Beans** | 碗x1, 任意食物x2, waterx5, 任意食物x1 | refried beans |
| **Spanish Rice** | 碗x1, boiled ricex1, 任意食物x1, saltx1, blackpepperx1 | spanish rice |
| **Pineapple salsa** | pineapple slicex2, 任意食物x1, 任意食物x1, 任意食物x1, 碗x1 | pineapple salsa |

## 8.11 杂项（53 种）

| 食物 | 材料需求 | 成品 |
|---|---|---|
| **Candied apple** | caramelx5, 任意食物x1 | candied apple |
| **Spider Lollipop** | rodsx1, sugarx5, waterx5, spiderlingx1 | spider lollipop |
| **Choco coin** | chocolate barx1 | chocolate coin |
| **Fudge dice** | dicex1, chocolate barx1 | fudge dice |
| **Choco orange** | 任意食物x1, chocolate barx1 | chocolate orange |
| **Cheesy fries** | space friesx1, cheese wedgex1 | cheesy fries |
| **Poutine** | space friesx1, cheese wedgex1, gravyx3 | poutine |
| **Beans** | ketchupx5, 任意食物x2 | tin of beans |
| **eggplant parmigiana** | cheese wedgex2, 任意食物x1 | eggplant parmigiana |
| **melon keg** | vodkax25, 任意食物x1, vodkax1 | melon keg |
| **Honey nut bar** | 任意食物x1, honeyx5 | honey nut bar |
| **Powercrepe** | flat doughx1, milkx1, cherryjellyx5, superx1, sabrex1 | powercrepe |
| **Bran Requests Cereal** | 任意食物x1, \improper 4no raisinsx1 | bran requests cereal |
| **Rice pudding** | 碗x1, milkx5, sugarx5, boiled ricex1 | rice pudding |
| **Risotto** | 碗x1, cheese wedgex1, winex5, boiled ricex1, 任意食物x1 | risotto |
| **Living bear/butter hybrid** | brainx1, heartx1, stick of butterx4, meatx5, 血液x50, tesliumx1 | butter |
| **Crab Rangoon** | dough slicex1, creamx5, cheese wedgex1, raw crab meatx1 | crab rangoon |
| **Royal Cheese** | cheese wheelx1, 皇冠x1, strange reagentx5, mutagenx5 | royal cheese |
| **Ant Candy** | rodsx1, sugarx5, waterx5, 蚂蚁x10 | ant candy |
| **Pesto** | firm cheese slicex1, saltx5, 任意食物x2, 任意食物x1, olivex5, canned pine nutsx1 | pesto |
| **Tomato sauce** | canned San Marzano tomatoesx1, saltx2, 任意食物x1, olivex5 | tomato sauce |
| **Bechamel sauce** | milkx10, flourx5, butter slicex1 | béchamel sauce |
| **Pierogi** | dough slicex1, 任意食物x1, 任意食物x1 | pierogi |
| **Stuffed cabbage** | cutletx2, boiled ricex1, 任意食物x1, 任意食物x1 | stuffed cabbage |
| **Granola bar** | 任意食物x1, 任意食物x1, chocolate barx1, \improper 4no raisinsx1 | granola bar |
| **Onigiri** | boiled ricex1, seaweed sheetx1 | onigiri |
| **Mashed potatoes** | 任意食物x2, 任意食物x1, butter slicex1 | mashed potatoes |
| **Pacoca** | 任意食物x2, sugarx5, saltx2 | pacoca |
| **Spring roll** | 任意食物x1, 任意食物x1, 任意食物x1, ricex10, waterx10 | spring roll |
| **Caramel popcorn** | popcornx1, caramelx3 | caramel popcorn |
| **Salty popcorn** | popcornx1, saltx3 | salty popcorn |
| **Spacy liberty duff** | vodkax5, 碗x1, 任意食物x3 | spacy liberty duff |
| **Amanita jelly** | vodkax5, 碗x1, 任意食物x3 | amanita jelly |
| **Buttered baked potato** | baked potatox1, butter slicex1 | buttered baked potato |
| **Loaded baked potato** | baked potatox1, cheese wedgex1, piece of baconx1, 任意食物x1 | loaded baked potato |
| **Cheese pierogi** | dough slicex1, 任意食物x1, cheese wedgex1 | cheese pierogi |
| **Meat pierogi** | dough slicex1, 任意食物x1, cutletx1 | meat pierogi |
| **Caramel truffle** | chocolate barx1, caramelx2 | caramel truffle |
| **Chocolate truffle** | chocolate barx1, sugarx2 | chocolate truffle |
| **Peanut truffle** | chocolate barx1, 任意食物x1 | peanut truffle |
| **Peanut butter cup** | chocolate barx1, peanut butterx2 | peanut butter cup |
| **Jar of pickles** | largex1, 任意食物x10, saltx20 | pickles jar |
| **Sauteed eggplant** | 任意食物x1, 任意食物x1, olivex3 | sauteed eggplant |
| **Stuffed eggplant** | 任意食物x1, 任意食物x1, 任意食物x1, cheese wedgex1, cutletx1 | stuffed eggplant |
| **Baba ghanoush** | 碗x1, pita breadx1, 任意食物x1, 任意食物x1, olivex5, lemonjuicex3 | baba ghanoush |
| **Moussaka** | 任意食物x2, 任意食物x1, 任意食物x1, cutletx1, béchamel saucex1 | moussaka |
| **Falafel** | 任意食物x1, 任意食物x1, 任意食物x1, 任意食物x1 | falafel |
| **Candied pineapple** | pineapple slicex1, sugarx2, waterx2 | candied pineapple |
| **Tzatziki sauce** | 任意食物x1, olivex2, 任意食物x1, saltx1 | tzatziki sauce |
| **Tzatziki and pita bread** | tzatziki saucex1, pita breadx1 | tzatziki and pita bread |
| **Grilled beef gyro** | tzatziki saucex1, pita breadx1, cutletx2, 任意食物x1, 任意食物x1, 任意食物x1 | grilled beef gyro |
| **Vegetarian gyro** | tzatziki saucex1, pita breadx1, 任意食物x1, 任意食物x1, 任意食物x1, 任意食物x1 | vegetarian gyro |
| **Raw pita bread** | flourx10, waterx5, olivex2, sugarx2 | raw pita bread |

## 8.12 蛾族料理（31 种）

| 食物 | 材料需求 | 成品 |
|---|---|---|
| **Herby cheese** | curd cheesex1, 任意食物x4, 碗x1 | herby cheese |
| **Mothic salad** | 任意食物x1, 任意食物x2, 任意食物x1 | mothic salad |
| **Toasted seeds** | sunflowerx1, pumpkinx1, poppyx1, olivex2 | toasted seeds |
| **Engine fodder** | toasted seedsx1, \improper C&Dsx1, popcornx1, \improper Gallery's peanutsx1, chipsx1 | engine fodder |
| **Skeklitmischtpoppl (Squeaking stir fry)** | 碗x1, cheese curdsx1, tofux1, 任意食物x1, boiled ricex1, 任意食物x1, 任意食物x1 | skeklitmischtpoppl |
| **Sweet chili cabbage wrap** | grilled cheesex1, mothic saladx1, 任意食物x1, 任意食物x1, honeyx5 | sweet chili cabbage wrap |
| **Ozlsettitæloskekllön ede pommes (Loaded curds and fries)** | chili sin carnex10, cheese curdsx1, 任意食物x1, cheese wedgex1, space friesx1 | ozlsettitæloskekllön ede pommes |
| **Stanntkraktælo (Baked cheese platter)** | baked cheese wheelx1, griddle toastx3 | stanntkraktælo |
| **raw green lasagne al forno** | pestox1, boiled spaghettix2, béchamel saucex1, firm cheese slicex1 | raw green lasagne al forno |
| **big rice pan** | vegetable soupx10, boiled ricex2, 任意食物x1, 任意食物x1, 任意食物x2 | big rice pan |
| **Buttered baked corn** | oven-baked cornx1, butter slicex1 | buttered baked corn |
| **Fiesta corn skillet** | oven-baked cornx1, \improper Boritos corn chipsx1, 任意食物x2, 任意食物x1, 任意食物x2, cheese wedgex1 | fiesta corn skillet |
| **raw ratatouille** | 任意食物x1, 任意食物x1, 任意食物x1, roasted bell pepperx1 | raw ratatouille |
| **Mozzarella sticks** | mozzarella cheesex1, bread slicex2 | mozzarella sticks |
| **Raw Voltölpapriken (Stuffed peppers)** | 任意食物x1, herby cheesex1, 任意食物x2 | raw voltölpaprik |
| **Fueljack's lunch** | 任意食物x1, 任意食物x1, 任意食物x2, 任意食物x1, firm cheese slicex1 | \improper Fueljack's lunch |
| **Macheronirölen (Mac balls)** | warm Ready-Donk: Donk-a-Ronix1, tomato saucex1, cornmeal batterx5 | macheronirölen |
| **Fried eggplant and polenta** | cornmeal porridgex10, 任意食物x1, bread slicex2, tomato saucex1, mozzarella cheesex1, 碗x1 | fried eggplant and polenta |
| **Caprese salad** | 任意食物x1, mozzarella cheesex1, 任意食物x1, olivex2, vinegarx2 | caprese salad |
| **Lörtonknusksolt (Fleet salad)** | olivex2, vinegarx2, bread slicex1, grilled cheesex1, 任意食物x1, mothic saladx1, 碗x1 | lörtonknusksolt |
| **Flöfrölenknusksolt (Cotton salad)** | olivex2, vinegarx2, 任意食物x1, mothic saladx1, 棉花x2, 碗x1 | flöfrölenknusksolt |
| **Kæniatknusksolt (Kenyan salad)** | limejuicex2, 任意食物x1, 任意食物x1, 任意食物x1, 任意食物x1, oven-baked cornx1, 碗x1 | \improper Kæniatknusksolt |
| **raw mothic margherita pizza** | mothic pizza doughx1, tomato saucex1, mozzarella cheesex1, firm cheese slicex1, 任意食物x1 | raw mothic margherita pizza |
| **raw mothic firecracker pizza** | mothic pizza doughx1, bbqsaucex10, firm cheese slicex1, oven-baked cornx1, 任意食物x1 | raw mothic firecracker pizza |
| **raw mothic five-cheese pizza** | mothic pizza doughx1, tomato saucex1, firm cheese slicex1, cheese wedgex1, mozzarella cheesex1, herby cheesex1, cheese curdsx1 | raw mothic five-cheese pizza |
| **raw mothic white-pie pizza** | mothic pizza doughx1, béchamel saucex1, firm cheese slicex1, 任意食物x1, mozzarella cheesex1, 任意食物x1 | raw mothic white-pie pizza |
| **raw mothic pesto pizza** | mothic pizza doughx1, pestox1, 任意食物x1, mozzarella cheesex1 | raw mothic pesto pizza |
| **raw mothic garlic pizzabread** | mothic pizza doughx1, butter slicex1, 任意食物x1, 任意食物x1 | raw mothic garlic pizzabread |
| **Ælorölen (Cheesecake balls)** | curd cheesex1, chocolate barx1, flourx5, sugarx5, honeyx5 | \improper ælorölen |
| **Mothmallows** | 任意食物x1, vanillax5, sugarx15, rumx5 | mothmallow tray |
| **Moffin** | milkx5, pastry basex1, 布料x1 | moffin |

## 8.13 糕点（75 种）

| 食物 | 材料需求 | 成品 |
|---|---|---|
| **Donut** | sugarx1, pastry basex1 | donut |
| **Chaos donut** | frostoilx5, capsaicinx5, pastry basex1 | chaos donut |
| **Meat donut** | raw cutletx1, pastry basex1 | meat donut |
| **Jelly donut** | berryjuicex5, pastry basex1 | jelly donut |
| **Slime jelly donut** | slimejellyx5, pastry basex1 | jelly donut |
| **Berry Donut** | berryjuicex3, donutx1 | pink donut |
| **Spaceman's Donut** | polypyrx3, donutx1 | spaceman's donut |
| **Apple Donut** | applejuicex3, donutx1 | apple donut |
| **Caramel Donut** | caramelx3, donutx1 | caramel donut |
| **Chocolate Donut** | chocolate barx1, donutx1 | chocolate donut |
| **Blumpkin Donut** | blumpkinjuicex3, donutx1 | blumpkin donut |
| **Bungo Donut** | bungojuicex3, donutx1 | bungo donut |
| **Matcha Donut** | teapowderx3, donutx1 | matcha donut |
| **Sweet Pea Donut** | laughsyrupx3, donutx1 | sweet pea donut |
| **Berry Jelly Donut** | berryjuicex3, jelly donutx1 | pink jelly donut |
| **Spaceman's Jelly Donut** | polypyrx3, jelly donutx1 | spaceman's jelly donut |
| **Apple Jelly Donut** | applejuicex3, jelly donutx1 | apple jelly donut |
| **Caramel Jelly Donut** | caramelx3, jelly donutx1 | caramel jelly donut |
| **Chocolate Jelly Donut** | chocolate barx1, jelly donutx1 | chocolate jelly donut |
| **Blumpkin Jelly Donut** | blumpkinjuicex3, jelly donutx1 | blumpkin jelly donut |
| **Bungo Jelly Donut** | bungojuicex3, jelly donutx1 | bungo jelly donut |
| **Matcha Jelly Donut** | teapowderx3, jelly donutx1 | matcha jelly donut |
| **Sweet Pea Jelly Donut** | laughsyrupx3, jelly donutx1 | sweet pea jelly donut |
| **Berry Slime Donut** | berryjuicex3, jelly donutx1 | pink jelly donut |
| **Spaceman's Slime Donut** | polypyrx3, jelly donutx1 | spaceman's jelly donut |
| **Apple Slime Donut** | applejuicex3, jelly donutx1 | apple jelly donut |
| **Caramel Slime Donut** | caramelx3, jelly donutx1 | caramel jelly donut |
| **Chocolate Slime Donut** | chocolate barx1, jelly donutx1 | chocolate jelly donut |
| **Blumpkin Slime Donut** | blumpkinjuicex3, jelly donutx1 | blumpkin jelly donut |
| **Bungo Slime Donut** | bungojuicex3, jelly donutx1 | bungo jelly donut |
| **Matcha Slime Donut** | teapowderx3, jelly donutx1 | matcha jelly donut |
| **Sweet Pea Jelly Donut** | laughsyrupx3, jelly donutx1 | sweet pea jelly donut |
| **Waffles** | pastry basex2 | waffles |
| **Soylent viridians** | pastry basex2, 任意食物x1 | \improper Soylent Virdians |
| **Soylent green** | pastry basex2, meatx2 | \improper Soylent Green |
| **Roffle waffles** | mushroomhallucinogenx5, pastry basex2 | roffle waffles |
| **Donk-pocket** | dough slicex1, meatballx1 | \improper Donk-pocket |
| **Dank-pocket** | dough slicex1, 任意食物x1 | \improper Dank-pocket |
| **Spicy-pocket** | dough slicex1, meatballx1, 任意食物x1 | \improper Spicy-pocket |
| **Teriyaki-pocket** | dough slicex1, meatballx1, soysaucex3 | \improper Teriyaki-pocket |
| **Pizza-pocket** | dough slicex1, cheese wedgex1, 任意食物x1 | \improper Pizza-pocket |
| **Honk-Pocket** | dough slicex1, 任意食物x1, sugarx3 | \improper Honk-pocket |
| **Berry-pocket** | dough slicex1, 任意食物x1 | \improper Berry-pocket |
| **Gondola-pocket** | dough slicex1, meatballx1, gondola mutation toxinx5 | \improper Gondola-pocket |
| **Deluxe Donk-pocket** | dough slicex1, meatballx1, piece of baconx1, 任意食物x1 | \improper Donk-pocket Deluxe |
| **Deluxe Meat-pocket** | heartx1, meatballx1, meatx1, 任意食物x1 | \improper Meat-pocket |
| **Deluxe Donk-roll** | dough slicex1, boiled ricex1, 任意食物x1, tofux2 | \improper Donk-roll |
| **Muffin** | milkx5, pastry basex1 | muffin |
| **Berry muffin** | milkx5, pastry basex1, 任意食物x1 | berry muffin |
| **Booberry muffin** | milkx5, pastry basex1, 任意食物x1, ectoplasmx1 | booberry muffin |
| **Khachapuri** | eggyolkx2, eggwhitex4, cheese wedgex1, breadx1 | khachapuri |
| **Sugar cookie** | sugarx5, pastry basex1 | sugar cookie |
| **Skull cookie** | pastry basex1, sugarx5, milkx5 | sugar cookie |
| **Coffin cookie** | pastry basex1, sugarx5, coffeex5 | sugar cookie |
| **Fortune cookie** | pastry basex1, paperx1 | fortune cookie |
| **Poppy pretzel** | poppyx1, pastry basex1 | poppy pretzel |
| **Plumphelmet biscuit** | pastry basex1, 任意食物x1 | plump helmet biscuit |
| **Cracker** | saltx1, dough slicex1 | cracker |
| **Choco cornet** | saltx1, pastry basex1, chocolate barx1 | chocolate cornet |
| **Oatmeal cookie** | pastry basex1, 任意食物x1 | oatmeal cookie |
| **Raisin cookie** | \improper 4no raisinsx1, pastry basex1, 任意食物x1 | raisin cookie |
| **Cherry cupcake** | pastry basex1, 任意食物x1 | cherry cupcake |
| **Blue cherry cupcake** | pastry basex1, 任意食物x1 | blue cherry cupcake |
| **Jupiter-cup-cake** | pastry basex1, 任意食物x1, caramelx3 | jupiter-cup-cake |
| **Honey bun** | pastry basex1, honeyx5 | honey bun |
| **Cannoli** | pastry basex1, milkx1, sugarx3 | cannoli |
| **Peanut butter cookie** | peanut butterx5, pastry basex1 | peanut butter cookie |
| **Raw brownie batter** | flourx5, sugarx5, eggx2, cocox5, butter slicex1 | raw brownie batter |
| **Raw peanut butter brownie batter** | flourx5, sugarx5, eggx2, cocox5, peanut butterx5, butter slicex1 | raw peanut butter brownie batter |
| **Crunchy peanut butter tart** | pastry basex1, peanut butterx5, 任意食物x1, creamx5 | crunchy peanut butter tart |
| **Chocolate chip cookie** | pastry basex1, chocolate barx1 | chocolate chip cookie |
| **Snickerdoodle** | pastry basex1, vanillax5 | snickerdoodle |
| **Thumbprint cookie** | pastry basex1, cherryjellyx5 | thumbprint cookie |
| **Macaron** | eggwhitex2, creamx5, flourx5 | macaron |
| **Apple fritter** | pastry basex1, apple slicex1 | apple fritter |

## 8.14 派类（24 种）

| 食物 | 材料需求 | 成品 |
|---|---|---|
| **Banana cream pie** | milkx5, plain piex1, 任意食物x1 | banana cream pie |
| **Meat pie** | blackpepperx1, saltx1, plain piex1, steakx1 | meat-pie |
| **Tofu pie** | plain piex1, tofux1 | tofu-pie |
| **Xeno pie** | plain piex1, xeno cutletx1 | xeno-pie |
| **Cherry pie** | plain piex1, 任意食物x1 | cherry pie |
| **Berry clafoutis** | plain piex1, 任意食物x1 | berry clafoutis |
| **Beary Pie** | plain piex1, 任意食物x1, bear steakx1 | beary pie |
| **Amanita pie** | plain piex1, 任意食物x1 | amanita pie |
| **Plump pie** | plain piex1, 任意食物x1 | plump pie |
| **Apple pie** | plain piex1, 任意食物x1 | apple pie |
| **Pumpkin pie** | milkx5, sugarx5, plain piex1, 任意食物x1 | pumpkin pie |
| **Golden apple tart** | milkx5, sugarx5, plain piex1, 任意食物x1 | golden apple streusel tart |
| **Grape tart** | milkx5, sugarx5, plain piex1, 任意食物x3 | grape tart |
| **Mime tart** | milkx5, sugarx5, plain piex1, nothingx5 | mime tart |
| **Berry tart** | milkx5, sugarx5, plain piex1, 任意食物x3 | berry tart |
| **Chocolate Lava tart** | milkx5, sugarx5, plain piex1, chocolate barx3, slime extractx1 | chocolate lava tart |
| **Blumpkin pie** | milkx5, sugarx5, plain piex1, 任意食物x1 | blumpkin pie |
| **Dulce de batata** | vanillax5, waterx5, 任意食物x2 | dulce de batata |
| **Frosty pie** | plain piex1, 任意食物x1 | frosty pie |
| **Baklava pie** | butter slicex2, tortillax4, oatx4 | baklava |
| **French silk pie** | sugarx5, plain piex1, chocolate barx2 | french silk pie |
| **Shepherds pie** | mashed potatoesx1, cutletx3, 任意食物x1, 任意食物x1, 任意食物x1, 任意食物x1 | shepherds pie |
| **Pie-flavored pie** | plain piex2 | pie-flavored pie |
| **battery acid pie** | plain piex1, 电池x2 | battery acid pie |

## 8.15 披萨类（13 种）

| 食物 | 材料需求 | 成品 |
|---|---|---|
| **raw pizza margherita** | flat doughx1, cheese wedgex4, 任意食物x1 | raw pizza margherita |
| **raw meatpizza** | flat doughx1, raw cutletx4, cheese wedgex1, 任意食物x1 | raw meatpizza |
| **raw Arnold pizza** | flat doughx1, raw cutletx3, 9mm子弹(Arnold披萨梗)x8, cheese wedgex1, 任意食物x1 | raw Arnold pizza |
| **raw mushroom pizza** | flat doughx1, 任意食物x5, cheese wedgex1 | raw mushroom pizza |
| **raw vegetable pizza** | flat doughx1, 任意食物x1, 任意食物x1, 任意食物x1, 任意食物x1, cheese wedgex1 | raw vegetable pizza |
| **raw donkpocket pizza** | flat doughx1, \improper Donk-pocketx3, cheese wedgex1, 任意食物x1 | raw donkpocket pizza |
| **raw dank pizza** | flat doughx1, 任意食物x3, cheese wedgex1, 任意食物x1 | raw dank pizza |
| **raw sassysage pizza** | flat doughx1, raw meatballx3, cheese wedgex1, 任意食物x1 | raw sassysage pizza |
| **raw Hawaiian pizza** | flat doughx1, raw cutletx2, pineapple slicex3, cheese wedgex1, 任意食物x1 | raw Hawaiian pizza |
| **\improper Ant Party pizza slice** | margherita slicex1, 蚂蚁x4 | \improper Ant Party pizza slice |
| **raw energy pizza** | flat doughx1, 电池x2 | raw energy pizza |
| **Meat calzone** | flat doughx1, cheese wedgex2, raw cutletx3, 任意食物x1 | raw meat calzone |
| **Vegetarian calzone** | flat doughx1, 任意食物x1, 任意食物x1, 任意食物x1 | raw vegetarian calzone |

## 8.16 沙拉类（15 种）

| 食物 | 材料需求 | 成品 |
|---|---|---|
| **Herb salad** | 碗x1, 任意食物x3, 任意食物x1 | herb salad |
| **Aesir salad** | 碗x1, 任意食物x3, 任意食物x1 | \improper Aesir salad |
| **Valid salad** | 碗x1, 任意食物x3, 任意食物x1, meatballx1 | valid salad |
| **melon fruit bowl** | 任意食物x1, 任意食物x1, 任意食物x1, 任意食物x1, 任意食物x1, 任意食物x1 | melon fruit bowl |
| **Fruit salad** | 碗x1, 任意食物x1, 任意食物x1, 任意食物x1, watermelon slicex2 | fruit salad |
| **Jungle salad** | 碗x1, 任意食物x2, 任意食物x2, 任意食物x2, watermelon slicex2 | jungle salad |
| **Citrus delight** | 碗x1, 任意食物x1, 任意食物x1, 任意食物x1 | citrus delight |
| **Salad of Eden** | 碗x1, 任意食物x1, 任意食物x1, 任意食物x1, 任意食物x1 | \improper Salad of Eden |
| **Kale salad** | 碗x1, 任意食物x1, 任意食物x2, 任意食物x1, olivex2 | kale salad |
| **Greek salad** | 碗x1, 任意食物x1, 任意食物x1, 任意食物x2, cheese wedgex1, olivex5, 任意食物x1 | greek salad |
| **Caesar salad** | 碗x1, 任意食物x2, 任意食物x1, cheese wedgex1, olivex5, bread slicex1 | caesar salad |
| **Spring salad** | 碗x1, 任意食物x2, 任意食物x1, 任意食物x1, olivex5 | spring salad |
| **Potato salad** | 碗x1, 任意食物x2, boiled eggx2, 任意食物x1, mayonnaisex5 | potato salad |
| **Spinach fruit salad** | 碗x1, 任意食物x3, 任意食物x2, pineapple slicex2, olivex2 | spinach fruit salad |
| **Antipasto salad** | 碗x1, 任意食物x2, 任意食物x1, 任意食物x1, cutletx1, mozzarella cheesex1 | antipasto salad |

## 8.17 三明治（13 种）

| 食物 | 材料需求 | 成品 |
|---|---|---|
| **Sandwich** | bread slicex2, 任意食物x1, steakx1, cheese wedgex1 | sandwich |
| **Cheese sandwich** | bread slicex2, cheese wedgex2 | cheese sandwich |
| **Jelly sandwich** | slimejellyx5, bread slicex2 | jelly sandwich |
| **Jelly sandwich** | cherryjellyx5, bread slicex2 | jelly sandwich |
| **Not a sandwich** | bread slicex2, fakemoustachex1 | not-a-sandwich |
| **Hot dog** | ketchupx5, bunx1, sausagex1 | hotdog |
| **Danish hot dog** | ketchupx5, bunx1, sausagex1, picklex1, 任意食物x1 | danish hotdog |
| **BLT** | bread slicex2, piece of baconx2, 任意食物x1, 任意食物x1 | \improper BLT |
| **Peanut butter and jelly sandwich** | bread slicex2, peanut butterx5, cherryjellyx5 | peanut butter and jelly sandwich |
| **Peanut butter and banana sandwich** | bread slicex2, peanut butterx5, 任意食物x1 | peanut butter and banana sandwich |
| **Philly Cheesesteak** | bread slicex2, cutletx2, cheese wedgex1, 任意食物x1 | philly cheesesteak |
| **Death Sandwich** | bread slicex2, salamix4, meatballx4, 任意食物x1 | death sandwich |
| **Toast Sandwich** | bread slicex2, buttered toastx1 | toast sandwich |

## 8.18 海鲜（13 种）

| 食物 | 材料需求 | 成品 |
|---|---|---|
| **Cuban carp** | flourx5, 任意食物x1, carp filletx1 | \improper Cuban carp |
| **Fish and chips** | space friesx1, fish filletx1 | fish and chips |
| **Fish fingers** | flourx5, bunx1, fish filletx1 | fish fingers |
| **Fish fry** | 任意食物x1, 任意食物x1, fish filletx1 | fish fry |
| **Spider Sashimi** | soysaucex5, spider eggsx1, fish filletx1 | spider sashimi |
| **Fish taco** | tortillax1, cheese wedgex1, fish filletx1, 任意食物x1 | fish taco |
| **Vegetarian sushi roll** | seaweed sheetx1, boiled ricex1, 任意食物x1, 任意食物x1 | vegetarian sushi roll |
| **Spicy filet sushi roll** | seaweed sheetx1, boiled ricex1, fish filletx1, 任意食物x1, 任意食物x1 | spicy filet sushi roll |
| **Nigiri sushi** | seaweed sheetx1, boiled ricex1, fish filletx1, soysaucex2 | nigiri sushi |
| **meat poke** | 碗x1, seaweed sheetx1, boiled ricex1, cutletx3, 任意食物x1, tofux1, soysaucex1, 任意食物x1 | meat poke |
| **fish poke** | 碗x1, seaweed sheetx1, boiled ricex1, fish filletx1, 任意食物x1, tofux1, soysaucex1, 任意食物x1 | fish poke |
| **futomaki sushi roll** | seaweed sheetx1, boiled ricex1, boiled eggx1, fish filletx1, 任意食物x1 | futomaki sushi roll |
| **philadelphia sushi roll** | seaweed sheetx1, boiled ricex1, cheese wedgex1, fish filletx1, 任意食物x1 | philadelphia sushi roll |

## 8.19 意面（15 种）

| 食物 | 材料需求 | 成品 |
|---|---|---|
| **Tomato pasta** | boiled spaghettix1, 任意食物x2 | spaghetti |
| **Copypasta** | spaghettix2 | copypasta |
| **Spaghetti meatball** | boiled spaghettix1, meatballx2 | spaghetti and meatballs |
| **Spesslaw** | boiled spaghettix1, meatballx4 | spesslaw |
| **Beef noodle** | 碗x1, boiled spaghettix1, cutletx2, 任意食物x1 | beef noodle |
| **Chowmein** | boiled spaghettix1, cutletx1, 任意食物x2, 任意食物x1 | chow mein |
| **Butter Noodles** | boiled spaghettix1, butter slicex1 | butter noodles |
| **Mac n' cheese** | boiled spaghettix1, béchamel saucex1, cheese wedgex2, bread slicex1, blackpepperx2 | mac n' cheese |
| **Shoyu Tonkotsu ramen** | 碗x1, boiled spaghettix1, boiled eggx1, seaweed sheetx1, cutletx1, 任意食物x1 | shoyu tonkotsu ramen |
| **Kitakata ramen** | 碗x1, boiled spaghettix1, cutletx2, 任意食物x1, 任意食物x1, 任意食物x1 | kitakata ramen |
| **Kitsune udon** | 碗x1, boiled spaghettix1, tofux2, 任意食物x1, soysaucex5, sugarx5 | kitsune udon |
| **Nikujaga** | 碗x1, boiled spaghettix1, cutletx2, 任意食物x1, 任意食物x1, 任意食物x1 | nikujaga |
| **Pho** | 碗x1, boiled spaghettix1, cutletx1, 任意食物x1, 任意食物x1 | pho |
| **Pad thai** | 碗x1, boiled spaghettix1, tofux1, 任意食物x1, 任意食物x1, 任意食物x1 | pad thai |
| **Spaghetti Carbonara** | boiled spaghettix1, firm cheese slicex1, piece of baconx1, eggx1, blackpepperx2 | spaghetti carbonara |

---

# 第九卷 · 食物试剂效果

**代码**: `reagents/food_reagents.dm`（1,393 行，81 种定义）→ 自化学总章迁移

> 食物试剂的营养/效果层。`nutriment_factor` = 每单位提供的营养值（决定饱腹度）。`on_mob_life` = 体内代谢效果。

## 9.1 营养因子排行

| 试剂 | nutriment_factor | 说明 |
|---|---|---|
| **布料纤维 Cloth Fibers** | 30 | 不是真营养但能喂饱蛾族 |
| **脂肪 Fat** | 18 | 热量最高（油/肥肉） |
| **蜂蜜 Honey** | 15 | 含抗菌成分，会分解为糖 |
| **营养 Nutriment** | 15 | 通用营养（碳水+维生素） |
| **稳定营养 Stabilized Nutriment** | 15 | 生物工程蛋白 |
| **花生酱 Peanut Butter** | 15 | 花生研磨 |
| **橄榄油 Olive Oil** | 10 | 高品质油 |
| **樱桃果酱 Cherry Jelly** | 10 | 只配涂在带樱桃的食物上 |
| **焦糖 Caramel** | 10 | 加热糖 |
| **肽 Peptides** | 10 | 恢复性肽（33% 少增重） |
| **蛋白质 Protein** | 9 | 氨基酸聚合物 |
| **蛋黄 Egg Yolk** | 8 | 高蛋白 |
| **植物油 Vegetable Oil** | 7 | 植物脂肪 |
| **番茄酱 Ketchup** | 5 | 番茄膏 |
| **芥末 Mustard** | 5 | 辣芥 |
| **可可粉 Coco Powder** | 5 | 可可豆 |
| **玉米油 Corn Oil** | 5 | 廉价油 |
| **烧烤酱 BBQ Sauce** | 5 | 甜烟熏 |
| **炭 Char** | 5 | 烧烤精华（有特殊性质） |
| **液体电 Liquid Electricity** | 5 | 精灵族血液 |
| **Korta 花蜜** | 5 | 蜥蜴族甜浆 |
| **蛋清 Egg White** | 4 | 更多蛋白 |
| **生奶油 Whipped Cream** | 4 | 打发奶油 |
| **米 Rice** | 3 | 营养谷物 |
| **糖 Sugar** | 2 | 蔗糖 |
| **大豆酱 Soysauce** | 2 | 咸酱 |
| **病毒食物 Virus Food** | 2 | 水+奶混合（病毒培养基） |
| **酸奶 Yoghurt** | 2 | 奶油发酵 |
| **秘密酱 Secret Sauce** | 2 | 不明配方 |
| **咖啡伴侣 Coffee Creamer** | 1.5 | 廉价奶粉 |
| **Gizmo Goop** | 0.5 | 灰色糊糊（"据说有营养"） |
| **Astrotame** | 0 | 零卡代糖 |
| **Molt'Obeso** | 0 | **增食欲酱**（刺激饥饿+提高食物热量吸收） |

## 9.2 食物试剂特殊效果（on_mob_life）

| 试剂 | 效果 |
|---|---|
| **维生素 Vitamin** | 体内恢复（维生素类） |
| **辣椒油 Capsaicin Oil** | 辣（辣椒素） |
| **冰霜油 Frost Oil** | 显著降温 |
| **浓缩辣椒素 Condensed Capsaicin** | 自卫/警察用 |
| **食盐 Table Salt** | 调味 |
| **洋葱泪 Tear Juice** | 致盲（切洋葱梗） |
| **糖霜 Sprinkles** | 甜甜圈上的彩色糖粒 |
| **监狱剩饭 Nutraslop** | 前日剩饭混合物 |
| **地狱拉面 Hell Ramen** | 辣面 |
| **面粉 Flour** | 抹身上装鬼 |
| **玉米淀粉 Corn Starch** | 滑溜溶液 |
| **玉米糖浆 Corn Syrup** | 分解为糖 |
| **蜂蜜 Honey** | 抗菌+分解为糖 |
| **Molt'Obeso** | 增食欲+热量吸收 |
| **稳定营养** | 缓释营养 |
| **熵多酚 Entropic Polypnium** | 蘑菇萃取（味道差） |
| **Tinea Luxor** | 刺激发光真菌生长 |
| **Vitrium 泡沫** | 愈伤皮肤 |
| **富集液态电** | 精灵族强化 |
| **焦糖** | 甜味 |
| **花生酱** | 饱腹 |
| **薄荷提取物** | 对付讨厌顾客 |
| **克拉克拉奶昔** | — | 

---

# 附录A · 代码路径索引

| 文件 | 行数 | 内容 |
|---|---|---|
| `code/modules/food_and_drinks/recipes/drinks/drinks_alcoholic.dm` | 785 | 酒精鸡尾酒 170 配方 |
| `code/modules/food_and_drinks/recipes/drinks/drinks_non-alcoholic.dm` | 185 | 无酒精饮料 43 配方 |
| `code/modules/food_and_drinks/recipes/drinks/drinks_lizard.dm` | 30 | 蜥蜴族饮品 7 配方 |
| `code/modules/food_and_drinks/recipes/drinks/drinks_moth.dm` | 32 | 蛾族饮品 8 配方 |
| `code/modules/food_and_drinks/recipes/food_mixtures.dm` | 362 | 食物混合 48 配方 |
| `code/modules/food_and_drinks/recipes/tablecraft/` | 7,820 | **实体食物桌台合成 571 配方（19 文件）** |
| `code/modules/food_and_drinks/recipes/tablecraft/recipes_guide.dm` | 968 | 非合成指引 173 条（未纳入） |
| `code/modules/food_and_drinks/recipes/processor_recipes.dm` | — | 处理器配方（榨汁等） |
| `code/modules/reagents/chemistry/reagents/food_reagents.dm` | 1,393 | 食物试剂 81 种定义（营养+效果） |
| `code/modules/reagents/chemistry/reagents/drinks/drink_reagents.dm` | 1,501 | 饮料试剂 95 种定义（56 种有效果） |
| `code/modules/reagents/chemistry/reagents/drinks/alcohol_reagents.dm` | — | 酒精试剂 200+ 种定义（198 种有效果） |

> **文档完** — 基于 TianGuan13 源码提取
> 覆盖: 847 配方（饮品 276 + 桌台食物 571）+ 668 试剂 | 输出: 1 篇
