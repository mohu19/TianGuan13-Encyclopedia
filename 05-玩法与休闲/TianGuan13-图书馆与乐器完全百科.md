# 天关 — 图书馆与乐器完全百科

> **项目**: TianGuan13 (Nova Sector → /tg/station)
> **代码**: `code/modules/library/`（4,118 行）+ `code/modules/instruments/`（1,939 行）
> **范围**: **图书馆**（图书管理员+技能学习）+ **乐器**（演奏系统）
>
> **关联文档**：宗教圣书见《售货机与宗教》；技能系统与《职业百科》联动。

---

## 目录

- [第一卷 · 图书馆系统](#第一卷--图书馆系统)（借阅链+8 类书+22 维基手册）
- [第二卷 · 技能学习](#第二卷--技能学习)（30 芯片+1-7 级表）
- [第三卷 · 乐器](#第三卷--乐器)（20 种+44 音色+合成器）
- [附录 · 代码路径索引](#附录--代码路径索引）

---

# 第一卷 · 图书馆系统

**代码**: `code/modules/library/`（4,118 行，28 文件）

## 1.1 书籍核心（book.dm 464 行）

| 机制 | 值 |
|---|---|
| **书属性** | 标题/作者/内容 |
| **到期日** | 借阅期限（due_date） |
| 唯一书 | unique（不可复制/修改） |
| **挖空** | carved（藏物于书） |
| 图标 | 8 种状态 |
| 攻击 | 砸/敲/教育（bashes/educates） |
| **NOVA** | I18N 全服中文反查（标题/正文） |

## 1.2 图书馆机器（lib_machines.dm 865 行）

| 机器 | 功能 |
|---|---|
| 借阅终端 | 借书/还书 |
| 扫描仪 | 条形码扫描 |
| 打印机 | 打印书 |
| 书架 | 藏书 |

## 1.3 书籍类型

| 类型 | 说明 |
|---|---|
| **普通书** | 可复制/可改 |
| **唯一书** | 特殊（不可复制） |
| **圣经** | 宗教书（19 皮肤） |
| **随机书** | 随机生成内容 |

## 1.4 图书管理员玩法

- 管理书架/借阅
- 扫描条形码
- 打印/复制书
- **建馆**（摆放书架）

---


---


## 1.5 图书馆机器详表

| 机器 | 功能 |
|---|---|
| **图书馆访客控制台** | 借书/还书/查询（TGUI） |
| **条形码扫描仪** | 扫码记录 |
| **书架** | 藏书/取书 |
| **技能站** | 技能芯片安装（见第二卷） |
| 随机书生成器 | 随机内容 |

## 1.6 圣经（bibles.dm 400 行）

| 类型 | 说明 |
|---|---|
| **19 种皮肤** | Bible/Quran/燃烧/小丑/香蕉/克苏鲁/托梅/死灵之书等 |
| 功能 | 祝福/圣水（联动宗教） |



## 1.7 图书馆机器链（借阅/扫描/打印/上传）

| 机器 | 功能 |
|---|---|
| **访客检索台** | SQL 检索书库（作者/标题/分类/ID，每页 18 本） |
| **图书管理台** | 6 屏：库存/借出/档案/上传/打印/隐藏页 |
| **条码扫描器** | 3 模式：还书/借出/入库（先配对管理台） |
| **扫描仪** | 扫描书→缓存副本（上传必需） |
| **装订机** | 刷 ID 设作者→放纸→4.1 秒装订成书 |

## 1.8 借阅流程

| 步骤 | 操作 |
|---|---|
| 入库 | 扫描器 Add to inventory |
| 借出 | 管理台选书+借阅人+借期→生成记录 |
| 还书 | 扫描器 Check in（书数据全等匹配） |
| 逾期 | 管理台 overdue 标记 |

## 1.9 书籍类型（8 类）

| 类型 | 可复制 | 说明 |
|---|---|---|
| **普通书** | ✅ | 可涂鸦 |
| **手册** | ❌ | 预设内容 |
| **随机手册** | ❌ | 打开随机替换 |
| **维基手册** | ❌ | 内嵌 iframe 跳维基 |
| **电子书** | ❌ | 定制 TGUI |
| **圣经** | ❌ | 19 皮肤 |
| **恶魔之书** | ❌ | 防火/酸/岩浆 |
| **随机书** | 生成后 | 数据库随机拉取 |
| **授予书** | ❌ | 阅读授予技能/法术 |

## 1.10 固定手册（5 本）

| 手册 | 内容 |
|---|---|
| **人类收割** | 豆荚人种植教学 |
| **Ripley 机甲手册** | 机甲建造 |
| **厨师菜谱** | 料理 |
| **核弹手册** | 核弹操作（随机池排除） |
| **古代羊皮纸** | 法师大仪式 |

## 1.11 维基手册与操作指南全录（34 本 · 源码全量）

> **源码**: `code/modules/library/` + 全库 `/obj/item/book/manual/` 定义。此前文档仅一句话带过约 15 本，现全量列出 34 本（22 本 wiki 系列 + 12 本其他手册）。

### wiki 系列（22 本）

| 手册 | 中文名/内容 |
|---|---|
| Lexica Atmosia | 大气圣经 |
| Barman Recipes: Mixing Drinks and Changing Lives | 调酒配方 |
| Chemistry Textbook | 化学教科书 |
| To Serve Man | 服务人类（人肉烹饪） |
| Unethically Grown Organics | 细胞学 |
| The Film Noir: Proper Procedures for Investigations | 调查程序 |
| Station Repairs and Construction | 工程建造 |
| Engineering Textbook | 工程教科书 |
| Hacking | 黑客手册 |
| Mentoring your Experiments | 实验指导 |
| DIY Chemical Grenades | 化学手榴弹 DIY |
| Infections - Making your own pandemic! | 传染病制作 |
| Medical Space Compendium, Volume 638 | 医学纲要 |
| medigun operating manual | 医疗枪操作 |
| Ordnance for Dummies or: How I Learned to Stop Worrying and Love the Maxcap | 军械入门 |
| Chemical Factories Without Narcotics | 化学工厂 |
| Research and Development 101 | R&D 101 |
| Robotics for Dummies | 机器人入门 |
| Space Law | 太空法 |
| 标准作业程序手册 |  |
| 供应SOP手册 |  |
| 中央指挥部SOP手册 |  |
| 指挥SOP手册 |  |
| 工程SOP手册 |  |
| 通用SOP手册 |  |
| 医疗SOP手册 |  |
| 科研SOP手册 |  |
| 安全SOP手册 |  |
| 服务SOP手册 |  |
| Brain Surgery for Dummies | 脑外科入门 |
| Subspace Telecommunications And You | 电信指南 |
| Teleportation Science - Bluespace for dummies! | 传送科学 |
| Tactical Game Cards - Player's Handbook | TCG 玩家手册 |

### 其他手册（12 本）

| 手册 | 内容 |
|---|---|
| ancient parchment | ancient_parchment |
| Chef Recipes | chef_recipes |
| Fish Encyclopedia | fish_catalog |
| The Human Harvest: From Seed to Market | hydroponics_pod_people |
| Fission Mailed: Nuclear Sabotage 101 | nuclear |
| APLU \ | ripley_build_and_repair |
| Starfruit preperation and you! | starfruit |
| Wargame: Blue Lizard - Example Ruleset | wargame_rules |
| Wargame: Blue Lizard - Example Ships and Scenarios | wargame_rules/examples |

## 1.12 emag 隐藏页

| 功能 | 效果 |
|---|---|
| **打印禁忌知识** | 献祭匕首+**年龄+10**（仅 1 次） |
| 拒绝 | 无奖励 |

## 1.13 NOVA 修改

①**读书 +15 语言经验**（读 150 本到传奇级）②标题/正文反查译文 ③乐器仅 I18N。


# 第二卷 · 技能学习

**代码**: `library/skill_learning/`（1,426 行）

## 2.1 技能芯片机制

| 机制 | 值 |
|---|---|
| **芯片** | 脑植入生物芯片（技能掌握） |
| 复杂度 | 1（脑容量限制） |
| 槽位 | 1（大脑槽上限硬设） |
| 价格 | 150 信用点 |
| 冷却 | 5 分钟（提取后） |
| 可移除 | 部分芯片可移除 |
| **技能站** | skill_station（311 行）安装 |

## 2.2 技能芯片全录（30 个）

| 芯片 | 类别 | 效果 |
|---|---|---|
| **杂技 Spinesthetics** | 通用 | 翻滚 |
| **ERROERERROR** | 通用 | 彩蛋（接吻杂技） |
| **ID 鉴定** | 通用 | 识别 ID 价值 |
| **水下编织篮** | 通用 | 彩蛋技能 |
| **强化指点** | 通用 | 大箭头指路 |
| **修剪 Hedgetrimming** | 通用 | 盆栽修剪 |
| **洗脑 Brainwashing** | 通用 | 洗脑 |
| **厨师之吻** | 通用 | 料理彩蛋 |
| **核磁盘验证** | 通用 | 核弹盘验证 |
| **无畏处理员** | 通用 | 管道加速 |
| **酒醉徒手精通** | 通用 | 醉拳 |
| **内脏阅读** | 通用 | 读内脏 |
| **心理味觉微积分** | 通用 | 彩蛋 |
| **近距离烹饪** | 职业 | 厨师 |
| **气球经** | 职业 | 小丑 |
| **侦探之味** | 职业 | 侦探 |
| **工程电路** | 职业 | 工程师 |
| **无声之声** | 职业 | 清洁工 |
| **战斗机器人爱好者** | 职业 | 矿工 |
| **超物质认知** | 职业 | 心理医生 |
| **赛博格电路** | 职业 | 机器人学家 |
| **灯泡移除** | 通用 | 快速拆灯 |
| **渔夫鉴别** | 通用 | 钓鱼精通 |
| **嘲讽闪避** | 通用 | 矩阵闪避 |
| **音乐记忆** | 通用 | 音乐 |
| **真实力量** | 职业 | **研究总监** |
| **佩剑开瓶** | 通用 | 军刀开香槟 |
| **自手术** | 通用 | 自己动手术 |
| **无用适配器** | 通用 | 彩蛋（0 复杂度） |
| **葡萄酒品鉴** | 通用 | 品酒 |

## 2.3 技能站

**代码**: `skill_station.dm`（311 行）——安装/移除技能芯片的机器。



## 2.4 技能等级经验表（1-7 级）

| 等级 | 经验 |
|---|---|
| 1 | 0 |
| 2 | 100 |
| 3 | 250 |
| 4 | 500 |
| 5 | 900 |
| 6 | 1500 |
| 7 | 2500 |

## 2.5 Skillsoft 站

| 机制 | 值 |
|---|---|
| 植入/取出 | **15 秒** |
| 上限 | 复杂度+槽位双限制 |
| 冷却 | 5 分钟 |


# 第三卷 · 乐器

**代码**: `code/modules/instruments/`（1,939 行，17 文件）——**20 种乐器**

## 3.1 乐器全录（20 种）

| 乐器 | 说明 |
|---|---|
| **手风琴** | 键盘乐器 |
| **班卓琴** | 弦乐 |
| **鎏金自行车喇叭** | 彩蛋 |
| **电吉他** | 电声 |
| **钟琴** | 打击 |
| **吉他** | 经典 |
| **口琴** | 吹奏 |
| **音乐飞蛾** | **彩蛋生物** |
| **合成器** | 电子（MIDI） |
| **耳机** | 合成器配件 |
| **太空舱耳机** | 彩蛋 |
| **竖笛** | 吹奏 |
| **萨克斯** | 吹奏 |
| **幽灵萨克斯** | 幽灵版 |
| **长号** | 吹奏 |
| **幽灵长号** | 幽灵版 |
| **小号** | 吹奏 |
| **幽灵小号** | 幽灵版 |
| **太空小提琴** | 弦乐 |
| **金色小提琴** | 稀有版 |

## 3.2 演奏机制

| 机制 | 说明 |
|---|---|
| **MIDI 演奏** | 合成器支持 |
| 手弹 | 手动演奏 |
| 歌曲 | songs 数据 |
| 音效 | 演奏音效 |

---


## 3.3 演奏格式

| 机制 | 说明 |
|---|---|
| **音名** | A-G+变音记号 |
| **八度** | 音高范围 |
| 时值 | `/时值` 记法 |
| **BPM** | 首行设定 |
| 上限 | 1000×300 音符 |

## 3.4 音色全录（43 个/8 类 · 源码全量）

**源码**: `code/modules/instruments/instrument_data/`（10 文件）。此前文档音色表 8 行全是"—"占位，现从源码补全全部 43 个音色：

| 类别 | 音色 |
|---|---|
| **铜管** | Crisis Brass Section |
| **铜管** | Crisis Trombone |
| **铜管** | Crisis Trumpet |
| **打击键盘** | Crisis Vibraphone |
| **打击键盘** | SGM Music Box |
| **打击键盘** | FluidR3 Celeste |
| **趣味** | !!HONK!! |
| **趣味** | Ping |
| **趣味** | Chime |
| **趣味** | MeowSynth |
| **趣味** | Spaceman |
| **趣味** | Moth Scream |
| **趣味** | Bilehorn |
| **吉他** | Crisis Steel String Guitar |
| **吉他** | Crisis Nylon String Guitar |
| **吉他** | Crisis Clean Guitar |
| **吉他** | Crisis Muted Guitar |
| **硬编码** | Accordion |
| **硬编码** | Bike Horn |
| **硬编码** | Electric Guitar |
| **硬编码** | Glockenspiel |
| **硬编码** | Guitar |
| **硬编码** | Harmonica |
| **硬编码** | Piano |
| **硬编码** | Recorder |
| **硬编码** | Saxophone |
| **硬编码** | Trombone |
| **硬编码** | Violin |
| **硬编码** | Xylophone |
| **硬编码** | Banjo |
| **风琴** | Crisis Church Organ |
| **风琴** | Crisis Hammond Organ |
| **风琴** | Crisis Accordian |
| **风琴** | Crisis Harmonica |
| **风琴** | Crisis Tango Accordian |
| **钢琴** | FluidR3 Grand Piano |
| **钢琴** | FluidR3 Harpsichord |
| **钢琴** | Crisis Harpsichord |
| **钢琴** | Crisis Grand Piano One |
| **钢琴** | Crisis Bright Piano One |
| **合成音** | Ideal square wave |
| **合成音** | Ideal sine wave |
| **合成音** | Ideal sawtooth wave |

## 3.5 合成器（可编程）

| 机制 | 说明 |
|---|---|
| **合成器** | 全部合成音色可切换 |
| **集成芯片** | 12 输入/3 输出端口 |
| MIDI 自动演奏 | 芯片驱动 |
| 变体 | 耳机/太空舱 |

## 3.6 NOVA 修改

仅 I18N（无功能性改动）。


# 附录 · 代码路径索引

| 系统 | 文件 | 行数 |
|---|---|---|
| 书籍 | `library/book.dm` | 464 |
| 图书馆机器 | `library/lib_machines.dm` | 865 |
| 圣经 | `library/bibles.dm` | 400 |
| 书架 | `library/bookcase.dm` | 249 |
| 条形码 | `library/barcode_scanner.dm` | 119 |
| 随机书 | `library/random_books.dm` | 137 |
| **技能学习** | `library/skill_learning/` | **1,426** |
| **乐器** | `instruments/`（17 文件） | **1,939** |
