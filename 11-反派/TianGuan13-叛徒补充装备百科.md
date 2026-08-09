# TianGuan13 叛徒补充装备百科

> **项目**: TianGuan13 (Nova Sector → /tg/station)
> **代码**: `modular_nova/modules/traitor-uplinks/`（22 文件 1,191 行）+ `modular_nova/modules/moretraitoritems/`（18 文件 884 行）
> **范围**: NOVA 叛徒（Traitor）Uplink 物品扩展全录——本卷为《反派系统百科》Uplink 395 件的**补充卷**，专注 NOVA 自研/重定价物品与更多叛徒道具
> **核心**: NOVA 统一 TC 档位（`standard_cost.dm`）+ `additions/` 新增物品 + `overwrites/` 重定价 + `moretraitoritems/` 更多叛徒道具

---

## 目录

- [第一卷 · NOVA Uplink 扩展（traitor-uplinks）](#第一卷--nova-uplink-扩展traitor-uplinks)
  - [1.1 标准 TC 档位（standard_cost）](#11-标准-tc-档位standard_cost)
  - [1.2 additions 新增物品全录](#12-additions-新增物品全录)
    - [弹药（ammunition）](#弹药ammunition)
    - [狠货（badass）](#狠货badass)
    - [危险品（dangerous）](#危险品dangerous)
    - [装置工具（device_tools）](#装置工具device_tools)
    - [植入体（implant）](#植入体implant)
    - [种族限定（species）](#种族限定species)
    - [潜行工具（stealthy_tools）](#潜行工具stealthy_tools)
    - [护甲与 MOD（suits）](#护甲与-modsuits)
  - [1.3 overwrites 原版物品重定价](#13-overwrites-原版物品重定价)
- [第二卷 · 更多叛徒道具（moretraitoritems）](#第二卷--更多叛徒道具moretraitoritems)
  - [2.1 自动手术刀（autosurgeon）](#21-自动手术刀autosurgeon)
  - [2.2 身体部件手术刀（autosurgeon_bodypart）](#22-身体部件手术刀autosurgeon_bodypart)
  - [2.3 身份卡（cards_id）](#23-身份卡cards_id)
  - [2.4 邪教（cultist）](#24-邪教cultist)
  - [2.5 酒杯（drinkingglass）](#25-酒杯drinkingglass)
  - [2.6 胶水（glue）](#26-胶水glue)
  - [2.7 耳机（headset）](#27-耳机headset)
  - [2.8 机甲（mechs）](#28-机甲mechs)
  - [2.9 药品（medicine）](#29-药品medicine)
  - [2.10 MOD 组装件（modsuits）](#210-mod-组装件modsuits)
  - [2.11 套装（outfits）](#211-套装outfits)
  - [2.12 海盗（pirate）](#212-海盗pirate)
  - [2.13 生成手雷（spawnergrenade）](#213-生成手雷spawnergrenade)
  - [2.14 辛迪加杂项（syndicate）](#214-辛迪加杂项syndicate)
  - [2.15 辛迪加镜子（syndiemirror）](#215-辛迪加镜子syndiemirror)
  - [2.16 假公告装置（traitor_announcer）](#216-假公告装置traitor_announcer)
  - [2.17 Uplink 套装（uplink_kits）](#217-uplink-套装uplink_kits)
  - [2.18 武器（weapons）](#218-武器weapons)
- [附录 · 代码路径索引](#附录--代码路径索引)

---

# 第一卷 · NOVA Uplink 扩展（traitor-uplinks）

**代码**: `modular_nova/modules/traitor-uplinks/`（22 文件 1,191 行）

NOVA 对原版 Uplink 系统的改造分为三层：**标准 TC 档位**（统一所有物品定价基准）、**additions/**（全新物品，直接挂载到原版 Uplink 分类下）、**overwrites/**（对原版物品的重定价与下架）。

## 1.1 标准 TC 档位（standard_cost）

**代码**: `standard_cost.dm`（85 行）

NOVA 统一 TC 定价基准，所有新物品与重定价均引用这些常量：

| 档位类 | 低（low） | 中（medium） | 高（high） |
|---|---|---|---|
| 通用小装置（gadgets） | **3 TC** | **7 TC** | **15 TC** |
| 武器（weaponry，含低中档） | **4 TC**（低中 **6 TC**） | **11 TC** | **18 TC** |
| 弹药（ammunition） | **1 TC** | **2 TC** | **3 TC** |
| 爆炸物（explosive） | **5 TC** | **15 TC** | **25 TC** |
| MOD 服（modsuit） | **7 TC** | **12 TC** | **18 TC** |
| 武术（martial_arts） | **17 TC** | **27 TC** | **35 TC** |
| 捆绑包（bundle） | **10 TC** | **20 TC** | **30 TC** |

> 档位子类型：`low_cost`、`low_medium_cost`、`medium_cost`、`high_cost`，武器类额外带 `/weaponry` 后缀。

## 1.2 additions 新增物品全录

### 弹药（ammunition）

**代码**: `additions/ammunition.dm`（165 行）—— 21 件

| # | 物品（EN） | 中文名 | TC | 效果 |
|---|---|---|---|---|
| 1 | Enforcer 10mm Magazine Case | Enforcer 10mm 弹匣盒 | 1 | 3 个 12 发 10mm 弹匣 + 大盒散装 10mm 弹药（Enforcer-TEN 手枪） |
| 2 | Enforcer 10mm Armour Piercing Magazine | Enforcer 穿甲弹匣 | 1 | 12 发 10mm 穿甲弹匣，穿透护甲但伤害较低 |
| 3 | Enforcer 10mm Hollow Point Magazine | Enforcer 空尖弹匣 | 1 | 12 发 10mm 空尖弹匣，伤害更高但对护甲无效 |
| 4 | Enforcer 10mm Incendiary Magazine | Enforcer 燃烧弹匣 | 1 | 12 发 10mm 燃烧弹匣，伤害低但点燃目标 |
| 5 | Slug Twelve-Gauge Box | 12 号口径独头弹盒 | 1 | 标准独头霰弹盒 |
| 6 | Buckshot Twelve-Gauge Box | 12 号口径鹿弹盒 | 1 | 标准鹿弹霰弹盒 |
| 7 | Flechette Twelve-Gauge Box | 12 号口径箭形弹盒 | 1 | 标准箭形霰弹盒 |
| 8 | Incendiary Twelve-Gauge Box | 12 号口径燃烧弹盒 | 1 | 标准燃烧霰弹盒 |
| 9 | 10mm Handgun Magazine (Ansem) | Ansem 10mm 手枪弹匣 | 1 | 8 发 10mm 弹匣（Ansem 手枪） |
| 10 | 10mm Armour Piercing Magazine (Ansem) | Ansem 穿甲弹匣 | 1 | 8 发穿甲弹匣 |
| 11 | 10mm Hollow Point Magazine (Ansem) | Ansem 空尖弹匣 | 1 | 8 发空尖弹匣 |
| 12 | 10mm Incendiary Magazine (Ansem) | Ansem 燃烧弹匣 | 1 | 8 发燃烧弹匣 |
| 13 | Ammo Pouch | 弹药袋 | 1 | 口袋大小，可容纳三个弹匣 |
| 14 | .45 SMG Magazine (C-20r) | C-20r .45 SMG 弹匣 | 1 | 24 发 .45 弹匣（C-20r 冲锋枪） |
| 15 | .45 Armor Piercing SMG Magazine (C-20r) | C-20r 穿甲弹匣 | 1 | 24 发穿甲弹匣 |
| 16 | .45 Hollow Point SMG Magazine (C-20r) | C-20r 空尖弹匣 | 1 | 24 发空尖弹匣 |
| 17 | .45 Incendiary SMG Magazine (C-20r) | C-20r 燃烧弹匣 | 1 | 24 发燃烧弹匣 |
| 18 | Milspec Buckshot Box | 军规鹿弹盒 | 2 | 15 发 Scarborough 热装鹿弹，任何霰弹枪可用 |
| 19 | Milspec Slugs Box | 军规独头弹盒 | 2 | 15 发 Scarborough 热装独头弹，任何霰弹枪可用 |
| 20 | Breacher Twelve-Gauge Box | 破门霰弹盒 | 1 | 12 发破门弹，摧毁建筑结构极佳 |
| 21 | .357 Haywire+ (EMP) speedloader | .357 Haywire+ EMP 快速装弹器 | 3 | 7 发 .357 电磁脉冲弹（辛迪加左轮用），杀人+耗尽电池 |

> 出售对象：全部 `UPLINK_TRAITORS | UPLINK_SERIOUS_OPS`（SMG 系列仅叛徒）。

### 狠货（badass）

**代码**: `additions/badass.dm`（98 行）—— 6 件

| # | 物品（EN） | 中文名 | TC | 效果 |
|---|---|---|---|---|
| 1 | Guerilla Gloves | 游击手套 | 1 | 战斗抓握手套，近战放倒（takedown）出色，带绝缘内衬 |
| 2 | Combat Jackboots | 战斗军靴 | 1 | 高速低阻战斗靴 |
| 3 | Bulletproof Armor Vest | 防弹背心 | 3 | Type III 重型防弹背心，防传统弹道武器，轻微防爆 |
| 4 | Syndicate Helmet | 辛迪加头盔 | 3 | 红黑条纹 SWAT 头盔，坚固且可太空使用 |
| 5 | Henchmen Bundle | 打手套装（5 套） | 4 | 5 套装甲打手装：帽/外套/制服/手套/皮鞋/弹簧刀 |
| 6 | Syndicate Bunny Kit | 辛迪加兔女郎暗杀套装 | 8 | Carota 兔窝特供战术兔装：兔耳/兔装/燕尾服/领结/高跟鞋 + 1 根胡萝卜匕首 |

### 危险品（dangerous）

**代码**: `additions/dangerous.dm`（150 行）—— 19 件

| # | 物品（EN） | 中文名 | TC | 效果 |
|---|---|---|---|---|
| 1 | Donksoft Riot SMG Case | Donksoft 防暴冲锋枪箱 | 1 | 玩具 SMG，高速发射防暴泡沫镖，可击倒目标；附 2 弹匣，可装消音器 |
| 2 | Enforcer-TEN Handgun Case | Enforcer-TEN 手枪箱 | 4 | 10mm 战斗手枪 + 2 弹匣 + 散装弹药；不可装消音器，声音大 |
| 3 | Ansem Pistol Case | Ansem 手枪箱 | 4 | 小巧易隐藏的 10mm 手枪，8 发弹匣，可装消音器，附 2 弹匣 |
| 4 | Modified Revolver Case | 改装左轮箱 | 4 | .357 马格南左轮，发射跳弹子弹（Ocelot 式，重口味风格） |
| 5 | Syndie Fitness Nunchuks | 辛迪加健身双节棍 | 4 | 钛合金重型双节棍，快速击晕/伤害对手；近战可格挡所有近战与投掷 |
| 6 | Carwo-Cawil M64 Shotgun Case | Carwo-Cawil M64 霰弹枪箱 | 4 | 12 号口径霰弹枪，弹仓 8 发 |
| 7 | "Covenant" elite energy sword | 「圣约」精英能量剑 | 4 | 双刃纯能量剑，收刀时可入口袋；激活声大，不适合潜行 |
| 8 | Allstar Laser-Carbine Case | Allstar 激光卡宾枪箱 | 2 | 改装激光枪：射速更快但单发伤害低，附充电套件 |
| 9 | Cybersun S-120 Case | Cybersun S-120 激光枪箱 | 6 | 辛迪加保安用激光枪，连发低功率等离子束，附充电套件 |
| 10 | Carwo-Cawil Sindano Case | Carwo-Cawil Sindano 冲锋枪箱 | 6 | Sindano SMG，附致命/非致命弹药与三个弹匣 |
| 11 | Alacrán PDW Case | Alacrán 个人防卫武器箱 | 6 | 紧凑无托 PDW，.27-54 Cesarzowa 口径，3 个满弹匣持续火力 |
| 12 | Scarborough C-20r Case | Scarborough C-20r 冲锋枪箱 | 11 | 无托冲锋枪，.45 口径 24 发弹匣，可装消音器，附 2 弹匣 |
| 13 | Energy Shield | 能量盾 | 11 | 高偏转能量盾，与能量剑绝配 |
| 14 | Katana | 武士刀 | 11 | 折叠九千次锻造的极锋利坚固之刃，高致命且非法 |
| 15 | Boarder-980 Grenade Launcher Case | Boarder-980 榴弹发射器箱 | 11 | .980 Tydhouer 榴弹发射器，即时空爆配置，5+1 容量；预装破片榴弹 + 2 盒破片 + 1 盒磷弹 |
| 16 | Szot Dynamica 'Wyłom' AMR Case | Szot Dynamica「Wyłom」反器材步枪箱 | 11 | CIN 军退役巨型反器材步枪，发射毁灭性 .60 Strela 无壳弹（穿透过强而停产） |
| 17 | Carwo-Cawil MMR-2543E Assault Rifle | Carwo-Cawil MMR-2543E 突击步枪 | 11 | 重型战斗步枪，战术黑涂装，兼容标准 SolFed 步枪弹匣 |
| 18 | Shitzu Magfed Shotgun Case | Shitzu 弹匣供弹霰弹枪箱 | 18 | 12 号口径弹匣供弹霰弹枪（Gorlex Marauders 贡献），狗是人类最好的朋友→12 号口径才是 |
| 19 | Modified Sawn-off Shotgun Case | 改装锯短霰弹枪箱 | 18 | 射程不是问题——你可以把受害者拉到你面前（带钩） |

### 装置工具（device_tools）

**代码**: `additions/device_tools.dm`（45 行）—— 5 件

| # | 物品（EN） | 中文名 | TC | 效果 |
|---|---|---|---|---|
| 1 | Syndicate Jaws of Life | 辛迪加液压破拆器 | 3 | 撬棍+剪线钳二合一，撬棍模式可强行撬开气闸 |
| 2 | Syndicate Cyborg Upgrade | 辛迪加机器人劫持升级 | 3 | 劫持机器人：强大修复纳米机器人/建造设备/破坏者模块 |
| 3 | Syndikush Green Crack cart | Syndikush 绿色大麻烟弹 | 3 | 廉价中国产电子烟弹，THC+兴奋剂混合（并非真 crack） |
| 4 | Glue | 辛迪加超级胶水 | 7 | 一次性胶水，对任意物品使用使其**无法脱手**（NODROP） |
| 5 | Ammo Fabricator Advanced Lethal Authentication Module | 弹药制造机高级致命认证模块 | 7 | Gorlex Marauders 改装弹药制造模块，含大量麻烦的认证密钥（走私品） |

### 植入体（implant）

**代码**: `additions/implant.dm`（37 行）—— 4 件（NIFSoft 神经软件系列）

| # | 物品（EN） | 中文名 | TC | 效果 |
|---|---|---|---|---|
| 1 | Grimoire Opera NIFSoft | Grimoire Opera 工具 NIFSoft | 3 | 技师专用，按需生成纳米工具：万能钻/焊枪/万能工具/钢丝刷/封门胶 |
| 2 | Grimoire Asclepius NIFSoft | Grimoire Asclepius 手术 NIFSoft | 3 | 应急手术套件：手术刀/牵开器/骨锯/分析仪；为火星 EMT 红漠环境研发 |
| 3 | Thermal Lens NIFSoft | 热成像镜片 NIFSoft | 7 | 军规视觉增强，将红外辐射转为可见光，可隔墙看清热源；需兼容眼镜激活 |
| 4 | Blood Steal NIFSoft | 夺血 NIFSoft | 15 | 战斗向纳米包：双手化为致命武器，动能吸血 + 冲击波击退；合成体尤佳，有机体有组织坏死与精神错乱风险 |

### 种族限定（species）

**代码**: `additions/species.dm`（13 行）—— 1 件

| # | 物品（EN） | 中文名 | TC | 效果 |
|---|---|---|---|---|
| 1 | Xeno-organ Implant Kit | 异形器官植入套件 | 7 | 非法获得的异形器官盒；**仅限 XENO 种族**购买，盈余 5 |

### 潜行工具（stealthy_tools）

**代码**: `additions/stealthy_tools.dm`（46 行）—— 5 件

| # | 物品（EN） | 中文名 | TC | 效果 |
|---|---|---|---|---|
| 1 | Thieves Gloves | 窃贼手套 | 3 | 增强从他人身上悄悄卸下小物品的能力，静默扒窃 |
| 2 | Reinforced Knuckleduster | 强化指虎 | 3 | 紧凑隐蔽的强化指虎，安静快速，威力超出外表（非法科技+走私品） |
| 3 | Ransomware Neuroware Chip | 勒索软件神经芯片 | 3 | CrypSys 勒索病毒包，针对合成人形：暂时致哑/定身/昏迷（昏迷前仍可行动） |
| 4 | Fake Announcement | 假公告装置 | 3 | 向空间站伪造任意公告（见 2.16），无盈余 |
| 5 | Extra Large Syndicate Shotglasses | 超大辛迪加子弹杯 | 1 | 伪装成 15 单位的 50 单位子弹杯（7 只），一杯 Bacchus 祝福灌倒对手；**仅限酒保/桥面助理** |

### 护甲与 MOD（suits）

**代码**: `additions/suits.dm`（59 行）—— 8 件

| # | 物品（EN） | 中文名 | TC | 效果 |
|---|---|---|---|---|
| 1 | Nakamura Standard MODsuit | 中村标准民用 MOD 服 | 7 | 中村工程三代模块化民用服，银河系民用标配 |
| 2 | Contractor MODsuit | 承包商 MOD 服 | 7 | 罕见的非辛迪加配色，为私人佣兵制造 |
| 3 | Elite MODsuit | 精英 MOD 服 | 18 | 辛迪加服进化版：更厚重+哑光黑，仅限高级军官与精英突击队 |
| 4 | MODsuit Donksoft Recycler | MOD 服 Donksoft 回收器 | 3 | 回收发射过的泡沫镖（与垃圾），重新打包为半盒防暴镖 |
| 5 | MODsuit Flamethrower | MOD 服火焰喷射器 | 3 | 定做火焰喷射器，烧穿你的路。烧得好。 |
| 6 | MODsuit Energy-Shield | MOD 服能量护盾 | 3 | 个人防护力场（星舰护盾缩小版），几乎可挡任何攻击但每数秒仅一次 |
| 7 | MODsuit Ionic Jump Jet | MOD 服离子跳跃喷射器 | 7 | 离子推进器，短促强力冲刺可对抗重力，之后需充能 |
| 8 | MODsuit Bulwark | MOD 服壁垒 | 15 | 层层减震板，防止被愤怒人群推进墙里 |

## 1.3 overwrites 原版物品重定价

**代码**: `overwrites/`（13 文件）—— 对原版 Uplink 物品的价格调整与下架（注释 `//` 后为原版价）

### 弹药 `overwrites/ammunition.dm`

| 物品 | NOVA 价 | 原价 | 备注 |
|---|---|---|---|
| Toy Darts（玩具镖） | — | — | `purchasable_from = NONE` 下架（没必要） |
| 9mm 手枪弹匣（pistol/ap/hp/fire） | 1 TC | 2/2/3/2 | 统一低档弹药 |
| .38 左轮弹匣（revolver） | 1 TC | 4 | 统一低档弹药 |

### 狠货 `overwrites/badass.dm`

| 物品 | NOVA 价 | 原价 |
|---|---|---|
| 服装包（costumes） | 3 TC | 4 |
| 辛迪加现金（syndiecash） | 7 TC | 3 |
| 危险马（dangerous_horse） | 15 TC | 10 |

### 捆绑包 `overwrites/bundle.dm`

| 物品 | NOVA 价 | 原价 | 备注 |
|---|---|---|---|
| Bundle A / B（随机套装 A/B） | 20 TC | 20 | 维持中档捆绑 |
| 盈余箱（surplus） | 20 TC | 20 | **内容保证值 50 TC**，每人限 1 箱 |
| 联合盈余箱（surplus/united） | 20 TC | — | **内容保证值 125 TC**，高级防篡改锁，需他人购买盈余钥匙开箱 |
| 盈余钥匙（surplus_key） | 20 TC | 20 | 开联合盈余箱 |

### 承包商 `overwrites/contractor.dm`

| 物品 | NOVA 价 | 原价 | 备注 |
|---|---|---|---|
| 承包商套件（contract_kit） | **动态**：`TELECRYSTALS_DEFAULT(35) − (MOD低档7 + 低档3) = 25 TC` | 20 | 购后获得合同 Uplink 平板 + 变色龙制服/面具 + 特工卡 + 承包商警棍 |

### 危险品 `overwrites/dangerous.dm`

| 物品 | NOVA 价 | 原价 | 备注 |
|---|---|---|---|
| Donksoft 防暴手枪（foampistol） | 1 TC | 6 | 大降价 |
| 手枪（pistol） | 4 TC | 7 | |
| 左轮（revolver） | 4 TC | 13 | |
| 能量剑（sword） | 4 TC | 6 | |
| 动力拳套（powerfist） | 4 TC | 6 | |
| 猫（cat） | — | — | 下架（低角色扮演） |
| 速射激光（rapid） | 11 TC | 8 | |
| 守护者（guardian） | 11 TC | 18 | |
| 双头能量剑（doublesword） | 18 TC | 13 | |

### 装置工具 `overwrites/device_tools.dm`

| 物品 | NOVA 价 | 原价 | 备注 |
|---|---|---|---|
| 战术急救包（tactical_medkit） | 3 TC | 3 | |
| 辛迪加手术包（surgery_syndie） | 3 TC | 3 | |
| 加密密钥（encryptionkey） | 3 TC | 2 | |
| 辛迪加法典（syndietome） | 3 TC | 5 | |
| 二进制翻译器（binary） | 3 TC | 5 | |
| 电磁卡（emag） | 3 TC | 4 | |
| 门锁破解器（doorjack） | 3 TC | 3 | |
| 合成框架（frame） | 3 TC | 4 | |
| 电车遥控（tram_remote） | — | — | 下架 |
| 热成像护目镜（thermal） | 7 TC | 4 | |
| 兴奋剂（stimpack） | 7 TC | 5 | |
| 公文包发射台（briefcase_launchpad） | — | — | 下架 |
| 可疑电话（suspiciousphone） | — | — | 下架 |
| 实验传送器（syndicate_teleporter） | 15 TC | 8 | |
| 催眠手雷（hypnotic_grenade） | 15 TC | 12 | |
| 能源吸收器（powersink） | 15 TC | 11 | |

### 爆炸物 `overwrites/explosive.dm`

| 物品 | NOVA 价 | 原价 | 备注 |
|---|---|---|---|
| 肥皂集束弹（soap_clusterbang） | — | — | 下架（低 RP 且烦人） |
| C4 袋（c4bag） | 5 TC | 5 | 低档爆炸物 |
| EMP 手雷（emp） | 5 TC | 2 | 大涨价——本服 EMP 威力强得多 |
| 炸弹拆解器（detomatix） | — | — | 下架 |
| 辛迪加炸弹（syndicate_bomb） | 25 TC | 11 | 高档爆炸物 |
| 辛迪加 EMP 炸弹（bomb/emp） | 25 TC | 22 | 高档爆炸物 |

### 植入体 `overwrites/implant.dm`

| 物品 | NOVA 价 | 原价 | 备注 |
|---|---|---|---|
| 辛迪加无线电植入体（radio） | 3 TC | 4 | |
| 自由植入体（freedom） | 7 TC | 5 | 上调防滥用（对安保心理冲击大） |
| 潜行植入体（stealthimplant） | 15 TC | 8 | |
| 储物植入体（storage） | 15 TC | 8 | |

### 角色限定 `overwrites/job.dm`

| 物品 | NOVA 价 | 原价 | 备注 |
|---|---|---|---|
| 小丑别针（clownpin） | 1 TC | 4 | |
| 爆炸热土豆（explosive_hot_potato） | — | — | 下架（与服务器氛围不符） |
| 小丑超级别针（clownsuperpin） | 3 TC | 7 | |
| 反向左轮（reverse_revolver） | 3 TC | 14 | |
| 气压模块（pressure_mod） | 3 TC | 5 | 矿工/货运技师限定 |
| 邮件伪造套件（mail_counterfeit_kit） | — | — | 下架 |
| 大猩猩方块（gorillacube） | 3 TC | 6 | 遗传学家/科学家/科研主管限定 |
| 弹簧锁模块（springlock_module） | — | — | 科学系限定（未改价） |
| 隐藏武器舱（concealed_weapon_bay） | — | — | 科学系限定（未改价） |
| 官僚错误（bureaucratic_error） | 7 TC | 2 | 大涨价（本服 QM/HoP 不能当叛徒） |
| 轻松清洁包（ez_clean_bundle） | 7 TC | 6 | 清洁工/桥面助理限定 |
| 洗脑盘（brainwash_disk） | 7 TC | 5 | |
| 改装注射枪（modified_syringe_gun） | 7 TC | 14 | 科学系限定 |
| 炮塔盒（turretbox） | 8 TC | 11 | 工程师/总工/大气技师限定（祖父价） |
| 钢筋弩（rebarxbowsyndie） | — | — | 工程师系限定 |
| 震荡盘（concussivedisk） | — | — | 工程师系限定 |
| 肉钩（meathook） | 7 TC | 11 | |
| 化学枪（chemical_gun） | 7 TC | 12 | |
| 激光臂（laser_arm） | 5 TC | 10 | 科学系限定（祖父价） |
| 派炮（pie_cannon） | 7 TC | 10 | |
| 蜘蛛注射器（spider_injector） | — | — | 下架 |
| 巨猩血清（magillitis_serum） | — | — | 科学系限定（未改价） |
| 沉默者（reticence） | 15 TC | 20 | 哑剧演员/机器人技师限定 |
| 小丑炸弹（clown_bomb） | 15 TC | 15 | |
| 圣恩（his_grace） | — | — | 下架（低角色扮演） |
| 爆破加农炮（blastcannon） | — | — | 下架 |

### 特殊 `overwrites/special.dm`

| 物品 | NOVA 价 | 原价 |
|---|---|---|
| 辛迪加自动手术刀（autosurgeon） | 3 TC | 5 |

### 潜行武器 `overwrites/stealthy.dm`

| 物品 | NOVA 价 | 原价 | 备注 |
|---|---|---|---|
| 滑溜棒（slipstick） | 3 TC | 6 | |
| 辛迪加弓弩（crossbow） | 4 TC | 10 | |
| 武术（martialarts） | 27 TC | 17 | 中档武术 |
| 违禁警棍（contrabaton） | 11 TC | 7 | |
| Romerol 套件（romerol_kit） | — | — | 下架 |

### 潜行工具 `overwrites/stealthy_tools.dm`

| 物品 | NOVA 价 | 原价 | 备注 |
|---|---|---|---|
| 重摇身份（mulligan） | — | — | 下架（损坏待重做） |
| 邮件伪造（mail_counterfeit） | — | — | 下架 |
| 法医伪造器（forensics_spofer） | 3 TC | 5 | |
| 电信黑屏（telecomm_blackout） | 7 TC | 4 | |
| 全站断电（blackout） | 7 TC | 6 | |

### 护甲 `overwrites/suits.dm`

| 物品 | NOVA 价 | 原价 | 备注 |
|---|---|---|---|
| 渗透者套装（infiltrator_bundle） | 12 TC | 6 | 中档 MOD 服 |
| 辛迪加 MOD 服（modsuit） | 7 TC | 8 | 低档 MOD 服 |
| 热成像眼镜（thermal） | 6 TC | 2 | 祖父价 |

---

# 第二卷 · 更多叛徒道具（moretraitoritems）

**代码**: `modular_nova/modules/moretraitoritems/`（18 文件 884 行，原 Skyrat PR「moretraitoritems」——新增叛徒小道具分类；作者 Funce/Tyger/Dawson/ErdinyoBarboza）

## 2.1 自动手术刀（autosurgeon）

**代码**: `code/autosurgeon.dm`（73 行）

自动手术刀：对自身一键植入义体的便携工具。NOVA 扩展以下预制型号：

| # | 型号（EN） | 中文名 | 植入物 | 效果 |
|---|---|---|---|---|
| 1 | Autosurgeon /toolset | 工具组手术刀 | 手臂工具套件义体 | 植入标准工具手臂（扳手/螺丝刀/焊枪等） |
| 2 | Autosurgeon /surgery | 手术手术刀 | 手臂手术套件义体 | 植入手术工具手臂 |
| 3 | Autosurgeon /botany | 植物学手术刀 | 手臂植物套件义体 | 植入植物学工具手臂 |
| 4 | Autosurgeon /janitor | 清洁工手术刀 | 手臂清洁套件义体 | 植入清洁工具手臂 |
| 5 | Autosurgeon /armblade | 臂刃手术刀 | 手臂臂刃义体 | 植入刀刃手臂 |
| 6 | Autosurgeon /muscle | 肌肉手术刀 | 强健手臂义体 | 植入强化肌肉手臂 |
| 7 | **Syndie** Autosurgeon /hackerman | 黑客手臂手术刀 | 黑客工具手臂 | 植入黑客工具手臂（破解时显示所有线路功能） |
| 8 | **Syndie** Autosurgeon /esword_arm | 能量剑手臂手术刀 | 能量剑手臂 | 植入能量剑手臂（emag 后力量 30） |
| 9 | **Syndie** Autosurgeon /nodrop | 防脱手手术刀 | 脑部防脱手植入体 | 植入防脱手芯片（手持物不可掉落，EMP 长硬直） |
| 10 | **Syndie** Autosurgeon /baton | 警棍手臂手术刀 | 手臂警棍义体 | 植入警棍手臂 |
| 11 | **Syndie** Autosurgeon /flash | 闪光手臂手术刀 | 手臂闪光义体 | 植入闪光手臂 |

**异形器官扩展**（配合第一卷 Xeno-organ Implant Kit）：

| # | 物品（EN） | 中文名 | 效果 |
|---|---|---|---|
| 12 | Strange Autosurgeon | 奇异自动手术刀 | 手术速度 ×2，仅接受异形器官（`organ_whitelist = /obj/item/organ/alien`） |
| 13 | Alien Plasmavessel /opfor | 异形血浆囊（OPFOR） | 500 血浆容量，血浆率 10 |
| 14 | Alien Resinspinner | 异形树脂喷射器 | 喷树脂器官 |
| 15 | Alien Acid | 异形酸腺 | 喷酸器官 |
| 16 | Alien Neurotoxin | 异形神经毒素 | 神经毒器官 |
| 17 | Alien Hivenode | 异形蜂巢节点 | 蜂巢意识节点 |
| 18 | Strange Organ Transport Box | 奇异器官运输盒 | 含 60 单位低温液（cryostylane）+ 上述全套：奇异手术刀/血浆囊/树脂/酸/神经毒/蜂巢节点 |
| 19 | Strange Organ Box /eggsac | 奇异器官盒（卵囊版） | 额外含异形卵囊器官 |

## 2.2 身体部件手术刀（autosurgeon_bodypart）

**代码**: `code/autosurgeon_bodypart.dm`（87 行）

| # | 物品（EN） | 中文名 | 效果 |
|---|---|---|---|
| 1 | Bodypart Upgrade Autosurgeon | 身体部件升级手术刀 | 自动替换目标身体部位：使用即拆卸旧肢体并装上存储的部件；可用螺丝刀卸出/装入新部件；默认随附 **右机械臂（r_arm_robotic）**（hi2ipc 图标）；次数用尽后失效 |

## 2.3 身份卡（cards_id）

**代码**: `code/cards_id.dm`（2 行）

| # | 物品（EN） | 中文名 | 效果 |
|---|---|---|---|
| 1 | Elite Chameleon ID /impostorsr | 精英变色龙 ID（冒名顶替者版） | 高级变色龙卡；权限 = **维护通道 + 辛迪加 + 指挥（桥接）**——别因为上级没桥接权限就开枪打他 |

## 2.4 邪教（cultist）

**代码**: `code/cultist.dm`（9 行）

| # | 物品（EN） | 中文名 | 效果 |
|---|---|---|---|
| 1 | Zealot's Blindfold | 狂热者眼罩 | 无限制版邪教眼罩：健康 HUD + 夜视，焊接级防闪光（"我们将要去的地方不需要眼睛"） |
| 2 | Zealot's Blindfold /narsie | 狂热者眼罩（Nar'Sie 版） | 同上，Nar'Sie 祝福版（"愿 Nar'Sie 引你穿越黑暗，护你免于光芒"） |

## 2.5 酒杯（drinkingglass）

**代码**: `code/drinkingglass.dm`（15 行）

| # | 物品（EN） | 中文名 | 效果 |
|---|---|---|---|
| 1 | Syndicate Shotglass | 辛迪加子弹杯 | **一口 50 单位**（正常 15），一次灌倒；可注/可倒/可蘸，但**不透明**（别想看清杯里是啥） |
| 2 | Box of Shotglasses | 子弹杯盒（7 只） | 装 7 只辛迪加子弹杯 |

## 2.6 胶水（glue）

**代码**: `code/glue.dm`（27 行）

| # | 物品（EN） | 中文名 | 效果 |
|---|---|---|---|
| 1 | Bottle of Super Glue | 超级胶水瓶 | 黑市高强粘合剂（勿食），**1 次**使用：对任意物品使用使其获得 NODROP（不可脱手），物品描述追加"看起来黏糊糊的"；用尽后变空瓶 |

## 2.7 耳机（headset）

**代码**: `code/headset.dm`（12 行）

| # | 物品（EN） | 中文名 | 效果 |
|---|---|---|---|
| 1 | CentCom Headset /impostorsr | 中央指挥耳机（冒名顶替者版） | 无第二密钥槽（keyslot2 = null） |
| 2 | Chameleon Headset /advanced | 高级变色龙耳机 | 渗透用变色龙耳机：**防闪光弹 + 音量放大**；指挥频道 + 全频段自由接收 |

## 2.8 机甲（mechs）

**代码**: `code/mechs.dm`（75 行）—— 「掠夺者」（Marauder）系列机甲蓝图（供掠夺者代金券系统）

| # | 机甲（EN） | 中文名 | 武器/配置 | 部件 |
|---|---|---|---|---|
| 1 | Dark Gygax /marauder | 黑暗 Gygax 掠夺者 | 左臂 LMG 轻机枪 + 右臂散弹炮；工具：电台/维修机器人/离子推进；装甲：反 EMP 隐匿增强 | 蓝空间电池 + 三频扫描 + 二次电容 + 飞秒伺服 |
| 2 | Mauler /marauder | Mauler 掠夺者 | 左臂 LMG + 右臂闪光弹发射器；工具：电台/维修机器人/离子推进；装甲：反 EMP 隐匿增强 | 同上 |
| 3 | Death Ripley /marauder | 死亡 Ripley 掠夺者 | 左臂液压钳 + 右臂无；工具：电台/钻石钻/弹出器 | 蓝空间电池 + 三频扫描 + 二次电容 + 飞秒伺服 |
| 4 | Dark Honker /marauder | 黑暗 Honker 掠夺者 | 左臂拳击手套发射器 + 右臂香蕉迫击炮；工具：电台/维修机器人/离子推进 | 同上 |

## 2.9 药品（medicine）

**代码**: `code/medicine.dm`（13 行）

| # | 物品（EN） | 中文名 | 效果 |
|---|---|---|---|
| 1 | Cordiolis Hepatico Pill | 强心护肝药丸（5 粒） | 高级保密化学剂，**暂时消除对心脏/肝脏的需求**（自手术神药）；每粒含 cordiolis_hepatico 10u + 糖 5u |

## 2.10 MOD 组装件（modsuits）

**代码**: `code/modsuits.dm`（11 行）

| # | 物品（EN） | 中文名 | 效果 |
|---|---|---|---|
| 1 | MOD Plating /syndicate | 辛迪加 MOD 底板 | 组装主题：辛迪加 MOD 服 |
| 2 | MOD Plating /syndicate_elite | 辛迪加精英 MOD 底板 | 组装主题：精英 MOD 服 |
| 3 | MOD Plating /syndicate_infiltrator | 辛迪加渗透者 MOD 底板 | 组装主题：渗透者 MOD 服 |

## 2.11 套装（outfits）

**代码**: `code/outfits.dm`（38 行）

| # | 套装（EN） | 中文名 | 内容 |
|---|---|---|---|
| 1 | Syndicate Operative - Stealth Kit | 辛迪加特工·潜行套装 | 辛迪加制服/变色龙耳机（辛迪加加密+频道）/夜视+医疗 HUD/辛迪加面具/变色龙核弹 MOD 服/应急氧气罐/军用腰带 + 背包：喷气背包、手枪、生存战斗刀；随机代号名，加入辛迪加阵营 |

## 2.12 海盗（pirate）

**代码**: `code/pirate.dm`（38 行）—— Heister（劫匪）套装

| # | 物品（EN） | 中文名 | 效果 |
|---|---|---|---|
| 1 | Armored Suit Jacket /heister | 装甲西装外套 | 纳米自修复凯夫拉内衬；**2 层护盾充能**（PAYDAY 式，受击消耗，8 秒后恢复）；装甲：近战 35/子弹 30/激光 30/能量 40/炸弹 25/火 50/酸 50/伤口 10 |
| 2 | Nitrile Heister Gloves | 劫匪丁腈手套 | 比乳胶更厚的无菌手套，**隐藏指纹**；电导系数 0 |

## 2.13 生成手雷（spawnergrenade）

**代码**: `code/spawnergrenade.dm`（4 行）

| # | 物品（EN） | 中文名 | 效果 |
|---|---|---|---|
| 1 | Nuclear Delivery Grenade | 核弹投送手雷 | 爆炸生成 **2 名辛迪加太空步枪兵**（trooper/ranged/space） |

## 2.14 辛迪加杂项（syndicate）

**代码**: `code/syndicate.dm`（238 行）

| # | 物品（EN） | 中文名 | TC/获取 | 效果 |
|---|---|---|---|---|
| 1 | Old Radio | 老式收音机 | OPFOR 装备（无 TC） | 老式辛迪加 Uplink：无密码、空 TC 账户（OPFOR「旧辛迪加 Uplink」） |
| 2 | Elite MODsuit /unrestricted | 精英 MOD 服（无限制版） | — | 移除精英 MOD 服的权限要求（任意人可穿） |
| 3 | Boxed Recharger Kit | 武器充电器套件盒 | — | 组装武器充电器：充电器电路板 + 二次电容 + 5 铁 + 5 电缆 + 核弹螺丝刀 + 扳手 |
| 4 | Boxed Space Suit and Helmet | 太空服头盔盒 | — | 随机配色辛迪加太空服+头盔（红/绿/深绿/蓝/橙/黑） |
| 5 | Armoured Noir Jacket /spy | 装甲黑色侦探外套 | 间谍系 | 使用 Heister 装甲数据（近战 35/子弹 30/激光 30/能量 40…） |
| 6 | Armoured French Beret | 装甲法式贝雷帽 | 间谍系 | 安保化妆护甲 |
| 7 | Armoured Black Suit (+skirt) | 装甲黑西装（+裙版） | 间谍系 | 辛迪加制服级护甲 |
| 8 | Dark Leather Holster | 深色皮枪套 | 间谍系 | 辛迪加皮枪套 |
| 9 | Gunman Clothing Bundle | 枪手服装包 | — | 枪手三件套：皮革长风衣（近战 45/子弹 40/激光 40/能量 50/炸弹 25/火 50/酸 50/伤口 10）+ 「狠人裤」（全 20 护甲）+ 「狠人墨镜」（子弹 60 防弹墨镜，防闪光） |
| 10 | Guardian Creator /traitor/opfor | 守护者创造器（叛徒 OPFOR 版） | — | 召唤守护者，**允许变形怪（changeling）守护者** |
| 11 | Codeword Manual | 暗号手册 | — | 1 次使用：让使用者（或被打的人）学会辛迪加暗号（蓝/红关键词听力），用尽变随机书 |
| 12 | Viral Injector | 病毒注射器 | — | **反派授予器**：注入后成为变形怪（changeling） |
| 13 | Strange Book | 奇异之书 | — | **反派授予器**：翻开后成为异端（heretic） |
| 14 | Brass Contraption | 黄铜装置 | — | **反派授予器**：成为钟表邪教徒（clock cultist，solo），并附赠钟表石板 |

## 2.15 辛迪加镜子（syndiemirror）

**代码**: `code/syndiemirror.dm`（67 行）

| # | 物品（EN） | 中文名 | 效果 |
|---|---|---|---|
| 1 | Handheld Mirror /syndie | 手持镜 | 内置理发纳米机器人：对准头部使用，**即时更换发型/胡型**（1 秒），可对他人使用；辛迪加专属描述 |
| 2 | （并入）Chameleon Kit 增强 | 变色龙套件增强 | 原版变色龙套件现在额外附带手持镜 + **染发喷雾**（dyespray） |

## 2.16 假公告装置（traitor_announcer）

**代码**: `code/traitor_announcer.dm`（77 行）

| # | 物品（EN） | 中文名 | TC/获取 | 效果 |
|---|---|---|---|---|
| 1 | Odd Device | 奇异装置 | Uplink 3 TC（见 1.2 Fake Announcement） | 伪造**优先公告**：自定义来源地点/音频键/颜色/标题/正文，全站广播；**1 次使用**（管理员版无限次） |

## 2.17 Uplink 套装（uplink_kits）

**代码**: `code/uplink_kits.dm`（16 行）

| # | 物品（EN） | 中文名 | 效果 |
|---|---|---|---|
| 1 | Cult Construct Kit | 邪教构造体套件 | 皮带 ×1（含 6 颗**净化灵魂石**）+ 2 个构造体外壳信标（激活后 Nar'Sie 构造体外壳投送到你位置） |

## 2.18 武器（weapons）

**代码**: `code/weapons.dm`（82 行）

| # | 物品（EN） | 中文名 | 效果 |
|---|---|---|---|
| 1 | Colt Peacemaker Revolver | Colt Peacemaker 左轮 | 改装 .357 左轮：伤害较低但**跳弹极多**（最多 6 次跳弹，跳弹率 200%，自动瞄准 50°/6 格） |
| 2 | Speed Loader (.357 Peacemaker) | .357 Peacemaker 快速装弹器 | 7 发 Peacemaker 弹；**可在自动加工台（autolathe）制造**（破解类目，2 铁） |
| 3 | Odd Bowler | 古怪圆顶礼帽 | 帽内绣红 S 与 X；**投掷力 45、抛速 5、射程 9、穿甲 30、锐利**——一刀两断的飞帽杀 |
| 4 | Curator Katana | 策展人武士刀 | 古代武士刀（与策展人阔剑同属性）：力量 15、格挡 30%、穿甲 5 |

---

## 附录 · 代码路径索引

### traitor-uplinks（22 文件 1,191 行）

| 路径 | 行数 | 内容 |
|---|---|---|
| `standard_cost.dm` | 85 | 统一 TC 档位常量 |
| `additions/ammunition.dm` | 165 | 新增 21 件弹药 |
| `additions/badass.dm` | 98 | 新增 6 件狠货（游击手套/兔女郎套等） |
| `additions/dangerous.dm` | 150 | 新增 19 件危险品（枪械箱/能量剑/双节棍） |
| `additions/device_tools.dm` | 45 | 新增 5 件装置工具（破拆器/胶水/机器人劫持） |
| `additions/implant.dm` | 37 | 新增 4 件 NIFSoft 植入体 |
| `additions/species.dm` | 13 | 新增异形器官套件（XENO 限定） |
| `additions/stealthy_tools.dm` | 46 | 新增 5 件潜行工具 |
| `additions/suits.dm` | 59 | 新增 3 MOD 服 + 5 MOD 模块 |
| `overwrites/ammunition.dm` | 34 | 弹药重定价 |
| `overwrites/badass.dm` | 21 | 狠货重定价 |
| `overwrites/bundle.dm` | 37 | 捆绑包重定价 + 盈余箱改版 |
| `overwrites/contractor.dm` | 25 | 承包商套件动态定价（25 TC） |
| `overwrites/dangerous.dm` | 44 | 危险品重定价 |
| `overwrites/device_tools.dm` | 70 | 装置工具重定价 |
| `overwrites/explosive.dm` | 31 | 爆炸物重定价 |
| `overwrites/implant.dm` | 25 | 植入体重定价 |
| `overwrites/job.dm` | 110 | 角色限定物品重定价/下架 |
| `overwrites/special.dm` | 15 | 自动手术刀重定价（3 TC） |
| `overwrites/stealthy.dm` | 28 | 潜行武器重定价 |
| `overwrites/stealthy_tools.dm` | 28 | 潜行工具重定价 |
| `overwrites/suits.dm` | 25 | 护甲/MOD 重定价 |

### moretraitoritems（18 文件 884 行）

| 路径 | 行数 | 内容 |
|---|---|---|
| `code/autosurgeon.dm` | 73 | 11 种预制自动手术刀 + 异形器官箱 |
| `code/autosurgeon_bodypart.dm` | 87 | 身体部件替换手术刀 |
| `code/cards_id.dm` | 2 | 精英变色龙 ID（冒名顶替者版） |
| `code/cultist.dm` | 9 | 狂热者眼罩 |
| `code/drinkingglass.dm` | 15 | 50u 辛迪加子弹杯 |
| `code/glue.dm` | 27 | 超级胶水（NODROP） |
| `code/headset.dm` | 12 | 高级变色龙耳机/中央耳机 |
| `code/mechs.dm` | 75 | 掠夺者系列 4 机甲 |
| `code/medicine.dm` | 13 | 强心护肝药丸 |
| `code/modsuits.dm` | 11 | 3 种 MOD 底板 |
| `code/outfits.dm` | 38 | 辛迪加潜行套装 |
| `code/pirate.dm` | 38 | Heister 劫匪装甲套装 |
| `code/spawnergrenade.dm` | 4 | 核弹投送手雷 |
| `code/syndicate.dm` | 238 | 老式 Uplink/充电器套件/间谍服/枪手装/暗号手册/反派授予器 |
| `code/syndiemirror.dm` | 67 | 手持理发镜 |
| `code/traitor_announcer.dm` | 77 | 假公告装置 |
| `code/uplink_kits.dm` | 16 | 邪教构造体套件 |
| `code/weapons.dm` | 82 | Peacemaker 左轮/飞帽/策展人武士刀 |
| `readme.md` | 40 | 模块说明（Skyrat PR 存档） |
| `sound/` + `icons/` | — | 音频与图标资源 |

---

> **统计**: 第一卷 traitor-uplinks 新增物品 **69 件**（含 21 弹药/6 狠货/19 危险品/5 装置/4 植入体/1 种族/5 潜行/8 护甲），原版重定价 **90 项**；第二卷 moretraitoritems 道具 **60+ 项**。TC 价格均为 NOVA 标准档位（低 3/中 7/高 15，武器 4/6/11/18，弹药 1/2/3，爆炸物 5/15/25，MOD 7/12/18）。
