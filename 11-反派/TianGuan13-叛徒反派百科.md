# TianGuan13 · 叛徒（Traitor）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/traitor/`（7 文件 1,100 行）+ `code/modules/uplink/`（20 文件 3,746 行）
> **类型**: 单人潜伏型反派（最经典）｜**难度**: ★★★（目标+Uplink 搭配决定强度）
> **一句话**: 你是辛迪加叛徒——用 **TC 电信水晶**买 Uplink 装备（见 [Uplink 系统百科](TianGuan13-上行链路Uplink系统百科.md)），完成**叛徒目标**（刺杀/偷窃/破坏/终局 3 选）。
> **本版全量审计**: 旧版目标系统仅一句"任务目标+终局目标"——**本版从源码全量提取目标生成机制（概率/类型/终局判定）**，并已将共用 Uplink 部分拆出为独立篇。

---

## 目录

- [一、核心机制](#一核心机制)
- [二、叛徒目标系统（全量）](#二叛徒目标系统全量)
- [三、目标生成流程](#三目标生成流程)
- [四、目标类型全录](#四目标类型全录)
- [五、终局目标详解](#五终局目标详解)
- [六、承包商契约](#六承包商契约)
- [七、暗号与风味](#七暗号与风味)
- [八、对战攻略](#八对战攻略)
- [九、数值速查表](#九数值速查表)

> **Uplink 系统**（TC 货币/395 件物品全录/购买规则）已独立成篇：[上行链路 Uplink 系统百科](TianGuan13-上行链路Uplink系统百科.md)

---

## 一、核心机制

| 机制 | 值 |
|---|---|
| 目标 | 叛徒目标（职业/通用）+ **终局目标** |
| 终局目标 | **劫机**（hijack）/ **殉道**（martyr）/ **逃脱**（escape）|
| 雇主 | 75% 辛迪加 / 25% Nanotrasen（flavor 系统）|
| Uplink | 授予（UPLINK_TRAITORS 标志，见 Uplink 百科）|
| 折扣销售 | 每局 4-6 件折扣物品 |
| 进度系统 | 声誉（progression）解锁高级物品 |
| 自杀口号 | "FOR THE SYNDICATE!!" |
| 暗号 | 辛迪加暗号（蓝/红识别）|

**NOVA EDIT**：`can_assign_self_objectives = FALSE`（原版 TRUE，NOVA 收紧——叛徒不能自己换目标，需管理员批准）

---

## 二、叛徒目标系统（全量）

**代码**: `datum_traitor.dm`（354 行）forge_traitor_objectives / forge_ending_objective

### 2.1 目标数量

| 项 | 值 |
|---|---|
| 目标上限 | `traitor_objectives_amount`（默认 **2**，min 0）|
| 劫机追加 | 满足条件时劫机目标占 1 个名额 |
| 职业目标 | 1 个名额可被职业目标占用（40% 概率）|

### 2.2 目标生成概率

| 概率 | 值 | 说明 |
|---|---|---|
| **JOB_PROB** | **40%** | 生成职业目标（assigned_role.generate_traitor_objective）|
| **KILL_PROB** | **50%** | 生成杀人系目标（刺杀/流放/摧毁 AI）|
| **DESTROY_AI_PROB** | 100/人数 | 有活跃 AI 时摧毁 AI 概率 |
| **MAROON_PROB** | 30% | 流放目标（在杀人系内）|
| **HIJACK_PROB** | **10%** | 劫机目标（玩家≥30 时）|
| **MARTYR_PROB** | **0%** | 殉道目标（NOVA 改为 0，原版 20%）|

> **NOVA 关键改动**：`MARTYR_PROB 0`（原版 20）——NOVA 下叛徒不会获得殉道终局。

### 2.3 生成流程

```
forge_traitor_objectives:
① 玩家数 ≥30 且 10% 概率 → 劫机者（is_hijacker=TRUE，占 1 名额）
② objective_limit = 目标上限（默认 2）
③ 先尝试职业目标（JOB_PROB 40%）
④ 循环生成通用目标直到满上限：
   - 40% 职业目标（若有）
   - 50% 杀人系：→ 有 AI 时 100/人数 摧毁 AI
              → 否则 30% 流放
              → 否则刺杀
   - 否则：偷窃目标
forge_ending_objective:
⑤ 劫机者 → 劫机终局
⑥ 否则：目标全部 martyr_compatible 且 MARTYR_PROB（NOVA=0）→ 殉道
⑦ 否则 → 逃脱终局
```

---

## 三、目标生成流程（逐步）

### 3.1 劫机判定（HIJACK）

| 条件 | 值 |
|---|---|
| 玩家人数 | ≥ **30**（HIJACK_MIN_PLAYERS）|
| 概率 | **10%**（HIJACK_PROB）|

满足 → `is_hijacker = TRUE`，终局 = 劫机

### 3.2 通用目标生成（forge_single_generic_objective）

| 分支 | 概率 | 目标 |
|---|---|---|
| 职业目标 | 40%（JOB_PROB）| 职业专属目标（若存在）|
| 杀人系 | 50%（KILL_PROB）| 见 3.3 |
| 其他 | 50% | **偷窃**（steal）|

### 3.3 杀人系分支（KILL_PROB 内）

| 分支 | 概率 | 目标 |
|---|---|---|
| 摧毁 AI | 100/人数（有活跃 AI 时）| **摧毁**（destroy）|
| 流放 | 30%（MAROON_PROB）| **流放**（maroon）|
| 默认 | — | **刺杀**（assassinate）|

### 3.4 终局生成（forge_ending_objective）

| 条件 | 终局 |
|---|---|
| is_hijacker | **劫机**（hijack）|
| 全部目标 martyr_compatible + MARTYR_PROB（NOVA 0%）| **殉道**（martyr）|
| 默认 | **逃脱**（escape）|

---

## 四、目标类型全录

> 目标类型基于 `/datum/objective` 体系（`code/game/gamemodes/objective.dm`）

| # | 目标 | 说明 |
|---|---|---|
| 1 | **Assassinate 刺杀** | 杀掉指定目标（经典叛徒目标）|
| 2 | **Steal 偷窃** | 偷指定物品（objective_items）|
| 3 | **Maroon 流放** | 把目标流放到无法返回的地方（如小行星）|
| 4 | **Destroy 摧毁** | 摧毁 AI 核心（AI 目标）|
| 5 | **Job 职业目标** | 按职业生成的专属目标（assigned_role.generate_traitor_objective）|
| 6 | **Hijack 劫机**（终局）| 独自劫持穿梭机逃离 |
| 7 | **Martyr 殉道**（终局）| 带着目标一起死（NOVA 关闭）|
| 8 | **Escape 逃脱**（终局）| 活着逃离空间站 |

**目标特性**:
| 特性 | 说明 |
|---|---|
| martyr_compatible | 目标是否与殉道兼容（影响殉道终局）|
| find_target | 自动选目标（dupe_search_range 去重）|
| give_special_equipment | 部分目标给特殊装备 |

---

## 五、终局目标详解

### 5.1 劫机 Hijack

| 项 | 值 |
|---|---|
| 条件 | 玩家≥30 + 10% 概率 |
| 判定 | 独自/小团队劫持穿梭机（emergency shuttle）|
| 玩法 | 抢先到穿梭机 → 挡其他乘客 → 起飞 |

### 5.2 殉道 Martyr（NOVA 关闭）

| 项 | 值 |
|---|---|
| 概率 | **0%**（NOVA；原版 20%）|
| 判定 | 目标全部 martyr_compatible 时触发 |
| 玩法 | 带着任务目标同归于尽 |

### 5.3 逃脱 Escape

| 项 | 值 |
|---|---|
| 概率 | 默认（非劫机非殉道）|
| 判定 | 活着逃离空间站（穿梭机/逃生舱）|

---

## 六、承包商契约

**代码**: `contractor/`（5 文件 608 行）

| 机制 | 值 |
|---|---|
| 合同 | 目标+赎金+投放点（提取舱回收）|
| 赎金 | **7,500-15,000 信用**（NOVA 从 1,800-4,500 上调）|
| 分成 | 承包商 35%（NOVA）|
| 奖金 | 大单 +9-13 TC / 中单 +6-8 / 小单 +2-4 |

---

## 七、暗号与风味

| 机制 | 说明 |
|---|---|
| 辛迪加暗号 | syndicate_code_phrase（蓝）/ response（红）|
| 雇主系统 | 75% 辛迪加 / 25% Nanotrasen（flavor）|
| 伪装 | 部分角色可伪装成其他阵营 |
| 自杀口号 | "FOR THE SYNDICATE!!" |
| 终局音效 | final_objective.ogg（提交目标时播放）|

---

## 八、对战攻略

### 怎么玩叛徒（推荐流程）

```
① 确认目标（职业/通用 + 终局）+ Uplink 位置（偏好设定）
② 起始 TC 买核心装备（武器/潜行/逃生）
③ 完成任务目标（刺杀/偷窃/破坏）
④ 用声誉系统解锁高级物品（progression）
⑤ 终局：劫机 / 逃脱（NOVA 下无殉道）
```

### 怎么防叛徒（船员对策）

| 方法 | 说明 |
|---|---|
| **搜 Uplink** | 叛徒的 PDA/笔/收音机可能是 Uplink——检查可疑物品 |
| **盯贵重物** | 叛徒目标常是磁盘/武器库/引擎 |
| **安保巡逻** | 叛徒单人——保持警惕+结伴 |
| **反潜行** | 变色套件/特工 ID 是常见潜行手段——查监控 |
| **保护目标** | 被刺杀的指定目标应被重点保护 |

---

## 九、数值速查表

| 项 | 值 |
|---|---|
| 源码 | traitor/ 7 文件 1,100 行 + uplink/ 3,746 行 |
| 目标上限 | 2（traitor_objectives_amount）|
| JOB_PROB | 40% |
| KILL_PROB | 50% |
| MAROON_PROB | 30% |
| DESTROY_AI_PROB | 100/人数 |
| HIJACK_PROB | 10%（玩家≥30）|
| MARTYR_PROB | **0%**（NOVA，原版 20）|
| 终局 | 劫机/殉道(NOVA 关)/逃脱 |
| 目标类型 | 刺杀/偷窃/流放/摧毁/职业/劫机/殉道/逃脱 |
| 承包商赎金 | 7,500-15,000（NOVA）|
| 承包商分成 | 35% |
| Uplink | 见 [Uplink 系统百科](TianGuan13-上行链路Uplink系统百科.md)（395 件）|

---

*本文档数值全部实测自 `code/modules/antagonists/traitor/datum_traitor.dm`（354 行）+ `code/__DEFINES/antagonists.dm` 源码，无推测。*
