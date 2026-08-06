# 天关 — 供应与采矿完全百科

> **代码**: `code/modules/cargo/` (70+文件), `code/modules/mining/` (40+文件, 含 `boulder_processing/` 巨石加工6文件、`laborcamp/` 劳改营2文件、矿石筒仓/堆叠机/卸载机/矿料处理链), `code/modules/jobs/job_types/cargo_technician.dm`, `code/modules/jobs/job_types/quartermaster.dm`, `code/modules/jobs/job_types/shaft_miner.dm`

---

## 第一篇 · 供应系统 (Cargo)

### 一、供应控制台

**代码**: `orderconsole.dm` (554行)

| 终端类型 | 功能 |
|---|---|
| **Supply Console** | 全功能: 下单/审批/控制航天飞机 |
| **Request Console** | 仅可发送采购请求 |

### 二、订购流程

```
1. 部门人员用 Request Console 提交采购请求
2. 军需官用 Supply Console 审批请求
3. 确认后→扣款→货物装载到货运穿梭机上
4. 穿梭机抵达→打开板条箱→取出货物
```

### 三、采购包分类

**代码**: `packs/` (12个文件, 共约3,000行)

| 分类 | 文件 | 代表品 |
|---|---|---|
| **工程** | `engineering.dm` | 电缆/材料/工具/发电机零件 |
| **医疗** | `medical.dm` | 药品/担架/医疗设备 |
| **科研** | `science.dm` | 科研设备/植物基因 |
| **安全** | `security.dm` | 弹药/手铐/防弹衣 |
| **物料** | `materials.dm` | 矿物锭/玻璃/塑料/木材 |
| **有机** | `organic.dm` | 种子/食物/活体动物 |
| **生活** | `livestock.dm` | 鸡/牛/羊/猪 |
| **服务** | `service.dm` | 派对/娱乐/装饰 |
| **服饰** | `costumes_toys.dm` | 服装/玩具/面具 |
| **通用** | `general.dm` | 工具/容器/日常品 |
| **紧急** | `emergency.dm` | 紧急求生/避难用品 |
| **进口** | `imports.dm` | 稀有外来品 |

### 四、货币系统

| 概念 | 说明 |
|---|---|
| **预算** | 各部门有独立预算(工程/医疗/科研/安全等) |
| **收入** | 通过出口矿物/工艺品/有机品等获得 |
| **支出** | 每个采购包有固定价格(以CARGO_CRATE_VALUE为基准) |
| **优惠券** | 可折扣特定采购包 |

### 五、出口系统

**代码**: `exports/` (19个文件, 约1,500行)

| 出口类型 | 文件 | 可出口 |
|---|---|---|
| **矿物** | `materials.dm` | 各矿锭/金属 |
| **板材** | `sheets.dm` | 玻璃/塑料/木材锭 |
| **零件** | `parts.dm` | 机器零件/电路板 |
| **种子** | `seeds.dm` | 植物种子 |
| **食物** | `food_and_drink.dm` | 料理/酒水 |
| **武器** | `weapons.dm` | 武器/弹药 |
| **工具** | `tools.dm` | 各种工具 |
| **大型物品** | `large_objects.dm` | 家具/机械 |
| **Lavaland** | `lavaland.dm` | 熔岩地特产 + 巨兽战利品 |
| **鱼** | `fish.dm` | 钓鱼成果 |
| **异常体** | `anomaly.dm` | 异常点产物 |
| **试剂** | `reagent.dm` | 化学试剂 |
| **器官** | `organs.dm` | 义肢/器官 |
| **机甲** | `mecha.dm` | 机甲零件 |
| **异生** | `xenobio.dm` | 异生学产物 |
| **古董** | `antiques.dm` | 古董/艺术 |
| **装备** | `gear.dm` | 武器装备 |
| **盖章文件** | `stamped_paperwork.dm` | 盖章公文 |
| **船员赏金** | `civilian_bounty.dm` | 随机市民赏金任务 |

### 六、赏金系统

**代码**: `bounties/` (15个文件)

按部门分类的悬赏任务(医用/工程/安保/科研/种植/烹饪/挖矿等)

---

## 第二篇 · 采矿系统 (Mining)

### 一、矿工职业

**代码**: `code/modules/jobs/job_types/shaft_miner.dm`

| 项目 | 内容 |
|---|---|
| 岗位 | 3总/3出生 |
| 上级 | 军需官 |
| 部门 | 货运 |
| ID | 货运权限+外部采矿 |
| 邮件礼物 | 20%勘探扫描仪/采矿钻头/高品质矿物/进阶激光/铁皮条/煎蛋 |
| 特殊 | **Lavaland勘探**: 可下熔岩地挖矿打Boss |

**初始装备**:
```
- 采矿头盔 (内置灯)
- 采矿激光 (挖矿)
- 勘探分析仪 (扫描矿物)
- 急救包
- 腰带/背包/PDA
- 矿工券 (可换工具/武器)
```

### 二、矿石种类

**代码**: `ores_coins.dm` (738行)

| 矿石 | 代码 | 点数 | 精炼产物 | 分布 |
|---|---|---|---|---|
| **铁矿石** | `/obj/item/stack/ore/iron` | 1 | 铁板 | 普通 |
| **铀矿** | `/obj/item/stack/ore/uranium` | 10 | 铀板 | 稀有 |
| **等离子矿** | `/obj/item/stack/ore/plasma` | 15 | 等离子板 | 稀有 |
| **金矿** | `/obj/item/stack/ore/gold` | 30 | 金板 | 稀有 |
| **银矿** | `/obj/item/stack/ore/silver` | 20 | 银板 | 稀有 |
| **钻石** | `/obj/item/stack/ore/diamond` | 50 | 钻石 | 极稀有 |
| **蓝宝石** | `/obj/item/stack/ore/bluespace_crystal` | 50 | 蓝空间水晶 | 极稀有 |
| **沙堆** | `/obj/item/stack/ore/glass` | 1 | 玻璃 | 基础 |
| **玄武岩灰** | `/obj/item/stack/ore/glass/basalt` | 0 | 无 | Lavaland |
| **橙水晶** | `/obj/item/stack/ore/bananium` | 50 | 香蕉板 | 极稀有 |
| **钛矿** | `/obj/item/stack/ore/titanium` | 15 | 钛板 | 稀有 |

**各矿点数**: 铁1 < 沙1 < 铀10 < 等离子15 < 钛15 < 银20 < 金30 < 钻石50 < 蓝水晶50 < 香蕉板50

### 三、采矿工具

**代码**: `equipment/mining_tools.dm` (389行)

| 工具 | 功能 |
|---|---|
| **采矿激光(Mining Laser)** | 基础挖矿，射速快 |
| **钻石钻头(Diamond Drill)** | 高效挖矿 |
| **勘探分析仪(Mineral Scanner)** | 扫描周围矿物分布 |
| **动能粉碎器(Kinetic Crusher)** | 近战挖矿+战斗 |
| **共振器(Resonator)** | 产生共振波挖矿 |
| **抓手枪(Grapple Gun)** | 跨越地形 |
| **采矿背包(Mining Satchel)** | 自动吸取矿石 (50总容量) |
| **矿石箱(Ore Box)** | 木质大箱, 批量装矿石/巨石 |

**矿石背包与矿石箱详解**:

| 物品 | 代码 | 功能 |
|---|---|---|
| **采矿背包** | `/obj/item/storage/bag/ore` (`bags.dm`) | 50总容量; 装备后自动吸取脚下矿石; 拉拽矿石箱时矿石直接入箱; 机械体变体`/cyborg` |
| **持有型采矿背包** | `/obj/item/storage/bag/ore/holding` | 无限容量 (蓝空间版) |
| **矿石箱** | `/obj/structure/ore_box` (`satchel_ore_box.dm`) | 木质 (4木材), 可装矿石+巨石; 撬棍拆解掉落内容; 机甲(mecha)可拖拽装载, 巨石入机甲矿石箱自动破成矿石 |

### 四、采矿券兑换

**代码**: `voucher_sets.dm` (111行)

| 券类型 | 可换物品 |
|---|---|
| **矿工工具券** | 采矿激光/钻头/共振器/勘探仪 |
| **矿工装备券** | 背包/腰带/防爆服/防尘服 |

### 五、矿石加工

#### 5.1 矿石赎还机 (ORM)

**代码**: `machine_redemption.dm` (423行)

```
矿石 → ORM → 锭/板 + 点数
点数可用于采购包购买
```

| 功能 | 说明 |
|---|---|
| 输入 | 矿石堆 |
| 处理 | 自动计算点数+精炼成锭 |
| 输出 | 锭送到地下仓储 |

#### 5.2 矿料处理控制台 (Mineral Processing Unit)

**代码**: `machine_processing.dm` (290行)

处理 ORM 分散的材料 → 提供工程/科研使用。由 **生产机械控制台 (production machine console)** + **熔炉 (furnace)** 组成。

| 部件 | 说明 |
|---|---|
| 处理控制台 `processing_unit_console` | 2格内自动连接熔炉, 无熔炉则自毁; 选择材料/合金/开关 |
| 熔炉 `processing_unit` | 接受堆叠物→材料容器→按配方熔炼 (SMELT_AMOUNT=5/秒) |
| 合金系统 | 内置自动解锁科技网 `smelter`, 可炼合金配方 |
| 劳改营变体 `processing_unit/gulag` | 只接受矿石堆 (工作营用) |

**基类机制** (`/obj/machinery/mineral`): 监听输入格 (input_dir) 的 `ATOM_ENTERED`/`ATOM_CREATED` 信号 → `pickup_item()` → 输出到 output_dir 邻格 (`unload_mineral`)。

#### 5.3 矿石筒仓 (Ore Silo)

**代码**: `machine_silo.dm` (624行)

全站矿物的蓝空间存储与传输枢纽, 向全站机器 (ORM/熔炉/堆叠机/bouldertech/RCD/制造机等) 供料。

| 功能 | 说明 |
|---|---|
| 材料容器 | `MATERIAL_SILO_STORED` 材料无限容量 |
| 远程连接 | 机器用 `remote_materials` 组件接入, 筒仓统一供料/回收 |
| 强制ID | 默认全站首个筒仓要求**有效银行账户ID** (ID_required) |
| 持有机制 (Hold) | 暂停某台连接机器的取料权限 |
| 断开机制 | 从筒仓 UI 移除某台机器的连接 |
| 日志系统 | 记录每笔存取 (时间/机器/区域/材料/操作者ID), 相似日志自动合并 |
| 广播系统 | 封禁/解封/限制变更通过无线电 (通用/指挥/补给/安保频道) 播报 |
| 撬开 (emag) | 允许硅基封禁/解除ID限制 (默认禁止硅基操作) |

**矿工封禁机制 (Banned Users)**:
```
- 有 QM(军需官) 权限者可封禁/解封任意用户
- 有 Captain(舰长) 权限者可封禁 QM
- 硅基生命 (AI/机器人/无人机) 默认不可封禁 (除非emag)
- 变色龙ID持有者免疫封禁 (仅记录日志)
- 封禁后该用户无法通过任何连接机器取用筒仓材料
```

#### 5.4 堆叠机与卸载机 (Stacking & Unloading Machines)

**代码**: `machine_stacking.dm` (181行), `machine_unloading.dm` (23行)

| 机器 | 功能 |
|---|---|
| 堆叠机 `stacking_machine` | 从输入格吸取板材/蓝水晶, 堆到 50 自动打包输出; 连接筒仓则直接存入 |
| 堆叠机控制台 `stacking_unit_console` | 显示堆叠机内材料、释放打包、旋转输入/输出方向 |
| 卸载机 `unloading_machine` | 从输入格取矿石堆/矿石箱, 把箱内矿石全部倒出到输出格 |

**用法**: 传送带 (conveyor) 上 卸载机→堆叠机→筒仓 自动链, 卸载机把矿石箱拆空, 堆叠机把散落板材自动归堆。

#### 5.5 巨石加工系统 (Bouldertech)

**代码**: `boulder_processing/` (6个文件, 共约1,300行)

```
矿脉(ore vent) → 巨石(boulder) → BRM回收矩阵 → 精炼厂(refinery)/熔炼厂(smelter) → 筒仓/矿点
```

##### 5.5.1 巨石 (Boulder) — `boulder.dm` + `boulder_types.dm`

| 巨石类型 | 代码 | 说明 |
|---|---|---|
| 基础巨石 | `/obj/item/boulder` | 耐久 5-8, 双手持, 拖拽减速 |
| 神器巨石 | `/obj/item/boulder/artifact` | 内含遗物 (lavaland relic), 机器处理得额外100矿点 |
| 蓝空间神器巨石 | `artifact/bluespace` | 附加蓝空间材料 |
| 钻石神器巨石 | `artifact/diamond` | 附加钻石材料 |
| 铁矿巨石 | `/obj/item/boulder/gulag_vent` | 劳改营矿脉产出, 纯铁 (16铁=5矿石) |
| 劣质巨石 | `/obj/item/boulder/gulag` | 劳改营宝箱式, 随机金/铁/等离子/银 10-13单位 |
| 测试巨石 | `/obj/item/boulder/shabby` | 管理用, 铁+玻璃, 耐久1 |

**处理方式**:
```
机器处理: 放入 bouldertech 机器 → 每 tick 减耐久 → 拆解成材料/矿点
手动处理: 采矿工具/BOULDER_BREAKER 特质者敲击 → 掉落矿石 (比机器少1, 1-10个)
投掷: 扔到熔岩上 → 生成 10秒 临时平台 (catwalk), 供跨越熔岩
```

**巨石特性**: 不可放入矿石背包; 被矿用装备 (BOULDER_BREAKER) 者攻击效率翻倍; 巨石间互不叠放。

##### 5.5.2 BRM 巨石回收矩阵 (Boulder Retrieval Matrix) — `brm.dm` (334行)

| 功能 | 说明 |
|---|---|
| 左键 | 手动传送 1 颗已开采巨石到矩阵旁 (1.5秒冷却) |
| 右键 | 切换自动回收 (每批3秒冷却) |
| 数据源 | 从 `SSore_generation.available_boulders` 随机选取巨石 |
| 零件 | 组件等级决定每批处理上限 `boulders_processing_max` |
| 区域限制 (NOVA) | 仅在站内/矿区 (station/mine area) 可用 |
| 传送限制 | 所在格已有巨石则暂停 |

##### 5.5.3 精炼厂与熔炼厂 (Refinery & Smelter) — `refinery.dm` (168行)

**共同基类** `/obj/machinery/bouldertech` (`_boulder_processing.dm`, 447行): 接受巨石→减耐久→拆解材料入筒仓+积攒矿点; 用 ID 卡领取矿点; 可被化学试剂增幅效率。

| 机器 | 处理材料 | 增幅试剂 (效率+10~50%) | 废料 |
|---|---|---|---|
| 精炼厂 `refinery` | 非金属: 玻璃/等离子/钻石/蓝空间/香蕉/塑料 | 酸+10% / 强酸+20% / 硝化酸+30% / 泰斯拉姆+50% | 工业废酸 |
| 熔炼厂 `smelter` | 金属: 铁/钛/银/金/铀 | 燃料+10% / 铝热剂+20% / 火药+30% / 液态暗物质+50% | 水 |

**零件升级**: matter_bin 等级↑→容纳巨石数↑; servo 等级↑→每轮处理数↑。

##### 5.5.4 巨石信标 (Boulder Beacon) — `beacon.dm` (29行)

```
使用信标 → 依次召唤: ①BRM回收矩阵 → ②精炼厂 → ③熔炼厂 (3次用完销毁)
一条龙搭起整条 bouldertech 加工线
```

### 六、Lavaland 熔岩地

#### 6.1 环境

| 特性 | 说明 |
|---|---|
| 地形 | 玄武岩/熔岩/灰烬/沙尘 |
| 危险 | 高温/熔岩池/有毒大气 |
| 生物 | 巨兽+普通怪物+植物 |
| 宝箱 | 死灵宝箱(Necropolis Chests) |

#### 6.2 普通怪物

| 怪物 | 说明 |
|---|---|
| **Legion** | 骷髅军团, 爆头可复活 |
| **Goliath** | 巨兽, 投射钩爪+地震 |
| **Watcher** | 眼球怪, 激光射击 |
| **Basilisk** | 石化蛇, 冰冻视线 |
| **Magmawing** | 熔岩飞龙(小型) |

#### 6.3 巨兽 (Megafauna) — 10种

**代码**: 怪物本体 `mob/living/simple_animal/hostile/megafauna/` (8个文件) + `mob/living/basic/boss/` (2个文件); 战利品 `lavaland/mining_loot/megafauna/` (9个文件)

| 巨兽 | 文件 | 简述 |
|---|---|---|
| **Ash Drake (灰烬龙)** | `megafauna/drake.dm` | 飞行喷火巨兽, 2500血, 死灵墓地守护者 |
| **Bubblegum (泡泡糖)** | `megafauna/bubblegum.dm` | 猩红巨兽, 最经典, 高血量狂暴近战 |
| **Clockwork Defender (齿轮守卫)** | `megafauna/clockwork_knight.dm` | 齿轮骑士残党, 300血, 冰月齿轮废墟, 极简单 |
| **Colossus (巨像)** | `megafauna/colossus.dm` | 巨型石像, 弹幕+激光 |
| **Hierophant (圣职者)** | `megafauna/hierophant.dm` | 传送+方块攻击 |
| **Legion (军团核心)** | `megafauna/legion.dm` | 骷髅王, 召唤骷髅军团 |
| **Demonic Frost Miner (恶魔冰矿工)** | `megafauna/demonic_frost_miner.dm` | 冰霜, 冰月环境 |
| **The Thing (怪物)** | `basic/boss/thing/thing.dm` | 恐惧之物, 附身AI躯壳 |
| **Blood Drunk (血醉者)** | `basic/boss/blood_drunk_miner/_blood_drunk_miner.dm` | 狂暴矿工, 电锯连击 |
| **Wendigo (温迪戈)** | `megafauna/wendigo.dm` | 冰原食人, 冰月环境 |

#### 6.4 巨兽战利品

| 来源 | 战利品 | 用途 |
|---|---|---|
| **Bubblegum** | 血腥之刃(mayhem) / 敌对环境套装(hostile_environment) | 近战武器 / 高防护套装(吸血) |
| **Ash Drake** | 龙鳞甲 / 熔火之心 / 幽灵刃 / 熔岩法杖 / 火球术书 / 龙血 | 防火装甲/召唤幽灵/造熔岩/变龙 |
| **Colossus** | 巨像盾 / 神之眼 | 远程武器 |
| **Hierophant** | 圣职者杖 / 传送核心 | 传送能力 |
| **Legion** | 军团之帽 | 召唤骷髅 |
| **Clockwork Defender** | 齿轮合金 (clockwork_alloy) | 最强齿轮骑士残骸 |
| **Demonic Frost Miner** | 复活水晶 / 诅咒冰靴 | 死亡重生 / 冰上加速(不可脱下) |
| **The Thing** | AI上行脑 (AI-uplink brain) | 可脱离躯壳/操控AI |
| **Blood Drunk** | 劈砍锯(cleaving saw) / 荒野猎手刀 | 电锯武器/高效屠宰 |
| **Wendigo** | 温迪戈之血 / 温迪戈颅骨 | 变身药剂/装饰追踪颅骨 |

#### 6.5 死灵宝箱

**代码**: `necropolis_chests.dm`

Lavaland上随机分布, 打开获得稀有物品/巨兽战利品。

#### 6.6 灰烬植物 (Ash Flora)

**代码**: `ash_flora.dm` (488行)

已在[水培章节]中覆盖: 9种Lavaland特有植物。

#### 6.7 怪物器官采集

**代码**: `equipment/monster_organs/`

| 器官 | 来源 | 用途 |
|---|---|---|
| **再生核心** | Goliath | 恢复生命 |
| **Brimdust囊** | 各种 | 爆炸物原料 |
| **冲刺腺** | 各种 | 加速 |
| **Lazarus注射器** | 可复活怪物当宠物 | |

#### 6.8 劳改营系统 (Labor Camp / Gulag)

**代码**: `laborcamp/` (`laborshuttle.dm` 43行 + `laborstacker.dm` 245行)

囚犯被流放到熔岩地劳改营挖矿还债, 挖满配额才能坐穿梭机回站。

**① 劳改穿梭机 (Labor Shuttle) — `laborshuttle.dm`**

| 部件 | 功能 |
|---|---|
| 劳改穿梭机控制台 `computer/shuttle/labor` | 双向调度 (站内⇄劳改营), 需**拘留所权限(ACCESS_BRIG)** |
| 单向囚犯控制台 `shuttle/labor/one_way` | 仅能把穿梭机叫到劳改营 (单程), 无权限要求 |
| 停靠港 | 站内 `laborcamp_home` (Delta/Kilo/Nebula 三种船型模板) + 劳改营 `laborcamp_away` |
| 单程限制 | 穿梭机已在劳改营时, 囚犯控制台拒绝再次召唤 |

**② 劳改堆叠机链 (Labor Stacker) — `laborstacker.dm`**

| 部件 | 功能 |
|---|---|
| 点券认领控制台 `labor_claim_console` | 认领堆叠机积攒的点数到囚犯ID; 达标后可呼叫穿梭机回家 |
| 劳改收集单元 `stacking_machine/laborstacker` | 自动堆叠矿石板材, 按 `gulag_value` 换算点数; 强制连接筒仓; 防御21防囚犯拆 |
| 点数查询台 `labor_points_checker` | 刷囚犯ID 播报配额进度 (当前点数/目标) |
| 囚犯ID卡 `card/id/advanced/prisoner` | 记录个人点数与配额目标 (goal) |

**③ 配额与释放机制**:
```
挖矿 → 板材入劳改收集单元 → 累积点数
→ 点券认领台刷卡 → 点数写入囚犯ID
→ 点数 ≥ 配额 → 可呼叫穿梭机回站
→ 回站后: 通缉状态清除(假释) + 安保频道公告释放
```

**④ 劳改巨石 (Gulag Boulders)**: 劳改营矿脉产出 `gulag_vent` 纯铁矿巨石 (16铁) 与 `gulag` 劣质巨石 (随机金/铁/等离子/银 10-13单位), 用镐敲开像开箱, 保证稳定出矿点。

**⑤ 安全措施**: 非人类/持有拘留所权限者不计入"待释放名单"; 释放时若劳改营内还有囚犯则拦截穿梭机; 撬开(emag)控制台可绕过配额直接呼叫。

### 七、Fulton回收系统

**代码**: `fulton.dm`

| 功能 | 说明 |
|---|---|
| 用 Fulton球+气球 把大型物品吊回太空站 |
| 可回收: 矿石/板条箱/机械/甚至昏迷的队友 |

### 八、生存小屋 (Shelters)

**代码**: `shelters.dm`

Lavaland上预制的避难所, 可用紧急装置展开。

### 九、辅助基地 (Auxiliary Base)

**代码**: `aux_base.dm` (449行) + `datums/shuttles/aux_base.dm` (穿梭机模板)

可从空间站向采矿点投放一座**可部署远征基地** (殖民舱), 落地后兼作采矿穿梭机的补给母港。

| 部件 | 功能 |
|---|---|
| 辅助基地管理控制台 `computer/auxiliary_base` | 投放/调度基地+炮塔控制; 需辅助基地权限(ACCESS_AUX_BASE)或指挥权限; 内置GPS(NT_AUX) |
| 着陆场标定器 `assault_pod/mining` | 在矿区地面使用→标定着陆区(5秒引导); 全区域版`/unrestricted`可任意投放 |
| 随机投放 | 控制台"随机"盲投到矿区随机矿物格 (不可控) |
| 基地炮塔 `porta_turret/aux_base` | 控制台可查看状态/远程开关, 基地自带的自动防御 |
| 采矿穿梭机信标 `mining_shuttle_beacon` | 部署在基地附近→为采矿穿梭机建立着陆港 (15格内需有基地控制台) |
| 落地后转换 | 基地落地矿区后, 控制台转为**采矿穿梭机控制台** (可调度采矿穿梭机往返基地) |

**投放流程**:
```
1. 标定着陆区 (设计器/随机投放)
2. 控制台确认投放 → 基地从空间站降落到矿区 (着陆区须在熔岩地表/冰月地下, 避开封闭墙/熔岩/矿物)
3. 部署采矿穿梭机信标 → 采矿穿梭机可停靠基地旁
4. 控制台切换为采矿穿梭机调度
```

### 十、矿工装备升级路径

```
初始: 采矿激光 + 勘探仪
  ↓ 券: 动能粉碎器/共振器
  ↓ 购买: 高级激光(更高效)
  ↓ 巨兽: 战利品武器(终极)
```

### 十一、矿工安全提示

| 风险 | 应对 |
|---|---|
| 高温环境 | 穿防火服/带冷却 |
| 熔岩池 | 跳过去/不要掉进去 |
| 巨兽巡逻区 | 远离/组队/打Boss准备充足 |
| 缺氧 | 自带O₂罐 |
| 迷路 | 标记信标/地图 |
| 时间不够 | 穿梭机按时返回 |
