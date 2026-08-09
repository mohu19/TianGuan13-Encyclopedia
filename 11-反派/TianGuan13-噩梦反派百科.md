# TianGuan13 · 噩梦（Nightmare）反派百科

> **数据来源**：`code/modules/antagonists/nightmare/` 全 4 文件共 **355 行**（55 + 86 + 170 + 44），全部数值经源码 grep/read 逐条核对，无推测。
> 涉及联动文件：`code/modules/spells/spell_types/jaunt/shadow_walk.dm`、`code/modules/spells/spell_types/pointed/terrorize.dm`、`code/datums/status_effects/debuffs/terrified.dm`、`code/datums/components/fearful/*`、`code/datums/elements/light_eater.dm`、`code/datums/elements/light_eaten.dm`、`code/modules/surgery/bodyparts/bodypart_effects.dm`、`code/__DEFINES/mobs.dm`、`code/__DEFINES/mood.dm` 等。
> **项目**：TianGuan13（Nova Sector 分支）

---

## 1. 一句话定位

**噩梦（Nightmare）**是暗影生物（Shadowling 后裔）中极具攻击性的亚种——一只只能活在黑暗里的「噬光者」：潜伏于阴影、穿梭暗域、挥舞**噬光之刃**永久熄灭光源、将猎物拉进黑暗用**恐怖化**折磨至疯，甚至能在黑暗中**死而复生**。它是动态中期事件（midround）从幽灵中选拔的**独立入侵者**（Invader），一局至多 1 只。

源码 4 文件分工：

| 文件 | 行数 | 内容 |
|---|---|---|
| `nightmare.dm` | 55 | antag datum + outfit + 目标（fluff objective） |
| `nightmare_equipment.dm` | 86 | 噬光之刃 light eater（含暴击机制） |
| `nightmare_organs.dm` | 170 | 噩梦脑（能力授予/子弹闪避）+ 黑暗之心（复活/噬光刃） |
| `nightmare_species.dm` | 44 | 暗影/噩梦亚种种族定义 |

---

## 2. 核心机制：暗影生物怎么运作

### 2.1 光阈值（一切机制的基准）

```dm
#define SHADOW_SPECIES_LIGHT_THRESHOLD 0.2   // code/__DEFINES/mobs.dm:509
```

**亮度 `lumcount < 0.2` = 安全黑暗；`≥ 0.2` = 危险光明。** 所有能力（脑部判定、暗影行走、恐怖化、复活）都以此阈值判定。

### 2.2 光照下的生死循环（身体部位效果 nyxosynthesis）

噩梦身体由暗影肢体构成（`BODYTYPE_ORGANIC | BODYTYPE_SHADOW`），每个肢体挂载 `/datum/status_effect/grouped/bodypart_effect/nyxosynthesis`（每 1 秒 tick 一次）：

| 环境 | 效果（每肢体每秒，乘肢体系数） |
|---|---|
| 光 `lumcount ≥ 0.2` | **受 1 点钝击 + 1 点灼伤**（`take_overall_damage`，限 BODYTYPE_SHADOW） |
| 暗 `lumcount < 0.2` | **回复 0.5 钝击 + 0.5 灼伤**（BODYTYPE_SHADOW） |

- 暗影肢体还有 **`burn_modifier = 1.5`**（受灼伤 +50%）。
- 非噩梦的普通暗影人在黑暗中会挂 `/datum/status_effect/shadow`（2.2 秒刷新）并弹「shadow regeneration」提示；**噩梦不弹这个重复提示**（bodypart_effects.dm:138 专门排除 `SPECIES_NIGHTMARE`），噩梦的提示由脑部另行处理（见 2.4）。
- 被闪光弹/闪光武器直击：噩梦继承暗影种族 `on_flashed`——满偏差命中 **16 灼伤 + 最多 6 秒混乱**；非满偏差 **8 灼伤 + 最多 3 秒混乱**，并强制尖叫（`shadow_wail.ogg`）。

### 2.3 光即是死亡，暗即是重生

- **白天/强光下裸奔**：每秒全身肢体累积灼伤+钝伤（见 2.2），无减伤（`no_equip_flags` 禁穿衣物/护甲/鞋/手套/面罩，见 §5），所以噩梦必须靠**关灯**生存。
- **黑暗中死亡 → 复活**：黑暗之心（heart of darkness）的 `on_death` 机制（详见 §3.5）——只要尸体躺在黑暗中，复活进度以每秒 1 点累积，**累计满 80 秒**即满血复活。
- 结论：**噩梦的整局游戏 = 把站内所有灯永久关掉**（噬光之刃），制造属于它的黑暗主场。

### 2.4 黑暗中的增益：Lightless Domain（无光领域）

噩梦脑（`/obj/item/organ/brain/shadow/nightmare`）每生命 tick 检查所在格：**`lumcount < 0.2` 时施加 `/datum/status_effect/shadow/nightmare`**（id = "nightmare"，持续 2.2 秒、刷新型）：

- 效果期间注册 `COMSIG_ATOM_PRE_BULLET_ACT`：**子弹命中时判定为穿透（COMPONENT_BULLET_PIERCED），子弹从身体穿过去不造成伤害**，播放 `bulletflyby.ogg`（音量 75）。即**黑暗中完全闪避枪弹**。
- 屏幕提示为「**Lightless Domain**」："Bathed in soothing darkness you will slowly regenerate, even past the point of death. Heightened reflexes will allow you to dodge projectile weapons."（沐浴黑暗缓慢再生，甚至死后亦然；反射神经强化可闪避投射武器）。

> 注：真正的持续回血来自暗影肢体 nyxosynthesis（2.2）与暗影行走虚体（3.1），而非此状态本身。

---

## 3. 能力全录

> 噩梦全部主动/被动能力（含授予条件与数值）。

### 3.1 暗影行走 Shadow Walk（主动，无冷却）

来源：`shadow_walk.dm`（`/datum/action/cooldown/spell/jaunt/shadow_walk`，噩梦脑植入即授予，**任何持有噩梦脑者都能用**）

| 项目 | 数值 |
|---|---|
| 冷却 | **0**（不覆盖基类 `cooldown_time = 0`，可无限连发，受光条件限制） |
| 施放条件 | 所在格 `lumcount < 0.2` 才能进入；在光中施放会被提示「太亮了」并拒绝 |
| 进入效果 | `nightmare_poof.ogg`（音量 50）+ 醒目警告；清除一切行动不能与体力损失（`SetAllImmobility(0)`、`set_stamina_loss(0)`） |
| 虚体形态 | `obj/effect/dummy/phased_mob/shadow`（"shadows"，紫色激光图标），穿墙移动 |
| 虚体回血 | 每 SSobj tick（每秒）**回复 1.5 钝击 + 1.5 灼伤**（`healing_rate = 1.5`，仅有机体 BODYTYPE_ORGANIC） |
| 虚体光限制 | 进入 `lumcount > 0.2` 的格子即被**强制弹出**（`nightmare_reappear.ogg` 音量 50 + 醒目警告） |
| 移动进光警告 | 首次尝试进光格弹气球提示并拦截；警告冷却 **0.75 秒**，1 秒后重新武装；无视警告再闯 → 强制弹出 |
| 太空限制 | 虚体无法进入太空格（提示并拦截） |
| 切换 | 已在虚体中再按 = 主动退出 |

**联动暴击**：进入虚体（`COMSIG_MOB_ENTER_JAUNT`）启动 **7 秒**暴击计时器；退出虚体（`COMSIG_MOB_AFTER_EXIT_JAUNT`）取消（仅限真噩梦 antag 持有者，见 3.4）。

### 3.2 恐怖化 Terrorize（主动，25 秒冷却）

来源：`terrorize.dm`（`/datum/action/cooldown/spell/pointed/terrorize`）——**仅授予拥有 `/datum/antagonist/nightmare` 的「真噩梦」**（噩梦脑插入时检查 mind，nightmare_organs.dm:29-31）

| 项目 | 数值 |
|---|---|
| 冷却 | **25 秒** |
| 射程 | **9 格**（`cast_range = 9`） |
| 魔法抗性 | `MAGIC_RESISTANCE_MIND`（心智类抗魔可挡） |
| 目标要求 | 仅限人类（human）；目标周围 1 格内**暗格数必须 ≥ 亮格数**（亮 = `lumcount > 0.2`），否则提示失败——**站在灯下通常免疫此技能** |
| 效果 | 施加 `/datum/status_effect/terrified`：目标**尖叫 + 立即获得 100 点恐怖值**，并激活暗恐处理器（`nyctophobia/terrified`） |
| 叠加 | 再次施放刷新效果，**每次 +135 点恐怖值**（`STACK_TERROR_AMOUNT`），恐怖值上限 **1000**（`TERROR_BUILDUP_MAXIMUM`） |
| 恐怖积累 | 目标身处黑暗时恐怖值以 **15/秒** 上升（`buildup_per_second = 15`）；**护目镜/夜视无法抵消**（`meson_negated = FALSE`，区别于普通恐黑怪癖版） |
| 恐慌阈值 | 恐怖值 > **500**（`TERROR_BUILDUP_PANIC`）触发恐慌级效果（恐惧组件 FEAR 系：概率性恐慌行为、尖叫等） |
| 附加 | 恐怖激活时叠加心情事件「nyctophobia」（**-3 心情**，MOOD_EVENT_FEAR）；技能描述提示：**对惊恐目标空手扇巴掌可惊吓并使其晕头转向**（fearful 组件的 `COMSIG_CARBON_PRE_MISC_HELP` 实现） |
| 免疫 | 暗影/噩梦种族自身不受恐黑影响（fearful sources 明确排除 `SPECIES_SHADOW, SPECIES_NIGHTMARE`） |

### 3.3 噬光之刃 Light Eater（被动武器，心植入即给）

来源：`nightmare_equipment.dm`（`/obj/item/light_eater`）——黑暗之心插入宿主时**自动放入手中**（`put_in_hands`）；非噩梦脑移除时销毁。

| 项目 | 数值 |
|---|---|
| 伤害 force | **25** |
| 穿甲 armour_penetration | **35** |
| 伤口加成 | `wound_bonus = -30` / `exposed_wound_bonus = 20` |
| 体积/锋利 | `WEIGHT_CLASS_HUGE`、`SHARP_EDGED` |
| 工具行为 | `TOOL_MINING`（可当矿镐挖矿） |
| 组件 | 附带屠宰组件：**屠宰速度 8 秒、效率 70**（尸体可当场肢解） |
| 抗性 | `INDESTRUCTIBLE | ACID_PROOF | FIRE_PROOF | LAVA_PROOF | UNACIDABLE`（不可摧毁/免疫酸火岩浆） |
| 物品旗标 | `ABSTRACT | DROPDEL`（抽象物品、掉落即删）+ `TRAIT_NODROP`（不可主动丢弃/换手） |
| 命中音 | `bladeslice.ogg` |

**核心能力——吞噬光线**（`/datum/component/light_eater` + `/datum/element/light_eater` 联动）：

- 用刃**击打/投掷命中/格挡**任何光源（灯具、枪、电池、照明弹、发光物……）→ **永久「吃掉」其发光能力**（挂 `light_eaten` 元素：封禁 set_light_power/range/on，光源不再发光）。
- 被吃的光源**无法自行恢复**，直到该物品被摧毁/更换（元素随宿主销毁脱离）。
- 空手攻击被吃光物体时无法用光（`light_power ≤ 0` 或 `light_range ≤ 0` 或 `light_on = FALSE` 不触发）；太空/岩浆地表、透明地块**不可吃**。
- 打人时若对方正拖着一个发光物，连带吃掉（防「人肉灯架」）；命中带 `CAN_BE_HIT` 的物品会做伪攻击动画。
- 被吃光的物体被检查时会弹警告文本，且有 **20% 概率**让检查者**短暂致盲 10 秒 + 视线模糊 20 秒**（light_eaten 彩蛋）。

**暴击机制（Crit）**（仅真噩梦 antag 持有者装备时注册信号）：

| 阶段 | 数值/效果 |
|---|---|
| 触发 | 进入暗影行走后 **7 秒**计时（`addtimer`，退出虚体即取消） |
| 就绪 | `has_crit = TRUE`：刀刃套**红色描边滤镜**（COLOR_CARP_RIFT_RED，size 5）+ 持刀者气球提示 |
| 命中消耗 | 一击后清除（`remove_crit`） |
| 对碳基（非绿巨人） | 公告 + **瘫痪 1 秒**（`Paralyze(1 SECONDS)`） |
| 对硅基（非绿巨人） | 公告 + **瘫痪 2 秒**（`Paralyze(2 SECONDS)`） |
| 对绿巨人/其他 | 无瘫痪，改为**强制 25 点伤害**（`apply_damage(force, forced = TRUE)`，无视抗性） |
| 音效 | `crackandbleed.ogg`（音量 100） |

### 3.4 能力授予逻辑（谁拿到什么）

| 持有者 | 暗影行走 | 恐怖化 | 暴击联动 |
|---|---|---|---|
| 噩梦 antag（种族=nightmare） | ✅ | ✅ | ✅ |
| 移植了噩梦脑的普通人 | ✅（脑 `on_mob_insert` 即 Grant） | ❌（需 mind 有 nightmare datum） | ❌（equipped 需 mind 有 nightmare datum） |
| 移除噩梦脑 | 全部 QDEL 回收 | 全部 QDEL 回收 | — |

### 3.5 黑暗之心 Heart of Darkness（复活引擎）

来源：`nightmare_organs.dm`（`/obj/item/organ/heart/nightmare`）

| 项目 | 数值/行为 |
|---|---|
| 名称/描述 | "heart of darkness"——「暴露在光下会扭曲蠕动的外星器官」 |
| 外观 | 可视化器官（`visual = TRUE`），图标 `dark_heart-on` |
| 心跳音 | "the writhing pulses of a fear given form" |
| 心跳停跳 | `Stop()` 恒返回 FALSE——**心脏永不骤停**（不会心脏停跳致死） |
| 腐败 | `decay_factor = 0`——**尸体永不腐烂** |
| 进食 | `food_reagents = nutriment/organ_tissue = 5`：**可吃器官组织补营养** |
| 特质 | `organ_traits = TRAIT_LIGHT_DRINKER`——附带「Light Drinker」怪癖（酒精极易上头），源码注释："In case you want to drink light as well as eat it"（彩蛋） |
| 佩戴武器 | 插入时生成一把**噬光之刃**塞进宿主双手；移除时销毁（`QDEL_NULL(blade)`） |
| 自插 | 用心脏攻击自己 = 当场吞下植入自己体内（`demon_consume.ogg` 音量 50）——**噩梦手术摘心可被夺回自插** |
| 可用性 | `get_availability`：仅 `isnightmare` 返回 TRUE（正常流程只出现在噩梦身上） |

**复活流程（on_death）**：

1. 宿主死亡后，每生命 tick 检查尸体所在格：`lumcount < 0.2` → `respawn_progress += 1 秒`，并播放 **`singlebeat.ogg`（音量 40）**（黑暗中每 tick 一声心跳）。
2. `respawn_progress ≥ 80 秒`（`HEART_RESPAWN_THRESHHOLD`）→ **复活**：`revive(HEAL_ALL & ~HEAL_REFRESH_ORGANS)`（全愈但不刷新器官）。
3. 复活后若宿主物种**既非 shadow 也非 nightmare** → 强制改造成**普通暗影人**（shadow species），以 `HEART_SPECIAL_SHADOWIFY` 标志重插心脏（**不再生成新噬光刃**），播放 `ghost.ogg` + `far_noise.ogg`（音量 50）+ 醒目提示。
4. 复活进度归零。**被拉出黑暗/光照下死亡 = 无法复活**（进度不涨）。
5. 移除心脏时 `respawn_progress = 0`。

> 源码宏定义：`#define HEART_RESPAWN_THRESHHOLD (80 SECONDS)`（注意代码拼写 THRESHHOLD 双 H）；`#define HEART_SPECIAL_SHADOWIFY 2`（定义处注释称"appears to be unused"，但实际用于复活改造路径——注释已过时）。

---

## 4. 核心属性：antag datum 全属性

来源：`nightmare.dm`（/datum/antagonist/nightmare）+ 基类 `/datum/antagonist` 默认值（`code/modules/antagonists/_common/antag_datum.dm`）。

| 字段 | 值 | 来源 |
|---|---|---|
| `name` | "Nightmare"（improper） | nightmare.dm:2 |
| `antagpanel_category` | **"Extradimensional Abominations"**（`ANTAG_GROUP_ABOMINATIONS`，antagonists.dm:421） | nightmare.dm:3 |
| `pref_flag` | `ROLE_NIGHTMARE`（="Nightmare"） | nightmare.dm:4 / role_preferences.dm:36 |
| `show_in_antagpanel` | FALSE（内部类型，admin 面板不直接添加） | nightmare.dm:5 |
| `show_name_in_check_antagonists` | TRUE | nightmare.dm:6 |
| `show_to_ghosts` | TRUE（幽灵可见其 antag 身份） | nightmare.dm:7 |
| `ui_name` | "AntagInfoNightmare" | nightmare.dm:8 |
| `suicide_cry` | **"FOR THE DARKNESS!!"**（C4/自杀冲锋吼） | nightmare.dm:9 |
| `preview_outfit` | `/datum/outfit/nightmare`（预览仅改种族为 nightmare） | nightmare.dm:10 |
| `roundend_category` | **未设置 → 基类默认 "other antagonists"** | 基类 antag_datum.dm:11 |
| `show_in_roundend` | 未设置 → TRUE（进结算报告） | 基类:13 |
| `antag_moodlet` | **不存在**（基类默认 null，未设置） | 基类:29 |
| `antag_hud_name` | **不存在**（基类默认 null，未设置 → 无专属 HUD 图标名） | 基类:37 |
| `hud_icon` | 未设置 → 默认 `'icons/mob/huds/antag_hud.dmi'` | 基类:35 |
| `stinger_sound` | **不存在**（基类默认 null，获得时无音频 stinger） | 基类:60 |
| `hijack_speed` | **未设置 → 0**（无劫船速度加成） | 基类:33 |
| `can_elimination_hijack` | 未设置 → `ELIMINATION_NEUTRAL` | 基类:31 |
| `hardcore_random_bonus` | **未设置 → FALSE**（无硬核随机通关奖励） | 基类:58 |
| `jobban_flag` | 未设置 → 回退用 `pref_flag` | 基类:19 |
| `replace_banned` | 未设置 → TRUE | 基类:23 |
| `antag_flags` | 未设置 → NONE | 基类:52 |
| `can_assign_self_objectives` | 未设置 → FALSE | 基类:54 |
| `desensitized_modifier` | 未设置 → 1.0 | 基类:62 |
| `silent` | 未设置 → FALSE | 基类:17 |

**心情钩子（唯一 mood 干预）**：`apply_innate_effects` 将 `mob_mood.mood_modifier -= 1`（获得角色时全心情事件 -1 修正），`remove_innate_effects` 恢复 +1（nightmare.dm:20-26）。这是对「antag_moodlet 字段不存在」的直接替代实现。

**登场钩子**：`on_gain()` → `forge_objectives()`（锻造目标）→ 基类；`greet()` 额外 `owner.announce_objectives()`（广播目标）。

**装备预览**：`/datum/outfit/nightmare/post_equip` 直接 `set_species(/datum/species/shadow/nightmare)`。

### 4.1 种族属性（/datum/species/shadow/nightmare）

| 项 | 值 |
|---|---|
| id | `SPECIES_NIGHTMARE`（="nightmare"，mobs.dm:246） |
| 来源 | shadow 亚种（继承：暗影眼、FACTION_FAITHLESS 阵营、暗影语言、尖叫声、闪光惩罚等） |
| changesource_flags | `MIRROR_BADMIN | WABBAJACK | MIRROR_PRIDE`（无 MIRROR_MAGIC，比普通暗影少一个来源） |
| no_equip_flags | 面罩/外套/手套/鞋/内衬/套装位 6 个槽位**全禁**（无衣物护甲） |
| inherent_traits（14 条） | `TRAIT_NO_UNDERWEAR`、`TRAIT_RESISTCOLD`、`TRAIT_NOBREATH`、`TRAIT_RESISTHIGHPRESSURE`、`TRAIT_RESISTLOWPRESSURE`、`TRAIT_RADIMMUNE`、`TRAIT_VIRUSIMMUNE`、`TRAIT_PIERCEIMMUNE`、`TRAIT_NODISMEMBER`（×2，源码重复列出）、`TRAIT_NOHUNGER`、`TRAIT_NOBLOOD`、`TRAIT_NO_DNA_COPY`、`TRAIT_NEVER_WOUNDED` |
| mutantheart | `/obj/item/organ/heart/nightmare`（黑暗之心） |
| mutantbrain | `/obj/item/organ/brain/shadow/nightmare`（噩梦脑） |
| bodypart_overrides | 双臂 = 噩梦暗影臂（`TRAIT_CHUNKYFINGERS` 粗手指：**无法精细操作** + nyxosynthesis）；头/腿/胸 = 普通暗影肢体 |
| 改名 | 种族获得时强制随机改名为 `GLOB.nightmare_names` 之一 |
| 开局资格 | `check_roundstart_eligible()` **恒 FALSE**（只能中期/管理生成） |

**噩梦名字池**（`strings/names/nightmare.txt`，10 个）：Amerziox / Gyrg-mylin / Hel-uae / Kanet'pruunance / Mii`mahza / Nex / Noaey'gief / U'ruan / Vigistaezian / Y`shej

### 4.2 生成途径

| 途径 | 详情 |
|---|---|
| **动态中期规则** `/datum/dynamic_ruleset/midround/from_ghosts/nightmare` | 类型 `LIGHT_MIDROUND`、`RULESET_INVADER`；**权重 5**、**最低人口 15**、**单局上限 1**；报名预览物 = 噬光之刃；生成点要求**维生+黑暗的维护通道**（`find_maintenance_spawn(atmos_sensitive=TRUE, require_darkness=TRUE)`）；流程：加 antag datum → 改种族 → forceMove 到维护通道 → `ethereal_exit.ogg`（音量 50） |
| **Admin 秘密菜单** | `secrets.dm` 可从 ROLE_NIGHTMARE 直接生成（ROLE_TRAITOR/CHANGELING/HERETIC/CULTIST/NINJA/WIZARD/NIGHTMARE 同列） |
| **反恶梦宗教仪式** | `banish_nightmare.dm`：梦境宗教可用驱除仪式针对 nightmare（`isnightmare` 判定） |
| 角色槽 | `/datum/job/nightmare`（title = ROLE_NIGHTMARE，供偏好设置展示，非可玩职位） |

---

## 5. 目标系统

**单目标**：1 个 `nightmare_fluff` 目标，`on_gain()` → `forge_objectives()` 锻造（nightmare.dm:52-55）。

**文本池（7 选 1，`pick()` 随机）**：

1. "Consume the last glimmer of light from the space station."（吞尽空间站最后一缕光）
2. "Bring judgment upon the daywalkers."（审判白昼行者）
3. "Extinguish the flame of this hellscape."（熄灭这地狱的火焰）
4. "Reveal the true nature of the shadows."（揭示暗影的真面目）
5. "From the shadows, all shall perish."（暗影之中，万物终焉）
6. "Conjure nightfall by blade or by flame."（以刃或火召来夜幕）
7. "Bring the darkness to the light."（把黑暗带给光明）

**完成判定**（`check_completion()`）：**`owner.current && owner.current.stat != DEAD`** —— 即**结算时活着就算目标达成**（绿字）。没有击杀/关灯数量等硬指标，本质是「活到终局」型目标。

**特性**：无 `can_assign_self_objectives`（不能自设目标）；目标在 greeter 阶段广播给本人。

---

## 6. 风味彩蛋

| 类型 | 内容 |
|---|---|
| 出场音效 | 生成：`sound/effects/magic/ethereal_exit.ogg`；暗影行走进入 `nightmare_poof.ogg`、退出/被弹出 `nightmare_reappear.ogg`；子弹闪避 `bulletflyby.ogg`（75 音量） |
| 复活音效 | 黑暗中死亡进度心跳 **`singlebeat.ogg`（40 音量）**；复活成功 `far_noise.ogg`（50）；强制改造暗影人 `ghost.ogg` |
| 心脏音效 | 自插心脏 `sound/effects/magic/demon_consume.ogg`（50）；刀刃暴击 `crackandbleed.ogg`（100）；挥砍 `bladeslice.ogg` |
| 心脏彩蛋 | 心跳声文案 "the writhing pulses of a fear given form"（恶趣味注释 "evil schmeevil"）；`food_reagents` 吃器官组织；`TRAIT_LIGHT_DRINKER` 酒精怪癖（"In case you want to drink light as well as eat it"——想"喝"光也可以） |
| 器官描述 | 噩梦脑 = "tumorous mass"（肿瘤块，shade_color "black, somehow"）；普通暗影脑 = shadowling tumor |
| 眼 | 继承「burning red eyes」：凝视即恐惧（color_cutoffs 20/10/40，辣椒水防护，闪光敏感） |
| 检查被吃光物体 | 20% 概率检查者被致盲 10 秒 + 眼糊 20 秒（"它看起来……很不对劲"） |
| 被吃光的灯 | 检查文本提示灯已死 |
| 暴击就绪 | 刀刃红色描边 + 气球提示（对手可见警告） |
| 自杀语 | **"FOR THE DARKNESS!!"** |
| 心情 | 恐黑受害者：nyctophobia 心情事件 **-3**（MOOD_EVENT_FEAR）；噩梦自身 mood_modifier **-1**（一切心情更差，暗影的执念） |
| 名字池 | 10 个洛夫克拉夫特式异形名（见 §4.1） |

---

## 7. NOVA 专属改动

`grep -r "NOVA EDIT" code/modules/antagonists/nightmare/` 结果（**2 处**，均为 I18N 本地化改造，无机制改动）：

| 文件 | 行 | 内容 |
|---|---|---|
| `nightmare_equipment.dm` | 1 | `// NOVA EDIT - I18N CODEMOD - 玩家可见字符串已改写为 LANG()；请勿手改 key，见 modular_nova/modules/i18n/readme.md` |
| `nightmare_organs.dm` | 1 | 同上 |

- 即：Nova 分支对噩梦模块只做了**玩家可见字符串 → LANG() 键**的本地化改写（键如 `obj.97d3c43d`、`datum.252328cf`、`obj.664a40e4` 等），**所有数值/机制为上游原样**。
- 联动文件中的 NOVA 标记：`shadow_walk.dm`、`terrorize.dm`、`terrified.dm`、`light_eater.dm`（element）、`shadowpeople.dm`（含一处 `// NOVA EDIT: Fixing Shadowpeople` 修复 `examine_limb_id = SPECIES_SHADOW`）均为同类 I18N/修复性改动。
- `modular_nova/` 下 **0 处** nightmare 相关覆盖（grep 无结果）——确认无模块化二次修改。

---

## 8. 数值速查表

| 数值 | 值 | 出处 |
|---|---|---|
| 光阈值 SHADOW_SPECIES_LIGHT_THRESHOLD | 0.2 | mobs.dm:509 |
| 光照下暗影肢体伤害 | 1 钝 + 1 灼/肢体/秒 | bodypart_effects.dm:130-131 |
| 黑暗中暗影肢体回复 | 0.5 钝 + 0.5 灼/肢体/秒 | bodypart_effects.dm:135 |
| 暗影肢体 burn_modifier | 1.5 | misc_bodyparts.dm:352 |
| 闪光满命中/非满命中 | 16 / 8 灼伤 | shadowpeople.dm:122,125 |
| 闪光混乱 | 满 3-6 秒 / 非满 1-3 秒 | shadowpeople.dm:123,126 |
| 暗影行走虚体回血 | 1.5 钝 + 1.5 灼/秒 | shadow_walk.dm:61,86 |
| 暗影行走冷却 | 0（不覆盖） | cooldown_action.dm:11 |
| 进光警告冷却 / 重武装 | 0.75 秒 / 1 秒 | shadow_walk.dm:140-141 |
| 暴击计时 | 7 秒 | nightmare_equipment.dm:68 |
| 暴击瘫痪（碳/硅） | 1 秒 / 2 秒 | nightmare_equipment.dm:61 |
| 暴击强制伤害（绿巨人等） | 25（=force） | nightmare_equipment.dm:64 |
| 噬光之刃 force / 穿甲 | 25 / 35 | nightmare_equipment.dm:11-12 |
| 噬光之刃 wound/exposed | -30 / +20 | nightmare_equipment.dm:21-22 |
| 屠宰速度 / 效率 | 8 秒 / 70 | nightmare_equipment.dm:32-33 |
| 恐怖化冷却 / 射程 | 25 秒 / 9 格 | terrorize.dm:12-13 |
| 首击恐怖值 / 叠加 | 100 / +135 | terrified.dm:3,5 |
| 恐怖积累速度 | 15/秒（暗恐 terrified 版） | _sources.dm:78 |
| 恐怖上限 / 恐慌阈值 | 1000 / 500 | mood.dm:72,66 |
| 恐黑心情 | -3（MOOD_EVENT_FEAR） | generic_negative_events.dm:146-149 |
| 复活所需黑暗时间 | 80 秒 | nightmare_organs.dm:3 |
| 复活心跳音间隔 | 每生命 tick（1 秒） | nightmare_organs.dm:148 |
| 心脏 organ_tissue 进食量 | 5 | nightmare_organs.dm:89 |
| 心脏 decay_factor | 0 | nightmare_organs.dm:87 |
| 心脏 Stop() | 永不骤停 | nightmare_organs.dm:132-133 |
| 心情 mood_modifier | -1 | nightmare.dm:22 |
| 中期规则权重 / 最低人口 / 上限 | 5 / 15 / 1 | dynamic_ruleset_midround.dm:526-528 |
| 目标池文本数 | 7 | nightmare.dm:37-45 |
| 噩梦名池 | 10 | strings/names/nightmare.txt |
| no_equip_flags 槽位数 | 6 | nightmare_species.dm:9 |
| inherent_traits 条数 | 14（NODISMEMBER 重复计） | nightmare_species.dm:10-25 |

---

## 9. 审计说明

- 全部数值取自源码 grep/read 原文，未做任何推算补全。
- 自动审计脚本 `audit_nightmare.py` 对照源码逐项断言 ≥20 个数值声明（见审计输出）。
- 已知源码瑕疵（照实记录）：`HEART_RESPAWN_THRESHHOLD` 拼写（双 H）为上游原样；`HEART_SPECIAL_SHADOWIFY` 的 "appears to be unused" 注释与实际使用不符；`inherent_traits` 中 `TRAIT_NODISMEMBER` 重复出现两次（源码如此）；`TRAIT_LIGHT_DRINKER` 在本分支实为「酒精易醉」怪癖特质而非"喝光"（源码注释是玩笑）。

---
*生成：Hermes 子代理 · TianGuan13 反派百科系列（叛徒篇标准）· 源码 355 行全量提取*
