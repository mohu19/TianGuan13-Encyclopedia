# 天关 — Lavaland 熔岩地百科

> **代码**: `code/modules/mining/lavaland/`, `code/modules/mining/equipment/`, `code/modules/mining/`

---

## 目录

- [第一章 · Lavaland 环境概述](#第一章--lavaland-环境概述)（生物群系 3 种）
- [第二章 · 矿工装备详解](#第二章--矿工装备详解)（基础/动能武器/战利品/狂战士甲/诅咒刀/上帝之眼/屠神者/巨兽武器）
- [第三章 · 矿石与矿物](#第三章--矿石与矿物)（点数/精炼/矿脉）
- [第四章 · Lavaland普通怪物全录](#第四章--lavaland普通怪物全录)（17 种/机制/触手柱/刷怪权重/器官/**4.6 戈利亚骑乘/4.7 精英怪 7 种**）
- [第五章 · 巨兽 (Megafauna) 完整数据](#第五章--巨兽-megafauna-完整数据)（通用机制 + 9 巨兽）
- [第六章 · 宝箱系统](#第六章--宝箱系统)（触手/恶魔/巨兽/谜题 + 战利品池）
- [第六章B · 环境结构（矿石喷口/间歇泉）](#第六章b--环境结构矿石喷口间歇泉)（矿石喷口/间歇泉/熔岩植物/废墟 38 种/出口）
- [第七章 · Lavaland实用信息](#第七章--lavaland实用信息)（升级路径/巨兽攻略速查）
- [附录 · 代码路径索引](#附录--代码路径索引)（15,535 行）

---

## 第一章 · Lavaland 环境概述

Lavaland = 熔岩地行星表面。高温/有毒/怪物横行，但有最值钱的矿物和巨兽战利品。
矿工通过采矿穿梭机往返于空间站和地表之间。

### 1.1 环境特性

| 属性 | 内容 |
|---|---|
| 地表温度 | 极高 (需防火服) |
| 大气 | 有毒 (需O₂) |
| 地形 | 玄武岩/火山灰/熔岩池/灰烬平原 |
| 天然危险 | 熔岩池(掉入即死)、火山灰(积压)、高温 |
| 生物 | 怪物群 + 9种巨兽(Megafauna) + 灰烬植物 |
| 宝箱 | 死灵宝箱(Necropolis Chests) + 触手宝箱(Tendril Chests) |
| 矿物 | 高价值矿(金/钻石/等离子/铀) + 火山灰 |
| 建筑 | 灰烬行者营地/远古废墟/太空站 |

### 1.2 生物群系（3 种）

**代码**: `code/datums/mapgen/biomes/lavaland.dm`（113 行）

| 属性 | 玄武岩 | 页岩 | 红岩 |
|---|---|---|---|
| 地面 | 玄武岩小行星 | 页岩平滑 | 红岩（菱铁矿） |
| 岩壁 | 随机火山矿 | 页岩火山矿 | 红岩火山矿 |
| 动物密度 | 6 | 6 | 6 |
| 植物密度 | 2.5 | **5** | 4 |
| 典型植物 | 帽蘑菇/火之花/茎蘑菇 | 火之花/叶蘑菇/Seraka/发光菌 | 仙人掌/帽菇/高蘑菇/茎菇 |
| 间歇泉 | plasma_oxide/protozine/wittel | hollowwater/plasma_oxide | protozine/chiral_buffer |
| 特征 | 随机/矿石喷口(boss版) | 随机/矿石喷口 | 随机/矿石喷口 |

> 间歇泉类型：plasma_oxide（等离子氧化物）/ protozine（原蛋白）/ wittel / hollowwater / chiral_buffer / random。生物群系 mob 刷新权重见第四章 4.4。

### 1.3 灰烬风暴（Ash Storm）

**代码**: `code/datums/weather/weather_types/ash_storm.dm`（143 行）

| 属性 | 值 |
|---|---|
| 触发概率 | **90%**（拉瓦兰高频天气） |
| 预警 | 30 秒（"黑烟遮天，寻找掩体"） |
| 持续 | **1-2 分钟** |
| 结束 | 30 秒（"现在出门安全了"） |
| 伤害 | **每 tick 4 点火焰伤害**（仅有机体） |
| 免疫 | TRAIT_ASHSTORM_IMMUNE / **防火服**（消防服级隔热） |
| 效果 | 严重遮蔽视野 + 风声 + 雷电（红色） |
| 结束效果 | 玄武岩挖过的矿洞**自动回填** |

**余烬雨 Emberfall**（10% 概率替代灰烬风暴）：无害的温和余烬，不造成伤害（"像怪异的雪一样飘落"）。

---

## 第二章 · 矿工装备详解

### 2.1 基础装备（精确数值）

**代码**: `code/modules/mining/equipment/mining_tools.dm`（389 行）

| 工具 | force | toolspeed | 说明 |
|---|---|---|---|
| **镐 Pickaxe** | 15 | 1 | 基础 |
| **锈镐** | 1 | — | 最差 |
| **迷你镐** | 10 | — | 紧凑 |
| **银镐** | 17 | **0.5** | 2 倍速 |
| **钻石镐** | 19 | **0.3** | 3.3 倍速 |
| **矿钻** | — | 0.6 | 持续挖 |
| **钻石钻头** | — | **0.2** | 5 倍速 |
| **声波镐** | — | **0.1** | 10 倍速（最快） |
| **临时镐** | 10 | 3 | 手搓 |
| **铲** | 8 | 1 | 挖沙/墓 |
| **骨铲** | 10 | **0.3** | 3.3 倍速 |
| **战壕铲** | 15 | 0.75 | 军事 |
| **Big Slappy** | **30** | **0.1** | 巨型扳手（彩蛋） |

**扫描仪**（mineral_scanner.dm 116 行）：手动扫描仪/自动扫描仪/高级自动扫描仪（显示矿物类型+距离）

### 2.2 动能粉碎器 Kinetic Crusher（矿工招牌）

**代码**: `code/modules/mining/equipment/kinetic_crusher/kinetic_crusher.dm`（376 行）

| 属性 | 值 |
|---|---|
| force | 0（**双手持握 20**） |
| 投掷 | 5 / 速度 4 |
| AP | **10** |
| 屠宰 | 速度 6s / 效率 110 |
| **标记引爆** | 命中标记目标 **+50 伤害** |
| **背刺** | 背后攻击 **+30 额外伤害** |
| 充能 | 1.5s（能量弹发射） |
| 战利品槽 | 可装粉碎者战利品（怪物/巨兽掉落强化） |

**战利品系统**（trophies_fauna 377 + trophies_megafauna 282 + trophies_misc 154）：击杀矿怪/巨兽掉战利品，装在粉碎器上获得强化（如对特定生物增伤/爆炸等）

### 2.3 生存装备（精确数值）

**代码**: `code/modules/mining/equipment/`

| 装备 | 数值 | 文件 |
|---|---|---|
| **共振器** | force 15，创造共振场（范围挖矿） | resonator.dm 185 |
| **升级共振器** | 更强共振场 | resonator.dm |
| **钩爪枪** | 跨越地形/拉向目标点 | grapple_gun.dm 187 |
| **虫洞跳跃器** | 随机虫洞跳跃（逃离危险） | wormhole_jaunter.dm 129 |
| **标记信标** | 放置路径标记（可拆） | marker_beacons.dm 211 |
| **生存舱胶囊** | 展开生存小屋（带床/ChemMaster/空调） | survival_pod.dm 338 |
| **豪华生存舱** | 更豪华版 | survival_pod.dm |
| **豪华精英吧舱** | 酒吧主题舱 | survival_pod.dm |
| **应急救济舱** | 救灾版 | survival_pod.dm |
| **探险服** | 矿工防护服 | explorer_gear.dm 249 |
| **Kheiral 手铐** | 特殊手铐 | kheiral_cuffs.dm 129 |
| **Lazarus 注射器** | 复活怪物→宠物（不可复活巨兽） | lazarus_injector.dm 68 |
| **采矿气象收音机** | 风暴预警 | miningradio.dm 24 |
| **喷口指示器** | 定位喷口 | vent_pointer.dm 21 |
| **Fulton 回收套件** | 物体→气球→运回站 | fulton.dm |

### 2.4 矿工券套装（Voucher Sets）

**代码**: `code/modules/mining/voucher_sets.dm`（111 行）

矿工用券兑换的起始套装：

| 套装 | 内容 |
|---|---|
| **粉碎器套装 Crusher Kit** | 动能粉碎器 + 迷你灭火器 + 工程紧急氧气罐 |
| **采集救援套装 Extraction Kit** | Fulton 回收包 + 信标 + **30 个标记信标** |
| **共振器套装 Resonator Kit** | 共振器 + 迷你灭火器（低压增伤） |
| **生存舱套装 Survival Capsule** | 探险者腰带（内含备用生存舱胶囊） |
| **挖矿机器人套装 Minebot Kit** | 挖矿机器人 |

### 2.5 辅助基地（Auxiliary Base）

**代码**: `code/modules/mining/aux_base.dm`（449 行）

**辅助基地管理台**（auxiliary base management console）：矿工可发射**采矿突击舱**（assault_pod/mining）降落到指定区域，建立临时前哨基地（降落点选择/采矿模式设置）。

### 2.6 生存舱模板（Shelters）

**代码**: `code/modules/mining/shelters.dm`（182 行）

多种生存舱模板（alpha 等），由生存舱胶囊展开生成（第二章 2.3 已列胶囊本体）。

### 2.7 怪物器官装备

| 器官 | 来源 | 功能 |
|---|---|---|
| **再生核心 (Regenerative Core)** | Goliath等 | 使用后恢复大量生命 |
| **Brimdust囊 (Brimdust Sac)** | 各种怪物 | 爆炸物原料 |
| **冲刺腺 (Rush Gland)** | 特定怪物 | 加速移动 |
| **Lazarus注射器** | 商店 | **复活怪物→变成宠物!** 仅限低级生物, 不能复活巨兽 |

**Lazarus注射器使用**:
```dm
1. 对死亡简单生物使用 → 复活
2. 被复活生物变得友好(跟随主人)
3. EMP会导致注射器故障 → 复活后敌对我方
4. 不能复活: 巨兽/机器人/高智能生物
```

### 2.8 特殊装备 (战利品)

**代码**: `code/modules/mining/lavaland/mining_loot/equipment.dm`（254 行）

| 装备 | 效果 | 来源 |
|---|---|---|
| **Wisp Lantern** | 释放友好精灵跟随, 给予**热视觉** | 解谜宝箱 |
| **Jacob's Ladder** | 放置后连接所有Z层(直通上下) | 触手宝箱 |
| **Book of Babel** | 使用后获得所有语言能力 | 触手宝箱 |
| **飞行药水** | 喝下长出翅膀(可选种类) | 触手宝箱 |
| **Skeleton Key** | 打开触手宝箱(骨制钥匙) | Ash Walker 掉落 |
| **阿斯克勒庇俄斯之杖** | 念希波克拉底誓言激活→治疗光环（绑定不可掉落） | 触手宝箱 |
| **红/蓝传送立方体** | 成对传送（红↔蓝，0.5s 动画） | 触手宝箱 |
| **不朽护符** | **10 秒完全无敌**（GODMODE+隐形，60s CD，反魔法） | 触手宝箱 |
| **悖论袋** | 双位置存储（15 容量/21 槽） | 触手宝箱 |
| **狂战士甲** | 见 2.7 | 血醉者 |

### 2.9 狂战士甲 Berserker（血醉者掉落）

**代码**: `code/modules/mining/lavaland/mining_loot/berserker.dm`（152 行）

**护甲**：近战 30 / 子弹 30 / 激光 10 / 能量 20 / 炸弹 50 / 生物 60 / 火 100 / 酸 100 / 伤口 10。全身体覆盖+防火防酸+THICKMATERIAL+反魔法。

**狂暴模式**（头盔激活）：
- **充能**：受伤→充能 +伤害×0.75（弹道 ×1.5）；满 100 可用
- **效果**：**减伤 50%**（brute_mod ×0.5）+ **攻击速度 ×4** + 禁枪（NOGUNS）+ 移速提升 + 红色染色
- **消耗**：每秒 -5 充能，耗尽自动结束；头盔掉落/脱下强制结束
- **反射**：狂暴中 IsReflect（可反射弹道）

**龙遗骸强化**（armor_plate）：用龙遗骸强化 → +35 近战 / +30 激光 / +20 能量 / +20 炸弹，称号"empowered"。

### 2.10 诅咒武士刀 Cursed Katana（血醉者/触手）

**代码**: `code/modules/mining/lavaland/mining_loot/cursed_katana.dm`（238 行）

| 属性 | 值 |
|---|---|
| 来源 | 暗色碎片（手臂植入体伸出，藏宝图指向） |
| 基础 | force 15 / **AP 30** / **格挡 30%** / SHARP_EDGED / 巨大 |
| 机制 | 命中=尝血；**未饮血收回刀→自己 25 钝击伤**；命中矿工类攻击间隔 CLICK_CD_RAPID；掉落即销毁 |

**6 连招**（最大连段 4）：
| 连招 | 按键 | 效果 |
|---|---|---|
| 柄击 | L-L-R | 17伤+投掷5格（撞墙+5伤+混乱8s） |
| 横扫 | R-L-L | 主目标5伤+伤口10；前方5方向各15伤 |
| 突进 | L-R-R | 17伤+**传送8格** |
| 割腱 | R-R-L | 15伤+伤口15+**6层出血** |
| 黑暗斗篷 | L-R-L-R | **5秒隐形**（怪物无法索敌） |
| 碎裂 | R-L-R-L | 普通35伤/矿工类**75伤**；刀碎→45s凝血重生 |

### 2.11 上帝之眼 Eye of God

**代码**: `mining_loot/eye_of_god.dm`

| 属性 | 值 |
|---|---|
| 来源 | 随机战利品池（触手/恶魔宝箱） |
| 视觉 | **透视墙**(SEE_TURFS)+**疯狂免疫**+色彩偏移 |
| 绑定 | 戴上即 TRAIT_NODROP（斩首才可取下） |
| **扫描技能** | 35s CD：目标获得**攻击间隔增加(rebuked)+30秒红轮廓+15秒追踪信标**(pinpointer)；自己同样获得追踪信标 |

### 2.12 屠神者套装 Godslayer

**代码**: `mining_loot/godslayer.dm`

| 属性 | 值 |
|---|---|
| 来源 | **手工合成**：冰能量水晶+温迪戈头骨+钟表合金（6s） |
| 护甲 | 近战 **70** / 子弹 50 / 激光 30 / 能量 40 / 炸弹 50 / 生物 50 / 火 100 / 酸 100 |
| **复活特效** | 倒地时自动触发（10 分钟 CD）：**治疗 500 伤害**（钝击→烧伤→缺氧顺序） |
| 防护 | 气压防护+THICKMATERIAL+防火防酸防冻 |

### 2.13 巨兽战利品武器

| 武器 | 来源 | 数据 |
|---|---|---|
| **开膛锯** | 血醉矿工 | 闭合 force 12/攻击×0.5/叠3层锯出血；展开 force 20/对矿系**+30伤**/3方向AOE |
| **幽灵剑** | 龙宝箱 | force 5+每幽灵+4伤(上限75)+每幽灵+5%格挡(上限75)；60s CD 召唤幽灵 |
| **熔岩杖** | 龙宝箱 | force 18/BURN；指哪哪变熔岩(10伤+着火)，CD 10s；对熔岩还原玄武岩 CD 5s |
| **龙血瓶** | 龙宝箱 | 2=变骷髅 / 3=化龙变形法术 / 4=**永久岩浆免疫**（NOVA 禁用 1 号变蜥蜴） |
| **风暴杖** | Legion 全灭 | 召唤/驱散灰烬风暴+强力近战 |
| **圣师之杖** | Hierophant | 放信标→蓄力 4s 传送自己+周围生物 |
| **冰能量水晶** | 恶魔冰矿工 | 屠神者材料 |
| **温迪戈血** | 温迪戈 | 喝下获**北极熊变形法术** |
| **温迪戈头骨** | 温迪戈 | 屠神者材料 |
| **复活水晶** | 恶魔冰矿工 1/3 | 死后克隆替死+满血复活+变暗影族 |
| **诅咒冰靴** | 恶魔冰矿工 1/3 | 不可脱，踩出 6s 临时冰面 |
| **恶魔电钻** | 恶魔冰矿工 1/3 | toolspeed 0+击退4+吸血5+矿扫 |
| **钟表合金** | 钟表骑士 | 屠神者材料 |
| **血狂瓶** | Bubblegum 宝箱 | 使用后全场狂暴（NOVA 替换原版 mayhem） |
| **魂镰** | Bubblegum 宝箱 | 灵魂镰刀 |

---

## 第三章 · 矿石与矿物

### 3.1 Lavaland矿石（点数/精炼/矿脉）

**代码**: `code/modules/mining/ores_coins.dm`

| 矿石 | 点数(ORM) | 矿脉概率 | 精炼产物 | 说明 |
|---|---|---|---|---|
| **钻石 Diamond** | **50** | 20% | 钻石板 | 最稀有 |
| **钛 Titanium** | **50** | 35% | 钛板 | Lavaland 大量 |
| **香蕉矿 Bananium** | **60** | 20% | 香蕉板 | 小丑核心 |
| **铀 Uranium** | **30** | 35% | 铀板 | 辐射 |
| **金 Gold** | **18** | 35% | 金板 | — |
| **银 Silver** | **16** | 35% | 银板 | — |
| **等离子 Plasma** | **15** | 45% | 等离子板 | 燃料 |
| **铁 Iron** | **1** | 65% | 铁板 | 最基础 |
| **沙/玻璃** | 1 | — | 玻璃板 | 沙堆 |

> 矿石通过**矿石赎回机（ORM）**换点数，点数兑换材料/装备。矿脉类型：cluster/scatter/plain/branching 4 种。

### 3.2 熔岩地市场交易

**代码**: `markets/market_items/local_goods.dm`

Lavaland特产可通过市场系统交易(如巨兽战利品/稀有矿物)。

---

## 第四章 · Lavaland普通怪物全录

**代码**: `code/modules/mob/living/basic/lavaland/`（57 文件 ~8,100 行）
**通用**: 采矿怪物基类免疫熔岩/灰风暴/雪暴、STAMINA 伤害系数 0、被碎骨炮针对（投射物力<30 仅 30% 伤害）

### 4.1 怪物总览表（15 种核心）

| 怪物 | 血量 | 近战 | 速度 | 攻击方式 | 掉落物 |
|---|---|---|---|---|---|
| **Goliath 歌利亚** | 300 | 25 | 12 | 近战+触手三技能 | 歌利亚肉×2+骨头×2+兽皮×1 |
| **Watcher 看守者** | 160 | 15 | 3 | 冰冻弹(3s CD,10 burn)+凝视(20s CD) | 骨头+钻石×2+筋腱×2 |
| **Magmawing 熔翼看守者** | 175 | 15 | 3 | 灼烧弹(5 burn)+灼烧凝视(麻痹3s) | 骨头+钻石×2+熔翼筋腱×3 |
| **Icewing 冰翼看守者** | 130 | 15 | 3 | 寒冰弹(5 burn)+寒冰凝视(冻结3s) | 骨头+钻石×5+冰翼筋腱×3 |
| **Legion 军团** | 75 | 15 | 3 | 骷髅投射(4s CD)+近战 | 军团再生核心×1 |
| **Legion Brood 骷髅** | 1 | 12 | — | 撕咬12+寄生 | 无（10s自毁） |
| **Goldgrub 金虫** | 175 | 0 | 5 | 无伤害；吃矿+钻地+产蛋 | 吐出吞掉的矿石+随机矿1-3 |
| **Raptor 迅猛龙** | 200-400 | 10-25 | 0.5 | 近战撕咬+骑乘攻击 | 鸡肉×4+骨头×2+迅猛龙羽 |
| **Mook 打手** | 150 | 8-15 | 5 | 近战+治疗同族+跳跃突袭 | 无 |
| **Lobstrosity 龙虾怪** | 150 | 15/19 | 慢 | 冲锋(8格击倒2.5s)+撕臂+钓鱼 | 生蟹肉×2+骨头×2+**冲刺腺×1** |
| **Bileworm 胆汁虫** | 110 | 无 | 不动 | 胆汁弹幕(20 burn/AP40)+钻地+吞噬 | 虫肉×4+胆汁虫皮+金矿石×2 |
| **Gutlunch 肠炮手** | 70 | 8-13 | 5 | 近战+裂墙采矿 | 宠物（灰烬行者） |
| **Hivelord 蜂巢领主** | 75 | 0 | 2 | 召唤蜂群(2s CD)+被击爆兵 | **再生核心×1** |
| **Node Drone 节点无人机** | 300+100/级 | 无 | — | 守卫矿脉+逃跑+死亡爆炸 | 无 |
| **Brimdemon 硫魔** | 250 | 7.5 | 3 | 硫磺光束(25 burn+持续7)+死亡自爆 | 肉×2+**硫尘囊×1** |
| **Basilisk 巴西利斯克** | 200 | 12 | 20(极慢) | 冰冻弹+过热变40 BRUTE 能量弹 | 骨头+钻石×2+筋腱×2 |
| **Tendril 触手柱** | **800** | 20 | 不动 | 4 技能（见 4.3） | **地牢宝箱**（需骷髅钥匙） |

### 4.2 关键怪物机制

**Goliath 歌利亚**：
- 触手三技能：地震触手(12s CD/射程7)、近身爆发(24s CD/8方向)、线性抓取(12s CD/追踪2s)
- 触手：0.7s 预警→0.3s 抓取→**腿铐**(slowdown4/挣脱6s/耐久75)+伤害10-15；拖拽型拉向歌利亚(15s)
- 驯服 10%+5%；用歌利亚鞍可骑乘；变种：immortal(400血/每tick刷触手)、ancient、deathmatch

**Watcher 看守者**：
- 凝视：20s CD，0.8s 睁眼→1.6s 蓄力→正看着它的玩家被 flash+混乱12s；自身 Stun 1.5s
- 保持 3-5 格游走射击；被凝视的玩家不会被射击（红绿灯机制）

**Legion 军团**：
- 骷髅投射(4s CD/射程7)；brood 骷髅 1血/可穿人/**命中重伤者→寄生**（人变新 Legion）
- 寄生：死亡→复活→装入 Legion 体内；**75% 概率植入军团瘤**
- 变种：dwarf(60血)、**myriad(450血/20伤/生小军团)**、monkey(40血)

**Goldgrub 金虫**：
- 纯和平；吃稀有矿回血（金5/铀5/钻石15/蓝晶20/香蕉矿30）；**反射 PKA/切割光束**
- 钻地(7s CD/5格传送)；吃蓝水晶 40% 生蛋；驯服 25%+5% 可骑乘吐矿

**Raptor 迅猛龙**（7 色亚种）：
| 颜色 | 血量 | 伤害 | 特性 |
|---|---|---|---|
| 红 | 300 | 15/20 | 战斗骑乘、攻击性 AI |
| 紫 | 140 | 10/15 | 小型坐骑、**可当背包(19格)**、飞行翅膀 |
| 绿 | 400 | 10/15 | 血牛+采矿效率 |
| 白 | 220 | 10/15 | 治疗同族/骑手 |
| 黄 | 220 | 10/15 | **极速**（speed 0） |
| 蓝 | 300 | 10/15 | **岩浆免疫** |
| 黑 | 400 | 20/25 | 出生率 1/151、岩浆免疫、战斗 AI |

**Lobstrosity 龙虾怪**：
- 胆小 AI：目标健康→逃跑；目标倒地→**反扑冲锋**(8格/击倒2.5s)
- **撕臂**(5s 钳掉手臂)、钓鱼；幼体 65血/惧渔夫/驯服 35%+20%/7-12分钟成年

**Bileworm 胆汁虫**：
- 定身（靠技能移动）；胆汁弹幕(3s CD/每发20 BURN/AP40)
- 钻地(无敌0.7-1.2s→5格内出现)；吞噬昏迷目标（满血回复）；**30分钟后进化 vileworm**(175血/6发散射/27伤)

**Basilisk 巴西利斯克**：
- 过热机制：被熔岩/热弹/燃烧伤害→**3分钟过热**：立即满血+40 BRUTE 能量弹+加速；泼水/冰冻可解除
- BURN 伤害×0.1（火焰几乎无效）

**Brimdemon 硫魔**：
- 硫磺光束：5s CD，1s 蓄力（可从背后打断）→射程10/持续2s，命中 25 BURN+每秒 7 BURN
- 死亡自爆：1.9s 后 1 格内 20 BURN；屠宰掉硫尘囊

### 4.3 Tendril 触手柱完整机制

**代码**: `tendril/tendril.dm`(169) + `tendril_actions.dm`(415)

| 项目 | 数值 |
|---|---|
| 血量 | **800**（30s 无伤后每秒回复 20） |
| 近战 | 20/20 穿刺（不可伤口）+8 方向尖刺 |
| GPS | Eerie Signal（红光照 3 格） |
| 生成 | 玄武岩 7/页岩 8/红岩 6 权重；生成清空半径 4.5 格岩石+黑红地砖 |
| **Tentacle Lash** | 12s CD：十字+对角 8 向触手弹(10伤+拉向柱) |
| **Impaling Spikes** | 12s CD：追踪尖刺(15伤)；血量<60% 变广域 |
| **Cross Spikes** | 12s CD：半径 7 尖刺群(15伤)；血量<30% 密度翻倍 |
| **Tentacle Stab** | 4s CD：被近战命中触发，8 方向短刺(15伤) |
| 心率 | 无目标 1.8s/有目标 0.4-1.0s 心跳加速 |
| 破坏 | 掉**地牢宝箱**(需骷髅钥匙)+感染地面复原+全图清光得成就 |

### 4.4 刷怪体系（生态权重）

**生物群系权重**（`biomes/lavaland.dm`）：

| 怪物 | 玄武岩 | 页岩 | 红岩 |
|---|---|---|---|
| goliath | 50 | 40 | 20 |
| legion | 40 | 20 | 25 |
| watcher | 20 | 30 | 55 |
| bileworm | 10 | — | 35 |
| brimdemon | — | 40 | 15 |
| lobstrosity/lava | 20 | 40 | 25 |
| raptor | 15 | 15 | 20 |
| goldgrub | 15 | 35 | 15 |
| **tendril** | **7** | **8** | **6** |
| megafauna | 2 | 2 | 2 |

**变种概率**：watcher 普通80/熔翼15/冰翼5；goliath 普通99/**immortal 1**；raptor 六色各25/**黑1**。
**风暴重生**：灰风暴/沙尘暴/暴风雪来临时，死亡怪物 1-10s 重新生成（排除巨兽/raptor/tendril）。

### 4.5 怪物器官/掉落物

| 掉落物 | 来源 | 效果 |
|---|---|---|
| **再生核心** | Hivelord 必掉/Legion 版 | 使用→持续回复；植入→**濒死自动完全复活**；4 分钟衰败（稳定血清保鲜） |
| **军团瘤** | Legion 75% 植入 | 胸腔寄生：每 80s 成熟一级(共5级)，5 级后宿主变 Legion |
| **冲刺腺** | Lobstrosity 必掉 | 30s 移速提升+免疫减速；植入→60s 延长版+血<30 自动触发+挣脱抓取 |
| **硫尘囊** | Brimdemon 必掉 | 3 层硫尘镀层：被打→微型爆炸(40伤)；植入后每 30s 自动+1 层 |
| **硫尘** | Brimdemon 屠宰 | 15u 硫尘试剂（种植用） |
| 歌利亚兽皮/胆汁虫皮 | 必掉 | 动物皮革 |

> **NOVA EDIT**：采矿怪物阵营改为 `FACTION_MINING_FAUNA`（不再与灰烬行者同阵营）；触手柱诅咒（灰烬行者用诅咒匕首切换）；碎骨炮战利品 8 种（goliath触手/legion骷髅/watcher翼/龙虾钳/胆汁虫喷囊/硫魔獠牙等）

### 4.6 戈利亚骑乘（矿工坐骑详解）

**代码**: `lavaland/goliath/goliath.dm` — `make_rideable()`（2026-08 补充）

| 机制 | 值 |
|---|---|
| **鞍具** | 戈利亚鞍（`/obj/item/goliath_saddle`）——对戈利亚使用后装备（"This rough saddle will give you a serviceable seat upon a goliath!"） |
| 效果 | saddled=TRUE → `AddElement(/datum/element/ridable, /datum/component/riding/creature/goliath)` 可骑乘（像骑马） |
| 外观 | 加鞍后显示鞍覆层（goliath_saddled） |
| 驯服 | 喂 ash_flora（拉瓦兰植物）回血 10；BEAST_EMPATHY 加成 |
| 触手反击 | 受伤害越多召唤触手越频繁（`apply_damage` 触发） |
| **死亡竞赛戈利亚** | `goliath/deathmatch`：预装鞍（开场即可骑）+ 专属骑乘组件 |

> 骑乘戈利亚 = 矿工拉瓦兰移动方式，适合运输矿石/穿越岩浆区；deathmatch 版无需鞍具。

### 4.7 拉瓦兰精英怪（7 种 · Boss 级）★新增

**代码**: `code/modules/mob/living/simple_animal/hostile/mining_mobs/elites/`（5 文件，2026-08 mob 图鉴新发现）

> **矿工终极挑战**——全部 1000 血 Boss 级，击杀掉特殊战利品。由 elite 基类派生（随机替换普通怪或特殊刷新）。

| 精英怪 | 血量 | 伤害 | 特性/掉落 |
|---|---|---|---|
| **戈利亚母蛛 Broodmother** | **1000** | 30 | 召唤**幼戈利亚**群（30 血 5 伤，群体小兵）；掉母蛛相关材料 |
| 幼戈利亚 | 30 | 5 | 母蛛召唤物（群体） |
| **先驱者 Herald** | **1000** | 20 | 召唤**先驱镜像**（60 血）；掉**先知斗篷 Cloak of the Prophet**（传说装备） |
| 先驱镜像 | 60 | — | 先驱召唤物 |
| **军团兵 Legionnaire** | **1000** | 35 | **冲锋**（锁定方向+移动禁令）+**篝火交换**（召唤可换位篝火，可燃）；掉军团兵头 |
| 军团兵头 | 200 | 20 | 军团兵掉落（类似军团骷髅头） |
| **潘多拉 Pandora** | **1000** | 15 | 圣职者方块攻击（hiero squares 变体）；掉**希望（Hope）**（传说物品） |

> **攻略**：先驱者掉**先知斗篷**、潘多拉掉**希望**为矿工传说级装备——精英怪狩猎是高端矿工玩法，需重火力+走位（1000 血不能硬刚）。

---

## 第五章 · 巨兽 (Megafauna) 完整数据

**代码**: `code/modules/mob/living/simple_animal/hostile/megafauna/`（2,771 行）+ `code/modules/mob/living/basic/boss/`（1,330 行）

### 5.0 巨兽通用机制（基类 `_megafauna.dm`）

| 属性 | 值 |
|---|---|
| 基础血量 | 1000（各巨兽覆写为 700-2500） |
| 伤害系数 | BRUTE ×1 / BURN ×0.5 / TOX ×1 / STAMINA ×0（免疫耐力） |
| 环境破坏 | ENVIRONMENT_SMASH_RWALLS（可拆加固墙） |
| 物体伤害 | obj_damage 400 |
| 爆炸减伤 | 毁灭级-250 / 重-100 / 轻-50 |
| 天气免疫 | 熔岩+灰烬风暴免疫 |
| GPS | 每只巨兽有专属 GPS 信号 |
| 吞噬 | 吃尸体回血 maxHealth×0.5、拆肺心肝 |
| 粉碎者 | 用动能粉碎器击杀 60% 概率掉专属战利品 |
| 死亡 | 防止瞬杀（血量>0 免疫 gib/dust） |

### 5.1 Bubblegum（泡泡糖）· 2500血 · Hard

| 属性 | 值 |
|---|---|
| 血量 | **2500** / 近战 40 / AP 40 / 速度 5 |
| 特殊 | rapid_melee 8（**0.25秒/击**）、血迹行走、AP40 |
| GPS | Bloody Signal（召唤语：GRAAAAAAAHHHHHHHHH!） |
| 阵营 | 采矿+BOSS+**地狱** |

**攻击**：
| 技能 | 机制 |
|---|---|
| 血池攻击 | 站在血池上的目标被血手攻击：清醒=血手拍击 10 伤；昏迷=血爪拖入血中→**吞噬** |
| 血战狂暴 | 7 秒：**免疫所有弹道**、移速提升至 3.75、身边 AOE 伤害+击退 |
| 三重冲锋 | 连续 3 次冲锋（半血前） |
| 幻觉冲锋 | 半血后：从四方向冲锋的幻影+本体（幻影 1 血、透明） |

**掉落**：H.E.C.K. 防护服（头盔+外套）+ 死城宝箱（血狂瓶/死亡契约/法术剑 三选一，粉碎者版=恶魔爪战利品）。死亡解锁**穿梭机购买权**。被舌头舔有成就（Frenching）。

### 5.2 Ash Drake（灰烬龙）· 2500血 · Hard

| 属性 | 值 |
|---|---|
| 血量 | **2500** / 近战 40 / AP 40 / 速度 5 |
| GPS | Fiery Signal |

**攻击**：火焰锥（喷火）/ **陨石雨**（CD3）/ **大范围喷火**（Mass Fire CD10）/ **熔岩俯冲**（Lava Swoop CD4，俯冲路径变熔岩）。
**竞技场**：熔岩竞技场+熔岩池；离开竞技场会狂暴。

**掉落**：龙之宝箱（四选一：**幽灵剑/熔岩法杖/圣火法术/龙血**）+ 粉碎者=尾刺战利品。龙鳞甲（防火防爆）。

### 5.3 Colossus（巨像）· 2500血 · Very Hard

| 属性 | 值 |
|---|---|
| 血量 | **2500** / 近战 40 / AP 40 / 速度 10 |
| GPS | Angelic Signal（召唤语：Your trial begins now.） |

**攻击**（概率按狂暴等级递增）：
| 技能 | 概率 | 机制 |
|---|---|---|
| 螺旋射击 | 20+狂暴% | 死亡螺栓螺旋弹幕 |
| 全方向射击 | 20% | 八方死亡螺栓 |
| 霰弹爆破 | 60+狂暴% | 死亡螺栓霰弹（全中=秒杀） |
| 交替射击 | 其余 | 十字/对角交替 |
| 巨人终曲 | 10% 血 | **最终技能**：全屏死亡螺栓 |
| 狂暴 | 33% 血 | 全方向射击+移速 3 |

**死亡螺栓**：25 伤 / **AP 100 无视一切护甲** / 可格挡（parriable）。
**反武术**：对**睡鲤武术**或**黄金魔像**目标直接进入狂暴状态（enrage）。
**宝箱免疫**：宝箱只能被 Colossus 子弹打开。

**掉落**：死城宝箱（异常水晶+**神之声带**+**凯因与亚伯匕首**）。**黑匣子**：放入物品可跨轮次继承！

**凯因与亚伯匕首**（`cain_and_abel/` 5 文件 900 行）：
- force 15 / 攻击速度 6 / SHARP_EDGED / **reach 2 格**
- **连击系统**：连续命中叠连击（最多 6 层），每层 **+15% 伤害**（force×连击×1.15^n），血灵环绕视觉
- **血灵射击**：右键向目标发射储存的血灵（0.15s 间隔连发），每次消耗 1 层连击
- **投掷模式**：攻击键切换"水晶投掷/发射"两种模式
- 被攻击会清空连击；需空另一只手（单持）

### 5.4 Hierophant（圣职者）· 2500血 · Hard

| 属性 | 值 |
|---|---|
| 血量 | **2500** / 近战 15 / AP 50 / 速度 10 |
| 远程冷却 | 4s（随狂暴缩短） |
| GPS | Zealous Signal |
| 语音 | ROT4 加密（用 ROT22 解码） |

**攻击**：
| 技能 | 机制 |
|---|---|
| 传送闪烁 | 传送到目标旁，起点终点爆炸（半血后多次） |
| 十字爆破 | 目标脚下生成十字/对角光束（5 格） |
| 追踪者 | 4 个追踪者追目标，留爆炸轨迹（速度随狂暴提升） |
| 追踪者群 | 半血后全方向追踪者群 |
| 竞技场 | 目标出竞技场→围出 10s 竞技场（20s CD） |

**掉落**：**圣职者杖**（死亡后缩小的弱化版，可放置传送信标+传送队友）+ 粉碎者=漩涡护符。

### 5.5 Legion（军团王）· 700血 · Medium

| 属性 | 值 |
|---|---|
| 血量 | **700**（分裂后 350/200）/ 近战 25 / AP 50 / 速度 5 |
| 远程冷却 | 2s |
| GPS | Echoing Signal |
| 位置 | 死城之门（Lavaland 最北端），进入死城时出现 |

**攻击**：骷髅召唤（最多用）/ 冲锋（旋转+突进）/ 哨兵塔（最多 3-6 座）。
**分裂**：死亡→3 个中型（左/右/眼）→各分裂 3 个小型（共 9）→**全灭才死**。
**特殊**：对昏迷目标攻击→**寄生**（变成小军团）；受伤会强制开门（防卡门）。

**掉落**：全灭后掉**风暴之杖**（召唤/驱散灰烬风暴+强力近战武器）；小军团 20% 掉触手宝箱。

### 5.6 Blood Drunk Miner（血醉矿工）· 1300血 · Medium

| 属性 | 值 |
|---|---|
| 血量 | **1300** / 速度 2.5（极快） |
| GPS | Resonant Signal |

**攻击**：链锯连击（**5 连击**，变形后 3 连击）/ 冲刺 / 动能加速器射击 / 冲刺攻击。
**AI**：无限追击、无视地形、被攻击会打断动作（10% 延迟）。

**掉落**：**劈锯**（cleaving saw）+ **地狱 PKA**（infernal kinetic accelerator）+ 粉碎者=矿工之眼+猎手刀。

### 5.7 Demonic Frost Miner（恶魔冰矿工）· 1500血 · Extremely Hard

| 属性 | 值 |
|---|---|
| 血量 | **1500** / 近战 10 / **AP 100** / 速度 20（极快） |
| GPS | Bloodchilling Signal |

**攻击**：冰球（rapid）/ 强冰球 / **雪球机枪** / 硬雪球机枪 / 冰霰弹 / 圆冰霰弹（半血）。

**掉落**：**冰能量水晶** + 等离子遗骸。

### 5.8 The Thing（异形之物）· 1800血

| 属性 | 值 |
|---|---|
| 血量 | **1800** / 近战 30 / AP 40 / 速度 3 |
| 来源 | `code/modules/mob/living/basic/boss/thing/`（337+203+143 行） |

**机制**：不可名状的异形怪物，触手+精神污染系攻击。死亡掉恐惧之物相关物品。

### 5.9 Wendigo（温迪戈）· 2500血 · Hard（冰月）

| 属性 | 值 |
|---|---|
| 血量 | **2500** / 近战 40 / AP 40 / 速度 6（半血变 4） |
| GPS | Berserk Signal（召唤语：GwaHOOOOOOOOOOOOOOOOOOOOO） |
| 位置 | 冰月（Icemoon）巨兽，天气免疫雪暴 |

**攻击**：
| 技能 | 机制 |
|---|---|
| 地裂震荡 | 跺脚：周围 8 格抛飞+20 伤 |
| 传送 | 传送到目标旁 |
| 霰弹/交替环/螺旋/波浪 | 4 种冲击波弹幕（半血增强） |
| 尖啸 | 10s CD：7 格内全体眩晕 12s |

**掉落**：**温迪戈血** + **温迪戈骷髅**（屠夫产物）+ 粉碎者=温迪戈角。死亡开**出口传送门**。

### 5.10 Clockwork Defender（发条守卫）· 300血

| 属性 | 值 |
|---|---|
| 血量 | **300** / 近战 20 / 速度 5 |
| GPS | Clockwork Signal |
| 来源 | 发条世界（clockwork realm）守卫，非 Lavaland 原生 |

---

## 第六章 · 宝箱系统

### 6.1 触手宝箱 (Tendril Chest)

**代码**: `necropolis_chests.dm`

| 属性 | 内容 |
|---|---|
| 名称 | Necropolis Chest |
| 描述 | "It's watching you suspiciously." |
| 打开方式 | 需要 **Skeleton Key**（一次性消耗） |
| 无法暴力开启 | `integrity_failure = 0` |
| 内容 | 随机战利品池 1 件 |

**随机战利品池**（`spawners/random/mining_loot.dm`，所有条目权重 1）：

| 物品 | 触手宝箱 | 恶魔宝箱 |
|---|---|---|
| 悖论袋 | ✅ | ✅ |
| 采矿灵魂石 | ✅ | ✅ |
| 上帝之眼 | ✅ | ✅ |
| 飞行药水 | ✅ | ✅ |
| 震荡拳套 | ✅ | ✅ |
| 稀有 PKA 模组（AOE/连发/谐振爆破/赏金/吸血 5选1） | ✅ | ✅ |
| 阿斯克勒庇俄斯之杖 | ✅ | ✅ |
| 诅咒巫师心脏 | ✅ | ✅ |
| 瓶中船 | ✅ | ✅（另有狼皮大衣/异星焊枪/圣焰书） |
| 狂战士套装 | ✅ | ❌ |
| 雅各天梯 | ✅ | ✅ |
| 矿工守护灵创造器 | ✅ | ✅ |
| 红/蓝传送方块 | ✅ | ✅ |
| 精灵灯 | ✅ | ❌ |
| 不朽护符 | ✅ | ✅ |
| 召唤物品书 | ✅ | ✅ |
| 巴别之书 | ✅ | ✅ |
| Memento Mori 项链 | ✅ | ✅ |

### 6.2 恶魔宝箱 (Demonic Chest)

| 属性 | 内容 |
|---|---|
| 描述 | 更强力的战利品池 |
| 内容 | 同上表恶魔列（狂战士/精灵灯替换为狼皮大衣/异星焊枪/圣焰书） |

### 6.3 巨兽宝箱 (Megafauna Chest)

击杀巨兽后掉落, 固定+随机战利品(见各巨兽章节)

### 6.4 解谜宝箱 (Puzzling Chest)

**随机三选一**:
| 1 | 2 | 3 |
|---|---|---|
| Soulstone (灵魂石) | Wisp Lantern (精灵灯) | Prison Cube (囚禁立方) |

### 6.5 Skeleton Key

| 属性 | 内容 |
|---|---|
| 来源 | Lavaland骸骨掉落(Ash Walker掉落) |
| 材质 | 骨材料 (5片骨板) |
| 用途 | 打开触手宝箱的唯一方法 |

---

## 第六章B · 环境结构（矿石喷口/间歇泉）

### 6B.1 矿石喷口 Ore Vent

**代码**: `code/game/objects/structures/lavaland/ore_vent.dm`（842 行）+ `__DEFINES/mining.dm` 常量

| 属性 | 值 |
|---|---|
| 发现 | 需**采矿扫描仪/高级扫描仪**扫描（4 秒 do_after，一次性）+**100 采矿点**+GPS |
| 挖掘 | 需 TRAIT_BOULDER_BREAKER（徒手或挖矿怪），3 次×巨石大小秒，每次 -20 耐力，10s 冷却 |
| 巨石大小 | 小 5 / 中 10 / 大 15（耐久 rand(2,大小)；喷口权重 大3/中5/小7） |
| 矿物类型 | 每喷口 4 种随机矿物，巨石含 **3 种**；每次 50×(对数函数) 单位 |
| 矿石产量 | 对数递减：小喷口首次 16-7 张、后续 8-6 张；大喷口可高掷 50 张整叠 |
| 文物巨石 | **7% 概率**（artifact 子类随机） |
| 无敌 | INDESTRUCTIBLE（不可破坏，无血量） |

**矿物权重池**（`ores_spawned.dm`）：铁 14 / 玻璃 13 / 等离子 10 / 银 7 / 钛 6 / 金 5 / 铀 4 / 塑料 1

**波次防御（tapped 后触发）**：

| 波次参数 | 小喷口 | 中喷口 | 大喷口 |
|---|---|---|---|
| 时长 | 60s | 90s | 150s |
| 刷怪间隔 | 15s | 20s | 25s |
| 每次刷怪数 | 2 | 3 | 4 |
| 场上上限 | 10 | 10 | 10 |

1. 5 秒震动：清理周围 5 格（挖墙/熔岩变平台/瓦砾），熔岩平台寿命 小20s/中45s/大90s
2. 生成**节点无人机**（血量 小400/中500/大600，NOVA 继承玩家阵营）
3. 波次怪物：Goliath/Legion/Watcher/Lobstrosity/Brimdemon/Bileworm
4. **胜利奖励**（7 格内矿工）：采矿 XP 小50/中100/大150 + **采矿点 小+600/中+1100/大+1600**
5. 失败（无人机死亡）→喷口恢复可重试；反卡位：刷怪被堵→轻爆（高概率炸死无人机）

**Boss 喷口**（menacing ore vent）：开局随机选 1 个 boss（Bubblegum/Dragon/Colossus）；开波直接召唤 boss（无限冷却），**boss 死后**才出无人机并结算。冰月 boss：恶魔冰矿工/Wendigo/Colossus。

**喷口变体**：starter_resources（站内初始，已开采）/ random（随机尺寸矿物）/ random/icebox（冰月）/ boss（巨兽）/ debug（调试）/ ghost_mining（NOVA 幽灵采矿）

### 6B.2 间歇泉 Geyser

**代码**: `code/game/objects/structures/lavaland/geyser.dm`（201 行）

| 属性 | 值 |
|---|---|
| 机制 | 每 **2 秒**产出 **2u** 试剂（容量 500u），可被管道系统抽取 |
| 发现 | 采矿扫描仪扫描 → **+100 采矿点数** + GPS 标记 |
| 试剂类型 | 油（默认）/ wittel / plasma_oxide / protozine / hollowwater / chiral_buffer / 随机 |
| 特殊 | 周围可 swab 下界细胞（异星生物学） |

**稀有间歇泉**：
| 类型 | 试剂 | 点数 |
|---|---|---|
| **Wittel** | wittel（强大试剂） | **+250** |
| **Chiral Inversing** | 手性反转缓冲液 | **+250** |
| **随机** | 随机试剂 | **+500** |
| Plasma-Oxide | 等离子氧化物 | +100 |
| Protozine | 原蛋白 | +100 |
| Hollow Water | 空洞水 | +100 |

### 6B.3 古拉格通风口 Gulag Vent

**代码**: `code/game/objects/structures/lavaland/gulag_vent.dm`

劳改营通风口，用于古拉格（gulag）矿工传送。

### 6B.4 熔岩植物 Ash Flora

**代码**: `code/modules/mining/lavaland/ash_flora.dm`（488 行）

| 植物 | 产物 | 种子 | 再生时间 | 特点 |
|---|---|---|---|---|
| **大蘑菇** | 蘑菇碎屑×1 | 多孔菇 | 7 分钟 | 4 变体；碎屑=蘑菇碗材料 |
| **叶蘑菇** | 蘑菇叶×1(最多4) | 牛肝菌 | 4-10 分钟 | 采摘 20s |
| **帽蘑菇** | 蘑菇帽×1(最多4) | 丝膜菌 | 5-9 分钟 | 采摘 50s |
| **茎蘑菇** | 蘑菇茎×1(最多4) | 余烬菇 | 5-10 分钟 | **发光**(光1.5/功率2.1) |
| **仙人掌** | 仙人掌果(最多2)+种子 | 仙人掌 | 8-12 分钟 | **踩踏伤害3-6/70%概率**(caltrop) |
| **Seraka 蘑菇** | Seraka 菇帽×1(最多6) | Seraka | 5-9 分钟 | 灰烬蜥蜴带来的 Tizira 原产 |
| **火之花** | 火之花×1(最多3) | — | 4-7 分钟 | 熔岩旁生长、发光(黄) |
| **发光菌群** | 发光菌×1(最多4) | — | 8-16 分钟 | 温泉岩石上、发光(青绿) |
| **寒冷果（冰月）** | 冰辣椒×1 | — | 4-9 分钟 | 冰月植物 |

**产物用途**（wine_power）：
- 蘑菇碎屑 20 / 蘑菇叶 40 / Seraka 帽 40 / 仙人掌果 50 / 蘑菇茎 60 / 蘑菇帽 70
- 蘑菇碎屑+蘑菇叶+蘑菇帽+蘑菇茎 → **蘑菇碗**（餐盘）
- 仙人掌果：迅猛龙食物（生长+0.1/能力-0.05）
- Seraka 菇帽：迅猛龙食物（能力+0.1）

**种子属性**（`/obj/item/seeds/lavaland` 基类：寿命50/耐力25/成熟7/产出4/产量4/效力15，全部自带 fire_resistance+真菌代谢）：

| 种子 | 产物试剂 | 特点 |
|---|---|---|
| **多孔菇** | 营养/乙醇/稳定剂/微量提取物 | — |
| **牛肝菌** | 营养/糖/维他/尼**烟碱 0.04** | — |
| **丝膜菌** | **致幻剂 mindbreaker/entpoly/蘑菇致幻素** | 致幻+毒 |
| **余烬菇** | 锡/维他命/太空毒品；基因发光 | — |
| **Seraka** | 蘑菇粉/Seraka 提取物(凝血剂) | — |
| **火焰花** | 锡/营养/碳；基因发光+黄色 | 可戴头上 |
| **发光菌** | 发光液/营养/硅；NO_PLANTING | 不可种植 |
| **仙人掌** | 维他命/营养/维他 | 突变→星仙人掌 |
| **星仙人掌** | 水/营养/**helbital 药**；基因粘+刺 | **蒸馏→龙舌兰酒** |

### 6B.5 遗迹废墟全清单（46 种 = 原版 38 + NOVA 8）

**代码**: `code/datums/ruins/lavaland.dm`（364 行）+ `code/modules/mapfluff/ruins/lavalandruin_code/`（8 文件）+ NOVA `modular_nova/modules/mapping/code/lavaland.dm`

| 废墟 | 成本 | 说明 |
|---|---|---|
| **海滩生物圈** | 5 | 热带海滩+木吧台+自杀酒保 |
| **冬季生物圈** | 5 | 豪华度假村+独特激光枪展示+ICE |
| **小丑星球** | 5 | WELCOME TO CLOWN PLANET! HONK! |
| **蜥蜴加油站** | — | 加油站连锁 |
| **许愿者方块** | 10 | "Nothing good can come from this" |
| **种子库** | 10 | 远古种族种子库（种毒品和枪） |
| **自由魔像船** | 20 | 魔像族母舰（AnywhereRuins） |
| **伊甸园** | 5 | 和平绿洲 |
| **七宗罪·嫉妒** | 10 | sin/envy |
| **七宗罪·暴食** | 10 | sin/gluttony |
| **七宗罪·贪婪** | 10 | sin/greed |
| **七宗罪·傲慢** | 10 | sin/pride |
| **七宗罪·懒惰** | 0 | sin/sloth（成本 0） |
| **死神 Ratvar** | 0 | 发条教主的安息地 |
| **圣师竞技场** | 0 | 圣师巨兽竞技场（必刷） |
| **血醉矿工** | 0 | 3 变体（指引/猎人/随机，随机必刷） |
| **UFO 坠落** | 5 | 绑架者飞船残骸 |
| **异形巢** | 20 | 异形社区 |
| **喷泉大厅** | 5 | 副作用警告喷泉 |
| **生存舱废墟** | 5 | 矿工的墓 |
| **披萨派对** | 5 | 蓝空间事故毁掉的生日派对 |
| **邪教召唤仪式** | 10 | 血祭坛+恶魔笑声 |
| **隐士小屋** | 10 | 独居者的庇护所 |
| **里普利机甲** | 5 | 损坏的采矿机甲（可修） |
| **黑暗巫师祭坛** | 5 | 巫师守护的秘密 |
| **强石** | 2 | 特别强大的石头 |
| **远古谜题** | 5 | 待解之谜 |
| **脉动肿瘤** | 5 | 强大野兽的宿主（必刷） |
| **象冢** | 10 | 弃子的墓地 |
| **胆汁虫巢** | 5 | 胆汁虫的庇护所 |
| **电话亭** | 5 | NT 全息亭宣传 |
| **战场** | 3 | 远古野兽 vs 人形战迹 |
| **矿石喷口** | 0 | 自然矿喷（必刷） |
| **看守者墓地** | 5 | **看守者蛋**：携带 600 步孵化→小看守者宠物（帮你打怪）；三具尸体 |
| **打手村** | 5 | 友好 Mook 村落（治疗者/矿工/吟游诗人/酋长） |
| **穿梭机残骸** | — | 没回到 CentCom 的穿梭机 |
| **坠落点** | — | 太早发射 |
| **鞋厂** | 10 | 废弃鞋厂 |

**NOVA 新增 8 种**（`modular_nova/modules/mapping/code/lavaland.dm`）：

| 废墟 | 成本 | 说明 |
|---|---|---|
| **灰烬行者巢** | 1000 | NOVA 重新启用（成本 1000 几乎不刷） |
| **Interdyne 制药基地** | 0 | 行星地面研究设施（必刷，always_place） |
| **大竞技场** | 0 | 古角斗竞技场+致命战士（必刷） |
| **殖民者农庄** | 5 | Tiziran 灌木丛俱乐部"沉浸式"农庄 |
| **地质站点** | 0（矿 2） | 军团遭遇全军覆没的挖掘点（2 矿喷口） |
| **仪式之地** | 0（矿 1） | 石板神庙+惧兽（1 矿喷口） |
| **熔岩井** | 0（矿 1） | 熔岩湖下的富矿井（1 矿喷口） |
| **双人隐士小屋** | 5 | 双人版隐士庇护所 |

**象冢细节**（`elephantgraveyard.dm` 358 行）：
- **油井**：触摸浇 20u 油、可钓鱼（油井鱼源）、铁锹可拆
- **坟墓堆**：8 种随机战利品（金币+钱包/介子眼镜/银币+铲子/酒圣经/听诊器+手术刀等）+人类骸骨；铁锹挖掘/掩埋

**谜题废墟细节**（`puzzle.dm` 373 行）：
- **滑动拼图**：3×3 九宫格（8 块+1 空位），还原后 7 秒震动+发放奖励

**看守者墓地细节**（`watcher_grave.dm` 271 行）：
- **看守者蛋**：行走 600 步孵化（300 步开始抖动/450 步即将孵化），孵化出**小看守者宠物**；也可吃掉（尝到"眼液+孤独"）
- **幼看守者宠物**：环绕飞行+**自动攻击 5 格内熔岩怪**（光束 10 伤/5 秒一发）；主人死亡→**复仇模式射速×5**（1 秒一发）

> **NOVA EDIT**：原版"灰烬行者巢"和"辛迪加熔岩基地"（syndicate_base）在 base 中被 MAPPING 注释移除；**NOVA 分支重新启用了灰烬行者巢**（成本改为 1000，几乎不刷），并用 **Interdyne 制药基地**（always_place 必刷）取代辛迪加熔岩基地，另新增竞技场/农庄/地质站点/双喷口/双隐士等 6 种（见上表 NOVA 新增 8 种）。

### 6B.6 Lavaland 出口（Export 价值）

**代码**: `code/modules/cargo/exports/lavaland.dm`（91 行）· 基准 `CARGO_CRATE_VALUE = 200cr`

| 档位 | 单价 | 物品 |
|---|---|---|
| **次要神器** | **4000 cr** | 不朽护符/巴别之书/精灵灯/诅咒武士刀碎片/狼皮大衣/上帝之眼/Memento Mori/诅咒巫师心脏/龙斗篷/瓶中船/香蕉鞋/喇叭杖/嫉妒刀/灵魂俄式轮盘/vealrender/狂战士甲/冰冻方块/采矿灵魂石/震荡拳套/雅各天梯/吸血模组/钟表合金/地狱 PKA（23 项） |
| **主要神器** | **8000 cr** | 龙血/矿工守护灵/龙遗骸/熔岩杖/幽灵剑/囚禁方块/阿斯克勒庇俄斯杖/荒野猎刀/凯因亚伯（9 项） |
| **巨兽战利品** | **16000 cr** | 圣师杖/开膛锯/神之声带/异常水晶/soulscythe/风暴杖/H.E.C.K.套/温迪戈血/温迪戈头骨/冰能量水晶/复活水晶/诅咒冰靴/恶魔电钻（14 项） |

> 消耗品/一次性物品（魔法 D20、暴食祝福等）不可出口。注：出口表仍含原版 mayhem，但 NOVA 已从石棺移除该掉落（表未同步）。

---

## 第七章 · Lavaland实用信息

### 7.1 进阶装备升级路径

```
矿工券 → 动能粉碎器 + 共振器
  ↓
购买动能加速器(KA) + 模组盘
  ↓
收集战利品(精灵灯/飞行药水/雅各布梯)
  ↓
组队打巨兽 → 巨兽战利品武器
  ↓
Bubblegum → H.E.C.K.套(终极防护)
  ↓
Colossus → 该隐亚伯匕首(终极武器)
```

### 7.2 巨兽攻略速查（按难度）

| 巨兽 | 血量 | 难度 | 核心机制 | 关键掉落 |
|---|---|---|---|---|
| Legion | 700 | Medium | 分裂 3 次、召唤骷髅 | 风暴之杖 |
| Blood Drunk | 1300 | Medium | 5 连击链锯、极快 | 劈锯+地狱PKA |
| Demonic Frost | 1500 | Extremely Hard | AP100、速度20 | 冰能量水晶 |
| The Thing | 1800 | Hard | 触手精神污染 | 恐惧之物 |
| Bubblegum | 2500 | Hard | 血池攻击+狂暴 | H.E.C.K.套 |
| Ash Drake | 2500 | Hard | 陨石+熔岩俯冲 | 幽灵剑/龙血 |
| Hierophant | 2500 | Hard | 传送+追踪者 | 圣职者杖 |
| Colossus | 2500 | Very Hard | AP100 死亡螺栓 | 凯因亚伯匕首 |
| Wendigo | 2500 | Hard（冰月） | 地裂+尖啸 | 温迪戈血/骷髅 |

### 7.3 灰烬行者（Ash Walker）· NOVA 专属

**代码**: `code/modules/antagonists/ashwalker/ashwalker.dm`（80 行）+ `modular_nova/modules/ashwalkers/`（3,967 行）

| 项目 | 内容 |
|---|---|
| 类型 | 反派阵营（ROLE_LAVALAND），部族制 |
| 目标 | **保护死城（Necropolis）**，向死城献祭血肉繁殖族人 |
| 种族 | lizard/ashwalker：**灰风暴免疫 + 熔岩免疫 + 碎岩者** |
| 繁殖 | 献祭肉（"worthy sacrifice"）→ 死城产出**灰烬行者蛋** |
| 心情 | 查看头桩（headpike）→ 献祭心情事件 |

**NOVA 灰烬行者扩展**（3,967 行，NOVA 专属玩法）：

| 类别 | 内容 |
|---|---|
| **建筑** | 蚁丘/衣服商店（Ashland Clothing）/植物罐/轨道/树/虫牧场/燃料井/触手柱诅咒/蚂蚁农场 |
| **农业** | 灰烬农业（ash_farming）、虫牧场、植物罐（灰烬作物） |
| **仪式** | 灰烬仪式（ash_rituals 545 行）：献祭/召唤 Lavaland 怪物 |
| **工具** | 灰烬工具（ash_tool 202 行，含 **ore seed 肉块种子**→生成矿石喷口）、灰烬手术（126 行）、灰烬武器（33 行） |
| **服装** | 灰烬护甲/下装/鞋/项链 |
| **萨满** | 萨满装备（62 行） |
| **诅咒** | **触手柱诅咒**（tendril_cursing）：诅咒匕首切换触手被诅咒状态（使用者承受 100 火焰伤害）；破坏被诅咒触手→范围内人类获 ash_cursed（禁传送+减速） |
| **物种** | 灰烬蜥蜴（340 行）+ 伤害系数 |

> **NOVA EDIT**：灰烬行者巢废墟已从地图移除，但灰烬行者玩法保留（改由其他途径生成）；骷髅钥匙配方不再自动教授（改为仪式获得）。

### 7.4 劳改营（Gulag/Labor Camp）

**代码**: `code/modules/mining/laborcamp/`（288 行）+ `gulag_vent.dm`

| 项目 | 内容 |
|---|---|
| 劳改穿梭机 | 押送囚犯往返 |
| 劳改点数机 | 犯人采矿换取减刑点数 |
| 古拉格通风口 | 劳改营传送 |

### 7.5 采矿机器

**代码**: `code/modules/mining/machine_*.dm`（1,541 行）

| 机器 | 功能 |
|---|---|
| **矿石赎回机 ORM** | 矿石→点数/材料（machine_redemption） |
| **矿石加工机** | 精炼矿石（machine_processing） |
| **筒仓** | 大宗储存（machine_silo） |
| **堆叠机** | 自动堆叠（machine_stacking） |
| **卸载机** | 自动卸载（machine_unloading） |

---

# 附录 · 代码路径索引

**源码总量：约 15,535 行**（含 mob/basic/lavaland 8,100 + megafauna 2,771 + boss 1,330 + mining_loot ~1,800 + 环境结构 ~1,300 + ruins ~1,200）

| 系统 | 文件 | 行数 |
|---|---|---|
| **环境/地图** | | |
| 生物群系 | `code/datums/mapgen/biomes/lavaland.dm` | 113 |
| 废墟清单 | `code/datums/ruins/lavaland.dm` | 364 |
| 矿石喷口 | `code/game/objects/structures/lavaland/ore_vent.dm` | 842 |
| 间歇泉 | `code/game/objects/structures/lavaland/geyser.dm` | 201 |
| 古拉格通风口 | `code/game/objects/structures/lavaland/gulag_vent.dm` | 54 |
| 熔岩植物 | `code/modules/mining/lavaland/ash_flora.dm` | 488 |
| 矿石常量 | `code/__DEFINES/mining.dm` | 68 |
| 矿石矿物池 | `code/_globalvars/lists/ores_spawned.dm` | 31 |
| 矿石/硬币 | `code/modules/mining/ores_coins.dm` | — |
| 出口 | `code/modules/cargo/exports/lavaland.dm` | 91 |
| 宝箱 | `code/modules/mining/lavaland/necropolis_chests.dm` | 138 |
| 战利品池 | `code/game/objects/effects/spawners/random/mining_loot.dm` | 66 |
| **怪物（lavaland）** | `code/modules/mob/living/basic/lavaland/`（57 文件） | ~8,100 |
| Goliath | goliath/goliath.dm(238)+goliath_ai.dm(126)+tentacle.dm(242) | 606 |
| Watcher | watcher/watcher.dm(115)+watcher_gaze.dm(222)+watcher_overwatch.dm(165)+watcher_projectiles.dm(43) | 545 |
| Legion | legion/legion.dm(180)+legion_tumour.dm(167)+legion_brood.dm(142)+spawn_legions.dm(118) | 607 |
| Goldgrub | goldgrub/goldgrub.dm(219)+goldgrub_ai.dm(232) | 451 |
| Raptor | raptor/_raptor.dm(572)+raptor_color.dm(458) | 1,030 |
| Mook | mook/mook.dm(297)+mook_ai.dm(424)+mook_abilities.dm(147) | 868 |
| Lobstrosity | lobstrosity/lobstrosity.dm(289)+lobstrosity_ai.dm(339) | 628 |
| Bileworm | bileworm/bileworm_actions.dm(308)+_bileworm.dm | 308+ |
| Hivelord | hivelord/spawn_hivelord_brood.dm(124) | 124 |
| Node Drone | node_drone/node_drone.dm | 191 |
| Brimdemon | brimdemon/brimbeam.dm(162)+brimdemon.dm | 162+ |
| Tendril | tendril/tendril.dm(169)+tendril_actions.dm(415) | 584 |
| Gutlunch | gutlunchers/gutlunchers.dm(179)+gutlunchers_ai.dm(142) | 321 |
| **巨兽（megafauna）** | `code/modules/mob/living/simple_animal/hostile/megafauna/` | 2,771 |
| 基类 | _megafauna.dm | 213 |
| Hierophant | hierophant.dm | 785 |
| Bubblegum | bubblegum.dm | 406 |
| Ash Drake | drake.dm | 343 |
| Demonic Frost | demonic_frost_miner.dm | 303 |
| Colossus | colossus.dm | 239 |
| Wendigo | wendigo.dm | 228 |
| Legion 王 | legion.dm | 198 |
| 发条守卫 | clockwork_knight.dm | 56 |
| **巨兽（basic/boss）** | `code/modules/mob/living/basic/boss/` | 1,330 |
| The Thing | thing/thing.dm(337)+thing_abilities.dm(203)+thing_objects.dm(143)+thing_ai.dm(72) | 755 |
| Blood Drunk | blood_drunk_miner/_blood_drunk_miner.dm(215)+blood_drunk_actions.dm(83)+blood_drunk_ai.dm(66)+blood_drunk_subtypes.dm(49)+blood_drunk_objects.dm(30) | 443 |
| **能力系统** | `code/datums/actions/mobs/`（fire_breath/charge/blood_warp/ground_slam/lava_swoop/meteors/teleport/projectileattack/create_legion_skull/create_legion_turrets） | ~1,000 |
| **战利品装备** | `code/modules/mining/lavaland/mining_loot/` | ~1,800 |
| equipment.dm | 254 |
| consumables.dm | 243 |
| clothing.dm | 145 |
| berserker.dm | 152 |
| cursed_katana.dm | 238 |
| eye_of_god.dm | — |
| godslayer.dm | — |
| megafauna/（ash_drake 300/colossus 441/blood_drunk 166/bubblegum 132/legion 130/the_thing 218/demonic_frost_miner） | ~1,700 |
| 凯因亚伯 | cain_and_abel/（_cain_and_abel 231+dagger_abilities+dagger_effects 209+dagger_status_effects+dagger_throw） | ~900 |
| **废墟代码** | `code/modules/mapfluff/ruins/lavalandruin_code/` | ~1,100 |
| 象冢 | elephantgraveyard.dm | 358 |
| 谜题 | puzzle.dm | 373 |
| 看守者墓地 | watcher_grave.dm | 271 |
| 冬季生物圈 | biodome_winter.dm | 50 |
| 辛迪加基地 | syndicate_base.dm | 37 |
| 地表/树懒/小丑 | surface.dm(27)/sloth.dm(5)/biodome_clown_planet.dm(7) | 39 |

> **文档完** — 覆盖 Lavaland 全系统：环境/生物群系、17 种怪物、9 巨兽、装备战利品、矿石喷口、间歇泉、熔岩植物、38 废墟、宝箱、出口，全部带精确数值与代码索引。

