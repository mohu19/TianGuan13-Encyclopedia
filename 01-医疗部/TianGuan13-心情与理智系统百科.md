# TianGuan13 心情与理智系统百科 (Mood & Sanity Encyclopedia)

> 基于 TianGuan13 NovaSector 分支源码全量整理。核心：`code/datums/mood.dm`（698 行）+ `code/datums/mood_events/`（14 文件 2,523 行）+ `code/controllers/subsystem/moods.dm` + `code/__DEFINES/mood.dm`。
> **范围**：心情（Mood）/理智（Sanity）核心机制、分级、HUD、营养心情、**530 个心情事件全录**（含心情值/超时/隐藏/触发源）、特殊机制。

## 目录

- [一、核心机制](#一核心机制)
- [二、心情分级（Mood Levels）](#二心情分级mood-levels)
- [三、理智分级（Sanity Levels）](#三理智分级sanity-levels)
- [四、营养心情](#四营养心情)
- [五、HUD 与显示](#五hud-与显示)
- [六、心情事件全录（530 个）](#六心情事件全录530-个)
- [附录 · 代码路径索引](#附录--代码路径索引)

---

## 一、核心机制

### 1.1 系统概述

| 属性 | 值 |
|---|---|
| 数据源 | `/datum/mood`（挂在 living mob 上） |
| 子系统 | `SSmood`（moods.dm：wait 1 秒、SS_BACKGROUND、priority 20） |
| 心情值 | `mood`（所有 moodlet 总和 × modifier） |
| 理智值 | `sanity`（0-150，受 mood 等级影响增减） |
| 事件列表 | `mood_events`（按 category 键控，同类互斥替换） |
| 修饰器 | mood_modifier / positive_mood_modifier / negative_mood_modifier / positive_moodlet_length_modifier / negative_moodlet_length_modifier |

### 1.2 心情事件（Mood Event）

| 字段 | 说明 |
|---|---|
| description | 事件描述（显示在心情栏） |
| mood_change | 心情值（正=开心，负=难过） |
| timeout | 持续时间（0=常驻直至清除） |
| hidden | 隐藏（不在心情栏显示，反派事件用） |
| category | 类别（同类互斥：新事件替换旧事件） |
| event_flags | 标志：ART 艺术 / WHIMSY 顽皮 / GAMING 游戏 / SPIRITUAL 灵性 / FEAR 恐惧 / FOOD 食物 / PAIN 痛苦 |
| required_job | 限定职业（非该职业不受影响） |
| special_screen_obj | 特殊屏幕图标（覆盖默认心情图标） |

### 1.3 心情计算

```
mood = Σ(所有 moodlet 的 mood_change × 正/负 modifier) × mood_modifier
shown_mood = Σ(非隐藏 moodlet × modifier) × mood_modifier
mood 值 → 按阈值映射到 mood_level（1-9 级）
```

- 有 `TRAIT_APATHETIC`（冷漠）时心情恒为 0（不计算）
- `add_mood_event` 同类替换：新事件覆盖旧事件（按 category）
- 条件事件（conditional）按优先级选择最高者

## 二、心情分级（Mood Levels）

| 等级 | mood_level | 心情值阈值 | 理智变化（/秒） |
|---|---|---|---|
| 狂喜 | HAPPY4 (9) | +15 以上 | +0.6 |
| 开心 | HAPPY3 (8) | +10 ~ +15 | +0.4 |
| 愉快 | HAPPY2 (7) | +6 ~ +10 | +0.3 |
| 不错 | HAPPY1 (6) | +2 ~ +6 | +0.2 |
| 中性 | NEUTRAL (5) | -3 ~ +2 | 0（补回至最低） |
| 低落 | SAD1 (4) | -3 ~ -7 | -0.05 |
| 难过 | SAD2 (3) | -7 ~ -15 | -0.1 |
| 悲伤 | SAD3 (2) | -15 ~ -20 | -0.15 |
| 绝望 | SAD4 (1) | -20 以下 | -0.3 |

### 2.1 低心情惩罚（insanity_effect）

| 等级 | 惩罚 | 效果 |
|---|---|---|
| SAD4（绝望） | **+10**（MAJOR_INSANITY_PEN） | 降低 crit_threshold（濒死阈值）：更早进入濒死 |
| SAD3（悲伤） | **+5**（MINOR_INSANITY_PEN） | 同上（较轻） |
| SAD2 以下 | 0 | 无惩罚 |

> `set_insanity_effect`：调整 mob 的 crit_threshold。NOVA 已移除低理智幻觉（NOVA EDIT REMOVAL，TODO 重新平衡）。

## 三、理智分级（Sanity Levels）

| 等级 | sanity 值 | sanity_level | 说明 |
|---|---|---|---|
| 极佳 | 125-150 | GREAT (1) | 上限 150 |
| 正常 | 100-125 | NEUTRAL (2) | 默认 100 |
| 不安 | 75-100 | DISTURBED (3) | — |
| 不稳 | 50-75 | UNSTABLE (4) | — |
| 疯狂 | 25-50 | CRAZY (5) | — |
| 疯癫 | 0-25 | INSANE (6) | 下限 0 |

> 理智随心情等级增减（见第二节表格）；`adjust_sanity(amount, min, max)` 带钳制。

## 四、营养心情

**代码**: `mood.dm` `update_nutrition_moodlets()` + `code/datums/mood_events/needs_events.dm`

| 营养档位 | 阈值 | 心情事件 | 值 |
|---|---|---|---|
| 过饱 | ≥550 (FULL) | too_wellfed（无 VORACIOUS）/ wellfed（有 VORACIOUS） | 0 / 8 |
| 饱腹 | 450-550 (WELL_FED~FULL) | wellfed | 8 |
| 正常 | 350-450 (FED~WELL_FED) | fed | 5 |
| 微饿 | 250-350 (HUNGRY~FED) | （无事件） | 0 |
| 饥饿 | 200-250 (VERY_HUNGRY~HUNGRY) | hungry | -3 |
| 很饿 | 150-200 (STARVING~VERY_HUNGRY) | hungry_very | -6 |
| 濒饿 | 0-150 | starving | -10 |

> 特质影响：TRAIT_FAT（肥胖）→ fat -6；TRAIT_GLUTTON（贪吃）→ 恒 hungry；TRAIT_VORACIOUS（暴食）→ 永不满足；TRAIT_NOHUNGER（无饥饿）→ 无事件。

---

## 六、心情事件全录（530 个）

> 全库 530 个 `/datum/mood_event/` 定义（含子类型）。主表列事件信息；**触发源见各节下方的对应表**（编号对应）。

### 6.1 通用正面事件（116 个）

| # | 事件 | 心情值 | 超时 | 隐藏 | 描述 |
|---|---|---|---|---|---|
| 1 | /datum/mood_event/hug | 1（孤僻-1/冷酷0） | 2 分钟 | 否 | 拥抱真好。"Hugs are nice." |
| 2 | /datum/mood_event/bear_hug | 1（孤僻/冷酷-2） | 2 分钟 | 否 | 我被紧紧勒住，但还挺舒服。"I got squeezed very tightly, but it was quite nice." |
| 3 | /datum/mood_event/betterhug | 3（孤僻1/冷酷0） | 4 分钟 | 否 | 有人对我非常好。[朋友名] 对我非常好。"Someone was very nice to me." / "[friend.name] was very nice to me." |
| 4 | /datum/mood_event/besthug | 5（孤僻2/冷酷0） | 4 分钟 | 否 | 有人陪伴真是太棒了，让我好开心！"Someone is great to be around, they make me feel so happy!" |
| 5 | /datum/mood_event/warmhug | 1（孤僻/冷酷0） | 2 分钟 | 否 | 温暖舒适的拥抱最棒了！"Warm cozy hugs are the best!" |
| 6 | /datum/mood_event/tailpulled | 1（孤僻/冷酷-2） | 2 分钟 | 否 | 我喜欢尾巴被拉的感觉！"I love getting my tail pulled!" |
| 7 | /datum/mood_event/arcade | 3（勤勉/尽职-1） | 8 分钟 | 否 | 我打赢了街机游戏！【GAMING】"I beat the arcade game!" |
| 8 | /datum/mood_event/blessing | 1 | 8 分钟 | 否 | 我受到了祝福。【SPIRITUAL】"I've been blessed." |
| 9 | /datum/mood_event/maintenance_adaptation | 8 | 0 | 否 | [神祇] 帮助我适应了维护通道！"GLOB.deity has helped me adapt to the maintenance shafts!" |
| 10 | /datum/mood_event/book_nerd | 1（博学2/未受教育-1） | 5 分钟 | 否 | 我最近读了一本书。"I have recently read a book." |
| 11 | /datum/mood_event/exercise | 依体质浮动（懒惰×-0.5、非运动×0.5） | 0 | 否 | 锻炼会释放内啡肽！"Working out releases those endorphins!" |
| 12 | /datum/mood_event/pet_animal | 动态（厌恶动物-1；犬/猫迷或爱动物者+3；默认+1） | 5 分钟 | 否 | 动物太可爱了！我忍不住一直摸！"Animals are adorable! I can't stop petting them!" |
| 13 | /datum/mood_event/honk | 2 | 4 分钟 | 否 | 我被"哔"了！【WHIMSY】屏幕特效：honked_nose（红鼻子）"I've been honked!" |
| 14 | /datum/mood_event/saved_life | 6（冷酷0/厌世-1） | 8 分钟 | 否 | 救人性命的感觉真好。"It feels good to save a life." |
| 15 | /datum/mood_event/oblivious | 3 | 0 | 否 | 多么美好的一天。"What a lovely day." |
| 16 | /datum/mood_event/jolly | 6 | 2 分钟 | 否 | 我无缘无故地感到快乐。"I feel happy for no particular reason." |
| 17 | /datum/mood_event/focused | 8 | 0 | 是 | 我有一个目标，无论如何都要达成！（供反派专注目标）"I have a goal, and I will reach it, whatever it takes!" |
| 18 | /datum/mood_event/badass_antag | 8 | 0 | 是 | 我是个该死的狠角色，周围所有人都知道。看他们吓得发抖的样子。屏幕特效：badass_sun |
| 19 | /datum/mood_event/ling | 12 | 0 | 是 | 我们有一个目标，无论如何都会达成！"We have a goal, and we will reach it, whatever it takes!" |
| 20 | /datum/mood_event/creeping | 18 | 3 秒 | 是 | 那些声音终于松开了对我心智的钩爪！我感觉自由了！（靠近执念对象时）"The voices have released their hooks on my mind! I feel free again!" |
| 21 | /datum/mood_event/creeping/dead | 8 | 0 | 是 | （执念对象死亡后的变体，心情回落） |
| 22 | /datum/mood_event/revolution | 3 | 0 | 是 | 革命万岁！"VIVA LA REVOLUTION!" |
| 23 | /datum/mood_event/cult | 12 | 0 | 是 | 我看到了真相，赞美至高者！"I have seen the truth, praise the almighty one!" |
| 24 | /datum/mood_event/heretics | 12 | 0 | 是 | 我升得越高，看得越远。"THE HIGHER I RISE, THE MORE I SEE." |
| 25 | /datum/mood_event/rift_fishing | 6 | 5 分钟 | 否 | 我钓得越多，升得越高。"THE MORE I FISH, THE HIGHER I RISE." |
| 26 | /datum/mood_event/blood_worm | 999 | 0 | 是 | 杀、吞、繁、胜。（血虫宿主无感情，数值无实际意义，仅用于置顶特效）"KILL, CONSUME, MULTIPLY, CONQUER." |
| 27 | /datum/mood_event/family_heirloom | 1 | 0 | 否 | 我的家传宝物在我身边很安全。"My family heirloom is safe with me." |
| 28 | /datum/mood_event/clown_enjoyer_pin | 1 | 0 | 否 | 我喜欢展示我的小丑徽章！"I love showing off my clown pin!" |
| 29 | /datum/mood_event/mime_fan_pin | 1 | 0 | 否 | 我喜欢展示我的哑剧徽章！"I love showing off my mime pin!" |
| 30 | /datum/mood_event/goodmusic | 3 | 60 秒 | 否 | 这音乐有某种抚慰人心的东西。【ART】"There is something soothing about this music." |
| 31 | /datum/mood_event/chemical_euphoria | 4 | 0 | 否 | 嘿……嘿嘿嘿……嘿嘿……"Heh...hehehe...hehe..." |
| 32 | /datum/mood_event/chemical_laughter | 4 | 3 分钟 | 否 | 笑真的是最好的药！是吗？"Laughter really is the best medicine! Or is it?" |
| 33 | /datum/mood_event/chemical_superlaughter | 12 | 3 分钟 | 否 | *喘不上气*"*WHEEZE*" |
| 34 | /datum/mood_event/religiously_comforted | 3 | 5 分钟 | 否 | 圣职者的存在让我感到安慰。【SPIRITUAL】"I feel comforted by the presence of a holy person." |
| 35 | /datum/mood_event/clownshoes | 3 | 0 | 否 | 这双鞋是小丑的传承，我永远不想脱下它们！"The shoes are a clown's legacy, I never want to take them off!" |
| 36 | /datum/mood_event/sacrifice_good | 5 | 3 分钟 | 否 | 众神对这份祭品很满意！【SPIRITUAL】"The gods are pleased with this offering!" |
| 37 | /datum/mood_event/artok | 2 | 5 分钟 | 否 | 看到周围有人在创作艺术真好。【ART】"It's nice to see people are making art around here." |
| 38 | /datum/mood_event/artgood | 4 | 5 分钟 | 否 | 多么发人深省的艺术品，我会记很久。【ART】"What a thought-provoking piece of art. I'll remember that for a while." |
| 39 | /datum/mood_event/artgreat | 6 | 5 分钟 | 否 | 那件作品伟大到让我相信人性的善良——在这种地方实属不易。【ART】"That work of art was so great it made me believe in the goodness of humanity. Says a lot in a place like this." |
| 40 | /datum/mood_event/bottle_flip | 2 | 3 分钟 | 否 | 瓶子那样落地真令人满足。"The bottle landing like that was satisfying." |
| 41 | /datum/mood_event/hope_lavaland | 6（悲观0） | 0 | 否 | 多么奇特的徽章，它让我对未来充满希望。"What a peculiar emblem. It makes me feel hopeful for my future." |
| 42 | /datum/mood_event/confident_mane | 2 | 0 | 否 | 拥有一头浓密头发让我充满自信。"I'm feeling confident with a head full of hair." |
| 43 | /datum/mood_event/holy_consumption | 1 | 3 分钟 | 否 | 真的，那是神之食物！（1+喜欢食物5=接近 jolly）"Truly, that was the food of the Divine!" |
| 44 | /datum/mood_event/high_five | 2 | 45 秒 | 否 | 我喜欢击掌！"I love getting high fives!" |
| 45 | /datum/mood_event/helped_up | 动态（冷酷/厌世-2；共情+2） | 45 秒 | 否 | 扶起他们感觉真好！（或：他们应该自己爬起来）"Helping them up felt good!" |
| 46 | /datum/mood_event/high_ten | 3 | 45 秒 | 否 | 太棒了！高十击掌！【WHIMSY】"AMAZING! A HIGH-TEN!" |
| 47 | /datum/mood_event/down_low | 4 | 90 秒 | 否 | 哈！那个傻瓜，根本没机会……（击掌落空羞辱对方）【WHIMSY】"HA! What a rube, they never stood a chance..." |
| 48 | /datum/mood_event/aquarium_positive | 3 | 90 秒 | 否 | 看水族箱里的鱼令人平静。"Watching fish in an aquarium is calming." |
| 49 | /datum/mood_event/gondola | 6 | 0 | 否 | 我感到平静，没有冲动做任何突然或鲁莽的事。"I feel at peace and feel no need to make any sudden or rash actions." |
| 50 | /datum/mood_event/kiss | 1.5 | 2 分钟 | 否 | 有人朝我飞吻，我一定很有魅力！（直接亲吻则："[名字] 亲了我，啊！！"）"Someone blew a kiss at me, I must be a real catch!" |
| 51 | /datum/mood_event/honorbound | 4 | 0 | 否 | 遵循我的荣誉守则令人满足！"Following my honorbound code is fulfilling!" |
| 52 | /datum/mood_event/et_pieces | 50 | 10 分钟 | 否 | 嗯……我爱花生酱……（ET 糖果）"Mmm... I love peanut butter..." |
| 53 | /datum/mood_event/memories_of_home | 3 | 5 分钟 | 否 | 这个味道莫名让人怀念……"This taste seems oddly nostalgic..." |
| 54 | /datum/mood_event/observed_soda_spill | 2 | 30 秒 | 否 | 啊哈哈！看人被汽水喷一脸总是很好笑！【WHIMSY】"Ahaha! It's always funny to see someone get sprayed by a can of soda." |
| 55 | /datum/mood_event/gaming | 2（勤勉/尽职-1） | 30 秒 | 否 | 我在享受一场愉快的游戏！【GAMING】"I'm enjoying a nice gaming session!" |
| 56 | /datum/mood_event/gamer_won | 6 | 5 分钟 | 否 | 我爱赢游戏！【GAMING】"I love winning video games!" |
| 57 | /datum/mood_event/love_reagent | 5（悲观0；超时随药效时长） | 动态 | 否 | 这食物让我想起美好的旧时光。"This food reminds me of the good ol' days." |
| 58 | /datum/mood_event/won_52_card_pickup | 3 | 3 分钟 | 否 | 哈！那个输家要捡好久的牌！【WHIMSY+GAMING】"HA! That loser will be picking cards up for a long time!" |
| 59 | /datum/mood_event/playing_cards | 2×玩家人数（上限约 6 人） | 3 分钟 | 否 | 我喜欢和别人一起打牌！【GAMING】"I'm enjoying playing cards with other people!" |
| 60 | /datum/mood_event/garland | 1 | 0 | 否 | 这些花很抚慰人心。"These flowers are rather soothing." |
| 61 | /datum/mood_event/russian_roulette_win | 2（赌徒 2，否则 1.8 的装弹数幂，四舍五入） | 5 分钟 | 否 | 我拿命赌了一把并且赢了！我还活着真是幸运……"I gambled my life and won! I'm lucky to be alive..." |
| 62 | /datum/mood_event/fishing | 4 | 3 分钟 | 否 | 钓鱼很放松。"Fishing is relaxing." |
| 63 | /datum/mood_event/fish_released | 1 | 2 分钟 | 否 | 去吧，鱼儿，游向自由！（病态视角有特殊文案）"Go, fish, swim and be free!" |
| 64 | /datum/mood_event/fish_petting | 2 | 2 分钟 | 否 | 摸鱼的感觉真好。【WHIMSY】"It felt nice to pet the fish." |
| 65 | /datum/mood_event/kobun | 14 | 10 秒 | 否 | 你们都被宇宙所爱。我并不孤单，你们也是。"You are all loved by the Universe. I'm not alone, and you aren't either." |
| 66 | /datum/mood_event/sabrage_success | 2 | 4 分钟 | 否 | 我成功完成了马刀开香槟！当个显摆鬼感觉真好。"I pulled that sabrage stunt off! Feels good to be a show-off." |
| 67 | /datum/mood_event/sabrage_witness | 1 | 2 分钟 | 否 | 我目睹有人用相当狂野的方式撬开香槟瓶塞。"I saw someone pop the cork off a champagne bottle in quite a radical fashion." |
| 68 | /datum/mood_event/it_was_on_the_mouse | 1 | 2 分钟 | 否 | 嘿嘿。"就在鼠标上"。一语双关。"Heh heh. \"It's on the mouse\". What a play on words." |
| 69 | /datum/mood_event/birthday | 2 | 0 | 否 | 今天是我的生日！屏幕特效：birthday |
| 70 | /datum/mood_event/basketball_score | 2 | 5 分钟 | 否 | 唰！空心入网。【WHIMSY+GAMING】"Swish! Nothing but net." |
| 71 | /datum/mood_event/basketball_dunk | 2 | 5 分钟 | 否 | 灌篮！轰，沙卡拉卡！【WHIMSY+GAMING】"Slam dunk! Boom, shakalaka!" |
| 72 | /datum/mood_event/moon_smile | 10 | 2 分钟 | 否 | 月亮向我展示真相，它的微笑正对着我！！！"THE MOON SHOWS ME THE TRUTH AND ITS SMILE IS FACED TOWARDS ME!!!" |
| 73 | /datum/mood_event/madness_elation | 200 | 0 | 否 | 疯狂才是最伟大的祝福……（巫师终局仪式施法者专属）"Madness truly is the greatest of blessings..." |
| 74 | /datum/mood_event/prophat | 2 | 0 | 否 | 这顶帽子让我充满奇思妙想的喜悦！【WHIMSY】"This hat fills me with whimsical joy!" |
| 75 | /datum/mood_event/slots | 1（赌徒×2；勤勉/尽职-1） | 1 分钟 | 否 | 去赌博吧！【GAMING】"Let's go gambling!" |
| 76 | /datum/mood_event/slots/win | 2 | 5 分钟 | 否 | 啊耶，我赢了！【GAMING】"Aw yeah I won!"（be_replaced：新事件心情值更低时阻止替换） |
| 77 | /datum/mood_event/slots/win/big | 3 | 10 分钟 | 否 | （大胜，继承父级描述） |
| 78 | /datum/mood_event/slots/win/jackpot | 4 | 0 | 否 | 头奖！啊耶！【GAMING】"JACKPOT! AW YEAH!"（可覆盖 all_gone 的封锁） |
| 79 | /datum/mood_event/slots/all_gone | -2 | 20 分钟 | 否 | 不不不不！全没了！！！（负值，卡内余额被清空）"NOOOOOOO! IT'S ALL GONE!!!" |
| 80 | /datum/mood_event/empathetic_happy | 2 | 2 分钟 | 否 | 看到快乐的人让我也快乐。"Seeing happy people makes me happy." |
| 81 | /datum/mood_event/misanthropic_happy | 2 | 2 分钟 | 否 | 看到难过的人让我高兴。"Seeing sad people makes me glad." |
| 82 | /datum/mood_event/paranoid/alone | 1 | 0 | 否 | 安静祥和，没有人在附近威胁我。"Peace and quiet, no one around to threaten me." |
| 83 | /datum/mood_event/paranoid/small_group | 2 | 0 | 否 | 在这个小团体里我感觉更安全，我们互相照应。"I feel safer in this small group. We've got each other's backs." |
| 84 | /datum/mood_event/nt_loyalist | 2 | 0 | 否 | 作为 NT™ 大家庭的一员我感到自豪！"I feel proud to be part of the NT™ family!" |
| 85 | /datum/mood_event/loyalist_revs_lost | 4 | 10 分钟 | 否 | 革命被击败了！Nanotrasen 万岁！"The revolution was defeated! Long live the Nanotrasen!" |
| 86 | /datum/mood_event/disillusioned_revs_win | 4 | 10 分钟 | 否 | 革命成功了！革命万岁！"The revolution was a success! Viva la revolution!" |
| 87 | /datum/mood_event/enjoying_department_area | 1 | 0 | 否 | 我爱我的工作。"I love my job." |
| 88 | /datum/mood_event/slacking_off_lazy | 1 | 0 | 否 | 老板赚一块，我赚一毛。所以我上班摸鱼。"Boss makes a dollar, I make a dime. That's why I slack on company time." |
| 89 | /datum/mood_event/working_diligent | 1 | 0 | 否 | 努力工作本身就是回报。"Working hard is its own reward." |
| 90 | /datum/mood_event/creative_patronage | 2 | 5 分钟 | 否 | 支持艺术家！"Support artists!" |
| 91 | /datum/mood_event/creative_framing | 2 | 5 分钟 | 否 | 挂起艺术品让房间更有格调。"Hanging up art really ties the room together." |
| 92 | /datum/mood_event/creative_sculpting | 2 | 5 分钟 | 否 | 雕刻是很棒的创作出口。"Sculpting is a great creative outlet." |
| 93 | /datum/mood_event/whimsical_slip | 3 | 2 分钟 | 否 | 哈哈！那家伙摔倒了！【WHIMSY】"Haha! That guy fell over!" |
| 94 | /datum/mood_event/bibulous_hangover | 2 | 10 分钟 | 否 | 多棒的夜晚！我等不及再来一次！（好酒之徒宿醉）"What a night! I can't wait to do it all again!" |
| 95 | /datum/mood_event/gizmo_positive | 3 | 30 秒 | 否 | 我听到一个声音在我耳边低语善意的话语！"I hear a voice whispering kind words in my ear!" |
| 96 | /datum/mood_event/horridroom | -5 | 0 | 否 | 这房间糟透了！"This room looks terrible!" |
| 97 | /datum/mood_event/badroom | -3 | 0 | 否 | 这房间看起来真的很糟糕。"This room looks really bad." |
| 98 | /datum/mood_event/ehroom | -1 | 0 | 否 | 这房间看起来有点糟。"This room looks kinda bad." |
| 99 | /datum/mood_event/decentroom | 1 | 0 | 否 | 这房间看起来还行。"This room looks alright." |
| 100 | /datum/mood_event/goodroom | 3 | 0 | 否 | 这房间真漂亮！"This room looks really pretty!" |
| 101 | /datum/mood_event/greatroom | 5 | 0 | 否 | 这房间美极了！"This room is beautiful!" |
| 102 | /datum/mood_event/area | 0（由区域 mood_bonus 动态传入） | 0 | 否 | 描述留空，由所在 /area/ 的 mood_message 填充。"（Fill this out in the area）" |
| 103 | /datum/mood_event/axe_gone | -15 | 0 | 否 | 斧头怎么了……它在哪？不可能……"What happened to the axe... Where is it? It can't be..." |
| 104 | /datum/mood_event/axe_neutral | 1 | 0 | 否 | 我相信斧头没事。"I'm sure the axe is okay." |
| 105 | /datum/mood_event/axe_cabinet | 5 | 0 | 否 | 斧头回到了它该在的地方。"The axe is where it belongs." |
| 106 | /datum/mood_event/axe_missing | -5 | 0 | 否 | 斧头，有点不对劲……"The axe, there's something wrong..." |
| 107 | /datum/mood_event/axe_held | -10 | 0 | 否 | 我不配拥有你，斧头。我得把你放回去。"I'm not worthy of you, axe. I need to put you back." |
| 108 | /datum/mood_event/eldritch_painting | -6 | 3 分钟 | 否 | 自从砍下那幅画，我就一直听到奇怪的笑声……"I've been hearing weird laughter since cutting down that painting..." |
| 109 | /datum/mood_event/eldritch_painting/weeping | -3 | 11 秒 | 否 | 他在这里！"He is here!" |
| 110 | /datum/mood_event/eldritch_painting/weeping_heretic | 5 | 3 分钟 | 否 | 他的痛苦激励着我！"His suffering inspires me!" |
| 111 | /datum/mood_event/eldritch_painting/weeping_withdrawal | 1 | 3 分钟 | 否 | 我头脑清醒。他不在这里。"My mind is clear. He is not here." |
| 112 | /datum/mood_event/eldritch_painting/desire_heretic | -2 | 3 分钟 | 否 | 虚空在尖叫。"The void screams." |
| 113 | /datum/mood_event/eldritch_painting/desire_examine | 3 | 3 分钟 | 否 | 饥饿被喂饱了，暂时……"The hunger has been fed, for now..." |
| 114 | /datum/mood_event/eldritch_painting/heretic_vines | 3 | 3 分钟 | 否 | 哦，多可爱的花！"Oh what a lovely flower!" |
| 115 | /datum/mood_event/eldritch_painting/rust_examine | -2 | 3 分钟 | 否 | 那幅画真把我吓坏了。"That painting really creeped me out." |
| 116 | /datum/mood_event/eldritch_painting/rust_heretic_examine | 6 | 3 分钟 | 否 | 攀爬。腐朽。锈蚀。"Climb. Decay. Rust." |

#### 触发源对应表


| # | 事件 | 触发源 |
|---|---|---|
| 1 | /datum/mood_event/hug | `carbon_defense.dm:403`（被拥抱）；`toys.dm:1788`（玩具 HELP 交互） |
| 2 | /datum/mood_event/bear_hug | `carbon_defense.dm:398`（熊抱） |
| 3 | /datum/mood_event/betterhug | `carbon_defense.dm:426`（好友拥抱） |
| 4 | /datum/mood_event/besthug | `carbon_defense.dm:424`（挚友拥抱） |
| 5 | /datum/mood_event/warmhug | `carbon_defense.dm:402,405`（温暖拥抱，双方） |
| 6 | /datum/mood_event/tailpulled | `carbon_defense.dm:266`（被拉尾巴） |
| 7 | /datum/mood_event/arcade | `_arcade.dm:87`（街机获胜） |
| 8 | /datum/mood_event/blessing | `bibles.dm:97,236`；`instrument_rites.dm:127`；`religion_sects.dm:143,181,206,217,312,414,467`（宗教祝福/仪式） |
| 9 | /datum/mood_event/maintenance_adaptation | `rites.dm:258`（宗教仪式：维护适应） |
| 10 | /datum/mood_event/book_nerd | `book.dm:123`（读书） |
| 11 | /datum/mood_event/exercise | `buffs.dm:256,261`（锻炼增益，按运动技能等级） |
| 12 | /datum/mood_event/pet_animal | `corgi.dm:225`（抚摸柯基犬） |
| 13 | /datum/mood_event/honk | `clown_items.dm:227`（被小丑喇叭哔） |
| 14 | /datum/mood_event/saved_life | `defib.dm:653`（除颤救人）；`human.dm:606`（CPR 救人） |
| 15 | /datum/mood_event/oblivious | `mild.dm:62`（脑损伤"笨拙"创伤） |
| 16 | /datum/mood_event/jolly | `jolly.dm:15`（快乐怪癖） |
| 17 | /datum/mood_event/focused | 反派 `antag_moodlet`：`brother.dm:11`、`ert.dm:12`、`operative.dm:8`、`space_ninja.dm:10`、`spy.dm:8`、`datum_traitor.dm:11`、`wizard.dm:11` |
| 18 | /datum/mood_event/badass_antag | `toys.dm:278`（反派捡起辛迪加气球） |
| 19 | /datum/mood_event/ling | `changeling.dm:12`（变形人 antag_moodlet） |
| 20 | /datum/mood_event/creeping | `creepy_trauma.dm:81`（跟踪狂创伤，靠近执念对象） |
| 21 | /datum/mood_event/creeping/dead | `creepy_trauma.dm:77`（执念对象死亡） |
| 22 | /datum/mood_event/revolution | `revolution.dm:7`（革命者 antag_moodlet） |
| 23 | /datum/mood_event/cult | `cultist.dm:6`（ cult 教徒 antag_moodlet） |
| 24 | /datum/mood_event/heretics | `heretic_antag.dm:23`；`heretic_monsters.dm:7`（异端/异端怪物 antag_moodlet） |
| 25 | /datum/mood_event/rift_fishing | `rifts.dm:152`（裂缝钓鱼） |
| 26 | /datum/mood_event/blood_worm | `blood_worm_antag.dm:8`（血虫宿主 antag_moodlet） |
| 27 | /datum/mood_event/family_heirloom | `family_heirloom.dm:65`（家传宝物怪癖） |
| 28 | /datum/mood_event/clown_enjoyer_pin | `badges.dm:47`（佩戴小丑爱好者徽章） |
| 29 | /datum/mood_event/mime_fan_pin | `badges.dm:75`（佩戴哑剧爱好者徽章） |
| 30 | /datum/mood_event/goodmusic | `buffs.dm:377`（听好音乐增益） |
| 31 | /datum/mood_event/chemical_euphoria | `life.dm:425`；`_lungs.dm:699`（吸入麻醉气体等欣快） |
| 32 | /datum/mood_event/chemical_laughter | `drink_reagents.dm:162`（笑气饮料） |
| 33 | /datum/mood_event/chemical_superlaughter | `drink_reagents.dm:178`（大笑气/高浓度） |
| 34 | /datum/mood_event/religiously_comforted | `examine.dm:228`（被圣职者检查/安抚） |
| 35 | /datum/mood_event/clownshoes | `clown.dm:28`；`mod_service.dm:85`（穿小丑鞋） |
| 36 | /datum/mood_event/sacrifice_good | `ashwalker.dm:43`；`ash_walker_den.dm:91`（献祭成功） |
| 37 | /datum/mood_event/artok | `art.dm:30`（欣赏一般艺术品） |
| 38 | /datum/mood_event/artgood | `art.dm:27`（欣赏佳作） |
| 39 | /datum/mood_event/artgreat | `art.dm:24,60,84`（欣赏杰作） |
| 40 | /datum/mood_event/bottle_flip | `drinks.dm:304`（水瓶立稳挑战成功） |
| 41 | /datum/mood_event/hope_lavaland | `pandora.dm:190`（潘多拉精英怪掉落的希望徽章） |
| 42 | /datum/mood_event/confident_mane | `bald.dm:83`（秃头怪癖戴假发时） |
| 43 | /datum/mood_event/holy_consumption | `melon.dm:157`（食用神圣甜瓜） |
| 44 | /datum/mood_event/high_five | `high_fiver.dm:82,83`（击掌成功，双方） |
| 45 | /datum/mood_event/helped_up | `hand_items.dm:421,422`（扶人/被扶起，仅共情/冷酷/厌世人格触发） |
| 46 | /datum/mood_event/high_ten | `high_fiver.dm:71,72`（高十击掌，双方） |
| 47 | /datum/mood_event/down_low | `alert.dm:550`（击掌骗局成功） |
| 48 | /datum/mood_event/aquarium_positive | `aquarium.dm:719`（观赏水族箱） |
| 49 | /datum/mood_event/gondola | `debuffs.dm:630`（贡多拉平静状态） |
| 50 | /datum/mood_event/kiss | `hand_items.dm:612,650`（飞吻/亲吻） |
| 51 | /datum/mood_event/honorbound | `honorbound_trauma.dm:22`（荣誉守则创伤） |
| 52 | /datum/mood_event/et_pieces | `food_reagents.dm:1251`（食用 ET 糖果） |
| 53 | /datum/mood_event/memories_of_home | `alcohol_reagents.dm:2578`（特殊酒"家乡的味道"） |
| 54 | /datum/mood_event/observed_soda_spill | `soda.dm:147`（旁观汽水喷人） |
| 55 | /datum/mood_event/gaming | `living.dm:2889`（玩游戏） |
| 56 | /datum/mood_event/gamer_won | `gamer.dm:47`（游戏宅怪癖获胜） |
| 57 | /datum/mood_event/love_reagent | `other_reagents.dm:3371,3378`（爱情药水试剂） |
| 58 | /datum/mood_event/won_52_card_pickup | `deck.dm:217`（52 张拾牌游戏获胜） |
| 59 | /datum/mood_event/playing_cards | `deck.dm:138`（打牌） |
| 60 | /datum/mood_event/garland | `garlands.dm:10`（戴花环） |
| 61 | /datum/mood_event/russian_roulette_win | `revolver.dm:350`（俄罗斯轮盘赌幸存） |
| 62 | /datum/mood_event/fishing | `fishing_minigame.dm:240`（钓鱼小游戏） |
| 63 | /datum/mood_event/fish_released | `_fish_source.dm:585,587`（放生鱼） |
| 64 | /datum/mood_event/fish_petting | `_fish.dm:1611`（抚摸鱼） |
| 65 | /datum/mood_event/kobun | `dog_subtypes.dm:130`（与 kobun 犬类接触） |
| 66 | /datum/mood_event/sabrage_success | `glassbottle.dm:754`（马刀开香槟成功） |
| 67 | /datum/mood_event/sabrage_witness | `glassbottle.dm:756`（目击马刀开香槟） |
| 68 | /datum/mood_event/it_was_on_the_mouse | `rat_organs.dm:144`（老鼠器官植入者） |
| 69 | /datum/mood_event/birthday | `neutral_traits.dm:228`（生日） |
| 70 | /datum/mood_event/basketball_score | `hoop.dm:169`（篮球投篮得分） |
| 71 | /datum/mood_event/basketball_dunk | `hoop.dm:110`（扣篮） |
| 72 | /datum/mood_event/moon_smile | `mind_gate.dm:56`（异端魔法 Mind Gate） |
| 73 | /datum/mood_event/madness_elation | `cheese.dm:47`（巫师终局仪式奶酪结局施法者） |
| 74 | /datum/mood_event/prophat | `soft_caps.dm:199`（佩戴螺旋桨帽） |
| 75 | /datum/mood_event/slots | `roulette_machine.dm:228`；`slotmachine.dm:339`（玩老虎机/轮盘） |
| 76 | /datum/mood_event/slots/win | `slotmachine.dm:452`（老虎机获胜） |
| 77 | /datum/mood_event/slots/win/big | `slotmachine.dm:446`；`roulette_machine.dm:273`（大额获胜） |
| 78 | /datum/mood_event/slots/win/jackpot | `slotmachine.dm:428`；`roulette_machine.dm:273`（中头奖） |
| 79 | /datum/mood_event/slots/all_gone | `account.dm:137`（账户余额被取空） |
| 80 | /datum/mood_event/empathetic_happy | `mood.dm:197`（情绪反应：附近有人获得正面心情时，共情人格） |
| 81 | /datum/mood_event/misanthropic_happy | `mood.dm:203`（情绪反应：附近有人获得负面心情时，厌世人格） |
| 82 | /datum/mood_event/paranoid/alone | `reaction_to_groups.dm:41`（偏执人格独处时） |
| 83 | /datum/mood_event/paranoid/small_group | `reaction_to_groups.dm:45`（偏执人格在小群体中） |
| 84 | /datum/mood_event/nt_loyalist | `reaction_to_nt.dm:31`（忠诚派 NT 人格 mood_event_type） |
| 85 | /datum/mood_event/loyalist_revs_lost | `revolution_handler.dm:107`（革命失败时旁观者中的忠诚派） |
| 86 | /datum/mood_event/disillusioned_revs_win | `revolution_handler.dm:78`（革命胜利时旁观者中的幻灭派） |
| 87 | /datum/mood_event/enjoying_department_area | `reaction_to_job.dm:10`（部门人格：身处本部门工作区域） |
| 88 | /datum/mood_event/slacking_off_lazy | `reaction_to_recreation.dm:44`（懒惰人格 mood_event_type，休闲区） |
| 89 | /datum/mood_event/working_diligent | `reaction_to_recreation.dm:60,72`（勤勉人格在工作区） |
| 90 | /datum/mood_event/creative_patronage | `paintings.dm:310`（赞助艺术家） |
| 91 | /datum/mood_event/creative_framing | `paintings.dm:651`（装裱挂画） |
| 92 | /datum/mood_event/creative_sculpting | `statues.dm:412`（雕刻雕像） |
| 93 | /datum/mood_event/whimsical_slip | `mob_movement.dm:387`（目睹他人滑倒，顽皮人格） |
| 94 | /datum/mood_event/bibulous_hangover | `landmarks.dm:595`（好酒之徒出生点宿醉） |
| 95 | /datum/mood_event/gizmo_positive | `gizmood.dm:30`（gizmo 正向情绪脉冲装置） |
| 96 | /datum/mood_event/horridroom | `mood.dm:548,562`（区域美感极差） |
| 97 | /datum/mood_event/badroom | `mood.dm:545,565`（区域美感差） |
| 98 | /datum/mood_event/ehroom | `mood.dm:542`（区域美感偏差） |
| 99 | /datum/mood_event/decentroom | `mood.dm:571`（区域美感尚可） |
| 100 | /datum/mood_event/goodroom | `mood.dm:554,573`（区域美感佳） |
| 101 | /datum/mood_event/greatroom | `mood.dm:552,575`（区域美感极佳） |
| 102 | /datum/mood_event/area | `mood.dm:527`（进入带 mood_bonus 的区域时，以 new_area.mood_bonus/mood_message 填充） |
| 103 | /datum/mood_event/axe_gone | `special.dm:618,659`（斧头丢失） |
| 104 | /datum/mood_event/axe_neutral | `special.dm:611,625,644,700,703`（斧头状态正常） |
| 105 | /datum/mood_event/axe_cabinet | `special.dm:640,696`（斧头在柜中） |
| 106 | /datum/mood_event/axe_missing | `special.dm:614,629`（斧头失踪） |
| 107 | /datum/mood_event/axe_held | `special.dm:670`（手持斧头） |
| 108 | /datum/mood_event/eldritch_painting | `eldritch_painting.dm:59`（撕毁异端画作） |
| 109 | /datum/mood_event/eldritch_painting/weeping | `debuffs.dm:320`（异端"哭泣"诅咒状态） |
| 110 | /datum/mood_event/eldritch_painting/weeping_heretic | `eldritch_painting.dm:105`（异端检查哭泣画作） |
| 111 | /datum/mood_event/eldritch_painting/weeping_withdrawal | `eldritch_painting.dm:100`（非异端检查哭泣画作） |
| 112 | /datum/mood_event/eldritch_painting/desire_heretic | `eldritch_painting.dm:153`（异端检查欲望画作） |
| 113 | /datum/mood_event/eldritch_painting/desire_examine | `eldritch_painting.dm:130`（非异端检查欲望画作） |
| 114 | /datum/mood_event/eldritch_painting/heretic_vines | `eldritch_painting.dm:197`（异端藤蔓画作） |
| 115 | /datum/mood_event/eldritch_painting/rust_examine | `eldritch_painting.dm:250`（非异端检查锈蚀画作） |
| 116 | /datum/mood_event/eldritch_painting/rust_heretic_examine | `eldritch_painting.dm:254`（异端检查锈蚀画作） |

---

### 6.2 通用负面事件（135 个）

| # | 事件 | 心情值 | 超时 | 隐藏 | 描述 |
|---|---|---|---|---|---|
| 1 | `handcuffed` | -1 | 永久 | — | **我猜我的恶作剧终于自食其果了。**<br><small>I guess my antics have finally caught up with me.</small> |
| 2 | `broken_vow` | -4 | 3 MINUTES | — | **我给自己的名字蒙羞，违背了哑剧演员的神圣誓言，背叛了同胞……**<br><small>I have brought shame upon my name, and betrayed my fellow mimes by breaking our sacred vow...</small> |
| 3 | `on_fire`<br><small>FEAR</small> | -12 | 永久 | — | **我着火了！！！**<br><small>I'M ON FIRE!!!</small> |
| 4 | `suffocation`<br><small>FEAR</small> | -12 | 永久 | — | **无法……呼吸……**<br><small>CAN'T... BREATHE...</small> |
| 5 | `burnt_thumb` | -1 | 2 MINUTES | — | **我不该玩打火机的……**<br><small>I shouldn't play with lighters...</small> |
| 6 | `cold` | -5 | 永久 | — | **这里太冷了。**<br><small>It's way too cold in here.</small> |
| 7 | `hot` | -5 | 永久 | — | **这里开始热起来了。**<br><small>It's getting hot in here.</small> |
| 8 | `creampie`<br><small>WHIMSY</small> | -2 | 3 MINUTES | — | **我被糊了一脸奶油。尝起来像派的味道。**<br><small>I've been creamed. Tastes like pie flavor.</small> |
| 9 | `inked` | -3 | 3 MINUTES | — | **我被泼了鱿鱼墨汁。尝起来像盐。**<br><small>I've been splashed with squid ink. Tastes like salt.</small> |
| 10 | `slipped`<br><small>WHIMSY</small> | -2 | 3 MINUTES | — | **我滑倒了。下次该小心点……**<br><small>I slipped. I should be more careful next time...</small> |
| 11 | `eye_stab` | -4 | 3 MINUTES | — | **我以前也像你一样是个冒险者，直到我的眼睛被螺丝刀捅了。**<br><small>I used to be an adventurer like you, until I took a screwdriver to the eye.</small> |
| 12 | `delam` | -2 | 4 MINUTES | — | **那些该死的工程师什么都干不好……**<br><small>Those goddamn engineers can't do anything right...</small> |
| 13 | `cascade` | -8 | 5 MINUTES | — | **我从没想过会亲眼看到共振级联，更别说亲身经历了……**<br><small>I never thought I'd see a resonance cascade, let alone experience one...</small> |
| 14 | `depression` | -12 | 2 MINUTES | — | **我无缘无故地感到难过。**<br><small>I feel sad for no particular reason.</small> |
| 15 | `shameful_suicide` | -15 | 60 SECONDS | — | **我连自我了断都做不到！**<br><small>I can't even end it all!</small> |
| 16 | `dismembered` | -10 | 8 MINUTES | — | **啊！！我的肢体！我正用着呢！**<br><small>AHH! MY LIMB! I WAS USING THAT!</small> |
| 17 | `reattachment`<br><small>PAIN</small> | -3 | 2 MINUTES | — | **哎哟！我的肢体感觉像睡着时压麻了一样。**<br><small>Ouch! My limb feels like I fell asleep on it.</small> |
| 18 | `tased` | -3 | 2 MINUTES | — | **「泰瑟枪」里没有「z」，它在电击里。**<br><small>"There's no \"z\" in \"taser\". It's in the zap."</small> |
| 19 | `embedded` | -7 | 永久 | — | **把它拔出来！**<br><small>Pull it out!</small> |
| 20 | `table` | -2 | 2 MINUTES | — | **有人把我扔到桌子上！**<br><small>Someone threw me on a table!</small> |
| 21 | `table_limbsmash`<br><small>PAIN</small> | -3 | 3 MINUTES | — | **那该死的桌子，真疼……**<br><small>That fucking table, man that hurts...</small> |
| 22 | `brain_damage` | -3 | 永久 | — | **（脑损伤随机台词）Hurr durr……** |
| 23 | `hulk` | -4 | 永久 | — | **浩克砸！**<br><small>HULK SMASH!</small> |
| 24 | `epilepsy` | -3 | 5 MINUTES | — | **我早该注意癫痫警告的。**<br><small>I should have paid attention to the epilepsy warning.</small> |
| 25 | `photophobia`<br><small>FEAR</small> | -3 | 永久 | — | **灯光太刺眼了……**<br><small>The lights are too bright...</small> |
| 26 | `nyctophobia`<br><small>FEAR</small> | -3 | 永久 | — | **这里真够黑的……**<br><small>It sure is dark around here...</small> |
| 27 | `claustrophobia`<br><small>FEAR</small> | -7 | 1 MINUTES | — | **为什么我觉得被困住了？！放我出去！！！**<br><small>Why do I feel trapped?! Let me out!!!</small> |
| 28 | `bright_light` | -12 | 永久 | — | **我讨厌亮光……我需要找个更暗的地方……**<br><small>I hate it in the light... I need to find a darker place...</small> |
| 29 | `family_heirloom_missing` | -4 | 永久 | — | **我的传家宝不见了……**<br><small>I'm missing my family heirloom...</small> |
| 30 | `healsbadman` | -4 | 2 MINUTES | — | **我感觉自己像被细线拼凑起来的，随时都可能散架！**<br><small>I feel like I'm held together by flimsy string, and could fall apart at any moment!</small> |
| 31 | `healsbadman/long_term` | 继承父类(healsbadman) | 10 MINUTES | — | **（继承父类描述，长时间版）** |
| 32 | `jittery` | -2 | 永久 | — | **我紧张不安、坐立难安！！**<br><small>I'm nervous and on edge and I can't stand still!!</small> |
| 33 | `choke`<br><small>FEAR</small> | -10 | 永久 | — | **我喘不上气！！！**<br><small>I CAN'T BREATHE!!!</small> |
| 34 | `vomit` | -2 | 2 MINUTES | — | **我刚吐了。真恶心。**<br><small>I just threw up. Gross.</small> |
| 35 | `vomitself` | -4 | 3 MINUTES | — | **我吐了自己一身。太恶心了。**<br><small>I just threw up all over myself. This is disgusting.</small> |
| 36 | `painful_medicine`<br><small>PAIN</small> | -5 | 60 SECONDS | — | **药可能对我有好处，但现在疼得要命。**<br><small>Medicine may be good for me but right now it stings like hell.</small> |
| 37 | `startled`<br><small>FEAR</small> | -1 | 1 MINUTES | — | **听到那个词让我想到了可怕的事。**<br><small>Hearing that word made me think about something scary.</small> |
| 38 | `phobia`<br><small>FEAR</small> | -4 | 4 MINUTES | — | **我看到了非常可怕的东西！**<br><small>I saw something very frightening!</small> |
| 39 | `spooked`<br><small>FEAR</small> | -4 | 4 MINUTES | — | **那些骨头的咔哒声……至今仍萦绕在我心头。**<br><small>The rattling of those bones... It still haunts me.</small> |
| 40 | `notcreeping`<br><small>隐藏</small> | -6 | 3 SECONDS | ✔ | **那些声音不高兴了，它们痛苦地扭曲我的思绪，逼我回到任务上。**<br><small>The voices are not happy, and they painfully contort my thoughts into getting back on task.</small> |
| 41 | `notcreepingsevere` | -30 | 3 SECONDS | — | **它们需要执念！！**<br><small>THEY NEEEEEEED OBSESSIONNNN!!</small> |
| 42 | `tower_of_babel` | -1 | 15 SECONDS | — | **我的交流能力变成了一团混乱的胡言乱语……**<br><small>My ability to communicate is an incoherent babel...</small> |
| 43 | `back_pain`<br><small>PAIN</small> | -15 | 永久 | — | **背包从没在我背上放对位置，疼得要命！**<br><small>Bags never sit right on my back, this hurts like hell!</small> |
| 44 | `sacrifice_bad`<br><small>SPIRITUAL</small> | -5 | 2 MINUTES | — | **那些该死的野蛮人！**<br><small>Those darn savages!</small> |
| 45 | `artbad`<br><small>ART</small> | -2 | 2 MINUTES | — | **我用屁股都能产出比那更好的艺术品。**<br><small>I've produced better art than that from my ass.</small> |
| 46 | `graverobbing` | -8 | 3 MINUTES | — | **我刚亵渎了某人的坟墓……我简直不敢相信自己做了那种事……**<br><small>I just desecrated someone's grave... I can't believe I did that...</small> |
| 47 | `deaths_door` | -20 | 永久 | — | **就是现在了……我真的要死了。**<br><small>This is it... I'm really going to die.</small> |
| 48 | `gunpoint`<br><small>FEAR</small> | -10 | 永久 | — | **这家伙疯了！我最好小心点……**<br><small>This guy is insane! I better be careful...</small> |
| 49 | `tripped` | -5 | 2 MINUTES | — | **我简直不敢相信自己中了最老套的招！**<br><small>I can't believe I fell for the oldest trick in the book!</small> |
| 50 | `untied` | -3 | 60 SECONDS | — | **我讨厌鞋带松开的感觉！**<br><small>I hate when my shoes come untied!</small> |
| 51 | `gates_of_mansus`<br><small>FEAR</small> | -25 | 4 MINUTES | — | **我瞥见了这个世界之外的恐怖。现实在我眼前展开！**<br><small>I HAD A GLIMPSE OF THE HORROR BEYOND THIS WORLD. REALITY UNCOILED BEFORE MY EYES!</small> |
| 52 | `high_five_full_hand` | -1 | 45 SECONDS | — | **天哪，我连击掌都不会……**<br><small>Oh god, I don't even know how to high-five correctly...</small> |
| 53 | `too_slow` | -2 | 2 MINUTES | — | **不！我怎么会……太慢了？？？**<br><small>NO! HOW COULD I BE... TOO SLOW???</small> |
| 54 | `surgery`<br><small>FEAR</small> | -8 | 永久 | — | **他们在把我开膛破肚！！**<br><small>THEY'RE CUTTING ME OPEN!!</small> |
| 55 | `surgery/success` | 继承父类(surgery) | 3 MINUTES | — | **那场手术真的很疼……不过庆幸成功了，大概吧……**<br><small>That surgery really hurt... Glad it worked, I guess...</small> |
| 56 | `surgery/failure` | 继承父类(surgery) | 10 MINUTES | — | **啊啊啊啊啊！他们把我活生生地切了！**<br><small>AHHHHHGH! THEY FILLETED ME ALIVE!</small> |
| 57 | `bald` | -3 | 永久 | — | **我需要点东西遮住脑袋……**<br><small>I need something to cover my head...</small> |
| 58 | `bald_reminder` | -5 | 4 MINUTES | — | **有人提醒我头发再也长不回来了！太糟糕了！**<br><small>I was reminded that I can't grow my hair back at all! This is awful!</small> |
| 59 | `bad_touch` | -3 | 4 MINUTES | — | **我不喜欢别人碰我。**<br><small>I don't like when people touch me.</small> |
| 60 | `very_bad_touch` | -5 | 4 MINUTES | — | **我非常不喜欢别人碰我。**<br><small>I really don't like when people touch me.</small> |
| 61 | `noogie` | -2 | 60 SECONDS | — | **嗷！这就像太空高中时代重演……**<br><small>Ow! This is like space high school all over again...</small> |
| 62 | `noogie_harsh` | -4 | 60 SECONDS | — | **嗷！！比普通钻头槌还疼！**<br><small>OW!! That was even worse than a regular noogie!</small> |
| 63 | `aquarium_negative` | -3 | 90 SECONDS | — | **所有的鱼都死了……**<br><small>All the fish are dead...</small> |
| 64 | `tail_lost` | -8 | 10 MINUTES | — | **我的尾巴！！为什么？！**<br><small>My tail!! Why?!</small> |
| 65 | `tail_balance_lost` | -2 | 永久 | — | **没有尾巴我感觉失去平衡。**<br><small>I feel off-balance without my tail.</small> |
| 66 | `tail_regained_wrong` | -12 | 5 MINUTES | — | **这是什么恶心的玩笑？！这不是我的尾巴。**<br><small>Is this some kind of sick joke?! This is NOT the right tail.</small> |
| 67 | `tail_regained_species` | -5 | 5 MINUTES | — | **这条尾巴不是我的，但至少让我平衡了点……**<br><small>This tail is not mine, but at least it balances me out...</small> |
| 68 | `tail_regained_right` | -2 | 5 MINUTES | — | **我的尾巴回来了，但那太惊悚了……**<br><small>My tail is back, but that was traumatic...</small> |
| 69 | `burnt_wings` | -10 | 10 MINUTES | — | **我珍贵的翅膀！！**<br><small>MY PRECIOUS WINGS!!</small> |
| 70 | `holy_smite` | -5 | 5 MINUTES | — | **我被我的神明惩罚了！**<br><small>I have been punished by my deity!</small> |
| 71 | `heresy` | -5 | 5 MINUTES | — | **这么多异端横行，我简直喘不过气！**<br><small>I can hardly breathe with all this HERESY going on!</small> |
| 72 | `soda_spill` | -2 | 1 MINUTES | — | **酷！没关系，我本来就打算把汽水穿在身上，而不是喝掉……**<br><small>Cool! That's fine, I wanted to wear that soda, not drink it...</small> |
| 73 | `watersprayed` | -1 | 30 SECONDS | — | **我讨厌被水喷！**<br><small>I hate being sprayed with water!</small> |
| 74 | `gamer_withdrawal`<br><small>GAMING</small> | -5 | 永久 | — | **真希望我现在正在打游戏……**<br><small>I wish I was gaming right now...</small> |
| 75 | `gamer_lost`<br><small>GAMING</small> | -6 | 10 MINUTES | — | **如果我电子游戏玩不好，还能算真正的玩家吗？**<br><small>If I'm not good at video games, can I truly call myself a gamer?</small> |
| 76 | `lost_52_card_pickup`<br><small>WHIMSY+GAMING</small> | -3 | 3 MINUTES | — | **太丢人了！我都不好意思捡地上的牌……**<br><small>This is really embarrassing! I'm ashamed to pick up all these cards off the floor...</small> |
| 77 | `russian_roulette_lose_cheater` | -10 | 10 MINUTES | — | **我赌输了！还好我没瞄准自己的头……**<br><small>I gambled and lost! Good thing I wasn't aiming for my head...</small> |
| 78 | `russian_roulette_lose` | -20 | 10 MINUTES | — | **我拿命赌输了！我想这就是终点了……**<br><small>I gambled my life and lost! I guess this is the end...</small> |
| 79 | `bad_touch_bear_hug` | -1 | 2 MINUTES | — | **我刚刚被抱得太紧了。**<br><small>I just got squeezed way too hard.</small> |
| 80 | `rippedtail` | -5 | 30 SECONDS | — | **我把他们的尾巴扯了下来，我都干了什么！**<br><small>I ripped their tail right off, what have I done!</small> |
| 81 | `sabrage_fail` | -2 | 4 MINUTES | — | **该死！那招没按计划来！**<br><small>Blast it! That stunt didn't go as planned!</small> |
| 82 | `body_purist` | 继承父类(mood_event) | 永久 | — | **我感觉身上装着机械义体，我恨透了！**<br><small>I feel cybernetics attached to me, and I HATE IT!</small> |
| 83 | `unsatisfied_nomad` | -3 | 永久 | — | **我在这里待太久了！我想出去探索太空！**<br><small>I've been here too long! I want to go out and explore space!</small> |
| 84 | `moon_insanity`<br><small>FEAR</small> | -3 | 5 MINUTES | — | **月亮审判了我，认定我有所欠缺！！！**<br><small>THE MOON JUDGES AND FINDS ME WANTING!!!</small> |
| 85 | `amulet_insanity`<br><small>FEAR</small> | -6 | 5 MINUTES | — | **我看見了光，必須阻止它！**<br><small>I sEe THe LiGHt, It mUsT BE stOPPed!</small> |
| 86 | `mallet_humiliation` | -3 | 10 SECONDS | — | **被这么蠢的武器击中，真丢人……**<br><small>Getting hit by such a stupid weapon feels rather humiliating...</small> |
| 87 | `madness_despair`<br><small>特殊屏幕:mood_despair</small> | -200 | 永久 | — | **不配，不配，不配！！！**<br><small>UNWORTHY, UNWORTHY, UNWORTHY!!!</small> |
| 88 | `all_nighter` | -5 | 永久 | — | **我昨晚一宿没睡。我筋疲力尽。**<br><small>I didn't sleep at all last night. I'm exhausted.</small> |
| 89 | `desentized`<br><small>特殊屏幕:mood_desentized</small> | -3 | 永久 | — | **没有什么能比得上我过去见过的场面……**<br><small>Nothing will ever rival what I've seen in the past...</small> |
| 90 | `pacifism_bypassed` | -20 | 10 MINUTES | — | **我不是故意要伤害他们的！**<br><small>I DIDN'T MEAN TO HURT THEM!</small> |
| 91 | `bapped` | -1 | 3 MINUTES | — | **嗷……我的头，我现在觉得自己有点傻！**<br><small>Ow.. my head, I feel a bit foolish now!</small> |
| 92 | `encountered_evil` | -1 | 1 MINUTES | — | **我不愿相信，但世上确实存在真正邪恶的人。**<br><small>I didn't want to believe it, but there are people out there that are genuinely evil.</small> |
| 93 | `smoke_in_face` | -3 | 30 SECONDS | — | **香烟的烟真恶心。**<br><small>Cigarette smoke is disgusting.</small> |
| 94 | `slots/loss`<br><small>GAMING</small> | -2 | 5 MINUTES | — | **唉，真倒霉！**<br><small>Aww dang it!</small> |
| 95 | `lost_control_of_life` | -5 | 5 MINUTES | — | **我失去了对生活的掌控。**<br><small>I've lost control of my life.</small> |
| 96 | `empathetic_sad` | -2 | 3 MINUTES | — | **看到悲伤的人让我也难过。**<br><small>Seeing sad people makes me sad.</small> |
| 97 | `misanthropic_sad` | -2 | 3 MINUTES | — | **看到开心的人让我不安。**<br><small>Seeing happy people makes me uneasy.</small> |
| 98 | `paranoid/one_on_one`<br><small>FEAR</small> | -3 | 永久 | — | **我和某人独处——万一他们想杀我怎么办？**<br><small>I'm alone with someone - what if they want to kill me?</small> |
| 99 | `paranoid/large_group`<br><small>FEAR</small> | -3 | 永久 | — | **周围人太多了——其中任何一个都可能是来害我的！**<br><small>There are so many people around - any one of them could be out to get me!</small> |
| 100 | `nt_disillusioned` | -2 | 永久 | — | **我讨厌这家公司，以及它所代表的一切。**<br><small>I hate the company, and everything it stands for.</small> |
| 101 | `disillusioned_revs_lost` | -2 | 10 MINUTES | — | **革命失败了……真「好」啊。**<br><small>The revolution was defeated... greaaaat.</small> |
| 102 | `loyalist_revs_win` | -2 | 10 MINUTES | — | **革命成功了……这会影响季度利润的。**<br><small>The revolution was a success... This will hurt quarterly profits.</small> |
| 103 | `slacking_off_diligent` | -1 | 永久 | — | **我该回去工作了。**<br><small>I should get back to work.</small> |
| 104 | `unimaginative_patronage` | -2 | 5 MINUTES | — | **那感觉就像白花钱。**<br><small>That felt like a waste of money.</small> |
| 105 | `unimaginative_framing` | -2 | 5 MINUTES | — | **我本该在那里挂点更有用的东西。**<br><small>I could've hung something more useful there.</small> |
| 106 | `unimaginative_sculpting` | -2 | 5 MINUTES | — | **那感觉就像浪费材料。**<br><small>That felt like a waste of materials.</small> |
| 107 | `splattered_with_blood` | -4 | 4 MINUTES | — | **呕，我全身沾满了血！**<br><small>Eugh, I just got coated in blood!</small> |
| 108 | `teetotal_hangover` | -4 | 10 MINUTES | — | **真是丢人的表现！这就是纵情饮酒的下场！**<br><small>What a disgraceful display! This is what happens when one indulges in alcohol!</small> |
| 109 | `normal_hangover` | 0 | 10 MINUTES | — | **唉，真是个难忘的夜晚。**<br><small>Ugh, what a night.</small> |
| 110 | `jabbed_with_tester` | -4 | 5 MINUTES | — | **天哪，被那玩意儿扎一下可真难受。**<br><small>Man, getting jabbed with that thing really sucked.</small> |
| 111 | `gizmo_negative` | -3 | 30 SECONDS | — | **我听到一个低语的声音，我不喜欢它说的话。**<br><small>I hear a voice whispering, and I don't like what it says.</small> |
| 112 | `conditional/see_death` | -8 | 5 MINUTES | — | **我刚刚看到 %DEAD_MOB% 死了。真可怕……（根据死者/死亡方式动态生成）** |
| 113 | `conditional/see_death/naive`<br><small>优先级:NAIVE_PRIORITY</small> | 0 | 5 MINUTES | — | **好好睡一觉吧，%DEAD_MOB%。（天真者把死亡当睡觉）** |
| 114 | `conditional/see_death/cult`<br><small>优先级:CULT_PRIORITY</small> | +4 (= -8 × -0.5) | 5 MINUTES | — | **几何之神又多了个灵魂！（邪教徒视死亡为乐）**<br><small>More souls for the Geometer!</small> |
| 115 | `conditional/see_death/revolutionary`<br><small>优先级:REVOLUTIONARY_PRIORITY</small> | +4 (= -8 × -0.5) | 5 MINUTES | — | **「XXX」死了！革命万岁！（革命者见部门主管死亡而欣喜）** |
| 116 | `conditional/see_death/gamer`<br><small>优先级:GAMER_PRIORITY</small> | +4 (= -8 × -0.5) | 5 MINUTES | — | **又有一个倒下了！（游戏职业者不在乎死亡）**<br><small>Another one bites the dust!</small> |
| 117 | `conditional/see_death/dontcare`<br><small>优先级:DONTCARE_PRIORITY</small> | 0 | 2.5 MINUTES (×0.5) | — | **哦，%DEAD_MOB% 死了。真可惜，大概吧。（冷漠者无感）** |
| 118 | `conditional/see_death/ashwalker`<br><small>优先级:ASHWALKER_PRIORITY</small> | 0（gibbed 时 +4） | 5 MINUTES | — | **哦，%DEAD_MOB% 死了。真可「惜」啊。（灰烬行者：献祭给 Necropolis 时反而愉悦）** |
| 119 | `conditional/see_death/pet`<br><small>优先级:PET_PRIORITY</small> | -8（animal_friend ×1.5 / 非 compassionate ×0.25） | 5 MINUTES | — | **%DEAD_MOB% 刚刚死了！！（宠物死亡，优先级最高档）** |
| 120 | `conditional/see_death/xeno`<br><small>优先级:XENO_PRIORITY</small> | 正数（基础 -8 反转为 +，按异形类型 0.1~0.75 倍） | 5 MINUTES × modifier | — | **看到异形死后，我体内有什么在翻涌。（异形宿主/看到异形死反而高兴）** |
| 121 | `conditional/see_death/desensitized`<br><small>优先级:DESENSITIZED_PRIORITY</small> | -8 × desensitized 系数 | 2.5 MINUTES (×0.5) | — | **我看到 %DEAD_MOB% 死了。（麻木者：效果减半）** |
| 122 | `morbid_dismemberment` | 2 | 2 MINUTES | — | **没有什么比干净利落地肢解更令人满足了！**<br><small>Nothing quite as satisfying as a clean dismemberment!</small> |
| 123 | `morbid_dissection_success` | 2 | 2 MINUTES | — | **我为我的工作感到自豪。没人能像我这样解剖尸体。**<br><small>I take pride in my work. Nobody can dissect a body quite like I can.</small> |
| 124 | `morbid_abominable_surgery_success` | 2 | 2 MINUTES | — | **毕加索用画笔都难以企及我用刀做出的作品。**<br><small>Picasso himself would struggle to match with a brush what I can do with a knife.</small> |
| 125 | `morbid_revival_success` | 6 | 8 MINUTES | — | **它活了！啊哈哈哈哈！！**<br><small>IT LIVES! AH HA HA HA HA!!</small> |
| 126 | `morbid_graverobbing` | 2 | 2 MINUTES | — | **死人用不着财产。而我，活得好好的，非常需要。**<br><small>The dead have no need for possessions. I, on the other hand, am very much alive and very much in need.</small> |
| 127 | `morbid_hauntium` | 3 | 6 MINUTES | — | **我与灵魂的联系更紧密了，我喜欢这样！**<br><small>I feel a better connection with the spirits, I love this!</small> |
| 128 | `morbid_aquarium_good` | 3 | 90 SECONDS | — | **嘿嘿，所有的鱼都在「睡觉」……**<br><small>Eh eh, all the fish are sleeping...</small> |
| 129 | `morbid_tend_wounds` | -2 | 2 MINUTES | — | **为什么我要把才华浪费在这种琐事上？照料活人是白费力气。**<br><small>Why must I waste my talents on this trivial nonsense? Tending to breathers is a waste of effort.</small> |
| 130 | `morbid_saved_life` | -6 | 2 MINUTES | — | **我本可以用他们的尸体做更多事，而不是救他们那没用的命。真扫兴。**<br><small>I could have done so much more with their corpse than I could have saving their useless life. Dreadful.</small> |
| 131 | `morbid_aquarium_bad` | -3 | 90 SECONDS | — | **看水族箱里的鱼真无聊。**<br><small>Watching fish in an aquarium is lousy.</small> |
| 132 | `it_was_on_the_mouse`<br><small>WHIMSY</small> | 1 | 2 MINUTES | — | **嘿嘿。「它就在鼠标上」。双关语真妙。**<br><small>"Heh heh. \"It's on the mouse\". What a play on words."</small> |
| 133 | `gondola_serenity`<br><small>特殊屏幕:mood_gondola</small> | 10 | 永久 | — | **此刻你心头可能有很多事。但这种满足感，一种静坐旁观的普世召唤，正涌遍你的全身……**<br><small>There's a lot that could be on your mind right now. But this feeling of contentedness, a universal calling to simply sit back and observe is washing over you...</small> |
| 134 | `fish_waterless` | -3 | 永久 | — | **离开水真难受。我感觉自己像条离水的鱼。**<br><small>It sucks to be dry. I feel like a fish out of water.</small> |
| 135 | `fish_water` | 1 | 永久 | — | **咕噜咕噜！**<br><small>Glug glug!</small> |

#### 触发源对应表


| # | 事件 | 触发源 |
|---|---|---|
| 1 | `handcuffed` | modules/mob/living/carbon/carbon.dm |
| 2 | `broken_vow` | modules/spells/spell_types/self/mime_vow.dm |
| 3 | `on_fire`<br><small>FEAR</small> | datums/status_effects/debuffs/fire_stacks.dm |
| 4 | `suffocation`<br><small>FEAR</small> | modules/mob/living/carbon/life.dm |
| 5 | `burnt_thumb` | game/objects/items/lighter.dm |
| 6 | `cold` | modules/mob/living/carbon/human/_species.dm |
| 7 | `hot` | modules/mob/living/carbon/human/_species.dm |
| 8 | `creampie`<br><small>WHIMSY</small> | datums/components/face_decal.dm, datums/components/splat.dm |
| 9 | `inked` | modules/fishing/fish/fish_traits.dm, modules/fishing/fish/types/saltwater.dm, modules/food_and_drinks/food/misc.dm, modules/food_and_drinks/food/packaged.dm, modules/projectiles/projectile/special/spit.dm |
| 10 | `slipped`<br><small>WHIMSY</small> | modules/mob/mob_movement.dm |
| 11 | `eye_stab` | datums/elements/eyestab.dm |
| 12 | `delam` | modules/power/supermatter/supermatter_delamination/delamination_effects.dm |
| 13 | `cascade` | modules/power/supermatter/supermatter_delamination/delamination_effects.dm |
| 14 | `depression` | datums/quirks/negative_quirks/depression.dm |
| 15 | `shameful_suicide` | modules/client/verbs/suicide.dm |
| 16 | `dismembered` | modules/surgery/bodyparts/dismemberment.dm |
| 17 | `reattachment`<br><small>PAIN</small> | modules/surgery/bodyparts/dismemberment.dm |
| 18 | `tased` | modules/projectiles/guns/magic/wands/wand_zap.dm, modules/projectiles/projectile/energy/stun.dm |
| 19 | `embedded` | datums/embedding.dm |
| 20 | `table` | datums/elements/table_smash.dm, modules/clothing/suits/reactive_armour.dm |
| 21 | `table_limbsmash`<br><small>PAIN</small> | datums/elements/table_smash.dm |
| 22 | `brain_damage` | modules/mob/living/brain/brain_item.dm |
| 23 | `hulk` | datums/mutations/hulk.dm |
| 24 | `epilepsy` | datums/mutations/body.dm |
| 25 | `photophobia`<br><small>FEAR</small> | datums/quirks/negative_quirks/photophobia.dm |
| 26 | `nyctophobia`<br><small>FEAR</small> | datums/components/fearful/sources/_sources.dm |
| 27 | `claustrophobia`<br><small>FEAR</small> | datums/components/fearful/sources/_sources.dm |
| 28 | `bright_light` | modules/reagents/withdrawal/generic_addictions.dm |
| 29 | `family_heirloom_missing` | datums/quirks/negative_quirks/family_heirloom.dm |
| 30 | `healsbadman` | modules/mining/equipment/monster_organs/regenerative_core.dm, modules/mob/living/basic/lavaland/legion/legion_tumour.dm |
| 31 | `healsbadman/long_term` | modules/mob/living/basic/lavaland/legion/legion.dm |
| 32 | `jittery` | datums/status_effects/debuffs/jitteriness.dm |
| 33 | `choke`<br><small>FEAR</small> | datums/status_effects/debuffs/choke.dm |
| 34 | `vomit` | modules/mob/living/carbon/carbon.dm |
| 35 | `vomitself` | modules/mob/living/carbon/carbon.dm |
| 36 | `painful_medicine`<br><small>PAIN</small> | modules/reagents/chemistry/reagents/cat2_medicine_reagents.dm |
| 37 | `startled`<br><small>FEAR</small> | datums/components/fearful/sources/phobia.dm |
| 38 | `phobia`<br><small>FEAR</small> | datums/brain_damage/phobia.dm, datums/components/fearful/sources/phobia.dm |
| 39 | `spooked`<br><small>FEAR</small> | datums/brain_damage/phobia.dm, datums/elements/spooky.dm |
| 40 | `notcreeping`<br><small>隐藏</small> | datums/brain_damage/creepy_trauma.dm |
| 41 | `notcreepingsevere` | datums/brain_damage/creepy_trauma.dm |
| 42 | `tower_of_babel` | datums/status_effects/debuffs/tower_of_babel.dm, modules/projectiles/projectile/magic.dm |
| 43 | `back_pain`<br><small>PAIN</small> | datums/quirks/negative_quirks/bad_back.dm |
| 44 | `sacrifice_bad`<br><small>SPIRITUAL</small> | modules/mapfluff/ruins/objects_and_mobs/ash_walker_den.dm |
| 45 | `artbad`<br><small>ART</small> | datums/elements/art.dm |
| 46 | `graverobbing` | modules/mapfluff/ruins/lavalandruin_code/elephantgraveyard.dm |
| 47 | `deaths_door` | game/objects/structures/guillotine.dm |
| 48 | `gunpoint`<br><small>FEAR</small> | datums/components/gunpoint.dm |
| 49 | `tripped` | modules/clothing/shoes/_shoes.dm |
| 50 | `untied` | modules/clothing/shoes/_shoes.dm |
| 51 | `gates_of_mansus`<br><small>FEAR</small> | modules/antagonists/heretic/influences.dm, modules/antagonists/heretic/items/corrupted_organs.dm, modules/antagonists/heretic/items/forbidden_book.dm, modules/antagonists/heretic/items/labyrinth_handbook.dm, modules/antagonists/heretic/magic/flesh_ascension.dm 等8处 |
| 52 | `high_five_full_hand` | datums/elements/high_fiver.dm |
| 53 | `too_slow` | _onclick/hud/screen_objects/alert.dm |
| 54 | `surgery`<br><small>FEAR</small> | modules/surgery/operations/_operation.dm |
| 55 | `surgery/success` | modules/surgery/operations/_operation.dm |
| 56 | `surgery/failure` | modules/surgery/operations/_operation.dm |
| 57 | `bald` | datums/quirks/neutral_quirks/bald.dm |
| 58 | `bald_reminder` | game/objects/structures/mirror.dm |
| 59 | `bad_touch` | datums/personality/reaction_to_others.dm |
| 60 | `very_bad_touch` | datums/personality/reaction_to_others.dm |
| 61 | `noogie` | game/objects/items/hand_items.dm |
| 62 | `noogie_harsh` | game/objects/items/hand_items.dm |
| 63 | `aquarium_negative` | datums/components/aquarium.dm |
| 64 | `tail_lost` | modules/surgery/organs/external/tails.dm |
| 65 | `tail_balance_lost` | modules/surgery/organs/external/tails.dm |
| 66 | `tail_regained_wrong` | modules/surgery/organs/external/tails.dm |
| 67 | `tail_regained_species` | modules/surgery/organs/external/tails.dm |
| 68 | `tail_regained_right` | modules/surgery/organs/external/tails.dm |
| 69 | `burnt_wings` | modules/surgery/organs/external/wings/moth_wings.dm |
| 70 | `holy_smite` | modules/religion/honorbound/honorbound_trauma.dm |
| 71 | `heresy` | (无引用 — dead code) |
| 72 | `soda_spill` | modules/reagents/reagent_containers/cups/soda.dm |
| 73 | `watersprayed` | modules/reagents/chemistry/reagents/other_reagents.dm |
| 74 | `gamer_withdrawal`<br><small>GAMING</small> | datums/quirks/neutral_quirks/gamer.dm |
| 75 | `gamer_lost`<br><small>GAMING</small> | datums/quirks/neutral_quirks/gamer.dm |
| 76 | `lost_52_card_pickup`<br><small>WHIMSY+GAMING</small> | modules/cards/deck/deck.dm |
| 77 | `russian_roulette_lose_cheater` | modules/projectiles/guns/ballistic/revolver.dm |
| 78 | `russian_roulette_lose` | modules/projectiles/guns/ballistic/revolver.dm |
| 79 | `bad_touch_bear_hug` | modules/mob/living/carbon/carbon_defense.dm |
| 80 | `rippedtail` | modules/mob/living/carbon/carbon_defense.dm |
| 81 | `sabrage_fail` | modules/reagents/reagent_containers/cups/glassbottle.dm |
| 82 | `body_purist` | datums/quirks/negative_quirks/body_purist.dm |
| 83 | `unsatisfied_nomad` | game/machinery/dna_infuser/organ_sets/carp_organs.dm |
| 84 | `moon_insanity`<br><small>FEAR</small> | modules/antagonists/heretic/magic/moon_parade.dm, modules/antagonists/heretic/status_effects/mark_effects.dm |
| 85 | `amulet_insanity`<br><small>FEAR</small> | modules/antagonists/heretic/items/heretic_necks.dm |
| 86 | `mallet_humiliation` | game/objects/items/clown_items.dm |
| 87 | `madness_despair`<br><small>特殊屏幕:mood_despair</small> | modules/antagonists/wizard/grand_ritual/finales/cheese.dm |
| 88 | `all_nighter` | datums/quirks/negative_quirks/all_nighter.dm |
| 89 | `desentized`<br><small>特殊屏幕:mood_desentized</small> | datums/brain_damage/special.dm |
| 90 | `pacifism_bypassed` | datums/martial/junglearts.dm, datums/martial/psychotic_brawl.dm |
| 91 | `bapped` | game/objects/items/stacks/wrap.dm |
| 92 | `encountered_evil` | datums/components/empathy.dm |
| 93 | `smoke_in_face` | game/objects/items/cigarettes.dm |
| 94 | `slots/loss`<br><small>GAMING</small> | game/machinery/roulette_machine.dm, game/machinery/slotmachine.dm |
| 95 | `lost_control_of_life` | modules/food_and_drinks/recipes/food_mixtures.dm |
| 96 | `empathetic_sad` | datums/mood.dm (personality reaction) |
| 97 | `misanthropic_sad` | datums/mood.dm (personality reaction) |
| 98 | `paranoid/one_on_one`<br><small>FEAR</small> | datums/personality/reaction_to_groups.dm |
| 99 | `paranoid/large_group`<br><small>FEAR</small> | datums/personality/reaction_to_groups.dm |
| 100 | `nt_disillusioned` | datums/personality/reaction_to_nt.dm |
| 101 | `disillusioned_revs_lost` | modules/antagonists/revolution/revolution_handler.dm |
| 102 | `loyalist_revs_win` | modules/antagonists/revolution/revolution_handler.dm |
| 103 | `slacking_off_diligent` | datums/personality/reaction_to_recreation.dm |
| 104 | `unimaginative_patronage` | modules/art/paintings.dm |
| 105 | `unimaginative_framing` | modules/art/paintings.dm |
| 106 | `unimaginative_sculpting` | modules/art/statues.dm |
| 107 | `splattered_with_blood` | game/objects/effects/decals/cleanable/blood.dm, modules/antagonists/changeling/powers/headcrab.dm, modules/mob/living/basic/vermin/cockroach/cockroach.dm |
| 108 | `teetotal_hangover` | game/objects/effects/landmarks.dm |
| 109 | `normal_hangover` | game/objects/effects/landmarks.dm |
| 110 | `jabbed_with_tester` | modules/antagonists/blood_worm/blood_worm_tester.dm |
| 111 | `gizmo_negative` | modules/research/gizmo/gizmodes/gizmood.dm |
| 112 | `conditional/see_death` | modules/mob/living/death.dm |
| 113 | `conditional/see_death/naive`<br><small>优先级:NAIVE_PRIORITY</small> | — |
| 114 | `conditional/see_death/cult`<br><small>优先级:CULT_PRIORITY</small> | — |
| 115 | `conditional/see_death/revolutionary`<br><small>优先级:REVOLUTIONARY_PRIORITY</small> | — |
| 116 | `conditional/see_death/gamer`<br><small>优先级:GAMER_PRIORITY</small> | — |
| 117 | `conditional/see_death/dontcare`<br><small>优先级:DONTCARE_PRIORITY</small> | — |
| 118 | `conditional/see_death/ashwalker`<br><small>优先级:ASHWALKER_PRIORITY</small> | — |
| 119 | `conditional/see_death/pet`<br><small>优先级:PET_PRIORITY</small> | — |
| 120 | `conditional/see_death/xeno`<br><small>优先级:XENO_PRIORITY</small> | — |
| 121 | `conditional/see_death/desensitized`<br><small>优先级:DESENSITIZED_PRIORITY</small> | — |
| 122 | `morbid_dismemberment` | modules/jobs/job_types/chaplain/chaplain_vorpal_scythe.dm, modules/surgery/surgery_tools.dm |
| 123 | `morbid_dissection_success` | modules/surgery/operations/operation_amputation.dm, modules/surgery/operations/operation_autopsy.dm |
| 124 | `morbid_abominable_surgery_success` | modules/surgery/operations/operation_organ_manip.dm, modules/surgery/operations/operation_plastic_surgery.dm |
| 125 | `morbid_revival_success` | modules/surgery/operations/operation_revival.dm |
| 126 | `morbid_graverobbing` | modules/mapfluff/ruins/lavalandruin_code/elephantgraveyard.dm |
| 127 | `morbid_hauntium` | modules/reagents/chemistry/reagents/other_reagents.dm |
| 128 | `morbid_aquarium_good` | datums/components/aquarium.dm |
| 129 | `morbid_tend_wounds` | modules/surgery/operations/operation_healing.dm |
| 130 | `morbid_saved_life` | game/objects/items/tools/medical/defib.dm, modules/mob/living/carbon/human/human.dm |
| 131 | `morbid_aquarium_bad` | datums/components/aquarium.dm |
| 132 | `it_was_on_the_mouse`<br><small>WHIMSY</small> | game/machinery/dna_infuser/organ_sets/rat_organs.dm |
| 133 | `gondola_serenity`<br><small>特殊屏幕:mood_gondola</small> | game/machinery/dna_infuser/organ_sets/gondola_organs.dm |
| 134 | `fish_waterless` | game/machinery/dna_infuser/organ_sets/fish_organs.dm |
| 135 | `fish_water` | game/machinery/dna_infuser/organ_sets/fish_organs.dm |

---

### 6.3 需求/食物/饮药事件（78 个）

| # | 事件路径 | 描述 | 心情值 | 超时 | 类别 |
|---|---|---|---|---|---|
| 1 | `/datum/mood_event/fat` | `<B>I'm so fat...</B>` | -6 | 无（常驻） | 营养类 |
| 2 | `/datum/mood_event/too_wellfed` | `I think I've eaten too much.` | 0 | 无（常驻） | 营养类 |
| 3 | `/datum/mood_event/wellfed` | `I'm stuffed!` | 8 | 无（常驻） | 营养类 |
| 4 | `/datum/mood_event/fed` | `I have recently had some food.` | 5 | 无（常驻） | 营养类 |
| 5 | `/datum/mood_event/hungry` | `I'm getting a bit hungry.` | -3 | 无（常驻） | 营养类 |
| 6 | `/datum/mood_event/hungry_very` | `I'm hungry!` | -6 | 无（常驻） | 营养类 |
| 7 | `/datum/mood_event/starving` | `I'm starving!` | -10 | 无（常驻） | 营养类 |
| 8 | `/datum/mood_event/supercharged` | `I can't possibly keep all this power inside, I need to release some quick!` | -10 | 无（常驻） | 电量类 |
| 9 | `/datum/mood_event/overcharged` | `I feel dangerously overcharged, perhaps I should release some power.` | -4 | 无（常驻） | 电量类 |
| 10 | `/datum/mood_event/charged` | `I feel the power in my veins!` | 6 | 无（常驻） | 电量类 |
| 11 | `/datum/mood_event/lowpower` | `My power is running low, I should go charge up somewhere.` | -6 | 无（常驻） | 电量类 |
| 12 | `/datum/mood_event/decharged` | `I'm in desperate need of some electricity!` | -10 | 无（常驻） | 电量类 |
| 13 | `/datum/mood_event/gross` | `I saw something gross.` | -4 | 无（常驻） | 厌恶类 |
| 14 | `/datum/mood_event/verygross` | `I think I'm going to puke...` | -6 | 无（常驻） | 厌恶类 |
| 15 | `/datum/mood_event/disgusted` | `Oh god, that's disgusting...` | -8 | 无（常驻） | 厌恶类 |
| 16 | `/datum/mood_event/disgust/bad_smell` | `I can smell something horribly decayed inside this room.` | -6 | 无（常驻） | 厌恶类（气味） |
| 17 | `/datum/mood_event/disgust/nauseating_stench` | `The stench of rotting carcasses is unbearable!` | -12 | 无（常驻） | 厌恶类（气味） |
| 18 | `/datum/mood_event/disgust/dirty_food` | `That was too dirty to eat...` | -6 | 4 MINUTES | **add_effects 特殊机制**：`ascetic` 人格 ×0.25（"That food was dirty…（完整见对应表） |
| 19 | `/datum/mood_event/shower` | `I have recently had a nice shower.` | 4 | 5 MINUTES | **add_effects(shower_reagent) 分派**：①血淋浴：`TRAIT_MORBID`/`TRAI…（完整见对应表） |
| 20 | `/datum/mood_event/hot_spring` | `It's so relaxing to bathe in steamy water...` | 5 | 无（常驻） | 温泉 |
| 21 | `/datum/mood_event/hot_spring_hater` | `No, no, no, no, I don't want to take a bath!` | -2 | 无（常驻） | 温泉（厌恶者） |
| 22 | `/datum/mood_event/hot_spring_left` | `That was an enjoyable bath.` | 4 | 4 MINUTES | 温泉（离开后余韵） |
| 23 | `/datum/mood_event/hot_spring_hater_left` | `I hate baths! And I hate how cold it's once you step out of it!` | -3 | 2 MINUTES | 温泉（厌恶者离开后） |
| 24 | `/datum/mood_event/fresh_laundry` | `There's nothing like the feeling of a freshly laundered jumpsuit.` | 2 | 10 MINUTES | 干净衣物 |
| 25 | `/datum/mood_event/surrounded_by_silicon` | `I'm surrounded by perfect lifeforms!!` | 8 | 无（常驻） | 周围全是硅基 |
| 26 | `/datum/mood_event/around_many_silicon` | `So many silicon lifeforms near me!` | 4 | 无（常驻） | 周围很多硅基 |
| 27 | `/datum/mood_event/around_silicon` | `The silicon lifeforms near me are absolutely perfect.` | 2 | 无（常驻） | 周围有硅基 |
| 28 | `/datum/mood_event/around_organic` | `The organics near me remind me of the inferiority of flesh.` | -2 | 无（常驻） | 周围有有机体 |
| 29 | `/datum/mood_event/around_many_organic` | `So many disgusting organics!` | -4 | 无（常驻） | 周围很多有机体 |
| 30 | `/datum/mood_event/surrounded_by_organic` | `I'm surrounded by disgusting organics!!` | -8 | 无（常驻） | 周围全是有机体 |
| 31 | `/datum/mood_event/completely_robotic` | `I've abandoned my feeble flesh, my form is perfect!!` | 8 | 无（常驻） | 全身机械化 |
| 32 | `/datum/mood_event/very_robotic` | `I'm more robot than organic!` | 4 | 无（常驻） | 高机械化 |
| 33 | `/datum/mood_event/balanced_robotic` | `I'm part machine, part organic.` | 0 | 无（常驻） | 半机械半有机 |
| 34 | `/datum/mood_event/very_organic` | `I hate this feeble and weak flesh!` | -4 | 无（常驻） | 高有机化 |
| 35 | `/datum/mood_event/completely_organic` | `I'm completely organic, this is miserable!!` | -8 | 无（常驻） | 完全有机 |
| 36 | `/datum/mood_event/favorite_food` | `I really enjoyed eating that.` | 5 | 4 MINUTES | `event_flags = MOOD_EVENT_FOOD` |
| 37 | `/datum/mood_event/gross_food` | `I really didn't like that food.` | -2 | 4 MINUTES | `event_flags = MOOD_EVENT_FOOD` |
| 38 | `/datum/mood_event/disgusting_food` | `That food was disgusting!` | -6 | 4 MINUTES | `event_flags = MOOD_EVENT_FOOD` |
| 39 | `/datum/mood_event/allergic_food` | `My throat itches.` | -2 | 4 MINUTES | `event_flags = MOOD_EVENT_FOOD`（食物过敏） |
| 40 | `/datum/mood_event/breakfast` | `Nothing like a hearty breakfast to start the shift.` | 2 | 10 MINUTES | `event_flags = MOOD_EVENT_FOOD`（早餐加成） |
| 41 | `/datum/mood_event/food` | `That food was [GLOB.food_quality_description[quality]].`（动态） | 动态 | 5 MINUTES（可被 `timeout_mod` 乘算） | **核心动态事件**：`add_effects(quality = FOOD_QUALITY_NORMAL, timeo…（完整见对应表） |
| 42 | `/datum/mood_event/pacifist_eating_fish_item` | `I shouldn't be eating living creatures...` | -1 | 4 MINUTES | `event_flags = MOOD_EVENT_FOOD`（和平主义者吃活物内疚，注释：仅补充语境，主惩罚来自 di…（完整见对应表） |
| 43 | `/datum/mood_event/drunk` | `Everything just feels better after a drink or two.`（基础档） | 动态 3~6 | 无（常驻，随醉酒度变化） | **动态醉酒事件**：`add_effects(drunkness)` → `update_change()` 按醉酒度…（完整见对应表） |
| 44 | `/datum/mood_event/drunk_after` | `The buzz might be gone, but I still feel good.` | 2 | 5 MINUTES | 醉酒余韵 |
| 45 | `/datum/mood_event/wrong_brandy` | `I hate that type of drink.` | -2 | 6 MINUTES | 喝到不爱的酒 |
| 46 | `/datum/mood_event/quality_revolting` | `That drink was the worst thing I've ever consumed.` | -8 | 7 MINUTES | 饮品品质（最差档） |
| 47 | `/datum/mood_event/quality_nice` | `That drink wasn't bad at all.` | 2 | 7 MINUTES | 饮品品质（一般档） |
| 48 | `/datum/mood_event/quality_good` | `That drink was pretty good.` | 4 | 7 MINUTES | 饮品品质（好档） |
| 49 | `/datum/mood_event/quality_verygood` | `That drink was great!` | 6 | 7 MINUTES | 饮品品质（很好档） |
| 50 | `/datum/mood_event/quality_fantastic` | `That drink was amazing!` | 8 | 7 MINUTES | 饮品品质（最佳档） |
| 51 | `/datum/mood_event/amazingtaste` | `Amazing taste!` | **50** | 10 MINUTES | 神级味觉加成 |
| 52 | `/datum/mood_event/wellcheers` | `What a tasty can of Wellcheers! The salty grape flavor is a great pick-me-up.` | 3 | 7 MINUTES | Wellcheers 汽水专有 |
| 53 | `/datum/mood_event/sweetcoffee` | `The bitter sweet taste of coffee was not too bad` | 2 | 5 MINUTES | 甜咖啡 |
| 54 | `/datum/mood_event/sweettea` | `Let your worries dissolve like sugar in tea.` | 4 | 2.5 MINUTES | 甜茶 |
| 55 | `/datum/mood_event/high` | `Woooow duudeeeeee... I'm tripping baaalls...` | 6 | 无（常驻） | 致幻兴奋 |
| 56 | `/datum/mood_event/stoned` | `I'm sooooo stooooooooooooned...` | 6 | 无（常驻） | 大麻石醉 |
| 57 | `/datum/mood_event/maintenance_high` | `I'm on top of the world, baby! Tide worldwide!` | 动态 | 2 MINUTES | **随机机制**：`add_effects(param)` → `value = rand(-1, 6)`（有翻车概率）…（完整见对应表） |
| 58 | `/datum/mood_event/hang_over` | `I have a killer hang over!` | -4 | 1 MINUTES | 宿醉 |
| 59 | `/datum/mood_event/smoked` | `I have had a smoke recently.` | 2 | 6 MINUTES | 抽烟愉悦 |
| 60 | `/datum/mood_event/wrong_brand` | `I hate that brand of cigarettes.` | -2 | 6 MINUTES | 抽错牌子的烟 |
| 61 | `/datum/mood_event/overdose` | `I think I took a bit too much of that [drug_name]!`（动态） | -8 | 5 MINUTES | **动态描述**：`add_effects(drug_name)` 填入药名 |
| 62 | `/datum/mood_event/withdrawal_light` | `I could use some [drug_name]...`（动态） | -2 | 无（常驻） | **动态描述**：`add_effects(drug_name)`；轻度戒断（`_addiction.dm:25` 默认…（完整见对应表） |
| 63 | `/datum/mood_event/withdrawal_medium` | `I really need [drug_name].`（动态） | -5 | 无（常驻） | 中度戒断 |
| 64 | `/datum/mood_event/withdrawal_severe` | `Oh god, I need some of that [drug_name]!`（动态） | -8 | 无（常驻） | 重度戒断 |
| 65 | `/datum/mood_event/happiness_drug` | `Can't feel a thing...` | **50** | 无（常驻） | 快乐药（Happiness drug） |
| 66 | `/datum/mood_event/happiness_drug_good_od` | `YES! YES!! YES!!!` | **100** | 30 SECONDS | `special_screen_obj = "mood_happiness_good"`（屏幕特效） |
| 67 | `/datum/mood_event/happiness_drug_bad_od` | `NO! NO!! NO!!!` | **-100** | 30 SECONDS | `special_screen_obj = "mood_happiness_bad"`（屏幕特效） |
| 68 | `/datum/mood_event/narcotic_medium` | `I feel comfortably numb.` | 4 | 3 MINUTES | 麻醉剂（中） |
| 69 | `/datum/mood_event/narcotic_heavy` | `I feel like I'm wrapped up in cotton!` | 9 | 3 MINUTES | 麻醉剂（重） |
| 70 | `/datum/mood_event/antinarcotic_medium` | `I wish I was numb again!` | -4 | 3 MINUTES | 抗麻醉/戒断（中） |
| 71 | `/datum/mood_event/antinarcotic_heavy` | `NO!! Make the cotton come back!` | -9 | 3 MINUTES | 抗麻醉（重） |
| 72 | `/datum/mood_event/stimulant_medium` | `I have so much energy! I feel like I could do anything!` | 4 | 3 MINUTES | 兴奋剂（中） |
| 73 | `/datum/mood_event/stimulant_heavy` | `Eh ah AAAAH! HA HA HA HA HAA! Uuuh.` | 6 | 3 MINUTES | 兴奋剂（重）；NovaSector 有子类 `/datum/mood_event/stimulant_heavy/sun…（完整见对应表） |
| 74 | `/datum/mood_event/eigentrip` | `I swapped places with an alternate reality version of myself!` | 动态 0±10 | 10 MINUTES | **随机机制**：`add_effects(param)` → `value = rand(-10, 10)`（`#de…（完整见对应表） |
| 75 | `/datum/mood_event/nicotine_withdrawal_moderate` | `Haven't had a smoke in a while. Feeling a little on edge... ` | -5 | 无（常驻） | 尼古丁戒断（中） |
| 76 | `/datum/mood_event/nicotine_withdrawal_severe` | `Head pounding. Cold sweating. Feeling anxious. Need a smoke to calm down!` | -8 | 无（常驻） | 尼古丁戒断（重） |
| 77 | `/datum/mood_event/hauntium_spirits` | `I feel my soul degrading!` | -8 | 8 MINUTES | 幽魂素（Hauntium）后劲 |
| 78 | `/datum/mood_event/sadness_inverse` | `I'M SO SAD...` | **-150** | 无（常驻） | `special_screen_obj = "mood_happiness_bad"`（屏幕特效） |

#### 触发源对应表


| # | 事件路径 | 触发源 |
|---|---|---|
| 1 | `/datum/mood_event/fat` | `mood.dm` `update_nutrition_moodlets()`：有 `TRAIT_FAT` 且无 `TRAIT_VORACIOUS` 时（NovaSector 在 `modular_nova/master_files/code/datums/traits/neutral.dm:369` 有 `/datum/mood_event/fat/add_effects(...)` 覆写） |
| 2 | `/datum/mood_event/too_wellfed` | `mood.dm`：营养 ≥ `NUTRITION_LEVEL_FULL` 且无 `TRAIT_VORACIOUS` |
| 3 | `/datum/mood_event/wellfed` | `mood.dm`：营养 `WELL_FED~FULL`；或 ≥FULL 且带 `TRAIT_VORACIOUS` |
| 4 | `/datum/mood_event/fed` | `mood.dm`：营养 `FED~WELL_FED` |
| 5 | `/datum/mood_event/hungry` | `mood.dm`：营养 `VERY_HUNGRY~HUNGRY`；另带 `TRAIT_GLUTTON` 时固定触发（"you'll never get enough"） |
| 6 | `/datum/mood_event/hungry_very` | `mood.dm`：营养 `STARVING~VERY_HUNGRY` |
| 7 | `/datum/mood_event/starving` | `mood.dm`：营养 0~`NUTRITION_LEVEL_STARVING` |
| 8 | `/datum/mood_event/supercharged` | `stomach_ethereal.dm:106`（`add_mood_event("charge", ...)`） |
| 9 | `/datum/mood_event/overcharged` | `stomach_ethereal.dm:102` |
| 10 | `/datum/mood_event/charged` | `stomach_ethereal.dm:99` |
| 11 | `/datum/mood_event/lowpower` | `stomach_ethereal.dm:96` |
| 12 | `/datum/mood_event/decharged` | `stomach_ethereal.dm:86,91`（急缺电） |
| 13 | `/datum/mood_event/gross` | `_stomach.dm:389`（`add_mood_event("disgust", ...)`，呕吐/恶心） |
| 14 | `/datum/mood_event/verygross` | `_stomach.dm:392` |
| 15 | `/datum/mood_event/disgusted` | `shotgun.dm:69`（`"stink-pocket"` 臭弹） |
| 16 | `/datum/mood_event/disgust/bad_smell` | `life.dm:373`（`"smell"`）、`_lungs.dm:477`（呼吸到腐臭味） |
| 17 | `/datum/mood_event/disgust/nauseating_stench` | `life.dm:378,384`、`_lungs.dm:482,488` |
| 18 | `/datum/mood_event/disgust/dirty_food` | `infective.dm:98`（吃脏食物，`"disgust"` 类）<br><small>机制：**add_effects 特殊机制**：`ascetic` 人格 ×0.25（"That food was dirty, but edible."）；`gourmand` 人格 ×1.5（"That food was filthy, was it made in a dumpster?!"）</small> |
| 19 | `/datum/mood_event/shower` | `shower.dm:281`（淋浴间喷淋 `"shower"` 类）<br><small>机制：**add_effects(shower_reagent) 分派**：①血淋浴：`TRAIT_MORBID`/`TRAIT_EVIL`/亡灵 → +6「lovely blood shower」；否则 -4「horrible shower raining blood」3 MIN。②清水：`TRAIT_WATER_HATER` 且无 `TRAIT_WATER_ADAPTATION` → -2「I hate being wet!」3 MIN；否则 return（正常淋浴）。③脏水 → -3「dirty shower」3 MIN</small> |
| 20 | `/datum/mood_event/hot_spring` | `water.dm:286`（泡温泉中） |
| 21 | `/datum/mood_event/hot_spring_hater` | `water.dm:288` |
| 22 | `/datum/mood_event/hot_spring_left` | `water.dm:309` |
| 23 | `/datum/mood_event/hot_spring_hater_left` | `water.dm:311` |
| 24 | `/datum/mood_event/fresh_laundry` | `_under.dm:233`（穿刚洗的制服）、`towels.dm:318`（`onwear_mood` 组件） |
| 25 | `/datum/mood_event/surrounded_by_silicon` | `transhumanist.dm:199`（`MOOD_CATEGORY_TRANSHUMANIST_PEOPLE`） |
| 26 | `/datum/mood_event/around_many_silicon` | `transhumanist.dm:201` |
| 27 | `/datum/mood_event/around_silicon` | `transhumanist.dm:203` |
| 28 | `/datum/mood_event/around_organic` | `transhumanist.dm:207` |
| 29 | `/datum/mood_event/around_many_organic` | `transhumanist.dm:209` |
| 30 | `/datum/mood_event/surrounded_by_organic` | `transhumanist.dm:211` |
| 31 | `/datum/mood_event/completely_robotic` | `transhumanist.dm:112`（`MOOD_CATEGORY_TRANSHUMANIST_BODYPART`） |
| 32 | `/datum/mood_event/very_robotic` | `transhumanist.dm:124` |
| 33 | `/datum/mood_event/balanced_robotic` | `transhumanist.dm:126` |
| 34 | `/datum/mood_event/very_organic` | `transhumanist.dm:128` |
| 35 | `/datum/mood_event/completely_organic` | `transhumanist.dm:115` |
| 36 | `/datum/mood_event/favorite_food` | `_cup.dm:122`（喝到最爱饮品 `"fav_food"`）、`modular_nova/.../drink_reagents.dm:88`（最爱饮品试剂） |
| 37 | `/datum/mood_event/gross_food` | `edible.dm:641`（难吃食物 `"gross_food"`）、`_cup.dm:118` |
| 38 | `/datum/mood_event/disgusting_food` | `edible.dm:635`（有毒/恶心食物 `"toxic_food"`）、`_cup.dm:114` |
| 39 | `/datum/mood_event/allergic_food` | `taste.dm:159`（`"allergic_food"`，尝到过敏原） |
| 40 | `/datum/mood_event/breakfast` | `edible.dm:625`（早上吃第一餐）、`_cup.dm:106` |
| 41 | `/datum/mood_event/food` | `edible.dm:649`（吃东西按品质结算 `"quality_food"`）；`food_reagents.dm:56` 的 `amazingtaste` 另算（见饮品）<br><small>机制：**核心动态事件**：`add_effects(quality = FOOD_QUALITY_NORMAL, timeout_mod = 1)` → `mood_change = calculate_mood_change(quality)`，`timeout *= timeout_mod`。`calculate_mood_change`：`quality = 1 + 1.5 * base_quality`，`ascetic` 人格 ×0.5，`gourmand` 人格且品质≤GOOD 时压回 NORMAL；`ceil` 取整。`be_refreshed()` 会取新旧 timeout/mood 的**最大值**并同步描述。`event_flags = MOOD_EVENT_FOOD`</small> |
| 42 | `/datum/mood_event/pacifist_eating_fish_item` | `_fish.dm:438`（吃鱼 `"eating_fish"`）<br><small>机制：`event_flags = MOOD_EVENT_FOOD`（和平主义者吃活物内疚，注释：仅补充语境，主惩罚来自 disgusting_food）</small> |
| 43 | `/datum/mood_event/drunk` | `drunk.dm:106`（醉酒状态效果 `"drunk"`，带 drunk_value）；`modular_nova/.../human.dm:276`（`"drunk"`）<br><small>机制：**动态醉酒事件**：`add_effects(drunkness)` → `update_change()` 按醉酒度直接设定：0~30=+3 / 30~45=+4「Is it getting hotter...」/ 45~60=+5「Who keeps moving the floor...」/ 60~90=+6「I'm noooot drunk...」/ 90+ =+3（crash out「You're my BESSST frien'...」）。`teetotal` 人格 ×-1.5（"I don't like drinking..."），`bibulous` 人格 ×1.5。人类额外挂 `blush` 脸红 overlay（`datum/bodypart_overlay/simple/emote/blush`），`remove_effects()` 时删除。值变化会调 `update_mood()`</small> |
| 44 | `/datum/mood_event/drunk_after` | `drunk.dm:124`（醉酒结束 `"[id]_after"`） |
| 45 | `/datum/mood_event/wrong_brandy` | `addict.dm:235`（成瘾者 `"wrong_alcohol"`） |
| 46 | `/datum/mood_event/quality_revolting` | `food_reagents.dm:45`（`"quality_drink"`） |
| 47 | `/datum/mood_event/quality_nice` | `food_reagents.dm:47` |
| 48 | `/datum/mood_event/quality_good` | `food_reagents.dm:49` |
| 49 | `/datum/mood_event/quality_verygood` | `food_reagents.dm:51` |
| 50 | `/datum/mood_event/quality_fantastic` | `food_reagents.dm:53` |
| 51 | `/datum/mood_event/amazingtaste` | `food_reagents.dm:56`（`"quality_food"` 类别，品质极佳时） |
| 52 | `/datum/mood_event/wellcheers` | `drink_reagents.dm:704`（`"wellcheers"`） |
| 53 | `/datum/mood_event/sweetcoffee` | `bevarages.dm:9`（堆叠效果 `"sweet_coffee"`） |
| 54 | `/datum/mood_event/sweettea` | `bevarages.dm:19`（`"sweet_tea"`） |
| 55 | `/datum/mood_event/high` | `drugginess.dm:14`（drugginess 状态效果）；`drug_reagents.dm:534`（`"tripping"` 迷幻药）、`607`（`"vibing"`） |
| 56 | `/datum/mood_event/stoned` | `drug_effects.dm:80`（`"stoned"`）；`modular_nova/.../thc.dm:30,61`（THC 试剂，`"stoned"` 带 name 参数；NovaSector 在该文件有覆写） |
| 57 | `/datum/mood_event/maintenance_high` | `drink_reagents.dm:557`（`"maintenance_fun"` 维修工饮料）、`drug_reagents.dm:371,418`（`"maintenance_fun"`）<br><small>机制：**随机机制**：`add_effects(param)` → `value = rand(-1, 6)`（有翻车概率）；value<0 → -X 且描述「No! Don't! My gloves! Auuuuurgh!」；≥0 用初始描述</small> |
| 58 | `/datum/mood_event/hang_over` | `split_personality.dm:299`（`"hang_over"`） |
| 59 | `/datum/mood_event/smoked` | `drug_reagents.dm:105`（`"smoked"`） |
| 60 | `/datum/mood_event/wrong_brand` | `addict.dm:167`（成瘾者 `"wrong_cigs"`） |
| 61 | `/datum/mood_event/overdose` | `drug_reagents.dm:34`、`reagents.dm:291`（`"[type]_overdose"` 通用过量） |
| 62 | `/datum/mood_event/withdrawal_light` | `_addiction.dm:25`（成瘾系统通用轻戒断）<br><small>机制：**动态描述**：`add_effects(drug_name)`；轻度戒断（`_addiction.dm:25` 默认 `light_withdrawal_moodlet`）</small> |
| 63 | `/datum/mood_event/withdrawal_medium` | `_addiction.dm:27`（默认 `medium_withdrawal_moodlet`） |
| 64 | `/datum/mood_event/withdrawal_severe` | `_addiction.dm:29`（默认 `severe_withdrawal_moodlet`） |
| 65 | `/datum/mood_event/happiness_drug` | `drug_reagents.dm:325`（`"happiness_drug"`） |
| 66 | `/datum/mood_event/happiness_drug_good_od` | `drug_reagents.dm:346`（快乐药良性过量） |
| 67 | `/datum/mood_event/happiness_drug_bad_od` | `drug_reagents.dm:352`（快乐药恶性过量） |
| 68 | `/datum/mood_event/narcotic_medium` | `alcohol_reagents.dm:2526`（`"numb"` 酒类麻醉）、`medicine_reagents.dm:771`（`"numb"`，带 name） |
| 69 | `/datum/mood_event/narcotic_heavy` | `drug_reagents.dm:139`（`"smacked out"`）、`impure_medicine_reagents.dm:961`、`toxin_reagents.dm:894`（带 name）、`modular_nova/.../opium.dm:115` |
| 70 | `/datum/mood_event/antinarcotic_medium` | `medicine_reagents.dm:2001`（`"not numb"`） |
| 71 | `/datum/mood_event/antinarcotic_heavy` | `medicine_reagents.dm:2005`（`"not smacked out"`） |
| 72 | `/datum/mood_event/stimulant_medium` | `drug_reagents.dm:204`（`"tweaking"`）、`878`（`"tweaking"` kronkaine）、`modular_nova/.../cortical_borer_chems.dm:9`（带 name） |
| 73 | `/datum/mood_event/stimulant_heavy` | `drug_reagents.dm:263`（`"salted"`）、`modular_nova/.../cocaine.dm:49`（`"zoinked"` 带 name）、`demoneye.dm:105`（sundowner）<br><small>机制：兴奋剂（重）；NovaSector 有子类 `/datum/mood_event/stimulant_heavy/sundowner`（`demoneye.dm:146`）</small> |
| 74 | `/datum/mood_event/eigentrip` | `neutral.dm:570`（`"Eigentrip"` 本征维度跳跃）<br><small>机制：**随机机制**：`add_effects(param)` → `value = rand(-10, 10)`（`#define EIGENTRIP_MOOD_RANGE 10`）；<0 → 负面描述「...I want to go home!」；≥0 → 正面描述「...this place is much better than my old life.」</small> |
| 75 | `/datum/mood_event/nicotine_withdrawal_moderate` | `generic_addictions.dm:295`（`medium_withdrawal_moodlet` 覆写） |
| 76 | `/datum/mood_event/nicotine_withdrawal_severe` | `generic_addictions.dm:296`（`severe_withdrawal_moodlet` 覆写） |
| 77 | `/datum/mood_event/hauntium_spirits` | `other_reagents.dm:3418`（`"hauntium_spirits"`，带 name，注释 "8 minutes of mood debuff"） |
| 78 | `/datum/mood_event/sadness_inverse` | `impure_medicine_reagents.dm:1122`（`"sadness_inverse"` 悲伤反转药） |

---

### 6.4 外围触发源事件（93 个定义）

| # | 事件 | 心情值 | 超时 | 隐藏 | 描述 |
|---|---|---|---|---|---|
| 1 | `/datum/mood_event/anxiety_eyecontact` | -5 | 3 MINUTES | 否 | 有时眼神接触让我非常紧张……「Sometimes eye contact makes me so nervous...」 |
| 2 | `/datum/mood_event/spacer` | 0（空基类） | — | 否 | 基类，无描述 |
| 3 | `/datum/mood_event/spacer/in_space` | 0（继承） | — | 否 | 太空漫长黑暗而空旷，但那是我的家。「Space is long and dark and empty, but it's my home.」 |
| 4 | `/datum/mood_event/spacer/on_planet` | -2 | — | 否 | 我在行星上。这里的重力让我不舒服。「I'm on a planet. The gravity here makes me uncomfortable.」 |
| 5 | `/datum/mood_event/spacer/on_planet/too_long` | -4 | — | 否 | 我在这个星球上待太久了，我需要回到太空。「I've been on this planet for too long. I need to get back to space.」 |
| 6 | `/datum/mood_event/spacer/on_planet/nerfed` | -3 | — | 否 | 我被派驻在行星上。我真想回到太空。「I'm stationed on a planet. I'd love to be back in space.」 |
| 7 | `/datum/mood_event/spacer/on_planet/low_grav` | +3（每 5 分钟 -1，最低 +1） | — | 否 | 这感觉就像回到了家！「This feels like I'm back home!」 |
| 8 | `/datum/mood_event/spacer/on_planet/low_grav/add_effects(...)` | — | — | — | proc：5 分钟后回调 `lower_mood_bonus` |
| 9 | `/datum/mood_event/spacer/on_planet/low_grav/proc/lower_mood_bonus()` | — | — | — | proc：mood_change -= 1，>1 则继续每 5 分钟递减 |
| 10 | `/datum/mood_event/fat/add_effects(...)`（NOVA 覆写基础 fat） | 0（若有 OFF_BALANCE_TACKLER 特质） | — | 否 | 「<b>我很胖。</b>」「<b>I'm fat.</b>」——NOVA 让有"失衡扑倒者"特质的胖子不再因肥胖掉心情 |
| 11 | `/datum/mood_event/stoat` | 0（空基类） | — | 否 | 基类，无描述 |
| 12 | `/datum/mood_event/stoat/enemies_nearby` | 0（中间节点） | — | 否 | `event_flags = MOOD_EVENT_FEAR`（恐惧类） |
| 13 | `/datum/mood_event/stoat/enemies_nearby/one` | -1 | — | 否 | 我的本能告诉我附近有危险的东西，最好小心点。「My instincts say there's something dangerous nearby, better be careful.」 |
| 14 | `/datum/mood_event/stoat/enemies_nearby/multiple` | -3 | — | 否 | 我的本能告诉我附近有潜在危险，最好保持警惕。「My instincts say there potential danger nearby, better be on edge.」 |
| 15 | `/datum/mood_event/stoat/enemies_nearby/crowd` | -5 | — | 否 | 我的本能告诉我附近有很多危险的东西，我得离开这里！「My instincts say there are a lot of dangerous things nearby, I need to get out of here!」 |
| 16 | `/datum/mood_event/stoat/alone` | +1 | — | 否 | 附近没有人，我的本能安歇了。我感到平静。「There is no one nearby, my instincts are at rest. I feel at peace.」 |
| 17 | `/datum/mood_event/stoat/friendlies_nearby` | 0（中间节点） | — | 否 | `event_flags = MOOD_EVENT_FEAR` |
| 18 | `/datum/mood_event/stoat/friendlies_nearby/one` | 0（继承） | — | 否 | 附近只有一个朋友，我的本能安歇了。「There is only one friend nearby, my instincts are at rest.」 |
| 19 | `/datum/mood_event/stoat/friendlies_nearby/multiple` | -1 | — | 否 | 我的本能告诉我附近人太多，我有点紧张。「My instincts say there are too many people nearby, I feel a little on edge.」 |
| 20 | `/datum/mood_event/stoat/friendlies_nearby/crowd` | -3 | — | 否 | 我的本能告诉我附近人太多了，我得离开这里！「My instincts say there are too many people nearby, I need to get out of here!」 |
| 21 | `/datum/mood_event/surgery_emp_active` | **-90** | 1 MINUTES | 否 | 手术不完成病人就活不了！「THE PATIENT WILL NOT SURVIVE UNLESS THE OPERATION IS COMPLETE!」（`special_screen_obj = "mood_despair"` 绝望屏显） |
| 22 | `/datum/mood_event/surgery_emp_expired` | 0 | 5 MINUTES | 否 | 我失控了——幸好现在结束了。「I lost control - Thankfully it's over now.」 |
| 23 | `/datum/mood_event/rapid_wound_healing` | -3 | 5 MINUTES | 否 | 伤口愈合了，但那疼痛难以忍受！「The wound is gone, but that pain was unbearable!」 |
| 24 | `/datum/mood_event/traitor_poster_antag` | +2 | 2 MINUTES | **是** | 我在做正确的事。「I am doing the right thing.」 |
| 25 | `/datum/mood_event/traitor_poster_crew` | -2 | 2 MINUTES | **是** | 那张海报让我对自己的工作感到难受……「That poster made me feel bad about my job...」 |
| 26 | `/datum/mood_event/traitor_poster_auth` | -3 | 2 MINUTES | **是** | 那张海报最好别给船员什么奇怪的想法……「That poster better not be giving the crew any funny ideas...」 |
| 27 | `/datum/mood_event/traitor_graffiti_antag` | +2 | 2 MINUTES | **是** | 辛迪加标志？多么大胆的挑衅。「The Syndicate logo? How delightfully bold.」 |
| 28 | `/datum/mood_event/traitor_graffiti_crew` | -2 | 2 MINUTES | **是** | 辛迪加标志？我在这里安全吗？「The Syndicate logo? Am I safe here?」 |
| 29 | `/datum/mood_event/traitor_graffiti_auth` | -3 | 2 MINUTES | **是** | 是哪个懒鬼画了那个辛迪加标志？！「Which of these layabouts drew that Syndicate logo?!」 |
| 30 | `/datum/mood_event/traitor_module_antag` | +1 | 2 MINUTES | **是** | 我想我会故意制造些麻烦。「I think I'll cause problems on purpose.」 |
| 31 | `/datum/mood_event/traitor_module_crew` | -4 | 2 MINUTES | **是** | 他们就在站上！我知道！他们要来抓我了！「They're on the station! I know it! They're going to get me!」 |
| 32 | `/datum/mood_event/traitor_module_auth` | -5 | 2 MINUTES | **是** | 这个站上没有人站在我这边，敌人可能是任何人！我必须采取更极端的措施……「Nobody on this station is on my side, and the enemy could be anyone! I have to take more drastic measures...」 |
| 33 | `/datum/mood_event/shadow_realm` | **-15** | 3 MINUTES | 否 | 我在哪里？！「Where am I?!」 |
| 34 | `/datum/mood_event/shadow_realm_live` | +4 | 5 MINUTES | 否 | 我活着……我还活着！！「I'm alive... I'm alive!!」 |
| 35 | `/datum/mood_event/shadow_realm_live_sad` | -6 | 10 MINUTES | 否 | 那些手！那些可怕、恐怖的手！我一闭眼就看到它们！「The hands! The horrible, horrific hands! I see them when I close my eyes!」 |
| 36 | `/datum/mood_event/shadow_realm_live_sad_heretic` | -8 | 8 MINUTES | 否 | 我被羞辱了！我的知识被从体内抽走！世界又变得暗淡无光……「I've been humiliated! My knowledge sapped from my being! The world feels much duller again...」 |
| 37 | `/datum/mood_event/heretics/lunatic` | +10 | — | 否 | 真相大白，谎言破灭。「THE TRUTH REVEALED, THE LIE SLAIN.」 |
| 38 | `/datum/mood_event/soultrapped_heretic` | **-20** | — | 否 | 他们困住了我！我逃不掉！「They trapped me! I can't escape!」 |
| 39 | `/datum/mood_event/mansus_dream_fatigue` | -2 | 5 MINUTES | 否 | 我必须恢复之后才能再次梦见曼苏斯。「I must recover before I can dream of Mansus again.」 |
| 40 | `/datum/mood_event/fallen_changeling` | -4 | — | 否 | 我的力量！我的力量去哪儿了？！「My powers! Where are my powers?!」 |
| 41 | `/datum/mood_event/royale_survivor` | +4 | — | 否 | 我活着从乱斗大逃杀中活下来了。「I made it out of Rumble Royale with my life.」 |
| 42 | `/datum/mood_event/clown_world` | +4 | — | 否 | （add_effects 动态覆写）「我超爱在小丑研究站工作！！」「I LOVE working at Clown Research Station [站名]!!」 |
| 43 | `/datum/mood_event/clown_world/add_effects(param)` | — | — | — | proc：按当前站名动态生成描述 |
| 44 | `/datum/mood_event/soul_core_torment` | **-20** | 10 SECONDS | 否 | 它在燃烧！！燃烧！！我存在的最深处！！它在燃烧！！「IT BURNS!! IT BURNS!! THE DEEPEST DEPTHS OF MY BEING!! IT BURNS!!」 |
| 45 | `/datum/mood_event/soul_core_torment/heretic` | -20（继承） | 10 SECONDS | 否 | 从我脑子里滚出去！从我脑子里滚出去！从我脑子里滚出去！！「GET OUT OF MY HEAD GET OUT OF MY HEAD GET OUT OF MY HEAD!!」 |
| 46 | `/datum/mood_event/soul_core_discomfort` | -3 | 10 SECONDS | 否 | 我不喜欢这些神力在我脖子上喘气。「I'm no fan of these divine powers breathing down my neck.」 |
| 47 | `/datum/mood_event/soul_core_warning` | 0 | 10 SECONDS | 否 | 我能感觉到我的 MOD 服在吸取我的能量。我最好保持高昂的情绪。「I can feel my modsuit siphoning my energy. I'd better keep my spirits high.」 |
| 48 | `/datum/mood_event/banish_nightmare` | +4 | 10 MINUTES | 否 | 我驱逐了噩梦，守护了我们的梦境！「I banished a nightmare and protected our dreams!」 |
| 49 | `/datum/mood_event/dream_interrupted` | -2 | 5 MINUTES | 否 | 我从梦中被粗暴地弄醒了！「I was rudely awakened from my dreams!」 |
| 50 | `/datum/mood_event/dream_failed` | -2 | 5 MINUTES | 否 | 我无法从梦中接收到清晰的预兆！「I couldn't receive a clear vision from my dreams!」 |
| 51 | `/datum/mood_event/poster_mood` | +2 | — | 否 | 那张海报真的很激励我！「That poster is really motivating me!」 |
| 52 | `/datum/mood_event/depressing_party` | -1 | 30 SECONDS | 否 | 那真是个阴郁的派对……「That was a really grim party...」 |
| 53 | `/datum/mood_event/festive_party` | +2 | 30 SECONDS | 否 | 那真是个超棒的派对！「That was a really fantastic party!」 |
| 54 | `/datum/mood_event/artifact_effect_good_major` | +10 | 10 MINUTES | 否 | 呜哦！发生什么事了？「Wooo! What is going on?」 |
| 55 | `/datum/mood_event/artifact_effect_good_minor` | +5 | 5 MINUTES | 否 | 我感觉很奇怪，但又非常放松……「I feel strange, but also very relaxed...」 |
| 56 | `/datum/mood_event/artifact_effect_bad_major` | -10 | 10 MINUTES | 否 | 感觉像一群鹅在啄我的头骨……「It feels like a gaggle of geese are pecking at my skull...」 |
| 57 | `/datum/mood_event/artifact_effect_bad_minor` | -5 | 5 MINUTES | 否 | 我感到奇怪而不安……「I feel strange and uneasy...」 |
| 58 | `/datum/mood_event/faded_hope_lavaland` | +4 | — | 否 | 多么奇特的徽记。它让我对未来充满希望。「What a peculiar emblem. It makes me feel hopeful for my future.」 |
| 59 | `/datum/mood_event/brushed` | +3 | 4 MINUTES | 否 | （add_effects 覆写）「[某人]最近给我梳了[部位]，感觉太棒了！」「Someone brushed me recently, that felt great!」 |
| 60 | `/datum/mood_event/brushed/add_effects(mob/brusher, brush_target)` | — | — | — | proc：按梳毛者与部位生成动态描述 |
| 61 | `/datum/mood_event/brushed/expert` | +4 | 4 MINUTES | 否 | 「有人大师级地给我梳了毛，我感觉棒极了！」「Someone masterfully brushed me recently, I feel fantastic!」 |
| 62 | `/datum/mood_event/brushed/expert/add_effects(mob/brusher, brush_target)` | — | — | — | proc：动态描述 |
| 63 | `/datum/mood_event/brushed/self` | +2 | 4 MINUTES | 否 | 「我最近给自己梳了毛！」「I brushed myself recently!」（自己梳够不到所有好位置） |
| 64 | `/datum/mood_event/brushed/self/add_effects(brush_target)` | — | — | — | proc：动态描述 |
| 65 | `/datum/mood_event/brushed/self/expert` | +3 | 4 MINUTES | 否 | 「我完美地给自己梳了毛，我感觉棒极了！」「I brushed myself flawlessly, I feel fantastic!」 |
| 66 | `/datum/mood_event/brushed/self/expert/add_effects(brush_target)` | — | — | — | proc：动态描述 |
| 67 | `/datum/mood_event/brushed/pet/add_effects(mob/brushed_pet)` | +2（继承 brushed/self） | — | — | proc：「我梳了[宠物]，它好可爱！」「I brushed [宠物] recently, [它] so cute!」 |
| 68 | `/datum/mood_event/harshly_brushed` | -3 | — | 否 | 嗷！梳得太用力了！「Oww! That brushing was too rough!」 |
| 69 | `/datum/mood_event/harsh_brushed/add_effects(brush_target)` | — | — | — | proc：「嗷！梳我的[部位]太用力了！」 |
| 70 | `/datum/mood_event/hotspring` | +4 | 20 MINUTES | 否 | 我最近在舒服的温水里泡了一会儿！真清爽！「I recently had a paddle in some nice warm water! It was so refreshing!」 |
| 71 | `/datum/mood_event/hotspring/nerfed` | +2 | — | 否 | 水暖得令人愉快！「The water was enjoyably warm!」 |
| 72 | `/datum/mood_event/tucked_in` | +3 | 2 MINUTES | 否 | （add_effects 覆写）「把[某人]安顿好睡个好觉让我感觉很好！」「I feel better having tucked in [名字] for a good night's rest!」 |
| 73 | `/datum/mood_event/tucked_in/add_effects(mob/tuckee)` | — | — | — | proc：按被掖者名字动态生成描述 |
| 74 | `/datum/mood_event/warmhug/rubi` | 0 | 2 MINUTES | 否 | 温暖舒适的小蜜蜂拥抱最棒了！「Warm cozy bee hugs are the best!」 |
| 75 | `/datum/mood_event/warmhug/parsec` | +3 | 5 MINUTES | 否 | 她舔了我的鼻子！啊呜！「She licked me right on my nose! Awwwh!」 |
| 76 | `/datum/mood_event/plasteel_chef` | +3 | — | 否 | 你对自己所取得的成就感到极度自豪。「You have an extreme sense of pride at what you've accomplished.」 |
| 77 | `/datum/mood_event/xenohybrid_resin` | +2 | 15 SECONDS | 否 | 我喜欢待在树脂上。它让我的身心平静。「I like being on resin. It calms my body and soul.」 |
| 78 | `/datum/mood_event/hemophage_feed_human` | +2 | 5 MINUTES | 否 | 我以新鲜、充满活力的血液解了渴。感觉真好！「I slaked my hunger on fresh, vital blood. That felt good!」 |
| 79 | `/datum/mood_event/disgust/hemophage_feed_monkey` | -4 | 5 MINUTES | 否 | 我不得不吸一只吱吱叫的猴子……我变成了什么？「I had to feed off a gibbering monkey... what have I become?」 |
| 80 | `/datum/mood_event/disgust/hemophage_feed_humonkey` | -1 | 5 MINUTES | 否 | 不知怎的我内心深处知道，人猴的血无法替代真货……「Somehow I know deep down that humonkey blood is no substitute for the real thing...」 |
| 81 | `/datum/mood_event/orgasm` | 0 | 5 MINUTES | 否 | 哇……这愉快的疲惫感……我爱死了。「Woah... This pleasant tiredness... I love it.」 |
| 82 | `/datum/mood_event/climaxself` | 0 | 4 MINUTES | 否 | 我刚刚在自己内裤里高潮了。真 messy。「I just came in my own underwear. Messy.」 |
| 83 | `/datum/mood_event/overgasm` | 0 | 10 MINUTES | 否 | 呃……我不想再欲火焚身了。「Uhh... I don't want to be horny anymore.」 |
| 84 | `/datum/mood_event/ropebunny` | 0（显式） | — | 否 | 我被绑住了！动不了！这些绳子……啊！~「I'm tied! Cannot move! These ropes... Ah!~」 |
| 85 | `/datum/mood_event/subspace` | 0 | — | 否 | 一切都好晕……疼痛感觉……好棒。「Everything is so woozy... Pain feels so... Awesome.」 |
| 86 | `/datum/mood_event/perv_spanked` | 0 | 5 MINUTES | 否 | 啊，是的！再来！惩罚我吧！「Ah, yes! More! Punish me!」 |
| 87 | `/datum/mood_event/minor_overdose` | 0 | 5 MINUTES | 否 | （add_effects 覆写）「我想我[药物]吸/吃太多了……」 我想我[Drug]吃太多了……「I think I took a bit too much [drug_name]...」 |
| 88 | `/datum/mood_event/minor_overdose/add_effects(drug_name)` | — | — | — | proc：按药物名动态生成描述 |
| 89 | `/datum/mood_event/tickled` | 0 | 2 MINUTES | 否 | 呜哦……我被挠痒了。真……有趣！「Wooh... I was tickled. It was... Funny!」 |
| 90 | `/datum/mood_event/bimbo` | 0 | — | 否 | 好……无……助……爱死了！「So-o... Help..less... Lo-ve it!」 |
| 91 | `/datum/mood_event/sadistic` | 0 | — | 否 | 他人的痛苦让我更快乐。「Others' suffering makes me happier.」 |
| 92 | `/datum/mood_event/stimulant_heavy/sundowner` | 继承 stimulant_heavy | — | 否 | 我他妈的无敌了！！！！「I'M FUCKING INVINCIBLE!!!!」 |
| 93 | `/datum/mood_event/stoned`（NOVA 覆写） | **+6** | 3 MINUTES | 否 | 你现在完全嗨了……「You're totally baked right now...」 |

#### 触发源对应表


| # | 事件 | 触发源 |
|---|---|---|
| 1 | `/datum/mood_event/anxiety_eyecontact` | 社交焦虑气质（social_anxiety quirk）与人眼神接触时（social_anxiety.dm:114） |
| 2 | `/datum/mood_event/spacer` | 中间节点 |
| 3 | `/datum/mood_event/spacer/in_space` | 太空人气质（spacer quirk）身处太空时（quirks/positive_quirks/spacer.dm:215） |
| 4 | `/datum/mood_event/spacer/on_planet` | 太空人气质在行星上（spacer.dm:147、242） |
| 5 | `/datum/mood_event/spacer/on_planet/too_long` | 行星久留计时触发（spacer quirk on_planet_for_too_long，变量引用 spacer.dm:164） |
| 6 | `/datum/mood_event/spacer/on_planet/nerfed` | 行星地图上的削弱版久留（同上，nerfed 分支） |
| 7 | `/datum/mood_event/spacer/on_planet/low_grav` | 低重力环境（低重力区停留，spacer.dm:242） |
| 8 | `/datum/mood_event/spacer/on_planet/low_grav/add_effects(...)` | 覆写 |
| 9 | `/datum/mood_event/spacer/on_planet/low_grav/proc/lower_mood_bonus()` | 覆写 |
| 10 | `/datum/mood_event/fat/add_effects(...)`（NOVA 覆写基础 fat） | 覆写 code/datums/mood_events/needs_events.dm 的 fat 事件（neutral_traits 特质联动） |
| 11 | `/datum/mood_event/stoat` | 中间节点 |
| 12 | `/datum/mood_event/stoat/enemies_nearby` | 中间节点 |
| 13 | `/datum/mood_event/stoat/enemies_nearby/one` | 鼬心移植者（stoat organ set）5 格内 1 个敌人（stoat_organs.dm:101） |
| 14 | `/datum/mood_event/stoat/enemies_nearby/multiple` | 2~4 个敌人（:103） |
| 15 | `/datum/mood_event/stoat/enemies_nearby/crowd` | 4+ 个敌人（:105） |
| 16 | `/datum/mood_event/stoat/alone` | 定义了但当前 tick 逻辑未直接引用（0 友时走 friendlies_nearby/one） |
| 17 | `/datum/mood_event/stoat/friendlies_nearby` | 中间节点 |
| 18 | `/datum/mood_event/stoat/friendlies_nearby/one` | 0~1 个友方（stoat_organs.dm:111，代码 0 人时用此） |
| 19 | `/datum/mood_event/stoat/friendlies_nearby/multiple` | 2~4 个友方（:113） |
| 20 | `/datum/mood_event/stoat/friendlies_nearby/crowd` | 4+ 个友方（:115） |
| 21 | `/datum/mood_event/surgery_emp_active` | 手术处理器芯片被 EMP 时（augments_internal.dm:443，强制疯狂状态） |
| 22 | `/datum/mood_event/surgery_emp_expired` | EMP 效果结束后（:444，定时器补发） |
| 23 | `/datum/mood_event/rapid_wound_healing` | NOVA 快速愈合堆叠物（healing stack items）快速修复伤口后（healing_stack_items.dm:92） |
| 24 | `/datum/mood_event/traitor_poster_antag` | 叛徒看到自己贴的煽动海报（demoraliser 组件按视角分发，:57） |
| 25 | `/datum/mood_event/traitor_poster_crew` | 普通船员看到海报（:63） |
| 26 | `/datum/mood_event/traitor_poster_auth` | 安保/指挥层看到海报（:60） |
| 27 | `/datum/mood_event/traitor_graffiti_antag` | 叛徒看到自己画的辛迪加涂鸦 |
| 28 | `/datum/mood_event/traitor_graffiti_crew` | 普通船员看到涂鸦 |
| 29 | `/datum/mood_event/traitor_graffiti_auth` | 安保/指挥层看到涂鸦 |
| 30 | `/datum/mood_event/traitor_module_antag` | 叛徒看到自己装的士气打击模块（如坏灯泡/发霉物） |
| 31 | `/datum/mood_event/traitor_module_crew` | 普通船员看到模块 |
| 32 | `/datum/mood_event/traitor_module_auth` | 安保/指挥层看到模块 |
| 33 | `/datum/mood_event/shadow_realm` | 被异教徒献祭拖入暗影界时（sacrifice_knowledge.dm:452） |
| 34 | `/datum/mood_event/shadow_realm_live` | 从暗影界幸存归来（:594） |
| 35 | `/datum/mood_event/shadow_realm_live_sad` | 幸存者（非异教徒）归来后（:598） |
| 36 | `/datum/mood_event/shadow_realm_live_sad_heretic` | 异教徒被献祭羞辱后（:596） |
| 37 | `/datum/mood_event/heretics/lunatic` | 月之狂人（moon lunatic）异教徒反派人物的常驻 moodlet（antag_moodlet 变量，moon_lunatic.dm:9） |
| 38 | `/datum/mood_event/soultrapped_heretic` | 灵魂禁锢异教徒（soultrapped heretic）反派常驻 moodlet（antag_moodlet，:8） |
| 39 | `/datum/mood_event/mansus_dream_fatigue` | 异教徒 Mansus 梦境疲劳状态效果（dreams.dm:38） |
| 40 | `/datum/mood_event/fallen_changeling` | 堕落变体（fallen changeling）反派人物的常驻 moodlet（antag_moodlet，fallen_changeling.dm:7） |
| 41 | `/datum/mood_event/royale_survivor` | 大逃杀（Rumble Royale）幸存者（survivalist.dm:86） |
| 42 | `/datum/mood_event/clown_world` | 法师大仪式小丑终局（clown finale）中"热爱小丑"（TRAIT_CLOWN_ENJOYER）者（clown.dm:24） |
| 43 | `/datum/mood_event/clown_world/add_effects(param)` | 覆写 |
| 44 | `/datum/mood_event/soul_core_torment` | 灵魂核心（soul core MOD）穿戴者灵能低于阈值时（get_backlash_type 返回，mod_core.dm:616/622，每 10 秒刷） |
| 45 | `/datum/mood_event/soul_core_torment/heretic` | 穿戴者是异教徒时的专用文本（:619） |
| 46 | `/datum/mood_event/soul_core_discomfort` | 灵能较低时（:624） |
| 47 | `/datum/mood_event/soul_core_warning` | 灵能轻度不足预警（mod_core.dm:590） |
| 48 | `/datum/mood_event/banish_nightmare` | 宗教仪式"驱逐噩梦"成功（banish_nightmare.dm:70） |
| 49 | `/datum/mood_event/dream_interrupted` | 梦境预兆被打断（dream_portent.dm:77） |
| 50 | `/datum/mood_event/dream_failed` | 梦境预兆解读失败（:66） |
| 51 | `/datum/mood_event/poster_mood` | 查看激励海报（poster_motivational.dm:104） |
| 52 | `/datum/mood_event/depressing_party` | 被黑客（emag）的氛围机器人播放压抑派对（vibebot_abilities.dm:39 变量引用） |
| 53 | `/datum/mood_event/festive_party` | 正常氛围机器人播放欢乐派对（同上） |
| 54 | `/datum/mood_event/artifact_effect_good_major` | 神器"感觉"效果-大幅增益（feelings.dm:142） |
| 55 | `/datum/mood_event/artifact_effect_good_minor` | 神器效果-小幅增益（:147） |
| 56 | `/datum/mood_event/artifact_effect_bad_major` | 神器效果-大幅负面（:127） |
| 57 | `/datum/mood_event/artifact_effect_bad_minor` | 神器效果-小幅负面（:132） |
| 58 | `/datum/mood_event/faded_hope_lavaland` | NOVA 远古考古物品"褪色的希望徽记"（xenoarch_item.dm:247） |
| 59 | `/datum/mood_event/brushed` | 被他人用梳子梳头/须/尾（hairbrush.dm:71、91） |
| 60 | `/datum/mood_event/brushed/add_effects(mob/brusher, brush_target)` | 覆写 |
| 61 | `/datum/mood_event/brushed/expert` | 梳毛者拥有"发型大师"特质（hairbrush.dm:87） |
| 62 | `/datum/mood_event/brushed/expert/add_effects(mob/brusher, brush_target)` | 覆写 |
| 63 | `/datum/mood_event/brushed/self` | 自己梳自己（hairbrush.dm:82） |
| 64 | `/datum/mood_event/brushed/self/add_effects(brush_target)` | 覆写 |
| 65 | `/datum/mood_event/brushed/self/expert` | 自我意识/发型大师特质者自己梳（:79） |
| 66 | `/datum/mood_event/brushed/self/expert/add_effects(brush_target)` | 覆写 |
| 67 | `/datum/mood_event/brushed/pet/add_effects(mob/brushed_pet)` | 给宠物梳毛（:101） |
| 68 | `/datum/mood_event/harshly_brushed` | **已定义但当前代码未引用（dead code）**——战斗模式下粗暴梳毛仅触发 brushed 事件 |
| 69 | `/datum/mood_event/harsh_brushed/add_effects(brush_target)` | 未引用的覆写 |
| 70 | `/datum/mood_event/hotspring` | NOVA 泳池/温泉浸泡（pool.dm:64） |
| 71 | `/datum/mood_event/hotspring/nerfed` | 原始猫娘地图的削弱版温泉（map_items.dm:42） |
| 72 | `/datum/mood_event/tucked_in` | NOVA 低温舱：给他人掖好被子（cryopod.dm:605） |
| 73 | `/datum/mood_event/tucked_in/add_effects(mob/tuckee)` | 覆写 |
| 74 | `/datum/mood_event/warmhug/rubi` | NOVA 捐赠者玩偶"蜜蜂鲁比"（warmhug 子类，donator_plushies.dm:797） |
| 75 | `/datum/mood_event/warmhug/parsec` | NOVA 工作人员奖励玩偶"帕塞克"（staff_reward_plushies.dm:109） |
| 76 | `/datum/mood_event/plasteel_chef` | NOVA 活动奖励"塑料钢大厨"头衔/物品（plasteel_chef.dm:35、51） |
| 77 | `/datum/mood_event/xenohybrid_resin` | NOVA 异种杂交体（xenohybrid）站在异形树脂上（alien/organs.dm:66 引用） |
| 78 | `/datum/mood_event/hemophage_feed_human` | 血噬族吸食人类血液（corrupted_tongue.dm:163） |
| 79 | `/datum/mood_event/disgust/hemophage_feed_monkey` | 吸食猴子血（:109，disgust 子类） |
| 80 | `/datum/mood_event/disgust/hemophage_feed_humonkey` | 吸食人猴（humonkey）血（:116） |
| 81 | `/datum/mood_event/orgasm` | 高潮后（多巴胺试剂 dopamine.dm:27） |
| 82 | `/datum/mood_event/climaxself` | 自我高潮（climax.dm:187、203） |
| 83 | `/datum/mood_event/overgasm` | 过度高潮/纵欲过度（dopamine.dm:38） |
| 84 | `/datum/mood_event/ropebunny` | 绳缚兔状态效果（ropebunny.dm:10） |
| 85 | `/datum/mood_event/subspace` | 打屁股导致的"亚空间"状态（spank_related.dm:12） |
| 86 | `/datum/mood_event/perv_spanked` | 被皮鞭抽打/打屁股垫惩罚（leather_whip.dm:251、265；spanking_pad.dm:80） |
| 87 | `/datum/mood_event/minor_overdose` | NOVA 情趣化学物轻微过量：樟脑（camphor.dm:86）、魅魔之酿（incubus_draft.dm:171）、魅魔奶（succubus_milk.dm:158） |
| 88 | `/datum/mood_event/minor_overdose/add_effects(drug_name)` | 覆写 |
| 89 | `/datum/mood_event/tickled` | 被羽毛挠痒（feather.dm:72） |
| 90 | `/datum/mood_event/bimbo` | NOVA"金发傻妞"（bimbo）气质常驻 moodlet（lewd_quirks.dm:161） |
| 91 | `/datum/mood_event/sadistic` | NOVA"施虐狂"（sadistic）气质常驻 moodlet（:272） |
| 92 | `/datum/mood_event/stimulant_heavy/sundowner` | NOVA 恶魔之眼（demoneye）药物强刺激（stimulant_heavy 子类，demoneye.dm:105） |
| 93 | `/datum/mood_event/stoned`（NOVA 覆写） | NOVA THC 大麻试剂/状态效果"stoned"（thc.dm:61；基础版 code/datums/mood_events/drug_events.dm 为 +1） |

---

## 附录 · 代码路径索引

| 系统 | 文件 | 行数 |
|---|---|---|
| 心情核心 | `code/datums/mood.dm` | 698 |
| 心情子系统 | `code/controllers/subsystem/moods.dm` | ~10 |
| 事件基类 | `code/datums/mood_events/_mood_event.dm` | 172 |
| 通用正面事件 | `code/datums/mood_events/generic_positive_events.dm` | 721 |
| 通用负面事件 | `code/datums/mood_events/generic_negative_events.dm` | 662 |
| 死亡事件 | `code/datums/mood_events/death.dm` | 303 |
| 需求事件 | `code/datums/mood_events/needs_events.dm` | 177 |
| 药物事件 | `code/datums/mood_events/drug_events.dm` | 141 |
| 饮料事件 | `code/datums/mood_events/drink_events.dm` | 98 |
| 食物事件 | `code/datums/mood_events/food_events.dm` | 63 |
| 病态事件 | `code/datums/mood_events/morbid_events.dm` | 60 |
| 异端画作 | `code/datums/mood_events/eldritch_painting_events.dm` | 48 |
| 美感事件 | `code/datums/mood_events/beauty_events.dm` | 23 |
| 斧头创伤 | `code/datums/mood_events/axe_events.dm` | 20 |
| 区域事件 | `code/datums/mood_events/area_events.dm` | 17 |
| DNA 注入 | `code/datums/mood_events/dna_infuser_events.dm` | 18 |
| 分级常量 | `code/__DEFINES/mood.dm` | 124 |
| 营养阈值 | `code/__DEFINES/mobs.dm`（L367-389） | — |
| 叛徒士气打击 | `code/modules/antagonists/traitor/components/demoraliser.dm` | — |
| 异教徒献祭 | `code/modules/antagonists/heretic/knowledge/sacrifice_knowledge/sacrifice_moodlets.dm` | — |
| 神器情感 | `modular_nova/modules/xenoarchartifacts/effects/feelings.dm` | — |
| 梳子事件 | `modular_nova/modules/hairbrush/code/mood_events.dm` | — |
| 情趣事件 | `modular_nova/modules/modular_items/lewd_items/` | — |

---

> **索引**：本页共 1 篇，覆盖心情系统全量（核心机制 + 530 事件 + 触发源 + 特殊机制）。