# 天关 — 供应与采矿完全百科

> **代码**: `code/modules/cargo/` (70+文件), `code/modules/mining/` (40+文件), `code/modules/jobs/job_types/cargo_technician.dm`, `code/modules/jobs/job_types/quartermaster.dm`, `code/modules/jobs/job_types/shaft_miner.dm`

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

#### 5.2 矿料加工机

**代码**: `machine_processing.dm` (290行)

处理 ORM 分散的材料 → 提供工程/科研使用

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

#### 6.3 巨兽 (Megafauna) — 9种

**代码**: `lavaland/mining_loot/megafauna/` (9个文件)

| 巨兽 | 文件 | 简述 |
|---|---|---|
| **Ash Drake (灰烬龙)** | `ash_drake.dm` | 飞行喷火巨兽 |
| **Bubblegum (泡泡糖)** | `bubblegum.dm` | 猩红巨兽, 最经典 |
| **Colossus (巨像)** | `colossus.dm` | 巨型石像 |
| **Hierophant (圣职者)** | `hierophant.dm` | 传送+方块攻击 |
| **Legion (军团核心)** | `legion.dm` | 骷髅王 |
| **Blood Drunk (血醉者)** | `blood_drunk.dm` | 狂暴矿工 |
| **Demonic Frost Miner (恶魔冰矿工)** | `demonic_frost_miner.dm` | 冰霜 |
| **The Thing (怪物)** | `the_thing.dm` | 恐惧之物 |
| **Wendigo (温迪戈)** | `wendigo.dm` | 冰原食人 |

#### 6.4 巨兽战利品

| 来源 | 战利品 | 用途 |
|---|---|---|
| **Bubblegum** | 血腥之刃 / 杀戮手套 | 近战武器 |
| **Ash Drake** | 龙鳞甲 / 熔火之心 | 防火装甲 |
| **Colossus** | 巨像盾 / 神之眼 | 远程武器 |
| **Hierophant** | 圣职者杖 / 传送核心 | 传送能力 |
| **Legion** | 军团之帽 | 召唤骷髅 |
| **Wendigo** | 冰之心 | 冰冻武器 |

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

### 七、Fulton回收系统

**代码**: `fulton.dm`

| 功能 | 说明 |
|---|---|
| 用 Fulton球+气球 把大型物品吊回太空站 |
| 可回收: 矿石/板条箱/机械/甚至昏迷的队友 |

### 八、生存小屋 (Shelters)

**代码**: `shelters.dm`

Lavaland上预制的避难所, 可用紧急装置展开。

### 九、矿工装备升级路径

```
初始: 采矿激光 + 勘探仪
  ↓ 券: 动能粉碎器/共振器
  ↓ 购买: 高级激光(更高效)
  ↓ 巨兽: 战利品武器(终极)
```

### 十、矿工安全提示

| 风险 | 应对 |
|---|---|
| 高温环境 | 穿防火服/带冷却 |
| 熔岩池 | 跳过去/不要掉进去 |
| 巨兽巡逻区 | 远离/组队/打Boss准备充足 |
| 缺氧 | 自带O₂罐 |
| 迷路 | 标记信标/地图 |
| 时间不够 | 穿梭机按时返回 |
