# 天关水培种植指南 — 附录：植物数据库

> **代码**: `code/modules/hydroponics/grown/` (45+ 文件, 4,747 行)
> 本文档补充上一份指南中缺失的具体作物数据

---

## A. 完整突变链

```
【水果类】
苹果 (Apple) ──→ 金苹果 (Golden Apple)
香蕉 (Banana) ──→ 米玛娜 (Mimana) ──→ 蓝空间香蕉 (Bluespace Banana)
浆果 (Berry) ──→ 毒浆果 (Poison Berry) ──→ 死亡浆果 (Death Berry)
             └──→ 发光浆果 (Glow Berry)
蓝番茄 (Blue Tomato) ──→ 蓝空间番茄 (Bluespace Tomato)
番茄 (Tomato) ──→ 血番茄 (Blood Tomato) ──→ 杀手番茄 (Killer Tomato)
             └──→ 蓝番茄 (Blue Tomato)
青柠 (Lime) ──→ 橙子 (Orange) ──→ 3D 橙子 (Extradimensional Orange)
                              └──→ 青柠 (Lime)
柠檬 (Lemon) ──→ 可燃柠檬 (Combustible Lemon)
葡萄 (Grape) ──→ 绿葡萄 (Green Grape)
西瓜 (Watermelon) ──→ 圣灵瓜 (Holymelon)
                 └──→ 桶瓜 (Barrelmelon)

【蔬菜类】
胡萝卜 (Carrot) ──→ 欧洲萝卜 (Parsnip)
              └──→ 坎恩根 (Cahn'root)
白甜菜 (White Beet) ──→ 红甜菜 (Red Beet)
辣椒 (Chili) ──→ 冰辣椒 (Chilly Pepper)
           └──→ 鬼椒 (Ghost Chili)
玉米 (Corn) ──→ 爆裂玉米 (Snapcorn)
          └──→ 胡椒玉米 (Peppercorn)
大豆 (Soybean) ──→ 锦鲤豆 (Koibean)
             └──→ 黄油豆 (Butterbean)
青豆 (Green Bean) ──→ 跳豆 (Jumping Bean)

【花卉类】
罂粟 (Poppy) ──→ 天空葵 (Geranium) ──→ 白鲜 (Fraxinella)
           └──→ 百合 (Lily) ──→ 太空人喇叭 (Spaceman's Trumpet)
向日葵 (Sunflower) ──→ 月花 (Moonflower)
                 └──→ 新星花 (Novaflower)

【药草类】
Ambrosia Vulgaris ──→ Ambrosia Deus ──→ Ambrosia Gaia

【蘑菇类】
圆胖菇 (Plump Helmet) ──→ 行走蘑菇 (Walking Mushroom)
毒蝇伞 (Fly Amanita) ──→ 毁灭天使 (Destroying Angel)
鸡油菌 (Chanterelle) ──→ 朱庇特杯 (Jupiter Cup)
荧光菇 (Glowshroom) ──→ 荧光帽 (Glowcap)
                     └──→ 暗影菇 (Shadowshroom)

【茶叶/咖啡类】
茶树 (Tea Aspera) ──→ 茶星 (Tea Astra)
咖啡 (Coffee Arabica) ──→ 咖啡罗布斯塔 (Coffee Robusta)

【可卡/香草类】
可可 (Cocoa Pod) ──→ 香草 (Vanilla Pod)
               └──→ 文果 (Bungo Fruit)

【烟草类】
烟草 (Tobacco) ──→ 太空烟草 (Space Tobacco)

【大麻类】
大麻 (Cannabis) ──→ 彩虹大麻 (Rainbow Weed)
              ├──→ 死亡大麻 (Deathweed)
              ├──→ 生命草 (Lifeweed)
              ├──→ Ω大麻 (Omega Weed)
              └──→ 反大麻 (Anti Weed)
```

---

## B. 完整植物数据表

> **属性解读**: lifespan(寿命) / endurance(生命值) / maturation(成熟周期) / production(生产周期) / yield(产量) / potency(效力) / instability(不稳定度)
> **空 = 默认值**: lifespan=25, endurance=15, maturation=6, production=6, yield=3, potency=10, instability=5
> **juice** = 榨汁获得的试剂; **distill** = 酿造获得的酒/试剂; **wine_power** = 果酒烈度

### B-1 水果类

| 植物 | 基础属性 | 所含化学物 (配方) | Juice | Distill | 备注 |
|---|---|---|---|---|---|
| **苹果** (Apple) | 55/35/-/-/5/-/- | 维生素 4%, 营养 10% | 苹果汁 | 苹果酒 (硬 cider) `/hcider` | 刀切成5片; 砸科学家脑袋有彩蛋 |
| **金苹果** (Golden Apple) | -/-/10/10/-/-/- | 金 20%, 维生素 4%, 营养 10% | — | 不酿(`null`), wine_power=50 | 稀有度40, 炼金材料 |
| **香蕉** (Banana) | 50/30/-/-/-/10 | 香蕉 10%, 钾 10%, 维生素 4%, 营养 2% | 香蕉汁 `banana` | 香蕉酒 `bananahonk` | 自带滑倒基因, 小丑最爱, 皮可磨粉 |
| **米玛娜** (Mimana) | 继承 | 虚无 10%, 致哑毒素 10%, 营养 2% | — | 静默酒 `silencer` | 透明图标, 稀有15 |
| **蓝空间香蕉** | -/-/-/-/-/40 | 蓝空间 20%, 香蕉 10%, 维生素 4%, 营养 2%, 暗物质 20% | — | wine_power=60 | 自带传送+滑倒, 稀有30 |
| **浆果** (Berry) | 20/-/5/5/2/-/30 | 维生素 4%, 营养 10% | 浆果汁 `berryjuice` | 杜松子酒 `gin` | 高不稳定度 |
| **毒浆果** (Poison Berry) | 继承 | 氰化物 15%, 疲劳毒素 20%, 维生素 4%, 营养 10% | 毒浆果汁 `poisonberryjuice` | 不酿, wp=35 | 稀有10, 3口量 |
| **死亡浆果** (Death Berry) | 30/-/-/-/-/50/- | 毒芹碱 8%, 疲劳毒素 10%, 维生素 4%, 营养 10% | 毒浆果汁 | 不酿, wp=50 | 稀有30, 接穗传递抗毒 |
| **发光浆果** (Glow Berry) | 30/25/-/-/-/-/- | 铀 25%, 碘 20%, 维生素 4%, 营养 10% | — | 不酿, wp=60 | 白色发光基因, 稀有 |
| **番茄** (Tomato) | -/-/8/-/-/-/25 | 维生素 4%, 营养 10% | 番茄汁 `tomatojuice` | 酶 `enzyme` | 自带压碎, 研磨→番茄酱 |
| **血番茄** (Blood Tomato) | 继承 | 血 20%, 维生素 4%, 营养 10% | — | 血腥玛丽 `bloody_mary` | 3口量, 稀有, 砸到出内脏 |
| **蓝番茄** (Blue Tomato) | -/-/-/-/2/-/- | 润滑油 20%, 维生素 4%, 营养 10% | — | 欢笑 `laughter` | 自带滑倒, 2口量 |
| **蓝空间番茄** | 继承 yield=2 | 润滑油 20%, 蓝空间 20%, 维生素 4%, 营养 10% | — | 不酿, wp=80 | 压碎+滑倒+传送, 稀有50 |
| **杀手番茄** | -/-/-/-/2/-/- | (无试剂配方, mob_transformation) | — | — | 变成杀手番茄怪! |
| **青柠** (Lime) | 55/50/-/-/4/15 | 维生素 4%, 营养 5% | 青柠汁 `limejuice` | — | |
| **橙子** (Orange) | 60/50/-/-/5/20 | 维生素 4%, 营养 5% | 橙汁 `orangejuice` | 三重秒 `triple_sec` | |
| **3D 橙子** | 60/50/-/-/5/20/64 | 维生素 4%, 营养 5%, 氟哌啶醇 15% | 橙汁 | 迷失心智 `mindbreaker` | 2口量, 味道=多边形/蓝空间/真实 |
| **柠檬** (Lemon) | 55/45/-/-/4/- | 维生素 4%, 营养 5% | 柠檬汁 `lemonjuice` | — | |
| **可燃柠檬** | 55/45/-/-/4/- | 营养 5%, 燃料 5% | — | wine_power=70 | 自带爆炸植物基因! |
| **葡萄** (Grape) | 50/25/-/-/4/10 | 维生素 4%, 营养 10% | 葡萄汁 `grapejuice` | 葡萄酒 `wine` | |
| **西瓜** (Watermelon) | 50/40/-/-/-/-/20 | 水 20%, 维生素 4%, 营养 20% | 西瓜汁 `watermelonjuice` | wp=40 | 勺子挖出果肉, 可做成头盔/胸甲 |
| **圣灵瓜** (Holymelon) | 继承 | 圣水 20%, 维生素 4%, 营养 10% | 圣水 `holywater` | wp=70, 味道="神性" | 发光+反魔法(次数=potency/20), 稀有 |
| **桶瓜** (Barrelmelon) | 继承 | 麦芽酒 20%, 营养 10% | — | 解酒剂 `antihol` | 自带酿酒基因, 稀有10 |
| **菠萝** (Pineapple) | 50/30/-/6/4/-/20 | 维生素 4%, 营养 20% | 菠萝汁 `pineapplejuice` | wp=40 | |

### B-2 蔬菜/根茎类

| 植物 | 基础属性 | 所含化学物 | Juice | Distill | 备注 |
|---|---|---|---|---|---|
| **胡萝卜** (Carrot) | -/-/10/1/5/-/15 | 眼药水 10%, 维生素 4%, 营养 5% | 胡萝卜汁 `carrotjuice` | wp=30 | 喜土, 可磨成胡萝卜剑 |
| **欧洲萝卜** (Parsnip) | 继承 | 维生素 5%, 营养 5%, 铝 5% | 欧洲萝卜汁 | wp=35 | 可磨成军刀 |
| **坎恩根** (Cahn'root) | -/50/-/-/-/-/10 | 维生素 5%, 营养 5%, 纤维素 1%, 糖 1% | null | 根汁汽水 `rootbeer` | 喜土+杂草适应, 稀有10 |
| **白甜菜** (White Beet) | 60/50/-/-/6/-/10 | 维生素 4%, 糖 20%, 营养 5% | — | wp=40 | 喜土 |
| **红甜菜** (Red Beet) | 60/50/-/-/5/-/15 | 维生素 5%, 营养 5% | — | wp=60 | 喜土+maxchem基因 |
| **土豆** (Potato) | 50/10/10/1/4/-/55 | 营养 10%, 维生素 4% | 土豆汁 `potatojuice` | 伏特加 `vodka` | 高不稳定55 |
| **洋葱** (Onion) | 20/25/3/3/3/10/20 | 维生素 4%, 营养 10% | — | wp=30 | |
| **玉米** (Corn) | -/-/8/-/-/20/50 | 玉米油 20%, 维生素 4%, 营养 10% | 玉米淀粉 `corn_starch` | 威士忌 `whiskey` | 高不稳定50, 可做爆米花/玉米芯烟斗 |
| **爆裂玉米** (Snapcorn) | 继承 | — | — | — | 可一瓣瓣掰下当摔炮, 稀有10 |
| **胡椒玉米** (Peppercorn) | 继承 | 黑胡椒 20%, 维生素 4%, 营养 10% | — | — | 研磨→黑胡椒 |
| **茄子** (Eggplant) | 25/20/6/-/2/20/20 | 维生素 4%, 营养 10% | — | wp=40 | |
| **辣椒** (Chili) | 20/-/5/5/4/20/30 | 辣椒素 25%, 维生素 4%, 营养 4% | — | wp=20 | |
| **冰辣椒** (Chilly Pepper) | 25/-/4/4/-/-/- | 霜油 25%, 维生素 2%, 营养 2% | — | wp=30 | 自带化学冷却基因, 稀有色 |
| **鬼椒** (Ghost Chili) | -/10/10/10/3/-/- | 浓缩辣椒素 30%, 辣椒素 55%, 营养 4% | — | wp=50 | 自带化学加热基因 |
| **灯笼椒** (Bell Pepper) | -/10/10/10/3/-/- | 维生素 8%, 营养 4% | — | — | 可烘焙, 稀有色 |
| **黄瓜** (Cucumber) | 20/20/6/-/-/-/10 | 维生素 4%, 营养 10% | — | — | |
| **豌豆** (Peas) | -/-/6/1/3/10/5 | 维生素 4%, 营养 10% | — | wp=25 | |
| **花生** (Peanut) | 30/20/6/6/3/-/5 | 维生素 4%, 营养 10% | 花生酱 ` peanut_oil` | — | |
| **蒜** (Garlic) | 25/20/10/5/4/-/10 | 营养 5% | — | — | |
| **芦荟** (Aloe) | 20/10/-/2/-/-/10 | — | — | — | |

### B-3 豆类

| 植物 | 基础属性 | 所含化学物 | Juice | Distill | 备注 |
|---|---|---|---|---|---|
| **大豆** (Soybean) | -/-/4/4/-/15 | 维生素 4%, 营养 5%, 植物油 3% | 豆浆 `soymilk` | 酱油 `soysauce` | |
| **锦鲤豆** (Koibean) | 继承 potency=10 | 鲤鱼毒素 10%, 维生素 4%, 营养 5% | — | wp=40 | 稀有, 可捏成仿鲤鱼片 |
| **黄油豆** (Butterbean) | 继承 potency=10 | 牛奶 5%, 维生素 4%, 奶油 5% | — | 酸奶 `yoghurt` | 稀有20, 可捏成黄油片 |
| **青豆** (Green Bean) | -/-/4/3/-/10/0 | 维生素 4%, 多韦 4% | — | — | 永不突变基因, 健康食品 |
| **跳豆** (Jumping Bean) | -/-/8/4/2/20/18 | 营养 5%, 蚂蚁 10% | — | — | 稳定属性基因, 稀有 |

### B-4 蘑菇类

| 植物 | 基础属性 | 所含化学物 | Distill | 备注 |
|---|---|---|---|---|
| **圆胖菇** (Plump Helmet) | -/-/8/1/4/15 | 维生素 4%, 营养 10% | 矮人烈酒 `manly_dorf` | 真菌代谢 |
| **行走蘑菇** | 30/30/5/-/1/- | 维生素 5%, 营养 15% | 不酿(`FALSE`) | 稀有30, 变成行走蘑菇怪! |
| **鸡油菌** (Chanterelle) | 35/20/7/1/5/15/20 | 营养 10% | — | 真菌代谢, potency≥95可做法师帽 |
| **朱庇特杯** (Jupiter Cup) | 40/-/4/-/4/8 | 营养 10%, 液态电 固有基因 | — | 食肉, 食虫 |
| **灵芝** (Reishi) | 35/35/10/5/4/15/30 | 吗啡 35%, 多韦 35%, 营养 0 | — | 药用真菌 |
| **毒蝇伞** (Fly Amanita) | 50/35/10/5/4/-/30 | 致幻蘑菇素 4%, 鹅膏毒素 35%, 营养 0, 生长血清 10% | — | 高毒 |
| **毁灭天使** | 50/35/12/5/2/35 | 致幻蘑菇素 4%, 鹅膏毒素 10%, 营养 0, α-鹅膏蕈碱 20% | wp=60 | 稀有30, 剧毒 |
| **自由帽** (Liberty Cap) | -/-/7/1/5/15/10 | 致幻蘑菇素 25%, 营养 2% | wp=80 | 高产快熟, 产量5 |
| **荧光菇** (Glowshroom) | 100/30/15/1/3/30/20 | 镭 10%, 磷 10%, 营养 4% | wp=50 | 发光, 寿命100, 稀有(中) |
| **荧光帽** (Glowcap) | 继承 | 特斯铢 10%, 营养 4% | — | 红色发光+电池充电, 稀有30 |
| **暗影菇** (Shadowshroom) | 继承 | 镭 20%, 营养 4% | wp=60 | 暗影发光, 稀有30 |
| **可憎 puffball** | -/-/3/8/-/30/65 | 孢子毒素 20%, 营养 4% | wp=50 | 烟雾+压碎基因, 稀有35, 不稳定65 |

### B-5 茶叶/咖啡/可可

| 植物 | 基础属性 | 所含化学物 | Juice/Distill | 备注 |
|---|---|---|---|---|
| **茶树 Aspera** (Tea) | 20/-/5/5/5 | 维生素 4%, 茶粉 10% | 干磨→茶粉, 不可酿酒 | 需干燥 |
| **茶星 Astra** | 子变体 | 合尼西丁 10%, 维生素 4%, 茶粉 10% | 磨粉→茶粉+盐水 | 稀有 |
| **咖啡 Arabica** | 30/20/5/5/5/20 | 维生素 4%, 咖啡粉 10%, 氮 5% | 磨粉→咖啡粉, 酿→咖啡酒 `kahlua` | 需干燥 |
| **咖啡 Robusta** | 子变体 | 麻黄碱 10%, 维生素 4%, 咖啡粉 10% | 磨粉→咖啡粉+吗啡 | 稀有 |
| **可可** (Cocoa Pod) | 20/-/5/5/2/20 | 可可 25%, 营养 10% | 酿→可可奶油酒 `creme_de_cacao` | |
| **香草** (Vanilla Pod) | 继承 | 香草 25%, 营养 10% | 酿→香草精 `vanilla` | |
| **文果** (Bungo Fruit) | 30/-/4/7/3 | 酶 10%, 营养 10% | 文果汁, 不酿 | 稀有15, 籽有毒(蜂毒素) |

### B-6 花卉类

| 植物 | 基础属性 | 所含化学物 | Distill | 备注 |
|---|---|---|---|---|
| **罂粟** (Poppy) | -/10/8/6/20/1 | 利比妥 20%, 营养 5% | 苦艾酒 `vermouth` | 极低不稳定1 (适合杂交降不稳定) |
| **百合** (Lily) | 子变体 | — | — | instability=1 |
| **太空人喇叭** | 80/10/12/5/4/20 | 营养 5%, 聚吡咯(固有试剂基因) | — | 稀有30 |
| **天空葵** (Geranium) | 子变体 | — | — | instability=1 |
| **白鲜** (Fraxinella) | 子变体 | 营养 5%, 燃油 5% | 灰烬 `ash` | 稀有15 |
| **风铃草** (Harebell) | 100/20/7/1/2/30/1 | 营养 4% | 苦艾酒 `vermouth` | 杂草适应, 低不稳定 |
| **向日葵** (Sunflower) | -/20/-/2/2 | 植物油 8%, 营养 4% | — | 可攻击(0伤害/火焰), 可晒干→瓜子 |
| **月花** (Moonflower) | 子变体 | 月光酒 20%, 维生素 2%, 营养 2% | 苦艾酒 `absinthe` | 紫色发光, 稀有15 |
| **新星花** (Novaflower) | 子变体 | 浓缩辣椒素 25%, 辣椒素 30%, 营养 0, 酸 5% | — | 稀有, 攻击灼伤, 不可食用 |

### B-7 药草/特殊

| 植物 | 基础属性 | 所含化学物 | Distill | 备注 |
|---|---|---|---|---|
| **Ambrosia Vulgaris** | 60/25/6/6 | 爱尤里 10%, 利比妥 10%, 太空毒品 15%, 维生素 4%, 营养 5%, 毒素 10% | wp=30 | 基础草药, 当帽子 |
| **Ambrosia Deus** | 子变体 | 万能药 15%, 合尼西丁 15%, 太空毒品 10%, 维生素 4%, 营养 5% | wp=50 | 稀有40 |
| **Ambrosia Gaia** | 子变体 | 大地之血 5%, 营养 6%, 维生素 5% | wp=70, 味道="大地母亲的祝福" | 罕见30, 高产杂草(weed_rate=4, 100%), 自带发光! |
| **烟草** (Tobacco) | 20/-/5/5/10 | 尼古丁 3%, 营养 3% | 薄荷酒 `creme_de_menthe` | 需干燥, 产量10 |
| **太空烟草** (Space Tobacco) | 子变体 | 沙丁胺醇 5%, 尼古丁 8%, 营养 3% | 不酿, wp=50 | 稀有 |
| **大麻** (Cannabis) | -/-/8/-/20/40 | THC 15% | wp=20 | 高不稳定40 |
| **彩虹大麻** | 子变体 | 彩虹试剂 5%, 心理健康素 3%, 快乐素 10%, 迷失心智 10%, 脂肪溶解素 15%, 太空毒品 15% | wp=60 | 稀有40 |
| **死亡大麻** | 子变体 | 氰化物 35%, THC 15% | wp=40 | 稀有40 |
| **生命草** (Lifeweed) | 子变体/30 | 万能药 35%, THC 15% | wp=10 | 稀有40 |
| **Ω大麻** (Omega Weed) | 子变体 | THC 30%, 迷失心智 30%, 汞 15%, 锂 15%, 阿托品 15%, 甲基苯丙胺 15%, 浴盐 15%, 克罗克 15%, 尼古丁 10% | wp=90 | 稀有69! 发光+大容量 |
| **反大麻** (Anti Weed) | 子变体/0 | 纳洛酮 30%, 解酒剂 20%, 苯海拉明 10% | — | 稀有40, 暗影发光, 图标倒置! |

### B-8 其他作物

| 植物 | 所含化学物 | 备注 |
|---|---|---|
| **棉花** (Cotton) | — | 纺织原料, 2种变体 |
| **甘蔗** (Sugarcane) | 糖 20% | 酿→朗姆酒 `rum` |
| **小麦** (Wheat) | 营养 10% | 酿→啤酒 `beer`, 磨→面粉 |
| **大米** (Rice) | 营养 10% | 做饭用 |
| **烟草** (Tobacco) | 见 B-7 | |
| **Korta Nut** | 营养 10% | Tizira 特产 |
| **Kronkus** | 营养 5% | 外星藤蔓 |
| **复制荚** (Replicapod) | 多种试剂 | 克隆船员! |
| **Gatfruit** | — | **长出来的不是水果是子弹!!!** |

---

## C. 按 Juice/Distill/Wine 检索

### 果汁类 (Juice)

| 植物 | 产出试剂 | 代码路径 |
|---|---|---|
| 苹果 | `applejuice` | `/datum/reagent/consumable/applejuice` |
| 香蕉 | `banana` | `/datum/reagent/consumable/banana` |
| 浆果 | `berryjuice` | `/datum/reagent/consumable/berryjuice` |
| 毒浆果 | `poisonberryjuice` | `/datum/reagent/consumable/poisonberryjuice` |
| 番茄 | `tomatojuice` | `/datum/reagent/consumable/tomatojuice` |
| 橙子 | `orangejuice` | `/datum/reagent/consumable/orangejuice` |
| 青柠 | `limejuice` | `/datum/reagent/consumable/limejuice` |
| 柠檬 | `lemonjuice` | `/datum/reagent/consumable/lemonjuice` |
| 葡萄 | `grapejuice` | `/datum/reagent/consumable/grapejuice` |
| 西瓜 | `watermelonjuice` | `/datum/reagent/consumable/watermelonjuice` |
| 圣灵瓜 | `holywater` | `/datum/reagent/water/holywater` |
| 胡萝卜 | `carrotjuice` | `/datum/reagent/consumable/carrotjuice` |
| 欧洲萝卜 | `parsnipjuice` | `/datum/reagent/consumable/parsnipjuice` |
| 玉米 | `corn_starch` | `/datum/reagent/consumable/corn_starch` |
| 大豆 | `soymilk` | `/datum/reagent/consumable/soymilk` |
| 菠萝 | `pineapplejuice` | `/datum/reagent/consumable/pineapplejuice` |
| 土豆 | `potatojuice` | `/datum/reagent/consumable/potatojuice` |
| 文果 | `bungojuice` | `/datum/reagent/consumable/bungojuice` |
| 3D 橙子 | `orangejuice` | (同橙汁) |

### 酿酒类 (Distill)

> 自动发酵(brew基因): 桶瓜自带 `brewing` — 收获时自动转化为麦芽酒
> 普通酿造: 放入发酵桶 (fermenting barrel)

| 植物产出 | 酿造产物 | 代码路径 | wine_power |
|---|---|---|---|
| 苹果 | 苹果硬酒 `hcider` | `/datum/reagent/consumable/ethanol/hcider` | — |
| 香蕉 | 香蕉 HONK 酒 | `/datum/reagent/consumable/ethanol/bananahonk` | — |
| 米玛娜 | 静默酒 `silencer` | `/datum/reagent/consumable/ethanol/silencer` | — |
| 浆果 | 杜松子酒 `gin` | `/datum/reagent/consumable/ethanol/gin` | — |
| 番茄 | 酶 `enzyme` | `/datum/reagent/consumable/enzyme` | — |
| 血番茄 | 血腥玛丽 | `/datum/reagent/consumable/ethanol/bloody_mary` | — |
| 蓝番茄 | 欢笑 | `/datum/reagent/consumable/laughter` | — |
| 橙子 | 三重秒 | `/datum/reagent/consumable/ethanol/triple_sec` | — |
| 3D 橙子 | 迷失心智 | `/datum/reagent/toxin/mindbreaker` | — |
| 葡萄 | 葡萄酒 `wine` | `/datum/reagent/consumable/ethanol/wine` | — |
| 月亮花 | 苦艾酒 `absinthe` | `/datum/reagent/consumable/ethanol/absinthe` | — |
| 罂粟 | 苦艾酒 `vermouth` | `/datum/reagent/consumable/ethanol/vermouth` | — |
| 风铃草 | 苦艾酒 `vermouth` | (同上) | — |
| 白鲜 | 灰烬 `ash` | `/datum/reagent/ash` | — |
| 圆胖菇 | 矮人烈酒 | `/datum/reagent/consumable/ethanol/manly_dorf` | — |
| 玉米 | 威士忌 `whiskey` | `/datum/reagent/consumable/ethanol/whiskey` | — |
| 土豆 | 伏特加 `vodka` | `/datum/reagent/consumable/ethanol/vodka` | — |
| 可可 | 可可奶油酒 | `/datum/reagent/consumable/ethanol/creme_de_cacao` | — |
| 香草 | 香草精 | `/datum/reagent/consumable/vanilla` | — |
| 咖啡 | 咖啡酒 `kahlua` | `/datum/reagent/consumable/ethanol/kahlua` | — |
| 烟草 | 薄荷酒 | `/datum/reagent/consumable/ethanol/creme_de_menthe` | — |
| 甘蔗 | 朗姆酒 `rum` | `/datum/reagent/consumable/ethanol/rum` | — |
| 小麦 | 啤酒 `beer` | `/datum/reagent/consumable/ethanol/beer` | — |
| 大豆 | 酱油 | `/datum/reagent/consumable/soysauce` | — |
| 黄油豆 | 酸奶 | `/datum/reagent/consumable/yoghurt` | — |
| 胡萝卜根 | 根汁汽水 | `/datum/reagent/consumable/rootbeer` | — |
| 桶瓜 | 解酒剂 `antihol` | `/datum/reagent/medicine/antihol` | — |
| 圣灵瓜 | *(无专用)* | — | 70 |

纯果酒 (无 distill_reagent 的植物): 生成 `/datum/reagent/consumable/ethanol/fruit_wine`, 配方根据植物名+颜色+口味自动生成, boozepwr = wine_power × 纯度 × 2

### 磨粉/加工产物

| 植物 | 研磨产物 |
|---|---|
| 玉米 | 玉米粉 + 玉米油 |
| 胡椒玉米 | 黑胡椒 |
| 番茄 | 番茄酱 |
| 血番茄 | 番茄酱 + 血 |
| 烟草/太空烟草 | 烟草粉 (干燥后) |
| 茶树 | 茶粉 |
| 咖啡 | 咖啡粉 |
| 香蕉皮 | 凝血剂 (potency × 0.2) |
| 向日葵 | 瓜子 (干燥后) |

---

## D. 稀有度索引 (rarity)

| 稀有度 | 值 | 植物 |
|---|---|---|
| 普通 | 0 | 苹果/香蕉/浆果/番茄/青柠/橙子/柠檬/葡萄/西瓜/胡萝卜/辣椒/玉米/大豆/小麦/大米/棉花/甘蔗/蘑菇(基础种) |
| 低稀有 | 10 | 毒浆果/桶瓜/爆裂玉米/坎恩根/白鲜 |
| 中稀有 | 15 | 米玛娜/月花/文果/白鲜 |
| 稀有 (PLANT_MODERATELY_RARE) | - | 发光浆果/血番茄/冰辣椒/鬼椒/灯笼椒/圣灵瓜/荧光菇/荧光帽/暗影菇/太空烟草/茶星/咖啡Robusta/新星花/Ambrosia Deus/大麻变体(4种)/反大麻/生命草/彩虹大麻/行走蘑菇/朱庇特杯 |
| 高稀有 | 30 | 死亡浆果/毁灭天使/蓝空间香蕉/Ambrosia Gaia/太空人喇叭/Ω大麻 |
| 极高稀有 | 40 | 金苹果/Ambrosia Deus/彩虹大麻/死亡大麻/生命草 |
| 稀有69 | 69 | Ω大麻 |
| 稀有50 | 50 | 蓝空间番茄 |

---

## 索引说明

> 本文档中的所有数据均直接来源于:
> `code/modules/hydroponics/grown/*.dm` (45+ 文件, 4,747 行)
> 
> 配方倍率解释: `reagents_add = list(/datum/reagent/X = 0.2)`
> → 每个产物的 X 含量 = max(1, round(50 × (potency/100) × 0.2))
> → 100 potency 时 = max(1, round(50 × 1 × 0.2)) = 10u
