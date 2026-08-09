# TianGuan13 · 革命（Revolution）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/revolution/`（3 文件 **787 行**）
> **类型**: 团队型转化反派（人头压制流）｜**难度**: ★★★（闪光转化，人海战术）
> **一句话**: 你是革命军——用**闪光（flash）转化**船员加入革命，目标是**推翻指挥层**（干掉/驱逐所有头目）。人头越多越强，但**心灵护盾是克星**。

---

## 目录

- [TianGuan13 · 革命（Revolution）反派百科](#tianguan13--革命revolution反派百科)
  - [目录](#目录)
  - [一、核心概念](#一核心概念)
  - [二、革命军（Revolutionary）](#二革命军revolutionary)
  - [三、革命头目（Head Revolutionary）](#三革命头目head-revolutionary)
  - [四、转化机制（闪光）](#四转化机制闪光)
  - [五、目标系统](#五目标系统)
    - [5.1 叛乱目标（objective/mutiny）](#51-叛乱目标objectivemutiny)
    - [5.2 革命处理者（revolution\_handler）](#52-革命处理者revolution_handler)
  - [六、国敌（Enemy of the State）变体](#六国敌enemy-of-the-state变体)
  - [七、对战攻略](#七对战攻略)
    - [怎么玩革命（推荐流程）](#怎么玩革命推荐流程)
    - [怎么防革命（船员对策）](#怎么防革命船员对策)
  - [八、数值速查表](#八数值速查表)

---

## 一、核心概念

**革命**（`/datum/antagonist/rev`）是经典"暴民革命"反派：用**闪光**把普通船员转化为革命者，滚雪球壮大队伍，目标是**推翻指挥层**。

**核心设计**：
- **闪光转化**：头目用闪光（flash）洗脑目标 → 加入革命
- **团队制**：所有革命者同属革命团队（revolution）
- **头目升级**：普通革命者积累后可晋升为头目（promote）
- **目标动态更新**：新指挥层出现自动追加目标（update_objectives）
- **克星**：**心灵护盾植入体**（mindshield）——免疫转化，且转化失败会暴露

**基础属性**:
| 项 | 值 |
|---|---|
| HUD | `rev`（革命 HUD）|
| 界面 | AntagInfoRevolution |
| 转化手段 | 闪光（flash）|
| 头目 | max_headrev（上限）|
| 防转化 | 心灵护盾（on_mindshield 触发）|

---

## 二、革命军（Revolutionary）

**革命军**（`/datum/antagonist/rev`）:
- 被闪光转化的普通船员
- 获得革命 HUD（识别队友）
- 加入革命团队（create_team）
- 装备：`equip_rev`（分发革命装备）

**转化注意**：
- `can_be_owned`：部分目标不可转化（安保头目/已有反派等）
- 转化后忠诚不可逆（除非心灵护盾）

---

## 三、革命头目（Head Revolutionary）

**头目**（`/datum/antagonist/rev/head`）:
- 初始革命者为头目（出生自带）
- 普通革命者积累后可**晋升头目**（promote，`/datum/antagonist/rev/head`）
- 上限：max_headrev（防止无限头目）
- 头目才能转化新成员（闪光转化）
- 头目专属暗号（code phrases/responses——识别队友）

**头目职责**：
- 转化新成员（用闪光）
- 组织攻击指挥层
- 目标管理（mutiny 目标）

---

## 四、转化机制（闪光）

```
① 头目拿到闪光（flash——普通手电式闪光器）
② 对目标使用 → 判定转化
③ 成功：目标成为革命军（获得 HUD + 加入团队）
④ 失败：目标带心灵护盾 → 闪光无效 + 暴露身份
```

**转化限制**：
- 目标必须有大脑（碳基人类）
- 目标不能已是革命者/反派
- **心灵护盾**免疫转化（on_mindshield 信号）

---

## 五、目标系统

### 5.1 叛乱目标（objective/mutiny）

- 初始：`start_revolution` 为所有革命者添加"推翻指挥层"目标
- **动态更新**：新指挥层成员出生（`update_objectives` 信号）→ 自动追加目标
- 判定：所有指挥层成员被干掉/驱逐/失去权力

### 5.2 革命处理者（revolution_handler）

- 全局处理器管理革命状态
- `start_revolution`：初始化
- `process`：每 tick 检查
- `declare_revs_win`：宣布革命胜利
- `cleanup`：清理

---

## 六、国敌（Enemy of the State）变体

**国敌**（`/datum/antagonist/enemy_of_the_state`）——革命相关变体，目标二选一：

| 目标 | 说明 |
|---|---|
| **流放 Exile** | 成为流亡者存活（Minor Victory 小胜利）|
| **劫持 Hijack** | 劫持穿梭机（Major Victory 大胜利——"chose the badass option"）|

**回合报告分级**：
- 大胜利（Major Victory）：劫持穿梭机（badass 选项）
- 小胜利（Minor Victory）：作为流亡者存活
- 失败（Failed）：死亡/被抓获

---

## 七、对战攻略

### 怎么玩革命（推荐流程）

```
① 出生头目 → 拿闪光器 → 找落单船员
② 闪光转化 → 壮大队伍（目标：人数碾压）
③ 头目带小队 → 逐个处理指挥层目标
④ 目标完成：所有指挥层下台 → 宣布胜利
（变体国敌：选流亡苟活 或 劫持穿梭机硬刚）
```

### 怎么防革命（船员对策）

| 方法 | 说明 |
|---|---|
| **心灵护盾植入体** | 最有效——免疫转化+暴露革命者 |
| **指挥层抱团** | 别落单——革命专抓指挥层 |
| **闪光器警觉** | 有人拿闪光器靠近=革命头目 |
| **快速响应** | 革命滚雪球极快——早期镇压比后期容易 |
| **安保联动** | 发现革命者立即全站通告 |

---

## 八、数值速查表

| 项 | 值 |
|---|---|
| 源码 | 3 文件 787 行 |
| 转化 | 闪光器（头目专属）|
| 团队 | revolution |
| 目标 | 推翻指挥层（动态更新）|
| 头目 | 可晋升，max_headrev 上限 |
| 克星 | 心灵护盾 |
| 变体 | 国敌（流亡/劫持二选一）|
| 大胜利 | 劫持穿梭机 |
| 小胜利 | 流亡存活 |

---

*本文档数值全部实测自 `code/modules/antagonists/revolution/`（3 文件 787 行）源码，无推测。*
