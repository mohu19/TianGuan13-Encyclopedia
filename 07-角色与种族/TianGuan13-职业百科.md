# 天关 — 职业百科（2026-08-06 深度翻新版）

> 按心理医生专精深度格式重写：每职业含基础信息表/技能芯片逐个 TRAIT 分析/装备部位表/背包内容/特质（mind_traits+liver_traits）/邮件礼物/Nova 扩展/一句话总结。数据全部从源码精确提取。
> 覆盖：指挥链 3 + 安保 5 + 部门警卫 6 + 工程 3 + 医疗 9（含心理医生）+ 科研 4 + 货运 4 + 服务 9 + NOVA 特有 10 = 50+ 职业 + 技能芯片全录。

---

## 第一篇 · 指挥链与安保部



> 本章节覆盖：【指挥链】船长 / 人事部长 / NT顾问；【安保部】安全部长 / 看守 / 保安官 / 侦探 / 律师；【部门警卫系】狱警 / 秩序员 / 科学警卫 / 海关 / 保安 / 工程警卫（共 14 个岗位系）。
> 数值与路径全部提取自 `TianGuan13-master`（NovaSector 分支）源码，双语标注。

---

### 1. 船长 (Captain)

**代码**: `code/modules/jobs/job_types/captain.dm`（Nova 覆写：`modular_nova/master_files/code/modules/jobs/job_types/captain.dm`）

#### 基础信息表

| 项目 | 内容 |
|---|---|
| 岗位数量 | 1 总 / 1 出生 (`total_positions = 1` / `spawn_positions = 1`) |
| 上级 | Nanotrasen officials and Space Law（NT 官员 + 太空法，无需向站内任何人汇报） |
| 部门 | 指挥部 (`/datum/job_department/command` + `/datum/job_department/captain`) |
| 工资等级 | PAYCHECK_COMMAND（指挥级工资） |
| 发薪部门 | ACCOUNT_CMD（指挥账目）⚠️ Nova 覆写：原版 TG 为 `ACCOUNT_SEC` |
| 经验要求 | 180 分钟；经验类型 = 船员经验 (EXP_TYPE_CREW)，且要求指挥经验 (EXP_TYPE_COMMAND)；发放船员经验 |
| 玩家年龄 | 14 天（minimal_player_age），`req_admin_notify = 1` |
| 家族遗物 | 金色酒壶 (flask/gold)、"船长语录"收集玩具 (toy/captainsaid/collector) |
| 其他 | `rpg_title = "Star Duke"`；`voice_of_god_power = 1.4`（指挥层威权）；`human_authority = JOB_AUTHORITY_HUMANS_ONLY`（仅人类）；`JOB_ANTAG_PROTECTED`（反叛徒保护） |

#### 技能芯片 (Skillchip)

**代码**: `code/modules/library/skill_learning/generic_skillchips/misc.dm` L47-55

```
/obj/item/skillchip/disk_verifier
  name = "K33P-TH4T-D15K skillchip"
  desc = "A skillchip with a tiny print of a nuclear authentification disk stamped onto it."
  auto_traits = list(TRAIT_DISK_VERIFIER)
  skill_name = "Nuclear Disk Verification"
  skill_description = "Nuclear authentication disks have an extremely long serial number for verification. \
    This skillchip stores that number, which allows the user to automatically spot forgeries."
```

船长出生自带 **disk_verifier 核磁盘验证芯片**（`skillchips = list(/obj/item/skillchip/disk_verifier)`）。

**效果 (TRAIT_DISK_VERIFIER)**: 大脑内储存核认证磁盘的超长序列号，**可以自动识别核磁盘的真伪**（核反恐任务核心能力——防止辛迪加用赝品磁盘骗过核弹代码）。

**直观理解**: 船长自带"核磁盘验钞机"，拿着假磁盘一眼就能看穿。

#### 初始物品/装备

**代码**: `/datum/outfit/job/captain`

| 部位 | 物品 |
|---|---|
| ID | `/obj/item/card/id/advanced/gold` — 金色高级ID卡 |
| ID权限 | `/datum/id_trim/job/captain` — 全站最高权限 |
| 制服 | 船长制服 (under/rank/captain) |
| 外套 | 船长装甲胸甲 (suit/armor/vest/capcarapace) |
| 腰带 | 船长PDA (modular_computer/pda/heads/captain) |
| 耳机 | 船长耳机·alt 弓形版 (radio/headset/heads/captain/alt，防闪光弹) |
| 眼镜 | 墨镜 (clothing/glasses/sunglasses) |
| 手套 | 船长手套 (clothing/gloves/captain) |
| 头部 | 船长帽 (head/hats/caphat) |
| 鞋子 | 系带皮鞋 (shoes/laceup) |
| 饰品 | 金色船长勋章 (accessory/medal/gold/captain) |
| 植入体 | 心灵屏蔽植入体 (implant/mindshield) |
| 背包 | 船长背包 / 挎包(cap) / 行李袋(captain) / 斜挎包(messenger/cap) |

**背包内容 (backpack_contents)**:
```
/obj/item/melee/baton/telescopic/gold = 1  — 金色伸缩警棍
/obj/item/station_charter = 1             — 站长宪章（部分地图替换为站旗 banner，pre_equip 处理）
```

**变色龙装备 (chameleon_extras)**: e_gun 能量枪 + 船长印章 (stamp/head/captain)

**MODsuit 变体** (`/datum/outfit/job/captain/mod`)：`pre_equipped/magnate` 大亨MOD + 大气用船长防毒面具 (mask/gas/atmos/captain)，氧气罐挂外套槽。

#### 特质

**心灵特质 (mind_traits)** — `HEAD_OF_STAFF_MIND_TRAITS`（`code/__DEFINES/jobs.dm` L347，所有部门主管共享）:
| 特质 | 效果 |
|---|---|
| `TRAIT_FAST_TYING` | 打领带/解领带速度减半（`_neck.dm` L97：`tie_timer_actual *= 0.5`），部门主管都是系领带专家 |
| `TRAIT_HIGH_VALUE_RANSOM` | 被海盗绑架后赎金 ×3：普通船员 1000 → 主管 3000 信用点（`pirate_shuttle_equipment.dm` L451） |

另有 `desensitized_base = DESENSITIZED_THRESHOLD`（0.5，见 `code/__DEFINES/mood.dm` L117）—— 接触恐怖事物后的"脱敏"门槛值更高（更不容易被吓到）。

**肝特质 (liver_traits)**:
```
liver_traits = list(TRAIT_ROYAL_METABOLISM)
```
**TRAIT_ROYAL_METABOLISM — 皇家代谢**:
- 开香槟 (sabrage) 成功率 **+20%**（`glassbottle.dm` L700：`command_bonus = 20`）——配合邮件可能送来的 sabrage 技能芯片效果更佳
- 喝下"皇家地毯"试剂 (Royal Carpet) 时会发表贵族台词："Peasants.." / "This carpet is worth more than your contracts!" / "I could fire you at any time..."（`other_reagents.dm` L2071-2075）
- 检查肝脏时显示皇家代谢提示（`_liver.dm` L116，royal 压过 pretender royal）

#### 邮件礼物 (mail_goodies)

| 物品 | 权重 | 说明 |
|---|---|---|
| 哈瓦那雪茄 (cigarette/cigar/havana) | 20 | 常见 |
| 哈瓦那雪茄盒 (storage/fancy/cigarettes/cigars/havana) | 15 | 常见 |
| 香槟瓶 (bottle/champagne) | 5 | 稀有 |
| 诅咒香槟 (bottle/champagne/cursed) | 5 | 稀有 |
| "船长语录"收集玩具 | 20 | 常见 |
| sabrage 开香槟技能芯片 (skillchip/sabrage) | 5 | 稀有 — 配合皇家代谢 +20% 成功率，开瓶成功率拉满 |

#### Nova 扩展

**代码**: `modular_nova/master_files/code/modules/jobs/job_types/captain.dm`

- 覆写 `messenger = /obj/item/storage/backpack/messenger/cap`（斜挎包款式，与 base 相同值，实际无差异）
- **NOVA EDIT**: `paycheck_department` 从原版 TG 的 `ACCOUNT_SEC`（安保账目）改为 **`ACCOUNT_CMD`**（指挥账目）
- 船长岗位数量：Nova 版本保持 1（TG 原版即 1）
- akula（鲨鱼人）专属装备：`akula_outfit = /datum/outfit/akula/command`（`_job_attire.dm` L12）
- 天关模块 (modular_z121)：清空 `species_blacklist`（物种限制由 `human_authority` 继续约束）
- 天关模块 (modular_tianguan sop_book)：出生时背包额外发放**指挥SOP手册**（`/obj/item/book/manual/wiki/sop/command`，指向天关维基）

#### 一句话总结

**船长 = 全站最高权限金卡 + 核磁盘验真芯片 + 皇家代谢(开香槟+20%) + 金色伸缩警棍 + 站长宪章 + 心灵屏蔽**

人形自走核弹保险丝，站里唯一不用向任何人汇报的"皇帝"。

---

### 2. 人事部长 (Head of Personnel / HoP)

**代码**: `code/modules/jobs/job_types/head_of_personnel.dm`（Nova 覆写：`modular_nova/master_files/code/modules/jobs/job_types/head_of_personnel.dm` + `modular_nova/modules/hop_drip/code/head_of_personnel.dm`）

#### 基础信息表

| 项目 | 内容 |
|---|---|
| 岗位数量 | 1 总 / 1 出生 |
| 上级 | 船长 (SUPERVISOR_CAPTAIN) |
| 部门 | 服务部 + 指挥部 (`/datum/job_department/service` + `/datum/job_department/command`) |
| 工资等级 | PAYCHECK_COMMAND（指挥级工资） |
| 发薪部门 | ACCOUNT_SRV（服务账目） |
| 经验要求 | 180 分钟；船员经验 (EXP_TYPE_CREW)，且要求服务经验 (EXP_TYPE_SERVICE)；发放船员经验 |
| 玩家年龄 | 10 天，`req_admin_notify = 1` |
| 家族遗物 | 银奖杯 (trophy/silver_cup) |
| 其他 | `head_announce = RADIO_CHANNEL_SERVICE`；`rpg_title = "Guild Questgiver"`；`voice_of_god_power = 1.4`；仅人类；`bounty_types = CIV_JOB_RANDOM` |

#### 技能芯片

无（HoP 没有自带技能芯片）。

#### 初始物品/装备

**代码**: `/datum/outfit/job/hop`

| 部位 | 物品 |
|---|---|
| ID | `/obj/item/card/id/advanced/platinum` — 铂金高级ID卡（全站改卡权限） |
| ID权限 | `/datum/id_trim/job/head_of_personnel` |
| 制服 | HoP 制服 (under/rank/civilian/head_of_personnel) |
| 外套 | HoP 装甲马甲 (suit/armor/vest/hop) |
| 腰带 | HoP PDA (pda/heads/hop) |
| 耳机 | HoP 耳机 (headset/heads/hop) |
| 头部 | HoP 帽 (head/hats/hopcap) |
| 鞋子 | 系带皮鞋 (shoes/laceup) |
| 背包 | HoP 背包 / 挎包 / 行李袋 / 斜挎包（Nova 定制外观，见下） |

**背包内容 (backpack_contents)**:
```
/obj/item/melee/baton/telescopic/silver = 1  — 银色伸缩警棍
```

**变色龙装备**: e_gun 能量枪 + HoP 印章 (stamp/head/hop)

**彩蛋**: 若当天是 Ian 假日 (IAN_HOLIDAY)，内衬会变成 Ian 柯基图案。

#### 特质

**心灵特质**: `HEAD_OF_STAFF_MIND_TRAITS`（TRAIT_FAST_TYING 快速系领带 + TRAIT_HIGH_VALUE_RANSOM 高价值赎金 ×3，同船长）。

**肝特质**:
```
liver_traits = list(TRAIT_ROYAL_METABOLISM)
```
TRAIT_ROYAL_METABOLISM — 皇家代谢：开香槟 +20% 成功率、皇家地毯台词、肝脏检查显示皇家提示（详见船长章节）。

#### 邮件礼物 (mail_goodies)

| 物品 | 权重 | 说明 |
|---|---|---|
| 银色高级ID卡 (card/id/advanced/silver) | 10 | 常见 — 可直接发银卡 |
| 骨头堆 (stack/sheet/bone) | 5 | 稀有 |

**特殊邮件规则**（`get_mail_goodies` 覆写）：如果站上的柯基 **Ian 死了**，邮件中会加入 **strange_reagent 奇异药剂 ×20** —— "唯一值得复活的宠物"。

#### Nova 扩展

**代码**: `modular_nova/master_files/code/modules/jobs/job_types/head_of_personnel.dm` + `modular_nova/modules/hop_drip/code/head_of_personnel.dm`

- **hop_drip 模块**：四款专属背包外观（`backpack_hop` / `satchel_hop` / `duffel_hop`），描述为"Nanotrasen 二把手的专属背包"；另有 HoP 弓形耳机 alt 版（防闪光弹）
- master_files 覆写四款背包为上述定制款
- akula 专属：`akula_outfit = /datum/outfit/akula/command`
- 天关模块 (sop_book)：出生发放**服务SOP手册**（departments_list[1] = service 部门判定）
- **叛徒彩蛋**：HoP 专属叛徒目标是"取代船长"——暗杀船长并抢走金卡成为新船长（`generate_traitor_objective` → `/datum/objective/assassinate/captain_replacement`，检查完成条件为身上有 gold ID）

#### 一句话总结

**HoP = 铂金改卡权限 + 银伸缩警棍 + 皇家代谢 + Ian 守护者 + 服务/指挥双部门**

全站的"发卡机"，船长死后自动晋升代理船长，叛徒的终极目标是干掉你抢卡。

---

### 3. NT顾问 (Nanotrasen Consultant / NT Rep)

**代码**: `modular_nova/modules/nanotrasen_rep/code/nanotrasen_consultant.dm`（Nova 专属职业，`nova_stars_only = TRUE`）

#### 基础信息表

| 项目 | 内容 |
|---|---|
| 岗位数量 | 1 总 / 1 出生 |
| 上级 | Central Command（中央指挥部，`supervisors = JOB_CENTCOM`） |
| 部门 | 指挥部 + 中央指挥部 (`/datum/job_department/command` + `/datum/job_department/central_command`) |
| 工资等级 | PAYCHECK_COMMAND（指挥级工资） |
| 发薪部门 | ACCOUNT_CMD（指挥账目） |
| 经验要求 | **600 分钟（全站最高）**；船员经验 + 指挥经验 (EXP_TYPE_COMMAND)；发放船员经验 |
| 玩家年龄 | 14 天 |
| 家族遗物 | 太空法手册 (book/manual/wiki/security_space_law) |
| 其他 | `bounty_types = CIV_JOB_SEC`；`JOB_BOLD_SELECT_TEXT`；**`JOB_CANNOT_OPEN_SLOTS`**（无法通过ID控制台开放岗位，与 AI/机器人同级）；`JOB_ANTAG_PROTECTED` |

#### 技能芯片 (Skillchip)

```
skillchips = list(/obj/item/skillchip/disk_verifier)
```

与船长同款 **K33P-TH4T-D15K 核磁盘验证芯片**（TRAIT_DISK_VERIFIER）——自动识别核认证磁盘真伪。

#### 初始物品/装备

**代码**: `/datum/outfit/job/nanotrasen_consultant`

| 部位 | 物品 |
|---|---|
| ID | `/obj/item/card/id/advanced/centcom/station` — **中央指挥站内卡**（最高规格） |
| ID权限 | `/datum/id_trim/job/nanotrasen_consultant` |
| 制服 | NT顾问制服 (under/rank/nanotrasen_consultant) |
| 外套 | NT顾问装甲马甲 (suit/armor/vest/nanotrasen_consultant) |
| 腰带 | NT顾问PDA (pda/nanotrasen_consultant) — 内置指挥磁盘 + 绿金钢笔，**自动连接办公室传真机**（传真通知App） |
| 耳机 | NT顾问耳机 (headset/heads/nanotrasen_consultant) — **指挥频道 + 中央频道 + 安保频道**（keyslot2 = ccrep 中央+安保双频道加密密钥） |
| 眼镜 | 墨镜 (glasses/sunglasses) |
| 手套 | 海军战斗手套·黑 (gloves/combat/naval/nanotrasen_consultant/black) |
| 头部 | NT顾问帽 (head/nanotrasen_consultant) |
| 鞋子 | 军靴 (shoes/jackboots) |
| 饰品 | **外交勋章** (accessory/medal/gold/nanotrasen_consultant) — 不可摧毁·防火防酸 |
| 植入体 | 心灵屏蔽植入体 |
| 背包 | 基础款背包 / 挎包 / 行李袋 / 斜挎包 |

**背包内容 (backpack_contents)**:
```
/obj/item/melee/baton/telescopic = 1   — 伸缩警棍
/obj/item/choice_beacon/ntc = 1        — 一次性"枪械信标"，可呼叫 4 选 1 武器补给舱
```

**枪械信标 (choice_beacon/ntc) 可选内容**:
| 选项 | 内容 |
|---|---|
| Takbok 左轮套装 | 手枪箱 (guncase/nova/pistol/trappiste_small_case/takbok) |
| Skild 手枪套装 | 手枪箱 (…/skild) |
| 绿色NT刀鞘 | 鞘内装中央指挥军刀 (sabre/central_command) |
| 黑色NT刀鞘 | 鞘内装中央指挥军刀·黑 |

中央指挥军刀：格挡率 40%、穿甲 40%（`block_chance = 40` / `armour_penetration = 40`）。

**变色龙装备**: e_gun + 中央指挥部印章 (stamp/centcom)

**专属衣柜**（`secure_closet/nanotrasen_consultant`，需 ACCESS_CAPTAIN + ACCESS_CENT_GENERAL）：备用服装袋（含 15+ 套正装/贝雷帽/中央制服/冬季大衣）、备用弓形耳机、皮挎包、宠物项圈+宠物携带器、个人相册、中央床单、备用磁盘。

#### 特质

**心灵特质**: 无 mind_traits（不是部门主管）。**肝特质**: 无（标准代谢）。

#### 邮件礼物 (mail_goodies)

| 物品 | 权重 | 说明 |
|---|---|---|
| 哈瓦那雪茄 | 20 | 常见 |
| 哈瓦那雪茄盒 | 15 | 常见 |
| 香槟瓶 | 10 | 较常见 — 与船长口味一致 |

#### Nova 扩展

- 本职业即 Nova 原创（`nova_stars_only = TRUE`，只在天关/Nova 星图启用），无更上层覆写
- akula 专属：`akula_outfit = /datum/outfit/akula/command`
- 天关模块 (sop_book)：出生发放**中央指挥部SOP手册**（`sop/central_command`，与蓝盾同款）
- 天关模块 (modular_z121)：清空 species_blacklist
- 等离子体变体：`/datum/outfit/plasmaman/nanotrasen_consultant`（中央官员等离子服 + 船长手套"太经典不舍得换"）

#### 一句话总结

**NT顾问 = 中央指挥站内卡 + 核磁盘验真芯片 + 四选一武器信标 + 中央/安保双频道 + 外交勋章 + 全站最高经验门槛(600min)**

NT 公司的"钦差大臣"，站在船长之上的行政大佛——以及全站最贵的绑架目标。

---

### 4. 安全部长 (Head of Security / HoS)

**代码**: `code/modules/jobs/job_types/head_of_security.dm`（Nova 覆写：`modular_nova/master_files/code/modules/jobs/job_types/head_of_security.dm`）

#### 基础信息表

| 项目 | 内容 |
|---|---|
| 岗位数量 | 1 总 / 1 出生 |
| 上级 | 船长 (SUPERVISOR_CAPTAIN) |
| 部门 | 安保部 + 指挥部 (`/datum/job_department/security` + `/datum/job_department/command`) |
| 工资等级 | PAYCHECK_COMMAND（指挥级工资） |
| 发薪部门 | ACCOUNT_SEC（安保账目） |
| 经验要求 | 300 分钟；船员经验 + 安保经验 (EXP_TYPE_SECURITY)；发放船员经验 |
| 玩家年龄 | 14 天，`req_admin_notify = 1` |
| 家族遗物 | 太空法手册 (security_space_law) |
| 其他 | `head_announce = RADIO_CHANNEL_SECURITY`；`rpg_title = "Guard Leader"`；`voice_of_god_power = 1.4`；仅人类；`JOB_ANTAG_PROTECTED`；`bounty_types = CIV_JOB_SEC` |

#### 技能芯片

无（HoS 没有自带技能芯片，靠装备与经验）。

#### 初始物品/装备

**代码**: `/datum/outfit/job/hos`

| 部位 | 物品 |
|---|---|
| ID | `/obj/item/card/id/advanced/silver` — 银色高级ID卡 |
| ID权限 | `/datum/id_trim/job/head_of_security` |
| 制服 | HoS 制服 (under/rank/security/head_of_security) |
| 外套 | HoS 风衣甲 (suit/armor/hos/trenchcoat) |
| 外套槽 (suit_store) | **e_gun 能量枪** |
| 腰带 | HoS PDA (pda/heads/hos) |
| 耳机 | HoS 耳机·alt 弓形版 (headset/heads/hos/alt，防闪光弹) |
| 眼镜 | 安保HUD墨镜 (glasses/hud/security/sunglasses) |
| 手套 | 黑色安保手套 (gloves/color/black/security) |
| 头部 | HoS 贝雷帽 (head/hats/hos/beret) |
| 鞋子 | 安保军靴 (shoes/jackboots/sec) |
| 左口袋 | 手铐 (restraints/handcuffs) |
| 右口袋 | 手持闪光 (assembly/flash/handheld) |
| 植入体 | 心灵屏蔽植入体 |
| 背包 | 安保背包 / 挎包(sec) / 行李袋(sec) / 斜挎包(messenger/sec) |
| 盒子 | 安保生存盒 (box/survival/security) |

**背包内容 (backpack_contents)**（含 Nova 追加）:
```
/obj/item/evidencebag = 1                    — 证据袋（base）
/obj/item/melee/baton/security/loaded/hos = 1 — HoS 特供电击棒（base）
/obj/item/melee/baton/security/stunsword/hos/loaded = 1 — 眩晕剑（Nova pre_equip 追加）
```

**变色龙装备**: e_gun/hos 特供能量枪 + HoS 印章 (stamp/head/hos)

**MODsuit 变体**: `pre_equipped/safeguard` 护卫MOD + sechailer 防毒面具。

#### 特质

**心灵特质**: `HEAD_OF_STAFF_MIND_TRAITS`（快速系领带 + 高价值赎金 ×3）+ `desensitized_base = DESENSITIZED_THRESHOLD`（脱敏门槛 0.5）。

**肝特质** — 双肝特质，全站最强代谢组合:
```
liver_traits = list(TRAIT_LAW_ENFORCEMENT_METABOLISM, TRAIT_ROYAL_METABOLISM)
```
**TRAIT_LAW_ENFORCEMENT_METABOLISM — 执法代谢**:
- 吃任何甜甜圈 = FOOD_LIKED（`donuts.dm` L58：所有警察都爱甜甜圈）
- 喝 Beepsky Smash（酒）：无此特质会得 Beepsky 幻觉脑创伤；有此特质 → **回复体力** + 随机"附近假物品/流弹"幻觉（`alcohol_reagents.dm` L818-824）
- 喝 Quadruple Sec（酒）：**治疗肉体伤害**（brute+burn 每tick 1点，L1693-1696）
- 喝 Quintuple Sec（酒）：**强效治疗**（brute 2 / burn 1 / 体力 5，L1712-1717）
- 吃糖霜 (sprinkles)：**回血 0.5/0.5**（`food_reagents.dm` L633）
- 吸 Mindbreaker（迷幻药）：幻觉固定为"辛迪加敌人"特定幻觉（`drug_reagents.dm` L974）
- 喝 Beepsky Smash 过量：不会得安保恐惧症脑创伤（L835）

**TRAIT_ROYAL_METABOLISM — 皇家代谢**: 开香槟 +20% 成功率（详见船长章节）。

**彩蛋（天关保留）**: `after_spawn` 有 **40% 概率**（PIG_COP_PROBABILITY，`jobs.dm` L351）把身体各部位的肉块掉落换成**猪肉**——"猪警官"梗。

#### 邮件礼物

HoS **没有** mail_goodies 字段（不订阅邮件礼物）。

#### Nova 扩展

**代码**: `modular_nova/master_files/code/modules/jobs/job_types/head_of_security.dm`

- `pre_equip` 追加 **HoS 特供眩晕剑** (stunsword/hos/loaded) 进背包
- 覆写 `messenger = messenger/sec`（斜挎包款式，与 base 同值）
- akula 专属：`akula_outfit = /datum/outfit/akula/security_officer`
- 天关模块 (sop_book)：出生发放**安全SOP手册**
- 天关模块 (modular_z121)：清空 species_blacklist

#### 一句话总结

**HoS = 风衣甲+能量枪+眩晕剑 + 双肝特质(执法代谢+皇家代谢) + 安保HUD墨镜 + 心灵屏蔽 + 40%猪肉梗**

安保部的大脑与铁拳——喝酒回血、吃甜甜圈回好感、开香槟加成，全站最能打的文职。

---

### 5. 看守 (Warden)

**代码**: `code/modules/jobs/job_types/warden.dm`（Nova 覆写：`modular_nova/master_files/code/modules/jobs/job_types/warden.dm`）

#### 基础信息表

| 项目 | 内容 |
|---|---|
| 岗位数量 | 1 总 / 1 出生 |
| 上级 | 安全部长 (SUPERVISOR_HOS) |
| 部门 | 安保部 (`/datum/job_department/security`) |
| 工资等级 | **PAYCHECK_CREW（船员级工资）** — 注意：不是指挥级 |
| 发薪部门 | ACCOUNT_SEC（安保账目） |
| 经验要求 | 300 分钟；船员经验；发放船员经验 |
| 玩家年龄 | 7 天 |
| 家族遗物 | 太空法手册 |
| 其他 | `rpg_title = "Jailor"`；`JOB_BOLD_SELECT_TEXT`；`JOB_ANTAG_PROTECTED`；`bounty_types = CIV_JOB_SEC` |

#### 技能芯片

无。

#### 初始物品/装备

**代码**: `/datum/outfit/job/warden`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/warden`（无专属ID物品 → 默认标准卡） |
| 制服 | 看守制服 (under/rank/security/warden) |
| 外套 | 看守装甲马甲·alt (suit/armor/vest/warden/alt) |
| 外套槽 (suit_store) | **disabler 失能枪**（非致命） |
| 腰带 | 看守 PDA (pda/warden) |
| 耳机 | 安保耳机·alt 弓形版 (headset_sec/alt) |
| 眼镜 | 安保HUD墨镜 |
| 手套 | 黑色安保手套 |
| 头部 | 看守红帽 (head/hats/warden/red) |
| 鞋子 | 安保军靴 |
| 左口袋 | 手铐 |
| 右口袋 | 手持闪光 |
| 植入体 | 心灵屏蔽植入体 |
| 背包 | 安保背包 / 挎包(sec) / 行李袋(sec) / 斜挎包(messenger/sec) |
| 盒子 | 安保生存盒 |

**背包内容**: 无 backpack_contents 字段（空背包）。

**变色龙装备**: 无。

#### 特质

**心灵特质**: 无 mind_traits（看守不是主管），但 `desensitized_base = DESENSITIZED_THRESHOLD`（脱敏门槛）。

**肝特质** — 双肝特质:
```
liver_traits = list(TRAIT_LAW_ENFORCEMENT_METABOLISM, TRAIT_PRETENDER_ROYAL_METABOLISM)
```
- **TRAIT_LAW_ENFORCEMENT_METABOLISM — 执法代谢**: 甜甜圈喜欢、Beepsky Smash 回体力、Quadruple/Quintuple Sec 回血、糖霜回血（详见 HoS 章节）
- **TRAIT_PRETENDER_ROYAL_METABOLISM — 伪皇家代谢**: 喝皇家地毯试剂时概率触发"山寨贵族"内心戏（`other_reagents.dm` L2078：SPT_PROB(8) 消息），但**没有**开香槟 +20% 加成——"伪王 vs 真王"的区别

**彩蛋**: 同样 40% 概率猪警官（PIG_COP_PROBABILITY）。

#### 邮件礼物 (mail_goodies)

| 物品 | 权重 | 说明 |
|---|---|---|
| 香烟盒 (storage/fancy/cigarettes) | 15 | 常见 |
| 手铐箱 (storage/box/handcuffs) | 10 | 常见 |
| 催泪瓦斯箱 (storage/box/teargas) | 10 | 常见 |
| 闪光弹箱 (storage/box/flashbangs) | 10 | 常见 |
| 橡皮弹箱 (storage/box/rubbershot) | 10 | 常见 |

#### Nova 扩展

- 覆写 `messenger = messenger/sec`（斜挎包款式，与 base 同值）
- akula 专属：`akula_outfit = /datum/outfit/akula/security_officer`
- 天关模块 (sop_book)：出生发放**安全SOP手册**
- 天关模块 (modular_z121)：清空 species_blacklist

#### 一句话总结

**看守 = 失能枪+红帽+HUD墨镜 + 执法代谢+伪皇家代谢 + 防暴邮件礼包 + 船员级工资**

监狱长本尊——电人用失能枪、管人用手铐箱、装X用伪皇家代谢（但开香槟没加成）。

---

### 6. 保安官 (Security Officer)

**代码**: `code/modules/jobs/job_types/security_officer.dm`（Nova 覆写：`modular_nova/master_files/code/modules/jobs/job_types/security_officer.dm`；天关扩展：`modular_tianguan/code/security_officer.dm`）

#### 基础信息表

| 项目 | 内容 |
|---|---|
| 岗位数量 | **8 总 / 8 出生** — Nova EDIT：原版 TG 为 5，Nova 改为 8（`//NOVA EDIT: SET TO 8, WAS 5`） |
| 上级 | 安全部长 + 所分配部门的部长 ("the Head of Security, and the head of your assigned department (if applicable)") |
| 部门 | 安保部 (`/datum/job_department/security`) |
| 工资等级 | PAYCHECK_CREW（船员级工资） |
| 发薪部门 | ACCOUNT_SEC（安保账目） |
| 经验要求 | 300 分钟；船员经验；发放船员经验 |
| 玩家年龄 | 7 天 |
| 家族遗物 | 太空法手册、安保贝雷帽 (beret/sec) |
| 备用职称 | 医疗保安官 / 工程保安官 / 供应保安官 / 科研保安官（JOB_SECURITY_OFFICER_MEDICAL/ENGINEERING/SUPPLY/SCIENCE） |
| 其他 | `rpg_title = "Guard"`；`JOB_ANTAG_PROTECTED`；`bounty_types = CIV_JOB_SEC` |

**部门分配系统**（base 代码，Nova 已移除自动执行）: TG 原版按玩家偏好把保安官分到 工程/医疗/科研/供应 四个部门（换部门耳机+部门ID权限+部门出生点+臂章，算法 `get_officer_departments` 确定性分配）。**Nova 版本 REMOVE 了 `after_roundstart_spawn` 与 `after_latejoin_spawn` 中的自动分配**（`//NOVA EDIT REMOVAL`）——因为 Nova 改用部门警卫体系（见第 9 节）替代部门派驻。

#### 技能芯片

无。

#### 初始物品/装备

**代码**: `/datum/outfit/job/security`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/security_officer`（无专属ID物品 → 默认标准卡） |
| 制服 | 保安官制服 (under/rank/security/officer) |
| 外套 | 安保装甲马甲·alt (suit/armor/vest/alt/sec) |
| 腰带 | 安保 PDA (pda/security) |
| 耳机 | 安保耳机·alt 弓形版 (headset_sec/alt) |
| 手套 | 黑色安保手套 |
| 头部 | 安保头盔 (head/helmet/sec) |
| 鞋子 | 安保军靴 |
| 左口袋 | 手铐 |
| 右口袋 | 手持闪光 |
| 植入体 | 心灵屏蔽植入体 |
| 背包 | 安保背包 / 挎包(sec) / 行李袋(sec) / 斜挎包(messenger/sec) |
| 盒子 | 安保生存盒 |

**背包内容 (backpack_contents)**:
```
/obj/item/evidencebag = 1                — 证据袋
/obj/item/security_voucher/primary = 1   — 装备券·主武器（可换主武器）
/obj/item/security_voucher/utility = 1   — 装备券·工具（可换工具装备）
```

**变色龙装备**: 安保HUD墨镜 / 标准头盔 / 失能枪

**MODsuit 变体**: `pre_equipped/security` 安保MOD + sechailer 防毒面具。

#### 特质

**心灵特质**: 无 mind_traits，但 `desensitized_base = DESENSITIZED_THRESHOLD`。

**肝特质**:
```
liver_traits = list(TRAIT_LAW_ENFORCEMENT_METABOLISM)
```
执法代谢：甜甜圈喜欢、Beepsky Smash 回体力、Quadruple/Quintuple Sec 回血、糖霜回血（详见 HoS 章节）。

**彩蛋**: 40% 概率猪警官（PIG_COP_PROBABILITY）。

#### 邮件礼物 (mail_goodies)

| 物品 | 权重 | 说明 |
|---|---|---|
| 焦糖甜甜圈 (food/donut/caramel) | 10 | 常见 |
| 抹茶甜甜圈 (food/donut/matcha) | 10 | 常见 |
| 南瓜甜甜圈 (food/donut/blumpkin) | 5 | 稀有 |
| 哨子 (clothing/mask/whistle) | 5 | 稀有 |
| 回旋镖电击棒 (melee/baton/security/boomerang/loaded) | 1 | 极稀有 — 投掷回旋电击棒 |

#### Nova 扩展

- 岗位数量 **5 → 8**（Nova EDIT）
- 移除部门自动分配（Nova EDIT REMOVAL）
- 覆写 `messenger = messenger/sec`（与 base 同值）
- akula 专属：`akula_outfit = /datum/outfit/akula/security_officer`
- **天关特色（modular_tianguan/code/security_officer.dm）**：`post_equip` 时额外往背包塞一把 **e_gun/mini 迷你能量枪**（`equip_to_storage(new /obj/item/gun/energy/e_gun/mini(equipped), ITEM_SLOT_BACK)`）——天关保安官出生自带小型实弹能量枪，这是其他服没有的改动
- 天关模块 (sop_book)：出生发放**安全SOP手册**
- 天关模块 (modular_z121)：清空 species_blacklist

#### 一句话总结

**保安官 = 电击棒+手铐+闪光 + 双装备券(主武器/工具) + 执法代谢 + 天关特供迷你能量枪**

**（天关版）保安官 = 出生多一把 e_gun/mini**，巡逻底气直接拉满——甜甜圈管够，战力管饱。

---

### 7. 侦探 (Detective)

**代码**: `code/modules/jobs/job_types/detective.dm` + `code/modules/library/skill_learning/job_skillchips/detective.dm`（Nova 覆写：`modular_nova/master_files/code/modules/jobs/job_types/detective.dm`）

#### 基础信息表

| 项目 | 内容 |
|---|---|
| 岗位数量 | 1 总 / 1 出生 |
| 上级 | 安全部长 (SUPERVISOR_HOS) |
| 部门 | 安保部 (`/datum/job_department/security`) |
| 工资等级 | PAYCHECK_CREW（船员级工资） |
| 发薪部门 | ACCOUNT_SEC（安保账目） |
| 经验要求 | 300 分钟；船员经验；发放船员经验 |
| 玩家年龄 | 7 天 |
| 家族遗物 | 威士忌瓶 (bottle/whiskey) |
| 其他 | `rpg_title = "Thiefcatcher"`；`job_tone = "objection"`（异议！台词音调）；`JOB_ANTAG_PROTECTED` |

#### 技能芯片 (Skillchip)

**代码**: `code/modules/library/skill_learning/job_skillchips/detective.dm`

```
/obj/item/skillchip/job/detectives_taste
  name = "DET.ekt skillchip"
  desc = "Detective \"Encyclopedic Knowledge of Tastes\" v1.21"
  auto_traits = list(TRAIT_DETECTIVES_TASTE)
  skill_name = "Detective's Taste"
  skill_description = "Deduce the minute chemical compositions of any liquid substance \
    just by swishing it around your mouth for a bit."
```

侦探出生自带 **DET.ekt 品化学芯片**（TRAIT_DETECTIVES_TASTE）。

**效果**: 任何液体**含在嘴里漱一漱就能推断出它的化学组成**——不需要实验室分析仪。

**直观理解**: 舌头就是色谱仪，抿一口就知道杯子里是毒药还是烈酒、掺了什么料。审讯室拿杯水就能当测谎仪用。

#### 初始物品/装备

**代码**: `/datum/outfit/job/detective`

| 部位 | 物品 |
|---|---|
| ID | `/obj/item/card/id/advanced/plainclothes` — 便衣高级ID卡（不显示姓名档） |
| ID权限 | `/datum/id_trim/job/detective` |
| 制服 | 侦探制服 (under/rank/security/detective) |
| 外套 | 侦探风衣 (suit/toggle/jacket/det_trench) |
| 腰带 | 侦探 PDA (pda/detective) |
| 耳机 | 安保耳机·alt 弓形版 |
| 手套 | 黑色手套 |
| 头部 | 侦探帽 (head/fedora/det_hat) |
| 口罩位 | **点着的香烟** (mask/cigarette，post_equip 自动点燃) |
| 脖子 | 侦探领带 (neck/tie/detective) |
| 鞋子 | 棕色运动鞋 (shoes/sneakers/brown) |
| 左口袋 | 白色粉笔 (toy/crayon/white) |
| 右口袋 | 打火机 (lighter) |
| 植入体 | 心灵屏蔽植入体 |

**背包内容 (backpack_contents)**（base 版）:
```
/obj/item/detective_scanner = 1    — 侦探扫描仪（收集指纹/血迹证据）
/obj/item/melee/baton = 1          — 普通电击棒
/obj/item/storage/box/evidence = 1 — 证据箱
```
⚠️ **Nova 覆写替换了背包内容**（见 Nova 扩展）。

**变色龙装备**: 墨镜 / 侦探左轮 (gun/ballistic/revolver/c38/detective)

**未成年彩蛋**: 未成年侦探 → 糖果香烟 + 迷你侦探帽（`pre_equip`：AGE_MINOR 判定）。

#### 特质

**心灵特质**: 无 mind_traits，但 `desensitized_base = DESENSITIZED_THRESHOLD`。

**肝特质**:
```
liver_traits = list(TRAIT_LAW_ENFORCEMENT_METABOLISM)
```
执法代谢（详见 HoS 章节）。

#### 邮件礼物 (mail_goodies)

| 物品 | 权重 | 说明 |
|---|---|---|
| 香烟盒 (storage/fancy/cigarettes) | 25 | 最常见 — 老烟枪 |
| .38 快速装弹器 (ammo_box/speedloader/c38) | 20 | 常见 |
| .38 达姆弹 (c38/dumdum) | 5 | 稀有 — 扩张弹 |
| .38 热弹 (c38/hotshot) | 5 | 稀有 — 燃烧弹 |
| .38 冰弹 (c38/iceblox) | 5 | 稀有 — 冰冻弹 |
| .38 比赛弹 (c38/match) | 5 | 稀有 — 精度弹 |
| .38 追踪弹 (c38/trac) | 5 | 稀有 — 追踪弹 |
| 便衣ID卡 (card/id/advanced/plainclothes) | 5 | 稀有 |
| 完整枪套腰带 (storage/belt/holster/detective/full) | 1 | 极稀有 — 左轮+弹药全套 |

#### Nova 扩展

**代码**: `modular_nova/master_files/code/modules/jobs/job_types/detective.dm`

- **腰带**改为 **完整枪套** (storage/belt/holster/detective/full) — 左轮常驻腰间
- **左口袋**改为侦探PDA；**右口袋**改为**全息侦探徽章** (accessory/badge/holo/detective)
- **背包内容整体替换**为:
```
/obj/item/detective_scanner = 1       — 侦探扫描仪
/obj/item/choice_beacon/detective = 1 — 侦探武器信标（可选左轮/霰弹枪等）
/obj/item/pinpointer/crew = 1         — 船员定位器（追踪船员位置）
```
  → Nova 侦探不再背普通电击棒+证据箱，改为信标+定位器
- `bounty_types = CIV_JOB_SEC`
- akula 专属：`akula_outfit = /datum/outfit/akula/security_officer`
- 天关模块 (sop_book)：出生发放**安全SOP手册**

#### 一句话总结

**侦探 = 品化学芯片(舌头=色谱仪) + 侦探扫描仪 + 点烟进场 + 全弹种邮件 + Nova枪套左轮+船员定位器**

抽烟、抿一口、翻白眼，然后指着凶手说 "objection!" ——全站最帅的验毒员。

---

### 8. 律师 (Lawyer)

**代码**: `code/modules/jobs/job_types/lawyer.dm`（无 Nova 覆写文件）

#### 基础信息表

| 项目 | 内容 |
|---|---|
| 岗位数量 | **2 总 / 2 出生**（第二位律师自动换紫色西装） |
| 上级 | 人事部长 (SUPERVISOR_HOP) |
| 部门 | 服务部 (`/datum/job_department/service`) — 注意：律师属于服务部而非安保部 |
| 工资等级 | PAYCHECK_CREW（船员级工资） |
| 发薪部门 | **ACCOUNT_SRV（服务账目）** |
| 经验要求 | **无**（没有 exp_requirements 字段；仅发放船员经验） |
| 家族遗物 | 法槌 (gavelhammer)、太空法手册 |
| 其他 | `rpg_title = "Magistrate"`；`job_tone = "objection"`；**`job_flags = STATION_JOB_FLAGS`（无 JOB_ANTAG_PROTECTED — 律师可以当叛徒！）** |

#### 技能芯片

无。

#### 初始物品/装备

**代码**: `/datum/outfit/job/lawyer`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/lawyer`（无专属ID物品 → 默认标准卡） |
| 制服 | 蓝色律师西装 (under/rank/civilian/lawyer/bluesuit) — 第二位律师 → **紫色西装** (purpsuit，`pre_equip` 静态变量切换) |
| 外套 | 律师西装外套 (suit/toggle/lawyer) |
| 腰带 | 律师 PDA (pda/lawyer) |
| 耳机 | **服务+安保双频道耳机** (headset_srvsec) |
| 鞋子 | 系带皮鞋 |
| 脖子 | 红色领带·系好款 (neck/tie/red/tied) |
| 左口袋 | **红色激光笔** (laser_pointer/red) |
| 右口袋 | **律师徽章** (clothing/accessory/lawyers_badge) |
| 左手 | **律师公文包** (storage/briefcase/lawyer) |

**背包内容**: 无 backpack_contents 字段（空背包）。

**变色龙装备**: 律师印章 (stamp/law)

#### 特质

**心灵特质**: 无 mind_traits。

**肝特质**:
```
liver_traits = list(TRAIT_LAW_ENFORCEMENT_METABOLISM)
```
执法代谢（甜甜圈喜欢、Beepsky Smash 回体力、Quadruple/Quintuple Sec 回血——详见 HoS 章节）。律师虽然是服务部，但拥有一颗"执法者之肝"。

#### 邮件礼物

律师 **没有** mail_goodies 字段。

#### Nova 扩展

- 无 Nova 覆写（base 文件即为最终版）
- akula 专属：无（_job_attire.dm 未收录律师）
- 天关模块 (sop_book)：出生发放**服务SOP手册**（departments_list[1] = service 判定）
- **无 JOB_ANTAG_PROTECTED** 是重点差异：律师/法槌/太空法手册开局，但叛徒系统照常可以选你——"知法犯法"专业户

#### 一句话总结

**律师 = 双频道耳机(服务+安保) + 红色激光笔 + 律师徽章 + 公文包 + 执法代谢 + 可选叛徒**

服务部编制的法庭打工人，两位律师一蓝一紫——记住：你没有反叛徒保护，知法犯法者众。

---

### 9. 部门警卫系 (Department Guards) — 6 岗位合并

**代码**: `modular_nova/modules/goofsec/code/department_guards.dm`（科学警卫/秩序员/工程警卫/海关/保安）+ `modular_nova/modules/sec_haul/code/corrections_officer/corrections_officer.dm`（狱警）

> Nova 的设计理念（文件头注释）："与其往每个部门派驻保安官，不如给每个部门配自家的警卫！"
> 六大部门警卫共用一套框架：2 岗位、船员工资、部门发薪、部门电击棒、部门色装备、部门耳机、PDA 放左口袋+手电筒放右口袋。

#### 9.0 共同特性（六者通用）

**岗位/工资**：全部 `total_positions = 2` / `spawn_positions = 2`（**唯一例外：狱警 1 岗**）、`PAYCHECK_CREW`、`JOB_ANTAG_PROTECTED`

**家族遗物**：全部 = 太空法手册 + 对应部门安保贝雷帽

**邮件礼物（5 个部门警卫完全一致）**:
| 物品 | 权重 |
|---|---|
| 焦糖甜甜圈 | 10 |
| 抹茶甜甜圈 | 10 |
| 南瓜甜甜圈 | 5 |
| 哨子 | 5 |
| 回旋镖电击棒 | 1 |

**部门电击棒 (departmental stun baton)** — 核心特色装备:
```
/obj/item/melee/baton/security/loaded/departmental
  var/non_departmental_uses_left = 4
```
- **区域锁定**：只在所属部门区域内正常使用；**部门外只剩 4 次使用机会**，用完自动关机
- 回到自己部门区域再次开启可**重置回 4 次**
- 可被 **emag 解锁**：解除区域限制（`emag_act` → `emagged = TRUE`）
- 每个部门有专属电击棒型号与名称（医疗/工程/科研/货舱/服务/监狱），各自锁定不同 area 列表，逃生舱 (shuttle/escape) 全部豁免
- 额外电击棒可通过对应部门补给箱购买（各 `CARGO_CRATE_VALUE * 2`）

**部门腰带**（`/obj/item/storage/belt/security/department_guard/<部门>`）内藏 5 件套（颜色随部门）:
```
/obj/item/restraints/handcuffs/cable/<部门色>  — 电缆手铐
/obj/item/assembly/flash/handheld              — 手持闪光
/obj/item/ammo_box/magazine/pepperball         — 胡椒球弹匣
/obj/item/gun/ballistic/automatic/pistol/pepperball — 胡椒球手枪（非致命）
/obj/item/melee/baton/security/loaded/departmental/<部门> — 部门电击棒
```

**ID 权限模式**：全部有专属 `id_trim`（部门色卡面 + 自定义 sechud 图标），权限 = 本部门全权限 + **ACCESS_BRIG_ENTRANCE + ACCESS_SECURITY + ACCESS_WEAPONS**（武器库权限！）+ 部门相关项。

**无特质**：除狱警外均无 mind_traits / liver_traits / desensitized_base / skillchips。

---

#### 9.1 科学警卫 (Science Guard)

**代码**: `modular_nova/modules/goofsec/code/department_guards.dm` L515-609

| 项目 | 内容 |
|---|---|
| 岗位数量 | 2 / 2 |
| 上级 | 科研主任 (SUPERVISOR_RD) |
| 部门 | 科研部 (ACCOUNT_SCI，`bounty_types = CIV_JOB_SCI`) |
| 工资 | PAYCHECK_CREW |
| 家族遗物 | 太空法手册 + 科研贝雷帽 (beret/sec/science) |

**装备**:
| 部位 | 物品 |
|---|---|
| 制服 | 科学警卫制服 (blueshirt/nova — 蓝色衬衫，TG 蓝衫同款图标) |
| 外套 | 蓝衫装甲马甲 (suit/armor/vest/blueshirt/nova) |
| 头盔 | 蓝衫头盔基础款 (helmet/blueshirt/nova — 科学警卫专用无改款) |
| 腰带 | 科学警卫腰带（粉色电缆手铐 + 胡椒球枪 + 科研电击棒） |
| 耳机 | 科研耳机 (headset_sci) |
| 鞋子 | 军靴 (jackboots) |
| 左口袋 | 科研 PDA（pda_slot = 左口袋） |
| 右口袋 | 手电筒 |
| 背包 | 科研背包 / 挎包(科研) / 行李袋(科研) / 斜挎包(科研) |

**ID权限**（`trim_calhoun`，科学粉）：科研部全权限（研究/机器人/基因/军械/异种生物/技术存储/辅基地）+ 武器库 + 安保 + 监狱入口。

**部门电击棒**：科研版 — 有效区域 = 科研区 + 科研维护 + 逃生舱。

---

#### 9.2 秩序员 (Orderly) — 医疗警卫

**代码**: `modular_nova/modules/goofsec/code/department_guards.dm` L614-704

| 项目 | 内容 |
|---|---|
| 岗位数量 | 2 / 2 |
| 上级 | 首席医疗官 (SUPERVISOR_CMO) |
| 部门 | 医疗部 (ACCOUNT_MED，`bounty_types = CIV_JOB_MED`) |
| 工资 | PAYCHECK_CREW |
| 家族遗物 | 太空法手册 + 医疗贝雷帽 (beret/sec/medical) |

**装备**:
| 部位 | 物品 |
|---|---|
| 制服 | 秩序员制服 (orderly uniform — 白色手术服+灰裤，"穿这身的人可能把希波克拉底誓言当建议") |
| 外套 | 秩序员装甲大衣 (armored orderly coat — 深急救蓝) |
| 头盔 | 无蓝线警卫头盔 (helmet/blueshirt/nova/guard) |
| 腰带 | 医疗警卫腰带（蓝色电缆手铐 + 胡椒球枪 + 医疗电击棒） |
| 耳机 | 医疗耳机 (headset_med) |
| 鞋子 | 白色运动鞋 (sneakers/white) |
| 左口袋 | 医疗 PDA |
| 右口袋 | 手电筒 |
| 背包 | 医疗背包 / 挎包(med) / 行李袋(med) / 斜挎包(med) + **医疗生存盒** (box/survival/medical) |

**ID权限**（`trim_orderly`，医疗蓝）：医疗部全权限（医疗/药房/手术/太平间/病毒/管道/机械医疗）+ 武器库 + 安保 + 监狱入口。

**部门电击棒**：医疗版 — 有效区域 = 医疗区 + 医疗维护 + 逃生舱。

---

#### 9.3 工程警卫 (Engineering Guard)

**代码**: `modular_nova/modules/goofsec/code/department_guards.dm` L709-804

| 项目 | 内容 |
|---|---|
| 岗位数量 | 2 / 2 |
| 上级 | 总工程师 (SUPERVISOR_CE) |
| 部门 | 工程部 (ACCOUNT_ENG，`bounty_types = CIV_JOB_ENG`) |
| 工资 | PAYCHECK_CREW |
| 家族遗物 | 太空法手册 + 工程贝雷帽 (beret/sec/engineering) |

**装备**:
| 部位 | 物品 |
|---|---|
| 制服 | 工程警卫制服 (engineering guard uniform — 高可见度衬垫工装) |
| 外套 | 工程警卫装甲大衣 (armored engineering guard coat — 警示条磨到快没用的那件) |
| 头盔 | 无蓝线警卫头盔 |
| 腰带 | 工程警卫腰带（黄色电缆手铐 + 胡椒球枪 + 工程电击棒） |
| 耳机 | 工程耳机 (headset_eng) |
| 鞋子 | 工靴 (workboots) |
| 左口袋 | 工程 PDA |
| 右口袋 | 手电筒 |
| 背包 | 工业背包 / 挎包(eng) / 行李袋(engineering) / 斜挎包(eng) + **工程师生存盒** (box/survival/engineer) |

**ID权限**（`trim_engiguard`，工程橙）：工程部全权限（工程/大气/施工/外部气闸/工程装备/技术存储/TCOMMS/机械工程/辅基地/维护隧道）+ 武器库 + 安保 + 监狱入口。

**部门电击棒**：工程版 — 有效区域 = 工程区 + 引擎维护 + 逃生舱。

---

#### 9.4 海关 (Customs Agent) — 货舱警卫

**代码**: `modular_nova/modules/goofsec/code/department_guards.dm` L809-896

| 项目 | 内容 |
|---|---|
| 岗位数量 | 2 / 2 |
| 上级 | 军需官 (SUPERVISOR_QM) |
| 部门 | 货舱部 (ACCOUNT_CAR，`bounty_types = CIV_JOB_RANDOM`) |
| 工资 | PAYCHECK_CREW |
| 家族遗物 | 太空法手册 + 货舱贝雷帽 (beret/sec/cargo) |

**装备**:
| 部位 | 物品 |
|---|---|
| 制服 | 海关制服 (customs agent uniform — 货棕色短袖衬衫+炭色短裤，"只为FTU最强壮的手") |
| 外套 | 海关装甲大衣 (armored customs agent coat — 精致编织图案) |
| 头盔 | 无蓝线警卫头盔 |
| **眼镜** | **持枪证HUD** (glasses/hud/gun_permit) — 唯一有专属眼镜的部门警卫！ |
| 腰带 | 货舱警卫腰带（橙色电缆手铐 + 胡椒球枪 + 货舱电击棒） |
| 耳机 | 货舱耳机 (headset_cargo) |
| 鞋子 | 黑色运动鞋 (sneakers/black) |
| 左口袋 | 货舱 PDA |
| 右口袋 | 手电筒 |
| 背包 | 基础款背包 / 挎包 / 行李袋 / 斜挎包（无部门花色） |

**ID权限**（`trim_customs`，货棕）：货舱部全权限（货舱/矿业/矿业站/航运/机械采矿/矿物储藏室/维护隧道）+ 武器库 + 安保 + 监狱入口。

**部门电击棒**：货舱版 — 有效区域 = 货舱区 + 货舱维护 + 逃生舱。

**职责梗**（description）：检查进出包裹、保护货舱、"把试图往 Spinward 星盟偷运可卡因的人揍出屎"。

---

#### 9.5 保安/门卫 (Bouncer / Service Guard) — 服务部警卫

**代码**: `modular_nova/modules/goofsec/code/department_guards.dm` L901-990

| 项目 | 内容 |
|---|---|
| 岗位数量 | 2 / 2 |
| 上级 | 人事部长 (SUPERVISOR_HOP) |
| 部门 | 服务部 (ACCOUNT_SRV，`bounty_types = CIV_JOB_DRINK`) |
| 工资 | PAYCHECK_CREW |
| 家族遗物 | 太空法手册 + 服务贝雷帽 (beret/sec/service) |
| 备注 | ID卡上的职位名写作 "Service Guard"（服务警卫） |

**装备**:
| 部位 | 物品 |
|---|---|
| 制服 | 保安制服 (bouncer uniform — 短袖+牛仔裤，"让醉鬼听话的那股酷劲") |
| 外套 | **无徽章版**蓝衫装甲马甲 (suit/armor/vest/blueshirt/nova/guard) |
| 头盔 | 无蓝线警卫头盔 |
| 眼镜 | 墨镜 (sunglasses) |
| 腰带 | 服务警卫腰带（绿色电缆手铐 + 胡椒球枪 + 服务电击棒） |
| 耳机 | 服务耳机 (headset_srv) |
| 鞋子 | 黑色运动鞋 |
| 左口袋 | 酒吧 PDA (pda/bar) |
| 右口袋 | 手电筒 |
| 背包 | 基础款背包 / 挎包 / 行李袋 / 斜挎包 |

**ID权限**（`trim_bouncer`，服务青柠色）：服务部全权限（服务/酒吧/厨房/水培/剧院/清洁/停尸房）+ 武器库 + 安保 + 监狱入口。

**部门电击棒**：服务版 — 有效区域 = 服务区 + 主大厅前部 + 公共休息室 + 小教堂维护 + 船员宿舍维护 + 逃生舱（六个里范围最广）。

**职责梗**（description）：防止有人翻厨房柜台、阻止礼拜堂涂鸦、查酒吧顾客ID、阻止可怕的"食物大战"。

---

#### 9.6 狱警 (Corrections Officer) — 监狱警卫

**代码**: `modular_nova/modules/sec_haul/code/corrections_officer/corrections_officer.dm` + `corrections_officer_equipment.dm`

**部门警卫中的特殊个体**：唯一 1 岗位、唯一有肝特质、唯一有经验要求、唯一有专属防暴衣柜、隶属安保部。

| 项目 | 内容 |
|---|---|
| 岗位数量 | **1 总 / 1 出生**（其他警卫 2 岗） |
| 上级 | 安全部长 (SUPERVISOR_HOS) |
| 部门 | 安保部 (ACCOUNT_SEC，`bounty_types = CIV_JOB_SEC`) |
| 工资等级 | PAYCHECK_CREW |
| 经验要求 | **150 分钟**（其他警卫无经验要求）；船员经验 |
| 玩家年龄 | 7 天 |
| 家族遗物 | 太空法手册、警帽 (security_cap)、哨子 (mask/whistle) |
| 其他 | `rpg_title = "Bailiff"`；`auto_deadmin_role_flags = DEADMIN_POSITION_SECURITY`；`JOB_ANTAG_PROTECTED` |

**肝特质**:
```
liver_traits = list(TRAIT_LAW_ENFORCEMENT_METABOLISM)
```
执法代谢（甜甜圈喜欢、Beepsky Smash 回体力、Quadruple/Quintuple Sec 回血——详见 HoS 章节）。

**邮件礼物（与部门警卫同款 + 监狱特色）**:
| 物品 | 权重 |
|---|---|
| 焦糖甜甜圈 | 10 |
| 抹茶甜甜圈 | 10 |
| 南瓜甜甜圈 | 5 |
| 哨子 | 5 |
| **监狱违禁品随机箱** (/obj/effect/spawner/random/contraband/prison) | 5 — "给狱警拿来压犯人，或者藏起来" |
| 回旋镖电击棒 | 1 |

**装备** (`/datum/outfit/job/corrections_officer`):
| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/corrections_officer`（无专属ID物品 → 默认标准卡） |
| 制服 | 狱警毛衣制服 (corrections_officer/sweater — 黑色作战毛衣罩白衬衫，"适合叫醒服务")，内衬防刺 (melee 10) |
| 外套 | 狱警西装夹克 (suit/toggle/jacket/nova/corrections_officer — 熨烫整齐、防刺 melee 10) |
| 腰带 | 安保 PDA (pda/security) |
| 耳机 | 安保耳机 (headset_sec — 注意：非 alt 弓形版) |
| 眼镜 | 墨镜 |
| 头部 | 警帽 (head/security_garrison) |
| 鞋子 | 系带皮鞋 |
| 植入体 | 心灵屏蔽植入体 |
| 背包 | 安保背包 / 挎包(sec) / 行李袋(sec) / 斜挎包(sec) + 安保生存盒 |

**背包内容 (backpack_contents)** — 部门警卫中最豪华:
```
/obj/item/melee/baton/security/loaded/departmental/prison = 1 — 监狱电击棒（区域锁定）
/obj/item/restraints/handcuffs = 2                            — 两副手铐
/obj/item/clothing/mask/whistle = 1                           — 哨子
/obj/item/gun/energy/disabler = 1                             — 失能枪
```

**部门电击棒**：监狱版 — 有效区域 = 监狱 + 审讯/处理区 + 逃生舱。

**专属防暴衣柜**（`secure_closet/corrections_officer`，riot 外观）：防暴甲、防暴头盔、防暴盾、闪光弹、手持闪光、手铐、安保军靴、备用制服。

---

#### 部门警卫·共同 Nova/天关扩展

- 全部部门警卫均有 akula 专属：`akula_outfit = /datum/outfit/akula/security_officer`（`_job_attire.dm`）
- 天关模块 (sop_book)：按部门发放对应 SOP 手册（科研/医疗/工程/货舱/服务/安全）
- 天关模块 (modular_z121)：狱警清空 species_blacklist
- 等离子体变体：各警卫有专属 plasmaman 装备（科学/医疗/工程/货舱/party_bouncer/security）
- 部门制服支持大量换装：turtleneck 高领 / skirt 裙装 / plainskirt / dress 连衣裙 / shorts 短裤（每部门 5 种）+ 部门贝雷帽 / 警帽 / 冬季大衣 / 危险警示马甲 / 肩甲披风 / 手套，均可从衣柜袋 (garment bag) 获取
- 每部门另有补给箱可购额外部门电击棒（`CARGO_CRATE_VALUE * 2`）

#### 一句话总结（部门警卫系）

**部门警卫 = 区域锁定电击棒(部门外仅4次) + 胡椒球枪5件套腰带 + 部门耳机+部门色装备 + 2岗位船员薪**

Nova 用"一个部门一个自家警卫"取代了保安官派驻制——科学警卫管书呆子、秩序员按病人、工程警卫盯SM、海关查可卡因、保安看酒吧、狱警守监狱（唯一带失能枪和防暴柜的）。

---

## 章节小结

| 职业 | 岗位 | 工资 | 发薪 | 经验 | 技能芯片 | 肝特质 |
|---|---|---|---|---|---|---|
| 船长 Captain | 1 | 指挥 | 指挥账目 | 180min | disk_verifier 核磁盘验证 | 皇家代谢 |
| 人事部长 HoP | 1 | 指挥 | 服务账目 | 180min | — | 皇家代谢 |
| NT顾问 NT Rep | 1 | 指挥 | 指挥账目 | **600min** | disk_verifier | — |
| 安全部长 HoS | 1 | 指挥 | 安保账目 | 300min | — | 执法+皇家 |
| 看守 Warden | 1 | 船员 | 安保账目 | 300min | — | 执法+伪皇家 |
| 保安官 Sec Officer | **8** | 船员 | 安保账目 | 300min | — | 执法 |
| 侦探 Detective | 1 | 船员 | 安保账目 | 300min | DET.ekt 品化学 | 执法 |
| 律师 Lawyer | 2 | 船员 | 服务账目 | 无 | — | 执法 |
| 部门警卫 ×5 | 2 | 船员 | 各部门 | 无 | — | — |
| 狱警 Corrections | 1 | 船员 | 安保账目 | 150min | — | 执法 |

**天关特色改动清单**：
1. 保安官出生自带 **e_gun/mini 迷你能量枪**（modular_tianguan，其他服没有）
2. 所有船员出生发放**部门SOP手册**（modular_tianguan sop_book，NT顾问/蓝盾发中央指挥部版）
3. 指挥官/保安官/狱警/NT顾问等清空物种限制（modular_z121，仅人类限制仍由 human_authority 约束）
4. 船长发薪部门改为指挥账目（Nova EDIT）
5. 保安官岗位 5 → 8（Nova EDIT）


---

## 第二篇 · 工程部与医疗部



> 数据源（NovaSector 分支）：`code/modules/jobs/job_types/{chief_engineer, station_engineer, atmospheric_technician, chief_medical_officer, medical_doctor, chemist, geneticist, coroner, paramedic}.dm` + `modular_nova/master_files/code/modules/jobs/job_types/virologist.dm`（病毒学家为 Nova 独占）+ 各职业 Nova 覆写。
> 技能芯片：`code/modules/library/skill_learning/job_skillchips/station_engineer.dm`（工程芯片）+ `code/modules/library/skill_learning/generic_skillchips/misc.dm`（内脏解读芯片）。
> 说明：心理医生已单独成篇（《TianGuan13-职业心理医生专精.md》），本部分不重复。

---


---

### 一、总工程师 Chief Engineer（CE）

**代码**: `code/modules/jobs/job_types/chief_engineer.dm` + `modular_nova/.../job_types/chief_engineer.dm`（仅 messenger 覆写）

#### 1.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 1总 / 1出生 |
| 上级 | 船长 (SUPERVISOR_CAPTAIN) |
| 部门 | 工程部 + 指挥 (engineering + command) |
| 工资等级 | PAYCHECK_COMMAND（指挥级工资） |
| 发薪部门 | 工程部账目 (ACCOUNT_ENG) |
| 经验类型 | 船员经验 (EXP_TYPE_CREW)；**需 180 分钟**，其中**工程部经验** (EXP_TYPE_ENGINEERING) 必填 |
| 账号年龄 | 最少 7 天 (minimal_player_age = 7) |
| 自动Deadmin | 是 (DEADMIN_POSITION_HEAD)，需管理员通知 (req_admin_notify) |
| 公告频道 | 工程频道 (head_announce = RADIO_CHANNEL_ENGINEERING) |
| 悬赏类型 | CIV_JOB_ENG（工程悬赏） |
| 家族遗物 | 白色硬帽 / 螺丝刀 / 扳手 / 焊枪 / 撬棍 / 剪线钳 |
| 角色限定 | **人类限定** (human_authority = JOB_AUTHORITY_HUMANS_ONLY) |
| 神谕权威 | voice_of_god_power = 1.4（指挥层权威，语音命令更强） |
| RPG称号 | Head Crystallomancer（首席晶体法师） |

**特殊逻辑**：
- `after_spawn`：出生时自动记住**站内电信消息服务器密钥**（message_server_key 记忆）——维护电信/加密通讯的底气。
- `get_captaincy_announcement`：船长缺位时晋升代理船长，播报 "Due to staffing shortages, newly promoted Acting Captain [name] on deck!"

#### 1.2 技能芯片 (Skillchip) — 工程芯片

**代码**: `code/modules/library/skill_learning/job_skillchips/station_engineer.dm`（CE 与驻站工程师共用）

```dm
/obj/item/skillchip/job/engineer
  name = "Engineering C1-RCU-1T skillchip"
  desc = "Endorsed by Poly."        // 鹦鹉波利背书
  auto_traits = list(TRAIT_KNOW_ENGI_WIRES)
  skill_name = "Engineering Circuitry"
  activate_message = "You suddenly comprehend the secrets behind airlock and APC circuitry."
```

总工程师自带技能芯片植入（slot = 技能芯片槽，占 2 槽位），出生即激活。

**TRAIT_KNOW_ENGI_WIRES — 工程线路直觉**（逐个效果）：
| 生效对象 | 代码位置 | 效果 |
|---|---|---|
| 气闸 (airlock) | `wires/airlock.dm` L227-229 | `can_reveal_wires()` 直接返回 TRUE —— 打开面板一眼看清线路 |
| APC | `wires/apc.dm` L89-91 | 同上，APC 线路布局直接可见 |
| 空气警报器 (air alarm) | `wires/airalarm.dm` L77-79 | 同上，警报器线路直接可见 |

**直观理解**：普通人拆开气闸/APC 面板看到的是乱麻电线，工程师芯片直接把每根线的功能（门锁/电源/AI控制…）标在眼前，剪错线的概率大降。**注意：芯片不包含"剪线技巧"，只包含"看懂线路"**。

#### 1.3 装备详情

**代码**: `/datum/outfit/job/ce`

| 部位 | 物品 |
|---|---|
| ID | `/obj/item/card/id/advanced/silver` — **银色**高级ID卡（头职银卡） |
| ID权限 | `/datum/id_trim/job/chief_engineer` |
| 制服 | 总工程师制服 |
| 腰带 | 总工程师工具腰带（`belt/utility/chief/full`，满配工具） |
| 耳机 | 总工程师耳机（`heads/ce`，工程+指挥频道） |
| 手套 | 黑手套 |
| 头 | 白色焊接硬帽（`hardhat/welding/white/up`，收起状态） |
| 鞋 | 工作靴 |
| 左口袋 | 总工程师PDA（`pda/heads/ce`） |
| 背包 | 工业背包 / 工程挎包(satchel) / 工程行李袋 / **斜挎包(messenger/eng)**（Nova） |
| 生存盒 | 扩容生存盒（`box/survival/engineer`，内含**扩容版**应急氧气罐） |
| 变装道具 | 总工程师印章 (`stamp/head/ce`) |
| 技能芯片 | 工程芯片（见 1.2） |

**MOD 变体** (`/datum/outfit/job/ce/mod`)：高级MOD服 (`mod/control/pre_equipped/advanced`) + 麦松护目镜 + 黄绝缘手套 + 高级磁力靴 + 氧气罐。

#### 1.4 背包内容 (backpack_contents)

```
/obj/item/melee/baton/telescopic/silver = 1   — 银色望远镜伸缩警棍（头职制式）
/obj/item/construction/rcd/ce = 1             — 专业RCD（"professional RCD"）
```

**RCD/ce 专属强化**（`RCD.dm` L528-533）：比普通RCD多了两项升级 —— `RCD_UPGRADE_ANTI_INTERRUPT`（防打断：施工不因受击中断）+ `RCD_UPGRADE_NO_FREQUENT_USE_COOLDOWN`（无频繁使用冷却）。总工程师的施工效率是全场天花板。

#### 1.5 特质（mind_traits + liver_traits）

```dm
mind_traits  = list(HEAD_OF_STAFF_MIND_TRAITS)
liver_traits = list(TRAIT_ENGINEER_METABOLISM, TRAIT_ROYAL_METABOLISM)
```

**HEAD_OF_STAFF_MIND_TRAITS**（所有头职共享，`jobs.dm` L347）：
- `TRAIT_FAST_TYING` — **系领带加速**：打领带耗时 ×0.5（`_neck.dm` L97-98，"Heads of staff are experts at tying their ties." 指挥层系领带专家）。
- `TRAIT_HIGH_VALUE_RANSOM` — **高价值赎金**：被海盗绑架卖给中央时赎金 **3000** 信用点（普通船员 1000）（`pirate_shuttle_equipment.dm` L451-452）。

**TRAIT_ENGINEER_METABOLISM — 工程师代谢**（肝特质）：
- **螺丝刀鸡尾酒**（Screwdriver Cocktail）饮用期间：获得 `TRAIT_HALT_RADIATION_EFFECTS`（辐射效果抑制）+ 每秒恢复毒素伤害（`alcohol_reagents.dm` L682-693）。工程师肝配螺丝刀酒 = 现场抗辐射。
- Entrails Reader 检查肝时可见此特质（能看出"这是工程师的肝"）。

**TRAIT_ROYAL_METABOLISM — 皇家代谢**：
- 头职专属"皇家肝"标记（船长/CE/CMO/HoS/HoP 同款）。在 Entrails Reader 检查中显示为皇家肝脏（"royal trumps pretender royal"，`_liver.dm` L116-119）。代码中无独立机械效果——本质是**身份标记**，与"皇族酒"等互动属于设定彩蛋。

#### 1.6 邮件礼物 (mail_goodies)

| 物品 | 权重 | 说明 |
|---|---|---|
| 饼干 (cracker) | 25 | 最常见的——"for poly"，给鹦鹉波利吃的 |
| 钻石板 (diamond) | 15 | 矿物 |
| 铀板×5 | 15 | 矿物 |
| 等离子板×5 | 15 | 矿物 |
| 金板 (gold) | 15 | 矿物 |
| 高级工具随机刷 | 3 | 极稀有 — 随机高级工程工具 |

#### 1.7 Nova 扩展

- **messenger 斜挎包**：`messenger/eng`（Nova 覆写，唯一差异）。
- **替代称号**（alternative_job_titles）：Engineering Foreman / Engineering Supervisor / Head of Engineering。
- **Akula 鲨人制服**：`akula_outfit = /datum/outfit/akula/station_engineer`（`_job_attire.dm`）。

#### 1.8 一句话总结

**总工程师 = 指挥级工资 + 工程经验180 + 专业RCD(防打断无冷却) + 银色伸缩警棍 + 工程线路直觉芯片 + 工程师/皇家双肝 + 人类限定**

工程部的国王：出生自带全站最快的施工设备和读懂一切线路的大脑。

---

### 二、驻站工程师 Station Engineer（SE）

**代码**: `code/modules/jobs/job_types/station_engineer.dm` + Nova 覆写（仅 messenger）

#### 2.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 5总 / 5出生 |
| 上级 | 总工程师 (SUPERVISOR_CE) |
| 部门 | 工程部 |
| 工资等级 | PAYCHECK_CREW（船员工资） |
| 发薪部门 | 工程部账目 (ACCOUNT_ENG) |
| 经验类型 | 船员经验；需 **60 分钟** |
| 悬赏类型 | CIV_JOB_ENG |
| 家族遗物 | 硬帽 / 螺丝刀 / 扳手 / 焊枪 / 撬棍 / 剪线钳 |
| RPG称号 | Crystallomancer（晶体法师） |

#### 2.2 技能芯片 — 工程芯片（同 CE）

**代码**: `job_skillchips/station_engineer.dm`

与总工程师完全相同：`TRAIT_KNOW_ENGI_WIRES` — 气闸 / APC / 空气警报器线路一眼可见（详见 1.2）。**驻站工程师是普通船员中唯一开局自带技能芯片的职业之一**。

#### 2.3 装备详情

**代码**: `/datum/outfit/job/engineer`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/station_engineer` |
| 制服 | 工程师制服 |
| 腰带 | 满配工程工具腰带（`belt/utility/full/engi`） |
| 耳机 | 工程频道耳机 (`headset_eng`) |
| 头 | 焊接硬帽（收起状态） |
| 鞋 | 工作靴 |
| 左口袋 | 工程PDA (`pda/engineering`) |
| 右口袋 | **T射线扫描仪** (`t_scanner`) — 看穿墙内管线/布线 |
| 背包 | 工业背包 / 工程挎包 / 工程行李袋 / 斜挎包(messenger/eng)（Nova） |
| 生存盒 | 扩容生存盒（扩容氧气罐） |
| 技能芯片 | 工程芯片 |

**变体**：
- `engineer/gloved` — **黄绝缘手套**版本（触电防护，拉网/修高压必备）。
- `engineer/mod` — 工程MOD服版本（`mod/control/pre_equipped/engineering`）。

#### 2.4 背包内容

```
/obj/item/construction/rcd/loaded = 1   — 满电RCD（快速建造装置，带弹药）
```

注意：普通工程师的 RCD 是"loaded"基础款，无 CE 专业款的防打断/无冷却升级。

#### 2.5 特质

```dm
liver_traits = list(TRAIT_ENGINEER_METABOLISM)
```

`TRAIT_ENGINEER_METABOLISM` — 见 1.5：螺丝刀鸡尾酒 = 抗辐射 + 毒素恢复；Entrails 检查可见"工程师的肝"。无 mind_traits。

#### 2.6 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 混合灯泡箱 | 20 | 最常见 — 换灯工单的核心耗材 |
| 灯泡更换器 (lightreplacer) | 10 | 一键换灯神器 |
| 太空刀 (spess_knife) | 10 | 太空小刀（撬板/防身） |
| 工程全息标志生成器 | 8 | 封锁危险区 |
| 螺栓扳手 (wrench/bolter) | 8 | 拧螺栓专用 |
| 升级版红色硬帽 | 1 | 极稀有 — 带灯+焊接面罩的升级硬帽 |

#### 2.7 Nova 扩展

- **messenger/eng** 斜挎包（Nova 覆写）。
- **替代称号**：Electrician / Damage Control Technician / Engine Technician / EVA Technician / Mechanic / Architect / Structural Engineer / Electrical Engineer / Apprentice Engineer 等。
- **Akula 制服**：`akula/station_engineer`。

#### 2.8 一句话总结

**驻站工程师 = 5人编制 + 满配工具腰带 + RCD + T射线扫描仪 + 工程线路直觉芯片 + 工程师肝**

开局自带"看懂线路"大脑的工地主力，SM 点火、太阳能接线、修船壳全靠这五个人。

---

### 三、大气技师 Atmospheric Technician

**代码**: `code/modules/jobs/job_types/atmospheric_technician.dm` + Nova 覆写（仅 messenger）

#### 3.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 3总 / **2出生**（1 个空位留给中盘加入） |
| 上级 | 总工程师 (SUPERVISOR_CE) |
| 部门 | 工程部 |
| 工资等级 | PAYCHECK_CREW |
| 发薪部门 | 工程部账目 (ACCOUNT_ENG) |
| 经验类型 | 船员经验；需 **60 分钟** |
| 悬赏类型 | CIV_JOB_ATMOS（大气悬赏） |
| 家族遗物 | 打火机 / 灰阶打火机 / 火柴盒（点火三件套——大气技师的人设就是玩火） |
| RPG称号 | Aeromancer（大气法师） |

#### 3.2 技能芯片

**无技能芯片**。大气技师不带任何 skillchip（工程芯片仅 CE / SE 拥有）——靠的是装备和手法。

#### 3.3 装备详情

**代码**: `/datum/outfit/job/atmos`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/atmospheric_technician` |
| 制服 | 大气技师制服 |
| 外衣 | 大气工作服 (`suit/atmos_overalls`) — 防火耐温工作服 |
| 腰带 | 大气技师工具腰带 (`belt/utility/atmostech`) |
| 耳机 | 工程频道耳机 |
| 左口袋 | 大气PDA (`pda/atmos`) |
| 右口袋 | **气体分析仪** (`analyzer`) — 读气体成分/温度/压力 |
| 背包 | 工业背包 / 工程挎包 / 工程行李袋 / 斜挎包(messenger/eng)（Nova） |
| 生存盒 | 扩容生存盒 |

**MOD 变体** (`atmos/mod`)：大气MOD服 (`mod/control/pre_equipped/atmospheric`) + **大气防毒面具** (`mask/gas/atmos`) + 氧气罐（替换掉大气工作服）。

#### 3.4 背包内容

**无**（backpack_contents 为空——装备全在腰带上）。

#### 3.5 特质

```dm
liver_traits = list(TRAIT_ENGINEER_METABOLISM)
```

与工程师同款肝特质（螺丝刀鸡尾酒抗辐射，见 1.5）。

#### 3.6 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| RPD 升级：卸管扳手 (unwrench) | 30 | 最常见 — RPD（快速管道铺设器）可拆管升级 |
| 晶体泡沫手雷 | 10 | 灭火泡沫晶体手雷 |
| 原硝酸晶体手雷 | 10 | 产氧晶体手雷 |
| 氦气晶体手雷 | 10 | 产氦晶体手雷（治疗性气体） |
| 一氧化二氮晶体手雷 | 5 | 笑气晶体手雷 — 少，因为这是麻醉气体 |

#### 3.7 Nova 扩展

- **messenger/eng** 斜挎包。
- **替代称号**：Atmospheric Trainee / Emergency Fire Technician / Fusion Reactor Operator / Gas Synthesis Technician / Nuclear Reactor Operator / Firefighter / Life Support Technician。
- **Akula 制服**：`akula/station_engineer`。

#### 3.8 一句话总结

**大气技师 = 3人编制(2出生) + 大气工作服 + 气体分析仪 + 大气MOD服变体 + 工程师肝 + 无技能芯片**

全站的呼吸质量、灭火和管道工程都在这双手上——出生不带芯片，但 RPD 玩得比谁都溜。

---


---

### 四、首席医疗官 Chief Medical Officer（CMO）

**代码**: `code/modules/jobs/job_types/chief_medical_officer.dm` + Nova 覆写（仅 messenger）

#### 4.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 1总 / 1出生 |
| 上级 | 船长 (SUPERVISOR_CAPTAIN) |
| 部门 | 医疗部 + 指挥 (medical + command) |
| 工资等级 | PAYCHECK_COMMAND（指挥级工资） |
| 发薪部门 | 医疗部账目 (ACCOUNT_MED) |
| 经验类型 | 船员经验；**需 180 分钟**，其中**医疗部经验** (EXP_TYPE_MEDICAL) 必填 |
| 账号年龄 | 最少 7 天 |
| 自动Deadmin | 是 (DEADMIN_POSITION_HEAD)，需管理员通知 |
| 公告频道 | 医疗频道 (RADIO_CHANNEL_MEDICAL) |
| 悬赏类型 | CIV_JOB_MED_VIRO（医疗+病毒悬赏） |
| 家族遗物 | 古代传家医疗箱 / 手术刀 / 止血钳 / 环锯 / 牵开器 / 电灼器 / 希波克拉底半身像 |
| 角色限定 | **人类限定** (JOB_AUTHORITY_HUMANS_ONLY) |
| 神谕权威 | voice_of_god_power = 1.4 |
| RPG称号 | High Cleric（高阶牧师） |

**特殊逻辑**：`get_captaincy_announcement` — 船长缺位时晋升代理船长（同 CE）。

#### 4.2 技能芯片 — 内脏解读芯片

**代码**: `code/modules/library/skill_learning/generic_skillchips/misc.dm` L57-64

```dm
/obj/item/skillchip/entrails_reader
  name = "3NTR41LS skillchip"
  auto_traits = list(TRAIT_ENTRAILS_READER)
  skill_name = "Entrails Reader"
  desc = "Be able to learn about a person's life, by looking at their internal organs. Not to be confused with looking into the future."
  skill_icon = "lungs"
```

CMO 开局自带（`skillchips = list(/obj/item/skillchip/entrails_reader)`）。

**TRAIT_ENTRAILS_READER — 内脏解读**（逐个效果）：
| 检查对象 | 代码位置 | 效果 |
|---|---|---|
| 任意器官 | `_organ.dm` L205-207 | 显示是否为**原始出生器官**（TRAIT_CLIENT_STARTING_ORGAN）—— 判断这器官是不是本人原装的（换过器官/被调包一眼看穿） |
| 肝脏 | `_liver.dm` L93-119 | 显示肝的**代谢特质**——看出肝主人曾经是哪个职业（警察/厨师/调酒师/小丑/医生/工程师/科学家/清洁工/法医/皇家…），换肝前的"前科"一览无余 |
| 舌头 | `_tongue.dm` L65-71 | 显示舌头主人的**食物喜好**（喜欢/讨厌/有毒的食物类型）——通过舌头了解一个人的口味人生 |

**直观理解**：CMO 掰开器官就能读出"这个人这辈子怎么活的"——原装器官、职业肝、口味舌。法医破案、医生验伤的神级辅助。

#### 4.3 装备详情

**代码**: `/datum/outfit/job/cmo`

| 部位 | 物品 |
|---|---|
| ID | `/obj/item/card/id/advanced/silver` — **银色**高级ID卡 |
| ID权限 | `/datum/id_trim/job/chief_medical_officer` |
| 制服 | CMO制服 |
| 外衣 | CMO白大褂 (`labcoat/cmo`) |
| 衣上挂 | 急救笔灯 (`flashlight/pen/paramedic`) |
| 腰带 | CMO PDA (`pda/heads/cmo`) |
| 耳机 | CMO耳机 (`heads/cmo`，医疗+指挥频道) |
| 鞋 | 白色帆布鞋 |
| 左口袋 | **蓝色激光笔** (`laser_pointer/blue`) |
| 右口袋 | **船员定位器** (`pinpointer/crew`) — 实时定位船员伤势/位置，医疗指挥核心 |
| 左手 | **外科医疗包** (`medkit/surgery`) — 开局即带全套手术工具 |
| 背包 | CMO专用背包 (`chief_medic`，背包/挎包/行李袋/斜挎包全套) |
| 生存盒 | 医疗生存盒 |
| 变装道具 | 注射枪 (`gun/syringe`) + CMO印章 |
| 技能芯片 | 内脏解读芯片 |

**MOD 变体** (`cmo/mod`)：**救援MOD服** (`mod/control/pre_equipped/rescue`) + 医疗呼吸面罩 + 急救笔灯。

#### 4.4 背包内容

```
/obj/item/melee/baton/telescopic/silver = 1   — 银色望远镜伸缩警棍（头职制式）
```

#### 4.5 特质（mind_traits + liver_traits）

```dm
mind_traits        = list(HEAD_OF_STAFF_MIND_TRAITS)
desensitized_base  = DESENSITIZED_THRESHOLD   // 0.5
liver_traits       = list(TRAIT_MEDICAL_METABOLISM, TRAIT_ROYAL_METABOLISM)
```

**HEAD_OF_STAFF_MIND_TRAITS**：`TRAIT_FAST_TYING`（系领带×0.5）+ `TRAIT_HIGH_VALUE_RANSOM`（海盗赎金3000）——详见 1.5。

**desensitized_base = 0.5（医疗脱敏）**：`DESENSITIZED_THRESHOLD` 定义于 `mood.dm` L117 = **0.5**。出生时 `mind.desensitized_level ×= 0.5` —— 对血腥/尸体/死亡的负面心情减半。医生见惯生死，血溅一脸不动如山。

**TRAIT_MEDICAL_METABOLISM — 医疗代谢**（肝特质）：
- **牛顿苹果彩蛋**：被投掷的苹果砸中时额外受 **2 点钝击伤害**（`grown/apple.dm` L44-45）——"医生被苹果砸头"的牛顿梗，医疗肝唯一的机械效果。
- Entrails Reader 检查可见（"这是医生的肝"）。

**TRAIT_ROYAL_METABOLISM — 皇家肝**：同 CE（1.5），头职身份标记。

#### 4.6 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 随机器官刷 | 10 | 最常见的医疗邮包 — 随机人体器官 |
| 随机"梗器官"刷 | 8 | 趣味/异种器官 |
| 高级手术工具刷 | 4 | 高级手术器械 |
| 异星手术工具刷 | 1 | 极稀有 — 外星手术工具（等离子刀级别） |

#### 4.7 Nova 扩展

- **messenger/med** 斜挎包（Nova 覆写）。
- **替代称号**：Chief Physician / Head of Medical / Medical Supervisor / Head Physician / Medical Director / Medical Administrator。
- **Akula 制服**：`akula/doctor`。
- **RPG称号** High Cleric（Nova 设定）。

#### 4.8 一句话总结

**首席医疗官 = 指挥级工资 + 医疗经验180 + 船员定位器 + 外科医疗包 + 内脏解读芯片 + 皇家/医疗双肝 + 医疗脱敏**

医疗部的总指挥：左手定位全站伤情，右手掰开内脏读人生，银警棍防身、激光笔点人。

---

### 五、医生 Medical Doctor

**代码**: `code/modules/jobs/job_types/medical_doctor.dm` + Nova 覆写（仅 messenger）

#### 5.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 6总 / **4出生**（2 个空位留给中盘加入） |
| 上级 | 首席医疗官 (SUPERVISOR_CMO) |
| 部门 | 医疗部 |
| 工资等级 | PAYCHECK_CREW |
| 发薪部门 | 医疗部账目 (ACCOUNT_MED) |
| 经验类型 | 船员经验（无硬性分钟要求） |
| 悬赏类型 | CIV_JOB_MED_VIRO |
| 家族遗物 | 古代传家医疗箱 / 手术刀 / 止血钳 / 环锯 / 牵开器 / 电灼器 / 希波克拉底半身像（同CMO） |
| RPG称号 | Cleric（牧师） |

#### 5.2 技能芯片 — 内脏解读芯片

**与 CMO 同款**：`/obj/item/skillchip/entrails_reader` → `TRAIT_ENTRAILS_READER`（详见 4.2）。普通医生也能一眼读出器官的原装性、肝的职业前科和舌头的口味。

#### 5.3 装备详情

**代码**: `/datum/outfit/job/doctor`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/medical_doctor` |
| 制服 | 医生制服 |
| 外衣 | 白大褂 (`labcoat`) |
| 衣上挂 | 笔灯 (`flashlight/pen`) |
| 腰带 | 医疗PDA (`pda/medical`) |
| 耳机 | 医疗频道耳机 (`headset_med`) |
| 鞋 | 白色帆布鞋 |
| 左手 | **外科医疗包** (`medkit/surgery`) — 开局全套手术工具 |
| 背包 | 医用背包 (`medic`，背包/挎包/行李袋/斜挎包全套) |
| 生存盒 | 医疗生存盒 |
| 变装道具 | 注射枪 (`gun/syringe`) |
| 技能芯片 | 内脏解读芯片 |

**注意**：医生无手套/无头饰——白大褂+笔灯+手术包，标准医者形象。医生没有独立 MOD 变体（急救员的 `doctor/mod` 变体仅供急救员使用）。

#### 5.4 背包内容

**无**（backpack_contents 为空——手术包直接拿在手上）。

#### 5.5 特质

```dm
desensitized_base  = DESENSITIZED_THRESHOLD   // 0.5
liver_traits       = list(TRAIT_MEDICAL_METABOLISM)
```

- **医疗脱敏 0.5**：见 4.5。
- **TRAIT_MEDICAL_METABOLISM**：见 4.5（苹果彩蛋 + Entrails 可见）。无 mind_traits。

#### 5.6 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 高级健康分析仪 | 15 | 最常见 — 比基础版多显示更多身体数据 |
| 高级手术刀 | 6 | 进阶手术工具 |
| 高级牵开器 | 6 | 进阶手术工具 |
| 高级电灼器 | 6 | 进阶手术工具 |
| 甲醛瓶 | 6 | 防腐/尸检用（法医同款耗材） |
| 随机器官刷 | 5 | 人体器官 |
| 随机"梗器官"刷 | 1 | 极稀有 |

#### 5.7 Nova 扩展

- **messenger/med** 斜挎包。
- **替代称号**：General Practitioner / Medical Resident / Nurse / Physician / Surgeon / Medical Student / Clinician / Physician Assistant / Emergency Physician / Registered Nurse。
- **Akula 制服**：`akula/doctor`。

#### 5.8 一句话总结

**医生 = 6人编制(4出生) + 外科医疗包 + 内脏解读芯片 + 医疗肝 + 医疗脱敏**

医疗部的中坚力量：人手一套手术包、脑内一枚读器官芯片，见到谁都能先扫描再下刀。

---

### 六、化学师 Chemist

**代码**: `code/modules/jobs/job_types/chemist.dm` + Nova 覆写（仅 messenger）

#### 6.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 2总 / 2出生 |
| 上级 | 首席医疗官 (SUPERVISOR_CMO) |
| 部门 | 医疗部 |
| 工资等级 | PAYCHECK_CREW |
| 发薪部门 | 医疗部账目 (ACCOUNT_MED) |
| 经验类型 | 船员经验；需 **60 分钟** |
| 悬赏类型 | CIV_JOB_CHEM（化学悬赏） |
| 家族遗物 | 化学手册书 (`book/manual/wiki/chemistry`) / 药房手册 (`ph_booklet`) |
| RPG称号 | Alchemist（炼金术士） |

#### 6.2 技能芯片

**无技能芯片**。化学师不带任何 skillchip——靠的是配方记忆和化学工作站。

#### 6.3 装备详情

**代码**: `/datum/outfit/job/chemist`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/chemist` |
| 制服 | 化学师制服 |
| 外衣 | 化学师白大褂 (`labcoat/chemist`) |
| 腰带 | 化学师PDA (`pda/chemist`) |
| 耳机 | 医疗频道耳机 |
| 眼镜 | **科学护目镜** (`glasses/science`) — 防止化学溅射伤眼 |
| 鞋 | 白色帆布鞋 |
| 左口袋 | **随机缓冲液瓶** (`bottle/random_buffer`) — 随机初始缓冲液，开局的炼金起点 |
| 右口袋 | **滴管** (`dropper`) — 精确取液 |
| 背包 | 化学背包 (`chemistry`，背包/挎包/行李袋/斜挎包全套) |
| 生存盒 | 医疗生存盒 |
| 变装道具 | 注射枪 |

#### 6.4 背包内容

**无**。

#### 6.5 特质

```dm
liver_traits = list(TRAIT_MEDICAL_METABOLISM)
```

`TRAIT_MEDICAL_METABOLISM` — 见 4.5（苹果彩蛋 + Entrails 可见）。无 mind_traits、无脱敏。

#### 6.6 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 闪光粉瓶 (flash_powder) | 15 | 最常见 — 做闪光弹/恶作剧的原料 |
| 异星稳定剂瓶 (exotic_stabilizer) | 5 | 稀有试剂 — 稳定异星物质 |
| 醋酸铅瓶 (leadacetate) | 5 | 毒物原料 |
| 秘方纸条 (secretrecipe) | 1 | 极稀有 — "秘密配方"纸条，可能指向隐藏配方 |

#### 6.7 Nova 扩展

- **messenger/chem** 斜挎包。
- **替代称号**：Registered Pharmacist / Clinical Pharmacist / Assistant Pharmacist / Chemical Engineer / Pharmacist / Pharmacologist / Trainee Pharmacist。
- **无 Akula 专用制服**（`_job_attire.dm` 中未列出化学师——用默认 Akula 制服）。

#### 6.8 一句话总结

**化学师 = 2人编制 + 科学护目镜 + 随机缓冲液 + 滴管 + 医疗肝 + 无技能芯片**

全站药品供应链的源头：开局一瓶随机缓冲液、一支滴管，剩下的全凭脑子里的配方表。

---

### 七、病毒学家 Virologist

**代码**: `modular_nova/master_files/code/modules/jobs/job_types/virologist.dm` —— **本分支中病毒学家为 Nova 独占职业文件**（基础代码库 `code/modules/jobs/job_types/` 下没有 virologist.dm）。

#### 7.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 1总 / 1出生 |
| 上级 | 首席医疗官 (SUPERVISOR_CMO) |
| 部门 | 医疗部 |
| 工资等级 | PAYCHECK_CREW |
| 发薪部门 | 医疗部账目 (ACCOUNT_MED) |
| 经验类型 | 船员经验；需 **60 分钟** |
| 悬赏类型 | CIV_JOB_VIRO（病毒悬赏） |
| 家族遗物 | 注射器 / 希波克拉底半身像 |
| RPG称号 | Plague Doctor（瘟疫医生） |

#### 7.2 技能芯片

**无技能芯片**。

#### 7.3 装备详情

**代码**: `/datum/outfit/job/virologist`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/virologist` |
| 制服 | 病毒学家制服 |
| 外衣 | 病毒学白大褂 (`labcoat/virologist`) |
| 衣上挂 | 笔灯 |
| 腰带 | **病毒学PDA** (`pda/viro`) — 预装程序：**医疗记录 + 机器人控制**（病毒室查病例、控机仆两不误） |
| 耳机 | 医疗频道耳机 |
| 口罩 | **外科口罩** (`mask/surgical`) |
| 鞋 | 白色帆布鞋 |
| 背包 | 病毒学背包 (`virology`，背包/挎包/行李袋/斜挎包全套) |
| 生存盒 | 医疗生存盒 |

**PDA 专属配色**：`pda/viro` 为灰阶三色条纹（白/蓝/绿），灰色配置 `stripe_double`。

#### 7.4 背包内容

**无**。

#### 7.5 特质

```dm
liver_traits = list(TRAIT_MEDICAL_METABOLISM)
```

`TRAIT_MEDICAL_METABOLISM` — 见 4.5。无 mind_traits、无脱敏。

#### 7.6 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 随机病毒瓶 | 15 | 最常见 — 随机病毒样本，研究素材 |
| 甲醛瓶 | 10 | 防腐/灭活 |
| 精神安定剂瓶 (synaptizine) | 10 | 抗精神类药物 |
| 等离子板 | 10 | 矿物原料（病毒培养基成分） |
| 铀板 | 5 | 矿物原料 |

#### 7.7 Nova 扩展

- **整个职业文件位于 Nova**：本分支的病毒学家由 `modular_nova/master_files` 提供（独立于上游 TG 的职业拆分方案），上游 `code/modules/jobs/job_types/` 无此文件。
- **messenger/vir** 斜挎包（`messenger/vir`）。
- **替代称号**：Epidemiologist / Microbiologist / Pathologist / Junior Pathologist。
- **Akula 制服**：`akula/doctor`。
- **RPG称号** Plague Doctor（Nova 设定）。

#### 7.8 一句话总结

**病毒学家 = 1人编制 + 外科口罩 + 病毒学PDA(病历+机器人控制) + 随机病毒瓶邮件 + 医疗肝 + 无技能芯片**

Nova 独占的瘟疫医生：全站唯一研究病毒的人，好坏病毒都从这双手里诞生。

---

### 八、基因学家 Geneticist

**代码**: `code/modules/jobs/job_types/geneticist.dm` + Nova 覆写（仅 messenger）

> **注意**：基因学家名义上属**科研部**（上级是研发总监、发薪走科研部账目），但职责上与医疗/克隆强相关，故收入本篇。

#### 8.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 2总 / 2出生 |
| 上级 | **研发总监** (SUPERVISOR_RD) |
| 部门 | **科研部** (science) |
| 工资等级 | PAYCHECK_CREW |
| 发薪部门 | **科研部账目** (ACCOUNT_SCI) |
| 经验类型 | 船员经验；需 **60 分钟** |
| 悬赏类型 | CIV_JOB_SCI（科研悬赏） |
| 家族遗物 | **紫色短裤** (`under/shorts/purple`) — 基因学家的标志性紫短裤 |
| RPG称号 | Genemancer（基因法师） |

#### 8.2 技能芯片

**无技能芯片**。

#### 8.3 装备详情

**代码**: `/datum/outfit/job/geneticist`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/geneticist` |
| 制服 | 科研部制服 (`under/rank/rnd/geneticist`) |
| 外衣 | 基因学白大褂 (`labcoat/genetics`) |
| 衣上挂 | 笔灯 |
| 腰带 | 基因学家PDA (`pda/geneticist`) |
| 耳机 | **科研频道耳机** (`headset_sci`) |
| 鞋 | 白色帆布鞋 |
| 左口袋 | **基因序列扫描仪** (`sequence_scanner`) — 扫描DNA/基因序列，基因操作核心工具 |
| 背包 | 基因学背包 (`genetics`，背包/挎包/行李袋/斜挎包全套) |

**缺失项**：基因学家**没有**生存盒 (`box`)、没有变装道具 (`chameleon_extras`)、没有技能芯片——是医疗/科研线里装备最简的职业之一。

#### 8.4 背包内容

**无**。

#### 8.5 特质

**无任何特质**——基因学家既没有 mind_traits，也没有 liver_traits，也没有脱敏基础值。干净的科研人员体质。

#### 8.6 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 猴子方块箱 | 10 | 唯一邮件 — 猴子方块（变猴子/做实验的核心耗材） |

#### 8.7 Nova 扩展

- **messenger/gen** 斜挎包。
- **替代称号**：Molecular Biologist / Gene Scientist / Gene Analyzer / Gene Tailor / Mutation Researcher。
- **Akula 制服**：`akula/scientist`（科研系鲨人制服）。

#### 8.8 一句话总结

**基因学家 = 2人编制 + 科研部编制 + 序列扫描仪 + 猴子方块邮件 + 紫色短裤遗物 + 零特质**

披着白大褂的科研人员：改基因、变猴子、存DNA，全站唯一能把人变猴又变回来的人。

---

### 九、法医 Coroner

**代码**: `code/modules/jobs/job_types/coroner.dm` + Nova 覆写（仅 messenger）

#### 9.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 1总 / 1出生 |
| 上级 | **人事部长 + 首席医疗官**（"the Head of Personnel and the Chief Medical Officer"——双线汇报） |
| 部门 | 医疗部 + 服务部 (medical + service) |
| 工资等级 | PAYCHECK_CREW |
| 发薪部门 | 医疗部账目 (ACCOUNT_MED) |
| 经验类型 | 船员经验（无硬性分钟要求） |
| 悬赏类型 | CIV_JOB_MED |
| 家族遗物 | 骷髅头盔 / 台钟 / 铲子 / 腌黄瓜罐（人生终点四件套） |
| RPG称号 | Undertaker（殡葬师） |

#### 9.2 技能芯片 — 内脏解读芯片

**与 CMO / 医生同款**：`/obj/item/skillchip/entrails_reader` → `TRAIT_ENTRAILS_READER`（详见 4.2）。**法医是内脏解读芯片最正牌的使用者**——验尸读器官就是他吃饭的本事。

#### 9.3 装备详情

**代码**: `/datum/outfit/job/coroner`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/coroner` |
| 制服 | 法医刷手服 (`scrubs/coroner`) |
| 外衣 | 法医白大褂 (`labcoat/coroner`) |
| 腰带 | 法医PDA (`pda/coroner`) |
| 耳机 | **服务+医疗双频道耳机** (`headset_srvmed`) — 与心理医生同款双频道 |
| 手套 | 乳胶手套·法医款 (`gloves/latex/coroner`) |
| 头 | 黑色手术帽 (`surgerycap/black`) |
| 口罩 | 外科口罩 |
| 鞋 | 黑色帆布鞋 |
| 左口袋 | **写字板** (`clipboard`) — 放尸检报告、做笔记 |
| 右口袋 | 滴管 — 取尸液/防腐液 |
| 背包 | 法医背包 (`coroner`，背包/挎包/行李袋/斜挎包全套) |
| 生存盒 | 医疗生存盒 |
| 技能芯片 | 内脏解读芯片 |

#### 9.4 背包内容 (backpack_contents)

```
/obj/item/storage/box/bodybags = 1       — 尸袋一盒（收尸必备）
/obj/item/autopsy_scanner = 1           — 尸检扫描仪（验尸核心工具）
/obj/item/storage/medkit/coroner = 1    — 法医医疗包
```

开局就是一套完整的"收尸+验尸"组合。

#### 9.5 特质（mind_traits + liver_traits）

```dm
mind_traits       = list(TRAIT_MORBID)
desensitized_base = DESENSITIZED_THRESHOLD   // 0.5
liver_traits      = list(TRAIT_CORONER_METABOLISM)
```

**TRAIT_MORBID — 病态心理**（法医独有 mind trait，逐个效果）：
| 场景 | 代码位置 | 效果 |
|---|---|---|
| 幽灵附身物品 (hauntium) | `haunted_controller.dm` L31-32 | **不会成为闹鬼物品的袭击目标**——鬼都嫌你晦气 |
| 水族箱 | `aquarium.dm` L714-719 | 看到**活鱼**反而获得坏心情（morbid_aquarium_bad）——病态者只喜欢死物 |
| 挖坟 | `gravedigger.dm` L48 | 挖坟耗时 **×0.7**（10秒→7秒）——挖坑专业户 |
| 被血溅一身 | `generic_negative_events.dm` L637-639 | **心情 0 惩罚**："I just got coated in blood. Fascinating!"（被血淋一身：真有趣！） |
| 区域美观度 | `mood.dm` L538+ | 对区域美观度的心情反应与常人不同 |

**desensitized_base = 0.5**：医疗脱敏（见 4.5）——法医对死亡天然免疫，再叠病态特质，全站最铁石心肠。

**TRAIT_CORONER_METABOLISM — 法医代谢**（肝特质）：
- **腌黄瓜**（pickle）被判定为"喜欢食物"（`food/misc.dm` L649-652，`FOOD_LIKED`）——法医吃腌黄瓜有心情加成，遗物里的腌黄瓜罐闭环了。
- Entrails Reader 检查可见（"这是法医的肝"）。

#### 9.6 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 甲醛瓶 | 30 | 最常见 — 防腐/尸检耗材 |
| 尸袋箱 | 15 | 收尸耗材 |
| 健康分析仪 | 10 | 基础版 |
| 钝锯齿铲 | 5 | 挖坟专用铲（钝口，暗示…） |
| 随机器官刷 | 5 | 人体器官 |
| 罐装脑 (brain_in_a_jar) | 5 | 脑标本罐 |
| 随机"梗器官"刷 | 1 | 极稀有 |
| 镰刀 (scythe) | 1 | 极稀有 — 死神同款镰刀 |

#### 9.7 Nova 扩展

- **messenger/coroner** 斜挎包（`messenger/coroner`）。
- **替代称号**：Forensic Pathologist / Funeral Director / Medical Examiner / Mortician。
- **Akula 制服**：`akula/doctor`。

#### 9.8 一句话总结

**法医 = 1人编制 + 病态心理(鬼不理/血不惊/挖坟快) + 法医肝(爱腌黄瓜) + 尸检套件 + 内脏解读芯片 + 双频道耳机**

游走在生死边界的殡葬师：解剖刀、尸袋、镰刀三件套，看活鱼都难受、被血淋身却说"真有趣"。

---

### 十、急救员 Paramedic

**代码**: `code/modules/jobs/job_types/paramedic.dm` + Nova 覆写（仅 messenger）

#### 10.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 2总 / 2出生 |
| 上级 | 首席医疗官 (SUPERVISOR_CMO) |
| 部门 | 医疗部 |
| 工资等级 | PAYCHECK_CREW |
| 发薪部门 | 医疗部账目 (ACCOUNT_MED) |
| 经验类型 | 船员经验（无硬性分钟要求） |
| 悬赏类型 | CIV_JOB_MED |
| 家族遗物 | 古代传家医疗箱 / **救援鱼钩** (`fishing_hook/rescue`) |
| RPG称号 | Corpse Runner（尸体搬运工） |

#### 10.2 技能芯片

**无技能芯片**。急救员靠机动性和装备救人。

#### 10.3 装备详情

**代码**: `/datum/outfit/job/paramedic`

| 部位 | 物品 |
|---|---|
| ID | `/obj/item/card/id/advanced` — **高级ID卡**（普通船员是基础卡） |
| ID权限 | `/datum/id_trim/job/paramedic` |
| 制服 | 急救员制服 |
| 外衣 | 急救员白大褂 (`labcoat/paramedic`) |
| 衣上挂 | 急救笔灯 (`flashlight/pen/paramedic`) |
| 腰带 | **急救员医疗腰带** (`belt/medical/paramedic`) — 医用腰带，随身带药 |
| 耳机 | 医疗频道耳机 |
| 头 | 急救员软帽 (`soft/paramedic`) |
| 手套 | **丁腈手套** (`gloves/latex/nitrile`) — 比乳胶更耐化学品 |
| 鞋 | 黑色工作靴 (`workboots/black`) — 全医疗部唯一穿靴子的 |
| 左口袋 | 急救员PDA (`pda/medical/paramedic`) |
| 右口袋 | **绷带盒** (`box/bandages`) |
| 背包 | 医用背包 (`medic` 全套) |
| 生存盒 | 医疗生存盒 |
| 变装道具 | 注射枪 |

**MOD 变体** (`/datum/outfit/job/doctor/mod`，定义于 paramedic.dm L71)：**医疗MOD服** (`mod/control/pre_equipped/medical`) + 医疗呼吸面罩 + 笔灯 + 氧气罐。

#### 10.4 背包内容

```
/obj/item/emergency_bed = 1   — 应急折叠床！现场展开把伤员拉回医疗部
```

急救员的招牌装备——**应急折叠床**（rollerbod），全站唯一开局自带伤员运输工具的职业。

#### 10.5 特质

```dm
desensitized_base = DESENSITIZED_THRESHOLD   // 0.5
liver_traits      = list(TRAIT_MEDICAL_METABOLISM)
```

- **医疗脱敏 0.5**：见 4.5。
- **TRAIT_MEDICAL_METABOLISM**：见 4.5。无 mind_traits。

#### 10.6 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 基础医疗笔 (medipen) | 20 | 最常见 — 肾上腺素笔 |
| 奥沙龙笔 (oxandrolone) | 10 | 促恢复笔 |
| 水杨酸笔 (salacid) | 10 | 止痛笔 |
| 沙丁胺醇笔 (salbutamol) | 10 | 喘气/缺氧笔 |
| 青霉酸笔 (penacid) | 10 | 酸烧伤笔 |
| 豪华生存笔 | 5 | 生存医疗笔的豪华版 |
| 绷带盒 | 5 | 包扎耗材 |

邮件全是笔——急救员的"口袋急救箱"由邮件系统慢慢补齐。

#### 10.7 Nova 扩展

- **messenger/med** 斜挎包。
- **替代称号**：Emergency Medical Technician / Search and Rescue Technician / Trauma Team Responder / Emergency Medical Responder。
- **Akula 制服**：`akula/doctor`。

#### 10.8 一句话总结

**急救员 = 2人编制 + 应急折叠床 + 急救医疗腰带 + 丁腈手套 + 黑工作靴 + 医疗脱敏 + 医疗肝 + 无技能芯片**

全站跑得最快的白衣人：折叠床一铺、伤员一放、直奔医疗部——RPG称号"尸体搬运工"名副其实。

---

### 附：工程+医疗部速查总表

| 职业 | 编制 | 上级 | 工资 | 经验要求 | 技能芯片 | 肝特质 | mind特质 | 脱敏 |
|---|---|---|---|---|---|---|---|---|
| 总工程师 CE | 1 | 船长 | 指挥 | 180(工程) | 工程芯片 | 工程+皇家 | 头职 | 无 |
| 驻站工程师 SE | 5 | CE | 船员 | 60 | 工程芯片 | 工程 | 无 | 无 |
| 大气技师 | 3(2出生) | CE | 船员 | 60 | 无 | 工程 | 无 | 无 |
| 首席医疗官 CMO | 1 | 船长 | 指挥 | 180(医疗) | 内脏解读 | 医疗+皇家 | 头职 | 0.5 |
| 医生 | 6(4出生) | CMO | 船员 | 无 | 内脏解读 | 医疗 | 无 | 0.5 |
| 化学师 | 2 | CMO | 船员 | 60 | 无 | 医疗 | 无 | 无 |
| 病毒学家 (Nova) | 1 | CMO | 船员 | 60 | 无 | 医疗 | 无 | 无 |
| 基因学家 | 2 | 研发总监 | 船员(科研账) | 60 | 无 | **无** | 无 | 无 |
| 法医 | 1 | 人事+CMO | 船员 | 无 | 内脏解读 | 法医 | 病态 | 0.5 |
| 急救员 | 2 | CMO | 船员 | 无 | 无 | 医疗 | 无 | 0.5 |


---


### 心理医生 Psychologist

**代码**: `code/modules/jobs/job_types/psychologist.dm` + `code/modules/library/skill_learning/job_skillchips/psychologist.dm` + `code/modules/power/supermatter/supermatter_extra_effects.dm` + `code/game/objects/items/storage/pillbottles.dm`

#### 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 1总/1出生 |
| 上级 | 人事部长 + 首席医疗官 |
| 部门 | 服务部 |
| 工资等级 | CREW |
| 发薪部门 | 服务部账目 |
| 经验类型 | 船员经验 |
| 家族遗物 | 药瓶 |

#### 技能芯片 (Skillchip) — HYPERG1G4

**代码**: `code/modules/library/skill_learning/job_skillchips/psychologist.dm`

```
/obj/item/skillchip/job/psychology
  name = "HYPERG1G4 skillchip"
  desc = "Learn to bend the abyss to your will."
  auto_traits = list(
    TRAIT_SUPERMATTER_SOOTHER,   // SM心理治疗 — 站在SM旁可降低发热+抑衰减
    TRAIT_MADNESS_IMMUNE,        // 疯狂免疫 — 不受SM幻觉影响
  )
```

心理医生自带**技能芯片植入**（slot = skillchips），出生即激活。

##### TRAIT_SUPERMATTER_SOOTHER — SM心理治疗

**代码**: `supermatter_extra_effects.dm` L76-90

```dm
psychological_examination():
    for each human in SM视野范围:
        if HAS_MIND_TRAIT(seen_by_sm, TRAIT_SUPERMATTER_SOOTHER):
            psy_coeff_diff = 0.05   // 有心理医生→ 每tick +0.05
        else:
            psy_coeff_diff = -0.05  // 无心理医生→ 每tick -0.05
    psy_coeff = clamp(psy_coeff + psy_coeff_diff, 0, 1)
```

**效果** (psy_coeff 0~1):

| 受影响项 | 效果 |
|---|---|
| **温度极限** | `temp_limit += psy_coeff × 45` (最多+45K耐热) |
| **废热倍率** | `waste_multiplier -= 0.2 × psy_coeff` (最多-20%废气) |
| **能量衰减** | `powerloss -= 0.2 × psy_coeff × 衰减量` (最多-20%衰减) |

**直观理解**: 心理医生站在SM附近→SM更稳定→更耐热→产更少废热→能量衰减更慢

##### TRAIT_MADNESS_IMMUNE — 疯狂免疫

**代码**: `supermatter.dm` L259

```dm
/examine:
    if HAS_MIND_TRAIT(user, TRAIT_MADNESS_IMMUNE):
        // 不显示疯狂文本
    else:
        // 显示"你感到一股不可名状的恐惧..."
```

心理医生**不会被SM的幻觉影响**——接近SM不会产生幻觉, 检查SM不会受到精神伤害。

#### 初始药品

心理医生出生携带**5瓶专用药**, 分别对应5种常见的心理健康问题:

##### 3.1 Mannitol (甘露醇) — 脑损伤药

| 项目 | 内容 |
|---|---|
| 对应 | **情绪低落/脑损伤** |
| 治疗 | 脑部物理损伤 + 脑腐烂病 |
| 备注 | 属于常见脑药 |

##### 3.2 Happiness (快乐药)

| 项目 | 内容 |
|---|---|
| 对应 | **抑郁特质 (Depression)** |
| 效果 | 强行提升情绪 |
| 备注 | 官方名字"happiness pills" |

##### 3.3 LSD (致幻剂)

| 项目 | 内容 |
|---|---|
| 对应 | **精神错乱特质 (Insanity)** |
| 效果 | 致幻体验 (以毒攻毒) |
| 备注 | 带有隐喻: 用受控幻觉对抗精神错乱 |

##### 3.4 Pax (和平药)

| 项目 | 内容 |
|---|---|
| 对应 | **暴力倾向/敌对行为** |
| 效果 | 镇静/强制非暴力 |
| 备注 | Pax = 拉丁语"和平" |

##### 3.5 Psicodine (精神安定)

| 项目 | 内容 |
|---|---|
| 对应 | **社交焦虑/恐惧症/幻觉症状** |
| 治疗 | 治愈导致幻觉的疾病症状 |
| 备注 | 最通用的精神疾病药 |

##### 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| Mannitol药瓶 | 30 | 最常见 |
| Happy药瓶 | 5 | 稀有 |
| 注射枪 | 1 | 极稀有 — 可以远程注射 |

#### 装备详情

**代码**: `/datum/outfit/job/psychologist`

| 部位 | 物品 |
|---|---|
| ID | `/obj/item/card/id/advanced` — 高级ID卡 |
| ID权限 | `/datum/id_trim/job/psychologist` — 医疗+服务权限 |
| 制服 | 衬衫+西裤 |
| 腰带 | 心理医生PDA |
| 耳机 | 服务+医疗双频道耳机 (`headset_srvmed`) |
| 鞋子 | 系带皮鞋 |
| 左手 | 写字板 |
| 脖子 | 黑色领带 |
| 背包 | 医用背包 |

**背包内容 (5瓶药)**

```
/obj/item/storage/pill_bottle/happinesspsych  — 快乐药
/obj/item/storage/pill_bottle/lsdpsych        — LSD 
/obj/item/storage/pill_bottle/mannitol         — 甘露醇
/obj/item/storage/pill_bottle/paxpsych         — 和平药
/obj/item/storage/pill_bottle/psicodine        — 精神安定
```

#### 肝特质

**代码**: `psychologist.dm` L18

```dm
liver_traits = list(TRAIT_MEDICAL_METABOLISM)
```

`TRAIT_MEDICAL_METABOLISM` — **药物代谢优化**: 摄入的药物代谢速度更快, 减少药物在体内的副作用时间。

#### Nova 扩展

**代码**: `modular_nova/.../psychologist.dm`

Nova版本给心理医生增加了 `messenger = /obj/item/storage/backpack/messenger/med`（医用斜挎包款式）。

#### 一句话总结

**心理医生 = SM稳压器 + 疯狂免疫 + 5种精神药物 + 服务医疗双频道 + 医疗代谢**

不只是来看心理的——在工程部SM旁边站岗就是为全站做贡献。


---

## 第三篇 · 科研部与货运部



**代码**:
- `code/modules/jobs/job_types/{research_director,scientist,roboticist,quartermaster,cargo_technician,shaft_miner,cargo_gorilla}.dm`
- `code/modules/jobs/job_types/station_trait/cargo_gorilla.dm`（货运大猩猩实际生效版本）
- `code/modules/bitrunning/job.dm`（比特跑者，本分支已并入核心代码）
- `code/modules/library/skill_learning/job_skillchips/{research_director,roboticist,miner}.dm`
- `code/datums/id_trim/jobs.dm`（权限装饰）
- `modular_nova/modules/ballmer_fix/{scientist,research_director}.dm`（Nova 覆写）

> 说明：任务书提到的 `modular_nova/modules/bitrunning/code/jobs/bitrunner.dm` 在本分支中已被合并进核心代码 `code/modules/bitrunning/job.dm`，内容一致。

---


---

### 一、科研部长 Research Director（RD）

**代码**: `code/modules/jobs/job_types/research_director.dm`

#### 1.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 1总/1出生 |
| 上级 | 船长 |
| 部门 | 科研部 + 指挥组 |
| 工资等级 | PAYCHECK_COMMAND（指挥级） |
| 发薪部门 | 科研部账目 (ACCOUNT_SCI) |
| 经验要求 | 船员经验 180 + 科研部门经验 + 最少 7 天游玩年龄 |
| 家族遗物 | 史莱姆毛绒玩具 (slimeplushie) |
| 头衔 | 人类限定（`JOB_AUTHORITY_HUMANS_ONLY`）|
| RPG称号 | Archmagister（大法师）|
| 声音权力 | voice_of_god_power = 1.4（指挥层权威）|
| 到岗广播 | 科研频道 (RADIO_CHANNEL_SCIENCE) |

**职责描述**: 「监督科研工作，确保机器人部运转正常，确保AI和赛博格没有叛变——叛变了就换掉它们。」
(Supervise research efforts, ensure Robotics is in working order, make sure the AI and its Cyborgs aren't rogue, replace them if they are.)

**彩蛋**: 因人手短缺被提拔为代理船长时会广播 "Due to staffing shortages, newly promoted Acting Captain [名字] on deck!"

#### 1.2 技能芯片 (Skillchip)

RD 出生植入 **2 枚**技能芯片（`outfit.skillchips`，出生即激活）:
```dm
skillchips = list(
    /obj/item/skillchip/research_director,  // R.D.S.P.L.X.
    /obj/item/skillchip/job/roboticist,     // Cyborg C1-RCU-1T（与机器人学家同款）
)
```

##### 1.2.1 R.D.S.P.L.X. skillchip — 双 TRAIT

**代码**: `code/modules/library/skill_learning/job_skillchips/research_director.dm`

```dm
/obj/item/skillchip/research_director
    name = "R.D.S.P.L.X. skillchip"
    auto_traits = list(TRAIT_ROD_SUPLEX, TRAIT_STRENGTH)
    skill_name = "True Strength"     // 真·力量
    skill_icon = "dumbbell"
    chip_category = SKILLCHIP_CATEGORY_GENERAL
    slot_use = 1
```
激活台词: 「你意识到只要用正确的力、在正确的角度，就能让不可移动之物永久可移动。而且……天哪，你看起来壮了一圈。」
(You realise if you apply the correct force, at the correct angle, it is possible to make the immovable permanently movable. And... damn, you look huge.)

**TRAIT_ROD_SUPLEX — 不可移动之棍过肩摔**

**代码**: `immovable_rod.dm` L219-257 + `human_helpers.dm` L338-339

- 徒手点击**不可移动之棍 (immovable rod)** 时，若持有此特质 → 触发 `suplex_rod()` 把它**过肩摔**：
  - 获得成就 `feat_of_strength`（力量壮举）
  - 掉落点生成 1 根**节日柱 (festivus pole)** + 1 个**磁通异常 (flux anomaly)**
  - 获得经验：`100 × 被棍击杀的有意识生物数`（重引力下 ×2）→ 击杀 25 个以上可瞬间成为传奇运动员
  - 施加 `exercised` 状态（该休息了）
- 体能计算：`calculate_fitness()` 中 fitness_modifier **×2**（"要摔动一根棍，你必须拥有不可思议的力量"）

**TRAIT_STRENGTH — 力量**

**代码**: 全局特质 `#define TRAIT_STRENGTH "strength"`（declarations.dm L697）

| 作用点 | 效果 |
|---|---|
| `human_helpers.dm` L336 | 体能系数 ×1.5 |
| `_species.dm` L806-807 | 徒手伤害**上限 +2** |
| `boxing.dm` L158 | 拳击力量加成 +2 |
| `swimming_tile.dm` L85/145 | 游泳时耐力消耗不减半（强者游得轻松）|
| `punching_bag.dm` / `weight_machine.dm` | 健身器械耐力伤害减免 |
| `gulag_vent.dm` L45 | 古拉格通风口耐力伤害 120 → **60**（减半）|
| 钓鱼（rift/saltwater） | 单手可收重鱼（巨型裂缝鱼等）|

**直观理解**: RD 是科研部里最能打的人——徒手伤害更高、体能更强、还能把飞来的天灾（不可移动之棍）当场摔成节日柱。芯片名 R.D.S.P.L.X. = RD SuPLeX（RD 过肩摔）。

##### 1.2.2 Cyborg C1-RCU-1T skillchip — TRAIT_KNOW_ROBO_WIRES

与机器人学家共用，分析见下方机器人学家章节 §4.2。

#### 1.3 装备详情

**代码**: `/datum/outfit/job/rd`

| 部位 | 物品 |
|---|---|
| ID | `/obj/item/card/id/advanced/silver` — **银色ID** |
| ID权限 | `/datum/id_trim/job/research_director` — 科研+指挥全权限（AI上传/门禁/异星生物/军械库/传送门/EVA/密钥认证，通配权限 ACCESS_RD）|
| 制服 | RD 高领制服 (turtleneck) |
| 外套 | RD 白大褂 (toggle labcoat) |
| 腰带 | RD PDA |
| 头 | 科研贝雷帽 (beret/science/rd) |
| 耳机 | 指挥科研双频道 (headset/heads/rd) |
| 鞋 | 军靴 (jackboots) |
| 手套 | 黑色手套 |
| 左口袋 | 紫色激光笔 (laser_pointer/purple) |
| 左手 | 写字板 (clipboard) |
| 背包 | 科研背包 / 斜挎包 / 行李袋 (backpack/science 系列) |
| 变色龙位 | RD 印章 (stamp/head/rd) |

#### 背包内容
```
/obj/item/melee/baton/telescopic/silver  — 银色伸缩警棍 ×1
```

**MODsuit 变体**: 科研 MOD 装甲 (pre_equipped/research) + 氧气罐 + 呼吸面罩（替换白大褂/贝雷帽）。

#### 1.4 特质 (Traits)

```dm
mind_traits = list(HEAD_OF_STAFF_MIND_TRAITS)  // TRAIT_FAST_TYING + TRAIT_HIGH_VALUE_RANSOM
liver_traits = list(TRAIT_ROYAL_METABOLISM, TRAIT_SCIENTIST_LIVER)  // 后者被 Nova 移除，见 §1.6
```

**TRAIT_FAST_TYING — 快速打领带**: 打/解领带耗时 ×0.5（`_neck.dm` L96-98，"指挥层都是打领带专家"）。

**TRAIT_HIGH_VALUE_RANSOM — 高价值赎金**: 被海盗绑架后**赎金 3000 币**（普通船员 1000，`pirate_shuttle_equipment.dm` L451）。

**TRAIT_ROYAL_METABOLISM — 皇家代谢**: 喝皇家地毯药水 (royal carpet) 时会强制说出「Peasants..」「This carpet is worth more than your contracts!」等台词（`other_reagents.dm` L2066-2080）；开香槟剑术 (sabrage) 成功率 +20（`glassbottle.dm` L700）。

**TRAIT_SCIENTIST_LIVER — 科学家之肝（Ballmer峰）**: 醉酒时 5% 概率强制说出 Ballmer 名言（`drunk.dm` L166-176）；被苹果砸头有 2%/40% 概率「顿悟万有引力」并喊出名言（`apple.dm` L46/55）。

#### 1.5 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 猴子肉块盒 (monkeycubes) | 30 | 最常见 — 喂给异星生物 |
| 派对睡眠舱电路板 (sleeper/party) | 3 | 稀有 — 派对主题医疗舱 |
| 赛博格AI升级模块 (borg/upgrade/ai) | 2 | 极稀有 |

#### 1.6 Nova 扩展

**代码**: `modular_nova/modules/ballmer_fix/research_director.dm`

```dm
/datum/job/research_director/New()
    liver_traits -= TRAIT_SCIENTIST_LIVER   // 不再强制喊话
```
Nova 移除了 RD 的科学家之肝——「No more force-say」，Ballmer 峰强制喊话被删除。

#### 1.7 一句话总结

**科研部长 = 双技能芯片（可摔不可移动之棍 + 识破机器人电路）+ 银色ID全科研权限 + 皇家代谢 + 紫色激光笔**

物理上的科研天花板：左手写字板，右手把陨石摔成节日柱。

---

### 二、科学家 Scientist

**代码**: `code/modules/jobs/job_types/scientist.dm`

#### 2.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 5总/3出生 |
| 上级 | 科研部长 (RD) |
| 部门 | 科研部 |
| 工资等级 | PAYCHECK_CREW（船员级） |
| 发薪部门 | 科研部账目 (ACCOUNT_SCI) |
| 经验要求 | 船员经验 60 |
| 家族遗物 | 史莱姆毛绒玩具 (slimeplushie) |
| RPG称号 | Thaumaturgist（奇术师）|
| PDA铃声 | **"boom"**（job_tone = "boom"，响起来就是爆炸声梗）|

**职责描述**: 「做实验、搞研究、喂史莱姆、造炸弹。」
(Do experiments, perform research, feed the slimes, make bombs.)

**彩蛋**: 出生时有 0.4% 概率获得一条**"可怕的领带"**（horrible tie，`prob(0.4)`）——科学家品味守恒定律。

#### 2.2 技能芯片

**无技能芯片**（出生不带任何 skillchip）。

#### 2.3 装备详情

**代码**: `/datum/outfit/job/scientist`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/scientist` — 科研权限（辅助基地/军械库/异星生物/机器人部/遗传学）|
| 制服 | 科学家连体服 |
| 外套 | 科研白大褂 (labcoat/science) |
| 腰带 | 科学 PDA |
| 耳机 | 科研频道 (headset_sci) |
| 鞋 | 白色运动鞋 (sneakers/white) |
| 背包 | 科研背包 / 斜挎包 / 行李袋 / 邮差包 |

#### 背包内容
```
无特殊物品（干净清爽，符合"实验室新人"人设）
```

#### 2.4 特质 (Traits)

```dm
liver_traits = list(TRAIT_SCIENTIST_LIVER)  // 被 Nova 移除，见 §2.6
```

**TRAIT_SCIENTIST_LIVER — 科学家之肝（Ballmer峰）**: 原版效果——喝醉时处于 Ballmer 峰区间会 5% 概率强制说名言（`drunk.dm` L166-176）；苹果砸头 2%/40% 概率「牛顿附体」喊出名言（`apple.dm`）。**Nova 分支已删除此特质**（见 §2.6）。

#### 2.5 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 随机原始异常核心 (raw_anomaly_core/random) | 10 | 最常见 — 异常研究素材 |
| BEPIS 设计盘 (design_disk/bepis) | 2 | 稀有 — 工程打印蓝图 |

#### 2.6 Nova 扩展

**代码**: `modular_nova/modules/ballmer_fix/scientist.dm`

```dm
/datum/job/scientist/New()
    liver_traits -= TRAIT_SCIENTIST_LIVER   // 不再强制喊话
```
与 RD 同理，Nova 删除了科学家的 Ballmer 峰强制喊话特质。

#### 2.7 一句话总结

**科学家 = 科研权限 + 0.4%恐怖领带 + "boom"铃声PDA + 无技能芯片的纯白板**

从喂史莱姆到造炸弹，一切都是你自己的选择——记得系好那 0.4% 的领带。

---

### 三、机器人学家 Roboticist

**代码**: `code/modules/jobs/job_types/roboticist.dm`

#### 3.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 2总/2出生 |
| 上级 | 科研部长 (RD) |
| 部门 | 科研部 |
| 工资等级 | PAYCHECK_CREW（船员级） |
| 发薪部门 | 科研部账目 (ACCOUNT_SCI) |
| 经验要求 | 船员经验 60 |
| 家族遗物 | PK毛绒玩具 + **全部机甲玩具**（New() 时动态加入所有 mecha 玩具子类型）|
| RPG称号 | Necromancer（死灵法师）— 因为你"复活"机械 |

**职责描述**: 「建造与维修AI和赛博格，制造机甲。」
(Build and repair the AI and cyborgs, create mechs.)

#### 3.2 技能芯片 (Skillchip)

出生植入 1 枚技能芯片（`outfit.skillchips`）:

##### 3.2.1 Cyborg C1-RCU-1T skillchip — TRAIT_KNOW_ROBO_WIRES

**代码**: `code/modules/library/skill_learning/job_skillchips/roboticist.dm`

```dm
/obj/item/skillchip/job/roboticist
    name = "Cyborg C1-RCU-1T skillchip"   // 谐音 "Circuit"
    desc = "A roboticist's second best friend."  // 机器人学家的第二好朋友
    auto_traits = list(TRAIT_KNOW_ROBO_WIRES)
    skill_name = "Cyborg Circuitry"       // 赛博格电路学
    skill_icon = "sitemap"
```
激活台词: 「你突然领悟了赛博格电路背后的秘密。」(You suddenly comprehend the secrets behind cyborg circuitry.)

**TRAIT_KNOW_ROBO_WIRES — 识破机器人电路**

**代码**: `wires/robot.dm` L110-114 + `wires/mecha.dm` L108 + `wires/mod.dm` L61 + Nova 医疗伤口系统

| 作用点 | 效果 |
|---|---|
| `robot.dm` `can_reveal_wires()` | **直接看穿赛博格线缆布局**（不用试错/电笔）|
| `mecha.dm` `can_reveal_wires()` | 看穿**机甲**线缆布局 |
| `mod.dm` `can_reveal_wires()` | 看穿 **MOD 装甲**线缆布局 |
| Nova `robotic_blunt_T3.dm` / `secures_internals.dm` / `robotic_slash.dm` | 对合成体（赛博格/机甲）手术时**成功率更高、失误更少** |

**直观理解**: 修机器人不用再"剪线玄学"——一眼看穿所有机械体线缆，手术台上对合成体手也更稳。这是 RD 与机器人学家的共享芯片。

#### 3.3 装备详情

**代码**: `/datum/outfit/job/roboticist`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/roboticist` — 科研+机器人部+军械库+停尸房 |
| 制服 | 机器人学家连体服 |
| 外套 | 机器人学家白大褂 (labcoat/roboticist) |
| 腰带 | **满配工具腰带** (belt/utility/full) |
| 耳机 | **医疗+科研双频道** (headset_medsci) ← Nova 修改，原版为科研单频道 |
| 左口袋 | 机器人学家 PDA（PDA 槽位在左口袋）|

#### 背包内容
```
无（工具都在腰带上）
```

**MODsuit 变体**: 标准 MOD 装甲 (pre_equipped/standard) + 氧气罐 + 呼吸面罩。

#### 3.4 特质 (Traits)

```dm
mind_traits = 无
liver_traits = 无
```
机器人学家没有特殊心智/肝脏特质——他的本事全在芯片里。

#### 3.5 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 闪光弹盒 (box/flashes) | 20 | 最常见 — 造机用 |
| 铁锭 ×20 (stack/sheet/iron/twenty) | 15 | 常见 — 造机用 |
| 笔记本电脑 (modular_computer/laptop) | 5 | 稀有 |
| 球形脑尸 (mmi/posibrain/sphere) | 5 | 稀有 — 球形正电子脑 |

#### 3.6 Nova 扩展

- **耳机变更**: `headset_sci` → `headset_medsci`（医疗+科研双频道，文件内 NOVA EDIT 标记）— 方便与医生配合做合成体手术。
- 家族遗物动态扩充：`family_heirlooms += subtypesof(/obj/item/toy/mecha)` — 所有机甲玩具都可能成为你的家传宝。

#### 3.7 一句话总结

**机器人学家 = 识破所有机械线缆的芯片 + 满配工具腰带 + 医科双频道耳机 + 死灵法师**

机械的造物主与救赎者——赛博格的第二好朋友，就是他自己。

---

### 四、比特跑者 Bitrunner

**代码**: `code/modules/bitrunning/job.dm`（Nova 模块已并入核心代码；任务书路径为 `modular_nova/modules/bitrunning/code/jobs/bitrunner.dm`，本分支已迁移）

#### 4.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 3总/3出生 |
| 上级 | 军需官 (QM) |
| 部门 | **货运部**（虚拟领域打工人，隶属补给系统）|
| 工资等级 | PAYCHECK_CREW（船员级） |
| 发薪部门 | 货运部账目 (ACCOUNT_CAR) |
| 家族遗物 | 太空山风汽水罐 (space_mountain_wind) |
| RPG称号 | Recluse（隐士）— 宅在虚拟世界的冲浪者 |

**职责描述**: 「在虚拟领域中冲浪，搜寻装备与战利品，回站解密你的奖励。」
(Surf the virtual domain for gear and loot, decrypt your rewards on station.)

**玩法背景**: 通过量子服务器 (qserver) 进入**虚拟领域 (virtual domain)** 打副本——扫描器等级决定可见领域、点数购买入场券、通关掉落加密宝箱回站解密。领域里还有幽灵扮演的**领域守护者**（"Defend your domain from the intruders!"，`antagonists/ghost_role.dm`）。

#### 4.2 技能芯片

**无技能芯片**（比特跑者的本事在扫描器与解密里）。

#### 4.3 装备详情

**代码**: `/datum/outfit/job/bitrunner`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/bitrunner` — **比特巢穴 (ACCESS_BIT_DEN)** + 货运 + 维修通道 + 采矿机甲 + 矿物库房（额外：采矿/采矿站）|
| 制服 | 比特跑者连体服 (under/rank/cargo/bitrunner) |
| 腰带 | 比特跑者 PDA (pda/bitrunner) |
| 耳机 | 货运频道 (headset_cargo) |

#### 背包内容
```
无特殊物品
```

（另有 `plasmaman_outfit = /datum/outfit/plasmaman/bitrunner` 等离子人变体。）

#### 4.4 特质 (Traits)

```dm
mind_traits = 无
liver_traits = 无
```
无特殊特质——纯技术流职业。

#### 4.5 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 玉米片 (cornchips) | 1 | 原味 |
| 太空山风汽水 | 1 | 经典宅家饮料 |
| 绿/红/紫/蓝玉米片 | 各1 | 全套彩虹玉米片 |

#### 4.6 Nova 扩展

- **Cybernetic Revolution 站台特质彩蛋**: 该正面特质会给比特跑者植入**热成像机械眼**（`positive_traits.dm` L263）——虚拟冲浪者标配夜视。
- 本分支中比特跑者已从 modular_nova 模块迁入核心代码 `code/modules/bitrunning/`，含完整虚拟领域子系统 (`SSbitrunning`)、加密锁箱、领域守护者幽灵阵营。

#### 4.7 一句话总结

**比特跑者 = 货运部编外 + 虚拟领域打副本 + 加密宝箱 + 玉米片管够的隐士**

表面是货运部的人，实际是潜入数据世界刷装备的网瘾冒险家。

---


---

### 五、军需官 Quartermaster（QM）

**代码**: `code/modules/jobs/job_types/quartermaster.dm`

#### 5.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 1总/1出生 |
| 上级 | 船长 |
| 部门 | 货运部 + 指挥组 |
| 工资等级 | PAYCHECK_COMMAND（指挥级） |
| 发薪部门 | 货运部账目 (ACCOUNT_CAR) |
| 经验要求 | 最少 7 天游玩年龄 + 补给部门经验 |
| 家族遗物 | **"准予"印章 + "驳回"印章** (stamp/granted + stamp/denied) |
| 种族限制 | 允许非人类 (`JOB_AUTHORITY_NON_HUMANS_ALLOWED`) |
| RPG称号 | Steward（总管）|
| 声音权力 | voice_of_god_power = 1.4（指挥层权威）|
| 到岗广播 | 补给频道 (RADIO_CHANNEL_SUPPLY) |

**职责描述**: 「协调货运技师与矿工，协助经济采购。」
(Coordinate cargo technicians and shaft miners, assist with economical purchasing.)

**肝注释彩蛋**: 源码中 `liver_traits = list(TRAIT_ROYAL_METABOLISM) // finally upgraded`（"终于升级了"）——QM 曾经只有"伪皇家代谢" (pretender royal)，现在正式转正。

#### 5.2 技能芯片

**无技能芯片**。

#### 5.3 装备详情

**代码**: `/datum/outfit/job/quartermaster`

| 部位 | 物品 |
|---|---|
| ID | `/obj/item/card/id/advanced/silver` — **银色ID** |
| ID权限 | `/datum/id_trim/job/quartermaster` — 货运+指挥全权限（金库/比特巢穴/预算/采矿站/密钥认证/武器库/EVA，通配权限 ACCESS_QM，指挥级大指针）|
| 制服 | QM 货运制服 |
| 腰带 | QM PDA |
| 外套 | QM 夹克 (jacket/quartermaster) |
| 耳机 | 指挥补给双频道 (heads/qm) |
| 眼镜 | 墨镜 (sunglasses) |
| 鞋 | 系带皮鞋 (laceup) |
| 左手 | 写字板 (clipboard) |
| 背包 | （默认背包）|
| 变色龙位 | QM 印章 (stamp/head/qm) |

#### 背包内容
```
/obj/item/melee/baton/telescopic/bronze  — 青铜伸缩警棍 ×1
```

#### 5.4 特质 (Traits)

```dm
mind_traits = list(HEAD_OF_STAFF_MIND_TRAITS)  // TRAIT_FAST_TYING + TRAIT_HIGH_VALUE_RANSOM
liver_traits = list(TRAIT_ROYAL_METABOLISM)    // 皇家代谢（"终于升级了"）
```

- **TRAIT_FAST_TYING**: 打领带耗时减半（指挥层标配）。
- **TRAIT_HIGH_VALUE_RANSOM**: 被海盗绑票赎金 3000 币（普通船员 1000）。
- **TRAIT_ROYAL_METABOLISM**: 皇家地毯药水强制说贵族台词；开香槟剑术 +20 成功率。注：QM 曾在旧版本是 `TRAIT_PRETENDER_ROYAL_METABOLISM`（伪皇家，台词更卑微），现已成为真·皇家代谢。

#### 5.5 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 发射器电路板 (circuitboard/machine/emitter) | 3 | 唯一礼物 — 工程部大杀器电路板 |

#### 5.6 Nova 扩展

- 无独立覆写文件；唯一的 Nova 痕迹是源码内的中文注释（i18n codemod）与 `// finally upgraded` 梗。
- 注意：任务书所述"modular_nova 覆写"在本分支中不存在独立文件——QM 直接使用核心版本。

#### 5.7 一句话总结

**军需官 = 银色ID金库权限 + 皇家代谢 + 青铜伸缩警棍 + 准予/驳回双印章**

货运部唯一穿皮鞋打领带的人——盖章的快乐你想象不到。

---

### 六、货运技师 Cargo Technician

**代码**: `code/modules/jobs/job_types/cargo_technician.dm`

#### 6.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 5总/3出生 |
| 上级 | 军需官 (QM) |
| 部门 | 货运部 |
| 工资等级 | PAYCHECK_CREW（船员级） |
| 发薪部门 | 货运部账目 (ACCOUNT_CAR) |
| 家族遗物 | 写字板 (clipboard) |
| RPG称号 | Merchantman（商贾）|
| 头衔敬语 | "Courier"（信使）|

**职责描述**: 「把各部门订购的物资分发到位，回收空板条箱，装卸补给穿梭机，运送赏金箱。」
(Distribute supplies to the departments that ordered them, collect empty crates, load and unload the supply shuttle, ship bounty cubes.)

#### 6.2 技能芯片

**无技能芯片**。

#### 6.3 装备详情

**代码**: `/datum/outfit/job/cargo_tech`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/cargo_technician` — 预算/货运/维修通道/采矿机甲/矿物库房/发货区（额外：比特巢穴/采矿/采矿站）|
| 制服 | 货运技师连体服 |
| 腰带 | 货运 PDA |
| 外套 | 货运技师夹克 (toggle/cargo_tech) |
| 耳机 | 货运频道 (headset_cargo) |
| 左手 | **万用扫描仪** (universal_scanner) |
| 背包 | （默认背包）|

#### 背包内容
```
/obj/item/boxcutter  — 拆箱刀 ×1（开板条箱专用）
```

**MODsuit 变体**: **装卸工 MOD 装甲** (pre_equipped/loader) — 搬运工专用型号。

#### 6.4 特质 (Traits)

```dm
mind_traits = 无
liver_traits = 无
```
纯体力活职业，无特殊特质。

#### 6.5 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 披萨盒 (pizzabox) | 10 | 最常见 — 运送中的"意外收获" |
| 金矿 ×1 | 5 | 常见 |
| 铀矿 ×1 | 4 | 稀有 |
| 钻石 ×1 | 3 | 稀有 |
| 栓动步枪 (boltaction) | 1 | 极稀有 — 咦？ |
| WT550 自动步枪 | 1 | 极稀有 — 货运部军火库？ |

#### 6.6 Nova 扩展

- 无独立覆写文件；`Cybernetic Revolution` 站台特质给货运技师**T2 机械胃**（`positive_traits.dm` L266）。
- MODsuit 变体 (loader) 为 Nova 体系的标准装卸工装甲。

#### 6.7 一句话总结

**货运技师 = 万用扫描仪 + 拆箱刀 + 装卸工MOD + 邮件偶尔寄来一把步枪**

星站的物流血管，工资不高，但拆箱的爽感无价。

---

### 七、矿工 Shaft Miner

**代码**: `code/modules/jobs/job_types/shaft_miner.dm`

#### 7.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 3总/3出生 |
| 上级 | 军需官 (QM) |
| 部门 | 货运部 |
| 工资等级 | PAYCHECK_CREW（船员级） |
| 发薪部门 | 货运部账目 (ACCOUNT_CAR) |
| 家族遗物 | 迷你镐 (pickaxe/mini) 或 铁锹 (shovel) |
| RPG称号 | Adventurer（冒险者）|

**职责描述**: 「去陌生的土地旅行，开采矿石，遇见奇怪的生物，然后为了它们的金子干掉它们。」
(Travel to strange lands, mine ores, meet strange creatures, kill them for their gold.)

#### 7.2 技能芯片 (Skillchip)

出生植入 1 枚技能芯片（`outfit.skillchips`）:

##### 7.2.1 TUNN3L_R4T skillchip — TRAIT_ROCK_STONER

**代码**: `code/modules/library/skill_learning/job_skillchips/miner.dm`

```dm
/obj/item/skillchip/job/miner
    name = "TUNN3L_R4T skillchip"   // 谐音 "Tunnel Rat"（隧道鼠）
    desc = "Gain control of minebots."   // 获得矿机控制权
    auto_traits = list(TRAIT_ROCK_STONER)
    skill_name = "Battlebot enthusiast"  // 战斗机器人爱好者
    skill_description = "Lead minebots into war."  // 带领矿机去打仗
```
激活台词: 「一种对战斗机器人的全新痴迷在你心中萌生。」
(A newfound obsession with battlebots fosters within you.)
失效台词: 「你终于度过了战斗机器人中二时期，现在去找份正经工作吧。」
(You finally get over your battlebots phase, now go get a job.)

**TRAIT_ROCK_STONER — 岩石守望者（矿机驯服者）**

**代码**: `minebot_ai.dm` L58-70（`/datum/ai_planning_subtree/befriend_miners`）

```dm
/datum/ai_behavior/find_and_set/miner_to_befriend/search_tactic(...)
    for(var/mob/living/carbon/human/target in oview(search_range, controller.pawn))
        if(HAS_TRAIT(target, TRAIT_ROCK_STONER))
            return target
```

**效果**: 矿机 (minebot) 的 AI 会**主动搜索附近带有 TRAIT_ROCK_STONER 的人类并与之"交友"**——矿机跟随你、听从你的指挥，变成你的私人战斗/挖矿小队。芯片描述直白点题："Lead minebots into war"（带矿机打仗）。

**直观理解**: 别人挖矿靠镐，你挖矿靠一整个矿机军团——芯片名 TUNN3L_R4T（隧道鼠）点明地下战专家人设。

#### 7.3 装备详情

**代码**: `/datum/outfit/job/miner`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/shaft_miner` — 采矿/采矿站/矿物库房/采矿机甲/货运（额外：比特巢穴/维修通道）|
| 制服 | 熔岩地采矿连体服 (under/rank/cargo/miner/lavaland) |
| 腰带 | 矿工 PDA (pda/shaftminer) |
| 耳机 | 货运+采矿频道 (headset_cargo/mining) |
| 手套 | 黑色手套 |
| 鞋 | 采矿工靴 (workboots/mining) |
| 左口袋 | **生存医疗笔** (medipen/survival) |
| 右口袋 | **矿石袋** (bag/ore) — 放背包会出问题，所以放口袋 |
| 背包 | 探险家背包 / 斜挎包 / 行李袋 / 邮差包 (explorer 系列) |
| 生存盒 | 采矿生存盒 (box/survival/mining)：折叠探险家面罩 + 紧急氧气罐 + 生存医疗笔 + **红色撬棍** + **矿工简易健康分析仪** |
| 变色龙位 | 动能加速器 (kinetic_accelerator) |

#### 背包内容
```
/obj/item/flashlight/seclite                  — 强光手电 ×1
/obj/item/knife/combat/survival               — 生存战斗刀 ×1
/obj/item/mining_voucher                      — 采矿兑换券 ×1
/obj/item/suit_voucher                        — 套装兑换券 ×1（NOVA 新增）
/obj/item/stack/marker_beacon/ten             — 标记信标 ×10
/obj/item/t_scanner/adv_mining_scanner/lesser — 简易高级采矿扫描仪 ×1
```

#### 进阶出装变体
| 变体 | 追加内容 |
|---|---|
| **equipped（装备版）** | 探险家兜帽服 + 氧气罐（胸口）+ **动能加速器** + 麦森眼镜 + 探险家防毒面罩 |
| **combat（战斗版）** | 夜视健康HUD麦森镜 + 护腕手套 + 护身符 + **矿工改造件盒** + 动能加速器×2 + 紧凑动能碎骨者 + 升级谐振器 + 双倍氧气罐+GPS+高级采矿扫描仪（bonus生存盒）+ 采矿治疗腰带；落地后自动给探险家服**3层巨兽皮甲**（衣+帽各3层）+ 动能加速器装**刺刀+强光手电** |

#### 7.4 特质 (Traits)

```dm
mind_traits = list(TRAIT_DETECT_STORM)   // 风暴感知
liver_traits = 无
```

**TRAIT_DETECT_STORM — 风暴感知**

**代码**: `weather.dm` L408-410

```dm
/datum/weather/proc/can_see_weather(mob/player)
    if(HAS_MIND_TRAIT(player, TRAIT_DETECT_STORM))
        return TRUE   // 无论身处何地都能收到天气警报
```

**效果**: 矿工**在任何位置都能感知天气**——即便在矿洞深处/室内，熔岩地的灰烬风暴 (ash storm) 警报也会照常送达。野外保命关键特质（幽灵观察者同样自带此特质）。

#### 7.5 邮件礼物

**无**（`shaft_miner.dm` 未定义 mail_goodies——矿工的奖励在矿石里，不在邮箱里）。

#### 7.6 Nova 扩展

- **套装兑换券 (suit_voucher)**: 背包新增（`//NOVA EDIT ADDITION`），可在采矿订单台兑换 **SEVA 防护服套装** 或 **探险家套装**（`modular_nova/modules/SEVA_suit/code/suit_voucher.dm` + `mining_vendor_additions`）。
- Nova 还扩展了采矿兑换券内容：生存舱套装附**Kheiral 手环**、碎骨者改造件套装、胡萝卜星兔子征召套装等（`voucher_sets.dm`）。

#### 7.7 一句话总结

**矿工 = 矿机军团芯片（TRAIT_ROCK_STONER）+ 风暴感知 + 满配野外生存包 + 巨兽皮甲**

要么当矿工，要么当"带矿机打仗的隧道鼠"——芯片台词已经替你选好了。

---

### 八、货运大猩猩 Cargo Gorilla

**代码**: `code/modules/jobs/job_types/station_trait/cargo_gorilla.dm`（实际生效版；`code/modules/jobs/job_types/cargo_gorilla.dm` 为同内容早期版，两份都被 .dme 编译，后者覆盖）

#### 8.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 0总/0出生（**站台特质职业**，靠大厅按钮报名）|
| 上级 | 军需官 (QM) |
| 部门 | 货运部 |
| 种族 | 大猩猩 (`/mob/living/basic/gorilla/cargorilla`) |
| 工资 | **有工资的大猩猩**：出生即获得银行账户 + 3 张起始支票（免费支付）|
| 邮件礼物 | 香蕉 ×1 |
| RPG称号 | Beast of Burden（负重之兽）|
| 反叛 | **禁止反派** (JOB_ANTAG_BLACKLISTED) |
| 报到 | 到岗广播 (JOB_ANNOUNCE_ARRIVAL) + 新玩家可加入 |

**职责描述**: 「协助补给部门搬运货物，并处理不受欢迎的水果。」
(Assist the supply department by moving freight and disposing of unwanted fruits.)

#### 8.2 站台特质机制

**代码**: `job_traits.dm` L72-102（`/datum/station_trait/job/cargorilla`）

```dm
name = "Cargo Gorilla"
button_desc = "Sign up to become the Cargo Gorilla, a peaceful shepherd of boxes."
show_in_report = FALSE  // 选择性注意力测试。你看到大猩猩了吗？
```

- 随机站台特质（weight 1），**不出现在报告里**——"选择性注意力测试。你看到大猩猩了吗？"
- 生效时**删除货运部的树懒 (cargo_sloth) 和里普利机甲 (cargo_ripley)**，用大猩猩取而代之（"猴子搬箱子，机器人时代结束了"）。
- 大厅出现**报名按钮**（gorilla_on/gorilla_off 图标），玩家报名后以 `total_positions` 动态分配。

#### 8.3 大猩猩本体

**代码**: `gorilla.dm` L152-165 + `gorilla_accessories.dm`

```dm
/mob/living/basic/gorilla/cargorilla
    name = "Cargorilla"
    desc = "Cargo's pet gorilla. They seem to have an 'I love Mom' tattoo."
    maxHealth = 200 / health = 200          // 200血皮糙肉厚
    faction = list(FACTION_NEUTRAL, FACTION_MONKEY, FACTION_JUNGLE)
    ai_controller = null                     // 纯玩家操控，无AI
/Initialize:
    ADD_TRAIT(src, TRAIT_PACIFISM, INNATE_TRAIT)      // 天生和平主义
    AddComponent(/datum/component/crate_carrier)      // 板条箱搬运工组件
```

**crate_carrier 组件**（`crate_carrier.dm`）: 最多同时**搬 3 个板条箱**（默认 crate_limit=3，含人的箱子太重搬不动）；空手点击板条箱抱起、点击地面放下、死亡时全掉落；查看时会显示"它正搬着 N 个箱子"。

**出生流程** (`after_spawn`):
- 从 `strings/names/cargorilla.txt` 随机取名（如经典猩猩名）
- 建立银行账户，直接发 3 张起始支票——**有工资的大猩猩**（"Gorilla with a wage, what's he buyin?"）
- 发放**大猩猩专用ID卡**（`/obj/item/card/id/advanced/cargo_gorilla`，描述："为大猩猩体型的货运技师准备的大猩猩尺寸ID"，权限 = 货运技师 trim）
- `mind.special_roles += "Cargorilla"`

#### 8.4 技能芯片

**无**（大猩猩不需要芯片——它有肌肉）。

#### 8.5 装备详情

| 部位 | 物品 |
|---|---|
| 手中 | 大猩猩ID卡 (card/id/advanced/cargo_gorilla) |
| 身体 | 天生 200 血 + 和平主义 + 3箱搬运力 |
| 名字 | 随机大猩猩名 |

#### 8.6 特质 (Traits)

```dm
mind_traits = 无（但 special_roles = "Cargorilla"）
liver_traits = 无
TRAIT_PACIFISM（天生内置，INNATE_TRAIT）
```

#### 8.7 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 香蕉 (food/grown/banana) | 1 | 唯一礼物 — 应得的 |

#### 8.8 Nova 扩展

整个职业就是 Nova 体系的**站台特质玩法**（cargo_gorilla 由 upstream 并入，两份文件均带 i18n codemod 中文注释）。它替换了货运部原有的树懒+机甲，属于"看运气刷出来的快乐岗位"。

#### 8.9 一句话总结

**货运大猩猩 = 200血和平主义搬箱工 + 3箱同时搬运 + 自带工资卡 + 香蕉邮件**

有工资、有ID、有纹身（"I love Mom"）的货运部编外吉祥物——只是偶尔要处理掉"不受欢迎的水果"。

---


| 职业 | 部门 | 岗位数 | 工资 | 技能芯片 | 心智特质 | 肝特质 |
|---|---|---|---|---|---|---|
| 科研部长 RD | 科研+指挥 | 1 | 指挥级 | ✅ R.D.S.P.L.X. + 机器人芯片 | 快速打领带+高额赎金 | 皇家代谢（科学家肝被Nova移除）|
| 科学家 | 科研 | 5总/3生 | 船员级 | ❌ | 无 | 科学家肝（被Nova移除）|
| 机器人学家 | 科研 | 2 | 船员级 | ✅ 赛博格电路 | 无 | 无 |
| 比特跑者 | 货运 | 3 | 船员级 | ❌ | 无 | 无 |
| 军需官 QM | 货运+指挥 | 1 | 指挥级 | ❌ | 快速打领带+高额赎金 | 皇家代谢 |
| 货运技师 | 货运 | 5总/3生 | 船员级 | ❌ | 无 | 无 |
| 矿工 | 货运 | 3 | 船员级 | ✅ 隧道鼠 | 风暴感知 | 无 |
| 货运大猩猩 | 货运 | 特质报名 | 有工资 | ❌ | 无 | 无（内置和平主义）|


---

## 第四篇 · 服务部



**覆盖职业**: 调酒师 / 厨师 / 植物学家 / 清洁工 / 小丑 / 哑剧演员 / 馆长 / 牧师 / 囚犯

**主代码**: `code/modules/jobs/job_types/{bartender,cook,botanist,janitor,clown,mime,curator,prisoner}.dm` + `code/modules/jobs/job_types/chaplain/`（目录）+ `code/datums/id_trim/jobs.dm`
**技能芯片**: `code/modules/library/skill_learning/job_skillchips/{chef,clown,janitor}.dm` + `code/modules/library/skill_learning/generic_skillchips/misc.dm` + `code/modules/library/skill_learning/job_skillchips/_job.dm`
**Nova 覆写**: `modular_nova/master_files/code/modules/jobs/job_types/{mime,clown,chaplain,botanist}.dm` + `modular_nova/master_files/code/modules/jobs/prisoner.dm`

> 说明: 全部 9 个职业同属 `/datum/job_department/service`（服务部），上级均为 `SUPERVISOR_HOP`（人事部长），工资均为 `PAYCHECK_CREW` 档（囚犯除外，为 `PAYCHECK_LOWER`），发薪账户均为 `ACCOUNT_SRV`（服务部账目），经验类型均为 `EXP_TYPE_CREW`（船员经验），下文不再逐职业重复。

---

### 一、调酒师 Bartender

**代码**: `code/modules/jobs/job_types/bartender.dm` + `code/modules/library/skill_learning/generic_skillchips/misc.dm`（drunken_brawler 芯片）

#### 1.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 1总/1出生 |
| 上级 | 人事部长 |
| 部门 | 服务部 |
| 工资等级 | CREW（船员级） |
| 发薪部门 | 服务部账目 |
| 经验类型 | 船员经验 |
| 配置标签 | "BARTENDER" |
| 赏金类型 | CIV_JOB_DRINK（调酒赏金） |
| RPG称号 | Tavernkeeper（酒馆老板） |
| 家族遗物 | 抹布 `/obj/item/rag` / 礼帽 `/obj/item/clothing/head/hats/tophat` / 调酒壶 `/obj/item/reagent_containers/cup/glass/shaker` |
| 血浆人制服 | `/datum/outfit/plasmaman/bar` |
| 职业成就 | 调酒师游客评分 `bartender_tourist_score`（按酒吧累计收入 `SSrestaurant.all_venues[/datum/venue/bar]` 计算） |

**特色机制**: `post_equip` 中若角色年龄小于法定饮酒年龄（`AGE_MINOR`），会把 ID 上的登记年龄强制改为 `AGE_MINOR`——调酒师未成年？先把你"改成年"再说。

#### 1.2 技能芯片 — F0RC3 4DD1CT10N skillchip（醉酒格斗）

**代码**: `generic_skillchips/misc.dm` L124-132

```dm
/obj/item/skillchip/drunken_brawler
  name = "F0RC3 4DD1CT10N skillchip"
  auto_traits = list(TRAIT_DRUNKEN_BRAWLER)
  skill_name = "Drunken Unarmed Proficiency"
  skill_description = "When intoxicated, you gain increased unarmed effectiveness."
```

调酒师出生自带 1 枚芯片（`skillchips = list(/obj/item/skillchip/drunken_brawler)`），职业芯片槽位占用 2（`_job.dm`）。

#### TRAIT_DRUNKEN_BRAWLER — 醉酒格斗

| 触发场景 | 效果 |
|---|---|
| **醉酒射击** (`drunk.dm` L137) | 喝醉时**开枪无散布惩罚**——源码注释原文: "excusing the bartender, because shotgun"（原谅调酒师，因为霰弹枪） |
| **被擒挣脱** (`drunk.dm` L147) | 被抓住时挣脱判定 `GRAB_STAT_EFFECTIVE_STATE + 1`，挣脱失败伤害 = 自身(brute+burn)/10，钳制在 3~20 |
| **近战命中** (`_species.dm` L836/857/877) | 只要处于醉酒状态（不要求喝满），**徒手/近战攻击永不落空**；对被击倒/踉跄目标攻击也必中 |

**直观理解**: 醉得越狠，拳头越准、枪越稳、越难被抓住。酒吧吧台后的自保被动。

#### 1.3 装备部位表

**代码**: `/datum/outfit/job/bartender`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/bartender` — 酒吧、矿物仓库、服务部、剧场、**武器库** (ACCESS_WEAPONS)；额外: 水培+厨房 |
| 脖子 | 领结 `/obj/item/clothing/neck/bowtie` |
| 制服 | 服务部衬衫西裤 `/obj/item/clothing/under/costume/buttondown/slacks/service` |
| 外套 | **装甲背心** `/obj/item/clothing/suit/armor/vest`（服务部唯一自带护甲的民用职业之一） |
| 腰带 | 调酒师PDA `/obj/item/modular_computer/pda/bar` |
| 耳机 | 服务频道 `/obj/item/radio/headset/headset_srv` |
| 眼睛 | **试剂检测墨镜** `/obj/item/clothing/glasses/sunglasses/reagent`（看得到杯内化学成份） |
| 鞋子 | 系带皮鞋 `/obj/item/clothing/shoes/laceup` |
| 技能芯片 | `/obj/item/skillchip/drunken_brawler`（见上） |

#### 背包内容

```
/obj/item/storage/box/beanbag = 1   — 豆袋弹盒（配合武器库权限用的非致命弹药）
```

#### 1.4 肝特质 — TRAIT_BARTENDER_METABOLISM（调酒师代谢）

**代码**: `bartender.dm` L18 + `alcohol_reagents.dm` L3302

| 项目 | 内容 |
|---|---|
| 肝特质 | `liver_traits = list(TRAIT_BARTENDER_METABOLISM)` |
| 专属饮品 | **Bartender's Handshake（调酒师握手礼）** — 极苦鸡尾酒，`boozepwr = 50` |
| 调酒师喝 | 每 tick 治疗 **1点 brute + 1点 burn**（`heal_bodypart_damage(brute=1, burn=1)`） |
| 其他人喝 | 产生 **2 点 disgust（恶心值）** — "用最苦的两种原料考验同行品味" |
| 可读性 | 拥有 TRAIT_ENTRAILS_READER 的人检查肝脏可读出此特质 |

**直观理解**: 调酒师肝 = 免费自愈被动，但只对"同行暗号酒"有效；外人喝了只会干呕。

#### 1.5 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 独头弹盒 `/obj/item/storage/box/rubbershot` | 30 | 最常见（配合武器库权限） |
| 小丑之泪酒 `/obj/item/reagent_containers/cup/bottle/clownstears` | 10 | 稀有 |
| 等离子矿锭 `/obj/item/stack/sheet/mineral/plasma` | 10 | 稀有 |
| 铀矿锭 `/obj/item/stack/sheet/mineral/uranium` | 10 | 稀有 |

#### 1.6 Nova 扩展

无专门覆写（Nova 未改动调酒师本体；其语音/外观由公共模块处理）。

#### 1.7 一句话总结

**调酒师 = 醉拳大师 + 护甲背心 + 武器库权限 + 试剂墨镜 + 握手礼自愈肝**

喝得越醉越能打、开枪越稳；装甲背心和武器库权限让吧台后面那位其实是最不好惹的民用岗。

---

### 二、厨师 Cook / Chef

**代码**: `code/modules/jobs/job_types/cook.dm` + `code/modules/library/skill_learning/job_skillchips/chef.dm`

#### 2.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 2总/2出生 |
| 上级 | 人事部长 |
| 部门 | 服务部 |
| 工资等级 | CREW |
| 发薪部门 | 服务部账目 |
| 经验类型 | 船员经验 |
| 配置标签 | "COOK" |
| 替代职称 | **JOB_CHEF（主厨）** — `alternate_titles` |
| 赏金类型 | CIV_JOB_CHEF（厨师赏金） |
| RPG称号 | Tavern Chef（酒馆主厨） |
| 家族遗物 | 盐瓶 `/obj/item/reagent_containers/condiment/saltshaker` / 擀面杖 `/obj/item/kitchen/rollingpin` / 厨师帽 `/obj/item/clothing/head/utility/chefhat` |
| 血浆人制服 | `/datum/outfit/plasmaman/chef` |
| 职业成就 | 厨师游客评分 `chef_tourist_score`（按餐厅累计收入 `SSrestaurant.all_venues[/datum/venue/restaurant]` 计算） |
| 脱敏基础值 | `desensitized_base = DESENSITIZED_THRESHOLD`（屠夫：对血腥/解剖场景脱敏，杀肉不恶心） |

**双厨师机制**（`pre_equip` + `cooks` 计数器）: 本站第一个出生的是 **Chef（主厨）**——穿厨师外套+厨师帽，ID 职称 `JOB_CHEF`；第二个及以后出生的是 **Cook（厨子）**——改穿围裙 `/obj/item/clothing/suit/apron/chef` + 米姆软帽 `/obj/item/clothing/head/soft/mime`，ID 职称回落到 `JOB_COOK`。

#### 2.2 技能芯片 — B0RK-X3 skillchip（厨房近战武术）

**代码**: `job_skillchips/chef.dm`（全 23 行）

```dm
/obj/item/skillchip/job/chef
  name = "B0RK-X3 skillchip"        // bork bork bork
  desc = "...This biochip faintly smells of garlic..."
  skill_name = "Close Quarters Cooking"
  skill_description = "A specialised form of self defence, developed by skilled
    sous-chef de cuisines. No man fights harder than a chef to defend his kitchen."
  var/datum/martial_art/cqc/under_siege/style
```

厨师出生自带 1 枚芯片（`skillchips = list(/obj/item/skillchip/job/chef)`）。这不是 auto_traits 型芯片，而是**激活时教武术、移除时遗忘武术**的动态芯片:

| 项目 | 内容 |
|---|---|
| 芯片名 | B0RK-X3（闻起来有股大蒜味） |
| 激活 | `on_activate` → `style.teach(user)` 教授 CQC 武术 |
| 失活 | `on_deactivate` → `style.unlearn(user)` 遗忘武术 |
| 武术 | `/datum/martial_art/cqc/under_siege`（"厨房被围困时"的近身格斗 CQC 变体） |
| 生效范围 | `refresh_valid_areas()` — **只在厨房区域**可用踢击、摔投、擒拿 |

**直观理解**: 芯片 = 把 CQC 武术刻进脑子，但只在厨房范围触发——"没人比厨师更拼命保卫自己的厨房"。芯片被移除（脑手术）后武术立即遗忘。

#### 2.3 装备部位表

**代码**: `/datum/outfit/job/cook`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/cook/chef`（主厨）→ 厨房、矿物仓库、**停尸房** (ACCESS_MORGUE)、服务部；额外: 酒吧+水培。副厨为 `/datum/id_trim/job/cook` |
| 制服 | 服务部衬衫西裤 |
| 外套 | 厨师外套 `/obj/item/clothing/suit/toggle/chef`（副厨: 围裙 `apron/chef`） |
| 头 | 厨师帽 `head/utility/chefhat`（副厨: 米姆软帽 `head/soft/mime`） |
| 面罩 | **意大利假胡子** `/obj/item/clothing/mask/fakemoustache/italian` |
| 腰带 | 厨师PDA `/obj/item/modular_computer/pda/cook` |
| 耳机 | 服务频道 `headset_srv` |
| 技能芯片 | `/obj/item/skillchip/job/chef`（见上） |

#### 背包内容

```
/obj/item/choice_beacon/ingredient = 1   — 食材传送信标（Sophronia 广播公司"Plasteel Chef"食材箱，任选一箱主题食材）
/obj/item/sharpener = 1                  — 磨刀石（给菜刀开刃）
```

#### 2.4 肝特质 — TRAIT_CULINARY_METABOLISM（烹饪代谢）

**代码**: `cook.dm` L19 + `food_reagents.dm` L597

| 项目 | 内容 |
|---|---|
| 肝特质 | `liver_traits = list(TRAIT_CULINARY_METABOLISM)` |
| 专属食材 | **大蒜 (garlic)** — 厨师吃大蒜时每 tick 有 **10% 概率治疗 3.34 brute + 3.34 burn**（`heal_bodypart_damage(3.34, 3.34)`） |
| 副作用 | 大蒜代谢慢，治疗节奏比香蕉/糖霜更慢（源码注释） |
| 可读性 | TRAIT_ENTRAILS_READER 持有者可读出 |

**直观理解**: 厨师肝 = 大蒜就是移动急救包。被砍了？回厨房啃两瓣蒜。

#### 2.5 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 随机食材箱 `/obj/item/storage/box/ingredients/random` | 40 | 最常见 |
| 焦糖酱 `/obj/item/reagent_containers/cup/bottle/caramel` | 7 | |
| 面粉 `/obj/item/reagent_containers/condiment/flour` | 7 | |
| 大米 `/obj/item/reagent_containers/condiment/rice` | 7 | |
| 番茄酱 `/obj/item/reagent_containers/condiment/ketchup` | 7 | |
| 芥末 `/obj/item/reagent_containers/condiment/mustard` | 7 | |
| 酶 `/obj/item/reagent_containers/condiment/enzyme` | 7 | |
| 豆奶 `/obj/item/reagent_containers/condiment/soymilk` | 7 | |
| 汤勺 `/obj/item/kitchen/spoon/soup_ladle` | 6 | |
| 夹子 `/obj/item/kitchen/tongs` | 6 | |
| 厨刀 `/obj/item/knife/kitchen` | 4 | |
| 屠刀 `/obj/item/knife/butcher` | 2 | 最稀有 |

#### 2.6 Nova 扩展

无专门覆写（Nova 未改动厨师本体）。

#### 2.7 一句话总结

**厨师 = 厨房限定CQC武术 + 大蒜自愈肝 + 屠夫脱敏 + 主厨/副厨双职称 + 食材信标**

第一个出生是主厨（厨师外套），第二个是副厨（围裙+软帽）；芯片一插，厨房就是你的格斗场。

---

### 三、植物学家 Botanist

**代码**: `code/modules/jobs/job_types/botanist.dm` + `modular_nova/master_files/code/modules/jobs/job_types/botanist.dm`

#### 3.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 3总/**2出生** |
| 上级 | 人事部长 |
| 部门 | 服务部 |
| 工资等级 | CREW |
| 发薪部门 | 服务部账目 |
| 经验类型 | 船员经验 |
| 配置标签 | "BOTANIST" |
| 赏金类型 | CIV_JOB_GROW（种植赏金） |
| RPG称号 | Gardener（园丁） |
| 家族遗物 | 锄头 `/obj/item/cultivator` / 木制浇水壶 `/obj/item/reagent_containers/cup/watering_can/wood` / 蜜蜂玩偶 `/obj/item/toy/plush/beeplushie` |
| 血浆人制服 | `/datum/outfit/plasmaman/botany` |

**特色**: 3 个岗位位但只有 2 个出生位（第 3 位留给中后期加入的玩家）。**无 mind_traits、无 liver_traits、无技能芯片**——纯工具人。

#### 3.2 装备部位表

**代码**: `/datum/outfit/job/botanist`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/botanist` — 水培、矿物仓库、服务部；额外: 酒吧+厨房+停尸房 |
| 制服 | 水培制服 `/obj/item/clothing/under/rank/civilian/hydroponics` |
| 外套 | 围裙 `/obj/item/clothing/suit/apron` |
| 背挂 | **植物分析仪** `/obj/item/plant_analyzer`（suit_store 位，扫植物看基因数据） |
| 腰带 | 植物学家PDA `/obj/item/modular_computer/pda/botanist` |
| 耳机 | 服务频道 `headset_srv` |
| 手套 | **园艺皮手套** `/obj/item/clothing/gloves/botanic_leather`（防植物刺伤/防酸） |

#### 背包内容

出生**不带背包**——只有上面列的部位，靠储物柜领装备。（`backpack/satchel/duffelbag/messenger` 四件套均已指定，但未列入出生携带。）

| 背包款式 | 路径 |
|---|---|
| 背包 | `/obj/item/storage/backpack/botany` |
| 挎包 | `/obj/item/storage/backpack/satchel/hyd` |
| 行李袋 | `/obj/item/storage/backpack/duffelbag/hydroponics` |
| 斜挎包(Nova) | `/obj/item/storage/backpack/messenger/hyd` |

#### 3.3 特质分析

| 类型 | 内容 |
|---|---|
| mind_traits | 无 |
| liver_traits | 无 |
| 技能芯片 | 无 |

植物学家是纯功能职业：无任何血脉被动，一切收益来自植物本身（基因编辑、变异、产果）。

#### 3.4 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 诱变剂 `/obj/item/reagent_containers/cup/bottle/mutagen` | 20 | 植物变异核心药 |
| 硝石 `/obj/item/reagent_containers/cup/bottle/saltpetre` | 20 | 促生长 |
| 二乙胺 `/obj/item/reagent_containers/cup/bottle/diethylamine` | 20 | 促生长 |
| **花枪** `/obj/item/gun/energy/floragun` | 10 | 能量花枪（发射生长/毒花粉） |
| 高级浇水壶 `/obj/item/reagent_containers/cup/watering_can/advanced` | 10 | 大容量 |
| 稀有种子随机 `/obj/effect/spawner/random/food_or_drink/seed_rare` | 5 | 强种子，源码注释"慎用" |
| 蜜蜂猴方块 `/obj/item/food/monkeycube/bee` | 2 | 召唤蜜蜂猴 |

#### 3.5 Nova 扩展

**代码**: `modular_nova/.../job_types/botanist.dm`

Nova 给植物学家增加了 `messenger = /obj/item/storage/backpack/messenger/hyd`（水培斜挎包款式）。

#### 3.6 一句话总结

**植物学家 = 园艺手套 + 植物分析仪 + 花枪 + 2/3出生位 + 纯工具人（无任何特质）**

服务部唯一没有血脉被动的职业——一切靠基因操作台说话。

---

### 四、清洁工 Janitor

**代码**: `code/modules/jobs/job_types/janitor.dm` + `code/modules/library/skill_learning/job_skillchips/janitor.dm` + `code/modules/library/skill_learning/generic_skillchips/misc.dm`（disposals 芯片）

#### 4.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 2总/**1出生** |
| 上级 | 人事部长 |
| 部门 | 服务部 |
| 工资等级 | CREW |
| 发薪部门 | 服务部账目 |
| 经验类型 | 船员经验 |
| 配置标签 | "JANITOR" |
| RPG称号 | Groundskeeper（园地管理员） |
| 职业音调 | `job_tone = "slip"`（🔊"滑倒"） |
| 家族遗物 | 拖把 `/obj/item/mop` / 警示锥 `/obj/item/clothing/suit/caution` / 水桶 `/obj/item/reagent_containers/cup/bucket` / 香皂说明纸 `/obj/item/paper/fluff/stations/soap` |
| 血浆人制服 | `/datum/outfit/plasmaman/janitor` |

**特色机制**: `pre_equip` 检测 **GARBAGEDAY（垃圾日）节假日**——当天清洁工出生额外携带**左轮手枪** `/obj/item/gun/ballistic/revolver` + **.357 快速装弹器** `/obj/item/ammo_box/speedloader/c357`（垃圾日暴乱自卫）。

#### 4.2 技能芯片 — 双芯片（全服务部唯一）

清洁工出生带 **2 枚技能芯片**（`skillchips = list(/obj/item/skillchip/job/janitor, /obj/item/skillchip/disposals)`），两个都是 auto_traits 型。

#### 4.2.1 CL34NM4ST.R skillchip — TRAIT_CLEANBOT_WHISPERER（清洁机器人低语者）

**代码**: `job_skillchips/janitor.dm` + `cleanbot_ai.dm` L182-190

```dm
/obj/item/skillchip/job/janitor
  name = "CL34NM4ST.R skillchip"
  auto_traits = list(TRAIT_CLEANBOT_WHISPERER)
  skill_name = "Voice Of The Voiceless"
  skill_description = "Gain the affection of all thankless, hardworking cleanbots on the station."
```

| 项目 | 内容 |
|---|---|
| 芯片名 | CL34NM4ST.R（CLEANMASTER 的 leetspeak） |
| 特质 | TRAIT_CLEANBOT_WHISPERER |
| 效果 | cleanbot AI 在视野内扫描时**只把拥有此特质的人当作"盟友"**（`cleanbot_ai.dm`: 非此特质的人类直接跳过）——清洁机器人会主动跟随你、优先响应你 |

**直观理解**: 全站的清洁机器人认你当老大。别人使唤不动的扫地机器人，你路过就跟着你走。

#### 4.2.2 T4RG3T.bin skillchip — TRAIT_THROWINGARM（投掷臂）

**代码**: `generic_skillchips/misc.dm` L159-167

```dm
/obj/item/skillchip/disposals
  name = "T4RG3T.bin skillchip"
  auto_traits = list(TRAIT_THROWINGARM)
  skill_name = "Dauntless Disposaler"
  skill_description = "You have an uncanny ability to perfectly land every toss into disposal units."
```

| 项目 | 内容 |
|---|---|
| 特质 | TRAIT_THROWINGARM |
| 投掷距离 | 投掷距离 **+2**（`living_item_handling.dm` L77） |
| 垃圾处理器 | 扔进垃圾管道**必定命中**（普通人只有 75% 概率 `bin.dm` L570） |

**直观理解**: 远程精准把垃圾扔进处理口，扔手雷/扔道具也白赚 2 格距离。

#### 4.3 装备部位表

**代码**: `/datum/outfit/job/janitor`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/janitor` — 清洁工、**维护隧道** (ACCESS_MAINT_TUNNELS)、矿物仓库、服务部 |
| 制服 | 清洁工制服 `/obj/item/clothing/under/rank/civilian/janitor` |
| 腰带 | 清洁工PDA `/obj/item/modular_computer/pda/janitor` |
| 耳机 | 服务频道 `headset_srv` |
| 技能芯片 | `skillchip/job/janitor` + `skillchip/disposals`（见上） |

#### 背包内容

```
/obj/item/access_key = 1   — 万能钥匙/门禁密钥（维护隧道通行用）
```

（垃圾日额外: 左轮 + .357 弹匣，见 4.1）

#### 4.4 特质分析

| 类型 | 内容 |
|---|---|
| mind_traits | 无 |
| liver_traits | 无（肝脏无特殊代谢） |
| 技能芯片 | **2 枚**（全服务部唯一双芯片职业，见 4.2） |

#### 4.5 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 清洁手雷 `/obj/item/grenade/chem_grenade/cleaner` | 30 | 最常见（大面积清洁） |
| 混合灯泡盒 `/obj/item/storage/box/lights/mixed` | 20 | |
| 换灯器 `/obj/item/lightreplacer` | 10 | 稀有（一键换全站灯泡） |

#### 4.6 Nova 扩展

无专门覆写（Nova 未改动清洁工本体）。

#### 4.7 一句话总结

**清洁工 = 双技能芯片（清洁机器人老大 + 投掷臂） + 维护隧道权限 + 万能钥匙 + 垃圾日左轮**

服务部唯一自带双芯片的职业，也是少数出生即拥有维护隧道通行权的民用岗；垃圾日别惹他。

---

### 五、小丑 Clown

**代码**: `code/modules/jobs/job_types/clown.dm` + `code/modules/library/skill_learning/job_skillchips/clown.dm` + `modular_nova/master_files/code/modules/jobs/job_types/clown.dm`

#### 5.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 1总/1出生 |
| 上级 | 人事部长 |
| 部门 | 服务部 |
| 工资等级 | CREW |
| 发薪部门 | 服务部账目 |
| 经验类型 | 船员经验 |
| 配置标签 | "CLOWN" |
| RPG称号 | Jester（弄臣） |
| 职业音调 | `job_tone = "honk"`（🔊"HONK"） |
| 家族遗物 | 金喇叭 `/obj/item/bikehorn/golden` |
| 血浆人制服 | `/datum/outfit/plasmaman/clown` |
| 出生改名 | `after_spawn` 强制套用 `clown_names` 小丑名（ID 同步更新） |
| 愚人节彩蛋 | 4月1日出生的小丑血型强制改为 **BLOOD_TYPE_CLOWN（小丑血）** |

**特色机制**（`post_equip`，视觉预览除外）:
1. 强制改名为随机小丑名；
2. `H.dna.add_mutation(/datum/mutation/clumsy, MUTATION_SOURCE_CLOWN_CLUMSINESS)` — **注入笨拙突变**（源标记为"小丑笨拙"，可被基因清理）；
3. `ADD_TRAIT(H, TRAIT_CLOWN_ENJOYER, INNATE_TRAIT)` — 天生小丑爱好者；
4. `H.add_faction(FACTION_CLOWN)` — 加入**小丑阵营**（ClownOps 友好、部分敌对生物不攻击）。

**站内彩蛋**: `STATION_TRAIT_BANANIUM_SHIPMENTS` 站内特性开启时，小丑背包多 **5 片香蕉合金** `/obj/item/stack/sheet/mineral/bananium/five`。

#### 5.2 技能芯片 — B@L00NY skillchip（气球瑜伽术）

**代码**: `job_skillchips/clown.dm`（全 10 行）

```dm
/obj/item/skillchip/job/clown
  name = "B@L00NY skillchip"
  desc = "...several terabytes of uncannily religious, Honkmother-praising guides
    on how to reshape balloons into silly animals."
  auto_traits = list(TRAIT_BALLOON_SUTRA)
  skill_name = "Balloon Sutra"
  skill_description = "Learn the ancient Honkmotherian arts of balloon-sutra."
```

> ⚠️ **勘误说明**: 有资料称小丑有"3 枚芯片"，但本代码库源码显示小丑出生**仅 1 枚**职业芯片（`skillchips = list(/obj/item/skillchip/job/clown)`）。其余"芯片级"能力（笨拙突变、悲催长号植入体）不是技能芯片。以源码为准。

#### TRAIT_BALLOON_SUTRA — 气球瑜伽术

| 触发场景 | 效果 |
|---|---|
| **气球动物** (`toys.dm` L179) | 手持两根**不同颜色**的长气球互相组合 → 拧出气球动物（蜥蜴/史莱姆/飞蛾/以太体/等离子人等多色组合表） |
| **气球锤** (`clown_items.dm` L329) | 可制作/使用气球锤 `balloon_mallet`；对理智值低的受害者伤害更高（`force` 8/4 随 SANITY 档位变化），并施加"羞辱"心情事件 |
| **额外文本** | 检查气球锤时能看到专属描述文本 |

**直观理解**: 芯片里装了几 TB 的"洪克母神"气球教学——两根气球一拧就是一只动物，小丑的快乐源泉。

#### 5.3 装备部位表

**代码**: `/datum/outfit/job/clown`

| 部位 | 物品 |
|---|---|
| ID | **彩虹高级ID卡** `/obj/item/card/id/advanced/rainbow` |
| ID权限 | `/datum/id_trim/job/clown` — 矿物仓库、服务部、剧场、**武器库** (ACCESS_WEAPONS) |
| 制服 | 小丑制服 `/obj/item/clothing/under/rank/civilian/clown` |
| 鞋 | **小丑大鞋** `/obj/item/clothing/shoes/clown_shoes`（踩地响） |
| 面罩 | 小丑防毒面具 `/obj/item/clothing/mask/gas/clown_hat` |
| 腰带 | 小丑PDA `/obj/item/modular_computer/pda/clown` |
| 耳机 | 服务频道 `headset_srv` |
| 左口袋 | 自行车喇叭 `/obj/item/bikehorn` |
| 植入体 | **悲催长号植入体** `/obj/item/implant/sad_trombone`（死亡/装死时播放 sad trombone 音效） |
| 技能芯片 | `/obj/item/skillchip/job/clown`（见上） |

#### 背包内容

```
/obj/item/stamp/clown = 1                          — 小丑印章（盖"HONK"红章）
/obj/item/reagent_containers/spray/waterflower = 1 — 水枪花（喷水整蛊）
/obj/item/food/grown/banana = 1                    — 香蕉（地上滑人）
/obj/item/instrument/bikehorn = 1                  — 自行车喇叭
/obj/item/storage/box/balloons = 1                 — 气球盒
```

#### 背包款式

| 款式 | 路径 |
|---|---|
| 背包 | `/obj/item/storage/backpack/clown` |
| 挎包 | `/obj/item/storage/backpack/clown` |
| 行李袋 | `/obj/item/storage/backpack/duffelbag/clown`（源码注释: "strangely has a duffel" 竟然有行李袋） |
| 斜挎包(Nova) | `/obj/item/storage/backpack/messenger/clown` |
| 生存盒 | `/obj/item/storage/box/survival/hug`（拥抱主题） |
| 变色龙配件 | `/obj/item/stamp/clown` |
| MODsuit 变体 | `/datum/outfit/job/clown/mod` — **Cosmohonk MOD 装甲** + 氧气瓶（太空小丑） |

#### 5.4 特质分析 — mind_traits + liver_traits（重点）

#### 5.4.1 mind_traits = list(TRAIT_NAIVE) — 天真

**代码**: `clown.dm` L17 + `examine.dm` L34、`chasm.dm` L180、`mood_events/death.dm` L99

| 场景 | 效果 |
|---|---|
| **检查尸体** (`examine.dm` L34) | 小丑**无法区分死亡与睡眠**——检查尸体显示"看起来像睡着了"(just_sleeping)，不会显示死亡文本 |
| **深渊坠落** (`chasm.dm` L180) | 掉进深渊时有专属动画（`do_alert_animation` + 抖动 + 3 秒延迟），随后才判定坠亡——"Wow nice job" |
| **宠物死亡** (`befriend_petting.dm` L40) | 小丑看到死去的宠物/动物会以为是"在睡觉" |
| **死亡心情** (`mood_events/death.dm` L99) | 对他人死亡（非化成灰/非碎尸）触发悲伤心情——天真者更难过 |

**直观理解**: 天真 = 大脑自动过滤死亡讯息。尸体检查、死去的动物，在小丑眼里都只是"睡着了"——愚人节的愚。

#### 5.4.2 额外特质

| 特质 | 来源 | 效果 |
|---|---|---|
| TRAIT_CLOWN_ENJOYER | post_equip 天生赋予 | 小丑狂热者: 触发 HUD 粉丝标记（`DATA_HUD_FAN`）、佩戴小丑粉丝徽章有心情加成、脑伤创伤表把小丑职业映射为喜剧创伤 |
| 笨拙突变 | post_equip 注入 | 走路摔倒、物品脱手（经典小丑笨拙） |
| FACTION_CLOWN | post_equip 加入 | 小丑阵营: ClownOps 相关不敌对 |

#### 5.4.3 liver_traits = list(TRAIT_COMEDY_METABOLISM) — 喜剧代谢

**代码**: `clown.dm` L18 + `_liver.dm` L36-60、`banana.dm` L46、`drink_reagents.dm` L128、`alcohol_reagents.dm` L1362

| 场景 | 效果 |
|---|---|
| **肝脏物理** (`_liver.dm` L60) | 肝脏自带 squeak 组件——被打击/投掷/踩踏时发出**自行车喇叭声**（源码: "clown livers will act just like their bike horns"） |
| **吃香蕉** (`banana.dm` L46) | 香蕉对喜剧肝永远是 **FOOD_LIKED**（喜爱食物），除非有味觉缺失 |
| **香蕉汁** (`drink_reagents.dm` L128) | 喝香蕉汁每 tick 治疗 **0.5 brute + 0.5 burn**（和猿猴同款待遇） |
| **香蕉酒 Bananahonk** (`alcohol_reagents.dm` L1362) | 每 tick 治疗 **1 brute + 1 burn** |
| **药物幻觉** (`drug_reagents.dm` L976) | 嗑药幻觉固定为"看到安保人员"的 delusion 预设（喜剧肝的恐惧 = 保安） |

**直观理解**: 喜剧肝 = 会响的肝 + 香蕉医疗包。小丑吃香蕉=喝药，肝被揍=放屁声。

#### 5.5 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 香蕉 `/obj/item/food/grown/banana` | 100 | 最多（量超大） |
| 奶油派 `/obj/item/food/pie/cream` | 50 | 糊脸 |
| **太空小刀** `/obj/item/spess_knife` | 20 | 工程部对小丑的"开玩笑"礼物（笨拙小丑用） |
| 战斗小丑鞋 `/obj/item/clothing/shoes/clown_shoes/combat` | 10 | 稀有（小丑鞋装甲版） |
| **润滑油水枪花** `/obj/item/reagent_containers/spray/waterflower/lube` | 20 | 喷润滑油（滑倒全场） |
| **超润滑水枪花** `/obj/item/reagent_containers/spray/waterflower/superlube` | 1 | 极稀有（超润滑，天呐） |

#### 5.6 Nova 扩展

**代码**: `modular_nova/.../job_types/clown.dm`

- `vox_outfit = /datum/outfit/vox/clown` — Vox 种族小丑制服；
- 确认 `messenger = /obj/item/storage/backpack/messenger/clown` 斜挎包款式（与主代码一致）。

#### 5.7 一句话总结

**小丑 = 天真大脑 + 会响的肝 + 笨拙突变 + 气球瑜伽芯片 + 悲催长号植入体 + 彩虹ID（带武器库权限）**

活着是整蛊之王，死了也要用天真滤镜把尸体看成"睡着的"，最后让长号声送你上路——HONK!

---

### 六、哑剧演员 Mime

**代码**: `code/modules/jobs/job_types/mime.dm` + `modular_nova/master_files/code/modules/jobs/job_types/mime.dm`

#### 6.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 1总/1出生 |
| 上级 | 人事部长 |
| 部门 | 服务部 |
| 工资等级 | CREW |
| 发薪部门 | 服务部账目 |
| 经验类型 | 船员经验 |
| 配置标签 | "MIME" |
| 岗位描述 | "..."（哑剧式沉默描述） |
| RPG称号 | Fool（愚者） |
| 职业音调 | `job_tone = "silence"`（🔊"沉默"） |
| 家族遗物 | 法棍 `/obj/item/food/baguette` |
| 血浆人制服 | `/datum/outfit/plasmaman/mime` |
| 出生改名 | `after_spawn` 强制套用 `mime_names` 哑剧名 |
| 神之声 | `voice_of_god_power = 0.5`（几乎不能说话——"Why are you speaking"）；`voice_of_god_silence_power = 3`（沉默的力量极高） |

#### 6.2 核心机制 — 沉默誓言 (Vow of Silence)

**代码**: `mime.dm` L69-80

```dm
// Start our mime out with a vow of silence and the ability to break (or make) it
if(H.mind)
    var/datum/action/cooldown/spell/vow_of_silence/vow = new(H.mind)
    vow.Grant(H)
```

- 出生即获得 **沉默誓言法术**（`/datum/action/cooldown/spell/vow_of_silence`）；
- 立誓期间**不能说话**，打破誓言有惩罚、守誓有奖励（经典哑剧玩法）；
- `post_equip` 额外 `ADD_TRAIT(H, TRAIT_MIME_FAN, INNATE_TRAIT)` — 天生哑剧爱好者。

#### 哑剧法术书 — Guide to Dank Mimery（《酷哑剧指南》）

**代码**: `mime.dm` L82-134 — 出生背包 1 本，**一次性**（读完即销毁），可在 4 个法术中**任选 1 个**学习（Nova 版比原版多 1 个）:

| 法术 | 类型 | 效果 |
|---|---|---|
| 隐形墙 | `/datum/action/cooldown/spell/conjure/invisible_wall` | 凝结隐形墙壁 |
| 隐形椅 | `/datum/action/cooldown/spell/conjure/invisible_chair` | 凝结隐形椅子 |
| 隐形箱 | `/datum/action/cooldown/spell/conjure_item/invisible_box` | 凝结隐形箱子 |
| **米姆单色** (Nova新增) | `/datum/action/cooldown/spell/touch/mime_grayscale` | 触碰把目标变成黑白单色 |

**注意**: 读完书若还没有沉默誓言能力会一并补授；书中法术通过径向菜单选择，需手持书本且清醒。

#### 6.3 装备部位表

**代码**: `/datum/outfit/job/mime`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/mime` — 矿物仓库、服务部、剧场 |
| 制服 | 哑剧制服 `/obj/item/clothing/under/rank/civilian/mime` |
| 外套 | 背带裤 `/obj/item/clothing/suit/toggle/suspenders` |
| 头 | **法式贝雷帽** `/obj/item/clothing/head/beret/frenchberet` |
| 面罩 | 哑剧防毒面具 `/obj/item/clothing/mask/gas/mime`（黑白条纹） |
| 手套 | 白手套 `/obj/item/clothing/gloves/color/white` |
| 鞋 | 系带皮鞋 `shoes/laceup` |
| 腰带 | 哑剧PDA `/obj/item/modular_computer/pda/mime` |
| 耳机 | 服务频道 `headset_srv` |

#### 背包内容

```
/obj/item/book/granter/action/spell/mime/mimery = 1              — 《酷哑剧指南》（一次性学1个哑剧法术）
/obj/item/reagent_containers/cup/glass/bottle/bottleofnothing = 1 — 虚无之酒（"Nothing" 饮料，尝起来是虚无）
/obj/item/stamp/mime = 1                                          — 哑剧印章
```

#### 背包款式

| 款式 | 路径 |
|---|---|
| 背包 | `/obj/item/storage/backpack/mime` |
| 挎包 | `/obj/item/storage/backpack/mime` |
| 生存盒 | `/obj/item/storage/box/survival/hug/black`（黑色拥抱盒） |
| 变色龙配件 | `/obj/item/stamp/mime` |

#### 6.4 特质分析

| 类型 | 内容 |
|---|---|
| mind_traits | **源码中无 `mind_traits` 声明**（与小丑不同，哑剧没有天真特质） |
| post_equip 特质 | `TRAIT_MIME_FAN`（天生赋予）— 哑剧狂热者: HUD 粉丝标记、米姆粉丝徽章心情加成 |
| liver_traits | 无 |
| 技能芯片 | 无 |

> ⚠️ **勘误说明**: 有资料称小丑/哑剧演员有 `TRAIT_CLOWN_MENTALITY` 等 mind_traits——本代码库中**不存在 `TRAIT_CLOWN_MENTALITY` 这一特质**（全库搜索 0 结果）。小丑的 mind_traits 实为 `TRAIT_NAIVE`，哑剧演员则完全没有 mind_traits。

#### 6.5 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 法棍 `/obj/item/food/baguette` | 15 | 最常见（也是家族遗物） |
| 奶酪轮 `/obj/item/food/cheese/wheel` | 10 | |
| 虚无之酒 `/obj/item/reagent_containers/cup/glass/bottle/bottleofnothing` | 10 | |
| 《酷哑剧指南》 `/obj/item/book/granter/action/spell/mime/mimery` | 1 | 极稀有（再学一个哑剧法术） |

#### 6.6 Nova 扩展

**代码**: `modular_nova/.../job_types/mime.dm`

- `vox_outfit = /datum/outfit/vox/mime` — Vox 种族哑剧制服；
- 哑剧法术书新增 **米姆单色** 触碰法术（Nova 特色，见 6.2）。

#### 6.7 一句话总结

**哑剧演员 = 沉默誓言 + 一次性法术书（隐形墙/椅/箱/单色）+ 法棍 + 哑剧狂热者 + 无特质无芯片**

用沉默换力量：不说话的角色，却能把空气捏成墙和椅子；法棍是唯一的武器。

---

### 七、馆长 Curator

**代码**: `code/modules/jobs/job_types/curator.dm`

#### 7.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 1总/1出生 |
| 上级 | 人事部长 |
| 部门 | 服务部 |
| 工资等级 | CREW |
| 发薪部门 | 服务部账目 |
| 经验类型 | 船员经验 |
| 配置标签 | "CURATOR" |
| RPG称号 | Veteran Adventurer（老练冒险者） |
| 神之声沉默力 | `voice_of_god_silence_power = 3` |
| 家族遗物 | 钢笔 `/obj/item/pen/fountain` / 骰子 `/obj/item/storage/dice` |
| 血浆人制服 | `/datum/outfit/plasmaman/curator` |

**特色机制**:
- **唯一摄影师**（`pre_equip`）: 全站只有一个馆长能拿到**广播摄像机** `/obj/item/broadcast_camera`（static 标记 `cameraman_choosen`，谁先出生谁拿）；
- **全语言精通**（`post_equip`）: `grant_all_languages(source = LANGUAGE_CURATOR)` + `remove_blocked_language` — 出生即会**所有语言**且解除所有语言封锁（巴别塔根本关不住他）。

#### 7.2 特质分析 — mind_traits = list(TRAIT_TOWER_OF_BABEL)（巴别塔免疫）

**代码**: `curator.dm` L18 + `status_effects/debuffs/tower_of_babel.dm` L22-32

| 项目 | 内容 |
|---|---|
| mind_traits | `mind_traits = list(TRAIT_TOWER_OF_BABEL)` |
| 效果 | **免疫巴别塔状态效果**（`tower_of_babel.dm`: 有该特质则直接跳过 ADD_TRAIT）——即免疫"语言混乱"类 debuff（说话变乱码、听不懂别人） |
| 备注 | 巴别塔 debuff 一般来自特殊鱼类（`fish/types/rift.dm` L780/808）或语言干扰；馆长天生免疫 |
| 联动 | 配合 post_equip 的全语言授予，馆长是真正意义上"听得懂一切、乱不了心智"的学者 |

**直观理解**: 巴别塔想让全站人语言错乱时，馆长稳如老狗——所有语言照说，心智不受干扰。

| 类型 | 内容 |
|---|---|
| liver_traits | 无 |
| 技能芯片 | 无 |

#### 7.3 装备部位表

**代码**: `/datum/outfit/job/curator`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/curator` — **辅助基地** (ACCESS_AUX_BASE)、**图书馆** (ACCESS_LIBRARY)、矿物仓库、**采矿站** (ACCESS_MINING_STATION)、服务部 |
| 制服 | 馆长制服 `/obj/item/clothing/under/rank/civilian/curator` |
| 鞋 | 系带皮鞋 `shoes/laceup` |
| 腰带 | 馆长PDA `/obj/item/modular_computer/pda/curator` |
| 耳机 | **服务+娱乐双频道** `/obj/item/radio/headset/headset_srvent`（全服务部独一份的双频道耳机） |
| 左手 | **书袋** `/obj/item/storage/bag/books`（装书用） |
| 左口袋 | 绿色激光笔 `/obj/item/laser_pointer/green` |
| 右口袋 | **展示柜钥匙** `/obj/item/key/displaycase`（开图书馆展柜） |
| 配件 | 满配口袋保护套 `/obj/item/clothing/accessory/pocketprotector/full`（钢笔/铅笔插满） |

#### 背包内容

```
/obj/item/barcodescanner = 1                — 条码扫描器（图书馆借阅系统）
/obj/item/choice_beacon/hero = 1            — 英雄信标（Sophronia 广播"历史名人"服装套装，任选一套）
/obj/item/glassblowing/magnifying_glass     — 放大镜（NOVA EDIT: 玻璃工艺模块新增）
```

（出生若为全站首个馆长，额外获得广播摄像机 `broadcast_camera`，见 7.1）

#### 7.4 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 随机书 `/obj/item/book/random` | 44 | 最常见 |
| 随机手册 `/obj/item/book/manual/random` | 5 | |
| **WGW 致盲咒书** `/obj/item/book/granter/action/spell/blind/wgw` | 1 | 极稀有（学习致盲法术 "WGW"） |

#### 7.5 Nova 扩展

Nova 将放大镜加入馆长出生包（`glassblowing/magnifying_glass`，见 7.3 背包内容，带 NOVA EDIT 注释）。无其他专门覆写。

#### 7.6 一句话总结

**馆长 = 巴别塔免疫 + 全语言精通 + 双频道耳机 + 唯一广播摄像机 + 图书馆/采矿站权限**

知识与语言的化身：全站唯一"什么都听得懂"的人，也是唯一能开广播直播的人。

---

### 八、牧师 Chaplain

**代码**: `code/modules/jobs/job_types/chaplain/chaplain.dm`（目录）+ `chaplain_nullrod.dm`（圣器变体）+ `modular_nova/master_files/code/modules/jobs/job_types/chaplain.dm`

#### 8.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 1总/1出生 |
| 上级 | 人事部长 |
| 部门 | 服务部 |
| 工资等级 | CREW |
| 发薪部门 | 服务部账目 |
| 经验类型 | 船员经验 |
| 配置标签 | "CHAPLAIN" |
| RPG称号 | Paladin（圣骑士） |
| 职业音调 | `job_tone = "holy"`（🔊"神圣"） |
| 神之声 | `voice_of_god_power = 2`（牧师的神之声力量翻倍——"very good at speaking with the voice of god"） |
| 脱敏基础值 | `desensitized_base = DESENSITIZED_THRESHOLD`（对尸体/血腥脱敏，葬礼从业者） |
| 家族遗物 | 发条工具箱玩具 `/obj/item/toy/windup_toolbox` / 圣水 `/obj/item/reagent_containers/cup/glass/bottle/holywater` |
| 血浆人制服 | `/datum/outfit/plasmaman/chaplain` |

**核心机制 — 创建宗教**（`after_spawn`）:
- 首个牧师为 **HIGHPRIEST（大祭司）**，可自定义: 宗教名 `religion`、神名 `deity`、圣经名 `bible`（角色偏好设置）；
- 之后加入的牧师为 **PRIEST（普通神职）**，沿用全站既有的 `GLOB.religion/deity/bible_name` 与圣经外观；
- 出生即获得**圣水圣经**（装进背包）+ **圣器武器**（见 8.2），并触发宗教教派转化 `GLOB.religious_sect.on_conversion`；
- **名字彩蛋**: 宗教名含 `gay/penis/lol/meme/skibidi` 等词时，圣经与神名自动替换成对应梗（如 "War of Cocks"、"Skibidi Toilet"），且角色**脑损伤 100 开局**（源码: "starts off brain damaged as fuck"）。

#### 8.2 圣器武器 — Null Rod（虚空权杖）与变体

**代码**: `chaplain_nullrod.dm` — 出生 `put_in_hands` 一把圣器，可经 `subtype_picker` 径向菜单**自选变体**（`GLOB.nullrod_variants`）:

| 变体 | 说明 |
|---|---|
| 标准虚空权杖 `/obj/item/nullrod` | 黑曜石权杖，`force = 18`，抑制魔法，可别腰带 |
| 鲤鱼玩偶圣器 `/obj/item/toy/plush/carpplushie/nullrod` | 伤害略低但可爱；可为一人赐福获得太空鲤鱼友谊 |
| 神圣之弓 `/obj/item/gun/ballistic/bow/divine` | 神弓 + 10 支圣箭 |
| **夺魂镰刃碎片** `/obj/item/organ/cyberimp/arm/toolkit/shard/scythe` | 植入手臂，召唤夺魂镰；可斩首强化；解除镰刀未伤人会自伤；植入者获得"病态"兴趣 |
| 圣滑板 `/obj/item/melee/skateboard/holyboard` | 骑行获得飞行 + 反魔法能力 |
| 半藏刀 `/obj/item/storage/belt/sheath/hanzo_katana` | 低概率格挡近战；腰带鞘支持快速反击 |
| 圣光剑 `/obj/item/melee/energy/sword/nullrod` | 能量剑变体：伤害较低、无穿甲、低概率格挡，可开关 |

（另有 `chaplain_vorpal_scythe.dm` / `chaplain_divine_archer.dm` / `chaplain_costumes.dm` 提供对应机制与服饰。）

#### 8.3 技能芯片 — 3NTR41LS skillchip（内脏读者）

**代码**: `generic_skillchips/misc.dm` L57-64

```dm
/obj/item/skillchip/entrails_reader
  name = "3NTR41LS skillchip"
  auto_traits = list(TRAIT_ENTRAILS_READER)
  skill_name = "Entrails Reader"
  skill_description = "Be able to learn about a person's life, by looking at their internal organs."
```

牧师出生自带 1 枚芯片（`skillchips = list(/obj/item/skillchip/entrails_reader)`）。

#### TRAIT_ENTRAILS_READER — 内脏读者

| 场景 | 效果 |
|---|---|
| **检查肝脏** (`_liver.dm` L93-110) | 检查他人肝脏时读出其**代谢特质**（执法/烹饪/调酒/喜剧/医疗/工程/科学家肝等专属文本，如喜剧肝显示 "honking"） |
| **检查器官** | 解读器官上的"人生痕迹"（肝损伤、心脏劳损、肺疤痕） |

**直观理解**: 牧师一眼看出你肝是"会响的喜剧肝"还是"调酒师肝"——殡葬从业者的验尸直觉。

#### 8.4 特质分析 — mind_traits = list(TRAIT_SPIRITUAL)（虔诚）

**代码**: `chaplain.dm` L16 + `pray.dm` L33、`_mood_event.dm` L84、`areas/station/service.dm` L118

| 场景 | 效果 |
|---|---|
| **祈祷** (`pray.dm` L33) | 祈祷以 **SPIRITUAL_PRAYER（虔诚祈祷）** 类型发送给管理员——牧师祈祷自带神圣标识 |
| **教堂区域** (`areas/station/service.dm` L118) | 教堂/礼拜堂区域设有 `mood_trait = TRAIT_SPIRITUAL` 心情加成——站对地方心情好 |
| **精神心情事件** (`_mood_event.dm` L84) | 需要虔诚特质的宗教心情事件正常触发 |
| **神圣触诊** (`mutations/touch.dm` L387) | 神选/虔诚者相关互动判定（圣手类突变） |

| 类型 | 内容 |
|---|---|
| liver_traits | 无（但有脱敏基础值，见 8.1） |
| 技能芯片 | `skillchip/entrails_reader`（见 8.3） |

#### 8.5 装备部位表

**代码**: `/datum/outfit/job/chaplain`

| 部位 | 物品 |
|---|---|
| ID权限 | `/datum/id_trim/job/chaplain` — **教堂办公室** (ACCESS_CHAPEL_OFFICE)、**火化间** (ACCESS_CREMATORIUM)、矿物仓库、**停尸房** (ACCESS_MORGUE)、服务部、剧场 |
| 制服 | 牧师制服 `/obj/item/clothing/under/rank/civilian/chaplain` |
| 腰带 | 牧师PDA `/obj/item/modular_computer/pda/chaplain` |
| 耳机 | 服务频道 `headset_srv` |
| 背包 | 邪教背包 `/obj/item/storage/backpack/cultpack`（挎包同款） |
| 变色龙配件 | 牧师印章 `/obj/item/stamp/chap` |

#### 背包内容

```
/obj/item/camera/spooky = 1   — 灵异相机（拍照出鬼影特效）
/obj/item/stamp/chap = 1      — 牧师印章
```

（圣水圣经由 after_spawn 装入背包；圣器武器直接递到手上。）

#### 8.6 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 圣水 `/obj/item/reagent_containers/cup/glass/bottle/holywater` | 30 | 最常见 |
| 圣水手雷 `/obj/item/grenade/chem_grenade/holy` | 5 | 稀有（圣水爆炸） |
| 纳尔玩偶 `/obj/item/toy/plush/narplush` | 2 | 邪神纳尔·希玩偶 |
| 鼠神玩偶 `/obj/item/toy/plush/ratplush` | 1 | 极稀有（鼠神 Ratvar） |

#### 8.7 Nova 扩展

**代码**: `modular_nova/.../job_types/chaplain.dm`

Nova 增加了**大祭司继承系统**:
- 记录当前大祭司 `GLOB.current_highpriest`（弱引用）；
- 新加入的普通牧师自动加入继承名单 `GLOB.holy_successors`；
- 大祭司离任/死亡后，后继者自动补发自己的虚空权杖（`put_in_hands(new /obj/item/nullrod)`）。

#### 8.8 一句话总结

**牧师 = 自建宗教 + 圣器自选（镰刀/神弓/圣光剑…） + 虔诚特质 + 内脏读者芯片 + 火化间/停尸房权限**

信仰即是战斗力：自定义神名圣经、选一把圣器、葬礼火化一条龙；虔诚祈祷直通管理员，内脏读者一眼看穿你的肝。

---

### 九、囚犯 Prisoner

**代码**: `code/modules/jobs/job_types/prisoner.dm` + `modular_nova/master_files/code/modules/jobs/prisoner.dm` + `code/modules/client/preferences/prisoner_crime.dm`（犯罪类型定义）

#### 9.1 基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | **0总/4出生**（不能主动选岗，只能被"发配"进永久监禁区） |
| 上级 | "the security team"（安保团队） |
| 部门 | 服务部（档案归属，Nova 改为**助手部门**偏好） |
| 工资等级 | **LOWER（最低档）** — 全服务部唯一低薪职业 |
| 发薪部门 | 服务部账目 |
| 经验类型 | 船员经验 |
| 配置标签 | "PRISONER" |
| RPG称号 | Defeated Miniboss（被击败的小Boss） |
| 家族遗物 | 蓝笔 `/obj/item/pen/blue` |
| 血浆人制服 | `/datum/outfit/plasmaman/prisoner` |
| 岗位标志 | `STATION_JOB_FLAGS \| JOB_CANNOT_OPEN_SLOTS \| JOB_ANTAG_PROTECTED & ~JOB_REOPEN_ON_ROUNDSTART_LOSS` |
| 邮件独占 | `exclusive_mail_goodies = TRUE`（只收囚犯专属礼物） |

**Nova 差异**: 原版 `department_for_prefs = /datum/job_department/security`（安保部），Nova 改为 `/datum/job_department/assistant`（助手部）——带 NOVA EDIT 注释。

**犯罪记录机制**（`handle_prisoner_joining` + `post_equip`）:
1. 出生时按玩家偏好 `prisoner_crime`（可选择 "Random" 随机）从 `GLOB.prisoner_crimes` 挑一项罪名；
2. 犯罪记录写入船员档案 `target_record.crimes`（判决: "Central Command / Indefinite."），重新生成档案照片；
3. 角色获得对应记忆 `permabrig_crimes`；
4. **纹身**: 按罪名等级在随机肢体上烙印 `tattoo` 组件（故事文本取自持久化库 `SSpersistence.prison_tattoos_to_use`），罪名越重纹身越多。

#### 罪名与纹身数（全 19 种，`prisoner_crime.dm`）

| 罪名 | 纹身数 | 备注 |
|---|---|---|
| 恶劣刑事过失 (Abhorrent Criminal Negligence) | 0 | |
| 试图研发克隆 (Attempted Development of Cloning) | 0 | 2560 年起克隆非法 |
| 试图谋杀 (Attempted Murder) | 2 | |
| 生物恐怖主义 (Biological Terrorism) | 3 | |
| 机密罪名 (Classified) | 0 | 找法务 |
| 商业间谍 (Corporate Espionage) | 1 | |
| 伪造 (Counterfeiting) | 1 | |
| 公司之敌 (Enemy of the Corporation) | 1 | |
| 重大破坏 (Grand Sabotage) | 2 | |
| 重大盗窃 (Grand Theft) | 1 | |
| 高层身份盗窃 (Identity Theft of High-Ranking Figure) | 0 | 有纹身没法冒充别人 |
| 乱穿马路 (Jaywalker) | 0 | 注释调侃: "还是该 6 个?" |
| 绑架 (Kidnapping) | 1 | |
| 大规模谋杀 (Mass Murder) | **6** | |
| 医疗事故 (Medical Malpractice) | 2 | 器官摘取等 |
| 谋杀 (Murder) | 3 | |
| 叛变 (Mutiny) | **5** | 注释: "+rep for trying" |
| 其他 (Other) | 1 | 找法务 |
| 篡改AI (Tampering of Artificial Intelligence) | 3 | |
| 逃税 (Tax Evasion) | 1 | |
| 崇拜黑名单神祇 (Worship of Blacklisted Deities) | 1 | |

#### 9.2 装备部位表

**代码**: `/datum/outfit/job/prisoner`

| 部位 | 物品 |
|---|---|
| ID | 囚犯高级ID `/obj/item/card/id/advanced/prisoner` |
| ID权限 | `/datum/id_trim/job/prisoner` — **无任何实际门禁**（仅 template_access 占位，无 minimal_access） |
| 制服 | 囚服 `/obj/item/clothing/under/rank/prisoner` |
| 鞋 | **橙色运动鞋** `/obj/item/clothing/shoes/sneakers/orange` |
| 腰带 | **无**（belt = null） |
| 耳机 | **无**（ears = null） |
| PDA槽 | **无**（pda_slot = null） |
| 生存盒 | 囚犯生存盒 `/obj/item/storage/box/survival/prisoner` |

#### 彩蛋

`pre_equip` 有 **1% 概率**戴黑色 D-BOY 无檐帽 `/obj/item/clothing/head/beanie/black/dboy`（"D BOYYYYSSSSS"）。

#### 9.3 特质分析

| 类型 | 内容 |
|---|---|
| mind_traits | 无 |
| liver_traits | 无 |
| 技能芯片 | 无 |

囚犯没有任何血脉被动——惩罚型职业，全凭监狱求生智慧。

#### 9.4 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| 监狱违禁品随机 `/obj/effect/spawner/random/contraband/prison` | 1 | 唯一礼物（监狱专属违禁品随机池: 自制武器/毒品/工具等） |

#### 9.5 Nova 扩展

**代码**: `modular_nova/.../jobs/prisoner.dm`

`post_equip` 覆写: 出生时**没收所有可没收的植入体**——遍历全身 `cyberimp`，`cannot_confiscate` 标记的除外，其余一律删除并提示（"Some of your implants have been confiscated."）。囚犯想藏义体？Nova 说不行。

#### 9.6 一句话总结

**囚犯 = 0岗位位/4出生位 + 无门禁囚犯ID + 无PDA无耳机 + 随机罪名与纹身 + Nova没收义体 + 最低工资**

从进永久监禁区的那一刻起，你的犯罪档案、纹身和（被没收的）义体都已写好——想越狱？先把 6 个纹身的罪名洗白。

---

### 服务部职业速查表

| 职业 | 岗位(总/出生) | 工资 | 技能芯片 | mind_traits | liver_traits | 特殊点 |
|---|---|---|---|---|---|---|
| 调酒师 | 1/1 | CREW | drunken_brawler（醉酒格斗） | — | 调酒师代谢 | 装甲背心+武器库权限 |
| 厨师 | 2/2 | CREW | chef（厨房CQC武术） | — | 烹饪代谢 | 主厨/副厨双职称、大蒜自愈 |
| 植物学家 | 3/2 | CREW | — | — | — | 纯工具人、花枪邮件 |
| 清洁工 | 2/1 | CREW | **双芯片**: 清洁机器人低语+投掷臂 | — | — | 维护隧道权限、垃圾日左轮 |
| 小丑 | 1/1 | CREW | clown（气球瑜伽术） | **TRAIT_NAIVE 天真** | 喜剧代谢（会响的肝） | 笨拙突变+彩虹ID+悲催长号 |
| 哑剧演员 | 1/1 | CREW | — | 无 | — | 沉默誓言+哑剧法术书 |
| 馆长 | 1/1 | CREW | — | **TRAIT_TOWER_OF_BABEL 巴别塔免疫** | — | 全语言精通+广播摄像机 |
| 牧师 | 1/1 | CREW | entrails_reader（内脏读者） | **TRAIT_SPIRITUAL 虔诚** | — | 自建宗教+圣器自选 |
| 囚犯 | 0/4 | **LOWER** | — | — | — | 无门禁无PDA、罪名纹身、Nova没收义体 |

**服务部共性**: 全部上级=人事部长、部门=服务部、工资档 CREW（囚犯 LOWER）、发薪=服务部账目、经验=船员经验、血浆人制服齐全。


---

## 第五篇 · NOVA 特有职业



> 本章覆盖 **NovaSector（天关13）专属职业** —— 这些职业在原版 TG Station 中不存在，全部由 `modular_nova/` 模块新增或深度改造。
> 来源文件逐一标注于各职业标题下方，数值与路径均从源码精确提取。
> 通用说明：NOVA 特有职业大多属于**幽灵角色（ghost role）**或**离站阵营职业**，不上站内岗位轮换表；除极个别例外（通信专家、Interdyne 矿工）外**没有技能芯片（skillchip）**。

---

### 一、蓝盾军官 Blueshield（JOB_BLUESHIELD）

- **源码**：`modular_nova/modules/blueshield/code/blueshield.dm`（配套：`clothing.dm` / `closet.dm` / `devices/sensor_device.dm`）
- **定位**：跟随中央指挥（Central Command）派驻的**高管贴身保镖**，专职保护站内所有部门主管（Heads of Staff），隶属于中央指挥+指挥双部门。

#### 基础信息表

| 项目 | 内容 |
|---|---|
| 职位路径 | `/datum/job/blueshield` |
| 职位名 | JOB_BLUESHIELD「蓝盾军官」 |
| 描述（原文） | "Protect heads of staff, get your fancy gun stolen, cry as the captain touches the supermatter." |
| 阵营 | FACTION_STATION（站内阵营） |
| 岗位数 | 1（total_positions = 1 / spawn_positions = 1） |
| 上级 | "Central Command and the Nanotrasen Consultant"（中央指挥与 NT 顾问） |
| 经验要求 | 2400（EXP_TYPE_CREW，部门要求 EXP_TYPE_COMMAND） |
| 最低玩家年龄 | minimal_player_age = 7（天） |
| 薪水 | PAYCHECK_COMMAND（指挥级）/ ACCOUNT_CMD |
| 部门 | `/datum/job_department/central_command` + `/datum/job_department/command` |
| 偏好部门 | `/datum/job_department/captain`（归类于舰长部门） |
| 肝脏特质 | TRAIT_PRETENDER_ROYAL_METABOLISM（伪皇室代谢） |
| 悬赏类型 | CIV_JOB_SEC（民事安保悬赏） |
| 职业标志 | STATION_JOB_FLAGS \| JOB_CANNOT_OPEN_SLOTS \| JOB_ANTAG_PROTECTED（不可开槽、反antag保护） |
| 特殊限制 | `nova_stars_only = TRUE`（仅 NOVA 星玩家可选，受 GLOB.nova_star_restrictions 控制） |
| 自动去admin | auto_deadmin_role_flags = DEADMIN_POSITION_SECURITY |
| 传家宝 | `/obj/item/bedsheet/captain`、`/obj/item/clothing/head/beret/blueshield` |
| 邮件礼物 | 哈瓦那雪茄×10、500面额现金×3、假核弹磁盘×2、可收藏舰长帽×4 |

#### 装备部位表（/datum/outfit/job/blueshield）

| 部位 | 装备路径 | 说明 |
|---|---|---|
| 制服 uniform | `/obj/item/clothing/under/rank/blueshield` | 经典保镖西装，蓝袖口+NT徽记；护甲 近战10/子弹5/激光5/能量10/爆炸10/火50/酸50；strip_delay 50；传感器强制坐标模式 |
| 外套 suit | `/obj/item/clothing/suit/armor/vest/blueshield/jacket` | 凯夫拉内衬夹克，金徽章+背部"NT"字样，覆盖胸+手臂 |
| 手套 gloves | `/obj/item/clothing/gloves/tackler/security` | 安保擒抱手套 |
| 胸卡 id | `/obj/item/card/id/advanced/centcom/station` | 中央指挥站内卡 |
| 鞋子 shoes | `/obj/item/clothing/shoes/jackboots` | 军用高帮靴 |
| 耳机 ears | `/obj/item/radio/headset/headset_bs/alt` | 蓝盾专属耳机，双密钥：heads/blueshield + headset_cent；alt 版带防闪光弹耳保护组件 |
| 眼镜 glasses | `/obj/item/clothing/glasses/hud/security/sunglasses/blue` | 蓝色安保HUD墨镜 |
| 头部 head | `/obj/item/clothing/head/beret/blueshield` | 杜拉线贝雷帽+金徽章（LT 中尉标识），纳米凯夫拉内衬，护甲 cosmetic_sec |
| 腰带 belt | `/obj/item/modular_computer/pda/blueshield` | 蓝盾PDA（钢笔+安保记录+机器人控制程序） |
| 左口袋 l_pocket | `/obj/item/sensor_device/blueshield` | 「蓝盾手持监视器」：定制版船员监控，一键开启全船生命体征监控（GLOB.blueshield_crewmonitor） |
| 背包 | `/obj/item/storage/backpack/blueshield`（satchel/duffelbag/messenger 各版本） | 蓝盾定制背包 |
| 植入体 implants | `/obj/item/implant/mindshield` | 心灵护盾植入体（反洗脑） |
| 背包内容 | `/obj/item/choice_beacon/blueshield` ×1 | 一次性武器召唤信标（见下） |
| 生存盒 | `/obj/item/storage/box/survival/security` | 安保生存盒 |
| 塑料人 | `/datum/outfit/plasmaman/blueshield` | 专属环境服（护甲 近战10/生物100/火95/酸95） |

#### 特质与专属装备

- **武器信标 choice_beacon/blueshield（weaponry beacon）**：一次性使用，呼叫补给舱，可选武器共 5 种：
  1. **Blueshield Energy Shield**（`/obj/item/shield/energy/returning/blueshield`）——可投掷回旋能量盾，激活投掷力 27（5 击致死），带回旋镖组件，NT 制造商标识
  2. **Fendér Revolver Set**（`trappiste_small_case/bluvolva`）——Fendér 左轮套装
  3. **Custom Hellfire Laser Carbine**（`hellgun/blueshield`）——「流线型地狱火激光卡宾枪」，蓝色配色，弹药为蓝盾版地狱火（e_cost = LASER_SHOTS(20, STANDARD_CELL_CHARGE)，比标准版多几发）
  4. **NT20 Submachinegun Gunset**（`ntspecial/nt20`）——NT20 冲锋枪组
  5. **Katyusha Shotgun Gunset**（`katyusha`）——喀秋莎霰弹枪组
- **专属储物柜（secure_closet/blueshield，需舰长权限 ACCESS_CAPTAIN）**：保险公文包、满配安保腰带、闪光弹、手持闪光、手铐、安保HUD墨镜、边疆医疗包、服装袋（garment bag：冬大衣/双贝雷帽/软帽/多套制服/马甲/披肩等 14 件套）、**MOD 装甲**（pre_equipped/blueshield：大容量储物/磁力挂载/手电/弹道阻尼/快速携带/枪套模块）、MOD 喷漆、蓝盾监视器
- **替代职称**：Blueshield / Command Bodyguard / Corporate Protection Specialist / Executive Protection Agent
- **ID 权限（id_trim/job/blueshield）**：minimal = 货运/安保/全员个人储物柜/禁闭室入口/中央通用/指挥/施工/工程/EVA/维护隧道/医疗/矿物库/科研/传送器/武器库/**舰长**；extra = 禁闭室/法庭/传送门；template = 舰长+改卡权限
- **PDA 预装程序**：安保记录（records/security）、机器人控制（robocontrol）

> **一句话总结**：中央指挥派驻的专职保镖，全站通行+心灵护盾+武器信标五选一，职责就是让各个部门主管活着下班。

---

### 二、舰桥助理 Bridge Assistant（JOB_BRIDGE_ASSISTANT）

- **源码**：`modular_nova/modules/bridge_assistant/code/bridge_assistant.dm`
- **定位**：舰桥上的「打杂+传令官」，指挥部的多面手，既是舰长的随从也兼服务部职责。

#### 基础信息表

| 项目 | 内容 |
|---|---|
| 职位路径 | `/datum/job/bridge_assistant` |
| 职位名 | 默认职称：Bridge Officer「舰桥军官」（无独立 title 字段，用 outfit 名） |
| 岗位数 | 1（total_positions = 1 / spawn_positions = 1） |
| 职业标志 | STATION_JOB_FLAGS \| JOB_ANTAG_PROTECTED |
| 特殊限制 | `nova_stars_only = TRUE`（仅 NOVA 星玩家） |
| 部门 | `/datum/job_department/command` + `/datum/job_department/service`（指挥+服务） |
| 偏好部门 | `/datum/job_department/captain` |
| 邮件礼物 | 托盘×1、香烟售货机补给×1、咖啡售货机补给×1 |
| 薪水 | 未指定（继承基础） |
| 塑料人 | `/datum/outfit/job/bridge_assistant/plasmaman` |

#### 装备部位表（/datum/outfit/job/bridge_assistant）

| 部位 | 装备路径 | 说明 |
|---|---|---|
| 制服 uniform | `/obj/item/clothing/under/rank/civilian/lawyer/greensuit` | 绿色纽扣西装（可调，覆盖胸部），post_equip 自动加挂**马甲**（waistcoat） |
| 领饰 neck | `/obj/item/clothing/neck/bowtie/green` | 绿色领结 |
| 耳机 ears | `/obj/item/radio/headset/bridge_officer` | 舰桥军官耳机：指挥密钥 headset_com，command = TRUE（指挥频道） |
| 腰带 belt | `/obj/item/modular_computer/pda/bridge_assistant` | PDA（状态程序），PDA 槽位在腰带 |
| 左口袋 l_pocket | `/obj/item/melee/baton/telescopic/bronze` | 青铜伸缩警棍 |
| 右口袋 r_pocket | `/obj/item/gun/energy/e_gun/mini` | 迷你能量枪 |
| 胸卡 id | `/obj/item/card/id/advanced/silver` | 银卡 |
| 眼镜/手套/头 | `null`（显式移除，原版基础装备不适用） |
| 背包内容 | `choice_beacon/job_locker/bridge_officer`（职业储物柜信标）+ `choice_beacon/coffee`（咖啡信标） |

#### 特质与专属装备

- **ID 权限（id_trim/job/bridge_assistant）**：minimal = 指挥/外太空服/传送门/维护隧道/广播公告/传送器/武器库/**金库（VAULT）**/技术仓库/服务/矿物库（NOVA 新增）；department_color 服务青柠色 + 子部门指挥蓝
- **PDA 预装**：status（状态）
- **替代职称**：Bridge Officer / Command Aide / Ensign / Command Cadet / Bridge Attendant / Command Secretary / Command Intern
- **敬称**：Underling / Assistant / Mate
- **塑料人版**：标准塑料人服+黑手套+环境头盔+绿领结+青铜警棍+迷你能量枪

> **一句话总结**：穿绿西装系绿领结的舰桥跑腿，配青铜警棍和迷你能量枪，管着咖啡和命令传达，还能开金库。

---

### 三、通信专家 Telecomms Specialist（JOB_TELECOMMS_SPECIALIST）

- **源码**：`modular_nova/modules/telecomms_specialist/telecomms_specialist.dm`
- **定位**：工程部下辖的**专职电信维护员**，负责全站通信网络（Tcomms）的监控、配置与维护，兼做轻度工程。

#### 基础信息表

| 项目 | 内容 |
|---|---|
| 职位路径 | `/datum/job/telecomms_specialist` |
| 职位名 | JOB_TELECOMMS_SPECIALIST「通信专家」 |
| 描述（原文） | "Monitor, configure, and maintain all station communications and assist with light engineering work." |
| 阵营 | FACTION_STATION |
| 岗位数 | 1（total_positions = 1 / spawn_positions = 1） |
| 上级 | SUPERVISOR_CE（总工程师） |
| 经验要求 | 60（EXP_TYPE_CREW） |
| 薪水 | PAYCHECK_CREW / ACCOUNT_ENG |
| 肝脏特质 | TRAIT_ENGINEER_METABOLISM（工程师代谢，抗酒更抗毒） |
| 悬赏类型 | CIV_JOB_ENG |
| 部门 | `/datum/job_department/engineering` |
| 传家宝 | 笔记本电脑 / 无线电 / pai卡 / 口袋保护套 |
| 邮件礼物 | 咖啡胶囊×20、杯子×10、亚空间零件×8、banhammer×8、维护磁盘×1 |
| RPG称号 | rpg_title = "Diviner"（占卜师） |
| 职业标志 | STATION_JOB_FLAGS（标准站内职业，**可被 AI/威胁槽位**） |

#### 装备部位表（/datum/outfit/job/telecomms_specialist）

| 部位 | 装备路径 | 说明 |
|---|---|---|
| 制服 uniform | `/obj/item/clothing/under/rank/engineering/engineer/nova/utility/telecomm` | NOVA 通信专家工装 |
| 外套 suit | `/obj/item/clothing/suit/toggle/jacket/nova/colorable_bomber/tcomm` | 可染色轰炸机夹克（tcomm 配色） |
| 领饰 neck | `/obj/item/clothing/neck/link_scryer` | 「链接窥视器」领饰（通信主题饰品） |
| 腰带 belt | `/obj/item/screwdriver` | 螺丝刀 |
| 耳机 ears | `/obj/item/radio/headset/headset_eng` | 工程耳机 |
| 头部 head | `/obj/item/clothing/head/utility/hardhat/dblue` | 深蓝安全帽 |
| 鞋子 shoes | `/obj/item/clothing/shoes/laceup` | 系带皮鞋 |
| 左口袋 l_pocket | `/obj/item/modular_computer/pda/telecomms` | 通信PDA（报警监视器+信号指挥官+NT网络监视器） |
| 右口袋 r_pocket | `/obj/item/multitool` | 万用工具 |
| 背包 | `/obj/item/storage/backpack/industrial`（satchel/duffelbag/messenger 工程版） | 工程背包 |
| 生存盒 | `/obj/item/storage/box/survival/engineer` | 工程师生存盒 |
| **技能芯片** | **`/obj/item/skillchip/job/engineer`（工程师技能芯片！）** | 少数带技能芯片的 NOVA 职业 |
| 背包内容 | `paper/monitorkey`（监控密钥说明纸）、`wirecutters`（剪线钳）、`holosign_creator/atmos`（大气全息标志生成器） |

#### 特质与专属装备

- **PDA 预装程序**：alarm_monitor（报警监视）/ signal_commander（信号指挥官）/ ntnetmonitor（NT 网络监视）
- **ID 权限（id_trim/job/telecomms_specialist）**：minimal = 施工/工程/维护隧道/机械工程/矿物库/**迷你卫星站（MINISAT）**/**网络（NETWORK）**/广播公告/通信（TCOMMS）/技术仓库；extra = 大气/引擎装备/外部气闸；template = 舰长+改卡+CE
- **专属出生点标记**：`/obj/effect/landmark/start/telecomms_specialist`（NOVA 专属图标）
- **替代职称**：Telecomms Specialist / Wireless Operator / Network Engineer / Sysadmin / Telecomms Technician / Tram Technician
- **塑料人**：使用工程塑料人制服 `/datum/outfit/plasmaman/engineering`

> **一句话总结**：管着全站无线电命脉的通信网管，权限直通迷你卫星站和网络核心，还顺手带着工程师技能芯片。

---

### 四、比特跑者 Bitrunner（JOB_BITRUNNER）

- **源码**：`code/modules/bitrunning/job.dm`（主定义）+ `modular_nova/modules/bitrunning/code/outfit.dm`（NOVA 装备扩展）
- **定位**：货舱部的**虚拟领域冒险家**，肉身躺进比特锚点（Bitrunning Anchor），意识进入虚拟领域（Virtual Domain）搜刮装备与战利品，回站解密奖励。

#### 基础信息表

| 项目 | 内容 |
|---|---|
| 职位路径 | `/datum/job/bitrunner` |
| 职位名 | JOB_BITRUNNER「比特跑者」 |
| 描述（原文） | "Surf the virtual domain for gear and loot, decrypt your rewards on station." |
| 阵营 | FACTION_STATION |
| 岗位数 | 3（total_positions = 3 / spawn_positions = 3） |
| 上级 | SUPERVISOR_QM（军需官） |
| 经验 | 无门槛（无 exp_requirements，仅 exp_granted_type = CREW） |
| 薪水 | PAYCHECK_CREW / ACCOUNT_CAR |
| 悬赏类型 | CIV_JOB_BITRUN |
| 部门 | `/datum/job_department/cargo` |
| 传家宝 | 太空山风汽水罐（space_mountain_wind） |
| 邮件礼物 | 玉米片×1 + 各色玉米片（绿/红/紫/蓝）×1 + 太空山风汽水×1 |
| RPG称号 | rpg_title = "Recluse"（隐士） |
| 职业标志 | STATION_JOB_FLAGS |
| 塑料人 | `/datum/outfit/plasmaman/bitrunner` |

#### 装备部位表（/datum/outfit/job/bitrunner + NOVA outfit.dm 扩展）

| 部位 | 装备路径 | 说明 |
|---|---|---|
| 制服 uniform | `/obj/item/clothing/under/rank/cargo/bitrunner` | 货舱比特跑者制服 |
| 腰带 belt | `/obj/item/modular_computer/pda/bitrunner` | 比特跑者PDA（街机+技能追踪器） |
| 耳机 ears | `/obj/item/radio/headset/headset_cargo/bitrunning` | **比特跑者耳机**：密钥含 供应+科研+阵营 三频道 |
| 右口袋 r_pocket | `/obj/item/disk/bitrunning/prefs` | 比特跑偏好盘（NOVA 新增） |

#### 特质与专属装备

- **ID 权限（id_trim/job/bitrunner）**：minimal = **比特巢穴（BIT_DEN）**/货运/维护隧道/机械采矿/矿物库；extra = 采矿/采矿站；template = 舰长+改卡+QM
- **虚拟领域玩法**：通过 `modular_nova/modules/bitrunning/` 整套模块（server.dm / virtual_domains/）进入多种虚拟领域——古代军事模拟（ancient_milsim）、海盗、岛屿乱斗、辛迪加突击等，战利品经 `loot_crate.dm` / `lockbox.dm` 带回
- **外包比特跑者（subcontracted_bitrunner）**：`/datum/outfit/subcontracted_bitrunner`，独立阵营版装备（黑色运动鞋+信使包）
- **Bit Avatar**：`/datum/job/bit_avatar`「比特化身」子职业
- **替代职称**：Bitrunner / Bitdomain Technician / Data Retrieval Specialist / Netdiver / Pod Jockey / Union Bitrunner / Junior Runner

> **一句话总结**：意识出窍进虚拟领域打宝的网瘾矿工，三个频道耳机一戴，下本回来开锁箱。

---

### 五、货船船员 Freighter Crew（ROLE_FREIGHTER_CREW）

- **源码**：`modular_nova/master_files/code/modules/jobs/job_types/spawner/freighter_crew.dm` + `modular_nova/modules/mapping/code/mob_spawns.dm`（lostcargo 段）
- **定位**：**「迷失太空卡车司机」（Lost Space Truckers）**——六名被困深空的独立货船船员，海盗袭击后引擎报废，只能互相协作求生。

#### 基础信息表

| 项目 | 内容 |
|---|---|
| 职位路径 | `/datum/job/freighter_crew` |
| 职位名 | ROLE_FREIGHTER_CREW「货船船员」 |
| 薪水 | PAYCHECK_ZERO（零薪水，离站阵营） |
| 政策索引 | policy_index = ROLE_FREIGHTER_CREW |
| 类型 | 幽灵角色（ghost role），非站内职业 |

#### 三个子角色装备表

| 子角色 | 生成器 | 装备内容 |
|---|---|---|
| **货船船员**（lostcargo，cryo crew pod） | `/datum/outfit/freighter_crew` | 制服 `cargo/tech/nova/casualman`、工装靴、普通背包、胸卡 `away/freightcrew`（**货运技术员 trim**）；"You are not directly working with NT, you are an independent freighter crew for the ship's Chief." |
| **挖掘员**（lostminer，cryo excavator pod） | `/datum/outfit/freighter_excavator` | 制服 `cargo/tech/nova/gorka`、采矿工装靴、背包内含：手电/生存战斗刀/采矿券/高级采矿扫描仪(降级)/动能加速器/信标×10；右口袋矿石袋；胸卡 `away/freightmine`（**矿工 trim**） |
| **货船老大**（lostcargoqm，cryo boss pod） | `/datum/outfit/freighter_boss` | 制服 `cargo/tech/nova/turtleneck`、工装靴、**军需官披风**、背包内含货运用扩音器；胸卡 `away/silver/freightqm`（**军需官 trim** 银卡）；"You are the captain of the ship, which you purchased a while ago" |

#### 特质与玩法

- **开局设定**：海盗袭击→引擎报废→全员困在货船残骸；提示词强调「与船员合作，不要抛弃队友」
- **银行账户**：post_equip 通过 `handlebank()` 创建离站银行账户（不可替换，记忆存档）
- **独立身份**：所有胸卡 trim 复用站内职业（cargo_tech / shaft_miner / quartermaster）但属 `away` 系列，明确「不是为 NT 工作」
- 支持自定义外貌/性格（quirks）+ 外观预设继承

> **一句话总结**：海盗打烂引擎后被扔在深空的一船倒霉货工，船长带头修船挖矿卖货，活下去就是胜利。

---

### 六、黑市商人 Black Market Dealer（ROLE_BLACK_MARKET_DEALER）

- **源码**：`modular_nova/master_files/code/modules/jobs/job_types/spawner/blackmarket.dm` + `modular_nova/modules/mapping/code/mob_spawns.dm`（blackmarket 段）
- **定位**：在 NT 空间边缘的小行星上开店的**独立黑市商人**，倒卖文物、宝藏与违禁品，可与任何阵营交易。

#### 基础信息表

| 项目 | 内容 |
|---|---|
| 职位路径 | `/datum/job/blackmarket` |
| 职位名 | ROLE_BLACK_MARKET_DEALER「黑市商人」 |
| 薪水 | PAYCHECK_ZERO |
| 政策索引 | policy_index = ROLE_BLACK_MARKET_DEALER |
| 类型 | 幽灵角色（生成器：Black Market Trader 黑市商人冬眠舱） |
| 初始现金 | **500~2000 信用点随机**（BM_TRADER_MIN_CASH=500 / MAX_CASH=2000） |

#### 装备部位表（/datum/outfit/black_market）

| 部位 | 装备路径 | 说明 |
|---|---|---|
| 制服 uniform | `/obj/item/clothing/under/rank/cargo/tech` | 货运技术员制服 |
| 鞋子 shoes | `/obj/item/clothing/shoes/laceup` | 系带皮鞋 |
| 胸卡 id | `/obj/item/card/id/advanced/chameleon/elite/black/blackmarket` | 「磨损的塑料ID卡」，职称 Deck Crewman，权限 ACCESS_AWAY_GENERIC4（变色龙卡） |
| 左口袋 l_pocket | `/obj/item/shuttle_remote/bmd` | **黑市爆发穿梭机遥控器**（自动绑定全图 BM Burst 穿梭机控制台） |

#### 特质与玩法

- **开店设定**：在小行星上开「有点阴暗但能用的」商店，向 NT 的傻蛋们倒卖文物装备，或迎接「更异国情调的顾客」
- **非反派**：明确标注"You are not an antagonist."
- **武器随机刷点**（black_market_trader 武器生成器）：Cybersun 激光枪(80)、老式能量枪(50)、战斗霰弹枪(50)、违禁手枪(30)、Sol 步枪 evil 版(20)、Sol 冲锋枪 evil 版(20)、Bulldog 霰弹枪（无限制版）——武器自带换过撞针的 Cybersun 激光枪（`pin = /obj/item/firing_pin`）
- **穿梭机玩法**：BMD 遥控器与 `computer/shuttle/caravan/blackmarket_burst` 控制台双向绑定，可远程召回/驾驶黑市穿梭机
- 支持外貌预设+性格+装载（loadout）

> **一句话总结**：藏在 NT 眼皮底下的独立军火贩子，揣着 500~2000 块启动资金和黑市穿梭机遥控器，谁的钱都赚。

---

### 七、Tarkon 公司雇员 Port Tarkon（ROLE_PORT_TARKON）

- **源码**：`modular_nova/master_files/code/modules/jobs/job_types/spawner/tarkon.dm` + `modular_nova/modules/tarkon/code/misc_fluff/spawner.dm`
- **定位**：**Tarkon 工业**（Tarkon Industries）派往「塔肯港」（Port Tarkon）——一座被重新夺回的查理空间站式残骸——的 8 人幽灵角色团队，要在异形威胁下修复港口/完成建造。

#### 基础信息表

| 项目 | 内容 |
|---|---|
| 职位路径 | `/datum/job/tarkon`（及 `/command` 子类） |
| 职位名 | ROLE_PORT_TARKON「塔肯港雇员」 |
| 薪水 | PAYCHECK_ZERO |
| 部门账户 | ACCOUNT_TI（Tarkon 工业账户） |
| 悬赏类型 | TARKON_JOB_CREW（命令子类：TARKON_JOB_COMMAND） |
| 广播 | command 子类：head_announce = RADIO_CHANNEL_TARKON |
| 类型 | 幽灵角色（8 人），生成器：Port Tarkon Crew Member |
| 阵营 | FACTION_TARKON |
| 上级 | Ensign（少尉）与 Site Director（场地总监） |

#### 装备部位表（/datum/outfit/tarkon 及子类）

| 角色 | 装备内容 |
|---|---|
| **通用船员**（基础 outfit） | 制服 `under/tarkon/general`、焊工帽、冬靴、战斗手套、背包、胸卡 `advanced/tarkon`、耳机 `headset/tarkon`（FREQ_TARKON 频道）、撬棍；背包按偏好切换塔肯背包/挎包/行李袋/信使包 |
| **打捞技术员 cargo** | 塔肯货运制服、墨镜、采矿券（l_pocket）、胸卡 `tarkon/cargo` |
| **研究员 sci** | 塔肯科研制服、诊断HUD、高容量电池（r_pocket）、塔肯机器人卡（l_pocket） |
| **创伤军医 med** | 塔肯医疗制服、生命体征HUD、听诊器、健康分析仪、药物缝合线 |
| **维护工程师 engi** | 塔肯维护制服、网格目镜（meson tray）、**安保披风（tarkon）**、应急氧气罐、电缆线圈、撬棍+感应充电器 |
| **安保 sec** | 塔肯警卫制服、安保HUD、擒抱战斗手套、安保披风、伸缩警棍、**屏障手雷** |
| **少尉 ensign**（二把手） | 塔肯指挥制服、指挥耳机、安保披风、塔肯少尉卡 |
| **场地总监 director**（一把手） | 塔肯指挥制服、指挥耳机、安保披风、塔肯机器人卡（r_pocket）；spawner_job_path = /datum/job/tarkon/command |

#### 特质与玩法

- **开局设定**：「你不得抛弃塔肯港」「查看其他冬眠舱选择其他职位」「听从场地总监与少尉」；非塔肯角色勿选
- **异形威胁**：塔肯港被 **T-35 异形巢穴**（Xenomorph Hive T-35）感染——巢穴分为 `infested warren`（500耐久/4只/30秒刷怪，女王大型体）、`infested nest`（300耐久/2只/40秒）、`infested tunnel`（150耐久/1只/40秒）；**摧毁巢穴触发塌陷事件**：5 秒后刷出 BOSS（女王）+ 掉落战利品（异形手术工具/绑架者工具组/科技战利品）
- **专属武器**：M6 个人防卫武器（`m6pdw`，点射 2 连发、c35sol 手枪弹匣、不可消音、滑套卡涩的沧桑感）+ **A.R.C.S 谐振器**（`resonant_system`，能量自动充能枪，自动/手动/矩阵三模式，共振场上限 5 个）
- **可拾取尸体**：死亡塔肯少尉尸体（`corpse/human/tarkon`）穿 loot 版制服+塔肯 MOD 装甲+战壕工具
- **装备细节**：塔肯制服分 货运/科研/警卫/医疗/维护/指挥/通用 七色系；塔肯ID、塔肯耳机、安保披风为阵营标识

> **一句话总结**：Tarkon 工业派的 8 人开荒队，在异形横行的废弃港修船盖楼，拆巢刷BOSS捡异形科技。

---

### 八、幽灵咖啡厅 Ghost Cafe（ROLE_GHOST_CAFE）

- **源码**：`modular_nova/master_files/code/modules/jobs/job_types/spawner/ghostcafe.dm` + `modular_nova/modules/ghostcafe/code/ghost_role_spawners.dm`
- **定位**：**死者的社交咖啡厅**——玩家以幽灵角色身份「休假」来喝咖啡聊天，能听到死者频道，离开区域即消散。

#### 基础信息表

| 项目 | 内容 |
|---|---|
| 职位路径 | `/datum/job/ghostcafe` |
| 职位名 | ROLE_GHOST_CAFE「幽灵咖啡厅」 |
| 薪水 | PAYCHECK_ZERO |
| 政策索引 | policy_index = ROLE_GHOST_CAFE |
| 类型 | 幽灵角色（无限使用生成器：Cafe Sleeper / Cafe Robotic Storage） |

#### 装备部位表（/datum/outfit/ghostcafe）

| 部位 | 装备路径 | 说明 |
|---|---|---|
| 制服 uniform | `/obj/item/clothing/under/color/random` | 随机颜色制服 |
| 鞋子 shoes | `/obj/item/clothing/shoes/sneakers/black` | 黑色运动鞋 |
| 胸卡 id | `/obj/item/card/id/advanced/chameleon/ghost_cafe` | 「Cafe ID」变色龙卡，wildcard_slots = WILDCARD_LIMIT_ADMIN（管理员级通配） |
| 背包 back | `/obj/item/storage/backpack/chameleon` | 变色龙背包 |
| 背包内容 | `storage/box/syndie_kit/chameleon/ghostcafe` | **咖啡厅服装套装**：变色龙全套（制服/外套/手套/鞋/眼镜/头/面具/领饰/腰带）+ 发型魔镜 |

#### 特质与玩法

- **核心特性**：**TRAIT_SIXTHSENSE（第六感：能听见死者频道）** + **TRAIT_FREE_GHOST（随时自由脱出）** + **TRAIT_NOBREATH（无需呼吸）**；生成即附赠「Toggle deadchat」技能——可开关幽灵频道收听
- **区域限制**：离开咖啡厅区域（`dusts_on_leaving_area`）或在僵直昏迷（`dusts_on_catatonia`）时**直接化为灰烬**；咖啡厅区域内**禁用无线电**（COMSIG_MOVABLE_USING_RADIO 拦截）
- **机器人变体**：`Cafe Robot`（roleplay 型号机器人，无限使用），同样吃第六感+自由脱出，中性性别
- **种族适配**：塑料人/瓦克斯自动追加对应呼吸罐×2
- 支持外貌预设+性格+装载

> **一句话总结**：死后也能点单的休闲副本，戴上耳机听鬼魂唠嗑，一离开咖啡厅就当场风化。

---

### 九、DS2 佣兵 Deep Space 2（ROLE_DS2）

- **源码**：`modular_nova/master_files/code/modules/jobs/job_types/spawner/ds2.dm` + `modular_nova/modules/mapping/code/mob_spawns.dm`（ds2 段）
- **定位**：**辛迪加（Syndicate）深空2号前进基地（FOB）**人员——一个在 13 扇区监视 NT 采矿活动的生物武器研究设施。包含囚犯、普通特工、指挥层、机器人等多个子角色。

#### 基础信息表

| 项目 | 内容 |
|---|---|
| 职位路径 | `/datum/job/ds2`（子类：prisoner / command / engineer / science / enforce） |
| 职位名 | ROLE_DS2「深空2号」 |
| 薪水 | PAYCHECK_CREW（囚犯子类 PAYCHECK_ZERO；指挥子类 PAYCHECK_COMMAND） |
| 部门账户 | ACCOUNT_DS2（囚犯无账户） |
| 悬赏类型 | DYNE_JOB_SCIENCE（囚犯 CIV_JOB_RANDOM；指挥 DS2_JOB_COMMAND；工程师 DS2_JOB_ENGINEER；科研 DS2_JOB_MECHANICAL；执法 DS2_JOB_ENFORCER） |
| 广播 | 指挥子类：head_announce = RADIO_CHANNEL_INTERDYNE |
| 类型 | 幽灵角色（生成器：DS2 personnel，可自定义外观） |

#### 子角色装备表

| 子角色 | 生成器 / 装备路径 | 装备内容 |
|---|---|---|
| **辛迪加囚犯**（prisoner） | `/datum/outfit/ds2/prisoner` | 辛迪加囚服、深红运动鞋、囚犯ID；提示"必须 ahelp 后才能敌对 DS2" |
| **DS-2 特工**（syndicate） | `/datum/outfit/ds2/syndicate` | 辛迪加战术制服、战斗靴、Interdyne 耳机、背包（Interdyne 生存盒+NIF幽灵盒+撬棍）、黑色ID、**weapons_auth 武器授权植入体** |
| **DS-2 矿务官**（miner） | `/datum/outfit/ds2/syndicate/miner` | 辛迪加工装裤、矿石袋腰带、探险挎包（生存盒+战斗刀+采矿扫描仪+动能加速器）、采矿点卡+采矿券、黑软帽 |
| **DS-2 总务**（service） | `/datum/outfit/ds2/syndicate/service` | 战术制服+**厨师围裙**+哑剧软帽+挎包 |
| **DS-2 引擎技师**（enginetech） | `/datum/outfit/ds2/syndicate/enginetech` | 工装裤、辛迪加安保软帽、焊接护目镜（上翻）、辛迪加工具腰带、战斗手套 |
| **DS-2 研究员**（researcher） | `/datum/outfit/ds2/syndicate/researcher` | 辛迪加科研工装、科研白大褂、化学墨镜、黑手套、挎包 |
| **DS-2 医疗官**（stationmed） | `/datum/outfit/ds2/syndicate/stationmed` | 辛迪加手术服、Interdyne 白大褂、医护腰带、丁腈创伤手套、挎包（含手术医疗包） |
| **DS-2 禁闭官**（brigoff） | `/datum/outfit/ds2/syndicate/brigoff` | 辛迪加战斗服、绝缘擒抱手套、老式防弹衣、SWAT头盔(ds)、安保HUD墨镜、Sol 手枪 evil 版+弹匣、防毒面具、Interdyne 耳机 |
| **DS-2 指挥特工**（syndicate_command） | `/datum/outfit/ds2/syndicate_command` | 战术制服+指挥耳机（interdyne/command）+weapons_auth 植入体 |
| **DS-2 军械长**（masteratarms） | `/datum/outfit/ds2/syndicate_command/masteratarms` | 战斗服、看守长式辛迪加防弹背心、安保HUD、HoS 贝雷帽、手电、**weapons_auth + kaza_ruk 双植入体** |
| **DS-2 公司联络官**（corporateliaison） | `/datum/outfit/ds2/syndicate_command/corporateliaison` | 狙击手西装、**软呢帽（fedora）**、系带皮鞋 |
| **DS-2 上将**（admiral） | `/datum/outfit/ds2/syndicate_command/admiral` | 舰长式辛迪加制服、capcarapace 辛迪加甲、**APS 手枪（腰带）**、HoS 舰长帽、金色ID |
| **DS-2 机器人**（robot） | `/mob/living/silicon/robot/model/ds2` | 专属模型：阵营 辛迪加+DS2、**辛迪加 AI 律法覆盖（syndicate_override_ds2）**、超能电池、无内置摄像头、辛迪加频道广播 |

#### 特质与玩法

- **共同特性**：特工与指挥层生成时**免费获得代码语（Codespeak）**；post_equip 加入 ROLE_DS2 阵营；所有人类角色自动创建离站银行账户
- **开局设定**：「你是一名受雇于绝密生物武器研究设施的辛迪加特工。可恨的敌人纳米传讯已开始在本星区采矿。尽力运作，保持低调。」——**明确非反派**，与站内船员敌对前需 ahelp
- **囚犯玩法**：囚犯不知道自己在哪，只知道被囚禁，受标准囚犯政策约束
- **电脑区域**：特工生成在 `des_two/halls`，囚犯在 `des_two/security/prison`，普通人员在 `des_two/service/dorms`

> **一句话总结**：潜伏在 13 扇区监视 NT 的辛迪加生物武器研究船全员，从囚犯到上将一应俱全，会代码语、带武器授权植入体，规矩是「保持低调」。

---

### 十、Interdyne 行星基地 Interdyne Planetary Base（ROLE_INTERDYNE_PLANETARY_BASE）

- **源码**：`modular_nova/master_files/code/modules/jobs/job_types/spawner/interdyne_planetary_base.dm` + `modular_nova/master_files/code/modules/mob_spawn/ghost_roles/mining_roles.dm`（Interdyne 段）
- **定位**：**Interdyne 制药**（Interdyne Pharmaceuticals）设在拉瓦兰/冰月上的**行星研究基地**，研发生物武器，与辛迪加有交易，船尾停靠货运用渡轮可同时与两方贸易。

#### 基础信息表

| 项目 | 内容 |
|---|---|
| 职位路径 | `/datum/job/interdyne_planetary_base`（子类：mining / command；冰月变体 `_icebox`） |
| 职位名 | ROLE_INTERDYNE_PLANETARY_BASE「Interdyne 行星基地」 / ROLE_INTERDYNE_PLANETARY_BASE_ICEBOX（冰月版） |
| 薪水 | PAYCHECK_CREW（指挥子类 PAYCHECK_COMMAND） |
| 部门账户 | ACCOUNT_INT |
| 肝脏特质 | **TRAIT_MEDICAL_METABOLISM（医疗代谢）** |
| 悬赏类型 | DYNE_JOB_SCIENCE（采矿子类 DYNE_JOB_MINING） |
| 广播 | 指挥子类：head_announce = RADIO_CHANNEL_INTERDYNE |
| 类型 | 幽灵角色（生成器：Interdyne Scientist / Shaft Miner / Deck Officer） |

#### 子角色装备表

| 子角色 | 装备路径 | 装备内容 |
|---|---|---|
| **Interdyne 科学家**（scientist） | `/datum/outfit/interdyne_planetary_base` | Interdyne 制服、白色 Interdyne 白大褂、Interdyne 医疗贝雷帽、Interdyne 背包（生存盒+NIF幽灵盒+疾病分析仪）、绝缘擒抱手套、**Interdyne 绿色耳机（防闪光弹耳保护）**、战斗靴、左口袋手枪、右手 **Sindano 恶版枪盒**、weapons_auth 植入体 |
| **冰月科学家**（scientist/ice） | `/datum/outfit/interdyne_planetary_base/ice` | 同上但外套换**医用病毒学冬大衣（interdyne）**，白大褂收进背包 |
| **Interdyne 矿工**（shaftminer） | `/datum/outfit/interdyne_planetary_base/shaftminer` | Interdyne 矿工制服、Interdyne 夹克、矿石袋、探险背包（生存盒+手电+生存刀+采矿券+扫描仪+动能加速器+信标+采矿点卡）、**矿工技能芯片（skillchip/job/miner！）** |
| **Interdyne 甲板官**（deck_officer） | `/datum/outfit/interdyne_planetary_base/shaftminer/deckofficer` | Interdyne 甲板官制服、黑色指挥帽、**HoS 甲板官装甲**、指挥耳机、黑银变色龙卡、矿工技能芯片；生成器销毁时留下一具空冬眠舱 |

#### 特质与玩法

- **共同特性**：生成时**免费获得代码语（Codespeak）**；post_equip 加入 ROLE_INTERDYNE_PLANETARY_BASE 阵营；Interdyne 耳机（`headset/interdyne`）自带防闪光弹耳保护组件
- **开局设定**：「Interdyne 中层管理转达：纳米传讯正在本星区积极采矿。与辛迪加的协议仍然有效。船尾停靠的货运渡轮可用于与双方贸易。」（冰月版：辛迪加星舰已离开本星系，量子传送台失去用途）
- **专属医疗**：`sansufentanyl` 冷冻箱（10 瓶药，治疗遗传性流形病 Hereditary Manifold Sickness）、Interdyne 三级生化防护柜（L3 closet：生物袋+防护服+防毒面具+氧气瓶+抗病毒注射器）
- **机器人变体**：`model/interdyne` 机器人（阵营 辛迪加+Interdyne，**辛迪加覆盖律法 syndicate_override_interdyne**）

> **一句话总结**：Interdyne 制药藏在拉瓦兰/冰月的生物武器研究所，科学家+矿工+甲板官三人组，会代码语、穿防闪耳机、兜售治怪病的药。

---

### 附：NOVA 特有职业通用机制速查

| 机制 | 说明 | 涉及职业 |
|---|---|---|
| `nova_stars_only = TRUE` | 仅 NOVA 星（贡献/老玩家）可选，受 `GLOB.nova_star_restrictions` 开关控制 | 蓝盾、舰桥助理 |
| `JOB_ANTAG_PROTECTED` | 反 antagonist 保护（不会被强制转反派） | 蓝盾、舰桥助理 |
| `JOB_CANNOT_OPEN_SLOTS` | 不可手动开启额外槽位 | 蓝盾 |
| 幽灵角色生成器 | 全部通过 `/obj/effect/mob_spawn/ghost_role/` 生成，非岗位轮换 | 货船船员、黑市、塔肯、幽灵咖啡厅、DS2、Interdyne |
| `handlebank()` | 离站阵营专属银行账户创建（不可替换） | 货船、黑市、DS2、Interdyne、塔肯 |
| 代码语（Codespeak） | 生成时免费授予 | DS2 特工/指挥、Interdyne 全员 |
| 技能芯片 | 仅两处：通信专家（engineer）、Interdyne 矿工（miner） | 通信专家、Interdyne |


---

## 附录 · 职业技能芯片全录



> 数据源：`code/modules/library/skill_learning/`（job_skillchips 9 文件 + generic_skillchips 7 文件）+ `modular_nova` 追加芯片（hair_expert）。分支：NovaSector。
> 全库共 **31 枚技能芯片**：职业技能芯片 9 枚 + NOVA 追加 1 枚 + 通用技能芯片 21 枚。

---

### 〇、技能芯片系统机制（Skillchip System Overview）

技能芯片（skillchip）是一种可植入大脑的生物芯片，植入后通过"技能站"（skill station，`/obj/machinery/skill_station`）激活/停用/取出。核心数值（`code/modules/library/skill_learning/skillchip.dm`）：

| 项目 | 数值 | 说明 |
|---|---|---|
| 大脑槽位上限 `max_skillchip_slots` | **5** 槽 | `brain_item.dm:38`，硬上限 |
| 大脑复杂度上限 `max_skillchip_complexity` | **3** 复杂度 | `brain_item.dm:36`，可被种族/器官 `skillchip_complexity_modifier` 修正 |
| 植入/取出耗时 | **15 秒** | `skill_station.dm:2-3`（SKILLCHIP_IMPLANT_TIME / REMOVAL_TIME） |
| 芯片默认冷却 | **5 分钟** | `skillchip.dm:39`（cooldown，激活/停用后进入冷却，期间不可再次切换） |
| 芯片复杂度（默认） | 1 | 激活态芯片计入；复杂度超限自动停用最旧芯片（`update_skillchips`） |
| 类别（chip_category） | `general` / `job` | `skills.dm:56-57`；职业芯片互斥（`SKILLCHIP_RESTRICTED_CATEGORIES` + `incompatibility_list`） |
| 特质来源 `SKILLCHIP_TRAIT` | `"skillchip"` | `traits/sources.dm:175`；激活时 `add_traits(auto_traits, SKILLCHIP_TRAIT)`，停用时移除（`skillchip.dm:157-158, 195-196`） |
| 可重复植入 | 默认禁止同型多枚 | 除非 `SKILLCHIP_ALLOWS_MULTIPLE` 标志（`_flags.dm:304-306`） |

**职业芯片统一设定**（`job_skillchips/_job.dm`）：`/obj/item/skillchip/job` 抽象父类型 —— `chip_category = SKILLCHIP_CATEGORY_JOB`、`skillchip_flags = SKILLCHIP_RESTRICTED_CATEGORIES`、`incompatibility_list = list(SKILLCHIP_CATEGORY_JOB)`、**`slot_use = 2`**（每个职业芯片占 2 个大脑槽位，且所有"职业类"芯片互斥，脑内只能存在一枚）。复杂度均为默认 1。

**激活流程**：`on_activate()` → ① 播放 activate_message → ② `add_traits(auto_traits, SKILLCHIP_TRAIT)` → ③ `active = TRUE` → ④ 授予 actions → ⑤ 进入 5 分钟冷却。

---

### 一、职业技能芯片（Job Skillchips，9 枚）

#### 1. B0RK-X3 —— 厨师（Chef / Cook）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/job/chef`（`job_skillchips/chef.dm`） |
| 技能名 | Close Quarters Cooking（厨房近身格斗） |
| 所属职业 | 厨师 Cook（`job_types/cook.dm:82` 开局自带） |
| 植入槽位 | 2 槽（职业类），复杂度 1 |
| auto_traits | **无**（不通过 trait 生效，通过自定义 on_activate 生效） |
| 描述 | "This biochip faintly smells of garlic... 这枚生物芯片隐约有股大蒜味。" |

**效果说明**：激活时 `style.teach(user)` 传授**地下 CQC 格斗术**（`/datum/martial_art/cqc/under_siege`，即 "Close Quarters Cooking"）；停用时 `style.unlearn(user)` 收回。该格斗术**仅在厨房区域可用** —— `can_use()` 检查 `kitchen_areas`（默认 `/area/station/service/kitchen`，并可通过地图配置 `CHECK_MAP_JOB_CHANGE(JOB_COOK, "additional_cqc_areas")` 追加区域，`cqc.dm:396-428`）。CQC 连招体系：SLAM（摔投）、KICK（踢击）、RESTRAIN（压制）、PRESSURE（压力）、CONSECUTIVE（连击）组合技。

```dm
// chef.dm:10-19
var/datum/martial_art/cqc/under_siege/style
/obj/item/skillchip/job/chef/Initialize(mapload)
	. = ..()
	style = new(src)
	style.refresh_valid_areas()
/obj/item/skillchip/job/chef/on_activate(mob/living/carbon/user, silent = FALSE)
	. = ..()
	style.teach(user)
```

**直观理解**：戴上芯片后在厨房里化身功夫大厨，近战连招（摔、踢、擒拿、压制）打遍厨房无敌手；出了厨房就失效。不是刀术芯片，而是"厨房格斗术"。

---

#### 2. B@L00NY —— 小丑（Clown）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/job/clown`（`job_skillchips/clown.dm`） |
| 技能名 | Balloon Sutra（气球经） |
| 所属职业 | 小丑 Clown（`job_types/clown.dm:77` 开局自带；NOVA 货运补给 `costumes_toys.dm:279` 可购） |
| 植入槽位 | 2 槽（职业类），复杂度 1 |
| auto_traits | `TRAIT_BALLOON_SUTRA`（"balloon_sutra"，`traits/declarations.dm:784`） |

**TRAIT_BALLOON_SUTRA 逐个分析**：

- **① 气球动物制作**（`toys.dm:179`）：手持两根**不同颜色**的长气球（`/obj/item/toy/balloon/long`）互相敲击，可现场把气球扭成**气球动物**并收入手中，两根气球随之消耗。颜色组合表（`toys.dm:164-172`）：
  - 绿+橙 → 蜥蜴气球（lizard）；绿+紫 → 史莱姆（slime）；黄+橙 → 飞蛾（moth）；黄+紫 → 以太人（ethereal）；橙+紫 → 塑形人（plasmaman）。
  - 同色组合会被拒绝（"That won't work"）。
```dm
// toys.dm:178-179
if(!istype(tool, /obj/item/toy/balloon/long) || !HAS_TRAIT(user, TRAIT_BALLOON_SUTRA))
	return ..()
```
- **② 气球木槌彩蛋**（`clown_items.dm:329`）：持有该特质时检查气球木槌（`/obj/item/balloon_mallet`）会看到额外描述文本（纯趣味）。木槌本身伤害随目标理智值变化：SANITY_INSANE~CRAZY 时 force=8，其余 force=4 并附加"受辱"心情事件。

**直观理解**：小丑的核心整活芯片 —— 有了它就能把两根长气球扭成各种物种的气球动物送人/装饰，是职业乐趣型芯片。当前代码库中小丑仅有这一枚职业芯片（无其他小丑芯片）。

---

#### 3. DET.ekt —— 侦探（Detective）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/job/detectives_taste`（`job_skillchips/detective.dm`） |
| 技能名 | Detective's Taste（侦探之味） |
| 所属职业 | 侦探 Detective（`job_types/detective.dm:79` 开局自带） |
| 植入槽位 | 2 槽（职业类），复杂度 1 |
| auto_traits | `TRAIT_DETECTIVES_TASTE`（"detectives_taste"，`traits/declarations.dm:786`） |

**TRAIT_DETECTIVES_TASTE 逐个分析**：

- **① 品尝识别精确试剂名**（`reagents.dm:304-307`，`/datum/reagent/proc/get_taste_description`）：正常角色品尝液体时只会得到该试剂的风味描述（如"辛辣的""甜腻的"）；而持有本特质者，返回的味觉描述**直接是该化学物质的小写名称**（`LOWER_TEXT(name)`）。喝一口任何液体，就能从品尝消息中精确读出它含有什么试剂 —— 也就是"用嘴做化学分析"。
```dm
// reagents.dm:303-307
/datum/reagent/proc/get_taste_description(mob/living/taster)
	if(isnull(taster) || !HAS_TRAIT(taster, TRAIT_DETECTIVES_TASTE))
		return list("[taste_description]" = 1)
	return list("[LOWER_TEXT(name)]" = 1)
```

**直观理解**：抿一口就能报出毒药/药剂/酒水的准确化学成分，破案利器 —— 不需要光谱仪，舌头就是质谱仪。

---

#### 4. CL34NM4ST.R —— 清洁工（Janitor）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/job/janitor`（`job_skillchips/janitor.dm`） |
| 技能名 | Voice Of The Voiceless（无声者的声音） |
| 所属职业 | 清洁工 Janitor（`job_types/janitor.dm:42` 开局自带） |
| 植入槽位 | 2 槽（职业类），复杂度 1 |
| auto_traits | `TRAIT_CLEANBOT_WHISPERER`（"cleanbot_whisperer"，`traits/declarations.dm:1431`） |

**TRAIT_CLEANBOT_WHISPERER 逐个分析**：

- **① 清洁机器人认主**（`cleanbot_ai.dm:186`，`/datum/ai_behavior/find_and_set/friendly_janitor/search_tactic`）：清洁机器人 AI 的"寻找友好清洁工"行为会在视野内搜索持有 `TRAIT_CLEANBOT_WHISPERER` 的**清醒且有意识**的人类（`stat == CONSCIOUS` 且非无 mind），找到后将其设为 `BB_FRIENDLY_JANITOR` 并结为盟友（`has_ally`）。搜索冷却 30 秒（`action_cooldown = 30 SECONDS`）。
```dm
// cleanbot_ai.dm:186
if(!HAS_TRAIT(human_target, TRAIT_CLEANBOT_WHISPERER))
	continue
```

**直观理解**：戴上后全站的清洁机器人都会把你当"自己人"，主动凑过来跟随你、把你设为友好目标 —— 你就是机器人的"清洁工之王"。

---

#### 5. TUNN3L_R4T —— 矿工（Miner）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/job/miner`（`job_skillchips/miner.dm`） |
| 技能名 | Battlebot enthusiast（战斗机器人爱好者） |
| 所属职业 | 矿工 Shaft Miner（`job_types/shaft_miner.dm:37` 开局自带；NOVA 幽灵角色矿工 `mining_roles.dm:208,227` 也自带） |
| 植入槽位 | 2 槽（职业类），复杂度 1 |
| auto_traits | `TRAIT_ROCK_STONER`（"rock_stoner"，`traits/declarations.dm:1434`） |

**TRAIT_ROCK_STONER 逐个分析**：

- **① 矿机认主**（`minebot_ai.dm:68`，`/datum/ai_behavior/find_and_set/miner_to_befriend/search_tactic`）：矿机 AI 的"结交矿工"行为子树（`befriend_miners`）会在视野内寻找持有 `TRAIT_ROCK_STONER` 的人类，将其设为 `BB_MINER_FRIEND`，随后执行"结交目标"（`befriend_target`）与之结盟 —— 矿机从此跟随你、协助你挖矿与作战。
```dm
// minebot_ai.dm:67-71
/datum/ai_behavior/find_and_set/miner_to_befriend/search_tactic(datum/ai_controller/controller, locate_path, search_range = SEARCH_TACTIC_DEFAULT_RANGE)
	for(var/mob/living/carbon/human/target in oview(search_range, controller.pawn))
		if(HAS_TRAIT(target, TRAIT_ROCK_STONER))
			return target
```

**直观理解**：矿机（minebot）把你当指挥官，自动跟随并保护你 —— "带领矿机去打仗"名副其实。

---

#### 6. HYPERG1G4 —— 心理学家（Psychologist）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/job/psychology`（`job_skillchips/psychologist.dm`） |
| 技能名 | Supermatter Cognition Theory（超物质认知理论） |
| 所属职业 | 心理学家 Psychologist（`job_types/psychologist.dm:61` 开局自带） |
| 植入槽位 | 2 槽（职业类），复杂度 1 |
| auto_traits | `TRAIT_SUPERMATTER_SOOTHER` + `TRAIT_MADNESS_IMMUNE`（双特质） |

**TRAIT_MADNESS_IMMUNE 逐个分析**（"supermatter_madness_immune"，`traits/declarations.dm:596`）：

- **① 超物质幻觉免疫**（`hallucinations.dm:87` `visible_hallucination_pulse` / `:114` `hallucination_pulse`）：两种致幻脉冲函数都会跳过持有该特质的活体 —— 超物质（supermatter）的"疯癫光环"（幻觉脉冲，`hallucination_duration = internal_energy * hallucination_power`，上限 400 秒）对你不生效。注意 `hallucination_pulse` 连**盲人**都影响，但同样跳过你。
- **② 检查超物质无警告**（`supermatter.dm:259`）：`examine()` 中，若处于 SM 幻觉范围内（`get_dist < SM_HALLUCINATION_RANGE(internal_energy)`）会附加危险警告文本（"你感到仿佛有什么东西在凝视你的灵魂……"），持有本特质者**不会看到**该警告。
- **③ 附带来源**：神器眼（Eye of God，`eye_of_god.dm:8`）服装也提供该特质；异教徒月之传说等场景同样引用。

**TRAIT_SUPERMATTER_SOOTHER 逐个分析**（"supermatter_soother"，`traits/declarations.dm:600`）：

- **① 心理安抚系数**（`supermatter_extra_effects.dm:77-84`，`psychological_examination()`）：每 tick，SM 扫描幻觉范围内的人类 —— 只要有**一名**持此特质者（一般为心理学家）在注视 SM，`psy_coeff_diff` 就从默认 **-0.05**（衰减）变为 **+0.05**（增长），使 SM 的 `psy_coeff`（0~1 钳制）持续上升。
- **② psy_coeff 的实际收益**（`supermatter.dm`）：
  - `SM_POWER_POWERLOSS_SOOTHED`（:718）= `-min(1 - gas_powerloss_inhibition, 0.2 * psy_coeff) × 功率流失` —— **减少功率流失**（最高削减 20%）；
  - `SM_WASTE_SOOTHED`（:793）= `-0.2 * psy_coeff` —— **减少废料（waste）产生**（最高 -20%）；
  - `SM_TEMP_LIMIT_SOOTHED`（:815）= `psy_coeff × 45` —— **热容极限 +45K/满系数**（`(T0C+40) × (1 + 燃气热容 + psy_coeff)` 公式中的额外加成）；
  - 视觉（:515-516）：SM 图标叠加 `-psy` 图层，透明度 `psy_coeff × 255` —— SM 看起来更"平静"。

**直观理解**：心理学家专属人形镇定剂 —— 站在超物质旁边注视它，就能让 SM 掉功率更慢、产废料更少、更耐热；同时自己完全免疫 SM 的致幻疯癫（不中幻觉、无警告文本）。这是**全库唯一一枚双 auto_traits 的职业芯片**。

---

#### 7. R.D.S.P.L.X. —— 研究主管（Research Director）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/research_director`（`job_skillchips/research_director.dm`） |
| 技能名 | True Strength（真·力量） |
| 所属职业 | 研究主管 RD（`job_types/research_director.dm:80-82` 开局自带，连同 roboticist 芯片一起发放） |
| 植入槽位 | **1 槽**，复杂度 1（**特殊**：`chip_category = SKILLCHIP_CATEGORY_GENERAL`、`skillchip_flags = NONE` —— 不属职业类，可与任何芯片共存，只占 1 槽而非 2 槽） |
| auto_traits | `TRAIT_ROD_SUPLEX` + `TRAIT_STRENGTH`（双特质） |

**TRAIT_ROD_SUPLEX 逐个分析**（"rod_suplex"，`traits/declarations.dm:764`）：

- **① 徒手摔停不可阻挡之棒**（`immovable_rod.dm:224-237`）：事件"不可阻挡之棒"（immovable rod）碾压全站时，持此特质者可以**徒手攻击（attack_hand）它**触发 `suplex_rod()` —— 播放流星撞击音效、周围 8 格内所有清醒者镜头震动，随后把之棒**过肩摔**！结果：之棒被摧毁，原地生成 `festivus` 锚点结构 + `flux` 异常；摔棒者获得成就 `feat_of_strength`，并根据之棒击杀的智慧生物数量获得运动经验 `100 × num_sentient_mobs_hit`（25 只即秒变传奇运动员），重重力环境经验翻倍，并获得 `exercised` 状态（需要小憩）。这正是芯片描述里的"不可阻挡之力 vs 不可移动之物"谜题的答案。
```dm
// immovable_rod.dm:224-231
if(!HAS_MIND_TRAIT(user, TRAIT_ROD_SUPLEX))
	return
playsound(src, 'sound/effects/meteorimpact.ogg', 100, TRUE)
for(var/mob/living/nearby_mob in urange(8, src))
	if(nearby_mob.stat != CONSCIOUS)
		continue
	shake_camera(nearby_mob, 2, 3)
return suplex_rod(user)
```
- **② 体能倍率**（`human_helpers.dm:338`，`calculate_fitness()`）：体能修正 **×2**（"能摔停之棒，必有超凡之力"）。

**TRAIT_STRENGTH 逐个分析**（"strength"，`traits/declarations.dm:697`）：

- **① 拳击伤害 +2**（`boxing.dm:158`）：拳击格斗术中，持有 `TRAIT_STRENGTH` 的攻击者获得 **+2 伤害**加成（`strength_bonus = HAS_TRAIT(attacker, TRAIT_STRENGTH) ? 2 : 0`，"投资基因级力量强化会让你成为更好的拳手"）。
- **② 体能倍率**（`human_helpers.dm:336`）：`calculate_fitness()` 中体能修正 **×1.5**。
- **③ 其他来源**：拳击流派默认自带 `TRAIT_BOXING_READY, TRAIT_STRENGTH, TRAIT_STIMMED`（`boxing.dm:409`）。

**直观理解**：RD 的"大力士"芯片 —— 最亮眼的是能**徒手过肩摔不可阻挡之棒**（全站唯一手段，棒杀生物越多经验越肥），平时还提供拳击 +2 伤害与 1.5 倍体能。因归类为通用类，可与一枚职业芯片共存（如 RD 自带的 roboticist 芯片）。

---

#### 8. Cyborg C1-RCU-1T —— 机器人技术员（Roboticist）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/job/roboticist`（`job_skillchips/roboticist.dm`） |
| 技能名 | Cyborg Circuitry（机仆电路学） |
| 所属职业 | 机器人技术员 Roboticist（`job_types/roboticist.dm:53` 开局自带；`job_boxes.dm:312-313` 储物盒×2；RD 开局也附带） |
| 植入槽位 | 2 槽（职业类），复杂度 1 |
| auto_traits | `TRAIT_KNOW_ROBO_WIRES`（"know_robo_wires"，`traits/declarations.dm:775`） |

**TRAIT_KNOW_ROBO_WIRES 逐个分析**：

- **① 机仆线路透视**（`robot.dm:111`，`/datum/wires/robot/can_reveal_wires`）：持有该特质时 `can_reveal_wires()` 直接返回 TRUE —— 在破译/黑客界面中**每根线的功能始终可见**（相当于不随机线路时揣着蓝图，或手持外星多功能工具的效果，见 `_wires.dm:277-295`）。
- **② 机甲线路透视**（`mecha.dm:108`）：对机甲（mecha）内部线路同样生效。
- **③ MOD 套装线路透视**（`mod.dm:61`）：对 MOD（模块化外骨骼）线路生效。

**直观理解**：一眼看穿机仆、机甲、MOD 的全部线路功能 —— 修机器人、拆机甲、洗脑机仆（改线）如虎添翼，闭着眼都知道哪根线是干什么的。

---

#### 9. Engineering C1-RCU-1T —— 站台工程师（Station Engineer）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/job/engineer`（`job_skillchips/station_engineer.dm`） |
| 技能名 | Engineering Circuitry（工程电路学） |
| 所属职业 | 站台工程师 Station Engineer（`job_types/station_engineer.dm:66` 开局自带）、总工程师 CE（`chief_engineer.dm:86`）、ERT 工程师（`ert.dm:141,555`）；`job_boxes.dm:320-321` 储物盒×2；NOVA：货运市场出售（"Engineer skillchip"，沾血的二手货，`markets/market_items/misc.dm:66-68`）、反叛军植入包（`opposing_force/implants.dm:6`）、电信专家（`telecomms_specialist.dm:72`）、补给船/穿梭机（`mapping/shuttles.dm:153,178`） |
| 植入槽位 | 2 槽（职业类），复杂度 1 |
| auto_traits | `TRAIT_KNOW_ENGI_WIRES`（"know_engi_wires"，`traits/declarations.dm:776`） |

**TRAIT_KNOW_ENGI_WIRES 逐个分析**：

- **① 气闸线路透视**（`airlock.dm:228`）：`/datum/wires/airlock/can_reveal_wires` → TRUE，气闸（airlock）破译界面直接显示每根线功能。
- **② APC 线路透视**（`apc.dm:90`）：APC（电力控制面板）线路全部可见 —— 断电、改线、修电力一目了然。
- **③ 空气警报线路透视**（`airalarm.dm:78`）：空气警报器（air alarm）线路可见。

**直观理解**：工程师核心芯片 —— 气闸/APC/空气警报三大工程设备的线路图全开，黑客面板里每根线的功能直接标注，拆解和改造效率碾压无芯片者。"Poly 代言"（Endorsed by Poly，Poly 是 CE 的鹦鹉）。

---

### 二、NOVA 追加技能芯片（Modular Nova Additions，1 枚）

#### 10. H41R 3XP3R7 —— 理发专家（Hair Expert）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/hair_expert`（`modular_nova/modules/hairbrush/code/skillchip.dm`） |
| 技能名 | Hair expert（美发专家） |
| 所属职业 | 理发师 Barber（NOVA 沙龙职业，`modular_nova/modules/salon/code/barber.dm:36` 开局自带；理发自动售货机有售，`barbervend.dm:28`，库存 2 枚） |
| 植入槽位 | 1 槽（通用类），复杂度 1 |
| auto_traits | `TRAIT_HAIR_EXPERT`（"hair_expert"，`~nova_defines/traits/declarations.dm:185`，NOVA 独有特质） |

**TRAIT_HAIR_EXPERT 逐个分析**（NOVA 模块内使用点）：

- **① 梳头心情加成**（`hairbrush/code/hairbrush.dm:77,84`）：用梳子梳头时，持此特质者获得**更好的心情加成**（与 `TRAIT_SELF_AWARE` 同级的优质心情事件，而非普通心情事件）。
- **② 剪发速度提升**（`salon/code/scissors.dm:55,80`）：用理发剪刀理发时显著**缩短理发耗时** —— 芯片描述 "Cut hair faster!"（剪得更快），让你成为名副其实的专家理发师。

**直观理解**：NOVA 沙龙系统的配套芯片 —— 戴上后梳头心情更好、理发更快，理发师职业体验直接升级。

---

### 三、通用技能芯片（Generic Skillchips，21 枚，全量）

通用芯片均属 `SKILLCHIP_CATEGORY_GENERAL`，默认 1 槽 1 复杂度（个别除外），彼此间无类别互斥，但可与一枚职业芯片共存。

#### 11. F058UR7 —— 杂技（Acrobatics / "Spinesthetics"）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/acrobatics`（`generic_skillchips/acrobatics.dm`） |
| auto_traits | 无（自定义信号系统） |

- **效果**：激活后注册 `COMSIG_MOB_EMOTE_COOLDOWN_CHECK`，**spin（旋转）/ flip（空翻）/ backflip（后空翻）三种表情无视冷却**，可无限连翻。
- **耐久机制**：芯片有 `max_integrity = 100` 耐久与 `allowed_usage = 5` 次免费额度，每 10 秒回充 1 次（`reload_charge = 10 SECONDS`）。免费额度用尽后每次花式动作**扣 1 点芯片耐久**；激活期间芯片以 **1%/SSobj tick** 速率再生（`/datum/element/obj_regen, 0.01`）。
- **故障事故**（`whowee()`，损坏后按 `rand(mintegrity, integrity)` 判定，最低值随耐久下降而降低）：
  - **roll 1（大失败）**：原地**爆炸**（light_impact_range=2）、颅骨裂隙伤（cranial fissure）、血溅周围 2 格（溅血者 Stun 1s + Knockdown 2s + 视线模糊 15s + 混乱 4s）、头部器官掉落、`gibspawner` 特效，芯片销毁；
  - **roll 7-9**：触电 15（可致混乱 15s、模糊 10s），并造成 `20 - 当前耐久` 点**脑损伤**；
  - **roll 13-15**：大脑过热冒烟（`chip_overheat` 状态 15s），过热叠加则**点火燃烧**（火堆 11 - 耐久）；
  - **roll 16-50**：轻微电火花特效（纯视觉警告）。
- **K1SS 变体**（`/obj/item/skillchip/acrobatics/kiss`，"prototype N. 807 - K1SS"）：只影响 **kiss 表情**，免费额度仅 1 次、30 秒回充，`max_integrity = 25`，标价 `PAYCHECK_CREW × 500` —— 一枚亲吻就会烧坏大脑的实验性废案。
- **直观理解**：杂技演员芯片 —— 无限转圈翻跟头，翻多了芯片会烧坏并让你原地爆炸/触电/脑冒烟。欢乐与作死并存。

#### 12. BULLET_DODGER —— 嘲讽闪避（Matrix Taunt / "Taunt 2 Dodge"）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/matrix_taunt`（`generic_skillchips/matrix_taunt.dm`） |
| auto_traits | 无（自定义信号系统；运行时临时授予 `TRAIT_UNHITTABLE_BY_PROJECTILES`） |

- **效果**：激活后注册 `COMSIG_MOB_EMOTED("taunt")`。**每次嘲讽（taunt 表情）消耗 19 点体力**（`TAUNT_STAMINA_COST`），获得持续 `TAUNT_EMOTE_DURATION × 1.5`（约 1.4 秒 × 1.5 ≈ 2.1 秒）的**子弹免疫**（`TRAIT_UNHITTABLE_BY_PROJECTILES`，`projectile.dm:639` 处实弹命中判定直接跳过）。
- **防自残**（`check_if_we_can_taunt`）：若嘲讽后体力将跌破濒死阈值（`maxHealth - (体力流失 + 19) <= crit_threshold`），**阻止嘲讽**并提示 —— 防止玩家为躲子弹把自己体力耗到倒地。
- **获得途径**：NOVA 忍者反派开局自带（`ninja/outfit.dm:16`）；巫师技能书也提供同款临时特质（`spellbook_entries/perks.dm:139`）。
- **直观理解**：挑衅即无敌帧 —— 嘲讽一下，1.4 倍嘲讽时长内子弹全部打空，Matrix 式躲子弹。

#### 13. WINE —— 品酒师（Wine Tasting）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/wine_taster`（`generic_skillchips/misc.dm:4`） |
| auto_traits | `TRAIT_WINE_TASTER`（"wine_taster"，`traits/declarations.dm:772`） |

- **效果**（`alcohol_reagents.dm:421-427`）：品尝葡萄酒时，`get_taste_description` 直接返回该酒**精确的年份/品种**（`data["vintage"]`，如 "1972 vintage"）；非年份数据则显示 "synthetic wine"（合成酒）。普通角色只能尝出"这是葡萄酒"。
- **额外信息**：蜥蜴人种族天生自带该特质（`lizardpeople.dm:217`）。
- **直观理解**：品一口就知道是哪年哪个酒庄的酒 —— 酒桌上永远不缺谈资。

#### 14. Hedge 3 —— 园艺修剪（Hedgetrimming）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/bonsai`（`generic_skillchips/misc.dm:14`） |
| auto_traits | `TRAIT_BONSAI`（"bonsai"，`traits/declarations.dm:773`） |

- **效果**：用**任何带刃工具**（`tool.get_sharpness()`）修剪盆栽/树篱（`kirbyplants.dm:61`、`hedges.dm:51`）时，可将它们修成**各种形状**（园艺造型功能）。塑料植物不可修剪。
- **直观理解**：园丁芯片 —— 拿把刀就能把盆栽剪成狮子、飞船等造型。

#### 15. Skillchip adapter —— 无用适配器（Useless adapter）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/useless_adapter`（`generic_skillchips/misc.dm:24`） |
| auto_traits | 无 |

- **效果**：**`complexity = 0`、`slot_use = 0`**、`SKILLCHIP_ALLOWS_MULTIPLE` —— 不占任何槽位和复杂度，且允许同型多枚。描述："Yo dawg, heard you like skillchips so we put a skillchip in your skillchip..."（听说你喜欢芯片，所以我们在你的芯片里塞了芯片……）。**字面意义上的什么都不干**。
- **直观理解**：纯搞笑纪念品芯片，白占脑内"接口"但零开销。

#### 16. N16H7M4R3 —— 灯泡拆除（Lightbulb Removing）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/light_remover`（`generic_skillchips/misc.dm:37`） |
| auto_traits | `TRAIT_LIGHTBULB_REMOVER`（"lightbulb_remover"，`traits/declarations.dm:774`） |

- **效果**：
  - **拆灯泡不怕烫**（`light.dm:687`）：徒手拆热灯泡时不再被劝退 —— 可以忍痛完成 5 秒拆除流程（拆灯管，`do_after 5 SECONDS`），代价是拆完后手部受到 **10 点烧伤**（流程中再被烫 5 点）；普通角色第一次触碰就会被烫伤并拒绝继续。若持 `TRAIT_RESISTHEAT`/隔热手套则完全无伤。
  - **空手接滚烫弹壳**（`ballistic.dm:401`）：射击后飞出的滚烫弹壳可以**直接用手接住**（`CASING_CATCH_SUCCESSFUL_OUCH`，接住但手被烫），普通角色会被烫得接不住。
- **NOVA 获得途径**：夜视模块商店可购（`night_vision.dm:13`）。
- **直观理解**：不怕烫手的换灯工 —— 拆灯泡、接弹壳都不再被烫得缩手。

#### 17. K33P-TH4T-D15K —— 核弹磁盘鉴定（Nuclear Disk Verification）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/disk_verifier`（`generic_skillchips/misc.dm:47`） |
| auto_traits | `TRAIT_DISK_VERIFIER`（"disk-verifier"，`traits/declarations.dm:396`） |

- **效果**（`nuclear_authentication_disk.dm:71`）：检查核认证磁盘（`/obj/item/disk/nuclear`）时，能**自动识别赝品** —— 假盘会额外显示警告文本（与观察者视角相同）。芯片内存储着真正的超长序列号，供逐位比对。
- **获得途径**：舰长开局自带（`job_types/captain.dm:93`）；NOVA：纳米特拉斯顾问（`nanotrasen_consultant.dm:59`）与 ERT 装备（`ert.dm:22`）自带。
- **直观理解**：防伪造专家 —— 拿到假核弹盘一眼识破，反恐必备。

#### 18. 3NTR41LS —— 内脏解读（Entrails Reader）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/entrails_reader`（`generic_skillchips/misc.dm:57`） |
| auto_traits | `TRAIT_ENTRAILS_READER`（"entrails_reader"，`traits/declarations.dm:777`） |

- **效果**：检查**器官**（`_organ.dm:205`）、**肝脏**（`_liver.dm:93`）、**舌头**（`_tongue.dm:65`）时获得**类似观察者/幽灵的解读信息** —— 能看出器官是否为原生（`TRAIT_CLIENT_STARTING_ORGAN`）、死因线索等。"通过看内脏了解一个人的一生"。
- **获得途径**：CMO（`chief_medical_officer.dm:84`）、法医（`coroner.dm:75`）、医生（`medical_doctor.dm:62`）开局自带。
- **直观理解**：法医之眼 —— 打开肚子就能"读"出死者的生平与死因。

#### 19. GENUINE ID Appraisal Now! —— 证件鉴定（ID Appraisal）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/appraiser`（`generic_skillchips/misc.dm:66`） |
| auto_traits | `TRAIT_ID_APPRAISER`（"id_appraiser"，`traits/declarations.dm:399`） |

- **效果**（`cards_ids.dm:898-907`）：检查 ID 卡时能分辨**中央司令部签发**（`TRAIT_JOB_FIRST_ID_CARD`，显示 "this ID was issued from Central Command!"）与**本区自制**（"created in this sector"）。额外彩蛋：若是"厚实 ID"（`TRAIT_TASTEFULLY_THICK_ID_CARD`）会触发精神污染事件 —— 心跳音效、抖动 10 秒、ID 卡 10 秒后脱手（NODROP 期间）。
- **直观理解**：一眼鉴别 ID 真伪与来源 —— 行政/安保排查利器。

#### 20. Le S48R4G3 —— 军刀开瓶（Sabrage Proficiency）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/sabrage`（`generic_skillchips/misc.dm:76`） |
| auto_traits | `TRAIT_SABRAGE_PRO`（"sabrage_pro"，`traits/declarations.dm:778`） |

- **效果**（`glassbottle.dm:703-704`）：用军刀/利刃开香槟（sabrage）时，成功率获得 **+35% 固定加成**（`skillchip_bonus = HAS_TRAIT(user, TRAIT_SABRAGE_PRO) ? 35 : 0`）。基础公式：`(武器 force × sabrage_success_percentile) + 指挥官肝脏代谢加成(20) + 芯片加成(35)`，例：上尉军刀 force=15 → 75% + 35% = 110% 必然成功。成功率越高，浪费的泡沫越少。
- **直观理解**：军官派对开瓶神技 —— 挥剑开香槟几乎必成，泡沫飞溅还优雅。

#### 21. suspicious —— 洗脑（Brainwashing）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/brainwashing`（`generic_skillchips/misc.dm:87`） |
| auto_traits | `TRAIT_BRAINWASHING`（"brainwashing"，`traits/declarations.dm:780`） |

- **效果**：
  - **洗衣机洗脑**（`brain_item.dm:490`，`machine_wash()`）：把大脑放进洗衣机清洗时，若操作者持本特质，大脑会被**完美洗脑** —— 脑损伤清零（`set_organ_damage(0)`）+ **治愈全部脑创伤（含 lobotomy 级）**（`cure_all_traumas(TRAUMA_RESILIENCE_LOBOTOMY)`）；否则洗衣机直接杀死大脑（`BRAIN_DAMAGE_DEATH`）。
  - **洗衣机标记**（`washing_machine.dm:448-449`）：操作洗衣机时给洗衣机本身打上洗脑标记（触发上述流程）。
- **代价与警告**（`misc.dm:96-103`）：检查时显示 "WARNING: The integrity of this chip is compromised. Please discard this skillchip."；**激活瞬间直接造成 20 点脑损伤**（`adjust_organ_loss(ORGAN_SLOT_BRAIN, 20)`）—— 芯片本身是坏的。
- **直观理解**：坏掉的"洗脑芯片" —— 激活先自损 20 脑损伤，但换来了用洗衣机批量洗脑/洗去创伤的能力（可洗掉心灵屏蔽类创伤？至少 lobotomy 级全部可愈）。

#### 22. K1SS —— 大厨之吻（Chef's Kiss）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/chefs_kiss`（`generic_skillchips/misc.dm:105`） |
| auto_traits | `TRAIT_CHEF_KISS`（"chefs_kiss"，`traits/declarations.dm:782`） |

- **效果**（`emote.dm:276-278`）：使用 **kiss 表情**时，生成的亲吻道具升级为**大厨之吻**（`/obj/item/hand_item/kisser/chef`，替代普通 kisser）。芯片描述："Allows you to kiss food you've created to make them with love."（可以亲吻自己做的食物，注入爱心）。
- **直观理解**：亲亲表情变成厨师特供"爱心之吻" —— 风味道具彩蛋。

#### 23. INTJ —— 心灵味觉演算（Mental Flavour Calculus）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/intj`（`generic_skillchips/misc.dm:115`） |
| auto_traits | `TRAIT_REMOTE_TASTING`（"remote_tasting"，`traits/declarations.dm:346`） |

- **效果**：
  - **检查即品尝**（`edible.dm:304-311`）：检查食物时自动触发 `checkLiked` + `taste_container` —— 隔着屏幕"脑内品尝"食物，还能判断咸淡（无盐提示）。
  - **检查容器即品尝**（`reagent_containers.dm:93-95`）：检查任何试剂容器（杯子/瓶子）时直接尝出其内容物。
  - **副作用**（`supermatter.dm:264-267`）：检查超物质时会"尝到"它 —— 被电击 15 伤害（`electrocute_act(15, SHOCK_KNOCKDOWN|SHOCK_NOGLOVES)`）。
- **直观理解**：脑内味觉模拟器 —— 看一眼食物/饮品就知道味道；但别拿它去"品尝"超物质。

#### 24. F0RC3 4DD1CT10N —— 醉拳（Drunken Unarmed Proficiency）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/drunken_brawler`（`generic_skillchips/misc.dm:124`） |
| auto_traits | `TRAIT_DRUNKEN_BRAWLER`（"drunken brawler"，`traits/declarations.dm:727`） |

- **效果**：
  - **醉酒必中**（`_species.dm:857`）：只要处于醉酒状态（无论醉意高低），徒手攻击**永不 miss**（除非目标躺地等特殊情况）—— "Drunken brawlers while drunk also don't miss"。
  - **醉酒枪械不飘**（`drunk.dm:137-141`）：醉酒射击时本应叠加的散布惩罚（`bonus_spread += drunk_value × 0.5`）被**完全豁免**。
  - **挣脱抓取强化**（`drunk.dm:147-152`）：被抓取时挣脱（grabbed_resisting）获得 `GRAB_STAT_EFFECTIVE_STATE +1`，且挣脱失败时对抓取者造成 `clamp((火伤+钝伤)/10, 3, 20)` 点伤害。
- **获得途径**：调酒师开局自带（`job_types/bartender.dm:65`）。
- **直观理解**：醉拳芯片 —— 越醉越能打（拳拳必中）、开枪不飘、挣脱有力；代价是肝还是你的肝。

#### 25. Mast-Angl-Er —— 钓鱼大师（Fisherman's Discernment）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/master_angler`（`generic_skillchips/misc.dm:134`） |
| auto_traits | `TRAIT_REVEAL_FISH` + `TRAIT_EXAMINE_FISHING_SPOT` + `TRAIT_EXAMINE_FISH` + `TRAIT_EXAMINE_DEEPER_FISH`（**四特质芯片**，全库最多） |
| actions | `/datum/action/cooldown/fishing_tip`（"Dispense Fishing Tip"，2.5 秒冷却，随机发送一条钓鱼谚语） |

- **TRAIT 逐个分析**：
  - `TRAIT_REVEAL_FISH`（"reveal_fish"）：钓鱼小游戏中**直接显示咬钩鱼类的图标**（`fishing_minigame.dm:428`，默认是问号/通用图标）。
  - `TRAIT_EXAMINE_FISHING_SPOT`（"examine_fishing_spot"）：**检查钓点**时列出该水域可钓到的鱼种名单（`fishing_spot.dm:42-49`、`turf.dm:864-872`、`bait.dm:98`）。
  - `TRAIT_EXAMINE_FISH`（"examine_fish"）：**检查鱼获**时显示详细鱼种信息（`_fish.dm:555`、`fishing_rod.dm:133`、`adjust_fishing_difficulty.dm:58`）。
  - `TRAIT_EXAMINE_DEEPER_FISH`（"examine_deeper_fish"）：鱼获信息中显示**精确的难度修正百分比**（`adjust_fishing_difficulty.dm:70`、`_fish.dm:571`）。
- **直观理解**：钓鱼佬全能芯片 —— 看钓点知鱼种、看咬钩知大小、看鱼获知参数，附赠钓鱼谚语播放器。

#### 26. T4RG3T.bin —— 无畏投放员（Dauntless Disposaler）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/disposals`（`generic_skillchips/misc.dm:159`） |
| auto_traits | `TRAIT_THROWINGARM`（"throwing_arm"，`traits/declarations.dm:1049`） |

- **效果**：
  - **完美投掷**（`disposal/bin.dm:570`）：把东西扔进垃圾管道（disposal bin）时**必定精准命中**（普通角色仅 75% 概率）。
  - **投掷距离 +2**（`living_item_handling.dm:77`）：所有投掷的额外射程 +2 格。
- **获得途径**：清洁工开局自带（`job_types/janitor.dm:42`，与 janitor 芯片一起发放）；同名天赋（quirk）也存在（`quirks/positive_quirks/throwing_arm.dm`）。
- **直观理解**：垃圾管道神射手 —— 隔老远也能把杂物精准扔进管道，投掷距离还更远。

#### 27. Old Copy of "Space Station 13: The Musical" —— 音乐记忆（Memory of a Musical）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/musical`（`generic_skillchips/musical.dm`） |
| auto_traits | 无（自定义信号系统） |

- **效果**（`make_music()`，`musical.dm:25-70`）：激活后注册 `COMSIG_MOB_SAY`。**所有说的话自动变成"唱"**：单字句直接切歌唱模式（MODE_SING）；多字句则把最后一个词的最后一个元音**拉长 4 倍**、句尾句号变感叹号 —— 例如 "hello there" → "hello thereeeee!"（并追加感叹号）。
- **彩蛋**：`Initialize` 时芯片名称里的 "Old" 会被替换为随机年份（当前年份 - 50/100/150/200/250 取整到 5）；`examine_more` 列出唱片目录（"The Ballad of Space Station 13"、"Banned from Cargo" 等 11 首歌，其中一首被划掉）。
- **直观理解**：说话自动变歌剧 —— 全站广播从此充满艺术气息；是歌手（无口）职业的反义词。

#### 28. Kommand —— 强化指点（Enhanced Pointing / Big Pointer）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/big_pointer`（`generic_skillchips/point.dm`） |
| auto_traits | 无（自定义信号系统） |
| actions | `/datum/action/change_pointer_color`（"Change Pointer Color"，可自定义箭头颜色/重置） |

- **效果**（`fancier_pointer()`，`point.dm:28-40`）：指点（point）时生成的箭头从普通小箭头升级为**大箭头**（`arrow_large`）；若设置了自定义颜色则使用白底大箭头 + 自定义着色 + 高光图层。`TRAIT_UNKNOWN_APPEARANCE`（身份不明）时不放大。颜色选择器（`pick_color`）提供全色盘。
- **直观理解**：领导力指点 —— 箭头更大更醒目，还能自定义颜色，指哪打哪更有气势。

#### 29. 4U70-P3R4710N —— 自我手术（Self Surgery）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/self_surgery`（`generic_skillchips/self_surgery.dm`） |
| auto_traits | `TRAIT_SELF_SURGERY`（"self_surgery"，`traits/declarations.dm:1631`） |

- **效果**（`_operation.dm:493,763-764,922-924`）：允许对自己执行**通常禁止自我操作**的手术（`OPERATION_SELF_OPERABLE` 之外的手术），但会施加**明显的成功率惩罚**（flat penalty）—— 芯片描述也提示 "bit rot has probably rendered it somewhat risky"（年久失修，有风险）。
- **违禁品标记**：`Initialize` 时自带 `TRAIT_CONTRABAND`（`self_surgery.dm:14`）—— 安检扫描会亮红灯。
- **直观理解**：战地自救芯片 —— 能给自己开膛破肚做手术，但成功率打折，而且这芯片本身是违禁品。

#### 30. Basketsoft 3000 —— 水下编篮（Underwater Basketweaving）

| 项 | 值 |
|---|---|
| 类型路径 | `/obj/item/skillchip/basketweaving`（`generic_skillchips/underwater_basketweaving.dm`） |
| auto_traits | 无（激活时传授配方） |

- **效果**：激活时向持有者**传授全部水下编篮配方**（`typesof(/datum/crafting_recipe/underwater_basket)`，`learn_recipes`）；停用时遗忘。监听 `COMSIG_MIND_TRANSFERRED` / `COMSIG_MOB_MIND_TRANSFERRED_INTO` —— 换脑（mindswap）时配方随之转移或遗忘。
- **植入限制**（`has_mob_incompatibility`）：目标**必须拥有 mind**，否则拒绝植入（"Target incapable of learning recipe."）。
- **直观理解**：经典梗芯片 —— 水下编篮子这门"绝学"，装上就会，换脑不忘。

#### 31. （附）K1SS 原型 —— 见第 11 条 acrobatics/kiss 变体（不再重复）。

---

### 四、芯片与职业对照速查表（Chip ↔ Job Quick Reference）

| 芯片（类型路径） | 所属职业/来源 | 槽位 | 复杂度 | auto_traits |
|---|---|---|---|---|
| B0RK-X3（job/chef） | 厨师 Cook | 2 | 1 | 无（授予厨房 CQC） |
| B@L00NY（job/clown） | 小丑 Clown | 2 | 1 | TRAIT_BALLOON_SUTRA |
| DET.ekt（job/detectives_taste） | 侦探 Detective | 2 | 1 | TRAIT_DETECTIVES_TASTE |
| CL34NM4ST.R（job/janitor） | 清洁工 Janitor | 2 | 1 | TRAIT_CLEANBOT_WHISPERER |
| TUNN3L_R4T（job/miner） | 矿工 Shaft Miner | 2 | 1 | TRAIT_ROCK_STONER |
| HYPERG1G4（job/psychology） | 心理学家 Psychologist | 2 | 1 | TRAIT_SUPERMATTER_SOOTHER + TRAIT_MADNESS_IMMUNE |
| R.D.S.P.L.X.（research_director） | 研究主管 RD | **1**（通用类） | 1 | TRAIT_ROD_SUPLEX + TRAIT_STRENGTH |
| Cyborg C1-RCU-1T（job/roboticist） | 机器人技术员 Roboticist | 2 | 1 | TRAIT_KNOW_ROBO_WIRES |
| Engineering C1-RCU-1T（job/engineer） | 站台工程师/总工/ERT | 2 | 1 | TRAIT_KNOW_ENGI_WIRES |
| H41R 3XP3R7（hair_expert，NOVA） | 理发师 Barber（NOVA） | 1 | 1 | TRAIT_HAIR_EXPERT（NOVA） |
| F058UR7（acrobatics） | 通用（市场/地图） | 1 | 1 | 无（表情系统） |
| BULLET_DODGER（matrix_taunt） | 通用；NOVA 忍者开局 | 1 | 1 | 无（临时授予子弹免疫） |
| WINE（wine_taster） | 通用 | 1 | 1 | TRAIT_WINE_TASTER |
| Hedge 3（bonsai） | 通用 | 1 | 1 | TRAIT_BONSAI |
| Skillchip adapter（useless_adapter） | 通用 | **0** | **0** | 无 |
| N16H7M4R3（light_remover） | 通用；NOVA 夜视店 | 1 | 1 | TRAIT_LIGHTBULB_REMOVER |
| K33P-TH4T-D15K（disk_verifier） | 舰长/ERT/NT 顾问 | 1 | 1 | TRAIT_DISK_VERIFIER |
| 3NTR41LS（entrails_reader） | CMO/法医/医生 | 1 | 1 | TRAIT_ENTRAILS_READER |
| GENUINE ID（appraiser） | 通用 | 1 | 1 | TRAIT_ID_APPRAISER |
| Le S48R4G3（sabrage） | 通用 | 1 | 1 | TRAIT_SABRAGE_PRO |
| suspicious（brainwashing） | 通用（危险品） | 1 | 1 | TRAIT_BRAINWASHING |
| K1SS（chefs_kiss） | 通用 | 1 | 1 | TRAIT_CHEF_KISS |
| INTJ（intj） | 通用 | 1 | 1 | TRAIT_REMOTE_TASTING |
| F0RC3 4DD1CT10N（drunken_brawler） | 调酒师 Bartender | 1 | 1 | TRAIT_DRUNKEN_BRAWLER |
| Mast-Angl-Er（master_angler） | 通用 | 1 | 1 | 钓鱼四特质（见上） |
| T4RG3T.bin（disposals） | 清洁工 Janitor 附带 | 1 | 1 | TRAIT_THROWINGARM |
| SS13: The Musical（musical） | 通用 | 1 | 1 | 无（说话变唱） |
| Kommand（big_pointer） | 通用 | 1 | 1 | 无（大箭头+自定义颜色） |
| 4U70-P3R4710N（self_surgery） | 通用（违禁品） | 1 | 1 | TRAIT_SELF_SURGERY |
| Basketsoft 3000（basketweaving） | 通用 | 1 | 1 | 无（传授水下编篮配方） |
| prototype N. 807 K1SS（acrobatics/kiss） | 通用（废案） | 1 | 1 | 无（亲吻触发故障） |

---

### 五、备注与联动（Notes & Cross-References）

1. **职业芯片互斥规则**：所有 `chip_category = JOB` 的芯片互相排斥（脑内同时只能激活/植入一枚职业芯片，见 `_job.dm:2-4` 与 `skillchip.dm:242-243`）。例外：`research_director` 芯片被归类为 `GENERAL`（`research_director.dm:10-12`），因此 **RD 可以同时佩戴 R.D.S.P.L.X. + 任意职业芯片**（RD 开局实际发放 R.D.S.P.L.X. + roboticist 两枚，两者不冲突）。
2. **NOVA 引用的既有芯片**（非新增定义）：`modular_nova` 中多处直接发放既有芯片 —— 忍者（matrix_taunt）、ERT/NT 顾问（disk_verifier）、幽灵矿工（job/miner）、补给船工程师（job/engineer）、反叛军植入包（job/engineer + job/roboticist，`opposing_force/implants.dm:6-11`）、电信专家（job/engineer）、货运市场（job/engineer 二手货）、雇佣合同文本（提及技能芯片条款，`employment_contract.dm:37-39`）、Cybersun 赏金任务（收集 CNS skillchip 植入体，`destwo.dm:24-26`）。NOVA **新增定义**仅 `hair_expert` 一枚。
3. **洗脑芯片与洗衣机**：`TRAIT_BRAINWASHING` 由"洗脑"技能芯片授予（来源 `SKILLCHIP_TRAIT`），洗衣机据此判定是否执行完美洗脑流程（`brain_item.dm:490`、`washing_machine.dm:448-449`）。
4. **精神类特质使用 `HAS_MIND_TRAIT` 判定**：`TRAIT_MADNESS_IMMUNE`、`TRAIT_SUPERMATTER_SOOTHER`、`TRAIT_ROD_SUPLEX`、`TRAIT_ENTRAILS_READER`、钓鱼四特质等以 mind 为单位判定，换脑（mindswap）后特质跟随大脑/心智而非身体。
5. **数值速记**：职业芯片 = 2 槽 / 通用芯片 = 1 槽；大脑共 5 槽、3 复杂度；激活/停用冷却 5 分钟；植入/取出 15 秒。脑内最多同时激活复杂度 ≤ 3 的芯片（默认），超限自动停用最旧芯片。
