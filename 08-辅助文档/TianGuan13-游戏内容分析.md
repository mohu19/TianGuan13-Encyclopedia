# 天关（TianGuan13）游戏内容分析报告

> **项目路径**: `C:\Users\33922\Desktop\Hermes\TianGuan13\TianGuan13-master\`
> **引擎**: BYOND (DreamMaker)
> **上游关系**: 天关 → Nova Sector → /tg/station

---

## 一、项目概览

### 上游链

```
/tg/station (上游主分支)
    └─ Nova Sector (下游 fork, 中文社区扩展)
        └─ 天关 (本仓库, 在 Nova Sector 上继续下游开发)
```

### 代码分层

| 层级 | 目录 | 说明 |
|---|---|---|
| **核心引擎** | `code/` | BYOND DM 核心游戏逻辑 |
| **Nova 扩展** | `modular_nova/` | Nova Sector 添加的模块化内容 |
| **天关专用** | `modular_tianguan/` | 本 fork 独有改动 |
| **Z121 扩展** | `modular_z121/` | 另一个贡献者的模块 |
| **地图** | `_maps/` | 空间站地图文件 (JSON+DMM) |
| **前端 UI** | `tgui/` | TypeScript Web 界面 (TGUI) |

---

## 二、游戏内容架构深度分析

### 2.1 职业系统 — 分工明确的角色扮演

代码位于 `code/modules/jobs/job_types/`，共约 **40+ 个正式职位**。

#### 指挥层 (Command)

| 职位 | 人数上限 | 职责 |
|---|---|---|
| **Captain** (舰长) | 1 | 全站最高权限, 掌握核武认证 |
| **Head of Personnel** (人事部长) | 1 | 管理民事部门、财务、人员调配 |
| **Head of Security** (安全部长) | 1 | 指挥安保力量、反间谍 |
| **Chief Engineer** (总工程师) | 1 | 领导工程部门、SM 引擎 |
| **Chief Medical Officer** (医疗总监) | 1 | 领导医疗部门、管理克隆/手术 |
| **Research Director** (科研总监) | 1 | 领导科研部门、R&D 进度 |

#### 工程部 (Engineering)

| 职位 | 人数上限 | 核心描述(源码原文) |
|---|---|---|
| **Station Engineer** (站台工程师) | 5 | *"Start the Supermatter, wire the solars, repair station hull and wiring damage"* |
| **Atmospheric Technician** (大气技师) | 3 | 管理管道气体网络, 调节温度压力 |

启动反应堆 → 铺设太阳能 → 维修船体。装备: RCD、工具腰带、T 扫描仪、焊接面罩。

#### 医疗部 (Medical)

| 职位 | 人数上限 | 核心描述 |
|---|---|---|
| **Medical Doctor** (医生) | 6 | *"Save lives, run around looking for victims, scan everyone in sight"* |
| **Paramedic** (救护员) | — | 外勤急救、运送伤员 |
| **Coroner** (法医) | — | 尸检、遗体处理 |
| **Psychologist** (心理医生) | — | 精神健康辅导 |
| **Geneticist** (基因学家) | — | DNA 改造、基因突变 |
| **Chemist** (药剂师) | — | 化工合成、制药 |

#### 科研部 (Science)

| 职位 | 人数上限 | 核心描述 |
|---|---|---|
| **Scientist** (科学家) | 5 | *"Do experiments, perform research, feed the slimes, make bombs"* |
| **Roboticist** (机器人专家) | — | 制造机器人/AI 躯体 |
| **Ordnance Technician** (军火技师) | — | 炸弹测试、弹道研究 (modular_nova) |

#### 安保部 (Security)

| 职位 | 人数上限 | 核心描述 |
|---|---|---|
| **Security Officer** (安全官) | 8 | *"Protect company assets, follow the SOP, eat donuts"* |
| **Detective** (侦探) | — | 犯罪现场调查、弹道分析 |
| **Warden** (典狱长) | — | 管理牢房/武器库 |
| **Lawyer** (律师) | — | 法律咨询、辩护 |

> 天关改动: 安全官额外获得一把小型能量枪配发 (`/obj/item/gun/energy/e_gun/mini`)。

#### 后勤部 (Supply)

| 职位 | 职责 |
|---|---|
| **Quartermaster** (军需官) | 管理货运 |
| **Cargo Technician** (货运技工) | 装卸货、订购物资 |
| **Shaft Miner** (矿工) | 前往小行星带/熔岩地采矿 |

#### 服务部 (Service)

| 职位 | 职责 |
|---|---|
| **Bartender** (酒保) | 调酒 |
| **Botanist** (植物学家) | 水培种植 |
| **Chef** (厨师) | 烹饪 |
| **Janitor** (清洁工) | 维持站内卫生, 处理污渍 |
| **Chaplain** (牧师) | 宗教仪式、圣书 |
| **Curator** (馆长) | 管理图书馆 |
| **Clown** (小丑) | 娱乐大众(绊倒、HONK!) |
| **Mime** (默剧) | 无声表演、透明墙 |
| **Cargo Gorilla** (货运猩猩) | 大猩猩货运助手 (modular_nova) |

#### 特殊职位

| 职位 | 说明 |
|---|---|
| **AI** (人工智能) | 全站 AI 核心, 摄像头/门禁/广播 |
| **Cyborg** (机器人) | AI 下属的人形机器人, 模块化工具 |
| **Prisoner** (囚犯) | 初始身份即囚犯 |

#### 经济系统特征

- 每个职业有 `paycheck`(工资等级) 和 `paycheck_department`(部门账户)
- 工资存放在工作站银行账户
- 职业特有 `liver_traits` 肝特性(如 `TRAIT_ENGINEER_METABOLISM`, `TRAIT_MEDICAL_METABOLISM`)
- 随机 `family_heirlooms` 传家宝初始物品
- 邮件系统 `mail_goodies` 定期随机派发物品
- `exp_requirements` 经验门槛 (以分钟计, 如安全官需 300 分钟经验)

---

### 2.2 反派/敌对阵营 (Antagonists) — 33 种

这是 SS13 最核心的玩法: 每局随机生成不同的敌对玩家。

#### 第一梯队 — 经典反派

| 反派 | 核心机制 |
|---|---|
| **Traitor** (叛徒) | 最基础的反派。通过加密 Uplink 购买装备(武器/工具/伪装道具), 完成刺杀/破坏目标。 |
| **Changeling** (变形怪) | 外星生物, 吸收人类 DNA 后可以完美变形; 使用化学分泌物(神经毒/酸液/假死); 有 `dna_max=6` 的 DNA 槽位上限; 原身模板 `first_profile` 永久保留。 |
| **Nuclear Operative** (核武特工) | 一队 5 人精英雇佣兵, 乘坐太空船接近空间站, 目标是引爆核弹摧毁站台。 |
| **Cult** (血 cult) | 邪教徒, 通过符文和献祭召唤神祇 Nar'Sie, 可将人类转化为教徒/构建传送门。 |
| **Wizard** (巫师) | 单人超强角色, 随机传送到站内, 用魔法书学习咒语(火球/传送/附身/结界); 可召唤学徒; 有 `Grand Ritual` 大招; `suicide_cry = "FOR THE FEDERATION!!"`。 |
| **Blob** (菌母) | 站内爆发的新型物种, 快速蔓延菌毯和核心, 全体船员需合作清除。 |
| **Revolution** (革命) | 革命军试图推翻指挥部, 通过闪光(flash)洗脑方式发展成员。 |
| **Malfunctioning AI** (失控 AI) | AI 核心叛变, 控制站内所有电子系统, 可关生命维持/开气闸。 |

#### 第二梯队 — 特殊反派

| 反派 | 机制 |
|---|---|
| **Heretic** (异教徒) | 通过献祭解锁禁忌知识, 获取超自然力量 |
| **Xenomorph** (异形) | 经典抱脸虫→破胸→成长链, 蜂巢意识 |
| **Abductor** (掳掠者) | 外星人小队, 绑架船员做实验/植入追踪器 |
| **Revenant** (怨灵) | 无敌灵体, 可吸食灵魂、损坏设备 |
| **Nightmare** (梦魇) | 黑暗生物, 在光照下会受伤 |
| **Space Ninja** (太空忍者) | 潜行型刺客, 带能量刀/烟雾弹/爬墙能力 |
| **Pirate** (太空海盗) | 抢掠商船 |
| **Space Dragon** (太空龙) | 召唤龙之裂隙, 破坏船体 |
| **Morph** (变形兽) | 可变形成物体, 伏击船员 |
| **Fugitive** (逃犯) | 逃离法律追捕 |
| **Sentient Creature** (有意识生物) | 觉醒的站内实验体 |

#### 第三梯队 — 小规模/趣味性

| 反派 | 机制 |
|---|---|
| **Brainwashing** (洗脑) | 深度洗脑间谍 |
| **Brother** (兄弟会) | 2 人协力叛徒 |
| **Clown Operative** (小丑特工) | 极其搞笑版的核弹特工 |
| **Evil Clone** (邪恶克隆体) | 反叛的克隆人 |
| **Hypnotized** (催眠) | 被深度催眠的特工 |
| **Obsessed** (痴迷) | 跟踪/纠缠特定船员 |
| **Paradox Clone** (悖论克隆) | 时间线紊乱出现的另一个你 |
| **Pyro Slime** (烈焰史莱姆) | 爆炸性史莱姆 |
| **Spy** (间谍) | 渗透套取情报 |
| **Survivalist** (生存主义者) | 在混乱中自我求生 |
| **Venus Human Trap** (食人花) | 植物系伏击者 |
| **Voidwalker** (虚空行者) | 虚空穿梭 |
| **Wishgranter** (许愿机) | 获得力量但付出代价 |

---

### 2.3 物种系统 — 20+ 可玩种族

代码位于 `code/modules/mob/living/carbon/human/species_types/`:

| 物种 | 特性 |
|---|---|
| **Human** (人类) | 基准种族 |
| **Lizardperson** (蜥蜴人) | 尾巴、鳞甲、体温调节 |
| **Mothman** (蛾人) | 怕火、毛茸茸 |
| **Plasmaman** (等离子人) | 必须穿太空服、只能呼吸等离子体、自燃风险 |
| **Flyperson** (蝇人) | 呕吐、吃垃圾 |
| **Jellyperson** (果冻人) | 半透明、毒素代谢特殊 |
| **Skeleton** (骷髅) | 无器官、无血液、免疫疾病 |
| **Zombie** (丧尸) | 病毒感染状态 (非可选角色) |
| **Podperson** (植物人) | 光合作用 |
| **Abductor** (外星人) | 只有掳掠者可用 |
| **Android** (安卓) | 人造躯体 |
| **Dullahan** (无头骑士) | 头部飘浮 |
| **Ethereal** (以太体) | 能量生命, 需充电 |
| **Felinid** (猫人) | 猫耳、猫尾, 猫行为特征 |
| **Golem** (魔像) | 纯物理躯体 |
| **Mushperson** (蘑菇人) | 真菌共生 |
| **Shadowperson** (暗影人) | 怕光 |
| **Snail** (蜗牛) | 极慢、蜗牛壳 |
| **Vampire** (吸血鬼) | 吸血、怕圣光 (modular_nova 物种) |
| **Ghost** (幽灵) | 观察者模式 |

另有 `vox` (鸟人族, `modular_nova/modules/voxes/`)、`teshari` (鸟形, `modular_nova/modules/teshari/`) 等新增种族。

---

### 2.4 医疗/生理系统 — 深度体验

#### 外科手术 (`code/modules/surgery/`)

- **手术部位**: 身体各部位可单独操作 (`bodyparts/`)
- **手术种类**: 器官移植、肢体修复、开颅、开胸 (`operations/`)
- **手术工具**: 手术刀、止血钳、骨锯、牵开器、电凝器、手术盘
- **高级工具**: `advanced` 系列手术器械 (性能更好)

#### 伤口系统 (`code/datums/wounds/`)

- **钝伤** (blunt): 撞击、钝器打击
- **割伤** (slash): 刀具、利刃
- **穿刺** (pierce): 子弹、刺击
- **烧伤** (burns): 火焰、激光、等离子灼伤
- **骨折** (bones): 骨骼断裂、需要夹板/手术
- **颅骨裂缝** (cranial fissure): 头部重击
- **组织损失** (loss): 严重损伤导致组织缺失
- **疤痕系统** (scars): 受伤后留疤, 永久影响美观/功能

#### 疾病系统 (`code/datums/diseases/`)

共约 **25+ 种疾病**，涵盖:

| 疾病 | 效果 |
|---|---|
| **Cold / Flu** (感冒/流感) | 喷嚏、咳嗽、体温异常 |
| **GBS** (重症真菌感染) | 高致死率 |
| **Brainrot** (脑腐病) | 智力下降、意识模糊 |
| **Beesease** (蜜蜂病) | 怪异症状 |
| **Pierrot's Throat** (小丑喉) | 不受控制地讲笑话 |
| **Anxiety** (焦虑症) | 心理异常 |
| **Magnitis** (磁化症) | 身体被金属吸引 |
| **Parrotpossession** (鹦鹉附体) | 说出重复违禁词语 |
| **DNA-spread** (DNA 扩散) | 基因变异的传染病 |
| **Advanced Diseases** (高级疾病) | 可编程 DIY 病毒系统, 自定义症状/传播方式/抗性 |

#### 突变系统 (`code/datums/mutations/`)

- **Hulk** (浩克): 大力 + 破坏
- **Fire Breath** (喷火): 吐出火焰
- **Chameleon** (变色龙): 隐形皮肤
- **Cold/Hot**: 温度适应
- **Farsight**: 远视
- **Antenna**: 触角感知
- **Autotomy**: 断肢逃生

---

### 2.5 化学/药剂系统

#### 化工合成 (`code/modules/reagents/chemistry/recipes.dm` — **565 条合成配方**)

从基础化学到复杂药物的完整配方系统。涵盖:

- 基础化学合成
- 医药制药 (止痛/麻醉/治疗/解毒)
- 毒素与毒药
- 兴奋剂与镇定剂
- 爆炸物和燃料
- 酸碱和腐蚀剂

#### 水培种植 (`code/modules/hydroponics/`)

- **种子系统**: 652 行种子定义, 涵盖数十种作物
- **植物基因**: `plant_genes.dm` 基因拼接
- **水培机**: 光照/营养/温度管理
- **蜜蜂养殖**: `beekeeping/`
- **生物反应器**: `biogenerator.dm` 将生物质转化为有机物
- **嫁接**: `grafts.dm` 植物嫁接

#### 食物与饮品 (`code/modules/food_and_drinks/`)

- 烤箱/微波炉/煎锅配方向导 (`recipes/`)
- 酒保调制鸡尾酒 (`restaurant/`)
- 食物/食材处理 (`food/`)
- 苏打机/咖啡机 (`machinery/`)

---

### 2.6 工程与电力系统

#### 大气系统 (`code/modules/atmospherics/`)

TG 系最著名的深度系统:

- **气体混合**: `gasmixtures/` 气体物理引擎 — 压力/温度/摩尔浓度模拟
- **管道网络**: `machinery/` 管道/阀门/泵/过滤器
- **环境模拟**: `environmental/` 空间温度、气体扩散
- 支持 **多 Z 层** 气体流动 (上下层互通)

#### 电力系统 (`code/modules/power/`)

- **SMES** 储能电池
- **APC** 区域供电控制器 (`apc/`)
- **太阳能板** (`solar/`)
- **超物质引擎 (Supermatter)** — 全站主反应堆
- **奇点引擎 (Singularity)** — 替代能源, 黑洞生成
- **重力发生器** (`gravitygenerator.dm`)
- **RTG** 放射性同位素热电发生器 (`rtg.dm`)
- **便携式发电机** (`port_gen.dm`)

#### 管道系统

- 各种管道铺设 (`pipes_n_cables`)
- 管道清洁器 (`pipecleaners.dm`)

---

### 2.7 武器与战斗系统

#### 枪械 (`code/modules/projectiles/guns/`)

完整枪械体系, 含弹药分类、弹道计算:

- **能量武器**: E-Guns, laser guns, disablers
- **实弹武器**: 手枪/步枪/冲锋枪/霰弹枪
- **弹药**: 各种口径 `ammunition/`
- **弹匣/弹药盒**: `boxes_magazines/`
- **枪管改装**: `pins.dm` 枪管刻字/改装

#### 武术系统 (`code/datums/martial/`)

| 武术 | 特性 |
|---|---|
| **Boxing** (拳击) | 基础近战 |
| **CQC** (近战格斗) | 安全部精英战斗术 |
| **Sleeping Carp** (睡鲤拳) | 防反流武术, 可空手接子弹 |
| **Plasma Fist** (等离子拳) | 超强终结技 |
| **Wrestling** (摔跤) | 锁技/投技 |
| **Kaza Ruk** | 外星格斗术 |
| **Mushpunch** (蘑菇拳) | 蘑菇人特有 |
| **Psychotic Brawl** (疯人乱斗) | |
| **Spider's Bite** (蜘蛛之咬) | |

---

### 2.8 研究与科技树

代码位于 `code/modules/research/`:

#### Techweb 系统 (`techweb/`)

网格化科技树 — 各个节点解锁不同的科技:

- 设计图 (`designs/`) — 数百种可打印物品
- **破坏性分析仪**: 拆解物品获取科技点数
- **实验器**: `experimentor/` 高风险实验系统
- **军火研究**: `ordnance/` 爆炸物研究
- **外星生物**: `xenobiology/` 史莱姆提取物研究
- **异物质**: `anomaly/` 空间异常精炼 (`anomaly_refinery.dm`)
- **股票零件**: `stock_parts/` 零件升级系统

---

### 2.9 货运与经济

代码位于 `code/modules/cargo/`:

- **订购系统**: 军需官通过控制台下单, 货运舱空投包裹
- **赏金**: `bounties/` 完成特定目标获取额外资金
- **出口**: `exports/` 向外输送稀有物资获利
- **市场**: `markets/` 材料市场价格波动
- **优惠券**: `coupon.dm`
- **补给舱**: `supplypod.dm` 空投舱物理系统
- **物资包**: `packs/` 不同类型的物资订购包

---

### 2.10 采矿与勘探

代码位于 `code/modules/mining/`:

- **熔岩地 (Lavaland)**: `lavaland/` 岩浆行星勘探
- **太空舱**: `aux_base.dm` 辅助基地
- **富尔顿回收系统**: `fulton.dm` — 标记物品/敌人直接拉回
- **矿物处理**: [`boulder_processing/`, `machine_processing.dm`, `machine_redemption.dm`]
- **自动采矿机**: `machine_silo.dm`
- **矿石与硬币**: `ores_coins.dm`
- **庇护所圆盘**: `shelters.dm` — 展开即用的临时住所

---

### 2.11 交易/合成与宗教

#### 宗教系统 (`code/modules/religion/`)

- 多个教派 (`religion_sects.dm`)
- 仪式 (`rites.dm`) — 活祭、净化、祝福
- 宗教建筑 (`religion_structures.dm`)
- 敬拜机器人 (`deaconize.dm`)
- 祭祀火焰 (`pyre/`)

#### 炼金/制作系统

- **制造工坊**: `code/modules/manufactorio/`
- **库存零件**: 工件升级、MOD 装备制造

---

### 2.12 太空导航与运输

#### 穿梭机系统 (`code/modules/shuttle/`)

- 货运穿梭机、客运穿梭机
- 移动太空港 (`mobile_port/`)
- 固定太空港 (`stationary_port/`)

#### 地图系统 (`_maps/`)

| 地图名 | 特点 |
|---|---|
| **Metastation** | 经典布局 |
| **DeltaStation** | 大型站台 |
| **Icebox** | 冰行星环境站 |
| **Kilostation** | 小型站台 |
| **Blueshift** | |
| **NebulaStation** | (Nova 特有图) |
| **CatwalkStation** | |
| **Tramstation** | 有轨道穿梭车的站台 |
| 另有随机废墟/远征地图 | |

---

### 2.13 玩家个性与特质

#### 特质系统 (`code/datums/quirks/`)

开局可选的特殊个性/身体特质:

- **正面特质**: 快速学习、适应力强、酒精抗力等
- **中性特质**: 素食主义、多指症等
- **负面特质**: 失明、癫痫、色盲、血友病、肢端肥大等

#### 情绪系统 (`code/datums/mood.dm`)

- 基于环境/事件/社交的动态情绪
- 影响工作效率和社交互动
- 多种情绪事件 (`mood_events/`)

---

### 2.14 NovaSector 新增内容 (modular_nova)

`modular_nova/modules/` 下有 **上百个模块**, 主要亮点:

| 模块 | 内容 |
|---|---|
| **bitrunning** | 终端穿越 — 进入虚拟空间执行任务, 类似赛博空间渗透 |
| **armaments** | 军火站系统, 自动化武器装配 |
| **xenomorph** | 完整异形蜂巢机制 |
| **solfed_mechs** | 机甲战斗系统 |
| **shadekin** | 暗影猫族 (新种族) |
| **advanced_shuttles** | 高级飞船引擎 |
| **synths** | 合成人种族 |
| **bluespace_miner** | 蓝空间采矿 |
| **alcohol_processing** | 酿酒工业化 |
| **cellguns** | 细胞武器 |
| **space_vines** | 太空藤蔓灾害事件 |
| **fabcraft** | 以物易物合成系统 |
| **awaymissions_nova** | 远征任务模块 |
| **star_only** | 星空场景扩展 |
| **xenoarch / xenoarchartifacts** | 外星考古系统 |
| **station_traits** | 空间站全局特性 (开局随机选择) |
| **capitalism** | 资本主义经济系统扩展 |

---

### 2.15 天关专属改动 (modular_tianguan)

| 模块 | 内容 |
|---|---|
| **Alert_level** | 警报等级自定义 |
| **cargo_tweaks** | 货运系统微调 |
| **emergency_shuttle_cc** | 中央指挥部紧急穿梭机调整 |
| **medical_blanks** | 医疗记录模板 |
| **sop_book** | 标准操作程序手册 |
| **uplink_item** | 加密 Uplink 道具定制 |
| **security_officer.dm** | 安全官额外获得迷你能量枪 |

---

## 三、玩法总结

### SS13 的核心玩法循环

```
开局（分配职业 + 随机生成反派）
  ↓
日常（各司其职：工程供电/医疗待命/科研升级/安保巡逻）
  ↓
事件触发（随机灾害/反派行动/玩家矛盾）
  ↓
危机升级（电力崩溃/管道爆裂/病毒爆发/AI叛变/外星入侵）
  ↓
冲突高潮（全员对抗反派/部门内战/撤离/核爆倒计时）
  ↓
终局（紧急穿梭机撤离/全灭/反派胜利/英勇化解）
```

### 关键设计哲学

1. **高度分工协作**: 30+ 种职业各司其职, 无法单打独斗通关
2. **玩家驱动叙事**: 反派玩家创造剧情冲突，每次对局不同
3. **深度模拟**: 气体物理/电力网络/管道系统/化学配比, 不是"看起来像"而是真的在模拟
4. **系统互联**: 化学合成×医学治疗、电力供应×生命维持、矿石开采×R&D研发, 各系统深度交织
5. **蝴蝶效应**: 一个小故障（管道泄漏）可能连锁演变成全站灾难
6. **高自由度**: 背叛/拉帮结派/私下交易/黑市买卖, 完全玩家驱动

### 数值规模

| 统计项目 | 数据 |
|---|---|
| 可玩职业 | **40+** 个 |
| 反派类型 | **33** 种 |
| 可玩种族 | **20+** 种 |
| 化学合成配方 | **565** 条 |
| 已定义疾病 | **25+** 种 |
| 武术流派 | **11** 种 |
| 地图站台 | **13+** 张主地图 + 随机废墟 |
| 研究科技节点 | 数十个 techweb 节点, 数百种设计图 |
| MOD 模块 | **200+** 个 Nova 扩展模块 |
| 核心代码 | 数十万行 DM 代码 |
