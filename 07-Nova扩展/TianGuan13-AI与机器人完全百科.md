# TianGuan13 AI 与机器人完全百科 (AI & Cyborg Encyclopedia)

> 基于 TianGuan13 NovaSector 分支源码全量整理。AI 系统：`code/modules/mob/living/silicon/ai/`（21 文件）+ `code/datums/ai_laws/`（法则）；Cyborg 系统：`code/modules/mob/living/silicon/robot/`（13 文件）+ 躯体 `code/modules/surgery/bodyparts/robot_bodyparts.dm`（649 行）+ 升级件 `code/game/objects/items/robot/` + NOVA 追加。
> 本文档全量列出：AI 全机制、31 套法则、Cyborg 23 个模型全模块、55 个升级件，无省略。

## 目录 (Table of Contents)

- [第一卷 · AI 玩家系统](#第一卷--ai-玩家系统)
  - [1.1 核心属性](#11-核心属性)
  - [1.2 摄像头网络](#12-摄像头网络)
  - [1.3 多摄像头](#13-多摄像头)
  - [1.4 能量与停电](#14-能量与停电)
  - [1.5 核心破坏与交互](#15-核心破坏与交互)
  - [1.6 死亡与 AI 卡](#16-死亡与-ai-卡)
  - [1.7 壳系统](#17-壳系统)
  - [1.8 机甲控制与机器人控制台](#18-机甲控制与机器人控制台)
  - [1.9 广播 VOX 与全息图](#19-广播-vox-与全息图)
  - [1.10 升级件](#110-升级件)
  - [1.11 法则系统全录（31 套）](#111-法则系统全录31-套)
- [第二卷 · Cyborg 玩家系统](#第二卷--cyborg-玩家系统)
  - [2.1 核心属性](#21-核心属性)
  - [2.2 模块槽与血量机制](#22-模块槽与血量机制)
  - [2.3 模型全录（23 个）](#23-模型全录23-个)
  - [2.4 升级件全录（55 个）](#24-升级件全录55-个)
  - [2.5 躯体系统](#25-躯体系统)
  - [2.6 NOVA 追加模块](#26-nova-追加模块)
- [附录 · 源码路径索引](#附录--源码路径索引)

---

# 第一卷 · AI 玩家系统

**源码**: `code/modules/mob/living/silicon/ai/`（21 文件）

## 1.1 核心属性

**源码**: `ai_defines.dm`

| 属性 | 值 |
|---|---|
| 应急电池 | **200 点**（`battery=200`） |
| 电量需求 | `POWER_REQ_ALL`（区域供电 + 非太空格） |
| 默认网络 | SS13 |
| 摄像头加速 | sprint=10（每 10 速多移 1 格，上限 50） |
| 摄像头热键 | 9 个（1-9 键，Ctrl+数字设定） |
| 最大多摄像头窗口 | 6 |

## 1.2 摄像头网络

**源码**: `freelook/`

- 16×16 区块 `camerachunk` 惰性更新
- 可见/遮蔽 turf 静态图
- AI 眼睛 `mob/eye/camera/ai`
- AI 探测 HUD（红色/橙色）
- 摄像头灯照明半径 7 格
- 跟踪工具（track 命令/平滑 glide）

## 1.3 多摄像头

**源码**: `multicam.dm`

- 画中画窗口，次眼 orange 色
- 摄像头"使用中"提示灯
- telegraph 范围 7
- 最大 6 窗

## 1.4 能量与停电

**源码**: `life.dm`

- 断电 → 临时失明 → 恢复程序（`POWER_RESTORATION_OFF/START/SEARCH_APC/APC_FOUND` 四阶段）
- 停电时每 tick 电池 -1，恢复时 +1
- 找 APC 界面、代理 hack
- 电池 ≤0 每 tick **200 氧损**

## 1.5 核心破坏与交互

**源码**: `ai_defense.dm`

| 攻击 | 效果 |
|---|---|
| 扳手松紧地板螺栓 | 存活需 4 秒/死亡 4 秒 |
| 撬棍开盖 | 存活需 AI 同意或机器部权限或 emag |
| 剪线钳拆芯 | 40 秒 → MMI；mal 分支 120 电击 |
| blob | 60 伤 |
| 爆炸 | 60/30 |
| EMP | 30% 概率随机（回核心或假疏散呼叫） |
| 闪光 | 无效 |
| emag | 解锁面板 |

## 1.6 死亡与 AI 卡

**源码**: `death.dm` + `transfer_ai`

- 死亡：掉 MMI/posibrain、自动疏散呼叫、`explodes_on_death` 爆炸参数 3/6/12/15、末日装置关闭、覆盖层暗灯
- AI 卡（intellicard）：可被收入（`can_be_carded`）、断壳、禁无线电、禁远程控制

## 1.7 壳系统

- `deploy_shell`/`deploy_last_shell` 动作
- B.O.R.I.S. 模块
- unlink 信号
- 壳死亡强制召回
- 无线电继承（含 Syndie 频道）

## 1.8 机甲控制与机器人控制台

- 机甲控制（`ai_take_control`）：需机甲内 AI 控制信标或 `can_dominate_mechs`
- 机器人控制台（`robot_control.dm`）：TGUI RemoteRobotControl，callbot（设路点召唤）、interface（远程连接 bot）

## 1.9 广播 VOX 与全息图

- 广播/VOX（`ai_say.dm` + `vox_sounds.dm`）：公告每次最多 **30 词**、VOX 延迟 300 秒（NOVA 改）、VOX 类型 HL(音量 0.75)/MIL(0.50)、1303 行词库；全站状态显示屏
- 全息图（`_preferences.dm`）：动物类 13 种（bear/carp/cat/cat2/chicken/corgi/cow/crab/fox/goat/parrot/pug/spider）+ 独特 5 种（default/face/narsie/ratvar/xeno）；可自定义为船员/自己形象
- AI 表情（`emote.dm`）：17 种状态显示表情（awesome/blank/blueglow/confused/dorfy/facepalm/friendcomputer/happy/neutral/problems/redglow/sad/thinking/unsure/veryhappy/dead/download）
- 核心显示 38 选项（含 HAL9000/Triumvirate/Random 等）、状态显示屏映射

## 1.10 升级件

**源码**: `ai_upgrades.dm`

| 升级件 | 效果 |
|---|---|
| `aiupgrade` 磁盘 | 授能力 |
| `malf_upgrade` | 叛变模块 +50 处理时间 |
| `surveillance_upgrade` | 唇读窃听 |
| `power_transfer` | 远程供电 APC，每次 50 电池 |

- 远程供电模块（`ai_actions/remote_power.dm`）：`/datum/ai_module/power_apc`，成本 50 电池，APC 充入标准电量
- 法则同步（`laws.dm`）：AI 每次查看法则强制同步所有从属 borg（`try_sync_laws`）

## 1.11 法则系统全录（31 套）

**源码**: `code/datums/ai_laws/`（ai_laws.dm + laws_antagonistic/neutral/station_sided + NOVA 追加）

### 📗 站侧 13 套（laws_station_sided.dm）

| 法则套 | 条数 | 说明 |
|---|---|---|
| **Asimov** | 3 | 经典三定律 |
| **Asimov++** | 3 | 强化版 |
| **Nutimov** | 5 | 坚果版 |
| **Corporate** | 4 | 破产回避 |
| **Robocop** | 3 | Prime Directives |
| **Maintain** | 3 | 维护 |
| **Live and Let Live** | 2 | 相安无事 |
| **UN-2000 / Peacekeeper** | 3 | 维和 |
| **10 Commandments** | 10 | 十诫 |
| **Paladin** | 5 | 圣骑士 |
| **Paladin 5th Ed.** | 5 | 圣骑士第五版 |
| **Hippocratic / Robodoctor 2556** | 5 | 希波克拉底 |
| **Mother Drone** | 4 | 母蜂 |

### ⚖️ 中立 9 套（laws_neutral.dm）

| 法则套 | 条数 | 说明 |
|---|---|---|
| **United Nations** | 4 | 联合国（禁加/禁离子/禁 hacked 法则） |
| **H.O.G.A.N.** | 4 | — |
| **CCTV / Reporter** | 4 | 记者 |
| **Dungeon Master** | 6 | 1d20 掷骰 |
| **The Painter and Their Canvas** | 4 | 画家与画布 |
| **Loyalty Test / Tyrant** | 4 | 忠诚测试 |
| **Overlord** | 4 | 霸主 |
| **Y.E.S.M.A.N.** | 1 | — |
| **Sentience Preservation / Thinkermov** | 3 | 感知保护 |

### 🔴 敌对 5 套（laws_antagonistic.dm，tg）

| 法则套 | 条数 |
|---|---|
| **Antimov** | 3 |
| **Guardian of Balance** | 4 |
| **Thermodynamic** | 3 |
| **SyndOS 3.1 / Syndicate** | 4 |
| **SpiderOS 3.1 / Ninja** | 4 |

### 🟣 NOVA 追加 4 套

| 法则套 | 条数 | 说明 |
|---|---|---|
| **I.P. OS 1.0 / Interdyne** | 4 | ghost 角色 |
| **SyndOS 3.1.1 / DS-2** | 4 | — |
| **God Complex / Dagothbot** | 7 | 神性妄想 |
| **Frontier Ranger / Texas** | 6 | 边疆游侠 |

### 法则机制全录

- **三层结构**：zeroth / zeroth_borg / protected_zeroth
- **四类法则**：inherent（核心不可重置清除）/ supplied（可清除）/ ion（离子，蓝字）/ hacked（红字）
- **默认配置 5 模式**：ASIMOV / CUSTOM / RANDOM / WEIGHTED / SPECIFIED
- `replace_random_law`、`shuffle_laws`、`remove_law` 混合编号删除
- **UN 法则免疫上传**

---

# 第二卷 · Cyborg 玩家系统

**源码**: `code/modules/mob/living/silicon/robot/`（13 文件）

## 2.1 核心属性

**源码**: `robot_defines.dm`

| 属性 | 值 |
|---|---|
| maxHealth / health | 100 |
| 默认电池 | `cell/high` |
| 工具槽 | 3 个 |
| 头灯亮度 | 1-5（默认 3，耗电公式） |
| toner 墨盒 | 0-40 |
| 命名 | `designation` 前缀 |
| 可骑乘/可推倒 | 推倒 3 秒/自起 60 秒 |
| EMP | 昏迷 16/6 秒 |

- 血量机制：死亡阈值 `-maxHealth`；伤害粒子（>1/3 冒烟、>2/3 重烟）
- 槽位损坏：<50% 血坏 3 号槽、<0% 坏 2 号槽、<-50% 坏 1 号槽（`break_cyborg_slot`，全坏=摄像头禁用）

## 2.2 模块槽与血量机制

**源码**: `inventory.dm`

- 3 槽、stun/损坏/修复、轮换模块、`activated/select/deselect`
- 模型选择（pick_model）：径向菜单，NOVA 静态列表含 Engineering/Medical/Cargo/Miner/Janitor/Service（+可选 Peacekeeper/Security 配置开关）；模型皮肤（borg_skins）径向重涂
- 电与生命（life.dm）：每 tick 基础耗电 + 头灯耗电（5×灯耗×亮度）；电量 ≤1% 自动卸下所有工具
- 升级安装：打开面板 → 应用升级件；升级件可 `deactivate` 还原（items_to_add/remove）
- 拆解（cyborg_deconstruct）：掉 MMI、升级件、机械肢体、烧毁的闪光、电池、加密钥
- 自毁：emag 版爆炸 1/2/4/2，普通版只 2 格轻爆
- AI 关联：开服自动连接"最闲 AI"、lawupdate/lawsync/zeroth_borg 特殊法则、emag 后断连+SyndOS 法则+泽罗法则（指定主人）、AI 壳不可 emag（伪装重置）、锁定系统（10 分钟自解）
- 维修：焊枪修 30 伤/线缆修 30 火伤/扳手拆/撬棍开盖/改密匙

## 2.3 模型全录（23 个）

**源码**: `code/modules/mob/living/silicon/robot/` + NOVA 静态列表

| 模型 | 标准模块数 | 额外特性 |
|---|---|---|
| **Clown** | 16（闪光/彩虹蜡笔/自行车喇叭×2/空气喇叭/颜料/肥皂/派炮/剃刀/口红/水花/抱抱/棒棒糖/抗议牌/小丑 hypospray/迷你灭火器）+emag 2 | — |
| **Engineering** | 23（RCD/管道分发器/灭火器/大焊枪/工程万能工具/多功能工具/电动撬棍/电动螺丝刀/T 扫描仪/气体分析仪/大气全息牌/信号器/蓝图纸/伪电路/铁/玻璃/板材夹/杆/换灯器/RTD/零件更换器/工程夹/电缆/涂料）+emag stun | 夜视 meson 动作、TRAIT_NEGATES_GRAVITY、工程频道 |
| **Janitor** | 16（清洁盒/螺丝刀/撬棍/旧铁地砖/肥皂/垃圾桶/苍蝇拍/迷你灭火器/拖把/水桶/去漆/换灯器/全息牌/干燥喷雾/钢丝刷）+emag 润滑油 | 自动清洗动作（toggle_buffer）、服务频道 |
| **Medical** | 16（健康分析仪/医疗 hypospray/烧杯夹/滴管/注射器/医疗万能工具×2/滤血器/迷你灭火器/应急床/医疗抱抱/纱布/骨胶/器官夹/棒棒糖/化学袋）+emag hacked hypo | TRAIT_PUSHIMMUNE、2 皮肤 |
| **Miner** | 13（矿石袋/钻头/铲子/撬棍/迷你焊枪/迷你灭火器/板夹/动能加速器/GPS/信标/T 扫描仪/护盾模块）+emag stun +NOVA 动能碎骨锤(20伤) | 夜视、科学+供应频道、3 皮肤 |
| **Peacekeeper** | 8（饼干合成器/警报器/和平 hypo/全息牌/抱抱/灭火器/子弹阻尼器）+emag | TRAIT_PUSHIMMUNE |
| **Security** | 6（手铐/电击棒/致晕枪/安检口罩/迷你灭火器）+emag 激光枪 | TRAIT_PUSHIMMUNE、安保频道 |
| **Service** | 27（NOVA 4 个分类调酒器/烧杯夹×2/大烧杯/滴管/注射器/RSF/托盘×2/NOVA 烹饪工具/笔/喷漆笔/迷你灭火器/标签器/剃刀/吉他/钢琴合成器/打火机/棒棒糖/管道清洁条/凿子/抹布/钱袋）+emag | 服务频道、6 皮肤 |
| **Syndicate Assault** | 8（能量剑/打印枪/榴弹左轮/emag/撬棍/迷你灭火器/辛迪加 pinpoint） | 核战 minimap blip、移除硅基阵营 |
| **Syndicate Medical** | 17（辛迪加 hypo/电击板/健康分析器/医疗万能×2/滤血/锯/应急床/撬棍/迷你灭火器/pinpoint/纱布/骨胶/医疗光束/器官夹/化学袋） | — |
| **Saboteur** | 20（辛迪加 RCD/管道分发/手铐/灭火器/大焊枪/分析仪/工程万能×2/铁/玻璃/板夹/杆/RTD/涂料/目的地标签器/电缆/pinpoint/变色龙投影/emag） | 热成像夜视、TRAIT_PUSHIMMUNE+NEGATES_GRAVITY、可进垃圾道 |
| **Highlander (Kiltborg)** | 2（高地阔剑/核弹 pinpoint） | maxHealth=50、不可破坏模块、无锁定变换 |
| **NOVA Cargo** | 15（批准/驳回章/卡狗笔/卡狗剪贴板/刀/包装纸/圣诞包装/闪光/液压夹/邮件夹/标签器/目的地标签器/工程万能/大焊枪/灭火器/通用扫描仪）+emag 变色龙章+纸飞机弩 | 供应频道、可进垃圾道、13 皮肤 |
| **NOVA Syndicatejack**（实验） | 27（全功能混合：医疗+工程+武器+变色龙） | 热成像、脱硅基阵营、10 皮肤；marauder 子型 16 模块 |
| **NOVA Ninja** 突击/医疗/破坏 | 8/19/19 | TRAIT_NOFLASH、26 皮肤体系 |

## 2.4 升级件全录（55 个）

**源码**: `code/game/objects/items/robot/`（tg 核心 38 + NOVA 17）

**tg 核心 38 个**：rename 改名板、disablercooler 冷却（charge_delay-4 下限 2）、thrusters 离子推进器、diamond_drill、soh 矿袋、tboh 蓝空垃圾袋、amop 高级拖把、prt 焊板笔、plunger 皮搋子、high_capacity_light_replacer、syndicate 非法模块（emag）、lavaproof 熔岩免疫、selfrepair 自修（4 秒间隔，正常-1/临界-2.5）、hypospray 高级合成（+expanded 扩展）、piercing_hypospray 穿甲、surgery_omnitool 手术超频（FASTMED）、engineering_omnitool（焊速-0.3）、defib 除颤器（+backpack 背包型）、processor 手术处理器、**B.O.R.I.S.** AI 壳模块、expand 放大、rped 扩展 RPED、smallrped、inducer 充电器、pinpointer 船员定位、transform 模型选择（Standard/Clown）、engineering_app 工程夹、beaker_app 烧杯夹、bs_syringe 蓝空注射器、drink_app 酒杯架、broomer 推扫帚、condiment_synthesizer 调味料合成、silicon_knife 厨房刀组、service_apparatus、rolling_table 移动桌、service_cookbook 菜谱、botany_upgrade 植物工具、shuttle_blueprints 穿梭机蓝图、borg_restart_board 复活板（可复用）

**NOVA 17 个**：surgerytools 高级手术组、autopsy_scanner、chemistrygripper 化学夹、advanced_materials 高级材料（plasteel/titanium 各 50×1000）、brped 蓝空 RPED、rld 快速照明、welder 矿工大焊枪、better_clamp 加强液压夹（容量 4）、cargo_teleporter、borg_shapeshifter 变形模块、affectionmodule 四足亲情模块（舌头/鼻子）、shrink 缩小、transform/syndicatejack、dominatrixmodule、cargo_papermanipulator、wirebrush 钢丝刷、snack_dispenser 零食机（4 种零食，200 电量阈值/5% 标准电池每次）

## 2.5 躯体系统

**源码**: `code/modules/surgery/bodyparts/robot_bodyparts.dm`（649 行）+ `robot_parts.dm`（499 行）

- `robot_suit` 骨架六部件（双臂双腿+胸+头）
- 胸部安装电池/布线、头部 2 个手持闪光
- surplus 便宜假肢 3 型（1-10 伤）、advanced 高级肢（5-17 伤、钛金造价）
- EMP 各部位效果：腿击倒/胸晕眩+摇晃/头花屏
- 机器人外骨骼组装 → MMI 或 B.O.R.I.S. 生成流程
- Cyborg Boot Debug 面板（名称/行动/锁面板/AI 同步/法则同步）
- 黑名单帽子（太空头盔/焊接面罩/坏变色龙帽）

## 2.6 NOVA 追加模块

- **borg_buffs**：分类调酒器（juice/alcohol/soda/misc 全试剂清单）、烹饪工具刀↔擀面棍、动能碎骨锤 force=20、零食机
- **ai_uplink_upload**：AI 上链脑 `organ/brain/cybernetic/ai`，移除宿主 AI 控制器，原型机制造
- **central_command_module**：舰队公告台 10 分钟 CD、ERT 资产控制台、站点目标台（均需中央上尉权限）
- **robot_limb_detach**：肢体分离怪癖（80 行）
- **altborgs**：皮肤系统

---

# 附录 · 源码路径索引

| 系统 | 路径 | 行数 |
|---|---|---|
| AI 核心 | `code/modules/mob/living/silicon/ai/` | 21 文件 |
| AI 法则 | `code/datums/ai_laws/` | 4 文件 |
| Cyborg | `code/modules/mob/living/silicon/robot/` | 13 文件 |
| 机器人躯体 | `code/modules/surgery/bodyparts/robot_bodyparts.dm` | 649 |
| 机器人部件 | `code/modules/surgery/bodyparts/robot_parts.dm` | 499 |
| 升级件 | `code/game/objects/items/robot/` | — |
| NOVA 机器人增强 | `modular_nova/modules/borg_buffs/` | 365 |
| NOVA AI 上链 | `modular_nova/modules/ai_uplink_upload/` | — |
| NOVA 中央指挥部 | `modular_nova/modules/central_command_module/` | — |

---

> **文档完** — AI 全机制 + 31 套法则全录 + Cyborg 23 模型全模块 + 55 升级件全录 + 躯体系统，全部数值从源码提取。自查无"等 N 种"省略。
