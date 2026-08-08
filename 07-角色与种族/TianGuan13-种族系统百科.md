# TianGuan13 种族系统与种族特质百科 (Species & Traits Encyclopedia)

> 基于 TianGuan13 NovaSector 分支源码全量整理。种族定义：`code/modules/mob/living/carbon/human/species_types/`（基础）+ `modular_nova/modules/customization/.../species/`（NOVA）+ 各专属模块。
> 本文档全量列出 **50 个玩家可选种族**（基础 24 + NOVA 27 = 51 条记录，Slimeperson/开局史莱姆双计去重后 50；**开局可选用 35 个** = 基础 8 + NOVA 27，均经 `config/game_options.txt` ROUNDSTART_RACES 逐行核实），每个含源码路径+关键数值+机制+评价，无省略。

## 目录

- [一、开局配置总览](#一开局配置总览)
- [二、基础种族全录（24 个）](#二基础种族全录24-个)
- [三、NOVA 种族全录（27 个）](#三nova-种族全录27-个)
- [四、相关模块区分](#四相关模块区分)
- [五、种族特质全录](#五种族特质全录)
- [六、特质全量总览（矩阵）](#六特质全量总览矩阵)

---

## 一、开局配置总览

**roundstart 官方开闸**（`config/game_options.txt` 第 374-427 行，NOVA 编辑后）：

**基础开闸 8 种**：human, lizard, felinid, fly, moth, plasmaman, ethereal, snail（shadow/jelly/abductor/skeleton/zombie/pod/ghost/android/golem 注释关闭）

**NOVA 开闸 27 种**：mammal, vulpkanin, tajaran, akula, unathi, skrell, humanoid, xeno, slimeperson, podweak, dwarf, synth, vox, aquatic, insect, insectoid, ghoul, teshari, hemophage, vox_primalis, abductorweak, golemweak, dullahan, kobold, protean, shadekin, holosynth

> 注：Ashwalker 灰烬行者**不在**开局列表（熔岩星转化/蛋生专属，见三章专节）；NOVA 开闸含 dullahan，因其定义已并入基础表第 15 行（去重），NOVA 种族表以 Ashwalker 补足 27 行。

---

## 二、基础种族全录（24 个）

**源码**: `code/modules/mob/living/carbon/human/species_types/`

| # | 种族 | 源文件 | 核心机制 |
|---|---|---|---|
| 1 | **Human 人类** | humans.dm | payday 1.1，基准种族 |
| 2 | **Lizardperson 蜥蜴人** | lizardpeople.dm | coldmod 1.5/heatmod 0.67，firebreath 惰性突变，耐高温/低温 |
| 3 | **Felinid 猫人** | felinid.dm | 猫系优雅、被狗讨厌、怕水 |
| 4 | **Flyperson 蝇人** | flypeople.dm | 苍蝇全套变异器官，翅膀 |
| 5 | **Mothman 蛾人** | mothmen.dm | 蛾翅可低重力飞行，燃烧事件烧毁翅膀 |
| 6 | **Plasmaman 等离子人** | plasmamen.dm | 需太空服，heatmod 1.5，体温-40，变异体 MOB_MINERAL\|SKELETAL |
| 7 | **Ethereal 以太体** | ethereal.dm | 能量摄入，siemens 0.5，体温+50，EMP/闪光弹干扰，发光 |
| 8 | **Snail 蜗牛人** | snail.dm | 无滑倒、水下呼吸、贝壳背包 armor melee40/bullet30/laser30，软体 |
| 9 | **Jellyperson 果冻人** | jellypeople.dm | 再生肢体，毒液血液，coldmod6/heatmod0.5，毒素爱好者 |
| 10 | **Slimeperson 史莱姆人** | jellypeople.dm:186 | 分身/换体 |
| 11 | **Luminescent 发光体** | jellypeople.dm:491 | 整合/发射提取物 |
| 12 | **Stargazer 观星者** | jellypeople.dm:681 | 心灵感应 |
| 13 | **Abductor 外星掳掠者** | abductors.dm | 外星人 HUD，不呼吸不饥饿，粗手指 |
| 14 | **Android 安卓** | android.dm | 全机械免疫：毒/辐射/压力/火/穿刺，机器人器官 |
| 15 | **Dullahan 无头骑士** | dullahan.dm | 头部感知切换，不死族 |
| 16 | **Golem 魔像** | golems.dm | 免疫火/岩浆/压力，不可装备，矿物生物 |
| 17 | **Spirit/Ghost 幽灵** | ghost.dm | 漂浮移动，灵体，万圣节限定 |
| 18 | **Shadow 暗影人** | shadowpeople.dm | 暗处回血，光下受伤，万圣节限定 |
| 19 | **Skeleton 骷髅** | skeletons.dm | 全环境免疫+毒免疫，易断肢 |
| 20 | **Vampire 吸血鬼** | vampire.dm | 血族，持续失血，吸血，万圣节限定 |
| 21 | **Zombie（高智商）丧尸** | zombies.dm | 不死，NODEATH，火/毒/辐射免疫 |
| 22 | **Mushroomperson 蘑菇人** | mushpeople.dm | 不呼吸、抗闪光、heatmod1.5，菌盖 |
| 23 | **Podperson 植物人** | podpeople.dm | 光合作用营养，血浆 H2O，heatmod1.5 |
| 24 | **Monkey 猴子** | monkeys.dm | 弱智，wrench 焊接 |

---

## 三、NOVA 种族全录（27 个）

**源码**: `modular_nova/modules/customization/.../species/` + 各专属模块

| # | 种族 | 源文件 | 核心机制 |
|---|---|---|---|
| 25 | **Anthromorph/Mammal 哺乳类** | customization/.../species/mammal.dm | 毛绒模板 |
| 26 | **Vulpkanin 狐人** | species/vulpkanin.dm | 狐耳狐尾 |
| 27 | **Tajaran 猫族** | species/tajaran.dm | 猫型：猫系优雅+怕水+被狗讨厌 |
| 28 | **Akula 鲨鱼人** | species/akula.dm | 鲨鱼，需保湿 10 分钟，水呼吸，滑溜皮肤 |
| 29 | **Unathi 蜥蜴族** | species/unathi.dm | 蜥蜴类，锐利齿爪 |
| 30 | **Skrell 乌贼人** | species/skrell.dm | 体温+70，冷空气伤害，两栖肺 |
| 31 | **Humanoid 人形** | species/humanoid.dm | 万能自定义模板 |
| 32 | **Xeno 异形混血** | species/xeno.dm | 树脂/血巢，等离子容器，heatmod 2.5 |
| 33 | **Slime 开局史莱姆混合** | species/roundstartslime.dm:585 | 半透明 alpha155 |
| 34 | **Podperson(弱) 植物人弱化** | species/podweak.dm | 光合自愈，光多回血，starving 扣血 |
| 35 | **Dwarf 矮人** | species/dwarf.dm | TRAIT_DWARF+SNOB，矮体型 |
| 36 | **Synthetic 合成人** | synths/code/species/synthetic.dm | 机器器官全替换，机油血液，EMP/emag 失魂，self-revival |
| 37 | **Holosynth 全息合成人** | holosynth/code/holosynth.dm | 半透明，防爆3倍/灼烧5倍，扫描线，玻璃穿过 |
| 38 | **Vox 鸟人** | species/vox.dm | 氮气呼吸，冷抗热敏感，专用装备 |
| 39 | **Vox Primalis 原始鸟人** | better_vox/code/vox_species.dm | 原始种，特殊贴图 |
| 40 | **Aquatic 水族** | species/aquatic.dm | 水呼吸 |
| 41 | **Insect 昆虫** | species/insect.dm | 蜂翅 |
| 42 | **Insectoid 蛛型** | species/insectoid.dm | 织网+网上下移动，蜘蛛肉 |
| 43 | **Ghoul 食尸鬼** | species/ghoul.dm | 皮包骨，易断肢，肉块接肢，自拆肢拾取 |
| 44 | **Teshari 鸟形族** | teshari/code/_teshari.dm | 鸟人，体温-30K，冷mod0.67热mod1.3，穿桌 |
| 45 | **Hemophage 噬血者** | species/hemophage/ | 持续失血+阴影回血，吸血，自愈 |
| 46 | **Rogue(弱 Abductor)** | species/abductorweak.dm | 外星人 HUD，粗手指 |
| 47 | **Golem(弱)** | species/golemweak.dm | 饿得慢，不可吃蓝空间矿 |
| 48 | **Kobold 狗头人** | master_files/.../kobold.dm | 猴子级，通风管裸爬行，不可屠宰 |
| 49 | **Protean 液态金属** | species/protean_species.dm | 纳米机器 MOD 套装壳体，NODEATH，铁血，易断肢 |
| 50 | **Shadekin 影狐** | species/shadekin.dm | 不呼吸，夜视，水呼吸，滑溜皮肤，亮度≤35 深色 |
| 51 | **Ashwalker 灰烬行者** | ashwalkers/code/species/Ashwalkers.dm | 蜥蜴亚种，灰烬风暴免疫+夜视+原始人，熔岩星转化/蛋生（**非开局**），老化进化+仪式系统，见下方专节 |

### Ashwalker 灰烬行者（亚种专节）

> 源码：`modular_nova/modules/ashwalkers/`（species/effects/buildings/clothing/items/turfs 全套，约 350 行）+ `modular_nova/modules/bodyparts/code/ashwalker_bodyparts.dm`（专属肢体）+ master_files 补丁（antag/语言/权限）。熔岩星（Lavaland）部落种族，**非开局可选**。

**定位与获得方式**

- 出生：幽灵角色蛋生 `/obj/effect/mob_spawn/ghost_role/human/ash_walker`（职阶 `/datum/job/ash_walker`、初始装备 `/datum/outfit/ashwalker`，"You are an ash walker. Your tribe worships the Necropolis."）
- 转化：非灰烬行者徒手触碰母卷须巢 `/obj/structure/lavaland/ash_walker`，需 **2 名族人旁观**（REQUIRED_OBSERVERS=2）→ 确认后卸除全部装备、变身灰烬行者、挂 `/datum/antagonist/ashwalker`（ROLE_LAVALAND、ANTAG_FAKE、ANTAG_SKIP_GLOBAL_LIST）；拒绝受 50 钝击、旁观者不足受 10 钝击
- 阵营/团队：`FACTION_ASHWALKER` + `/datum/team/ashwalkers`（"Ash Walker Tribe"，目标保卫 Necropolis，结算报告记录献祭数与蛋数）
- 转化后带 `TRAIT_PRIMITIVE`（原始人）；冰月区域转化额外获得 `TRAIT_NOBREATH` + `TRAIT_RESISTCOLD`；语言为 `ashtongue` 灰烬语（master_files 白名单，负面语言词条）

**物种定义**（`code/species/Ashwalkers.dm`）

- `/datum/species/lizard/ashwalker`：蜥蜴人亚种
- 专属夜视眼 `/obj/item/organ/eyes/night_vision/ashwalker`（红蓝调暗视，低/中/高 cutoff = 22,12,17 / 33,18,26 / 75,41,61，定义于 customization/.../organs/eyes.dm）
- 六处肢体全部替换为 ashwalker 蜥蜴肢体（bodyparts 模块）
- `TRAIT_ASHSTORM_IMMUNE` 灰烬风暴免疫；攻击命中非族人时给目标挂 `ashwalker_damage` 状态（累计记录灰烬行者造成的伤害，供"分担伤害"仪式结算）

**ash_age 老化进化**（`code/species/Ashwalkers.dm` 组件+状态；每次进化后 **15 分钟冷却**（ash_age 状态，期间无法再进化），由"Ashen Age Ceremony 老化仪式"触发 COMSIG_RUNE_EVOLUTION；注释总时间线 15→90 分钟）

| 阶段 | 增益 |
|---|---|
| 1 | 减伤 `damage_modifier` +10 |
| 2 | 双臂徒手伤害 +5，`TRAIT_BOULDER_BREAKER` 碎石 |
| 3 | Ashen Hivemind Speak 蜂巢思维（全服灰烬行者广播，含死者/旁观者，"sings" 唱出） |
| 4 | 移速修正 `multiplicative_slowdown` −0.2（约 +20% 速度） |
| 5 | 植入 `ashen_armblade` 器官 → 可召唤灰烬臂刃 |
| 6 | `TRAIT_LAVA_IMMUNE` 岩浆免疫 + 火焰吐息行动（cooldown 类） |
| 7+ | 巅峰，不再进化 |

**灰烬臂刃 ashen blade**（进化 5 解锁）：骨肉刃，force 5、锐利（SHARP_EDGED，含刺击副形态）、穿甲 5、NODROP，可拆桌/撬门/拆电脑，附 butchering 组件（6 秒/80%）；可吞噬 crusher_trophy 战利品升级：**最多 7 个**，每个 force+5 / 穿甲+5 / 伤口+2，吞噬瞬间回 5 钝击 + 5 灼烧（25% 概率失败只吞不升）。

**仪式系统**（`code/effects/ash_rune.dm` + `ash_rituals.dm`，共 **16 种**）

- `ash rune` 符文：不可破坏（INDESTRUCTIBLE/LAVA/FIRE/ACID/FREEZE 全免疫），中央符文+四向副符文；灰烬行者直接执行已选仪式，外人不选则无法执行
- 仪式列表（方向组件按需消耗，失败则回滚）：

| 仪式 | 效果（消耗组件） |
|---|---|
| Summon Ash Staff | 召唤灰烬法杖（木+再生核心） |
| Summon Draconic Necklace | 龙语翻译项链（骨+再生核心+筋腱×2） |
| Summon Skeleton Key | 骷髅钥匙（骨×4，开卷须巢宝箱） |
| Summon Cursed Ash Knife | 诅咒匕首（再生核心+锻造匕首+骨+筋腱） |
| Summon Tendril Seed | 卷须种子（再生核心+诅咒匕首+巨蜥触须+观察者翼），手持使用长出卷须巢 |
| Summon Ore Seed | 矿石种子（军团头骨+再生核心+观察者翼+巨蜥触须），召唤矿脉 vent |
| Summon Tunneling Worm | 掘洞蠕虫（胆汁蠕虫卵+再生核心+蓝空间×2），可命名双向隧道 |
| Incite Megafauna | 招引巨兽（军团肿瘤×3+卷须种子）：随机血醉矿工/龙/圣像 |
| Ashen Age Ceremony | 老化仪式（军团肿瘤+再生核心+骨+筋腱）：触发 ash_age 进化 |
| Summon Lavaland Creature | 召唤 2 只熔岩生物（再生核心+灰龙皮+蓝空间×2） |
| Summon Icemoon Creature | 召唤 2 只冰月生物（再生核心+surik+蓝空间×2） |
| Uncover Strange Rocks | 揭示奇异岩石（蓝空间+巨蜥皮+刷子+破损物，30% 跳过） |
| Share Victim's Damage | 分担伤害（骨+筋腱）：中央受害者钝+灼伤害均分给周围 2 格内活着的族人并治愈受害者 |
| Banish Kin | 流放族人（免费）：范围内族人投票简单多数，通过后传送至随机小行星地块 |
| Revive Animal | 复活动物（再生核心×2+骨+筋腱）：复活并加入灰烬行者阵营 |
| Attune your body to the land | 身体调谐（火之花+再生核心+筋腱×2）：加入 FACTION_MINING_FAUNA + `TRAIT_PACIFISM`（放弃战斗换取废土生物友善） |

**卷须巢 / Necropolis**（`code/buildings/ash_tendril.dm`）

- 献祭 `consume()`：紧邻 1 格内尸体（非硅基）→ 卸装备 → 若有幽灵附着的族人 → 生成 occupied ashwalker egg（**30 秒后满血复活并强制还魂**）；否则 gib。巨兽 +20 肉计数 / 普通 +1；卷须巢恢复 5% HP；献祭者 **40% 概率**归还重生名额（players_spawned）；族人心境 `sacrifice_good`、外人心境 `sacrifice_bad`
- 再生核心：族人可将 regenerating core 放入卷须巢稳定（preserve，阻止衰减）
- 灰烬法杖充能：用 ash_staff 敲卷须巢充能 5 分钟
- 摧毁时 log_game 记录（含 7 格内人员名单）

**灰烬法杖 staff of the ashlands**（`items/ashwalker_shaman.dm`）：需卷须巢充能；对普通开阔地块（充能有效期内）2 秒后转化为熔岩星玄武岩地表（lava_land_surface）；对玄武岩地表 4 秒后化为岩浆（lava）——熔岩星"开垦/毁地"两用。

**原始工具与武器**

- 武器：Ash Macahuitl 黑曜石砍刀（force 15、wound_bonus 15、exposed_wound_bonus 10、防火/防熔岩；配方骨×2+筋腱×2+巨蜥皮×2，需学习）
- 骨制工具（`items/ash_tool.dm`）：primitive 螺丝刀/剪线钳/扳手/修枝剪/撬棍/凿子（toolspeed 4）/锻铁钳/锻铁锤/锻铁风箱，全部 bone 材料
- 原始手术工具（`items/ash_surgery.dm`）：cautery/drill/scalpel/saw/retractor/hemostat/bonesetter 七件套
- 通用配方 `/datum/crafting_recipe/ash_recipe`：骨+筋腱、4 秒、CAT_TOOLS、需学习（CRAFT_MUST_BE_LEARNED）

**农业与生产建筑**（readme：熔岩星种植对灰烬行者开放，非灰烬行者种植难度提高）

- 种植：`seed_mesh` 种子网（配沙子过滤大种子）、`simple_farm` 简易农场（增产/缩短冷却/收获）、`simple_tree` 简易树（嫁接种子→试剂、树汁 sap/蜂蜜/果实、施肥、采木、升级）、`plant_tank` 植物储罐、`wormfarm` 蚯蚓农场（worm_fertilizer 蚯蚓肥：EZ/Left4Zed/Robust 营养剂各 3u）、`antfarm` 蚁巢（吸引蚂蚁）、`gutlunch_trough` 食槽
- 水/燃料：`fuel_well` 燃料井（燃油）/ `brick_well` 砖井（水源，铲/焊枪维护）
- 交通：`railroad` 铁路（rail_track 轨道 10/50 堆叠 + rail_cart 矿车：可骑乘、挂拖车、铲子铲除）
- 其他：`ash_clothing_vendor` 部落衣物售货机、`worm_tunnel` 蠕虫隧道（双向传送、可命名）、`primitive_centrifuge` 原始离心机（分离/删除试剂）、thatch 茅草双人床
- 地块（`turfs/`）：熔岩星石地板 / 冰月木地板 / 冰月沙地；封闭地块可插火把（place_torch）

**专属衣物**（`clothing/`）：`ash_headdress` 头饰（winged 有翼版）、`shamanash` 萨满头饰、`ash_robes`/gladiator 系列 10 种下装、`ash_plates` 骨板甲（decorated 装饰版）、`bone_greaves` 骨护胫、`ashwalk` 手套 / `claw` 爪套、`ashwalkermantle` 披风（cape 版）、`tribalmantle` 部落斗篷、`jackboots` 靴（legate 版）、`wraps` 裹脚布（tribal/mundane 版）

---

## 四、相关模块区分

| 模块 | 说明 |
|---|---|
| `modules/spider/` | 巨型蜘蛛**敌人**（非玩家种族，见 Nova 上卷） |
| `modules/mutants/` | 变种怪**感染型**（非开局可选） |
| `modules/ashwalkers/` | **Ashwalker 灰烬行者**专属模块（物种/仪式/卷须巢/建筑/衣物/物品全套，见三章专节）；另配 `modules/bodyparts/code/ashwalker_bodyparts.dm` 专属肢体 |
| `digitigrade_cybernetics` / `vox_sprites` / `alt_vox` | 外观/音效扩展（趾行腿/鸟人贴图/AI 语音） |
| `master_files/.../species_type/xenohybrid.dm` | 仅含 `/datum/mood_event/xenohybrid_resin`（树脂心情 +2），**非种族**，勿与种族表混淆 |

---

> **文档完** — 50 个玩家种族全录（基础 24 + NOVA 27 = 51 条记录，Slimeperson/开局史莱姆双计去重后 50），开局可选 **35** 个（基础 8 + NOVA 27，`config/game_options.txt` 核实），全部含源码路径与核心机制。自查无"等 N 种"省略。

---

## 五、种族特质全录

> 每个物种的固有特质（inherent_traits）+ 动态特质（on_species_gain/技能/器官机制）全量。特质中文含义依据 `code/__DEFINES/traits/declarations.dm` 官方注释翻译。
> **基类** `/datum/species` 的 inherent_traits 默认为空——以下全部为各物种自行定义。
> 5 批提取（基础 22 + NOVA 27+），每物种含：固有特质全表 + 动态特质 + 独有标注 ★。

### 5.1 基础种族特质（批 1-3）

### 1. 人类 Human（humans.dm）

主类：`/datum/species/human`（id = SPECIES_HUMAN）

- **固有特质（inherent_traits，1 条）**
  - `TRAIT_USES_SKINTONES` — 使用肤色系统着色（可用肤色自定义外观）
- **动态特质（on_species_gain 等）**
  - 无（本文件未定义 on_species_gain，无 ADD_TRAIT）
- **器官隐含特质（mutant organs）**
  - 无特殊突变器官（`skinned_type = 人皮`，剥皮得人类皮革）
- **其他属性**
  - `payday_modifier = 1.1`（工资 1.1 倍）
  - 特有偏好：默认法律（Asimov）相关 perk、人类权威（human_authority）perk（create_pref_unique_perks，配置驱动）
- **独有特质**：无独有 TRAIT（`TRAIT_USES_SKINTONES` 与猫人共享）
- **共享基类**：`TRAIT_USES_SKINTONES` 亦见于猫人（felinid）

---

### 2. 蜥蜴人 Lizardperson（lizardpeople.dm）

主类：`/datum/species/lizard`（id = SPECIES_LIZARD）

- **固有特质（inherent_traits，1 条）**
  - `TRAIT_MUTANT_COLORS` — 使用突变色系统着色（自定义体色）
- **动态特质**
  - 无 ADD_TRAIT；但 `inert_mutation = /datum/mutation/firebreath` — 惰性基因突变【喷火】（经基因操作可激活）
  - 冷血机制：`body_temperature_core()` 直接 return，**不自动调节体温**（环境决定体温）
  - 耐热怕冷：`coldmod = 1.5`（寒冷伤害倍率提升）、`heatmod = 0.67`（炎热伤害倍率降低）、`bodytemp_heat_damage_limit = BODYTEMP_HEAT_LAVALAND_SAFE`（可承受拉瓦兰高温）、`bodytemp_cold_damage_limit = BODYTEMP_COLD_DAMAGE_LIMIT - 10`
- **器官隐含特质（mutant organs）**
  - `mutantbrain = /obj/item/organ/brain/lizard` — 蜥蜴脑：`variant_traits_added = list(TRAIT_TACKLING_TAILED_DEFENDER)`【甩尾防御：可用尾巴格挡/反击】
  - `mutanttongue = /obj/item/organ/tongue/lizard` — 分叉舌：嘶嘶声（say_mod="hisses"）、s→sss 替换、母语 Draconic（龙语）+ Ashtongue（NOVA）、味觉灵敏度 10（舌鼻合一）
  - `mutanteyes = /obj/item/organ/eyes/lizard` — 爬虫眼：竖瞳、非同步眨眼
  - 视觉器官：horns（角）/ frills（褶）/ snout（吻）/ spines（棘）/ tail/lizard（蜥蜴尾，可 wag）
  - 外星血型 `exotic_bloodtype = BLOOD_TYPE_LIZARD`
- **独有特质**：冷血（不自调体温）、喷火惰性突变、甩尾防御（TRAIT_TACKLING_TAILED_DEFENDER）、蜥蜴血型
- **共享**：`TRAIT_MUTANT_COLORS` 与蛾人（NOVA）/以太人共享

### 2a. 灰行者 Ash Walker（子类 /datum/species/lizard/ashwalker）
- **固有特质**：`TRAIT_MUTANT_COLORS`（突变色）+ `TRAIT_VIRUSIMMUNE`（免疫病毒）
  - ⚠️ **NOVA 差异**：`modular_nova/.../species/lizard.dm` 覆盖为 `list(TRAIT_MUTANT_COLORS)`，病毒免疫被移除（仅保留突变色）
- **器官**：`mutantlungs = /obj/item/organ/lungs/lavaland`（可在拉瓦兰大气呼吸）、`mutantbrain = /obj/item/organ/brain/primitive`（原始脑，通常文盲）、强制趾行（DIGITIGRADE_FORCED）、Ash 语言
- **独有**：拉瓦兰肺、强制趾行

### 2b. 银鳞 Silver Scale（子类 /datum/species/lizard/silverscale）
- **固有特质（7 条）**
  - `TRAIT_HOLY` — 神圣（抵抗邪教魔法）
  - `TRAIT_NOBREATH` — 无需呼吸（免疫缺氧伤害）
  - `TRAIT_PIERCEIMMUNE` — 穿刺免疫（不会被刺穿）
  - `TRAIT_RESISTHIGHPRESSURE` — 耐高压
  - `TRAIT_RESISTLOWPRESSURE` — 耐低压
  - `TRAIT_VIRUSIMMUNE` — 免疫病毒
  - `TRAIT_WINE_TASTER` — 品酒师（酒类技能特质）
- **动态特质**：`damage_modifier = 10`（物理伤害大幅减免，鳞片缓冲）；`on_species_gain`：突变色强制银白 #eeeeee、眼睛蓝色、加"银光"滤镜（on_species_loss 还原）
- **器官**：`mutanttongue = /obj/item/organ/tongue/lizard/silver` — 银舌（雕像化能力）、`mutantlungs = null`（无肺，配合 NOBREATH）
- **独有**：TRAIT_HOLY / TRAIT_PIERCEIMMUNE / TRAIT_WINE_TASTER（本批唯一）、银舌雕像化、10 倍伤害减免

---

### 3. 猫人 Felinid（felinid.dm）

主类：`/datum/species/human/felinid`（id = SPECIES_FELINE，人类子类）

- **固有特质（inherent_traits，4 条）**
  - `TRAIT_CATLIKE_GRACE` — 猫之优雅：坠落时总是脚先着地
  - `TRAIT_HATED_BY_DOGS` — 被狗讨厌：附近的狗有概率骚扰你
  - `TRAIT_USES_SKINTONES` — 使用肤色系统着色
  - `TRAIT_WATER_HATER` — 讨厌水：被淋浴/喷水会不爽（负面效果）
- **动态特质**
  - `on_species_gain`：无 ADD_TRAIT；但被强制"purrbation"（喵化，非 pref_load 时）会自动补猫尾/猫耳；若无可视猫耳则退回普通人类耳朵
  - 狂暴咬人机制（大脑驱动，见下）
- **器官隐含特质（mutant organs）**
  - `mutantbrain = /obj/item/organ/brain/felinid` — 猫脑（0.8 倍大小）：饥饿（营养≤饥饿线）+ 低理智时概率触发"野性咬人"
  - `mutanttongue = /obj/item/organ/tongue/cat` — 猫舌：`organ_traits = list(TRAIT_WOUND_LICKER【舔舐伤口自愈】, TRAIT_FISH_EATER【爱吃鱼】, TRAIT_CARPOTOXIN_IMMUNE【河豚毒素免疫】)`；咬人强化（头部无武装伤害 +4~7、咬击、利齿 SHARP_EDGED）；可开启狂暴模式获得 `TRAIT_FERAL_BITER`【野性撕咬】（go_feral 器官技能）；母语 Nekomimetic（猫语）、say_mod="meows"
  - `mutanteyes = /obj/item/organ/eyes/felinid` — 猫眼（竖瞳、反光）
  - `mutantears = /obj/item/organ/ears`（⚠️ NOVA 修改：原版为猫耳 /obj/item/organ/ears/cat，NOVA 改回普通耳朵，猫耳走视觉器官系统）
  - `mutant_organs = /obj/item/organ/tail/cat` — 猫尾（可 wag）
- **独有特质**：TRAIT_CATLIKE_GRACE、TRAIT_HATED_BY_DOGS、TRAIT_WATER_HATER、TRAIT_WOUND_LICKER / TRAIT_FISH_EATER / TRAIT_CARPOTOXIN_IMMUNE / TRAIT_FERAL_BITER（本批唯一，均来自猫舌/猫脑）
- **共享**：`TRAIT_USES_SKINTONES`（与人类共享）

---

### 4. 蝇人 Flyperson（flypeople.dm）

主类：`/datum/species/fly`（id = SPECIES_FLYPERSON）

- **固有特质（inherent_traits）**：**无**（继承基类空列表）
  - ⚠️ NOVA 差异：`allows_food_preferences() = FALSE`（禁用食物偏好选择）
- **动态特质**：无 ADD_TRAIT；`inherent_biotypes = MOB_ORGANIC|MOB_HUMANOID|MOB_BUG`（昆虫类，病毒/疾病按昆虫判定）
- **器官隐含特质（mutant organs，全部换装）**
  - `mutanteyes = /obj/item/organ/eyes/fly` — 复眼：`flash_protect = FLASH_PROTECTION_HYPER_SENSITIVE`（对闪光极度敏感，负面）、`native_fov = NONE`（360° 无视野盲区）、不眨眼
  - `mutanttongue = /obj/item/organ/tongue/fly` — 口器（proboscis）：say_mod="buzzes"（嗡嗡声）、s→z 替换、母语 Buzzwords（嗡嗡语）、味觉灵敏度 25（迟钝）、`liked_foodtypes = GROSS|GORE`（爱吃呕吐物/内脏）、**无任何有毒食物**（可喝漂白剂等）
  - `mutantheart` / `mutantlungs` / `mutantliver` / `mutantstomach` / `mutantappendix` — 蝇化内脏（外观随机、名称随机）；蝇肝 `alcohol_tolerance = 0.007`（酒精耐受极低）；蝇胃呕吐不损失营养
  - `mutant_organs = list(/obj/item/organ/fly, /obj/item/organ/fly/groin)` — 两个无用"奇怪器官"（仅为增加手术难度）
  - 全部蝇器官带 `organ_set_bonus/fly`：**植入 4 个蝇器官即强制转变为蝇人**（flyify）
- **独有特质**：无 TRAIT 列表，全部能力来自器官：复眼全视野+闪光敏感、吃垃圾/呕吐物、嗡嗡语、极低酒精耐受、4 器官转种机制
- **共享**：无共享 TRAIT

---

### 5. 蛾人 Mothman（mothmen.dm）

主类：`/datum/species/moth`（id = SPECIES_MOTH）

- **固有特质（inherent_traits）**
  - 主代码：**无**
  - ⚠️ **NOVA 差异**：`modular_nova/.../species/moth.dm` 覆盖添加 `TRAIT_MUTANT_COLORS`（使用突变色系统着色）
- **动态特质**：无 ADD_TRAIT；`inherent_biotypes = MOB_ORGANIC|MOB_HUMANOID|MOB_BUG`（昆虫类）
- **器官隐含特质（mutant organs）**
  - `mutanttongue = /obj/item/organ/tongue/moth` — 蛾舌：say_mod="flutters"（振翅声）、母语 Moffic（蛾语）、`liked_foodtypes = VEGETABLES|DAIRY|CLOTH`（爱吃蔬菜/奶制品/布料）、`toxic_foodtypes = MEAT|RAW|SEAFOOD`（**肉/生食/海鲜对其有毒**）
  - `mutanteyes = /obj/item/organ/eyes/moth` — 蛾眼：`flash_protect = FLASH_PROTECTION_SENSITIVE`（光敏感，负面）、不眨眼、复眼（ommatidia）
  - 翅膀/触角：主代码 mutant_organs 被 NOVA 移除，改由 customization 系统提供（`FEATURE_WINGS = "Moth (Plain)"`、`FEATURE_MOTH_ANTENNAE = "Plain"`、FEATURE_FLUFF 绒毛、FEATURE_EARS）
  - `/obj/item/organ/wings/moth` — 蛾翅：带 jetpack 组件（**无重力环境可飞行/推进**，有重力不可用）、`can_soften_fall()` 缓冲坠落、**可燃**（着火时翅膀烧毁 burnt，烧毁后失去飞行与坠缓冲，全身治愈或医疗可恢复）
- **独有特质**：无重力飞行（蛾翅 jetpack）、蛾翅可燃机制、肉食中毒（蛾舌）、光敏感蛾眼
- **共享**：`TRAIT_MUTANT_COLORS`（NOVA，与蜥蜴/以太共享）

---

### 6. 等离子人 Plasmaman（plasmamen.dm）

主类：`/datum/species/plasmaman`（id = SPECIES_PLASMAMAN）

- **固有特质（inherent_traits，8 条）**
  - `TRAIT_GENELESS` — 无基因（不受基因改造/基因操作影响）
  - `TRAIT_HARDLY_WOUNDED` — 难以受伤（需严重伤口才触发肢体伤，对应断肢只需重度骨折）
  - `TRAIT_NOBLOOD` — 无血（不流血、不受失血影响）
  - `TRAIT_NO_DNA_COPY` — DNA 不可复制（疾病/变形者无法复制其 DNA）
  - `TRAIT_NO_PLASMA_TRANSFORM` — 不会被等离子河变成骷髅
  - `TRAIT_RADIMMUNE` — 辐射免疫
  - `TRAIT_RESISTCOLD` — 耐寒
  - `TRAIT_UNHUSKABLE` — 任何情况下都不会变成干尸（husk）
- **动态特质**
  - `sexes = FALSE`（无性别）；`inherent_biotypes = MOB_HUMANOID|MOB_MINERAL|MOB_SKELETAL`（矿物+骷髅类）
  - 体温特化：`bodytemp_normal = 体温-40`（正常体温更低）、`bodytemp_autorecovery_min = 2`（自动恢复慢，怕热）、`bodytemp_heat_damage_limit = 约40°C`（高温伤害阈值低）、`bodytemp_cold_damage_limit = 约-50°C`（更耐冷）、`heatmod = 1.5`（炎热伤害倍率提升）
  - `outfit_important_for_life = /datum/outfit/plasmaman`（必须穿防爆服/供等离子装备才能存活）；职业/反派 outfit 自动替换为等离子版（outfit_override_registry）
- **器官隐含特质（mutant organs）**
  - `mutantlungs = /obj/item/organ/lungs/plasmaman` — 等离子肺：呼吸等离子、缺氧即损；接触氧气环境会自燃（等离子体身体遇氧燃烧）
  - `mutanttongue = /obj/item/organ/tongue/bone/plasmaman`、`mutantliver = /obj/item/organ/liver/bone/plasmaman`、`mutantstomach = /obj/item/organ/stomach/bone/plasmaman` — 骨质内脏
  - `mutantappendix = null`、`mutantheart = null` — 无心脏、无阑尾
  - 语言：Skeleton（骷髅语）
- **独有特质**：TRAIT_NO_PLASMA_TRANSFORM（本批唯一）、TRAIT_UNHUSKABLE + TRAIT_HARDLY_WOUNDED + TRAIT_GENELESS + TRAIT_NO_DNA_COPY 组合（骨架类共享定义，本批仅此物种）；无心脏/阑尾、等离子肺自燃机制
- **共享**：TRAIT_RADIMMUNE / TRAIT_RESISTCOLD / TRAIT_NOBLOOD / TRAIT_NOBREATH 系与骨架类（skeletons.dm，不在本批）共享

---

### 7. 以太人 Ethereal（ethereal.dm）

主类：`/datum/species/ethereal`（id = SPECIES_ETHEREAL）

- **固有特质（inherent_traits，3 条）**
  - `TRAIT_MUTANT_COLORS` — 使用突变色系统着色
  - `TRAIT_FIXED_MUTANT_COLORS` — 固定突变色（玩家不可选、游戏中不可被外部手段更改）
  - `TRAIT_AGENDER` — 无性别（sexes = FALSE）
- **动态特质**
  - `siemens_coeff = 0.5` — 电击伤害减半（以电为食）
  - `on_species_gain` 注册信号：EMAG 击中（2 分钟迪斯科变色模式）、EMP 击中（10/20 秒熄灯 disrupted）、光食者攻击（触发 EMP 效果）、健康更新（发光颜色/亮度随生命值变化）
  - 发光系统：`ethereal_light` 本体光源（亮度/范围/颜色随生命值 0~100% 渐变），死亡时变灰熄灭
  - 体温特化：`bodytemp_normal = 体温+50`（体温更高）、`bodytemp_heat_damage_limit = 约150°C`（耐热）、`bodytemp_cold_damage_limit = 约10°C`（怕冷）
  - 头发/胡须 = 固定突变色（USE_FIXED_MUTANT_COLOR），半透明（alpha 140）
- **器官隐含特质（mutant organs）**
  - `mutantheart = /obj/item/organ/heart/ethereal` — 水晶核心（以太心脏）：**死亡后自动结晶复活**（晶体化流程：死亡 → 锁定尸体 TRAIT_CORPSELOCKED → 生成以太水晶 → 复活出晶）；被推搡可中断计时、冷却无限（防止连续复活）、`TRAIT_CANNOT_CRYSTALIZE` 可阻止（如黑手党模式）
  - `mutantlungs = /obj/item/organ/lungs/ethereal` — 以太肺（呼吸机制特殊）、`mutantstomach = /obj/item/organ/stomach/ethereal`、`mutanttongue = /obj/item/organ/tongue/ethereal` — 以太舌
  - `exotic_bloodtype = BLOOD_TYPE_ETHEREAL`
- **独有特质**：TRAIT_FIXED_MUTANT_COLORS + TRAIT_AGENDER 组合（本批唯一）、水晶核心死亡复活、发光/EMP/EMAG 交互系统、电击减半
- **共享**：`TRAIT_MUTANT_COLORS`（与蜥蜴/蛾共享）

### 7a. 光辉者 Lustrous（子类 /datum/species/ethereal/lustrous）
- **固有特质（7 条）**：基类 3 条 + 追加
  - `TRAIT_NOBREATH` — 无需呼吸
  - `TRAIT_RESISTHIGHPRESSURE` — 耐高压
  - `TRAIT_RESISTLOWPRESSURE` — 耐低压
  - `TRAIT_VIRUSIMMUNE` — 免疫病毒
- **器官**：`mutantbrain = /obj/item/organ/brain/lustrous`（可看到蓝移空间裂缝/创伤）、`on_species_gain` 强制从 5 种光辉色中随机着色
- **独有**：蓝移空间视觉（光辉脑）

---

## 汇总表

| 物种 | 固有特质数 | 动态/器官特质要点 | 独有特质亮点 |
|---|---|---|---|
| 人类 | 1 | 无 | —（肤色与猫人共享） |
| 蜥蜴人 | 1 | 喷火惰性突变、冷血、甩尾防御、耐热怕冷 | 冷血机制、TRAIT_TACKLING_TAILED_DEFENDER |
| └灰行者 | 2（NOVA:1） | 拉瓦兰肺、原始脑、强制趾行 | 病毒免疫（NOVA 移除） |
| └银鳞 | 7 | 10 倍物理减伤、银白化+滤镜 | TRAIT_HOLY/PIERCEIMMUNE/WINE_TASTER、银舌雕像化 |
| 猫人 | 4 | 猫舌自愈/鱼食/河豚免疫、野性咬人、狂暴撕咬 | TRAIT_CATLIKE_GRACE/HATED_BY_DOGS/WATER_HATER、WOUND_LICKER |
| 蝇人 | 0 | 全器官换装：复眼全视野、口器食秽、嗡嗡语、4 器官转种 | 吃 GROSS/GORE、呕吐不掉营养、超低酒精耐受 |
| 蛾人 | 0（NOVA:1） | 蛾翅无重力飞行、翅可燃、肉食中毒、光敏感 | 蛾翅 jetpack、toxic 肉类 |
| 等离子人 | 8 | 等离子肺自燃、无心脏/阑尾、骨质内脏、防爆服生存 | NO_PLASMA_TRANSFORM、UNHUSKABLE、HARDLY_WOUNDED、GENELESS |
| 以太人 | 3 | 水晶核心死亡复活、发光系统、电击半减、怕冷耐热 | FIXED_MUTANT_COLORS、AGENDER、死亡结晶复活 |
| └光辉者 | 7 | 蓝移空间视觉、强制光辉色 | TRAIT_NOBREATH 等 4 条追加、光辉脑 |

### 1. 蜗牛 Snailperson（SPECIES_SNAIL）

- 文件：`species_types/snail.dm`（含 NOVA 改造：开局可选蜗牛，允许穿内衣）
- 简介：黏滑带壳生物，爬行时分泌粘液，比多数物种爬行略快；几乎纯水构成，对电击极度敏感，盐会重创它们。
- 设定：遗传实验失败的产物，现已如 Felinid 一样成为正式种族。

### 1.1 inherent_traits（先天特质，共 3 条）

| 特质代码 | 中文名 | 含义/效果 | 共享/独有 |
|---|---|---|---|
| TRAIT_MUTANT_COLORS | 突变体颜色 | 使用突变颜色（mutant_color）作为身体着色方式，颜色可自定义 | 共享（果冻、史莱姆同有） |
| TRAIT_NO_SLIP_ALL | 全防滑 | 免疫一切滑倒与滑行（官方：Stops all slipping and sliding from occurring） | **独有** |
| TRAIT_WATER_BREATHING | 水下呼吸 | 可在水下呼吸（NOVA 特质，官方注释："One can breath under water"；NOVA 开局蜗牛改造新增——海蜗牛存在，陆蜗牛也能在水下坚持近一天） | **独有** |

> 注：`TRAIT_NO_UNDERWEAR` 被 NOVA 移除（蜗牛值得穿内衣），不生效。

### 1.2 动态特质 / on_species_gain 行为

| 项目 | 内容 | 说明 |
|---|---|---|
| 强制装备蜗牛壳 | `on_species_gain` 检查背部槽，非蜗牛壳则强制装备 `/obj/item/storage/backpack/snail` | 壳 = 护甲 + 储物箱 |
| TRAIT_NODROP（物品级） | 壳在 `Initialize` 时 `ADD_TRAIT(src, TRAIT_NODROP, INNATE_TRAIT)` | 不可主动丢弃；`dropped()` 时清空并自毁 |
| on_species_loss | 移除并删除蜗牛壳（先清空储物） | 变回其他物种时壳消失 |

蜗牛壳数据（`/obj/item/storage/backpack/snail`）：护甲 melee 40 / bullet 30 / laser 30 / energy 10 / bomb 25 / acid 50，耐久 200，`FIRE_PROOF|ACID_PROOF`，带 `TRAIT_NODROP`。

### 1.3 其他关键生理参数

- `coldmod = 0.5`：寒冷伤害减半（蜗牛只在冷湿天气出门）
- `siemens_coeff = 2`：电击伤害 ×2（身体大部分是水）
- `sexes = FALSE`：雌雄同体，无性别
- `exotic_bloodtype = BLOOD_TYPE_SNAIL`：蜗牛血
- 突变器官：蜗牛眼/蜗牛舌/蜗牛肝（`mutanteyes/tongue/liver`）
- 全身蜗牛肢体覆盖（`bodypart_overrides`，头/胸/双臂/双腿）
- `changesource_flags = MIRROR_BADMIN | WABBAJACK | MIRROR_MAGIC | MIRROR_PRIDE | RACE_SWAP`
- 职业预设加成（perks）：爬行更快（中性）、蜗牛相关（中性）、盐伤害（负面）、电击易感（负面）

---

### 2. 果冻 Jellyperson（SPECIES_JELLYPERSON）

- 文件：`species_types/jellypeople.dm`（`/datum/species/jelly`）
- 简介：完全由凝胶构成的外星生物，三只眼，体内可见骨骼。
- 设定：Nanotrasen 科学家正对其独特生物构造进行活体实验。

### 2.1 inherent_traits（先天特质，共 2 条）

| 特质代码 | 中文名 | 含义/效果 | 共享/独有 |
|---|---|---|---|
| TRAIT_MUTANT_COLORS | 突变体颜色 | 使用突变颜色着色（发色/胡子色同步为突变色，头发透明度 150） | 共享（蜗牛、史莱姆同有） |
| TRAIT_TOXINLOVER | 毒素爱好者 | 毒素对它们起治疗作用而非伤害（大多数有毒物可治疗它们；反之阻止毒素的物质会伤害它们） | 共享（史莱姆继承） |

### 2.2 动态特质 / on_species_gain 行为

| 项目 | 内容 | 说明 |
|---|---|---|
| 再生肢体动作 | 授予 `/datum/action/innate/regenerate_limbs` | 消耗血液（果冻）再生缺失肢体，每肢 40 血；血量足够可分批再生 |
| 软着陆元素 | `AddElement(/datum/element/soft_landing)` | 高处坠落不受伤害（等价于特质效果） |
| 史莱姆血液处理 | 注册 `COMSIG_HUMAN_ON_HANDLE_BLOOD → slime_blood` | 血液即果冻：血量为 0 时以 2.5/秒再生但持续受钝击伤；低于正常血量且营养足够时以 1.5/秒再生并消耗营养；低于警戒血量时概率提示；低于危险血量时**吞噬自身肢体**（先腿后臂）换血自救，无肢可吞则窒息加重 |
| on_species_loss | 移除动作、软着陆元素、注销信号 | 变回其他物种时全部清除 |

血液/果冻相关常数：`JELLY_REGEN_RATE = 1.5`、`JELLY_REGEN_RATE_EMPTY = 2.5`、`BLOOD_VOLUME_LOSE_NUTRITION = 550`。

### 2.3 其他关键生理参数

- `inherent_biotypes = MOB_ORGANIC|MOB_HUMANOID|MOB_SLIME`：有机/人形/史莱姆
- `inherent_factions = list(FACTION_SLIME)`：史莱姆阵营
- `coldmod = 6`：寒冷伤害 ×3；`heatmod = 0.5`：热伤害 ×1/4
- `exotic_bloodtype = BLOOD_TYPE_TOX`：毒血
- `mutantheart = null`：**无心脏**
- 突变器官：果冻舌/史莱姆肺/果冻眼；肉 = 史莱姆肉排
- 全身果冻肢体覆盖
- `species_language_holder = /datum/language_holder/jelly`
- `changesource_flags`：多含 `SLIME_EXTRACT`（史莱姆提取物可转化）
- 血型 perk：毒血（中性，说明可静脉注射/输血的特殊性质）

---

### 3. 史莱姆 Slimeperson（SPECIES_SLIMEPERSON）

- 文件：`species_types/jellypeople.dm`（`/datum/species/jelly/slime`，果冻子类）
- 简介：像史莱姆一样可以分裂，保留单一意识，可随时在身体间切换，甚至死后转移。
- 特性概述（官方）：血液是果冻，液泡可在吸入等离子体时极快将其转化为血液；用多余血液分裂出备用身体，意识可自由转移或死后转移；有毒物治疗、防毒物伤害。

### 3.1 inherent_traits（先天特质，共 2 条，全部继承自果冻）

| 特质代码 | 中文名 | 含义/效果 | 共享/独有 |
|---|---|---|---|
| TRAIT_MUTANT_COLORS | 突变体颜色 | 同果冻（分裂出的分身会随机变浅色系突变色） | 共享 |
| TRAIT_TOXINLOVER | 毒素爱好者 | 同果冻 | 共享 |

### 3.2 动态特质 / on_species_gain 行为

| 项目 | 内容 | 说明 |
|---|---|---|
| 分裂身体动作 | 授予 `/datum/action/innate/split_body`（Split Body） | 血量 ≥ BLOOD_VOLUME_SLIME_SPLIT 时可用；6 秒施法后复制 DNA/物种/突变，生成分身（随机浅色调），本体血量 ×0.45，意识转移至分身 |
| 交换身体动作 | 授予 `/datum/action/innate/swap_body`（Swap Body） | TGUI 界面列出所有身体（含状态/位置/血量），可把意识切换到任意空闲且清醒的分身 |
| 死亡转移 | 注册 `COMSIG_LIVING_DEATH → on_death_move_body` | 死亡时若存在可交换的空闲分身，意识自动转移过去（死后换体） |
| 身体池 | `bodies` 列表维护所有分身；`copy_properties_from` 让克隆体继承身体池 | 克隆后找回全部身体 |
| 分裂阈值提示 | `on_life`：血量 ≥ 分裂阈值时概率提示可分裂；血量不足但营养充足时缓慢回血 | 营养 ≥ 饱食时 1.5/秒回血 |
| on_species_loss | 移除两动作、断开身体池、血量上限压回正常、注销信号 | 变回其他物种时清理 |
| 继承果冻动态能力 | 再生肢体、软着陆、史莱姆血液处理（同 §2.2） | 全部生效 |

### 3.3 其他关键生理参数

- `mutanteyes = /obj/item/organ/eyes`：普通眼睛（三眼特征消失）
- 史莱姆专用肢体覆盖（jelly/slime 系列）
- 其余参数继承果冻（毒血、无心脏、史莱姆阵营、coldmod 6 / heatmod 0.5 等）

---

### 4. 外星 Abductor（SPECIES_ABDUCTOR）

- 文件：`species_types/abductors.dm`
- 简介：俗称"小灰人"，三指、苍白皮肤、好奇心强的外星种族，与普通船员沟通困难。
- 特性概述（官方）：不需要呼吸、进食，没有血液、心脏、胃和肺，不会被人类病毒感染；强健体格使皮肤不会被划伤或断肢，但粗壮的三指手难以操作人类设备。

### 4.1 inherent_traits（先天特质，共 9 条）

| 特质代码 | 中文名 | 含义/效果 | 共享/独有 |
|---|---|---|---|
| TRAIT_ABDUCTOR_HUD | 外星人 HUD | 获得 DATA_HUD_ABDUCTOR（GLAND_HUD）：显示被植入外星腺体的实验目标位置（配合外星人任务追踪） | **独有** |
| TRAIT_CHUNKYFINGERS_IGNORE_BATON | 粗手指（警棍豁免） | 手指粗壮，无法使用带普通扳机护圈的武器（chunky fingers），但警棍不受此限制仍可使用 | **独有** |
| TRAIT_NEVER_WOUNDED | 永不受伤 | 肢体伤口系统完全免疫，永远不会产生伤口 | 共享（魔像同有） |
| TRAIT_NOBLOOD | 无血液 | 体内没有血液（不会流血、无血型） | 共享（安卓、魔像同有） |
| TRAIT_NOBREATH | 无需呼吸 | 免疫缺氧伤害，不需要呼吸 | 共享（安卓、无头、魔像同有） |
| TRAIT_NODISMEMBER | 断肢免疫 | 肢体不会被切断/扯下 | 共享（魔像同有） |
| TRAIT_NOHUNGER | 无饥饿 | 不会饥饿，无需进食 | 共享（安卓、无头同有） |
| TRAIT_NO_UNDERWEAR | 无内衣 | 不能穿戴内衣 | 共享（安卓、魔像同有） |
| TRAIT_VIRUSIMMUNE | 病毒免疫 | 不会被病毒感染（官方：Can't be given viruses） | **独有** |

### 4.2 动态特质 / on_species_gain 行为

- **无**（该物种未覆盖 `on_species_gain`，不授予任何动作/元素/信号）。

### 4.3 其他关键生理参数

- `sexes = FALSE`：无性别
- 突变器官：外星舌；`mutantstomach = null`、`mutantheart = null`、`mutantlungs = null`（无胃/心/肺）；`mutantbrain = /obj/item/organ/brain/abductor`（外星大脑）
- 全身外星肢体覆盖
- `changesource_flags = MIRROR_BADMIN | WABBAJACK | MIRROR_PRIDE | MIRROR_MAGIC | RACE_SWAP | ERT_SPAWN | SLIME_EXTRACT`
- 职业预设加成：无需呼吸（正面）、皮肤坚韧（正面）、腺体植入专精（正面）、无饥饿（中性）、无血液/血型（中性）、三指手难用人类设备（负面）

---

### 5. 安卓 Android（SPECIES_ANDROID）

- 文件：`species_types/android.dm`
- 简介：完全合成的物种，Nanotrasen 制造，作为人类与 cyborg 之间的中间形态。
- 特性概述（官方）：与全身义体化人类几乎一样，但完全免疫毒素伤害，没有血液和器官（除头部外），不会饥饿，可以自行装回肢体；EMP 会重创它们，且无法代谢任何化学物质。

### 5.1 inherent_traits（先天特质，共 19 条）

| 特质代码 | 中文名 | 含义/效果 | 共享/独有 |
|---|---|---|---|
| TRAIT_GENELESS | 无基因 | 没有基因（不受基因操作/遗传学影响） | 共享（魔像同有） |
| TRAIT_LIMBATTACHMENT | 肢体直接附着 | 断肢可直接装回身上，无需手术（官方：limb_attach） | **独有** |
| TRAIT_LIVERLESS_METABOLISM | 无肝代谢 | 始终以无肝方式代谢：化学物质不经肝脏处理（几乎无法正常代谢药物/毒素） | **独有** |
| TRAIT_NOBLOOD | 无血液 | 体内没有血液（不会流血） | 共享（外星、魔像同有） |
| TRAIT_NOBREATH | 无需呼吸 | 免疫缺氧伤害 | 共享（外星、无头、魔像同有） |
| TRAIT_NOCRITDAMAGE | 无濒死伤害 | 不会因濒死（crit）状态持续掉血死亡 | 共享（魔像同有） |
| TRAIT_NOFIRE | 不可燃 | 免疫火焰/燃烧伤害（官方：nonflammable） | 共享（魔像同有） |
| TRAIT_NOHUNGER | 无饥饿 | 不会饥饿 | 共享（外星、无头同有） |
| TRAIT_NO_DNA_COPY | 禁止 DNA 复制 | 疾病与变形者（changeling）无法复制其 DNA | 共享（魔像同有） |
| TRAIT_NO_PLASMA_TRANSFORM | 抗等离子转化 | 等离子体河流不会把它变成骷髅（官方：Cannot be turned into a funny skeleton by the plasma river） | 共享（魔像同有） |
| TRAIT_NO_UNDERWEAR | 无内衣 | 不能穿戴内衣 | 共享（外星、魔像同有） |
| TRAIT_OVERDOSEIMMUNE | 过量免疫 | 化学药物永远不会过量（overdose） | **独有** |
| TRAIT_PIERCEIMMUNE | 穿刺免疫 | 免疫穿刺类伤害 | 共享（魔像同有） |
| TRAIT_RADIMMUNE | 辐射免疫 | 免疫被辐射（官方：Immune to being irradiated） | 共享（魔像同有） |
| TRAIT_RESISTCOLD | 耐寒 | 抵抗寒冷环境伤害 | **独有** |
| TRAIT_RESISTHEAT | 耐热 | 抵抗高温环境伤害 | **独有** |
| TRAIT_RESISTHIGHPRESSURE | 耐高压 | 抵抗高压环境伤害 | **独有** |
| TRAIT_RESISTLOWPRESSURE | 耐低压 | 抵抗低压（真空）环境伤害 | **独有** |
| TRAIT_TOXIMMUNE | 毒素免疫 | 完全免疫毒素伤害（官方：Mob is immune to toxin damage） | **独有** |

### 5.2 动态特质 / on_species_gain 行为

- **无**（该物种未覆盖 `on_species_gain`）。

### 5.3 其他关键生理参数

- `inherent_biotypes = MOB_ROBOTIC|MOB_HUMANOID`：机械/人形
- `exotic_bloodtype = BLOOD_TYPE_OIL`：**机油**血
- `meat = null`：无肉
- 突变器官：赛博脑（cybernetic）/ 机器人舌 / 机械眼 / 赛博耳；**无胃、无阑尾、无心、无肝、无肺**
- 全身机器人肢体覆盖（robot/android 系列）
- `species_language_holder = /datum/language_holder/synthetic`：合成语言
- `changesource_flags`：含 `ERT_SPAWN | SLIME_EXTRACT`
- 职业预设加成：合成躯体坚韧（正面）、可自装肢体（正面）、无法处理化学物质（负面）、无器官需求（中性）、EMP 弱点（负面）

---

### 6. 无头 Dullahan（SPECIES_DULLAHAN）

- 文件：`species_types/dullahan.dm`
- 简介：愤怒的灵魂因未竟之事滞留人间；头部与身体分离，必须随身携带。
- 特性概述（官方）：与人类非常相似，但头部脱离身体，需提在手上（可切换"从头部看"或"从身体盲看"两种视角）。

### 6.1 inherent_traits（先天特质，共 7 条）

| 特质代码 | 中文名 | 含义/效果 | 共享/独有 |
|---|---|---|---|
| TRAIT_NOBREATH | 无需呼吸 | 免疫缺氧伤害 | 共享（外星、安卓、魔像同有） |
| TRAIT_NOHUNGER | 无饥饿 | 不会饥饿 | 共享（外星、安卓同有） |
| TRAIT_USES_SKINTONES | 使用肤色 | 用肤色（skin tone）而非突变色着色 | **独有** |
| TRAIT_ADVANCEDTOOLUSER | 高级工具使用者 | 可操作机器与复杂物品（官方：dexterous enough to use machines and certain items；正常情况下由大脑提供，但无头没有大脑所以显式声明） | **独有** |
| TRAIT_LITERATE | 识字 | 可以读写（官方：Whitelist for mobs that can read or write） | **独有** |
| TRAIT_CAN_STRIP | 可扒装备 | 可以扒下其他 mob 的装备（官方：This mob can strip other mobs） | **独有** |
| TRAIT_BRAINLESS_CARBON | 无脑碳基 | 复活不需要大脑（官方：doesn't need a brain to be revived；除颤等部分复活手段仍会检查功能性大脑） | **独有** |

### 6.2 动态特质 / on_species_gain 行为

| 项目 | 内容 | 说明 |
|---|---|---|
| 解除听力敏感 | `human.lose_hearing_sensitivity(TRAIT_GENERIC)` | 获得物种时移除听力敏感特质 |
| 头部中继 | 注册信号；创建 `/obj/item/dullahan_relay` 并让头部脱落到手上 | 说话/听觉/视野均经由头部中继（`relay` 初始化时 `become_hearing_sensitive(ROUNDSTART_TRAIT)`，让中继能听到声音） |
| 非法头部检查 | 注册 `COMSIG_CARBON_ATTACH_LIMB` | 若有人把头部装回无头肩膀 → 直接 gib（碎尸） |
| 头部损毁即死 | 注册 `COMSIG_QDELETING`（头部）/ `COMSIG_MOVABLE_MOVED`（中继） | 头部被毁或中继被移动（头被冲进垃圾道等）→ 本体 gib |
| 除颤检查 | 注册 `COMSIG_CARBON_DEFIB_BRAIN_CHECK` | 允许用头部内的脑进行除颤复活判定 |
| 视野切换 | 眼睛器官带 tint，登录/切换时重置视角到头部 | 提供"切换视角"器官动作（头部视角 ↔ 身体盲视角） |
| on_species_loss | 清理中继与头部、注销信号、`become_hearing_sensitive()` 恢复听力敏感、重生长回头部 | 变回其他物种时完整还原 |

### 6.3 其他关键生理参数

- `inherent_biotypes = MOB_UNDEAD|MOB_HUMANOID`：亡灵/人形（"次级亡灵"没有专门 biotype，perk 里单独说明）
- 突变器官：假脑（非致命、可欺骗探查）、胸部舌头（不可摘除）、胸部耳朵（不可摘除、不腐烂）、胸部眼睛（"头部视野"抽象、不可摘除、tint 控制视角）
- `mutantstomach = null`、`mutantlungs = null`：无胃无肺
- `skinned_type = /obj/item/stack/sheet/animalhide/carbon/human`：可剥皮
- `check_roundstart_eligible()`：**仅万圣节**可作为开局种族
- `changesource_flags = MIRROR_BADMIN | WABBAJACK | ERT_SPAWN`（无 RACE_SWAP/魔法镜像）
- 职业预设加成：头部需随身携带（负面）、亡灵生物类型（正面）

---

### 7. 魔像 Golem（SPECIES_GOLEM）

- 文件：`species_types/golems.dm`
- 简介：岩石活化生物，防御强，无需呼吸，靠吃矿物生存并获得额外增益（吃不同材料获得不同能力）。
- 特性概述（官方）：石制躯干天然抵抗多种危险（窒息、火焰、辐射、电流、病毒）；必须持续进食材料维持身体活化，营养即生命值。

### 7.1 inherent_traits（先天特质，共 18 条）

| 特质代码 | 中文名 | 含义/效果 | 共享/独有 |
|---|---|---|---|
| TRAIT_GENELESS | 无基因 | 没有基因 | 共享（安卓同有） |
| TRAIT_LAVA_IMMUNE | 熔岩免疫 | 熔岩地面/"地板是熔岩"效果免疫（官方：Used by lava turfs and The Floor Is Lava） | **独有** |
| TRAIT_NEVER_WOUNDED | 永不受伤 | 肢体伤口系统完全免疫 | 共享（外星同有） |
| TRAIT_NOBLOOD | 无血液 | 体内没有血液 | 共享（外星、安卓同有） |
| TRAIT_NOBREATH | 无需呼吸 | 免疫缺氧伤害 | 共享（外星、安卓、无头同有） |
| TRAIT_NOCRITDAMAGE | 无濒死伤害 | 不会因濒死持续掉血（魔像的"濒死"由营养机制替代，见 §7.2） | 共享（安卓同有） |
| TRAIT_NODISMEMBER | 断肢免疫 | 肢体不会被切断 | 共享（外星同有） |
| TRAIT_NOFAT | 不会发胖 | 永远不会超重（官方：prevents you from becoming overweight） | **独有** |
| TRAIT_NOFIRE | 不可燃 | 免疫火焰/燃烧伤害 | 共享（安卓同有） |
| TRAIT_NOSOFTCRIT | 无软濒死 | 不会进入软濒死（soft crit）状态 | **独有** |
| TRAIT_NO_AUGMENTS | 禁义体改造 | 不能接受义体改造手术（官方：cannot get augmentation surgery） | **独有** |
| TRAIT_NO_DNA_COPY | 禁止 DNA 复制 | 疾病/变形者无法复制其 DNA | 共享（安卓同有） |
| TRAIT_NO_PLASMA_TRANSFORM | 抗等离子转化 | 等离子体河流不会将其变为骷髅 | 共享（安卓同有） |
| TRAIT_NO_UNDERWEAR | 无内衣 | 不能穿戴内衣 | 共享（外星、安卓同有） |
| TRAIT_PIERCEIMMUNE | 穿刺免疫 | 免疫穿刺类伤害 | 共享（安卓同有） |
| TRAIT_RADIMMUNE | 辐射免疫 | 免疫被辐射 | 共享（安卓同有） |
| TRAIT_SNOWSTORM_IMMUNE | 暴风雪免疫 | 暴风雪天气效果免疫（能扛等离子河流，暴风雪自然不在话下） | **独有** |
| TRAIT_UNHUSKABLE | 不可干尸化 | 任何情况下不会变成 husk（官方：Won't become a husk under any circumstances） | **独有** |

### 7.2 动态特质 / on_species_gain 行为

| 项目 | 内容 | 说明 |
|---|---|---|
| 营养即生命信号链 | 注册 6 个信号：`AFTER_APPLY_DAMAGE → drain_nutrition`、`LIVING_UPDATE_NUTRITION → check_nutrition`、`CARBON_DEFIB_HEART_CHECK → defib_check`、`ATOM_ITEM_INTERACTION → rebuild_check`、`ATOM_EXAMINE → on_examine`、`LIVING_LIFE → on_life` | 营养 = 生命值：受伤（钝击/灼烧）按 3×伤害量扣营养；营养 ≤20 强制昏迷（≈硬濒死）；营养 ≤100 或生命低于濒死阈值时持续流失营养（双重消耗）；营养 > 肥胖阈值时压回上限 |
| 饥饿警告 | `check_nutrition`：营养 < 饥饿阈值发早期警告；< 50 发最终警告；< 2 直接死亡 | 各有 1 分钟冷却防刷屏 |
| 不可除颤 | `defib_check` 返回 DEFIB_FAIL_GOLEM | 没有心脏、非传统生物，无法除颤复活 |
| 材料重建 | `rebuild_check/rebuild`：死亡后可用矿石堆（`GLOB.golem_stack_food_directory` 内的材料堆）重建 | 每次消耗 1 张材料，按材料强度治疗钝/灼伤并回营养；完全修好后复活 |
| 初始营养 | 获得物种时 `set_nutrition(NUTRITION_LEVEL_WELL_FED)` | 给个开局缓冲 |
| 生理修正 | `stamina_mod *= 0.6`、`stun_mod *= 0.6`、`knockdown_mod *= 1.2` | 耐力消耗/眩晕减免 40%，但被击倒概率 +20%（on_species_loss 时还原） |
| on_species_loss | 注销全部信号、还原生理修正 | 变回其他物种时清理 |

### 7.3 其他关键生理参数

- `inherent_biotypes = MOB_HUMANOID|MOB_MINERAL`：人形/矿物
- `siemens_coeff = 0`：**完全免疫电击**
- `no_equip_flags`：不能装备面罩/外衣/手套/鞋/内衣物/制服存储位（配合 `TRAIT_NO_JUMPSUIT` 体系）
- `sexes = FALSE`：无性别
- `meat = /obj/item/food/meat/slab/human/mutant/golem`：魔像肉
- 温度极限：`bodytemp_heat_damage_limit = BODYTEMP_HEAT_LAVALAND_SAFE`（耐到熔岩地）、`bodytemp_cold_damage_limit = BODYTEMP_COLD_ICEBOX_SAFE`（耐到冰盒地）
- 突变器官：精金谐振器（`adamantine_resonator`）/ 魔像眼 / 魔像脑 / 魔像舌 / 魔像胃 / 魔像肝 / 魔像阑尾（全套魔像器官）
- 全身魔像肢体覆盖
- `species_language_holder = /datum/language_holder/golem`
- `changesource_flags = MIRROR_BADMIN | WABBAJACK | MIRROR_PRIDE | MIRROR_MAGIC`（**无 RACE_SWAP**）
- 职业预设加成：石肤防御（正面）、材料增益（正面）、工具/建造（正面）、需进食矿物维持生命（负面）

---

### 8. 共享特质汇总（本批内 ≥2 物种）

| 特质代码 | 中文名 | 持有物种 | 数量 |
|---|---|---|---|
| TRAIT_NOBREATH | 无需呼吸 | 外星、安卓、无头、魔像 | 4 |
| TRAIT_NOBLOOD | 无血液 | 外星、安卓、魔像 | 3 |
| TRAIT_NOHUNGER | 无饥饿 | 外星、安卓、无头 | 3 |
| TRAIT_NO_UNDERWEAR | 无内衣 | 外星、安卓、魔像 | 3 |
| TRAIT_MUTANT_COLORS | 突变体颜色 | 蜗牛、果冻、史莱姆 | 3 |
| TRAIT_TOXINLOVER | 毒素爱好者 | 果冻、史莱姆 | 2 |
| TRAIT_NEVER_WOUNDED | 永不受伤 | 外星、魔像 | 2 |
| TRAIT_NODISMEMBER | 断肢免疫 | 外星、魔像 | 2 |
| TRAIT_NOCRITDAMAGE | 无濒死伤害 | 安卓、魔像 | 2 |
| TRAIT_NOFIRE | 不可燃 | 安卓、魔像 | 2 |
| TRAIT_PIERCEIMMUNE | 穿刺免疫 | 安卓、魔像 | 2 |
| TRAIT_RADIMMUNE | 辐射免疫 | 安卓、魔像 | 2 |
| TRAIT_GENELESS | 无基因 | 安卓、魔像 | 2 |
| TRAIT_NO_DNA_COPY | 禁止 DNA 复制 | 安卓、魔像 | 2 |
| TRAIT_NO_PLASMA_TRANSFORM | 抗等离子转化 | 安卓、魔像 | 2 |

> 明显聚类：**安卓 ↔ 魔像**共享 8 条（无血/无呼吸/无濒死/不可燃/穿刺免疫/辐射免疫/无基因/禁 DNA 复制/抗等离子转化/无内衣），构成"无机物生命体"共通特质组；
> **外星 ↔ 魔像**共享 3 条（永不受伤/断肢免疫/无血液）；**外星 ↔ 安卓 ↔ 魔像**共享"三无"（无血/无呼吸/无内衣）。

### 9. 独有特质汇总（本批内仅 1 物种）

| 特质代码 | 中文名 | 持有物种 |
|---|---|---|
| TRAIT_NO_SLIP_ALL | 全防滑 | 蜗牛 |
| TRAIT_WATER_BREATHING | 水下呼吸 | 蜗牛 |
| TRAIT_ABDUCTOR_HUD | 外星人 HUD | 外星 |
| TRAIT_CHUNKYFINGERS_IGNORE_BATON | 粗手指（警棍豁免） | 外星 |
| TRAIT_VIRUSIMMUNE | 病毒免疫 | 外星 |
| TRAIT_LIMBATTACHMENT | 肢体直接附着 | 安卓 |
| TRAIT_LIVERLESS_METABOLISM | 无肝代谢 | 安卓 |
| TRAIT_OVERDOSEIMMUNE | 过量免疫 | 安卓 |
| TRAIT_RESISTCOLD | 耐寒 | 安卓 |
| TRAIT_RESISTHEAT | 耐热 | 安卓 |
| TRAIT_RESISTHIGHPRESSURE | 耐高压 | 安卓 |
| TRAIT_RESISTLOWPRESSURE | 耐低压 | 安卓 |
| TRAIT_TOXIMMUNE | 毒素免疫 | 安卓 |
| TRAIT_USES_SKINTONES | 使用肤色 | 无头 |
| TRAIT_ADVANCEDTOOLUSER | 高级工具使用者 | 无头 |
| TRAIT_LITERATE | 识字 | 无头 |
| TRAIT_CAN_STRIP | 可扒装备 | 无头 |
| TRAIT_BRAINLESS_CARBON | 无脑碳基 | 无头 |
| TRAIT_LAVA_IMMUNE | 熔岩免疫 | 魔像 |
| TRAIT_NOFAT | 不会发胖 | 魔像 |
| TRAIT_NOSOFTCRIT | 无软濒死 | 魔像 |
| TRAIT_NO_AUGMENTS | 禁义体改造 | 魔像 |
| TRAIT_SNOWSTORM_IMMUNE | 暴风雪免疫 | 魔像 |
| TRAIT_UNHUSKABLE | 不可干尸化 | 魔像 |

> 独有特质共 24 条：安卓独占 8 条（环境抗性全家桶），魔像 6 条，无头 5 条，外星 3 条，蜗牛 2 条。

### 10. 动态特质/能力汇总（on_species_gain 相关）

> 说明：本批 7 个物种的 `on_species_gain` 均**未直接调用 ADD_TRAIT**（唯一的 ADD_TRAIT 在蜗牛壳物品的 Initialize 上，来源 INNATE_TRAIT）。"动态特质"以动作授予、元素、信号注册、生理修等形式体现，汇总如下：

| 物种 | 动态能力 | 实现方式 |
|---|---|---|
| 蜗牛 | 强制装备蜗牛壳（带 TRAIT_NODROP 物品特质） | equip_to_slot_or_del + 物品 Initialize ADD_TRAIT |
| 果冻 | 再生肢体动作、软着陆元素、史莱姆血液处理信号 | Grant / AddElement / RegisterSignal |
| 史莱姆 | 分裂身体、交换身体动作；死亡转移意识信号；身体池 | Grant ×2 / RegisterSignal / 变量池 |
| 外星 | —（无） | — |
| 安卓 | —（无） | — |
| 无头 | 解除听力敏感（gain）/ 恢复听力敏感（loss）；头部中继全套机制；除颤脑检查 | lose/become_hearing_sensitivity + RegisterSignal + relay |
| 魔像 | 营养即生命信号链（受伤耗营养/饿死/不可除颤/材料重建/生命提示）；生理修正（耐力/眩晕 −40%、击倒 +20%） | RegisterSignal ×6 + physiology 修正 |

---

*本清单由源代码直接提取（NovaSector 分支），特质中文释义基于官方 `#define` 注释与实际实现。*

### 1. Rogue（叛逃者 / abductorweak）

**文件**: `abductorweak.dm`（101 行）｜**ID**: `SPECIES_ABDUCTORWEAK`｜**显示名**: "Rogue"（叛逃者）
**简介**: 阿布杜克特（Abductor，绑架者）的叛逃分支——三指、苍白、好奇心强的外星人，无法与船员正常交流（需 TTS 设备或替换声带）。曾为绑架者一员但记忆被抹除。

### inherent_traits（6 条，全量）

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_CHUNKYFINGERS_IGNORE_BATON` | 粗手指·警棍豁免 | 粗壮的三指手难以操作常规装备，但可正常使用警棍类武器 | ★ |
| `TRAIT_NOBREATH` | 无需呼吸 | 不需要呼吸（无肺） | |
| `TRAIT_NOHUNGER` | 无饥饿 | 不需要进食（无胃） | |
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械与高级工具 | |
| `TRAIT_CAN_STRIP` | 可脱衣 | 可以将他人身上的衣物脱下 | |
| `TRAIT_LITERATE` | 识字 | 能阅读书写，理解书面信息 | |

### 动态特质（on_species_gain / 其他）

- **on_species_gain**: 为角色开启 `DATA_HUD_ABDUCTOR`（阿布杜克特 HUD，可查看绑架者相关数据）；`on_species_loss` 时移除。
- **器官**: 突变舌=阿布杜克特舌、突变脑=阿布杜克特脑；胃/肺为 `null`（没有胃和肺）；全身使用阿布杜克特体肢（六肢覆盖）。
- **武器联动**: 阿布杜克特发射针（firing pin）允许 Rogue 使用（`isrogue(user)`）。
- **物理属性文案**: "Rogues do not need to breathe, eat, have a stomach, or lungs but their naturally chunky tridactyl hands make it hard to operate generic equipment."（不需要呼吸进食、没有胃和肺，但粗壮的三指手难以操作常规装备）

---

### 2. Akula（阿库拉鲨人 / akula）

**文件**: `akula.dm`（277 行）｜**ID**: `SPECIES_AKULA`｜**显示名**: "Akula"，复数 "Akulae"
**简介**: 阿祖兰人（Azuleans），来自 Agurkrral 王国的君主制鲨鱼文明，扩张主义、集体主义的机遇主义者。

### inherent_traits（6 条，全量）

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械与高级工具 | |
| `TRAIT_CAN_STRIP` | 可脱衣 | 可以将他人身上的衣物脱下 | |
| `TRAIT_LITERATE` | 识字 | 能阅读书写 | |
| `TRAIT_WATER_BREATHING` | 水中呼吸 | 可在水下呼吸（可离水生存但会干燥） | ★（本批仅鲨鱼类） |
| `TRAIT_SLICK_SKIN` | 湿滑皮肤 | 潮湿机制核心：湿身层数≥3 时触发湿滑事件（NOVA 特有机制） | ★ |
| `TRAIT_MUTANT_COLORS` | 突变色 | 可使用三色自定义配色（鳍、尾、花纹） | |

### 动态特质（on_species_gain / 湿身系统）

- **on_species_gain**: 注册 `COMSIG_MOB_TRIGGER_WET_SKIN` 信号；初始给予 **5 层湿身**（WETSTACK_INITIAL）。
- **wetted()**（湿身层数 ≥3 时触发）: 
  - 若未拥有 → `ADD_TRAIT(TRAIT_SLIPPERY, SPECIES_TRAIT)` ★ **动态获得湿滑特质**（难以被抓住/易滑脱）。
  - 清除 "dry_skin" 负面心情；启动 10 分钟干燥计时器（DRY_UP_TIME）。
- **dried()**（10 分钟未沾水）: 添加 `dry_skin`（皮肤干燥）负面心情，直到再次弄湿。
- **湿身层数衰减**: 层数 ≤0.5 时 `REMOVE_TRAIT(TRAIT_SLIPPERY, SPECIES_TRAIT)`。
- **器官组**: 全部替换为 carp/akula 系列（脑/心/肺/舌/眼），拥有独立的"器官组加成"（`organ_set_bonus_carp_akula`，无 carpskin 纹理）；肺移除 `TRAIT_NO_BREATHLESS_DAMAGE`（离水会缺氧受伤）；舌偏好海鲜+生肉。
- **随机特征**: 4 套固定配色方案（青绿/绯红/金黄/紫罗兰）。
- **专属职业装备**: `job.akula_outfit`（每个职业可定义专属阿库拉制服）。

---

### 3. Akula Generic（通用阿库拉 / aquatic）

**文件**: `aquatic.dm`（84 行）｜**ID**: `SPECIES_AQUATIC`｜**显示名**: "Akula (Generic)"（通用阿库拉）
**简介**: 通用/模板版鲨人，供玩家自定义更自由的外貌（无专属文化设定，placeholder 文案）。

### inherent_traits（5 条，全量）

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械 | |
| `TRAIT_CAN_STRIP` | 可脱衣 | 可脱他人衣物 | |
| `TRAIT_LITERATE` | 识字 | 能阅读书写 | |
| `TRAIT_WATER_BREATHING` | 水中呼吸 | 可在水下呼吸 | ★（鲨鱼类） |
| `TRAIT_MUTANT_COLORS` | 突变色 | 三色自定义配色 | |

### 动态特质

- 无 on_species_gain 特质逻辑；**器官**: 突变舌=水生舌（偏好海鲜/肉/水果/内脏，厌恶布料/恶心物，毒物=TOXIC），气泡图标覆盖为鱼。
- **默认突变部件**: 鲨鱼尾、鲨鱼吻、锤头耳、正常腿；角/翅禁用。
- **随机特征**: 5 套鲨鱼灰蓝配色。

---

### 4. Dwarf（矮人 / dwarf）

**文件**: `dwarf.dm`（26 行）｜**ID**: `SPECIES_DWARF`｜**显示名**: "Dwarf"（矮人）

### inherent_traits（6 条，全量）

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_DWARF` | 矮人 | 矮小体型（本批仅矮人拥有；配合 `body_size_restricted = TRUE` 体型锁定为矮） | ★ |
| `TRAIT_SNOB` | 势利眼 | 对低品质酒/饮品有负面反应（势利性格特质） | ★ |
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械 | |
| `TRAIT_CAN_STRIP` | 可脱衣 | 可脱他人衣物 | |
| `TRAIT_LITERATE` | 识字 | 能阅读书写 | |
| `TRAIT_USES_SKINTONES` | 使用肤色 | 使用标准肤色而非突变色 | ★（本批仅矮人/血噬族） |

### 动态特质

- **器官**: 突变舌=矮人语舌（`tongue/dwarven`）。
- **体型**: `body_size_restricted = TRUE`（强制矮人体型）。
- **剥皮产物**: 人类兽皮（`animalhide/carbon/human`）。
- **预览**: 红棕色矮人胡子 "Beard (Dwarf)"。

---

### 5. Ghoul（食尸鬼 / ghoul）

**文件**: `ghoul.dm`（219 行）｜**ID**: `SPECIES_GHOUL`｜**显示名**: "Ghoul"（食尸鬼）
**简介**: 皮包骨头的辐射幸存者——"they're like fuckin skin and bones"。

### inherent_traits（8 条，全量）

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械 | |
| `TRAIT_RADIMMUNE` | 辐射免疫 | 完全免疫辐射伤害 | ★ |
| `TRAIT_CAN_STRIP` | 可脱衣 | 可脱他人衣物 | |
| `TRAIT_EASYDISMEMBER` | 易肢解 | 肢体更容易被砍断 | ★ |
| `TRAIT_EASILY_WOUNDED` | 易受伤 | 更容易受到伤口（创伤）影响 | ★ |
| `TRAIT_LITERATE` | 识字 | 能阅读书写 | |
| `TRAIT_MUTANT_COLORS` | 突变色 | 可使用自定义配色 | |
| `TRAIT_FIXED_MUTANT_COLORS` | 固定突变色 | 颜色锁定为选择的腐尸色（不可再改） | ★ |

### 动态特质（on_species_gain）

- **on_species_gain**: 
  - 校正腐尸肤色（`proof_ghoul_features` → `set_ghoul_color`，将 `FEATURE_GHOUL_COLOR` 写入 `fixed_mut_color`）。
  - 注册 `COMSIG_ATOM_ATTACKBY` → **attach_meat**（生肉补肢机制）★。
  - 将默认体肢全部替换为食尸鬼体肢（`ReassignForeignBodyparts`）。
- **生肉补肢（独有机制）**: 对缺失的四肢使用生肉块（`/obj/item/food/meat/slab`），3 秒后长出新肢并消耗肉块——食尸鬼独有。
- **自我拆肢**: 对自身使用"缴械（disarm）"可拆下手臂/腿并捡起掉落肉块（3 秒，需无手铐、有机肢体）。
- **数值调整**: `stunmod = 1.25`（受到眩晕时长 ×1.25，更容易被控）；`bodytemp_normal = T20C`（正常体温 20°C，比人类低）；`can_have_genitals = FALSE`（无生殖器）。
- **on_species_loss**: 取消信号、恢复默认体肢类型。

---

### 6. Golem Weak（弱化魔像 / golemweak）

**文件**: `golemweak.dm`（20 行）｜**ID**: `SPECIES_GOLEMWEAK`｜**显示名**: 继承基类 "Golem"
**说明**: 本文件为基类 `/datum/species/golem` 的弱化子类（`nova_stars_only = TRUE`，仅 NOVA 服务器可选）。**未定义自己的 inherent_traits**，完整继承基类魔像的 18 条特质。

### 继承自基类 golem 的 inherent_traits（18 条，全量）

| 特质 | 中文名 | 含义/效果 |
|---|---|---|
| `TRAIT_GENELESS` | 无基因 | 无 DNA/基因，不受基因改造影响 |
| `TRAIT_LAVA_IMMUNE` | 岩浆免疫 | 免疫岩浆伤害 |
| `TRAIT_NEVER_WOUNDED` | 永不受伤 | 不会形成伤口（创伤） |
| `TRAIT_NOBLOOD` | 无血液 | 没有血液循环 |
| `TRAIT_NOBREATH` | 无需呼吸 | 不需要呼吸（无肺） |
| `TRAIT_NOCRITDAMAGE` | 无暴击伤害 | 不会进入暴击（crit）状态 |
| `TRAIT_NODISMEMBER` | 不可肢解 | 肢体不会被砍断 |
| `TRAIT_NOFAT` | 无脂肪 | 不会变胖/增重 |
| `TRAIT_NOFIRE` | 无火焰 | 不会着火 |
| `TRAIT_NOSOFTCRIT` | 无软暴击 | 不会进入软暴击（晕厥）状态 |
| `TRAIT_NO_AUGMENTS` | 无植入物 | 不能安装机械植入物 |
| `TRAIT_NO_DNA_COPY` | 无DNA复制 | 无法被克隆/DNA 复制 |
| `TRAIT_NO_PLASMA_TRANSFORM` | 无血浆转化 | 不会因血浆转化为其他物种 |
| `TRAIT_NO_UNDERWEAR` | 无内衣 | 不显示内衣槽位 |
| `TRAIT_PIERCEIMMUNE` | 穿刺免疫 | 免疫穿刺类伤害 |
| `TRAIT_RADIMMUNE` | 辐射免疫 | 免疫辐射 |
| `TRAIT_SNOWSTORM_IMMUNE` | 暴风雪免疫 | 免疫暴风雪环境伤害 |
| `TRAIT_UNHUSKABLE` | 不可干尸化 | 不会被烧成干尸 |

### 动态特质与弱化差异（★ 独有）

- **弱化胃**: `mutantstomach = /obj/item/organ/stomach/golem/weak`，`hunger_modifier = 1`——**弱化魔像会正常饥饿**（基类魔像靠吃矿物、不饿）。
- **黑名单矿物增益（★ 独有）**: 无法通过食用以下材料获得魔像增益：蓝空间（bluespace）、吉布顿岩（gibtonite）、香蕉岩（bananium）、钛（titanium）、血浆（plasma）——防止这些强力/爆炸性增益被滥用。
- 基类其余属性: 无心脏/无肺、`siemens_coeff = 0`（完全绝缘）、矿物生物类型、禁止大部分装备槽、吃矿物获得增益（adamantine_resonator 器官）。

---

### 7. Hemophage（血噬族 · 全套 / hemophage）

**文件**: `hemophage/` 子目录共 **13 个 .dm**（本批重点）
- `_hemophage_defines.dm`（21 行，宏定义）
- `hemophage_species.dm`（142 行，物种本体）
- `hemophage_status_effects.dm`（221 行，状态效果）
- `hemophage_tumor.dm`（147 行，搏动肿瘤=心脏）
- `hemophage_actions.dm`（234 行，5 个主动技能）
- `hemophage_organs.dm`（110 行，腐化器官）
- `tumor_corruption.dm`（81 行，肿瘤腐化扩散元素）
- `_organ_corruption.dm`（196 行，器官腐化组件基类）
- `atrophied_lungs.dm` / `corrupted_liver.dm` / `corrupted_stomach.dm` / `corrupted_tongue.dm`（腐化器官子类）
- `hemophage_moods.dm`（14 行，心情事件）

**ID**: `SPECIES_HEMOPHAGE`｜**显示名**: "Hemophage"（血噬族）
**简介**: 并非真正独立物种，而是被共生感染（通常经咬伤传播）的个体统称。共生体与宿主心血管系统整合，钻入脊髓与大脑，使宿主必须饮血维生。万圣节可开局选择（`check_roundstart_eligible`）。

### inherent_traits（9 条，全量）

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械 | |
| `TRAIT_CAN_STRIP` | 可脱衣 | 可脱他人衣物 | |
| `TRAIT_NOHUNGER` | 无饥饿 | 不依赖常规营养（靠血生存） | |
| `TRAIT_NOBREATH` | 无需呼吸 | 不呼吸（肿瘤肺循环 CO₂→O₂） | |
| `TRAIT_OXYIMMUNE` | 缺氧免疫 | 免疫缺氧伤害 | ★ |
| `TRAIT_VIRUSIMMUNE` | 病毒免疫 | 免疫绝大多数疾病 | ★ |
| `TRAIT_LITERATE` | 识字 | 能阅读书写 | |
| `TRAIT_DRINKS_BLOOD` | 饮血 | 通过饮血维持生命（核心机制） | ★ |
| `TRAIT_USES_SKINTONES` | 使用肤色 | 使用标准肤色 | |

**其他物种属性**: 通用血液型（`exotic_bloodtype = BLOOD_TYPE_UNIVERSAL`）；无性别生殖器限制未设置；`allows_food_preferences = FALSE`（不吃常规食物）。

### 动态特质（on_species_gain / 状态效果，★ 独有机制）

- **on_species_gain**: 
  - 发送出生提示（"你是血噬族，在橱柜外会持续失血……"）。
  - 血容量设为 **最大值的 60%**（BLOOD_VOLUME_ROUNDSTART_HEMOPHAGE）。
  - `bleed_mod *= 1.2`（流血量 ×1.2，更易失血）；on_species_loss 时还原为正常血容量。
- **持续失血（核心机制）**: 肿瘤心脏 `on_life` 每秒失血 0.05u（NORMAL_HEMOPHAGE_BLOOD_DRAIN）；在橱柜内（非尸袋）不扣血；血容量 ≤ 生存阈值 → 直接死亡（"失血饿死"）。
- **黑暗中/橱柜内回血**: 光照 < 0.1 或处于橱柜中时快速回血（消耗血）。
- **肿瘤休眠（Dormant State）★**:
  - `ADD_TRAIT(TRAIT_AGEUSIA, TRAIT_TUMOR)`——**休眠时失去味觉**（无法品味，防呕吐机制失效）。
  - 血耗降为 1/10（×0.1），移动速度大幅降低（×2 减速），受到伤害 ×3（brute/burn/tox/stamina 全乘 3）。
  - 所有血噬技能禁用（除开关休眠外）；2 分钟冷却。
- **Master of the House（家中之主）★**: 主动夺回肺的控制权——
  - 激活: `REMOVE_TRAIT(TRAIT_NOBREATH, SPECIES_TRAIT)` + `REMOVE_TRAIT(TRAIT_OXYIMMUNE, SPECIES_TRAIT)`（重新需要呼吸）；耐力上限减半、移动减速 ×0.75；每秒回血 0.02u（上限为出生血容量）。
  - 结束: 若存在缺氧伤害 → 施加 `slave_to_the_tumor`（40 秒内用血修复缺氧，每秒 0.25u 血/点）；否则直接 `ADD_TRAIT` 恢复 NOBREATH + OXYIMMUNE。
- **Slave to the Tumor（肿瘤之奴）**: 结束时恢复 `ADD_TRAIT(TRAIT_NOBREATH)` + `ADD_TRAIT(TRAIT_OXYIMMUNE)`。
- **Hemokinetic Regen（血动再生）**: 总伤害 ≤50 时每秒回 1.8 点 brute+burn，每点伤害耗 0.25u 血；超过 50 伤害自动停止。
- **Hemokinetic Clot（血动凝血）**: 消耗 50u 血完全止住一个流血伤口（临时，受伤部位再次受损则伤口复发）。
- **Drain Victim（吸血）★（独有主动）**: 拖拽活体碳基目标，3 秒后吸取最多 50u 血；吸血有大量限制——目标有玩家且为人类时吸血量 ×1.15 并获得 30 分钟 "Thirst Satiated"（血耗减半）；对猴子吸血会恶心（-4 心情）；目标血液含大蒜素（garlic）会厌恶退缩；神圣抗性目标无法吸血；无血/不同血型目标无法吸。
- **器官腐化系统（★ 独有）**: 肿瘤（心脏）不可摘除（ORGAN_UNREMOVABLE）；肝/胃/舌/肺全部被"腐化"（ORGAN_TUMOR_CORRUPTED）——午夜黑色搏动组织包裹：
  - **腐化肝**: 饮酒时若杯中 <75% 为"补血试剂" → 剧烈呕吐（清除 95% 胃内容物）+ 恶心；饮酒本身不提供营养。
  - **腐化胃**: 吃非 BLOODY 食物 → 呕吐；血在胃中缓慢代谢（0.1×标准代谢率）。
  - **腐化舌**: 偏好 BLOODY 食物；给予"吸血"技能。
  - **萎缩肺**: 循环 CO₂→O₂（无需呼吸的原理）。
  - **肿瘤移除后果**: 所有腐化器官每秒受损 0.5（迅速衰竭）；肿瘤移植给他人后原宿主器官持续衰败。
- **心情系统**: 饮人血 +2 心情（5 分钟）；吸猴子血 -4；吸人猴血 -1。
- **血液 HUD**: 状态栏显示当前血量（"Current blood level: X/Y"）。

---

### 8. Humanoid（人形模板 / humanoid）

**文件**: `humanoid.dm`（50 行）｜**ID**: `SPECIES_HUMANOID`｜**显示名**: "Humanoid"（人形）
**简介**: 供玩家/创作者自定义的模板物种（"This is a template species for your own creations!"）。

### inherent_traits（4 条，全量）

| 特质 | 中文名 | 含义/效果 |
|---|---|---|
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械 |
| `TRAIT_CAN_STRIP` | 可脱衣 | 可脱他人衣物 |
| `TRAIT_LITERATE` | 识字 | 能阅读书写 |
| `TRAIT_MUTANT_COLORS` | 突变色 | 三色自定义配色 |

### 动态特质

- 无 on_species_gain 特质逻辑；标准人形体肢；所有突变部件默认禁用（尾/吻/耳/翅/人马/角）。
- 预览: 棕色+黑色卷角（Curled）、玉米辫发型。

---

### 9. Insect（拟人昆虫 / insect）

**文件**: `insect.dm`（51 行）｜**ID**: `SPECIES_INSECT`｜**显示名**: "Anthromorphic Insect"（拟人昆虫）

### inherent_traits（4 条，全量）

| 特质 | 中文名 | 含义/效果 |
|---|---|---|
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械 |
| `TRAIT_CAN_STRIP` | 可脱衣 | 可脱他人衣物 |
| `TRAIT_LITERATE` | 识字 | 能阅读书写 |
| `TRAIT_MUTANT_COLORS` | 突变色 | 自定义配色 |

### 动态特质

- **生物类型**: `MOB_ORGANIC | MOB_HUMANOID | MOB_BUG`（含虫类生物类型，受影响虫类相关效果）★。
- **器官**: 突变舌=昆虫舌。
- **默认部件**: 蜂翅（Bee）；尾/吻/角/耳/人马/绒毛/蛾触角默认禁用。
- 无 on_species_gain 特质逻辑。

---

### 10. Insectoid（虫族 / insectoid）

**文件**: `insectoid.dm`（63 行）｜**ID**: `SPECIES_INSECTOID`｜**显示名**: "Insectoid"（虫族）

### inherent_traits（6 条，全量）

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_MUTANT_COLORS` | 突变色 | 自定义配色 | |
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械 | |
| `TRAIT_CAN_STRIP` | 可脱衣 | 可脱他人衣物 | |
| `TRAIT_LITERATE` | 识字 | 能阅读书写 | |
| `TRAIT_WEB_WEAVER` | 织网者 | 可编织蜘蛛网 | ★ |
| `TRAIT_WEB_SURFER` | 网上冲浪 | 在蛛网上快速移动、不受蛛网减速 | ★ |

### 动态特质

- **器官（★ 独特组合）**: 突变眼=虫眼（无眨眼动画）、突变胃=蟑螂胃、突变肝=蟑螂肝、突变阑尾=蟑螂阑尾——**消化系统为蟑螂器官**。
- **血液**: 异种血型 "Chlorocruorin"（氯血绿蛋白，蜘蛛/节肢动物血液）。
- **生物类型**: `MOB_ORGANIC | MOB_HUMANOID | MOB_BUG`。
- **身体结构**: 可选趾行（digitigrade）；默认皇家触角、虫尾、虫翅 II、虫绒毛。
- **肉**: 蜘蛛肉。
- 无 on_species_gain 特质逻辑。

---

### 11. Lizard（蜥蜴人修改 / lizard）

**文件**: `lizard.dm`（105 行）｜**ID**: `SPECIES_LIZARD`（修改基类）＋子类 ashwalker / silverscale
**说明**: 本文件为**基类蜥蜴人的 NOVA 修改**（NOVA EDIT），并非新物种。

### 修改内容

- `body_markings = list()`（清空基类默认身体花纹）
- `mutant_organs = list()`（清空默认突变器官）
- `payday_modifier = 1.0`（工资系数）
- **默认突变部件**: 光滑尾、尖+浅吻、长+膜棘、短褶、卷角、"浅腹"花纹、趾行腿（DIGITIGRADE_LEGS）。
- **随机配色**: 随机三色方案（同色/衍生色/全随机）。

### 子类特质

- **Ash Walker（灰烬行者）**: `always_customizable = TRUE`；`inherent_traits = list(TRAIT_MUTANT_COLORS)`（基类灰烬行者另有 `TRAIT_VIRUSIMMUNE`）；棘默认禁用。NOVA 修改: 移除阵营 `FACTION_ASHWALKER`（改由仪式授予）。
- **Silver Scale（银鳞）**: 继承基类银鳞特质（`TRAIT_HOLY` 神圣、`TRAIT_NOBREATH` 无需呼吸、`TRAIT_PIERCEIMMUNE` 穿刺免疫、`TRAIT_RESISTHIGHPRESSURE` 高压抵抗、`TRAIT_RESISTLOWPRESSURE` 低压抵抗、`TRAIT_VIRUSIMMUNE` 病毒免疫、`TRAIT_WINE_TASTER` 品酒师）；NOVA 修改: 颜色不随机化、蓝眼睛。

---

### 12. Mammal（兽人模板 / mammal）

**文件**: `mammal.dm`（115 行）｜**ID**: `SPECIES_MAMMAL`｜**显示名**: "Anthromorph"（拟人兽）
**简介**: NOVA 的核心兽人模板（"the species is so much more universal than just mammals"）。

### inherent_traits（4 条，全量）

| 特质 | 中文名 | 含义/效果 |
|---|---|---|
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械 |
| `TRAIT_CAN_STRIP` | 可脱衣 | 可脱他人衣物 |
| `TRAIT_LITERATE` | 识字 | 能阅读书写 |
| `TRAIT_MUTANT_COLORS` | 突变色 | 三色自定义配色 |

### 动态特质

- **器官**: 突变舌=兽人舌（偏好谷物+肉，厌恶布料/恶心/内脏）。
- **默认部件**: 哈士奇尾、哈士奇吻、哈士奇耳；角/人马/绒毛/翅/头饰/颈饰默认禁用。
- **随机特征**: 7 套方案（白黑、橙白、白橙、灰白、棕橙、奶油、以及"闪光狗"全随机）。
- **随机花纹**: 从推荐给该物种的花纹组中随机挑选。
- 无 on_species_gain 特质逻辑。

---

### 13. Monkey（猴子修改 / monkey）

**文件**: `monkey.dm`（27 行）｜**ID**: 修改基类 `/datum/species/monkey`
**说明**: 基类猴子特质的 NOVA 修改（外观/行为补充），未新增 inherent_traits。

### 继承基类 inherent_traits（3 条）

| 特质 | 中文名 | 含义/效果 |
|---|---|---|
| `TRAIT_NO_BLOOD_OVERLAY` | 无血液覆盖层 | 不显示血迹覆盖层 |
| `TRAIT_NO_DNA_COPY` | 无DNA复制 | 无法被克隆/DNA 复制 |
| `TRAIT_NO_UNDERWEAR` | 无内衣 | 无内衣槽位 |

### 动态特质

- **默认部件**: 猴子尾（强制，随机化时排除无尾选项）。
- **自定义穿着图标**: 猴子使用专属穿戴图标（`worn_icon_monkey`）。
- **Kobold 猴子变体**（`/mob/living/carbon/human/species/monkey/kobold`）: 由猴子突变而来时，原物种设为蜥蜴人、使用蜥蜴人名字生成。

---

### 14. Moth（蛾人修改 / moth）

**文件**: `moth.dm`（41 行）｜**ID**: 修改基类 `/datum/species/moth`

### 新增 inherent_traits（1 条）

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_MUTANT_COLORS` | 突变色 | 自定义配色（NOVA 为基类蛾人新增；基类蛾人原本无 inherent_traits） | ★（NOVA 新增） |

### 动态特质

- **默认部件**: 蛾标记（默认无）、绒毛"Plain"、翅"Moth (Plain)"、蛾触角"Plain"。
- **随机特征**: 固定默认色 `#E5CD99`（米黄色）。
- **随机花纹**: 从推荐花纹组随机挑选。
- 无 on_species_gain 特质逻辑。

---

## 附录 · 本批统计

| 物种 | 文件 | inherent_traits 数 | 动态特质 | 独有发现 |
|---|---|---|---|---|
| Rogue | abductorweak.dm | 6 | 阿布杜克特 HUD | 粗手指、无胃无肺、叛逃者 |
| Akula | akula.dm | 6 | 湿身系统 → 动态 TRAIT_SLIPPERY | 湿滑皮肤+水中呼吸+10分钟干燥机制 |
| Akula Generic | aquatic.dm | 5 | 无 | 通用鲨人模板 |
| Dwarf | dwarf.dm | 6 | 无 | TRAIT_DWARF+SNOB、体型锁定 |
| Ghoul | ghoul.dm | 8 | 生肉补肢、腐尸色锁定、食尸鬼体肢 | 易肢解+易受伤组合、自我拆肢吃自己 |
| Golem Weak | golemweak.dm | 18（继承） | 黑名单矿物增益、会饥饿的胃 | 弱化魔像：不能吃蓝空间/钛等 |
| **Hemophage** | **hemophage/**（13 文件） | **9** | **肿瘤系统/休眠/吸血/血动再生/器官腐化/家中之主** | **最复杂物种：饮血、缺氧免疫、病毒免疫、失血死亡、腐化器官连锁** |
| Humanoid | humanoid.dm | 4 | 无 | 模板物种 |
| Insect | insect.dm | 4 | 无 | MOB_BUG 生物类型、蜂翅 |
| Insectoid | insectoid.dm | 6 | 无 | 织网+网上冲浪、蟑螂消化器官、氯血红蛋白 |
| Lizard | lizard.dm（修改） | 1~7（子类） | 无 | 基类修改、灰烬/银鳞子类 |
| Mammal | mammal.dm | 4 | 无 | 核心兽人模板 |
| Monkey | monkey.dm（修改） | 3（继承） | 猴子专属穿戴图标 | Kobold 猴子变体 |
| Moth | moth.dm（修改） | 1（新增） | 无 | NOVA 为蛾人新增突变色 |

**血噬族重点总结**: 全 NOVA 最复杂的物种系统（13 个文件、约 1,480 行），核心是"搏动肿瘤"（不可摘除心脏）驱动的**失血-吸血-回血**闭环：常态持续失血（0.05u/秒）→ 橱柜/黑暗中回血 → 吸血补充（最多 50u/次，玩家目标 ×1.15）；四个主动技能（休眠、血动再生、血动凝血、家中之主）；五器官腐化系统（肝/胃/舌/肺/心脏）在肿瘤摘除后连锁衰败；动态特质通过状态效果增删 `TRAIT_NOBREATH`/`TRAIT_OXYIMMUNE`（家中之主期间主动呼吸）与 `TRAIT_AGEUSIA`（休眠时失味）。

### 5.2 NOVA 种族特质（批 4-5）

### 1. Mushpeople（蘑菇人修改 / mushpeople）

**文件**: `mushpeople.dm`（15 行，NOVA 覆写）｜**基类**: `/datum/species/mush`（基础批已收录，4 条特质）
**定位**: NOVA 对基础蘑菇人的外观定制补丁，**不新增/不修改 inherent_traits**，全部改动围绕"菌盖"部件。

### inherent_traits（继承基础 4 条，NOVA 未改动）

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_MUTANT_COLORS` | 突变色 | 三色自定义配色 | （基础继承） |
| `TRAIT_NOBREATH` | 不呼吸 | 无需呼吸/无窒息伤害 | （基础继承） |
| `TRAIT_NOFLASH` | 抗闪光 | 免疫闪光弹/强光致盲 | （基础继承） |
| `TRAIT_NO_UNDERWEAR` | 不穿内衣 | 无内衣槽位 | （基础继承） |

### 动态特质 / 覆写行为

- **`get_default_mutant_bodyparts()`**: 默认部件 = 耳（无）+ **菌盖 `FEATURE_MUSH_CAP` 强制 "Round"**（注释：we don't want cap-less mushpeople —— 不允许无盖蘑菇人）。
- **`randomize_features()`**: 随机化时菌盖从全部菌盖样式中 pick（**剔除 `SPRITE_ACCESSORY_NONE` 无盖选项**）★。
- **`prepare_human_for_preview()`**: 预览角色固定红色圆盖（`#FF4B19`）。

> 核心发现：NOVA 蘑菇人 = 基础蘑菇人 + **"必须长菌盖"** 的强制外观规则，无任何新增特质。

---

### 2. Podweak（弱化植物人 / podweak）

**文件**: `podweak.dm`（74 行）｜**基类**: `/datum/species/pod`（基础植物人）｜**子类**: `/datum/species/pod/podweak`（ID = `SPECIES_PODPERSON_WEAK`，显示名 "Podperson"）
**定位**: NOVA 植物人体系：先给基类 pod 打补丁，再定义弱化子类 podweak。

### 2.1 `/datum/species/pod`（基类 NOVA 补丁）

**inherent_traits（5 条，NOVA 覆写完整列表）：**

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_MUTANT_COLORS` | 突变色 | 三色自定义配色 | （基础继承） |
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械 | （基础继承） |
| `TRAIT_CAN_STRIP` | 可脱衣 | 可脱他人衣物 | （基础继承） |
| `TRAIT_PLANT_SAFE` | 植物安全 | 植物类物品/植物人相关机制安全 | （基础继承） |
| `TRAIT_LITERATE` | 识字 | 能阅读书写 | （基础继承） |

**其他关键定义**: `mutant_organs = list()`（移除 pod 毛发器官，NOVA 改由 DNA 部件处理）；`payday_modifier = 1.0`；默认突变部件 = 耳无 + **藤蔓发型 "Ivy"** + 正常腿。

### 2.2 `/datum/species/pod/podweak`（弱化子类，开局可选）

**inherent_traits（4 条，完整）：**

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械 | |
| `TRAIT_CAN_STRIP` | 可脱衣 | 可脱他人衣物 | |
| `TRAIT_LITERATE` | 识字 | 能阅读书写 | |
| `TRAIT_MUTANT_COLORS` | 突变色 | 三色自定义配色 | |

> **对比**：子类去掉了基类的 `TRAIT_PLANT_SAFE`（植物安全），且 `always_customizable = FALSE`（不可自由定制部件）。

### 动态特质（on_species_gain 注册 COMSIG_LIVING_LIFE → on_life）

- **光合营养**: 站地面时按光照 `light_amount = min(1, 光照) - 0.5` 调整营养，`adjust_nutrition(5 × light × spt)`；营养超过 ALMOST_FULL 时钳制回满。
- **光下自愈**（light_amount > 0.2）★: 每秒回 0.5 钝击 + 0.35 灼烧 + 回体力 0.4 + 去毒 0.1 + 去缺氧 0.2，带红色治疗特效粒子（`/obj/effect/temp_visual/heal`）。
- **饥饿惩罚** ★: 营养 < 饥饿线+50 时**每秒受 1 钝击**，伴随 `/obj/effect/temp_visual/annoyed/plant` 动画（基础植物人同样机制）。

---

### 3. Protean（液态金属纳米机群 / protean_species）

**文件**: `protean_species.dm`（312 行）｜**ID**: `SPECIES_PROTEAN`｜**显示名**: "Protean"｜**生物类型**: `MOB_ROBOTIC | MOB_HUMANOID`
**简介**: 由纳米机器群构成的人形合成生命，寄宿在专属 MOD 套服核心中——"脆弱但杀不死"。

### inherent_traits（22 条，全量）

| # | 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|---|
| 1 | `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械 | |
| 2 | `TRAIT_CAN_STRIP` | 可脱衣 | 可脱他人衣物 | |
| 3 | `TRAIT_LITERATE` | 识字 | 能阅读书写 | |
| 4 | `TRAIT_MUTANT_COLORS` | 突变色 | 三色自定义配色 | |
| 5 | `TRAIT_NOBREATH` | 不呼吸 | 无需呼吸 | 合成系共享 |
| 6 | `TRAIT_ROCK_EATER` | 食石者 | 可吞噬矿物/石头（纳米机群进食金属） | ★（本批唯一） |
| 7 | `TRAIT_STABLEHEART` | 稳定心脏 | 心脏不受致命伤影响 | 合成系共享 |
| 8 | `TRAIT_NOHUNGER` | 不饥饿 | 无饥饿条（吃金属替代） | 合成系共享 |
| 9 | `TRAIT_LIMBATTACHMENT` | 断肢可装回 | 断肢可手动插回 | 合成系共享 |
| 10 | `TRAIT_GENELESS` | 无基因 | 无基因可提取/改造 | 合成系共享 |
| 11 | `TRAIT_NO_HUSK` | 不可剥壳 | 不会被吸成干尸 | 合成系共享 |
| 12 | `TRAIT_NO_DNA_SCRAMBLE` | 免疫 DNA 扰乱 | 不受 DNA 污染/扰乱影响 | ★ |
| 13 | `TRAIT_NO_PLASMA_TRANSFORM` | 免疫等离子转化 | 不会被等离子变异 | ★ |
| 14 | `TRAIT_SYNTHETIC` | 合成体 | 机器人身份（EMP/逻辑相关） | 合成系共享 |
| 15 | `TRAIT_TOXIMMUNE` | 毒素免疫 | 毒素伤害无效 | 合成系共享 |
| 16 | `TRAIT_NEVER_WOUNDED` | 永不受伤 | 无肢体伤口 | 合成系共享 |
| 17 | `TRAIT_VIRUSIMMUNE` | 病毒免疫 | 不会被病毒感染 | 合成系共享 |
| 18 | `TRAIT_RADIMMUNE` | 辐射免疫 | 不受辐射伤害 | 合成系共享 |
| 19 | `TRAIT_EASYDISMEMBER` | 易肢解 | 肢体容易被打断（纳米重组代价） | ★（副作用） |
| 20 | `TRAIT_RDS_SUPPRESSED` | 抑制随机位移症 | 免疫 RDS（超物质随机位移） | ★ |
| 21 | `TRAIT_MADNESS_IMMUNE` | 疯狂免疫 | 免疫超物质疯狂（supermatter madness） | ★ |
| 22 | `TRAIT_NODEATH` | 不死 | 不会真正死亡——重伤时缩回套服核心重建 | ★（核心机制） |

> 注：表格实际 22 行（编号 1-22），全量无省略。

### 动态特质 / 机制（on_species_gain）

- **protean_limb 组件**: 给全身 6 处肢体挂 `/datum/component/protean_limb`（纳米肢体），后续新装肢体自动补挂。
- **专属 MOD 套服**: 自动在背部装备 `/obj/item/mod/control/pre_equipped/protean`（纳米机群壳体），核心 `core.linked_protean` 绑定本人；`NODEATH` 配合"重伤缩入核心"逻辑。
- **器官同化** ★: 注册 `COMSIG_CARBON_GAIN_ORGAN` → 非纳米/机械器官插入会被 **qdel 销毁并替换为 protean 等效器官**（心脏/胃等），提示"your body rejects the foreign organ"。
- **7 个专属 verb** ★: `protean_ui`（纳米界面）/ `protean_heal`（纳米修复）/ `lock_suit`（锁定套服）/ `suit_transformation`（套服变形）/ `low_power`（低电量模式）/ `remove_assimilated_modsuit` / `remove_assimilated_plating`。
- **on_species_loss**: 清理 verb、移除 `TRAIT_CRITICAL_CONDITION`、拆除套服与肢体组件。
- **其他定义**: `siemens_coeff = 1.5`（电系伤害 1.5 倍，怕电）★；`exotic_bloodtype = BLOOD_TYPE_IRON`（铁血，新血型 `/datum/blood_type/iron`）；`meat = /obj/item/stack/sheet/iron`（肉=铁板）；`reagent_flags = PROCESS_SYNTHETIC`；无肺无阑尾；全身机器人 bodypart；`language_prefs_whitelist = /datum/language/monkey`；`allows_food_preferences() = FALSE`（不能选食物偏好，吃金属）。
- **pre/post_equip_outfit 覆写** ★: 换装时临时销毁套服腾出背部槽位，结束后把新背部物品同化/替换为 protean 套服（含存储内容迁移）。

---

### 4. Roundstart Slime（开局史莱姆混血 / roundstartslime）

**文件**: `roundstartslime.dm`（1,239 行，NOVA 最大物种文件之一）｜**基类**: `/datum/species/jelly`（果冻人）｜**子类**: `/datum/species/jelly/roundstartslime`（ID = `SPECIES_SLIMESTART`，显示名 "Xenobiological Slime Hybrid"）
**定位**: NOVA 对果冻人基类的全面覆写 + 开局可选史莱姆混血子类（半透明 alpha 155）。

### 4.1 `/datum/species/jelly`（基类 NOVA 补丁）

**inherent_traits（3 条，NOVA 覆写）：**

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_MUTANT_COLORS` | 突变色 | 三色自定义配色 | （基础继承） |
| `TRAIT_TOXINLOVER` | 毒素爱好者 | 喜欢毒素（毒液血液、毒素当营养） | （基础继承） |
| `TRAIT_EASYDISMEMBER` | 易肢解 | 肢体容易被打断（史莱姆可再生的代价） | ★（NOVA 新增） |

**其他关键定义**: 发/胡须 alpha 160；突变器官全套史莱姆版（肝 `liver/slime`、胃 `stomach/slime`、脑 `brain/slime`、耳 `ears/jelly`）；**无阑尾**；全部器官（眼/耳/舌/肺/肝/胃/脑）改为**胸腔位置**（zone = CHEST，史莱姆无头身结构）且 `ORGAN_UNREMOVABLE` 不可摘除 ★。

### 动态特质（on_species_gain 授予 4 个专属技能 + on_life 被动）

- **4 个专属技能** ★（`/datum/action`）:
  - **Alter Form 变形**（`/datum/action/innate/alter_form`）: 径向菜单随意修改颜色/DNA/发型/花纹/体型/生殖器官/突变部件（史莱姆独有外观自由）。
  - **Slime Washing 史莱姆清洗**（`/datum/action/cooldown/spell/slime_washing`）: 1 秒冷却，清洁自身+装备+脚下地板（光脚时），顺带微量营养。
  - **Hydrophobia 疏水**（`/datum/action/cooldown/spell/slime_hydrophobia`）: 1 分钟冷却，动态 `ADD_TRAIT(TRAIT_SLIME_HYDROPHOBIA)` + 1.5 倍减速，获得防水但**停止再生**。
  - **Core Signal 核心信号**（`/datum/action/innate/core_signal`）: 开关核心 GPS 芯片（死后核心定位用）。
- **on_life 被动** ★（`COMSIG_LIVING_LIFE`）:
  - **被动再生**: 血量 ≥ 正常时每秒回 0.6 钝击 + 0.5 灼烧 + 去缺氧 1（带治疗粒子）。
  - **怕水**: 湿身层数 > 5 → 每秒失血 2；> 1 → 停止再生 + 每秒失血 1（怕水核心机制）。
  - **水呼吸反转**: 若拥有 `TRAIT_WATER_BREATHING`（水呼吸变体）则逻辑反转——**干燥时失血受伤，潮湿时才回血** ★（水栖史莱姆变体支持）。
- **史莱姆核心（脑）机制** ★: 死亡 → **核心弹出**（身体 qdel、核心+植入物落地、装备全卸、蒸汽特效），可被 100u 等离子泼洒修复重生（15 秒 do_after，重建身体、移除四肢、补血，玩家移入新身体）；核心带 GPS 可选；核心不可破坏（FIRE/LAVA/ACID/FREEZE 全免疫）。
- **史莱姆肝化学**: 摄入**等离子/热冰 → 治疗所有伤口 + 修复随机器官**（消耗 22% 体积）；摄入**水 → 失血 3/秒**（除非疏水/水呼吸）。

### 4.2 `/datum/species/jelly/roundstartslime`（开局史莱姆子类）

**inherent_traits**: 继承 jelly 全部 3 条，无新增（子类自身无 inherent_traits 覆写）。

**独有定义** ★:
- `specific_alpha = 155`（**半透明身体**）、`markings_alpha = 130`（花纹更透明，防止叠色变实）。
- `coldmod = 3` / `heatmod = 1`（怕冷，耐热正常——基础 jelly 为 coldmod 6 / heatmod 0.5）。
- 突变眼 = `eyes/roundstartslime`（感光眼斑，胸腔位）；突变舌 = `tongue/jelly`。
- 全身 jelly/slime/roundstart 专属肢体（6 处）。
- `apply_supplementary_body_changes`: 若玩家允许"发色不匹配"偏好则清空发色模式限制。
- **perks（8 个）**: 4 负 4 正——负：易肢解/怕水/水损伤/（scissors 相关肢体易断）；正：再生/器官修复/（hand-holding-droplet）/（person-booth 核心机制）。

> 核心发现：开局史莱姆 = 果冻人基类 + **半透明 + 怕水怕冷 + 核心弹射复活 + 等离子治疗** 的 NOVA 完整改造；水呼吸反转逻辑使"水栖史莱姆"变体成为可能。

---

### 5. Shadekin（影狐 / shadekin）

**文件**: `shadekin.dm`（179 行）｜**ID**: `SPECIES_SHADEKIN`｜**显示名**: "Shadekin"｜**生物类型**: `MOB_ORGANIC | MOB_HUMANOID`
**简介**: 由黑暗凝聚而成的生灵（"pitch darkness given form"），光穿过身体会令其疲惫，不投射影子。

### inherent_traits（8 条，全量）

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械 | |
| `TRAIT_CAN_STRIP` | 可脱衣 | 可脱他人衣物 | |
| `TRAIT_LITERATE` | 识字 | 能阅读书写 | |
| `TRAIT_WATER_BREATHING` | 水中呼吸 | 可在水下呼吸 | ★（本批仅鲨鱼/蜗牛类共享） |
| `TRAIT_SLICK_SKIN` | 湿滑皮肤 | 潮湿机制核心：湿身后触发湿滑事件 | ★（NOVA 特色） |
| `TRAIT_MUTANT_COLORS` | 突变色 | 三色自定义配色 | |
| `TRAIT_NIGHT_VISION` | 夜视 | 黑暗中视如白昼 | ★（本批唯一） |
| `TRAIT_NOBREATH` | 不呼吸 | 无需呼吸（"do not respirate"） | ★ |

### 动态特质 / 机制

- **暗色强制** ★（`SHADEKIN_MAX_BRIGHTNESS = 35`）: 所有颜色（突变色 + 肢体/部件色）的 HSV 亮度被**钳制 ≤ 35**（必须深色），`apply_supplementary_body_changes`/`on_species_gain` 时执行 `clamp_primary_colors`——影狐只能深色外观。
- **随机特征**: 4 套暗色调色板（近黑灰 / 暗酒红 / 暗金棕 / 暗紫）。
- **默认突变部件**: 尾 "Shade"（随机）、耳 "Shade Ears"（随机）、吻无、正常腿。
- **器官**: 突变舌/耳/脑/眼全套 shadekin 版；`species_language_holder = shadekin`；语言白名单 = `/datum/language/marish/empathy`（玛瑞什共情语）。
- **perks（2 个）**: 1 正（lightbulb，暗色系）、1 负（crutch，脆弱）。
- **设定文本**: "Shadekin do not respirate, and their bodies are reformed in the darkness, although frail."（不呼吸、黑暗中重塑身体、脆弱）。

---

### 6. Skrell（乌贼人 / skrell）

**文件**: `skrell.dm`（158 行）｜**ID**: `SPECIES_SKRELL`｜**显示名**: "Skrell"｜**生物类型**: `MOB_ORGANIC | MOB_HUMANOID`

### inherent_traits（4 条，全量）

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械 | |
| `TRAIT_CAN_STRIP` | 可脱衣 | 可脱他人衣物 | |
| `TRAIT_LITERATE` | 识字 | 能阅读书写 | |
| `TRAIT_MUTANT_COLORS` | 突变色 | 三色自定义配色（绿蓝系） | |

### 动态特质 / 机制

- **体温异常** ★: `bodytemp_normal = BODYTEMP_NORMAL + 70`（**体温比人类高 70K**）；热伤阈值 = 火焰蔓延温度；冷伤阈值 = T20C-10（**冷空气就会受伤**——怕冷）。
- **两栖肺** ★（`/obj/item/organ/lungs/skrell`）: `safe_plasma_max = 40` / `safe_co2_max = 40`（能承受少量等离子/CO2）；冷气体阈值 248/220/170K（1/2/3 级，冷伤=钝击）；热气体阈值 318/348/1000K（热伤=灼烧）——呼吸冷空气受伤、呼吸过热气体受伤。
- **内部声囊舌** ★（`/obj/item/organ/tongue/skrell`）: 味觉敏感度 5；**18 种语言**（common/uncommon/draconic/codespeak/monkey/narsie/machine/slime/beachbum/aphasia/piratespeak/moffic/sylvan/shadowtongue/terrum/vox/nekomimetic/skrell）；饮食偏好：喜欢 毒/水果/蔬菜，厌恶 生肉/布料，**毒物=乳制品/肉类**。
- **海棉脑 / 两栖眼 / 斯克莱尔肝**: 眼 `flash_protect = FLASH_PROTECTION_SENSITIVE`（闪光敏感）；肝 `alcohol_tolerance = 5`、`toxTolerance = 10`（可扛 10u 毒素）、`liver_resistance = 1.2×`（+20% 抗毒）。
- **默认突变部件**: 耳无 + 斯克莱尔发（"Short"，随机）；**随机特征**: 6 套绿蓝色板。
- **语言**: `species_language_holder = skrell`；描述/背景为 placeholder（待维护者补写）。

---

### 7. Tajaran（猫族 / tajaran）

**文件**: `tajaran.dm`（106 行）｜**ID**: `SPECIES_TAJARAN`｜**显示名**: "Tajaran"｜**生物类型**: `MOB_ORGANIC | MOB_HUMANOID`

### inherent_traits（7 条，全量）

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械 | |
| `TRAIT_CAN_STRIP` | 可脱衣 | 可脱他人衣物 | |
| `TRAIT_LITERATE` | 识字 | 能阅读书写 | |
| `TRAIT_HATED_BY_DOGS` | 被狗讨厌 | 狗会敌对/厌恶（猫狗天敌） | ★（本批唯一，与基础猫人共享） |
| `TRAIT_MUTANT_COLORS` | 突变色 | 三色自定义配色 | |
| `TRAIT_CATLIKE_GRACE` | 猫系优雅 | 猫科优雅动作（摔落缓冲等） | ★（与基础猫人共享） |
| `TRAIT_WATER_HATER` | 怕水 | 讨厌水、水中/淋湿受惩罚 | ★（与基础猫人共享） |

### 动态特质 / 机制

- **猫舌**（`/obj/item/organ/tongue/cat/tajaran`）: 喜欢 谷物/肉，厌恶 布料。
- **默认突变部件**: 大猫尾 "Cat (Big)"、短兽吻 "Mammal, Short"、警觉猫耳 "Cat, Alert"、正常腿。
- **随机特征**: 5 套偏冷色动物系配色（灰褐/灰/米褐/米白/奶黄）。
- **随机花纹**: 从 "Tajaran / Floof / Floofer" 三套花纹组随机。
- **perks（1 个）**: 中性 perk（FA_ICON_PERSON_FALLING，猫系优雅相关）。
- **语言**: `species_language_holder = tajaran`；`examine_limb_id = SPECIES_MAMMAL`（沿用哺乳类肢体贴图）；描述/背景为 placeholder。

> 核心发现：Tajaran = 猫人三件套（HATED_BY_DOGS + CATLIKE_GRACE + WATER_HATER）的独立物种化，NOVA 特色在于大猫尾/警觉猫耳/专属花纹组。

---

### 8. Unathi（蜥蜴族 / unathi）

**文件**: `unathi.dm`（75 行）｜**ID**: `SPECIES_UNATHI`｜**显示名**: "Unathi"｜**生物类型**: `MOB_ORGANIC | MOB_HUMANOID`

### inherent_traits（4 条，全量）

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械 | |
| `TRAIT_CAN_STRIP` | 可脱衣 | 可脱他人衣物 | |
| `TRAIT_LITERATE` | 识字 | 能阅读书写 | |
| `TRAIT_MUTANT_COLORS` | 突变色 | 三色自定义配色（绿棕系） | |

### 动态特质 / 机制

- **乌纳希舌**（`/obj/item/organ/tongue/unathi`）: 喜欢 内脏/肉/海鲜/坚果，厌恶 谷物/乳制品/布料/恶心物，**毒物 = 毒素**。
- **默认突变部件**: 光滑尾 "Smooth"、尖吻 "Sharp + Light"、卷曲角 "Curled"（固定）、平滑腹花纹 "Smooth Belly"（随机）；刺/褶边禁用；正常腿。
- **随机特征**: 5 套绿色/棕色配色（主色亮、副色深）。
- **肢体**: 全部替换为**蜥蜴肢体**（`bodypart/.../lizard`，含鳞片外观）；`examine_limb_id = SPECIES_LIZARD`。
- **语言**: `species_language_holder` 未单独指定（沿用默认）；描述/背景为 placeholder。

> 注意：本文件无 `TRAIT_RESISTHEAT/COLD` 等耐温特质（基础蜥蜴人有 coldmod 1.5/heatmod 0.67，NOVA unathi 走独立定义）；锐利齿爪等机制在器官/肢体层实现而非特质层。

---

### 9. Vampire（吸血鬼覆写 / vampire）

**文件**: `vampire.dm`（2 行，NOVA 覆写）｜**基类**: `/datum/species/human/vampire`（基础批 3 已收录：`TRAIT_BLOOD_CLANS` + `TRAIT_USES_SKINTONES` + `TRAIT_NO_MIRROR_REFLECTION` 共 3 条）

### inherent_traits（继承基础 3 条，NOVA 未改动）

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_BLOOD_CLANS` | 血族氏族 | 可加入吸血鬼氏族体系 | （基础继承） |
| `TRAIT_USES_SKINTONES` | 使用肤色 | 走肤色系统（强制白化 albino） | （基础继承） |
| `TRAIT_NO_MIRROR_REFLECTION` | 镜子无倒影 | 照镜子看不到自己 | （基础继承） |

### 动态特质 / 覆写行为

- **唯一改动** ★: `prepare_human_for_preview()` 覆写为**直接 return 跳过父类预览逻辑**（NOVA 注释：Skip parent proc）——吸血鬼预览不再强制某些外观设定。
- 无任何新增特质/器官/机制改动（万圣节限定等基础规则不变）。

---

### 10. Vox（鸟人 / vox）

**文件**: `vox.dm`（103 行）｜**ID**: `SPECIES_VOX`｜**显示名**: "Vox"｜**生物类型**: `MOB_ORGANIC | MOB_HUMANOID`
**简介**: 鸟类人形生物，**呼吸氮气**（非氧气），耐寒怕热。

### inherent_traits（4 条，全量）

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械 | |
| `TRAIT_CAN_STRIP` | 可脱衣 | 可脱他人衣物 | |
| `TRAIT_LITERATE` | 识字 | 能阅读书写 | |
| `TRAIT_MUTANT_COLORS` | 突变色 | 三色自定义配色 | |

### 动态特质 / 机制

- **氮气呼吸** ★（`/obj/item/organ/lungs/nitrogen/vox`）: 靠氮气生存（氧气对其有毒/无用），核心生存机制。
- **皮质脑** ★（`/obj/item/organ/brain/cybernetic/cortical/vox`）: 赛博皮质脑（vox 特有）。
- **耐寒怕热** ★: `bodytemp_cold_damage_limit = 标准-30`（更耐寒）；`bodytemp_heat_damage_limit = 标准-15`（**更怕热**，注释：being cold resistant, should make you heat sensitive）。
- **`can_augment = FALSE`**: 不可机械义体改造 ★。
- **专属生存装备**: `outfit_important_for_life = /datum/outfit/vox`（氮气供应装备）；`pre_equip_species_outfit` 优先穿职业专属 vox 制服（`job.vox_outfit`），否则发基础生存装。
- **全槽位专属穿戴图标** ★（`custom_worn_icons`）: 头/面具/外套/制服/手/鞋/眼镜/腰带/杂项/耳 10 个槽位全部使用 vox 专属贴图文件。
- **默认突变部件**: 尾 "Vox Tail"、吻 "Vox Snout"、背刺 "Vox Bands"（随机）、**趾行腿（DIGITIGRADE）**；`digitigrade_customization = DIGITIGRADE_OPTIONAL`。
- **随机特征**: 主色 4 套青绿、副色 2 套米黄、三色 5 套（黑/青/紫等）；随机花纹从 5 套 Vox 花纹组。
- **肉**: 鸡肉（`meat/slab/chicken/human`）；**语言**: `species_language_holder = vox`；描述/背景 placeholder。

---

### 11. Vulpkanin（狐人 / vulpkanin）

**文件**: `vulpkanin.dm`（93 行）｜**ID**: `SPECIES_VULP`｜**显示名**: "Vulpkanin"｜**生物类型**: `MOB_ORGANIC | MOB_HUMANOID`

### inherent_traits（4 条，全量）

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械 | |
| `TRAIT_CAN_STRIP` | 可脱衣 | 可脱他人衣物 | |
| `TRAIT_LITERATE` | 识字 | 能阅读书写 | |
| `TRAIT_MUTANT_COLORS` | 突变色 | 三色自定义配色（橙红系） | |

### 动态特质 / 机制

- **狐舌**（`/obj/item/organ/tongue/vulpkanin`）: 喜欢 生肉/肉，厌恶 布料，**毒物 = 毒素**。
- **默认突变部件**: 狐尾 "Fox"、长兽吻 "Mammal, Long"、狐耳 "Fox"、正常腿。
- **随机特征**: 5 套明亮动物系配色（橙黄/橙/杏黄/橙白/灰白）。
- **随机花纹**: 从 "Fox / Floof / Floofer" 三套花纹组随机。
- **肉**: 柯基肉（`meat/slab/corgi`）★；`examine_limb_id = SPECIES_MAMMAL`（哺乳类肢体贴图）。
- **语言**: `species_language_holder = vulpkanin`；描述/背景 placeholder。

---

### 12. Xeno / Xenohybrid（异形混血 / xeno.dm + xenohybrid.dm）

**文件**: `xeno.dm`（166 行，物种主体）+ `master_files/.../species_type/xenohybrid.dm`（4 行，仅 1 个心情事件）｜**ID**: `SPECIES_XENO`｜**显示名**: "Xenomorph Hybrid"｜**生物类型**: `MOB_ORGANIC | MOB_HUMANOID`
**简介**: 克隆事故产物——人类与异形（Xenomorph）DNA 的混合体。`isxenohybrid(A)` 宏 = 判断 `is_species(A, /datum/species/xeno)`，即 **xenohybrid 就是 xeno 物种**。

### inherent_traits（4 条，全量）

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械 | |
| `TRAIT_CAN_STRIP` | 可脱衣 | 可脱他人衣物 | |
| `TRAIT_LITERATE` | 识字 | 能阅读书写 | |
| `TRAIT_MUTANT_COLORS` | 突变色 | 三色自定义配色（紫蓝系） | |

### 动态特质 / 机制（NOVA 特色核心 ★）

- **等离子容器** ★（`/obj/item/organ/alien/plasmavessel/roundstart`）: 储 55 等离子、回复率 2/秒、自愈率 1.5；技能 = **种植杂草（plant weeds，0.5 秒建造）** + **等离子转移（transfer）**。
- **树脂纺丝器** ★（`/obj/item/organ/alien/resinspinner/roundstart`）: 技能 = **建造树脂结构**（0.5 秒建造）——开局异形混血可造巢穴/树脂墙。
- **肝可处理等离子** ★（`/obj/item/organ/liver/xeno_hybrid`）: 对等离子毒素 `toxpwr = 0`（完全无害，还能代谢）。
- **异形血**: `exotic_bloodtype = BLOOD_TYPE_XENO`；**怕热**: `heatmod = 2.5`（2.5 倍热伤害）★。
- **异形肢体**: 全身 xenohybrid 专属肢体，**腿为趾行（digitigrade）**。
- **死亡特效**: `death_sound = hiss6.ogg`（嘶嘶声）、`gib_anim = "gibbed-a"`；**gib/dust 覆写** ★: 死亡碎尸用**异形碎尸特效**（`gibspawner/xeno`），化成灰则留**异形遗骸**（`remains/xeno`）——异形混血死后留下异形残骸。
- **肉/皮**: 异形肉（`meat/slab/xeno`）+ 异形皮（`animalhide/xeno`）。
- **传家宝**: 鲁尼玩偶 + 抱脸虫玩具面具（family heirlooms）。
- **默认突变部件**: 异形尾 "Xenomorph Tail"、背管 "Xenodorsal Standard"（随机）、异形头 "Xenohead Standard"（随机）、**趾行腿**、无角。
- **perks（2 个）**: 1 正（biohazard，等离子能力）、1 负（fire，怕火）。
- **xenohybrid.dm（master_files）**: 仅定义心情事件 `xenohybrid_resin`——**站在树脂上心情 +2**（"I like being on resin. It calms my body and soul."，离开 15 秒后消退）。

> 核心发现：异形混血 = **完整异形器官套（等离子容器+树脂纺丝器）+ 等离子免疫肝 + 异形死亡特效**的开局可玩异形，是 NOVA 特色最鲜明的战斗向物种之一。

---

### 13. Snail（蜗牛人 NOVA 覆写 / master_files species_type/snail.dm）

**文件**: `modular_nova/master_files/code/modules/mob/living/carbon/human/species_type/snail.dm`（179 行）｜**基类**: `/datum/species/snail`（基础批 2 已收录）
**定位**: NOVA 对基础蜗牛人的器官/装备层覆写，**不新增 inherent_traits**（基础 3 条：`TRAIT_MUTANT_COLORS` / `TRAIT_NO_SLIP_ALL` / `TRAIT_WATER_BREATHING` 不变）。

### inherent_traits（继承基础 3 条，NOVA 未改动）

| 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|
| `TRAIT_MUTANT_COLORS` | 突变色 | 三色自定义配色 | （基础继承） |
| `TRAIT_NO_SLIP_ALL` | 永不滑倒 | 免疫一切滑倒 | （基础继承） |
| `TRAIT_WATER_BREATHING` | 水中呼吸 | 可在水下呼吸（NOVA 为开局蜗牛新增） | （基础继承） |

### 动态特质 / 覆写行为（NOVA 特色 ★）

- **蜗牛肝** ★（`/obj/item/organ/liver/snail`）: 更好的解毒肝（注释：thematic，处理毒素更强）。
- **蜗牛心** ★（`/obj/item/organ/heart/snail`）: 提供**贝壳背后减伤**（背后受击伤害降低）+ 更耐用的心脏。
- **贝壳背包系统** ★（`/obj/item/storage/backpack/snail`）: 
  - 基础: `slowdown = 6`（**背壳减速 6 倍**）、`IMMUTABLE_SLOW`（锁定减速）、`ABOVE_BODY_FRONT_LAYER`（覆盖尾巴层）、30 格容量。
  - **11 种贝壳皮肤**（`/datum/atom_skin/snail_backpack`）: 锥壳/圆壳/肉桂壳/焦糖壳/机甲壳/金字塔壳/象牙金字塔/螺旋壳/象牙螺旋/岩石壳/象牙岩石。
  - **蓝空间核心升级** ★: 插入蓝空间异常核心 → 变"**Snail Shell of Holding 空间袋贝壳**"（容量 35、袋中袋存储类型、蓝色发光轮廓、30 槽）。
- **on_species_gain**: 刷新图标；**perks（8 个）**: 5 正 3 负（home/酒/心/水/骨 正面；crutch/frown/skull 负面）。
- **预览**: 突变色固定 `#adaba7` 灰。

> 核心发现：NOVA 蜗牛 = 基础蜗牛（不滑倒+水中呼吸）+ **贝壳背包体系（11 皮肤 + 蓝空间升级为空间袋）** + 背后减伤心脏；特质层无改动，全部在器官/装备层。

---

### 14. Synthetic（合成人 / synths synthetic.dm）

**文件**: `modular_nova/modules/synths/code/species/synthetic.dm`（309 行）｜**ID**: `SPECIES_SYNTH`｜**显示名**: "Synthetic Humanoid"｜**生物类型**: `MOB_ROBOTIC | MOB_HUMANOID`
**简介**: 机器人人形（前 IPC 体系），机油血液、机器器官、EMP/EMAG 会"失魂"。

### inherent_traits（14 条，全量）

| # | 特质 | 中文名 | 含义/效果 | 独有 |
|---|---|---|---|---|
| 1 | `TRAIT_CAN_STRIP` | 可脱衣 | 可脱他人衣物 | |
| 2 | `TRAIT_ADVANCEDTOOLUSER` | 高级工具使用者 | 可操作复杂机械 | |
| 3 | `TRAIT_RADIMMUNE` | 辐射免疫 | 不受辐射伤害 | 合成系共享 |
| 4 | `TRAIT_NOBREATH` | 不呼吸 | 无需呼吸 | 合成系共享 |
| 5 | `TRAIT_TOXIMMUNE` | 毒素免疫 | 毒素伤害无效 | 合成系共享 |
| 6 | `TRAIT_GENELESS` | 无基因 | 无基因可提取/改造 | 合成系共享 |
| 7 | `TRAIT_STABLEHEART` | 稳定心脏 | 心脏不受致命伤影响 | 合成系共享 |
| 8 | `TRAIT_LIMBATTACHMENT` | 断肢可装回 | 断肢可手动插回 | 合成系共享 |
| 9 | `TRAIT_NO_HUSK` | 不可剥壳 | 不会被吸成干尸 | 合成系共享 |
| 10 | `TRAIT_OXYIMMUNE` | 缺氧免疫 | 不受缺氧伤害 | ★（本批唯一） |
| 11 | `TRAIT_LITERATE` | 识字 | 能阅读书写 | |
| 12 | `TRAIT_NOCRITDAMAGE` | 无重伤 | 无传统重伤判定（自处理重伤逻辑） | 合成系共享 |
| 13 | `TRAIT_ROBOTIC_DNA_ORGANS` | 机器人 DNA 器官 | 器官以机器人形式从 DNA 生成 | ★（合成人标志） |
| 14 | `TRAIT_SYNTHETIC` | 合成体 | 机器人身份（EMP/逻辑相关） | 合成系共享 |

### 动态特质 / 机制（NOVA 特色核心 ★）

- **全套机器器官**: 脑/胃/耳/舌/眼/肺/心/肝全部 `/synth` 版，**无阑尾**；`exotic_bloodtype = BLOOD_TYPE_OIL`（**机油血液**）；`reagent_flags = PROCESS_SYNTHETIC`（合成代谢）。
- **温度特性**: `coldmod = 1.2`（略怕冷）、`heatmod = 2`（**2 倍热伤害**——岩浆秒杀）、`siemens_coeff = 1`（电击正常）。
- **过热濒死** ★（on_life 注册 COMSIG_LIVING_LIFE）: 进入软/硬濒死时**每秒 +1 灼烧 + 体温 +13K**（过热自毁），10% 概率冒火花警告。
- **EMAG 失魂** ★（`COMSIG_ATOM_EMAG_ACT`）: 被 EMAG 电击后：40% 概率进入**强制离子法律播报**（forced_speech 3~5 次，每次随机 5~25 秒念一条离子法）；60% 概率**直接说反派台词**（"I AM +NOT+ A CYBORG YOU TROGLODYTE." 等 6 句）——合成人被 EMAG 后"发疯"。
- **复活机制** ★（`spec_revival`）: 复活时屏幕切 "Console"，5 秒后恢复保存的屏幕；播放 chime 音效。
- **屏幕系统** ★: 有屏幕的合成人获得 `/datum/action/innate/monitor_change/screen`（换屏技能）；**死亡 → BSOD 蓝屏 5 秒 → 黑屏**（`bsod_death`）。
- **sing_tones 技能**: 获得唱歌动作（合成人特色）。
- **左臂电源线**: `mutant_organs = /obj/item/organ/cyberimp/arm/toolkit/power_cord/left_arm`（可插电充电）。
- **`allows_food_preferences() = FALSE`**: 不能选食物偏好。
- **perks（4 个）**: 2 正（robot/star-of-life）+ 1 中性（robot）+ 1 正（music）。
- **换装保护**（pre/post_equip_outfit）: 防换装强制破坏背部 MOD 套服（与 protean 类似机制）。

---

## 附录 A · Ramatae 与 Placeholder Helper 定位说明

### Ramatae（拉马坦）
父任务点名文件，但**全仓不存在独立 `/datum/species/ramatae` 物种定义**。实际构成：
- `sprite_accessories/species/ramatae.dm`: **纯外观配件**——拉马坦耳（标准/高耳）、腹部花纹（simple light belly）等 sprite 定义（`/datum/sprite_accessory/...`、`/datum/body_marking/...`）。
- `modules/ramatae/code/`: 语言（`/datum/language/ramatae` + 语言持有者）与表情动作。
- `modules/bodyparts/code/ramatae_bodyparts.dm`: 拉马坦肢体贴图（`limb_id = SPECIES_RAMATAE`，无独立特质）。

→ 结论：Ramatae 是**哺乳类（mammal）的外观/语言变体**，无独立 inherent_traits；其特质即 mammal 模板特质（批4 已收录）。

### Placeholder Helper（`placeholder_helper.dm`）
非物种文件，仅向基类 `/datum/species` 追加 3 个变量：`placeholder_description`（占位描述）、`placeholder_lore`（占位背景）、`payday_modifier = 1.0`。供各物种 `get_species_description()` 返回占位文案使用（本批 skrell/tajaran/unathi/vox/vulpkanin/xeno 均调用）。

---

## 附录 B · 本批统计

| 物种 | 文件 | inherent_traits 数 | 动态特质 | 独有发现 |
|---|---|---|---|---|
| Mushpeople | mushpeople.dm（覆写） | 4（继承，未改动） | 菌盖强制（无盖剔除） | "必须长菌盖"外观规则 |
| Podweak | podweak.dm | 基类 5 / 子类 4 | 光合营养+光下自愈+饥饿扣血 | 子类去掉 PLANT_SAFE、不可自定义 |
| Protean | protean_species.dm | **22** | 纳米肢体/器官同化/MOD 套服/7 verb | NODEATH+铁血+食石+怕电 1.5 倍 |
| Roundstart Slime | roundstartslime.dm | 3（继承） | 4 技能（变形/清洗/疏水/核心信号）+被动再生+怕水 | 核心弹射复活、等离子治疗、半透明 155、水呼吸反转 |
| Shadekin | shadekin.dm | 8 | 暗色钳制（亮度≤35） | 夜视+不呼吸+水中呼吸+湿滑四合一 |
| Skrell | skrell.dm | 4 | 两栖肺（耐等离子/CO2）+体温+70K | 冷空气受伤、18 语言声囊舌 |
| Tajaran | tajaran.dm | 7 | 猫舌 | 猫三件套（优雅/怕水/被狗讨厌）独立物种化 |
| Unathi | unathi.dm | 4 | 乌纳希舌 | 蜥蜴肢体+内脏食性（特质层无耐温） |
| Vampire | vampire.dm（覆写） | 3（继承，未改动） | 预览跳过 | 无新增，仅跳过父类预览 |
| Vox | vox.dm | 4 | 氮气肺+皮质脑+专属装备/图标 | 氮气呼吸、耐寒怕热、不可义体化 |
| Vulpkanin | vulpkanin.dm | 4 | 狐舌 | 柯基肉、狐三件套外观 |
| Xeno/Xenohybrid | xeno.dm + xenohybrid.dm | 4 | 等离子容器+树脂纺丝+等离子肝+异形死亡特效 | 完整开局异形、heatmod 2.5 怕火、树脂心情+2 |
| Snail（覆写） | master_files species_type/snail.dm | 3（继承，未改动） | 贝壳背包（11 皮肤+蓝空间升级） | 背后减伤心、空间袋贝壳、减速 6 |
| Synthetic | synths/code/species/synthetic.dm | **14** | 过热自毁/EMAG 失魂/屏幕系统/复活 | 机油血、离子法律播报、BSOD 死亡 |

**本批关键结论**:

1. **合成系三巨头特质高度重叠**（Protean/Synthetic 共享 NOBREATH、TOXIMMUNE、RADIMMUNE、GENELESS、STABLEHEART、LIMBATTACHMENT、NO_HUSK、NOCRITDAMAGE、SYNTHETIC 等），但定位迥异：Protean = **纳米机群不死者**（NODEATH + 食石 + 器官同化），Synthetic = **可被 EMAG 黑化的机器人**（离子法/反叛台词 + 屏幕系统）。
2. **最复杂物种 = Roundstart Slime**（1,239 行）：核心弹射复活闭环 + 4 技能 + 怕水/怕冷 + 等离子治疗，是 NOVA 果冻人体系的完全重构；Protean（312 行）以 NODEATH+MOD 套服壳体同样复杂度极高。
3. **Xeno/Xenohybrid 是"开局可玩异形"**：等离子容器+树脂纺丝器让玩家能造巢穴，等离子免疫肝 + heatmod 2.5 怕火构成鲜明强弱曲线；`isxenohybrid` 宏确认其与 xeno 为同一物种。
4. **覆写类文件（mushpeople/vampire/snail）特质层零改动**：全部差异在器官/装备/外观层（蜗牛贝壳背包、吸血鬼预览、蘑菇菌盖强制），印证 NOVA 倾向用器官与物品系统而非特质系统实现物种差异。
5. **本批独有特质**：`TRAIT_ROCK_EATER`（Protean 食石）、`TRAIT_NO_DNA_SCRAMBLE`、`TRAIT_NO_PLASMA_TRANSFORM`、`TRAIT_RDS_SUPPRESSED`、`TRAIT_MADNESS_IMMUNE`、`TRAIT_OXYIMMUNE`（Synthetic）、`TRAIT_NIGHT_VISION`（Shadekin）、`TRAIT_SYNTHETIC`/`TRAIT_ROBOTIC_DNA_ORGANS`（合成系）、猫三件套（Tajaran）。

### 一、Spirit / Ghost 幽灵（ghost.dm）

### 1.1 `/datum/species/spirit`（Spirit 灵魂，幽灵基类）

**inherent_traits（10 条，完整）：**

| # | 特质 | 中文名/含义 | 属性 |
|---|---|---|---|
| 1 | `TRAIT_GENELESS` | 无基因：没有可提取/操作的基因，基因改造无效 | 亡灵系共享 |
| 2 | `TRAIT_NEVER_WOUNDED` | 永不受伤：不会产生任何肢体伤口（与丧尸的 EASILY_WOUNDED 相反） | 亡灵系共享 |
| 3 | `TRAIT_NOBLOOD` | 无血：没有血液，不流血、不贫血 | 亡灵系共享 |
| 4 | `TRAIT_NODISMEMBER` | 不可肢解：肢体不会被砍断 | 亡灵系共享 |
| 5 | `TRAIT_NO_DNA_COPY` | 无法复制 DNA：不能被克隆/测序 | 亡灵系共享 |
| 6 | `TRAIT_NO_PLASMA_TRANSFORM` | 免疫等离子转化：不会被等离子体污染/变异 | 幽灵独有 |
| 7 | `TRAIT_NO_UNDERWEAR` | 不穿内衣：无内衣槽位/不生成内衣 | 亡灵系共享 |
| 8 | `TRAIT_UNHUSKABLE` | 不可剥壳：不会被吸干成干尸（husk） | 亡灵系共享 |
| 9 | `TRAIT_NO_FLOATING_ANIM` | 无漂浮动画：正常站立姿态（幽灵日常形态） | 幽灵独有 |
| 10 | `TRAIT_MOVE_FLYING` | 飞行移动：悬空移动，不受重力/地面限制 | 幽灵独有 |

**其他关键定义：**
- `inherent_biotypes = MOB_SPIRIT | MOB_UNDEAD`（灵体 + 不死）
- `no_equip_flags = ITEM_SLOT_FEET`：**不能穿鞋**（无腿）
- `sexes = FALSE`：无性别
- `meat = /obj/item/ectoplasm`：肉=灵质（ectoplasm）
- 全身幽灵变异器官（brain/eyes/ears/lungs/liver/stomach/tongue 均为 `/ghost` 版），无心脏/阑尾
- 躯干只覆盖头/胸/双臂（**无腿 bodypart**）
- `halloween_exclusive = TRUE`：万圣节限定

### 1.2 `/datum/species/spirit/ghost`（Ghost 幽灵，可穿墙形态）

**inherent_traits（11 条 = 继承 spirit 全部 10 条 + 1 条独有）：**

| # | 独有特质 | 中文名/含义 |
|---|---|---|
| 11 | `TRAIT_SEE_BLESSED_TILES` | 看见祝福地块：能看见牧师布置的祝福地板（用于规避陷阱） |

**动态特质（on_species_gain 时获得）：**
- 赋予 `passthrough_ability`（`/datum/action/innate/toggle_passthrough` 穿墙切换技能）
- 开启穿墙模式（swap_mode）时**临时添加**以下特质（关闭时移除）：

| 动态特质 | 中文名/含义 |
|---|---|
| `TRAIT_MOVE_PHASING` | 穿墙移动：可穿过任何固体（幽灵独有核心能力） |
| `TRAIT_PIERCEIMMUNE` | 穿刺免疫：子弹/穿刺伤害无效 |
| `TRAIT_INVISIBLE_TO_CAMERA` | 摄像头不可见：监控拍不到 |
| `TRAIT_HANDS_BLOCKED` | 手被封锁：无法使用物品/拾取（MOBILITY_USE\|PICKUP\|STORAGE） |
| `TRAIT_PULL_BLOCKED` | 拖拽被封锁：无法拖拽（MOBILITY_PULL） |
| `TRAIT_UI_BLOCKED` | UI 被封锁：无法使用界面/机台（MOBILITY_UI） |

> 注：开启穿墙时**移除** `TRAIT_NO_FLOATING_ANIM`（进入漂浮幽灵姿态）；被圣水泼中/站在祝福地块上时技能不可用；非幽灵肢体/器官会在开启时全部掉落。万圣节专属开关 `halloween_exclusive = FALSE`（幽灵本体可通过魔法镜/管理员获得）。

---

### 二、Shadow 暗影人（shadowpeople.dm）

### `/datum/species/shadow`（Shadow，暗影人）

**inherent_traits（6 条，完整）：**

| # | 特质 | 中文名/含义 | 属性 |
|---|---|---|---|
| 1 | `TRAIT_NOBREATH` | 不呼吸：无需呼吸/无窒息伤害 | 亡灵系共享 |
| 2 | `TRAIT_RADIMMUNE` | 辐射免疫：不受辐射伤害 | 亡灵系共享 |
| 3 | `TRAIT_VIRUSIMMUNE` | 病毒免疫：不会被病毒感染 | 暗影独有（本批仅此一家） |
| 4 | `TRAIT_NOBLOOD` | 无血：没有血液 | 亡灵系共享 |
| 5 | `TRAIT_NODISMEMBER` | 不可肢解 | 亡灵系共享 |
| 6 | `TRAIT_NEVER_WOUNDED` | 永不受伤：无肢体伤口 | 亡灵系共享 |

**动态机制（非特质但核心）：**
- `inherent_factions = FACTION_FAITHLESS`：无信仰阵营
- 注册 `COMSIG_MOB_FLASH_OVERRIDE_CHECK`：**闪光弹/闪光灯对其造成烧伤**（满闪 16 BURN + 3~6 秒混乱；非满闪 8 BURN + 1~3 秒混乱）并强制尖叫——暗影人怕光的独特弱点
- `mutanteyes = /obj/item/organ/eyes/shadow`：燃烧红眼，**暗视**（黑暗中视如白昼），闪光保护敏感级
- `mutantbrain = /obj/item/organ/brain/shadow`：暗影肿瘤（不可逆改造）
- 无心脏、无肺（`mutantheart = null`、`mutantlungs = null`）
- `meat = /obj/item/food/meat/slab/human/mutant/shadow`
- 万圣节限定（check_roundstart_eligible 仅万圣节 TRUE）

---

### 三、Skeleton 骷髅（skeletons.dm）

### `/datum/species/skeleton`（Spooky Scary Skeleton）

**inherent_traits（17 条，完整）：**

| # | 特质 | 中文名/含义 | 属性 |
|---|---|---|---|
| 1 | `TRAIT_EASYDISMEMBER` | 易肢解：肢体容易被打断 | 亡灵系共享（副作用） |
| 2 | `TRAIT_FAKEDEATH` | 假死：可装死骗过敌人 | 亡灵系共享 |
| 3 | `TRAIT_GENELESS` | 无基因 | 亡灵系共享 |
| 4 | `TRAIT_LIMBATTACHMENT` | 肢体可手动装回：断肢捡起来直接插回 | 亡灵系共享 |
| 5 | `TRAIT_NOBLOOD` | 无血 | 亡灵系共享 |
| 6 | `TRAIT_NOBREATH` | 不呼吸 | 亡灵系共享 |
| 7 | `TRAIT_NO_DNA_COPY` | 无法复制 DNA | 亡灵系共享 |
| 8 | `TRAIT_NO_UNDERWEAR` | 不穿内衣 | 亡灵系共享 |
| 9 | `TRAIT_PIERCEIMMUNE` | 穿刺免疫：子弹无效 | 亡灵系共享（骷髅/幽灵/穿墙形态） |
| 10 | `TRAIT_RADIMMUNE` | 辐射免疫 | 亡灵系共享 |
| 11 | `TRAIT_RESISTCOLD` | 耐寒：低温环境不受伤 | 亡灵系共享 |
| 12 | `TRAIT_RESISTHEAT` | 耐热：高温环境不受伤 | 骷髅独有（本批） |
| 13 | `TRAIT_RESISTHIGHPRESSURE` | 耐高压：高压环境不受伤 | 亡灵系共享 |
| 14 | `TRAIT_RESISTLOWPRESSURE` | 耐低压：真空/低压环境不受伤 | 亡灵系共享 |
| 15 | `TRAIT_TOXIMMUNE` | 毒素免疫：毒物伤害无效 | 亡灵系共享 |
| 16 | `TRAIT_UNHUSKABLE` | 不可剥壳 | 亡灵系共享 |
| 17 | `TRAIT_XENO_IMMUNE` | 异形免疫：不会被异形感染/寄生 | 骷髅独有（本批） |

**其他关键定义：**
- `inherent_biotypes = MOB_UNDEAD | MOB_HUMANOID | MOB_SKELETAL`（不死+人形+骷髅）
- `inherent_factions = FACTION_SKELETON`：骷髅阵营
- 骨舌/骨胃/骨肝器官（tongue/stomach/liver 的 `/bone` 版），**无心脏、无肺、无阑尾**
- `species_cookie = /obj/item/reagent_containers/condiment/milk`：**喝牛奶（钙）治疗伤口**的机制来源
- 万圣节限定

---

### 四、Vampire 吸血鬼（vampire.dm）

### `/datum/species/human/vampire`（Vampire）

**inherent_traits（3 条，完整）：**

| # | 特质 | 中文名/含义 | 属性 |
|---|---|---|---|
| 1 | `TRAIT_BLOOD_CLANS` | 血族氏族：可加入吸血鬼氏族体系（每族 5 人，VAMPIRES_PER_HOUSE） | 吸血鬼独有 |
| 2 | `TRAIT_USES_SKINTONES` | 使用肤色：走肤色系统（获得时强制设为 albino 白化） | 吸血鬼独有 |
| 3 | `TRAIT_NO_MIRROR_REFLECTION` | 镜子无倒影：照镜子看不到自己（经典吸血鬼特征） | 吸血鬼独有 |

**器官附带特质（vampire 舌，organ_traits）：**

| 器官特质 | 中文名/含义 |
|---|---|
| `TRAIT_DRINKS_BLOOD` | 喝血：可通过吸血鬼舌吸血（Drain Victim 技能，3 秒冷却，每次最多 50u VAMP_DRAIN_AMOUNT） |
| `TRAIT_NOBREATH` | 不呼吸（与亡灵系共享） |
| `TRAIT_NOHUNGER` | 不饥饿：无需进食（亡灵系共享） |

**动态机制（on_life 每 tick）：**
- **棺材外**：持续失血 `0.125/s`；血低于存活阈值（BLOOD_VOLUME_SURVIVE）→ **化灰（dust）**
- **棺材内**：每秒回 2 点 brute/burn + 2 点 tox/oxy（`heal_overall_damage` 2/s × 各类型）
- **教堂区域**：每秒 10 点火焰伤害 + 3 点火苗堆积 + 点燃（`ignite_mob`）→ 圣域弱点
- 血 HUD：始终显示当前血量（on_hud_created 添加 blood_level 屏幕对象）
- `exotic_bloodtype = BLOOD_TYPE_VAMPIRE`：特殊吸血鬼血型；`blood_deficiency_drain_rate` 失血速率加成
- `mutantheart = /obj/item/organ/heart/vampire`、`mutanttongue = /obj/item/organ/tongue/vampire`
- `inherent_biotypes = MOB_UNDEAD | MOB_HUMANOID`（不死+人形）；`examine_limb_id = SPECIES_HUMAN`（外观同人类）
- 万圣节限定

---

### 五、Zombie 丧尸家族（zombies.dm，重点）

丧尸家族共 **4 个物种定义**，特质按"全丧尸共享 / 亚种独有"分层，是本批最复杂的结构。

### 5.1 共享特质（"SHARED WITH ALL ZOMBIES"，16~17 条）

以下特质出现在**所有丧尸物种**（高智商 + 感染性）的 inherent_traits 中：

| # | 特质 | 中文名/含义 | 出现范围 |
|---|---|---|---|
| 1 | `TRAIT_BLOODY_MESS` | 血腥邋遢：走哪都留血、喷血 | 全丧尸 |
| 2 | `TRAIT_EASILY_WOUNDED` | 易受伤：更容易产生伤口（与幽灵的 NEVER_WOUNDED 相反） | 全丧尸 |
| 3 | `TRAIT_EASYDISMEMBER` | 易肢解 | 全丧尸 |
| 4 | `TRAIT_FAKEDEATH` | 假死 | 全丧尸 |
| 5 | `TRAIT_LIMBATTACHMENT` | 肢体可手动装回 | 全丧尸 |
| 6 | `TRAIT_LIVERLESS_METABOLISM` | 无肝代谢：没有肝脏也能正常代谢，药物代谢异常 | 全丧尸 |
| 7 | `TRAIT_NOBREATH` | 不呼吸 | 全丧尸 |
| 8 | `TRAIT_NODEATH` | **不死**：不会真正死亡，生命值归零不进入死亡态（丧尸核心） | 全丧尸 |
| 9 | `TRAIT_NOCRITDAMAGE` | 无重伤伤害：不会进入濒死（crit）状态 | 全丧尸 |
| 10 | `TRAIT_NOHUNGER` | 不饥饿 | 全丧尸 |
| 11 | `TRAIT_NO_DNA_COPY` | 无法复制 DNA | 全丧尸 |
| 12 | `TRAIT_RADIMMUNE` | 辐射免疫 | 全丧尸 |
| 13 | `TRAIT_RESISTCOLD` | 耐寒 | 全丧尸 |
| 14 | `TRAIT_RESISTHIGHPRESSURE` | 耐高压 | 全丧尸 |
| 15 | `TRAIT_RESISTLOWPRESSURE` | 耐低压 | 全丧尸 |
| 16 | `TRAIT_TOXIMMUNE` | 毒素免疫 | 全丧尸 |
| 17 | `TRAIT_NO_ZOMBIFY` | 免疫丧尸化：不会被二次感染成丧尸 | **仅高智商丧尸**（见 5.2） |

> 注：`TRAIT_NO_ZOMBIFY` 在源文件中位于"SHARED"注释块，但**感染性丧尸不继承**（感染性丧尸本身就是感染源，其列表不含此条）——这是丧尸家族最重要的亚种差异。

### 5.2 `/datum/species/zombie`（High-Functioning Zombie 高智商丧尸）

**inherent_traits（20 条 = 17 条共享 + 2 条独有）：**

| # | 独有特质 | 中文名/含义 |
|---|---|---|
| 18 | `TRAIT_NOBLOOD` | 无血：不流血（高智商丧尸版"稳定心脏"方案） |
| 19 | `TRAIT_SUCCUMB_OVERRIDE` | 屈服覆盖：覆盖"投降/放弃"判定，不会因伤势过重而屈服倒下 |

**其他关键定义：**
- 变异器官全空：`mutantstomach/heart/liver/lungs = null`（**无胃无心无肝无肺**）
- 体温机制：`bodytemp_normal = T0C`（无体温）、`bodytemp_heat_damage_limit = 火焰最低温度`（到火温才受伤）、`bodytemp_cold_damage_limit = 最低移动温度`；重写 `body_temperature_core()` **完全不调节体温**（冷血行尸）
- `inherent_biotypes = MOB_UNDEAD | MOB_HUMANOID`
- 全身丧尸肢体（head/chest/arm/leg 的 `/zombie` 版）
- `meat = /obj/item/food/meat/slab/human/mutant/zombie`
- 万圣节限定

### 5.3 `/datum/species/zombie/infectious`（Infectious Zombie 感染性丧尸）

**inherent_traits（19 条 = 16 条共享 + 3 条独有）：**

| # | 独有特质 | 中文名/含义 |
|---|---|---|
| 17 | `TRAIT_STABLEHEART` | 稳定心脏：可以流血（有血液循环），但不需要心脏存活（替代 NOBLOOD 方案） |
| 18 | `TRAIT_STABLELIVER` | 稳定肝脏：不需要肝脏也能正常代谢（与 STABLEHEART 配套） |
| 19 | `TRAIT_APATHETIC` | 冷漠：没有心情/情绪系统（"脑子不够用"） |

**动态特质（on_species_gain 添加）：**
- `TRAIT_COMBAT_MODE_LOCK`：**战斗模式锁定**（无法退出战斗模式，强制攻击姿态）
- `physiology.stamina_mod *= 0.33`：耐力消耗仅 1/3（耐力抗性）
- `spec_stun()` 覆盖：**眩晕上限 2 秒**（`min(2 SECONDS, amount)`）

**动态组件（on_species_gain 添加）：**
- `/datum/component/mutant_hands`：丧尸手爪（`/obj/item/mutant_hand/zombie`，替代普通手）
- `/datum/component/regenerator`：**自动再生**——每 6 秒（regen_time）回复 brute/burn/tox 各 0.5（regen_amount）+ oxy 0.25，且可治愈伤口（heals_wounds = TRUE）
- `/obj/item/organ/zombie_infection`：感染器官（丧尸化感染源，跨物种转换保留）

**其他关键定义：**
- `damage_modifier = 20`：**需 120 点伤害才能 KO**（致死门槛极高）
- `mutanteyes = /obj/item/organ/eyes/zombie`、`mutantbrain = /obj/item/organ/brain/zombie`（感染脑）
- 腿部为 `/infectious` 慢腿版 bodypart
- **不可开局选择**（check_roundstart_eligible = FALSE）

### 5.4 `/datum/species/zombie/infectious/mindless`（Mindless 无脑感染丧尸）

继承感染性丧尸全部特质，弱化版：
- `regen_time = 10 SECONDS`（再生变慢）、`regen_amount = 0.2`（再生量减半以上）
- `zombie_hand = /obj/item/mutant_hand/zombie/weak`（弱手爪）
- **动态减速**：on_species_gain 添加 `/datum/movespeed_modifier/mindless_zombie`（`multiplicative_slowdown = 0.75`，移动速度 ×0.75 的减速修正）

### 5.5 `/datum/species/human/krokodil_addict`（Krokodil Human 鳄鱼毒瘾者）

- **inherent_traits：无**（0 条，纯外观物种）
- 仅替换为丧尸外观肢体（head/chest/arm/leg 的 `/zombie` 版），`examine_limb_id = SPECIES_HUMAN`
- 机制上仍是人类（无 NODEATH 等），皮肤溃烂的毒品受害者外观

---

### 六、Mushroomperson 蘑菇人（mushpeople.dm）

### `/datum/species/mush`（Mushroomperson）

**inherent_traits（4 条，完整）：**

| # | 特质 | 中文名/含义 | 属性 |
|---|---|---|---|
| 1 | `TRAIT_MUTANT_COLORS` | 突变色：使用自定义突变配色系统 | 植物系共享（蘑菇/植物） |
| 2 | `TRAIT_NOBREATH` | 不呼吸 | 亡灵/植物系共享 |
| 3 | `TRAIT_NOFLASH` | 抗闪光：闪光弹/闪光灯无效 | 蘑菇独有（本批） |
| 4 | `TRAIT_NO_UNDERWEAR` | 不穿内衣 | 亡灵系共享 |

**其他关键定义：**
- `inherent_factions = FACTION_MUSHROOM`：蘑菇阵营
- `no_equip_flags`：**不能装备** 面具/外衣/手套/鞋/内衣（ITEM_SLOT_MASK \| OCLOTHING \| GLOVES \| FEET \| ICLOTHING）
- `heatmod = 1.5`：**火焰伤害 1.5 倍**（怕火）
- `mutantlungs = null`（无肺，配合 NOBREATH）；夜视蘑菇眼（`/obj/item/organ/eyes/night_vision/mushroom`）
- 蘑菇舌（`/obj/item/organ/tongue/mush`）；`mutant_organs`：蘑菇帽（`/obj/item/organ/mushroom_cap`，"Round"）
- **死亡孢子机制**：死亡时蘑菇帽喷出花粉粒子，45 秒后（MUSHROOM_SPORE_COOLDOWN）在原地生成一丛棕色蘑菇（`/obj/structure/glowshroom/brownshroom`）
- **动态武术**：on_species_gain 传授 `mushpunch` 武术（`/datum/martial_art/mushpunch`，锁定使用）
- 固定发色 `#FF4B19`（红色菌盖）

---

### 七、Podperson 植物人（podpeople.dm）

### `/datum/species/pod`（Podperson）

**inherent_traits（2 条，完整）：**

| # | 特质 | 中文名/含义 | 属性 |
|---|---|---|---|
| 1 | `TRAIT_MUTANT_COLORS` | 突变色：使用自定义突变配色 | 植物系共享 |
| 2 | `TRAIT_PLANT_SAFE` | 植物安全：对植物类威胁（植物毒/花粉/藤蔓类伤害）免疫 | 植物系独有 |

**其他关键定义：**
- `inherent_biotypes = MOB_ORGANIC \| MOB_HUMANOID \| MOB_PLANT`：**有机+人形+植物**（本批唯一非亡灵生物类型）
- `inherent_factions = FACTION_PLANTS, FACTION_VINES`：植物阵营 + 藤蔓阵营
- `exotic_bloodtype = BLOOD_TYPE_H2O`：**血液是水**
- `heatmod = 1.5`：火焰伤害 1.5 倍（"身体像火绒一样易燃"）
- 全身植物变异器官（brain/ears/eyes/heart/liver/lungs/stomach/tongue/appendix 均为 `/pod` 版）
- `mutant_organs`：植物发型（`/obj/item/organ/pod_hair`，"None"）
- 光/暗机制：**光照回血、黑暗枯萎**（与暗影人相反，perk 描述确认：heal in light / starve with the dark）
- `payday_modifier = 1.0`（薪资系数）；`meat = .../mutant/plant`（植物肉）
- 来源：复活舱（revival pod）复活突变的人类

---

### 八、Monkey 猴子（monkeys.dm）

### `/datum/species/monkey`（Monkey）

**inherent_traits（3 条，完整）：**

| # | 特质 | 中文名/含义 | 属性 |
|---|---|---|---|
| 1 | `TRAIT_NO_BLOOD_OVERLAY` | 无血迹覆盖层：身上不显示血迹贴图 | 猴子独有 |
| 2 | `TRAIT_NO_DNA_COPY` | 无法复制 DNA | 亡灵/猴子共享 |
| 3 | `TRAIT_NO_UNDERWEAR` | 不穿内衣 | 亡灵系共享 |

**动态特质（on_species_gain，仅 pref_load 时）：**
- `TRAIT_BORN_MONKEY`（源 INNATE_TRAIT）：**天生猴子**——"无法逃脱的基因命运"，人化后仍保留猴脑/猴特征（注释原话：Not a species trait, you cannot escape your genetic destiny）
- 添加 `/datum/mutation/race` 突变（种族突变，on_species_loss 时移除）

**大脑器官特质（`/obj/item/organ/brain/primate`，organ_traits 4 条）：**

| 器官特质 | 中文名/含义 |
|---|---|
| `TRAIT_CAN_STRIP` | 可扒衣：能扒下他人衣物 |
| `TRAIT_PRIMITIVE` | 原始：不识字、无法读写/使用复杂工具（"No literacy or advanced tool usage"） |
| `TRAIT_GUN_NATURAL` | 枪械亲和：用枪时减少卡壳/抖动惩罚（配合"聪明猴子能开枪"设定） |
| `TRAIT_WEAK_SOUL` | 灵魂虚弱：灵魂相关机制（附身/通灵）弱化 |

**其他关键定义：**
- `no_equip_flags`：不能装备 外衣/手套/鞋/西装口袋（ITEM_SLOT_OCLOTHING \| GLOVES \| FEET \| SUITSTORE）
- `inherent_factions = FACTION_MONKEY`：猴子阵营
- 猴尾器官（`/obj/item/organ/tail/monkey`）；猴舌；`skinned_type`：猴子皮（carbon/monkey）；`knife_butcher_results`：刀屠宰=5 猴肉+1 猴皮
- `species_cookie = /obj/item/food/grown/banana`：**香蕉**
- `payday_modifier = 1.5`：薪资 1.5 倍（"更灵巧，尤其是偷东西时"）
- `ai_controlled_species = TRUE`：AI 可控制
- **绊倒机制**：猴脑 `tripping` 默认 TRUE——战斗模式的碳基生物撞到猴子会把猴子撞倒（`knockOver`），有客户端玩家可通过 "Toggle Tripping" 动作关闭
- 攻击：非高级工具使用者/狂暴咬人者用**头槌**（get_attacking_limb 返回头部）；`human_biter` 元素（咬人）
- 猴日（Monkey Day）限定开局

---

### 九、跨物种特质汇总表

### 9.1 按共享度排序（出现 ≥2 物种的共享特质）

| 特质 | 中文 | 幽灵 | 暗影 | 骷髅 | 吸血鬼 | 丧尸 | 蘑菇 | 植物 | 猴子 |
|---|---|---|---|---|---|---|---|---|---|
| `TRAIT_NOBREATH` | 不呼吸 | ✗* | ✓ | ✓ | ✓(器官) | ✓ | ✓ | ✗ | ✗ |
| `TRAIT_NOBLOOD` | 无血 | ✓ | ✓ | ✓ | ✗ | ✓(仅高智商) | ✗ | ✗ | ✗ |
| `TRAIT_NO_DNA_COPY` | 无法复制 DNA | ✓ | ✗ | ✓ | ✗ | ✓ | ✗ | ✗ | ✓ |
| `TRAIT_NO_UNDERWEAR` | 不穿内衣 | ✓ | ✗ | ✓ | ✗ | ✗ | ✓ | ✗ | ✓ |
| `TRAIT_RADIMMUNE` | 辐射免疫 | ✗ | ✓ | ✓ | ✗ | ✓ | ✗ | ✗ | ✗ |
| `TRAIT_NODISMEMBER` | 不可肢解 | ✓ | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ | ✗ |
| `TRAIT_NEVER_WOUNDED` | 永不受伤 | ✓ | ✓ | ✗ | ✗ | ✗(反向易伤) | ✗ | ✗ | ✗ |
| `TRAIT_GENELESS` | 无基因 | ✓ | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ |
| `TRAIT_FAKEDEATH` | 假死 | ✗ | ✗ | ✓ | ✗ | ✓ | ✗ | ✗ | ✗ |
| `TRAIT_LIMBATTACHMENT` | 断肢可装回 | ✗ | ✗ | ✓ | ✗ | ✓ | ✗ | ✗ | ✗ |
| `TRAIT_EASYDISMEMBER` | 易肢解 | ✗ | ✗ | ✓ | ✗ | ✓ | ✗ | ✗ | ✗ |
| `TRAIT_RESISTCOLD` | 耐寒 | ✗ | ✗ | ✓ | ✗ | ✓ | ✗ | ✗ | ✗ |
| `TRAIT_RESISTHIGHPRESSURE` | 耐高压 | ✗ | ✗ | ✓ | ✗ | ✓ | ✗ | ✗ | ✗ |
| `TRAIT_RESISTLOWPRESSURE` | 耐低压 | ✗ | ✗ | ✓ | ✗ | ✓ | ✗ | ✗ | ✗ |
| `TRAIT_TOXIMMUNE` | 毒素免疫 | ✗ | ✗ | ✓ | ✗ | ✓ | ✗ | ✗ | ✗ |
| `TRAIT_PIERCEIMMUNE` | 穿刺免疫 | ✓(穿墙态) | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ |
| `TRAIT_UNHUSKABLE` | 不可剥壳 | ✓ | ✗ | ✓ | ✗ | ✗ | ✗ | ✗ | ✗ |
| `TRAIT_MUTANT_COLORS` | 突变色 | ✗ | ✗ | ✗ | ✗ | ✗ | ✓ | ✓ | ✗ |
| `TRAIT_NOHUNGER` | 不饥饿 | ✗ | ✗ | ✗ | ✓(器官) | ✓ | ✗ | ✗ | ✗ |

*幽灵=Spirit 基类，Ghost 子类另有 SEE_BLESSED_TILES；吸血鬼 NOBREATH/NOHUNGER 来自吸血鬼舌器官而非 inherent_traits。

### 9.2 独有特质清单（仅一个物种拥有）

| 特质 | 中文 | 拥有者 |
|---|---|---|
| `TRAIT_NO_PLASMA_TRANSFORM` | 免疫等离子转化 | 幽灵 Spirit |
| `TRAIT_NO_FLOATING_ANIM` | 无漂浮动画 | 幽灵 Spirit（穿墙时移除） |
| `TRAIT_MOVE_FLYING` | 飞行移动 | 幽灵 Spirit |
| `TRAIT_SEE_BLESSED_TILES` | 看见祝福地块 | 幽灵 Ghost 子类 |
| `TRAIT_MOVE_PHASING` | 穿墙移动 | 幽灵 Ghost（动态） |
| `TRAIT_INVISIBLE_TO_CAMERA` | 摄像头不可见 | 幽灵 Ghost（动态） |
| `TRAIT_HANDS_BLOCKED` | 手被封锁 | 幽灵 Ghost（动态） |
| `TRAIT_PULL_BLOCKED` | 拖拽封锁 | 幽灵 Ghost（动态） |
| `TRAIT_UI_BLOCKED` | UI 封锁 | 幽灵 Ghost（动态） |
| `TRAIT_VIRUSIMMUNE` | 病毒免疫 | 暗影 Shadow |
| `TRAIT_RESISTHEAT` | 耐热 | 骷髅 Skeleton |
| `TRAIT_XENO_IMMUNE` | 异形免疫 | 骷髅 Skeleton |
| `TRAIT_BLOOD_CLANS` | 血族氏族 | 吸血鬼 Vampire |
| `TRAIT_USES_SKINTONES` | 使用肤色 | 吸血鬼 Vampire |
| `TRAIT_NO_MIRROR_REFLECTION` | 镜子无倒影 | 吸血鬼 Vampire |
| `TRAIT_DRINKS_BLOOD` | 喝血 | 吸血鬼（舌器官） |
| `TRAIT_NODEATH` | 不死 | 丧尸（全系） |
| `TRAIT_NOCRITDAMAGE` | 无重伤 | 丧尸（全系） |
| `TRAIT_LIVERLESS_METABOLISM` | 无肝代谢 | 丧尸（全系） |
| `TRAIT_BLOODY_MESS` | 血腥邋遢 | 丧尸（全系） |
| `TRAIT_EASILY_WOUNDED` | 易受伤 | 丧尸（全系） |
| `TRAIT_NO_ZOMBIFY` | 免疫丧尸化 | 丧尸-高智商（感染性丧尸故意缺失） |
| `TRAIT_SUCCUMB_OVERRIDE` | 屈服覆盖 | 丧尸-高智商 |
| `TRAIT_STABLEHEART` | 稳定心脏 | 丧尸-感染性 |
| `TRAIT_STABLELIVER` | 稳定肝脏 | 丧尸-感染性 |
| `TRAIT_APATHETIC` | 冷漠（无情结） | 丧尸-感染性 |
| `TRAIT_COMBAT_MODE_LOCK` | 战斗模式锁定 | 丧尸-感染性（动态） |
| `TRAIT_NOFLASH` | 抗闪光 | 蘑菇 Mush |
| `TRAIT_PLANT_SAFE` | 植物安全 | 植物 Pod |
| `TRAIT_NO_BLOOD_OVERLAY` | 无血迹覆盖层 | 猴子 Monkey |
| `TRAIT_BORN_MONKEY` | 天生猴子 | 猴子 Monkey（动态） |
| `TRAIT_PRIMITIVE` | 原始（不识字） | 猴子（大脑器官） |
| `TRAIT_CAN_STRIP` | 可扒衣 | 猴子（大脑器官） |
| `TRAIT_GUN_NATURAL` | 枪械亲和 | 猴子（大脑器官） |
| `TRAIT_WEAK_SOUL` | 灵魂虚弱 | 猴子（大脑器官） |

---

### 十、关键结论

1. **丧尸是本批最复杂的特质体系**：4 个物种定义、19~20 条特质按"全丧尸共享 16~17 条 + 亚种独有"分层；`TRAIT_NODEATH`（不死）+ `TRAIT_NOCRITDAMAGE`（无重伤）+ `TRAIT_NOBREATH` 构成"打不死"铁三角，配合 `damage_modifier = 20`（120 伤害才 KO）、眩晕上限 2 秒、6 秒自动再生组件，是真正的"行尸"。
2. **感染性丧尸 vs 高智商丧尸的关键差异**：高智商用 `NOBLOOD`（不流血）方案且带 `NO_ZOMBIFY`（免疫二次感染）；感染性丧尸改走 `STABLEHEART/STABLELIVER`（可流血但器官非必需）方案、**故意去掉 NO_ZOMBIFY**（自身是感染源）、外加 `APATHETIC`（无情结）与动态 `COMBAT_MODE_LOCK`（强制战斗模式）。
3. **幽灵双形态**：Spirit 基类 10 特质（飞行、无腿、灵体器官）；Ghost 子类 +1（看见祝福地块）并拥有全游戏独一份的**动态穿墙特质组**（PHASING + PIERCEIMMUNE + 摄像头隐形 + 三封锁），代价是丢弃全部装备。
4. **亡灵系共享特征明显**：NOBREATH/NOBLOOD/辐射免疫/耐高低温压/毒素免疫/不可剥壳/不穿内衣等在多物种间复用；骷髅是"免疫最多"（17 条），暗影是"免疫最少但带闪光弱点"（6 条）。
5. **植物系（蘑菇/植物）**：走 `MUTANT_COLORS` + `heatmod 1.5`（怕火）路线，与亡灵系无交集（蘑菇抗闪光、植物免疫植物伤害）；植物人血液是水、生物类型为有机植物（非不死）。
6. **吸血鬼/猴子走"器官承载特质"模式**：吸血鬼 3 条 inherent + 舌器官 3 条；猴子 3 条 inherent + 猴脑器官 4 条（原始/扒衣/枪械亲和/灵魂虚弱）——特质不全在物种层，拆解时需连器官一起看。

## 六、特质全量总览（矩阵）

> **全库物种特质总表**：107 种特质 × 覆盖物种。按特质类别分组。
> 读法：TRAIT_X → 拥有该特质的物种（★ = 该特质主要意义所在）。

### 6.1 环境免疫类

| 特质 | 含义 | 覆盖物种 |
|---|---|---|
| TRAIT_CARPOTOXIN_IMMUNE | CARPOTOXIN_IMMUNE | 3. 猫人 Felinid（felinid.dm） |
| TRAIT_LAVA_IMMUNE | LAVA_IMMUNE | 6. Golem Weak（弱化魔像 / golemweak）, 7. 魔像 Golem（SPECIES_GOLEM） |
| TRAIT_MADNESS_IMMUNE | MADNESS_IMMUNE | 3. Protean（液态金属纳米机群 / protean_species） |
| TRAIT_NOFIRE | NOFIRE | 5. 安卓 Android（SPECIES_ANDROID）, 6. Golem Weak（弱化魔像 / golemweak）, 7. 魔像 Golem（SPECIES_GOLEM） |
| TRAIT_OVERDOSEIMMUNE | OVERDOSEIMMUNE | 5. 安卓 Android（SPECIES_ANDROID） |
| TRAIT_OXYIMMUNE | OXYIMMUNE | 14. Synthetic（合成人 / synths synthetic.dm）, 7. Hemophage（血噬族 · 全套 / hemophage） |
| TRAIT_PIERCEIMMUNE | PIERCEIMMUNE | 11. Lizard（蜥蜴人修改 / lizard）, 2. 蜥蜴人 Lizardperson（lizardpeople.dm）, 5. 安卓 Android（SPECIES_ANDROID）, 6. Golem Weak（弱化魔像 / golemweak）… |
| TRAIT_RADIMMUNE | RADIMMUNE | 14. Synthetic（合成人 / synths synthetic.dm）, 3. Protean（液态金属纳米机群 / protean_species）, 5. Ghoul（食尸鬼 / ghoul）, 5. 安卓 Android（SPECIES_ANDROID）… |
| TRAIT_RDS_SUPPRESSED | RDS_SUPPRESSED | 3. Protean（液态金属纳米机群 / protean_species） |
| TRAIT_RESISTCOLD | RESISTCOLD | 5. 安卓 Android（SPECIES_ANDROID）, 6. 等离子人 Plasmaman（plasmamen.dm）, 三、Skeleton 骷髅（skeletons.dm）, 五、Zombie 丧尸家族（zombies.dm，重点） |
| TRAIT_RESISTHEAT | RESISTHEAT | 5. 安卓 Android（SPECIES_ANDROID）, 8. Unathi（蜥蜴族 / unathi）, 三、Skeleton 骷髅（skeletons.dm） |
| TRAIT_RESISTHIGHPRESSURE | RESISTHIGHPRESSURE | 11. Lizard（蜥蜴人修改 / lizard）, 2. 蜥蜴人 Lizardperson（lizardpeople.dm）, 5. 安卓 Android（SPECIES_ANDROID）, 7. 以太人 Ethereal（ethereal.dm）… |
| TRAIT_RESISTLOWPRESSURE | RESISTLOWPRESSURE | 11. Lizard（蜥蜴人修改 / lizard）, 2. 蜥蜴人 Lizardperson（lizardpeople.dm）, 5. 安卓 Android（SPECIES_ANDROID）, 7. 以太人 Ethereal（ethereal.dm）… |
| TRAIT_SNOWSTORM_IMMUNE | SNOWSTORM_IMMUNE | 6. Golem Weak（弱化魔像 / golemweak）, 7. 魔像 Golem（SPECIES_GOLEM） |
| TRAIT_TOXIMMUNE | TOXIMMUNE | 14. Synthetic（合成人 / synths synthetic.dm）, 3. Protean（液态金属纳米机群 / protean_species）, 5. 安卓 Android（SPECIES_ANDROID）, 三、Skeleton 骷髅（skeletons.dm）… |
| TRAIT_TOXINLOVER | TOXINLOVER | 2. 果冻 Jellyperson（SPECIES_JELLYPERSON）, 3. 史莱姆 Slimeperson（SPECIES_SLIMEPERSON）, 4. Roundstart Slime（开局史莱姆混血 / roundstartslime） |
| TRAIT_VIRUSIMMUNE | VIRUSIMMUNE | 11. Lizard（蜥蜴人修改 / lizard）, 2. 蜥蜴人 Lizardperson（lizardpeople.dm）, 3. Protean（液态金属纳米机群 / protean_species）, 4. 外星 Abductor（SPECIES_ABDUCTOR）… |
| TRAIT_XENO_IMMUNE | XENO_IMMUNE | 三、Skeleton 骷髅（skeletons.dm） |

### 6.2 生命维持类

| 特质 | 含义 | 覆盖物种 |
|---|---|---|
| TRAIT_AGEUSIA | AGEUSIA | 7. Hemophage（血噬族 · 全套 / hemophage） |
| TRAIT_LIVERLESS_METABOLISM | LIVERLESS_METABOLISM | 5. 安卓 Android（SPECIES_ANDROID）, 五、Zombie 丧尸家族（zombies.dm，重点） |
| TRAIT_NOBLOOD | NOBLOOD | 4. 外星 Abductor（SPECIES_ABDUCTOR）, 5. 安卓 Android（SPECIES_ANDROID）, 6. Golem Weak（弱化魔像 / golemweak）, 6. 等离子人 Plasmaman（plasmamen.dm）… |
| TRAIT_NOBREATH | NOBREATH | 1. Mushpeople（蘑菇人修改 / mushpeople）, 1. Rogue（叛逃者 / abductorweak）, 11. Lizard（蜥蜴人修改 / lizard）, 14. Synthetic（合成人 / synths synthetic.dm）… |
| TRAIT_NOCRITDAMAGE | NOCRITDAMAGE | 14. Synthetic（合成人 / synths synthetic.dm）, 5. 安卓 Android（SPECIES_ANDROID）, 6. Golem Weak（弱化魔像 / golemweak）, 7. 魔像 Golem（SPECIES_GOLEM）… |
| TRAIT_NODEATH | NODEATH | 3. Protean（液态金属纳米机群 / protean_species）, 五、Zombie 丧尸家族（zombies.dm，重点） |
| TRAIT_NOFAT | NOFAT | 6. Golem Weak（弱化魔像 / golemweak）, 7. 魔像 Golem（SPECIES_GOLEM） |
| TRAIT_NOHUNGER | NOHUNGER | 1. Rogue（叛逃者 / abductorweak）, 3. Protean（液态金属纳米机群 / protean_species）, 4. 外星 Abductor（SPECIES_ABDUCTOR）, 5. 安卓 Android（SPECIES_ANDROID）… |
| TRAIT_NO_DNA_COPY | NO_DNA_COPY | 13. Monkey（猴子修改 / monkey）, 5. 安卓 Android（SPECIES_ANDROID）, 6. Golem Weak（弱化魔像 / golemweak）, 6. 等离子人 Plasmaman（plasmamen.dm）… |
| TRAIT_NO_DNA_SCRAMBLE | NO_DNA_SCRAMBLE | 3. Protean（液态金属纳米机群 / protean_species） |
| TRAIT_STABLEHEART | STABLEHEART | 14. Synthetic（合成人 / synths synthetic.dm）, 3. Protean（液态金属纳米机群 / protean_species）, 五、Zombie 丧尸家族（zombies.dm，重点） |
| TRAIT_STABLELIVER | STABLELIVER | 五、Zombie 丧尸家族（zombies.dm，重点） |

### 6.3 生物/体型类

| 特质 | 含义 | 覆盖物种 |
|---|---|---|
| TRAIT_ADVANCEDTOOLUSER | ADVANCEDTOOLUSER | 1. Rogue（叛逃者 / abductorweak）, 10. Insectoid（虫族 / insectoid）, 10. Vox（鸟人 / vox）, 11. Vulpkanin（狐人 / vulpkanin）… |
| TRAIT_BORN_MONKEY | BORN_MONKEY | 八、Monkey 猴子（monkeys.dm） |
| TRAIT_CAN_STRIP | CAN_STRIP | 1. Rogue（叛逃者 / abductorweak）, 10. Insectoid（虫族 / insectoid）, 10. Vox（鸟人 / vox）, 11. Vulpkanin（狐人 / vulpkanin）… |
| TRAIT_CHUNKYFINGERS_IGNORE_BATON | CHUNKYFINGERS_IGNORE_BATON | 1. Rogue（叛逃者 / abductorweak）, 4. 外星 Abductor（SPECIES_ABDUCTOR） |
| TRAIT_FIXED_MUTANT_COLORS | FIXED_MUTANT_COLORS | 5. Ghoul（食尸鬼 / ghoul）, 7. 以太人 Ethereal（ethereal.dm） |
| TRAIT_GENELESS | GENELESS | 14. Synthetic（合成人 / synths synthetic.dm）, 3. Protean（液态金属纳米机群 / protean_species）, 5. 安卓 Android（SPECIES_ANDROID）, 6. Golem Weak（弱化魔像 / golemweak）… |
| TRAIT_LIMBATTACHMENT | LIMBATTACHMENT | 14. Synthetic（合成人 / synths synthetic.dm）, 3. Protean（液态金属纳米机群 / protean_species）, 5. 安卓 Android（SPECIES_ANDROID）, 三、Skeleton 骷髅（skeletons.dm）… |
| TRAIT_LITERATE | LITERATE | 1. Rogue（叛逃者 / abductorweak）, 10. Insectoid（虫族 / insectoid）, 10. Vox（鸟人 / vox）, 11. Vulpkanin（狐人 / vulpkanin）… |
| TRAIT_MUTANT_COLORS | MUTANT_COLORS | 1. Mushpeople（蘑菇人修改 / mushpeople）, 1. 蜗牛 Snailperson（SPECIES_SNAIL）, 10. Insectoid（虫族 / insectoid）, 10. Vox（鸟人 / vox）… |
| TRAIT_PRIMITIVE | PRIMITIVE | 八、Monkey 猴子（monkeys.dm） |
| TRAIT_USES_SKINTONES | USES_SKINTONES | 1. 人类 Human（humans.dm）, 3. 猫人 Felinid（felinid.dm）, 4. Dwarf（矮人 / dwarf）, 6. 无头 Dullahan（SPECIES_DULLAHAN）… |

### 6.4 特殊能力/限制类

| 特质 | 含义 | 覆盖物种 |
|---|---|---|
| TRAIT_ABDUCTOR_HUD | ABDUCTOR_HUD | 4. 外星 Abductor（SPECIES_ABDUCTOR） |
| TRAIT_AGENDER | AGENDER | 7. 以太人 Ethereal（ethereal.dm） |
| TRAIT_APATHETIC | APATHETIC | 五、Zombie 丧尸家族（zombies.dm，重点） |
| TRAIT_BLOODY_MESS | BLOODY_MESS | 五、Zombie 丧尸家族（zombies.dm，重点） |
| TRAIT_BLOOD_CLANS | BLOOD_CLANS | 9. Vampire（吸血鬼覆写 / vampire）, 四、Vampire 吸血鬼（vampire.dm） |
| TRAIT_BRAINLESS_CARBON | BRAINLESS_CARBON | 6. 无头 Dullahan（SPECIES_DULLAHAN） |
| TRAIT_CANNOT_CRYSTALIZE | CANNOT_CRYSTALIZE | 7. 以太人 Ethereal（ethereal.dm） |
| TRAIT_CATLIKE_GRACE | CATLIKE_GRACE | 3. 猫人 Felinid（felinid.dm）, 7. Tajaran（猫族 / tajaran） |
| TRAIT_COMBAT_MODE_LOCK | COMBAT_MODE_LOCK | 五、Zombie 丧尸家族（zombies.dm，重点） |
| TRAIT_CORPSELOCKED | CORPSELOCKED | 7. 以太人 Ethereal（ethereal.dm） |
| TRAIT_CRITICAL_CONDITION | CRITICAL_CONDITION | 3. Protean（液态金属纳米机群 / protean_species） |
| TRAIT_DRINKS_BLOOD | DRINKS_BLOOD | 7. Hemophage（血噬族 · 全套 / hemophage）, 四、Vampire 吸血鬼（vampire.dm） |
| TRAIT_DWARF | DWARF | 4. Dwarf（矮人 / dwarf） |
| TRAIT_EASILY_WOUNDED | EASILY_WOUNDED | 5. Ghoul（食尸鬼 / ghoul）, 五、Zombie 丧尸家族（zombies.dm，重点） |
| TRAIT_EASYDISMEMBER | EASYDISMEMBER | 3. Protean（液态金属纳米机群 / protean_species）, 4. Roundstart Slime（开局史莱姆混血 / roundstartslime）, 5. Ghoul（食尸鬼 / ghoul）, 三、Skeleton 骷髅（skeletons.dm）… |
| TRAIT_FAKEDEATH | FAKEDEATH | 三、Skeleton 骷髅（skeletons.dm）, 五、Zombie 丧尸家族（zombies.dm，重点） |
| TRAIT_FERAL_BITER | FERAL_BITER | 3. 猫人 Felinid（felinid.dm） |
| TRAIT_FISH_EATER | FISH_EATER | 3. 猫人 Felinid（felinid.dm） |
| TRAIT_GENERIC | GENERIC | 6. 无头 Dullahan（SPECIES_DULLAHAN） |
| TRAIT_GUN_NATURAL | GUN_NATURAL | 八、Monkey 猴子（monkeys.dm） |
| TRAIT_HANDS_BLOCKED | HANDS_BLOCKED | 一、Spirit / Ghost 幽灵（ghost.dm） |
| TRAIT_HARDLY_WOUNDED | HARDLY_WOUNDED | 6. 等离子人 Plasmaman（plasmamen.dm） |
| TRAIT_HATED_BY_DOGS | HATED_BY_DOGS | 3. 猫人 Felinid（felinid.dm）, 7. Tajaran（猫族 / tajaran） |
| TRAIT_HOLY | HOLY | 11. Lizard（蜥蜴人修改 / lizard）, 2. 蜥蜴人 Lizardperson（lizardpeople.dm） |
| TRAIT_INVISIBLE_TO_CAMERA | INVISIBLE_TO_CAMERA | 一、Spirit / Ghost 幽灵（ghost.dm） |
| TRAIT_MOVE_FLYING | MOVE_FLYING | 一、Spirit / Ghost 幽灵（ghost.dm） |
| TRAIT_MOVE_PHASING | MOVE_PHASING | 一、Spirit / Ghost 幽灵（ghost.dm） |
| TRAIT_NEVER_WOUNDED | NEVER_WOUNDED | 3. Protean（液态金属纳米机群 / protean_species）, 4. 外星 Abductor（SPECIES_ABDUCTOR）, 6. Golem Weak（弱化魔像 / golemweak）, 7. 魔像 Golem（SPECIES_GOLEM）… |
| TRAIT_NIGHT_VISION | NIGHT_VISION | 5. Shadekin（影狐 / shadekin） |
| TRAIT_NODISMEMBER | NODISMEMBER | 4. 外星 Abductor（SPECIES_ABDUCTOR）, 6. Golem Weak（弱化魔像 / golemweak）, 7. 魔像 Golem（SPECIES_GOLEM）, 一、Spirit / Ghost 幽灵（ghost.dm）… |
| TRAIT_NODROP | NODROP | 1. 蜗牛 Snailperson（SPECIES_SNAIL） |
| TRAIT_NOFLASH | NOFLASH | 1. Mushpeople（蘑菇人修改 / mushpeople）, 六、Mushroomperson 蘑菇人（mushpeople.dm） |
| TRAIT_NOSOFTCRIT | NOSOFTCRIT | 6. Golem Weak（弱化魔像 / golemweak）, 7. 魔像 Golem（SPECIES_GOLEM） |
| TRAIT_NO_AUGMENTS | NO_AUGMENTS | 6. Golem Weak（弱化魔像 / golemweak）, 7. 魔像 Golem（SPECIES_GOLEM） |
| TRAIT_NO_BLOOD_OVERLAY | NO_BLOOD_OVERLAY | 13. Monkey（猴子修改 / monkey）, 八、Monkey 猴子（monkeys.dm） |
| TRAIT_NO_BREATHLESS_DAMAGE | NO_BREATHLESS_DAMAGE | 2. Akula（阿库拉鲨人 / akula） |
| TRAIT_NO_FLOATING_ANIM | NO_FLOATING_ANIM | 一、Spirit / Ghost 幽灵（ghost.dm） |
| TRAIT_NO_HUSK | NO_HUSK | 14. Synthetic（合成人 / synths synthetic.dm）, 3. Protean（液态金属纳米机群 / protean_species） |
| TRAIT_NO_JUMPSUIT | NO_JUMPSUIT | 7. 魔像 Golem（SPECIES_GOLEM） |
| TRAIT_NO_MIRROR_REFLECTION | NO_MIRROR_REFLECTION | 9. Vampire（吸血鬼覆写 / vampire）, 四、Vampire 吸血鬼（vampire.dm） |
| TRAIT_NO_PLASMA_TRANSFORM | NO_PLASMA_TRANSFORM | 3. Protean（液态金属纳米机群 / protean_species）, 5. 安卓 Android（SPECIES_ANDROID）, 6. Golem Weak（弱化魔像 / golemweak）, 6. 等离子人 Plasmaman（plasmamen.dm）… |
| TRAIT_NO_SLIP_ALL | NO_SLIP_ALL | 1. 蜗牛 Snailperson（SPECIES_SNAIL）, 13. Snail（蜗牛人 NOVA 覆写 / master_files species_type/ |
| TRAIT_NO_UNDERWEAR | NO_UNDERWEAR | 1. Mushpeople（蘑菇人修改 / mushpeople）, 1. 蜗牛 Snailperson（SPECIES_SNAIL）, 13. Monkey（猴子修改 / monkey）, 4. 外星 Abductor（SPECIES_ABDUCTOR）… |
| TRAIT_NO_ZOMBIFY | NO_ZOMBIFY | 五、Zombie 丧尸家族（zombies.dm，重点） |
| TRAIT_PLANT_SAFE | PLANT_SAFE | 2. Podweak（弱化植物人 / podweak）, 七、Podperson 植物人（podpeople.dm） |
| TRAIT_PULL_BLOCKED | PULL_BLOCKED | 一、Spirit / Ghost 幽灵（ghost.dm） |
| TRAIT_ROBOTIC_DNA_ORGANS | ROBOTIC_DNA_ORGANS | 14. Synthetic（合成人 / synths synthetic.dm） |
| TRAIT_ROCK_EATER | ROCK_EATER | 3. Protean（液态金属纳米机群 / protean_species） |
| TRAIT_SEE_BLESSED_TILES | SEE_BLESSED_TILES | 一、Spirit / Ghost 幽灵（ghost.dm） |
| TRAIT_SLICK_SKIN | SLICK_SKIN | 2. Akula（阿库拉鲨人 / akula）, 5. Shadekin（影狐 / shadekin） |
| TRAIT_SLIME_HYDROPHOBIA | SLIME_HYDROPHOBIA | 4. Roundstart Slime（开局史莱姆混血 / roundstartslime） |
| TRAIT_SLIPPERY | SLIPPERY | 2. Akula（阿库拉鲨人 / akula） |
| TRAIT_SNOB | SNOB | 4. Dwarf（矮人 / dwarf） |
| TRAIT_SUCCUMB_OVERRIDE | SUCCUMB_OVERRIDE | 五、Zombie 丧尸家族（zombies.dm，重点） |
| TRAIT_SYNTHETIC | SYNTHETIC | 14. Synthetic（合成人 / synths synthetic.dm）, 3. Protean（液态金属纳米机群 / protean_species） |
| TRAIT_TACKLING_TAILED_DEFENDER | TACKLING_TAILED_DEFENDER | 2. 蜥蜴人 Lizardperson（lizardpeople.dm） |
| TRAIT_TUMOR | TUMOR | 7. Hemophage（血噬族 · 全套 / hemophage） |
| TRAIT_UI_BLOCKED | UI_BLOCKED | 一、Spirit / Ghost 幽灵（ghost.dm） |
| TRAIT_UNHUSKABLE | UNHUSKABLE | 6. Golem Weak（弱化魔像 / golemweak）, 6. 等离子人 Plasmaman（plasmamen.dm）, 7. 魔像 Golem（SPECIES_GOLEM）, 一、Spirit / Ghost 幽灵（ghost.dm）… |
| TRAIT_WATER_BREATHING | WATER_BREATHING | 1. 蜗牛 Snailperson（SPECIES_SNAIL）, 13. Snail（蜗牛人 NOVA 覆写 / master_files species_type/, 2. Akula（阿库拉鲨人 / akula）, 3. Akula Generic（通用阿库拉 / aquatic）… |
| TRAIT_WATER_HATER | WATER_HATER | 3. 猫人 Felinid（felinid.dm）, 7. Tajaran（猫族 / tajaran） |
| TRAIT_WEAK_SOUL | WEAK_SOUL | 八、Monkey 猴子（monkeys.dm） |
| TRAIT_WEB_SURFER | WEB_SURFER | 10. Insectoid（虫族 / insectoid） |
| TRAIT_WEB_WEAVER | WEB_WEAVER | 10. Insectoid（虫族 / insectoid） |
| TRAIT_WINE_TASTER | WINE_TASTER | 11. Lizard（蜥蜴人修改 / lizard）, 2. 蜥蜴人 Lizardperson（lizardpeople.dm） |
| TRAIT_WOUND_LICKER | WOUND_LICKER | 3. 猫人 Felinid（felinid.dm） |

### 6.5 统计

- **总特质数**：107 种（含动态特质）
- **覆盖物种段**：49 个（基础 + NOVA + 亚种）
- 最广泛共享：NOBREATH（~15 物种）、MUTANT_COLORS（~14）、CAN_STRIP/ADVANCEDTOOLUSER/LITERATE（~10）
- 最独特：TRAIT_CANNOT_CRYSTALIZE（以太人）、TRAIT_BORN_MONKEY（猴子）、TRAIT_ABDUCTOR_HUD（外星）、TRAIT_DWARF/SNOB（矮人）
