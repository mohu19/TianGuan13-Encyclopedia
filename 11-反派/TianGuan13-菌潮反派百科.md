# TianGuan13 · 菌潮（Blob）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/blob/`（27 文件 **2,759 行**）+ `code/__DEFINES/blob_defines.dm`（117 行常量）+ `code/datums/components/blob_minion.dm`（223 行）+ `code/modules/mob/living/basic/blob_minions/`（4 文件 489 行）+ `code/_onclick/hud/screen_objects/blob.dm`（167 行 HUD 购买）
> **类型**: 群体扩张型反派｜**难度**: ★★★★（菌株随机决定强度，前期脆弱后期碾压）
> **一句话**: 你是菌潮主宰（Blob Overmind）——在站内**安放核心**，用**资源点**扩张菌块、建节点/工厂/资源块、升级强菌块/反射菌块、招募孢子大军与魔像，菌块达到 **400 格**即达成"临界质量"（Critical Mass）。
> **本版全量审计**: 旧版（《反派系统百科》第八卷 8.2）宣称"14 菌株"仅每株一行简述——**本版从 `blobstrains/` 17 个文件全量提取 14 个可选菌株 + 1 个内部 multiplex 组合型**，全部数值实测自 `blob_defines.dm` 常量与各菌株/结构文件，无省略。
> **NOVA 关键差异**: `modular_nova/master_files/.../blob/overmind.dm` 覆盖 **`end_round_on_victory = FALSE`** —— 菌潮达到临界质量**不会强制结束回合**（上游 TG 会全站灭杀），改为 SEC_LEVEL_RED 警报继续游戏。

---

## 目录

- [一、核心机制](#一核心机制)
- [二、资源经济系统](#二资源经济系统)
- [三、菌株全量（14 可选 + 1 内部）](#三菌株全量14-可选--1-内部)
- [四、菌块结构全量（Tiles）](#四菌块结构全量tiles)
- [五、主宰操作与能力（Powers）](#五主宰操作与能力powers)
- [六、爪牙单位（Mobs）](#六爪牙单位mobs)
- [七、胜利与失败](#七胜利与失败)
- [八、NOVA 专属改动](#八nova-专属改动)
- [九、对战攻略](#九对战攻略)
- [十、数值速查表](#十数值速查表)

---

## 一、核心机制

| 机制 | 值 | 源码出处 |
|---|---|---|
| 初始点数 | **60** | `OVERMIND_STARTING_POINTS 60` |
| 点数上限 | **100** | `OVERMIND_MAX_POINTS_DEFAULT 100` |
| 基础收入 | **+2/秒**（核心 process 每 1 秒） | `BLOB_BASE_POINT_RATE 2` + `core_process()` 中 `resource_delay = world.time + 10` |
| 开局免费换菌株 | **1 次** | `OVERMIND_STARTING_REROLLS 1` |
| 换菌株花费 | **40 点**，此后每 **4 分钟**再送 1 次免费，**6 选 1** | `BLOB_POWER_REROLL_COST 40` / `BLOB_POWER_REROLL_FREE_TIME (4 MINUTES)` / `BLOB_POWER_REROLL_CHOICES 6` |
| 核心手动放置 | 出生后 **1 分钟**起可放置 | `OVERMIND_STARTING_MIN_PLACE_TIME (1 MINUTES)` |
| 核心自动放置 | **6 分钟**未放置 → 随机合法点自动放 | `OVERMIND_STARTING_AUTO_PLACE_TIME (6 MINUTES)` |
| 胜利条件 | **400 格**合法菌块（Critical Mass） | `OVERMIND_WIN_CONDITION_AMOUNT 400` |
| 公开警报 | 菌块达 **75 格** 或开局 **10 分钟** | `OVERMIND_ANNOUNCEMENT_MIN_SIZE 75` / `OVERMIND_ANNOUNCEMENT_MAX_TIME (10 MINUTES)` |

**Antag 属性**（blob_antag.dm datum）:
| 项 | 值 |
|---|---|
| roundend_category | "blobs" |
| pref_flag | ROLE_BLOB |
| HUD | blob（菌潮 HUD）|
| **出场音效** | **blobalert.ogg** |
| 感染变体 | ROLE_BLOB_INFECTION（感染流变体）|
| 预览 | 矿工套装预览（/datum/outfit/job/miner）|

> **说明**：菌潮 antag datum **无 moodlet/无 suicide_cry/无 hijack_speed/hardcore**（源码 grep 0 命中）——菌潮是"区域控制型"反派，力量来自主宰点数/菌块扩张而非角色属性。此为源码事实，非漏写。
| 主宰相机 | 只能游走菌块 **3×3** 范围（1 格） | `OVERMIND_MAX_CAMERA_STRAY "3x3"` |
| 扩张花费 | **4 点/格**；攻击命中目标则退 **2 点** | `BLOB_EXPAND_COST 4` / `BLOB_ATTACK_REFUND 2` |
| 目标 | "Reach critical mass!"（达成临界质量） | `blob_antag.dm` create_objectives |
| 阵营 | ROLE_BLOB；全菌潮共享"Blob Telepathy"心电通讯 | `overmind.dm` / `blob_minion.dm` |
| 感染模式 | 人类宿主获得 **Pop** 动作（6 分钟自动引爆/可提前手动），引爆后宿主 gib 化为主宰 | `blob_antag.dm` `/datum/action/innate/blobpop` |
| 哨站规则 | 无法在太空/非站内/非 BLOBS_ALLOWED 区域扩张获胜 | `is_valid_turf()` |

**抗性基础公式**（`_blob.dm` run_atom_armor）：钝伤 × `brute_resist 0.5`，烧伤 × `fire_resist 1`，再乘护甲减伤 `(100 - 护甲值)%`，最后过菌株 `damage_reaction()`。特斯拉电击伤害 = `power × 1.25e-3`（且电量被扣 `power × 2.5e-3`）。浩克拳击固定 15 伤害。

**自然扩张概率**（`pulse_area`）：每次脉冲有 `70%` 基础概率尝试扩张（`prob(70*(1/BLOB_EXPAND_CHANCE_MULTIPLIER))`，乘数=1）；单块扩张概率 `max(20 - 距离×8, 1)%`，紧邻菌块固定 `20%`，距离 ≤ expand_range 才扩张。

---

## 二、资源经济系统

### 收入来源

| 来源 | 数值 | 说明 |
|---|---|---|
| 核心被动 | **+2/秒** | `core_process()` 每 1 秒结算 `point_rate(2) + point_rate_bonus` |
| 资源菌块 | **+1 点/次脉冲**，冷却 **4 秒 + 0.25 秒×已有资源块数** | `BLOB_RESOURCE_GATHER_AMOUNT 1` / `GATHER_DELAY (4 SECONDS)` / `GATHER_ADDED_DELAY (0.25 SECONDS)` |
| 菌株加成 | Regenerative Materia **+1/秒** | `point_rate_bonus = 1` |
| 网络纤维手动扩张 | 换位 +1 点 / 无邻核 +4 点 | `networked_fibers.dm` expand_reaction |

### 支出与退款全表

| 项目 | 花费 | 拆除退款 | 备注 |
|---|---|---|---|
| 扩张到新格 | 4 | — | 攻击命中退 2 |
| 强菌块（Shield） | **15** | **4** | `BLOB_UPGRADE_STRONG_COST` / `BLOB_REFUND_STRONG_COST` |
| 反射菌块（Reflective） | **15** | **8** | `BLOB_UPGRADE_REFLECTOR_COST` / `BLOB_REFUND_REFLECTOR_COST` |
| 资源菌块 | **40** | **15** | `BLOB_STRUCTURE_RESOURCE_COST` / `BLOB_REFUND_RESOURCE_COST` |
| 节点 | **50** | **25** | `BLOB_STRUCTURE_NODE_COST` / `BLOB_REFUND_NODE_COST` |
| 工厂 | **60** | **25** | `BLOB_STRUCTURE_FACTORY_COST` / `BLOB_REFUND_FACTORY_COST` |
| 魔像（Blobbernaut） | **40** | — | `BLOBMOB_BLOBBERNAUT_RESOURCE_COST 40` |
| 核心迁移（与节点互换） | **80** | — | `BLOB_POWER_RELOCATE_COST 80` |
| 换菌株 | **40**（或免费） | — | `BLOB_POWER_REROLL_COST 40` |
| 尸变（分布式神经） | **5** | — | `distributed_neurons.dm` `add_points(-5)` |
| 核心 | 不可拆除 | **-1**（point_return < 0 禁止移除） | `point_return = -1` |
| 普通菌块 | — | **0** | 拆除无退款 |

> 建造约束：资源块/工厂**必须**在核心脉冲范围（4 格）或节点脉冲范围（3 格）内（`nodes_required = TRUE` 默认开启，可用 HUD 切换）；同类结构最小间距：节点 5 格、工厂 7 格、资源块 4 格。

---

## 三、菌株全量（14 可选 + 1 内部）

> **池定义**（`_blobstrain.dm` L1）：`valid_blobstrains = subtypesof(/datum/blobstrain) - (reagent 抽象父类, multiplex 内部型)` = **14 个可选菌株**。开局随机 pick 一个，可花 40 点/免费换。
> **攻击基准**：试剂类菌株每次菌块攻击注入 **25 体积**试剂（`BLOB_REAGENTATK_VOL 25`）；实际体积 = `round(25 × min(1.5 - 生物护甲%, 1), 0.1)`（满生物护甲=减半，护甲 <50% 则全额）。表中"×体积"均以 25 为基准。

### 3.1 总览表（14 可选）

| # | 菌株 | 颜色 | 伤害类型 | 核心效果 | 核心被动加成 |
|---|---|---|---|---|---|
| 1 | **Reactive Spines 反应棘刺** | #9ACD32 | 高钝（无视生物护甲） | 被打时 2.5s 冷却反击 1 格内一切 | — |
| 2 | **Explosive Lattice 爆炸晶格** | #8B2500 | 钝+烧 AOE | 免疫爆炸；孢子死亡爆炸 | — |
| 3 | **Cryogenic Poison 低温剧毒** | #8BA6E9 | 注入冻油/冰/本体 | 持续钝烧毒三系 DoT | — |
| 4 | **Blazing Oil 炽热燃油** | #B68D00 | 高烧+点燃 | 被烧爆出火苗；免疫火焰；怕水 | — |
| 5 | **Distributed Neurons 分布式神经** | #E88D5D | 中低毒 | 处决昏迷者；尸体变孢子僵尸；工厂孢子有灵 | — |
| 6 | **Electromagnetic Web 电磁网** | #83ECEC | 高烧+EMP | 钝伤全额（4 倍）；死亡 EMP | — |
| 7 | **Energized Jelly 充能凝胶** | #EFD65A | 体力+缺氧+窒息 | 特斯拉免疫；怕 EMP | — |
| 8 | **Networked Fibers 网状纤维** | #4F4441 | 高钝+高烧 | 只能手动扩张（换位核心/节点） | 核心再生 **+3/秒** |
| 9 | **Pressurized Slime 加压黏液** | #AAAABB | 低钝+高体力+缺氧 | 地板润滑灭火；钝伤减半 | — |
| 10 | **Regenerative Materia 再生物质** | #A88FB7 | 中毒+幻觉 | 假健康 HUD；持续毒 DoT | 核心再生 **+18/秒**、点数 **+1/秒** |
| 11 | **Replicating Foam 复制泡沫** | #7B5A57 | 中钝 | 30% 二次扩张；被烧 60% 扩张；怕钝 | — |
| 12 | **Shifting Fragments 位移碎片** | #C8963C | 中钝 | 被打概率与邻块换位 | — |
| 13 | **Synchronous Mesh 同步网格** | #65ADA2 | 低钝×邻块堆叠 | 伤害平摊给相邻块（最多 9 块含自身）；最高 2.6× | — |
| 14 | **Debris Devourer 残骸吞噬者** | #8B1000 | 投掷残骸（非试剂类） | 吞物喷物；受伤减免≤10 | — |

### 3.2 每株详细数值（实测自源码）

#### 1. Reactive Spines 反应棘刺（`reactive_spines.dm`）
- **攻击**：钝伤 = **1.0×体积**（无视生物护甲——`return_mob_expose_reac_volume` 直接返回全量体积，不乘防护系数）。
- **被动反击**：被钝伤/烧伤且存活时，**2.5 秒冷却**（`2.5 SECONDS`）内自动攻击 1 格内所有可攻击目标（活物注入本菌株试剂，其余走 `blob_act`）。
- 魔像攻击动词 "stabs"；预览图标默认菌株。

#### 2. Explosive Lattice 爆炸晶格（`explosive_lattice.dm`）
- **攻击**：体积 >10（非孢子云来源）时——震中 **0.35×体积钝 + 0.35×体积烧**（受 BOMB 护甲：`×(2 - round(护甲×0.01, 0.05))`），1 格内 AOE **0.25×体积钝 + 0.25×体积烧**；体积 ≤10（孢子云）时 **0.6×体积钝**。
- **抗性**：爆炸伤害（BOMB flag）→ **0**；非近战/子弹/激光伤害 → **×1.5**。
- **孢子死亡**：爆炸，总伤 `(10 + 10×云大小) / 距离`，均分钝/烧；无友伤（ROLE_BLOB 豁免）。

#### 3. Cryogenic Poison 低温剧毒（`cryogenic_poison.dm`）
- **攻击**：注入 **0.3×体积冻油 + 0.3×体积冰 + 0.3×体积本体**；直接钝伤 **0.2×体积**。
- **本体 DoT**（on_mob_life）：每 tick **0.25 钝 + 0.25 烧 + 0.25 毒**（×代谢率×秒）。
- 魔像攻击动词 "injects"。

#### 4. Blazing Oil 炽热燃油（`blazing_oil.dm`）
- **攻击**：烧伤 **0.8×体积**（禁伤口）+ 火堆 `round(体积/10)` + **点燃**（ignite_mob）；碳基目标尖叫。
- **被动**：受到非能量烧伤时，**80% 概率**在 1 格内非本菌株菌块格生成火苗（hotspot）。
- **抗性**：火焰伤害（FIRE flag）→ **0**（免疫）。
- **弱点**：被水/灭火液泼到 → 直接吃 **4.5 烧伤**（`extinguish_reaction` `take_damage(4.5, BURN, ENERGY)`）。
- 魔像攻击动词 "splashes"。

#### 5. Distributed Neurons 分布式神经（`distributed_neurons.dm`）
- **攻击**：毒伤 **0.6×体积**。
- **处决**：人类目标处于 UNCONSCIOUS / HARD_CRIT → 直接 `death()`。
- **尸变**：目标 DEAD 且主宰 ≥5 点 → 生成孢子僵尸（**-5 点**，`can_buy(5)`）。
- **濒死产孢**：被近战/子弹/激光造成 **≤20 伤害**击杀时，**15% 概率**生成**弱孢子**（15 HP）。
- **有灵孢子**：工厂产出的孢子可被幽灵直控（ROLE_BLOB_INFECTION）。

#### 6. Electromagnetic Web 电磁网（`electromagnetic_web.dm`）
- **攻击**：烧伤 **1.0×体积** + `2×体积%` 概率 **EMP_LIGHT**。
- **抗性**：钝伤**全额承受**（`damage / brute_resist(0.25)` = **×4**）；其余伤害 **×1.25**（一发激光 25 伤可秒普通块）。
- **死亡**：被近战/子弹/激光击杀 → `empulse(1, 3)`（1 格半径 EMP 3 强度）。

#### 7. Energized Jelly 充能凝胶（`energized_jelly.dm`）
- **攻击**：窒息 `round(0.2×体积)` + 体力 **1.2×体积** + 缺氧 **0.6×体积**。
- **特斯拉免疫**：`tesla_reaction` 返回 FALSE（不受电击）。
- **EMP 弱点**：受 EMP → 烧伤 `rand(30,50) - 强度×rand(10,15)`。
- 濒死（10% 概率）冒出 2-4 朵火花。

#### 8. Networked Fibers 网状纤维（`networked_fibers.dm`）
- **攻击**：**0.6×体积钝 + 0.6×体积烧**。
- **核心再生 +3/秒**（`core_regen_bonus = 3`，叠加基础 2 = 5/秒）。
- **只能手动扩张**：自然扩张被替换——**手动**扩张时若新块 1 格内有核心/节点则**与它换位**（菌潮可整体移动）；手动扩张无邻核时新菌块直接删除并 **+4 点**；**自动**扩张（无手动控制）时新菌块删除并 **+1 点**。

#### 9. Pressurized Slime 加压黏液（`pressurized_slime.dm`）
- **攻击**：钝伤 **0.4×体积** + 体力 **1.0×体积** + 缺氧 **0.4×体积** + 目标格 `体积%` 概率润滑（10 秒+5 秒续，TURF_WET_LUBE）。
- **抗性**：钝伤 **×0.5**。
- **被打**（近战/子弹/激光或非烧伤）：以伤害值为概率润滑 1 格内地板并**灭火**（物灭火、活物湿堆 2.5 + 灭火）。
- **死亡**：近战/子弹/激光击杀 → 50 概率大范围润滑灭火。

#### 10. Regenerative Materia 再生物质（`regenerative_materia.dm`）
- **攻击**：毒伤 **0.7×体积** + 注入 **0.2×体积本体 + 0.2×体积孢子毒素** + 幻觉 `2秒×体积`（drugginess）。
- **假健康 HUD**：目标全身显示"已痊愈"（fake_healthy 状态）。
- **本体 DoT**：每 tick **0.5 毒**。
- **核心再生 +18/秒、点数 +1/秒**（`core_regen_bonus = 18`，`point_rate_bonus = 1`）。

#### 11. Replicating Foam 复制泡沫（`replicating_foam.dm`）
- **攻击**：钝伤 **0.7×体积**。
- **抗性/反应**：钝伤 **×2**（脆弱）；被烧伤且存活 → **60% 概率立即扩张**一格（继承当前血量）。
- **扩张**：新菌块 **30% 概率二次扩张**。

#### 12. Shifting Fragments 位移碎片（`shifting_fragments.dm`）
- **攻击**：钝伤 **0.7×体积**。
- **被打**：近战/子弹/激光且 `prob(60 - 伤害)%` → 与 1 格内随机同菌株普通/强菌块（强块仅 25% 概率入选）**互换位置**。
- **扩张**：新块与母块换位（普通/强块）。

#### 13. Synchronous Mesh 同步网格（`synchronous_mesh.dm`）
- **攻击**：钝伤 **0.2×体积** + **每相邻菌块 +0.3×体积**（源码注释：满围 8 邻块 = 2.4×体积加成，总计 **2.6×体积**）。
- **受伤分摊**：近战/子弹/激光伤害分摊给相邻同菌株菌块（含自身最多 **9 块**，每块 `伤害/分摊数`，最低 11%）；核心/节点不参与（`ignore_syncmesh_share`）。
- **其余伤害 ×1.25**。

#### 14. Debris Devourer 残骸吞噬者（`debris_devourer.dm`）—— 唯一非试剂类可选菌株
- **攻击**：无试剂注入，改为投掷**残骸**（核心内物品），基础钝伤极低。
- **残骸密度**：`密度 = 核心内物品数 / (合法菌块数 × 0.25)`（每菌块物品数）。
- **投掷概率**：主宰攻击 `40 × 密度%`；爪牙攻击固定 `80%`（`FREE_MINION_DEBRIS_CHANCE 80`）；投掷距离 6、速度 5。
- **受伤减免**：`min(密度, 10)` 点减伤（上限 **10**）。
- **吞噬**：扩张时吸收格内物品进核心；爪牙可吃物品（上限 `mob_size×5` 件）。
- **孢子死亡**：抛出 **3 件**物品（`SPORE_TRASH_COUNT 3`）散射。
- 密度 1-3/4-7/8-10 对应扫描器不同描述档。

#### 15.（内部）Multiplex 多重组合（`multiplex.dm`，不在随机池）
- 组合多个菌株的伤害反应，每株系数 `typeshare = 0.8×株数 - (株数-1)`（1 株=80%，2 株=60%…）；特斯拉反应按组合概率判定。管理/特殊用途，正常对局不会出现。

### 3.3 菌株换选（Reroll）

- 开局 **1 次免费**，之后 **40 点/次**，每 **4 分钟**自动回赠 1 次免费（`last_reroll_time` 计时）。
- **6 选 1** 径向菜单，展示各株伤害/效果描述（analyzerdescdamage / analyzerdesceffect）。
- 换株即时生效：核心/节点范围加成、全菌块 HP 乘数、全爪牙 HP 乘数全部重算（`on_gain`/`on_lose`）。

---

## 四、菌块结构全量（Tiles）

> 基础属性：`density = TRUE`、`anchored`、可被击中、`light_range = 2`（普通块 0）；破坏时播放 splat 音效。所有结构 `resistance_flags = LAVA_PROOF`。

### 4.1 结构数值总表

| 结构 | HP | 再生/秒 | 护甲 | 特殊 | 花费 | 退款 |
|---|---|---|---|---|---|---|
| 核心 Core | **400** | **2**（+菌株加成） | fire 75 / acid 90 | 爆炸阻挡 6；claim 12 / pulse 4 / expand 3；强化半径 1；被炸伤 `10×(severity+1)`（20/15/10） | — | 不可拆 |
| 节点 Node | **200** | **3** | fire 65 / acid 90 / laser 25 | claim 10 / pulse 3 / expand 2；最小间距 5 | 50 | 25 |
| 工厂 Factory | **200** | **1** | laser 25 | 最多 3 孢子；8 秒产孢；出魔像后 HP×0.25=**50** | 60 | 25 |
| 资源块 Resource | **60** | （脉冲发 1 点） | laser 25 | 冷却 4s+0.25s×资源数；最小间距 4 | 40 | 15 |
| 普通块 Normal | **25**（初始 **21**） | 脉冲回 **1** | fire 80 / acid 70 / laser 50 | brute_resist 0.25；≤15 HP 变 fragile（brute_resist 升 0.5） | 4（扩张） | 0 |
| 强块 Shield | **150** | **2** | fire 90 / acid 90 / laser 25 | brute_resist 0.25；爆炸阻挡 3；**封锁大气**（<50% HP 失效）；<50% 显示 "weakened" | 15 | 4 |
| 反射块 Reflective | **150** | **2** | 同上 | **弹射子弹**（RICOCHET_SHINY）；爆炸阻挡 2 | 15 | 8 |

### 4.2 结构机制细节

- **核心 Core**（`core.dm`）：每 process() 结算 `core_process()`（每秒 +点数、`repair_damage(2 + 菌株加成)`）、`pulse_area`（claim/pulse/expand 半径内激活）与 `reinforce_area`（强化半径内普通块→强块、强块→反射块，每秒 **2.5%** 概率 `BLOB_REINFORCE_CHANCE 2.5`）。核心被毁 = 主宰死亡（`blob_core = null` 后主宰自毁）。核心自身 `ignore_syncmesh_share = TRUE`。stationloving 组件（不可带离站）。
- **节点 Node**（`node.dm`）：与核心同款脉冲+强化，但不产点数（无 core_process）；`health_regen = 3`。`creation_action` 注册进主宰 node_blobs。核心迁移 = 与节点互换位置（80 点）。
- **工厂 Factory**（`factory.dm`）：每 `spore_cooldown 8 秒` 产 1 只孢子（`max_spores 3`，含僵尸）；孢子死亡后重新计时；**召唤魔像后 max HP ×0.25（=50）**且停产孢子，魔像死亡后恢复。`is_creating_blobbernaut` 防重复召唤。
- **资源块 Resource**（`resource.dm`）：被脉冲时 `+1 点`（`BLOB_RESOURCE_GATHER_AMOUNT 1`），冷却 = `4s + 0.25s × 已有资源块数`（边际收益递减）。
- **强块/反射块**：由普通块升级而来（CTRL+左键），或核心/节点强化半径自动升级（免费，`point_return = 0` 的核心系变体）；反射块可弹射子弹；<50% HP 时名称加 "weakened"、大气封锁失效。
- **占领（Claim）**：无主菌块（前主宰死亡遗留）被脉冲时 30% 概率重新归属当前主宰。

---

## 五、主宰操作与能力（Powers）

| 操作 | 花费 | 说明 |
|---|---|---|
| 放置核心 | 0 | 需 1 分钟后；7 格内无活玩家、13 格视野内无人可见、地面无密度物 |
| 扩张（点地面） | 4 | 成功攻击目标退 2；对角线攻击不可扩张只结算 |
| 升级强块（CTRL+左键普通块） | 15 | — |
| 升级反射块（CTRL+左键强块） | 15 | 需强块 >50% HP |
| 建造资源块 / 节点 / 工厂（HUD） | 40 / 50 / 60 | 需在核心 4 格或节点 3 格内；间距限制 4 / 5 / 7 格 |
| 召唤魔像（站在工厂上点 HUD） | 40 | 工厂需 >50% HP；**20 秒**幽灵征集；失败全额退款 |
| 迁移核心（站在节点上点 HUD） | 80 | 核心与节点互换 |
| 换菌株（HUD） | 40 / 免费 | 6 选 1；4 分钟送 1 次免费 |
| 跳跃到节点 | 0 | 相机瞬移 |
| 集结孢子 | 0 | 35 格内无玩家控制的孢子 AI 指定行军目标 |
| 拆除菌块（ALT+左键） | 退款 | 核心（-1）不可拆；超出点数上限拒绝退款 |
| 切换节点需求 | 0 | `nodes_required` 开关 |

**HUD 布局**（`screen_objects/blob.dm`）：Jump to Node / Jump to Core（未放置时=放置核心）/ Produce Blobbernaut / Resource Blob / Node Blob / Factory Blob / Readapt Strain / Relocate Core / blob power 显示。

---

## 六、爪牙单位（Mobs）

> 全部爪牙：`faction = ROLE_BLOB`、TRAIT_BLOB_ALLY、哑巴（只能心电）、可穿菌块（blob_borne 时 PASSBLOB）、火伤 `clamp(0.01×温度, 1, 5)`、触碰菌块回血 `1.25% maxHealth/次`（`BLOBMOB_HEALING_MULTIPLIER 0.0125`）。

| 单位 | HP | 近战伤害 | 对物伤害 | 死亡效果 | 备注 |
|---|---|---|---|---|---|
| 孢子 Spore | **30** | **4-8** | 10 | **毒云 3×3**（40 体积菌株试剂） | 可飞、可钻通风管；近战杀死人类→尸变 |
| 弱孢子 Weak Spore | **15** | **2-4** | 0 | 毒云 1×1 | 分布式神经产出；不能尸变 |
| 孢子僵尸 Zombie | **70** | **10-15** | 20 | 毒云 3×3 | +尸体 MELEE 护甲进 maxHP；掉落尸体 |
| 魔像 Blobbernaut | **200** | 有主宰 **4** / 独行 **20** | **60** | — | 攻击注入 **20 体积**菌株试剂；详见下 |

### 6.1 孢子（Spore）

- 工厂每 8 秒产 1 只（上限 3）；`blob_spore.dm`：`health 30`、`melee 4-8`、`obj_damage 10`、`death_cloud_size = BLOBMOB_CLOUD_NORMAL(1)`。
- **死亡毒云**：`on_death` → 菌株 `on_sporedeath()`（试剂类=释放 `BLOBMOB_CLOUD_REAGENT_VOLUME 40` 体积菌株试剂的化学烟云；爆炸晶格=爆炸；残骸吞噬=抛 3 件物品）。无主孢子默认释放孢子毒素（toxin/spore）。
- 近战击杀人类（stat=DEAD）→ `zombify` 转化为**僵尸**（`change_mob_type` 继承阵营）。
- 工厂孢子绑定 z 层（离开工厂 z 层即死）；`TRAIT_PERMANENTLY_MORTAL`（不可复活）。
- 独立孢子（cytology 金核召唤）：橙色外观、幽灵可直控（ROLE_FREE_BLOB）、死亡掉落 spore_sack。
- **分布式神经**菌株下：工厂孢子获得幽灵直控组件（ROLE_BLOB_INFECTION），换株移除。

### 6.2 孢子僵尸（Zombie）

- `blob_zombie.dm`：`health 70`、`melee 10-15`、`obj_damage 20`、毒云 3×3。
- `consume_corpse`：吞噬尸体获得其 MELEE 护甲加成（maxHP += 护甲值）；死亡/尸体复活时吐出尸体。
- 工厂产出的僵尸（controlled）会 ping 幽灵接管（ROLE_BLOB_INFECTION）。

### 6.3 魔像（Blobbernaut）

- `blobbernaut.dm`：`maxHealth 200`、`melee_damage 4/4`（有主宰时，`BLOBMOB_BLOBBERNAUT_DMG_LOWER/UPPER 4/4`；主宰死亡后独行 **20/20** `SOLO`）、`obj_damage 60`、`damage_coeff BRUTE 0.5`、**伤害阈值 10**（每击 ≥10 才受伤）、`pressure_resistance 50`、MOB_SIZE_LARGE。
- **攻击注入 20 体积菌株试剂**（`BLOBMOB_BLOBBERNAUT_REAGENTATK_VOL 20`）+ 基础钝伤 4（注释："plus 4 from base brute damage"）。
- **回复**：核心 2 格内 **+5% maxHP/秒**（`HEALING_CORE 0.05`）；节点 2 格内 **+2.5%/秒**（`HEALING_NODE 0.025`）。
- **衰减**：远离菌块（2 格内无菌块）**-1.25% maxHP/秒**（`HEALTH_DECAY 0.0125`，TOX）；工厂被毁（orphaned）再 -1.25%；两者叠加 -2.5%/秒。生成时初始 **50% HP**。
- **可驯服**：投喂蛋类/荧光棒/沙姆博汽水，驯服率 **25%**（+15% 加成）。
- 攻击动词随菌株变化（slams/stabs/splashes/blasts/emits slime at…）。

---

## 七、胜利与失败

### 胜利（Critical Mass）

1. 合法菌块（BLOBS_ALLOWED 区域）达 **400 格** → `begin_victory()`：公告 "Biohazard has reached critical mass"，安全等级 **SEC_LEVEL_DELTA**，状态屏生物危害。
2. **45 秒**后 `victory()`：目标标记完成。
3. **NOVA 版**（`end_round_on_victory = FALSE`）：**不结束回合**——播放"紧急生物危害对策"公告，安全等级降为 **RED**；菌块数 ≥400 后 `can_make_blob` 禁止继续扩张（"400 tiles is still a lot to fight through..."）；点数保持上限 100。
4. **上游原版**（对照）：victory 后全站非菌潮阵营生物死亡、区域全部变为菌潮贴图、强制结束回合（FORCE_END_ROUND）。

### 失败

- 核心被摧毁 → 主宰消失（`cleanup_overmind_blobs` 异步清除所有菌块归属，菌块变灰色无主）。结分报告显示菌潮峰值格数（`max_count`）。
- 未放置核心 6 分钟 → 随机放置（BLOB_RANDOM_PLACEMENT）。
- 主宰幽灵可继续观战。

---

## 八、NOVA 专属改动

| 文件 | 改动 |
|---|---|
| `modular_nova/master_files/code/modules/antagonists/blob/overmind.dm` | **`end_round_on_victory = FALSE`**（菌潮临界质量不再强制结束回合）；`Initialize()` 清除 `SSshuttle` 敌对环境（菌潮不再导致穿梭机敌对）；`victory()` 后安全等级设为 **RED** |
| 11 个核心文件头注释 | **I18N CODEMOD**：全部玩家可见字符串改写为 `LANG()`（i18n 键值系统），见 `modular_nova/modules/i18n/readme.md`——涉及 `blob_antag.dm`、`overmind.dm`、`powers.dm`、`structures/factory.dm`、`structures/resource.dm`、`structures/_blob.dm`、菌株 `debris_devourer/distributed_neurons/pressurized_slime/reactive_spines` 及 HUD `screen_objects/blob.dm`、爪牙 4 文件 |
| `overmind.dm` L75 | NOVA EDIT CHANGE：主宰名字用 `lang_reverse_text(initial(name))` 反查中文名（避免 i18n 覆盖） |
| HUD `readapt_strain` L131 | NOVA EDIT CHANGE：免费换株按钮显示 "(FREE)" 中文化 |

> 旧版《反派系统百科》8.3 提到的"变形怪蜘蛛能力移除"等属于**其他**反派系统（蜘蛛/绑架者），与菌潮无关，此处不采信。

---

## 九、对战攻略

- **开局**：60 点 —— 先放核心，立刻造 1 个资源块（40 点）回本，留 20 点扩张 5 格；1 分钟放置窗口结束前找封闭空间（如维护通道）发育。
- **经济优先级**：资源块（40）→ 节点（50，扩大脉冲范围才能建更多资源/工厂）→ 工厂（60，产孢子防线）→ 强块（15）封口。资源块越多冷却越长（+0.25s/个），8-10 个即到收益拐点。
- **菌株适配**：Networked Fibers = 机动流（换位跑路）；Regenerative Materia = 龟缩流（+18 核心再生）；Replicating Foam = 爆发扩张流；Debris Devourer = 恶心流（吞工具武器喷人）；Electromagnetic Web 专克机甲/机械。
- **防御**：强块封锁大气防放火/毒气，反射块防弹射流；核心 1 格内自动免费升级强块（2.5%/秒）。
- **魔像**：40 点 + 20 秒幽灵征集；优先在有节点/核心掩护的工厂出，避免孤儿衰减（-1.25%/秒×2）。
- **NOVA 注意**：临界质量不结束回合 —— 达到 400 格后无法再扩张（can_make_blob 拦截），只能固守，**别把 400 格当终局**；尽早用免费换株调整菌株应对全站围攻。

---

## 十、数值速查表

| 项 | 值 |
|---|---|
| 初始/上限点数 | 60 / 100 |
| 基础点数收入 | +2/秒 |
| 资源块收入 | +1/次，4s+0.25s×数量 |
| 扩张 / 攻击退款 | 4 / 2 |
| 胜利格数 / 警报格数 | 400 / 75（或 10 分钟） |
| 核心 HP / 再生 | 400 / 2+菌株加成 |
| 节点 HP / 再生 / 价格 | 200 / 3 / 50 |
| 工厂 HP / 再生 / 价格 / 孢子上限 / 产孢 | 200 / 1 / 60 / 3 / 8 秒 |
| 资源块 HP / 价格 | 60 / 40 |
| 普通块 HP（初始）/ 再生 | 25（21）/ 脉冲+1 |
| 强块 HP / 价格 / 退款 | 150 / 15 / 4 |
| 反射块 HP / 价格 / 退款 | 150 / 15 / 8 |
| 魔像价格 / HP / 攻（有主/独行）/ 对物 | 40 / 200 / 4 / 20 / 60 |
| 魔像回复（核 2 格/节 2 格） | +5% / +2.5% maxHP/秒 |
| 魔像衰减（离块/无厂） | -1.25% / -1.25% maxHP/秒（叠加） |
| 孢子 HP / 攻 / 对物 | 30 / 4-8 / 10 |
| 弱孢子 HP / 攻 | 15 / 2-4 |
| 僵尸 HP / 攻 / 对物 | 70 / 10-15 / 20 |
| 死亡毒云 | 3×3，40 体积菌株试剂 |
| 菌块攻击注入试剂 | 25 体积 |
| 魔像攻击注入试剂 | 20 体积 |
| 换株价格 / 免费间隔 / 选项 | 40 / 4 分钟 / 6 选 1 |
| 迁移核心 / 尸变 | 80 / 5 |
| 核心/节点/工厂/资源最小间距 | — / 5 / 7 / 4 |
| 核心脉冲半径（claim/pulse/expand） | 12 / 4 / 3 |
| 节点脉冲半径（claim/pulse/expand） | 10 / 3 / 2 |
| 核心强化半径（强/反射） | 1 / 0 |
| 强化概率 / 自然扩张基础概率 | 2.5%/秒 / 70%/脉冲 |
| 单块扩张概率 | max(20-距离×8, 1)%（邻近 20%） |
| 钝/烧伤害系数 | ×0.5 / ×1 |
| 基础护甲（fire/acid/laser） | 80 / 70 / 50 |
| 特斯拉伤害 | power×1.25e-3 |
| 浩克拳 | 15 |
| 核心爆炸伤害 | 10×(severity+1) |
| 菌株可选数 | **14**（+1 内部 multiplex） |

---

## 源码声明

- 主目录：`code/modules/antagonists/blob/`（27 文件 2,759 行）
- 常量定义：`code/__DEFINES/blob_defines.dm`（117 行，本页所有 BLOB_/OVERMIND_/BLOBMOB_ 数值来源）
- 菌株：`code/modules/antagonists/blob/blobstrains/`（17 文件，含 `_blobstrain.dm` 池定义、`_reagent.dm` 试剂父类、14 可选株 + multiplex）
- 结构：`code/modules/antagonists/blob/structures/`（6 文件：`_blob.dm` 基础 + core/node/factory/resource/shield）
- 主宰：`code/modules/antagonists/blob/overmind.dm`（401 行）+ `powers.dm`（411 行）+ `blob_antag.dm`（187 行）+ `blob_minion.dm`（36 行）
- 爪牙：`code/modules/mob/living/basic/blob_minions/`（blob_mob/blob_spore/blob_zombie/blobbernaut，4 文件 489 行）+ `code/datums/components/blob_minion.dm`（223 行）
- HUD 购买：`code/_onclick/hud/screen_objects/blob.dm`（167 行）
- NOVA 覆盖：`modular_nova/master_files/code/modules/antagonists/blob/overmind.dm`（10 行）
- 审计：所有数值均 grep/read 自上述文件，禁止推测；本页 30+ 数值声明已由脚本对照源码复核（见审计说明）。
