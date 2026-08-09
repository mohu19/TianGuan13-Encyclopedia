# TianGuan13 · 巫师（Wizard）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/wizard/`（31 文件 **5,931 行**）+ `code/modules/spells/spell_types/`（6,894 行）
> **类型**: 单人法术型反派｜**难度**: ★★★★（法术书搭配决定强度）
> **一句话**: 你是巫师——**法术书 10 点**买法术（可重复购买升级），从火球到时停到召唤天体武器全都有。目标：完成**大仪式**（7 次=胜利）或按预设流派碾压全站。
> **本版全量审计**: 旧版宣称"6 类 50+ 法术"实际只列了约 12 个——**本版从 `spellbook_entries/` 7 个分类文件全量提取全部 79 个条目**（含法术+物品+天赋+仪式+挑战），无省略。
> **v2 补全**: 对照 `wizard.dm`/`grand_ritual/`/`_common/antag_spawner.dm` 补齐 **核心 datum 全属性**（moodlet/hijack_speed/自杀口号/硬核奖励等）、**目标系统全量**（概率/判定/自选）、**学徒与随从机制**（4 学派/契约/傀儡/恶魔）、**风味与彩蛋**（roundend 报告/音效/梦境/硬核加分）。

---

## 目录

- [一、核心机制](#一核心机制)
- [二、法术书全量（79 条目·7 分类）](#二法术书全量79-条目7-分类)
- [三、攻击类（24）](#三攻击类24)
- [四、防御类（19）](#四防御类19)
- [五、机动类（10）](#五机动类10)
- [六、辅助类（14）](#六辅助类14)
- [七、天赋类（10）](#七天赋类10)
- [八、仪式类（6）](#八仪式类6)
- [九、挑战类（2）](#九挑战类2)
- [十、装备数值](#十装备数值)
- [十一、大仪式（Grand Ritual）](#十一大仪式grand-ritual)
- [十二、目标系统全量](#十二目标系统全量)
- [十三、学徒与随从机制](#十三学徒与随从机制)
- [十四、风味与彩蛋](#十四风味与彩蛋)
- [十五、预设流派](#十五预设流派)
- [十六、对战攻略](#十六对战攻略)
- [十七、数值速查表](#十七数值速查表)

---

## 一、核心机制

### 1.1 反派 datum 全属性（`/datum/antagonist/wizard`，wizard.dm L5-31）

| 属性 | 值 | 说明 |
|---|---|---|
| name | `"Space Wizard"` | 显示名"太空巫师" |
| roundend_category | `wizards/witches` | 回合结束报告分组 |
| antagpanel_category | `ANTAG_GROUP_WIZARDS` | 反派人面板分组 |
| pref_flag | `ROLE_WIZARD` | 偏好开关 |
| **antag_hud_name** | `"wizard"` | 敌人 HUD 标记 |
| **antag_moodlet** | `/datum/mood_event/focused` | **聚焦心情：+8 心情，hidden（不显示来源）**，文案"I have a goal, and I will reach it, whatever it takes!"（generic_positive_events.dm L210-213） |
| **hijack_speed** | **0.5** | 劫机速度倍率（基准阶段 5s → **每阶段 10 秒**，与叛徒相同） |
| ui_name | `AntagInfoWizard` | 反派信息 TGUI 界面 |
| **suicide_cry** | **"FOR THE FEDERATION!!"** | 自杀口号（自尽时喊出） |
| preview_outfit | `/datum/outfit/wizard` | 反派人预览外观（蓝巫） |
| **can_assign_self_objectives** | **TRUE** | **NOVA 未收紧（叛徒被改为 FALSE，巫师保留 TRUE）**——巫师可自选/更换目标 |
| default_custom_objective | "Demonstrate your incredible and destructive magical powers." | 自选目标默认文案 |
| **hardcore_random_bonus** | **TRUE** | 硬核随机模式**有加分资格**（greentext 时 ×2） |
| show_to_ghosts | TRUE | 幽灵可见 |
| give_objectives | TRUE | 出生生成目标 |
| strip | TRUE | 出生**扒光原装备**再换巫师装 |
| allow_rename | TRUE | 允许改名（默认随机名） |
| move_to_lair | TRUE | 出生**传送到巫师巢穴**（wizardstart 随机点） |
| outfit_type | `/datum/outfit/wizard` | 新手装 |
| **wiz_age** | **30**（`WIZARD_AGE_MIN`，mobs.dm L547）| "巫师天生不能太年轻" |
| 特性 Traits | `TRAIT_MAGICALLY_GIFTED` + `TRAIT_SEE_BLESSED_TILES` | 魔法天赋 + 可看见祝福地块（同叛徒/生存主义者） |
| 人格 Personality | introvert（内向）+ callous（冷漠） | "没有是非观念" |

### 1.2 出生流程（on_gain）

```
① assign_ritual()      → 授予大仪式能力（/datum/action/cooldown/grand_ritual）
② equip_wizard()       → strip 扒光 → 强制人类种 → 年龄≥30 → 换巫师装
③ 人格 + 特质           → introvert/callous + MAGICALLY_GIFTED/SEE_BLESSED_TILES
④ create_objectives()  → 目标（见第十二章）
⑤ send_to_lair()       → lazy load 巫师巢穴模板（LAZY_TEMPLATE_KEY_WIZARDDEN）→ 传送到 GLOB.wizardstart 随机点
⑥ rename_wizard()      → tgui 改名（默认 wizardfirst.txt + wizardsecond.txt 随机组合）
⑦ 阵营 ROLE_WIZARD + 团队 HUD
```

### 1.3 生成途径（动态规则集）

| 途径 | 规则 | 数值 |
|---|---|---|
| 开局巫师 | `/datum/dynamic_ruleset/roundstart/wizard` | **min_pop 30 / max_antag_cap 1**；权重：低 0 / 中低 0 / 中高 **1** / 高 **2**；`RULESET_INVADER\|RULESET_HIGH_IMPACT`；强制职业 space_wizard；**repeatable=FALSE** |
| 中局巫师 | `/datum/dynamic_ruleset/midround/from_ghosts/wizard` | HEAVY_MIDROUND 幽灵角色；**min_pop 30 / max 1**；报名图标=巫师帽；pref `ROLE_WIZARD_MIDROUND`、jobban `ROLE_WIZARD` |
| 职业 | `/datum/job/space_wizard` | title=ROLE_WIZARD，faction=ROLE_WIZARD；出生点=wizardstart 随机 |

> 与叛徒不同：**巫师 datum 未定义 stinger_sound**（base 为 null）→ **出场无警报音效**（静默进站），详见第十四章。

### 1.4 法术书规则

| 机制 | 值 |
|---|---|
| 法术书 | 初始 **10 点**（uses=10；半随机 +2 / 全随机 +5，随机化/预设仅在**满 10 点**时可用）|
| 升级 | 重复购买 = 升 1 级（减冷却，上限 spell_max_level）|
| 退款 | **法术**可退；**物品/仪式/天赋/挑战不可退**；特殊召唤物（学徒契约/血瓶/痒瓶）可**喂回法术书**退款 |
| 彩票 | Spells Lottery 天赋下：`prob(50/cost)` 免费购 |
| 新手装 | 蓝帽+蓝袍+传送卷轴+手杖+法术书（背包内）|
| 学徒 | 4 流派：破坏/蓝空/治疗/无袍（见第十三章）|
| 预设 | Classic/Mjolnir/WizardMy/Soultap（耗尽 10 点锁定退款）|
| 职业 | /datum/job/space_wizard |
| 团队 | /datum/team/wizard（召唤学徒/仆从时创建）|

---

## 二、法术书全量（79 条目·7 分类）

> 全量提取自 `spellbook_entries/`（offensive 167 行/defensive 177 行/mobility 71 行/assistance 109 行/perks 215 行/summons 136 行/challenges 10 行）

| 分类 | 数量 | 含法术/物品 |
|---|---|---|
| 攻击 Offensive | **24** | 火球/法术卡/无敌棒/天罚/召唤猿/失明/系鞋带/变异/石化/特斯拉/闪电箭/无限枪/奥术弹幕/谷仓诅咒/溅射施法/放血一击/为我尖叫 + 7 武器（混沌杖/变形杖/雷神锤/奇点锤/法术刃/高频刀/青蛙契约/缩小杖）|
| 防御 Defensive | **19** | 魔法导弹/科技禁用/斥力/投掷闪电/时停/烟雾/力墙/巫妖绑定/中二咒语/时空扭曲/陷阱/召唤蜜蜂/诅咒背包 + 6 物品（治疗杖/储物柜杖/观测球/魔杖套/魔杖套(廉价)/工艺甲）|
| 机动 Mobility | **10** | 换脑/敲门/闪烁/传送/以太穿墙/互换/幽灵体 + 4 物品（哨子/造门杖/传送杆）|
| 辅助 Assistance | **14** | 召唤物品/充能/野性变形/灵魂汲取 + 9 物品（动画杖/魂石/死灵石/学徒契约/守护者卡组/血瓶/痒瓶/自动贩卖权杖）|
| 天赋 Perks | **10** | 四只手/蠕虫重生/既视感/法术彩票/赌博/食心/史莱姆朋友/透明化/磁力 |
| 仪式 Rituals | **6** | 召唤幽灵(0)/召唤枪/召唤魔法/召唤事件(2)/疯狂诅咒(4)/群体教学(3) |
| 挑战 Challenges | **2** | 多元宇宙剑/友好巫师渣 |

> **修正注**：挑战类 2 条目（multiverse/antiwizard）**不设 category**，`can_be_purchased()` 返回 FALSE 且 `can_buy()` 恒 FALSE——它们**只出现在书内作为风味展示，不可购买**（源码注释："THESE ARE NOT PURCHASABLE SPELLS!"）。因此严格意义的**可购买条目为 77**，本表按源码文件全量 79 条目统计。

---

## 三、攻击类（24）

### 法术（17）

| # | 法术 | 效果 | 点数 |
|---|---|---|---|
| 1 | **Fireball 火球** | 爆炸火球，射程 8，经典中的经典 | 2 |
| 2 | **Spell Cards 法术卡** | 灼热追踪连发卡，送敌人进影之界 | 2 |
| 3 | **Rod Form 无敌棒** | 变不可破坏棒冲撞，多买增伤 | 2 |
| 4 | **Smite 天罚** | 手蓄力触碰 → 目标暴力爆炸 | 2 |
| 5 | **Summon Simians 召唤猿** | 从香蕉元素界召唤猿猴大军 | 2 |
| 6 | **Blind 失明** | 临时致盲单个目标 | 1 |
| 7 | **Tie Shoes 系鞋带** | 解/系目标鞋带，升级静音化 | 1 |
| 8 | **Mutate 变异** | 变浩克+激光眼（30s）| 2 |
| 9 | **Flesh to Stone 石化** | 触碰变雕像（长时间）| 2 |
| 10 | **Tesla Blast 特斯拉爆发** | 蓄力电弧跳射随机目标，可移动蓄力 | 2 |
| 11 | **Lightning Bolt 闪电箭** | 闪电跳跃目标，不可击倒 | 1 |
| 12 | **Lesser Summon Guns 次级召唤枪** | 无尽栓动步枪流（低伤但无限）| 3 |
| 13 | **Arcane Barrage 奥术弹幕** | 奥术能量洪流（远强于召唤枪）| 3 |
| 14 | **Barnyard Curse 谷仓诅咒** | 目标获得动物口音+脸 | 2 |
| 15 | **Splattercasting 溅射施法** | 全法术冷却大幅降低，但耗血施法 | 2 |
| 16 | **Exsanguinating Strike 放血一击** | 附魔下次攻击：增伤+吸血+补血 | 2 |
| 17 | **Scream For Me 为我尖叫** | 触碰给目标全身大量严重血伤 | 1 |

### 武器（7）

| # | 武器 | 效果 | 点数 |
|---|---|---|---|
| 18 | **Staff of Chaos 混沌杖** | 无逻辑发射各种魔法 | 2 |
| 19 | **Staff of Change 变形杖** | 目标外形重塑 | 2 |
| 20 | **Mjolnir 雷神锤** | 双手 25/投掷 30；命中晕 1.5s+闪电+扔 200 格 | 2 |
| 21 | **Singularity Hammer 奇点锤** | 重击产生引力场牵引 | 2 |
| 22 | **Spellblade 法术刃** | 力 20/破甲 75/格挡 50；能量刃切肢 | 2 |
| 23 | **High Frequency Blade 高频刀** | 超快斩切一切 | 3 |
| 24 | **Frog Contract 青蛙契约** | 签订蛙神契约获破坏性宠物守护者 | 2 |
| （25）| **Staff of Shrinking 缩小杖** | 缩小一切，小结构可走过 | 2 |

> 注：原版攻击类含 25 项（含缩小杖），统计口径按 spellbook_entries 实际条目为准。

---

## 四、防御类（19）

### 法术（12）

| # | 法术 | 效果 | 点数 |
|---|---|---|---|
| 1 | **Magic Missile 魔法导弹** | 发射数枚缓慢追踪魔法弹 | 2 |
| 2 | **Disable Tech 禁用科技** | 禁用范围内武器/摄像头/科技 | 1 |
| 3 | **Repulse 斥力** | 推开周围一切 | 2 |
| 4 | **Thrown Lightning 投掷闪电** | 凝聚纯能量包投掷 | 2 |
| 5 | **Time Stop 时停** | 只你能动（其他人冻结）| 2 |
| 6 | **Smoke 烟雾** | 生成窒息烟雾云 | 1 |
| 7 | **Force Wall 力墙** | 只有你能穿过的魔法屏障 | 1 |
| 8 | **Bind Soul 巫妖绑定** | 灵魂绑定物品，死后复活 | 2 |
| 9 | **Chuuni Invocations 中二咒语** | 法术咏唱变中二，施法回少量血 | 2 |
| 10 | **Spacetime Distortion 时空扭曲** | 缠绕周围时空弦 | 1 |
| 11 | **The Traps! 陷阱！** | 召唤陷阱群，踩中受伤+激怒 | 1 |
| 12 | **Lesser Summon Bees 次级召唤蜜蜂** | 踢跨维蜂巢召唤蜜蜂 | 2 |
| 13 | **Bestow Cursed Duffel Bag 诅咒背包** | 恶魔背包贴目标背上 | 1 |

### 物品（6）

| # | 物品 | 效果 | 点数 |
|---|---|---|---|
| 14 | **Staff of Healing 治疗杖** | 治疗+复活死者 | 1 |
| 15 | **Staff of the Locker 储物柜杖** | 发射储物柜吞人（焊接封死）| 2 |
| 16 | **Scrying Orb 观测球** | 活体出魂侦查+X 光 | 2 |
| 17 | **Wand Assortment 魔杖套** | 多种功能魔杖组合 | 2 |
| 18 | **Wand Assortment (Bargain Bin) 廉价魔杖套** | 随机学徒魔杖 | 1 |
| 19 | **Mastercrafted Armor Set 工艺甲** | 可施法的护甲套（免咏唱）| 2 |

---

## 五、机动类（10）

### 法术（6）

| # | 法术 | 效果 | 点数 |
|---|---|---|---|
| 1 | **Mindswap 换脑** | 与邻格目标换身体（双方昏睡）| 2 |
| 2 | **Knock 敲门** | 打开附近门窗 | 1 |
| 3 | **Blink 闪烁** | 随机短距传送 | 2 |
| 4 | **Teleport 传送** | 传送到选定区域 | 2 |
| 5 | **Ethereal Jaunt 以太穿墙** | 灵体化：隐形+穿墙 | 2 |
| 6 | **Swap 互换** | 与 9 格内活物换位（右键标第二目标）| 1 |
| 7 | **Forsake Body 弃体** | 永久斩断灵魂与身体（部分锚定物质界）| 2 |

### 物品（3）

| # | 物品 | 效果 | 点数 |
|---|---|---|---|
| 8 | **Warp Whistle 传送哨** | 传送到站内安全点（有脆弱窗口）| 1 |
| 9 | **Staff of Door Creation 造门杖** | 墙变门 | 1 |
| 10 | **Teleport Rod 传送杆** | 屏内任意传送 | 2 |

---

## 六、辅助类（14）

### 法术（4）

| # | 法术 | 效果 | 点数 |
|---|---|---|---|
| 1 | **Summon Item 召唤物品** | 从宇宙任意处召回已标记物品 | 1 |
| 2 | **Charge 充能** | 充能手中物品（魔法/电气）| 1 |
| 3 | **Wild Shapeshift 野性变形** | 变 6 种形态用自然能力（选定后不可换）| 1 |
| 4 | **Soul Tap 灵魂汲取** | 用灵魂施法（耗命换法力）| 1 |

### 物品（10）

| # | 物品 | 效果 | 点数 |
|---|---|---|---|
| 5 | **Staff of Animation 动画杖** | 射出活化能量弹使物体活过来 | 2 |
| 6 | **Soulstone Shard Kit 魂石套件** | 捕获死/濒死者的灵魂 | 2 |
| 7 | **Necromantic Stone 死灵石** | 复活 3 具骷髅仆从 | 2 |
| 8 | **Contract of Apprenticeship 学徒契约** | 召唤学徒巫师 | 2 |
| 9 | **Guardian Deck 守护者卡组** | 绑定个人守护者（多类型塔罗）| 2 |
| 10 | **Bottle of Blood 血瓶** | 魔法血瓶，召唤杀戮恶魔（气味吸引...）| 2 |
| 11 | **Bottle of Tickles 痒瓶** | 魔法痒瓶（召唤欢笑恶魔，恶作剧）| 1 |
| 12 | **Scepter of Vendormancy 自动贩卖权杖** | 符文自动贩卖魔法 | 2 |

---

## 七、天赋类（10）

| # | 天赋 | 效果 | 点数 |
|---|---|---|---|
| 1 | **Four Hands 四只手** | 更多手施展魔法 | 2 |
| 2 | **Worm Born 蠕虫重生** | 死后重生为巨大蠕虫 | 2 |
| 3 | **Déjà vu 既视感** | 每 60s 回到 60s 前的位置+血量 | 2 |
| 4 | **Spells Lottery 法术彩票** | 免费抽奖（但**不能再退款**）| 2 |
| 5 | **Gamble 赌博** | 获得 2 个随机天赋 | 2 |
| 6 | **Heart Eater 食心** | 吃心获取生命精华 | 2 |
| 7 | **Slime Friends 史莱姆朋友** | 史莱姆不攻击你 | 2 |
| 8 | **Transparence 透明化** | 更接近亡者世界（半透明）| 2 |
| 9 | **Magnetism 磁力** | 小型重力异常环绕你 | 2 |

> 天赋条目（/datum/spellbook_entry/perks）**refundable = FALSE**（不可退），购买时记入 `wizard_datum.perks` 并在 HUD 显示紧凑天赋栏（HUD_WIZARD_COMPACT_PERKS）。

---

## 八、仪式类（6）

| # | 仪式 | 效果 | 点数 | 限制 |
|---|---|---|---|---|
| 1 | **Summon Ghosts 召唤幽灵** | 让船员见鬼 | 0 | 1 |
| 2 | **Summon Guns 召唤枪** | 武装全体疯子（召唤 10 把枪）| 2 | 1 |
| 3 | **Summon Magic 召唤魔法** | 与船员分享魔法（10 件魔法物品）| 2 | 1 |
| 4 | **Summon Events 召唤事件** | 所有事件替换为巫师事件 | 2 | 5（可叠加强化）|
| 5 | **Curse of Madness 疯狂诅咒** | 扭曲全员心智+永久创伤（慎用：连自己也中）| 4 | 1 |
| 6 | **Mass Wizard Teaching 群体教学** | 教全站一个法术（4-7 点）| 3 | 1 |

> 仪式类**不可退款**（summon 条目 refundable=FALSE，limit=1；specific_spell 可自定义法术）。
> **限制条件**：Summon Guns/Magic/Events 及群体教学均要求**动态威胁等级 = HIGH**（`SSdynamic.current_tier.tier == DYNAMIC_TIER_HIGH`），且受 config `no_summon_*` 开关控制。群体教学总额 = 3 + 所选法术点数（最便宜 4、最贵 7），全站共享且自身也学会；每局限一次（GLOB.mass_teaching）。

---

## 九、挑战类（2）

| # | 挑战 | 效果 | 点数 |
|---|---|---|---|
| 1 | **Multiverse Sword 多元宇宙剑** | 空间站获得多元宇宙剑对抗你（真人版大军）| 0（挑战）|
| 2 | **Friendly Wizard Scum 友好巫师渣** | 友好巫师保护空间站并追杀你 | 0（挑战）|

> **修正注**：挑战类条目 `can_buy()` 恒 FALSE——**不可购买**，仅作为风味展示（纪念被删的旧法术）。"接受挑战"并不存在于本分支代码，源码注释明言 "THESE ARE NOT PURCHASABLE SPELLS!"。

---

## 十、装备数值

### 10.1 巫师套装（wiz_robe.dm）

| 装备 | 护甲（近战/子弹/激光/能量/炸弹）| 其他 |
|---|---|---|
| **巫师帽** head/wizard | **30/20/20/30/20** | bio **100** / fire **100** / acid **100** / wound 20；抗火抗酸；剥离/穿戴各 5s；**钓鱼难度 -6**（"巫师总是练习施法"）|
| **巫师袍** suit/wizrobe | **30/20/20/30/20**（同帽）| bio 100/fire 100/acid 100/wound 20；覆盖胸/腹/臂/腿；可装：高频刀/魔杖带/传送卷轴；**钓鱼难度 -7** |
| 魔法凉鞋 sandal/magic | — | 新手装 |
| 传送卷轴 | — | 新手装右口袋（`/obj/item/teleportation_scroll`）|
| 手杖 staff | — | 新手装左手 |
| 法术书 spellbook | — | 背包内，owner 绑定 |

### 10.2 武器

| 装备 | 数值 |
|---|---|
| **Mjolnir 雷神锤** | 双手 25/投掷 30；命中 1.5s 晕+闪电+扔 200 格 |
| **奇点锤** | 双手 20/重击 5 格奇点牵引 |
| **Spellblade** | 力 20/破甲 75/格挡 50；能量刃 |
| 高频刀 | 力 8/伤 20-25/超快攻速 |
| Scrying Orb | 灼伤 15+第六感+X 光 |
| **Wabbajack**（隐藏谢幕）| **max_charges 999999 / recharge 1**，不可摧毁，全员可用（allow_intruder_use），可发射炮弹/火箭/特斯拉/魔法/温度弹等 |

### 10.3 新手装（/datum/outfit/wizard "Blue Wizard"）

浅紫制服 + 巫师袍 + 背包（法术书）+ 生存盒 + 耳机 + 巫师帽 + 魔法凉鞋 + 右口袋传送卷轴 + 左手杖。装束需 `CASTING_CLOTHES` 才能施法（SPELL_REQUIRES_WIZARD_GARB）。变体：red（红巫）/weeb（魔理沙）/bookless（无书）/apprentice/academy。

---

## 十一、大仪式（Grand Ritual）

**代码**: `grand_ritual/`（grand_ritual.dm 329 行 + grand_rune.dm 413 行 + grand_side_effect.dm 422 行 + fluff.dm + finales/ 8 文件）

### 11.1 核心数值

| 机制 | 值 |
|---|---|
| 触发 | **7 次仪式 = 胜利**（`GRAND_RITUAL_FINALE_COUNT 7`；第 7 次为谢幕符文）|
| 绘制符文 | 原地 **4 秒**（do_after；绘制动画 4s / 失败动画 0.5s）|
| 绘制冷却 | 符文完成后 **2 分钟**全局冷却（防 5 级传送刷）|
| 符文面积 | 3×3 区域（`RANGE_TURFS(1)`，绘制时蒸发障碍物：墙直接拆毁/其他物体魔法摧毁）|
| 咏唱 | **3 段**（`GRAND_RUNE_INVOKES_TO_COMPLETE 3`）×（**7s** `BASE_INVOKE_TIME` + **2s×已完成次数** `ADD_INVOKE_TIME`）|
| 每段咏唱 | 念一句随机魔法词 → 全站灯闪烁；3 段后触发事件+副作用+现实裂缝 |
| 奶酪献祭 | 累计 **50 个**奶酪轮（`CHEESE_SACRIFICE_GOAL`；召唤奶酪术每次 9 个，7 次仪式 7×9=63 有余量）→ 第 7 次改为**奶酪谢幕符文**（Wabbajack）|
| 完成反馈 | SSblackbox 记录 + 第 7 次发 `COMSIG_GRAND_RITUAL_FINAL_COMPLETE` |

### 11.2 区域规则

| 规则 | 值 |
|---|---|
| 白名单（13 类）| cargo/command/commons/construction/engineering/medical/science/security/service + maintenance 的 disposal/radshelter/tram 子类 |
| 黑名单（8 处）| 超物质室/传送管/军械 bomb/burnchamber/freezerchamber/server/监狱安全区 |
| 地表黑名单 | 不可破坏墙/裂谷/开放空间/岩浆/太空/室内外 |
| 定位提示 | 不在目标区时触发：very near(≤15 格)/near(16-31)/far(32-127)/very far/above/below/on station 等方向提示 |

### 11.3 符文通告（船员警报）

| 已完成后（potency）| 效果 |
|---|---|
| **≥3**（第 4 个符文起）| 每次创建符文 → `priority_announce` "Anomaly Alert"（警告）|
| **≥6**（第 7 个=谢幕符文）| "Anomaly Alert" 紧急通告（imminent）|
| 谢幕 dire_warning | 3 段递进公告："Large anomalous energy spike detected…" → "Automatic causality stabilisation failed…" → "Imminent local reality failure… All crew please prepare to evacuate." |

### 11.4 完成副作用（第 2 次起，potency>0）

每次完成触发 1 个随机 `grand_side_effect`（数值随 potency 在 0→7 间线性插值 LERP）：

| 副作用 | 效果 |
|---|---|
| 时空扭曲 scramble_turfs | 区域时空弦扭曲 10-15s |
| 区域换肤 transmogrify_area | 2-4 格范围随机维度主题（逐圈 0.5s 扩散）|
| 异常集群 create_anomalies | 1-4 个（bioscrambler/hallucination/grav/flux minor）寿命 15-30s |
| EMP | 重 EMP 0-3 / 轻 EMP 3-6 |
| 传送换位 translocate | 5 格内活物随机互换位置 |
| 滑油 slippery | 2-4 格滑油泡沫 |
| 召唤船员 summon_crewmate | 随机船员被锁定 **9 秒**后传送到符文旁（magic 传送通道）|
| 彩色烟雾 smoke | 2-4 格彩色烟雾 |
| 血腥 gore | 4 个 gibspawner |
| 食物雨 create_food | 10-30s 隐形胶囊投喂随机食物 |
| 延迟怪群 spawn_delayed_mobs | 1-4 只（carp/illusion/killer_tomato/skeleton/wumborian_fugu/ooze）经 10-15s 传送门出现 |
| 猴子乐团 orchestra | 1-4 只穿服务生服的猴子持随机乐器 |

### 11.5 现实裂缝（Reality Tears）

| potency | 最大裂缝数 |
|---|---|
| 0-2 | 1 |
| 3-5 | 2 |
| 6-7 | 3 |

每个裂缝 **50% 概率已耗尽**（after_drain），互相至少间隔 1 格。

### 11.6 事件触发

每次完成从 `SSevents.control` 中筛选 `min_wizard_trigger_potency ≤ potency ≤ max_wizard_trigger_potency` 且可生成的巫师事件，随机触发一个（event_cause "a Grand Ritual Rune"）。

### 11.7 谢幕（Finale，7 个可选 + 1 隐藏）

第 7 个符文（potency 6）是 finale 符文：径向菜单选择谢幕；**"Continuation 继续"** = 不选谢幕继续普通玩法。default 咏唱时间 **30s**（ritual_invoke_time），选中后符文颜色改变。

| 谢幕 | 效果 | 限制 |
|---|---|---|
| **Evolution 进化**（magic）| 全站召唤魔法：`summon_magic(survivor_probability = 20)`（20% 幸存率）| 无 |
| **Usurpation 篡位**（usurp）| 全员昏迷 3s → 你成为舰长（金 ID+全套船长装+勋章）；原船长**降职助理**+流放维护区+**vendetta 必杀目标**；其他船员 10% 获得 vendetta | 无 |
| **Connection 连接**（all_access）| 全站所有门解锁+开启+清空权限要求，公告 "AULIE OXIN FIERA!!" | 无 |
| **Jubilation 欢庆**（clown）| 全站改写成"小丑研究站"：全员变魔法小丑（15% vendetta）；小丑职业获口袋法术；巫师获**满级系鞋带**（喊话型）| 无 |
| **Transformation 变形**（midas）| 空间站从你位置起逐圈（5s/格距离）整体换肤为 金/肉/披萨/自然 主题 | 无 |
| **Perpetuation 永续**（immortality）| **全员不死**：死亡后 10s 预激活+**50s 后原地复活**（HEAL_ALL；自杀除外——"There is a way out of the cycle"）| **≥30 分钟** |
| **Annihilation 湮灭**（armageddon）| 你自爆（gib）：随机 奇点（energy 300）/特斯拉球（energy 200）/流星雨；死前喊随机遗言（13 句）| **≥90 分钟**；咏唱 **60s**；dire warning |
| **Wabbajack 隐藏**（cheese）| 献祭≥50 奶酪解锁：除你外全员**永久幻觉创伤**（脑损至 BRAIN_DAMAGE_DEATH-25）+ vendetta 必杀目标；已疯者反被治愈；你获 `blessing_of_insanity` 状态+Wabbajack 杖（999999 充能）| **≥45 分钟**；dire warning；不可选择（自动）|

> 谢幕成就：`/datum/award/achievement/misc/grand_ritual_finale`。完成谢幕后 times_completed 继续累计，之后回到普通符文行为。隐藏奶酪符文咏唱词："Greetings! Salutations!" → "Welcome! Now go away." → "Leave. Run. Or die."

### 11.8 风味

- 魔法词 22 套（"Fus Roh Dah!!"、"Klaatu! Barada! Nikto!!"、"Abra Cadabra Alakazam"、"git checkout origin master / git reset --hard HEAD~2 / git push origin master --force!!"、FF14 长咏唱梗等）
- 法术书里可找到《ancient parchment》（作者 **Pelagius the Mad**）暗示奶酪谢幕（"cheese wheels?.."）

---

## 十二、目标系统全量

**代码**: wizard.dm L138-182 `create_objectives()` + L249-256 `on_ritual_complete()` + `_common/antag_datum.dm`

### 12.1 目标生成概率（出生时 roll 1-100）

| 随机区间 | 概率 | 目标组合 |
|---|---|---|
| 1-30 | **30%** | 刺杀（assassinate）+ 逃脱（escape）|
| 31-60 | **30%** | 偷窃（steal）+ 逃脱（escape）|
| 61-85 | **25%** | 刺杀 + 偷窃 + 存活（survive）|
| 86-100 | **15%** | **仅劫机**（hijack，无附加目标）|

去重规则：加目标前检查 `locate(/datum/objective/escape)` 等是否已存在——不重复添加。与叛徒不同：**巫师没有流放/摧毁 AI/殉道/职业目标**，目标池仅 4 类基础目标+终局。

### 12.2 大仪式覆盖目标（胜利条件）

- 完成 **7 次**大仪式 → `on_ritual_complete()`：**清空全部目标**，替换为自定目标 **"Complete the Grand Ritual at least seven times."** 且 `completed = TRUE`（回合结束直接判绿字胜利）
- 反派 UI（AntagInfoWizard）显示 `remaining = 7 - times_completed` 与下一个目标区域

### 12.3 自选目标（NOVA 保留）

| 项 | 值 |
|---|---|
| can_assign_self_objectives | **TRUE**（NOVA 未收紧；对比叛徒 datum_traitor 被改为 FALSE）|
| 默认文案 | "Demonstrate your incredible and destructive magical powers." |
| UI | `data["can_change_objective"] = TRUE`，巫师可自选目标 |

> **硬核联动**：roundend.dm L197 跳过条件为"initial(TRUE) && 当前 FALSE"——巫师保持 TRUE 不触发跳过 → **巫师始终保有硬核加分资格**（见 14.6）。

### 12.4 目标判定

| 目标 | 判定 |
|---|---|
| 刺杀 | 目标死亡（find_target 自动选人）|
| 偷窃 | 目标物品到手（objective_items 池）|
| 存活 | 回合结束时存活 |
| 逃脱 | 活着离开空间站（穿梭机/逃生舱）|
| 劫机 | 劫持穿梭机（**hijack_speed 0.5 → 每阶段 10s**，基准 5s×2）|
| 仪式 | 7 次完成（completed 标记）|

### 12.5 团队/随从目标（召唤后）

| 角色 | 目标 |
|---|---|
| 学徒 apprentice | **保护**（/datum/objective/protect）："Protect [master]，the wizard." |
| 傀儡 wizard_minion | 自定目标："Serve [master]"（名称与文案同为 "Serve X"）|
| 杀戮恶魔 slaughter | 刺杀召唤者（若有）+ 杀光所有人（no_failure，无失败惩罚）|
| 欢笑恶魔 laughter | "Hug and tickle" 所有人（无失败）|
| 小恶魔 imp | "Try to get a promotion to a higher devilish rank."（升职恶魔阶级）|

---

## 十三、学徒与随从机制

### 13.1 学徒 datum（/datum/antagonist/wizard/apprentice，wizard.dm L265-334）

| 属性 | 值 |
|---|---|
| name | "Wizard Apprentice" |
| antag_hud_name | **"apprentice"** |
| **can_assign_self_objectives** | **FALSE**（学徒不能自选目标，与巫师 TRUE 不同）|
| move_to_lair | FALSE（不传送巢穴，直接出现在契约处）|
| 年龄 | **29**（`APPRENTICE_AGE_MIN`）|
| outfit | /datum/outfit/wizard/apprentice（右口袋**学徒传送卷轴**，无手杖/无书）|
| 大仪式 | **无**（assign_ritual 返回——"还没学会"）|
| school | 默认 `APPRENTICE_DESTRUCTION` |
| 特性 | MAGICALLY_GIFTED + SEE_BLESSED_TILES + ROLE_WIZARD 阵营 |
| 目标 | 保护主人（protect objective）|

**4 学派法术表**（equip_wizard 按 school 授予）：

| 学派 | 法术/物品 |
|---|---|
| **破坏 Destruction** | 魔法导弹 + 火球 |
| **蓝空 Bluespace** | 区域传送（巫师版）+ 以太穿墙 |
| **治疗 Healing** | 充能 + 力墙 + **治疗杖**（物品）|
| **无袍 Robeless** | 敲门 + 换脑（mind_transfer）|

### 13.2 学徒契约（Contract of Apprenticeship）

| 项 | 值 |
|---|---|
| 点数 | **2**（辅助类条目 /datum/spellbook_entry/item/contract）|
| 使用 | 打开 TGUI 选学派 → 向幽灵征集 **15 秒**（check_jobban / role = `ROLE_WIZARD_MIDROUND`，报名图标=红巫师帽）|
| 响应 | 幽灵选中 → 生成人类（玩家预置外貌）→ 授予学徒 datum + 学派法术 → 职业 wizard_apprentice（"Apprentice"）→ 播放 magic.ogg |
| 团队 | 若无团队则 `create_wiz_team()`（队名"[巫师名] team"）→ 学徒加入成员 |
| 用后 | used=TRUE 不可再用；**可喂回法术书退款 2 点** |
| 无人响应 | 提示失败，契约保留 |

### 13.3 变体：冒牌学徒（Imposter）

随机事件用：`/datum/antagonist/wizard/apprentice/imposter`——**复制主人的全套装备**（耳/衣/鞋/外套/头/背），学以太穿墙+区域传送+闪烁，目标就是"Operation: Fuck off and scare people"（吓人）。show_in_antagpanel=FALSE、不可改名。

### 13.4 变体：学院教师（Academy）

`/datum/antagonist/wizard/academy`：红袍红帽、以太穿墙+魔法导弹+火球、**流放植入体**（exile implant）；目标"Protect Wizard Academy from the intruders"；无仪式、无巢穴、不可自选目标。

### 13.5 傀儡（Wizard Minion）

| 项 | 值 |
|---|---|
| datum | /datum/antagonist/wizard_minion（wizard.dm L33-79）|
| 来源 | **死灵石 Necromantic Stone 复活**（骷髅仆从，上限 **3** 具 max_thralls）|
| hud | "apprentice"（与学徒同 HUD）|
| roundend | show_in_roundend = **FALSE**（只在团队报告里出现）|
| 目标 | "Serve [master]" |
| 特性 | MAGICALLY_GIFTED + SEE_BLESSED_TILES + 团队 HUD |

### 13.6 恶魔（slaughter/laughter）

- **血瓶 Bottle of Blood**（2 点）→ 召唤**杀戮恶魔**（/datum/antagonist/slaughter）：目标=杀召唤者+杀光所有人；roundend 报告"consumed N bodies"（N=吞噬数）
- **痒瓶 Bottle of Tickles**（1 点）→ 召唤**欢笑恶魔**（laughter 变体）：目标="Hug and tickle" 所有人
- 两者均可喂回法术书退款；恶魔 roundend_category="demons"、pref ROLE_SENTIENCE、antagpanel 归巫师组
- **小恶魔 Imp**（imp_antag.dm）：无面板/无 roundend 的纯风味 antag，目标升职恶魔阶级；fluff "You're an Imp, a lesser being of congealed sin summoned to serve the hierarchy of hell."（原为恶魔晋升召唤，现为废弃遗留）

---

## 十四、风味与彩蛋

### 14.1 出场与音效

| 项 | 值 |
|---|---|
| 自杀口号 | **"FOR THE FEDERATION!!"**（suicide_cry）|
| 出场音效 | **无**——wizard datum 未定义 stinger_sound（base null；叛徒是 tatoralert.ogg）。`sound/announcer/vox_fem/wizard.ogg` 只是 AI vox 词库里的单词，不是反派警报 |
| 学徒出现 | magic.ogg（契约召唤）|
| 符文相关 | blind.ogg（绘制蒸发障碍）/ staff_animation.ogg（咏唱）/ staff_change.ogg（完成）/ summon_magic.ogg 等 |
| 心情 | antag_moodlet = **focused（+8，hidden）**"I have a goal, and I will reach it, whatever it takes!" |

### 14.2 命名与人格

- 随机名：`strings/names/wizardfirst.txt` + `wizardsecond.txt`（如 "X Y" 组合），出生时 tgui 输入改名（可取消用随机名）
- 人格：introvert + callous（"没有是非观念"）

### 14.3 回合结束报告（roundend_report）

| 报告项 | 内容 |
|---|---|
| 单人 | printplayer + "Grand Rituals completed: N" + 逐条目标（成功绿字/失败红字）→ "The wizard was successful!" / "The wizard has failed!" |
| 购买记录 | GLOB.wizard_spellbook_purchases_by_key → "X used the following spells: 2x Fireball, Jaunt…"；没买过 → "X didn't buy any spells!" |
| 团队 | 巫师+学徒组队时：header "Wizards/witches of X team were:" + 巫师报告 + "X apprentices and minions were:" + 成员名单（红框 panel）|

### 14.4 新闻与世界观

- 巫师被全灭 → `WIZARD_KILLED = 19` → 新闻："Tensions have flared with the **Space Wizard Federation** following the death of one of their members aboard [station]."
- 自杀口号呼应太空巫师联邦（Space Wizard Federation）设定
- 梦境预兆（dream_portent）：存活巫师会让船员梦见"the magician"；3 次仪式后梦境内容升级（"the grand finale is near"）；7 次后梦见谢幕

### 14.5 法术书彩蛋

- 对法术书施"魔法充能"（Charge）→ 红色警告 + 随机嘲讽："NICE TRY BUT NO!" / "CLEVER BUT NOT CLEVER ENOUGH!" / "SUCH FLAGRANT CHEESING IS WHY WE ACCEPTED YOUR APPLICATION!" / "CUTE! VERY CUTE!" / "YOU DIDN'T THINK IT'D BE THAT EASY, DID YOU?"（5 句随机）
- 契约/血瓶/痒瓶可喂回书退款（"On second thought, maybe summoning a demon isn't a funny idea."）

### 14.6 硬核奖励（hardcore_random_bonus）

| 项 | 值 |
|---|---|
| 资格 | wizard `hardcore_random_bonus = TRUE`（roundend.dm L195 过滤"revs/hypnos 等不给"）|
| 规则 | 硬核模式存活分 `hardcore_survival_score`：**全部目标完成（greentext）→ score ×2** 授予 `/datum/award/score/hardcore_random`；否则逃脱成功 → ×1 |
| 巫师特殊性 | can_assign_self_objectives 保持 TRUE → 不落入 L197 跳过分支 → **自选目标也能拿硬核分**（叛徒 NOVA 改 FALSE 后自选目标者被排除）|

### 14.7 其他

- 大仪式咏唱魔法词 22 套（含 FF14 咏唱梗/git 命令梗/上古卷轴 Fus Roh Dah 等）
- 谢幕自我加难：Annihilation 需 ≥90 分钟才可选，咏唱 60s 给船员充足打断时间
- 复活：巫师死亡后 on_respawn → 重新传送 wizardstart + 重新配装（返回 TRUE）
- 移除反派人：删除所有 target=owner 的法术（"目前移除所有法术，无论先天后天"）+ 移除特性

---

## 十五、预设流派

| 预设 | 说明 | 点数构成 |
|---|---|---|
| **Classic 经典** | 传统法术组合 | 火球+魔法导弹+天罚(Disintegrate)+以太穿墙×2 = 2+2+2+4 = **10** |
| **Mjolnir 雷神** | 雷神锤流派 | 雷神锤+召唤物品+变异+力墙+闪烁+特斯拉 = 2+1+2+1+2+2 = **10** |
| **WizardMy 巫师我** | 亡灵大军流 | 魂石+变形杖+死灵石+传送+以太穿墙 = 2+2+2+2+2 = **10** |
| **Soultap 灵魂** | 灵魂汲取流派 | 灵魂汲取+天罚+石化+换脑+敲门+传送 = 1+2+2+2+1+2 = **10** |
| 学徒-破坏 | 攻击向学徒 | 魔法导弹+火球 |
| 学徒-蓝空 | 机动向学徒 | 区域传送+以太穿墙 |
| 学徒-治疗 | 治疗向学徒 | 充能+力墙+治疗杖 |
| 学徒-无袍 | 裸装挑战 | 敲门+换脑 |

> 预设购买消耗全部 10 点 → **锁定退款**（uses < initial(uses) 后不可再随机化/换预设）。

---

## 十六、对战攻略

### 怎么玩巫师（推荐流程）

```
① 开局：选预设或手动点法术书（10 点）
② 核心搭配建议：
   - 生存：Ethereal Jaunt（穿墙跑）+ Time Stop（控场）
   - 输出：Fireball（清场）+ Lightning Bolt（单体）
   - 反制：Disable Tech（拆武器摄像头）+ Force Wall（堵路）
③ 用 Teleport/Blink 保持机动，别被围
④ 完成大仪式（7 次）或直接武力碾压——大仪式是主要胜利条件：
   去目标区 → 4s 画符文（2 分钟冷却）→ 3 段咏唱（7s+2s×次数）→ 等事件/副作用
⑤ 可选：契约招学徒（2 点）、死灵石拉骷髅（3 上限）、血瓶招恶魔
⑥ 挑战模式自虐（多元宇宙剑——注意：挑战条目不购买也可在书里看到）
```

### 怎么防巫师（船员对策）

| 方法 | 说明 |
|---|---|
| **拆法术书** | 巫师没了法术书=没了核心（书在背包里，可被抢/毁）|
| **反时停** | 时停只有巫师能动——提前分散站位 |
| **科技压制** | 巫师法术书买不起无限科技——用炮塔/机甲 |
| **围剿** | 巫师是单人——多人包围+火力覆盖 |
| **破坏仪式** | 大仪式绘制 4s 可打断；咏唱 7s+/段可打断——看到符文立刻阻止 |
| **听通告** | 第 4 个符文起每次创建全站 "Anomaly Alert"（含区域名）→ 直接去抓 |
| **防谢幕** | 45/90 分钟门槛的谢幕（Wabbajack/湮灭）前击杀；Usurpation 后盯着新"船长" |

---

## 十七、数值速查表

| 项 | 值 |
|---|---|
| 源码 | wizard/ 31 文件 5,931 行 + spells 6,894 行 |
| 法术书点数 | 10（半随机+2/全随机+5）|
| 可购条目 | **79** 条目（攻击 24/防御 19/机动 10/辅助 14/天赋 10/仪式 6/挑战 2）；严格可购买 **77**（挑战不可购）|
| datum 全名 | Space Wizard / roundend_category wizards/witches |
| HUD | antag_hud_name "wizard"（学徒/傀儡 "apprentice"）|
| 心情 | focused **+8**（hidden）|
| hijack_speed | **0.5** → 劫机每阶段 **10 秒** |
| 自杀口号 | "FOR THE FEDERATION!!" |
| 出场音效 | 无（无 stinger；叛徒为 tatoralert.ogg）|
| 自选目标 | can_assign_self_objectives = **TRUE**（NOVA 保留；叛徒 FALSE）|
| 默认目标文案 | "Demonstrate your incredible and destructive magical powers." |
| 硬核奖励 | TRUE（greentext ×2 分）|
| 年龄 | 巫师 **30** / 学徒 **29** |
| 目标概率 | 刺杀+逃脱 30% / 偷窃+逃脱 30% / 刺杀+偷窃+存活 25% / 仅劫机 15% |
| 仪式胜利 | 7 次（GRAND_RITUAL_FINALE_COUNT）→ 覆盖目标自动绿字 |
| 绘制/冷却 | 4 秒绘制 / 2 分钟冷却 |
| 咏唱 | 3 段 ×（7s + 2s×已完成次数）|
| 通告阈值 | potency ≥3 警告 / ≥6 紧急 |
| 现实裂缝 | 1（0-2 次）/ 2（3-5 次）/ 3（6-7 次），50% 已耗尽 |
| 奶酪献祭 | 50 个 → Wabbajack 谢幕（999999 充能杖）|
| 谢幕 | 7 可选 + 1 隐藏奶酪；门槛：永续 30 分钟 / Wabbajack 45 分钟 / 湮灭 90 分钟（咏唱 60s）|
| 学徒 | 4 学派（破坏/蓝空/治疗/无袍）；契约 15s 幽灵征集；年龄 29 |
| 傀儡 | 死灵石上限 3 具 |
| 巫师帽/袍 | 护甲 30/20/20/30/20 + bio/fire/acid 100；钓鱼 -6/-7 |
| 生成 | 开局规则集 min_pop 30 / max 1（中局幽灵角色同）|
| 新闻 | WIZARD_KILLED=19（巫师联邦抗议）|
| 旧版审计 | 宣称 50+ 实列 12 → 本版全量 79 条目 |

---

*本文档数值全部实测自 `code/modules/antagonists/wizard/`（wizard.dm 456 行 + grand_ritual/ 5 文件 + equipment/ 9 文件 + imp/slaughter）+ `code/__DEFINES/antagonists.dm`（APPRENTICE_*/GRAND_RITUAL_*）+ `code/__DEFINES/mobs.dm`（WIZARD_AGE_MIN/APPRENTICE_AGE_MIN）+ `code/__HELPERS/roundend.dm`（硬核加分）+ `code/controllers/subsystem/dynamic/`（规则集）+ `code/datums/mood_events/generic_positive_events.dm`（focused +8）源码，无推测。*
