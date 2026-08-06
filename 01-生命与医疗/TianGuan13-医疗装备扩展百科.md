# TianGuan13 医疗装备扩展百科

> **项目**: TianGuan13（Nova Sector 分支 → /tg/station）
> **代码**: `modular_nova/modules/deforest_medical_items/`（2,161 行，15 个 .dm）+ `modular_nova/modules/hyposprays/`（1,233 行，5 个 .dm）
> **范围**: **治愈堆叠物品**（9 种）· **Deforest 自动注射器**（19 支）· **医疗站与生物打印**（29 项设计）· **存储容器与医疗包**（11 种）· **DeForest Med-Vend** 售货机 · **货运包**（7 种）· **Hypospray Mk.II 系统**（9 型号+1 升级套件）· **药瓶 Hypovial**（3 规格+16 预装）· **工具包**（9 种）· **车床设计与售货机**
> **来源**: 由 `deforest_medical_items` 与 `hyposprays` 两模块合并而成（2026-08）

## 目录

- [第一卷 · 治愈堆叠物品](#第一卷--治愈堆叠物品)（9 种：夹板/凝血/药膏/纱布/合成体修复）
- [第二卷 · Deforest 自动注射器](#第二卷--deforest-自动注射器)（14 合法 + 5 非法 + 2 违禁药物）
- [第三卷 · 医疗站与生物打印机](#第三卷--医疗站与生物打印机)（壁挂医疗站 + 12 医疗设计 + 15 血袋设计）
- [第四卷 · 存储容器与医疗包](#第四卷--存储容器与医疗包)（药瓶 2 + 医疗包 9 + 工具包 3）
- [第五卷 · DeForest Med-Vend 售货机](#第五卷--deforest-med-vend-售货机)
- [第六卷 · 货运包](#第六卷--货运包)（7 种）
- [第七卷 · Hypospray Mk.II 系统](#第七卷--hypospray-mkii-系统)（9 型号 + 升级套件）
- [第八卷 · 药瓶 Hypovial](#第八卷--药瓶-hypovial)（3 规格 + 16 预装）
- [第九卷 · 工具包 Hypospray Kits](#第九卷--工具包-hypospray-kits)（9 种）
- [第十卷 · 车床设计与售货机](#第十卷--车床设计与售货机)（8 设计）
- [附录 · 代码路径索引](#附录--代码路径索引)

---

# 第一卷 · 治愈堆叠物品

**代码**: `modular_nova/modules/deforest_medical_items/code/healing_stack_items.dm`（197 行）+ `code/synth_healing.dm`（199 行）+ `code/vulnerable_status_effect.dm`（28 行）+ `code/treatment_zone_projector.dm`（48 行）

## 1.1 皮下夹板敷贴器系统（wound_recovery）

**源码**: `healing_stack_items.dm`

**核心常量**：
| 常量 | 值 | 说明 |
|---|---|---|
| `INSTANT_WOUND_HEAL_LIMB_DAMAGE` | 25 | 治疗后肢体瞬间受到的钝击伤害 |
| `INSTANT_WOUND_HEAL_STAMINA_DAMAGE` | 80 | 治疗后承受的耐力损失 |

### ① Subdermal Splint Applicator（皮下夹板敷贴器）`/obj/item/stack/medical/wound_recovery`

- **描述**：一卷柔性材料，单面密布微型注射器；内含纳米机械在受损骨骼周围形成皮下自愈夹板。方便美观且愈合快速，但治疗后数分钟内宿主对新伤害格外脆弱。
- **图标**：`stack_items.dmi` / `subsplint`；手持 `sampler`
- **数值**：`max_amount = 3`、`amount = 3`、`self_delay = 10 秒`、`other_delay = 5 秒`、`novariants = TRUE`、`custom_price = PAYCHECK_COMMAND × 2.5`
- **适用伤口**：`/datum/wound/blunt/bone`（钝击·骨骼）、`/datum/wound/muscle`（肌肉）
- **治疗音效**：`sound/items/duct_tape/duct_tape_rip.ogg`
- **机制**：
  - 目标无对应肢体 → 拒绝；肢体无伤口 → 拒绝（"好问题，可惜没有"）；已有 `vulnerable_to_damage` 状态 → 拒绝
  - 伤口已被扫描（`TRAIT_WOUND_SCANNED`）→ 治疗耗时 × 0.5
  - 成功后：移除伤口 → 播放音效 → 患者尖叫（除非 `TRAIT_ANALGESIA` 或 `causes_pain = FALSE`）→ 触发心情事件 `rapid_wound_healing`（-3 心情，5 分钟）
  - 肢体承受 25 点钝击伤害（`CANT_WOUND` 不可造成新伤口）→ 耐力损失 80 → 施加 `vulnerable_to_damage` 状态 → 消耗 1 单位

### ② Rapid Coagulant Applicator（速效凝血敷贴器）`/obj/item/stack/medical/wound_recovery/rapid_coagulant`

- **描述**：装有速效凝血剂的小装置，用于出血部位时几乎瞬间止住全部流血；快速凝血可能带来暂时易伤。
- **图标**：`clotter`；手持 `implantcase`
- **适用伤口**：`/datum/wound/slash/flesh`（切割·血肉）、`/datum/wound/pierce/bleed`（穿刺·流血）
- **特效**：治愈后追加注入 **5u `coagulant/fabricated`（人造凝血素）**

### ③ Red Sun Balm（红日药膏）`/obj/item/stack/medical/ointment/red_sun`

- **描述**：红日之下专治可怕烧伤的知名药膏品牌。至于"哪个红日"？连生产商自己都不确定。
- **图标**：`balm`；手持 `bandage`
- **数值**：`amount = 12`、`max_amount = 12`、`self_delay = 4 秒`、`other_delay = 2 秒`、`custom_price = PAYCHECK_LOWER × 1.5`
- **疗效**：`heal_burn = 5`、`heal_brute = 5`、`flesh_regeneration = 5`、`sanitization = 3`
- **研磨产出**：`oxandrolone = 3u`
- **特效**：治愈后追加注入 **2u `lidocaine`（利多卡因）**

### ④ Sealed Aseptic Gauze（密封无菌纱布）`/obj/item/stack/medical/wrap/gauze/sterilized`

- **描述**：经特殊处理完全无菌的弹性材料卷，塑料密封双重保险。治疗烧伤效果极佳，但因尺寸小巧，作为骨骼伤口包扎则差强人意。
- **图标**：`burndaid`
- **数值**：`max_amount = 6`、`amount = 6`、`splint_factor = 1.2`、`burn_cleanliness_bonus = 0.1`、`custom_price = PAYCHECK_LOWER × 1.5`
- **特效**：治愈后追加注入 **5u `sterilizine`（消毒液）** 并对患者进行 TOUCH 暴露

### ⑤ Coagulant-F Packet（凝血素-F 敷包）`/obj/item/stack/medical/suture/coagulant`

- **描述**：一包人造凝血剂。封合伤口不如其他凝血手段高效，但强于普通缝合线——代价是修复的实际损伤更少。
- **图标**：`clotter_slow`
- **数值**：`amount = 12`、`max_amount = 12`、`repeating = FALSE`、`heal_brute = 0`、`stop_bleeding = 2`、`custom_price = PAYCHECK_LOWER × 1.5`

## 1.2 合成体修复物品

**源码**: `synth_healing.dm`

### ⑥ Robotic Repair Spray（机器人修复泡沫）`/obj/item/stack/medical/wound_recovery/robofoam`

- **描述**：针尖泡沫枪，装有高级合成泡沫，可迅速填充并稳定合成体的结构性损伤。泡沫硬化期间受损区域易受进一步伤害。
- **图标**：`robofoam`；手持 `implantcase`；音效 `sound/effects/spray.ogg`
- **数值**：`max_amount = 2`、`amount = 2`、`causes_pain = FALSE`
- **适用伤口**：`/datum/wound/blunt/robotic`（机械体钝击）
- **特效**：治愈后注入 **5u `nanite_slurry`（纳米泥浆）+ 5u `coagulant/fabricated`**

### ⑦ Premium Robotic Repair Spray（高级机器人修复泡沫）`/obj/item/stack/medical/wound_recovery/robofoam_super`

- **描述**：同上的高级版，可修复几乎任何类型的合成体损伤。
- **图标**：`robofoam_super`
- **数值**：`max_amount = 2`、`amount = 2`、`causes_pain = FALSE`
- **适用伤口**：`blunt/robotic`、`muscle/robotic`（机械体肌肉）、`electrical_damage`（电损伤）、`burn/robotic`（机械体烧伤）
- **特效**：治愈后注入 **5u `coagulant/fabricated` + 5u `nanite_slurry` + 5u `dinitrogen_plasmide`**

### ⑧ Robotic Repair Patches（机器人修复贴片）`/obj/item/stack/medical/synth_repair`

- **描述**：密封小纳米虫群贴片 + 导电凝血试剂，修复小量合成体损伤。
- **图标**：`synth_patch`；**数值**：`amount = 3`、`max_amount = 3`、`self_delay = 4 秒`、`other_delay = 2 秒`
- **前置检查**：目标必须为**机械肢体**（`IS_ROBOTIC_LIMB`）；患者体内 `nanite_slurry ≥ 2u` 时拒绝
- **研磨产出**：`nanite_slurry = 10u`、`dinitrogen_plasmide = 5u`、`coagulant/fabricated = 10u`
- **特效**：治愈后把研磨产出全部注入患者

### ⑨ Cybernetic Repair Paste（赛博格修复膏）`/obj/item/cybernetic_repair_paste`

- **描述**：修复膏涂抹笔，可在**器官操作手术**中直接修复赛博格器官。素材致谢 [@splat1125](https://github.com/splat1125)。
- **图标**：`cyberpaste`（耗尽后变 `cyberpaste_spent`）；`w_class = SMALL`
- **数值**：`repair_amount = 25`（每次修复 25 点器官损伤）、`uses = 5`（可用 5 次）、修复需 `do_after 5 秒`
- **机制**：可点击目标人体选择器官（需手术已进入"切开皮肤+切割器官"阶段，胸腔/大脑还需骨锯）修复；也可直接对掉落/持有的机械器官物品使用

## 1.3 易伤状态效果（Vulnerable To Damage）

**源码**: `vulnerable_status_effect.dm`

| 属性 | 值 |
|---|---|
| `/datum/status_effect/vulnerable_to_damage` | id = `"vulnerable_to_damage"` |
| 持续 | **5 分钟** |
| `remove_on_fullheal` | TRUE（满血自动移除） |
| `damage_resistance_subtraction` | **50**（减伤百分比 -50） |
| `bleed_modifier_addition` | **+1**（流血系数 +1） |
| 提示 | "身体修复自身期间你将承受比平时更多的伤害！" |

## 1.4 紧急治疗区投影仪

**源码**: `treatment_zone_projector.dm`

- **Treatment Zone Indicator（治疗区指示器）** `/obj/structure/holosign/treatment_zone_warning`：3×3 格巨型发光全息警告牌（96×96 图标，`BELOW_OBJ_LAYER`，pixel ±32）
- **Emergency Treatment Zone Projector（紧急治疗区投影仪）** `/obj/item/holosign_creator/medical/treatment_zone`：`creation_time = 1 秒`、`max_signs = 1`
- **科技设计** `/datum/design/treatment_zone_projector`：id `"treatment_zone_projector"`，`PROTOLATHE | AWAY_LATHE`，材料铁×5/玻璃×5/银×1（SMALL 单位），分类 `RND_CATEGORY_TOOLS + RND_SUBCATEGORY_TOOLS_MEDICAL`，`DEPARTMENT_BITFLAG_MEDICAL`
- **挂载点**：`techweb_node/holographics` 科技树解锁；医疗机器人（medical borg）基础模块自带

---

# 第二卷 · Deforest 自动注射器

**代码**: `modular_nova/modules/deforest_medical_items/code/injectors.dm`（231 行）+ `code/illegal_injectors.dm`（75 行）+ `code/chemicals/twitch.dm`（290 行）+ `code/chemicals/demoneye.dm`（150 行）

## 2.1 基类机制

**Non-Functional Deforest Autoinjector（无功能 DeForest 自动注射器）** `/obj/item/reagent_containers/hypospray/medipen/deforest`

- **容量**：`volume = 25u`（初始化时 `amount_per_transfer_from_this = volume`）
- **价格**：`custom_price = PAYCHECK_COMMAND`
- **注射他人延迟**：`inject_others_time = 1.5 秒`（可被 `CHEM_INTERACT_DELAY` 影响）
- **机制**：注射他人必须完成 1.5 秒动作条；始终记录管理员日志（尝试注入与成功注入各一次）；支持 `infinite`（无限）与 `stealthy`（无声）模式

## 2.2 合法注射器全录（14 支）

| # | 注射器（英文名） | 中文名 | 试剂配方（容量 25u） |
|---|---|---|---|
| 1 | **Occuisate Sensory Restoration Injector** | 感官恢复注射器 | `inacusiate`=7 · `oculine`=7 · `impurity/inacusiate`=3 · `inverse/oculine`=3 · `lipolicide`=5 |
| 2 | **Adrenaline Injector** | 肾上腺素注射器 | `synaptizine`=5 · `inaprovaline`=5 · `determination`=10 · `histamine`=5 |
| 3 | **Morpital Regenerative Stimulant Injector** | 再生兴奋剂注射器 | `morphine`=5 · `omnizine/protozine`=15 · `staminatoxin`=5 |
| 4 | **Lipital Regenerative Stimulant Injector** | 再生兴奋剂注射器（钝击特化） | `lidocaine`=5 · `omnizine`=5 · `c2/probital`=10 |
| 5 | **Meridine Antidote Injector** | 解毒剂注射器 | `c2/multiver`=10 · `potass_iodide`=10 · `nitrous_oxide`=5 |
| 6 | **Synephrine Emergency Stimulant Injector** | 紧急兴奋剂注射器 | `epinephrine`=10 · `synaptizine`=5 · `synaphydramine`=5（价格 `PAYCHECK_COMMAND × 2.5`） |
| 7 | **Calopine Emergency Stabilizant Injector** | 紧急稳定剂注射器 | `atropine`=10 · `coagulant/fabricated`=5 · `salbutamol`=5 · `staminatoxin`=5 |
| 8 | **Coagulant-S Injector** | 凝血素-S 注射器 | `coagulant`=5 · `salglu_solution`=15 · `impurity`=5 |
| 9 | **Krotozine Manipulative Stimulant Injector** | 操控性兴奋剂注射器 | `ondansetron`=5 · `kronkaine`=5 · `omnizine/protozine`=10 · `maint/tar`=5（价格 `PAYCHECK_COMMAND × 2.5`） |
| 10 | **Lepoturi Burn Treatment Injector** | 烧伤治疗注射器 | `mine_salve`=5 · `leporazine`=5 · `c2/lenturi`=10 · `staminatoxin`=5 |
| 11 | **Psifinil Personal Recovery Injector** | 个人恢复注射器 | `modafinil`=10 · `psicodine`=10 · `leporazine`=5 |
| 12 | **Halobinin Soberant Injector** | 解酒解毒注射器 | `haloperidol`=5 · `antihol`=5 · `higadrite`=5 · `silibinin`=5 |
| 13 | **Synthetic Cleaner Autoinjector** | 合成体清洁注射器 | `system_cleaner`=15 · `dinitrogen_plasmide`=5（机器人用，清除毒素/合成体化学物但短暂减速） |
| 14 | **Synthetic Smart-Solder Autoinjector** | 合成体智能焊锡注射器 | `liquid_solder`=15 · `dinitrogen_plasmide`=5（机器人用，修复处理器核心脑损伤但短暂减速） |

## 2.3 非法/违禁注射器全录（5 支）

| # | 注射器（英文名） | 中文名 | 试剂配方（容量 25u） | 价格 |
|---|---|---|---|---|
| 15 | **TWitch Sensory Stimulant Injector** | TWitch 感官兴奋剂 | `drug/twitch`=10 · `maint/tar`=5 · `silibinin`=5 · `leadacetate`=5 | `PAYCHECK_COMMAND × 3.5` |
| 16 | **DemonEye Steroid Injector** | DemonEye 类固醇注射器 | `drug/demoneye`=10 · `maint/sludge`=10 · `leadacetate`=5 | `PAYCHECK_COMMAND × 3.5` |
| 17 | **Aranepaine Combat Stimulant Injector** | 战斗兴奋剂注射器 | `aranesp`=5 · `kronkaine`=5 · `pumpup`=5 · `diphenhydramine`=5 · `impurity`=5 | `PAYCHECK_COMMAND × 2.5` |
| 18 | **Pentibinin Normalizant Injector** | 归一化注射器 | `c2/penthrite`=5 · `polypyr`=5 · `silibinin`=5 · `omnizine`=5 · `inverse/healing/tirimol`=5 | `PAYCHECK_COMMAND × 2.5` |
| 19 | **Synalvipitol Muscle Stimulant Injector** | 肌肉兴奋剂注射器 | `mine_salve`=5 · `synaptizine`=10 · `muscle_stimulant`=5 · `impurity`=5 | `PAYCHECK_COMMAND × 2.5` |

> 违禁清单依据：TWitch/DemonEye 属违禁药物注射器；aranepaine 为"警棍无法战胜的耐力鸡尾酒"；pentibinin 本身不违法但混合试剂极其危险；synalvipitol 使人对疲劳/肌肉衰竭近乎免疫。

## 2.4 TWitch 违禁药物

**源码**: `chemicals/twitch.dm`

### 合成反应 `/datum/chemical_reaction/twitch`

- **产出**：`drug/twitch = 10u`（17u 原料 → 10u 产物）
- **原料**：`impedrezene`=5 · `bluespace`=10 · `liquidelectricity/enriched`=2
- **标签**：`REACTION_TAG_EASY | DRUG | ORGAN | DAMAGING`；`mob_react = FALSE`

### 药物 TWitch `/datum/reagent/drug/twitch`

| 属性 | 值 |
|---|---|
| 名称/颜色/味道 | "TWitch" / `#c22a44` / 电视雪花噪点（television static） |
| 代谢率 | `0.65 × REAGENTS_METABOLISM`；pH=3 |
| 过量阈值 | **15u** |
| 成瘾 | `stimulants = 40` |
| 常量 | `CONSTANT_DOSE_SAFE_LIMIT = 60`（秒） |

- **起源**：Plutonian 人袭击时使用的药物，因"现实解离 + 急性心脏病"未获广泛使用；可故意过量增强效果。
- **生效效果**：移动速度修正 `-0.4`（`/datum/movespeed_modifier/reagent/twitch`）→ 行动/攻击速度 ×0.7 → 声音环境变 DIZZY → 听到的话语染成绿色 → 移动留下残影（`twitch_afterimage`，0.75 秒变色残影）→ 近战攻击者/被投掷物击中会**踉跄**（+6 秒封顶 12 / +3 秒封顶 3）→ 屏幕绿色滤镜 + 径向模糊
- **持续机制**：`constant_dose_time` 累加；机械体或 `TRAIT_STABLEHEART` 者体温按 `3.8 ×` 加热（类似 herignis）；有机体心脏每秒器官损失 `0.77u`；与 `kronkaine` 同服 → 强制感染 `adrenal_crisis`（肾上腺危象）疾病
- **过量效果**：激活**子弹闪避**（被击倒/踉跄/脚镣时无效，闪避时高斯模糊 0.5 秒）→ 再提速 ×0.7 → 屏幕滤镜变紫色动画 → 每秒抖动 7.7 秒 → 毒素损伤 0.77/秒 → 5% 概率咳血 → 10% 概率相位波纹模糊
- **代谢结束**：`constant_dose_time < 60 秒` → 仅耐力损失（= 累计时长）；`≥ 60 秒` → 咳血 + 心脏器官损失 `0.3 × dose_time`（除非 `TRAIT_TWITCH_ADAPTED`），移除滤镜

## 2.5 DemonEye 违禁药物

**源码**: `chemicals/demoneye.dm`

### 合成反应 `/datum/chemical_reaction/demoneye`

- **产出**：`drug/demoneye`=10 · `impurity/healing/medicine_failure`=10 · `impurity`=5（25u 原料 → 25u 产物）
- **原料**：`ephedrine`=5 · `blood`=15 · `stable_plasma`=5
- **标签**：`REACTION_TAG_EASY | DRUG | ORGAN | DAMAGING`；`mob_react = FALSE`

### 药物 DemonEye `/datum/reagent/drug/demoneye`

| 属性 | 值 |
|---|---|
| 名称/颜色/味道 | "DemonEye" / `#af00be` / 工业穿梭机燃料（industrial shuttle fuel） |
| 代谢率 | `0.65 × REAGENTS_METABOLISM`；pH=7 |
| 过量阈值 | **15u** |
| 成瘾 | `stimulants = 50` |
| 代谢特质 | `TRAIT_UNNATURAL_RED_GLOWY_EYES`（红眼）· `TRAIT_NOSOFTCRIT`（免疫软濒死）· `TRAIT_NOHARDCRIT`（免疫硬濒死）· `TRAIT_FEARLESS`（无畏）· `TRAIT_ANALGESIA`（无痛） |
| 常量 | `CONSTANT_DOSE_SAFE_LIMIT = 60`、`METABOLISM_END_LIMB_DAMAGE = 20` |

- **起源**：火星研发的增强药物，深受帮派与法外之徒喜爱；滥用会导致可怕成瘾与身体损伤。
- **生效效果**：双眼变血 cult 红色（记录并恢复原色）→ 声音环境 PSYCHOTIC → 屏幕红色滤镜 + 角向模糊（size 4）→ 心情事件 `stimulant_heavy/sundowner`（"I'M FUCKING INVINCIBLE!!!!"）→ 每秒恢复耐力 7.7、睡眠 -1.5 秒、困倦 -3.8 → 25% 概率心跳声 + 3 秒红闪 → 5% 概率随机器官受损 3 点
- **器官池**：脑/阑尾/肺/心/肝/胃（`hurt_that_mans_organs`）
- **联动**：与 TWitch 同服 → 直接施加 `heart_attack`（心脏病发）状态
- **代谢结束**：`< 60 秒` 或无血液 → 仅提示；`≥ 60 秒` → 随机肢体承受**致命切割伤口** + 20 点钝击伤害 + 裂开特效（cleave）；若过量再施加 `vulnerable_to_damage`
- **过量效果**：抖动 7.7 秒/秒 · 10% 概率随机器官受损 5 点并呕血

---

# 第三卷 · 医疗站与生物打印机

**代码**: `modular_nova/modules/deforest_medical_items/code/medstation.dm`（56 行）+ `code/medstation_designs/medical.dm`（74 行）+ `code/medstation_designs/blood.dm`（92 行）

## 3.1 壁挂医疗站

### Wall Med-Station（壁挂医疗站）`/obj/machinery/biogenerator/medstation`

- **描述**：前沿哨站与殖民地常见的高级机器，可将有机植物物质转化为各种紧急医疗物资与注射器。
- **数值**：`efficiency = 1`、`productivity = 1`、`circuit = null`、`anchored = TRUE`、`density = FALSE`
- **产品分类**：`RND_CATEGORY_DEFOREST_MEDICAL` + `RND_CATEGORY_DEFOREST_BLOOD`
- **交互**：撬棍被 `tool_blocker` 屏蔽；**扳手**拆解（1 秒）→ `deconstruct(TRUE)`；拆解后掉落 `repacked_type`
- **朝向**：`MAPPING_DIRECTIONAL_HELPERS(..., 29)` 全方位贴墙

### Unmounted Wall Med-Station（未安装墙框）`/obj/item/wallframe/frontier_medstation`

- **描述**：可整齐安装在墙上的生物打印机框体。
- **数值**：`w_class = NORMAL`、`pixel_shift = 29`、`result_path = /obj/machinery/biogenerator/medstation`
- **造价**：铁×5 + 银×3 + 金×1（SHEET 单位）

## 3.2 医疗设计全录（12 项，生物质打印）

**源码**: `medstation_designs/medical.dm`（基类 `/datum/design/biogen/deforest_medical`：生物质 50，`build_path = /obj/item/stack/medical`）

| # | 设计名 | id | 生物质 | 产物 |
|---|---|---|---|---|
| 1 | **Red Sun Balm** 红日药膏 | `organic_sun_balm` | 50 | `ointment/red_sun` |
| 2 | **Sealed Aseptic Guaze** 密封无菌纱布 | `organic_gauze` | 50 | `wrap/gauze/sterilized` |
| 3 | **Coagulant-F Packet** 凝血素-F 敷包 | `organic_coagulant_pack` | 50 | `suture/coagulant` |
| 4 | **Ointment** 药膏 | `organic_ointment` | 50 | `ointment` |
| 5 | **Bruise Packs** 淤伤敷包 | `organic_bruise_packs` | 50 | `bruise_pack` |
| 6 | **Amollin Painkiller** 阿莫林止痛药 | `organic_printer_amollin_pill` | 50 | `applicator/pill/amollin` |
| 7 | **First Aid Bandage** 急救绷带 | `organic_bandaid` | **100** | `stack/medical/bandage` |
| 8 | **Robotic Repair Patch** 机器人修复贴片 | `organic_repair_patch` | **100** | `synth_repair` |
| 9 | **Robotic Repair Spray** 机器人修复泡沫 | `organic_repair_foam` | **100** | `wound_recovery/robofoam` |
| 10 | **Bone Gel** 骨胶 | `organic_bone_gel` | **100** | `bone_gel` |
| 11 | **Surgical Tape** 手术胶带 | `organic_surgical_tape` | **100** | `wrap/sticky_tape/surgical` |
| 12 | **Bottle Of Protozine** 原虫素瓶 | `organic_protozine` | **200** | `cup/glass/waterbottle/large/protozine` |

## 3.3 血袋设计全录（15 项，生物质打印）

**源码**: `medstation_designs/blood.dm`（基类 `/datum/design/biogen/organic_bloodbag`：生物质 100，`build_path = /obj/item/reagent_containers/blood`）

| # | 设计名 | id | 生物质 | 血袋 |
|---|---|---|---|---|
| 1 | **A+ Blood Pack** | `organic_bloodbag_aplus` | 100 | `blood/a_plus` |
| 2 | **A- Blood Pack** | `organic_bloodbag_aminus` | 100 | `blood/a_minus` |
| 3 | **B+ Blood Pack** | `organic_bloodbag_bplus` | 100 | `blood/b_plus` |
| 4 | **B- Blood Pack** | `organic_bloodbag_bminus` | 100 | `blood/b_minus` |
| 5 | **O+ Blood Pack** | `organic_bloodbag_oplus` | 100 | `blood/o_plus` |
| 6 | **O- Blood Pack** | `organic_bloodbag_ominus` | **150** | `blood/o_minus` |
| 7 | **L Blood Pack**（蜥蜴血） | `organic_bloodbag_lizard` | 100 | `blood/lizard` |
| 8 | **LE Blood Pack**（以太血） | `organic_bloodbag_ethereal` | 100 | `blood/ethereal` |
| 9 | **H2O Blood Pack**（植物人血） | `organic_bloodbag_plant` | **50** | `blood/podperson` |
| 10 | **TOX Blood Pack**（史莱姆毒血） | `organic_bloodbag_slimeperson` | 100 | `blood/toxin`（代码中出现两次） |
| 11 | **Haemocyanin Blood Pack** 血蓝蛋白血 | `organic_bloodbag_haemocyanin` | 100 | `blood/haemocyanin` |
| 12 | **Chlorocruorin Blood Pack** 血绿蛋白血 | `organic_bloodbag_chlorocruorin` | 100 | `blood/chlorocruorin` |
| 13 | **Hemerythrin Blood Pack** 血褐蛋白血 | `organic_bloodbag_hemerythrin` | 100 | `blood/hemerythrin` |
| 14 | **Pinnaglobin Blood Pack** 扇贝血红蛋白 | `organic_bloodbag_pinnaglobin` | 100 | `blood/pinnaglobin` |
| 15 | **Exotic Blood Pack** 异星血 | `organic_bloodbag_exotic` | 100 | `blood/exotic` |

---

# 第四卷 · 存储容器与医疗包

**代码**: `modular_nova/modules/deforest_medical_items/code/storage_items.dm`（498 行）+ `code/storage_items_robotics.dm`（72 行）

## 4.1 药瓶（2 种）

### ① Amollin Pill Bottle（阿莫林药瓶）`/obj/item/storage/pill_bottle/painkiller`

- **描述**：纯白色密封药瓶，标签为阿莫林——矿工药膏、利多卡因与糖的混合配方。
- **数值**：`spawn_count = 7`、`custom_price = PAYCHECK_CREW × 1.5`、图标 `painkiller_bottle`
- **内含**：**Amollin Pill（阿莫林药丸）** `/obj/item/reagent_containers/applicator/pill/amollin`（图标 `pill9`）——`mine_salve`=10 · `lidocaine`=5 · `sugar`=5

### ② Alifil Pill Bottle（阿利菲尔药瓶）`/obj/item/storage/pill_bottle/prescription_stimulant`

- **描述**：微型药瓶，内部插片酷似左轮弹巢，专为"民防"级壳式急救包设计；只接受 DeForest™ 专属形状的阿利菲尔药丸。瓶身黄色大字警告："遵守剂量说明"。
- **数值**：`w_class = TINY`、`spawn_count = 5`、存储 `/datum/storage/alifil_pills`（`max_slots = 5`，仅可装阿利菲尔药丸）
- **内含**：**Alifil Pill（阿利菲尔药丸）** `/obj/item/reagent_containers/applicator/pill/prescription_stimulant`（图标 `pill15`）——`sugar`=5 · `synaptizine`=5 · `modafinil`=3。治疗困倦与突然昏厥；警告："适量服用"

## 4.2 急救包（6 种）

### ③ Civil Defense Medical Kit（民防医疗包）`/obj/item/storage/medkit/civil_defense`

- **描述**：只能装自动注射器的小型医疗包，俗称"奶酪包"（连奶酪切片都能塞）。`w_class = SMALL`、`custom_price = PAYCHECK_COMMAND × 3`、`max_slots = 4`
- **可容纳**：medipen 类注射器、阿利菲尔药瓶、硬质奶酪切片/奶酪楔
- **/stocked 预装**：`meridine`×1 · `halobinin`×1 · `lipital`×1 · `calopine`×1
- **/thunderdome 版**：从 13 种随机注射器中随机抽取 **6 支**（twitch / demoneye / aranepaine / pentibinin / synalvipitol / adrenaline / morpital / lipital / synephrine / calopine / coagulants / krotozine / lepoturi）

### ④ Civil Defense Symptom Support Kit（民防症状支持包）`/obj/item/storage/medkit/civil_defense/comfort`

- **描述**：口袋大小的变体，针对慢性病与适应综合征（如重力病）的症状负担。
- **/stocked 预装**：`psifinil`×3 + 阿利菲尔药瓶×1

### ⑤ Frontier Medical Kit（前沿医疗包）`/obj/item/storage/medkit/frontier`

- **描述**：卷盖式防水医疗包，常见于医疗支持欠佳的前沿地带，可挂腰带。
- **/stocked 预装**：`meridine`×1 · `morpital`×1 · `ointment`×1 · `suture`×1 · `suture/coagulant`×1 · `gauze/sterilized`×1 · `painkiller`×1

### ⑥ Combat Surgeon Medical Kit（战斗外科医疗包）`/obj/item/storage/medkit/combat_surgeon`

- **描述**：折叠式医疗包，理想状态装满外科工具与针对难治伤口的特化治疗方案。存储限重 `WEIGHT_CLASS_NORMAL`
- **/stocked 预装**：`bonesetter` · `hemostat` · `cautery` · `wound_recovery`×1 · `wound_recovery/rapid_coagulant`×1 · `gauze/sterilized`×1 · `healthanalyzer/simple`×1

### ⑦ Robotic Repair Equipment Kit（机器人维修设备包）`/obj/item/storage/medkit/robotic_repair`

- **描述**：工业级塑料箱，装满足以将合成体从致命损伤中修复的物资。图标 `synth_medkit`
- **可容纳**：标准医疗包物品 + 机械工具（电缆线圈/撬棍/螺丝刀/扳手/焊枪/剪线钳/万用表/皮搋子/焊接护目镜）
- **/stocked 预装**：`gauze`×1 · `synth_repair`×3 · `robofoam`×1 · `robot_system_cleaner`×1 · `healthanalyzer/simple`×1

### ⑧ Premium Robotic Repair Equipment Kit（高级机器人维修设备包）`/obj/item/storage/medkit/robotic_repair/preemo`

- **描述**：侧面带额外收纳的高级版。图标 `synth_medkit_super`；存储升级为 12 格 × `WEIGHT_CLASS_NORMAL`
- **/stocked 预装**：`gauze/twelve`×1 · `cable_coil/thirty`×1 · `synth_repair`×4 · `robofoam`×1 · `robot_system_cleaner`×1 · `robot_liquid_solder`×1 · `coagulants`×1 · `spray/dinitrogen_plasmide`×1 · `healthanalyzer/simple`×1

## 4.3 重型工具包（3 种，可穿戴）

### ⑨ Satchel Medical Kit（挎包医疗包）`/obj/item/storage/backpack/duffelbag/deforest_medkit`

- **描述**：大号橙色挎包，能装下几乎所有小型医疗设备，可背在背上或挂在腰带。拉链减速 `0.25`、开包 1.2 秒；存储 21 格 × SMALL
- **/stocked 预装（19 项）**：`morpital`·`lepoturi`·`lipital`·`meridine`·`calopine`·`coagulants` 各 1 · `bonesetter`·`hemostat`·`cautery` 各 1 · `wound_recovery`·`rapid_coagulant`·`suture/coagulant` 各 1 · `suture/bloody`×2 · `mesh`×2 · `gauze/sterilized`·`gauze` 各 1 · `red_sun`×1 · `painkiller`×1 · `healthanalyzer/simple`×1

### ⑩ First Responder Surgical Kit（急救员外科包）`/obj/item/storage/backpack/duffelbag/deforest_surgical`

- **描述**：灰色大包，装得下所有外科工具与急救设备，可穿戴。拉链减速 `0.5`、开包 1.2 秒；存储 14 格 × NORMAL
- **/stocked 预装（14 项）**：`scalpel`·`hemostat`·`retractor`·`circular_saw`·`bonesetter`·`cautery`·`surgical_drapes`·`blood_filter`·`emergency_bed` 各 1 · `gauze`·`gauze/sterilized` 各 1 · `medigel/sterilizine`×1 · `sticky_tape/surgical`×1 · `bone_gel`×1

### ⑪ Medical Technician Kit（医疗技师包）`/obj/item/storage/backpack/duffelbag/deforest_paramedic`

- **描述**：粉红色变体，为流动急救员配备 Hypospray 挂载位与轻量快取口袋，但只能装小件物品。拉链减速 `0.3`；存储 21 格 × SMALL
- **/stocked 预装（19 项）**：`scalpel`·`hemostat`·`retractor`·`circular_saw/field_medic/lowforce`·`bonesetter`·`cautery`·`surgical_drapes` 各 1 · `bone_gel`×1 · `wound_recovery`·`rapid_coagulant` 各 1 · `mesh/advanced`×2 · `suture/medicated`×2 · `gauze/sterilized`×1 · `painkiller`×1 · **`hypospray/mkii/piercing/atropine`**×1（穿透版注射枪+阿托品药瓶）· `vial/small/libital`·`vial/small/lenturi`·`vial/small/seiver` 各 1 · `healthanalyzer/advanced`×1

---

# 第五卷 · DeForest Med-Vend 售货机

**代码**: `modular_nova/modules/deforest_medical_items/code/vendor.dm`（81 行）

## 5.1 机器参数

- **DeForest Med-Vend** `/obj/machinery/vending/deforest_medvend`：图标 `medvend`、面板 `panel15`、灯光浅青色（`LIGHT_COLOR_LIGHT_CYAN`）
- **广告语**："以法规规定的合理价格提供医疗服务！" / "DeForest 对因用品误用造成的事故概不负责！"
- **价格**：`default_price = PAYCHECK_CREW`、`extra_price = PAYCHECK_COMMAND × 4`、`payment_department = NO_FREEBIES`（无免费品）、`allow_custom = TRUE`
- **补给罐**：`/obj/item/vending_refill/medical_deforest`（机名 "DeForest Med-Vend"，图标 `refill_medical`）
- **特殊**：若生成在中央委员会中转站（interlink）区域，强制 `all_products_free = FALSE`

## 5.2 商品全录

### 急救栏（First Aid，图标 `notes-medical`，19 种）

| 商品 | 库存 |
|---|---|
| `ointment/red_sun` 红日药膏 | 4 |
| `ointment` 药膏 | 4 |
| `bruise_pack` 淤伤敷包 | 4 |
| `wrap/gauze/sterilized` 密封无菌纱布 | 4 |
| `suture/coagulant` 凝血素-F 敷包 | 4 |
| `suture` 缝合线 | 4 |
| `suture/bloody` 染血缝合线 | 2 |
| `mesh` 修复网 | 4 |
| `mesh/bloody` 染血修复网 | 2 |
| `bandage` 绷带 | 4 |
| `wound_recovery` 皮下夹板敷贴器 | 2 |
| `wound_recovery/rapid_coagulant` 速效凝血敷贴器 | 2 |
| `medkit/civil_defense/stocked` 民防医疗包（预装） | 2 |
| `pill_bottle/painkiller` 阿莫林药瓶 | 4 |
| `synth_repair` 机器人修复贴片 | 4 |
| `disk/neuroware/lidocaine` 神经软件碟·利多卡因 | 4 |
| `disk/neuroware/reset` 神经软件碟·重置 | 2 |
| `disk/neuroware/brain` 神经软件碟·大脑 | 2 |
| `wound_recovery/robofoam` 机器人修复泡沫 | 2 |
| `wound_recovery/robofoam_super` 高级机器人修复泡沫 | **1** |

### 注射器栏（Autoinjectors，图标 `syringe`，12 种）

| 商品 | 库存 |
|---|---|
| `occuisate` 感官恢复 | 3 |
| `adrenaline` 肾上腺素 | 3 |
| `morpital` 再生兴奋剂 | 4 |
| `lipital` 再生兴奋剂·钝击 | 3 |
| `meridine` 解毒剂 | 3 |
| `calopine` 紧急稳定剂 | 4 |
| `coagulants` 凝血素-S | 4 |
| `lepoturi` 烧伤治疗 | 3 |
| `psifinil` 个人恢复 | 3 |
| `halobinin` 解酒解毒 | 3 |
| `robot_system_cleaner` 合成体清洁 | 3 |
| `robot_liquid_solder` 合成体焊锡 | 3 |

### 违禁品栏（Contraband，7 种，需 EMAG 解锁）

| 商品 | 库存 |
|---|---|
| `pentibinin` 归一化 | 2 |
| `synephrine` 紧急兴奋剂 | 2 |
| `krotozine` 操控性兴奋剂 | 2 |
| `aranepaine` 战斗兴奋剂 | 2 |
| `synalvipitol` 肌肉兴奋剂 | 2 |
| `twitch` TWitch 感官兴奋剂 | 2 |
| `demoneye` DemonEye 类固醇 | 2 |

---

# 第六卷 · 货运包

**代码**: `modular_nova/modules/deforest_medical_items/code/cargo_packs.dm`（70 行）

> 所有货运包均需 `ACCESS_MEDICAL` 医疗权限；`CARGO_CRATE_VALUE` 为单箱基准价。

| # | 货运包 | 中文名 | 成本 | 内容 |
|---|---|---|---|---|
| 1 | **Civil Defense Medical Kit Crate** | 民防医疗包箱 | `×10`（注释 2000） | `civil_defense/stocked`×10（紧急向公众分发的注射器小包） |
| 2 | **Civil Defense Symptom Support Kit Crate** | 民防症状支持包箱 | `×5`（注释 2000） | `comfort/stocked`×10（每包 3 支 psifinil + 5 粒阿利菲尔药丸管，专治慢性病与重力病） |
| 3 | **Frontier First Aid Crate** | 前沿急救箱 | `×10` | `frontier/stocked`×3 + `combat_surgeon/stocked`×3 |
| 4 | **Heavy Duty Medical Kit Crate - Technician** | 重型医疗包箱·技师 | `×5.5` | `duffelbag/deforest_paramedic/stocked`×1（粉色医疗技师包） |
| 5 | **Heavy Duty Medical Kit Crate - Surgical** | 重型医疗包箱·外科 | `×5` | `duffelbag/deforest_surgical/stocked`×1（灰色急救员外科包） |
| 6 | **Heavy Duty Medical Kit Crate - Medical** | 重型医疗包箱·医疗 | `×4.5` | `duffelbag/deforest_medkit/stocked`×1（橙色挎包医疗包） |
| 7 | **DeForest Med-Vend Resupply Crate** | Med-Vend 补货箱 | `×5` | `vending_refill/medical_deforest`×1 |

---

# 第七卷 · Hypospray Mk.II 系统

**代码**: `modular_nova/modules/hyposprays/code/hyposprays_II.dm`（382 行）

## 7.1 基类与机制

**Hypospray Mk.II** `/obj/item/hypospray/mkii`

- **描述**：DeForest Medical 的新品，采用 50u 药瓶作为药源，可快速更换。
- **数值**：`w_class = TINY`；材料塑料×5 + 玻璃×3 + 银×1（SHEET 单位）；`quickload = TRUE`（可热插拔药瓶）；`penetrates = null`（默认不穿透）
- **默认药瓶**：仅接受 `vial/small`（60u 小药瓶）
- **单次剂量档位**：`possible_transfer_amounts = (1, 3, 5, 10, 15)`，默认 `amount_per_transfer = 1`
- **GAGS 自定义**：Ctrl+Shift 打开灰度配色菜单（默认配色 `#00AAFF#FFAA00`）或还原默认外观

### 注射/喷雾延迟（宏定义）

| 档位 | 注射他人 | 喷雾他人 | 自我注射 | 自我喷雾 |
|---|---|---|---|---|
| 标准（WAIT/SELF） | 1.5 秒 | 1.5 秒 | 2 秒 | 2 秒 |
| 豪华（DELUXE） | 0.5 秒 | 0.5 秒 | 1 秒 | 1 秒 |
| 战斗（COMBAT） | 0 秒 | 0 秒 | 0 秒 | 0 秒 |

### 交互方式

| 操作 | 效果 |
|---|---|
| 主手点击人/物 | **喷雾**（`HYPO_SPRAY`，PATCH 途径） |
| 副手点击人/物 | **注射**（`HYPO_INJECT`，INJECT 途径） |
| 点击药瓶 | 插入药瓶（已有药瓶且 quickload 时自动换出） |
| 攻击自身（attack_self） | 剂量档位**前进**一档 |
| 副手攻击自身 | 剂量档位**后退**一档 |
| 空手点击（attack_hand） | 卸下药瓶 |
| **EMAG** | 在豪华/战斗速度之间切换（可反复切换） |
| 音效 | ≥15u 长音效 `hypospray_long.ogg`；其余随机 `hypospray.ogg`/`hypospray2.ogg` |

## 7.2 型号全录（9 种）

| # | 型号 | 中文名 | 药瓶支持 | 速度 | 穿透 | 特殊 |
|---|---|---|---|---|---|---|
| 1 | **Mk.II** `hypospray/mkii` | 标准版 | 小药瓶（50u 档） | 标准 | 无 | 基础款 |
| 2 | **Mk.II Deluxe** `hypospray/mkii/deluxe` | 豪华版 | 小+大药瓶（50/100u 档） | 标准 | 无 | 图标 `bighypo2` |
| 3 | **Mk.II Advanced** `hypospray/mkii/piercing` | 先进版 | 小药瓶 | 标准 | **可穿透厚装甲** | 额外材料钛×1；图标 `piercinghypo2` |
| 4 | **Mk.II Advanced (Atropine)** `hypospray/mkii/piercing/atropine` | 先进版·阿托品 | 小药瓶 | 标准 | 穿透 | 出厂自带 `vial/small/atropine`（急救员包专用） |
| 5 | **Mk.II Deluxe: CMO Edition** `hypospray/mkii/deluxe/cmo` | CMO 典藏版 | 小+大药瓶 | **豪华** | **穿透** | `INDESTRUCTIBLE|LAVA|FIRE|ACID` 全免疫；剂量档位 `(0.1,1,3,5,10,15,20,30)` |
| 6 | **Mk.II Deluxe: Combat Edition** `hypospray/mkii/deluxe/cmo/combat` | 战斗典藏版 | 小+大药瓶 | **战斗（0 秒）** | **穿透** | `LAVA|FIRE|ACID` 免疫（可被摧毁，管理员生成物） |
| 7 | **Mk.II-Y** `hypospray/mkii/interdyne` | Interdyne 专供版 | **中号 Interdyne 药瓶**（90u） | **豪华** | **穿透** | 图标 `interdyne2` |
| 8 | **Mk.II-Y Advanced** `hypospray/mkii/interdyne/deckoff` | Interdyne 先进版 | 中号 Interdyne 药瓶 | **战斗（0 秒）** | **穿透** | `LAVA|FIRE|ACID` 免疫；剂量档位 `(0.1,1,3,5,10,15,20,30)` |
| 9 | （Mk.II 本体系即第 1 款） | — | — | — | — | — |

## 7.3 升级套件

**Hypospray Mk.II Deluxe Bodykit（豪华升级外壳套件）** `/obj/item/device/custom_kit/deluxe_hypo2`

- **描述**：将标准 Mk.II 升级为支持大药瓶的 Deluxe 版。
- **转换**：`from_obj = /obj/item/hypospray/mkii` → `to_obj = /obj/item/hypospray/mkii/deluxe`
- **造价**：塑料×8 + 玻璃×4 + 银×2（SHEET 单位）
- **限制**：目标已是 Mk.II 子类型（如 Deluxe/CMO 版）或枪内已有药瓶时拒绝改装（"别折腾一把装好弹的（医疗）枪"）

---

# 第八卷 · 药瓶 Hypovial

**代码**: `modular_nova/modules/hyposprays/code/hypovials.dm`（382 行）

## 8.1 药瓶基类与换肤系统

**基类** `/obj/item/reagent_containers/cup/vial`（"broken hypovial"——正常情况下不应见到）：`volume = 10`、剂量档位 `(1,2,5,10)`、液面阈值 `(10,25,50,75,100)`、`chem_color` 供 Hypospray 叠加层取色；Ctrl+Shift 重置灰度配色

**换肤系统** `/datum/atom_skin/hypovial`：应用皮肤时可**重命名药瓶**；Custom（自定义）皮肤额外打开灰度配色菜单。三套皮肤组：

| 皮肤组 | 基底图标 | 皮肤（8+1 种） |
|---|---|---|
| 标准 `hypovial` | `hypovial` | Sterile 无菌 / Generic 通用 / Brute 钝击 / Burn 烧伤 / Tox 毒素 / Oxy 缺氧 / Crit 濒死 / Buff 增益 / **Custom 自定义**（灰度） |
| 大号 `hypovial/large` | `hypoviallarge` | 同上（`hypoviallarge-*`） |
| Interdyne 中号 `hypovial/interdyne_medium` | `hypovial-interdyne` | 同上（`hypovial-interdyne-*`） |

## 8.2 三种药瓶规格

| 规格 | 中文名 | 容量 | 剂量档位 | 材质 | 适配 |
|---|---|---|---|---|---|
| **Hypovial** `vial/small` | 小药瓶 | **60u** | `(5,10,15,20,30,60)` | 铁×0.5 + 玻璃×0.5（HALF_SHEET） | 所有 Mk.II |
| **Large Hypovial** `vial/large` | 大药瓶 | **120u** | `(5,10,15,20,30,40,60,120)` | 玻璃×1 + 铁×0.5（SHEET/HALF） | 仅 Deluxe/CMO 版 |
| **Medium Mountable Hypovial** `vial/interdyne_medium` | Interdyne 中号药瓶 | **90u** | `(1,2,5,10,15,20,30,60,90)` | —（带安装夹+Interdyne 印章） | 仅 Mk.II-Y 系列 |

## 8.3 预装药瓶全录（16 种）

### CMO 典藏包出厂药瓶（大号，4 种）

| 药瓶 | 中文名 | 图标 | 试剂 |
|---|---|---|---|
| `vial/large/deluxe` | 豪华药瓶 | `hypoviallarge-buff` | `omnizine`=15 · `leporazine`=15 · `atropine`=15 |
| `vial/large/salglu` | 大绿药瓶（salglu） | `hypoviallarge-oxy` | `salglu_solution`=50 |
| `vial/large/synthflesh` | 大橙药瓶（合成肉） | `hypoviallarge-crit` | `c2/synthflesh`=50 |
| `vial/large/multiver` | 大黑药瓶（万解） | `hypoviallarge-tox` | `c2/multiver`=50 |

### 战斗包定制药瓶（大号，7 种，各 100u）

| 药瓶 | 中文名 | 图标 | 试剂 |
|---|---|---|---|
| `vial/large/advbrute` | Brute Heal 钝击治疗 | `hypoviallarge-brute` | `c2/libital`=50 + `sal_acid`=50 |
| `vial/large/advburn` | Burn Heal 烧伤治疗 | `hypoviallarge-burn` | `c2/aiuri`=50 + `oxandrolone`=50 |
| `vial/large/advtox` | Toxin Heal 毒素治疗 | `hypoviallarge-tox` | `pen_acid`=100 |
| `vial/large/advoxy` | Oxy Heal 缺氧治疗 | `hypoviallarge-oxy` | `c2/tirimol`=50 + `salbutamol`=50 |
| `vial/large/advcrit` | Crit Heal 濒死治疗 | `hypoviallarge-crit` | `atropine`=100 |
| `vial/large/advomni` | All-Heal 全效治疗 | `hypoviallarge-buff` | `regen_jelly`=100 |
| `vial/large/numbing` | Numbing 麻木 | `hypoviallarge-generic` | `mine_salve`=50 + `morphine`=50 |

### 急救员包定制药瓶（小号，5 种，初始化注满 60u，纯度 1）

| 药瓶 | 中文名 | 图标 | 试剂 |
|---|---|---|---|
| `vial/small/libital` | 钝击药瓶（libital） | `hypovial-brute` | `c2/libital` 注满 60u |
| `vial/small/lenturi` | 烧伤药瓶（lenturi） | `hypovial-burn` | `c2/lenturi` 注满 60u |
| `vial/small/seiver` | 毒素药瓶（seiver） | `hypovial-tox` | `c2/seiver` 注满 60u（温度 975K） |
| `vial/small/convermol` | 毒素药瓶（convermol） | `hypovial-oxy` | `c2/convermol` 注满 60u |
| `vial/small/atropine` | 濒死药瓶（atropine） | `hypovial-crit` | `atropine` 注满 60u |

---

# 第九卷 · 工具包 Hypospray Kits

**代码**: `modular_nova/modules/hyposprays/code/hypospray_kits.dm`（321 行）

## 9.1 基类与外壳设计

**Hypospray Kit（Hypospray 工具包）** `/obj/item/storage/hypospraykit`

- **描述**：带药瓶泡沫内衬与底部挂载点的 Hypospray 工具包。`w_class = SMALL`、可挂腰带、投掷速度 3/距离 7
- **存储**：`/datum/storage/hypospray_kit`——**7 格**，仅可容纳 Mk.II 系 Hypospray 与药瓶
- **挂载 Hypospray**：副手右键用 Mk.II 点击工具包 → 吸附在包底；Alt+副手点击卸下
- **外观切换**：Ctrl+Shift 打开径向菜单选外壳；custom 系列打开灰度配色
- **默认出厂**：自带 1 支 `hypospray/mkii`（`empty` 版为空包，造价塑料×3+铁×1）
- **小号外壳 16 种**：`firstaid`（急救）/`brute`（钝击）/`burn`（烧伤）/`toxin`（毒素）/`oxy`（缺氧）/`advanced`（先进）/`buffs`（增益）/`custom`（自定义灰度）+ Interdyne 同款 8 种（`interdyne-*`）
- **大号外壳 7 种**：`cmo` / `emt` / `tactical` / `deluxe-custom` / `tactical-custom` / `interdyne-deluxe` / `interdyne-deluxe-custom`

## 9.2 各型号全录（6 种 + 3 种收纳盒）

| # | 型号 | 中文名 | 存储 | 预装内容 |
|---|---|---|---|---|
| 1 | **Hypospray Kit** `hypospraykit` | 标准工具包 | 7 格 | `hypospray/mkii`×1 |
| 2 | **Interdyne Hypospray Kit** `hypospraykit/interdyne` | Interdyne 工具包 | 7 格 | `hypospray/mkii/interdyne`×1 + `interdyne_medium/style/buff`×**7** |
| 3 | **Deluxe Hypospray Kit** `hypospraykit/cmo` | 豪华工具包 | **21 格**（总容量 28） | `hypospray/mkii/deluxe/cmo`×1（`w_class = NORMAL` 不可塞口袋） |
| 4 | **CMO's Deluxe Hypospray Kit** `hypospraykit/cmo/preloaded` | CMO 典藏豪华包 | 21 格 | CMO 典藏枪 + `large/deluxe`·`large/multiver`·`large/salglu`·`large/synthflesh` 药瓶 |
| 5 | **Combat Hypospray Kit** `hypospraykit/cmo/combat` | 战斗工具包 | 21 格 | 战斗典藏枪 + `advbrute`·`advburn`·`advtox`·`advoxy`·`advcrit`·`advomni`·`numbing` 七瓶 |
| 6 | **Interdyne Deluxe Hypospray Kit** `hypospraykit/cmo/interdyne` | Interdyne 豪华工具包 | 21 格 | `mkii/interdyne/deckoff`×1 + `interdyne_medium/style/buff`×**21** |
| 7 | **Box of Hypovials** `storage/box/vials` | 药瓶盒 | — | 每种 `vial/small/style` 子类型各 1 支 |
| 8 | **Box of Deluxe Hypovials** `storage/box/vials/deluxe` | 豪华药瓶盒 | — | 每种 `vial/large/style` 子类型各 1 支 |
| 9 | **Box of Hypospray Kits** `storage/box/hypospray` | 工具包盒 | — | 7 个预换皮工具包：firstaid/brute/burn/toxin/oxy/advanced/buffs |

> **销毁保护**：工具包销毁时，内部不可摧毁物品（含 CMO 典藏枪）会被安全转移到地面，普通物品随包删除。

---

# 第十卷 · 车床设计与售货机

**代码**: `modular_nova/modules/hyposprays/code/autolathe_designs.dm`（141 行）+ `code/vending_hypospray.dm`（7 行）

## 10.1 自动车床设计全录（8 项）

| # | 设计名 | id | 车床类型 | 材料（SHEET 单位） | 产物 |
|---|---|---|---|---|---|
| 1 | **Hypovial** 药瓶 | `hypovial` | `AUTOLATHE\|PROTOLATHE\|AWAY_LATHE` | 玻璃×0.5 + 铁×0.5 | `vial/small` |
| 2 | **Large Hypovial** 大药瓶 | `large_hypovial` | 同上 | 玻璃×1 + 铁×0.5 | `vial/large` |
| 3 | **Hypospray Case** 工具包壳 | `hypokit` | `PROTOLATHE\|AWAY_LATHE` | 塑料×3 + 铁×1 | `hypospraykit/empty` |
| 4 | **Deluxe Hypospray Case** 豪华工具包壳 | `hypokit_deluxe` | 同上 | 塑料×6 + 铁×3 + 银×1 | `hypospraykit/cmo/empty` |
| 5 | **Hypospray Mk. II** | `hypomkii` | 同上 | 塑料×5 + 玻璃×3 + 银×1 | `hypospray/mkii` |
| 6 | **Hypospray Mk. II Deluxe Upgrade** 豪华升级套件 | `hypomkii_deluxe` | 同上 | 塑料×8 + 玻璃×4 + 银×2 | `device/custom_kit/deluxe_hypo2` |
| 7 | **Hypospray Mk. II Advanced** 先进版 | `hypomkii_advanced` | 同上 | 塑料×5 + 玻璃×3 + 银×1 + 钛×1 | `hypospray/mkii/piercing` |
| 8 | **Pen** 笔（"闹鬼"设计） | `pen` | `AUTOLATHE\|AWAY_LATHE` | 铁×1 + 玻璃×1（SMALL） | `obj/item/pen` |

**科技树挂载**：
- `techweb_node/medbay_equip`（医疗设备）→ + `hypovial`
- `techweb_node/medbay_equip_adv`（先进医疗设备）→ + `hypokit_deluxe`、`hypomkii_advanced`
- `techweb_node/alien_surgery`（异星手术）→ + `hypomkii_deluxe`
- 附带修改：`/obj/item/pen` 与 `/obj/item/thermometer/pen` 获得车床材料定义（铁+玻璃 SMALL）

## 10.2 售货机

**源码**: `vending_hypospray.dm`

- **Drugs 售货机**（`/obj/machinery/vending/drugs`）初始化时按所在区域补货：
  - 位于 `area/ruin/interdyne_planetary_base`（Interdyne 行星基地废墟）→ 上架 **`hypospraykit/interdyne`** ×5
  - 其他区域 → 上架 **`hypospraykit`** ×5

---

# 附录 · 代码路径索引

## deforest_medical_items（15 文件，2,161 行）

| 文件 | 行数 | 内容 |
|---|---|---|
| `modular_nova/modules/deforest_medical_items/code/healing_stack_items.dm` | 197 | 皮下夹板/速效凝血/红日药膏/无菌纱布/凝血素-F |
| `modular_nova/modules/deforest_medical_items/code/synth_healing.dm` | 199 | 机器人修复泡沫×2/修复贴片/赛博格修复膏 |
| `modular_nova/modules/deforest_medical_items/code/vulnerable_status_effect.dm` | 28 | 易伤状态效果 |
| `modular_nova/modules/deforest_medical_items/code/treatment_zone_projector.dm` | 48 | 治疗区全息指示器/投影仪/科技设计 |
| `modular_nova/modules/deforest_medical_items/code/injectors.dm` | 231 | Deforest 注射器基类 + 14 支合法注射器 |
| `modular_nova/modules/deforest_medical_items/code/illegal_injectors.dm` | 75 | 5 支非法注射器 |
| `modular_nova/modules/deforest_medical_items/code/chemicals/twitch.dm` | 290 | TWitch 药物（反应+效果+残影+移速修正） |
| `modular_nova/modules/deforest_medical_items/code/chemicals/demoneye.dm` | 150 | DemonEye 药物（反应+效果+心情事件） |
| `modular_nova/modules/deforest_medical_items/code/medstation.dm` | 56 | 壁挂医疗站 + 墙框 |
| `modular_nova/modules/deforest_medical_items/code/medstation_designs/medical.dm` | 74 | 12 项医疗打印设计 |
| `modular_nova/modules/deforest_medical_items/code/medstation_designs/blood.dm` | 92 | 15 项血袋打印设计 |
| `modular_nova/modules/deforest_medical_items/code/storage_items.dm` | 498 | 药瓶×2/民防包/前沿包/战斗外科包/重型工具包×3 |
| `modular_nova/modules/deforest_medical_items/code/storage_items_robotics.dm` | 72 | 机器人维修包×2 |
| `modular_nova/modules/deforest_medical_items/code/vendor.dm` | 81 | DeForest Med-Vend 售货机 + 补货罐 |
| `modular_nova/modules/deforest_medical_items/code/cargo_packs.dm` | 70 | 7 种医疗货运包 |

## hyposprays（5 文件，1,233 行）

| 文件 | 行数 | 内容 |
|---|---|---|
| `modular_nova/modules/hyposprays/code/hyposprays_II.dm` | 382 | Mk.II 基类/豪华/先进/CMO/战斗/Interdyne 系列 + 升级套件 |
| `modular_nova/modules/hyposprays/code/hypovials.dm` | 382 | 药瓶基类/换肤系统/3 规格/16 种预装药瓶 |
| `modular_nova/modules/hyposprays/code/hypospray_kits.dm` | 321 | 工具包基类/外壳设计/6 型号/3 收纳盒 |
| `modular_nova/modules/hyposprays/code/autolathe_designs.dm` | 141 | 8 项车床设计 + 科技树挂载 |
| `modular_nova/modules/hyposprays/code/vending_hypospray.dm` | 7 | Drugs 售货机 Interdyne 区域补货 |
