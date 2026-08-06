# TianGuan13 濒死体验与通灵听觉百科 (Near-Death Experience & Sixth Sense)

> 基于 TianGuan13 NovaSector 分支源码全量整理。机制核：`code/modules/mob/living/carbon/carbon.dm`（濒死授 trait）+ `code/__HELPERS/mobs.dm`（死信广播过滤）+ `code/modules/mob/mob_say.dm`（say_dead）。

## 概述

**你是否曾在濒死时听到死人的对话？** 这不是幻觉——是服务器的**濒死体验（Near-Death Experience）**机制在起作用：当你的血量跌破阈值时，你会获得**第六感（TRAIT_SIXTHSENSE）**，从而能听到**全部死信（deadchat）**——包括死去的玩家、观战中的幽灵，以及死亡咆哮。

**一句话**: 血量 ≤ -90 → 获得第六感 → 能听到全服幽灵对话 → 恢复血量后失效。

---

## 一、核心机制（源码确认）

### 1.1 触发条件（carbon.dm L687-692）

```dm
/mob/living/carbon/set_health(new_value)
	if(CONFIG_GET(flag/near_death_experience))
		if(. > HEALTH_THRESHOLD_NEARDEATH)
			if(health <= HEALTH_THRESHOLD_NEARDEATH && !HAS_TRAIT(src, TRAIT_NODEATH))
				ADD_TRAIT(src, TRAIT_SIXTHSENSE, "near-death")
		else if(health > HEALTH_THRESHOLD_NEARDEATH)
			REMOVE_TRAIT(src, TRAIT_SIXTHSENSE, "near-death")
```

| 条件 | 值 | 说明 |
|---|---|---|
| **血量阈值** | `HEALTH_THRESHOLD_NEARDEATH = -90` | 跌破 -90 触发（combat.dm L92） |
| **配置开关** | `near_death_experience`（flag） | 服务器配置项，默认关闭，开启才生效 |
| **免疫条件** | `TRAIT_NODEATH` | 持有"不会死"特质的角色不触发 |
| **恢复条件** | 血量回升 > -90 | 第六感立即移除 |
| **触发对象** | 碳基生物（/mob/living/carbon） | 人类等有血量的生物 |

### 1.2 听力判定（mobs.dm L384-391）

```dm
if(HAS_TRAIT(M, TRAIT_SIXTHSENSE) && message_type == DEADCHAT_REGULAR)
	override = SEE_DEADCHAT_NORMAL
...
if(M.stat != DEAD && !override)
	continue  // 没死的活人默认跳过——除非有 override
```

**关键**: 活人（`stat != DEAD`）默认被死信广播跳过，但持有第六感时 `override = SEE_DEADCHAT_NORMAL` 让死信送达——**这是濒死活人听到幽灵对话的根本原因**。

### 1.3 广播范围（mobs.dm L369）

```dm
for(var/mob/M in GLOB.player_list)
```

`deadchat_broadcast` 遍历**全服务器玩家列表**，**无距离/区域过滤**——所以你听到的是**全部幽灵对话**（全站），不只是身边的。范围与幽灵"听全站"一致。

### 1.4 死信消息类型

| 类型 | 含义 | 第六感能否听到 |
|---|---|---|
| `DEADCHAT_REGULAR` | 幽灵普通说话（say_dead） | ✅ 能 |
| `DEADCHAT_DEATHRATTLE` | 死亡咆哮（死前遗言） | ✅ 能（受 `DISABLE_DEATHRATTLE` 偏好影响） |
| `DEADCHAT_ARRIVALRATTLE` | 到达咆哮（新玩家进服播报） | ✅ 能（受 `DISABLE_ARRIVALRATTLE` 偏好影响） |
| `DEADCHAT_LAWCHANGE` | AI 法则变更 | ✅ 能（受 `CHAT_GHOSTLAWS` 偏好影响） |

---

## 二、第六感（TRAIT_SIXTHSENSE）的其他来源

除了濒死触发，`TRAIT_SIXTHSENSE` 还有这些授予途径：

| 来源 | 位置 | 说明 |
|---|---|---|
| **濒死体验** | carbon.dm | 血量 ≤ -90（本文主题） |
| **脑创伤（特殊）** | `brain_damage/special.dm` L308 | 某种特殊脑损伤授予（TRAUMA_TRAIT） |
| **巫师天眼** | `antagonists/wizard/equipment/artefact.dm` L204 | 巫师天眼魔法（Scrying Orb，配合 X 光视觉） |
| **塔罗牌** | `cards/deck/tarot.dm` L65 | 塔罗牌抽取授予（MAGIC_TRAIT） |
| **死斗模式** | `deathmatch/deathmatch_modifier.dm` L568 | 死亡竞赛修改器（配合 X 光听觉） |
| **复仇幽灵** | `revenant/_revenant.dm` L102 | 复仇幽灵本体自带 |
| **幽灵咖啡厅**（NOVA） | `ghostcafe/code/ghost_role_spawners.dm` | NOVA 幽灵角色机器人/人类变体授予，脱出时移除 |

---

## 三、死亡咆哮（Deathrattle）彩蛋

死亡咆哮是死信的一种——**死亡瞬间自动发出的遗言**，由植入体触发：

- **位置**: `code/game/objects/items/implants/implant_deathrattle.dm`
- **机制**: 植入 `deathrattle implant`（死亡咆哮植入体）后，死亡时自动向死信广播遗言
- **与濒死听觉的关系**: 濒死玩家（第六感持有者）能听到他人的死亡咆哮

---

## 四、NOVA 扩展

| 扩展 | 位置 | 说明 |
|---|---|---|
| **幽灵咖啡厅第六感** | `ghostcafe/code/ghost_role_spawners.dm` | 幽灵角色机器人（L36）/人类（L65）出生授予第六感，脱出（L105-109）移除——让幽灵角色能保持与死者的交流 |

---

## 五、实战要点（玩家向）

1. **什么时候能听到幽灵**：血量 ≤ -90（约等于濒死/昏迷临界），且服务器开启了 `near_death_experience`
2. **听到的范围**：全服死信（无距离限制）——不仅是身边的幽灵，远处死人也听得到
3. **怎么触发**：被打到残血（失血/受伤/缺氧到 -90 以下）即触发；通常是昏迷倒地状态
4. **怎么失效**：血量回升 > -90 立即失效；死亡后变幽灵（本来就听得到）；有 `TRAIT_NODEATH` 的角色（如某些不死种族）永远触发不了
5. **实用价值**：濒死时幽灵可能给你提示（救援方向/凶手是谁）
6. **注意**：听到的幽灵对话是**全局广播**，幽灵说"去东边走廊"可能是说给别人听的，不一定是给你的提示

---

## 六、一句话总结

**血量 -90 以下 → 第六感 → 全服幽灵对话广播 → 回血失效** 

> 源码路径索引：`carbon.dm`（触发）/ `mobs.dm`（广播过滤）/ `combat.dm`（阈值 -90）/ `game_options.dm`（配置开关）/ `mob_say.dm`（say_dead）/ `implant_deathrattle.dm`（死亡咆哮）/ `ghostcafe`（NOVA 扩展）
