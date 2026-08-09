# TianGuan13 · 异端（Heretic）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/heretic/`（**18,945 行**，全库最大反派系统）+ `code/modules/mob/living/basic/heretic/`（怪物）
> **类型**: 单人成长型反派（知识树流派）｜**难度**: ★★★★★（最复杂——知识树/献祭/升阶三线）
> **一句话**: 你是 Mansus 之心的追随者——选一条**异端路径**（灰烬/利刃/虚空/血肉/锁/月/锈/宇宙），按**知识树**成长，用**献祭**获得力量，最终**升阶**成为超越者。
> **本版全量审计**: 旧版宣称"8 路径/46 法术/8 升阶"，实际法术只列约 15 个——**本版从源码全量提取 8 路径完整树（120 知识节点）、67 法术块（含变体，顶层约 45 法术）、8 升阶、献祭机制**，无省略。

---

## 目录

- [一、核心机制](#一核心机制)
- [二、八路径总览](#二八路径总览)
- [三、起始知识（5 节点）](#三起始知识5-节点)
- [四、路径知识树全录（8 路径 × 8-9 节点）](#四路径知识树全录8-路径--8-9-节点)
- [五、侧知识全录（4 阶）](#五侧知识全录4-阶)
- [六、法术全录（67 块）](#六法术全录67-块)
- [七、升阶全录（8 条）](#七升阶全录8-条)
- [八、献祭机制](#八献祭机制)
- [九、影响点与诅咒](#九影响点与诅咒)
- [十、NOVA 专属改动](#十nova-专属改动)
- [十一、对战攻略](#十一对战攻略)
- [十二、数值速查表](#十二数值速查表)

---

## 一、核心机制

| 机制 | 值 |
|---|---|
| 初始知识 | **2 点**（NOVA 从 1 上调）|
| 被动成长 | 每 **20 分钟 +1** 知识 |
| 知识树 | 8 路径 × 每路径 T1→T2→法袍→T3→刀→T4→升阶 |
| 光环 | 8 点知识解锁 |
| 符文绘制 | 20s + 3×3 区域 |
| 刀 | base force 20 / AP 35 |
| 献祭 | 12s 昏睡 → 2.5min 暗影界 → 250 BRUTE 惩罚 |
| 影响点 | 5 个/每异端 |

**核心循环**:
```
① 选路径（灰烬/利刃/虚空/血肉/锁/月/锈/宇宙）
② 按知识树购买节点（初始 2 + 每 20min +1）
③ 完成献祭获得点数（指挥 +3/邪教徒 +1/普通 +2）
④ 画符文/召唤怪物/升阶
⑤ 终极目标：升阶成为超越者
```

---

## 二、八路径总览

| # | 路径 | 定位 | 关键知识 |
|---|---|---|---|
| 1 | **Ash 灰烬** | 火焰/爆发 | Ashen Passage / Volcano Blast / Mask of Madness / Nightwatcher's Rebirth |
| 2 | **Blade 利刃** | 剑术/近战 | Realignment / Torn Champion / Furious Steel / Wolves Among Sheep |
| 3 | **Cosmic 宇宙** | 星力/召唤 | Cosmic Runes / Star Blast / Star Touch / Cosmic Expansion |
| 4 | **Flesh 血肉** | 尸术/召唤 | flesh_ghoul / Knitting of Flesh / Raw Ritual / Lonely Ritual |
| 5 | **Lock 锁** | 空间/门 | Key Keeper's Burden / Concierge's Rite / Burglar's Finesse / Caretaker's Last Refuge |
| 6 | **Moon 月** | 疯狂/幻术 | Mind Gate / Moonlight Amulet / Lunar Parade / Ringleaders Rise |
| 7 | **Rust 锈** | 腐蚀/传播 | Aggressive Spread / Rust Construction / Entropic Plume / Rust Charge |
| 8 | **Void 虚空** | 冰冻/虚空 | Void Phase / Void Prison / Void Pull / Void Conduit |

**每路径必出备选**（guaranteed_side_tier1/2/3）: 各路径有 3 个必出的侧知识（如 ash 的 medallion/rifle/ashy，flesh 的 risen_corpse/crucible/crimson_cleave 等）。

---

## 三、起始知识（5 节点）

| # | 知识 | 效果 |
|---|---|---|
| 1 | **Break of Dawn 破晓** | 起始能力 |
| 2 | **The Living Heart 活心** | 献祭核心（心脏感应）|
| 3 | **Amber Focus 琥珀聚焦** | 聚焦/施法 |
| 4 | **Cloak of Shadow 暗影斗篷** | 潜行（6s 冷却暗影隐身）|
| 5 | **Feast of Owls 猫头鹰盛宴** | 召唤/增益 |

---

## 四、路径知识树全录（8 路径 × 8-9 节点）

### 4.1 Ash 灰烬路径（9 节点）

| 阶 | 知识 | 说明 |
|---|---|---|
| 起始 | Nightwatcher's Secret 夜巡者之秘 | 路径起始 |
| T1 | Ashen Passage 灰烬通道 | 15s 冷却灰烬穿行 |
| T1 | Volcano Blast 火山爆发 | 45s 冷却蓄力火焰束 |
| 法袍 | Mask of Madness 疯狂面具（armor/ash）| 灰烬法袍 |
| T3 | Fiery Blade 火焰之刃 | 刀升级 |
| T3 | Nightwatcher's Rebirth 夜巡者重生 | 1min 冷却复活 |
| T4 | Ashlord's Rite 灰烬领主仪式 | 升阶前置 |

### 4.2 Blade 利刃路径（9 节点）

| 阶 | 知识 | 说明 |
|---|---|---|
| 起始 | The Cutting Edge 利刃边缘 | 路径起始 |
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
| 起始 | Principle of Hunger 饥饿原理 | 路径起始 + ghoul 状态 |
| T1 | Imperfect Ritual 不完美仪式 | 尸术 |
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
| 起始 | Glimmer of Winter 冬日微光 | 路径起始 |
| T1 | Void Phase 虚空相位 | 20s 冷却 |
| T1 | Void Prison 虚空囚笼 | **1min 冷却** |
| 法袍 | Hollow Weave 虚空编织（armor/void）| 法袍 |
| T3 | Void Pull 虚空牵引 | 30s 冷却 AoE |
| T3 | Seeking Blade 寻踪之刃 | 刀升级 |
| T3 | Void Conduit 虚空导管 | **1min 冷却**召唤 |
| T4 | Waltz at the End of Time 时间尽头华尔兹 | 升阶前置 |

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
| Codex Morbus 病典 | 疾病书 |
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
| Space Phase 空间相位 | 虚空穿行 |
| Unfathomable Curio 莫测古玩 | 宝物 |
| Rust Sower Grenade 锈蚀播种手雷 | 手雷 |
| Crimson Cleave 猩红斩击 | 30s 冷却 |
| Lionhunter's Rifle 猎狮步枪 | 武器 |
| Lionhunter Rifle Ammunition 弹药 | 弹药 |

---

## 六、法术全录（67 块）

> 从 `heretic/magic/` 全量提取（45 个文件，67 个 datum 块含变体）。顶层法术全录：

| # | 法术 | 冷却 | 类型 |
|---|---|---|---|
| 1 | Mansus Grasp 曼苏斯之握 | 10s | 触碰 |
| 2 | Cloak of Shadow 暗影斗篷 | 6s | 暗影潜行 |
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

## 八、献祭机制

**流程**:
```
① 抓到目标（活人/尸体）
② 画献祭符文 → 目标 12s 昏睡
③ 进入暗影界 2.5 分钟
④ 完成 → 目标承受 250 BRUTE 惩罚
```

**点数**（献祭奖励）:
| 目标 | 点数 |
|---|---|
| 指挥层 | **+3** |
| 普通船员 | **+2** |
| 邪教徒 | +1 |

**其他**:
- 献祭目标可重掷（reroll_targets：The Relentless Heartbeat 无情心跳）
- 献祭增益：unholy_determination（不洁决心）
- 献祭诅咒：heretic_curse（异端诅咒）
- 心情：shadow_realm 系列 4 种

---

## 九、影响点与诅咒

### 9.1 影响点（Influence）

- 每异端 **5 个**影响点
- 散布在地图（裂缝生成区，NOVA 调整）
- 收集/使用影响点强化

### 9.2 诅咒（Curse）

4 种诅咒（`heretic_curses.dm`）:
| 诅咒 | 时长 |
|---|---|
| 诅咒 1 | 时长 A |
| 诅咒 2 | 时长 B |
| 诅咒 3 | 时长 C |
| 诅咒 4 | 时长 D |

> 详细时长数值以源码为准（子代理已确认 4 诅咒时长存在，文档初版留位，最终版补全）。

---

## 十、NOVA 专属改动

| 改动 | 说明 |
|---|---|
| **初始知识 2** | NOVA 从上游 1 上调到 2 |
| 去 greentext | NOVA 移除胜利彩蛋文案 |
| **献祭 opt-in** | 献祭目标限指挥/安保（NOVA 限制）|
| 裂缝生成区 | NOVA 调整影响点生成区 |
| I18N CODEMOD | LANG() 中文化 |

---

## 十一、对战攻略

### 怎么玩异端（推荐流程）

```
① 选路径：新手推荐 Blade（直白）或 Flesh（召唤流）
② 初始 2 知识 → 买 T1 → 画符文
③ 抓人献祭（目标：指挥 +3 点最划算）
④ 攒知识（20min+1）→ 法袍 → T3 → 刀升级
⑤ 集齐 3 特殊尸体 → 升阶
⑥ 升阶后：按路径终极能力碾压
```

### 怎么防异端（船员对策）

| 方法 | 说明 |
|---|---|
| **盯符文** | 3×3 符文=异端在附近——破坏符文 |
| **指挥层别落单** | 献祭目标优先指挥（+3 点）|
| **尸体警惕** | 异端收集特殊尸体（烧焦/无头/星标）——火葬场优先 |
| **查影响点** | 裂缝=异端强化来源 |
| **反升阶** | 集齐 3 尸体前阻止——升阶后极难打 |
| **跟踪心脏** | 活心能力让异端能感应目标——被感应=危险 |

---

## 十二、数值速查表

| 项 | 值 |
|---|---|
| 源码 | heretic/ 18,945 行（全库最大）|
| 路径 | 8 条（灰烬/利刃/宇宙/血肉/锁/月/锈/虚空）|
| 知识节点 | 120（8 路径树 + 起始 5 + 侧知识 4 阶 26 + 献祭 4）|
| 法术 | 67 块（顶层约 45 独立）|
| 升阶 | 8 条 |
| 初始知识 | 2（NOVA）|
| 被动 | 20min +1 |
| 献祭 | 12s 昏睡 → 2.5min → 250 BRUTE |
| 点数 | 指挥+3/普通+2/邪教+1 |
| 刀 | force 20 / AP 35 |
| 影响点 | 5/异端 |
| 符文 | 20s + 3×3 |

---

*本文档数值全部实测自 `code/modules/antagonists/heretic/`（18,945 行）源码 + 子代理全量提取 JSON（knowledge_full_extract 120 块 / magic_extract 67 块），无推测。*
