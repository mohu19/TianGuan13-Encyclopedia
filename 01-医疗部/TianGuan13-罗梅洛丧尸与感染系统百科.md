# TianGuan13 罗梅洛丧尸与僵尸系统百科 (Romero Zombie & Infection Encyclopedia)

> 基于 TianGuan13 NovaSector 分支源码全量整理。核心：`code/modules/zombie/`（items.dm 93 行 + organs.dm 135 行）+ `code/modules/mob/living/carbon/human/species_types/zombies.dm`（206 行）+ `code/modules/surgery/operations/operation_zombie.dm`（76 行）+ `code/modules/reagents/chemistry/reagents/other_reagents.dm`（Romerol 试剂段）。
> **范围**：罗梅洛丧尸（Romerol）是什么、4 种制造/感染途径、3 种丧尸物种、感染全流程、丧尸爪战斗、丧尸器官、战术与应对。

---

## 一、罗梅洛丧尸概述

**罗梅洛（Romerol）** 是一种高度实验性的**生物恐怖剂**（bioterror agent），在源码注释里被称为"真正的丧尸粉"（the REAL zombie powder）。它的名字致敬乔治·A·罗梅罗（George A. Romero）——现代丧尸电影之父。

> **源码描述**：Romerol 会在受试者大脑灰质中蚀刻出休眠结节。这些结节只在宿主死亡时激活，届时次级结构接管宿主身体。（`other_reagents.dm:2644`）

**核心特征**：
- **静默感染**：接触/口服/注射/吸入即植入感染器官，**无任何提示**（`feel_for_damage` 返回空）
- **死后激活**：宿主死亡后 45-70 秒内复活为丧尸
- **可传播**：丧尸爪攻击其他人类 100% 感染（弱化版 50%）

---

## 二、制造/获取途径（4 种）

### 2.1 辛迪加 Uplink 购买（最直接）

| 属性 | 值 |
|---|---|
| 名称 | Romerol（stealthy_weapons 分类） |
| 价格 | **25 TC** |
| 内容 | Romerol 瓶 + 注射器 + 滴管（`syndie_kit/romerol`） |
| 限制 | 低人口地图限制 + **进度 30 分钟**后可购买 |
| 购买者 | 叛徒/核战特工（间谍不可） |

### 2.2 手术"诱发生物坏死"（Bionecrosis）

> **前置科技树完整链路**（见下方 2.2a 专章）——该手术是**隐藏外星科技**，需要从蓝空间+外星科技解锁。

**代码**: `code/modules/surgery/operations/operation_zombie.dm`

| 属性 | 值 |
|---|---|
| 手术名 | Induce Bionecrosis（诱发生物坏死）/ Bionecroplasty (Necrotic Revival) |
| 原理 | 在患者**脑内植入 Romerol 肿瘤** |
| 时长 | **5 秒** |
| 前置状态 | 皮肤切开 + 骨骼锯开（SURGERY_SKIN_OPEN \| SURGERY_BONE_SAWED） |
| 阻塞状态 | 血管未夹闭（SURGERY_VESSELS_UNCLAMPED） |
| 所需试剂 | 工具或患者体内 **>1u** rezadone（复生丹）**或** zombiepowder（僵尸粉） |
| 消耗 | 手术成功后消耗 1u 试剂 |
| 限定 | 脑部必须存在、且未已有感染 |
| 标志 | MORBID（病态）+ LOCKED（初始锁定需研究）+ NOTABLE |
| 研究名 | Bionecroplasty (Necrotic Revival) |

> **制造流程**：搞到 rezadone 或 zombiepowder → 开皮 + 锯骨 → 5 秒手术 → 脑内植入感染器官 → 等目标死亡 → 复活为丧尸。


### 2.2a 手术解锁：科技树完整链路（Bionecroplasty）

> **本手术是隐藏科技（hidden = TRUE）**，不在常规科技树显示，需要扫描外星物品才能出现。

#### ① 手术设计（research design）

**代码**: `code/modules/research/designs/medical_designs.dm:1425`

| 属性 | 值 |
|---|---|
| 设计 | `/datum/design/surgery/necrotic_revival` |
| ID | `surgery_zombie` |
| 手术 | `/datum/surgery_operation/limb/bionecrosis` |
| 图标 | surgery_head |
| 解锁方式 | 科技树节点（非直接设计） |

#### ② 科技节点：Alien Surgery（外星手术）

**代码**: `code/modules/research/techweb/nodes/alien_nodes.dm:73`

| 属性 | 值 |
|---|---|
| 节点 ID | TECHWEB_NODE_ALIEN_SURGERY |
| 名称 | **Alien Surgery**（外星手术）——"Abductors did nothing wrong." |
| 费用 | **5 级点数**（TECHWEB_TIER_5_POINTS） |
| **隐藏** | ✅（hidden = TRUE，需扫描解锁） |
| 折扣实验 | 史莱姆硬扫描（TECHWEB_TIER_5_POINTS） |
| 公告 | 医疗频道 |

**前置节点（2 个，需同时满足）**：
| 前置 | 来源 |
|---|---|
| **Alien Technology**（外星科技） | alien_nodes.dm:9 |
| **Advanced Surgery Tools**（高级手术工具） | surgery_nodes.dm:76 |

**解锁的设计（同节点 11 个）**：外星烧灼器/外星钻/外星止血钳/外星牵开器/外星锯/外星手术刀（外星手术工具全套）+ 医疗机器人升级 4 级 + 脑洗手术 + 脑洗手术（机械）+ femto 治疗组合升级 + **surgery_zombie（本手术）**

**解锁需扫描的外星物品（10 种）**：外星烧灼器/外星圆锯/外星撬棍/外星能量枪/缩小射线枪/外星止血钳/外星电击棒/外星多功能工具/外星牵开器/外星手术刀/外星螺丝刀/外星手术钻/外星焊枪/外星剪线钳/外星扳手 + 外星矿物板

#### ③ 前置节点链（完整展开）

```
Applied Bluespace（应用蓝空间）
 └→ Bluespace Travel（蓝空间旅行）        ← 二级科技（research_nodes.dm:55）
     └→ Alien Technology（外星科技）      ← 扫描外星物品解锁
         └─┐
Advanced Surgery（高级手术）
 └→ Experimental Surgery（实验外科）      ← surgery_nodes.dm:43
     └→ Advanced Surgery Tools（高级手术工具） ← 4 级点数（surgery_nodes.dm:76）
         └─┘
          └→ Alien Surgery（外星手术）    ← 5 级点数 + 隐藏（alien_nodes.dm:73）
              └→ 解锁 surgery_zombie → Bionecrosis 手术
```

#### ④ 手术本身前置条件（操作时）

| 条件 | 要求 |
|---|---|
| 前置状态 | 皮肤切开 + 骨骼锯开 |
| 阻塞状态 | 血管未夹闭 |
| 所需试剂 | rezadone 或 zombiepowder **>1u**（工具或患者体内） |
| 目标 | 脑部存在 + 未已有感染 |
| 时长 | 5 秒 |

> **实用建议**：rezadone（复生丹）是医疗常规药（可合成），比 zombiepowder（毒素，假死）更好获取——手术时把试剂装进注射器/滴管里用就行。完整科技链（应用蓝空间→蓝空间旅行→外星科技→高级手术工具→外星手术）是**科研部大后期**内容，通常只有 R&D 深度研究 + 获得外星样本后才能解锁。


### 2.3 罗梅洛披萨（20% 概率彩蛋）

**代码**: `code/modules/cargo/packs/organic.dm`

- 披萨板条箱（pizza crate）发货时，**20% 概率**（romerol_chance = 0.2）某块披萨被注入 Romerol（每块剩余切片对应试剂单位）
- 吃下这披萨 → 静默感染 → 死后变丧尸
- 生成时会记录日志 + 管理员广播（"A romerol pizza was created in a pizza crate delivery."）

### 2.4 雨风暴（稀有化学雨）

**代码**: `code/datums/weather/weather_types/rain_storm.dm`

- 特殊"化学雨"天气的 `allowed_exotic_reagents` 列表包含 **Romerol**（与 CLF3/火焰药水/致幻剂等并列）
- 淋到带 Romerol 的雨 → 接触感染

---

## 三、丧尸物种（3 种 + 1 亚型）

### 3.1 高级功能丧尸（High-Functioning Zombie）

**代码**: `zombies.dm:2`（`/datum/species/zombie`）

| 属性 | 值 |
|---|---|
| 名称 | High-Functioning Zombie（高级功能丧尸） |
| 肉 | 丧尸人肉排（mutant meat） |
| 舌 | 丧尸舌 |
| 生物类型 | 亡灵 + 类人（MOB_UNDEAD \| MOB_HUMANOID） |
| 体温 | 无自然体温（T0C，环境调节）；火焰温度受损，极寒以下受损 |
| 生成 | 万圣节可选（check_roundstart_eligible：HALLOWEEN 时 TRUE） |

**固有特质（18 个）**：
| 特质 | 效果 |
|---|---|
| TRAIT_BLOODY_MESS | 血腥狼藉 |
| TRAIT_EASILY_WOUNDED | 易受伤口 |
| TRAIT_EASYDISMEMBER | 肢体易脱落 |
| TRAIT_FAKEDEATH | 可装死 |
| TRAIT_LIMBATTACHMENT | 肢体可重新装上 |
| TRAIT_LIVERLESS_METABOLISM | 无肝代谢 |
| TRAIT_NOBREATH | 不需呼吸 |
| TRAIT_NODEATH | 不会死 |
| TRAIT_NOCRITDAMAGE | 无临界伤害 |
| TRAIT_NOHUNGER | 无饥饿 |
| TRAIT_NO_DNA_COPY | 无法复制 DNA |
| TRAIT_NO_ZOMBIFY | 不可被再转化 |
| TRAIT_RADIMMUNE | 辐射免疫 |
| TRAIT_RESISTCOLD | 耐寒 |
| TRAIT_RESISTHIGHPRESSURE | 耐高压 |
| TRAIT_RESISTLOWPRESSURE | 耐低压 |
| TRAIT_TOXIMMUNE | 毒素免疫 |
| **TRAIT_NOBLOOD**（独有） | 无血液 |
| **TRAIT_SUCCUMB_OVERRIDE**（独有） | 不受屈服限制 |

> 描述：亡灵完全免疫环境危害和除了钝器创伤与灼烧外的一切物理威胁；肢体容易掉但能安回去。

### 3.2 感染性丧尸（Infectious Zombie）★ 核心

**代码**: `zombies.dm:83`（`/datum/species/zombie/infectious`）

| 属性 | 值 |
|---|---|
| 名称 | Infectious Zombie（感染性丧尸） |
| 伤害修正 | **20**（120 总伤害 KO，普通人的 5 倍） |
| 眼 | 丧尸眼（突变） |
| 脑 | 丧尸脑 |
| 舌 | 丧尸舌 |
| 腿 | 感染性丧尸腿（更慢） |
| 耐力修正 | ×0.33（耐力抗性） |
| 眩晕 | 眩晕上限 **2 秒**（spec_stun） |
| 战斗模式 | 强制锁定（TRAIT_COMBAT_MODE_LOCK） |
| 生成 | 不可开局选择 |

**固有特质（含 3 个独有）**：
| 特质 | 效果 |
|---|---|
| 共享丧尸特质 | 同 3.1 前 17 个（无 NOBLOOD/SUCCUMB） |
| **TRAIT_STABLEHEART** | 稳定心脏（可流血但不需要心脏） |
| **TRAIT_STABLELIVER** | 稳定肝脏 |
| **TRAIT_APATHETIC** | 冷漠（没有心情——没脑子） |

**再生组件**（`/datum/component/regenerator`）：
| 参数 | 值 |
|---|---|
| 再生延迟 | **6 秒** |
| 钝伤/灼伤/毒素 | **0.5/秒** |
| 缺氧 | 0.25/秒 |
| 治疗伤口 | ✅（heals_wounds） |

**感染器官处理**：转化时自动插入 `zombie_infection` 器官（若无），带 `mutant_hands` 组件（丧尸爪）。

### 3.3 无心智感染性丧尸（Mindless Infectious Zombie）

**代码**: `zombies.dm:174`

| 属性 | 值 |
|---|---|
| 名称 | Mindless Infectious Zombie（无心智丧尸） |
| 再生延迟 | **10 秒**（更慢） |
| 再生量 | **0.2/秒**（更弱） |
| 丧尸爪 | 弱化版（force 16 / 感染 50%） |
| 移速 | **×0.75 减速**（multiplicative_slowdown） |
| 来源 | **无 mind 的尸体**复活时（鼓励感染真玩家而非乱葬岗尸体） |

### 3.4 鳄鱼人（Krokodil Human）

**代码**: `zombies.dm:193`

- 鳄鱼药（krokodil）成瘾者的最终形态——皮肤脱落成丧尸外观
- 不是真丧尸（id = SPECIES_ZOMBIE_KROKODIL），无感染能力，仅外观丧尸化

---

## 四、感染全流程

### 4.1 感染植入（try_to_zombie_infect）

**代码**: `items.dm:31` + `organs.dm`

| 条件 | 详情 |
|---|---|
| 命中要求 | 丧尸爪命中（感染几率 100%/弱 50%）或 Romerol 试剂接触 |
| 头部 | 目标必须**有头**（无头不可感染） |
| TRAIT_NO_ZOMBIFY | 有此特质者不可感染 |
| 太空霉素 | `TRAIT_VIRUS_RESISTANCE`（病毒抗性）+ 无免疫缺陷 → **75% 概率阻断感染** |
| 肢体护甲 | 厚材料（THICKMATERIAL）+25 护甲；护甲 > 肢体伤害 → 不感染 |

### 4.2 潜伏期（process）

感染器官在宿主体内每 tick 处理：
- **毒伤**：活体且非丧尸时，**0.5 毒/秒**（nodamage 版不造成伤害）
- **5% 概率**提示"[诡异感受]"
- 宿主**死亡后** → 启动复活计时器

### 4.3 复活（zombify）

| 阶段 | 详情 |
|---|---|
| 计时 | 死亡后 **45-70 秒**（随机） |
| 转化 | 有 mind → 感染性丧尸；无 mind → 无心智丧尸 |
| 复活 | 完全治愈 + 复活（首次站起） |
| 效果 | "抽搐着站起来，眼中燃起贪婪的饥饿" |
| 眩晕 | 3 秒踉跄（jitter + Stun） |
| 幽灵接管 | 无 mind 尸体会向幽灵广播"想成为丧尸吗？"（ghost_direct_control） |

### 4.4 死亡终结

- 已丧尸化的宿主**再次死亡** → 感染器官销毁（"你死得太彻底，不再是丧尸了"）
- 但高级功能丧尸有 TRAIT_NODEATH（不会死）+ TRAIT_NOCRITDAMAGE

---

## 五、丧尸爪战斗（Zombie Claw）

**代码**: `items.dm:2`

| 属性 | 值 |
|---|---|
| 伤害 | **21**（force，刚好破气闸门） |
| 伤口加成 | -30（易开伤口） |
| 暴露伤口加成 | +15 |
| 锋利 | SHARP_EDGED |
| 音效 | 僵尸咆哮 |
| 感染 | 命中人类 100% 尝试感染 |
| 弱化版 | force 16 / 感染 50% / 拆解 ×1.33 |

**食尸回血**（check_feast）：攻击**尸体**（stat == DEAD）时：
- 尸体**粉碎**（gib，DROPS 全部残留）
- 恢复 = 尸体最大生命值（HP 全额）的**钝伤+毒+灼伤+脑损**全治疗
- 营养补满至饱腹（FULL）
- 日志记录"被丧尸吞噬"

**丧尸爪自杀**：自砍头（BRUTELOSS）。

---

## 六、丧尸器官（Zombie Organs）

| 器官 | 代码 | 效果 |
|---|---|---|
| 丧尸感染（festering ooze） | `organs.dm:2` | 核心感染器官，头部槽位（ORGAN_SLOT_ZOMBIE） |
| 丧尸舌 | `/obj/item/organ/tongue/zombie` | 丧尸语言 |
| 丧尸眼 | `/obj/item/organ/eyes/zombie` | 感染性丧尸突变眼 |
| 丧尸脑 | `/obj/item/organ/brain/zombie` | 感染性丧尸突变脑 |
| 丧尸头/胸/臂/腿 | `bodypart/xxx/zombie` | 全套丧尸肢体（易脱落+可重装） |

---

## 七、应对与攻略

### 感染后（还没死）
1. **太空霉素**（spaceacillin）——有病毒抗性特质时 75% 概率防感染
2. **无头化**——砍掉头无法感染（但没头你也活不久）
3. 检查脑部手术——Bionecrosis 手术可被反制（挖出感染器官）
4. 死后 45-70 秒是窗口期——烧尸/斩首/挖脑防复活

### 对抗丧尸
1. **钝器 + 灼烧**——丧尸免疫环境/毒素/辐射，但吃钝伤和火
2. **总伤害 120**（普通 20 KO 的 5 倍）——要狠打
3. **砍四肢**——肢体易脱落（EASYDISMEMBER），但会装回去（LIMBATTACHMENT）
4. **斩首**——感染需要头，无头丧尸无法传播
5. **眩晕上限 2 秒**——眩晕控制效率低，用伤害压制
6. **别让它打尸体**——食尸会全额回血

### 当丧尸（玩家）
1. **优先感染活人**——有 mind 的转化者更强（无心智版是弱化 50% 感染+慢速）
2. **食尸回血**——打尸体全额回血，续航关键
3. **破门**——爪 21 伤刚好破气闸
4. **游走**——6 秒再生 + 0.5/秒，边打边回
5. **注意火**——火焰温度会烧死丧尸（bodytemp_heat_damage_limit = FIRE_MINIMUM_TEMPERATURE_TO_EXIST）

---

## 八、彩蛋与梗

- **罗梅洛命名**：致敬乔治·罗梅罗（《活死人之夜》导演）
- **"Sincerely, the Zombies!"**：物种描述——丧尸每年万圣节来袭吓唬船员
- **植物学家世仇**：丧尸 lore"与植物学家长期不和——上次事件涉及防御性植物的草坪，让它们非常抓狂"
- **Romerol 口味**："brains"（脑子）
- **Zombie Powder 口味**："death"（死亡）
- **披萨彩蛋**：20% 概率的罗梅洛披萨——"黑暗料理"巅峰
- **扫描门**：`scanner_gate.dm:236` 能检测 "Romerol infection"——安保可查感染
- **太空废墟**：`derelict_sulaco.dm` 的废弃 Interdyne 设施"因意外发现 Romerol 而废弃"
- **"BRAAAINS!"**：食尸时 Zom Bee 喊"BRAAAAISNSs!1!"

---

## 附录 · 代码路径索引

| 系统 | 文件 | 行数 |
|---|---|---|
| 丧尸爪/感染/食尸 | `code/modules/zombie/items.dm` | 93 |
| 感染器官/复活 | `code/modules/zombie/organs.dm` | 135 |
| 丧尸物种 3 型 | `code/modules/mob/living/carbon/human/species_types/zombies.dm` | 206 |
| 生物坏死手术 | `code/modules/surgery/operations/operation_zombie.dm` | 76 |
| Romerol 试剂 | `code/modules/reagents/chemistry/reagents/other_reagents.dm` L2644 | — |
| Zombie Powder | `code/modules/reagents/chemistry/reagents/toxin_reagents.dm` L340 | — |
| Uplink Romerol 包 | `code/game/objects/items/storage/uplink_kits.dm` L570 | — |
| Uplink 条目（25 TC） | `code/modules/uplink/uplink_items/nukeops.dm` L790 | — |
| 罗梅洛披萨 | `code/modules/cargo/packs/organic.dm` L188 | — |
| 雨风暴 Romerol | `code/datums/weather/weather_types/rain_storm.dm` L218 | — |
| Romerol 瓶 | `code/modules/reagents/reagent_containers/cups/bottle.dm` L282 | — |
| 扫描门检测 | `code/game/machinery/scanner_gate.dm` L236 | — |

---

> **索引**：本页共 1 篇，覆盖罗梅洛丧尸系统全量（4 制造途径 + 3 物种 + 感染流程 + 丧尸爪 + 器官 + 战术 + 彩蛋）。
