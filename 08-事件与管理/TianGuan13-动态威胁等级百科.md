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

## 四、config 覆盖（dynamic_config）

所有等级参数可通过 **dynamic_config**（`config/dynamic.json` 或动态配置系统）覆盖：

- `weight`：抽选权重
- `min_pop`：最低人口
- `ruleset_type_settings`：各类型 LOW_END/HIGH_END/时间阈值/冷却
- `advisory_report`：公告文案

**代码**: `_dynamic_tier.dm:82-104`（set_config_value 解析，TIME_THRESHOLD 等按分钟换算）

---

## 五、实战解读

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
