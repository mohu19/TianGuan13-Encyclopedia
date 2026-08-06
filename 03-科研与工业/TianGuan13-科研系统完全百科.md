# 天关 — 科研系统完全百科

> **项目**: TianGuan13 (Nova Sector → /tg/station)
> **代码**: `code/modules/research/`（33,604 行）+ `code/modules/experisci/`（1,862 行实验）
> **范围**: 科技网（techweb）、科技树 117 节点、实验系统、设计图、科研设备、异星生物、军械实验

---

## 目录

- [第一卷 · 科研架构](#第一卷--科研架构)
- [第二卷 · 科技树全录（117 节点）](#第二卷--科技树全录117-节点)
- [第三卷 · 实验系统](#第三卷--实验系统)
- [第四卷 · 科研设备](#第四卷--科研设备)
- [第五卷 · 设计图与制造](#第五卷--设计图与制造)（1,236 设计图/40 类）
- [第六卷 · 异星生物（Xenobiology）](#第六卷--异星生物xenobiology)（21 色史莱姆/交叉繁殖/缸培育）
- [第七卷 · 军械实验与论文](#第七卷--军械实验与论文)
- [第八卷 · 异常与小工具](#第八卷--异常与小工具)
- [第九卷 · 基因学（Genetics）](#第九卷--基因学genetics)（68 突变/DNA 系统/**9.7 细胞学/9.8 DNA 融合器**）
- [附录 · 代码路径索引](#附录--代码路径索引)

---

# 第一卷 · 科研架构

## 1.1 科技网 Techweb 核心

**代码**: `code/modules/research/techweb/_techweb.dm`（570 行）

科技网（techweb）是科研系统的核心 datum，存储已解锁研究，挂在控制台/服务器/科技盘上（**非全局**）。

| 字段 | 说明 |
|---|---|
| `researched_nodes` | 已解锁节点（id=TRUE） |
| `visible_nodes` | 可见节点（不一定能研究） |
| `available_nodes` | 可立即研究（需求满足） |
| `researched_designs` | 可用设计图 |
| `custom_designs` | 科技盘插入的自定义设计 |
| `hidden_nodes` | 隐藏节点（需求满足时显示） |
| `deconstructed_items` | 已分解物品（防刷点） |
| `research_points` | 研究点数（多类型） |
| `research_logs` | 研究日志 |
| `available/completed_experiments` | 实验状态 |
| `consoles_accessing` / `techweb_servers` | 关联控制台/服务器 |

**科技网类型**（techweb_types.dm）：

| 类型 | ID | 说明 |
|---|---|---|
| **SCIENCE** | SCIENCE | 主站科技网，Nanotrasen，自动生点 |
| **CHARLIE** | CHARLIE | 旧站科技网，开局含"实验解剖"科技 |
| **ADMIN** | ADMIN | 管理员全解锁 |
| **D1SK** | D1SK | 科技盘 |
| **autounlocking** | — | 自动解锁（自动车床/肢体培育/生物生成器/冶炼器） |

## 1.2 研究点数

| 项目 | 值 |
|---|---|
| 点数类型 | 单类型 **"General Research"**（通用研究） |
| 服务器产出 | **每秒 +1 点**（单台服务器） |
| 主服务器 | 被毁/HDD 被盗 → **研究速度减半** |
| 破坏分析仪 | 分解物品→获得点数（每类物品限一次） |

**科技成本 5 档**（`__DEFINES/research.dm`）：

| 档位 | 点数 |
|---|---|
| Tier 1 | 40 |
| Tier 2 | 80 |
| Tier 3 | 120 |
| Tier 4 | 160 |
| Tier 5 | 200 |

## 1.3 科技节点 datum

**代码**: `techweb/_techweb_node.dm`（135 行）

| 字段 | 说明 |
|---|---|
| `id` / `display_name` | 内部 ID / 显示名 |
| `hidden` | 是否开局隐藏 |
| `experimental` | 是否 BEPIS 随机奖励科技 |
| `starting_node` | 是否开局可用 |
| `prereq_ids` | 前置节点链 |
| `design_ids` | 解锁的设计图 |
| `required_items_to_unlock` | 需分解的物品（解锁条件） |
| `research_costs` | 研究点数成本 |
| `required_experiments` | 所需完成的实验 |
| `discount_experiments` | 折扣实验（完成可减价） |
| `announce_channels` | 研究完成公告频道 |

**研究流程**：控制台 → 选择可见节点 → 满足前置+实验 → 支付点数 → 解锁设计图/新节点。

## 1.4 与已完成系统的联动（科技树交叉）

| 已读系统 | 关联科技节点 | 联动内容 |
|---|---|---|
| **工程/气体** | Gas Compression/Controlled Plasma/Fusion | 气体压缩/等离子控制/聚变（含 HFR 相关设计） |
| **水培/植物** | Hydroponics/Artificial Selection/Botany Equipment | 水培设备/人工选育（种子库科技） |
| **化学** | Chemical Synthesis | 化学合成设备（化学文档的医疗/爆炸配方联动） |
| **手术/器官** | Surgery 系列/Xenobiology/Cytology | 手术工具升级/异星器官/细胞学（基因工程） |
| **战斗/武器** | Basic Arms/Exotic Ammo/Beam Weapons/Explosives | 武器设计图（能量枪/弹药/爆炸物） |
| **拉瓦兰/采矿** | Mining 系列/Anomaly Research | 采矿科技/异常核心（超物质剑设计图） |
| **病毒** | Bio Scan/Cytology | 生物扫描/细胞学（病毒培养设备） |

---

# 第二卷 · 科技树全录（117 节点）

**代码**: `techweb/nodes/`（17 文件 3,342 行）
**统计**: 117 节点（16 起始 / 14 隐藏 / 10 BEPIS 实验）/ 成本 5 档（40-200 点）

## 2.1 起始节点（16 个，开局即解锁）

| 节点 | 名称 | 说明 |
|---|---|---|
| TECHWEB_NODE_PARTS | Essential Stock Parts | 基础零件 19 种：微伺服/基础电池/基础电容/基础电池芯/基础物质仓/基础微型激光/基础扫描/高容量电池/高容量电池芯/微型电源/冷凝器/点火器/红外发射器/接近传感器/信号器/定时器/语音分析器/健康传感器/闪光器 |
| TECHWEB_NODE_ATMOS | Atmospherics | 大气学基础 |
| TECHWEB_NODE_CONSTRUCTION | Construction | 建造基础 |
| TECHWEB_NODE_MECH_ASSEMBLY | Exosuit Assembly | 机甲组装 |
| TECHWEB_NODE_MEDBAY_EQUIP | Medbay Equipment | 医疗设备 |
| TECHWEB_NODE_MATERIAL_PROC | Material Processing | 材料加工 |
| TECHWEB_NODE_MOD_SUIT | Modular Suits | 模块化服装 |
| TECHWEB_NODE_FUNDIMENTAL_SCI | Fundamental Science | 基础科学 |
| TECHWEB_NODE_ROBOTICS | Robotics | 机器人学 |
| TECHWEB_NODE_BASIC_ARMS | Basic Arms | 基础武器 |
| TECHWEB_NODE_OFFICE_EQUIP | Office Equipment | 办公设备 |
| TECHWEB_NODE_CAFETERIA_EQUIP | Cafeteria Equipment | 食堂设备 |
| TECHWEB_NODE_FISHING_EQUIP | Fishing Equipment | 钓鱼设备 |
| TECHWEB_NODE_BOTANY_EQUIP | Botany Equipment | 植物学设备 |
| TECHWEB_NODE_PROGRAMMING | Programming | 编程 |
| TECHWEB_NODE_AUGMENTATION | Augmentation | 增强植入 |

## 2.2 工程类（engi_nodes.dm，11 节点）

| 节点 | 名称 | 成本 |
|---|---|---|
| TECHWEB_NODE_GAS_COMPRESSION | Gas Compression | T1 40 |
| TECHWEB_NODE_PLASMA_CONTROL | Controlled Plasma | T2 80 |
| TECHWEB_NODE_FUSION | Fusion | T3 120 |
| TECHWEB_NODE_EXP_TOOLS | Experimental Tools | T4 160 |
| TECHWEB_NODE_RCD_UPGRADE | Rapid Construction Device Upgrades | T5 200 |
| TECHWEB_NODE_PARTS_UPG | Upgraded Parts | T2 80 |
| TECHWEB_NODE_PARTS_ADV | Advanced Parts | T3 120 |
| TECHWEB_NODE_PARTS_BLUESPACE | Bluespace Parts | T4 160 |
| TECHWEB_NODE_TELECOMS | Telecommunications Technology | T5 200 |
| TECHWEB_NODE_ENERGY_MANIPULATION | Energy Manipulation | T1 40 |
| TECHWEB_NODE_SHUTTLE_ENG | Shuttle Engineering | T1 40 |
| TECHWEB_NODE_HOLOGRAPHICS | Holographics | T2 80 |
| TECHWEB_NODE_HUD | Integrated HUDs | T3 120 |
| TECHWEB_NODE_NIGHT_VISION | Night Vision Technology | T4 160 |

## 2.2b 大气科技树（atmos_nodes.dm，6 节点）

> **源码**: `code/modules/research/techweb/nodes/atmos_nodes.dm`。此前文档仅 2.1 起始节点提了基础大气，完整大气分支未展开，现补全。

| 节点 | 名称 | 成本 | 描述 | 关键设计 |
|---|---|---|---|---|
| TECHWEB_NODE_ATMOS | Atmospherics 大气学 | 起始 | 维护站内空气与生命维持系统 | 大气控制台/警报器/热机/加热器/净化器/各类气罐/灭火器/气体过滤器/分析仪 |
| TECHWEB_NODE_GAS_COMPRESSION | Gas Compression 气体压缩 | T1 40 | 高压气体蕴含巨大能量潜力 | 罐压缩机/泵/紧急氧气/涡轮部件（压缩/转子/定子）/大气热交换机/气动密封/大焊接工具 |
| TECHWEB_NODE_PLASMA_CONTROL | Controlled Plasma 可控等离子 | T2 80 | 高压气体与电的实验，结晶化与可控等离子 | 电解器/管道净化器/PacMan 发电机/机甲发电机/等离子切割机 |
| TECHWEB_NODE_FUSION | Fusion 聚变 | T3 120 | 研究聚变反应堆技术实现可持续高效能源 | 高级灭火器/扳手/RPD/引擎护目镜/结晶器 |
| TECHWEB_NODE_EXP_TOOLS | Experimental Tools 实验工具 | T4 160 | 增强站内工具的功能与多样性 | 平板打包器/手钻/外焊枪/救生钳/远程分析仪/RTD/RCD/磁靴 |
| TECHWEB_NODE_RCD_UPGRADE | RCD Upgrades 快速建造升级 | T5 200 | RCD/RPD 的新设计与增强 | 筒仓链接/防打断/冷却/框架/装饰/简易电路/卸螺栓升级 |

## 2.3 机甲类（mech_nodes.dm，14 节点）

| 节点 | 名称 | 成本 |
|---|---|---|
| TECHWEB_NODE_MECH_EQUIPMENT | Expedition Equipment | T1 40 |
| TECHWEB_NODE_MECH_CLOWN | Funny Robots | T1 40 |
| TECHWEB_NODE_MECH_MEDICAL | Medical Exosuit | T2 80 |
| TECHWEB_NODE_MECH_MINING | Mining Exosuit | T2 80 |
| TECHWEB_NODE_MECH_COMBAT | Combat Exosuits | T2 80 |
| TECHWEB_NODE_MECH_ASSAULT | Assault Exosuits | T3 120 |
| TECHWEB_NODE_MECH_LIGHT | Light Combat Exosuits | T3 120 |
| TECHWEB_NODE_MECH_HEAVY | Heavy Exosuits | T4 160 |
| TECHWEB_NODE_MECH_INFILTRATOR | Infiltration Exosuits | T4 160 |
| TECHWEB_NODE_MECH_ENERGY_GUNS | Exosuit Energy Guns | T4 160 |
| TECHWEB_NODE_MECH_FIREARMS | Exosuit Firearms | T5 200 |
| TECHWEB_NODE_MECH_HEAVY_ARMS | Heavy Exosuit Firearms | T5 200 |
| TECHWEB_NODE_MECH_EQUIP_BLUESPACE | Bluespace Exosuit Equipment | T5 200 |

## 2.4 增强/机器人（cyborg_nodes.dm + robo_nodes.dm）

| 节点 | 名称 | 成本 |
|---|---|---|
| TECHWEB_NODE_CYBERNETICS | Cybernetics | T1 40 |
| TECHWEB_NODE_BORG_SERVICES | Service Cyborg Upgrades | T2 80 |
| TECHWEB_NODE_BORG_MINING | Mining Cyborg Upgrades | T2 80 |
| TECHWEB_NODE_BORG_MEDICAL | Medical Cyborg Upgrades | T3 120 |
| TECHWEB_NODE_BORG_UTILITY | Utility Cyborg Upgrades | T3 120 |
| TECHWEB_NODE_BORG_ENGI | Engineering Cyborg Upgrades | T3 120 |
| TECHWEB_NODE_PASSIVE_IMPLANTS | Passive Implants | T1 40 |
| TECHWEB_NODE_EXODRONE | Exploration Drones | T1 40 |
| TECHWEB_NODE_AI | Artificial Intelligence | T1 40 |
| TECHWEB_NODE_AI_LAWS | Advanced AI Upgrades | T3 120 |

## 2.5 医疗类（medbay_nodes.dm + biology_nodes.dm + surgery_nodes.dm）

| 节点 | 名称 | 成本 |
|---|---|---|
| TECHWEB_NODE_CHEM_SYNTHESIS | Chemical Synthesis | T1 40 |
| TECHWEB_NODE_MEDBAY_EQUIP_ADV | Advanced Medbay Equipment | T3 120 |
| TECHWEB_NODE_CRYOSTASIS | Cryostasis | T4 160 |
| TECHWEB_NODE_BIO_SCAN | Biological Scan | T1 40 |
| TECHWEB_NODE_CYTOLOGY | Cytology | T2 80 |
| TECHWEB_NODE_XENOBIOLOGY | Xenobiology | T3 120 |
| TECHWEB_NODE_GENE_ENGINEERING | Gene Engineering | T4 160 |
| TECHWEB_NODE_SURGERY | Improved Wound-Tending | T1 40 |
| TECHWEB_NODE_SURGERY_ADV | Advanced Surgery | T2 80 |
| TECHWEB_NODE_SURGERY_EXP | Experimental Surgery | T3 120 |
| TECHWEB_NODE_SURGERY_TOOLS | Advanced Surgery Tools | T4 160 |

## 2.6 采矿/植物（mining_nodes.dm + biology_nodes.dm 植物部分）

| 节点 | 名称 | 成本 |
|---|---|---|
| TECHWEB_NODE_MINING | Mining Technology | T1 40 |
| TECHWEB_NODE_LOW_PRESSURE_EXCAVATION | Low-Pressure Excavation | T2 80 |
| TECHWEB_NODE_PLASMA_MINING | Plasma Beam Mining | T3 120 |
| TECHWEB_NODE_BITRUNNING | Bitrunning Technology | T3 120 |
| TECHWEB_NODE_MINING_ADV | Advanced Mining Technology | T4 160 |
| TECHWEB_NODE_HYDROPONICS | Hydroponics | T2 80 |
| TECHWEB_NODE_SELECTION | Artificial Selection | T3 120 |

## 2.7 安保/武器（security_nodes.dm）

| 节点 | 名称 | 成本 |
|---|---|---|
| TECHWEB_NODE_SEC_EQUIP | Security Equipment | T1 40 |
| TECHWEB_NODE_RIOT_SUPRESSION | Riot Supression | T2 80 |
| TECHWEB_NODE_EXPLOSIVES | Explosives | T3 120 |
| TECHWEB_NODE_EXOTIC_AMMO | Exotic Ammunition | T4 160 |
| TECHWEB_NODE_ELECTRIC_WEAPONS | Electric Weaponry | T3 120 |
| TECHWEB_NODE_BEAM_WEAPONS | Advanced Beam Weaponry | T4 160 |

## 2.8 服务/杂项（service_nodes.dm + circuit_nodes.dm）

| 节点 | 名称 | 成本 |
|---|---|---|
| TECHWEB_NODE_SANITATION | Advanced Sanitation Technology | T2 80 |
| TECHWEB_NODE_CONSOLES | Civilian Consoles | T1 40 |
| TECHWEB_NODE_GAMING | Gaming | T2 80 |
| TECHWEB_NODE_FOOD_PROC | Food Processing | T2 80 |
| TECHWEB_NODE_FISHING_EQUIP_ADV | Advanced Fishing Tools | T2 80 |
| TECHWEB_NODE_MARINE_UTIL | Marine Utility | T3 120 |
| TECHWEB_NODE_FISHING_ANOMALOUS | Anomalous Fishing | T1 40 |
| TECHWEB_NODE_CIRCUIT_SHELLS | Advanced Circuit Shells | T1 40 |
| TECHWEB_NODE_BCI | Brain-Computer Interface | T2 80 |
| TECHWEB_NODE_PROGRAMMED_ROBOT | Programmed Robot | T2 80 |
| TECHWEB_NODE_PROGRAMMED_SERVER | Programmed Server | T3 120 |

## 2.9 模块化服装（modsuit_nodes.dm，10 节点）

| 节点 | 名称 | 成本 |
|---|---|---|
| TECHWEB_NODE_MOD_EQUIP | Modular Suit Equipment | T1 40 |
| TECHWEB_NODE_MOD_SERVICE | Civilian Modular Suits | T1 40 |
| TECHWEB_NODE_MOD_ENTERTAINMENT | Entertainment Modular Suits | T1 40 |
| TECHWEB_NODE_MOD_MEDICAL | Medical Modular Suits | T2 80 |
| TECHWEB_NODE_MOD_ENGI | Engineering Modular Suits | T2 80 |
| TECHWEB_NODE_MOD_SECURITY | Security Modular Suits | T2 80 |
| TECHWEB_NODE_MOD_MEDICAL_ADV | Field Surgery Modules | T3 120 |
| TECHWEB_NODE_MOD_ENGI_ADV | Advanced Engineering Modular Suits | T3 120 |
| TECHWEB_NODE_MOD_ANOMALY | Anomalock Modular Suits | T4 160 |

## 2.10 基础科学（research_nodes.dm）

| 节点 | 名称 | 成本 |
|---|---|---|
| TECHWEB_NODE_BLUESPACE_THEORY | Bluespace Theory | T1 40 |
| TECHWEB_NODE_APPLIED_BLUESPACE | Applied Bluespace Research | T2 80 |
| TECHWEB_NODE_BLUESPACE_TRAVEL | Bluespace Travel | T3 120 |
| TECHWEB_NODE_ANOMALY_RESEARCH | Anomaly Research | T3 120 |
| TECHWEB_NODE_ANOMALY_SHELLS | Advanced Anomaly Shells | T5 200 |

## 2.11 隐藏/特殊节点（15 个）

| 节点 | 名称 | 成本 | 说明 |
|---|---|---|---|
| TECHWEB_NODE_ALIEN_ENGI | Alien Engineering | T2 80 | 隐藏，外星工程 |
| TECHWEB_NODE_ALIEN_TECH | Alien Technology | T2 80 | 隐藏，外星科技 |
| TECHWEB_NODE_ALIEN_SURGERY | Alien Surgery | T5 200 | 隐藏，外星手术 |
| TECHWEB_NODE_OLDSTATION_SURGERY | Experimental Dissection | T1 40 | 隐藏，旧站解剖（CHARLIE 开局有） |
| TECHWEB_NODE_SYNDICATE_BASIC | Illegal Technology | T5 200 | 隐藏，非法科技 |
| TECHWEB_NODE_UNREGULATED_BLUESPACE | Unregulated Bluespace Research | T1 40 | 隐藏，非管制蓝空间 |

**BEPIS 实验节点（10 个，科技盘随机奖励，全部 hidden+experimental）**：

| 节点 | 名称 | 说明 |
|---|---|---|
| TECHWEB_NODE_LIGHT_APPS | Illumination Applications | 灯光应用 |
| TECHWEB_NODE_ADVANCED_OFFICE | Advanced Office Applications | 高级办公应用 |
| TECHWEB_NODE_SPECIALIZED_ENG | Specialized Engineering | 专业化工程 |
| TECHWEB_NODE_AUS_SECURITY | Australicus Security Protocols | 澳斯塔利库斯安保协议 |
| TECHWEB_NODE_ENHANCED_INTERROGATION | Enhanced Interrogation Technology | 强化审讯技术 |
| TECHWEB_NODE_ADVANCED_STICKY | Advanced Sticky Technology | 高级粘性科技 |
| TECHWEB_NODE_ADVANCED_GRAPPLE | Advanced Grapple Technology | 高级抓钩科技 |
| TECHWEB_NODE_EXPERIMENTAL_MODSUITS | Experimental Modular Suits | 实验性 MOD 服 |
| TECHWEB_NODE_POSISPHERE | Experimental Spherical Positronic Brain | 实验性球形正电子脑 |
| TECHWEB_NODE_DONK_SHELL | Donk Co. Failed Products Schematics | Donk 公司失败品图纸 |

---

# 第三卷 · 实验系统

**代码**: `code/modules/experisci/experiment/`（18 文件 1,862 行）+ `code/modules/research/experimentor/`（804 行）

## 3.1 实验系统（Experiments）· 78 种（源码全量）

实验=完成特定任务（扫描物品/做行为）→ 获得研究点数+科技折扣。科技节点的 `required_experiments`/`discount_experiments` 关联。

> 源码实际 **78 个具名实验**（此前文档声明的 84 种是估算，含抽象基类）。按源码类型分组：

| 实验类型 | 数量 |
|---|---|
| 尸检实验 autopsy | 4 |
| 探索实验 exploration | 4 |
| 军械实验 ordnance（爆炸 5 + 气体罐 5 + 基类 1） | 11 |
| 物理实验 physical | 3 |
| 扫描实验 scanning（史莱姆 3/细胞学 4/卫生 1/材料 10/植物 2/机器 10/机甲 2/人类 5/试剂 2/点数 3/鱼 5/基类 4） | 56 |

### 全量实验清单（78 个）

| 实验 | 说明 |
|---|---|
| **Autopsy Experiment** | 尸检实验（基类） |
| **Human Autopsy Experiment** | 人类尸检 |
| **Non-human Autopsy Experiment** | 非人尸检 |
| **Xenomorph Autopsy Experiment** | 异形尸检 |
| **Exploration Experiment** | 探索实验（基类） |
| **Scan Asteroid Belt** | 扫描小行星带 |
| **Deep scan a black hole** | 深扫黑洞 |
| **Random Exoscan Experiment** | 随机外骨骼扫描 |
| **Toxin Research** | 毒素研究（基类） |
| **Low-Yield Explosives** | 低当量爆炸 |
| **High-Yield Explosives** | 高当量爆炸 |
| **Hydrogen Explosives** | 氢爆炸 |
| **Noblium Explosives** | 超氮爆炸 |
| **Reactionless Explosives** | 无反应爆炸 |
| **Nitrous Oxide Gas Shells** | N₂O 气体罐 |
| **Plasma Gas Shells** | 等离子气体罐 |
| **BZ Gas Shells** | BZ 气体罐 |
| **Hypernoblium Gas Shells** | 超氮气体罐 |
| **Halon Gas Shells** | 哈龙气体罐 |
| **Physical Experiment** | 物理实验（基类） |
| **Extreme Cooking Experiment** | 极限烹饪实验 |
| **Playtesting Experiences** | 游戏测试体验 |
| **Base Slime Experiment** | 基础史莱姆调查 |
| **Challenging Slime Survey** | 挑战史莱姆调查 |
| **Expert Slime Survey** | 专家史莱姆调查 |
| **Basic Cytology Scanning Experiment** | 基础细胞学扫描 |
| **Advanced Cytology Scanning Experiment** | 高级细胞学扫描 |
| **Advanced Cytology Scanning Experiment One** | 高级细胞学扫描 1 |
| **Advanced Cytology Scanning Experiment Two** | 高级细胞学扫描 2 |
| **Station Hygiene Inspection** | 空间站卫生检查 |
| **Biological Material Scanning Experiment** | 生物材料扫描 |
| **Low Grade Material Scanning Experiment** | 低级材料扫描 |
| **Medium Grade Material Scanning Experiment** | 中级材料扫描 |
| **Medium Grade Material Scanning Experiment One** | 中级材料扫描 1 |
| **Medium Grade Material Scanning Experiment Two** | 中级材料扫描 2 |
| **Medium Grade Material Scanning Experiment Three** | 中级材料扫描 3 |
| **High Grade Material Scanning Experiment** | 高级材料扫描 |
| **High Grade Material Scanning Experiment One** | 高级材料扫描 1 |
| **High Grade Material Scanning Experiment Two** | 高级材料扫描 2 |
| **High Grade Material Scanning Experiment Three** | 高级材料扫描 3 |
| **Wild Biomatter Mutation Sample** | 野生生物质突变样本 |
| **Unique Biomatter Mutation Sample** | 独特生物质突变样本 |
| **Advanced Stock Parts Benchmark** | 高级零件基准（车床） |
| **Bluespace Machinery Attunement** | 蓝空间机器调谐 |
| **High Efficiency Parts Applications Test** | 高效零件应用测试 |
| **Military-grade Mech Bay Setup** | 军级机甲舱配置 |
| **Upgraded Stock Parts Benchmark** | 升级零件基准（任意） |
| **Advanced Stock Parts Benchmark** | 高级零件基准（任意） |
| **High-power Micro-lasers Calibration** | 高功率微型激光校准 |
| **Advanced Capacitors Benchmark** | 高级电容基准 |
| **Advanced Scanning Modules Calibration** | 高级扫描模块校准 |
| **Power Cells Capacity Test** | 电池容量测试 |
| **Ultra-high-power Micro-lasers Calibration** | 超高功率微型激光校准 |
| **Exosuit Materials: Stress Failure Test** | 机甲材料：应力破坏测试 |
| **Exosuit Materials: Load Strain Test** | 机甲材料：载荷应变测试 |
| **Human Field Research: Genetic Mutations** | 人类实地研究：基因突变 |
| **Human Field Research: Divergent Biology** | 人类实地研究：异源生物 |
| **Human Field Research: Augmented Organs** | 人类实地研究：增强器官 |
| **Human Field Research: Skill Chip Implants** | 人类实地研究：技能芯片 |
| **Human Field Research: Full Augmentation** | 人类实地研究：全身改造 |
| **Pure Cryostylane Scan** | 纯低温硅扫描 |
| **Pure Haloperidol Scan** | 纯氟哌啶醇扫描 |
| **Bluespace Crystal Sampling** | 蓝空间水晶采样 |
| **Neutralized Anomaly Analysis** | 中和异常分析 |
| **Point Scanning Experiment** | 点数扫描实验（基类） |
| **Fish Scanning Experiment 1** | 鱼类扫描实验 1 |
| **Fish Scanning Experiment 2** | 鱼类扫描实验 2 |
| **Fish Scanning Experiment 3** | 鱼类扫描实验 3 |
| **Holographic Fish Scanning Experiment** | 全息鱼扫描实验 |
| **Fish Scanning Experiment 4** | 鱼类扫描实验 4 |
| **Scanning Experiment** | 扫描实验（基类） |
| **Base random scanning experiment** | 基础随机扫描实验 |
| **Cytology Scanning Experiment** | 细胞学扫描实验 |
| **Vat-Grown Slime Scan** | 缸培养史莱姆扫描 |
| **Botanical Scanning Experiment** | 植物学扫描实验（基类） |
| **Material Scanning Experiment** | 材料扫描实验（基类） |
| **Upgraded Machinery Scanning Experiment** | 升级机械扫描实验（基类） |
| **Machinery Pinpoint Stock Parts Scanning Experiment** | 机械精确定位零件扫描实验（基类） |
| **Fish Scanning Experiment 1** | 鱼类扫描实验 1 |

## 3.2 实验器 E.X.P.E.R.I-MENTOR

**代码**: `experimentor/experimentor.dm`（383 行）+ `experiments.dm`（364 行）

随机实验机器：放入物品 → 随机反应 → 得点数/物品/灾难。

| 反应 | 效果 |
|---|---|
| **Poke 戳** | 机械臂戳物品（低概率故障：伤害/消灭/扔出） |
| **Irradiate 辐射** | 辐射照射（克隆物品/辐射脉冲/变形） |
| **Gas 充气** | 充气（完美混合/爆炸） |
| **Obliterate 消灭** | 直接摧毁（得点数） |

**关键物品**（critical_items）：RCD/手雷/AI 卡/史莱姆提取物/TTV → **高概率灾难性反应**（爆炸/事故）。
**故障链**：极低→低→中概率 → 15 钝伤 / 转换物品 / 扔出砸人 / 辐射脉冲。
**有效物品**：零件类+15 权重、食物 1-4 权重（转换目标池）。

## 3.3 破坏分析仪（Destructive Analyzer）

**代码**: `destructive_analyzer.dm`（226 行）

| 机制 | 值 |
|---|---|
| 功能 | 分解物品 → **研究点数** + 解锁科技（required_items_to_unlock） |
| 防刷 | 每类物品限分解一次（deconstructed_items 记录） |
| 耗电 | 基础待机×2.5 |
| 特殊 | 不可分解 INDESTRUCTIBLE 物品；辛迪加螺丝刀可用 |

---

# 第四卷 · 科研设备

## 4.1 R&D 控制台

**代码**: `rdconsole.dm`（447 行）

| 机制 | 值 |
|---|---|
| 名称 | R&D Console（科研主操作台） |
| 连接 | 自动连接 3 格内设备（分解器/原型机/电路机） |
| 插槽 | 科技盘（tech_disk）+ 设计盘（design_disk） |
| 权限 | 锁定/解锁需科研权限（ACCESS_RESEARCH） |
| 功能 | 研究节点（enqueue 队列）/看科技树/管理实验/打印设计 |
| 连接 | multitool 可传输 techweb 数据 |

## 4.2 R&D 服务器

**代码**: `server.dm`（268 行）+ `server_control.dm`（88 行）

| 机制 | 值 |
|---|---|
| 功能 | **生成研究点数**（每秒 +1/台） |
| 停机 | EMP 60s / 控制台禁用 / 断电 |
| 名称 | 随机 4 位十六进制后缀 |
| **主服务器** | 1800 耐久、**HDD 盗窃目标**（4 步拆解：面板→撬→剪线→盗取）、被毁→**研究速度减半** |

## 4.3 原型机 Protolathe / 电路印刷机 / 自动车床

**代码**: `rdmachines.dm`（159 行）+ `designs.dm`（245 行）

| 机器 | 功能 |
|---|---|
| **原型机 Protolathe** | 按设计图打印物品（需科技解锁） |
| **电路印刷机 Circuit Imprinter** | 打印电路板 |
| **自动车床 Autolathe** | 基础材料物品（自解锁，可 hack） |
| **肢体培育机 Limbgrower** | 培育器官/肢体 |
| **生物生成器 Biogenerator** | 生物材料物品 |

## 4.4 RPED 零件更换器

**代码**: `part_replacer.dm`（201 行）

| 版本 | 功能 |
|---|---|
| 标准 RPED | 存储/排序/安装机器零件（按等级自动排序） |
| **蓝空间 RPED** | 远程+摄像头操作、更大容量 |

## 4.5 零件系统

**代码**: `stock_parts.dm`（254 行）+ `stock_parts/`（200 行）

| 零件 | 等级 |
|---|---|
| 电容 Capacitor | 基础/高级/超级/**二次方 quadratic** |
| 扫描模块 Scanning | 基础/高级/**相位 phasic/三相位 triphasic** |
| 伺服 Servo | 基础/**纳米 nano/皮可 pico/飞米 femto** |
| 微激光 Micro-laser | 基础/高/超/**极 ultra** |
| 物质箱 Matter bin | 基础/高级/超级 |

---

# 第五卷 · 设计图与制造

**代码**: `code/modules/research/designs/`（39 文件 14,352 行）+ `designs.dm`（245 行）
**总量**: **1,236 个设计图**（解析全部 DM 继承链+材料宏展开后核实）

## 5.1 制造机器分布

| 机器 | 设计图数 |
|---|---|
| **原型机 Protolathe** | 516 |
| 边远原型机 Away Protolathe | 482 |
| **机甲制造机 Exosuit Fab** | 300（含机器人模块） |
| **电路印刷机 Circuit Imprinter** | 247 |
| 边远电路印刷机 Away Imprinter | 230 |
| **自动车床 Autolathe** | 190 |
| 元件打印机 Component Printer（wiremod） | 112 |
| 肢体培育器 Limb Grower | 26 |
| 生物发生器 Biogenerator | 24 |
| 冶炼炉 Smelter | 8 |

## 5.2 设计图类别全表（40 类）

| 类别 | 数量 | 说明 |
|---|---|---|
| 机甲制造 MECHFAB | 204 | 机器人模块/部件 |
| 机械电路板 | 139 | 电解器/SMES/动力连接器等机器板 |
| 医疗 | 132 | 健康分析仪/尸检扫描仪/蓝空间烧杯 |
| 杂项 | 95 | 各种杂项物品 |
| 自动车床-服务 | 56 | 服务类 |
| 自动车床-材料 | 3 | 材料（铁/玻璃等基础板） |
| 机甲本体 | 53 | 机甲核心部件 |
| 武器 | 52 | 弹药/武器 |
| 自动车床-多部门 | 50 | 通用 |
| 电脑板 | 41 | 电脑电路板 |
| AI 模块 | 37 | AI 核心板/模块 |
| 工具 | 36 | 高级灭火器/电动螺丝刀/救生颚 |
| 零件 | 31 | 各级零件 |
| 肢体培育 | 30 | 器官/肢体 |
| 生物发生器 | 24 | 生物材料 |
| 采矿 | 23 | 镐/矿机/快速补给盘 |
| 动力 | 22 | 动力设备 |
| 自动车床-安保 | 22 | 安保工具 |
| wiremod 组件 | 112 | 列表/外壳/数学/实体/动作/BCI/工具/通信/字符串/核心/ID/传感器 |
| 自动车床-工程 | 20 | 工程工具 |
| 自动车床-医科 | 20 | 医疗工具 |
| 电信 | 8 | 电信设备 |
| 蓝空间 | 7 | 追踪信标/惰性宝袋/人工蓝水晶 |
| 冶炼 | 7 | 塑钢/塑钛/等离子玻璃 |
| 电子 | 6 | 电子元件 |
| 电脑部件 | 3 | 电脑部件 |
| 实验科学 | 1 | experisci |

## 5.3 代表性设计图（材料精确值）

**武器类**（weapon_designs.dm）：
| 设计图 | 材料 |
|---|---|
| .38 快速装填器（致命） | 铁 300 |
| .38 TRAC 追踪装填器（非致命） | 铁 150+银 75+金 50 |
| .38 Hot Shot 热弹装填器 | 铁 150+等离子 75 |

**医疗类**：
| 设计图 | 材料 |
|---|---|
| 健康分析仪 | 铁 50+玻璃 5 |
| 尸检扫描仪 | 铁 50+玻璃 10 |
| **蓝空间烧杯** | 玻璃 250+塑料 150+钻石 50 |

**机甲类**（MECHFAB）：
| 设计图 | 材料 | 时间 |
|---|---|---|
| 机器人内骨骼 | 铁 750 | 50s |
| 机器人躯干 | 铁 2000 | 35s |
| 机器人头部 | 铁 250 | 35s |

**机械板**：
| 设计图 | 材料 |
|---|---|
| 电解器板 | 玻璃 50 |
| SMES 板 | 玻璃 50 |
| 动力连接器板 | 玻璃 50 |

**采矿/蓝空间/冶炼/工具**：
| 设计图 | 材料 |
|---|---|
| 镐 | 铁 100 |
| 快速补给舱盘 | 玻璃 50 |
| 惰性宝袋 | 金 150+钻石 75+铀 25 |
| 人工蓝空间水晶 | 钻石 75+等离子 75 |
| **塑钢** | 铁 100+等离子 100 |
| **塑钛** | 钛 100+等离子 100 |
| 等离子玻璃 | 等离子 50+玻璃 100 |
| 高级灭火器 | 钛 50+金 50 |
| 电动螺丝刀 | 铁 175+银 75+钛 125 |

## 5.4 NOVA 覆写（36 个）

生物发生器：生物肉（生物质 25）/柯比立方（生物质 50）；肢体培育：蜗牛舌/蜗牛肝/蜗牛心（NOVA 新增物种器官）等。

---

# 第六卷 · 异星生物（Xenobiology）

**代码**: `code/modules/research/xenobiology/`（30 文件 8,272 行）+ `slime/`（slime 系列）+ `slime_extracts.dm`（配方 589 行）

## 6.1 核心流程（Xenobio）

| 步骤 | 操作 |
|---|---|
| 1 抓捕 | 史莱姆管理控制台远程摄取/放置（上限 5 只） |
| 2 驯化 | 喂血浆片+驯化药剂（彻底驯服并命名） |
| 3 饲养 | 喂猴立方/猴子（死猴投入猴子回收机换猴立方） |
| 4 成长 | 营养 ≥150 生长、达 10 进化成体（耗 100 营养） |
| 5 繁殖 | 成体+生长满→**分裂 4 只**（同格 ≥2 只拥挤无法繁殖） |
| 6 变异 | **30% 变异率**从加权表抽色，100% 必变彩虹 |
| 7 取核 | 手术取提取物（默认 1 核）；**成体死亡复活为幼体+额外 1 幼体** |
| 8 反应 | 提取物注入试剂触发配方（默认 1 次使用/研磨 20u 史莱姆胶） |
| 9 交叉繁殖 | 喂 **10 个同色提取物**→死亡生成交叉提取物 |

**生理基础**：幼体 150 血/成体 **200**、成体伤害 **14-34**、**BURN=-1（火焰治疗）**、电击命中 `power×7%+10%`（麻痹 2s+击倒）、低温冻结（≤T0C-40）、BZ≥5% 静止、营养上限 200/生长 150/饥饿 50。

## 6.2 史莱姆 21 色全录

| 颜色(Tier) | 提取物 | 变异 | minor 效果 | major 效果 |
|---|---|---|---|---|
| 灰(T0) | grey | 蓝/金属/橙/紫 | 生成猴立方 | 4s 后生成灰史莱姆 |
| 蓝(T1) | blue | 深蓝/粉/银 | 注入突变净化剂+碘化钾 | 生成 20 格泡沫 |
| 金属(T1) | metal | 金/银/黄 | 生成 5 片玻璃 | 生成 5 片铁 |
| 紫(T1) | purple | 深蓝/深紫/绿 | 营养+50/血+50 | 注入再生胶 10u |
| 橙(T1) | orange | 深紫/红/黄 | 注入辣椒素 | 磷钾糖→烟雾 |
| 深蓝(T2) | darkblue | 蓝/天蓝/紫 | 灭火+霜油+再生胶 | 生成 40mol 氮气@2.7K |
| 深紫(T2) | darkpurple | 橙/紫/赭 | 生成等离子矿 | 生成 20mol 等离子 |
| 银(T2) | silver | 蓝/金属/黄铁矿 | 生成随机食物 | 生成随机饮料 |
| 黄(T2) | yellow | 蓝空间/金属/橙 | 发光+5 | **EMP 脉冲** |
| 蓝空间(T3) | bluespace | 蓝空间 | 随机传送半径 6 | 传回标记点 |
| 天蓝(T3) | cerulean | 天蓝 | 注入沙丁胺醇 | 生成 O2+N2 |
| 黄铁矿(T3) | pyrite | 黄铁矿 | 生成蜡笔 | 生成喷漆罐 |
| 赭(T3) | sepia | 赭 | 生成照相机 | **3s 后时间停止** |
| 金(T4) | gold | 精金/金 | 生成友好生物 | **生成敌对生物** |
| 绿(T4) | green | 黑/绿 | 12s 后变回人类 | 12s 后变随机果冻人 |
| 粉(T4) | pink | 浅粉/粉 | **性别反转** | 注入 pax 2u |
| 红(T4) | red | 油/红 | 注入麻黄碱 | **全部史莱姆狂暴** |
| 精金(T5) | adamantine | 精金 | 伤害抗性+25/2分钟 | 12s 后变**石魔** |
| 黑(T5) | black | 黑 | 感染转化疾病 | 12s 后变**暗影族** |
| 浅粉(T5) | lightpink | 浅粉 | 生成改名药水 | 生成智慧药剂 |
| 油(T5) | oil | 油 | 生成油污 | 6s 后**自爆 1/3/6** |
| 彩虹(特殊) | rainbow | 彩虹 | 随机突变色 | 生成随机色提取物 |

> T3 起为终点色；精金/黑/浅粉/油/彩虹不可再向普通色突变。

## 6.3 交叉繁殖（11 效果 × 21 色 = 231 产物）

| 效果（来源） | 机制 |
|---|---|
| **reproductive 繁殖**(灰) | 喂 3 猴立方→产 1-4 个同色提取物 |
| **self-sustaining 自给**(深紫) | 炸成 4 个 autoslime（自动触发白名单反应） |
| **stabilized 稳定**(蓝) | 被动 buff（免疫水滑/回温/电池充电等 21 色效果） |
| **consuming 吞噬**(银) | 吃食物→产 5 个史莱姆饼干（21 种效果：营养+15/体温+110/钝伤×0.9/电击免疫/传送到安全点等） |
| **regenerative 再生**(紫) | 完全治疗+附加效果（伪造死亡/时间回溯/godmode 10s 等） |
| **recurring 循环**(天蓝) | 每 10s +1 次使用（赭 36s/金 30s/彩虹 40s） |
| **industrial 工业**(金属) | 吸血浆→产物（猴立方/铁片/电池/灭火器等） |
| **burning 燃烧**(橙) | 注入 10u 血浆→进攻效果 |
| **chilling 冰冻**(深蓝) | 注入 10u 血浆→防御效果 |
| **charged 充能**(黄) | 注入 10u 血浆→生成物品/药水 |
| **prismatic 棱镜**(黄铁矿) | 无限次画笔（灰=除色/彩虹=自定义） |

> **11 色提取物无交叉效果**（"过于不稳定"失败）：金/红/粉/绿/浅粉/黑/油/精金/蓝空间/赭/彩虹。

## 6.4 缸培育（Vatgrowing）

**流程**：采集(3s)→培养皿(3 层合并)→显微镜→300u 缸。
**数值**：生长率 4（器官 1）、100 满产出、**15% 突变**（painted/mutant/rare/shiny/mutant king）、44 种细胞系试剂表（需求/喜好/厌恶）、病毒用 spaceacillin 抑制、缸兽产出后销毁缸。

## 6.5 提取物配方完全表（水/血/等离子 三路径）

**代码**: `code/modules/reagents/chemistry/recipes/slime_extracts.dm`（589 行，52 反应）
**机制**: 每个颜色的提取物可注入不同试剂触发不同反应——**血浆（plasma）/ 血（blood）/ 水（water）** 三条通用路径 + 每色专属试剂。反应以 `required_container`（提取物颜色）+ `required_reagents` 配对。

| 颜色 | 等离子（plasma） | 血（blood） | 水（water） | 专属试剂 |
|---|---|---|---|---|
| **灰** | 血浆1→**生成史莱姆**（slimespawn） | 血1→**猴立方**（slimemonkey） | 水5→**肾上腺素×3**（slimeinaprov） | — |
| **蓝** | 血浆1→**霜油×10**（slimefrost） | 血1→**稳定剂**（stabilizer） | 水5→**泡沫**（foam） | — |
| **金属** | 血浆1→**塑钢+铁**（slimemetal） | — | 水1→**强化玻璃+玻璃**（slimeglass） | — |
| **紫** | 血浆1→**成长激素**（psteroid） | 血1→**再生胶×5**（slimeregen） | — | — |
| **橙** | 血浆15→**火焰**（slimefire） | 血1→**辣椒素×10**（slimecasp） | 水5→**磷10+钾10+糖10 烟雾**（slimesmoke） | — |
| **深蓝** | 血浆1→**冰冻**（slimefreeze） | — | 水1→**防火剂**（fireproof） | — |
| **深紫** | 血浆1→**等离子矿**（slimeplasma） | — | — | — |
| **银** | 血浆1→**随机物品**（slimebork） | — | — | — |
| **黄** | 血浆1→**史莱姆电池**（slimecell） | 血15→**过载**（slimeoverload） | 水1→**发光**（slimeglow） | — |
| **蓝空间** | 血浆1→**蓝水晶**（slimecrystal） | 血1→**蓝空间地板**（slimefloor2） | 水1→**史莱姆电台**（slimeradio） | — |
| **天蓝** | 血浆1→**增强剂**（psteroid2） | 血1→**领地**（territory） | — | — |
| **黄铁矿** | 血浆1→**颜料**（slimepaint） | 血1→**蜡笔**（slimecrayon） | — | — |
| **赭** | 血浆1→**时间停止**（slimestop） | 血1→**赭地板**（slimefloor） | 水1→**相机+胶卷**（slimecamera） | — |
| **金** | 血浆15→**生成生物**（mobspawn） | — | — | — |
| **绿** | 血浆1→**变异胶**（slimemutate） | 血1→**变异毒素**（slimehuman） | — | 镭1→蜥蜴变异/牛奶1→猫变异/纤维素1→蛾变异/电1→以太人变异 |
| **粉** | 血浆1→**驯化药剂**（docility） | 血1→**性别反转**（gender） | — | — |
| **红** | 血浆1→**变异剂**（slimemutator） | 血1→**血怒**（bloodlust） | 水1→**速度**（slimespeed） | — |
| **精金** | 血浆15→**精金锭**（adamantine） | — | — | — |
| **黑** | 血浆1→**二次变异毒**（mutate2） | — | — | — |
| **浅粉** | 血浆1→**智慧药剂**（sentience） | — | 水1→**改名药水**（renaming） | — |
| **油** | 血浆15→**爆炸**（slimeexplosion） | 血1→**油污×10**（slimeoil） | — | — |
| **彩虹** | 血浆1→**随机反应**（rng） | 血1→**意识转移**（transference） | 圣水5+铀5→**飞行药剂**（flight） | 史莱姆胶1→**史莱姆炸弹**（slimebomb） |

**产物说明**：
- **血浆路径**（1u 标准/15u 强力）：生成史莱姆/霜冻/塑钢/成长激素/火焰/冰冻/电池/蓝水晶/时间停止/变异/驯化药剂/精金/智慧药剂/爆炸等
- **血路径**（1u 标准/15u 过载）：猴立方/稳定剂/再生胶/辣椒素/人类转化/性别反转/血怒/意识转移等
- **水路径**（1u 标准/5u 大量）：泡沫/烟雾/玻璃/防火/发光/电台/相机/改名/速度等
- **专属试剂**：绿提取物=镭(蜥蜴)/牛奶(猫科)/纤维素(蛾)/浓缩电(以太人)；彩虹=圣水+铀(飞行药剂)、史莱姆胶(炸弹)

## 6.6 NOVA EDIT（异星生物 4 处实质改动）

① 全部文本 LANG() 化；② slime 改名正则重写；③ **猴子回收机产率 ×2**（伺服/储料 ×0.2，原 ×0.1，T4 达 1.2）；④ 史莱姆扫描仪允许盲人使用。

---

# 第七卷 · 军械实验与论文

**代码**: `code/modules/research/ordnance/`（1,048 行）

## 7.1 多普勒阵列（Tachyon-Doppler Array）

**代码**: `ordnance/doppler_array.dm`（307 行）

| 机制 | 值 |
|---|---|
| 功能 | **高精度方向性传感器**：测量爆炸能量释放（检测爆炸/气体反应） |
| 方向 | 旋转决定可检测方向（带方向灯指示） |
| 数据 | 生成 tachyon_record（爆炸数据） |
| 论文 | 数据写入科学论文→发布→研究点数+折扣 |

## 7.2 罐压缩机（Tank Compressor）

**代码**: `ordnance/tank_compressor.dm`（343 行）

| 机制 | 值 |
|---|---|
| 功能 | 压缩气体罐 → 爆破 → 多普勒阵列测量 |
| 用途 | 军械实验（气体罐爆炸：N2O/爆炸气体） |

## 7.3 科学论文（Scientific Paper）

**代码**: `ordnance/_scipaper.dm`（321 行）

| 类型 | 内容 |
|---|---|
| 爆炸类 | 爆炸数据（低/高/氢/氮/无反应 5 种） |
| 气体类 | 气体罐数据（N2O 等） |
| 发布 | 发布后→对应科技节点**获得折扣/升格**（convey boosts） |

**科学合作者**（scipaper_partner.dm）：与外部研究机构合作（跨站论文）。

## 7.4 军械实验（ordnance experiments）

| 实验 | 目标 | 奖励 |
|---|---|---|
| 低当量爆炸 | 5/10/20 爆炸量 | 10/15/20 点 |
| 高当量爆炸 | 50/100/300 | 10/50/100 点 |
| 氢爆炸 | 50/75/150（氢/氚火） | 15/40/60 点 |
| 氮爆炸 | 50/100/300（超氮冷凝） | 15/60/120 点 |
| 无反应爆炸 | 20/50/100（无气体反应） | 10/50/100 点 |
| N2O 气弹 | 200/600 单位 | 10/40 点 |

---

# 第八卷 · 异常与小工具

## 8.1 异常系统（Anomaly）

**代码**: `code/modules/research/anomaly/`（713 行）

| 核心 | 功能 |
|---|---|
| **原始异常核心**（5 种） | 普通/蓝空间/涡旋/引力/高温火 |
| **异常核心** | 精炼后的核心（可触发特殊效果） |
| **异常精炼厂** | 提炼原始核心 → 异常科技/材料（带爆炸模拟） |

## 8.2 文物（Relic）

**代码**: `relics.dm`（649 行）

随机物品 → 激活后获得**随机隐藏能力**（6-30s 冷却）：

| 主题 | 能力 |
|---|---|
| **原型主题**（13 种） | 柯基炮/清洁泡沫/闪光弹/召唤动物/失控传送/过热爆炸/自我复制/饮料机/肚子疼/充电器/拥抱者/维度转移/伪装器 |
| **死灵主题**（14 种） | 维度转移/召唤怪物/过热爆炸/护盾 T1/T2/失控传送/AOE 传送/充电器/放石头/喷血/吸血/清洁泡沫/酸泡沫 |

拉瓦兰版文物=死灵主题（necrotech）。

## 8.3 小工具 Gizmo（解谜编程）

**代码**: `code/modules/research/gizmo/`（1,655 行）

**架构**：对象 > gizmo_controller > gizmo_puzzle + gizmo_interface > gizmode > gizpulse > callbacks

| 控制器类型 | 交互 |
|---|---|
| 基础 | 线束界面 |
| Beyblade 陀螺 | 移动界面 |
| Toggle 切换 | 图标/发光切换 |
| Voice 语音 | 语音拼图+线束提示 |
| Item 物品 | 手持式 |
| Cursed 诅咒 | 永恒痛苦 |
| Moo 牛叫 | 硬核代码破解 |

**gizmodes**（模式，10 种）：filler（填充：甩动产油）/actives（主动：远程遥控，像遥控器和电视）/bad（坏：能致残或杀死你的效果，供科研作死）/code（代码：生成破解代码）/copier（复制：扫描并复制最近物体，复制品无功能）/lectric（电：吸收电力并射出）/misc（杂项：移动元素+发光）/mood（情绪：发送好/坏情绪脉冲）/mopper（拖把：再生、循环并排出试剂）/porter（传送：传送自己或他人）

---

# 第九卷 · 基因学（Genetics）

**代码**: `code/datums/mutations/`（24 文件 3,565 行）+ `code/datums/dna/`（DNA 系统）+ `code/game/machinery/dna_scanner.dm` + `dna_console.dm`（2,414 行）+ `dna_injector.dm`（552 行）
**归属**: 科研部（基因学家职位，隶属科学部门，上级 R&D 主管）

## 9.1 基因学家职业

**代码**: `code/modules/jobs/job_types/geneticist.dm`（50 行）

| 属性 | 值 |
|---|---|
| 岗位数 | 2 |
| 上级 | R&D 主管（SUPERVISOR_RD） |
| 经验要求 | 60（船员经验） |
| 薪资 | CREW 级（科学账户） |
| 职责 | 修改基因组、猴子↔人类转化、DNA 备份 |
| 邮件赠礼 | 猴立方×10 |
| 配发 | 序列扫描仪/遗传学背包/白色实验服 |

## 9.2 DNA 系统核心

**代码**: `code/datums/dna/dna.dm`（759 行）

| 字段 | 说明 |
|---|---|
| `unique_enzymes` | 持有人真名的 md5 哈希 |
| `unique_identity` | 肤色/发型/性别等外貌哈希 |
| `unique_features` | 非人类特征哈希 |
| `blood_type` | 血型 datum |
| `species` | 突变种族（如土豆人） |
| `mutations` / `mutation_index` | 突变列表/突变块索引 |
| `stability` | **稳定性（默认 100）** |
| `scrambled` | 摄入变异剂后为 TRUE（防止扫描破解） |
| 熔毁 | nonfatal（非致命）/fatal（致命）加权表 |

**突变源**：MUTATION_SOURCE_ACTIVATED（激活）/MUTATION_SOURCE_MUTATOR（变异剂）等（见 defines/dna.dm）。

## 9.3 DNA 控制台（DNA Console）

**代码**: `code/game/machinery/computer/dna_console.dm`（2,414 行）

| 功能 | 说明 |
|---|---|
| **突变激活器创建** | 制作激活器（升级减冷却） |
| **突变注入器创建** | 制作注入器（升级减冷却） |
| **高级注入器** | 硬上限冷却 |
| **基因组合存储** | 存储槽上限（保存 DNA 组合） |
| **DNA 扰乱** | 扰乱目标基因（DNA Scramble） |
| **Joker 功能** | 猜解一个基因（时间随扫描仪升级减少） |
| **酶脉冲** | 脉冲酶（基因损伤强度/持续时间可调，大值会误伤其他酶） |
| **搜索系统** | 4 源搜索：扫描仪占用者/控制台存储/磁盘存储/高级注入器突变 |
| **酶+组合复制** | 复制到他人（基础冷却） |

## 9.4 DNA 扫描仪（DNA Scanner）

**代码**: `code/game/machinery/dna_scanner.dm`（185 行）

| 属性 | 值 |
|---|---|
| 功能 | 扫描 DNA 结构（供控制台操作） |
| 限制 | 不可操作断头/断脑（DNA 操作需要完整大脑） |
| 升级 | 扫描仪升级档位减少 Joker 时间 |

## 9.5 DNA 注入器（DNA Injector）

**代码**: `code/game/objects/items/dna_injector.dm`（552 行）

| 属性 | 值 |
|---|---|
| 功能 | 注入突变→目标激活 |
| 源 | 可从 DNA 磁盘复制 DNA 数据到注入器 |
| 效果 | 激活/突变目标已有突变、持续时间记录 |
| 已使用标记 | 追踪是否已用（一次性） |

## 9.6 突变系统全录（68 种）

**代码**: `code/datums/mutations/`（24 文件 3,565 行）
**分类机制**: quality 字段（POSITIVE=好 / NEGATIVE=坏 / MINOR_NEGATIVE=中性），locked=TRUE 为锁定突变（不进基因池，仅配方/管理/特殊来源）

> **⚠ 稳定性（instability）为固定值**：每个突变在源码中用常量宏或字面值写死，无动态计算（已全量核对 `_mutations.dm` + 各突变文件）。常量共 8 档：
>
> | 常量 | 值 | 含义 |
> |---|---|---|
> | POSITIVE_INSTABILITY_MINI | **+5** | 几乎无收益（发光） |
> | POSITIVE_INSTABILITY_MINOR | **+10** | 小众/稀有场合有用 |
> | POSITIVE_INSTABILITY_MODERATE | **+25** | 约等于站内装备强度 |
> | POSITIVE_INSTABILITY_MAJOR | **+35** | 独特强力、改变战斗（Hulk/TK） |
> | NEGATIVE_STABILITY_MINI | **0** | 基本无害（语言类） |
> | NEGATIVE_STABILITY_MINOR | -20 | 轻微烦人（**未使用**） |
> | NEGATIVE_STABILITY_MODERATE | **-30** | 不常见/轻度阻碍 |
> | NEGATIVE_STABILITY_MAJOR | **-40** | 重大持续阻碍（聋/哑/酸肉） |
>
> 好突变全部**加**稳定性（+5~+35），坏突变全部**扣**（-30~-40），语言类中性突变 = 0。例外：**Internal Martyrdom 内在殉道是好突变但 -40**（拿命换爆发）、**Void Magnet 虚空磁铁是中性但 +25**。下方表格每个突变已标注（稳定 ±N）。

**突变 datum 关键字段**: power_path（主动技能）/ instability（稳定性惩罚：好突变 +5~+35，坏突变 -30~-40）/ difficulty（缺失序列数，越高越难解锁）/ blocks（基因序列块数，默认 4）/ conflicts（冲突突变）/ health_req（获得所需最低健康）/ species_allowed（限定物种）/ can_chromosome（染色体兼容）/ 系数（stabilizer/synchronizer/power/energy，染色体可改）

**突变来源**: activated（序列激活/激活剂）/ mutator（基因突变器）/ crew_monkey / dna_vault（DNA 金库）/ spell / heart_eater / wishgranter / mannitoil / changeling / species_innate（NOVA 新增）等

### 9.6.1 好突变（POSITIVE，35 种）

| 突变 | 锁定 | 效果 |
|---|---|---|
| Adrenaline Rush 肾上腺素爆发（稳定 +25） | | 主动 2 分钟冷却：注入兴奋剂等 10u×力量系数，25 秒后反冲（疲劳+眩晕10s） |
| Adaptation 适应（稳定 -40） | 🔒 | 环境适应（父类，不可直接获得） |
| Antenna 触角（稳定 +10） | | 植入内部天线收音机（被动接入公共频道） |
| Mind Reader 心灵读取（稳定 +10） | 🔒 | 读心 |
| Autotomy 自断尾（稳定 +10） | | 主动 10 秒冷却：随机卸下非头/胸肢体 |
| Dwarfism 矮小（稳定 +10） | 🔒 | 缩小体型 |
| Glowy 发光（稳定 +5） | | 随机颜色发光（范围 2.5×力量） |
| Strength 力量（稳定 +10） | | TRAIT_STRENGTH 近战伤害提升 |
| Stimmed 兴奋剂（稳定 +5） | | 速度/反应提升 |
| Insulated 绝缘（稳定 +25） | | TRAIT_SHOCKIMMUNE 电击免疫 |
| Internal Martyrdom 内在殉道（稳定 -40） | 🔒 | 濒死爆发 |
| Hypermetabolic Blood 高代谢血（稳定 +10） | | 血液效果增强 |
| Rock Eater 食石者（稳定 +5） | | 吃石头 |
| Rock Absorber 吸石者（稳定 +35） | 🔒 | 吸收岩石 |
| Inexorable 不可阻挡（稳定 +25） | | 抗击退 |
| Chameleon 变色龙（稳定 +35） | | 静止时透明度每 tick -12.5×力量（最低全隐），**移动/攻击重置 204**；NOVA 需 CHAMELEON_SKIN 特质 |
| Geladikinesis 凝胶念动（稳定 +10） | | 凝胶控制 |
| Cryokinesis 冷冻念动（稳定 +25） | | 主动 Cryobeam：射程 9、16 秒冷却 |
| Farsight 远视（稳定 +10） | | 远距离视物 |
| Fire Breath 火吐息（稳定 +25） | 🔒 | 主动 40 秒冷却：**3 格宽火柱**，灼伤 max(10,40-5×等级)、点燃；嘴被遮住会自燃 |
| Cindikinesis 余烬念动（稳定 +10） | 🔒 | 余烬控制 |
| Pyrokinesis 火焰念动（稳定 +25） | 🔒 | 火焰控制 |
| Hulk 绿巨人（稳定 +35） | 🔒 | 巨力+变身（**health_req 25 激活**/免疫伤害减速/抓人甩出砸墙） |
| Transcendent Olfaction 超凡嗅觉（稳定 +25） | | 超强嗅觉 |
| Biotech Compatibility 生物科技兼容（稳定 +5） | | 义体增强 |
| Clever 聪明（稳定 +25） | | 智力提升 |
| Telekinesis 念动力（稳定 +35） | | 远程隔空互动（需头部） |
| Elastic Arms 弹性手臂（稳定 +35） | | 触达+1 格；代价：无法戴手套/双手持物 |
| Thermal Vision 热视觉（稳定 +35） | | 主动 60 秒冷却：30 秒热视觉（穿墙看热源），每次激活 7.5 眼部伤 |
| Laser Eyes 激光眼（稳定 0） | 🔒 | 眼中发射激光 |
| Telepathy 心灵感应（稳定 +10） | | 主动心灵传讯（选目标自由传讯） |
| Tongue Spike 舌刺（稳定 +5） | | 主动 1 秒冷却：射舌 14 格/速度 4，尖刺 force 2/throwforce 25/**100% 嵌入**/痛感×15 |
| Shock Touch 电击触（稳定 +25） | 🔒 | 触摸放电 |
| Mending Touch 治愈触（稳定 +35） | | 主动 12 秒冷却：转移目标最多 35×力量 伤到自己；**对亡灵改为天罚** |
| Webbing Production 织网（稳定 +25） | | 主动 4 秒冷却织网（能量染色体→2 秒），生成粘网仅自己可轻松穿越 |

### 9.6.2 坏突变（NEGATIVE，18 种）

| 突变 | 锁定 | 效果 |
|---|---|---|
| Epilepsy 癫痫（稳定 -30） | | 随机倒地 |
| Unstable DNA 不稳定 DNA | 🔒 | DNA 不稳定 |
| Paranoia 偏执（稳定 -30） | | 幻觉威胁 |
| Tourette's Syndrome 图雷特（稳定 0） | | 不由自主咒骂 |
| Deafness 失聪（稳定 -40） | | 听不见 |
| Monkified 猴化（稳定 -40） | 🔒 | 变成猴子 |
| Fiery Sweat 火焰汗（稳定 0） | | 出汗自燃 |
| Spatial Instability 空间不稳（稳定 -30） | | 随机闪烁 |
| Acidic Flesh 酸性肉体（稳定 -40） | | 皮肤腐蚀 |
| Spastic 痉挛（稳定 -30） | | 肌肉失控 |
| Two Left Feet 双左脚（稳定 -30） | | 更容易摔倒 |
| H.A.R.S. 无头综合症（稳定 -40） | | 失去头（特殊） |
| Radioactivity 放射性（稳定 -40） | | 辐射外泄 |
| Blindness 失明（稳定 -40） | | 看不见 |
| Illiterate 文盲（稳定 -40） | | 无法阅读 |
| Mute 哑巴（稳定 -40） | | 无法说话 |
| Unintelligible 语无伦次（稳定 -30） | | 说话混乱 |
| Stoner 石头人（稳定 0） | | 语速缓慢（抽大麻口吻） |

### 9.6.3 中性突变（MINOR_NEGATIVE，14 种）

| 突变 | 锁定 | 效果 |
|---|---|---|
| Cough 咳嗽（稳定 -30） | | 随机咳嗽 |
| Acromegaly 肢端肥大（稳定 -30） | | 手脚变大 |
| Gigantism 巨人症（稳定 0） | | 体型增大 |
| Clumsiness 笨拙（稳定 -40） | | 容易摔东西 |
| Near Sightness 近视（稳定 -30） | | 视野模糊 |
| Nervousness 神经质（稳定 0） | | 紧张反应 |
| Wacky 古怪（稳定 0） | | 随机搞笑行为 |
| Heckacious Larincks 古怪喉音（稳定 0） | 🔒 | 奇怪发音 |
| Swedish 瑞典口音（稳定 0） | | 说瑞典语 |
| Chav 小混混口音（稳定 0） | | 伦敦俚语 |
| Elvis 猫王口音（稳定 0） | | 猫王说话 |
| Medieval 中世纪口音（稳定 0） | | 古英语 |
| Pig Latin 猪拉丁（稳定 0） | | 猪拉丁语 |
| Void Magnet 虚空磁铁（稳定 +25） | | 吸引物体（虚空法术 **1 分钟冷却**/诅咒版濒死触发） |

### 9.6.4 特殊/未分类

| 突变 | 锁定 | 效果 |
|---|---|---|
| X Ray Vision X 光视觉（稳定 +35） | 🔒 | 透视墙壁 |

> 锁定突变（16 种）：Adaptation/Mind Reader/Dwarfism/Internal Martyrdom/Rock Absorber/Fire Breath/Cindikinesis/Pyrokinesis/Hulk/Laser Eyes/Shock Touch/Unstable DNA/Monkified/Heckacious/X Ray 等——需特殊获取途径（子代理详述）。

---

## 9.7 细胞学（Cytology · 细胞样本收集与克隆）

**代码**: `code/modules/research/xenobiology/vatgrowing/`（1,659 行）+ `code/game/machinery/experimental_cloner/`（765 行）

> **玩法**：用活检工具从生物上刮取细胞样本→显微镜观察→培养皿培养→培养槽（Vatgrower）生长成完整生物/器官；实验性克隆器可从尸体记录克隆人类。

### 9.7.1 细胞学设备链

| 设备 | 代码 | 功能 |
|---|---|---|
| **活检工具 Biopsy Tool** | `biopsy_tool.dm`（31 行） | 从生物身上刮取细胞样本 |
| **拭子 Swab** | `swab.dm`（19 行） | 收集表面细胞 |
| **显微镜 Microscope** | `microscope.dm`（137 行） | 观察样本/微生物 |
| **培养皿 Petri Dish** | `petri_dish.dm`（67 行） | 培养微生物样本（3 层） |
| **培养槽 Vatgrower** | `vatgrower.dm`（212 行） | 从样本生长出完整生物（"Tastes just like the chef's soup"） |

### 9.7.2 培养槽机制

| 机制 | 值 |
|---|---|
| 试剂容量 | **300 单位**（"汤"） |
| 样本注入 | 培养皿→槽（克隆样本微生物） |
| 生长 | 每 tick 消耗试剂喂食样本（handle_growth） |
| 重新采样器 | 激活后生长完成自动重启 |
| 水管系统 | plumbing 组件（可接入试剂网络） |
| 冲洗 | 清空试剂+删除样本 |

### 9.7.3 细胞系全录（27 动物 + 4 器官系 · 源码全量）

**代码**: `code/modules/research/xenobiology/vatgrowing/samples/cell_lines/`（common.dm 27 动物 + organs.dm 4 器官系）

**动物系（27 种，common.dm）**：

| 细胞系 | 描述 | 产物 |
|---|---|---|
| **奶牛** | Bovine stem cells | cow |
| **月独角兽** | Fairyland Bovine stem cells | cow/moonicorn |
| **山羊** | Caprine cells | goat |
| **猪** | Porcine stem cells | pig |
| **猫** | Feliform cells | pet/cat |
| **柯基** | Canid cells | pet/dog/corgi |
| **叭狗** | Squat canid cells | pet/dog/pug |
| **太空鲤** | Cyprinid cells | carp |
| **巨鲤** | Cartilaginous cyprinid cells | carp/mega |
| **蛇** | Ophidic cells | snake |
| **史莱姆** | Slime particles | slime |
| **菌潮巨像** | Blobular myocytes | blob_minion/blobbernaut/independent |
| **明胶立方体** | Cubic ooze particles | ooze/gelatinous |
| **夏莲葡萄** | Globular ooze particles | ooze/grapes |
| **持枪蟑螂** | Gattodeoid anthropod cells | cockroach/glockroach×2 |
| **豪猪蟑螂** | Hattodeoid anthropod cells | cockroach/hauberoach×2 |
| **松树** | Coniferous plant cells | tree |
| **槽兽** | Hypergenic xenocytes | vatbeast |
| **netherworld 残渣** | Aberrant residue | — |
| **小丑（随机突变/血肉小丑）** | None | pick(clown/mutant, clown/fleshclown) |
| **青蛙（随机）** | anura amphibian cells | /obj/effect/spawner/random/frog |
| **蝾螈** | caudata amphibian cells | axolotl |
| **行走蘑菇** | motile fungal hyphae | mushroom |
| **女王蜂** | aphid cells | /obj/item/queen_bee/bought |
| **蝴蝶** | Papilionoidea cells | butterfly×3 |
| **巨型蛛形纲** | pseudoarachnoid cells | mega_arachnid |
| **蜗牛** | gastropod epithelial cells | snail |

**器官系（4 主系 + 变体，organs.dm）**：心（+进化/神圣/腐败）、肺（+进化）、肝（+进化/血淋/酿酒）、胃（+进化）——器官细胞系可直接培养出可移植器官（手术移植用）。

**细胞系配方结构**：
```
required_reagents: 必需试剂（如蛋白质）
supplementary_reagents: 辅助试剂（生长血清/维他命等，加速）
suppressive_reagents: 抑制试剂（如肝素 -6，抑制污染）
growth_rate: 生长速率（VAT_GROWTH_RATE=4）
virus_suspectibility: 病毒易感性
resulting_atom: 结果生物（+数量）
```


### 9.7.4 实验性克隆器（Experimental Cloner）

**代码**: `experimental_cloner/`（5 文件）——被 Nanotrasen 高管使用过的**非法克隆舱原型**。

| 机制 | 值 |
|---|---|
| **克隆时间** | **1 分钟** |
| **全局故障率** | **50%**（experimental_cloner_fuckup_chance，跨构建持久） |
| 邪恶克隆率 | **2%**（evil_chance） |
| 流程 | 扫描仪扫描尸体→克隆记录→舱体培育→招募幽灵玩家 |
| 失败 | 内脏喷溅+冲水声（fail_growing） |
| 断电 | 培育中停电→失败 |

## 9.8 DNA 融合器（DNA Infuser · 融合基因）

**代码**: `code/game/machinery/dna_infuser/`（2,816 行）

> **玩法**：把生物尸体的基因融合进人体——注入后获得该生物的器官变异（太空呼吸/夜视/灰风暴免疫等）。

### 9.8.1 核心机制

| 机制 | 值 |
|---|---|
| 设备 | "A defunct genetics machine for merging foreign DNA"（废弃基因机） |
| 流程 | 受试者入舱 + 注入源（尸体/样本）→ **4 秒注入**（INFUSING_TIME）+ 3 秒尖叫 |
| 伤害 | 注入造成 **10 基础伤害**（take_overall_damage） |
| 层级限制 | max_tier_allowed = DNA_MUTANT_TIER_ONE（可升级解锁更高 tier） |
| 失败 | 未知 DNA（fly 条目）/过度复杂（Overcomplexity）→ **打印错误报告** |
| 记忆 | 注入后获得 dna_infusion 记忆（可被查看） |

### 9.8.2 融合条目全录（17 种）

**Tier 0（基础）**：

| 条目 | 效果（器官组） |
|---|---|
| **蝇 Fly** | 失败默认条目（"Unknown DNA"）；成为蝇人 |
| **狐 Vulpini** | 狐狸基因（稀有） |
| **蛾螂 Mothroach** | 蛾+蟑螂混合基因 |
| **蜥蜴 Lizard** | 蜥蜴基因 |
| **猫人 Felinid** | 猫人基因（"EVERYONE CALM DOWN!"） |
| **企鹅 Penguin** | 企鹅基因 |
| **植物 Plants** | 植物基因（水培联动） |

**Tier 1（进阶）**：

| 条目 | 效果 |
|---|---|
| **戈利亚 Goliath** | 夜视眼/熔岩肺（呼吸拉瓦兰空气）/触手臂（近战+击退）/灰风暴心脏 |
| **太空鲤 Carp** | **太空肺（空间呼吸！）**/鲨齿（近战强化）/寒冷抗性 |
| **鼠 Rat** | **通风管爬行**/夜视/食腐/缩小（1.5× 受伤） |
| **蟑螂 Roach** | 背后减伤（但击倒更久）/胃免恶心/肝高阈值解毒/无阑尾炎 |
| **鱼 Fish** | **鱼鳃（水蒸气呼吸）**/鱼尾（水中加速）/安全吃生鱼/墨汁/河豚毒 |
| **鱿鱼 Squid** | 头足类基因 |
| **河豚 TTX** | 河豚毒素（Tetrodotoxin） |
| **两栖 Amphibious** | 水陆两栖 |
| **白鼬 Stoat** | 白鼬基因（无畏） |
| **独木舟 Gondola** | **观察者之身：免疫环境危害/和平主义者（mob 中立）/禅舌（不能说话但强正面心情）/爱之臂（不能拿东西）** |

### 9.8.3 器官组详解（8 种生物 × 多器官）

| 生物器官组 | 器官效果 |
|---|---|
| **鲤器官** | 空间肺（反窒息）/鲨齿（强化撕咬）/鲤脑（需定时去新 z-level）/冷抗热弱 |
| **鱼器官** | 鱼鳃（水蒸气呼吸）/鱼尾（水中加速）/生鱼安全食用/墨囊/毒腺（河豚毒） |
| **蝇器官** | 蝇人化 |
| **戈利亚器官** | 夜视眼/熔岩肺/触手臂（采矿阵营伤害+击退）/灰风暴心脏 |
| **独木舟器官** | 环境免疫/和平主义/禅舌/爱之臂 |
| **鼠器官** | 通风管爬行/夜视/食腐/缩小 |
| **蟑螂器官** | 背后减伤/免恶心/肝解毒/无阑尾炎 |
| **白鼬器官** | 无畏/白鼬相关特性 |

> **注意**：鱼器官最多 5 个同时（肝/胃/肺/尾/舌）——集齐可获得"鱼人"完整形态。

---

# 附录 · 代码路径索引

**源码总量：约 35,466 行**（research 33,604 + experisci 1,862）

| 系统 | 文件 | 行数 |
|---|---|---|
| **科技网核心** | | |
| Techweb datum | `techweb/_techweb.dm` | 570 |
| 科技节点 datum | `techweb/_techweb_node.dm` | 135 |
| 科技网类型 | `techweb/techweb_types.dm` | 85 |
| 辅助函数 | `techweb/__techweb_helpers.dm` | 39 |
| 科技树-工程 | `techweb/nodes/engi_nodes.dm` | 11 节点 |
| 科技树-机甲 | `techweb/nodes/mech_nodes.dm` | 14 节点 |
| 科技树-机器人 | `techweb/nodes/cyborg_nodes.dm` + `robo_nodes.dm` | 12 节点 |
| 科技树-医疗 | `techweb/nodes/medbay_nodes.dm` + `biology_nodes.dm` + `surgery_nodes.dm` | 16 节点 |
| 科技树-采矿 | `techweb/nodes/mining_nodes.dm` | 6 节点 |
| 科技树-安保 | `techweb/nodes/security_nodes.dm` | 7 节点 |
| 科技树-服务 | `techweb/nodes/service_nodes.dm` | 10 节点 |
| 科技树-模块服 | `techweb/nodes/modsuit_nodes.dm` | 10 节点 |
| 科技树-科学 | `techweb/nodes/research_nodes.dm` | 6 节点 |
| 科技树-BEPIS | `techweb/nodes/bepis_nodes.dm` | 10 节点 |
| 科技树-电路 | `techweb/nodes/circuit_nodes.dm` | 5 节点 |
| 科技树-外星/辛迪加 | `techweb/nodes/alien_nodes.dm` + `syndicate_nodes.dm` | 4 节点 |
| 科技树-大气 | `techweb/nodes/atmos_nodes.dm` | 6 节点 |
| 科技树-水培 | `techweb/nodes/botany_nodes.dm`（并入 biology） | — |
| **设计图** | `code/modules/research/designs/`（39 文件） | 14,352 |
| 设计图基类 | `code/modules/research/designs.dm` | 245 |
| 机甲制造 | `designs/mechfabricator_designs.dm` | 204 设计 |
| 机械板 | `designs/machine_designs.dm` | 139 设计 |
| 医疗 | `designs/medical_designs.dm` | 132 设计 |
| 自动车床 | `designs/autolathe/`（7 文件） | 190 设计 |
| 武器 | `designs/weapon_designs.dm` | 52 设计 |
| wiremod | `designs/wiremod/`（12 文件） | 112 设计 |
| **异星生物** | `code/modules/research/xenobiology/`（30 文件） | 8,272 |
| 史莱姆本体 | `code/modules/mob/living/basic/slime/`（8 文件） | — |
| 提取物配方 | `code/modules/reagents/chemistry/recipes/slime_extracts.dm` | 589 |
| **实验系统** | `code/modules/experisci/experiment/`（18 文件） | 1,862 |
| **实验器** | `code/modules/research/experimentor/`（4 文件） | 804 |
| **军械** | `code/modules/research/ordnance/`（4 文件） | 1,048 |
| **异常** | `code/modules/research/anomaly/`（3 文件） | 713 |
| **小工具** | `code/modules/research/gizmo/`（18 文件） | 1,655 |
| **设备** | | |
| R&D 控制台 | `rdconsole.dm` | 447 |
| 服务器 | `server.dm` + `server_control.dm` | 356 |
| 破坏分析仪 | `destructive_analyzer.dm` | 226 |
| 原型机等 | `rdmachines.dm` | 159 |
| RPED | `part_replacer.dm` | 201 |
| 文物 | `relics.dm` | 649 |
| 零件 | `stock_parts.dm` + `stock_parts/` | 454 |
| 科技盘 | `research_disk.dm` | 23 |
| 定义 | `code/__DEFINES/research.dm` + `research/slimes.dm` | — |
| **基因学-细胞学** | `research/xenobiology/vatgrowing/`（1,659 行）+ `game/machinery/experimental_cloner/`（765 行） | 2,424 |
| **基因学-融合器** | `game/machinery/dna_infuser/` | 2,816 |
| **基因学** | | |
| 突变系统 | `code/datums/mutations/`（24 文件） | 3,565 |
| DNA 系统 | `code/datums/dna/`（dna.dm 759 + blocks/） | ~900 |
| DNA 控制台 | `code/game/machinery/computer/dna_console.dm` | 2,414 |
| DNA 扫描仪 | `code/game/machinery/dna_scanner.dm` | 185 |
| DNA 注入器 | `code/game/objects/items/dna_injector.dm` | 552 |
| 基因学家 | `code/modules/jobs/job_types/geneticist.dm` | 50 |
| 基因全局 | `code/_globalvars/genetics.dm` | 7 |

> **文档完** — 覆盖科研系统全貌：科技网架构、117 科技节点、84 实验、1,236 设计图、21 色史莱姆+231 交叉产物、缸培育、军械论文、异常文物、gizmo 解谜，全部带精确数值与代码索引。


### 9.6.5 基因配方（合成锁定突变）

**代码**: `_combined.dm`（get_mixed_mutation）

| 配方 | 输入1 | 输入2 | 结果 |
|---|---|---|---|
| mindread | Antenna 触角 | Paranoia 偏执 | **Mind Reader 读心术** |
| shock | Insulated 绝缘 | Radioactive 放射性 | **Shock Touch 电击触摸** |
| cindikinesis | Geladikinesis 造雪 | Fiery Sweat 火焰汗 | **Cindikinesis 造灰** |
| pyrokinesis | Cryokinesis 冷冻 | Fiery Sweat 火焰汗 | **Pyrokinesis 火焰念动** |
| thermal_adaptation | Cold 耐寒 | Heat 耐热 | **Thermal Adaptation 温适应** |
| antiglow | Glowy 发光 | Void Magnet 虚空磁铁 | **Anti-Glow 反光** |
| tonguechem | Tongue Spike 舌钉 | Stimmed 兴奋 | **Chem Spike 化学舌钉** |
| martyrdom | Strength 力量 | Stimmed 兴奋 | **Internal Martyrdom 内在殉道** |
| heckacious | Wacky 滑稽 | Stoner 呆瓜 | **Heckacious 脏话喉** |
| rock_absorber | Rock Eater 食石 | Stoner 呆瓜 | **Rock Absorber 吸石者** |
| ~~hulk~~ | ~~Strength~~ | ~~Radioactive~~ | ~~Hulk~~（**NOVA 已删除**） |
| ~~ork~~ | ~~Hulk~~ | ~~Clumsiness~~ | ~~Ork~~（**NOVA 已删除**） |

### 9.6.6 Hulk 绿巨人专条（hulk.dm）

| 项目 | 数值 |
|---|---|
| 品质/稳定性 | POSITIVE / **+35** |
| 锁定/难度 | TRUE（配方已删，仅管理/特殊来源）/ 16 |
| 物种限制 | **仅人类** |
| 激活条件 | health ≥ **25** |
| 特质 | TRAIT_CHUNKYFINGERS + TRAIT_HULK + **TRAIT_PUSHIMMUNE + TRAIT_STUNIMMUNE** |
| 外观 | 全身深青柠色 |
| 语言 | 全大写、句尾 "!!"、每 5 秒吼 **"HULK SMASH"** |
| 攻击 | 空手破墙/破物、推飞 |
| **尾甩** | 投掷+颈抓+选中腰部+目标有尾巴：2 秒蓄力→**28 步旋转甩动**（4→0.1 秒/步），碰到目标受 step×0.5 钝伤抛飞；最后以 10 格/速度 6 投出 |
| 移动 | 免疫伤害减速 |
| **弱点** | health < crit（≈-50）→ **突变自动失效被删**；无法正常说话 |
| 变体 wizardly | 魔法版：无物种限制/health_req=0/吼叫间隔减半 |
| 变体 superhuman | 超人版：health_req=0 + NOSOFTCRIT/NOHARDCRIT（永不失效） |
| ~~变体 ork~~ | **NOVA 已删除**（原 hulk+clumsy 配方） |

### 9.6.7 NOVA EDIT（基因学 5 大玩法改动）

① **Hulk/Ork 基因配方删除**（_combined.dm）——Hulk 仅剩管理/特殊来源
② **适应类可叠加**（adaptation.dm 移除 conflicts 限制）——原版同时只能 1 种适应
③ **变色龙门控**（chameleon.dm）——需 TRAIT_CHAMELEON_SKIN 特质才隐身
④ **矮人/巨人 body_size 惩罚**（body.dm）——body_size 冲突时吃 25 钝伤不生效
⑤ **脑虫宿主禁突变**（_mutations.dm）——有脑虫时拒绝获得突变
⑥ I18N CODEMOD 全文件 + MUTATION_SOURCE_SPECIES_INNATE 新增