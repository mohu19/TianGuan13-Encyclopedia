# TianGuan13 变种怪感染百科 (Mutant Infection Encyclopedia)

> 基于 TianGuan13 NovaSector 分支源码全量整理。变种怪（Mutant / HNZ-1）系统位于 `modular_nova/modules/mutants/`，共 **7 个代码文件、1,036 行**：
> `mutant_antag_datum.dm`（21 行）· `mutant_component.dm`（193 行）· `mutant_cure.dm`（375 行）· `mutant_species.dm`（284 行）· `mutant_event.dm`（48 行）· `mutant_techweb.dm`（61 行）· `mutant_zombie_bodyparts.dm`（54 行）。
> 本文档全量列出：变种怪阵营、感染组件全机制、治疗与抵御全流程、3 种变体 + 基础种 + 爪/躯体全录、事件与科技，无省略，数值精确。
> 模块来源：Skyrat-SS13/Skyrat-tg PR #4664，原作者 Gandalf2k15。

## 目录 (Table of Contents)

- [第一卷 · 变种怪阵营 (Antagonist)](#第一卷--变种怪阵营-antagonist)
  - [1.1 阵营数据](#11-阵营数据)
  - [1.2 阵营授予 (on_gain)](#12-阵营授予-ongain)
- [第二卷 · 感染组件 (Infection Component)](#第二卷--感染组件-infection-component)
  - [2.1 全局常量](#21-全局常量)
  - [2.2 组件属性](#22-组件属性)
  - [2.3 初始化 (Initialize)](#23-初始化-initialize)
  - [2.4 销毁 (Destroy)](#24-销毁-destroy)
  - [2.5 逐帧处理 (process)](#25-逐帧处理-process)
  - [2.6 转化 (transform_host)](#26-转化-transformhost)
  - [2.7 死亡与复活 (mutant_death / regenerate)](#27-死亡与复活-mutantdeath--regenerate)
  - [2.8 治愈与移除感染 (cure_host / remove_infection)](#28-治愈与移除感染-curehost--removeinfection)
  - [2.9 RNA 提取 (extract_rna / refresh_rna)](#29-rna-提取-extractrna--refreshrna)
  - [2.10 感染光效 (create_glow)](#210-感染光效-createglow)
- [第三卷 · 治疗与抵御 (Cure)](#第三卷--治疗与抵御-cure)
  - [3.1 RNA 提取器 (rna_extractor)](#31-rna-提取器-rnaextractor)
  - [3.2 RNA 小瓶 (rna_vial)](#32-rna-小瓶-rnavial)
  - [3.3 HNZ-1 治愈药瓶 (hnz_cure)](#33-hnz-1-治愈药瓶-hnzcure)
  - [3.4 RNA 重组机 (rna_recombinator)](#34-rna-重组机-rnarecombinator)
  - [3.5 HNZ-1 病毒试剂 (/datum/reagent/hnz)](#35-hnz-1-病毒试剂-datumreagenthnz)
  - [3.6 HNZ-1 瓶装试剂与生物容器](#36-hnz-1-瓶装试剂与生物容器)
- [第四卷 · 变种怪类型与变体全录 (Species)](#第四卷--变种怪类型与变体全录-species)
  - [4.1 变体总览](#41-变体总览)
  - [4.2 基础变种怪 (High-Functioning mutant)](#42-基础变种怪-high-functioning-mutant)
  - [4.3 感染型变种怪 (Mutated Abomination)](#43-感染型变种怪-mutated-abomination)
  - [4.4 快速变体 (Fast Mutated Abomination)](#44-快速变体-fast-mutated-abomination)
  - [4.5 缓慢变体 (Slow Mutated Abomination)](#45-缓慢变体-slow-mutated-abomination)
  - [4.6 共通机制（再生/体温/眩晕/手铐）](#46-共通机制再生体温眩晕手铐)
  - [4.7 感染判定 (try_to_mutant_infect)](#47-感染判定-trytomutantinfect)
  - [4.8 治愈判定 (try_to_mutant_cure)](#48-治愈判定-trytomutantcure)
  - [4.9 变种怪之爪 (hnz_mutant_hand)](#49-变种怪之爪-hnzmutanthand)
  - [4.10 进食盛宴 (check_feast)](#410-进食盛宴-checkfeast)
  - [4.11 变种怪躯体部件全录](#411-变种怪躯体部件全录)
- [第五卷 · 事件与科技 (Event & Techweb)](#第五卷--事件与科技-event--techweb)
  - [5.1 中期事件：HNZ-1 病原体爆发](#51-中期事件hnz-1-病原体爆发)
  - [5.2 科技节点 (mutanttech)](#52-科技节点-mutanttech)
  - [5.3 设计图纸全录（3 张）](#53-设计图纸全录3-张)
- [附录 · 源码路径索引](#附录--源码路径索引)

---

# 第一卷 · 变种怪阵营 (Antagonist)

**源码**: `modular_nova/modules/mutants/code/mutant_antag_datum.dm`（21 行）

## 1.1 阵营数据

`/datum/antagonist/mutant`（变种怪敌对阵营）：

| 属性 | 值 | 说明 |
|---|---|---|
| `name` | `"Mutated Abomination"`（变异可憎之物） | 阵营显示名 |
| `pref_flag` | `ROLE_MUTANT` | 偏好开关 |
| `roundend_category` | `"mutants"` | 结算分类 |
| `antagpanel_category` | `"Mutant"` | 反恐面板分类 |
| `show_in_antagpanel` | `TRUE` | 反恐面板显示 |
| `antag_hud_name` | `"mutant"` | HUD 名称 |
| `hud_icon` | `'modular_nova/modules/mutants/icons/antag_hud.dmi'` | HUD 图标 |
| `antag_memory` | 见下方原文 | 阵营记忆（开场提示） |

**阵营记忆原文**（`antag_memory`，开场逐字发给玩家）：

> "You are a mutated abomination. You yearn for flesh. Your mind is torn apart, you do not remember who you are. All you know is that you want to kill. You retain some capability to reason. Being friendly or helping crew will result in punishment. Attacking your fellow zombies will result in punishment. Hindering your fellow zombies will result in punishment."
>
> （你是被变异的可憎之物。你渴望血肉。你的心智已被撕裂，你不记得自己是谁。你只知道你想杀戮。你保留一定的推理能力。对船员友善或提供帮助将受到惩罚。攻击你的同类僵尸将受到惩罚。妨碍你的同类僵尸将受到惩罚。）

## 1.2 阵营授予 (on_gain)

`on_gain()` 流程：

1. 若宿主身上没有 `/datum/component/mutant_infection` 组件 → 自动补加（`AddComponent`）。
2. 向玩家发送 `antag_memory` 全文（`span_boldannounce` 加粗红色大字提示）。

---

# 第二卷 · 感染组件 (Infection Component)

**源码**: `modular_nova/modules/mutants/code/mutant_component.dm`（193 行）

> `/datum/component/mutant_infection` 组件负责"人 → 僵尸（变种怪）"转化的全部中间过程。所有时间常量见 2.1。

## 2.1 全局常量

| 常量 | 值 | 用途 |
|---|---|---|
| `CURE_TIME` | **10 秒** | 治愈后组件延迟销毁时间 |
| `REVIVE_TIME_LOWER` | **2 分钟** | 复活计时下限（随机） |
| `REVIVE_TIME_UPPER` | **3 分钟** | 复活计时上限（随机） |
| `IMMUNITY_LOWER` | **5 分钟** | 治愈后免疫时长下限（随机） |
| `IMMUNITY_UPPER` | **10 分钟** | 治愈后免疫时长上限（随机） |
| `RNA_REFRESH_TIME` | **2 分钟** | RNA 可再次提取的冷却时间 |

## 2.2 组件属性

| 变量 | 默认值 | 说明 |
|---|---|---|
| `host` | — | 宿主（`mob/living/carbon/human`）引用 |
| `old_species` | `/datum/species/human` | 感染前种族，治愈时用于还原 |
| `mutant_species` | `list(/datum/species/mutant/infectious/fast, /datum/species/mutant/infectious/slow)` | 可选转化变体（快速 / 缓慢） |
| `selected_type` | — | 转化后选定的种族 |
| `insanity_phrases` | 3 条（见下） | 转化期间发送的疯狂呓语 |
| `timer_id` | — | 各阶段计时器 ID |
| `rna_extracted` | `FALSE` | 是否已提取过 RNA |
| `tox_loss_mod` | **0.5** | 每 tick 毒素伤害系数 |

**疯狂呓语原文**（`insanity_phrases`，转化期间随机发送）：

- `"You feel too hot! Something isn't right!"`（你感觉太热了！有什么不对劲！）
- `"You can't think straight, please end the suffering!"`（你无法正常思考，请结束这痛苦吧！）
- `"AAAAAAAAAAAAAAAGHHHHHHHH!"`（啊啊啊啊啊啊啊啊啊！）

## 2.3 初始化 (Initialize)

1. **仅限人类**：`parent` 非 `ishuman` → 返回 `COMPONENT_INCOMPATIBLE`（组件不兼容，自动卸载）。
2. `host = parent`。
3. **若宿主已是 DEAD（尸体感染）**：随机 2~3 分钟（`rand(REVIVE_TIME_LOWER, REVIVE_TIME_UPPER)`）后执行 `transform_host` 转化，并发送 `span_userdanger` 警告。
4. 注册信号 `COMSIG_MUTANT_CURED` → `cure_host`（治愈信号）。
5. 启动处理：`START_PROCESSING(SSobj, src)`（加入 SSobj 每 tick 处理）。

## 2.4 销毁 (Destroy)

1. `STOP_PROCESSING(SSobj, src)` 停止处理。
2. 注销信号 `COMSIG_MUTANT_CURED`、`COMSIG_LIVING_DEATH`。
3. 若有 `timer_id` → `deltimer` 取消并置空。
4. 若有 `host` → 执行 `remove_infection()`（还原种族/复活/免疫），置空 `host`。

## 2.5 逐帧处理 (process)

`process(seconds_per_tick)` 按 `seconds_per_tick`（SPT）推进：

**A. 未转化且存活时（人类感染阶段）：**

| 毒素值 | 效果 |
|---|---|
| `tox_loss < 50` | 毒素伤害 +`0.5 × SPT`/秒；`SPT_PROB(5)` → 发送 `span_userdanger` 提示 + `Paralyze(10)`（麻痹 10 tick） |
| `tox_loss ≥ 50` | 毒素伤害 +`1.0 × SPT`/秒（`0.5 × 2`）；`SPT_PROB(10)` → 在胸部（`BODY_ZONE_CHEST`）施加伤口 `/datum/wound/slash/flesh/moderate`（中度撕裂肉伤，`rotting_wound`）+ 随机表情 `cough`（咳嗽）/ `sneeze`（喷嚏）/ `scream`（尖叫） |

**B. 死亡阶段：**

1. 若已有转化计时器（`timer_id`）→ 直接返回。
2. 宿主存活 → 返回。
3. 宿主未转化 → 发送 `span_cult_large` 提示（"你将崛起"类文字）。
4. 随机 2~3 分钟 → `transform_host`（尸体/濒死者的转化倒计时）。

## 2.6 转化 (transform_host)

1. `timer_id = null` 清计时器。
2. `selected_type = pick(mutant_species)` —— **从快速 / 缓慢两种变体中随机二选一**。
3. 未转化则记录 `old_species = host.dna.species`，执行 `host.set_species(selected_type)`。
4. `stand_up = (stat == DEAD) || (stat == UNCONSCIOUS)`：死者/昏迷者将以"踉跄起身"姿态复活。
5. 首次起身**完全治疗全部伤害**（`regenerate()`）。
6. `do_jitter_animation(30)` 抖动动画；`visible_message`（起身描述，`span_danger`）；`playsound('sound/effects/hallucinations/far_noise.ogg', 50)`。
7. 按变体发送提示：快速 → `datum.797e17c9`；缓慢 → `datum.198293c1`（均为 `span_redtext`），再发 `span_alertalien` 提示。
8. `host.mind?.add_antag_datum(/datum/antagonist/mutant)` —— **授予变种怪阵营**。
9. `create_glow()` 红色描边光效。
10. 注册信号 `COMSIG_LIVING_DEATH` → `mutant_death`（转化后的死亡处理）。

## 2.7 死亡与复活 (mutant_death / regenerate)

**`mutant_death()`**（转化后宿主死亡触发）：
- 随机 2~3 分钟 → `regenerate()`，发送 `span_cult_large` 提示。

**`regenerate()`**（复活 + 找玩家接管）：
1. **无 mind（无玩家控制）**：`SSpolling.poll_ghosts_for_target` 向幽灵广播招募——
   - 标题：`"Do you want to play as a mutant([host.name])?"`（你想扮演变种怪 [名字] 吗？）
   - `role_name_text = "mutant [host.name]"`，`alert_pic = host`；
   - 无候选人 → 直接返回；有则 `pick_n_take(candidates)` 随机取一人，`host.PossessByPlayer(C.key)` 附身。
2. **有 mind**：`host.grab_ghost()` 拉回灵魂。
3. `playsound('sound/effects/magic/demon_consume.ogg', 50)`（恶魔吞噬音效）。
4. `host.revive(TRUE, TRUE)` 强制完全复活。

## 2.8 治愈与移除感染 (cure_host / remove_infection)

**`cure_host()`**（收到 `COMSIG_MUTANT_CURED` 信号时）：
- 代码原样判断 `if(!host.stat == DEAD)` → 发送 `span_notice` 治愈提示（"感染正在消退"类）。
- `STOP_PROCESSING(SSobj, src)`；**10 秒后**（`CURE_TIME`）销毁组件 `Destroy()`。

**`remove_infection()`**（销毁时调用，治愈落地执行）：
1. 若已变种且记录过 `old_species` → `host.set_species(old_species)` **还原原种族**。
2. `host.grab_ghost()` 召回灵魂。
3. `host.revive(TRUE, TRUE)` 强制复活。
4. 发送 `span_greentext` 治愈成功提示。
5. `ADD_TRAIT(host, TRAIT_MUTANT_IMMUNE, "mutant_virus")` —— **授予变种怪免疫特质**。
6. `host.mind?.remove_antag_datum(/datum/antagonist/mutant)` 移除阵营。
7. `host.remove_filter("infection_glow")` 移除红色描边；`update_appearance()`。
8. 随机 **5~10 分钟**（`IMMUNITY_LOWER`~`IMMUNITY_UPPER`）后执行 `remove_mutant_immunity()` 移除免疫特质（`REMOVE_TRAIT(src, TRAIT_MUTANT_IMMUNE, "mutant_virus")`）。

> 免疫期间（5~10 分钟随机）该角色无法再被感染；期间可被重新提取 RNA。

## 2.9 RNA 提取 (extract_rna / refresh_rna)

- **`extract_rna()`**：已提取过（`rna_extracted`）→ 返回 `FALSE`（拒绝）；否则置 `TRUE`、发送 `span_userdanger` 提示，**2 分钟后**（`RNA_REFRESH_TIME`）`refresh_rna()` 重置为可再次提取，返回 `TRUE`。
- **`refresh_rna()`**：`rna_extracted = FALSE`。

> 用途：供 RNA 提取器（见第三卷）从变种怪身上采集病毒 RNA，进而合成治愈剂或新病毒。

## 2.10 感染光效 (create_glow)

- `create_glow()`：宿主加滤镜 `"infection_glow"` —— `type = "outline"`（描边）、`color = COLOR_RED`（红色）、`size = 2`；随后随机延迟 **0.1~1.9 秒** 启动光效循环。
- `start_glow_loop()`：滤镜透明度动画循环 —— `alpha = 110`（1.5 秒，`loop = -1` 无限循环）→ `alpha = 40`（2.5 秒），形成呼吸式红色脉动轮廓。

---

# 第三卷 · 治疗与抵御 (Cure)

**源码**: `modular_nova/modules/mutants/code/mutant_cure.dm`（375 行）

> 完整治愈链条：**RNA 提取器（从变种怪身上采 RNA）→ RNA 小瓶 → RNA 重组机（合成）→ HNZ-1 治愈药瓶（注射/涂抹目标）→ 感染组件被治愈**。重组机也可反向合成 HNZ-1 病毒试剂。

## 3.1 RNA 提取器 (rna_extractor)

`/obj/item/rna_extractor`（高级病毒 RNA 提取器）

| 属性 | 值 |
|---|---|
| `name` | `"advanced virus RNA extractor"`（高级病毒 RNA 提取器） |
| `desc` | `"A tool used to extract the RNA from viruses. Apply to skin."`（用于提取病毒 RNA 的工具，涂抹在皮肤上使用） |
| `icon` / `icon_state` | `'extractor.dmi'` / `"extractor"` |
| 材料 | 铁 ×2 片（`SHEET_MATERIAL_AMOUNT * 2`）、金 ×2 片、铀 ×½ 片（`HALF_SHEET_MATERIAL_AMOUNT`）、钻石 ×½ 片 |
| `loaded_vial` | 已装入的 RNA 小瓶引用 |

**交互逻辑**：

| 动作 | 条件 | 结果 |
|---|---|---|
| 插入小瓶（`item_interaction`） | 已装有小瓶 → 拒绝（`ITEM_INTERACT_BLOCKING` + 警告）；空瓶 + `transferItemToLoc` 成功 → 装入、播放 `autoguninsert.ogg` 音效、更新外观 | 成功装入 |
| 主动使用（`attack_self`） | 未失能（`!user.incapacitated`）→ `unload_vial` | 取出小瓶 |
| 对目标使用（`interact_with_atom`） | 见下方 5 项检查 | 提取 RNA |

**提取 RNA 五重检查**（全部通过才成功）：
1. 目标必须是人类（`ishuman`）。
2. 必须已装入小瓶（否则 `span_danger` 提示）。
3. 小瓶必须为空（`!contains_rna`，否则提示已含 RNA）。
4. 目标必须是变种怪（`ismutant`，否则提示"目标不是变种"）。
5. 目标必须带有 `mutant_infection` 组件（否则同样提示）。

成功后：`target_infection.extract_rna()` 返回 `TRUE` → `loaded_vial.load_rna(target)` 小瓶装入 RNA、播放 `spray2.ogg`、更新外观、`span_notice` 提示；若冷却中（2 分钟内）→ `span_warning` 提示无法提取。

**其他**：`unload_vial` 将小瓶放回使用者手中（`put_in_hands`）+ `empty.ogg`；`update_overlays` 有瓶时叠加 `"extractor_load"`；`Destroy` 时小瓶掉落在原地。

## 3.2 RNA 小瓶 (rna_vial)

`/obj/item/rna_vial`（生 RNA 小瓶）

| 属性 | 值 |
|---|---|
| `name` | `"raw RNA vial"`（生 RNA 小瓶） |
| `desc` | `"A glass vial containing raw virus RNA. Slot this into the combinator to upload the sample."`（装有生病毒 RNA 的玻璃小瓶，插入重组机以上传样本） |
| `icon_state` | `"rnavial"` |
| 材料 | 铁 ×½ 片、玻璃 ×1 片（`SHEET_MATERIAL_AMOUNT`）、银 ×½ 片 |
| `contains_rna` | `FALSE`（默认空） |

- `load_rna(H)`：`contains_rna = TRUE` + 更新外观。
- `update_overlays`：含 RNA 时叠加 `"rnavial_load"`。
- `examine`：含 RNA 时附加说明。

## 3.3 HNZ-1 治愈药瓶 (hnz_cure)

`/obj/item/hnz_cure`（HNZ-1 治愈药瓶）

| 属性 | 值 |
|---|---|
| `name` | `"HNZ-1 cure vial"`（HNZ-1 治愈药瓶） |
| `desc` | `"A counter to the HNZ-1 virus, used to rapidly reverse the effects of the virus."`（HNZ-1 病毒的解药，可迅速逆转病毒效果） |
| `icon_state` | `"tvirus_cure"`（使用后变为 `"tvirus_used"`） |
| `used` | `FALSE`（一次性） |

**使用流程**（`attack` 点击目标）：
1. 已用过（`used`）→ `span_danger` 拒绝。
2. 目标须为人类（`ishuman`），且带有 `mutant_infection` 组件（否则 `span_danger` 提示"目标没有感染"）。
3. `do_after(user, 4 SECONDS)` **4 秒引导** 成功后 → `cure_target(H)`：
   - `SEND_SIGNAL(target, COMSIG_MUTANT_CURED)` —— 发出治愈信号，触发感染组件的 `cure_host`（第二卷 2.8）。
4. 播放 `spray2.ogg`、`used = TRUE`、更新图标（`"tvirus_used"`）。

## 3.4 RNA 重组机 (rna_recombinator)

`/obj/machinery/rnd/rna_recombinator`（RNA 重组机）

### 3.4.1 常量与属性

| 常量 | 值 |
|---|---|
| `STATUS_IDLE` | `"System Idle"`（系统空闲） |
| `STATUS_RECOMBINATING_VIRUS` | `"System Synthesising Virus"`（系统正在合成病毒） |
| `STATUS_RECOMBINATING_CURE` | `"System Synthesising Cure"`（系统正在合成治愈剂） |
| `RECOMBINATION_STEP_TIME` | **15 秒**（基础每步时间） |
| `RECOMBINATION_STEP_AMOUNT` | **25%**（基础每步进度） |

| 属性 | 值 |
|---|---|
| `name` / `desc` | `"RNA recombinator"`（RNA 重组机）/ 重组提取到的病毒 RNA 序列 |
| `icon` / `icon_state` / `base_icon_state` | `'cure_machine.dmi'` / `"h_lathe"` / `"h_lathe"` |
| `density` | `TRUE` |
| `use_power` | `IDLE_POWER_USE` |
| `circuit` | `/obj/item/circuitboard/machine/rna_recombinator`（电路板） |
| `recombination_step_amount` | 25（随部件加成） |
| `recombination_step_time` | 15 秒（随部件缩减） |
| `cure_progress` | 0~100% |
| `timer_id` | 重组计时器 |

### 3.4.2 交互与界面

- **插入小瓶**（`item_interaction`）：战斗模式 / 未就绪 / 非 `rna_vial` → 阻断；成功插入则播放 `autoguninsert.ogg`、`flick("h_lathe_load")`。
- **UI**（`ui_interact`，浏览器窗口 `"rna_recombinator"`，700×400）：
  - `System Status: [status]`（系统状态）
  - `System Efficency - Step time: [时间] SECONDS | Step percent: [25+加成]%`（效率参数）
  - 工作中显示 `Current RNA restructure progress: [cure_progress]%`（重组进度）
  - 已装入小瓶：显示 `RNA structure: HNZ-1`（含 RNA 时）；空瓶显示 `ERROR NO RNA`
  - 按钮：`Synthesize Cure`（合成治愈剂）/ `Synthesize Virus`（合成病毒）/ `Eject`（弹出）/ `Refresh`（刷新）/ `Close`（关闭）
- **Topic 操作**：断电/损坏/维护中 → 拒绝；`close` → 关窗；`eject` → `ejectItem()`；`refresh` → 刷新 UI；其余操作需满足：状态空闲 + 已装小瓶 + 操作对象为当前装入物（防 href 作弊），然后 `virus` → 病毒状态 / 其他 → 治愈状态，`recombinate_start()` + `use_energy(active_power_usage)`。
- **`ejectItem()`**：弹到 `view(1, src)` 随机格；空（虚空）保险则用 `drop_location()`。

### 3.4.3 重组流程

1. **`recombinate_start()`**：断电/损坏 → 重置进度回空闲；否则消耗小瓶（`vial.contains_rna = FALSE` + 更新外观）、弹出空瓶、播放 `rped.ogg`、`flick("h_lathe_wloop")`、`use_energy`，**15 秒后**（`recombination_step_time`）进入 `recombinate_step()`。
2. **`recombinate_step()`**：断电/损坏 → 重置；`cure_progress += 25`（`recombination_step_amount`）；达 **100%** → `recombinate_finish()`；否则继续 `flick("h_lathe_wloop")` + `use_energy` + `rped.ogg` + 15 秒后下一步（基础 **4 步 × 15 秒 = 60 秒**一轮）。
3. **`recombinate_finish()`**：断电/损坏 → 重置；`cure_progress = 0`；
   - **合成治愈剂**：产出 **3 瓶** `/obj/item/hnz_cure`（HNZ-1 治愈药瓶）；
   - **合成病毒**：产出 **1 瓶** `/obj/item/reagent_containers/cup/bottle/hnz/one`（1 单位 HNZ-1 病毒）；
   - `flick("h_lathe_leave")` + `use_energy` + `ding.ogg`，状态回 `STATUS_IDLE`。

### 3.4.4 部件加成 (RefreshParts)

| 部件 | 效果 |
|---|---|
| 伺服器（`servo`，每个） | `recombination_step_time -= tier`（每级 -1 秒步进时间；当 `(step_time - tier) >= 1` 且 `step_time > 0` 时生效） |
| 扫描模块（`scanning_module`，每个） | `recombination_step_amount += tier * 2`（每级 +2% 步进进度） |
| 微型激光器（`micro_laser`，每个） | `recombination_step_amount += tier`（每级 +1% 步进进度） |

> 电路板所需部件：扫描模块 ×1、伺服器 ×2、微型激光器 ×2（见第五卷科技）。

`update_overlays`：断电/损坏/维护中或无装入物 → 叠加 `"lathe_empty"`（空机台）。

## 3.5 HNZ-1 病毒试剂 (/datum/reagent/hnz)

`/datum/reagent/hnz`（HNZ-1 病原体试剂）：

| 属性 | 值 |
|---|---|
| `name` | `"HNZ-1"` |
| `description` | `"HNZ-1 is a highly experimental viral bioterror agent which causes dormant nodules to be etched into the grey matter of the subject. These nodules only become active upon death of the host, upon which, the secondary structures activate and take control of the host body."`（HNZ-1 是一种高度实验性的病毒生物恐怖制剂，会在受试者大脑灰质中蚀刻出休眠结节。这些结节仅在宿主死亡时激活，此后次级结构激活并接管宿主躯体。） |
| `color` | `#191dff`（蓝色） |
| `metabolization_rate` | `INFINITY`（无限，瞬间代谢） |
| `taste_description` | `"brains"`（尝起来像脑子） |
| `ph` | **0.5**（强酸性） |

**接触感染**（`expose_mob`，`methods=TOUCH`）：任意接触（涂抹/吸入等）→ `try_to_mutant_infect(exposed_mob, TRUE)` —— **强制感染**（跳过概率与免疫检查中的概率部分）。

## 3.6 HNZ-1 瓶装试剂与生物容器

**`/obj/item/reagent_containers/cup/bottle/hnz`**（HNZ-1 试剂瓶）：
- `name` = `"HNZ-1 bottle"`，`desc` = `"A small bottle of the HNZ-1 pathogen. Nanotrasen Bioweapons inc."`（一小瓶 HNZ-1 病原体，纳米传讯生物武器公司出品）
- `icon_state` = `"tvirus_infector"`；材料：玻璃 ×½ 片
- `list_reagents = list(/datum/reagent/hnz = 30)` —— **30 单位 HNZ-1**

**`/obj/item/reagent_containers/cup/bottle/hnz/one`**：`list_reagents = list(/datum/reagent/hnz = 1)` —— **1 单位 HNZ-1**（重组机"合成病毒"的产物；可视为感染武器）

**`/obj/item/storage/briefcase/virology/hnz`**（HNZ-1 生物容器，任务/事件补给箱）：
- `name` = `"\improper HNZ-1 biocontainer"`（HNZ-1 生物安全箱）
- `desc` = `"An airtight biosealed box containing the highly reactive substance, HNZ1. Authorised personnel only."`（装有高反应性物质 HNZ-1 的气密生物密封箱，仅限授权人员）
- `w_class = WEIGHT_CLASS_SMALL`（小型）；`max_integrity = 500`
- **内含物**（`PopulateContents`，全量）：
  1. `/obj/item/reagent_containers/cup/bottle/hnz/one` ×2（1 单位病毒瓶 ×2）
  2. `/obj/item/circuitboard/machine/rna_recombinator` ×1（RNA 重组机电路板）
  3. `/obj/item/rna_extractor` ×1（RNA 提取器）
  4. `/obj/item/rna_vial` ×1（RNA 小瓶）

---

# 第四卷 · 变种怪类型与变体全录 (Species)

**源码**: `modular_nova/modules/mutants/code/mutant_species.dm`（284 行）+ `modular_nova/modules/mutants/code/mutant_zombie_bodyparts.dm`（54 行）

## 4.1 变体总览

| 变体 | 类型路径 | 种族 ID | 名称 | 伤害修正 `damage_modifier` | 再生速率 `heal_rate` | 腿速度修正 | 爪 |
|---|---|---|---|---|---|---|---|
| 基础变种怪 | `/datum/species/mutant` | `SPECIES_MUTANT` | High-Functioning mutant（高机能变种怪） | —（未设，默认 0） | —（无自愈循环） | —（普通腿） | —（无爪） |
| 感染型变种怪 | `/datum/species/mutant/infectious` | `SPECIES_MUTANT_INFECTIOUS` | Mutated Abomination（变异可憎之物） | **10** | **1.0** | **0.5** | 标准爪 |
| 快速变体 | `/datum/species/mutant/infectious/fast` | `SPECIES_MUTANT_FAST` | Fast Mutated Abomination（快速变异可憎之物） | **0** | **0.5** | **0.25** | 弱爪 |
| 缓慢变体 | `/datum/species/mutant/infectious/slow` | `SPECIES_MUTANT_SLOW` | Slow Mutated Abomination（缓慢变异可憎之物） | **15** | **1.5** | **0.75** | 标准爪 |

> 注：`damage_modifier` 为本代码库中定义于 `_species.dm`（默认 0）的种族属性（`heart_eater` 组件会修改它），本分支未见其被伤害结算消费的调用点，按源码原值如实记录。

**转化时随机二选一**（`mutant_species` 列表）：快速 / 缓慢变体（见第二卷 2.2）。

## 4.2 基础变种怪 (High-Functioning mutant)

`/datum/species/mutant`：

| 属性 | 值 |
|---|---|
| `name` / `id` | `"High-Functioning mutant"`（高机能变种怪）/ `SPECIES_MUTANT` |
| `meat` | `/obj/item/food/meat/slab/human/mutant/zombie`（僵尸人肉排） |
| `mutanttongue` | `/obj/item/organ/tongue/zombie`（僵尸舌头） |
| `inherent_biotypes` | `MOB_UNDEAD \| MOB_HUMANOID`（不死 + 人形） |
| `changesource_flags` | `MIRROR_BADMIN \| WABBAJACK \| MIRROR_PRIDE \| ERT_SPAWN` |
| `bodytemp_normal` | `T0C`（0℃，无自然体温，由环境决定） |
| `bodytemp_heat_damage_limit` | `FIRE_MINIMUM_TEMPERATURE_TO_SPREAD`（火焰蔓延温度才受热伤） |
| `bodytemp_cold_damage_limit` | `MINIMUM_TEMPERATURE_TO_MOVE`（低于最低移动温度才受冷伤） |

**固有特质 `inherent_traits`（12 项，全量）**：

| 特质 | 说明 |
|---|---|
| `TRAIT_NOBLOOD` | 无血液 |
| `TRAIT_NODISMEMBER` | 不会肢体断裂 |
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 |
| `TRAIT_LIVERLESS_METABOLISM` | 无肝代谢 |
| `TRAIT_TOXIMMUNE` | 毒素免疫 |
| `TRAIT_RESISTCOLD` | 抗寒 |
| `TRAIT_RESISTHIGHPRESSURE` | 抗高压 |
| `TRAIT_RESISTLOWPRESSURE` | 抗低压 |
| `TRAIT_RADIMMUNE` | 辐射免疫 |
| `TRAIT_LIMBATTACHMENT` | 肢体可接回 |
| `TRAIT_NOBREATH` | 无需呼吸 |
| `TRAIT_NO_ZOMBIFY` | 不会被僵尸化（不可再被感染） |

**惊悚音效 `spooks`（5 个，间歇播放）**：`growl1.ogg`、`growl2.ogg`、`growl3.ogg`、`veryfar_noise.ogg`、`wail.ogg`（均在 `sound/effects/hallucinations/`）。

**躯体覆盖**：六部位全部替换为 `mutant_zombie` 系列（见 4.11）。

**其他**：`check_roundstart_eligible()` —— 仅 **万圣节**（`check_holidays(HALLOWEEN)`）可开局选择；`get_species_description`/`get_species_lore` 返回占位文本；`body_temperature_core()` 为空实现（**冷血，不调节体温**）。

玩家种族快捷类型：`/mob/living/carbon/human/species/mutant`（race = 基础种）、`/mob/living/carbon/human/species/mutant/infectious`（race = 感染型）。

## 4.3 感染型变种怪 (Mutated Abomination)

`/datum/species/mutant/infectious`（继承基础种全部特质）：

| 属性 | 值 |
|---|---|
| `name` / `id` | `"Mutated Abomination"`（变异可憎之物）/ `SPECIES_MUTANT_INFECTIOUS` |
| `damage_modifier` | **10**（源码值，见 4.1 注） |
| `mutanteyes` | `/obj/item/organ/eyes/zombie`（僵尸之眼） |
| `changesource_flags` | `MIRROR_BADMIN \| WABBAJACK \| ERT_SPAWN`（无 `MIRROR_PRIDE`） |
| `hands_to_give` | `/obj/item/hnz_mutant_hand`（标准变种爪） |
| `heal_rate` | **1.0**（再生速率） |
| `regen_cooldown` | `COOLDOWN_DECLARE`（再生冷却声明） |

**躯体覆盖**：头/胸/双臂 = `mutant_zombie` 标准件；双腿 = `/obj/item/bodypart/leg/{left,right}/mutant_zombie/infectious`，**速度修正 `speed_modifier = 0.5`**。

**信号注册**（`on_species_gain`）：
- `AddComponent(/datum/component/mutant_hands, mutant_hand_path = hands_to_give)` —— 装备变种爪（不可卸下）。
- `COMSIG_MOB_AFTER_APPLY_DAMAGE` → `queue_regeneration`（受伤后排队再生）。
- `COMSIG_LIVING_LIFE` → `on_life`（每生命 tick 再生处理）。
- `on_species_loss` 注销上述两信号。

**其他**：`check_roundstart_eligible()` 返回 `FALSE`（**不可开局选择**）；`spec_stun(H, amount)` = `min(20, amount)`（**眩晕上限 20**）；`canBeHandcuffed()` 返回 `FALSE`（**无法被拷手铐**）。

## 4.4 快速变体 (Fast Mutated Abomination)

`/datum/species/mutant/infectious/fast`：

| 属性 | 值 |
|---|---|
| `name` / `id` | `"Fast Mutated Abomination"`（快速变异可憎之物）/ `SPECIES_MUTANT_FAST` |
| `hands_to_give` | `/obj/item/hnz_mutant_hand/fast`（弱化爪） |
| `damage_modifier` | **0** |
| `heal_rate` | **0.5**（再生较慢） |
| 双腿 | `/obj/item/bodypart/leg/{left,right}/mutant_zombie/fast`，**`speed_modifier = 0.25`**（极快） |

> 特征：**速度最快**（腿速修正 0.25），但伤害减免最差、自愈最慢、爪伤害最低（force 21，见 4.9）。

## 4.5 缓慢变体 (Slow Mutated Abomination)

`/datum/species/mutant/infectious/slow`：

| 属性 | 值 |
|---|---|
| `name` / `id` | `"Slow Mutated Abomination"`（缓慢变异可憎之物）/ `SPECIES_MUTANT_SLOW` |
| `damage_modifier` | **15** |
| `heal_rate` | **1.5**（再生最快） |
| 双腿 | `/obj/item/bodypart/leg/{left,right}/mutant_zombie/slow`，**`speed_modifier = 0.75`**（较慢） |

> 特征：**最肉**（伤害修正最高）、**自愈最快**（1.5/s），但**速度最慢**（腿速修正 0.75）。

## 4.6 共通机制（再生/体温/眩晕/手铐）

**再生冷却**：`#define REGENERATION_DELAY (5 SECONDS)` —— 受伤后 **5 秒** 才开始自动再生（`queue_regeneration`：冷却结束后 `COOLDOWN_START(src, regen_cooldown, REGENERATION_DELAY)` 启动）。

**`on_life` 再生循环**（感染型三变体共通）：
1. 冷却结束后：`heal_amt = heal_rate`；若带 `TRAIT_CRITICAL_CONDITION`（濒死）→ **×2**。
2. 每 tick 治疗：`heal_overall_damage(heal_amt × SPT, heal_amt × SPT)`（整体伤害）+ `adjust_stamina_loss(-heal_amt × SPT)`（体力恢复）+ `adjust_tox_loss(-heal_amt × SPT)`（毒素清除）；有变化才 `updatehealth()`。
3. 伤口自愈：遍历 `all_wounds`，`SPT_PROB(2 - 伤口严重度/2)` → `remove_wound()` 移除伤口（伤口越重越难自愈）。
4. 惊悚音效：非濒死时 `SPT_PROB(2)` → 随机播放 `spooks` 音效（音量 50，范围 10）。

> 注释原意："变种怪永远不会真正死亡，他们只是倒下，直到再生到足以重新站起来。"（mutants never actually die, they just fall down until they regenerate enough to rise back up.）

**体温**：`body_temperature_core` 空实现 —— 冷血行尸，不主动调节体温。
**眩晕**：`spec_stun` = `min(20, amount)`，眩晕时间上限 20 tick。
**手铐**：`canBeHandcuffed()` = `FALSE`，感染型无法被上手铐。

## 4.7 感染判定 (try_to_mutant_infect)

`/proc/try_to_mutant_infect(mob/living/carbon/human/target, forced = FALSE, mob/user)`：

| 步骤 | 检查 | 结果 |
|---|---|---|
| 0 | `CHECK_DNA_AND_SPECIES(target)` | 无 DNA/种族 → 直接失败 |
| 1 | `forced == TRUE`（强制感染） | **无条件** `AddComponent(mutant_infection)`，返回 `TRUE`（用于试剂接触/事件/管理指令） |
| 2 | 目标带 `TRAIT_NO_ZOMBIFY`（如高机能变种怪） | 拒绝（`FALSE`） |
| 3 | 目标已有感染组件 | 拒绝（`FALSE`） |
| 4 | `!target.can_inject(user)`（无法注射） | 拒绝（`FALSE`） |
| 5 | `prob(70)`（`INFECT_CHANCE = 70`） | **70% 概率失败**（即每次有效命中仅 **30% 感染成功率**） |
| 6 | 目标带 `TRAIT_MUTANT_IMMUNE`（治愈后 5~10 分钟免疫期） | 拒绝（`FALSE`） |
| 7 | 通过全部检查 | `AddComponent(mutant_infection)`，返回 `TRUE` |

## 4.8 治愈判定 (try_to_mutant_cure)

`/proc/try_to_mutant_cure(mob/living/carbon/target)`（供管理指令等使用）：目标带感染组件 → `qdel(infection)` **直接销毁组件**（等效立即治愈）。

## 4.9 变种怪之爪 (hnz_mutant_hand)

`/obj/item/hnz_mutant_hand`（变种爪）：

| 属性 | 标准爪 | 弱爪 `/fast` |
|---|---|---|
| `name` | `"mutant claw"`（变种爪） | `"weak mutant claw"`（弱变种爪） |
| `desc` | 变种怪的主要工具：感染人类、屠宰其他生物以维持自身、砸开气密门、拧开儿童安全瓶盖 | —（继承） |
| `item_flags` | `ABSTRACT \| DROPDEL` | 同左 |
| `resistance_flags` | `INDESTRUCTIBLE \| LAVA_PROOF \| FIRE_PROOF \| UNACIDABLE \| ACID_PROOF`（不可毁/防火/防熔岩/防酸） | 同左 |
| `icon_state` | `"bloodhand_left"` / `"bloodhand_right"`（按持有手切换） | 同左 |
| `inhand_icon_state` | `"mutant"` | 同左 |
| 手持图标文件 | `mutant_hand_lefthand.dmi` / `mutant_hand_righthand.dmi` | 同左 |
| `hitsound` | `growl1.ogg`（低吼） | 同左 |
| `force` | **26** | **21** |
| `sharpness` | `SHARP_EDGED`（锐利） | `NONE`（无） |
| `wound_bonus` | **-20** | **-40** |
| `damtype` | `BRUTE`（钝/物理伤） | 同左 |

**机制**：
- `Initialize`：`ADD_TRAIT(TRAIT_NODROP, HAND_REPLACEMENT_TRAIT)` —— **不可掉落**（换手特质）。
- `equipped`：按持有手索引奇偶切换左右血手图标（注释：故意反向）。
- `afterattack` 点击目标：
  - 目标为**人类** → `try_to_mutant_infect(target, user = user)`（30% 感染判定，见 4.7）；
  - 目标为**其他生物** → `check_feast(target, user)`（进食，见 4.10）。

## 4.10 进食盛宴 (check_feast)

`check_feast(target, user)`（仅对**已死亡**的非人生物目标生效）：

1. `hp_gained = target.maxHealth`（获得目标最大生命值等量的治疗量）。
2. `investigate_log` 记录死亡日志（"已被变种怪 [user] 吞噬"）。
3. `target.gib()` —— **目标被碎尸**。
4. 治疗者三系伤害全恢复：`adjust_brute_loss(-hp_gained)` + `adjust_tox_loss(-hp_gained)` + `adjust_fire_loss(-hp_gained)`（不即时更新生命，最后统一 `updatehealth()`）。
5. `adjust_organ_loss(ORGAN_SLOT_BRAIN, -hp_gained)` —— **修复大脑器官**（"Zom Bee 嘎嘣着 'BRAAAAISNS!1!'"）。
6. `set_nutrition(min(nutrition + hp_gained, NUTRITION_LEVEL_FULL))` —— 营养值补满（上限满腹）。

## 4.11 变种怪躯体部件全录

**源码**: `modular_nova/modules/mutants/code/mutant_zombie_bodyparts.dm`（54 行）

六件套共通：`icon_greyscale = icon = icon_static = 'mutant_parts_greyscale.dmi'`（灰度部件图）、`limb_id = SPECIES_MUTANT`。

| 部件 | 路径 | icon_state | 特有属性 |
|---|---|---|---|
| 头 | `/obj/item/bodypart/head/mutant_zombie` | `"mutant_head"` | `is_dimorphic = FALSE`（无两性形态）；`species_color = "#ffffff"`（白色）；`head_flags = HEAD_HAIR \| HEAD_LIPS \| HEAD_DEBRAIN`（可染发/嘴唇/取脑）；`eyes_icon = 'mutant_eyes.dmi'`（僵尸眼图标） |
| 胸 | `/obj/item/bodypart/chest/mutant_zombie` | `"mutant_chest"` | `is_dimorphic = FALSE`；`should_draw_greyscale = FALSE` |
| 左臂 | `/obj/item/bodypart/arm/left/mutant_zombie` | `"mutant_l_arm"` | `should_draw_greyscale = FALSE` |
| 右臂 | `/obj/item/bodypart/arm/right/mutant_zombie` | `"mutant_r_arm"` | `should_draw_greyscale = FALSE` |
| 左腿 | `/obj/item/bodypart/leg/left/mutant_zombie` | `"mutant_l_leg"` | `digitigrade_type = null`（无趾行形态）；`should_draw_greyscale = FALSE` |
| 右腿 | `/obj/item/bodypart/leg/right/mutant_zombie` | `"mutant_r_leg"` | `digitigrade_type = null`；`should_draw_greyscale = FALSE` |

**腿变体（速度修正 `speed_modifier`，全量）**：

| 腿路径 | speed_modifier |
|---|---|
| `/obj/item/bodypart/leg/left/mutant_zombie/infectious` | **0.5** |
| `/obj/item/bodypart/leg/right/mutant_zombie/infectious` | **0.5** |
| `/obj/item/bodypart/leg/left/mutant_zombie/fast` | **0.25** |
| `/obj/item/bodypart/leg/right/mutant_zombie/fast` | **0.25** |
| `/obj/item/bodypart/leg/left/mutant_zombie/slow` | **0.75** |
| `/obj/item/bodypart/leg/right/mutant_zombie/slow` | **0.75** |

---

# 第五卷 · 事件与科技 (Event & Techweb)

**源码**: `modular_nova/modules/mutants/code/mutant_event.dm`（48 行）+ `modular_nova/modules/mutants/code/mutant_techweb.dm`（61 行）

## 5.1 中期事件：HNZ-1 病原体爆发

`/datum/dynamic_ruleset/midround/mutant_infestation`（动态规则集）：

| 属性 | 值 |
|---|---|
| `name` | `"HNZ-1 Pathogen Outbreak"`（HNZ-1 病原体爆发） |
| `config_tag` | `"Mutant Infestation"`（变种怪侵扰） |
| `preview_antag_datum` | `/datum/antagonist/mutant` |
| `midround_type` | `HEAVY_MIDROUND`（重型中期事件） |
| `pref_flag` | `ROLE_MUTANT` |
| `weight` | **0**（不参与常规权重随机） |
| `min_pop` | **30**（最少在线人数） |
| `min_antag_cap` | **2**（最少反角数） |
| `false_alarm_able` | `TRUE`（可虚警） |

**流程**：
- `New()`：`max_antag_cap += prob(50)` —— **50% 概率反角上限 +1**。
- `execute()`：**600 秒（10 分钟）后** `announce_mutant_infestation()`。
- `announce_mutant_infestation()`：全服警报音 `alert2.ogg`（`override_volume = TRUE`）+ 优先公告 ——
  - 原文：`"Automated air filtration screeing systems have flagged an unknown pathogen in the ventilation systems, quarantine is in effect."`
  - 标题：`"Level-1 Viral Biohazard Alert"`（一级病毒生化危害警报）
  - 播音员：`ANNOUNCER_MUTANTS`。
- `false_alarm()`：同样触发公告（虚警）。
- `collect_candidates()`：`GLOB.alive_player_list`（全体存活玩家）。
- `is_valid_candidate()`：必须在**主站层**（`is_station_level`）、是**人类**、且**有 DNA 种族**。
- `assign_role()`：`add_antag_datum(/datum/antagonist/mutant)` + `try_to_mutant_infect(candidate.current, TRUE)`（强制感染）+ `notify_ghosts`（"XXX 已被 HNZ-1 病原体感染！"）。

## 5.2 科技节点 (mutanttech)

`/datum/techweb_node/mutanttech`：

| 属性 | 值 |
|---|---|
| `id` | `TECHWEB_NODE_MUTANT_TECH` |
| `display_name` | `"Advanced Nanotrasen Viral Bioweapons Technology"`（纳米传讯高级病毒生物武器科技） |
| `description` | `"Research devices from the Nanotrasen viral bioweapons division! Got a virus problem? This'll save your day."`（研究纳米传讯病毒生物武器部门设备！有病毒问题？它会救你一命。） |
| `prereq_ids` | `TECHWEB_NODE_SURGERY_TOOLS`（手术工具）+ `TECHWEB_NODE_CYTOLOGY`（细胞学） |
| `design_ids` | `"rna_vial"`、`"rna_extractor"`、`"rna_recombinator"`（3 张图纸） |
| `research_costs` | `TECHWEB_POINT_TYPE_GENERIC = TECHWEB_TIER_3_POINTS`（**三级通用点数**） |
| `announce_channels` | `RADIO_CHANNEL_SCIENCE`（科研频道）+ `RADIO_CHANNEL_MEDICAL`（医疗频道） |

**电路板** `/obj/item/circuitboard/machine/rna_recombinator`：`greyscale_colors = CIRCUIT_COLOR_SCIENCE`（科研蓝灰）；**所需部件：扫描模块 ×1、伺服器 ×2、微型激光器 ×2**。

## 5.3 设计图纸全录（3 张）

| 图纸 | 名称 | 制造类型 | 材料 | 分类 | 部门 |
|---|---|---|---|---|---|
| `/datum/design/rna_vial`（`"rna_vial"`） | `"Empty RNA vial"`（空 RNA 小瓶） | `PROTOLATHE`（原型车床） | 铁 ×½、玻璃 ×1、银 ×½ | 装备 + 医疗 + 科研 | 科研 \| 医疗 |
| `/datum/design/rna_extractor`（`"rna_extractor"`） | `"RNA Extractor Device"`（RNA 提取设备） | `PROTOLATHE` | 铁 ×2、金 ×2、铀 ×½、钻石 ×½ | 装备 + 医疗 + 科研 | 科研 \| 医疗 |
| `/datum/design/board/rna_recombinator`（`"rna_recombinator"`） | `"RNA Recombinator Board"`（RNA 重组机电路板） | —（机器板） | — | 机器 + 科研 | 工程 \| 科研 \| 医疗 |

---

# 附录 · 源码路径索引

**模块根目录**: `modular_nova/modules/mutants/`

| 文件 | 行数 | 内容 |
|---|---|---|
| `code/mutant_antag_datum.dm` | 21 | 变种怪阵营 `/datum/antagonist/mutant`（第一卷） |
| `code/mutant_component.dm` | 193 | 感染组件 `/datum/component/mutant_infection` 全机制（第二卷） |
| `code/mutant_cure.dm` | 375 | 治疗与抵御：RNA 提取器/小瓶/重组机/HNZ-1 试剂与容器（第三卷） |
| `code/mutant_species.dm` | 284 | 种族/变体全录、感染判定、变种爪、进食（第四卷） |
| `code/mutant_event.dm` | 48 | 中期事件 HNZ-1 病原体爆发（第五卷 5.1） |
| `code/mutant_techweb.dm` | 61 | 科技节点与 3 张图纸（第五卷 5.2~5.3） |
| `code/mutant_zombie_bodyparts.dm` | 54 | 变种怪六件套躯体部件与腿变体（第四卷 4.11） |
| `icons/` | — | `antag_hud.dmi`、`extractor.dmi`、`cure_machine.dmi`、`mutant_parts_greyscale.dmi`、`mutant_hand_lefthand.dmi`、`mutant_hand_righthand.dmi`、`mutant_eyes.dmi`、`mutant_claw.dmi` |
| `readme.md` | 23 | 模块说明（MODULE ID: MUTANTS，来源 Skyrat PR #4664，作者 Gandalf2k15） |

**关联外部定义**（本模块引用、定义于他处）：`/obj/item/food/meat/slab/human/mutant/zombie`（`code/modules/food_and_drinks/food/meatslab.dm`）、`/obj/item/organ/tongue/zombie` 与 `/obj/item/organ/eyes/zombie`（`code/modules/mob/living/carbon/human/species_types/zombies.dm`）。
