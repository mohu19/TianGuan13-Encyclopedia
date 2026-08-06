# TianGuan13 电磁卡 EMAG 百科 (Cryptographic Sequencer Encyclopedia)

> 基于 TianGuan13 NovaSector 分支源码全量整理。本体：`code/game/objects/items/emags.dm`（253 行）+ NOVA 覆写 `modular_nova/master_files/code/game/objects/items/emags.dm`；Uplink 条目：`code/modules/uplink/uplink_items/device_tools.dm`。
> **全库 149 处 `emag_act` 钩子**——每个可骇设备都有自己的专属效果。

## 概述

**EMAG（电磁卡 / Cryptographic Sequencer / 密码序列器）**是反派（叛徒等）的万能骇入设备——一张带磁条的卡片，能解锁电子设备中的隐藏功能、颠覆预期用途、轻松破坏安全机制。

**核心**:
- Uplink 价格 **3 TC**（`device_tools/emag`）
- 可骇入 **143 处设备钩子**（全量明细见第三章）
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
| **标准 EMAG** | cryptographic sequencer | 基础版，近程骇入，3 TC |
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
| **叛徒 Uplink** | **3 TC**（device_tools/emag）——最常见 |
| **核弹队 Uplink** | 核弹队上链可购 |
| **门磁卡 Uplink** | 专门条目（doorjack，3 充能） |
| **战列巡洋舰卡** | 特殊反派（战列巡洋舰队）持有 |
| **地图生成** | 部分地图/任务可能自带（如间谍藏匿点） |
| **假卡** | 玩具店/杂物掉落（Donk Co. 仿冒，会爆炸） |

---

## 三、骇入效果全录（143 个 · 源码全量）

> 全库 `emag_act` 钩子 **143 个**（去重后），每个设备的专属骇入效果。全部从源码逐条核实。

| 设备类型路径 | 中文名 | 源码文件 | EMAG 骇入效果 |
|---|---|---|---|
| /obj/machinery/announcement_system | 公告系统 | code\game\machinery\announcement_system.dm | 机器进入 EMP 故障态；所有可修改的公告配置条目被替换为乱码（到达公告→"#!@%ERR-34%2 CANNOT LOCAT@# JO# F*LE!"、部门主管公告→"OV#RL()D: [UNKNOWN??] DET*#CT)D!"、研究节点公告→"BEPIS BEPIS BEPIS" 等随机乱码），公告内容全部损坏 |
| /obj/machinery/barsign | 酒吧招牌 | code\game\machinery\barsigns.dm | 加载非法招牌：10 秒后自动把招牌替换为隐藏辛迪加酒吧招牌(syndibarsign)，招牌名称被覆盖 |
| /obj/machinery/button | 按钮 | code\game\machinery\buttons.dm | 清空 req_access/req_one_access 访问限制，任何人可按下；并尝试对被控设备(device)递归 emag（如门禁按钮背后的门），返回自主反馈"access overridden" |
| /obj/machinery/digital_clock | 数字时钟 | code\game\machinery\digital_clock.dm | 时间显示乱码：分钟数变为随机 0-99 而非真实时间（持续乱跳）；可用万用表对已 emag 时钟操作 6 秒解除 |
| /obj/machinery/fat_sucker | 脂肪抽取器（脂质提取机） | code\game\machinery\fat_sucker.dm | 解除厨房权限限制（req_access 失效）；阈值改为 start_at=100、stop_at=0：从极低营养水平即可开始抽脂，并一直抽到 0；alt-click 锁定/解锁安全舱口不再需要权限 |
| /obj/machinery/firealarm | 火灾报警器 | code\game\machinery\firealarm.dm | 电路烧毁：报警鸣笛强制停止、灯光变蓝(蓝光常亮)、区域起火时也不报警；AI 无法再切换自动火灾探测 |
| /obj/machinery/gulag_item_reclaimer | 劳改营装备回收站 | code\game\machinery\gulag_item_reclaimer.dm | ID 校验器故障：req_access 清空，任何人无需刷犯人卡/警卫权限即可回收站内全部存储物品；屏幕图标变为 emagged_general |
| /obj/machinery/harvester | 器官收割机 | code\game\machinery\harvester.dm | 生命体征扫描仪过载：allow_living=TRUE、allow_clothing=TRUE，不再要求尸体或脱光衣物，可直接对活人、穿装备者启动收割（活人会被通知"about to be ground up"） |
| /obj/machinery/limbgrower | 肢体培育机 | code\game\machinery\limbgrower.dm | 解锁非法肢体生产：可培育合成臂刃(armblade)等被黑的 RND_CATEGORY_HACKED 设计 |
| /obj/machinery/medical_kiosk | 医疗自助终端 | code\game\machinery\medical_kiosk.dm | pandemonium=TRUE：TGUI 显示乱码/错误信息；每次使用额外收取 10-30 信用点随机附加费；主板电路同样被标记 EMAGGED |
| /obj/machinery/photobooth | 拍照亭（安保证件照亭） | code\game\machinery\photobooth.dm | 清空访问限制；拍照流程变为 5 连拍、每次强闪光(flash_act 5)+被拍者尖叫，快速完成安保照片记录更新 |
| /obj/machinery/power/portagrav | 便携重力发生器 | code\game\machinery\portagrav.dm | 解锁危险重力：重力强度上限提高 +2（可调至超过 GRAVITY_DAMAGE_THRESHOLD-1 的致死等级，正常无法达到） |
| /obj/machinery/recycler | 回收机 | code\game\machinery\recycler.dm | 解除安全模式(safety_mode=FALSE)：不再拒绝活体，可直接碾碎活着的人回收成材料，并更新外观 |
| /obj/machinery/scanner_gate | 安检扫描门 | code\game\machinery\scanner_gate.dm | ID 检查器禁用：locked=FALSE 解锁面板，req_access 清空，任何人可调节扫描设置/拆解 |
| /obj/machinery/shieldgen | 护盾发生器 | code\game\machinery\shieldgen.dm | 访问控制器短路：locked=FALSE，任何人无需权限即可开关/调节护盾 |
| /obj/machinery/power/shieldwallgen | 护盾墙发生器 | code\game\machinery\shieldgen.dm | 访问控制器短路：locked=FALSE，任何人无需权限即可开关/调节护盾墙 |
| /obj/machinery/sleeper | 睡眠舱（医疗治疗舱） | code\game\machinery\sleepers.dm | 药剂按钮随机乱序(chem_buttons 打乱)：按"肾上腺素"实际注入的是随机其他药剂（吗啡/莱布他林等，无重复），且校验走原药剂逻辑——可能注入有害药害死患者；每次注入 5% 概率弹警告 |
| /obj/machinery/computer/slot_machine | 老虎机 | code\game\machinery\slotmachine.dm | 机器被做手脚：吐币改为铁币(iron coin)而非银币，且付款直接朝 2 格范围内随机活人脸上抛掷砸人 |
| /obj/machinery/wall_healer | 墙壁治疗仪 | code\game\machinery\wall_healer.dm | 安全装置禁用：不再治疗，每次注入周期造成 33 点钝性/锐器伤害、溅血、耗能 500J；取绷带 99% 概率被拒失败；主板同步 EMAGGED |
| /obj/machinery/big_manipulator | 大型机械臂 | code\game\machinery\big_manipulator\big_manipulator.dm | 过载：所有现存及新建的货运任务 type_filters 加入 /mob/living，机械臂可抓取活人作为货物搬运 |
| /obj/machinery/computer/apc_control | 电力流控制台（APC 远程管控台） | code\game\machinery\computer\apc_control.dm | 访问控制器短路：无需 ID 卡即可登录（显示 Unknown），获得全站 APC 远程开关/断路器/子系统控制权限，操作写入日志 |
| /obj/machinery/computer/communications/syndicate | 辛迪加通讯台 | code\game\machinery\computer\communications.dm | 直接 return FALSE：完全免疫 emag（无法骇入） |
| /obj/machinery/computer/communications | 通讯台 | code\game\machinery\computer\communications.dm | 普通 emag：路由电路乱序，已登录时授予全站访问权限(authorize_access=全部区域)，之后登录无需 ID 即以 Unknown 身份获得全站权限（可发动核弹/改警报等）；若用战列巡洋舰卡：消耗充能并在 20 秒~1 分钟后召唤辛迪加战列巡洋舰，全员高优先级公告（仅限叛徒，非叛徒被拒） |
| /obj/machinery/computer/upload | AI 法律上传台 | code\game\machinery\computer\law.dm | 去除访问限制：电路板 EMAGGED、解锁(locked=FALSE)、req_access/req_one_access 置空，任何人可上传 AI/机器人的法律法规 |
| /obj/machinery/computer/arcade/battle | 战斗街机 | code\game\machinery\computer\arcade\battle.dm | 硬核模式：敌人更强、19 秒炸弹倒计时时间限制（归零即死）；在街机中战败玩家直接碎尸(gib)，背景音乐换为伦巴舞曲 |
| /obj/machinery/computer/arcade/orion_trail | 猎户座之旅街机 | code\game\machinery\computer\arcade\orion.dm | 写实模式(Realism Mode)：游戏事件真实化（如撞恒星玩家真被点燃 fire_stacks=5）；游戏失败玩家真的死亡(death)，死亡后机器自动恢复；通关可获得迷你炸弹奖励 |
| /obj/machinery/door/airlock | 气闸门 | code\game\machinery\doors\airlock.dm | 0.6 秒后破坏式永久锁死：门永久锁定(locked=TRUE)、失去主电源(loseMainPower)、反馈失效(feedback=FALSE)，卡死无法再操作（使用 doorjack 门卡则消耗其充能） |
| /obj/machinery/door/firedoor | 防火门 | code\game\machinery\doors\firedoor.dm | 直接异步强制打开(open)，并永久 EMAGGED：之后无法再关闭/锁回，常开形同虚设 |
| /obj/machinery/door/window | 玻璃风门（Windoor） | code\game\machinery\doors\windowdoor.dm | 0.6 秒后破碎销毁：火花动画后风门永久损坏报废，无法再操作 |
| /obj/machinery/door_buttons | 门禁按钮/门禁控制台 | code\game\machinery\embedded_controller\access_controller.dm | 访问控制器短路：req_access/req_one_access 清空，任何人可按按钮开启门 |
| /obj/machinery/porta_turret | 便携哨戒炮塔 | code\game\machinery\porta_turret\portable_turret.dm | 威胁评估短路：此后对所有人一律返回威胁值 10——敌我不分、见任何人就开火；controllock=TRUE 永久锁控制权；关闭 6 秒后自动重启继续行凶 |
| /obj/machinery/porta_turret_cover | 哨戒炮塔伪装盖板 | code\game\machinery\porta_turret\portable_turret_cover.dm | 作用于父炮塔：使其 EMAGGED（敌我不分）、立即关闭，4 秒后自动重新开启继续攻击所有人 |
| /obj/machinery/turretid | 炮塔控制器 | code\game\machinery\porta_turret\turret_control.dm | 访问分析模块短路：locked=FALSE，任何人无需权限即可切换炮塔开关、设置区域 |
| /obj/machinery/computer/message_monitor | 信息监控台（PDA 消息监控） | code\game\machinery\telecomms\computers\message.dm | 被黑界面：屏幕切到 hacked 画面、原地生成一张监控解密密钥纸条(monitorkey，内容为乱码符)、强制关闭所连接的通讯服务器(toggled=FALSE)、显示冒充错误的乱码；持续 100×解密密钥长度 刻后自动恢复 |
| /obj/item/vape | 电子烟 | code\game\objects\items\cigarettes.dm | 须先拧开盖子才能 emag：电压最大化(super 强制关)、烟雾扩大为 4 格菱形大范围化学烟云；每吸一口 5% 概率爆炸自毁（20 灼伤+麻痹 300 刻/30 秒），图标变 vapeopen_high |
| /obj/item/card/emag | 加密序列器（emag 本体） | code\game\objects\items\emags.dm | 把另一张 emag 卡磁吸贴到自己身上：两张卡黏连成一体，名字变为"cryptographic sequencer-[卡名]"（最多叠 128 层防过载），2 秒后响雷声并警告用户——纯娱乐/自嘲行为 |
| /obj/item/toy/nuke | 核弹玩具 | code\game\objects\items\toys.dm | 启用爆炸模拟：激活后播放 14 秒核弹警报声，随后真实爆炸(light_impact_range=1)并自毁；冷却 60 秒 |
| /obj/item/toy/minimeteor | 迷你陨石玩具 | code\game\objects\items\toys.dm | 内部电子短路：扔出撞击时真实爆炸(light_impact_range=1)，与核弹玩具类似 |
| /obj/item/toy/intento | Intento 记忆力玩具 | code\game\objects\items\toys.dm | 负反馈循环：玩游戏失败后玩具强制吸附到手(TRAIT_NODROP 无法脱离)，并按其演示序列对持有者执行真实意图——帮助(HELP)=加心情、卸除(DISARM)=击倒 2 秒、抓取(GRAB)=眩晕 2 秒、攻击(HARM)=造成真实 20-30 钝伤——以彼之道还施彼身 |
| /obj/item/circuitboard/computer/slot_machine | 老虎机电路板 | code\game\objects\items\circuitboards\computer_circuitboards.dm | 加载非法机器：build_path 改为辛迪加老虎机(syndicate 版，符号为核弹/emag/暗影灵爪爪机制等） |
| /obj/item/circuitboard/computer/rdconsole | RD 研究控制台电路板 | code\game\objects\items\circuitboards\computer_circuitboards.dm | 先磁力解锁(locked=FALSE)，再烧毁节点公告芯片：silence_announcements=FALSE，强制每个研究成果节点都在通讯频道公告（暴露全站研究进度） |
| /obj/item/circuitboard/computer/cargo | 货运控制台电路板 | code\game\objects\items\circuitboards\computer_circuitboards.dm | contraband=TRUE：解锁违禁品/特殊补给目录，可订购武器等非法商品 |
| /obj/item/circuitboard/computer/cargo/express | 快速货运（空投舱）电路板 | code\game\objects\items\circuitboards\computer_circuitboards.dm | contraband=TRUE + 修改路由协议：允许空投舱降落到地图任意位置（而非仅限港口），可定点投送补给/武器 |
| /obj/item/circuitboard/machine/wall_healer | 墙壁治疗仪电路板 | code\game\objects\items\circuitboards\machines\machine_circuitboards.dm | 解除安全装置：焊入机器的电路板即带 EMAGGED 标记，安装后治疗仪从治疗变为伤害（同机器本体效果） |
| /obj/item/lightreplacer | 换灯器 | code\game\objects\items\devices\lightreplacer.dm | 灯管内填充等离子：之后替换的每个灯泡都变成等离子陷阱，通电会缓慢加热点燃爆炸；名字变为"shortcircuited light replacer"、图标变化（显眼暴露！） |
| /obj/item/lightreplacer/blue | 蓝色换灯器 | code\game\objects\items\devices\lightreplacer.dm | 直接 return FALSE：不可 emag（平衡性设计，防止远距离连环爆炸） |
| /obj/item/megaphone | 扩音器 | code\game\objects\items\devices\megaphone.dm | 语音合成器过载：voicespan 改为 REALLYBIG+userdanger——播报变为全屏超大红色警告级文本；TTS 滤镜转为失真刺耳噪声(acrusher/echo/emi 等) |
| /obj/item/radio/intercom | 对讲机 | code\game\objects\items\devices\radio\intercom.dm | 可破坏频率锁/密钥锁：freqlock 置为 UNLOCKED、keylock 置为 UNLOCKED（取决于存在哪种锁），破锁后冒火花；无锁则提示"no locks to break" |
|---|---|---|---|
| /obj/item/rsf/cookiesynth | 曲奇合成器（Cookie Synthesizer） | code/game/objects/items/rcd/RSF.dm | 切换"毒素模式"：试剂安全检测器被短路，下一次使用合成器时产物变为"安眠曲奇"（/obj/item/food/cookie/sleepy，吃下会睡着的中毒饼干），再刷一次 EMAG 可切回普通曲奇 |
| /obj/item/harmalarm | 声波伤害预防工具（机械体配件） | code/game/objects/items/robot/items/generic.dm | 关闭安全模式：安全状态下是"无害警告蜂鸣+使周围人困惑6秒"，EMAG 后变为强力声波攻击（soundbang_act 强级：致聋、眩晕 4 秒、口吃 20~30 秒、颤抖 20~50 秒），冷却更短，并记录攻击日志 |
| /obj/item/storage/lockbox | 锁定箱（锁盒） | code/game/objects/items/storage/lockbox.dm | 摧毁锁芯：broken=TRUE、存储永久解锁（STORAGE_NOT_LOCKED），之后无需钥匙/权限即可直接取用内部物品，且无法再上锁 |
| /obj/item/storage/lockbox/bitrunning/encrypted | 加密位运行锁盒 | code/game/objects/items/storage/lockbox.dm | EMAG 完全无效（emag_act 直接返回 FALSE）：无法用 EMAG 撬开，只能走正常解密流程 |
| /obj/item/door_remote | 门遥控器（控制魔杖） | code/game/objects/items/tools/control_wand.dm | 解锁受限功能：模式循环新增"电击门"（Shock Door）档位——可对任意门通电，电击触碰者；配合原有的开门/上栓/紧急出入/断电模式使用，图标变为紧急样式 |
| /obj/item/defibrillator | 除颤器 | code/game/objects/items/tools/medical/defib.dm | 切换安全协议（safety 取反）：关闭后紧凑型除颤器的电击板可当武器使用——无需战斗姿态即可电击目标，造成 60 耐力流失、击倒 75、抖动、抽搐（convulsing）状态；并叠加 EMAG 外观贴图 |
| /obj/structure/displaycase/forsale | 待售展示柜 | code/game/objects/structures/displaycase.dm | 重置收款账户并清空访问权限：读卡器烧毁冒烟，payments_acc=null 后购买与"打开"流程全部被拦（提示"未登记出售"），商品无法再被购买或取出，售卖功能被破坏、展示品锁死柜内 |
| /obj/structure/bodycontainer/morgue | 停尸柜 | code/game/objects/structures/morgue.dm | 警报系统过载：状态灯不再检测/显示"内有可复活尸体"（跳过 occupant_revivable 检查），强制显示"仅脑死"图标（morgue2），把可复活尸体伪装成脑死状态，防止医护人员发现 |
| /obj/item/wallframe/secure_safe | 保险柜框架（壁挂保险柜） | code/game/objects/structures/secure_safe.dm | 锁被摧毁：冒出火花，清除存储锁码（stored_lock_code=null）、储物格解锁（atom_storage.locked=STORAGE_NOT_LOCKED），无需密码即可直接取物，外观更新为解锁态 |
| /obj/structure/toiletbong | 马桶烟枪 | code/game/objects/structures/toiletbong.dm | 弄坏马桶：烟熏半径由 1 扩大到 2（冒烟覆盖面积翻倍）；一次性把管内全部物品烟熏殆尽（正常每次只处理 1 件就停止）；不再触发 5% 概率的"从管道掏出老鼠"彩蛋；EMAG 卡会掉进马桶再捞回 |
| /obj/structure/training_machine | 训练机（AURUMILL MkII） | code/game/objects/structures/training_machine.dm | 覆盖安全协议：强制丢弃原训练工具并装上"辛迪加工具箱"（syndicate toolbox，致命攻击武器），该工具箱不可拆卸/不可更换；启动后移动中无法停止（"一旦开动就停不下来"）；叠加 evil_trainer 贴图 |
| /obj/structure/closet | 储物柜（保险型） | code/game/objects/structures/crates_lockers/closets.dm | 撬开锁定：仅对 secure=TRUE 且未损坏的柜子生效——冒火花、锁被破坏（broken=TRUE）、解锁（locked=FALSE）、更新外观；普通非保险柜无效 |
| /obj/structure/closet/crate/secure/loot | 战利品箱（废弃板条箱） | code/game/objects/structures/crates_lockers/crates/abandoned_crates/abandoned_crates.dm | 若箱子仍处于锁定状态：直接引爆（boom 诱杀陷阱）——炸毁箱子并波及周围，无任何提示反馈（爆炸本身就是反馈） |
| /obj/structure/signboard/holosign | 全息招牌 | code/game/objects/structures/signboards/holosign.dm | 破坏 ID 锁并清除注册所有者（registered_owner=null）：任何人无需授权即可编辑招牌文字、且无法重新上锁；冒火花、叠加 "_emag" 图标、写入调查日志 |
| /obj/machinery/airalarm | 空气警报器（大气报警器） | code/modules/atmospherics/machinery/air_alarm/air_alarm_interact.dm | 认证传感器扰乱：冒火花，此后任何人都能绕过权限操作空气警报器界面（调整通风/气体设置） |
| /obj/machinery/quantum_server | 量子服务器（位运行） | code/modules/bitrunning/server/_parent.dm | 系统越狱：glitch_chance 由 0.2 翻倍至 0.4、threat_prob_max 由 15 翻倍至 30——虚拟域内敌人生成几率与威胁等级上限翻倍（更危险的地牢）；叠加 emag_overlay 贴图 |
| /obj/machinery/computer/cargo/express | 快速补给控制台 | code/modules/cargo/expressconsole.dm | 篡改路由协议：补给舱可降落在站内任意区域（无需信标，landingzone 随机选站内区域）；解锁违禁商品（contraband=TRUE）；所有订单享 0.72 折扣（EXPRESS_EMAG_DISCOUNT，即 7.2 折）；状态永久写入电路板 |
| /obj/machinery/computer/cargo | 补给订购控制台 | code/modules/cargo/orderconsole.dm | 调整路由与接收频谱：解锁违禁/仅限 EMAG 的特殊补给目录（contraband=TRUE，可订购 ORDER_CONTRABAND 与 ORDER_EMAG_ONLY 的物资）；状态永久写入电路板并刷新界面 |
| /obj/item/clothing/glasses/hud | HUD 眼镜（通用基类） | code/modules/clothing/glasses/hud.dm | 显示屏扰乱：描述变为"屏幕略微闪烁"（与 EMP 破坏效果相同，纯视觉）；之后该眼镜免疫后续 EMP 的显示扰乱（EMP 检查 EMAGGED 直接跳过） |
| /obj/item/clothing/mask/gas/sechailer | 安保喊话器 | code/modules/clothing/masks/hailer.dm | 语音电路烧毁（safety=FALSE）：喊话内容固定为 EMAG 专属短语（select_phrase 永远返回 EMAG_PHRASE 索引 1），不再按警戒度随机播报，且无法恢复 |
| /obj/item/clothing/suit/space | 宇航服（太空服） | code/modules/clothing/spacesuits/_spacesuits.dm | 热调节器限制解除：用螺丝刀调节恒温温度的范围由正常 20~45°C 扩展为 -20~120°C——可把宇航服加热到灼热或制冷到极寒（配合安全阀机制使用） |
| /obj/machinery/destructive_scanner | 实验性破坏性扫描仪 | code/modules/experisci/destructive_scanner.dm | 安全传感器 BIOS 禁用：允许扫描销毁舱内人类（正常检测到人类会拒绝并蜂鸣提示"Cannot scan with humans inside"），转为激进模式（aggressive=TRUE）直接夹紧销毁 |
| /obj/item/survivalcapsule/fishing | 钓鱼点求生舱 | code/modules/fishing/fishing_equipment.dm | 解除安全限制：径向菜单解锁全部"不安全"钓鱼点（正常被过滤掉）；部署时无视锚定物体与禁止物体检查（CAPSULE_IGNORE_ANCHORED_OBJECTS、CAPSULE_IGNORE_BANNED_OBJECTS），可强行展开 |
| /obj/machinery/fishing_portal_generator | 鱼类传送机 3000（钓鱼传送门发生器） | code/modules/fishing/fishing_portal_machine.dm | 加载辛迪加设定：鱼源选择菜单新增"辛迪加"鱼源（/datum/fish_source/portal/syndicate），可钓取辛迪加/违禁鱼类；冒火花提示 |
| /obj/machinery/computer/holodeck | 全息甲板控制台 | code/modules/holodeck/computer.dm | 破坏安全与防护协议：程序内武器由"耐力伤害（玩具枪）"改为"真实伤害"（nerf 反转，damtype 恢复初始值），可加载 EMAG 专属危险程序；广播"自动关机与反实体化协议已损坏"；记录日志并通告管理员 |
| /obj/machinery/computer/libraryconsole/bookmanagement | 图书馆图书管理控制台 | code/modules/library/lib_machines.dm | 解锁禁忌知识：可打印"禁忌文献"（print_forbidden_lore，需库内有禁忌内容且密度条件满足），界面显示禁忌文献入口 |
| /obj/machinery/ore_silo | 矿石筒仓 | code/modules/mining/machine_silo.dm | 静默封禁：任何人（包括 AI/机械体/无人机）无需 QM 权限即可封禁/解封使用矿石筒仓的用户；操作直接生效且无反馈提示、不经过常规权限检查（emagged 分支跳过 QM 校验） |
| /obj/item/grapple_gun | 抓钩枪 | code/modules/mining/equipment/grapple_gun.dm | 压力设置过载：解除低压限制——可在太空/低压环境发射抓钩（正常会被 lavaland_equipment_pressure_check 阻止并提示"压力不足"），实现太空机动 |
| /obj/machinery/mineral/labor_claim_console | 劳改积分申领控制台 | code/modules/mining/laborcamp/laborstacker.dm | ID 验证器短路：囚犯无需攒够劳改积分即可"回家"（UI 中 can_go_home=TRUE，随时呼叫劳改穿梭机返回空间站）；且囚犯完成目标后不再自动将其档案标记为假释（WANTED_PAROLE） |
| /mob/living/basic/bot | 机器人（通用基类） | code/modules/mob/living/basic/bots/_bots.dm | 分两次骇入：第 1 次解除面板盖锁定（BOT_COVER_LOCKED 清除，提示"盖板已解锁"）；面板螺丝打开后第 2 次——标记 BOT_COVER_EMAGGED、重新锁定盖板、关闭 AI 远程控制（BOT_MODE_REMOTE_ENABLED 清除，AI 无法再指挥）、自动开机、重置行为并调用 emag_effects（子类定制），播放"OVERRIDE DETECTED"警告 |
| /mob/living/basic/bot/secbot/ed209 | ED-209 警用机器人 | code/modules/mob/living/basic/bots/ed209/ed209.dm | 安全协议禁用：主武器由激光换装为微型火箭弹（/obj/projectile/bullet/rocket/weak）+ 火箭发射音效（minebot_rocket），发出威胁性蜂鸣；骑行组件也更换为危险版（nukie） |
| /mob/living/basic/bot/firebot | 灭火机器人 | code/modules/mob/living/basic/bots/firebot/firebot.dm | 开启"以火灭火"模式：内部灭火器药液更换为三氟化氯（/datum/reagent/clf3，液态火）、功率 3、关闭安全与精准喷水、容量无限并立即补满——喷出的是火焰而非水；发出异常蜂鸣与火花 |
| /mob/living/basic/bot/mulebot | 骡子运输机器人 | code/modules/mob/living/basic/bots/mulebot/mulebot_tool_interactions.dm | 播放 EMAG 动画（flick 骡子 EMAG 贴图）+ 火花音效（视觉表现）；实际叛变行为（锁 AI 控制、自动开机等）由父类通用 EMAG 处理 |
| /mob/living/basic/bot/secbot | 安保机器人（Beepsky） | code/modules/mob/living/basic/bots/secbot/secbot.dm | 目标判定电路短路：清除 SECBOT_DECLARE_ARRESTS（不再播报"逮捕"通告）、播放 EMAG 专用消息、更新外观；攻击一切目标的行为由通用机器人 AI 处理 |
| /mob/living/silicon/ai | 人工智能核心（AI） | code/modules/mob/living/silicon/ai/ai_defense.dm | 短路访问面板锁（emagged=TRUE）：无需机械师权限、也无需 AI 本人同意即可用撬棍打开 AI 核心面板（正常需 AI 弹窗确认或机器人权限），为后续篡改开路；弹火花提醒 AI |
| /mob/living/silicon/robot | 机械体（博格） | code/modules/mob/living/silicon/robot/robot_defense.dm | 两阶段骇入：第 1 次（盖板关闭时）摧毁盖板锁（locked=FALSE）；盖板打开后第 2 次——瘫痪并眩晕 10 秒、关闭法律更新（lawupdate=FALSE）、断开与所属 AI 的连接、重建模块，延迟后覆盖为辛迪加法律（叛变博格）；AI 壳（shell）无法被策反（伪装成重置），从属于恶意 AI 的博格会阻止外来软件执行 |
| /obj/item/mod/control | MOD 控制单元（动力装甲） | code/modules/mod/mod_control.dm | 切换 ID 锁状态（locked 取反）：锁定↔解锁——无需原授权者即可解锁/锁定整套 MOD 装甲的穿戴权限 |
| /obj/item/mod/module/dna_lock | MOD DNA 锁模块 | code/modules/mod/modules/modules_general.dm | 清除 DNA 数据（dna=null）：任何人都能激活穿戴、也能脱下该套 MOD 装甲（DNA 校验 on_mod_activation/on_mod_removal 直接放行） |
| /obj/item/modular_computer | 模块化电脑（掌上/平板） | code/modules/modular_computers/computers/item/computer.dm | 强制覆盖权限并修改固件（需先开机）：加载辛迪加系统主题（PDA_THEME_SYNDICATE/syndieOS），界面与系统风格变为辛迪加版；写入"权限被手动覆盖、固件被修改，需重装系统"的日志 |
| /obj/machinery/modular_computer | 模块化电脑（台式/主机） | code/modules/modular_computers/computers/machinery/modular_computer.dm | 转发给内置 CPU 的 emag_act：同样覆盖权限、加载辛迪加主题 syndieOS（需主机通电且 CPU 存在，否则提示"先开机"） |
| /obj/item/pai_card | pAI 卡（个人 AI 卡） | code/modules/pai/card.dm | 转发给卡内 pAI 的 handle_emag：抹除 pAI 的主人 DNA 与补充指令，改写为辛迪加控制（详见 /mob/living/silicon/pai） |
| /mob/living/silicon/pai | 个人 AI（pAI） | code/modules/pai/pai.dm | 彻底策反：emagged=TRUE、抹除原主人 DNA（master_dna="不可追踪签名"）、主人改为"辛迪加"、写入补充法律第 0 条"不得干涉辛迪加的行动"——不再听命原主人，为辛迪加服务 |
| /obj/machinery/fax | 传真机 | code/modules/paperwork/fax.dm | 迁移到辛迪加网络 2.0：可向辛迪加"破坏部门"（Sabotage Department，emag_needed=TRUE 目标）发送传真；收件/检索列表加入全部违禁（exotic）传真类型；屏幕闪现辛迪加帽 Ian 图案；需打开面板或特殊机型 |
| /obj/machinery/photocopier | 复印机 | code/modules/paperwork/photocopier.dm | 付费系统短路：复印不再收费（正常每次复印从账户扣 5 信用点，attempt_charge 被跳过），冒火花提示 |
| /obj/machinery/ticket_machine | 取号机 | code/modules/paperwork/ticketmachine.dm | 官僚噩梦模式：当前号码随机重置为 0~100 之间的随机数、已发出的所有排队票全部销毁（"票已消散"）；此后取出的票是"燃烧的票"（着火），彻底扰乱排队秩序 |
| /obj/machinery/power/port_gen/pacman | PACMAN 型便携发电机 | code/modules/power/port_gen.dm | 解锁最大功率输出：功率档位上限由 4 解除（power_output 可继续调高）；代价是自身立即承受重度 EMP 冲击（EMP_HEAVY，短暂宕机/断电） |
| /obj/machinery/power/apc | APC 电力控制终端 | code/modules/power/apc/apc_tool_act.dm | 解锁界面：locked=FALSE——无需 ID 卡权限即可操作 APC（切换电源模式等）；界面图标闪烁显示被黑样式（flicker_hacked_icon）；需盖板与面板均关闭且未损坏；可用螺丝刀解除 EMAG 状态 |
| /obj/machinery/power/emitter | 能量发射器 | code/modules/power/singularity/emitter.dm | ID 锁短路：locked=FALSE——任何人都能开关能量发射器、切换手动/自动射击模式（正常需 ID 权限），冒火花提示 |
| # | 设备类型路径 | 中文名 | 源码文件 | EMAG 骇入效果 |
|---|---|---|---|---|
| 1 | `/obj/item/firing_pin` | 枪械撞针（授权栓） | `code/modules/projectiles/pins.dm` | 认证检查被覆盖：置 EMAGGED 标志后，任何装入该撞针的枪械开火判定 `pin.pin_auth(user) \|\| (pin.obj_flags & EMAGGED)`（gun.dm:466）直接通过——DNA 锁、植入物认证、付费墙、阵营限制等全部失效，任何人都能开火 |
| 2 | `/obj/item/gun/ballistic/automatic/battle_rifle` | NT BR-38 战斗步枪 | `code/modules/projectiles/guns/ballistic/automatic.dm` | 散热系统停用：`projectile_damage_multiplier = emagged_projectile_damage_multiplier`（=1.6），弹丸伤害提升至 1.6 倍，以枪械过热为代价换输出 |
| 3 | `/obj/item/dragnet_beacon` | DRAGnet 传送锁定信标 | `code/modules/projectiles/projectile/energy/net_snare.dm` | 信标解锁：`locked = FALSE` 且 `set_anchored(FALSE)` 解除锚定，原本锁死部署的信标可被移动/拾取，同时冒出 3 处火花 |
| 4 | `/obj/machinery/chem_dispenser` | 化学药剂配给器（化学合成机） | `code/modules/reagents/chemistry/machinery/chem_dispenser.dm` | 安全装置短路：`dispensable_reagents \|= emagged_reagents`，可配给列表加入禁用试剂（Nova 版默认：太空毒品、等离子体毒素、冰油、鲤鱼毒素、组胺、吗啡），可光明正大合成违禁品 |
| 5 | `/obj/structure/disposaloutlet` | 垃圾管道出口 | `code/modules/recycling/disposal/outlet.dm` | 弹射力度最大化：`eject_speed = EJECT_SPEED_YEET`（=6，普通为1）、`eject_range = EJECT_RANGE_YEET`（=10 格），管道内所有物品/垃圾被 6 倍速度抛飞到 10 格外 |
| 6 | `/obj/machinery/computer/rdconsole` | 研发（R&D）控制台 | `code/modules/research/rdconsole.dm` | 安全协议禁用：电路板 `locked = FALSE`（解除锁定，无需权限即可操作研发/解锁科技）、`silence_announcements = TRUE`（研发成果不再向全站广播公告），伴随火花音效 |
| 7 | `/obj/machinery/computer/rdservercontrol` | 研究服务器控制台 | `code/modules/research/server_control.dm` | 控制台被骇：置 EMAGGED 后权限检查 `!(obj_flags & EMAGGED)` 短路（server_control.dm:70）——无 ID 权限者也能操作研究服务器（开关服务器、调研究点等） |
| 8 | `/obj/machinery/research/anomaly_refinery` | 异常精炼机（压缩测试机） | `code/modules/research/anomaly/anomaly_refinery.dm` | 固件非法访问（提示 ERROR: Unauthorized firmware access）：启动压缩测试时不再正常压缩，2 秒后触发 error_test——直接打开转移阀并强制弹出一枚**已武装的 TTV 炸弹**（toggle_valve 已开启、引信激活），每次测试都吐活炸弹，日志记录给管理员 |
| 9 | `/obj/machinery/vatgrower` | 生物培养缸（基因缸） | `code/modules/research/xenobiology/vatgrowing/vatgrower.dm` | 重采样电路过载：点击面板的开关重采样器操作（attack_hand）直接失效——重采样器状态被锁死无法切换（样本不再循环重置而是被消耗），外观闪烁 `growing_vat_emagged` 警告图标 |
| 10 | `/obj/machinery/vatgrower/small` | 小型培养缸 | `code/modules/research/xenobiology/vatgrowing/vatgrower.dm` | 无效果：emag_act 直接 `return`，无法骇入 |
| 11 | `/obj/machinery/power/emitter/energycannon/magical` | 魔法能量炮（wabbajack 能量炮） | `code/modules/shuttle/misc/special.dm` | 无法骇入：`return FALSE`（特殊穿梭机设施，EMAG 免疫） |
| 12 | `/obj/machinery/scanner_gate/luxury_shuttle` | 豪华穿梭机检票扫描门 | `code/modules/shuttle/misc/special.dm` | 无法骇入：`return FALSE`（豪华穿梭机专属检票门，EMAG 免疫） |
| 13 | `/obj/machinery/computer/shuttle/ferry` | 运输摆渡穿梭机控制台 | `code/modules/shuttle/mobile_port/variants/ferry.dm` | 默认拒绝骇入：`allow_emag = FALSE` 时提示"防火墙太强！"并返回 FALSE；全站未发现任何 allow_emag=TRUE 的摆渡控制台，即所有摆渡控制台都无法被骇 |
| 14 | `/obj/machinery/computer/emergency_shuttle` | 紧急穿梭机控制台（逃生舰桥） | `code/modules/shuttle/mobile_port/variants/emergency/emergency_console.dm` | 仅当穿梭机已停靠（IS_DOCKED）且引擎未启动时生效：发射冲击力改为 `KNOCKDOWN=60 / THROW=20`（起飞瞬间全员击倒甩飞，安全带也救不了）；伪造 10 张随机姓名+随机职业的授权 ID 卡加入授权名单；立即调用 process() 触发提前发射（约 10 秒后发射）；向管理员和穿梭机日志记录骇入者与剩余时间 |
| 15 | `/obj/machinery/computer/shuttle/pod` | 逃生舱控制台 | `code/modules/shuttle/mobile_port/variants/emergency/pods.dm` | 警戒等级检查禁用：`locked = FALSE` 且 check_lock 收到 EMAGGED 后直接返回——不再需要红色警戒（SEC_LEVEL_RED）即可发射逃生舱，屏幕图标变为 `emagged_general` |
| 16 | `/obj/machinery/computer/shuttle` | 穿梭机控制台（通用） | `code/modules/shuttle/shuttle_consoles/shuttle_console.dm` | ID 检查系统烧毁：`req_access = list()` 清空访问要求——任何人无需任何 ID/权限即可操作穿梭机控制台（选目的地、呼叫等） |
| 17 | `/obj/machinery/computer/bsa_control` | BSA 轨道炮控制台（蓝图加速器） | `code/modules/station_goals/bsa.dm` | 改装成"会爆炸"：聚焦水晶短路；BSA 开火时 `get_impact_turf()` 在 EMAGGED 下直接返回控制台自身所在格——炮击落点变成控制台自己，蓄意自杀式打击 |
| 18 | `/obj/machinery/satellite/meteor_shield` | 陨石护盾卫星 | `code/modules/station_goals/meteor_shield.dm` | 进入调试模式：附加"损坏的陨石护盾吸引信号"GPS 组件（全站 GPS 可追踪）；活跃卫星时流星事件权重 ×2（change_meteor_chance(2)）；累计骇入 3/6/7 颗逐级警告，**第 10 颗触发黑暗陨石（dark matt-eor）事件**；每次骇入共享冷却 1 分钟，关闭卫星可降回权重 ×0.5 |
| 19 | `/obj/item/organ/cyberimp/arm/toolkit/paperwork` | 文书工具箱义肢（手臂植入-文书工具包） | `code/modules/surgery/organs/internal/cyberimp/augments_arms.dm` | 解锁实验性印章：物品列表加入变色龙印章（`/obj/item/stamp/chameleon`），可伪造任意签名盖章（已有则拒绝重复） |
| 20 | `/obj/item/organ/cyberimp/arm/toolkit/toolset` | 工程工具箱义肢（手臂植入-工具包） | `code/modules/surgery/organs/internal/cyberimp/augments_arms.dm` | 解锁内置刀：物品列表加入机器人战斗刀（`/obj/item/knife/combat/cyborg`），义肢可弹出战斗刀（已有则拒绝重复） |
| 21 | `/obj/item/organ/eyes/robotic/tacvisor` | 战术 EFF 面甲（战术目镜植入体） | `code/modules/surgery/organs/internal/eyes/eyes_augments.dm` | 感知扫描器被篡改：IFF 敌我识别覆盖范围从碳基生物扩展到**所有活物**（create_illusions/on_hear/on_examine 均对非人形活物生效），且即使未设置威胁标志（threat_flags=0）也能标记目标 |
| 22 | `/obj/item/assembly/control/elevator` | 电梯呼叫按钮组件（升降机控制器） | `code/modules/transport/elevator/elev_controller.dm` | 安全装置覆盖（safeties overridden）：轿厢启用暴力着陆（violent_landing=TRUE）、下行不再预警（warns_on_down_movement=FALSE）、垂直速度减半（×0.5）；关联的所有电梯门解锁并强制打开（BYPASS_DOOR_CHECKS）且标记 EMAGGED |
| 23 | `/obj/machinery/elevator_control_panel` | 电梯控制面板 | `code/modules/transport/elevator/elev_panel.dm` | 同 22：轿厢暴力着陆+下行不预警+速度减半，关联电梯门全部解锁并强制打开，伴随火花音效；用多功能工具可修复 |
| 24 | `/obj/machinery/transport/tram_controller` | 电车（有轨电车）控制器 | `code/modules/transport/tram/tram_controller.dm` | 访问控制器短路：`cover_locked = FALSE`——外壳盖板锁解除，无需刷卡即可打开维护面板访问内部线路 |
| 25 | `/obj/machinery/transport/crossing_signal` | 电车平交路口信号机 | `code/modules/transport/tram/tram_signals.dm` | 运动传感器禁用：`operating_status = TRANSPORT_LOCAL_FAULT`——信号机进入本地故障状态（指示灯变蓝），无法正常控制电车通行/警示 |
| 26 | `/obj/structure/tram/spoiler` | 电车扰流板 | `code/modules/transport/tram/tram_structures.dm` | 伺服电机过热短路：扰流板**强制展开并锁死**（set_spoiler 中 EMAGGED 时永远 deploy，无法收放），冒出 5 处火花；可用多功能工具修复（multitool_act 清除 EMAGGED） |
| 27 | `/obj/vehicle/ridden/wheelchair/motorized` | 电动轮椅 | `code/modules/vehicles/motorized_wheelchair.dm` | 内置炸弹（需先打开面板才能骇）："炸弹出现了"——摇铃（COMSIG_WHEELCHAIR_BELL_RANG）或撞击墙体/活物（Bump）时 detonate_bomb 爆炸：重伤范围 1 格、轻伤 3 格、闪光 2 格 |
| 28 | `/obj/vehicle/sealed/car/clowncar` | 小丑车 | `code/modules/vehicles/cars/clowncar.dm` | 欢乐模式（fun mode）：儿童安全锁被破坏，面板出现 6 个彩色按钮——解锁"掷骰子"动作（roll_the_dice，随机效果，有冷却）和"小丑加农炮"动作（toggle_cannon，蓄力 2 秒发射被绑架的随机路人）；整车获得摇摆（TRAIT_WADDLING）特质 |
| 29 | `/obj/machinery/vending` | 自动售货机 | `code/modules/vending/vendor/_vending.dm` | 产品锁禁用：置 EMAGGED 后访问检查 `allowed(user) \|\| (obj_flags & EMAGGED)` 短路（interaction.dm:49）——绕过 req_access，无权限者也能购买/取出锁定的高级商品 |
| 30 | `///from base of atom`（非钩子） | （信号宏定义文件，非设备） | `code/__DEFINES/dcs/signals/signals_atom/signals_atom_x_act.dm` | 非 emag 钩子：仅包含 COMSIG_ATOM_EMAG_ACT 等信号宏定义（`#define COMSIG_ATOM_EMAG_ACT "atom_emag_act"`），是提取器误入的定义文件，无骇入效果 |
| 31 | `/obj/machinery/computer/shuttle/pod/advanced` | 高级逃生舱控制台（Nova 模块） | `modular_nova/modules/advanced_shuttles/code/computer.dm` | 同 15：烧毁警戒等级检查系统，`locked = FALSE`——无需红色警戒即可发射逃生舱 |
| 32 | `/obj/machinery/door/airlock/keyed` | 钥匙气闸门（Nova 美学模块） | `modular_nova/modules/aesthetics/keyed_doors/code/keyed_door.dm` | 无效果：emag_act 直接 `return`，无法骇入（钥匙门只能用对应钥匙开） |
| 33 | `/obj/machinery/button/door/indestructible/ancient_milsim` | 古代军事模拟战场按钮（不可毁，bitrunning 虚拟域） | `modular_nova/modules/bitrunning/code/virtual_domains/ancient_milsim/fog_of_war.dm` | 无效果：`return`，无法骇入（战争迷雾控制按钮免疫 EMAG） |
| 34 | `/obj/machinery/bluespace_miner` | 蓝空间采矿机 | `modular_nova/modules/bluespace_miner/code/bluespace_miner.dm` | 矿石表加入香蕉矿：`ore_chance += list(/obj/item/stack/sheet/mineral/bananium = 1)`——采矿机有几率产出稀有的香蕉矿（bananium），旁观者看到机器"滋滋作响" |
| 35 | `/obj/machinery/computer/cargo/express/ghost` | 幽灵快递控制台（Nova 货运模块） | `modular_nova/modules/cargo/code/expressconsole.dm` | **永远无法骇入**：尝试修改路由协议会显示运行时错误，`return FALSE`（源码注释明确 "never let this console be emagged"） |
| 36 | `/obj/machinery/button/delam_scram` | 反应堆失控紧急停止按钮 | `modular_nova/modules/delam_emergency_stop/code/scram.dm` | 无效果：`return`，无法骇入（防融化急停按钮免疫 EMAG） |
| 37 | `/obj/item/melee/baton/security/loaded/departmental` | 部门警棍（goofsec 部门守卫） | `modular_nova/modules/goofsec/code/department_guards.dm` | 部门锁被破坏：`emagged = TRUE`——部门专属锁失效，任何人都能自由使用该警棍（不再限制本部门成员），冒火花并播放电击音效 |
| 38 | `/obj/item/hypospray/mkii` | MK II 豪华注射器（皮下注射枪） | `modular_nova/modules/hyposprays/code/hyposprays_II.dm` | 控制电路超频切换：当前是豪华模式（注射延迟 0.5 秒、自用/喷雾 1 秒）则切为**战斗模式（注射/喷雾/自用延迟全部 0 秒，瞬时生效）**；反之切回豪华模式——即反复骇可来回切换注射速度 |
| 39 | `/obj/item/organ/cyberimp/arm/toolkit/armblade` | 手臂刀刃植入体（Nova 植入模块） | `modular_nova/modules/implants/code/augments_arms.dm` | 解锁能量臂刃：物品列表加入能量植入臂刃（`/obj/item/melee/implantarmblade/energy`），"You madman!"——义肢可弹出能量刀刃 |
| 40 | `/obj/item/organ/cyberimp/arm/toolkit/botany` | 植物学工具箱义肢（Nova 植入模块） | `modular_nova/modules/implants/code/augments_arms.dm` | 解锁豪华园艺设备：物品列表加入植入式链锯（`/obj/item/implant_mounted_chainsaw`），"time to landscape the station"——可在站内砍树/锯人 |
| 41 | `/obj/item/organ/cyberimp/arm/toolkit/janitor` | 清洁工工具箱义肢（Nova 植入模块） | `modular_nova/modules/implants/code/augments_arms.dm` | 解锁豪华清洁用品：物品列表加入辛迪加肥皂（`/obj/item/soap/syndie`，强力滑倒）+ 机器人润滑油喷雾（`/obj/item/reagent_containers/spray/cyborg_lube`，地面打滑） |
| 42 | `/obj/item/organ/cyberimp/arm/toolkit/lighter` | 打火机植入体（Nova 植入模块） | `modular_nova/modules/implants/code/augments_arms.dm` | 解锁内置 Zippo 打火机（`/obj/item/lighter`），"终于可以优雅地抽烟了" |
| 43 | `/obj/machinery/button/door/indestructible/blackmarket_trader` | 黑市商人门按钮（不可毁，ghostrole 屏蔽） | `modular_nova/modules/mapping/code/ghostrole_shields.dm` | 无效果：`return`，无法骇入（黑市商人区域防护按钮免疫 EMAG） |
| 44 | `/obj/item/pizzavoucher` | 披萨券 | `modular_nova/modules/pizza_voucher/code/pizza_voucher.dm` | 配送协议被覆盖："mamma mia!"——下一次使用券订披萨时，蓝空间裂隙吐出的不是披萨盒而是**已武装的炸弹披萨盒**（`/obj/item/pizzabox/bomb/armed`），券本身随之化为灰烬 |
| 45 | `/obj/item/mod/control/pre_equipped/protean` | Protean MOD 套装控制器（变形者套装） | `modular_nova/modules/protean/code/modsuit/protean_modsuit.dm` | 无响应：MOD 套装拒绝被骇——提示"控制器对 EMAG 卡没有反应"，`return FALSE`（Protean 套装固件免疫） |
| 46 | `/obj/machinery/power/rbmk2` | RBMK-2 核反应堆（切尔诺贝利式） | `modular_nova/modules/RBMK2/code/reactor_acts.dm` | 超频模式（overdrive engaged）：`safety = FALSE` 关闭全部安全系统——反应堆可无保护超速运行、堆芯失控风险大增；向管理员发送通知、写入游戏日志与工程调查日志 |
| 47 | `/obj/item/gun/ballistic/shotgun/dex4` | DEX-4 智能霰弹枪（电磁加速，z121 模组） | `modular_z121/code/modules/weapon_addtion/weapon_addtion.dm` | 电磁抑制系统停用：`projectile_damage_multiplier = 2`、`projectile_speed_multiplier = 2`——弹丸伤害与飞行速度全部翻倍（2 倍），穿透力大增 |

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

1. **开局先买 EMAG**（3 TC）——性价比最高的万能工具
2. **开门用门磁卡**（另购），EMAG 留给机器——EMAG 对气闸是黑名单，别浪费
3. **蓝空间 EMAG 值钱**：远程骇入可以隔墙/隔门操作（prox_check = FALSE）
4. **骇入有动静**：动作可见——别在人多处动手；语音播报可能触发警报
5. **假 EMAG 是陷阱**：如果目标是 EMAG 且你"成功"了，检查是不是假卡——真的会被引爆
6. **战列巡洋舰卡**：对通信台用一次就是一场灾难（舰队来袭）
7. **骇入前确认设备**：149 处钩子效果各异——贩卖机是免费用、APC 是改供电、武器机是军火库模式，先想好要干什么

---

## 六、一句话总结

**3 TC 一张卡，149 种隐藏功能** —— 机器都能"解锁"，门要用门磁卡，蓝空间版能隔空操作，假卡会炸脸。

> 源码索引：`emags.dm`（本体+变体）/ `device_tools.dm`（Uplink 4TC）/ `emag_act` 钩子全库 149 处（game/machinery 34 + game/objects 29 + NOVA 20 + modules 66）
