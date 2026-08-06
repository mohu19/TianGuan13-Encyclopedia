# TianGuan13 Nova 特色模块完全百科 (Nova Modules Complete Encyclopedia)

> 基于 TianGuan13 NovaSector 分支源码全量整理，覆盖 `modular_nova/modules/` 全部特色模块。
> 本文档由原"上卷（9 大模块）+ 下卷（26 模块）"合并而成，共 **35 个模块**全录，无省略。

## 目录 (Table of Contents)

- [第一部 · 大型模块（1-9）](#第一部--大型模块1-9)
  - [1. 合成人 Synths](#1-合成人-synths)
  - [2. 鸟形族 Teshari](#2-鸟形族-teshari)
  - [3. 暗影猫 Shadekin](#3-暗影猫-shadekin)
  - [4. 鸟人族 Vox / Vox Primalis](#4-鸟人族-vox--vox-primalis)
  - [5. 蜘蛛族 Giant Spiders](#5-蜘蛛族-giant-spiders)
  - [6. 异形蜂巢 Xenomorph](#6-异形蜂巢-xenomorph)
  - [7. 外星考古 Xenoarchaeology](#7-外星考古-xenoarchaeology)
  - [8. 外星文物 Xenoarch Artifacts](#8-外星文物-xenoarch-artifacts)
  - [9. 赛博空间潜入 Bitrunning](#9-赛博空间潜入-bitrunning)
- [第二部 · 中小型模块（10-35）](#第二部--中小型模块10-35)
  - [10. 蓝空间采矿机 Bluespace Miner](#10-蓝空间采矿机-bluespace-miner)
  - [11. 军火配装站 Armaments](#11-军火配装站-armaments)
  - [12. 细胞枪 Cell Guns](#12-细胞枪-cell-guns)
  - [13. Tarkon 公司](#13-tarkon-公司)
  - [14. 地下交易 Underworld Connections](#14-地下交易-underworld-connections)
  - [15. 高级飞船 Advanced Shuttles](#15-高级飞船-advanced-shuttles)
  - [16. 蓝图武器 BSA Overhaul](#16-蓝图武器-bsa-overhaul)
  - [17. 蓝盾军官 Blueshield](#17-蓝盾军官-blueshield)
  - [18. 舰桥助理 Bridge Assistant](#18-舰桥助理-bridge-assistant)
  - [19. 通信专家 Telecomms Specialist](#19-通信专家-telecomms-specialist)
  - [20. 咬人特质 Bitey Quirk](#20-咬人特质-bitey-quirk)
  - [21. 机器人增强 Borg Buffs](#21-机器人增强-borg-buffs)
  - [22. 太空藤蔓 Space Vines](#22-太空藤蔓-space-vines)
  - [23. 酿酒 Alcohol Processing](#23-酿酒-alcohol-processing)
  - [24. 自我实现装置 Self Actualization Device](#24-自我实现装置-self-actualization-device)
  - [25. 兵棋投影 Wargame Projectors](#25-兵棋投影-wargame-projectors)
  - [26. 部落扩展 Tribal Extended](#26-部落扩展-tribal-extended)
  - [27. 经济 Economy](#27-经济-economy)
  - [28. 站台特质 Station Traits](#28-站台特质-station-traits)
  - [29. 打卡 Time Clock](#29-打卡-time-clock)
  - [30. 货运物品 Cargo Items](#30-货运物品-cargo-items)
  - [31. 物种合成器 Species Synthesizer](#31-物种合成器-species-synthesizer)
  - [32. SEVA 防护服](#32-seva-防护服)
  - [33. 幽灵采矿 Ghost Mining](#33-幽灵采矿-ghost-mining)
  - [34. 殖民地制造器 Colony Fabricator](#34-殖民地制造器-colony-fabricator)
  - [35. 死亡竞赛 Deathmatch](#35-死亡竞赛-deathmatch)

---

# 第一部 · 大型模块（1-9）

## 1. 合成人 Synths
- **英文/路径**：Synthetic Humanoid；`modular_nova/modules/synths/`（3,426 行，圆周生成种族）。
- **核心机制**：机械类人种族，血型为油（`BLOOD_TYPE_OIL`），以「营养值=电量」运行；EMP 是主要弱点；死亡后可用合成人专属手术修复复活；可被 emag 触发强制离子法案胡话；死亡时屏幕 BSOD。
- **关键数值**：耐热惩罚 2 倍伤害、耐冷 1.2 倍；导电系数 1；全身 6 部件均为机械可替换；优先器官阈值：脑与胃 2×标准、心与肺 1.5×、肝/耳/眼 1×。
- **内置特质（TRAIT）**：防辐射、不需呼吸、免疫毒素/缺氧、无基因、稳定心脏、肢体可重接、不会尸变、无濒死状态（自定义处理）、可识字。
- **专属器官全录**（全部可在异能打印机打印，铁/玻璃各半片）：compact positronic brain（紧凑正电子脑，内含虚拟 PDA「persocom」64 容量、3 常驻程序）、合成胃（电化学燃料反应堆）、散热肺（heatsink）、试剂处理器肝（reagent processing unit）、液压泵心脏、听觉麦克风耳、光学传感器眼、合成声带（beeps/boops 语态）。
- **身体部位全录**：安卓头/胸/左右臂/左右腿 + 趾行腿（digitigrade）变体；头部与躯干可涂装，展示"刮花/凹陷/散架"与"烧焦/熏黑/冒烟"损伤文本；机械胸提供全机械肢体时抗高/低压。
- **脑变体全录（10 种外形）**：posibrain（正电子脑，默认）、MMI（紧凑人机接口）、circuit（紧凑 AI 电路）、hyperboard、limaengine（液体引擎）、ai braindisk、neuroboard、condensed（超晶体）、cyberdeck；玩家可打印空闲正电子脑，幽灵可从新弹出的机器人人格复活（ghostrole_on_revive）。
- **子系统强化手术（5 种，技术节点「实验合成人手术」）**：液压冗余回路(血管强化，免心脏)、增强伺服(神经拼接，眩晕与耐力伤害×0.5/0.8)、强化电容(神经接地，电击免疫)、锚点强化(韧带加固，免疫断肢但易受伤)、皮层折叠(脑创伤强化)+皮层烙印(持续治愈基础脑创伤、免疫基础创伤)。
- **合成人专属手术全录（6 类）**：结构修理(焊枪/电缆补损，可升级 3 级+组合式) 、液压泵维护(心)、试剂处理器除垢(肝)、散热器检修(肺)、燃料舱修复(胃)、逻辑核心重置(脑，清除脑洗/神经软件)。对应 3 个科技节点（基础/高级/实验）。
- **专属药物全录（3 种+剂型）**：System Cleaner（解毒）、Liquid Solder（治脑伤）、Nanite Slurry（修复机械肢体，过量自燃）；各带药丸与药瓶版本。
- **充电方式**：内置充电电源线（合成人出生自带左臂植入物，可打印）插 APC/电池充电；另可直接使用机械充电仓。
- **玩家可交互**：屏幕切换（屏幕配件全套：Blank、Blank White、Pink、Green、Red、Blue、Yellow、Shower、Nature、Eight、Goggles、Heart、Mono Eye、Breakout、Purple、Scroll、Console、RGB、Gol Glider、Rainbow、Sunburst、Static、BSOD、Red Text、Sine Wave、Square Wave、ECG Wave、Eyes、Text Drop、Stars，共 27 种）；机身/头部外观（Default、Dark、Human、Moth、Android、Mammal、Lizard、Morpheus Cyberkinetics、Bishop Cyberkinetics 2.0、Hephaestus Industries 2.0、Shellguard Munitions 等品牌底盘）。
- **获取**：圆桌角色创建选合成人（3 选 1 的启动代价与人类相同）；异能打印机可造「Android Frame」空机架 + 上述全部配件。

## 2. 鸟形族 Teshari
- **英文/路径**：Teshari；`modular_nova/modules/teshari/`（393 行）+ 定制化外观支持。
- **核心机制**：小型有袋羽毛类人种族（约 1.1 m），可穿过桌下（TRAIT_PASSTABLE）；体温基准比人类低 30 K，耐寒 0.67 倍、耐热 1.3 倍；肉=鸡肉；全自定义服装图标（含帽子/面具/披肩/护甲/太空服/MOD 服/制服/手套/鞋/眼镜/腰带/背包/耳朵配件，GAGS 灰阶支持）。
- **关键数值**：payday 1.0；体温正常值=310K-30K（约 280K）。
- **器官**：teshari 舌（喜肉食/生食，厌谷物）；teshari 耳。
- **语言**：Schechi（母语，按 F 键用，音节"i ii si aci…"，名称生成规则 chi/chu/ka/ki…）；语言持有者同时通晓通用语+Schechi。
- **外观**：默认外套三色随机化，尾巴（Default/Fluffy/Thin 三型），耳羽（Feathers Upright 等），禁趾行。
- **获取**：角色创建选 Teshari；限定体型。

## 3. 暗影猫 Shadekin
- **英文/路径**：Shadekin；`modular_nova/modules/shadekin/`（190 行）+ `customization/.../species/shadekin.dm`（179 行定义种族）。
- **核心机制**：纯黑暗**具象化**生物；不呼吸、水中呼吸、滑皮；天生夜视；受光会减速+无法回血；暗处自动「黑暗再生」。颜色亮度被钳制在 HSV 35% 以下（必须深色）。
- **关键数值**：暗处每秒回 0.5 瘀伤+0.5 烧伤（仅有机肢体）；亮处移速+25% 减速（light_averse）；暗处移速+20%、动作速度+25%（dark_affinity + hands_of_darkness）。
- **专属器官全录（4 件）**：shadekin 脑（光检测+再生状态机）、shadekin 眼（巨大、闪光敏感、夜视、色彩偏移 20/10/40）、shadekin 舌（共情语言 Marish/Empathy 传送，全服务器暗影猫耳接收；心情越好传得越快越好）、shadekin 耳（灵敏听觉法术+伤害倍率 1.5）。
- **情绪对话机制**：共情信息颜色与延迟随精神卫生变化（GREAT 即时绿/NEUTRAL 1s/UNSTABLE 4s/CRAZY 5s 星号/INSANE 6s 乱码）；中途移动会 ROT13 打码。
- **特色**：随机 4 套深色配色（黑灰/暗紫红/暗黄/暗紫蓝）；尾巴 "Shade"、"Shade Ears" 可选；可趾行。
- **获取**：角色创建选 Shadekin。

## 4. 鸟人族 Vox / Vox Primalis
- **路径**：`voxes/`（50 行器官图标）+ `customization/.../species/vox.dm`（Vox 种族定义）+ `better_vox/`（339 行，Primalis 重做）+ `alt_vox/`（1,288 行，AI 语音库）。
- **Vox（经典鸟人）**：氮呼吸（需氮气面罩/装备）、耐冷-30K、畏热-15K；吸氧受损；趾行；默认部件 Vox 尾巴/嘴/Vox Bands 背刺；随机颜色 #77DD88 系；花纹固定池 5 款（Vox、Vox Hive、Vox Nightling、Vox Heart、Vox Tiger）；皮质堆叠脑（cortical stack，科幻永生设定）。
- **Vox Primalis（完整重做外形）**：专属头（72 颗牙）/胸/臂/腿，独立服装图标，**5 种体色**（default、darkteal、yellow、albino、brown）；尾巴 5 型（Vox Primalis Tail + Albino/Brown/Darkteal/Yellow）；发型 14 款（Shortquills、Razor、Kingly、Punk、Bayonet、Rome、Kingly Long、Whip、Long、Classic、Dreads、Long Dreads、Kingly Dreads）；**7 种社会职阶花纹**（Leader 首领、Drone 工蜂、Reaver 收割者、Raider 掠夺者、Larva 幼虫、Scavenger 拾荒者、Servirtor 侍从）；嘴喙 1 款。
- **Vox 器官全录（voxes 模块）**：心/眼/耳/肝/胃/氮肺/脑/舌全部专属图标。
- **AI 语音（alt_vox）**：AI 公告 VOX 语音 4 套——标准/VOX_HL（半条命 630 词）/VOX_BMS（黑山 236 词）/VOX_MIL（军语 291 词），可切换；配音词串可显示/清除。
- **获取**：角色创建选 Vox 或 Vox Primalis。

## 5. 蜘蛛族 Giant Spiders
- **英文/路径**：Nova 巨型蜘蛛；`modular_nova/modules/spider/`（1,178 行）。
- **蜘蛛种类全录（7+1 种）**：
  1. **Webslinger 织网恐蛛**（175 HP，咬 25-30，毒=猎蛛毒素每次 1.5，物伤 45，半透明 alpha30，可潜行、抛蛛网束缚、钩爪拉人、医网治同类）；
  2. **Voltaic 沃尔特蛛**（伏击者，175 HP，咬 8，注递 teslium 2，喷酸+爬墙）；
  3. **Pit 坑恐蛛**（250 HP，破甲 25，物伤 60，撕墙+拆肢 50，冲锋，25 伤口加成；速度-4 玩家修正）；
  4. **Ogre 食人魔蛛**（600 HP 坦克，咬 5，毒=蝮蛛毒 1.5，5 秒自愈 50/50，铺全部网型：图腾/实心网/粘网/通道/尖刺网/密封网/反射网）；
  5. **Carrier 携带者蛛**（225 HP，咬 10-15，注 spidereggs 试剂，猎物体内长 spiderling）；
  6. **Baron 男爵蛛**（2000 HP，破甲 50，咬 20-40，毒 5，物伤 200，伤口加成 30/60，震地+跳跃+凝视+超级网钩，3 分钟图腾）；
  7. **Badnana 恶香蕉蛛**（40 HP 搞笑单位，咬注笑气，致死大笑）；
  8. **Spiderling**（由卵生出的幼蛛）。
- **通用能力全录**：爬天花板（Climb 隐身 alpha135）、Web Snare 粘性束缚（15s cd，8 格射程）、Webhook 钩蛛网（3 分钟 cd，Baron 版 1 分钟）、蜘蛛图腾（铺活体网状根系，6-10s 扩张至 3 格，摧毁时音爆+部分人得「蜘蛛幻影」脑创伤）、abdomen pound 腹部猛击（10s cd，震飞 7 格+麻痹 5s，连续使用有递增后摇）、《Stalking Phantom Spider》幻影创伤（仅本人可见的蜘蛛追猎，近身随机掉血 20-50）。
- **感染机制（spideregg_organ）**：被携带者咬后体内埋 spider egg 器官，每 30-60s 产出 1 只 spiderling，共 10 次；可用手术取出。
- **AI 全录**：webslinger/voltaic/pit/ogre/carrier/baron/badnana 七套专属 AI 控制器，含打监控、逃跑、瞄准束缚等行为。
- **获取**：地图上天花板自然生成；Baron 多为管理员/事件单位；金核不可繁殖（NO_SPAWN）。

## 6. 异形蜂巢 Xenomorph
- **路径**：`modular_nova/modules/xenomorph/`（覆盖层：alien 语言持有者=异形通用语+通用语；queen 速度=1）；本体在上游 `code/modules/mob/living/carbon/alien/`。
- **亚种/阶层全录**：**Drone 工蜂**（125 HP，可进化 500 血浆→Praetorian）、**Hunter 猎人**（125 HP，速度 -0.3，疾跑）、**Sentinel 哨兵**（150 HP，速度 0.2，喷酸）、**Praetorian 禁卫**（250 HP，速度 0.5，500 血浆进化 →Queen）、**Queen 女王**（Nova 覆盖：alien_speed=1）；另有 Larva 幼虫、Alien Embryo 寄生胚胎、Facehugger 抱脸虫。
- **核心机制**：血浆（plasma）资源系统、进化树（Drone→Praetorian→Queen）、树脂建造、宿主感染（胚胎→破胸）。语言=异形通用语。

## 7. 外星考古 Xenoarchaeology
- **英文/路径**：Xenoarch；`modular_nova/modules/xenoarch/`（2,229 行；含 10 种外星球植物）。
- **玩法闭环**：挖矿层找「奇异岩」→工具测量/扫描→锤子挖至物品深度→刷子小心揭开→获得文物/破损物→研究机合成。
- **工具全录（17 件）**：测量卷尺（4s 标注当前深度）、手持扫描器(普通 2s/高级 1s 显示最大/安全/真实深度)、锤子 8 档（1/2/3/4/5/6/10cm，每 cm 0.5s）、高级锤（可调深度 1-30）、普通刷（3s）/高级刷（0.5s）、核心取样器、天体稳定器（变巨石稳定为可带走）、粒子电池/利用器、手脚雷达（寻考古点）、手持雷达（引导方向箭头 HUD）、考古工具腰带+满配版、考古袋（25 格自动拾取）/高级袋（50 格）、考古衣柜（含灰烬行者版）。
- **机器全录（4 台）**：Xenoarch Researcher 研究机（吞奇异岩/破损物→研究点，点可兑换：熔岩宝箱 100 点+骷髅钥匙 / 反常水晶 100 点 / Bepis 科技磁盘 60 点）、Xenoarch Scanner 扫描机（批量标记深度）、Xenoarch Digger 挖掘机（直接揭出文物）、Radiocarbon Spectrometer 放射性碳谱仪。
- **奇异岩规则**：三档奖励（1-50 分=tier1，51-87=tier2，88-100=tier3）；深度=21~(22×档位) cm；挖过头=毁掉；考古技能影响速度/精度/额外产出；在熔岩/雪地额外掉 1 块。
- **奖励全录**：tier1=古银币+古金币+种子/动物沉积物+普通技术碎片；tier2=古金币+古精金币+技术碎片+武器/服装/违禁品/外星沉积物；tier3=古精金币+古秘银币+高级武器/违禁/外星/服装碎片；特殊：蓝晒细胞、豪华生存舱、夜视医疗眼镜、V8 引擎、医疗光束枪、**旧穿梭机建造套件**（完整电路板+蓝图）、死亡人帽檐（100% 挡头击后弹飞）、褪色希望徽章（+4 心情）、褪色圆盾（100% 格挡 25 耐久）、**冷迅猛龙蛋**（走 300 步孵化幼迅猛龙）、泥封医疗机器人（双倍治疗）、古代金币面值（银=1.25 货箱、金=2.5、精金=5、秘银=12.5 货箱）。
- **外星植物全录（10 种，均为 5 产量）**：Amauri（毒球茎）、Gelthi（蜂蜜荚果→糖汁）、Jurlmah（冷冻疗法水果）、Nofruit（沉默表演者立方果）、Shand（实验室油质叶）、Surik（火山纹水晶，含 Indecipheres 精华）、Telriis（草→可榨奶）、Thaadra（药+银花）、Vale（易燃叶，曾自燃下架）、Vaporsac（悬浮气囊化药剂）。
- **获取**：岩浆/雪地/红岩/小行星矿层随机生成奇异岩矿脉；考古技能成长（每块 25 经验）。

## 8. 外星文物 Xenoarch Artifacts
- **英文/路径**：Xenoarch Artifacts；`modular_nova/modules/xenoarchartifacts/`（5,014 行）。
- **巨石 Boulder 流程**：挖掘后残余巨石（目标深度 25-200cm）→扫描/测量/稳定→刷子揭开。未稳定则 50% 概率掉落损坏/消失文物。
- **文物机器全录（5 类）**：
  1. **Alien Artifact 通用外星机器**（1000 耐久；随机外观 12 种：魔法师大/小、火星人大/小/粉、立方体、柱、电脑、通风口、浮空金属、大水晶-绿/紫/蓝；随机 1-2 个效应；**13 种触发**：接触/水/酸/易挥发/毒素/强力/能量/热/冷/等离子/氧/CO2/氮/靠近探测）；
  2. **Bluespace Crystal 蓝晒晶体**（150-300 耐久；破坏或受损时生成随机异常：通量/高重力/ectoplasm/bioscrambler/高温/pyro/黑洞/蓝晒，蓝晒权重 6 倍；寿命 1800s+传送）；
  3. **Power Crystal 巨型能量水晶**（接入电网发电，激活时图标变亮，可用武器充电/放电）；
  4. **Auto Cloner 神秘荚舱**（1200-3600s 生成一只生物，33% 是危险生物；可被 DNA 档案解锁友善生物）；
  5. **Alien Replicator 外星复制机**（投入任意物品作原料，点按钮造出 39 类随机产物，含违禁品乃至军械库级；产物以原料命名/描述）。
- **文物效应全录（30 种）**：
  - **主效应 15**：温度(冷/热)、DNA 切换、EMP、气体生成、重力(拉/斥/混乱)、辐射、睡眠、眩晕、特斯拉电弧、传送、身体交换、身体恐怖(肢体突变)、机械暴乱(嘲讽+黑入合成人/机器人)、血再造、血抽取；
  - **副效应 13**：坏情绪/好情绪、充能、放电、治疗、伤害(含机器人版 roboheal/robohurt)、发光/黑暗、噪音、恶心、致幻剂；
  - 另含**特斯拉**（充电脉冲）、**电网**（powernet，供能）。
- **处理机全录（4 台）**：Anomaly Analyser 异常分析仪（+扫描台；打印扫描报告）、Exotic Particle Harvester 收割机（750W、抽取文物能量入粒子电池）、Artifact Scanpad 扫描垫、Radiocarbon Spectrometer。
- **手持设备全录（4 件）**：粒子电池(最大 100 电荷)、粒子利用器(发射复制文物效应，含定时模式)、Alden-Saraspova 猎奇波扫描背包(外波搜索器，需插入处理器)、手持异常稳定器(8 种场型)。
- **文物外骨骼全录（2 台）**：**Phantom 幻影 Reticence 机甲**（静音近隐形、配静音 S.H.H. Quietus 卡宾 + 灵能尖叫炮「Psionic Temporary Scream Desensetizer 2500」）、**Medivac 医疗 Odysseus 机甲**（撤伤兵用）；残骸可搜刮，内含濒死远古蓝晒电池。
- **玩家防护**：生物防护衣 85+（胸 0.5/头 0.3）+乳胶手套 0.1+科学护目镜 0.1，合计可达完全免疫异常效应。
- **获取**：挖矿层巨石/遗迹；全流程无需科技解锁（基础科技自带考古）。

## 9. 赛博空间潜入 Bitrunning
- **英文/路径**：Bitrunning；`modular_nova/modules/bitrunning/`（2,290 行）。主供应百科已有完整玩法章，此处仅**概述+补充细节**。
- **概述**：量子服务器开虚拟域，玩家以数字化身潜入 4 类域（古战场模拟、辛迪加袭击、海盗、岛屿乱斗）；3 次连接机会、威胁等级系统、可采购装备盘升级。
- **补充细节全录**：
  - **量子服务器**：幽灵留言、防刷屏 30s 队列、扫描模块决定锚点上限（T1=1…T3=3）；
  - **域锚**：一次性平板，为服务器+1 出生点/+1 威胁（熔成半透明“锚定安全连接”10s 后隐形）；
  - **装备盘**：能力盘（等级 0 戏法/1 圣光+疗愈/2 火球+闪电+冲刺+闪烁+鲜血打击+数据轻敲+召唤物品+充能/3 石魔+幽魂变身+死亡循环+中二咒语）与物品盘（等级 0 杂物/休闲/1 救援信标+抢修+军械/2 手枪+步枪+黑客植入/3 领域锚+反掉落植入+激光枪+纳米手枪皮套+激光迷你炮）；法术前缀全部「反魔法豁免化」；「数据轻敲」重置所有法术 CD 但降最大生命；「腐化位置」随机闪烁；
  - **休闲盘**：投送自动售货机（AutoDrobe/Good Clean Fun/ClothesMate/宿舍机，域内全免费）；
  - **外包工程师**：Subcontracted Bitrunner 中继接单（单次使用信标，幽灵加入域，目标：协助完成域+搞事）；
  - **域生物（Nova 新增）**：圣光石魔（300 HP，40 物伤，撕墙）、圣光幽魂（150 HP，潜行+冲刺+魔法弹）、模拟战士兵（CIN 近战 15-20/远程 5-10+霰弹枪变体）、竞赛西瓜（50 HP，5-9 撞击）、迷彩拟态箱、棕榈树；
  - **古战场域设施**：奖励房卡/出口隧道卡、N-URSEI 自动医疗站（液体治疗）、战雾、地雷、炮塔、弹药/材料 MOD 模块（电缆、隐形地雷、飞刀、定制注射器、全愈针、EMP 手雷、机枪箱、步枪弹匣）；
  - **通讯**：bitrunning 加密钥匙（补给/科学/派系三频道）、专属头戴装备、AI 派系公告密钥；
  - **目标保护**：加密保险箱/加密锁箱不可被「域内敌方化身」（域幽灵/故障角色）拖动/推/摸/使用/投掷。
  - **获取**：货运营地（quartermaster 零售目录，价格 250-500 信用点不等，各等级盘上述）。

---

---

---

# 第二部 · 中小型模块（10-35）

## 10. 蓝空间采矿机 Bluespace Miner

**源码**: `modular_nova/modules/bluespace_miner/`（252 行）

- **机器本体 + 电路板 + 货运包**
- **权重出矿表**：铁 20 / 玻璃 20 / 等离子 14 / 银 8 / 钛 8 / 铀 3 / 金 3 / 钻石 1
- **环境约束**：温度 ≥20℃ 停机、气压 ≤1atm 或 ≥1.5atm 停机、相邻 1 格互斥
- **升级**：微激光降排气温度（100→80K）/ 伺服降周期（6s→4s）
- **聚焦产物 ×3**
- **emag 后**：加入 bananium 权重 1 并排放氚
- 电路板需精炼蓝水晶；货运 10000 金；空闲功耗 300W

## 11. 军火配装站 Armaments

**源码**: `modular_nova/modules/armaments/`（484 行）

- `SSarmaments` 子系统 + `datum/armament_entry`（cost/分类限额/弹匣购买/限制购入）
- 点卡 `armament_points_card`（点数卡，默认 10 点，可互相转账）
- 配装站（默认需 ACCESS_SYNDICATE）
- **本仓库中是纯基础设施，无具体商品条目**

## 12. 细胞枪 Cell Guns

**源码**: `modular_nova/modules/cellguns/`（1,379 行）

- `cell_loaded` 电池装填枪基类（最多 3 弹巢、`weaponcell` 插入即加弹药、alt 点出）
- **医疗枪 CWM-479 系列**：标准 / 升级 4 巢 / CMO 5 巢自充
- **`weaponcell/medical` 全部 18 种医疗电池**：I/II/III 级 ×（brute/burn/toxin/oxygen）+ 8 种功能电池（clotting、temperature、hardlight gown、salve、roller bed、body teleporter、relocation 标准/升级）
- **弹药治疗数值**：I 级 brute 7.5/氧 10/毒素 5；II 级 11.25/20/7.5 阈值 30；III 级 15/30/10 阈值 40；治疗伴随 disgust+营养消耗；毒素治疗受非药品化学物削减
- **升级套件**：uranium×2 / glass×2 / plasma / diamond + 全套 12 个研发设计

## 13. Tarkon 公司

**源码**: `modular_nova/modules/tarkon/`（1,474 行，残骸站模块）

- **M6 PDW**（两连发，c35sol 弹匣）
- **A.R.C.S 共振器**（自动/手动双模式、共振链接状态效果 15 伤害、低气压 ×3 倍、5 个场限制）
- **攀达服/背包四件套**（防火）
- **6 套制服**（货/科/卫/医/工/指挥/平民，护甲 melee10 fire50 acid50 wound10）+ 焊工帽 + 护臂可换肤
- **大气控制计算机 8 套**（O2/N2/N2O/CO2/等离子/混合舱/焚烧炉全套传感器+气闸）
- 外星生命体摄像台 + 安保摄像网络；SMES×2（备份/驱动）；Tarkon 权限玻璃门
- **13 种 ID 卡/trims**（甲板工/货运/警卫/创伤医/维护/研究员/机器人/少尉/总监）
- 3 张尘土录音带 + 12 张纸条 + 蓝图册；耳机
- **全套科技树**（TECHWEB_NODE_TARKON 5 级隐藏节点 + 防御节点，含 MOD Tarkon 装甲板、ARCS、Tarkon RCD、BSC 炼化箱设计，R&D 服务器/原型车床）
- **鬼魂角色**（甲板工/货运/科研/医/工/警/少尉/总监 8 岗 + 尸体制服）
- **异形巢 3 种强度**：warren 500 血 4 怪 30s / 巢 300 血 2 怪 40s / 隧道 150 血 1 怪 40s；巢破刷 Boss（queen/large 等）+ 战利品
- **Turret 套件**：Cerberus（3 弹匣槽）/ Hoplite（2 弹匣槽）+ 组装件设计

## 14. 地下交易 Underworld Connections

**源码**: `modular_nova/modules/underworld_connections/`（371 行）

- 0 值特质，开局定制**黑市 uplink（10 种皮肤**：砖头手机/默认/耳机/对讲机/PDA/采矿对讲机/红话机/红对讲机/辛迪加手机/硅基平板 + 自定义名/描述）
- 获得 Exploitables 访问；HUD 标记嫌疑且禁止武器执照
- **黑市价格调平覆盖 37 个条目**（分服装/消耗品/杂项/工具/武器 5 类，如 switchblade 4.25-8×工资、Voskhod 改装套件 4-5.75×货值、chainsaw 2-4× 且 75% 概率）

## 15. 高级飞船 Advanced Shuttles

**源码**: `modular_nova/modules/advanced_shuttles/`（716 行）

- **NTV Relay 到达飞船逻辑**（9s 呼叫/5s 点火/9s 再充、有活人自动取消返程、`arrivals_wait` 配置）
- 到达控制台 + 召回台
- **9 个地图模板**：NAV Monarch 渡轮、Nova/Delta/Ouroboros 货船、SFS Christian 白舰、JN Chasse-Galerie 咖啡白舰、NTV Relay、标准紧急船 60 人限、NMC Drudge 劳改船、NMC Chimera/Phoenix/Manticore 矿船
- 多套飞船墙/地板（pod/ferry/evac/arrivals/cargo/mining 各系）；探索地板 19 种 + 12 个舱内贴花；7 种壁挂储物柜 + 3 个壁柜框架；G250 标志 + 力场

## 16. 蓝图武器 BSA Overhaul

**源码**: `modular_nova/modules/bsa_overhaul/`（576 行）

- **三级分段组装**（发生器/炮膛/熔聚器，多工器链接、对齐检查）
- 完工后消耗 1MW 目标充能（上限 1 亿、单周期吸 1MW）、**预发射警报 20 秒** → 光束贯穿全图（可被拦截）
- 爆炸强度 = 电容/1e7（最大 10/20/40）；屏幕震动 0.25-0.75；装填后冷却
- 控制台（GPS 或区域瞄准、emag 自瞄）、站台目标、管理员开关 BSA 指令

## 17. 蓝盾军官 Blueshield

**源码**: `modular_nova/modules/blueshield/`（373 行）

- **单哨位职业**（7 天年龄、2400min 经验、命令部门）
- 全套装备（制服/防弹背心可换肤 3 种/贝雷帽×2/软帽/冬大衣+兜帽/背包×3/耳麦/加密钥匙/塑料人套服）
- **专属武器信标 5 选 1**（能量盾/转轮枪组/定制地狱火卡宾/冲锋枪组/霰弹枪组）
- 定制地狱火弹药（20 枪标准电池）
- 专属手持人员监控（仅显示指挥+中央职务）；衣柜 22 件；哨位点

## 18. 舰桥助理 Bridge Assistant

**源码**: `modular_nova/modules/bridge_assistant/`（270 行）

- **永久岗位**（原先的站台特质被禁用）
- 配绿西装/蝴蝶结/机械手杖/迷你能量枪/咖啡托盘（4 杯咖啡）
- 咖啡信标（Piccionaia Impressa Modello 5 全套含 NT 版马克杯）
- 储物柜含 RCD+2 升级

## 19. 通信专家 Telecomms Specialist

**源码**: `modular_nova/modules/telecomms_specialist/`（120 行）

- **完整岗位**（60min 经验、工程部、`Diviner` RPG 称号）
- 全套服饰 + 3 预装程序 PDA
- 完整的权限表（含 ACCESS_NETWORK/ACCESS_TCOMMS）与邮件礼包

## 20. 咬人特质 Bitey Quirk

**源码**: `modular_nova/modules/bitey_quirk/`（261 行）

- 0 值特质，开关"兽性"动作（随机 7 个名字）
- 加伤 +4/+7、效果 +10、钝击 +0.5、锐利
- 猫舌共存自动移除逻辑；普攻 10% 随机咬；与猫舌 go_feral 互斥

## 21. 机器人增强 Borg Buffs

**源码**: `modular_nova/modules/borg_buffs/`（365 行）

- **4 种机器人调酒器**（果汁 19 种/酒精 26 种/苏打 9 种/杂项 22 种试剂）
- 自动烹饪工具（刀↔擀面杖切换，速度 0.5）
- 扫地机器人钢丝刷升级（MECHFAB）+ 矿工机器人动能破碎机（力 20）
- **零食分配器**（培根饼干/布饼干/糖曲奇/机器人棒棒糖 4 种，发射/递送双模式，200 电截止，5% 电池/个）

## 22. 太空藤蔓 Space Vines

**源码**: `modular_nova/modules/space_vines/`（361 行）

- **镰刀 T1-T4**（力 13/15/18/22、横扫范围 0-3 格）+ 4 级科技节点 + 2 货运箱
- **困难版藤蔓事件**（≥40 人、权重 8、可生成捕人藤，3 变异：开花/发光/荆棘或耐寒）
- **5 个新藤蔓变异**：软组织修复（跨越 1 点/进食 5 点）、驯化、破洞修补生成藤地板、碳循环 CO2→O2
- venus_human_trap 禁止拖入太空；藤蔓 plantbgone 抗性变量

## 23. 酿酒 Alcohol Processing

**源码**: `modular_nova/modules/alcohol_processing/`（53 行）

- 乙醇代谢降为 0.3×
- **8 档 BAC 心情文本**（0.01→0.23+）

## 24. 自我实现装置 Self Actualization Device

**源码**: `modular_nova/modules/self_actualization_device/`（337 行）

- **Vey-Medical 机器**，电路板只要求 1 个微激光
- 处理 70s - 激光等级×10s；功率 7.2MW/处理时间
- 需玩家同意（否则可 5s 挣脱、断电则头部 15-30 烧伤×部位）
- 完成后按角色预设重写 DNA/外观，支持 AI 脑（需改造检查）
- 6 条播报广告

## 25. 兵棋投影 Wargame Projectors

**源码**: `modular_nova/modules/wargame_projectors/`（757 行）

- **3 款投影仪**（单位蓝色/红色、地形灰色，各限 30 个投影、10 色可选）
- **19 种全息标记**：突击艇/突击艇编队/小型/中型/大型/备用大型舰、未识别、导弹、探测器、空间站、平台、尘埃云、小/大/群小行星、行星
- DIY 兵棋套装
- **两本完整规则书**（"Blue Lizard"规则 + 4 阵营 13 种示例舰船 + 3 个剧本，完整 D20 伤害表）

## 26. 部落扩展 Tribal Extended

**源码**: `modular_nova/modules/tribal_extended/`（718 行）

- **骨弓**（力 20、火/熔岩抗性、4 骨材料）/ **管弓**（力 10）
- **箭矢**：骨箭（35 伤、20% 穿甲、33% 嵌入）/ 灰烬箭（15 伤+对矿怪 60 加成）/ 青铜箭（30 伤+对巨兽 90 加成）
- 戈利亚盾 200 耐久
- **骨剑**（力 20、10% 穿甲）
- **符文巨剑/巨斧/长矛**（动能破碎者变体、充能转圈 AOE 标记 1 格）
- **吹号角**（5 种节奏、170 格传播、5.5s cd）/ **战号角**（全图通知、11.5s cd）
- 16 个新手工艺配方 + 木头碗/骨骰/丝线 + 星德拉克尔飞船碎片（凿子激活、30s、部落专属 → 符文武配方）

## 27. 经济 Economy

**源码**: `modular_nova/modules/economy/`（49 行）

- 银河材料市场不再收购矿物（仅出售）
- 重加 **5 种材料货箱**（玻璃 50/铁 50/钢 20/钢 50/石砖 50）

## 28. 站台特质 Station Traits

**源码**: `modular_nova/modules/station_traits/`（39 行）

- 溢出岗位官僚化修改（跳过 nova_stars_only）
- 辐射风暴禁用、赛博革命禁用、skub 禁用
- 生日权重 3、生日退出偏好；桥助特质禁用

## 29. 打卡 Time Clock

**源码**: `modular_nova/modules/time_clock/`（57 行）

- `off_duty_timer` 组件（ID 冷却打卡/存原职务/需要 HoP 解锁）

## 30. 货运物品 Cargo Items

**源码**: `modular_nova/modules/cargo_items/`（459 行）

- 杂项货运科技节点（T2）+ 3 设计
- **货运传送器**（3 标记、8s 冷却、最多 20 物、荧光黄标记）
- **传送带分拣器**（普通 4 分拣/4 方向+5 物品 → 改进 8/10/8 方向蓝光）
- **礼品盒夹持器**（8 盒、销毁通告 AAS 配置）

## 31. 物种合成器 Species Synthesizer

**源码**: `modular_nova/modules/species_synthesizer/`（48 行）

- **Sing Tones 唱歌动作**：用内置合成器唱歌（`/datum/action/sing_tones`）
- 可用乐器 5 种：spaceman / meowsynth / square / sine / saw
- **emag 后追加 2 种**：bikehorn / honk（并强制切到 honk）
- 歌曲容量 15（`new(grant_to, allowed_instrument_ids, 15)`）
- 失去物种时自动删除（COMSIG_SPECIES_LOSS）；以太体可用

## 32. SEVA 防护服

**源码**: `modular_nova/modules/SEVA_suit/`（95 行）

- **SEVA 服 + 头罩**（潜行者风格，防火，不能升级戈利亚板）
- **护甲**：melee 20 / bullet 10 / laser 10 / energy 10 / bomb 30 / bio 50 / **fire 100** / acid 50 / wound 10
- 覆盖 CHEST|GROIN|LEGS|ARMS；耐热 `ARMOR_MAX_TEMP_PROTECT`、耐冷 `FIRE_SUIT_MIN_TEMP_PROTECT`；FIRE_PROOF
- 允许放矿工装备（`GLOB.mining_suit_allowed`）；teshari/趾行变体图标
- **SEVA 套件券**（suit_voucher）：矿工装备售货机兑换，含 PKA/生存刀/seclite/探索服/meson/自动采矿扫描仪/矿袋/防毒面具/矿工无线电钥匙/特殊 ID 卡（基本矿权）
- SEVA 防毒面具（gas mask/seva）

## 33. 幽灵采矿 Ghost Mining

**源码**: `modular_nova/modules/ghost_mining/`（1,168 行）

- **Boulder Collector 巨石收集箱**（BSC Refinery Box）：自动拾取并存储指定方向的矿石/巨石；变体：Suspicious BSC Box / Tarkon BSC Box / NT BSC Refinery Box / Boulder Snatchinator 3000 / compacted 可压缩版（普通/辛迪加/Tarkon）
- **Ghost Vent 幽灵矿脉**（`ore_vent/ghost_mining`）：波次防御采矿点（produce_boulder/start_wave_defense），产巨石+可循环重置；拉瓦兰变体
- **Linked Retrieval Matrix 联动回收矩阵**（400 行）：矿点回收联动系统

## 34. 殖民地制造器 Colony Fabricator

**源码**: `modular_nova/modules/colony_fabricator/`（3,876 行）

- **快速建造制造器 colony_lathe**（`/obj/machinery/rnd/production/colony_lathe`）：殖民地建筑核心，COLONY_FABRICATOR buildtype，可平板打包（repackable 5s），有专属 techweb（`/datum/techweb/colony_fabricator`）
- **设计图集**：appliances 276 行 / construction 124 / flatpack_machines 326 / tools 79 + 5 个 fabricator_flag_additions 补丁（computer_board/construction/equipment/machine_boards/stock_parts/tools）+ rations_printer 设计（ingredients/reagents/seeds/snacks/utensils）
- **机器全录**：arc_furnace 电弧炉（224 行）、ore_silo 矿筒、power_storage_unit 电池组（smes/battery_pack + large/precharged 变体）、rtg 放射性同位素发电机（42 行）、solar_panels 太阳能板（116 行）、solid_fuel_generator 固体燃料发电机（53 行）、stirling_generator 斯特林发电机（130 行）、thermomachine 热机（64 行）
- **家电全录**：chem_machines 化学机（149 行）、co2_cracker CO2 裂解器（122 行）、foodricator 食物制造机、recycler 回收器（82 行）、space_heater 太空加热器（118 行）、wall_cell_charger 壁挂充电器（53 行）、wind_turbine 风力涡轮（89 行）+ 厨房三件套（griddle 煎盘/macrowave 微波炉/range 灶台）
- **建造**：手动门（81 行）、自动门（56 行）、窗户（45 行）、地面 turf（169 行）
- **工具**：omni_drill 万用钻螺丝刀、doorforcer 撬门撬棍
- **货运包**（cargo_packs 49 行）+ 循环音效（44 行）

## 35. 死亡竞赛 Deathmatch

**源码**: `modular_nova/modules/deathmatch/`（129 行）

- **NOVA 死亡竞赛模式**（Nova 死斗，战斗百科已提及，此处为模块明细）
- **2 个地图模板**：Cybersun Training Simulator（4 人，Cybersun Grunt 装载）、Deep Space（货运站攻防：货船船员 vs 辛迪加登舰队 vs Azulean 海盗）
- **装载（loadouts）**：Cybersun Grunt / Azulean Boarder / Cargo Spaceman / Syndicate Spaceman / Spacetider 等
- 修饰器（modifier 19 行）+ 结构（15 行）

---

> **文档完** — 26 个 Nova 中小型模块全录，全部数值从源码提取。自查无"等 N 种"省略。

---

---

> **文档完** — Nova 特色模块 35 个全录（大型 9 + 中小型 26），全部数值从源码提取。自查无"等 N 种"省略。
