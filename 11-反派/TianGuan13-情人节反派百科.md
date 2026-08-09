# TianGuan13 · 情人节（Valentine）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/valentines/`（2 文件 **110 行**：valentine 89 + heartbreaker 21）
> **类型**: 节日型配对反派（情人节活动）｜**难度**: ★★（互相守护）
> **一句话**: 情人节到了——你被**配对**给一个"约会对象"（date），目标：**互相保护对方**（AI/机械体版有零号法律）。另有**心碎者**（Heartbreaker）逆势而行。

---

## 目录

- [一、核心机制](#一核心机制)
- [二、目标系统](#二目标系统)
- [三、核心属性](#三核心属性)
- [四、AI/机械体变体](#四ai机械体变体)
- [五、电灯泡（Third Wheel）变体](#五电灯泡third-wheel变体)
- [六、心碎者（Heartbreaker）](#六心碎者heartbreaker)
- [七、对战攻略](#七对战攻略)
- [八、数值速查表](#八数值速查表)

---

## 一、核心机制

**情人节**（`/datum/antagonist/valentine`）是节日型反派：情人节活动时玩家被**随机配对**（date），每对互相保护。不是"真反派"（ANTAG_FAKE）——更多是浪漫活动机制。

**核心设计**：
- **配对机制**：每对情人节互相是对方的 date
- **互相保护**：目标 = 保护对方（protect/valentine）
- **恋爱状态**：in_love 状态效果（apply_status_effect）
- **AI 变体**：AI/机械体获得零号法律"保护你的约会对象"
- **假反派**：ANTAG_FAKE——不触发反派相关交互

**核心循环**:
```
① 情人节活动触发 → 玩家被配对（date）
② 获得保护目标：Protect [date], your date.
③ 互相守护（in_love 状态）
④ 两人都活到最后 = 成功约会
```

---

## 二、目标系统

**代码**: `valentine.dm` L13-17（forge_objectives）

| 项 | 值 |
|---|---|
| 目标 | **protect/valentine**——"Protect [date], your date."（保护你的约会对象）|
| 生成 | on_gain 时 forge（1 目标）|
| 判定 | 双方 check_completion（存活=完成）|
| admin_grantable | FALSE（管理员不可授）|
| human_check | FALSE（非人类也可）|

---

## 三、核心属性

| 属性 | 值 |
|---|---|
| roundend_category | "valentines" |
| show_in_antagpanel | FALSE（隐藏）|
| **自杀口号** | **"FOR MY LOVE!!"**（为了我的爱！）|
| ui_name | null（无 UI）|
| antag_flags | **ANTAG_FAKE** \| ANTAG_SKIP_GLOBAL_LIST（假反派）|
| date | 约会对象的 mind（VAR_FINAL）|
| 状态 | in_love（恋爱状态效果）|

---

## 四、AI/机械体变体

### 4.1 AI 版

| 项 | 值 |
|---|---|
| 零号法律 | "Protect your date, [date]. All other laws still apply in situations not pertaining to your date." |
| 翼神 | "Be a good wingman for your master AI. Assist them in protecting their date, [date]." |

### 4.2 机械体版

| 项 | 值 |
|---|---|
| 断开 AI | set_connected_ai(null) + lawupdate=FALSE |
| 零号法律 | "Protect your date, [date]. All other laws still apply in situations not relating to your date." |

---

## 五、电灯泡（Third Wheel）变体

**代码**: `valentine.dm` L72-82

| 项 | 值 |
|---|---|
| 名称 | "Third Wheel"（电灯泡）|
| 机制 | 第三者在配对中——也要保护 date |
| 报告 | 绿字 "was a third wheel, but protected [date]!" |
| 失败 | 红字 "was a third wheel, but failed to protect [date]!" |

---

## 六、心碎者（Heartbreaker）

**代码**: `heartbreaker.dm`（21 行）

| 项 | 值 |
|---|---|
| 名称 | "Heartbreaker"（心碎者）|
| 目标 | **殉道**（martyr/normiesgetout——"普通人滚出去"）|
| 定位 | 情人节逆势反派——不保护而是殉道 |
| 报告 | 正常殉道报告 |

---

## 七、对战攻略

### 怎么玩情人节（推荐流程）

```
① 被配对 → 找到你的 date（in_love 状态指引）
② 互相保护（目标：双方存活）
③ 为对方挡伤害/提供医疗
④ 两人都活到最后 → "成功约会" greentext
```

### 怎么玩心碎者

```
① 被选中为 Heartbreaker
② 目标：殉道（normiesgetout）
③ 干扰其他配对/制造混乱
```

---

## 八、数值速查表

| 项 | 值 |
|---|---|
| 源码 | valentines/ 2 文件 110 行 |
| 目标 | 保护 date（互相）|
| 状态 | in_love |
| AI 法律 | 零号法律保护 date |
| 变体 | Third Wheel（电灯泡）|
| 逆势 | Heartbreaker（殉道）|
| 口号 | FOR MY LOVE!! |
| 类型 | ANTAG_FAKE（假反派）|

---

*本文档数值全部实测自 `code/modules/antagonists/valentines/`（2 文件 110 行）源码，无推测。*
