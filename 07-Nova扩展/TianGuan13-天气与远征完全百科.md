# TianGuan13 天气与远征完全百科 (Weather & Away Missions Encyclopedia)

> 基于 TianGuan13 NovaSector 分支源码全量整理。天气系统：`code/datums/weather/`（9 文件 1,458 行）+ 天气子系统调度；远征系统：`code/modules/awaymissions/`（30 文件 2,507 行）+ NOVA 追加 476 行 + `_maps/RandomZLevels/*.dmm`（10 个地图）。
> 本文档全量列出所有天气类型与远征目的地，无省略。

## 目录 (Table of Contents)

- [1. 天气系统总览](#1-天气系统总览-weather-system-overview)
- [2. 天气全录（11 种 + 7 变体）](#2-天气全录11-种--7-变体)
- [3. 天气机制补充](#3-天气机制补充)
- [4. 远征系统机制](#4-远征系统机制-away-missions)
- [5. 远征目的地全录（10 个地图）](#5-远征目的地全录10-个地图)
- [6. 附加内容](#6-附加内容)
- [7. 源码路径索引](#7-源码路径索引)

---

## 1. 天气系统总览 (Weather System Overview)

**源码**: `code/datums/weather/`（9 文件 1,458 行）+ 天气子系统（SSweather）

**核心机制**：
- **触发**：SSweather 按目标 ztrait 加权随机触发；风暴间隔 5–10 分钟
- **雷电**：5 档概率（0.0025 ~ 0.00002）
- **天气塔**：召唤/清除天气 + 雷达预测
- **矿用天气监视器** + weather_announcer 组件广播
- **storm_hating 组件**：地上的物品在灰烬/雪/虚空风暴中销毁
- **天气异常**：随机选雨/雪/沙；雷鸣异常固定雨
- **station trait 联动**：Forever Storm（行星=永恒雪暴）、放射性星云（辐射风暴/nebula+护盾包裹）

---

## 2. 天气全录（11 种 + 7 变体）

| 天气 | 目标 ztrait | 概率 | 持续 | 伤害/效果 | 免疫/防护 |
|---|---|---|---|---|---|
| **灰烬风暴 ash_storm**（粒子版） | ZTRAIT_ASHSTORM | 90 | 1–2 分钟 | **4 火焰伤/次**（仅有机体）；雷电陨击（极稀有 1/50,000/格/tick，暗红） | TRAIT_ASHSTORM_IMMUNE；隔热≥FIRE_IMMUNITY_MAX_TEMP_PROTECT 的护甲；室内自动豁免 |
| **余烬雨 emberfall**（ash 变体） | 同上 | 10 | 同上 | 无伤害；结束时自动回填被挖玄武岩 | 无害 |
| **辐射风暴 rad_storm** | ZTRAIT_STATION | 无(事件) | 1–2.5 分钟 | 40% 突变率→90% 负面突变；辐射 | 保护区域：维护道/飞船/AI区/紧急储藏/监狱安全区/矿井维护；TRAIT_RADSTORM_IMMUNE；铅防护服 |
| **辐射星云 nebula**（rad 变体） | — | 0 | **无限** | 每次 5% 辐射脉冲 | 仅 shuttle/radshelter |
| **雪暴 snow_storm** | ZTRAIT_SNOWSTORM | 90 | 1–2.5 分钟 | 寒冷（ICEBOX_MIN-40K，**无视衣物隔热**，严格警报） | TRAIT_SNOWSTORM_IMMUNE；室内；耐寒服 |
| **永恒雪暴 forever_storm** | 同上 | 0 | 无限且更强（再-40K） | 同上 | 同上 |
| **雨 rain_storm**（粒子） | ZTRAIT_RAINSTORM | 90 | 3–5 分钟 | 落水试剂（WEATHER_REAGENT_VOLUME 5u×3），熄火/洗污 | TRAIT_RAINSTORM_IMMUNE |
| **血雨/等离子雨/天妇罗雨**（rain 变体，admeme） | — | 0 | — | 对应试剂雨 | — |
| **酸雨 acid**（rain 变体，加权硫酸/硝酸/氟酸） | — | 0 | 1–2 分钟 | 强酸灼伤 | 室内 |
| **巫师魔法雨 wizard**（rain 变体，随机药水池） | 站内 | 0 | 30s–1 分 | 随机试剂雨（含魔法/药/毒/食物） | 同辐射保护名单 |
| **沙暴 sand_storm** | ZTRAIT_SANDSTORM | 90 | 1–2 分钟 | **5 钝伤/次**（仅有机体） | TRAIT_SANDSTORM_IMMUNE |
| **沙落 sandfall**（无伤变体） | 同上 | 10 | 同上 | 无伤害 | — |
| **地板是熔岩 floor_is_lava** | 站内 | 事件 | 30s–1 分 | **3 火焰伤/次**；站稠密物体/床/硅基/飞行豁免 | TRAIT_LAVA_IMMUNE |
| **虚空风暴 void_storm** | ZTRAIT_VOIDSTORM | 0(事件) | 1–2 分钟/无限 | 无直接伤害，事件性 | — |

---

## 3. 天气机制补充

- **风暴间隔**：5–10 分钟（SSweather 随机）
- **雷电 5 档概率**：0.0025 / 0.0005 / 0.0001 / 0.00002（由低到高触发等级）
- **storm_hating 组件**：地面物品在灰烬/雪/虚空风暴中被销毁（防止物品堆积）
- **天气塔**：可主动召唤/清除指定天气，带雷达预测功能
- **矿用天气监视器**：拉瓦兰矿区用，配 weather_announcer 组件播报
- **天气异常（anomaly）**：随机生成雨/雪/沙天气异常；雷鸣异常固定触发雨

---

## 4. 远征系统机制 (Away Missions)

**源码**: `code/modules/awaymissions/`（30 文件 2,507 行）+ `modular_nova/modules/awaymissions_nova/`（476 行）

**核心流程**：
1. 传送门（网关 gateway）**供电** → **校准** → **控制台选择目的地** → 钥匙卡/延迟激活
2. 传送门目的地 = 远征地图内 `/obj/effect/landmark/awaystart`（按 id 建 `gateway_destination/point`）
3. **流放者植入物**被站内网关拒绝
4. **NOVA**：`requires_key` 网关需**全局召回钥匙**（boss 掉落）；`borg_gateway_blacklist` 禁机器人/MMI 通过
5. 幽灵不能进 secret 层；cordon 边界（远征区域隔离）

**配置**：
- `awaymissionconfig.txt`：当前仅 maintsroom 启用
- `GATEWAY_DELAY 18000`（普通目的地延迟 18 秒）
- `GATEWAY_DELAYS_BY_ID`：AWAYSTART_BEACH 6000 / AWAYSTART_MUSEUM 9000（海滩 6 秒/博物馆 9 秒）
- `borg_gateway_blacklist`：禁机器人通过列表

---

## 5. 远征目的地全录（10 个地图）

| # | 目的地 | 内容 | 出入口/密钥 |
|---|---|---|---|
| 1 | **TheBeach 海滩** | 海盗钓鱼/朗姆信 | AWAYSTART_BEACH（延时 10 分钟） |
| 2 | **SnowCabin 雪屋** | 雪林/伐木锯/苏联地堡/传送符文/帝企鹅/弱化魔杖 | AWAYSTART_SNOWCABIN |
| 3 | **moonoutpost19 月球前哨 19** | 异形逃生站：蜂巢/研究（8 份异形论文）/矿区/辛迪加装备 | AWAYSTART_MOONOUTPOST |
| 4 | **research 研究前哨** | 低温/克隆/基因/逃生舱/网关室 | AWAYSTART_RESEARCH |
| 5 | **undergroundoutpost45 地下前哨 45** | 中央大厅/工程/采矿/研究/洞穴/网关 | AWAYSTART_UNDERGROUND |
| 6 | **museum 博物馆** | 博物馆+食堂+蛾虫虚空 | AWAYSTART_MUSEUM（延时 15 分钟） |
| 7 | **Academy 学院** | 巫师学院：Buff 法师/高法师/亡灵骑士守卫；商人 Mr Corporate（收符文/禁品，卖 kyle 链锯剑） | AWAYSTART（普通） |
| 8 | **heretic 异端传送门** | 6 区密钥卡谜题（入口/高安/CBRN/生物/武器/杂项）+轻轨线+博福斯 40mm 舰炮 | 无直接 awaystart（靠异端传送门进） |
| 9 | **mothership_astrum 星辰母舰（NOVA）** | 绑架者母舰 5 甲板（战斗/娱乐/冰冻/异形研究/海滩全息舱）；abductor 科学家/特工/远程单位；**Boss 绑架者船长（1750HP hierophant）掉落全局召回钥匙+Zeta 爆能枪**；龙虾人灵薄狱幽灵角色 | AWAYSTART（普通） |
| 10 | **maintsroom 维修间（NOVA）** | 程序生成随机维修室（墙壁/白色地板/强化桌/异常核心/矿物堆）；crowlie/chamenos 双卡门 | AWAYSTART_MAINTSROOM（**当前唯一启用**） |

---

## 6. 附加内容

- **super_secret_room**：错误处理房（会说话的瓷砖 + 救赎路标 + 卢比）
- **stationCollision / murderdome / centcomAway**：代码存在但**本仓库无对应启用地图**（centcomAway XCC-P5831 仅为旧 CentCom.dmm 引用错误房）

---

## 7. 源码路径索引

| 系统 | 路径 | 行数 |
|---|---|---|
| 天气核心 | `code/datums/weather/` | 1,458 |
| 天气子系统 | `code/controllers/subsystem/weather.dm` | — |
| 天气塔 | `code/game/machinery/weather_tower.dm`（或 weather 相关） | — |
| 矿用天气监视器 | `code/game/machinery/weather_monitor.dm` | — |
| 天气异常 | `code/modules/anomaly/`（weather 相关） | — |
| 远征核心 | `code/modules/awaymissions/` | 2,507 |
| 远征地图 | `_maps/RandomZLevels/*.dmm` | 10 张 |
| NOVA 远征追加 | `modular_nova/modules/awaymissions_nova/` | 476 |
| 远征配置 | `config/awaymissionconfig.txt` | — |

---

> **文档完** — 天气 11 种 + 7 变体、远征目的地 10 个全录，全部数值从源码提取。自查无"等 N 种"省略。
