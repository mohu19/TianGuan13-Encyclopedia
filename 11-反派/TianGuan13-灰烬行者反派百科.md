# TianGuan13 · 灰烬行者（Ash Walker）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/ashwalker/ashwalker.dm`（1 文件 **80 行**）
> **类型**: 部落型反派（拉瓦兰原住民）｜**难度**: ★★（守护巢穴+献祭）
> **一句话**: 你是**拉瓦兰灰烬之地的部落成员**——目标：**守护 Necropolis 巢穴** + 向巢穴**献祭"有价值"的祭品**（产卵繁衍）。口号（彩蛋）："I HAVE NO IDEA WHAT THIS THING DOES!!"

---

## 目录

- [一、核心机制](#一核心机制)
- [二、部落团队（Necropolis 守护）](#二部落团队necropolis-守护)
- [三、核心属性](#三核心属性)
- [四、风味与心情](#四风味与心情)
- [五、NOVA 专属改动](#五nova-专属改动)
- [六、对战攻略](#六对战攻略)
- [七、数值速查表](#七数值速查表)

---

## 一、核心机制

**灰烬行者**（`/datum/antagonist/ashwalker`）是拉瓦兰（Lavaland）地图的原住民反派：从 **Necropolis 巢穴的蛋**孵化，守护巢穴并向它献祭。

**核心设计**：
- **巢穴守护**：保护 Necropolis（protect_object 目标）
- **献祭系统**：向巢穴献祭"有价值"（带肉）的祭品 → 巢穴产卵 → 孵化更多灰烬行者
- **部落团队**：Ash Walker Tribe（全员共享目标）
- **假反派**：ANTAG_FAKE（原住民不是传统反派）

**核心循环**:
```
① 从 Necropolis 蛋孵化 → 成为部落成员
② 守护巢穴（目标：Necropolis 存活）
③ 猎杀"值得"的生物（带肉）→ 献祭给巢穴
④ 巢穴产卵 → 新灰烬行者孵化 → 部落壮大
```

---

## 二、部落团队（Necropolis 守护）

**代码**: `ashwalker.dm` L45-80（/datum/team/ashwalkers）

| 项 | 值 |
|---|---|
| 团队名 | Ash Walker Tribe（灰烬行者部落）|
| 成员名 | Ash Walker |
| sacrifices_made | 已献祭数（带肉祭品计数）|
| eggs_created | 巢穴产卵数 |

**目标**:
| 目标 | 说明 |
|---|---|
| **守护 Necropolis**（protect_object）| 巢穴存活 = 胜利（"The nest stands! Glory to the Necropolis!"）|
| 部落其他目标 | 共享 |

**回合报告**:
- "The nest stands! Glory to the Necropolis!"（巢穴屹立！荣耀归于 Necropolis！）/ "The Necropolis was destroyed, the tribe has fallen..."（巢穴被毁，部落已亡...）
- 献祭统计："performed [N] sacrifices... produced [N] Ash Walker eggs."

---

## 三、核心属性

| 属性 | 值 |
|---|---|
| pref_flag | **ROLE_LAVALAND**（拉瓦兰角色）|
| show_in_antagpanel | FALSE（隐藏）|
| 幽灵可见 | show_to_ghosts = TRUE |
| antagpanel_category | ANTAG_GROUP_ASHWALKERS |
| **自杀口号** | **"I HAVE NO IDEA WHAT THIS THING DOES!!"**（彩蛋——我不知道这东西干啥的！）|
| antag_flags | ANTAG_FAKE \| ANTAG_SKIP_GLOBAL_LIST（假反派）|

---

## 四、风味与心情

| 机制 | 说明 |
|---|---|
| **headpike 心情** | 查看**头桩**（headpike，敌人的头插在桩上）→ **sacrifice_good 心情**（"oogabooga"——原始喜悦）|
| 出生 | 从 Necropolis 蛋孵化 |
| 语言 | 原始部落沟通 |

---

## 五、NOVA 专属改动

| 改动 | 说明 |
|---|---|
| **骷髅钥匙移除** | NOVA 移除了 teach_crafting_recipe(skeleton_key)（灰烬仪式化）|
| **阵营保持中立** | NOVA 移除 add/remove_faction(FACTION_NEUTRAL)——灰烬行者仍保持中立阵营，仪式化处理 |

---

## 六、对战攻略

### 怎么玩灰烬行者（推荐流程）

```
① 从蛋孵化 → 看部落目标（守护 Necropolis）
② 猎杀带肉生物 → 拖回巢穴献祭
③ 献祭 → 巢穴产卵 → 新蛋孵化
④ 守卫巢穴（防掠夺者/挖矿者破坏）
⑤ 巢穴存活到回合结束 = 胜利
```

### 怎么防灰烬行者（船员对策）

| 方法 | 说明 |
|---|---|
| **别惹巢穴** | 灰烬行者守护 Necropolis——破坏会引发围攻 |
| **拉瓦兰警惕** | 在拉瓦兰挖矿时注意部落活动 |
| **别被抓献祭** | 被灰烬行者抓去 = 变成祭品 |

---

## 七、数值速查表

| 项 | 值 |
|---|---|
| 源码 | ashwalker/ 1 文件 80 行 |
| pref_flag | ROLE_LAVALAND |
| 团队 | Ash Walker Tribe |
| 目标 | 守护 Necropolis + 献祭 |
| 献祭 | 带肉祭品 → 产卵 |
| 口号 | I HAVE NO IDEA WHAT THIS THING DOES!! |
| 心情 | sacrifice_good（看头桩）|
| NOVA | 骷髅钥匙移除/保持中立 |

---

*本文档数值全部实测自 `code/modules/antagonists/ashwalker/ashwalker.dm`（80 行）源码，无推测。*
