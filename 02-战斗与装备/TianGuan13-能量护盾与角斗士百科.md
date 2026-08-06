# TianGuan13 能量护盾与角斗士百科

> **项目**: TianGuan13（Nova Sector 分支 → /tg/station）
> **代码**: `modular_nova/modules/energy_shield/`（674 行，2 个 .dm）+ `modular_nova/modules/gladiator/`（790 行，2 个 .dm）
> **范围**: **能量护盾本体**（1 件基础件 + 全机制）· **能量护盾变体全录**（6 种变体）· **角斗士装备**（战利品/披风/狂战士甲/屠龙剑/宝箱）· **巨兽「被标记者」**（The Marked One 四阶段全数据 + 遗骸结构）
> **来源**: 由 `energy_shield` 与 `gladiator` 两模块合并而成（2026-08）

## 目录

- [第一卷 · 能量护盾本体](#第一卷--能量护盾本体)（基础件 + 13 项核心机制 + HUD 16 档）
- [第二卷 · 能量护盾变体全录](#第二卷--能量护盾变体全录)（6 变体：民用/军用/辛迪加/耐力/相位/壁垒）
- [第三卷 · 角斗士装备](#第三卷--角斗士装备)（余烬骨/披风/狂战士甲/屠龙剑/宝箱）
- [第四卷 · 巨兽「被标记者」](#第四卷--巨兽被标记者)（四阶段 Boss 全数值 + 8 攻击技能 + 遗骸）
- [附录 · 代码路径索引](#附录--代码路径索引)

---

# 第一卷 · 能量护盾本体

**代码**: `modular_nova/modules/energy_shield/code/energy_shield.dm`（585 行）

## 1.1 核心常量与定义

**源码**: `energy_shield.dm`（1–12 行）

| 常量 | 值 | 说明 |
|---|---|---|
| `ENERGY_SHIELD_FILTER` | `"energy_shield_tint"` | 护盾轮廓辉光滤镜名 |
| `ENERGY_SHIELD_PATTERN_FILTER` | `"energy_shield_pattern"` | 护盾纹理图案滤镜名 |
| `ENERGY_SHIELD_TRAIT` | `"energy_shield"` | 护盾特质来源 |
| `TRAIT_ENERGY_SHIELDED` | `"energy_shielded"` | 施加给穿戴者的特质，防止叠加多个护盾 |
| `SHIELD_HUD_Y_OFFSET` | `6` | 护盾 HUD 条 Y 偏移，避免与血条重叠 |
| `SHIELD_VISUAL_LINGER` | `1.5 SECONDS` | 受击后滤镜保持可见的时长 |

## 1.2 能量护盾投影仪（Energy Shield Projector）`/obj/item/clothing/accessory/energy_shield`

- **名称**: energy shield projector（能量护盾投影仪）
- **描述**: 一台紧凑的个人能量护盾投影仪，可作配件夹在衣物上，投射出吸收来袭伤害的保护屏障。
- **图标**: `energy_shield.dmi`（`energy_shield`）/ 穿戴 `energy_shield_worn.dmi`（`energy_shield`，`icon_state_is_worn = FALSE`）
- **槽位**: `slot_flags = NONE`、`attachment_slot = NONE`、`above_suit = FALSE`、配件挂点 `action_slots = ITEM_SLOT_ICLOTHING`（内衣物）
- **重量**: `WEIGHT_CLASS_SMALL`（小型）；**抗性**: `FIRE_PROOF`（防火）
- **动作按钮**: `/datum/action/item_action/toggle_energy_shield`（启用/停用开关）

### 基础数值（默认）

| 变量 | 默认值 | 说明 |
|---|---|---|
| `shield_health` | `0` | 当前护盾值——初始为空，装备后需充能 |
| `max_shield_health` | `50` | 护盾上限 |
| `shield_active` | `FALSE` | 护盾是否激活（有充能且已穿戴） |
| `shield_color` | `"#00aaff"` | 护盾特效颜色 |
| `recharge_delay` | `10 SECONDS` | 受击后开始回充的延迟 |
| `recharge_rate` | `5` | 回充期间每秒恢复的护盾值 |
| `max_armor_class` | `10` | 外层衣物护甲超过此值时护盾拒绝激活 |
| `enabled` | `FALSE` | 用户是否启用护盾（动作按钮切换） |
| `emp_retention` | `0` | EMP 后保留的护盾值比例（0=清空，0.5=减半） |
| `blocks_projectiles` | `TRUE` | 是否阻挡物理弹射物伤害 |
| `blocks_melee` | `TRUE` | 是否阻挡物理近战伤害 |
| `blocks_stamina` | `TRUE` | 是否吸收任意来源的耐力伤害 |
| `persistent_visuals` | `FALSE` | 激活期间是否持续显示护盾视觉效果（直至崩溃） |

## 1.3 核心机制

### ① 穿戴与激活（equipped / attach / detach）

- 作为配件挂在内衣物（`ITEM_SLOT_ICLOTHING`）时触发 `equipped()`。
- 穿戴者获得 `TRAIT_ENERGY_SHIELDED`（防叠加），并注册 4 个信号：
  - `COMSIG_MOB_APPLY_DAMAGE_MODIFIERS` → 近战/自伤部分吸收
  - `COMSIG_ATOM_PRE_BULLET_ACT` → 弹射物吸收
  - `COMSIG_MOB_AFTER_APPLY_DAMAGE` → 伤害结算后扣减护盾
  - `COMSIG_LIVING_CHECK_BLOCK` → 近战/投掷完全格挡
- 未启用 → 提示需开启；穿戴重型护甲 → 直接关闭并警告；已有充能 → 立即激活；否则开始回充倒计时。
- 脱下/销毁 → `turn_off(detach_wearer = TRUE)` 清理信号与特质。
- 挂载/取下配件时注册/注销内衣物 `COMSIG_ATOM_EXAMINE`（查看内衣物可见护盾状态）。

### ② 重型护甲限制（wearer_has_heavy_armor）

外层衣物（`ITEM_SLOT_OCLOTHING`）的 **MELEE / LASER / ENERGY / BULLET** 任一护甲值 > `max_armor_class` 即判定为重型护甲 → 护盾拒绝激活/强制关闭（提示"能量护盾与重型护甲不兼容"）。默认上限 10（class II 级以下），各变体可调整。

### ③ 状态检查（examine / on_uniform_examined）

| 状态 | 显示文本 |
|---|---|
| 未启用 | `The energy shield is disabled.`（警告，橙色） |
| 激活中 | `active (X% integrity)`（提示，绿色） |
| 回充中 | `recharging (X% integrity)`（提示，绿色） |
| 无充能 | `The energy shield is offline.`（警告，橙色） |

### ④ EMP 交互（emp_act）

- 护盾值 × `emp_retention`（基础件为 0 → 全清空），重启回充倒计时。
- 归零则触发 `shield_collapse()`，否则刷新 HUD。

### ⑤ 弹射物吸收（on_pre_bullet）——`COMSIG_ATOM_PRE_BULLET_ACT`

- 吸收顺序（按护盾余量依次扣减）：
  1. **主伤害池** `proj.damage`：弹射物伤害类型为 STAMINA 时由 `blocks_stamina` 决定，否则由 `blocks_projectiles` 决定
  2. **次级伤害** `secondary_damage`（脉冲/等离子弹特有）：由 `blocks_projectiles` 决定
  3. **耐力伤害** `proj.stamina`：由 `blocks_stamina` 决定
- **完全吸收**（吸收量 ≥ 总伤害）→ 返回 `COMPONENT_BULLET_BLOCKED`（阻止伤口/嵌入/流血）。
- **部分吸收** → 各伤害池按吸收量扣减后正常结算。
- 受击后进入回充延迟 + 视觉反馈。

### ⑥ 近战/投掷完全格挡（on_check_block）——`COMSIG_LIVING_CHECK_BLOCK`

- 条件：伤害类型对应开关开启、护盾激活、`shield_health >= damage`。
- 满足 → 扣减护盾、返回 `SUCCESSFUL_BLOCK`（天然阻止伤口/嵌入/流血）。
- 受击肢体：取攻击者选中部位，否则随机（胸腔 65% 权重）。
- 耐力伤害由 `blocks_stamina` 决定，其余由 `blocks_melee` 决定。

### ⑦ 近战/自伤部分吸收（on_damage_modifiers + on_after_damage）

- `on_damage_modifiers`（纯函数，无副作用）：弹射物跳过（由 ⑤ 全权处理）；仅吸收**外部攻击**（有 `attack_direction` 或 `attacking_item`；嵌入/伤口/试剂伤害不吸收）。
- 吸收量 = `min(damage, shield_health)`，记入 `pending_absorption`，伤害修正系数 = `max((damage - absorbed) / damage, 0.001)`（保留微小值确保 `apply_damage` 不提前返回）。
- `on_after_damage`：伤害结算后消费 `pending_absorption`，调用 `apply_shield_hit()` 扣护盾 + 视觉/音效反馈。

### ⑧ 受击结算（apply_shield_hit）

1. `shield_health -= absorbed`；重启回充倒计时（`recharge_delay`）；标记回充视觉待显示；关闭回充显示模式
2. 非持续视觉模式 → 启动 `SHIELD_VISUAL_LINGER`（1.5s）滤镜残留，播放受击特效
3. `shield_hit_effect(limb)`：播放 `tap.ogg`、穿戴者闪色（护盾色 0.3s 渐变还原）、在被击肢体位置生成同心涟漪
4. 护盾归零 → `shield_collapse()`；刷新 HUD

### ⑨ 视觉系统（show/hide/update_shield_visuals）

- 轮廓滤镜：`outline_filter(size = 1, color = 带透明度护盾色)`
- 纹理滤镜：`layering_filter(icon = fishscale, color = shield_color, blend_mode = BLEND_INSET_OVERLAY)`（鱼鳞纹理）
- 透明度随护盾值变化：`alpha = (health / max) × 200 + 5`（满值 205，空值 5）

### ⑩ 受击涟漪特效 `/obj/effect/temp_visual/energy_shield_ripple`

- `icon_state = "at_shield2"`、`FLY_LAYER`、`ABOVE_GAME_PLANE`、`duration = 6`、`light_range = 1.5`、`light_power = 0.8`
- 环绕穿戴者公转（orbit），缩放 = `0.35 × current_size × (mob_height / HUMAN_HEIGHT_MEDIUM)`
- 肢体位置偏移：

| 肢体 | off_x | off_y |
|---|---|---|
| 头（BODY_ZONE_HEAD） | 0 | 14 |
| 左臂（L_ARM） | 10 | 2 |
| 右臂（R_ARM） | -10 | 2 |
| 左腿（L_LEG） | 4 | -16 |
| 右腿（R_LEG） | -4 | -16 |

### ⑪ 医疗 HUD 护盾条（update_shield_hud / SHIELD_HUD）

- 复用血条图标状态，按护盾百分比映射 16 档，染护盾色，Y 偏移 +6：

| 百分比区间 | 图标状态 |
|---|---|
| 100 以上 | `health100` |
| 90.625–100 | `health93.75` |
| 84.375–90.625 | `health87.5` |
| 78.125–84.375 | `health81.25` |
| 71.875–78.125 | `health75` |
| 65.625–71.875 | `health68.75` |
| 59.375–65.625 | `health62.5` |
| 53.125–59.375 | `health56.25` |
| 46.875–53.125 | `health50` |
| 40.625–46.875 | `health43.75` |
| 34.375–40.625 | `health37.5` |
| 28.125–34.375 | `health31.25` |
| 21.875–28.125 | `health25` |
| 15.625–21.875 | `health18.75` |
| 9.375–15.625 | `health12.5` |
| 1–9.375 | `health6.25` |
| 0 及以下 | `health0`（或空） |

### ⑫ 崩溃与关闭（shield_collapse / turn_off）

- **崩溃**（护盾归零）：播放 `mech_shield_drop.ogg`、可见消息、3 个火花（`do_sparks`）、隐藏视觉、HUD 清空。
- **关闭**（turn_off）：`enabled = FALSE`、护盾清 0、清除全部瞬态状态、隐藏视觉、注销信号、移除 `TRAIT_ENERGY_SHIELDED`、停止处理。

### ⑬ 被动回充（process）

- 未启用 / 穿重型护甲（强制关闭+提示）→ 不处理。
- 回充倒计时结束后：`shield_health += recharge_rate × seconds_per_tick`（上限 `max_shield_health`）。
- 从空到有充能 → 重新激活；若 `persistent_visuals` 则显示视觉。
- 回充开始（`recharge_visual_pending`）：播放 `eshield_recharge.ogg`、可见消息、显示滤镜（任意百分比都会触发）。
- 非持续视觉模式：受击闪光在 1.5s 后隐藏；回充满时隐藏回充显示。

### ⑭ 切换动作按钮 `/datum/action/item_action/toggle_energy_shield`

- 名称: Toggle Energy Shield；图标 `bci_shield`；按钮状态：启用 = 蓝色（`COLOR_BLUE`）/ 停用 = 红色（`COLOR_RED`）。

---

# 第二卷 · 能量护盾变体全录

**代码**: `modular_nova/modules/energy_shield/code/energy_shield_variants.dm`（89 行）

**变体总览表**（6 种）：

| 变体 | 名称 | 护盾值 | 回充延迟 | 回充速率 | 颜色 | 特殊 |
|---|---|---|---|---|---|---|
| `civilian` | Bolt SafeGuard 个人能量屏障 | 50 | 20s | 5/s | `#88ccff` | 出厂厂商 Bolt（法外民间款） |
| `military` | NT 战术护盾投影仪 | 100 | 8s | 8/s | `#4488ff` | `max_armor_class = 20`（兼容 II 级护甲） |
| `syndicate` | Gorlex 能量护盾 | 125 | 6s | 10/s | `#ff2244` | EMP 保留 50%，无护甲限制 |
| `syndicate/stamina` | 低功率能量护盾投影仪 | 100 | 20s | 10/s | `#ffaa33` | 仅耐力/低能弹，不挡致命弹与近战 |
| `syndicate/phasic` | Gorlex 相位偏转器 | 150 | 6s（继承） | 10/s（继承） | `#cc44ff` | 仅拦截高速弹射物，近战穿透 |
| `syndicate/bulwark` | Gorlex 壁垒发生器 | 200 | 8s | 8/s | `#ff6644` | 持续视觉 + 激活时减速 35% |

## 2.1 民用款 —— Bolt SafeGuard 个人能量屏障 `/obj/item/clothing/accessory/energy_shield/civilian`

- **描述**: Bolt Fabrications 旗下 SafeGuard 民用保护线量产的个人能量屏障。面向边境商贩、长途货运船员和法治覆盖之外的殖民者销售。输出有限挡不住军规弹药，但在边境装备店已成为标配。
- **数值**: `max_shield_health = 50`、`recharge_delay = 20 SECONDS`、`recharge_rate = 5`、`shield_color = "#88ccff"`
- **护甲上限**: 继承基础件 10（class II 以下）
- **厂商标注**: `manufacturer_examine` → **Bolt 公司**（COMPANY_BOLT）

## 2.2 军用款 —— NT 战术护盾投影仪 `/obj/item/clothing/accessory/energy_shield/military`

- **描述**: 为 Nanotrasen 安保部队设计的军规能量护盾。硬化电路使其可兼容标准制式衣物，但无法与重型护甲同用。
- **数值**: `max_shield_health = 100`、`recharge_delay = 8 SECONDS`、`recharge_rate = 8`、`shield_color = "#4488ff"`
- **护甲上限**: `max_armor_class = 20`（可搭配至 class II 级护甲）

## 2.3 辛迪加款 —— Gorlex 能量护盾 `/obj/item/clothing/accessory/energy_shield/syndicate`

- **描述**: 辛迪加制造的高性能个人护盾。超频电池 + 军规护盾发射器，性能远超合法市场任何产品。
- **数值**: `max_shield_health = 125`、`recharge_delay = 6 SECONDS`、`recharge_rate = 10`、`shield_color = "#ff2244"`
- **EMP 保留**: `emp_retention = 0.5`（EMP 后护盾减半）
- **护甲上限**: `max_armor_class = 100`（无护甲限制）

### 2.3.1 耐力特化款 —— 低功率能量护盾投影仪 `/obj/item/clothing/accessory/energy_shield/syndicate/stamina`

- **描述**: 特化屏障，波长针对眩晕光束（disabler）调谐，顺带拦截橡皮弹等低能弹；致命弹药无阻碍穿透。低强度发射器兼容任何护甲。通常为与法律作对的人所用。
- **数值**: `max_shield_health = 100`、`recharge_delay = 20 SECONDS`、`recharge_rate = 10`、`shield_color = "#ffaa33"`
- **开关**: `blocks_projectiles = FALSE`、`blocks_melee = FALSE`（仅 `blocks_stamina = TRUE`）
- **厂商标注**: `manufacturer_examine` → **Scarborough 公司**（COMPANY_SCARBOROUGH）

### 2.3.2 相位偏转款 —— Gorlex 相位偏转器 `/obj/item/clothing/accessory/energy_shield/syndicate/phasic`

- **描述**: 专为高速弹射物拦截调谐的辛迪加护盾。相移屏障对近战攻击完全透明。
- **数值**: `max_shield_health = 150`、`shield_color = "#cc44ff"`（延迟/速率继承 6s/10/s）
- **开关**: `blocks_melee = FALSE`（仅拦截弹射物 + 耐力）

### 2.3.3 壁垒款 —— Gorlex 壁垒发生器 `/obj/item/clothing/accessory/energy_shield/syndicate/bulwark`

- **描述**: 重型辛迪加护盾发生器，投射极其强大的屏障，代价是机动性。激活时引力子场发射器干扰正常移动。
- **数值**: `max_shield_health = 200`、`recharge_delay = 8 SECONDS`、`recharge_rate = 8`、`shield_color = "#ff6644"`、`persistent_visuals = TRUE`（激活期间持续显示护盾视觉）
- **移速惩罚**: `/datum/movespeed_modifier/energy_shield_bulwark` → `multiplicative_slowdown = 0.35`（激活时减速 35%，崩溃/脱下时移除）

---

# 第三卷 · 角斗士装备

**代码**: `modular_nova/modules/gladiator/code/game/objects/items/gladiator_items.dm`（221 行）

## 3.1 核心常量

| 常量 | 值 | 说明 |
|---|---|---|
| `BERSERK_MAX_CHARGE` | `100` | 狂战士模式最大充能 |
| `PROJECTILE_HIT_MULTIPLIER` | `1.5` | 弹射物受击充能倍率 |
| `DAMAGE_TO_CHARGE_SCALE` | `1` | 伤害 → 充能换算比例 |
| `CHARGE_DRAINED_PER_SECOND` | `3` | 狂战士模式每秒充能消耗 |
| `BERSERK_MELEE_ARMOR_ADDED` | `50` | 狂战士模式近战护甲加成（常量定义，供联动使用） |
| `BERSERK_ATTACK_SPEED_MODIFIER` | `0.25` | 狂战士模式攻速修正（常量定义，供联动使用） |

## 3.2 余烬骨（Ashen Bones）`/obj/item/crusher_trophy/gladiator`

- **名称**: ashen bones（余烬骨）
- **描述**: 一位值得尊敬的战士留下的一排烟熏肋骨。适合作为动能粉碎者（kinetic crusher）的战利品。
- **图标**: `demon_claws`，着色 `#808080`（灰色），性别复数
- **数值**: `bonus_value = 15`；`denied_type` 自身（同一战利品不可重复安装）
- **效果**: 使粉碎者获得 **15% 概率格挡来袭攻击**（`block_chance += 15`，安装/移除时增减）

## 3.3 被标记者之披风（Cloak of the Marked One）`/obj/item/clothing/neck/warrior_cape`

- **名称**: cloak of the marked one
- **描述**: 面见死亡并凯旋之人所披的斗篷。
- **图标**: `berserk_icons.dmi`（`berk_cape`）/ 穿戴 `berserk_suit.dmi`；手持状态为空
- **抗性**: `INDESTRUCTIBLE`（不可摧毁）
- **检查提示**: 附加警示文本（"woag"彩蛋之一）

## 3.4 狂战士铠甲（Berserker Armor）`/obj/item/clothing/suit/hooded/berserker/gatsu`

- **名称**: berserker armor
- **描述**: 一套注入强力精神磁性的古代铠甲，能大幅提升穿戴者的近战技艺，代价是侵蚀心智、过度透支身体。
- **图标**: `berserk_icons.dmi`（`berk_suit`）/ 穿戴 `berserk_suit.dmi` + 数字化 `berserk_suit_digi.dmi`
- **重量**: `WEIGHT_CLASS_BULKY`（笨重）；**抗性**: `INDESTRUCTIBLE`
- **兜帽类型**: `/obj/item/clothing/head/hooded/berserker/gatsu`（狂战士头盔）

### 护甲数据 `/datum/armor/berserker_gatsu`

| 类型 | 值 | 类型 | 值 |
|---|---|---|---|
| melee 近战 | 40 | bullet 子弹 | 40 |
| laser 激光 | 20 | energy 能量 | 25 |
| bomb 爆炸 | 70 | bio 生化 | 100 |
| fire 火焰 | 100 | acid 强酸 | 100 |

- **检查提示**: 附加警示文本。

## 3.5 狂战士头盔（Berserker Helmet）`/obj/item/clothing/head/hooded/berserker/gatsu`

- **名称**: berserker helmet
- **描述**: 一顶造型独特、带有可怖红眼的头盔，将穿戴者密封在内。
- **图标**: `berk_helm`；抗性 `INDESTRUCTIBLE`；护甲同 `/datum/armor/berserker_gatsu`
- **遮蔽**: `HIDEMASK|HIDEEARS|HIDEEYES|HIDEFACE|HIDEHAIR|HIDEFACIALHAIR|HIDESNOUT`（面具/耳/眼/脸/发/胡须/口鼻全遮蔽）
- **不可脱下**: 初始化时附加 `TRAIT_NODROP`（`LOCKED_HELMET_TRAIT`）
- **动作按钮**: `/datum/action/item_action/berserk_mode`（狂战士模式）

### 狂战士充能机制

- **受击充能**（`hit_reaction`，狂战士模式激活时不充能）：
  - 充能值 = `damage × DAMAGE_TO_CHARGE_SCALE`（1:1）
  - 弹射物攻击 × `PROJECTILE_HIT_MULTIPLIER`（1.5 倍）
  - 充能达到 100 → 气泡提示"充能已满"
- **消耗**（`process`）：狂战士激活时每秒 -3 充能；归零且在人形体内 → `end_berserk`
- **反射**（`IsReflect`）：狂战士激活时返回 `TRUE`（可反射弹射物）
- **脱下**（`dropped`）→ 强制结束狂战士模式
- **检查提示**: 附加警示文本（"woag!!!"彩蛋）

## 3.6 屠龙剑（Dragonslayer）`/obj/item/claymore/dragonslayer`

- **名称**: Dragonslayer
- **描述**: 一把大到不像剑的剑。太大、太厚、太重、太粗糙——更像一大块生铁。
- **图标**: `dragonslayer.dmi`；手持 64×64（左右手 `64x64_lefthand/righthand.dmi`）；命中音效 `bloodyslice.ogg`
- **重量**: `WEIGHT_CLASS_HUGE`（巨大）；**抗性**: `INDESTRUCTIBLE`
- **数值**:

| 属性 | 值 |
|---|---|
| force 基础伤害 | 20 |
| wound_bonus 伤口加成 | 10 |
| exposed_wound_bonus 暴露伤口加成 | 5 |
| armour_penetration 护甲穿透 | 35 |
| block_chance 格挡率 | 25 |
| sharpness 锋利度 | `SHARP_EDGED`（利刃） |
| item_flags | `NO_BLOOD_ON_ITEM`（不沾血） |
| slot_flags | 无（不可装袋） |

- **宿敌加成**: `faction_bonus_force = 40`，对 **FACTION_MINING（采矿阵营）** 与 **FACTION_BOSS（Boss 阵营）** 目标攻击时 force 临时 +40（攻击结束还原）。*注释：这个 Boss 很难打，而这把剑真的很大。*
- **检查提示**: 附加警示文本。

### 翻滚闪避（Dodge Roll）

- **触发**: 手持时对目标使用**次要交互**（右键点目标）。
- **条件**: 不可被定身（`IsImmobilized`）——"没有免费翻滚"。
- **消耗**: 10 耐力（STAMINA）；短暂定身 0.1s（不允许调整翻滚方向）。
- **效果**: 向目标格投掷自身（`throw_at`，`roll_range = 3` 格，`speed = 1`），播放 `SFX_BODYFALL` + `SFX_RUSTLE`，附加 1 秒无敌帧状态。
- **状态**: `/datum/status_effect/dodgeroll_iframes`（`id = "dodgeroll_dodging"`，`STATUS_EFFECT_REFRESH`，`duration = 1 SECONDS`）
  - 期间所有攻击判定 → `SUCCESSFUL_BLOCK`（whiff：玩家周围气泡"闪避！"+ `thudswoosh.ogg`）

## 3.7 淬火屠龙剑（Tempered Dragonslayer）`/obj/item/claymore/dragonslayer/very_fucking_loud`

- **名称**: Tempered Dragonslayer
- **命中音效**: `Clang_cut.ogg`（标志性金属碰撞声）
- **检查提示**: 附加 `span_userdanger` 红色危险文本（"很吵"）

## 3.8 角斗士宝箱（Gladiator Chest）

### `/obj/structure/closet/crate/necropolis/gladiator`

- **名称**: gladiator chest（角斗士宝箱）
- **内容**:
  - 5% 概率：淬火屠龙剑 `/obj/item/claymore/dragonslayer/very_fucking_loud`
  - 95% 概率：屠龙剑 `/obj/item/claymore/dragonslayer`
  - 必出：狂战士铠甲 `/obj/item/clothing/suit/hooded/berserker/gatsu`
  - 必出：被标记者披风 `/obj/item/clothing/neck/warrior_cape`

### `/obj/structure/closet/crate/necropolis/gladiator/crusher`（粉碎者击杀版）

- **名称**: dreadful gladiator chest（可怖角斗士宝箱）
- **内容**: 基础宝箱全部内容 + 余烬骨战利品 `/obj/item/crusher_trophy/gladiator`

---

# 第四卷 · 巨兽「被标记者」

**代码**: `modular_nova/modules/gladiator/code/modules/mob/living/simple_animal/hostile/megafauna/markedone.dm`（569 行）

## 4.1 常量定义

| 常量 | 值 | 说明 |
|---|---|---|
| `MARKED_ONE_STUN_DURATION` | `1.5 SECONDS` | 冲撞后眩晕时长 |
| `MARKED_ONE_ANGER_DURATION` | `10 MINUTES` | 愤怒状态持续时间 |
| `MARKED_ONE_FIRST_PHASE` | `1` | 第一阶段 |
| `MARKED_ONE_SECOND_PHASE` | `2` | 第二阶段 |
| `MARKED_ONE_THIRD_PHASE` | `3` | 第三阶段 |
| `MARKED_ONE_FINAL_PHASE` | `4` | 最终阶段 |
| `ONE_HUNDRED_PERCENT` | `100` | 血量阈值 100% |
| `SEVENTY_FIVE_PERCENT` | `75` | 血量阈值 75% |
| `FIFTY_PERCENT` | `50` | 血量阈值 50% |
| `SHOWDOWN_PERCENT` | `25` | 血量阈值 25%（决战） |
| `CHARGE_MODIFIER` | `0.4` | 冲撞移速修正值 |
| `TELE_QUIP_CHANCE` | `20` | 传送后说骚话概率（%） |

## 4.2 基础属性 `/mob/living/simple_animal/hostile/megafauna/gladiator`

- **名称**: The Marked One（被标记者）
- **描述**: 一位迷失在时间中的远古矿工，被死灵之城（Necropolis）选中并改造，封入一套铠甲。只有极少数人能与他的速度与力量匹敌。
- **图标**: `markedone.dmi`（`marked1`）/ 死亡 `marked_dying`
- **攻击动词**: cleave / cleaves（劈砍）；攻击音效 `bloodyslice.ogg`；死亡音效 `space_dragon_roar.ogg`
- **死亡信息**: "falls on his sword, ash evaporating from every hole in his armor."（倒在自己的剑上，灰烬从他铠甲每个孔洞蒸发）
- **GPS 信号名**: Forgotten Signal（被遗忘的信号）；性别男性
- **体型像素**: `pixel_x = -32`、`pixel_y = -9`（64×64 大精灵）
- **行动**: `wander = FALSE`（不闲逛）、`mouse_opacity = OPAQUE`

### 战斗数值

| 属性 | 值 | 说明 |
|---|---|---|
| health / maxHealth | **4000** | 对比：泡泡糖（Bubblegum）与巨像（Colossus）均为 2500 |
| melee_damage_lower / upper | 40 / 40 | 基础近战伤害 |
| rapid_melee | 1 | 快速近战连击数 |
| melee_queue_distance | 2 | 近战队列距离 |
| speed | 1 | 移动速度 |
| move_to_delay | 2.25 | 移动间隔 |
| ranged | 1 | 视为远程单位（无弹药的远程调度） |
| ranged_cooldown_time | 30 | 远程技能基础冷却 |
| minimum_distance | 1 | 最小攻击距离 |
| movement_type | `GROUND` | 地面移动（不飞行） |
| block_chance | 50 | 第 1/4 阶段 50% 概率完全格挡 |

### 战利品

| 击杀方式 | 战利品 |
|---|---|
| 普通击杀（loot） | 角斗士宝箱 `/obj/structure/closet/crate/necropolis/gladiator` + 遗骸 `/obj/structure/dead_gladiator` |
| 粉碎者击杀（crusher_loot） | 可怖角斗士宝箱 `/obj/structure/closet/crate/necropolis/gladiator/crusher` + 遗骸 |

- `replace_crusher_drop = TRUE`；`del_on_death = TRUE`（死后即删，防止肢解刷宝箱）
- 初始化附加 `TRAIT_NO_FLOATING_ANIM`；Destroy 时 `get_calm()`

## 4.3 状态机：冷静 ↔ 愤怒

- **冷静态**（初始）：`Found()`/`ListTargets()` 均返回空——不会主动攻击任何目标。
- **愤怒触发**（`get_angry`）：受到任何伤害（`adjustHealth`）；或向**灰烬蜥蜴人**（ash walker）自我介绍后。
- **愤怒持续**: 10 分钟（`MARKED_ONE_ANGER_DURATION`），计时器可叠加刷新；到期 `get_calm()` 恢复冷静。
- **攻击判定**: 仅愤怒态下 `Found`/`ListTargets` 正常返回目标。

## 4.4 自我介绍系统（introduction）

- 未愤怒且冷却（`next_intro_scan`，10s 间隔）时，扫描 4 格视野内生物；**非人类生物（simplemob）优先**。
- 对人类（SPECIES_HUMAN）——仅说话不攻击（6 句）：

  > "Is this all that is left?" / "Show the necropolis it was wrong to choose me." / "Ironic that I become what I once fought like you." / "Sometimes, the abyss gazes back." / "Show me a good time, miner!" / "I'll give you the first hit."

- 对灰烬蜥蜴人（SPECIES_LIZARD_ASH）——说话 + **立即愤怒** + 锁定目标，用灰烬语（ashtongue）（4 句）：

  > "Foolishness, ash walker!" / "I've had enough of you for a lifetime!" / "I don't need a crusher to KICK YOUR ASS!" / "GET OVER HERE!!"

- 对其他类人种族——说话 + 愤怒 + 锁定目标（5 句）：

  > "I will smite you!" / "I will show you true power!" / "Let us see how worthy you are!" / "You will make a fine rug!" / "For the necropolis!"

- 对非人形生物——只说一句："It's berserkin' time!"
- 每个目标仅自我介绍一次（`introduced` 列表记录）。

## 4.5 受击与格挡（adjustHealth）

- 每次受击 → `get_angry()`。
- **第 1 与第 4 阶段**：50% 概率完全格挡（黄色格挡特效 + 加大的 `parry.ogg`），返回 FALSE 不受伤害。
- 未格挡 → 正常扣血 + `update_phase()` + 攻击延迟：`next_move += min(amount × 0.15, 15)`（受击即被打断攻击，最多 +15 tick）。

## 4.6 四阶段变身（update_phase）——血量阈值

| 阶段 | 血量区间 | 图标 | rapid_melee | move_to_delay | 近战伤害 | 进入时动作 |
|---|---|---|---|---|---|---|
| 1 · 初见 | 75%–100% | `marked1` | 1 | 2.25 | 40/40 | 基础状态（格挡 50% 生效） |
| 2 · 第二阶段 | 50%–75% | `marked2` | 2 | 2 | 30/30 | 冲撞（charge, 21 格）+ 远程冷却 +8s |
| 3 · 第三阶段 | 25%–50% | `marked2`（沿用） | 4 | 1.5 | 25/25 | 冲撞 + 远程冷却 +5s |
| 4 · 最终阶段（决战） | 0%–25% | `marked3` | 1 | 1.2 | 50/50 | 剑砸地 + 践踏 + 冲撞，远程冷却 +8s（格挡 50% 重新生效） |

- 阶段转换只触发一次（用变量阶段值判断，防重复）。

## 4.7 技能全录（8 种）

### ① 旋转攻击（spinattack）

- 触发喊话（9 句随机）：

  > "Duck!" / "I'll break your legs!" / "Plain, dead, simple!" / "SWING AND A MISS!" / "Only one of us makes it outta here!" / "JUMP-ROPE!!" / "Slice and dice, right?!" / "Come on, HIT ME!" / "CLANG!!"

- 红色闪光 1s → 以自身为中心 12 条射线（每 30° 一条，`spinning_range = 6` 格）收集路径 turf（允许重复）。
- 沿途每个 turf：冒烟特效 + 对非友方生物造成 **40 点 BRUTE 胸部伤害**（`CANT_WOUND` 不可致伤），播放 `Clang_cut.ogg`。
- 伤害被挡住（被格挡）→ 旋转立即停止；否则继续，每格 0.75s 推进。

### ② 冲撞（charge）

- 触发喊话（7 句随机）：

  > "Heads up!" / "Coming through!" / "This ends only one way!" / "Hold still!" / "GET OVER HERE!" / "Looking for this?!" / "COME ON!!"

- 红色闪光 0.3s + 蓄力 4 tick（`SLEEP_CHECK_DEATH`）→ `charging = TRUE`、`minimum_distance = 0`、`move_to_delay -= 0.4`。
- 最大冲撞距离 `chargerange = 21` 格；撞到封闭 turf 或活物即结算。

### ③ 撞击结算（Bump / discharge）

- **撞到活物**：瞬移到对方位置、可见消息、目标**麻痹 20 tick**、冲撞终止（discharge）。
- **撞到封闭墙 turf**：可见消息、`discharge(1.5)`（眩晕 2.25s）。
- **discharge**：`stunned = TRUE`、还原 minimum_distance / move_to_delay、播放 `Clang_cut.ogg`、红色闪光还原、眩晕 `1.5s × modifier`。

### ④ 传送（teleport）

- 先尝试传送到目标**面朝方向**的下一格（不可为深渊/开放空间）。
- 失败则从目标周围 3 格视野随机选安全格。
- 起终点均有半秒烟雾特效；20% 概率（`TELE_QUIP_CHANCE`）说骚话（7 句）：

  > "Hi." / "Hello there." / "Hello." / "Hey." / "Yo." / "Boo." / "Sup."

- 近战命中后 5% × 阶段概率触发（阶段 4 时 20%）。

### ⑤ 骨刃投掷（bone_knife_throw）

- 生成战斗骨刃 `/obj/item/knife/combat/bone`，`throwforce = 35`，投掷距离 7 格，3 秒后自动销毁。
- 音效 `bolathrow.ogg`。

### ⑥ 地面重击（ground_pound，源自温迪戈地裂）

- 环形冲击波从自身向外扩散：`RANGE_TURFS(range)` 内按距离分层，每层间隔 `delay`。
- 层内所有活物（自身/被投掷中的除外）：红色警告消息 → 被抛飞（`throw_at` 8 格速 2，`MOVE_FORCE_OVERPOWERING` 不可抗）→ **20 点 BRUTE**（`CANT_WOUND`）→ 镜头震动。
- 音效 `bamf.ogg`（600 音量，10 外扩）。

### ⑦ 剑砸地（swordslam）

- `ground_pound(5, 0.4 SECONDS, 8)`：大半径（5 格）慢速扩散冲击波，抛飞距离 8。

### ⑧ 践踏（stomp）

- `ground_pound(2, 0.2 SECONDS, 3)`：小半径（2 格）快速扩散冲击波，抛飞距离 3。

## 4.8 远程技能调度（OpenFire）——按阶段概率表

> 机制：被标记者是"没有弹药的远程单位"，靠 OpenFire 调度技能。基础远程冷却 30s，每次出招追加冷却时间。

| 阶段 | 分支 | 概率 | 动作 | 追加冷却 |
|---|---|---|---|---|
| **1** | A | 10%（距离 ≤ 6） | 旋转攻击 | +5.5s |
| | B1 | 45%（A 不中时 50%） | 剑砸地 | +3s |
| | B2 | 45%（A 不中时 50%） | 骨刃投掷 | +1s |
| **2** | A1 | 60%（75%×80%×50%，距离 ≤ 6） | 旋转攻击 | +5s |
| | A2 | 15%（75%×80%×50%） | 剑砸地 | +2s |
| | A3 | 15%（75%×20%） | 传送 | +5s |
| | B | 10%（25%） | 传送 | +0.5s |
| **3** | A1 | 10.5%（70%×50%×30%，距离 ≤ 6） | 旋转攻击 | +3s |
| | A2 | 24.5%（70%×50%×70%） | 传送 + 践踏 | +4s |
| | A3 | 35%（70%×50%） | 骨刃投掷 + 剑砸地 | +2s |
| | B | 30% | 骨刃投掷 | +0.5s |
| **4** | A1 | 6.25%（50%×50%×25%） | 骨刃投掷 + 传送 + 践踏 | +1s |
| | A2 | 18.75%（50%×50%×75%） | 剑砸地 + 践踏 | +1s |
| | A3 | 25%（50%×50%） | 骨刃投掷 + 践踏 | +0.5s |
| | B | 50% | 传送 + 践踏 | +1s |

- 旋转/眩晕/冲撞进行中 → 不出招（OpenFire 返回）。

## 4.9 移动与地形规避（Move）

- **旋转/眩晕中** → 无法移动。
- **深渊规避**：目标格为深渊（chasm）时，按方向查偏移表绕行（8 方向各有 2 个候选偏移格）。
- **冲撞计数**：正常移动/绕行每格 +1 `chargetiles`，达到 `chargerange`（21）→ 强制 `discharge`。

## 4.10 遗骸结构 `/obj/structure/dead_gladiator`

- **名称**: solemn remains（肃穆遗骸）
- **描述**: 一位迷失在时间中的远古矿工，被死灵之城选中并改造，封入一套铠甲……而显然有人或某物击败了他。非凡的能量将尸体束缚在败亡之处，无法移动。
- **图标**: `markedone.dmi`（`marked_dying`）；男性；`pixel_x = -32`、`pixel_y = -9`
- **属性**: `anchored = TRUE`（固定）、`density = FALSE`（无碰撞）、`resistance_flags = LAVA_PROOF | FIRE_PROOF | UNACIDABLE | INDESTRUCTIBLE`（熔岩/火焰/强酸免疫 + 不可摧毁）
- **GPS**: 初始化时附加 GPS 组件，信号名 **"Fading Signal"（渐逝的信号）**

---

# 附录 · 代码路径索引

| 模块 | 文件 | 行数 | 内容 |
|---|---|---|---|
| energy_shield | `modular_nova/modules/energy_shield/code/energy_shield.dm` | 585 | 护盾本体（基础件 + 13 项机制 + HUD + 特效 + 动作按钮） |
| energy_shield | `modular_nova/modules/energy_shield/code/energy_shield_variants.dm` | 89 | 6 种变体（民用/军用/辛迪加/耐力/相位/壁垒）+ 移速修正 |
| energy_shield | `modular_nova/modules/energy_shield/icons/` | — | `energy_shield.dmi` / `energy_shield_worn.dmi` |
| gladiator | `modular_nova/modules/gladiator/code/game/objects/items/gladiator_items.dm` | 221 | 角斗士装备（余烬骨/披风/狂战士甲/头盔/屠龙剑/宝箱） |
| gladiator | `modular_nova/modules/gladiator/code/modules/mob/living/simple_animal/hostile/megafauna/markedone.dm` | 569 | 巨兽「被标记者」+ 遗骸结构 |
| gladiator | `modular_nova/modules/gladiator/icons/` | — | `markedone.dmi` / `berserk_icons.dmi` / `berserk_suit.dmi` / `berserk_suit_digi.dmi` / `dragonslayer.dmi` / `dragonslayer_inhand_L/R.dmi` |
| gladiator | `modular_nova/modules/gladiator/Clang_cut.ogg` | — | 标志性金属碰撞音效（屠龙剑/旋转攻击/冲撞） |

**合计**: 4 个 .dm 源码文件，1,464 行（674 + 790）。
