# TianGuan13 动态威胁等级百科 (Dynamic Threat Tier Encyclopedia)

> 基于 TianGuan13 NovaSector 分支源码全量整理。核心：`code/controllers/subsystem/dynamic/_dynamic_tier.dm`（316 行，5 个等级定义）+ `dynamic.dm`（选取/执行逻辑）+ `dynamic_config.dm`（config 加载）。
> **范围**：决定每局反派（antagonist）数量的动态威胁等级系统——绿星/黄星/红星/黑色轨道/午夜太阳 5 档，含权重/规则集配置/选取机制/人口修正/公告文案。

---

## 一、系统概述

**动态威胁等级（Dynamic Threat Tier）** 是每局开局时由 **SSdynamic**（动态反派子系统）随机选取的**威胁强度档位**——它直接决定当局会生成多少反派（叛徒/异端/巫师等），以及他们的强度。

- **源码**：`code/controllers/subsystem/dynamic/`（dynamic.dm + _dynamic_tier.dm + _dynamic_ruleset.dm 等）
- **等级图标**：管理层在开局**中央指挥公告**里收到"Advisory Level"（咨询等级）播报
- **5 个等级**：绿星 → 黄星 → 红星 → 黑色轨道 → 午夜太阳（威胁递增）

### 等级速览

| 等级 | 代号 | 权重 | 最低人口 | 反派规模 |
|---|---|---|---|---|
| **绿星 Green Star** | Greenshift | **2** | 0 | 无反派（完全和平局） |
| **黄星 Yellow Star** | Low Chaos | **8** | 0 | 少量（1-2 开局 + 轻中局） |
| **红星 Red Star** | Low-Medium Chaos | **46** ⭐ | 0 | 中等（1-2 开局 + 2 轻 + 1-2 晚入） |
| **黑色轨道 Black Orbit** | Medium-High Chaos | **36** | 0 | 较高（2-3 开局 + 1-2 轻 + 1-2 重 + 1-3 晚入） |
| **午夜太阳 Midnight Sun** | High Chaos | **10** | **25** | 最高（3-4 开局 + 2-4 重中局 + 2-3 晚入） |

> **默认权重合计 102**——最常抽到**红星**（46/102 ≈ 45%），其次黑色轨道（36/102 ≈ 35%）。午夜太阳需人口 ≥25。

---

## 二、等级详细参数

### 2.1 绿星 Greenshift（Green Star）

**代码**: `_dynamic_tier.dm:113`

| 参数 | 值 |
|---|---|
| tier | DYNAMIC_TIER_GREEN（0） |
| config_tag | Greenshift |
| 权重 | **2**（≈2%） |
| min_pop | 0 |

**规则集配置**：全部 0（ROUNDSTART/LIGHT_MIDROUND/HEAVY_MIDROUND/LATEJOIN 均为 0 个）——**完全无反派**。

**公告**："Advisory Level: **Green Star**。Your sector's advisory level is Green Star. Surveillance information shows no credible threats to Nanotrasen assets within the Spinward Sector at this time."

### 2.2 黄星 Yellow Star（Low Chaos）

**代码**: `_dynamic_tier.dm:124`

| 参数 | 值 |
|---|---|
| tier | DYNAMIC_TIER_LOW（1） |
| config_tag | Low Chaos |
| 权重 | **8**（≈8%） |
| min_pop | 0 |

**规则集配置**：
| 类型 | LOW-HIGH | 时间阈值 | 冷却 |
|---|---|---|---|
| ROUNDSTART | 1-1 | — | — |
| LIGHT_MIDROUND | 0-2 | 30 分钟 | 10-20 分钟 |
| HEAVY_MIDROUND | 0-1 | 60 分钟 | 10-20 分钟 |
| LATEJOIN | 0-1 | 5 分钟 | 10-20 分钟 |

**公告**："Advisory Level: **Yellow Star**。Surveillance shows a credible risk of enemy attack against our assets in the Spinward Sector."

### 2.3 红星 Red Star（Low-Medium Chaos）★ 最常抽中

**代码**: `_dynamic_tier.dm:171`

| 参数 | 值 |
|---|---|
| tier | DYNAMIC_TIER_LOWMEDIUM（2） |
| config_tag | Low-Medium Chaos |
| 权重 | **46**（≈45%）⭐ |
| min_pop | 0 |

**规则集配置**：
| 类型 | LOW-HIGH | 时间阈值 | 冷却 |
|---|---|---|---|
| ROUNDSTART | **1-2** | — | — |
| LIGHT_MIDROUND | 0-2 | 30 分钟 | 10-20 分钟 |
| HEAVY_MIDROUND | 0-1 | 60 分钟 | 10-20 分钟 |
| LATEJOIN | **1-2** | 5 分钟 | 10-20 分钟 |

**公告**："Advisory Level: **Red Star**。The Department of Intelligence has decrypted Cybersun communications suggesting a high likelihood of attacks on Nanotrasen assets."

### 2.4 黑色轨道 Black Orbit（Medium-High Chaos）

**代码**: `_dynamic_tier.dm:219`

| 参数 | 值 |
|---|---|
| tier | DYNAMIC_TIER_MEDIUMHIGH（3） |
| config_tag | Medium-High Chaos |
| 权重 | **36**（≈35%） |
| min_pop | 0 |

**规则集配置**：
| 类型 | LOW-HIGH | 时间阈值 | 冷却 |
|---|---|---|---|
| ROUNDSTART | **2-3** | — | — |
| LIGHT_MIDROUND | 1-2 | 30 分钟 | 10-20 分钟 |
| HEAVY_MIDROUND | **1-2** | 60 分钟 | 10-20 分钟 |
| LATEJOIN | **1-3** | 5 分钟 | 10-20 分钟 |

**公告**："Advisory Level: **Black Orbit**。Your sector's local communications network is currently undergoing a blackout... information passed to us by GDI suggests a high amount of enemy activity."

### 2.5 午夜太阳 Midnight Sun（High Chaos）

**代码**: `_dynamic_tier.dm:268`

| 参数 | 值 |
|---|---|
| tier | DYNAMIC_TIER_HIGH（4） |
| config_tag | High Chaos |
| 权重 | **10**（≈10%） |
| **min_pop** | **25**（人口不足 25 不抽） |

**规则集配置**：
| 类型 | LOW-HIGH | 时间阈值 | 冷却 |
|---|---|---|---|
| ROUNDSTART | **3-4** | — | — |
| LIGHT_MIDROUND | 1-2 | **20 分钟** | 10-20 分钟 |
| HEAVY_MIDROUND | **2-4** | **30 分钟** | 10-20 分钟 |
| LATEJOIN | **2-3** | 5 分钟 | 10-20 分钟 |

**公告**："Advisory Level: **Midnight Sun**。Credible information passed to us by GDI suggests that the Syndicate is preparing to mount a major concerted offensive on Nanotrasen assets... to cripple our foothold there."

---

## 三、选取机制（SSdynamic）

### 3.1 流程

```
开局 → SSdynamic.select_roundstart_antagonists()
 → load_config()（加载 dynamic_config）
 → pick_tier()：遍历 5 个等级，过滤 min_pop/weight≤0，按 weight 加权随机抽一个
 → set_tier()：确定规则集数量
 → 按规则集配置 spawn 反派
```

**代码**: `dynamic.dm`（pick_tier / set_tier / select_roundstart_antagonists）

### 3.2 人口修正（set_tier）

抽取后按**实际人口**修正规则集上限：

| 条件 | 效果 |
|---|---|
| 人口 ≤ HALF_RANGE_POP_THRESHOLD（25） | high_end = max(low_end, ⌈high_end × 0.25⌉)（四分之一） |
| 人口 ≤ FULL_RANGE_POP_THRESHOLD（40） | high_end = max(low_end, ⌈high_end × 0.5⌉)（减半） |
| 人口 > 40 | 完整范围 |

> 例：红星（roundstart 1-2）人口 30 → high_end 减半 → 1-1（实际 1 个开局反派）。

### 3.3 规则集类型

| 类型 | 含义 | 时机 |
|---|---|---|
| ROUNDSTART | 开局反派 | 开局直接生成 |
| LIGHT_MIDROUND | 轻中局 | 30 分钟后 |
| HEAVY_MIDROUND | 重中局 | 60 分钟后 |
| LATEJOIN | 晚入反派 | 5 分钟后可加入 |

---

## 四、规则集权重与反派数量（每级刷多少反派）

> **核心机制**：每个反派规则集（Traitors/Malf AI/Wizard/...）有自己的**等级权重表**（`weight`，可按 tier 分级）和**人数上限**（`max_antag_cap`）。这就是"每局刷多少反派取决于威胁等级"的真相——**高威胁规则集在低等级权重为 0，根本不出现**。

### 4.1 开局规则集全表（14 个，源码全录）

**代码**: `code/controllers/subsystem/dynamic/dynamic_ruleset_roundstart.dm`

| 规则集 | 黄星T1 | 红星T2 | 黑轨T3 | 午夜T4 | min_pop | 人数上限 |
|---|---|---|---|---|---|---|
| **叛徒 Traitors** | 10 | 10 | 10 | 10 | 3 | **玩家/24** |
| **血兄弟 Blood Brothers** | 5 | 5 | 5 | 5 | 10 | 玩家/29 |
| **变形怪 Changelings** | 3 | 3 | 3 | 3 | 15 | 玩家/29 |
| **异端 Heretics** | 3 | 3 | 3 | 3 | 30 | 玩家/24 |
| **血虫 Blood Worms** | 1 | 1 | 1 | 1 | 10 | 玩家/29 |
| **Malf AI** | **0** | 1 | 3 | 3 | 30 | **1** |
| **巫师 Wizard** | **0** | **0** | 1 | 2 | 30 | **1** |
| **血 cult Blood Cult** | **0** | 1 | 3 | 3 | 30 | — |
| **核弹队 Nukies** | **0** | 1 | 3 | 3 | 30 | — |
| **革命 Revolution** | **0** | 1 | 3 | 3 | 30 | **3** |
| **间谍 Spies** | **0** | 1 | 3 | 3 | 10 | — |
| 延长 Extended | 0 | 0 | 0 | 0 | — | — |
| 流星 Meteor | 0 | 0 | 0 | 0 | — | — |
| 国境 Nations | 0 | 0 | 0 | 0 | — | — |

### 4.2 两类规则集

**① 全等级通用**（固定权重，任何威胁等级都可能出现）：
- 叛徒（10）/ 血兄弟（5）/ 变形怪（3）/ 异端（3）/ 血虫（1）
- **低威胁局的主力反派**——黄星/红星时主要刷这些

**② 高等级专属**（权重按 tier 分级，低等级为 0）：
- **Malf AI**：黄星 0、红星 1、黑轨 3、午夜 3（需 30 人口）
- **巫师**：黄星 0、红星 0、黑轨 1、午夜 2——**只有黑轨/午夜才出巫师**
- **血 cult/核弹队/革命/间谍**：黄星 0、红星 1、黑轨 3、午夜 3
- **午夜太阳专属**：巫师权重升到 2（最高单人反派权重）

### 4.3 反派人数上限（max_antag_cap）

| 类型 | 计算 | 例（30 人服） |
|---|---|---|
| `玩家数/24` | 叛徒/异端 | 30/24 ≈ **1 个** |
| `玩家数/29` | 血兄弟/变形怪/血虫 | 30/29 ≈ **1 个** |
| 固定 1 | Malf AI/巫师 | 1 个 |
| 固定 3 | 革命 | 3 个头目 |

> **注意**：这是**每个规则集**的上限——叛徒上限 1 不代表全服只有 1 个反派，因为可能同时刷叛徒+血虫+变形怪等多个规则集。

### 4.4 权重变化机制（pick_roundstart_rulesets）

- 每个规则集有**独立权重**，按 tier 查表（`get_tier_specific_value`）
- **solo 规则集**（巫师/核弹队等）：抽中后**取消其他所有选择**——一整局就是它
- **不可重复**（repeatable=FALSE）：抽中后从池中移除
- 可重复规则集每次抽中**权重 -2**（repeatable_weight_decrease）
- **RULESET_HIGH_IMPACT**（高冲击规则集）在非最高等级时从池中剔除

---

## 五、config 覆盖（dynamic_config）

所有等级参数可通过 **dynamic_config**（`config/dynamic.json` 或动态配置系统）覆盖：

- `weight`：抽选权重
- `min_pop`：最低人口
- `ruleset_type_settings`：各类型 LOW_END/HIGH_END/时间阈值/冷却
- `advisory_report`：公告文案

**代码**: `_dynamic_tier.dm:82-104`（set_config_value 解析，TIME_THRESHOLD 等按分钟换算）

---

## 六、实战解读

| 开局公告 | 含义 | 玩家应对 |
|---|---|---|
| **Green Star 绿星** | 无反派局 | 安心发展，但可能有自然威胁（超物质/虫子） |
| **Yellow Star 黄星** | 少量反派 | 常规警惕，1 个左右开局叛徒 |
| **Red Star 红星** | 中等反派 | 最常遇到的局——安保保持戒备 |
| **Black Orbit 黑色轨道** | 高威胁 | 通讯黑幕叙事，多反派+晚入频繁，安保高警戒 |
| **Midnight Sun 午夜太阳** | 最高威胁 | 辛迪加总攻叙事，开局 3-4 反派+重中局，全员备战 |

> **提示**：午夜太阳需要 **25+ 人口**才会出现——低人口服几乎见不到；红星+黑色轨道占 **80%** 的局，是最常见的两种威胁等级。

---

## 附录 · 代码路径索引

| 文件 | 行数 | 内容 |
|---|---|---|
| `code/controllers/subsystem/dynamic/_dynamic_tier.dm` | 316 | 5 个等级定义（权重/规则集/公告） |
| `code/controllers/subsystem/dynamic/dynamic.dm` | — | 选取/执行/人口修正 |
| `code/controllers/subsystem/dynamic/dynamic_config.dm` | — | config 加载 |
| `code/controllers/subsystem/dynamic/_dynamic_ruleset.dm` | — | 规则集基类 |
| `code/controllers/subsystem/dynamic/dynamic_ruleset_roundstart.dm` | — | 开局规则集 |
| `code/controllers/subsystem/dynamic/dynamic_ruleset_midround.dm` | — | 中局规则集 |
| `code/controllers/subsystem/dynamic/dynamic_ruleset_latejoin.dm` | — | 晚入规则集 |
| `code/controllers/subsystem/dynamic/__dynamic_defines.dm` | — | ROUNDSTART/LIGHT_MIDROUND 等常量 |
| `code/controllers/subsystem/dynamic/dynamic_admin.dm` | — | 管理员干预 |

---

> **索引**：本页共 1 篇，覆盖动态威胁等级系统全量（5 等级/权重/规则集/人口修正/config/公告）。
