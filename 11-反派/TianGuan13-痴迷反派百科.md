# TianGuan13 · 痴迷（Obsessed）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/obsessed/obsessed.dm`（1 文件 **309 行**）+ 关联脑创伤 `brain_trauma/special/obsessed`
> **类型**: 单人心理反派（跟踪狂）｜**难度**: ★★★（潜伏跟踪+情感操控）
> **一句话**: 你**痴迷**上了一个船员——由**脑创伤**触发，你会跟踪、偷拍、拥抱、嫉妒，最终**谋杀**你的痴迷对象。目标：完成 3 个"诡异目标"后杀掉 TA。
> **名言**: "FOR MY LOVE!!"（为了我的爱！）

---

## 目录

- [一、核心机制](#一核心机制)
- [二、目标系统（5+1 目标全录）](#二目标系统51-目标全录)
- [三、触发机制（脑创伤）](#三触发机制脑创伤)
- [四、核心属性](#四核心属性)
- [五、风味与报告](#五风味与报告)
- [六、对战攻略](#六对战攻略)
- [七、数值速查表](#七数值速查表)

---

## 一、核心机制

**痴迷**（`/datum/antagonist/obsessed`）是心理跟踪型反派——由**脑创伤**（obsessed trauma）触发，不是主动选择。你被强制"爱上"一个船员，会**跟踪 TA、偷拍 TA、拥抱 TA、嫉妒 TA 的同事**，最后**谋杀 TA**。

**核心设计**：
- **创伤触发**：`gain_trauma(/datum/brain_trauma/special/obsessed)`——被脑创伤逼疯
- **3 个诡异目标**：从目标池随机选 3 个（陪伴/拍立得/拥抱/传家宝/嫉妒）
- **最终刺杀**：杀掉痴迷对象（最后完成才能 greentext）
- **跟踪机制**：创伤持续 tick 累积"跟踪时间"（total_time_creeping）
- **Blood 套装**：痴迷预览套装满身是血（"Oh yes, there will be blood..."）

**核心循环**:
```
① 脑创伤触发 → 获得痴迷目标（随机船员）
② 跟踪 TA（累积陪伴时间）
③ 完成 3 个诡异目标：陪伴/拍立得/拥抱（/传家宝/嫉妒）
④ 最终：谋杀痴迷对象
⑤ greentext（先杀就失败其他目标）
```

---

## 二、目标系统（5+1 目标全录）

**代码**: `obsessed.dm` L96-144（forge_objectives）+ L183-304（目标定义）

**生成**：`objectives_to_generate = 3`（随机 3 个）+ 最终刺杀

| # | 目标 | 说明 | 判定 |
|---|---|---|---|
| 1 | **陪伴 Spend Time** | 在 TA 活着时**靠近 TA 共处 5 分钟**（5min ± 60s）| 创伤 tick 累积计时 |
| 2 | **拍立得 Polaroid** | 拍一张**包含 TA 和你**的照片，放在包里 | 照片含 TA（未死）且在你物品中 |
| 3 | **拥抱 Hug** | **拥抱 TA 4-6 次**（随机，活着时）| hugs_needed 归零 |
| 4 | **传家宝 Heirloom** | 偷走 TA 的**家族传家宝**（需 TA 有该特质）| 传家宝在你物品中 |
| 5 | **嫉妒刺杀 Jealous** | 谋杀 **TA 的同事**（同部门优先，否则全站随机）| 同事死亡 |
| 6 | **最终刺杀 Kill**（固定最后）| **谋杀 TA 本人**（痴迷版刺杀）| TA 死亡 |

**目标池逻辑**：
- 3 个必选（陪伴/拍立得/拥抱）
- 传家宝：仅当 TA 有 family_heirloom 特质
- 嫉妒：仅当 TA 不是船长（船长无同事）
- 最终刺杀**总是最后加**（必须先完成其他目标才 greentext）

**嫉妒目标细节**（jealous）：
- 找 TA 同部门同事（departments_bitflags 匹配）
- 无同事 → 全站随机
- "Murder [同事], [痴迷对象]'s coworker."

---

## 三、触发机制（脑创伤）

**代码**: 关联 `brain_trauma/special/obsessed`

| 项 | 值 |
|---|---|
| 触发 | `gain_trauma(/datum/brain_trauma/special/obsessed)` |
| 要求 | **必须有大脑**（can_be_owned 检查脑器官）|
| 效果 | 强制痴迷一个船员（创伤生成 obsession）|
| 跟踪时间 | trauma.total_time_creeping（创伤持续 tick 累积）|
| 治愈 | 创伤治愈 → 变成 **former_obsessed**（前痴迷者）|

**Former Obsessed（前痴迷者）**：
- 治愈后显示给管理员（show_in_antagpanel）
- ANTAG_FAKE 标志（假反派，不算真 antag）
- can_elimination_hijack = ELIMINATION_PREVENT

---

## 四、核心属性

**代码**: `obsessed.dm` L8-26

| 属性 | 值 |
|---|---|
| antagpanel_category | ANTAG_GROUP_CREW |
| pref_flag | ROLE_OBSESSED |
| 幽灵可见 | show_to_ghosts = TRUE |
| HUD | obsessed（痴迷 HUD）|
| 显示名 | show_name_in_check_antagonists = TRUE |
| roundend_category | "obsessed" |
| antag_flags | ANTAG_SKIP_GLOBAL_LIST |
| silent | TRUE（静默——greet 由创伤触发）|
| **自杀口号** | **"FOR MY LOVE!!"**（为了我的爱！）|
| 预览套装 | /datum/outfit/obsessed（沾血套装）|
| **硬核奖励** | hardcore_random_bonus = TRUE |
| **出场音效** | **creepalert.ogg**（stinger_sound）|
| 目标数 | 3（+1 最终刺杀）|

---

## 五、风味与报告

### 5.1 预览套装（outfit/obsessed）

| 槽位 | 装备 |
|---|---|
| 制服 | 工装连体服 |
| 手套 | 乳胶手套 |
| 面罩 | 手术面罩 |
| 脖子 | **相机**（拍立得用！）|
| 外套 | 围裙 |
| 鞋 | 黑色运动鞋 |
| 特殊 | **全部装备沾血**（add_mob_blood）|

### 5.2 回合报告

- 报告头："Someone became obsessed!"（有人变得痴迷了！）
- 跟踪时间：绿字 "spent a total of [X] being near [痴迷对象]!"
- 未跟踪：红字 "did not go near their obsession the entire round! That's extremely impressive!"
- 无创伤：红字 "had no trauma attached... You might as well show yourself!!"

---

## 六、对战攻略

### 怎么玩痴迷（推荐流程）

```
① 被脑创伤触发 → 确定痴迷对象（随机船员）
② 跟踪 TA：靠近累积陪伴时间（5 分钟目标）
③ 拍立得：和 TA 合影（相机在脖子上！）
④ 拥抱 4-6 次
⑤ 传家宝：偷 TA 的传家宝（如果有）
⑥ 嫉妒：杀 TA 的同事（同部门）
⑦ 最后杀 TA 本人 → greentext
```

### 怎么防痴迷（船员对策）

| 方法 | 说明 |
|---|---|
| **盯跟踪狂** | 有人频繁出现在你附近+偷拍 = 痴迷者 |
| **医疗治疗** | 脑创伤可被治愈（治愈后变 former）|
| **别落单** | 痴迷者最终会动手——结伴 |
| **护同事** | 嫉妒目标会杀你同事——部门警惕 |
| **查相机** | 有人拿相机拍你 = 危险信号 |

---

## 七、数值速查表

| 项 | 值 |
|---|---|
| 源码 | obsessed/ 1 文件 309 行 |
| 触发 | 脑创伤（必须有大脑）|
| 目标 | 3 随机 + 1 最终刺杀 |
| 陪伴时间 | 5 分钟（±60s）|
| 拥抱次数 | 4-6 次 |
| 拍立得 | 照片含 TA（未死）|
| 嫉妒 | 同部门同事优先 |
| 出场音 | creepalert.ogg |
| 口号 | FOR MY LOVE!! |
| 硬核奖励 | TRUE |
| 治愈变体 | former_obsessed（假反派）|

---

*本文档数值全部实测自 `code/modules/antagonists/obsessed/obsessed.dm`（309 行）源码，无推测。*
