# TianGuan13 街机兑奖券与奖品百科 (Arcade Ticket & Prize Encyclopedia)

> 基于 TianGuan13 NovaSector 分支源码全量整理。核心：`code/game/machinery/computer/arcade/`（街机 6 文件）+ `code/_globalvars/arcade.dm`（奖品池 70 种）+ `code/game/objects/items/stacks/tickets.dm`（兑奖券）。
> **范围**：街机胜利兑奖券机制、兑奖券抽奖、70 种奖品全录（含特性/权重/概率）、EMAG 彩蛋、索引。

## 目录

- [一、兑奖券机制](#一兑奖券机制)
- [二、街机与胜利奖励](#二街机与胜利奖励)
- [三、抽奖机制](#三抽奖机制)
- [四、奖品全录（70 种）](#四奖品全录70-种)
- [五、彩蛋与特殊机制](#五彩蛋与特殊机制)
- [附录 · 代码路径索引](#附录--代码路径索引)

---

## 一、兑奖券机制

### 1.1 兑奖券（Arcade Ticket）

| 属性 | 值 |
|---|---|
| 名称 | arcade tickets（街机兑奖券） |
| 描述 | '哇！攒够这些你都能买辆自行车了！……得了吧。' |
| 堆叠上限 | **30 张**（max_amount = 30） |
| 图标 | arcade-ticket（4 档：1/2-6/6-12/12+ 张） |
| 合并 | 自动堆叠（merge_type） |

> 兑奖券从街机胜利获得（见第二节），**2 张券 = 1 次抽奖**（`tickets.use(2)` 后 `prizevend()`）。

## 二、街机与胜利奖励

| 街机 | 胜利条件 | 券奖励 | EMAG 效果 |
|---|---|---|---|
| **战斗街机**（battle arcade） | 击败 9 个世界全部敌人（难度 1→3 倍递增） | **2 张** | 击败 Cuban Pete 给**真等离子炸弹**（定时器装配）+ 收藏帽 |
| **截肢冒险**（Mediborg Amputation） | 手臂伸进机器并**真截肢**（do_after 5 秒） | **6-10 张**（随机） | — |
| **猎户座之旅**（The Orion Trail） | 抵达猎户座（剧情通关） | **2 张** | 给**爆炸玩具飞船**（orion_ship） |
| **机甲格斗**（Battle Gear） | 机甲对战胜利 | 无券 | — |

> **截肢街机**是最高回报（6-10 张）但也最狠——真的要砍掉自己一只手（`dismember`）。战斗街机 9 世界敌人数值随世界递增（1.0→3.0 倍）。

## 三、抽奖机制

| 机制 | 值 |
|---|---|
| 投入 | **2 张券**（右键街机使用） |
| 奖品池 | `GLOB.arcade_prize_pool`（70 种） |
| 权重 | 权重 2（43 种普通）/ 权重 1（27 种稀有） |
| 总权重 | **113** |
| 普通概率 | 2/113 ≈ **1.77%**（每种） |
| 稀有概率 | 1/113 ≈ **0.88%**（每种） |
| **百万大奖** | 0.01%（1/1,000,000）——**真脉冲步枪**（prize 版）+ 成就 |
| 心情 | 每次抽奖 +arcade 心情事件 |
| 游戏大师 | 传奇技能 + GAMERGOD 特质：**奖品翻倍** |

> 抽奖代码：`prizevend()` 里 `prob(0.0001)` 触发脉冲步枪，否则 `pick_weight(arcade_prize_pool)` 抽取。

## 四、奖品全录（70 种）

> 权重 2 = 普通（1.77%），权重 1 = 稀有（0.88%）。按类别分组。

### 4.1 玩具机甲（18 种）

| 类型 | 名称 | 权重 | 特性 |
|---|---|---|---|
| `/obj/item/toy/mecha/clarke` | 玩具机甲 Clarke（toy Clarke） | 1 | HP4；特殊技=通用(+1伤+1治)，战吼 "ROLL OUT" |
| `/obj/item/toy/mecha/darkgygax` | 玩具机甲 Dark Gygax（toy Dark Gygax） | 1 | HP6；特殊技=通用，战吼 "ULTRA SERVOS" |
| `/obj/item/toy/mecha/darkhonk` | 玩具暗黑H.O.N.K.（小丑机甲）（toy Dark H.O.N.K.） | 1 | 战斗HP 5（300 结构）；必杀技=攻击型（+2 伤害），喊话 **"BOMBANANA SPREE"**（香蕉炸弹连击）；专属音效 honkbot 邪恶笑声；可参与机甲对战/自杀吸魂 |
| `/obj/item/toy/mecha/deathripley` | 玩具死亡雷普利（toy Death-Ripley） | 1 | 战斗HP 5（250 结构）；必杀技=特殊型 **"KILLER CLAMP"**：若对手当前血量低于自己则**直接秒杀**（HP 清零），否则只造成 1 伤害；音效 sonic_jackhammer |
| `/obj/item/toy/mecha/durand` | 玩具机甲 Durand（toy Durand） | 1 | HP6；特殊技=治疗(+2)，战吼 "SHIELD OF PROTECTION" |
| `/obj/item/toy/mecha/firefighter` | 玩具消防员（toy Firefighter） | 1 | 战斗HP 5（250 结构）；必杀技=治疗型（+2 自疗），喊话 **"FIRE SHIELD"**（火盾）；源码注释 `//rip`（该机甲本体已被删的纪念梗） |
| `/obj/item/toy/mecha/gygax` | 玩具机甲 Gygax（toy Gygax） | 1 | HP5；特殊技=通用(+1伤+1治)，战吼 "SUPER SERVOS" |
| `/obj/item/toy/mecha/hauler` | 玩具机甲 Hauler（toy Hauler） | 1 | HP3；特殊技=通用(+1伤+1治)，战吼 "HAUL AWAY" |
| `/obj/item/toy/mecha/honk` | 玩具机甲 H.O.N.K.（toy H.O.N.K.） | 1 | HP4；特殊技=OTHER：对方特殊技 CD+3 并自愈 1；战吼 "MEGA HORN"，配 honkbot 邪笑声 |
| `/obj/item/toy/mecha/marauder` | 玩具掠夺者（toy Marauder） | 1 | 战斗HP 7（500 结构）；必杀技=攻击型（+2 伤害），喊话 **"BEAM BLAST"**（光束炮）；音效 marauder.ogg |
| `/obj/item/toy/mecha/mauler` | 玩具机甲 Mauler（toy Mauler） | 1 | HP7；特殊技=伤害(+2)，战吼 "BULLET STORM" |
| `/obj/item/toy/mecha/odysseus` | 玩具机甲 Odysseus（toy Odysseus） | 1 | HP4；特殊技=治疗(+2)，战吼 "MECHA BEAM" |
| `/obj/item/toy/mecha/phazon` | 玩具机甲 Phazon（toy Phazon） | 1 | HP6；特殊技=通用，战吼 "NO-CLIP" |
| `/obj/item/toy/mecha/reticence` | 玩具沉默者（缄默机甲）（toy Reticence） | 1 | 战斗HP 4（100 结构）；**quiet=TRUE 哑巴机甲**（战斗台词/讲话全静音）；必杀技=特殊型 "*wave"（挥手）：自冷却-1、对手冷却+1 并白嫖 1 伤害——克制型机甲 |
| `/obj/item/toy/mecha/ripley` | 玩具机甲 Ripley MK-I（toy Ripley MK-I） | 1 | 机甲对战系列：HP4；特殊技=伤害(+2)，战吼 "CLAMP SMASH" |
| `/obj/item/toy/mecha/ripleymkii` | 玩具机甲 Ripley MK-II（toy Ripley MK-II） | 1 | HP5；特殊技=伤害(+2)，战吼 "GIGA DRILL BREAK"（天元突破梗） |
| `/obj/item/toy/mecha/savannahivanov` | 玩具机甲 Savannah-Ivanov（toy Savannah-Ivanov） | 1 | HP7（本批最高）；特殊技=通用，战吼 "SKYFALL!! IVANOV STRIKE" |
| `/obj/item/toy/mecha/seraph` | 玩具炽天使（toy Seraph） | 1 | 战斗HP **8（550 结构，全机甲最高）**；必杀技=攻击型（+2 伤害），喊话 **"ROCKET BARRAGE"**（火箭弹幕） |

### 4.2 毛绒玩偶（7 种）

| 类型 | 名称 | 权重 | 特性 |
|---|---|---|---|
| `/obj/item/toy/plush/monkey` | 猴子毛绒（monkey plushie） | 2 | JUMBO 大号毛绒（W_BULKY）；攻击动词 Oops/Eeks；尖叫音效；彩蛋：喂 mimana（mime 香蕉）变法国猴 "peluche de singe"、喂蓝空香蕉会传送、吃香蕉后把香蕉皮砸向随机人并吼大猩猩叫 |
| `/obj/item/toy/plush/abductor/agent` | 外星特工毛绒（abductor agent plushie） | 2 | 手里粘着"惰性电击棒"；发声为电击手套/扎带声，彩蛋文案："我不会单独跟它待着" |
| `/obj/item/toy/plush/abductor` | 外星绑架者毛绒（abductor plushie） | 2 | 标签文字无法解读；发声为极微弱的灰烬风暴环境音（绑架者从不说话） |
| `/obj/item/toy/plush/moth` | 飞蛾毛绒（moth plushie） | 2 | 摸起来发出飞蛾人尖叫声；自杀彩蛋：每有人用它自杀计数，第 3 次后变为"神圣"不可摧毁 |
| `/obj/item/toy/plush/pkplush` | 维和机器人毛绒（peacekeeper plushie） | 2 | 和平卫士 cyborg 造型；攻击动词 hug/squeeze，发出 thudswoosh 音效 |
| `/obj/item/toy/plush/rouny` | 异形 Runner 毛绒（runner plushie） | 2 | 纪念 LV-426 战役百年；可被异形（xenomorph）持有（XENOMORPH_HOLDABLE） |
| `/obj/item/toy/plush/shark` | 鲨鱼毛绒（Blåhaj）（shark plushie） | 2 | 致敬宜家 Blåhaj：标签写着"hákarl"，由"古斯堪的纳维亚某家具厂"制造 |

### 4.3 说话玩具（4 种）

| 类型 | 名称 | 权重 | 特性 |
|---|---|---|---|
| `/obj/item/toy/talking/ai` | 玩具 AI 核心（toy AI） | 2 | 说话玩具：激活时随机"宣布"一条离子定律（ion law），30s 冷却 |
| `/obj/item/toy/talking/codex_gigas` | 玩具恶魔法典（Toy Codex Gigas） | 2 | 说话玩具：60s 冷却，随机念 6 条恶魔梗台词（"魔鬼真名是 Ian"） |
| `/obj/item/toy/talking/griffin` | 狮鹫（反派）手办（griffin action figure） | 2 | "The Griffin，犯罪主脑"手办。台词："You can't stop me, Owl!" / "My plan is flawless! The vault is mine!" / "Caaaawwww!" / "You will never catch me!"；与 owl 手办台词互相叫板（正邪对决彩蛋）；狮鹫叫声 30 秒冷却 |
| `/obj/item/toy/talking/owl` | 猫头鹰（夜枭）手办（owl action figure） | 2 | "The Owl，正义守护者"手办。按下播放台词："You won't get away this time, Griffin!" / "Stop right there, criminal!" / "Hoot! Hoot!" / "I am the night!"；会发出猫头鹰叫声（chatter 音效），30 秒冷却 |

### 4.4 武器玩具（8 种）

| 类型 | 名称 | 权重 | 特性 |
|---|---|---|---|
| `/obj/item/gun/ballistic/shotgun/toy/crossbow` | 泡沫弩（foam force crossbow） | 2 | 真枪械系统+泡沫镖弹仓；syringeproj 音效；无枪口闪光；可挂腰带 |
| `/obj/item/toy/gun` | 火帽枪（玩具左轮）（cap gun） | 2 | 7 发火帽，用玩具弹药装填；击发左轮声，空枪 *click*；可挂腰带 |
| `/obj/item/toy/minimeteor` | 迷你流星（Mini-Meteor） | 2 | "SweetMeat-eor Co." 出品。投掷落地：播放陨石撞击音效 + **摇晃 10 格内所有存活玩家镜头（3 秒）**；**彩蛋：emag 后投掷会在落点产生真爆炸（light_impact_range=1）** |
| `/obj/item/toy/nuke` | 玩具核弹（Nuclear Fission Explosive toy） | 2 | 塑料核弹模型。激活：播放全站核弹警报音（nuke_alarm.ogg），图标 3 段变化（idle→armed→cool），13.5 秒后冷却，1 分钟 CD。**彩蛋：被 emag 后激活会播放警报→14 秒后真·爆炸（light_impact_range=1）并自毁！** |
| `/obj/item/toy/sword` | 玩具光剑（toy sword） | 2 | 可开关变形（音效）；螺丝刀循环换色；多工具有 hack 彩虹彩蛋；两把互敲合成双头剑 |
| `/obj/item/toy/toy_xeno` | 异形手办（xenomorph action figure） | 2 | "MEGA 出品 Xenos Isolated 异形手办，附真实音效！"拉绳激活：图标变化 + 播放**真实异形嘶吼**（hiss1-4.ogg 随机），5 秒冷却 |
| `/obj/item/toy/foamfinger` | 泡沫手指（foam finger） | 2 | 啦啦队加油手指；使用后角色 5 秒冷却地左右/上下抖动画（"主队加油！"） |
| `/obj/item/toy/toy_dagger` | 玩具匕首（toy dagger） | 2 | 纯装饰塑料玩具匕首（THE ARM Toys, Inc. 出品），无伤害 |

### 4.5 服装与饰品（10 种）

| 类型 | 名称 | 权重 | 特性 |
|---|---|---|---|
| `/obj/item/clothing/under/syndicate/tacticool` | 战术高领毛衣（tacticool turtleneck） | 2 | 带传感器、顽固污渍；火抗 50/酸抗 40；染辛迪加色可"洗"成真·辛迪加制服 |
| `/obj/item/coin/antagtoken` | 反派代币（antag token） | 2 | 描述："A novelty coin that helps the heart know what hard evidence cannot prove."（帮你的心去感知证据无法证明之事——测谁是叛徒的玄学硬币）。**彩蛋：可研磨出 4u 蚂蚁试剂（ants）+ 4u 蛋黄（eggyolk）** |
| `/obj/item/toy/balloon/arrest` | 阿雷斯特气球（arreyst balloon） | 2 | 半瘪的怀旧男团气球，嘲讽红色连体服（安保）不时尚；固定配色 |
| `/obj/item/toy/spinningtoy` | 引力奇点旋转玩具（gravitational singularity） | 2 | "Singulo" 牌旋转玩具；自杀彩蛋：吞下后窒息死亡、塞入胸腔腔隙 |
| `/obj/item/toy/spinningtoy/dark_matter` | 暗物质奇点旋转玩具（dark matter singularity） | 1 | 同旋转玩具+自杀机制；desc 引用总工程师卖暗物质周边的梗 |
| `/obj/item/card/emagfake` | 假加密序列器（假电磁卡）（cryptographic sequencer） | 1 | Donk Co. 劣质复刻 emag；刷卡只响自行车喇叭；被真 emag 刷过后变"炸弹"，再碰目标 1 秒后小爆炸并自毁 |
| `/obj/item/clothing/glasses/trickblindfold` | 透明眼罩（blindfold） | 2 | 看似蒙眼实际全透明，作弊神器（"钉电击棒在 clown 身上的游戏"）；无失明效果 |
| `/obj/item/clothing/mask/party_horn` | 派对喇叭（party horn） | 2 | 戴嘴上，UI 动作"TOOT!"：10 秒冷却吹响 party_horn.ogg 并播放吹奏动画 |
| `/obj/item/clothing/shoes/kindle_kicks` | Kindle Kicks 发光鞋（Kindle Kicks） | 2 | 激活后鞋子随机 RGB 变色发光（2 格范围），15 轮×0.5 秒闪烁秀后熄灭 |
| `/obj/item/clothing/shoes/wheelys` | 轮滑鞋（Wheely-Heels） | 2 | 可伸缩轮子，穿鞋激活 UI 动作即弹出轮子变成可骑乘滑板车（加速移动），脱下自动收起 |

### 4.6 卡片与桌游（7 种）

| 类型 | 名称 | 权重 | 特性 |
|---|---|---|---|
| `/obj/item/toy/cards/deck` | 一叠扑克牌（deck of cards） | 2 | 太空级标准 52 张 + 2 张 Joker（含"Joker Clown"）；完整卡牌游戏系统（`code/modules/cards/deck/`）：可抽牌、发牌、洗牌（5 秒）、玩牌桌游戏；可挂腰带上；双击/双手持握有翻牌音效 |
| `/obj/item/toy/clockwork_watch` | 蒸汽朋克怀表（steampunk watch） | 2 | 数千小齿轮组成的蒸汽朋克怀表，可挂腰带（ITEM_SLOT_BELT）。激活：播放机械钟声（ark_activation.ogg），3 分钟冷却。**特殊：检视时显示服务器当前 IC 时间戳**（十二/二十四小时制按玩家偏好） |
| `/obj/item/toy/eightball` | 魔法八号球（magic eightball） | 2 | 摇一摇占卜：20 条经典预言（"It is certain"…"Very doubtful" 三档 10/5/5 条），5 秒摇动+10 秒冷却。**彩蛋：生成时 1% 概率变闹鬼版（haunted）——会"问死者"：30 秒摇动+3 分钟冷却，让附近幽灵/尸体投票给出答案（死灵占卜）** |
| `/obj/item/toy/windup_toolbox` | 发条工具箱（windup toolbox） | 2 | 绿色工具箱复制品（"rumbles when you turn the key"）。上发条后剧烈震动（抖动动画 + pope_entry 音效）**60 秒**，然后"哐当"一声停下恢复；当武器用攻击语是 "robusts"（大力敲人） |
| `/obj/item/toy/braintoy` | 吱吱响大脑（squeaky brain） | 2 | Mr. Monstrous 牌仿人脑手感玩具，捏一下发 blob 攻击音效（1 秒冷却） |
| `/obj/item/toy/brokenradio` | 坏收音机（broken radio） | 2 | 打开只会播 30 秒冷却的电流杂音（radiostatic.ogg） |
| `/obj/item/toy/eldritch_book` | 亵渎魔典玩具（Codex Cicatrix）（Codex Cicatrix） | 2 | 仿异端魔典：人造皮+大眼珠子封面，符文纯乱码；打开播放翻书动画 5 秒后自动合上 |

### 4.7 盒装与消耗品（15 种）

| 类型 | 名称 | 权重 | 特性 |
|---|---|---|---|
| `/obj/item/grenade/chem_grenade/glitter` | 白色闪光弹（white glitter grenade） | 1 | 基础款，glitter 白色；"For that somnolent glittery look."（催眠闪粉） |
| `/obj/item/grenade/chem_grenade/glitter/blue` | 蓝色闪光弹（blue glitter bomb） | 1 | 同上，glitter 蓝色（#4040ff）；"For that COOL glittery look." |
| `/obj/item/grenade/chem_grenade/glitter/pink` | 粉色闪光弹（pink glitter bomb） | 1 | 已装配好（GRENADE_READY 即拿即炸）；引爆喷洒 glitter 闪光试剂（粉 #ff8080）+ 钾/磷/糖配方；"For that HOT glittery look." |
| `/obj/item/restraints/handcuffs/fake` | 假手铐（fake handcuffs） | 2 | 恶作剧假手铐（"Fake handcuffs meant for gag purposes."）；拷上后 **1 秒即可挣脱**（breakouttime=1 SECONDS），弱束缚型；被捆者可快速反抗 |
| `/obj/item/stack/tile/eighties/loaded` | 复古地砖（15 块）（retro tile） | 2 | 80 年代迪斯科风地砖（`turf/open/floor/eighties`）；手持可**切换黑色/红色两种图案**（tile_reskin_types） |
| `/obj/item/stack/tile/fakepit/loaded` | 假坑地毯（30 块）（fake pits） | 2 | 强制透视错觉"假坑"地毯，铺出看起来会掉下去的坑（`turf/open/floor/fakepit`）；"绝对骗不到任何人！"；可燃烧 |
| `/obj/item/storage/box/actionfigure` | 手办盒（box of action figures） | 1 | 打开随机生成 **4 个职业手办**（从全部 `/obj/item/toy/figure` 子类型中抽，如 CMO/助理/大气技师等，共 20+ 种）；每个手办按一下会说职业梗台词（CMO: "Suit sensors!"、助理: "Greytide world wide!"、大气: "Glory to Atmosia!"） |
| `/obj/item/storage/box/fakesyndiesuit` | 盒装仿制太空服+头盔（boxed replica space suit and helmet） | 2 | 内含玩具辛迪加头套+外套（syndicatefake） |
| `/obj/item/storage/box/snappops` | 摔炮盒（snap pop box） | 2 | 内含 8 个摔炮；摔炮被扔出落地/被奔跑者踩到/遇火即爆（火花+灰烬+音效） |
| `/obj/item/extendohand/acme` | ACME 伸缩手（ACME Extendo-Hand） | 1 | ACME 公司搞笑玩具；射程 2 格"远程手掌"，可隔空用手交互/攻击 2 格外的物体与门 |
| `/obj/item/hot_potato/harmless/toy` | 无害烫手山芋玩具（hot potato (toy)） | 1 | Donk Co. 玩具版：倒计时后不爆炸只"激活"，可反复使用、不粘手、不强制附加，纯整蛊传球 |
| `/obj/item/storage/belt/military/snack/full` | 战术零食腰带（满装）（tactical snack rig） | 2 | 军规弹带造型但装零食；随机 5 个零食（Donk Pocket/薯片/玉米片等），赞助商标签随机（Donk Co./Waffle Corp./Gorlex…） |
| `/obj/item/storage/box/heretic_box` | 一盒"刺穿现实"玩具（box of pierced realities） | 1 | 内含 1~4 个 reality_pierce 玩具（"你以为是真的？哈！"） |
| `/obj/item/storage/box/party_poppers` | 一盒派对拉炮（box of party poppers） | 2 | 内含 5 个"party popper"小喷雾器：喷彩纸屑（confetti 试剂×15），"让清洁工忙个不停" |
| `/obj/item/storage/crayons` | 蜡笔盒（box of crayons） | 2 | 7 色蜡笔可画符文；空盒可拆成 cardboard 纸板（回收彩蛋） |

### 4.8 其他玩具（1 种）

| 类型 | 名称 | 权重 | 特性 |
|---|---|---|---|
| `/obj/item/toy/redbutton` | 大红按钮（big red button） | 2 | 大塑料红按钮，背面印着 **"From HonkCo Pranks!"**（小丑恶作剧公司梗）。按下：播放远处爆炸音效（explosionfar.ogg）+ 摇晃 10 格内所有存活玩家镜头（2 秒），30 秒冷却 |

## 五、彩蛋与特殊机制

### 5.1 百万大奖
- 0.01% 概率出**真脉冲步枪**（`/obj/item/gun/energy/pulse/prize`）——'1 in a million' 彩蛋 + 成就

### 5.2 机甲对战系统（18 种玩具机甲共享）
- 敲另一个手持机甲的人 = 发起对战（6 秒接受）；回合制互殴 + 5% 暴击
- 血量 < 1/3 且必杀技冷却完毕自动释放；每台机甲有专属必杀喊话
- **自杀吸魂**：拿机甲自杀会被真打死（450 brute），然后机甲 HP×1.5 变红变强

### 5.3 EMAG 彩蛋
- 战斗街机：击败 Cuban Pete → 真等离子炸弹 + 收藏帽（管理员广播）
- 猎户座：通关 → 爆炸玩具飞船
- 玩具核弹（nuke）：emag 后引爆 = 真爆炸
- 迷你流星：emag 后投掷 = 真爆炸
- 假 EMAG 奖品：被真 EMAG 刷过变定时炸弹，1 秒后小爆炸自毁

### 5.4 玩具梗彩蛋
- tacticool 毛衣染辛迪加色 = 变真辛迪加制服（卧底梗）
- 玩具光剑两把互敲合成双头剑；multitool 变彩虹色
- 飞蛾毛绒：第 3 次被用于自杀后变'神圣'全免疫不可摧毁
- 鲨鱼毛绒 = 宜家 Blåhaj 梗；猴子毛绒吃 mimana 变法国猴
- 玩具 AI 念随机离子定律；恶魔法典'魔鬼真名是 Ian'
- 8 号球 1% 概率是闹鬼版（问幽灵占卜）
- 反派代币可研磨出蚂蚁试剂 + 蛋黄

---

## 附录 · 代码路径索引

| 系统 | 文件 | 行数 |
|---|---|---|
| 街机基类（兑奖券/抽奖/重置） | `code/game/machinery/computer/arcade/_arcade.dm` | 108 |
| 战斗街机 | `arcade/battle.dm` | 581 |
| 截肢街机 | `arcade/amputation.dm` | 46 |
| 猎户座之旅 | `arcade/orion.dm` | ~500 |
| 机甲格斗 | `arcade/battle_gear.dm` | — |
| 猎户座事件 | `arcade/orion_event.dm` | — |
| **奖品池（70 种）** | `code/_globalvars/arcade.dm` | ~70 |
| 兑奖券 | `code/game/objects/items/stacks/tickets.dm` | ~20 |
| 玩具主体 | `code/game/objects/items/toys.dm` | 1,929 |
| 玩具机甲 | `code/game/objects/items/toy_mechs.dm` | — |
| 毛绒玩偶 | `code/game/objects/items/plushes.dm` | — |

---

> **索引**：本页共 1 篇，覆盖街机兑奖券系统全量（机制 + 70 奖品 + 彩蛋）。