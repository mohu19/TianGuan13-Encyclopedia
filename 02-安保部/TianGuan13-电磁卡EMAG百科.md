# TianGuan13 电磁卡 EMAG 百科 (Cryptographic Sequencer Encyclopedia)

> 基于 TianGuan13 NovaSector 分支源码全量整理。本体：`code/game/objects/items/emags.dm`（253 行）+ NOVA 覆写 `modular_nova/master_files/code/game/objects/items/emags.dm`；Uplink 条目：`code/modules/uplink/uplink_items/device_tools.dm`。
> **全库 149 处 `emag_act` 钩子**——每个可骇设备都有自己的专属效果。

## 概述

**EMAG（电磁卡 / Cryptographic Sequencer / 密码序列器）**是反派（叛徒等）的万能骇入设备——一张带磁条的卡片，能解锁电子设备中的隐藏功能、颠覆预期用途、轻松破坏安全机制。

**核心**:
- Uplink 价格 **4 TC**（`device_tools/emag`）
- 可骇入 **149 处设备钩子**（25+ 子系统）
- **不能开气闸**——开门需要专门的"门磁卡"（doorjack）
- 使用有提示：骇入时目标附近的旁观者能看到动作

---

## 一、EMAG 本体（/obj/item/card/emag）

| 属性 | 值 |
|---|---|
| 名称 | cryptographic sequencer（密码序列器） |
| 图标 | emag（红色卡片样式） |
| 槽位 | ITEM_SLOT_ID（可插 ID 槽） |
| 描述 | "一张带磁条和电路的卡片" |
| 近程检查 | `prox_check = TRUE`（需贴近目标） |
| 黑名单 | 气闸/玻璃门/防火门（需专用门磁卡） |

**骇入流程**（`interact_with_atom` → `emag_act`）：
1. 对目标使用 EMAG
2. 检查黑名单（`can_emag`）——气闸类被拦下："需要更专业的骇入设备"
3. 调用目标的 `emag_act()`——每个设备有专属效果
4. 成功：记录 `log_combat` + 黑箱统计（`SSblackbox` "atom_emagged"）
5. **注意**：骇入过程有可见动作（旁观者能看到"某某正在用卡戳机器"）

### 1.1 变体全录

| 变体 | 名称 | 特点 |
|---|---|---|
| **标准 EMAG** | cryptographic sequencer | 基础版，近程骇入，4 TC |
| **蓝空间 EMAG** | bluespace cryptographic sequencer | `prox_check = FALSE` —— **远程骇入**！蓝色卡身 |
| **门磁卡 Doorjack** | modified ID card | 专骇气闸/玻璃门/防火门；**3 次充能**，每 **3 分钟**回充 1 次 |
| **战列巡洋舰卡** | battlecruiser coordinates upload card | 对通信控制台使用 → **召唤辛迪加舰队**；一次性 |
| **假 EMAG** | emagfake | Donk Co. 仿冒品；被真 EMAG 处理会**爆炸**（1 格冲击） |
| **万圣节 EMAG** | hack-o'-lantern | 南瓜造型皮肤 |
| **蓝色 EMAG** | (blue) | 蓝色皮肤（非远程版） |

### 1.2 彩蛋：EMAG 吸 EMAG

把两张 EMAG 对在一起（emag_act 目标也是 EMAG）会**磁力吸附叠在一起**——最多 128 张叠成一摞（防 sprite 过载上限）。叠卡后名字变成 "cryptographic sequencer-[另一张的名字]"。

### 1.3 NOVA 覆写

- 特殊检查描述（`EXAMINE_CHECK_SYNDICATE`）：只有**辛迪加阵营**能看出 EMAG 的真实用途；普通人看到的是"改装过的 ID 卡"
- 假卡同理：辛迪加玩具检查（`EXAMINE_CHECK_SYNDICATE_TOY`）才能识破 Donk Co. 仿冒

---

## 二、获取方式

| 途径 | 价格/条件 |
|---|---|
| **叛徒 Uplink** | **4 TC**（device_tools/emag）——最常见 |
| **核弹队 Uplink** | 核弹队上链可购 |
| **门磁卡 Uplink** | 专门条目（doorjack，3 充能） |
| **战列巡洋舰卡** | 特殊反派（战列巡洋舰队）持有 |
| **地图生成** | 部分地图/任务可能自带（如间谍藏匿点） |
| **假卡** | 玩具店/杂物掉落（Donk Co. 仿冒，会爆炸） |

---

## 三、骇入效果全录（149 处钩子分类）

> 每个可骇设备的专属效果（按子系统分类，钩子数量=源码 `emag_act` 计数）。

### 3.1 机械类（34 处 · game/machinery）

| 设备 | EMAG 效果 |
|---|---|
| **气闸** | ⛔ 普通 EMAG 不可用（黑名单）——需门磁卡 |
| **APC 电源** | 解锁电源面板（可改区域供电） |
| **贩卖机** | 解锁违禁商品/免费模式（详见售货机百科） |
| **ATM/银行机** | 提款模式/信用点操纵 |
| **医疗机器** | 解锁隐藏模式（如基因改造器） |
| **通信控制台** | 战列巡洋舰卡专用：召唤舰队 |
| **传送机** | 解锁目标重定向 |
| **武器机** | 解锁军火库模式 |
| **其他机械** | 各类隐藏功能（详见各子系统百科） |

### 3.2 物品类（29 处 · game/objects）

| 物品 | EMAG 效果 |
|---|---|
| **电路板** | 解锁隐藏电路（如军械库/货舱电路板 4 处） |
| **储物柜** | 解锁锁定储物柜 |
| **保险箱** | 破解密码锁 |
| **枪械** | 解锁非法模式（自动/连发） |
| **玩具** | 3 处玩具被 EMAG 后有特殊反应 |

### 3.3 NOVA 专属（20 处 · modular_nova）

| 模块 | EMAG 效果 |
|---|---|
| **植入体** | 手臂增强 4 处（augments_arms） |
| **售货机** | NOVA 售货机解锁隐藏商品 |
| **安保改革** | 电击棒区域锁破解（部门外可用） |
| **超物质熔毁** | SCRAM 系统免疫（emag 免疫大红按钮） |
| **穿梭机/其他** | NOVA 新增设备的骇入效果 |

### 3.4 其他子系统（66 处 · modules/）

| 子系统 | 钩子数 | 典型效果 |
|---|---|---|
| mob（生物） | 7 | 生物相关骇入（如 AI/机器人） |
| shuttle（穿梭机） | 6 | 穿梭机控制系统 |
| research（科研） | 5 | 实验机/设计台 |
| transport（运输） | 5 | 传送带/运输系统 |
| clothing（服装） | 3 | 服装内置设备 |
| mining（采矿） | 3 | 采矿机 |
| paperwork（文书） | 3 | 打印/文件机 |
| power（电力） | 3 | 发电设备 |
| projectiles（弹道） | 3 | 武器系统 |
| surgery（手术） | 3 | 手术台 |
| cargo/vehicles/mod/pai 等 | 2+ | 各系统专属效果 |

---

## 四、门磁卡 Doorjack 详解

| 属性 | 值 |
|---|---|
| 名称 | modified ID card（改装 ID 卡） |
| 描述 | "专用于覆盖站内气闸门禁的密码序列器，使用自回充能量骇入气闸" |
| 充能 | **3 次**（max_charges = 3） |
| 回充 | 每 **3 分钟**（1800 ticks）回充 1 次，有充电进度条显示 |
| 白名单 | 气闸/玻璃门/防火门（普通 EMAG 的黑名单正好是它的白名单） |
| 限制 | 对储物容器无效（atom_storage 跳过） |

**用法**: 对门使用 → 消耗 1 充能 → 门被 EMAG（解锁/伪装成已授权）→ 3 分钟后充能恢复。流窜作案必备。

---

## 五、实战要点（反派向）

1. **开局先买 EMAG**（4 TC）——性价比最高的万能工具
2. **开门用门磁卡**（另购），EMAG 留给机器——EMAG 对气闸是黑名单，别浪费
3. **蓝空间 EMAG 值钱**：远程骇入可以隔墙/隔门操作（prox_check = FALSE）
4. **骇入有动静**：动作可见——别在人多处动手；语音播报可能触发警报
5. **假 EMAG 是陷阱**：如果目标是 EMAG 且你"成功"了，检查是不是假卡——真的会被引爆
6. **战列巡洋舰卡**：对通信台用一次就是一场灾难（舰队来袭）
7. **骇入前确认设备**：149 处钩子效果各异——贩卖机是免费用、APC 是改供电、武器机是军火库模式，先想好要干什么

---

## 六、一句话总结

**4 TC 一张卡，149 种隐藏功能** —— 机器都能"解锁"，门要用门磁卡，蓝空间版能隔空操作，假卡会炸脸。

> 源码索引：`emags.dm`（本体+变体）/ `device_tools.dm`（Uplink 4TC）/ `emag_act` 钩子全库 149 处（game/machinery 34 + game/objects 29 + NOVA 20 + modules 66）
