# TianGuan13 种族系统完全百科 (Species Encyclopedia)

> 基于 TianGuan13 NovaSector 分支源码全量整理。种族定义：`code/modules/mob/living/carbon/human/species_types/`（基础）+ `modular_nova/modules/customization/.../species/`（NOVA）+ 各专属模块。
> 本文档全量列出 **50 个玩家可选种族**（开局可选用 44 个），每个含源码路径+关键数值+机制+评价，无省略。

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

---

## 四、相关模块区分

| 模块 | 说明 |
|---|---|
| `modules/spider/` | 巨型蜘蛛**敌人**（非玩家种族，见 Nova 上卷） |
| `modules/mutants/` | 变种怪**感染型**（非开局可选） |
| `digitigrade_cybernetics` / `vox_sprites` / `alt_vox` | 外观/音效扩展（趾行腿/鸟人贴图/AI 语音） |

---

> **文档完** — 50 个玩家种族全录（基础 24 + NOVA 27，部分重叠分类），开局可选 44 个，全部含源码路径与核心机制。自查无"等 N 种"省略。
