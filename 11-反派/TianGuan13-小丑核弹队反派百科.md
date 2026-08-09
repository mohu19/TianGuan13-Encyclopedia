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
- [五、Uplink 专属物品（17 件）](#五uplink-专属物品17-件)
- [六、香蕉核弹](#六香蕉核弹)
- [七、核心属性](#七核心属性)
- [八、与普通核弹队对比](#八与普通核弹队对比)
- [九、对战攻略](#九对战攻略)
- [十、数值速查表](#十数值速查表)

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

### 3.2 基础套装槽位（完整）

| 槽位 | 装备 |
|---|---|
| 制服 | 辛迪加制服 |
| 鞋 | 小丑战斗鞋（clown_shoes/combat）|
| 面罩 | 小丑帽（clown_hat）|
| 手套 | 战斗手套 |
| 背部 | **小丑背包**（clown backpack）|
| 耳朵 | 辛迪加耳机（alt）|
| 左口袋 | 核弹追踪器（pinpointer/nuke/syndicate）|
| 右口袋 | **自行车笛**（bikehorn——搞笑工具）|
| ID | 变色龙精英 ID（chameleon/elite，trim=operative/clown）|
| 背包内容 | toy/riot/clandestine 枪（伤害×1.4）+ 匕首笔 + **笨拙基因注射器**（dnainjector/clumsymut）+ 辛迪加套件 + **clownpins 击针 7 个**（ultra）+ 水花向日葵/润滑剂 + honkerative MOD 皮肤 |
| 植入体 | **sad_trombone 悲伤长号** + 核弹战术地图 |
| Uplink | uplink/clownop 型 |
| ID 标题 | "Syndicate Entertainment Operative"（辛迪加娱乐特工）|
| 安全 HUD | **SECHUD_CLOWNOPS**（安保看到小丑核弹队标记）|

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

### 4.3 其他武器

| 武器 | 数值 |
|---|---|
| **香蕉能量盾** | throw_speed 1/throw_range 5/force 0；激活加 slippery 60 + **boomerang 回旋**（throw_range+2）；投掷反弹 |
| **泪须手雷**（tearstache）| 范围 view(6)；给无小丑/哑剧面具者**粘假胡子**（TRAIT_NODROP 600 分秒=60s）|
| **水枪**（waterflower/lube）| volume 30/lube 30u/转移 3u 每次/range 1 |
| **炸弹香蕉**（bombanana）| 种子→树→吃下→生成 **bombanana 皮**（含增益迷你炸弹，det_time 5s 倒计爆炸）+ log_bomber 记录 |
| **玩具枪**（toy/riot/clandestine）| 伤害×1.4（小丑核弹队强化）|
| **clownpins 击针** | 7 个 ultra 击针（枪械增强，比普通便宜）|

---

## 五、Uplink 专属物品（17 件）

**代码**: `code/modules/uplink/uplink_items/clownops.dm`（UPLINK_CLOWN_OPS = 1<<2）

| # | 物品 | TC |
|---|---|---|
| 1 | 香蕉奶油派炮 pie_cannon | 10 |
| 2 | **香蕉能量盾** bananashield | 16 |
| 3 | **香蕉能量剑** clownsword | 3 |
| 4 | 小丑击针 clownoppin | 1 |
| 5 | 超级击针 superpin | 4 |
| 6 | 泡沫 SMG foamsmg | 5 |
| 7 | 泡沫机枪 foammachinegun | 10 |
| 8 | 炸弹香蕉 bombanana | 4 |
| 9 | 小丑炸弹 clown_bomb | 15 |
| 10 | 泪须手雷 tearstache | 3 |
| 11 | 皮纳塔 pinata（限量 1）| 12 |
| 12 | 小丑增援 clown_reinforcement（角色限定/可退）| 20 |
| 13 | 猿人特工 monkey_agent | 7 |
| 14 | 猿人补给 monkey_supplies | 4 |
| 15 | **Dark H.O.N.K. 机甲** | **80** |
| 16 | 战斗香蕉鞋 combatbananashoes | 6 |
| 17 | 笨拙注射器 clumsiness injector | 1 |

### 5.1 Dark H.O.N.K. 机甲（80 TC）

| 项 | 值 |
|---|---|
| 耐久 | **max_integrity 300** |
| 护甲 | 近战 40/子弹 40/**激光 50**/能量 35/炸弹 20/**火 100/酸 100** |
| 移速 | movedelay 3 |
| 近战 | force 30 |
| 温度 | max_temp 35000 |
| 权限 | ACCESS_SYNDICATE + ID_LOCK_ON（锁 ID）|
| 武器 | **HoNkEr BlAsT 5000**（气喇叭击退 6 格）+ **bombanana 迫击炮**（8 发）+ tearstache 榴弹 |
| 其他 | hyper cell + phasic 扫描 |

---

## 六、香蕉核弹

**代码**: `bananium_bomb.dm`（60 行）

| 项 | 值 |
|---|---|
| 名称 | **bananium fission explosive**（香蕉裂变弹）|
| 图标 | bananiumbomb（香蕉核弹图标）|
| 类型 | 核弹（/obj/machinery/nuclearbomb/syndicate/bananium）|
| 倒计时 | 同普通核弹（90s）|
| **爆炸特效** | **airhorn.ogg**（空气喇叭音效，cinematic/nuke/clown，cleanup 10s）|
| **特殊效果** | **把幸存者变成小丑**（make_into_clown）：Stun 1s + 强制小丑服/鞋/面具（NODROP）+ **clumsy 突变** + 小丑恐惧脑伤 |
| 炸歪站 | 自行车笛 fake 特效（bikehorn.ogg）|

> **关键**：香蕉核弹爆炸不杀——**把全站变小丑**！喜剧核弹（Stun+强制小丑装+笨拙基因）。

---

## 七、核心属性

**代码**: `clownop.dm`（84 行）

### 7.1 小丑变体覆写（clownop.dm）

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
| **特质** | **TRAIT_NAIVE**（天真——尸体显示"睡着了"）|
| **肝脏** | **TRAIT_COMEDY_METABOLISM**（喜剧代谢——吃香蕉回复/酒精代谢特化）|

### 7.2 继承自核弹队基类（operative.dm）

| 属性 | 值 |
|---|---|
| pref_flag | ROLE_OPERATIVE（继承）|
| HUD | synd（继承）|
| 心情 | focused（继承）|
| **劫机速度** | **2**（继承——打不掉空间站就抢穿梭机）|
| 基类口号 | FOR THE SYNDICATE!!（小丑覆写为 HAPPY BIRTHDAY!!）|
| 出场音效 | ops.ogg（继承）|
| 队长折扣 | 5 团队 + 10 限量（继承）|
| TC 加成 | 每 5 人 +5（继承）|
| 脱敏修正 | DESENSITIZED_THRESHOLD × 0.5（继承）|

### 7.3 队长（leader/clownop）

| 属性 | 值 |
|---|---|
| 名称 | Clown Operative Leader |
| 头衔 | Head Honker（首席哈哈）/Slipmaster（滑倒大师）/Clown King（小丑王）/Honkbearer（号角使者）|
| 改名 | 队长名带标题（"Head Honker 张三"；非人类 "队名 标题"）|
| 挑战物品 | nuclear_challenge/clownops（播 **clownops.ogg**）|

### 7.4 生成规则

| 项 | 值 |
|---|---|
| 规则集 | roundstart/midround（**weight = 0**——需强制开启）|
| 队长 | 最有经验者 |
| 职业 | nuclear_operative/clown_operative |
| 增援 | reinforcement/clownop（outfit 无 TC）|

---

## 八、与普通核弹队对比

| 项 | 普通核弹队 | 小丑核弹队 |
|---|---|---|
| datum | nukeop | **nukeop/clownop**（子类）|
| 核弹 | 普通核弹 | **香蕉核弹**（变全站小丑+airhorn.ogg）|
| 武器 | Bulldog 霰弹枪等 | **香蕉剑（0 伤+滑倒）+ 水花向日葵 + 泪须手雷** |
| 植入体 | 武器认证等 | **sad_trombone 悲伤长号** |
| 渗透者 | infiltrator_basic | **infiltrator_clown**（1550 行专属地图）|
| 特质 | 无 | **TRAIT_NAIVE 天真** |
| 口号 | FOR THE SYNDICATE!! | **HAPPY BIRTHDAY!!** |
| 队长头衔 | 无 | 4 种小丑头衔 |
| 增援 | 普通 | 无 TC 版 |
| Uplink | 通用 | **UPLINK_CLOWN_OPS 专属 17 件** |
| 目标 | nuclear | **相同**（martyr_compatible/5s 延迟分配/10 结局全同）|

---

## 九、对战攻略

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

## 十、数值速查表

| 项 | 值 |
|---|---|
| 源码 | clown_ops/ 4 文件 396 行 |
| 类型 | 核弹队子类 |
| 核弹 | 香蕉裂变弹（Stun 1s+全站变小丑+airhorn.ogg）|
| 香蕉剑 | 0 伤 + 滑倒 60（胶鞋无效）|
| Uplink | **专属 17 件**（Dark H.O.N.K. 80TC 最贵）|
| 机甲 | Dark H.O.N.K.（300 耐久/激光 50 抗/气喇叭击退 6 格）|
| 口号 | HAPPY BIRTHDAY!! |
| 特质 | TRAIT_NAIVE |
| 肝脏 | COMEDY_METABOLISM |
| 渗透者 | infiltrator_clown |
| 队长头衔 | 4 种（Head Honker 等）|
| 植入体 | sad_trombone |
| 生成 | weight=0（需强制开启）|
| 基类 | hijack 2/ops.ogg/focused 心情继承 |

---

*本文档数值全部实测自 `code/modules/antagonists/clown_ops/`（4 文件 396 行）+ `code/modules/antagonists/nukeop/`（基类）+ `code/modules/uplink/uplink_items/clownops.dm`（Uplink 17 件）源码，无推测。*
