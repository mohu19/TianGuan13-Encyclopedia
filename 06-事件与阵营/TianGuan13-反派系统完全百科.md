# 天关 — 反派系统完全百科

> **项目**: TianGuan13 (Nova Sector → /tg/station)
> **代码**: `code/modules/antagonists/`（50 目录 60,645 行）+ `code/modules/uplink/`（3,746 行）
> **范围**: 叛徒/核弹队/异端/巫师/邪教/变形怪/绑架者/菌潮/恶意 AI 等全部反派阵营

---

## 目录

- [第一卷 · 叛徒（Traitor）](#第一卷--叛徒traitor)（Uplink 323 件/承包商契约）
- [第二卷 · 核弹队（Nukeop）](#第二卷--核弹队nukeop)（10 结局/挑战模式）
- [第三卷 · 异端（Heretic）](#第三卷--异端heretic)（8 路径/46 法术/8 升阶）
- [第四卷 · 巫师（Wizard）](#第四卷--巫师wizard)（50+ 法术/大仪式 8 谢幕）
- [第五卷 · 邪教（Cult of Nar'Sie）](#第五卷--邪教cult-of-narsie)（12 符文/血魔法）
- [第六卷 · 变形怪（Changeling）](#第六卷--变形怪changeling)（40+ 能力）
- [第七卷 · 绑架者（Abductor）](#第七卷--绑架者abductor)（15 腺体）
- [第八卷 · 菌潮（Blob）](#第八卷--菌潮blob)（14 菌株）
- [第九卷 · 其他反派](#第九卷--其他反派)（Malf AI/血虫/虚空行者等 30 阵营）
- [附录 · 代码路径索引](#附录--代码路径索引)

---

# 第一卷 · 叛徒（Traitor）

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

## 1.3 Uplink 物品全录（323 件，21 分类）

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

# 第二卷 · 核弹队（Nukeop）

**代码**: `code/modules/antagonists/nukeop/`（16 文件 2,464 行）

## 2.1 核心机制

| 机制 | 值 |
|---|---|
| 团队 | 辛迪加小队（随机队名） |
| 目标 | 核弹摧毁空间站（核心目标） |
| 队长折扣 | 5 团队折扣 + 10 限量折扣 |
| 玩家加成 TC | 每 5 人 +5TC（取整） |
| 渗透者 | 飞船 spawn（infiltrator_basic） |
| 副目标 | 盗取认证磁盘（nuclear disk） |

## 2.2 结局（10 种）

| 结局 | 结果 |
|---|---|
| 核胜 NUKE_WIN | 辛迪加大捷（站毁） |
| 核败 FLUKE | 羞辱性失败（核弹被还回） |
| 无幸存者 | 总歼灭（核爆后未撤离） |
| 炸错目标 | 船员小胜（炸了别的东西） |
| 劫持+磁盘 | 微小胜利（劫船+磁盘） |
| 劫持无磁盘 | 无关紧要胜利 |
| 船员胜+全灭 | 船员大捷 |
| 磁盘丢失 | 中立（磁盘丢但灭敌） |
| 磁盘被夺 | 辛迪加小胜（存活未毁站） |
| 任务中止 | 中立 |

**Darwin 奖**：炸错目标+自己被炸死（彩蛋）。

## 2.3 核弹挑战模式

**代码**: `equipment/nuclear_challenge.dm`（207 行）

| 机制 | 值 |
|---|---|
| 类型 | 高难度挑战（无渗透者/无装备/单核弹） |
| 条件 | 指定玩家数、指定时间限制 |
| 奖励 | 全队 Uplink 大额 TC |

## 2.4 装备

| 装备 | 数值 |
|---|---|
| 精英特工服 | 高级护甲（nuclear_operative_elite） |
| 认证磁盘 | 核弹启动必需（pinpointer 追踪） |
| 瞭望工具 | 指挥视角（overwatch） |

---

# 第三卷 · 异端（Heretic）

**代码**: `code/modules/antagonists/heretic/`（103 文件 18,945 行）+ `code/modules/mob/living/basic/heretic/`（怪物）
**规模**: 全库最大反派系统（8 路径/46 法术/8 刀/11 召唤物）

## 3.1 核心机制

| 机制 | 值 |
|---|---|
| 初始知识点 | **2**（NOVA 从 1 上调） |
| 被动知识 | 每 **20 分钟 +1** |
| 知识获取 | 汲取裂缝 +1（持法典+2）/献祭船员 +2/指挥 +3/邪教徒 +1/知识仪式 +4/猫头鹰盛宴 +5（弃升阶） |
| 光环 | 累计 8 点后绿色漩涡光环（禁刀碎传送） |
| **8 条路径** | 刀/血肉/灰烬/锈蚀/虚空/月亮/锁/宇宙（各自知识树） |
| 知识树 | T1→T2→法袍→T3→刀升级→T4→升阶（链式） |
| 草稿系统 | 4 轮 3 选 1（概率 T1:50/50、T2:50/25/25...） |

**目标**：研究目标（动态）+ 次级献祭 **4 名**船员 + 高级献祭 **1 名**指挥 + 升阶。

## 3.2 献祭系统

| 机制 | 值 |
|---|---|
| 目标 | 5 人：1 指挥 + 1 安保 + 随机（NOVA opt-in 限指挥/安保） |
| 献祭条件 | 目标软死+活心脏 |
| 流程 | 符文上执行→昏睡 12s→传送**曼苏斯暗影界**→2.5 分钟生存小游戏（Helgrasp 诅咒手）→存活送回 |
| 点数 | 普通 +2/指挥 +3/邪教徒 +1+装备 |
| 失败惩罚 | 中断→开膛破肚（**250 BRUTE**） |
| 诅咒器官 | 随机植入 2-4 个腐化器官 |

## 3.3 符文

**转化符文**：绘制 20s（持诅咒刀 14s）→3×3 大符文→选择仪式；限制：1 格内开放地块/3 格内无其他符文。
**宇宙符文**：15s CD 施法，最多 2 个互链符文，站上传送；带星标者强制传送。

## 3.4 异端刀（8 种）

| 刀 | 路径 | 配方 | 上限 | 特效 |
|---|---|---|---|---|
| Rusty 锈蚀刀 | 锈 | 刀+垃圾 | 2 | 攻击+50 恶心 |
| Ashen 灰烬刀 | 灰 | 刀+火柴 | 2 | 攻击点燃 |
| Bloody 血刀 | 血肉 | 刀+血泊 | 3 | 走路留血 66.6% |
| Void 虚空刀 | 虚空 | 刀+零下环境 | 2 | +2 虚空寒意/瞬移追击 |
| Sundered 刀 | 刀 | 刀+银/钛 | 4 | 双持二连击/破拆 2.5 |
| Cosmic 宇宙刀 | 宇宙 | 刀+等离子片 | 2 | 打星标/连锁烧伤+14/+28 |
| Key 钥匙刀 | 锁 | 刀+撬棍 | 2 | 兼撬棍/35% 撕脱伤（升阶 65%） |
| Lunar 月亮刀 | 月亮 | 刀+2玻璃 | 2 | 精神攻击/脑损伤 |

**基础刀**：force 20/AP 35/伤口+5/工具速度 0.375/碎刀传送。
**诅咒刀**：force 25/格挡 35/AP 35/伤口 25（邪教徒献祭异端解锁）。

## 3.5 法术（46 种精选）

| 法术 | 路径 | 冷却 | 效果 |
|---|---|---|---|
| Mansus Grasp 曼苏斯之握 | 全 | 10s | 10 钝伤+路径标记 |
| Furious Steel 狂怒之钢 | 刀T3 | 30s | 3 把环绕刀（25/35 伤） |
| Wolves Among Sheep | 刀T4 | **5 分钟** | 61s 魔法竞技场 |
| Volcano Blast 火山爆发 | 灰T2 | 45s | 20 烧伤+3 火堆 |
| Nightwatcher's Rebirth | 灰T4 | 1 分钟 | 燃烧附近 20 烧伤/回血 |
| Void Prison 虚空囚牢 | 虚空T2 | 1 分钟 | 困球体 10s |
| Void Conduit 虚空导管 | 虚空T4 | 1 分钟 | 15s 脉冲门 |
| Cosmic Expansion | 宇宙T4 | 15s | 5×5 宇宙场+星标 |
| Cloak of Shadow 影之斗篷 | 起始 | 6s | 隐身 3 分钟 |

## 3.6 召唤物（11 种）

| 怪物 | HP | 伤害 | 特效 |
|---|---|---|---|
| 食尸鬼 | 25 | 依尸体 | 玩家操控/上限 3 |
| 无声亡者 | 50 | 依尸体 | 哑巴/上限 2 |
| 破碎复苏者 | 125 | 16 | 骨刃双手 |
| 锈蚀行者 | 100 | 15-20 | 锈地回血 3/s |
| 镜中女仆 | 80 | 12-16 | 镜面游走/被看受伤 |
| 灰烬之灵 | 75 | 15-20 | 火环/回血 |
| 火鲨 | 16 | 8 | 毒注 phlogiston |
| 血肉潜行者 | 150 | 15-20 | 变形伏击/EMP |
| 裸先知 | 65 | 5-10 | X 光/致盲 |
| **星观者** | **6000/INFINITY** | **40** | AP20/死亡爆炸/防弹墙 |
| **夜之主 Armsy** | 400×6段 | 30-50 | 吃臂回血/破坏强化墙 |

## 3.7 升阶路线（8 条）

| 路径 | 升阶名 | 祭品 | 奖励 |
|---|---|---|---|
| 刀 | 银之漩涡 | 无头尸体 | 8 把环绕刀/**全伤口免疫** |
| 血肉 | 牧师终曲 | **4 具** | 蜕形夜之主/召唤上限×3 |
| 灰烬 | 灰烬领主之仪 | 烧焦尸体 | 火环/免疫爆炸火焰太空 |
| 锈蚀 | 锈蚀使者之誓 | 3 具（舰桥） | 全站锈化波/14 项免疫 |
| 虚空 | 时间尽头之舞 | 3 具（零下） | 虚空风暴/75% 弹道偏转 |
| 月亮 | 最后一幕 | 3 具 50+ 脑损 | 理智光环/1/5 船员转疯子 |
| 锁 | 解锁迷宫 | 3 具无器官 | 升阶变形/锁之裂痕无限血 |
| 宇宙 | 造物主之赐 | 3 具带星标 | 星观者（无限血）/宇宙刀强化 |

**升阶通用**：伤害系数 ×0.5、应急梭不可召回、全站公告。

## 3.8 NOVA EDIT（9 处）

① 初始知识 2（原 1）② 移除 greentext ③ 献祭目标 opt-in（限指挥/安保）④ 裂缝可生成于穿梭区 ⑤ 太空游走 traits 清空 ⑥-⑧ 刀上限描述文本修正 ⑨ I18N 全文件。

---

# 第四卷 · 巫师（Wizard）

**代码**: `code/modules/antagonists/wizard/`（31 文件 5,931 行）+ `code/modules/spells/`

## 4.1 核心机制

| 机制 | 值 |
|---|---|
| 法术书 | 初始 **10 点**（半随机 +2/全随机 +5） |
| 升级 | 重复购买=升级 1 级（减冷却） |
| 预设 | Classic/Mjolnir/WizarMy/Soultap（耗尽 10 点锁退款） |
| 新手装 | 蓝帽+蓝袍+传送卷轴+手杖+法术书 |
| 学徒 | 4 流派：破坏/蓝空/治疗/无袍 |

## 4.2 法术全录（6 类 50+）

**攻击类**（默认 2 点）：
| 法术 | 效果 | 冷却 |
|---|---|---|
| Fireball 火球 | 爆炸火球，射程 8 | 6s |
| Rod Form 无敌棒 | 变不可破坏棒冲撞 70+伤 | 25s |
| Smite 天罚 | 触碰爆炸 gib+7 格闪盲 | 60s |
| Mutate 变异 | 30s 绿巨人+激光眼 | 40s |
| Flesh to Stone 石化 | 触碰石化+4s 晕 | 60s |
| Tesla Blast 特斯拉 | 电弧跳跃 30 能量/5 跳 | 30s |
| Lightning Bolt 闪电箭 | 闪电+特斯拉免疫 | 10s |
| Barnyard Curse | 动物面具 | 15s |
| Splattercasting | 变吸血鬼（降冷却耗血） | 1s |
| Sanguine Strike | 吸血+20 上限 | 60s |

**防御类**：Magic Missile 导弹（7 格全打）/Disable Tech EMP/Repulse 斥力/**Time Stop 时停 10s**/Force Wall 力墙/Bind Soul 巫妖绑定/Smoke/Spacetime Distortion。

**机动类**：Mindswap 换脑（40s 昏迷）/Knock 敲门/Blink 闪烁（0-6 格）/Teleport 传送/Ethereal Jaunt 穿墙/Forsake Body 弃体。

**辅助类**：Summon Item/Charge 充能/Wild Shapeshift 变形（6 形态）/Soul Tap 灵魂汲取/Staff of Animation/魂石带/契约。

**仪式类**（不可退款）：Summon Ghosts/Guns/Magic/Events、Curse of Madness 4 点、Mass Teaching 3+。

**天赋类**：Four Hands 四只手/Worm Born 蠕虫重生/Déjà vu 回滚/Heart Eater 食心/Transparence 透明化/Magnetism 磁力。

## 4.3 装备数值

| 装备 | 数值 |
|---|---|
| 巫师帽 | 护甲 30/20/20/30/20 + 全抗 |
| 巫师袍 | 同帽+覆盖全身 |
| **Mjolnir 雷神锤** | 双手 25/投掷 30；命中 1.5s 晕+闪电+扔 200 格 |
| **奇点锤** | 双手 20/重击 5 格奇点牵引 |
| **Spellblade** | 力 20/破甲 75/格挡 50 |
| 高频刀 | 力 8/伤 20-25/超快攻速 |
| Scrying Orb | 灼伤 15+第六感+X 光 |

## 4.4 大仪式（Grand Ritual）

| 机制 | 值 |
|---|---|
| 触发 | **7 次仪式=胜利**（第 7 次自选谢幕） |
| 符文 | 3×3 区域/4s 绘制/完成后 2 分钟全局冷却 |
| 咏唱 | 每段 7s+2s×已完成次数（3 段） |
| 区域 | 白名单（货运/指挥/公共等） |
| 副作用 | 第 2 次起：时空扭曲/EMP/滑油/传送换位等 |
| **8 谢幕** | Evolution 魔法/Usurpation 篡位/Connection 门锁/Jubilation 小丑/Transformation 主题/Perpetuation 不死/Annihilation 自爆圣战 |
| **隐藏第 9** | 奶酪 50 个→**Wabbajack**（999999 充能杖） |

---

# 第五卷 · 邪教（Cult of Nar'Sie）

**代码**: `code/modules/antagonists/cult/`（19 文件 5,498 行）

## 5.1 核心机制

| 机制 | 值 |
|---|---|
| 转化 | Offer 符文：2 教徒转化（治疗 75%+10s 昏迷）/3 人活祭 |
| 初始 | 仪式匕首+10 符文金属+血感 HUD+血魔法 |
| 成长 | 信徒比>Risen 红眼发光/>Ascendent 全光环 |
| 领袖 | Final Reckoning 全体传送/血标 90s/Eldritch Pulse 15s CD |
| **目标** | ①献祭指定目标（3 人解锁复活符文）②**3 弱界点召唤 Nar'Sie**（9 教徒+50s+40.1 自伤） |

## 5.2 符文/仪式（10 种）

| 符文 | 效果 | 需求 |
|---|---|---|
| Offer 献祭 | 转化/活祭/祭品进魂石 | 1-3 |
| Empower 强化 | 血魔法更便宜 | 1 |
| Teleport 传送 | 全站符文网络互传 | 1 |
| **Nar'Sie 召唤** | 召唤古神（只能在对的地点） | **9** |
| Revive 复活 | 复活教徒（每 3 献祭 1 次） | 1 |
| Barrier 屏障 | 不可穿过屏障 | 1 |
| Summon Cultist 召唤教徒 | 将指定教徒传送至符文（施法者受伤 10） | 2 |
| Boil Blood 血沸 | 视线内非教徒 25 伤×3 | 3 |
| Spirit Realm 灵魂界 | 幽灵复制体教徒（限 3） | 1 |
| **Apocalypse 天启** | 消耗召唤点：全站幻影+EMP+灾难 | 3 |

## 5.3 血魔法（10 种）

| 法术 | 效果 | 次数 |
|---|---|---|
| Stun 眩晕 | 16s 麻痹+12s 沉默 | 1 |
| Teleport | 传送教徒 | 1 |
| EMP | 半径 2/5 | 1 |
| Shadow Shackles | 暗影手铐+10s 沉默 | 4 |
| Twisted Construction | 铁→构造外壳/机器人→构造体 | 1 |
| Summon Combat Equipment 召唤战斗装备 | 触碰教徒：邪教甲+锁链+长剑 | 1 |
| Summon Ritual Dagger 召唤仪式匕首 | 遗失后重新召唤仪式匕首 | 1 |
| Hallucinations 幻觉 | 7 格内远程致幻 240s（无声无形） | 4 |
| Conceal Presence 隐匿存在 | 隐藏/显示 5-7 格内符文与邪教结构 | 10 |
| **Blood Rites 血礼** | 抽血充能/治疗/兑换长戟 150/弹幕 300/血束 500 | 5 |

## 5.4 装备

| 装备 | 数值 |
|---|---|
| 仪式匕首 | 力 15/破甲 35/格挡 25 |
| 邪教长剑 | 力 30/格挡 50 |
| 血腥长戟 | 单手 17/双手 24/投掷 40 |
| **Nar'Sien 硬化甲** | 50/40/50/60+全抗（锻造炉） |
| 镜盾 | 格挡造幻觉替身/反射弹道 |
| 红玉髓 | 血魔法+1 栏/施法减半 |

**构造体**：Artificer（造壳）/Wraith（高伤穿墙）/Juggernaut（肉盾）/Harvester。

## 5.5 NOVA EDIT

魂石捕获需 5s do_after（上游无延迟）/Stun 对钟表教团特殊化/献祭目标 opt-in 过滤/混沌法杖移除变形弹种。

---

# 第六卷 · 变形怪（Changeling）

**代码**: `code/modules/antagonists/changeling/`（31 文件 4,593 行）

## 6.1 核心数值

| 机制 | 值 |
|---|---|
| 初始化学 | **20**（上限 75，恢复 1/秒） |
| 基因点 | 10（上限 10） |
| **DNA 存储** | 上限 **6 档案** |
| 吸收 | 3 段×15s do_after（共 45s），第 3 段 40 伤害 |
| 吸收获得 | +10 化学 + 对方一半基因点 |
| 限制 | 不可吸收猴/机器人/无 DNA |

## 6.2 能力全录（40+ 种）

**变身系**：Transform 变身（5 化学复制外貌）/Lesser Form 低级形态（变猴）/Regenerate 再生（10 完全治愈）/Reviving Stasis 假死（15，40s 复活）。

**武器系**：Arm Blade 臂刃（20，25 伤/破甲 35）/Tentacle 触手（10，8 格抓取）。

**防御系**：Organic Shield 有机盾（20，50% 格挡）/Chitinous Armor 甲壳甲（20，40/40/40 护甲）/Fleshmend 愈合（20，10s 每秒 4 钝/4 缺/2 灼）/Anatomic Panacea（20 清全部负面）。

**攻击毒刺**：Transformation Sting 变形毒刺（33）/Extract DNA Sting（25）/Mute 沉默（20）/Blind 致盲（25，40s）/Hallucination 幻觉（10，180s）/Cryogenic 冻油（15）。

**特殊**：Last Resort 自爆（20，变头虫）/Defibrillator Grasp 反杀（被电击撕双臂复活）/Hive Head 蜂巢（15，6 蜜蜂）。

**NOVA 移除**：Spread Infestation 蜘蛛能力（原 45 化学）整文件注释。

## 6.3 限制

DNA 满 6 挤出最旧（初始档案保护）/假死中不可购买/被吸收者转堕落变形怪/头虫蛋 4 分钟孵化爆体变猴。

---

# 第七卷 · 绑架者（Abductor）

**代码**: `code/modules/antagonists/abductor/`（31 文件 3,209 行）

## 7.1 核心机制

| 机制 | 值 |
|---|---|
| 团队 | Mothership+希腊字母编号 |
| 目标 | **试验 6 名人类** |
| 角色 | Agent（抓人）/Scientist（手术）/Solo |
| 流程 | 电棍放倒→手术摘心植腺体→实验机（+1 点/腺体）→信用点兑换 |

## 7.2 腺体全录（15 种）

| 腺体 | 冷却 | 效果 |
|---|---|---|
| chem 药厂 | 50s | 注入随机药物/毒素 |
| egg 产蛋 | 300-400s | 产出试剂蛋 |
| electric 电击 | 800-1200s | 免疫电击+放电 4 格 |
| heal 自愈 | 200-400s | 再生+排植入物 |
| mindshock 心灵冲击 | 40-70s | 眩晕/脑伤/幻觉 |
| plasma 血浆 | 1200-1800s | 呕吐 50 等离子 |
| quantum 量子 | 150s | 与目标位置互换 |
| slime 史莱姆 | 600-1200s | 呕吐友好史莱姆 |
| spiderman 蜘蛛 | 450-900s | 生成守护蜘蛛 |
| transform 变形 | 900-1800s | 随机外观/物种 |
| trauma 脑伤 | 800-1200s | 随机脑创伤（限 5 次） |
| ventcrawling 通风管 | 1800-2400s | 永久爬管（限 1 次） |
| viral 病毒 | 1800-2400s | 携带高级疾病（限 1 次） |
| blood 血液 | 1200-1800s | 血型变试剂 |
| **access 全权限** | 600-1200s | 全部区域访问权（限 1 次） |

## 7.3 装备

| 装备 | 数值 |
|---|---|
| 外星手枪 | 20 毒伤+30% 辐射脉冲 |
| 先进电棍 | 4 模式：眩晕 14s/催眠 2 分钟/上铐/探查 |
| 缩小射线 | 3s 冷却，缩小 30s 爆体 |
| 全工具刀 | 工具速度 0.25 |
| 特工背心 | 战斗 50 全甲+肾上腺素 |

---

# 第八卷 · 菌潮（Blob）

**代码**: `code/modules/antagonists/blob/`（27 文件 2,759 行）

## 8.1 核心数值

| 机制 | 值 |
|---|---|
| 初始点数 | **60**（上限 100，基础 +2/秒） |
| 扩张 | 4 点/格（攻击命中退 2） |
| **胜利** | **400 格**合法菌潮 |
| 公开警报 | 75 格或 10 分钟 |
| 核心 | 400 HP（迁移 80 点） |
| 节点 | 200 HP（建造 50 点） |
| 普通菌潮 | 25 HP（建造 4 点） |
| 强化菌潮 | 150 HP（建造 15 点） |
| 反射菌潮 | 150 HP（建造 15 点） |

## 8.2 菌株全录（14 种）

**试剂类**（攻击=注入 25 体积试剂）：
| 菌株 | 效果 |
|---|---|
| Reactive Spines 反应棘 | 高钝穿甲+被攻击全圈反击 |
| Explosive Lattice 爆炸格 | 爆炸 AOE+孢子死亡爆炸 |
| Cryogenic Poison 冷冻毒 | 注入冻油/冰/本体 |
| Blazing Oil 燃烧油 | 点火+80% 引火 |
| Distributed Neurons 神经 | 毒伤+尸体变孢子僵尸 |
| Electromagnetic Web 电磁网 | EMP+钝伤全额 |
| Energized Jelly 能量果冻 | 缺氧+体力+窒息 |
| Networked Fibers 网状纤维 | 核心再生+3 |
| Pressurized Slime 加压黏液 | 制造润滑地板 |
| Regenerative Materia 再生物质 | 核心再生+18+假健康 HUD |
| Replicating Foam 复制泡沫 | 30% 二次扩张 |
| Shifting Fragments 位移碎片 | 被攻击换位 |
| Synchronous Mesh 同步网格 | 满围 2.6×伤害平摊 |
| Debris Devourer 碎屑吞噬 | 收集物品喷射 |

**孢子**：30 HP/攻 4-8/死亡毒云。**魔像**：200 HP/攻 4+20 试剂/核心 2 格内回 5%/秒。

## 8.3 NOVA EDIT

变形怪蜘蛛能力移除/肌肉极限 5 层瘫/毒刺对机器人无效/绑架者团队默认实验目标移除/I18N 全文件。

---

# 第九卷 · 其他反派

## 9.1 恶意 AI（Malf AI）

**代码**: `code/modules/antagonists/malf_ai/`（3 文件 1,785 行）

**AI 模块 23 种**（CPU 点）：

| 模块 | CPU | 效果 |
|---|---|---|
| **Doomsday Device 末日装置** | 130 | 最终毁灭 |
| 站台封锁 | 30 | 全站门锁 |
| 机器覆盖 | 30 | 接管机器 |
| 摧毁 RCD | 25 | 摧毁建造器 |
| 机器过载 | 20 | 机器爆炸 |
| 黑灯 | 15 | 全站断电 |
| **机器人工厂** | 100 | 机器人转化（移除分流） |
| 空气警报覆盖 | 50 | 接管空气警报 |
| 热传感器覆盖 | 25 | 接管火警 |
| 应急灯禁用 | 10 | 关应急灯 |
| 摄像头重启 | 10 | 恢复摄像头 |
| 摄像头升级 | 35 | 增强监控 |
| AI 炮塔升级 | 30 | 炮塔强化 |
| 增强监控 | 30 | 窃听 |
| **机械体统治** | 30 | 控制机甲 |
| 变声器 | 20 | 伪装声音 |
| 定点安全覆盖 | 20 | 电磁撬锁 |
| 滚动伺服 | 10 | 核心倾斜 |

## 9.2 血虫（Blood Worm）

**代码**: `blood_worm/`（17 文件 2,666 行）

| 机制 | 值 |
|---|---|
| 类型 | 血虫阵营（寄生生物） |
| 特质 | TRAIT_APATHETIC（冷漠）+ TRAIT_FEARLESS（无畏） |
| 特性 | 不可被转化（TRAIT_UNCONVERTABLE） |
| 玩法 | 吸血/附身宿主/团队（GLOB.blood_worm_team） |

## 9.3 虚空行者（Voidwalker）

**代码**: `voidwalker/`（11 文件 1,129 行）

| 机制 | 值 |
|---|---|
| 类型 | 太空反派（骚扰太空附近的人） |
| 目标 | 带人看虚空真相 / 找回孩子 |
| 彩蛋 | 5% 文案 "Man I fucking love glass." |

## 9.4 逃犯（Fugitive）

**代码**: `fugitive/`（6 文件 1,025 行）

**5 种背景**：越狱犯/邪教徒/沃尔多（捉迷藏）/合成人（S.E.L.F. 解放）/隐形人（隐身原型偷窃）
**目标**：躲避逃犯猎人（存活）

## 9.5 革命（Revolution）

**代码**: `revolution/`（3 文件 787 行）

| 机制 | 值 |
|---|---|
| 转化 | 闪光（flash）成功转化碳基 → 革命者 |
| 头目 | 头目升级（max_headrev）、闪光转化 |
| 暗号 | 头目专属暗号（code phrases/responses） |
| 胜利 | 头目存活+推翻指挥层 |

## 9.6 太空龙（Space Dragon）

**代码**: `space_dragon/`（3 文件 677 行）

| 机制 | 值 |
|---|---|
| 核心 | 召唤**裂隙**（rift）→ 太空鲤增援 |
| 链接 | Wavespeak 心灵链接（龙↔太空鲤） |
| 胜利 | 裂隙充能完成 |
| 限制 | 裂隙只能在特定区域放置 |

## 9.7 其他小型阵营（27 个）

| 阵营 | 行数 | 说明 |
|---|---|---|
| 小丑核弹队 Clown Ops | 396 | 小丑版核弹队 |
| 噩梦 Nightmare | 355 | 暗影生物 |
| 亡魂 Revenant | 364 | 幽灵 |
| 兄弟 Brother | 331 | 结拜兄弟 |
| 痴迷 Obsessed | 309 | 跟踪狂 |
| 分裂分子 Separatist | 280 | 独立分子 |
| 太空忍者 Space Ninja | 173 | 忍者 |
| 异形 Xeno | 175 | 异形 |
| 灰烬行者 Ash Walker | 80 | 灰烬之地部落（猎杀外来者） |
| 战列巡洋舰 Battlecruiser | 65 | 辛迪加战列舰乘员 |
| 洗脑 Brainwashed | 87 | 洗脑受害者（植入物） |
| 邪恶克隆 Evil Clone | 68 | 平行时间克隆体 |
| 高地人 Highlander | 103 | 高地人决斗（只能活一个） |
| 催眠 Hypnotized | 19 | 被催眠的目标 |
| 变形体 Morph | 7 | 拟态怪物（伪装成物品） |
| 悖论克隆 Paradox Clone | 85 | 时间悖论克隆体 |
| 太空海盗 Space Pirate | 1298 | 海盗舰队 |
| 火焰史莱姆 Pyroclastic | 27 | 火焰异常体 |
| 圣诞老人 Santa | 59 | 圣诞老人（节日） |
| 有知觉生物 Sentient Creature | 41 | 觉醒宠物变反派 |
| 蜘蛛 Spiders | 56 | 蜘蛛巢穴 |
| 间谍 Spy | 1413 | 偷窃任务间谍 |
| 生存主义者 Survivalist | 104 | 生存主义者 |
| 辛迪加猴 Syndicate Monkey | 37 | 辛迪加猴子特工 |
| 情人节 Valentine | 110 | 情人节/心碎者（节日） |
| 捕蝇草人 Venus Human Trap | 26 | 食人植物人 |
| 许愿机 Wishgranter | 31 | 许愿机化身 |

---

---

---

# 附录B · Uplink 物品全录（323 件 · 中文翻译版）

**代码**: `code/modules/uplink/`（20 文件 3,746 行）

**说明**: TC=电信水晶（叛徒货币）；核弹弹药/增援等部分物品价格由队伍折扣动态决定（显示 ?）


## 角色限定（42）

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
# 附录 · 代码路径索引

**源码总量：约 64,391 行**（antagonists 60,645 + uplink 3,746）

| 系统 | 文件 | 行数 |
|---|---|---|
| **叛徒** | `code/modules/antagonists/traitor/`（7 文件） | 1,100 |
| 叛徒核心 | `traitor/datum_traitor.dm` | 354 |
| Uplink handler | `traitor/uplink_handler.dm` | 138 |
| 承包商 | `traitor/contractor/`（5 文件） | 608 |
| Uplink 物品 | `code/modules/uplink/`（20 文件） | 3,746 |
| **核弹队** | `code/modules/antagonists/nukeop/`（16 文件） | 2,464 |
| 特工核心 | `nukeop/datums/operative.dm` + `operative_team.dm` | 591 |
| 挑战模式 | `nukeop/equipment/nuclear_challenge.dm` | 207 |
| **异端** | `code/modules/antagonists/heretic/`（103 文件） | 18,945 |
| 异端怪物 | `code/modules/mob/living/basic/heretic/` | — |
| **巫师** | `code/modules/antagonists/wizard/`（31 文件） | 5,931 |
| 法术 | `code/modules/spells/`（88 文件） | 7,405 |
| **邪教** | `code/modules/antagonists/cult/`（19 文件） | 5,498 |
| **变形怪** | `code/modules/antagonists/changeling/`（31 文件） | 4,593 |
| **绑架者** | `code/modules/antagonists/abductor/`（31 文件） | 3,209 |
| **菌潮** | `code/modules/antagonists/blob/`（27 文件） | 2,759 |
| **恶意 AI** | `code/modules/antagonists/malf_ai/`（3 文件） | 1,785 |
| **间谍** | `code/modules/antagonists/spy/`（4 文件） | 1,413 |
| **海盗** | `code/modules/antagonists/pirate/`（5 文件） | 1,298 |
| **血虫** | `code/modules/antagonists/blood_worm/`（17 文件） | 2,666 |
| **虚空行者** | `code/modules/antagonists/voidwalker/`（11 文件） | 1,129 |
| **逃犯** | `code/modules/antagonists/fugitive/`（6 文件） | 1,025 |
| **革命** | `code/modules/antagonists/revolution/`（3 文件） | 787 |
| **太空龙** | `code/modules/antagonists/space_dragon/`（3 文件） | 677 |
| **小丑核弹队** | `code/modules/antagonists/clown_ops/`（4 文件） | 396 |
| **噩梦/亡魂/其他** | 30+ 小型目录 | ~3,000 |
| 通用 | `code/modules/antagonists/_common/`（5 文件） | 1,385 |

> **文档完** — 覆盖反派系统全貌：叛徒（Uplink 323 件）、核弹队、异端（8 路径/46 法术/11 召唤物/8 升阶）、巫师（50+ 法术/大仪式）、邪教（12 符文/血魔法）、变形怪（40+ 能力）、绑架者（15 腺体）、菌潮（14 菌株）、恶意 AI（23 模块）+ 30 小型阵营，全部带精确数值与代码索引。