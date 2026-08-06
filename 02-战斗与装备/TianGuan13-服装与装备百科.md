# 天关 — 服装与装备百科

> **项目**: TianGuan13 (Nova Sector → /tg/station)
> **代码**: `code/modules/clothing/`（158 文件 26,988 行）+ `code/modules/mod/`（29 文件 13,976 行 MOD 服）
> **范围**: 全身装备（外套/制服/头/脸/鞋/眼/手/脖/腰带）+ 模块服（MODsuits）——按部位 + S-A-B-C 难度分级分类
>
> **关联文档**：武器数值详见《战斗系统百科》；太空服内层与工程联动。

---

## 目录

- [第一卷 · 防具系统机制](#第一卷--防具系统机制)
- [第二卷 · 外套与护甲](#第二卷--外套与护甲)（120+ 种 S-A-B-C 分级）
- [第三卷 · 制服与内层](#第三卷--制服与内层)（100+ 种岗位/反派/狂欢）
- [第四卷 · 头饰与面具](#第四卷--头饰与面具)（49 种头盔 S-A-B-C）
- [第五卷 · 鞋类与眼镜](#第五卷--鞋类与眼镜)（鞋 75+/眼镜 65+）
- [第六卷 · 手套/脖子/腰带](#第六卷--手套脖子腰带)
- [第七卷 · 太空服](#第七卷--太空服)
- [第八卷 · MOD 模块服（Modular Outerwear Device）](#第八卷--mod-模块服modular-outerwear-device)（35 主题/132 模块/5 核心）
- [第九卷
- [第十卷 · NOVA 外观与装甲](#第十卷--nova-外观与装甲2026-08-06-补全) · 变色龙伪装系统](#第九卷--变色龙伪装系统)
- [第二卷B · 外套全量总表（252 种）](#第二卷b--外套全量总表252-种)
- [第三卷B · 制服全量总表（381 种）](#第三卷b--制服全量总表381-种)
- [第四卷B · 头饰全量总表（241 种）](#第四卷b--头饰全量总表241-种)
- [第四卷C · 面具全量总表（86 种）](#第四卷c--面具全量总表86-种)
- [第五卷B · 鞋类全量总表（75 种）](#第五卷b--鞋类全量总表75-种)
- [第五卷C · 眼镜全量总表（71 种）](#第五卷c--眼镜全量总表71-种)
- [第六卷B · 手套全量总表（70 种）](#第六卷b--手套全量总表70-种)
- [第六卷C · 脖子全量总表（37 种）](#第六卷c--脖子全量总表37-种)
- [第七卷B · 太空服全量总表（84 种）](#第七卷b--太空服全量总表84-种)
- [第八卷B · MOD 模块全量总表（132 种）](#第八卷b--mod-模块全量总表132-种)
- [附录 · 代码路径索引](#附录--代码路径索引)

---

# 第一卷 · 防具系统机制

## 1.1 护甲数值体系

**代码**: `code/__DEFINES/~nova_defines/armor_defines.dm`

| 常量 | 值 | 含义 |
|---|---|---|
| ARMOR_LEVEL_TINY | **10** | 微量防护 |
| ARMOR_LEVEL_WEAK | **30** | 弱防护 |
| ARMOR_LEVEL_MID | **50** | 中等防护 |
| ARMOR_LEVEL_INSANE | **90** | 近乎免疫 |
| WOUND_ARMOR_WEAK | 10 | 伤口防护弱 |
| WOUND_ARMOR_STANDARD | 20 | 伤口防护标准 |
| WOUND_ARMOR_HIGH | 30 | 伤口防护高 |

**护甲类型**（/datum/armor/，九项）：melee（近战）→bullet（子弹）→laser（激光）→energy（能量）→bomb（爆炸）→bio（生物/毒）→fire（火焰）→acid（酸液）→wound（伤口抗性）

> **注**：本代码库护甲用现代 `armor = list()` 格式，**无 BRUTE/BURN/TOX/STAMINA 字段**（对应关系：melee=BRUTE、bio=TOX、fire=BURN，无 STAMINA 项）。

## 1.2 护甲堆叠机制（关键）

**代码**: `code/modules/mob/living/carbon/human/human_defense.dm`

```
check_armor:
  保护 = 100
  每件覆盖部位的衣物: 保护 ×= (100 - 该衣护甲) × 0.01   ← 乘算堆叠！
  最终护甲 = 100 - 保护
```

**乘算堆叠示例**：外套 50% + 制服 10% 同部位 = 100 - (100-50)×0.01 × (100-10)×0.01 = **55% 减免**（不是 60%）——永远叠不到 100%。

**要点**：
- **乘算堆叠**：穿多层护甲会叠加但**递减**（第一件 50% 减伤，第二件 50% 变 25% 总减伤）
- **部位覆盖**：每件衣服只保护 body_parts_covered 指定的部位
- **平均机制**：不指定部位攻击→所有肢体护甲**取平均**
- **生理护甲**：物种/生理改造的护甲与衣物乘算

## 1.3 弹道反射与格挡

| 机制 | 实现 |
|---|---|
| **弹道反射** | 外套/头盔 IsReflect → 反射弹道（check_reflect） |
| **格挡** | 手持物/穿戴物 block_chance - (AP 差/2) + 伤害修正 |
| **AP 穿透** | 武器护甲穿透降低格挡/护甲效果 |

---

# 第二卷 · 外套与护甲

**代码**: `clothing/suits/`（5,045 行）——58 种护甲外套

## 2.1 S 档 · 传说护甲（全面防护）

| 护甲 | 近战 | 子弹 | 激光 | 能量 | 爆炸 | 特性 |
|---|---|---|---|---|---|---|
| **重甲 Heavy Armor** | **80** | **80** | 50 | 50 | **100** | bio/fire/acid 全 90-100——最强肉盾 |
| **战争领主金甲 Warlord** | **70** | 60 | **70** | **70** | 40 | 金板甲；wound 30；6s 脱卸 |
| **船长甲 Captain's Carapace** | 50 | 40 | 50 | 50 | 25 | fire 100/acid 90；8s 脱卸 |
| **MK.I SWAT 服** | 40 | 30 | 30 | 40 | **50** | bio 90/fire 100/acid 100 |

## 2.2 A 档 · 高级护甲（专业防护）

| 护甲 | 近战 | 子弹 | 激光 | 特性 |
|---|---|---|---|---|
| **战术装甲背心 Marine** | **50** | **50** | 30 | bio 100/bomb 50——全面均衡 |
| **防弹衣 Bulletproof** | 15 | **60** | 10 | 子弹特化 |
| **激光反射背心 Reflector** | 10 | 10 | **60** | 激光/能量特化；fire/acid 100 |
| **防暴服 Riot Suit** | **50** | 10 | 10 | 近战特化；电击免疫（siemens 0） |
| **反应装甲 Reactive** | — | — | — | fire/acid 100；50% 触发/手动开关/CD 10s；EMP 后 30s 坏效果 |
| ├ 传送版 | | | | 受击随机传送 6 格 |
| ├ 燃烧版 | | | | 点燃 6 格敌人 |
| ├ 隐形版 | | | | 隐身 4s+逃跑幻影 |
| ├ 特斯拉版 | | | | 电击免疫+电弧 2.5 万功率/20 格 |
| ├ 斥力版 | | | | 击退 7 格 |
| ├ 桌子版 | | | | 砸桌+传送 |
| ├ 幻觉版 | | | | 疯狂免疫+幻觉脉冲 |
| ├ 生物重排版 | | | | 重排 5 格生物肢体 |
| ├ 路障版 | | | | 推开+异界屏障 |
| ├ 附身版 | | | | CD 40s 闹鬼物体群 |
| ├ 气象版 | | | | CD 30s 雷暴闪电 |
| **站防者大衣 Militia** | 40 | 40 | 30 | wound 30 |
| **粗制胸甲 Crude** | 45 | 25 | 25 | 手工制作 |
| **消融大衣 Ablative** | — | — | — | 能量吸收 |
| **安保夹克 Secjacket** | — | — | — | 安保制服 |

## 2.3 B 档 · 中等护甲

| 护甲 | 近战 | 子弹 | 激光 | 特性 |
|---|---|---|---|---|
| **标准护甲 Armor** | 35 | 30 | 30 | 全防均衡（基础军用） |
| **HoS 装甲大衣** | 30 | 30 | 30 | fire 70/acid 90 |
| **杜拉丝纤维背心 Durathread** | 20 | 10 | 30 | 纺织自制 |
| **俄式背心 Russian** | — | — | — | 俄式 |
| **俄式战斗大衣** | — | — | — | 战斗强化 |
| **骑士甲 Knight** | — | — | — | 中世纪 |

## 2.4 C 档 · 民用/功能护甲

| 护甲 | 特性 |
|---|---|
| **生物防护服 Bio Suit** | 生物隔离 |
| **炸弹服 Bomb Suit** | bomb 100（拆弹用） |
| **辐射服 Radiation** | bio 60 抗辐射 |
| **紧急消防服** | 灭火 |
| **西瓜甲/圣西瓜甲/桶瓜甲** | 彩蛋护甲 |
| **湿地板标志** | 站立减速 |
| **工作围裙/厨师围裙** | 职业 |
| **实验服 Labcoat** | 医务/科研 |
| **风衣（侦探）** | 低护甲 |
| **大衣（冬日）** | 保暖（各职业版） |

## 2.5 护甲获取途径

| 档位 | 获取 |
|---|---|
| S 档 | 战争领主（角斗场）/重甲（军火库）/船长甲（船长室） |
| A 档 | 军火库（防弹/防暴）/战术（安保）/反应（科研） |
| B 档 | 军备/自制（杜拉丝）/俄式（废船） |
| C 档 | 民用/商店/职业标配 |

---

---
# 第三卷 · 制服与内层

**代码**: `clothing/under/`（4,887 行，35 文件）——100+ 种

> **通用**：所有制服自带 bio 10 + wound 5（基类）；可调节样式（alt-click）；内置制服传感器（关/生命/活体/坐标四档）；可挂 5 件配件。

## 3.1 岗位制服（职位标记）

| 制服 | 追加护甲 | 特性 |
|---|---|---|
| **安保制服** | +melee 10/fire 30/acid 30/wound 10 | 传感器默认坐标 |
| **HoS 制服** | +melee 10/**fire 50/acid 50** | 高抗 |
| 医疗连体服 | +bio 50 | 防生化 |
| **化学家服** | +**fire 50/acid 65** | 防火防酸特化 |
| 囚犯连体服 | 基值 | 传感器**锁定全开**（追踪囚犯） |
| 星际警察/巡警 | +10 三抗 | 事件 |

## 3.2 制服配件（NOVA 织带替代战术背心模块）

| 配件 | 功能 |
|---|---|
| **NOVA 织带** | 3 格存储（替代已移除的战术背心模块） |
| tinypacks 腰包/胸包 | 小存储 |
| 臂章/勋章/背带 | 装饰+职位标记 |

## 3.3 反派制服（15 种精选 · 全量见第三卷B）

> 反派制服主体为 `clothing/under/syndicate.dm` 的 15 款辛迪加制服（核弹队/幽灵队员各岗位同款）；异形服属外套类（第二卷B `suit/costume/xenos`），血虫/吸血鬼无专属制服。

| 制服 | 路径 | 特性 |
|---|---|---|
| **战术高领衫**（tactical turtleneck） | `under/syndicate` | melee 10/fire 50/acid 40/wound 10，无传感器 |
| **战术裙高领衫**（tactical skirtleneck） | `under/syndicate/skirt` | 同上，裙装版 |
| **血红潜行服**（blood-red sneaksuit） | `under/syndicate/bloodred` | 另加子弹/激光/能量 10，防火防酸，钓鱼 -4 |
| **血红睡衣**（blood-red pajamas） | `under/syndicate/bloodred/sleepytime` | fire 50/acid 40，睡衣版 |
| **战术酷高领衫**（tacticool turtleneck） | `under/syndicate/tacticool` | fire 50/acid 40，有传感器，顽固污渍 |
| **战术酷裙高领衫**（tacticool skirtleneck） | `under/syndicate/tacticool/skirt` | 同上，裙装版 |
| **狙击手战术西装**（tactical turtleneck suit） | `under/syndicate/sniper` | 民用西装伪装，不可调节 |
| **迷彩作训服**（camouflage fatigues） | `under/syndicate/camo` | 军绿迷彩 |
| **Cybersun 商务服**（Cybersun businesswear） | `under/syndicate/cybersun` | 黑橙商务，防火材质 |
| **地砖迷彩作训服**（floortile camouflage fatigues） | `under/syndicate/floortilecamo` | 地砖迷彩，钓鱼 -5 |
| **Ratnik 5 运动服**（Ratnik 5 tracksuit） | `under/syndicate/soviet` | melee 10 |
| **战斗制服**（combat uniform） | `under/syndicate/combat` | 口袋多，适合行动 |
| **高级军用运动服**（advanced military tracksuit） | `under/syndicate/rus_army` | melee 5 |
| **战术手术服**（tactical scrubs） | `under/syndicate/scrubs` | melee 10/bio 50/fire 50/acid 40，钓鱼 -3 |
| **战术酷环境服**（tacticool envirosuit） | `under/plasmaman/syndicate` | 等离子人款，防火，无传感器 |

## 3.4 民用/狂欢服（60+ 种）

| 类别 | 制服 |
|---|---|
| 基础 | 各色连体服/工装/衬衫 |
| **狂欢服** | 闪光服/鬼魂床单/纸箱服/熊服/鲤鱼服/柯基服/木乃伊/稻草人/机器人服/机甲服 |
| 贵族 | 燕尾服/西装/长衫/旗袍 |
| 特色 | 旧 T 恤 12 款（洗 5 次缩水）/哈斯塔袍/锁子甲/尤里/税务局 |



---

---

# 第二卷B · 外套全量总表（252 种）

| 名称 | 路径 | 护甲（近战/子弹/激光/能量/爆炸/生物/火/酸/伤口） |
|---|---|---|
| **服**（suit） | `suit` | 无 |
| **ablative 兜帽**（ablative hood） | `head/hooded/ablative` | 10 / 10 / 60 / 60 / 100 / 100 |
| **风衣 ablative**（ablative trenchcoat） | `suit/hooded/ablative` | 10 / 10 / 60 / 60 / 100 / 100 |
| **护甲**（armor） | `suit/armor` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **护甲 背心**（armor vest） | `suit/armor/vest` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **pre 护甲 背心**（press armor vest） | `suit/armor/vest/press` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **战术护甲背心**（tactical armor vest） | `suit/armor/vest/marine` | 50 / 50 / 30 / 25 / 50 / 100 / 40 / 50 / 20 |
| **战术护甲背心 large**（large tactical armor vest） | `suit/armor/vest/marine/security` | 50 / 50 / 30 / 25 / 50 / 100 / 40 / 50 / 20 |
| **战术 通用 护甲 背心**（tactical utility armor vest） | `suit/armor/vest/marine/engineer` | 50 / 50 / 30 / 25 / 50 / 100 / 40 / 50 / 20 |
| **战术 medic 护甲 背心**（tactical medic's armor vest） | `suit/armor/vest/marine/medic` | 50 / 50 / 30 / 25 / 50 / 100 / 40 / 50 / 20 |
| **degrading 护甲 背心**（degrading armor vest） | `suit/armor/vest/old` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **large 护甲 背心**（large armor vest） | `suit/armor/vest/blueshirt` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **胸甲**（cuirass） | `suit/armor/vest/cuirass` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **长大衣 装甲**（armored greatcoat） | `suit/armor/hos` | 30 / 30 / 30 / 40 / 25 / 70 / 90 / 10 |
| **风衣 装甲**（armored trenchcoat） | `suit/armor/hos/trenchcoat` | 30 / 30 / 30 / 40 / 25 / 70 / 90 / 10 |
| **安保主管 冬 trenchcoat**（head of security's winter trenchcoat） | `suit/armor/hos/trenchcoat/winter` | 30 / 30 / 30 / 40 / 25 / 70 / 90 / 10 |
| **安保主管 阅兵 夹克**（Head of Security's parade jacket） | `suit/armor/hos/hos_formal` | 30 / 30 / 30 / 40 / 25 / 70 / 90 / 10 |
| **典狱长 夹克**（warden's jacket） | `suit/armor/vest/warden` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **典狱长 装甲 夹克**（warden's armored jacket） | `suit/armor/vest/warden/alt` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **ecurity 夹克**（security jacket） | `suit/armor/vest/secjacket` | /datum/armor/armor_secjacket |
| **大衣 ecurity**（security overcoat） | `suit/armor/vest/leather` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **船长甲壳**（captain's carapace） | `suit/armor/vest/capcarapace` | 50 / 40 / 50 / 50 / 25 / 100 / 90 / 10 |
| **辛迪加 船长 背心**（syndicate captain's vest） | `suit/armor/vest/capcarapace/syndicate` | 50 / 40 / 50 / 50 / 25 / 100 / 90 / 10 |
| **阅兵大衣 船长**（captain's parade coat） | `suit/armor/vest/capcarapace/captains_formal` | 50 / 40 / 50 / 50 / 25 / 100 / 90 / 10 |
| **防暴服**（riot suit） | `suit/armor/riot` | 50 / 10 / 10 / 10 / 80 / 80 / 20 |
| **气球背心**（balloon vest） | `suit/armor/balloon_vest` | 10 / 10 / 10 / 60 / 50 |
| **防弹衣**（bulletproof armor） | `suit/armor/bulletproof` | 15 / 60 / 10 / 10 / 40 / 50 / 50 / 20 |
| **reflector 背心**（reflector vest） | `suit/armor/laserproof` | 10 / 10 / 60 / 60 / 100 / 100 |
| **防弹背心 detective**（detective's flak vest） | `suit/armor/vest/det_suit` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **特警 MK.I Suit**（MK.I SWAT Suit） | `suit/armor/swat` | 40 / 30 / 30 / 40 / 50 / 90 / 100 / 100 / 15 |
| **重甲**（heavy armor） | `suit/armor/heavy` | 80 / 80 / 50 / 50 / 100 / 100 / 90 / 90 |
| **雷神穹顶 uit**（thunderdome suit） | `suit/armor/tdome/red` | 无 |
| **雷神穹顶 uit**（thunderdome suit） | `suit/armor/tdome/green` | 无 |
| **雷神穹顶 uit**（thunderdome suit） | `suit/armor/tdome/holosuit` | 10 / 10 |
| **板甲**（plate armour） | `suit/armor/riot/knight` | 50 / 10 / 10 / 10 / 80 / 80 / 20 |
| **骑士 armour**（knight armour） | `suit/armor/riot/knight/greyscale` | 35 / 10 / 10 / 10 / 10 / 10 / 40 / 40 |
| **杜拉丝 背心**（durathread vest） | `suit/armor/vest/durathread` | 20 / 10 / 30 / 40 / 15 / 40 / 50 |
| **俄式 背心**（russian vest） | `suit/armor/vest/russian` | 25 / 30 / 10 / 10 / 20 / 50 / 10 |
| **俄式 战斗 大衣**（russian battle coat） | `suit/armor/vest/russian_coat` | 25 / 20 / 20 / 30 / 20 / 50 / 50 / 10 |
| **远古大气人 护甲**（Elder Atmosian Armor） | `suit/armor/elder_atmosian` | 25 / 20 / 30 / 30 / 85 / 10 / 65 / 40 / 15 |
| **中央指挥部 正式 大衣**（CentCom formal coat） | `suit/armor/centcom_formal` | 35 / 40 / 40 / 50 / 35 / 10 / 10 / 60 |
| **人事主管 大衣**（head of personnel's coat） | `suit/armor/vest/hop` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **站防者 大衣**（station defender's coat） | `suit/armor/militia` | 40 / 40 / 30 / 25 / 50 / 40 / 50 / 30 |
| **粗制 chestplate**（Crude chestplate） | `suit/armor/vest/military` | 45 / 25 / 25 / 25 / 25 / 10 / 50 / 20 |
| **金板甲**（golden plate armor） | `suit/armor/riot/knight/warlord` | 70 / 60 / 70 / 70 / 40 / 50 / 50 / 30 |
| **watermelon 护甲**（watermelon armor） | `suit/armor/durability/watermelon` | 15 / 10 / 10 / 10 / 0 / 25 / 5 |
| **holymelon 护甲**（holymelon armor） | `suit/armor/durability/holymelon` | 15 / 10 / 10 / 10 / 0 / 25 / 5 |
| **barrelmelon 护甲**（barrelmelon armor） | `suit/armor/durability/barrelmelon` | 25 / 20 / 15 / 10 / 0 / 35 / 10 |
| **drachen 服**（drachen suit） | `suit/armor/dragoon` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **生物 兜帽**（bio hood） | `head/bio_hood` | 100 / 30 / 100 |
| **生物防护服**（bio suit） | `suit/bio_suit` | 100 / 30 / 100 |
| **plague 医生 uit**（plague doctor suit） | `suit/bio_suit/plaguedoctorsuit` | 100 / 30 / 100 |
| **披风 棕**（brown cloak） | `neck/cloak` | 无 |
| **安保主管 披风**（head of security's cloak） | `neck/cloak/hos` | 无 |
| **军需官 披风**（quartermaster's cloak） | `neck/cloak/qm` | 无 |
| **首席医疗官 披风**（chief medical officer's cloak） | `neck/cloak/cmo` | 无 |
| **总工程师 披风**（chief engineer's cloak） | `neck/cloak/ce` | 无 |
| **研究主任 披风**（research director's cloak） | `neck/cloak/rd` | 无 |
| **船长 披风**（captain's cloak） | `neck/cloak/cap` | 无 |
| **人事主管 披风**（head of personnel's cloak） | `neck/cloak/hop` | 无 |
| **披风 传奇 gamer**（legendary gamer's cloak） | `neck/cloak/skill_reward/gaming` | 无 |
| **披风 传奇 cleaner**（legendary cleaner's cloak） | `neck/cloak/skill_reward/cleaning` | 无 |
| **披风 传奇 miner**（legendary miner's cloak） | `neck/cloak/skill_reward/mining` | 无 |
| **老兵披风 传奇**（legendary veteran's cloak） | `neck/cloak/skill_reward/playing` | 无 |
| **flashy 戏服**（flashy costume） | `suit/hooded/flashsuit` | 无 |
| **闪光 按钮**（flash button） | `head/hooded/flashsuit` | 无 |
| **海盗 大衣**（pirate coat） | `suit/costume/pirate` | 无 |
| **海盗 船长 大衣**（pirate captain coat） | `suit/costume/pirate/captain` | 无 |
| **cyborg 服**（cyborg suit） | `suit/costume/cyborg_suit` | 无 |
| **justice 服**（justice suit） | `suit/costume/justice` | 35 / 30 / 30 / 40 / 25 / 50 / 50 |
| **judge 长袍**（judge's robe） | `suit/costume/judgerobe` | 无 |
| **太空服 黑 and 红 replica**（black and red space suit replica） | `suit/syndicatefake` | 无 |
| **Hastur 长袍**（Hastur's robe） | `suit/costume/hastur` | 无 |
| **Imperium monk 服**（Imperium monk suit） | `suit/costume/imperium_monk` | 无 |
| **鸡 uit**（chicken suit） | `suit/costume/chickensuit` | 无 |
| **monkey 服**（monkey suit） | `suit/costume/monkeysuit` | 无 |
| **披风 owl**（owl cloak） | `suit/toggle/owlwings` | 无 |
| **披风 griffon**（griffon cloak） | `suit/toggle/owlwings/griffinwings` | 无 |
| **cardborg 服**（cardborg suit） | `suit/costume/cardborg` | 无 |
| **雪人 套装**（snowman outfit） | `suit/costume/snowman` | 无 |
| **斗篷**（poncho） | `suit/costume/poncho` | 无 |
| **绿 斗篷**（green poncho） | `suit/costume/poncho/green` | 无 |
| **红 斗篷**（red poncho） | `suit/costume/poncho/red` | 无 |
| **斗篷 of hame**（poncho of shame） | `suit/costume/poncho/ponchoshame` | 无 |
| **白 dre**（white dress） | `suit/costume/whitedress` | 无 |
| **鲤 costume**（carp costume） | `suit/hooded/carp_costume` | 无 |
| **鲤 兜帽**（carp hood） | `head/hooded/carp_hood` | 无 |
| **太空服 鲤**（carp space suit） | `suit/hooded/carp_costume/spaceproof` | 100 / 60 / 75 |
| **鲤 头盔**（carp helmet） | `head/hooded/carp_hood/spaceproof` | 100 / 60 / 75 |
| **太空服 battered 鲤**（battered carp space suit） | `suit/hooded/carp_costume/spaceproof/old` | 100 / 60 / 75 |
| **corgi 兜帽**（corgi hood） | `head/hooded/ian_hood` | 无 |
| **蜜蜂 兜帽**（bee hood） | `head/hooded/bee_hood` | 无 |
| **hark 兜帽**（shark hood） | `head/hooded/shark_hood` | 无 |
| **hork 兜帽**（shork hood） | `head/hooded/shork_hood` | 无 |
| **bloated human 头**（bloated human head） | `head/hooded/human_head` | 无 |
| **hrine maiden outfit**（shrine maiden's outfit） | `suit/costume/shrine_maiden` | 无 |
| **毛衣 triped**（striped sweater） | `suit/costume/striped_sweater` | 无 |
| **dracula 大衣**（dracula coat） | `suit/costume/dracula` | 无 |
| **实验服 医生 freeze**（doctor freeze's labcoat） | `suit/costume/drfreeze_coat` | 无 |
| **gothic 大衣**（gothic coat） | `suit/costume/gothcoat` | 无 |
| **异形 uit**（xenos suit） | `suit/costume/xenos` | 无 |
| **pharoah 束腰外衣**（pharoah tunic） | `suit/costume/nemes` | 无 |
| **红 changshan**（red changshan） | `suit/costume/changshan_red` | 无 |
| **蓝 changshan**（blue changshan） | `suit/costume/changshan_blue` | 无 |
| **红 cheongsam**（red cheongsam） | `suit/costume/cheongsam_red` | 无 |
| **蓝 cheongsam**（blue cheongsam） | `suit/costume/cheongsam_blue` | 无 |
| **青铜 uit**（bronze suit） | `suit/costume/bronze` | 5 / 10 / 20 / 20 |
| **mystic 长袍**（mystic's robe） | `suit/hooded/mysticrobe` | 无 |
| **mystic 兜帽**（mystic's hood） | `head/hooded/mysticrobe` | 无 |
| **coordinator 夹克**（coordinator jacket） | `suit/coordinator` | 25 / 15 / 25 / 35 / 25 / 50 / 50 |
| **夏威夷 外衬衫**（hawaiian overshirt） | `suit/costume/hawaiian` | 无 |
| **橄榄球 protective gear**（football protective gear） | `suit/costume/football_armor` | 无 |
| **comedian 大衣**（comedian coat） | `suit/costume/joker` | 无 |
| **连帽衫 decker**（decker hoodie） | `suit/costume/deckers` | 无 |
| **oviet 装甲 大衣**（soviet armored coat） | `suit/costume/soviet` | 无 |
| **yuri initiate 大衣**（yuri initiate coat） | `suit/costume/yuri` | 无 |
| **Lost M.C. 露指**（Lost M.C. cut） | `suit/costume/tmc` | 无 |
| **powder ganger 夹克**（powder ganger jacket） | `suit/costume/pg` | 无 |
| **internal revenue ervice 夹克**（internal revenue service jacket） | `suit/costume/irs` | 无 |
| **熊 uit**（bear suit） | `suit/costume/bear_suit` | 无 |
| **以太 raincoat**（ethereal raincoat） | `suit/hooded/ethereal_raincoat` | 无 |
| **小径守望 oilcoat**（trailwarden oilcoat） | `suit/hooded/ethereal_raincoat/trailwarden` | 无 |
| **以太 rainhood**（ethereal rainhood） | `head/hooded/ethereal_rainhood` | 无 |
| **鬼魂 heet**（ghost sheet） | `suit/costume/ghost_sheet` | 无 |
| **吓人 ghost**（spooky ghost） | `suit/spooky_ghost_sheet` | 无 |
| **毛衣 夹克**（sweater jacket） | `suit/toggle/jacket/sweater` | 无 |
| **风衣**（trenchcoat） | `suit/toggle/jacket/trenchcoat` | 无 |
| **西装夹克 夹克**（blazer jacket） | `suit/jacket/blazer` | 无 |
| **oversized 夹克**（oversized jacket） | `suit/jacket/oversized` | 无 |
| **华丽 fur 大衣**（fancy fur coat） | `suit/jacket/fancy` | 无 |
| **轰炸机夹克**（bomber jacket） | `suit/jacket/bomber` | 无 |
| **皮革 夹克**（leather jacket） | `suit/jacket/leather` | 无 |
| **biker 夹克**（biker jacket） | `suit/jacket/leather/biker` | 无 |
| **棉服夹克**（puffer jacket） | `suit/jacket/puffer` | 50 |
| **puffer 背心**（puffer vest） | `suit/jacket/puffer/vest` | 30 |
| **军事 夹克**（military jacket） | `suit/jacket/miljacket` | 无 |
| **letterman 夹克**（letterman jacket） | `suit/jacket/letterman` | 无 |
| **红 letterman 夹克**（red letterman jacket） | `suit/jacket/letterman_red` | 无 |
| **blood-red letterman 夹克**（blood-red letterman jacket） | `suit/jacket/letterman_syndie` | 无 |
| **蓝 letterman 夹克**（blue letterman jacket） | `suit/jacket/letterman_nanotrasen` | 无 |
| **围裙**（apron） | `suit/apron` | 50 |
| **防水裤 horticultural**（horticultural waders） | `suit/apron/waders` | 50 |
| **连体工装**（coveralls） | `suit/apron/overalls` | 50 |
| **阅兵夹克 船长**（captain's parade jacket） | `suit/jacket/capjacket` | 无 |
| **围裙 chef**（chef's apron） | `suit/toggle/chef` | 50 |
| **围裙 cook**（cook's apron） | `suit/apron/chef` | 50 |
| **棕色风衣**（brown trenchcoat） | `suit/toggle/jacket/det_trench` | 25 / 10 / 25 / 35 / 45 |
| **风衣 noir**（noir trenchcoat） | `suit/toggle/jacket/det_trench/noir` | 25 / 10 / 25 / 35 / 45 |
| **棕 西装夹克 夹克**（brown blazer jacket） | `suit/jacket/det_suit` | 25 / 10 / 25 / 35 / 45 |
| **noir 西装夹克 夹克**（noir blazer jacket） | `suit/jacket/det_suit/noir` | 25 / 10 / 25 / 35 / 45 |
| **轰炸机夹克 aerostatic**（aerostatic bomber jacket） | `suit/jacket/det_suit/kim` | 25 / 10 / 25 / 35 / 45 |
| **disco a 西装夹克**（disco ass blazer） | `suit/jacket/det_suit/disco` | 25 / 10 / 25 / 35 / 45 |
| **警示背心**（hazard vest） | `suit/hazardvest` | 无 |
| **蓝 正式 uit 夹克**（blue formal suit jacket） | `suit/toggle/lawyer` | 无 |
| **紫 正式 uit 夹克**（purple formal suit jacket） | `suit/toggle/lawyer/purple` | 无 |
| **黑 正式 uit 夹克**（black formal suit jacket） | `suit/toggle/lawyer/black` | 无 |
| **货运 gorka**（cargo gorka） | `suit/toggle/cargo_tech` | 无 |
| **军需官 overcoat**（quartermaster's overcoat） | `suit/jacket/quartermaster` | 无 |
| **正式 uit 夹克**（formal suit jacket） | `suit/toggle/lawyer/greyscale` | 无 |
| **背带**（suspenders） | `suit/toggle/suspenders` | 无 |
| **军官 ecurity 夹克**（security officer's jacket） | `suit/jacket/officer/blue` | 无 |
| **军官 ecurity 夹克**（security officer's jacket） | `suit/jacket/officer/tan` | 无 |
| **典狱长 夹克**（warden's jacket） | `suit/jacket/warden/blue` | 无 |
| **典狱长 夹克**（warden's jacket） | `suit/jacket/warden/tan` | 无 |
| **安保主管 夹克**（head of security's jacket） | `suit/jacket/hos/blue` | 无 |
| **安保主管 夹克**（head of security's jacket） | `suit/jacket/hos/tan` | 无 |
| **围裙 urgical**（surgical apron） | `suit/apron/surgical` | 50 |
| **宝藏 猎手 大衣**（treasure hunter's coat） | `suit/jacket/curator` | 25 / 10 / 25 / 35 / 45 |
| **techpriest 长袍**（techpriest robes） | `suit/hooded/techpriest` | 无 |
| **techpriest 兜帽**（techpriest's hood） | `head/hooded/techpriest` | 无 |
| **工装裤 大气**（atmospherics overalls） | `suit/atmos_overalls` | 100 / 50 |
| **实验服**（labcoat） | `suit/toggle/labcoat` | 50 / 50 / 50 |
| **首席医疗官 labcoat**（chief medical officer's labcoat） | `suit/toggle/labcoat/cmo` | 50 / 50 / 50 |
| **急救员 夹克**（paramedic's jacket） | `suit/toggle/labcoat/paramedic` | 50 / 50 / 50 |
| **实验服 The Mad**（The Mad's labcoat） | `suit/toggle/labcoat/mad` | 50 / 50 / 50 |
| **实验服 geneticist**（geneticist labcoat） | `suit/toggle/labcoat/genetics` | 50 / 50 / 50 |
| **实验服 chemist**（chemist labcoat） | `suit/toggle/labcoat/chemist` | 50 / 50 / 50 |
| **病毒学家 labcoat**（virologist labcoat） | `suit/toggle/labcoat/virologist` | 50 / 50 / 50 |
| **实验服 coroner**（coroner labcoat） | `suit/toggle/labcoat/coroner` | 50 / 50 / 50 |
| **实验服 cientist**（scientist labcoat） | `suit/toggle/labcoat/science` | 50 / 50 / 50 |
| **实验服 机器人学家**（roboticist labcoat） | `suit/toggle/labcoat/roboticist` | 50 / 50 / 50 |
| **实验服 interdyne**（interdyne labcoat） | `suit/toggle/labcoat/interdyne` | 50 / 50 / 50 |
| **研究主任 大衣**（research director's coat） | `suit/toggle/labcoat/research_director` | 75 / 75 / 75 |
| **激光标签护甲 蓝**（blue laser tag armor） | `suit/bluetag` | 无 |
| **激光标签护甲 红**（red laser tag armor） | `suit/redtag` | 无 |
| **蛾族 flightsuit**（mothic flightsuit） | `suit/mothcoat` | 无 |
| **蛾族 mantella**（mothic mantella） | `suit/mothcoat/winter` | 无 |
| **反应装甲**（reactive armor） | `suit/armor/reactive` | 100 / 100 |
| **反应 teleport 护甲**（reactive teleport armor） | `suit/armor/reactive/teleport` | 100 / 100 |
| **反应 incendiary 护甲**（reactive incendiary armor） | `suit/armor/reactive/fire` | 100 / 100 |
| **反应 tealth 护甲**（reactive stealth armor） | `suit/armor/reactive/stealth` | 100 / 100 |
| **反应 tesla 护甲**（reactive tesla armor） | `suit/armor/reactive/tesla` | 100 / 100 |
| **反应 repulse 护甲**（reactive repulse armor） | `suit/armor/reactive/repulse` | 100 / 100 |
| **反应 table 护甲**（reactive table armor） | `suit/armor/reactive/table` | 100 / 100 |
| **反应 hallucinating 护甲**（reactive hallucinating armor） | `suit/armor/reactive/hallucinating` | 100 / 100 |
| **反应 bioscrambling 护甲**（reactive bioscrambling armor） | `suit/armor/reactive/bioscrambling` | 100 / 100 |
| **反应 barricade 护甲**（reactive barricade armor） | `suit/armor/reactive/barricade` | 100 / 100 |
| **反应 附身 护甲**（reactive possession armor） | `suit/armor/reactive/ectoplasm` | 100 / 100 |
| **反应 weather 护甲**（reactive weather armor） | `suit/armor/reactive/weather` | 100 / 100 |
| **well-worn hirt**（well-worn shirt） | `suit/costume/wellworn_shirt` | 无 |
| **pro-skub hirt**（pro-skub shirt） | `suit/costume/wellworn_shirt/skub` | 无 |
| **anti-skub hirt**（anti-skub shirt） | `suit/costume/wellworn_shirt/skub/anti` | 无 |
| **well-worn graphic hirt**（well-worn graphic shirt） | `suit/costume/wellworn_shirt/graphic` | 无 |
| **well-worn ian hirt**（well-worn ian shirt） | `suit/costume/wellworn_shirt/graphic/ian` | 无 |
| **worn-out hirt**（worn-out shirt） | `suit/costume/wellworn_shirt/wornout` | 无 |
| **worn-out graphic hirt**（worn-out graphic shirt） | `suit/costume/wellworn_shirt/wornout/graphic` | 无 |
| **worn-out ian hirt**（worn-out ian shirt） | `suit/costume/wellworn_shirt/wornout/graphic/ian` | 无 |
| **messy worn-out hirt**（messy worn-out shirt） | `suit/costume/wellworn_shirt/messy` | 无 |
| **凌乱 graphic 衬衫**（messy graphic shirt） | `suit/costume/wellworn_shirt/messy/graphic` | 无 |
| **凌乱 ian 衬衫**（messy ian shirt） | `suit/costume/wellworn_shirt/messy/graphic/ian` | 无 |
| **gamer 衬衫**（gamer shirt） | `suit/costume/wellworn_shirt/messy/graphic/gamer` | 无 |
| **traight 夹克**（straight jacket） | `suit/jacket/straight_jacket` | 无 |
| **紧急防火服**（emergency firesuit） | `suit/utility/fire` | 15 / 5 / 20 / 20 / 20 / 50 / 100 / 50 |
| **防火服 重**（heavy firesuit） | `suit/utility/fire/heavy` | 15 / 5 / 20 / 20 / 20 / 50 / 100 / 50 |
| **防火服 大气**（atmospheric firesuit） | `suit/utility/fire/atmos` | 15 / 5 / 20 / 20 / 20 / 50 / 100 / 50 |
| **炸弹 兜帽**（bomb hood） | `head/utility/bomb_hood` | 20 / 20 / 30 / 100 / 50 / 80 / 50 |
| **炸弹服**（bomb suit） | `suit/utility/bomb_suit` | 20 / 20 / 30 / 100 / 50 / 80 / 50 |
| **辐射 兜帽**（radiation hood） | `head/utility/radiation` | 60 / 30 / 30 |
| **辐射服**（radiation suit） | `suit/utility/radiation` | 60 / 30 / 30 |
| **湿地 地板 标志**（wet floor sign） | `suit/caution` | 5 |
| **冬大衣**（winter coat） | `suit/hooded/wintercoat` | 10 |
| **冬 兜帽**（winter hood） | `head/hooded/winterhood` | 10 |
| **冬大衣 Endotherm**（Endotherm winter coat） | `suit/hooded/wintercoat/eva` | 10 / 10 / 10 / 50 / 50 / 20 |
| **Endotherm 冬 兜帽**（Endotherm winter hood） | `head/hooded/winterhood/eva` | 10 / 10 / 10 / 50 / 50 / 20 |
| **冬大衣 中央指挥部**（centcom winter coat） | `suit/hooded/wintercoat/centcom` | 35 / 40 / 40 / 50 / 35 / 10 / 10 / 60 |
| **冬大衣 船长**（captain's winter coat） | `suit/hooded/wintercoat/captain` | 25 / 30 / 30 / 40 / 25 / 50 |
| **人事主管 冬 大衣**（head of personnel's winter coat） | `suit/hooded/wintercoat/hop` | 10 / 15 / 15 / 25 / 10 / 35 |
| **冬大衣 hydroponic**（hydroponics winter coat） | `suit/hooded/wintercoat/hydro` | 10 |
| **冬大衣 janitor**（janitors winter coat） | `suit/hooded/wintercoat/janitor` | 10 |
| **冬夹克 ecurity**（security winter jacket） | `suit/hooded/wintercoat/security` | 25 / 15 / 30 / 40 / 25 / 45 |
| **冬大衣 医疗**（medical winter coat） | `suit/hooded/wintercoat/medical` | 40 / 10 / 20 |
| **首席医疗官 冬 大衣**（chief medical officer's winter coat） | `suit/hooded/wintercoat/medical/cmo` | 50 / 20 / 30 |
| **冬大衣 chemistry**（chemistry winter coat） | `suit/hooded/wintercoat/medical/chemistry` | 40 / 10 / 20 |
| **冬大衣 coroner**（coroner winter coat） | `suit/hooded/wintercoat/medical/coroner` | 40 / 10 / 20 |
| **冬大衣 病毒学**（virology winter coat） | `suit/hooded/wintercoat/medical/viro` | 40 / 10 / 20 |
| **冬大衣 paramedic**（paramedic winter coat） | `suit/hooded/wintercoat/medical/paramedic` | 40 / 10 / 20 |
| **冬大衣 cience**（science winter coat） | `suit/hooded/wintercoat/science` | 10 / 20 |
| **研究主任 冬 大衣**（research director's winter coat） | `suit/hooded/wintercoat/science/rd` | 20 / 30 |
| **冬大衣 robotic**（robotics winter coat） | `suit/hooded/wintercoat/science/robotics` | 10 / 20 |
| **冬大衣 genetic**（genetics winter coat） | `suit/hooded/wintercoat/science/genetics` | 10 / 20 |
| **冬大衣 工程**（engineering winter coat） | `suit/hooded/wintercoat/engineering` | 20 |
| **总工程师 冬 大衣**（chief engineer's winter coat） | `suit/hooded/wintercoat/engineering/ce` | 30 / 10 |
| **冬大衣 大气**（atmospherics winter coat） | `suit/hooded/wintercoat/engineering/atmos` | 20 |
| **冬大衣 货运**（cargo winter coat） | `suit/hooded/wintercoat/cargo` | 10 |
| **军需官 冬 大衣**（quartermaster's winter coat） | `suit/hooded/wintercoat/cargo/qm` | 10 |
| **冬大衣 采矿**（mining winter coat） | `suit/hooded/wintercoat/miner` | 10 |
| **冬大衣 tailored**（tailored winter coat） | `suit/hooded/wintercoat/custom` | 10 |
| **冬大衣 tailored 兜帽**（tailored winter coat hood） | `head/hooded/winterhood/custom` | 10 |
| **套头衫**（pullover） | `suit/hooded/wintercoat/pullover` | 10 |
| **套头衫 兜帽**（pullover hood） | `head/hooded/winterhood/pullover` | 10 |
| **拉链衫**（zipup） | `suit/hooded/wintercoat/zipup` | 10 |
| **拉链衫 兜帽**（zipup hood） | `head/hooded/winterhood/zipup` | 10 |


---

# 第三卷B · 制服全量总表（381 种）

| 名称 | 路径 | 护甲（近战/子弹/激光/能量/爆炸/生物/火/酸/伤口） |
|---|---|---|
| **内层**（under） | `under` | 10 / 5 |
| **配件**（Accessory） | `accessory` | 无 |
| **红 armband**（red armband） | `accessory/armband` | 无 |
| **ecurity deputy 臂章**（security deputy armband） | `accessory/armband/deputy` | 无 |
| **货运 bay guard armband**（cargo bay guard armband） | `accessory/armband/cargo` | 无 |
| **工程 guard armband**（engineering guard armband） | `accessory/armband/engine` | 无 |
| **cience 守卫 臂章**（science guard armband） | `accessory/armband/science` | 无 |
| **hydroponic 守卫 臂章**（hydroponics guard armband） | `accessory/armband/hydro` | 无 |
| **医疗 guard armband**（medical guard armband） | `accessory/armband/med` | 无 |
| **医疗 guard armband**（medical guard armband） | `accessory/armband/medblue` | 无 |
| **attorney badge**（attorney's badge） | `accessory/lawyers_badge` | 无 |
| **小丑 Pin**（Clown Pin） | `accessory/clown_enjoyer_pin` | 无 |
| **哑剧 Pin**（Mime Pin） | `accessory/mime_fan_pin` | 无 |
| **口袋 护板**（pocket protector） | `accessory/pocketprotector` | 无 |
| **狗牌**（Dogtag） | `accessory/dogtag` | 无 |
| **过敏 狗牌**（Allergy dogtag） | `accessory/dogtag/allergy` | 无 |
| **Pre-Approved Cyborg Candidate dogtag**（Pre-Approved Cyborg Candidate dogtag） | `accessory/dogtag/borg_ready` | 无 |
| **pride 徽章**（pride pin） | `accessory/pride` | 无 |
| **deaf 人员 徽章**（deaf personnel pin） | `accessory/deaf_pin` | 无 |
| **debt payer 徽章**（debt payer pin） | `accessory/debt_payer_pin` | 无 |
| **ubversive 徽章**（subversive pin） | `accessory/anti_sec_pin` | 无 |
| **记者 徽章**（press badge） | `accessory/press_badge` | 无 |
| **青铜 medal**（bronze medal） | `accessory/medal` | 无 |
| **distinguished conduct 勋章**（distinguished conduct medal） | `accessory/medal/conduct` | 无 |
| **青铜 heart medal**（bronze heart medal） | `accessory/medal/bronze_heart` | 无 |
| **绶带**（ribbon） | `accessory/medal/ribbon` | 无 |
| **\**（\） | `accessory/medal/ribbon/cargo` | 无 |
| **ilver 勋章**（silver medal） | `accessory/medal/silver` | 无 |
| **勋章 的 valor**（medal of valor） | `accessory/medal/silver/valor` | 无 |
| **robust ecurity 奖**（robust security award） | `accessory/medal/silver/security` | 无 |
| **人事主管 the 奖 for outstanding achievement in the field of excellence**（the head of personnel award for outstanding achievement in the field of excellence） | `accessory/medal/silver/excellence` | 无 |
| **卓越 在 Bureaucracy 勋章**（Excellence in Bureaucracy Medal） | `accessory/medal/silver/bureaucracy` | 无 |
| **金 勋章**（gold medal） | `accessory/medal/gold` | 无 |
| **exemplary performance 勋章**（exemplary performance medal） | `accessory/medal/med_medal` | 无 |
| **卓越 在 medicine 勋章**（excellence in medicine medal） | `accessory/medal/med_medal2` | 无 |
| **勋章 的 captaincy**（medal of captaincy） | `accessory/medal/gold/captain` | 无 |
| **勋章 的 exceptional heroism**（medal of exceptional heroism） | `accessory/medal/gold/heroism` | 无 |
| **等离子 medal**（plasma medal） | `accessory/medal/plasma` | 无 |
| **nobel cience 奖**（nobel sciences award） | `accessory/medal/plasma/nobel_science` | 无 |
| **紧急 ervice 奖**（emergency services award） | `accessory/medal/silver/emergency_services` | 无 |
| **精通奖 大气**（atmospheric mastery award） | `accessory/medal/silver/elder_atmosian` | 无 |
| **骨 talisman**（bone talisman） | `accessory/talisman` | 无 |
| **骷髅 护裆**（skull codpiece） | `accessory/skullcodpiece` | 无 |
| **inew 裙**（sinew skirt） | `accessory/skilt` | 无 |
| **马甲**（waistcoat） | `accessory/waistcoat` | 无 |
| **毛衣 背心**（sweater vest） | `accessory/sweatervest` | 无 |
| **heriff 背心**（sheriff vest） | `accessory/vest_sheriff` | 无 |
| **围裙 maid**（maid apron） | `accessory/maidapron` | 无 |
| **连体服**（jumpsuit） | `under/color` | 10 / 5 |
| **黑 连体服**（black jumpsuit） | `under/color/black` | 10 / 5 |
| **黑 连体短裙**（black jumpskirt） | `under/color/jumpskirt/black` | 无 |
| **灰 连体服**（grey jumpsuit） | `under/color/grey` | 10 / 5 |
| **灰 连体短裙**（grey jumpskirt） | `under/color/jumpskirt/grey` | 无 |
| **ancient 连体服**（ancient jumpsuit） | `under/color/grey/ancient` | 10 / 5 |
| **蓝 连体服**（blue jumpsuit） | `under/color/blue` | 10 / 5 |
| **蓝 连体短裙**（blue jumpskirt） | `under/color/jumpskirt/blue` | 无 |
| **绿 连体服**（green jumpsuit） | `under/color/green` | 10 / 5 |
| **绿 连体短裙**（green jumpskirt） | `under/color/jumpskirt/green` | 无 |
| **橙 连体服**（orange jumpsuit） | `under/color/orange` | 10 / 5 |
| **橙 连体短裙**（orange jumpskirt） | `under/color/jumpskirt/orange` | 无 |
| **粉 连体服**（pink jumpsuit） | `under/color/pink` | 10 / 5 |
| **粉 连体短裙**（pink jumpskirt） | `under/color/jumpskirt/pink` | 无 |
| **红 连体服**（red jumpsuit） | `under/color/red` | 10 / 5 |
| **红 连体短裙**（red jumpskirt） | `under/color/jumpskirt/red` | 无 |
| **白 连体服**（white jumpsuit） | `under/color/white` | 10 / 5 |
| **白 连体短裙**（white jumpskirt） | `under/color/jumpskirt/white` | 无 |
| **黄 连体服**（yellow jumpsuit） | `under/color/yellow` | 10 / 5 |
| **黄 连体短裙**（yellow jumpskirt） | `under/color/jumpskirt/yellow` | 无 |
| **深 蓝 连体服**（dark blue jumpsuit） | `under/color/darkblue` | 10 / 5 |
| **深 蓝 连体短裙**（dark blue jumpskirt） | `under/color/jumpskirt/darkblue` | 无 |
| **teal 连体服**（teal jumpsuit） | `under/color/teal` | 10 / 5 |
| **teal 连体短裙**（teal jumpskirt） | `under/color/jumpskirt/teal` | 无 |
| **轻 紫 连体服**（light purple jumpsuit） | `under/color/lightpurple` | 10 / 5 |
| **轻 紫 连体短裙**（light purple jumpskirt） | `under/color/jumpskirt/lightpurple` | 无 |
| **深 绿 连体服**（dark green jumpsuit） | `under/color/darkgreen` | 10 / 5 |
| **深 绿 连体短裙**（dark green jumpskirt） | `under/color/jumpskirt/darkgreen` | 无 |
| **轻 棕 连体服**（light brown jumpsuit） | `under/color/lightbrown` | 10 / 5 |
| **轻 棕 连体短裙**（light brown jumpskirt） | `under/color/jumpskirt/lightbrown` | 无 |
| **棕 连体服**（brown jumpsuit） | `under/color/brown` | 10 / 5 |
| **棕 连体短裙**（brown jumpskirt） | `under/color/jumpskirt/brown` | 无 |
| **栗色 连体服**（maroon jumpsuit） | `under/color/maroon` | 10 / 5 |
| **栗色 连体短裙**（maroon jumpskirt） | `under/color/jumpskirt/maroon` | 无 |
| **彩虹 连体服**（rainbow jumpsuit） | `under/color/rainbow` | 10 / 5 |
| **彩虹 连体短裙**（rainbow jumpskirt） | `under/color/jumpskirt/rainbow` | 无 |
| **Roman 护甲**（Roman armor） | `under/costume/roman` | 无 |
| **呆子 套装**（jabroni outfit） | `under/costume/jabroni` | 无 |
| **owl 制服**（owl uniform） | `under/costume/owl` | 无 |
| **griffon 制服**（griffon uniform） | `under/costume/griffin` | 无 |
| **choolgirl 制服**（schoolgirl uniform） | `under/costume/seifuku` | 无 |
| **海盗 outfit**（pirate outfit） | `under/costume/pirate` | 无 |
| **oviet 制服**（soviet uniform） | `under/costume/soviet` | 无 |
| **redcoat 制服**（redcoat uniform） | `under/costume/redcoat` | 无 |
| **苏格兰裙**（kilt） | `under/costume/kilt` | 无 |
| **gladiator 制服**（gladiator uniform） | `under/costume/gladiator` | 无 |
| **maid 戏服**（maid costume） | `under/costume/maid` | 无 |
| **geisha 服**（geisha suit） | `under/costume/geisha` | 无 |
| **黑 yukata**（black yukata） | `under/costume/yukata` | 无 |
| **绿 yukata**（green yukata） | `under/costume/yukata/green` | 无 |
| **白 yukata**（white yukata） | `under/costume/yukata/white` | 无 |
| **黑 kimono**（black kimono） | `under/costume/kimono` | 无 |
| **红 kimono**（red kimono） | `under/costume/kimono/red` | 无 |
| **紫 kimono**（purple kimono） | `under/costume/kimono/purple` | 无 |
| **villain 服**（villain suit） | `under/costume/villain` | 无 |
| **ailor 服**（sailor suit） | `under/costume/sailor` | 无 |
| **黄 performer outfit**（yellow performer's outfit） | `under/costume/singer/yellow` | 无 |
| **蓝 performer outfit**（blue performer's outfit） | `under/costume/singer/blue` | 无 |
| **红 performer outfit**（red performer's outfit） | `under/costume/singer/red` | 无 |
| **木乃伊绷带**（mummy wrapping） | `under/costume/mummy` | 无 |
| **稻草人衣**（scarecrow clothes） | `under/costume/scarecrow` | 无 |
| **dracula 大衣**（draculass coat） | `under/costume/draculass` | 无 |
| **医生 freeze 连体服**（doctor freeze's jumpsuit） | `under/costume/drfreeze` | 无 |
| **foam lobster 服**（foam lobster suit） | `under/costume/lobster` | 无 |
| **gondola 皮 uit**（gondola hide suit） | `under/costume/gondola` | 无 |
| **keleton 连体服**（skeleton jumpsuit） | `under/costume/skeleton` | 无 |
| **mech 飞行员 uit**（mech pilot's suit） | `under/costume/mech_suit` | 无 |
| **军官 俄式 制服**（Russian officer's uniform） | `under/costume/russian_officer` | /datum/armor/clothing_under/costume_russian_officer |
| **button-down hirt with lack**（button-down shirt with slacks） | `under/costume/buttondown/slacks` | 无 |
| **button-down hirt with hort**（button-down shirt with shorts） | `under/costume/buttondown/shorts` | 无 |
| **button-down hirt with kirt**（button-down shirt with skirt） | `under/costume/buttondown/skirt` | 无 |
| **jack bro 套装**（jack bros outfit） | `under/costume/jackbros` | 无 |
| **甲板工 套装**（deckers outfit） | `under/costume/deckers` | 无 |
| **橄榄球 制服**（football uniform） | `under/costume/football_suit` | 无 |
| **Swag 套装**（Swag outfit） | `under/costume/swagoutfit` | 无 |
| **referee 制服**（referee uniform） | `under/costume/referee` | 无 |
| **comedian 服**（comedian suit） | `under/costume/joker` | 无 |
| **yuri initiate 连体服**（yuri initiate jumpsuit） | `under/costume/yuri` | 无 |
| **dutch uit**（dutch's suit） | `under/costume/dutch` | 无 |
| **O.S.I. 连体服**（O.S.I. jumpsuit） | `under/costume/osi` | 无 |
| **Lost MC 服装**（Lost MC clothing） | `under/costume/tmc` | 无 |
| **martial 柔道服**（martial gi） | `under/costume/gi` | 无 |
| **acred 柔道服**（sacred gi） | `under/costume/gi/goku` | 无 |
| **traditional 服**（traditional suit） | `under/costume/traditional` | 无 |
| **皮革 loincloth**（leather loincloth） | `under/costume/loincloth` | 无 |
| **henchmen 连体服**（henchmen jumpsuit） | `under/costume/henchmen` | 无 |
| **re-enactor gambeson**（re-enactor's gambeson） | `under/costume/gamberson` | 无 |
| **wordsman gambeson**（swordsman's gambeson） | `under/costume/gamberson/military` | /datum/armor/clothing_under/rank_security |
| **船长 uit**（captain's suit） | `under/costume/captain` | 无 |
| **绿 uitskirt**（green suitskirt） | `under/costume/captain/skirt` | 无 |
| **人事主管 uit**（head of personnel's suit） | `under/costume/head_of_personnel` | 无 |
| **teal 短裙**（teal suitskirt） | `under/costume/head_of_personnel/skirt` | 无 |
| **以太 束腰外衣**（ethereal tunic） | `under/ethereal_tunic` | 10 / 5 |
| **小径守望 束腰外衣**（trailwarden tunic） | `under/ethereal_tunic/trailwarden` | 10 / 5 |
| **军需官 制服**（quartermaster's uniform） | `under/rank/cargo/qm` | 无 |
| **军需官 kirt**（quartermaster's skirt） | `under/rank/cargo/qm/skirt` | 无 |
| **货运 技师 制服**（cargo technician's uniform） | `under/rank/cargo/tech` | 无 |
| **货运 技师 hort**（cargo technician's shorts） | `under/rank/cargo/tech/alt` | 无 |
| **货运 技师 kirt**（cargo technician's skirt） | `under/rank/cargo/tech/skirt` | 无 |
| **货运 技师 hortskirt**（cargo technician's shortskirt） | `under/rank/cargo/tech/skirt/alt` | 无 |
| **haft miner 连体服**（shaft miner's jumpsuit） | `under/rank/cargo/miner` | /datum/armor/clothing_under/cargo_miner |
| **haft miner 连体服**（shaft miner's jumpsuit） | `under/rank/cargo/miner/lavaland` | /datum/armor/clothing_under/cargo_miner |
| **bitrunner 连体服**（bitrunner's jumpsuit） | `under/rank/cargo/bitrunner` | 无 |
| **中央指挥部 指挥官 uit**（CentCom commander's suit） | `under/rank/centcom/commander` | 无 |
| **中央指挥部 官员 uit**（CentCom official's suit） | `under/rank/centcom/official` | 无 |
| **中央指挥部 实习生 连体服**（CentCom intern's jumpsuit） | `under/rank/centcom/intern` | 无 |
| **高领衫 中央指挥部**（CentCom turtleneck suit） | `under/rank/centcom/officer` | 无 |
| **高领衫 中央指挥部 replica**（CentCom turtleneck replica） | `under/rank/centcom/officer/replica` | 无 |
| **高领衫 中央指挥部 kirt**（CentCom turtleneck skirt） | `under/rank/centcom/officer_skirt` | 无 |
| **高领衫 中央指挥部 kirt replica**（CentCom turtleneck skirt replica） | `under/rank/centcom/officer_skirt/replica` | 无 |
| **中央指挥部 指挥官 uitskirt**（CentCom commander's suitskirt） | `under/rank/centcom/centcom_skirt` | 无 |
| **战术 战斗 制服**（tactical combat uniform） | `under/rank/centcom/military` | /datum/armor/clothing_under/centcom_military |
| **战术 工程 制服**（tactical engineering uniform） | `under/rank/centcom/military/eng` | /datum/armor/clothing_under/centcom_military |
| **紫 bartender 制服**（purple bartender's uniform） | `under/rank/civilian/purple_bartender` | 无 |
| **牧师 连体服**（chaplain's jumpsuit） | `under/rank/civilian/chaplain` | 无 |
| **牧师 连体短裙**（chaplain's jumpskirt） | `under/rank/civilian/chaplain/skirt` | 无 |
| **人事主管 制服**（head of personnel's uniform） | `under/rank/civilian/head_of_personnel` | 无 |
| **人事主管 kirt**（head of personnel's skirt） | `under/rank/civilian/head_of_personnel/skirt` | 无 |
| **植物学家 连体服**（botanist's jumpsuit） | `under/rank/civilian/hydroponics` | /datum/armor/clothing_under/civilian_hydroponics |
| **植物学家 连体短裙**（botanist's jumpskirt） | `under/rank/civilian/hydroponics/skirt` | /datum/armor/clothing_under/civilian_hydroponics |
| **清洁工 连体服**（janitor's jumpsuit） | `under/rank/civilian/janitor` | /datum/armor/clothing_under/civilian_janitor |
| **清洁工 连体短裙**（janitor's jumpskirt） | `under/rank/civilian/janitor/skirt` | /datum/armor/clothing_under/civilian_janitor |
| **maid 制服**（maid uniform） | `under/rank/civilian/janitor/maid` | /datum/armor/clothing_under/civilian_janitor |
| **律师 uit**（Lawyer suit） | `under/rank/civilian/lawyer` | 无 |
| **律师 黑 uit**（lawyer black suit） | `under/rank/civilian/lawyer/black` | 无 |
| **律师 黑 uitskirt**（lawyer black suitskirt） | `under/rank/civilian/lawyer/black/skirt` | 无 |
| **律师 good uit**（good lawyer's suit） | `under/rank/civilian/lawyer/beige` | 无 |
| **律师 good uitskirt**（good lawyer's suitskirt） | `under/rank/civilian/lawyer/beige/skirt` | 无 |
| **律师 红 uit**（lawyer red suit） | `under/rank/civilian/lawyer/red` | 无 |
| **律师 红 uitskirt**（lawyer red suitskirt） | `under/rank/civilian/lawyer/red/skirt` | 无 |
| **律师 蓝 uit**（lawyer blue suit） | `under/rank/civilian/lawyer/blue` | 无 |
| **律师 蓝 uitskirt**（lawyer blue suitskirt） | `under/rank/civilian/lawyer/blue/skirt` | 无 |
| **蓝 buttondown uit**（blue buttondown suit） | `under/rank/civilian/lawyer/bluesuit` | 无 |
| **蓝 buttondown uitskirt**（blue buttondown suitskirt） | `under/rank/civilian/lawyer/bluesuit/skirt` | 无 |
| **紫 uit**（purple suit） | `under/rank/civilian/lawyer/purpsuit` | 无 |
| **紫 uitskirt**（purple suitskirt） | `under/rank/civilian/lawyer/purpsuit/skirt` | 无 |
| **蓝 galaxy uit**（blue galaxy suit） | `under/rank/civilian/lawyer/galaxy` | 无 |
| **蓝 galaxy uitskirt**（blue galaxy suitskirt） | `under/rank/civilian/lawyer/galaxy/skirt` | 无 |
| **红 galaxy uit**（red galaxy suit） | `under/rank/civilian/lawyer/galaxy/red` | 无 |
| **红 galaxy uitskirt**（red galaxy suitskirt） | `under/rank/civilian/lawyer/galaxy/red/skirt` | 无 |
| **grilling 短裤**（grilling shorts） | `under/rank/civilian/cookjorts` | 无 |
| **哑剧 outfit**（mime's outfit） | `under/rank/civilian/mime` | 无 |
| **哑剧 kirt**（mime's skirt） | `under/rank/civilian/mime/skirt` | 无 |
| **exy 哑剧 outfit**（sexy mime outfit） | `under/rank/civilian/mime/sexy` | 无 |
| **小丑 uit**（clown suit） | `under/rank/civilian/clown` | 无 |
| **蓝 小丑 uit**（blue clown suit） | `under/rank/civilian/clown/blue` | 无 |
| **绿 小丑 uit**（green clown suit） | `under/rank/civilian/clown/green` | 无 |
| **黄 小丑 uit**（yellow clown suit） | `under/rank/civilian/clown/yellow` | 无 |
| **紫 小丑 uit**（purple clown suit） | `under/rank/civilian/clown/purple` | 无 |
| **橙 小丑 uit**（orange clown suit） | `under/rank/civilian/clown/orange` | 无 |
| **彩虹 小丑 uit**（rainbow clown suit） | `under/rank/civilian/clown/rainbow` | 无 |
| **小丑 uit**（jester suit） | `under/rank/civilian/clown/jester` | 无 |
| **小丑 uit**（jester suit） | `under/rank/civilian/clown/jesteralt` | 无 |
| **exy-clown uit**（sexy-clown suit） | `under/rank/civilian/clown/sexy` | 无 |
| **ensible 服**（sensible suit） | `under/rank/civilian/curator` | 无 |
| **ensible 短裙**（sensible suitskirt） | `under/rank/civilian/curator/skirt` | 无 |
| **宝藏 猎手 制服**（treasure hunter uniform） | `under/rank/civilian/curator/treasure_hunter` | 无 |
| **NASA 连体服**（NASA jumpsuit） | `under/rank/civilian/curator/nasa` | 无 |
| **船长 连体服**（captain's jumpsuit） | `under/rank/captain` | /datum/armor/clothing_under/rank_captain |
| **船长 连体短裙**（captain's jumpskirt） | `under/rank/captain/skirt` | /datum/armor/clothing_under/rank_captain |
| **正式制服 船长**（captain's formal uniform） | `under/rank/captain/parade` | /datum/armor/clothing_under/rank_captain |
| **船长 royal 制服**（captain's royal uniform） | `under/rank/captain/royal` | /datum/armor/clothing_under/rank_captain |
| **总工程师 连体服**（chief engineer's jumpsuit） | `under/rank/engineering/chief_engineer` | /datum/armor/clothing_under/engineering_chief_engineer |
| **总工程师 连体短裙**（chief engineer's jumpskirt） | `under/rank/engineering/chief_engineer/skirt` | /datum/armor/clothing_under/engineering_chief_engineer |
| **总工程师 turtleneck**（chief engineer's turtleneck） | `under/rank/engineering/chief_engineer/turtleneck` | /datum/armor/clothing_under/engineering_chief_engineer |
| **总工程师 turtleneck kirt**（chief engineer's turtleneck skirt） | `under/rank/engineering/chief_engineer/turtleneck/skirt` | /datum/armor/clothing_under/engineering_chief_engineer |
| **大气 技师 连体服**（atmospheric technician's jumpsuit） | `under/rank/engineering/atmospheric_technician` | 无 |
| **大气 技师 连体短裙**（atmospheric technician's jumpskirt） | `under/rank/engineering/atmospheric_technician/skirt` | 无 |
| **工程师 连体服**（engineer's jumpsuit） | `under/rank/engineering/engineer` | 无 |
| **工程师 警示 连体服**（engineer's hazard jumpsuit） | `under/rank/engineering/engineer/hazard` | 无 |
| **工程师 连体短裙**（engineer's jumpskirt） | `under/rank/engineering/engineer/skirt` | 无 |
| **医疗 医生 连体服**（medical doctor's jumpsuit） | `under/rank/medical/doctor` | 无 |
| **医疗 医生 连体短裙**（medical doctor's jumpskirt） | `under/rank/medical/doctor/skirt` | 无 |
| **首席医疗官 连体服**（chief medical officer's jumpsuit） | `under/rank/medical/chief_medical_officer` | 无 |
| **首席医疗官 连体短裙**（chief medical officer's jumpskirt） | `under/rank/medical/chief_medical_officer/skirt` | 无 |
| **首席医疗官 crub**（chief medical officer's scrubs） | `under/rank/medical/chief_medical_officer/scrubs` | 无 |
| **首席医疗官 turtleneck**（chief medical officer's turtleneck） | `under/rank/medical/chief_medical_officer/turtleneck` | 无 |
| **首席医疗官 turtleneck kirt**（chief medical officer's turtleneck skirt） | `under/rank/medical/chief_medical_officer/turtleneck/skirt` | 无 |
| **病毒学家 连体服**（virologist's jumpsuit） | `under/rank/medical/virologist` | 无 |
| **病毒学家 连体短裙**（virologist's jumpskirt） | `under/rank/medical/virologist/skirt` | 无 |
| **医疗 crub**（medical scrubs） | `under/rank/medical/scrubs` | 无 |
| **法医 连体服**（coroner jumpsuit） | `under/rank/medical/coroner` | 无 |
| **法医 连体短裙**（coroner jumpskirt） | `under/rank/medical/coroner/skirt` | 无 |
| **法医 crub**（coroner scrubs） | `under/rank/medical/scrubs/coroner` | 无 |
| **化学家 连体服**（chemist's jumpsuit） | `under/rank/medical/chemist` | /datum/armor/clothing_under/medical_chemist |
| **化学家 连体短裙**（chemist's jumpskirt） | `under/rank/medical/chemist/skirt` | /datum/armor/clothing_under/medical_chemist |
| **急救员 连体服**（paramedic jumpsuit） | `under/rank/medical/paramedic` | 无 |
| **急救员 连体短裙**（paramedic jumpskirt） | `under/rank/medical/paramedic/skirt` | 无 |
| **nurse uit**（nurse's suit） | `under/rank/medical/doctor/nurse` | 无 |
| **等离子环境服 中央指挥部 指挥官**（CentCom commander plasma envirosuit） | `under/plasmaman/centcom_commander` | /datum/armor/clothing_under/plasmaman |
| **等离子环境服 中央指挥部 官员**（CentCom official plasma envirosuit） | `under/plasmaman/centcom_official` | /datum/armor/clothing_under/plasmaman |
| **等离子环境服 中央指挥部 实习生**（CentCom intern plasma envirosuit） | `under/plasmaman/centcom_intern` | /datum/armor/clothing_under/plasmaman |
| **等离子环境服**（plasma envirosuit） | `under/plasmaman` | /datum/armor/clothing_under/plasmaman |
| **等离子环境服 货运**（cargo plasma envirosuit） | `under/plasmaman/cargo` | /datum/armor/clothing_under/plasmaman |
| **等离子环境服 采矿**（mining plasma envirosuit） | `under/plasmaman/mining` | /datum/armor/clothing_under/plasmaman |
| **等离子环境服 chef**（chef's plasma envirosuit） | `under/plasmaman/chef` | /datum/armor/clothing_under/plasmaman |
| **环境休闲裤**（enviroslacks） | `under/plasmaman/enviroslacks` | /datum/armor/clothing_under/plasmaman |
| **等离子环境服 chaplain**（chaplain's plasma envirosuit） | `under/plasmaman/chaplain` | /datum/armor/clothing_under/plasmaman |
| **等离子环境服 curator**（curator's plasma envirosuit） | `under/plasmaman/curator` | /datum/armor/clothing_under/plasmaman |
| **等离子环境服 janitor**（janitor's plasma envirosuit） | `under/plasmaman/janitor` | /datum/armor/clothing_under/plasmaman |
| **环境服 botany**（botany envirosuit） | `under/plasmaman/botany` | /datum/armor/clothing_under/plasmaman |
| **环境服 哑剧**（mime envirosuit） | `under/plasmaman/mime` | /datum/armor/clothing_under/plasmaman |
| **环境服 小丑**（clown envirosuit） | `under/plasmaman/clown` | /datum/armor/clothing_under/plasmaman |
| **环境服 bitrunner**（bitrunner envirosuit） | `under/plasmaman/bitrunner` | /datum/armor/clothing_under/plasmaman |
| **环境服 prisoner**（prisoner envirosuit） | `under/plasmaman/prisoner` | /datum/armor/clothing_under/plasmaman |
| **等离子环境服 船长**（captain's plasma envirosuit） | `under/plasmaman/captain` | /datum/armor/clothing_under/rank_captain/plasmaman |
| **人事主管 等离子 envirosuit**（head of personnel's plasma envirosuit） | `under/plasmaman/head_of_personnel` | /datum/armor/clothing_under/plasmaman |
| **安保主管 envirosuit**（head of security's envirosuit） | `under/plasmaman/security/head_of_security` | /datum/armor/clothing_under/security_head_of_security/plasmaman |
| **等离子环境服 总 工程师**（chief engineer's plasma envirosuit） | `under/plasmaman/chief_engineer` | /datum/armor/clothing_under/engineering_chief_engineer/plasmaman |
| **首席医疗官 等离子 envirosuit**（chief medical officer's plasma envirosuit） | `under/plasmaman/chief_medical_officer` | /datum/armor/clothing_under/plasmaman |
| **研究主任 等离子 envirosuit**（research director's plasma envirosuit） | `under/plasmaman/research_director` | /datum/armor/clothing_under/rnd_research_director/plasmaman |
| **等离子环境服 工程**（engineering plasma envirosuit） | `under/plasmaman/engineering` | /datum/armor/clothing_under/plasmaman_engineering |
| **等离子环境服 大气**（atmospherics plasma envirosuit） | `under/plasmaman/atmospherics` | /datum/armor/clothing_under/plasmaman_atmospherics |
| **等离子环境服 医疗**（medical plasma envirosuit） | `under/plasmaman/medical` | /datum/armor/clothing_under/plasmaman |
| **等离子环境服 coroner**（coroner plasma envirosuit） | `under/plasmaman/coroner` | /datum/armor/clothing_under/plasmaman |
| **环境服 paramedic**（paramedic envirosuit） | `under/plasmaman/paramedic` | /datum/armor/clothing_under/plasmaman |
| **等离子环境服 cience**（science plasma envirosuit） | `under/plasmaman/science` | /datum/armor/clothing_under/plasmaman |
| **等离子环境服 robotic**（robotics plasma envirosuit） | `under/plasmaman/robotics` | /datum/armor/clothing_under/plasmaman |
| **等离子环境服 病毒学**（virology plasma envirosuit） | `under/plasmaman/viro` | /datum/armor/clothing_under/plasmaman |
| **等离子环境服 genetic**（genetics plasma envirosuit） | `under/plasmaman/genetics` | /datum/armor/clothing_under/plasmaman |
| **等离子环境服 chemistry**（chemistry plasma envirosuit） | `under/plasmaman/chemist` | /datum/armor/clothing_under/plasmaman |
| **等离子环境服 ecurity**（security plasma envirosuit） | `under/plasmaman/security` | /datum/armor/clothing_under/rank_security/plasmaman |
| **等离子环境服 detective**（detective plasma envirosuit） | `under/plasmaman/security/detective` | /datum/armor/clothing_under/rank_security/plasmaman |
| **等离子环境服 典狱长**（warden plasma envirosuit） | `under/plasmaman/security/warden` | /datum/armor/clothing_under/rank_security/plasmaman |
| **研究主任 背心 uit**（research director's vest suit） | `under/rank/rnd/research_director` | /datum/armor/clothing_under/rnd_research_director |
| **研究主任 背心 uitskirt**（research director's vest suitskirt） | `under/rank/rnd/research_director/skirt` | /datum/armor/clothing_under/rnd_research_director |
| **研究主任 tan uit**（research director's tan suit） | `under/rank/rnd/research_director/alt` | /datum/armor/clothing_under/rnd_research_director |
| **研究主任 tan uitskirt**（research director's tan suitskirt） | `under/rank/rnd/research_director/alt/skirt` | /datum/armor/clothing_under/rnd_research_director |
| **研究主任 turtleneck**（research director's turtleneck） | `under/rank/rnd/research_director/turtleneck` | /datum/armor/clothing_under/rnd_research_director |
| **研究主任 turtleneck kirt**（research director's turtleneck skirt） | `under/rank/rnd/research_director/turtleneck/skirt` | /datum/armor/clothing_under/rnd_research_director |
| **cientist 连体服**（scientist's jumpsuit） | `under/rank/rnd/scientist` | /datum/armor/clothing_under/science |
| **cientist 连体短裙**（scientist's jumpskirt） | `under/rank/rnd/scientist/skirt` | /datum/armor/clothing_under/science |
| **机器人学家 连体服**（roboticist's jumpsuit） | `under/rank/rnd/roboticist` | 无 |
| **机器人学家 连体短裙**（roboticist's jumpskirt） | `under/rank/rnd/roboticist/skirt` | 无 |
| **geneticist 连体服**（geneticist's jumpsuit） | `under/rank/rnd/geneticist` | 无 |
| **geneticist 连体短裙**（geneticist's jumpskirt） | `under/rank/rnd/geneticist/skirt` | 无 |
| **ecurity 制服**（security uniform） | `under/rank/security/officer` | 无 |
| **灰 ecurity 连体服**（grey security jumpsuit） | `under/rank/security/officer/grey` | 无 |
| **ecurity 裙**（security skirt） | `under/rank/security/officer/skirt` | 无 |
| **蓝 hirt and 领带**（blue shirt and tie） | `under/rank/security/officer/blueshirt` | 无 |
| **正式制服 ecurity 军官**（security officer's formal uniform） | `under/rank/security/officer/formal` | 无 |
| **constable 套装**（constable outfit） | `under/rank/security/constable` | 无 |
| **ecurity 服**（security suit） | `under/rank/security/warden` | 无 |
| **灰 ecurity uit**（grey security suit） | `under/rank/security/warden/grey` | 无 |
| **典狱长 uitskirt**（warden's suitskirt） | `under/rank/security/warden/skirt` | 无 |
| **正式制服 典狱长**（warden's formal uniform） | `under/rank/security/warden/formal` | 无 |
| **hard-worn uit**（hard-worn suit） | `under/rank/security/detective` | 无 |
| **detective uitskirt**（detective's suitskirt） | `under/rank/security/detective/skirt` | 无 |
| **黑白 uit**（noir suit） | `under/rank/security/detective/noir` | 无 |
| **黑白 短裙**（noir suitskirt） | `under/rank/security/detective/noir/skirt` | 无 |
| **安保主管 制服**（head of security's uniform） | `under/rank/security/head_of_security` | /datum/armor/clothing_under/security_head_of_security |
| **安保主管 kirt**（head of security's skirt） | `under/rank/security/head_of_security/skirt` | /datum/armor/clothing_under/security_head_of_security |
| **安保主管 灰 连体服**（head of security's grey jumpsuit） | `under/rank/security/head_of_security/grey` | /datum/armor/clothing_under/security_head_of_security |
| **安保主管 turtleneck**（head of security's turtleneck） | `under/rank/security/head_of_security/alt` | /datum/armor/clothing_under/security_head_of_security |
| **安保主管 turtleneck kirt**（head of security's turtleneck skirt） | `under/rank/security/head_of_security/alt/skirt` | /datum/armor/clothing_under/security_head_of_security |
| **安保主管 阅兵 制服**（head of security's parade uniform） | `under/rank/security/head_of_security/parade` | /datum/armor/clothing_under/security_head_of_security |
| **安保主管 正式 制服**（head of security's formal uniform） | `under/rank/security/head_of_security/parade/female` | /datum/armor/clothing_under/security_head_of_security |
| **安保主管 正式 制服**（head of security's formal uniform） | `under/rank/security/head_of_security/formal` | /datum/armor/clothing_under/security_head_of_security |
| **警 制服**（police uniform） | `under/rank/security/officer/spacepol` | /datum/armor/clothing_under/sec_uniform_spacepol |
| **prison 连体服**（prison jumpsuit） | `under/rank/prisoner` | 无 |
| **prison 连体短裙**（prison jumpskirt） | `under/rank/prisoner/skirt` | 无 |
| **pace 警 制服**（space police uniform） | `under/rank/security/officer/beatcop` | 无 |
| **uperstar cop 制服**（superstar cop uniform） | `under/rank/security/detective/disco` | 无 |
| **aerostatic 服**（aerostatic suit） | `under/rank/security/detective/kim` | 无 |
| **ai 制服**（ai's uniform） | `under/rank/station_trait/human_ai` | 无 |
| **睡衣**（PJs） | `under/misc/pj` | 无 |
| **patriotic 服**（patriotic suit） | `under/misc/patriotsuit` | 无 |
| **mailman 连体服**（mailman's jumpsuit） | `under/misc/mailman` | 无 |
| **psychedelic 连体服**（psychedelic jumpsuit） | `under/misc/psyche` | 无 |
| **军官 vice 连体服**（vice officer's jumpsuit） | `under/misc/vice_officer` | 无 |
| **administrative cybernetic 连体服**（administrative cybernetic jumpsuit） | `under/misc/adminsuit` | /datum/armor/clothing_under/adminsuit |
| **葬礼服**（burial garments） | `under/misc/burial` | 无 |
| **工装裤 laborer**（laborer's overalls） | `under/misc/overalls` | 无 |
| **正式制服 助手**（assistant's formal uniform） | `under/misc/assistantformal` | 无 |
| **杜拉丝 连体服**（durathread jumpsuit） | `under/misc/durathread` | /datum/armor/clothing_under/durathread |
| **bouncer 制服**（bouncer uniform） | `under/misc/bouncer` | /datum/armor/clothing_under/bouncer |
| **coordinator 连体服**（coordinator jumpsuit） | `under/misc/coordinator` | 无 |
| **辛迪加 ouvenir tee**（syndicate souvenir tee） | `under/misc/syndicate_souvenir` | 无 |
| **休闲裤**（slacks） | `under/pants/slacks` | 无 |
| **牛仔裤**（jeans） | `under/pants/jeans` | 无 |
| **运动裤**（track pants） | `under/pants/track` | 无 |
| **迷彩 pant**（camo pants） | `under/pants/camo` | 无 |
| **短裤**（shorts） | `under/shorts` | 10 / 5 |
| **牛仔裤 hort**（jean shorts） | `under/shorts/jeanshorts` | 10 / 5 |
| **运动 短裤**（athletic shorts） | `under/shorts/red` | 10 / 5 |
| **运动 短裤**（athletic shorts） | `under/shorts/green` | 10 / 5 |
| **运动 短裤**（athletic shorts） | `under/shorts/blue` | 10 / 5 |
| **运动 短裤**（athletic shorts） | `under/shorts/black` | 10 / 5 |
| **运动 短裤**（athletic shorts） | `under/shorts/grey` | 10 / 5 |
| **运动 短裤**（athletic shorts） | `under/shorts/purple` | 10 / 5 |
| **条纹 裙**（striped dress） | `under/dress/striped` | 无 |
| **ailor 裙**（sailor dress） | `under/dress/sailor` | 无 |
| **婚礼 dre**（wedding dress） | `under/dress/wedding_dress` | 无 |
| **晚礼服**（evening gown） | `under/dress/eveninggown` | 无 |
| **开衫 kirt**（cardigan skirt） | `under/dress/skirt` | 无 |
| **plaid 裙**（plaid skirt） | `under/dress/skirt/plaid` | 无 |
| **高领衫 kirt**（turtleneck skirt） | `under/dress/skirt/turtleskirt` | 无 |
| **tango 裙**（tango dress） | `under/dress/tango` | 无 |
| **内层**（sundress） | `under/dress/sundress` | 无 |
| **charcoal 服**（charcoal suit） | `under/suit/charcoal` | 无 |
| **navy 服**（navy suit） | `under/suit/navy` | 无 |
| **burgundy 服**（burgundy suit） | `under/suit/burgundy` | 无 |
| **checkered 服**（checkered suit） | `under/suit/checkered` | 无 |
| **beige 服**（beige suit） | `under/suit/beige` | 无 |
| **黑 two piece uit**（black two piece suit） | `under/suit/black` | 无 |
| **黑 two piece uit**（black two piece suit） | `under/suit/black/skirt` | 无 |
| **白 uit**（white suit） | `under/suit/white` | 无 |
| **白 uitskirt**（white suitskirt） | `under/suit/white/skirt` | 无 |
| **tan 服**（tan suit） | `under/suit/tan` | 无 |
| **waiter outfit**（waiter's outfit） | `under/suit/waiter` | 无 |
| **executive 服**（executive suit） | `under/suit/black_really` | 无 |
| **executive 短裙**（executive suitskirt） | `under/suit/black_really/skirt` | 无 |
| **燕尾服**（tuxedo） | `under/suit/tuxedo` | 无 |
| **carpskin 服**（carpskin suit） | `under/suit/carpskin` | 无 |
| **高领衫 战术**（tactical turtleneck） | `under/syndicate` | /datum/armor/clothing_under/syndicate |
| **战术 kirtleneck**（tactical skirtleneck） | `under/syndicate/skirt` | /datum/armor/clothing_under/syndicate |
| **blood-red neaksuit**（blood-red sneaksuit） | `under/syndicate/bloodred` | /datum/armor/clothing_under/syndicate_bloodred |
| **blood-red pajama**（blood-red pajamas） | `under/syndicate/bloodred/sleepytime` | /datum/armor/clothing_under/bloodred_sleepytime |
| **高领衫 tacticool**（tacticool turtleneck） | `under/syndicate/tacticool` | /datum/armor/clothing_under/syndicate_tacticool |
| **战术高领衫**（tacticool skirtleneck） | `under/syndicate/tacticool/skirt` | /datum/armor/clothing_under/syndicate_tacticool |
| **高领衫 战术**（tactical turtleneck suit） | `under/syndicate/sniper` | /datum/armor/clothing_under/syndicate |
| **迷彩 作训服**（camouflage fatigues） | `under/syndicate/camo` | /datum/armor/clothing_under/syndicate |
| **商务服 Cybersun**（Cybersun businesswear） | `under/syndicate/cybersun` | /datum/armor/clothing_under/syndicate |
| **地砖 迷彩 作训服**（floortile camouflage fatigues） | `under/syndicate/floortilecamo` | /datum/armor/clothing_under/syndicate |
| **Ratnik 5 tracksuit**（Ratnik 5 tracksuit） | `under/syndicate/soviet` | /datum/armor/clothing_under/syndicate_soviet |
| **战斗 制服**（combat uniform） | `under/syndicate/combat` | /datum/armor/clothing_under/syndicate |
| **高级 军事 tracksuit**（advanced military tracksuit） | `under/syndicate/rus_army` | /datum/armor/clothing_under/syndicate_rus_army |
| **战术 crub**（tactical scrubs） | `under/syndicate/scrubs` | /datum/armor/clothing_under/syndicate_scrubs |
| **环境服 tacticool**（tacticool envirosuit） | `under/plasmaman/syndicate` | /datum/armor/clothing_under/plasmaman |
| **command 制服**（command uniform） | `under/trek/command` | 无 |
| **engsec 制服**（engsec uniform） | `under/trek/engsec` | 无 |
| **medsci 制服**（medsci uniform） | `under/trek/medsci` | 无 |
| **french marshall 制服**（french marshall's uniform） | `under/trek/q` | 无 |

# 第四卷 · 头饰与面具

**代码**: `clothing/head/`（4,459 行）+ `clothing/masks/`（1,996 行）

## 4.1 S 档 · 传说头盔

| 头盔 | 近战 | 子弹 | 激光 | 特性 |
|---|---|---|---|---|
| **雷神穹顶头盔 Thunderdome** | **80** | **80** | 50 | bomb/bio 100/fire 90——最强 |
| **战争领主金头盔 Warlord** | **70** | 60 | **70** | 金 barbute；wound 30 |

## 4.2 A 档 · 高级头盔

| 头盔 | 近战 | 子弹 | 激光 | 特性 |
|---|---|---|---|---|
| **战术作战头盔 Marine** | **50** | **50** | 30 | bio 100/bomb 50；tint 2 |
| **防弹头盔 Bulletproof** | 15 | **60** | 10 | 子弹特化 |
| **SWAT 头盔** | 40 | 30 | 30 | bio 90/fire 100 |
| **防暴头盔 Riot** | **50** | 10 | 10 | 近战特化；fire 80 |
| **感知矩阵头盔** | 15 | 15 | **45** | 能量 60 特化 |

## 4.3 B 档 · 标准头盔

| 头盔 | 近战 | 子弹 | 特性 |
|---|---|---|---|
| **标准头盔 Helmet** | 35 | 30 | 基础军用 |
| **粗制头盔 Crude** | 45 | 25 | 手工 |
| **俄式头盔** | 25 | 30 | 俄式 |
| **骑士头盔** | 50 | 10 | 近战；中世纪 |
| **HoS 帽** | 40 | 30 | 安保指挥 |
| **看守帽 Warden** | 40 | 30 | 安保 |

## 4.4 C 档 · 民用/功能

| 头盔 | 特性 |
|---|---|
| **安全帽 Hard Hat** | fire 100 防坠物 |
| **焊接头盔** | fire 100/防闪 |
| **船长帽/贝雷帽** | 25 近战 |
| **侦探软呢帽** | 25 近战 |
| **锡箔帽** | 防读心（彩蛋） |
| **皇冠/青铜帽** | 装饰 |
| **雕刻南瓜/驯鹿帽** | 节日 |
| **杜拉丝系列** | 纺织自制 |
| **西瓜/圣西瓜/桶瓜头盔** | 彩蛋 |

## 4.5 面具概要（30+ 种）

**代码**: `clothing/masks/`（1,996 行）——子代理数据待整合，核心类别：

| 类别 | 面具 |
|---|---|
| 呼吸面罩 | 氧气面罩/呼吸面罩 |
| **防毒面具** | 气体过滤（消防/安保） |
| 口罩 | 外科口罩/医用 |
| 审讯面罩 | 审讯用 |
| 特色 | 小丑面具/狼面具/骷髅面具等 |


---

---

# 第四卷B · 头饰全量总表（241 种）

| 名称 | 路径 | 护甲（近战/子弹/激光/能量/爆炸/生物/火/酸/伤口） |
|---|---|---|
| **小猫 耳**（kitty ears） | `head/costume/kitty` | 无 |
| **兔 ear**（rabbit ears） | `head/costume/rabbitears` | 无 |
| **无檐便帽**（beanie） | `head/beanie` | 无 |
| **无檐便帽 黑**（black beanie） | `head/beanie/black` | 无 |
| **无檐便帽 红**（red beanie） | `head/beanie/red` | 无 |
| **无檐便帽 深 蓝**（dark blue beanie） | `head/beanie/darkblue` | 无 |
| **无檐便帽 黄**（yellow beanie） | `head/beanie/yellow` | 无 |
| **无檐便帽 橙**（orange beanie） | `head/beanie/orange` | 无 |
| **无檐便帽 christma**（christmas beanie） | `head/beanie/christmas` | 无 |
| **杜拉丝 beanie**（durathread beanie） | `head/beanie/durathread` | 15 / 5 / 15 / 25 / 10 / 30 / 5 |
| **拉斯塔帽**（rastacap） | `head/rasta` | 无 |
| **红 triped bobble 帽**（red striped bobble hat） | `head/waldo` | 无 |
| **无檐便帽 test ubject**（test subject beanie） | `head/beanie/black/dboy` | 无 |
| **蛋糕帽**（cakehat） | `head/utility/hardhat/cakehat` | 无 |
| **能量蛋糕**（energy cake） | `head/utility/hardhat/cakehat/energycake` | 无 |
| **collectable 帽**（collectable hat） | `head/collectable` | 无 |
| **ultra 稀有 Pete hat!**（ultra rare Pete's hat!） | `head/collectable/petehat` | 无 |
| **collectable xenomorph helmet!**（collectable xenomorph helmet!） | `head/collectable/xenom` | 无 |
| **collectable chef 帽**（collectable chef's hat） | `head/collectable/chef` | 无 |
| **collectable paper 帽**（collectable paper hat） | `head/collectable/paper` | 无 |
| **高顶礼帽 collectable**（collectable top hat） | `head/collectable/tophat` | 无 |
| **船长帽 collectable**（collectable captain's hat） | `head/collectable/captain` | 无 |
| **军官 collectable 警 帽**（collectable police officer's hat） | `head/collectable/police` | 无 |
| **贝雷帽 collectable**（collectable beret） | `head/collectable/beret` | 无 |
| **焊接头盔 collectable**（collectable welding helmet） | `head/collectable/welding` | 无 |
| **collectable lime 帽**（collectable slime hat） | `head/collectable/slime` | 无 |
| **collectable flat 帽**（collectable flat cap） | `head/collectable/flatcap` | 无 |
| **collectable 海盗 帽**（collectable pirate hat） | `head/collectable/pirate` | 无 |
| **collectable 小猫 耳**（collectable kitty ears） | `head/collectable/kitty` | 无 |
| **collectable 兔 ear**（collectable rabbit ears） | `head/collectable/rabbitears` | 无 |
| **collectable 巫师 帽**（collectable wizard's hat） | `head/collectable/wizard` | 无 |
| **安全帽 collectable**（collectable hard hat） | `head/collectable/hardhat` | 无 |
| **collectable HoS 帽**（collectable HoS hat） | `head/collectable/hos` | 无 |
| **collectable HoP 帽**（collectable HoP hat） | `head/collectable/hop` | 无 |
| **雷神穹顶 collectable 头盔**（collectable Thunderdome helmet） | `head/collectable/thunderdome` | 无 |
| **特警 collectable 头盔**（collectable SWAT helmet） | `head/collectable/swat` | 无 |
| **警示锥**（warning cone） | `head/cone` | 无 |
| **powdered 假发**（powdered wig） | `head/costume/powdered_wig` | 无 |
| **hastur 兜帽**（hastur's hood） | `head/costume/hasturhood` | 无 |
| **仿品 黑 pace-helmet**（black space-helmet replica） | `head/syndicatefake` | 无 |
| **cueball 头盔**（cueball helmet） | `head/costume/cueball` | 无 |
| **nowman 头**（snowman head） | `head/costume/snowman` | 无 |
| **witch 戏服 假发**（witch costume wig） | `head/costume/witchwig` | 无 |
| **女仆头带**（maid headband） | `head/costume/maid_headband` | 无 |
| **鸡 uit head**（chicken suit head） | `head/costume/chicken` | 无 |
| **griffon 头**（griffon head） | `head/costume/griffin` | 无 |
| **异形 头盔**（xenos helmet） | `head/costume/xenos` | 无 |
| **foam lobster 头**（foam lobster head） | `head/costume/lobsterhat` | 无 |
| **医生 freeze wig**（doctor freeze's wig） | `head/costume/drfreezehat` | 无 |
| **hrine maiden wig**（shrine maiden's wig） | `head/costume/shrine_wig` | 无 |
| **cardborg 头盔**（cardborg helmet） | `head/costume/cardborg` | 无 |
| **青铜 帽**（bronze hat） | `head/costume/bronze` | 5 / 10 / 20 / 20 |
| **华丽 帽**（fancy hat） | `head/costume/fancy` | 无 |
| **橄榄球 头盔**（football helmet） | `head/costume/football_helmet` | 无 |
| **television 头盔**（television helmet） | `head/costume/tv_head` | 无 |
| **internal revenue ervice 帽**（internal revenue service cap） | `head/costume/irs` | 无 |
| **Lost M.C. bandana**（Lost M.C. bandana） | `head/costume/tmc` | 无 |
| **甲板工 headphone**（Decker headphones） | `head/costume/deckers` | 无 |
| **yuri initiate 头盔**（yuri initiate helmet） | `head/costume/yuri` | 无 |
| **allie 头盔**（allies helmet） | `head/costume/allies` | 无 |
| **华丽 hairpin**（fancy hairpin） | `head/costume/hairpin` | 无 |
| **trange 头巾**（strange bandana） | `head/costume/snakeeater` | 无 |
| **fake medieval 头盔**（fake medieval helmet） | `head/costume/knight` | 无 |
| **王冠**（crown） | `head/costume/crown` | 15 / 10 / 100 / 50 / 5 |
| **magnificent 王冠**（magnificent crown） | `head/costume/crown/fancy` | 15 / 10 / 100 / 50 / 5 |
| **软呢帽**（fedora） | `head/fedora` | 无 |
| **软呢帽 白**（white fedora） | `head/fedora/white` | 无 |
| **软呢帽 beige**（beige fedora） | `head/fedora/beige` | 无 |
| **软呢帽 carpskin**（carpskin fedora） | `head/fedora/carpskin` | 无 |
| **软呢帽 pre**（press fedora） | `head/fedora/beige/press` | 无 |
| **贝雷帽 french**（french beret） | `head/beret/frenchberet` | 无 |
| **花环**（floral garland） | `head/costume/garland` | 无 |
| **彩虹 flower 王冠**（rainbow flower crown） | `head/costume/garland/rainbowbunch` | 无 |
| **unflower 王冠**（sunflower crown） | `head/costume/garland/sunflower` | 无 |
| **poppy 王冠**（poppy crown） | `head/costume/garland/poppy` | 无 |
| **lily 王冠**（lily crown） | `head/costume/garland/lily` | 无 |
| **安全帽**（hard hat） | `head/utility/hardhat` | 15 / 5 / 20 / 10 / 20 / 50 / 100 / 50 / 10 |
| **消防员 头盔**（firefighter helmet） | `head/utility/hardhat/red` | 15 / 5 / 20 / 10 / 20 / 50 / 100 / 50 / 10 |
| **workplace-ready 消防员 头盔**（workplace-ready firefighter helmet） | `head/utility/hardhat/red/upgraded` | 15 / 5 / 20 / 10 / 20 / 50 / 100 / 50 / 10 |
| **安全帽 焊接**（welding hard hat） | `head/utility/hardhat/welding` | 15 / 5 / 20 / 10 / 20 / 50 / 100 / 50 / 10 |
| **大气 消防员 头盔**（atmospheric firefighter helmet） | `head/utility/hardhat/welding/atmos` | 15 / 5 / 20 / 10 / 20 / 50 / 100 / 50 / 10 |
| **雕刻 pumpkin**（carved pumpkin） | `head/utility/hardhat/pumpkinhead` | 无 |
| **雕刻 blumpkin**（carved blumpkin） | `head/utility/hardhat/pumpkinhead/blumpkin` | 无 |
| **novelty reindeer 帽**（novelty reindeer hat） | `head/utility/hardhat/reindeer` | 无 |
| **中央指挥部 帽**（CentCom hat） | `head/hats/centhat` | 30 / 15 / 30 / 40 / 25 / 50 / 50 |
| **constable 头盔**（constable helmet） | `head/costume/constable` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **pace 警 帽**（space police cap） | `head/costume/spacepolice` | 无 |
| **triped 红 tophat**（striped red tophat） | `head/costume/canada` | 无 |
| **redcoat 帽**（redcoat's hat） | `head/costume/redcoat` | 无 |
| **mailman 帽**（mailman's hat） | `head/costume/mailman` | 无 |
| **plague 医生 帽**（plague doctor's hat） | `head/bio_hood/plague` | 100 |
| **nurse 帽**（nurse's hat） | `head/costume/nursehat` | 无 |
| **bowler-hat**（bowler-hat） | `head/hats/bowler` | 无 |
| **熊 pelt 帽**（bear pelt hat） | `head/costume/bearpelt` | 无 |
| **flat 帽**（flat cap） | `head/flatcap` | 无 |
| **牛仔帽**（cowboy hat） | `head/cowboy` | 5 / 5 / 5 / 15 |
| **赏金 猎 帽**（bounty hunting hat） | `head/cowboy/bounty` | 5 / 5 / 5 / 15 |
| **desperado 帽**（desperado hat） | `head/cowboy/black` | 5 / 5 / 5 / 15 |
| **ten-gallon 帽**（ten-gallon hat） | `head/cowboy/white` | 5 / 5 / 5 / 15 |
| **drifter 帽**（drifter hat） | `head/cowboy/grey` | 5 / 5 / 5 / 15 |
| **deputy 帽**（deputy hat） | `head/cowboy/red` | 5 / 5 / 5 / 15 |
| **heriff 帽**（sheriff hat） | `head/cowboy/brown` | 5 / 5 / 5 / 15 |
| **anta 帽**（santa hat） | `head/costume/santa` | 无 |
| **anta 帽**（santa hat） | `head/costume/santa/gags` | 无 |
| **jester 帽**（jester hat） | `head/costume/jester` | 无 |
| **jester 帽**（jester hat） | `head/costume/jesteralt` | 无 |
| **rice 帽**（rice hat） | `head/costume/rice_hat` | 无 |
| **lizardskin cloche 帽**（lizardskin cloche hat） | `head/costume/lizard` | 无 |
| **carecrow 帽**（scarecrow hat） | `head/costume/scarecrow_hat` | 无 |
| **pharaoh 帽**（pharaoh hat） | `head/costume/pharaoh` | 无 |
| **headdre 的 Neme**（headdress of Nemes） | `head/costume/nemes` | 无 |
| **delinquent 帽**（delinquent hat） | `head/costume/delinquent` | 无 |
| **中央指挥部 Head 实习生 beancap**（CentCom Head Intern beancap） | `head/hats/intern` | 无 |
| **coordinator 帽**（coordinator cap） | `head/hats/coordinator` | 25 / 15 / 25 / 35 / 25 / 50 / 50 |
| **frosty 帽**（frosty hat） | `head/costume/jackbros` | 无 |
| **婚礼 面纱**（wedding veil） | `head/costume/weddingveil` | 无 |
| **中央指挥部 指挥官 帽**（CentCom commander cap） | `head/hats/centcom_cap` | 30 / 15 / 30 / 40 / 25 / 50 / 50 |
| **human kin 帽**（human skin hat） | `head/fedora/human_leather` | 无 |
| **乌尚卡帽**（ushanka） | `head/costume/ushanka` | 无 |
| **乌尚卡帽 熊 猎手**（bear hunter's ushanka） | `head/costume/ushanka/polar` | 无 |
| **乌尚卡帽 ecurity**（security ushanka） | `head/costume/ushanka/sec` | 30 / 25 / 25 / 35 / 25 / 20 / 50 / 5 |
| **蓝 nightcap**（blue nightcap） | `head/costume/nightcap/blue` | 无 |
| **红 nightcap**（red nightcap） | `head/costume/nightcap/red` | 无 |
| **paper 帽**（paper hat） | `head/costume/paper_hat` | 无 |
| **ancient paper 帽**（ancient paper hat） | `head/costume/paper_hat/savior` | 无 |
| **头盔**（helmet） | `head/helmet` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **pre 头盔**（press helmet） | `head/helmet/press` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **防弹头盔**（bulletproof helmet） | `head/helmet/alt` | 15 / 60 / 10 / 10 / 40 / 50 / 50 / 5 |
| **战术作战头盔**（tactical combat helmet） | `head/helmet/marine` | 50 / 50 / 30 / 25 / 50 / 100 / 40 / 50 / 20 |
| **陆战队员 重 头盔**（marine heavy helmet） | `head/helmet/marine/security` | 50 / 50 / 30 / 25 / 50 / 100 / 40 / 50 / 20 |
| **陆战队员 通用 头盔**（marine utility helmet） | `head/helmet/marine/engineer` | 50 / 50 / 30 / 25 / 50 / 100 / 40 / 50 / 20 |
| **陆战队员 medic 头盔**（marine medic helmet） | `head/helmet/marine/medic` | 50 / 50 / 30 / 25 / 50 / 100 / 40 / 50 / 20 |
| **degrading 头盔**（degrading helmet） | `head/helmet/old` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **蓝 头盔**（blue helmet） | `head/helmet/blueshirt` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **防暴头盔**（riot helmet） | `head/helmet/toggleable/riot` | 50 / 10 / 10 / 10 / 80 / 80 / 15 |
| **balloon 头盔**（balloon helmet） | `head/helmet/balloon` | 10 / 60 / 50 |
| **头盔 of justice**（helmet of justice） | `head/helmet/toggleable/justice` | 无 |
| **alarm 头盔**（alarm helmet） | `head/helmet/toggleable/justice/escape` | 无 |
| **特警 头盔**（SWAT helmet） | `head/helmet/swat` | 40 / 30 / 30 / 40 / 50 / 90 / 100 / 100 / 15 |
| **特警 头盔**（SWAT helmet） | `head/helmet/swat/nanotrasen` | 40 / 30 / 30 / 40 / 50 / 90 / 100 / 100 / 15 |
| **雷神穹顶 头盔**（Thunderdome helmet） | `head/helmet/thunderdome` | 80 / 80 / 50 / 50 / 100 / 100 / 90 / 90 |
| **Roman 头盔**（Roman helmet） | `head/helmet/roman` | 25 / 25 / 10 / 10 / 100 / 50 / 5 |
| **Roman legionnaire 头盔**（Roman legionnaire helmet） | `head/helmet/roman/legionnaire` | 25 / 25 / 10 / 10 / 100 / 50 / 5 |
| **gladiator 头盔**（gladiator helmet） | `head/helmet/gladiator` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **激光标签头盔 红**（red laser tag helmet） | `head/helmet/taghelm/red` | 无 |
| **激光标签头盔 蓝**（blue laser tag helmet） | `head/helmet/taghelm/blue` | 无 |
| **medieval 头盔**（medieval helmet） | `head/helmet/knight` | 50 / 10 / 10 / 10 / 80 / 80 |
| **骑士 头盔**（knight helmet） | `head/helmet/knight/greyscale` | 35 / 10 / 10 / 10 / 10 / 10 / 40 / 40 |
| **杜拉丝 头盔**（durathread helmet） | `head/helmet/durathread` | 20 / 10 / 30 / 40 / 15 / 40 / 50 / 5 |
| **俄式 头盔**（russian helmet） | `head/helmet/rus_helmet` | 25 / 30 / 10 / 10 / 20 / 50 / 5 |
| **战斗乌尚卡**（battle ushanka） | `head/helmet/rus_ushanka` | 25 / 20 / 20 / 30 / 20 / 50 / 50 / 5 |
| **远古大气人 头盔**（Elder Atmosian Helmet） | `head/helmet/elder_atmosian` | 25 / 20 / 30 / 30 / 85 / 10 / 65 / 40 / 15 |
| **粗制 头盔**（Crude Helmet） | `head/helmet/military` | 45 / 25 / 25 / 25 / 25 / 10 / 50 / 20 |
| **金 barbute 头盔**（golden barbute helmet） | `head/helmet/knight/warlord` | 70 / 60 / 70 / 70 / 40 / 50 / 50 / 30 |
| **watermelon 头盔**（watermelon helmet） | `head/helmet/durability/watermelon` | 15 / 10 / 10 / 10 / 0 / 25 / 5 |
| **holymelon 头盔**（holymelon helmet） | `head/helmet/durability/holymelon` | 15 / 10 / 10 / 10 / 0 / 25 / 5 |
| **barrelmelon 头盔**（barrelmelon helmet） | `head/helmet/durability/barrelmelon` | 25 / 20 / 15 / 10 / 0 / 35 / 10 |
| **drachen 头盔**（drachen helmet） | `head/helmet/dragoon` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **chef 帽**（chef's hat） | `head/utility/chefhat` | 无 |
| **船长帽**（captain's hat） | `head/hats/caphat` | 25 / 15 / 25 / 35 / 25 / 50 / 50 / 5 |
| **船长 阅兵 帽**（captain's parade cap） | `head/hats/caphat/parade` | 25 / 15 / 25 / 35 / 25 / 50 / 50 / 5 |
| **船长 双角帽**（captain's bicorne） | `head/hats/caphat/bicorne` | 25 / 15 / 25 / 35 / 25 / 50 / 50 / 5 |
| **船长 beret**（captain's beret） | `head/caphat/beret` | 25 / 15 / 25 / 35 / 25 / 50 / 50 / 5 |
| **人事主管 帽**（head of personnel's cap） | `head/hats/hopcap` | 25 / 15 / 25 / 35 / 25 / 50 / 50 |
| **nun 兜帽**（nun hood） | `head/chaplain/nun_hood` | 无 |
| **nun 面纱**（nun veil） | `head/chaplain/habit_veil` | 无 |
| **主教冠**（bishop mitre） | `head/chaplain/bishopmitre` | 无 |
| **侦探软呢帽**（detective's fedora） | `head/fedora/det_hat` | 25 / 5 / 25 / 35 / 30 / 50 / 5 |
| **软呢帽 detective noir**（detective's noir fedora） | `head/fedora/det_hat/noir` | 25 / 5 / 25 / 35 / 30 / 50 / 5 |
| **软呢帽 检查员**（inspector's fedora） | `head/fedora/inspector_hat` | 25 / 5 / 25 / 35 / 30 / 50 / 5 |
| **贝雷帽**（beret） | `head/beret` | 无 |
| **安保主管 generic 帽**（generic head of security hat） | `head/hats/hos` | 40 / 30 / 25 / 35 / 25 / 10 / 50 / 60 / 10 |
| **安保主管 帽**（head of security cap） | `head/hats/hos/cap` | 40 / 30 / 25 / 35 / 25 / 10 / 50 / 60 / 10 |
| **辛迪加 帽**（syndicate cap） | `head/hats/hos/cap/syndicate` | 40 / 30 / 25 / 35 / 25 / 10 / 50 / 60 / 10 |
| **坚固高筒军帽**（sturdy shako） | `head/hats/hos/shako` | 40 / 30 / 25 / 35 / 25 / 10 / 50 / 60 / 10 |
| **安保主管 beret**（head of security's beret） | `head/hats/hos/beret` | 40 / 30 / 25 / 35 / 25 / 10 / 50 / 60 / 10 |
| **安保主管正式贝雷帽**（head of security's formal beret） | `head/hats/hos/beret/navyhos` | 40 / 30 / 25 / 35 / 25 / 10 / 50 / 60 / 10 |
| **辛迪加 beret**（syndicate beret） | `head/hats/hos/beret/syndicate` | 40 / 30 / 25 / 35 / 25 / 10 / 50 / 60 / 10 |
| **典狱长 警 帽**（warden's police hat） | `head/hats/warden` | 40 / 30 / 30 / 40 / 25 / 30 / 60 / 5 |
| **军官 警 帽**（police officer's hat） | `head/hats/warden/police` | 40 / 30 / 30 / 40 / 25 / 30 / 60 / 5 |
| **典狱长 帽**（warden's hat） | `head/hats/warden/red` | 40 / 30 / 30 / 40 / 25 / 30 / 60 / 5 |
| **典狱长 campaign 帽**（warden's campaign hat） | `head/hats/warden/drill` | 40 / 30 / 30 / 40 / 25 / 30 / 60 / 5 |
| **贝雷帽 ecurity**（security beret） | `head/beret/sec` | 30 / 25 / 25 / 35 / 25 / 20 / 50 / 5 |
| **典狱长 beret**（warden's beret） | `head/beret/sec/navywarden` | 30 / 25 / 25 / 35 / 25 / 20 / 50 / 5 |
| **贝雷帽 cience**（science beret） | `head/beret/science` | 无 |
| **贝雷帽 医疗**（medical beret） | `head/beret/medical` | 无 |
| **急救员 beret**（paramedic beret） | `head/beret/medical/paramedic` | 无 |
| **首席医疗官 beret**（chief medical officer beret） | `head/beret/medical/cmo` | 无 |
| **蓝 urgery 帽**（blue surgery cap） | `head/utility/surgerycap` | 无 |
| **burgundy urgery 帽**（burgundy surgery cap） | `head/utility/surgerycap/purple` | 无 |
| **绿 urgery 帽**（green surgery cap） | `head/utility/surgerycap/green` | 无 |
| **turquoise urgery 帽**（turquoise surgery cap） | `head/utility/surgerycap/cmo` | 无 |
| **黑 urgery 帽**（black surgery cap） | `head/utility/surgerycap/black` | 无 |
| **头 mirror**（head mirror） | `head/utility/head_mirror` | 无 |
| **贝雷帽 工程**（engineering beret） | `head/beret/engi` | 无 |
| **货运 beret**（cargo beret） | `head/beret/cargo` | 无 |
| **软呢帽 宝藏 猎手**（treasure hunter's fedora） | `head/fedora/curator` | 无 |
| **杜拉丝 beret**（durathread beret） | `head/beret/durathread` | 15 / 5 / 15 / 25 / 10 / 30 / 5 / 5 |
| **中央指挥部 正式 Beret**（CentCom Formal Beret） | `head/beret/centcom_formal` | 80 / 80 / 50 / 50 / 100 / 100 / 100 / 90 / 10 |
| **贝雷帽 Militia General**（Militia General's Beret） | `head/beret/militia` | 30 / 25 / 25 / 35 / 25 / 20 / 50 / 5 |
| **justice 帽**（justice hat） | `head/costume/justice` | 无 |
| **monkey mind magnification 头盔**（monkey mind magnification helmet） | `head/helmet/monkey_sentience` | 35 / 30 / 30 / 40 / 25 / 50 / 50 / 10 |
| **蛾族 oftcap**（mothic softcap） | `head/mothcap` | 无 |
| **paper ack 帽**（paper sack hat） | `head/costume/papersack` | 无 |
| **paper ack 帽**（paper sack hat） | `head/costume/papersack/smiley` | 无 |
| **感知矩阵头盔**（perceptomatrix helm） | `head/helmet/perceptomatrix` | 15 / 15 / 45 / 60 / 15 / 50 / 50 / 10 |
| **幻觉**（Hallucinate） | `head/helmet/perceptomatrix/functioning` | 15 / 15 / 45 / 60 / 15 / 50 / 50 / 10 |
| **海盗 帽**（pirate hat） | `head/costume/pirate` | 无 |
| **海盗 船长 帽**（pirate captain hat） | `head/costume/pirate/captain` | 无 |
| **海盗 bandana**（pirate bandana） | `head/costume/pirate/bandana` | 无 |
| **帽 of the Honkmother**（Hat of the Honkmother） | `head/chaplain/clownmitre` | 无 |
| **犹太小帽**（kippah） | `head/chaplain/kippah` | 无 |
| **medieval Jewish 帽**（medieval Jewish hat） | `head/chaplain/medievaljewhat` | 无 |
| **白 taqiyah**（white taqiyah） | `head/chaplain/taqiyah/white` | 无 |
| **红 taqiyah**（red taqiyah） | `head/chaplain/taqiyah/red` | 无 |
| **货运 帽**（cargo cap） | `head/soft` | 无 |
| **红 帽**（red cap） | `head/soft/red` | 无 |
| **蓝 帽**（blue cap） | `head/soft/blue` | 无 |
| **绿 帽**（green cap） | `head/soft/green` | 无 |
| **黄 帽**（yellow cap） | `head/soft/yellow` | 无 |
| **灰 帽**（grey cap） | `head/soft/grey` | 无 |
| **橙 帽**（orange cap） | `head/soft/orange` | 无 |
| **白 帽**（white cap） | `head/soft/mime` | 无 |
| **紫 帽**（purple cap） | `head/soft/purple` | 无 |
| **黑 帽**（black cap） | `head/soft/black` | 无 |
| **彩虹 帽**（rainbow cap） | `head/soft/rainbow` | 无 |
| **ecurity 帽**（security cap） | `head/soft/sec` | 30 / 25 / 25 / 35 / 25 / 20 / 50 / 5 |
| **老兵 帽**（veteran cap） | `head/soft/veteran` | 30 / 25 / 25 / 35 / 25 / 20 / 50 / 5 |
| **急救员 帽**（paramedic cap） | `head/soft/paramedic` | 无 |
| **传奇 fishing 帽**（legendary fishing hat） | `head/soft/fishing_hat` | 无 |
| **propeller 帽**（propeller hat） | `head/soft/propeller_hat` | 无 |
| **宽边帽**（sombrero） | `head/costume/sombrero` | 无 |
| **绿 ombrero**（green sombrero） | `head/costume/sombrero/green` | 无 |
| **羞耻帽**（shamebrero） | `head/costume/sombrero/shamebrero` | 无 |
| **锡箔帽**（tinfoil hat） | `head/costume/foilhat` | 无 |
| **高顶礼帽**（top-hat） | `head/hats/tophat` | 无 |
| **高顶礼帽 balloon**（balloon top-hat） | `head/hats/tophat/balloon` | 无 |
| **焊接头盔**（welding helmet） | `head/utility/welding` | 10 / 100 / 60 |
| **放屁坐垫**（whoopee cushion） | `head/costume/whoopee` | 无 |
| **假发**（wig） | `head/wig` | 无 |
| **natural 假发**（natural wig） | `head/wig/natural` | 无 |


---

# 第四卷C · 面具全量总表（86 种）

| 名称 | 路径 | 护甲（近战/子弹/激光/能量/爆炸/生物/火/酸/伤口） |
|---|---|---|
| **面具**（mask） | `mask` | 无 |
| **面具 猪**（pig mask） | `mask/animal/pig` | 无 |
| **面具 frog**（frog mask） | `mask/animal/frog` | 无 |
| **面具 牛**（cow mask） | `mask/animal/cowmask` | 无 |
| **面具 horse**（horse mask） | `mask/animal/horsehead` | 无 |
| **面具 A 小 animal**（A small animal mask） | `mask/animal/small` | 无 |
| **面具 rat**（rat mask） | `mask/animal/small/rat` | 无 |
| **面具 fox**（fox mask） | `mask/animal/small/fox` | 无 |
| **面具 蜜蜂**（bee mask） | `mask/animal/small/bee` | 无 |
| **面具 熊**（bear mask） | `mask/animal/small/bear` | 无 |
| **面具 蝙蝠**（bat mask） | `mask/animal/small/bat` | 无 |
| **面具 乌鸦**（raven mask） | `mask/animal/small/raven` | 无 |
| **面具 jackal**（jackal mask） | `mask/animal/small/jackal` | 无 |
| **面具 tribal**（tribal mask） | `mask/animal/small/tribal` | 无 |
| **头巾**（bandana） | `mask/bandana` | 无 |
| **红 bandana**（red bandana） | `mask/bandana/red` | 无 |
| **蓝 bandana**（blue bandana） | `mask/bandana/blue` | 无 |
| **紫 bandana**（purple bandana） | `mask/bandana/purple` | 无 |
| **绿 bandana**（green bandana） | `mask/bandana/green` | 无 |
| **金 头巾**（gold bandana） | `mask/bandana/gold` | 无 |
| **橙 bandana**（orange bandana） | `mask/bandana/orange` | 无 |
| **黑 bandana**（black bandana） | `mask/bandana/black` | 无 |
| **白 bandana**（white bandana） | `mask/bandana/white` | 无 |
| **杜拉丝 bandana**（durathread bandana） | `mask/bandana/durathread` | 无 |
| **条纹 头巾**（striped bandana） | `mask/bandana/striped` | 无 |
| **条纹 头巾**（striped bandana） | `mask/bandana/striped/black` | 无 |
| **条纹 ecurity 头巾**（striped security bandana） | `mask/bandana/striped/security` | 无 |
| **条纹 cience 头巾**（striped science bandana） | `mask/bandana/striped/science` | 无 |
| **triped 工程 bandana**（striped engineering bandana） | `mask/bandana/striped/engineering` | 无 |
| **triped 医疗 bandana**（striped medical bandana） | `mask/bandana/striped/medical` | 无 |
| **货运 triped bandana**（striped cargo bandana） | `mask/bandana/striped/cargo` | 无 |
| **条纹 botany 头巾**（striped botany bandana） | `mask/bandana/striped/botany` | 无 |
| **kull 头巾**（skull bandana） | `mask/bandana/skull` | 无 |
| **围巾 face**（facescarf） | `mask/facescarf` | 无 |
| **巴拉克拉瓦帽**（balaclava） | `mask/balaclava` | 无 |
| **地砖 balaclava**（floortile balaclava） | `mask/floortilebalaclava` | 无 |
| **面具 Luchador**（Luchador Mask） | `mask/luchador` | 无 |
| **面具 Tecnico**（Tecnicos Mask） | `mask/luchador/tecnicos` | 无 |
| **面具 Rudo**（Rudos Mask） | `mask/luchador/rudos` | 无 |
| **俄式 balaclava**（russian balaclava） | `mask/russian_balaclava` | 无 |
| **呼吸面罩**（breath mask） | `mask/breath` | 50 |
| **面具 医疗**（medical mask） | `mask/breath/medical` | 90 |
| **面具 urgery**（surgery mask） | `mask/breath/muzzle` | 100 |
| **面具 emotion**（emotion mask） | `mask/joy` | 无 |
| **面具 mummy**（mummy mask） | `mask/mummy` | 无 |
| **面具 ack**（sack mask） | `mask/scarecrow` | 无 |
| **面具 kitsune**（kitsune mask） | `mask/kitsune` | 无 |
| **面具 rebellion**（rebellion mask） | `mask/rebellion` | 无 |
| **防毒面具**（gas mask） | `mask/gas` | 100 |
| **防毒面具 大气**（atmospheric gas mask） | `mask/gas/atmos` | 100 / 20 / 10 |
| **船长 ga mask**（captain's gas mask） | `mask/gas/atmos/captain` | 100 / 20 / 10 |
| **防毒面具 中央指挥部**（CentCom gas mask） | `mask/gas/atmos/centcom` | 100 / 20 / 10 |
| **面具 焊接**（welding mask） | `mask/gas/welding` | 10 / 100 / 100 / 55 |
| **面具 plague 医生**（plague doctor mask） | `mask/gas/plaguedoctor` | 100 |
| **辛迪加 mask**（syndicate mask） | `mask/gas/syndicate` | 100 |
| **面具 Cybersun**（Cybersun mask） | `mask/gas/syndicate/cybersun` | 100 |
| **面具 小丑 wig and**（clown wig and mask） | `mask/gas/clown_hat` | 100 |
| **面具 exy-clown wig and**（sexy-clown wig and mask） | `mask/gas/sexyclown` | 100 |
| **面具 gamer wig and**（gamer's wig and mask） | `mask/gas/jonkler` | 100 |
| **面具 哑剧**（mime mask） | `mask/gas/mime` | 100 |
| **面具 monkey**（monkey mask） | `mask/gas/monkeymask` | 100 |
| **面具 exy 哑剧**（sexy mime mask） | `mask/gas/sexymime` | 100 |
| **cyborg 面罩**（cyborg visor） | `mask/gas/cyborg` | 100 |
| **面具 owl**（owl mask） | `mask/gas/owl_mask` | 100 |
| **面具 鲤**（carp mask） | `mask/gas/carp` | 100 |
| **面具 tiki**（tiki mask） | `mask/gas/tiki_mask` | 100 |
| **赏金猎杀面具**（bounty hunting mask） | `mask/gas/hunter` | 100 |
| **防毒面具 prop**（prop gas mask） | `mask/gas/prop` | 100 |
| **防毒面具 prop 大气**（prop atmospheric gas mask） | `mask/gas/atmosprop` | 100 |
| **面具 driscoll**（driscoll mask） | `mask/gas/driscoll` | 100 |
| **面具 gondola**（gondola mask） | `mask/gondola` | 无 |
| **防毒面具 ecurity**（security gas mask） | `mask/gas/sechailer` | 100 |
| **特警 mask**（SWAT mask） | `mask/gas/sechailer/swat` | 100 |
| **面具 pacepol**（spacepol mask） | `mask/gas/sechailer/swat/spacepol` | 100 |
| **安保 hailer**（security hailer） | `mask/gas/sechailer/cyborg` | 100 |
| **警 whistle**（police whistle） | `mask/whistle` | 无 |
| **派对喇叭**（party horn） | `mask/party_horn` | 无 |
| **fake 胡子**（fake moustache） | `mask/fakemoustache` | 无 |
| **italian 胡子**（italian moustache） | `mask/fakemoustache/italian` | 无 |
| **口套**（muzzle） | `mask/muzzle` | 无 |
| **胶带 件**（tape piece） | `mask/muzzle/tape` | 无 |
| **uper 胶带 件**（super tape piece） | `mask/muzzle/tape/super` | 无 |
| **urgical 胶带 件**（surgical tape piece） | `mask/muzzle/tape/surgical` | 无 |
| **pointy 胶带 件**（pointy tape piece） | `mask/muzzle/tape/pointy` | 无 |
| **uper pointy 胶带 件**（super pointy tape piece） | `mask/muzzle/tape/pointy/super` | 无 |
| **面具 terile**（sterile mask） | `mask/surgical` | 100 |

# 第五卷 · 鞋类与眼镜

**代码**: `clothing/shoes/`（1,772 行）+ `clothing/glasses/`（1,413 行）——鞋 75+ 种/眼镜 65+ 种

## 5.1 鞋类（75+ 种核心）

| 鞋 | 特性 |
|---|---|
| **战斗靴 Combat Boots** | 标准战斗 |
| **磁力靴 Magboots** | 磁力锁定|
| **工作靴 Work Boots** | 工程标配 |
| **气垫鞋** | 防滑 |
| **魔法鞋** | 法师用 |
| 防滑鞋 | 防滑倒 |
| 潜水鞋 | 水下 |
| 各职业靴 | 职业配色 |

## 5.2 眼镜（65+ 种核心）

| 眼镜 | 特性 |
|---|---|
| **夜视镜** | 夜视 |
| **医疗扫描镜** | 医疗 HUD |
| **安保 HUD** | 犯罪记录扫描 |
| **工程扫描镜** | 结构完整性 |
| **科学扫描镜** | 试剂分析 |
| 墨镜 | 防闪 |
| **外星眼镜** | 特殊视野 |
| 战术眼镜 | 战斗 HUD |


---

# 第五卷B · 鞋类全量总表（75 种）

| 名称 | 路径 | 护甲（近战/子弹/激光/能量/爆炸/生物/火/酸/伤口） |
|---|---|---|
| **鞋**（shoes） | `shoes` | 50 |
| **mk-honk 原型 hoe**（mk-honk prototype shoes） | `shoes/clown_shoes/banana_shoes` | 50 |
| **长筒靴**（jackboots） | `shoes/jackboots` | 90 |
| **长筒靴 floortile camouflage**（floortile camouflage jackboots） | `shoes/jackboots/floortile` | 90 |
| **冬 靴**（winter boots） | `shoes/winterboots` | 80 |
| **ice hiking 靴**（ice hiking boots） | `shoes/winterboots/ice_boots` | 80 |
| **Endotherm hiking 靴**（Endotherm hiking boots） | `shoes/winterboots/ice_boots/eva` | 10 / 10 / 10 / 50 / 50 / 10 |
| **工作靴**（work boots） | `shoes/workboots` | 80 |
| **采矿 靴**（mining boots） | `shoes/workboots/mining` | 80 |
| **工作靴 战术**（tactical work boots） | `shoes/workboots/black` | 80 |
| **俄式 靴**（russian boots） | `shoes/russian` | 50 |
| **绿 lizardskin hoe**（green lizardskin shoes） | `shoes/discoshoes` | 50 |
| **aerostatic 靴**（aerostatic boots） | `shoes/jackboots/kim` | 90 |
| **海盗 靴**（pirate boots） | `shoes/pirate` | 50 |
| **小丑 hoe**（clown shoes） | `shoes/clown_shoes` | 50 |
| **小丑 鞋**（jester shoes） | `shoes/clown_shoes/jester` | 50 |
| **meown 鞋**（meown shoes） | `shoes/clown_shoes/meown_shoes` | 50 |
| **蛾子**（moffers） | `shoes/clown_shoes/moffers` | 50 |
| **战斗 小丑 hoe**（combat clown shoes） | `shoes/clown_shoes/combat` | 25 / 25 / 25 / 25 / 50 / 90 / 70 / 50 |
| **mk-honk 战斗 hoe**（mk-honk combat shoes） | `shoes/clown_shoes/banana_shoes/combat` | 25 / 25 / 25 / 25 / 50 / 50 / 90 / 50 |
| **roman 凉鞋**（roman sandals） | `shoes/roman` | 10 |
| **griffon 靴**（griffon boots） | `shoes/griffin` | 50 |
| **黄 performer 靴**（yellow performer's boots） | `shoes/singery` | 50 |
| **蓝 performer 靴**（blue performer's boots） | `shoes/singerb` | 50 |
| **红 performer 靴**（red performer's boots） | `shoes/singerr` | 50 |
| **青铜 靴**（bronze boots） | `shoes/bronze` | 50 |
| **grilling 凉鞋**（grilling sandals） | `shoes/cookflops` | 50 |
| **frosty 靴**（frosty boots） | `shoes/jackbros` | 50 |
| **wag 鞋**（swag shoes） | `shoes/swagshoes` | 50 |
| **glow 鞋**（glow shoes） | `shoes/glow` | 50 |
| **圣徒运动鞋**（saints sneakers） | `shoes/saints` | 50 |
| **小丑 鞋**（jester shoes） | `shoes/jester_shoes` | 50 |
| **ducky 鞋**（ducky shoes） | `shoes/ducky_shoes` | 50 |
| **牛仔 靴**（cowboy boots） | `shoes/cowboy` | 90 |
| **白 牛仔 靴**（white cowboy boots） | `shoes/cowboy/white` | 90 |
| **黑 牛仔 靴**（black cowboy boots） | `shoes/cowboy/black` | 90 |
| **bilton wrangler 靴**（bilton wrangler boots） | `shoes/cowboy/fancy` | 95 |
| **lizardskin 靴**（lizardskin boots） | `shoes/cowboy/lizard` | 90 / 40 |
| **Hugs-The-Feet lizardskin 靴**（Hugs-The-Feet lizardskin boots） | `shoes/cowboy/lizard/masterwork` | 90 / 40 |
| **黑 purred 牛仔 靴**（black spurred cowboy boots） | `shoes/cowboy/black/syndicate` | 25 / 25 / 25 / 25 / 50 / 90 / 70 / 50 |
| **Nar'Sian 靴**（Nar'Sian boots） | `shoes/cult` | 50 |
| **Nar'Sian invoker 靴**（Nar'Sian invoker boots） | `shoes/cult/alt` | 50 |
| **橡胶套鞋**（galoshes） | `shoes/galoshes` | 100 / 40 / 75 |
| **吸水套鞋**（absorbent galoshes） | `shoes/galoshes/dry` | 100 / 40 / 75 |
| **障碍靴**（disaboots） | `shoes/gunboots/disabler` | 无 |
| **jump 靴**（jump boots） | `shoes/bhop` | 90 |
| **rocket 靴**（rocket boots） | `shoes/bhop/rocket` | 90 |
| **jet 靴**（jet boots） | `shoes/bhop/rocket/jet` | 90 |
| **Kindle 运动鞋**（Kindle Kicks） | `shoes/kindle_kicks` | 50 |
| **laceup 鞋**（laceup shoes） | `shoes/laceup` | 50 |
| **磁力靴**（magboots） | `shoes/magboots` | 90 |
| **磁力靴 高级**（advanced magboots） | `shoes/magboots/advance` | 90 |
| **磁力靴 blood-red**（blood-red magboots） | `shoes/magboots/syndie` | 90 |
| **凉鞋**（sandals） | `shoes/sandal` | 10 |
| **黑 andal**（black sandals） | `shoes/sandal/alt` | 10 |
| **magical 凉鞋**（magical sandals） | `shoes/sandal/magic` | 10 |
| **flip-flop**（flip-flops） | `shoes/sandal/beach` | 10 |
| **velcro 凉鞋**（velcro sandals） | `shoes/sandal/velcro` | 10 |
| **黑 velcro andal**（black velcro sandals） | `shoes/sandal/alt/velcro` | 10 |
| **黑 hoe**（black shoes） | `shoes/sneakers/black` | 无 |
| **棕 hoe**（brown shoes） | `shoes/sneakers/brown` | 无 |
| **蓝 hoe**（blue shoes） | `shoes/sneakers/blue` | 95 |
| **绿 hoe**（green shoes） | `shoes/sneakers/green` | 无 |
| **黄 hoe**（yellow shoes） | `shoes/sneakers/yellow` | 无 |
| **紫 hoe**（purple shoes） | `shoes/sneakers/purple` | 无 |
| **红 hoe**（red shoes） | `shoes/sneakers/red` | 无 |
| **白 hoe**（white shoes） | `shoes/sneakers/white` | 95 |
| **彩虹 hoe**（rainbow shoes） | `shoes/sneakers/rainbow` | 无 |
| **橙 hoe**（orange shoes） | `shoes/sneakers/orange` | 无 |
| **哑剧 hoe**（mime shoes） | `shoes/sneakers/mime` | 无 |
| **魔法 hoe**（magic shoes） | `shoes/sneakers/marisa` | 无 |
| **cyborg 靴**（cyborg boots） | `shoes/sneakers/cyborg` | 无 |
| **Wheely-Heel**（Wheely-Heels） | `shoes/wheelys` | 50 |
| **轮滑鞋**（roller skates） | `shoes/wheelys/rollerskates` | 50 |
| **ki 鞋**（ski shoes） | `shoes/wheelys/skishoes` | 50 |

# 第五卷C · 眼镜全量总表（71 种）

| 名称 | 路径 | 护甲（近战/子弹/激光/能量/爆炸/生物/火/酸/伤口） |
|---|---|---|
| **眼镜**（glasses） | `glasses` | 无 |
| **optical 介子 canner**（optical meson scanner） | `glasses/meson` | 无 |
| **夜视 介子 canner**（night vision meson scanner） | `glasses/meson/night` | 无 |
| **加尔 介子**（gar mesons） | `glasses/meson/gar` | 无 |
| **cience 护目镜**（science goggles） | `glasses/science` | 80 / 100 |
| **夜视 cience goggle**（night vision science goggles） | `glasses/science/night` | 80 / 100 |
| **夜视 goggle**（night vision goggles） | `glasses/night` | 无 |
| **眼罩**（eyepatch） | `glasses/eyepatch` | 无 |
| **眼罩 医疗**（medical eyepatch） | `glasses/eyepatch/medical` | 无 |
| **单片眼镜**（monocle） | `glasses/monocle` | 无 |
| **optical material 扫描仪**（optical material scanner） | `glasses/material` | 无 |
| **optical material 扫描仪**（optical material scanner） | `glasses/material/mining` | 无 |
| **加尔 material canner**（gar material scanner） | `glasses/material/mining/gar` | 无 |
| **处方 眼镜**（prescription glasses） | `glasses/regular` | 无 |
| **thin 处方 眼镜**（thin prescription glasses） | `glasses/regular/thin` | 无 |
| **jamjar 眼镜**（jamjar glasses） | `glasses/regular/jamjar` | 无 |
| **处方 眼镜**（prescription glasses） | `glasses/regular/hipster` | 无 |
| **circle 眼镜**（circle glasses） | `glasses/regular/circle` | 无 |
| **太阳镜**（sunglasses） | `glasses/sunglasses` | 无 |
| **beer 护目镜**（beer goggles） | `glasses/sunglasses/reagent` | 无 |
| **cience 眼镜**（science glasses） | `glasses/sunglasses/chemical` | 无 |
| **黑 加尔 glasse**（black gar glasses） | `glasses/sunglasses/gar` | 无 |
| **加尔 glasse**（gar glasses） | `glasses/sunglasses/gar/orange` | 无 |
| **巨型加尔眼镜 黑**（black giga gar glasses） | `glasses/sunglasses/gar/giga` | 无 |
| **巨型加尔眼镜**（giga gar glasses） | `glasses/sunglasses/gar/giga/red` | 无 |
| **黑白 眼镜**（noir glasses） | `glasses/sunglasses/noir` | 无 |
| **焊接 goggle**（welding goggles） | `glasses/welding` | 无 |
| **眼罩**（blindfold） | `glasses/blindfold` | 无 |
| **眼罩**（blindfold） | `glasses/trickblindfold` | 无 |
| **blind 人员 blindfold**（blind personnel blindfold） | `glasses/blindfold/white` | 无 |
| **optical 热 canner**（optical thermal scanner） | `glasses/thermal` | 无 |
| **辛迪加 xray goggle**（syndicate xray goggles） | `glasses/thermal/xray` | 无 |
| **变色龙 热**（chameleon thermals） | `glasses/thermal/syndi` | 无 |
| **热单目镜**（thermoncle） | `glasses/thermal/monocle` | 无 |
| **眼罩 optical 热**（optical thermal eyepatch） | `glasses/thermal/eyepatch` | 无 |
| **cold 护目镜**（cold goggles） | `glasses/cold` | 无 |
| **heat 护目镜**（heat goggles） | `glasses/heat` | 无 |
| **橙 glasse**（orange glasses） | `glasses/orange` | 无 |
| **红 glasse**（red glasses） | `glasses/red` | 无 |
| **鬼灵凝视镜**（geist gazers） | `glasses/geist_gazers` | 无 |
| **psych 眼镜**（psych glasses） | `glasses/psych` | 无 |
| **debug 眼镜**（debug glasses） | `glasses/debug` | 无 |
| **望远镜镜片**（binoclard lenses） | `glasses/regular/kim` | 无 |
| **colored 眼镜**（colored glasses） | `glasses/salesman` | 无 |
| **nightmare 视 goggle**（nightmare vision goggles） | `glasses/nightmare_vision` | 无 |
| **太阳镜 O.S.I.**（O.S.I. Sunglasses） | `glasses/osi` | 无 |
| **面具 Phantom Thief**（Phantom Thief Mask） | `glasses/phantom` | 无 |
| **工程 canner goggle**（engineering scanner goggles） | `glasses/meson/engine` | 无 |
| **huttle region 扫描仪**（shuttle region scanner） | `glasses/meson/engine/shuttle` | 无 |
| **大气 热 imaging goggle**（atmospheric thermal imaging goggles） | `glasses/meson/engine/atmos_imaging` | 无 |
| **admin imaging 护目镜**（admin imaging goggles） | `glasses/meson/engine/admin` | 无 |
| **HUD**（HUD） | `glasses/hud` | 无 |
| **health 扫描仪 HUD**（health scanner HUD） | `glasses/hud/health` | 无 |
| **health 扫描仪 ecurity HUD**（health scanner security HUD） | `glasses/hud/medsechud` | 无 |
| **夜视 health canner HUD**（night vision health scanner HUD） | `glasses/hud/health/night` | 无 |
| **夜视 介子 health canner HUD**（night vision meson health scanner HUD） | `glasses/hud/health/night/meson` | 无 |
| **夜视 医疗 cience canner HUD**（night vision medical science scanner HUD） | `glasses/hud/health/night/science` | 无 |
| **太阳镜 医疗 HUD**（medical HUDSunglasses） | `glasses/hud/health/sunglasses` | 无 |
| **诊断 HUD**（diagnostic HUD） | `glasses/hud/diagnostic` | 无 |
| **夜视 diagnostic HUD**（night vision diagnostic HUD） | `glasses/hud/diagnostic/night` | 无 |
| **太阳镜 diagnostic**（diagnostic sunglasses） | `glasses/hud/diagnostic/sunglasses` | 无 |
| **安保 HUD**（security HUD） | `glasses/hud/security` | 无 |
| **变色龙 ecurity HUD**（chameleon security HUD） | `glasses/hud/security/chameleon` | 无 |
| **眼罩 HUD**（eyepatch HUD） | `glasses/hud/security/sunglasses/eyepatch` | 无 |
| **太阳镜 ecurity HUD**（security HUDSunglasses） | `glasses/hud/security/sunglasses` | 无 |
| **夜视 ecurity HUD**（night vision security HUD） | `glasses/hud/security/night` | 无 |
| **HUD 加尔 glasse**（HUD gar glasses） | `glasses/hud/security/sunglasses/gars` | 无 |
| **巨型 HUD 加尔 glasse**（giga HUD gar glasses） | `glasses/hud/security/sunglasses/gars/giga` | 无 |
| **切换 HUD**（Toggle HUD） | `glasses/hud/toggle` | 无 |
| **热 HUD canner**（thermal HUD scanner） | `glasses/hud/toggle/thermal` | 无 |
| **警 aviator**（police aviators） | `glasses/hud/spacecop` | 无 |

# 第六卷 · 手套/脖子/腰带
## 6.1 手套（65 种精选）

**代码**: `clothing/gloves/`（1,105 行）

### 绝缘手套家族（防电击核心）

| 手套 | 特性 |
|---|---|
| **绝缘手套 Insulated** | 标准绝缘（防电击） |
| **重绝缘手套 Ceramic-lined** | 陶瓷衬里（更耐） |
| 喷式绝缘 | 一次性喷雾 |
| 磨损绝缘 | 老旧版 |
| 露指绝缘 | 露指版 |
| **总工程师绝缘** | 高级绝缘 |

### 战斗手套

| 手套 | 特性 |
|---|---|
| **战斗手套 Combat** | 基础战斗 |
| **北极星手套 Gloves of the North Star** | 强化拳击（A 档） |
| **大猩猩手套 Gorilla** | 力量强化 |
| **火箭手套 Rocket** | 火箭助推拳 |
| **H.A.U.L. 外骨骼手套** | 工程外骨骼 |

### 环境手套（Envirogloves）

| 手套 | 特性 |
|---|---|
| 等离子人/黑/丁腈/白 | 环境隔离 |
| 各职业版（工程/大气/医务/货运等） | 职业专属 |
| **翻译手套** | 翻译功能 |

### 其他

| 手套 | 特性 |
|---|---|
| 拳击手套/邪恶拳击 | 拳击 |
| **内热手套 Endotherm** | 保暖 |
| 钓鱼手套 | 钓鱼加速 |
| 抢球手套 | 增强抓取 |
| **海豚手套** | 游泳强化 |
| 一次性医用手套（乳胶/丁腈） | 卫生 |

## 6.2 脖子（精选 · 全量 37 种见第六卷C）

**代码**: `clothing/neck/`（857 行，37 种 = 36 具体物品 + 1 抽象基类）

| 类别 | 物品 |
|---|---|
| 领带 | 油滑领带/蓝色/红色/黑色/恐怖领带/侦探领带 |
| 蝴蝶结 | 标准/彩虹 |
| 围巾 | 11 色+大型围巾 3 色+**辛迪加围巾**+无限围巾 |
| 披风 | 长袍披风 |
| **听诊器** | 医疗诊断 |
| **项圈** | 宠物项圈 |
| 项链 | 金项链/塑料珠 |
| **花环** | 守望者花环/冰翼花环/熔翼花环（拉瓦兰战利品） |
| **项圈炸弹** | 爆炸项圈（安保用） |
| 恐怖领带 | 活领带（生物） |

## 6.3 腰带

**代码**: `clothing/belts/`（201 行）——储物腰带（详见储物系统）。

---


---

# 第六卷B · 手套全量总表（70 种）

| 名称 | 路径 | 护甲（近战/子弹/激光/能量/爆炸/生物/火/酸/伤口） |
|---|---|---|
| **手套**（gloves） | `gloves` | 无 |
| **骨 bracer**（bone bracers） | `gloves/bracer` | 15 / 25 / 15 / 15 / 20 / 10 |
| **植物学家 皮革 手套**（botanist's leather gloves） | `gloves/botanic_leather` | 50 / 70 / 30 |
| **拳击 手套**（boxing gloves） | `gloves/boxing` | 无 |
| **evil 拳击 手套**（evil boxing gloves） | `gloves/boxing/evil` | 无 |
| **金 手套**（golden gloves） | `gloves/boxing/golden` | 无 |
| **黑 手套**（black gloves） | `gloves/color/black` | 无 |
| **ecurity 手套**（security gloves） | `gloves/color/black/security` | 无 |
| **fingerle 手套**（fingerless gloves） | `gloves/fingerless` | 无 |
| **橙 手套**（orange gloves） | `gloves/color/orange` | 无 |
| **红 手套**（red gloves） | `gloves/color/red` | 无 |
| **绝缘手套**（insulated gloves） | `gloves/color/red/insulated` | 50 |
| **彩虹 手套**（rainbow gloves） | `gloves/color/rainbow` | 无 |
| **蓝 手套**（blue gloves） | `gloves/color/blue` | 无 |
| **紫 手套**（purple gloves） | `gloves/color/purple` | 无 |
| **绿 手套**（green gloves） | `gloves/color/green` | 无 |
| **灰 手套**（grey gloves） | `gloves/color/grey` | 无 |
| **Endotherm 手套**（Endotherm gloves） | `gloves/color/grey/protects_cold` | 无 |
| **轻 棕 手套**（light brown gloves） | `gloves/color/light_brown` | 无 |
| **棕 手套**（brown gloves） | `gloves/color/brown` | 无 |
| **白 手套**（white gloves） | `gloves/color/white` | 无 |
| **战斗手套**（combat gloves） | `gloves/combat` | 90 / 80 / 50 |
| **附魔 手套**（enchanted gloves） | `gloves/combat/wizard` | 90 / 80 / 50 |
| **floortile camouflage 手套**（floortile camouflage gloves） | `gloves/combat/floortile` | 90 / 80 / 50 |
| **aerostatic 手套**（aerostatic gloves） | `gloves/kim` | 无 |
| **绝缘手套**（insulated gloves） | `gloves/color/yellow` | 50 |
| **绝缘手套 ceramic-lined**（ceramic-lined insulated gloves） | `gloves/color/yellow/heavy` | 50 |
| **绝缘手套 pray-on**（spray-on insulated gloves） | `gloves/color/yellow/sprayon` | 无 |
| **绝缘手套 磨损 out**（worn out insulated gloves） | `gloves/color/fyellow/old` | 无 |
| **绝缘手套 fingerle**（fingerless insulated gloves） | `gloves/cut` | 无 |
| **绝缘手套 高级**（advanced insulated gloves） | `gloves/chief_engineer` | 无 |
| **环境手套 等离子**（plasma envirogloves） | `gloves/color/plasmaman` | 100 / 95 / 95 |
| **环境手套 黑**（black envirogloves） | `gloves/color/plasmaman/black` | 100 / 95 / 95 |
| **环境手套 丁腈**（nitrile envirogloves） | `gloves/color/plasmaman/plasmanitrile` | 100 / 95 / 95 |
| **环境手套 白**（white envirogloves） | `gloves/color/plasmaman/white` | 100 / 95 / 95 |
| **环境手套 机器人学家**（roboticist envirogloves） | `gloves/color/plasmaman/robot` | 100 / 95 / 95 |
| **环境手套 janitor**（janitor envirogloves） | `gloves/color/plasmaman/janny` | 100 / 95 / 95 |
| **环境手套 货运**（cargo envirogloves） | `gloves/color/plasmaman/cargo` | 100 / 95 / 95 |
| **环境手套 工程**（engineering envirogloves） | `gloves/color/plasmaman/engineer` | 100 / 95 / 95 |
| **环境手套 atmo**（atmos envirogloves） | `gloves/color/plasmaman/atmos` | 100 / 95 / 95 |
| **环境手套 探险**（explorer envirogloves） | `gloves/color/plasmaman/explorer` | 100 / 95 / 95 |
| **环境手套 botany**（botany envirogloves） | `gloves/color/plasmaman/botanic_leather` | 100 / 95 / 95 |
| **环境手套 原型**（prototype envirogloves） | `gloves/color/plasmaman/prototype` | 100 / 95 / 95 |
| **环境手套 小丑**（clown envirogloves） | `gloves/color/plasmaman/clown` | 100 / 95 / 95 |
| **人事主管 enviroglove**（head of personnel's envirogloves） | `gloves/color/plasmaman/head_of_personnel` | 100 / 95 / 95 |
| **总工程师 enviroglove**（chief engineer's envirogloves） | `gloves/color/plasmaman/chief_engineer` | 100 / 95 / 95 |
| **研究主任 enviroglove**（research director's envirogloves） | `gloves/color/plasmaman/research_director` | 100 / 95 / 95 |
| **环境手套 中央指挥部 指挥官**（CentCom commander envirogloves） | `gloves/color/plasmaman/centcom_commander` | 100 / 95 / 95 |
| **环境手套 中央指挥部 官员**（CentCom official envirogloves） | `gloves/color/plasmaman/centcom_official` | 100 / 95 / 95 |
| **环境手套 中央指挥部 实习生**（CentCom intern envirogloves） | `gloves/color/plasmaman/centcom_intern` | 100 / 95 / 95 |
| **环境手套 翻译**（translation envirogloves） | `gloves/color/plasmaman/radio` | 100 / 95 / 95 |
| **punching mitt**（punching mitts） | `gloves/fingerless/punch_mitts` | 25 / 5 / 5 / 5 / 100 / 100 / 30 |
| **H.A.U.L. gauntlet**（H.A.U.L. gauntlets） | `gloves/cargo_gauntlet` | 无 |
| **手套 of the North Star**（Gloves of the North Star） | `gloves/rapid` | 无 |
| **翻译 手套**（translation gloves） | `gloves/radio` | 无 |
| **race 手套**（race gloves） | `gloves/race` | 无 |
| **船长 手套**（captain's gloves） | `gloves/captain` | 90 / 70 / 50 |
| **乳胶 手套**（latex gloves） | `gloves/latex` | 100 |
| **丁腈 手套**（nitrile gloves） | `gloves/latex/nitrile` | 100 |
| **法医 手套**（coroner's gloves） | `gloves/latex/coroner` | 100 |
| **tinker 手套**（tinker's gloves） | `gloves/tinkerer` | 70 |
| **大气 extrication 手套**（atmospheric extrication gloves） | `gloves/atmos` | 无 |
| **athletic fishing 手套**（athletic fishing gloves） | `gloves/fishing` | 无 |
| **enhanced retrieval 手套**（enhanced retrieval gloves） | `gloves/tackler` | 无 |
| **dolphin 手套**（dolphin gloves） | `gloves/tackler/dolphin` | 无 |
| **大猩猩 手套**（gorilla gloves） | `gloves/tackler/combat` | 无 |
| **guerrilla 手套**（guerrilla gloves） | `gloves/tackler/combat/insulated` | 50 |
| **rocket 手套**（rocket gloves） | `gloves/tackler/rocket` | 无 |
| **improvised gripper 手套**（improvised gripper gloves） | `gloves/tackler/offbrand` | 无 |
| **橄榄球 手套**（football gloves） | `gloves/tackler/football` | 无 |

# 第六卷C · 脖子全量总表（37 种）

| 名称 | 路径 | 护甲（近战/子弹/激光/能量/爆炸/生物/火/酸/伤口） |
|---|---|---|
| **项链**（necklace） | `neck` | 无 |
| **蝴蝶结**（bow tie） | `neck/bowtie` | 无 |
| **蝴蝶结 彩虹**（rainbow bow tie） | `neck/bowtie/rainbow` | 无 |
| **lick 领带**（slick tie） | `neck/tie` | 无 |
| **蓝 领带**（blue tie） | `neck/tie/blue` | 无 |
| **红 领带**（red tie） | `neck/tie/red` | 无 |
| **黑 领带**（black tie） | `neck/tie/black` | 无 |
| **horrible 领带**（horrible tie） | `neck/tie/horrible` | 无 |
| **披风 长袍**（robe cape） | `neck/robe_cape` | 无 |
| **loose 领带**（loose tie） | `neck/tie/detective` | 无 |
| **听诊器**（stethoscope） | `neck/stethoscope` | 无 |
| **围巾**（scarf） | `neck/scarf` | 无 |
| **围巾 黑**（black scarf） | `neck/scarf/black` | 无 |
| **围巾 粉**（pink scarf） | `neck/scarf/pink` | 无 |
| **围巾 红**（red scarf） | `neck/scarf/red` | 无 |
| **围巾 绿**（green scarf） | `neck/scarf/green` | 无 |
| **围巾 深 蓝**（dark blue scarf） | `neck/scarf/darkblue` | 无 |
| **围巾 紫**（purple scarf） | `neck/scarf/purple` | 无 |
| **围巾 黄**（yellow scarf） | `neck/scarf/yellow` | 无 |
| **围巾 橙**（orange scarf） | `neck/scarf/orange` | 无 |
| **围巾 青**（cyan scarf） | `neck/scarf/cyan` | 无 |
| **围巾 zebra**（zebra scarf） | `neck/scarf/zebra` | 无 |
| **围巾 christma**（christmas scarf） | `neck/scarf/christmas` | 无 |
| **围巾 large**（large scarf） | `neck/large_scarf` | 无 |
| **围巾 large 红**（large red scarf） | `neck/large_scarf/red` | 无 |
| **围巾 large 绿**（large green scarf） | `neck/large_scarf/green` | 无 |
| **围巾 large 蓝**（large blue scarf） | `neck/large_scarf/blue` | 无 |
| **围巾 uspiciou looking triped**（suspicious looking striped scarf） | `neck/large_scarf/syndie` | 50 / 40 |
| **围巾 infinity**（infinity scarf） | `neck/infinity_scarf` | 无 |
| **宠物项圈**（pet collar） | `neck/petcollar` | 无 |
| **金项链**（gold necklace） | `neck/necklace/dope` | 无 |
| **plastic bead necklace**（plastic bead necklace） | `neck/beads` | 无 |
| **守望者 wreath**（watcher wreath） | `neck/wreath` | 无 |
| **icewing 花环**（icewing wreath） | `neck/wreath/icewing` | 无 |
| **magmawing 花环**（magmawing wreath） | `neck/wreath/magmawing` | 无 |
| **项圈炸弹**（collar bomb） | `neck/collar_bomb` | 97 / 97 / 97 |
| **领带 horrific**（horrific necktie） | `neck/tie/disco` | 无 |

# 第七卷 · 太空服

**代码**: `clothing/spacesuits/`（1,383 行）

## 7.1 太空服通用机制

| 机制 | 值 |
|---|---|
| 防压 | STOPSPRESSUREDAMAGE（免压伤） |
| **热调节器** | 0.018 × 标准电池/秒（THERMAL_REGULATOR_COST） |
| 电池 | 内置高电池（可换） |
| 温度设置 | 默认 BODYTEMP_NORMAL（可调） |
| 脱卸延迟 | 8 秒（strip_delay） |
| **钓鱼修正** | 太空服影响钓鱼难度（+3 头盔） |
| 慢速 | slowdown 1 |

## 7.2 太空头盔通用机制

| 机制 | 值 |
|---|---|
| 防 UV | 太阳紫外屏蔽 |
| 面罩翻转 | visor 可上下（flash_protect 焊工级） |
| 帽稳定器 | hat_stabilizer 组件（防掉帽） |
| 脱卸 | 5 秒 |

## 7.3 太空服型号

| 型号 | 特性 |
|---|---|
| **标准太空服** | 基础（背上有 13） |
| **软太空服 Softsuit** | 轻便民用 |
| **辛迪加太空服** | 反派用 |
| **特种行动服** | 精英 |
| **赏金猎人服** | 赏金猎人 |
| **海盗太空服** | 海盗 |
| **等离子人服** | 等离子人专用 |
| **自由服 Freedom** | 特色 |
| **圣诞老人服** | 节日 |

---

---

---

# 第七卷B · 太空服全量总表（84 种）

| 名称 | 路径 | 护甲（近战/子弹/激光/能量/爆炸/生物/火/酸/伤口） |
|---|---|---|
| **太空头盔**（space helmet） | `head/helmet/space` | 100 / 80 / 70 |
| **太空服**（space suit） | `suit/space` | 100 / 80 / 70 |
| **赏金 猎 uit**（bounty hunting suit） | `suit/space/hunter` | 60 / 40 / 40 / 50 / 100 / 100 / 100 / 100 |
| **eagle 头盔**（eagle helmet） | `head/helmet/space/freedom` | 20 / 40 / 30 / 40 / 100 / 100 / 80 / 80 |
| **eagle 服**（eagle suit） | `suit/space/freedom` | 20 / 40 / 30 / 40 / 100 / 100 / 80 / 80 |
| **modified EVA 头盔**（modified EVA helmet） | `head/helmet/space/pirate` | 30 / 50 / 30 / 40 / 30 / 30 / 60 / 75 |
| **modified EVA 服**（modified EVA suit） | `suit/space/pirate` | 30 / 50 / 30 / 40 / 30 / 30 / 60 / 75 |
| **designer 海盗 头盔**（designer pirate helmet） | `head/helmet/space/pirate/tophat` | 30 / 50 / 30 / 40 / 30 / 30 / 60 / 75 |
| **designer 海盗 uit**（designer pirate suit） | `suit/space/pirate/silverscale` | 30 / 50 / 30 / 40 / 30 / 30 / 60 / 75 |
| **等离子环境服 EVA**（EVA plasma envirosuit） | `suit/space/eva/plasmaman` | 100 / 100 / 75 |
| **等离子环境头盔**（plasma envirosuit helmet） | `head/helmet/space/plasmaman` | 100 / 100 / 75 |
| **等离子环境头盔 ecurity**（security plasma envirosuit helmet） | `head/helmet/space/plasmaman/security` | /datum/armor/head_helmet/plasmaman |
| **等离子环境头盔 detective**（detective's plasma envirosuit helmet） | `head/helmet/space/plasmaman/security/detective` | /datum/armor/fedora_det_hat/plasmaman |
| **等离子环境头盔 典狱长**（warden's plasma envirosuit helmet） | `head/helmet/space/plasmaman/security/warden` | /datum/armor/hats_warden/plasmaman |
| **等离子环境头盔 head of ecurity**（head of security's plasma envirosuit helmet） | `head/helmet/space/plasmaman/security/head_of_security` | /datum/armor/hats_hos/plasmaman |
| **等离子环境头盔 prisoner**（prisoner's plasma envirosuit helmet） | `head/helmet/space/plasmaman/prisoner` | 100 / 100 / 75 |
| **等离子环境头盔 医疗 医生**（medical doctor's plasma envirosuit helmet） | `head/helmet/space/plasmaman/medical` | 100 / 100 / 75 |
| **等离子环境头盔 coroner**（coroners's plasma envirosuit helmet） | `head/helmet/space/plasmaman/coroner` | 100 / 100 / 75 |
| **等离子环境头盔 paramedic**（paramedic plasma envirosuit helmet） | `head/helmet/space/plasmaman/paramedic` | 100 / 100 / 75 |
| **等离子环境头盔 病毒学**（virology plasma envirosuit helmet） | `head/helmet/space/plasmaman/viro` | 100 / 100 / 75 |
| **等离子环境头盔 chemistry**（chemistry plasma envirosuit helmet） | `head/helmet/space/plasmaman/chemist` | 100 / 100 / 75 |
| **等离子环境头盔 总 医疗 军官**（chief medical officer's plasma envirosuit helmet） | `head/helmet/space/plasmaman/chief_medical_officer` | 100 / 100 / 75 |
| **等离子环境头盔 cience**（science plasma envirosuit helmet） | `head/helmet/space/plasmaman/science` | 100 / 100 / 75 |
| **等离子环境头盔 robotic**（robotics plasma envirosuit helmet） | `head/helmet/space/plasmaman/robotics` | 100 / 100 / 75 |
| **等离子环境头盔 geneticist**（geneticist's plasma envirosuit helmet） | `head/helmet/space/plasmaman/genetics` | 100 / 100 / 75 |
| **等离子环境头盔 研究 director**（research director's plasma envirosuit helmet） | `head/helmet/space/plasmaman/research_director` | 100 / 100 / 75 |
| **等离子环境头盔 工程**（engineering plasma envirosuit helmet） | `head/helmet/space/plasmaman/engineering` | /datum/armor/space_plasmaman/engineering_atmos |
| **等离子环境头盔 大气**（atmospherics plasma envirosuit helmet） | `head/helmet/space/plasmaman/atmospherics` | /datum/armor/space_plasmaman/engineering_atmos |
| **等离子环境头盔 总 工程师**（chief engineer's plasma envirosuit helmet） | `head/helmet/space/plasmaman/chief_engineer` | /datum/armor/space_plasmaman/engineering_atmos |
| **等离子环境头盔 货运**（cargo plasma envirosuit helmet） | `head/helmet/space/plasmaman/cargo` | 100 / 100 / 75 |
| **等离子环境头盔 采矿**（mining plasma envirosuit helmet） | `head/helmet/space/plasmaman/mining` | 100 / 100 / 75 |
| **等离子环境头盔 chaplain**（chaplain's plasma envirosuit helmet） | `head/helmet/space/plasmaman/chaplain` | 100 / 100 / 75 |
| **等离子环境头盔 白**（white plasma envirosuit helmet） | `head/helmet/space/plasmaman/white` | 100 / 100 / 75 |
| **等离子环境头盔 curator**（curator's plasma envirosuit helmet） | `head/helmet/space/plasmaman/curator` | 100 / 100 / 75 |
| **等离子环境头盔 botany**（botany plasma envirosuit helmet） | `head/helmet/space/plasmaman/botany` | 100 / 100 / 75 |
| **等离子环境头盔 janitor**（janitor's plasma envirosuit helmet） | `head/helmet/space/plasmaman/janitor` | 100 / 100 / 75 |
| **环境服 哑剧 头盔**（mime envirosuit helmet） | `head/helmet/space/plasmaman/mime` | 100 / 100 / 75 |
| **环境服 小丑 头盔**（clown envirosuit helmet） | `head/helmet/space/plasmaman/clown` | 100 / 100 / 75 |
| **人事主管 envirosuit 头盔**（head of personnel's envirosuit helmet） | `head/helmet/space/plasmaman/head_of_personnel` | /datum/armor/hats_hopcap/plasmaman |
| **等离子环境头盔 船长**（captain's plasma envirosuit helmet） | `head/helmet/space/plasmaman/captain` | /datum/armor/hats_caphat/plasmaman |
| **等离子环境头盔 中央指挥部 指挥官**（CentCom commander plasma envirosuit helmet） | `head/helmet/space/plasmaman/centcom_commander` | /datum/armor/hats_centhat/plasmaman |
| **等离子环境头盔 中央指挥部 官员**（CentCom official plasma envirosuit helmet） | `head/helmet/space/plasmaman/centcom_official` | 100 / 100 / 75 |
| **等离子环境头盔 中央指挥部 实习生**（CentCom intern plasma envirosuit helmet） | `head/helmet/space/plasmaman/centcom_intern` | 100 / 100 / 75 |
| **环境服 tacticool 头盔**（tacticool envirosuit helmet） | `head/helmet/space/plasmaman/syndie` | 100 / 100 / 75 |
| **等离子环境头盔 bitrunner**（bitrunner's plasma envirosuit helmet） | `head/helmet/space/plasmaman/bitrunner` | 100 / 100 / 75 |
| **Santa 帽**（Santa's hat） | `head/helmet/space/santahat` | 100 / 80 / 70 |
| **Santa uit**（Santa's suit） | `suit/space/santa` | 100 / 80 / 70 |
| **NASA 虚空 头盔**（NASA void helmet） | `head/helmet/space/nasavoid` | 100 / 80 / 70 |
| **NASA voidsuit**（NASA voidsuit） | `suit/space/nasavoid` | 100 / 80 / 70 |
| **工程 虚空 头盔**（engineering void helmet） | `head/helmet/space/nasavoid/old` | 100 / 80 / 70 |
| **工程 voidsuit**（engineering voidsuit） | `suit/space/nasavoid/old` | 100 / 80 / 70 |
| **EVA 服**（EVA suit） | `suit/space/eva` | 100 / 50 / 65 |
| **EVA 头盔**（EVA helmet） | `head/helmet/space/eva` | 100 / 50 / 65 |
| **太空头盔 紧急**（emergency space helmet） | `head/helmet/space/fragile` | 5 |
| **太空服 紧急**（emergency space suit） | `suit/space/fragile` | 5 |
| **军官 中央指挥部 beret**（CentCom officer's beret） | `head/helmet/space/beret` | 80 / 80 / 50 / 60 / 100 / 100 / 100 / 100 / 15 |
| **军官 中央指挥部 大衣**（CentCom officer's coat） | `suit/space/officer` | 80 / 80 / 50 / 60 / 100 / 100 / 100 / 100 / 15 |
| **太空头盔 红**（red space helmet） | `head/helmet/space/syndicate` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空服 红**（red space suit） | `suit/space/syndicate` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空头盔 绿**（green space helmet） | `head/helmet/space/syndicate/green` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空服 绿**（green space suit） | `suit/space/syndicate/green` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空头盔 深 绿**（dark green space helmet） | `head/helmet/space/syndicate/green/dark` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空服 深 绿**（dark green space suit） | `suit/space/syndicate/green/dark` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空头盔 橙**（orange space helmet） | `head/helmet/space/syndicate/orange` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空服 橙**（orange space suit） | `suit/space/syndicate/orange` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空头盔 蓝**（blue space helmet） | `head/helmet/space/syndicate/blue` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空服 蓝**（blue space suit） | `suit/space/syndicate/blue` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空头盔 黑**（black space helmet） | `head/helmet/space/syndicate/black` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空服 黑**（black space suit） | `suit/space/syndicate/black` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空头盔 黑**（black space helmet） | `head/helmet/space/syndicate/black/green` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空服 黑 and 绿**（black and green space suit） | `suit/space/syndicate/black/green` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空头盔 黑**（black space helmet） | `head/helmet/space/syndicate/black/blue` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空服 黑 and 蓝**（black and blue space suit） | `suit/space/syndicate/black/blue` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空服 黑 and 白**（black and white space suit） | `suit/space/syndicate/black/white` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空头盔 黑 医疗**（black medical space helmet） | `head/helmet/space/syndicate/black/med` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空服 黑 医疗**（black medical space suit） | `suit/space/syndicate/black/med` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空头盔 黑**（black space helmet） | `head/helmet/space/syndicate/black/orange` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空服 黑 and 橙**（black and orange space suit） | `suit/space/syndicate/black/orange` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空头盔 黑**（black space helmet） | `head/helmet/space/syndicate/black/red` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空服 黑 and 红**（black and red space suit） | `suit/space/syndicate/black/red` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **contractor 头盔**（contractor helmet） | `head/helmet/space/syndicate/contract` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空服 contractor**（contractor space suit） | `suit/space/syndicate/contract` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空头盔 黑 工程**（black engineering space helmet） | `head/helmet/space/syndicate/black/engie` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |
| **太空服 黑 工程**（black engineering space suit） | `suit/space/syndicate/black/engie` | 40 / 50 / 30 / 40 / 30 / 30 / 80 / 85 |

# 第八卷 · MOD 模块服（Modular Outerwear Device）

**代码**: `code/modules/mod/`（29 文件 13,976 行）——**穿戴式高科技动力装甲**

## 8.1 核心机制

| 机制 | 数值 |
|---|---|
| **电量** | 标准电池 10,000 J；核心=电池电量；0 电自动关机 |
| **常驻耗电** | **50 J/秒**（标准电池约 200 秒） |
| 故障额外耗电 | 0.2-4× 常驻（平均 3×） |
| **密封** | 激活时四部件（头盔/胸甲/手套/靴子）逐件密封，每件 1 秒 |
| **复杂度** | 每模块占用，总和 ≤ **15**（默认，主题可增减） |
| **EMP** | 解除模块+5/severity 灼伤+10% 尖叫 |
| **MODlink** | 套装间全息通话（NT/SYND/CHRL/CC 四频率）+0.25×/s |
| **涂装** | 颜色矩阵 RGB 0.25-2.0/单色 ≤1.25/总和 1.5-4.0 |

## 8.2 核心类型（5 种）

| 核心 | 电量 |
|---|---|
| 标准核心 | 内置电池（可换） |
| **无限核心** | 无限（Fixium 聚变） |
| 空灵核心 | 空灵族生物电量 |
| **等离子核心** | 等离子花（拉瓦兰） |
| 灵魂核心 | 灵魂（法师） |

## 8.3 主题（38 种全录）

**代码**: `mod/mod_types.dm` + `mod/mod_theme.dm` + NOVA 覆写——复杂度上限默认 15，减速默认 0.75

| 主题 | 复杂度上限 | 减速 | 说明 |
|---|---|---|---|
| **行政 Administrative** | **1000** | 0 | 管理员矿制成 |
| **高级 Advanced** | 15 | 0.5 | 高级型 |
| **秘典 Apocryphal** | 15 | 0.75 | 神秘学 |
| **大气 Atmospheric** | 15 | 1 | 大气工程 |
| **护卫 Praetorian** | 15 | 0.5 | 蓝盾专用 |
| **计时 Chrono** | 15 | 0 | 时间穿越 |
| **民用 Civilian** | 15 | 0.75 | 基础民用 |
| **企业 Corporate** | 15 | 0 | 公司 |
| **宇宙小丑 Cosmohonk** | 15 | 1.25 | 太空小丑 |
| **调试 Debug** | **50** | 0 | 管理员调试 |
| **精英 Elite** | 15 | 0 | 赛博阳升级，高护甲 |
| **上将 Admiral** | 15 | 0.75 | 精英变体 |
| **附魔 Enchanted** | 15 | 0.75 | 魔法 |
| **工程 Engineering** | 15 | 1 | 耐热抗电 |
| **前沿殖民者 Frontier** | 15 | 0 | 边境防护 |
| **故障 Glitch** | 15 | 0 | 电子故障 |
| **渗透者 Infiltrator** | 15 | 0 | 潜入 |
| **Interdyne** | 15 | 0.75 | 制药公司 |
| **装载者 Loader** | 15 | 0.75 | 工程装载 |
| **富豪 Magnate** | 15 | 0.25 | 富豪 |
| **陆战队员 Marine** | 15 | 0 | 高强度战斗 |
| **医疗 Medical** | 15 | 0.5 | 医疗 |
| **采矿 Mining** | 15 | 0.75 | 灰甲+球体 |
| **忍者 Ninja** | 15 | 0 | 蜘蛛氏族刺客 |
| **便携 Portable** | 15 | 0 | 便携式 |
| **原型 Prototype** | 15 | 1 | 原型 |
| **搬运工 Hauler** | 15 | 1 | 原型变体 |
| **救援 Rescue** | 15 | 0.25 | 救援 |
| **科研 Research** | 15 | 1.25 | 科研 |
| **守誓者 Responsory** | 15 | 0 | 圣武士 |
| **暗黑圣骑士 Dark Paladin** | 15 | 0.75 | 守誓者变体 |
| **守卫 Safeguard** | 15 | 0.75 | 保护 |
| **安保 Security** | 15 | 0.75 | 安保 |
| **辛迪加 Syndicate** | 15 | 0 | 非法护甲（核弹队） |
| **深空 Deepspace** | 15 | 0.75 | 辛迪加变体 |
| **塔尔孔 Tarkon** | 15 | 0.75 | NOVA 飞船族 |
| **沃斯科德 Voskhod** | 15 | 0.45 | NOVA 扩展 |

> **NOVA 主题**：塔尔孔/沃斯科德/深空/前沿殖民者/富豪（Nova 覆写新增）。

## 8.4 MOD 模块（132 种全录）

**代码**: `mod/modules/`（7,382 行）——**87 类 132 个模块**（完整双语表见第八卷B）：

| 类别 | 模块数 | 代表模块 |
|---|---|---|
| **面甲类** | 8 | 夜视/热成像/T射线/状态读数 |
| **异常锁类** | 7 | 念动力/传送器/时间停止/时间线跳跃 |
| **储物类** | 6 | 次元储物/整理器/矿石袋 |
| **隐身类** | 3 | 变色龙/静音 |
| **焊接类** | 3 | 焊接/管道连接 |
| **喷雾类** | 3 | 胡椒肩/清洁喷雾 |
| **EMP 盾类** | 3 | EMP 护盾 |
| **分发类** | 3 | 试剂分发/纸分发 |
| **回收类** | 3 | 回收器 |
| **能量盾类** | 2 | 能量护盾/战法师盾 |
| **反魔法类** | 2 | 魔法湮灭器/中和器 |
| **磁靴/喷射/手电/束带/气球/搬运/抓捕/除颤/扫描/格挡/夹钳** | 各 2 | — |
| **单模块类** | 50+ | 动力踢/医疗光束/武器召回/能量网/探路/徽章/防滑/火焰喷射器/黑客/扩音器/GPS/钻头/液压/磁铁/时间停止等 |

> 模块类型：**被动**（装上生效）/ **使用**（点击触发）/ **开关**（开启持续耗电）/ **主动**（同时一个）。

## 8.5 MOD 服 vs 普通护甲

| 对比 | MOD 服 | 普通护甲 |
|---|---|---|
| 标准主题护甲 | 10/5/5/5/0/100/25/25/5 | 太空服仅 bio 100 |
| **辛迪加 MOD** | **50/50/40/50/40/100/50/90/25** | 防弹衣 15/60/10/10/40 |
| **精英 MOD** | **60/60/50/50/55/100/100/100/25** | 重甲 80/80/50/50/100 |
| 功能 | 132 模块全能 | 纯防护 |
| 缺点 | 电量限制（200s）/复杂度上限 | 无电量但无功能 |

> **结论**：MOD 服 = 功能全能但受电量约束；普通护甲 = 纯防护无限制。精英 MOD 接近重甲且带功能。


---

# 第八卷B · MOD 模块全量总表（132 种）

| 名称 | 类型 | 复杂度 | 冷却 | 功能 |
|---|---|---|---|---|
| **模块 主动 声纳**（MOD active sonar） | MODULE_USABLE | 2 | 15s | Ancient tech from the 20th century, this modu |
| **模块 肾上腺素 强化**（MOD adrenaline boost module） | MODULE_USABLE |  | 12s | The secrets of the Spider Clan are many. The  |
| **模块 异常 锁定**（MOD anomaly locked module） |  |  | s | A form of a module, locked behind an anomalou |
| **模块 反重力**（MOD anti-gravity module） | MODULE_TOGGLE | 2 | s | A module that uses a gravitational core to ma |
| **模块 念动力**（MOD kinesis module） | MODULE_ACTIVE | 3 | 0s | A modular plug-in to the forearm, this module |
| **模块 念动力++**（MOD kinesis++ module） |  | 0 | s | A modular plug-in to the forearm, this module |
| **模块 念动力+**（MOD kinesis+ module） |  | 0 | s | A modular plug-in to the forearm, this module |
| **模块 原型 念动力**（MOD prototype kinesis module） |  | 0 | s |  |
| **模块 传送器**（MOD teleporter module） | MODULE_ACTIVE | 3 | 4s | A module that uses a bluespace core to let th |
| **模块 魔法 湮灭器**（MOD magic nullifier module） |  |  | s | A series of obsidian rods installed into crit |
| **模块 魔法 中和器**（MOD magic neutralizer module） |  |  | s | The caster wielding this spell gains an invis |
| **模块 灰烬 增生**（MOD ash accretion module） |  |  | s | A module that collects ash from the terrain,  |
| **模块 恶行**（MOD atrocinator module） | MODULE_TOGGLE | 2 | s | A mysterious orb that has mysterious effects  |
| **模块 气球 blower**（MOD balloon blower module） | MODULE_USABLE | 1 | 15s | A strange module invented years ago by some i |
| **模块 advanced 气球 blower**（MOD advanced balloon blower module） |  |  | 20s | A relatively new piece of technology develope |
| **模块 bike horn**（MOD bike horn module） | MODULE_USABLE | 1 | 1s | A shoulder-mounted piece of heavy sonic artil |
| **模块 变色龙**（MOD chameleon module） | MODULE_USABLE | 2 | 0s | A module using chameleon technology to disgui |
| **模块 液压 夹钳**（MOD hydraulic clamp module） | MODULE_ACTIVE | 3 | 0s | A series of actuators installed into both arm |
| **模块 loader 液压 夹钳**（MOD loader hydraulic clamp module） |  | 0 | s |  |
| **模块 建造器**（MOD constructor module） | MODULE_USABLE | 2 | 11s | This module entirely occupies the wearer's fo |
| **模块 罪犯 抓捕**（MOD criminal capture module） | MODULE_ACTIVE | 2 | 0s | The private security that had orders to take  |
| **模块 patient transport**（MOD patient transport module） |  |  | s | A module built into the forearm of the suit.  |
| **模块 除颤器**（MOD defibrillator module） | MODULE_ACTIVE | 2 | 0s | A module built into the gauntlets of the suit |
| **模块 combat 除颤器**（MOD combat defibrillator module） | MODULE_ACTIVE | 1 | s | A module built into the gauntlets of the suit |
| **模块 灵能回响 士气打击**（MOD psi-echo demoralizer module） |  | 0 | s | One incredibly morbid member of the RND team  |
| **模块 burger 分发器**（MOD burger dispenser module） | MODULE_USABLE | 3 | 5s | A rare piece of technology reverse-engineered |
| **模块 mirage grenade 分发器**（MOD mirage grenade dispenser module） |  |  | 20s | This module can create mirage grenades at the |
| **模块 ninja star 分发器**（MOD ninja star dispenser module） |  |  | 0s | This piece of Spider Clan technology can expl |
| **模块 管道 selector**（MOD disposal selector module） |  | 2 | s | A module that connects to the disposal pipeli |
| **模块 DNA 锁**（MOD DNA lock module） | MODULE_USABLE | 1 | 0s | A module which engages with the various locks |
| **模块 reinforced DNA 锁**（MOD reinforced DNA lock module） |  |  | s | A module which engages with the various locks |
| **模块 钻头**（MOD drill module） | MODULE_ACTIVE | 1 | 0s | An arm-mounted drill, typically extending ove |
| **模块 EMP 护盾**（MOD EMP shield module） |  | 1 | s | A field inhibitor installed into the suit, pr |
| **模块 advanced EMP 护盾**（MOD advanced EMP shield module） |  | 2 | s | An advanced field inhibitor installed into th |
| **模块 EMP pulse**（MOD EMP pulse module） | MODULE_USABLE |  | 8s | This module is normally set to activate on dr |
| **模块 能量 网**（MOD energy net module） | MODULE_ACTIVE |  | 5s | A custom-built net-thrower. While conventiona |
| **模块 能量 护盾**（MOD energy shield module） |  | 3 | s | A personal, protective forcefield typically s |
| **模块 战法师 护盾**（MOD battlemage shield module） |  |  | s | The caster wielding this spell gains a visibl |
| **模块 根除 锁**（MOD eradication lock module） | MODULE_USABLE |  | 0s | A module which remembers the original owner o |
| **模块 钓鱼 手套**（MOD fishing glove module） |  | 1 | s | A MOD module that takes in an external fishin |
| **模块 火焰喷射器**（MOD flamethrower module） | MODULE_ACTIVE | 3 | 2s | A custom-manufactured flamethrower, used to b |
| **模块 手电**（MOD flashlight module） | MODULE_TOGGLE | 1 | s | A simple pair of configurable flashlights ins |
| **模块 flashdark**（MOD flashdark module） |  |  | s | A quirky pair of configurable flashdarks inst |
| **模块 internal GPS**（MOD internal GPS module） | MODULE_USABLE | 1 | 0s | This module uses common Nanotrasen technology |
| **模块 黑客**（MOD hacker module） |  |  | s | Built for one purpose, electronic warfare, th |
| **模块 帽子 稳定器**（MOD hat stabilizer module） |  |  | s | A simple set of deployable stands, directly a |
| **模块 elite 帽子 稳定器**（MOD elite hat stabilizer module） |  | 0 | s | A simple set of deployable stands, directly a |
| **模块 safety-first head 防护**（MOD safety-first head protection module） |  | 1 | s | A series of dampening plates are installed al |
| **模块 健康 分析器**（MOD health analyzer module） | MODULE_ACTIVE | 1 | 0s | A module installed into the glove of the suit |
| **模块 枪套**（MOD holster module） | MODULE_USABLE | 2 | 0s | Based off typical storage compartments, this  |
| **模块 loader 液压 arms**（MOD loader hydraulic arms module） | MODULE_ACTIVE |  | 4s | A pair of powerful hydraulic arms installed i |
| **模块 渗透核心程序**（MOD infiltration core programs module） |  | 0 | s | The primary stealth systems operating within  |
| **模块 注射器**（MOD injector module） | MODULE_ACTIVE | 1 | 0s | A module installed into the wrist of the suit |
| **模块 徽章**（MOD insignia module） |  |  | s | Despite the existence of IFF systems, radio c |
| **模块 ion 喷射背包**（MOD ion jetpack module） | MODULE_TOGGLE | 3 | s | A series of electric thrusters installed acro |
| **模块 advanced ion 喷射背包**（MOD advanced ion jetpack module） |  |  | s | An improvement on the previous model of elect |
| **模块 关节 扭转 ratchet**（MOD joint torsion ratchet module） |  | 1 | s | A compact, weak AC generator that charges the |
| **模块 ionic 跳跃 喷气**（MOD ionic jump jet module） | MODULE_USABLE | 3 | 30s | A specialised ionic thruster which provides a |
| **模块 缓落**（MOD longfall module） |  | 1 | s | Useful for protecting both the suit and the w |
| **模块 磁性 stability**（MOD magnetic stability module） | MODULE_TOGGLE | 2 | s | These are powerful electromagnets fitted into |
| **模块 advanced 磁性 stability**（MOD advanced magnetic stability module） |  | 0 | s |  |
| **模块 loader 液压 磁铁**（MOD loader hydraulic magnet module） | MODULE_ACTIVE |  | 1s | A powerful hydraulic electromagnet able to la |
| **模块 磁性 束缚**（MOD magnetic harness module） |  | 2 | s | Based off old TerraGov harness kits, this mag |
| **模块 医疗 光束枪**（MOD medical beamgun module） | MODULE_ACTIVE | 1 | 0s | A wrist mounted variant of the medbeam gun, a |
| **模块 扩音器**（MOD megaphone module） | MODULE_TOGGLE | 1 | s | A microchip megaphone linked to a MODsuit, fo |
| **模块 微波 光束**（MOD microwave beam module） | MODULE_ACTIVE | 1 | 4s | An oddly domestic device, this module is inst |
| **模块 water 喷雾器**（MOD water mister module） | MODULE_ACTIVE | 2 | 0s | A module containing a mister, able to spray i |
| **模块 resin 喷雾器**（MOD resin mister module） |  |  | s | An atmospheric resin mister, able to fix up a |
| **模块 janitorial 喷雾器**（MOD janitorial mister module） |  |  | s | A space cleaner mister, able to clean up mess |
| **模块 eating apparatus**（MOD eating apparatus module） |  | 1 | s | A favorite by Miners, this modification to th |
| **模块 防滑**（MOD anti slip module） |  | 1 | s | These are a modified variant of standard magn |
| **模块 ore bag**（MOD ore bag module） | MODULE_USABLE | 1 | 0s | An integrated ore storage system installed in |
| **模块 整理器**（MOD organizer module） | MODULE_ACTIVE | 2 | 0s | A device recovered from a crashed Interdyne P |
| **模块 纸 分发器**（MOD paper dispenser module） | MODULE_USABLE | 1 | 5s | A simple module designed by the bureaucrats o |
| **模块 寻路**（MOD pathfinder module） | MODULE_USABLE | 1 | s | This module, brought to you by Nakamura Engin |
| **模块 胡椒 肩部**（MOD pepper shoulders module） | MODULE_USABLE | 1 | 5s | A module that attaches two pepper sprayers on |
| **模块 等离子 稳定器**（MOD plasma stabilizer module） |  | 1 | s | This system essentially forms an atmosphere o |
| **模块 板甲 压缩**（MOD plate compression module） |  | 2 | s | A module that keeps the suit in a very tightl |
| **模块 动力踢**（MOD power kick module） | MODULE_ACTIVE |  | 5s | This module uses high-power myomer to generat |
| **模块 弹道 阻尼器**（MOD projectile dampener module） | MODULE_TOGGLE | 3 | 1s | Using technology from peaceborgs, this module |
| **模块 快速 搬运**（MOD quick carry module） |  | 1 | s | A suite of advanced servos, redirecting power |
| **模块 advanced 快速 搬运**（MOD advanced quick carry module） |  | 0 | s |  |
| **模块 restraint assist**（MOD restraint assist module） |  | 0 | s | Enhanced gauntlet grip pads that help with pl |
| **模块 radiation 防护**（MOD radiation protection module） |  | 2 | s | A module utilizing polymers and reflective sh |
| **模块 试剂 扫描**（MOD reagent scanner module） | MODULE_TOGGLE | 1 | s | A module based off research-oriented Nanotras |
| **模块 advanced 试剂 扫描**（MOD advanced reagent scanner module） |  | 0 | s | An advanced module with all the features of r |
| **模块 回收器**（MOD recycler module） | MODULE_ACTIVE | 2 | s | An innovative garbage collection module that  |
| **模块 riot foam dart 回收器**（MOD riot foam dart recycler module） |  |  | s | A mod module collects and repackages fired fo |
| **模块 foam dart 回收器**（MOD foam dart recycler module） |  | 1 | s | A mod module that collects and repackages fir |
| **模块 倒带器**（MOD rewinder module） | MODULE_USABLE |  | 20s | A module that can pull the user back through  |
| **模块 perfumer**（MOD perfumer module） | MODULE_USABLE | 1 | 10s | A small spray to clean oneself up. Has a plea |
| **模块 电击 absorption**（MOD shock absorption module） |  | 1 | s | A module that makes the user resistant to the |
| **模块 射击 助手**（MOD shooting assistant module） | MODULE_PASSIVE | 3 | s | A botched prototype meant to boost the TGMC c |
| **模块 bulwark**（MOD bulwark module） |  | 3 | s | Layers upon layers of shock dampening plates, |
| **模块 黏合堡垒**（superglued MOD bulwark module） |  | 0 | s | Layers upon layers of shock dampening plates, |
| **模块 手套 translator**（MOD glove translator module） |  | 1 | s | A module that adds motion sensors into the su |
| **模块 球体 变形**（MOD sphere transform module） | MODULE_ACTIVE |  | 1s | A module able to move the suit's parts around |
| **模块 弹簧锁**（MOD springlock module） |  | 3 | s | A module that spans the entire size of the MO |
| **模块 stamper**（MOD stamper module） | MODULE_ACTIVE | 1 | 0s | A module installed into the wrist of the suit |
| **模块 状态 读数**（MOD status readout module） |  | 1 | s | A once-common module, this technology unfortu |
| **模块 Spider Clan 状态 读数**（MOD Spider Clan status readout module） |  |  | s | A once-common module, this technology unfortu |
| **模块 原型 cloaking**（MOD prototype cloaking module） | MODULE_TOGGLE | 4 | 5s | A complete retrofitting of the suit, this is  |
| **模块 advanced cloaking**（MOD advanced cloaking module） |  |  | 3s | The latest in stealth technology, this module |
| **模块 Wraith Cloaking **（MOD Wraith Cloaking Module） | MODULE_ACTIVE |  | 2s | A more destructive adaptation of the stealth  |
| **模块 compact 储物**（MOD compact storage module） |  | 1 | s | What amounts to a series of integrated storag |
| **模块 case 储物**（MOD case storage module） |  | 0 | s | Some concessions had to be made when creating |
| **模块 bluespace 储物**（MOD bluespace storage module） |  | 3 | s | A storage system developed by Nanotrasen, the |
| **模块 储物 of 次元储物**（MOD storage module of holding） |  | 4 | s | A prototype storage module utilizing the powe |
| **模块 储物**（MOD storage module） |  | 3 | s | Reverse engineered by Nakamura Engineering fr |
| **模块 syndicate 储物**（MOD syndicate storage module） |  | 3 | s | A storage system using nanotechnology develop |
| **模块 手术 处理器**（MOD surgical processor module） | MODULE_ACTIVE | 2 | 0s | A module using an onboard surgical computer w |
| **模块 t-ray scan**（MOD t-ray scan module） | MODULE_TOGGLE | 1 | s | A module installed into the visor of the suit |
| **模块 tanning**（MOD tanning module） | MODULE_USABLE | 1 | 30s | A tanning module for modular suits. Skin canc |
| **模块 timestream 根除**（MOD timestream eradication module） | MODULE_ACTIVE |  | 0s | The correction device of a fourth dimensional |
| **模块 应急 系绳**（MOD emergency tether module） | MODULE_ACTIVE | 2 | 1s | A custom-built grappling-hook powered by a wi |
| **模块 温度 调节器**（MOD thermal regulator module） | MODULE_TOGGLE | 1 | s | Advanced climate control, using an inner body |
| **模块 线 撕裂器**（MOD thread ripper module） | MODULE_ACTIVE | 2 | 1s | A custom-built module integrated with the sui |
| **模块 时间线 跳跃器**（MOD timeline jumper module） | MODULE_USABLE |  | 5s | A module used to traverse timelines, phasing  |
| **模块 时间停止**（MOD timestopper module） | MODULE_USABLE |  | 60s | A module that can halt time in a small radius |
| **模块 面甲**（MOD visor module） | MODULE_TOGGLE | 1 | s | A heads-up display installed into the visor o |
| **模块 diagnostic 面甲**（MOD diagnostic visor module） |  |  | s | A heads-up display installed into the visor o |
| **模块 医疗 面甲**（MOD medical visor module） |  |  | s | A heads-up display installed into the visor o |
| **模块 meson 面甲**（MOD meson visor module） |  |  | s | A heads-up display installed into the visor o |
| **模块 夜视 面甲**（MOD night visor module） |  |  | s | A heads-up display installed into the visor o |
| **模块 rave 面甲**（MOD rave visor module） |  | 1 | s | A Super Cool Awesome Visor (SCAV), intended f |
| **模块 security 面甲**（MOD security visor module） |  |  | s | A heads-up display installed into the visor o |
| **模块 温度 面甲**（MOD thermal visor module） |  |  | s | A heads-up display installed into the visor o |
| **模块 摇摆**（MOD waddle module） |  | 1 | s | Some of the most primitive technology in use  |
| **模块 武器 召回**（MOD weapon recall module） | MODULE_USABLE |  | 0s | The cornerstone of a clanmember's life as a b |
| **模块 焊接 防护**（MOD welding protection module） |  | 1 | s | A module installed into the visor of the suit |
| **模块 摄像头 视觉**（MOD camera vision module） |  | 0 | s | A module installed into the suit's helmet. Th |
| **模块 防闪光光学套件**（MODsuit flash-protected optical suite） |  | 0 | s |  |

# 第九卷 · 变色龙伪装系统

**代码**: `clothing/chameleon/`（1,464 行）

| 机制 | 值 |
|---|---|
| **伪装** | 可变成任何衣物外观（腕表拨盘切换） |
| **护甲** | melee/bullet/laser 10 + fire/acid 50 |
| **EMP 破坏** | EMP→BREAK_CHAMELEON_ACTION（永久锁定外观） |
| 传感器 | SENSOR_OFF（"辛迪加穿梭机上这是谁？"） |
| 组件 | 变色龙枪/扫描器/无人机/套装 |

> **变色龙护甲**（suit_chameleon）：10 三防+火酸 50——伪装为主，防护为辅。

---
# 第十卷 · NOVA 外观与装甲（2026-08-06 补全）

> **源码**: `modular_nova/modules/` GAGS/jaeger_mod/specialist_armor/modsuit_overrides/clothing_improvements/holdingfashion_port/dogfashion。此前文档 TG 侧全量但 NOVA 层缺失，现补全。

## 10.1 GAGS 染色系统（7,038 行）

**源码**: `modular_nova/modules/GAGS/`（greyscale_configs.dm 3,205 行 + nsfw/ 520 行 + 552 个 JSON）。TG 核心 GAGS 系统（`code/datums/greyscale/` 614 行）= JSON 配置 + DMI 素材 + 配置 datum 三要素。

- **676 个配置**（237 独立基类 + 439 个 Worn/Digi/Vox/Teshari/Snouted/Muzzled 变体）
- 玩家用**喷漆罐（spraycan）** + `gags_recolorable` 元素弹出染色 UI 重着色
- 物品通过 `greyscale_colors`（十六进制色串）与 `IS_PLAYER_COLORABLE_1` 标记启用

**可染色部位**：头（贝雷帽/牛仔帽/蘑菇帽/军帽/花针/兜帽/猫耳耳机/蝴蝶结）、面具（小丑/呼吸器/无菌/化装舞会/护颈/防毒面具）、眼（蒙眼布）、脖（披风/斗篷/肩衣/项圈/围巾/泰莎瑞斗篷）、外套（围裙/法兰绒/浴袍/正装/铅笔裙/海军制服/连帽衫/毛衣/大衣/皮夹克/风衣/飞行员夹克/和服等）、内层（连体服/裤装/裙装/礼服/旗袍/水手服/女仆装/毕业礼服）、鞋（靴/牛仔靴/运动鞋/高跟鞋/凉鞋）、手（晚宴手套/设计师手套/布裹手）、腰带（大腿枪套）、ID 卡、勋章（8 种+勋条）、PDA（14 种外型）、NRI 军余装备、部门护甲（depgag 系列 28 种）

**NSFW 染色**（GAGS/nsfw/ 27 个配置）：项圈（细/厚/皮革）、口球/宿舍面具/剥夺头盔/支配帽/口枷（shibari）/乳胶袜/芭蕾跟/捆缚架等

## 10.2 Jaeger 装甲（jaeger_mod 108 行）

`/datum/mod_theme/jaeger_med` "modular infantry"——中型步兵型 MOD 动力外骨骼，**非太空防护**：

| 属性 | 值 |
|---|---|
| 护甲 | melee 40/bullet 50/laser 30/energy 40/bomb 40/bio 50/fire 50/acid 60/wound 20 |
| 导电 | seimens 0.25 |
| 复杂上限 | −3；部署减速 0.25 |
| 内置模块 | `/obj/item/mod/module/jaeger_sprint`（腿部伺服超频冲刺，主动耗电 2×、加速 −0.25 减速，不可卸） |
| 皮肤 | 默认 "infantry" |
| 预配 | jaeger_med 控制核心（超级电池+大容量储物/磁力挂架/快速手铐） |
| 获取 | 军械库剩余品补给箱（稀有权重） |

## 10.3 专家装甲 3 系（specialist_armor）

数值映射：TINY=10/WEAK=30/MID=50/INSANE=90，WOUND WEAK=10/HIGH=30。

| 系 | 装备 | 护甲 | 特色机制 |
|---|---|---|---|
| **硬化系** | 'Muur' 硬化背心+封闭头盔（EMT 变体 'Archangel'） | melee 30/bullet 50/laser 30/energy 10/bomb 30/fire 50/acid 30/wound 10 | **弹道穿甲归零**（COMSIG_PROJECTILE_PREHIT 信号）；头盔动态遮挡；耐火 |
| **维和系** | 'Touvou' 维和背心 + 'Kastrol' 头盔（民用版） | melee 30/bullet 50/laser 10/energy 10/bomb 30/fire 50/acid 30/wound 30 | 弹伤中等但**高伤口抗性**；激光直穿弱点 |
| **牺牲系** | 'Val' 牺牲弹道背心+头盔 | bullet **90（INSANE）**、wound 30、bomb 50/fire 50 | **自我牺牲机制**：`clothing_damaged_by_bullets` 组件，被弹击按 1×弹伤自损（抗弱护甲弹 ×0.5），不可修复，max_integrity 200；头盔面甲（损坏>20 碎裂） |

三系均可通过**补给箱**获取（各 3 套，安全部门军械库，5× 箱价）。

## 10.4 其余 NOVA 服装模块

| 模块 | 行数 | 内容 |
|---|---|---|
| **modsuit_overrides** | 225+7 | 17 个 MOD 主题护甲值重定义；卸载 security/safeguard 胡椒肩模块 |
| **clothing_improvements** | 294 | 功能性切换（Ctrl+Shift+左键外套↔脖子两穿）；织带 3 件套（可重涂）；飞行员储物挂具（2 皮肤）；枪套右击定制加装（1.5 秒）；小背包（腰包/胸包/储物腰带） |
| **holdingfashion_port** | 102 | 体内存亡包/行李袋（惰性体+蓝空异常核心合成；金×2/钻石/蓝空/铀材料） |
| **dogfashion** | 12 | 狗头装扮：Yankee 说唱犬（MC %REAL_NAME%）、黄色耻辱圆锥 |


# 附录 · 代码路径索引

| 系统 | 文件 | 行数 |
|---|---|---|
| 防具定义 | `code/__DEFINES/~nova_defines/armor_defines.dm` + `code/datums/armor/` | 7+ |
| 护甲机制 | `code/modules/mob/living/carbon/human/human_defense.dm` | 766 |
| 制服配件 | `clothing/under/accessories`（NOVA 织带） | — |
| 外套 | `code/modules/clothing/suits/` | 5,045 |
| 制服 | `code/modules/clothing/under/` | 4,887 |
| 头饰 | `code/modules/clothing/head/` | 4,459 |
| 面具 | `code/modules/clothing/masks/` | 1,996 |
| 鞋类 | `code/modules/clothing/shoes/` | 1,772 |
| 眼镜 | `code/modules/clothing/glasses/` | 1,413 |
| 手套 | `code/modules/clothing/gloves/` | 1,105 |
| 脖子 | `code/modules/clothing/neck/` | 857 |
| 腰带 | `code/modules/clothing/belts/` | 201 |
| 耳朵 | `code/modules/clothing/ears/` | 36 |
| 太空服 | `code/modules/clothing/spacesuits/` | 1,383 |
| 变色龙 | `code/modules/clothing/chameleon/` | 1,464 |
| 套装 | `code/modules/clothing/outfits/` | 1,703 |
| **MOD 服** | `code/modules/mod/` | **13,976** |
# 附录 · 代码路径索引

| 系统 | 文件 | 行数 |
|---|---|---|
| 防具定义 | `code/__DEFINES/~nova_defines/armor_defines.dm` + `code/datums/armor/` | 7+ |
| 护甲机制 | `code/modules/mob/living/carbon/human/human_defense.dm` | 766 |
| 制服配件 | `clothing/under/accessories`（NOVA 织带） | — |
| 外套 | `code/modules/clothing/suits/` | 5,045 |
| 制服 | `code/modules/clothing/under/` | 4,887 |
| 头饰 | `code/modules/clothing/head/` | 4,459 |
| 面具 | `code/modules/clothing/masks/` | 1,996 |
| 鞋类 | `code/modules/clothing/shoes/` | 1,772 |
| 眼镜 | `code/modules/clothing/glasses/` | 1,413 |
| 手套 | `code/modules/clothing/gloves/` | 1,105 |
| 脖子 | `code/modules/clothing/neck/` | 857 |
| 腰带 | `code/modules/clothing/belts/` | 201 |
| 耳朵 | `code/modules/clothing/ears/` | 36 |
| 太空服 | `code/modules/clothing/spacesuits/` | 1,383 |
| 变色龙 | `code/modules/clothing/chameleon/` | 1,464 |
| 套装 | `code/modules/clothing/outfits/` | 1,703 |
| **MOD 服** | `code/modules/mod/` | **13,976** |
