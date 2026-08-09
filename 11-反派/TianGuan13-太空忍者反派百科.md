# TianGuan13 · 太空忍者（Space Ninja）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/space_ninja/`（3 文件 **173 行**：space_ninja 133 + outfit 40 + equipment 3 文件）
> **类型**: 单人潜行刺客型反派｜**难度**: ★★★★（潜行+高科技装备碾压）
> **一句话**: 你是**蜘蛛忍者家族**的精英雇佣兵——用忍者手套**黑入**空间站（劫持机械体/黑门/扰乱安保），目标：**不留痕迹地摧毁关键设施**。
> **名言**: "Surprise is my weapon. Shadows are my armor."（出其不意是我的武器，阴影是我的盔甲。）

---

## 目录

- [一、核心机制](#一核心机制)
- [二、目标系统（7 目标全录）](#二目标系统7-目标全录)
- [三、装备全录](#三装备全录)
- [四、能力详解](#四能力详解)
- [五、核心属性](#五核心属性)
- [六、风味与记忆](#六风味与记忆)
- [七、对战攻略](#七对战攻略)
- [八、数值速查表](#八数值速查表)

---

## 一、核心机制

**忍者**（`/datum/antagonist/ninja`）是潜行黑入型反派：不靠正面火力，靠**忍者手套的黑入能力**完成目标——从机械体劫持到门劫持到安保扰乱，全是"用手套摸一下"的潜行流。

**核心设计**：
- **黑入手套**：核心能力——摸机器/门/机械体完成劫持
- **潜行定位**：freerunning+light_step 特质（跑酷+轻步），阴影是盔甲
- **7 目标全录**：机械体劫持/科研破坏/门劫持/炸弹安放/安保扰乱/恐怖消息/存活
- **蜘蛛 C4**：限定区域爆炸（只在你目标区域有效）
- **能量武士刀**：30 伤+传送斩击（"teleports behind you"）

**核心循环**:
```
① 出生太空 → 找空间站入口
② 用忍者手套黑入：机械体/门/控制台
③ 完成目标（机械体劫持→科研破坏→门劫持 15-40 扇→炸弹安放）
④ 扰乱安保（全站 arrest 标记）+ 恐怖消息
⑤ 存活到回合结束
```

---

## 二、目标系统（7 目标全录）

**代码**: `space_ninja.dm` L40-102（addObjectives）

| # | 目标 | 说明 | 判定 |
|---|---|---|---|
| 1 | **机械体劫持 Cyborg Hijack** | "用手套转化至少 1 个机械体协助破坏" | 手套 DrainAct 转化机械体 |
| 2 | **科研破坏 Research Secrets** | 用手套摸 R&D 服务器破坏科研 | 手套摸 R&D 服务器（AI 会被警示！）|
| 3 | **门劫持 Door Jack** | 用手套黑开 **15-40 扇**气闸（随机）| 黑完最后一扇 |
| 4 | **炸弹安放 Plant Explosive** | 在指定区域引爆起始炸弹（**只在那区域有效**）| 炸弹启动（prepriming）|
| 5 | **安保扰乱 Security Scramble** | 摸安保控制台 → 全站设为逮捕 | 摸安保控制台（AI 会被警示！）|
| 6 | **恐怖消息 Terror Message** | 摸通信控制台 → 引来另一个威胁 | 摸通信控制台（AI 会被警示！）|
| 7 | **存活 Survival** | 存活到回合结束 | 回合结束存活 |

> **注意**：3 个目标（科研/安保/消息）都会**警示 AI**——黑入会暴露你。

---

## 三、装备全录

**代码**: `outfit.dm`（40 行）+ `equipment/`（3 文件）

### 3.1 忍者套装（outfit/ninja）

| 槽位 | 装备 |
|---|---|
| 制服 | 辛迪加忍者制服 |
| 眼镜 | 夜视镜 |
| 面罩 | 忍者气体面罩 |
| 耳朵 | 耳机 |
| 鞋 | 战斗靴 |
| 外套挂 | 忍者刀鞘 |
| 左口袋 | **蜘蛛 C4**（限定区域炸弹）|
| 右口袋 | 应急氧气罐 |
| 背部 | **忍者 MOD 服**（pre_equipped/ninja）|
| 植入体 | 爆炸植入体 |
| 变体 | 等离子人版（Plasmaman）|

### 3.2 能量武士刀（energy_katana.dm）

| 项 | 值 |
|---|---|
| 名称 | energy katana（能量武士刀）|
| **伤害** | **force 30 / throwforce 30** |
| 特殊 | 传送斩击系统（jaunt）——**"*teleports behind you*"** 台词 |
| 获取 | 忍者专属（MOD 服武器召回模块）|

### 3.3 蜘蛛 C4（ninja_explosive.dm）

| 项 | 值 |
|---|---|
| 名称 | **spider charge**（蜘蛛炸药）|
| 描述 | "巨大爆炸力，但**只在特定区域有效**" |
| 机制 | set_detonation_area（绑定目标区域，其他地方哑弹）|
| 位置 | 左口袋起始携带 |

### 3.4 忍者服装（ninja_clothing.dm）

| 装备 | 说明 |
|---|---|
| ninja mask 忍者面罩 | 气体面罩 |
| ninja suit 忍者服 | 制服 |

---

## 四、能力详解

### 4.1 黑入手套（核心）

- 摸机器/门/机械体 → 劫持/破坏
- 机械体转化（DrainAct）：把机械体变成你的盟友
- 门劫持：黑开气闸（目标 15-40 扇）

### 4.2 潜行特质

| 特质 | 说明 |
|---|---|
| **freerunning 跑酷** | 翻越障碍更快 |
| **light_step 轻步** | 行走无声（更难被发现）|
| 夜视镜 | 黑暗中视物 |
| 忍者 MOD 服 | 潜行+防护 |

### 4.3 重生

- on_respawn：重新装备 + 传送到太空 spawn 点

---

## 五、核心属性

**代码**: `space_ninja.dm` L2-18

| 属性 | 值 |
|---|---|
| antagpanel_category | ANTAG_GROUP_NINJAS |
| pref_flag | ROLE_NINJA |
| HUD | ninja（忍者 HUD）|
| **劫机速度** | **1**（hijack_speed）|
| 显示名 | show_name_in_check_antagonists = TRUE |
| 幽灵可见 | show_to_ghosts = TRUE |
| 心情 | **focused**（聚焦）|
| **自杀口号** | **"FOR THE SPIDER CLAN!!"**（为了蜘蛛忍者家族！）|
| 预览套装 | /datum/outfit/ninja_preview |
| 可自选目标 | can_assign_self_objectives = TRUE |
| 界面 | AntagInfoNinja |
| 默认目标 | "Destroy vital station infrastructure, without being seen."（不留痕迹地摧毁关键设施）|
| 脱敏修正 | DESENSITIZED_THRESHOLD |
| 职业 | /datum/job/space_ninja |

---

## 六、风味与记忆

### 6.1 出场音效

| 音效 | 说明 |
|---|---|
| **ninja_greeting.ogg** | 出场音（greet 时播放）|

### 6.2 起始记忆（addMemories）

- "I am an elite mercenary of the mighty Spider Clan. A **SPACE NINJA**!"
- "Surprise is my weapon. Shadows are my armor. Without them, I am nothing."

### 6.3 出生提示

- 出生时提示空间站方向（dir2text 朝向）

---

## 七、对战攻略

### 怎么玩忍者（推荐流程）

```
① 出生太空 → 夜视镜+忍者 MOD 服潜行入站
② 机械体劫持：摸机械体 → 转化盟友（第一目标）
③ 门劫持：黑开气闸（15-40 扇）——顺便移动
④ 科研破坏：摸 R&D 服务器（注意 AI 警示）
⑤ 炸弹安放：去目标区域放蜘蛛 C4
⑥ 安保扰乱 + 恐怖消息（两个都会警示 AI）
⑦ 存活到回合结束
```

### 怎么防忍者（船员对策）

| 方法 | 说明 |
|---|---|
| **盯机械体** | 机械体突然叛变=忍者在附近 |
| **查门** | 大量门被黑开=忍者活动 |
| **护 R&D/安保/通信** | 忍者的 3 个黑入目标（都会警示 AI）|
| **AI 监控** | AI 收到警示后全站搜忍者 |
| **反潜行** | 忍者轻步无声——靠热成像/扫描 |
| **防炸弹** | 蜘蛛 C4 只在指定区域有效——发现即拆 |

---

## 八、数值速查表

| 项 | 值 |
|---|---|
| 源码 | space_ninja/ 3 文件 173 行 |
| 目标 | 7 个（机械体/科研/门 15-40/炸弹/安保/消息/存活）|
| 能量武士刀 | force 30 / throw 30 + 传送斩击 |
| 蜘蛛 C4 | 限定区域爆炸 |
| 劫机速度 | 1 |
| 心情 | focused |
| 口号 | FOR THE SPIDER CLAN!! |
| 出场音 | ninja_greeting.ogg |
| 特质 | freerunning + light_step |
| 职业 | space_ninja |
| 默认目标 | 不留痕迹摧毁关键设施 |

---

*本文档数值全部实测自 `code/modules/antagonists/space_ninja/`（3 文件 173 行）源码，无推测。*
