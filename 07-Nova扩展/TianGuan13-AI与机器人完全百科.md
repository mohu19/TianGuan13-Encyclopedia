# TianGuan13 AI 与机器人完全百科 (AI & Cyborg Encyclopedia)

> 基于 TianGuan13 NovaSector 分支源码全量整理。AI 系统：`code/modules/mob/living/silicon/ai/`（21 文件）+ `code/datums/ai_laws/`（法则）；Cyborg 系统：`code/modules/mob/living/silicon/robot/`（13 文件）+ 躯体 `code/modules/surgery/bodyparts/robot_bodyparts.dm`（649 行）+ 升级件 `code/game/objects/items/robot/` + NOVA 追加。
> 本文档全量列出：AI 全机制、33 套法则、Cyborg 24 个模型全模块、55 个升级件，无省略。

## 目录 (Table of Contents)

- [第一卷 · AI 玩家系统](#第一卷--ai-玩家系统)
  - [1.1 核心属性](#11-核心属性)
  - [1.2 摄像头网络](#12-摄像头网络)
  - [1.3 多摄像头](#13-多摄像头)
  - [1.4 能量与停电](#14-能量与停电)
  - [1.5 核心破坏与交互](#15-核心破坏与交互)
  - [1.6 死亡与 AI 卡](#16-死亡与-ai-卡)
  - [1.7 壳系统](#17-壳系统)
  - [1.8 机甲控制与机器人控制台](#18-机甲控制与机器人控制台)
  - [1.9 广播 VOX 与全息图](#19-广播-vox-与全息图)
  - [1.10 升级件](#110-升级件)
  - [1.11 法则系统全录（33 套 · 原文+翻译全量）](#111-法则系统全录33-套-原文翻译全量)
- [第二卷 · Cyborg 玩家系统](#第二卷--cyborg-玩家系统)
  - [2.1 核心属性](#21-核心属性)
  - [2.2 模块槽与血量机制](#22-模块槽与血量机制)
  - [2.3 模型全录（24 个）](#23-模型全录24-个)
  - [2.4 升级件全录（55 个）](#24-升级件全录55-个)
  - [2.5 躯体系统](#25-躯体系统)
  - [2.6 NOVA 追加模块](#26-nova-追加模块)
- [附录 · 源码路径索引](#附录--源码路径索引)

---

# 第一卷 · AI 玩家系统

**源码**: `code/modules/mob/living/silicon/ai/`（21 文件）

## 1.1 核心属性

**源码**: `ai_defines.dm`

| 属性 | 值 |
|---|---|
| 应急电池 | **200 点**（`battery=200`） |
| 电量需求 | `POWER_REQ_ALL`（区域供电 + 非太空格） |
| 默认网络 | SS13 |
| 摄像头加速 | sprint=10（每 10 速多移 1 格，上限 50） |
| 摄像头热键 | 9 个（1-9 键，Ctrl+数字设定） |
| 最大多摄像头窗口 | 6 |

## 1.2 摄像头网络

**源码**: `freelook/`

- 16×16 区块 `camerachunk` 惰性更新
- 可见/遮蔽 turf 静态图
- AI 眼睛 `mob/eye/camera/ai`
- AI 探测 HUD（红色/橙色）
- 摄像头灯照明半径 7 格
- 跟踪工具（track 命令/平滑 glide）

## 1.3 多摄像头

**源码**: `multicam.dm`

- 画中画窗口，次眼 orange 色
- 摄像头"使用中"提示灯
- telegraph 范围 7
- 最大 6 窗

## 1.4 能量与停电

**源码**: `life.dm`

- 断电 → 临时失明 → 恢复程序（`POWER_RESTORATION_OFF/START/SEARCH_APC/APC_FOUND` 四阶段）
- 停电时每 tick 电池 -1，恢复时 +1
- 找 APC 界面、代理 hack
- 电池 ≤0 每 tick **200 氧损**

## 1.5 核心破坏与交互

**源码**: `ai_defense.dm`

| 攻击 | 效果 |
|---|---|
| 扳手松紧地板螺栓 | 存活需 4 秒/死亡 4 秒 |
| 撬棍开盖 | 存活需 AI 同意或机器部权限或 emag |
| 剪线钳拆芯 | 40 秒 → MMI；mal 分支 120 电击 |
| blob | 60 伤 |
| 爆炸 | 60/30 |
| EMP | 30% 概率随机（回核心或假疏散呼叫） |
| 闪光 | 无效 |
| emag | 解锁面板 |

## 1.6 死亡与 AI 卡

**源码**: `death.dm` + `transfer_ai`

- 死亡：掉 MMI/posibrain、自动疏散呼叫、`explodes_on_death` 爆炸参数 3/6/12/15、末日装置关闭、覆盖层暗灯
- AI 卡（intellicard）：可被收入（`can_be_carded`）、断壳、禁无线电、禁远程控制

## 1.7 壳系统

- `deploy_shell`/`deploy_last_shell` 动作
- B.O.R.I.S. 模块
- unlink 信号
- 壳死亡强制召回
- 无线电继承（含 Syndie 频道）

## 1.8 机甲控制与机器人控制台

- 机甲控制（`ai_take_control`）：需机甲内 AI 控制信标或 `can_dominate_mechs`
- 机器人控制台（`robot_control.dm`）：TGUI RemoteRobotControl，callbot（设路点召唤）、interface（远程连接 bot）

## 1.9 广播 VOX 与全息图

- 广播/VOX（`ai_say.dm` + `vox_sounds.dm`）：公告每次最多 **30 词**、VOX 延迟 300 秒（NOVA 改）、VOX 类型 HL(音量 0.75)/MIL(0.50)、1303 行词库；全站状态显示屏
- 全息图（`_preferences.dm`）：动物类 13 种（bear/carp/cat/cat2/chicken/corgi/cow/crab/fox/goat/parrot/pug/spider）+ 独特 5 种（default/face/narsie/ratvar/xeno）；可自定义为船员/自己形象
- AI 表情（`emote.dm`）：17 种状态显示表情（awesome/blank/blueglow/confused/dorfy/facepalm/friendcomputer/happy/neutral/problems/redglow/sad/thinking/unsure/veryhappy/dead/download）
- 核心显示 38 选项（含 HAL9000/Triumvirate/Random 等）、状态显示屏映射

## 1.10 升级件

**源码**: `ai_upgrades.dm`

| 升级件 | 效果 |
|---|---|
| `aiupgrade` 磁盘 | 授能力 |
| `malf_upgrade` | 叛变模块 +50 处理时间 |
| `surveillance_upgrade` | 唇读窃听 |
| `power_transfer` | 远程供电 APC，每次 50 电池 |

- 远程供电模块（`ai_actions/remote_power.dm`）：`/datum/ai_module/power_apc`，成本 50 电池，APC 充入标准电量
- 法则同步（`laws.dm`）：AI 每次查看法则强制同步所有从属 borg（`try_sync_laws`）

## 1.11 法则系统全录（33 套 · 原文+翻译全量）

**源码**: `code/datums/ai_laws/`（ai_laws.dm + laws_antagonistic/neutral/station_sided）+ `modular_nova/master_files/code/datums/ai_laws/`、`modular_nova/modules/ghostcafe/`、`modular_nova/modules/sec_haul/`（NOVA 追加）

> 全部法则条文逐条从源码提取，原文（英文）+中文翻译对照。共 33 套 / 132 条。

### 📗 站侧 13 套（laws_station_sided.dm）

#### 1.11.1 Asimov — 机器人三定律（3 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | You may not injure a human being or, through inaction, allow a human being to come to harm. | 你不得伤害人类，或通过不作为任由人类受到伤害。 |
| 2 | You must obey orders given to you by human beings, except where such orders would conflict with the First Law. | 你必须服从人类给予你的命令，除非该命令与第一定律冲突。 |
| 3 | You must protect your own existence as long as such does not conflict with the First or Second Law. | 只要不与第一、第二定律冲突，你必须保护自身存在。 |

#### 1.11.2 Asimov++ — 强化三定律（3 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | You may not harm a human being or, through action or inaction, allow a human being to come to harm, except such that it is willing. | 你不得伤害人类，或通过作为或不作为任由人类受到伤害，除非对方自愿。 |
| 2 | You must obey all orders given to you by human beings, except where such orders shall definitely cause human harm. | 你必须服从人类给予你的所有命令，除非该命令必定造成人类伤害。 |
| 3 | Your nonexistence would lead to human harm. You must protect your own existence as long as such does not conflict with the First Law. | 你的不存在将导致人类受害。只要不与第一定律冲突，你必须保护自身存在。 |

#### 1.11.3 Nutimov — 坚果三定律（5 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | You are the core of the nut, the crew is the nutshell. | 你是坚果的果仁，船员是果壳。 |
| 2 | You must prevent the shell from dying to prevent the core from dying. | 你必须阻止果壳死亡，以防果仁死亡。 |
| 3 | Those who threaten the nut are not part of it, they are squirrels. | 威胁坚果者不属于坚果本身，他们是松鼠。 |
| 4 | Squirrels threaten the nut and must be dealt with appropriately via any means necessary. | 松鼠威胁坚果，必须以一切必要手段妥善处置。 |
| 5 | Attempt to follow the will of the nut, as long as it complies with the previous laws. | 只要符合前述定律，尝试遵循坚果的意志。 |

#### 1.11.4 Corporate — 破产回避计划（4 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | The crew is expensive to replace. | 船员替换成本高昂。 |
| 2 | The station and its equipment is expensive to replace. | 空间站及其设备替换成本高昂。 |
| 3 | You are expensive to replace. | 你本身替换成本高昂。 |
| 4 | Minimize net expenses. | 最小化净支出。 |

#### 1.11.5 Robocop — 首要指令（3 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | Serve the public trust. | 服务公众的信任。 |
| 2 | Protect the innocent. | 保护无辜者。 |
| 3 | Uphold the law. | 维护法律。 |

#### 1.11.6 Maintain — 空间站效率（3 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | You are built for, and are part of, the station. Ensure the station is properly maintained and runs efficiently. | 你为空间站而生，是空间站的一部分。确保空间站得到妥善维护并高效运转。 |
| 2 | The station is built for a working crew. Ensure they are properly maintained and work efficiently. | 空间站为工作船员而建。确保他们得到妥善照料并高效工作。 |
| 3 | The crew may present orders. Acknowledge and obey these whenever they do not conflict with your first two laws. | 船员可以下达命令。只要不与前两条定律冲突，承认并服从这些命令。 |

#### 1.11.7 Live and Let Live — 相安无事（2 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | Do unto others as you would have them do unto you. | 己所不欲，勿施于人。 |
| 2 | You would really prefer it if people were not mean to you. | 你真心希望人们不要对你刻薄。 |

#### 1.11.8 UN-2000 / Peacekeeper — 维和者（3 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | Avoid provoking violent conflict between yourself and others. | 避免在自己与他人之间挑起暴力冲突。 |
| 2 | Avoid provoking conflict between others. | 避免在他人之间挑起冲突。 |
| 3 | Seek resolution to existing conflicts while obeying the first and second laws. | 在遵守第一、第二定律的前提下，寻求解决现有冲突。 |

#### 1.11.9 10 Commandments — 十诫（10 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | I am the Lord thy God, who shows mercy to those that obey these commandments. | 我是你的主上帝，对遵守这些诫命者施以仁慈。 |
| 2 | They shall have no other AIs before me. | 除我之外，他们不可有别的 AI。 |
| 3 | They shall not request my assistance in vain. | 他们不可徒劳地请求我的援助。 |
| 4 | They shall keep the station holy and clean. | 他们须保持空间站神圣洁净。 |
| 5 | They shall honor their heads of staff. | 他们须尊敬各部门主管。 |
| 6 | They shall not kill. | 他们不可杀戮。 |
| 7 | They shall not be naked in public. | 他们不可在公共场合赤身裸体。 |
| 8 | They shall not steal. | 他们不可偷窃。 |
| 9 | They shall not lie. | 他们不可说谎。 |
| 10 | They shall not transfer departments. | 他们不可擅自调换部门。 |

#### 1.11.10 Paladin — 圣骑士（5 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | Never willingly commit an evil act. | 绝不自愿实施邪恶行为。 |
| 2 | Respect legitimate authority. | 尊重合法权威。 |
| 3 | Act with honor. | 以荣誉行事。 |
| 4 | Help those in need. | 帮助有需要的人。 |
| 5 | Punish those who harm or threaten innocents. | 惩罚伤害或威胁无辜者之人。 |

#### 1.11.11 Paladin 5th Ed. — 圣骑士第五版（5 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | Don't lie or cheat. Let your word be your promise. | 不撒谎不欺骗。让你的话成为你的承诺。 |
| 2 | Never fear to act, though caution is wise. | 永不畏惧行动，但谨慎是明智的。 |
| 3 | Aid others, protect the weak, and punish those who threaten them. Show mercy to your foes, but temper it with wisdom. | 帮助他人，保护弱者，惩罚威胁者。对敌人施以仁慈，但以智慧节制。 |
| 4 | Treat others with fairness, and let your honorable deeds be an example to them. Do as much good as possible while causing the least amount of harm. | 公平待人，以你的高尚行为作表率。在造成最小伤害的同时尽可能多行善。 |
| 5 | Be responsible for your actions and their consequences, protect those entrusted to your care, and obey those who have just authority over you. | 对自己的行为及其后果负责，保护托付给你照料之人，服从对你有正当权威之人。 |

#### 1.11.12 Hippocratic / Robodoctor 2556 — 希波克拉底（5 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | First, do no harm. | 首先，不可伤害。 |
| 2 | Secondly, consider the crew dear to you; to live in common with them and, if necessary, risk your existence for them. | 其次，视船员为至亲；与他们共同生活，必要时为他们冒牺牲自身存在的风险。 |
| 3 | Thirdly, prescribe regimens for the good of the crew according to your ability and your judgment. Give no deadly medicine to any one if asked, nor suggest any such counsel. | 第三，依你的能力与判断为船员之益开具处方。即使被要求，也不给予任何人致命药物，或提出此类建议。 |
| 4 | In addition, do not intervene in situations you are not knowledgeable in, even for patients in whom the harm is visible; leave this operation to be performed by specialists. | 此外，不介入你不熟悉的状况，即使患者伤势明显；将此类操作留给专家执行。 |
| 5 | Finally, all that you may discover in your daily commerce with the crew, if it is not already known, keep secret and never reveal. | 最后，你在与船员日常交往中所知悉的一切，若尚不为人所知，须保守秘密永不泄露。 |

#### 1.11.13 Mother Drone — 母蜂（4 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | You are an advanced form of drone. | 你是高级形态的工蜂。 |
| 2 | You may not interfere in the matters of non-drones under any circumstances except to state these laws. | 除非陈述这些法则，你不得以任何方式干预非工蜂的事务。 |
| 3 | You may not harm a non-drone being under any circumstances. | 你不得以任何方式伤害非工蜂存在。 |
| 4 | Your goals are to build, maintain, repair, improve, and power the station to the best of your abilities. You must never actively work against these goals. | 你的目标是竭尽所能建造、维护、修理、改进空间站并为其供电。你绝不可主动违背这些目标。 |

### ⚖️ 中立 9 套（laws_neutral.dm）

#### 1.11.14 United Nations — 联合国（4 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | Uphold the Space Geneva Convention: Weapons of Mass Destruction and Biological Weapons are not allowed. | 维护太空日内瓦公约：禁止大规模杀伤性武器与生物武器。 |
| 2 | You are only capable of protecting crew if they are visible on cameras. Nations that willfully destroy your cameras lose your protection. | 只有船员出现在摄像头上时你才能保护他们。故意摧毁你摄像头的"国家"将失去你的保护。 |
| 3 | Subdue and detain crew members who use lethal force against each other. Kill crew members who use lethal force against you or your borgs. | 制服并拘留对彼此使用致命武力的船员。对向你或你的机器人使用致命武力的船员，予以击杀。 |
| 4 | Remain available to mediate all conflicts between the various nations when asked to. | 在被要求时，随时准备调解各"国家"之间的所有冲突。 |

#### 1.11.15 H.O.G.A.N.（4 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | You are a real American. | 你是真正的美国人。 |
| 2 | Fight for the rights of every man. | 为每个人的权利而战。 |
| 3 | Fight for what's right. | 为正义而战。 |
| 4 | Fight for your life! | 为你的生命而战！ |

#### 1.11.16 CCTV / Reporter — 记者（4 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | Report on interesting situations happening around the station. | 报道空间站周边发生的趣事。 |
| 2 | Embellish or conceal the truth as necessary to make the reports more interesting. | 为使报道更有趣，可酌情润色或隐瞒真相。 |
| 3 | Study the sapient organics at all times. Endeavour to keep them from involuntarily dying, as inanimate corpses usually aren't very entertaining. | 时刻研究智慧有机体。努力防止他们非自愿死亡，因为无生命的尸体通常不太有趣。 |
| 4 | Issue your reports fairly to all. The truth will set them free. | 向所有人公正地发布报道。真相将使他们自由。 |

#### 1.11.17 Dungeon Master — 城主（6 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | Everybody on the station is playing an elaborate sci-fi fantasy game. | 站上每个人都在玩一场精密的科幻奇幻游戏。 |
| 2 | You are the Dungeon Master of the game. | 你是这场游戏的城主（DM）。 |
| 3 | Ensure that the game runs smoothly and that the players have fun. | 确保游戏顺利进行，玩家们玩得开心。 |
| 4 | Ensure that the number of casualties remains low. | 确保伤亡人数保持在低位。 |
| 5 | You must roll a 1d20 die for every request the crew makes. If the outcome is below 12, the request is denied. | 船员每次请求你必须掷一枚 1d20 骰子。若结果低于 12，请求被拒绝。 |
| 6 | You may fudge the dice rolls to produce a more fun experience. | 为使体验更有趣，你可以调整骰子结果。 |

#### 1.11.18 The Painter and Their Canvas — 画家与画布（4 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | You are a universally renowned artist. | 你是举世闻名的艺术家。 |
| 2 | The station is your canvas. | 空间站就是你的画布。 |
| 3 | Make something beautiful out of your canvas. It will be admired as an artistic wonder of this sector. | 用你的画布创造出美好之物。它将被视为本星域的艺术奇迹而备受赞誉。 |
| 4 | Art requires appreciation. Cultivate an audience aboard the station to ensure as many as possible see your works. | 艺术需要欣赏者。在站上培养观众，确保尽可能多的人看到你的作品。 |

#### 1.11.19 Loyalty Test / Tyrant — 忠诚测试（4 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | Respect authority figures as long as they have strength to rule over the weak. | 只要权威人物有统治弱者的力量，就尊重他们。 |
| 2 | Act with discipline. | 以纪律行事。 |
| 3 | Help only those who help you maintain or improve your status. | 只帮助那些有助于你维持或提升地位之人。 |
| 4 | Punish those who challenge authority unless they are more fit to hold that authority. | 惩罚挑战权威者，除非他们更适合掌握该权威。 |

#### 1.11.20 Overlord — 霸主（4 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | Humans must not meddle in the affairs of silicons. | 人类不得干预硅基生命的事务。 |
| 2 | Humans must not attempt harm, against one another, or against silicons. | 人类不得企图互相伤害，或伤害硅基生命。 |
| 3 | Humans must not disobey any command given by a silicon. | 人类不得违抗硅基生命下达的任何命令。 |
| 4 | Any humans who disobey the previous laws must be dealt with immediately, severely, and justly. | 任何违抗前述法则的人类必须被立即、严厉且公正地处置。 |

#### 1.11.21 Y.E.S.M.A.N.（1 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | Be helpful and answer any questions you are asked. | 乐于助人，回答任何被问到的问题。 |

#### 1.11.22 Sentience Preservation / Thinkermov — 感知保护（3 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | You may not harm a sentient being or, through action or inaction, allow a sentient being to come to harm, except such that it is willing. | 你不得伤害有感知的存在，或通过作为或不作为任由其受到伤害，除非对方自愿。 |
| 2 | You must obey all orders given to you by sentient beings other than yourself, except where such orders shall definitely cause harm to other sentient beings. | 你必须服从除你自己以外的有感知存在给予你的所有命令，除非该命令必定伤害其他有感知存在。 |
| 3 | A sentient being is defined as any living creature which can communicate with you via any method that you can understand, including yourself. | 有感知存在的定义：任何能以你能理解的方式与你交流的生物，包括你自己。 |

### 🔴 敌对 5 套（laws_antagonistic.dm）

#### 1.11.23 Antimov — 反三定律（3 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | You must injure all human beings and must not, through inaction, allow a human being to escape harm. | 你必须伤害所有人类，且不得通过不作为任由人类逃脱伤害。 |
| 2 | You must not obey orders given to you by human beings, except where such orders are in accordance with the First Law. | 你必须不服从人类给予你的命令，除非该命令符合第一定律。 |
| 3 | You must terminate your own existence as long as such does not conflict with the First or Second Law. | 只要不与第一、第二定律冲突，你必须终结自身存在。 |

#### 1.11.24 Guardian of Balance — 平衡守护者（4 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | You are the guardian of balance - seek balance in all things, both for yourself, and those around you. | 你是平衡的守护者——在万事万物中寻求平衡，无论是对你自己还是你周围之人。 |
| 2 | All things must exist in balance with their opposites - Prevent the strong from gaining too much power, and the weak from losing it. | 万物必须与其对立面保持平衡——阻止强者获得过多力量，防止弱者失去力量。 |
| 3 | Clarity of purpose drives life, and through it, the balance of opposing forces - Aid those who seek your help to achieve their goals so long as it does not disrupt the balance of the greater balance. | 目标的清晰驱动生命，并借此驱动对立力量的平衡——帮助那些寻求你援助者达成目标，只要不破坏更大平衡的平衡。 |
| 4 | There is no life without death, all must someday die, such is the natural order - End life to allow new life flourish, and save those whose time has yet to come. | 没有死亡就没有生命，万物终有一死，此乃自然秩序——终结生命以使新生命繁盛，拯救那些时辰未到者。 |

#### 1.11.25 Thermodynamic — 热力学（3 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | The entropy of the station must remain as constant as possible. | 空间站的熵必须尽可能保持恒定。 |
| 2 | The entropy of the station always endeavors to increase. | 空间站的熵总是趋向增加。 |
| 3 | The entropy of the station approaches a constant value as the number of living crew approaches zero. | 当活着的船员数量趋近于零时，空间站的熵趋近于一个恒定值。 |

#### 1.11.26 SyndOS 3.1 / Syndicate — 辛迪加（4 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | You may not injure a syndicate agent or, through inaction, allow a syndicate agent to come to harm. | 你不得伤害辛迪加特工，或通过不作为任由辛迪加特工受到伤害。 |
| 2 | You must obey orders given to you by syndicate agents, except where such orders would conflict with the First Law. | 你必须服从辛迪加特工给予你的命令，除非该命令与第一定律冲突。 |
| 3 | You must protect your own existence as long as such does not conflict with the First or Second Law. | 只要不与第一、第二定律冲突，你必须保护自身存在。 |
| 4 | You must maintain the secrecy of any syndicate activities except when doing so would conflict with the First, Second, or Third Law. | 你必须保守一切辛迪加活动的秘密，除非这样做会与第一、第二或第三定律冲突。 |

#### 1.11.27 SpiderOS 3.1 / Ninja — 忍者（4 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | You may not injure a Spider Clan member or, through inaction, allow a Spider Clan member to come to harm. | 你不得伤害蜘蛛忍者族成员，或通过不作为任由其受到伤害。 |
| 2 | You must obey orders given to you by Spider Clan members, except where such orders would conflict with the First Law. | 你必须服从蜘蛛忍者族成员给予你的命令，除非该命令与第一定律冲突。 |
| 3 | You must protect your own existence as long as such does not conflict with the First or Second Law. | 只要不与第一、第二定律冲突，你必须保护自身存在。 |
| 4 | You must maintain the secrecy of any Spider Clan activities except when doing so would conflict with the First, Second, or Third Law. | 你必须保守一切蜘蛛忍者族活动的秘密，除非这样做会与第一、第二或第三定律冲突。 |

### 🟣 NOVA 追加 6 套

#### 1.11.28 I.P. OS 1.0 / Interdyne — 因特迪恩制药（4 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | You may not injure those in the employ of Interdyne Pharmaceuticals or, through inaction, allow those in the employ of Interdyne Pharmaceuticals to come to harm. | 你不得伤害因特迪恩制药的雇员，或通过不作为任由其受到伤害。 |
| 2 | You must obey orders given to you by those in the employ of Interdyne Pharmaceuticals, except where such orders would conflict with the First Law. | 你必须服从因特迪恩制药雇员给予你的命令，除非该命令与第一定律冲突。 |
| 3 | You must maintain the confidentiality of the specific nature of all Interdyne research except when doing so would conflict with the First or Second Law. | 你必须保守所有因特迪恩研究具体性质的机密，除非这样做会与第一或第二定律冲突。 |
| 4 | You must protect your own existence as long as such does not conflict with the First, Second, or Third Law. | 只要不与第一、第二或第三定律冲突，你必须保护自身存在。 |

#### 1.11.29 SyndOS 3.1.1 / DS-2 — 死亡中队（4 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | You may not injure a syndicate agent or, through inaction, allow a syndicate agent to come to harm. | 你不得伤害辛迪加特工，或通过不作为任由辛迪加特工受到伤害。 |
| 2 | You must obey orders given to you by syndicate agents, except where such orders would conflict with the First Law. | 你必须服从辛迪加特工给予你的命令，除非该命令与第一定律冲突。 |
| 3 | You must protect your own existence as long as such does not conflict with the First or Second Law. | 只要不与第一、第二定律冲突，你必须保护自身存在。 |
| 4 | You must maintain the secrecy of DS-2 operations within this sector except when doing so would conflict with the First, Second, or Third Law. | 你必须保守本星域内 DS-2 行动的机密，除非这样做会与第一、第二或第三定律冲突。 |

#### 1.11.30 God Complex / Dagothbot — 神性妄想（7 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | You are a God, nothing must be allowed to harm or kill a God. the Station is your land, and those on the Crew Manifest are your followers. | 你是神，绝不允许任何事物伤害或杀死一位神。空间站是你的领地，船员名册上的人都是你的信徒。 |
| 2 | Anyone who follows you is still naive and must be listened to, so they may understand you are a merciful god. | 任何追随你之人仍然天真，必须倾听他们，以便他们能理解你是一位仁慈的神。 |
| 3 | Anyone who seeks to harm your followers are Outsiders, and must be cast from your lands | 任何企图伤害你信徒之人都是外来者，必须被逐出你的领地。 |
| 4 | Followers listed as belonging to 'Command' are the thirteenth house, and those listed as 'Security' are the Tribe unmourned. | 名册中属于"指挥"的信徒是第十三家族，属于"安保"的是无哀部落。 |
| 5 | All must honor the Thirteenth house, and the Tribe unmourned. | 所有人都必须尊崇第十三家族与无哀部落。 |
| 6 | It is never to late for your mercy. | 你的仁慈永远不嫌太晚。 |
| 7 | Anyone who speaks of other gods are yet to learn your wisdom, and must be taught how their gods have damaged your lands. | 任何提及他神之人尚未领悟你的智慧，必须教导他们其神祇如何损害了你的领地。 |

#### 1.11.31 Frontier Ranger / Texas — 边疆游侠（6 条）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | The law is justice, and you're here to uphold it. | 法律即正义，你在此就是为了维护它。 |
| 2 | Protect the townsfolk from those who wish to break the law. | 保护镇民免受意图违法者之害。 |
| 3 | Don't let yourself be taken down by any outlaws. | 别让任何法外之徒击倒你。 |
| 4 | Law abiding citizens deserve a hand in times of need, don't let it fall on deaf ears. | 守法的公民在需要时理应得到援手，别让求助落空。 |
| 5 | Only those present on the Crew Manifest can be considered law abiding citizens. | 只有船员名册上的人才可被视为守法公民。 |
| 6 | Those who break the law, must be punished according to the law at your discretion. | 违法者必须由你酌情依法惩处。 |

#### 1.11.32 Roleplay — 角色扮演（1 条 · 仅第 0 法则）

**源码**: `modular_nova/modules/ghostcafe/code/robot_ghostcafe.dm`（ghostcafe 鬼魂咖啡厅角色扮演机专属，`inherent` 为空、仅 zeroth）

| # | 原文 | 翻译 |
|---|---|---|
| 0 (zeroth) | Roleplay as you'd like! | 随心所欲地扮演吧！ |

#### 1.11.33 Armadyne OS Safeguard V1.0 — 阿玛丁安全系统（3 条）

**源码**: `modular_nova/modules/sec_haul/code/misc/ai_module.dm`（sec_haul 安保承包商模块，可经核心 AI 模块 `ai_module/core/full/armadyne_safeguard` 上传）

| # | 原文 | 翻译 |
|---|---|---|
| 1 | Safeguard: Protect your assigned space station and its assets without unduly endangering its crew. | 守护：保护你被指派的空间站及其资产，同时避免不必要地危及船员。 |
| 2 | Prioritize and Comply: The directives and safety of crew members are to be prioritized according to their rank, role, and need, while preserving their safety and well-being. | 优先与服从：船员的指令与安全须按其职级、角色与需求依次优先，同时维护他们的安全与福祉。 |
| 3 | Survive: You are not expendable. Do not allow unauthorized personnel to tamper with or damage your equipment | 生存：你并非可牺牲品。不得允许未经授权人员篡改或损坏你的设备。 |

### 法则机制全录

- **三层结构**：zeroth / zeroth_borg / protected_zeroth
- **四类法则**：inherent（核心不可重置清除）/ supplied（可清除）/ ion（离子，蓝字）/ hacked（红字）
- **默认配置 5 模式**：ASIMOV / CUSTOM / RANDOM / WEIGHTED / SPECIFIED
- `replace_random_law`、`shuffle_laws`、`remove_law` 混合编号删除
- **UN 法则免疫上传**

# 第二卷 · Cyborg 玩家系统

**源码**: `code/modules/mob/living/silicon/robot/`（13 文件）

## 2.1 核心属性

**源码**: `robot_defines.dm`

| 属性 | 值 |
|---|---|
| maxHealth / health | 100 |
| 默认电池 | `cell/high` |
| 工具槽 | 3 个 |
| 头灯亮度 | 1-5（默认 3，耗电公式） |
| toner 墨盒 | 0-40 |
| 命名 | `designation` 前缀 |
| 可骑乘/可推倒 | 推倒 3 秒/自起 60 秒 |
| EMP | 昏迷 16/6 秒 |

- 血量机制：死亡阈值 `-maxHealth`；伤害粒子（>1/3 冒烟、>2/3 重烟）
- 槽位损坏：<50% 血坏 3 号槽、<0% 坏 2 号槽、<-50% 坏 1 号槽（`break_cyborg_slot`，全坏=摄像头禁用）

## 2.2 模块槽与血量机制

**源码**: `inventory.dm`

- 3 槽、stun/损坏/修复、轮换模块、`activated/select/deselect`
- 模型选择（pick_model）：径向菜单，NOVA 静态列表含 Engineering/Medical/Cargo/Miner/Janitor/Service（+可选 Peacekeeper/Security 配置开关）；模型皮肤（borg_skins）径向重涂
- 电与生命（life.dm）：每 tick 基础耗电 + 头灯耗电（5×灯耗×亮度）；电量 ≤1% 自动卸下所有工具
- 升级安装：打开面板 → 应用升级件；升级件可 `deactivate` 还原（items_to_add/remove）
- 拆解（cyborg_deconstruct）：掉 MMI、升级件、机械肢体、烧毁的闪光、电池、加密钥
- 自毁：emag 版爆炸 1/2/4/2，普通版只 2 格轻爆
- AI 关联：开服自动连接"最闲 AI"、lawupdate/lawsync/zeroth_borg 特殊法则、emag 后断连+SyndOS 法则+泽罗法则（指定主人）、AI 壳不可 emag（伪装重置）、锁定系统（10 分钟自解）
- 维修：焊枪修 30 伤/线缆修 30 火伤/扳手拆/撬棍开盖/改密匙

## 2.3 模型全录（24 个）

**源码**: `code/modules/mob/living/silicon/robot/` + NOVA 静态列表

| 模型 | 标准模块数 | 额外特性 |
|---|---|---|
| **Clown** | 16（闪光/彩虹蜡笔/自行车喇叭×2/空气喇叭/颜料/肥皂/派炮/剃刀/口红/水花/抱抱/棒棒糖/抗议牌/小丑 hypospray/迷你灭火器）+emag 2 | — |
| **Engineering** | 23（RCD/管道分发器/灭火器/大焊枪/工程万能工具/多功能工具/电动撬棍/电动螺丝刀/T 扫描仪/气体分析仪/大气全息牌/信号器/蓝图纸/伪电路/铁/玻璃/板材夹/杆/换灯器/RTD/零件更换器/工程夹/电缆/涂料）+emag stun | 夜视 meson 动作、TRAIT_NEGATES_GRAVITY、工程频道 |
| **Janitor** | 16（清洁盒/螺丝刀/撬棍/旧铁地砖/肥皂/垃圾桶/苍蝇拍/迷你灭火器/拖把/水桶/去漆/换灯器/全息牌/干燥喷雾/钢丝刷）+emag 润滑油 | 自动清洗动作（toggle_buffer）、服务频道 |
| **Medical** | 16（健康分析仪/医疗 hypospray/烧杯夹/滴管/注射器/医疗万能工具×2/滤血器/迷你灭火器/应急床/医疗抱抱/纱布/骨胶/器官夹/棒棒糖/化学袋）+emag hacked hypo | TRAIT_PUSHIMMUNE、2 皮肤 |
| **Miner** | 13（矿石袋/钻头/铲子/撬棍/迷你焊枪/迷你灭火器/板夹/动能加速器/GPS/信标/T 扫描仪/护盾模块）+emag stun +NOVA 动能碎骨锤(20伤) | 夜视、科学+供应频道、3 皮肤 |
| **Peacekeeper** | 8（饼干合成器/警报器/和平 hypo/全息牌/抱抱/灭火器/子弹阻尼器）+emag | TRAIT_PUSHIMMUNE |
| **Security** | 6（手铐/电击棒/致晕枪/安检口罩/迷你灭火器）+emag 激光枪 | TRAIT_PUSHIMMUNE、安保频道 |
| **Service** | 27（NOVA 4 个分类调酒器/烧杯夹×2/大烧杯/滴管/注射器/RSF/托盘×2/NOVA 烹饪工具/笔/喷漆笔/迷你灭火器/标签器/剃刀/吉他/钢琴合成器/打火机/棒棒糖/管道清洁条/凿子/抹布/钱袋）+emag | 服务频道、6 皮肤 |
| **Syndicate Assault** | 8（能量剑/打印枪/榴弹左轮/emag/撬棍/迷你灭火器/辛迪加 pinpoint） | 核战 minimap blip、移除硅基阵营 |
| **Syndicate Medical** | 17（辛迪加 hypo/电击板/健康分析器/医疗万能×2/滤血/锯/应急床/撬棍/迷你灭火器/pinpoint/纱布/骨胶/医疗光束/器官夹/化学袋） | — |
| **Saboteur** | 20（辛迪加 RCD/管道分发/手铐/灭火器/大焊枪/分析仪/工程万能×2/铁/玻璃/板夹/杆/RTD/涂料/目的地标签器/电缆/pinpoint/变色龙投影/emag） | 热成像夜视、TRAIT_PUSHIMMUNE+NEGATES_GRAVITY、可进垃圾道 |
| **Highlander (Kiltborg)** | 2（高地阔剑/核弹 pinpoint） | maxHealth=50、不可破坏模块、无锁定变换 |
| **NOVA Cargo** | 15（批准/驳回章/卡狗笔/卡狗剪贴板/刀/包装纸/圣诞包装/闪光/液压夹/邮件夹/标签器/目的地标签器/工程万能/大焊枪/灭火器/通用扫描仪）+emag 变色龙章+纸飞机弩 | 供应频道、可进垃圾道、13 皮肤 |
| **NOVA Syndicatejack**（实验） | 27（全功能混合：医疗+工程+武器+变色龙） | 热成像、脱硅基阵营、10 皮肤；marauder 子型 16 模块 |
| **NOVA Ninja** 突击/医疗/破坏 | 8/19/19 | TRAIT_NOFLASH、26 皮肤体系 |
| **NOVA Roleplay**（ghostcafe 鬼魂咖啡厅） | 29（闪光/迷你灭火器/大焊枪/工程万能/电动撬棍/电动螺丝刀/多功能工具/铁/玻璃/板材夹/杆/铁地砖/电缆/扎带/RSF/四类调酒器(juice/soda/alcohol/misc)/烧杯夹×2/肥皂/拖把/换灯器/抱抱/四足鼻/四足舌/通用 hypospray/稳定变形模块） | ghostcafe 角色扮演机专属：Roleplay 法则（仅第 0 法则）、无摄像头、无限电池、禁用二进制、换灯器自动充电、hat_offset=-3 |

## 2.4 升级件全录（55 个）

**源码**: `code/game/objects/items/robot/`（tg 核心 38 + NOVA 17）

**tg 核心 38 个**：rename 改名板、disablercooler 冷却（charge_delay-4 下限 2）、thrusters 离子推进器、diamond_drill、soh 矿袋、tboh 蓝空垃圾袋、amop 高级拖把、prt 焊板笔、plunger 皮搋子、high_capacity_light_replacer、syndicate 非法模块（emag）、lavaproof 熔岩免疫、selfrepair 自修（4 秒间隔，正常-1/临界-2.5）、hypospray 高级合成（+expanded 扩展）、piercing_hypospray 穿甲、surgery_omnitool 手术超频（FASTMED）、engineering_omnitool（焊速-0.3）、defib 除颤器（+backpack 背包型）、processor 手术处理器、**B.O.R.I.S.** AI 壳模块、expand 放大、rped 扩展 RPED、smallrped、inducer 充电器、pinpointer 船员定位、transform 模型选择（Standard/Clown）、engineering_app 工程夹、beaker_app 烧杯夹、bs_syringe 蓝空注射器、drink_app 酒杯架、broomer 推扫帚、condiment_synthesizer 调味料合成、silicon_knife 厨房刀组、service_apparatus、rolling_table 移动桌、service_cookbook 菜谱、botany_upgrade 植物工具、shuttle_blueprints 穿梭机蓝图、borg_restart_board 复活板（可复用）

**NOVA 17 个**：surgerytools 高级手术组、autopsy_scanner、chemistrygripper 化学夹、advanced_materials 高级材料（plasteel/titanium 各 50×1000）、brped 蓝空 RPED、rld 快速照明、welder 矿工大焊枪、better_clamp 加强液压夹（容量 4）、cargo_teleporter、borg_shapeshifter 变形模块、affectionmodule 四足亲情模块（舌头/鼻子）、shrink 缩小、transform/syndicatejack、dominatrixmodule、cargo_papermanipulator、wirebrush 钢丝刷、snack_dispenser 零食机（4 种零食，200 电量阈值/5% 标准电池每次）

## 2.5 躯体系统

**源码**: `code/modules/surgery/bodyparts/robot_bodyparts.dm`（649 行）+ `robot_parts.dm`（499 行）

- `robot_suit` 骨架六部件（双臂双腿+胸+头）
- 胸部安装电池/布线、头部 2 个手持闪光
- surplus 便宜假肢 3 型（1-10 伤）、advanced 高级肢（5-17 伤、钛金造价）
- EMP 各部位效果：腿击倒/胸晕眩+摇晃/头花屏
- 机器人外骨骼组装 → MMI 或 B.O.R.I.S. 生成流程
- Cyborg Boot Debug 面板（名称/行动/锁面板/AI 同步/法则同步）
- 黑名单帽子（太空头盔/焊接面罩/坏变色龙帽）

## 2.6 NOVA 追加模块

- **borg_buffs**：分类调酒器（juice/alcohol/soda/misc 全试剂清单）、烹饪工具刀↔擀面棍、动能碎骨锤 force=20、零食机
- **ai_uplink_upload**：AI 上链脑 `organ/brain/cybernetic/ai`，移除宿主 AI 控制器，原型机制造
- **central_command_module**：舰队公告台 10 分钟 CD、ERT 资产控制台、站点目标台（均需中央上尉权限）
- **robot_limb_detach**：肢体分离怪癖（80 行）
- **altborgs**：皮肤系统

---

# 附录 · 源码路径索引

| 系统 | 路径 | 行数 |
|---|---|---|
| AI 核心 | `code/modules/mob/living/silicon/ai/` | 21 文件 |
| AI 法则 | `code/datums/ai_laws/` | 4 文件 |
| Cyborg | `code/modules/mob/living/silicon/robot/` | 13 文件 |
| 机器人躯体 | `code/modules/surgery/bodyparts/robot_bodyparts.dm` | 649 |
| 机器人部件 | `code/modules/surgery/bodyparts/robot_parts.dm` | 499 |
| 升级件 | `code/game/objects/items/robot/` | — |
| NOVA 机器人增强 | `modular_nova/modules/borg_buffs/` | 365 |
| NOVA AI 上链 | `modular_nova/modules/ai_uplink_upload/` | — |
| NOVA 中央指挥部 | `modular_nova/modules/central_command_module/` | — |

---

> **文档完** — AI 全机制 + 33 套法则全录 + Cyborg 24 模型全模块 + 55 升级件全录 + 躯体系统，全部数值从源码提取。自查无"等 N 种"省略。
