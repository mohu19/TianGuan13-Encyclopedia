# TianGuan13 成就与记录百科 / Achievements & Records Complete Encyclopedia

> **版本 / Version**：NovaSector 分支（TianGuan13-master）
> **数据来源 / Data source**：源码逐文件提取，非文档转述。成就数值、ID、描述均直接取自 DM 源码。
> **覆盖范围 / Scope**：成就系统（95 项成就 + 21 项积分榜/进度）、记录系统（犯罪/医疗/名册/记录）、投票系统（4 种投票 + 子系统机制）、NOVA 追加模块（event_awards / character_directory / records_on_examine / autotransfer）、物理勋章系统。

---

## 目录 / Table of Contents

- [一、成就系统总览 / Achievement System Overview](#一成就系统总览--achievement-system-overview)
- [二、成就全录：BOSS 成就（22 项） / Boss Achievements](#二成就全录boss-成就22-项--boss-achievements)
- [三、成就全录：职业成就（9 项） / Job Achievements](#三成就全录职业成就9-项--job-achievements)
- [四、成就全录：技能成就（2 项） / Skill Achievements](#四成就全录技能成就2-项--skill-achievements)
- [五、成就全录：杂项成就（43 项） / Misc Achievements](#五成就全录杂项成就43-项--misc-achievements)
- [六、成就全录：黑手党成就（19 项） / Mafia Achievements](#六成就全录黑手党成就19-项--mafia-achievements)
- [七、积分榜与进度系统（21 项） / Scores & Progress](#七积分榜与进度系统21-项--scores--progress)
- [八、记录系统 / Record System](#八记录系统--record-system)
- [九、投票系统 / Vote System](#九投票系统--vote-system)
- [十、NOVA 追加模块 / NOVA Additions](#十nova-追加模块--nova-additions)
- [十一、物理勋章与嘉奖 / Physical Medals & Commendations](#十一物理勋章与嘉奖--physical-medals--commendations)
- [十二、源码路径索引 / Source Path Index](#十二源码路径索引--source-path-index)

---

## 一、成就系统总览 / Achievement System Overview

成就系统由 `SSachievements`（`/datum/controller/subsystem/achievements`）驱动，所有数据持久化于数据库表 `achievements` 与 `achievement_metadata`。玩家通过 `client.give_award(成就类型, mob, value)` 解锁成就或累加积分。

**核心数据结构 / Core datums**：

| Datum | 用途 / Purpose |
|---|---|
| `/datum/achievement_data` | 每个 ckey 一份的成就数据持有者，负责从数据库加载/保存（`code/datums/achievements/_achievement_data.dm`） |
| `/datum/award` | 所有奖励的抽象基类（`code/datums/achievements/_awards.dm`） |
| `/datum/award/achievement` | 一次性成就（解锁后 `data[type] = TRUE`，不会重复触发） |
| `/datum/award/score` | 可累加的积分榜（每次解锁 `data[type] += value`，前 50 名进入排行榜） |
| `/datum/award/score/progress` | 收集类进度（钓鱼图鉴 Fishdex、PDA 主题），独立数据库表存储条目 |

**关键机制 / Key mechanics**：

- **分类 / Categories**：UI 与单元测试使用的固定分类列表：`"Bosses", "Jobs", "Skills", "Misc", "Mafia", "Scores"`（`code/_globalvars/lists/achievements.dm`）。
- **Medal 奖励 / Medal reward**：本系统的"奖章"即成就本身——每个成就的 `database_id` 是历史遗留的 BYOND 奖章中心（medal hub）ID（如 `BOSS_MEDAL_BUBBLEGUM`），同时写入数据库 `achievement_metadata` 表。另有独立的**物理勋章物品**（可佩戴在制服上的勋章，见第十一章）。
- **解锁广播 / Unlock feedback**：解锁成就时全频道广播 `span_greenannounce`，按玩家偏好播放音效（`GLOB.achievement_sounds`：Success Ping / Beeps Jingle / Tada Fanfare，音量 70/70/30），并生成 `/datum/achievement_report` 记入 `GLOB.achievements_unlocked`（回合结束播报"Total Achievements Earned"）。
- **成就之声偏好 / Sound preference**：`/datum/preference/choiced/sound_achievement`，选项：Tada Fanfare、Beeps Jingle、Success Ping、Disabled。
- **进度类特殊反馈**：进度类解锁时播放半音量成就音效，并提示"New progress made"。
- **管理员面板 / Admin panel**：`ADMIN_VERB(achievements_cleanup, R_ADMIN, "成就管理面板", ...)` 打开 `/datum/achievement_admin_panel`，可归档（archive，版本号置为 `ACHIEVEMENT_ARCHIVED_VERSION=9999`）或彻底清除（cleanup_orphan）孤儿成就元数据。
- **玩家查看 / Player verb**：`checkachievements()`，OOC 分类，命令名"查看成就"，打开 TGUI `Achievements` 界面。
- **UI 统计 / UI stats**：成就浏览器显示"Unlocked by N players so far"（当前被多少玩家解锁）、与最高解锁成就的百分比对比；`most_unlocked_achievement` 记录全场解锁人数最多的成就。
- **特殊解锁**：`/datum/award/score/achievements_score`（"Achievements Unlocked"）是第一个积分项，统计玩家解锁的成就总数，按 ckey 分组排行。
- **难度说明 / Difficulty note**：源码中没有显式难度字段；本百科中"难度"列为编者按解锁条件推断的分级（★ 简单 / ★★ 中等 / ★★★ 困难 / ★★★★ 极难），仅供参考。
- **数据库版本 / DB version**：`achievement_version = 2`；`ACHIEVEMENT_ARCHIVED_VERSION 9999` 标记已归档。
- **禁用开关 / Killswitch**：`SSachievements.achievements_enabled`，数据库未连接时整个系统不启用（`SS_INIT_NO_NEED`）。

**数量统计 / Count**：成就 **95** 项（BOSS 22 + 职业 9 + 技能 2 + 杂项 43 + 黑手党 19），积分/进度 **21** 项（BOSS 积分 12 + 职业积分 2 + 杂项积分 4 + 进度 2 + 成就数积分 1），合计 **116** 项奖励。

---

## 二、成就全录：BOSS 成就（22 项） / Boss Achievements

> 源码：`code/datums/achievements/boss_achievements.dm`（135 行）
> 机制：大部分通过 `/datum/element/kill_achievement` 附加在巨型 fauna 上——死亡时向 **12 格范围内**（`achievement_range`，部分为 7）所有存活的玩家发放击杀成就；若 BOSS 所受伤害 ≥60% 来自动能粉碎器（kinetic crusher）且击杀者手持 `kinetic_crusher`，则发放对应的 Crusher 成就。管理端 spawn 的 BOSS 不计数。击杀同时通过 `SSblackbox.record_feedback` 计入 `megafauna_kills` 统计。
> 数据表：`code/datums/elements/kill_achievement.dm`、`code/modules/mob/living/basic/boss/boss.dm`、`code/modules/mob/living/simple_animal/hostile/megafauna/_megafauna.dm`、`legion.dm`
> 难度：击杀类 ★★★，粉碎器类 ★★★★（要求 60% 以上粉碎器伤害）。

| # | 成就 / Achievement | 描述 / Description | 解锁条件 / Unlock | Medal ID（database_id） | 图标 |
|---|---|---|---|---|---|
| 1 | **触手清除者 / Tendril Exterminator** | "Watch your step" / 小心脚下 | 摧毁一个拉瓦兰德触手核心（tendril） | `BOSS_MEDAL_TENDRIL`（"Tendril Exterminator"） | tendril |
| 2 | **BOSS 杀手 / Boss Killer** | "You've come a long ways from asking how to switch hands." / 从连怎么换手都要问一路走到今天 | 击杀任意 BOSS（附加在 basic boss 通用型上） | `"Boss Killer"`（`BOSS_MEDAL_ANY`） | firstboss |
| 3 | **血醉矿工杀手 / Blood-Drunk Miner Killer** | "I guess he couldn't handle his drink that well." / 看来他酒量不行 | 击杀血醉矿工（Blood-Drunk Miner） | `BOSS_MEDAL_MINER` | miner |
| 4 | **恶魔冰霜矿工杀手 / Demonic-Frost Miner Killer** | "Definitely harder than the Blood-Drunk Miner." / 绝对比血醉矿工难 | 击杀恶魔冰霜矿工（Demonic-Frost Miner） | `BOSS_MEDAL_FROSTMINER` | frostminer |
| 5 | **泡泡糖杀手 / Bubblegum Killer** | "I guess he wasn't made of candy after all" / 看来他不是糖做的 | 击杀泡泡糖（Bubblegum） | `BOSS_MEDAL_BUBBLEGUM` | bbgum |
| 6 | **巨像杀手 / Colossus Killer** | "The bigger they are... the better the loot" / 越大个…战利品越好 | 击杀巨像（Colossus） | `BOSS_MEDAL_COLOSSUS` | colossus |
| 7 | **龙兽杀手 / Drake Killer** | "Now I can wear Rune Platebodies!" / 现在我能穿符文板甲了 | 击杀龙兽（Drake） | `BOSS_MEDAL_DRAKE` | drake |
| 8 | **圣者杀手 / Hierophant Killer** | "Hierophant, but not triumphant." / 圣者，但不凯旋 | 击杀圣者（Hierophant） | `BOSS_MEDAL_HIEROPHANT` | hierophant |
| 9 | **军团杀手 / Legion Killer** | "We were many..now we are none." / 我们曾是很多…现在一个不剩 | 击杀军团（Legion） | `BOSS_MEDAL_LEGION` | legion |
| 10 | **温迪戈杀手 / Wendigo Killer** | "You've now ruined years of mythical storytelling." / 你毁掉了多年的神话传说 | 击杀温迪戈（Wendigo） | `BOSS_MEDAL_WENDIGO` | wendigo |
| 11 | **异形杀手 / Thing Killer** | "Thank goodness it was not infectious. Probably." / 谢天谢地它不传染。大概吧 | 击杀异形（The Thing） | `BOSS_MEDAL_THETHING`（"Thing Exterminator"） | thething |
| 12 | **血醉矿工粉碎者 / Blood-Drunk Miner Crusher** | "I guess he couldn't handle his drink that well." / 看来他酒量不行 | 用动能粉碎器击杀血醉矿工 | `BOSS_MEDAL_MINER_CRUSHER` | miner |
| 13 | **恶魔冰霜矿工粉碎者 / Demonic-Frost Miner Crusher** | "Definitely harder than the Blood-Drunk Miner." / 绝对比血醉矿工难 | 用动能粉碎器击杀恶魔冰霜矿工 | `BOSS_MEDAL_FROSTMINER_CRUSHER` | frostminer |
| 14 | **泡泡糖粉碎者 / Bubblegum Crusher** | "I guess he wasn't made of candy after all" / 看来他不是糖做的 | 用动能粉碎器击杀泡泡糖 | `BOSS_MEDAL_BUBBLEGUM_CRUSHER` | bbgum |
| 15 | **巨像粉碎者 / Colossus Crusher** | "The bigger they are... the better the loot" / 越大个…战利品越好 | 用动能粉碎器击杀巨像 | `BOSS_MEDAL_COLOSSUS_CRUSHER` | colossus |
| 16 | **龙兽粉碎者 / Drake Crusher** | "Now I can wear Rune Platebodies!" / 现在我能穿符文板甲了 | 用动能粉碎器击杀龙兽 | `BOSS_MEDAL_DRAKE_CRUSHER` | drake |
| 17 | **圣者粉碎者 / Hierophant Crusher** | "Hierophant, but not triumphant." / 圣者，但不凯旋 | 用动能粉碎器击杀圣者 | `BOSS_MEDAL_HIEROPHANT_CRUSHER` | hierophant |
| 18 | **军团粉碎者 / Legion Crusher** | "We were many... now we are none." / 我们曾是很多…现在一个不剩 | 用动能粉碎器击杀军团 | `BOSS_MEDAL_LEGION_CRUSHER` | legion |
| 19 | **温迪戈粉碎者 / Wendigo Crusher** | "You've now ruined years of mythical storytelling." / 你毁掉了多年的神话传说 | 用动能粉碎器击杀温迪戈 | `BOSS_MEDAL_WENDIGO_CRUSHER` | wendigo |
| 20 | **异形粉碎者 / Thing Crusher** | "MacReady would be proud." / 麦克雷迪会为你骄傲 | 用动能粉碎器击杀异形 | `BOSS_MEDAL_THETHING_CRUSHER` | thething |
| 21 | **山羊王杀手 / King Goat Killer** | "The king is dead, long live the king!" / 国王已死，国王万岁！ | 击杀山羊王（King Goat，源码注释"should be removed soon"即将移除） | `BOSS_MEDAL_KINGGOAT` | goatboss |
| 22 | **山羊王粉碎者 / King Goat Crusher** | "The king is dead, long live the king!" / 国王已死，国王万岁！ | 用动能粉碎器击杀山羊王 | `BOSS_MEDAL_KINGGOAT_CRUSHER` | goatboss |

---

## 三、成就全录：职业成就（9 项） / Job Achievements

> 源码：`code/datums/achievements/job_achievements.dm`（69 行）
> 注：源码注释「civilian achievies! while not recognized by the code, it is recognized by our hearts」（平民成就，代码不承认但我们的心承认）——无平民成就条目。

| # | 成就 / Achievement | 描述 / Description | 解锁条件 / Unlock | Medal ID（database_id） | 图标 |
|---|---|---|---|---|---|
| 1 | **一切尽在理论极限内 / All Within Theoretical Limits** | "I never thought I'd see a resonance cascade, let alone prevent one..." / 我从没想过会亲眼见到共振级联，更别说阻止它了 | 工程：阻止超物质（Supermatter）共振级联/拯救超物质（`supermatter.dm:363` savior 判定） | `MEDAL_THEORETICAL_LIMITS` | theoreticallimits |
| 2 | **睡神先生 / Mister Sandman** | "Mechanically speaking, there's no real benefit to being unconscious during surgery. Weird how insistent this doctor is about using the N2O anyway though, huh?" / 从机制上讲，手术时昏迷没有任何好处。但这位医生怎么就这么执着于用笑气呢？ | 医疗：在病人于麻醉（N2O 睡眠）状态下完成手术（手术结束且 `patient.stat == DEAD && was_sleeping`，`_operation.dm:953`） | `MEDAL_SANDMAN` | basemisc |
| 3 | **Helbitaljanken（生死拳）** | "You janked hard" / 你狠狠赌了一把 | 医疗：Helbital 药物过量触发的"与死神猜拳"小游戏（rock paper scissors with the grim reaper）中获胜（1/3 概率），获胜后复活（`cat2_medicine_reagents.dm:81`） | `MEDAL_HELBITALJANKEN` | helbital |
| 4 | **法式湿吻 / Frenching** | "Just a taste, for science!" / 就尝一口，为了科学！ | 采矿：亲吻泡泡糖巨型 fauna（`bubblegum.dm:159`） | `MEDAL_FRENCHING` | frenchingthebubble |
| 5 | **力量壮举 / Feat of Strength** | "If the rod is immovable, is it passing you or are you passing it?" / 如果棍子是不可移动的，是它穿过你，还是你穿过它？ | 科学：格挡/摔倒不可移动之棍（Immovable Rod，"rod suplex"，`immovable_rod.dm:242`） | `MEDAL_RODSUPLEX` | featofstrength |
| 6 | **K K K i i i l l l m m m e e e（杀了我）** | "You were a little too ambitious, but hey, I guess you're still alive?" / 你有点太激进了，不过嘿，你还活着不是吗？ | 科学：被胃溶解病（gastrolisis）变成蜗牛后存活（`gastrolisis.dm:71`） | `MEDAL_SNAIL` | snail |
| 7 | **中央指挥部评级：差劲服务 / Centcom Grade: Shitty Service** | "Well, you at least tried. How about trying harder?" / 好吧，你至少努力了。要不要再努力点？ | 服务部：整局游客收入 1~2000 信用点（回合结束结算，`roundend.dm:562`） | `MEDAL_BAD_SERVICE`（"Bad Service"） | service_bad |
| 8 | **中央指挥部评级：合格服务 / Centcom Grade: Acceptable Service** | "Well, it'll do! You and your department did just fine." / 行吧，凑合！你和你的部门干得还行 | 服务部：整局游客收入 2001~4999 信用点（`roundend.dm:564`） | `MEDAL_OKAY_SERVICE`（"Okay Service"） | service_okay |
| 9 | **中央指挥部评级：模范服务 / Centcom Grade: Exemplary Service** | "Centcom is very impressed with your department!" / 中央指挥部对你们部门印象深刻！ | 服务部：整局游客收入 5000 信用点以上（`roundend.dm:566`） | `MEDAL_GOOD_SERVICE`（"Good Service"） | service_good |

---

## 四、成就全录：技能成就（2 项） / Skill Achievements

> 源码：`code/datums/achievements/skill_achievements.dm`（14 行）

| # | 成就 / Achievement | 描述 / Description | 解锁条件 / Unlock | Medal ID（database_id） | 图标 |
|---|---|---|---|---|---|
| 1 | **传奇矿工 / Legendary miner** | "No mere rock can stop me!" / 区区岩石拦不住我！ | 采矿技能达到大师（Master）级后开采强矿脉（strong mineral，`minerals.dm:1266`） | `MEDAL_LEGENDARY_MINER` | mining |
| 2 | **传奇渔夫 / Legendary fisher** | "Give a spaceman a fish and you feed him for a while; teach a spaceman to fish and you feed him until the shuttle arrives." / 授人以鱼不如授人以渔（直到穿梭机到达） | 钓鱼技能达到传奇（Legendary）级（`fishing_minigame.dm:368`） | `MEDAL_LEGENDARY_FISHER` | fishing_hat |

---

## 五、成就全录：杂项成就（43 项） / Misc Achievements

> 源码：`code/datums/achievements/misc_achievements.dm`（259 行）
> 未声明 `icon_state` 的成就默认继承 `basemisc`（源码注释：给那些还没有正式图标的成就用）。

| # | 成就 / Achievement | 描述 / Description | 解锁条件 / Unlock | Medal ID（database_id） | 图标 |
|---|---|---|---|---|---|
| 1 | **你的一生在眼前闪过 / Your Life Before Your Eyes** | "Take a close look at hurtling space debris" / 仔细看看飞驰的太空碎片 | 检查（examine）一颗来袭的流星（`meteor_types.dm:172`） | `MEDAL_METEOR` | meteors |
| 2 | **你的一生在你手中 / Your Life In Your Hands** | "Give some space debris a piece of your mind" / 给太空碎片一点颜色看看 | 徒手/近战击偏一颗流星（`meteor_deflection.dm:72`） | `MEDAL_METEOR_PUNCH` | punchmeteors |
| 3 | **头奖 / Jackpot** | "Win a pulse rifle from an arcade machine" / 从街机里赢到一把脉冲步枪 | 街机（arcade）中奖获得脉冲步枪（`_arcade.dm:91`） | `MEDAL_PULSE` | jackpot |
| 4 | **浪费时间者 / Time waster** | "Speak no evil, hear no evil, see just errors" / 不说恶言，不听恶语，只看见报错 | 在隐藏房间（super secret room，`super_secret_room.dm:82`）停留足够久 | `MEDAL_TIMEWASTE`（"Overextended The Joke"） | timewaste |
| 5 | **圆滚滚与饱饱 / Round and Full** | "Well at least you aren't down the river, I hear they eat people there." / 至少你没去下游，听说那儿的人吃人 | 被小丑车（clown car）吞噬（`clowncar.dm:87`） | `MEDAL_CLOWNCARKING` | clownking |
| 6 | **最佳司机 / The Best Driver** | "100 honks later" / 100 声喇叭之后 | 开小丑车累计鸣笛 100 次（`clowncar.dm:317`） | `MEDAL_THANKSALOT` | clownthanks |
| 7 | **升级啦 / Getting an upgrade** | "Make your first unique material item!" / 制作你的第一件独特材料物品！ | 用独特材料（unique material）制作第一件物品（`autolathe.dm:361`） | `MEDAL_MATERIALCRAFT` | upgrade |
| 8 | **磁盘，谢谢！/ Disk, Please!** | "Is the man currently pointing a loaded rocket launcher at your head point blank really dumb enough to pull the trigger? Do you really want to find out?" / 那个拿火箭筒顶着你的头的人真的蠢到会开枪吗？你真想试试吗？ | 被火箭筒指着（gunpoint 组件）时抢走对方的磁盘（`gunpoint.dm:73`） | `MEDAL_DISKPLEASE` | rocket_holdup |
| 9 | **我的监视名单状态不重要 / My Watchlist Status is Not Important** | "You may be under the impression that violent video games are a harmless pastime, but the security and medical personnel swarming your location with batons and knockout gas look like they disagree." / 你可能觉得暴力游戏是无害消遣，但拿着警棍和麻醉气涌过来的安保和医疗人员显然不这么认为 | 在猎户座街机（Orion Trail）上赢得胜利（`orion.dm:134`） | `MEDAL_GAMER` | live_sec_reaction |
| 10 | **我曾是个少年无政府主义者 / I Was a Teenage Anarchist** | "You were doing a great job sticking it to the system until that vending machine decided to fight back." / 你把系统怼得正爽，直到那台自动售货机决定反击 | 被倾倒的自动售货机压死/压中（`vendor/tilting.dm:235`） | `MEDAL_VENDORSQUISH` | basemisc |
| 11 | **马桶新世界 / A Bowl-d New World** | "There's a lot of grisly ways to kick it on the Spinward Periphery, but drowning to death in a toilet probably wasn't what you had in mind. Probably." / 在旋臂边疆有很多惨烈的死法，但淹死在马桶里大概不在你的计划内。大概。 | 被塞进马桶溺死/在马桶中溺水（toilet swirlie，`toilet.dm:161`） | `MEDAL_SWIRLIE` | basemisc |
| 12 | **怎么换手？？？/ How Do I Switch Hands???** | "If you saw someone casually club themselves upside the head with a toolbox anywhere in the galaxy but here, you'd probably be pretty concerned for them." / 如果在银河系任何其他地方看到有人随手用工具箱敲自己脑袋，你大概会替他们担心 | 用工具/武器误伤自己（攻击自己，`item_attack.dm:253`） | `MEDAL_SELFOUCH` | basemisc |
| 13 | **一台精瘦强悍的清洁机器 / One Lean, Mean, Cleaning Machine** | "How does it feel to know that your workplace values a mop bucket on wheels more than you?" / 得知你的工作单位比看重你更看重一台带轮子的拖把桶，感觉如何？ | 被清洁机器人（cleanbot）击败（`cleanbot.dm:191`） | `MEDAL_CLEANBOSS` | cleanboss |
| 14 | **第八条规则 / Rule 8** | "Call an admin this is ILLEGAL!!" / 叫管理员！这违法了！！ | 对毛绒玩具（plush）做一些不可描述的事（`plushes.dm:238`） | `MEDAL_RULE8` | rule8 |
| 15 | **漫长的班次 / Long shift** | "Well, that didn't take long." / 嗯，没花多长时间 | 回合在 10 分钟内结束（`roundend.dm:222,231`，`speed_round` 判定） | `MEDAL_LONGSHIFT` | longshift |
| 16 | **小心点，长官！/ Look Out, Sir!** | "Either awarded for making the ultimate sacrifice for your comrades, or a really dumb attempt at grenade jumping." / 要么是为战友做出终极牺牲，要么是愚蠢的手雷跳尝试 | 为队友挡下霰弹（pellet_cloud 弹幕中为他人挡枪，`pellet_cloud.dm:360`） | `MEDAL_LOOKOUTSIR` | martyr |
| 17 | **哈，骗到你啦 / HA, GOTTEM** | "Made you look!" / 让你看了吧！ | 使用"看那边！"（hand_items 转移注意力道具）成功骗到别人（`hand_items.dm:95`） | `MEDAL_GOTTEM` | gottem |
| 18 | **飞升 / Ascension** | "Caedite eos. Novit enim Dominus qui sunt eius." / "杀光他们，主自会辨认谁属于他"（拉丁文） | 用 His Grace 武器达成条件（`his_grace.dm:314`） | `MEDAL_ASCENSION` | ascension |
| 19 | **守夜人之眼 / Nightwatcher's Eyes** | "You've risen above the flames, became one with the ashes. You've been reborn as one with the Nightwatcher." / 你从火焰中升起，与灰烬合一，与守夜人一同重生 | 异端（heretic）灰烬之道飞升仪式完成（`ash_lore.dm:186`） | `MEDAL_ASH_ASCENSION`（"Ash"） | ashascend |
| 20 | **手臂漩涡 / Vortex of Arms** | "You've became something more, something greater. A piece of the emperor resides within you, and you within him." / 你成为了更伟大的存在。皇帝的一部分居于你体内，你也居于他体内 | 异端血肉之道飞升仪式完成（`flesh_lore.dm:309`） | `MEDAL_FLESH_ASCENSION`（"Flesh"） | fleshascend |
| 21 | **锈蚀之丘 / Hills of Rust** | "You've summoned a piece of the Hill of rust, and so the Hills welcome you." / 你召唤了锈丘的一部分，锈丘欢迎你 | 异端锈蚀之道飞升仪式完成（`rust_lore.dm:231`） | `MEDAL_RUST_ASCENSION`（"Rust"） | rustascend |
| 22 | **一切终将消逝 / All that perish** | "Place of a different being, different time. Everything ends there... but maybe it is just the beginning?" / 异界异时之地。万物终结于此……但也许这仅仅是开始？ | 异端虚空之道飞升仪式完成（`void_lore.dm:195`） | `MEDAL_VOID_ASCENSION`（"Void"） | voidascend |
| 23 | **银与钢 / Silver and Steel** | "You've become the master of all duellists - the paragon of blades." / 你成为所有决斗者的大师——剑之典范 | 异端利刃之道飞升仪式完成（`blade_lore.dm:323`） | `MEDAL_BLADE_ASCENSION`（"Blade"） | bladeascend |
| 24 | **它来了 / It arrived** | "You managed to teleport an entity on the station that really shouldn't be there." / 你把一个不该出现在站上的存在传送了上来 | 异端宇宙之道飞升仪式完成（`cosmic_lore.dm:235`） | `MEDAL_COSMOS_ASCENSION`（"Cosmos"） | cosmicascend |
| 25 | **锁闭迷宫的秘密 / Secrets of the Locked Labyrinth** | "You managed to open a gate into the mansus." / 你打开了通往曼苏斯之门 | 异端锁之道飞升仪式完成（`lock_lore.dm:229`） | `MEDAL_LOCK_ASCENSION`（"Knock"） | lockascend |
| 26 | **最后一幕 / The Last Act** | "You managed to become the ringleader and slay the lie." / 你成为了主谋并弑杀了谎言 | 异端月亮之道飞升仪式完成（`moon_lore.dm:198`） | `MEDAL_MOON_ASCENSION`（"Moon"） | moonascend |
| 27 | **大法师 / Archmage** | "Made a big impression on the station with your phenomenal cosmic power." / 用你惊人的宇宙力量给空间站留下了深刻印象 | 法师大仪式（Grand Ritual）完成（`grand_rune.dm:361`） | `MEDAL_ARCHMAGE` | archmage |
| 28 | **灵魂售罄 / SOUL'd Out** | "My eternal soul was destroyed to make a toolbox look funny and all I got was this achievement..." / 我的永恒灵魂被摧毁，只为让工具箱看起来搞笑，而我只得到这个成就… | 将灵魂（灵魂石中的灵魂）注入灵魂工具箱（`soulstone.dm:267`） | `MEDAL_TOOLBOX_SOUL` | toolbox_soul |
| 29 | **真他妈火辣！/ Hot Damn!** | "Sometimes you need to make some noise to make a point." / 有时你得搞点动静来表明立场 | 穿牛仔靴（cowboy boots）踢出火花/触发踢击特效（`cowboy.dm:52`） | `MEDAL_HOT_DAMN` | hotdamn |
| 30 | **非常重要的小鱼 / Very Important Piscis** | "You can rest well now." / 你现在可以安息了 | 击杀携带凯宴磁盘（Cayenne disk）的太空鲤鱼（`carp.dm:269`） | `MEDAL_CAYENNE_DISK` | cayenne_disk |
| 31 | **电车冲浪手 / Tram Surfer** | "Lights out, guerilla radio!" / 熄灯，游击队电台！ | 在电车上进行载具动作（冲浪/跳跃，`vehicle_actions.dm:379`） | `MEDAL_TRAM_SURFER` | tram_surfer |
| 32 | **刚才发生了什么 / WHAT JUST HAPPENED** | "As a blood cultist, be part of a team that summons 3 shuttle curses within 10 seconds. Imagine cleaning up after them, g r o s s!" / 作为血 cult 信徒，参与团队在 10 秒内召唤 3 次穿梭机诅咒。想象一下收拾残局，恶 心 死 了！ | 血 cult：10 秒内团队召唤 3 次穿梭机诅咒（`cult_items.dm:675`） | `MEDAL_CULT_SHUTTLE_OMFG` | cult_shuttle_omfg |
| 33 | **标题党 / Clickbait** | "Where's my free smartphone?!?" / 我的免费智能手机呢？！？ | PDA 收到"免费智能手机"钓鱼消息并点击（`pda.dm:227`） | `MEDAL_CLICKBAIT` | bait |
| 34 | **如果纳尔西这么好，为什么没有……/ If Nar'Sie is so good, why isn't there a...** | "Even interdimensional space deitys need a friend." / 连跨维度空间神祇也需要朋友 | 血 cult：召唤纳尔西至高形态（Nar'Sie Supreme，`runes.dm:682`） | `MEDAL_NARSUPREME` | narsupreme |
| 35 | **MOD 服里的人 / The Man Inside the MODsuit** | "Ignore the warning label on a springlock MODsuit." / 无视弹簧锁 MOD 服上的警告标签 | 穿戴弹簧锁（springlock）MOD 模块并被其夹住（`modules_maint.dm:90`） | `MEDAL_SPRINGLOCK` | springlock |
| 36 | **健康的典范 / The Picture of Health** | "Don't be such a baby, it's just a heart attack. You've bounced back from worse!" / 别那么娇气，不就是心脏病发作吗。更糟的你都挺过来了！ | 心脏病发作（heart attack 事件）后存活（`heart_attack.dm:80`） | `MEDAL_HEALTHY`（"Heart Healthy"） | picofhealth |
| 37 | **神之怒火 / God's Wrath** | "Did you think you could get away with defiling the word of God?" / 你以为亵渎神之言能逃过惩罚？ | 用圣经殴打/被圣经惩罚（`bibles.dm:135`） | `MEDAL_GODS_WRATH` | godswrath |
| 38 | **重重一摔 / A Nasty Fall** | "...And the earth opened its mouth and swallowed them and their station- all the HOP's men and all their possessions." / ……大地张开嘴吞没了他们和他们的空间站——人资主管的所有人马和全部家当 | 在地震事件（earthquake）中坠落/被吞没（`earthquake.dm:179`） | `MEDAL_EARTHQUAKE_VICTIM` | earthquake |
| 39 | **债务偿清 / Outdebted** | "I've paid my dues, shift after shift... I've done my sentence but commited no griff..." / 我一个班次一个班次地还债……我服完了刑期但没有搞破坏… | 负债者（indebted）特质：还清全部债务（`indebted.dm:31,40`） | `MEDAL_DEBT_EXTINGUISHED` | outdebted |
| 40 | **西西弗斯的考验 / Ordeal of Sisyphus** | "Successfully carry a boulder from Lavaland all to Centcom, without ever dropping it. We must imagine you're happy to unlock this." / 把巨石从拉瓦兰德一路搬到中央指挥部，全程不脱手。我们必须想象你解锁这个时是开心的 | 用西西弗斯组件（`sisyphus_awarder.dm:52`）把巨石从拉瓦兰德运到中央指挥部且不落地 | `MEDAL_SISYPHUS` | sisyphus |
| 41 | **不健康零食 / Unhealthy Snacks** | "You were curious to taste it. And then another. You must have more!" / 你好奇地尝了一根，然后又是一根。你必须来更多！ | 吃下大量香烟（`cigarettes.dm:297`） | `MEDAL_CIGARETTES` | cigarettes |
| 42 | **你吃什么你就是什么 / You're What You Eat** | "Nutritionists often recommend a balanced and varied diet. However that clearly isn't the case for some creatures." / 营养学家常推荐均衡多样的饮食。但显然有些生物不是这样 | 太空龙（space dragon）吃下鱼（`space_dragon.dm:129`） | `MEDAL_SHARKDRAGON` | dragon_plus_fish |
| 43 | **在佛兰德斯战场 / In Flanders Fields** | "You have witnessed more death and despair in one shift than most TerraGov marines have seen across an entire tour of duty. Perhaps one day you'll find peace. But not today." / 你一个班次目睹的死亡与绝望，比大多数 TerraGov 陆战队整个服役期还多。也许有一天你会找到平静。但不是今天 | 一局内目睹超过阈值数量的死亡（`_mind.dm:580` desensitized 判定） | `MEDAL_DESENSITIZED` | desensitized |

---

## 六、成就全录：黑手党成就（19 项） / Mafia Achievements

> 源码：`code/datums/achievements/mafia_achievements.dm`（120 行）
> 机制：每局黑手党游戏结束时，`/datum/mafia_controller/proc/award_role()` 按各角色的 `winner_award` 变量发放对应成就（`code/modules/mafia/roles/` 各角色文件）。城镇阵营胜利发 Town 系图标（town），黑手党/换形怪阵营发 mafia 图标，中立阵营发 neutral 图标。
> 难度：★（只要在该角色下赢一局）。

| # | 成就 / Achievement | 描述 / Description | 解锁条件 / Unlock | Medal ID（database_id） | 图标 |
|---|---|---|---|---|---|
| 1 | **助理胜利 / Assistant Victory** | "If you got killed instead of someone more important, you just flexed the true strength of your """"role""""." / 如果你替某个更重要的人死了，你就展示了你的""""角色""""的真正力量 | 以助理（Assistant）身份赢得黑手党局（默认 `winner_award`，`roles.dm:41`） | `MAFIA_MEDAL_ASSISTANT` | town |
| 2 | **侦探胜利 / Detective Victory** | "If you did this with a Medical Doctor in the game, i'm not really that impressed." / 如果局里有医生你还这样，我可不怎么佩服 | 以侦探（Detective）身份获胜（`town_investigative.dm:6`） | `MAFIA_MEDAL_DETECTIVE` | town |
| 3 | **心理医生胜利 / Psychologist Victory** | "You learned how to not reveal someone random night one! Or... maybe you're just a lucky bastard." / 你学会了第一晚别乱报人！或者……你就是个幸运的混蛋 | 以心理医生（Psychologist）身份获胜（`town_investigative.dm:18`） | `MAFIA_MEDAL_PSYCHOLOGIST` | town |
| 4 | **牧师胜利 / Chaplain Victory** | "The curse finally broken." / 诅咒终于被打破 | 以牧师（Chaplain）身份获胜（`town_support.dm:33`） | `MAFIA_MEDAL_CHAPLAIN` | town |
| 5 | **验尸官胜利 / Coroner Victory** | "Useless... until the one night the thoughtfeeder confidently claims themselves as detective. Mafia's true bullshit detector." / 没用……直到某个夜晚念食者自信地自称侦探。黑手党真正的扯淡探测器 | 以验尸官（Coroner）身份获胜（`town_investigative.dm:32`） | `MAFIA_MEDAL_CORONER` | town |
| 6 | **医生胜利 / Medical Doctor Victory** | "Congratulations on learning how to not talk!" / 恭喜你学会了不说话！ | 以医生（Medical Doctor）身份获胜（`town_protective.dm:8`） | `MAFIA_MEDAL_MD` | town |
| 7 | **安保官胜利 / Security Officer Victory** | "Don't worry, you can win this if you're dead! You... did use your ability to become dead, right?" / 别担心，死了也能赢！你……用了你的能力让自己死了吧？ | 以安保官（Security Officer）身份获胜（`town_protective.dm:20`） | `MAFIA_MEDAL_OFFICER` | town |
| 8 | **律师胜利 / Lawyer Victory** | "Oh don't mind me, i'm just the worst rol- Oops, I just instantly ended the game." / 别管我，我只是最烂的角——哎呀，我直接终结了游戏 | 以律师（Lawyer）身份获胜（`town_support.dm:8`） | `MAFIA_MEDAL_LAWYER` | town |
| 9 | **人事主管胜利 / Head of Personnel Victory** | "King of Assistants, waster of a single mafia's night, thrower of games." / 助理之王，浪费黑手党一夜的狠人，送分局之王 | 以人事主管（HoP）身份获胜（`town_support.dm:21`） | `MAFIA_MEDAL_HOP` | town |
| 10 | **典狱长胜利 / Warden Victory** | "Make changelings think you're detective, go on lockdown, actual detective investigates you and dies. Cha cha real smooth!" / 让换形怪以为你是侦探，进入封锁，真侦探来查你然后死了。真是行云流水！ | 以典狱长（Warden）身份获胜（`town_killing.dm:22`） | `MAFIA_MEDAL_WARDEN` | town |
| 11 | **安保主管胜利 / Head of Security Victory** | "Certified not shitcurity." / 认证的靠谱安保 | 以安保主管（HoS）身份获胜（`town_killing.dm:9`） | `MAFIA_MEDAL_HOS` | town |
| 12 | **换形怪胜利 / Changeling Victory** | "I think the changelings are metacomming." / 我觉得换形怪在开黑 | 以换形怪（Changeling）阵营身份获胜（`changelings/changeling.dm:10`） | `MAFIA_MEDAL_CHANGELING` | mafia |
| 13 | **念食者胜利 / Thoughtfeeder Victory** | "Clown's best friend. And Obsessed. And fugitive? Whose side are you on?!" / 小丑最好的朋友，也是痴迷者，还是逃犯？你到底站哪边？！ | 以念食者（Thoughtfeeder）身份获胜（`changelings/changeling.dm:33`） | `MAFIA_MEDAL_THOUGHTFEEDER` | mafia |
| 14 | **叛徒胜利 / Traitor Victory** | "Guys, we still have two more changelings to ki-!! TRAITOR VICTORY !!" / 兄弟们，我们还有两个换形怪要——！！叛徒胜利！！ | 以叛徒（Traitor，中立杀手）身份获胜（`neutral_killing.dm:9`） | `MAFIA_MEDAL_TRAITOR` | neutral |
| 15 | **梦魇胜利 / Nightmare Victory** | "DID YOUR LIGHT FLICKER?!" / 你的灯闪了吗？！ | 以梦魇（Nightmare）身份获胜（`neutral_killing.dm:39`） | `MAFIA_MEDAL_NIGHTMARE` | neutral |
| 16 | **逃犯胜利 / Fugitive Victory** | "I'm just the description on an achievement, but if you end up having to choose between town and changelings, go changelings." / 我只是成就上的描述文字，但如果你不得不在城镇和换形怪之间选，选换形怪 | 以逃犯（Fugitive，中立良善）身份获胜（`neutral_benign.dm:11`） | `MAFIA_MEDAL_FUGITIVE` | neutral |
| 17 | **痴迷者胜利 / Obsessed Victory** | "You got your target lynched, so instead of being spiteful and annoying, you're just smug and annoying." / 你把目标吊死了，所以你不是恶毒又烦人，而是得意又烦人 | 以痴迷者（Obsessed，中立混乱）身份获胜（`neutral_chaos.dm:13`） | `MAFIA_MEDAL_OBSESSED` | neutral |
| 18 | **小丑胜利 / Clown Victory** | "Did you know this works on traitors, despite their immunity? If you hit the jackpot and manage to kill one, they'll salt into the next dimension. Clown tips!" / 你知道吗，这招对叛徒也有效，尽管他们免疫！如果你中头奖杀掉一个，他们会气到穿越到另一个次元。小丑小贴士！ | 以小丑（Clown，中立混乱）身份获胜（`neutral_chaos.dm:65`） | `MAFIA_MEDAL_CLOWN` | neutral |
| 19 | **全民公敌 / Universally Hated** | "Managed to get more than 12 votes when put up on trial, jesus christ." / 被审判时拿到超过 12 票，天哪 | 在黑手党审判中获得超过 12 票（`mafia_achievements.dm:116`） | `MAFIA_MEDAL_HATED` | hated |

---

## 七、积分榜与进度系统（21 项） / Scores & Progress

> 源码：`boss_scores.dm`（59 行）、`job_scores.dm`（14 行）、`misc_scores.dm`（23 行）、`progress_scores.dm`（132 行）、`_awards.dm`（achievements_score，183 行起）
> 机制：`/datum/award/score` 每次 `unlock` 累加 `value`；`track_high_scores = TRUE` 时从数据库取前 50 名生成排行榜（`LoadHighScores()`）。`/datum/award/score/progress` 的数值等于已收集条目数，条目存于独立数据表，UI 中有专属进度页签。

### 7.1 BOSS 击杀积分（12 项） / Boss Kill Scores

| # | 积分 / Score | 描述 / Description | 数据库 ID / database_id |
|---|---|---|---|
| 1 | **触手积分 / Tendril Score** | "Watch your step" / 小心脚下 | `TENDRIL_CLEAR_SCORE`（"Tendrils Killed"） |
| 2 | **BOSS 击杀数 / Bosses Killed** | "You've killed HOW many?" / 你杀了多少？！ | `BOSS_SCORE`（"Bosses Killed"） |
| 3 | **血醉矿工击杀数 / Blood-Drunk Miners Killed** | "You've killed HOW many?" | `MINER_SCORE`（"BDMs Killed"） |
| 4 | **恶魔冰霜矿工击杀数 / Demonic-Frost Miners Killed** | "You've killed HOW many?" | `FROST_MINER_SCORE`（"DFMs Killed"） |
| 5 | **泡泡糖击杀数 / Bubblegums Killed** | "You've killed HOW many?" | `BUBBLEGUM_SCORE`（"Bubblegum Killed"） |
| 6 | **巨像击杀数 / Colossus Killed** | "You've killed HOW many?" | `COLOSSUS_SCORE`（"Colossus Killed"） |
| 7 | **龙兽击杀数 / Drakes Killed** | "You've killed HOW many?" | `DRAKE_SCORE`（"Drakes Killed"） |
| 8 | **圣者击杀数 / Hierophants Killed** | "You've killed HOW many?" | `HIEROPHANT_SCORE`（"Hierophants Killed"） |
| 9 | **军团击杀数 / Legions Killed** | "You've killed HOW many?" | `LEGION_SCORE`（"Legion Killed"） |
| 10 | **蜂群信标击杀数 / Swarmer Beacons Killed** | "You've killed HOW many?" | `SWARMER_BEACON_SCORE`（"Swarmer Beacs Killed"） |
| 11 | **温迪戈击杀数 / Wendigos Killed** | "You've killed HOW many?" | `WENDIGO_SCORE`（"Wendigos Killed"） |
| 12 | **异形击杀数 / The Thing Killed** | "You've killed HOW many?" | `THETHING_SCORE`（"The Thing Killed"） |

> 注：`code/__DEFINES/achievements.dm` 还定义了 `KINGGOAT_SCORE`（"King Goat Killed"）与 `BOSS_MEDAL_KINGGOAT*` 对应的击杀积分常量，但 `boss_scores.dm` 中**没有**对应的积分 datum 实例（山羊王只有成就、无积分榜项）。

### 7.2 职业积分（2 项） / Job Scores

| # | 积分 / Score | 描述 / Description | 数据库 ID / database_id |
|---|---|---|---|
| 1 | **厨师服务游客最高分 / Tourists Served as Chef Highscore** | "Your highscore on serving tourist bots as chef." / 作为厨师服务游客机器人的最高分（回合结束结算，`cook.dm:56,63`） | `CHEF_TOURISTS_SERVED` |
| 2 | **调酒师服务游客最高分 / Tourists Served as Bartender Highscore** | "Your highscore on serving tourist bots as bartender." / 作为调酒师服务游客机器人的最高分（回合结束结算，`bartender.dm:39,46`） | `BARTENDER_TOURISTS_SERVED` |

### 7.3 杂项积分（4 项） / Misc Scores

| # | 积分 / Score | 描述 / Description | 数据库 ID / database_id |
|---|---|---|---|
| 1 | **硬核随机点数 / Hardcore random points** | "Well, I might be a blind, deaf, crippled guy, but hey, at least I'm alive." / 好吧，我可能又瞎又聋又残，但嘿，至少我还活着 | 硬核随机模式（hardcore random）存活得分（回合结束按 `hardcore_survival_score` 结算，`roundend.dm:195-212`）。≥5000 分解锁"玩家轮椅"（gamer wheelchair，`paraplegic.dm:24`） | `HARDCORE_RANDOM_SCORE` |
| 2 | **维修间药丸摄入量 / Maintenance Pills Consumed** | "Wait why?" / 等等，为啥？ | 吃下的维修间药丸（maintenance pill）总数（`pill.dm:402`） | `MAINTENANCE_PILL_SCORE` |
| 3 | **Intento 分数 / Intento Score** | "A blast from the future?" / 来自未来的冲击？ | Intento 游戏最高分（`toys.dm:1762`） | `INTENTO_SCORE` |
| 4 | **风格点数 / Style Score** | "You might not be a robot, but you were damn close." / 你可能不是机器人，但你已经非常接近了 | 风格系统（style component）累计风格点数（`style.dm:184`） | `STYLE_SCORE` |

### 7.4 进度类（2 项） / Progress Scores

| # | 进度 / Progress | 描述 / Description | 数据库 ID / database_id | 数据表 / Table |
|---|---|---|---|---|
| 1 | **钓到的鱼类品种 / Fish Species Caught** | "How many different species of fish you've caught so far. Gotta fish 'em all." / 目前已钓到的不同鱼类品种数。钓鱼宝可梦！ | `FISH_SCORE`（"Fish Score"） | `fish_progress` |
| 2 | **已解锁 PDA 主题 / Unlocked PDA Themes** | "Any special PDA theme that you've installed on your PDA, which will then be added to your roundstart PDA on future rounds as well." / 你安装到 PDA 上的特殊主题，之后的回合会直接出现在开局 PDA 上 | `PDA_THEMES_SCORE`（"PDA Themes"），`track_high_scores = FALSE`（纯个人进度，不上榜） | `pda_themes_progress` |

> 钓鱼图鉴（Fishdex）：UI 显示"已钓/可钓"百分比，未钓到的品种显示 `??????`；会剔除已移除的鱼种（`validate_early_joiners`）。PDA 主题同理，未解锁显示 `??????`。

### 7.5 成就统计积分（1 项） / Achievement Count Score

| # | 积分 / Score | 描述 / Description | 数据库 ID / database_id |
|---|---|---|---|
| 1 | **已解锁成就数 / Achievements Unlocked** | "Don't worry, metagaming is all that matters." / 别担心，反正重要的是元游戏 | `ACHIEVEMENTS_SCORE`（"Achievements Score"），图标 `elephant`；排行榜按 ckey 解锁成就总数排序（JOIN `achievement_metadata` 表，类型为 'Achievement'） |

---

## 八、记录系统 / Record System

> 源码：`code/datums/records/`（5 个文件，678 行）+ NOVA 覆盖/追加

### 8.1 犯罪记录 / Crime（`crime.dm`，68 行）

**`/datum/crime`** —— 一条犯罪记录：

| 字段 / Field | 类型 / Type | 说明 / Description |
|---|---|---|
| `name` | string | 罪行名称（默认 "Crime"） |
| `details` | string | 罪行细节（默认 "No details provided."） |
| `author` | string | 记录写入者（默认 "Anonymous"） |
| `time` | string | 罪行时间（`round_timestamp()` 站内时间戳） |
| `valid` | boolean | 记录是否有效（默认 TRUE；无效记录在打印的案卷中显示 `--REDACTED--` 涂黑） |
| `voider` | string | 将该记录标记为无效的玩家 |

**`/datum/crime/citation`** —— 罚单子类（违章/罚款）：

| 字段 / Field | 类型 / Type | 说明 / Description |
|---|---|---|
| `fine` | number | 罚款金额（默认 0） |
| `paid` | number | 已支付金额（默认 0） |

| Proc | 说明 / Description |
|---|---|
| `pay_fine(amount)` | 支付罚款：`paid` 累加、`fine` 扣减，并自动修正异常值（超付/负数）；金额 ≤0 返回 FALSE |
| `alert_owner(sender, source, target_name, message)` | 以"Security Citation / Citation Server"为发件人，向目标 PDA 的通讯软件发送自动违规通知（`automated = TRUE`），并记入 `LOG_PDA` |

> 使用方：船员记录的 `crimes` 与 `citations` 列表（`record.dm`）；案卷打印 `get_rapsheet()` 时有效罪行逐行列出、无效罪行整行涂黑为 `--REDACTED--`。

### 8.2 通用数据 / Data（`data.dm`，4 行）

**`/datum/data`** —— 极简数据壳：

| 字段 / Field | 说明 / Description |
|---|---|
| `name` | 条目名称 |

> 源码注释：目前用于实验（experiments）与售货机商品（vending products）。

### 8.3 船员名册 / Manifest（`manifest.dm`，247 行）

**`/datum/manifest`**（全局单例 `GLOB.manifest`）—— 存储全体船员记录：

| 字段 / Field | 说明 / Description |
|---|---|
| `general` | 全部船员记录列表（`/datum/record/crew`） |
| `locked` | 不可在游戏内修改的锁定记录列表（`/datum/record/locked`），供 `respawn_character()` 复活用 |
| `print_count` | 安全案卷打印总数（改变打印件头部编号 `SR-N`） |

| Proc | 说明 / Description |
|---|---|
| `build()` | 开局构建名册：遍历 `new_player_list` 中已就绪的玩家，写 `log_manifest` 数据库日志（`ReportRoundstartManifest`）并 `inject` 入册 |
| `get_manifest()` | 按部门顺序生成名册（部门头排最前、命令部门可跨部门显示）；无合法岗位/无 `JOB_CREW_MANIFEST` 标记的记录归入"未分配"；NOVA 追加：名字为 "Unknown" 或职位为 "Unassigned"/"Unknown" 的记录不显示 |
| `get_html(monochrome)` | 生成名册 HTML 表格（彩色或单色打印版） |
| `inject(person, appearance_proxy, person_client)` | 注入新船员记录：复制 DNA、指纹（`md5(unique_identity)`）、血型、性别、年龄、职务；同时创建 `/datum/record/locked` 锁定记录（含完整 DNA 与 mind 引用）并建立 `lock_ref` 关联。NOVA：支持访客（Visitor ID）→ 走 `inject_guest()` 不上名册；支持自定义替代职务（alt job titles）；记录 chrono_age 时间年龄；写入背景信息/可利用信息/过往档案（从玩家偏好读取） |
| `modify(name, assignment, trim)` | 按姓名修改记录职务与 trim |
| `remove(name)` | 按姓名删除记录 |
| `change_pictures(name, person, add_height_chart)` | 重拍记录照片（支持身高标尺背景） |
| `ui_*` | TGUI `CrewManifest` 界面：名册 + 各部门空缺职位统计（含无限职位例外列表、部门颜色） |

> NOVA（`modular_nova/master_files/code/datums/records/manifest.dm`）：`inject_guest()` —— 访客/游客（Visitor ID 特质 + 助理岗）生成"只有有限数据"的简化记录（姓名缩写为"X. Lastname"、无职务、无背景信息），不上名册。

### 8.4 医疗备注 / Medical Note（`medical_note.dm`，15 行）

**`/datum/medical_note`** —— 玩家撰写的医疗备注：

| 字段 / Field | 说明 / Description |
|---|---|
| `author` | 撰写者（默认 "Anonymous"） |
| `content` | 备注内容（默认 "No details provided."） |
| `time` | 站内时间戳（默认 "--:--:--"） |

> 存放于 `/datum/record/crew` 的 `medical_notes` 列表。

### 8.5 记录基类与船员/锁定记录 / Record（`record.dm`，344 行）

**`/datum/record`** —— 记录基类（船员记录与管理员锁定记录共用）：

| 字段 / Field | 说明 / Description |
|---|---|
| `age` | 角色年龄（默认 18） |
| `chrono_age` | 时间年龄（NOVA 追加，默认 18） |
| `blood_type` | 血型（默认 "?"） |
| `character_appearance` | 角色外观（`mutable_appearance`） |
| `dna_string` | DNA 字符串（unique_enzymes，默认 "Unknown"） |
| `fingerprint` | 指纹（`md5(unique_identity)`，默认 "?????"） |
| `gender` | 性别（默认 "Other"） |
| `initial_rank` | 开局初始职务（默认 "Unassigned"） |
| `name` | 角色名（默认 "Unknown"） |
| `rank` | 当前职务（默认 "Unassigned"） |
| `species` | 种族（默认 "Human"） |
| `trim` | ID trim（默认 "Unassigned"） |
| `voice` | 语音（默认 "?????"） |

**`/datum/record/crew`** —— 船员记录（加入 `GLOB.manifest.general`）：

| 字段 / Field | 说明 / Description |
|---|---|
| `citations` | 罚单列表（`/datum/crime/citation`） |
| `crimes` | 罪行列表（`/datum/crime`） |
| `lock_ref` | 关联锁定记录的引用（`REF(lockfile)`） |
| `major_disabilities` | 重大残疾名称串（开局扫描，默认 "None"） |
| `major_disabilities_desc` | 重大残疾描述（默认 "No disabilities have been diagnosed at the moment."） |
| `minor_disabilities` | 轻微残疾名称串 |
| `minor_disabilities_desc` | 轻微残疾描述 |
| `medical_notes` | 医疗备注列表（`/datum/medical_note`） |
| `physical_status` | 医疗档案中的生理状态（`PHYSICAL_ACTIVE` 等） |
| `cause_of_death` | 若被宣告死亡则记录死因；重新宣告存活时清空 |
| `mental_status` | 心理状态（`MENTAL_STABLE` 等） |
| `quirk_notes` | 正面/中性特质字符串 |
| `security_note` | 安保备注 |
| `wanted_status` | 通缉状态（`WANTED_NONE` 等） |
| `record_photos` | 记录照片缓存（`photo_front` / `photo_side` 两个 `obj/item/photo`） |
| `background_information` | 背景信息（NOVA，RP 记录） |
| `exploitable_information` | 可利用信息（NOVA，RP 记录，仅反派/管理员可看） |
| `past_general_records` | 过往综合档案（NOVA，玩家自写） |
| `past_medical_records` | 过往医疗档案（NOVA，玩家自写） |
| `past_security_records` | 过往安保档案（NOVA，玩家自写） |

| Proc | 说明 / Description |
|---|---|
| `get_front_photo()` / `get_side_photo()` | 取正面（SOUTH 朝向）/侧面（WEST 朝向）照片，未生成则现拍并缓存 |
| `recreate_manifest_photos(add_height_chart)` | 重建正/侧两张照片（可加身高标尺） |
| `get_rapsheet(alias, header, description)` | 打印安全案卷：生成 `obj/item/paper`，标题 `SR-[print_count]: [header]`，含姓名/性别/年龄/时间年龄/别名/种族/指纹/通缉状态 + NOVA 过往综合档案与过往安保档案 + 罪行表（无效行涂黑 REDACTED）+ 罚单表（含罚款额）+ 重要备注 |

**`/datum/record/locked`** —— 管理员锁定记录（加入 `GLOB.manifest.locked`，游戏内不可改）：

| 字段 / Field | 说明 / Description |
|---|---|
| `locked_dna` | 完整 DNA datum（用于 `respawn_character()` 复活） |
| `mind_ref` | mind datum 弱引用（`WEAKREF`） |
| `species_type` | 玩家种族 typepath（复活用） |

> NOVA 追加：`/datum/record/locked/New()` 中把 `name -> character_appearance` 写入 `GLOB.name_to_appearance` 缓存，供角色目录（Character Directory）的预览视图使用。

### 8.6 记录相关状态常量

| 常量 / Constant | 说明 / Description |
|---|---|
| `PHYSICAL_ACTIVE` | 生理状态：在世 |
| `MENTAL_STABLE` | 心理状态：稳定 |
| `WANTED_NONE` | 通缉状态：无 |

---

## 九、投票系统 / Vote System

> 源码：`code/datums/votes/`（3 个文件）+ `code/controllers/subsystem/vote.dm`（484 行）+ NOVA 追加/覆盖

### 9.1 投票基类 / Vote Base（`_vote_datum.dm`，259 行）

**`/datum/vote`** —— 投票单例（每种投票一个实例，由 `SSvote` 在初始化时扫描 `subtypesof(/datum/vote)` 创建）：

| 字段 / Field | 说明 / Description |
|---|---|
| `name` | 投票名 |
| `override_question` | 覆盖问题（显示时替代投票名） |
| `vote_sound` | 发起投票时全服播放的音效（默认 `sound/misc/bloop.ogg`；NOVA 覆盖为 `sound/announcer/announcement/announce_dig.ogg`） |
| `default_choices` | 默认选项列表 |
| `contains_vote_in_name` | 名字是否已含"vote"字样（影响发起公告措辞） |
| `default_message` | 可发起时的悬停提示（默认 "Click to initiate a vote."） |
| `count_method` | 计票方式：`VOTE_COUNT_METHOD_SINGLE`（单选，每人一票）/ `VOTE_COUNT_METHOD_MULTI`（多选，每人可勾选多个） |
| `winner_method` | 胜者算法：`VOTE_WINNER_METHOD_SIMPLE`（"Simple" 票多者胜）/ `VOTE_WINNER_METHOD_WEIGHTED_RANDOM`（"Weighted Random" 按票数加权随机）/ `VOTE_WINNER_METHOD_NONE`（"None" 无胜者） |
| `display_statistics` | 投票期间是否实时显示各选项票数 |
| `print_results` | `display_statistics=FALSE` 时，结束后是否仍把票数结果打印到聊天 |
| `choices` | 当前票数表（选项 → 票数） |
| `choices_by_ckey` | 投票记录（ckey → 所选项；多选时为 ckey+选项 键） |
| `started_time` / `time_remaining` | 开始时间 / 剩余秒数 |

| Proc | 说明 / Description |
|---|---|
| `is_accessible_vote()` | 是否有默认选项（无选项则不注册该投票） |
| `reset()` | 重置投票状态 |
| `toggle_votable()` / `is_config_enabled()` | 与配置项联动开关（无配置返回 -1） |
| `can_be_initiated(forced)` | 能否发起（配置关闭时拒绝；`forced` 为管理员强制） |
| `create_vote(vote_creator)` | 发起前准备选项 |
| `initiate_vote(initiator, duration)` | 正式发起，返回公告文本（"X vote started by Y"） |
| `get_vote_result(non_voters)` | 按 `winner_method` 计算胜者列表（平局时列表 >1） |
| `get_simple_winner()` / `get_random_winner()` | 简单多数 / 加权随机 |
| `get_choice_display_name(choice)` | NOVA i18n 追加：选项显示名（键仍为原值） |
| `get_result_text(...)` / `get_winner_text(...)` | 结果播报文本（含百分比、胜者、平局列表、弃权者处理） |
| `tiebreaker(winners)` | 平局随机破平 |
| `finalize_vote(winning_option)` | 投票生效（各子类实现实际效果） |

### 9.2 子系统机制 / Vote Subsystem（`vote.dm`，484 行）

- **`SSvote`**：`wait = 1 SECONDS`，运行于 Lobby 与默认关卡。
- **发起流程** `initiate_vote(vote_type, name, initiator, forced)`：`can_vote_start`（初始化完成 + 冷却 `vote_delay` + 无进行中投票）→ `can_be_initiated` → `create_vote` → 广播"Type **vote** or click here to place your votes. You have [duration] to vote." → 给所有客户端发放 `/datum/action/vote` 按钮（结束后移除）。
- **计票**：`submit_single_vote`（改票会扣除旧票）/ `submit_multi_vote`（再点取消）。亡灵投票受 `no_dead_vote` 配置限制（管理员除外）。
- **结束**：`end_vote()` → `process_vote_result()`：收集未投票者（剔除 AFK）→ 计算胜者 → 平局 `tiebreaker` → 全服紫色播报结果（`vote_font` 紫色）→ `finalize_vote` 生效 → 记 `log_vote` 日志。
- **玩家入口**：`/mob/verb/vote()`（OOC 分类，命令名"投票"）打开 TGUI `VotePanel`。
- **管理员操作**（`ui_act`）：`cancel`（取消投票）、`endNow`（提前结束）、`toggleDeadVote`（开关亡灵投票）、`toggleVote`（开关某种投票）、`callVote`（发起，管理员自动 `forced`）、`resetCooldown`（重置冷却）。全部校验 `R_ADMIN`/holder 权限并 `message_admins` 留痕。
- **配置项**：`vote_period`（投票时长）、`vote_delay`（两次投票间隔）、`no_dead_vote`、`default_no_vote`（未投票者默认票向）。

### 9.3 自定义投票 / Custom Vote（`custom_vote.dm`，121 行）

- **名称**：Custom（自定义）。
- **仅管理员可发起**（`can_be_initiated`：非 forced 一律拒绝，"Only admins can create custom votes."；`create_vote` 结束时校验发起者仍是管理员）。
- **可配置项**：计票方式（Single / Multiple）、胜者算法（Simple / Weighted Random / No Winner）、是否实时显示票数、结束后是否打印票数、覆盖问题文本、选项（**最多 10 个**，`MAX_CUSTOM_VOTE_OPTIONS`，每个选项 ≤ `MAX_NAME_LEN` 字符，选项自动首字母大写）。
- 选项为空或中途取消则放弃发起。

### 9.4 地图投票 / Map Vote（`map_vote.dm`，82 行）

- **名称**：Map（地图投票）。提示语："Vote for next round's map!"。
- 选项来自 `SSmap_vote.get_valid_map_vote_choices()`（有效地图列表）。
- **核心分支版本**：`count_method = SINGLE`、`winner_method = NONE`（无胜者，票数仅供 `SSmap_vote.finalize_map_vote` 参考）。
- **NOVA 覆盖**（`modular_nova/master_files/code/datums/votes/map_vote.dm`）：改为 `count_method = MULTI`（多选）、`winner_method = SIMPLE`（简单多数）——"地图投票不应该用加权随机"。
- **特殊逻辑**：只剩 1 个可选地图时自动轮换（"voted by not voting"）不发起投票；0 个可选时公告并放弃。
- **未投票者**：`default_no_vote` 关闭且存在默认地图时，未投票者自动投其偏好地图（`preferred_map` 偏好），无偏好则投默认地图。
- **i18n（NOVA）**：选项显示名走 `lang_map_display_name`（"译名-英文"），键仍为英文 map_name。
- 生效：`finalize_vote` → `SSmap_vote.finalize_map_vote(src)`（`code/controllers/subsystem/map_vote.dm`）。
- 配置：`allow_vote_map`。

### 9.5 重启投票 / Restart Vote（`restart_vote.dm`，72 行）

- **名称**：Restart（重启）。选项固定两个：`"Restart Round"`（重启回合）/ `"Continue Playing"`（继续游戏）。
- 提示语："Vote to restart the ongoing round. Only works if there are no non-AFK admins online."（仅当没有在线且非 AFK 的管理员时生效）。
- **`admins_present()`**：存在在线、非 AFK、拥有 `R_SERVER` 权限的管理员则返回 TRUE。
- 发起时若有管理员在线，弹窗提醒发起者"有管理员在线，投票可能被取消"。
- **未投票者**：`default_no_vote` 关闭时，未投票者自动计入"Continue Playing"。
- **生效** `finalize_vote`：若胜者为 Restart Round 且有管理员在线 → 取消并公告；若通过 → 先回滚已选定的下一张地图（`SSmap_vote.revert_next_map()`），再 `SSticker.force_ending = FORCE_END_ROUND` 强制结束回合。
- 配置：`allow_vote_restart`。

### 9.6 换班投票 / Transfer Vote（NOVA，`autotransfer` 模块）

- **名称**：Transfer（换班/撤离）。选项固定两个：`"Initiate Crew Transfer"`（发起船员转移）/ `"Continue Playing"`。
- 提示语："Vote to initiate a transfer, forcing a shuttle call that cannot be recalled. Don't touch it unless it's not working automatically."（强制呼叫不可撤回的穿梭机；自动换班失效时才需要手动）。
- 配置：需 `autotransfer` 与 `allow_vote_transfer` 同时开启才可发起。
- 生效：胜者为转移时 `SSshuttle.autoEnd()` 强制结束并更新通讯台状态。
- 自动触发：`autotransfer.dm:38` 由自动换班系统 `SSvote.initiate_vote(/datum/vote/transfer_vote, "automatic transfer", forced = TRUE)` 强制发起。

### 9.7 投票类型一览 / Vote Types Summary

| 投票 / Vote | 选项 / Choices | 计票 / Count | 胜者 / Winner | 可否玩家发起 | 配置开关 |
|---|---|---|---|---|---|
| 自定义 Custom | 发起者自定义（≤10） | 单选或多选 | Simple / Weighted Random / None | 仅管理员 | 无 |
| 地图 Map | 有效地图列表 | 单选（NOVA：多选） | None（NOVA：Simple） | 是 | `allow_vote_map` |
| 重启 Restart | Restart Round / Continue Playing | 单选 | Simple | 是（有管理员在线时会被取消） | `allow_vote_restart` |
| 换班 Transfer（NOVA） | Initiate Crew Transfer / Continue Playing | 单选 | Simple | 是（需 autotransfer 开启） | `autotransfer` + `allow_vote_transfer` |

---

## 十、NOVA 追加模块 / NOVA Additions

### 10.1 活动奖励 / Event Awards（`modular_nova/modules/event_awards/`）

**Plasteel Chef 活动奖励 —— "émincer" 厨师刀**（`code/plasteel_chef.dm`，51 行）：

- `obj/item/knife/kitchen/plasteel_chef`：定制高级厨刀，材质为钛合金（plasteel，6 单位）+ 金边 + 木柄，刀身根部蚀刻厨师刀与剁骨刀交叉图案，**获奖者名字刻在刀柄上**。
- 属性：`LAVA_PROOF | FIRE_PROOF`，仅能持握（`ITEM_SLOT_HANDS`）。
- **白名单**：`allowed_ckeys`（静态列表，由加载项 `ckeywhitelist` 指定：`"bearagon"`、`"mrsanderp"`）；非白名单玩家无法拾取/装备（装备后强制掉落并气泡提示）。
- **心情加成**：`/datum/mood_event/plasteel_chef`，`mood_change = 3`（"You have an extreme sense of pride at what you've accomplished." 极度自豪），持有时生效、放下移除。
- **配装项**：`/datum/loadout_item/inhand/plasteel_chef`（ckey 白名单配装）。

### 10.2 角色目录 / Character Directory（`modular_nova/modules/character_directory/code/character_directory.dm`，344 行）

- **入口**：`/client/verb/show_character_directory`（OOC 分类，命令名"角色目录"），10 秒冷却（`char_directory_cooldown`，防刷屏卡服）。
- **全局**：`GLOB.character_directory` 单例 + `GLOB.name_to_appearance` 外观缓存（由 `/datum/record/locked/New()` 填充，供预览视图）。
- **新增偏好 / Preferences**：
  - `toggle/show_in_directory`：是否出现在目录中（游戏偏好，默认 TRUE）。
  - `text/character_ad`：角色广告文本（≤ `MAX_FLAVOR_LEN`）。
  - `choiced/attraction`：性取向（13 选项：Unset / Check OOC / Straight / Lesbian / Gay / Bisexual / Pansexual / Polysexual / Asexual / Aromantic / Aro/Ace / Skoliosexual / Omnisexual）。
  - `choiced/display_gender`：展示性别（16 选项：Unset / Check OOC / Male / Male-Femme / Male-Butch / Female / Female-Femme / Female-Butch / Nonbinary / Genderfluid / Trans / Andromorph / Gynomorph / Agender / Plural / Omnigender）。
- **显示内容**：仅存活玩家（`GLOB.alive_player_list`），隐藏面部的角色（面具/头盔 `HIDEFACE`、`TRAIT_UNKNOWN_APPEARANCE`）不显示；人类显示自定义种族/口味文本（flavor text）/头像（headshot）；硅基显示大脑类型与硅基口味文本。
- **每行数据**：姓名（含伪装名）、外观名、种族、OOC 备注（含 NSFW 版）、性取向、展示性别、ERP/吞噬/非自愿/催眠标签（erp_status / erp_status_v / erp_status_nc / erp_status_hypno，默认 "Ask"）、NovaStar 状态（`SSplayer_ranks.is_nova_star`）、角色广告、口味文本（含 NSFW 版）、头像、ref。
- **操作**：`refresh`（10 秒冷却刷新）、`orbit`（观察者轨道跟随）、`view_character`（预览 3D 角色外观）。

### 10.3 检查时记录 / Records On Examine（`modular_nova/modules/records_on_examine/`）

**可利用信息（Exploitable Information）系统** —— 让反派查看船员填写的"可利用信息"（背景弱点等 RP 信息）：

- `record_variables.dm`：`/datum/mind` 新增 `can_see_exploitables`（标准判定，给反派）、`has_exploitables_override`（管理员/OPFOR 无条件强制）、`has_exploitable_menu`（verb 是否已挂载的跟踪标志）。
- `records_procs.dm`：`handle_exploitables()` 遍历 mind 的 `antag_datums`，任一 `view_exploitables = TRUE` 即授予访问；`handle_exploitables_menu()` 按状态增删 verb（获得时红字提示 `VIEW_CREW_EXPLOITABLES_GAIN_TEXT`）。
- `view_exploitables.dm`：`/mob/proc/view_exploitables_verb`（OOC 分类，命令名"查看船员可利用信息"）打开 TGUI `RecordManifest`。
- `record_manifest.dm`：`/datum/record_manifest` + `get_exploitable_manifest()`（按部门列出所有填写了非空、非默认文本可利用信息的船员；部门头排最前）；UI 操作 `show_exploitables`（聊天框展示可利用信息）/ `show_background`（展示背景信息）。`ui_status` 要求 `can_see_exploitables` 或 override。

### 10.4 NOVA 其他相关改动

- **记录字段追加**（`modular_nova/master_files/code/datums/records/record.dm`）：`/datum/record` 增加 `chrono_age`；`/datum/record/crew` 增加 `background_information`、`exploitable_information`、`past_general_records`、`past_medical_records`、`past_security_records`。
- **访客入册**（`manifest.dm` 覆盖）：`inject_guest()` 见 8.3。
- **名册过滤**（`manifest.dm` 覆盖）：Unknown/Unassigned 记录不上名册。
- **替代职务**：`manifest.inject()` 使用 `alt_job_titles` 偏好。
- **i18n 代码改造**：玩家可见字符串改写为 `LANG()`（本百科所引英文为源码原始文案）。
- **投票覆盖**：见 9.4、9.6（`_vote_datum.dm` 覆盖 `vote_sound`）。

---

## 十一、物理勋章与嘉奖 / Physical Medals & Commendations

> 源码：`code/modules/clothing/under/accessories/medals.dm`（167 行）+ 全局 `GLOB.commendations`（`code/_globalvars/lists/achievements.dm`）
> 说明：与成就系统（奖章中心 ID）不同，物理勋章是可佩戴在制服上的真实物品（`/obj/item/clothing/accessory/medal`），通过"别针组件"（`/datum/component/pinnable_accessory`）别到制服上。

**机制 / Mechanics**：
- 别上勋章时需输入嘉奖理由（≤140 字符），被授予者会获得 `/datum/memory/received_medal` 记忆，嘉奖记入 `GLOB.commendations`（回合结束播报），并写入黑箱统计（commendation）。
- 同一枚勋章只能授予一人（`awarded_to` 已设置则不可再授予）；不能自己授予自己；描述会追加铭文："The inscription reads: [理由] - Awarded to [被授予者] by [授予者]"。

**勋章全录 / Complete Medal List**：

| # | 勋章 / Medal | 材质 / Material | 说明 / Description |
|---|---|---|---|
| 1 | 青铜勋章 / bronze medal | 铁（iron） | 基础勋章（`medaltype = "medal"`） |
| 2 | 卓越品行勋章 / distinguished conduct medal | 铁 | 纳米特拉斯授予的最基本荣誉，常由船长颁给船员 |
| 3 | 青铜之心勋章 / bronze heart medal | 铁 | 为牺牲/重伤授予（常为追授），心形 |
| 4 | 绶带 / ribbon | — | 基础绶带 |
| 5 | "本班最佳货运员"奖 / "cargo tech of the shift" award | — | 授予忠于卡戈尼亚传统的货运员 |
| 6 | 银质勋章 / silver medal | 银（silver） | 基础银章 |
| 7 | 英勇勋章 / medal of valor | 银 | 授予非凡英勇行为 |
| 8 | 稳健安保奖 / robust security award | 银 | 授予捍卫纳米特拉斯商业利益的安保人员 |
| 9 | 人事主管卓越成就奖 / the head of personnel award for outstanding achievement in the field of excellence | 银 | 授予"符合卓越定义"的稀有船员 |
| 10 | 卓越官僚奖章 / Excellence in Bureaucracy Medal | 银 | 授予合同期内杰出的管理服务 |
| 11 | 金质勋章 / gold medal | 金（gold） | 基础金章 |
| 12 | 船长勋章 / medal of captaincy | 金 | 仅授予晋升为船长者（不可毁坏：INDESTRUCTIBLE/Lava/Fire/Acid proof） |
| 13 | 非凡英雄主义勋章 / medal of exceptional heroism | 金 | CentCom 颁发的极稀有最高荣誉，几乎只授予指挥官 |
| 14 | 模范表现勋章 / exemplary performance medal | — | 授予医疗部杰出品行/表现/主动性 |
| 15 | 卓越医术勋章 / excellence in medicine medal | — | 授予超出一切预期的传奇级医疗表现 |
| 16 | 等离子勋章 / plasma medal | 等离子体（plasma） | 遇高温（>300K）会释放等离子气并销毁自身（atmos_sensitive） |
| 17 | 诺贝尔科学奖 / nobel sciences award | 等离子体 | 授予对科学/工程有重大贡献者 |
| 18 | 应急服务奖 / emergency services award | 银 | 授予逆境中守护船员安全的杰出应急人员 |
| 19 | 应急服务奖（工程） / emergency services award (engineering) | 银 | 背面刻橙色扳手 |
| 20 | 应急服务奖（医疗） / emergency services award (medical) | 银 | 背面刻深蓝十字 |
| 21 | 大气掌控奖 / atmospheric mastery award（"elder atmosian"） | 银 | 授予精通大气的科学家/技师 |

---

## 十二、源码路径索引 / Source Path Index

**成就 / Achievements**：
- `code/datums/achievements/_achievement_data.dm`（138 行）—— 玩家成就数据持有者
- `code/datums/achievements/_awards.dm`（368 行）—— award/achievement/score/progress 基类
- `code/datums/achievements/boss_achievements.dm`（135 行）、`boss_scores.dm`（59 行）
- `code/datums/achievements/job_achievements.dm`（69 行）、`job_scores.dm`（14 行）
- `code/datums/achievements/skill_achievements.dm`（14 行）
- `code/datums/achievements/mafia_achievements.dm`（120 行）
- `code/datums/achievements/misc_achievements.dm`（259 行）、`misc_scores.dm`（23 行）
- `code/datums/achievements/progress_scores.dm`（132 行）
- `code/datums/achievements/admin_panel.dm`（74 行）—— 成就管理面板
- `code/__DEFINES/achievements.dm`（172 行）—— 全部 Medal/Score ID 常量
- `code/controllers/subsystem/achievements.dm`（136 行）—— SSachievements
- `code/_globalvars/lists/achievements.dm` —— 分类/音效/嘉奖全局
- `code/datums/elements/kill_achievement.dm`（62 行）—— BOSS 击杀成就发放

**记录 / Records**：
- `code/datums/records/crime.dm`（68 行）、`data.dm`（4 行）、`manifest.dm`（247 行）、`medical_note.dm`（15 行）、`record.dm`（344 行）
- `modular_nova/master_files/code/datums/records/manifest.dm`（访客入册）、`record.dm`（NOVA 字段）

**投票 / Votes**：
- `code/datums/votes/_vote_datum.dm`（259 行）、`custom_vote.dm`（121 行）、`map_vote.dm`（82 行）、`restart_vote.dm`（72 行）
- `code/controllers/subsystem/vote.dm`（484 行）、`code/controllers/subsystem/map_vote.dm`
- `code/__DEFINES/subsystems.dm`（288-300 行）—— 计票/胜者常量
- `modular_nova/master_files/code/datums/votes/_vote_datum.dm`、`map_vote.dm`（NOVA 覆盖）
- `modular_nova/modules/autotransfer/code/transfer_vote.dm`、`autotransfer.dm`

**NOVA 模块**：
- `modular_nova/modules/event_awards/code/plasteel_chef.dm`
- `modular_nova/modules/character_directory/code/character_directory.dm`
- `modular_nova/modules/records_on_examine/code/`（record_variables.dm / records_procs.dm / record_manifest.dm / view_exploitables.dm）

**物理勋章**：
- `code/modules/clothing/under/accessories/medals.dm`

---

*本百科由 TianGuan13-master（NovaSector 分支）源码逐文件提取生成。成就总数 95（BOSS 22 / 职业 9 / 技能 2 / 杂项 43 / 黑手党 19），积分与进度 21，奖励合计 116；记录 datum 5 类；投票类型 4 种（含 NOVA 换班投票）。*
