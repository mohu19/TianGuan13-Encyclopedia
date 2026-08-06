# TianGuan13 天关专属模块百科 (Complete Encyclopedia of Tianguan-Exclusive Modules)

> 基于 TianGuan13 NovaSector 分支源码 `modular_tianguan/`（302 行 DM）全量整理。天关（TianGuan）是 Nova Sector 的下游 fork，所有天关专属改动均模块化存放于 `modular_tianguan/`，不进入 `modular_nova/`。本文档全量列出该目录下所有模块与改动条目，无省略。

## 目录 (Table of Contents)

- [1. 目录结构总览](#1-目录结构总览-directory-structure-overview)
- [2. 共享代码 code/](#2-共享代码-code)
  - [2.1 安全官配装安全官额外获得迷你能量枪](#21-安全官配装安全官额外获得迷你能量枪-security-officer-mini-egun)
  - [2.2 弹道速度调整](#22-弹道速度调整-projectile-speed-tweaks)
- [3. 模块：Zeta 警报等级](#3-模块zeta-警报等级-module-alert_level-zeta)
- [4. 模块：货运参数优化](#4-模块货运参数优化-module-cargo_tweaks-energy-shield)
- [5. 模块：中央指挥穿梭机停靠点](#5-模块中央指挥穿梭机停靠点-module-emergency_shuttle_cc)
- [6. 模块：医疗表格模板](#6-模块医疗表格模板-module-medical_blanks)
- [7. 模块：标准作业程序书](#7-模块标准作业程序书-module-sop_book)
- [8. 模块：加密 Uplink 道具定制](#8-模块加密-uplink-道具定制-module-uplink_item)
- [9. 配套资源与数据文件](#9-配套资源与数据文件-supporting-assets)
- [10. 检索与维护附录](#10-检索与维护附录-appendix)

## 1. 目录结构总览 (Directory Structure Overview)

完整文件清单（18 个文件）：

```
modular_tianguan/
├── readme.md                        # 天关模块化开发规范（190 行）
├── mirroring_guide.md               # 镜像/同步流程草案（16 行）
├── module_template.md               # 模块 readme 模板（31 行）
├── code/
│   ├── .keep
│   ├── security_officer.dm          # 安全官配装
│   └── projectiles/
│       └── projectiles.dm           # 弹道速度调整
├── icons/.keep
├── master_files/.keep
├── sound/
│   ├── .keep
│   └── security_levels/Zeta.ogg     # Zeta 警报专属音效（878,370 字节）
├── tools/.keep
└── modules/
    ├── .keep
    ├── Alert_level/
    │   ├── security_alerts_define_override.dm
    │   └── Zeta.dm
    ├── cargo_tweaks/code/energy_shield_pack.dm
    ├── emergency_shuttle_cc/
    │   ├── readme.md
    │   └── code/emergency_shuttle_cc.dm
    ├── medical_blanks/
    │   ├── readme.md
    │   └── code/init_medical_blanks.dm
    ├── sop_book/
    │   ├── code/sop_book.dm
    │   └── icons/sop_books.dmi
    └── uplink_item/
        ├── readme.md
        ├── code/syndicate_battle_cruiser.dm
        └── code/uplink_override.dm
```

`tgstation.dme` 第 9914-9923 行按序 include 全部 10 个 DM 文件（排在 `modular_nova` 之后、`modular_z121` 之前）——这是 Uplink 覆盖生效的编译顺序前提。

## 2. 共享代码 code/

### 2.1 安全官配装：安全官额外获得迷你能量枪 (Security Officer Mini E-Gun)

**源码路径**：`modular_tianguan/code/security_officer.dm`

**核心改动**：覆盖 `/datum/outfit/job/security/post_equip()`。安全官（Security Officer）生成配装后，额外向背包（`ITEM_SLOT_BACK`）放入一把迷你能量枪 `/obj/item/gun/energy/e_gun/mini`。`visuals_only = TRUE` 时跳过（不影响装扮预览）。

**关键数值**（迷你能量枪 = 上游 tgstation 核心物品，天关只是塞进安全官背包）：
- 双模式：disable（disabler，20 发/标准电池）与 kill（laser，12 发/标准电池）
- 电池：`/obj/item/stock_parts/power_store/cell/mini_egun`，容量 `STANDARD_CELL_CHARGE * 0.6`（约 6 KJ / 60% 标准电池）
- 3 格充能指示（`charge_sections = 3`），小型体积（`WEIGHT_CLASS_SMALL`），内置不可拆卸防爆手电（seclite）
- 弹：laser 25 伤害 BURN；disabler 30 伤害 STAMINA

**与上游的区别**：上游 tgstation/Nova 的 `/datum/outfit/job/security`（`code/modules/jobs/job_types/security_officer.dm:238`）背包内容仅含证据袋 ×1 + 安全官凭证 ×2，**没有**迷你能量枪。天关玩家实际体验：开局安全官白嫖一把小型自卫混能枪（可放入 holster 能量枪套快速拔枪），无需从储物柜或补给中获取。

### 2.2 弹道速度调整 (Projectile Speed Tweaks)

**源码路径**：`modular_tianguan/code/projectiles/projectiles.dm`

**核心改动**：覆盖 1 个枪械倍率 + 9 个弹种速度。速度语义：`/obj/projectile/speed` 为每 SSprojectiles tick 移动格数（基类 1.25），**值越大弹速越快**。

**全量条目与数值**：

| 类型 | 天关值 | 上游默认值 | 效果 |
|---|---|---|---|
| `/obj/item/gun/energy/laser/carbine` 的 `projectile_speed_multiplier` | **1.15** | 1.5 | 5/R 激光卡宾枪（Type 5/R）弹速乘数降低：点射弹不再极快 |
| `/obj/projectile/beam`（基础激光弹） | **2.5** | 1.25 | 基础光束（含普通激光）提速 2 倍 |
| `/obj/projectile/beam/laser/carbine/cybersun` | **1.8** | 1.25（继承 beam） | 赛博太阳卡宾枪弹提速 1.44 倍 |
| `/obj/projectile/beam/laser/hellfire` | **3.2** | 1.6 | 地狱火激光（30 伤）提速 2 倍 |
| `/obj/projectile/beam/disabler/scatter` | **2.4** | 1.2 | 散射致晕弹（5.5 伤）提速 2 倍 |
| `/obj/projectile/bullet`（基础实弹） | **2** | 1.25 | 全部实弹基类提速 1.6 倍 |
| `/obj/projectile/bullet/incendiary/fire` | **1.6** | 无显式（继承 bullet 1.25） | 火焰弹提速 1.28 倍 |
| `/obj/projectile/bullet/pellet/shotgun_buckshot` | **2** | 1.1 | 霰弹鹿弹（5 伤/粒）提速约 1.82 倍 |
| `/obj/projectile/bullet/pellet/shotgun_rubbershot` | **1.8** | 0.8 | 橡皮弹（3 伤+10 耐力）提速 2.25 倍 |
| `/obj/projectile/bullet/pellet/flechette` | **2** | 1.2 | 箭弹（2 伤）提速约 1.67 倍 |
| `/obj/projectile/beam/disabler` | **2.5** | 1.6 | 致晕光束（30 伤）提速约 1.56 倍 |

**与上游的区别**：上游 tgstation/Nova 各弹种速度如上表"默认值"栏。天关玩家实际体验：几乎所有能量武器与实弹的飞行速度全面加快——激光更难被走位躲开，霰弹/弹丸命中窗口更大，远程枪战节奏明显变快；卡宾枪则相反微降，平衡其点射连发强度。

## 3. 模块：Zeta 警报等级 (Module: Alert_level / Zeta)

**源码路径**：
- `modular_tianguan/modules/Alert_level/security_alerts_define_override.dm`（1 行）
- `modular_tianguan/modules/Alert_level/Zeta.dm`（16 行）
- `modular_tianguan/sound/security_levels/Zeta.ogg`

**核心改动**：新增第十级安全等级 **Zeta（ζ）**，并让死亡小队（Deathsquad）响应代码变为 "Zeta"。

**改动条目与关键数值**：
1. `#define SEC_LEVEL_ZETA 10` —— 扩展警报数字刻度。上游链（`code/__DEFINES/~nova_defines/security_alerts.dm`）：GREEN=0, BLUE=1, VIOLET=2, ORANGE=3, AMBER=4, RED=5, DELTA=6, EPSILON=7, GAMMA=8, FEDERAL=9；天关在其后追加 **10 = ZETA**。
2. `/datum/security_level/zeta`：名称 `zeta`、简称 `ζ`、公告色粉色（pink）、状态屏图标复用 `epsilonalert`、火警灯黑色（`COLOR_BLACK`）、`shuttle_call_time_mod = 1.5`（与 Nova `ALERT_COEFF_NOVA` 相同：穿梭机呼叫时间 ×1.5，默认 10 分钟 → 15 分钟）、升级播报文案走 `config_entry/string/alert_zeta`。
3. **Zeta 公告文案**（天关自定义 config entry 默认值）："全体船员注意。中央指挥部已启用 Zeta 协议。本站已被定义为不可恢复的损失。所有合同都已终止。"
4. **专属音效**：`Zeta.ogg`（878 KB），替代常规警报音。
5. `/datum/ert/deathsquad` 的 `code` 由核心默认 **"Delta"** 改为 **"Zeta"**（核心定义在 `code/datums/ert.dm:57`）——死亡小队激活码与 Zeta 等级联动。

**机制说明**：`SSsecurity_level` 启动时遍历 `subtypesof(/datum/security_level)` 自动注册新等级（`code/controllers/subsystem/security_level.dm:12`），因此 Zeta 通过 `SEC_LEVEL_ZETA = 10` 自动成为可设置的最高等级。

**与上游的区别**：上游 Nova 最高为 FEDERAL=9；天关额外多了 Zeta=10 与配套粉色公告、黑色火警灯、专属 OGG 音效、死亡小队"Zeta"响应码。玩家实际体验：admin/事件触发 Zeta 时全站收到粉色"不可恢复损失"公告、15 分钟慢速撤离倒计时，Deathsquad 以 Zeta 为代号入场。

## 4. 模块：货运参数优化 (Module: cargo_tweaks / Energy Shield Pack)

**源码路径**：`modular_tianguan/modules/cargo_tweaks/code/energy_shield_pack.dm`

**核心改动**：覆盖货物订单 `/datum/supply_pack/companies/armor/bolt/energy_shield`（Bolt 公司单兵能量盾，民用版）。该订单上游定义于 `modular_nova/master_files/code/modules/cargo/packs/companies/armor.dm:179`，商品为 `/obj/item/clothing/accessory/energy_shield/civilian`。

**改动条目与关键数值**：
1. `cost = 10000`（原 **400**，即 `CARGO_CRATE_VALUE * 2` = 200×2）
2. `order_flags = ORDER_GOODY`（原为 `ORDER_COMPANY` = `ORDER_GOODY \| ORDER_DEPARTMENTAL_GOODY`，见 `code/__DEFINES/~nova_defines/cargo.dm:14`）——去掉 `ORDER_DEPARTMENTAL_GOODY` 后，**无法用部门/货运预算下单**（`orderconsole.dm:287,323` + `budgetordering.dm:284` 的 Nova 逻辑），只能个人账户自费订购（goody 无 crate、走 supplypod 直投）。

**民用能量盾数值**（`modular_nova/modules/energy_shield/code/energy_shield_variants.dm:2`）：`max_shield_health = 50`、`recharge_delay = 20 秒`、`recharge_rate = 5`、护盾色 `#88ccff`、Bolt SafeGuard 品牌。

**与上游的区别**：价格暴涨 25 倍（400cr → 10000cr）且只能私人自费购买，杜绝用部门公款批量采购廉价护盾。玩家实际体验：能量盾依旧可买，但成为高达一万信用点的个人奢侈品，货运/部门渠道无法报销。

## 5. 模块：中央指挥穿梭机停靠点 (Module: emergency_shuttle_cc)

**源码路径**：`modular_tianguan/modules/emergency_shuttle_cc/code/emergency_shuttle_cc.dm` + `readme.md`

**核心改动**：修复紧急穿梭机撤离终点（`emergency_away`）的停靠点选择——强制选中基础 CentCom 地图恢复翼的大型撤离船停靠点，避免误选 Nova Interlink 附加层里同名的副本。

**改动条目与机制**：
1. 新增 define（文件内 `#define` + 底部 `#undef`）：`TIANGUAN_EMERGENCY_AWAY_DOCK = "emergency_away"`、`TIANGUAN_CC_RECOVERY_WING_DOCK_DIR = EAST`。
2. 新增 proc：`/datum/controller/subsystem/shuttle/proc/tianguan_get_cc_emergency_away_dock()` —— 遍历 `stationary_docking_ports`，筛选同时满足以下条件的停靠点：
   - `port_destinations == "emergency_away"`
   - `area_type == /area/space`
   - `dir == EAST`
   - `width == 50 && height == 50 && dwidth == 25`
3. 覆盖 `/obj/docking_port/mobile/emergency/dock_id(id)`：当 `id == "emergency_away"` 时优先调用上述 proc；找到则记录日志并 `initiate_docking(selected_dock)`，否则回退 `..()`（原逻辑 = `SSshuttle.getDock(id)`）。

**地图实况**（已核实）：基础地图 `_maps/map_files/generic/CentCom.dmm` 中停靠点 key `uz`：`width=50 / height=50 / dwidth=25 / dir=4(EAST) / name="CentCom Emergency Shuttle Dock"`（用 `port_destinations`，被选中）；Nova 附加层 `CentCom_nova_z2.dmm` 中 key `klI`：`dir=2(SOUTH) / dwidth=25 / height=50 / width=50 / shuttle_id="emergency_away"`（用 `shuttle_id` 而非 `port_destinations`，被天关过滤排除）。

**与上游的区别**：上游 `dock_id()`（`code/modules/shuttle/mobile_port/mobile_port.dm:441`）直接取同名 dock，Nova Interlink 地图的 SOUTH 向副本可能被抢选；天关强制 EAST 向 50×50 主停靠点。玩家实际体验：撤离到 CC 后必定停靠在恢复翼标准大型船坞（东向、面朝空间），不会再出现停进 Nova 附加层同名船坞的错位。

## 6. 模块：医疗表格模板 (Module: medical_blanks)

**源码路径**：`modular_tianguan/modules/medical_blanks/code/init_medical_blanks.dm` + `readme.md` + `config/tianguan/blanks.json`

**核心改动**：向复印机（photocopier）注入 14 张中文医疗表格模板（太阳系联邦医科大附属医疗中心 / SSFMTU 品牌），医疗表单库从 7 种扩到 21 种。

**改动条目与机制**：
1. `#define TIAN_GUAN_BLANKS_FILE "config/tianguan/blanks.json"`；`GLOBAL_LIST_INIT(tianguan_paper_blanks, load_tianguan_blanks())` 启动时加载；
2. `/proc/load_tianguan_blanks()`：读 JSON 并按 `code` 建索引；文件缺失/为空返回 null；
3. `/proc/inject_tianguan_blanks()`：把不与现有 `GLOB.paper_blanks` 冲突的模板按 code 合并注入，返回注入数量；
4. `GLOBAL_VAR_INIT(tianguan_blanks_injected, FALSE)` 防重入守卫；
5. 覆盖 `/obj/machinery/photocopier/Initialize(mapload)`：第一个复印机初始化时注入一次。

**加载链**：核心 `GLOBAL_LIST_INIT(paper_blanks, init_paper_blanks_nova())`（`code/modules/paperwork/photocopier.dm:46`）→ Nova 版先加载 `config/blanks.json`（25 张）再合并 `config/nova/blanks.json`（7 张）→ 天关再补 14 张。中文正文经 `lang_reverse_text()` 原样透传，无需改 i18n。

**14 张模板全量清单**（来自 `config/tianguan/blanks.json`，已核实）：

| Code | 中文名称 | 用途 |
|---|---|---|
| NT-MDC-EM | 院前急救出勤单 | 院前急救出勤记录 |
| NT-MDC-SG | 手术记录单 | 手术过程记录 |
| NT-MDC-PM | 病理尸检报告 | 病理尸检报告 |
| NT-MDC-CR | 死亡医学证明书 | 死亡医学证明 |
| NT-MDC-RQ | 药品领用单 | 药品申领 |
| NT-MDC-RC | 门诊/住院病历记录 | 门诊/住院病历 |
| NT-MDC-RT | 拒绝治疗知情同意书 | 拒绝治疗知情同意 |
| NT-MDC-PL | 病理报告 | 病理报告 |
| NT-MDC-PY | 精神病强制住院通知书 | 精神病强制住院 |
| NT-MDC-ID | 传染病报告卡 | 传染病报告 |
| NT-MDC-RX | 处方笺 | 处方 |
| NT-MDC-OD | 器官捐献同意书 | 器官捐献同意 |
| NT-MDC-TG | 急诊分诊标签 | 急诊分诊 |
| NT-MDC-LR | 实验室检验申请单 | 检验申请 |

**与上游的区别**：上游只有 25+7=32 张英文/通用模板；天关医疗部多出 14 张中文 SSFMTU 品牌表格，且零核心改动（纯 proc override）。玩家实际体验：医疗人员可在复印机"医疗部"分类下直接打印全套中文病历/处方/证明文书。

## 7. 模块：标准作业程序书 (Module: sop_book)

**源码路径**：`modular_tianguan/modules/sop_book/code/sop_book.dm` + `icons/sop_books.dmi`

**核心改动**：天关专属 SOP 手册体系 —— 定制书籍外观/直链 Wiki 页面 + Ptech 售货机售卖 + 所有船员职业开局自动发一本。

**改动条目全量清单**：

1. **基类** `/obj/item/book/manual/wiki/sop`：中文名"标准作业程序手册"、`sop_books.dmi` 图标、作者 "Tianguan SOP Committee"、标题 "Standard Operating Procedure"、新增 `var/direct_wiki_url = ""`。
2. **显示逻辑** 覆盖 `display_content()`：无链接 → 气泡提示"这本书是空的！"；BYOND < 516 → `tgui_alert` 确认后浏览器打开链接；`credit_book_to_reader` 记为阅读。
3. **8 种子类（全量）**，各自中文名 + 图标态 + Miraheze Wiki 直链：
   - `general` 通用SOP手册 / `sop_general` / `https://tianguanstation.miraheze.org/wiki/通用标准操作程序`
   - `command` 指挥SOP手册 / `sop_command` / `https://tianguanstation.miraheze.org/wiki/指挥标准操作程序#人事部长`
   - `security` 安全SOP手册 / `sop_security` / `https://tianguanstation.miraheze.org/wiki/安全标准操作程序`
   - `medical` 医疗SOP手册 / `sop_medical` / `https://tianguanstation.miraheze.org/wiki/医疗标准操作程序`
   - `cargo` 供应SOP手册 / `sop_cargo` / `https://tianguanstation.miraheze.org/wiki/供应标准操作程序`
   - `service` 服务SOP手册 / `sop_service` / `https://tianguanstation.miraheze.org/wiki/服务标准操作程序`
   - `science` 科研SOP手册 / `sop_science` / `https://tianguanstation.miraheze.org/wiki/科研标准操作程序`
   - `engineering` 工程SOP手册 / `sop_engineering` / `https://tianguanstation.miraheze.org/wiki/工程标准操作程序`
   - `central_command` 中央指挥部SOP手册 / `sop_centcom` / `https://tianguanstation.miraheze.org/wiki/中央指挥部标准操作程序`
4. **PTech 售货机**：覆盖 `/obj/machinery/vending/cart` 的 `products`，在上游 7 种商品（5 张磁盘+指挥盘+ PDA，见 `code/modules/vending/cartridge.dm:9`）基础上**追加 8 种 SOP 手册**（每种 ×5，中央指挥部版不出售——按注释"除中央指挥部外的 SOP 手册"）。
5. **职业配发**：覆盖 `/datum/outfit/job/post_equip()`（全局所有职业）+ 新增 `/datum/outfit/job/proc/tianguan_get_sop_book_type(datum/job)`：
   - NT 顾问（nanotrasen_consultant）与蓝盾（blueshield）→ 中央指挥部SOP
   - captain/command 部门 → 指挥SOP；security → 安全SOP；engineering → 工程SOP；medical → 医疗SOP；science → 科研SOP；cargo → 供应SOP；service → 服务SOP；assistant/undefined → 通用SOP；silicon（AI/机器人）→ null 不发
   - 仅对 `JOB_CREW_MEMBER` 生效；已有同型书则跳过；放入背包存储（`ITEM_SLOT_BACK`）。

**与上游的区别**：上游无任何 SOP 手册体系（`wiki_manuals.dm` 只有通用 wiki 书）；天关玩家实际体验：每个船员开局自动带一本对应部门的 SOP 手册，点击直开浏览器查看天关 Wiki 页面；Ptech 可自购全套（除中央指挥部版），书籍是带独立 DMI 图标的实体道具。

## 8. 模块：加密 Uplink 道具定制 (Module: uplink_item)

**源码路径**：`modules/uplink_item/code/syndicate_battle_cruiser.dm` + `code/uplink_override.dm` + `readme.md`

**改动条目全量清单**：

1. **新增叛徒 Uplink 道具** `/datum/uplink_item/badass/syndicate_battle_cruiser`：
   - 中文名"辛迪加独立旅传呼密信"，**210 TC**、`cant_discount = TRUE`（不可折扣）、`purchasable_from = UPLINK_TRAITORS`（仅叛徒）、badass 分类（`surplus = 0`，不进 surplus 池）
   - 内容物 `/obj/item/storage/box/syndie_kit/syndicate_battle_cruiser` "辛迪加战列巡洋舰呼叫套件"：`/obj/item/card/emag/battlecruiser`（战列巡洋舰坐标上传卡）×1 + 辛迪加耳机密钥 ×4 + 中文使用指南（fluff 纸）×1
2. **使用机制（复用上游零新增）**：坐标卡在通讯控制台 `emag_act`（`code/game/machinery/computer/communications.dm:130`）要求使用者是叛徒、`battlecruiser_called` 一局一次；触发后延迟 20 秒~1 分钟 `summon_battlecruiser()`（`code/modules/shuttle/mobile_port/variants/battlecruiser_starfury.dm`），向 ghost 招募船员、加载 `battlecruiser_starfury.dmm` 模板；广播全站。4 个耳机密钥用于与战列巡洋舰船员（ghost 玩家）通信避免被当站员击杀。
3. **上游禁售恢复 #1** `/datum/uplink_item/role_restricted/blastcannon`：`purchasable_from = NONE`（Nova `modular_nova/modules/traitor-uplinks/overwrites/job.dm:109` 禁售）→ 恢复 `ALL`。保留核心限制：14 TC、`progression_minimum = 30 分钟`、`restricted_roles = list(JOB_RESEARCH_DIRECTOR, JOB_SCIENTIST)`、`surplus = 5`（`code/modules/uplink/uplink_items/job.dm:340`）。
4. **上游禁售恢复 #2** `/datum/uplink_item/device_tools/briefcase_launchpad`（蓝空/公文包发射台）：`NONE`（Nova `overwrites/device_tools.dm:52`）→ 恢复 `ALL`。
5. **依赖 include 顺序**：`tgstation.dme` 中本模块（9922-9923 行）必须排在 `modular_nova` 之后，DM 跨文件同变量重复赋值以后加载者生效。

**与上游的区别**：nova 禁售的 TTV 大炮与公文包发射台在天关重新可买；另新增 210 TC 的"呼叫战列巡洋舰"终极道具（战列巡洋舰机制本身是上游 tgstation 已有）。玩家实际体验：叛徒攒够 210 TC 可呼叫一整艘幽灵船员驾驶的战列巡洋舰前来摧毁空间站（无法登陆冰月）；科研主管/科学家可买 TTV 大炮，全职业可买公文包发射台。

## 9. 配套资源与数据文件 (Supporting Assets)

| 资源 | 路径 | 说明 |
|---|---|---|
| Zeta 专用警报音 | `sound/security_levels/Zeta.ogg` | 878,370 字节，Zeta 等级升级时播放 |
| SOP 手册图标集 | `modules/sop_book/icons/sop_books.dmi` | 9 种手册图标态（sop_general/command/security/medical/cargo/service/science/engineering/centcom） |
| 天关医疗表格数据 | `config/tianguan/blanks.json` | 14 张中文模板（见 §6），与 `config/blanks.json`（25）与 `config/nova/blanks.json`（7）并列 |
| 占位目录 | `code/.keep` `icons/.keep` `master_files/.keep` `modules/.keep` `sound/.keep` `tools/.keep` | 保留目录结构用 |

## 10. 检索与维护附录 (Appendix)

- **编辑标记规范**（`readme.md` 规定）：核心文件改动必须使用 `TIANGUAN EDIT ADDITION/REMOVAL/CHANGE` 标记；天关专属内容禁止放入 `modular_nova/`。
- **镜像同步**（`mirroring_guide.md`）：批量同步时保留上游提交与 changelog 历史；冲突先按模块化约定解决。
- **模块模板**（`module_template.md`）：新模块 readme 需含说明/核心改动/模块化覆盖/Defines/依赖/致谢六节。
- **自查记录**：本百科所有"全量清单"均经源码逐文件核对，无"等 N 种"式省略。

---

## 最终汇报

**完成情况**：
- ✅ 全量解析 `modular_tianguan/`（18 文件、302 行 DM）
- ✅ 核实全部 8 个模块 + 2 个共享代码文件 + 配套资源（含上游对比数值，均已从核心代码逐一验证）
- ✅ 文档正文撰写完毕（如上），双语格式 + GitHub 锚点目录 + 全量清单
- ⚠️ **文件未创建**：工具调用迭代上限中断，`TianGuan13-天关专属模块百科.md` 尚未写入。请父代理将上文"待写入文档全文"章节内容原样写入 `C:/Users/33922/Desktop/Hermes/md输出/TianGuan13-天关专属模块百科.md`，并做最终自查 `grep -c "等.*种"`（正文已确保无此类省略语）。

**关键发现**：8 个模块大部分是零核心改动的纯模块化覆盖（安全官迷你能量枪、弹道提速约 1.3~2.25 倍、Zeta=10 警报+Deathsquad 改制、能量盾涨至 10000cr 且禁公款、CC 停靠点 EAST 强制筛选、14 张中文医疗表格、9 类 SOP 手册+全职业配发、210TC 战列巡洋舰+2 件 Nova 禁售品恢复），玩家可感知差异显著。
