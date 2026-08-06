# TianGuan13 百科遗漏清单（2026-08-06 翻新核查）

> **来源**: 以 `TianGuan13-玩法目录与代码出处.md`（866 行全玩法索引）为基准，对比现有 26 篇主百科 + 辅助文档的实际覆盖。
> **状态**: 2026-08-06 翻新核查——现有百科的"偷懒点"（声明数量未列全）已由子代理逐篇增补，本篇列出**源码中存在但百科完全没有专篇覆盖**的系统。

---

## ✅ 2026-08-06 翻新完成记录（增补一览）

**执行方式**: 5 批共 15 个子代理并行（每批 3 个），按篇从源码提取全量数据补全；2 篇（供应出口表/管理员动词）由主 agent 手动收尾。
| 文档 | 增补内容 | 关键修正 |
|---|---|---|
| 休闲玩法 | pAI 15 软件全录（RAM 精确价）、NOVA 语言 19 种全录 | 软件价格"5-35 RAM"→逐项精确值 |
| 生物图鉴 | 行为组件 22、狗 14、家畜 17、太空熊 7、小丑星球 12、虚拟域生物 11、假船员 10 | 行为组件 21→22（源码实际） |
| 科研 | 隐藏节点 15、实验 78 全量、细胞系 27+4 全录 | 实验 84→78、细胞系 45→40（源码实际） |
| 模块电脑 | 程序 64 个完整详表（NTNet/硬件/大小/权限） | "详见子代理数据补充"→全量 |
| 管理员参考 | 动词 251 全录（9 分类+NOVA 30）、天罚 44 全录 | 动词 191→251、天罚 38→44 |
| 反派 | 符文 10、血魔法 10、小型阵营 27 全录 | 符文 12→10（源码实际） |
| 战斗 | 法杖 16、魔杖 31 全录 | 魔杖 19→31；删除 2 个幻觉条目（幻影杖/龙卷杖） |
| wiremod | 数学 11/列表 21/工具 14 全补、BCI 8/实体 15/NTNet 3/变量 3 | 123 类型与源码一致 |
| 供应+比特跑者 | 悬赏 16 分类、出口 172 种全量表 | 出口 120→172（NOVA 调价标注） |
| 工程气体 | 核实为误报（4 处早已齐全） | — |
| 随机事件 | 藤蔓突变 21、巫师事件 32 | 17→21、28→32；修正权重错误 |
| 服装装备 | 反派制服 15 全量、脖子精选标注 | 14→15 |
| 小游戏 | 全息甲板 29 场景 | 修正阵营分组错误、地图 8→7 |
| 钓鱼 | 鱼种 70 全录 | +3 新鱼 |
| 载具机甲 | 弹药箱/工具模块补齐 | — |
| 化学 | 病毒 12 级全表 | "Lv2→Lv12"→实际上限 Lv8 |
| 水培 | 外星考古植物 10 种全列 | "等 10 种"→全列 |
| 拉瓦兰 | 废墟 46 种全录 | 38→46（NOVA +8） |
| 售货机 | 66 种全录 + 特殊功能 18 项 | 65→66 |
| 工厂无人机 | 探索事件 31 核实清楚（8+15+6+2） | 标题 9→8 |

**新产出**: 本篇遗漏清单 + `TianGuan13-百科遗漏清单.md`（本文件）

---

## 一、玩家核心系统缺失（高优先级）

### 1.1 开局特质 Quirks 系统 — ❌ 无专篇
- **源码**: `code/datums/quirks/`（`_quirk.dm` + positive/neutral/negative 三个子目录）
- **现状**: 现有百科仅职业百科 L25-26 顺带提了 `mind_traits`/`liver_traits` 字段名，**无任何百科系统介绍特质玩法**
- **内容量**: 正面特质（酒精耐受/共情/多语种/自由奔跑/快乐/音乐家/太空人/饕餮等）+ 中性特质（秃头/素食/恐惧症/摄影师/菠萝爱/恨/贼眼等）+ 负面特质（失明/耳聋/哑巴/截瘫/四肢截肢/嗜睡/社交焦虑/偏瘫等）
- **建议**: 新增专篇 `TianGuan13-开局特质百科.md`，全量列出三类特质（名称+效果+代价）

### 1.2 种族选择 Species 系统 — ❌ 无专篇
- **源码**: `code/modules/mob/living/carbon/human/species_types/`（18 个基础种族）+ `modular_nova/modules/voxes/` `teshari/` `shadekin/`（3 个 NOVA 种族）
- **现状**: 职业百科 0 提及，仅服装/战斗/休闲等百科顺带提种族外观
- **内容量**: 人类/蜥蜴人/蛾人/等离子人/蝇人/果冻人/骷髅/植物人/以太体/猫人/暗影人/外星掳掠者/安卓/无头骑士/魔像/蘑菇人/蜗牛人/吸血鬼/丧尸 + NOVA 鸟人/鸟形族/暗影猫
- **建议**: 新增专篇 `TianGuan13-种族系统百科.md`（各种族特性/优劣势/专属机制/配装需求）

---

## 二、Nova Sector 特色模块缺失（中优先级）

| # | 模块 | 源码路径 | 说明 | 状态 |
|---|---|---|---|---|
| 1 | **Armaments 军火配装站** | `modular_nova/modules/armaments/` | 军火配装 | ❌ 无覆盖 |
| 2 | **Bluespace Miner 蓝空间采矿** | `modular_nova/modules/bluespace_miner/` | 蓝空间采矿玩法 | ❌ 无覆盖 |
| 3 | **Shadekin 暗影猫族** | `modular_nova/modules/shadekin/` | NOVA 专属种族 | ❌ 无覆盖 |
| 4 | **Advanced Shuttles 高级飞船** | `modular_nova/modules/advanced_shuttles/` | 高级飞船引擎 | ❌ 无覆盖 |
| 5 | **BSA Overhaul 蓝图武器** | `modular_nova/modules/bsa_overhaul/` | 蓝图武器改造 | ❌ 无覆盖 |
| 6 | **Bridge Assistant 舰桥助理** | `modular_nova/modules/bridge_assistant/` | 舰桥助理职业 | ❌ 无覆盖 |
| 7 | **Telecomms Specialist 通信专家** | `modular_nova/modules/telecomms_specialist/` | 通信专家职业 | ❌ 无覆盖 |
| 8 | **Capitalism 资本主义经济** | `modular_nova/modules/economy/`（49 行，非 capitalism） | 资本主义经济系统（银河市场调价+材料货箱） | ⚠️ 已在 Nova 下卷 #27 经济覆盖 |
| 9 | **Station Traits 站台特质** | `modular_nova/modules/station_traits/` | 空间站全局特征 | ❌ 无覆盖 |
| 10 | **Biteng 咬人特质** | `modular_nova/modules/bitey_quirk/` | 咬人特质 | ❌ 无覆盖 |
| 11 | **Borg Buffs 机器人增强** | `modular_nova/modules/borg_buffs/` | 机器人增强 | ❌ 无覆盖 |
| 12 | **Brain Tumor 脑瘤** | base `code/datums/quirks/negative/brain_tumor.dm`（非独立模块） | 脑瘤怪癖 | ✅ 已在开局特质百科覆盖 |
| 13 | **Cell Guns 细胞枪** | `modular_nova/modules/cellguns/` | 细胞武器 | ❌ 无覆盖 |
| 14 | **Underworld Connections 地下交易** | `modular_nova/modules/underworld_connections/` | 地下交易 | ❌ 无覆盖 |
| 15 | **Self Actualization Device** | `modular_nova/modules/self_actualization_device/` | 自我实现装置 | ❌ 无覆盖 |
| 16 | **Wargame Projectors 兵棋投影** | `modular_nova/modules/wargame_projectors/` | 兵棋推演投影 | ❌ 无覆盖 |
| 17 | **Tribal Extended 部落扩展** | `modular_nova/modules/tribal_extended/` | 部落扩展 | ❌ 无覆盖 |
| 18 | **Tarkon 公司模块** | `modular_nova/modules/tarkon/` | Tarkon 公司 | ⚠️ 仅提及 2 次 |
| 19 | **Cargo Items (Nova)** | `modular_nova/modules/cargo_items/` | 新增货运物品 | ❌ 无覆盖 |
| 20 | **Spider 蜘蛛种族** | `modular_nova/modules/spider/` | 蜘蛛种族 | ❌ 无覆盖（注意生物图鉴 3.5 有蜘蛛家族 48 种，但那是生物非种族） |
| 21 | **Species Synthesizer 物种合成器** | `modular_nova/modules/species_synthesizer/` | 物种合成器 | ❌ 无覆盖 |
| 22 | **SEVA Suit** | `modular_nova/modules/SEVA_suit/` | SEVA 防护服（潜行者风格） | ❌ 无覆盖 |
| 23 | **Fabcraft 以物易物合成** | 源码无此独立模块（为 base crafting 系统扩展，见 `code/modules/crafting/`） | 以物易物合成 | 🔍 待核实实际模块名 |
| 24 | **Solfed Mecha** | `modular_nova/modules/solfed_mechs/` | 机甲战斗 | ⚠️ 载具百科有提及但无专章 |
| 25 | **Teshari 鸟形族** | `modular_nova/modules/teshari/` | 鸟形族 | ⚠️ 仅提及 2 次 |

---

## 三、核心游戏系统缺失（中优先级）

| # | 系统 | 源码路径 | 现状 |
|---|---|---|---|
| 1 | **穿梭机/飞船系统** | `code/modules/shuttle/`（16 种类型） | 各百科零星提及（紧急穿梭机/采矿穿梭机），**无专篇**。16 种：紧急/货运/采矿/到达/定期/突击舱/逃生舱/ERT/渗透艇/海盗船/猎船/劳改/辅助基地/白船/废墟/星怒战斗机 |
| 2 | **通信系统** | `code/modules/telecommunications/` + `code/game/say.dm` | 无专篇。对话/喊话/耳语/广播/全息通话/请求消息/无线电加密频道/数据 HUD |
| 3 | **AI 玩家系统** | `code/modules/mob/living/silicon/ai/` | 管理员参考提 7 次（AI 管理命令），**无 AI 玩家玩法专篇**（AI 核心/法则/视角摄像头） |
| 4 | **远征任务 Away Missions** | `code/modules/awaymissions/` | 仅生物图鉴提 1 次，无专篇 |
| 5 | **全局天气系统** | `code/datums/weather/` | 仅拉瓦兰灰烬风暴有局部覆盖，**无全局天气专篇**（灰烬风暴/辐射/雪/雨） |
| 6 | **成就系统** | `code/datums/achievements/` | 战斗百科提 1 次，无专篇（BOSS/职业/技能/杂项成就） |
| 7 | **记录系统** | `code/datums/records/` | 无专篇（犯罪/医疗/机组档案） |
| 8 | **街机 Arcade** | `code/game/machinery/computer/arcade/` | 小游戏百科 0 提及，无专篇（打飞机/猎户座等） |
| 9 | **抓捕旗 CTF** | `code/modules/capture_the_flag/` | 无覆盖 |
| 10 | **死亡竞赛** | `code/modules/deathmatch/` | 战斗百科提及但无专章 |

---

## 四、天关专属模块（低优先级，多为小改动）

| # | 模块 | 源码路径 | 说明 |
|---|---|---|---|
| 1 | 安全官配装 | `modular_tianguan/code/security_officer.dm` | 安全官额外获得迷你能量枪 |
| 2 | 项目符号 | `modular_tianguan/code/projectiles/` | 弹道调整 |
| 3 | 警报等级 | `modular_tianguan/modules/Alert_level/` | 警报等级自定义 |
| 4 | 货运调整 | `modular_tianguan/modules/cargo_tweaks/` | 货运参数优化 |
| 5 | 紧急穿梭机 CC | `modular_tianguan/modules/emergency_shuttle_cc/` | 中央指挥穿梭机调整 |
| 6 | 医疗记录 | `modular_tianguan/modules/medical_blanks/` | 医疗表格模板 |
| 7 | SOP 手册 | `modular_tianguan/modules/sop_book/` | 标准作业程序书 |
| 8 | Uplink 物品 | `modular_tianguan/modules/uplink_item/` | 加密 Uplink 道具定制 |

---

## 五、建议处理顺序

1. **立即补**（玩家可交互性最高）：种族系统、开局特质——玩家创建角色必见
2. **随主百科补**：穿梭机系统（并入载具百科或新增）、通信系统（并入模块电脑或新增）、AI 玩家系统（并入反派 Malf 章节或新增）
3. **Nova 特色模块**：挑内容量大的独立成篇（Bluespace Miner/Capitalism/Station Traits），小模块合并成一个"Nova 特色模块合集"
4. **天关专属**：合并成一个小章节即可
5. **成就/记录/街机等**：并入现有相关百科（成就→管理员参考、街机→小游戏）

---

## ✅ 2026-08-06 第二批：新章节 10 篇（子代理派发中/已完成）

按本清单建议顺序派发 10 个新章节，4 批子代理执行：

| # | 新章节 | 源码范围 | 状态 |
|---|---|---|---|
| 1 | `TianGuan13-种族系统百科.md` | species_types 21 文件 4.4K 行 + NOVA voxes/teshari/shadekin/synths/spider | ✅ **已完成**（50 种族全录） |
| 2 | `TianGuan13-开局特质百科.md` | quirks 98 文件 4.4K 行（正/中/负三类）+ NOVA quirk | ✅ **已完成**（168 特质全录，2,419 行） |
| 3 | `TianGuan13-穿梭机与飞船百科.md` | shuttle 41 文件 7.1K 行（16 种类型）+ advanced_shuttles | ✅ **已完成**（16 类型 130+ 型号） |
| 4 | `TianGuan13-AI与机器人百科.md` | silicon/ai 21 + robot 13 + ai_laws | ✅ **已完成**（31 法则+23 模型+55 升级件） |
| 5 | `TianGuan13-通信系统百科.md` | telecomms 2.5K + say.dm + radio | ✅ **已完成**（频道 38+机器 30+耳机 39） |
| 6 | `TianGuan13-天气与远征百科.md` | weather 1.5K + awaymissions 2.5K | ✅ **已完成**（天气 18+远征 10 目的地） |
| 7 | `TianGuan13-成就与记录百科.md` | achievements 1.4K + records 678 行 | ✅ **已完成**（95 成就+21 积分） |
| 8 | `TianGuan13-Nova特色模块百科上.md` | synths/teshari/shadekin/voxes/spider/xenomorph/xenoarch/bitrunning | ✅ **已完成**（9 大模块） |
| 9 | `TianGuan13-Nova特色模块百科下.md` | economy/station_traits/bluespace_miner/armaments/cellguns/tarkon 等 25+ 模块 | ✅ **已完成**（26 模块） |
| 10 | `TianGuan13-天关专属模块百科.md` | modular_tianguan 302 行（8 模块） | ✅ **已完成**（22.9KB，8 模块全录+上游对比） |

---

*本文档由 2026-08-06 全库扫描生成：玩法目录 866 行 × 26 篇主百科关键词比对，命中 <3 次判定为未覆盖。*
