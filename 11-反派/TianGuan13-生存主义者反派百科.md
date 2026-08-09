# TianGuan13 · 生存主义者（Survivalist）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/survivalist/survivalist.dm`（1 文件 **104 行**）
> **类型**: 个人生存型反派（囤积流）｜**难度**: ★★（保命+囤货）
> **一句话**: 你的**自身安全高于一切**——目标：**存活**（或囤积枪械/魔法物品）。有 3 个变体：**枪械囤积者/业余魔法师/大逃杀选手**。口号："FOR MYSELF!!"（为了我自己！）

---

## 目录

- [一、核心机制](#一核心机制)
- [二、变体全录（3 种）](#二变体全录3-种)
- [三、目标系统](#三目标系统)
- [四、核心属性](#四核心属性)
- [五、对战攻略](#五对战攻略)
- [六、数值速查表](#六数值速查表)

---

## 一、核心机制

**生存主义者**（`/datum/antagonist/survivalist`）是最简单的反派类型：**活下去**。核心目标是 survive（存活），变体则附加囤积枪械/魔法物品/大逃杀目标。

**核心设计**：
- **存活优先**：核心目标 = 活到回合结束
- **3 变体**：枪械囤积者（囤枪）/ 业余魔法师（囤魔法）/ 大逃杀选手（竞技场存活）
- **隐藏 antag**：show_in_antagpanel = FALSE
- **硬核奖励**：guns/magic 变体有 hardcore_random_bonus

**核心循环**:
```
① 被选中为生存主义者 → 获得存活目标
② （变体）囤积枪械/魔法物品
③ 躲藏/防御保命
④ 活到回合结束 = 胜利
```

---

## 二、变体全录（3 种）

| # | 变体 | 名称 | 目标 | 特点 |
|---|---|---|---|---|
| 1 | **guns 枪械囤积者** | Survivalist | 囤积 **N 把枪**（steal_n_of_type/summon_guns）| "囤枪保命，挡路者杀" |
| 2 | **magic 业余魔法师** | **Amateur Magician** | 囤积 **N 件魔法物品**（summon_magic）| 获得魔法天赋特质 |
| 3 | **battle_royale 大逃杀** | **Battle Royale Contestant** | 到达目标区域+存活 | 大逃杀竞技场（Rumble Royale）|

### 2.1 枪械囤积者（guns）

> "Your own safety matters above all else, and the only way to ensure your safety is to stockpile weapons! Grab as many guns as possible, by any means necessary. Kill anyone who gets in your way."

- 目标：囤积枪械（数量目标）
- 硬核奖励：TRUE

### 2.2 业余魔法师（magic）

> "Grow your newfound talent! Grab as many magical artefacts as possible, by any means necessary. Kill anyone who gets in your way."

- 目标：囤积魔法物品
- 特质：**TRAIT_MAGICALLY_GIFTED + TRAIT_SEE_BLESSED_TILES**（魔法天赋+看见祝福地块）
- 硬核奖励：TRUE

### 2.3 大逃杀选手（battle_royale）

- 名称：Battle Royale Contestant
- 目标：**到达目标区域**（"Reach the [区域] before time runs out."）+ 存活
- 死亡 → 移除 antag（on_died）
- 存活 → **royale_survivor 心情 +4**（"I made it out of Rumble Royale with my life."）

---

## 三、目标系统

| 变体 | 目标 |
|---|---|
| 基础 | **survive**（存活到回合结束）|
| guns | survive + **囤积 N 把枪**（summon_guns）|
| magic | survive + **囤积 N 件魔法物品**（summon_magic）|
| battle_royale | **到达目标区域** + survive |

---

## 四、核心属性

| 属性 | 值 |
|---|---|
| show_in_antagpanel | FALSE（隐藏）|
| 显示名 | show_name_in_check_antagonists = TRUE |
| **自杀口号** | **"FOR MYSELF!!"**（为了我自己！）|
| greet_message | 变体专属（见上）|
| announce_objectives | TRUE（默认公告目标）|
| 硬核奖励 | guns/magic 变体 TRUE |

---

## 五、对战攻略

### 怎么玩生存主义者（推荐流程）

```
① 被选中 → 看变体（存活/囤枪/囤魔法/大逃杀）
② 囤枪流：找武器库 → 囤枪 → 防守
③ 魔法流：找魔法物品 → 囤积
④ 大逃杀：去目标区域 → 活到最后
⑤ 活到回合结束 = 胜利
```

### 怎么防生存主义者（船员对策）

| 方法 | 说明 |
|---|---|
| **盯武器库** | 囤枪流会扫武器库——提前防守 |
| **盯魔法物品** | 魔法流会搜刮魔法物品 |
| **大逃杀区** | 大逃杀选手会去目标区——注意埋伏 |
| **辨识** | 生存主义者没有明显标志——靠行为判断 |

---

## 六、数值速查表

| 项 | 值 |
|---|---|
| 源码 | survivalist/ 1 文件 104 行 |
| 基础目标 | 存活 |
| 变体 | 3 种（guns/magic/battle_royale）|
| 口号 | FOR MYSELF!! |
| 魔法特质 | MAGICALLY_GIFTED + SEE_BLESSED_TILES |
| 大逃杀心情 | royale_survivor +4 |
| 硬核奖励 | guns/magic TRUE |

---

*本文档数值全部实测自 `code/modules/antagonists/survivalist/survivalist.dm`（104 行）源码，无推测。*
