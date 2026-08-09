# TianGuan13 · 叛徒（Traitor）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/traitor/`（7 文件 1,100 行）+ `code/modules/uplink/`（20 文件 3,746 行）
> **类型**: 单人潜伏型反派（最经典）｜**难度**: ★★★（Uplink 搭配决定强度）
> **一句话**: 你是辛迪加叛徒——用**TC 电信水晶**买 Uplink 装备（395 件），完成任务目标或直接大闹空间站。

---

## 目录

- [一、核心机制](#一核心机制)
- [二、Uplink 系统](#二uplink-系统)
- [三、Uplink 物品全录（395 件）](#三uplink-物品全录395-件)
- [四、承包商契约](#四承包商契约)
- [五、对战攻略](#五对战攻略)

---


**代码**: `code/modules/antagonists/traitor/`（7 文件 1,100 行）+ `code/modules/uplink/`（20 文件 3,746 行）

## 1.1 叛徒核心机制

**代码**: `datum_traitor.dm`（354 行）

| 机制 | 值 |
|---|---|
| 目标 | 任务目标（职业/通用）+ 终局目标 |
| 终局目标 | **劫机**（hijack）/ **殉道**（martyr）/ **逃脱**（escape） |
| 雇主 | 75% 辛迪加 / 25% Nanotrasen（flavor 系统） |
| Uplink | 授予（UPLINK_TRAITORS 标志） |
| 折扣销售 | 每局 4-6 件折扣物品 |
| 进度系统 | 声誉（progression）解锁高级物品 |
| 自杀口号 | "FOR THE SYNDICATE!!" |

**NOVA EDIT**：`can_assign_self_objectives = FALSE`（原版 TRUE，NOVA 收紧）

## 1.2 Uplink 系统

**代码**: `uplink_handler.dm`（138 行）

| 机制 | 值 |
|---|---|
| TC 购买 | telecrystals 扣除 |
| 声誉 | progression_points（经验，解锁高级物品） |
| 库存 | 套装/盈余各 1 库存（shared stock） |
| 限制 | 角色限定/物种限定/population 要求 |
| 购买锁定 | shop_locked（被 EMP/黑客可锁） |

## 1.3 Uplink 物品全录（395 件，21 分类）

**代码**: `code/modules/uplink/`（20 文件 3,746 行）

| 分类 | 数量 | 代表物品（TC 价） |
|---|---|---|
| 角色限定 | 42 | 幽灵八号球 2/邮件伪造套件 2/生化炸弹 2 |
| 装置工具 | 37 | 辛迪加肥皂 1/可疑行李袋 2/战斗急救包 3 |
| 核弹弹药 | 37 | Bulldog 弹鼓系列 |
| 爆炸物 | 29 | 香蕉炸弹 4/小丑炸弹 15/胡须手雷 3/武器级皮纳塔 12 |
| 间谍专属 | 29 | 辛迪加弓弩 1/扩音器 1/战斗手套 1 |
| 武器包 | 22 | 香蕉奶油派炮 10/香蕉能量盾 16/香蕉能量剑 3 |
| 护甲 | 16 | 精英辛迪加 MOD 服 ?/MOD 能量盾 8/EMP 盾 5 |
| 潜行工具 | 15 | 战斗香蕉鞋 6/特工 ID 卡 2/AI 探测器 1/变色套件 2 |
| 狠货 | 14 | 辛迪加气球 20/扑克牌 1/辛迪加烟 2/公文包现金 3 |
| 潜行武器 | 14 | Romerol 25/飞镖枪 4/脱水太空鲤 1/能量匕首 2 |
| TC 捆绑 | 13 | 随机物品 0/1 原TC 1/战术套件 20/特殊套件 20 |
| 植入体 | 13 | 自由植入体 5/辛迪加无线电 4/潜行植入体 8/储物植入体 8 |
| 危险品 | 10 | Donksoft 防暴枪 6/马卡洛夫 7/投掷武器盒 3/能量剑 6 |
| 增援 | 7 | 特工增援/辛迪加突击机器人/医疗机器人/破坏机器人 |
| 弹药 | 6 | 9mm 弹匣 2/穿甲 2/空尖 3 |
| 机甲 | 5 | Dark H.O.N.K. 80/Dark Gygax 60/**Mauler 100** |
| 基地钥匙 | 5 | 辛迪加军械实验室 30/生化武器实验室/化学厂/Lopez |
| 承包商 | 4 | 合同重掷 0/承包商指示器 1/Fulton 套件 1 |
| 增援2 | 3 | 小丑增援 20/猿人特工 7 |
| 特殊 | 1 | 辛迪加自动手术器 5 |
| 种族限定 | 1 | 超亮灯笼 2 |

## 1.4 承包商契约（Contractor）

**代码**: `contractor/`（5 文件 608 行）

| 机制 | 值 |
|---|---|
| 合同 | 目标+赎金+投放点（提取舱回收） |
| 赎金 | **7,500-15,000 信用**（NOVA 从 1,800-4,500 上调） |
| 分成 | 承包商 35%（NOVA） |
| 奖金 | 大单 +9-13 TC / 中单 +6-8 / 小单 +2-4 |

---


---

## 三、Uplink 物品全录（395 件）


**代码**: `code/modules/uplink/`（20 文件 3,746 行）

**说明**: TC=电信水晶（叛徒货币）；核弹弹药/增援等部分物品价格由队伍折扣动态决定（显示 ?）


## 角色限定（42）

### Uplink 补录（70 件 · 2026-08-06 源码核对）

> 此前文档附录 B 收录 323 件，经源码全量核对（`code/modules/uplink/` 20 文件 324 件 + NOVA traitor-uplinks 69 件 + 天关 1 件 + modular_implants 1 件 = **395 件**），确认缺失 70 件（61 零重叠 + 9 同名独立 datum）。以下为补录清单：

| 物品 | 分类 | TC | 描述 |
|---|---|---|---|
| **Cybersun 'Scalpel' NIF-Cutter** | 未分类 | 3 | A modified version of a NIFSoft remover that allows the user |
| **Ammo Pouch** | 未分类 | 1 | A small yet large enough pouch that can fit in your pocket,  |
| **Breacher Twelve-Gauge Box** | 未分类 | =/datum/uplink_item/low_cost/ammunition | A stack of twelve of breacher shells, superb at destroying a |
| **Buckshot Twelve-Gauge Box** | 未分类 | =/datum/uplink_item/low_cost/ammunition | A standard-sized box of buckshot shells. |
| **Enforcer 10mm Magazine Case** | 未分类 | =/datum/uplink_item/low_cost/ammunition | A case containing three additional 12-round 10mm magazines,  |
| **Enforcer 10mm Armour Piercing Magazine** | 未分类 | =/datum/uplink_item/low_cost/ammunition | An additional 12-round 10mm magazine, compatible with the En |
| **Enforcer 10mm Incendiary Magazine** | 未分类 | =/datum/uplink_item/low_cost/ammunition | An additional 12-round 10mm magazine, compatible with the En |
| **Enforcer 10mm Hollow Point Magazine** | 未分类 | =/datum/uplink_item/low_cost/ammunition | An additional 12-round 10mm magazine, compatible with the En |
| **Flechette Twelve-Gauge Box** | 未分类 | =/datum/uplink_item/low_cost/ammunition | A standard-sized box of flechette shells. |
| **Incendiary Twelve-Gauge Box** | 未分类 | =/datum/uplink_item/low_cost/ammunition | A standard-sized box of incendiary shells. |
| **Milspec Buckshot Box** | 未分类 | =/datum/uplink_item/medium_cost/ammunition | A standard-sized box of 15 Scarborough-manufactured, hot-loa |
| **Milspec Slugs Box** | 未分类 | =/datum/uplink_item/medium_cost/ammunition | A standard-sized box of 15 Scarborough-manufactured, hot-loa |
| **.357 Haywire+ (EMP) speedloader** | 未分类 | =/datum/uplink_item/high_cost/ammunition | A speed loader that contains seven additional .357 Magnum Ha |
| **Slug Twelve-Gauge Box** | 未分类 | =/datum/uplink_item/low_cost/ammunition | A standard-sized box of slug shells. |
| **10mm Handgun Magazine (Ansem)** | 未分类 | =/datum/uplink_item/low_cost/ammunition | An additional 8-round 10mm magazine, compatible with the Ans |
| **10mm Armour Piercing Magazine (Ansem)** | 未分类 | =/datum/uplink_item/low_cost/ammunition | An additional 8-round 10mm magazine, compatible with the Ans |
| **10mm Hollow Point Magazine (Ansem)** | 未分类 | =/datum/uplink_item/low_cost/ammunition | An additional 8-round 10mm magazine, compatible with the Ans |
| **10mm Incendiary Magazine (Ansem)** | 未分类 | =/datum/uplink_item/low_cost/ammunition | An additional 8-round 10mm magazine, compatible with the Ans |
| **.45 SMG Magazine (C-20r)** | 未分类 | =/datum/uplink_item/low_cost/ammunition | An additional 24-round .45 magazine suitable for use with th |
| **.45 Armor Piercing SMG Magazine (C-20r)** | 未分类 | =/datum/uplink_item/low_cost/ammunition | An additional 24-round .45 magazine suitable for use with th |
| **.45 Hollow Point SMG Magazine (C-20r)** | 未分类 | =/datum/uplink_item/low_cost/ammunition | An additional 24-round .45 magazine suitable for use with th |
| **.45 Incendiary SMG Magazine (C-20r)** | 未分类 | =/datum/uplink_item/low_cost/ammunition | An additional 24-round .45 magazine suitable for use with th |
| **Bulletproof Armor Vest** | 未分类 | =/datum/uplink_item/low_cost | A Type III heavy bulletproof vest that excels in protecting  |
| **Combat Jackboots** | 未分类 | 1 | High speed, low drag combat boots. |
| **Guerilla Gloves** | 未分类 | 1 | A pair of highly robust combat gripper gloves that excels at |
| **Henchmen Bundle** | 未分类 | 4 | A set of five armored henchmen outfits! Each set comes with  |
| **Syndicate Helmet** | 未分类 | =/datum/uplink_item/low_cost | An extremely robust, space-worthy helmet in a nefarious red  |
| **Syndicate Bunny Kit** | 未分类 | 8 | Straight from the dens of Carota. The Tactical Rabbit Ensemb |
| **Alacrán PDW Case** | 未分类 | =/datum/uplink_item/low_medium_cost/weaponry | A compact bullpup PDW chambered in .27-54 Cesarzowa. \ Comes |
| **Ansem Pistol Case** | 未分类 | =/datum/uplink_item/low_cost/weaponry | A small, easily concealable handgun that uses 10mm auto roun |
| **Boarder-980 Grenade Launcher Case** | 未分类 | =/datum/uplink_item/medium_cost/weaponry | A weapon case featuring the Boarder-980 grenade launcher, ch |
| **Carwo-Cawil M64 Shotgun Case** | 未分类 | =/datum/uplink_item/low_cost/weaponry | A twelve guage shotgun with an eight shell capacity undernea |
| **"Covenant" elite energy sword** | 未分类 | =/datum/uplink_item/low_cost/weaponry | Much like its more conventional sibling, the energy sword, t |
| **Enforcer-TEN Handgun Case** | 未分类 | =/datum/uplink_item/low_cost/weaponry | A weapon case containing the Enforcer-TEN combat handgun, al |
| **Energy Shield** | 未分类 | =/datum/uplink_item/medium_cost/weaponry | A highly deflective energy shield, pairs well with the energ |
| **Donksoft Riot SMG Case** | 未分类 | 1 | A case containing an innocent-looking toy SMG designed to fi |
| **Modified Sawn-off Shotgun Case** | 未分类 | =/datum/uplink_item/high_cost/weaponry | Range isn't an issue when you can bring your victim to you. |
| **Allstar Laser-Carbine Case** | 未分类 | 2 | A modified laser gun which can shoot far faster, but each sh |
| **Scarborough C-20r Case** | 未分类 | =/datum/uplink_item/medium_cost/weaponry | A fully-loaded Scarborough Arms bullpup submachine gun. The  |
| **Modified Revolver Case** | 未分类 | =/datum/uplink_item/low_cost/weaponry | A .357 Magnum revolver firing ricochet bullets, in case you  |
| **Cybersun S-120 Case** | 未分类 | =/datum/uplink_item/low_medium_cost/weaponry | A laser gun primarily used by syndicate security guards. It  |
| **Shitzu Magfed Shotgun Case** | 未分类 | =/datum/uplink_item/high_cost/weaponry | Everyone says a dog is man's best friend, let's change that  |
| **Carwo-Cawil Sindano Case** | 未分类 | =/datum/uplink_item/low_medium_cost/weaponry | A Sindano SMG, with spare lethal-and-non-lethal ammo, and th |
| **Carwo-Cawil MMR-2543E Assault Rifle** | 未分类 | =/datum/uplink_item/medium_cost/weaponry | A heavy battle rifle, this one seems to be painted tacticool |
| **Szot Dynamica 'Wyłom' AMR Case** | 未分类 | =/datum/uplink_item/medium_cost/weaponry | A massive, outdated beast of an anti materiel rifle that was |
| **Katana** | 未分类 | =/datum/uplink_item/medium_cost/weaponry | An extremely sharp and robust sword folded over nine thousan |
| **Syndie Fitness Nunchuks** | 未分类 | =/datum/uplink_item/low_cost/weaponry | Heavyweight titanium nunchucks that can be used to knock out |
| **Ammo Fabricator Advanced Lethal Authentication Module** | 未分类 | =/datum/uplink_item/medium_cost | A Gorlex Marauders-modified ammunition fabricator module, lo |
| **Syndicate Cyborg Upgrade** | 未分类 | =/datum/uplink_item/low_cost | A marvel of modern syndicate technology; a syndicate borg hi |
| **Glue** | 未分类 | =/datum/uplink_item/medium_cost | A cheap bottle of one use syndicate brand super glue. \ Use  |
| **Syndikush Green Crack cart** | 未分类 | =/datum/uplink_item/low_cost | A cheap Chinese vape cart that contains a potent combination |
| **Syndicate Jaws of Life** | 未分类 | =/datum/uplink_item/low_cost | Based on a Nanotrasen model, this powerful tool can be used  |
| **Blood Steal NIFSoft** | 未分类 | =/datum/uplink_item/high_cost | A combat-oriented nanite package that transforms the user's  |
| **Grimoire Asclepius NIFSoft** | 未分类 | =/datum/uplink_item/low_cost | An emergency surgical NIFSoft containing a full set of nanit |
| **Thermal Lens NIFSoft** | 未分类 | =/datum/uplink_item/medium_cost | A military-grade visual enhancement package that modifies th |
| **Grimoire Opera NIFSoft** | 未分类 | =/datum/uplink_item/low_cost | A specialized NIFSoft for technicians, creating nanite-based |
| **Xeno-organ Implant Kit** | 未分类 | =/datum/uplink_item/medium_cost | An organ implant kit filled with illegally obtained xenomorp |
| **Fake Announcement** | 未分类 | =/datum/uplink_item/low_cost | A device that allows you to spoof an announcement to the sta |
| **Reinforced Knuckleduster** | 未分类 | =/datum/uplink_item/low_cost | A compact, concealable set of reinforced knuckles. Quiet, fa |
| **Ransomware Neuroware Chip** | 未分类 | =/datum/uplink_item/low_cost | This Syndicate neuroware chip contains CrypSys, a package of |
| **Thieves Gloves** | 未分类 | =/datum/uplink_item/low_cost | Gloves which enhance the wearer's ability to strip small ite |
| **Extra Large Syndicate Shotglasses** | 未分类 | 1 | These modified shot glasses can hold up to 50 units of booze |
| **MODsuit Flamethrower** | 未分类 | =/datum/uplink_item/low_cost | A custom-manufactured flamethrower, used to burn through you |
| **MODsuit Ionic Jump Jet** | 未分类 | =/datum/uplink_item/medium_cost | A specialised ionic thruster which provides a short but powe |
| **Nakamura Standard MODsuit** | 未分类 | =/datum/uplink_item/low_cost/modsuit | A third-generation, modular civilian class suit by Nakamura  |
| **Contractor MODsuit** | 未分类 | =/datum/uplink_item/low_cost/modsuit | A rare depart from the Syndicate's usual color scheme, this  |
| **Elite MODsuit** | 未分类 | =/datum/uplink_item/high_cost/modsuit | An evolution of the syndicate suit, featuring a bulkier buil |
| **MODsuit Donksoft Recycler** | 未分类 | =/datum/uplink_item/low_cost | A mod module collects and repackages fired foam darts (and g |
| **MODsuit Bulwark** | 未分类 | =/datum/uplink_item/high_cost | Layers upon layers of shock dampening plates, just to stop y |
| **辛迪加独立旅传呼密信** | 未分类 | 210 | 团结联盟共和国的死敌叛军独立旅的呼叫工具，如果这里需要被彻底摧毁的话。那么我们只需在通讯控制台使用这个，然后静候毁灭到来 |

| 物品 | 花费 | 用途 |
|---|---|---|
| **高级整形手术程序**（Advanced Plastic Surgery Program） | 1 TC | 高级整容手术盘 |
| **闹鬼魔法八号球**（Haunted Magic Eightball） | 2 TC | 预言球（随机答案） |
| **GLA 品牌邮件伪造套件**（GLA Brand Mail Counterfeit Kit） | 2 TC | 伪造邮件 |
| **有机资本扰乱病毒**（Organic Capital Disturbance Virus） | 2 TC | 扰乱岗位分配病毒 |
| **古代工具箱**（Ancient Toolbox） | 2 TC | 标志性古代工具箱 |
| **辛迪加牌 MMI 假脑**（Syndicate Brand MMI） | 2 TC | 辛迪加法律假脑 |
| **Molt'Obeso 酱瓶**（Molt'Obeso Sauce Bottle） | 2 TC | 特殊酱料 |
| **重度改装弹簧锁 MOD 服模块**（Heavily Modified Springlock MODsuit Module） | 2 TC | 弹簧锁陷阱模块 |
| **隐藏武器舱**（Concealed Weapon Bay） | 3 TC | 隐藏武器空间 |
| **超级搞笑撞针**（Ultra Hilarious Firing Pin） | 4 TC | 枪变玩具（搞笑） |
| **爆炸烫手山芋**（Exploding Hot Potato） | 4 TC | 爆炸物（传递） |
| **蜜蜂熏烟器**（Bee Smoker） | 4 TC | 大麻烟雾（蜜蜂） |
| **猿人特工补给**（Simian Agent Supplies） | 4 TC | 猿人特工装备 |
| **公文包内置枪械扳机**（Briefcase Embedded Firearm Trigger） | 4 TC | 公文包内置枪 |
| **反向捕熊夹**（Reverse Bear Trap） | 5 TC | 陷阱（捕熊） |
| **洗脑手术程序**（Brainwashing Surgery Program） | 5 TC | 洗脑手术盘 |
| **动能加速器压力模块**（Kinetic Accelerator Pressure Mod） | 5 TC | PKA 压力强化 |
| **超震荡二极管盘**（Hyperconcussive Diode Disk） | 5 TC | 高能震荡二极管 |
| **EZ 清洁手雷捆绑包**（EZ Clean Grenade Bundle） | 6 TC | 清洁剂手雷 |
| **大猩猩立方**（Gorilla Cube） | 6 TC | 水泡成大猩猩 |
| **猿人特工增援**（Simian Agent Reinforcements） | 6 TC | 召唤猿人特工 |
| **超级无敌搞笑撞针**（Super Ultra Hilarious Firing Pin） | 7 TC | 更强搞笑撞针 |
| **战斗烘焙套件**（Combat Bakery Kit） | 7 TC | 烘焙武器套件 |
| **邪恶幼苗**（Evil Seedling） | 8 TC | 危险植物种子 |
| **公文包内置枪械扳机（组合装）**（Briefcase Embedded Firearm Trigger (Combo Deal)） | 8 TC | 公文包枪+折扣 |
| **激光手臂植入体**（Laser Arm Implant） | 10 TC | 手臂激光枪 |
| **香蕉奶油派炮**（Banana Cream Pie Cannon） | 10 TC | 发射奶油派（滑倒） |
| **澳式史莱姆变异器**（Australicus Slime Mutator） | 10 TC | 史莱姆变异 |
| **屠夫肉钩**（Butcher's Meat Hook） | 11 TC | 链钩拉人 |
| **一次性哨戒炮**（Disposable Sentry Gun） | 11 TC | 部署自动哨戒炮 |
| **辛迪加钢筋弩**（Syndicate Rebar Crossbow） | 12 TC | 专业钢筋弩 |
| **高级哑剧指南系列**（Guide to Advanced Mimery Series） | 12 TC | 哑剧武术 |
| **试剂镖枪**（Reagent Dartgun） | 12 TC | 注射试剂飞镖 |
| **改装注射枪**（Modified Syringe Gun） | 14 TC | 发射 DNA 注射器 |
| **反向左轮**（Reverse Revolver） | 14 TC | 弹匣在前的左轮 |
| **冲击炮**（Blast Cannon） | 14 TC | 高能冲击炮 |
| **巨型症血清自注射器**（Magillitis Serum Autoinjector） | 15 TC | 变巨血清 |
| **小丑炸弹**（Clown Bomb） | 15 TC | 小丑大笑炸弹 |
| **古代连体服**（Ancient Jumpsuit） | 20 TC | 无属性旧连体服 |
| **小丑汽车**（Clown Car） | 20 TC | 小丑车（撞人） |
| **恩典**（His Grace） | 20 TC | 教会武器，吞噬灵魂 |
| **缄默隐形刺杀机甲**（Reticence Cloaked Assasination exosuit） | 20 TC | 隐形刺杀机甲 |

## 装置工具（37）

| 物品 | 花费 | 用途 |
|---|---|---|
| **辛迪加肥皂**（Syndicate Soap） | 1 TC | 滑倒他人+清血渍 |
| **自适应纸板人**（Adaptive Cardboard Cutouts） | 1 TC | 纸板伪装（可投影） |
| **SyndEye 程序**（SyndEye Program） | 1 TC | 无人机视角应用 |
| **胸挂装具**（Chest Rig） | 1 TC | 7 格胸挂带 |
| **诱饵核认证磁盘**（Decoy Nuclear Authentication Disk） | 1 TC | 假磁盘迷惑安保 |
| **失效安全 Uplink 代码**（Failsafe Uplink Code） | 1 TC | Uplink 自毁代码 |
| **辛迪加满装工具箱**（Full Syndicate Toolbox） | 1 TC | 全套辛迪加工具 |
| **超级尖利胶带**（Super Pointy Tape） | 1 TC | 尖锐胶带（武器） |
| **辛迪加攀爬钩**（Syndicate Climbing Hook） | 1 TC | 钩索攀爬 |
| **可疑行李袋**（Suspicous Duffel Bag） | 2 TC | 可疑包（隐藏物品） |
| **辛迪加加密密钥**（Syndicate Encryption Key） | 2 TC | 辛迪加加密频道 |
| **电车遥控器**（Tram Remote Control） | 2 TC | 控制电车 |
| **战斗急救包**（combat first aid kit） | 3 TC | 战斗医疗包 |
| **辛迪加手术医疗箱**（Full Syndicate Surgery Medkit） | 3 TC | 全套手术工具 |
| **气闸认证覆盖卡**（Airlock Authentication Override Card） | 3 TC | 打开任意气闸 |
| **放射性微型激光器**（Radioactive Microlaser） | 3 TC | 辐射激光 |
| **偏光隐形眼镜**（Polarized Contact Lenses） | 3 TC | 防闪光眼镜 |
| **热成像眼镜**（Thermal Imaging Glasses） | 4 TC | 热视觉 |
| **F.R.A.M.E. 磁盘**（F.R.A.M.E. disk） | 4 TC | 武器框架程序 |
| **密码序列器**（Cryptographic Sequencer） | 4 TC | 黑客工具（开锁/撬门） |
| **破解版 AI 法律上传模块**（Hacked AI Law Upload Module） | 4 TC | 给 AI 上传自定义法律 |
| **电力信标**（Power Beacon） | 4 TC | 接入电网 |
| **辛迪加救生颚**（Syndicate Jaws of Life） | 4 TC | 电锯（破拆） |
| **辛迪加战斗军医包**（Syndicate Combat Medic Kit） | 4 TC | 战斗医疗套件 |
| **辛迪加智慧药剂**（Syndicate Sentience Potion） | 4 TC | 赋予生物智能 |
| **辛迪加魔典**（Syndicate Tome） | 5 TC | 邪教知识书 |
| **二进制翻译密钥**（Binary Translator Key） | 5 TC | 听懂机器人语言 |
| **兴奋剂注射器**（Stimpack） | 5 TC | 5 次兴奋剂 |
| **公文包发射台**（Briefcase Launchpad） | 6 TC | 公文包式发射装置 |
| **CRAB-17 协议手机**（Protocol CRAB-17 Phone） | 7 TC | 特殊协议手机 |
| **催眠闪光**（Hypnotic Flash） | 7 TC | 催眠目标闪光 |
| **实验性辛迪加传送器**（Experimental Syndicate Teleporter） | 8 TC | 实验传送 |
| **电力水槽**（Power Sink） | 11 TC | 吸干电网（断电） |
| **催眠手雷**（Hypnotic Grenade） | 12 TC | 催眠范围目标 |
| **医疗光束枪模块**（Medbeam Gun Module） | 15 TC | 远程治疗光束 |
| **辛迪加战斗医疗套件**（Syndicate Combat Medical Suite） | 15 TC | 高级战斗医疗 |
| **突击舱瞄准装置**（Assault Pod Targeting Device） | 30 TC | 选择突击舱落点 |

## 核弹弹药（37）

| 物品 | 花费 | 用途 |
|---|---|---|
| **盈余智能冲锋枪弹匣（智能枪）**（Surplus Smart-SMG Magazine (Smartgun)） | 1 TC | 智能冲锋枪弹匣 |
| **.357 追心快速装填器（左轮）**（.357 Heartseeker Speed Loader (Revolver)） | 3 TC | .357 追心弹（追踪目标） |
| **.45 燃烧冲锋枪弹匣（C-20r）**（.45 Incendiary SMG Magazine (C-20r)） | 4 TC | .45 燃烧弹匣 |
| **.50 BMG 盈余弹匣盒（AMSR）**（.50 BMG Surplus Magazine Box (AMSR)） | 7 TC | .50 BMG 反器材弹匣 |
| **12 号鹿弹弹鼓（斗牛犬）**（12g Buckshot Drum (Bulldog)） | ? TC | 斗牛犬霰弹枪鹿弹鼓 |
| **12 号独头弹弹鼓（斗牛犬）**（12g Slug Drum (Bulldog)） | ? TC | 斗牛犬霰弹枪独头弹鼓 |
| **12 号箭形弹（斗牛犬）**（12g Flechette Shells (Bulldog)） | ? TC | 斗牛犬霰弹枪箭形弹 |
| **12 号 Donk 尖刺箭形弹匣盒（斗牛犬）**（12g Donk Co. 'Donk Spike' Flechette Magazine Box (Bulldog)） | ? TC | Donk 尖刺箭形弹（麻醉） |
| **12 号龙息弹弹鼓（斗牛犬）**（12g Dragon's Breath Drum (Bulldog)） | ? TC | 龙息燃烧弹鼓 |
| **12 号流星弹（斗牛犬）**（12g Meteorslug Shells (Bulldog)） | ? TC | 流星弹（击退） |
| **10mm 手枪弹匣（Ansem）**（10mm Handgun Magazine (Ansem)） | ? TC | Ansem 手枪 10mm 弹匣 |
| **10mm 穿甲弹匣（Ansem）**（10mm Armour Piercing Magazine (Ansem)） | ? TC | 10mm 穿甲弹 |
| **10mm 空尖弹匣（Ansem）**（10mm Hollow Point Magazine (Ansem)） | ? TC | 10mm 空尖弹 |
| **10mm 燃烧弹匣（Ansem）**（10mm Incendiary Magazine (Ansem)） | ? TC | 10mm 燃烧弹 |
| **.45 冲锋枪弹匣（C-20r）**（.45 SMG Magazine (C-20r)） | ? TC | .45 冲锋枪弹匣 |
| **.45 穿甲冲锋枪弹匣（C-20r）**（.45 Armor Piercing SMG Magazine (C-20r)） | ? TC | .45 穿甲弹匣 |
| **.45 空尖冲锋枪弹匣（C-20r）**（.45 Hollow Point SMG Magazine (C-20r)） | ? TC | .45 空尖弹匣 |
| **.357 快速装填器（左轮）**（.357 Speed Loader (Revolver)） | ? TC | .357 快速装填 |
| **.357 相位快速装填器（左轮）**（.357 Phasic Speed Loader (Revolver)） | ? TC | .357 相位穿墙弹 |
| **84mm 高爆火箭束（火箭筒）**（84mm HE Rocket Bouquet (Rocket Launcher)） | ? TC | 火箭筒高爆弹 |
| **84mm 高爆穿甲火箭（火箭筒）**（84mm HEAP Rocket (Rocket Launcher)） | ? TC | 火箭筒高爆穿甲弹 |
| **7mm 弹匣盒（L6 SAW）**（7mm Box Magazine (L6 SAW)） | ? TC | L6 机枪 7mm 弹匣 |
| **7mm 穿甲弹匣盒（L6 SAW）**（7mm (Armor Penetrating) Box Magazine (L6 SAW)） | ? TC | L6 穿甲弹 |
| **7mm 空尖弹匣盒（L6 SAW）**（7mm (Hollow-Point) Box Magazine (L6 SAW)） | ? TC | L6 空尖弹 |
| **7mm 燃烧弹匣盒（L6 SAW）**（7mm (Incendiary) Box Magazine (L6 SAW)） | ? TC | L6 燃烧弹 |
| **7mm 比赛弹匣盒（L6 SAW）**（7mm (Match) Box Magazine (L6 SAW)） | ? TC | L6 比赛弹（精准） |
| **.223 顶装弹匣（M-90gl）**（.223 Toploader Magazine (M-90gl)） | ? TC | M-90gl 卡宾 .223 弹匣 |
| **.223 顶装相位弹匣（M-90gl）**（.223 Toploader Phasic Magazine (M-90gl)） | ? TC | M-90gl 相位穿墙弹 |
| **.50 BMG 弹匣（AMSR）**（.50 BMG Magazine (AMSR)） | ? TC | 反器材狙击 .50 弹匣 |
| **.50 BMG 穿甲弹匣（AMSR）**（.50 BMG Penetrator Magazine (AMSR)） | ? TC | .50 穿甲弹 |
| **.50 BMG 燃烧弹匣（AMSR）**（.50 BMG Incendiary Magazine (AMSR)） | ? TC | .50 燃烧弹 |
| **.50 BMG 干扰弹匣（AMSR）**（.50 BMG Disruptor Magazine (AMSR)） | ? TC | .50 电磁干扰弹 |
| **.50 BMG 精确射手弹匣（AMSR）**（.50 BMG Marksman Magazine (AMSR)） | ? TC | .50 精确弹 |
| **40mm 高爆榴弹盒（榴弹发射器）**（40mm HE Shell Box (Grenade Launchers)） | ? TC | 40mm 高爆榴弹 |
| **40mm 橡胶榴弹盒（榴弹发射器）**（40mm Rubber Shell Box (Grenade Launchers)） | ? TC | 40mm 橡胶（非致命） |
| **40mm 钛破片榴弹盒（榴弹发射器）**（40mm Titanium Flak Shell Box (Grenade Launchers)） | ? TC | 40mm 钛破片榴弹 |
| **40mm 燃烧榴弹盒（榴弹发射器）**（40mm Incendiary Shell Box (Grenade Launchers)） | ? TC | 40mm 燃烧榴弹 |

## 爆炸物（29）

| 物品 | 花费 | 用途 |
|---|---|---|
| **滑倒天启集束弹**（Slipocalypse Clusterbang） | 1 TC | 连锁滑倒炸弹 |
| **C-4 炸药**（Composition C-4） | 1 TC | 可塑炸药 |
| **破片手雷**（Frag Grenade） | 1 TC | 标准破片手雷 |
| **辛迪加起爆器**（Syndicate Detonator） | 1 TC | 远程引爆 |
| **X-4 炸药**（Composition X-4） | 2 TC | 高爆可塑炸药 |
| **EMP 手雷+植入器套件**（EMP Grenades and Implanter Kit） | 2 TC | EMP 攻击+植入 |
| **烟雾手雷**（Smoke Grenades） | 2 TC | 烟雾掩护 |
| **披萨炸弹**（Pizza Bomb） | 2 TC | 披萨盒伪装炸弹 |
| **辛迪加迷你炸弹**（Syndicate Minibomb） | 2 TC | 迷你定时炸弹 |
| **E-2 裂地弹**（E-2 Earthcracker） | 2 TC | 地震弹 |
| **X-4 炸药**（Composition X-4） | 2 TC | 高爆可塑炸药 |
| **辛迪加迷你炸弹**（Syndicate Minibomb） | 2 TC | 迷你定时炸弹 |
| **披萨炸弹**（Pizza Bomb） | 2 TC | 披萨盒伪装炸弹 |
| **胡须老师手雷**（Teachstache Grenade） | 3 TC | 胡须爆炸（搞笑） |
| **香蕉炸弹**（Bombanana） | 4 TC | 香蕉形炸弹 |
| **C-4 炸药包**（Bag of C-4 explosives） | 5 TC | C-4×5 |
| **破片手雷盒**（Frag Grenade Box） | 5 TC | 破片手雷×5 |
| **C-4 炸药包**（Bag of C-4 explosives） | 5 TC | C-4×5 |
| **Detomatix 磁盘**（Detomatix disk） | 6 TC | 远程引爆 PDA |
| **辛迪加 EMP 炸弹**（Syndicate EMP Bomb） | 6 TC | EMP 炸弹 |
| **辛迪加 EMP 炸弹**（Syndicate EMP Bomb） | 7 TC | EMP 炸弹 |
| **辛迪加炸弹**（Syndicate Bomb） | 8 TC | 高威力炸弹 |
| **X-4 炸药包**（Bag of X-4 explosives） | 10 TC | X-4×5 |
| **辛迪加炸弹**（Syndicate Bomb） | 11 TC | 高威力炸弹 |
| **武器级皮纳塔套件**（Weapons Grade Pinata Kit） | 12 TC | 皮纳塔爆炸物 |
| **小丑炸弹**（Clown Bomb） | 15 TC | 小丑大笑炸弹 |
| **扫兴手雷盒**（Buzzkill Grenade Box） | ? TC | 蜂群骚扰手雷 |
| **真菌结核手雷盒**（Fungal Tuberculosis Grenade Box） | ? TC | 真菌疾病手雷 |
| **碎肉机投送手雷盒**（Viscerator Delivery Grenade Box） | ? TC | 碎肉机生物手雷 |

## 间谍专属（29）

| 物品 | 花费 | 用途 |
|---|---|---|
| **辛迪加弓手**（Syndicate Bowman） | 1 TC | 静音弓 |
| **扩音器**（Megaphone） | 1 TC | 放大声音 |
| **战斗手套**（Combat Gloves） | 1 TC | 战斗拳套 |
| **鹿弹盒**（Box of Buckshot） | 1 TC | 霰弹鹿弹 |
| **钢笔炸弹**（Penbang） | 1 TC | 钢笔形炸弹 |
| **相机闪光**（Camera Flash） | 1 TC | 闪光装置 |
| **靴中匕首**（Boot Dagger） | 1 TC | 靴藏匕首 |
| **战斗手套 Plus**（Combat Gloves Plus） | ? TC | 强化战斗手套 |
| **游击手套**（Guerrilla Gloves） | ? TC | 游击战术手套 |
| **藤蔓种子**（Kudzu） | ? TC | 致命藤蔓 |
| **战斗刀**（Combat Knife） | ? TC | 战斗匕首 |
| **弹簧刀**（Switchblade） | ? TC | 可折叠刀 |
| **安保 HUD 植入体**（SecHUD Implant） | ? TC | 安保信息植入 |
| **栓动步枪**（Bolt-Action Rifle） | ? TC | 栓动步枪 |
| **连发霰弹枪**（Cycler Shotgun） | ? TC | 泵动霰弹枪 |
| **斗牛犬霰弹枪**（Bulldog Shotgun） | ? TC | 自动霰弹枪 |
| **Ansem 手枪**（Ansem Pistol） | ? TC | 10mm 手枪 |
| **马卡洛夫手枪**（Makarov Pistol） | ? TC | 9mm 手枪 |
| **Donksoft 防暴手枪**（Donksoft Riot Pistol） | ? TC | 非致命手枪 |
| **火箭筒**（Rocket Launcher） | ? TC | 火箭发射器 |
| **破门独头弹盒**（Box of Breacher Slugs） | ? TC | 破门独头弹 |
| **独头弹盒**（Box of Slugs） | ? TC | 霰弹独头弹 |
| **潜行腰带**（Stealth Belt） | ? TC | 潜行装备带 |
| **武士刀**（Katana） | ? TC | 近战武士刀 |
| **辛迪加急救包**（Syndicate First Medic Kit） | ? TC | 医疗急救 |
| **辛迪加健身双节棍**（Syndie Fitness Nunchuks） | ? TC | 双节棍 |
| **随机怪物立方**（Random Monster Cubes） | ? TC | 随机怪物 |
| **睡鲤武术卷轴**（Sleeping Carp Technique） | ? TC | 睡鲤武术（弹道反弹） |
| **蜘蛛咬武术卷轴**（Spider Bite Technique） | ? TC | 蜘蛛咬武术 |

## 武器包（22）

| 物品 | 花费 | 用途 |
|---|---|---|
| **超级搞笑撞针**（Ultra Hilarious Firing Pin） | 1 TC | 枪变玩具（搞笑） |
| **盈余智能冲锋枪（Flukie）**（Surplus Smart-SMG (Flukie)） | 2 TC | 低配智能冲锋枪 |
| **香蕉合金能量剑**（Bananium Energy Sword） | 3 TC | 无伤滑倒剑 |
| **超级无敌搞笑撞针**（Super Ultra Hilarious Firing Pin） | 4 TC | 更强搞笑撞针 |
| **玩具冲锋枪**（Toy Submachine Gun） | 5 TC | 玩具枪（搞笑） |
| **香蕉奶油派炮**（Banana Cream Pie Cannon） | 10 TC | 发射奶油派（滑倒） |
| **玩具机枪**（Toy Machine Gun） | 10 TC | 玩具机枪（搞笑） |
| **香蕉合金能量盾**（Bananium Energy Shield） | 16 TC | 滑倒能量盾 |
| **核心装备盒（基础）**（Core Equipment Box (Essential)） | ? TC | 基础装备盒 |
| **斗牛犬霰弹枪箱（中等）**（Bulldog Shotgun Case (Moderate)） | ? TC | 自动霰弹枪+弹药 |
| **Ansem 手枪箱（简单/备用）**（Ansem Pistol Case (Easy/Spare)） | ? TC | 10mm 手枪箱 |
| **C-20r 冲锋枪箱（简单）**（C-20r Submachine Gun Case (Easy)） | ? TC | .45 冲锋枪箱 |
| **能量盾+剑箱（极难）**（Energy Shield and Sword Case (Very Hard)） | ? TC | 能量盾+剑组合 |
| **CQC 装备箱（极难）**（CQC Equipment Case (Very Hard)） | ? TC | CQC 武术+装备 |
| **辛迪加左轮箱（中等）**（Syndicate Revolver Case (Moderate)） | ? TC | 辛迪加左轮箱 |
| **Dardo-RE 火箭推进榴弹发射器（困难）**（Dardo-RE Rocket Propelled Grenade Launcher (Hard)） | ? TC | 火箭榴弹发射器 |
| **L6 班用自动武器（中等）**（L6 Squad Automatic Weapon (Moderate)） | ? TC | L6 轻机枪 |
| **M-90gl 卡宾枪箱（困难）**（M-90gl Carbine Case (Hard)） | ? TC | .223 榴弹卡宾 |
| **反器材狙击步枪公文包（困难）**（Anti-Materiel Sniper Rifle Briefcase (Hard)） | ? TC | .50 反器材狙击 |
| **双刃能量剑箱（极难）**（Double-Bladed Energy Sword Case (Very Hard)） | ? TC | 双刃能量剑 |
| **掷弹兵腰带+气动榴弹发射器套件（困难）**（Grenadier's Belt and Pneumatic Grenade Launcher Kit (Hard)） | ? TC | 榴弹兵套装 |
| **泵动榴弹发射器套件（中等）**（Pump-Action Grenade Launcher Kit (Moderate)） | ? TC | 泵动榴弹发射器 |

## 护甲（16）

| 物品 | 花费 | 用途 |
|---|---|---|
| **MOD 服变色模块**（MODsuit Chameleon Module） | 1 TC | MOD 服伪装 |
| **MOD 服装甲压缩模块**（MODsuit Plate Compression Module） | 1 TC | 装甲强化 |
| **MOD 服防滑模块**（MODsuit Anti-Slip Module） | 1 TC | 防滑 |
| **MOD 服减震模块**（MODsuit Shock-Absorber Module） | 1 TC | 防震 |
| **MOD 服注射器模块**（MODsuit Injector Module） | 2 TC | 内置注射器 |
| **MOD 服枪套模块**（MODsuit Holster Module） | 2 TC | 内置枪套 |
| **MOD 服热视觉面罩模块**（MODsuit Thermal Visor Module） | 2 TC | 热视觉 |
| **MOD 服幽灵隐形模块**（MODsuit wraith cloaking module） | 2 TC | 幽灵隐形 |
| **辛迪加 AI 升级**（Syndicate AI Upgrade） | 4 TC | AI 升级（辛迪加） |
| **辛迪加太空服**（Syndicate Space Suit） | 4 TC | 太空防护服 |
| **MOD 服高级 EMP 护盾模块**（MODsuit Advanced EMP Shield Module） | 5 TC | EMP 护盾 |
| **渗透者 MOD 服**（Infiltrator MODsuit） | 6 TC | 轻量潜行 MOD 服 |
| **MOD 服能量护盾模块**（MODsuit Energy Shield Module） | 8 TC | 能量护盾 |
| **辛迪加 MOD 服**（Syndicate MODsuit） | 8 TC | 辛迪加装甲 MOD 服 |
| **预载辛迪加智能卡**（Pre-Loaded Syndicate Intellicard） | 12 TC | 智能卡（控 AI） |
| **精英辛迪加 MOD 服**（Elite Syndicate MODsuit） | ? TC | 顶级装甲 MOD 服 |

## 潜行工具（15）

| 物品 | 花费 | 用途 |
|---|---|---|
| **AI 探测器**（Artificial Intelligence Detector） | 1 TC | 探测 AI 监视 |
| **无线电干扰器**（Radio Jammer） | 1 TC | 干扰无线电 |
| **走私者挎包**（Smuggler's Satchel） | 1 TC | 隐藏物品包 |
| **GLA 品牌邮件伪造装置**（GLA Brand Mail Counterfeit Device） | 1 TC | 伪造邮件 |
| **特工 ID 卡**（Agent Identification Card） | 2 TC | 伪装 ID（改名） |
| **变色套件**（Chameleon Kit） | 2 TC | 变色伪装全套 |
| **防滑变色鞋**（No-Slip Chameleon Shoes） | 2 TC | 防滑+变色 |
| **EMP 手电筒**（EMP Flashlight） | 2 TC | EMP 照明 |
| **暗语手册**（Codespeak Manual） | 3 TC | 暗语交流 |
| **重来套件**（Mulligan Kit） | 4 TC | 替换目标重置 |
| **禁用电信系统**（Disable Telecomms） | 4 TC | 破坏电信 |
| **法证伪造套件**（Forensics Spoofing Kit） | 5 TC | 伪造现场 |
| **战斗香蕉鞋**（Combat Banana Shoes） | 6 TC | 战斗+滑倒 |
| **全站断电触发器**（Trigger Stationwide Blackout） | 6 TC | 全站断电 |
| **变色投影仪**（Chameleon Projector） | 7 TC | 投影伪装 |

## 狠货（14）

| 物品 | 花费 | 用途 |
|---|---|---|
| **辛迪加扑克牌**（Syndicate Playing Cards） | 1 TC | 太空扑克牌，单分子刃可割物 |
| **辛迪加贴纸包**（Syndicate Sticker Pack） | 1 TC | 8 张可疑物品贴纸 |
| **辛迪加丧气海报包**（Syndicate Demotivational Poster Pack） | 1 TC | 丧气海报，打击士气 |
| **辛迪加喷漆罐**（Syndicate Spraycan） | 1 TC | 辛迪加风格喷漆 |
| **辛迪加香烟**（Syndicate Smokes） | 2 TC | 浓烟香烟，含万能素（治疗） |
| **盈余定位指示器**（Surplus Pinpointer） | 2 TC | 旧款定位器，追踪核磁盘 |
| **辛迪加现金公文包**（Syndicate Briefcase Full of Cash） | 3 TC | 5000 信用点，可贿赂/购物 |
| **帽子箱**（Hat Crate） | 5 TC | 随机帽子（装饰） |
| **盒装辛迪加小马**（a boxed syndicate pony） | 10 TC | 小马+心灵药水+玩具 |
| **辛迪加气球**（Syndicate Balloon） | 20 TC | 红色气球，纯炫富装饰 |
| **小丑服装**（Clown Costume） | ? TC | 小丑装，吓人用 |
| **安睡睡衣套装**（Sleepy Time Pajama Bundle） | ? TC | 血红睡衣，休息用 |
| **损坏的变色套件**（Broken Chameleon Kit） | ? TC | 残缺变色科技，部分伪装 |
| **中央指挥部官方服装**（CentCom Official Costume） | ? TC | 伪装中央指挥部官员 |

## 潜行武器（14）

| 物品 | 花费 | 用途 |
|---|---|---|
| **脱水太空鲤**（Dehydrated Space Carp） | 1 TC | 注水变太空鲤 |
| **消音器**（Suppressor） | 1 TC | 枪械消音 |
| **辛迪加枪套**（Syndicate Holster） | 1 TC | 隐藏枪套 |
| **能量匕首**（Energy Dagger） | 2 TC | 小型能量刀 |
| **食肉之血**（Carnivorous Blood） | 3 TC | 腐蚀血液 |
| **镖枪**（Dart Pistol） | 4 TC | 注射镖枪 |
| **催眠笔**（Sleepy Pen） | 4 TC | 催眠钢笔 |
| **折纸套件盒**（Boxed Origami Kit） | 4 TC | 折纸武器（鹤/飞机） |
| **辛迪加口红**（Syndie Lipstick） | 6 TC | 毒药口红 |
| **毒药套件**（Poison Kit） | 6 TC | 各种毒药 |
| **承包商警棍**（Contractor Baton） | 7 TC | 电击警棍 |
| **迷你能量弩**（Miniature Energy Crossbow） | 10 TC | 小型能量弩 |
| **武术卷轴+武术服装**（Martial Arts Scroll and Martial Arts Outfit） | 17 TC | 随机武术+道服 |
| **罗梅洛尔**（Romerol） | 25 TC | 生化病毒（僵尸化） |

## TC捆绑（13）

| 物品 | 花费 | 用途 |
|---|---|---|
| **随机物品**（Random Item） | 0 TC | 随机购买一件物品 |
| **1 颗原始电信水晶**（1 Raw Telecrystal） | 1 TC | 1 颗裸 TC（可转移） |
| **辛迪加入会套件**（Syndicate Induction Kit） | 10 TC | 叛徒入门套件 |
| **辛迪加法外之徒套件**（Syndicate Outlaw Kit） | 18 TC | 法外之徒套装（武器+工具） |
| **辛迪加战术套件**（Syndi-kit Tactical） | 20 TC | 战术套装（武器+装备） |
| **辛迪加特殊套件**（Syndi-kit Special） | 20 TC | 特殊套装（潜行+工具） |
| **辛迪加盈余箱**（Syndicate Surplus Crate） | 20 TC | 仓库盈余箱（随机物品） |
| **联合盈余箱**（United Surplus Crate） | 20 TC | 大盈余箱（需钥匙） |
| **联合盈余箱钥匙**（United Surplus Crate Key） | 20 TC | 开联合盈余箱的钥匙 |
| **契约套件**（Contract Kit） | 20 TC | 承包商契约启动套件 |
| **赛博植入体捆绑包**（Cybernetic Implants Bundle） | 20 TC | 全套赛博植入体 |
| **医疗捆绑包**（Medical bundle） | 25 TC | 医疗用品套装 |
| **阿尔法喷火兵捆绑包**（Spetsnaz Pyro bundle） | 30 TC | 喷火兵特种套装 |

## 植入体（13）

| 物品 | 花费 | 用途 |
|---|---|---|
| **微型炸弹植入体**（Microbomb Implant） | 2 TC | 体内微型炸弹 |
| **辛迪加内部无线电植入体**（Internal Syndicate Radio Implant） | 4 TC | 内置无线电 |
| **死亡铃声植入体盒**（Box of Deathrattle Implants） | 4 TC | 死亡报警植入体 |
| **自由植入体**（Freedom Implant） | 5 TC | 挣脱手铐 |
| **战术否认植入体**（Tactical Deniability Implant） | 6 TC | 防测谎 |
| **潜行植入体**（Stealth Implant） | 8 TC | 隐身 |
| **储物植入体**（Storage Implant） | 8 TC | 体内储物空间 |
| **复苏植入体**（Reviver Implant） | 8 TC | 濒死自动复苏 |
| **热视觉眼**（Thermal Eyes） | 8 TC | 热成像眼植入 |
| **X 光视觉植入体**（X-ray Vision Implant） | 8 TC | 透视植入体 |
| **CNS 重启植入体**（CNS Rebooter Implant） | 8 TC | 免疫眩晕 |
| **巨型炸弹植入体**（Macrobomb Implant） | 20 TC | 体内巨型炸弹（死后炸） |
| **Uplink 植入体**（Uplink Implant） | ? TC | 体内 Uplink |

## 危险品（10）

| 物品 | 花费 | 用途 |
|---|---|---|
| **投掷武器盒**（Box of Throwing Weapons） | 3 TC | 各种投掷武器 |
| **野猫手雷盒**（Feral Cat Grenade Box） | 5 TC | 投出野猫攻击 |
| **Donksoft 防暴手枪箱**（Donksoft Riot Pistol Case） | 6 TC | 非致命防暴手枪 |
| **能量剑**（Energy Sword） | 6 TC | 经典能量剑，展开伤害高 |
| **动力拳套**（Power Fist） | 6 TC | 液压拳套，击退 |
| **马卡洛夫手枪箱**（Makarov Pistol Case） | 7 TC | 9mm 紧凑手枪 |
| **北极星之拳套**（Gloves of the North Star） | 8 TC | 拳击武术，点穴眩晕 |
| **双刃能量剑**（Double-Bladed Energy Sword） | 13 TC | 双刃能量剑，格挡 75% |
| **辛迪加左轮**（Syndicate Revolver） | 13 TC | 强力左轮手枪 |
| **全息寄生体**（Holoparasites） | 18 TC | 全息共生体（战斗助手） |

## 增援（7）

| 物品 | 花费 | 用途 |
|---|---|---|
| **瞭望情报特工**（Overwatch Intelligence Agent） | 10 TC | AI 特工（支援） |
| **一次性哨戒炮**（Disposable Sentry Gun） | 16 TC | 部署自动哨戒炮 |
| **改装 ED209**（Modified ED209） | 20 TC | 改装执法机器人 |
| **特工增援**（Operative Reinforcements） | ? TC | 呼叫特工增援 |
| **辛迪加突击机器人**（Syndicate Assault Cyborg） | ? TC | 召唤突击机器人 |
| **辛迪加医疗机器人**（Syndicate Medical Cyborg） | ? TC | 召唤医疗机器人 |
| **辛迪加破坏机器人**（Syndicate Saboteur Cyborg） | ? TC | 召唤破坏机器人 |

## 弹药（6）

| 物品 | 花费 | 用途 |
|---|---|---|
| **Donksoft 防暴手枪弹药盒**（Donksoft Riot Pistol Ammunition Case） | 2 TC | Donksoft 防暴手枪备用弹匣 |
| **9mm 弹匣盒**（9mm Magazine Case） | 2 TC | 马卡洛夫手枪 9mm 弹匣×3+散弹 |
| **9mm 穿甲弹匣**（9mm Armour Piercing Magazine） | 2 TC | 9mm 穿甲弹（8 发） |
| **9mm 燃烧弹匣**（9mm Incendiary Magazine） | 2 TC | 9mm 燃烧弹（8 发） |
| **9mm 空尖弹匣**（9mm Hollow Point Magazine） | 3 TC | 9mm 空尖弹（8 发） |
| **.357 快速装填器**（.357 Speed Loader） | 4 TC | .357 马格南快速装填×7 |

## 机甲（5）

| 物品 | 花费 | 用途 |
|---|---|---|
| **暗黑 Gygax 支援行李袋**（Dark Gygax Support Duffel Bag） | 4 TC | Gygax 配件袋 |
| **Mauler 支援行李袋**（Mauler Support Duffel Bag） | 6 TC | Mauler 配件袋 |
| **暗黑 Gygax 机甲**（Dark Gygax Exosuit） | 60 TC | 高速战斗机甲 |
| **暗黑 H.O.N.K. 机甲**（Dark H.O.N.K.） | 80 TC | 小丑战斗机甲 |
| **Mauler 机甲**（Mauler Exosuit） | 100 TC | 重型战斗机甲 |

## 基地钥匙（5）

| 物品 | 花费 | 用途 |
|---|---|---|
| **辛迪加军械实验室门卡**（Syndicate Ordnance Laboratory Access Card） | 30 TC | 进入辛迪加军械实验室（需基地钥匙） |
| **辛迪加生物武器实验室门卡**（Syndicate Bio-Weapon Laboratory Access Card） | ? TC | 进入生物武器实验室 |
| **辛迪加化学厂门卡**（Syndicate Chemical Plant Access Card） | ? TC | 进入化学厂 |
| **洛佩兹的门卡**（Lopez's Access Card） | ? TC | 洛佩兹专属门卡 |
| **辛迪加清洁门卡**（Syndicate Custodial Access Card） | ? TC | 清洁工门卡 |

## 承包商（4）

| 物品 | 花费 | 用途 |
|---|---|---|
| **合同重掷**（Contract Reroll） | 0 TC | 重新随机合同 |
| **承包商定位指示器**（Contractor Pinpointer） | 1 TC | 定位合同目标 |
| **Fulton 回收套件**（Fulton Extraction Kit） | 1 TC | 气球回收物品/尸体 |
| **承包商增援**（Contractor Reinforcement） | 2 TC | 合同增援 |

## 增援2（3）

| 物品 | 花费 | 用途 |
|---|---|---|
| **猿人特工补给**（Simian Agent Supplies） | 4 TC | 猿人特工装备 |
| **猿人特工增援**（Simian Agent Reinforcements） | 7 TC | 召唤猿人特工 |
| **小丑增援**（Clown Reinforcements） | 20 TC | 召唤小丑小队 |

## 特殊（1）

| 物品 | 花费 | 用途 |
|---|---|---|
| **辛迪加自动手术器**（Syndicate Autosurgeon） | 5 TC | 免手术植入器 |

## 种族限定（1）

| 物品 | 花费 | 用途 |
|---|---|---|
| **超亮灯笼**（Extra-Bright Lantern） | 2 TC | 超亮光源 |

---

## 五、对战攻略

### 怎么玩叛徒（推荐流程）

```
① 确认目标（任务+终局目标）+ Uplink 位置（偏好设定）
② 起始 TC 买核心装备（武器/潜行/逃生）
③ 完成任务目标（盗取/破坏/杀人）
④ 用声誉系统解锁高级物品（progression）
⑤ 终局：劫机 / 殉道 / 逃脱
```

### 怎么防叛徒（船员对策）

| 方法 | 说明 |
|---|---|
| **搜 Uplink** | 叛徒的 PDA/笔/收音机可能是 Uplink——检查可疑物品 |
| **盯贵重物** | 叛徒目标常是磁盘/武器库/引擎 |
| **安保巡逻** | 叛徒单人——保持警惕+结伴 |
| **反潜行** | 变色套件/特工 ID 是常见潜行手段——查监控 |

---

*本文档数值全部实测自 `code/modules/antagonists/traitor/` + `code/modules/uplink/` 源码，无推测。*
