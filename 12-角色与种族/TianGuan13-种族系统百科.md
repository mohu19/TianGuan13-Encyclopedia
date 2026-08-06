# TianGuan13 种族系统百科 (Species Encyclopedia)

> 基于 TianGuan13 NovaSector 分支源码全量整理。种族定义：`code/modules/mob/living/carbon/human/species_types/`（基础）+ `modular_nova/modules/customization/.../species/`（NOVA）+ 各专属模块。
> 本文档全量列出 **50 个玩家可选种族**（基础 24 + NOVA 27 = 51 条记录，Slimeperson/开局史莱姆双计去重后 50；**开局可选用 35 个** = 基础 8 + NOVA 27，均经 `config/game_options.txt` ROUNDSTART_RACES 逐行核实），每个含源码路径+关键数值+机制+评价，无省略。

## 目录

- [一、开局配置总览](#一开局配置总览)
- [二、基础种族全录（24 个）](#二基础种族全录24-个)
- [三、NOVA 种族全录（27 个）](#三nova-种族全录27-个)
- [四、相关模块区分](#四相关模块区分)

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
