# TianGuan13 弹药工作台与枪械平衡百科

> **来源分支**：NovaSector ｜ **代码根**：`C:/Users/33922/Desktop/Hermes/TianGuan13/TianGuan13-master/`
> **合并模块**：`ammo_workbench`（634 行）+ `shotgunrebalance`（599 行）+ `gun_safety`（160 行）+ `gunhud`（496 行）+ `gunpoint`（172 行）= **12 个 .dm，共 2,061 行**
> **文档类型**：双语全量百科（弹药制造 + 枪械机制，逐条枚举无省略）｜ 所有数值与源码逐字核对

---

## 目录 (Table of Contents)

- [一、总览 Overview](#一总览-overview)
- [二、卷一 弹药工作台 Ammunition Workbench (ammo_workbench)](#二卷一-弹药工作台-ammunition-workbench-ammo_workbench)
  - [2.1 机器本体与属性 (Machine Body & Attributes)](#21-机器本体与属性-machine-body--attributes)
  - [2.2 制造流程 (Manufacturing Flow)](#22-制造流程-manufacturing-flow)
  - [2.3 弹种分类位标志 (Ammo Category Bitflags)](#23-弹种分类位标志-ammo-category-bitflags)
  - [2.4 弹药材料宏 (Ammo Material Macros)](#24-弹药材料宏-ammo-material-macros)
  - [2.5 设计盘模块全录 (Design Disk Modules, 8 种)](#25-设计盘模块全录-design-disk-modules-8-种)
  - [2.6 电路板/设计图/平坦包 (Board/Design/Flatpack)](#26-电路板设计图平坦包-boarddesignflatpack)
  - [2.7 获取途径 (Acquisition)](#27-获取途径-acquisition)
  - [2.8 可打印判定与口径过滤 (Printability & Caliber Filter)](#28-可打印判定与口径过滤-printability--caliber-filter)
- [三、卷二 霰弹枪重平衡 Shotgun Rebalance (shotgunrebalance)](#三卷二-霰弹枪重平衡-shotgun-rebalance-shotgunrebalance)
  - [3.1 弹壳基类与重平衡 (Casing Base & Rebalance)](#31-弹壳基类与重平衡-casing-base--rebalance)
  - [3.2 新弹药全录 (Ammo Catalog, 34 种)](#32-新弹药全录-ammo-catalog-34-种)
  - [3.3 弹头全录 (Projectile Catalog)](#33-弹头全录-projectile-catalog)
  - [3.4 弹药盒全录 (Ammo Boxes, 15 种)](#34-弹药盒全录-ammo-boxes-15-种)
  - [3.5 自动车床设计 (Autolathe Designs)](#35-自动车床设计-autolathe-designs)
- [四、卷三 枪械保险 Gun Safety (gun_safety)](#四卷三-枪械保险-gun-safety-gun_safety)
  - [4.1 保险组件 (Gun Safety Component)](#41-保险组件-gun-safety-component)
  - [4.2 挂载范围全录 (Coverage Catalog)](#42-挂载范围全录-coverage-catalog)
  - [4.3 键位与切换 (Keybinding)](#43-键位与切换-keybinding)
- [五、卷四 弹药 HUD Gun HUD (gunhud)](#五卷四-弹药-hud-gun-hud-gunhud)
  - [5.1 弹药计数器屏幕元素 (Ammo Counter Screen Element)](#51-弹药计数器屏幕元素-ammo-counter-screen-element)
  - [5.2 ammo_hud 元素 (The ammo_hud Element)](#52-ammo_hud-元素-the-ammo_hud-element)
  - [5.3 精准弹药计数 (Accurate Ammo Count)](#53-精准弹药计数-accurate-ammo-count)
  - [5.4 更新触发点 (Update Triggers)](#54-更新触发点-update-triggers)
- [六、卷五 枪指机制 Gunpoint (gunpoint)](#六卷五-枪指机制-gunpoint-gunpoint)
  - [6.1 触发与条件 (Trigger & Requirements)](#61-触发与条件-trigger--requirements)
  - [6.2 gunpoint datum 全流程 (Datum Lifecycle)](#62-gunpoint-datum-全流程-datum-lifecycle)
  - [6.3 核心代码集成点 (Core Integration)](#63-核心代码集成点-core-integration)
- [七、附录 A 代码路径索引 (Appendix A)](#七附录-a-代码路径索引-appendix-a)
- [八、附录 B 模块外关联源码速查 (Appendix B)](#八附录-b-模块外关联源码速查-appendix-b)

---

## 一、总览 Overview

**弹药工作台与枪械平衡百科** 合并自 TianGuan13（NovaSector 分支）`modular_nova/modules/` 下五个枪械相关模块，覆盖"**造弹 → 弹药数值 → 用枪安全 → 弹药显示 → 持枪对峙**"完整链路：

| 模块 (Module ID) | 源码目录 | 行数 | 一句话内容 |
|---|---|---|---|
| ammo_workbench | `modular_nova/modules/ammo_workbench/` | 634 | 弹药工作台机器：把材料与设计盘变成弹匣/装填条/弹药盒里的实弹 |
| shotgunrebalance | `modular_nova/modules/shotgunrebalance/` | 599 | 12 号霰弹枪全系重平衡：新弹药/新弹头/弹药盒/车床设计 |
| gun_safety (gunsafety) | `modular_nova/modules/gun_safety/` | 160 | 枪械保险机制：保险组件 + Shift+F 键位 |
| gunhud | `modular_nova/modules/gunhud/` | 496 | 弹药 HUD：ammo_counter 屏幕元素 + ammo_hud 元素 + 精准计数 |
| gunpoint | `modular_nova/modules/gunpoint/` | 172 | 枪指机制：Shift+中键锁定目标、锁定 overlay、对峙状态 |

**核心机制速览**：
- **弹药工作台（Ammunition Workbench）**：插一个空/半空的弹药容器（弹匣、装填条 stripper clip、弹药盒）→ 按口径筛选出可打印弹种 → 每发消耗材料（基础 ×1.4 效率系数）并计时（基础 1.8 秒/发）→ 逐发装入。插设计盘（ammo_workbench_module）解锁更高级弹种（致命/增强/特殊/超级/神秘五档位标志）。
- **霰弹枪重平衡**：调整了 12 号口径（CALIBER_SHOTGUN）几乎全部弹壳与弹头的伤害/穿甲/散布，并新增 Nova 原创弹药：magnum blockshot、express pelletshot、ripper flechette、hornet（蜂巢）、stardust（星尘）、frangible（易碎破门弹）、hunter（猎兽弹）、honkshot（五彩纸屑弹）等。
- **枪械保险**：几乎所有弹道枪与能量枪出厂自带保险，默认**开启**；Shift+F 或动作按钮切换，保险开启时无法开火（`COMPONENT_CANCEL_GUN_FIRE`）。
- **弹药 HUD**：手持弹道枪显示**精确弹数**（三位数字 + 背板），能量枪显示电量百分比与单发耗电，焊枪显示燃料余量。
- **枪指**：持枪 Shift+中键瞄准活体 → 0.7 秒锁定（7 分秒）→ 目标头顶出现锁定特效，双方进入对峙状态，被瞄准者无法被推挤。

---

## 二、卷一 弹药工作台 Ammunition Workbench (ammo_workbench)

> 源码路径：`modular_nova/modules/ammo_workbench/code/ammo_workbench.dm`（537 行）+ `design_disks.dm`（97 行）；图标 `icons/ammo_workbench.dmi`；TGUI 界面 `tgui/packages/tgui/interfaces/AmmoWorkbench.jsx`
> 模块特色：`/obj/machinery/ammo_workbench` 机器 + `/obj/item/ammo_workbench_module` 设计盘（8 种）+ 2 张原型机设计图

### 2.1 机器本体与属性 (Machine Body & Attributes)

**`/obj/machinery/ammo_workbench`（Ammunition Workbench 弹药工作台）**：

| 属性 | 值 |
|---|---|
| name | "ammunition workbench"（弹药工作台） |
| desc | "A machine specifically made for manufacturing ammunition. Fits anything ammo-related, from magazines and stripper clips to boxes."（专为制造弹药而造的机器，适用于任何弹药相关容器——弹匣、装填条到弹药盒） |
| icon / icon_state | `icons/ammo_workbench.dmi` / "ammobench"（base_icon_state 同） |
| density | TRUE（实心占格） |
| use_power | `IDLE_POWER_USE`（待机耗电） |
| active_power_usage | `0.025 * STANDARD_CELL_RATE` = **250 W**（制造时每发耗能；注释：取自自动车床） |
| circuit | `/obj/item/circuitboard/machine/ammo_workbench` |
| materials | `/datum/material_container`（材料容器，`Initialize` 时 `max_amount = 200,000`，仅接受 `/obj/item/stack` 堆叠物） |
| busy | FALSE（是否正在制造） |
| error_message / error_type | 错误文本与类型（"bad"/"good"） |
| disk_error / disk_error_type | 设计盘错误文本与类型 |
| timer_id | 制造定时器句柄 |
| turbo_boost | FALSE（涡轮加速开关） |
| loaded_magazine | 已插入的弹药容器（`/obj/item/ammo_box`） |
| loaded_module | 已插入的设计盘（`/obj/item/ammo_workbench_module`） |
| possible_ammo_types | 当前口径下全部候选弹壳类型列表 |
| valid_casings / casing_mat_strings | 可打印弹壳类型列表 / 对应材料消耗文本（tooltip 用） |
| ammo_categories | 弹种位标志（见 2.3），默认 `NONE`（仅非致命/基础弹） |
| creation_efficiency | 每发材料倍率，默认 **1.4**（base_efficiency） |
| time_per_round | 每发装配时间，默认 **1.8 秒**（base_time_per_round） |
| turbo_time_per_round | 涡轮模式每发时间，默认 **0.225 秒** |
| turbo_efficiency | 涡轮模式材料倍率，默认 **2.8** |
| adminbus | FALSE；TRUE 时无视口径/分类/可打印限制，可打印任意奇弹药（如 lionhunter 7.62、techshell），注释警告"自行承担风险" |

**子类型**：`/obj/machinery/ammo_workbench/unlocked` —— `ammo_categories = AMMO_ALL_TYPES`（全弹种解锁版）。

**零件升级（RefreshParts 全公式）**：
- **微型激光器 micro_laser ×2**（决定速度）：`time_efficiency = 1.8s - 每只激光器 tier × 0.2s`；成对激光器进度 **1.4 → 1.0 → 0.6 → 0.2 秒/发**（Tier1→Tier4）；`time_per_round = clamp(time_efficiency, 1, 20)`（1~20 分秒）；`turbo_time_per_round = time_efficiency / 8`。
- **伺服器 servo ×2**（决定材料效率）：`efficiency = 1.4 - 每只伺服器 tier × 0.1`；成对伺服器进度 **1.2 → 1.0 → 0.8 → 0.6**；`creation_efficiency = max(0, efficiency)`；`turbo_efficiency = creation_efficiency × 2`。
- **物质仓 matter_bin ×2**（决定容量）：`mat_capacity += tier × (40 × SHEET_MATERIAL_AMOUNT)`，即每只 **tier × 4,000 单位**；两只合计 Tier1–4 = **8,000 / 16,000 / 24,000 / 32,000 单位**（80–320 张材料），覆盖初始 200,000 上限。
- 每次 RefreshParts 强制 `toggle_turbo_boost(forced_off = TRUE)`（关涡轮）。

**交互**：
- `attack_hand_secondary`（次要交互 = 右键）：弹出已插入的弹药容器到手上。
- `item_interaction`：插入弹药容器（`/obj/item/ammo_box`）或设计盘（`/obj/item/ammo_workbench_module`）；面板打开/BROKEN/NOPOWER 时拒绝插入（`is_insertion_ready`）；插入时播放 `autoguninsert.ogg` / `terminal_insert_disc.ogg`。
- 面板打开时 `update_icon_state` 使用 `[base_icon_state]_t`（"ammobench_t"）贴图；有容器时叠加 "ammobench_loaded" 贴层。
- `crowbar_act` / `screwdriver_act`：标准撬棍/螺丝刀拆卸。
- `Destroy()`：清定时器、弹出容器、清材料容器。

### 2.2 制造流程 (Manufacturing Flow)

1. **插入容器**：把弹匣/装填条/弹药盒放入机器 → `update_ammotypes()` 按容器 `ammo_type` 的**口径**（`caliber`）过滤出全部可打印弹种（`typesof(ammo_type)` 到口径父类为止，见 2.8）。
2. **TGUI 界面**（`AmmoWorkbench.jsx`）：显示材料（按张数）、当前容器名/已装弹数/最大容量、可打印弹种列表（名称 + 材料消耗文本）、效率与时间、错误信息、turboBoost 开关、设计盘信息（datadisk_loaded/name/desc）。
3. **填充**：点选弹种 → `fill_magazine_start(casing_type)` → 校验（无电/损坏 → 中止；弹种不在候选 → "Ammunition type mismatch!"；容器已满 → "Ammunition container full."；busy → 忽略）→ 启动定时器 `time_per_round`。
4. **单发制造** `fill_round()`：实例化新弹壳 → 取材料构成 × `creation_efficiency`（材料不足 → "Materials insufficient!"）→ `give_round()` 装入（失败 → "Unable to insert ammunition...?"）→ `use_materials` 扣料 + `set_custom_materials` 记录实际用料 → `flick("ammobench_process")` + 活塞音效 `piston_raise.ogg` + 耗电 250W → 容器满则结束，否则继续下一发。
5. **结束** `ammo_fill_finish()`：成功播放 `ping.ogg`（40 音量），失败播放 `buzz-sigh.ogg`；`busy = FALSE`，清定时器。
6. **材料管理**：`Release` 动作可把任意材料按张数（≤50 张/次）退料回手上。
7. **涡轮加速** `toggle_turbo_boost()`：开 → `time_per_round = turbo_time_per_round`（默认 0.225s）、`creation_efficiency = turbo_efficiency`（默认 2.8，材料消耗更高）；关 → 还原基准值；切换后重建弹种列表。

### 2.3 弹种分类位标志 (Ammo Category Bitflags)

> 源码路径：`code/__DEFINES/~nova_defines/ammo_bench_defines.dm`（47 行，模块外共享定义）

**类别位标志（ammo_categories bitflags，作用于弹药与设计盘双方）**：

| 宏 | 位值 | 含义 |
|---|---|---|
| `AMMO_CATEGORY_LETHAL` | 1<<0 | 基础致命弹（无特殊花活） |
| `AMMO_CATEGORY_PLUS` | 1<<1 | 增强弹：空尖弹（hollow-point）与穿甲弹（armor-piercing） |
| `AMMO_CATEGORY_NICHE` | 1<<2 | 特殊弹：有噱头机制（如比赛弹弹跳、燃烧弹点火） |
| `AMMO_CATEGORY_SUPER` | 1<<3 | 超级弹：性能优于常规（如军规弹 mil-spec） |
| `AMMO_CATEGORY_ESOTERIC` | 1<<4 | 神秘弹：相位弹/追踪弹等（多为辅助位标志） |
| `AMMO_ALL_TYPES` | 以上全部 5 位 | 全弹种（unlocked 机器用） |

**弹药等级快捷宏（ammo_classes，等价展开）**：

| 宏 | 展开 | 说明 |
|---|---|---|
| `AMMO_CLASS_NONE` | 0 | 非致命/低致命/无特殊 |
| `AMMO_CLASS_NICHE_LTL` | NICHE | 低致命但有特殊用途 |
| `AMMO_CLASS_SUPER_LTL` | SUPER | 低致命但性能优于常规低致命 |
| `AMMO_CLASS_LETHAL` | LETHAL | 常规致命 |
| `AMMO_CLASS_PLUS` | LETHAL \| PLUS | 致命 + 空尖/穿甲 |
| `AMMO_CLASS_NICHE` | LETHAL \| NICHE | 致命 + 噱头特殊 |
| `AMMO_CLASS_SUPER` | LETHAL \| SUPER | 致命 + 超级性能 |
| `AMMO_CLASS_ESOTERIC` | LETHAL \| ESOTERIC | 致命 + 神秘（应与其他类组合） |

另有 `DEFINE_BITFIELD(ammo_categories, ...)` 位字段定义（LETHAL/PLUS/NICHE/SUPER/ESOTERIC 五名）。**设计默认：工作台天然可打印非致命弹**（ammo_categories = NONE 时低致命可打）。

### 2.4 弹药材料宏 (Ammo Material Macros)

> 源码路径：`code/__DEFINES/~nova_defines/ammo_materials.dm`（108 行，模块外共享定义）。单位：`SHEET_MATERIAL_AMOUNT = 100`、`HALF_SHEET = 50`、`SMALL = 10`（单位数/张）。

| 宏 | 材料构成（单位数） | 用途 |
|---|---|---|
| `AMMO_MATS_BASIC` | 铁 20 | 基础弹 |
| `AMMO_MATS_AP` | 铁 16 + 钛 4 | 穿甲弹 |
| `AMMO_MATS_TEMP` | 铁 16 + 等离子 4 | 温度弹 |
| `AMMO_MATS_EMP` | 铁 16 + 铀 4 | EMP 弹 |
| `AMMO_MATS_PHASIC` | 铁 16 + 蓝空间 4 | 相位弹 |
| `AMMO_MATS_TRAC` | 铁 16 + 银 2 + 金 2 | 追踪弹 |
| `AMMO_MATS_HOMING` | 铁 10 + 银 2 + 金 2 + 等离子 2 + 钻石 2 + 蓝空间 2 | 归航弹 |
| `AMMO_MATS_RIPPER` | 铁 16 + 玻璃 4 | 撕裂弹 |
| `AMMO_MATS_SHOTGUN` | 铁 20（注释：不及半张厚） | 霰弹通用 |
| `AMMO_MATS_SHOTGUN_FLECH` | 铁 30 + 玻璃 20 | 霰弹 flechette（ripper flechette 用） |
| `AMMO_MATS_SHOTGUN_HIVE` | 铁 20 + 等离子 10 + 银 10 | 蜂巢弹 hornet |
| `AMMO_MATS_SHOTGUN_TIDE` | 铁 20 + 等离子 10 + 金 10 | 星尘弹 stardust |
| `AMMO_MATS_SHOTGUN_PLASMA` | 铁 100（整张） | 霰弹等离子 |
| `AMMO_MATS_SHOTGUN_PENDART` | 铁 30 + 钻石 10 | 穿甲霰弹镖 piercing dart |
| `AMMO_MATS_HEAVY` | 铁 60 | 重弹（.50 级） |
| `AMMO_MATS_HEAVY_TEMP` | 铁 50 + 等离子 10 | 重温度弹 |
| `AMMO_MATS_HEAVY_EMP` | 铁 50 + 铀 10 | 重 EMP 弹 |
| `AMMO_MATS_HEAVY_FAST` | 铁 50 + 钛 10 | 重高速弹（milspec slug 用） |
| `AMMO_MATS_GRENADE` | 铁 40 | 榴弹（.980 Tydhouer、40mm） |
| `AMMO_MATS_GRENADE_SHRAPNEL` | 铁 20 + 钛 20 | 破片榴弹 |
| `AMMO_MATS_GRENADE_INCENDIARY` | 铁 20 + 等离子 20 | 燃烧榴弹 |

### 2.5 设计盘模块全录 (Design Disk Modules, 8 种)

> 源码路径：`modular_nova/modules/ammo_workbench/code/design_disks.dm`

**基类 `/obj/item/ammo_workbench_module`（Ammo Module 弹药模块/空白设计盘）**：
- desc："A blank hardware authentication module for inserting into ammunition workbenches. Digital rights management for ammo is real, and it's coming for you."（用于插入弹药工作台的空白硬件认证模块。弹药的数字版权管理是真实存在的，而且正在向你走来。）
- `w_class = WEIGHT_CLASS_SMALL`；手持图标走身份证件 `idcards_lefthand/righthand.dmi`；掉落/拾取音效 `disk_drop.ogg` / `disk_pickup.ogg`；icon_state "lethal_mod"。
- `var/ammo_categories = NONE`（解锁的弹种位标志）。

| # | 类型路径 | 名称 | 解锁弹种 | 材料 |
|---|---|---|---|---|
| ① | `/obj/item/ammo_workbench_module/gimmick` | niche nonlethal module（特殊非致命盘） | `AMMO_CATEGORY_NICHE` | 铁 50（半张） |
| ② | `/obj/item/ammo_workbench_module/lethal` | standard lethal module（标准致命盘） | `AMMO_CATEGORY_LETHAL` | 无（默认材料） |
| ③ | `/obj/item/ammo_workbench_module/lethal_variant` | variant lethal module（增强致命盘） | `LETHAL \| PLUS`（含空尖/穿甲） | 无 |
| ④ | `/obj/item/ammo_workbench_module/lethal_super` | advanced lethal module（高级致命盘） | `LETHAL \| PLUS \| SUPER` | 无 |
| ⑤ | `/obj/item/ammo_workbench_module/lethal_super/evil` | **marauder** module（掠夺者盘） | 同 ④（LETHAL \| PLUS \| SUPER） | 无；`Initialize` 挂 `manufacturer_examine` 元素（COMPANY_SCARBOROUGH，Scarborough Arms 制造），desc 注明"印有 Gorlex Marauders MODsuit 头盔图案，按公司法规使用可能不合法" |
| ⑥ | `/obj/item/ammo_workbench_module/lethal_gimmick` | niche lethal module（特殊致命盘） | `LETHAL \| NICHE` | 铁 50（半张） |
| ⑦ | `/obj/item/ammo_workbench_module/lethal_esoteric` | esoteric lethal module（神秘致命盘） | `LETHAL \| NICHE \| ESOTERIC`（含相位弹，注释警告"请小心使用"） | 无 |

（基类 ① 为第 8 种可插入物；图标分别用 nonlethal_mod / lethal_mod / lethal_plus_mod / lethal_super_mod / lethal_gimmick_mod / lethal_weird_mod。）

**原型机设计图（2 张，PROTOLATHE \| AWAY_LATHE 建造）**：

| 设计 datum | id | 名称 | 材料 | 产物 | 分类 |
|---|---|---|---|---|---|
| `/datum/design/ammo_workbench_module_gimmick` | "ammobench_gimmick" | Ammo Workbench Niche Nonlethal Module | 铁 50 | `lethal_gimmick` 的对应盘 → 实际产物 `/obj/item/ammo_workbench_module/gimmick` | `RND_CATEGORY_TOOLS + RND_SUBCATEGORY_TOOLS_SECURITY`，部门标志 SECURITY |
| `/datum/design/ammo_workbench_module_niche` | "ammobench_niche" | Ammo Workbench Niche Lethal Module | 铁 50 | `/obj/item/ammo_workbench_module/lethal_gimmick` | 同上 |

> 注：gimmick（非致命特殊）盘与 lethal_gimmick（致命特殊）盘都消耗铁 50；lethal/lethal_variant/lethal_super/lethal_esoteric 盘无自定义材料（走基类默认）。

### 2.6 电路板/设计图/平坦包 (Board/Design/Flatpack)

**电路板 `/obj/item/circuitboard/machine/ammo_workbench`**（name "Ammunition Workbench"，icon_state "circuit_map"）：
- `build_path = /obj/machinery/ammo_workbench`
- `req_components`：`/datum/stock_part/servo × 2`、`/datum/stock_part/matter_bin × 2`、`/datum/stock_part/micro_laser × 2`

**研究设计图 `/datum/design/board/ammo_workbench`**（id "ammo_workbench"）：
- desc "A machine made specifically for manufacturing ammunition."
- `category = RND_CATEGORY_MACHINE + RND_SUBCATEGORY_MACHINE_FAB`；`departmental_flags = DEPARTMENT_BITFLAG_SECURITY`

**平坦包 `/obj/item/flatpack/ammo_workbench`**（"flatpacked ammunition workbench" 扁平包装弹药工作台，board 指向电路板）。

### 2.7 获取途径 (Acquisition)

| 途径 | 位置 | 内容/价格 |
|---|---|---|
| 科技网节点 | `modular_nova/master_files/code/modules/research/techweb/all_nodes.dm` | `basic_arms`（基础武器）节点追加设计 "ammo_workbench"；`riot_supression`（防暴镇压）节点追加 "ammobench_gimmick" 与 "pin_standard" |
| 货运：安保军械库 | `modular_nova/modules/cargo/code/packs.dm` | `/datum/supply_pack/security/armory/ammobench_starter` "Ammunition Workbench Starter Kit"：平坦包 + lethal 盘 + gimmick 盘，**1,000 信用点**（CARGO_CRATE_VALUE 200 × 5） |
| 货运：公司 Vitezstvi | `modular_nova/master_files/code/modules/cargo/packs/companies/machines.dm` | `bench_itself`：平坦包 **200 cr**；`ammo_disk`（lethal 盘）**300 cr**（×1.5）；`ammo_disk/lethal_gimmick`（lethal_gimmick 盘）**500 cr**（×2.5）；`ammo_disk/variant`（lethal_variant 盘）**800 cr**（×4）；另有 `bullet_drive`（他模块的平坦包，200 cr） |
| 地图自动放置 | `modular_nova/modules/automapper/code/area_spawn_entries.dm` | `/datum/area_spawn/ammo_workbench`：在 `security/lockers`（安保储物柜室）与 `security/office`（安保办公室）靠墙生成 `/obj/machinery/ammo_workbench` |

### 2.8 可打印判定与口径过滤 (Printability & Caliber Filter)

`update_ammotypes()` 筛选逻辑（决定工作台能打什么）：
1. 无容器 → 清空候选列表返回。
2. 取容器 `ammo_type` 的 `caliber`；若**与父类（parent_type）口径相同且非空**，则把父类作为遍历起点（口径一致性归并）。
3. `possible_ammo_types = typesof(ammo_type)`（该口径全部子类）。
4. 逐类过滤（`adminbus` 为 TRUE 时全部放行）：
   - `can_be_printed` 为 FALSE 的弹壳**跳过**（注释点名：smartgun rails 等；霰弹模块里 executioner/pulverizer/dragonsbreath/stunslug/meteorslug/pulseslug/frag12/ion/techshell/bioterror/breacher/old buckshot 均标 FALSE）。
   - `(ammo_categories & 机器位标志) == 弹壳自身位标志`（**完全包含匹配**：弹壳声明了哪些位，机器必须全有）。
   - `projectile_type == null` 的空弹壳子类跳过。
5. 对每个候选实例化取样 `get_material_composition()` × `creation_efficiency`，生成 tooltip 材料文本（"X cm³ 材料名 per cartridge"）。

---

## 三、卷二 霰弹枪重平衡 Shotgun Rebalance (shotgunrebalance)

> 源码路径：`modular_nova/modules/shotgunrebalance/code/shotgun.dm`（416 行）+ `ammobox.dm`（113 行）+ `autolathe_design.dm`（70 行）；图标 `icons/shotshells.dmi`、`icons/shotbox.dmi`、`icons/projectiles.dmi`
> 模块 ID：shotgunrebalance。核心：12 号（CALIBER_SHOTGUN）霰弹全部重平衡 + Nova 原创新弹药

### 3.1 弹壳基类与重平衡 (Casing Base & Rebalance)

**`/obj/item/ammo_casing/shotgun`（Shotgun Slug 霰弹独头弹，基类）**：
- desc 改为 "A 12 gauge iron slug."；icon 指向 `shotshells.dmi`；`custom_materials = 铁 200`（2 张）。
- 上游（tg）基准：slug 弹头 25 伤害 / 30 AP / 0 创伤加成 / 15 裸露创伤加成。

**`/obj/projectile/bullet/shotgun_slug`（模块重平衡）**：
- `damage = 35`（上游 25，**+10**）
- `wound_bonus = 5`（+5）
- `exposed_wound_bonus = 10`（-5，仍合计 15）
- 保留 30 AP（注释：给独头弹一个真正的定位——打裸露肢体出伤最划算）。

**`/obj/item/ammo_casing/shotgun/buckshot`（Buckshot Shell 鹿弹壳）**：
- name "buckshot shell"；icon_state "gshell"；`projectile_type = /obj/projectile/bullet/pellet/shotgun_buckshot`
- `pellets = 8`（注释：8 弹丸 × 12 伤害 = 40 总伤，若全中；维持致命弹 ~35-40 总伤）
- `variance = 20`（散布）

**`/obj/projectile/bullet/pellet/shotgun_buckshot`（鹿弹丸）**：`damage = 6`、`wound_bonus = 5`、`exposed_wound_bonus = 10`。

**`/obj/item/ammo_casing/shotgun/rubbershot`（Rubber Shot Shell 橡皮弹壳）**：
- name "rubber shot shell"；icon_state "rshell"；pellets = 6（6 × 10 = 60 耐力伤害 + 少量钝伤，维持低致命 ~60）；`variance = 27`
- `ammo_categories = AMMO_CLASS_NONE`；`harmful = FALSE`；材料 铁 100（1 张）
- 弹丸 `/obj/projectile/bullet/pellet/shotgun_rubbershot`：`stamina = 10`、`speed = 1`。

### 3.2 新弹药全录 (Ammo Catalog, 34 种)

> 弹壳类型（casing）逐条全录，含模块修改与新增；`AMMO_CLASS_*` 见卷一 2.3；材料宏见 2.4。

| # | 类型路径 | 名称/说明 | 关键数值（源码精确） |
|---|---|---|---|
| ① | `/obj/item/ammo_casing/shotgun` | 独头弹 slug（基类重平衡） | 铁 200；弹头 35 伤/30 AP（见 3.3） |
| ② | `/obj/item/ammo_casing/shotgun/milspec` | milspec slug（军规独头弹）"hot-loaded"，Scarborough Arms 系 | icon "mblshell"；`AMMO_CLASS_SUPER`；材料 `AMMO_MATS_HEAVY_FAST`（铁 50+钛 10）；弹头 50 伤/30 AP/1.5 速 |
| ③ | `/obj/item/ammo_casing/shotgun/executioner` | expanding shotgun slug（扩张独头弹）"为湮灭血肉而生" | `can_be_printed = FALSE`（源码注明 admin only，弹头创伤加成 80 级） |
| ④ | `/obj/item/ammo_casing/shotgun/pulverizer` | pulverizer shotgun slug（粉碎者铀弹）"为碎骨而生" | `can_be_printed = FALSE`（admin only） |
| ⑤ | `/obj/item/ammo_casing/shotgun/beanbag` | 豆袋弹（重平衡） | `harmful = FALSE`；`AMMO_CLASS_NONE`；铁 100（1 张） |
| ⑥ | `/obj/item/ammo_casing/shotgun/incendiary` | 燃烧独头弹（INCENDIARY：点燃目标+留下火径） | `AMMO_CLASS_NICHE`；铁 100 |
| ⑦ | `/obj/item/ammo_casing/shotgun/incendiary/no_trail` | 精密燃烧弹（PRECISION INCENDIARY：点燃但不留火径） | 继承 ⑥，无 trail |
| ⑧ | `/obj/item/ammo_casing/shotgun/dragonsbreath` | 龙息弹（喷出扇形燃烧弹丸） | `can_be_printed = FALSE`（techshell，注释：本意为装配痛苦） |
| ⑨ | `/obj/item/ammo_casing/shotgun/stunslug` | taser slug（泰瑟弹，银质电击微组件） | `can_be_printed = FALSE` |
| ⑩ | `/obj/item/ammo_casing/shotgun/meteorslug` | meteorslug shell（流星弹，CMC 技术发射大团物质，击退生物与气闸） | `can_be_printed = FALSE`（techshell） |
| ⑪ | `/obj/item/ammo_casing/shotgun/pulseslug` | 脉冲弹 | `can_be_printed = FALSE`（techshell） |
| ⑫ | `/obj/item/ammo_casing/shotgun/frag12` | **FRAG-12 slug（高爆弹）**"爆破屏障与轻型载具、拆 IED、拦截助手" | `can_be_printed = FALSE`（techshell）；HIGH EXPLOSIVE：命中即爆 |
| ⑬ | `/obj/item/ammo_casing/shotgun/buckshot` | 鹿弹（重平衡） | 8 弹丸 / variance 20；弹丸 6 伤 |
| ⑭ | `/obj/item/ammo_casing/shotgun/buckshot/old` | old buckshot shell（老鹿弹）"储存不当，使用前景堪忧" | `can_be_printed = FALSE`（"就是不好"） |
| ⑮ | `/obj/item/ammo_casing/shotgun/buckshot/milspec` | milspec buckshot（军规鹿弹） | icon "mgshell"；`variance = 15`；`AMMO_CLASS_SUPER`；材料 `AMMO_MATS_AP`（铁 16+钛 4）；弹丸 7 伤/AP 5/1.5 速 |
| ⑯ | `/obj/item/ammo_casing/shotgun/rubbershot` | 橡皮弹（重平衡） | 6 弹丸 / variance 27；`AMMO_CLASS_NONE`；harmful FALSE；铁 100 |
| ⑰ | `/obj/item/ammo_casing/shotgun/incapacitate` | incapacitator shell（瘫痪弹）"弹丸极多、牺牲单发强度换近距停止力"（INCAPACITATOR） | 12 弹丸 × 6 耐力 = **72 耐力**；variance 25（注释：要么贴脸要么打空）；`AMMO_CLASS_NICHE_LTL`；harmful FALSE |
| ⑱ | `/obj/item/ammo_casing/shotgun/flechette` | shredder flechette shell（碎纸机镖弹）"专切护甲、嵌入极深" | `AMMO_CLASS_SUPER`；材料 铁 30 + 玻璃 20；弹丸 5 伤（见 3.3）；`/datum/embedding/bullet/flechette` embed_chance = **40**（tg 基准 25，+15） |
| ⑲ | `/obj/item/ammo_casing/shotgun/flechette/donk` | 唐克（donk）镖弹变体 | `AMMO_CLASS_NONE`（注释：其实是微塑料注射，算致命/致命特殊都行） |
| ⑳ | `/obj/item/ammo_casing/shotgun/ion` | 离子弹 | `can_be_printed = FALSE`（techshell） |
| ㉑ | `/obj/item/ammo_casing/shotgun/scatterlaser` | 散射激光弹 | `AMMO_CLASS_NICHE`（"它很科技"） |
| ㉒ | `/obj/item/ammo_casing/shotgun/techshell` | 科技弹壳（空壳） | `can_be_printed = FALSE`（注释：没有定义弹头本来也打不了） |
| ㉓ | `/obj/item/ammo_casing/shotgun/dart` | 霰弹镖（麻醉/注射） | `AMMO_CLASS_NICHE_LTL`（"严格来说"）；铁 100 |
| ㉔ | `/obj/item/ammo_casing/shotgun/dart/piercing` | piercing shotgun dart（穿甲镖）"钻石尖头，可注入最多 **5 单位**任意化学" | icon "pcshell"；`projectile_type = /obj/projectile/bullet/dart/piercing`；`reagent_amount = 5`；材料 `AMMO_MATS_SHOTGUN_PENDART`（铁 30+钻石 10） |
| ㉕ | `/obj/item/ammo_casing/shotgun/dart/bioterror` | 生物恐怖镖（预装恐怖化学） | `can_be_printed = FALSE`（"可能别了"） |
| ㉖ | `/obj/item/ammo_casing/shotgun/breacher` | **breacher（破门弹）**"对气闸/机甲/机器 1k 伤害，对人约 5 伤害" | `AMMO_CLASS_NICHE_LTL`；`can_be_printed = FALSE` |
| ㉗ | `/obj/item/ammo_casing/shotgun/magnum` | **magnum blockshot shell（马格南块弹，Nova 原创）**SolFed 反海盗执法者最爱，专治 vox | icon "magshell"；pellets = **4**（弹丸数减半、单发伤害翻倍，总伤与鹿弹相同）；variance 20；`AMMO_CLASS_SUPER` |
| ㉘ | `/obj/item/ammo_casing/shotgun/express` | **express pelletshot shell（快车小弹丸弹，Nova 原创）**"弹丸更多更小，teshari 面前提它都是禁忌" | icon "expshell"；pellets = **10**（3×10=30 伤，散布更小）；`variance = 12`；`AMMO_CLASS_SUPER` |
| ㉙ | `/obj/item/ammo_casing/shotgun/flechette_nova` | **ripper flechette shell（撕裂者镖弹，Nova 原创）**"专撕无甲目标" | icon "fshell"；pellets = 8（8×6=48 伤害潜力）；variance 15；`AMMO_CLASS_PLUS`；材料 `AMMO_MATS_SHOTGUN_FLECH`（铁 30+玻璃 20）；弹丸 7 伤 |
| ㉚ | `/obj/item/ammo_casing/shotgun/beehive` | **hornet shell（蜂巢弹，Nova 原创）**"低致命，4 枚可反弹并自动重瞄最近目标的弹丸——但会找**任何**目标" | icon "cnrshell"；pellets = 4；variance 15；`fire_sound = taser.ogg`；harmful FALSE；材料 `AMMO_MATS_SHOTGUN_HIVE`（铁 20+等离子 10+银 10）；`AMMO_CLASS_NICHE_LTL` |
| ㉛ | `/obj/item/ammo_casing/shotgun/antitide` | **stardust shell（星尘弹，Nova 原创）**"纳米电极展开成更大电极、拉回电缆连在弹壳上，像泰瑟枪——无限电力！" | icon "lasershell"；`projectile_type = /obj/projectile/energy/electrode`；harmful FALSE；`fire_sound = taser.ogg`；材料 `AMMO_MATS_SHOTGUN_TIDE`（铁 20+等离子 10+金 10）；`AMMO_CLASS_NICHE_LTL` |
| ㉜ | `/obj/item/ammo_casing/shotgun/frangible` | **frangible slug（易碎破门弹，Nova 原创）**"弱反器材弹，专卸气闸、拆路障与结构，对人无效" | icon "breacher"；材料 钛 400（4 张）+ 铁 200（2 张）+ 塑料 200（2 张）；弹头 15 伤（注释："你能用它杀人我服你"）；`demolition_mod = 2`，对窗户/气闸/格栅/门框/窗门 → **50** |
| ㉝ | `/obj/item/ammo_casing/shotgun/hunter` | **hunter slug shell（猎兽弹，Nova 原创）**"对本地 fauna 造成额外伤害" | icon "huntershell"；弹头 20 伤 / range 12 / 生物类型倍率 ×5（MOB_BEAST 或体型 ≥LARGE 且非 OVERSIZED），还能直接钻矿（矿物地块 gets_drilled 并穿透） |
| ㉞ | `/obj/item/ammo_casing/shotgun/honkshot` | **confetti shell（五彩纸屑弹，Nova 原创）**"装满纸屑的鹿弹。耶！" | icon "honkshell"；pellets = **19**（"最关键的加强"）；variance 35；`fire_sound = bikehorn.ogg`；harmful FALSE；`AMMO_CLASS_NONE` |

> 合计 34 行条（含基类 ①），其中**模块新增 Nova 原创**：magnum / express / flechette_nova / beehive / antitide / frangible / hunter / honkshot（8 种）。

### 3.3 弹头全录 (Projectile Catalog)

| # | 类型路径 | 名称 | 数值（源码精确） |
|---|---|---|---|
| ① | `/obj/projectile/bullet/shotgun_slug` | 独头弹 | damage **35**、wound_bonus **5**、exposed_wound_bonus **10**（继承 30 AP） |
| ② | `/obj/projectile/bullet/shotgun_slug/milspec` | 军规独头弹 | damage **50**、armour_penetration **30**、speed **1.5**、wound_bonus **10**、exposed_wound_bonus **5** |
| ③ | `/obj/projectile/bullet/pellet/shotgun_buckshot` | 鹿弹丸 | damage **6**、wound_bonus **5**、exposed_wound_bonus **10** |
| ④ | `/obj/projectile/bullet/pellet/shotgun_buckshot/milspec` | 军规鹿弹丸 | damage **7**（7×8=56）、`damage_falloff_tile = -0.1`、`wound_falloff_tile = -0.25`、speed **1.5**、AP **5** |
| ⑤ | `/obj/projectile/bullet/pellet/shotgun_rubbershot` | 橡皮弹丸 | stamina **10**、speed **1** |
| ⑥ | `/obj/projectile/bullet/pellet/flechette` | shredder flechette（碎纸机镖） | name "shredder flechette"；damage **5**（4×8=32 潜在伤害）、`damage_falloff_tile = -0.1`、speed **1.35**、wound_bonus **5**、exposed_wound_bonus **10**（上游 2 伤/8 弹丸/30 AP，模块 +2.5 伤每弹丸、falloff 缓化、裸露创伤 +5） |
| ⑦ | `/datum/embedding/bullet/flechette` | 镖弹嵌入设定 | `embed_chance = 40`（tg 基准 25，+15） |
| ⑧ | `/obj/projectile/bullet/pellet/shotgun_buckshot/magnum` | magnum blockshot（马格南块弹丸） | damage **13**、exposed_wound_bonus **5**、wound_bonus **5**、AP **5**、`weak_against_armour = FALSE`；`Initialize` 缩放 **1.25×** |
| ⑨ | `/obj/projectile/bullet/pellet/shotgun_buckshot/express` | express pellet（快车弹丸） | damage **3**、wound_bonus **0**、exposed_wound_bonus **20**；`Initialize` 缩放 **0.75×** |
| ⑩ | `/obj/projectile/bullet/pellet/shotgun_buckshot/flechette_nova` | ripper flechette（撕裂者镖） | icon `projectiles.dmi`/"flechette"；damage **7**、wound_bonus **0**、exposed_wound_bonus **15**、`sharpness = SHARP_EDGED`（"你知道吗，镖是横着飞的"）；`Initialize` 自旋动画 |
| ⑪ | `/obj/projectile/bullet/pellet/shotgun_buckshot/beehive` | hornet flechette（大黄蜂镖） | icon "hornet"；damage **5**、stamina **15**、`damage_falloff_tile = -0.1`、`stamina_falloff_tile = -0.1`、wound_bonus **-5**、exposed_wound_bonus **5**、sharpness NONE；**反弹参数**：`ricochets_max = 5`、`ricochet_chance = 200`、`ricochet_auto_aim_angle = 60`、`ricochet_auto_aim_range = 8`、`ricochet_decay_damage = 1`、`ricochet_decay_chance = 1`、`ricochet_incidence_leeway = 0`（"纳米机器，儿子"） |
| ⑫ | `/obj/projectile/bullet/frangible_slug` | frangible slug（易碎弹头） | damage **15**、wound_bonus **30**、exposed_wound_bonus **30**、`demolition_mod = 2`；`on_hit` 对 typecache（`/obj/structure/window`、`/obj/machinery/door/airlock`、`/obj/structure/grille`、`/obj/structure/door_assembly`、`/obj/machinery/door/window`）→ `demolition_mod = 50` |
| ⑬ | `/obj/projectile/bullet/shotgun_slug/hunter` | 12g hunter slug（猎兽弹头） | name "12g hunter slug"；damage **20**、range **12**；`biotype_damage_multiplier = 5`（命中正确生物类型 ×5）、`biotype_we_look_for = MOB_BEAST`；对大型非 OVERSIZED 目标同样 ×5；命中矿物地块直接钻矿并穿透 |
| ⑭ | `/obj/projectile/bullet/honkshot` | confetti（纸屑） | name "confetti"；damage **0**、sharpness NONE、shrapnel_type NONE、impact_effect null、`ricochet_chance = 0`、`jitter = 1 秒`、`eyeblur = 1 秒`、`hitsound = SFX_CLOWN_STEP`、range **4**（`Initialize` 随机 1–4）、icon_state "guardian"；`Initialize` 自旋 + 随机取**骄傲旗六色**（COLOR_PRIDE_RED/ORANGE/YELLOW/GREEN/BLUE/PURPLE）；`Moved` 每格留下 `/obj/effect/decal/cleanable/confetti` 纸屑污渍；`on_hit` 让活体 `SpinAnimation(7,1)`（后空翻）；`on_range` 触发 `do_sparks(1)` 火花 |

### 3.4 弹药盒全录 (Ammo Boxes, 15 种)

> 源码路径：`modular_nova/modules/shotgunrebalance/code/ammobox.dm`；基类 `/obj/item/ammo_box/advanced/s12gauge`（"shell box (slugs)" 弹壳盒，图标 `shotbox.dmi`，max_ammo 15）。全部弹药盒均可直接用于弹药工作台。

| # | 类型路径 | 名称 | 容量 | 弹种 |
|---|---|---|---|---|
| ① | `/obj/item/ammo_box/advanced/s12gauge` | shell box (slugs) 独头弹盒 | 15 | `/obj/item/ammo_casing/shotgun` |
| ② | `.../buckshot` | shell box (buckshot) 鹿弹盒 | 15 | `.../buckshot` |
| ③ | `.../rubber` | shell box (rubber shot) 橡皮弹盒 | 15 | `.../rubbershot` |
| ④ | `.../bean` | shell box (beanbag slugs) 豆袋弹盒 | 15 | `.../beanbag` |
| ⑤ | `.../magnum` | shell box (magnum blockshot) 马格南盒 | 15 | `.../magnum` |
| ⑥ | `.../express` | shell box (express pelletshot) 快车弹盒 | 15 | `.../express` |
| ⑦ | `.../hunter` | shell box (hunter slug) 猎兽弹盒 | 15 | `.../hunter` |
| ⑧ | `.../flechette` | shell box (ripper flechette) 撕裂者镖盒 | 15 | `.../flechette_nova` |
| ⑨ | `.../frangible` | Frangible Slug ammo box 易碎弹盒 | **10** | `.../frangible` |
| ⑩ | `.../beehive` | shell box (hornet's nest) 蜂巢弹盒 | 15 | `.../beehive` |
| ⑪ | `.../antitide` | shell box (stardust) 星尘弹盒 | 15 | `.../antitide` |
| ⑫ | `.../incendiary` | shell box (incendiary slug) 燃烧弹盒 | 15 | `.../incendiary` |
| ⑬ | `.../honkshot` | shell box (confetti) 纸屑弹盒 | **35** | `.../honkshot` |
| ⑭ | `.../milspec` | shell box (milspec slug) 军规独头弹盒 | 15 | `.../milspec`（Scarborough Arms 制造，`manufacturer_examine` 元素 COMPANY_SCARBOROUGH） |
| ⑮ | `.../buckshot/milspec` | shell box (milspec buckshot) 军规鹿弹盒 | 15 | `.../buckshot/milspec`（Scarborough Arms 制造） |

### 3.5 自动车床设计 (Autolathe Designs)

> 源码路径：`modular_nova/modules/shotgunrebalance/code/autolathe_design.dm`

**新设计（2 张，AUTOLATHE 建造，分类 `RND_CATEGORY_HACKED + RND_CATEGORY_WEAPONS + RND_SUBCATEGORY_WEAPONS_AMMO`）**：

| 设计 datum | id | 名称 | 材料 | 产物 |
|---|---|---|---|---|
| `/datum/design/shotgun_slug` | "shotgun_slug" | Shotgun Slug | 铁 200（2 张） | `/obj/item/ammo_casing/shotgun` |
| `/datum/design/buckshot_shell` | "buckshot_shell" | Buckshot Shell | 铁 200（2 张） | `/obj/item/ammo_casing/shotgun/buckshot` |

**既有设计打折（材料下调至铁 200，`inherit_materials = DESIGN_INHERIT_MATS_SPECIAL`）**，另有 `/sec`（安保版）子类再降至**铁 100（1 张）**：

| 设计 datum | 产物 | 普通 | /sec 安保版 |
|---|---|---|---|
| `/datum/design/rubbershot` | rubbershot 橡皮弹 | 铁 200 | 铁 100 |
| `/datum/design/beanbag_slug` | beanbag 豆袋弹 | 铁 200 | 铁 100 |
| `/datum/design/shotgun_dart` | 霰弹镖 | 铁 200 | 铁 100 |
| `/datum/design/incendiary_slug` | 燃烧弹 | 铁 200 | 铁 100 |

---

## 四、卷三 枪械保险 Gun Safety (gun_safety)

> 源码路径：`modular_nova/modules/gun_safety/code/safety_component.dm`（93 行）+ `safety_additions.dm`（48 行）+ `keybinding.dm`（19 行）；图标 `icons/actions.dmi`；模块 ID：gunsafety；作者：Gandalf2k15（OG code）
> 模块特色：`/datum/component/gun_safety` 保险组件 + `/datum/action/item_action/gun_safety_toggle` 切换动作 + Shift+F 键位

### 4.1 保险组件 (Gun Safety Component)

**`/datum/component/gun_safety`**：

| 属性 | 值 |
|---|---|
| `safety_currently_on` | TRUE（保险默认**开启**） |
| `toggle_safety_action` | `/datum/action/item_action/gun_safety_toggle`（切换动作持有者） |

**行为全录**：
- `Initialize()`：非枪械（`!isgun(parent)`）→ `COMPONENT_INCOMPATIBLE`；给父物体加 `gun_safety_toggle` 物品动作；**保留层（reserved level）且不在穿梭机区域内 → 保险默认关闭**（`safety_currently_on = FALSE`）。
- `RegisterWithParent()` 注册三个信号：
  - `COMSIG_GUN_TRY_FIRE` → `check_if_we_can_actually_shooty`：保险开启时 `balloon_alert` 提示并返回 **`COMPONENT_CANCEL_GUN_FIRE`**（阻止开火，同时取消 `COMSIG_MOB_TRYING_TO_FIRE_GUN`）。
  - `COMSIG_ITEM_UI_ACTION_CLICK` → `we_may_be_toggling_safeties`：点击的动作若是保险动作 → `toggle_safeties(user)`；返回 `COMPONENT_ACTION_HANDLED`。
  - `COMSIG_ATOM_EXAMINE` → `on_examine`：检查文本追加保险状态——**绿色 `ON`（#00ff15）/ 红色 `OFF`（#ff0000）**，并附上该玩家绑定的保险键位提示（`key_bindings["toggle_safety"]`）。
- `toggle_safeties(mob/user)`：翻转 `safety_currently_on`；播放 `sound/items/weapons/empty.ogg`（音量 100）；可见消息 "…toggles the safety **ON/OFF**"（绿/红着色）；`SEND_SIGNAL(parent, COMSIG_GUN_SAFETY_TOGGLED)` —— 跨模块监听者：`modular_weapons` 的 Scarborough Arms **Enforcer 手枪**监听此信号刷新外观贴层（`safety_toggled` → `update_appearance()`）。
- `update_action_button_state()`：动作按钮图标 `"safety_on"` / `"safety_off"`。
- `Destroy()`：删除切换动作。

**动作 `/datum/action/item_action/gun_safety_toggle`**：name "Toggle Gun Safety"（切换枪械保险）；按钮图标 `actions.dmi` / "safety_on"。

### 4.2 挂载范围全录 (Coverage Catalog)

> 源码路径：`safety_additions.dm` + 其他模块覆写。挂载点：`code/modules/projectiles/gun.dm` 的 `/obj/item/gun/Initialize` 调用 `give_gun_safeties()`（NOVA EDIT ADDITION）。

**基类规则**：
- `/obj/item/gun/proc/give_gun_safeties()`：空实现（不挂）。
- `/obj/item/gun/ballistic/give_gun_safeties()`：`AddComponent(/datum/component/gun_safety)` —— **所有弹道枪挂保险**。
- `/obj/item/gun/energy/give_gun_safeties()`：`AddComponent(...)` —— **所有能量枪挂保险**。

**无保险例外全录（override 返回空，11 处核心 + 5 处其他模块）**：

| # | 类型路径 | 说明 |
|---|---|---|
| 1 | `/obj/item/gun/ballistic/bow` | 弓（弓箭） |
| 2 | `/obj/item/gun/ballistic/rifle/enchanted` | 附魔步枪 |
| 3 | `/obj/item/gun/ballistic/automatic/laser/ctf` | CTF 激光枪 |
| 4 | `/obj/item/gun/ballistic/shotgun/ctf` | CTF 霰弹枪 |
| 5 | `/obj/item/gun/ballistic/automatic/laser/ctf/marksman` | CTF 神射手激光枪 |
| 6 | `/obj/item/gun/ballistic/automatic/pistol/deagle/ctf` | CTF 沙漠之鹰 |
| 7 | `/obj/item/gun/ballistic/revolver/grenadelauncher` | 左轮榴弹发射器 |
| 8 | `/obj/item/gun/energy/plasmacutter` | 等离子切割枪 |
| 9 | `/obj/item/gun/energy/recharge/kinetic_accelerator` | 动能加速器（KA） |
| 10 | `/obj/item/gun/energy/recharge/fisher` | 渔枪 |
| 11 | `/obj/item/gun/syringe/blowgun` | 吹箭筒（注射枪系） |
| 12 | `/obj/item/gun/energy/cell_loaded`（`modular_nova/modules/cellguns/`） | 电池供能枪（cellguns 模块，因使用自定义充能贴层） |
| 13 | `/obj/item/gun/ballistic/shotgun/shell_launcher`（`modular_nova/modules/implants/code/augments_arms.dm`） | 义体臂内置弹壳发射器 |
| 14 | `/obj/item/gun/ballistic/shotgun/automatic/ubsg`（`modular_nova/modules/marines/code/gear.dm`） | UBSG 自动霰弹枪（陆战队） |
| 15 | `/obj/item/gun/syringe/smartdart`（`modular_nova/modules/medical/code/smartdarts.dm`） | 智能镖注射枪 |
| 16 | `/obj/item/gun/energy/recharge/kinetic_accelerator/cyborg/unicorn`、`/obj/item/gun/energy/plasmacutter/unicorn`（`modular_z121/code/modules/cyborg/UnicornBorgModule.dm`） | 独角兽机娘borg 版 KA/等离子切割枪 |

### 4.3 键位与切换 (Keybinding)

> 源码路径：`modular_nova/modules/gun_safety/code/keybinding.dm`

**`/datum/keybinding/carbon/toggle_safety`**：

| 属性 | 值 |
|---|---|
| hotkey_keys | **ShiftF**（Shift + F） |
| name | "toggle_safety" |
| full_name | "Toggle gun's safety mode"（切换持枪手的保险模式） |
| description | "Toggles gun's safety mode in an active hand."（切换主动手中枪械的保险模式） |
| keybind_signal | `COMSIG_KB_CARBON_TOGGLE_SAFETY` |

`down(client/user)`：取主动手持物品（`get_active_held_item()`）→ 无枪/无保险组件则忽略 → `safety.toggle_safeties(owner)`。切换音效与可见消息见 4.1。

---

## 五、卷四 弹药 HUD Gun HUD (gunhud)

> 源码路径：`modular_nova/modules/gunhud/code/gun_hud.dm`（90 行）+ `gun_hud_component.dm`（406 行）；图标 `icons/gun_hud.dmi`；模块 ID：GUNHUD；作者：Gandalf2k15（OG creation）、Larentoun（modularisation）
> 模块特色：`/atom/movable/screen/ammo_counter` 屏幕元素 + `/datum/element/ammo_hud` 元素 + `get_accurate_ammo_count()` 精准弹药计数

### 5.1 弹药计数器屏幕元素 (Ammo Counter Screen Element)

**`/atom/movable/screen/ammo_counter`（Ammo Counter 弹药计数器）**：

| 属性 | 值 |
|---|---|
| name | "ammo counter"（弹药计数器） |
| icon / icon_state | `icons/gun_hud.dmi` / "backing"（背板） |
| screen_loc | `ui_ammocounter` = **"EAST-1:28,CENTER-5:9"**（屏幕右下） |
| invisibility | `INVISIBILITY_ABSTRACT`（默认隐藏，`turn_on` 时置 0） |
| `backing_color` | `COLOR_RED`（背板/数字/指示器着色，默认红） |
| `oth_backing` | "oth_light"（数字背光层图标名） |
| `oth_o` / `oth_t` / `oth_h` | OTH 数字位：个位 oX / 十位 tX / 百位 hX（X = 0–9） |
| `indicator` | 底部方框内的自定义指示器图标（如半自动/全自动切换、空仓、火苗） |
| `on` | HUD 是否显示中 |

**proc 全录**：
- `turn_off()`：隐藏（INVISIBILITY_ABSTRACT）、清 maptext、重置 backing_color/oth_backing/oth_o/t/h/indicator、`update_appearance()`、`on = FALSE`。
- `turn_on()`：`invisibility = 0`、`on = TRUE`（保留当前设定值）。
- `set_hud(_backing_color, _oth_o, _oth_t, _oth_h, _indicator, _oth_backing = "oth_light")`：主设置入口，赋值后 `update_appearance()`。
- `update_overlays()`：按 oth_backing → o → t → h → indicator 顺序叠加 `mutable_appearance`，**全部以 `backing_color` 着色**。

**HUD 挂载点**（NOVA EDIT ADDITION，模块外）：`code/_onclick/hud/human.dm`（人类）与 `code/_onclick/hud/generic_dextrous.dm`（灵巧类）各 `add_screen_object(/atom/movable/screen/ammo_counter, HUD_MOB_AMMO, HUD_GROUP_INFO)`。

### 5.2 ammo_hud 元素 (The ammo_hud Element)

**`/datum/element/ammo_hud`**：
- `Attach()`：仅接受**枪械（isgun）或焊枪（`/obj/item/weldingtool`）**，否则 `ELEMENT_INCOMPATIBLE`。
- 信号注册：`COMSIG_ITEM_EQUIPPED` → `on_equipped`；`COMSIG_ITEM_DROPPED` → `on_dropped`；弹道枪额外注册 `COMSIG_UPDATE_AMMO_HUD + COMSIG_GUN_CHAMBER_PROCESSED` → `update_ballistic`；能量枪同样两信号 → `update_energy`；焊枪只注册 `COMSIG_UPDATE_AMMO_HUD` → `update_welder`。
- `get_hud(holder, parent_item)`：定位显示者——`hud_owner.hud_used.screen_objects[HUD_MOB_AMMO]`（无人类 HUD 则返回 null）。
- `on_equipped`：仅当装备者正**手持**该物品；`hud.turn_on()`；`ADD_TRAIT(source, TRAIT_DISPLAYING_AMMO_HUD, REF(src))`；立即刷新；若另一只手也持有 HUD 物品 → 注册 `COMSIG_MOB_SWAP_HANDS`（换手跟随）。
- `on_dropped`：掉落时检查两手剩余 HUD 物品，有则刷新为剩余物品，无则 `hud.turn_off()`；`REMOVE_TRAIT`；不再双持时注销换手信号。
- `on_hands_swap`：换手后若新主动手物品带 `TRAIT_DISPLAYING_AMMO_HUD` 且 HUD 开启 → 刷新。
- `should_update(to_update)`：**抑制非主动手更新**——若主动手物品带 HUD trait 且请求者是另一只手的物品 → FALSE。
- `update_hud(source, hud)`：按类型路由到 ballistic / energy / welder 三处理器。

**显示格式全录**：

**弹道枪 `update_ballistic`**：背板青色（`COLOR_CYAN`）；
- 无弹匣 → 数字位显示 "oe/te/he"，指示器 **"no_mag"**（无弹匣）；
- 有弹匣但 `get_ammo() == 0` → **"empty_flash"**（空仓闪烁）；
- 否则 `get_accurate_ammo_count()` 转文本：1 位 → 个位 `oN`；2 位 → `tN oN`；3 位 → `hN tN oN`；≥4 位（异常）→ "o9/t9/h9"（999）。

**能量枪 `update_energy`**：icon_state "eammo_counter"（空电 "eammo_counter_empty"）；清 overlays；`maptext` 双行：**电池百分比（大字）＋ 单发耗电百分比（青色小字）**；
- 计算：`batt_percent = floor(clamp(cell.charge / cell.maxcharge, 0, 1) × 100)`；`shot_cost_percent = floor(clamp(选中弹种 e_cost / cell.maxcharge, 0, 1) × 100)`；
- 颜色阈值：无法射击（`!can_shoot()`）→ 电池数字**红色**；`batt_percent ≤ 25` → **黄色**；否则 **COLOR_VIBRANT_LIME 亮青柠绿**；耗电百分比恒为青色；
- `maptext_x`：双值任一 >99 → -12，否则 -8（对齐）。

**焊枪 `update_welder`**：背板**棕橙色**（`COLOR_TAN_ORANGE`）；燃料 `get_fuel() < 1` → "empty_flash"；否则数字位显示燃料量；指示器 **"flame_on"（点燃）/ "flame_off"（熄灭）**。

### 5.3 精准弹药计数 (Accurate Ammo Count)

**`/obj/item/gun/ballistic/proc/get_accurate_ammo_count()`**：
- `bolt_type == BOLT_TYPE_OPEN`（开放式枪机）→ `get_ammo(countchambered = FALSE)`（不计膛内弹）；
- 否则 → `get_ammo(countchambered = TRUE)`（计入膛内弹）。

**覆写全录（4 处）**：
- **脉冲步枪 `/obj/item/gun/ballistic/automatic/pulse_rifle`**：遍历弹匣与膛内全部 `/obj/item/ammo_casing/pulse`，累加 `remaining_uses > 0` 的剩余充能次数（**显示剩余次数而非弹数**）。
- **脉冲狙击枪 `/obj/item/gun/ballistic/rifle/pulse_sniper`**：每个 pulse 弹壳按 `floor(remaining_uses / shots_per_fire)` 折算**可射击次数**（余量不足一发不计数）。
- **左轮 `/obj/item/gun/ballistic/revolver`**：`get_ammo(countchambered = FALSE, countempties = FALSE)`（"他妈的就是左轮"——不计空膛与膛内弹）。
- **弓 `/obj/item/gun/ballistic/bow`**：`get_ammo(countchambered = FALSE)`（弓也不该算上膛）。

**挂载**：`/obj/item/gun/ballistic/Initialize`、`/obj/item/gun/energy/Initialize`、`/obj/item/weldingtool/Initialize` 均 `AddElement(/datum/element/ammo_hud)`。

### 5.4 更新触发点 (Update Triggers)

> 源码路径：`modular_nova/master_files/`（模块外配套覆写，NOVA 主文件）

| 位置 | 触发 |
|---|---|
| `code/modules/projectiles/guns/ballistic.dm` | `eject_magazine()`（退弹匣）与 `insert_magazine()`（插弹匣）成功后 `SEND_SIGNAL(src, COMSIG_UPDATE_AMMO_HUD)` |
| `code/modules/projectiles/guns/energy.dm` | `select_fire()`（切换射击模式）后发送；`attack_hand_secondary`（次要交互）在多弹种且 `can_select` 时切模式并发送（注释：不走 attack_self_secondary 是为了不与转枪冲突） |
| `code/modules/projectiles/guns/energy/recharge.dm` | 充能相关更新发送信号 |
| `code/game/objects/items/tools/engineering/weldingtool.dm` | `on_reagents_change`（试剂容器变化，含 `set_welding` 与试剂更新注册 `COMSIG_REAGENTS_HOLDER_UPDATED`）→ 发送信号刷新燃料显示 |
| `code/modules/projectiles/guns/ballistic/bows/_bow.dm` | 弓的装/卸箭发送信号 |

配套定义：`#define COMSIG_UPDATE_AMMO_HUD "update_ammo_hud"`（`code/__DEFINES/~nova_defines/signals.dm`）；`#define TRAIT_DISPLAYING_AMMO_HUD "displaying_ammo_hud"`（`~nova_defines/traits.dm`）；`HUD_MOB_AMMO "mob_ammo"`、`ui_ammocounter "EAST-1:28,CENTER-5:9"`（`~nova_defines/hud.dm`）；`#define AMMO_HUD "25"`（`code/__DEFINES/atom_hud.dm`，模块 readme 记载的旧 HUD 常量）。

---

## 六、卷五 枪指机制 Gunpoint (gunpoint)

> 源码路径：`modular_nova/modules/gunpoint/code/gunpoint.dm`（54 行）+ `gunpoint_datum.dm`（118 行）；图标 `icons/targeted.dmi`、`icons/radial_gunpoint.dmi`；音效 `sound/targeton.ogg`、`sound/targetoff.ogg`；模块 ID：GUNPOINT；作者：Azarak（original code & porting）
> 模块特色：`/datum/gunpoint` 枪指 datum + `/obj/effect/overlay/gunpoint_effect` 锁定特效 + `ShiftMiddleClickOn` 触发（Baystation 风格，视觉更清晰）

### 6.1 触发与条件 (Trigger & Requirements)

**`/mob/living/ShiftMiddleClickOn(atom/A)`（Shift + 中键）**：
- 主动手持枪（`/obj/item/gun`）→ `DoGunpoint(A, G)`；
- 否则 → 回退到普通指向 `pointed(A)`（核心指向行为）。

**`/mob/living/proc/DoGunpoint(atom/A, obj/item/gun/G)`**：
- 目标是**自己** → 直接返回；
- 目标**不是活体**：若正在枪指 → `qdel(gunpointing)`（取消），返回；
- 目标是**当前枪指目标** → `gunpointing.click_destroy()`（取消锁定）；
- 否则：`CanGunpointAt(L, TRUE)` 通过后，先销毁旧 gunpoint，再 `gunpointing = new(src, L, G)`。

**`/mob/living/proc/CanGunpointAt(mob/living/L, notice = FALSE)` 三条件**：
1. 不能是**躺着/休息中**（`resting`）——提示；
2. 目标必须在**自身 8 格视野内**（`viewers(8, src)`）；
3. 目标不能**隐身/低透明度**（`alpha < 70` 拒绝——防对隐身目标锁定）。

### 6.2 gunpoint datum 全流程 (Datum Lifecycle)

**`/datum/gunpoint` 字段**：`source`（持枪者）、`target`（被指者）、`aimed_gun`（所用枪）、`locked`（是否已锁定）、`was_running`（旧版记录奔跑意图，见下）。

**`New(user, tar, gun)` 初始化**：
- 互挂引用：`source.gunpointing = src`；`target.gunpointed += src`；
- `source.face_atom(target)` 面向目标；危险消息 "[source] aims [gun] at [target]!"；
- ~~旧版（NOVA EDIT REMOVAL）：记录 `was_running`、关闭奔跑意图、加 `TRAIT_NORUNNING`~~ —— **本分支已移除**（NOVA EDIT REMOVAL 注释块）；
- 若目标还没有特效：`target.gp_effect = new`，加入 `target.vis_contents`（目标可见特效）——overlay `/obj/effect/overlay/gunpoint_effect`（icon "locking"，FLY_LAYER/GAME_PLANE，`APPEARANCE_UI_IGNORE_ALPHA | KEEP_APART`，鼠标穿透）；
- 信号注册：
  - 来源：`COMSIG_MOVABLE_MOVED` → `source_moved`；`COMSIG_LIVING_STATUS_STUN/KNOCKDOWN/PARALYZE` → `source_cc`（被打断）；`COMSIG_LIVING_UPDATED_RESTING` → `source_updated_resting`；
  - 枪：`COMSIG_ITEM_EQUIPPED` / `COMSIG_ITEM_DROPPED` → `click_destroy`（收枪/换枪即解除）；
  - 双方：`COMSIG_QDELETING` → `Destroy`；
- `addtimer(CALLBACK(src, PROC_REF(lock_on)), 7)` —— **7 分秒（0.7 秒）后锁定**。

**`lock_on()`（锁定）**：`check_continuity()` 通过 → `locked = TRUE`；`log_combat(target, source, "locked onto with aiming")` 战斗日志；播放 `targeton.ogg`（音量 50）；来源提示 "[target] is now locked on."；目标警告 "[source] aims [gun] at you!"；**首个枪指者**（`target.gunpointed.len == 1`）额外危险提示；特效 icon_state → **"locked"**。

**`check_continuity()`**：目标仍存在 + 来源仍 `CanGunpointAt(target)`（无 notice）→ 重新面向目标返回 TRUE，否则 FALSE。

**`Destroy()`**：注销全部信号；移除 `TRAIT_NORUNNING`（旧代码残留，实际已在 New 中移除）；**最后一个枪指者**（`target.gunpointed.len == 1`）→ 从 `vis_contents` 移除并销毁特效；双方解除引用；若已锁定 → 双方可见消息 "…lowers the gun."；清空 source/target/aimed_gun。

**`click_destroy()`**：已锁定 → 播放 `targetoff.ogg`（音量 50）；`qdel(src)`。

**`source_cc()`**：来源受眩晕/击倒/麻痹（amount 且非 ignore）→ 立即解除枪指。

**`source_moved()`**：来源移动后 `check_continuity()` 失败 → 解除（跑出 8 格视野/超出条件即失效）。

**`source_updated_resting()`**：来源躺下 → 解除。

**移动转向**：`/mob/living/carbon/Move()` 覆写——枪指中每步按 `get_dir(source, target)` 强制面向目标（无法背对逃跑）。

### 6.3 核心代码集成点 (Core Integration)

> 模块依赖的核心代码改动（NOVA EDIT，`code/` 下），与 6.2 的 datum 配套：

| 位置 | 行为 |
|---|---|
| `code/modules/mob/living/living.dm`（MobBump，NOVA EDIT ADDITION GUNPOINT） | 推挤被枪指者：除非推挤者就是枪指者本人，否则**无法推挤**正被枪指的人；也无法推挤正在枪指别人的人（提示语："You can't push [them]!"） |
| `code/modules/mob/living/carbon/examine.dm` | 检查（examine）被枪指者：显示 "[source] is holding [him] at gunpoint with [gun]!"（警告色，逐人列出）；检查枪指者本人：显示 "He is aiming at [target.name] with [gun.name]." |
| `code/modules/mob/living/carbon/human/examine.dm` | 人类检查同样接入枪指状态（readme 记载） |

**关联系统（模块外，供参考）**：
- **记忆/心情**：`code/datums/memory/general_memories.dm` 有 `/datum/memory/held_at_gunpoint`（"被枪指"记忆，主角=被指者、配角=持枪者）；`code/datums/mood_events/generic_negative_events.dm` 有 `/datum/mood_event/gunpoint`（负面心情）。
- **处决/抵枪射击**：`code/modules/projectiles/gun.dm`（`interacting_with` 处决逻辑）与 `code/modules/projectiles/guns/ballistic/pistol.dm`（左轮处决附加 `/datum/status_effect/grouped/heldup`）关联"抵人开枪"。
- **⚠️ 同名新系统**：核心代码另有 **`/datum/component/gunpoint`**（`code/datums/components/gunpoint.dm`，组件版，阶段 1→2→3：`GUNPOINT_DELAY_STAGE_2 = 2.5s`、`GUNPOINT_DELAY_STAGE_3 = 7.5s`、创伤加成 5×阶段倍率 1.25/2/2.5、`GUNPOINT_MULT_*`），由 `gun.dm` 的抵枪交互触发——**不是本模块**（本模块 = 旧版 datum 枪指）。两者并存，玩家触发入口不同（Shift+中键 vs 抵枪交互）。

---

## 七、附录 A 代码路径索引 (Appendix A)

> 全部 12 个 .dm，合计 **2,061 行**（ammo_workbench 634 + shotgunrebalance 599 + gun_safety 160 + gunhud 496 + gunpoint 172）。

| 文件 | 行数 | 内容 |
|---|---|---|
| `modular_nova/modules/ammo_workbench/code/ammo_workbench.dm` | 537 | 工作台机器本体、制造流程、TGUI、零件升级、插入/弹出、电路板、设计图、平坦包 |
| `modular_nova/modules/ammo_workbench/code/design_disks.dm` | 97 | 设计盘基类 + 7 种模块 + 2 张原型机设计图 |
| `modular_nova/modules/shotgunrebalance/code/shotgun.dm` | 416 | 霰弹弹壳/弹头全系重平衡 + Nova 新弹药 |
| `modular_nova/modules/shotgunrebalance/code/ammobox.dm` | 113 | 15 种 12 号弹药盒 |
| `modular_nova/modules/shotgunrebalance/code/autolathe_design.dm` | 70 | 自动车床设计 2 张 + 4 组打折设计（含 /sec 版） |
| `modular_nova/modules/gun_safety/code/safety_component.dm` | 93 | 保险组件、切换动作、检查/开火拦截 |
| `modular_nova/modules/gun_safety/code/safety_additions.dm` | 48 | give_gun_safeties 挂载/例外清单 |
| `modular_nova/modules/gun_safety/code/keybinding.dm` | 19 | Shift+F 键位 |
| `modular_nova/modules/gunhud/code/gun_hud.dm` | 90 | ammo_counter 屏幕元素 |
| `modular_nova/modules/gunhud/code/gun_hud_component.dm` | 406 | ammo_hud 元素、三处理器、精准计数 |
| `modular_nova/modules/gunpoint/code/gunpoint.dm` | 54 | Shift+中键触发、DoGunpoint、CanGunpointAt、移动转向 |
| `modular_nova/modules/gunpoint/code/gunpoint_datum.dm` | 118 | gunpoint datum、锁定、特效、解除 |

资源：`ammo_workbench/icons/ammo_workbench.dmi`；`shotgunrebalance/icons/{shotshells,shotbox,projectiles}.dmi`；`gun_safety/icons/actions.dmi`；`gunhud/icons/gun_hud.dmi`；`gunpoint/icons/{targeted,radial_gunpoint}.dmi`、`gunpoint/sound/{targeton,targetoff}.ogg`；TGUI：`tgui/packages/tgui/interfaces/AmmoWorkbench.jsx`。

---

## 八、附录 B 模块外关联源码速查 (Appendix B)

| 定义/集成 | 路径 |
|---|---|
| 弹种位标志与 AMMO_CLASS_* | `code/__DEFINES/~nova_defines/ammo_bench_defines.dm` |
| 弹药材料宏 AMMO_MATS_* | `code/__DEFINES/~nova_defines/ammo_materials.dm` |
| 材料单位（SHEET=100 / HALF=50 / SMALL=10） | `code/__DEFINES/construction/material.dm` |
| 电源常量 STANDARD_CELL_RATE = 10 KW | `code/__DEFINES/power.dm` |
| 口径 CALIBER_SHOTGUN "shotgun" | `code/__DEFINES/projectiles.dm` |
| COMSIG_GUN_TRY_FIRE / COMPONENT_CANCEL_GUN_FIRE | `code/__DEFINES/dcs/signals/signals_object.dm` |
| COMSIG_GUN_CHAMBER_PROCESSED | 同上 |
| COMSIG_UPDATE_AMMO_HUD / COMSIG_GUN_SAFETY_TOGGLED / COMSIG_MOVABLE_RADIO_TALK_INTO | `code/__DEFINES/~nova_defines/signals.dm` |
| TRAIT_DISPLAYING_AMMO_HUD | `code/__DEFINES/~nova_defines/traits.dm` |
| HUD_MOB_AMMO / ui_ammocounter | `code/__DEFINES/~nova_defines/hud.dm` |
| ammo_counter 屏幕对象挂载（人类/灵巧 HUD） | `code/_onclick/hud/human.dm`、`code/_onclick/hud/generic_dextrous.dm` |
| give_gun_safeties() 调用点 | `code/modules/projectiles/gun.dm`（/obj/item/gun/Initialize） |
| HUD 更新触发（master_files 覆写） | `modular_nova/master_files/code/modules/projectiles/guns/{ballistic,energy,energy/recharge}.dm`、`.../ballistic/bows/_bow.dm`、`.../tools/engineering/weldingtool.dm` |
| 科技网节点（basic_arms / riot_supression） | `modular_nova/master_files/code/modules/research/techweb/all_nodes.dm` |
| 货运包（安保军械库 / 公司 Vitezstvi） | `modular_nova/modules/cargo/code/packs.dm`、`modular_nova/master_files/code/modules/cargo/packs/companies/machines.dm` |
| 地图自动生成 | `modular_nova/modules/automapper/code/area_spawn_entries.dm` |
| 12 号弹壳/弹头上游基准（core） | `code/modules/projectiles/ammunition/ballistic/shotgun.dm`、`code/modules/projectiles/projectile/bullets/shotgun.dm` |
| 保险联动：Scarborough Enforcer 手枪 | `modular_nova/modules/modular_weapons/code/company_and_or_faction_based/scarborough_arms/pistol.dm` |
| 无保险例外覆写（其他模块） | `modular_nova/modules/cellguns/code/cellguns.dm`、`implants/code/augments_arms.dm`、`marines/code/gear.dm`、`medical/code/smartdarts.dm`、`modular_z121/code/modules/cyborg/UnicornBorgModule.dm` |
| 枪指核心集成（推挤/检查） | `code/modules/mob/living/living.dm`、`code/modules/mob/living/carbon/examine.dm`、`code/modules/mob/living/carbon/human/examine.dm` |
| 同名组件版枪指（非本模块） | `code/datums/components/gunpoint.dm`；处决联动 `code/modules/projectiles/gun.dm`、`code/modules/projectiles/guns/ballistic/pistol.dm` |

---

*本文档由 TianGuan13 源码（NovaSector 分支）五个模块 12 个 .dm、共 2,061 行全量提取生成。所有伤害、容量、时间、材料、价格、概率数值均与源码逐字一致。*
