# TianGuan13 通信系统百科（Communications System Complete Encyclopedia）

> 基于 TianGuan13（NovaSector 分支）源码全量提取。覆盖：对话系统、电信网络（Telecomms）、无线电设备（Radio Devices）、通信控制台、全息通话、请求消息、数据 HUD 及 NOVA 追加内容。
> 文档采用中英双语格式：中文为主，英文术语/对象名随行标注。所有数值均直接提取自源码，未做推算。

## 目录 (Table of Contents)

1. [总览：通信链路如何工作](#1-总览通信链路如何工作)
2. [对话系统（Say System）](#2-对话系统say-system)
   - [2.1 说话核心流程](#21-说话核心流程)
   - [2.2 语音范围与耳语/喊话](#22-语音范围与耳语喊话)
   - [2.3 说话动词与消息修饰](#23-说话动词与消息修饰)
   - [2.4 虚拟说话者（Virtualspeaker）](#24-虚拟说话者virtualspeaker)
3. [无线电频道总表（Radio Channels）](#3-无线电频道总表radio-channels)
4. [电信网络（Telecommunications Network）](#4-电信网络telecommunications-network)
   - [4.1 信号传输方式与数据包](#41-信号传输方式与数据包)
   - [4.2 完整信号链路](#42-完整信号链路)
   - [4.3 电信机器全录（10 类基础型 + 全部预设）](#43-电信机器全录10-类基础型--全部预设)
   - [4.4 电信电脑全录（3 种）](#44-电信电脑全录3-种)
   - [4.5 机器交互与检修（Multitool 操作）](#45-机器交互与检修multitool-操作)
   - [4.6 网络被破坏的后果](#46-网络被破坏的后果)
5. [无线电设备全录（Radio Devices）](#5-无线电设备全录radio-devices)
   - [5.1 手持电台 /obj/item/radio 全参数](#51-手持电台-objitemradio-全参数)
   - [5.2 头戴式耳机全录（Headset）](#52-头戴式耳机全录headset)
   - [5.3 对讲机全录（Intercom）](#53-对讲机全录intercom)
   - [5.4 加密钥匙全录（Encryption Keys）](#54-加密钥匙全录encryption-keys)
   - [5.5 其他无线电设备](#55-其他无线电设备)
6. [通信控制台（Communications Console）](#6-通信控制台communications-console)
7. [全息通话（Holocalls）](#7-全息通话holocalls)
8. [请求消息（Request Messages）](#8-请求消息request-messages)
9. [数据 HUD（Data HUDs）](#9-数据-huddata-huds)
10. [NOVA 追加内容](#10-nova-追加内容)
11. [源码路径索引](#11-源码路径索引)

---

## 1. 总览：通信链路如何工作

TianGuan13 的通信系统分为三大层次：

1. **对话系统（Say System）**：`code/game/say.dm` 定义了一切"说话"的底层机制——消息清洗（sanitize）、范围传播（`send_speech`）、语言翻译（`translate_language`）、消息修饰（强调/歌唱/耳语）、虚拟说话者（virtualspeaker）。
2. **电信网络（Telecomms）**：`code/game/machinery/telecomms/` 是一整套由 **接收器（Receiver）→ 总线（Bus）→ 处理器（Processor）→ 服务器（Server）→ 广播器（Broadcaster）** 构成的亚空间（subspace）信号处理管线，外加**中枢（Hub）**与**中继（Relay）**负责跨 Z 层转发，**消息服务器（Message Server）**负责 PDA 与请求台消息。**这套网络被破坏 = 全站无线电瘫痪**（头戴式耳机仅能走亚空间，而亚空间必须经过接收器）。
3. **无线电设备（Radio Devices）**：`code/game/objects/items/devices/radio/` 是玩家手中的一切终端——手持对讲机（station bounced radio）、头戴式耳机（headset）、墙上对讲机（intercom）、加密钥匙（encryption key）、电击背包（electropack）。

关键数值速查：

| 项目 | 数值 | 出处 |
|---|---|---|
| 普通说话范围（默认 message_range） | 7 格 | `say.dm` |
| 耳语范围 | 1 格 | `living_say.dm` |
| 单条消息最大长度 MAX_MESSAGE_LEN | 2048 字符（NOVA 从 1024 翻倍） | `__DEFINES/say.dm` |
| 无线电广播截断长度 MAX_BROADCAST_LEN | 512 字符 | `__DEFINES/say.dm` |
| 公共可用频率段（自由频率） | 1441–1489（MIN_FREQ–MAX_FREQ） | `__DEFINES/radio.dm` |
| 特殊电台全频段（freerange） | 1201–1599（MIN_FREE_FREQ–MAX_FREE_FREQ） | `__DEFINES/radio.dm` |
| 电信机器自动链接距离 | 卡迪纳距离 ≤ 20 或同 Z 层 | `telecomunications.dm` |
| 中继/中枢跨 Z 层能力 | long_range_link = TRUE | `relay.dm` / `hub.dm` |
| 服务器日志上限 MAX_LOG_ENTRIES | 400 条 | `server.dm` |
| 消息服务器解密钥匙（默认/预设） | "password" / 随机词组合 | `message_server.dm` |
| 电台 EMP 失效时长 | 20 秒 | `radio.dm` |

---

## 2. 对话系统（Say System）

源码：`code/game/say.dm`（425 行）+ `code/modules/mob/living/living_say.dm`

### 2.1 说话核心流程

`/atom/movable/proc/say()`（`say.dm` L50）是所有说话的入口，参数与默认值：

- `message` — 消息本体
- `sanitize = TRUE` — 是否清洗消息；清洗后截断至 `MAX_MESSAGE_LEN`（2048 字符）
- `message_range = 7` — **默认传播范围 7 格**
- `language` — 说话语言，默认取 `get_selected_language()`
- `ignore_spam = FALSE`、`forced`（强制来源，非布尔）、`filterproof = FALSE`
- `saymode` — 特殊说模式（外星频道等）
- `message_mods` — 消息修饰符列表（耳语/歌唱等）

流程：`try_speak()`（过滤 + 防刷屏检查）→ 清洗/截断 → 附加 `speech_span` → 确定语言 → 确定说话动词 `say_mod()` → **`send_speech()`**。

`send_speech()`（L138）：`get_hearers_in_view(range, source)` 获取范围内所有能听到的 `atom/movable`，逐个调用 `Hear()`；同时通过 `do_tts_message()` 排队 TTS 语音合成（若 `SStts.tts_enabled` 开启且说话者有 voice）。

`can_speak()`（L116）：核心判定是 `!HAS_TRAIT(src, TRAIT_MUTE)`——**哑巴特质（TRAIT_MUTE）会禁止一切说话**。

### 2.2 语音范围与耳语/喊话

- **普通说话**：范围 7 格（`message_range = 7`）。
- **耳语（Whisper）**：`living_say.dm` L184 将 `message_range` 强制设为 **1 格**；耳语消息附加斜体 span（`SPAN_ITALICS`）。重伤硬濒死（HARD_CRIT）时耳语会被截断成遗言（`-..`）。
- **软声特质（TRAIT_SOFTSPOKEN）**：自动降级为耳语（除非使用手语）。
- **喊话（Yell）**：不以范围扩大实现，而是通过 `say_mod()` 将动词改为喊（verb_yell）并附加 `SPAN_YELL`——判定条件是句尾为 `!!` 或全角 `！！`（NOVA I18N 修改，L209）。
- **歌唱（Sing）**：`MODE_SING` 时消息两端加音符 `♩ ♪ ♫`，附加 `SPAN_SINGING`。
- **机器人/远端说话**：`Hear()` 返回 `HEAR_HEARD | HEAR_UNDERSTOOD`；翻译逻辑见 `translate_language()`——听不懂的语言会被 `scramble_paragraph()` 打乱（部分理解语言按理解度混合）。

### 2.3 说话动词与消息修饰

`say_mod()`（L207）按结尾字符决定动词：

| 条件 | 动词 |
|---|---|
| 句尾 `!!` 或 `！！` | 喊（verb_yell） |
| `MODE_SING` | 唱（verb_sing） |
| `WHISPER_MODE` | 耳语（verb_whisper） |
| 句尾 `?` 或 `？` | 问（verb_ask） |
| 句尾 `!` 或 `！` | 惊叹（verb_exclaim） |
| 其他 | 默认动词（verb_say） |

**消息强调语法**（`apply_message_emphasis`，L274）：`|文字|` 斜体、`+文字+` 粗体、`_文字_` 下划线、`^文字^` 小号字；反斜杠可转义（`\|`）。

**频道显示**：`compose_message()`（L165）负责渲染聊天行——频道前缀 `[频道名]`、说话者名字、语言图标（`display_icon_type`）、职位后缀。`get_radio_name()` 将频率格式化为 `145.9` 样式；`get_radio_span()` 查 `freqtospan` 表决定颜色 class；`get_radio_color()` 返回频道颜色。

### 2.4 虚拟说话者（Virtualspeaker）

`/atom/movable/virtualspeaker`（L378）是无线电说话的"发言人"替身，用于让 AI/玩家正确显示身份：

- `name`：`radio.anonymize` 为真时显示 **"Unknown"**，否则取说话者语音名
- 继承说话者的 verb_say/verb_ask/verb_exclaim/verb_yell
- `GetJob()` 职业判定（L396-416）：
  - 人类 → 船员档案（crew record）中的职位，无档案则 "Unknown"
  - 碳基非人类 → "No ID"
  - AI → "AI"
  - 机器人（Cyborg）→ "[designation] Cyborg"
  - pAI → JOB_PERSONAL_AI
  - 物体 → "Machine"
  - 其他 → "Unknown"

---

## 3. 无线电频道总表（Radio Channels）

源码：`code/game/communications.dm`（GLOBAL_LIST_INIT default_radio_channels / reserved_radio_frequencies / reserved_radio_colors）、`code/__DEFINES/radio.dm`、`code/__DEFINES/~nova_defines/*.dm`

频道前缀输入法：在聊天框输入 `:x 消息` 即用对应频道发言；`:h` 表示部门频道（第一个加密频道），`:b` 为二进制频道。

### 3.1 部门/标准频道（默认频道表，24 个）

| 频道名 (Channel) | 前缀 Token | 频率 (MHz) | 颜色 (Color) | 说明 |
|---|---|---|---|---|
| Common 公共 | `;` | 145.9 (FREQ_COMMON 1459) | `#1ecc43` | 所有电台默认频率 |
| Science 科学 | `:n` | 135.1 (1351) | `#c68cfa` | 研发部 |
| Command 指挥 | `:c` | 135.3 (1353) | `#fcdf03` | 指挥部 |
| Medical 医疗 | `:m` | 135.5 (1355) | `#57b8f0` | 医疗部 |
| Engineering 工程 | `:e` | 135.7 (1357) | `#f37746` | 工程部 |
| Security 安保 | `:s` | 135.9 (1359) | `#dd3535` | 安全部 |
| Supply 补给 | `:u` | 134.7 (1347) | `#b88646` | 货运部 |
| Service 服务 | `:v` | 134.9 (1349) | `#6ca729` | 服务部 |
| AI Private AI 私线 | `:o` | 144.7 (1447) | `#d65d95` | AI 私密频道 |
| Entertainment 娱乐 | `:p` | 141.5 (1415) | `#79c5a8` | 娱乐广播 |
| Binary 二进制 | `:b` | —（特殊） | — | 机器语言，需 RADIO_SPECIAL_BINARY |
| Department 部门 | `:h` | —（取第一个加密频道） | — | 快捷切换 |

### 3.2 特殊/受限频道（需对应加密钥匙或特殊设备）

| 频道名 (Channel) | 前缀 Token | 频率 (MHz) | 颜色 | 访问条件 |
|---|---|---|---|---|
| Syndicate 辛迪加 | `:t` | 121.3 (1213) | `#8f4a4b` | RADIO_SPECIAL_SYNDIE（反叛者钥匙/广播站） |
| Uplink 上链 | `:z` | 121.1 (1211) | `#8f4a4b` | 仅用于 Uplink 循环，游戏内不可见 |
| CentCom 中央指挥部 | `:y` | 133.7 (1337) | `#2681a5` | RADIO_SPECIAL_CENTCOM |
| Faction 派系（NOVA） | `:f` | 133.5 (1335) | — | 派系钥匙（FREQ_FACTION） |
| Cybersun 赛博太阳（NOVA） | `:q` | 121.1 (1211) | — | Cybersun 钥匙（FREQ_CYBERSUN） |
| Interdyne 因特代恩（NOVA） | `:w` | 120.9 (1209) | — | Interdyne 钥匙（FREQ_INTERDYNE） |
| Guild 行会（NOVA） | `:d` | 121.4 (1214) | — | Guild 钥匙（FREQ_GUILD） |
| Tarkon 塔肯工业（NOVA） | `:k` | 124.3 (1243) | — | Tarkon 钥匙（FREQ_TARKON） |
| SolFed 太阳联邦（NOVA） | `:f` | 137.7 (1377) | — | SolFed 钥匙（FREQ_SOLFED） |
| Red Team 红队 | — | 121.5 (1215) | `#ff0000` | CTF 模式 |
| Blue Team 蓝队 | — | 121.7 (1217) | `#0000ff` | CTF 模式 |
| Green Team 绿队 | — | 121.9 (1219) | `#00ff00` | CTF 模式 |
| Yellow Team 黄队 | — | 122.1 (1221) | `#d1ba22` | CTF 模式 |
| Captain-Cast 舰长播报 | — | 143.5 (1435) | `#00ff99` | 状态显示屏（FREQ_STATUS_DISPLAYS） |

### 3.3 保留专用频率（非频道，供机器/装置使用）

源码：`code/__DEFINES/radio.dm` L81-122

- FREQ_FUGITIVE_HUNTER 1243 — 逃犯猎人
- FREQ_INTERROGATION 1423 — 审讯室对讲机
- FREQ_HOLOGRID_SOLUTION 1433 — 全息网格
- FREQ_PRESSURE_PLATE 1447 — 压力板（与 AI 私线同频）
- FREQ_ELECTROPACK 1449 — 电击背包（与 FREQ_MAGNETS 磁铁同频）
- FREQ_LOCATOR_IMPLANT 1451 — 定位植入体
- FREQ_RADIO_NAV_BEACON 1455 — 无线电导航信标
- FREQ_SIGNALER 1457 — 信号器默认频率（默认码 30，DEFAULT_SIGNALER_CODE）
- FREQ_CONFESSIONAL 1481 — 告解室对讲机（教堂）
- MIN_UNUSED_FREQ 1461 — 防止随机到 AI 私线或公共频

**禁止频率（banned_frequencies）**（`machine_interactions.dm` L10）：服务器/总线不可监听以下频率——FREQ_SYNDICATE、FREQ_CENTCOM、FREQ_CTF_RED、FREQ_CTF_YELLOW、FREQ_CTF_GREEN、FREQ_CTF_BLUE。

**传输方式（Transmission Methods）**（`radio.dm` L125-128）：

| 常量 | 值 | 含义 |
|---|---|---|
| TRANSMISSION_WIRE | 0 | 有线（未使用） |
| TRANSMISSION_RADIO | 1 | 常规电磁波（默认，对讲机/广播站） |
| TRANSMISSION_SUBSPACE | 2 | 亚空间（仅头戴式耳机） |
| TRANSMISSION_SUPERSPACE | 3 | 超空间（仅独立电台，如 CentCom） |

**特殊频道标志（special_channels）**：RADIO_SPECIAL_SYNDIE (1<<0)、RADIO_SPECIAL_CENTCOM (1<<1)、RADIO_SPECIAL_BINARY (1<<2)。

---

## 4. 电信网络（Telecommunications Network）

源码：`code/game/machinery/telecomms/`（共 2,550 行左右，15 个文件）

### 4.1 信号传输方式与数据包

- **亚空间信号** `/datum/signal/subspace`（`broadcasting.dm`）：`server_type` 默认指向服务器；`levels` 列表决定信号可达的 Z 层（含 0 表示全 Z 层，即 `RADIO_NO_Z_LEVEL_RESTRICTION`）；`blacklisted_spans` 含 `SPAN_SOAPBOX`（禁止肥皂箱 span 进入公共频道）。
- **语音信号** `/datum/signal/subspace/vocal`：构造时压缩率取 **35–65 随机值**（COMPRESSION_VOCAL_SIGNAL_MIN/MAX）；`broadcast()` 时若压缩 > 0 则用 `Gibberish()` 打乱消息（压缩 ≥ 30 时替换字符，COMPRESSION_REPLACE_CHARACTER_THRESHOLD）。
- **消息信号** `/datum/signal/subspace/messaging`：默认 `server_type = /obj/machinery/telecomms/message_server`，频率 FREQ_COMMON；子类型 `tablet_message`（PDA 短信）与 `rc`（请求台）。
- **广播分发**（`broadcast()`，L110）：按传输方式分派——SUBSPACE 发给所有同频电台（过滤 `can_receive`）；RADIO 只发给非亚空间模式电台；SUPERSPACE 只发给带 RADIO_SPECIAL_CENTCOM 的独立电台。**辛迪加电台可以听到所有知名频道**（L140-144：遍历 default_radio_channels，把 FREQ_SYNDICATE 上的电台加入收听者）。幽灵（observer）开启 CHAT_GHOSTRADIO 也能听到。

### 4.2 完整信号链路

```
说话者 → 电台 talk_into()
   │  （构造 vocal 信号，压缩 35-65）
   ▼
send_to_receivers() ──► 接收器 Receiver（须在信号 levels 内）
   │  （清空 levels，复制信号）
   ▼
中枢 Hub（把压缩信号转发给 Bus，copysig=TRUE）
   │
   ▼
总线 Bus（若 change_frequency 非空则改写频率）
   │
   ▼
处理器 Processor（解压：compression → 0；或压缩：→ 100）
   │  （送回 Bus）
   ▼
总线 Bus（依次尝试 server_type → Hub → Broadcaster）
   │
   ▼
服务器 Server（记录日志，最多 400 条；附加频道名/颜色）
   │  （relay_information 到 Hub，失败则直达 Broadcaster）
   ▼
广播器 Broadcaster（防刷屏去重 → broadcast()）
   │
   ▼
所有同频电台 on_receive_message() → 佩戴者 Hear()
```

**兜底机制**：非亚空间电台（对讲机/广播站）在 `talk_into()` 发送亚空间信号后，若 **2 秒内**（`backup_transmission`，radio.dm L397）信号未被处理（`signal.data["done"]` 未标记且 Z 层不符），则改用普通无线电（TRANSMISSION_RADIO）直接广播——**因此只有纯亚空间设备（头戴式耳机）会因电信网络瘫痪而完全失联**。

**信号延迟（Lag）**：`relay_information()` 中 `netlag = round(traffic / 50)` 会累加到 `signal.data["slow"]`；处理器无总线直连服务器时额外 `rand(5, 10)`；总线逐跳额外 `rand(0, 1)`；广播器/一体机按 `slow` 值 `sleep()` 模拟网络延迟。

### 4.3 电信机器全录（10 类基础型 + 全部预设）

#### 4.3.1 基类 `/obj/machinery/telecomms`（`telecomunications.dm`）

所有电信机器的公共父类。关键属性：

- `critical_machine = TRUE`（关键机器，多数破坏性事件会保护它）
- `links` / `links_by_telecomms_type` — 双向链接表（按 telecomms_type 分组）
- `traffic`（流量）/ `netspeed = 2.5`（每秒流失 50GB×netspeed 流量）
- `autolinkers` — 自动链接标识符列表；`id`（默认 "NULL"）；`network`（默认 "NULL"）
- `freq_listening` — 监听频率列表，**为空则监听全部频率**
- `on` / `toggled`；`long_range_link = FALSE`；`hide = FALSE`（隐藏机不显示在非隐藏机 UI）
- `relay_information(signal, filter, copysig, amount = 20)` — 至多转发 20 台目标机
- 自动链接规则（`post_machine_initialize`，L110）：`long_range_link` 双方均为真 **或** 卡迪纳距离 ≤ 20 **或** 同 Z 层（connected levels），且 autolinker 有交集
- 电源判定 `update_power()`：`BROKEN | NOPOWER | EMPED` 任一状态 → 关机
- EMP 处理（L161）：`prob(100/severity)` 概率进入 EMPED 状态，持续 `300 秒 / severity`（±2 秒随机）

#### 4.3.2 接收器 Subspace Receiver（`machines/receiver.dm`）

- 空闲功耗 `BASE_MACHINE_IDLE_CONSUMPTION * 0.05`；电路板 `/obj/item/circuitboard/machine/telecomms/receiver`
- 职责：接收亚空间信号（`receive_signal`），检查 `check_receive_level`（本机 Z 层在信号 levels 内，或链接的中枢下挂中继可接收），**复制信号并清空 levels**，转发给 Hub；无 Hub 则直达 Bus
- **若接收器全灭 → 头戴式耳机信号进不了网络**
- 预设：
  - `preset_left` "Receiver A"（autolinker `receiverA`）：监听 Science、Medical、Supply、Service、Entertainment
  - `preset_right` "Receiver B"（autolinker `receiverB`）：监听 Command、Engineering、Security，**且初始化时把全部自由频率 1441–1489 加入监听**
  - `preset_left/birdstation` "Receiver"：清空频率列表

#### 4.3.3 中枢 Telecommunication Hub（`machines/hub.dm`）

- `long_range_link = TRUE`，`netspeed = 40`，带循环音效（soundloop）
- **网络的心脏**：来自接收器的压缩信号 → 转发给 Bus（copysig=TRUE）；来自服务器/Bus 的解压信号 → 先转发给所有 Relay（补 Z 层），再转发给 Broadcaster
- 预设 `preset` "Hub"：autolinkers 包含全部 20 个标识符——`hub, relay, s_relay, m_relay, r_relay, science, medical, supply, service, common, command, engineering, entertainment, security, receiverA, receiverB, broadcasterA, broadcasterB, autorelay, messaging`

#### 4.3.4 中继 Relay（`machines/relay.dm`）

- `long_range_link = TRUE`，`netspeed = 5`，`broadcasting = TRUE` / `receiving = TRUE` 两个独立开关
- 职责：跨 Z 层转发。收到信号后把 `SSmapping` 中所有 `ZTRAIT_STATION` 的连通层加入 `signal.levels`
- 预设：
  - `preset/station` "Station Relay"（`s_relay`）— 站内中继
  - `preset/telecomms` "Telecomms Relay"（`relay`）— 电信卫星中继
  - `preset/mining` "Mining Relay"（`m_relay`）— 矿星中继
  - `preset/ruskie` "Ruskie Relay"（`r_relay`）— 隐藏、默认关闭（toggled = FALSE）
  - `preset/auto`（`autorelay`）— 隐藏的通用自动中继

#### 4.3.5 总线 Bus Mainframe（`machines/bus.dm`）

- `netspeed = 40`；`change_frequency = NONE`（非空且不在 banned_frequencies 时改写信号频率）
- 职责：网络枢纽节点。信号未处理过 → 先发给处理器；然后依次尝试 `signal.server_type`（服务器）→ Hub → Broadcaster，每跳增加 `rand(0,1)` 延迟
- 预设：
  - `preset_one` "Bus 1"：监听 Science、Medical；链接 `processor1, science, medical`
  - `preset_two` "Bus 2"：监听 Supply、Service、Entertainment；链接 `processor2, supply, service, entertainment`
  - `preset_three` "Bus 3"：监听 Security、Command；链接 `processor3, security, command`
  - `preset_four` "Bus 4"：监听 Engineering；链接 `processor4, engineering, common, messaging`，**初始化时加入全部自由频率**
  - `preset_one/birdstation` "Bus"：清空频率，链接 `processor1, common, messaging`

#### 4.3.6 处理器 Processor Unit（`machines/processor.dm`）

- `compressing = FALSE`（压缩模式则把信号压缩率设为 **100**；解压模式则清为 **0**）
- 职责：解压亚空间信号使其可听；处理后**直送回调用的 Bus**；无 Bus 时转给服务器并加 `rand(5, 10)` 延迟
- 预设：`preset_one` "Processor 1"（processor1）、`preset_two` "Processor 2"（processor2）、`preset_three` "Processor 3"（processor3）、`preset_four` "Processor 4"（processor4）、`preset_one/birdstation` "Processor"

#### 4.3.7 服务器 Telecommunication Server（`machines/server.dm`）

- 职责：记录全部语音流量日志（`comm_log_entry`，**上限 400 条**自动裁旧）；为信号附加频道名/颜色（frequency_infos）；记录 `total_traffic`；把信号转发给 Hub（失败则直达 Broadcaster）
- 日志条目：mobtype、name、job、message、language；压缩 > 0 时记录为 "Corrupt File" 乱码
- 预设（network 均为 "tcommsat"，name 取 id）：
  - `presets/science` "Science Server"（`science`）：监听 1351
  - `presets/medical` "Medical Server"（`medical`）：监听 1355
  - `presets/supply` "Supply Server"（`supply`）：监听 1347
  - `presets/service` "Service & Entertainment Server"（`service, entertainment`）：监听 1349、1415
  - `presets/common` "Common & AI Server"（`common`）：监听空列表 = 全部频率，**初始化时加入全部自由频率 1441–1489**，并登记 Common 与 AI Private 的频道信息
  - `presets/command` "Command Server"（`command`）：监听 1353
  - `presets/engineering` "Engineering Server"（`engineering`）：监听 1357
  - `presets/security` "Security Server"（`security`）：监听 1359
  - `presets/common/birdstation`：清空频率

#### 4.3.8 广播器 Subspace Broadcaster（`machines/broadcaster.dm`）

- 职责：把处理后的消息广播给**所有**无线电设备（不限于耳机，对讲机与广播站也算）。拒绝 `reject` 标记或无 message 的信号；通过 `GLOB.recent_messages` 去重（**1 秒重置**，`end_message_delay()`）防刷屏；广播时播放 `broadcaster_send` 动画
- 预设：`preset_left` "Broadcaster A"（`broadcasterA`）、`preset_right` "Broadcaster B"（`broadcasterB`）、`preset_left/birdstation` "Broadcaster"

#### 4.3.9 一体机 Telecommunications Mainframe（`machines/allinone.dm`）

- **无需供电**（`use_power = NO_POWER_USE`，idle_power_usage = 0），便携式亚空间处理机
- 内置全部 10 个标准频道（Science/Medical/Supply/Service/Entertainment/Common/AI Private/Command/Engineering/Security）的名称与颜色
- `syndicate = FALSE`：为真时可处理**任意 Z 层**的辛迪加通讯（`signal.levels = list(0)` 广播给所有特工）
- 收到信号 → 附加频道信息 → 解压（compression = 0）→ `mark_done()` → 按 slow 延迟 → `broadcast()`
- 变体：
  - `nuclear` "advanced telecommunications mainframe"：监听 FREQ_SYNDICATE，`syndicate = TRUE`（核弹小队用）
  - `indestructible`：`INDESTRUCTIBLE | LAVA_PROOF | FIRE_PROOF | UNACIDABLE | ACID_PROOF`，且螺丝刀/撬棍不可拆

#### 4.3.10 消息服务器 Messaging Server（`machines/message_server.dm`）

- 职责：**PDA 短信与请求台（Request Console）消息的收发核心**。PDA 需要完整电信网络；**请求台只需要消息服务器**即可工作
- `decryptkey = "password"`；`calibrating = 15 MINUTES`（开机校准期，期间不可用并播报 "Calibrating..."）
- 日志：`pda_msgs`（`/datum/data_tablet_msg`：sender/recipient/message/photo/automated）、`rc_msgs`（`/datum/data_rc_msg`：sender_department/receiving_department/message/stamp/id_auth/priority）
- 优先级定义：REQUEST_PRIORITY_NORMAL "Normal" / HIGH "High" / EXTREME "Extreme" / UNDETERMINED "Undetermined"
- 预设 `preset` "Messaging Server"（`messaging`）：`calibrating = 0`，**随机生成解密钥匙**（`词1 + 词2 + 数字`，如 "deadrosebud7"；同一 Z 层所有预设服务器共享同一钥匙，存入 `GLOB.preset_station_message_server_key`）
- 同文件附带：
  - **黑匣子记录仪 Blackbox Recorder**（`/obj/machinery/blackbox_recorder`）：装饰性 SSblackbox 代表物，内含**叛徒偷窃任务物品** `/obj/item/blackbox`（"the blackbox"，不可毁、WEIGHT_CLASS_BULKY）；护甲 melee 25 / bullet 10 / laser 10 / fire 50 / acid 70
  - 信号子类型：`/datum/signal/subspace/messaging/tablet_message`（PDA）与 `/rc`（请求台）

#### 4.3.11 电信机器清单汇总

**共 10 类基础机器**：Receiver、Hub、Relay、Bus、Processor、Server、Broadcaster、Allinone（Mainframe）、Message Server、Blackbox Recorder。
**预设机器**：Receiver A/B、Hub、Station/Telecomms/Mining/Ruskie/Auto Relay、Bus 1-4、Processor 1-4、Science/Medical/Supply/Service/Common/Command/Engineering/Security Server、Broadcaster A/B、Messaging Server、核弹一体机、不可毁一体机、Birdstation 变体（Receiver/Bus/Processor/Broadcaster/Common Server）。

### 4.4 电信电脑全录（3 种）

#### 4.4.1 电信监控台 Telecommunications Monitoring Console（`computers/telemonitor.dm`）

- `/obj/machinery/computer/telecomms/monitor`，电路板 `comm_monitor`
- 功能：按**网络名**（≤15 字符）探测（probe）网络内全部电信机器并缓存（weakref）；查看单机详情及其链接列表；`flush` 清空缓存
- 操作失败提示：网络 ID 过长 / 缓存已满 / 找不到网络实体

#### 4.4.2 消息监控台 Message Monitor Console（`computers/message.dm`）

- `/obj/machinery/computer/message_monitor`，电路板 `message_monitor`，绿灯
- 自动链接第一个消息服务器（`post_machine_initialize`）
- **解密钥匙认证**：输入 `linked_server.decryptkey` 登录（authenticated）
- 功能：查看/删除 PDA 短信日志与请求台日志；清空两类日志；`turn_server` 开关服务器；站内可改解密钥匙（旧钥匙验证 + 新钥匙 ≥ 4 字符）；**发送伪造消息**（send_fake_message，任意伪造发件人/职位，经服务器中转）
- **电磁卡（emag）**：进入 MSG_MON_SCREEN_HACKED 画面，打印"监控解密钥匙"纸条（`/obj/item/paper/monitorkey`，含 `<h2>Daily Key Reset</h2>` 模板），**强制关闭服务器** `linked_server.toggled = FALSE`，持续 `100 × 钥匙长度` 秒后自愈（unemag_console）；emag 后螺丝刀拆卸被阻止
- **故障 AI/机器人黑客（hack）**：`10 秒 × 钥匙长度` 后恢复，期间认证为真
- 钥匙纸条 `/obj/item/paper/monitorkey`：CE 记忆中配套的每日钥匙重置凭证

#### 4.4.3 服务器监控台 Server Monitoring Console（`computers/logbrowser.dm`）

- `/obj/machinery/computer/telecomms/server`，电路板 `comm_server`，**删除消息需 ACCESS_TCOMMS**
- 功能：`scan_network` 在 **25 格半径**（urange）内扫描指定网络名的服务器并缓存；查看服务器流量（total_traffic）与数据包日志；**删除数据包**（权限校验，emag 可绕过）
- 日志渲染：语言未知时乱码（除非 `universal_translate` 或使用者会该语言）；种族识别——Humanoid（人类/大脑）、Slime、Artificial Life（硅基或 AI 职位）、Machinery、Domestic Animal、Unidentifiable

### 4.5 机器交互与检修（Multitool 操作）

源码：`code/game/machinery/telecomms/machine_interactions.dm`

- 交互界面 "Telecomms"（tgui）：显示频道列表（get_channels，含保留频率与服务器登记的频道）、链接列表、监听频率
- 操作（`ui_act`，非硅基必须手持**多功能工具 multitool**）：
  - `toggle` 开关机（记日志）
  - `id` 改 ID（≤32 字符）；`network` 改网络名（≤15 字符，**改网络会清空全部链接**）
  - `freq` 添加监听频率（banned_frequencies 拒绝）；`delete` 移除
  - `link` 用 multitool 缓冲链接机器；`unlink` 断开；`buffer`/`flush` 管理缓冲
- 各机型专属选项：
  - **Relay**：`broadcast` / `receive` 收发开关
  - **Bus**：`change_freq` 改频（< 10000 生效，否则清零）
  - **Server**：`modify_freq_info` 改频道名/颜色（有主题的频道不可改色）、`add_freq_info`（频率须已在监听列表，名不得与现有频道冲突）、`delete_freq_info`
- 工具交互：螺丝刀拆装（default_deconstruction）、撬棍拆除、multitool 直接打开 UI（`multitool_act`）
- AI 可用内置 `aiMulti` 直接操作

### 4.6 网络被破坏的后果

| 被破坏对象 | 后果 |
|---|---|
| **接收器全灭** | 头戴式耳机信号无法进入网络（`check_receive_level` 失败），亚空间通讯全废 |
| **服务器全灭** | 信号无法被记录、无法附加频道名/颜色；但 Bus 可跳过服务器直达 Hub/Broadcaster（延迟略增） |
| **广播器全灭** | 处理后的信号无法播发到电台 |
| **中枢全灭** | Receiver 无法把信号送入 Bus（除非直连）；跨 Z 层转发中断 |
| **中继全灭** | 跨 Z 层（如矿星）通讯中断 |
| **消息服务器全灭** | PDA 短信与请求台消息无法传输（请求台仅依赖它，无兜底） |
| **整网 EMP/断电** | `update_power()` 使所有机器 `on = FALSE`；手持对讲机（非亚空间）2 秒后自动降级为常规无线电仍可本地通讯，**耳机彻底失联** |
| 电信机器被 EMP | `prob(100/severity)` 概率进入 EMPED，持续 `300 秒/severity`（±2 秒） |

---

## 5. 无线电设备全录（Radio Devices）

源码：`code/game/objects/items/devices/radio/`（radio.dm 791 行 / headset.dm 499 行 / intercom.dm 468 行 / encryptionkey.dm 308 行 / electropack.dm 141 行）

### 5.1 手持电台 /obj/item/radio 全参数

**station bounced radio（站内弹跳电台/对讲机）**——基础型，默认图标 walkietalkie。

关键属性（radio.dm L4-101）：

| 属性 | 默认值 | 说明 |
|---|---|---|
| `on` | TRUE | 总开关；关闭时收发全停 |
| `frequency` | FREQ_COMMON (1459) | 当前频率（显示为 145.9） |
| `broadcasting` | FALSE | 是否把周围 3 格内听到的话转播进频道 |
| `listening` | TRUE | 是否接收频道消息 |
| `canhear_range` | 3 | 广播拾音/播放范围 |
| `should_be_broadcasting/listening` | FALSE/TRUE | EMP 强制关闭后恢复的依据 |
| `unscrewed` | FALSE | 螺丝刀拆开后暴露电线（wires.interact） |
| `freerange` | FALSE | 全频段（1201–1599）可调 |
| `subspace_transmission` | FALSE | 纯亚空间模式（耳机为 TRUE） |
| `subspace_switchable` | FALSE | 是否可随时切换亚空间模式 |
| `freqlock` | RADIO_FREQENCY_UNLOCKED | 频率锁（1=锁定，2=可被 emag 解锁） |
| `keylock` | RADIO_KEYSLOT_UNLOCKED | 钥匙槽锁 |
| `use_command` / `command` | FALSE / FALSE | 命令频道模式（消息加粗加大 + SPAN_COMMAND） |
| `radio_noise` | TRUE | 播放无线电噪音 |
| `anonymize` | FALSE | 匿名发言（显示 "Unknown"） |
| `keyslot` | null | 加密钥匙槽 |
| `special_channels` | NONE | 特殊频道标志位 |

核心行为：

- **`talk_into()`**（L286）：检查 `COMSIG_MOVABLE_USING_RADIO`/`COMSIG_RADIO_NEW_MESSAGE` 信号 → 异步 `talk_into_impl()` → 返回 `ITALICS | REDUCE_RANGE`（**使用电台说话会降低本地语音范围到 1 格**）
- **发射流程**（`talk_into_impl` L311）：关机电台/剪断 WIRE_TX 线/`try_speak` 失败 → 放弃；`use_command` 附加 SPAN_COMMAND；**耳语经电台会变成 `stars()` 星号**并加斜体；在**无线电干扰器（jammer）范围内无法发射**（除非辛迪加特殊频道）；构造 vocal 信号 → `send_to_receivers()`；纯亚空间设备就此结束，普通设备 **2 秒后**走 `backup_transmission` 常规无线电兜底
- **接收**（`on_receive_message` L457）：播放 `radio_receive.ogg`（0.5 秒冷却）；含 SPAN_COMMAND 的消息播放重要提示音 `radio_important.ogg`
- **EMP**（L657）：`emped++`，频道全部置 0，`set_on(FALSE)`，**20 秒后**恢复（若期间再被 EMP 则顺延）
- **电线（wires）**：WIRE_TX 剪断 = 无法发射（监狱对讲机即如此）
- **彩蛋**：0.5% 概率（愚人节 50%）变成玩具 "Little-Crew: Assistant's First Radio"（`make_silly()`）
- 附加工艺：普通电台可用 `slapcrafting` 做成简易爆炸物

**子类型全录**：

| 类型 | 特点 |
|---|---|
| `/obj/item/radio/borg` | 机器人电台：亚空间、可切换；`resetChannels` 按机型号频道重置 |
| `/obj/item/radio/borg/syndicate` | 辛迪加机器人电台：RADIO_SPECIAL_SYNDIE + 辛迪加钥匙，默认频率 121.3 |
| `/obj/item/radio/borg/syndicate/ghost_role`（NOVA） | DS-2 / Interdyne 硅基电台：Interdyne 钥匙，默认频率 120.9 |
| `/obj/item/radio/off` | 初始关闭听音（省性能） |
| `/obj/item/radio/entertainment` | 娱乐电台：canhear_range 7、freerange、频率锁、无噪音，固定 141.5 |
| `/obj/item/radio/entertainment/speakers` | 娱乐监视器内置音箱（单向收，剪 WIRE_TX） |
| `/obj/item/radio/entertainment/speakers/physical` | "entertainment radio" 实体音箱 |
| `/obj/item/radio/entertainment/microphone` | 广播摄像头内置麦克风（单向发，剪 WIRE_RX） |
| `/obj/item/radio/entertainment/microphone/physical` | "microphone" 实体麦克风，canhear_range 3 |
| `/obj/item/radio/toy` | 玩具电台（直接 make_silly） |

### 5.2 头戴式耳机全录（Headset）

`/obj/item/radio/headset`（headset.dm）：**subspace_transmission = TRUE（纯亚空间）**、canhear_range = 0、槽位 ITEM_SLOT_EARS、**双钥匙槽**（keyslot + keyslot2）。佩戴时由钥匙提供**语言理解**（`grant_headset_languages`，理解度 100 = 完全理解，低于 100 = 部分理解）；脱下时清除（LANGUAGE_RADIOKEY 来源）。**广播开关控制 talk_into 能力**（headset 不因广播而获得听觉敏感）。离体自动关闭听音（`possibly_deactivate_in_loc`）。Alt-点击可在 command 电台切换命令模式（`click_alt`）。

**全部耳机型号清单**：

| 型号 | 说明 / 钥匙 |
|---|---|
| `/headset` | 基础耳机，无钥匙 |
| `/headset/syndicate` | 伪装成普通耳机的辛迪加耳机（make_syndie：辛迪加钥匙 + RADIO_SPECIAL_SYNDIE） |
| `/headset/syndicate/alt` | "syndicate headset" 鲍曼耳机，防闪光弹（earprotection 组件） |
| `/headset/syndicate/alt/leader` | 队长耳机，command = TRUE |
| `/headset/binary` | 二进制翻译钥匙（机器语言） |
| `/headset/headset_sec` | "security radio headset" 安保耳机（安保钥匙） |
| `/headset/headset_sec/alt` | 安保鲍曼耳机（防闪光） |
| `/headset/headset_eng` | 工程耳机（工程钥匙） |
| `/headset/headset_rob` | 机器人学耳机（科学+工程钥匙） |
| `/headset/headset_med` | 医疗耳机（医疗钥匙） |
| `/headset/headset_sci` | 科学耳机（科学钥匙） |
| `/headset/headset_medsci` | 医研耳机（医疗+科学钥匙） |
| `/headset/headset_srvsec` | "law and order headset" 服务+安保钥匙 |
| `/headset/headset_srvmed` | 心理/服务医疗耳机（医疗+服务钥匙） |
| `/headset/headset_srvent` | "press headset" 记者耳机（服务+娱乐钥匙） |
| `/headset/headset_com` | 指挥耳机（指挥钥匙） |
| `/headset/heads` | 指挥层基类，command = TRUE |
| `/headset/heads/captain` | 船长耳机（船长钥匙：指挥+安保+全部门 0） |
| `/headset/heads/captain/alt` | 船长鲍曼耳机 |
| `/headset/heads/rd` | 研发总监耳机（科学+指挥） |
| `/headset/heads/hos` | 安保部长耳机（安保+指挥） |
| `/headset/heads/hos/advisor` | 资深安保顾问耳机（同钥匙，command = FALSE） |
| `/headset/heads/hos/alt` | 安保部长鲍曼耳机 |
| `/headset/heads/ce` | 总工程师耳机（工程+指挥） |
| `/headset/heads/cmo` | 首席医疗官耳机（医疗+指挥） |
| `/headset/heads/hop` | 人事主管耳机（服务+指挥） |
| `/headset/heads/qm` | 军需官耳机（补给+指挥） |
| `/headset/headset_cargo` | 补给耳机（补给钥匙） |
| `/headset/headset_cargo/mining` | 矿工耳机（补给+科学钥匙）：**低气压环境扩音**（TRAIT_SPEECH_BOOSTER），Ctrl-点击开关呼号（callouts 组件） |
| `/headset/headset_srv` | 服务耳机（服务钥匙） |
| `/headset/headset_cent` | CentCom 耳机（CentCom 钥匙 + 指挥钥匙双槽） |
| `/headset/headset_cent/empty` | 无钥匙版 |
| `/headset/headset_cent/commander` | 指挥官版（船长钥匙，command = TRUE） |
| `/headset/headset_cent/alt` | CentCom 鲍曼耳机（防闪光，单钥匙） |
| `/headset/headset_cent/alt/leader` | 队长版（command = TRUE） |
| `/headset/silicon/pai` | "mini Integrated Subspace Transceiver" pAI 收发器（**非亚空间**） |
| `/headset/silicon/ai` | "Integrated Subspace Transceiver" AI 收发器（AI 全频道钥匙，command = TRUE） |
| `/headset/silicon/ai/evil` | "Evil Integrated Subspace Transceiver" 故障 AI（AI 邪恶钥匙 + 辛迪加） |
| `/headset/silicon/human_ai` | "Disconnected Subspace Transceiver"（AI+二进制钥匙，TRAIT_LOUD_BINARY） |
| `/headset/chameleon/advanced`（NOVA） | 变色龙高级耳机：防闪光 + 扩音，command + freerange |
| `/headset/headset_faction`（NOVA） | 派系耳机（派系钥匙） |
| `/headset/headset_faction/bowman`（NOVA） | 派系鲍曼耳机 |
| `/headset/headset_faction/bowman/captain`（NOVA） | 派系队长鲍曼耳机（command = TRUE） |
| `/headset/headset_solfed/atmos`（NOVA） | SolFed 高级大气耳机（SolFed 钥匙） |
| `/headset/tarkon`（NOVA） | "tarkon headset"：freerange + 频率锁 + Tarkon 钥匙 |
| `/headset/tarkon/command`（NOVA） | Tarkon 指挥耳机（command = TRUE） |
| `/headset/silicon/ai/faction`（NOVA bitrunning） | 派系 AI 耳机（bitrunning 公告用，主频道在 keyslot2） |

### 5.3 对讲机全录（Intercom）

`/obj/item/radio/intercom`（intercom.dm）：**canhear_range = 2**、anchored（挂墙）、受**区域供电**控制（COMSIG_AREA_POWER_CHANGE → AreaPowerCheck，断电即关）；螺丝刀拆面板、扳手拆墙（80 单位时间）；emag 可解除频率锁/钥匙锁（EMAGGABLE_LOCK）；拆卸后得到 `/obj/item/wallframe/intercom`（铁×2，可重装）。

**全部型号清单**：

| 型号 | 说明 |
|---|---|
| `/intercom` | 标准站内对讲机 |
| `/intercom/unscrewed` | 初始拆面板版（墙框安装产物） |
| `/intercom/prison` | "receive-only intercom" 监狱接收-only（剪 WIRE_TX） |
| `/intercom/chapel` | "Confessional intercom" 告解室：anonymize + 频率锁（emag 可解），固定 **148.1**（FREQ_CONFESSIONAL） |
| `/intercom/command` | 指挥对讲机：freerange + command（任意频率 + 扩音） |
| `/intercom/interrogation` | 审讯室对讲机基类：频率锁，固定 **142.3**（FREQ_INTERROGATION） |
| `/intercom/interrogation/inside` | 室内版：持续广播（broadcasting=TRUE，listening=FALSE） |
| `/intercom/interrogation/outside` | 室外版：anonymize（变声"隐私"） |
| `/intercom/freerange` | "free-range intercom" 任意频率 |
| `/intercom/freerange/ai_core` | AI 核心自由对讲机（初始不听音） |
| `/intercom/syndicate` | 辛迪加对讲机：command + RADIO_SPECIAL_SYNDIE |
| `/intercom/syndicate/freerange` | "syndicate wide-band intercom" 宽频版（+freerange） |
| `/intercom/mi13` | 无名 "intercom"（freerange） |
| `/intercom/ai_private` | AI 私线对讲机，固定 **144.7** |
| `/intercom/ai_private/broadcasting` | AI 上传室版（持续广播） |
| `/intercom/ai_private/freerange` | AI 舱室版（+freerange，初始不听音） |
| `/intercom/ai_private/quiet` | AI 前厅版（不听音，不广播） |
| `/intercom/departmental` | 部门对讲机基类：钥匙锁（emag 可解）+ 彩色条纹（stripe_color）；初始化时把频率调到钥匙第一个频道 |
| `/intercom/departmental/cargo` | 补给条纹 `#956929`（补给钥匙） |
| `/intercom/departmental/command` | 指挥条纹 `#486091`（指挥钥匙） |
| `/intercom/departmental/engineering` | 工程条纹 `#EFB341`（工程钥匙） |
| `/intercom/departmental/medical` | 医疗条纹 `#52B4E9`（医疗钥匙） |
| `/intercom/departmental/science` | 科学条纹 `#D381C9`（科学钥匙） |
| `/intercom/departmental/security` | 安保条纹 `#DE3A3A`（安保钥匙） |
| `/intercom/departmental/service` | 服务条纹 `#83ca41`（服务钥匙） |
| `/intercom/departmental/head` | 指挥层基类（command = TRUE + 指挥层图标） |
| `/intercom/departmental/head/ce` | 总工程师对讲机（CE 钥匙） |
| `/intercom/departmental/head/cmo` | 首席医疗官对讲机（CMO 钥匙） |
| `/intercom/departmental/head/hop` | 人事主管对讲机（HOP 钥匙） |
| `/intercom/departmental/head/hos` | 安保部长对讲机（HOS 钥匙） |
| `/intercom/departmental/head/qm` | 军需官对讲机（QM 钥匙） |
| `/intercom/departmental/head/rd` | 研发总监对讲机（RD 钥匙） |

全部 28 个 intercom 型号均注册了方向辅助（MAPPING_DIRECTIONAL_HELPERS，偏移 27 像素）供地图使用。

### 5.4 加密钥匙全录（Encryption Keys）

`/obj/item/encryptionkey`（encryptionkey.dm）：微型物品；`channels`（频道→开关）、`special_channels`、`language_data`（语言理解度，0-100；25-50 差 / 50-75 中 / 75-100 好）。插入耳机/对讲机即解锁对应频道。

**全部钥匙清单**：

| 钥匙 | 频道（=1 开启，=0 关闭） | 特殊标志 | 语言 |
|---|---|---|---|
| `/encryptionkey`（标准） | 无 | 无 | 无 |
| `/encryptionkey/syndicate` | Syndicate | RADIO_SPECIAL_SYNDIE | — |
| `/encryptionkey/binary` | — | RADIO_SPECIAL_BINARY | 机器语言 100 |
| `/encryptionkey/headset_sec` | Security | — | — |
| `/encryptionkey/headset_eng` | Engineering | — | — |
| `/encryptionkey/headset_rob` | Science, Engineering | — | — |
| `/encryptionkey/headset_med` | Medical | — | — |
| `/encryptionkey/headset_sci` | Science | — | — |
| `/encryptionkey/headset_medsci` | Science, Medical | — | — |
| `/encryptionkey/headset_srvsec` | Service, Security | — | — |
| `/encryptionkey/headset_srvmed` | Medical, Service | — | — |
| `/encryptionkey/headset_srvent` | Service, Entertainment(=0 关闭) | — | — |
| `/encryptionkey/headset_com` | Command | — | — |
| `/encryptionkey/heads/captain` | Command, Security + Engineering/Science/Medical/Supply/Service(=0) | — | — |
| `/encryptionkey/heads/rd` | Science, Command | — | — |
| `/encryptionkey/heads/hos` | Security, Command | — | — |
| `/encryptionkey/heads/ce` | Engineering, Command | — | — |
| `/encryptionkey/heads/cmo` | Medical, Command | — | — |
| `/encryptionkey/heads/hop` | Service, Command | — | — |
| `/encryptionkey/heads/qm` | Supply, Command | — | — |
| `/encryptionkey/headset_cargo` | Supply | — | — |
| `/encryptionkey/headset_mining` | Supply, Science | — | — |
| `/encryptionkey/headset_service` | Service | — | — |
| `/encryptionkey/headset_cent` | CentCom | RADIO_SPECIAL_CENTCOM | — |
| `/encryptionkey/ai` | Command, Security, Engineering, Science, Medical, Supply, Service, AI Private, Entertainment | — | — |
| `/encryptionkey/ai_with_binary` | 同上全部 9 频道 | RADIO_SPECIAL_BINARY | 机器语言 100 |
| `/encryptionkey/ai/evil` | Syndicate | RADIO_SPECIAL_SYNDIE | — |
| `/encryptionkey/secbot` | AI Private, Security | — | — |
| `/encryptionkey/headset_syndicate/cybersun`（NOVA） | Cybersun | RADIO_SPECIAL_CENTCOM | — |
| `/encryptionkey/headset_syndicate/interdyne`（NOVA） | Interdyne | RADIO_SPECIAL_CENTCOM | — |
| `/encryptionkey/headset_syndicate/guild`（NOVA） | Guild | RADIO_SPECIAL_CENTCOM | — |
| `/encryptionkey/headset_cargo/tarkon`（NOVA） | Tarkon | RADIO_SPECIAL_CENTCOM | — |
| `/encryptionkey/headset_faction`（NOVA） | Faction | RADIO_SPECIAL_CENTCOM | — |
| `/encryptionkey/headset_solfed/atmos`（NOVA） | SolFed | — | — |
| `/encryptionkey/headset_bitrunning`（NOVA） | bitrunning 专用 | — | — |

### 5.5 其他无线电设备

#### 电击背包 Electropack（`electropack.dm`）

- 背包装备（ITEM_SLOT_BACK）、巨型、导电；默认频率 **144.9**（FREQ_ELECTROPACK）、默认码 **2**
- 收到同频同码信号器信号 → 电击佩戴者：随机方向踉跄一步、`Paralyze(100)`、火花，**10 秒冷却**
- 可调频率（120.1–159.9 全自由频段）与码（1–100，clamp）；可与头盔组装成**刑具 shock_kit**（`/obj/item/assembly/shock_kit`）
- 信号过滤：RADIO_SIGNALER

#### 无线电干扰器（Jammer）

`talk_into_impl` 中检查 `is_within_radio_jammer_range(src)`——**干扰器范围内的电台无法发射**（辛迪加特殊频道除外）。

---

## 6. 通信控制台（Communications Console）

源码：`code/game/machinery/computer/communications.dm`（980 行）

`/obj/machinery/computer/communications`：需要 **ACCESS_COMMAND**；关键冷却——重要行动 60 秒（IMPORTANT_ACTION_COOLDOWN）、紧急通道 30 秒（EMERGENCY_ACCESS_COOLDOWN）、紧急通道免费使用 3 次（toggle_max_uses）。

**完整功能清单**（`ui_act`）：

| 功能 | 权限 | 说明 |
|---|---|---|
| 呼叫穿梭机 callShuttle | 已认证（硅基或刷卡），非辛迪加台 | 原因 ≥ 12 字符（CALL_SHUTTLE_REASON_LENGTH） |
| 召回穿梭机 recallShuttle | 已认证，**AI 不可** | 取消撤离 |
| 购买穿梭机 purchaseShuttle | 有购买权限（SSshuttle.has_purchase_shuttle_access） | 从货运账户扣款；emag_only 型号需 emag；可查看预算/占用/描述 |
| 更改警戒级别 changeSecurityLevel | 舰长刷卡或硅基 | 仅绿→琥珀（NOVA 限制，原为绿→蓝），DELTA 期间不可改 |
| 优先级公告 makePriorityAnnouncement | 舰长/硅基，或辛迪加台 | 经 `communications_controller.make_announcement`，有播音冷却 |
| 联系上级 messageAssociates | 舰长或 AI（NOVA 修改允许 AI）；emag/辛迪加台发给辛迪加 | 60 秒冷却；经 `message_centcom` / `message_syndicate` |
| 请求核弹代码 requestNukeCodes | 非硅基舰长 | 60 秒冷却，全站优先级公告 |
| 跨服务器消息 sendToOtherSector | 非硅基舰长 | 需配置 cross_server；软过滤确认 + 管理员可取消（cancel 计时器） |
| 修改状态显示屏 setStatusMessage | 已认证 | 两行各 ≤ 40 字符（MAX_STATUS_LINE_LENGTH），经 FREQ_STATUS_DISPLAYS (1435) 广播 |
| 设置状态图片 setStatusPicture | 已认证 | approved pictures 列表 |
| 紧急维护通道 toggleEmergencyAccess | 舰长/硅基 | 免费 3 次后 30 秒冷却 |
| 工程门禁覆写 toggleEngOverride（NOVA） | 舰长/硅基 | GLOB.force_eng_override |
| 请求备用 ID 保险柜码 requestSafeCodes | 无现任船长时 | 120 秒后投放 |
| 联系联邦 messagethefeds（NOVA） | — | SolFed 消息流程，60 秒冷却 |
| 呼叫紧急响应 callThePolice/callTheCatmos/callTheParameds（NOVA） | pre_911_check | 警长/高级大气/EMT 响应，带趣味广播词（"WOOP WOOP THAT'S THE SOUND OF THE POLICE" 等） |
| 叫披萨 callThePizza（NOVA） | 需 emag | 触发 Dogginos 披萨事件（EMERGENCY_RESPONSE_EMAG "AYO THE PIZZA HERE"） |
| 认证 toggleAuthentication | 刷 ID 卡 | emag 后自动全站权限（REGION_ALL_STATION） |
| 回复消息 answerMessage / 删除 deleteMessage | 已认证 | 处理 `/datum/comm_message`（title/content/possible_answers/answer_callback） |

**emag 效果**：获得全站访问权限（`SSid_access.get_region_access_list(REGION_ALL_STATION)`）；messageAssociates 改发给辛迪加；解锁 emag_only 穿梭机；可 "restoreBackupRoutingData" 解除 emag（需舰长）。

**辛迪加变体** `/communications/syndicate`：红色主题、需 ACCESS_SYNDICATE_LEADER、不可买穿梭机/不可跨服务器/不可硅基认证；其 get_communication_players 排除同 Z 层与已死亡者；**无法被 emag**。

**控制台黑客（hack_console）**：`try_hack_console(hacker, duration = 30 秒)` → 结果随机：**海盗**（≥4 幽灵，非行星图）、**逃犯**（≥6 幽灵，非行星图）、**休眠特工**（数量 = 存活人数/40，clamp 1-3）。战列巡洋舰 emag 卡（`/obj/item/card/emag/battlecruiser`）可召唤辛迪加战列巡洋舰（20 秒–1 分钟延迟，全站公告，仅限叛徒使用，每局一次）。

**Destroy 行为**：从 shuttle_caller_list 移除并触发 `SSshuttle.autoEvac()`。

---

## 7. 全息通话（Holocalls）

源码：`code/datums/holocall.dm`（595 行）+ `code/__DEFINES/holopads.dm`

**`/datum/holocall`**（全息通话管理 datum，L18）：

- 字段：`user`（呼叫者）、`calling_holopad`（发起全息垫）、`connected_holopad`（应答垫，可空）、`dialed_holopads`（所有被拨打的垫）、`eye`（用户视角）、`hologram`（用户全息投影）、`hangup`（挂断技能 `/datum/action/innate/end_holocall`）、`head_call`（**指挥层呼叫自动连接**——若应答垫不 secure）
- **拨号**（New）：遍历所有目标垫，`is_operational` 的垫播报 "Incoming call."（指挥呼叫未加密垫播 "Incoming connection."，加密垫播 "Auto-connection refused, falling back to call mode."）；无可用垫则主叫垫播报并销毁
- **应答**（Answer）：断开其他被拨垫、断开该垫上的其他通话；在应答垫生成全息投影（`activate_holo(user)`）、创建远程视角 eye、授予挂断技能、播放 ping 音
- **校验**（Check）：任何被拨垫失效 → 断线；主叫者死亡/ incapacitated / 离开主叫垫 → 通话结束；**拨号超时 HOLOPAD_MAX_DIAL_TIME = 200 tick（20 秒）**
- **断线**（Disconnect / ConnectionFailure）：优雅挂断播报区域名/用户名；非优雅断线直接销毁

**全息录音（Holorecord）**：

- `/datum/holorecord`：caller_name / caller_image / entries / language
- `/obj/item/disk/holodisk`：全息录音碟，可复制到另一张碟；脚本命令：`NAME`（改名）、`DELAY`（延迟）、`SAY`（说话）、`SOUND`（音效）、`LANGUAGE`（语言）、`PRESET`（预设形象）
- 预设形象 `/datum/preset_holoimage`：`clown`（小丑）、`engineer`（工程师）、`engineer/mod`、`engineer/ce`（总工）、`engineer/ce/mod`、`engineer/atmos`（大气技师）、`engineer/atmos/mod`、`researcher`（研究员）、`captain`（船长）、`nanotrasenprivatesecurity`（NT 私兵）、`syndicatebattlecruisercaptain`（辛迪加舰长）、`hivebot`（蜂巢机器人）、`ai`、`robot`、`assistant`（助手）、`corgi`（柯基）
- 内置录音碟：`/example`（小丑笑话）、`/donutstation/whiteship`（DS024 黑匣子）、`/ruin/snowengieruin`（EB412）、`/ruin/ghost_restaurant`（NG234 鬼餐厅）、`/ruin/space/travelers_rest`（店主留言）

---

## 8. 请求消息（Request Messages）

源码：`code/datums/request_message.dm`（57 行）

`/datum/request_message`（请求台消息 datum）：

- 字段：`sender_department`（发送部门）、`received_time`（到达时间，round_timestamp）、`content`（内容）、`message_verified_by`（验证 ID 姓名）、`message_stamped_by`（盖章者）、`priority`（优先级）、`radio_channel`（广播频道，如 notify_channel）、`request_type`（请求类型）、`appended_list`（附加列表，如矿石清单）
- 请求类型常量（`__DEFINES/radio.dm` L143-147）：Assistance Request 援助请求、Supplies Request 补给请求、Relay Information 信息转达、Ore Update 矿石更新、Reply 回复
- `get_alert()`：生成警报文本 "Message from [部门][, Verified by 姓名 (Authenticated) / , Stamped by 姓名 (Authenticated)]"
- `message_ui_data()`：转为 tgui JSON 数据

---

## 9. 数据 HUD（Data HUDs）

源码：`code/game/data_huds.dm`（568 行）

采用观察者-监听者模式（observer-listener），`/datum/atom_hud/data` 为数据 HUD 基类。

**HUD datum 全录**：

| HUD | hud_icons | 说明 |
|---|---|---|
| `/data/human/medical/basic` | STATUS_HUD, HEALTH_HUD, DNR_HUD, SHIELD_HUD（NOVA 加后两者） | 基础医疗 HUD：**需开启制服传感器**（TRAIT_BASIC_HEALTH_HUD_VISIBLE） |
| `/data/human/medical/advanced` | 同上 | 高级医疗 HUD：始终可见 |
| `/data/human/security/basic` | ID_HUD | 仅显示 ID 职位 |
| `/data/human/security/advanced` | ID_HUD, IMPSEC_FIRST_HUD, IMPLOYAL_HUD, IMPSEC_SECOND_HUD, WANTED_HUD, PERMIT_HUD, DNR_HUD（NOVA 加后两者） | 职位 + 植入体 + 忠诚 + 通缉 + 枪证 |
| `/data/human/fan_hud` | FAN_HUD | 粉丝识别（小丑/哑剧粉丝徽章） |
| `/data/diagnostic` | DIAG_HUD, DIAG_STAT_HUD, DIAG_BATT_HUD, DIAG_MECH_HUD, DIAG_BOT_HUD, DIAG_TRACK_HUD, DIAG_CAMERA_HUD, DIAG_AIRLOCK_HUD, DIAG_LAUNCHPAD_HUD | 诊断 HUD（9 种图标） |
| `/data/bot_path` | DIAG_PATH_HUD | 机器人路径 HUD（private 变体不共享全局分类） |
| `/abductor` | GLAND_HUD | 绑架者腺体 HUD |
| `/ai_detector` | AI_DETECT_HUD | AI 探测器 |
| `/data/malf_apc` | MALF_APC_HUD | 故障 AI 的 APC 黑客 HUD |
| `/data/human/blood` | BLOOD_HUD | 血量 HUD |

**健康显示**（RoundHealth）：0-100 按 6.25 步进分档（health100 → health0），死亡/假死显示 health-100；濒死（crit）按 maxHealth=100 折算（异形等高血量碳基）。

**医疗钩子**：`med_hud_set_health` / `med_hud_set_status`——状态含：健康（hudhealthy）、死亡（huddead）、可除颤（huddefib）、幽灵角色（hudghost）、异形宿主（hudxeno，NOVA 加蜘蛛宿主）、**疾病等级 hudill0–hudill6**（非威胁→不可愈）、增益（hudbuff）、传感器损坏（hudnosensor）。

**安保钩子**：`update_ID_card`（职位图标 + NOVA 枪证 PERMIT_HUD）、`sec_hud_set_implants`（2 个安保植入体槽 + 忠诚芯片）、`sec_hud_set_security_status`（通缉状态：hudwanted 逮捕 / hudincarcerated 监禁 / hudsuspected 嫌疑 / hudparolled 假释 / huddischarged 释放；TRAIT_ALWAYS_WANTED 常驻通缉）。

**诊断钩子**：硅基生命（huddiag* 分档 max/good/high/med/low/crit/dead）、机娘电池（hudbatt*）、AI 外壳追踪（hudtracking / hudtrackingai）、机甲（机甲健康/电池/状态警告/信标追踪/摄像头 EMP 红点）、气闸通电（electrified）、故障 APC。

**血液钩子**：round_blood_for_hud（hudblood100/75/50/25/0）。

---

## 10. NOVA 追加内容

### 10.1 电信专家职业（`modular_nova/modules/telecomms_specialist/telecomms_specialist.dm`，120 行）

- **JOB_TELECOMMS_SPECIALIST**：1 个岗位，归属工程部（departments_list = engineering），上级 CE，要求 **60 经验**（EXP_TYPE_CREW）
- 描述："Monitor, configure, and maintain all station communications and assist with light engineering work."
- 装备：工程耳机（headset_eng）、多功能工具、电信 PDA（内置 alarm_monitor / **signal_commander** / ntnetmonitor 程序）、monitorkey 纸条、电工技能芯片
- 门禁：ACCESS_TCOMMS、**ACCESS_NETWORK**（网络）、ACCESS_RC_ANNOUNCE（请求台公告）、ACCESS_MINISAT、ACCESS_CONSTRUCTION、ACCESS_ENGINEERING、ACCESS_MAINT_TUNNELS、ACCESS_MECH_ENGINE、ACCESS_MINERAL_STOREROOM、ACCESS_TECH_STORAGE；额外：大气/工程设备/外部气闸；模板：船长/改 ID/CE
- 邮件礼物：咖啡弹匣 20 / 杯子 10 / 亚空间零件 8 / 锤子 8 / 维护碟 1
- 传家宝：笔记本、电台、pAI 卡、口袋护具

### 10.2 派系频道支持（`modular_nova/modules/faction/code/radio.dm`）

- `/obj/item/encryptionkey/headset_faction`（Faction 频道 + RADIO_SPECIAL_CENTCOM）
- `/obj/item/radio/headset/headset_faction` + bowman + bowman/captain

### 10.3 阵营钥匙（`modular_nova/modules/mapping/code/radio.dm`）

- Cybersun（`:q` 121.1）、Interdyne（`:w` 120.9）、Guild（`:d` 121.4）、Tarkon（`:k` 124.3）四把钥匙，均带 RADIO_SPECIAL_CENTCOM（可全图收听）
- SolFed 钥匙（`modular_nova/modules/goofsec/code/sol_fed.dm`）：`/encryptionkey/headset_solfed/atmos` + 对应耳机

### 10.4 无线电音效（`modular_nova/modules/radiosound/code/radio.dm`）

- 基础电台：walkie_talkie.ogg，音量 25，短距离音域，可变调
- 耳机：radio_chirp.ogg（静音级音域，不变调）
- 对讲机：短距离音域
- 辛迪加耳机：syndie.ogg；安保耳机：security.ogg
- 取代原版仅播放给说话者的机制——**NOVA 改为对附近所有玩家播放**（`playsound_if_pref`，尊重 sound_radio_noise 音量偏好）

### 10.5 Bitrunning 电信接入（`modular_nova/modules/bitrunning/code/telecomms.dm`）

- `/obj/item/radio/headset/silicon/ai/faction`：给公告系统派系频道访问权
- **服务器/接收器 A/总线 2 的 Initialize 追加监听 FREQ_FACTION**——保证派系消息可在站内传输

### 10.6 弹药 HUD（`modular_nova/modules/gunhud/code/gun_hud.dm` + `gun_hud_component.dm`）

- `/atom/movable/screen/ammo_counter`：可自定义弹药计数器 HUD（screen_loc = ui_ammocounter），由 ammo_hud 组件驱动
- `turn_on` / `turn_off` / `set_hud(_backing_color, _oth_o, _oth_t, _oth_h, _indicator, _oth_backing)`：三位数字（OTH：个/十/百）+ 指示灯 + 背光，全部可着色（默认红色背光）

### 10.7 处方 HUD 眼镜（`modular_nova/modules/huds/code/glasses/HUD_Glasses.dm`）

- 处方健康扫描 HUD、处方诊断 HUD、处方安保 HUD、处方科学眼镜（火 80/酸 100 护甲）、处方光学介子扫描仪、处方工程扫描护目镜、处方 T-ray 扫描仪——均带 TRAIT_NEARSIGHTED_CORRECTED（矫正近视）
- 另有 `modular_nova/modules/huds/code/designs.dm`（设计图）

### 10.8 其他 NOVA 通信物品

- 机器人幽灵角色电台（DS-2/Interdyne，master_files radio.dm）
- 变色龙高级耳机、伪装者 CentCom 耳机（moretraitoritems）
- 塔肯耳机（tarkon/misc_fluff/radio.dm）
- 中文 I18N 支持：`say.dm` 中全角 `！！？` 与半角等价（NOVA EDIT CHANGE）；玩家可见字符串统一走 `LANG()` 键值系统（各文件头部注释）

---

## 11. 源码路径索引

| 模块 | 源码路径 |
|---|---|
| 对话系统 | `code/game/say.dm`、`code/modules/mob/living/living_say.dm` |
| 频道/频率定义 | `code/__DEFINES/radio.dm`、`code/__DEFINES/say.dm`、`code/__DEFINES/~nova_defines/radio.dm`、`~nova_defines/faction.dm`、`~nova_defines/solfed.dm` |
| 全局电台注册表 | `code/game/communications.dm` |
| 电信基类 | `code/game/machinery/telecomms/telecomunications.dm` |
| 电信机器 | `code/game/machinery/telecomms/machines/receiver.dm`、`hub.dm`、`relay.dm`、`bus.dm`、`processor.dm`、`server.dm`、`broadcaster.dm`、`allinone.dm`、`message_server.dm` |
| 电信电脑 | `code/game/machinery/telecomms/computers/telemonitor.dm`、`message.dm`、`logbrowser.dm` |
| 交互 | `code/game/machinery/telecomms/machine_interactions.dm`、`broadcasting.dm` |
| 手持电台 | `code/game/objects/items/devices/radio/radio.dm` |
| 耳机 | `code/game/objects/items/devices/radio/headset.dm` |
| 对讲机 | `code/game/objects/items/devices/radio/intercom.dm` |
| 加密钥匙 | `code/game/objects/items/devices/radio/encryptionkey.dm` |
| 电击背包 | `code/game/objects/items/devices/radio/electropack.dm` |
| 通信控制台 | `code/game/machinery/computer/communications.dm` |
| 全息通话 | `code/datums/holocall.dm`、`code/__DEFINES/holopads.dm` |
| 请求消息 | `code/datums/request_message.dm` |
| 数据 HUD | `code/game/data_huds.dm` |
| NOVA 电信专家 | `modular_nova/modules/telecomms_specialist/telecomms_specialist.dm` |
| NOVA 派系/阵营 | `modular_nova/modules/faction/code/radio.dm`、`modular_nova/modules/mapping/code/radio.dm`、`modular_nova/modules/goofsec/code/sol_fed.dm`、`modular_nova/modules/tarkon/code/misc_fluff/radio.dm` |
| NOVA 音效 | `modular_nova/modules/radiosound/code/radio.dm` |
| NOVA bitrunning | `modular_nova/modules/bitrunning/code/telecomms.dm`、`outfit.dm` |
| NOVA 弹药 HUD | `modular_nova/modules/gunhud/code/gun_hud.dm`、`gun_hud_component.dm` |
| NOVA HUD 眼镜 | `modular_nova/modules/huds/code/glasses/HUD_Glasses.dm`、`designs.dm` |
| NOVA 硅基电台 | `modular_nova/master_files/code/game/objects/items/devices/radio/radio.dm` |
