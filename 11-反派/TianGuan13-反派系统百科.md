# 天关 — 反派系统百科（导航总览）

> **项目**: TianGuan13 (Nova Sector → /tg/station)
> **说明**: 2026-08-10 拆分升级——各反派已独立成篇（见下方导航），本篇保留**未拆分的汇总内容**（Malf AI/逃犯/30 小阵营表/代码路径索引）作为总览入口。
> **代码**: `code/modules/antagonists/`（50 目录 60,645 行）+ `code/modules/uplink/`（3,746 行）

---

## 目录

- [一、反派独立篇导航（11 篇）](#一反派独立篇导航11-篇)
- [二、恶意 AI（Malf AI）](#二恶意-aimalf-ai)
- [三、逃犯（Fugitive）](#三逃犯fugitive)
- [四、其他小型阵营（30 个）](#四其他小型阵营30-个)
- [附录 · 代码路径索引](#附录--代码路径索引)

---

## 一、反派独立篇导航（28 篇）

> 全部反派已拆分独立成篇（`11-反派/`），每篇按叛徒标准：核心属性全录（moodlet/音效/口号/hijack_speed/hardcore）+ 目标系统全量 + 风味彩蛋 + 数值速查表 + 源码审计。

### 大反派（8 篇）
| 反派 | 独立篇 | 审计要点 |
|---|---|---|
| 叛徒 Traitor | [叛徒反派百科](TianGuan13-叛徒反派百科.md) | 目标系统 8 类型/暗号/承包商/雇主 4 列表 |
| 核弹队 Nukeop | [核弹队反派百科](TianGuan13-核弹队反派百科.md) | 10 结局/核弹 90s/7 套装/ops.ogg |
| 异端 Heretic | [异端反派百科](TianGuan13-异端反派百科.md) | 8 路径 120 知识/67 法术/8 升阶/heretic_gain.ogg |
| 巫师 Wizard | [巫师反派百科](TianGuan13-巫师反派百科.md) | 法术书 79 条目/学徒机制/FOR THE FEDERATION!! |
| 邪教 Cult | [邪教反派百科](TianGuan13-邪教反派百科.md) | 10 符文/血魔法 10/构造体 6/FOR NAR'SIE!! |
| 变形怪 Changeling | [变形怪反派百科](TianGuan13-变形怪反派百科.md) | 40 能力/化学制剂/FOR THE HIVE!! |
| 菌潮 Blob | [菌潮反派百科](TianGuan13-菌潮反派百科.md) | 14+1 菌株/blobalert.ogg |
| 恶意 AI Malf AI | [恶意AI反派百科](TianGuan13-恶意AI反派百科.md) | 23 AI 模块/CPU 点系统 |

### 中反派（10 篇）
| 反派 | 独立篇 | 审计要点 |
|---|---|---|
| 绑架者 Abductor | [绑架者反派百科](TianGuan13-绑架者反派百科.md) | 腺体 15/外星手术/ayylien.ogg |
| 血虫 Blood Worm | [血虫反派百科](TianGuan13-血虫反派百科.md) | 三阶段/宿主机制/exit_blood.ogg |
| 异形 Xeno | [异形反派百科](TianGuan13-异形反派百科.md) | 感染链 6 阶段/职阶全录 |
| 间谍 Spy | [间谍反派百科](TianGuan13-间谍反派百科.md) | 赏金 4 类/黑市价/偷穿梭机 |
| 太空海盗 Pirate | [太空海盗反派百科](TianGuan13-太空海盗反派百科.md) | 10 帮派/FOR ME MATEYS!! |
| 虚空行者 Voidwalker | [虚空行者反派百科](TianGuan13-虚空行者反派百科.md) | 凝视致晕/FOR THE VOID!!/FOR THE SUN!! |
| 革命 Revolution | [革命反派百科](TianGuan13-革命反派百科.md) | 闪光转化/VIVA LA REVOLUTION!! |
| 太空龙 Space Dragon | [太空龙反派百科](TianGuan13-太空龙反派百科.md) | 裂隙召唤鲤群 |
| 逃犯 Fugitive | [逃犯反派百科](TianGuan13-逃犯反派百科.md) | 5 背景/猎人机制 |
| 小丑核弹队 Clown Ops | [小丑核弹队反派百科](TianGuan13-小丑核弹队反派百科.md) | 香蕉武器/Dark H.O.N.K. |

### 小反派（10 篇）
| 反派 | 独立篇 | 审计要点 |
|---|---|---|
| 太空忍者 Ninja | [太空忍者反派百科](TianGuan13-太空忍者反派百科.md) | 7 目标/能量武士刀/FOR THE SPIDER CLAN!! |
| 亡魂 Revenant | [亡魂反派百科](TianGuan13-亡魂反派百科.md) | 精华系统/能力全录 |
| 噩梦 Nightmare | [噩梦反派百科](TianGuan13-噩梦反派百科.md) | 黑暗能力 |
| 兄弟 Brother | [兄弟反派百科](TianGuan13-兄弟反派百科.md) | 结拜/共享目标 |
| 痴迷 Obsessed | [痴迷反派百科](TianGuan13-痴迷反派百科.md) | 脑创伤/5+1 目标/FOR MY LOVE!! |
| 分裂分子 Separatist | [分裂分子反派百科](TianGuan13-分裂分子反派百科.md) | 部门建国/灭国目标/FOR THE MOTHERLAND!! |
| 高地人 Highlander | [高地人反派百科](TianGuan13-高地人反派百科.md) | 剑决斗/FOR SCOTLAND!! |
| 情人节 Valentine | [情人节反派百科](TianGuan13-情人节反派百科.md) | 配对互保/Heartbreaker |
| 生存主义者 Survivalist | [生存主义者反派百科](TianGuan13-生存主义者反派百科.md) | 3 变体/FOR MYSELF!! |
| 灰烬行者 Ashwalker | [灰烬行者反派百科](TianGuan13-灰烬行者反派百科.md) | 守护 Necropolis/献祭产卵 |

### 相关
- [叛徒补充装备百科](TianGuan13-叛徒补充装备百科.md)（NOVA Uplink 69 新物品）
- [上行链路 Uplink 系统百科](TianGuan13-上行链路Uplink系统百科.md)（395 件全录）
- [电磁卡EMAG百科](TianGuan13-电磁卡EMAG百科.md)（143 钩子明细）
- [变种怪感染百科](TianGuan13-变种怪感染百科.md) / [脑寄生蠕虫百科](TianGuan13-脑寄生蠕虫百科.md)

---

## 二、恶意 AI（Malf AI）

**代码**: `code/modules/antagonists/malf_ai/`（3 文件 1,785 行）

**AI 模块 23 种**（CPU 点）：

| 模块 | CPU | 效果 |
|---|---|---|
| **Doomsday Device 末日装置** | 130 | 最终毁灭 |
| 站台封锁 | 30 | 全站门锁 |
| 机器覆盖 | 30 | 接管机器 |
| 摧毁 RCD | 25 | 摧毁建造器 |
| 机器过载 | 20 | 机器爆炸 |
| 黑灯 | 15 | 全站断电 |
| **机器人工厂** | 100 | 机器人转化（移除分流）|
| 空气警报覆盖 | 50 | 接管空气警报 |
| 热传感器覆盖 | 25 | 接管火警 |
| 应急灯禁用 | 10 | 关应急灯 |
| 摄像头重启 | 10 | 恢复摄像头 |
| 摄像头升级 | 35 | 增强监控 |
| AI 炮塔升级 | 30 | 炮塔强化 |
| 增强监控 | 30 | 窃听 |
| **机械体统治** | 30 | 控制机甲 |
| 变声器 | 20 | 伪装声音 |
| 定点安全覆盖 | 20 | 电磁撬锁 |
| 滚动伺服 | 10 | 核心倾斜 |

---

## 三、逃犯（Fugitive）

**代码**: `fugitive/`（6 文件 1,025 行）

**5 种背景**：
1. 越狱犯（prisoner）
2. 邪教徒（cultist）
3. 沃尔多（捉迷藏）——Waldo
4. 合成人（S.E.L.F. 解放）
5. 隐形人（隐身原型偷窃）

**目标**：躲避逃犯猎人（存活）

---

## 四、其他小型阵营（30 个）

### 隐藏小型阵营（3 个 · 2026-08-06 补全）

| 阵营 | 类型 | 生成条件 | 机制 |
|---|---|---|---|
| **Greentext 连胜者** | 标记 | Hardcore Random 硬核随机点 | `show_in_antagpanel=FALSE`、`ANTAG_FAKE\|ANTAG_SKIP_GLOBAL_LIST`、`hardcore_random_bonus=TRUE`；forge_objectives 生成已完成=true 的 "Succeed" 目标；roundend 中 greentext 成功得 2× 生存分数奖励 |
| **Magic Servant 魔法仆从** | 仆从 | 巫师召唤仆从法术触发（butler 装扮、玩家附身）| roundend/antagpanel 均隐藏；`setup_master(M)` 生成 "Serve [M.real_name]" 目标 |
| **Loyal Shade 忠魂阴影** | 阴影 | 灵魂石 `assign_master` 绑定主人 | 静默、隐藏、主名 master_name；邪教徒则转 cult/shade；神圣主题灵魂石可解除邪教改造 |

### 小型阵营汇总表

| 阵营 | 行数 | 说明 |
|---|---|---|
| 小丑核弹队 Clown Ops | 396 | 小丑版核弹队 |
| 噩梦 Nightmare | 355 | 暗影生物 |
| 亡魂 Revenant | 364 | 幽灵 |
| 兄弟 Brother | 331 | 结拜兄弟 |
| 痴迷 Obsessed | 309 | 跟踪狂 |
| 分裂分子 Separatist | 280 | 独立分子 |
| 太空忍者 Space Ninja | 173 | 忍者 |
| 异形 Xeno | 175 | 异形（独立篇见上）|
| 灰烬行者 Ash Walker | 80 | 灰烬之地部落（猎杀外来者）|
| 战列巡洋舰 Battlecruiser | 65 | 辛迪加战列舰乘员 |
| 洗脑 Brainwashed | 87 | 洗脑受害者（植入物）|
| 邪恶克隆 Evil Clone | 68 | 平行时间克隆体 |
| 高地人 Highlander | 103 | 高地人决斗（只能活一个）|
| 催眠 Hypnotized | 19 | 被催眠的目标 |
| 变形体 Morph | 7 | 拟态怪物（伪装成物品）|
| 悖论克隆 Paradox Clone | 85 | 时间悖论克隆体 |
| 太空海盗 Space Pirate | 1298 | 海盗舰队（独立篇见上）|
| 火焰史莱姆 Pyroclastic | 27 | 火焰异常体 |
| 圣诞老人 Santa | 59 | 圣诞老人（节日）|
| 有知觉生物 Sentient Creature | 41 | 觉醒宠物变反派 |
| 蜘蛛 Spiders | 56 | 蜘蛛巢穴 |
| 间谍 Spy | 1413 | 偷窃任务间谍（独立篇见上）|
| 生存主义者 Survivalist | 104 | 生存主义者 |
| 辛迪加猴 Syndicate Monkey | 37 | 辛迪加猴子特工 |
| 情人节 Valentine | 110 | 情人节/心碎者（节日）|
| 捕蝇草人 Venus Human Trap | 26 | 食人植物人 |
| 许愿机 Wishgranter | 31 | 许愿机化身 |

> 已拆分的（异形/太空海盗/间谍/虚空行者/革命/太空龙/血虫）在独立篇有完整内容，本表保留行数参考。

---

## 附录 · 代码路径索引

**核心**: `code/modules/antagonists/`（50 目录 60,645 行）

| 目录 | 反派 |
|---|---|
| traitor/ | 叛徒 |
| nukeop/ | 核弹队 |
| heretic/ | 异端 |
| wizard/ | 巫师 |
| cult/ | 邪教 |
| changeling/ | 变形怪 |
| abductor/ | 绑架者 |
| blob/ | 菌潮 |
| malf_ai/ | 恶意 AI |
| blood_worm/ | 血虫 |
| xeno/ | 异形 |
| voidwalker/ | 虚空行者 |
| fugitive/ | 逃犯 |
| revolution/ | 革命 |
| space_dragon/ | 太空龙 |
| pirate/ | 太空海盗 |
| spy/ | 间谍 |
| nightmare/ | 噩梦 |
| revenant/ | 亡魂 |
| brother/ | 兄弟 |
| obsessed/ | 痴迷 |
| morph/ | 变形体 |
| highlander/ | 高地人 |
| space_ninja/ | 太空忍者 |
| clown_ops/ | 小丑核弹队 |
| ashwalker/ | 灰烬行者 |
| ... | 其余小阵营 |

---

*本篇为导航总览：已拆反派见独立篇（全量审计版），未拆内容（Malf AI/逃犯/小阵营表）保留于此。*
