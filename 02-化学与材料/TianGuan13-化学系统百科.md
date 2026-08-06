# 天关 — 化学系统百科（整合总章）

> **项目**: TianGuan13 (Nova Sector → /tg/station)
> **代码根**: `code/modules/reagents/chemistry/`（recipes 4,383行 + reagents 11,515行）+ `modular_nova/` 扩展
> **整合自**: 化学01-医疗配方百科 / 化学02-爆炸与毒素百科 / 化学03-其他化学品百科 / 化学系统全量框架
> **本次核实**: 全配方文件逐条对照源码，补齐缺失配方 40+，修正错误 3 处
> **可合成化学品总量**: ~300+ 种（含 Nova 扩展）

---

## 目录

- [第一卷 · 系统架构](#第一卷--系统架构)
- [第二卷 · 医疗配方全表](#第二卷--医疗配方全表)
- [第三卷 · 急救策略速查](#第三卷--急救策略速查)
- [第四卷 · 爆炸品全表](#第四卷--爆炸品全表)
- [第五卷 · 毒素全表](#第五卷--毒素全表)
- [第六卷 · 毒品全表](#第六卷--毒品全表)
- [第七卷 · 基础原料链](#第七卷--基础原料链)
- [第八卷 · 其他化学品](#第八卷--其他化学品)
- [第九卷 · 病毒培养系统](#第九卷--病毒培养系统)
- [第十卷 · 贫民窟化学（Ghetto Chemistry）](#第十卷--贫民窟化学ghetto-chemistry)
- [第十一卷 · 反应条件与杂质系统](#第十一卷--反应条件与杂质系统)
- [附录A · 代码路径索引](#附录a--代码路径索引)
- [附录B · 化学品数量统计](#附录b--化学品数量统计)

---

# 第一卷 · 系统架构

## 1.1 试剂基础 (`/datum/reagent`)

| 字段 | 说明 |
|---|---|
| `name` / `description` | 名称/描述 |
| `color` | 颜色 |
| `specific_heat` | 比热 J/(K·mol) |
| `metabolization_rate` | 代谢速率（默认 0.4u/s；医疗 C2 多为 0.5） |
| `overdose_threshold` | 过剂量阈值（0=不过量） |
| `pH` | 酸碱度 |
| `purity` / `creation_purity` | 纯度 |
| `burning_temperature` | 燃烧点（null=不燃） |
| `addiction_types` | 成瘾类型+阈值 |
| `penetrates_skin` | 皮肤渗透方式（TOUCH/VAPOR/INGEST） |
| `metabolized_traits` | 代谢期间附加特质（如 TRAIT_ANALGESIA 止痛） |

## 1.2 反应系统 (`/datum/chemical_reaction`)

| 字段 | 说明 |
|---|---|
| `results` | 产物: list(试剂=产量) |
| `required_reagents` | 原料: list(试剂=需要量) |
| `required_catalysts` | 催化剂: list(试剂=最低量, 不消耗) |
| `required_temp` / `optimal_temp` / `overheat_temp` | 温度要求 |
| `optimal_ph_min` / `optimal_ph_max` | pH 范围 |
| `purity_min` | 最低纯度 |
| `rate_up_lim` | 最大反应速率 |
| `thermic_constant` | 放热(+) / 吸热(-) 常数 |
| `H_ion_release` | 每步 pH 释放量 |
| `reaction_flags` | REACTION_INSTANT(即时) / REACTION_COMPETITIVE(竞争) / REACTION_PH_VOL_CONSTANT 等 |
| `reaction_tags` | 分类标签（EASY/MODERATE/HEALING/EXPLOSIVE/DAMAGING...） |
| `mix_message` | 混合时提示文本 |
| `overheated()` | 过热时效果（爆炸/毒气） |
| `overly_impure()` | 过杂时效果 |

**反应引擎**：FermiChem 平衡系统（`holder/reactions.dm` + `equilibrium.dm`），每 tick 计算反应速率（受温度/pH/纯度影响），产物逐步生成。

## 1.3 设备

| 设备 | 代码 | 功能 |
|---|---|---|
| **化学配药机** | `chem_dispenser.dm` | 自动输出基础化学物 |
| **化学加热器** | `chem_heater.dm` | 控制反应温度 |
| **化学质谱仪** | `chem_mass_spec.dm` | 分析成分 |
| **化学合成仪** | `chem_synthesizer.dm` | 精准合成 |
| **化学分离器** | `chem_separator.dm` | 分离混合液 |
| **化学大师** | `chem_master.dm` | 批量分装+制作药片/针剂 |
| **大流行** | `pandemic.dm` | 病毒培养（见第九卷） |
| **手持混药器** | `portable_chem_mixer.dm` | 随身混药 |
| **研磨机** | `reagentgrinder.dm` | 植物/矿石→试剂 |
| **烟雾机** | `smoke_machine.dm` | 喷洒化学烟雾 |

---

# 第二卷 · 医疗配方全表

**代码**: `recipes/medicine.dm` (447行) + `recipes/cat2_medicines.dm` (357行)
**试剂**: `reagents/medicine_reagents.dm` (2,010行) + `reagents/cat2_medicine_reagents.dm` (676行)

基础医疗(白药) ~30种 + C2进阶医疗(蓝药) 12种 + 医疗堆叠物 3种。C2 = Category 2，比基础药更高效但配方更复杂。

## 2.1 基础医疗配方表

| # | 药名 | 每份配方 | 产量 | 功能 |
|---|---|---|---|---|
| 01 | **Leporazine** | Si1 + Cu1（催化 Plasma5） | 2u | 体温稳定至37°C |
| 02 | **Rezadone** | Carpotoxin1 + Cryptobiolin1 + Cu1 | 3u | 修复烧伤+消除畸形 |
| 03 | **Spaceacillin** | Cryptobiolin1 + Epinephrine1 | 2u | 抗生素 |
| 04 | **Oculine** | Multiver1 + C1 + H1 | 3u | 治疗眼伤（最佳200~400K） |
| 05 | **Inacusiate** | Water1 + C1 + Multiver1 | 2u | 治疗耳聋 |
| 06 | **Synaptizine** | Sugar1 + Li1 + Water1 | 3u | 解毒+清醒 |
| 07 | **Salglu Solution** | Salt Water2 + Sugar1 | 3u | 生理盐水（补血+补水） |
| 08 | **Mine Salve** | Oil1 + Water1 + Fe1 | 3u | 矿工药（缓减碾压伤） |
| 09 | **Mine Salve2** | Plasma5 + Fe5 + Sugar1 | **15u** | 矿工药加强版 |
| 10 | **Synthflesh** | Blood1 + C1 + Libital1 | 3u | 合成肉体（修复烧伤+外伤） |
| 11 | **Calomel** | Hg1 + Cl1 | 2u | 强力排毒（排所有外物） |
| 12 | **Ammoniated Mercury** | Calomel1 + Ammonia2 | 3u | 汞化氨 |
| 13 | **Potass Iodide** | K1 + I1 | 2u | 碘化钾（防辐射吸入） |
| 14 | **Pen Acid** | Fuel1 + NH₃1 + Formaldehyde1 + Salt1 + Cyanide1 | 5u | 排毒 |
| 15 | **Sal Acid** | Na1 + Phenol1 + C1 + O1 + H₂SO₄1 | 5u | 排毒 |
| 16 | **Oxandrolone** | C3 + Phenol1 + H1 + O1 | 6u | 促进肌肉恢复 |
| 17 | **Salbutamol** | Sal Acid1 + Li1 + Al1 + Br1 + NH₃1 | 5u | 治疗缺氧 |
| 18 | **Albuterol** | Li3 + Al3 + Br3 + Convermol1 | **15u** | 高效治缺氧（400K起，最佳600K） |
| 19 | **Convermol（经Albuterol）** | Albuterol5（催化 Flu Acid1, 900K） | 1u | 心脏+呼吸+意识恢复 |
| 20 | **Ephedrine** | Sugar1 + Oil1 + H1 + Diethylamine1 | 4u | 兴奋剂（+移动速度） |
| 21 | **Diphenhydramine** | Oil1 + C1 + Br1 + Diethylamine1 + Ethanol1 | 4u | 抗组胺（抗过敏） |
| 22 | **Atropine** | Ethanol1 + Acetone1 + Diethylamine1 + Phenol1 + H₂SO₄1 | 5u | **重伤急救**: 推血+推氧+防心跳停 |
| 23 | **Epinephrine** | Phenol1 + Acetone1 + Diethylamine1 + O1 + Cl1 + H1 | 6u | **通用急救**: 防致死+摇醒 |
| 24 | **Strange Reagent** | Omnizine1 + Holy Water1 + Mutagen1 | 3u | **复活!** 10u+心脏停时复活死人 |
| 25 | **Strange/Alt** | Protozine1 + Holy Water1 + Mutagen1 | 2u | 同上（替代配方） |
| 26 | **Fishy Reagent** | Omnizine1 + Salt Water1 + Carpotoxin1 | 3u | 鱼用复活 |
| 27 | **Mannitol** | Sugar1 + H1 + Water1 | 3u | 修复脑损伤 |
| 28 | **Neurine** | Mannitol1 + Acetone1 + O1 | 3u | 修复神经系统 |
| 29 | **Mutadone** | Mutagen1 + Acetone1 + Br1 | 3u | 治愈基因突变 |
| 30 | **Antihol** | Ethanol1 + Multiver1 + Cu1 | 3u | 解酒 |
| 31 | **Cryoxadone** | Stable Plasma1 + Acetone1 + Mutagen1 | 3u | **低温治疗**: 仅<170K有效, 全伤治疗 |
| 32 | **Pyroxadone** | Cryoxadone1 + Slime Jelly1 | 2u | **高温治疗**: 仅>370K有效 |
| 33 | **Haloperidol** | Cl1 + F1 + Al1 + Potass Iodide1 + Oil1 | 5u | 抗精神病+降压+止幻觉 |
| 34 | **Regen Jelly** | Omnizine1 + Slime Jelly1 | 2u | 再生凝胶 |
| 35 | **Higherdite** | Phenol2 + Li1 | 3u | 促进骨折愈合 |
| 36 | **Morphine** | C2 + H2 + Ethanol1 + O1 | 2u | 强力止痛+镇静 |
| 37 | **Modafinil** | Diethylamine1 + NH₃1 + Phenol1 + Acetone1 + H₂SO₄1 | 5u | 抗疲劳+抗睡眠 |
| 38 | **Psicodine** | Mannitol2 + Water2 + Impedrezene1 | 5u | 抗幻觉+精神安定 |
| 39 | **Granibitaluri** | Salt1 + C1 + Acid1（催化 Fe5） | 3u | 温和止痛+治小伤 |
| 40 | **Ondansetron** | Oil1 + N1 + O1（催化 Ethanol3, pH≤11） | 3u | 止吐 |
| 41 | **Naloxone** | Morphine1 + H₂O₂1 + Br1 + Ethanol1 | 4u | 解药过量（清阿片类） |

> ✅ 本次补齐: 38-41 号原"待查"配方 + 19号 Convermol 真实配方（Albuterol5 + FluAcid催化, 900K）

## 2.2 C2 进阶医疗配方

| # | 药名 | 配方 | 产量 | 功能 |
|---|---|---|---|---|
| C01 | **Helbital** | Sugar1 + F1 + C1 | 3u | 治疗钝器/挫伤 |
| C02 | **Libital** | Phenol1 + O1 + N1 | 3u | 治疗切割伤 |
| C03 | **Probital** | Cu1 + Acetone2 + P1 | 4u | 治疗镭射/能量伤 |
| C04 | **Lenturi** | NH₃1 + Ag1 + S1 + O1 + Cl1 | 5u | 延长药物效果 |
| C05 | **Aiuri** | NH₃1 + H₂SO₄1 + H2 | 4u | 治疗毒素伤害+推氧 |
| C06 | **Hercuri** | Cryostylane3 + Br1 + Lye1 | 5u | 治疗烧伤+清毒 |
| C07 | **Convermol** | H1 + F1 + Oil1（370K起） | 3u | 心脏+呼吸+意识恢复 |
| C08 | **Tirimol** | N3 + Acetone2 | 5u | 治疗呼吸损伤 |
| C09 | **Seiver** | N1 + K1 + Al1 | 3u | 紧急合成代谢（加力量） |
| C10 | **Multiver** | Ash1 + Salt1 | 2u | 广谱排毒（万能解） |
| C11 | **Syriniver** | S1 + F1 + Toxin1 + N₂O2 | 5u | 渗透性净化 |
| C12 | **Penthrite** | Pentaerythritol1 + Acetone1 + Nitracid1 + Wittel1 | 3u | 治疗出血 |

**C2 反应特性**：多数 C2 配方含 `overheated()`（过热→爆炸/冲击波）和 `overly_impure()`（过杂→同样炸）。Convermol 有氧时反应快10倍、过热爆炸威力大；无氧时反应慢10倍、过热只小爆炸（damage 2 vs 5）。

## 2.3 Albuterol ↔ Convermol 转换链（完整）

```
Salbutamol ──(+Convermol1, 催化Acid, 500K)──▶ Albuterol(4u) + SalAcid0.5 + NH₃0.5
Albuterol ──(+O1, 催化Acid, 300K)──▶ Salbutamol(2u) + NH₃1
Albuterol ──(催化FluAcid, 900K)──▶ Convermol(1u) + Li3 + Al3 + Br3  [过热→爆炸烟]
```

直接制作 Albuterol: Li3 + Al3 + Br3 + Convermol1（400K，最佳600K，过热900K）→ 15u

## 2.4 医疗堆叠物（Medical Stacks）

**代码**: `medicine.dm` L408-430，全部 `REACTION_INSTANT` 即时生成物品：

| 物品 | 配方 | 产出 | 效果 |
|---|---|---|---|
| **Medsuture 缝合线** | Cellulose2 + Formaldehyde4 + Polypyr3 | vol×4 个 | 医用缝合线（治出血） |
| **Medmesh 高级敷料** | Cellulose2 + Aloe Juice4 + Sterilizine2 | vol×3 个 | 高级医用网（治烧伤） |
| **Poultice 药膏** | Bungotoxin4 + Cellulose4 + Aloe Juice4 | vol×3 个 | 药膏（治外伤+烧伤） |

## 2.5 Nova 扩展医疗配方（modular_nova）

**代码**: `modular_nova/modules/customization/modules/reagents/chemistry/recipes/medicine.dm`

| 名称 | 配方 | 产量 | 效果 |
|---|---|---|---|
| **System Cleaner 系统清洁剂** | Ethanol1 + Cl1 + Phenol2 + K1 | 4u | 中和合成体(赛博格)体内有害化合物 |
| **Liquid Solder 液态焊料** | Ethanol1 + Cu1 + Ag1（370K） | 3u | 修复合成体脑损伤 |
| **Nanite Slurry 纳米浆** | Foaming Agent1 + Gold1 + Fe1 | 3u | 修复机械部件；过量过热/从口中喷出 |
| **Taste Suppressor 味觉抑制剂** | Na1 + S1 + Water1 | 3u | 钝化味觉（持续在体内期间） |
| **Taste Suppressor (maint)** | S1 + Salt Water1（300K） | 3u + Cl1 | 同上（维修通道廉价版） |
| **Dermagen 皮肤再生剂** | Ethanol4 + Synthflesh3 + Mine Salve3 | 5u | 外用去疤痕，最少 10u 才有效 |

**Nova 合成体专用试剂**（`modular_nova/modules/synths/code/reagents/reagents.dm`）：System Cleaner / Liquid Solder / Nanite Slurry 仅对合成体（synth）有效，对有机物无效或有害。

## 2.6 医疗试剂效果数值表（深挖）

**代码**: `reagents/medicine_reagents.dm` (2,010行) + `reagents/cat2_medicine_reagents.dm` (676行)

> 数值均为每代谢 tick 的效果（1 tick ≈ 2秒，实际乘以 metabolization_ratio × seconds_per_tick）。`REAGENTS_METABOLISM` = 0.4u/s 基准。

### 2.6.1 基础医疗试剂效果

| 试剂 | 代谢率 | 过量阈值 | 每tick效果 | 过量效果 |
|---|---|---|---|---|
| **Leporazine** | 默认 | — | 体温±20/tick 向正常体温回归（含核心温度） | — |
| **Synaptizine** | 默认 | — | 减困5s、解昏迷10、减幻觉10s；清 Mindbreaker 2.5u/tick | — |
| **Synaphydramine** | 默认 | — | 同 Synaptizine + 清 Histamine 2.5u/tick | — |
| **Sansufentanyl** | 默认 | — | 混淆+1.5s(上限5s)、眩晕+3s(上限12s)、耐力-0.5；10%概率铃声 | — |
| **Cryoxadone** | 动态 | — | 仅<273K：power = -0.00003×T²+3（昏迷×2）；氧-1.5×power、外伤/烧伤/毒-0.5×power；修伤口+去毁容 | — |
| **Pyroxadone** | 默认 | — | 仅>360K：power按温度 2/3/5（着火×2）；氧-1×power、外伤-0.5、烧伤-0.75、毒-0.5 | — |
| **Rezadone** | 默认 | 30u | 外伤/烧伤-0.5、去毁容；接触≥5u 可解焦尸 | 毒+0.5、眩晕/抖动 |
| **Spaceacillin** | 0.1× | — | TRAIT_VIRUS_RESISTANCE（抗病毒） | — |
| **Oxandrolone** | 0.5× | 25u | 烧伤>25：-4×纯度；≤25：-0.5×纯度 | 烧伤+4.5 |
| **Salglu Solution** | 0.5× | 60u | 18%概率外伤/烧伤-0.5；补血+0.25/tick（铁血型） | 1.5%概率转盐/糖 |
| **Mine Salve** | 0.4× | — | 外伤/烧伤-0.3125；止痛+假健康HUD；烧伤伤口消毒+0.3/愈合+0.5 | — |
| **Omnizine** | 0.25× | 30u | 全伤-1/tick（毒/氧/外伤/烧伤各-0.5×2） | 全伤+3/tick |
| **Protozine** | 0.25× | 30u | Omnizine 弱版：全伤-0.4/tick | 同 Omnizine |
| **Calomel** | 默认 | 20u | 清所有试剂 1.5u/tick×purge；毒伤 = health/40（越健康越痛） | 自清1u+毒+1.25 |
| **Ammoniated Mercury** | 0.1× | 10u | 清毒素25u/tick×purge；毒伤=(外伤/15+烧伤/30-3)×5；无毒素时自清5u | 毒+15 |
| **Potass Iodide** | 2× | — | 辐照时毒-0.25；TRAIT_HALT_RADIATION_EFFECTS | — |
| **Pen Acid** | 0.5× | — | 毒-2；清所有其他试剂2u/tick×purge；防辐射 | — |
| **Sal Acid** | 0.5× | 25u | 外伤>25：-4×纯度；≤25：-0.5×纯度 | 外伤+4.5 |
| **Salbutamol** | 0.25× | — | 氧-6；losebreath-4 | — |
| **Albuterol** | 1× | 30u | 肺 received_pressure_mult+0.4（吸入时） | 25%抖动、35%呼吸问题；40周期后眼模糊+耐力伤 |
| **Ephedrine** | 0.5× | 30u | 移动速度+；解昏迷-20、耐力-4；TRAIT_BATON_RESISTANCE+STIMULATED；成瘾150 | 1%概率心脏病、18%毒+1+断气 |
| **Diphenhydramine** | 0.5× | — | 清Histamine 2u/tick；5%概率困 | — |
| **Morphine** | 0.5× | 30u | 止痛+免伤害减速；成瘾30；周期>16起逐步困→睡 | 18%丢物+眩晕抖动 |
| **Oculine** | 默认 | 30u | 眼伤-1×纯度；治愈近视/失明；视力+10×纯度 | 眼伤+0.75 |
| **Inacusiate** | 默认 | — | 耳伤-2、临时失聪-4；纯度≥1：TRAIT_GOOD_HEARING | — |
| **Atropine** | 0.25× | 35u | 临界时：毒/外伤/烧伤-4、氧-10；losebreath清零；TRAIT_NOCRITDAMAGE+防植入爆 | 毒+1+眩晕抖动 |
| **Epinephrine** | 0.25× | 30u | 临界时：全伤-1；losebreath-4、耐力-4；10%解昏迷；TRAIT_NOCRITDAMAGE | 18%耐力+5+毒+2+断气 |
| **Strange Reagent** | 1.25× | — | **复活**：5伤害/u、过量治疗80%惩罚；<200%最大血量可复活；体内反而随机伤0-2.5 | — |
| **Fishy Reagent** | 1.25× | — | 只复活鱼类/水生生物 | — |
| **Mannitol** | 默认 | 15u | 脑伤-1×纯度；TRAIT_TUMOR_SUPPRESSED | 随机提示文本 |
| **Neurine** | 默认 | — | 清Neurotoxin 2.5u/tick；8%×纯度治愈轻度创伤；死后脑伤-0.5 | — |
| **Mutadone** | 默认 | — | 清所有基因突变；移除猴子化 | — |
| **Antihol** | 默认 | — | 清乙醇4u/tick×纯度、醉酒-5；清混乱/眩晕/困/口齿不清 | — |
| **Stimulants** | 0.5× | 60u | 血量<50时全伤-1；解昏迷-60、耐力-12；TRAIT_BATON_RESISTANCE+ANALGESIA+STIMULATED | 18%耐力+2.5+毒+1+断气 |
| **Insulin** | 0.5× | — | 清Sugar 3u/tick | — |
| **Inaprovaline** | 默认 | — | losebreath≥5 时减2.5 | — |
| **Regen Jelly** | 默认 | — | 全伤-0.75/tick；对史莱姆无害 | — |
| **Syndicate Nanites** | 默认 | 30u | 外伤/烧伤-2.5、氧-7.5、毒-2.5、脑伤-7.5 | 13%呕吐纳米机器人 |
| **Earthsblood** | 默认 | 25u | 前10u：外伤/烧伤-0.5、氧/毒-0.25、耐力-1、脑伤+0.5；之后：外伤/烧伤-2.5、氧/毒-1.5、耐力-4、脑伤+1；幻觉+10s；TRAIT_PACIFISM | 幻觉+5s、毒+2；>26周期永久和平主义创伤 |
| **Haloperidol** | 0.4× | — | 清毒品2.5u/tick×purge；困+5s；减幻觉12.5s；10%脑伤+1；耐力+3.125 | — |
| **Changeling Adrenaline** | 默认 | 30u | 解昏迷-10、耐力-15；TRAIT_SLEEPIMMUNE+BATON_RESISTANCE | 毒+0.5 |
| **Changeling Haste** | 2.5× | — | 大幅加速；毒+4 | — |
| **Higadrite** | 默认 | — | TRAIT_STABLELIVER（保肝） | — |
| **Cordiolis Hepatico** | 默认 | — | TRAIT_STABLELIVER+STABLEHEART | — |
| **Muscle Stimulant** | 默认 | — | TRAIT_ANALGESIA+免伤害减速 | — |
| **Modafinil** | 0.1× | 20u随机±5 | TRAIT_SLEEPIMMUNE；解昏迷-25、耐力-15 | 阶段1抖动口齿不清→阶段2氧+2→82+睡200s+氧30 |
| **Psicodine** | 0.25× | 30u | 减抖动/眩晕24s、混淆12s、厌恶12；理智+10至中性；TRAIT_FEARLESS | 幻觉+20s+毒+2 |
| **Metafactor** | 0.0625× | 10u | 食物→肽转化（极慢代谢） | 13%呕吐 |
| **Silibinin** | 1.5× | — | 肝伤-0.67 | — |
| **Polypyr** | 0.25× | 50u | 肺伤-0.5、外伤-0.7；染紫发 | 肺伤+1 |
| **Granibitaluri** | 0.5× | 50u | 治疗 = max(1.5-0.01×(外伤+烧伤), 0.5) 外伤/烧伤；止痛 | 肝+0.2+毒+0.2 |
| **Coagulant (Sanguirite)** | 0.25× | 20u | **止血**：最重伤口血流量-0.15/tick；全出血×0.7被动；TRAIT_COAGULATING | 7.5%断气2-4+氧伤1-3+肺/心伤 |
| **Banana Peel** | 0.5× | — | 弱版凝血：血流量-0.1、全出血×0.8 | — |
| **Seraka Extract** | 0.2× | 10u | 强版凝血：血流量-0.2、全出血×0.5 | 同Coagulant过量 |
| **Ondansetron** | 0.5× | — | 止吐；8%困+2s、15%耐力-10；厌恶-10 | — |
| **Naloxone** | 0.5× | — | 清吗啡/Impedrezene/Fentanyl/Krokodil 3u/tick；减困5s；losebreath-1；TRAIT_ADDICTIONRESILIENT | — |
| **Adminordrazine** | 默认 | — | **管理员作弊药**：外伤/烧伤-2.5、毒-2.5、全愈（含器官/束缚）；TRAIT_ANALGESIA；死后仍生效（REAGENT_DEAD_PROCESS）；水培=全效恢复+随机变异 | — |
| **Quantum Medicine** | 默认 | — | Adminordrazine 变体：**从平行维度"换一具健康身体"**（量子愈） | — |

### 2.6.2 C2 试剂效果（深挖）

| 试剂 | 代谢率 | 过量阈值 | 每tick效果 | 过量效果 |
|---|---|---|---|---|
| **Helbital** | 0.5× | 35u | 清醒：治疗=max(总伤/20,3)+氧+2；软crit：治疗=总伤/13+氧+1；硬crit：治疗=总伤/10；0.005%概率与死神猜拳（输=灰飞烟灭，赢=满血复活+成就） | 枯萎+失明诅咒 |
| **Libital** | 0.5× | — | 外伤-3×纯度；**肝伤+0.3** | — |
| **Probital** | 0.5× | 20u | 外伤-3×纯度；耐力+1×(3.5→按耐力降为2)；摄入时5%转Metafactor | 耐力+3；≥80困、≥100睡10s |
| **Lenturi** | 0.5× | — | 烧伤-3.75×纯度；**胃伤+0.4** | — |
| **Aiuri** | 0.5× | — | 烧伤-2×纯度；**眼伤+0.25** | — |
| **Hercuri** | 0.5× | 25u | 烧伤>50：-3×纯度；≤50：-2.25×纯度；体温-5~-25、化学物温度-10、减火stack-1；蒸汽灭火 | 体温-10 |
| **Convermol** | 0.5× | 35u | 氧-2×oxycalc（oxycalc=2.5×(周期-1)，非OD时≤实际氧损）；毒+oxycalc×0.4；概率减losebreath | 代谢率+2.5× |
| **Tirimol** | 0.5× | — | 氧-4.5×纯度；耐力+2；每15s困+20s；结束>21周期睡10s | — |
| **Seiver** | 0.5× | — | 热：毒治疗=min(5+(temp-1000)/175, 5)（最高2.5/tick）；冷+辐照：毒-0.9×radcalc；**心伤=治疗点/5** | — |
| **Multiver** | 0.5× | — | 毒-0.5×min(医疗数,6×纯度)；肺伤+0.5；清其他试剂3u/tick×purge（≥3种独特医疗时不清理医疗）；纯度1：+2医疗数 | — |
| **Syriniver** | 0.75× | 6u | 毒-1.34；肝伤+0.54；清其他0.27/tick；注射转化Musiver | 肝+1+厌恶+2 |
| **Musiver** | 0.25× | 25u | 毒-3×纯度；肝伤+0.2；清其他0.4/tick×purge | 肝+3+肌肉无力创伤 |
| **Synthflesh** | — | — | 外用：外伤/烧伤-1.25×接触量；毒=治疗量×(0.5~0.75)；60u纯/100u低纯解焦尸 | — |
| **Penthrite** | 0.5× | 50u | 临界时：毒/外伤/烧伤-2、氧-6、losebreath清零、心伤+1×max(体积/10,1)；耐力-12.5；TRAIT_STABLEHEART+NOHARDCRIT+NOSOFTCRIT | 耐力+10+心伤+10+心脏病 |

**C2 设计哲学**（源码注释）：所有 C2 药无论剂量大小都有负面效果（肝/眼/胃/心伤），以阻止滥用——要么是应急药，要么是"转化伤害"（把一种伤害转成另一种并顺带治疗一点）。

---

# 第三卷 · 急救策略速查

## 3.1 按受伤类型选药

| 受伤类型 | 首选药 | 备选药 | 说明 |
|---|---|---|---|
| **外伤(割伤)** | Libital (C2) | Synthflesh, Medsuture | 治切伤 |
| **外伤(钝器)** | Helbital (C2) | — | 治钝器伤 |
| **烧伤** | Hercuri (C2) | Rezadone, Synthflesh, Medmesh | 治烧伤 |
| **能量/镭射** | Probital (C2) | — | 治能量伤 |
| **缺氧** | Salbutamol | Albuterol(更强), Convermol | 蓝字信号 |
| **毒素** | Multiver (C2) | Aiuri (C2), Calomel | 绿字信号 |
| **脑损伤** | Mannitol | Neurine, Liquid Solder(合成体) | 脑白质信号 |
| **出血** | Penthrite (C2) | Medsuture | 流血红字 |
| **各处全伤** | Cryoxadone(低温) | Pyroxadone(高温) | 低于170K有效! |
| **骨折** | Higherdite | Bone Gel | 骨头接合 |
| **基因变异** | Mutadone | — | 变异/畸形 |
| **眼伤** | Oculine | — | 看不清 |
| **耳聋** | Inacusiate | — | 听不到 |
| **药物过量** | Naloxone | — | OD了（清阿片类） |
| **酒精** | Antihol | — | 喝多了 |
| **精神病** | Haloperidol | Psicodine | 幻觉/精神错乱 |
| **过敏** | Diphenhydramine | — | 荨麻疹 |
| **体温异常** | Leporazine | — | 冷热不稳 |
| **恶心呕吐** | Ondansetron | — | 反胃 |
| **疤痕** | Dermagen (外用) | — | 旧伤疤 |

## 3.2 通用急救套餐

```
轻度伤:   Epinephrine(保命) + Libital(割) + Helbital(钝) = 基础三件套
中度伤:   + Salbutamol(氧) + Multiver(毒) + Mannitol(脑)
重度伤:   + Atropine(重伤) + Cryoxadone(全治) + Synthflesh(肉体)
垂危:     + Strange Reagent(复活!)
```

## 3.3 治疗优先级

```
1. 判断心跳: 停? → Defib + Epinephrine + Strange Reagent
2. 判断呼吸: 停? → Convermol
3. 判断出血: 流血? → Penthrite + Medsuture
4. 外伤/烧伤 → Libital/Helbital/Hercuri/Probital 按类型
5. 毒素 → Multiver
6. 脑伤 → Mannitol
7. 骨折 → Higherdite + 夹板
8. 辅助 → Salbutamol(氧) + Leporazine(体温) + Potass Iodide(防辐射)
```

## 3.4 特殊药用法

| 药 | 使用要点 |
|---|---|
| **Cryoxadone** | 需冷却到<170K才能生效，放冷冻机里 |
| **Synthflesh** | 一次50u，直接修复外伤+烧伤 |
| **Strange Reagent** | 心脏停跳时10u → 50%复活，成功了再Epinephrine |
| **Epinephrine** | 临界状态自动推，防死 |
| **Atropine** | 重伤自动推，每分钟推血+推氧 |
| **Calomel** | 强力但太猛，会把所有外物排掉，包括好药 |
| **Naloxone** | 针对吗啡/Impedrezene/Fentanyl/Krokodil，可皮肤渗透 |
| **Psicodine** | 过量30u→幻觉+毒素伤害，控制剂量 |
| **Granibitaluri** | 温和止痛（TRAIT_ANALGESIA），治小伤；过量50u→肝损 |
| **Ondansetron** | 止吐；8%概率嗜睡、15%概率耐力-10 |

---

# 第四卷 · 爆炸品全表

**代码**: `recipes/pyrotechnics.dm` (667行)

## 4.1 爆炸品威力系统

每份原料通过公式转化为爆炸: `爆炸力 = round(产量 / strengthdiv + modifier)`

| strengthdiv | 含义 | 越小=越强 |
|---|---|---|
| 2 | 最高威力(barely 2u=1级爆炸) | |
| 8 | 中等 | |
| 10 | 低威力 | |
| 20 | 极小威力 | |

## 4.2 爆炸品配方总表

| # | 名称 | 配方 | 产量 | 威力 | 稳定剂可用 | 条件 |
|---|---|---|---|---|---|---|
| **01** | **水+钾** | Water1 + K1 | — | strengthdiv=20 极小 | ❌ | 即时 |
| **02** | **圣水+钾** | Holy Water1 + K1 | — | strengthdiv=20→8 (≥150u) | ❌ | 破邪神 |
| **03** | **硝化甘油 NG** | Glycerol1 + Nitracid1 + H₂SO₄1 | 2u | **Strengthdiv=2 极高** | Exotic Stabilizer | 配方即爆 |
| **04** | **NG爆炸** | NG ≥1 | — | strengthdiv=2 | ❌ | ≥474K |
| **05** | **RDX(黑索金)** | Phenol2 + Nitracid1 + Acetone Oxide1 | 2u | **strengthdiv=8 高** | Stabilizing Agent | ≥404K |
| **06** | **RDX爆炸** | RDX ≥1 | — | strengthdiv=7, mod=2 | ❌ | ≥474K |
| **07** | **RDX+液态电** | RDX1 + Liquid Electricity1 | — | strengthdiv=3.5, mod=4 +火 | ❌ | 最强RDX |
| **08** | **RDX+Teslium** | RDX1 + Teslium1 | — | strengthdiv=3.5, mod=6 大火 | ❌ | 超级 |
| **09** | **TATP(三过氧三丙酮)** | Acetone Oxide1 + Nitracid1 + Pentaerythritol1 | 1u | **strengthdiv=3 极高** | Exotic Stabilizer | ≥450K(随机±49) |
| **10** | **TATP爆炸** | TATP ≥1 | — | strengthdiv=3(大体积可低于NG) | ❌ | ≥550K(随机±49) |
| **11** | **火药 Gunpowder** | Saltpetre1 + Multiver1 + S1 | 3u | 不直接爆 | ❌ | ≈20u配方弹 |
| **12** | **火药爆炸** | Gunpowder ≥1 | — | strengthdiv=10, mod=5 | ❌ | ≥474K, 延迟5~10秒 |
| **13** | **铝热剂 Thermite** | Al1 + Fe1 + O1 | 3u | 不爆, **烧穿地板** | ❌ | 放置引燃 |
| **14** | **EMP脉冲** | U1 + Fe1 + Al1 | — | EMP范围=vol/12~vol/7 | ❌ | 配方即发 |
| **15** | **闪光粉 Flash Powder** | Al1 + K1 + S1 | 3u | 闪光+晕 | Stabilizing Agent | 配方即发 |
| **16** | **闪光爆** | Flash Powder ≥1 | — | 闪光+晕 | ❌ | ≥374K |
| **17** | **烟雾粉 Smoke Powder** | K1 + Sugar1 + P1 | 3u | 烟雾+肺损伤 | Stabilizing Agent | 配方即发 |
| **18** | **音波粉 Sonic Powder** | O1 + Space Cola1 + P1 | 3u | 震聋+晕 | 稳定剂可用 | |
| **19** | **音波爆** | Sonic Powder ≥1 | — | 震聋+晕 | ❌ | ≥374K |
| **20** | **Phlogiston(燃素)** | P1 + H₂SO₄1 + Stable Plasma1 | 3u | 放出高温Plasma | 稳定剂可用 | |
| **21** | **凝固汽油 Napalm** | Oil1 + Fuel1 + Ethanol1 | 3u | 持续火焰 | | |
| **22** | **Cryostylane(制冷剂)** | Ice1 + Stable Plasma1 + N1 | 3u | **降温+冷却** | | ≤-200K |
| **23** | **Sorium(斥力)** | Hg1 + O1 + N1 + C1 | 4u | **斥开周围物体** | 稳定剂可用 | |
| **24** | **Sorium涡** | Sorium ≥1 | — | 斥力涡 | ≥474K | |
| **25** | **液态暗物质 LDM** | Stable Plasma1 + Radium1 + C1 | 3u | **引力吸拉** | 稳定剂可用 | |
| **26** | **LDM涡** | LDM ≥1 | — | 引力涡 | ≥474K | |
| **27** | **稳定剂** | Fe1 + O1 + H1 | 3u | **稳定爆炸物!** | | |
| **28** | **CLF3(三氟化氯)** | Cl1 + F3 | 4u | **自燃! 周围起火+1000K** | ≥424K | |
| **29** | **冰爆 Beesplosion** | Honey1 + Strange Reagent1 + Radium1 | — | **生成蜜蜂!** | | |
| **30** | **Penthrite+Epi** | Penthrite(C2)1 + Epinephrine1 | — | 爆炸(strengthdiv=5) | 清体内毒 | |
| **31** | **Penthrite+Atropine** | Penthrite1 + Atropine1 | — | 爆炸(strengthdiv=5, mod=5) | 清体内毒 | |
| **32** | **冰毒爆 Methsplosion** | Methamphetamine ≥1 | — | 爆炸+火焰(strengthdiv=12) | ≥380K | |
| **33** | **冰毒爆II** | Diethylamine1 + I1 + P1 + H1 | — | 爆炸 | ≥300K(室温) | |
| **34** | **Teslium 特斯拉胶** | Stable Plasma1 + Silver1 + Gunpowder1 | 3u | **电击传导介质** | | ≥400K |
| **35** | **Teslium闪电** | Teslium1 + Water1 | — | **3次特斯拉闪电+爆炸** | | 即时 |
| **36** | **Energized Jelly 电胶** | Slime Jelly1 + Teslium1 | 2u | 发光电浆胶 | | |
| **37** | **Pyrosium 自燃物** | Stable Plasma1 + Radium1 + P1 | 3u | **遇氧自燃+加热** | | 即时 |
| **38** | **N₂O爆炸** | N₂O1 | — | strengthdiv=9, mod=1 + 斥力涡 | | ≥575K |
| **39** | **灭火泡沫** | Stabilizing Agent1 + Fluorosurfactant1 + C1 | 3u | **灭火泡沫** | | 200K冷配 |
| **40** | **爱国者过载** | Planet Cracker1 + Triumphal Arch1 | — | strengthdiv=20 爆炸 | | 即时 |

> ✅ 本次补齐: #34-40（Teslium 系列 / Pyrosium / N₂O爆 / 灭火泡沫 / 爱国者过载）

## 4.3 Teslium 闪电链详解（新增）

Teslium1 + Water1 → 闪电（`teslium_lightning`）：

| 触发量 | 闪电 | 威力 |
|---|---|---|
| ≥10u | 第3道闪电 | created_volume × 120（最少 1200） |
| ≥40u | 第2道闪电 | created_volume × 50 |
| ≥75u | 第1道闪电 | created_volume × 20 |
| 全部 | 最终爆炸 | strengthdiv=100, mod=-100 |

- 闪电为特斯拉电弧（zap_range=7），ZAP_MOB_DAMAGE/OBJ/MOB_STUN/LOW_POWER_GEN
- 100u 时三次闪电威力为 8e5 / 2e6 / 4.8e6（对比特斯拉左轮手枪威力 10,000）
- 注释原文：`100 units : Zap 3 times, with powers 8e5/2e6/4.8e6. Tesla revolvers have a power of 10000 for comparison.`

## 4.4 Pyrosium 自燃（新增）

- 制作: Stable Plasma1 + Radium1 + P1 → 3u（Pyrosium）
- Pyrosium + O₂ → **即时加热**（`holder.expose_temperature(chem_temp + 10×vol, 1)`）
- 制作反应本身降温20K（`expose_temperature(20, 1)`）

## 4.5 Cryostylane 冷却链（新增）

- Cryostylane + O₂ → **强吸热冷却**（`cryostylane_oxygen`，thermic_constant=-5，每步降温）
- 反应条件: is_cold_recipe=TRUE，required_temp=99999 / optimal_temp=300（冷配异常值，实际极低温下运行）

## 4.6 稳定剂系统

| 稳定剂 | 配方 | 适用 | 效果 |
|---|---|---|---|
| **Stabilizing Agent** | Fe1 + O1 + H1 → 3u | 闪光/烟雾/音波/Sorium/LDM/Phlogiston/RDX | 抑制即时爆炸 |
| **Exotic Stabilizer** | Plasma Oxide1 + Stabilizing Agent1 → 2u | NG/TATP | 需要更多量 |

## 4.7 爆炸试剂效果数值表（深挖）

**代码**: `reagents/pyrotechnic_reagents.dm` (525行)

> `reagent_explode` 公式：`爆炸力 = round(体积 / strengthdiv + modifier)`。火花触发（on_spark_act）是大多数爆炸试剂的引爆方式。

| 试剂 | 引爆方式 | 爆炸参数 | 其他效果 |
|---|---|---|---|
| **Thermite** | 点燃 | — | 熔穿地板；体内烧伤+0.5/tick |
| **Nitroglycerin** | 火花/加热 | strengthdiv=2（最强） | 体内反而治疗心脏-0.5 |
| **Stabilizing Agent** | — | — | 稳定其他爆炸物；水培减不稳定度 |
| **CLF3 三氟化氯** | 配方即爆 | 火球 | 烧穿地板；接触火stack+min(vol/5,10)+点燃；体内火stack+0.1 |
| **Sorium** | 火花 | 斥力涡 range=√vol(1-6) | 推飞周围物体 |
| **Liquid Dark Matter** | 火花 | 引力涡 range=√(vol/2)(1-6) | 吸拉周围物体 |
| **Gunpowder** | 火花/≥474K | strengthdiv=10, mod=5（火花）；受震1+vol/6 | 有稳定剂时每次触发消耗稳定剂；延迟5-10秒 |
| **RDX** | 火花/≥474K | 无电：strengthdiv=8；有电：mod=2×log2(电量), strengthdiv=7/min(log,4)；≥474K：mod=2, div=7 | 电量越高越强（log2倍数） |
| **TATP** | 火花 | strengthdiv=1.5~3（随机） | 自杀级炸药 |
| **Flash Powder** | 火花 | 闪光 range=vol/15 | 4格内麻痹+倒地，远处眩晕；掉手持物；对硅基也生效 |
| **Smoke Powder** | 火花 | 烟雾 vol/1.5 | 载药烟雾；体内肺伤vol/15 |
| **Sonic Powder** | 火花 | 音波 range=vol/15 | 震聋10s+晕 |
| **Phlogiston** | 火花/接触 | 点燃 | 火stack+0.5/tick；接触火stack+1+点燃；火花时容器伤vol/3 |
| **Napalm** | 火花/接触 | 持续火 | 火stack+0.5/tick；接触火stack+min(vol/4,20)；水培植物伤×6 |
| **Cryostylane** | 与氧反应 | 0K冷却 | 体内：清氧1u/tick+体温-30；尸体防腐；手术减速1.1-1.5×；5u以上喷雾伤史莱姆 |
| **Pyrosium** | 与氧反应 | 3500K燃烧 | 体内：清氧0.5u/tick+体温+15 |
| **Teslium** | 周期性 | 电击5-20 | **体内每5-30tick随机电击5-20伤害**；siemens系数×2（易被电）；火花→特斯拉电弧 range=vol/5, power=vol×20 |
| **Energized Jelly** | — | 电击 | 果冻人：解昏迷-20+耐力-5；其他人=普通Teslium电击 |
| **Firefighting Foam** | 接触 | 灭火泡沫 | 灭热点、温度减半、灭火、灭人火 |

**火花触发等级**（SPARK_ACT 标志）：
- `SPARK_ACT_DESTRUCTIVE` = 爆炸并清空容器
- `SPARK_ACT_NON_DESTRUCTIVE` = 触发效果但保留试剂
- 火花来源：电击/火花塞/电弧/激光等

---

# 第五卷 · 毒素全表

**代码**: `recipes/toxins.dm` (407行)

## 5.1 毒素配方总表

| # | 名称 | 配方 | 产量 | 毒性 | 效果 |
|---|---|---|---|---|---|
| **T01** | **Formaldehyde** | Ethanol1 + O1 + Ag1 | 3u | ★★★ | 细胞毒素/防腐 |
| **T02** | **Fentanyl** | Space Drugs1 | 1u | ★★★★ | 强止痛但上瘾 |
| **T03** | **Cyanide** | Oil1 + NH₃1 + O1 | 3u | **★★★★★** | **极速氰化物中毒** |
| **T04** | **Itching Powder** | Fuel1 + NH₃1 + Multiver1 | 3u | ★ | **痒粉(恶作剧)** |
| **T05** | **Flu Acid** | H₂SO₄1 + F1 + H1 + K1 | 4u | ★★★★ | **溶解性强酸** |
| **T06** | **Nitracid** | Flu Acid1 + N1 + H₂O₂1 | 2u | ★★★★★ | **最强酸+爆炸催化剂** |
| **T07** | **Sulfonal** | Acetone1 + Diethylamine1 + S1 | 3u | ★★★ | 催眠镇定 |
| **T08** | **Lipolicide** | Hg1 + Diethylamine1 + Ephedrine1 | 3u | ★★★ | 快速燃脂(致死) |
| **T09** | **Mutagen** | Radium1 + P1 + Cl1 | 3u | ★★★ | **致突变/基因变异** |
| **T10** | **Lexorin** | Plasma1 + H1 + Salbutamol1 | 3u | ★★★★ | **呼吸停止** |
| **T11** | **热冰→Plasma** | Hot Ice1 | **12u Plasma** | ★★★ | 1热冰=12Plasma |
| **T12** | **Chloral Hydrate** | Ethanol1 + Cl3 + Water1 | 1u | ★★★ | 睡眠/麻醉 |
| **T13** | **Mutetoxin** | U2 + Water1 + C1 | 2u | ★★★★★ | **沉默毒素(不能说话)** |
| **T14** | **Zombie Powder** | Carpotoxin5 + Morphine5 + Cu5 | 2u | ★★★★ | **假死粉** |
| **T15** | **Ghoul Powder** | Zombie Powder1 + Epinephrine1 | 2u | ★★★ | 半假死(可移动) |
| **T16** | **Mindbreaker** | Si1 + H1 + Multiver1 | 5u | ★★★★ | **强效致幻** |
| **T17** | **Heparin** | Formaldehyde1 + Salt1 + Li1 | 3u | ★★★★ | **血稀剂(出血不止)** |
| **T18** | **Rotatium** | Mindbreaker1 + Teslium1 + Fentanyl1 | 3u | ★★★★ | **致幻+眩晕+旋转** |
| **T19** | **Anacea** | Haloperidol1 + Impedrezene1 + Radium1 | 3u | ★★★ | 净化所有药物效果 |
| **T20** | **Mimesbane** | Radium1 + Mutetoxin1 + Nothing1 | 3u | ★★★ | **哑剧毒(不能说话)** |
| **T21** | **Bonehurting Juice** | Mutagen1 + Itching Powder3 + Milk1 | 5u | ★★★ | **骨痛果汁(恶作剧)** |

> ✅ 本次补齐: T21 Bonehurting Juice（100K起配，最佳450K，过热900K；thermic_constant=-400 强吸热）

## 5.2 毒素试剂效果数值表（深挖）

**代码**: `reagents/toxin_reagents.dm` (1,765行)

> 毒素基类：每tick毒伤 = toxpwr × 纯度 × 代谢率（`health_required` 血量阈值内生效）。`toxpwr` 见下表。`liver_damage_multiplier` = 肝损伤倍率。

| 试剂 | toxpwr | 代谢率 | 特点/每tick效果 |
|---|---|---|---|
| **Amatoxin** | 2.5 | 默认 | 蘑菇强毒 |
| **Unstable Mutagen** | 0 | 默认 | 毒+0.25 + 随机基因突变（98%负面/2%正面） |
| **Plasma（液态）** | 3 | 默认 | 血中plasma+10；清Epinephrine 1u/tick；4500K可燃烧；火花→爆炸 |
| **Hot Ice Slush** | 3 | 默认 | 血中plasma+10 + 体温-3.5 |
| **Lexorin** | 0 | 默认 | **阻止呼吸**：氧+2.5、losebreath+1；10%喘息 |
| **Carnivorous Blood** | 0 | 1.5× | **食血**：血-2×倍率/tick（死亡×2、DNA匹配×3）；吸入清空 |
| **Slime Jelly** | 0 | 默认 | 5%概率毒+20-60；23%概率治疗5外伤 |
| **Carpotoxin** | 1 | 默认 | 太空鲤鱼神经毒；TRAIT_CARPOTOXIN_IMMUNE 免疫 |
| **Zombie Powder** | 0.5 | 默认 | **假死**：周期2-6混淆困口齿不清→6-9耐力+20→10+假死状态；吸入即刻假死 |
| **Ghoul Powder** | 0.8 | 默认 | 假死但可活动：氧+0.5；TRAIT_FAKEDEATH |
| **Mindbreaker** | 0 | 默认 | 幻觉+5s/tick（已有幻觉创伤则反治）；成瘾60 |
| **Plant-B-Gone** | 1 | 默认 | 植物伤×20；无面罩人类蒸气毒伤 |
| **Spore Toxin** | 1 | 默认 | 吸入80%概率咳+晕1s；眼模糊 |
| **Burning Spore** | 0.5 | 默认 | 火stack+1 + 点燃 |
| **Chloral Hydrate** | 0 | 1.5× | 周期2-11混淆困→11-51睡眠13.34→52+睡眠+毒0.34×(周期-51) |
| **Fake Beer (B33r)** | 0 | 1.5× | 伪装啤酒的睡眠药：周期2-51睡眠13.34→52+毒 |
| **Coffee Grounds** | 0.5 | 默认 | TRAIT_STIMULATED |
| **Ground Tea Leaves** | 0.1 | 默认 | TRAIT_STIMULATED |
| **Mute Toxin** | 0 | 默认 | 沉默（不能说话） |
| **Tirizene (Staminatoxin)** | 0 | 默认 | 耐力+0.5×15（逐tick衰减至3） |
| **Polonium** | 0 | 0.125× | **致命辐照**：概率+IRRADIATED；辐照后毒+4 |
| **Histamine** | 0 | 0.25× | 30%概率：眼模糊/咳/喷嚏/外伤+4；OD 30u：全伤+4/tick |
| **Formaldehyde** | 1 | 0.5× | 防腐；2.5%概率转化Histamine 5-15u |
| **Venom** | 0.1×vol | 0.25× | **肿胀**：体型+10%/tick；外伤+0.6×vol；8%概率转化Histamine |
| **Fentanyl** | 0 | 0.5× | 脑伤+3；毒+1；周期>18睡眠40；成瘾25 |
| **Cyanide** | 1.25 | 0.125× | 2.5%断气+1、4%眩晕+毒8 |
| **Itching Powder** | 0 | 0.4× | 8%概率身体部位痒+0.25伤；1.5%转化Histamine |
| **Initropidril** | 2.5 | 0.5× | 13%概率：麻痹60s/断气10+氧5-25/心脏病 |
| **Pancuronium** | 0 | 0.25× | 周期>10：Stun 80；10%断气+4；无味（taste_mult=0） |
| **Sodium Thiopental** | 0 | 0.75× | 周期>10：睡眠26.67；耐力+0.67 |
| **Sulfonal** | 0.5 | 0.125× | 周期>22：睡眠160 |
| **Amanitin** | 0 | 0.5× | **延迟毒**：累积毒伤6/tick，代谢完一次性爆发 |
| **Lipolicide** | 0 | 0.5× | 营养-3/tick；饥饿时毒+1 |
| **Coniine** | 1.75 | 0.06× | 极慢代谢；losebreath增至5 |
| **Spewium** | 0 | 默认 | **呕吐**：周期>11呕吐+清毒；OD 29u：吐器官 |
| **Curare** | 1 | 0.125× | 周期>11：麻痹240；氧+2 |
| **Heparin** | 0 | 0.2× | **抗凝**：TRAIT_BLOOD_FOUNTAIN；清Coagulant 5u/tick |
| **Rotatium** | 0.5 | 0.6× | 屏幕旋转（每20周期+1°，上限89°） |
| **Anacea** | 0.15 | 0.08× | 清医疗6.25u/tick×purge（有Calomel/PenAcid时降到0.5） |
| **Sulfuric Acid** | 1 | 自耗 | 酸pwr=10；摄入外伤6；注射×1.5；接触acid_act |
| **Fluorosulfuric Acid** | 2 | 自耗 | 酸pwr=42；体内每tick烧伤+0.5×(周期/15) |
| **Nitric Acid** | 3 | 自耗 | 酸pwr=5；体内烧伤+0.5×(体积/10) |
| **Toxin Microcapsules** | 0 | 0（潜伏） | 周期≤31潜伏，之后毒+2.5/tick+5%麻痹 |
| **Mime's Bane** | 0 | 默认 | TRAIT_EMOTEMUTE（不能做动作表情） |
| **Bonehurting Juice** | 0 | 默认 | 耐力+3.25；10%概率说"oof ouch my bones"；OD 50u：2%断肢+20外伤 |
| **Bungotoxin** | 0 | 0.5× | **心脏毒**：心伤+3；晕时附加混淆 |
| **Lead Acetate** | 0.5 | 默认 | 耳伤+0.5、脑伤+0.5 |
| **Spider Toxin** | 1 | 默认 | 血量>40才生效；无肝伤 |
| **Viper Spider Toxin** | 5 | 默认 | 血量>10生效；幻觉+5s |
| **Tetrodotoxin** | 0→1.5 | 0.1× | **河豚毒**：4阶段（7-13抖动口齿不清→13-21耐力麻痹→21-29器官伤→29+脑/肺伤）；周期>38 5%心脏病；TRAIT_TETRODOTOXIN_HEALING 免疫 |
| **Industrial Waste** | 2 | — | 酸pwr=30；≥45u容器损坏；倾倒污染地面 |
| **Gibbium** | 0 | 4× | 周期≥5：强制感染GBS（无传播） |
| **Spider Serum** | 0 | 默认 | 10%概率吐蜘蛛；周期≥30变巨型蜘蛛+反叛者 |
| **Gatfruit (Phytotoxin)** | 1 | 默认 | **枪果毒**（稀有枪果植物产）；**无肝伤**（liver_damage_multiplier=0），纯毒伤 |

---

# 第六卷 · 毒品全表

**代码**: `recipes/drugs.dm` (208行) + `reagents/drug_reagents.dm` (993行)

## 6.1 毒品配方

| # | 名称 | 配方 | 产量 | 效果 |
|---|---|---|---|---|
| **D01** | **Space Drugs** | Hg1 + Sugar1 + Li1 | 3u | 致幻+傻笑 |
| **D02** | **Krokodil** | Diphenhydramine1 + Morphine1 + Cleaner1 + K1 + P1 + Fuel1 | 6u | **止痛+皮肤坏死** |
| **D03** | **Methamphetamine** | Ephedrine1 + I1 + P1 + H1 | 4u | **加速+高强度干活** |
| **D04** | **冰毒结晶** | Meth+Acid(冷却) | — | 固体毒 |
| **D05** | **Bath Salts** | Bad Food1 + Saltpetre1 + Nutriment1 + Cleaner1 + Enzyme1 + Tea1 + Hg1 | 7u | 狂怒+加速+幻觉 |
| **D06** | **Aranesp** | Epinephrine1 + Atropine1 + Morphine1 | 3u | 血氧+止痛 |
| **D07** | **Happiness** | N₂O2 + Epinephrine1 + Ethanol1 | 4u | 欣快感 |
| **D08** | **Pump-Up** | Epinephrine2 + Coffee5 | 5u | 兴奋+提神 |
| **D09** | **Maint Tar 维修焦油** | Tea1 + Yuck1 + Fuel1 **或** Tea1 + Enzyme3 + Fuel1 | Tar3 + Acid1 | **维修通道土制毒** |
| **D10** | **Maint Sludge 维修污泥** | Tar3 + Flu Acid1（催化 H₂O₂5） | 1u | 土制毒进阶 |
| **D11** | **Maint Powder 维修粉末** | Sludge6 + Nitracid1 + Enzyme1（催化 Acetone Oxide5） | 1u | 土制毒最高级 |
| **D12** | **Moon Rock 月球石** | Kronkus Extract15 + Fuel5 + Ammonia3 | 固体 | 固体毒品（Draintek变体: Lye3 替代 Ammonia3） |
| **D13** | **Blastoff Ampoule 发射安瓿** | Silver10 + Cyanide10 + Lye5 | 固体 | 一次性安瓿（剧毒配方） |
| **D14** | **Saturnx Glob 土星X球** | Lead5 + Nothing5 + Maint Tar10 | 固体 | **隐身毒品!**（全身隐形+HCY色移+径向模糊） |

> ✅ 本次补齐: D09-D14（TianGuan/Nova 新增毒品，含 Maint 链、Moon Rock、Saturnx）

## 6.2 新增毒品详解

**Maint 链**（维修通道土法制毒，从简单到复杂）：
```
Yuck1 + Tea1 + Fuel1 ──▶ Maint Tar (3u) + Acid1
Maint Tar3 + Flu Acid1 (催化H₂O₂5) ──▶ Maint Sludge (1u)
Maint Sludge6 + Nitracid1 + Enzyme1 (催化AcetoneOxide5) ──▶ Maint Powder (1u)
```

**Saturnx Glob**（`drug_reagents.dm` L712-776）：服用后变为隐形人（invisible_man），全屏 HCY 色彩矩阵滤镜 + 径向模糊，代谢结束恢复。制作需 Lead5 + Nothing5 + Maint Tar10。

**Moon Rock**: Kronkus Extract 来自植物（水培），制作时随机像素偏移散布。

## 6.3 毒品试剂效果（`drug_reagents.dm`）

| 试剂 | 代谢效果 | 过量 |
|---|---|---|
| **Methamphetamine** | 加速1.5x+高强度干活，on_merge 增强 | 毒素伤害 |
| **Krokodil** | 强力止痛 | 不可逆皮肤坏死 |
| **Bath Salts** | 狂怒+加速+幻觉 | 精神错乱 |
| **Maint Tar** | 致幻 | 毒素 |
| **Maint Sludge** | 致幻（更强） | 毒素 |
| **Maint Powder** | 强致幻 | 高毒素 |
| **Saturnx** | **隐身+画面滤镜** | — |

## 6.4 毒品试剂效果数值表（深挖）

**代码**: `reagents/drug_reagents.dm` (993行)

| 试剂 | 代谢率 | 过量阈值 | 每tick效果 | 过量效果 |
|---|---|---|---|---|
| **Space Drugs** | 默认 | 30u | 药效+30s；5%概率随机走一步；3.5%抖/流口水/呻吟/傻笑；成瘾60 | 幻觉+10s |
| **Cannabis** | 0.125× | ∞（不过量） | 石乐志状态；营养-0.6；4%躺下睡眠/坐下瘫痪10s；安抚猴子 | — |
| **Nicotine** | 0.125× | 15u | 解昏迷-20s；去抖动；TRAIT_STIMULATED；成瘾10 | 毒+0.4、氧+4.4 |
| **Krokodil** | 默认 | 20u | 止痛+麻木；周期36：**皮肤坏死→秃头+25外伤+变Krokodil成瘾者种族**；成瘾30 | 脑伤+0.25、毒+0.25 |
| **Methamphetamine** | 0.75× | 20u | 解昏迷-26.67、耐力-3.34、抖动；脑伤+0.67×(1-4)；TRAIT_STIMULATED；成瘾75 | 随机移动、10%笑、18%丢物、毒+0.67、脑伤 |
| **Bath Salts** | 默认 | 20u | 耐力-6、脑伤+4、幻觉+10s；**免疫眩晕+睡眠**；TRAIT_STUNIMMUNE+SLEEPIMMUNE+ANALGESIA；狂暴创伤；成瘾25 | 幻觉+10s、8格随机移动、28%丢物 |
| **Aranesp** | 默认 | — | 耐力-18、毒+0.5；30%断气+1；TRAIT_STIMULATED；成瘾75 | — |
| **Happiness** | 默认 | 20u | 去抖动/混淆、厌恶清零、脑伤+0.2；TRAIT_FEARLESS+ANALGESIA；成瘾30 | 16%概率情绪突变（笑/晕/怒） |
| **Pump-Up** | 2× | 30u | 抖动+2.5s；7.5%断气+1+毒+2；TRAIT_BATON_RESISTANCE+ANALGESIA+STIMULATED；成瘾400（最高） | 2.5%丢物、7.5%抖、10%断气+耐力+4 |
| **Maint Tar** | 默认 | 30u | 解昏迷-10；**肝伤+1.5**；成瘾120 | 毒+5、肝+3 |
| **Maint Sludge** | 2× | 25u | 毒+0.125；TRAIT_HARDLY_WOUNDED+ANALGESIA（抗伤）；成瘾300 | 毒+0.25、5%毒+2.5+呕吐 |
| **Maint Powder** | 0.5× | 15u | **学习加速**：经验倍率+volume/3/tick；脑伤+0.1；成瘾50 | 脑伤+6 |
| **Mushroom Hallucinogen** | 0.2× | 30u | 口齿不清+抖动递增（50s→100s）；**全屏彩虹色轮转+波浪滤镜**；成瘾20 | 10%抖动/吐、10%巴别塔 |
| **bLaSToFF** | 默认 | 30u | 肺伤+0.3、解倒地-20；**强制跳舞**（概率flip/spin，3次后超级后空翻/扔舞伴）；HCY滤镜；成瘾40 | 肺伤+0.3、spin概率+ |
| **Saturn-X** | 默认 | 25u | **隐身**：TRAIT_INVISIBLE_MAN+藏外部器官；肝伤+0.3；HCY色移+径向模糊滤镜；成瘾30 | 7.5%咯咯笑、5%大笑、肝+0.2 |
| **Stabilized Saturn-X** | 0.15× | 50u | 同上但代谢慢、成瘾5（战术版） | — |
| **Kronkaine** | 0.75× | 20u | 耐力-0.54×vol/tick；**心伤+0.67×(0.1+0.04×vol)**；解睡-1.34、减困-6.67；摄入/吸入瞬间耐力+12/unit；**清毒惩罚：+肾上腺危象**；成瘾50 | 心伤+0.34、抖动；75%心伤警告 |
| **Gore（脏Kronkaine）** | 0.75× | 20u | 回声定位者：外伤/烧伤-2；否则同上 | **立即变弹跳城堡+粉身碎骨** |
| **Syndol** | 默认 | 10u | **恐惧定制幻觉**（执法者见叛徒/维修工见保安，5分钟）；肝伤+0.5；成瘾50 | 刷新幻觉 |

**毒品成瘾强度排行**：Pump-Up(400) > Maint Sludge(300) > Maint Tar(120) > Meth(75) > Aranesp(75) > Space Drugs(60) > Kronkaine(50) > Maint Powder(50) > Syndol(50) > bLaSToFF(40) > Krokodil(30) > Happiness(30) > Saturn-X(30) > Bath Salts(25) > Mushroom(20) > Nicotine(10)

---

# 第七卷 · 基础原料链

**代码**: `recipes/others.dm`（1,167行）

> 这些是化学系统的"底层原料"，很多配方依赖它们。原文档03遗漏了大部分，本次全部补齐。

## 7.1 基础化学品

| 名称 | 配方 | 产量 | 用途 |
|---|---|---|---|
| **Acetone** | Oil1 + Fuel1 + O1 | 3u | 溶剂/原料 |
| **Oil** | Fuel1 + C1 + H1 | 3u | 基础油 |
| **Phenol** | Water1 + Cl1 + Oil1 | 3u | 酚/医药原料 |
| **Ash** | Oil1(≥480K) | 1u | 灰烬/肥料 |
| **Saltpetre** | K1 + N1 + O3 | 3u | 火药原料+肥 |
| **Lye** | Na1 + H1 + O1 | 3u | **碱液** |
| **Lye alt** | Ash1 + Water1 + C1 | 2u | 碱液替代 |
| **Ammonia** | H3 + N1 | 3u | 肥料+原料 |
| **Diethylamine** | Ammonia1 + Ethanol1 | 2u | 肥料+原料 |
| **Sodium Chloride (盐)** | Na1 + Cl1 | 2u | 调味+原料（有液态电时抑制） |
| **Sterilizine 消毒液** | Ethanol1 + Multiver1 + Cl1 | 3u | 消毒+Medmesh原料 |
| **Glycerol 甘油** | Corn Oil3 + Acid1 | 1u | **NG硝化甘油原料!** |
| **Cryptobiolin** | K1 + O1 + Sugar1 | 3u | Rezadone/Spaceacillin原料 |
| **Impedrezene** | Hg1 + O1 + Sugar1 | 2u | Psicodine/Anacea原料 |
| **Hydrogen Peroxide** | Water1 + O1 + Cl1 | 3u | Naloxone/Nitracid原料 |
| **Acetone Oxide** | Acetone2 + O1 + H₂O₂1 | 2u | **RDX/TATP原料!** |
| **Pentaerythritol** | Acetaldehyde1 + Formaldehyde3 + Water1 | 2u | **Penthrite/TATP原料!** |
| **Acetaldehyde** | Acetone1 + Formaldehyde1 + Water1（450K） | 3u | Pentaerythritol原料 |
| **Stable Plasma** | Plasma1（催化 Stabilizing Agent1） | 1u | 大量配方原料 |
| **Lube 润滑油** | Water1 + Si1 + O1 | 4u | 润滑/恶作剧 |
| **Superlube 超滑油** | Lube1 + Strange Reagent1 + Banana1 | 3u | 永久润滑 |
| **Spraytan 喷晒** | Orange Juice1 + Oil1 | 2u | 晒黑皮肤（变体: 植物油） |
| **CO₂** | C1 + O2（777K） | 3u | 气体 |
| **N₂O 笑气** | Ammonia2 + N1 + O2（525K起, 最佳550K） | 5u | 麻醉气体 |
| **Cellulose 碳化** | Cellulose1（512K） | C1 | 纤维素→碳 |
| **Mulligan** | Jelly Mutationtoxin1 + Mutagen1 | 1u | 突变毒素（洗DNA） |
| **Holy Water 圣水** | Hollow Water1（催化 Holy Water1） | 1u | 圣水（破邪/复活原料） |
| **Salt Water 盐水** | Water1 + Salt1 | 2u | Salglu/盐水泡沫原料 |
| **Ice 冰** | Water1（<274K, 最佳200K） | 1.09u | 制冷/原料 |
| **Water 水** | Ice1（>274K, 最佳350K） | 0.92u | 冰融化回水 |
| **Universal Indicator** | Ash1 + Ethanol1 + Iodine1（274K） | 3u | pH指示剂 |
| **Yuck** | Fuel3（容器: 死老鼠） | 4u | 恶心物/Maint Tar原料 |
| **Slime Jelly 史莱姆胶** | Oil3 + Radium2 + Tinlux1（容器: 发光蘑菇） | 5u | Pyroxadone/Energized Jelly原料 |
| **Astrotame 太空糖** | 代糖（营养0） | — | 零卡甜味剂：营养=0、代谢2×、甜味×8、过量17u |
| **Unholy Water 邪水** | 非固定配方（禁术提取） | — | **邪教圣水**：邪教徒=凝血+全恢复+减困；普通人=全伤+脑伤（50u约90伤）；皮肤渗透 |
| **E-Z Nutrient** | 肥料 | — | 水培：不稳定性+0.2、效力+vol×0.3、产量+vol×0.1；**人喝5%概率毒** |
| **Left 4 Zed** | 肥料 | — | 水培：植物健康+vol×0.1、不稳定性+vol×0.2（**促变异**）；毒概率13% |
| **Robust Harvest** | 肥料 | — | 水培：不稳定性**-0.25**（抑变异）、效力+vol×0.1、产量+vol×0.2；毒概率8% |
| **Enduro Grow** | 肥料 | — | 水培：效力-vol×0.1、产量-vol×0.075、**耐力+vol×0.35**（保命肥）；毒概率8% |
| **Liquid Earthquake** | 肥料 | — | 水培：杂草率+vol×0.1、杂草概率+vol×0.3、**生产周期-vol×0.075**（加速）；毒概率13% |

> ✅ 本次补齐: Sterilizine/Glycerol/Cryptobiolin/Impedrezene/H₂O₂/Acetone Oxide/Pentaerythritol/Acetaldehyde/Stable Plasma/Lube/Superlube/Spraytan/CO₂/N₂O/Mulligan/Holy Water/Salt Water/Ice/Water/Universal Indicator/Yuck/Slime Jelly/Astrotame/Unholy Water/Plantnutriment×5 共 28 种

## 7.2 固化/材料化反应（新增）

| 名称 | 配方 | 产物 |
|---|---|---|
| **Plasma 固化** | Fe5 + Frost Oil5 + Plasma20 | **等离子板**（即时） |
| **Gold 固化** | Frost Oil5 + Gold20 + Fe1 | **金板**（即时） |
| **Uranium 固化** | Frost Oil5 + U20 + K1 | **铀板**（即时） |
| **Silver 固化** | Silver20 + C10（630K） | **银板**（即时） |
| **Hauntium 固化** | Holy Water10 + Hauntium20 + Fe1 | **幽灵素板**（即时） |
| **Soapification 制肥皂** | Liquid Gibs10 + Lye10（374K） | **手工肥皂**（每vol一个） |
| **Omegasoapification** | 土豆汁/蜥蜴酒/猴粉/Krokodil/Nitracid/Baldium/Hooch/Bluespace/Pump-Up/太空可乐 各10（999K, 过热1200K） | **欧米茄肥皂** |
| **Candlefication 制蜡烛** | Liquid Gibs5 + O5（374K） | **蜡烛**（每vol一个） |
| **Meatification 制肉** | Liquid Gibs10 + Nutriment10 + C10 | **肉块**（每vol一个） |
| **Capsaicin 冷凝** | Capsaicin1 + Ethanol5 | 浓缩辣椒素5u |
| **Bone Gel 骨胶** | Bone Dust10 + C10（630K） | **骨胶**（骨科手术用） |

## 7.3 电解 & 盐电解

| 名称 | 配方 | 产物 |
|---|---|---|
| **电解水** | Liquid Electricity1 + Water5 | O2.5 + H5 |
| **电解水(富电)** | Enriched Liquid Electricity1 + Water5 | O2.5 + H5 |
| **盐电解** | Salt5（催化 Liquid Electricity1） | Cl2.5 + Na2.5 |
| **Slime Extractification** | Slime Jelly30 + Frost Oil5 + Plasma5 | **灰色史莱姆萃取物** |
| **Metalgen Imprint** | Metalgen1 + LDM1 | **金属印记**（吸收40u金属, 记录材料） |
| **Gravitum 重力剂** | Wittel1 + Sorium10 | 10u | 重力相关 |

### 史莱姆萃取全录（52 反应 · 源码全量）

> **源码**: `code/modules/reagents/chemistry/recipes/slime_extracts.dm`（589 行，52 个反应 datum）。产物在 `results` 字段或 `on_reaction()` 中。此前标注"科研内容另行处理"为悬空引用，现补全。

| 提取物 | 试剂 | 产物/效果 |
|---|---|---|
| **灰色** | toxin/plasma = 1 | 生成 1 只灰色史莱姆 |
| **灰色** | water = 5 | medicine/epinephrine = 3 |
| **灰色** | blood = 1 | monkeycube |
| **绿色** | toxin/plasma = 1 | mutationtoxin/jelly = 1 |
| **绿色** | blood = 1 | mutationtoxin = 1 |
| **绿色** | uranium/radium = 1 | mutationtoxin/lizard = 1 |
| **绿色** | consumable/milk = 1 | mutationtoxin/felinid = 1 |
| **绿色** | cellulose = 1 | mutationtoxin/moth = 1 |
| **绿色** | consumable/liquidelectricity/enriched = 1 | mutationtoxin/ethereal = 1 |
| **金属** | toxin/plasma = 1 | plasteel、iron |
| **金属** | water = 1 | rglass、glass |
| **金色** | toxin/plasma = 15 | 生成随机生物（金色史莱姆召唤） |
| **银色** | toxin/plasma = 1 | 生成 1-2 个随机物品 |
| **蓝色** | toxin/plasma = 1 | consumable/frostoil = 10 |
| **蓝色** | blood = 1 | stabilizer |
| **蓝色** | water = 5 | 喷射 80 单位泡沫 |
| **深蓝** | toxin/plasma = 1 | 5 秒后生成氮气（温度 2.7K），提取物 55 秒后消失 |
| **深蓝** | water = 1 | fireproof |
| **橙色** | blood = 1 | consumable/capsaicin = 10 |
| **橙色** | toxin/plasma = 15 | 5 秒后喷出等离子气体（1000K），提取物 55 秒后消失 |
| **橙色** | water = 5 | phosphorus = 10, potassium = 10, consumable/sugar = 10 |
| **黄色** | blood = 15 | 产生 EMP 脉冲 |
| **黄色** | toxin/plasma = 1 | slime |
| **黄色** | water = 1 | slime |
| **紫色** | toxin/plasma = 1 | steroid |
| **紫色** | blood = 1 | medicine/regen_jelly = 5 |
| **深紫** | toxin/plasma = 1 | plasma |
| **红色** | toxin/plasma = 1 | mutator |
| **红色** | blood = 1 | 史莱姆陷入狂暴（嗜血） |
| **红色** | water = 1 | speed |
| **粉色** | toxin/plasma = 1 | docility |
| **粉色** | blood = 1 | genderchange |
| **黑色** | toxin/plasma = 1 | aslimetoxin = 1 |
| **油** | toxin/plasma = 15 | 史莱姆爆炸 |
| **油** | blood = 1 | consumable/nutriment/fat/oil/corn = 10 |
| **浅粉** | toxin/plasma = 1 | sentience |
| **浅粉** | water = 1 | renaming |
| **精金** | toxin/plasma = 15 | adamantine |
| **蓝空间** | blood = 1 | bluespace |
| **蓝空间** | toxin/plasma = 1 | 生成水晶 |
| **蓝空间** | water = 1 | slimeradio |
| **天蓝** | toxin/plasma = 1 | enhancer |
| **天蓝** | blood = 1 | slime |
| **深褐** | toxin/plasma = 1 | timestop |
| **深褐** | water = 1 | camera、camera_film |
| **深褐** | blood = 1 | sepia |
| **黄铁** | toxin/plasma = 1 | 生成颜料/染色 |
| **黄铁** | blood = 1 | 生成蜡笔 |
| **彩虹** | toxin/plasma = 1 | 随机效果（彩虹史莱姆） |
| **彩虹** | toxin/slimejelly = 1 | 爆炸（彩虹史莱姆炸弹） |
| **彩虹** | blood = 1 | transference |
| **彩虹** | water/holywater = 5, uranium = 5 | flight |

---

# 第八卷 · 其他化学品

## 8.1 泡沫系统

### 发泡剂

| 名称 | 配方 | 产物 |
|---|---|---|
| **发泡剂(Foaming Agent)** | Lithium1 + H1 | 1u |
| **智能发泡剂** | Foaming Agent3 + Acetone1 + Fe1 | 3u |
| **氟表面活性剂** | F2 + C2 + H₂SO₄1 | 5u |

### 泡沫类型

| 泡沫 | 配方 | 寿命 | 滑 |
|---|---|---|---|
| **基础泡沫** | Surfactant1 + Water1 | 8s | ✅ |
| **中空泡沫** | Surfactant1 + Hollow Water1 | 24s | ✅ |
| **盐泡沫** | Surfactant1 + Salt Water1 | **1s** | ❌ |
| **冰泡沫** | Surfactant1 + Ice1 | — | ❌ |
| **苏打泡沫** | Surfactant1 + Soda Water1 | 1s | ✅ |
| **圣泡沫** | Surfactant1 + Holy Water1 | **24s** | ❌ |
| **金属泡沫** | Al3 + Foaming Agent1 + Flu Acid1 | →金属墙 | |
| **智能泡沫** | Al3 + Smart Foaming Agent1 + Flu Acid1 | →智能墙(补洞) | |
| **铁泡沫** | Fe3 + Foaming Agent1 + Flu Acid1 | →铁墙 | |

## 8.2 清洁与农业

| 名称 | 配方 | 产物 | 用途 |
|---|---|---|---|
| **Space Cleaner** | Ammonia1 + Water1 | 2u | 通用清洁剂 |
| **Plant BGone** | Toxin1 + Water4 | 5u | 杀植物 |
| **Weedkiller** | Toxin1 + Ammonia4 | 5u | 除草 |
| **Pestkiller** | Toxin1 + Ethanol4 | 5u | 杀虫 |
| **Drying Agent** | Stable Plasma2 + Ethanol1 + Na1 | 3u | 干燥吸水 |

## 8.3 工业化学品

| 名称 | 配方 | 产量 | 用途 |
|---|---|---|---|
| **Plastic Polymers** | Oil5 + H₂SO₄2 + Ash3 | 10u | 塑料原料 |
| **Plastic Hardening** | Plastic Polymers10(≥374K) | →**塑料板** | |
| **Royal Bee Jelly** | Mutagen10 + Honey40 | 5u | 蜂王浆 |
| **Glitter 闪光** | Plastic Polymers1 + Al1（催化 Stabilizing Agent1） | 1u | 闪光粉 |
| **Glitter Pigmentation** | Glitter1 + Acetone1 | 1u | 变色（混合容器内其他试剂颜色） |
| **Metalgen Imprint** | Metalgen1 + LDM1 | — | 金属印记（见7.3） |

## 8.4 生物化学品

| 名称 | 配方 | 效果 |
|---|---|---|
| **Life** | Strange Reagent1 + Synthflesh1 + Blood1(≥374K) | **创造敌对生物(猴子)!** |
| **Life Friendly** | Strange Reagent1 + Synthflesh1 + Sugar1(≥374K) | **创造友好生物** |
| **Corgium** | Nutriment1 + Colorful1 + Strange Reagent1 + Blood1(≥374K) | **创造柯基狗!** |
| **Lifish** | Fishy Reagent1 + Synthflesh1 + Blood1(≥374K) | **创造随机鱼** |
| **Monkey Powder** | Banana1 + Nutriment2 + Liquid Gibs1 | 5u |
| **Monkey** | Monkey Powder50 + Water1 | **创造猴子!** |
| **Angry Monkey** | Monkey Powder50 + Inverse Bath Salts10 | **创造愤怒猴子!** |
| **Butterflium** | Colorful1 + Omnizine1 + Strange Reagent1 + Nutriment1 | **创造蝴蝶** |
| **Scream** | Strange Reagent1 + Cream5 + Lizard Wine5(≥374K) | **强制尖叫(可调音量)!** |
| **Laughter** | Sugar1 + Banana1 | **10u笑气** |
| **Ants 蚂蚁** | Sugar大量 | 繁殖蚂蚁 |
| **Ant Slurry** | 蚂蚁+Synthflesh | 做更大的蚂蚁 |
| **Carnivorous Blood 配对** | Carnivorous Blood1 + Blood1 | 吸收血中DNA |
| **Carnivorous Blood 喂食** | Protein1（催化 Carnivorous Blood1） | 血食生物喂食 |

## 8.5 地毯系统（23色）

**代码**: `others.dm` L477-571。基础地毯 = Space Drugs1 + Blood1，加染料变色：

### 普通色（9种）

| 颜色 | 配方 |
|---|---|
| 基础红 | Space Drugs1 + Blood1 |
| 黑色 | Carpet1 + Oil1 |
| 蓝色 | Carpet1 + Cryostylane1 |
| **青色** | Carpet1 + **Cyanide1**（梗: cyanide→cyan） |
| 绿色 | Carpet1 + Green Beer1 |
| 橙色 | Carpet1 + Orange Juice1 |
| 紫色 | Carpet1 + Regen Jelly1 |
| 红色 | Carpet1 + Liquid Gibs1 |
| 皇家黑 | Black Carpet1 + Royal Bee Jelly1 |
| 皇家蓝 | Blue Carpet1 + Royal Bee Jelly1 |

### 霓虹系列（13种，Carpet1 + Tinlux1 + ...）

| 颜色 | +配方 |
|---|---|
| 白 | + Na1 |
| 红 | + Mindbreaker1 |
| 橙 | + Vitfro(维他命) |
| 黄 | + Stabilizing Agent1 |
| 青柠 | + Lime Juice1 |
| 绿 | + Mutagen1 |
| 青 | + Salbutamol1 |
| 青蓝 | + Nicotine1 |
| 蓝 | + Happiness1 |
| 紫 | + Plasma Oxide1 |
| 紫罗兰 | + Helbital(C2)1 |
| 粉 | + Impedrezene1 |
| 黑 | + Multiver(C2)1 |

> ⚠️ 修正: 实际 23 色（基础1 + 普通9 + 霓虹13），原文档写 24 色有误

## 8.6 反应剂 (Reaction Agents)

**代码**: `reaction_agents.dm` (123行)

| 名称 | 配方 | 用途 |
|---|---|---|
| **Basic Buffer (pH碱缓冲)** | NH₃3 + Cl2 + H2 + O2 | **推高pH** |
| **Acidic Buffer (pH酸缓冲)** | Na2 + H2 + Ethanol2 + Water2 | **拉低pH** |
| **Prefactor A** | Phenol1 + Ethanol3 + Plasma1(800K冷配) | 竞争反应中间体 |
| **Prefactor B** | Prefactor A5(50K) | 竞争反应中间体 |
| **Purity Tester** | Prefactor A5 + Stable Plasma5 | 检测纯度 |
| **Speed Agent** | Prefactor B5 + Stable Plasma5 | 加速反应 |

## 8.7 特殊化学品

**代码**: `special.dm` (320行) + `others.dm`

| 名称 | 配方 | 效果 |
|---|---|---|
| **PAX** | Mindbreaker1 + Synaptizine1 + Water1 | 3u **强制非暴力/镇静** |
| **Colorful Reagent** | Stable Plasma1 + Radium1 + Space Drugs1 + Cryoxadone1 + Triple Citrus1 | 5u **闪烁彩色光** |
| **Hair Dye** | Colorful Reagent1 + Cl1 + Space Drugs1 | 5u染发 |
| **Barber's Aid** | Carpet1 + Radium1 + Space Drugs1 | 5u理发 |
| **Concentrated Barber's Aid** | Barber's Aid1 + Mutagen1 | 2u |
| **Baldium** | Radium1 + H₂SO₄1 + Lye1(≥395K) | 1u **脱发剂** |
| **Sonic Powder** | O1 + Space Cola1 + P1 | 3u震聋 |
| **Yuck** | Fuel3(容器:死老鼠) | 4u **恶心物** |
| **Gravitum** | Wittel1 + Sorium10 | 10u 重力相关 |
| **Bone Gel** | Bone Dust10 + C10(630K) | **骨骼修复** |
| **Ice** | Water1(<274K) | 冰块 |
| **Water(H₂+O₂)** | H2 + O1 | 水合成 |
| **Universal Indicator** | Ash1 + Ethanol1 + Iodine1(274K) | pH指示剂 |
| **Eigenstate** | Bluespace1 + Stable Plasma1 + Caramel1 | **量子态化学!** (过热爆炸/过杂有毒) |
| **Ants** | Sugar大量 | 繁殖蚂蚁 |
| **Ant Slurry** | 蚂蚁+Synthflesh | 做更大的蚂蚁 |
| **Hauntium** | (固化见7.2) | 幽灵相关 |
| **Glitter** | Plastic Polymers1 + Al1 | 闪光粉 |
| **Glitter Pigmentation** | Glitter1 + Acetone1 | 闪光染色 |
| **Carnivorous Blood** | (配对/喂食见8.4) | 血食生物喂养 |
| **Fish Hallucinogen 降解** | Fish Mindbreaker1（363K起） | 鱼致幻剂→蛋白质0.1 |

## 8.8 随机配方系统

**代码**: `special.dm`

| 随机配方 | 周期 | 配方范围 | 效果 |
|---|---|---|---|
| **Secret Sauce** | **7天重置** (persistence_period=7) | 食物类试剂中随机2~5种 | 产生Secret Sauce |
| **Metalgen** | **3天重置** (persistence_period=3) | 医疗试剂中随机2~5种 + Wittel催化 | 产生**金属生成剂**(20u) |
| **Gorgium** | **7天重置** (persistence_period=7) | 任意试剂中随机 | 产生**石化剂**(变石头) |

**获取方式**: 在地上捡到 `Old Recipe` 纸张，配方按周期自动变化。

## 8.9 催化剂系统

**代码**: `catalysts.dm` (98行)

| 名称 | 配方 | 效果 |
|---|---|---|
| **Catalyst Precursor (Temp)** | 温度催化前体（基础） | — |
| **Catalyst Precursor (pH)** | pH催化前体（基础） | 有金时反应速率×5 |
| **Thermic Modulator** | Precursor Temp5 + Stable Plasma5 | 调节反应放热 |
| **Ionic Modulator** | Precursor pH5 + Stable Plasma5 | 调节反应pH |
| **Medical Speed Catalyst** | Libital3 + Probital4 + Plasma2（200K起, 最佳500K, 过热800K） | **加速医疗反应**（过热/过杂→爆炸烟） |

---

# 第九卷 · 病毒培养系统

**代码**: `others.dm` L186-343（病毒食物 + mix_virus 12级）

## 9.1 病毒食物（精确配方）

| # | 名称 | 配方 | 产量 | 等级 |
|---|---|---|---|---|
| VF0 | **Virus Food 基础** | **Water5 + Milk5（600K起, 最佳625K, 过热700K）** | **15u** | 基础 |
| VF1 | **VF+Mutagen** | Mutagen1 + Virus Food1 | 1u | Lv2~4 |
| VF2 | **VF+Synaptizine** | Synaptizine1 + Virus Food1 | 1u | Lv4~4 |
| VF3 | **VF+Plasma** | Plasma1 + Virus Food1 | 1u | Lv5~5 |
| VF4 | **VF+Plasma+Synaptizine** | Synaptizine1 + Plasma VF1 | 2u (weak) | Lv6~6 |
| VF5 | **VF+Mutagen+Sugar** | Sugar1 + Mutagen VF1 | 2u | Lv4~4 |
| VF6 | **VF+Mutagen+Salglu** | Salglu Solution1 + Mutagen VF1 | 2u | Lv4~4 |
| VF7 | **VF+Uranium** | U1 + Virus Food1 | 1u | Lv7 |
| VF8 | **VF+Uranium+Plasma** | U5 + Plasma VF1 | 1u (unstable) | Lv7 |
| VF9 | **VF+Uranium+Gold+Plasma** | U10 + Gold10 + Plasma1 | 1u (stable) | **Lv8** |
| VF10 | **VF+Uranium+Silver+Plasma** | U10 + Silver10 + Plasma1 | 1u (stable) | **Lv8** |

> ⚠️ 修正: 基础 Virus Food 配方是 **Water5 + Milk5 加热 600K → 15u**，原文档03写"Mutagen + Synaptizine + Water 各1"错误。
> 新增 VF6 (Salglu 变体) 与 VF10 (Silver 变体)，stable 病毒食物有 Gold/Silver 两条路径。

## 9.2 病毒进化/退化

| 操作 | 配方 | 效果 |
|---|---|---|
| **进化** | Virus Food + Blood(催化) | Lv1→2 |
| **进化Lv2** | + Mutagen1 | Lv2→4 |
| **进化Lv4** | + Plasma1 | Lv4→6 |
| **进化Lv5** | + U1 | Lv5→6 |
| **退化 (rem_virus)** | + Synaptizine1 | 降一级 |
| **中性化 (neuter_virus)** | + Formaldehyde1 | 病毒失活(不能传播) |

## 9.3 病毒等级系统（mix_virus 12级）

> 源码依据：`code/modules/reagents/chemistry/recipes/others.dm` L233-315，共 **12 个 mix_virus 反应**（mix_virus 及 mix_virus_2 ~ mix_virus_12）。所有反应均以 **Blood 为催化剂**，作用于血液中的进阶病毒（advance disease）并使其进化（Evolve）到指定等级区间；产物为「进化后的病毒血」（基础 mix_virus 反应还会把血液标记为合成血）。

| # | 反应 | 配方（均需 Blood 1u 催化） | 病毒等级范围 |
|---|---|---|---|
| 1 | mix_virus | Virus Food 1u | Lv1~2 |
| 2 | mix_virus_2 | Mutagen 1u | Lv2~4 |
| 3 | mix_virus_3 | Plasma 1u | Lv4~6 |
| 4 | mix_virus_4 | Uranium 1u | Lv5~6 |
| 5 | mix_virus_5 | Mutagen Virus Food 1u（VF1） | Lv3 |
| 6 | mix_virus_6 | Mutagen VF（Sugar 版）1u（VF5） | Lv4 |
| 7 | mix_virus_7 | Plasma VF（弱化版）1u（VF4） | Lv5 |
| 8 | mix_virus_8 | Plasma VF 1u（VF3） | Lv6 |
| 9 | mix_virus_9 | Synaptizine VF 1u（VF2） | Lv1 |
| 10 | mix_virus_10 | Uranium VF 1u（VF7） | Lv6~7 |
| 11 | mix_virus_11 | Uranium VF（不稳定）1u（VF8） | Lv7 |
| 12 | mix_virus_12 | Uranium VF（稳定）1u（VF9/VF10） | **Lv8** |

**辅助反应**（同文件，不属于等级系列）：

| 反应 | 配方（+Blood 催化） | 效果 |
|---|---|---|
| rem_virus | Synaptizine 1u | 病毒退化一级（Devolve） |
| neuter_virus | Formaldehyde 1u | 病毒中性化，无法传播（Neuter） |

> 📌 说明：「12 级」指 12 个 mix_virus 进化反应；病毒实际等级上限为 **Lv8**（mix_virus_12 稳定铀病毒食物路径，Gold/Silver 双路线）。

---

# 第十卷 · 贫民窟化学（Ghetto Chemistry）

> **来源**: /tg/station Wiki 《Guide to Ghetto Chemistry》（2026-02 存档版）+ 本仓库源码逐条验证
> **定义**: 不依赖化学实验室（配药机/加热器/分装机），只用研磨机 + 打火机/喷灯 + 随手捡到的垃圾物品，提取基础原料合成化学品的方法。灰潮（Greytide）版的 Walter White 路线。

## 10.1 贫民窟化学工具

| 工具 | 说明 | 替代方案 |
|---|---|---|
| **研磨机 (All-in-One Grinder)** | 把物品磨成液体，贫民窟化学的核心 | 研钵和杵 (Mortar & Pestle) |
| **ChemMaster 3000** | 分离+瓶装化学品 | 蒸馏装置 (Distillation apparatus) |
| **打火机/火柴/喷灯/点火器** | 加热容器 | 加热温度取决于容器内试剂燃烧温度 |
| **恒温加热器 (Improvised chem heater)** | 用太空加热器改造，可精确控温 | 做复杂反应时最佳 |
| **木桶 (Barrels)** | 可装 300u 试剂，适合需要大量酸/稳定等离子来稳定的反应 | |
| **蒸馏装置** | 通过沸腾分离化学品（慢但可行） | ChemMaster 的替代 |
| **pH 手册 (pH booklets)** | 撕下一页用在烧杯上测 pH | 由 Universal Indicator 反应制作 |
| **温度计 (Thermometer)** | 加在任何装化学品的容器上显示温度 | |

**加热源燃料燃烧温度表**（放入燃烧器/加热器的试剂决定温度）：
| 燃料 | 特性 |
|---|---|
| **Ethanol** | 烧得很热且持久 |
| **Welding Fuel** | 烧得热且快 |
| **Oil** | 不如前两者热，但烧得久 |
| **Plasma** | 燃烧温度最高，消耗最快 |
| **Pyrosium** | 最高温且烧得慢，但燃烧器内需要氧气 |
| **Cryostylane** | 0K 燃烧，极冷，可用于冷却烧杯，需氧气 |
| **Cryoxadone** | 冷燃烧，可用于冷却烧杯 |
| **混合燃料** | 火焰温度 = 各试剂燃烧温度的加权和 |

## 10.2 研磨原料获取表（源码验证）

> ✅ = 已在 TianGuan13 源码 `grind_results()` / `food_reagents` 中确认存在
> ⚠️ = wiki 提到但本仓库源码未找到精确对应（可能已改版）

### 基础元素

| 化学品 | 研磨来源 | 产量 | 源码位置 |
|---|---|---|---|
| **Ammonia 氨** | 氨水晶 (Ammonia crystals) | 10u | `stacks/ammonia_crystals.dm` L11 ✅ |
| **Ash 灰** | 燃烧纸/油，用烧杯舀起 | 30u | 燃烧机制 ✅ |
| **Aluminium 铝** | 压扁的汽水罐 (crushed soda can) | 10u | `trash.dm` L137 ✅ |
| | Boritos 袋 | 1u | `trash.dm` L50 ✅ |
| | 拆解智能金属泡沫手雷 | 75u | `grenades/chem_grenade.dm` L457 ✅ |
| **Blood 血** | 死老鼠 | 血20u + 液体内脏5u | `mob/living/basic/vermin/mouse.dm` L356 ✅ |
| | 磨鱼 / 用注射器从猴子或人身上抽 | — | ✅ |
| **Bluespace Dust** | 蓝空间水晶 | 20u（人工的10u） | `mining/ores_coins.dm` ✅ |
| **Carbon 碳** | 煤 | 20u | `stacks/sheets/mineral.dm` L503 ✅ |
| | 钻石 | — | ✅ |
| | 骨头 | 20u | `stacks/sheets/sheet_types.dm` L850 ✅ |
| | 烟头 / 火把 (firebrand) | 2u | `cigarettes.dm` L172/957 ✅ |
| | 加热纤维素纤维 | — | `recipes/others.dm` L880 ✅ |
| | 烤焦焦糖（先热糖再热焦糖） | — | ✅ |
| **Cellulose 纤维素** | 木板 | 20u | `stacks/sheets/light.dm` ✅ |
| | 布 (cloth) | 20u | `sheet_types.dm` L563 ✅ |
| | 原棉 (raw cotton) | 20u | `sheet_types.dm` L632 ✅ |
| | 纸板 (cardboard) | 10u | ✅ |
| | 纸框架 (paper frame) | 20u | ✅ |
| | 竹屑 (bamboo shavings) | 10u | ✅ |
| | 玉米芯 (corn cob) | 10u | ✅ |
| | 包裹纸 (package wrapper) | 5u | `stacks/wrap.dm` L100 ✅ |
| | 纸 (paper) | 3u | ✅ |
| | 医用纱布 (gauze) | 2u | `stacks/medical.dm` L522 ✅ |
| | 太空现金 (space cash) | 10u | `stacks/cash.dm` L23 ✅ |
| **Chlorine 氯** | 盐电解（1u 液态电 + 5u 盐 → 2.5u 氯） | — | `recipes/others.dm` L711 ✅ |
| **Copper 铜** | 电缆/管道清洁器线圈 | 2u/根 | ✅ |
| | 青铜板 (bronze sheet) | 12u/张 | ✅ |
| **Ethanol 乙醇** | 喷罐 (spraycans) | 15u | ✅ |
| **Gold 金** | 金币 | 4u | ✅ |
| | Robust 金香烟（高级香烟贩售机） | 3u | ✅ |
| | 金条 | 20u | ✅ |
| **Hydrogen 氢** | 发光棒 (glowsticks) | 10u | `devices/flashlight.dm` L917 ✅ |
| | 电解水（1u 液态电 + 5u 水 → 3u 氢） | — | `recipes/others.dm` L700 ✅ |
| **Iodine 碘** | 墨盒 (toner cartridge) | 40u（大号90u） | `paperwork/photocopier.dm` L896 ✅ |
| | 照片 | 4u | ✅ |
| | 笔 | 1u | `items.dm` pen L61 ✅ |
| **Iron 铁** | 金属 (metal) | 20u | ✅ |
| | 等离子钢 (plasteel) | 20u | ✅ |
| | 棒棒糖 (lollipops) | 10u | `food_and_drinks/food/sweets.dm` L328 ✅ |
| | 电子元件 (electronics) | 10u | ✅ |
| | 强化玻璃 (reinforced glass) | 10u | `sheets/glass.dm` L175 ✅ |
| | 墨盒 | 10u | ✅ |
| | 笔 | 2u | ✅ |
| | 电池 (power cells) | 5u | `power/cell.dm` L33 ✅ |
| | 打火机 | 1u | `items/lighter.dm` L69 ✅ |
| **Lead 铅** | 铅管 (lead pipe) | 20u | `items/maintenance_loot.dm` L29 ✅ |
| | 铅酸电池 (lead-acid battery) | 铅15u + 酸15u + 水20u | `maintenance_loot.dm` L48 ✅ |
| | 高级战斗医疗包 | 10u | ✅ |
| **Lithium 锂** | 任何电池（含土豆电池） | 15u | `power/cell.dm` L33 ✅ |
| **Liquid Gibs 液体内脏** | 死老鼠 | 5u | `mouse.dm` L356 ✅ |
| | 地上的内脏 (gibs) 用烧杯舀 | 5u | ✅ |
| | 有机身体部件 | 5u | ✅ |
| | 自制肥皂 | 9u | `items/clown_items.dm` L68 ✅ |
| **Lye 碱液** | 肥皂 | 10u | `clown_items.dm` L39 ✅ |
| **Mercury 汞** | 气体分析仪 (gas analyzer) | 5u | `devices/scanners/gas_analyzer.dm` L47 ✅ |
| **Monkey Powder** | 猴立方 (monkey cube) | 30u | `food/monkeycube.dm` L7 ✅ |
| **Nicotine 尼古丁** | 香烟 | ~15u | `items/cigarettes.dm` L237 ✅ |
| | 雪茄 | 最高40u | ✅ |
| **Nitrogen 氮** | 灯泡 | 10u | ✅ |
| | 罐装空气 (canned air) | 24u | ✅ |
| **Nutriment 营养** | 任何食物 | — | ✅ |
| **Oil 油** | 打火机 | 5u | `lighter.dm` L69 ✅ |
| | 油渍 (oil spills) 用烧杯舀 | 30u | ✅ |
| **Organic Slurry 有机浆** | 死老鼠浸入 ≥3u（最优45u）焊接燃料 | — | ✅（Maint 毒必需） |
| **Oxygen 氧** | 罐装空气 | 6u | ✅ |
| | 发光棒 | 5u | `flashlight.dm` L917 ✅ |
| | 太空牌香烟 | 9u | `cigarettes.dm` L673 ✅ |
| | 电解水（1u 液态电 + 5u 水 → 2.5u 氧） | — | `recipes/others.dm` L700 ✅ |
| **Phenol 酚** | 发光棒 | 15u | `flashlight.dm` L917 ✅ |
| **Phosphorus 磷** | 火柴 | 2u | `cigarettes.dm` L35 ✅ |
| | 拆解催泪手雷 | 40u | `grenades/chem_grenade.dm` L550 ✅ |
| **Plasma 等离子** | 固态等离子板 | 20u | ✅ |
| | 等离子钢 (plasteel) | 20u | ✅ |
| | 等离子玻璃 | 10u | `sheets/glass.dm` L217 ✅ |
| | Nanotrasen 牌肥皂 | 10u | `clown_items.dm` L79 ✅ |
| **Potassium 钾** | 拆解催泪手雷 | 40u | `chem_grenade.dm` L549 ✅ |
| | 香蕉（产量随 potency 和基因变化） | 0.1/potency | `grown/banana.dm` L17 ✅ |
| **Peptides 肽** | 任何肝脏（鱼肝 5u） | 5u | `dna_infuser/organ_sets/fish_organs.dm` L532 ✅ |
| | 发霉/细菌披萨片（维修通道） | 3u | `food/pizza.dm` L437 ✅ |
| **Radium 镭** | 磨发光蘑菇 (glowshroom) | 0.1~0.2 | `grown/mushrooms.dm` L260 ✅ |
| **Silicon 硅** | 玻璃 | 20u | ✅ |
| | 电路板 | 20u | `circuitboards/circuitboard.dm` L33 ✅ |
| | 电子元件 | 10u | ✅ |
| | 灯泡 | 5u | ✅ |
| | 电池 | 5u | `power/cell.dm` L33 ✅ |
| | 分析仪 | 5u | `gas_analyzer.dm` L47 ✅ |
| **Silver 银** | 银币 | 4u | ✅ |
| | 银条 | 20u | ✅ |
| **Sodium Chloride 盐** | 盐瓶 (salt shakers) | 20u | ✅ |
| | 杯面 (cup ramen) | 3u | ✅ |
| | 牛肉干 (beef jerky) | 2u | ✅ |
| | 薯片 (chips) | 1u | ✅ |
| **Sodium 钠** | 盐电解（1u 液态电 + 5u 盐 → 2.5u 钠） | — | `recipes/others.dm` L711 ✅ |
| **Sugar 糖** | 任何甜食（糖果等） | — | ✅ |
| | 拆解催泪手雷 | 40u | `chem_grenade.dm` L551 ✅ |
| **Sulfur 硫** | 照明弹 (flare) | 15u | `devices/flashlight.dm` L491 ✅ |
| **Sulphuric Acid 硫酸** | 铅酸电池 | 15u | `maintenance_loot.dm` L48 ✅ |
| **Uranium 铀** | 铀板 | 20u | ✅ |
| | 粉碎的飞人肉块 | 3u | ✅ |
| **Water 水** | 水槽 / 水箱 | 最高1000u | ✅ |
| **Welding Fuel 焊接燃料** | 打火机 | 5u | `lighter.dm` L69 ✅ |
| | 焊接燃料罐 | 最高1000u | ✅ |

### 成品药研磨

| 药 | 研磨来源 | 产量 | 源码位置 |
|---|---|---|---|
| **Cryoxadone** | 医疗备用冷冻药烧杯（Metastation 维修酒吧也有） | — | ✅（燃料用） |
| **Lenturi** | 药膏 (ointment) | 10u | `stacks/medical.dm` L635 ✅ |
| **Fluorosulfuric acid** | 拆解智能金属泡沫手雷 | 25u | `chem_grenade.dm` L459 ✅ |
| **Fluorosurfactant** | 拆解清洁手雷 | 40u | ✅ |
| **Libital** | 淤伤包 (bruise pack) | 10u | `stacks/medical.dm` L381 ✅ |
| **Smart foaming agent** | 拆解智能金属泡沫手雷 | 25u | `chem_grenade.dm` L458 ✅ |
| **Salicyclic Acid** | 口香糖球 (gumballs) | 2u | `food/sweets.dm` L271 ✅ |
| **Strange Reagent** | 磨大猩猩立方 (gorilla cube) | 5u | ✅ |
| **Polypyr** | 医用缝合线 (medicated suture) | 1u | `stacks/medical.dm` L614 ✅ |
| **Omnizine** | 加热的 Donk Pocket | 6u | `food/donkpocket.dm` L40 ✅ |
| **Oxandrolone** | 口香糖球 (gumballs) | 2u | `food/sweets.dm` L271 ✅ |
| **Spaceacillin** | 缝合线 (sutures) | 2u | `stacks/medical.dm` L603 ✅ |
| **Space cleaner** | 拆解清洁手雷 | 10u | `chem_grenade.dm` L513 ✅ |

## 10.3 贫民窟化学实用流程

```
入门装备: 烧杯/汽水罐/瓶子 ×若干 + 桌子 ×2 + 研磨机 + ChemMaster
加热: 打火机/火柴/喷灯（温度由燃料决定）
  快热: Welding Fuel | 高温: Plasma | 持久: Oil | 冷却: Cryostylane/Cryoxadone
原料套路:
  死老鼠 = 血20u + 内脏5u（Yuck 配方容器 + Maint Tar 原料）
  打火机 = 油5u + 焊接燃料5u + 铁1u
  发光棒 = 酚15u + 氢10u + 氧5u
  催泪手雷拆解 = 磷40u + 钾40u + 糖40u
  盐瓶 = 盐20u（→ 电解 → 氯+钠）
  肥皂 = 碱液10u（→ Maint 链原料）
成品套路:
  淤伤包 → Libital 10u（C2 切伤药直接研磨!）
  药膏 → Lenturi 10u（C2 延长药直接研磨!）
  缝合线 → Spaceacillin 2u（抗生素）
  Donk Pocket 加热 → Omnizine 6u（万能药）
```

## 10.4 与化学系统的衔接

- 贫民窟化学的产物可直接进入第七卷基础原料链（如 Glycerol = Corn Oil3 + Acid1 需要酸，酸可从铅酸电池研磨获得）
- Maint 毒品链（第六卷 D09-D11）依赖有机浆（死老鼠+焊接燃料）与 Yuck（死老鼠容器）——完全为贫民窟路线设计
- 电解（7.3）需要液态电（Liquid Electricity），可从发电机/电力相关物品获取
- 火山口/垃圾堆 (maintenance loot) 是贫民窟原料的主要来源，含铅管、铅酸电池、氨水晶等

---

# 第十一卷 · 反应条件与杂质系统

## 11.1 反应条件速查

| 药 | 最佳温度 | 最佳pH | 过热效果 |
|---|---|---|---|
| **Oculine** | 400K | 4.8~8.5 | 闪光爆炸 |
| **Inacusiate** | 400K | 4.8~8.5 | 闪光 |
| **Mannitol** | 600K | — | 爆炸 |
| **Neurine** | 600K | — | 爆炸 |
| **Antihol** | 500K | 5~7 | 爆炸 |
| **Ephedrine** | 500K | 5~7 | 爆炸+毒气 |
| **基础医疗(通用)** | 700K | 10 | — |
| **Convermol (C2)** | 420K | 3.045~8.5 | 冲击波（有氧5伤/无氧2伤） |
| **Albuterol** | 600K | — | 900K过热 |
| **Salbutamol→Albuterol** | 610K | — | 980K过热 |
| **Albuterol→Convermol** | 920K | — | 990K过热爆炸烟 |
| **N₂O** | 550K | — | 575K过热→爆炸反应接管 |
| **Medical Speed Catalyst** | 500K | 5~6 | 800K过热→爆炸烟 |
| **Bonehurting Juice** | 450K | 5~9 | 900K过热 |

## 11.2 pH 系统

| 物质 | 类型 | pH |
|---|---|---|
| 纯水 | 中性 | 7.0 |
| 盐酸(HCl) | 强酸 | ~1 |
| 硫酸(H₂SO₄) | 强酸 | ~1 |
| 氢氧化钠(Lye) | 强碱 | ~12 |
| 氨水(Ammonia) | 弱碱 | ~11 |
| Psicodine | 弱碱 | 9.12 |
| Ondansetron | 弱碱 | 10.6 |
| Naloxone | 弱酸 | 4.0 |

**pH控制手段**: Basic Buffer（推高）/ Acidic Buffer（拉低）/ Ionic Modulator（催化剂）

## 11.3 反应热

| 类型 | 说明 |
|---|---|
| 放热(Thermic +) | 反应释放热量，可能过热（如 N₂O thermic=35 给15K余量） |
| 吸热(非Thermic/-) | 反应吸收热量，可能需要加热（如 Bonehurting Juice thermic=-400 强吸热） |

## 11.4 杂质系统

- 纯度 < `purity_min` → 产生副产物（`overly_impure()`）
- 纯度 < `inverse_chem_val` (0.25) → 代谢为逆化学物
- 过杂后果因配方而异：C2 药炸、N₂O 泄漏半量气体、Convermol 清除双倍反应物

---

# 第十二卷 · 修正与勘误

## 12.1 原文档错误（本次修正）

| # | 位置 | 原文 | 实际（源码） |
|---|---|---|---|
| 1 | 化学01 #19 | Convermol(经Albuterol) = Albuterol4 + SalAcid0.5 + NH₃0.5 催化H₂SO₄ | **Albuterol5 催化 Flu Acid1, 900K → Convermol1 + Li3+Al3+Br3**（albuterol_to_convermol） |
| 2 | 化学01 #18 | Albuterol 无温度条件 | 需 **400K** 起（最佳600K, 过热900K） |
| 3 | 化学03 病毒 | Virus Food = Mutagen+Synaptizine+Water 各1 | **Water5 + Milk5, 600K → 15u** |
| 4 | 化学03 地毯 | 24色 | 实际 **23色**（基础1+普通9+霓虹13） |
| 5 | 化学03 VF8-10 | U10+Ag10+Plasma1 Lv8 等标注 | 实际 3 种铀病毒食物 + Gold/Silver 两路 stable |

## 12.2 原文档"待查/配方内"补齐

| 位置 | 药 | 补齐配方 |
|---|---|---|
| 化学01 #38 | Psicodine | Mannitol2 + Water2 + Impedrezene1 → 5u |
| 化学01 #39 | Granibitaluri | Salt1 + C1 + Acid1（催化 Fe5）→ 3u |
| 化学01 #40 | Ondansetron | Oil1 + N1 + O1（催化 Ethanol3, pH≤11）→ 3u |
| 化学01 #41 | Naloxone | Morphine1 + H₂O₂1 + Br1 + Ethanol1 → 4u |
| 化学02 S-serie | 多个"特定" | 见第八卷各表 |
| 化学03 病毒进化 | 12级 | 源码为 mix_virus 至 mix_virus_12 共 12 个反应 |

## 12.3 新增发现（原文档完全未覆盖）

1. **医疗堆叠物 3 种**: Medsuture / Medmesh / Poultice（即时生成物品）
2. **Albuterol 转换链**（双向 3 个反应）
3. **Seraka Destroy**: Seraka Extract1 + Lye1 → Sugar1
4. **Teslium 全系**: 配方 + 3档闪电 + Energized Jelly
5. **Pyrosium**: 配方 + 遇氧自燃
6. **Maint 毒品链**: Tar → Sludge → Powder（3 级）
7. **Moon Rock / Blastoff / Saturnx**（TianGuan 新毒品）
8. **Nova 医疗 6 种**: System Cleaner / Liquid Solder / Nanite Slurry / Taste Suppressor ×2 / Dermagen
9. **22 种基础原料配方**（第七卷）
10. **12 种固化/材料化反应**（7.2）
11. **Slime Jelly / Slime Extractification / Metalgen Imprint / Gravitum**
12. **Carnivorous Blood 配对与喂食**
13. **边角补齐**（2026-08 核查）：Adminordrazine/Quantum Medicine（管理员药）、Gatfruit Phytotoxin（枪果毒，无肝伤）、Astrotame（零卡代糖）、Plantnutriment 全系 5 种（EZ/Left4Zed/RobustHarvest/EnduroGrow/LiquidEarthquake 水培肥料数值）、Unholy Water（邪水，邪教徒/普通人双效）、Hell Water（地狱水，火焚）

---

# 附录A · 代码路径索引

| 文件 | 行数 | 内容 |
|---|---|---|
| `recipes/medicine.dm` | 447 | 基础医疗 41 种 + 堆叠物 3 种 + Albuterol 链 |
| `recipes/cat2_medicines.dm` | 357 | C2 医疗 12 种 |
| `recipes/pyrotechnics.dm` | 667 | 爆炸品 40 种（含 Teslium/Pyrosium/N₂O/灭火泡沫/爱国者过载） |
| `recipes/toxins.dm` | 407 | 毒素 21 种 |
| `recipes/drugs.dm` | 208 | 毒品 14 种（含 Maint 链/月球石/土星X） |
| `recipes/others.dm` | 1,167 | 基础原料/泡沫/清洁/工业/生物/电解/地毯23色/病毒/特殊 |
| `recipes/catalysts.dm` | 98 | 催化剂 6 种 |
| `recipes/reaction_agents.dm` | 123 | 反应剂 6 种 |
| `recipes/special.dm` | 320 | 随机配方 3 种（Secret Sauce/Metalgen/Gorgium） |
| `recipes/slime_extracts.dm` | 589 | **史莱姆萃取 52 反应全录（见 7.2 节）** |
| `reagents/medicine_reagents.dm` | 2,010 | 医疗试剂效果 |
| `reagents/cat2_medicine_reagents.dm` | 676 | C2 医疗试剂效果 |
| `reagents/toxin_reagents.dm` | 1,765 | 毒素试剂效果 |
| `reagents/drug_reagents.dm` | 993 | 毒品试剂效果（含 Saturnx L712-776） |
| `reagents/pyrotechnic_reagents.dm` | 525 | 爆炸试剂效果 |
| `reagents/other_reagents.dm` | 3,602 | 其他试剂（水/元素/工业/疫苗） |
| `reagents/food_reagents.dm` | 1,393 | 食物试剂（营养/果汁/酒原料）→ **已迁移至《食物饮料酒精系统百科》第九卷** |
| `reagents/drinks/` | — | 饮料/酒精试剂 |
| `reagents/atmos_gas_reagents.dm` | 149 | 气体试剂 |
| `reagents/catalyst_reagents.dm` | 85 | 催化剂试剂 |
| `reagents/reaction_agents_reagents.dm` | 192 | 反应剂试剂 |
| `reagents/impure_reagents.dm` + `impure_*` | 125+ | 杂质试剂 |
| `reagents/unique/eigenstasium.dm` | — | 量子态化学 |
| `modular_nova/.../recipes/medicine.dm` | 31 | Nova 医疗 6 种 |
| `modular_nova/modules/synths/code/reagents/reagents.dm` | — | 合成体试剂定义 |
| `holder/reagents.dm` | 373 | 试剂基类 |
| `holder/holder.dm` | 820 | 试剂容器 |
| `holder/mob_life.dm` | 215 | 代谢核心 metabolize() |
| `holder/reactions.dm` | 276 | 化学反应引擎 |
| `machinery/pandemic.dm` | 387 | 大流行控制台 |

---

# 附录B · 化学品数量统计

| 分类 | 数量 |
|---|---|
| **基础医疗** (medicine) | 41 种（含 Adminordrazine/Quantum 2 种管理药） |
| **C2 医疗** (cat2) | 12 种 |
| **医疗堆叠物** (stacks) | 3 种 |
| **Nova 医疗** | 6 种 |
| **爆炸品** (pyrotechnics) | 40 种 |
| **毒素** (toxins) | 21 种 + Gatfruit 枪果毒 |
| **毒品** (drugs) | 14 种 |
| **病毒食物** (virus food) | 11 种 |
| **泡沫** | 9 种 + 3 发泡剂 |
| **清洁/农业** | 7 种 |
| **基础原料** | 30+ 种（含 Astrotame/Unholy Water） |
| **肥料** (plantnutriment) | 5 种（EZ/Left4Zed/RobustHarvest/EnduroGrow/LiquidEarthquake） |
| **固化/材料化** | 11 种 |
| **生物** | 13 种 |
| **地毯** | **23 色** |
| **反应剂** (reaction_agents) | 6 种 |
| **特殊** (special) | 20+ 种 |
| **催化剂** (catalysts) | 6 种 |
| **随机配方** | 3 种（7天/3天/7天重置） |
| **基础元素** | ~60+ 种 |
| **总计可合成的化学品** | **~310+ 种**（含 Nova 扩展） |
