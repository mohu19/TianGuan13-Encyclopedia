# 天关（TianGuan13）玩法目录与代码出处索引

> **基础路径**: `C:\Users\33922\Desktop\Hermes\TianGuan13\TianGuan13-master\`
> **用途**: 为后续编写教程提供代码检索入口，每个玩法标注其 DM 源文件位置

---

## 零、代码组织总览

| 层级 | 路径 | 说明 |
|---|---|---|
| 核心游戏 | `code/` | 根目录：`__DEFINES/` `__HELPERS/` `_globalvars/` `controllers/` `datums/` `game/` `modules/` |
| 副作用 | `html/` | 浏览器界面 HTML |
| 图标 | `icons/` | .dmi 格式物体/角色/UI 图标 |
| 音效 | `sound/` | .ogg 格式音效/音乐 |
| 地图 | `_maps/` | 空间站 JSON 描述 + .dmm 地图文件 |
| 前端 UI | `tgui/` | TypeScript 浏览器界面 (React) |
| Nova 扩展 | `modular_nova/` | Nova Sector 下游模块 |
| **天关专属** | `modular_tianguan/` | 本 fork 独有改动 |
| Z121 扩展 | `modular_z121/` | 另一个贡献者模块 |
| 配置 | `config/` | 服务器配置策略 |

---

## 一、玩家起始定制

### 1.1 角色创建/外观定制

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 发型/面毛 | `code/datums/sprite_accessories/hair.dm`, `facial_hair.dm` | 角色发型选择 |
| 尾巴/翅膀/角/鳍 | `code/datums/sprite_accessories/tails.dm`, `wings.dm`, `horns.dm`, `frills.dm` | 非人种族装饰 |
| 耳朵/鼻子/脊柱/花纹 | `code/datums/sprite_accessories/ears.dm`, `snouts.dm`, `spines.dm`, `markings.dm` | 面部特征/身体花纹 |
| 蛾天线 | `code/datums/sprite_accessories/moth_antennae.dm` | 蛾人专属天线 |
| 服装贴图 | `code/datums/sprite_accessories/clothing.dm` | 服装外观定义 |

### 1.2 开局特质 (Quirks)

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 特质系统核心 | `code/datums/quirks/_quirk.dm` | 特质框架定义 |
| 正面特质 | `code/datums/quirks/positive_quirks/` | 酒精耐受、共情、多语种等 |
| 中性特质 | `code/datums/quirks/neutral_quirks/` | 素食、色盲、摄影师等 |
| 负面特质 | `code/datums/quirks/negative_quirks/` | 失明、癫痫、截肢、口吃等 |

各特质文件：
```
positive: alcohol_tolerance, bilingual, empath, freerunning, jolly, musician, spacer, voracious ...
neutral:  bald, vegetarian, phobia, photographer, pineapple_hater/liker, shifty_eyes ...
negative: blind, deaf, mute, paraplegic, quadriplegic_amputee, narcolepsy, social_anxiety, hemiplegic ...
```

### 1.3 种族选择 (Species)

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 种族系统核心 | `code/modules/mob/living/carbon/human/species_types/` | 各物种定义文件 |

具体物种：
```
humans.dm          - 基准人类
lizardpeople.dm    - 蜥蜴人
mothmen.dm         - 蛾人
plasmamen.dm       - 等离子人 (需太空服)
flypeople.dm       - 蝇人
jellypeople.dm     - 果冻人
skeletons.dm       - 骷髅
podpeople.dm       - 植物人
ethereal.dm        - 以太体 (能量生命)
felinid.dm         - 猫人
shadowpeople.dm    - 暗影人
abductors.dm       - 外星掳掠者
android.dm         - 安卓
dullahan.dm        - 无头骑士
golems.dm          - 魔像
mushpeople.dm      - 蘑菇人
snail.dm           - 蜗牛人
vampire.dm         - 吸血鬼
zombies.dm         - 丧尸
```
Nova 新增: `modular_nova/modules/voxes/`, `modular_nova/modules/teshari/`, `modular_nova/modules/shadekin/`

---

## 二、职业系统

### 2.1 职业定义

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 职业框架 | `code/modules/jobs/jobs.dm`, `_job.dm` | 基础 job datum |
| 部门定义 | `code/modules/jobs/departments/` | 部门归属关系 |
| 权限系统 | `code/modules/jobs/access.dm` | ID 卡/门禁权限 |

### 2.2 各职位定义 (code/modules/jobs/job_types/)

**指挥层**:
```
captain.dm           - 舰长
head_of_personnel.dm - 人事部长
head_of_security.dm  - 安全部长
chief_engineer.dm    - 总工程师
chief_medical_officer.dm - 医疗总监
research_director.dm - 科研总监
```

**工程部**:
```
station_engineer.dm  - 站台工程师
atmospheric_technician.dm - 大气技师 (modular_nova)
```

**医疗部**:
```
medical_doctor.dm    - 医生
paramedic.dm         - 救护员
chemist.dm           - 药剂师
geneticist.dm        - 基因学家
coroner.dm           - 法医
psychologist.dm      - 心理医生
```

**科研部**:
```
scientist.dm         - 科学家
roboticist.dm        - 机器人专家
```

**安保部**:
```
security_officer.dm  - 安全官
detective.dm         - 侦探
warden               - 典狱长 (modular_nova)
lawyer.dm            - 律师
```

**后勤部**:
```
quartermaster.dm     - 军需官
cargo_technician.dm  - 货运技工
shaft_miner.dm       - 矿工
cargo_gorilla.dm     - 货运猩猩 (modular_nova)
```

**服务部**:
```
assistant/           - 助手
bartender.dm         - 酒保
botanist.dm          - 植物学家
cook.dm              - 厨师
janitor.dm           - 清洁工
chaplain/            - 牧师
curator.dm           - 馆长
clown.dm             - 小丑
mime.dm              - 默剧
```

**特殊**:
```
ai.dm                - AI 核心
cyborg.dm            - 机器人
prisoner.dm          - 囚犯
```

### 2.3 职业配装 (Outfits)

| 玩法 | 代码路径 |
|---|---|
| 初始装备 | 各 `job_types/*.dm` 中的 `/datum/outfit/job/` 定义 |
| ID 卡 | `code/datums/id_trim/` |

---

## 三、反派系统 (Antagonists)

### 3.1 框架与目标系统

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 目标系统 | `code/game/gamemodes/objective.dm` | 反派任务目标 |
| 目标物品 | `code/game/gamemodes/objective_items.dm` | 目标相关特殊物品 |
| 事件系统 | `code/game/gamemodes/events.dm` | 回合随机事件 |
| 反向通用框架 | `code/modules/antagonists/_common/` | 共享反向逻辑 |

### 3.2 各反向定义

| 反向 | 代码路径 | 核心机制 |
|---|---|---|
| **Traitor** 叛徒 | `code/modules/antagonists/traitor/` | Uplink 购买, 刺杀/破坏目标 |
| **Changeling** 变形怪 | `code/modules/antagonists/changeling/` | DNA 吸收 → 变形, 化学分泌物, `dna_max=6` |
| **Nuclear Operative** 核弹特工 | `code/modules/antagonists/nukeop/` | 5 人小队, 核弹摧毁空间站 |
| **Cult** 邪教 | `code/modules/antagonists/cult/` | 符文/献祭/召唤 Narsie |
| **Wizard** 巫师 | `code/modules/antagonists/wizard/` | 法术书/火球/传送/召唤学徒, `suicide_cry = "FOR THE FEDERATION!!"` |
| **Blob** 菌母 | `code/modules/antagonists/blob/` | 菌毯蔓延, 核心存活 |
| **Revolution** 革命 | `code/modules/antagonists/revolution/` | Flash 洗脑, 推翻指挥部 |
| **Malf AI** 失控 AI | `code/modules/antagonists/malf_ai/` | 控制站内电子系统 |
| **Heretic** 异教徒 | `code/modules/antagonists/heretic/` | 献祭解锁禁忌知识 |
| **Xenomorph** 异形 | `code/modules/antagonists/xeno/` | 抱脸虫→破胸→战士→皇后链 |
| **Abductor** 掳掠者 | `code/modules/antagonists/abductor/` | 外星绑架, 人体实验 |
| **Revenant** 怨灵 | `code/modules/antagonists/revenant/` | 无敌灵体, 吸食灵魂 |
| **Nightmare** 梦魇 | `code/modules/antagonists/nightmare/` | 黑暗生物, 怕光 |
| **Space Ninja** 太空忍者 | `code/modules/antagonists/space_ninja/` | 潜行, 能量刀 |
| **Pirate** 太空海盗 | `code/modules/antagonists/pirate/` | 抢掠商船 |
| **Space Dragon** 太空龙 | `code/modules/antagonists/space_dragon/` | 龙之裂隙 |
| **Morph** 变形兽 | `code/modules/antagonists/morph/` | 伪装成物体伏击 |
| **Ash Walker** 灰烬行者 | `code/modules/antagonists/ashwalker/` | 熔岩地土著 |
| **Fugitive** 逃犯 | `code/modules/antagonists/fugitive/` | 躲避追捕 |
| **Brainwashing** 洗脑特工 | `code/modules/antagonists/brainwashing/` | 深度洗脑 |
| **Brother** 兄弟会 | `code/modules/antagonists/brother/` | 2 人协力叛徒 |
| **Clown Operative** 小丑特工 | `code/modules/antagonists/clown_ops/` | 搞笑核弹特工 |
| **Evil Clone** 邪恶克隆 | `code/modules/antagonists/evil_clone/` | 反叛克隆人 |
| **Highlander** 高地人 | `code/modules/antagonists/highlander/` | 只能活一个 |
| **Hypnotized** 催眠 | `code/modules/antagonists/hypnotized/` | 植入指令 |
| **Obsessed** 痴迷 | `code/modules/antagonists/obsessed/` | 跟踪特定船员 |
| **Paradox Clone** 悖论克隆 | `code/modules/antagonists/paradox_clone/` | 时间线紊乱的另一个你 |
| **Pyro Slime** 烈焰史莱姆 | `code/modules/antagonists/pyro_slime/` | 爆炸史莱姆 |
| **Santa** 圣诞老人 | `code/modules/antagonists/santa/` | 送礼/惩罚 |
| **Sentient Creature** 觉醒生物 | `code/modules/antagonists/sentient_creature/` | 实验体觉醒 |
| **Separatist** 分离主义者 | `code/modules/antagonists/separatist/` | 部门独立派 |
| **Spider** 蜘蛛 | `code/modules/antagonists/spiders/` | 蜘蛛入侵 |
| **Spy** 间谍 | `code/modules/antagonists/spy/` | 渗透情报 |
| **Survivalist** 生存主义者 | `code/modules/antagonists/survivalist/` | 末日求生 |
| **Syndicate Monkey** 辛迪加猴 | `code/modules/antagonists/syndicate_monkey/` | 特工猴子 |
| **Valentines** 情人节 | `code/modules/antagonists/valentines/` | 情人节特殊 |
| **Venus Human Trap** 食人花 | `code/modules/antagonists/venus_human_trap/` | 植物伏击 |
| **Voidwalker** 虚空行者 | `code/modules/antagonists/voidwalker/` | 虚空穿梭 |
| **Wishgranter** 许愿机 | `code/modules/antagonists/wishgranter/` | 力量与代价 |
| **Battlecruiser** 战舰 | `code/modules/antagonists/battlecruiser/` | 敌舰入侵 |
| **Blood Worm** 血虫 | `code/modules/antagonists/blood_worm/` | 寄生感染 |
| **ERT** 应急反应队 | `code/modules/antagonists/ert/` | 中央指挥部派遣 |

### 3.3 反向专用配装 (antagonist job_types)

| 玩法 | 代码路径 |
|---|---|
| 各反向配装 | `code/modules/jobs/job_types/antagonists/` |

---

## 四、医疗与生理系统

### 4.1 伤害与生命

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 生命核心 | `code/modules/mob/living/living.dm` | 存活/死亡判断 |
| 伤害处理 | `code/modules/mob/living/damage_procs.dm` | 伤害计算和传递 |
| 生命体征 | `code/modules/mob/living/status_procs.dm` | 各状态管理 |
| 死亡 | `code/modules/mob/living/death.dm` | 死亡处理 |
| 碳基生物伤害 | `code/modules/mob/living/carbon/human/` | 人类特有伤害系统 |

### 4.2 伤口系统 (Wounds)

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 伤口框架 | `code/datums/wounds/_wounds.dm` | 伤口基础和生命周期 |
| 静态数据 | `code/datums/wounds/_wound_static_data.dm` | 伤口配置 |
| 钝伤 | `code/datums/wounds/blunt.dm` | 撞击/钝器 |
| 割伤 | `code/datums/wounds/slash.dm` | 刀剑/利刃 |
| 穿刺 | `code/datums/wounds/pierce.dm` | 子弹/刺击 |
| 烧伤 | `code/datums/wounds/burns.dm` | 火焰/激光/等离子 |
| 骨折 | `code/datums/wounds/bones.dm` | 断骨, 需夹板/手术 |
| 颅骨裂缝 | `code/datums/wounds/cranial_fissure.dm` | 头部重伤 |
| 组织损失 | `code/datums/wounds/loss.dm` | 严重组织缺失 |
| 疤痕系统 | `code/datums/wounds/scars/` | 永久疤痕, 影响外观/功能 |

### 4.3 外科手术

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 手术框架 | `code/modules/surgery/surgery_tray.dm` | 手术台 |
| 手术工具 | `code/modules/surgery/surgery_tools.dm` | 刀/钳/锯/电凝等 |
| 手术操作 | `code/modules/surgery/operations/` | 各手术类型 |
| 器官 | `code/modules/surgery/organs/` | 器官定义 |
| 身体部位 | `code/modules/surgery/bodyparts/` | 四肢/躯干/头部等 |
| 手术程序磁盘 | `code/modules/surgery/surgery_disks.dm` | 手术程序升级 |

### 4.4 疾病系统

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 疾病框架 | `code/datums/diseases/_disease.dm` | 疾病 datum |
| 疾病传播 | `code/datums/diseases/_MobProcs.dm` | 感染/传播逻辑 |
| 高级疾病 (DIY 病毒) | `code/datums/diseases/advance/` | 可编程病毒系统 |

各具体疾病:
```
cold.dm, flu.dm       - 感冒/流感 (咳嗽/喷嚏)
gbs.dm                - 重症真菌感染 (高致死)
brainrot.dm           - 脑腐病 (智力下降)
beesease.dm           - 蜜蜂病 (怪异症状)
pierrot_throat.dm     - 小丑喉 (不受控制讲笑话)
magnitis.dm           - 磁化症 (被金属吸引)
anxiety.dm            - 焦虑症
parrotpossession.dm   - 鹦鹉附体 (说违禁词)
dna_spread.dm         - DNA扩散 (基因变异)
parasitic_infection.dm- 寄生虫感染
gastrolisis.dm        - 胃溶症
cold9.dm              - 冷病毒9型
flu_spanish.dm        - 西班牙流感
fake_gbs.dm           - 假GBS
decloning.dm          - 去克隆化
adrenal_crisis.dm     - 肾上腺危象
anaphylaxis.dm        - 过敏性休克
asthma_attack.dm      - 哮喘发作
chronic_illness.dm    - 慢性病
death_sandwich_poisoning.dm - 死亡三明治中毒
floor_diseases/       - 地板传染病
```

### 4.5 身体与器官

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| DNA 系统 | `code/datums/dna/` | 基因/UI/UE |
| 突变系统 | `code/datums/mutations/` | Hulk/喷火/变色等 |
| 生物植入体 | `code/datums/status_effects/buffs/bioware/` | 循环/皮层/韧带/神经强化 |
| 身体部位覆盖 | `code/datums/bodypart_overlays/` | 肢体外观变化 |

### 4.6 精神/心理

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 情绪系统 | `code/datums/mood.dm` | 环境/事件/社交影响情绪 |
| 情绪事件 | `code/datums/mood_events/` | 各情绪触发条件 |
| 幻觉 | `code/modules/hallucination/` | 精神错乱幻觉 |
| 大脑损伤 | `code/datums/brain_damage/` | 脑损伤效果 |
| 人格 | `code/datums/personality/` | AI/机器人人格 |

---

## 五、战斗系统

### 5.1 枪械与弹药

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 枪械框架 | `code/modules/projectiles/gun.dm` | 基础枪类型 |
| 弹射物框架 | `code/modules/projectiles/projectile.dm`, `projectile/` | 弹道/子弹 |
| 弹药 | `code/modules/projectiles/ammunition/` | 各种口径弹药 |
| 弹匣/弹盒 | `code/modules/projectiles/boxes_magazines/` | 供弹系统 |
| 枪管改装 | `code/modules/projectiles/pins.dm` | 枪管刻字/改装 |
| 能量武器 | `code/modules/projectiles/guns/energy/` | E-gun, laser, disabler |
| 实弹武器 | `code/modules/projectiles/guns/ballistic/` | 手枪/步枪/冲锋枪/霰弹枪 |
| 投射武器 | `code/modules/projectiles/guns/grenade_launcher/` | 榴弹发射器 |
| 魔法法杖 | `code/modules/projectiles/guns/magic/` | 巫师法杖 |

### 5.2 近战武器

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 近战武器框架 | `code/game/objects/items/weaponry/melee/` | 各近战武器 |

具体:
```
baton.dm              - 警棍
knives.dm             - 刀类
sword.dm              - 剑
claymore.dm           - 阔剑
katana.dm             - 武士刀
dualsaber.dm          - 双头光剑
energy.dm             - 能量武器
chainsaw.dm           - 电锯
fireaxe.dm            - 消防斧
spear.dm              - 矛
powerfist.dm          - 动力拳套
baseball_bat.dm       - 棒球棒
supermatter_sword.dm  - 超物质剑
soulscythe.dm         - 灵魂镰刀
high_frequency_blade.dm - 高频刃
his_grace.dm          - "他的恩典" (特殊武器)
```

### 5.3 法射武器

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 火焰喷射器 | `code/game/objects/items/weaponry/ranged/flamethrower.dm` | |
| 气动炮 | `code/game/objects/items/weaponry/ranged/pneumatic_cannon.dm` | |
| 手里剑 | `code/game/objects/items/weaponry/ranged/throwing_star.dm` | |

### 5.4 盾牌

| 玩法 | 代码路径 |
|---|---|
| 盾牌 | `code/game/objects/items/weaponry/shields.dm` |

### 5.5 武术

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 武术框架 | `code/datums/martial/_martial.dm` | 基础 martial_art datum |
| 动作系统 | `code/datums/martial/_action.dm` | 武术动作 |
| 拳击 | `code/datums/martial/boxing.dm` | 基础近战 |
| CQC | `code/datums/martial/cqc.dm` | 安全部格斗术 |
| 睡鲤拳 | `code/datums/martial/sleeping_carp.dm` | 防反, 可空手接子弹 |
| 等离子拳 | `code/datums/martial/plasma_fist.dm` | 终结技 |
| 摔跤 | `code/datums/martial/wrestling.dm` | 锁技/投技 |
| Kaza Ruk | `code/datums/martial/kaza_ruk.dm` | 外星格斗 |
| 蘑菇拳 | `code/datums/martial/mushpunch.dm` | 蘑菇人武术 |
| 疯人乱斗 | `code/datums/martial/psychotic_brawl.dm` | |
| 蜘蛛之咬 | `code/datums/martial/spiders_bite.dm` | |
| Jungle Arts | `code/datums/martial/junglearts.dm` | 丛林格斗术 |

### 5.6 护甲与防御

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 护甲系统 | `code/datums/armor/` | 伤害减免计算 |
| 护甲类型 | `code/modules/clothing/` | 各护甲定义 |
| MOD 外骨骼 | `code/modules/mod/` | 模块化外骨骼装甲 |

---

## 六、化学与药剂

### 6.1 化学合成

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 化学反应框架 | `code/modules/reagents/chemistry/` | 化学合成引擎 |
| 合成配方 | `code/modules/reagents/chemistry/recipes.dm` | 565 条合成配方 |
| 药剂容器 | `code/modules/reagents/reagent_containers/` | 瓶子/注射器/试管 |
| 自动配药机 | `code/modules/reagents/reagent_dispenser.dm` | 化学品分配器 |
| 化学飞溅 | `code/modules/reagents/chem_splash.dm` | 化学品溅射效果 |
| 叠加效果 | `code/modules/reagents/stacked_effects/` | 多重化学效果 |
| 戒断反应 | `code/modules/reagents/withdrawal/` | 药物戒断 |

### 6.2 食物与饮品

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 食物系统 | `code/modules/food_and_drinks/food/` | 各种食物定义 |
| 饮品系统 | `code/modules/food_and_drinks/drinks/` | 饮料/酒品 |
| 配方 | `code/modules/food_and_drinks/recipes/` | 烤箱/煎锅/微波炉食谱 |
| 餐饮机器 | `code/modules/food_and_drinks/machinery/` | 厨师机器人/咖啡机等 |
| 餐饮设备 | `code/modules/food_and_drinks/equipment/` | 厨房设备 |
| 全自动餐厅 | `code/modules/food_and_drinks/restaurant/` | 餐厅管理 |
| 披萨盒 | `code/modules/food_and_drinks/pizzabox.dm` | |

### 6.3 水培种植

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 水培系统核心 | `code/modules/hydroponics/hydroponics.dm` | 种植机/生长 |
| 种子定义 | `code/modules/hydroponics/seeds.dm` | 652 行种子数据 |
| 植物基因 | `code/modules/hydroponics/plant_genes.dm` | 基因拼接改造 |
| 成熟作物 | `code/modules/hydroponics/grown.dm`, `growninedible.dm` | 可食用/不可食用作物 |
| 生物反应器 | `code/modules/hydroponics/biogenerator.dm` | 生物质转化 |
| 发酵桶 | `code/modules/hydroponics/fermenting_barrel.dm` | 发酵酿酒 |
| 嫁接 | `code/modules/hydroponics/grafts.dm` | 植物嫁接 |
| 蜜蜂养殖 | `code/modules/hydroponics/beekeeping/` | 养蜂/蜂蜜 |
| 种子提取器 | `code/modules/hydroponics/seed_extractor.dm` | 取种 |
| 化学反应 | `code/modules/hydroponics/hydroponics_chemreact.dm` | 植物化学互动 |
| 花束 | `code/modules/hydroponics/bouquets.dm` | 花束制作 |
| 蜗牛覆盖 | `code/modules/hydroponics/snail_overlay.dm` | |

---

## 七、工程与动力

### 7.1 大气系统

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 大气系统核心 | `code/modules/atmospherics/Atmospherics.md` | 设计文档 |
| 气体混合引擎 | `code/modules/atmospherics/gasmixtures/` | 物理模拟: 压力/温度/摩尔 |
| 管道机械 | `code/modules/atmospherics/machinery/` | 管道/阀门/泵/过滤器 |
| 环境模拟 | `code/modules/atmospherics/environmental/` | 温度扩散/空间环境 |

### 7.2 电力系统

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 电力网络 | `code/modules/power/power.dm`, `powernet.dm` | 电网计算 |
| APC | `code/modules/power/apc/` | 区域供电控制器 |
| SMES | `code/modules/power/smes.dm` | 大型储能电池 |
| 便携式 SMES | `code/modules/power/smes_portable.dm` | 移动储能 |
| 电池/电芯 | `code/modules/power/cell.dm` | 各类型电池 |
| 电缆 | `code/modules/power/cable.dm` | 电线铺设 |
| 太阳能 | `code/modules/power/solar.dm` | 太阳能板阵列 |
| 发电机 | `code/modules/power/port_gen.dm` | 便携发电机 |
| RTG | `code/modules/power/rtg.dm` | 放射性同位素发电机 |
| 重力发生器 | `code/modules/power/gravitygenerator.dm` | 站内重力 |
| 聚变反应堆 | `code/modules/power/energy_accumulator.dm` | 能源积累 |
| 充电器 | `code/modules/power/port_gen.dm` | |

### 7.3 电力生产 (反应堆)

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 超物质引擎 | `code/modules/power/singularity/` | Supermatter 反应堆 |
| 奇点引擎 | `code/modules/power/singularity/` | 黑洞发生器 |
| 特斯拉线圈 | `code/datums/wires/tesla_coil.dm` | |

### 7.4 灯光系统

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 照明 | `code/modules/lighting/` | 动态光照系统 |
| 泛光灯 | `code/modules/power/floodlight.dm` | 工程泛光灯 |
| 灯光开关 | `code/game/machinery/lightswitch.dm` | |

### 7.5 管道系统

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 管道图标 | `icons/obj/pipes_n_cables/` | 管道/线缆图标 |
| 管道清洁器 | `code/modules/power/pipecleaners.dm` | |

---

## 八、科研与科技

### 8.1 科技树 (Techweb)

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 科技网框架 | `code/modules/research/techweb/_techweb.dm` | 科技拓扑 |
| 科技节点定义 | `code/modules/research/techweb/nodes/` | 各科技节点 |
| 科技节点结构 | `code/modules/research/techweb/_techweb_node.dm` | 节点 datum |
| 设计图 | `code/modules/research/designs/` | 数百种可打印物品 |
| 设计图控制器 | `code/modules/research/designs.dm` | |

### 8.2 研究设备

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 破坏性分析仪 | `code/modules/research/destructive_analyzer.dm` | 拆解物品获取科技点 |
| 实验器 | `code/modules/research/experimentor/` | 高风险实验设备 |
| R&D 控制台 | `code/modules/research/rdconsole.dm` | 研究管理控制台 |
| 研究服务器 | `code/modules/research/server.dm` | 科技服务器核心 |
| 服务器控制 | `code/modules/research/server_control.dm` | |
| R&D 机器 | `code/modules/research/rdmachines.dm` | 各研究机器 |
| 零件升级 | `code/modules/research/stock_parts/` | 机器性能升级 |
| 零件替换器 | `code/modules/research/part_replacer.dm` | RPED |

### 8.3 特殊研究

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 外星生物 | `code/modules/research/xenobiology/` | 史莱姆提取物研究 |
| 异物质 | `code/modules/research/anomaly/` | 空间异常精炼 |
| 异物质精炼 | `code/modules/research/anomaly/anomaly_refinery.dm` | |
| 英军火研究 | `code/modules/research/ordnance/` | 爆炸物/武器研究 |
| 小工具 | `code/modules/research/gizmo/` | 新奇小发明 |
| 远古装置 | `code/modules/research/relics.dm` | 遗迹装置 |
| 研究磁盘 | `code/modules/research/research_disk.dm` | 科技存储/转移 |

### 8.4 异常系统

| 玩法 | 代码路径 |
|---|---|
| 异常核心 | `code/modules/research/anomaly/anomaly_core.dm` |
| 原始异常 | `code/modules/research/anomaly/raw_anomaly.dm` |

---

## 九、货运与经济

### 9.1 货运系统

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 订购控制台 | `code/modules/cargo/orderconsole.dm` | 军需官订购 UI |
| 物资包 | `code/modules/cargo/packs/` | 各订购包定义 |
| 出口 | `code/modules/cargo/exports/` | 可出口物资 |
| 出口控制器 | `code/modules/cargo/exports.dm` | |
| 订单 | `code/modules/cargo/order.dm` | 订单处理 |
| 补给舱 | `code/modules/cargo/supplypod.dm` | 空投舱物理 |
| 补给舱信标 | `code/modules/cargo/supplypod_beacon.dm` | 空投降落点 |

### 9.2 经济系统

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 赏金 | `code/modules/cargo/bounties/` | 完成目标获得资金 |
| 赏金核心 | `code/modules/cargo/bounty.dm` | |
| 优惠券 | `code/modules/cargo/coupon.dm` | 折扣券 |
| 特价品 | `code/modules/cargo/goodies.dm` | 每日特价 |
| 市场 | `code/modules/cargo/markets/` | 市场价格波动 |
| 材料市场 | `code/modules/cargo/materials_market.dm` | 矿石价格 |
| 银行 | `code/game/machinery/bank_machine.dm` | 站内银行终端 |
| 股市事件 | `code/datums/stock_market_events.dm` | 模拟股市波动 |

---

## 十、采矿与勘探

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 采矿主系统 | `code/modules/mining/` | 核心定义 |
| 熔岩地 | `code/modules/mining/lavaland/` | 岩浆行星生态/矿物 |
| 矿石/硬币 | `code/modules/mining/ores_coins.dm` | 矿物品名/货币 |
| 矿物处理 | `code/modules/mining/boulder_processing/` | 岩石破碎处理 |
| 自动加工机 | `code/modules/mining/machine_processing.dm` | |
| 赎矿机 | `code/modules/mining/machine_redemption.dm` | 矿石兑换 |
| 堆矿机 | `code/modules/mining/machine_stacking.dm` | 矿物堆叠 |
| 卸矿机 | `code/modules/mining/machine_unloading.dm` | |
| 矿仓 | `code/modules/mining/machine_silo.dm` | 自动采矿仓 |
| 采矿装备 | `code/modules/mining/equipment/` | 镐/背包/探测仪 |
| 采矿工具 | `code/modules/mining/mine_items.dm` | 各种矿区物品 |
| 富尔顿回收 | `code/modules/mining/fulton.dm` | 标记→回收拉回 |
| 庇护所圆盘 | `code/modules/mining/shelters.dm` | 即开式庇护所 |
| 劳改营 | `code/modules/mining/laborcamp/` | 囚犯采矿营 |
| 辅助基地 | `code/modules/mining/aux_base.dm` | 太空前哨基地 |
| 矿工券 | `code/modules/mining/voucher_sets.dm` | |
| 矿区宝箱 | `code/modules/mining/satchel_ore_box.dm` | |

---

## 十一、导航与交通

### 11.1 穿梭机/飞船

| 玩法 | 代码路径 |
|---|---|
| 穿梭机核心 | `code/modules/shuttle/shuttle.dm` |
| 接驳口 | `code/modules/shuttle/stationary_port/` |
| 移动港口 | `code/modules/shuttle/mobile_port/` |
| 穿梭机控制台 | `code/modules/shuttle/shuttle_consoles/` |
| 穿梭机事件 | `code/modules/shuttle/shuttle_events/` |
| 引擎 | `code/datums/shuttles/` |

穿梭机类型:
```
emergency.dm  - 紧急逃生穿梭机
cargo.dm      - 货运穿梭机
mining.dm     - 采矿穿梭机
arrival.dm    - 到达穿梭机
ferry.dm      - 定期穿梭机
assault_pod.dm - 突击舱
escape_pod.dm - 逃生舱
ert.dm        - 应急反应队穿梭机
infiltrator.dm - 渗透艇
pirate.dm     - 海盗船
hunter.dm     - 猎船
labour.dm     - 劳改穿梭机
aux_base.dm   - 辅助基地穿梭机
whiteship.dm  - 白船 (玩家可操控探索船)
ruin.dm       - 废墟穿梭机
starfury.dm   - 星怒战斗机
```

### 11.2 轨道/多 Z 层

| 玩法 | 代码路径 |
|---|---|
| 多 Z 层移动 | `code/modules/multiz/` (在 power/?) |
| 梯子 | `code/game/objects/structures/ladders.dm` |
| 楼梯 | `code/game/objects/structures/stairs.dm` |
| 平台 | `code/game/objects/structures/platform.dm` |
| 传送管 | `code/game/objects/structures/transit_tubes/` |
| 传送器 | `code/game/machinery/computer/teleporter.dm` |

---

## 十二、社交/文化/宗教

### 12.1 通信

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 对话系统 | `code/game/say.dm` | 说话/喊话/耳语/广播 |
| 通信控制台 | `code/game/machinery/computer/communications.dm` | 全站广播 |
| 全息通话 | `code/datums/holocall.dm` | 全息投影通讯 |
| 请求消息 | `code/datums/request_message.dm` | 部门间申请信息 |
| 广播 | `code/modules/telecommunications/` | 无线电/加密频道 |
| 数据 HUD | `code/game/data_huds.dm` | 信息抬头显示 |

### 12.2 图书馆/文字

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 图书系统 | `code/modules/library/` | 书/书架/借阅 |
| 圣经 | `code/modules/library/bibles.dm` | 各宗教圣典 |
| 法典巨著 | `code/modules/library/lib_codex_gigas.dm` | 禁忌知识书 |
| 自动写作 | `code/modules/library/admin_only.dm` | 管理员填书 |
| 技能学习站 | `code/modules/library/skill_learning/` | |

### 12.3 宗教

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 宗教系统 | `code/modules/religion/` | 信仰/仪式/建筑 |
| 教派 | `code/modules/religion/religion_sects.dm` | 各宗教派别 |
| 仪式 | `code/modules/religion/rites.dm` | 活祭/净化/祝福 |
| 宗教建筑 | `code/modules/religion/religion_structures.dm` | 祭坛/神龛 |
| 受洗机 | `code/modules/religion/deaconize.dm` | 机器人版洗礼 |
| 梦境 | `code/modules/religion/dreams/` | 宗教梦境 |
| 圣火 | `code/modules/religion/pyre/` | 祭祀火焰 |
| 节日 | `code/modules/religion/festival/` | 宗教节日 |
| 训练 | `code/modules/religion/sparring/` | 格斗训练 |
| 荣誉使命 | `code/modules/religion/honorbound/` | |
| 负担 | `code/modules/religion/burdened/` | |

### 12.4 娱乐/小游戏

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 全息甲板 | `code/modules/holodeck/` | 虚拟训练/娱乐 |
| 街机 | `code/game/machinery/computer/arcade/` | 打飞机/猎户座等 |
| 篮球 | `code/modules/basketball/` | 篮球小游戏 |
| 象棋 | `code/game/objects/structures/chess.dm` | 国际象棋 |
| TCG 卡牌 | `code/game/objects/items/tcg/` | 集换式卡牌游戏 |
| 乐器 | `code/modules/instruments/` | 电子琴/吉他/鼓 |
| 抓捕旗 | `code/modules/capture_the_flag/` | CTF 比赛 (modular_nova) |
| 死亡竞赛 | `code/modules/deathmatch/` | 死斗模式 |
| 黑手党 | `code/modules/mafia/` | 黑手党社交推理 |

---

## 十三、AI 与机器人

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| AI 核心 | `code/modules/mob/living/silicon/ai/` | AI 玩家控制 |
| AI 法则 | `code/datums/ai_laws/` | 机器人三法则等 |
| 机器人 | `code/modules/mob/living/silicon/robot/` | Cyborg 玩家 |
| 机器人躯体 | `code/modules/robot/` | 机器人躯干部件 |
| 飞行 AI | `code/modules/mob/living/silicon/ai/freelook/` | AI 视角/摄像头 |
| MOD 系统 | `code/modules/mod/` | 模块化外骨骼 |

---

## 十四、物品制作与合成

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 制造工坊 | `code/modules/manufactorio/` | 工厂自动化 |
| 自动车床 | `code/game/machinery/autolathe.dm` | 材料→物品 |
| 3D 打印机 | `code/game/machinery/flatpacker.dm` | 平板打印 |
| 电线系统 | `code/datums/wires/` | 破解/接线迷你游戏 |

可破解设备 (wires):
```
airlock.dm     - 气闸
apc.dm         - APC 供电
vending.dm     - 自动售货机
robot.dm       - 机器人
mecha.dm       - 机甲
syndicatebomb.dm - 炸弹
explosive.dm   - 爆炸物
radio.dm       - 无线电
r_n_d.dm       - R&D 机器
```

---

## 十五、地图与环境

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 主站地图 | `_maps/` | 13+ 张主地图定义 |
| 区域定义 | `code/game/area/areas/station/` | 各功能区域(工程/医疗/安保等) |
| 随机废墟 | `_maps/RandomRuins/` | 太空/冰原/熔岩废墟 |
| 随机 Z 层 | `_maps/RandomZLevels/` | 随机地图 |
| 远征任务 | `code/modules/awaymissions/` | 出站远征 |
| 天气系统 | `code/datums/weather/` | 灰烬风暴/辐射/雪/雨 |
| 矿物生成 | `code/game/turfs/open/minerals.dm` | 矿脉分布 |
| 自然环境 | `code/game/turfs/open/` | 各地形类型 |

---

## 十六、Nova Sector 特色模块

> 路径: `modular_nova/modules/`

| 模块 | 代码路径 | 说明 |
|---|---|---|
| Bitrunning | `modules/bitrunning/` | 赛博空间潜入, 进虚拟世界执行任务 |
| Xenomorph | `modules/xenomorph/` | 完整异形蜂巢 |
| Xenoarchaeology | `modules/xenoarch/`, `modules/xenoarchartifacts/` | 外星考古/文物 |
| Solfed Mecha | `modules/solfed_mechs/` | 机甲战斗 |
| Armaments Station | `modules/armaments/` | 军火配装站 |
| Bluespace Miner | `modules/bluespace_miner/` | 蓝空间采矿 |
| Bluespace Admin | `modules/bluespace_admin/` | 管理员蓝空间工具 |
| Synths | `modules/synths/` | 合成人种族 |
| Vox | `modules/voxes/` | 鸟人族 (完整重做) |
| Teshari | `modules/teshari/` | 鸟形族 |
| Shadekin | `modules/shadekin/` | 暗影猫族 |
| Advanced Shuttles | `modules/advanced_shuttles/` | 高级飞船引擎 |
| BSA Overhaul | `modules/bsa_overhaul/` | 蓝图武器改造 |
| Blueshield | `modules/blueshield/` | 蓝盾军官 (保护指挥层) |
| Bridge Assistant | `modules/bridge_assistant/` | 舰桥助理 |
| Telecomms Specialist | `modules/telecomms_specialist/` | 通信专家 |
| Capitalism | `modules/capitalism/` | 资本主义经济 |
| Station Traits | `modules/station_traits/` | 空间站全局特征 |
| Biteng | `modules/bitey_quirk/` | 咬人特质 |
| Borg Buffs | `modules/borg_buffs/` | 机器人增强 |
| Brain Tumor | `modules/brain_tumor/` | 脑瘤系统 |
| Cell Guns | `modules/cellguns/` | 细胞武器 |
| Space Vines | `modules/space_vines/` | 太空藤蔓灾害 |
| Alcohol Processing | `modules/alcohol_processing/` | 工业化酿酒 |
| Underworld Connections | `modules/underworld_connections/` | 地下交易 |
| Self Actualization Device | `modules/self_actualization_device/` | 自我实现装置 |
| Wargame Projectors | `modules/wargame_projectors/` | 兵棋推演投影 |
| Tribal Extended | `modules/tribal_extended/` | 部落扩展 |
| Tarkon | `modules/tarkon/` | Tarkon 公司模块 |
| Time Clock | `modules/time_clock/` | 上/下班打卡系统 |
| Cargo Items (Nova) | `modules/cargo_items/` | 新增货运物品 |
| Spider | `modules/spider/` | 蜘蛛种族 |
| Species Synthesizer | `modules/species_synthesizer/` | 物种合成器 |
| SEVA Suit | `modules/SEVA_suit/` | SEVA 防护服 (潜行者风格) |
| Fabcraft | `modules/fabcraft/` | 以物易物合成 |

---

## 十七、天关专属模块

> 路径: `modular_tianguan/`

| 模块 | 代码路径 | 说明 |
|---|---|---|
| 安全官配装 | `code/security_officer.dm` | 安全官额外获得迷你能量枪 |
| 项目符号 | `code/projectiles/` | 弹道调整 |
| 警报等级 | `modules/Alert_level/` | 警报等级自定义 |
| 货运调整 | `modules/cargo_tweaks/` | 货运参数优化 |
| 紧急穿梭机 | `modules/emergency_shuttle_cc/` | 中央指挥穿梭机调整 |
| 医疗记录 | `modules/medical_blanks/` | 医疗表格模板 |
| SOP 手册 | `modules/sop_book/` | 标准作业程序书 |
| Uplink 物品 | `modules/uplink_item/` | 加密 Uplink 道具定制 |

---

## 附：编程/脚本辅助

### 通用辅助系统

| 玩法 | 代码路径 | 说明 |
|---|---|---|
| 成就系统 | `code/datums/achievements/` | BOSS/职业/技能/杂项成就 |
| 技能学习 | `code/datums/skills/` | 钓鱼/采矿/清洁/运动/游戏 |
| 投票系统 | `code/datums/votes/` | 地图/重启/自定义投票 |
| 记录系统 | `code/datums/records/` | 犯罪/医疗/机组档案 |
| 音频系统 | `code/datums/3d_sounds/` | 3D 定位音效 |
| 自动公告 | `code/datums/announcers/` | 站内广播员 |
| 公告系统 | `code/game/machinery/announcement_system.dm` | 自动公共广播 |
| 传送记录 | `code/datums/station_alert.dm` | 全站警报状态 |
| 站台完整性 | `code/datums/station_integrity.dm` | 船体结构完整性 |
| 情绪表情 | `code/datums/emotes.dm` | 玩家情感动作 |

### 管理/调试

| 玩法 | 代码路径 |
|---|---|
| 管理员面板 | `code/modules/admin/` |
| 管理员频道 | `code/modules/admin/admin_频道/` |
| 投票管理 | `code/modules/admin/投票/` |
| 调试工具 | `code/modules/debugging/` |
| 日志 | `code/modules/logging/` |
| 错误处理 | `code/modules/error_handler/` |

---

## 索引用法

写教程时，对每个玩法：

1. **找定位** — 看此目录中对应 `代码路径` 列
2. **读核心定义** — 先读框架文件（如 `_disease.dm`, `_martial.dm`）
3. **看具体实现** — 读各子文件（如 `cold.dm`, `sleeping_carp.dm`）
4. **查配置** — 检查 `config/` 下对应配置策略
5. **查地图** — 检查 `_maps/` 中对应区域/物品生成

所有路径相对于 `C:\Users\33922\Desktop\Hermes\TianGuan13\TianGuan13-master\`。
