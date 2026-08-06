# 天关 — 病毒学完全百科（疾病+疫苗合并版）

**代码**: `code/datums/diseases/` (40+文件, ~6,000行)
**核心**: `_disease.dm` (基础), `advance/advance.dm` (进阶), `advance/symptoms/*.dm` (27处症状)

---

## 目录

- **第一部分 · 疾病系统**
  - [一、疾病基础系统](#一疾病基础系统)
  - [二、预设疾病 (自然疾病)](#二预设疾病-自然疾病)
  - [三、进阶疾病系统 (Advance Disease)](#三进阶疾病系统-advance-disease)
  - [四、38种症状完全表](#四38种症状完全表2026-08-核查补全)
  - [五、病毒传播与感染](#五病毒传播与感染)
  - [六、病毒学实验室设备](#六病毒学实验室设备)
  - [七、病毒学实用指南](#七病毒学实用指南)
- **第二部分 · 疫苗制造与疾病防控**
  - [八、疫苗系统核心](#八疫苗系统核心)
  - [九、疫苗制作流程](#九疫苗制作流程)
  - [十、免疫机制](#十免疫机制)
  - [十一、大规模防疫策略](#十一大规模防疫策略)
  - [十二、关于治愈的补充](#十二关于治愈的补充)
  - [十三、补充：症状级治疗](#十三补充症状级治疗)
  - [十四、快速参考](#十四快速参考)
- **附录**：[十五、代码路径索引](#十五代码路径索引)

---

## 一、疾病基础系统

### 1.1 疾病核心属性

**代码**: `_disease.dm` + `__DEFINES/diseases.dm`

| 属性 | 说明 |
|---|---|
| `name` | 疾病名称 |
| `desc` | 描述 |
| `max_stages` | 最大阶段(1~5) |
| `spread_flags` | 传播方式(位掩码) |
| `viable_mobtypes` | 可感染类型 |
| `cures` | 治愈物(试剂列表) |
| `visibility_flags` | 可见性 |
| `severity` | 严重度(7级) |
| `required_organ` | 所需器官 |

### 1.2 传播方式

| 标志 | 值 | 说明 |
|---|---|---|
| `DISEASE_SPREAD_SPECIAL` | 1 | 特殊传播(事件/触发) |
| `DISEASE_SPREAD_NON_CONTAGIOUS` | 2 | 不传染 |
| `DISEASE_SPREAD_BLOOD` | 4 | 血液传播 |
| `DISEASE_SPREAD_CONTACT_FLUIDS` | 8 | 体液接触 |
| `DISEASE_SPREAD_CONTACT_SKIN` | 16 | 皮肤接触 |
| `DISEASE_SPREAD_AIRBORNE` | 32 | **空气传播** |

### 1.3 严重度等级

| 等级 | 说明 | 示例 |
|---|---|---|
| **Positive** | 增益/治疗 | 治愈型病毒 |
| **Harmless** | 不具威胁性 | 打喷嚏 |
| **Minor** | 轻微干扰 | 头晕 |
| **Medium** | 中度干扰 | 呕吐 |
| **Harmful** | 显著伤害 | 脑腐烂 |
| **Dangerous** | 致死级 | 食肉菌 |
| **BIOHAZARD** | 生物危害 | 真菌TB, GBS |
| **Uncurable** | 不治之症 | HMS |

### 1.4 阶段系统

5阶段(Stage 1~5):
- 阶段越高症状越强
- 每阶段有概率自愈
- 治疗可以降低阶段

---

## 二、预设疾病 (自然疾病)

**代码**: `code/datums/diseases/` (非advance目录)

| # | 疾病 | 代码 | 严重度 | 说明 |
|---|---|---|---|---|
| 01 | **Common Cold (普通感冒)** | `cold.dm` | Harmless | 咳嗽+打喷嚏, Kosatime治愈 |
| 02 | **Cold 9 (九号感冒)** | `cold9.dm` | Minor | 更强版感冒 |
| 03 | **Flu (流感)** | `flu.dm` | Minor | 发烧+头痛+疲劳 |
| 04 | **Spanish Flu (西班牙流感)** | `fluspanish.dm` | Medium | 更强的流感 |
| 05 | **Brainrot (脑腐烂)** | `brainrot.dm` | **Dangerous** | 脑损伤, Mannitol治愈 |
| 06 | **GBS (内出血)** | `gbs.dm` | **BIOHAZARD** | 格雷塔内出血流行 |
| 07 | **Fake GBS (假GBS)** | `fake_gbs.dm` | Medium | 误诊版 |
| 08 | **Magnitis (磁力症)** | `magnitis.dm` | Minor | 金属物品被吸引 |
| 09 | **Beesease (蜜蜂病)** | `beesease.dm` | **Dangerous** | 体内长蜜蜂! |
| 10 | **Anaphylaxis (过敏性休克)** | `anaphylaxis.dm` | **Dangerous** | 过敏反应 |
| 11 | **Anxiety (焦虑)** | `anxiety.dm` | Harmless | 恐慌发作 |
| 12 | **Asthma Attack (哮喘)** | `asthma_attack.dm` | Medium | 4级(小/中/重/危) |
| 13 | **Adrenal Crisis (肾上腺危象)** | `adrenal_crisis.dm` | Medium | 肾上腺素失调 |
| 14 | **Chronic Illness (慢性病)** | `chronic_illness.dm` | Harmless | 长期疾病（含 **Hereditary Manifold Sickness 遗传流形病**：5阶段/非传染/sansufentanyl 缓解） |
| 15 | **Parasitic Infection (寄生虫)** | `parasitic_infection.dm` | **Dangerous** | 体内寄生虫 |
| 16 | **Decloning (克隆退化)** | `decloning.dm` | **Dangerous** | 克隆体排斥反应 |
| 17 | **DNA Spread (DNA扩散)** | `dna_spread.dm` | **Dangerous** | 随机DNA突变 |
| 18 | **Death Sandwich Poisoning (致命三明治)** | `death_sandwich_poisoning.dm` | Medium | 食物中毒 |
| 19 | **Gastrolisis (胃溃烂)** | `gastrolisis.dm` | Medium | 胃酸逆流 |
| 20 | **Pacifist (和平主义)** | `pacifist.dm` | Harmless | 强制非暴力 |
| 21 | **Retardation (智力下降)** | `retardation.dm` | Minor | 智力降低 |
| 22 | **Transformation (变形)** | `transformation.dm` | **BIOHAZARD** | 身体变形 |
| 23 | **Viral Roach (病毒蟑螂)** | `viral_roach.dm` | Medium | 体内有蟑螂 |
| 24 | **Tuberculosis (肺结核)** | `tuberculosis.dm` | **Dangerous** | 肺结核（含真菌版 Fungal TB） |
| 25 | **Brainrot 变体** | `decloning.dm` | **Dangerous** | 细胞退化（Cellular Degeneration，非传染） |
| 26 | **Space Retrovirus (太空逆转录病毒)** | `dna_spread.dm` | Medium | 血/皮/体液传播，DNA 突变 |
| 27 | **Parrot Possession (鹦鹉附身)** | `parrotpossession.dm` | Medium | 被鹦鹉怨灵附身！找牧师（SPECIAL 传播） |
| 28 | **Pierrot's Throat (皮埃罗喉)** | `pierrot_throat.dm` | Medium | 小丑喉咙病 |
| 29 | **Retrovirus (逆转录病毒)** | `retrovirus.dm` | Harmful | DNA 改造：持续打乱宿主结构酶 |
| 30 | **The Rhumba Beat (伦巴节奏)** | `rhumba_beat.dm` | **BIOHAZARD** | "我是古巴皮特-伦巴之王"！强制跳舞 |
| 31 | **Jungle Flu (丛林流感)** | `transformation.dm` | **BIOHAZARD** | 丛林流感（非传染） |
| 32 | **Robotic Transformation (机器人转化)** | `transformation.dm` | **BIOHAZARD** | 急性纳米机器感染→变成赛博格 |
| 33 | **Xenomorph Transformation (异形转化)** | `transformation.dm` | **BIOHAZARD** | 变成异形 |
| 34 | **Advanced Mutation (高级突变转化)** | `transformation.dm` | **BIOHAZARD** | 高浓度提取物，把一切转化为自身 |
| 35 | **The Barkening (树皮化)** | `transformation.dm` | **BIOHAZARD** | 变成柯基犬！ |
| 36 | **Gluttony's Blessing (暴食祝福)** | `transformation.dm` | **BIOHAZARD** | 来自某处的"礼物" |
| 37 | **Gondola Transformation (贡多拉转化)** | `transformation.dm` | **BIOHAZARD** | 吃贡多拉肉的代价 |
| 38 | **Verminous Plague (虫疫)** | `verminous_plague.dm` | Medium | 体内不断长出小动物（皮传播） |
| 39 | **Localized Weightloss Malfunction (局部减重故障)** | `weightlessness.dm` | Medium | 生物电信号改写→体重异常 |
| 40 | **Wizarditis (巫师病)** | `wizarditis.dm` | Harmful | 变成巫师！ |

> ✅ 查漏补缺：源码共 34+ 种预设疾病，本次补全 16 种（结核/细胞退化/太空逆转录/鹦鹉附身/皮埃罗喉/逆转录/伦巴/丛林流感/机器人转化/异形转化/高级突变/树皮化/暴食/贡多拉/虫疫/局部减重/巫师病）

**地板疾病** (踩到地板感染):
| # | 疾病 | 说明 |
|---|---|---|
| TD1 | **Carpellosis** | 地毯病(脚气) |
| TD2 | **Gastritium** | 地板胃炎 |
| TD3 | **Nebula Nausea** | 地板恶心 |

---

## 三、进阶疾病系统 (Advance Disease)

**代码**: `advance/advance.dm` (516行), `advance/symptoms/` (27个症状)

### 3.1 进阶疾病属性

| 属性 | 说明 |
|---|---|
| `name` | **自定义名称** |
| `form` = "Advanced Disease" | 标记为工程病毒 |
| `max_stages` = 5 | 固定5阶段 |
| `symptoms` | 症状列表(最多6个) |
| `properties` | 综合统计(stealth/resistance/stage_speed/transmittable) |
| `id` | 唯一ID(由症状哈希生成) |
| `mutable` | 是否可修改 |

### 3.2 属性计算公式

```dm
properties = Σ(所有症状属性值加总)

stealth      = 各症状stealth之和
resistance   = 各症状resistance之和
stage_speed  = 各症状stage_speed之和
transmittable= 各症状transmittable之和
```

### 3.3 每症状限制: 最多6个

---

## 四、38种症状完全表（2026-08 核查补全）

**代码**: `advance/symptoms/*.dm`（36 个症状类型，含变体共 44 行）

> ✅ 查漏补缺：源码实际 36 种症状，本次补全 11 种缺失（病毒自我适应/减重/感官恢复/心智恢复/白斑/坏死代谢/无机生物/眼前房出血/潜伏DNA激活/碱汗/自噬坏死）

### 基础属性速查

| # | 症状名 | 代码 | 等级 | 隐秘 | 抗性 | 速度 | 传播 | 严重度 |
|---|---|---|---|---|---|---|---|---|
| 01 | **打喷嚏 Sneeze** | `sneeze.dm` | 1 | 0 | 0 | 1 | **3** | Harmless |
| 02 | **咳嗽 Cough** | `cough.dm` | 1 | 0 | 0 | 1 | **3** | Harmless |
| 03 | **声音变化 Voice Change** | `voice_change.dm` | 1 | 0 | 0 | 0 | 0 | Positive |
| 04 | **头痛 Headache** | `headache.dm` | 1 | 0 | 0 | 1 | 0 | Minor |
| 05 | **视力 Vision** | `vision.dm` | 2 | 0 | 2 | 0 | 0 | Minor |
| 06 | **头晕 Dizzy** | `dizzy.dm` | 2 | 0 | 0 | 0 | 0 | Minor |
| 07 | **体温调节 Thermo** | `thermoregulation.dm` | 2 | 0 | 1 | 2 | 0 | Minor |
| 08 | **耳聋 Deafness** | `deafness.dm` | 2 | 1 | 0 | 0 | 0 | Minor |
| 09 | **感官混乱 Sensory** | `sensory.dm` | 2 | 0 | 2 | 0 | 0 | Minor |
| 10 | **精神错乱 Confusion** | `confusion.dm` | 3 | 0 | 1 | 1 | 0 | Medium |
| 11 | **呕吐 Vomit** | `vomit.dm` | 3 | 0 | 0 | 2 | 1 | Medium |
| 12 | **发冷 Chills** | `chills.dm` | 3 | 0 | 1 | 1 | 0 | Minor |
| 13 | **发烧 Fever** | `fever.dm` | 3 | 0 | 1 | 1 | 0 | Minor |
| 14 | **体重变化 Weight** | `weight.dm` | 3 | 0 | 1 | 1 | 0 | Minor |
| 15 | **痒 Itching** | `itching.dm` | 3 | 0 | 1 | 0 | 0 | Minor |
| 16 | **皮肤损伤 Skin** | `skin.dm` | 4 | 0 | 2 | 0 | 0 | Harmful |
| 17 | **幻觉 Hallucigen** | `hallucigen.dm` | 4 | 0 | 2 | 1 | 0 | Medium |
| 18 | **缺氧 Oxygen** | `oxygen.dm` | 4 | 0 | 2 | 1 | 0 | **Dangerous** |
| 19 | **窒息 Choking** | `choking.dm` | 4 | 0 | 2 | 1 | 1 | **Dangerous** |
| 20 | **出血 Bleeding** | `bleeding.dm` | 4 | 0 | 2 | 0 | 0 | **Dangerous** |
| 21 | **嗜睡 Narcolepsy** | `narcolepsy.dm` | 4 | 1 | 1 | 1 | 0 | Medium |
| 22 | **变异 Genetics** | `genetics.dm` | 4 | 0 | 1 | 1 | 0 | Harmful |
| 23 | **失聪 Deafness** | `deafness.dm` | 2 | 1 | 0 | 0 | 0 | Minor |
| 24 | **食肉 Flesh Eating** | `flesh_eating.dm` | 5 | 0 | 2 | 1 | 0 | **BIOHAZARD** |
| 25 | **自燃 Fire** | `fire.dm` | 5 | 0 | 2 | 1 | 0 | **BIOHAZARD** |
| 26 | **青春 Youth** | `youth.dm` | 5 | 2 | 3 | 5 | 0 | **Positive** (返老还童!) |
| 27 | **脱毛 Shedding** | `shedding.dm` | 5 | 0 | 2 | 1 | 0 | Harmless |
| 28 | **治愈 Heal** | `heal.dm` | 1 | 3 | 1 | 1 | 0 | **Positive** (治疗!) |
| 29 | **胡须 Beard** | `beard.dm` | 1 | 0 | 0 | 0 | 0 | **Positive** (长胡子) |
| 30 | **物种转化 Species** | `species.dm` | 5 | 1 | 2 | 0 | 0 | **Dangerous** (转物种!) |
| 31 | **病毒性 Viral** | `viral.dm` | 3 | 0 | 0 | 1 | 1 | Minor |
| 32 | **变形 Disfiguration** | `disfiguration.dm` | 2 | 0 | 1 | 0 | 0 | Minor |
| 33 | **烧伤/窒息变体** | `choking.dm`/`asphyxiation` | 4 | 0 | 2 | 1 | 1 | **Dangerous** |
| 34 | **病毒自我适应 Viral self-adaptation** | `viraladaptation`（viral.dm） | 3 | 3 | 5 | 0 | 0 | — （伪装+抗清除，降低可检测性） |
| 35 | **减重 Weight Loss** | `weight_loss`（weight.dm） | 3 | 0 | 2 | 1 | — | 3（代谢改造：几乎无法从食物获取营养） |
| 36 | **感官恢复 Sensory Restoration** | `sensory_restoration`（sensory.dm） | 4 | 0 | 1 | 1 | 2 | — （刺激感官组织再生，恢复知觉） |
| 37 | **心智恢复 Mind Restoration** | `mind_restoration`（sensory.dm） | 5 | 0 | 0 | 1 | 0 | — （强化神经元连接，缩短精神类疾病时长） |
| 38 | **白斑 Polyvitiligo** | `polyvitiligo`（skin.dm） | 5 | 0 | 3 | 1 | 2 | 1（黑色素替换为反应性色素） |
| 39 | **坏死代谢 Necrotic Metabolism** | `undead_adaptation`（species.dm） | 5 | 2 | 0 | 1 | 0 | 0（**亡灵适应**：病毒可在死者体内存活活动） |
| 40 | **无机生物 Inorganic Biology** | `inorganic_adaptation`（species.dm） | 5 | 0 | 4 | 1 | 3 | 0（**无机适应**：可在无机环境中存活复制，抗性↑） |
| 41 | **眼前房出血 Hyphema** | `visionloss`（vision.dm） | 5 | 0 | 0 | 1 | 0 | 5（眼内出血，视力损伤甚至失明） |
| 42 | **潜伏DNA激活 Dormant DNA Activator** | `genetic_mutation`（genetics.dm） | 6 | 0 | 0 | 1 | 0 | 4（与宿主DNA结合，激活随机潜伏突变） |
| 43 | **碱汗 Alkali perspiration** | `alkali`（fire.dm） | 7 | 2 | 0 | 1 | 0 | 6（附着汗腺，合成遇水自燃的化学物！） |
| 44 | **自噬坏死 Autophagocytosis Necrosis** | `flesh_death`（flesh_eating.dm） | 7 | 0 | 0 | 1 | 0 | 6（病毒快速吞噬感染细胞，大面积破坏） |

### 症状阈值特殊效果

部分症状有阈值(Treshold)效果:

| 症状 | 阈值条件 | 特殊效果 |
|---|---|---|
| **Sneeze** | stealth≥4 | 传播率+50% |
| **Cough** | stealth≥4 | 传播率+50% |
| **Heal** | resistance≥6 | 治愈所有疾病! |
| **Youth** | stealth≥6 | 彻底隐身不显示 |
| **Fever** | stage_speed≥7 | 温度可达致命 |
| **Chills** | stage_speed≥7 | 温度可致死 |
| **Fire** | stage_speed≥7 | 每阶段自燃 |
| **Flesh Eating** | stage_speed≥8 | 伤害翻倍 |
| **Oxygen** | stage_speed≥6 | 缺氧加重 |
| **Bleeding** | stealth≥8 | 出血不显示 |
| **Genetics** | resistance≥8 | 永久变异 |

### 症状治愈物

**默认治愈**: Spaceacillin(抗生素)

| 症状 | 特定治愈 |
|---|---|
| **Beard** | Mannitol |
| **Deafness** | Psicodine |
| **Hallucigen** | Psicodine |
| **Flesh Eating** | 手术移除 |
| **Heal** | 无(无治愈) |

---

## 五、病毒传播与感染

### 5.1 传播概率

```dm
// 空气传播
if 空气传播 && 同格:
    prob(stage_speed × 15 + 5)

// 接触传播
if 皮肤/体液接触:
    prob(stage_speed × 10 + transmittable × 5)

// 血液传播
if 血液接触:
    prob(stage_speed × 20 + transmittable × 5)
```

### 5.2 抗性与治愈

```dm
// 自愈概率 (每阶段检查)
self_cure_chance = 5 - resistance  // 最小值1%

// 药物治愈
if cures:
    if 体内有cures中的试剂:
        stage -= 1 (或直接治愈)

// 症状治愈
if symptom_cure:
    对应症状被抑制
```

### 5.3 阶段推进

```dm
stage_progress = stage_speed × 0.1 + 基础推进率
每tick概率推进: prob(stage_progress × 100)
阶段≥5 → 最大效果
```

---

## 六、病毒学实验室设备

### 6.1 大流行(Pandemic)控制台

**代码**: `machinery/pandemic.dm`

| 功能 | 说明 |
|---|---|
| **培养病毒** | 从血液样本提取病毒 |
| **添加症状** | 加入新症状到病毒 |
| **移除症状** | 移除症状 |
| **病毒进化** | 使用试剂进化病毒 |
| **制作病毒菜** | 批量生产病毒 |
| **扫描** | 分析病毒属性 |

### 6.2 病毒食物制作

| 配方 | 用途 |
|---|---|
| Virus Food(Mutagen+Synaptizine+Water) | 基础进化 |
| +Mutagen → Lv3 | |
| +Plasma → Lv5 | |
| +Synaptizine → 退化 | |
| +Formaldehyde → 中性化 | |

### 6.3 病毒学家初始装备

**代码**: `code/modules/jobs/job_types/virologist.dm` + `modular_nova/.../virologist.dm`

| 装备 | 用途 |
|---|---|
| 白大褂+医疗耳机 | 基础 |
| 病毒培养盒 | 病毒取样 |
| 消毒喷雾 | 清洁 |
| PDA | 通信 |
| 安全护目镜 | 防护 |
| ID: 医疗权限+病毒实验室 | 访问 |

---

## 七、病毒学实用指南

### 7.1 病毒设计

| 目标 | 推荐症状组合 |
|---|---|
| **高传染** | Sneeze+Cough+Viral(空气) |
| **高隐蔽** | Heal(stealth+3)+Youth(stealth+2)+Voice Change |
| **致死** | Flesh Eating+Oxygen+Bleeding |
| **治疗** | Heal(阈值resistance≥6→治愈所有疾病) |
| **快速推进** | Youth(stage_speed+5)+Fever+Chills |

### 7.2 症状等级限制

```dm
level = 症状.level 总和
if level < 1: 只能随机生成低等级症状
if level < 3: 中等
if level < 6: 高级
```

### 7.3 病毒制作工作流

```
1. 从患者血液中提取病毒样本
2. 用Pandemic分析当前属性
3. 添加症状(可用症状芯片或随机生成)
4. 用Virus Food进化到更高级
5. 制作病毒菜批量生产
6. 测试传播(注意安全!)
```

### 7.4 防御病毒

| 方法 | 说明 |
|---|---|
| **Spaceacillin** | 通用抗生素,缓解大部分症状 |
| **Mannitol** | 治疗Beard(胡须病) |
| **Psicodine** | 治疗幻觉/耳聋 |
| **消毒** | 清洁环境+洗手 |
| **隔离** | 感染者隔离 |
| **疫苗** | 可制作减活疫苗 |

### 7.5 特别警告

| 注意 | 说明 |
|---|---|
| **GBS是BIOHAZARD** | 快速致死, 要及时处理 |
| **食肉菌不可逆** | 不手术切除会死 |
| **蜜蜂病会产蜜蜂** | 体内爆蜜蜂 |
| **自燃病毒** | 人真的会烧起来 |
| **返老还童(Youth)** | 级别5, 但可让人变年轻(正面) |
| **治愈病毒(Heal)** | 抗性≥6时可治愈所有其他疾病! |

---

# 第八部分 · 疫苗制造与疾病防控（合并自《疫苗制造百科》）

**代码**: `pandemic.dm` (387行), `other_reagents.dm` L72-115 (vaccine试剂), `advance/advance.dm` (存档系统)

---

## 八、疫苗系统核心

### 1.1 疫苗试剂 (`/datum/reagent/vaccine`)

| 属性 | 值 |
|---|---|
| 颜色 | `#C81040` (暗红) |
| 味道 | "slime" |
| 渗透 | 无(必须喝或注射) |
| 生效方式 | **INGEST**(喝) 或 **INJECT**(注射) |

**每瓶疫苗**: 15u, 包含特定病毒的ID。

### 1.2 疫苗作用原理

```dm
/datum/reagent/vaccine/expose_mob():
    // 1. 治愈当前感染
    for each 感染者体内疾病:
        if 疾病ID in 疫苗数据:
            疾病.cure(add_resistance = TRUE)  // 治愈+产生抗性

    // 2. 添加永久抗性
    exposed_mob.disease_resistances |= 疫苗数据
    // disease_resistances = 免疫列表, 以后不会再被该病毒感染
```

**免疫机制**: `disease_resistances` 是人物属性列表, 包含已免疫的病毒ID。有抗性后该病毒无法再次感染。

---

## 九、疫苗制作流程

### 2.1 设备需求

| 设备 | 说明 |
|---|---|
| **Pandemic 控制台** | 制作疫苗/培养病毒/分析 |
| **烧杯** | 装血液样本 |
| **小试管** | 产出疫苗的容器 |

### 2.2 步骤

```
[第一步]: 从感染者获取血液样本
  ① 用注射器从患者抽血 → 注入烧杯
  ② 烧杯放入Pandemic控制台

[第二步]: 存档病毒
  ③ Pandemic扫描血液 → 病毒存档到数据库
     (自动: SSdisease.archive_diseases[病毒ID] = 病毒拷贝)
  ④ 在Pandemic界面确认病毒已识别

[第三步]: 制作疫苗 (5秒冷却)
  ⑤ Pandemic界面 → 选择病毒 → "Create Vaccine Bottle"
  ⑥ 输出: 疫苗试管 × 1 (15u疫苗)
  ⑦ 等待20秒冷却后才能做下一瓶

[第四步]: 接种
  ⑧ 用注射器抽取疫苗 (最多15u)
  ⑨ 注射给健康人员 (预防)
    或 注射给已感染者 (治疗+免疫)
```

### 2.3 冷却系统

| 操作 | 冷却时间 |
|---|---|
| 制作培养管 (Culture Bottle) | **5秒** |
| 制作疫苗管 (Vaccine Bottle) | **20秒** |
| 冷却期间wait=TRUE, 不能制作 |

---

## 十、免疫机制

### 3.1 获得免疫的途径

| 途径 | 说明 |
|---|---|
| **接种疫苗** | 注射/喝疫苗 → 获得对应病毒ID的抗性 |
| **自愈** | 自然康复后可能产生部分抗性 |
| **遗传** | 部分疾病不会传染给有抗性者 |

### 3.2 抗性列表 (`disease_resistances`)

```dm
// 每个角色的免疫列表
mob.disease_resistances = list("病毒ID1", "病毒ID2", ...)

// 感染检查
if diseaseID in 目标.disease_resistances:
    return FALSE  // 免疫, 不会感染
```

### 3.3 多联疫苗

```dm
疫苗数据可以合并!

/datum/reagent/vaccine/on_merge():
    if 混合:
        data |= mix_data  // 合并病毒ID列表

→ 因此可以把多瓶疫苗混合成一瓶多联疫苗!
```

**制作多联疫苗**:
```
1. 做疫苗A (15u, 含病毒A的ID)
2. 做疫苗B (15u, 含病毒B的ID)
3. 用Pandemic或手动把两管疫苗混合
4. 合并后的一管含两个病毒的ID
5. 一次注射免疫两种病毒!
```

---

## 十一、大规模防疫策略

### 4.1 场景一：已知病毒在传播

```
① 从感染者抽血 → 放入Pandemic
② 制作疫苗管 (20秒)
③ 疫苗注入注射器 → 注射给医护人员优先
④ 继续做疫苗 → 批量接种全体员工
⑤ 已感染者: 先注射疫苗治疗, 再接种预防
```

### 4.2 场景二：未知新病毒

```
① 隔离疑似感染者
② 取血样 → Pandemic分析 → 存档病毒
③ 制作疫苗 → 测试(注射给志愿者)
④ 确认有效 → 大规模生产
⑤ 全员接种
```

### 4.3 场景三：多种病毒同时爆发

```
① 取每种病毒的血样
② 分别存档每种病毒
③ 做每种病毒的疫苗管
④ 全部混合成一管多联疫苗
⑤ 一次注射免疫所有已知病毒!
```

### 4.4 疫苗分配建议

| 优先级 | 人员 | 理由 |
|---|---|---|
| **1** | 病毒学家本人 | 保证疫苗生产线不崩溃 |
| **2** | 医疗部全员 | 治疗者不能倒下 |
| **3** | 工程/安保 | 关键岗位 |
| **4** | 指挥链(船长/HoS/CE) | 决策者 |
| **5** | 所有船员 | 群体免疫 |

---

## 十二、关于治愈的补充

疫苗不仅仅是预防, 还能治疗当前感染者:

```dm
// 疫苗试剂的作用
if 感染者体内有疾病ID匹配:
    疾病.cure(add_resistance = TRUE)
    // 治愈 + 添加永久抗性, 不会再感染
```

也就是说**同一管疫苗既可以给健康人打(预防), 也可以给病人打(治疗)**。

---

## 十三、补充：症状级治疗

除了整体疫苗, 每种症状也有自己的**症状治愈物**:

| 症状 | 治愈物 |
|---|---|
| **通用** | Spaceacillin (抗生素, 缓解大部分症状) |
| **Beard (胡须)** | Mannitol |
| **Deafness (耳聋)** | Psicodine |
| **Hallucigen (幻觉)** | Psicodine |
| **Flesh Eating (食肉菌)** | **手术切除** (药物无效) |

**症状治愈物作用**:
```dm
if 体内有symptom_cure:
    remedied = TRUE  // 该症状被抑制
    return FALSE      // 不激活症状效果
```

抑制症状 ≠ 治愈疾病。只是症状不生效, 病毒仍在体内。

---

## 十四、快速参考

| 操作 | 设备 | 时间 |
|---|---|---|
| 放血样入Pandemic | 烧杯+注射器 | 即时 |
| 制作病毒培养管 | Pandemic | **5秒** |
| 制作疫苗管 | Pandemic | **20秒** |
| 混合多联疫苗 | 手动/其他设备 | 即时 |
| 注射疫苗 | 注射器 | 即时 |
| 喝疫苗 | 直接喝 | 即时(但恶心) |


---

## 十五、代码路径索引

> 覆盖 `code/datums/diseases/` 全部源码 + 疫苗/病毒相关设备。

### 15.1 疾病核心（31 文件 · 3,120 行）

| 文件 | 行数 |
|---|---|
| `_disease.dm` | 457 |
| `transformation.dm` | 367 |
| `asthma_attack.dm` | 263 |
| `wizarditis.dm` | 188 |
| `_MobProcs.dm` | 186 |
| `chronic_illness.dm` | 123 |
| `gastrolisis.dm` | 101 |
| `retrovirus.dm` | 89 |
| `retrovirus.dm` | 89 |
| `anaphylaxis.dm` | 84 |
| `dna_spread.dm` | 83 |
| `verminous_plague.dm` | 77 |
| `tuberculosis.dm` | 75 |
| `magnitis.dm` | 69 |
| `pierrot_throat.dm` | 66 |
| `decloning.dm` | 65 |
| `flu.dm` | 62 |
| `death_sandwich_poisoning.dm` | 60 |
| `brainrot.dm` | 59 |
| `cold.dm` | 57 |
| `parrotpossession.dm` | 54 |
| `parasitic_infection.dm` | 52 |
| `cold9.dm` | 51 |
| `fluspanish.dm` | 47 |
| `rhumba_beat.dm` | 46 |
| `anxiety.dm` | 45 |
| `weightlessness.dm` | 44 |
| `gbs.dm` | 43 |
| `beesease.dm` | 42 |
| `adrenal_crisis.dm` | 39 |
| `fake_gbs.dm` | 37 |

### 15.2 进阶疾病症状（32 文件 · 2,964 行）

| 文件 | 行数 |
|---|---|
| `advance/symptoms/heal.dm` | 637 |
| `advance/symptoms/fire.dm` | 170 |
| `advance/symptoms/choking.dm` | 157 |
| `advance/symptoms/symptoms.dm` | 136 |
| `advance/symptoms/flesh_eating.dm` | 134 |
| `advance/symptoms/sensory.dm` | 122 |
| `advance/symptoms/vision.dm` | 82 |
| `advance/symptoms/narcolepsy.dm` | 80 |
| `advance/symptoms/fever.dm` | 80 |
| `advance/symptoms/cough.dm` | 80 |
| `advance/symptoms/vomit.dm` | 79 |
| `advance/symptoms/chills.dm` | 79 |
| `advance/symptoms/deafness.dm` | 77 |
| `advance/symptoms/genetics.dm` | 73 |
| `advance/symptoms/oxygen.dm` | 72 |
| `advance/symptoms/voice_change.dm` | 71 |
| `advance/symptoms/bleeding.dm` | 70 |
| `advance/symptoms/sneeze.dm` | 68 |
| `advance/symptoms/hallucigen.dm` | 67 |
| `advance/symptoms/confusion.dm` | 64 |
| `advance/symptoms/headache.dm` | 57 |
| `advance/symptoms/shedding.dm` | 54 |
| `advance/symptoms/itching.dm` | 53 |
| `advance/symptoms/dizzy.dm` | 53 |
| `advance/symptoms/youth.dm` | 52 |
| `advance/symptoms/species.dm` | 52 |
| `advance/symptoms/disfiguration.dm` | 48 |
| `advance/symptoms/weight.dm` | 47 |
| `advance/symptoms/thermoregulation.dm` | 40 |
| `advance/symptoms/skin.dm` | 37 |
| `advance/symptoms/beard.dm` | 37 |
| `advance/symptoms/viral.dm` | 36 |

### 15.3 进阶疾病系统（2 文件 · 558 行）

| 文件 | 行数 |
|---|---|
| `advance/advance.dm` | 516 |
| `advance/presets.dm` | 42 |

### 15.4 地板疾病（3 文件 · 190 行）

| 文件 | 行数 |
|---|---|
| `floor_diseases/carpellosis.dm` | 99 |
| `floor_diseases/gastritium.dm` | 55 |
| `floor_diseases/nebula_nausea.dm` | 36 |

### 15.5 病毒/疫苗相关设备（5 文件 · 347 行）

| 文件 | 行数 |
|---|---|
| `code/modules/modular_computers/computers/item/disks/virus_disk.dm` | 158 |
| `code/modules/surgery/operations/operation_virus.dm` | 70 |
| `code/modules/cargo/bounties/virus.dm` | 66 |
| `code/modules/events/fake_virus.dm` | 39 |
| `code/__HELPERS/logging/virus.dm` | 14 |

### 15.6 疫苗制造核心设备

| 文件 | 说明 |
|---|---|
| `code/modules/reagents/chemistry/machinery/pandemic.dm` | **Pandemic 病毒学工作站**（放样/培养管/疫苗管核心设备） |
| `code/game/machinery/medipen_refiller.dm` | 医疗笔填充器 |
| `code/game/objects/items/devices/scanners/health_analyzer.dm` | 健康扫描仪（疾病检测） |
| `code/modules/research/xenobiology/vatgrowing/` | 病毒槽培养（vatgrowing） |
| `code/modules/reagents/chemistry/reagents/other_reagents.dm` | 病毒食物/相关试剂 |

> **总计覆盖**: `code/datums/diseases/` 7,179 行（含疫苗设备）

