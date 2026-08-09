# TianGuan13 · 高地人（Highlander）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/highlander/highlander.dm`（1 文件 **103 行**）
> **类型**: 多人决斗型反派（只能活一个）｜**难度**: ★★★（苏格兰剑决斗）
> **一句话**: 你被**苏格兰诅咒**选中——装备高地人套件（苏格兰短裙+高地阔剑），目标：**偷核弹盘 + 成为唯一幸存者**。口号："FOR SCOTLAND!!"（为了苏格兰！）

---

## 目录

- [一、核心机制](#一核心机制)
- [二、装备全录](#二装备全录)
- [三、目标系统](#三目标系统)
- [四、核心属性与特质](#四核心属性与特质)
- [五、机器人变体](#五机器人变体)
- [六、对战攻略](#六对战攻略)
- [七、数值速查表](#七数值速查表)

---

## 一、核心机制

**高地人**（`/datum/antagonist/highlander`）是经典"There Can Be Only One"（只能有一个）决斗型反派：多名高地人互相厮杀，**最后一个活着的才是胜利者**。

**核心设计**：
- **苏格兰套装**：苏格兰短裙+贝雷帽+高地阔剑（claymore）
- **荣誉束缚**：compulsion of honor（荣誉强迫——**直到你是最后一个才能拿别的东西**）
- **特质群**：6 个战斗特质（不死/不燃/免疫电击等）
- **目标**：偷核弹盘 + 歼灭其他高地人
- **机器人变体**：机械体版（"THERE CAN BE ONLY ONE" 零号法律）

**核心循环**:
```
① 被高地人事件选中 → 掉光所有物品
② 装备苏格兰套件（剑+短裙+核弹追踪器）
③ 找到其他高地人 → 决斗（只能活一个）
④ 偷核弹盘（追踪器引导）
⑤ 成为最后幸存者 → 胜利
```

---

## 二、装备全录

**代码**: `highlander.dm` L51-85（give_equipment）

| 槽位 | 装备 | 说明 |
|---|---|---|
| 制服 | 高地人苏格兰短裙（kilt）| 经典苏格兰裙 |
| 耳朵 | 辛迪加耳机 | 通信 |
| 头 | 高地人贝雷帽（beret）| 贝雷帽 |
| 鞋 | 战斗靴 | 战斗 |
| 左口袋 | **核弹追踪器**（pinpointer/nuke）| 自动激活追踪核弹盘 |
| ID | 高地人 ID（**NODROP 不可掉落**）| 锁定身份 |
| 手 | **高地阔剑**（claymore/highlander）| 主武器（NODROP）|
| 手 | **荣誉强迫**（compulsion of honor）| **无法拿其他东西直到最后幸存** |

**高地阔剑**：唯一允许的武器——杀了其他高地人前不能换武器。

---

## 三、目标系统

**代码**: `highlander.dm` L32-39（forge_objectives）

| # | 目标 | 说明 |
|---|---|---|
| 1 | **偷核弹盘**（steal/nukedisc）| 偷到核弹认证磁盘 |
| 2 | **歼灭高地人**（elimination/highlander）| 淘汰其他高地人（只能活一个）|

> 注意：**can_elimination_hijack = ELIMINATION_ENABLED**——高地人可以劫持淘汰机制。

---

## 四、核心属性与特质

### 4.1 Antag 属性

| 属性 | 值 |
|---|---|
| show_in_antagpanel | FALSE（隐藏——事件生成）|
| 显示名 | show_name_in_check_antagonists = TRUE |
| can_elimination_hijack | **ELIMINATION_ENABLED**（可劫持淘汰）|
| **自杀口号** | **"FOR SCOTLAND!!"**（为了苏格兰！）|
| antag_flags | ANTAG_FAKE \| ANTAG_SKIP_GLOBAL_LIST（假反派+跳过全局列表）|
| 脱敏修正 | DESENSITIZED_THRESHOLD × 0.2 |

### 4.2 战斗特质群（applicable_traits）

| # | 特质 | 效果 |
|---|---|---|
| 1 | TRAIT_NOBREATH | 不需要呼吸 |
| 2 | TRAIT_NODISMEMBER | 免疫断肢 |
| 3 | TRAIT_NOFIRE | 免疫燃烧 |
| 4 | TRAIT_NOGUNS | 不能用枪（剑决斗！）|
| 5 | TRAIT_SHOCKIMMUNE | 免疫电击 |
| 6 | TRAIT_TOSS_GUN_HARD | 扔枪用力（不能用枪的体现）|

**附加**：
- 移除和平主义（TRAIT_PACIFISM）——高地人必须能打
- 非暴力特质者恢复和平主义（on_removal）

---

## 五、机器人变体

**代码**: `highlander.dm` L87-103（/datum/antagonist/highlander/robot）

| 项 | 值 |
|---|---|
| 名称 | "highlander" |
| 装备 | 断开 AI + 清除固有法律 |
| **零号法律** | **"THERE CAN BE ONLY ONE"**（只能有一个）|
| 模型 | 辛迪加苏格兰机械体（kiltborg）|
| 武器 | 机器人版高地阔剑 |

---

## 六、对战攻略

### 怎么玩高地人（推荐流程）

```
① 被选中 → 掉光物品 → 领苏格兰套件
② 拿核弹追踪器 → 找核弹盘（目标 1）
③ 找其他高地人 → 阔剑决斗（目标 2）
④ 杀光其他高地人 → 成为唯一
⑤ 拿核弹盘 → 完成双目标
```

### 怎么防高地人（船员对策）

| 方法 | 说明 |
|---|---|
| **护核弹盘** | 高地人目标之一——磁盘重点保护 |
| **远离决斗** | 高地人互相厮杀——别卷入 |
| **缴械注意** | 高地人不能拿其他武器（荣誉强迫）——可利用 |
| **认标志** | 苏格兰短裙+阔剑=高地人 |

---

## 七、数值速查表

| 项 | 值 |
|---|---|
| 源码 | highlander/ 1 文件 103 行 |
| 目标 | 偷核弹盘 + 歼灭高地人 |
| 武器 | 高地阔剑（唯一）|
| 特质 | 6 个（不死/不燃/免电/禁枪等）|
| 口号 | FOR SCOTLAND!! |
| 机器人 | "THERE CAN BE ONLY ONE" 零号法律 |
| 荣誉强迫 | 最后幸存前不能拿别的东西 |
| 脱敏 | ×0.2 |

---

*本文档数值全部实测自 `code/modules/antagonists/highlander/highlander.dm`（103 行）源码，无推测。*
