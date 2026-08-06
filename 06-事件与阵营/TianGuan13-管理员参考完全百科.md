# 天关 — 管理员参考完全百科

> **项目**: TianGuan13 (Nova Sector → /tg/station)
> **代码**: `code/modules/admin/`（159 文件 31,165 行）
> **范围**: 管理员功能全录——封禁/权限/动词/SDQL2/变量查看/天罚
>
> **用途**: 服务器管理员参考手册。

---

## 目录

- [第一卷 · 权限体系](#第一卷--权限体系)
- [第二卷 · 封禁与消息](#第二卷--封禁与消息)（8 类型+粘性+投票）
- [第三卷 · 管理员动词（191 个）](#第三卷--管理员动词191-个)
- [第四卷 · SDQL2 与高级工具](#第四卷--sdql2-与高级工具)（38 天罚+SDQL2）
- [附录 · 代码路径索引](#附录--代码路径索引)

---

# 第一卷 · 权限体系

## 1.1 权限标志（15 个 R_XXX）

**代码**: `__DEFINES/admin.dm`

| 权限 | 位 | 说明 |
|---|---|---|
| **R_BUILD** | 1<<0 | 建造/编辑地图 |
| **R_ADMIN** | 1<<1 | 管理员基础 |
| **R_BAN** | 1<<2 | 封禁 |
| **R_FUN** | 1<<3 | 娱乐（天罚等） |
| **R_SERVER** | 1<<4 | 服务器管理 |
| **R_DEBUG** | 1<<5 | 调试 |
| **R_POSSESS** | 1<<6 | 附身 |
| **R_PERMISSIONS** | 1<<7 | 权限管理 |
| **R_STEALTH** | 1<<8 | 隐身 |
| **R_POLL** | 1<<9 | 投票管理 |
| **R_VAREDIT** | 1<<10 | 变量编辑 |
| **R_SOUND** | 1<<11 | 声音播放 |
| **R_SPAWN** | 1<<12 | 生成物品 |
| **R_AUTOADMIN** | 1<<13 | 自动激活管理员 |
| **R_DBRANKS** | 1<<14 | 数据库权限 |
| **R_EVERYTHING** | 全位 | 全部权限 |

## 1.2 管理员持有者（holder2.dm 523 行）

| 机制 | 值 |
|---|---|
| **/datum/admins** | 管理员数据（多 rank 组合） |
| **2FA** | 双因素认证（可绕过标志） |
| **href_token** | 代码安全令牌（topic 授权） |
| 标记数据 | tagged_datums（快速参考） |
| 假名 | fakekey（隐身） |
| 调试工具 | 滤镜/粒子/色盲/外观调试器 |

## 1.3 权限分配

| 机制 | 说明 |
|---|---|
| 管理员等级 | admin rank（组合权限） |
| 数据库权限 | R_DBRANKS |
| 激活 | R_AUTOADMIN 自动/手动 |
| 保护 | protected_admins（不可降级） |

---


---


---


---

# 第二卷 · 封禁与消息

## 2.1 封禁类型（8 种）

**代码**: `__DEFINES/admin.dm` + `sql_ban_system.dm`（1,130 行）

| 类型 | 值 | 说明 |
|---|---|---|
| **BANTYPE_PERMA** | 1 | 永久全服封禁 |
| **BANTYPE_TEMP** | 2 | 临时全服封禁 |
| **BANTYPE_JOB_PERMA** | 3 | 永久职位封禁 |
| **BANTYPE_JOB_TEMP** | 4 | 临时职位封禁 |
| **BANTYPE_ANY_FULLBAN** | 5 | 任何全服封禁 |
| **BANTYPE_ADMIN_PERMA** | 7 | 永久管理员封禁 |
| **BANTYPE_ADMIN_TEMP** | 8 | 临时管理员封禁 |
| **BANTYPE_ANY_JOB** | 9 | 任何职位封禁 |

## 2.2 封禁流程

| 机制 | 说明 |
|---|---|
| **SQL 封禁** | 数据库存储（跨重启） |
| 类型 | CKey/IP/CID 封禁 |
| 时长 | 临时/永久 |
| **解封** | 解封面板 |
| 粘性封禁 | stickyban（486 行）跨回合 |

## 2.3 消息系统（sql_message_system.dm 756 行）

| 机制 | 说明 |
|---|---|
| **管理员消息** | 留言/备注 |
| 警告 | 玩家警告记录 |
| SQL 存储 | 数据库持久化 |

## 2.4 其他

| 系统 | 文件 | 说明 |
|---|---|---|
| **投票管理** | poll_management.dm（721 行） | 服务器投票/问卷 |
| **事件日志** | event_logger.dm（552 行） | 管理事件记录 |

> 封禁 UI/流程细节见 §2.1-2.4 各小节。



## 2.5 封禁维度

| 维度 | 实现 |
|---|---|
| **KEY 封禁** | ckey 列 |
| **IP 封禁** | ip 列（INET_ATON） |
| **CID 封禁** | computerid 列 |
| 组合 | Key+IP+CID 同时勾选 |
| **服务器封禁** | role='Server'（踢出） |
| **角色封禁** | role=具体角色（职位/部门） |

## 2.6 粘性封禁（stickyban.dm 486 行）

| 机制 | 说明 |
|---|---|
| **跨回合** | 封禁记录跨重启 |
| 类型 | IP/CID 粘性 |
| 面板 | 粘性封禁面板 |

## 2.7 投票管理（poll_management.dm 721 行）

| 机制 | 说明 |
|---|---|
| **服务器投票** | 全服投票 |
| 问卷 | 玩家问卷 |
| 管理 | 创建/管理投票 |

## 2.8 事件日志（event_logger.dm 552 行）

| 机制 | 说明 |
|---|---|
| **管理事件记录** | 管理操作日志 |
| SQL 存储 | 持久化 |

## 2.9 NOVA 封禁

①EXTRA_BANS 扩展封禁 ②白名单 SQL 化 ③BAN_ 常量 10 个（nova_defines/banning.dm）④工单系统改造。


# 第三卷 · 管理员动词（251 个）

**代码**: `admin/verbs/`（14,691 行）——9 分类（源码全量提取）

## 3.1 分类总览

| 分类 | 数量 | 说明 |
|---|---|---|
| **调试 Debug** | 65 | 源码提取 |
| **地图 Mapping** | 26 | 源码提取 |
| **服务器 Server** | 25 | 源码提取 |
| **游戏 Game** | 35 | 源码提取 |
| **主 Main** | 24 | 源码提取 |
| **事件 Events** | 18 | 源码提取 |
| **娱乐 Fun** | 19 | 源码提取 |
| **穿梭机 Shuttle** | 6 | 源码提取 |
| **配置 Profile** | 3 | 源码提取 |
| **NOVA 追加** | 30 | modular_nova 专属（FUN 8/MAIN 9/EVENTS 5/DEBUG 3/SERVER 3/GAME 2） |

> 源码全量：`code/modules/admin` 共 **221 个动词** + NOVA 追加 **30 个** = **251 个**（此前文档声明的 191 个是不完整提取——漏掉了约 40 个带调用参数的动词与多个分类）。

### 3.2 主 Main（24 个）

| 命令 | 权限 | 功能 |
|---|---|---|
| **管理员命令 - 全部隐藏** | R_NONE | Hide most of your admin verbs. |
| **封禁面板** | R_BAN | Ban players here. |
| **解封面板** | R_BAN | Unban players here. |
| **服务器投票管理** | R_POLL | View and manage polls. |
| **潜行模式** | R_STEALTH | Toggle stealth. |
| **卸任管理员** | R_NONE | Shed your admin powers. |
| **图书馆管理** | R_BAN | List and manage the Library. |
| **已知小号面板** | R_ADMIN | View a panel of known alts. |
| **绘画管理器** | R_ADMIN | View and redact paintings. |
| **权限面板** | R_PERMISSIONS | Edit admin permissions. |
| **粘性封禁面板** | R_BAN | List and manage sticky bans. |
| **战利品管理器** | R_ADMIN | View all trophies. |
| **Whitelist CKey** | R_BAN | Adds a ckey to the Whitelist file. |
| **显示提示** | R_ADMIN | Sends a tip to all players. |
| **公告** | R_ADMIN | Announce your desires to the world. |
| **玩家游玩时长** | R_ADMIN | View player playtime. |
| **管理员私信** | R_NONE | Show a list of clients to PM |
| **管理员发言** | R_NONE | Send a message to other admins |
| **重新加载管理员** | R_NONE | Reloads all admins from the database. |
| **获取服务器日志** | R_ADMIN | View or retrieve logfiles. |
| **获取当前日志** | R_ADMIN | View or retrieve logfiles for the current round. |
| **玩家工单历史** | R_ADMIN | Allows you to view the ticket history of a player. |
| **政策面板** | R_ADMIN | View all policy the server has set. |
| **审核玩家制作的精灵图** | R_ADMIN | View sprites created by players this round. |

### 3.3 游戏 Game（35 个）

| 命令 | 权限 | 功能 |
|---|---|---|
| **调查** | R_NONE | Browse various detailed logs. |
| **管理员幽灵** | R_ADMIN | Become a ghost without DNR. |
| **管理员隐身** | R_ADMIN | Toggles ghost-like invisibility. |
| **切换管理员 ESP** | R_ADMIN | Toggle to be able to see ghosts and invisimins. |
| **检查反派** | R_ADMIN | See all antagonists for the round. |
| **列出炸弹与嫌疑人** | R_ADMIN | Look at all bombs and their likely culprit. |
| **列出信号器** | R_ADMIN | View all signalers. |
| **游戏面板** | R_ADMIN | Look at the state of the game. |
| **检查 AI 法则** | R_ADMIN | View the current AI laws. |
| **管理宗教教派** | R_ADMIN | Manages the chaplain's religion. |
| **切换管理员 AI 交互** | R_ADMIN | Allows you to interact with most machines as an AI would as a ghost. |
| **查看标签** | R_ADMIN | Display all of the tagged datums. |
| **玩家面板** | R_ADMIN | See all players and their Player Panel. |
| **重生角色** | R_ADMIN | Respawn a player that has been round removed in some manner. They must |
| **管理职位名额** | R_ADMIN | Manage the number of available job slots. |
| **更改视野范围** | R_ADMIN | Switch between 1x and custom views. |
| **切换组合 HUD** | R_ADMIN | Toggles the Admin Combo HUD. |
| **显示叛徒面板** | R_ADMIN | Edit mobs's memory and role |
| **显示技能面板** | R_ADMIN | Edit mobs's experience and skill levels |
| **显示卡顿缓解开关** | R_ADMIN | Display the controls for drastic lag mitigation. |
| **生成面板** | R_SPAWN | Spawn Panel (TGUI). |
| **跳转到区域** | R_ADMIN | Jumps to the specified area. |
| **跳转到地块** | R_ADMIN | Jump to any turf in the game. This will lag your client. |
| **跳转到生物** | R_ADMIN | Jump to any mob in the game. |
| **跳转到坐标** | R_ADMIN | Jump to a specific coordinate in the game world. |
| **跳转到 Key** | R_ADMIN | Jump to a specific player. |
| **身体跳转到幽灵处** | R_ADMIN | Jump your body to your Aghost. |
| **抓取 Mob** | R_ADMIN | Teleport a mob to your location. |
| **抓取 Key** | R_ADMIN | Teleport the player with the provided key to you. |
| **发送 Mob** | R_ADMIN | Teleport the specified mob to an area of your choosing. |
| **死者发言** | R_NONE | Speak to the dead. |
| **授予直接控制** | R_ADMIN | Give direct control of a mob to another player. |
| **修复空气** | R_ADMIN | Fixes air in a specified radius. |
| **请求管理器** | R_NONE | Open the request manager panel to view all requests during this round |
| **秘密** | R_NONE | Abuse harder than you ever have before with this handy dandy semi-misc |

### 3.4 调试 Debug（65 个）

| 命令 | 权限 | 功能 |
|---|---|---|
| **生成** | R_SPAWN | Spawn an atom. |
| **补给舱生成** | R_SPAWN | Spawn an atom via supply drop. |
| **生成货物** | R_SPAWN | Spawn a cargo crate. |
| **创建或修改区域** | R_DEBUG | Create of modify an area. wow. |
| **列出法则变更** | R_ADMIN | View all AI law changes. |
| **显示名单** | R_ADMIN | View the shift's Manifest. |
| **列出 DNA** | R_ADMIN | View DNA. |
| **列出指纹** | R_ADMIN | View fingerprints. |
| **获取 DynEx 范围** | R_FUN | Get the estimated range of a bomb using explosive power. |
| **获取 DynEx 威力** | R_FUN | Get the estimated required power of a bomb to reach the given range. |
| **设置 DynEx 比例** | R_FUN | Set the scale multiplier on dynex explosions. Default 0.5. |
| **重新加载卡牌** | R_DEBUG | Reload all TCG cards. |
| **验证卡牌** | R_DEBUG | Validate the card settings. |
| **测试卡包分发** | R_DEBUG | Test the distribution of a card pack. |
| **输出卡牌列表** | R_DEBUG | Print all cards to chat. |
| **填充世界** | R_DEBUG | Populate the world with test mobs. |
| **调试状态面板** | R_DEBUG | Toggles local debug of the stat panel |
| **发送地图性能分析** | R_DEBUG | View the profile. |
| **生成调试用完整船员** | R_DEBUG | Creates a full crew for the station, flling datacore and assigning min |
| **调试法术需求** | R_DEBUG | View all spells and their requirements. |
| **清除旧版资源缓存** | R_DEBUG | Clears the legacy asset cache, regenerating it immediately (may cause  |
| **清除智能资源缓存** | R_DEBUG | Clear the smart asset cache, causing it to regenerate next round. |
| **打开事件记录器** | R_DEBUG | Open the event logger interface. |
| **高级 ProcCall** | R_DEBUG | Call a proc on any datum in the server. |
| **调试-游戏** | R_DEBUG | Toggles game debugging. |
| **所在位置空气状态** | R_DEBUG | Gets the air status for your current turf. |
| **授予全部权限** | R_DEBUG | Grant full access to a mob. |
| **直接接管控制** | R_ADMIN | Assume direct control of a mob. |
| **修改目标** | R_ADMIN | Modify the station goals for the shift. |
| **调试生物列表** | R_DEBUG | For when you just gotta know. |
| **显示 del() 日志** | R_DEBUG | Display del's log of everything that's passed through it. |
| **显示叠加层日志** | R_DEBUG | Display SSoverlays log of everything that's passed through it. |
| **显示 Initialize() 日志** | R_DEBUG | Displays a list of things that didn't handle Initialize() properly. |
| **色盲测试** | R_DEBUG | Change your view to a budget version of colorblindness to test for usa |
| **编辑/调试渲染平面** | R_DEBUG | Edit and visualize plane masters and their connections (relays). |
| **调试 HUD** | R_DEBUG | Debug the data or antag HUDs. |
| **跳转到废墟** | R_DEBUG | Displays a list of all placed ruins to teleport to. |
| **卸载 CTF** | R_DEBUG | Despawns the majority of CTF. |
| **运行空查询** | R_DEBUG | Runs a specified number of empty queries. |
| **切换寻路测试** | R_DEBUG | Enables/Disables pathfinding testing action buttons |
| **清除动态地块预留** | R_DEBUG | Deallocates all reserved space, restoring it to round start conditions |
| **切换勋章禁用** | R_DEBUG | Toggles the safety lock on trying to contact the medal hub. |
| **查看运行时错误** | R_DEBUG | Opens the runtime viewer. |
| **抽取随机事件** | R_DEBUG | Schedules the event subsystem to fire a new random event immediately.  |
| **重新加载配置** | R_DEBUG | Reloads the configuration from the default path on the disk, wiping an |
| **检查计时器来源** | R_DEBUG | Checks the sources of running timers. |
| **重新连接 TTS** | R_DEBUG | Re-establishes connection to the TTS server if possible |
| **允许浏览器检查** | R_DEBUG | Allow browser debugging via inspect |
| **调试已穿戴物品精灵** | R_DEBUG | We're cancelling the Spritemageddon. (This will create a LOT of runtim |
| **立即运行 Tracy** | R_DEBUG | Start running the byond-tracy profiler immediately |
| **切换下回合 Tracy** | R_DEBUG | Toggle running the byond-tracy profiler next round |
| **调试 MC 依赖** | R_DEBUG | Debug MC dependencies. |
| **为电网线路上色** | R_DEBUG | Colors every node and cable of every powernet in a different color. |
| **统计原子/数据体** | R_DEBUG | Count how many atom or datum instances there are of each type, then ou |
| **解冻卡住的生物** | R_DEBUG | Unfreezes all frozen mobs. |
| **无线电报告** | R_DEBUG | Shows a report of all radio devices and their filters. |
| **设置服务器 FPS** | R_DEBUG | Sets game speed in frames-per-second. Can potentially break the game |
| **操作器官** | R_DEBUG | Manipulate the organs of a living carbon. |
| **地图导出** | R_DEBUG | Select a part of the map by coordinates and download it. |
| **地图模板 - 放置** | R_DEBUG | Place a map template at your current location. |
| **地图模板 - 上传** | R_DEBUG | Upload a map template to the server. |
| **停止所有正在播放的音效** | R_NONE | Stops all playing sounds for EVERYONE. |
| **生成物体-生物** | R_SPAWN | Spawn an object as if it were a mob. |
| **打开 Lua 编辑器** | R_DEBUG | Its codin' time. |
| **SDQL2 查询** | R_DEBUG | Run a SDQL2 query. |

### 3.5 地图 Mapping（26 个）

| 命令 | 权限 | 功能 |
|---|---|---|
| **检查管道** | R_DEBUG | Verifies the integrity of the plumbing network. |
| **检查电力** | R_DEBUG | Verifies the integrity of the power network. |
| **测试区域** | R_DEBUG | Tests the areas for various machinery. |
| **测试区域（仅空间站）** | R_DEBUG | Tests the areas for various machinery on station z-levels. |
| **测试区域（空间站 - 无维护区）** | R_DEBUG | Tests the areas for various machinery on station z-levels, excluding m |
| **测试区域（全部）** | R_DEBUG | Tests the areas for various machinery on all z-levels. |
| **生成废墟** | R_DEBUG | Attempt to randomly place a specific ruin. |
| **摄像头范围显示** | R_DEBUG | Shows the range of cameras on the station. |
| **Dirty Varedits** | R_DEBUG | Shows all dirty varedits. |
| **摄像头报告** | R_DEBUG | Get a printout of all camera issues. |
| **对讲机范围显示** | R_DEBUG | Shows the range of intercoms on the station. |
| **显示地图报告** | R_DEBUG | Displays a list of map reports. |
| **显示回合开始活跃地块列表** | R_DEBUG | Displays a list of active turfs coordinates at roundstart. |
| **显示回合开始活跃地块标记** | R_DEBUG | Places a marker on all active-at-roundstart turfs. |
| **启用地图编辑命令** | R_DEBUG | Enable all mapping verbs. |
| **禁用地图编辑命令** | R_DEBUG | Disable all mapping verbs. |
| **统计 Z 层物体** | R_DEBUG | Counts the number of objects of a certain type on a specific z-level. |
| **统计全部对象** | R_DEBUG | Counts the number of objects of a certain type in the game world. |
| **禁用所有通讯命令** | R_DEBUG | Disables all communication verbs. |
| **生成职位地标图标** | R_DEBUG | Generates job starting location landmarks. |
| **调试 Z 层** | R_DEBUG | Displays a list of all z-levels and their linkages. |
| **统计空间站食物** | R_DEBUG | Counts the number of food items on the station. |
| **统计空间站材料堆** | R_DEBUG | Count the stacks of materials on station. |
| **检查受阻的大气管道** | R_DEBUG | Checks for obstructions on atmospherics machines. |
| **切换灯光调试** | R_DEBUG | Toggles light debug mode. |
| **可视化照明角点** | R_DEBUG | Visualizes the corners of all lights on the station. |

### 3.6 服务器 Server（25 个）

| 命令 | 权限 | 功能 |
|---|---|---|
| **将存档导出为开发者偏好设置** | R_DEBUG | Exports your savefile to be used by any guests that connect to your lo |
| **点唱机上传音乐** | R_SERVER | Upload a valid .ogg file to be accessed via the jukebox. |
| **点唱机浏览音乐** | R_SERVER | Browse music files for moderation. |
| **更换地图** | R_SERVER | Set the next map. |
| **撤销地图投票** | R_SERVER | Revert the map vote, allowing a new vote. |
| **切换恐慌地堡** | R_SERVER | Toggles the panic bunker for the server. |
| **切换恐慌地堡面试** | R_SERVER | Toggle whether new players will be interviewed or blocked from connect |
| **重新连接数据库** | R_NONE | Attempts to (re)establish the DB Connection |
| **切换随机事件** | R_SERVER | Toggles random events on or off. |
| **切换 Hub** | R_SERVER | Toggles the server's visilibility on the BYOND Hub. |
| **重启世界** | R_SERVER | Restarts the world immediately. |
| **取消重启** | R_SERVER | Cancels a pending world reboot. |
| **结束回合** | R_SERVER | Forcibly ends the round and allows the server to restart normally. |
| **切换 OOC** | R_ADMIN | Toggle the OOC channel on or off. |
| **切换死亡 OOC** | R_ADMIN | Toggle the OOC channel for dead players on or off. |
| **切换死者投票** | R_ADMIN | Toggle the vote for dead players on or off. |
| **立即开始** | R_SERVER | Start the round RIGHT NOW. |
| **延迟回合结束** | R_ADMIN | Prevent the server from restarting. |
| **Delay Round End** | R_SERVER | Prevent the server from restarting. |
| **切换允许加入游戏** | R_SERVER | Toggle the ability to enter the game. |
| **切换可选 AI 职位** | R_SERVER | Toggle the ability to choose AI jobs. |
| **切换重生** | R_SERVER | Toggle the ability to respawn. |
| **延迟开局** | R_SERVER | Delay the game start. |
| **设置管理员通知** | R_SERVER | Set an announcement that appears to everyone who joins the server. Onl |
| **切换访客** | R_SERVER | Toggle the ability for guests to enter the game. |

### 3.7 事件 Events（18 个）

| 命令 | 权限 | 功能 |
|---|---|---|
| **传真面板** | R_ADMIN | View and respond to faxes sent to CC. |
| **PDA 消息** | R_ADMIN | Send a message to a user's PDA. |
| **切换自身建造模式** | R_BUILD | Toggle build mode for yourself. |
| **加载/跳转懒加载模板** | R_ADMIN | Loads a lazy template and/or jumps to it. |
| **创建黑市物品** | R_BUILD | Add an item to the black market for purchase. |
| **触发事件** | R_FUN | Forces an event to occur. |
| **全局旁白** | R_ADMIN | Send a direct narration to all connected players. |
| **添加自定义 AI 法则** | R_ADMIN | Add a custom law to the Silicons. |
| **设置警戒等级** | R_ADMIN | Changes the security level. Announcement effects only. |
| **指挥部报告脚注** | R_FUN | Adds a footnote to the roundstart command report. |
| **指挥部报告内容** | R_FUN | Sets the main content of the roundstart command report. |
| **延迟指挥部报告** | R_FUN | Prevents the roundstart command report from being sent; or forces it t |
| **访问新闻播报网络** | R_ADMIN | Allows you to view, add, and edit news feeds. |
| **生成试剂容器** | R_SPAWN | Spawn a reagent container. |
| **更改指挥部名称** | R_ADMIN | Change the name of Central Command. |
| **创建指挥部报告** | R_ADMIN | Create a command report to be sent to the station. |
| **幽灵池保护** | R_ADMIN | Choose which ways people can get into the round, or just clear it out  |
| **法则面板** | R_ADMIN | View the AI laws. |

### 3.8 娱乐 Fun（19 个）

| 命令 | 权限 | 功能 |
|---|---|---|
| **投放炸弹** | R_FUN | Cause an explosion of varying strength at your location |
| **投放 DynEx 炸弹** | R_FUN | Cause an explosion of varying strength at your location. |
| **创建 Mob 蠕虫** | R_FUN | Attach a linked list of mobs to your marked mob. |
| **自我碎尸** | R_ADMIN | Give yourself the same treatment you give others. |
| **自我化尘** | R_ADMIN | Give yourself the same treatment you give others. |
| **随机化所有人** | R_SERVER | Make everyone have a random appearance. |
| **大规模僵尸感染** | R_ADMIN | Infects all humans with a latent organ that will zombify them on death |
| **大规模僵尸解药** | R_ADMIN | Removes the zombie infection from all humans, returning them to normal |
| **全体变形** | R_ADMIN | Applies the effects of the bolt of change to every single mob. |
| **批量修改特质** | R_FUN | Adds or removes a trait from every mob. |
| **过场动画** | R_FUN | Show a cinematic to all players. |
| **召唤 ERT** | R_FUN | Summons an emergency response team. |
| **播放全局音效** | R_SOUND | Play a sound to all connected players. |
| **播放本地音效** | R_SOUND | Plays a sound only you can hear. |
| **直接播放生物音效** | R_SOUND | Play a sound directly to a mob. |
| **播放互联网音效** | R_SOUND | Play a given internet sound to all players. |
| **设置回合结束音效** | R_SOUND | Set the sound that plays on round end. |
| **附身物体** | R_POSSESS | Possess an object. |
| **解除附身** | R_POSSESS | Stop possessing an object. |

### 3.9 穿梭机 Shuttle（6 个）

| 命令 | 权限 | 功能 |
|---|---|---|
| **呼叫穿梭机** | R_ADMIN | Force a shuttle call with additional modifiers. |
| **取消穿梭机** | R_ADMIN | Recall the shuttle, regardless of circumstances. |
| **禁用穿梭机** | R_ADMIN | Those fuckers aren't getting out. |
| **启用穿梭机** | R_ADMIN | Those fuckers ARE getting out. |
| **敌对环境** | R_ADMIN | Disable the shuttle, naturally. |
| **穿梭机操纵器** | R_ADMIN | Opens the shuttle manipulator UI. |

### 3.10 配置 Profile（3 个）

| 命令 | 权限 | 功能 |
|---|---|---|
| **开始行性能分析** | R_DEBUG | Starts tracking line by line profiling for code lines that support it. |
| **停止行性能分析** | R_DEBUG | Stops tracking line by line profiling for code lines that support it. |
| **显示行性能分析** | R_DEBUG | Shows tracked profiling info from code lines that support it. |

### 3.11 NOVA 追加（30 个）

| 命令 | 权限 | 分类 | 功能 |
|---|---|---|---|
| **切换死亡聊天** | R_ADMIN | 服务器 | Toggle dis bitch. |
| **切换反派 OOC** | R_ADMIN | 服务器 | Toggles Antag OOC. |
| **修复说话** | R_ADMIN | 主 | Fix say for the players. |
| **loudAsay** | R_NONE | 主 | Send a message to other admins (loudly). |
| **管理玩家等级** | R_PERMISSIONS | 主 | Manage who has the special player ranks while the server is  |
| **导入偏好设置** | R_ADMIN | 主 | Upload a character preferences JSON file to the server. |
| **切换安保 OOC** | R_ADMIN | 服务器 | Toggles Security OOC. |
| **测试区域生成器** | R_DEBUG | 调试 | Show area spawner placement candidates as an overlay. |
| **切换 BSA 控制** | R_ADMIN | 娱乐 | Toggles the BSA control lock on and off. |
| **生成生物生成器** | R_ADMIN | 娱乐 | Spawns a mob spawner structure at your location. |
| **解离紧急停止** | R_ADMIN | 事件 | Activate the delam suppression system. |
| **解离抑制开关** | R_FUN | 事件 | Disable/enable the delam suppression system. |
| **事件生成器菜单** | R_ADMIN | 事件 | Event Spawners Menu. |
| **ICES 事件面板** | R_FUN | 事件 | Opens up the ICES panel. |
| **重新加载互动** | R_DEBUG | 调试 | Force reload interactions. |
| **生成液体** | R_ADMIN | 娱乐 | Spawns an amount of chosen liquid at your current location. |
| **移除液体** | R_ADMIN | 游戏 | Removes all liquids in specified radius. |
| **液体组颜色调试** | R_DEBUG | 调试 | Liquid Groups Color Debug. |
| **Lorecaster 故事** | R_ADMIN | 事件 | Open the Lorecaster Story Manager. |
| **征集 OPFOR 申请** | R_FUN | 娱乐 | Request players sign up for opfor if they have antag on. |
| **查看 OPFOR 申请** | R_ADMIN | 游戏 | View OPFORs. |
| **添加恐慌地堡豁免** | R_ADMIN | 主 | Allows a given ckey to connect despite the panic bunker for  |
| **撤销恐慌地堡豁免** | R_ADMIN | 主 | Revoke's a ckey's permission to bypass the panic bunker for  |
| **生成污染** | R_ADMIN | 娱乐 | Spawns an amount of chosen pollutant at your current locatio |
| **标题画面：更改** | R_FUN | 娱乐 | Upload a new titlescreen image. |
| **标题画面：设置公告** | R_FUN | 娱乐 | Sets a titlescreen notice, a big red text on the main screen |
| **修复大厅屏幕** | R_ADMIN | 主 | Lobbyscreen broke? Press this. |
| **标题画面：设置 HTML** | R_DEBUG | 娱乐 | Change lobby screen HTML on the go. |
| **添加白名单** | R_ADMIN | 主 | Adds a given ckey to the whitelist, allowing them access to  |
| **Revoke Whitelist** | R_BAN | 主 | Revokes a given's ckey's whitelist access, effectively preve |


# 第四卷 · SDQL2 与高级工具

## 4.1 天罚全录（35 个主类型 · 源码全量）

**代码**: `admin/smites/`（32 文件）+ NOVA 追加（smites.dm/pie.dm/bark.dm）

| 天罚 | 效果 |
|---|---|
| **Bad Luck 厄运** | 坏运气降临 |
| **:B:erforate 穿孔** | 全身穿孔 |
| **:B:erforate 穿孔（神圣版）** | 全身穿孔+神圣特效 |
| **:B:loodless 无血** | 抽干血液 |
| **:B:oneless 无骨** | 移除骨骼 |
| **脑损伤 Brain Damage** | 施加脑创伤 |
| **蓝空间炮 BSA** | Bluespace 炮打击 |
| **小丑化血 Clownify Blood** | 血液变为小丑物质 |
| **巴别塔诅咒 Curse of Babel** | 目标语言混乱 |
| **虚拟朋友 Imaginary Friend** | 召唤幽灵朋友（特殊版） |
| **靠港费 Dock Pay** | 从目标账户扣款 |
| **灰飞 Dust** | 化为灰烬 |
| **灰飞 Dust（神圣版）** | 化灰+神圣特效 |
| **假 bwoink Fake Bwoink** | 伪造管理员消息 |
| **增肥 Fatten Up** | 目标变胖 |
| **火球 Fireball** | 火球打击 |
| **幽灵控制 Ghost Control** | 幽灵附身操控 |
| **碎尸 Gib** | 肢解目标 |
| **碎尸 Gib（神圣版）** | 肢解+神圣特效 |
| **打击数字 Hitsplat** | 伤害数字弹出 |
| **打击数字叠加 Stackout** | Hitsplat 叠加版 |
| **沉浸 Fully Immerse** | 液体浸泡 |
| **系鞋带 Knot Shoes** | 鞋带打结 |
| **闪电 Lightning Bolt** | 雷击 |
| **闪电 Lightning Bolt（神圣版）** | 雷击+神圣特效 |
| **金块 Nugget** | 变为金块 |
| **物体化 Become Object** | 变为物体 |
| **物体化 Become Object（神圣版）** | 变物体+神圣特效 |
| **恐惧症 Ocky Icky Phobia** | 施加恐惧症 |
| **石化 Petrify** | 石化目标 |
| **石化 Petrify（神圣版）** | 石化+神圣特效 |
| **拼图 Puzzgrid** | 困入拼图网格 |
| **拼图 Puzzle** | 困入滑动拼图 |
| **回溯 Retcon** | 抹除目标存在痕迹 |
| **不可移动之棒 Immovable Rod** | 棒击 |
| **疤痕 Scarify** | 全身伤疤 |
| **补给舱 Supply Pod** | 补给舱砸落 |
| **补给舱快速版 Supply Pod Quick** | 快速补给舱砸落 |
| **锥形耻辱帽 Cone of Shame（NOVA）** | 锥形耻辱帽+神圣版 |
| **女仆化 Maid-ification（NOVA）** | 变为女仆 |
| **派 Pie（NOVA）** | 派糊脸 |
| **搞怪片段 Normal Blooper（NOVA）** | 播放搞怪片段 |


> **数字核账（2026-08-06）**：源码 `smites/` 目录 32 文件 − 基类 `_smite.dm` = **31 个主 smite**，NOVA 追加 4 个（cone_of_shame 锥形耻辱帽/maidification 女仆化/pie 派/normalblooper 搞怪片段）= **35 主类型**。此前文档的 44 是把 6 个 divine 子类型（berforate/objectify/dust/gib/lightning/petrify）+ stackout 子类型 + NOVA divine 变体重复计数所致。面板 `GLOB.smites`（`init_smites()` 遍历全 subtype）实际条目为 **43**，但独立主类型为 35。

## 4.2 SDQL2 数据库语言

**代码**: `admin/verbs/SDQL2/`（2,237 行）

## 4.3 变量查看（VV）

**代码**: `admin/view_variables/`（2,754 行）

## 4.4 Lua 脚本

**代码**: `admin/verbs/lua/`（616 行）——Lua 管理脚本。



## 4.5 SDQL2 数据库语言

### 四种查询

| 查询 | 语法 | 说明 |
|---|---|---|
| **SELECT** | `SELECT <选择器>` | 选择对象并列出 |
| **CALL** | `CALL <进程> ON <选择器>` | 对每个对象调用进程 |
| **UPDATE** | `UPDATE <选择器> SET 变量=值` | 批量改变量 |
| **DELETE** | `DELETE <选择器>` | 删除对象 |
| EXPLAIN | `EXPLAIN <查询>` | 打印解析树 |

### 选择器语法

| 语法 | 说明 |
|---|---|
| 类型路径 | `/mob`、`*` 通配符 |
| IN/FROM | 指定来源（全局列表） |
| WHERE | 过滤条件 |
| MAP | 替换为表达式结果（链式） |
| @[] | 子查询 |

### 常用查询示例

| 用途 | 示例 |
|---|---|
| 列出 mob | `SELECT /mob` |
| 指定层 | `SELECT /mob WHERE z == 4` |
| SMES 电量 | `SELECT /obj/machinery/power/smes MAP [charge / capacity * 100, RCon_tag, src]` |
| **批量删除蜘蛛** | `DELETE /mob/living/.../giant_spider WHERE loc.loc == marked` |
| **批量 gib** | `CALL gib() ON /mob/.../giant_spider WHERE get_area(src) == marked` |
| **批量传送** | `CALL forceMove(marked) ON /mob/living/carbon/superior_animal` |
| 批量改颜色 | `UPDATE /mob WHERE client SET client.color = [...]` |

### 特殊变量

`src`（当前对象）/`usr`（你的 mob）/`marked`（标记 datum）/`global`/`world`/`SS*`（子系统）

### USING 选项

PROCCALL（ASYNC/BLOCKING）/SELECT（FORCE/SKIP_NULLS）/PRIORITY（HIGH/NORMAL）/AUTOGC/SEQUENTIAL

### 内置进程包装（90+ 个，`_` 前缀）

数学（_abs/_rand/_prob）、文本（_ckey/_uppertext/_md5）、位置（_range/_viewers/_locate）、移动（_walk/_step）、列表（_list_add/_pick）、外观（_new/_animate/_flick）、特征（_has_trait）、客户端（_winset）

## 4.6 变量查看（VV）

| 机制 | 值 |
|---|---|
| **界面** | VV 窗口（debug_variables） |
| 变量类型 | 25 种 |
| **动作** | 60+（删除/组件/过滤器/粒子/矩阵/ProcCall） |
| 批量修改 | 多对象变量 |
| 权限锁 | 部分变量锁定（VV 权限） |
| 引用追踪 | 标记/标签/删除 |

## 4.7 生成面板

| 机制 | 值 |
|---|---|
| **TGUI SpawnPanel** | 对象树/图标预览/数量/偏移 |
| 生成位置 | 8 种 |
| 精准模式 | Target-Mark-Copy 三种 |
| 补给舱投送 | 可投送 |
| 旧版 | SpawnSearch 菜单 |

## 4.8 Lua 脚本

| 机制 | 值 |
|---|---|
| **DreamLuau** | Lua 编辑器（R_DEBUG） |
| 多 State | 日志/任务/全局表 |
| **5 秒限制** | 执行超时 |
| 安全包装 | _new/变量读写走 VV/进程调用走 WrapAdminProcCall |

## 4.9 NOVA 修改

①admin_delete 蓝移火花+delete_sparks 偏好 ②20 处功能性修改（工单/白名单 SQL 化）③237 处标记绝大多数为 I18N。



## 4.10 管理面板群全录（17 个 · 源码全量）

**源码**: `code/modules/admin/` 面板文件群。此前文档只覆盖动词/天罚/SDQL2/VV/Lua，这些交互面板全部未写，现补全。

| 面板 | 文件 | 行数 | 功能 |
|---|---|---|---|
| **反派面板** | antag_panel.dm | 227 | 浏览/管理所有反派玩家（换阵营/移除/查看目标） |
| **团队面板** | team_panel.dm | 148 | 团队管理（创建/编辑/移除队伍、成员管理） |
| **玩家面板** | player_panel.dm | 339 | 玩家总览（在线/离线/记录/操作入口） |
| **配装管理器** | outfit_manager.dm | 64 | 保存/加载/分享自定义配装（outfit） |
| **配装编辑器** | outfit_editor.dm | 207 | 可视化编辑 outfit 的每件装备/槽位 |
| **奖杯管理器** | trophy_manager.dm | 59 | 管理站点奖杯（颁发/回收） |
| **小号查询** | known_alts.dm | 186 | 查询玩家的已知小号列表 |
| **白名单** | whitelist.dm | 43 | 白名单管理（核心文件已被 NOVA 注释移除→SQL 化在 `modular_nova/modules/whitelist/code/whitelist.dm`，NOVA 版为 `add_whitelist`） |
| **音效发射器** | sound_emitter.dm | 156 | 向全服/区域播放自定义音效 |
| **调色板改色** | greyscale_modify_menu.dm | 367 | 可视化修改物品的 greyscale 颜色参数 |
| **创建生物** | create_mob.dm | 74 | 快速创建指定类型生物（含参数） |
| **生成菜单** | spawn_menu.dm | 92 | 物品/生物生成菜单（路径搜索） |
| **技能面板** | skill_panel.dm | 59 | 管理玩家技能等级 |
| **调查工具** | admin_investigate.dm | 68 | 调查事件/日志汇总 |
| **传真面板** | admin_fax_panel.dm | 145 | 管理站内传真收发 |
| **PDA 消息** | admin_pda_message.dm | 93 | 以管理员身份向玩家 PDA 发消息 |
| **强制事件** | force_event.dm | 95 | 手动触发任意游戏事件（见下） |
| 附属：标签 | tag.dm | 105 | 玩家标签管理 |

### force_event 强制事件机制

`ADMIN_VERB(force_event, R_FUN)` → TGUI "ForceEvent" 面板：遍历 `SSevents.control` 按 12 分类展示所有事件；forceevent 动作 → `text2path` 定位 → 可勾选 announce（100%/0% 播报覆盖）→ 有 `admin_setup` 则逐个 `prompt_admins()`（返回 CANCEL 中止）→ `run_event(admin_forced=TRUE)` → 全服/日志广播。

### 动词数字核账（2026-08-06）

- **源码真实数量**：核心纯 `ADMIN_VERB` **241** 个 + 右键专用 9 个 = 250；NOVA **32** 个（此前文档写 30，漏 `migrate_player_ranks` 和 `admin_stasis`）→ 全宏 **282** 个
- **漏掉的可见动词 14 个**：惩戒(admin_smite)、大气控制面板、更改穿梭机事件、生成电网、删除全部/强制/硬、生成职位配置、装束管理器、运行天气、停止天气、切换 CDN、启用/禁用视野、切换核弹
- **HIDDEN 类（ADMIN_CATEGORY_HIDDEN）22 个**：纯 15 个（给予/移除动作、给予/移除法术、给予疾病、授予 AI 控制器、给予随机 AI 发言、解除监禁、丢下所有物品、爆炸、电磁脉冲、碎尸、显示赛博格面板、制造赛博格、调试空气状态）+ 右键混合 7 个（OOC 发言、私密/耳机消息、本地/直接旁白、删除、检查内容物、调整组件评级）


# 附录 · 代码路径索引

| 系统 | 文件 | 行数 |
|---|---|---|
| 权限持有者 | `admin/holder2.dm` | 523 |
| 权限标志 | `__DEFINES/admin.dm` | — |
| 权限编辑 | `admin/permissionedit.dm` | 1,216 |
| **封禁系统** | `admin/sql_ban_system.dm` | 1,130 |
| 粘性封禁 | `admin/stickyban.dm` | 486 |
| 消息系统 | `admin/sql_message_system.dm` | 756 |
| 投票管理 | `admin/poll_management.dm` | 721 |
| 事件日志 | `admin/event_logger.dm` | 552 |
| topic | `admin/topic.dm` | 1,449 |
| 管理员动词 | `admin/admin_verbs.dm` | 919 |
| **动词命令** | `admin/verbs/` | **14,691** |
| **变量查看** | `admin/view_variables/` | **2,754** |
| **天罚** | `admin/smites.dm` | 924 |
| 生成面板 | `admin/spawn_panel.dm` | 522 |
| 调用进程 | `admin/callproc.dm` | 299 |
| **SDQL2** | `admin/verbs/SDQL2/` | **2,237** |
| Lua 脚本 | `admin/verbs/lua/` | 616 |
