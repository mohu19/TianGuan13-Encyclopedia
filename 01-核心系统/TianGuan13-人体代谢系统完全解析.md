# 天关 — 人体代谢系统完全解析（2026-08 审核重置版）

> **代码**: `code/modules/reagents/chemistry/holder/mob_life.dm`（215 行，试剂代谢核心）、`code/modules/reagents/chemistry/holder/holder.dm`（820 行）、`code/modules/mob/living/carbon/life.dm`（852 行，生命循环）、`code/modules/surgery/organs/internal/stomach/_stomach.dm`（595 行，胃消化）、`code/datums/status_effects/debuffs/drunk.dm`（279 行，醉酒）、`code/modules/reagents/withdrawal/`（成瘾）
>
> **审核说明（2026-08）**：原版 9.2KB 文档经对照源码核查，发现**多处错误与重大遗漏**——①血液系统描述与实际不符（实为血型试剂恢复系统，原文档写"血液再生/损失"泛泛而谈）；②Mannitol 过量阈值写 30 实为 15；③成瘾仅 6 种实为 7 类型；④**完全缺失**：醉酒系统、营养系统、胃消化数值、血型输血系统、试剂纯度系统。本版为重置后的完整精确版。

---

## 目录

- [一、整体代谢架构](#一整体代谢架构)
- [二、摄入途径](#二摄入途径)
- [三、代谢系统核心](#三代谢系统核心)
- [四、肝脏解毒系统](#四肝脏解毒系统)
- [五、血型与血液系统](#五血型与血液系统)
- [六、胃消化与营养系统](#六胃消化与营养系统)
- [七、醉酒系统](#七醉酒系统)
- [八、过量系统](#八过量系统)
- [九、成瘾系统](#九成瘾系统)
- [十、体温调节](#十体温调节)
- [十一、死后代谢与特殊状态](#十一死后代谢与特殊状态)
- [十二、试剂纯度系统](#十二试剂纯度系统)
- [十三、完整代谢流程图](#十三完整代谢流程图)
- [附录 · 代码路径索引](#附录--代码路径索引)

---

# 一、整体代谢架构

每 2 秒（SSmobs 每 tick），人物经历一次 `life()` 循环：

```
life():
  ├── 伤口处理（all_wounds）
  ├── handle_dead_metabolization()  ← 死后代谢（优先，可修改活体代谢）
  ├── handle_organs()               ← 器官处理（肝解毒/胃消化/心脏）
  ├── handle_reagents()             ← 试剂代谢（所有体内化学品）
  │   └── reagents.metabolize()
  ├── handle_blood()                ← 血液处理（血型试剂恢复）
  ├── 成瘾处理（addiction_points）
  ├── handle_brain_damage()         ← 脑损伤
  └── handle_environment()          ← 体温调节
```

**特殊状态**：
- **TRAIT_STASIS（静止）**：跳过正常代谢，只处理 `REAGENT_IGNORE_STASIS` 标记的试剂（`handle_stasis_chems`）
- **TRAIT_NO_TRANSFORM**：跳过整个 Life
- **NOVA 新增**：站在污染地块每 tick 触发 `pollution.touch_act`

**呼吸链**（`handle_breathing`）：
- 正常 **4 秒/次**；肺损伤>高阈值 **-1**；心脏损伤>高阈值 **-1**（最低 2 秒）
- 肺衰竭（ORGAN_FAILING）→ losebreath++；硬致命/被抓喉 → losebreath++；软致命 → +0.25
- **NOVA 新增**：水下呼吸系统（躺着+水位≥腰/站着+水位≥满格）、污染嗅闻（smell_act/breathe_act，冷却 SMELL_COOLDOWN）

---

# 二、摄入途径

## 2.1 进食/喝水（口入）

**流程**：
```
食物/饮料 → 进入胃(stomach) → 胃逐步释放到血液 → 全身代谢
```

**胃核心数值**（`_stomach.dm`）：
| 属性 | 值 |
|---|---|
| 胃容量 | **1000u**（reagent_vol） |
| 转移常量 | **STOMACH_METABOLISM_CONSTANT = 0.25**（每 tick 转移下限） |
| 转移效率 | **metabolism_efficiency = 0.05**（转移比例，最低 0.025） |
| 食物试剂保护 | food_reagents 不转移（保留 5u 胃壁细胞） |
| 非代谢试剂 | 代谢率≤0 的试剂**滞留胃中**（塑料占空间不消化） |

**转移公式**：
```dm
amount = min((round(0.05 × 试剂体积, 0.05) + 0.25) × 秒/tick, 试剂体积)
```

**营养获取**：营养试剂（nutriment）通过胃转移到血液后 → `nutriment_factor × volume / 代谢率` 转化为营养值。

**呕吐**：
| 条件 | 概率 | 效果 |
|---|---|---|
| 胃损伤<低阈值+有营养 | `0.0125 × 伤 × 营养²`/tick | 呕吐（lost_nutrition=损伤） |
| 胃损伤>高阈值+有营养 | `0.05 × 伤 × 营养²`/tick | 呕吐+清除毒素（blessing） |
| 无血呕吐 | 必然 | 营养 -lost_nutrition + 毒伤 -3 |

**胃腐烂**：decay_factor = 标准×1.15 ≈ **13 分钟**（最早腐烂的器官之一）

## 2.2 注射（静脉）

```
注射器/输液泵 → 直接进入血液(reagents) → 立即开始代谢
```
跳过胃部直接入血。部分试剂需注射才生效（疫苗等）。

## 2.3 吸入（气体）

```
环境大气/气罐 → 肺部(lungs) → 气体溶解为试剂 → 血液
```

| 气体 | 转化试剂 | 效果 |
|---|---|---|
| **O₂** | — | 维持生命，分压<16→缺氧 |
| **Plasma** | 等离子毒素 | 血液毒素，损伤器官 |
| **N₂O** | 氧化亚氮 | 麻醉效果 |
| **BZ** | BZ | 致幻 |
| **Tritium** | 铀/镭 | 辐射损伤 |
| **Pluoxium** | 医药 | **8 倍 O₂ 效率** |
| **Miasma** | 毒素 | 血液毒素 |
| **Freon** | 制冷剂 | 急剧降温 |

---

# 三、代谢系统核心

## 3.1 metabolize() — 每 tick 调用

**代码**: `mob_life.dm`（215 行）

```dm
/datum/reagents/proc/metabolize(owner, seconds_per_tick, can_overdose, liverless, dead):

步骤:
1. 温度影响: expose_temperature(体温, 0.25)  ← 体温影响反应速率
2. 肝脏检查: 获取肝脏+胃
   liver_tolerance = 肝脏HP% × toxTolerance
   毒素量(含胃内) < 肝耐受×每毒素倍数 → 肝脏直接代谢(无害)
   毒素量 > 肝耐受 → 毒素进入血液
3. 肝损伤计算:
   damage += (毒素量/15) × toxin_power × 肝损伤倍率 / 肝抗性
   上限: MAX_TOXIN_LIVER_DAMAGE × 秒/tick
4. 过量检查: 试剂 ≥ overdose_threshold → overdose_start() → overdose_process()
5. 成瘾检查: 有 addiction_types → 积累成瘾点 → 超阈值上瘾
6. 试剂效果: reagent.on_mob_life()  ← 每种试剂独有效果
7. 代谢: reagent.metabolize_reagent()  ← 减去代谢量
```

**关键细节**（原文档遗漏）：
- **毒素无害代谢**：`amount <= liver_tolerance × toxin.liver_tolerance_multiplier` 时，直接 remove_reagent（**每毒素有独立耐受倍数**），不进血液
- **堆叠代谢效应**：同一 tick 代谢 ≥2 种试剂时，检查 `stacked_metabolization_effect`（组合效应）
- **免疫特质**：TRAIT_TOXINLOVER（爱毒者）/TRAIT_TOXIMMUNE（毒免疫）→ 跳过肝损伤计算
- **器官特异性**：`affected_organ_flags`——某些毒素只影响特定类型器官
- **NOVA 新增**：`reagent_process_flags_valid()` 检查（不能处理的试剂按正常速率移除，不做成瘾/过量/on_mob_life）

## 3.2 代谢速率

```dm
metabolization_rate = 试剂默认速率 × 人物代谢效率 × seconds_per_tick
```

**影响代谢效率的因素**：
| 因素 | 值 | 条件 |
|---|---|---|
| 正常 | 1.0 | — |
| 营养饱足（营养>350 且饱足感>80） | **1.25** | 代谢加速 |
| 饥饿（营养<200） | **0.8** | 代谢减慢 |
| 肥胖（营养>600） | 1.0 | — |
| 昏迷/假死 | 降低 | — |
| 低温 | 降低 | 冷冻机减代谢 |

**肝损伤上限**：`MAX_TOXIN_LIVER_DAMAGE = 2`/秒（约 1 分钟满伤摧毁肝脏）

---

# 四、肝脏解毒系统

## 4.1 肝脏功能

**代码**: `code/modules/surgery/organs/internal/liver/`（肝器官）

| 属性 | 说明 |
|---|---|
| toxTolerance | 基础毒耐受 |
| liver_resistance | 抗毒系数 |
| filterToxins | 是否过滤毒素 |

```dm
肝耐受 = 当前HP/最大HP × toxTolerance × 每毒素耐受倍数
```

| 肝脏状态 | 毒耐受 | 效果 |
|---|---|---|
| 健康 | 100% | 正常过滤 |
| 轻度损伤 | 50~99% | 过滤下降 |
| 重度损伤 | 10~50% | 大量毒素通过 |
| 衰竭 | <10% | 几乎不过滤 |
| **无肝脏** | 0 | **不过滤，直接中毒** |

## 4.2 肝损伤公式

```dm
每tick伤害 = (毒素量/15) × toxin_power × 肝损伤倍率 / 肝抗性
上限 = 2 × seconds_per_tick
```

| 毒素 | toxin_power | 肝毒性 |
|---|---|---|
| Plasma | 2 | ★★ |
| Cyanide | 4 | ★★★★ |
| Histamine | 3 | ★★★ |
| Initropidril | 5 | ★★★★★ |
| Alcohol(Ethanol) | 1 | ★（少量无害，过量伤肝） |

**肝损伤提示**：`damage > 10` 时，每 tick `SPT_PROB(damage/6)` 概率腹部疼痛提示。

## 4.3 无肝代谢

```dm
if liverless && !reagent.self_consuming:
    return FALSE  // 不代谢
```

无肝脏（丧尸/机械体）时，只有 `self_consuming = TRUE` 的试剂能代谢（如 Omnizine）。

---

# 五、血型与血液系统

**代码**: `code/modules/mob/living/carbon/life.dm`（reagent_tick/reagent_expose）

> **审核修正**：原文档写"血液再生/损失"，实际是**血型试剂系统**——血液不是自动再生的，而是通过注射血型对应试剂恢复。

## 5.1 血液量常量

| 常量 | 值 | 含义 |
|---|---|---|
| **BLOOD_VOLUME_NORMAL** | **560u** | 正常血量 |
| BLOOD_VOLUME_SAFE | 476u（-15%） | 安全线 |
| BLOOD_VOLUME_OKAY | 392u（-30%） | 可行线 |
| BLOOD_VOLUME_RISKY | 308u（-45%） | 危险线 |
| BLOOD_VOLUME_BAD | 224u（-60%） | 濒死线 |
| BLOOD_VOLUME_SURVIVE | 112u（-80%） | 生存线 |
| BLOOD_VOLUME_MAXIMUM | 1000u | 最大血量 |
| BLOOD_VOLUME_MAX_LETHAL | 1075u（×1.075） | 致命过量 |
| BLOOD_VOLUME_EXCESS | 1050u（×1.05） | 过量 |
| BLOOD_REGEN_FACTOR | **0.25**/秒 | 恢复速率 |

## 5.2 血液恢复机制（血型试剂）

```dm
// reagent_tick: 每次试剂代谢检查
if 试剂 == 血型的restoration_chem && 血量 < 560:
    血量 += 0.25 × 秒/tick
    试剂按代谢率移除
```

**每种血型关联一个恢复试剂**（restoration_chem）——注射对应试剂且在血量不足时，按 **0.25/s** 恢复血液。

## 5.3 输血系统

```dm
// reagent_expose: 注入血型试剂时
if 注入试剂 == 血型的reagent_type:
    血量 += 注入量（上限 1000）
    // NOVA: 猴子血注入上限钳制 560
    检查血型兼容:
      不兼容 → 产生毒素 0.5×注入量（排异反应）
    合成血: 计算合成血成分比例（synth_blood 组件）
```

**关键规则**：
- 注入**同型血**=补血（每 1u 血试剂 = 1u 血量）
- 注入**异型血**=排异（产生 0.5×血量毒素）
- **NOVA 新增**：猴子血（MONKEY_ORIGINS）注入上限钳制 560u
- **合成血**：带合成成分标记的血，按加权平均计算合成血比例

## 5.4 心脏与血液再生

```dm
get_heart_blood_regeneration_multiplier():
  无心脏需要 → 1.0
  有心脏 → 心脏.get_blood_regeneration_multiplier()
  心脏缺失/停跳/衰竭 → 0（无法再生血）
```

---

# 六、胃消化与营养系统

**代码**: `_stomach.dm`（595 行）

## 6.1 营养消耗

```dm
// handle_hunger 每 tick:
hunger_rate = HUNGER_FACTOR(0.05)
  × 理智修正(理智>50: ×max(1-0.002×理智, 0.5))
  × 饥饿修正(饱足感<0: ×3，即0.15)
  × 胃的hunger_modifier
  × 生理hunger_mod
  × NOVA: 躺平/昏迷 ×0.5
nutrition -= hunger_rate × 秒/tick
```

**移动消耗**（`carbon_movement.dm`）：行走 HUNGER_FACTOR/10 = 0.005/步，跑步 ×2 = 0.01/步

## 6.2 营养等级

| 等级 | 值 | 效果 |
|---|---|---|
| **肥胖 FAT** | >600 | TRAIT_FAT（需过食 20 分钟）；代谢效率 1.0 |
| **满 FULL** | 550 | — |
| **吃好 WELL_FED** | 450 | 饱足感>80 时代谢效率 **1.25** |
| **吃饱 FED** | 350 | — |
| **饥饿 HUNGRY** | 250 | — |
| **很饿 VERY_HUNGRY** | 200 | 代谢效率 **0.8** |
| **挨饿 STARVING** | 150 | 代谢效率 0.8 |

## 6.3 饱足感（Satiety）

- 上限 **±600**（MAX_SATIETY）
- 正饱足感→每 tick 减 1
- **负饱足感**→每 tick 加 1，饥饿率 ×3（0.15），且 `SPT_PROB(-satiety/77)` 概率颤抖 10 秒

## 6.4 肥胖系统

- 营养>550 且无 TRAIT_NOFAT → 过食计时累加（上限 **20 分钟**）
- 过食≥20 分钟 → **TRAIT_FAT + TRAIT_OFF_BALANCE_TACKLER**
- 营养<550 → 过食计时每 tick -2 秒（减速 2 倍）

## 6.5 检查外观

- 营养<100（挨饿-50）→ "看起来营养不良"
- 营养<150（挨饿）→ "看起来饿了"

---

# 七、醉酒系统

**代码**: `code/datums/status_effects/debuffs/drunk.dm`（279 行）

> **审核补充**：原文档完全缺失醉酒系统。

## 7.1 核心机制

| 机制 | 值 |
|---|---|
| 阈值（微醺→醉） | **TIPSY_THRESHOLD = 23.4**（NOVA 从 6 上调） |
| 阶段 | tipsy（微醺）→ drunk（醉） |
| 铁肝上限 | iron_liver=51（TRAIT_IRON_LIVER） |
| **NOVA 代谢** | 醉酒值每秒降 **0.15%**（原版 4%+0.01/tick）——**NOVA 大幅放缓醉酒消退** |
| 枪械散射 | 每枪 +drunk×0.5 散布（DRUNKEN_BRAWLER 豁免） |
| 醉拳（grabbed_resisting） | 挣脱状态+1，失败伤害 clamp(钝伤/10, 3, 20) |
| Ballmer Peak | 科学家肝+5% 概率说彩蛋台词 |
| 外观 | 7 档：11 微红/21 红/41 酒气/51 醉态/61 烂醉/91 醉鬼 |

## 7.2 醉酒效果（NOVA 平滑曲线）

| 醉酒值 | 效果 |
|---|---|
| ≥43.4 | 口齿不清（prob(clamp(醉-8,0,100))×4s）+10% 混乱 4s |
| ≥63.4 | 眩晕 45s +15% 视物模糊 |
| ≥73.4 | 持续视物模糊 20s +3% 混乱 15s+呕吐（清毒素） |
| ≥83.4 | **持续中毒 +1/s** +5% 腹痛提示 |
| ≥93.4 | 中毒 +1/s + **脑损 0.4/s** + 长眠 90s（穿梭机在场豁免） |
| ≥103.4 | **中毒 +2/s**（必死） |

**NOVA 修改**：①阈值平滑（43.4/63.4/73.4/83.4/93.4/103.4，原 41/51/71/81/91/101）②**移除黑醉人格**（split_personality blackout，改为直接长眠）③代谢放缓 0.15%/tick ④TIPSY 阈值 23.4

---

# 八、过量系统

## 8.1 过量触发

```dm
if reagent.volume >= reagent.overdose_threshold && !reagent.overdosed:
    reagent.overdosed = TRUE
    reagent.overdose_start(owner)
```

**豁免**：TRAIT_OVERDOSEIMMUNE（过量免疫）

## 8.2 过量阈值全表（21 种药物，源码精确值）

| 试剂 | 阈值 | 过量效果 |
|---|---|---|
| **Omnizine** | **30u** | 伤害 |
| **Epinephrine 肾上腺素** | 30u | 心脏过速 |
| **Morphine 吗啡** | 30u | 呼吸抑制 |
| Atropine 阿托品 | 35u | — |
| Albuterol 沙丁胺醇 | 30u | — |
| Salicylic Acid 水杨酸 | 25u | — |
| Ephedrine 麻黄碱 | 30u | — |
| Mannitol 甘露醇 | **15u**（原文档误写 30） | 脑伤 |
| Rezadone | 30u | — |
| Oxandrolone 氧雄龙 | 25u | — |
| Saline-Glucose 盐水葡萄糖 | 60u | — |
| Calomel 甘汞 | 20u | — |
| Ammoniated Mercury 氨汞 | **10u** | — |
| Oculine | 30u | — |
| Stimulants 兴奋剂 | 60u | — |
| Modafinil 莫达非尼 | 20u | — |
| Psicodine | 30u | — |
| Metafactor 线粒体因子 | **10u** | — |
| Granibitaluri | 50u | — |
| Sanguirite 凝血剂 | 20u | — |
| Changeling Adrenaline | 30u | — |

---

# 九、成瘾系统

**代码**: `code/modules/reagents/withdrawal/`（7 类型）

## 9.1 成瘾触发

```dm
for each addiction, threshold in reagent.addiction_types:
    owner.mind.add_addiction_points(类型, 点)  // 点数按代谢量缩放
    if 点数 > 阈值: 触发成瘾
```

## 9.2 成瘾类型全录（7 种）

| 类型 | 说明 |
|---|---|
| **opioids 阿片类** | 吗啡/克罗地尔等 |
| **stimulants 兴奋剂** | 冰毒/麻黄碱等 |
| **alcohol 酒精** | 乙醇类 |
| **hallucinogens 致幻剂** | 幻觉类 |
| **maintenance_drugs 维持药** | 长期用药 |
| **medicine 药物** | 医疗依赖 |
| **nicotine 尼古丁** | 香烟 |

## 9.3 成瘾效果

缺药时：
- 戒断症状（对应类型）
- 情绪低落（mood event）
- 伤害积累
- 使用瘾物：暂时缓解

---

# 十、体温调节

**代码**: `life.dm` — `handle_environment()` / `natural_bodytemperature_stabilization()`

## 10.1 体温系统

| 常量 | 值 |
|---|---|
| **BODYTEMP_NORMAL** | **310.15K（37°C）** |
| BODYTEMP_HEAT_DAMAGE_LIMIT | 310.15+30 = **340.15K（67°C）** |
| BODYTEMP_AUTORECOVERY_DIVISOR | **28**（恢复除数） |
| BODYTEMP_AUTORECOVERY_MINIMUM | **3**（最小恢复增量） |
| BODYTEMP_COLD_DIVISOR | 15 |
| BODYTEMP_HEAT_DIVISOR | 15 |
| BODYTEMP_HEATING_MAX | 30 |

```dm
自然恢复 = 体温差 × 代谢效率 / 28
极冷(≤冷伤害线): max(恢复, 3)     // 快速升温
冷: max(恢复, min(温差, 3/4))      // 慢速升温
热: min(恢复, max(温差, -3/4))     // 慢速降温
极热(≥热伤害线): min(温差/28, -3)  // 快速降温
```

## 10.2 影响体温的因素

| 因素 | 方向 | 机制 |
|---|---|---|
| 环境温度 | 传导 | 温差×绝缘 |
| 衣服绝缘 | 减缓 | get_insulation_protection 反相 |
| 代谢效率 | 升温 | 恢复速度 ×代谢效率 |
| 燃烧/着火 | 急剧升温 | — |
| 体温调节药 | 稳定 | Leporazine |
| Freon 气体 | 急剧降温 | 制冷剂 |

**死亡后**：不自然稳定，体温逐步降至室温（火焰上则保持）。

---

# 十一、死后代谢与特殊状态

```dm
死后:
  reagents.metabolize(dead=TRUE, can_overdose=TRUE, liverless=TRUE)
  
  // 死后只有 REAGENT_DEAD_PROCESS 标记的试剂能代谢
  // 肝脏不工作 → 毒素不过滤（liverless）
  // 过量仍计算（can_overdose=TRUE）
  // Formaldehyde(甲醛) → 防腐(器官不腐烂)
```

**器官腐烂**（死后 handle_organs）：
- 含甲醛≥1u 或 Cryostylane → **不腐烂**
- 否则每个器官按 decay_factor 衰减
- 胃 decay 1.15×标准 ≈ 13 分钟（最早腐烂）

**特殊状态**：
- **TRAIT_STASIS**：只处理 REAGENT_IGNORE_STASIS 试剂
- **TRAIT_FAKEDEATH**：假死（醉酒检查外观豁免）
- **TRAIT_TOXIMMUNE**：毒免疫（跳过肝损伤）
- **TRAIT_TOXINLOVER**：爱毒（跳过肝损伤）

---

# 十二、试剂纯度系统

**代码**: `mob_life.dm` — `process_mob_reagent_purity()`（181-199 行）

> **审核补充**：原文档完全缺失。

```dm
// 试剂加入体内时的纯度处理:
if 纯度 == 1: 完美，直接加入
if 纯度 < inverse_chem_val 且有反转试剂:
    全部转为反转试剂(inverse_chem)  // 低纯度试剂反转
    若反转试剂有 REAGENT_SNEAKYNAME: 名称伪装为原试剂（隐藏负面）
else: 保留
```

**影响**：低纯度试剂（劣质制作）可能**反转成有害副产物**（如劣质毒品变成毒素），且名称可能被伪装。

---

# 十三、完整代谢流程图

```
[摄入]
  口     [气体]      [注射]
   ↓       ↓           ↓
  胃     肺    ←→   [血液(试剂)]
   │(转移)  ↓(溶解)     ↓
   └──────→→→┤         │
              ├→ metabolize() ←── 温度影响
              │    ↓
              ├→ 肝脏检查(耐受倍数)
              │    ↓ 毒素<耐受 → 无害代谢
              │    ↓ 毒素>耐受 → 进血液+肝损伤
              │
              ├→ 血型试剂检查
              │    ↓ 对应试剂+血量<560 → 回血0.25/s
              │
              ├→ 过量检查
              │    ↓ >阈值 → 过量效果
              │
              ├→ 成瘾检查
              │    ↓ 积累点数 → 上瘾
              │
              ├→ 堆叠代谢效应
              │    ↓ ≥2试剂组合效果
              │
              ├→ on_mob_life()
              │    ↓ 每种试剂独特效果
              │    ├── 药效(治疗/增益)
              │    ├── 毒效(伤害/debuff)
              │    ├── 营养(食物→饥饿系统)
              │    └── 酒精(→醉酒系统)
              │
              └→ metabolize_reagent()
                   ↓ 减去volume
                   试剂耗尽 → 效果终止
```

### 代谢速度速查

| 物质 | 速率 | 说明 |
|---|---|---|
| **水** | ~5 秒 | 快速代谢 |
| **食物营养** | 胃转移 0.05×体积+0.25 | 慢慢释放 |
| **药物** | 0.4u/tick 标准 | 代谢效率×营养 |
| **毒素** | 0.4u/tick 标准 | 肝耐受内无害 |
| **酒精** | 0.2u/tick | 较慢（醉酒值另算） |
| **Cryoxadone** | 仅低温有效 | 低于 170K 才代谢 |
| **死亡后药物** | 停止 | 除非 DEAD_PROCESS 标记 |
| **血液恢复** | 0.25/s | 血型试剂+血量<560 |

---

# 附录 · 代码路径索引

| 系统 | 文件 | 行数 |
|---|---|---|
| 代谢核心 | `code/modules/reagents/chemistry/holder/mob_life.dm` | 215 |
| 试剂容器 | `code/modules/reagents/chemistry/holder/holder.dm` | 820 |
| 生命循环 | `code/modules/mob/living/carbon/life.dm` | 852 |
| 碳基通用 | `code/modules/mob/living/carbon/carbon.dm` | ~750 |
| 移动饥饿 | `code/modules/mob/living/carbon/carbon_movement.dm` | ~30 |
| 胃消化 | `code/modules/surgery/organs/internal/stomach/_stomach.dm` | 595 |
| 醉酒 | `code/datums/status_effects/debuffs/drunk.dm` | 279 |
| 成瘾 | `code/modules/reagents/withdrawal/`（7 类型） | ~400 |
| 药物过量 | `code/modules/reagents/chemistry/reagents/medicine_reagents.dm` | ~350 |
| 乙醇试剂 | `code/modules/reagents/chemistry/reagents/ethanol_reagents.dm` | — |
| 血液常量 | `code/__DEFINES/mobs.dm` | 30+ |
| 体温常量 | `code/__DEFINES/atmospherics/atmos_mob_interaction.dm` | 70 |
| 营养常量 | `code/__DEFINES/mobs.dm`（HUNGER_FACTOR 等） | — |
| 器官基类 | `code/modules/surgery/organs/_organ.dm` | — |

> **审核结论**：重置版覆盖全部代谢子系统——试剂代谢核心、肝脏解毒、血型血液、胃消化营养、醉酒、过量、成瘾、体温、死后代谢、纯度系统，全部为源码精确数值（已修正原文档 3 处错误、补齐 5 大缺失系统）。