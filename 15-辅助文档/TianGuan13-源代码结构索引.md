# 天关 (TianGuan13) 源代码结构索引

> **项目根**: `C:\Users\33922\Desktop\Hermes\TianGuan13\TianGuan13-master\`
> **基础**: Nova Sector → /tg/station
> **本文档**: 基于已完成的分析工作(工程气体/化学/医疗/病毒学/水培/职业/供应/采矿)整理, 已覆盖~65+文件, 标记✅=已读

---

## 如何读这篇索引

```
路径/文件名.dm (行数) [状态] — 备注
```

`[状态]`:
- ✅ = 已全量读完并输出百科
- 🔍 = 已扫描结构但未深入
- ⬜ = 未读

---

# 第一卷 · 大气与工程 (Atmospherics / Engineering)

## 1.1 气体定义与常量

**根**: `code/modules/atmospherics/` + `code/__DEFINES/atmospherics/`

| # | 路径 | 行数 | 状态 | 内容 |
|---|---|---|---|---|
| G01 | `gasmixtures/gas_types.dm` | 333 | ✅ | **20种气体定义**: 比热/聚变功率/稀有度/价值/颜色/名称, 每种的`name`/`specific_heat`/`fusion_power`/`dangerous`/`rarity`/`base_value` |
| G02 | `gasmixtures/reactions.dm` | 1,240 | ✅ | **25+气体反应**: 4燃烧(Plasma/H₂/Tr/Freon) + 10合成(N₂O/BZ/Nitrium/Freon/Noblium/Healium/Zauker/PN/Antinob) + 3分解(N₂O/Nitrium/Zauker) + 4特殊(冷凝/Halon/PN三阶段) |
| G03 | `gasmixtures/reaction_factors.dm` | 216 | ✅ | 反应因素/手册显示文本 |
| G04 | `gasmixtures/gas_mixture.dm` | 815 | ✅ | **气体混合引擎**: `gas_mixture`数据类, 混合/分流/反应/热传导核心算法 |
| G05 | `__DEFINES/atmospherics/atmos_gasses.dm` | 20 | ✅ | 气体常量: `MOLES_GAS_VISIBLE` 等 |
| G06 | `__DEFINES/atmospherics/atmos_core.dm` | 190 | ✅ | 大气核心常量: 压力/温度/体积阈值 |
| G07 | `__DEFINES/atmospherics/atmos_piping.dm` | 70 | ✅ | 管道常量: 层/颜色/连接规则 |
| G08 | `__DEFINES/atmospherics/atmos_mob_interaction.dm` | 200 | ✅ | 气体对人体效果常量: 各气体分压阈值(16pp/10pp/0.05pp等) |

## 1.2 大气机械 (Pipes & Machinery)

**根**: `code/modules/atmospherics/machinery/`

| # | 路径 | 行数 | 状态 | 内容 |
|---|---|---|---|---|
| M01 | `atmosmachinery.dm` | 100 | ✅ | **大气机械基类**: 所有管道设备的父类 |
| M02 | `datum_pipeline.dm` | 376 | ✅ | **管线系统**: Pipeline数据类, reconcile_air()气体均衡, react()管内反应 |
| M03 | `components/components_base.dm` | 96 | ✅ | 组件基类: 端口映射/连接规则 |
| M04 | `components/binary_devices/pump.dm` | 139 | ✅ | **泵(Pump)**: target_pressure 0~4500kPa |
| M05 | `components/binary_devices/volume_pump.dm` | — | ✅ | **体积泵(Volume Pump)**: 固定流量, 可逆压差 |
| M06 | `components/binary_devices/passive_gate.dm` | — | ✅ | **被动门**: 不耗电单向阀 |
| M07 | `components/binary_devices/pressure_valve.dm` | — | ✅ | **压力阀**: 超过开启压力自动放气 |
| M08 | `components/binary_devices/valve.dm` | — | ✅ | 阀门: 简单通/断 |
| M09 | `components/binary_devices/circulator.dm` | — | ✅ | **循环泵**: TEG温差发电核心 |
| M10 | `components/binary_devices/temperature_gate.dm` | — | ✅ | 温度门: 温度阈值阀门 |
| M11 | `components/binary_devices/temperature_pump.dm` | — | ✅ | 温度泵: 按目标温度泵送 |
| M12 | `components/trinary_devices/filter.dm` | — | ✅ | **过滤器(Filter)**: 3口, 过滤特定气体到侧口 |
| M13 | `components/trinary_devices/mixer.dm` | — | ✅ | **混合器(Mixer)**: 2入1出, 按比例混合 |
| M14 | `components/unary_devices/vent_pump.dm` | — | ✅ | **通気泵**: 房间↔管道, 排气/吸气模式 |
| M15 | `components/unary_devices/vent_scrubber.dm` | — | ✅ | **洗气机**: 吸CO₂/指定污染物, 200L/s |
| M16 | `components/unary_devices/thermomachine.dm` | — | ✅ | **恒温机**: 加热/冷却到目标温度 |
| M17 | `components/unary_devices/heat_exchanger.dm` | — | ✅ | 热交换器: 管道↔环境换热(不耗电) |
| M18 | `components/tank.dm` | 200 | ✅ | **固定气罐**: 2500L, 46000kPa耐压, 4口 |
| M19 | `components/gas_recipe_machines/crystallizer.dm` | — | ✅ | **气体结晶器**: 气体→固体产物 |
| M20 | `components/gas_recipe_machines/crystallizer_items.dm` | — | ✅ | 结晶产物定义: 晶体/SM碎片/热冰等 |
| M21 | `components/gas_recipe_machines/atmos_machines_recipes.dm` | 200+ | ✅ | **19种结晶配方**: 完整配方表 |
| M22 | `components/electrolyzer/electrolyzer.dm` | — | ✅ | **电解器**: 电力→气体转化 |
| M23 | `components/electrolyzer/electrolyzer_reactions.dm` | 139 | ✅ | 3种电解反应: 水/Hnob→Anob/BZ→Halon |
| M24 | `portable/canister.dm` | 500+ | ✅ | **便携气罐**: 2000L, 500000kPa耐压, 可调释放压力, 爆炸逻辑 |
| M25 | `portable/portable_atmospherics.dm` | 190 | ✅ | 便携设备基类: nob晶体插入/反应抑制 |
| M26 | `other/miner.dm` | — | ✅ | **气体矿机**: 从环境采气, 约515mol/s |
| M27 | `air_alarm/air_alarm_modes.dm` | — | ✅ | **气阀警报**: 7种模式(过滤/污染/通风/补气/循环/抽空/紧急) |
| M28 | `air_alarm/air_alarm_thresholds.dm` | 117 | ✅ | 阈值表: O₂/CO₂/压力/温度的4级阈值 |
| M29 | `pipes/pipes.dm` | — | ✅ | 管道类型: 直管/歧管/4通/层连/桥管/智能 |
| M30 | `pipes/heat_exchange/junction.dm` | — | ✅ | **热交换接口**: 普通管↔热交换管转接 |
| M31 | `pipes/smart.dm` | 80 | ✅ | 智能管道: 位图式图标自动调整连接 |
| M32 | `code/_globalvars/lists/piping_colors_lists.dm` | 47 | ✅ | **12种管道颜色**: omni/green/blue/red/orange/cyan/dark等 |

## 1.3 LINDA 环境系统

| # | 路径 | 行数 | 状态 | 内容 |
|---|---|---|---|---|
| L01 | `gasmixtures/LINDA_fire.dm` | 472 | 🔍 | **火灾系统**: 火焰传播/燃烧/氧气消耗 |
| L02 | `gasmixtures/LINDA_system.dm` | 390 | 🔍 | **大气环流**: 气体扩散/压力均衡 |
| L03 | `gasmixtures/LINDA_turf.dm` | 532 | 🔍 | 地块大气: turf级别的气体处理 |

## 1.4 超物质引擎 (Supermatter)

**根**: `code/modules/power/supermatter/`

| # | 路径 | 行数 | 状态 | 内容 |
|---|---|---|---|---|
| SM01 | `supermatter.dm` | 1,160 | ✅ | **超物质核心**: 6步处理循环, 气体吸收/能量/损伤/解体/闪电 |
| SM02 | `supermatter_gas.dm` | 236 | ✅ | **17种气体对SM影响**: 输电/废热/耐热/热产电/抑衰(百分比加权!) |
| SM03 | `supermatter_hit_procs.dm` | 148 | ✅ | SM交互: 物体会被吞噬/激光可治疗/碎片剽窃 |
| SM04 | `supermatter_variants.dm` | 80 | ✅ | SM变体: engine/shard/small/hugbox |
| SM05 | `supermatter_extra_effects.dm` | 190 | ✅ | 额外效果: 高能量行为(4档闪电)/辐射/幻觉/引力/心理学 |
| SM06 | `supermatter_delamination/_sm_delam.dm` | — | ✅ | 解体基类 |
| SM07 | `supermatter_delamination/common_delams.dm` | — | ✅ | **4种解体策略**: 爆炸/奇点/特斯拉/Cascade |
| SM08 | `supermatter_delamination/cascade_delam.dm` | 99 | ✅ | **Cascade级联**: 条件(Hnob/Anob>40%), 全站晶簇扩散 |
| SM09 | `supermatter_delamination/cascade_delam_objects.dm` | — | ✅ | 级联晶簇: 不可摧毁, 吞噬活物 |
| SM10 | `supermatter_delamination/delamination_effects.dm` | — | ✅ | 解体效果: 辐射脉冲/士气打击/异常生成 |
| SM11 | `__DEFINES/supermatter.dm` | 197 | ✅ | **SM常量**: 能量惩罚阈值5000/摩尔惩罚1800/解体100 |

## 1.5 HFR 聚变反应堆

**根**: `code/modules/atmospherics/machinery/components/fusion/`

| # | 路径 | 行数 | 状态 | 内容 |
|---|---|---|---|---|
| H01 | `_hfr_defines.dm` | 161 | ✅ | **HFR常量**: 聚变阈值25mol/极限温度1e8/熔毁点900/亚临界1200 |
| H02 | `hfr_core.dm` | 214 | ✅ | **HFR核心**: 5部件结构, 核心变量(energy/instability/power_level/iron) |
| H03 | `hfr_fuel_datums.dm` | 140 | ✅ | **7种燃料**: P+O₂/H₂+O₂/Tr+O₂/H₂+Tr/Hnob+H₂/Hnob+Tr/Hnob+Anob |
| H04 | `hfr_main_processes.dm` | 587 | ✅ | **主处理循环**: 6步流程, fusion_process/不稳定度/能量公式/损伤/治疗 |
| H05 | `hfr_procs.dm` | 653 | ✅ | **辅助程序**: check_fuel/check_power_use/update_temperature/meltdown/警报 |
| H06 | `hfr_parts.dm` | 510 | ✅ | **部件系统**: fuel_input/moderator_input/waste_output/interface/corners |

## 1.6 奇点引擎

**根**: `code/modules/power/singularity/`

| # | 路径 | 行数 | 状态 | 内容 |
|---|---|---|---|---|
| SI01 | `singularity.dm` | 520 | ✅ | **奇点核心**: 6级尺寸(S1~S6), 能量系统, 吞噬, expand/check_energy |
| SI02 | `containment_field.dm` | 169 | ✅ | **约束磁场**: TRAIT_CONTAINMENT_FIELD 阻止奇点通过 |
| SI03 | `field_generator.dm` | 454 | ✅ | **磁场发生器**: 消耗电力产生约束磁场 |
| SI04 | `emitter.dm` | 706 | ✅ | **发射器**: 充能2秒/射击2秒, 激光给奇点充能 |
| SI05 | `dark_matter_singularity.dm` | 55 | ✅ | 暗物质奇点变体 |
| SI06 | `narsie.dm` | 336 | 🔍 | Nar'Sie邪神(事件相关) |
| SI07 | `__DEFINES/gravity.dm` | 58 | ✅ | 奇点能量阈值: S1=1/S2=200/S3=500/S4=1000/S5=2000/S6=3000 |

## 1.7 实验军火

**根**: `code/modules/research/ordnance/`

| # | 路径 | 行数 | 状态 | 内容 |
|---|---|---|---|---|
| OR01 | `tank_compressor.dm` | 343 | ✅ | **气罐压缩机**: 5000kPa上限, 内置防爆, 记录气体数据用于科研 |
| OR02 | `doppler_array.dm` | 307 | ✅ | **多普勒阵列**: 150格范围, 定向, 记录爆炸数据 |
| OR03 | `_scipaper.dm` | 321 | ✅ | 科研论文系统 |
| OR04 | `transfer_valve.dm` (在`items/devices/`) | 435 | ✅ | **TTV炸弹**: 2气罐+触发装置, merge_gases/toggle_valve/process_activation |
| OR05 | `tanks/tanks.dm` (在`items/`) | 450+ | ✅ | **气罐爆炸**: 3阶段(Leak/Rupture/Fragment), 爆炸公式, atom_destruction先react再炸 |

---

# 第二卷 · 化学系统 (Chemistry)

## 2.1 试剂基础

**根**: `code/modules/reagents/chemistry/`

| # | 路径 | 行数 | 状态 | 内容 |
|---|---|---|---|---|
| CR01 | `reagents.dm` | 373 | ✅ | **试剂基类**: name/description/color/metabolization_rate/overdose_threshold/pH/purity/burning_temperature/addiction_types |
| CR02 | `holder/holder.dm` | 820 | ✅ | **试剂容器**: 存储/混合/反应/温度/pH管理 |
| CR03 | `holder/mob_life.dm` | 215 | ✅ | **代谢核心**: metabolize()流程(肝脏/过量/成瘾/on_mob_life) |
| CR04 | `holder/reactions.dm` | 276 | ✅ | **化学反应引擎**: equilibrium系统, 温度/pH/纯度计算 |
| CR05 | `holder/properties.dm` | 164 | 🔍 | 试剂特性: 纯度/杂质/逆化学物 |
| CR06 | `equilibrium.dm` | — | 🔍 | 平衡系统: 反应速率/温度/pH动态 |
| CR07 | `colors.dm` | — | 🔍 | 试剂颜色系统 |
| CR08 | `taste.dm` | — | 🔍 | 味觉系统 |

## 2.2 试剂定义

**根**: `code/modules/reagents/chemistry/reagents/`

| # | 路径 | 行数 | 状态 | 内容 |
|---|---|---|---|---|
| RD01 | `medicine_reagents.dm` | 2,010 | ✅ | **医疗试剂**: Epinephrine/Atropine/Mannitol/Cryoxadone/Synthflesh等~30种, 每个的on_mob_life效果 |
| RD02 | `cat2_medicine_reagents.dm` | 676 | ✅ | **C2进阶医疗**: Helbital/Libital/Probital/Multiver/Penthrite等~15种 |
| RD03 | `toxin_reagents.dm` | 1,765 | ✅ | **毒素试剂**: Cyanide/Histamine/Initropidril/Curare等~20种, 每个的代谢伤害效果 |
| RD04 | `drug_reagents.dm` | 993 | ✅ | **毒品试剂**: Methamphetamine/Krokodil/Bath Salts/Happiness等 |
| RD05 | `pyrotechnic_reagents.dm` | 525 | ✅ | **爆炸试剂**: Gunpowder/NG/RDX/TATP/Thermite/Napalm/Phlogiston等 |
| RD06 | `other_reagents.dm` | 3,602 | ✅ | **其他试剂**: 疫苗/水/基础元素(O/H/C/N等)/工业品(Oil/Acetone/Phenol) |
| RD07 | `food_reagents.dm` | 1,393 | 🔍 | **食物试剂**: Nutriment/Vitamin/各果汁/酒/调料 |
| RD08 | `catalyst_reagents.dm` | 85 | ✅ | 催化剂试剂 |
| RD09 | `reaction_agents_reagents.dm` | 192 | ✅ | 反应剂试剂: Buffer/Prefactor等 |
| RD10 | `impure_reagents.dm` | 125 | ✅ | 杂质试剂 |
| RD11 | `impure_reagents/impure_medicine_reagents.dm` | — | ✅ | 医疗杂质 |
| RD12 | `impure_reagents/impure_toxin_reagents.dm` | — | ✅ | 毒素杂质 |
| RD13 | `atmos_gas_reagents.dm` | 149 | ✅ | 气体试剂: Pluoxium试剂等 |
| RD14 | `drinks/drink_reagents.dm` | — | 🔍 | 饮料试剂 |
| RD15 | `drinks/alcohol_reagents.dm` | — | 🔍 | 酒精试剂 |
| RD16 | `unique/eigenstasium.dm` | — | ✅ | 量子态化学 |

## 2.3 配方 (Reactions)

**根**: `code/modules/reagents/chemistry/recipes/`

| # | 路径 | 行数 | 状态 | 内容 |
|---|---|---|---|---|
| RP01 | `medicine.dm` | 447 | ✅ | **基础医疗配方**: ~30种药: Epinephrine/Atropine/Mannitol/Cryoxadone/Strange Reagent等 |
| RP02 | `cat2_medicines.dm` | 357 | ✅ | **C2医疗配方**: ~15种: Helbital/Libital/Probital/Multiver/Penthrite等 |
| RP03 | `pyrotechnics.dm` | 667 | ✅ | **爆炸配方**: ~33种: NG/RDX/TATP/火药/Thermite/闪光/烟雾/Sorium等 |
| RP04 | `toxins.dm` | 407 | ✅ | **毒素配方**: ~20种: Cyanide/Mutagen/Zombie Powder/Lexorin/Mindbreaker等 |
| RP05 | `drugs.dm` | 208 | ✅ | **毒品配方**: ~8种: Meth/Krokodil/Bath Salts/Aranesp/Happiness等 |
| RP06 | `others.dm` | 1,167 | ✅ | **其他配方**: ~80+种: 病毒/泡沫/清洁/地毯24色/塑料/肥皂/生命等 |
| RP07 | `catalysts.dm` | 98 | ✅ | **催化剂配方**: Thermic Modulator/Ionic Modulator/Medical Speed Catalyst |
| RP08 | `reaction_agents.dm` | 123 | ✅ | **反应剂配方**: Buffer/Prefactor/Purity Tester/Speed Agent |
| RP09 | `special.dm` | 320 | ✅ | **特殊配方**: 随机配方系统/Secret Sauce/Metalgen/Gorgium |
| RP10 | `slime_extracts.dm` | 589 | ⬜ | 黏萃取配方(科研内容, 未读) |

## 2.4 化学设备

**根**: `code/modules/reagents/chemistry/machinery/`

| # | 路径 | 行数 | 状态 | 内容 |
|---|---|---|---|---|
| CM01 | `chem_dispenser.dm` | — | 🔍 | 化学配药机: 自动输出基础化学物 |
| CM02 | `chem_heater.dm` | — | 🔍 | 化学加热器: 控制反应温度 |
| CM03 | `chem_mass_spec.dm` | — | 🔍 | 质谱仪: 分析成分 |
| CM04 | `chem_master.dm` | — | 🔍 | 化学大师: 批量分装+制丸+针剂 |
| CM05 | `chem_separator.dm` | — | 🔍 | 化学分离器: 分离混合液 |
| CM06 | `chem_synthesizer.dm` | — | 🔍 | 化学合成仪: 精准合成 |
| CM07 | `pandemic.dm` | 387 | ✅ | **大流行控制台**: 病毒存档/培养/疫苗制造(20秒冷却)/分析 |
| CM08 | `portable_chem_mixer.dm` | — | 🔍 | 手持混药器 |
| CM09 | `reagentgrinder.dm` | — | 🔍 | 研磨机: 植物/矿石→试剂 |
| CM10 | `smoke_machine.dm` | — | 🔍 | 烟雾机: 喷洒化学烟雾 |

---

# 第三卷 · 生物系统 (Medical / Biology)

## 3.1 疾病系统

**根**: `code/datums/diseases/`

| # | 路径 | 行数 | 状态 | 内容 |
|---|---|---|---|---|
| DS01 | `_disease.dm` | — | ✅ | **疾病基类**: name/spread_flags/cures/stages/severity |
| DS02 | `_MobProcs.dm` | — | 🔍 | 疾病与人物交互 |
| DS03 | `advance/advance.dm` | 516 | ✅ | **进阶疾病**: 自定义病毒, 6症状上限, 属性公式, 存档系统 |
| DS04 | `advance/presets.dm` | — | 🔍 | 预设进阶疾病 |
| DS05 | `advance/symptoms/symptoms.dm` | 136 | ✅ | **症状基类**: stealth/resistance/stage_speed/transmittable/severity, 阈值系统 |
| DS06 | `advance/symptoms/sneeze.dm` | — | ✅ | 打喷嚏: level1, transmittable+3 |
| DS07 | `advance/symptoms/cough.dm` | — | ✅ | 咳嗽: level1, transmittable+3 |
| DS08 | `advance/symptoms/voice_change.dm` | — | ✅ | 声音变化: level1, Positive |
| DS09 | `advance/symptoms/headache.dm` | — | ✅ | 头痛: level1 |
| DS10 | `advance/symptoms/vision.dm` | — | ✅ | 视力: level2 |
| DS11 | `advance/symptoms/dizzy.dm` | — | ✅ | 头晕: level2 |
| DS12 | `advance/symptoms/thermoregulation.dm` | — | ✅ | 体温调节: level2 |
| DS13 | `advance/symptoms/deafness.dm` | — | ✅ | 耳聋: level2, 需Psicodine治愈 |
| DS14 | `advance/symptoms/sensory.dm` | — | ✅ | 感官混乱: level2 |
| DS15 | `advance/symptoms/confusion.dm` | — | ✅ | 精神错乱: level3 |
| DS16 | `advance/symptoms/vomit.dm` | — | ✅ | 呕吐: level3 |
| DS17 | `advance/symptoms/chills.dm` | — | ✅ | 发冷: level3 |
| DS18 | `advance/symptoms/fever.dm` | — | ✅ | 发烧: level3 |
| DS19 | `advance/symptoms/weight.dm` | — | ✅ | 体重变化: level3 |
| DS20 | `advance/symptoms/itching.dm` | — | ✅ | 痒: level3 |
| DS21 | `advance/symptoms/skin.dm` | — | ✅ | 皮肤损伤: level4, Harmful |
| DS22 | `advance/symptoms/hallucigen.dm` | — | ✅ | 幻觉: level4, 需Psicodine |
| DS23 | `advance/symptoms/oxygen.dm` | — | ✅ | 缺氧: level4, Dangerous |
| DS24 | `advance/symptoms/choking.dm` | — | ✅ | 窒息: level4, Dangerous |
| DS25 | `advance/symptoms/bleeding.dm` | — | ✅ | 出血: level4, Dangerous |
| DS26 | `advance/symptoms/narcolepsy.dm` | — | ✅ | 嗜睡: level4 |
| DS27 | `advance/symptoms/genetics.dm` | — | ✅ | 变异: level4, Harmful |
| DS28 | `advance/symptoms/flesh_eating.dm` | — | ✅ | 食肉: level5, BIOHAZARD, 需手术 |
| DS29 | `advance/symptoms/fire.dm` | — | ✅ | 自燃: level5, BIOHAZARD |
| DS30 | `advance/symptoms/youth.dm` | — | ✅ | 青春: level5, Positive(返老还童!) |
| DS31 | `advance/symptoms/shedding.dm` | — | ✅ | 脱毛: level5 |
| DS32 | `advance/symptoms/heal.dm` | — | ✅ | 治疗: level1, Positive(抗性≥6=治愈所有疾病!) |
| DS33 | `advance/symptoms/beard.dm` | — | ✅ | 胡须: level1, Positive, 需Mannitol |
| DS34 | `advance/symptoms/species.dm` | 52 | ✅ | **物种转化**: level5, 包括Necrotic Metabolism(死者体内活动)和Inorganic Biology(感染机器人) |
| DS35 | `advance/symptoms/viral.dm` | — | ✅ | 病毒性: level3 |
| DS36 | `advance/symptoms/disfiguration.dm` | — | ✅ | 变形: level2 |
| DS37 | `advance/symptoms/asphyxiation` (在choking.dm内) | — | ✅ | **窒息变体**: level4, Dangerous |

**预设疾病 (非advance)**:
| # | 路径 | 状态 | 内容 |
|---|---|---|---|
| DI01 | `cold.dm` | ✅ | 普通感冒 |
| DI02 | `cold9.dm` | ✅ | 九号感冒 |
| DI03 | `flu.dm` | ✅ | 流感 |
| DI04 | `fluspanish.dm` | ✅ | 西班牙流感 |
| DI05 | `brainrot.dm` | ✅ | 脑腐烂(Mannitol治愈) |
| DI06 | `gbs.dm` | ✅ | 内出血(BIOHAZARD) |
| DI07 | `fake_gbs.dm` | ✅ | 假GBS |
| DI08 | `magnitis.dm` | ✅ | 磁力症 |
| DI09 | `beesease.dm` | ✅ | **蜜蜂病**: 体内长蜜蜂 |
| DI10 | `anaphylaxis.dm` | ✅ | 过敏性休克 |
| DI11 | `anxiety.dm` | ✅ | 焦虑 |
| DI12 | `asthma_attack.dm` | ✅ | 哮喘(4级:小/中/重/危) |
| DI13 | `adrenal_crisis.dm` | ✅ | 肾上腺危象 |
| DI14 | `chronic_illness.dm` | ✅ | 慢性病 |
| DI15 | `parasitic_infection.dm` | ✅ | 寄生虫 |
| DI16 | `decloning.dm` | ✅ | 克隆退化 |
| DI17 | `dna_spread.dm` | ✅ | DNA扩散 |
| DI18 | `death_sandwich_poisoning.dm` | ✅ | 致命三明治中毒 |
| DI19 | `gastrolisis.dm` | ✅ | 胃溃烂 |
| DI20 | `floor_diseases/carpellosis.dm` | ✅ | 地毯病 |
| DI21 | `floor_diseases/gastritium.dm` | ✅ | 地板胃炎 |
| DI22 | `floor_diseases/nebula_nausea.dm` | ✅ | 地板恶心 |

## 3.2 人体代谢/生命系统

**根**: `code/modules/mob/living/carbon/`

| # | 路径 | 行数 | 状态 | 内容 |
|---|---|---|---|---|
| LF01 | `life.dm` | 800+ | ✅ | **生命循环**: handle_breathing/handle_organs/reagents.metabolize/handle_environment |
| LF02 | `life.dm`(breath) | — | ✅ | **呼吸系统**: breathe()→check_breath(), 每4秒一次, 气体→试剂转化 |
| LF03 | `life.dm`(organs) | — | ✅ | **器官处理**: on_life每tick(心/肺/肝/胃等) |
| LF04 | `life.dm`(temp) | — | ✅ | **体温调节**: natural_bodytemperature_stabilization |

## 3.3 人体化学 (Reagents 对 mob 的效果)

**注意**: 这部分横跨 `reagents/` 下的所有文件, 每个试剂的 `on_mob_life()` 是实际效果:

| 试剂文件 | 效果 |
|---|---|
| `medicine_reagents.dm` | 每种的 healing/damage 计算(例如Epinephrine: 推血0.5/防致死/临界唤醒) |
| `toxin_reagents.dm` | 每种的 poison damage 计算(Cyanide: 3.5毒/tick + 氧损1.5) |
| `drug_reagents.dm` | Meth: 加速1.5x + 毒素; Krokodil: 皮肤坏死 |
| `other_reagents.dm` | Vaccine: 免疫添加; Water: 补水; 基础元素代谢 |
| `food_reagents.dm` | Nutriment: 营养恢复; Vitamin: 维生素 |

---

# 第四卷 · 水培系统 (Hydroponics)

**根**: `code/modules/hydroponics/`

| # | 路径 | 行数 | 状态 | 内容 |
|---|---|---|---|---|
| HY01 | `trays/tray.dm` | — | ✅ | **水培箱核心**: mutate()/harvest/process/water/nutrient/genes |
| HY02 | `trays/tray_soil.dm` | — | ✅ | 土壤系统: 4种土属性 |
| HY03 | `grown/` (45+文件) | 6,000+ | ✅ | **49+种作物定义**: 每种的数据(生长周期/产量/产物/基因) |
| HY04 | `seeds.dm` | — | ✅ | **种子系统**: seed datum/突变/属性 |
| HY05 | `gene.dm` | — | ✅ | **基因系统**: 20+特性/8大特殊基因类 |
| HY06 | `fermenting_barrel.dm` | — | ✅ | 发酵桶 |
| HY07 | `hydro_tank.dm` | — | ✅ | 水箱系统 |
| HY08 | `hydroponics_plumbing.dm` | — | ✅ | 管道连接 |
| HY09 | `bee_box.dm` | — | ✅ | 蜂箱/蜜蜂 |
| HY10 | ``__DEFINES/botany.dm` | 70 | ✅ | 水培常量 |
| HY11 | `__HELPERS/botany.dm` | — | ✅ | 水培辅助函数 |
| HY12 | `datums/components/plumbing/hydroponics.dm` | — | ✅ | 水培管道组件 |
| HY13 | `datums/elements/plant_backfire.dm` | — | ✅ | 植物反伤元素 |

**扩展植物**:
| # | 路径 | 状态 | 内容 |
|---|---|---|---|
| HY14 | `modular_nova/.../hydroponics/grown/*` | ✅ | Nova扩展植物(7种) |
| HY15 | `modular_nova/.../xenoarch/.../hydroponics/*` | ✅ | **外星考古植物(10种)** |
| HY16 | `modular_nova/.../morenarcotics/*` | ✅ | 毒品扩展(古柯叶/鸦片) |
| HY17 | `mining/lavaland/ash_flora.dm` | 488 | ✅ | **熔岩地植物(9种)** |

---

# 第五卷 · 职业系统 (Jobs)

**根**: `code/modules/jobs/job_types/`

| # | 路径 | 行数 | 状态 | 内容 |
|---|---|---|---|---|
| JB00 | `_job.dm` | 732 | ✅ | **职业基类**: title/description/outfit/skills/departments/paycheck/mail_goodies |
| JB01 | `captain.dm` | — | ✅ | 船长 |
| JB02 | `head_of_personnel.dm` | — | ✅ | 人事部长 |
| JB03 | `head_of_security.dm` | — | ✅ | 安全部长 |
| JB04 | `warden.dm` | — | ✅ | 看守 |
| JB05 | `security_officer.dm` | — | ✅ | 保安官 |
| JB06 | `detective.dm` | — | ✅ | 侦探 |
| JB07 | `lawyer.dm` | — | ✅ | 律师 |
| JB08 | `chief_engineer.dm` | — | ✅ | 总工程师 |
| JB09 | `station_engineer.dm` | — | ✅ | 驻站工程师 |
| JB10 | `atmospheric_technician.dm` | — | ✅ | 大气技师 |
| JB11 | `chief_medical_officer.dm` | — | ✅ | 首席医疗官 |
| JB12 | `medical_doctor.dm` | — | ✅ | 医生 |
| JB13 | `chemist.dm` | — | ✅ | 化学师 |
| JB14 | `virologist.dm` | — | ✅ | 病毒学家 |
| JB15 | `geneticist.dm` | — | ✅ | 基因学家 |
| JB16 | `coroner.dm` | — | ✅ | 法医 |
| JB17 | `psychologist.dm` | 61 | ✅ | **心理医生**: TRAIT_SUPERMATTER_SOOTHER, 5瓶药, skillchip |
| JB18 | `paramedic.dm` | — | ✅ | 急救员 |
| JB19 | `research_director.dm` | — | ✅ | 科研部长 |
| JB20 | `scientist.dm` | — | ✅ | 科学家 |
| JB21 | `roboticist.dm` | — | ✅ | 机器人学家 |
| JB22 | `quartermaster.dm` | — | ✅ | 军需官 |
| JB23 | `cargo_technician.dm` | — | ✅ | 货运技师 |
| JB24 | `shaft_miner.dm` | — | ✅ | **矿工**: Lavaland勘探, 采矿券 |
| JB25 | `bartender.dm` | — | ✅ | 调酒师 |
| JB26 | `cook.dm` | — | ✅ | 厨师 |
| JB27 | `botanist.dm` | — | ✅ | **植物学家**: 49+种作物, 基因编辑 |
| JB28 | `janitor.dm` | — | ✅ | 清洁工 |
| JB29 | `clown.dm` | — | ✅ | 小丑: 免疫滑倒 |
| JB30 | `mime.dm` | — | ✅ | 哑剧: 沉默誓言, 隐形墙 |
| JB31 | `curator.dm` | — | ✅ | 馆长 |
| JB32 | `chaplain.dm` | — | ✅ | **牧师**: 自定义神杖, 反亡灵 |
| JB33 | `prisoner.dm` | — | ✅ | 囚犯 |
| JB34 | `ai.dm` | — | ✅ | AI: 全站摄像头/门控 |
| JB35 | `cyborg.dm` | — | ✅ | 机械体: 模块化工具 |
| JB36 | `unassigned.dm` | — | ✅ | 助理 |

**Nova 扩展**:
所有在 `modular_nova/master_files/code/modules/jobs/job_types/` 下的文件都是Nova对基础职业的改写。

**幽灵角色 (Ghost Roles)**:
| 路径 | 说明 |
|---|---|
| `job_types/spawner/ash_walker.dm` | 灰烬行者 |
| `job_types/spawner/hermit.dm` | 隐士 |
| `job_types/spawner/space_pirate.dm` | 太空海盗 |
| `job_types/spawner/zombie.dm` | 丧尸(幽灵角色) |
| `job_types/spawner/skeleton.dm` | 骷髅 |
| `job_types/spawner/free_golem.dm` | 自由魔像 |
| (共~30个幽灵角色文件) | |

**反派**:
| 路径 | 说明 |
|---|---|
| `job_types/antagonists/nuclear_operative.dm` | 核特工 |
| `job_types/antagonists/space_ninja.dm` | 太空忍者 |
| `job_types/antagonists/wizard.dm` | 巫师 |
| `job_types/antagonists/xenomorph.dm` | 异形 |
| (共~15个反派文件) | |

---

# 第六卷 · 供应与采矿 (Cargo / Mining)

## 6.1 供应系统

**根**: `code/modules/cargo/`

| # | 路径 | 行数 | 状态 | 内容 |
|---|---|---|---|---|
| CG01 | `orderconsole.dm` | 554 | ✅ | **供应控制台**: 下单/审批/穿梭机控制 |
| CG02 | `order.dm` | 238 | ✅ | 订购系统: manifest/requisition/supply_order |
| CG03 | `packs/_packs.dm` | 190 | ✅ | **采购包基类**: name/cost/contains/crate_type |
| CG04 | `packs/engineering.dm` | — | 🔍 | 工程采购包 |
| CG05 | `packs/medical.dm` | — | 🔍 | 医疗采购包 |
| CG06 | `packs/science.dm` | — | 🔍 | 科研采购包 |
| CG07 | `packs/security.dm` | — | 🔍 | 安保采购包 |
| CG08 | `packs/materials.dm` | — | 🔍 | 物料采购包 |
| CG09 | `packs/organic.dm` | — | 🔍 | 有机/植物采购包 |
| CG10 | `packs/livestock.dm` | — | 🔍 | 活体动物采购包 |
| CG11 | `packs/service.dm` | — | 🔍 | 服务采购包 |
| CG12 | `packs/general.dm` | — | 🔍 | 通用采购包 |
| CG13 | `packs/emergency.dm` | — | 🔍 | 紧急采购包 |
| CG14 | `packs/costumes_toys.dm` | — | 🔍 | 服饰玩具采购包 |
| CG15 | `packs/imports.dm` | — | 🔍 | 进口采购包 |
| CG16 | `exports.dm` | — | 🔍 | 出口系统基类 |
| CG17 | `exports/materials.dm` | — | 🔍 | 矿物出口 |
| CG18 | `exports/lavaland.dm` | — | 🔍 | Lavaland特产出口 |
| CG19 | `bounty.dm` | — | 🔍 | 赏金系统 |
| CG20 | `bounties/` (15文件) | — | 🔍 | 各部门赏金 |
| CG21 | `supplypod.dm` | — | 🔍 | 供应舱系统 |

## 6.2 采矿系统

**根**: `code/modules/mining/`

| # | 路径 | 行数 | 状态 | 内容 |
|---|---|---|---|---|
| MN01 | `ores_coins.dm` | 738 | ✅ | **矿石定义**: 铁/铀/等离子/金/银/钻石/蓝水晶等, points/refined_type |
| MN02 | `machine_redemption.dm` | 423 | ✅ | **矿石赎还机(ORM)**: 矿石→锭+点数 |
| MN03 | `machine_processing.dm` | 290 | ✅ | 矿料加工机 |
| MN04 | `machine_stacking.dm` | — | 🔍 | 矿石堆叠机 |
| MN05 | `machine_silo.dm` | — | 🔍 | 矿仓 |
| MN06 | `equipment/mining_tools.dm` | 389 | ✅ | 采矿工具: 激光/钻头/勘探仪/共振器 |
| MN07 | `equipment/kinetic_crusher/` | — | ✅ | **动能粉碎器**: 近战挖矿+战利品系统 |
| MN08 | `equipment/lazarus_injector.dm` | 68 | ✅ | **Lazarus注射器**: 复活怪物当宠物 |
| MN09 | `equipment/resonator.dm` | — | ✅ | 共振器 |
| MN10 | `equipment/grapple_gun.dm` | — | ✅ | 抓手枪 |
| MN11 | `equipment/mineral_scanner.dm` | — | ✅ | 勘探分析仪 |
| MN12 | `equipment/monster_organs/` | — | ✅ | 怪物器官: 再生核心/Brimdust囊/冲刺腺 |
| MN13 | `equipment/survival_pod.dm` | — | ✅ | 生存小屋 |
| MN14 | `equipment/wormhole_jaunter.dm` | — | ✅ | 虫洞跳跃器 |
| MN15 | `equipment/fulton.dm` | — | ✅ | Fulton回收系统 |
| MN16 | `voucher_sets.dm` | 111 | ✅ | 矿工券兑换系统 |
| MN17 | `shelters.dm` | — | ✅ | 避难所 |

## 6.3 Lavaland

**根**: `code/modules/mining/lavaland/`

| # | 路径 | 行数 | 状态 | 内容 |
|---|---|---|---|---|
| LV01 | `ash_flora.dm` | 488 | ✅ | 灰烬植物(9种) |
| LV02 | `necropolis_chests.dm` | 138 | ✅ | **死灵宝箱**: tendril/demonic/dragon/bubblegum/colossus/puzzling, 骷髅钥匙 |
| LV03 | `mining_loot/consumables.dm` | 243 | ✅ | **Lavaland战利品**: KA模组盘/Wisp Lantern/Jacob's Ladder/Babel书/飞行药水 |
| LV04 | `mining_loot/equipment.dm` | — | ✅ | Lavaland装备 |
| LV05 | `mining_loot/megafauna/ash_drake.dm` | — | ✅ | **灰烬龙**: 火焰吐息, 龙鳞甲 |
| LV06 | `mining_loot/megafauna/bubblegum.dm` | — | ✅ | **泡泡糖**: 血池之王, H.E.C.K.套 |
| LV07 | `mining_loot/megafauna/colossus.dm` | — | ✅ | **巨像**: 能量弹, 异常水晶+声带+该隐亚伯 |
| LV08 | `mining_loot/megafauna/hierophant.dm` | — | ✅ | **圣职者**: 传送攻击, 传送杖 |
| LV09 | `mining_loot/megafauna/legion.dm` | — | ✅ | **军团核心**: 骷髅王 |
| LV10 | `mining_loot/megafauna/blood_drunk.dm` | — | ✅ | **血醉者**: 狂暴矿工 |
| LV11 | `mining_loot/megafauna/demonic_frost_miner.dm` | — | ✅ | **恶魔冰矿工**: 冰霜 |
| LV12 | `mining_loot/megafauna/the_thing.dm` | — | ✅ | **怪物**: 不可名状 |
| LV13 | `mining_loot/megafauna/wendigo.dm` | — | ✅ | **温迪戈**: 食人冰兽 |

---

# 第七卷 · 工具与公共系统 (Tools / Common)

## 7.1 定义文件

| # | 路径 | 内容 |
|---|---|---|
| DF01 | `code/__DEFINES/jobs.dm` | 职业相关常量 |
| DF02 | `code/__DEFINES/diseases.dm` | **疾病常量**: 传播方式(6种位掩码)/严重度8级/可见性/病毒限制 |
| DF03 | `code/__DEFINES/reactions.dm` | 反应系统常量 |
| DF04 | `code/__DEFINES/supermatter.dm` | SM常量(已读) |
| DF05 | `code/__DEFINES/gravity.dm` | 奇点常量(已读) |
| DF06 | `code/__DEFINES/surgery.dm` | 手术系统常量(器官标志等) |

## 7.2 ID权限

| # | 路径 | 内容 |
|---|---|---|
| ID01 | `code/datums/id_trim/jobs.dm` | **ID权限定义**: 每个职业的卡权限, trim_state/sechud_icon_state |

## 7.3 心理学

| # | 路径 | 内容 |
|---|---|---|
| PS01 | `code/modules/psychology/psychology_defines.dm` | 心理学定义 |
| PS02 | `code/modules/psychology/psychology.dm` | 心理系统 |
| PS03 | `code/modules/psychology/psychiatrist_roundstart.dm` | 心理医生轮次开始 |
| PS04 | `code/modules/psychology/mental_attacks.dm` | 心智攻击物 |
| PS05 | `code/modules/psychology/mental_attacks_types.dm` | 心智攻击类型 |
| PS06 | `code/modules/psychology/psych_trauma.dm` | 精神创伤 |
| PS07 | `code/modules/psychology/psych_trauma_types.dm` | 精神创伤类型 |

## 7.4 丧尸系统

| # | 路径 | 行数 | 状态 | 内容 |
|---|---|---|---|---|
| ZM01 | `code/modules/mob/living/carbon/human/species_types/zombies.dm` | 206 | ✅ | **丧尸种族**: High-Functioning(万圣节)/Infectious(传染型)/Mindless(无脑) |
| ZM02 | `code/modules/surgery/operations/operation_zombie.dm` | 76 | ✅ | **生物坏死手术**: 开颅+Rezadone/Zombie Powder → Romerol肿瘤 |
| ZM03 | `code/modules/zombie/organs.dm` | 135 | ✅ | **丧尸感染器官**: zombie_infection, 死后45~70秒转化 |
| ZM04 | `code/modules/zombie/items.dm` | — | ✅ | 丧尸物品 |

## 7.5 服装

| # | 路径 | 内容 |
|---|---|---|
| CL01 | `code/modules/clothing/under/jobs/*.dm` | 各职业制服 |
| CL02 | `code/modules/clothing/head/jobs.dm` | 职业头饰 |
| CL03 | `code/modules/clothing/suits/jobs.dm` | 职业外套 |

---

# 附录

## A. 未读但可能相关的大块

| 路径 | 行数 | 说明 |
|---|---|---|
| `code/modules/research/` | ~5,000+ | **科研系统**: 科技树/实验/设计图(水培科技树已读) |
| `code/modules/power/` (非singularity/supermatter) | ~3,000+ | 电力网络/TEG/太阳能/反应堆 |
| `code/modules/surgery/` | ~3,000+ | **手术系统**: 各手术类型 |
| `code/modules/reagents/chemistry/recipes/slime_extracts.dm` | 589 | 史莱姆萃取(用户说跳过) |
| `code/modules/events/` | ~2,000+ | 游戏事件 |
| `code/modules/mob/living/` | ~10,000+ | 所有生物AI |
| `code/modules/bitrunning/` | ~2,000+ | 虚拟域系统 |

## B. 已输出文档列表

| # | 文件 | 大小 | 涵盖 |
|---|---|---|---|
| 01 | 工程气体01-20种气体解析 | 15KB | 20气体+属性+效果+SM影响 |
| 02 | 工程气体02-气体反应全链路解析 | 15KB | 25+反应+生产链+策略 |
| 03 | 工程气体03-管道机械深度解析 | 12KB | 12组件+颜色+管网+警报 |
| 03b | 工程气体03b-压力安全系统 | 9KB | 3容器爆炸+抑爆+安全策略 |
| 04 | 工程气体04-超物质引擎解析 | 10KB | SM核心+6步循环+气体影响 |
| 04b | 工程气体04b-超物质引擎深度解析 | 10KB | 能量/高能/辐射/Cascade |
| 04c | 工程气体04c-超物质查漏补缺 | 8KB | 温度纠正+气体加权+完整验证 |
| 05 | 工程气体05-HFR聚变反应堆百科 | 22KB | 7燃料/PL判定/公式/损伤/熔毁/冷却 |
| 06 | 工程气体06-实验军火系统 | 9KB | TTV/压缩机/多普勒/科研 |
| 07 | 工程气体07-结晶器与电解器配方 | 5KB | 19结晶配方+3电解反应 |
| 08 | 工程08-奇点引擎解析 | 6KB | 6级/吞噬/约束场 |
| — | 工程气体-全量解析索引 | 6KB | 所有文档速查 |
| — | 工程气体-百科总章 | **79KB** | 13篇合并总章 |
| — | TTV炸弹制造指南 | 15KB | TTV制造全流程+清洗+配比 |
| 化学01-医疗配方百科 | 8KB | 53种医疗药+急救策略 |
| 化学02-爆炸与毒素百科 | 11KB | 33爆炸+20毒素+8毒品+策略 |
| 化学03-其他化学品百科 | 9KB | 病毒/泡沫/工业/生物/24色地毯/随机配方 |
| 水培种植百科(终版) | 45KB | 80种植物+基因+系统全解 |
| 水培勘误与最终补充 | 7KB | 常量修正+遗漏植物 |
| 水培最终补丁 | 6KB | 最后9种植物 |
| 病毒学百科 | 12KB | 23预设病+33症状+进阶病毒 |
| 病毒学疫苗制造百科 | 6KB | 疫苗制作/免疫/多联苗/防疫 |
| 人体代谢系统解析 | 9KB | 3途径+肝脏/过量/成瘾/温度/死代 |
| 职业百科 | 17KB | 40+职业+装备+特质 |
| Lavaland熔岩地百科 | 10KB | 环境/矿工/怪物/9巨兽/宝箱 |
| 供应与采矿百科 | 9KB | 供应系统/采矿/矿石加工 |
| 心理医生专精 | 5KB | SM心理+5药+技能芯片 |
| 本次: 源代码结构索引 | — | 全量文件索引(已读+未读) |

**总输出: ~400KB 文档, 涵盖 ~65+ 个直接读取的文件, 间接引用 ~200+ 个关联文件**。
