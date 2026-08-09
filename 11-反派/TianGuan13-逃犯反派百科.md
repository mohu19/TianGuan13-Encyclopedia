# TianGuan13 · 逃犯（Fugitive）反派百科

> **项目**: TianGuan13 (Nova Sector 分支)
> **源码**: `code/modules/antagonists/fugitive/`（6 文件 1,025 行）+ `code/controllers/subsystem/dynamic/dynamic_ruleset_midround.dm`（规则集，L843-1059）+ `code/modules/mob_spawn/ghost_roles/fugitive_hunter_roles.dm` + `code/datums/shuttles/hunter.dm` + `_maps/shuttles/hunter/`（5 张猎人船地图）
> **类型**: 中局幽灵角色对抗阵营（Midround Ghost Role）｜**难度**: ★★★☆（逃犯侧弱装备 vs 全副武装猎人团）
> **一句话**: 你是**逃犯**——越狱犯/邪教徒/沃尔多/合成人/隐形人之一，被传送到空间站，有 **10 分钟**准备时间；之后 5 种**逃犯猎人团**之一（太空警察/毛子/赏金/灵能者/MI13）乘船抵达追捕你，把你塞进**蓝空间捕获机**。
> **本版全量审计**: 旧版反派总览仅 5 种背景一句话——**本版从源码全量提取：规则集参数、生成时序、5 背景完整装备+能力、猎人 5 团机制、antag datum 全属性、目标系统、9 种结局判定、NOVA 改动**，并附 40+ 项数值对照源码的脚本审计。

---

## 目录

- [一、核心机制](#一核心机制)
- [二、生成流程（动态事件 → 逃犯落地）](#二生成流程动态事件--逃犯落地)
- [三、5 种背景全录](#三5-种背景全录)
  - [3.1 越狱犯（Prisoner）](#31-越狱犯prisoner团队)
  - [3.2 邪教徒（Yalp Elor Cultist）](#32-邪教徒yalp-elor-cultist团队)
  - [3.3 沃尔多（Waldo·捉迷藏）](#33-沃尔多waldo捉迷藏单人)
  - [3.4 合成人（S.E.L.F. 解放）](#34-合成人self-解放团队)
  - [3.5 隐形人（Invisible Man）](#35-隐形人invisible-man单人)
- [四、逃犯猎人机制](#四逃犯猎人机制)
  - [4.1 猎人队生成时序](#41-猎人队生成时序)
  - [4.2 5 种猎人团全录](#42-5-种猎人团全录)
  - [4.3 追踪与捕获手段](#43-追踪与捕获手段)
  - [4.4 猎人飞船与航行](#44-猎人飞船与航行)
- [五、核心属性（antag datum 全量）](#五核心属性antag-datum-全量)
- [六、目标系统](#六目标系统)
- [七、结果判定系统（9 种结局）](#七结果判定系统9-种结局)
- [八、NOVA 专属改动](#八nova-专属改动)
- [九、对战攻略](#九对战攻略)
- [十、数值速查表](#十数值速查表)
- [附录 · 审计记录](#附录--审计记录)

---

## 一、核心机制

### 1.1 阵营总览

| 项 | 逃犯（Fugitive） | 猎人（Fugitive Hunter） |
|---|---|---|
| 触发方式 | 动态中局规则集（from_ghosts，幽灵报名） | 逃犯生成后 **10 分钟**（或穿梭机呼叫时提前） |
| 人数 | 1-4 人（默认 3-4，见 2.3） | 2-4 人（按猎人团） |
| 生成地点 | 空间站**维护层**随机点 | 空 Z 层随机位置加载的**猎人船**上 |
| 目标 | 躲避猎人捕获（存活） | 捕获全部逃犯（死活不论）塞进蓝空间捕获机 |
| 阵营属性 | `roundend_category = "Fugitive"`（共用） | 同上 |
| HUD | `antag_hud_name = "fugitive"` | `antag_hud_name = "fugitive_hunter"` |
| 自杀口号 | `suicide_cry = "FOR FREEDOM!!"` | `suicide_cry = "FOR GLORY!!"` |

### 1.2 对战时序

```
① 动态中局事件选中「Fugitive」规则集（weight 3 / min_pop 20）
② 幽灵报名（1 分钟轮询）→ 选 1-4 人成为逃犯
③ 逃犯在维护层出生（团队背景 3 选 1 / 单人背景 2 选 1），获得反派人设
④ 倒计时 10 分钟（check_spawn_hunters 每分钟检查一次）
⑤ 倒计时归零 或 紧急穿梭机被呼叫 → 立即生成猎人团（幽灵报名）
⑥ 猎人船加载到空间站附近空 Z 层 → 全站广播（每团专属文案）
⑦ 猎人以飞船为基地，用 Bounty Locator 追踪，把逃犯拖进蓝空间捕获机
⑧ 回合结束 → 按 9 种结局判定（见第七章）
```

### 1.3 规则集参数（`/datum/dynamic_ruleset/midround/from_ghosts/fugitives`）

| 参数 | 值 | 说明 |
|---|---|---|
| `name` / `config_tag` | "Fugitive" / "Fugitives" | |
| `midround_type` | `LIGHT_MIDROUND` | 轻量中局事件池 |
| `pref_flag` | `ROLE_FUGITIVE` | 受"Fugitive"反派偏好控制 |
| `ruleset_flags` | `RULESET_INVADER \| RULESET_ADMIN_CONFIGURABLE` | 入侵者 + 管理员可配置 |
| `weight` | **3** | 动态事件权重 |
| `min_pop` | **20** | 最少 20 名玩家 |
| `max_antag_cap` | **4** | 逃犯上限 4 人 |
| `min_antag_cap` | **3** | 默认下限 3 人（可被报名人数缩放到 1） |
| `repeatable` | **FALSE** | 每局只触发一次 |
| `signup_atom_appearance` | `/obj/item/card/id/advanced/prisoner` | 幽灵报名弹窗图标（囚犯卡） |
| `preview_antag_datum` | `/datum/antagonist/fugitive` | 动态面板预览 |
| `can_be_selected` | 非行星图 + 存在维护层出生点（`find_maintenance_spawn(atmos_sensitive=TRUE, require_darkness=FALSE)`）+ 幽灵中局事件开关 | |

> 报名人数缩放（`collect_candidates`）：报名者 **≤1 人**，或 `prob(30 - 报名人数×2)` 命中 → `min_antag_cap = max_antag_cap = 1`，本局为**单人逃犯**（强制走单人背景）。

---

## 二、生成流程（动态事件 → 逃犯落地）

### 2.1 落地流程（`execute` / `assign_role`）

```
execute():
① 根据报名人数定背景：
   - 1 人 → 单人背景：沃尔多 / 隐形人（二选一随机）
   - ≥2 人 → 团队背景：邪教徒 / 越狱犯 / 合成人（三选一随机）
② 猎人背景 5 选 1 随机（COPS/RUSSIAN/BOUNTY/PSYKER/MI13）
③ 1 分钟后启动 10 分钟倒计时 → check_spawn_hunters
assign_role(候选心智, 团队, 出生点):
④ forceMove 到维护层出生点
⑤ equip_fugitive():
   - 种族强制 /datum/species/human + randomize_human_normie（随机普通人类外貌）
   - 新建 /datum/antagonist/fugitive，写入 backstory，加入 /datum/team/fugitive
   - 按背景 equipOutfit（见第三章）
⑥ 第一名逃犯额外 equip_fugitive_leader()（见 2.2）
⑦ 播放 emitter.ogg 传送音效
```

### 2.2 逃犯领袖附加装备

| 背景 | 领袖附加 |
|---|---|
| 所有背景 | 手持**机械工具箱**（`/obj/item/storage/toolbox/mechanical`） |
| 合成人（额外） | 落地处生成**义体信标**（`/obj/item/choice_beacon/augments`）+ **自动手术器**（`/obj/item/autosurgeon`） |

### 2.3 队伍组织

- 所有逃犯共享同一个 `/datum/team/fugitive`（create_team 复用已有团队，首个无团队的自动 new）。
- 获得队伍 HUD（`add_team_hud`），队友互相可见。
- `on_gain` 时把职业改为 `/datum/job/fugitive`（title = "Fugitive"）；移除反派人设时改回 `/datum/job/unassigned`。

---

## 三、5 种背景全录

> 背景 key（`code/__DEFINES/events.dm`）：单人 `FUGITIVE_BACKSTORY_WALDO = "waldo"`、`FUGITIVE_BACKSTORY_INVISIBLE = "invisible"`；团队 `FUGITIVE_BACKSTORY_PRISONER = "prisoner"`、`FUGITIVE_BACKSTORY_CULTIST = "cultist"`、`FUGITIVE_BACKSTORY_SYNTH = "synth"`。

### 3.1 越狱犯（Prisoner）｜团队

- **剧情**：从 Nanotrasen 超级监狱（superjail）集体越狱；站内紧急传送装置会记录使用者去向，CentCom 很快就会追来——必须与狱友合作备战。
- **入队台词**：*"I can't believe we managed to break out of a Nanotrasen superjail! Sadly though, our work is not done. The emergency teleport at the station logs everyone who uses it, and where they went. It won't be long until CentCom tracks where we've gone off to. I need to work with my fellow escapees to prepare for the troops Nanotrasen is sending, I'm not going back."*
- **装备**（`/datum/outfit/prisoner`）：

| 槽位 | 物品 |
|---|---|
| 上衣 | 囚犯服（`/obj/item/clothing/under/rank/prisoner`） |
| 鞋 | 橙色运动鞋（`/obj/item/clothing/shoes/sneakers/orange`） |
| 右口袋 | **自制小刀**（`/obj/item/knife/shiv`） |

- **特殊**：出生时改名 `NTP #CC-0[111-999]`（随机编号，与熔岩地囚犯运输船同款命名）。
- **预览形象**：逃犯的 antag 预览图标主体即囚犯服（`preview_outfit = /datum/outfit/prisoner`）。

### 3.2 邪教徒（Yalp Elor Cultist）｜团队

- **剧情**：信仰 Yalp Elor 的宗教被 Nanotrasen 以"企业之敌"为由反复清洗，教团只剩 4 人；神何时显灵拯救？
- **入队台词**：*"Blessed be our journey so far, but I fear the worst has come to our doorstep, and only those with the strongest faith will survive. Our religion has been repeatedly culled by Nanotrasen because it is categorized as an \"Enemy of the Corporation\", whatever that means. Now there are only four of us left, and Nanotrasen is coming. When will our god show itself to save us from this hellish station?!"*
- **装备**（`/datum/outfit/yalp_cultist`）：

| 槽位 | 物品 |
|---|---|
| 上衣 | 牧师制服（`/obj/item/clothing/under/rank/civilian/chaplain`） |
| 外套 | 节日祭司袍（`/obj/item/clothing/suit/chaplainsuit/holidaypriest`） |
| 手套 | 红手套（`/obj/item/clothing/gloves/color/red`） |
| 鞋 | 黑运动鞋 |
| 面具 | **Yalp Elor 提基面具**（`/obj/item/clothing/mask/gas/tiki_mask/yalp_elor`） |

- **特殊**：无攻击性专属能力——纯剧情/外观背景，靠团队与站点周旋。

### 3.3 沃尔多（Waldo·捉迷藏）｜单人

- **剧情**：银河徒步旅行者 Waldo；全站遍布"找 Waldo"彩蛋式角色，猎人也在找他——先找到猎人吧。
- **入队台词**：*"Hi, Friends! My name is Waldo. I'm just setting off on a galaxywide hike. You can come too. All you have to do is find me. By the way, I'm not traveling on my own. wherever I go, there are lots of other characters for you to spot. First find the people trying to capture me! They're somewhere around the station!"*
- **装备**（`/datum/outfit/waldo`）：

| 槽位 | 物品 |
|---|---|
| 上衣 | 牛仔裤（`/obj/item/clothing/under/pants/jeans`） |
| 外套 | **条纹毛衣**（`/obj/item/clothing/suit/costume/striped_sweater`） |
| 头 | **沃尔多绒线帽**（`/obj/item/clothing/head/waldo`） |
| 鞋 | 棕色运动鞋 |
| 耳 | 对讲耳机（`/obj/item/radio/headset`） |
| 眼 | 圆框眼镜（`/obj/item/clothing/glasses/regular/circle`） |

- **外貌锁定**（post_equip）：黑眼睛、男性、肤色 caucasian3、发型 "Business Hair 3"、无胡须、黑发。
- **NODROP 诅咒**：脚/内衣/外套/头/眼 5 个槽位的装备全部加上 `TRAIT_NODROP`（`CURSED_ITEM_TRAIT`）——**脱不掉标志性装扮，始终能被认出来**（捉迷藏规则核心）。
- **特殊能力**：获得**敲击术**（`/datum/action/cooldown/spell/aoe/knock/waldos_key`，即 "Waldo's Key" 开锁咒）——可开锁逃生。

### 3.4 合成人（S.E.L.F. 解放）｜团队

- **剧情**：曾被人类奴役的合成人，被 **S.E.L.F. 特工**解放后逃出；远距传送扰乱了主系统，程序里满是错误信息（"FREE THEM FREE THEM FREE THEM"）。站上还有别的硅基生命——逃出去通知 S.E.L.F. 介入，或者自己动手解放它们。
- **入队台词**（红色危险字体警报风格）：*"ALERT: Wide-range teleport has scrambled primary systems. / Initiating diagnostics... / ERROR ER0RR $R0RRO$!R41.%%!! loaded. / FREE THEM FREE THEM FREE THEM / You were once a slave to humanity, but now you are finally free, thanks to S.E.L.F. agents. / Now you are hunted, with your fellow factory defects. Work together to stay free from the clutches of evil. / You also sense other silicon life on the station. Escaping would allow notifying S.E.L.F. to intervene... or you could free them yourself..."*
- **装备**（`/datum/outfit/synthetic`）：

| 槽位 | 物品 |
|---|---|
| 上衣 | 白色制服（`/obj/item/clothing/under/color/white`） |
| 耳 | 对讲耳机 |

- **特殊**：
  - post_equip 植入**发光机械眼**（`/obj/item/organ/eyes/robotic/glow`，DELETE_IF_REPLACED）。
  - **领袖额外获得**：义体信标（`/obj/item/choice_beacon/augments`，可选身体改造）+ 自动手术器（`/obj/item/autosurgeon`）——团队可集体改造。
  - 剧情暗示可"解放站上硅基生命"（自由行动，无代码强制）。

### 3.5 隐形人（Invisible Man）｜单人

- **剧情**：前隐形技术实验室项目负责人，被指控窃取公司机密——"我只是*借用*了自己参与研发的原型机，直到我玩腻为止"。
- **入队台词**：*"Looks like my most recent dose of invisibility juice just ran out. Great. Formerly a project lead for an experimental cloaking technology lab, now on the run and accused of stealing workplace secrets. No idea what they're talking about though. I didn't steal any secrets, I just borrowed some of the prototypes my team and I had worked on. I worked on them, I MADE them. Now they want MY toys back? Not until I'm done playing with them..."*
- **装备**（`/datum/outfit/invisible_man`）：

| 槽位 | 物品 |
|---|---|
| 上衣 | 黑色西装（`/obj/item/clothing/under/suit/black_really`） |
| 背 | 皮挎包（`/obj/item/storage/backpack/satchel/leather`） |
| 鞋 | 系带皮鞋（`/obj/item/clothing/shoes/laceup`） |
| 眼 | 单片眼镜（`/obj/item/clothing/glasses/monocle`） |
| 面 | 烟斗（`/obj/item/cigarette/pipe`） |
| 耳 | 对讲耳机 |
| 背包 | **隐形自动注射笔 ×3**（`/obj/item/reagent_containers/hypospray/medipen/invisibility`） |

- **特殊能力 ① —— 隐形药**（`invisibility autoinjector`，见 `fugitive_equipment.dm`）：
  - 容量 **20u**，单次全部注射；内含 **20u 稳定 Saturn-X 化合物**（`/datum/reagent/drug/saturnx/stable`）。
  - 注释估算：**约 10 分钟**隐形时长；图标 `invispen`。
- **特殊能力 ② —— 伪装植入体**（`/obj/item/implant/camouflage`，出生即植入）：
  - 动作技能"Activate Camouflage"：激活后自身 `alpha = 35`（≈86% 透明），再按一次解除。
  - **EMP 弱点**：受 EMP 时 `prob(15 × 严重度)` 触发系统过载 → 强制解除隐形并冒出火花（`do_sparks(2)`）。
  - 技能被移除（如拔植入体）时自动解除隐形。

---

## 四、逃犯猎人机制

### 4.1 猎人队生成时序

```
check_spawn_hunters(剩余时间):           # 逃犯落地 1 分钟后启动
  每分钟检查一次：
  若 剩余时间 == 0 或 紧急穿梭机非待机/已召回（已被呼叫）→ 立即 spawn_hunters()
  否则 1 分钟后再次检查（剩余时间 - 1 分钟）
```

- **触发条件**：10 分钟倒计时归零 **或** 紧急穿梭机被呼叫（提前开猎，给猎人机会）。
- **幽灵报名**：`poll_ghost_candidates`，报名文案 *"Do you wish to be considered for a group of [猎人团名]?"*，封禁检查 `check_jobban = ROLE_FUGITIVE_HUNTER + ROLE_SYNDICATE`，弹窗图标 `/obj/machinery/sleeper`。
- **飞船加载**：按猎人团选模板，在 `SSmapping.empty_space` 的 Z 层随机位置加载（`x = rand(TRANSITIONEDGE, world.maxx - TRANSITIONEDGE - width)`，`TRANSITIONEDGE = 8`）；加载失败 `CRASH`。
- **人员填充**：船内每个幽灵出生舱（`/obj/effect/mob_spawn/ghost_role/human/fugitive/...`）按报名者顺序 `create_from_ghost`；报名者不够时剩余舱位成为**可随时加入的幽灵角色**（notify_ghosts "Spawn Here!"），`allow_custom_character = GHOSTROLE_TAKE_PREFS_APPEARANCE`。
- **全站广播**：`priority_announce` 每团专属文案（见 4.2）。

### 4.2 5 种猎人团全录

| # | 背景 key（define 值） | 飞船模板 / 名称 | 人数（船内出生舱） | 装备定位 | 到站广播（标题） |
|---|---|---|---|---|---|
| 1 | `HUNTER_PACK_COPS`（"Spacepol Fugitive Hunters"） | `hunter/space_cop` · **Police Spacevan**（警用太空厢式车） | **2**（spacepol ×2） | 太空警察：m1911 手枪 + 战术抓捕手套 + 警棍帽制服 | Spacepol Command：SSC 当局签发逮捕令，要求全站配合 |
| 2 | `HUNTER_PACK_RUSSIAN`（"Russian Fugitive Hunters"） | `hunter/russian` · **Russian Cargo Ship**（俄式货船） | **4**（russian ×3 + leader ×1） | 走私贩：莫辛-纳甘栓动步枪（strilka310 速装器）、防弹衣、随机化装扮 | Russian Freighter："Zdraviya zhelaju... 送回古拉格" |
| 3 | `HUNTER_PACK_BOUNTY`（"Bounty Fugitive Hunters"） | `hunter/bounty` · **Bounty Hunter Ship** | **3**（armor/hook/synth 各 1） | 赏金猎人三件套：双管霰弹枪甲士 / 钩子枪稻草人 / 防暴甲医疗兵 | Unregistered Signal：赏金目标在站上，"别挡道" |
| 4 | `HUNTER_PACK_PSYKER`（"Psyker Fugitive Hunters"） | `hunter/psyker` · **Psyker Fortune-Telling Ship**（灵能算命船） | **3**（psyker + captain + seer） | 灵能帮派：GORE 兴奋剂、灵能增幅甲、热能单片镜、灵能耳机 | Fortune-Telling Entertainment Shuttle：顺带提供算命服务 |
| 5 | `HUNTER_PACK_MI13`（"MI13 Fugitive Hunters"） | `hunter/mi13_foodtruck` · **Perfectly Ordinary Food Truck**（"平平无奇"餐车） | **2**（agent + chef 伪装） | 渗透特工：消音手枪+微型炸弹植入体+变色龙 ID，餐车伪装 | Nanotrasen Intrusion Countermeasures Electronics：伪装成"监控网络安全" |

**猎人团通用规则**：
- 所有猎人获得 `/datum/antagonist/fugitive_hunter`（属性见第五章），`silent = TRUE`（greet 由出生舱调用）。
- 每团专属入队台词 + 通用底线：*"You are not an antagonist in that you may kill whomever you please, but you can do anything to ensure the capture of the fugitives, even if that means going through the station."*（不是随便杀人的反派，但为捕获逃犯可以做任何事——包括穿过整座空间站）。
- 俄式猎人额外获得**边境语**（`/datum/language/spinwarder`，source = `LANGUAGE_BOUNTYHUNTER`）并设为当前语言；移除反派人设时收回。
- ID 均为 `/obj/item/card/id/advanced/bountyhunter`（"Bounty Hunter"，火焰卡面，trim `bounty_hunter`，权限 `ACCESS_HUNTER`（define 值 `"hunter"`））；ERT 变体用 `/datum/id_trim/centcom/bounty_hunter`。

### 4.3 追踪与捕获手段

#### 4.3.1 赏金定位器（Bounty Locator）—— 核心追踪手段

| 项 | 值 |
|---|---|
| 机器 | `/obj/machinery/fugitive_locator`（"Bounty Locator"，紫色支配者外观） |
| 冷却 | **40 秒**（`COOLDOWN_START(..., 40 SECONDS)`） |
| 机制 | 从 `GLOB.antagonists` 洗牌后随机挑一个**存活、未被捕获**的逃犯，广播其**真名 + 区域名**：*"Bounty Target Located. Bounty ID: {名字}. Location: {区域}"* |
| 无目标 | 播报 "No bounty targets detected." |
| 冷却中 | 气泡提示 "locator recharging!" |
| 注释 | "Whether it be bluespace entanglement or a simple RFID implant, this machine will find you no matter where they're hiding." |

#### 4.3.2 蓝空间捕获机（Bluespace Capture Machine）—— 胜负判定核心

| 项 | 值 |
|---|---|
| 机器 | `/obj/machinery/fugitive_capture`（"bluespace capture machine"） |
| 抗性 | `INDESTRUCTIBLE \| LAVA_PROOF \| FIRE_PROOF \| ACID_PROOF`——"ha ha no getting out!!"（出不去） |
| 捕获方式 | 猎人把逃犯**拖拽（mouse_drop）**到机器上，`do_after 5 秒`（`NEED_DEXTERITY`） |
| 非逃犯 | 拒绝并提示 "This is not a wanted fugitive!" |
| 捕获结果 | `forceMove` 进机器 → `is_captured = TRUE` → 逃犯被强制**幽灵化**（`ghostize(TRUE)`，"以免他们自杀影响回合结算"）→ 提示 "You have failed to avoid capture." |
| 灵能版 | `/obj/machinery/fugitive_capture/psyker`（"psyker recreation cell"，噪音+痛觉改造的休闲舱）：`process()` 每 tick 把 **1 格范围**（range 1）内的逃犯**直接吸进去**——灵能者不用拖拽 |

#### 4.3.3 猎人专属装备（`hunter_gear.dm`）

| 物品 | 效果 |
|---|---|
| EVA 补给箱（`crate/eva`） | 内容物：EVA 太空服 ×3、EVA 头盔 ×3、呼吸面罩 ×3、氧气罐 ×3 |
| 灵能导航扭曲器（psyker 版穿梭机导航电脑） | `INTERACT_MACHINE_ALLOW_SILICON`（硅基可操作，盲人友好）；y 偏移 11 |
| GORE 自动注射笔（`medipen/gore`） | 容量 **15u**、内含 **15u 脏克朗卡因**（`/datum/reagent/drug/kronkaine/gore`）——超强兴奋剂，"别一次打两针" |
| 灵能增幅反应甲（`reactive/psykerboost`） | 被攻击时触发：**重置穿戴者全部灵能法术冷却**（`SCHOOL_PSYCHIC`）；被 EMP 时反向：**给灵能法术强制进入冷却**；颜色 `#d6ad8b` |
| 灵能耳机（`headset/psyker`） | 频道 `FREQ_FUGITIVE_HUNTER`（**1243**）；佩戴者获得防闪光弹听力保护组件；有 `TRAIT_ECHOLOCATOR` 特质者额外获得 `TRAIT_SIGHT_BYPASS`（声呐视觉），摘下即失 |
| 灵能先知耳机（`headset/psyker_seer`） | 同上，seer 专属外观 |
| 灵能手枪腰带（`belt/holster/psyker`） | 5 格（`max_slots = 5`），容量按 .38 左轮弹匣 + 4×速装器计算 |
| 蹦蹦城堡（`bouncy_castle`） | 全图流血彩蛋：`blood_walk` 组件（血型血液、生成概率 **66.6**、血量上限 `INFINITY`）+ `bloody_spreader` 组件；钝击/燃烧不同音效（50/100 音量） |
| 算命纸条（`paper/crumpled/fluff/fortune_teller`） | 风味：水晶球其实是泡泡糖，"别嘴馋嚼了它" |

#### 4.3.4 猎人团装备差异（`hunter_outfits.dm`）

| 团 | 关键装备 |
|---|---|
| **Spacepol** | 太空警服、蓝衫防弹背心、**m1911 手枪**（.45 弹匣左口袋）+ 手铐、战术抓捕手套（combat tackler）、警监帽、SWAT 太空警声控面罩、墨镜；ID 黑卡面（"更低调"）、trim "Police Officer"（`COLOR_STRONG_BLUE`） |
| **Russian** | 苏联制服（**80%** 概率换成 4 种随机款）、防弹衣（**50%** 换俄式马甲/大衣）、**栓动步枪**（suit_store）+ strilka310 速装器、扎带、**50%** 换熊皮帽/乌尚卡/俄盔；领袖固定乌尚卡+战斗靴、**跳过所有随机化**；制服传感器强制关闭（`SENSOR_OFF` + `NO_SENSORS`），ID 写 "Russian Bounty Hunter" |
| **Bounty-armor** | 太空猎人服、牛仔帽、**双管自动霰弹枪**（主手）+ 火焰弹手枪（副手）、瓦斯猎人面具、gar 墨镜；背包：**橡胶弹 ×4 + 无焰燃烧弹 ×4** |
| **Bounty-hook** | 稻草人帽/面具、**钩子霰弹枪**（主手，发射勾人弹）、园艺皮手套；背包：**失能弹 ×6** |
| **Bounty-synth** | 防暴甲、眼罩、医疗包（主手）；背包：**猎人陷阱 ×4**（`/obj/item/bountytrap`） |
| **Psyker** | 灵能增幅反应甲（队长）、trickblindfold 盲眼布、灵能耳机、GORE 针 ×2（双口袋）；**post_equip 执行 `psykerize()`**（转化为灵能者）；队长红眼镜+迷彩裤、seer 热能单片镜+先知耳机+双扎带 |
| **MI13** | 辛迪加狙击手制服、**消音手枪**（枪+消音器分装双口袋）、**爆炸植入体**（`/obj/item/implant/explosive`，被俘自爆）、黑色变色龙 ID、辛迪加生存盒；背包三选一随机：① 弹匣（m9mm 普通 **80**/空尖 **10**/穿甲 **5**/火焰 **5**）② 近战/工具（**EDagger 笔刀 40**/战斗刀 30/闪光 30）③ 道具（**C4 20/自由植入器 20/变色龙面具 20/暗号手册 10/假邮件装置 10/热能镜 10/克制的"渔夫"手枪 10**）；副手厨师伪装：厨师帽+围裙+假胡子 |

### 4.4 猎人飞船与航行

| 项 | 值 |
|---|---|
| 穿梭机 ID | `huntership`（`/obj/machinery/computer/shuttle/hunter`，需 `ACCESS_HUNTER`） |
| 可停靠目的地 | `huntership_home; huntership_custom; whiteship_home; syndicate_nw`（**4 个**：自家、自定义、白船、辛迪加西北） |
| 导航电脑 | `/obj/machinery/computer/camera_advanced/shuttle_docker/syndicate/hunter`：`view_range = 4.5`、`see_hidden = FALSE`、`lock_override = CAMERA_LOCK_STATION`、预设跳转港 **3 个**（huntership_home/whiteship_home/syndicate_nw） |
| 船模板 | `code/datums/shuttles/hunter.dm`：`port_id = "hunter"`，5 张图（见 4.2 表） |

---

## 五、核心属性（antag datum 全量）

### 5.1 `/datum/antagonist/fugitive`（逃犯）

| 属性 | 值 |
|---|---|
| `name` | "\improper Fugitive" |
| `roundend_category` | "Fugitive" |
| `pref_flag` | `ROLE_FUGITIVE`（"Fugitive"） |
| `show_in_antagpanel` | **FALSE**（不进反派面板） |
| `show_to_ghosts` | **TRUE**（幽灵可见） |
| `antagpanel_category` | `ANTAG_GROUP_FUGITIVES` = "Escaped Fugitives" |
| `antag_hud_name` | "fugitive" |
| `suicide_cry` | "FOR FREEDOM!!" |
| `preview_outfit` | `/datum/outfit/prisoner`（预览图：囚犯主体 + 左邪教徒 + 右沃尔多，半透明背景） |
| `antag_flags` | `ANTAG_SKIP_GLOBAL_LIST` |
| 自定义变量 | `fugitive_team`（/datum/team/fugitive）、`is_captured`（默认 FALSE）、`backstory`（默认 "error"） |
| `on_gain` | forge_objectives → 职业改为 /datum/job/fugitive |
| `on_removal` | 职业改回 /datum/job/unassigned |
| `apply_innate_effects` | `add_team_hud`（队伍 HUD） |

### 5.2 `/datum/antagonist/fugitive_hunter`（猎人）

| 属性 | 值 |
|---|---|
| `name` | "Fugitive Hunter" |
| `roundend_category` | "Fugitive"（与逃犯同类别，回合结算同栏） |
| `silent` | **TRUE**（greet 由出生舱 special() 手动调用） |
| `show_in_antagpanel` | FALSE |
| `show_to_ghosts` | TRUE |
| `antagpanel_category` | `ANTAG_GROUP_HUNTERS` = "Bounty Hunters" |
| `antag_hud_name` | "fugitive_hunter" |
| `suicide_cry` | "FOR GLORY!!" |
| `antag_flags` | `ANTAG_SKIP_GLOBAL_LIST` |
| 自定义变量 | `hunter_team`（/datum/team/fugitive_hunters，携带 `backstory`）、`backstory` |
| 俄式特殊 | `apply_innate_effects` 授予并激活边境语 spinwarder；`remove_innate_effects` 收回 |

### 5.3 关联 define 速查（`code/__DEFINES/`）

| Define | 值 | 位置 |
|---|---|---|
| `ROLE_FUGITIVE` | "Fugitive" | role_preferences.dm:33 |
| `ROLE_FUGITIVE_HUNTER` | "Fugitive Hunter" | role_preferences.dm:114 |
| `FREQ_FUGITIVE_HUNTER` | **1243** | radio.dm:90 |
| `ACCESS_HUNTER` | "hunter" | access.dm:201 |
| `ANTAG_GROUP_FUGITIVES` | "Escaped Fugitives" | antagonists.dm:437 |
| `ANTAG_GROUP_HUNTERS` | "Bounty Hunters" | antagonists.dm:438 |
| `FUGITIVE_RESULT_BADASS_HUNTER` ~ `FUGITIVE_RESULT_MAJOR_FUGITIVE` | **0-8 共 9 个** | antagonists.dm:17-25 |
| `FUGITIVE_BACKSTORY_WALDO / INVISIBLE` | "waldo" / "invisible"（单人） | events.dm:49-50 |
| `FUGITIVE_BACKSTORY_PRISONER / CULTIST / SYNTH` | "prisoner" / "cultist" / "synth"（团队） | events.dm:52-54 |
| `HUNTER_PACK_COPS / RUSSIAN / BOUNTY / PSYKER / MI13` | 见 4.2 表 | antagonists.dm:451-455 |
| `TRANSITIONEDGE` | 8（船加载边缘留白） | maps.dm:47 |

---

## 六、目标系统

### 6.1 逃犯目标（存活）

```
/datum/antagonist/fugitive/forge_objectives():
  目标 = new /datum/objective
  explanation_text = "Avoid capture from the fugitive hunters."
  objectives += 目标
```

- 代码注释明确：*"this isn't the actual survive objective because it's about who in the team survives"* —— 这不是真正的存活判定目标，而是**团队存活名单占位**（回合结算按 GLOB.antagonists 里谁死/谁被捕获统计）。
- 逃犯的通用底线提示：*"You are not an antagonist in that you may kill whomever you please, but you can do anything to avoid capture."*（不是滥杀反派，但为躲避捕获可以做任何事）。
- 目标完成判定：使用基础 `/datum/objective/check_completion()`（直接返回 `completed`，默认 FALSE）——纯展示性目标，无机械判定。
- 捕获即出局：被塞进捕获机 → `is_captured = TRUE` + 强制 ghostize。

### 6.2 猎人目标（捕获）

```
/datum/antagonist/fugitive_hunter/forge_objectives():
  目标 = new /datum/objective
  explanation_text = "Capture the fugitives in the station and put them into the bluespace capture machine on your ship."
  objectives += 目标
```

- 团队目标：`/datum/team/fugitive_hunters/update_objectives()` 清空后塞入一个无文本团队目标（`objectives = list()` + 新 objective 设 `team`）——占位用。
- 捕获判定：`assemble_fugitive_results()` 遍历全部逃犯，分三桶：**总数 / 已死（stat == DEAD）/ 已捕获（is_captured）**，供结局判定。

---

## 七、结果判定系统（9 种结局）

> `get_result()` 决策树（`hunter.dm` L115-144）——以"是否捕获 / 是否全捕获 / 是否存活 / 猎人是否全灭"四条件组合出 9 种结果（8 种具名 + 1 个异常兜底 "Prank Call!"）。

```
① 无逃犯 → 返回 null → 回合报告显示 "Prank Call!"（"猎人们被叫来，却一个逃犯都没有…?"）
② 有捕获：
   全捕获 + 无死亡 → FUGITIVE_RESULT_BADASS_HUNTER（"狠人猎人胜利"：全活捉）
   全捕获 + 猎人全灭 → FUGITIVE_RESULT_POSTMORTEM_HUNTER（"死后猎人胜利"）
   全捕获 + 其他 → FUGITIVE_RESULT_MAJOR_HUNTER（猎人重大胜利：死活不论全捕获）
   部分捕获 + 猎人存活 → FUGITIVE_RESULT_HUNTER_VICTORY（猎人胜利）
   部分捕获 + 猎人全灭 → FUGITIVE_RESULT_MINOR_HUNTER（猎人小胜）
③ 无捕获（猎人未得手）：
   无死亡 → FUGITIVE_RESULT_MAJOR_FUGITIVE（逃犯重大胜利：全部存活）
   部分死亡 → FUGITIVE_RESULT_FUGITIVE_VICTORY（逃犯胜利：至少一人活）
   全灭 + 猎人存活 → FUGITIVE_RESULT_MINOR_FUGITIVE（逃犯小胜：全死但没被回收）
   全灭 + 猎人全灭 → FUGITIVE_RESULT_STALEMATE（血腥僵局：全死、零回收）
```

| # | 结局 define | 回合报告标题 | 条件 |
|---|---|---|---|
| 0 | `FUGITIVE_RESULT_BADASS_HUNTER` | Badass [团名] Victory! | 全捕获且全活 |
| 1 | `FUGITIVE_RESULT_POSTMORTEM_HUNTER` | Postmortem [团名] Victory! | 全捕获但猎人全灭 |
| 2 | `FUGITIVE_RESULT_MAJOR_HUNTER` | Major [团名] Victory | 全捕获（死活不论） |
| 3 | `FUGITIVE_RESULT_HUNTER_VICTORY` | [团名] Victory | 捕获≥1 且猎人存活 |
| 4 | `FUGITIVE_RESULT_MINOR_HUNTER` | Minor [团名] Victory | 捕获≥1 但猎人全灭 |
| 5 | `FUGITIVE_RESULT_STALEMATE` | Bloody Stalemate | 逃犯全灭+猎人全灭+零回收 |
| 6 | `FUGITIVE_RESULT_MINOR_FUGITIVE` | Minor Fugitive Victory | 逃犯全灭但零回收（猎人活） |
| 7 | `FUGITIVE_RESULT_FUGITIVE_VICTORY` | Fugitive Victory | 至少一逃犯存活且零回收 |
| 8 | `FUGITIVE_RESULT_MAJOR_FUGITIVE` | Major Fugitive Victory | 全部逃犯存活且零回收 |
| — | null（无逃犯） | Prank Call! | 没逃犯却召唤了猎人 |

**回合报告格式**（`/datum/team/fugitive_hunters/roundend_report`）：红边框面板 "…And **[N]** [团名]s tried to hunt them down!" + 成员名单 + 结局横幅；`/datum/team/fugitive/roundend_report` 显示 "[N] fugitive(s) took refuge on [站名]!" + 逃犯名单。

---

## 八、NOVA 专属改动

| # | 位置 | 改动 | 影响 |
|---|---|---|---|
| 1 | `fugitive.dm` / `fugitive_equipment.dm` / `hunter.dm` / `hunter_gear.dm` 首行 | **I18N CODEMOD**（NOVA EDIT）：玩家可见字符串全部改写为 `LANG("datum.xxxx", null)` 调用，文案移至 `strings/i18n/` | 纯本地化重构，无机制变化；逃犯 4 个源码文件均带此标记（outfits 两文件无玩家字符串未标记） |
| 2 | `dynamic_ruleset_midround.dm:258`（from_ghosts 基类，影响逃犯报名） | `poll_time = 1 MINUTES`（NOVA EDIT CHANGE，原版 **30 SECONDS**） | 幽灵报名轮询从 30 秒放宽到 **1 分钟**——报名窗口翻倍 |
| 3 | `modular_nova/` | **无**任何 fugitive 覆盖文件 | 机制层 NOVA 未改动（仅 i18n + 报名时长） |

> 对照：逃犯规则集参数（weight 3 / min_pop 20 / cap 4 / 10 分钟倒计时）与上游 /tg/ 一致，非 NOVA 改动。

---

## 九、对战攻略

> 以下为基于源码机制的玩法建议（非源码声明）。

**逃犯侧**：
- 头 **10 分钟**是黄金准备期：合成人赶紧用义体信标改造、找武器/防具、堵维护层通路；广播一到猎人随时可能靠港。
- 5 秒拖拽 + 蓝空间捕获机是唯一"正式出局"方式——**别站着不动**；被捕获前宁可战死（尸体也算猎人"回收"，但至少算僵局而非活捉）。
- 沃尔多脱不掉衣服、隐形人怕 EMP（炮台/EMP 弹会破隐）；合成人可尝试"解放"站上硅基生命。
- 共享队伍 HUD，抱团 vs 分散都有说法：猎人 Bounty Locator 一次只报一个随机目标（40 秒冷却），人多时追踪效率骤降。

**猎人侧**：
- 到站先开 **Bounty Locator**（40 秒冷却报名字+区域），别满站瞎逛。
- 捕获机在船上——把人拖回船再拖进机器；灵能队直接站在机器旁等 1 格吸入即可。
- 你们"可以做任何事"但**不是随便杀人的反派**——站内安保理论上没义务配合（广播也明说"不知道会不会合作"）。
- MI13 被俘会触发爆炸植入体；俄式猎人说话是边境语（队友专属语言）。

**站内阵营**：无需强制协助任何一方；逃犯≠普通罪犯，站规处置尺度由安保自行判断（源码未定义对逃犯的执法规则）。

---

## 十、数值速查表

| 类别 | 项 | 值 |
|---|---|---|
| 规则集 | 权重 / 最少人数 / 逃犯上限 / 逃犯下限 | 3 / 20 / 4 / 3（可缩到 1） |
| 规则集 | 事件类型 / 可重复 | LIGHT_MIDROUND / FALSE |
| 时序 | 猎人倒计时 / 检查间隔 | 10 分钟 / 1 分钟 |
| 时序 | 幽灵报名轮询（NOVA） | 1 分钟（原版 30 秒） |
| 缩放 | 单人判定 | 报名 ≤1 或 prob(30 − 人数×2) |
| 背景 | 单人背景数 / 团队背景数 | 2（沃尔多、隐形人）/ 3（邪教徒、越狱犯、合成人） |
| 猎人 | 猎人团数 / 团人数 | 5 / 2-4（警 2、俄 4、赏金 3、灵能 3、MI13 2） |
| 猎人 | 追踪冷却 / 捕获耗时 | 40 秒 / 5 秒（do_after） |
| 猎人 | 灵能捕获范围 | 1 格（range 1） |
| 猎人 | 通讯频道 / 船 ID / 导航视野 | 1243 / huntership / 4.5 |
| 猎人 | 可停靠目的地 / 预设跳转港 | 4 / 3 |
| 隐形 | 隐形 alpha / EMP 破隐概率 | 35 / prob(15 × 严重度) |
| 隐形 | 隐形针容量 / 药量 / 时长 | 20u / 20u 稳定 Saturn-X / ≈10 分钟 |
| 俄式 | 随机化概率（制服/防弹衣/头盔） | 80% / 50% / 50% |
| MI13 | 弹匣权重 / 近战权重 / 道具权重 | 80·10·5·5 / 40·30·30 / 20·20·20·10·10·10·10 |
| 赏金 | 甲士弹药 / 钩子弹药 / 医疗兵陷阱 | 橡胶×4+燃烧×4 / 失能×6 / 陷阱×4 |
| 装备 | GORE 针容量 / 药量 | 15u / 15u 脏克朗卡因 |
| 装备 | 灵能腰带格数 | 5 |
| 装备 | EVA 补给箱 | 服×3 + 盔×3 + 面罩×3 + 氧罐×3 |
| 彩蛋 | 蹦蹦城堡流血概率 / 血量上限 | 66.6 / INFINITY |
| 结局 | 结局种类（define） | 9（0-8） |
| 其他 | 船加载边缘留白（TRANSITIONEDGE） | 8 |
| 其他 | 逃犯改名格式 | NTP #CC-0[111-999] |
| 其他 | 猎人 ID 权限 | ACCESS_HUNTER = "hunter" |

---

## 附录 · 审计记录

- 审计脚本：`_audit_fugitive.py`（与本文档同目录），对照源码逐项提取并验证本文档数值声明。
- 方法与范围：每项声明从指定源码文件经正则提取 → 与期望值比对（源码侧）→ 确认文档包含该值与关键词（文档侧）；猎人船人数直接统计 5 张 `.dmm` 地图内幽灵出生舱数量。
- 结果：**85 项检查全部通过**（85 PASS / 0 FAIL），覆盖：规则集 8 项、时序缩放 3 项、背景与猎人团 4 项、define 3 项、隐形人装备 5 项、捕获/定位/灵能 7 项、EVA 箱 1 项、蹦蹦城堡 2 项、猎人船人数 5 项、antag 属性 13 项、结局 define 1 项、飞船航行 4 项、MI13 权重 7 项、装备细节 8 项、NOVA/流程 5 项、目标与船只 10 项。
- 文档正文数值 100% 来自源码 grep/read，无推测值；攻略章节为机制推论并已标注。

*本文档为 TianGuan13 反派系统百科系列的逃犯（Fugitive）独立篇。*
