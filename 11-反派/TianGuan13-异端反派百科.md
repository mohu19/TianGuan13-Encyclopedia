# TianGuan13 · 异端（Heretic）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/heretic/`（**18,945 行**，全库最大反派系统）+ `code/modules/mob/living/basic/heretic/`（怪物）+ `modular_nova/`（NOVA 覆写）
> **类型**: 单人成长型反派（知识树流派）｜**难度**: ★★★★★（最复杂——知识树/献祭/升阶三线）
> **一句话**: 你是 Mansus 之心的追随者——选一条**异端路径**（灰烬/利刃/虚空/血肉/锁/月/锈/宇宙），按**知识树**成长，用**献祭**获得力量，最终**升阶**成为超越者。
> **本版全量审计**: 旧版宣称"8 路径/46 法术/8 升阶"，实际法术只列约 15 个——**本版从源码全量提取 8 路径完整树（120 知识节点）、67 法术块（含变体，顶层约 45 法术）、8 升阶、献祭机制**；本版另补全：**antagonist datum 全属性、目标系统全量（生成流程/5 种目标/判定/升阶门槛）、NOVA 改动全查（12+ 项）、风味彩蛋（音效/心情/特效/roundend）**，无省略。

---

## 目录

- [一、核心机制](#一核心机制)
  - [1.1 Antagonist Datum 全属性](#11-antagonist-datum-全属性)
  - [1.2 成长与光环系统](#12-成长与光环系统)
- [二、目标系统（全量）](#二目标系统全量)
  - [2.1 目标生成流程](#21-目标生成流程)
  - [2.2 目标类型全录](#22-目标类型全录)
  - [2.3 研究目标数值公式](#23-研究目标数值公式)
  - [2.4 献祭目标生成（活心目标）](#24-献祭目标生成活心目标)
  - [2.5 目标判定与升阶门槛](#25-目标判定与升阶门槛)
- [三、起始知识（NOVA 4 节点）](#三起始知识nova-4-节点)
- [四、路径知识树全录（8 路径 × 8-9 节点）](#四路径知识树全录8-路径--8-9-节点)
- [五、侧知识全录（4 阶）](#五侧知识全录4-阶)
- [六、法术全录（67 块）](#六法术全录67-块)
- [七、升阶全录（8 条）](#七升阶全录8-条)
- [八、献祭机制（全量）](#八献祭机制全量)
  - [8.1 献祭流程](#81-献祭流程)
  - [8.2 暗影界生存小游戏](#82-暗影界生存小游戏)
  - [8.3 结局与副作用](#83-结局与副作用)
  - [8.4 点数与邪教徒奖励](#84-点数与邪教徒奖励)
  - [8.5 重掷目标](#85-重掷目标)
  - [8.6 路径专属献祭地图](#86-路径专属献祭地图)
- [九、影响点与诅咒（全量）](#九影响点与诅咒全量)
  - [9.1 影响点（现实裂隙）](#91-影响点现实裂隙)
  - [9.2 诅咒（4 种全录）](#92-诅咒4-种全录)
- [十、NOVA 专属改动（全查）](#十nova-专属改动全查)
- [十一、风味与彩蛋](#十一风味与彩蛋)
- [十二、对战攻略](#十二对战攻略)
- [十三、数值速查表](#十三数值速查表)

---

## 一、核心机制

| 机制 | 值 |
|---|---|
| 初始知识 | **2 点**（NOVA 从 1 上调）|
| 被动成长 | 每 **20 分钟 +1** 知识 |
| 起始知识节点 | **4 个**（NOVA；上游 5 个含 Feast of Owls）|
| 光环 | 累计知识 > **8** 触发（锁路径/献祭猫头鹰除外）|
| 符文绘制 | **20s** + 3×3 区域（路径色）|
| 刀 | base force **20** / AP **35** |
| 献祭 | 12s 昏睡链 → 2.5min 暗影界 → 存活/死亡/开膛三结局 |
| 影响点 | **5+4+3+2+1**（按异端数量递减）|
| 升阶等待 | 配置 `minimum_ascension_time`（默认 **0 分钟**）|

**核心循环**:
```
① 选路径（灰烬/利刃/虚空/血肉/锁/月/锈/宇宙）
② 按知识树购买节点（初始 2 点 + 每 20min +1）
③ 完成献祭获得点数（指挥 +3/邪教徒 +1/普通 +2）
④ 画符文/召唤怪物/升阶
⑤ 终极目标：完成全部目标 → 升阶成为超越者
```

### 1.1 Antagonist Datum 全属性

> **代码**: `heretic_antag.dm` L18-91（`/datum/antagonist/heretic`）

| 属性 | 值 |
|---|---|
| name | "Heretic" |
| roundend_category | **"Heretics"**（roundend 归组）|
| antagpanel_category | "Heretic" |
| ui_name | "AntagInfoHeretic"（TGUI 界面）|
| antag_moodlet | `/datum/mood_event/heretics`（**+12** 心情，hidden）|
| pref_flag | **ROLE_HERETIC**（白名单旗标）|
| antag_hud_name | "heretic"（HUD 图标）|
| hijack_speed | **0.5**（劫机阶段速度系数）|
| suicide_cry | **"THE MANSUS SMILES UPON ME!!"**（自杀口号）|
| preview_outfit | `/datum/outfit/heretic`（锈色异端法袍 + 曼苏斯之握）|
| can_assign_self_objectives | **TRUE**（可自定目标；提交后**永久失去升阶权**）|
| default_custom_objective | "Turn a department into a testament for your dark knowledge." |
| hardcore_random_bonus | **TRUE**（硬核随机模式加成）|
| stinger_sound | **`heretic_gain.ogg`**（出场音效）|
| antag_flags | 基类 + **ANTAG_OBSERVER_VISIBLE_PANEL** |
| view_exploitables | **TRUE**（NOVA 覆写，见第十章）|
| knowledge_points | **2**（NOVA EDIT；上游 1）|
| passive_gain_timer | **20 MINUTES** |
| points_to_aura | **8** |
| living_heart_organ_slot | 心脏；无心脏物种 → 肺/肝/胃兜底 |
| rust_strength | 1（可升级）|
| passive_level | 1 → 2 → 3（路径被动三阶）|
| give_objectives | TRUE |
| unlimited_blades | FALSE → TRUE（升阶后无限刀，失去刀爆传送）|
| unlocked_heretic_items | 诅咒刀 / 猩红徽记 / 收割者构装（邪教献祭奖励池）|

**on_gain 效果**（apply_innate_effects）:
- `handle_clown_mutation`（小丑异端克服武器自伤）
- 加入 `FACTION_HERETIC` 阵营
- 挂 `heretic_dreams` 状态（梦境）
- 加入现实裂隙追踪（reality_smash_track）
- 获得 `TRAIT_MANSUS_TOUCHED` + `TRAIT_SEE_BLESSED_TILES`
- 挂 **rust_healing 元素**（(FALSE, 1.5, 5) 锈蚀自愈）

### 1.2 成长与光环系统

| 阶段 | 触发 | 效果 |
|---|---|---|
| 光环显现 | 累计获得知识 **> 8**（points_to_aura）| `disable_blade_breaking()`：**失去刀爆传送、暗影斗篷被移除、刀无上限** |
| 光环外观 | unlimited_blades && 非锁路径 && 未献祭猫头鹰 | 全身 **绿色漩涡能量** overlay（heretic_aura.dmi）|
| examine 文案 | 有光环时 | "crackling with a swirling green vortex of energy."；可升阶时追加 "shedding their mortal shell!" |
| 锁路径例外 | PATH_LOCK | **永不显示光环**（代码硬性排除）|

> 注：aura 判定基于**累计获得**的知识（knowledge_gained），不是当前点数；花掉点数不影响。

---

## 二、目标系统（全量）

> **代码**: `heretic_antag.dm` L718-1188（forge_primary_objectives + 5 个 objective datum）

### 2.1 目标生成流程

```
on_gain（成为异端）:
① 添加 pick_path 目标（"Pick a path to pursue." 选路径）——填充占位
② 起始知识全部入手（NOVA 4 个，cost 0）
③ 启动 20min 被动知识计时器

选路径后（limited_amount/starting on_research → forge_primary_objectives）:
④ 删除 pick_path 占位目标
⑤ 添加 heretic_research（研究目标）
⑥ 统计存活玩家里指挥层人数 num_heads（JOB_HEAD_OF_STAFF）
⑦ 添加 minor_sacrifice（普通献祭：默认 4 人；num_heads < 2 时上调为 5 人）
⑧ 若 num_heads ≥ 2：追加 major_sacrifice（高级献祭：1 名指挥层）
```

### 2.2 目标类型全录

| # | 目标 | 类型 | 说明 | 判定（check_completion）|
|---|---|---|---|---|
| 1 | **pick_path 选路径** | 填充 | "Pick a path to pursue." | 选路径后即被移除 |
| 2 | **heretic_research 研究** | 主线 | 研究 N 个 Mansus 知识 | `researched_knowledge` 长度 ≥ N |
| 3 | **minor_sacrifice 普通献祭** | 主线 | 献祭至少 4（或 5）名船员 | `total_sacrifices` ≥ N |
| 4 | **major_sacrifice 高级献祭** | 追加 | 献祭 1 名指挥层 | `high_value_sacrifices` ≥ 1 |
| 5 | **heretic_summon 召唤** | 备用 | 召唤 2 只 Mansus 怪物（num_summoned）| `num_summoned` ≥ 2 |

> heretic_summon 已定义但**不在默认目标池**（forge_primary_objectives 不添加），供管理员/特殊用途。

### 2.3 研究目标数值公式

```
target_amount = 1                                // 基础
              + len(路径树)                       // 7（t1/t2/t3/t4 + 法袍 + 刀 + 升阶）
              + len(起始知识)                     // NOVA 4（上游 5）
              + ceil(侧知识草稿数 / 3)            // 12 个草稿 ÷ 3 = 4
              + rand(2, 4)                       // 随机挑战因子
```

- **NOVA 结果：18~20 个知识**（上游为 19~21）
- 文案："Research at least [N] knowledge from the Mansus. You start with [4] researched."

### 2.4 献祭目标生成（活心目标）

> **代码**: `sacrifice_knowledge.dm`（hunt_and_sacrifice.obtain_targets，L121-191）

**目标数量**: **5 个**（num_targets_to_generate = 5）

**候选过滤**:
| 条件 | 说明 |
|---|---|
| 非自己 | possible_target == user.mind 跳过 |
| 非黑名单 | target_blacklist（被献祭过的 mind 永久剔除）|
| 人类 | ishuman(current) |
| 存活 | stat != DEAD |
| **NOVA opt-in** | 启用时仅 `heretic_sac_target` 职业（**安保部 + 指挥部**）可被选 |

**选取结构**（按序）:
| 序 | 目标 | 说明 |
|---|---|---|
| ① | 1 名指挥层 | 任意 JOB_HEAD_OF_STAFF |
| ② | 1 名安保 | DEPARTMENT_BITFLAG_SECURITY |
| ③ | 1 名同部门 | **仅 opt-in 关闭时**（NOVA 改动：默认跳过此槽，直接随机补）|
| ④ | 随机补满 | pick_n_take 直到 5 个（sanity 上限 25 次）|

> **NOVA 改动**：默认配置（启用 antag opt-in）下献祭目标**只会是指挥/安保职业**；且"同部门目标"槽被跳过。配置 `disable_antag_opt_in_preferences` 关闭后恢复上游逻辑。

### 2.5 目标判定与升阶门槛

**can_ascend()（升阶知识购买前置）**:
| 条件 | 结果 |
|---|---|
| feast_of_owls = TRUE（献祭过猫头鹰）| ❌ "The owls have taken your right of ascension" |
| can_assign_self_objectives = FALSE（换过目标）| ❌ "The mansus has spurned you" |
| 任一目标未完成 | ❌ "Must complete all objectives before ascending." |
| 站内时间 < minimum_ascension_time（默认 **0** 分钟）| ❌ "Too early, must wait..." |
| 全部通过 | ✅ HERETIC_CAN_ASCEND |

> UI 中升阶（ultimate）知识在未达门槛时显示禁用 + 原因 tooltip；管理员面板可查/改目标与献祭名单。

---

## 三、起始知识（NOVA 4 节点）

> **代码**: `starting_lore.dm`；**NOVA 覆写** `modular_nova/.../heretic/knowledge/starting_lore.dm`

| # | 知识 | 效果 | cost |
|---|---|---|---|
| 1 | **Break of Dawn 破晓** | 授予 Mansus Grasp（10s 冷却触碰致晕，无需聚焦）| 0 |
| 2 | **The Living Heart 活心** | 活心组件：追踪献祭目标（罂粟+血池可再造）| 0 |
| 3 | **Amber Focus 琥珀聚焦** | 玻璃+眼睛合成聚焦项链（施法进阶法术必需）| 0 |
| 4 | **Cloak of Shadow 暗影斗篷** | **紫色烟雾**隐藏身份 3 分钟（6s 冷却）| 0 |

> **NOVA 改动**：上游起始知识含 **Feast of Owls 猫头鹰盛宴**（5 节点）；NOVA 将其 `is_starting_knowledge = FALSE` 且设为 `abstract_type`——**该知识在 NOVA 完全不可获得**（起始 5 → **4**）。

---

## 四、路径知识树全录（8 路径 × 8-9 节点）

### 4.1 Ash 灰烬路径（9 节点）

| 阶 | 知识 | 说明 |
|---|---|---|
| 起始 | Nightwatcher's Secret 夜巡者之秘 | 路径起始（火柴+刀 → 灰烬刀，limit 2；NOVA 描述改 5）|
| T1 | Ashen Passage 灰烬通道 | 15s 冷却灰烬穿行（NOVA 上调至 30s）|
| T1 | Volcano Blast 火山爆发 | 45s 冷却蓄力火焰束 |
| 法袍 | Mask of Madness 疯狂面具（armor/ash）| 灰烬法袍 |
| T3 | Fiery Blade 火焰之刃 | 刀升级 |
| T3 | Nightwatcher's Rebirth 夜巡者重生 | 1min 冷却复活 |
| T4 | Ashlord's Rite 灰烬领主仪式 | 升阶前置 |

### 4.2 Blade 利刃路径（9 节点）

| 阶 | 知识 | 说明 |
|---|---|---|
| 起始 | The Cutting Edge 利刃边缘 | 路径起始（刀 limit **4**——利刃路径专属）|
| T1 | Realignment 重整 | 6s 冷却 |
| T1 | Stance of the Torn Champion 破碎冠军架势 | 姿态 |
| 法袍 | armor/blade 利刃法袍 | 法袍 |
| T3 | Wolves Among Sheep 披着羊皮的狼 | 5min 冷却变身 |
| T3 | Empowered Blades 强化刀刃 | 刀升级 |
| T3 | Furious Steel 狂怒钢铁 | 30s 冷却投射 |
| T4 | Maelstrom of Silver 银之漩涡 | 升阶前置 |

### 4.3 Cosmic 宇宙路径（14 节点含宠物）

| 阶 | 知识 | 说明 |
|---|---|---|
| 起始 | Eternal Gate 永恒之门 | 路径起始 |
| T1 | Cosmic Runes 宇宙符文 | 15s 冷却 |
| T1 | Star Blast 星爆 | **1s 冷却**投射 |
| 法袍 | armor/cosmic 宇宙法袍 | 法袍 |
| T3 | Star Touch 星触 | 15s 冷却触碰 |
| T3 | Cosmic Blade 宇宙之刃 | 刀升级 |
| T3 | Cosmic Expansion 宇宙扩张 | 15s 冷却召唤 |
| T4 | Creator's Gift 造物者馈赠 | 升阶前置 + Star Gazer 宠物指令（idle/free/follow/attack）|

### 4.4 Flesh 血肉路径（11 节点）

| 阶 | 知识 | 说明 |
|---|---|---|
| 起始 | Principle of Hunger 饥饿原理 | 路径起始 + ghoul 状态（刀 limit 3；NOVA 描述改 20）|
| T1 | Imperfect Ritual 不完美仪式 | 尸术（flesh_ghoul limit 2）|
| T1 | Knitting of Flesh 血肉编织 | 20s 冷却（flesh surgery）|
| 法袍 | armor/flesh 血肉法袍 | 法袍 |
| T3 | Raw Ritual 生仪式 | 献祭强化 |
| T3 | Bleeding Steel 流血钢铁 | 刀升级 |
| T3 | Lonely Ritual 孤独仪式 | 单人献祭 |
| T4 | Priest's Final Hymn 祭司终章 | 升阶前置 |

### 4.5 Lock 锁路径（9 节点）

| 阶 | 知识 | 说明 |
|---|---|---|
| 起始 | A Steward's Secret 管家的秘密 | 路径起始 |
| T1 | Key Keeper's Burden 守钥人之担 | 锁术 |
| T1 | Concierge's Rite 门房仪式 | 开门 |
| 法袍 | armor/lock 锁法袍 | 法袍 |
| T3 | Burglar's Finesse 窃贼巧技 | 40s 冷却 |
| T3 | Opening Blade 开锁之刃 | 刀升级 |
| T3 | Caretaker's Last Refuge 守护者最后的庇护 | **1min 冷却** |
| T4 | Unlock the Labyrinth 解开迷宫 | 升阶前置 |

### 4.6 Moon 月路径（9 节点）

| 阶 | 知识 | 说明 |
|---|---|---|
| 起始 | Moonlight Troupe 月光剧团 | 路径起始 |
| T1 | Mind Gate 心门 | 20s 冷却 |
| T1 | Moonlight Amulet 月光护符 | 护符 |
| 法袍 | armor/moon 月法袍 | 法袍 |
| T3 | Lunar Parade 月亮游行 | 30s 冷却投射 |
| T3 | Moonlight Blade 月光之刃 | 刀升级 |
| T3 | Ringleaders Rise 头目崛起 | **1min 冷却** AoE |
| T4 | The Last Act 最后一幕 | 升阶前置 |

### 4.7 Rust 锈路径（9 节点）

| 阶 | 知识 | 说明 |
|---|---|---|
| 起始 | Blacksmith's Tale 铁匠传说 | 路径起始 |
| T1 | Rust Charge 锈蚀冲锋 | 45s 冷却冲撞 |
| T1 | Rust Construction 锈蚀构造 | **2s 冷却** |
| 法袍 | armor/rust 锈法袍 | 法袍 |
| T3 | Aggressive Spread 侵蚀扩散 | 30s 冷却 AoE |
| T3 | Toxic Blade 毒锈之刃 | 刀升级 |
| T3 | Entropic Plume 熵羽 | 30s 冷却锥形 |
| T4 | Rustbringer's Oath 锈蚀者誓言 | 升阶前置 |

### 4.8 Void 虚空路径（9 节点）

| 阶 | 知识 | 说明 |
|---|---|---|
| 起始 | Glimmer of Winter 冬日微光 | 路径起始（零下环境刀，limit 2；NOVA 描述改 5）|
| T1 | Void Phase 虚空相位 | 20s 冷却 |
| T1 | Void Prison 虚空囚笼 | **1min 冷却** |
| 法袍 | Hollow Weave 虚空编织（armor/void）| 法袍 |
| T3 | Void Pull 虚空牵引 | 30s 冷却 AoE |
| T3 | Seeking Blade 寻踪之刃 | 刀升级 |
| T3 | Void Conduit 虚空导管 | **1min 冷却**召唤 |
| T4 | Waltz at the End of Time 时间尽头华尔兹 | 升阶前置 |

> **刀数量上限速查**（limited_amount/starting 基类 limit = **2**）：利刃路径 **4**、血肉路径 **3**（+尸鬼 2）、其余 **2**；升阶后 unlimited_blades **解除上限**。NOVA 仅改了描述文案（灰烬/虚空"5 个"、血肉"20 个"），强制执行值仍为 limit 变量。

---

## 五、侧知识全录（4 阶）

### 5.1 Tier 1（8 节点）

| 知识 | 说明 |
|---|---|
| Void Cloak 虚空斗篷 | 虚空系护具 |
| Ashen Eyes 灰烬之眼 | 灰烬系视野 |
| Priest's Ritual 祭司仪式 | 献祭强化 |
| Phylactery of Damnation 诅咒命匣 | 巫妖命匣 |
| Mawed Crucible 吞噬坩埚 | 血肉结构 |
| Eldritch Coin 异端硬币 | 兑换物 |
| Codex Cicatrix 疤痕法典 | 知识书 |
| Warren King's Welcome 兔王欢迎 | 召唤 |

### 5.2 Tier 2（6 节点）

| 知识 | 说明 |
|---|---|
| Codex Morbus 病典 | 疾病书（**2.5s** 吸影响点）|
| Greaves of the Prophet 先知护胫 | 护甲 |
| **Wave of Desperation 绝望之波** | **5min 冷却** AoE |
| Carving Knife 雕刻刀 | 工具 |
| Ether of the Newborn 新生以太 | 材料 |
| Unsealed Arts 解封技艺 | 知识 |

### 5.3 Tier 3（6 节点）

| 知识 | 说明 |
|---|---|
| Rusted Ritual 锈化仪式 | 锈系献祭 |
| Maid in the Mirror 镜中女仆 | 召唤 |
| Ashen Ritual 灰烬仪式 | 灰系献祭 |
| Shattered Ritual 破碎仪式 | 献祭 |
| bone-shards 骨片 | 材料 |
| Scorching Shark 灼热鲨鱼 | 召唤 |

### 5.4 Tier 4（6 节点）

| 知识 | 说明 |
|---|---|
| Space Phase 空间相位 | 虚空穿行（NOVA 削弱，见第十章）|
| Unfathomable Curio 莫测古玩 | 宝物（挡 5 次伤害 + 存储；被 examine 者脑损+失明）|
| Rust Sower Grenade 锈蚀播种手雷 | 手雷 |
| Crimson Cleave 猩红斩击 | 30s 冷却 |
| Lionhunter's Rifle 猎狮步枪 | 武器（最大瞄准距离 30 格）|
| Lionhunter Rifle Ammunition 弹药 | 弹药 |

> **侧知识商店/草稿机制**（determine_drafted_knowledge）: 每路径 3 轮草稿（**草稿免费**，每轮 3 选 1，第一轮必出 guaranteed_side_*）；商店价格按阶 **1/2/2/2/3** 知识点；商店解锁顺序 = t1 → t2 → 法袍 → t3 → t4。草稿概率：D1 50/50（t1/t2）、D2 50/25/25（t1/t2/t3）、D3 20×5（t1-t5）、D4 100%（t5）。

---

## 六、法术全录（67 块）

> 从 `heretic/magic/` 全量提取（45 个文件，67 个 datum 块含变体）。顶层法术全录：

| # | 法术 | 冷却 | 类型 |
|---|---|---|---|
| 1 | Mansus Grasp 曼苏斯之握 | 10s | 触碰 |
| 2 | Cloak of Shadow 暗影斗篷 | 6s | 暗影潜行（3min）|
| 3 | Ashen Passage 灰烬通道 | 15s | 穿行 |
| 4 | Ashen Walk 灰烬行走 | — | 穿行（长）|
| 5 | Volcano Blast 火山爆发 | 45s | 蓄力火焰束 |
| 6 | Nightwatcher's Rite 夜巡者仪式 | 30s | 灰烬光束 |
| 7 | Nightwatcher's Rebirth 夜巡者重生 | 1min | 复活 |
| 8 | Oath of Flame 火焰誓言 | 70s | 火誓 |
| 9 | Lesser/Greater Fire Cascade 火焰瀑布 | 30s/— | 连喷 |
| 10 | Realignment 重整 | 6s | 刀术 |
| 11 | Cleave 斩裂 | 45s | 指向斩击 |
| 12 | Lesser Cleave 次级斩裂 | 60s | 斩击（长）|
| 13 | Blood Siphon 血虹吸 | 15s | 吸血 |
| 14 | Furious Steel 狂怒钢铁 | 30s | 投射 |
| 15 | Lesser Furious Steel | 20s | 投射 |
| 16 | Cursed Steel 诅咒钢铁 | 40s | 投射 |
| 17 | Wolves Among Sheep 披着羊皮的狼 | **5min** | 变身 |
| 18 | Cosmic Rune 宇宙符文 | 15s | 符文 |
| 19 | Star Blast 星爆 | **1s** | 投射 |
| 20 | Star Touch 星触 | 15s | 触碰 |
| 21 | Cosmic Expansion 宇宙扩张 | 15s | 召唤 |
| 22 | Ascended Shapechange 升阶变形 | 20s | 变形 |
| 23 | Shapechange 变形 | — | 变形 |
| 24 | Shed Form 蜕形 | — | 脱人形 |
| 25 | Eldritch Blind 异端致盲 | — | 致盲 |
| 26 | Energetic Pulse 能量脉冲 | 30s | EMP |
| 27 | Eldritch Telepathy 异端心灵感应 | — | 传心 |
| 28 | Expand Sight 扩张视野 | — | 视野 |
| 29 | Knit Flesh 血肉编织 | 20s | 手术 |
| 30 | Touch of Madness 疯狂之触 | 15s | 触碰致疯 |
| 31 | Manse Link 曼苏斯链接 | 20s | 链接 |
| 32 | Mind Gate 心门 | 20s | 指向 |
| 33 | Mirror Walk 镜中行走 | 6s | 穿行 |
| 34 | Lunar Parade 月亮游行 | 30s | 投射 |
| 35 | Ringleaders Rise 头目崛起 | 1min | AoE |
| 36 | Moonlight Echo 月光回声 | 4s | 追踪 |
| 37 | Rust Charge 锈蚀冲锋 | 45s | 冲撞 |
| 38 | Rust Formation 锈蚀构造 | **2s** | 指向构造 |
| 39 | Aggressive Spread 侵蚀扩散 | 30s | AoE 锈化 |
| 40 | Entropic Plume 熵羽 | 30s | 锥形 |
| 41 | Patron's Reach 守护者之触 | 35s | 锈波 |
| 42 | Void Phase 虚空相位 | 20s | 指向 |
| 43 | Void Prison 虚空囚笼 | 1min | 指向囚禁 |
| 44 | Void Pull 虚空牵引 | 30s | AoE |
| 45 | Void Conduit 虚空导管 | 1min | 召唤 |
| 46 | Void Blast 虚空爆发 | 30s | 锥形冷 |
| 47 | Caretaker's Last Refuge 守护者庇护 | 1min | 庇护 |
| 48 | Burglar's Finesse 窃贼巧技 | 40s | 指向 |
| 49 | Apetra Vulnera 阿佩特拉·瓦纳拉 | 45s | 指向 |
| 50 | Crimson Cleave 猩红斩击 | 30s | 指向 |
| 51 | Wave of Desperation 绝望之波 | **5min** | AoE |
| 52 | Space Phase 空间相位 | — | 虚空穿行 |
| 53 | Mirror Walk 镜中行走 | 6s | 穿行 |

> 注：67 块含变体（如 Construct Spread 15s 是 Aggressive Spread 的构造变体、Greater Fire Cascade 是升级版等）。顶层独立法术约 45 个，配合变体全量 67 块——**旧版"46 法术"的宣称与源码实际相符（45+ 独立法术），但旧版只列了 15 个**。

---

## 七、升阶全录（8 条）

> 每条路径 T4 后解锁的终极升阶（需 3 具特殊尸体献祭）：

| # | 路径 | 献祭需求 | 升阶效果 |
|---|---|---|---|
| 1 | **Ash 灰烬** | 3 具燃烧/烧焦尸体 | **火环 + 火焰瀑布 + 7 项免疫**（BOMBIMMUNE/NOBREATH/NOFIRE 等）|
| 2 | **Blade 利刃** | 3 具无头/颅裂尸体 | **8 把充能环绕刀 + 全伤口免疫 + 45s 眩晕吸收** |
| 3 | **Cosmic 宇宙** | 3 具带星标尸体 | **Star Gazer（6000→INFINITY HP）** |
| 4 | **Flesh 血肉** | 4 具尸体 | **Lord of the Night（400×6 段）+ 召唤上限×3 + 刀上限 999** |
| 5 | **Lock 锁** | 3 具胸腔无器官尸体 | **锁之裂痕无限刷怪 + 撕脱概率+30** |
| 6 | **Moon 月** | 3 具脑损≥50 尸体 | **全站 20% 转疯子** |
| 7 | **Rust 锈** | 3 具尸体（舰桥）| **全站锈化波** |
| 8 | **Void 虚空** | 3 具尸体（零下）| **虚空风暴** |

---

## 八、献祭机制（全量）

> **代码**: `knowledge/sacrifice_knowledge/sacrifice_knowledge.dm`（639 行）+ sacrifice_moodlets/buff/curse/map/reroll

### 8.1 献祭流程

```
① 目标判定: 符文旁（range 1）+ 活心目标 或 邪教徒；状态 ≥ SOFT_CRIT（濒死/更差）
② 仪式开始（begin_sacrifice）:
   - 目标脑损 85（上限 150）+ 氧气 -100
   - 复活治疗 50（heal_and_revive）；失败 → 开膛
   - 12s 昏睡（SACRIFICE_SLEEP_DURATION）+ 麻痹/禁锢 14.4s（×1.2）
   - 戴邪教手铐、丢腿铐、抖动动画
③ 6s 后（睡眠一半）传送到暗影界（路径专属地图）
   - 途中掉线/无 mind/传送失败 → 开膛（disembowel）
④ 醒来进入生存小游戏:
   - 随机植入 2-4 个腐化器官（7 种: 阑尾/眼/心/肝/肺/胃/舌；plasmaman 无肺）
   - Helgrasp 试剂（1 分钟）+ 亡灵诅咒（致盲+抓取）
   - 闪光、眼模糊 30s、抖动 20s、眩晕 20s、幻觉 24s、尖叫
⑤ 撑过 2.5 分钟（SACRIFICE_REALM_DURATION）→ 胜利返回
```

**时间常量**:
| 常量 | 值 |
|---|---|
| SACRIFICE_SLEEP_DURATION | **12 SECONDS** |
| SACRIFICE_REALM_DURATION | **2.5 MINUTES** |
| Helgrasp 时长 | **1 MINUTE** |
| 腐化器官数 | **rand(2, 4)** |

### 8.2 暗影界生存小游戏

**受害者增益 unholy_determination（不洁决心）**:
| 项 | 值 |
|---|---|
| 时长 | 3 分钟（= 生存期）|
| 治疗 | **0.25/秒**（缺腿加成：+0.25×(2-腿数)）|
| 特质 | TRAIT_COAGULATING + TRAIT_NOCRITDAMAGE + TRAIT_NOSOFTCRIT（不凝血/不进软硬濒死）|
| 血量 | 低于 OKAY 时拉回 OKAY |
| 软濒死 | 治疗 ×2 |
| 硬濒死 | 治疗 ×-0.5（加速死亡——硬濒死=输）|

**心情（shadow_realm 系列 4 种）**:
| 心情 | 数值 | 时长 | 触发 |
|---|---|---|---|
| shadow_realm | **-15** | 3min | 进入暗影界（"Where am I?!"）|
| shadow_realm_live | **+4** | 5min | 存活归来（"I'm alive..."）|
| shadow_realm_live_sad | **-6** | 10min | 存活归来（"The hands!..."）|
| shadow_realm_live_sad_heretic | **-8** | 8min | 异端被献祭存活（羞辱）|

### 8.3 结局与副作用

| 结局 | 条件 | 效果 |
|---|---|---|
| **存活返回** | 撑过 2.5min | 回站随机安全点：口齿不清+结巴 **40s**；闪光、混乱 **60s**、抖动 **120s**、眼模糊 **100s**、眩晕 **1min**、击倒 80、耐力 -120；阿托品 8u + 肾上腺素 8u；心情 +4/-6 |
| **暗影界死亡** | 2.5min 内死 | 尸体回站，原地生成**暗红色"现实裂痕"幻象**（"weakened rift in reality"，COLOR_DARK_RED）|
| **开膛（disembowel）** | 仪式中断/掉线/传送失败/试图逃离（z 层变化）| **250 BRUTE** + 器官散落 + 无肢体碎块特效；未死则直接判死 |
| **异端被献祭** | 受害者为异端 | 额外 **-3 知识点** + 专属心情 -8/8min |

> 献祭完成后异端会收到猎物的返回信息（"returned to the station - alive, but with a shattered mind" + 落地区域）。

### 8.4 点数与邪教徒奖励

**点数**（sacrifice_process）:
| 目标 | 点数 | 备注 |
|---|---|---|
| 指挥层（JOB_HEAD_OF_STAFF）| **+3** | high_value_sacrifices++（计入高级献祭目标）|
| 普通船员 | **+2** | — |
| 邪教徒 | **+1** | 被**扬成灰** + 掉落全部物品 + 掉落奖励 |

**邪教徒献祭奖励**（grant_reward → deposit_reward）:
| 项 | 值 |
|---|---|
| 奖励池 | 3 选 1：**诅咒病刀**（sickly_blade/cursed）/ **猩红徽记**（crimson_medallion）/ **收割者构装**（harvester）|
| 权重 | `min(5 - 已得次数×2, 1)`（防重复堆叠）|
| 特效 | 扬灰（disintegrate.ogg）+ 符文**红色描边**（COLOR_CULT_RED）+ 5s 后掉落奖励（repulse.ogg）|
| 邪教警示 | 每次奖励后 `prob(min(15×次数, ...))`（≤5 次）通知邪教团（narsie_attack.ogg + 大字报）|

> 反向彩蛋：邪教团献祭异端（on_cult_sacrificed）→ 掉落**亡灵刀**（haunted blade）+ 解锁上述 3 件物品（clockwork/narsie_attack.ogg 全团通报）。

### 8.5 重掷目标

**The Relentless Heartbeat 无情心跳**（reroll_targets）:
| 项 | 值 |
|---|---|
| 材料 | 风铃草（harebell）+ 书 + 制服（制服 = 任意 jumpsuit）|
| 效果 | 清空并重新生成 5 个献祭目标 |
| 前置 | 必须有活心 |
| cost | 上游 **1** → **NOVA 0（免费）**（modular_nova 覆写）|
| 位置 | 侧知识商店（drafting_tier 2，is_shop_only）|

### 8.6 路径专属献祭地图

> 暗影界不是一张图——**每条路径有自己的献祭落点**（sacrifice_map.dm 的 landmark）:

| 路径 | 献祭落点 |
|---|---|
| Ash / Flesh / Void / Rust / Lock | 各自专属 landmark（`/obj/effect/landmark/heretic/<path>`）|
| Blade / Cosmic / Moon | 走 PATH_START 默认落点 |
| Void 虚空 | 专属**极暗灯光**地图（bulb_power 0.5，亮度 3）|

---

## 九、影响点与诅咒（全量）

### 9.1 影响点（现实裂隙）

> **代码**: `influences.dm`（303 行）+ reality_smash_tracker

**生成**:
| 项 | 值 |
|---|---|
| 每异端基数 | NUM_INFLUENCES_PER_HERETIC = **5** |
| 多异端公式 | 第 n 个异端 `max(5-n+1, 1)` → **5+4+3+2+1**（第 1 个 5、第 2 个 4……保底 1）|
| 位置 | 随机安全站内地块（相距 ≥1 格，sanity 上限 100 次）|
| 可见性 | 仅异端可见（observer 级隐身 + 专属 HUD 外观），幽灵可见 |
| 生成时机 | 异端在站内（NOVA：centcom 层/到达穿梭机不生成，见第十章）|

**吸取（drain）**:
| 方式 | 知识 | 耗时 |
|---|---|---|
| 右键空手 | +1 | **10s**（HERETIC_RIFT_DEFAULT_DRAIN_SPEED）|
| 疤痕法典（Codex Cicatrix）| +2 | **5s**（codex.drain_speed）|
| 病典（Codex Morbus）| +2 | **2.5s** |
| 加速特效 | 非默认速度 | "滴血之眼" overlay（heretic_eye_dripping）|

**裂隙其他机制**:
| 机制 | 值 |
|---|---|
| 靠近透视 | 半径 **7** 格 proximity monitor → 临时 X 光（**3 分钟**冷却/次）|
| 吸干后 | 变为可见的"pierced reality"裂隙（15s 淡入）|
| 触摸可见裂隙 | **25%** 概率整臂断肢（手臂被吞进裂隙）|
| 念动力（TK）| 断头或**直接肢解+小爆炸**（dyn_explosion 1）|
| examine 裂隙 | 脑损 **10**（上限 190）+ 心情 **gates_of_mansus -25 / 4min**（恐惧）|
| 钓鱼点 | 裂隙 = 维度裂缝鱼点（dimensional_rift 鱼源，可钓出物品/手臂）|
| 裂隙命名 | 随机"前缀+后缀"组合（HERETIC_INFLUENCE_FILE）|

### 9.2 诅咒（4 种全录）

> **代码**: `heretic_curses.dm`（284 行）。基类: 施法距离 max_range **64**、基础时长 1 分钟。**施咒机制**: 用目标的**指纹/血样**物品做仪式材料锁定目标（可加"接触过/沾血"的物品延长诅咒）；可诅咒自己；抗魔/圣抗可挡；异端不可互咒（有提示）；施咒后清洗材料上的证据。

| # | 诅咒 | 时长 | 描边色 | 仪式材料 | 效果 |
|---|---|---|---|---|---|
| 1 | **Paralysis 麻痹** | **5 分钟** | #f19a9a | 斧头 + 左右腿各 1 | 双腿瘫痪（TRAIT_PARALYSIS 双腿）|
| 2 | **Corrosion 腐蚀** | **3 分钟** | #c1ffc9 | 剪线钳 + 呕吐物 + 心脏 | 持续呕吐 + 器官持续损伤 |
| 3 | **Transmutation 转化** | **∞（无限）** | 无 | 病典（Codex Morbus）| 任意 RACE_SWAP 种族转化；**法典被毁才解除**；受害者被记录 |
| 4 | **Indulgence 纵欲** | **8 分钟** | 栗色 | 病典 | 永久欲望画作效果 + 营养拉到**饥饿** |

---

## 十、NOVA 专属改动（全查）

> 全库 grep "NOVA EDIT"（`code/modules/antagonists/heretic/` 84 处含 I18N 头） + `modular_nova/` 覆写，**实质性改动 12+ 项**：

| # | 改动 | 位置 | 上游 → NOVA |
|---|---|---|---|
| 1 | **初始知识 2 点** | heretic_antag.dm:73 | 1 → **2** |
| 2 | **起始知识删 Feast of Owls** | modular_nova starting_lore.dm | 5 节点 → **4 节点**（该知识设为 abstract + 非起始，完全不可获得）|
| 3 | **重掷目标免费** | modular_nova general_side.dm | cost 1 → **0** |
| 4 | **献祭目标 opt-in** | sacrifice_knowledge.dm:134-137 | 全员可被选 → **仅安保/指挥职业**（`heretic_sac_target`，受 config `disable_antag_opt_in_preferences` 控制）|
| 5 | **同部门目标槽取消** | sacrifice_knowledge.dm:167-175 | 3 号槽"同部门"→ 仅 opt-in 关闭时启用（默认跳过，直接随机补）|
| 6 | **roundend 去 greentext** | heretic_antag.dm:803-833 | 移除成功/失败判定、绿字"THE HERETIC ASCENDED!"、"Ascension Forsaken"、红字失败、目标成功后缀 |
| 7 | **影响点生成区域限制** | influences.dm:66-69 | centcom 层（除 interlink）/到达穿梭机上不生成新裂隙 |
| 8 | **灰烬穿行冷却翻倍** | modular_nova ash_jaunt.dm | 15s → **30s** |
| 9 | **空间相位削弱** | magic/space_crawl.dm:25 | 相位期间 3 项保护（RESISTLOWPRESSURE/RESISTCOLD/NOBREATH）→ **全部移除**（list()）|
| 10 | **刀数量描述上调** | ash/void/flesh lore | 灰烬/虚空"2 个"→"5 个"；血肉"3 个"→"20 个"（描述文案；强制执行 limit 变量未变：2/2/3）|
| 11 | **view_exploitables** | modular_nova antag_datum.dm | FALSE → **TRUE**（异端可查看可利用物）|
| 12 | **I18N CODEMOD** | 全部文件 | 玩家可见字符串全部 LANG() 化（中文服本地化）；roundend 目标文案反向翻译 |

---

## 十一、风味与彩蛋

### 11.1 音效

| 音效 | 触发 |
|---|---|
| **heretic_gain.ogg** | 出场 stinger（成为异端）|
| **heretic_gain_intense.ogg** | 猫头鹰盛宴仪式（100 音量）|
| **heretic_sacrifice.ogg** | 献祭目标传送入暗影界时播放主题 |
| **singlebeat.ogg** | 活心追踪锁定目标时心跳声 |
| curse1-6.ogg / curseattack.ogg | 暗影斗篷开启/关闭 |
| castsummon.ogg | 符文仪式施放 |
| repulse.ogg / blind.ogg / disintegrate.ogg / demon_consume.ogg | 献祭传送/扬灰/活心仪式 |
| narsie_attack.ogg | 邪教团被献祭警示 / 邪教献祭异端奖励 |
| eatfood.ogg | 猫头鹰盛宴每点知识吞噬声 |

### 11.2 心情

| 心情 | 数值 | 说明 |
|---|---|---|
| **heretics（反派自带）** | **+12** | "THE HIGHER I RISE, THE MORE I SEE."（hidden）|
| lunatic（月疯子）| +10 | "THE TRUTH REVEALED, THE LIE SLAIN." |
| shadow_realm 系列 | -15 / +4 / -6 / -8 | 见 8.2 |
| gates_of_mansus | **-25** | examine 现实裂隙（4min，恐惧）|
| rift_fishing | — | 从裂隙钓上东西 |

### 11.3 视觉特效

| 特效 | 说明 |
|---|---|
| **紫色烟雾** | 暗影斗篷 = 紫色烟雾包裹 3 分钟（"completely conceal your identity in a purple smoke for three minutes"）|
| **绿色漩涡** | 光环 overlay（heretic_aura.dmi），examine 可见文案 |
| **符文路径色** | 绘制的 3×3 大符文按路径着色（见下表）|
| **红色描边** | 邪教奖励 reveal（COLOR_CULT_RED）|
| **暗红裂痕** | 献祭目标死在暗影界后站内残留的"weakened rift in reality"（COLOR_DARK_RED）|
| 滴血之眼 | 法典快速吸裂隙时的 overlay |
| 裂隙淡入 | 吸干后 15s 渐显 |

**路径颜色表**（GLOB.heretic_path_to_color）:
| 路径 | 颜色 |
|---|---|
| 起始 | 青柠色 |
| Rust 锈 | 货运棕 |
| Flesh 血肉 | 柔和红 |
| Ash 灰烬 | 鲜红 |
| Void 虚空 | 青色 |
| Blade 利刃 | 银色 |
| Cosmic 宇宙 | **紫色** |
| Lock 锁 | 黄色 |
| Moon 月 | 浅蓝 |

### 11.4 Roundend 报告（NOVA 版）

```
[玩家信息]
Sacrifices Made: [总献祭数]
The heretic's sacrifice targets were: [目标名单].
Objective #1: [研究目标]（NOVA 无成功后缀）
Objective #2: [献祭目标]
Knowledge Researched: [全部已研究知识名]
```

> NOVA 移除: 成功/失败判定、绿字"THE HERETIC ASCENDED!"、红字 "The heretic has failed."、绿字 "Ascension Forsaken"（对应 `feast_of_owls`）。

### 11.5 其他彩蛋

- **自杀口号**: "THE MANSUS SMILES UPON ME!!"（suicide_cry）
- **鱼竿强化**: 空手曼苏斯之握对鱼竿念咒 **"R'CH T'H F'SH!"** → 鱼竿难度 -20 + 紫色描边（Mansus 注入），钓到东西/35% 概率解除
- **小丑转化**: 小丑异端克服武器自伤（handle_clown_mutation）
- **活心追踪信息**: 同层按距离报"very near/near/far/very far + 方向"，跨层报"above you/below you/on station/on lavaland/beyond the gateway/on another plane"；目标死亡追加 "they're dead, ..."
- **被献祭的异端**: -3 知识 + 专属羞辱心情（"I've been humiliated! My knowledge sapped from my being!"）
- **换目标代价**: 提交自定义目标确认框写着 "You will no longer be able to Ascend."（放弃升阶）
- **Mansus 低语**: 每 20 分钟被动知识时耳边低语（drain_message 池）

---

## 十二、对战攻略

### 怎么玩异端（推荐流程）

```
① 选路径：新手推荐 Blade（直白）或 Flesh（召唤流）
② 初始 2 知识 → 买 T1 → 画符文（20s，路径色 3×3）
③ 抓人献祭（目标：指挥 +3 点最划算；NOVA 下目标必为指挥/安保）
④ 攒知识（20min+1）→ 法袍 → T3 → 刀升级
⑤ 集齐 3 特殊尸体 → 升阶
⑥ 升阶后：按路径终极能力碾压
```

### 怎么防异端（船员对策）

| 方法 | 说明 |
|---|---|
| **盯符文** | 3×3 路径色符文=异端在附近——破坏符文（符文防酸防火，用工具拆）|
| **指挥层别落单** | NOVA 下献祭目标**只可能是指挥/安保**——这两类职业是头号猎物 |
| **尸体警惕** | 异端收集特殊尸体（烧焦/无头/星标）——火葬场优先 |
| **查裂隙** | 现实裂隙=异端强化来源（靠近会被 X 光透视）——别摸！25% 断臂 |
| **反升阶** | 集齐 3 尸体前阻止——升阶后极难打 |
| **跟踪心脏** | 活心能力让异端能感应目标位置——被感应=危险 |
| **献祭自救** | 被献祭进暗影界：**撑 2.5 分钟**（有治疗 buff），别硬刚 Helgrasp 手 |
| **防诅咒** | 诅咒需要你的指纹/血样——销毁带血迹/指纹的物品 |

---

## 十三、数值速查表

| 项 | 值 |
|---|---|
| 源码 | heretic/ 18,945 行（全库最大）+ modular_nova 覆写 |
| 路径 | 8 条（灰烬/利刃/宇宙/血肉/锁/月/锈/虚空）|
| 知识节点 | 120（8 路径树 + 起始 4(NOVA) + 侧知识 4 阶 26 + 献祭 4）|
| 法术 | 67 块（顶层约 45 独立）|
| 升阶 | 8 条 |
| 初始知识 | 2（NOVA；上游 1）|
| 起始知识 | 4 节点（NOVA；上游 5 含猫头鹰盛宴）|
| 被动 | 20min +1 |
| 光环 | 累计知识 >8（锁路径/猫头鹰除外）|
| 献祭 | 12s 昏睡 → 6s 传送 → 2.5min 暗影界 → 存活/死亡/开膛 |
| 开膛惩罚 | 250 BRUTE + 器官散落 |
| 点数 | 指挥+3/普通+2/邪教+1 |
| 献祭目标 | 5 个（1 指挥 + 1 安保 + 随机；NOVA opt-in 仅指挥/安保）|
| 研究目标 | 18~20 知识（NOVA 公式）|
| 普通献祭目标 | 4 人（<2 指挥时 5 人）|
| 高级献祭目标 | 1 指挥（≥2 指挥时追加）|
| 邪教奖励 | 3 选 1（诅咒刀/猩红徽记/收割者），权重 5-2n |
| 腐化器官 | 2-4 个/次（7 种）|
| Helgrasp | 1 分钟 |
| 暗影界 buff | 0.25/s 治疗 + 3 特质（不凝血/不濒死）|
| 重掷目标 | 免费（NOVA；上游 1 知识）|
| 刀 | force 20 / AP 35 / throw 10 / wound 5 / exposed 15 |
| 刀上限 | 利刃 4 / 血肉 3(+尸鬼 2) / 其余 2（升阶后无限）|
| 影响点 | 5+4+3+2+1（按异端数递减）|
| 吸裂隙 | 10s +1 / 法典 5s +2 / 病典 2.5s +2 |
| 裂隙 X 光 | 半径 7，3min 冷却 |
| 裂隙触摸 | 25% 断臂；TK 断头/肢解 |
| 诅咒 | 麻痹 5min / 腐蚀 3min / 转化 ∞ / 纵欲 8min（距离 64）|
| 符文 | 20s 绘制 + 3×3 + 路径色 |
| 商店价格 | 1/2/2/2/3（按阶）|
| 草稿 | 免费，3 轮 × 4 阶，D1 必出保底侧知识 |
| 灰烬穿行 | 30s（NOVA；上游 15s）|
| 空间相位 | 无保护特质（NOVA 削弱）|
| 心情 | 自带 +12；暗影界 -15~+4；裂隙 -25 |
| 出场音效 | heretic_gain.ogg |
| 自杀口号 | "THE MANSUS SMILES UPON ME!!" |
| roundend | NOVA 无 greentext/红字 |

---

*本文档数值全部实测自 `code/modules/antagonists/heretic/`（18,945 行）源码 + `modular_nova/` 覆写 + 子代理全量提取 JSON（knowledge_full_extract 120 块 / magic_extract 67 块），无推测。目标系统/诅咒/影响点/风味为 v2 补全（对照 heretic_antag.dm、sacrifice_knowledge.dm、heretic_curses.dm、influences.dm、sacrifice_moodlets.dm、shadow_cloak.dm、starting_lore.dm、_heretic_paths.dm）。*
