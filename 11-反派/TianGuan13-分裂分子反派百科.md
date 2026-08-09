# TianGuan13 · 分裂分子（Separatist）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/separatist/`（3 文件 **280 行**：separatist 134 + nation_creation 76 + objectives 70）
> **类型**: 部门独立型反派（团队建国流）｜**难度**: ★★★（部门叛乱+国战）
> **一句话**: 你的**部门独立建国**了！你是新国家（Nation）的公民——目标：**摧毁敌对国家**（危险模式）或完成国格 flavor 目标。口号："FOR THE MOTHERLAND!!"（为了祖国！）

---

## 目录

- [一、核心机制](#一核心机制)
- [二、建国机制（nation_creation）](#二建国机制nation_creation)
- [三、目标系统全录](#三目标系统全录)
- [四、国家团队（Nation Team）](#四国家团队nation-team)
- [五、核心属性](#五核心属性)
- [六、硅基变体（UN AI）](#六硅基变体un-ai)
- [七、对战攻略](#七对战攻略)
- [八、数值速查表](#八数值速查表)

---

## 一、核心机制

**分裂分子**（`/datum/antagonist/separatist`）是"部门独立"型反派：某个部门**从空间站分裂建国**，该部门全体成员成为新国家的公民（nation），对抗空间站和其他国家。

**核心设计**：
- **部门独立**：随机部门（或指定）从空间站分裂 → 成为独立国家
- **全体转化**：该部门所有在职成员自动成为分裂分子
- **新成员加入**：之后入职该部门的玩家**自动加入**（COMSIG_GLOB_CREWMEMBER_JOINED）
- **危险模式**：国家对其他国家宣战（destroy_nation 目标）
- **Flavor 目标**：9 种国格目标（收税/立雕像/全副武装等）

**核心循环**:
```
① 部门独立事件触发 → 你的部门建国（随机部门：助手/医疗/工程/科研/货运/服务/安保）
② 全体成员转化为分裂分子（nation 团队）
③ 国家生成目标：flavor +（危险模式）宣战其他国家
④ 新入职该部门的玩家自动入国
⑤ 完成目标：灭敌国 / 完成国格目标
```

---

## 二、建国机制（nation_creation）

**代码**: `nation_creation.dm`（76 行，create_separatist_nation）

| 项 | 值 |
|---|---|
| 可选部门 | 助手/医疗/工程/科研/货运/服务/安保（随机-已独立者）|
| 转化条件 | 部门专属职业（departments_list[1] 匹配）|
| 国家名 | department.generate_nation_name() 生成 |
| 公告 | "The new independent state of [国家名] has formed from the ashes of the [部门] department!" |
| 助手特例 | "The assistants of the station have risen to form the new independent state of [国家名]!" |
| 无人转化 | 没有成员 → 国家销毁 |
| 新入职加入 | 之后入职自动加入（信号监听）|

---

## 三、目标系统全录

**代码**: `objectives.dm`（70 行）

### 3.1 灭国目标（destroy_nation）

| 项 | 值 |
|---|---|
| 触发 | 危险模式 + 有其他独立国家 |
| 目标 | "Make sure no member of [敌国] ([部门]) nation escapes alive!"（确保敌国无人逃脱）|
| 判定 | 敌国所有成员死亡/未逃离（CentCom/辛迪加基地算逃脱）|
| 宣战 | 宣战时敌国**获得反击目标**（war_declared）|

### 3.2 Flavor 目标（separatist_fluff，9 种随机）

| # | 目标 |
|---|---|
| 1 | "The rest of the station must be taxed for their use of [国]'s services."（向全站征税）|
| 2 | "Make statues everywhere of your glorious leader of [国]."（立雕像）|
| 3 | "[国] must be absolutely blinged out."（全副闪亮）|
| 4 | "Damage as much of the station as you can, keep it in disrepair."（破坏空间站）|
| 5 | "Heavily reinforce [国] against the dangers of the outside world."（重兵防御）|
| 6 | "Make sure [国] is fully off the grid."（完全脱离电网）|
| 7 | "Use a misaligned teleporter to make you flypeople. Bring toxin medication!"（错位传送变飞人）|
| 8 | "Save the station when it needs you most."（成为守护者）|
| 9 | "Arm up. The citizens of [国] have a right to bear arms."（武装起来）|

> Flavor 目标判定 = 恒真（永远完成——纯 flavor 装饰）。

### 3.3 联合国目标（united_nations，硅基专属）

| 项 | 值 |
|---|---|
| 触发 | 部门是硅基（silicon department）|
| 目标 | "Maintain the peace on the station. Ensure every nation has a delegate alive by the end of the round."（维持和平，确保每国代表存活）|
| 判定 | 所有国家都有代表存活 |

---

## 四、国家团队（Nation Team）

**代码**: `separatist.dm` L1-79（/datum/team/nation）

| 项 | 值 |
|---|---|
| name | "Nation"（国家）|
| member_name | "separatist"（分裂分子）|
| potential_recruits | 可入国职业列表 |
| dangerous_nation | 危险国家标志（默认 TRUE）|
| 新成员 | 入职自动加入（信号）|
| 目标共享 | update_all_member_objectives（全员同步目标）|
| 宣战 | war_declared（被宣战方获得反击目标）|

---

## 五、核心属性

**代码**: `separatist.dm` L81-90

| 属性 | 值 |
|---|---|
| name | "Separatists" |
| show_in_antagpanel | FALSE（隐藏——事件生成）|
| 显示名 | show_name_in_check_antagonists = TRUE |
| **自杀口号** | **"FOR THE MOTHERLAND!!"**（为了祖国！）|
| 界面 | AntagInfoSeparatist |
| 国家引用 | nation（团队 datum）|
| UI 颜色 | 部门 UI 色（低亮度）|
| 心情/音效/劫机 | **无**（源码 grep 0 命中——非个人型反派的字段缺失）|

---

## 六、硅基变体（UN AI）

| 项 | 值 |
|---|---|
| 触发 | 部门是硅基（silicon）|
| AI 角色 | 获得 **united_nations AI 法律**（联合国法律）|
| 目标 | 联合国和平目标（见 3.3）|
| 定位 | 硅基作为"维和力量"而非战斗国家 |

---

## 七、对战攻略

### 怎么玩分裂分子（推荐流程）

```
① 部门独立 → 你成为国家公民（看 UI 国家名+颜色）
② 查看国家目标（flavor + 可能宣战）
③ 危险模式：找敌国成员 → 歼灭（不让他们逃到 CentCom/基地）
④ Flavor：收税/立雕像/武装/脱离电网（选一个干）
⑤ 新同事入职自动加入 → 壮大国家
```

### 怎么防分裂分子（船员对策）

| 方法 | 说明 |
|---|---|
| **盯公告** | 部门独立有公告——第一时间知道谁是分裂分子 |
| **护其他部门** | 危险国家会攻击其他部门 |
| **防逃脱** | 灭国目标靠"无人生还"——别让分裂分子逃到 CentCom |
| **硅基 UN** | AI 有联合国法律——维持和平立场 |

---

## 八、数值速查表

| 项 | 值 |
|---|---|
| 源码 | separatist/ 3 文件 280 行 |
| 触发 | 部门独立（随机 7 部门）|
| 团队 | Nation（国家）|
| 目标 | destroy_nation + 9 种 flavor + UN（硅基）|
| 新成员 | 自动加入 |
| 口号 | FOR THE MOTHERLAND!! |
| 界面 | AntagInfoSeparatist |
| UI 颜色 | 部门色（低亮度）|
| 心情/音效 | 无（源码事实）|

---

*本文档数值全部实测自 `code/modules/antagonists/separatist/`（3 文件 280 行）源码，无推测。*
