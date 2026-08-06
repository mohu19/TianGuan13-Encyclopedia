# TianGuan13 植入体与神经软件百科（Implants & Neuroware Encyclopedia）

> 基于 TianGuan13（NovaSector 分支）源码全量提取。覆盖：NIF 纳米植入框架系统（Nanite Implant Framework）、NIFSoft 神经软件全录、NIFSoft 商店程序、TGUI 界面、植入体设备、科研设计图、跨回合持久化，以及相关硬件（Soulcatcher RSD 捕魂器设备）。
> 数据来源：`modular_nova/modules/modular_implants/`（35 个 .dm，5,191 行）。
> 文档采用中英双语格式：中文为主，英文原名/对象路径随行标注。所有数值均直接提取自源码，未做推算。

## 目录 (Table of Contents)

1. [总览：NIF 系统架构](#1-总览nif-系统架构)
2. [NIF 本体（Nanite Implant Framework）](#2-nif-本体nanite-implant-framework)
   - [2.1 母版 NIF 全参数](#21-母版-nif-全参数)
   - [2.2 NIF 型号全录](#22-nif-型号全录)
   - [2.3 校准过程（Calibration）](#23-校准过程calibration)
   - [2.4 损坏与耐久机制](#24-损坏与耐久机制)
   - [2.5 能量来源：营养抽取与血液抽取](#25-能量来源营养抽取与血液抽取)
   - [2.6 防盗保护与 NIF 检查文本](#26-防盗保护与-nif-检查文本)
   - [2.7 自动手术器与新手包](#27-自动手术器与新手包)
3. [NIFSoft 神经软件系统](#3-nifsoft-神经软件系统)
   - [3.1 基类与通用机制](#31-基类与通用机制)
   - [3.2 数据盘全录（NIFSoft Datadisk）](#32-数据盘全录nifsoft-datadisk)
   - [3.3 NIFSoft 全录（A 类：基础与功能）](#33-nifsoft-全录a-类基础与功能)
   - [3.4 NIFSoft 全录（B 类：召唤魔典 Grimoires）](#34-nifsoft-全录b-类召唤魔典-grimoires)
   - [3.5 NIFSoft 全录（C 类：职业与军用）](#35-nifsoft-全录c-类职业与军用)
   - [3.6 NIFSoft 全录（D 类：HUD 天眼镜）](#36-nifsoft-全录d-类hud-天眼镜)
   - [3.7 NIFSoft 全录（E 类：成人类）](#37-nifsoft-全录e-类成人类)
   - [3.8 NIFSoft 商店（NIFSoft Catalog）](#38-nifsoft-商店nifsoft-catalog)
4. [TGUI 界面（NifPanel）](#4-tgui-界面nifpanel)
5. [植入体设备全录（misc_devices.dm）](#5-植入体设备全录misc_devicesdm)
6. [科研设计图全录（nif_research.dm）](#6-科研设计图全录nif_researchdm)
7. [跨回合持久化（nif_persistence.dm）](#7-跨回合持久化nif_persistencedm)
8. [NIF 动作与菜单入口](#8-nif-动作与菜单入口)
9. [相关硬件：Soulcatcher RSD 捕魂器设备](#9-相关硬件soulcatcher-rsd-捕魂器设备)
10. [关键数值速查表](#10-关键数值速查表)
11. [源码路径索引](#11-源码路径索引)

---

## 1. 总览：NIF 系统架构

**NIF（Nanite Implant Framework，纳米植入框架）** 是一种大脑植入物（`/obj/item/organ/cyberimp/brain/nif`），向用户体内注入纳米机械，并暴露一个神经软件（NIFSoft）芯片槽位。安装于 `ORGAN_SLOT_BRAIN_NIF`（脑部 NIF 器官槽位）。

核心设计：

1. **本体（NIF）**：`code/nifs.dm` — 母版 NIF 定义全部属性（功率、耐久、校准、防盗），`code/nif_implants.dm` — 三种商业型号（标准型/经济甲板型/试用轻量型），`code/nifs_tgui.dm` — NifPanel TGUI 界面。
2. **软件（NIFSoft）**：`code/nifsofts.dm` — 基类与数据盘，`code/nifsofts/` 下 18 个文件 — 全部程序实现，`code/nifsoft_catalog.dm` — 商店程序（PDA 端）。
3. **设备（Devices）**：`code/misc_devices.dm` — NIF 切割器/再生器/HUD 适配器，`code/nif_research.dm` — 原型机制作设计图，`code/nif_persistence.dm` — 跨回合保存。
4. **相关硬件（Soulcatcher）**：`code/soulcatcher/` — RSD 捕魂器系列设备（与 Soulcatcher NIFSoft 联动）。

工作流程速览：

- **安装**：通过自动手术器（Autosurgeon）或手术植入 → 触发校准（Calibration，标准型 3 分钟）→ 校准期间短暂失明并可能产生副作用 → 校准完成后完全可用。
- **使用**：NIF 动作按钮「打开 NIF 菜单」打开 TGUI → 管理 NIFSoft（激活/卸载/保留）、能量源（营养/血液抽取）、主题与检查文本。
- **卸载**：手术取出 NIF（NIFSoft 随之销毁）；安全人员可用「NIF 切割器」直接移除他人 NIFSoft；跨回合未佩戴 NIF 会损失 25 耐久。

---

## 2. NIF 本体（Nanite Implant Framework）

源码：`code/nifs.dm`（535 行）+ `code/nif_implants.dm`（37 行）

### 2.1 母版 NIF 全参数

`/obj/item/organ/cyberimp/brain/nif` — **Nanite Implant Framework（纳米植入框架）** — 所有 NIF 的原型：

| 参数 | 数值 | 说明 |
|---|---|---|
| `name` | Nanite Implant Framework | 名称 |
| `slot` | `ORGAN_SLOT_BRAIN_NIF` | 器官槽位：脑部 NIF 槽 |
| `w_class` | `WEIGHT_CLASS_NORMAL` | 重量等级：普通 |
| `actions_types` | `/datum/action/item_action/nif/open_menu` | 绑定动作：打开 NIF 菜单 |
| `cannot_confiscate` | TRUE | 不可被没收 |
| `max_power_level` | 1000 | 最大功率上限 |
| `power_level` | 初始 0，`Initialize()` 后充满至 1000 | 当前电量 |
| `max_durability` / `durability` | 100 / 100 | 最大/当前耐久 |
| `death_durability_loss` | 10 | 死亡时损失耐久 |
| `nif_persistence` | TRUE | 是否跨回合保留（默认是） |
| `theft_protection` | TRUE | 防盗保护（仅管理员可关闭） |
| `durability_loss_vulnerable` | TRUE | 是否可受耐久损伤 |
| `rewards_points` | 0 | 奖励点数（上限 `MAX_NIF_REWARDS_POINTS` = 2000） |
| `max_nifsofts` | 5 | 可同时安装的 NIFSoft 数量上限 |
| `preinstalled_nifsofts` | `/datum/nifsoft/soul_poem` | 预装程序：灵魂诗篇 |
| `calibration_time` | 3 MINUTES（180 秒） | 校准总时长 |
| `side_effect_risk` | 50 | 校准副作用概率：每周期 1/50 |
| `nutrition_drain_rate` | 1.5 | 营养抽取速率（营养/秒） |
| `nutrition_conversion_rate` | 5 | 营养→功率转换率 |
| `minimum_nutrition` | 25 | 营养抽取最低门槛 |
| `blood_conversion_rate` | 5 | 血液→能量转换率（满血约 500 电量） |
| `blood_drain_rate` | 1 | 血液抽取速率（血量/秒） |
| `minimum_blood_level` | `BLOOD_VOLUME_SAFE` | 血液抽取最低血量（安全线） |
| `good_sound` / `bad_sound` / `click_sound` | default_good.ogg / default_bad.ogg / default_click.ogg | 事件音效 |
| `chat_icon` | "standard" | 聊天图标（chat.dmi） |
| `manufacturer_notes` | "There is no data currently avalible for this product." | 制造商注释（设定文本，默认） |

**NIFSoft 安装规则**（`install_nifsoft()`）：

- NIF 损坏（`broken`）或正在校准时无法安装。
- 已装程序数达到 `max_nifsofts` 时无法安装。
- NIFSoft 必须兼容该 NIF 型号（`compatible_nifs` 列表检查）。
- `single_install` 程序不可重复安装；`mutually_exclusive_programs` 互斥程序会阻止安装。
- 安装成功获得奖励点：`rewards_points_rate × purchase_price`（默认 0.5 倍购买价），上限 2000。

**NIF 处理循环**（每 tick）：若功率不足（`power_usage > power_level`）会自动停用所有活动 NIFSoft；每 tick 结算 `power_usage` 功率消耗。

### 2.2 NIF 型号全录

| 型号路径 | 名称 | 说明 | 差异参数 |
|---|---|---|---|
| `/obj/item/organ/cyberimp/brain/nif` | Nanite Implant Framework | 母版（见 2.1） | — |
| `/obj/item/organ/cyberimp/brain/nif/standard` | **Standard Type NIF（标准型）** | 高质量分类：高可靠性、与用户无缝结合、足够存储与算力运行各类程序。全星系存量不足一千台，极难"自制" | 无差异（母版参数） |
| `/obj/item/organ/cyberimp/brain/nif/roleplay_model` | **Econo-Deck Type NIF（经济甲板型）** | 低质量"经济"山寨版：低档电池、过时危险结构、粗糙校准。常见于绝望者、罪犯与"自制"作坊 | `max_power_level` = 500；`max_nifsofts` = 3；`calibration_time` = 1 分钟；`max_durability` = 50；`death_durability_loss` = 10 |
| `/obj/item/organ/cyberimp/brain/nif/roleplay_model/cheap` | **Trial-Lite Type NIF（试用轻量型）** | 临时性框架：宣传活动/单一用途/经销商试用发放。不与用户真正"结合"，数小时内溶解为死纳米机械并被呼出。寿命无法延长 | `nif_persistence` = FALSE（不跨回合） |
| `/obj/item/organ/cyberimp/brain/nif/standard/ghost_role` | Standard Type NIF（幽灵角色版） | 幽灵角色用标准型 | `nif_persistence` = FALSE；`is_calibrated` = TRUE（免校准） |
| `/obj/item/organ/cyberimp/brain/nif/debug` | 调试型 NIF | 管理员/调试用 | `is_calibrated` = TRUE（免校准） |

### 2.3 校准过程（Calibration）

首次植入 NIF 后自动开始校准（`perform_calibration()`），按完成百分比分阶段：

| 阶段 | 进度区间 | 效果 |
|---|---|---|
| 阶段 1 | 0 → 0.1 | 用户失明（盲因 `nif_setup`） |
| 阶段 2 | 0.2 → 0.9 | 恢复视力；每周期 1/`side_effect_risk`（1/50）概率出现随机副作用：① +25 恶心值；② -50 耐力 |
| 完成 | 1.0+ | 校准完成，`is_calibrated = TRUE`，保存持久化数据 |

- 校准期间无法安装/卸载 NIFSoft，无法打开菜单（提示 "The NIF is still calibrating, please wait!"）。
- 校准期间用户死亡则校准中止（下一轮重新开始）。
- 标准型 3 分钟、经济甲板型 1 分钟。

### 2.4 损坏与耐久机制

- **耐久（durability）**：上限 `max_durability`（标准 100 / 经济甲板 50）。归零后 NIF 仍存在但耐久 < 1 时无法打开菜单（"Durability low!"）。
- **EMP 攻击**（`emp_act`）：收到 EMP 时——
  - 抵消父类造成的眩晕加成（仅短暂警告，不眩晕）。
  - NIF 进入 `broken` 状态 30 秒（期间完全无法使用菜单与安装程序），3 分钟后恢复耐久易损性。
  - 耐久损失：severity 1 → **-10**；severity 2 → **-5**（`death_durability_loss / 2`）。
  - 所有已装 NIFSoft 触发 `on_emp`：停用并打乱程序名 60 秒。
  - 提示：**"ELECTROMAGNETIC INTERFERENCE DETECTED."（检测到电磁干扰）**。
- **死亡惩罚**（`damage_on_death`）：佩戴者死亡 → 耐久 -10，之后 20 分钟内不再受死亡损伤（宽限期）。
- **修复**：`Cerulean NIF Regenerator`（蔚蓝再生器，每次 +20 耐久，5 次）或手术修理。

### 2.5 能量来源：营养抽取与血液抽取

- **营养抽取（Nutrition Drain）**：开启后按 `nutrition_drain_rate`（1.5/秒）消耗宿主营养，转换为功率（速率 × 转换率 = 1.5 × 5 = 7.5 功率/秒计入 `power_usage`）。宿主营养低于 `minimum_nutrition`（25）自动关闭；无饥饿特质（`TRAIT_NOHUNGER`，如噬血者）无法开启。
- **血液抽取（Blood Drain）**：开启后按 `blood_drain_rate`（1/秒）消耗血量，转换率 5（满血 → 约 500 电量，速率 × 转换率 = 5 功率/秒）。血量低于 `minimum_blood_level`（`BLOOD_VOLUME_SAFE`）自动关闭。
- 两种抽取均可从 TGUI 界面开关。

### 2.6 防盗保护与 NIF 检查文本

- **防盗保护**：NIF 记录首个佩戴者的 `ckey`（`stored_ckey`）。他人佩戴时触发警报声（buzz-sigh）、警告消息，NIF 自动脱出掉落（除非 `theft_protection` 被管理员关闭）。
- **NIF 检查文本**：佩戴 NIF 的人获得 `/datum/component/nif_examine` 组件，他人检查时额外显示紫色文本（默认 *"There's a certain spark to their eyes.（他们眼中有一丝光芒。）"*）。文本可在 TGUI 中自定义（≤6 字符则恢复默认）。

### 2.7 自动手术器与新手包

| 物品 | 名称 | 说明 |
|---|---|---|
| `/obj/item/autosurgeon/organ/nif` | 标准型 NIF 自动手术器 | 植入标准型 NIF，1 次使用 |
| `/obj/item/autosurgeon/organ/nif/debug` | 调试型自动手术器 | 植入免校准调试 NIF，1 次使用 |
| `/obj/item/autosurgeon/organ/nif/disposable` | **Econo-Deck Type Autosurgeon（经济甲板型自动手术器）** | 植入试用轻量型（Trial-Lite）NIF，仅持续一个班次，1 次使用 |
| `/obj/item/autosurgeon/organ/nif/ghost_role` | **Enhanced Standard Type NIF Autosurgeon（强化标准型自动手术器）** | 植入免校准标准型（幽灵角色版），1 次使用 |
| `/obj/item/storage/box/nif_ghost_box` | **NIF Starter Kit（NIF 新手包）** | 内含：幽灵角色版自动手术器 + 6 张数据盘（变形 Polymorph / 召唤 Summoner / 利比丁魔典 Dorms / 利比丁之眼 Dorms-Hypnosis / 捕魂器 Soulcatcher / 自动估价 Money Sense） |
| `/obj/item/storage/box/nif_ghost_box/ghost_role` | 幽灵角色版新手包 | 额外含：蜂巢思维 Hivemind 数据盘 |

---

## 3. NIFSoft 神经软件系统

源码：`code/nifsofts.dm`（230 行）+ `code/nifsofts/`（18 个文件，1,890 行）+ `code/nifsoft_catalog.dm`（144 行）

### 3.1 基类与通用机制

`/datum/nifsoft` — **Generic NIFsoft（通用神经软件）** 基类：

| 参数 | 默认值 | 说明 |
|---|---|---|
| `purchase_price` | 300 | 商店购买价（信用点） |
| `buying_category` | `NIFSOFT_CATEGORY_GENERAL` | 商店分类 |
| `ui_icon` | "floppy-disk" | TGUI 图标（Font Awesome） |
| `ui_theme` | "default" | UI 主题（继承 NIF 当前主题） |
| `single_install` | TRUE | 是否只能装一个实例 |
| `mutually_exclusive_programs` | 空 | 互斥程序列表 |
| `active_mode` | FALSE | 是否有持续活动模式 |
| `active` / `active_cost` | FALSE / 0 | 当前是否活动 / 活动功率消耗 |
| `activation_cost` | 0 | 激活一次性功率消耗 |
| `cooldown` / `cooldown_duration` | FALSE / 5 秒 | 是否有冷却 / 冷却时长（`DEFAULT_NIFSOFT_COOLDOWN`） |
| `compatible_nifs` | 母版 NIF（+ debug 型自动附加） | 兼容的 NIF 型号 |
| `rewards_points_rate` | 0.5 | 购买价中转化为奖励点的比例 |
| `rewards_points_eligible` | TRUE | 是否可用奖励点购买 |
| `persistence` | FALSE | 是否有跨回合数据 |
| `able_to_keep` | FALSE | 是否允许标记"保留至下一回合" |
| `keep_installed` | FALSE | 是否已标记保留 |
| `lewd_nifsoft` | FALSE | 是否为成人内容（服务器关闭成人内容时数据盘自动销毁） |

**数据盘上传**：`/obj/item/disk/nifsoft_uploader`（**Generic NIFSoft datadisk（通用 NIFSoft 数据盘）**）—— 内含永久旋转的纳米"磁"带。对自己使用（`attack_self`）或对他人使用（`attack`，需 5 秒 `do_after`）即可上传程序。不可重复使用（默认 `reusable = FALSE`，用后销毁）；上传会跳过奖励点发放（防刷信用点）。

### 3.2 数据盘全录（NIFSoft Datadisk）

**基类与变体：**

| 路径 | 名称 | 说明 |
|---|---|---|
| `/obj/item/disk/nifsoft_uploader` | Generic NIFSoft datadisk | 基类（loaded_nifsoft = /datum/nifsoft） |
| `/obj/item/disk/nifsoft_uploader/mil_grade` | 军用级数据盘 | 高性能抗冲击量子驱动器（icon: mil_disk） |
| `/obj/item/disk/nifsoft_uploader/job` | 企业级数据盘 | 企业级安全数字驱动器（icon: job_disk） |

**内容盘全录（38 种）：**

| 路径 | 名称（盘面） | 装载程序 |
|---|---|---|
| `/obj/item/disk/nifsoft_uploader/summoner` | Grimoire Caeruleam（蔚蓝魔典），售价 `PAYCHECK_CREW × 3` | /datum/nifsoft/summoner |
| `/obj/item/disk/nifsoft_uploader/summoner/ghost` | Grimoire Caeruleam（幽灵版），售价 0 | /datum/nifsoft/summoner |
| `/obj/item/disk/nifsoft_uploader/summoner/tools` | Grimoire Opera（歌剧院） | /datum/nifsoft/summoner/job/tools |
| `/obj/item/disk/nifsoft_uploader/summoner/book` | Grimoire Akasha（阿卡夏） | /datum/nifsoft/summoner/book |
| `/obj/item/disk/nifsoft_uploader/job/summoner/detective` | Grimoire Vacholiere（瓦乔利尔） | /datum/nifsoft/summoner/job/detective |
| `/obj/item/disk/nifsoft_uploader/job/summoner/surgery` | Grimoire Asclepius（阿斯克勒庇俄斯） | /datum/nifsoft/summoner/job/surgery |
| `/obj/item/disk/nifsoft_uploader/job/summoner/service` | Grimoire Hestia（赫斯提亚） | /datum/nifsoft/summoner/job/service |
| `/obj/item/disk/nifsoft_uploader/shapeshifter` | Polymorph（变形） | /datum/nifsoft/action_granter/shapeshifter |
| `/obj/item/disk/nifsoft_uploader/dorms` | Grimoire Libidine（利比丁魔典） | /datum/nifsoft/summoner/dorms |
| `/obj/item/disk/nifsoft_uploader/dorms/hypnosis` | Purpura Eye（紫瞳） | /datum/nifsoft/action_granter/hypnosis |
| `/obj/item/disk/nifsoft_uploader/dorms/contract` | Libidine Contract（利比丁契约，可重复使用） | /datum/nifsoft/hypno |
| `/obj/item/disk/nifsoft_uploader/soul_poem` | Soul Poem（灵魂诗篇） | /datum/nifsoft/soul_poem |
| `/obj/item/disk/nifsoft_uploader/soulcatcher` | Soulcatcher（捕魂器） | /datum/nifsoft/soulcatcher |
| `/obj/item/disk/nifsoft_uploader/hivemind` | Hivemind（蜂巢思维） | /datum/nifsoft/hivemind |
| `/obj/item/disk/nifsoft_uploader/scryer` | NIFSoft Scryer Uploader Disk | /datum/nifsoft/scryer |
| `/obj/item/disk/nifsoft_uploader/mil_grade/thermal` | Thermal Lens（热成像镜片） | /datum/nifsoft/hud/thermal |
| `/obj/item/disk/nifsoft_uploader/mil_grade/blood_steal` | Blood Steal（血液窃取） | /datum/nifsoft/blood_steal |
| `/obj/item/disk/nifsoft_uploader/job/money_sense` | Automatic Appraisal（自动估价） | /datum/nifsoft/money_sense |
| `/obj/item/disk/nifsoft_uploader/job/med_hud` | Medical Scrying Lens（医疗天眼镜） | /datum/nifsoft/hud/job/medical |
| `/obj/item/disk/nifsoft_uploader/job/diag_hud` | Diagnostic Scrying Lens（诊断天眼镜） | /datum/nifsoft/hud/job/diagnostic |
| `/obj/item/disk/nifsoft_uploader/job/sec_hud` | Security Scrying Lens（安保天眼镜） | /datum/nifsoft/hud/job/security |
| `/obj/item/disk/nifsoft_uploader/job/permit_hud` | Permit Scrying Lens（许可证天眼镜） | /datum/nifsoft/hud/job/cargo_tech |
| `/obj/item/disk/nifsoft_uploader/job/sci_hud` | Science Scrying Lens（科学天眼镜） | /datum/nifsoft/hud/job/science |
| `/obj/item/disk/nifsoft_uploader/job/meson_hud` | Meson Scrying Lens（介子天眼镜） | /datum/nifsoft/hud/job/meson |

**纳米工具子物品**（各召唤魔典生成的纳米物品，全部 `toolspeed = 1.5` 或 `force = 0`）：

- 通用工具（Grimoire Opera）：`/obj/item/screwdriver/omni_drill/nanite`、`/obj/item/weldingtool/mini/nanite`、`/obj/item/multitool/nanite`、`/obj/item/wirebrush/nanite`、`/obj/item/door_seal/nanite`（unseal_time 1 秒）——全部 toolspeed 1.5。
- 手术工具（Grimoire Asclepius）：`/obj/item/scalpel/nanite`（force 0/throwforce 0）、`/obj/item/retractor/nanite`、`/obj/item/hemostat/nanite`、`/obj/item/circular_saw/nanite`（force 0）、`/obj/item/surgicaldrill/nanite`（force 0）、`/obj/item/cautery/nanite`、`/obj/item/surgical_drapes/nanite`、`/obj/item/healthanalyzer/nanite`、`/obj/item/autopsy_scanner/nanite`——全部 toolspeed 1.5。
- 服务工具（Grimoire Hestia）：`/obj/item/storage/bag/tray/nanite`（餐盘）、`/obj/item/reagent_containers/cup/glass/shaker/nanite`（调酒器）、`/obj/item/rag/nanite`（抹布）、`/obj/item/knife/kitchen/nanite`（厨师刀，force 0 + wound 0）、`/obj/item/kitchen/rollingpin/nanite`（擀面杖，force 0）、`/obj/item/cultivator/nanite`（除草器，force 0）、`/obj/item/geneshears/nanite`（基因剪，force 0）、`/obj/item/secateurs/nanite`（修枝剪，force 0）、`/obj/item/shovel/spade/nanite`（铲子，force 0）、`/obj/item/hatchet/nanite`（斧头，force 0）、`/obj/item/mop/nanite`（拖把，force 0）。
- 法证工具（Grimoire Vacholiere）：`/obj/item/detective_scanner/nanite`（法证扫描仪，range 4）、`/obj/item/folder/yellow/nanite`（黄色文件夹）、`/obj/item/binoculars/nanite`（望远镜）、`/obj/item/toy/crayon/white/nanite`（白色粉笔，charges -1 无限、不可食用）。

### 3.3 NIFSoft 全录（A 类：基础与功能）

**① Soul Poem — 灵魂诗篇（Poem of Communal Souls）**
- 源码：`nifsofts/soul_poem.dm`；**预装在所有 NIF 上**（免费，`purchase_price = 0`）。
- 分类：`NIFSOFT_CATEGORY_FUN`；`persistence = TRUE`（保存昵称与消息）。
- 功能：Altspace 女巫团第一个委托作品。用户可设置"诗节"消息与昵称，佩戴者接近（1 格 `proximity_monitor`）时自动与附近其他佩戴者交换消息。TGUI（NifSoulPoem）可：更改消息、更改昵称、删除消息、开关发送（transmitting）、开关接收（receiving）。

**② Hivemind — 蜂巢思维**
- 源码：`nifsofts/hivemind.dm`；`purchase_price = 350`；激活成本 10，持续成本 0.2。
- 分类：`NIFSOFT_CATEGORY_UTILITY`。
- 功能：模拟史莱姆心灵感应的局部亚空间网络。激活后生成 **Hivemind Interface Device（蜂巢思维接口装置）**（全息手势控制器，用于过滤杂念）。提供动作：**Hivemind Configuration Settings（蜂巢配置）** —— 连接用户（需对方同意）/移除用户/离开蜂巢/切换活动蜂巢/更改聊天颜色/开关邀请。每人的 NIF 自带一个"X's Hivemind Link"网络，可加入他人的蜂巢网络。

**③ Scryer — NIFLink Holocaller（NIFLink 全息通话器）**
- 源码：`nifsofts/scryer.dm`；`purchase_price = 200`；激活成本 20，持续成本 1；`active_mode = TRUE`。
- 分类：`NIFSOFT_CATEGORY_UTILITY`。
- 功能：类似 MODsuit 的 Scryer 全息通话功能，颈圈硬光投影（`/obj/item/clothing/neck/link_scryer/loaded/nifsoft`）。激活时在颈部生成投影通话器（内嵌无限纳米电池 `Nanite Cell`），支持全息通话、自定义标签（副手交互）。被拷住或颈部被占用时无法激活。

**④ Money Sense — Automatic Appraisal（自动估价）**
- 源码：`nifsofts/money_sense.dm`；`active_mode = TRUE`，持续成本 0.5。
- 兼容：仅标准型 NIF（`compatible_nifs = /obj/item/organ/cyberimp/brain/nif/standard`）。
- 功能：脑内直连物品价格数据库。激活后检查物品实时显示出口价值（"This item has an export value of: X credits."）。

**⑤ Blood Steal — PHMS_v0.4（血液窃取）**
- 源码：`nifsofts/blood_steal.dm`（军用级）；激活成本 50，持续成本 15；`active_mode = TRUE`。
- 功能：伪炼金血分解维护系统（Pseudoalchemical Hemodecompositional Maintenance System），为 CQB 合成体设计的战斗纳米包，传授格斗术 **Blood Steal（血液窃取）**：
  - **Feedbacker（反馈者）**：重拳攻击，伤害+5（上下限），有效力+10，命中吸取目标 10 血量、自身按 BRUTE→BURN→OXY 顺序治疗 5 点、吸血 5 点；非机械手臂使用者自身受 75% 毒素伤害并失血 7。
  - **Knuckleblaster（指节爆弹）**：冲击波攻击，击退目标（2 格、7 速）、伤害 10（自伤 20/自身 5 或 10）、目标耐力 -10、自伤手臂严重钝伤。
  - **弹道格挡（Parry）**：投掷模式下手部可格挡投射物——机械手直接摧毁弹体；非机械手以 180°±3° 反弹弹体（速度与伤害 ×1.25、红色曳光、5 秒冷却）。
  - 仅限合成体（`issynthetic`）激活；非合成体激活会收到警告。

### 3.4 NIFSoft 全录（B 类：召唤魔典 Grimoires）

**基类：Grimoire Caeruleam（蔚蓝魔典）** — `/datum/nifsoft/summoner`
- 源码：`nifsofts/prop_summoner.dm`；`purchase_price = 175`；激活成本 100（约标准 NIF 能量的 1/10）；`cooldown = TRUE`（5 秒）；`able_to_keep = TRUE`。
- 分类：`NIFSOFT_CATEGORY_FUN`。
- 功能：Altspace 女巫团（2544 年数字化的后异教女巫）开发的开源虚拟召唤目录。召唤物（"Icons"）为纳米机械+硬光投影（全息滤镜：alpha 180、光强 2、蓝色 `#acccff` 染色），名字前缀 "cerulean "。最多同时召唤 **5** 件（`max_summoned_items = 5`）；可逐件驱散。**列表（11 种）**：

| 路径 | 名称 | 说明 |
|---|---|---|
| `/obj/item/toy/katana/nanite` | hexblade（六面剑） | 马拉泰斯坦决斗者贡献的"钝化"玩具剑，force 0 |
| `/obj/item/cane/nanite` | staff（手杖） | 地球东大陆"智慧权利恢复协会"贡献，实心核心，助残 |
| `/obj/item/cane/white/nanite` | white staff（白色手杖） | 同上协会贡献的白色盲杖 |
| `/obj/item/storage/dice/nanite` | dice set（骰子组） | 塞勒涅兵棋协会捐赠的稀有月晶骰子复刻 |
| `/obj/item/toy/cards/deck/nanite` | main deck（主牌组） | 防偷看纳米眼追踪牌组，支持 50 万+变体 |
| `/obj/item/toy/cards/deck/tarot/nanite` | tarot deck（塔罗牌） | 78 张塔罗，女巫团占卜用 |
| `/obj/item/toy/cards/deck/kotahi/nanite` | kotahi deck（Kotahi 牌组） | 银河第一甩牌游戏 Kotahi 数字版，200cr |
| `/obj/item/toy/foamblade/nanite` | armblade（臂刃） | 虎合作社泄露的"飞升训练"非致命版 |
| `/obj/item/lighter/nanite` | catchflame（捕焰） | 点燃大气氢分子，完美燃烧蓝光，等同 Zippo |
| `/obj/item/holocigarette/nanite` | cloudstick（云棒） | 匿名印痕贡献的电子烟，可换烟幕像素化 |
| `/obj/item/toy/eightball/nanite` | 八号球 | 胶体纳米占卜混沌引擎玩具 |

- 子类规则：`/datum/nifsoft/summoner/job`（职业魔典，**互斥：每人一份职业魔典**）；`/datum/nifsoft/summoner/combat`（战斗魔典，**互斥：每人一份战斗魔典**）。

**Grimoire 各分支全录：**

| 分支 | 名称 | 激活成本 | 上限 | 召唤物 |
|---|---|---|---|---|
| `/datum/nifsoft/summoner/job/tools` | **Grimoire Opera（歌剧院）** — Altspace 承包商技师版 Ceruleam 分叉 | 150 | 2 件 | 5 种通用工具（见 3.2），前缀 "covenant " |
| `/datum/nifsoft/summoner/job/surgery` | **Grimoire Asclepius（阿斯克勒庇俄斯）** — 火星红马尼拉 EMT 应急手术套装 | 150 | 2 件 | 9 种手术工具，前缀 "manilian " |
| `/datum/nifsoft/summoner/job/service` | **Grimoire Hestia（赫斯提亚）** — Zvirdnyan 制造重定用途算法，食物培育与备餐 | 100 | 3 件 | 11 种服务工具，前缀 "hestial " |
| `/datum/nifsoft/summoner/job/detective` | **Grimoire Vacholiere（瓦乔利尔）** — Zvirdnyan 殖民民兵订购的法证魔典 | 200 | 2 件 | 4 种法证工具，前缀 "vacholiere-" |
| `/datum/nifsoft/summoner/book` | **Grimoire Akasha（阿卡夏）** — 教育硬光书籍；`purchase_price = 0`（面向新手的免费工具） | 100（继承） | 2 本 | 全部 wiki 手册书（`subtypesof(/obj/item/book/manual/wiki)`），不可雕刻（cannot_carve） |
| `/datum/nifsoft/summoner/dorms` | **Grimoire Libidine（利比丁魔典）** — 成人玩具数据库（见 3.7） | 100（继承） | 5 件（继承） | 无全息滤镜，前缀 "libidine " |

### 3.5 NIFSoft 全录（C 类：职业与军用）

**C1. 职业 HUD 天眼镜（Job Scrying Lenses）** — 详见 3.6 D 类。

**C2. 军用级（mil_grade）：**

| 程序 | 名称 | 关键参数 |
|---|---|---|
| `/datum/nifsoft/hud/thermal` | **Thermal Lens（热成像镜片）** | 军用级视觉增强包：直接改造佩戴者眼部（有机或机械）感知红外线，可透视墙壁与致密材料；激活时需佩戴兼容眼镜（`eyewear_check = TRUE`，从眼镜"借用"聚合物，关闭时完整归还，无取证痕迹）；持续成本 1；`TRAIT_THERMAL_VISION` |
| `/datum/nifsoft/blood_steal` | **PHMS_v0.4（血液窃取）** | 见 3.3 ⑤ |

### 3.6 NIFSoft 全录（D 类：HUD 天眼镜）

**基类：Scrying Lens（天眼镜）** — `/datum/nifsoft/hud`（`active_mode = TRUE`，持续成本 0.5）
- 源码：`nifsofts/huds.dm`；兼容仅标准型 NIF。
- 设定：历史最悠久的 NIFSoft 类型之一，纳米机械充当人工隐形眼镜；Nanotrasen 法规要求这些分叉改为与用户已佩戴的眼镜整合。
- 机制：`eyewear_check` 类程序激活时要求佩戴兼容眼镜（带 `TRAIT_NIFSOFT_HUD_GRANTER` 特质，由 HUD 适配器或自带眼镜提供）；脱眼镜自动停用。
- 子类互斥：职业 HUD（`/datum/nifsoft/hud/job`）互斥——**不能叠加职业 HUD**。

**全部 7 种：**

| 程序路径 | 名称 | HUD 特质 | UI 图标 |
|---|---|---|---|
| `/datum/nifsoft/hud` | Scrying Lens（天眼镜，基类） | 自定义 `hud_type`/`hud_traits` | eye |
| `/datum/nifsoft/hud/job/medical` | Medical Scrying Lens（医疗天眼镜） | `TRAIT_MEDICAL_HUD`（医疗 HUD） | staff-snake |
| `/datum/nifsoft/hud/job/diagnostic` | Diagnostic Scrying Lens（诊断天眼镜） | `TRAIT_DIAGNOSTIC_HUD`（诊断 HUD） | robot |
| `/datum/nifsoft/hud/job/security` | Security Scrying Lens（安保天眼镜） | `TRAIT_SECURITY_HUD`（安保 HUD） | shield |
| `/datum/nifsoft/hud/job/cargo_tech` | Permit Scrying Lens（许可证天眼镜） | `TRAIT_PERMIT_HUD`（许可证 HUD） | gun |
| `/datum/nifsoft/hud/job/science` | Science Scrying Lens（科学天眼镜） | `TRAIT_REAGENT_SCANNER`（试剂扫描）+ `TRAIT_RESEARCH_SCANNER`（研究扫描） | flask |
| `/datum/nifsoft/hud/job/meson` | Meson Scrying Lens（介子天眼镜） | `TRAIT_MADNESS_IMMUNE`（疯狂免疫）+ `TRAIT_MESON_VISION`（介子视觉） | radiation |

**HUD 眼镜（NIF HUD Glasses）：**

| 物品 | 名称 | 说明 |
|---|---|---|
| `/obj/item/clothing/glasses/trickblindfold/obsolete/nif` | modernized fake blindfold（现代化假眼罩） | 翻新复古假眼罩，预装 NIF HUD 电子元件（自带 `TRAIT_NIFSOFT_HUD_GRANTER`） |
| `/datum/element/nifsoft_hud` | NIFSoft HUD 元素 | 为眼镜添加 `TRAIT_NIFSOFT_HUD_GRANTER` 特质与检查文本 |

### 3.7 NIFSoft 全录（E 类：成人类）

> 服务器开启 `disable_lewd_items` 时，以下程序与数据盘全部自动销毁/下架。

| 程序 | 名称 | 参数 | 说明 |
|---|---|---|---|
| `/datum/nifsoft/action_granter/hypnosis` | **Libidine Eye（利比丁之眼）** — 数据盘名为 Purpura Eye（紫瞳） | `purchase_price = 150`；持续成本 0.1；`lewd_nifsoft = TRUE`；`able_to_keep = TRUE` | 基于 LustWish 贩卖机催眠设备的 NIFSoft。授予动作 **Hypnotize（催眠）**：对目标（需主动抓握 GRAB_AGGRESSIVE、目标开启相关偏好）进行 12 秒引导，目标同意后入睡 60 秒，期间可下达多条"暗示"（Suggestion）或释放（Release） |
| `/datum/nifsoft/hypno` | **Libidine Contract（利比丁契约）** | `purchase_price = 0`；`lewd_nifsoft = TRUE` | 强制佩戴者"遵守"契约中存储的规则（假法律 `fake_laws`，纯 RP 无强制力，可随时卸载）。契约数据盘可自定义法律文本、可重复使用 |
| `/datum/nifsoft/summoner/dorms` | **Grimoire Libidine（利比丁魔典）** | `purchase_price = 150`；`lewd_nifsoft = TRUE`；`obj_flags_nova = ERP_ITEM`（数据盘） | 成人玩具数据库（Dorms 贩卖机精简版），无全息滤镜，前缀 "libidine "。**召唤物全录（39 种）**：eggvib/signalvib、eggvib、buttplug、nipple_clamps、dildo/double_dildo、vibroring、dildo/custom_dildo、tickle_feather、fleshlight、kinky_shocker、mask/leatherwhip、sextoy/magic_wand、bdsm_candle、spanking_pad、sextoy/vibrator、restraints/handcuffs/lewd、holosign_creator/privacy、mask/muzzle/ballgag、mask/muzzle/ballgag/choking、mask/muzzle/ring、head/deprivation_helmet、glasses/blindfold/dorms、ears/dorms_headphones、mask/gas/bdsm_mask、glasses/hypno、neck/collar/holocollar、under/misc/latex_catsuit、suit/corset、suit/straight_jacket/latex_straight_jacket、suit/straight_jacket/shackles、suit/straight_jacket/kinky_sleepbag、neck/mind_collar、strapon、gloves/ball_mittens、gloves/long_gloves、shoes/latex_socks、shoes/ballet_heels |

**A 类补充：Polymorph（变形）** — `/datum/nifsoft/action_granter/shapeshifter`
- 源码：`nifsofts/shapeshifter.dm`；`purchase_price = 350`；兼容仅标准型 NIF。
- 分类：`NIFSOFT_CATEGORY_COSMETIC`。
- 功能：纳米通道大规模改造，允许低层次变形（质量守恒限制，不能离本体形态太远）。授予动作 **Polymorph（变形）**：径向菜单选择 **DNA / Hair（发型）/ Markings（印记）** 三项修改（NIF 版无法改变肤色）。

### 3.8 NIFSoft 商店（NIFSoft Catalog）

`/datum/computer_file/program/nifsoft_downloader` — **NIFSoft Catalog（NIFSoft 目录）** 程序：

- `filename` = "nifsoftcatalog"；`size` = 3；TGUI：`NtosNifsoftCatalog`；图标 bag-shopping。
- 运行平台：PDA（`PROGRAM_PDA`）；商店标志：NTNET 商店 + 需要 NTNET 连接。
- 功能：虚拟商店——安装 NIFSoft 到用户的 NIF 并购买 NIF 相关产品。支付方式：① 信用点（`paying_account`，PDA ID 卡银行账户）；② **奖励点（Rewards Points）**（NIF 内存储，安装程序时按 0.5×购买价积累，上限 2000）。
- **商店商品全录（9 种，`GLOB.purchasable_nifsofts`）**：

| # | 程序 | 名称 | 价格 | 分类 |
|---|---|---|---|---|
| 1 | `/datum/nifsoft/hivemind` | Hivemind（蜂巢思维） | 350 cr | 实用 |
| 2 | `/datum/nifsoft/summoner` | Grimoire Caeruleam（蔚蓝魔典） | 175 cr | 娱乐 |
| 3 | `/datum/nifsoft/action_granter/shapeshifter` | Polymorph（变形） | 350 cr | 外观 |
| 4 | `/datum/nifsoft/summoner/dorms` | Grimoire Libidine（利比丁魔典） | 150 cr | 成人 |
| 5 | `/datum/nifsoft/soul_poem` | Poem of Communal Souls（灵魂诗篇） | 0 cr（随 NIF 免费） | 娱乐 |
| 6 | `/datum/nifsoft/soulcatcher` | Soulcatcher（捕魂器） | 150 cr | — |
| 7 | `/datum/nifsoft/scryer` | NIFLink Holocaller（NIFLink 全息通话器） | 200 cr | 实用 |
| 8 | `/datum/nifsoft/summoner/book` | Grimoire Akasha（阿卡夏） | 0 cr（免费） | 信息 |
| 9 | `/datum/nifsoft/action_granter/hypnosis` | Libidine Eye（利比丁之眼） | 150 cr | 成人 |

---

## 4. TGUI 界面（NifPanel）

源码：`code/nifs_tgui.dm`（152 行）+ `code/nif_actions.dm`（34 行）

- **入口**：植入后获得动作按钮 **Open NIF Menu（打开 NIF 菜单）**（`/datum/action/item_action/nif/open_menu`）。仅佩戴者本人可打开（`ui_status` 非本人返回 UI_CLOSE）；意识清醒才可操作（`GLOB.conscious_state`）。
- 校准中/耐久 < 1/损坏时无法打开菜单（NIF 发送相应提示）。
- **主题（11 种）**：`abductor`（外星人）、`cardtable`（牌桌）、`hackerman`、`malfunction`（故障）、`default`（默认）、`ntos`、`ntos_darkmode`、`ntOS95`、`ntos_synth`、`ntos_terminal`、`wizard`（巫师）。切换主题会同步应用到所有已装 NIFSoft。
- **界面功能全录**：
  - 查看：功率等级/功率使用、营养水平、血量、耐久、奖励点数、制造商注释、NIF 检查文本。
  - 切换：营养抽取（toggle_nutrition_drain）、血液抽取（toggle_blood_drain）。
  - 自定义检查文本（change_examine_text，≤6 字符恢复默认）。
  - NIFSoft 管理：激活/停用（activate_nifsoft）、卸载（uninstall_nifsoft）、标记跨回合保留（toggle_keeping_nifsoft，仅 `able_to_keep` 程序）。

---

## 5. 植入体设备全录（misc_devices.dm）

源码：`code/misc_devices.dm`（207 行）——共 **4 件设备 + 1 件物品 + 1 个黑市条目**：

**① Nanotrasen 'Wrangler' NIF-Cutter（纳诺崔森"牧马人"NIF 切割器）** — `/obj/item/nifsoft_remover`
- 描述：小型设备，允许用户从 NIF 佩戴者身上移除 NIFSoft。私人军事公司为应对 NIF 犯罪潮而发明，粗暴擦除框架上的程序。
- 材料：银半片 + 铀半片 + 铁小量。
- 使用：攻击目标 → 5 秒引导 → 选择要移除的程序 → 再 5 秒引导 → 程序被删除。`create_disk = FALSE`（不生成磁盘）。

**② Cybersun 'Scalpel' NIF-Cutter（赛博太阳"手术刀"NIF 切割器）** — `/obj/item/nifsoft_remover/syndie`
- 描述：改良版切割器，移除 NIFSoft 的同时将空白副本保存到数据盘（`create_disk = TRUE`）。赛博太阳工业为"神经盗窃"领域发明的设备，5 秒内提取特定程序。
- 获取：**上行链接（Uplink）3 TC**（`/datum/uplink_item/device_tools/nifsoft_remover`）。

**③ Cerulean NIF Regenerator（蔚蓝 NIF 再生器）** — `/obj/item/nif_repair_kit`
- 描述：免手术修复 NIF 的修理包。Altspace 女巫团开发的开源替代品（原厂固件锁定"认证"品牌修补膏）。
- 参数：`repair_amount = 20`（每次 +20 耐久）；`uses = 5`（共 5 次）；重量小（WEIGHT_CLASS_SMALL）。
- 使用：对目标 5 秒引导，修复其 NIF 耐久。

**④ Scrying Lens Adapter（天眼镜适配器）** — `/obj/item/nif_hud_adapter`
- 描述：改造指定眼镜以显示 NIF HUD 的工具包。
- 材料：铁 2 片 + 玻璃 2 片 + 塑料 1 片。
- 使用：对兼容眼镜使用 → 眼镜更名 "HUD-upgraded X" 并添加 `nifsoft_hud` 元素（获得 `TRAIT_NIFSOFT_HUD_GRANTER`）。默认单次使用（`multiple_uses = FALSE`）。
- **兼容眼镜全录（17 种）**：trickblindfold（假眼罩）、monocle（单片眼镜）、fake_sunglasses（假墨镜）、regular（普通眼镜）、eyepatch（眼罩）、osi、phantom、salesman、nice_goggles、regular/thin（细框）、biker（骑行镜）、sunglasses/gar（Gar 墨镜）、hypno（催眠镜）、heat（热工镜）、cold（冷工镜）、orange（橙色镜）、red（红色镜）、psych（心理镜）。

**⑤ modernized fake blindfold（现代化假眼罩）** — `/obj/item/clothing/glasses/trickblindfold/obsolete/nif`（见 3.6 HUD 眼镜）。

---

## 6. 科研设计图全录（nif_research.dm）

源码：`code/nif_research.dm`（196 行）——共 **13 个设计图**（全部 `PROTOLATHE | AWAY_LATHE` 原型机/远距车床）：

| 设计图 | 名称 | 产物 | 材料 | 部门 |
|---|---|---|---|---|
| `/datum/design/nifsoft_remover` | Nanotrasen 'Wrangler' NIF-Cutter | `/obj/item/nifsoft_remover` | 铁小量+银半片+铀半片 | 安保 |
| `/datum/design/nifsoft_money_sense` | Automatic Appraisal NIFSoft（自动估价） | `/obj/item/disk/nifsoft_uploader/job/money_sense` | 铁 1 片+银半片+塑料 1 片 | 货运 |
| `/datum/design/soulcatcher_device` | Evoker-Type RSD（召唤师型 RSD） | `/obj/item/handheld_soulcatcher` | 铁 3 片+银 1 片+蓝空间 1 片 | 医疗+科研 |
| `/datum/design/mini_soulcatcher` | Poltergeist-Type RSD（波尔格斯特型 RSD） | `/obj/item/attachable_soulcatcher` | 玻璃半片+铁半片 | 科研+服务+医疗 |
| `/datum/design/nif_detective_tools` | Grimoire Vacholiere NIFSoft | `/obj/item/disk/nifsoft_uploader/job/summoner/detective` | 金刚石小量+金半片+铀半片 | 安保 |
| `/datum/design/nif_surgery_tools` | Grimoire Asclepius NIFSoft | `/obj/item/disk/nifsoft_uploader/job/summoner/surgery` | 金刚石小量+金半片+铀半片 | 医疗 |
| `/datum/design/nif_service_tools` | Grimoire Hestia NIFSoft | `/obj/item/disk/nifsoft_uploader/job/summoner/service` | 金刚石小量+金半片+铀半片 | 服务 |
| `/datum/design/nif_general_tools` | Grimoire Opera NIFSoft | `/obj/item/disk/nifsoft_uploader/summoner/tools` | 金刚石小量+金半片+铀半片 | 全部门 |
| `/datum/design/nifsoft_hud`（基类） | —（仅定义材料与分类：铁 1 片+银半片+塑料 1 片） | — | — | 装备 |
| `/datum/design/nifsoft_hud/medical` | Medical HUD NIFSoft | `/obj/item/disk/nifsoft_uploader/job/med_hud` | 同上 | 医疗 |
| `/datum/design/nifsoft_hud/security` | Security HUD NIFSoft | `/obj/item/disk/nifsoft_uploader/job/sec_hud` | 同上 | 安保 |
| `/datum/design/nifsoft_hud/cargo` | Permit HUD NIFSoft | `/obj/item/disk/nifsoft_uploader/job/permit_hud` | 同上 | 货运 |
| `/datum/design/nifsoft_hud/diagnostic` | Diagnostic HUD NIFSoft | `/obj/item/disk/nifsoft_uploader/job/diag_hud` | 同上 | 科研 |
| `/datum/design/nifsoft_hud/science` | Science HUD NIFSoft | `/obj/item/disk/nifsoft_uploader/job/sci_hud` | 同上 | 科研+服务+医疗 |
| `/datum/design/nifsoft_hud/meson` | Meson HUD NIFSoft | `/obj/item/disk/nifsoft_uploader/job/meson_hud` | 同上 | 货运+工程 |
| `/datum/design/nif_hud_kit` | NIF HUD Retrofitter（NIF HUD 改装器） | `/obj/item/nif_hud_adapter` | 铁 2 片+玻璃 2 片+塑料 1 片 | 全部 6 部门 |

> 注：`/datum/design/nifsoft_hud` 为基类设计图（不含 `build_path`，仅定材料），实际可制作的是其 6 个子型；加上其余 7 个独立设计图，共 **13 个可产物品项**。

---

## 7. 跨回合持久化（nif_persistence.dm）

源码：`code/nif_persistence.dm`（126 行）

- **数据保存**（`save_nif_data`）：回合结束时将 NIF 路径、耐久、主题、校准状态、奖励点、检查文本、持久化 NIFSoft 列表写入 `datum/modular_persistence`（存于大脑器官）。
- **跨回合保留程序**：仅 `able_to_keep = TRUE` 且用户勾选 `keep_installed` 的 NIFSoft 跨回合保留；`persistence = TRUE` 的程序另存专属数据（灵魂诗篇的昵称/消息、捕魂器的房间列表）。
- **未佩戴惩罚**：有档案但回合结束时未安装 NIF → 档案耐久 **-25**（`LOSS_WITH_NIF_UNINSTALLED`），不会丢失植入物；耐久归零后**有且仅有一个回合**的修复机会，否则 NIF 档案被清空。
- **幽灵角色**（`TRAIT_GHOSTROLE`）：不保存/加载任何 NIF 数据。
- **加载**（`load_nif_data`）：按档案重建 NIF 并植入，自动在 PDA 中安装 NIFSoft Catalog 程序。

---

## 8. NIF 动作与菜单入口

源码：`code/nif_actions.dm`（34 行）

| 动作 | 名称 | 说明 |
|---|---|---|
| `/datum/action/item_action/nif` | 基类 | 背景图标 android、按钮图标 actions_nif.dmi，需意识清醒（AB_CHECK_CONSCIOUS） |
| `/datum/action/item_action/nif/open_menu` | **Open NIF Menu（打开 NIF 菜单）** | 打开 NifPanel TGUI；校准中/耐久不足/损坏时拒绝并提示 |
| `/datum/action/innate/soulcatcher` | **Soulcatcher（捕魂器）** | 由 Soulcatcher NIFSoft 授予，打开捕魂器界面 |
| `/datum/action/innate/hivemind_config` | **Hivemind Configuration Settings（蜂巢配置）** | 由 Hivemind 授予，管理蜂巢网络 |
| `/datum/action/innate/hivemind_keyboard` | **Hivemind Keyboard（蜂巢键盘）** | 由 Hivemind 授予，重新生成接口装置 |
| `/datum/action/innate/alter_form/nif` | **Polymorph（变形）** | 由 Polymorph 授予，DNA/发型/印记修改 |
| `/datum/action/innate/nif_hypnotize` | **Hypnotize（催眠）** | 由 Libidine Eye 授予（见 3.7） |

---

## 9. 相关硬件：Soulcatcher RSD 捕魂器设备

源码：`code/soulcatcher/`（8 个文件，1,463 行）

**背景设定**：RSD（Resonance Simulation Device，共振模拟设备）——能够承载某人意识（"共振"）的仪器。"Jin-Padmanabhan Resonance / JP / Soul Resonance" 概念由研究员 Yun-Seo Jin 与 Kamakshi Padmanabhan 于 2500 年代初发现。**Soulcatcher NIFSoft（捕魂器，150cr，persistence + able_to_keep）** 是 NIF 的纳米系统近乎完全升级版，功能等同便携 RSD。

**设备全录：**

| 设备 | 名称 | 说明 |
|---|---|---|
| `/obj/item/handheld_soulcatcher` | **Evoker-type RSD（召唤师型 RSD）** | 手持式捕魂器，为医疗领域设计（为身体修复/生产期间的"临时离世者"提供慰藉）。本质是特制手持 NIF。可容纳大量印痕（Engram）无限期，可虚拟空间模拟、可与 NIF 互相转移灵魂。可挂腰带（ITEM_SLOT_BELT），可自定义重命名（UNIQUE_RENAME），名称带随机编号，自动登记兴趣点。材料：铁 3 片+银 1 片+蓝空间 1 片。扫描活人/尸体需本人同意（拒绝后 2 分钟冷却 `RSD_ATTEMPT_COOLDOWN`）；副手攻击可将捕魂器中灵魂注入 RSD 兼容大脑（`TRAIT_RSD_COMPATIBLE`） |
| `/obj/item/attachable_soulcatcher` | **Poltergeist-Type RSD（波尔格斯特型 RSD）** | 多形态纳米机械网，包裹物体使其成为"共振容器"。灵魂可感知外界、以新形态说话、小幅操控容器。小体积（WEIGHT_CLASS_SMALL）；材料：铁半片+玻璃半片。**黑名单物品（7 类）**：organ（器官）、mmi、pai_card、aicard、card、radio、disk/nuclear（核盘）；附着后原设备默认销毁（destroy_on_use = TRUE），可分离 |
| `/datum/component/soulcatcher` | Soulcatcher 组件 | 灵魂房间（soulcatcher_rooms）管理、TGUI、消息中继。`ghost_joinable = TRUE`（幽灵可加入）、`require_approval = TRUE`（需房主批准）、`max_souls = FALSE`（默认无上限）、`communicate_as_parent = FALSE`、`removable = FALSE` |
| `/datum/component/soulcatcher/small_device` | 小型设备组件 | `max_souls = 1`（单灵魂） |
| `/datum/component/soulcatcher/attachable_soulcatcher` | 可附着组件 | `max_souls = 1`、`communicate_as_parent = TRUE`、`removable = TRUE`（Ctrl+Shift 点击开界面，检查可见提示） |
| `/datum/component/soulcatcher/modular_laser` | 模块化激光组件 | `max_souls = 1`、`communicate_as_parent = TRUE`、`ghost_joinable = FALSE`；用于 Hoshi 与 Hyeseong 武器 |
| `/datum/component/previous_body` | 前躯体组件 | 标记灵魂的原躯体；扫描后恢复灵魂名与外貌文本；销毁时（`restore_mind = TRUE`）将意识放回原躯体 |
| `/mob/living/soulcatcher_soul` | 捕魂器灵魂 | 灵魂状态：`outside_sight/outside_hearing`（外界感知，默认开）、`internal_sight/internal_hearing`（内部感知，默认开）、`able_to_emote/able_to_speak`（默认开）、`able_to_rename`、`able_to_speak_as_container/able_to_emote_as_container`（以容器身份发言）、`communicating_externally`、`able_to_leave = TRUE`、`round_participant`、`body_scan_needed`；自带动作：离开捕魂器（leave_soulcatcher）、捕魂器用户界面 |
| `/obj/item/soulcatcher_holder` | Soul Holder（灵魂容器） | 抽象物品（ABSTRACT/DROPDEL），承载灵魂的容器对象，位于 mob 内容物中 |

**玩家动词（IC 频道）：**

| 动词 | 名称 | 功能 |
|---|---|---|
| `/mob/living/proc/soulcatcher_say` | 灵魂发言 | 向当前瞄准的捕魂器房间发送 Say 消息 |
| `/mob/living/proc/soulcatcher_emote` | 灵魂动作 | 向当前瞄准的捕魂器房间发送 Emote |

---

## 10. 关键数值速查表

| 项目 | 数值 | 出处 |
|---|---|---|
| NIF 校准时长 | 标准型 3 分钟 / 经济甲板型 1 分钟 | `nifs.dm` / `nif_implants.dm` |
| NIF 最大功率 | 标准型 1000 / 经济甲板型 500 | 同上 |
| NIF 最大程序槽（max_nifsofts） | 标准型 5 / 经济甲板型 3 | 同上 |
| NIF 最大耐久 | 标准型 100 / 经济甲板型 50 | 同上 |
| 死亡耐久损失（death_durability_loss） | 10（两型号相同） | 同上 |
| 死亡后免损宽限期 | 20 分钟 | `nifs.dm` |
| EMP 耐久损失 | severity 1 → -10；severity 2 → -5 | `nifs.dm` |
| EMP 后 broken 时长 / 恢复易损 | 30 秒 / 3 分钟 | `nifs.dm` |
| 奖励点上限（MAX_NIF_REWARDS_POINTS） | 2000 | `nifs.dm` |
| 奖励点获取率（rewards_points_rate） | 0.5 × 购买价 | `nifsofts.dm` |
| 营养抽取速率 / 转换率 / 最低营养 | 1.5 / 秒；×5；25 | `nifs.dm` |
| 血液抽取速率 / 转换率 / 最低血量 | 1 / 秒；×5（满血≈500 电量）；BLOOD_VOLUME_SAFE | `nifs.dm` |
| NIFSoft 默认激活冷却（DEFAULT_NIFSOFT_COOLDOWN） | 5 秒 | `nifsofts.dm` |
| NIFSoft 激活成本 | 各程序不同：召唤 100、魔典分支 100–200、蜂巢 10、全息通话 20、血液窃取 50 | 各 nifsofts 文件 |
| 数据盘上传引导时间 | 5 秒（对他人） | `nifsofts.dm` |
| 未佩戴 NIF 的回合结束惩罚 | 耐久 -25（一回合修复宽限） | `nif_persistence.dm` |
| 蔚蓝 NIF 再生器 | 每次 +20 耐久，共 5 次 | `misc_devices.dm` |
| 赛博太阳 NIF 切割器（上行链接） | 3 TC | `misc_devices.dm` |
| 召唤物全息滤镜 | alpha 180、光强 2、色 #acccff | `prop_summoner.dm` |
| 魔典召唤上限 | 蔚蓝 5 件 / Opera·Asclepius·Vacholiere 2 件 / Hestia 3 件 / Akasha 2 本 | 各 summoner 文件 |
| 捕魂器拒绝冷却（RSD_ATTEMPT_COOLDOWN） | 2 分钟 | `handheld_soulcatcher.dm` |
| 催眠入睡时长 | 60 秒（引导 12 秒） | `hypnosis.dm` |
| 血液窃取格挡冷却 | 5 秒 | `blood_steal.dm` |

---

## 11. 源码路径索引

| 源码路径（相对 `modular_nova/modules/modular_implants/`） | 行数 | 内容 |
|---|---|---|
| `code/nifs.dm` | 535 | NIF 母版、校准、耐久、能量、防盗、新手包 |
| `code/nif_implants.dm` | 37 | NIF 型号（标准/经济甲板/试用轻量/幽灵角色） |
| `code/nifs_tgui.dm` | 152 | NifPanel TGUI（主题、管理功能） |
| `code/nif_actions.dm` | 34 | 打开菜单动作 |
| `code/nifsofts.dm` | 230 | NIFSoft 基类、数据盘 |
| `code/nifsoft_catalog.dm` | 144 | NIFSoft 商店程序（9 种商品） |
| `code/nifsofts/base_types/action_granter.dm` | 26 | 行动授予基类 |
| `code/nifsofts/book_summoner.dm` | 36 | Grimoire Akasha |
| `code/nifsofts/blood_steal.dm` | 287 | PHMS_v0.4 血液窃取格斗术 |
| `code/nifsofts/detective_summoner.dm` | 61 | Grimoire Vacholiere |
| `code/nifsofts/dorms.dm` | 112 | Grimoire Libidine、Libidine Contract |
| `code/nifsofts/hivemind.dm` | 303 | Hivemind 蜂巢思维 |
| `code/nifsofts/huds.dm` | 173 | Scrying Lens 天眼镜全系列 |
| `code/nifsofts/hypnosis.dm` | 66 | Libidine Eye 催眠 |
| `code/nifsofts/money_sense.dm` | 59 | Automatic Appraisal 自动估价 |
| `code/nifsofts/prop_summoner.dm` | 219 | Grimoire Caeruleam 召唤物全录 |
| `code/nifsofts/scryer.dm` | 115 | NIFLink Holocaller |
| `code/nifsofts/service_summoner.dm` | 89 | Grimoire Hestia |
| `code/nifsofts/shapeshifter.dm` | 45 | Polymorph 变形 |
| `code/nifsofts/soul_poem.dm` | 217 | Poem of Communal Souls 灵魂诗篇 |
| `code/nifsofts/soulcatcher.dm` | 137 | Soulcatcher NIFSoft |
| `code/nifsofts/surgery_summoner.dm` | 58 | Grimoire Asclepius |
| `code/nifsofts/thermals.dm` | 17 | Thermal Lens 热成像镜片 |
| `code/nifsofts/tool_summoner.dm` | 37 | Grimoire Opera |
| `code/misc_devices.dm` | 207 | NIF 切割器×2、再生器、HUD 适配器 |
| `code/nif_research.dm` | 196 | 科研设计图 13 项 |
| `code/nif_persistence.dm` | 126 | 跨回合持久化 |
| `code/soulcatcher/attachable_soulcatcher.dm` | 110 | 波尔格斯特型 RSD |
| `code/soulcatcher/handheld_soulcatcher.dm` | 163 | 召唤师型 RSD |
| `code/soulcatcher/soulcatcher_body_component.dm` | 63 | 前躯体组件 |
| `code/soulcatcher/soulcatcher_component.dm` | 490 | 捕魂器组件与房间 |
| `code/soulcatcher/soulcatcher_devices.dm` | 4 | 模块化激光组件 |
| `code/soulcatcher/soulcatcher_mob.dm` | 237 | 捕魂器灵魂 |
| `code/soulcatcher/soulcatcher_tgui.dm` | 348 | 捕魂器 TGUI |
| `code/soulcatcher/soulcatcher_verbs.dm` | 58 | 灵魂发言/灵魂动作动词 |

---

*本文档由源码自动提取生成：NIF 核心系统（`nifs.dm`+`nif_implants.dm`+`nifs_tgui.dm`+`nif_actions.dm`，4 文件 758 行）+ NIFSoft 软件（`nifsofts.dm`+`nifsoft_catalog.dm`+`nifsofts/` 18 文件，20 文件 2,431 行）+ 设备与科研（`misc_devices.dm`+`nif_research.dm`+`nif_persistence.dm`，3 文件 529 行）+ Soulcatcher 相关硬件（`code/soulcatcher/`，8 文件 1,473 行）= 35 个 .dm，共 5,191 行。*
