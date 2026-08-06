# 天关 — wiremod 逻辑编程完全百科

> **项目**: TianGuan13 (Nova Sector → /tg/station)
> **代码**: `code/modules/wiremod/`（158 文件 14,098 行）
> **范围**: wiremod 电路逻辑编程系统——组件库/外壳/数据类型/集成
>
> **关联文档**：机器人/炮塔/自动驾驶集成与《战斗系统》《载具机甲》联动。

---

## 目录

- [第一卷 · 核心机制](#第一卷--核心机制)
- [第二卷 · 组件库全录](#第二卷--组件库全录)（120 种/123 类型端口详解）
- [第三卷 · 外壳系统](#第三卷--外壳系统)（19-21 种+容量档+NOVA 4 组件）
- [第四卷 · 实战教程](#第四卷--实战教程)
- [附录 · 代码路径索引](#附录--代码路径索引)

---

# 第一卷 · 核心机制

## 1.1 什么是 wiremod

wiremod 是 SS13 的**电路逻辑编程系统**——通过把"组件"（逻辑门/运算/信号）插入"集成芯片"，再用"外壳"封装成实际物品（炮塔/机器人/植入体等），实现自定义自动化。

**流程**：组件 → 集成芯片（接线）→ 外壳（封装）→ 实用物品

## 1.2 集成芯片（Integrated Circuit）

**代码**: `core/integrated_circuit.dm`（772 行）

| 机制 | 值 |
|---|---|
| 构成 | 电路板+电池+组件 |
| **电池** | 供电（组件每次输入耗电） |
| 开关 | 外壳控制 on/off |
| 锁定 | ID 授权锁定（防他人拆） |
| **变量系统** | 普通/可改/列表/关联列表 4 类 |
| setter-getter 上限 | 30 |
| 网格模式 | 组件网格布局 |
| USB 线 | 连接其他设备 |
| 组件打印机 | 远程打印组件 |

## 1.3 组件（Circuit Component）

**代码**: `core/component.dm`（439 行）

| 机制 | 值 |
|---|---|
| 端口 | 输入端口+输出端口列表 |
| **触发端口** | Trigger 输入/Triggered 输出（信号型） |
| 电路旗标 | CIRCUIT_FLAG_INPUT_SIGNAL/OUTPUT_SIGNAL |
| **耗电** | 0.001 × 标准电池/输入 |
| 组件大小 | circuit_size（占芯片空间） |
| 选项端口 | PORT_TYPE_OPTION（下拉选择） |
| 类别+UI 颜色 | 分类显示 |


## 1.5 运行系统

| 机制 | 值 |
|---|---|
| **SScircuit_component 子系统** | 每 **0.1 秒** tick 批量执行端口回调（防竞态） |
| 瞬时执行链 | instant/response signal 用于即时判定（气闸门禁） |
| **外壳供电** | SHELL_FLAG_REQUIRE_ANCHOR 从电网取电 |
| 超载 | 每分钟超 20× 标准电量→爆炸 |
| **USB 端口机器（11 种）** | 空气警报/泵/阀门/温度机/气压计/处理器/水培/发射器/传送带 |

## 1.6 机器集成

| 集成方式 | 说明 |
|---|---|
| **USB 端口** | 11 种机器可插 USB 线接入电路 |
| **设备外壳** | 4 种（相机/合成器/模块计算机） |
| **MOD 模块** | mod_program 11 个程序集成 |
| 组件打印机 | 打印电路组件 |

> **如实说明**：本代码库**无炮塔专用组件**（上游 tgstation 也无）——炮塔自动化需用枪械外壳+传感器组合实现。


## 1.4 数据类型（11 种）

**代码**: `__DEFINES/wiremod.dm`

| 类型 | 说明 |
|---|---|
| **any** | 任意 |
| **string** | 字符串 |
| **number** | 数字 |
| **signal** | 信号（触发） |
| **instant signal** | 即时信号 |
| **response signal** | 响应信号 |
| **table** | 表格 |
| **option** | 选项 |
| **boolean** | 布尔 |
| **list** | 列表（复合：PORT_TYPE_LIST(类型)） |
| **assoc list** | 关联列表（复合） |
| **entity** | 实体引用 |
| **datum / user** | 数据/用户 |

> **隐式转换**：数字→字符串等自动转换（convert_value）；输入端口检查兼容性（can_receive_from）。

---


---

# 第二卷 · 组件库全录

**代码**: `wiremod/components/`（7,976 行）——**120 个组件**

## 2.1 组件类别总览

| 类别 | 数量 | 组件 |
|---|---|---|
| **List 列表** | 21 | 列表字面量/拼接/索引/添加/移除/清空/过滤/循环/拆分/关联列表操作/表操作 |
| **Admin 管理** | 15 | 动画/滤镜/Proc 调用/SDQL/信号处理/生成物/变量读写 |
| **Entity 实体** | 15 | 方向/GPS/健康/健康状态/语音激活/材质扫描/定位器/试剂扫描/远程摄像头(含4变体)/物种/注射器/自引用 |
| **Utility 通用** | 14 | 时钟/延迟/NFC/路由器/多路复用器/类型转换/线束/线网收发 |
| **Math 数学** | 11 | 算术/比较/逻辑/非/随机/三角/二进制/十进制/长度/切换/反正切 |
| **Action 动作** | 11 | 激光笔/灯/MMI/寻路/开始拉拽/无线电/音效/语音/摄像头/程序摄像头/装备动作 |
| **BCI 脑机** | 8 | BCI 摄像头/条形覆盖/计数覆盖/物体覆盖/拦截/思想监听/VOX 广播/安装检测 |
| **String 字符串** | 5 | 拼接/包含/大小写/转数字/转字符串 |
| **ID 身份** | 4 | 门禁检查/读 ID 权限/获取 ID/读 ID 信息 |
| **NTNet 网络** | 3 | 接收/发送/发送列表 |
| **Sensor 传感器** | 3 | 压力/温度/视野 |
| **Logic/Signal/IO** | 18 | 比较/输入/输出/变量读写/摄像头/发射器/温度控制/多路选择器 |

## 2.2 逻辑门（compare/logic 选项模式）

> 逻辑组件用"选项端口"实现多模式（一个组件=多个门）。

| 模式 | 功能 |
|---|---|
| **AND** | 全真才真 |
| **OR** | 一真即真 |
| **XOR** | 异或 |
| **NAND/NOR** | 与非/或非 |
| **比较** | 大于/小于/等于（compare/comparison） |
| **切换 Toggle** | 翻转状态 |
| **Not** | 非门 |

## 2.3 数学组件

| 组件 | 功能 |
|---|---|
| **算术 Arithmetic** | 加减乘除（选项模式） |
| **三角函数** | sin/cos/tan |
| **反正切 2** | atan2 |
| **随机 Random** | 随机数 |
| **长度 Length** | 列表/字符串长度 |
| **二进制/十进制转换** | 进制转换 |
| **比较 Comparison** | 大小比较 |

## 2.4 实用组件详解

| 组件 | 功能 |
|---|---|
| **延迟 Delay** | 延时触发（定时器） |
| **时钟/时间片** | 时间读取 |
| **路由器 Router** | 信号分流 |
| **多路选择器 Multiplexer** | 多选一 |
| **线束 Wire Bundle** | 多条线打包 |
| **NFC/线网/网络收发** | 无线/有线通信 |
| **类型转换 Typecast** | 类型强制转换 |
| **变量 Getter/Setter** | 读写变量 |

## 2.5 传感器组件

| 组件 | 功能 |
|---|---|
| **压力传感器** | 读取气压 |
| **温度传感器** | 读取温度 |
| **视野传感器** | 读取视野信息 |
| **材质扫描器** | 扫描材料 |
| **试剂扫描器** | 扫描试剂 |
| **GPS** | 位置定位 |
| **健康读取** | 目标生命值 |
| **语音激活** | 声音触发 |

## 2.6 动作组件

| 组件 | 功能 |
|---|---|
| **灯** | 控制灯光 |
| **无线电** | 语音通信 |
| **语音 Speech** | 说话 |
| **音效发射器** | 播放音效 |
| **激光笔** | 激光指示 |
| **寻路 Pathfinder** | 路径查找 |
| **开始拉拽** | 拉动物体 |
| **MMI** | 脑机接口 |



---

# 第三卷 · 外壳系统

**代码**: `wiremod/shell/`（2,536 行）——**19 种外壳**

## 3.1 外壳全录

| 外壳 | 用途 | 特性 |
|---|---|---|
| **组装机器人 Bot Assembly** | 机器人外壳 | 电路驱动机器人 |
| **货币机器人 Moneybot** | 收费机器人 | 投币/收费 |
| **无人机 Drone** | 无人机 | 自主无人机 |
| **服务器 Server** | 服务器 | 电路服务器 |
| **气闸 Airlock** | 气闸门 | 电路控制气闸（自动门） |
| **分发器 Dispenser** | 自动售货 | 电路售货机 |
| **脑机接口 BCI** | 脑植入体 | **植入大脑**；脑机接口 |
| **扫描门 Scanner Gate** | 安检门 | 电路扫描门 |
| **紧凑遥控 Compact Remote** | 手持遥控 | 便携电路控制 |
| **控制器 Controller** | 控制器 | 通用控制 |
| **枪械 Wiremod Gun** | **能量枪** | 电路控制发射（可编程武器） |
| **植入体 Implant** | 皮下植入 | 电路植入体 |
| **键盘 Keyboard** | 键盘外壳 | 键盘输入电路 |
| **MOD 模块 Module** | MOD 服模块 | 电路版 MOD 模块 |
| **扫描器 Scanner** | 手持扫描 | 电路扫描 |
| **地下 Undertile** | 地板下 | 隐藏电路 |
| **壁挂 Wallmount** | 墙壁 | 挂墙电路 |
| **组装件 Assembly** | 通用组装 | 组装式 |

## 3.2 外壳选择指南

| 需求 | 推荐外壳 |
|---|---|
| 自动门 | 气闸外壳 |
| 便携遥控 | 紧凑遥控/键盘 |
| 隐形监控 | 地下/壁挂 |
| 战斗武器 | 枪械外壳 |
| 个人装置 | 植入体/BCI |
| 自动化机器人 | 机器人/无人机/服务器 |

## 3.3 特殊外壳详解

| 外壳 | 详解 |
|---|---|
| **BCI 脑机接口** | 植入大脑的芯片；思想监听/VOX 广播/摄像头；BCI 植入器机器 |
| **Wiremod 枪** | 能量枪：电路控制射击逻辑（自定义弹道行为） |
| **MOD 模块** | 装进 MOD 服的电路模块（复用 MOD 电源） |
| **紧凑遥控** | 手持式：按钮+屏幕控制电路 |



---

# 第四卷 · 实战教程

## 4.1 入门流程

```
1. 找组件打印机（component printer）→ 打印组件
2. 拿集成芯片（integrated circuit）→ 插入组件
3. 用线连接端口（输入→输出）
4. 放入外壳（shell）→ 组装成实用物品
5. 电池供电 → 激活
```

## 4.2 经典电路示例

### 自动门（气闸外壳）
```
压力传感器 → 比较（>50）→ 气闸打开
温度传感器 → 比较（>300K）→ 警报灯
```

### 自动售货（分发器外壳）
```
投币检测 → 货币机器人 → 发放物品
NFC 接收 → 门禁检查 → 解锁
```

### 智能炮塔（枪械外壳）
```
视野传感器 → 实体检测 → 开火
健康读取 → 比较（<50）→ 治疗
```

### 巡逻机器人（机器人外壳）
```
路径寻找 → 移动指令 → 巡逻
GPS → 位置记录 → 返回基地
```

## 4.3 组件组合技巧

| 组合 | 效果 |
|---|---|
| **传感器 + 比较 + 动作** | 自动化检测响应 |
| **变量 setter + getter** | 状态记忆 |
| **延迟 + 循环** | 定时任务 |
| **列表 + 遍历** | 批量处理 |
| **路由器 + 多路选择器** | 信号分流 |

## 4.4 注意事项

- 组件每次输入**耗电**（0.001 × 电池）——大电路耗电快
- 信号组件（Trigger）用于控制流，数据组件传值
- 锁定电路防他人篡改
- 变量上限 30 个 setter-getter



## 2.7 组件端口级详解（137 类型完整）

> 每个组件 = 输入端口/输出端口/类型。以下为全部组件逐条详解（数量以源码为准：数学 11、字符串 5、列表 21、工具 14，其余见其他类）。

### 数学类（11 种）

| 组件 | 功能 | 端口 |
|---|---|---|
| **算术运算 Arithmetic** | 加/减/乘/除/取模/最小/最大/幂（2~N 输入） | 选项(运算符)+Port 1..N → Output |
| **逻辑门 Logic** | AND/OR/XOR/NAND/NOR（多输入） | 选项+Port 1..N+Compare → True/False+Result |
| **非门 Not** | 反转 | Input → Result |
| **翻转开关 Toggle** | 触发翻转开/关 | Set Toggle State+Toggle And Compare → True/False+Result |
| **比较器 Comparison** | =/≠/>/</≥/≤ | 选项+A+B+Compare → True/False+Result |
| **反正切 2 Arctan2** | atan2（ΔX/ΔY→角度） | Delta X+Delta Y → Angle |
| **三角函数 Trigonometry** | sin/cos/tan/asin/acos/atan | 选项+Input → Output |
| **二进制转换 Binary Conversion** | 十进制数字→位数组（动态 N 位输出） | Number → Bit 1..N |
| **十进制转换 Decimal Conversion** | 位数组（动态 N 位输入）→十进制数字 | Bit 1..N → Number |
| **长度 Length** | 列表/字符串长度 | Input → Length |
| **随机数 Random** | min-max 随机整数 | Min+Max → Output |

### 字符串类（5 种）

| 组件 | 功能 | 端口 |
|---|---|---|
| **拼接 Concat** | 2~N 字符串连接 | 动态输入 → Output |
| **包含 Contains** | 检查子串 | Needle+Haystack → True/False+Result |
| **大小写 Textcase** | 转大写/小写 | 选项+Input → Output |
| **转数字 Tonumber** | 字符串→数字 | Input → Output |
| **转字符串 Tostring** | 任意值→文本（实体需 7 格内） | Input → Output |

### 列表类（21 种）

| 组件 | 功能 | 端口 |
|---|---|---|
| **列表字面量 List Literal** | 1~20 输入构成列表 | 选项(List Type)+Port 1..N → Value |
| **关联列表字面量 Assoc Literal** | 键值对构成关联列表 | 选项+键/值端口对 → Value |
| **索引取值 Index** | 按索引取值 | 选项+List+Index → Value |
| **关联索引取值 Index Assoc** | 按键(字符串)取值 | 选项+List+Key → Value |
| **元素查找 Element Find** | 返回是否找到+索引（失败 0） | 选项+List+To Check → Succeeded/Failed+Result+Index |
| **列表选取 List Pick** | 弹窗给玩家选 | List+Input Name+User → Picked Item+On Failure+On Success |
| **关联列表选取 Assoc Pick** | 弹窗选关联值 | 选项+List+User → Picked Item |
| **列表拼接 Concatenate** | 列表→字符串（带分隔符） | List+Separator → Output |
| **拆分 Split** | 字符串→列表（按分隔符） | Input+Separator → Output |
| **格式化 Format** | %1 %2 占位符替换 | Params+Format → Output |
| **关联格式化 Format Assoc** | 关联列表占位符替换 | Params+Format → Output |
| **遍历 For Each** | 逐元素触发+Next Index | List+Next Index+Reset And Trigger → Element+Index+Next+On Finished |
| **过滤 Filter** | Accept Entry 判定（上限 300 次） | List+Accept Entry → Element+Index+Next+Filtered List+On Finished/Failed |
| **列表添加 List Add** | 列表变量追加元素（上限 500） | 选项(Variable)+To Add+Allow Duplicate → Failed |
| **列表移除 List Remove** | 列表变量移除元素 | 选项(Variable)+To Remove → — |
| **列表清空 List Clear** | 清空列表变量 | 选项(Variable) → — |
| **关联列表设置 Assoc Set** | 关联列表变量设键值 | 选项(Variable)+Key+Value → Failed |
| **关联列表移除 Assoc Remove** | 关联列表变量删键 | 选项(Variable)+To Remove → — |
| **取列 Get Column** | 表→列（列表） | Input+Column Name → Output |
| **表索引 Index Table** | 表→行（关联列表） | Input+Index → Output |
| **选择查询 Select Query** | 条件筛选表行 | 选项(比较)+Input+Column Name+Comparison Input → Output |

### 工具类（14 种）

| 组件 | 功能 | 端口 |
|---|---|---|
| **时钟 Clock** | 约 1 秒周期信号 | On → Signal |
| **延迟 Delay** | 触发延迟秒数（可打断） | Delay+Trigger+Interrupt → Result |
| **时间钟表 Timepiece** | 站内时间读取 | 选项(格式+单位) → Time Format+Unit of Time |
| **路由器 Router** | 4×4 分流（类型可选） | 选项+Input Selector+Output Selector+Input 1..N → Output 1..N |
| **多路复用器 Multiplexer** | 4 选 1 | 选项+Input Selector+Output Selector+Input 1..N → Output 1..N |
| **类型检查 Typecheck** | 类型判断 | 选项+Value → True/False+Result |
| **类型转换 Typecast** | 强制转换类型 | 选项+Input → Output |
| **线束 Wire Bundle** | 暴露电线端口 | Pulse[线名] 1..N → [线名] Pulsed 1..N |
| **NFC 发送 NFC Transmitter** | 无线发送数据包 | 选项+Data Package+Encryption Key+Target → — |
| **NFC 接收 NFC Receiver** | 无线接收数据包 | 选项+Encryption Key → Data Package |
| **NFC 发送列表 NFC Send Literal** | 字面量值无线发送 | Encryption Key+Target+字面量值 → — |
| **线网发送 Wirenet Transmitter** | 有线(线网)发送 | 选项+Data Package+Encryption Key → — |
| **线网接收 Wirenet Receiver** | 有线(线网)接收 | 选项+Encryption Key → Data Package |
| **线网发送列表 Wirenet Send Literal** | 字面量值有线发送 | Encryption Key+字面量值 → — |

### 其他类

| 类 | 组件 |
|---|---|
| **ID（4）** | 权限检查(compare/access)/读权限(id_access_reader)/取 ID(id_getter)/读信息(id_info_reader) |
| **USB 设备（3）** | 发射器(emitter)/温度泵(atmos_temperature_pump)/温控机(thermomachine) |

### Admin 管理组件族（14 个 · 源码全量）

> **源码**: `code/modules/wiremod/components/admin/`（11 文件）。管理员/开发者向的高级组件——可操作目标对象的变量、调用 proc、执行 SDQL、生成物体。此前文档未覆盖，现补全。

| 组件 | 功能 |
|---|---|
| **Begin Animation 开始动画** | 在目标上开始动画；通过串联 Animation Step 创建动画步骤 |
| **Animation Step 动画步骤** | 执行单步动画；输入应直连或间接来自 Begin Animation |
| **Filter Parameter Helper 滤镜参数助手** | 从输入构造滤镜参数列表 |
| **Add Filter 添加滤镜** | 给目标 atom 添加滤镜 |
| **Filter Remover 移除滤镜** | 从目标移除指定滤镜 |
| **Animation & Filter Bitflag Helper 位标志助手** | 构造 BYOND 动画与滤镜参数的位标志 |
| **Get Variable 获取变量** | 获取对象上的变量值 |
| **Set Variable 设置变量** | 设置对象上的变量值 |
| **Proc Call 调用 Proc** | 调用对象上的 proc |
| **Input Request 输入请求** | 把字符串转换为 typepath（用于添加组件） |
| **Save Shell 保存外壳** | 保存一个外壳（shell） |
| **SDQL Operation SDQL 操作** | 被调用时执行一次 SDQL 操作 |
| **Spawn Atom 生成物体** | 在指定位置生成一个 atom |
| **String To Type 字符串转类型** | 把字符串转换为 typepath |


| **动作（11）** | 语音(speech)/音效(soundemitter)/灯(light)/无线电(radio)/寻路(pathfind)/拉拽(pull)/激光笔(laserpointer)/MMI(mmi)/摄像头(camera)/程序摄像头(mod_program/camera)/装备动作(equipment_action) |
| **BCI（8）** | 摄像头拦截(target_intercept)/物体覆盖(object_overlay)/条形覆盖(object_overlay/bar)/计数覆盖(counter_overlay)/思想监听(thought_listener)/VOX 广播(vox)/试剂注入器(reagent_injector)/安装检测(install_detector) |
| **实体（15）** | 方向(direction)/GPS(gps)/健康(health)/健康状态(health_state)/语音激活(hear)/材质扫描(matscanner)/定位器(pinpointer)/试剂扫描(reagentscanner)/远程摄像头(remotecam，含 bci/drone/airlock/polaroid 四变体)/物种(species)/自引用(self) |
| **管理（15）** | 动画(begin_animation+animation_step)/滤镜(filter_helper+filter_adder+filter_remover)/位标志(bitflag_helper)/Proc 调用(proccall)/SDQL(sdql_operation)/信号处理(signal_handler)/生成物(spawn_atom)/变量读写(get_variable+set_variable)/类型转换(to_type)/输入请求(input_request)/外壳保存(save_shell) |
| **传感器（3）** | 压力(pressuresensor)/温度(tempsensor)/视野(view_sensor，5 格) |
| **NTNet 网络（3）** | 发送(ntnet_send)/接收(ntnet_receive)/发送列表(ntnet_send_literal) |
| **变量（3）** | 读取(variable/getter)/写入(variable/setter)/触发写入(variable/setter/trigger，均需先选 Variable 选项) |



## 3.4 外壳容量档位

| 档位 | 容量 |
|---|---|
| TINY | 12 |
| SMALL | 30 |
| MEDIUM | 80 |
| LARGE | 200 |
| VERY_LARGE | 500 |

## 3.5 NOVA 新增组件（additional_circuit 模块）

| 组件 | 功能 | 限定 |
|---|---|---|
| **Mine 挖矿** | 挖掘 | 通用 |
| **Target Scanner 目标扫描** | 扫描目标 | 通用 |
| **Cell Charge 电池充电** | 充电 | 无人机外壳 |
| **Item Interact 物品交互** | 与物品互动 | 无人机外壳 |


# 附录 · 代码路径索引

| 系统 | 文件 | 行数 |
|---|---|---|
| 集成芯片 | `wiremod/core/integrated_circuit.dm` | 772 |
| 组件基类 | `wiremod/core/component.dm` | 439 |
| 端口系统 | `wiremod/core/port.dm` | 239 |
| 数据类型 | `wiremod/core/datatypes.dm` + `__DEFINES/wiremod.dm` | 118+ |
| 组件打印机 | `wiremod/core/component_printer.dm` | 552 |
| 复制器 | `wiremod/core/duplicator.dm` | 244 |
| USB 线 | `wiremod/core/usb_cable.dm` | 124 |
| 程序电路 | `wiremod/core/program_circuit.dm` | — |
| 管理面板 | `wiremod/core/admin_panel.dm` | — |
| **组件库** | `wiremod/components/` | **7,976** |
| **外壳** | `wiremod/shell/`（19 种） | **2,536** |
| DCS 组件 | `wiremod/dcs_components/` | 362 |
| 数据类型 | `wiremod/datatypes/` | 314 |
| 预设 | `wiremod/preset/` | 34 |
