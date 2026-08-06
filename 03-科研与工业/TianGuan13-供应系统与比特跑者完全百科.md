# 天关 — 供应系统与比特跑者完全百科

> **项目**: TianGuan13 (Nova Sector → /tg/station)
> **代码**: `code/modules/cargo/`（74 文件 13,502 行）+ `code/modules/bitrunning/`（5,934 行）
> **范围**: **供应部（Cargo）** + **比特跑者（Bitrunning）** 两大板块
>
> **关联文档**：采矿（含在拉瓦兰百科）；Uplink 补给联动见反派附录B。

---

## 目录

- [供应板块](#供应板块)
  - [第一章 · 供应核心机制](#第一章--供应核心机制)
  - [第二章 · 补给包全录](#第二章--补给包全录)（299 个 16 类+旗标）
  - [第三章 · 悬赏/出口/市场](#供应板块)（171 悬赏/120 出口/黑市）
- [比特跑者板块](#比特跑者板块)
  - [第四章 · 比特跑者核心](#第四章--比特跑者核心)（量子服务器/网舱/评分）
  - [第五章 · 虚拟域全录](#第五章--虚拟域全录)（26+1 个域）
- [附录 · 代码路径索引](#附录--代码路径索引)

---

# 供应板块

# 第一章 · 供应核心机制

## 1.1 订单系统

**代码**: `cargo/orderconsole.dm`（554 行）+ `order.dm`（238 行）

| 机制 | 值 |
|---|---|
| **预算** | 部门银行账户余额（bank.account_balance） |
| 部门预算 | 各部门独立（NOVA：cargo 预算标记） |
| 订单类型 | 标准/好物（每周）/公司单 |
| 消息冷却 | 30 秒 |

## 1.2 补给舱（Supply Pod）

**代码**: `cargo/supplypod.dm`（806 行）

| 机制 | 值 |
|---|---|
| **4 阶段延迟** | 飞行 30/坠落 4/开启 30/离开 30 |
| 落地伤害 | 舱下生物受击 |
| **爆炸** | 0,0,2,3（落地） |
| 变体 | 蓝空间舱（火花消失）/提取舱（辛迪加）/运输舱（回程） |

**8 种效果旗标**：

| 效果 | 说明 |
|---|---|
| **眩晕** | 舱下者眩晕至落地 |
| **断肢** | 弹掉一条肢体 |
| **爆器官** | 弹出全部肢体+器官 |
| **压碎** | 舱下者被压碎 |
| **隐身** | 不显示落地标记 |
| **静音** | 无声音 |
| **导弹** | 落地即删除（像导弹） |
| **任意角度** | 任意角度来袭 |


## 1.4 全局常量

| 常量 | 值 | 说明 |
|---|---|---|
| **CARGO_CRATE_VALUE** | **200** | 全站补给箱基准单位 |
| CARGO_MAX_ORDER | 50 | 同类订单单次上限 |
| PAYCHECK_LOWER/CREW/COMMAND | 25/50/100 | 工资基准 |
| BEACON_COST | 500 | 信标打印 |
| MAX_EMAG_ROCKETS | 5 | 骇入快运台导弹数 |

## 1.5 订单旗标（10 种）

| 旗标 | 说明 |
|---|---|
| ORDER_INVISIBLE | 永不显示 |
| ORDER_EMAG_ONLY | 仅骇入台 |
| ORDER_CONTRABAND | 仅违禁台 |
| ORDER_GOODY | 好物（私人账户） |
| ORDER_POD_ONLY | 仅快运台 |
| ORDER_SPECIAL | 条件性开放 |
| ORDER_NOT_DEPARTMENTAL | 部门台不可购 |
| ORDER_DANGEROUS | 购买通知管理员 |
| ORDER_COMPANY | 公司包（NOVA） |
| ORDER_DEPARTMENTAL_GOODY | 部门可购好物（NOVA） |

## 1.6 NOVA 修改

①公司包（company packs 2,153 行）②订单控制台限购（NT/INTERDYNE/TARKON/DS2）③I18N 全量汉化。


## 1.3 CentCom 投送器

**代码**: `cargo/centcom_podlauncher.dm`（888 行）——管理/中心投送（自定义效果/样式）。

---

# 比特跑者板块

# 第四章 · 比特跑者核心

**代码**: `code/modules/bitrunning/`（5,934 行）

## 4.1 玩法概述

比特跑者是**进入虚拟世界冒险**的玩法——通过网舱（Netpod）连接服务器，进入虚拟域（Virtual Domain）完成任务获取奖励（服务器点数/战利品）。

**流程**：网舱 → 连接服务器 → 选择虚拟域（花费点数） → 进入虚拟世界 → 完成任务 → 返回+奖励

## 4.2 服务器系统

**代码**: `bitrunning/server/`（1,356 行）

| 机制 | 说明 |
|---|---|
| 服务器 | 生成虚拟域+管理数据 |
| **威胁系统** | threats.dm（域内威胁） |
| 战利品 | loot.dm |
| 地图处理 | map_handling.dm |
| 对象生成 | obj_generation.dm |

## 4.3 网舱（Netpod）

**代码**: `bitrunning/netpod/`（559 行）

| 功能 | 说明 |
|---|---|
| 连接 | 玩家接入服务器 |
| 选择域 | 花费点数选虚拟域 |
| 进入/退出 | 虚拟世界往返 |


## 4.5 量子服务器

**代码**: `bitrunning/server/`（7 文件 1,356 行）

| 属性 | 值 |
|---|---|
| 部件 | cap 电容/scan 扫描/servo 伺服 |
| **电容系数** | 1.15 − 等级×0.15（冷却） |
| **扫描等级** | 决定域信息可见性 |
| **伺服加成** | Σ等级×0.1（断连伤害减免） |
| glitch 概率 | 0.2（EMAG ×2=0.4） |
| 威胁上限 | 15（EMAG ×2=30） |
| 冷却 | 2 分钟×电容系数 |
| 多人加成 | 1.1/额外玩家 |
| 无伤加成 | 0.8/无伤玩家 |
| **EMAG** | glitch×2+允许敌人**入侵站内** |

## 4.6 网舱（Netpod）

| 机制 | 值 |
|---|---|
| 回血 | 每秒 4 点 |
| 断连脑损 | 40×(1−servo_bonus) |
| 撬锁 | 15 秒强制断开 |
| 完成入账 | 点数×100 |

## 4.7 比特跑者装备

| 类型 | 说明 |
|---|---|
| **程序盘** | 5 类（tech 350-2500 np） |
| **花式盘** | 2 类（flair 50-1000 np） |
| BEPIS 盘 | 150-750 np |

## 4.8 评分系统

| 机制 | 值 |
|---|---|
| **评分公式** | 威胁×5+奖励+时间分×(难度+1) |
| 评级 | D~S |
| **BEPIS 科技盘** | A/S+难度≥中给 |
| 矿石倍率 | 铁 3/玻璃 2/银 0.7/钛 0.5/金 0.6/铀 0.4/钻石 0.3/蓝水晶 0.2 |

## 4.9 威胁角色

| 角色 | 威胁值 |
|---|---|
| **网络警察** | 0 |
| **网络战术** | 50 |
| **网络守护者** | 90（500HP 火箭兵） |


## 4.4 比特跑者装备

**代码**: `bitrunning/objects/`（1,232 行）+ `components/`（635 行）

- 虚拟世界道具/武器/工具
- 组件系统（域内装备）

---


---

# 第五章 · 虚拟域全录

**代码**: `bitrunning/virtual_domain/domains/`（1,092 行）——**25 个虚拟域**

## 5.1 域费用/难度/奖励分级

> cost：LOW/MEDIUM/HIGH/EXTREME；difficulty 同；reward 点数。

### Boss 战域（高难度/高奖励）

| 虚拟域 | 费用 | 难度 | 奖励 | 说明 |
|---|---|---|---|---|
| **血染巢穴（泡泡糖）** | 高 | 高 | 高 | 屠杀魔王泡泡糖；掉落泡泡糖玩偶 |
| **天试（巨像）** | 高 | 高 | 高 | 巨像试炼 |
| **狂热竞技场（圣职者）** | 高 | 高 | 高 | 圣职者竞技 |
| **冰川吞噬者（温迪戈）** | 高 | 高 | 高 | 温迪戈 |
| **炼狱（灰烬龙）** | 中 | 中 | 中 | 灰烬龙 |
| **血腥挖掘（血醉矿工）** | 中 | 中 | 中 | 血醉矿工 |

### 场景域（中低难度）

| 虚拟域 | 费用 | 难度 | 说明 |
|---|---|---|---|
| **绑架者飞船** | 中 | 中 | 绑架者场景 |
| **海滩酒吧** | — | — | 休闲 |
| **微风湾** | — | — | 场景 |
| **小丑星球** | 低 | 低 | 小丑 |
| **比萨派对** | 中 | 中 | 弗雷丁顿熊 |
| **海盗湾** | 中 | 中 | 海盗 |
| **蒸汽波（宇宙残迹）** | **极** | **无** | **最高难度域** |
| **异形巢穴** | 低 | 低 | 异形 |
| **感染域** | 中 | 中 | 灵能丧尸 |
| **板条箱混乱** | 低 | 中 | 灵能洗牌 |
| **管道工厂** | 低 | 低 | 垃圾管道 |
| **冰川研磨** | 低 | 低 | 悬崖地形 |
| **辛迪加突袭** | 中 | 中 | 辛迪加 |
| **戈尔贡达小行星** | — | — | 场景 |
| **草地狩猎** | — | — | 狩猎 |
| **异端狩猎** | 低 | 低 | 异端 |
| **岛屿混战** | 高 | 高 | 岛屿 |
| **元中心** | 低 | 低 | 场景 |

## 5.2 虚拟域机制

| 机制 | 说明 |
|---|---|
| 进入 | 网舱花费点数选择 |
| **完成奖励** | completion_loot（如泡泡糖玩偶）+reward_points |
| **强制着装** | forced_outfit（如矿工服） |
| 高级 NPC | 幽灵玩家扮演（lucky_ghosts） |
| 解锁 | scanner_tier+server_points 门槛 |



---

# 第二章 · 补给包全录

**代码**: `cargo/packs/`（3,933 行）——**299 个补给包**（16 类）

## 2.1 补给包全录（299 个，15 类）

> 花费换算：CCV=200 信用点（如 40×200=8000）。内容物已翻译。

### 通用补给包（2 个）

| 补给包 | 花费 | 内容物 |
|---|---|---|
| **采矿订单** | 动态 | 自定义矿物请求 |
| **材料订单** | 动态 | 自定义材料请求 |

### 服装玩具补给包（17 个）

| 补给包 | 花费 | 内容物 |
|---|---|---|
| **Collectable Hats Crate** | 8000 | 厨师、纸、收藏、船长、收藏、收藏、收藏、收藏、收藏、收藏、巫师、安全帽、安保总监、人事、收藏、收藏、史莱姆、收藏、史莱姆、收藏、收藏 |
| **Formalwear Crate** | 800 | 制服、制服×2、制服、服、制服、服、服、马甲、脖子饰品、脖子饰品、脖子饰品、头饰、头饰、头饰、头饰、头饰、鞋×3、服、服、服、服、服、随机 |
| **Hilarious Firing Pin Crate** | 2000 | 小丑 |
| **Laser Tag Crate** | 400 | 枪×3、枪×3、服×3、服×3、头盔×3、头盔×3 |
| **Knucklebones Game Crate** | 400 | D6 骰子×18、纸、蜡笔 |
| **Laser Tag Firing Pins Crate** | 700 | 盒 |
| **Mech Pilot's Suit Crate** | 600 | 服装×4 |
| **Original Costume Crate** | 400 | 服装、服装、鸡、服装、防毒面具、服装、服装、服装、服装、服装、服、服、服 |
| **Standard Costume Crate** | 400 | 小丑、鞋、防毒面具、小丑、自行车喇叭、默剧、鞋、白手套、默剧、头饰、服、药瓶、默剧 |
| **Toy Crate** | 1600 | （空/自定） |
| **Wizard Costume Crate** | 800 | 法杖、服、鞋、巫师 |
| **Trekkie Costume Crate** | 400 | 制服、制服、制服、制服、制服、制服、制服、制服、制服、制服、制服、制服 |
| **Big-Ass Booster Pack Pack** | 1000 | （空/自定） |
| **Sticker Pack Crate** | 600 | （空/自定） |
| **Corgi Pinata Kit** | 800 | 皮纳塔、baseball_bat、眼镜 |
| **Long Balloons Kit** | 800 | 盒、小丑 |
| **Surplus Christmas Gifts** | 6000 | （空/自定） |

### 紧急补给包（13 个）

| 补给包 | 花费 | 内容物 |
|---|---|---|
| **Biological Emergency Crate** | 400 | 头饰×2、服×2、生物防护、注射器×2、手套×2、盒、手套 |
| **Emergency Bot/Internals Crate** | 800 | 紧急氧气罐×5、呼吸面罩×5 |
| **Explosive Emergency Crate** | 400 | 头饰、服、防毒面具、螺丝刀、剪线钳、万能工具 |
| **Firefighting Crate** | 400 | 服×2、防毒面具×2、手电筒×2、氧气罐×2、高级×2、安全帽×2 |
| **Firefighting Tank Backpack** | 360 | 大气 |
| **Internals Crate** | 400 | 防毒面具×3、呼吸面罩×3、紧急氧气罐×3、氧气罐×3 |
| **Metal Foam Grenade Crate** | 480 | 盒 |
| **Plasmaman Space Envirosuits** | 700 | 等离子人×2、等离子人太空头盔×2 |
| **Plasmaman Supply Kit** | 600 | 等离子人×2、腰带×2、等离子人太空头盔×2、等离子人×2 |
| **Radiation Protection Crate** | 400 | 头饰×2、服×2、盖革计数器×2、药瓶、小酒杯×2 |
| **Space Suit Crate** | 600 | 服、头盔、呼吸面罩、气罐 |
| **Weed Control Crate** | 500 | 镰刀、植物学皮手套、防毒面具、手雷×2 |
| **Surplus Mothic Rations Triple-Pak** | 600 | 莫氏口粮×3 |

### 工程补给包（31 个）

| 补给包 | 花费 | 内容物 |
|---|---|---|
| **Anti-breach Shield Projector Crate** | 600 | 护盾发生器×2 |
| **APLU MK-I Crate** | 2000 | 瑞普利机甲、ripley_torso、ripley_right_arm、ripley_left_arm、ripley_right_leg、ripley_left_leg、零件、扫描模块、零件、主机、外设、drill、hydraulic_clamp |
| **Conveyor Assembly Crate** | 700 | 30、conveyor_switch_construct、纸 |
| **Engineering Gear Crate** | 800 | 腰带×3、服×3、头饰×3、安全帽×3、眼镜×2 |
| **Insulated Gloves Crate** | 1600 | 手套×3 |
| **NT-75 Electromagnetic Power Inducers Crate** | 800 | orderable×2 |
| **P.A.C.M.A.N Generator Crate** | 1000 | pacman |
| **Power Cell Crate** | 600 | 电池×3 |
| **Shuttle Engine Crate** | 1200 | shuttle_engine |
| **Toolbox Crate** | 1000 | 工具箱×3、工具箱×3 |
| **Portable Air Pump Crate** | 900 | 泵×2 |
| **Portable Scrubber Crate** | 900 | 净气器×2 |
| **Huge Portable Scrubber Crate** | 1500 | 货运 |
| **Space Heater Crate** | 400 | space_heater |
| **Bluespace Artillery Parts** | 6000 | 工程、front、middle、back、电脑 |
| **DNA Vault Parts** | 4800 | dna_vault、DNA 探针×5 |
| **DNA Vault Samplers** | 1200 | DNA 探针×5 |
| **Shield Generator Satellite** | 1200 | meteor_shield×3 |
| **Shield System Control Board** | 2000 | 电脑 |
| **Chief Engineer Turtlenecks** | 400 | 工程、工程 |
| **Emitter Crate** | 1400 | emitter×2 |
| **Field Generator Crate** | 1400 | 发电机×2 |
| **Grounding Rod Crate** | 1600 | grounding_rod×4 |
| **Solar Panel Crate** | 1600 | solar_assembly×21、电脑、tracker、纸 |
| **Supermatter Shard Crate** | 4000 | shard |
| **Tesla Coil Crate** | 2000 | tesla_coil×4 |
| **HFR Crate** | 4600 | corner×4、fuel_input、moderator_input、waste_output、interface、核心 |
| **Radiation Protection Modules** |  | MOD 防辐射模块×3 |
| **Radioactive Nebula Shielding** | 400 | MOD 防辐射模块×5、radioactive_nebula_shielding×5、纸×1 |
| **Portable Gravity Unit Crate** | 800 | portagrav×1 |
| **Golf Cart Parts Kit Crate** | 2200 | golfcart_kit×1、golfcart×2、电池×1 |

### 探索补给包（3 个）

| 补给包 | 花费 | 内容物 |
|---|---|---|
| **Scrapyard Crate** | 1000 | relic、broken_bottle、rusted |
| **Catering Crate** | 1000 | sandwich×5 |
| **Shrubbery Crate** | 1000 | 灌木×8 |

### 杂项补给包（22 个）

| 补给包 | 花费 | 内容物 |
|---|---|---|
| **Art Supplies** | 360 | rcl、工具箱、工具箱、蜡笔×3、crayons、蜡笔、蜡笔 |
| **Tattoo Kit** | 360 | tattoo_kit、墨粉×2 |
| **Bicycle** | 1000000 | bicycle |
| **Big Band Instrument Collection** | 2000 | violin、guitar、glockenspiel、accordion、saxophone、trombone、recorder、harmonica、未锚定 |
| **Book Crate** | 600 | 书、书×3、书×3 |
| **Command Encryption Key Crate** | 800 | headset_com×3 |
| **Exploration Drone** | 1000 | exodrone |
| **Drone Fuel Pellet** | 600 | fuel_pellet |
| **Bureaucracy Crate** | 640 | wheeled、camera_film、手持标签机、hand_labeler_refill×2、paper_bin、carbon、笔×2、笔、笔、笔、笔、蓝色、red、黄色、clipboard×2、盒、purple |
| **Calligraphy Crate** | 290 | 盒 |
| **Festive Wrapping Paper Crate** | 360 | wrapping_paper |
| **Funeral Supplies Crate** | 320 | 丧服、harebell、geranium |
| **Empty Supplypod** | 120 | （空/自定） |
| **Religious Supplies Crate** | 1200 | 药瓶×2、书×2、服×2、丧服×2 |
| **Candle Box Crate** | 400 | candle_box×3 |
| **Toner Crate** | 400 | 墨粉×6 |
| **Toner Crate (Large)** | 1200 | 墨粉×6 |
| **Training Toolbox Crate** | 400 | training_toolbox×2 |
| **Assorted Syndicate Gear** |  | （空/自定） |
| **Paper Cutters Crate** | 700 | papercutter×3、cutterblade×1 |
| **Liquid Cooler - Water** | 400 | 饮水机(无桶)×1、water×1 |
| **Liquid Cooler - Fruit Punch** | 1200 | 饮水机(无桶)×1、punch×1 |

### 进口补给包（30 个）

| 补给包 | 花费 | 内容物 |
|---|---|---|
| **Foam Force Crate** | 400 | 玩具×8 |
| **Foam Force Pistols Crate** | 600 | 玩具×2、玩具×2 |
| **Duct Spider Crate** | 800 |  |
| **Duct Spider Crate?** | 800 |  |
| **50 Bamboo Cuttings** | 3000 | 板 |
| **Wind Turbine Crate** | 400 | loaded |
| **A Single Sheet of Bananium** | 20000 | 板 |
| **A....Dumpster?** | 1000 |  |
| **A....Dumpster** | 1000 | dumpster、garbage×5 |
| **Lethal Shotgun Shell Box Crate** | 2000 | 盒×3 |
| **NULL_ENTRY** | 20000 | 书 |
| **Cosa Nostra Starter Pack** | 800 | （空/自定） |
| **'Contraband' Crate** | 4000 | contraband×5 |
| **Smuggled WT-550 Autorifle Crate** | 1400 | 枪×2、WT550 弹匣×2 |
| **Smuggled WT-550 Ammo Crate** | 800 | WT550 弹匣×2、弹匣×2、弹匣×2 |
| **Shocktrooper Crate** | 2000 | 盒、手雷×3、手雷×2、手雷×2、护甲背心、头盔 |
| **Special Ops Crate** | 2000 | 面具、制服、腰带、ID 卡、switchblade、手雷×5 |
| **Russian Surplus Military Gear Crate** | 2400 | rationpack、快速装弹器、快速装弹器、strilka、枪械维护用品、护甲背心、头盔、鞋、战斗擒抱手套、制服、服装、面具、头盔、护甲背心、工具箱×2 |
| **Refurbished Sakhno Precision Rifle Crate** | 1200 | 枪×6 |
| **Biker Gang Kit** | 800 | atv、atv、服、手套、头饰、骷髅 |
| **Abandoned Crate** | 10000 | （空/自定） |
| **Shamber's Juice Eldritch Energy! Crate** | 10000 | eldritch×1 |
| **Animal Hide Crate** | 6000 | animalhide×5 |
| **Dreadnog Carton Crate** | 1000 | 药瓶×3 |
| **Big Slappy parts** | 4400 | giant_wrench |
| **Galactic Materials Market Crate** | 600 | materials_market×1、板×5、five×2、扫描模块×1、零件×1 |
| **Floor-瓷砖 Camouflage Uniform** | 1200 | 制服×3、面具×3、手套×3、鞋×3、floortile×3 |
| **Black Market LTSRBT** | 2000 | ltsrbt、矿石×2、零件 |
| **Cargo Shuttle Upgrade: Air Renewal** | 8000 | 纸 |
| **Jet Boots** | 40000 | 鞋 |

### 牲畜补给包（27 个）

| 补给包 | 花费 | 内容物 |
|---|---|---|
| **Bird Crate** | 800 |  |
| **Butterflies Crate** | 1000 |  |
| **Cat Crate** | 800 | 宠物项圈、玩具 |
| **Chicken Crate** | 400 |  |
| **Corgi Crate** | 800 | 宠物项圈 |
| **Cow Crate** | 600 |  |
| **Sheep Crate** | 600 |  |
| **Pig Crate** | 600 |  |
| **Pony Crate** | 1000 |  |
| **Crab Rocket** | 1600 |  |
| **Exotic Corgi Crate** | 1400 | 宠物项圈 |
| **Fox Crate** | 800 | 宠物项圈 |
| **Goat Crate** | 600 |  |
| **Rabbit Crate** | 400 |  |
| **Mothroach Crate** | 400 |  |
| **Monkey Cube Crate** | 800 | （空/自定） |
| **Pug Crate** | 800 | 宠物项圈 |
| **Bull Terrier Crate** | 800 | 宠物项圈 |
| **Snake Crate** | 600 |  |
| **Amphibian Friends Crate** | 400 | frog |
| **Lizard Crate** | 400 |  |
| **Garden Gnome Crate** | 3000 |  |
| **Aquarium Fish Case** | 400 | 随机×2 |
| **Freshwater Fish Case** | 400 | freshwater×2 |
| **Saltwater Fish Case** | 400 | saltwater×2 |
| **Tiziran Fish Case** | 400 | tiziran×2 |
| **Turtle Crate** | 400 |  |

### 材料补给包（9 个）

| 补给包 | 花费 | 内容物 |
|---|---|---|
| **50 Cardboard Sheets** | 400 | 板 |
| **50 Empty License Plates** | 400 | 50 |
| **50 Plastic Sheets** | 400 | 板 |
| **30 Sandstone Blocks** | 400 | 板 |
| **50 Wood Planks** | 800 | 板 |
| **Firefighting Foam Tank Crate** | 600 | foamtank |
| **Fuel Tank Crate** | 320 | fueltank |
| **Large Fuel Tank Crate** | 800 | large |
| **Water Tank Crate** | 240 | 水箱 |

### 医疗补给包（19 个）

| 补给包 | 花费 | 内容物 |
|---|---|---|
| **Blood Pack Variety Crate** | 1400 | 血袋×2、血袋、血袋、血袋、血袋、血袋、O- 血袋、蜥蜴、血袋、医疗 |
| **Medipen Variety-Pak** | 700 | medipen×2、ekit×3、blood_loss×3 |
| **Autopsy Kit** | 500 | autopsy_scanner×1、coroner×1 |
| **Chemical Starter Kit Crate** | 520 | 药瓶、药瓶、药瓶、氧气罐、药瓶、药瓶、药瓶、药瓶、药瓶、药瓶、药瓶、药瓶、糖、科研、dropper、烧杯盒 |
| **Defibrillator Crate** | 1000 | 除颤器×2 |
| **IV Drip Crate** | 400 | iv_drip |
| **Medical Supplies Crate** | 800 | 药瓶、药瓶、药瓶、药瓶、烧杯、药丸、医疗、盒、烧杯盒、盒、注射器盒、盒、regular、o2、toxin、brute、fire、除颤器、O- 血袋、mining、药丸、医疗×2、医疗补货、药品补货 |
| **Experimental Medicine Crate** | 600 | sansufentanyl×2 |
| **Surgical Supplies Crate** | 1200 | 满、sterilizine、emergency_bed |
| **Heavy-Duty Saline Canister** | 1200 | saline |
| **Virus Crate** | 1000 | 药瓶、药瓶、药瓶×4、药瓶、药瓶、药瓶、药瓶、药瓶、药瓶、注射器盒、烧杯盒、药瓶 |
| **Chief Medical Officer Turtlenecks** | 400 | 医疗、医疗 |
| **Strong-Arm Implant Set** | 1200 | strongarm×2 |
| **Paperwork Implant Set** | 600 | paperwork×2 |
| **Recovered NT Employee corpse** | 1000 | with_body |
| **Organ Growing Kit** | 1000 | 未锚定、organ、培养皿盒、未锚定、mortar、pestle |
| **Chiral Inversing Buffer Crate** | 600 | 药瓶 |
| **Handheld Crew Monitor Crate** | ? | 传感器设备、传感器设备、传感器设备 |
| **DeForest First Aid Station** | 1200 | wall_healer |

### 有机补给包（27 个）

| 补给包 | 花费 | 内容物 |
|---|---|---|
| **Beekeeper Suit Crate** | 400 | 养蜂人头盔×2、养蜂人服×2 |
| **Beekeeping Starter Crate** | 600 | 未拧紧、honey_frame×3、bought、养蜂人头盔、养蜂人服、flyswatter |
| **Excellent Meat Crate** | 800 | 史莱姆、killertomato、bear、xeno、蜘蛛、rawbacon、penguin、spiderleg、carp、human、grassfed |
| **Fruit Crate** | 600 | lime、橙子、watermelon、apple、berries、lemon |
| **Vegetables Crate** | 360 | 辣椒、玉米、番茄、土豆、胡萝卜、chanterelle、onion、pumpkin、黄瓜 |
| **Exotic Seeds Crate** | 600 | amanita、bamboo、eggy、liberty、nettle、plump、replicapod、reishi、rainbow_bunch、seedling、灌木、随机×2 |
| **Food Crate** | 400 | flour、水稻、牛奶、豆奶、saltshaker、peppermill、egg_box、enzyme、糖、猴子、banana×3 |
| **High-yield Clown-grade Cream Pie Crate** | 2400 | 小丑 |
| **Hydroponics Crate** | 600 | plantbgone×2、药瓶×2、hatchet、cultivator、plant_analyzer、植物学皮手套、服 |
| **Hydroponics Backpack Crate** | 400 | 水箱 |
| **Pizza Crate** | 2000 | margherita×10、meat×10、mushroom×10、vegetable×10、donkpocket×10、dank×7、sassysage×10、pineapple×10、arnold×3、energy×5 |
| **Potted Plants Crate** | 300 | 随机×5 |
| **Seeds Crate** | 400 | 辣椒、cotton、berry、玉米、eggplant、番茄、soya、wheat、水稻、胡萝卜、向日葵、rose、chanter、土豆、sugarcane、黄瓜 |
| **Grilling Starter Kit** | 800 | 板、tongs、猴子能量汽水、未拧紧 |
| **Grilling Fuel Kit** | 800 | 板、猴子能量汽水 |
| **Tiziran Supply Box** | 600 | 盒、盒、盒 |
| **Mothic Supply Box** | 600 | 盒、盒、莫氏口粮 |
| **Coffee Syrups Box** | 800 | 焦糖糖浆、药瓶、药瓶 |
| **Contraband Syrups Box** | 1200 | 药瓶×2 |
| **Cooking Oil Vat** | 950 | cooking_oil |
| **Beer Keg** | 1250 | beer |
| **Nutraslop Serving Dish** | 800 | 未锚定 |
| **NT-Ag Potting Soil Crate** | 400 | soil_sack×5 |
| **NT-Ag Vermaculite Crate** | 400 | vermaculite×3 |
| **NT-Ag Hydrogel Beads Crate** | 400 | gel×3 |
| **NT-Ag Korta Coir Crate** | 600 | coir×3 |
| **NT-Ag Worm Castings Crate** | 800 | worm×3 |

### 科研补给包（9 个）

| 补给包 | 花费 | 内容物 |
|---|---|---|
| **Plasma Assembly Crate** | 400 | 等离子罐×3、igniter×3、接近传感器×3、timer×3 |
| **Robotics Assembly Crate** | 600 | 接近传感器×4、healthanalyzer×2、安全帽×2、medkit×2、工具箱×2、cleanbot×2 |
| **RPED crate** | 600 | 货运 |
| **Shield Generator Crate** | 800 | shieldwallgen×4 |
| **Tank Transfer Valves Crate** | 2400 | transfer_valve×2 |
| **Monkey Mind Magnification Helmet crate** | 600 | 头盔×2 |
| **Cytology supplies crate** | 600 | microscope、生物防护psy_tool、培养皿盒×2、盒、vatgrower、protein |
| **MOD 核心 Crate** | 600 | standard×3 |
| **Gizmo research crate** | 1000 | 小工具×1、小工具×2 |

### 安保补给包（41 个）

| 补给包 | 花费 | 内容物 |
|---|---|---|
| **Ammo Crate** | 1600 | 盒×3、盒×3、.38 追踪弹药、快速装弹器、快速装弹器 |
| **Armor Crate** | 600 | 护甲背心×3 |
| **Security MOD plating** | ? | 安保 |
| **Disabler Crate** | 600 | 枪×3 |
| **Forensics Crate** | 500 | detective_scanner、盒、摄像头、taperecorder、蜡笔、头饰、detectiveboard |
| **Helmets Crate** | 600 | 头盔×3 |
| **Security Barrier Grenades** | 400 | 手雷×4 |
| **Security Clothing Crate** | 600 | 安保×2、服×2、头饰×2、安保、服、头饰、安保、安保总监、安保总监 |
| **Stingbang Grenade Pack** | 1000 | 盒 |
| **Security Supplies Crate** | 700 | 盒、盒、盒、盒 |
| **Gun Maintenance Kits** | 400 | 枪械维护用品×3 |
| **Standard Firing Pins Crate** | 800 | 盒×2 |
| **Paywall Firing Pins Crate** | 400 | 盒×2 |
| **Standard Justice Enforcer Crate** | 1200 | 头盔、防毒面具 |
| **Stun Batons Crate** | 600 | 安保×3 |
| **Wall-Mounted Flash Crate** | 400 | 盒×4 |
| **Traditional Equipment Crate** | 440 | 安保、服装、白手套、面具、conversion_kit |
| **Bulletproof Armor Crate** | 600 | 护甲×3 |
| **Bulletproof Helmets Crate** | 600 | 头盔×3 |
| **Chemical Implants Crate** | 700 | 盒 |
| **Combat Shotguns Crate** | 3500 | 枪×3、腰带×3 |
| **DRAGnet Translocation Shotgun Crate** | 3600 | 枪×2、dragnet_beacon×1 |
| **Energy Guns Crate** | 3600 | 枪×3 |
| **Type 5 Laser Gun Crate** | 800 | 枪×3 |
| **Type 5/R Laser Carbine Crate** | 1800 | 枪×3 |
| **Disabler SMG Crate** | 1400 | 枪×3 |
| **NT BR-38 Crate** | 20000 | 枪×3、弹匣×6 |
| **NT BR-38 Magazine Crate** | 1400 | 弹匣×2、弹匣×2、弹匣×2 |
| **Exile Implants Crate** | 700 | 盒 |
| **Incendiary Weapons Crate** | 1400 | 满、等离子罐×3、手雷×3 |
| **Mindshield Implants Crate** | 1200 | loyalty |
| **Tracking Implants Crate** | 900 | 盒、.38 追踪弹药×3 |
| **Reflector Vest Crate** | 1000 | 护甲×2 |
| **Riot Armor Crate** | 1200 | 护甲×3 |
| **Riot Helmets Crate** | 800 | 头盔×3 |
| **Riot Shields Crate** | 1000 | 盾×3 |
| **SWAT Crate** | 1400 | 头盔×2、护甲×2、防毒面具×2、腰带×2、战斗擒抱手套×2 |
| **Thermal Pistol Crate** | 2000 | 腰带×2 |
| **Sunglasses Crate** | 400 | 眼镜×1 |
| **Heavy Thermal Guns Crate** | 5000 | cryo、inferno |
| **Hemoparasite Testing Crate** | 10000 | blood_worm_tester×4 |

### 服务补给包（24 个）

| 补给包 | 花费 | 内容物 |
|---|---|---|
| **Cargo Supplies Crate** | 350 | granted、denied、universal_scanner、dest_tagger、手持标签机、package_wrap |
| **High-traction Floor Tiles** | 800 | 30 |
| **Janitorial Supplies Crate** | 400 | bucket×3、mop、pushbroom、服×3、trash、cleaner、rag、手雷×3 |
| **Janitorial Cart and Galoshes Crate** | 800 | janitorialcart、鞋 |
| **Janitor Backpack Crate** | 400 | 清洁工 |
| **MULEbot Crate** | 800 |  |
| **Party Equipment** | 1000 | 盒、shaker、药瓶、药瓶、药瓶×2、sixbeer、sixsoda、glowstick、red、蓝色、cyan、橙子、黄色、pink |
| **Premium Carpet Crate** | 400 | 50×2、50×2 |
| **Exotic Carpet Crate** | 1600 | 50×2、50×2、50×2、50×2、50×2、50×2、50×2、50×2 |
| **Simple Neon Carpet Crate** | 3000 | 60×2、60×2、60×2、60×2、60×2、60×2、60×2、60×2、60×2、60×2、60×2、60×2、60×2 |
| **Replacement Lights** | 400 | 盒×3 |
| **Shaft Miner Starter Kit** | 800 | mining_conscript |
| **Survival Knives Crate** | 600 | 刀×3 |
| **Wedding Crate** | 600 | 制服、服、腰带、服装、bouquet、向日葵、poppy、药瓶 |
| **Grey ID Card Multipack Crate** | 600 | 盒 |
| **Silver ID Card Crate** | 1400 | ID 卡 |
| **Empty Crate** | 280 | （空/自定） |
| **Donk Pocket Variety Crate** | 800 | 盒、盒、盒、盒、盒 |
| **Ready-Donk Variety Crate** | 600 | ready_donk、mac_n_cheese、donkhiladas、nachos_grandes、donkrange_chicken、salisbury_steak、country_chicken |
| **Coffee Equipment Crate** | 800 | 盒、盒、coffeepot、coffee_condi_display、药瓶、牛奶、豆奶、糖、焦糖糖浆 |
| **Impressa Coffeemaker Crate** | 800 | impressa |
| **Bar Sign Replacement Kit** | 2800 | all_access |
| **Fletching and Bow-Making Starter Kit** | 300 | 书×1、板×10、板×10 |
| **Pest-B-Gon Mousetraps** | 400 | 盒×3 |

### 售货机补货补给包（25 个）

| 补给包 | 花费 | 内容物 |
|---|---|---|
| **Booze-o-mat and Coffee Supply Crate** | 400 | boozeomat、coffee |
| **Cigarette Supply Crate** | 400 | cigarette |
| **Cytology Vendor Supply Crate** | 600 | cytopro |
| **Dinnerware Supply Crate** | 400 | dinnerware |
| **Deluxe Silicate Selections Restock** | 600 | modularpc |
| **EngiVend Supply Crate** | 600 | engivend |
| **Games Supply Crate** | 400 | games |
| **Hydroponics Vending Machines Refills** | 800 | hydroseeds、hydronutrients |
| **Imported Vending Machines** | 1000 | sustenance、robotics、sovietsoda、工程 |
| **Medical Vending Crate** | 700 | 医疗补货、药品补货、wallmed |
| **PTech Supply Crate** | 500 | cart |
| **SecTech Supply Crate** | 600 | 安保 |
| **Snack Supply Crate** | 400 | snack |
| **Softdrinks Supply Crate** | 400 | cola |
| **Part-Mart & YouTool Supply Crate** | 600 | assist、youtool |
| **ClothesMate Supply Crate** | 400 | vending_refill |
| **Autodrobe Supply Crate** | 400 | autodrobe |
| **Cargo Wardrobe Supply Crate** | 300 | cargo_wardrobe |
| **Engineering Wardrobe Supply Crate** | 600 | engi_wardrobe、atmos_wardrobe |
| **General Wardrobes Supply Crate** | 1200 | curator_wardrobe、bar_wardrobe、chef_wardrobe、chap_wardrobe |
| **Hydrobe Supply Crate** | 300 | hydro_wardrobe |
| **JaniDrobe Supply Crate** | 300 | jani_wardrobe |
| **Medical Wardrobe Supply Crate** | 1200 | medi_wardrobe、chem_wardrobe、viro_wardrobe、coroner_wardrobe |
| **Science Wardrobe Supply Crate** | 900 | robo_wardrobe、gene_wardrobe、science_wardrobe |
| **Security Wardrobe Supply Crate** | 800 | sec_wardrobe、det_wardrobe、law_wardrobe |
## 3.1 悬赏系统（Bounties）

**代码**: `cargo/bounties/`（2,006 行）——**178 个悬赏**（16 个类别文件）

| 类别文件 | 内容 |
|---|---|
| assistant.dm | 助手物品悬赏（电击棒/长矛/工具箱/棒球棒/伸缩手/甜甜圈/Donk 口袋/猴子皮/盆栽/IED/气动炮/火焰喷射器等） |
| atmos.dm | 大气工程师悬赏（满罐稀有气体：钚氧/硝三烯/氟利昂/氚/氢/扎克气等） |
| bitrunning.dm | 比特跑者悬赏（绑架者玩偶/泡泡糖玩偶/自行车喇叭/披萨/灵能耳机/极地乌尚卡帽/辛迪加玩偶） |
| botany.dm | 植物悬赏（安布罗西亚/金苹果/香蕉/蓝空香蕉/死亡浆果/大麻/生命草/欧米茄草等） |
| chef.dm | 厨师悬赏（生日蛋糕/汤/爆米花/洋葱圈/冰淇淋三明治/面包/派/沙拉/超级巨无霸/罂粟椒盐卷饼等） |
| engineering.dm | 工程师悬赏（发射器/水培托盘/充电站/蓄电单元/PACMAN 发电机/力场发生器/特斯拉线圈/焊接燃料箱/反射板） |
| item.dm | 通用物品悬赏（悬赏凭证/全站悬赏凭证） |
| mecha.dm | 机甲悬赏（Ripley/Clarke/Odysseus/Gygax/Durand 机甲） |
| medical.dm | 医疗悬赏（心/肺/阑尾/耳/肝/眼/舌/蜥尾/猫尾/电锯/九尾鞭/手术电脑等） |
| mining.dm | 矿工悬赏（熔岩烤戈利亚牛排/戈利亚皮船/骨桨/骨斧/骨甲/骷髅头盔/骨护符/观察者花环/蘑菇碗等） |
| reagent.dm | 试剂悬赏（简单/复杂饮品、简单/稀有化学品、药丸，动态目标） |
| science.dm | 科学悬赏（E.X.P.E.R.I-MENTOR 装置/技术盘/基因致残突变器/模块化电脑/精炼核心等） |
| security.dm | 安保悬赏（巡逻站点/没收违禁品） |
| slime.dm | 史莱姆悬赏（绿/粉/金/油/黑/浅粉/精金/彩虹史莱姆核） |
| special.dm | 特殊悬赏（外星器官/辛迪加文件/精金） |
| virus.dm | 病毒悬赏（抗性/阶段速度/隐蔽/传播 4 种动态属性） |

**悬赏机制**：

| 机制 | 说明 |
|---|---|
| 刷新 | 每日刷新悬赏列表 |
| 完成 | 上贡指定物品/达到条件 |
| 奖励 | 信用点（银行入账） |
| 助手悬赏 | 上贡违禁/恶搞物品（电击棒/肥皂/IED） |

## 3.2 出口系统（Exports）

**代码**: `cargo/exports/`（1,340 行）+ `exports.dm`（257 行）——**19 个类别文件**

| 出口品 | 价值 |
|---|---|
| **鱼** | 30 信用点 |
| **食物** | 10 |
| **材料** | 5（基础） |
| **悬赏箱** | 1 |

**出口机制**：把物品放进货运出口 → 按物品价值折算信用点 → 增加预算。

## 3.3 市场系统（Markets）

**代码**: `cargo/markets/`（1,727 行）+ `materials_market.dm`（386 行）

| 市场 | 说明 |
|---|---|
| **材料市场** | 材料价格波动（事件联动：市场崩盘时硬锁最低） |
| 其他市场 | 商品价格动态 |


## 3.5 悬赏全录（167 个活跃 + 11 移除）

> 奖励换算：CCV=200 信用点。botany 悬赏 = 2000+multiplier×400 信用点，数量 rand(5,10)。

### 助理悬赏（20 个）

| 悬赏 | 需要的物品/条件 | 奖励（信用点） |
|---|---|---|
| **Stunprod** | 电击棒 | 520 |
| **Spears** | 长矛 ×5 | 800 |
| **Stocked Toolbox** | 工具箱（6 件套工具） | 800 |
| **Statue** | 雕像 | 800 |
| **Baseball Bat** | 棒球棒 ×5 | 800 |
| **Extendo-Hand** | 伸缩手 | 1000 |
| **Donuts** | 甜甜圈 ×6 | 1200 |
| **Donk-Pockets** | Donk 口袋 ×10 | 1200 |
| **Monkey Hide** | 猴子皮 | 600 |
| **Comfy Chairs** | 舒适椅 ×5 | 600 |
| **Geraniums** | 罂粟 ×3 | 1600 |
| **Poppies** | 罂粟 ×3 | 400 |
| **Potted Plants** | 盆栽植物、盆栽植物 ×3 | 800 |
| **Monkey Cubes** | 猴子方块 ×3 | 800 |
| **IED** | 简易炸弹 IED ×3 | 800 |
| **Water Tank** | 水箱 | 1000 |
| **Pneumatic Cannon** | 气动炮 | 800 |
| **Junk Shells** | 弹壳 ×5 | 800 |
| **Flamethrower** | 火焰喷射器 | 800 |
| **Fish** | 鱼、鱼盒 ×4 | 1900 |

### 大气工程师悬赏（7 个）

| 悬赏 | 需要的物品/条件 | 奖励（信用点） |
|---|---|---|
| **Gas Parent** | 气罐 | 3000 |
| **Full Tank of Pluoxium** | （特殊条件） |  |
| **Full Tank of Nitrium** | （特殊条件） |  |
| **Full Tank of Freon** | （特殊条件） |  |
| **Full Tank of Tritium** | （特殊条件） |  |
| **Full Tank of Hydrogen** | （特殊条件） |  |
| **Full Tank of Zauker** | （特殊条件） | 4000 |

### 比特跑者悬赏（7 个）

| 悬赏 | 需要的物品/条件 | 奖励（信用点） |
|---|---|---|
| **Abductor Plush** | 绑架者玩偶 ×1 | 1200 |
| **Bubblegum Plush** | 泡泡糖玩偶 ×1 | 2000 |
| **Bike Horn** | 自行车喇叭 ×1 | 800 |
| **Slice of Pizzeria Pizza** | 披萨片 ×1 | 1600 |
| **Psyker Headset** | 灵能耳机 ×1 | 1600 |
| **Polar Ushanka** | 极地乌尚卡帽 ×1 | 600 |
| **Syndicate Plush** | 核弹玩偶 ×1 | 1600 |

### 植物学家悬赏（35 个）

| 悬赏 | 需要的物品/条件 | 奖励（信用点） |
|---|---|---|
| **Ambrosia Vulgaris Leaves** | 仙草 | 2000 |
| **Ambrosia Gaia Leaves** | 盖亚仙草 | 3600 |
| **Golden Apples** | 金苹果 | 3600 |
| **Bananas** | 香蕉、蓝空间香蕉 | 2000 |
| **Bluespace Bananas** | 蓝空间香蕉 | 2800 |
| **Koi Beans** | 锦鲤豆 | 2800 |
| **Death Berries** | 死亡莓 | 2800 |
| **Glow-Berries** | 发光莓 | 2800 |
| **Cannabis Leaves** | 大麻、生命草（白大麻）、死亡草（死大麻）、欧米茄草 | 3600 |
| **Lifeweed Leaves** | 生命草（白大麻） | 4400 |
| **Deathweed Leaves** | 死亡草（死大麻） | 4400 |
| **Omega Weed Leaves** | 欧米茄草 | 4400 |
| **Wheat Grains** | 小麦 | 2000 |
| **Rice Grains** | 水稻 | 2000 |
| **Chili Peppers** | 辣椒 | 2000 |
| **Chilly Peppers** | 冰椒 | 2800 |
| **Ghost Chili Peppers** | 幽灵椒 | 2800 |
| **Limes** | 酸橙 | 2000 |
| **Lemons** | 柠檬 | 2000 |
| **Oranges** | 橙子 | 2000 |
| **Eggplants** | 茄子 | 2000 |
| **Egg-plants** | 蛋茄子 | 2800 |
| **Kudzu Pods** | 葛藤荚 | 3600 |
| **Watermelons** | 西瓜 | 2000 |
| **Holy Melons** | 圣瓜 | 2800 |
| **Glowshrooms** | 发光菇、发光帽菇、影菇 | 2000 |
| **Glowcaps** | 发光帽菇 | 2800 |
| **Shadowshrooms** | 影菇 | 2800 |
| **Death Nettles** | 死亡荨麻 | 2800 |
| **Pineapples** | 菠萝 | 2000 |
| **Tomatoes** | 番茄、番茄 | 2000 |
| **Bluespace Tomatoes** | 蓝空间番茄 | 3600 |
| **Oats** | 燕麦 | 2800 |
| **Lit Bonfire** | 点燃的篝火 | 2000 |
| **Cucumbers** | 黄瓜 | 2000 |

### 厨师悬赏（22 个）

| 悬赏 | 需要的物品/条件 | 奖励（信用点） |
|---|---|---|
| **Birthday Cake** | 生日蛋糕、birthday | 1600 |
| **Soup** | （特殊条件） |  |
| **Popcorn Bags** | 爆米花 ×3 | 1200 |
| **Onion Rings** | 洋葱圈 ×3 | 1200 |
| **Ice Cream Sandwiches** | 冰淇淋三明治 ×3 | 1600 |
| **Strawberry Ice Cream Sandwiches** | 草莓冰淇淋三明治 ×3 | 2000 |
| **Bread** | 面包、面包、bun、pizzabread、rawpastrybase | 400 |
| **Pie** | 派 | ? |
| **Salad or Rice Bowls** | 沙拉/米饭碗 ×3 | 1200 |
| **Carrot Fries** | 胡萝卜薯条 ×3 | 1400 |
| **Super Bite Burger** | 超级咬汉堡 | 4800 |
| **Poppy Pretzel** | 罂粟椒盐卷饼 | 1200 |
| **Cuban Carp** | 古巴鲤 | 3200 |
| **Hot Dog** | 热狗 | 3200 |
| **Eggplant Parmigianas** | 茄子帕玛森 ×3 | 1400 |
| **Muffins** | 松饼 ×3 | 1200 |
| **Chawanmushi** | 茶碗蒸 | 3200 |
| **Kebabs** | 烤肉串 ×3 | 1400 |
| **Soylent Green** | 人造绿蛋白 | 2000 |
| **Pancakes** | 煎饼 ×13 | 2000 |
| **Chicken Nuggets** | 鸡块 ×6 | 1600 |
| **Pickles** | 腌黄瓜 ×7 | 2000 |

### 工程师悬赏（9 个）

| 悬赏 | 需要的物品/条件 | 奖励（信用点） |
|---|---|---|
| **Emitter** | 发射器 | 1000 |
| **Hydroponics Tray** | 水培托盘 | 800 |
| **Recharging Station** | 充电站 | 1000 |
| **Power Storage Unit** | SMES 蓄电单元 | 1200 |
| **P.A.C.M.A.N. Generator** | P.A.C.M.A.N. 发电机 | 1000 |
| **Field Generator** | 力场发生器 | 1200 |
| **Tesla Coil** | 特斯拉线圈 | 1000 |
| **Welding Fuel Tank** | 燃料箱 | 1000 |
| **Reflector** | 反射器 | 1400 |

### 机甲悬赏（5 个）

| 悬赏 | 需要的物品/条件 | 奖励（信用点） |
|---|---|---|
| **APLU MK-II \** | （特殊条件） | 1300 |
| **Clarke** | （特殊条件） | 2400 |
| **Odysseus** | （特殊条件） | 1100 |
| **Gygax** | （特殊条件） | 5600 |
| **Durand** | （特殊条件） | 4000 |

### 医疗悬赏（13 个）

| 悬赏 | 需要的物品/条件 | 奖励（信用点） |
|---|---|---|
| **Heart** | 心脏（合成除外）、心脏（合成除外）、心脏（合成除外）、心脏（合成除外）、心脏（合成除外） | 1000 |
| **Lungs** | 肺（合成除外）、肺（合成除外）、肺（合成除外）、肺（合成除外）、肺（合成除外） ×3 | 2000 |
| **Appendix** | 阑尾 | 1000 |
| **Ears** | 耳朵（合成除外）、耳朵（合成除外）、耳朵（合成除外）、耳朵（合成除外）、耳朵（合成除外）、耳朵（合成除外）、耳朵（合成除外） ×3 | 2000 |
| **Livers** | 肝（合成除外）、肝（合成除外）、肝（合成除外）、肝（合成除外）、肝（合成除外） ×3 | 2000 |
| **Organic Eyes** | 眼（合成除外）、眼（合成除外）、眼（合成除外） ×3 | 2000 |
| **Tongues** | 舌头 ×3 | 2000 |
| **Lizard Tail** | 蜥蜴尾巴 | 1200 |
| **Cat Tail** | 猫尾巴 | 1200 |
| **Chainsaw** | 链锯 | 1000 |
| **Surgery Computer** | 手术电脑 | 2400 |
| **Operating Table** | 手术台 | 1200 |
| **Crew Medical Scanning** | 医疗健康报告 ×2 | 1600 |

### 矿工悬赏（13 个）

| 悬赏 | 需要的物品/条件 | 奖励（信用点） |
|---|---|---|
| **Lava-Cooked Goliath Steaks** | 熔岩烤戈利亚牛排 ×3 | 2000 |
| **Goliath Hide Boat** | 熔岩船 | 4000 |
| **Bone Oars** | 骨桨 ×2 | 1600 |
| **Bone Axe** | 骨斧 | 3000 |
| **Bone Armor** | 骨甲 | 2000 |
| **Skull Helmet** | 骷髅头盔 | 1600 |
| **Bone Talismans** | 骨头护符 ×3 | 3000 |
| **Watcher Wreaths** | 守望者花环 ×3 | 3000 |
| **Icewing Wreath** | 冰翼花环 ×1 | 6000 |
| **Bone Daggers** | 骨匕首 ×3 | 2000 |
| **Mushroom Bowl** | 蘑菇碗 | 3000 |
| **Mushroom Caps** | 蘑菇帽 ×3 | 1800 |
| **Mushroom Leaves** | 蘑菇叶 ×3 | 1800 |

### 试剂悬赏（4 种动态）

> 详见下方「试剂悬赏补充」动态试剂表。

### 史莱姆悬赏（8 个）

| 悬赏 | 需要的物品/条件 | 奖励（信用点） |
|---|---|---|
| **Green Slime Extract** | 绿色史莱姆核 |  |
| **Pink Slime Extract** | 粉色史莱姆核 |  |
| **Gold Slime Extract** | 金色史莱姆核 |  |
| **Oil Slime Extract** | 油色史莱姆核 |  |
| **Black Slime Extract** | 黑色史莱姆核 |  |
| **Light Pink Slime Extract** | 浅粉史莱姆核 |  |
| **Adamantine Slime Extract** | 精金史莱姆核 |  |
| **Rainbow Slime Extract** | 彩虹史莱姆核 |  |

### 特殊悬赏（3 个）

| 悬赏 | 需要的物品/条件 | 奖励（信用点） |
|---|---|---|
| **Alien Organs** | 异形大脑、alien、alien_embryo、肝（合成除外）、舌头、眼（合成除外） ×3 | 10000 |
| **Syndicate Documents** | 辛迪加文件、photocopy | 6000 |
| **Adamantine** | 精金板 ×10 | 14000 |


### 病毒悬赏（4 种动态属性）

> **动态生成**：name 随机生成"Virus (属性 of 数值)"——属性 4 选 1，数值 rand(4,11)（33% 概率为负）。奖励 = 2000 + rand(0,4)×200。

| 悬赏 | 需要的物品/条件 | 奖励（信用点） |
|---|---|---|
| **Virus (Resistance of N)** | 上交**抗性值 = N** 的病毒（≥1u） | 2000+rand(0,4)×200 |
| **Virus (Stage Speed of N)** | 上交**阶段速度 = N** 的病毒 | 同上 |
| **Virus (Stealth of N)** | 上交**隐匿值 = N** 的病毒 | 同上 |
| **Virus (Transmission of N)** | 上交**传播值 = N** 的病毒 | 同上 |

> 需要培养定制属性病毒（病毒学玩法联动）。

### 试剂悬赏补充（4 种动态试剂）

> **动态生成**：New() 时从 136 种试剂池随机挑 1 种目标试剂，上贡 ≥ 指定体积。

| 悬赏 | 条件 | 奖励（信用点） |
|---|---|---|
| **Simple Drink** | 上贡随机**简单饮品** ≥10u | 600 |
| **Complex Drink** | 上贡随机**复杂鸡尾酒** ≥10u | 1600 |
| **Simple Chemical** | 上贡随机**简单化学试剂** ≥30u | 1600 |
| **Rare Chemical** | 上贡随机**稀有化学试剂** ≥20u | 2400 |


### NOVA 移除的悬赏（11 个）

> 源码用 `/* NOVA EDIT REMOVAL */` 注释移除——运气型/破坏型/无聊型悬赏。

| 悬赏 | 移除原因 |
|---|---|
| **Strange Object** | 运气型（找神秘遗物） |
| **Scooter** | 无聊型 |
| **Skateboard** | 无聊型 |
| **Soap** | 运气型 |
| **Clown Box** | 运气型 |
| **Cheesie Honkers** | 无聊型 |
| **Dead Mice** | 破坏型 |
| **Raw Corgi Meat** | 破坏型 |
| **Arcade Toys** | 运气型 |
| **Paper Bins** | 无聊型 |
| **Crayons** | 无聊型 |


## 3.6 黑市（Markets）

| 机制 | 值 |
|---|---|
| 配送费 | LTSRBT 40/发射 0/传送 75/补给舱 350 |
| **商品** | 8 大类约 70 种（价格区间/库存/上架率） |
| LTSRBT 机器 | 补货 675 cr 翻倍/上架 30 cr/定价 25-10000/15% 税 |
| **材料市场** | 8 种可交易材料（基准价+库存） |
| 波动 | SSstock_market 60s 跳动/趋势寿命/NOVA 钳制 0.95-1.05×/5 种事件/崩盘 |

## 3.7 出口详表（172 种 · 源码全量）

> 数据源: `code/modules/cargo/exports/`（19 文件）+ NOVA 树（drugs.dm 等）。动态定价条目按公式标注；NOVA 调价已注明。

### 市场材料（materials.dm · market，随股票市场动态定价）（8 个）

| 出口品 | 单价（信用点） | 说明 |
|---|---|---|
| **Bscrystal** | 市场价×0.13 | NOVA 新增/调价；of bluespace crystals；收 bluespace_crystal |
| **Diamond** | 市场价×0.1 | NOVA 新增/调价；cm3 of diamonds |
| **Glass** | 市场价×1 | cm3 of glass；收 glass/ore/shard |
| **Gold** | 市场价×0.5 | NOVA 新增/调价；cm3 of gold |
| **Iron** | 市场价×1 | cm3 of iron；收 iron/rods/ore/coin |
| **Silver** | 市场价×0.5 | NOVA 新增/调价；cm3 of silver |
| **Titanium** | 市场价×0.25 | NOVA 新增/调价；cm3 of titanium |
| **Uranium** | 市场价×0.25 | NOVA 新增/调价；cm3 of uranium |

### 材料（materials.dm）（8 个）

| 出口品 | 单价（信用点） | 说明 |
|---|---|---|
| **Adamantine** | 200 | cm3 of adamantine |
| **Bananium** | 400 | cm3 of bananium |
| **Hot ice** | 160 | cm3 of Hot Ice |
| **Metal hydrogen** | 210 | cm3 of metallic hydrogen |
| **Mythril** | 600 | cm3 of mythril |
| **Plasma** | 20 | NOVA 新增/调价；cm3 of plasma |
| **Plastic** | 10 | cm3 of plastic |
| **Runite** | 240 | cm3 of runite |

### 板材与皮毛（sheets.dm）（23 个）

| 出口品 | 单价（信用点） | 说明 |
|---|---|---|
| **Abductor** | 100 | NOVA 新增/调价；of alien alloy；收 abductor |
| **Ammonia crystals** | 25 | 收 ammonia_crystals |
| **Cable piece** | 0.2 | 收 cable_coil |
| **Cardboard** | 2 | of cardboard；收 cardboard |
| **Carp skin** | 100 | 收 carp |
| **Cat hide** | 150 | 收 cat |
| **Cloth** | 5 | rolls of cloth；收 cloth |
| **Corgi hide** | 200 | 收 corgi |
| **Durathread** | 70 | rolls of durathread；收 durathread |
| **Goliath hide** | 200 | 收 goliath_hide |
| **Gondola hide** | 2000 | 收 gondola |
| **Human** | 100 | of human skin；收 human |
| **License plate** | 25 | 收 filled |
| **Lizard hide** | 150 | 收 lizard |
| **Meat** | 8 | 收 meat |
| **Monkey hide** | 50 | 收 monkey |
| **Pizza** | 12 | 收 pizza |
| **Plasteel** | 20.5 | NOVA 新增/调价；of plasteel；收 plasteel |
| **Plastitanium** | 32.5 | NOVA 新增/调价；of plastitanium；收 plastitanium |
| **Rglass** | 4 | of reinforced glass；收 rglass |
| **Sandstone** | 1 | of sandstone；收 sandstone |
| **Wood plank** | 10 | 收 wood |
| **Alien hide** | 500 | 收 xeno |

### 大型物件（large_objects.dm）（19 个）

| 出口品 | 单价（信用点） | 说明 |
|---|---|---|
| **Security barrier** | 50 | 收 barrier/security |
| **Crate** | 200 | 收 crate |
| **Cardboard box** | 40 | 收 cardboard |
| **Coffin** | 100 | 收 coffin |
| **Wooden crate** | 96 | 收 wooden |
| **Large wooden crate** | 40 | 收 large |
| **Ore box** | 40（继承） | 收 ore_box |
| **Emitter** | 550 | 收 emitter |
| **Field generator** | 550 | 收 generator |
| **Gas Canister** | 10 | 收 canister |
| **Grounding rod** | 240 | 收 grounding_rod |
| **Iv drip** | 50 | 收 iv_drip |
| **Pipe dispenser** | 500 | 收 pipedispenser |
| **Alcohol keg** | 700 | 收 beer/whiskey/rum |
| **Fueltank** | 100（继承） | 收 fueltank |
| **Premium keg** | 1300 | 收 gold |
| **Watertank** | 80 | 收 watertank |
| **Supermatter shard** | 3200 | 收 shard |
| **Tesla coil** | 450 | 收 tesla_coil |

### 工具（tools.dm）（28 个）

| 出口品 | 单价（信用点） | 说明 |
|---|---|---|
| **Analyzer** | 5 | 收 analyzer |
| **T-ray scanner** | 5 | 收 t_scanner |
| **Candle** | 12.25 | 收 candle |
| **Crowbar** | 2 | 收 crowbar |
| **Fire extinguisher** | 15 | 收 extinguisher |
| **Pocket fire extinguisher** | 2 | 收 mini |
| **Flashlight** | 5 | 收 flashlight |
| **Flare** | 2 | 收 flare |
| **Seclite** | 10 | 收 seclite |
| **Hand drill** | 100 | 收 power |
| **Jaws of life** | 100 | 收 power |
| **Radio** | 5 | 收 radio |
| **Rapid construction device** | 100 | 收 rcd |
| **Compressed matter cardridge** | 60 | 收 rcd_ammo |
| **Mini rapid lighting device** | 150 | 收 mini |
| **Rapid pipe dispenser** | 100 | 收 pipe_dispenser |
| **Rapid service fabricator** | 100 | 收 rsf |
| **Screwdriver** | 2 | 收 screwdriver |
| **Soap** | 75 | 收 soap |
| **Artisanal soap** | 30 | 收 homemade |
| **Omega soap** | 2800 | 收 omega |
| **Toolbox** | 4 | 收 toolbox |
| **Welding tool** | 5 | 收 weldingtool |
| **Emergency welding tool** | 2 | 收 mini |
| **Experimental welding tool** | 90 | NOVA 新增/调价；收 electric |
| **Industrial welding tool** | 10 | 收 largetank/hugetank |
| **Wirecutters** | 2 | of wirecutters；收 wirecutters |
| **Wrench** | 2 | 收 wrench |

### 武器（weapons.dm）（16 个）

| 出口品 | 单价（信用点） | 说明 |
|---|---|---|
| **Stun baton** | 100 | 收 security |
| **Cryo pistol** | 300 | 收 cryo |
| **Disabler** | 100 | 收 disabler |
| **Energy gun** | 300 | 收 e_gun |
| **Handheld flash** | 5 | 收 flash |
| **Flashbang grenade** | 5 | 收 flashbang |
| **Handcuffs** | 3 | of handcuffs；收 handcuffs |
| **Inferno pistol** | 300 | 收 inferno |
| **Combat knife** | 100 | 收 combat |
| **Laser gun** | 200 | 收 laser |
| **Assault laser gun** | 200 | NOVA 新增/调价；收 assault |
| **Laser pistol** | 200 | NOVA 新增/调价；收 pistol |
| **Combat shotgun** | 300 | 收 combat |
| **Advanced taser** | 200 | 收 advtaser |
| **Tear gas grenade** | 5 | 收 teargas |
| **WT-550 automatic rifle** | 300 | 收 wt550 |

### 装备（gear.dm）（20 个）

| 出口品 | 单价（信用点） | 说明 |
|---|---|---|
| **Bilton wrangler boots** | 1000 | 收 fancy |
| **Biosuit hood** | 20 | 收 bio_hood |
| **Biosuit** | 40 | 收 bio_suit |
| **Bomb suit hood** | 20 | 收 bomb_hood |
| **Bomb suit** | 40 | 收 bomb_suit |
| **Breath mask** | 2 | 收 breath |
| **Ebony die** | 200 | 收 ebony |
| **Gas mask** | 10 | 收 gas |
| **Space helmet** | 30 | 收 space/eva/nasavoid |
| **Lizard skin boots** | 140 | 收 lizard |
| **Hugs-the-Feet lizard boots** | 400 | 收 masterwork |
| **Radsuit hood** | 20 | 收 radiation |
| **Radsuit** | 40 | 收 radiation |
| **Novelty lighter** | 800 | 收 skull/mime/bright |
| **Riot shield** | 100 | 收 riot |
| **Armor vest** | 100 | 收 vest |
| **Helmet** | 100 | 收 sec |
| **Space suit** | 60 | 收 space/eva/nasavoid |
| **Syndicate space helmet** | 60 | 收 syndicate |
| **Syndicate space suit** | 120 | 收 syndicate |

### 器官（organs.dm）（10 个）

| 出口品 | 单价（信用点） | 说明 |
|---|---|---|
| **Cat ears** | 200 | 收 cat |
| **Cat tail** | 300 | 收 cat |
| **Humanoid ears** | 20 | 收 ears |
| **Humanoid eyes** | 20 | 收 eyes |
| **Humanoid heart** | 40 | 收 heart |
| **Humanoid liver** | 20 | 收 liver |
| **Lizard tail** | 250 | 收 lizard |
| **Humanoid lungs** | 20 | 收 lungs |
| **Humanoid stomach** | 20 | 收 stomach |
| **Humanoid tongue** | 20 | 收 tongue |

### 史莱姆核（xenobio.dm）（8 个）

| 出口品 | 单价（信用点） | 说明 |
|---|---|---|
| **EMP-proof slime core** | 200 | 收 slime |
| **Common slime core** | 24 | 收 metal/orange/purple/blue |
| **Epic slime core** | 88 | 收 black/cerulean/oil/sepia/pyrite/adamantine/lightpink/bluespace |
| **Grey slime core** | 10 | 收 grey |
| **Hypercharged slime core** | 240 | 收 slime_hypercharged |
| **Rainbow slime core** | 200 | 收 rainbow |
| **Rare slime core** | 56 | 收 silver/darkblue/darkpurple/yellow |
| **Uncommon slime core** | 40 | 收 gold/green/red/pink |

### 机甲（mecha.dm）（5 个）

| 出口品 | 单价（信用点） | 说明 |
|---|---|---|
| **Clarke** | 3200 | 收 clarke |
| **Durand** | 4000 | 收 durand |
| **Gygax** | 5600 | 收 gygax |
| **Odysseus** | 2200 | 收 odysseus |
| **APLU MK-II Ripley** | 2600 | 收 mk2 |

### 熔岩神器（lavaland.dm）（3 个）

| 出口品 | 单价（信用点） | 说明 |
|---|---|---|
| **Major** | 8000 | 收 dragons_blood/miner/drake_remains/lava_staff/ghost_sword/prisoncube/rod_of_asclepius/wildhunter/cain_and_abel |
| **Megafauna** | 16000 | 收 hierophant_club/cleaving_saw/colossus/anomalous_crystal/mayhem/soulscythe/storm_staff/hostile_environment/wendigo_blood/wendigo_skull/ice_energy_crystal/resurrection_crystal/ice_trail/demonic |
| **Minor** | 4000 | 收 immortality_talisman/book_of_babel/wisp_lantern/katana/wolf_coat/godeye/memento_mori/wizard/drake/ship_in_a_bottle/banana_shoes/honk/envy/soul/vealrender/berserker/freeze_cube/mining/gauntlets/jacobs_ladder/lifesteal/clockwork_alloy/bdm |

### 种子（seeds.dm）（2 个）

| 出口品 | 单价（信用点） | 说明 |
|---|---|---|
| **New plant species sample** | 动态 | 动态：基础价×（稀有度/最高稀有度）；收 seeds |
| **Improved plant sample** | 动态 | 动态：基础价×（效力提升值） |

### 零件与电路板（parts.dm）（7 个）

| 出口品 | 单价（信用点） | 说明 |
|---|---|---|
| **Advanced data disk** | 80 | 收 advanced |
| **Solar panel control board** | 150 | 收 solar_control |
| **Vending refill canister** | 100 | 收 vending_refill |
| **Solar panel assembly** | 50 | 收 solar_assembly |
| **Data disk** | 40 | 收 computer |
| **Super data disk** | 120 | 收 super |
| **Solar tracker board** | 100 | 收 tracker |

### 文件单据（manifest.dm / stamped_paperwork.dm）（6 个）

| 出口品 | 单价（信用点） | 说明 |
|---|---|---|
| **Approved manifest** | 动态 | 动态：订单价×12%（上限 120）；收 manifest |
| **Erroneously denied manifest** | 动态 | 动态：罚金 -订单价×48%（上限 480）；收 manifest |
| **Erroneously approved manifest** | 动态 | 动态：罚金 -订单价×48%（上限 480）；收 manifest |
| **Correctly denied manifest** | 动态 | 动态：订单价+订单价×24%（上限 240）；收 manifest |
| **Paperwork pile** | 动态 | NOVA 新增/调价；动态：盖章 +800，未盖章 -800；收 paperwork |
| **Messy paperwork pile** | 动态 | 动态：盖章 +200，作废 0，有反噬罚金；收 photocopy |

### 鱼与食物（fish.dm / food_and_drink.dm）（2 个）

| 出口品 | 单价（信用点） | 说明 |
|---|---|---|
| **Fish** | 30 | 收 fish |
| **Serving** | 10 | of food；收 food |

### 古董/异常/悬赏箱（antiques/anomaly/civilain_bounty）（3 个）

| 出口品 | 单价（信用点） | 说明 |
|---|---|---|
| **Antique** | 2000 | 收 antique |
| **Completed bounty cube** | 1 | 收 bounty_cube |
| **Inert anomaly core** | 1600 | 收 inert_anomaly |

### NOVA 毒品（drugs.dm）（4 个）

| 出口品 | 单价（信用点） | 说明 |
|---|---|---|
| **Blastoff ampoule** | 200 | NOVA 新增/调价；收 blastoff_ampoule |
| **Meth crystal** | 200 | NOVA 新增/调价；收 meth_crystal |
| **Moon rock** | 160 | NOVA 新增/调价；收 moon_rock |
| **Saturnx glob** | 200 | NOVA 新增/调价；收 saturnx |


## 3.8 好物系统（Goodies）

**代码**: `cargo/goodies.dm`（457 行）

| 机制 | 说明 |
|---|---|
| **每周好物** | 每周刷新特殊补给（便宜/限量） |
| 订单标记 | ORDER_GOODY |

## 5.3 虚拟域全参数（26+1 个）

| 虚拟域 | 消耗 | 难度 | 奖励 | 强制装 | 完成战利品 | 说明 |
|---|---|---|---|---|---|---|
| **绑架者飞船** | 中2 | 中2 | 中4 | 绑架者装 | 绑架者玩偶 | 绑架者士兵 |
| **灰烬炼狱** | 中2 | 中2 | 中4 | 矿工 | — | 灰烬龙巢穴 |
| **海滩酒吧** | 无0 | 无0 | 低3 | 默认 | 沙滩球 | **喝酒 +0.5 点/杯** |
| **猩红挖掘** | 中2 | 中2 | 中4 | 矿工 | — | 血醉矿工 BOSS |
| **微风湾** | 无0 | 无0 | 低3 | 默认 | — | **钓鱼 +2 点/条** |
| **血染巢穴** | 高3 | 高3 | 高5 | 矿工 | 泡泡糖玩偶 | **泡泡糖 BOSS** |
| **小丑星球** | 低1 | 低1 | 低3 | 小丑 | 自行车喇叭 | 谜题域 |
| **天体试炼** | 高3 | 高3 | 高5 | 矿工 | — | **巨像 BOSS** |
| **披萨派对** | 中2 | 中2 | 中4 | 默认 | 披萨片 | FNAF 梗 |
| **贡多拉小行星** | 无0 | 无0 | 低3 | 默认 | — | 贡多拉可推箱 |
| **草原狩猎** | 无0 | 无0 | 低3 | 默认 | — | **杀鹿 +3.5 点/头** |
| **异端猎杀** | 低1 | 低1 | 低3 | 异端套 | — | **献祭尸体**（指挥+3/安保+2） |
| **狂热者竞技场** | 高3 | 高3 | 高5 | 矿工 | — | **圣职者 BOSS** |
| **岛屿大乱斗** | 高3 | 高3 | 高5 | 战斗装 | 沙滩球×2 | **PvP 大逃杀**（死 1 人 +1 点） |
| **元中央** | 低1 | 低1 | 低3 | 安保 MOD | — | 镇压工人抗议 |
| **管道工厂** | 低1 | 低1 | 低3 | 默认 | 清洁线圈 | 蜂巢机器人 |
| **海盗湾** | 中2 | 中2 | 中4 | 默认 | — | 海盗伪装 |
| **板条箱混乱** | 低1 | 中2 | 高5 | **蒙眼** | — | 纯回声定位 |
| **感染领域** | 中2 | 中2 | 高5 | 蒙眼 | 灵能耳机 | 回声+僵尸群 |
| **冰川攀登** | 低1 | 低1 | 中4 | 冰攀者 | 雪人套装 | 攀岩+等离子湖 |
| **辛迪加突袭** | 中2 | 中2 | 中4 | 默认 | 核弹玩偶 | 辛迪加士兵（NOVA 改名"Cybersun 回收"） |
| **宇宙遗迹** | **极20** | 无0 | **极6** | 默认 | 太空币 c500×4 | 休闲观光域 |
| **冰川吞噬者** | 高3 | 高3 | 高5 | 矿工 | — | **温迪戈 BOSS** |
| **异形巢穴** | 低1 | 低1 | 低3 | 默认 | Rouny 玩偶 | 异形/哨兵/雄蜂 |
| **古代军事模拟**（NOVA） | 高3 | 高3 | 高5 | SolFed 陆战队 | 太空币 c200×3 | **雾战/地雷/炮塔**；CIN 敌人 |

> 奖励注意：beach_bar/gondola/grasslands 未声明 reward → 默认 1（勘误）。

## 5.4 虚拟域机制

| 机制 | 说明 |
|---|---|
| **任务** | 把加密缓存箱搬到目标地块 |
| **点数域** | 攒满 10 点主箱自动 reveal（如海滩酒吧喝酒） |
| **随机域** | 随机选图 +0.2 奖励倍率 |
| **全息梯** | 退出虚拟域 |
| 次要目标 | 收集加密好奇盒 |


# 附录 · 代码路径索引

| 系统 | 文件 | 行数 |
|---|---|---|
| 补给舱 | `cargo/supplypod.dm` | 806 |
| CentCom 投送器 | `cargo/centcom_podlauncher.dm` | 888 |
| 订单控制台 | `cargo/orderconsole.dm` | 554 |
| 好物 | `cargo/goodies.dm` | 457 |
| 材料市场 | `cargo/materials_market.dm` | 386 |
| 出口 | `cargo/exports.dm` | 257 |
| 订单 | `cargo/order.dm` | 238 |
| 快递控制台 | `cargo/expressconsole.dm` | 234 |
| 悬赏 | `cargo/bounty.dm` | 159 |
| 优惠券 | `cargo/coupon.dm` | 133 |
| 万能扫描器 | `cargo/universal_scanner.dm` | 277 |
| **补给包** | `cargo/packs/` | **3,933** |
| **悬赏** | `cargo/bounties/` | **2,006** |
| **市场** | `cargo/markets/` | **1,727** |
| **出口品** | `cargo/exports/` | **1,340** |
| **比特跑者** | `bitrunning/`（8 目录） | **5,934** |
