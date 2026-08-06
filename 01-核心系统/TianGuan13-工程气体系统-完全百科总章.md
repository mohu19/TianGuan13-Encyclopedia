# 天关工程气体系统 — 完全百科总章

> **基于**: TianGuan13 源码 (Nova Sector → /tg/station)
> **覆盖**: 65+ 文件, ~20,000+ 行 DM 代码
> **本篇整合**: 13篇专题文档的全部数据，未做任何删减

---

## 目录

- [第一卷：大气基础](#第一卷大气基础)
  - [第1章 · 20种气体完全数据](#第1章--20种气体完全数据)
    - [1.1 气体通用属性](#11-气体通用属性)
    - [1.2 出口价值排行](#12-出口价值排行)
    - [1.3 人体吸入效果](#13-人体吸入效果)
    - [1.4 生产链总图](#14-生产链总图)
  - [第2章 · 气体反应网络](#第2章--气体反应网络)
    - [2.1 反应系统核心](#21-反应系统核心)
    - [2.2 燃烧反应(4种)](#22-燃烧反应4种)
    - [2.3 合成反应(10种)](#23-合成反应10种)
    - [2.4 分解反应(3种)](#24-分解反应3种)
    - [2.5 特殊反应(4种)](#25-特殊反应4种)
    - [2.6 电解器反应(3种)](#26-电解器反应3种)
    - [2.7 反应网络总图](#27-反应网络总图)
    - [2.8 策略总结](#28-策略总结)
- [第二卷：管道工程](#第二卷管道工程)
  - [第3章 · 管道与组件系统](#第3章--管道与组件系统)
    - [3.1 系统架构](#31-系统架构)
    - [3.2 管道类型](#32-管道类型)
    - [3.3 管道颜色系统](#33-管道颜色系统)
    - [3.4 二元设备](#34-二元设备)
    - [3.5 三元设备](#35-三元设备)
    - [3.6 一元设备](#36-一元设备)
    - [3.7 便携设备](#37-便携设备)
    - [3.8 其他设备](#38-其他设备)
    - [3.9 特殊加工机](#39-特殊加工机)
    - [3.10 管线网络物理系统](#310-管线网络物理系统)
    - [3.11 恒温机](#311-恒温机)
    - [3.12 气阀警报系统](#312-气阀警报系统)
    - [3.13 默认端口分配速查](#313-默认端口分配速查)
    - [3.14 工程实务指南](#314-工程实务指南)
  - [第4章 · 压力安全与爆炸系统](#第4章--压力安全与爆炸系统)
    - [4.1 所有容器压力等级总表](#41-所有容器压力等级总表)
    - [4.2 手持气罐爆炸系统](#42-手持气罐爆炸系统)
    - [4.3 便携气罐爆炸系统](#43-便携气罐爆炸系统)
    - [4.4 泄漏系统(Gas Leaker)](#44-泄漏系统gas-leaker)
    - [4.5 Hypernoblium晶体抑爆原理](#45-hypernoblium晶体抑爆原理)
    - [4.6 固定气罐损伤机制](#46-固定气罐损伤机制)
    - [4.7 工程安全策略](#47-工程安全策略)
- [第三卷：发电引擎](#第三卷发电引擎)
  - [第5章 · 超物质引擎](#第5章--超物质引擎)
    - [5.1 SM核心属性](#51-sm核心属性)
    - [5.2 SM变体](#52-sm变体)
    - [5.3 SM处理循环](#53-sm处理循环)
    - [5.4 气体对SM的影响](#54-气体对sm的影响)
    - [5.5 温度极限公式](#55-温度极限公式)
    - [5.6 伤害计算](#56-伤害计算)
    - [5.7 能量系统](#57-能量系统)
    - [5.8 高能量行为](#58-高能量行为)
    - [5.9 辐射系统](#59-辐射系统)
    - [5.10 物质吞噬](#510-物质吞噬)
    - [5.11 心理学系统](#511-心理学系统)
    - [5.12 SM闪电系统](#512-sm闪电系统)
    - [5.13 解体机制](#513-解体机制)
    - [5.14 级联Cascade](#514-级联cascade)
    - [5.15 碎片剽窃](#515-碎片剽窃)
    - [5.16 SM废气排放](#516-sm废气排放)
    - [5.17 SM引力](#517-sm引力)
    - [5.18 操作指南](#518-操作指南)
  - [第6章 · HFR聚变反应堆](#第6章--hfr聚变反应堆)
    - [6.1 HFR架构总览](#61-hfr架构总览)
    - [6.2 7种燃料配方](#62-7种燃料配方)
    - [6.3 聚变等级系统](#63-聚变等级系统)
    - [6.4 主处理循环](#64-主处理循环)
    - [6.5 不稳定度系统](#65-不稳定度系统)
    - [6.6 能量与功率公式](#66-能量与功率公式)
    - [6.7 损伤治疗系统](#67-损伤治疗系统)
    - [6.8 调节剂溢出系统](#68-调节剂溢出系统)
    - [6.9 核粒子闪电幻觉引力](#69-核粒子闪电幻觉引力)
    - [6.10 熔毁系统](#610-熔毁系统)
    - [6.11 冷却系统](#611-冷却系统)
    - [6.12 警报与完整性](#612-警报与完整性)
    - [6.13 电源系统](#613-电源系统)
    - [6.14 完整常量表](#614-完整常量表)
    - [6.15 操作指南](#615-操作指南)
  - [第7章 · 奇点引擎](#第7章--奇点引擎)
    - [7.1 奇点基础](#71-奇点基础)
    - [7.2 奇点吞噬系统](#72-奇点吞噬系统)
    - [7.3 碰撞箱规则](#73-碰撞箱规则)
    - [7.4 约束系统](#74-约束系统)
    - [7.5 操作指南](#75-操作指南)
    - [7.6 暗物质奇点与NarSie](#76-暗物质奇点与narsie)
    - [7.7 三引擎对比](#77-三引擎对比)
- [第四卷：气体加工](#第四卷气体加工)
  - [第8章 · 结晶器与电解器](#第8章--结晶器与电解器)
    - [8.1 气体结晶器](#81-气体结晶器)
    - [8.2 完整配方表](#82-完整配方表)
    - [8.3 产物功能说明](#83-产物功能说明)
    - [8.4 电解器](#84-电解器)
    - [8.5 工程应用产线](#85-工程应用产线)
- [第四卷B：发电系统（深挖补充）](#第四卷b发电系统深挖补充)
  - [第8B章 · 温差发电 (TEG)](#第8b章--温差发电-teg)
  - [第8C章 · 燃气涡轮 (Gas Turbine)](#第8c章--燃气涡轮-gas-turbine)
  - [第8D章 · 太阳能 (Solar)](#第8d章--太阳能-solar)
  - [第8E章 · RTG 放射性同位素发电机](#第8e章--rtg-放射性同位素发电机)
  - [第8F章 · 引力发生器 (Gravity Generator)](#第8f章--引力发生器-gravity-generator)
  - [第8G章 · 特斯拉引擎 (Tesla)](#第8g章--特斯拉引擎-tesla)
  - [第8H章 · 补充机制](#第8h章--补充机制核查补齐)
  - [第8I章 · 电力网基础设施（APC/SMES/电缆/电网）](#第8i章--电力网基础设施apcsmest电缆电网)
- [第五卷：实验军火](#第五卷实验军火)
  - [第9章 · TTV炸弹制造](#第9章--ttv炸弹制造)
    - [9.1 基础概念](#91-基础概念)
    - [9.2 气体选择与配比](#92-气体选择与配比)
    - [9.3 清洗](#93-清洗)
    - [9.4 气罐加压](#94-气罐加压)
    - [9.5 TTV组装](#95-ttv组装)
    - [9.6 运输与安全](#96-运输与安全)
    - [9.7 引爆流程](#97-引爆流程)
    - [9.8 进阶技术](#98-进阶技术)
  - [第10章 · 科研实验系统](#第10章--科研实验系统)
    - [10.1 气罐压缩机](#101-气罐压缩机)
    - [10.2 多普勒阵列](#102-多普勒阵列)
    - [10.3 完整工作流](#103-完整工作流)
- [附录](#附录)
  - [A. 关键常量表](#a-关键常量表)
  - [B. 代码路径索引](#b-代码路径索引)
  - [C. 勘误记录](#c-勘误记录)
  - [D. 散篇文档对照表](#d-散篇文档对照表)

---

# 第一卷 · 大气基础

## 第1章 · 20种气体完全数据

**代码**: `code/modules/atmospherics/gasmixtures/gas_types.dm` (333行)

### 1.1 气体通用属性

**核心常量**:
```dm
比热(specific_heat): 热容,越高越难升温
聚变功率(fusion_power): HFR中影响(+增速,-减速)
稀有度(rarity): 越高越常见(1000最高),越低越稀有(1极稀有)
价值(base_value): 货运出口单价系数
可见度(moles_visible): 多少mol才可见(MOLES_GAS_VISIBLE的倍数)
```

| 序号 | 气体 | ID | 比热 | 聚变 | 危险 | 稀有度 | 价值 | 颜色 |
|---|---|---|---|---|---|---|---|---|
| 01 | **氧气 O₂** | `o2` | 20 | 0 | 否 | 900 | 0.2 | 🔵 #0000ff |
| 02 | **氮气 N₂** | `n2` | 20 | 0 | 否 | 1000 | 0.1 | 🟡 #ffff00 |
| 03 | **二氧化碳 CO₂** | `co2` | 30 | 0 | ⚠️ | 700 | 0.2 | ⬜ #808080 |
| 04 | **等离子 Plasma** | `plasma` | **200** | 0 | ⚠️🔥 | 800 | 1.5 | 🩷 #ffc0cb |
| 05 | **水蒸气 H₂O** | `water_vapor` | 40 | +8 | 否 | 500 | 0.5 | 🟦 #b0c4de |
| 06 | **Hyper-noblium** | `hypernoblium` | **2000** | +10 | 否 | 50 | 2.5 | 青色 #008080 |
| 07 | **笑气 N₂O** | `n2o` | 40 | +10 | ⚠️ | 600 | 1.5 | 🟨 #ffe4c4 |
| 08 | **Nitrium** | `nitrium` | 10 | +7 | ⚠️ | **1** | **6** | 🟫 #a52a2a |
| 09 | **氚 Tritium** | `tritium` | 10 | +5 | ⚠️☢️ | 300 | 2.5 | 🟢 #32cd32 |
| 10 | **BZ** | `bz` | 20 | +8 | ⚠️ | 400 | 1.5 | 🟣 #9370db |
| 11 | **Pluoxium** | `pluoxium` | 80 | **-10** | 否 | 200 | 2.5 | 🔮 #7b68ee |
| 12 | **瘴气 Miasma** | `miasma` | 20 | 0 | ⚠️ | 250 | 1 | 🟫 #808000 |
| 13 | **氟利昂 Freon** | `freon` | 600 | **-5** | ⚠️❄️ | **10** | **5** | 🟦 #afeeee |
| 14 | **氢气 H₂** | `hydrogen` | 15 | +2 | ⚠️🔥 | 600 | 1 | ⬜ #ffffff |
| 15 | **Healium** | `healium` | 10 | 0 | ⚠️ | 300 | **5.5** | 🟥 #fa8072 |
| 16 | **Proto-Nitrate** | `proto_nitrate` | 30 | 0 | ⚠️ | 200 | 2.5 | 🟩 #adff2f |
| 17 | **Zauker** | `zauker` | 350 | 0 | ⚠️☠️ | **1** | **7** | 🟢 #006400 |
| 18 | **Halon** | `halon` | 175 | 0 | ⚠️ | 300 | 4 | 🟣 #800080 |
| 19 | **氦气 Helium** | `helium` | 15 | +7 | 否 | 50 | 3.5 | 🟦 #f0f8ff |
| 20 | **Antinoblium** | `antinoblium` | **1** | **+20** | ⚠️ | **1** | **10** | 🟥 #800000 |

### 1.2 出口价值排行

| 排名 | 气体 | 价值 | 稀有度 |
|---|---|---|---|
| 🥇 | **Antinoblium** | **10** | 1 |
| 🥈 | **Zauker** | **7** | 1 |
| 🥉 | **Nitrium** | **6** | 1 |
| 4 | **Healium** | 5.5 | 300 |
| 5 | **Freon** | 5 | 10 |
| 6 | **Halon** | 4 | 300 |
| 7 | **Helium** | 3.5 | 50 |
| 8 | **Hyper-noblium** | 2.5 | 50 |
| 9 | **Pluoxium** | 2.5 | 200 |
| 10 | **Proto-Nitrate** | 2.5 | 200 |
| 11 | **Tritium** | 2.5 | 300 |
| 12 | **Plasma** | 1.5 | 800 |
| 13 | **N₂O** | 1.5 | 600 |
| 14 | **BZ** | 1.5 | 400 |
| 15 | **Miasma** | 1 | 250 |
| 16 | **H₂** | 1 | 600 |
| 17 | **H₂O** | 0.5 | 500 |
| 18 | **O₂** | 0.2 | 900 |
| 19 | **CO₂** | 0.2 | 700 |
| 20 | **N₂** | 0.1 | 1000 |

### 1.3 人体吸入效果

**代码**: `mob/living/carbon/life.dm` — `handle_breath()`

**基础参数**:
```dm
safe_oxygen_min = 16       // 最低安全O₂分压
safe_co2_max = 10          // 最高安全CO₂分压
safe_plas_max = 0.05       // 最高安全Plasma分压
n2o_para_min = 1           // N₂O昏迷阈值
n2o_sleep_min = 5          // N₂O睡眠阈值
PLUOXIUM_PROPORTION = 8    // Pluoxium效率 = 8倍O₂
```

**各气体效果**:

| 气体 | 效果阈值 | 效果 |
|---|---|---|
| **O₂** | <16pp | 窒息, `min(5×16/pp, 3)` 缺氧损伤; 回复 `-5` |
| **CO₂** | >10pp | 咳嗽(20%) → 12秒后昏迷6秒 → 30秒后致死+8 |
| **Plasma** | >0.05pp | 毒素 `clamp(ratio×10, 1, 10)/tick` |
| **N₂O** | >0.01pp | 20%傻笑; **≥1pp**: 昏迷6秒; **≥5pp**: 强制睡眠 |
| **Nitrium** | >0.5pp | 灼伤 `pp×0.15`; >5pp 毒素 `pp×0.05` |
| **BZ** | >0.01pp | 幻觉+10秒; >1pp 幻觉+20秒 |
| **Freon** | >0pp | 冻伤 `pp×0.25` |
| **Miasma** | 0.25~5pp | 5% 臭味; 5~15pp 15%恶心; >30pp 25%呕吐 |
| **Pluoxium** | >0pp | **8倍O₂效率**, 代谢成Pluoxium试剂 |

**压力伤害**:
```dm
<20kPa: 真空危险(直接受伤)
20~50kPa: 低压警告
>550kPa: 高压危险, damage = pressure/550 × 2 (max 2)
```

### 1.4 生产链总览

```
[基础气体]
  O₂ N₂ Plasma CO₂
     ↓
[第一步合成]
  Plasma+O₂燃烧 → Tritium (O₂:Plasma>96:1时) / CO₂+H₂O
  H₂+O₂燃烧 → H₂O
  N₂+O₂+BZ → N₂O
  N₂O+Plasma → BZ
  N₂O+Tritium+BZ → Nitrium
     ↓
[第二步合成]
  Tritium+N₂ → Hyper-noblium (<15K)
  N₂O+BZ+CO₂ → Freon (>473K)
  BZ+Freon → Healium (25~300K)
  Plasma+CO₂+BZ → Halon
  O₂+CO₂(SM内) → Pluoxium
     ↓
[第三步合成] (高端)
  Pluoxium+H₂ → Proto-Nitrate (5000~10000K)
  BZ+H₂+H₂O → Zauker (50000~75000K)
     ↓
[第四步] (HFR独家)
  Hypernob+H₂/Tritium → Antinoblium
  Antinoblium自我复制
```

---

## 第2章 · 气体反应网络

**代码**: `code/modules/atmospherics/gasmixtures/reactions.dm` (1,240行)

### 2.1 反应系统核心

#### 优先级处理顺序

```dm
PRIORITY_PRE_FORMATION  (1) 前驱分解 — Nitrium分解/Halon/PN三阶段
PRIORITY_FORMATION      (2) 合成 — N₂O/BZ/Pluoxium/Nitrium/Freon/Noblium/Healium/Zauker/PN/Antinob
PRIORITY_POST_FORMATION (3) 后处理 — 水蒸气凝结/Miasma灭菌/N₂O分解/Zauker分解
PRIORITY_FIRE           (4) 燃烧 — Plasma/H₂/Tritium/Freon
```

**Hyper-noblium抑制**: ≥5mol 且 温度<20K → 阻止所有反应

#### 反应标志

```dm
NO_REACTION       = 0     无反应
REACTING          = 1<<0  正在反应
STOP_REACTIONS    = 1<<1  停止后续
VOLATILE_REACTION = 1<<2  挥发性(辐射/爆炸)
```

### 2.2 燃烧反应(4种)

#### 2.2.1 等离子燃烧 (Plasma Fire)

**代码**: `/datum/gas_reaction/plasmafire`
**描述**: O₂+Plasma → CO₂+H₂O 或 Tritium

| 参数 | 值 |
|---|---|
| 最低温度 | 373K (100°C) |
| 燃烧速率 | `(plasma/9) × temp_scale` 或 `(O₂/10/9) × temp_scale` |
| 耗氧比 | `1.4 - temp_scale` (随温度从1.4降到0.4) |
| 放热 | **3,000,000 J/mol** Plasma |
| Tritium条件 | O₂:Plasma > **96:1** → 产Tritium |
| 贫氧产物 | CO₂ 75% + H₂O 25% |

```dm
temp_scale = (temp - 373) / 1270  // 0~1

// 限制
plasma_burn_rate = min(计算值, plasma_moles, oxygen_moles / oxygen_burn_ratio)

// Tritium产线条件:
O₂:Plasma > 96:1 → 1 Plasma + 1.4 O₂ → 1 Tritium + 热
O₂:Plasma < 96:1 → 1 Plasma + O₂ → 0.75 CO₂ + 0.25 H₂O + 热
```

#### 2.2.2 氢气燃烧 (H₂ Fire)

| 参数 | 值 |
|---|---|
| 最低温度 | 373K |
| 燃烧速率 | `min(H₂/2, O₂/2/10, H₂, O₂/0.5)` |
| 配比 | 1 H₂ + 0.5 O₂ → 1 H₂O |
| 放热 | **2,800,000 J/mol** H₂ |

#### 2.2.3 氚燃烧 (Tritium Fire)

| 参数 | 值 |
|---|---|
| 最低温度 | 373K |
| 燃烧速率 | 同H₂ |
| 配比 | 1 Tr + 0.5 O₂ → 1 H₂O |
| 放热 | **2,800,000 J/mol** Tr |

**辐射条件**:
```dm
燃烧Tr > 0.1mol
能量释放 > 2,800,000 × (体积/2500)³
10%概率 → 辐射脉冲, 范围=√燃烧Tr/0.5 (最大20)
```

#### 2.2.4 Freon燃烧 (Freon Fire) — 唯一吸热!

| 参数 | 值 |
|---|---|
| 温度范围 | **20K~283K** (唯一低温火!) |
| 燃烧速率 | `(freon/4) × temp_scale` 或 `(O₂/10/4) × temp_scale` |
| 配比 | 1 Freon + ~1 O₂ → 1 CO₂ |
| **吸热** | **300,000 J/mol** Freon (降温!) |
| 热冰 | 120~160K 下2%概率生成 **hot_ice** |

```dm
temp_scale:
>60K:  0.5
20~60K: (283-temp)/(283-20)
<20K:  0 (停止)
```

### 2.3 合成反应(10种)

#### 2.3.1 N₂O 合成

| 参数 | 值 |
|---|---|
| 最低需求 | O₂=10mol, N₂=20mol, BZ=5mol |
| 温度范围 | **200~250K** (严格!) |
| 催化剂 | BZ (不消耗) |
| 放热 | 10,000 J/mol N₂O |
| 反应 | N₂ + 0.5 O₂ → N₂O |

#### 2.3.2 BZ 合成

| 参数 | 值 |
|---|---|
| 最低需求 | N₂O=10mol, Plasma=10mol |
| 最高温度 | **<313K**(40°C) — 低于燃烧温度防炸弹 |
| 环境效率 | `体积/压力` — 大体积低压好 |
| 比例效应 | N₂O:Plasma < 1:3 → N₂O开始分解 |
| 放热 | 80,000 J/mol BZ |
| 反应 | 0.4 N₂O + 0.8 Plasma → BZ |

#### 2.3.3 Pluoxium 合成

| 参数 | 值 |
|---|---|
| 温度范围 | **50~273K** |
| 最大速率 | **5 mol/tick** |
| 放热 | 250 J/mol Pluoxium |
| 反应 | CO₂ + 0.5 O₂ + 0.01 Tr → Pluoxium + 0.01 H₂ |

#### 2.3.4 Nitrium 合成 (吸热)

| 参数 | 值 |
|---|---|
| 最低需求 | Tr=20mol, N₂=10mol, BZ=5mol |
| 最低温度 | **>1500K** |
| 消耗 | BZ 0.05/mol Nitrium |
| **吸热** | 100,000 J/mol Nitrium |
| 反应 | Tr + N₂ + 0.05 BZ → Nitrium |

#### 2.3.5 Freon 合成 (吸热)

| 参数 | 值 |
|---|---|
| 最低需求 | Plasma=0.06mol, CO₂=0.03mol, BZ=0.01mol |
| 最低温度 | >473K (200°C) |
| 效率曲线 | 800K峰值 + **>5500K(3倍效率)** |
| **吸热** | ~100~800 J/mol (随温变) |
| 反应 | 0.6Plasma + 0.3CO₂ + 0.1BZ → Freon |

#### 2.3.6 Hyper-noblium 合成 (强放热)

| 参数 | 值 |
|---|---|
| 最低需求 | N₂=10mol, Tr=5mol |
| 温度范围 | **2.7~15K** (极度低温!) |
| 放热 | **20,000,000 J/mol** Noblium (最强!) |
| BZ催化 | 降低放热 + 减少Tr消耗 |
| 产量 | `min((N₂+Tr)×0.01, Tr/5, N₂/10)` |
| 反应 | 10N₂ + 5Tr → Noblium |

#### 2.3.7 Healium 合成

| 参数 | 值 |
|---|---|
| 温度范围 | **25~300K** |
| 速率 | `min(温度×0.3, Freon/2.75, BZ/0.25)` |
| 放热 | 9,000 J/3mol Healium |
| 反应 | 2.75Freon + 0.25BZ → 3Healium |

#### 2.3.8 Zauker 合成 (吸热)

| 参数 | 值 |
|---|---|
| 温度范围 | **50,000~75,000K** (极高温!) |
| 速率 | `min(温度×5e-6, Hypernob/0.01, Nitrium/0.5)` |
| **吸热** | 5,000 J/0.5mol Zauker |
| 反应 | 0.01Hypernob + 0.5Nitrium → 0.5Zauker |

#### 2.3.9 Proto-Nitrate 合成

| 参数 | 值 |
|---|---|
| 温度范围 | **5,000~10,000K** |
| 速率 | `min(温度×0.005, Pluoxium/0.2, H₂/2)` |
| 放热 | 650 J/2.2mol PN |
| 反应 | 2H₂ + 0.2Pluoxium → 2.2Proto-Nitrate |

#### 2.3.10 Antinoblium 复制

| 参数 | 值 |
|---|---|
| 最低需求 | Antinoblium ≥ 0.25mol |
| 最低温度 | >20K |
| 转化率 | `min(Anob/90, 非Anob总mol)` per tick |

唯一不需要其他气体的反应。

### 2.4 分解反应(3种)

#### 2.4.1 N₂O 分解 (放热)

| 参数 | 值 |
|---|---|
| 最低需求 | N₂O ≥ 0.02mol |
| 温度范围 | **1,400~100,000K** |
| 速率 | `(N₂O/2) × 抛物线系数` (峰=50,700K) |
| 放热 | 200,000 J/mol |
| 反应 | N₂O → N₂ + 0.5 O₂ |

#### 2.4.2 Nitrium 分解 (放热)

| 参数 | 值 |
|---|---|
| 最低需求 | O₂=0.01mol(催化), Nitrium=0.01mol |
| 最高温度 | **<343K**(70°C) |
| 速率 | `温度/3000` mol/tick (极慢) |
| 放热 | 30,000 J/mol |
| 反应 | Nitrium → H₂ + N₂ |

#### 2.4.3 Zauker 分解 (放热)

| 参数 | 值 |
|---|---|
| 最低需求 | N₂=0.01mol, Zauker=0.01mol |
| 最大速率 | **20 mol/tick** |
| 放热 | 460 J/mol |
| 反应 | Zauker + N₂ → 0.3 O₂ + 0.7 N₂ |

### 2.5 特殊反应(4种)

#### 2.5.1 水蒸气凝结

| 温度 | 效果 |
|---|---|
| **<200K**(-73°C) | 冻结地板 (permafrost) |
| **200~303K**(30°C) | 湿地板 (打滑) |

#### 2.5.2 干热灭菌 (Miasma→O₂)

| 参数 | 值 |
|---|---|
| 最低温度 | >443K (170°C) |
| 湿度限制 | 水汽比例 < 10% |
| 速率 | `20 + (temp-443)/20` mol/tick |
| 放热 | 0.002 J/mol |

#### 2.5.3 Halon 灭火

| 参数 | 值 |
|---|---|
| 最低需求 | Halon=0.01mol, O₂=0.01mol |
| 最低温度 | >343K (70°C) |
| 消耗 | **1 Halon : 20 O₂** |
| 产物 | **2.5 Pluoxium** / mol Halon |
| **吸热** | 2,500 J/mol Halon |
| 树脂 | 满足消耗时生成灭火树脂泡沫 |

**最强大灭火机制**!

#### 2.5.4 Proto-Nitrate 三阶段

**阶段①: PN + H₂ → 更多PN**
| 参数 | 值 |
|---|---|
| 最低需求 | PN≥0.01mol, H₂≥**150mol** |
| 最大速率 | 5 mol H₂/tick |
| **吸热** | 2,500 J/mol H₂ |

**阶段②: PN + Tritium → H₂ (放热+辐射)**
| 参数 | 值 |
|---|---|
| 温度范围 | **150~340K** |
| 放热 | 10,000 J/mol Tr |
| 辐射 | 能量>阈值释放脉冲 |

**阶段③: PN + BZ → 分解 (核粒子!)**
| 参数 | 值 |
|---|---|
| 温度范围 | **260~280K** |
| 放热 | 60,000 J/mol BZ |
| 核粒子 | `min(consumed/5, 6)` 个/次 |
| 辐射脉冲 | 同步释放 |
| 幻觉范围 | 附近碳基生物致幻 |

### 2.6 电解器反应(3种)

**代码**: `electrolyzer_reactions.dm` (139行)

#### 2.6.1 水电解

| 参数 | 值 |
|---|---|
| 反应 | 2H₂O → O₂ + 2H₂ |
| 速率 | `min(H₂O/2, 2.5×功率²)` mol/tick |

#### 2.6.2 Hypernob→Antinoblium (SM闪电电解)

| 参数 | 值 |
|---|---|
| 条件 | Hypernob + SM闪电功率>5GeV |
| 反应 | Hypernob → Antinob 1:1 |
| 速率 | `Hypernob × clamp((功率-5)/(9-5), 0, 1)` |

#### 2.6.3 Halon 电解生成

| 参数 | 值 |
|---|---|
| 反应 | BZ → 2Halon + 0.2O₂ |
| 放热 | 91,232 J/mol BZ |
| 速率 | `BZ × (1-e^(-0.5×温度×功率/373))` |

### 2.7 反应网络总图

```
                          ┌───┐
                          │空气│
                          └─┬─┘
                            │
         ┌──────────────────┼──────────────────┐
         ▼                  ▼                  ▼
    [Plasma燃烧]        [H₂燃烧]            [电解]
    O₂+Plasma≥373K    H₂+O₂≥373K         2H₂O→O₂+2H₂
         │                  │
         ├──O₂:Plasma>96?   └──→ H₂O
         │   是→Tritium
         │   否→CO₂+H₂O
         ▼
     ┌──Tritium──┐
     │           │
     ▼           ▼
  [Tr燃烧]    [N₂O合成] ← BZ催化
  辐射!           │
                 ▼
          ┌──── BZ ────┬──────────┐
          │            │          │
          │      [Nitrium]   [Freon合成]
          │      ≥1500K吸热   >473K吸热
          ▼           │          │
      [Halon电解]     ▼          ▼
      BZ→Halon    [Zauker]   [Healium]
                 50000K+     25-300K
                      │
                      ▼
               (遇N₂分解)
               
[Hypernob合成] ←──
 N₂+Tr, 2.7-15K  │
 20MJ/mol!        │
        │         │
        ▼         │
   [电解器+SM]    │
   Hnob→Anob     │
        │         │
        ▼         │
  [Antinob复制]   │
  吃掉其他→更多   │
        └────┬────┘
             │
             ▼
      [Pluoxium合成]
      50-273K
             │
             ▼
      [Proto-Nitrate合成]
      5000-10000K
             │
        ┌────┼────┐
        ▼    ▼    ▼
      +H₂  +Tr   +BZ
     →更多→H₂  →核粒子
     PN   辐射  幻觉+辐射
```

### 2.8 策略总结

**能源/发电反应**:
| 反应 | 放热量 | 用途 |
|---|---|---|
| Plasma燃烧 | 3,000,000 J/mol | SM/发电/造Tritium |
| H₂燃烧 | 2,800,000 J/mol | 快速高热 |
| Tr燃烧 | 2,800,000 J/mol+辐射 | 产热+辐射 |
| Hypernob合成 | 20,000,000 J/mol | 最高密度能源 |

**冷却反应**:
| 反应 | 吸热 | 用途 |
|---|---|---|
| Freon燃烧 | 300,000 J/mol | **终极冷却** |
| Halon | 2,500 J/mol | 灭火冷却 |
| Nitrium合成 | 100,000 J/mol | 吸热 |

**产线规划**:
```
1. Tritium: Plasma+O₂(>96:1)+火
2. N₂O: N₂+O₂+BZ+200-250K
3. BZ: N₂O+Plasma+<313K
4. Hypernob: N₂+Tr+<15K
5. Freon: Plasma+CO₂+BZ+>473K
6. Antinoblium: HFR Hypernob+H₂/Tr
7. Halon: 电解器 BZ→Halon
8. Pluoxium: CO₂+O₂+Tr+50-273K (或SM自产)
9. Proto-Nitrate: H₂+Pluoxium+5000-10000K
10. Healium: BZ+Freon+25-300K
11. Zauker: Hypernob+Nitrium+50000-75000K
```

---

# 第二卷 · 管道工程

## 第3章 · 管道与组件系统

**代码**: `code/modules/atmospherics/machinery/` (~30文件, ~8,000行)

### 3.1 系统架构

```
大气机械层次:
管线层(1-5层)
  ↓
管道(Pipe) → Pipeline(管线) → Pipe Network(管网)
  ↓                                         ↓
组件(Components)                         + 接入组件
```

**管线层**: 1~5层, 不同层级同格不冲突
**管道颜色**: 通过颜色适配器或右键改变

### 3.2 管道类型

**代码**: `machinery/pipes/pipes.dm` + 子文件

| 类型 | 说明 |
|---|---|
| **直管(Simple)** | 两端连接,按方向对齐 |
| **歧管(Manifold)** | 3通连接 |
| **4通歧管(Manifold4w)** | 4方向连接 |
| **层连管(Layermanifold)** | 跨管道层连接 |
| **桥管(Bridge Pipe)** | 跨越同格其他管道 |
| **颜色适配器** | 连接不同颜色管道网络 |
| **智能管道(Smart)** | 用位图式图标自动调整连接 |
| **多Z管道** | 垂直层间连接 |

**热交换管道系列**:
| 类型 | 说明 |
|---|---|
| **热交换直管** | 与环境(太空)换热 |
| 热交换歧管 | 3通热交换版 |
| 热交换4通 | 4通热交换版 |
| **热交换接口(Junction)** | 普通管↔热交换管的转接点, `minimum_temperature_difference=300`, `thermal_conductivity=0.0` |

### 3.3 管道颜色系统

**代码**: `piping_colors_lists.dm` (47行)

| 颜色名 | RGB | 层偏移 |
|---|---|---|
| **omni**(默认) | #e0e0e0 | 0 |
| green | #00ff00 | -1 |
| blue | #0000ff | -5 |
| red | #ff0000 | +3 |
| orange | #ffa500 | +1 |
| cyan | #00ffff | -3 |
| dark | #333333 | -2 |
| yellow | #ffff00 | +5 |
| brown | #8b4513 | -4 |
| pink | #ffc0cb | — |
| purple | #800080 | +2 |
| violet | #8a2be2 | +4 |

**规则**: 同色才能直连; omni(默认灰)可连任意色; 颜色适配器专门连不同色

### 3.4 二元设备(2端口)

#### 3.4.1 泵 (Pump)

**代码**: `pump.dm` — `/obj/machinery/atmospherics/components/binary/pump`

| 参数 | 默认 | 范围 |
|---|---|---|
| `target_pressure` | 101.325 kPa | 0~4,500 kPa |
| Ctrl+左键 | 开关 | |
| Alt+左键 | 设为最大 | |

**工作逻辑**: `pump_gas_to(target_pressure)` → OUTPUT侧达到目标压力停止

#### 3.4.2 体积泵 (Volume Pump)

**代码**: `volume_pump.dm`

| 参数 | 默认 | 范围 |
|---|---|---|
| `transfer_rate` | 最大 | 0~MAX L/s |

**区别**: 按固定流量工作, **可逆压差**(OUTPUT比INPUT压力高也能推)

#### 3.4.3 被动门 (Passive Gate)

**代码**: `passive_gate.dm` — 不耗电

| 参数 | 默认 |
|---|---|
| `target_pressure` | 101.325 kPa |

**描述**: "不耗电的单向阀, OUTPUT压力低于目标时自动打开"

#### 3.4.4 压力阀 (Pressure Valve)

**代码**: `pressure_valve.dm` — 不耗电

| 参数 | 默认 |
|---|---|
| `target_pressure` | 101.325 kPa |

**描述**: "INPUT压力超过设定值时放气"

#### 3.4.5 阀门 (Valve)

**代码**: `valve.dm` — 简单通/断开关

#### 3.4.6 温度门 (Temperature Gate)

**代码**: `temperature_gate.dm` — 温度阈值阀门

#### 3.4.7 温度泵 (Temperature Pump)

**代码**: `temperature_pump.dm` — 按目标温度泵送

#### 3.4.8 双口通気泵 (DP Vent Pump)

**代码**: `dp_vent_pump.dm` — 同时连两个管网的双口通风

#### 3.4.9 循环泵 (Circulator)

**代码**: `circulator.dm` — TEG核心

| 参数 | 值 |
|---|---|
| 模式 | HOT/COLD |
| 需要 | ≥10kPa压力差 |
| 转移 | `pressure_delta × volume / (temp × R)` 取压力差一半 |

TEG发电: 热端和冷端**温差越大, 发电越多**

### 3.5 三元设备(3端口)

#### 3.5.1 过滤器 (Filter)

**代码**: `filter.dm`

| 参数 | 默认 |
|---|---|
| `transfer_rate` | MAX |
| `filter_type` | 列表(指定过滤气体) |

**端口**: NODE1=INPUT, NODE2=SIDE(过滤气), NODE3=MAIN(其余气体)

#### 3.5.2 混合器 (Mixer)

**代码**: `mixer.dm`

| 参数 | 默认 |
|---|---|
| `target_pressure` | 101.325 kPa |
| `node1_concentration` | 0.5 |
| `node2_concentration` | 0.5 |

**端口**: NODE1=气体1, NODE2=气体2, NODE3=混合输出

### 3.6 一元设备(1端口)

#### 3.6.1 通気泵 (Vent Pump)

**代码**: `vent_pump.dm`

| 参数 | 默认 | 说明 |
|---|---|---|
| `pump_direction` | RELEASING | RELEASING(管→房) / SIPHONING(房→管) |
| `pressure_checks` | EXTERNAL_BOUND | 压力限制模式 |
| `external_pressure_bound` | 101.325 kPa | 排气上限 |
| `internal_pressure_bound` | 0 kPa | 吸气上限 |
| 耐久 | 100 HP | 超频会损坏风扇(0.5HP/s) |

#### 3.6.2 洗气机 (Vent Scrubber)

**代码**: `vent_scrubber.dm`

| 参数 | 默认 |
|---|---|
| `scrubbing` | SCRUBBING |
| `filter_types` | [CO₂] (默认只吸CO₂) |
| `volume_rate` | 200 L/s |
| Widenet | 可设3×3范围 |

#### 3.6.3 恒温机 (Thermomachine)

**代码**: `thermomachine.dm`

| 参数 | 说明 |
|---|---|
| `target_temperature` | 目标温度(K) |
| `min_temperature` | 由零件决定 |
| `max_temperature` | 由零件决定 |

#### 3.6.4 热交换器 (Heat Exchanger)

**代码**: `heat_exchanger.dm` — 管道气体和所在空间换热(不耗电)

#### 3.6.5 冷冻机 (Cryo)

**代码**: `cryo.dm` — 深度冷却(比恒温机更冷)

#### 3.6.6 被动通风 (Passive Vent) — 不耗电压力平衡

#### 3.6.7 排注口 (Outlet Injector) — 强制注入气体

#### 3.6.8 气闸泵 (Airlock Pump) — 气闸抽空/充气

#### 3.6.9 接口 (Portables Connector) — 连接便携设备

#### 3.6.10 机器接口 (Machine Connector) — 连接发电设备

### 3.7 便携设备

**代码**: `machinery/portable/`

#### 3.7.1 气罐 (Canister)

**代码**: `canister.dm`

| 参数 | 值 |
|---|---|
| 内部体积 | **2,000 L** |
| 默认最大压力 | 90×101.325 = **9,119 kPa** (90atm) |
| 压力限制 | 500,000 kPa |
| 温度限制 | 10,000 K |
| 释放压力(可调) | 默认101.325 kPa |
| 耐久 | 400 HP (integrity_failure=40%) |

#### 3.7.2 便携泵 / 洗气机 / 管道洗气机

移动式设备, 功能同上

### 3.8 其他设备

| 设备 | 功能 |
|---|---|
| **气压计(Meter)** | 显示管道气体数据(压力/温度/组成) |
| **气体矿机(Gas Miner)** | 从环境采集气体, 约515mol/s, 外部压力上限6,500kPa |

### 3.9 特殊加工机

#### 3.9.1 电解器

**代码**: `electrolyzer.dm`

**内部体积**: 200L. 将气体通过电力转化为其他气体. 3种反应见 §2.6

#### 3.9.2 气体结晶器 (Crystallizer)

**代码**: `crystallizer.dm`

将气体转化为固体物品. 15种配方见 §8.2. 品质偏差0.90~1.10.

### 3.10 管线网络物理系统

**代码**: `datum_pipeline.dm` (376行)

```
管线 = 相连的一组管道 + 内部气体

核心流程:
  reconcile_air() — 均衡管线内所有节点气体
  air.react(src)  — 管线内部触发气体反应(管道里也能着火!)

连接规则:
  1. 同层(piping_layer)
  2. 同色或一方是omni
  3. 相邻方向
  4. 不被阻挡(can_atmos_pass)
```

每段管道默认体积 = 70L, 每个组件内部缓冲 = 200L

### 3.11 恒温机

**代码**: `thermomachine.dm`

| 参数 | 值 |
|---|---|
| 默认目标 | T20C (293.15K) |
| 最小温度 | 由零件决定 |
| 最大温度 | 由零件决定 |

### 3.12 气阀警报系统

**代码**: `air_alarm/` (6文件)

#### 默认阈值

| 监测项 | warning_min | hazard_min | warning_max | hazard_max |
|---|---|---|---|---|
| **O₂**(kPa) | 19 | 16 | — | — |
| **CO₂**(kPa) | — | — | 5 | 10 |
| **总压力**(kPa) | 50 | 20 | 325 | 550 |
| **温度**(K) | 280 | 270 | 313 | 340 |
| 冷库压力(kPa) | 91.2 | 81 | 111.5 | 121.6 |
| 冷库温度(K) | 239 | 219 | 313 | 340 |
| 厨房温度(K) | 239 | 219 | 313 | 340 |

#### 7种模式

| 模式 | 危险 | 功能 |
|---|---|---|
| **过滤**(Filtering) | 安全 | 开洗气机吸CO₂释O₂ |
| **污染**(Contaminated) | 安全 | 吸全部污染物 |
| **通风**(Draught) | 安全 | 从管道释气到房间 |
| **补气**(Refill) | ⚠️ | 强制通风补气 |
| **循环**(Cycle) | ⚠️ | 全抽空→充气 |
| **抽空**(Siphon) | ⚠️ | 全房间抽到管道 |
| **紧急抽空**(Panic Siphon) | ⚠️ | 最高速率抽空 |

### 3.13 默认端口分配速查

| 设备 | 端口1 | 端口2 | 端口3 |
|---|---|---|---|
| 泵 | INPUT | OUTPUT | — |
| 体积泵 | INPUT | OUTPUT | — |
| 过滤器 | INPUT | SIDE(过滤) | MAIN(其余) |
| 混合器 | INPUT1 | INPUT2 | OUTPUT |
| 通気泵 | 管道侧 | 房间 | — |
| 洗气机 | 管道侧 | 房间 | — |
| 恒温机 | INPUT | OUTPUT | — |
| 循环泵(热) | OUTPUT(冷) | INPUT(热) | — |

### 3.14 工程实务指南

**标准大气循环**:
```
供应管 → 混合器(N₂+O₂) → 恒温机(调温) → 通気泵(排气到房间)
废气 ← 洗气机(吸CO₂) ← 房间
```

**常用配置**:
1. **SM气体循环**: 气罐→泵→过滤器→混合器→泵→SM腔室→过滤器→热交换→储存
2. **配气站**: N₂罐+O₂罐→混合器(79/21)→恒温机→供应管
3. **气体精炼**: 原料气→过滤器#1(提Plasma)→燃烧室(造Tr)→冷却→过滤器#2(分Tr/CO₂)

---

## 第4章 · 压力安全与爆炸系统

**代码**: `tanks/tanks.dm`, `elements/volatile_gas_storage.dm`, `components/gas_leaker.dm`

### 4.1 所有容器压力等级总表

| 容器 | 泄漏压力 | 碎裂压力 | 爆炸压力 | 最大耐压 |
|---|---|---|---|---|
| **手持气罐** | 3,040 kPa | 3,546 kPa | **4,053 kPa** | 取决于材料 |
| **便携气罐(Canister)** | — | — | 由volatile_gas_storage控制 | 500,000 kPa |
| **固定气罐(Tank)** | 超过max_pressure掉血 | 800HP耗尽 | 5,000~100,000 kPa | **46,000 kPa**(默认) |
| **气体矿机** | — | — | — | 外部6,500 kPa |

### 4.2 手持气罐爆炸系统

**代码**: `tanks/tanks.dm`

| 常量 | 值 |
|---|---|
| `TANK_LEAK_PRESSURE` | **3,040 kPa** (30 atm) |
| `TANK_RUPTURE_PRESSURE` | **3,546 kPa** (35 atm) |
| `TANK_FRAGMENT_PRESSURE` | **4,053 kPa** (40 atm) |
| `TANK_FRAGMENT_SCALE` | **8,511 kPa** (84 atm) |
| `TANK_MELT_TEMPERATURE` | **1,000,000 K** |

**损伤公式** (每tick):
```dm
压力损伤 = max_integrity × (当前压力 - LEAK) / (RUPTURE - LEAK) × seconds_per_tick
温度损伤 = max_integrity × (温度 - 1e6) / 温度 × seconds_per_tick
```

**三阶段破坏**:
| 阶段 | 压力 | 效果 |
|---|---|---|
| **Leak(泄漏)** | ≥3,040 kPa | 漏气+声光 |
| **Rupture(碎裂)** | ≥3,546 kPa | HP耗尽→碎裂 |
| **Fragment(爆炸)** | ≥4,053 kPa + HP归零 | **先气体反应再爆炸!** |

**爆炸威力**:
```dm
// 碎裂前先触发气体反应(关键!)
air_contents.react(src)
pressure = air_contents.return_pressure()

power = (体积 × (pressure - 4,053)) / 8,511
dyn_explosion(src, power, flash_range=1.5)
```

### 4.3 便携气罐爆炸系统

**代码**: `portable/canister.dm`

```dm
temp_damage = air_contents.temperature / temp_limit(10,000K)
pressure_damage = air_contents.return_pressure() / pressure_limit(500,000kPa)

take_damage(clamp(temp_damage × pressure_damage, 5, 50), BURN)
```

**volatile_gas_storage组件**:
```dm
最低触发压力: 5,000 kPa
最高压力: 100,000 kPa
最大爆炸力: 9级

explosive_force = ceil(当前压力 / 100,000 × 9)
// 50,000kPa → 5级; 100,000kPa+ → 9级最大
```

**加热爆炸**:
```dm
if(exposed_temperature > TEMPERATURE_RESISTANCE && !shielding_powered)
    take_damage(5, BURN, 0) // 每tick
```

### 4.4 泄漏系统(Gas Leaker)

**代码**: `components/gas_leaker.dm`

触发: HP低于`integrity_leak_percent`(默认90%)

```dm
true_rate = (1 - current_integrity / max_integrity) × leak_rate
// HP剩80% → 泄漏20%×leak_rate
```

固定气罐泄漏参数: `leak_rate=0.05`, `integrity_leak_percent=0.9`

### 4.5 Hypernoblium晶体抑爆原理

**晶体来源**: 气体结晶器中消耗 O₂(1000) + Hypernob(85), 3~250K, 吸热

**用途**:
| 用途 | 说明 |
|---|---|
| **插便携设备** | 解锁反应抑制开关 |
| **插太空服** | 获得太空服级抗压+抗低温 |

**反应抑制原理**:
```dm
// portable_atmospherics.dm
insert_nob_crystal() → nob_crystal_inserted = TRUE
toggle_reaction_suppression() → 仅当nob_crystal_inserted时可切换

// 核心:
suppress_reactions = TRUE → 完全跳过 air_contents.react(src)
→ 气体不会在罐内发生任何反应
→ 只有PV=nRT物理升压
→ 防止链式放热爆炸
```

### 4.6 固定气罐损伤机制

**代码**: `components/tank.dm`

```dm
// 每tick
if 压力 > max_pressure(46,000kPa):
    take_damage(0.1, BRUTE)
    prob(40%) → 金属变形声

// 耐压随HP下降
max_pressure = (当前HP/初始HP) × 初始max_pressure

// 爆炸: HP归零 → break → volatile_gas_storage检查
```

### 4.7 工程安全策略

**5层防线**:
```
① 被动门/压力阀 → 自动限压
② 泄压回路 → 高压→压力阀→废气系统
③ 恒温机预冷 → 反应前降温
④ Hypernob晶体+抑反应 → 不反应
⑤ 别装满 → 起始压力≤50% max_pressure
```

**紧急应对**:
| 情况 | 应对 |
|---|---|
| 气罐开始泄漏(喷雾声) | 远离/戴面具 |
| 气罐压力高但没炸 | 转移空地→开阀泄压 |
| 固定气罐裂纹声 | 金属变形声→焊枪修(25HP/次) |
| 外界火烤气罐 | 加隔热护盾(需电池)/搬走 |

---

# 第三卷 · 发电引擎

## 第5章 · 超物质引擎

**代码**: `code/modules/power/supermatter/` 共5文件 1,814行 + `delamination/` 5文件

### 5.1 SM核心属性

**代码**: `supermatter.dm` (1,160行)

| 变量 | 说明 | 阈值 |
|---|---|---|
| `internal_energy` | 内部能量(eV) | 越高越危险 |
| `damage` | 损伤值0~100 | >5警告 >60危险 >75紧急 ≥100解体 |
| `temp_limit` | 温度耐受极限 | 基础313.15K(可被气体+低mol提升) |
| `absorption_ratio` | 每tick吸周围气体比例 | 默认0.15, 持续吸收后降 |
| `explosion_power` | 爆炸威力倍数 | 默认35, 碎片12 |
| `zap_cutoff` | 闪电能量阈值 | 1.2 MJ |

**状态级别**:
| 状态 | damage | 广播 |
|---|---|---|
| INACTIVE | 无能量 | — |
| NORMAL | 运行 | — |
| NOTIFY | 温度接近极限 | — |
| **WARNING** | ≥5 | 工程频道 |
| **DANGER** | ≥60 | 工程频道 |
| **EMERGENCY** | ≥75 | 公共频道 |
| **DELAMINATING** | ≥100 | 倒计时30秒 |

### 5.2 SM变体

**代码**: `supermatter_variants.dm` (80行)

| 变体 | 特性 |
|---|---|
| **engine** (标准主引擎) | `is_main_engine=TRUE`, 可触发级联 |
| **shard** (碎片) | 可移动, absorption=0.125, explosion_power=12 |
| **shard/engine** | 固定主引擎碎片 |
| **small** (小型) | 魔法造, adamantine底座 |
| **hugbox** (安全) | damage/gas/power全部禁用 |

### 5.3 SM处理循环

**每tick6步**:

```
PART 1: 预处理
  检查关闭/不在空间? 在封闭墙内→Melt()融化!

PART 2: 气体吸收
  env.remove_ratio(absorption_ratio) → 吸收
  calculate_gases() → 气体百分比加权计算
  sm_gas.extra_effects() → 特殊效果(BZ核粒子/CO₂→Pluoxium等)

PART 3: 能量
  calculate_internal_energy() → 能量变化
  calculate_zap_transmission_rate() → 闪电输电率
  accumulate/discharge_energy() → 释放闪电(4秒冷却)
  supermatter_zap()

PART 4: 损伤
  calculate_temp_limit() → 温度极限
  calculate_damage() → 5种伤害

PART 5: 废气
  calculate_waste_multiplier()
  产 Plasma + O₂ + 高温 → 排放到环境

PART 6: 额外
  emit_radiation() / hallucination / handle_high_power()
  supermatter_pull(15%) / 更新视觉
```

### 5.4 气体对SM的影响

**代码**: `supermatter_gas.dm` (236行)

**关键**: 气体效果按**百分比加权**! 50%N₂+50%Tritium → heat_modifier = (-2.5×0.5)+(9×0.5)=3.25

```dm
for each gas:
    gas_percentage[gas] = mole_count / total_moles
    gas_power_transmission_rate += sm_gas.power_transmission × gas_percentage[gas]
    gas_heat_modifier          += sm_gas.heat_modifier × gas_percentage[gas]
    ...
```

| 气体 | 输电 | 废热 | 耐热 | 热产电 | 抑衰 | 特殊效果 |
|---|---|---|---|---|---|---|
| **O₂** | +0.15 | — | — | +1 | — | |
| **N₂** | — | **-2.5** | — | -1 | — | **最强普通抑热** |
| **CO₂** | — | +1 | — | +1 | **+1** | CO₂+O₂→**产Pluoxium** |
| **Plasma** | **+0.4** | **+14** | — | +1 | — | 高发热高输电 |
| **H₂O** | -0.25 | +11 | — | +1 | — | 发热+降输电 |
| **Hypernob** | +0.3 | **-14** | — | -1 | — | **极强抑热** |
| **N₂O** | — | — | **+5** | — | — | **最高耐热** |
| **Tritium** | **+3** | +9 | — | +1 | — | **极强输电** |
| **BZ** | -0.2 | +4 | — | +1 | — | >40%→**核粒子** |
| **Pluoxium** | -0.5 | -1.5 | — | -1 | — | 温和抑热 |
| **Miasma** | — | — | — | +0.5 | — | **消耗→发电** |
| **Freon** | **-3** | **-9** | — | -1 | — | **极抑输电+极抑热** |
| **H₂** | +2.5 | +9 | +1 | +1 | — | 高输电+耐热 |
| **Healium** | +0.24 | +3 | — | +1 | — | |
| **PN** | +1.5 | -4 | +4 | +1 | — | 抑热+耐热 |
| **Zauker** | +2 | +7 | — | +1 | — | **生成闪电** |
| **Antinob** | -0.5 | +14 | — | +1 | — | 高发热 |

### 5.5 温度极限公式

**代码**: `supermatter.dm` — `calculate_temp_limit()`

```dm
BASE = 273.15 + 40 = 313.15K           // T0C + HEAT_PENALTY_THRESHOLD
GAS  = gas_heat_resistance × 313.15    // 耐热×基础值
SOOTHED = psy_coeff × 45                // 心理医生
LOW_MOLES = clamp(2 - total_moles/100, 0, 1) × 313.15  // 低摩尔数加成!

temp_limit = BASE + GAS + SOOTHED + LOW_MOLES
```

**计算实例**:
| 场景 | temp_limit |
|---|---|
| 纯N₂, 500mol, 无psy | 313K (40°C) |
| 纯N₂, **50mol**, 无psy | **626K (353°C)** ← 气体少反而更耐热! |
| 纯N₂O, 500mol, 无psy | **1,879K (1,606°C)** |
| 纯N₂O, 50mol, 无psy | **2,192K (1,919°C)** |
| N₂O+100mol+psy=1 | **2,237K (1,964°C)** |

**结论**: 保持SM腔内气体<100mol = temp_limit大幅提升 = 更耐热!

### 5.6 伤害计算

**代码**: `supermatter.dm` — `calculate_damage()`

```dm
// 5种伤害, 每tick叠加
1. 外部伤害: external_damage × clamp((75-damage)/75, 0, 1)
   → HP越低, 外部伤害越弱

2. 过热: clamp((温度 - temp_limit) / 24000, 0, 0.15)
   → 每超过temp_limit 24,000K → 0.15/tick

3. 过功率: clamp((energy - POWER_PENALTY) / 40000, 0, 0.1)
   → 超过5,000后每4,000能量+0.1伤害

4. 过摩尔: clamp((总mol - MOLE_PENALTY) / 3200, 0, 0.1)
   → 超过1,800后每3,200mol+0.1伤害

5. 太空暴露: energy × 0.000125 (上限1)

// 回血 (没暴露太空+有气体)
healing = clamp((温度 - temp_limit) / 6000, -0.1, 0)
// 温度低于temp_limit → 每6,000K差 -0.1/tick
```

### 5.7 能量系统

**代码**: `supermatter.dm` — `calculate_internal_energy()`

**4种能量来源**:
| 来源 | 公式 |
|---|---|
| 外部涓流 | `max(external_power_trickle/10, 40)` |
| 外部即时 | `external_power_immediate` |
| 热发电 | `gas_heat_power_generation × 温度 × 1/6` |
| 功率流失 | 见下方 |

**功率衰减**:
```dm
momentary_power = internal_energy + 新增能量

if momentary_power < powerloss_linear_threshold:
    powerloss = -(momentary_power / 500)³  // 三次方(低能慢)
else:
    powerloss = -(momentary_power × 0.83 + 偏移)  // 线性(高能恒速)

// 气体抑衰
powerloss += gas_powerloss_inhibition × powerloss
// 心理抑衰
powerloss += min(1-gas_powerloss, 0.2×psy_coeff) × powerloss
```

**闪电输电率**: `zap_rate = 1040 + 1040 × gas_power_transmission_rate W/MeV`
→ Tritium(gas_power=3): 1040+3120=4160 W/MeV (4倍!)

### 5.8 高能量行为

**代码**: `supermatter_extra_effects.dm` — `handle_high_power()`

当 `internal_energy > 5,000` 或 `damage > 60` 时触发:

**四档闪电**:
| 能量 | 图标 | 数量 | 伤害 |
|---|---|---|---|
| 5,000~7,000 | 默认黄 | 2道 | 无附加 |
| 7,000~9,000 | 蓝色 | 3道 | 伤人+毁物 |
| >9,000 | 红色 | **4道** | 伤人+毁物+炸机器+眩晕 |

**闪电参数**:
```dm
zap_cutoff = clamp(1.2e6 - (能量×总mol×40)/温度, 1.4e5, 1.2e6)
// 冷+气体多→zap_cutoff低→闪电易跳跃

range = clamp(能量/压力×10, 2, 7)
// 低压力高能量→闪电更远(最高7格)
```

**异常生成** (每tick):
| 异常 | 条件 | 概率 |
|---|---|---|
| 通量 | >5,000 | 5% |
| 幻觉 | >5,000 | 5% |
| 引力 | >7,000 | 5%; 否则1% |
| 火焰 | >7,000 | 2%; 否则0.3% |

### 5.9 辐射系统

**代码**: `supermatter_extra_effects.dm` — `emit_radiation()`

```dm
功率因子 = min(internal_energy, 5,000)
完整性 = 1 - damage/100

// HP<70%时垫高功率因子(即使无能量也辐射)
power_factor = max(功率因子, integrity_power_nudge)

// 阈值 (越低穿透越强)
threshold = (-power_factor/5000+1)^(1/完整性²)

// 辐射概率 (完整性越低越高)
chance = 0.37×(1-完整性) + 0.03
完整→3%, 半血→~21%, 解体→~40%
```

### 5.10 物质吞噬

**代码**: `supermatter_hit_procs.dm` (148行)

```dm
consume_callback(matter_increase, damage_increase)
    external_power_trickle += matter_increase
    external_damage_immediate += damage_increase
```

| 接触物 | energy | damage |
|---|---|---|
| 人体/生物 | 高 | 中→化灰 |
| 金属物 | 中 | 低 |
| 子弹 | 子弹×2 | 子弹×0.1 |
| 发射器激光 | 相关 | **可治疗!** 回血降能 |

**特殊激光** (hitscan):
```dm
if integrity_heal: damage = max(0, damage - heal)
if energy_reduction: internal_energy = max(0, energy - reduction)
if psi_change: psy_coeff = clamp(psy_coeff + change, 0, 1)
```

**磁感激光**: `absorption_ratio = clamp(absorption_ratio + 0.05, 0.15, 1)`

### 5.11 心理学系统

**代码**: `supermatter_extra_effects.dm` — `psychological_examination()`

```dm
// 心理医生 (TRAIT_SUPERMATTER_SOOTHER) 在场:
  psy_coeff += 0.05/tick (约20秒到1)
// 不在场:
  psy_coeff -= 0.05/tick (约20秒到0)

// 效果:
temp_limit += psy_coeff × 45           // 最多+45K
waste_multiplier -= 0.2 × psy_coeff    // 最多-20%
powerloss -= 0.2 × psy_coeff × 衰减    // 最多-20%
```

**幻觉**: `范围 = min(7, round(P^0.25))`, 时长 = energy×0.1 (最大400秒)

### 5.12 SM闪电系统

**代码**: `supermatter.dm`

```dm
// 正常闪电
冷却 4秒
范围 3格
能量 = accumulated_energy
zap_cutoff = 240 KJ
颜色由能量决定: 黄色→蓝色(随升温)

// 目标优先级
自行车 > 线圈 > 棒 > 活物 > 机器 > 物体
```

### 5.13 解体机制

**代码**: `supermatter_delamination/` (5文件)

**策略优先级**:
| 策略 | 触发 |
|---|---|
| **Cascade** | 优先级最高 |
| **Singularity** | 气体 > 1,800mol |
| **Tesla** | 能量 > 5,000 |
| **Explosive** | 总是可选(默认) |

**爆炸威力**:
```dm
power = explosion_power × max(gas_heat_power_generation, 0.205)
devastation_range = power × 0.5
heavy_range = power + 2
light_range = power + 4
flash_range = power + 6

// 主引擎(35): dev=7.2, heavy=9, light=11
// 碎片(12): dev=2.5, heavy=4, light=6
```

**解体效果** (所有类型都包含):
1. 辐射脉冲 (20格范围)
2. 士气打击 (全站致幻)
3. 生成异常 (仅主引擎, 10个)
4. 基础爆炸

### 5.14 级联Cascade

**代码**: `cascade_delam.dm` (99行)

**触发条件**:
```dm
1. 必须是主引擎
2. 气体 > 1,800 × absorption_ratio
3. Antinoblium 或 Hypernoblium 占比 > 40%
```

**人为触发**: `destabilizing_crystal` + 完整性>80% + 3秒动作

**解体执行**:
1. 大爆炸: dev=17.5, heavy=37, light=39
2. 三角洲警戒 + 全站红灯 + 维修通道开放
3. 锁定穿梭机(无法呼叫)
4. 生成逃生裂缝
5. **4~6个晶簇**全站扩散!
6. 晶簇不可摧毁, 吞噬活物, 缓慢蔓延

**逃生**: 逃生裂缝存续期间可从裂缝撤离, 晶簇破坏裂缝则全站结束

### 5.15 碎片剽窃

**代码**: `supermatter_hit_procs.dm` L87-104

```dm
使用 scalpel/supermatter 手术刀60秒:
1. 产 /obj/item/nuke_core/supermatter_sliver
2. supermatter_sliver_removed = TRUE
3. external_power_trickle += 800
4. 解体倒计时缩短 (30秒→5秒)
```

### 5.16 SM废气排放

**代码**: `supermatter.dm` L337-349

```dm
// 废气温度
temp += energy × waste_multiplier / 4
temp = clamp(temp, 2.7K, 2500 × waste_multiplier)

// 废气Plasma
plasma += max(energy × waste_multiplier / 650, 0)

// 废气氧气
oxygen += max(((energy + temp × waste_multiplier) - 273.15) / 340, 0)

// waste_multiplier = 1.0 + gas_heat_modifier - 0.2×psy_coeff (下限0.5)
```

### 5.17 SM引力

```dm
// 每tick 15%概率
supermatter_pull(loc, min(internal_energy/850, 3))
// 850能量→范围1, 1700→范围2, 2550→范围3
```

### 5.18 操作指南

**标准启动**:
1. 连接 TEG + 循环泵 (热端接SM附近, 冷端接冷却)
2. 冷却气: N₂(最便宜)或Hypernob(最强)
3. 反应气: O₂ + Plasma 或 冷Plasma
4. 调整气体配比控温

**冷却方案**:
| 方案 | 效果 | 成本 |
|---|---|---|
| **N₂** | 废热-2.5 | 便宜量足 |
| **Hypernob** | 废热-14 | 稀有 |
| **Freon** | 废热-9+输电-3 | 稀有 |
| **Pluoxium** | 废热-1.5+输电-0.5 | 中 |

**危险信号**:
| 信号 | 应对 |
|---|---|
| 温度>temp_limit | 加大N₂/加Hypernob |
| damage>5 | 检查配比 |
| damage>60 | 大量注冷却气 |
| damage>75 | 准备跑 |
| 倒计时开始 | 30秒后解体 |

---

## 第6章 · HFR聚变反应堆

**代码**: `code/modules/atmospherics/machinery/components/fusion/` 共6文件 2,265行

### 6.1 HFR架构总览

#### 物理结构

```
     ┌── [Corner NW] ── [Interface] ── [Corner NE] ──┐
     │                                                  │
[Fuel Input]                                    [Waste Output]
     │      ┌──────── HFR Core ────────┐              │
     │      │ 融合腔(internal_fusion)    │              │
     ├─────→│   体积 5,000 L            │←────────────┤
     │      │ 调节剂室(moderator)        │              │
     │      │   体积 10,000 L           │←── [Moderator Input]
     │      │                           │
     │      │ [管道1: 冷却液入]          │
     │      │ [管道2: 冷却液出]          │
     └── [Corner SW] ──── [ ] ──── [Corner SE] ──┘
```

#### 核心变量

| 变量 | 默认 | 说明 |
|---|---|---|
| `energy` | 0 | 聚变能量(E=mc²游戏版) |
| `core_temperature` | 293.15K | 理论反应中心温度 |
| `internal_power` | 0 | 内部聚变功率 |
| `power_output` | 0 | 有效功率输出 |
| `instability` | 0 | 不稳定度(决定放热/吸热) |
| `efficiency` | 0.01 | 效率 |
| `power_level` | 0~6 | 聚变等级 |
| `iron_content` | 0~1 | 铁积累量 |
| `critical_threshold_proximity` | 0~900 | 损伤值(900=熔毁) |

#### 用户可控参数

| 参数 | 范围 | 默认 | 说明 |
|---|---|---|---|
| `heating_conductor` | 0~500 | 100 | 热导率 |
| `magnetic_constrictor` | 0~200 | 100 | 磁约束 |
| `current_damper` | 0~100 | 0 | 阻尼器(降不稳定) |
| `fuel_injection_rate` | 0~200 | 25 | 燃料注入率 |
| `moderator_injection_rate` | 0~200 | 25 | 调节剂注入率 |
| `waste_remove` | on/off | off | 自动废料清除 |

### 6.2 7种燃料配方

**代码**: `hfr_fuel_datums.dm` (140行)

| 燃料 | 消耗 | 主产物 | 副产物(PL1→6) | 特性 |
|---|---|---|---|---|
| **Plasma+O₂** | Plasma+O₂ | CO₂,H₂O | CO₂/H₂O/Freon/N₂O/Pluoxium/Halon | 低温低能耗, 最小熔毁 |
| **H₂+O₂** | H₂+O₂ | He,N₂ | He/Plasma/O₂/N₂/BZ/Hypernob | 低温+EMP+中扩散 |
| **Tr+O₂** | Tr+O₂ | He,Pluoxium | He/Plasma/O₂/N₂/BZ/Hypernob | 辐射+中扩散 |
| **H₂+Tritium** | H₂+Tr | He | He/Plasma/O₂/N₂/BZ/Hypernob | **均衡!无偏置** |
| **Hnob+H₂** | Hnob+H₂ | **Antinoblium** | Anob/He/PN/Zauker/Healium/Miasma | 毁灭爆+大扩散 |
| **Hnob+Tr** | Hnob+Tr | **Antinoblium** | 同上 | 同上 |
| **Hnob+Anob** | Hnob+Anob | He | Plasma/O₂/N₂/PN/Nitrium/Miasma | **毁灭+临界!** |

**燃料倍率表**:
| 燃料 | 负温 | 正温 | 能量密度 | 消耗 | 产率 | 温限 | 熔毁标志 |
|---|---|---|---|---|---|---|---|
| P+O₂ | ×2.5 | ×0.1 | ×10 | ×3.3 | ×1.4 | 60% | 基础+最小 |
| H₂+O₂ | ×2 | ×0.6 | ×3 | ×1.1 | ×0.9 | 75% | EMP+中 |
| Tr+O₂ | ×2.1 | ×0.5 | ×2 | ×1.2 | ×0.8 | 80% | 辐射+中 |
| H₂+Tr | ×1 | ×1 | ×1 | ×1 | ×1 | 85% | 中爆+EMP+辐射+中 |
| Hnob+H₂ | ×0.2 | ×2.2 | ×0.2 | ×0.55 | ×1.4 | 90% | 毁灭+辐射+EMP+大 |
| Hnob+Tr | ×0.1 | ×2.5 | ×0.1 | ×0.45 | ×1.7 | 95% | 毁灭+辐射+EMP+大 |
| Hnob+Anob | **×0.01** | **×3.5** | ×2 | **×0.01** | ×3 | 100% | 毁灭+辐射+EMP+超大+**临界** |

### 6.3 聚变等级系统

**代码**: `hfr_procs.dm` L199-213

**关键: power_level 由融合腔温度决定, 与能量无关!**

```dm
fusion_temperature < 500K           → PL0
500K ~ 1,000K                       → PL1
1,000K ~ 10,000K                    → PL2
10,000K ~ 100,000K                  → PL3
100,000K ~ 1,000,000K              → PL4
1,000,000K ~ 10,000,000K           → PL5
> 10,000,000K                       → PL6
```

**各等级行为**:
| PL | 温度 | 产铁 | 核粒子 | 闪电 | 幻觉 | Antinob |
|---|---|---|---|---|---|---|
| 0 | <500K | ❌ | ❌ | ❌ | ❌ | ❌ |
| 1 | 0.5~1K | ❌ | ❌ | ❌ | ❌ | ❌ |
| 2 | 1K~10K | ❌ | ❌ | ❌ | ❌ | ❌ |
| 3 | 10K~100K | ❌ | ❌ | ❌ | ❌ | ❌ |
| 4 | 100K~1M | ❌ | ✅(BZ>37.5) | ✅(2~4) | ✅(BZ>100) | ❌ |
| 5 | 1M~10M | **17%/tick** | ✅ | ✅(伤人) | ✅ | ✅(条件) |
| 6 | >10M | **100%/tick** | ✅ | ✅(毁物) | ✅ | ✅ |

### 6.4 主处理循环

**代码**: `hfr_main_processes.dm` (587行)

```
每tick:
│
├── [1] 前置检查: active? check_part_connectivity?
│    ├── 未激活或部件不完整→return
│    └── 通过→assert_gases()
│
├── [2] fusion_process() — 主聚变
│   ├── check_power_use()
│   │   ├── 有电+冷却→inject_from_side_components()+process_internal_cooling()
│   │   └── 断电→安全参数+iron+=0.02×PL/s
│   ├── update_temperature_status() → 4路温度
│   ├── 不稳定度计算
│   ├── 调节剂修饰量计算
│   ├── 能量/功率/核心温度/热输出计算
│   ├── check_fuel() → 够? → 消耗
│   ├── moderator_fuel_process() → 按PL产物
│   └── moderator_common_process() → 幻觉/核粒子/闪电
│
├── [3] process_moderator_overflow() — 调节剂溢出
├── [4] process_damageheal() — 损伤/治疗
├── [5] check_alert() — 警报
└── [6] remove_waste() — 废料
```

### 6.5 不稳定度系统

**代码**: `hfr_main_processes.dm` L104-118

```dm
toroidal_size = 2π + arctan((体积 - 1000)/1000)

gas_power = Σ(融合腔mol × fusion_power)
            + 0.75 × Σ(调节剂mol × fusion_power)

instability = MODULUS((gas_power × 0.003)², toroidal_size)
              + damper × 0.01 - iron × 0.05
```

**气体的fusion_power**:
| 气体 | fusion_power |
|---|---|
| O₂/N₂/CO₂/Plasma/Miasma | 0 |
| H₂ | +2 |
| Tritium | +5 |
| Nitrium | +7 |
| BZ | +8 |
| **N₂O** | **+10** |
| **Hypernoblium** | **+10** |
| **Helium** | +7 |
| **Antinoblium** | **+20** (最高) |
| **Pluoxium** | **-10** (降不稳定!) |
| **Freon** | **-5** (降不稳定) |

```dm
if instability × 0.5 < 4:
    internal_instability = 1 → 放热(正常)
else:
    internal_instability = -1 → 吸热(反应逆向!)
```

**关键**: instability > 8 时反应吸热!

### 6.6 能量与功率公式

**核心公式**:
```dm
// 能量
energy = (energy_modifiers × 299,792,458²) × max(融合腔温度 × heat_modifier/100, 1)
energy /= energy_concentration_multiplier
energy = clamp(energy, 0, 1e35)

// 内部功率
internal_power = (燃料1_scaled × power_modifier/100) ×
                 (燃料2_scaled × power_modifier/100) ×
                 π × (2 × 燃料1_R × 燃料2_R)² × energy

// 效率
efficiency = 0.01 × clamp(主产物1_scaled, 1, 100)

// 核心温度
core_temperature = internal_power × power_modifier / 1000

// 热传导损失
conduction = -(融合腔温度 - core_temperature) × (magnetic_constrictor × 0.001)

// 辐射
radiation = max(-(2e-16/5e-18) × radiation_modifier × delta_temperature, 0)

// 有效输出
power_output = efficiency × (internal_power - conduction - radiation)

// 热限制器
heat_limiter_modifier = 5 × 10^power_level × (heating_conductor / 100)

// 热输出
heat_output_min = -heat_limiter_modifier × 0.01 × 负温倍率
heat_output_max = heat_limiter_modifier × 正温倍率
heat_output = clamp(internal_instability × power_output × heat_modifier / 200,
                    heat_output_min, heat_output_max)
```

**温度修正**:
```dm
temperature_modifier = 燃料.temperature_change_multiplier
if 融合腔温度 < 1e8 × temperature_modifier:
    融合腔温度 += heat_output × seconds_per_tick
    clamp(TCMB, 1e8 × temperature_modifier)
else:
    融合腔温度 -= heat_limiter_modifier × 0.01 × seconds_per_tick
```

**调节剂修饰量**:
| 调节剂 | energy_mod | power_mod | heat_mod | radiation_mod |
|---|---|---|---|---|
| **N₂** | +0.35 | — | -0.75 | -0.45 |
| **CO₂** | +0.55 | +0.95 | — | — |
| **N₂O** | +0.95 | -0.05 | -1.45 | — |
| **Zauker** | +1.55 | **+5.55** | — | — |
| **Antinob** | **+20** | — | — | **+10** |
| **Hypernob** | **-10** | — | — | — |
| **H₂O** | -0.75 | — | — | — |
| **Nitrium** | -0.15 | +1.45 | — | — |
| **Healium** | -0.45 | — | — | — |
| **Freon** | -1.15 | -0.75 | -0.95 | +1.15 |
| **O₂** | — | +0.55 | — | — |
| **Plasma** | — | +0.05 | +1.25 | -0.95 |
| **BZ** | — | — | — | +1.9 |
| **PN** | — | — | — | +0.1 |

### 6.7 损伤治疗系统

**代码**: `hfr_main_processes.dm` — `process_damageheal()`

**伤害来源**:
| 来源 | 条件 | 公式 |
|---|---|---|
| **过满** | PL≥6 | `mol/200 + temp_slope×冷却液温度 - 13.5` |
| **铁积累** | iron>0.35 | `max(iron-0.35, 0)`/tick |
| **超临界** | mol>10,000 | `(mol-10000)×0.002` (上限20/tick) |

**治疗来源**:
| 来源 | 条件 | 公式 |
|---|---|---|
| **亚临界** | mol<1,200, PL≤5 | `(mol-1200)/400` (负数=治疗) |
| **冷冷却液** | 冷却液<100,000K, PL≤4 | `log10(max(温度,1))×0.5-5` |

**铁积累**:
| PL | 概率 | 变化 |
|---|---|---|
| 1~4 | 25/(PL+1)% | -0.01/s |
| 5 | 85% | +0.005/s |
| 6 | 100% | +0.005/s |
| 断电 | 100% | +0.02×PL/s |

**O₂清铁**:
```dm
if O₂ > 150mol AND iron > 0:
    最大清除 = 0.0023/s
    消耗O₂ = 清除量 × 2409 mol
```

**损伤上限**: 每tick ≤ 0.005 × 900 = 4.5 (超临界无视此上限)

### 6.8 调节剂溢出系统

**代码**: `hfr_procs.dm` L604-651

条件: `moderator_internal.total_moles() > 10,000 mol`→必裂!

| 压力 | 泄漏速率 | 初始破裂 |
|---|---|---|
| <10,000 kPa | 0.05%/tick, 1% | 无声 |
| 10,000~12,000 kPa | 1%/tick | 小爆(轻1火焰3)+初始漏25% |
| **>12,000 kPa** | **5%/tick** | **大爆(重1轻3火焰5)+初始漏75%** |

裂缝修复: 焊枪10秒

### 6.9 核粒子闪电幻觉引力

**核粒子**:
```dm
条件: PL≥4, 调节剂BZ > 150/PL
效果: 从随机角落发射核粒子
```

**闪电弧**:
```dm
条件: PL≥4
数量: power_level-2 (PL4=2, PL5=3, PL6=4)
能量: PL × 240,000 J
截止: clamp(2.4e6 - (PL×总mol×360), 360,000, 2,400,000)
PL5: 伤人; PL6: 伤人+毁物
```

**幻觉**:
```dm
条件: PL≥4, 调节剂BZ>100
范围: min(7, round(|heat_output|^0.25))
持续: 100秒 × PL
```

**引力**:
```dm
概率: SPT_PROB(100 - critical_threshold/15, 秒)
范围: round(log2.5(critical_threshold))
损伤900→~7.5格, 损伤100→~5格
```

### 6.10 熔毁系统

**代码**: `hfr_procs.dm` L452-566

**触发**: `critical_threshold_proximity ≥ 900` → `countdown()`

**倒计时30秒**:
- 每5秒广播
- 最后10秒每秒报数
- 临界燃料: 第10秒特殊音效
- 可取消: 倒计时中损伤降到<900即可

**爆炸计算** (按燃料 `meltdown_flags`):
```dm
BASE_EXPLOSION:       flash=PL×3,  light=PL×2
MEDIUM_EXPLOSION:     flash=PL×6,  light=PL×5,  heavy=PL×0.5
DEVASTATING_EXPLOSION: flash=PL×8, light=PL×7,  heavy=PL×2,  dev=PL×1
```

**扩散计算**:
| 标志 | EMP轻 | EMP重 | 辐射 | 气袋 | 范围 |
|---|---|---|---|---|---|
| MINIMUM | PL×3 | PL×1 | 2PL+8 | 5 | PL×2 |
| MEDIUM | PL×5 | PL×3 | PL+24 | 7 | PL×4 |
| BIG | PL×7 | PL×5 | PL+34 | 10 | PL×6 |
| MASSIVE | PL×9 | PL×7 | PL+44 | 15 | PL×8 |

**临界熔毁** (Hnob+Anob燃料): 毁灭/重型/EMP ×2

**各燃料PL6熔毁**:
| 燃料 | 毁灭 | 重击 | 轻击 | 闪光 | EMP轻/重 | 辐射 | 气袋 |
|---|---|---|---|---|---|---|---|
| P+O₂ | 0 | 0 | 12 | 18 | 18/6 | 20 | 5 |
| H₂+O₂ | 0 | 3 | 30 | 36 | 30/18 | 30 | 7 |
| Tr+O₂ | 0 | 3 | 30 | 36 | — | 30 | 7 |
| H₂+Tr | 0 | 6 | 42 | 48 | 30/18 | 30 | 7 |
| Hnob+H₂ | **6** | **12** | 42 | 48 | 42/30 | 40 | 10 |
| Hnob+Tr | **6** | **12** | 42 | 48 | 42/30 | 40 | 10 |
| **Hnob+Anob** | **12** | **24** | 42 | 48 | **54/42** | 50 | **15** |

**气体扩散**: 融合腔×20% + 调节剂×20% → 随机散布 + EMP + 辐射

### 6.11 冷却系统

**代码**: `hfr_main_processes.dm` L524-548

```dm
步骤1: 融合腔→调节剂室 (METALLIC_VOID_CONDUCTIVITY=0.38)
步骤2: 冷却管道→调节剂室 (HIGH_EFFICIENCY_CONDUCTIVITY=0.975) [主冷却]
步骤3: 冷却管道→融合腔 (METALLIC_VOID_CONDUCTIVITY=0.38) [备选]
```

**注入**: 调节剂25mol/s (可调); 燃料均分注入

**调节剂蒸发**: `moderator_internal.remove(总mol × (1-(1-0.0005×PL)^秒))`
→ PL6: 约0.3%/tick

### 6.12 警报与完整性

**完整性级别**:
| 级别 | 完整性% | 广播 |
|---|---|---|
| **MELTING** | <5% | bloblarm |
| **EMERGENCY** | 5~25% | 公共频道 |
| **DANGER** | 25~50% | 工程频道 |
| **WARNING** | 50~100% | 工程频道 |
| NOMINAL | 正常 | — |
| INACTIVE | 未启动 | — |

**广播内容**:
| 标志 | 消息 |
|---|---|
| EMP | 乱码 |
| 过功率 | "Shield destabilizing due to excessive power!" |
| 铁损伤 | "Iron shards are damaging the internal core shielding!" |
| 高燃料 | "Fuel mix moles reaching critical levels!" |
| 铁增加 | "Iron amount inside the core is increasing!" |

### 6.13 电源系统

```dm
check_power_use():
    断电→返回FALSE (强制回退)
    耗电 = (PL+1) × 50KW (PL6=350KW)

    断电回退:
      magnetic_constrictor = 100
      heating_conductor = 500
      current_damper = 0
      fuel_injection_rate = 20
      moderator_injection_rate = 50
      waste_remove = FALSE
      iron_content += 0.02 × PL × 秒  ← 铁暴增!
```

### 6.14 完整常量表

**代码**: `_hfr_defines.dm` (161行)

| 常量 | 值 | 说明 |
|---|---|---|
| `LIGHT_SPEED` | 299,792,458 m/s | |
| `PLANCK_LIGHT_CONSTANT` | 2e-16 | |
| `CALCULATED_H2RADIUS` | 1.2m | |
| `CALCULATED_TRITRADIUS` | 0.23m | |
| `VOID_CONDUCTION` | 0.01 | |
| `FUSION_MOLE_THRESHOLD` | 25 mol | |
| `FUSION_MAXIMUM_TEMPERATURE` | 1e8 K | |
| `METALLIC_VOID_CONDUCTIVITY` | 0.38 | |
| `HIGH_EFFICIENCY_CONDUCTIVITY` | 0.975 | |
| `DAMAGE_CAP_MULTIPLIER` | 0.005 | |
| `melting_point` | 900 | |
| `MIN_POWER_USAGE` | 50 KW | |
| `HYPERTORUS_COUNTDOWN_TIME` | 30秒 | |
| `OVERFULL_MIN_POWER_LEVEL` | 6 | |
| `SUBCRITICAL_MOLES` | 1,200 mol | |
| `COLD_COOLANT_THRESHOLD` | 100,000K | |
| `MAX_SAFE_IRON` | 0.35 (35%) | |
| `HYPERCRITICAL_MOLES` | 10,000 mol | |
| `HYPERCRITICAL_SCALE` | 0.002 | |
| `HYPERCRITICAL_MAX_DAMAGE` | 20 | |

### 6.15 操作指南

**建造**: 四角方向=东南/朝南,西南/朝西,东北/朝东,西北/朝北; 3部件+Interface方向指向核心

**管道接法**:
```dm
Fuel Input     ← 燃料 (Plasma+O₂等)
Moderator Input ← 调节剂 (N₂等)
Waste Output   → 废气处理
Core 管道1     ← 冷却液
Core 管道2     → 冷却液出口
```

**启动**: 多用途工具点Interface→连接检查→激活→选燃料→开冷却→开调节剂→开燃料→开电源

**优化**:
| 目标 | 燃料 | 调节剂 |
|---|---|---|
| 稳定发电 | H₂+O₂ | N₂+少量O₂ |
| 产Antinob | Hnob+H₂ | 按要求 |
| 小风险 | P+O₂ | N₂ |
| 低不稳定 | 任一 | 加Pluoxium/Freon |

**危险信号**:
| 信号 | 应对 |
|---|---|
| 完整性<50% | 降PL/加冷却/清铁 |
| 完整性<25% | 紧急! 大量冷却 |
| 完整性<5% | 跑 |
| 调节剂溢出 | 焊枪修10秒 |
| 铁>35% | O₂清铁 |
| 断电 | 恢复供电(否则铁暴增) |

---

## 第7章 · 奇点引擎

**代码**: `code/modules/power/singularity/` 共7文件 2,315行

### 7.1 奇点基础

**代码**: `singularity.dm` (520行)

**6级尺寸**:
| 等级 | 尺寸 | 图标 | 引力范围 | 吞噬范围 | 所需能量 | 衰减 |
|---|---|---|---|---|---|---|
| **S1** | 1×1 | s1 | 4格 | 0 | ≥1 | 每10s -1 |
| **S2** | 3×3 | s3 | 6格 | 1 | ≥200 | 每5s -5 |
| **S3** | 5×5 | s5 | 8格 | 2 | ≥500 | 每4s -20 |
| **S4** | 7×7 | s7 | 10格 | 3 | ≥1,000 | 每10s -10 |
| **S5** | 9×9 | s9 | 10格 | 4 | ≥2,000 | **不衰减** |
| **S6** | 11×11 | s11 | **15格** | **5** | ≥3,000+**SM碎片** | 不衰减 |

**能量阈值**:
```dm
S1: 1~199     S2: 200~499   S3: 500~999
S4: 1000~1999 S5: 2000~2999 S6: ≥3000+SM碎片
```

**随机事件** (每2秒10%):
```dm
1/4 → EMP (范围8, 重10)
1/4 → 眩晕 (8格内无meson眼镜者60眩晕)
2/4 → 引燃 (仅S6, 20格碳基生物自燃)
```

### 7.2 奇点吞噬系统

**代码**: `singularity.dm` — `consume()`

```dm
consume(atom):
    if 蓝空间背包 → consume_boh() → 奇点坍缩消失!
    
    gain = thing.singularity_act(等级, src)
    energy += gain
    
    if 超物质晶体 && !consumed_supermatter:
        supermatter_upgrade() → 解锁S6!
```

**吞噬物能量**: 普通物体少量, 机器中量, 活物中~大量, SM晶体+100

**SM升级**: 名="supermatter-charged", 光照+10, 可升S6

**S6额外**: `dissipate=FALSE`, vision_hurting, 50%引燃20格, 引力15, 吞噬5

### 7.3 碰撞箱规则

```dm
扩展检查: 4方向各需一定格数空间
S1→S2: 3×3 (1格外)
S2→S3: 5×5 (2格外)
S3→S4: 7×7 (3格外)
S4→S5: 9×9 (4格外)
S5→S6: 11×11

限制: TRAIT_CONTAINMENT_FIELD 地砖不可通过
空间不够: 奇点尝试向其他方向移动
```

### 7.4 约束系统

| 组件 | 代码 | 功能 |
|---|---|---|
| **约束磁场** | `containment_field.dm` | 添加 `TRAIT_CONTAINMENT_FIELD` |
| **磁场发生器** | `field_generator.dm` (454行) | 消耗电力产生约束 |
| **发射器** | `emitter.dm` (706行) | 充能2秒, 射击冷却2秒, 激光充能奇点/维持SM |

**标准部署**: 4~8个磁场发生器围矩形 + 发射器指向奇点

### 7.5 操作指南

**启动**:
1. 安装奇点发生器→S1奇点
2. 立即启动约束磁场
3. 打开发射器→充能→升级
4. TEG利用温差发电

**事故处理**:
| 事故 | 处理 |
|---|---|
| 逃脱约束 | 关门窗→蓝空间背包 |
| S5+ | 极难控制, 蓝背包坍缩 |
| 约束场断电 | 奇点逃离 |
| 发射器损坏 | 能量衰减可能降级 |

**消除方法**:
| 方法 | 效果 |
|---|---|
| **蓝空间背包** | 吞噬后坍缩消失(最佳) |
| 强爆炸 | S1~S2可炸毁; S3+只减能量 |
| 等待 | 仅S1~S4自然衰减, S5+永恒 |

### 7.6 暗物质奇点与Nar'Sie

- **暗物质奇点** (`dark_matter_singularity.dm`, 55行): 变体, 参数同普通
- **Nar'Sie** (`narsie.dm`, 336行): 邪神事件, 非常规工程

### 7.7 三引擎对比

| 特性 | 奇点 | 超物质(SM) | HFR |
|---|---|---|---|
| 复杂度 | ★★ | ★★★ | **★★★★★** |
| 发电 | 间接(TEG) | 直接+闪电 | 直接+热 |
| 失控风险 | 高(跑) | 中(解体) | 中(熔毁) |
| 最大规模 | S6全站威胁 | 30秒倒计时 | 30秒倒计时 |
| 衰减 | S5以下自然衰减 | 气体维持 | 断电回退 |
| 消除 | 蓝背包 | 无法(必解体) | 可修复 |

---

# 第四卷 · 气体加工

## 第8章 · 结晶器与电解器

### 8.1 气体结晶器

**代码**: `crystallizer.dm`

**工作原理**: 绿管(INPUT)→内部气体室→消耗气体→累积进度→产物→红管(OUTPUT)
- 温度必须在配方范围内
- 品质偏差 0.90~1.10

### 8.2 完整配方表

**矿物/材料**:
| 配方 | 消耗气体 | 温度 | 能量 | 产物 |
|---|---|---|---|---|
| **Plasma Sheet** | Plasma 450 + BZ 15 | 10~20K | 放热 3,500,000 | 1×Plasma锭 |
| **Diamond** | CO₂ 1,500 | 10,000~30,000K | 放热 9,500,000 | 1×钻石 |
| **Hot Ice** | Freon 60 + Plasma 160 + O₂ 80 | 15~35K | **吸热** 3,000,000 | 1×热冰 |
| **Metallic Hydrogen** | H₂ 300 + BZ 50 | 50,000~150,000K | **吸热** 2,500,000 | 1×金属氢 |
| **Zaukerite** | Anob 5 + Zauker 20 + BZ 7.5 | 5~20K | 放热 2,900,000 | 2×Zaukerite锭 |
| **Ammonia Crystal** | H₂ 50 + N₂ 40 | 200~240K | 放热 950,000 | 2×氨结晶 |
| **Crystal Cell** | Plasma 800 + He 100 + BZ 50 | 50~90K | **吸热** 800,000 | 1×水晶电池 |

**功能性物品**:
| 配方 | 消耗气体 | 温度 | 能量 | 产物 |
|---|---|---|---|---|
| **Hypernoblium Crystal** | O₂ 1,000 + Hypernob 85 | 3~250K | **吸热** 250,000 | 1×晶体 |
| **Nitrium Crystal** | Nitrium 150 + O₂ 70 + BZ 50 | 10~25K | **吸热** 45,000 | 1×晶体 |
| **Healium Crystal** | Healium 100 + O₂ 120 + Plasma 50 | 200~400K | **吸热** 2,000,000 | 1×晶雷 |
| **Proto Nitrate Crystal** | PN 100 + N₂ 80 + O₂ 80 | 200~400K | 放热 1,500,000 | 1×晶雷 |
| **N₂O Crystal** | N₂O 150 + BZ 30 | 50~350K | 放热 3,500,000 | 1×晶雷 |
| **Crystal Foam** | CO₂ 150 + N₂O 100 + H₂O 25 | 不限 | 放热 140,000 | 1×泡沫雷 |
| **SM Shard** | Hnob 250 + Anob 250 + BZ 200 + Plasma 5,000 + O₂ 4,500 | 10~20K | 放热 3,500,000 | ⚠️ 1×SM碎块 |

**燃料颗粒**:
| 配方 | 消耗气体 | 能量 | 产物 |
|---|---|---|---|
| 标准燃料丸 | O₂ 50 + Plasma 100 | **吸热** 6,000,000 | 1×燃料丸 |
| 高级燃料丸 | Tr 100 + H₂ 100 | **吸热** 6,000,000 | 1×高级燃料丸 |
| 异星燃料丸 | Hnob 100 + Nitrium 100 | **吸热** 6,000,000 | 1×异星燃料丸 |

### 8.3 产物功能说明

| 产物 | 用途 |
|---|---|
| **Hypernob Crystal** | 插气罐→抑反应; 插太空服→防水压 |
| **Nitrium Crystal** | 敲碎→Nitrium烟雾(吸入强化) |
| **Healium/PN/N₂O Crystal** | 手雷→释放对应气体 |
| **Crystal Foam** | 手雷→泡沫封门灭火 |
| **SM Shard** | 可移动超物质碎块 |

### 8.4 电解器

**3种反应**:

| 反应 | 条件 | 速率 | 效果 |
|---|---|---|---|
| **2H₂O→O₂+2H₂** | 水蒸气≥0.01mol | `min(H₂O/2, 2.5×功率²)` | 产H₂燃料 |
| **Hnob→Anob** | SM闪电>5GeV | `Hnob × clamp((功率-5)/4, 0, 1)` | **产Antinoblium!** |
| **BZ→2Halon+0.2O₂** | BZ≥0.01mol | `BZ×(1-e^(-0.5×T×功率/373))` | 放热91,232J, 产Halon |

### 8.5 工程应用产线

```
1. 钻石: CO₂→结晶器(10000~30000K)→钻石
2. Plasma锭: Plasma+BZ→结晶器(10~20K)→锭
3. SM碎块: Plasma(5000)+O₂(4500)+BZ(200)+Hnob(250)+Anob(250)→结晶器(10~20K)→SM Shard
4. Antinoblium: SM腔→Hnob注入→SM闪电→电解器→Anob
5. 热冰冷却: Freon+Plasma+O₂→结晶器(15~35K吸热)→热冰
```

---

# 第四卷B · 发电系统（深挖补充）

> **代码**: `code/modules/power/`（非 SM/奇点部分，~7,300 行）
> **本次新增**: 核查发现工程总章缺整块发电系统，补齐 TEG/涡轮/太阳能/RTG/引力发生器/特斯拉

## 第8B章 · 温差发电 (TEG)

**代码**: `thermoelectric_generator.dm` (222行) + `circulator.dm` (176行)

### 原理

利用**冷热气体温度差**发电。需要一对循环泵（circulator）——一个泵冷气、一个泵热气——夹着 TEG 本体安装。

### 核心公式

```dm
#define TEG_EFFICIENCY 0.65

energy_transfer = ΔT × hot_heat_capacity × cold_heat_capacity / (hot_heat_capacity + cold_heat_capacity)
  其中 ΔT = hot_air.temperature - cold_air.temperature

lastgen += energy_transfer × 0.65        // 65% 转化为电能
heat = energy_transfer × 0.35            // 35% 作为废热还给冷循环

// 功率释放：每 tick 释放 lastgen/10，平滑输出
power_output = round(lastgen / 10)
```

**关键洞察**：
- 输出功率 ∝ 热容乘积 / 热容之和 × ΔT——**两侧热容都要大**，且 ΔT 越大越好
- 废热（35%）被回灌到**冷循环**，所以冷侧需要持续散热（否则冷侧升温、ΔT 缩小、功率衰减）
- 气体种类决定热容：高热容气体（如 BZ/等离子）单循环能带更多能量

### 结构要求

| 项目 | 要求 |
|---|---|
| 位置 | TEG 本体居中，两侧各 1 个循环泵 |
| 方向 | 循环泵朝向 TEG（东侧泵朝西、西侧泵朝东；南北同理） |
| 模式 | 一个设 `CIRCULATOR_COLD`（冷），一个设 `CIRCULATOR_HOT`（热） |
| 连接 | 用**多功能工具 (multitool)** 点 TEG 同步循环泵（`find_circulators()`） |
| 电网 | TEG 必须锚定并接入电力网（`connect_to_network()`） |

### 循环泵 (Circulator) 回顾

`circulator.dm`：双口二进设备，`CIRCULATOR_COLD`/`CIRCULATOR_HOT` 模式决定气流方向，泵送量取决于两侧压差。TEG 的 process_atmos() 每 tick 从两个循环泵取气（`return_transfer_air()`）、计算能量、把降温/升温后的气体**merge 回循环泵的 airs[1]**。

### TEG 界面数据

| 字段 | 含义 |
|---|---|
| cold/hot temperature_inlet | 循环泵进气口温度 |
| cold/hot temperature_outlet | 循环泵出气口温度 |
| cold/hot pressure_inlet/outlet | 进出口压力 |
| last_power_output | 当前输出功率 |

## 第8C章 · 燃气涡轮 (Gas Turbine)

**代码**: `turbine/turbine.dm` (710行) + `turbine_parts.dm` (130行) + `turbine_computer.dm` (120行)

### 结构：3 部件 + 1 电脑

```
[进气压缩机 inlet_compressor] ← [核心转子 core_rotor] → [排气涡轮 turbine_outlet]
        (1000L)                     (3000L)               (6000L)
                        ↕ 多功能工具连接
              [涡轮控制电脑 turbine_computer]
```

| 部件 | 体积 | 功能 |
|---|---|---|
| **进气压缩机** | 1000L | 从前方 turf 吸气，压缩至 1000L（2500L→1000L），温度压力上升 |
| **核心转子** | 3000L | 气体膨胀 1000→3000L，降温降压，带动转子旋转产生功 |
| **排气涡轮** | 6000L | 再膨胀 3000→6000L，进一步降温，排入后方 turf |
| **控制电脑** | — | 启停、转速显示、进气调节（intake_regulator 0.01~1） |

### 核心公式

```dm
// 每级气体转移的功
work_done = n × R × T × ln(压缩前体积×压力 / 输出体积×压力) × TURBINE_WORK_CONVERSION_MULTIPLIER

// 最终功率计算（排气后）
work_done = n × R × T_ejected × ln(压缩机压力 / 排气压力)
work_done = max(work_done - 压缩机功×系数 - 转子功, 0)

// RPM
rpm = ((work_done × 压缩机效率) ^ 涡轮效率) × 转子效率 / TURBINE_RPM_CONVERSION
rpm = min(rpm, max_allowed_rpm)

// 发电
produced_energy = rpm × TURBINE_ENERGY_RECTIFICATION_MULTIPLIER × TURBINE_RPM_CONVERSION × seconds_per_tick
```

### 部件升级系统（4 档）

| 档位 | 升级材料 | RPM上限倍率 | 温度上限 | 效率 |
|---|---|---|---|---|
| T1 | 基础打印 | ×1 (基准) | 50,000K | 压缩机/转子 25% |
| T2 | 等离子钢×10 | ×2.5 | 50,000^1.2 | +20% |
| T3 | 钛×10 | ×6.25 | 再^1.2 | +20% |
| T4 | 金属氢×5 | ×15.625 | 再^1.2 | +20% |

**特殊规则**：
- **Stator（定子）效率特殊**：T1=85%，之后每档 +1.5%（与压缩机/转子不同）
- **Stator 升级材料不同**：T2=钛×15、T3=金属氢×15、T4=Zaukerite×10
- 升级方式：手持材料点击部件（`item_interaction`），每次升级消耗对应材料
- 每部件 `get_tier_value(TURBINE_MAX_RPM)`：T1=基准，每档 ×2.5
- 温度上限公式：`50,000 ^ 1.2` 逐级（T4 可达聚变温度！）

### 损伤与爆炸

```dm
// 温度损伤（基于进气温度与部件耐温差）
damage_done = round(log_90(max(温差, 1)), 0.5)      // 温差越高伤害越大
damage = min(previous + damage_done × 0.5, previous + TURBINE_MAX_TAKEN_DAMAGE)
// 温度低于上限时缓慢自愈
if (温度差 < 0) damage = max(damage - TURBINE_DAMAGE_HEALING, 0)

// 爆炸分级（rpm 越高炸得越狠）
integrity ≤ 0 时:
  rpm < 基准        → explosion(0, 1, 4)
  rpm < 基准×2.5    → explosion(0, 2, 6)
  rpm < 基准×6.25   → explosion(1, 3, 7)
  rpm ≥ 基准×6.25   → explosion(2, 5, 7)
```

- 完整度 = `100 - damage/500 × 100`
- 损坏报警：`damage 增加 ≥2` 或 `damage > 阈值` 时工程频道告警（含位置和完整度%）
- 维修方式：温度降回上限以下即自愈（`TURBINE_DAMAGE_HEALING`/tick）

### 操作要点（官方纸面指南）

1. 把气体放入燃烧室点燃（等离子+氧 等）
2. 从电脑启动（需所有部件连接 + 面板关闭 + 方向正确）
3. 过热会损坏 → 升级部件提高耐温（T4 可承受聚变温度）
4. **气体利用率低**：很多未燃气体直接通过——可预燃烧或加过滤回收系统
5. 进气调节器（intake_regulator）控制进气量，默认 0.5，范围 0.01~1
6. 关机要求 RPM < 1000

## 第8D章 · 太阳能 (Solar)

**代码**: `solar.dm` (647行)

### 核心常量与公式

```dm
#define SOLAR_GEN_RATE 2500    // 基础发电量 W

// 每 tick 发电
sgen = SOLAR_GEN_RATE × sunfrac × power_tier
// sunfrac = cos(面板方位角 - 太阳方位角)，clamp 到 [0,1] —— Lambert 余弦定律
// 面板被遮挡时 sunfrac = 0
```

### 玻璃材质 → 功率档位

| 玻璃 | power_tier | 发电倍率 |
|---|---|---|
| 普通玻璃 | 1 | ×1 (2500W) |
| 钛玻璃 | 2 | ×2 (5000W) |
| 等离子玻璃 | 3 | ×3 (7500W) |
| 塑料钛玻璃 | 4 | ×4 (10000W) |

### 组件

| 组件 | 功能 |
|---|---|
| **太阳能面板** | 发电本体，`max_integrity=150`，损坏时 unset_control 并随机转 160-200° |
| **太阳能组装件 (solar_assembly)** | 可携带的建造套件：扳手锚定 + 玻璃封顶；加追踪电路→追踪器 |
| **追踪器 (tracker)** | 自动跟踪太阳（`sun_update`），需追踪电路板 |
| **控制电脑 (solar_control)** | 连接面板+追踪器，手动/定时/自动追踪模式 |

### 追踪模式

| 模式 | 行为 |
|---|---|
| `SOLAR_TRACK_OFF` | 手动：直接设方位角 |
| `SOLAR_TRACK_TIMED` | 定时：每 tick 方位角 += azimuth_rate（默认=太阳基础转速） |
| `SOLAR_TRACK_AUTO` | 自动：用追踪器实时对准太阳（需已连接追踪器） |

- 方位角可设范围：`-360 ~ 719.99`（自动归一到 0~360）
- 转速上限：`±2 × SSsun.base_rotation`
- 历史记录：每 `SSsun.wait` 记录一次供电/容量，保持一个完整太阳周期

### 性能提示

- 面板必须接电缆节点（`connect_to_network`）
- **空间藤蔓遮挡阳光**（除非透明突变）——`search_for_connected` 会跳过被藤蔓遮挡的面板
- 太阳被站体遮挡时 `sunfrac=0`，但面板仍会尝试转向
- 官方指南建议至少 20 块面板 + 1 追踪器 + 1 电脑

## 第8E章 · RTG 放射性同位素发电机

**代码**: `rtg.dm` (174行)

### 核心机制

```dm
power_gen = 1 KILO WATTS   // 基础：持续 1kW，无需燃料，数十年
// 受部件影响：part_level = Σ(部件档位)，power_gen = base × (part_level || 1)
// 每 tick: add_avail(power_to_energy(power_gen))
```

### 变体

| 变体 | 功率 | 备注 |
|---|---|---|
| **基础 RTG** | 1 kW | 部件升级可倍增功率 |
| **Advanced RTG** | 1.25 kW | 等离子辐射收集器增效，但寿命缩短 |
| **Lavaland RTG** | 20 kW | 仅熔岩地块+采矿层发电（`islava && is_mining_level`） |
| **Old Station RTG** | 0.75 kW | 废弃站用，拆卸会变灰烬 |
| **Void Core（虚空核心）** | 20 kW | 绑架者(Abductor)科技；**受击/火焰/爆炸/EMP 会过载**→10秒后爆炸(2,3,4)+特斯拉电弧 |
| **Debug RTG** | 20 kW | 管理用 |

### 特点

- 可绑人（`can_buckle=TRUE`），需要约束（buckle_requires_restraints）
- 不可摧毁（`INDESTRUCTIBLE`）——基础 RTG 是"无限燃料"的可靠电源
- 常用于废弃站/哨站代替 SMES

## 第8F章 · 引力发生器 (Gravity Generator)

**代码**: `gravitygenerator.dm` (533行)

### 功能

为整个 Z 层（station trait 层全部 z）提供人造重力。**不可摧毁**（`INDESTRUCTIBLE`，只有 EXPLODE_DEVASTATE 才能打坏）。

### 结构

```
9×9 区域（从底部中间生成 3×3 偏移），主件+9 个部件
中心部件承载覆盖层动画；上 3 个部件无密度（顶部）
```

### 状态机

```dm
POWER_IDLE(0) / POWER_UP(1) / POWER_DOWN(2)
charge_count: 0~100，充放速度 2/tick
  0→20  无覆盖层
  21→40 startup
  41→60 idle
  61→80 activating
  81→100 activated → enable()

breaker(主开关) + on(运行) + charge_count(充能)
供电不足/关 breaker → POWER_DOWN → charge_count 降到 0 → disable()
```

### 损坏修复流程（4 步）

| 步骤 | 工具 | 说明 |
|---|---|---|
| 1 | 螺丝刀 | 固定框架 (GRAV_NEEDS_SCREWDRIVER→WELDING) |
| 2 | 焊接器 | 修补框架 (→PLASTEEL) |
| 3 | 等离子钢×10 | 添加装甲板 (→WRENCH) |
| 4 | 扳手 | 固定装甲 → set_fix() 恢复 |

### 其他机制

- **启停震动全站**：`shake_everyone()` 相机震动 + 全站公告（重力在线/离线）
- **radioactive_nebula_shielding = 4**：运行时提供放射性星云护盾
- 引擎事故（特斯拉/奇点）会直接删除它（zap 时 qdel，防连环爆）
- **blackout()**：停电事件/重力异常爆炸会强制关闭（charge=0, breaker=FALSE）
- 重力覆盖所有 `ZTRAIT_STATION` z 层（多层站共享）

## 第8G章 · 特斯拉引擎 (Tesla)

**代码**: `tesla/energy_ball.dm` (379行) + `tesla/coil.dm` (169行)

### 核心机制

```dm
TESLA_DEFAULT_ENERGY = 695.304 MJ    // 主球每次放电功率
TESLA_MINI_ENERGY   = 347.652 MJ    // 迷你球

// 每 tick:
handle_energy()  → 能量积累
move(4 + 球数×1.5) → 随机移动（带动量惯性，30% 概率朝之前电击目标方向）
tesla_zap(range=3, power=695MJ) → 主放电
迷你球各放电: power = TESLA_MINI_ENERGY / 7 × range
```

### 能量升级/降级

| 事件 | 条件 | 效果 |
|---|---|---|
| **升级** | energy ≥ energy_to_raise (32 起步) | 阈值 ×1.25，10 秒后生成 1 个迷你球 |
| **降级** | energy < energy_to_lower (-20) | 阈值 /1.25，删除 1 个迷你球 |

- 迷你球：0.3~0.7 倍缩放，轨道半径随机，速度 3-6
- 主球每 tick 会**灰飞烟灭**路径上的碳基生物（除非附近 2 格有接地棒）
- 球体不可摧毁（`INDESTRUCTIBLE`），可通过放电给线圈/被接地棒消耗

### 特斯拉线圈 (Tesla Coil)

| 项目 | 数值 |
|---|---|
| 功能 | 吸收球体放电 → 转化为电网电力 |
| 转化率 | `input_power_multiplier`（电容部件提升，最高 50%） |
| 充能间隔 | `zap_cooldown = 100 - 电容档×20` |
| 主动放电 | 用 20% 电网盈余（`avail×0.2×multiplier`）产生 zap（range=10） |
| 绑人 | 可以绑人（惩戒叛徒的传统玩法） |

### 接地棒 (Grounding Rod)

| 项目 | 数值 |
|---|---|
| 功能 | 吸收放电保护区域，**不向电网输出**（release_energy 返回 FALSE） |
| 储能 | 吸收全部 zap 能量（stored_energy），可持续释放计算 |
| 保护 | 球体路过时若 2 格内有接地棒则不灰飞人（`dust_mobs` 检查） |

### zap 目标优先级

```
自行车 > 特斯拉线圈 > 接地棒 > 骑乘载具 > 活体 > 机械 > blob > 结构
```

- 活体伤害：`min(round(power/600), 90) + rand(-5,5)`，硅基额外 EMP
- 每次 zap 后 20% 概率分裂成 2 道（各 50% 功率）继续跳跃
- 空气中水电解：`air_mixture.electrolyze(power/200)`（zap 路径上产生氢/氧）

### 特斯拉作为引擎

1. 释放特斯拉球（需先造能量——通常用粒子加速器或 SM 泄压）
2. 周围布置特斯拉线圈阵列（吸收放电→电网）
3. 接地棒保护关键区域
4. **失控风险极高**：球体会持续移动、生成迷你球、灰飞路人——需要围栏

## 第8H章 · 补充机制（核查补齐）

### reaction_factors.dm（反应手册文本，216行）

每个气体反应在游戏内手册（大气控制台）显示的消耗/产物说明。关键数值（与第2章反应网络对应）：

| 反应 | 手册要点 |
|---|---|
| 水蒸气凝结 | 凝结消耗 MOLES_GAS_VISIBLE 摩尔；冻结不消耗；都需最低摩尔数 |
| 瘴气消毒 | 速率随温度差缩放；每摩尔瘴气产 1 摩尔氧；释放 MIASTER_STERILIZATION_ENERGY |
| 等离子燃烧 | 氧耗 1 摩尔/mol 等离子（低温）→ 2 摩尔（高温）；97 倍氧时产氚 1:1；否则产水汽 0.25 + CO₂ 0.75 |
| 氢气燃烧 | 氧 0.5/mol H₂；产水汽 1:1 |
| 氚燃烧 | 氧 0.5/mol；产水汽 1:1；释放辐射 |
| 制冷剂燃烧 | 产 CO₂ 1:1；特定温度产热冰 |
| N₂O 合成 | 需 10 氧 + 20 氮 + 5 BZ（BZ 不消耗）；产 N₂O |
| N₂O 分解 | 产 0.5 氧 + 1 氮每摩尔 |
| BZ 合成 | 等离子 0.8 + N₂O 0.4 每摩尔 BZ；N₂O 不足时分解 |
| Pluoxium | 每摩尔耗 1 CO₂ + 0.5 氧；氚→氢 0.01 |
| Nitrium 合成 | 需 5 BZ + 20 氚 + 10 氮；BZ 耗 0.05/摩尔 |
| Freon 合成 | 需 0.06 等离子 + 0.03 CO₂ + 0.01 BZ 起步；峰值 800K，>5500K 效率×3 |
| Hyper-Noblium | 需 10 氮 + 5 氚；BZ 降低能量释放与氚消耗 |
| Halon 除氧 | 20 氧/摩尔 halon；产 5 CO₂ |
| Healium | BZ 1/12 + Freon 11/12 每摩尔 |
| Zauker | 耗 Hnob 0.02 + Nitrium 1/摩尔；分解产 0.7 氮 + 0.3 氧 |
| Proto-Nitrate | Pluoxium 1/11 + 氢 10/11；氢响应需 2 氢/摩尔；氚响应释放中子辐射 |
| Antinoblium | 需 MOLES_GAS_VISIBLE 自复制，>REACTION_OPPRESSION_MIN_TEMP |

### machine_connector.dm（146行）

`/datum/gas_machine_connector`——给非大气机械（如 cryo 冷冻仓）提供大气接口的辅助 datum，让 cryo 等设备无需重写路径即可接入气网。含 `connected_machine` + `gas_connector` 两个引用。

### multiz.dm（60行）

`/obj/machinery/atmospherics/pipe/multiz`——**跨层管道适配器**（trinary），允许管道连接不同 Z 层的气网。`paintable=FALSE`，无气体视觉，用于多层站（如采矿层↔站体）。

## 第8I章 · 电力网基础设施（APC/SMES/电缆/电网）

**代码**: `power.dm` (537行) + `powernet.dm` (130行) + `cable.dm` (876行) + `apc/apc_main.dm` (835行) + `smes.dm` (560行)

> 发电设备产电后，通过电缆→电力网（powernet）→APC（区域配电）→SMES（储能）这一整套基础设施送到每个机器。这是工程玩家日常维护的核心。

### 8I.1 电力网 (Powernet) 核心

`/datum/powernet`——**每片连续的电缆+机器网络**。切电缆会把一个网分裂成多个。

| 字段 | 含义 |
|---|---|
| `cables` | 网络中所有电缆 |
| `nodes` | 网络中所有发电/用电机器 |
| `avail` | 当前可用功率（上一 tick 收集） |
| `newavail` | 本 tick 新收集的功率 |
| `load` | 当前负载（每台机器处理时累加） |
| `delayedload` | tick 之间的延迟负载 |
| `netexcess` | 盈余 = avail - load |

**处理流程（每 tick reset()）**：
```dm
netexcess = avail - load
if(netexcess > 100 && 有节点)
    for SMES in 节点: SMES.restore()   // 盈余回充 SMES
load = delayedload;  delayedload = 0
avail = newavail;    newavail = 0
```

**关键 API**：
- `add_avail(amount)` — 发电端：把功率加进 `newavail`
- `add_load(amount)` — 用电端：把负载加进 `load`
- `surplus()` — 返回 `clamp(avail-load, 0, avail)` 当前可用盈余
- 电击伤害：`get_electrocute_damage()` = f(avail)（假设 1 秒接触）

**电击伤害公式**（`ELECTROCUTE_DAMAGE`）：基于 avail 功率，电网功率越大摸电缆越疼。

### 8I.2 电缆 (Cable) 与电缆盘

**3 层电缆**（可上下叠加独立布线）：

| 层 | 颜色 | 说明 |
|---|---|---|
| Layer 1 | 红 | `CABLE_LAYER_1`，图标 `l1-*` |
| Layer 2 | 黄 | `CABLE_LAYER_2`（默认），图标 `l2-*` |
| Layer 3 | 蓝 | `CABLE_LAYER_3`，图标 `l4-*`（注意图标号是 l4） |

**电缆盘 (cable coil)**：
- 每盘 30 单位（MAXCOIL）
- 每单位材料：铁 0.1 + 玻璃 0.1
- 研磨：`grind_results → 铜 2u`（贫民窟化学联动！）
- 15 单位可做束具（CABLE_RESTRAINTS_COST）
- 每单位 = 1 格电缆（`place_turf` 消耗）

**连接规则**：
- 同层电缆才能互连（`cable_layer & cable_layer` 检查）
- 电缆自动连接 4 方向相邻的同层电缆
- 节点判定：终端/SMES 下方、电网机器（`should_have_node`）、格栅/加固桌
- 剪切（wirecutter）：`shock(user, 50)` 50%概率被电（无绝缘手套）；副手剪切可单独切断/恢复某方向连接（径向菜单）
- 爆炸破坏：`BOMB` 伤害对地板下电缆 ×0.25（减伤），被炸断会触发灯光闪烁传播
- 老鼠王会啃电缆：通电时老鼠被电 10 伤害，电缆被拆

### 8I.3 终端 (Terminal)

- 连接机器（SMES/APC/发电机）与电网的**中间设备**，自带 `master` 引用
- 电缆层可切换（红/黄/蓝）
- 机器通过 `connect_to_network()` 查找本格电缆节点或终端接入电网

### 8I.4 APC 区域配电控制器

每区域一个，通过终端接入电网，控制区域内 3 个供电通道。**NOVA 版关键阈值**：

| 常量 | 值 | 含义 |
|---|---|---|
| `CHARGELEVEL` | 0.01 | 电池充电速度上限（1%/秒） |
| `APC_CHANNEL_LIGHT_TRESHOLD` | **10%**（NOVA改，原15） | 低于此：关灯+关设备，仅环境 |
| `APC_CHANNEL_EQUIP_TRESHOLD` | **20%**（NOVA改，原30） | 低于此：NOVA改为**关灯保设备**（原版关设备保灯） |
| `APC_CHANNEL_ALARM_TRESHOLD` | 75% | 高于此：清除电源警报 |

**3 通道**：Equipment（设备）/ Lighting（照明）/ Environ（环境/门禁泵）

**处理循环（late_process）**：
```dm
// 1. 读区域用电
lastused_light = 通道开 ? area.energy_usage[LIGHT] : 0
// 2. 判定外部电源状态
无avail → NO_POWER；盈余≤0 → LOW_POWER；否则 HAS_POWER
// 3. 按电池电量分级降载：
电量=0    → 全通道 FORCE_OFF + 电源警报 + 强制夜灯
电量<10%  → 关设备+关灯，仅环境 + 警报
电量<20%  → NOVA: 关灯保设备 + 警报
电量≥20%  → 全开；>75% 清除警报
// 4. 充电（charge_channel）：
需要量 = 下一阈值充电需求，充电速率 = min(电池充电率, 最大×1%)
```

**其他机制**：
- **电池**：默认 upgraded 电池 2500 容量，启动 90% 电量；可换 5k/10k 电池
- **夜灯模式**：低电量自动开夜灯省电（`set_nightshift`），恢复供电后按安全等级关闭
- **Malf AI**：黑掉 APC 后每 30 秒耗 60kJ 生成 1 点处理时间；可承载 AI 进驻
- **灰潮事件**：`grey_tide()` 把所有站内 APC 灯光关掉（趁黑作乱）
- **EMP**：破坏通道状态；`overload_lighting()` 烧灯（耗 2% 电池）
- **电磁黑客**：解锁（`unlock`）、嫌犯访问（`give_syndicate_access`）等 mapping helper
- 温度 >2000K 时受损（`atmos_expose`，伤害 = 温度/100 上限10）

### 8I.5 SMES 储能单元

**核心参数**：
- 输入：`input_level` 默认 50kW，上限 `input_level_max` = 200kW × 电容系数（`2^(电容档-1)`，NOVA 版）
- 输出：`output_level` 默认 50kW，上限同上
- 容量：`total_capacity` = Σ 电池最大容量

**处理循环（process）**：
```dm
// 输出（放能）：
output_used = adjust_charge(-output_level)  // 从电池取电
if(output_used) add_avail(output_used)      // 注入电网
// 输入（充电）：
input_available = terminal.surplus()         // 看电网盈余
load = adjust_charge(min(input_level, input_available))
terminal.add_load(load)                      // 从电网取电
// restore()（电网盈余回充，每 tick 电网 reset 时调用）：
excess = min(output_used, netexcess)         // 输出未用完的部分收回
adjust_charge(excess)                        // 重新存回
```

**安装/操作**：
- 需要终端（10 单位电缆，从面板打开后安装）；终端朝向 SMES
- 面板开启后可换电缆层（multitool）
- EMP：随机开关输入/输出 + 随机功率 + 掉电（`STANDARD_BATTERY_CHARGE / severity`）
- 变体：`/full`（50×标准）、`/super/full`（100×）、`/engineering`（开局50×+输出90kW）、`/ship`（20×）、`/magical`（无限电）

### 8I.6 电力流完整链路

```
发电（SM/TEG/涡轮/太阳能/RTG/特斯拉线圈/奇点）
  → add_avail() 注入 powernet
  → 电缆传输（同层连接）
  → SMES 充电（terminal.surplus() 判断盈余）
  → APC 通过终端取电（surplus 优先，电池兜底）
  → 区域 3 通道分配（设备/照明/环境）
  → 机器 use_energy() / powered() 检查
  → 多余盈余 → SMES.restore() 回充（每 tick 电网 reset）
```

**断电排查顺序**（工程实务）：
1. 发电机是否在发电（add_avail 生效）？
2. 电缆是否被切断/爆炸破坏？（multitool 点电缆看 avail/load/excess）
3. SMES 是否输出？（检查 output_attempt + 电量）
4. APC 是否运行？（检查 operating + 电池电量 + 通道状态）
5. 区域是否 requires_power？（空间/穿梭机区域免费供电）

---

### 8I.7 电池全录（32 种）

**代码**: `power/cell.dm`（277 行）+ `power/battery.dm`（97 行）——基准：STANDARD_CELL_CHARGE = **10kJ** / STANDARD_CELL_RATE = **10kW** / STANDARD_BATTERY_CHARGE = **1MJ** / STANDARD_BATTERY_RATE = **1MW**

#### 普通电池 cell（24 种）

| 电池 | 容量 | 充电速率 | 特性 |
|---|---|---|---|
| **标准电池** | 10 kJ | 500 W | 基础；可作鱼饵（含锂） |
| **NT AA（垃圾）** | 5 kJ | 500 W | 玻璃外壳便宜货 |
| **升级电池** | 25 kJ | 250 W | 9V 外观 |
| **升级+** | 50 kJ | 500 W | 加强 |
| **高容量** | 100 kJ（10×） | 7.5 kW | EMP mod 3 |
| **超容量** | 200 kJ（20×） | 10 kW | EMP mod 5 |
| **极容量** | 300 kJ（30×） | 15 kW | 需金/银 |
| **蓝空间** | 400 kJ（40×） | 20 kW | 跨维充能，需钛/钻石 |
| **无限电池** | ∞ | ∞ | 永不消耗 |
| **虚空核心** | 500 kJ | ∞ | 绑架者外星科技 |
| **土豆电池** | 3 kJ | 500 W | 生物电池，初始 30% |
| **EMP 免疫** | 5 kJ | 500 W | 自带防护 |
| **史莱姆核心** | 50 kJ | 500 W | 有机体 |
| **应急灯微型** | 1.2 kJ | 500 W | 应急照明约 10 分钟 |
| **水晶电池** | 500 kJ（50×） | **不可充** | 等离子结晶 |
| 脉冲步枪 | 400 kJ | 7.5 kW | 约 200 发 |
| 脉冲卡宾枪 | 50 kJ | 500 W | 约 25 发 |
| 脉冲手枪 | 20 kJ | 500 W | 约 10 发 |
| 忍者黑电池 | 100 kJ | 10 kW | EMP 脆弱 |
| 安保博格 D | 6 kJ | 500 W | — |
| 微型电击枪 | 6 kJ | 500 W | — |
| X-01 治安官 | 12 kJ | 500 W | — |
| 激光手枪 | 10 kJ | 1.5 kW | — |
| 以太体内 | 特殊 | — | 内部用 |

#### 巨型电池 megacell（8 种）

| 电池 | 容量 | 充电速率 |
|---|---|---|
| **巨型电池** | 1 MJ | 50 kW |
| 升级巨型 | 2.5 MJ | 500 kW |
| 高容量巨型 | 10 MJ | 750 kW |
| 超容量巨型 | 20 MJ | 1 MW |
| 极容量巨型 | 30 MJ | 1.5 MW |
| 蓝空间巨型 | 40 MJ | 2 MW |
| NT AA 巨型 | 0.5 MJ | 50 kW |
| 无限巨型 | ∞ | ∞ |

> **通用机制**：评级 = maxcharge/(rating_base×10)；初始满电；爆炸损坏（50%/25% 容量减半，10% 可引爆）；EMP 消耗 (10kJ/severity)×mod；以太体吞电池（3.5s，8% 转化）。

**NOVA 新增**：手摇电池（摇 1s 充 1kJ）/自充电池（50/150/300 kJ）/多电池充电器。

### 8I.8 电力流完整链路（补充）

```
发电（SM/TEG/涡轮/太阳能/RTG/特斯拉/奇点）
  → add_avail() 注入 powernet
  → 电缆传输（同层连接，3 层红黄蓝）
  → SMES 充电（terminal.surplus() 判断盈余）
  → APC 通过终端取电（surplus 优先，电池兜底）
  → 区域 3 通道分配（设备/照明/环境）
  → 机器 use_energy() / powered() 检查
  → 多余盈余 → SMES.restore() 回充（每 tick 电网 reset）
```

**断电排查顺序**（工程实务）：
1. 发电机是否在发电（add_avail 生效）？
2. 电缆是否被切断/爆炸破坏？（multitool 点电缆看 avail/load/excess）
3. SMES 是否输出？（检查 output_attempt + 电量）
4. APC 是否运行？（检查 operating + 电池电量 + 通道状态）
5. 区域是否 requires_power？（空间/穿梭机区域免费供电）

---

### 8I.9 便携电源与监控

**代码**: `power/port_gen.dm`（288 行）+ `power/smes_portable.dm`（309 行）+ `power/monitor.dm`（113 行）+ `power/floodlight.dm`（336 行）

#### 便携发电机（3 种）

| 发电机 | 燃料 | 输出 | 档位 | 燃料消耗 | 备注 |
|---|---|---|---|---|---|
| **P.A.C.M.A.N.** | 等离子板 | 10 kW×档 | 1-4 | 180s/张 | 最多 50 张；**过热 >300 爆炸**（emag 后 400） |
| **Super P.A.C.M.A.N.** | 铀板 | 30 kW×档 | 1-4 | 60s/张 | 最多 20 张 |
| **预装燃料版** | 等离子×15 | 10 kW×档 | 1-4 | 同上 | 初始 15 张 |

#### 便携储能

| 设备 | 容量 | 说明 |
|---|---|---|
| **电源连接器** | 汇总 | 同格连接储能单元 |
| **便携储能单元** | 组件电池之和（megacell 1MJ/颗） | 扳手 4s 连接/8s 断开 |
| **超容量单元** | 同上 | 远距前哨用 |
| **满电超容量** | 初始 100 MJ | 地图即满电 |

#### 探照灯（4 档）

| 档位 | 照明范围 | 耗电 |
|---|---|---|
| 低功率 | 5 格 | 1 kW |
| 标准 | 10 格 | 2 kW |
| 高功率 | 15 格 | 3 kW |

> 必须直连电线；电力不足自动降档；可喷漆改色；构造 5 铁+5 电缆+灯管。

#### 电力监控台

| 机制 | 值 |
|---|---|
| **记录** | 供电/需求历史各 60 条（每 5 秒采样） |
| 显示 | 电网 supply/demand/各 APC 电池百分比/三通道状态 |

#### 太阳能追踪器

- 追踪太阳方位角；AUTO 时自动转动太阳能板；耐久 250

---

### 8I.10 照明系统（lighting/ 1,456 行）

**代码**: `power/lighting/`（light 861 + light_construct 205 + light_items 146 + mapping 179 + wallframes 65）+ SSlighting 子系统

#### 灯具参数

| 参数 | 值 |
|---|---|
| **管灯 tube** | 亮度 8/光角 170° |
| **泡灯 bulb** | 亮度 4 |
| **地灯 floor** | 亮度 4/光角 360° |
| 功耗通道 | AREA_USAGE_LIGHT（按区域结算） |
| 耐久 | 100 |

#### 灯泡状态（4 态）

| 状态 | 说明 |
|---|---|
| **正常** | 正常工作 |
| **空** | 无灯泡 |
| **烧毁** | 开关次数累积触发（switchcount） |
| **破碎** | 被打碎/损坏 |

> 烧毁概率随开关次数增加（开关越多越易烧）。**灯泡替换器**（lightreplacer）可快速更换。

#### 4 种模式

| 模式 | 亮度 | 颜色 |
|---|---|---|
| **正常** | 8 | 默认白 |
| **夜班** | 8/功率 0.45 | 暖色 #FFDDCC |
| **低功率模式** | 0.25× | 红（APC 低电量） |
| **应急** | 0.75× | 红 #ff4e4e（major emergency） |

#### 应急照明

| 机制 | 值 |
|---|---|
| **内置电池** | 每灯应急电池（1.2 kJ，约 10 分钟） |
| 应急模式 | 断电自动切换红色应急灯 |

#### 日光系统

- NOVA mapping 日光模块（自然光/昼夜）

> **灯具施工链**：拆除→框架→拉线→封板→装灯（light_construct 5 阶段）。灰潮事件全站关灯。

---

### 8I.11 线缆黑客系统（Wire Hacking）

**代码**: `code/datums/wires/`（31 文件 2,272 行）——**30 种可黑客设备**："push 不同线缆有不同效果"的核心机制

#### 线缆基础机制

| 机制 | 说明 |
|---|---|
| **工具** | 万能工具（脉冲）/剪线钳（剪断）/组件 |
| **颜色** | 17 种可能（红黄蓝绿紫等）——**每台设备随机分配** |
| **剪断/修复** | 剪断后线失效/再剪恢复 |
| **脉冲** | 触发线效果（剪断的线不能脉冲） |
| **装饰线** | 假线混淆（剪了无效果） |
| **EMP** | 随机脉冲最多 3 根线（33% 概率/线） |
| **状态提示** | 开面板看灯/接口状态判断 |

#### 气闸（黑门！15 种线+2 假线，11 变体）

> **操作流程**：1. 打开门面板（螺丝刀卸面板）→ 2. 万能工具打开线缆界面 → 3. 逐根脉冲测试（脉冲=试探效果，不会造成永久伤害）→ 4. 找到目标线后用剪线钳剪断。**注意**：门通电时非硅基生物开面板会被电击 100。

| 线缆（15 种） | 剪断效果 | 脉冲效果 |
|---|---|---|
| **主电源 1/2** | 失去主电源（**两根都修复才恢复**），电击操作者 50 | 暂时失去主电源 |
| **辅助电源 1/2** | 失去备用电源（两根都修复才恢复），电击操作者 50 | 暂时失去备用电源 |
| **门闩** | **上闩锁死**（修复无效！剪线钳剪不回） | 切换门闩（上闩；已上闩且有电则解锁） |
| **ID 扫描** | 无剪断处理 | **禁用紧急访问**+闪红灯（拒绝动画） |
| **AI 连接** | 禁 AI 控制（修复恢复） | 禁 AI 控制 1 秒（自动恢复） |
| **反馈** | 禁灯光+声音（修复恢复） | 切换反馈 |
| **开门** | 无剪断处理 | **开门**（被 emag 或需 ID 权限的门无效；开着则关闭） |
| **保险** | **关闭门保险（可夹人）**（修复恢复） | 切换保险；关保险时门开着立即关门 |
| **高压接地** | **永久通电**（电开门者），电击操作者 100，记 combat log | 通电 10 秒+电击 100 |
| **计时** | 禁自动关门（修复恢复；修复且门开着立即关门） | 切换关门速度（正常/慢速） |
| **无限制出口** | 禁用单向出口（修复后随机换方向） | 出口方向旋转 180°（需装闩锁） |
| **高压电路 1/2** | 电击操作者 50 | 无处理 |
| 假线 ×2 | 无效果 | 无效果 |

**变体（11 种）**：
- **Generic 普通气闸**：共享配色字典（全图同配色，跨局随机）
- **High Security 安全气闸**：**每台独立随机配色，不可破解**（interactable 直接 FALSE）
- Maintenance 维修 / Command 指挥 / Service 服务 / Security 安保 / Engineering 工程 / Medbay 医疗 / Science 科研 / AI / Cargo 货运：各自独立共享配色字典

**状态灯含义**（开面板看）：
- 门闩指示（锁没锁）/ 测试灯（有无电）/ AI 连接灯 / 线路检查灯（保险状态）/ 计时器灯（自动关门）/ 速度灯 / 应急灯 / 无限制出口显示屏

**黑客目标速查**：
- **想进被锁的门**：剪**门闩线**？不行——剪门闩=永久锁死！应该脉冲**开门线**（需门未 emag）+剪**保险线**（防止夹人）+剪**计时线**（防自动关门）
- **想困住某人**：剪**保险线**（关保险夹人）+剪**计时线**（防门自动开）+剪**主电源**（门断电打不开）
- **想电人**：剪**高压接地线**（永久通电，开门者被电 100）
- **想防 AI**：剪**AI 连接线**

#### APC（黑 APC！7 种线+6 假线）

> **操作流程**：1. 撬棍/螺丝刀开 APC 面板（panel_open）→ 2. 万能工具开线缆界面 → 3. 逐根脉冲试探 → 4. 剪目标线。**注意**：APC 面板打开但未完全打开（panel_open && !opened）时才能交互。

| 线缆（7 种） | 剪断效果 | 脉冲效果 |
|---|---|---|
| **装备线** | 装备通道断电（修复恢复自动） | 切换装备通道（关↔自动开） |
| **灯光线** | 灯光通道断电（修复恢复自动） | 切换灯光通道 |
| **环境线** | 环境通道断电（修复恢复自动） | 切换环境通道 |
| **主电源 1/2** | **短路 APC**（两根都修复才恢复），电击操作者 50 | 短路 2 分钟（自动恢复） |
| **接口线** | **锁定 APC 面板**（修复解锁） | 解锁 30 秒（自动重新锁定） |
| **AI 连接** | 禁 AI 控制（修复恢复） | 禁 AI 控制 1 秒 |
| 假线 ×6 | 无效果 | 无效果 |

**状态灯含义**：
- 接口灯（红=锁定/绿=解锁）/ 短路指示器（亮=短路）/ 通道 1-3 灯（装备/灯光/环境开合状态）/ AI 连接灯

**黑客目标速查**：
- **黑掉区域供电**：剪**主电源线**（短路 APC，整个区域断电）
- **关灯潜入**：剪**灯光线**（灯光通道断电，区域变黑）
- **解锁 APC 面板**：脉冲**接口线**（解锁 30 秒窗口内改设置/取电池）
- **防 AI 干预**：剪**AI 连接线**

#### 辛迪加炸弹（拆弹！4 主线+2 爆炸线，随机色）

| 线缆 | 剪断效果 | 脉冲效果 |
|---|---|---|
| **激活** | — | 启动炸弹 |
| **延迟** | 增加延迟 | 切换延迟 |
| **继续** | 拆除进度 | 继续 |
| **卸螺栓** | 卸螺栓 | 卸螺栓 |
| **爆炸线 ×2** | **立即爆炸！** | **立即爆炸！** |

#### 机甲（5 线+3 假线+灯线）

> **操作流程**：1. 用扳手/螺丝刀打开机甲面板（PANEL_OPEN）→ 2. 万能工具开线缆界面 → 3. 逐根脉冲试探 → 4. 剪目标线。**黑机甲=解锁别人的机甲**。

| 线缆 | 剪断效果 | 脉冲效果 |
|---|---|---|
| **ID 扫描** | **解除 ID 锁+清 DNA 锁（解锁机甲！）** | 切换 ID 锁+清 DNA 锁 |
| **卸武** | 禁用装备（修复恢复） | 禁用装备 |
| **电击** | **短路机甲**+电击操作者 50 | 切换短路状态 |
| **超频** | 关闭超频 | 切换超频模式 |
| **发射** | **攻击剪线者**（弹射/武器） | 攻击 |
| **灯光**（有灯机甲） | 强制开灯 | 切换灯光 |
| 假线 ×3 | 无效果 | 无效果 |

**状态灯含义**：橙=短路/红=超频闪烁/绿=ID 锁或 DNA 锁/黄=灯光/蓝=装备禁用

> **黑客目标**：**偷机甲**=剪 **ID 扫描线**（解锁）+剪 **卸武线**（防机甲内人反抗）→ 然后扳手卸驾驶员！

#### MULE 机器人（9 线随机色）

| 线缆 | 剪断效果 | 脉冲效果 |
|---|---|---|
| **电机 1** | 变快（FAST） | 音效 |
| **电机 2** | 变中（AVERAGE） | 音效 |
| **双电机全剪** | **瘫痪（IMMOBILIZED）** | — |
| 全修 | 变慢（SLOW） | — |
| **避障** | 关避障（**撞人**） | 音效 |
| 载重检查 | 无视载重 | — |
| 电源 1/2 | 断电 | 音效 |
| 收发/信标 | 断通讯 | — |

> **黑客目标**：剪**避障线**让 MULE 撞人/剪**双电机**瘫痪它再偷货。

#### 售货机（5 线+1 假线）

| 线缆 | 剪断效果 | 脉冲效果 |
|---|---|---|
| **电源** | 断电 | 断电 |
| **商品** | 卡货 | 出货 |
| **违禁品** | 解锁违禁品（**修复无效**） | 切换 |
| **年龄限制** | 解除年龄限制 | — |
| **黑客锁** | 解锁面板 | — |

> **黑客目标**：剪**违禁品线**（永久解锁隐藏商品）+剪**年龄限制线**（买任何东西）。

#### 微波炉（2 线）

| 线缆 | 剪断效果 | 脉冲效果 |
|---|---|---|
| **电源** | 断电 | 断电 |
| **加热** | 禁用加热 | 加热 |

#### 发射器（2 线）

| 线缆 | 剪断效果 | 脉冲效果 |
|---|---|---|
| **电源** | 断电 | 断电 |
| **发射** | 禁发射 | 发射 |

> **拆弹技巧**：炸弹 6 根线随机色，2 根爆炸线剪了即炸——用万能工具逐根脉冲试探，避开爆炸线，剪"继续/延迟"线推进拆除。高安保设备（气闸/炸弹）线缆随机化，普通气闸固定布局。


#### 颜色随机机制

| 机制 | 说明 |
|---|---|
| **颜色池** | 17 色（红黄蓝绿紫等+色盲灰阶变体） |
| **共享字典** | randomize=FALSE 设备全图同配色（跨局随机） |
| **独立随机** | 辛迪加炸弹/MULE/安全气闸（每台独立配色） |
| **假线** | add_duds 添加无效果线混淆（爆炸装置假线=爆炸线！） |
| **EMP** | 随机脉冲最多 3 根（33%/根；项圈炸弹免疫） |

#### 线缆揭示机制

| 方式 | 揭示范围 |
|---|---|
| **蓝图** | 非随机设备全部揭示 |
| **研究照片** | 拍到蓝图可揭示 |
| **绑架者万能工具** | 全部揭示 |
| **TRAIT_KNOW_ENGI_WIRES** | 气闸/APC/大气警报自动揭示（工程天赋） |
| **TRAIT_KNOW_ROBO_WIRES** | 机甲/Cyborg/MOD 自动揭示 |
| 管理员/AI 鬼魂 | 全部揭示 |

#### 重点设备补充

| 设备 | 要点 |
|---|---|
| **气闸** | 11 变体（维修/指挥/安保…各自配色）；**安全气闸不可破解**；门闩线剪断=永久锁死 |
| **APC** | 接口线剪断=**永久锁定面板**；主电源剪断=短路 |
| **辛迪加炸弹** | **剪激活线=拆除**！剪解闩线=可搬运；剪放行线=隐藏倒计时；爆炸线剪/脉冲=立即爆 |
| **机甲** | 剪 ID 扫描=解锁；发射线剪断攻击剪线者 |
| **MULE** | 双电机全剪=瘫痪 |
| **售货机** | 违禁品线修复无效 |
| **爆炸装置** | 假线=爆炸线（化学手雷/C4/披萨炸弹） |
| **项圈炸弹** | EMP 免疫 |

#### 全部 30 种可黑客设备总览

| 设备 | 主线数 | 装饰线 | 说明 |
|---|---|---|---|
| **气闸** | 15 | 2 | 黑门核心（电源/螺栓/电击/安全） |
| **APC** | 7 | 6 | 区域供电（3 通道/接口解锁） |
| **MULE 机器人** | 9 | 0 | 运输机器人 |
| **大气警报** | 6 | 3 | 大气告警 |
| **大机械臂** | 6 | 0 | 机械臂 |
| **机甲控制** | 5 | 3 | 机甲 |
| **售货机** | 5 | 1 | 商品机 |
| **机器人** | 5 | 2 | 赛博格 |
| **自动车床** | 4 | 6 | 车床 |
| **传真** | 4 | 1 | 传真机 |
| **MOD 控制** | 4 | 2 | MOD 服 |
| **轮盘** | 4 | 0 | 赌桌 |
| **火警** | 3 | 1 | 火警 |
| **研发机器** | 3 | 5 | 科研 |
| **电台** | 3 | 0 | 广播 |
| **扫描门** | 3 | 0 | 扫描门禁 |
| **储物服** | 3 | 2 | 太空服柜 |
| **发射器** | 2 | 0 | 发射器 |
| **全息垫** | 2 | 0 | 全息 |
| **质量驱动器** | 2 | 0 | 弹射 |
| **微波炉** | 2 | 0 | 微波 |
| **爆炸物** | 1 | 3 | 爆炸物（拆弹） |
| **防大气盾** | 1 | 0 | 护盾 |
| **项圈炸弹** | 1 | 0 | 项圈炸弹 |
| **以太嗅探器** | 1 | 0 | 灵异 |
| **护盾发生器** | 1 | 0 | 护盾墙 |
| **特斯拉线圈** | 1 | 0 | 特斯拉 |
| **辛迪加炸弹** | 4 | 0（+2 爆炸线） | **拆弹核心** |
| **传送带** | 0 | 0 | 传送带 |

# 第五卷 · 实验军火

## 第9章 · TTV炸弹制造

**代码**: `transfer_valve.dm` (435行), `tanks/tanks.dm` (爆炸), `ordnance/` (实验设备)

### 9.1 基础概念

**TTV = Transfer Tank Valve**: 一个阀门+两个气罐+触发装置=炸弹

**爆炸原理**:
```
TTV开阀→两罐气体混合→气体反应(燃烧/放热)→温度暴增→压力暴增→碎裂→再反应→爆炸
```

**核心常量**:
| 常量 | 值 | 含义 |
|---|---|---|
| `TANK_LEAK_PRESSURE` | **3,040 kPa** | 开始漏气 |
| `TANK_RUPTURE_PRESSURE` | **3,546 kPa** | 碎裂 |
| `TANK_FRAGMENT_PRESSURE` | **4,053 kPa** | 爆炸所需最低 |
| `TANK_FRAGMENT_SCALE` | **8,511 kPa** | 威力缩放 |

**爆炸公式**:
```dm
air_contents.react(src)  // ← 碎裂前再反应一轮! 关键!
pressure = air_contents.return_pressure()
power = (气罐体积 × (pressure - 4,053)) / 8,511
```

### 9.2 气体选择与配比

| 气体 | 放热 | 氧化剂 | 配比 | 评价 |
|---|---|---|---|---|
| **Plasma** | 3,000,000 J/mol | O₂ | O₂:Plasma≈1.4:1 | ★★★经典 |
| **H₂** | 2,800,000 | O₂ | H₂:O₂=2:1 | ★★★★更强 |
| **Tritium** | 2,800,000+辐射 | O₂ | Tr:O₂=2:1 | ★★★★+辐射 |
| **混合Plasma+H₂** | 综合 | O₂ | 可变 | ★★★★★ |

**示例配比**: H₂炸弹 4,000:2,000 kPa (H₂罐:O₂罐)
- H₂罐(70L/4,000kPa) ≈ 1,120mol
- O₂罐(70L/2,000kPa) ≈ 560mol
- 燃烧放热: 1,120 × 2.8e6 = 3.14e9 J

### 9.3 清洗

**为什么要清洗**: 残留气体稀释燃料/误反应/哑弹

**三种方法**:
| 方法 | 操作 | 需要 |
|---|---|---|
| **真空法**(推荐) | 接洗气机抽空2~3次 | 洗气机+管道 |
| **置换法**(快速) | 充目标气体→排空→重复2~3次 | 气体管道+阀 |
| **压缩机循环**(实验) | 插压缩机→N₂清洗→重复→目标气 | 压缩机 |

**验证**: 气体分析仪扫描→只有目标气体

### 9.4 气罐加压

**标准管道法**: 限制在~1,000kPa, 不够炸弹需求

**气罐压缩机法** (推荐):
```dm
// 安全防爆! 内置屏蔽, 爆炸不会炸出
limit: 5,000 kPa
流速: 20 L/s

操作:
1. 绿管接高压气源
2. 插入气罐+软盘
3. 开压缩机→加压到3,500~4,800kPa
4. 取出 (仅压力<~4,500kPa时可安全取出)
```

**威力估算**: TTV双70L罐+H₂配比→开阀后气体反应→压力暴远超4,053kPa→爆炸

### 9.5 TTV组装

| 步骤 | 操作 |
|---|---|
| 1 | 拿TTV+左键点气罐1(燃料) |
| 2 | 拿TTV+左键点气罐2(氧化剂) |
| 3 | 拿TTV+左键点触发装置 |
| 4 | (可选)右键TTV→开阀(已武装) |
| 5 | (可选)15电缆→背包携带 |

**6种触发装置**:
| 类型 | 触发条件 |
|---|---|
| **信号器** | 收到指定频率无线电信号(远程遥控) |
| **计时器** | 倒计时归零(定时炸弹) |
| **红外** | 红外线被阻断(绊雷) |
| **声控** | 检测到预设关键词 |
| **鼠标陷阱** | 被触碰(诡雷/箱中陷阱) |
| **距离感应** | 有人进入感应范围(接近雷) |

### 9.6 运输与安全

| 方式 | 说明 |
|---|---|
| 分罐运输(最安全) | TTY不装罐, 到目标再组装 |
| 装罐不开阀(较安全) | 气体未混合, 不会反应 |
| **Hypernob抑反应**(最安全) | 插晶体+开抑制→气体不反应→到目标再关 |
| 轮椅炸弹 | TTV点轮椅→轮椅炸弹 |

**安全检查清单**:
```
□ 气罐阀门关闭
□ TTV未开阀
□ 触发装置已锁定
□ 有晶体→反应抑制已开
□ 不在公共区域
```

### 9.7 引爆流程

```
1. 开阀(toggle_valve)→气体混合→反应→压力升
2. 触发装置激活→process_activation()
3. 等待气罐碎裂:
   air_contents.react(src) ← 最后一轮!
   power = (体积×(压力-4053))/8511
   dyn_explosion(power)
```

**去抖动**: `toggle=FALSE` (0.5秒), 防重复触发

### 9.8 进阶技术

**混合燃料**: 单罐预先混合Plasma+H₂ → 叠加放热

**Tritium脏弹**: 附带辐射脉冲(范围=√燃烧Tr/0.5)

**科研实验**: 压缩机记录+多普勒阵列→两张软盘→科研点

**哑弹排查**:
| 症状 | 原因 |
|---|---|
| 开阀压力不升 | 气体不够/Hypernob抑制 |
| 漏气不爆炸 | 压力>3,040但<4,053 |
| 威力小 | 配比不对/气体不足 |
| 完全没反应 | 残留Freon/温度不够 |
| 触发没反应 | 频率不对/电池没电 |

---

## 第10章 · 科研实验系统

**代码**: `ordnance/tank_compressor.dm` (343行), `ordnance/doppler_array.dm` (307行), `ordnance/_scipaper.dm` (321行)

### 10.1 气罐压缩机

**代码**: `tank_compressor.dm`

| 参数 | 值 |
|---|---|
| 压力上限 | 5,000 kPa |
| 最大流速 | 20 L/s |
| 泄露检测 | >10mol→记录数据 |
| 内置防爆 | 吸收爆炸(COMSIG_CANCEL_EXPLOSION) |

**气罐碎裂时记录**: 破裂前压力 + 气体组成 + 时间戳

**实验匹配** (`apply_experiments`): 检查泄露气体是否符合科研实验需求→标记到软盘

### 10.2 多普勒阵列

**代码**: `doppler_array.dm`

| 参数 | 值 |
|---|---|
| 冷却 | 10秒 |
| 最大距离 | **150格** |
| 方向性 | 需指向爆炸点 |

**爆炸记录** (`tachyon_record`):
```dm
timestamp, coordinates, displacement
factual_radius[dev, heavy, light]
theory_radius[dev, heavy, light]
reaction_results[3元素]
explosion_identifier
```

### 10.3 完整工作流

```
① 清洗气罐→接洗气机抽真空→分析仪确认
② 压缩机接燃料气源→插入气罐+软盘→加压到3,500~4,800kPa
③ 重复处理氧化剂气罐
④ TTV组装: Valve+燃料罐+氧化罐+触发装置
⑤ 放多普勒阵列→指向测试场→插软盘
⑥ 开阀→触发→爆炸
⑦ 阵列记录数据→软盘
⑧ 取两张软盘→科研控制台→提交→科研点
```

---

# 附录

## A. 关键常量表

| 类别 | 常量 | 值 |
|---|---|---|
| 大气 | 标准摩尔体积 | 2,500 L/格 |
| 大气 | 标准压力 | 101.325 kPa |
| 大气 | 理想气体方程 R | 8.31 kPa·L/(K·mol) |
| 大气 | 室温 T20C | 293.15 K |
| 大气 | 绝对零度 | 2.7 K |
| 爆炸 | 泄漏压力 | 3,040 kPa |
| 爆炸 | 碎裂压力 | 3,546 kPa |
| 爆炸 | 爆炸压力 | 4,053 kPa |
| 爆炸 | 缩放系数 | 8,511 kPa |
| 爆炸 | 爆炸触发下限 | 5,000 kPa |
| 爆炸 | 最大爆炸力(volatile) | 9级/100,000kPa |
| SM | 能量惩罚阈值 | 5,000 |
| SM | 摩尔惩罚阈值 | 1,800 mol |
| SM | 基础温度 | 313 K |
| SM | 解体点 | 100 |
| SM | 倒计时 | 30秒(碎偷5秒) |
| SM | 最大闪电数 | 4 |
| HFR | 聚变下限 | 25 mol |
| HFR | 熔毁点 | 900 |
| HFR | 倒计时 | 30秒 |
| HFR | 极限温度 | 1e8 K |
| HFR | 亚临界阈值 | 1,200 mol |
| HFR | 超临界阈值 | 10,000 mol |
| HFR | 铁安全阈值 | 35% |
| HFR | 漏气/中压/高压 | 10,000/12,000 kPa |
| 奇点 | S1/S2/S3/S4/S5/S6能量 | 1/200/500/1,000/2,000/3,000 |
| 放电 | 固定气罐 | 46,000 kPa |
| 放电 | 便携气罐 | 500,000 kPa / 10,000K |

## B. 代码路径索引

| 系统 | 路径 | 行数 |
|---|---|---|
| 气体定义 | `code/modules/atmospherics/gasmixtures/gas_types.dm` | 333 |
| 气体反应 | `code/modules/atmospherics/gasmixtures/reactions.dm` | 1,240 |
| 气体混合 | `code/modules/atmospherics/gasmixtures/gas_mixture.dm` | 815 |
| 管道/组件 | `code/modules/atmospherics/machinery/` | ~8,000 |
| 气罐爆炸 | `code/game/objects/items/tanks/tanks.dm` | ~400 |
| 超物质 | `code/modules/power/supermatter/` | 1,814 |
| 解体策略 | `code/modules/power/supermatter/supermatter_delamination/` | 5文件 |
| HFR | `code/modules/atmospherics/machinery/components/fusion/` | 2,265 |
| 奇点 | `code/modules/power/singularity/` | 2,315 |
| 军火 | `code/modules/research/ordnance/` | 1,048 |
| 结晶器 | `code/modules/atmospherics/machinery/components/gas_recipe_machines/` | 3文件 |
| 电解器 | `code/modules/atmospherics/machinery/components/electrolyzer/` | 2文件 |
| TTV | `code/game/objects/items/devices/transfer_valve.dm` | 435 |
| 大气常量 | `code/__DEFINES/atmospherics/` | 5文件 |
| SM常量 | `code/__DEFINES/supermatter.dm` | 197 |
| 重力(奇点) | `code/__DEFINES/gravity.dm` | 58 |
| 反应常量 | `code/__DEFINES/reactions.dm` | 274 |
| TEG温差发电 | `code/modules/power/thermoelectric_generator.dm` + `circulator.dm` | 398 |
| 燃气涡轮 | `code/modules/power/turbine/` (3文件) | 960 |
| 太阳能 | `code/modules/power/solar.dm` | 647 |
| RTG | `code/modules/power/rtg.dm` | 174 |
| 引力发生器 | `code/modules/power/gravitygenerator.dm` | 533 |
| 特斯拉 | `code/modules/power/tesla/` (2文件) | 548 |
| 反应手册 | `code/modules/atmospherics/gasmixtures/reaction_factors.dm` | 216 |
| 大气连接器 | `code/modules/atmospherics/machinery/components/unary_devices/machine_connector.dm` | 146 |
| 跨层管道 | `code/modules/atmospherics/machinery/pipes/multiz.dm` | 60 |
| 电力网核心 | `code/modules/power/power.dm` + `powernet.dm` | 667 |
| 电缆 | `code/modules/power/cable.dm` | 876 |
| APC | `code/modules/power/apc/apc_main.dm` | 835 |
| SMES | `code/modules/power/smes.dm` | 560 |
| Nova扩展 | `modular_nova/` 对应路径 | ~若干 |

## C. 勘误记录

本文档基于天关源码逐行提取，修正了此前版本中的以下错误:

| 错误 | 位置 | 正确值 |
|---|---|---|
| 周期时间10秒 | 水培文档 | **20秒** (HYDROTRAY_CYCLE_DELAY) |
| 产物容量50u | 水培文档 | **100u** (PLANT_REAGENT_VOLUME) |
| SOIL_LOVER惩罚0.5 | 水培文档 | **0.7** (少30%不是50%) |
| SM temp_limit 303K基础 | 早期SM分析 | **313.15K**, gas_heat_resistance×313.15 |
| SM气体效果加法 | 早期SM分析 | **百分比加权** (gas_percentage) |
| HFR power_level由能量决定 | 早期HFR分析 | **由融合腔温度决定** |

## D. 散篇文档对照表

本总章由以下13篇专题文档合并而来:

| # | 原文件 | 纳入章节 |
|---|---|---|
| 01 | 20种气体完全解析 | §1 |
| 02 | 气体反应全链路解析 | §2 |
| 03 | 管道机械深度解析 | §3 |
| 03b | 压力安全系统 | §4 |
| 04 | 超物质引擎完全解析 | §5 |
| 04b | 超物质引擎深度解析 | §5 |
| 04c | 超物质查漏补缺 | §5 |
| 05 | HFR聚变反应堆完全百科 | §6 |
| 06 | 实验军火系统 | §9-10 |
| 07 | 结晶器与电解器配方 | §8 |
| 08 | 奇点引擎完全解析 | §7 |
| — | TTV炸弹制造指南 | §9 |
| — | 全量解析索引 | 附录 |

> **文档完** — 基于 TianGuan13 源码的全量工程气体系统分析
> 代码覆盖: 90+文件, ~31,000+行（含发电系统+电力网补充） | 输出: 13篇→1篇总章 + 发电系统卷
> **2026-08 核查补充**: 新增第四卷B发电系统（TEG/涡轮/太阳能/RTG/引力/特斯拉 7,300行）+ 第8I章电力网基础设施（APC/SMES/电缆/电网 3,000行）+ reaction_factors/machine_connector/multiz 补齐
