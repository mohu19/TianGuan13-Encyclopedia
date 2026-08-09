# TianGuan13 · 小丑核弹队（Clown Ops）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/clown_ops/`（4 文件 **396 行**）+ 关联 `code/modules/antagonists/nukeop/`（核弹队基础）
> **类型**: 团队型袭击反派（辛迪加小丑精英）｜**难度**: ★★★★（小丑核弹队版核弹任务）
> **一句话**: 你是**小丑核弹队**——辛迪加的小丑精英版核弹队：**香蕉核弹**（bananium fission explosive）+ 香蕉能量剑 + 搞笑套装。目标与核弹队相同（核弹炸站），但**爆炸后全站变小丑**！口号："HAPPY BIRTHDAY!!"（生日快乐！！）

---

## 目录

- [一、核心机制](#一核心机制)
- [二、目标系统](#二目标系统)
- [三、装备全录](#三装备全录)
- [四、武器详解](#四武器详解)
- [五、香蕉核弹](#五香蕉核弹)
- [六、核心属性](#六核心属性)
- [七、与普通核弹队对比](#七与普通核弹队对比)
- [八、对战攻略](#八对战攻略)
- [九、数值速查表](#九数值速查表)

---

## 一、核心机制

**小丑核弹队**（`/datum/antagonist/nukeop/clownop`）是核弹队的**小丑变体**：继承核弹队全部机制（渗透者飞船/核弹/认证磁盘），但换成小丑装备+香蕉核弹。

**核心设计**：
- **继承核弹队**：`/datum/antagonist/nukeop/clownop`（子类）
- **香蕉核弹**：核弹本体是 bananium（香蕉金属）裂变弹
- **TRAIT_NAIVE 天真**：小丑核弹队获得"天真"特质（免疫部分负面）
- **喜剧代谢**：肝脏 TRAIT_COMEDY_METABOLISM（喜剧代谢）
- **滑倒武器**：香蕉剑/水花向日葵——以滑倒控制为主
- **队长头衔**：Head Honker（首席哈哈）/Slipmaster（滑倒大师）/Clown King（小丑王）/Honkbearer（号角使者）

**核心循环**:
```
① 乘小丑渗透者飞船（infiltrator_clown）到空间站
② 找/偷认证磁盘（同核弹队）
③ 安放香蕉核弹 → 90s 倒计时
④ 守核弹（小丑武器滑倒敌人）
⑤ 爆炸 = 胜利（全站变小丑！）
```

---

## 二、目标系统

| 项 | 值 |
|---|---|
| 核心目标 | **nuclear**（核弹爆炸，继承核弹队）|
| 队伍 | nuke_team（辛迪加小队）|
| 挑战物品 | nuclear_challenge/clownops（挑战模式）|
| 认证磁盘 | 核弹启动必需（同核弹队）|

---

## 三、装备全录

**代码**: `outfits.dm`（36 行）

### 3.1 套装变体

| # | 套装 | 说明 |
|---|---|---|
| 1 | Clown Operative - Basic | 基础小丑核弹队 |
| 2 | Clown Operative Leader - Basic | 队长版 |
| 3 | Clown Operative - Reinforcement | 增援版（无 TC）|

### 3.2 基础套装槽位

| 槽位 | 装备 |
|---|---|
| 制服 | 辛迪加制服 |
| 背部 | **小丑背包**（clown backpack）|
| 植入体 | **sad_trombone 悲伤长号**（死亡音效）+ 核弹战术地图 |
| 队长 | 战术地图 leader 版 |

### 3.3 预览套装

| 套装 | 装备 |
|---|---|
| Clown Operative | MOD 服（honkerative 型号）+ 辛迪加制服 |
| Clown Operative Elite | 同上（精英版）|

---

## 四、武器详解

**代码**: `clown_weapons.dm`（216 行）

### 4.1 水花向日葵（water flower）

| 项 | 值 |
|---|---|
| 描述 | "看似无害的向日葵...带滑腻的陷阱" |
| 机制 | 喷水滑倒敌人（非致命控制）|

### 4.2 香蕉能量剑（bananium sword）

| 项 | 值 |
|---|---|
| 名称 | bananium sword（香蕉能量剑）|
| **伤害** | **force 0 / throwforce 0**（不伤人！）|
| 图标色 | bananium（香蕉色）|
| **滑倒组件** | 激活时 slippery 60（GALOSHES_DONT_HELP——胶鞋也没用！）|
| 攻击动词 | slips / slip（滑倒攻击）|
| 投掷速度 | throw_speed_on = 4 |
| 失败音效 | 长号音（trombone——失败时搞笑音）|

> **定位**：香蕉剑不是武器是**控制工具**——0 伤害但 60 滑倒，把敌人滑在地上。

### 4.3 其他

- 小丑 MOD 服（honkerative 型号）

---

## 五、香蕉核弹

**代码**: `bananium_bomb.dm`（60 行）

| 项 | 值 |
|---|---|
| 名称 | **bananium fission explosive**（香蕉裂变弹）|
| 图标 | bananiumbomb（香蕉核弹图标）|
| 类型 | 核弹（/obj/machinery/nuclearbomb/syndicate/bananium）|
| 倒计时 | 同普通核弹（90s）|
| **特殊效果** | **爆炸后把幸存者变成小丑**（nuke_effects）|
| 电影效果 | 香蕉核弹专属（get_cinematic_type）|

> **关键**：香蕉核弹爆炸不杀——**把全站变小丑**！喜剧核弹。

---

## 六、核心属性

**代码**: `clownop.dm`（84 行）

| 属性 | 值 |
|---|---|
| name | ROLE_CLOWN_OPERATIVE |
| roundend_category | "clown operatives" |
| antagpanel_category | ANTAG_GROUP_CLOWNOPS |
| 套装 | /datum/outfit/syndicate/clownop |
| 职业 | nuclear_operative/clown_operative |
| **自杀口号** | **"HAPPY BIRTHDAY!!"**（生日快乐！！）|
| 预览 | clown_operative_elite（+背后普通版）|
| 核弹图标 | bananiumbomb_base |
| 渗透者 | **infiltrator_clown**（小丑渗透者飞船）|
| **特质** | **TRAIT_NAIVE**（天真——免疫部分负面）|
| **肝脏** | **TRAIT_COMEDY_METABOLISM**（喜剧代谢）|

### 6.1 队长（leader/clownop）

| 属性 | 值 |
|---|---|
| 名称 | Clown Operative Leader |
| 头衔 | Head Honker（首席哈哈）/Slipmaster（滑倒大师）/Clown King（小丑王）/Honkbearer（号角使者）|
| 改名 | 队长名带标题（"Head Honker 张三"）|
| 挑战物品 | nuclear_challenge/clownops |

---

## 七、与普通核弹队对比

| 项 | 普通核弹队 | 小丑核弹队 |
|---|---|---|
| datum | nukeop | **nukeop/clownop**（子类）|
| 核弹 | 普通核弹 | **香蕉核弹**（变全站小丑）|
| 武器 | Bulldog 霰弹枪等 | **香蕉剑（0 伤+滑倒）+ 水花向日葵** |
| 植入体 | 武器认证等 | **sad_trombone 悲伤长号** |
| 渗透者 | infiltrator_basic | **infiltrator_clown** |
| 特质 | 无 | **TRAIT_NAIVE 天真** |
| 口号 | FOR THE SYNDICATE!! | **HAPPY BIRTHDAY!!** |
| 队长头衔 | 无 | 4 种小丑头衔 |
| 增援 | 普通 | 无 TC 版 |

---

## 八、对战攻略

### 怎么玩小丑核弹队（推荐流程）

```
① 乘小丑渗透者飞船入站
② 找/偷认证磁盘（同核弹队）
③ 安放香蕉核弹 → 90s 倒计时
④ 用香蕉剑滑倒防守者（0 伤但滑倒 60——胶鞋无效）
⑤ 水花向日葵控场
⑥ 爆炸 = 全站变小丑 = 胜利
```

### 怎么防小丑核弹队（船员对策）

| 方法 | 说明 |
|---|---|
| **护磁盘** | 同核弹队——认证磁盘是启动关键 |
| **拆核弹** | 香蕉核弹也是核弹——可拆 |
| **防滑倒** | 香蕉剑滑倒**胶鞋无效**（GALOSHES_DONT_HELP）——别近战 |
| **远程** | 远程武器打小丑核弹队（近战会被滑倒）|

---

## 九、数值速查表

| 项 | 值 |
|---|---|
| 源码 | clown_ops/ 4 文件 396 行 |
| 类型 | 核弹队子类 |
| 核弹 | 香蕉裂变弹（变全站小丑）|
| 香蕉剑 | 0 伤 + 滑倒 60（胶鞋无效）|
| 口号 | HAPPY BIRTHDAY!! |
| 特质 | TRAIT_NAIVE |
| 肝脏 | COMEDY_METABOLISM |
| 渗透者 | infiltrator_clown |
| 队长头衔 | 4 种（Head Honker 等）|
| 植入体 | sad_trombone |

---

*本文档数值全部实测自 `code/modules/antagonists/clown_ops/`（4 文件 396 行）源码，无推测。*
