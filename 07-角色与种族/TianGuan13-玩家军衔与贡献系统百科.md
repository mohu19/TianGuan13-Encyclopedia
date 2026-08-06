# TianGuan13 玩家军衔与贡献系统百科

> 源码模块：`modular_nova/modules/player_ranks/`（共 7 个文件，968 行）
> Source module: `modular_nova/modules/player_ranks/` (7 files, 968 lines total)
> 分支：NovaSector ｜ 适用代码库：TianGuan13（基于 NovaSector / TGStation 系 BYOND SS13 代码库）
> 本文档为全量提取：模块内每一个宏、变量、过程（proc）、权限检查、报错文案均被收录。

---

## 目录 Table of Contents

- [1. 模块总览 Overview](#1-模块总览-overview)
- [2. 文件清单 File Inventory](#2-文件清单-file-inventory)
- [3. 子系统：player_ranks.dm Subsystem](#3-子系统player_ranksdm-subsystem)
  - [3.1 宏定义 Defines](#31-宏定义-defines)
  - [3.2 子系统定义 Subsystem Definition](#32-子系统定义-subsystem-definition)
  - [3.3 资格检查 Qualification Checks](#33-资格检查-qualification-checks)
  - [3.4 加载逻辑 Loading Logic](#34-加载逻辑-loading-logic)
  - [3.5 偏好同步 Preferences Sync](#35-偏好同步-preferences-sync)
  - [3.6 分组管理 Group Management](#36-分组管理-group-management)
  - [3.7 SQL 写入 SQL Writes](#37-sql-写入-sql-writes)
  - [3.8 旧系统迁移 Legacy Migration](#38-旧系统迁移-legacy-migration)
- [4. 基础控制器 _player_rank_controller.dm Base Controller](#4-基础控制器-_player_rank_controllerdm-base-controller)
  - [4.1 变量 Variables](#41-变量-variables)
  - [4.2 添加与移除 Add / Remove](#42-添加与移除-add--remove)
  - [4.3 加载与保存 Load / Save](#43-加载与保存-load--save)
  - [4.4 迁移辅助与保护 Migration Helpers & Guards](#44-迁移辅助与保护-migration-helpers--guards)
- [5. 捐赠者控制器 donator_controller.dm Donator Controller](#5-捐赠者控制器-donator_controllerdm-donator-controller)
- [6. 导师控制器 mentor_controller.dm Mentor Controller](#6-导师控制器-mentor_controllerdm-mentor-controller)
- [7. 新星玩家控制器 nova_star_controller.dm Nova Star Controller](#7-新星玩家控制器-nova_star_controllerdm-nova-star-controller)
- [8. 偏好设置 preferences.dm Preferences](#8-偏好设置-preferencesdm-preferences)
- [9. 服务器集成 world_topic.dm World Topic (Discord)](#9-服务器集成-world_topicdm-world-topic-discord)
- [10. 配置项 Config Entries](#10-配置项-config-entries)
- [附录 · 导师系统（Mentor）全录](#附录--导师系统mentor全录)
- [11. 交叉引用 Cross-References](#11-交叉引用-cross-references)

---

## 1. 模块总览 Overview

**中文**：`player_ranks` 模块是 TianGuan13（NovaSector 分支）的"玩家军衔与贡献系统"。它统一管理三类玩家特殊身份（军衔/贡献等级）：**捐赠者（Donator）**、**导师（Mentor）**、**新星玩家（Nova Star）**。系统同时支持两套存储后端：**传统文件系统（legacy）**（`config/nova/*.txt` 文本文件）与 **SQL 数据库系统**（`player_rank` 表）。模块提供统一抽象层 `/datum/player_rank_controller`（每个军衔一个控制器子类），由 `SSplayer_ranks` 子系统负责加载、增删、迁移；并提供资格查询过程（`is_donator` / `is_mentor` / `is_nova_star`）、捐赠者偏好同步（`donator_status`、存档槽位上限 `MAX_SAVE_SLOTS_SUBSCRIBER`）以及 Discord 世界话题命令 `set_player_rank`（通过 comms key 远程授予/撤销军衔）。

**English**: The `player_ranks` module is TianGuan13's (NovaSector branch) "player rank & contribution system". It centrally manages three special player identities (ranks/contribution tiers): **Donator**, **Mentor**, and **Nova Star**. The system supports two storage backends: the **legacy file system** (`config/nova/*.txt` text files) and the **SQL database system** (the `player_rank` table). The module provides a unified abstraction datum `/datum/player_rank_controller` (one controller subtype per rank), loaded/managed by the `SSplayer_ranks` subsystem, plus qualification procs (`is_donator` / `is_mentor` / `is_nova_star`), donator preference sync (`donator_status`, save-slot cap `MAX_SAVE_SLOTS_SUBSCRIBER`), and a Discord world-topic command `set_player_rank` (remote grant/revoke via comms key).

### 1.1 三种军衔一览 Three Ranks at a Glance

| 军衔 Rank | rank_title（数据库值 DB value） | 传统文件 Legacy file | 全局列表 Global list | 资格查询 Qualification |
|---|---|---|---|---|
| 捐赠者 Donator | `"donator"` | `config/nova/donators.txt` | `GLOB.donator_list` | `SSplayer_ranks.is_donator(user)` |
| 导师 Mentor | `"mentor"` | `config/nova/mentors.txt` | `GLOB.mentor_datums` / `GLOB.mentors`（mentor 模块） | `SSplayer_ranks.is_mentor(user)` → `user.is_mentor()` |
| 新星玩家 Nova Star | `"nova_star"` | `config/nova/nova_star_players.txt` | `GLOB.nova_star_list` | `SSplayer_ranks.is_nova_star(user)` |

---

## 2. 文件清单 File Inventory

| # | 文件 File | 行数 Lines | 职责 Responsibility |
|---|---|---|---|
| 1 | `code/subsystem/player_ranks.dm` | 499 | 子系统：加载、资格检查、分组增删、SQL、迁移（Subsystem: loading, checks, group add/remove, SQL, migration） |
| 2 | `code/player_rank_controller/_player_rank_controller.dm` | 227 | 基础控制器抽象层（Base controller abstraction layer） |
| 3 | `code/player_rank_controller/donator_controller.dm` | 49 | 捐赠者控制器（Donator controller） |
| 4 | `code/player_rank_controller/mentor_controller.dm` | 76 | 导师控制器（Mentor controller） |
| 5 | `code/player_rank_controller/nova_star_controller.dm` | 47 | 新星玩家控制器（Nova Star controller） |
| 6 | `code/preferences.dm` | 3 | 偏好扩展：`donator_status` 变量（Preferences extension: `donator_status` var） |
| 7 | `code/world_topic.dm` | 67 | Discord 世界话题命令 `set_player_rank`（World topic command） |
| — | **合计 Total** | **968** | — |

---

## 3. 子系统：player_ranks.dm Subsystem

> 源码路径 Source path: `modular_nova/modules/player_ranks/code/subsystem/player_ranks.dm`（499 行）

### 3.1 宏定义 Defines

| 宏 Define | 值 Value | 说明 Description |
|---|---|---|
| `PLAYER_RANK_TABLE_NAME` | `"player_rank"` | 数据库中存储玩家军衔的表名。表结构见 `nova_schema.sql`。The database table name holding player ranks; see `nova_schema.sql` for its schema. |
| `INDEX_CKEY` | `1` | 军衔查询结果行中 ckey 的列索引。The index of the ckey column in a query row for player ranks. |
| `LEGACY_MIGRATION_ADMIN_CKEY` | `"LEGACY"` | 传统系统迁移时写入数据库的 `admin_ckey` 占位名。The `admin_ckey` value recorded for legacy migrations. |

### 3.2 子系统定义 Subsystem Definition

**中文**：`SUBSYSTEM_DEF(player_ranks)` —— 名为 `"Player Ranks"` 的子系统，特征如下：

- `ss_flags = SS_NO_FIRE`：不参与周期性火焰（无 tick 处理，纯初始化/被动服务）。
- `init_stage = INITSTAGE_EARLY`：早期初始化阶段。
- `dependencies = list(/datum/controller/subsystem/server_maint)`：依赖服务器维护子系统（server_maint）先行初始化。
- 三个控制器变量（均指向对应军衔控制器实例）：
  - `var/datum/player_rank_controller/donator/donator_controller`
  - `var/datum/player_rank_controller/mentor/mentor_controller`
  - `var/datum/player_rank_controller/nova_star/nova_star_controller`

**English**: `SUBSYSTEM_DEF(player_ranks)` — subsystem named `"Player Ranks"`:
- `ss_flags = SS_NO_FIRE`: no periodic firing (pure initialization/passive service).
- `init_stage = INITSTAGE_EARLY`: early init stage.
- `dependencies = list(/datum/controller/subsystem/server_maint)`: depends on the server_maint subsystem.
- Three controller vars (one instance per rank): `donator_controller`, `mentor_controller`, `nova_star_controller`.

**Initialize()**：依次调用 `load_donators()`、`load_mentors()`、`load_nova_stars()`，返回 `SS_INIT_SUCCESS`。若被 `IsAdminAdvancedProcCall()` 拦截则直接返回（不做任何加载）。

**English Initialize()**: calls `load_donators()`, `load_mentors()`, `load_nova_stars()` in order, returns `SS_INIT_SUCCESS`; bails out entirely if `IsAdminAdvancedProcCall()`.

**Destroy()**：用 `QDEL_NULL` 依次销毁 `donator_controller`、`mentor_controller`、`nova_star_controller`，然后调用父类销毁。

**English Destroy()**: `QDEL_NULL`s all three controllers in order, then calls parent.

### 3.3 资格检查 Qualification Checks

三个公开过程用于判定某客户端（client）是否具备某军衔。**共同点**：`user` 必须是 `/client`，否则 `CRASH`；均支持 `admin_bypass`（默认 `TRUE`，管理员即使没有该军衔也判定通过）。**所有过程均先检查 `IsAdminAdvancedProcCall()`**，防止管理员借"高级过程调用"绕过权限检查。

Three public procs determine whether a client holds a rank. **Common behavior**: `user` must be a `/client` or the proc `CRASH`es; all support `admin_bypass` (default `TRUE` — admins pass even without the actual rank). **All procs check `IsAdminAdvancedProcCall()` first** so admins cannot bypass permission checks via advanced proc calls.

#### `proc/is_donator(client/user, admin_bypass = TRUE)` —— 是否为捐赠者

判定顺序 Check order：
1. `GLOB.donator_list[user.ckey]` 为真 → `TRUE`（在捐赠者列表内）。
2. `admin_bypass && is_admin(user)` → `TRUE`（管理员绕过）。
3. 否则 `FALSE`。

失败时 CRASH 文案：`"Invalid user type provided to is_donator(), expected 'client' and obtained '[user ? user.type : "null"]'."`

#### `proc/is_mentor(client/user, admin_bypass = TRUE)` —— 是否为导师

- 对 `user.is_mentor(admin_bypass)`（client 上的 `is_mentor()` 过程）的空指针安全包装（null check wrapper）。
- 失败时 CRASH 文案：`"Invalid user type provided to is_mentor(), expected 'client' and obtained '[user ? user.type : "null"]'."`

#### `proc/is_nova_star(client/user, admin_bypass = TRUE)` —— 是否为新星玩家

判定顺序 Check order：
1. `GLOB.nova_star_list[user.ckey]` 为真 → `TRUE`。
2. `admin_bypass && is_admin(user)` → `TRUE`。
3. 否则 `FALSE`。

失败时 CRASH 文案：`"Invalid user type provided to is_nova_star(), expected 'client' and obtained '[user ? user.type : "null"]'."`

### 3.4 加载逻辑 Loading Logic

三个 `load_*()` 过程结构完全一致（均 `PROTECTED_PROC(TRUE)` + `IsAdminAdvancedProcCall()` 拦截）：

1. 实例化对应控制器（`donator_controller = new` 等）。
2. 若对应配置旗标开启（如 `CONFIG_GET(flag/donator_legacy_system)`）→ 走传统系统 `controller.load_legacy()`（捐赠者额外调用 `update_all_prefs_donator_status()`），返回。
3. 若 `!SSdbcore.Connect()`（数据库连接失败）→ 记录 `log_config` + `log_game` + `message_admins`（文案 `"Failed to connect to database in load_donators(). Reverting to legacy system."` 等，按军衔替换函数名），`CONFIG_SET` 把对应 `*_legacy_system` 旗标置 `TRUE`，并 `controller.load_legacy()` 回退。
4. 否则走 SQL：`load_player_rank_sql(controller)`（捐赠者额外同步偏好）。

| 过程 Proc | 配置旗标 Config flag | 数据库连接失败文案 DB-failure message |
|---|---|---|
| `load_donators()` | `flag/donator_legacy_system` | `"Failed to connect to database in load_donators(). Reverting to legacy system."` |
| `load_mentors()` | `flag/mentor_legacy_system` | `"Failed to connect to database in load_mentors(). Reverting to legacy system."` |
| `load_nova_stars()` | `flag/nova_star_legacy_system` | `"Failed to connect to database in load_nova_stars(). Reverting to legacy system."` |

#### `proc/load_player_rank_sql(datum/player_rank_controller/rank_controller)`（PROTECTED_PROC）

- 执行 SQL：`SELECT ckey FROM player_rank WHERE deleted = 0 AND rank = :rank`（参数 `rank` = 控制器的 `rank_title`，表名经 `format_table_name()` 处理）。
- `warn_execute()` 失败则销毁查询并返回。
- 成功后调用 `rank_controller.load_from_query(query)` 填充内存数据，随后 `qdel` 查询。

### 3.5 偏好同步 Preferences Sync

#### `proc/update_all_prefs_donator_status()`

遍历 `GLOB.preferences_datums`，对每个偏好数据（prefs datum）调用 `update_prefs_donator_status()`。用于捐赠者加载完成后一次性刷新所有在线玩家的偏好。

Iterates all `GLOB.preferences_datums` and refreshes each via `update_prefs_donator_status()` — runs once after donators finish loading.

#### `proc/update_prefs_donator_status(datum/preferences/prefs)`

对单个偏好数据执行：
1. `prefs.unlock_content = !!prefs.parent.IsByondMember()` —— 内容解锁跟随 BYOND 会员状态。
2. `prefs.donator_status = is_donator(prefs.parent)` —— 捐赠者状态按资格检查结果写入。
3. 若 `unlock_content` 或 `donator_status` 任一为真 → `prefs.max_save_slots = MAX_SAVE_SLOTS_SUBSCRIBER`（**100 个存档槽位**，见 §10；非会员/非捐赠者默认槽位为 8）。

For a single prefs datum: (1) `unlock_content` follows BYOND membership; (2) `donator_status` is set from `is_donator(prefs.parent)`; (3) if either is true, `max_save_slots = MAX_SAVE_SLOTS_SUBSCRIBER` (**100 save slots**, see §10; the default non-subscriber cap is 8).

### 3.6 分组管理 Group Management

#### `proc/get_controller_for_group(rank_title)`（PROTECTED_PROC）

- 将 `rank_title` 规范化为小写并把空格替换为下划线：`LOWER_TEXT(replacetext(rank_title, " ", "_"))`。
- 依次与 `donator_controller.rank_title`、`mentor_controller.rank_title`、`nova_star_controller.rank_title` 比较，命中即返回对应控制器。
- 全部不匹配 → `CRASH("Invalid player_rank_controller \"[rank_title || "*null*"]\" used in get_controller_for_group()!")`。
- 注释说明：switch() 无法使用非常量值，故用 if 链。

#### `proc/add_player_to_group(admin, ckey, rank_title)`

**中文**：把 ckey 加入指定军衔组（数据库或传统系统）。流程：

1. `IsAdminAdvancedProcCall()` 拦截 → `FALSE`。
2. 参数空值检查：`ckey`、`admin`、`rank_title` 任一缺失 → `stack_trace("Missing either ckey ([ckey || "*NULL*"]), admin ([admin || "*NULL*"]) or rank_title ([rank_title || "*NULL*"]) in add_player_to_group()! Fix this ASAP!")` 并返回 `FALSE`。
3. 解析 `admin`：若为 `/client` 取其 `holder`；否则若为 `/datum/admins` 直接使用。拿不到 `admin_holder` → `FALSE`。
4. **权限检查**：`admin_holder.check_for_rights(R_PERMISSIONS)` 失败 → 客户端调用者收到警告 `LANG("datum.8063b60d", null)`（权限不足文案），返回 `FALSE`。
5. 规范化 `rank_title`（小写、空格转下划线）→ `get_controller_for_group()`；取不到控制器 → `stack_trace` + `FALSE`。
6. `ckey = ckey(ckey)` 规范化。
7. 查重：`already_in_config = controller.get_ckeys_for_legacy_save()`，若 `already_in_config[ckey]` 已存在 → 客户端调用者收到 `LANG("datum.d962eb81", list(ckey, rank_title))`（"已在组内"文案），返回 `FALSE`。
8. 写入：若 `controller.should_use_legacy_system()` → `controller.add_player_legacy(ckey)`，返回 `TRUE`；否则 → `add_player_rank_sql(controller, ckey, admin_holder.target)`。

**English**: Adds a ckey to the given rank group (DB or legacy). Flow: `IsAdminAdvancedProcCall()` guard → null-arg check (stack_trace + FALSE) → resolve `admin` to a `/datum/admins` holder (client `.holder` or direct datum; none → FALSE) → **permission check** `check_for_rights(R_PERMISSIONS)` (client caller gets warning `LANG("datum.8063b60d")`, returns FALSE) → normalize rank_title → resolve controller (invalid → stack_trace + FALSE) → normalize ckey → duplicate check against `get_ckeys_for_legacy_save()` (already present → warning `LANG("datum.d962eb81")` with ckey+rank, FALSE) → legacy write via `add_player_legacy()` or SQL via `add_player_rank_sql(controller, ckey, admin_holder.target)`.

#### `proc/remove_player_from_group(admin, ckey, rank_title)`

与 `add_player_to_group` 完全镜像（同一套空值检查、`R_PERMISSIONS` 权限检查、控制器解析、`LANG` 文案），仅最后一步不同：

- 若 `controller.should_use_legacy_system()` → `controller.remove_player_legacy(ckey)`，返回 `TRUE`；
- 否则 → `remove_player_rank_sql(controller, ckey, admin_holder.target)`。

**English**: Exact mirror of `add_player_to_group` (same null checks, `R_PERMISSIONS` check, controller resolution, `LANG` strings); only the final step differs: legacy → `remove_player_legacy(ckey)` / TRUE, else → `remove_player_rank_sql(controller, ckey, admin_holder.target)`.

### 3.7 SQL 写入 SQL Writes

#### `proc/add_player_rank_sql(controller, ckey, admin_ckey)`（PROTECTED_PROC）

```sql
INSERT INTO player_rank (ckey, rank, admin_ckey) VALUES(:ckey, :rank, :admin_ckey)
ON DUPLICATE KEY UPDATE deleted = 0, admin_ckey = :admin_ckey
```

- `warn_execute()` 失败 → 销毁查询、返回 `FALSE`。
- 成功 → `controller.add_player(ckey)`（内存中生效）→ `qdel` 查询 → `TRUE`。
- 说明：`ON DUPLICATE KEY UPDATE` 使重复条目被"复活"（`deleted` 归零）并记录新的操作管理员。

#### `proc/remove_player_rank_sql(controller, ckey, admin_ckey)`（PROTECTED_PROC）

```sql
UPDATE player_rank SET deleted = 1, admin_ckey = :admin_ckey WHERE ckey = :ckey AND rank = :rank
```

- 软删除（`deleted = 1`，保留历史记录并记录操作管理员）。
- `warn_execute()` 失败 → `FALSE`；成功 → `controller.remove_player(ckey)` → `qdel` → `TRUE`。

### 3.8 旧系统迁移 Legacy Migration

#### `proc/migrate_player_rank_to_sql(client/admin, rank_title)`

- 入口：管理员从游戏内动词调用。权限要求：`check_rights_for(admin, R_PERMISSIONS | R_DEBUG | R_SERVER)`（**三种权限同时具备**），不足则收到 `LANG("datum.8063b60d", null)` 警告并返回。
- 解析控制器 → `migrate_player_rank_to_sql_from_controller(controller)`。

#### `proc/migrate_player_rank_to_sql_from_controller(controller)`（PROTECTED_PROC）

1. `ckeys_to_migrate = controller.get_ckeys_to_migrate()`（读取传统文件中的全部 ckey）。
2. 查询数据库已有条目：`SELECT ckey FROM player_rank WHERE rank = :rank`，逐个 `ckeys_to_migrate -= ckey`（**显式不检查 deleted 状态**，只求绝对避免重复插入）。
3. 组装待插入行：`list("ckey" = ckey, "rank" = controller.rank_title, "admin_ckey" = LEGACY_MIGRATION_ADMIN_CKEY)`，即 `admin_ckey = "LEGACY"`。
4. `log_config("Migrating [length(rows_to_insert)] entries from \the [controller.rank_title] legacy system to the SQL-based system.")`
5. `SSdbcore.MassInsert(format_table_name(PLAYER_RANK_TABLE_NAME), rows_to_insert, warn = TRUE)` 批量写入。

---

## 4. 基础控制器 _player_rank_controller.dm Base Controller

> 源码路径 Source path: `modular_nova/modules/player_ranks/code/player_rank_controller/_player_rank_controller.dm`（227 行）
> 顶层宏：`#define INDEX_CKEY 1`（查询行中 ckey 的列索引，与子系统中的宏同值）

**中文**：`/datum/player_rank_controller` 是抽象基类，用于把"每种军衔各自如何增删玩家、如何维护全局列表、以及传统系统的增删/读写/迁移"统一封装。所有过程都内建 `IsAdminAdvancedProcCall()` 防护（管理员无法借高级过程调用绕过权限或清空数据）。未实现的关键过程直接 `CRASH` 以强制子类补齐。

**English**: `/datum/player_rank_controller` is an abstract base class abstracting how each rank adds/removes players from its global lists and handles legacy add/remove/load/save. Every proc has built-in `IsAdminAdvancedProcCall()` guards. Unimplemented critical procs `CRASH` to force subtypes to implement them.

### 4.1 变量 Variables

| 变量 Var | 默认值 Default | 说明 Description |
|---|---|---|
| `rank_title` | `null` | 数据库中的军衔名称。**子类必须设置**，否则会引发严重问题（subtype **MUST** set this or you WILL hit severe issues）。 |
| `legacy_file_path` | `null` | 存放该军衔全部玩家的传统文件路径。应在 `New()` 中设置（编译期非常量值）。Set in `New()` since it has a non-constant compile-time value. |
| `legacy_file_header` | `""` | 传统文件头注释（如有）；没有则留 `""`。Leave `""` if none. |

`vv_edit_var(var_name, var_value)`：**总是返回 `FALSE`** —— 控制器变量视作受保护配置，禁止 VV 修改。

### 4.2 添加与移除 Add / Remove

| 过程 Proc | 标记 Flags | 行为 Behavior |
|---|---|---|
| `add_player(ckey)` | `SHOULD_CALL_PARENT(FALSE)` | 抽象方法：在游戏内给 ckey 授予军衔（**不负责持久化**，持久化由调用方处理）。未实现 → `CRASH("[src] did not implement add_player()! Fix this ASAP!")`。 |
| `add_player_legacy(ckey)` | `SHOULD_NOT_OVERRIDE(TRUE)` | 便捷封装：`IsAdminAdvancedProcCall()` 拦截 → `add_player(ckey)` → `text2file(ckey, legacy_file_path)`（追加写入传统文件）。勿覆写。 |
| `remove_player(ckey)` | `SHOULD_CALL_PARENT(FALSE)` | 抽象方法：在游戏内移除军衔（不负责持久化）。未实现 → `CRASH("[src] did not implement remove_player()! Fix this ASAP!")`。 |
| `remove_player_legacy(ckey)` | `SHOULD_NOT_OVERRIDE(TRUE)` | 便捷封装：`IsAdminAdvancedProcCall()` 拦截 → `remove_player(ckey)` → `save_legacy()`（因列表任意位置删除，需整体重写文件）。勿覆写。 |

### 4.3 加载与保存 Load / Save

| 过程 Proc | 标记 Flags | 行为 Behavior |
|---|---|---|
| `load_from_query(datum/db_query/query)` | — | 从已执行的数据库查询加载：`IsAdminAdvancedProcCall()` 拦截 → `clear_existing_rank_data()` → 循环 `query.NextRow()`，取 `ckey(query.item[INDEX_CKEY])` 逐个 `add_player()`。 |
| `load_legacy()` | `SHOULD_NOT_OVERRIDE(TRUE)` | 从传统文件加载：拦截 → `clear_existing_rank_data()` → `for(var/line in world.file2list(legacy_file_path))`，跳过空行与以 `#` 开头的注释行 → 逐个 `add_player(line)`。返回 `TRUE`。 |
| `save_legacy()` | `SHOULD_NOT_OVERRIDE(TRUE)` | 保存到传统文件：拦截 → 内容以 `legacy_file_header` 开头 → `for(var/player in get_ckeys_for_legacy_save())` 逐行追加 `player + "\n"` → `rustg_file_write(save_file_contents, legacy_file_path)` 整文件覆盖写入。 |

### 4.4 迁移辅助与保护 Migration Helpers & Guards

| 过程 Proc | 标记 Flags | 行为 Behavior |
|---|---|---|
| `get_ckeys_to_migrate()` | `SHOULD_NOT_OVERRIDE(TRUE)`，`RETURN_TYPE(/list)` | 读取传统文件，返回应迁移到数据库的全部 ckey（字符串列表）：跳过空行与 `#` 注释行，`ckey(line)` 规范化，无效则跳过。 |
| `get_ckeys_for_legacy_save()` | `SHOULD_CALL_PARENT(FALSE)`，`RETURN_TYPE(/list)` | 子类覆写：返回保存传统文件时应写入的 ckey 列表（可过滤）。未实现 → `CRASH("[src] did not implement get_ckeys_for_legacy_save()! Fix this ASAP!")`。 |
| `should_use_legacy_system()` | `SHOULD_CALL_PARENT(FALSE)` | 子类覆写：是否使用传统系统（通常返回对应配置旗标）。未覆写 → 默认 `TRUE` 并 `stack_trace` 警告。 |
| `clear_existing_rank_data()` | `SHOULD_CALL_PARENT(FALSE)`，`PROTECTED_PROC(TRUE)` | 子类覆写：加载前清空/初始化所需列表。未实现 → `CRASH("[src] did not implement clear_existing_rank_data()! Fix this ASAP!")`。 |

**安全注释（源码原文要点）**：`add_player` / `remove_player` / `get_ckeys_for_legacy_save` / `clear_existing_rank_data` 的覆写**必须**加 `IsAdminAdvancedProcCall()` 检查，否则管理员可借高级过程调用跳过权限检查、把自己写入传统保存，或清空军衔数据。

---

## 5. 捐赠者控制器 donator_controller.dm Donator Controller

> 源码路径 Source path: `modular_nova/modules/player_ranks/code/player_rank_controller/donator_controller.dm`（49 行）

**中文**：捐赠者（Donator）= 支持服务器的贡献者，获得特殊装载（loadout）物品等权益。捐赠者列表以**关联列表**存储（`GLOB.donator_list[ckey] = TRUE`，注释注明"为了更快的速度"），并由 `GLOBAL_PROTECT` 保护全局变量。

**English**: Donators support the server and receive cool loadout items. The donator list is stored as an **associative list** (`GLOB.donator_list[ckey] = TRUE`, "Associative list for extra SPEED!") and is `GLOBAL_PROTECT`ed.

### 5.1 全局变量 Global Vars

| 定义 Definition | 说明 Description |
|---|---|
| `GLOBAL_LIST_EMPTY(donator_list)` | 全部捐赠者 ckey 的关联列表（ckey → TRUE）。Associative list of all donator ckeys. |
| `GLOBAL_PROTECT(donator_list)` | 全局变量受保护（禁止未授权修改）。Protected global. |

### 5.2 类型定义 Type Definition

- `rank_title = "donator"`（数据库中的军衔名）。
- `legacy_file_header`：超长文件头注释（源码注释自嘲"没错，这长得离谱，忍着点——为了保住顶部那行可爱的小注释"）：

```text
###############################################################################################
# List for people who support us! They get cool loadout items                                 #
# Case is not important for ckey.                                                             #
###############################################################################################
```

- `New()`：`legacy_file_path = "[global.config.directory]/nova/donators.txt"`。

### 5.3 过程 Procs

| 过程 Proc | 行为 Behavior |
|---|---|
| `add_player(ckey)` | 拦截检查 → `ckey = ckey(ckey)` 规范化 → `GLOB.donator_list[ckey] = TRUE`。 |
| `remove_player(ckey)` | 拦截检查 → `GLOB.donator_list -= ckey`。 |
| `get_ckeys_for_legacy_save()` | 拦截检查 → 返回 `GLOB.donator_list`（整个列表直接保存）。 |
| `should_use_legacy_system()` | 返回 `CONFIG_GET(flag/donator_legacy_system)`。 |
| `clear_existing_rank_data()` | 拦截检查 → `GLOB.donator_list = list()`（重置为空列表）。 |

---

## 6. 导师控制器 mentor_controller.dm Mentor Controller

> 源码路径 Source path: `modular_nova/modules/player_ranks/code/player_rank_controller/mentor_controller.dm`（76 行）

**中文**：导师系统比其他军衔更复杂，其 `GLOB.mentor_datums` / `GLOB.mentors` 全局列表与 `remove_mentor_verbs()` 等逻辑定义在独立的 `mentor` 模块（`modular_nova/modules/mentor/`，见 §11）。本控制器只负责把 ckey 加入/移出导师体系。

**English**: The mentor system is more complex than the other ranks; its `GLOB.mentor_datums` / `GLOB.mentors` global lists and `remove_mentor_verbs()` live in the separate `mentor` module (see §11). This controller only bridges ckeys in/out of the mentor system.

### 6.1 类型定义 Type Definition

- `rank_title = "mentor"`。
- `New()`：`legacy_file_path = "[global.config.directory]/nova/mentors.txt"`。

### 6.2 过程 Procs

| 过程 Proc | 行为 Behavior |
|---|---|
| `add_player(ckey)` | 拦截检查 → `ckey = ckey(ckey)` → `new /datum/mentors(ckey)`（创建导师 datum，挂入 `GLOB.mentor_datums`）。 |
| `remove_player(ckey)` | 拦截检查 → `GLOB.mentor_datums[ckey]?.remove_mentor()`（空安全移除导师）。 |
| `get_ckeys_for_legacy_save()` | 拦截检查 → **只保存原本就在配置文件中的导师**（避免把全体管理员写进配置文件，那会难以维护）；不保存新加入 `GLOB.mentor_datums` 的导师（它们已在 `add_player_legacy()` 时写入过）。实现：`world.file2list(legacy_file_path)` 逐行读取，跳过空行与 `#` 注释行，`ckey(line)` 规范化，**仅当该 ckey 仍存在于 `GLOB.mentor_datums` 时**才以关联列表形式加入 `mentors_to_save[existing_mentor] = TRUE`。 |
| `should_use_legacy_system()` | 返回 `CONFIG_GET(flag/mentor_legacy_system)`。 |
| `clear_existing_rank_data()` | 拦截检查 → `GLOB.mentor_datums.Cut()` 清空导师 datum 列表 → 遍历 `GLOB.mentors` 中每个 client：`ex_mentor.remove_mentor_verbs()`（移除导师动词）并 `ex_mentor.mentor_datum = null` → `GLOB.mentors.Cut()` 清空在线导师列表。 |

---

## 7. 新星玩家控制器 nova_star_controller.dm Nova Star Controller

> 源码路径 Source path: `modular_nova/modules/player_ranks/code/player_rank_controller/nova_star_controller.dm`（47 行）

**中文**：新星玩家（Nova Star）= 服务器"明星玩家"贡献等级。结构与捐赠者控制器几乎相同：关联列表 + 全局保护。

**English**: Nova Star is the "star player" contribution tier. Structurally nearly identical to the donator controller: associative list + global protection.

### 7.1 全局变量 Global Vars

| 定义 Definition | 说明 Description |
|---|---|
| `GLOBAL_LIST_EMPTY(nova_star_list)` | 全部新星玩家 ckey 的关联列表（ckey → TRUE）。Associative list of all Nova Star ckeys. |
| `GLOBAL_PROTECT(nova_star_list)` | 全局变量受保护。Protected global. |

### 7.2 类型定义 Type Definition

- `rank_title = "nova_star"`（注意：带下划线，数据库中的军衔名）。
- `New()`：`legacy_file_path = "[global.config.directory]/nova/nova_star_players.txt"`。

### 7.3 过程 Procs

| 过程 Proc | 行为 Behavior |
|---|---|
| `add_player(ckey)` | 拦截检查 → `ckey = ckey(ckey)` → `GLOB.nova_star_list[ckey] = TRUE`。 |
| `remove_player(ckey)` | 拦截检查 → `GLOB.nova_star_list -= ckey`。 |
| `get_ckeys_for_legacy_save()` | 拦截检查 → 返回 `GLOB.nova_star_list`。 |
| `should_use_legacy_system()` | 返回 `CONFIG_GET(flag/nova_star_legacy_system)`。 |
| `clear_existing_rank_data()` | 拦截检查 → `GLOB.nova_star_list = list()`。 |

---

## 8. 偏好设置 preferences.dm Preferences

> 源码路径 Source path: `modular_nova/modules/player_ranks/code/preferences.dm`（3 行）

```dm
/datum/preferences
	/// Does this member have donator status on the server
	var/donator_status = FALSE
```

**中文**：在 `/datum/preferences`（玩家偏好数据）上扩展一个布尔变量 `donator_status`（默认 `FALSE`），表示该成员在服务器上是否拥有捐赠者身份。该值由子系统 `update_prefs_donator_status()` 写入（见 §3.5），并结合 `unlock_content` 决定 `max_save_slots` 是否提升至 `MAX_SAVE_SLOTS_SUBSCRIBER`（100）。

**English**: Extends `/datum/preferences` with boolean `donator_status` (default `FALSE`) — whether this member has donator status on the server. It is written by `update_prefs_donator_status()` (see §3.5), and together with `unlock_content` decides whether `max_save_slots` is raised to `MAX_SAVE_SLOTS_SUBSCRIBER` (100).

---

## 9. 服务器集成 world_topic.dm World Topic (Discord)

> 源码路径 Source path: `modular_nova/modules/player_ranks/code/world_topic.dm`（67 行）

**中文**：定义 `/datum/world_topic/set_player_rank` —— 供外部服务（Discord 机器人）通过世界话题（world topic）远程授予/撤销玩家军衔。

**English**: Defines `/datum/world_topic/set_player_rank` — lets an external service (Discord bot) grant/revoke player ranks remotely via world topic.

### 9.1 定义 Definition

| 字段 Field | 值 Value | 说明 Description |
|---|---|---|
| `keyword` | `"set_player_rank"` | 世界话题关键字。 |
| `require_comms_key` | `TRUE` | 必须携带通信密钥（comms key）才可调用。 |

### 9.2 `Run(list/input)` 执行流程 Execution Flow

输入参数（input 键）：`sender_discord_id`（发送者 Discord ID）、`target_ckey`（目标玩家 ckey）、`target_rank`（目标军衔）、`desired_rank_status`（期望状态，1=授予 / 0=撤销）。返回 JSON 风格关联列表 `.["success"]` + `.["message"]`。

| 步骤 Step | 检查 Check | 失败返回 Failure response |
|---|---|---|
| 1 | `sender_discord_id` 是否存在 | `success = FALSE`；`message = "Invalid sender Discord ID, this should not be happening! Report this immediately!"` |
| 2 | `target_ckey = ckey(input["target_ckey"])` 是否有效 | `success = FALSE`；`message = "Invalid target ckey provided."` |
| 3 | `sender_ckey = ckey(SSdiscord.lookup_ckey(sender_discord_id))`（Discord 账号是否绑定 ckey） | `success = FALSE`；`message = "No ckey was found to be attached to the provided Discord account ID, **[sender_discord_id]**. Please verify your Discord account following the instructions of the in-game verb before trying this command again."` |
| 4 | `linked_admin_holder = GLOB.admin_datums[sender_ckey] \|\| GLOB.deadmins[sender_ckey]`（是否持有管理员 datum，含离职管理员表） | `success = FALSE`；`message = "No valid admin datum was found associated with the ckey associated to your Discord account."` |
| 5 | `linked_admin_holder.check_for_rights(R_PERMISSIONS)`（权限检查） | `success = FALSE`；`message = "You do not possess the permissions to execute this command."` |
| 6 | `target_rank = input["target_rank"]` 是否存在 | `success = FALSE`；`message = "Invalid target rank provided."` |
| 7 | 规范化军衔：`target_rank = full_capitalize(replacetext(target_rank, "_", " "))`（下划线转空格、首字母大写，如 `nova_star` → `Nova Star`） | — |
| 8 | `desired_rank_status = !!text2num(input["desired_rank_status"])`（转为布尔） | — |
| 9a | 状态为真 → `SSplayer_ranks.add_player_to_group(linked_admin_holder, target_ckey, target_rank)` | `success = !!result`；成功文案：`"**[linked_admin_holder.target]** successfully added **[target_rank]** status to **[target_ckey]**."`；失败文案：`"**[linked_admin_holder.target]** was unable to add **[target_rank]** status to **[target_ckey]**. Please verify that you entered their ckey correctly and that they did not already possess that status before trying again. Use the in-game verb to get more information if you keep on receiving this error."` |
| 9b | 状态为假 → `SSplayer_ranks.remove_player_from_group(linked_admin_holder, target_ckey, target_rank)` | `success = !!result`；成功文案：`"**[linked_admin_holder.target]** successfully removed **[target_rank]** status from **[target_ckey]**."`；失败文案：`"**[linked_admin_holder.target]** was unable to remove **[target_rank]** status from **[target_ckey]**. Please verify that you entered their ckey correctly and that they did possess that status before trying again. Use the in-game verb to get more information if you keep on receiving this error."` |

**附加行为**：9a/9b 之后均调用 `message_admins(replacetect(.["message"], "*", ""))` —— 把消息中的 `*`（Markdown 粗体标记）剥掉后广播给全体管理员。管理员 `target`（其 ckey）会作为 `admin_ckey` 记录进数据库（经 `add_player_to_group` / `remove_player_from_group` 链路）。

**Extra behavior**: after 9a/9b, `message_admins(replacetext(.["message"], "*", ""))` broadcasts the result to all admins with Markdown bold markers (`*`) stripped. The admin's `target` (their ckey) is recorded as `admin_ckey` in the database through the `add_player_to_group` / `remove_player_from_group` chain.

---

## 10. 配置项 Config Entries

> 相关源码：`modular_nova/master_files/code/controllers/configuration/entries/config_entries.dm`（模块外，供参考）

| 配置项 Config entry | 类型 Type | 保护 Protection | 说明 Description |
|---|---|---|---|
| `donator_legacy_system` | flag | `CONFIG_ENTRY_LOCKED` | 服务器是否使用传统捐赠者系统（`donators.txt`）而非 SQL 系统。Whether the server uses the legacy donator system (`donators.txt`) or the SQL system. |
| `mentor_legacy_system` | flag | `CONFIG_ENTRY_LOCKED` | 是否使用传统导师系统（`mentors.txt`）而非 SQL 系统。Legacy mentor system vs SQL. |
| `nova_star_legacy_system` | flag | `CONFIG_ENTRY_LOCKED` | 是否使用传统新星系统（`nova_star_players.txt`）而非 SQL 系统。Legacy star system vs SQL. |
| `mentors_mobname_only` | flag | — | 导师是否只显示 mob 名而非 ckey（mentor 模块配置，此处列出供参考）。Whether mentors see mobnames only. |

**存档槽位 Save slots**（`code/__DEFINES/~nova_defines/preferences.dm:37`）：

- `#define MAX_SAVE_SLOTS_SUBSCRIBER 100` —— 订阅者/捐赠者存档槽位上限为 **100**。
- 普通玩家默认上限为 **8**（`code/modules/client/preferences.dm:730`，NOVA EDIT：`max_save_slots = MAX_SAVE_SLOTS_SUBSCRIBER` 覆盖原值 8）。

---

## 附录 · 导师系统（Mentor）全录

> 源码模块：`modular_nova/modules/mentor/`（**11 个 .dm 文件，452 行**；另有 readme.md）
> Source module: `modular_nova/modules/mentor/` (11 .dm files, 452 lines; plus readme.md)
> 说明：本附录为 §6 / §11 交叉引用处的 mentor 模块**全量展开**。导师（Mentor）是服务器中帮助新玩家解答机制问题的资深玩家角色——玩家可向导师发送类似 ahelp 的求助消息（readme 原文：*"Adds a mentor system, allowing for players to send a-help like messages to mentors, asking them about game mechanics and help."*）。致谢 Credits：**Azarak**（移植与调整 Porting, tweaks）、**Poojawa**（实现 Implementation）。关联 TG 过程修改 TG Proc Changes：`code/modules/client/client_procs.dm > client/Topic()`、`/code/modules/admin/secrets.dm > /datum/admins/proc/Secrets_topic(), /datum/admins/proc/Secrets()`。
>
> **English**: This appendix fully expands the `mentor` module referenced in §6/§11. Mentors are veteran players who help newcomers with game mechanics — players can send a-help-like messages to mentors (readme: *"Adds a mentor system, allowing for players to send a-help like messages to mentors..."*). Credits: Azarak (porting, tweaks), Poojawa (implementation).

### A.1 文件清单 File Inventory

| # | 文件 File | 行数 Lines | 职责 Responsibility |
|---|---|---|---|
| 1 | `code/mentor.dm` | 74 | 导师 datum 核心：`/datum/mentors` 定义、构造/移除、HREF 鉴权、token 过程与 /client 扩展变量（Core datum, New/remove_mentor/CheckMentorHREF, tokens） |
| 2 | `code/mentorhelp.dm` | 102 | 求助动词 `mentorhelp`（30 秒冷却）、`get_mentor_counts()` 统计、`key_name_mentor()` 名称格式化 |
| 3 | `code/mentorpm.dm` | 93 | 导师私聊 `cmd_mentor_pm` 与面板 `cmd_mentor_pm_panel`（已弃用） |
| 4 | `code/mentorsay.dm` | 20 | 导师频道发言 `cmd_mentor_say`（MSAY，管理员/导师双色渲染） |
| 5 | `code/mentorwho.dm` | 22 | 在线导师列表 `mentorwho`（含状态后缀与 AFK 标记） |
| 6 | `code/dementor.dm` | 23 | 撤销导师 `cmd_mentor_dementor` / 恢复 `cmd_mentor_rementor` |
| 7 | `code/follow.dm` | 28 | 跟随玩家 `mentor_follow` / `mentor_unfollow` |
| 8 | `code/logging.dm` | 15 | 导师日志 `log_mentor` / 日志浏览器 `MentorLogSecret` |
| 9 | `code/mentor_verbs.dm` | 12 | 导师动词集 `GLOB.mentor_verbs` 与 `add_mentor_verbs` / `remove_mentor_verbs` |
| 10 | `code/client_procs.dm` | 60 | client 生命周期挂载：`New()`/`Destroy()`、Topic 分发 `client_procs()`、`mentor_datum_set()`、`is_mentor()` |
| 11 | `code/_globalvars.dm` | 3 | `GLOB.mentors`（在线导师 client 列表） |
| — | **合计 Total** | **452** | — |

### A.2 全局变量 Global Vars

| 定义 Definition | 位置 Location | 说明 Description |
|---|---|---|
| `GLOBAL_LIST_EMPTY(mentors)` + `GLOBAL_PROTECT(mentors)` | `_globalvars.dm` | 当前**在线且处于导师状态**的 client 关联列表（client → TRUE）。Associative list of all mentor clients. |
| `GLOBAL_LIST_EMPTY(mentor_datums)` + `GLOBAL_PROTECT(mentor_datums)` | `mentor.dm` | ckey → `/datum/mentors` 的导师数据关联列表（含离线导师）。Associative list of ckey → mentor datum. |
| `GLOBAL_VAR_INIT(mentor_href_token, GenerateToken())` + `GLOBAL_PROTECT(mentor_href_token)` | `mentor.dm` | 全局导师 HREF token（启动时生成）；导师个人 token 之外的第二重鉴权依据。Global mentor href token. |
| `GLOBAL_LIST_EMPTY(mentorlog)` + `GLOBAL_PROTECT(mentorlog)` | `logging.dm` | 本轮导师日志列表（`log_mentor()` 写入）。Per-round mentor log. |
| `GLOBAL_LIST_INIT(mentor_verbs, list(/client/proc/cmd_mentor_say, /client/proc/cmd_mentor_dementor))` + `GLOBAL_PROTECT(mentor_verbs)` | `mentor_verbs.dm` | 导师动词集——**恰好两个动词**：`cmd_mentor_say`（导师发言）与 `cmd_mentor_dementor`（撤销导师）。The mentor verb set — exactly two verbs. |

### A.3 导师数据 /datum/mentors（mentor.dm，74 行）

**中文**：`/datum/mentors` 是导师的身份载体 datum（对导师而言相当于管理员系统中的 admin holder）。每个导师 ckey 一个实例，注册进 `GLOB.mentor_datums[ckey]`；只有**非管理员**的导师会被加入 `GLOB.mentors`（管理员不显示在导师列表中）。

**English**: `/datum/mentors` is the identity datum of a mentor (to mentors what the admin-datum holder is to admins). One instance per mentor ckey, registered in `GLOB.mentor_datums[ckey]`; only **non-admin** mentors are added to `GLOB.mentors` (admins are excluded from the mentor list).

#### 变量 Variables

| 变量 Var | 默认值 Default | 说明 Description |
|---|---|---|
| `name` | `"someone's mentor datum"` | datum 显示名；`New()` 时改为 `"[ckey]'s mentor datum"`。 |
| `owner` | `null` | 实际导师本人，`/client` 类型。The actual mentor, client type. |
| `target` | `null` | 导师的 ckey（已规范化）。The mentor's ckey. |
| `href_token` | `null` | 导师命令的 HREF token，**与管理员共用同一套 token 机制**（`GenerateToken()` 生成）。href token for mentor commands, uses the same token used by admins. |
| `following` | `null` | 当前被该导师跟随的 mob（`/mob`）。Currently followed mob. |

#### `New(ckey)` 构造流程

1. 无 ckey → `QDEL_IN(src, 0)` + `CRASH("Mentor datum created without a ckey")`。
2. `target = ckey(ckey)`；`name = "[ckey]'s mentor datum"`；`href_token = GenerateToken()`。
3. `GLOB.mentor_datums[target] = src`（注册进全局表）。
4. `owner = GLOB.directory[ckey]`（查在线 client）；若在线：
   - `owner.mentor_datum = src`（挂载到 client）；
   - `owner.add_mentor_verbs()`（授予导师动词）；
   - `if(!check_rights_for(owner, R_ADMIN, 0))`（非管理员）→ `GLOB.mentors[owner] = TRUE`（**管理员不入导师列表**）。

#### `remove_mentor()` 移除流程

1. 若 `owner` 存在：`owner.remove_mentor_verbs()`（回收动词）→ `GLOB.mentors -= owner` → `owner.mentor_datum = null` → `owner = null`。
2. `log_admin_private("[target] was removed from the rank of mentor.")`（管理员私密日志）。
3. `GLOB.mentor_datums -= target`（注销）→ `qdel(src)`。

#### `CheckMentorHREF(href, href_list)` HREF 鉴权

- `auth = href_list["mentor_token"]`；`. = auth && (auth == href_token || auth == GLOB.mentor_href_token)`——个人 token 或全局 token 任一匹配即通过。
- 通过 → 返回 `TRUE`。
- 失败 → `message_admins("[key_name_admin(usr)] clicked an href with [msg] authorization key!")`，其中 `msg` 为 `"no"`（未携带 token）或 `"a bad"`（token 错误）。
- 若 `CONFIG_GET(flag/debug_admin_hrefs)` 开启（调试模式）→ `message_admins("Debug mode enabled, call not blocked. Please ask your coders to review this round's logs.")` + `log_world("UAH: [href]")` + 返回 `TRUE`（放行并留日志）。
- 否则 → `log_admin_private("[key_name(usr)] clicked an href with [msg] authorization key! [href]")`，返回空（拦截）。

#### Token 过程（/proc，全量）

- `RawMentorHrefToken(forceGlobal = FALSE)`：`tok = GLOB.mentor_href_token`；若 `!forceGlobal && usr` → 取 `C = usr.client`（源码此处原样保留两行调试残留：`to_chat(world, C)` 与 `to_chat(world, usr)`，无实际作用）→ 无 C 则 `CRASH("No client for HrefToken()!")` → 若 `C.mentor_datum` 存在则 `tok = holder.href_token`（**个人 token 优先**）；返回 `tok`。
- `MentorHrefToken(forceGlobal = FALSE)`：返回 `"mentor_token=[RawMentorHrefToken(forceGlobal)]"`——可直接拼入 href 的键值对。

#### /client 扩展变量（mentor.dm 尾部）

| 变量 Var | 说明 Description |
|---|---|
| `var/datum/mentors/mentor_datum` | 与 admin 的 holder 同理：持有导师 datum。**若非空，该 client 就是导师。** Acts the same way holder does towards admin: it holds the mentor datum; if set, the guy's a mentor. |
| `var/relay_tip_shown` | 本会话是否已向该 client 提示过 relay（防重复提示）。Whether the relay has been suggested to them this session. |

### A.4 通讯流程 Communication Flow

#### A.4.1 mentorhelp —— 求助（mentorhelp.dm，102 行）

`/client/verb/mentorhelp(msg as text)`（category = `"Mentor"`，name = `"导师帮助"`）：

1. 无 `msg` → 直接返回。
2. **30 秒冷却**：`remove_verb(src, /client/verb/mentorhelp)` 暂时移除动词 → `spawn(30 SECONDS)` 后 `add_verb(src, /client/verb/mentorhelp)` 加回（源码注释吐槽：*"Gotta love BYOND, god this is disgusting"*）。
3. `msg = sanitize(copytext_char(msg, 1, MAX_MESSAGE_LEN))`（清理 HTML + 截断至 `MAX_MESSAGE_LEN`）；空或 `!mob` → 返回。
4. `show_char = CONFIG_GET(flag/mentors_mobname_only)`。
5. 组消息：`span_mentor("<b>MENTORHELP:</b> <b>[key_name_mentor(src, TRUE, FALSE, TRUE, show_char)]</b>: [msg]")`（含链接 + 跟随链接 F）。
6. `log_mentor("MENTORHELP: [key_name_mentor(src, FALSE, FALSE, FALSE, FALSE)]: [msg]")`。
7. 遍历 `GLOB.mentors`：每个在线导师 `SEND_SOUND(mentor_client, 'sound/items/bikehorn.ogg')`（自行车喇叭提示音）+ `to_chat(mentor_client, mentor_msg)`。
8. 求助者本人收到 `span_mentor(LANG("client.25d147d7", list(msg)))`。

辅助过程：
- `get_mentor_counts()`：返回 `list("total" = 0, "afk" = 0, "present" = 0)`；遍历 `GLOB.mentors`——`total++`；`mentor_client.is_afk()` → `afk++`，否则 `present++`。
- `key_name_mentor(whom, include_link = null, include_name = FALSE, include_follow = FALSE, char_name_only = FALSE)`——导师名称格式化（全量规则）：
  - 无 `whom` → `"*null*"`；类型不是 /client、/mob、文本 → `"*invalid*"`。
  - 解析 `target_mob` / `target_client` / `key` / `ckey`（文本入参经 `GLOB.directory[ckey]` 反查在线 client）。
  - 无 `ckey` → 强制 `include_link = FALSE`。
  - `include_link` 时：`mentors_mobname_only` 开启则 href 用 `REF(target_mob)`，否则用 `ckey`；均附带 `[MentorHrefToken(TRUE)]`（强制全局 token）。
  - 显示名：`target_client.holder.fakekey`（隐身管理员）→ `"Administrator"`；`char_name_only && mentors_mobname_only` → 大厅（`/mob/dead/new_player`）或观察者（`/mob/dead/observer`）显示 `key`，进行中的玩家显示 `target_client.mob.name`，兜底显示 `key`；其余情况显示 `key`。
  - 无在线 client → 追加 `"\[DC\]"`（断线标记）。
  - `include_follow` → 追加跟随链接：`" (<a href='byond://?_src_=mentor;mentor_follow=[REF(target_mob)];[MentorHrefToken(TRUE)]'>F</a>)"`。
  - 无 key → `"*no key*"`。

#### A.4.2 mentorpm —— 导师私聊（mentorpm.dm，93 行）

- `cmd_mentor_pm_panel()`（category `"Mentor"`，name `"导师私信"`）：非导师 → `span_danger(LANG("client.d0d80bb7", null))`；收集全部在线 client 为 `targets` 关联列表 → `sort_list(targets)` 排序 → `input` 弹窗选择目标 → `cmd_mentor_pm(targets[target], null)` → `SSblackbox.record_feedback("tally", "Mentor_verb", TRUE, "APM")`。**源码注释声明该面板为弃用死代码**：*"We're not using this and I'm debating removing the code as it's dead and useless. We don't need mentors PMing people out of the blue."*
- `cmd_mentor_pm(whom, msg)` —— 核心私聊过程（`src` 发送方 → `target` 接收方）：
  1. 解析 `whom`：`ismob` → `mob_target.client`；`istext` → `GLOB.directory[whom]`；`istype /client` → 直接用。
  2. 无 `target`：发送方是导师 → `span_danger(LANG("client.4abe29ec", null))`；否则（普通玩家回复时导师已离线）→ 转 `mentorhelp(msg)`。
  3. `is_mentor(whom)`（接收方是导师）→ 先广播：`to_chat(GLOB.mentors, span_purple(span_mentor(LANG("client.fe41380a", list(src, whom)))))`。
  4. 无 `msg` → `tgui_input_text(src, LANG("client.008d3052", null), LANG("client.cb7a2c2f", null), max_length = MAX_MESSAGE_LEN)` 弹窗输入。
  5. 输入后仍无 `msg`：若接收方是导师 → 广播取消通知 `span_mentor(span_purple(LANG("client.ccaf83b5", list(src, whom))))`；返回。
  6. 输入后 `target` 丢失：发送方是导师 → 警告 `LANG("client.4abe29ec")`；否则 → `mentorhelp(msg)`（兜底转求助）。
  7. **双方都不是导师 → 直接返回**（禁止非导师互发导师 PM）。
  8. `log_mentor("Mentor PM: [key_name(src)]->[key_name(target)]: [msg]")`。
  9. `msg = emoji_parse(msg)`；`SEND_SOUND(target, 'sound/items/bikehorn.ogg')`。
  10. 收发组合着色（均以 `span_mentor` 包裹）：双方都是导师 → 接收方 `span_purple(LANG("client.4f79da08", ...))`、发送方 `span_blue(LANG("client.04a36481", ...))`；仅接收方是导师（玩家→导师）→ 接收方 `span_purple(LANG("client.0339519f", ...))`、发送方 `span_blue`；仅发送方是导师（导师→玩家）→ 接收方 `span_purple(LANG("client.4f79da08", ...))`、发送方 `span_blue`。
  11. 广播给**其他**导师（排除收发双方，`mentor?.key != key && mentor?.key != target.key`）：`"<B>Mentor PM: [key_name_mentor(src, mentor, FALSE, FALSE, show_char_sender)]-&gt;[key_name_mentor(target, mentor, FALSE, FALSE, show_char_recip)]:</B> [span_blue(msg)]"`；其中 `show_char_sender = !is_mentor() && CONFIG_GET(flag/mentors_mobname_only)`、`show_char_recip = !target.is_mentor() && CONFIG_GET(flag/mentors_mobname_only)`（非导师一方显示角色名）。

#### A.4.3 mentorsay —— 导师频道发言（mentorsay.dm，20 行）

`/client/proc/cmd_mentor_say(msg as text)`（category `"Mentor"`，name `"导师发言"`，`hidden = 1`——不显示在帮助菜单；源码注释：改短名字为 "msay" 便于输入）：

1. `!is_mentor()` → 返回。
2. `msg = copytext_char(sanitize(msg), 1, MAX_MESSAGE_LEN)`；空 → 返回。
3. `msg = emoji_parse(msg)`；`log_mentor("MSAY: [key_name(src)] : [msg]")`。
4. 着色：管理员（`check_rights_for(src, R_ADMIN, 0)`）→ 紫色 `#8A2BE2`；普通导师 → 粉紫 `#E236D8`。格式：`span_mentor("<b><font color ='...'><span class='prefix'>MENTOR:</span> <EM>[key_name(src, 0, 0)]</EM>: <span class='message'>[msg]</span></font></b>")`。
5. 发送对象：`GLOB.admins | GLOB.mentors`（管理员 ∪ 导师），`avoid_highlighting = (mentor == src)`（自己不高亮）。

#### A.4.4 mentorwho —— 在线导师列表（mentorwho.dm，22 行）

`/client/verb/mentorwho()`（category `"Mentor"`，name `"导师在线"`）：

1. 标题 `"<b>Current Mentors:</b>\n"`。
2. 遍历 `GLOB.mentors`：client 为 null（源码注释：*"weird runtime that happens randomly"*）→ `GLOB.mentors -= C` 并 `continue`。
3. **仅当调用者持有管理员 holder**（`if(holder)`——即管理员调用时）附加状态后缀：观察者（`isobserver`）→ `" - Observing"`；大厅（`/mob/dead/new_player`）→ `" - Lobby"`；其他 → `" - Playing"`；`C.is_afk()` → 再追加 `" (AFK)"`。普通导师调用不显示状态后缀。
4. 每行 `span_infoplain("\t[C][suffix]\n")`；最终 `to_chat(src, msg)`。

### A.5 撤销 / 跟随 / 日志（dementor.dm / follow.dm / logging.dm）

#### dementor.dm（23 行）—— 撤销与恢复导师

- `cmd_mentor_dementor()`（category `"Mentor"`，name `"dementor"`）：
  1. `!is_mentor()` → 返回。
  2. `remove_mentor_verbs()`（回收动词）。
  3. 若 verbs 中仍有 `mentor_unfollow`（`/client/proc/mentor_unfollow in verbs`）→ `mentor_unfollow()`（先停止跟随）。
  4. `GLOB.mentors -= src`（移出在线导师列表；**保留 `mentor_datum`**，故操作可逆）。
  5. `to_chat(src, span_interface(LANG("client.5d9efa79", null)))`。
  6. `log_mentor("MENTOR: [src] dementored.")`。
  7. `add_verb(src, /client/proc/cmd_mentor_rementor)`（授予恢复动词）。
- `cmd_mentor_rementor()`（category `"Mentor"`，name `"rementor"`）：
  1. `!is_mentor()` → 返回。
  2. `add_mentor_verbs()`；`GLOB.mentors[src] = TRUE`。
  3. `to_chat(src, span_interface(LANG("client.e350cdc4", null)))`；`log_mentor("MENTOR: [src] rementored.")`。
  4. `remove_verb(src, /client/proc/cmd_mentor_rementor)`（回收恢复动词）。

#### follow.dm（28 行）—— 跟随玩家

- `mentor_follow(mob/living/M)`：
  1. `!is_mentor()` → 返回。
  2. 非观察者（`!isobserver(usr)`）：`mentor_datum.following = M` → `usr.reset_perspective(M)`（视角锁定目标）→ `add_verb(src, /client/proc/mentor_unfollow)` → `to_chat(usr, span_info(LANG("client.18ceac38", list(MentorHrefToken(TRUE), key_name(M)))))` → `orbiting = FALSE`。
  3. 观察者（ghost）：`O.ManualFollow(M)`（幽灵手动跟随），`orbiting = TRUE`。
  4. 通知管理员：`to_chat(GLOB.admins, span_mentor(span_prefix(LANG("client.84aab3b1", list(key_name(usr), orbiting ? "orbiting" : "following", key_name(M), key_name(M), orbiting ? " as a ghost" : "")))))`。
  5. `log_mentor("[key_name(usr)] [orbiting ? "is now orbiting" : "began following"][key_name(M)][orbiting ? " as a ghost" : ""].")`。
- `mentor_unfollow()`（category `"Mentor"`，name `"停止跟随"`，desc `"Stop following the followed."`）：
  1. `!is_mentor()` → 返回。
  2. `usr.reset_perspective()`（恢复视角）→ `remove_verb(src, /client/proc/mentor_unfollow)`。
  3. `to_chat(GLOB.admins, span_mentor(span_prefix(LANG("client.f5713477", list(key_name(usr), key_name(mentor_datum.following))))))`。
  4. `log_mentor("[key_name(usr)] stopped following [key_name(mentor_datum.following)].")`。
  5. `mentor_datum.following = null`。

#### logging.dm（15 行）—— 日志

- `GLOBAL_LIST_EMPTY(mentorlog)` + `GLOBAL_PROTECT(mentorlog)`：本轮导师日志列表。
- `log_mentor(text, list/data)`：`GLOB.mentorlog.Add(text)` + `logger.Log(LOG_CATEGORY_GAME_MENTOR, text, data)`——双写：内存轮次列表 + 游戏日志分类 `LOG_CATEGORY_GAME_MENTOR`。
- `/datum/admins/proc/MentorLogSecret()`：构建 `"<B>Mentor Log<HR></B>"` + 每条 `"<li>[l]</li>"`；列表为空 → `"No mentors have done anything this round!"`；`usr << browse(dat, "window=mentor_log")` 弹出浏览器窗口。

### A.6 导师动词集与客户端挂载（mentor_verbs.dm / client_procs.dm）

#### mentor_verbs.dm（12 行）

- `GLOBAL_LIST_INIT(mentor_verbs, list(/client/proc/cmd_mentor_say, /client/proc/cmd_mentor_dementor))` + `GLOBAL_PROTECT(mentor_verbs)`。
- `add_mentor_verbs()`：**仅当 `mentor_datum` 存在** → `add_verb(src, GLOB.mentor_verbs)`。
- `remove_mentor_verbs()`：`remove_verb(src, GLOB.mentor_verbs)`。

#### client_procs.dm（60 行）—— client 生命周期与 Topic 挂载

- `/client/New()`：`. = ..()` 后调用 `mentor_datum_set()`（登录即挂载导师身份）。
- `/client/Destroy()`：若 `GLOB.mentors[src]` → `GLOB.mentors -= src`（下线自动移出在线列表）；返回 `..()`。
- `/client/proc/client_procs(href_list)`（Topic href 分发钩子——readme 声明的 TG 过程修改点 `client/Topic()` 的落地处）：
  - `href_list["connect_to_relay"]` → `connect_to_relay()`，返回 `TRUE`。
  - `href_list["mentor_msg"]` → `mentors_mobname_only` 开启时 `locate(href_list["mentor_msg"])` 解析为 mob（`M`），否则按文本处理；`cmd_mentor_pm(M, null)`，返回 `TRUE`。
  - `href_list["mentor_follow"]` → `locate` 为 `/mob/living` 且 `istype(M)` 成立 → `mentor_follow(M)`，返回 `TRUE`。
  - `href_list["mentor_unfollow"]` → `mentor_datum.following` 非空 → `mentor_unfollow()`，返回 `TRUE`。
- `mentor_datum_set()`：
  1. `mentor_datum = GLOB.mentor_datums[ckey]`（按 ckey 查找导师数据）。
  2. 无 datum 且 `is_admin(src)` → `new /datum/mentors(ckey)`（**管理员自动补建 datum**）。
  3. 有 datum → `mentor_datum.owner = src` → `GLOB.mentors[src] = TRUE` → `add_mentor_verbs()`。
  4. 管理员（`check_rights_for(src, R_ADMIN)`）且偏好 `auto_dementor`（`/datum/preference/toggle/admin/auto_dementor`）→ 自动 `cmd_mentor_dementor()`（管理员默认自动退出导师）。
- `is_mentor(admin_bypass = TRUE)`：返回 `mentor_datum || (admin_bypass && check_rights_for(src, R_ADMIN))`——持有导师 datum，或管理员按需绕过（默认 `TRUE`）。`player_ranks` 模块的 `SSplayer_ranks.is_mentor()`（§3.3）即此过程的空安全包装。

### A.7 与 player_ranks 模块的联动 Integration with player_ranks

| 联动点 Hook | 位置 Location | 关系 Relation |
|---|---|---|
| `new /datum/mentors(ckey)` | `mentor_controller.add_player()`（§6.2） | 授予导师 = 创建导师 datum（自动挂载动词/列表）。 |
| `GLOB.mentor_datums[ckey]?.remove_mentor()` | `mentor_controller.remove_player()`（§6.2） | 撤销导师 = 空安全调用 `remove_mentor()`。 |
| `GLOB.mentor_datums.Cut()` + 逐 client `remove_mentor_verbs()` / `mentor_datum = null` + `GLOB.mentors.Cut()` | `mentor_controller.clear_existing_rank_data()`（§6.2） | 加载前清空导师体系。 |
| `CONFIG_GET(flag/mentors_mobname_only)` | `mentorhelp.dm` / `mentorpm.dm` / `key_name_mentor()` | 显示 mob 名而非 ckey（配置见 §10）。 |
| `CONFIG_GET(flag/debug_admin_hrefs)` | `mentor.dm` `CheckMentorHREF()` | 调试模式放行错误 token 的 href。 |
| `/datum/preference/toggle/admin/auto_dementor` | `client_procs.dm` `mentor_datum_set()` | 管理员自动退导师偏好。 |
| `LOG_CATEGORY_GAME_MENTOR` | `logging.dm` `log_mentor()` | 导师日志分类。 |

---

## 11. 交叉引用 Cross-References

| 引用 Reference | 位置 Location | 关系 Relation |
|---|---|---|
| `/datum/mentors`、`GLOB.mentor_datums`、`GLOB.mentors`、`remove_mentor_verbs()`、`mentor_datum` | `modular_nova/modules/mentor/code/mentor.dm`、`client_procs.dm` | 导师体系主体在独立 `mentor` 模块；`mentor_controller` 仅桥接。**该模块已全量展开，见文末《附录 · 导师系统（Mentor）全录》。** |
| `SSdiscord.lookup_ckey()` | Discord 模块（`SSdiscord`） | `set_player_rank` 世界话题用它把 Discord ID 反查为 ckey。 |
| `GLOB.admin_datums` / `GLOB.deadmins` | 管理模块（admin） | 世界话题校验发送者是否为管理员（含已离职管理员）。 |
| `R_PERMISSIONS` / `R_DEBUG` / `R_SERVER` | 权限位（admin rights defines） | 分组增删需 `R_PERMISSIONS`；迁移需 `R_PERMISSIONS \| R_DEBUG \| R_SERVER`。 |
| `MAX_SAVE_SLOTS_SUBSCRIBER` | `code/__DEFINES/~nova_defines/preferences.dm` | 值 `100`，捐赠者/订阅者存档槽位上限。 |
| `nova_schema.sql` | 数据库 schema | `player_rank` 表（列：`ckey`、`rank`、`admin_ckey`、`deleted`）的结构定义。 |
| `SSdbcore` / `SSdbcore.MassInsert()` | 数据库核心（dbcore） | 子系统 SQL 读写与批量迁移。 |
| `server_maint` 子系统 | `/datum/controller/subsystem/server_maint` | `SSplayer_ranks` 的初始化依赖项。 |
| 游戏内管理动词（"in-game verb"） | 管理模块 | 世界话题失败文案提示用户改用游戏内动词获取更多信息。 |

---

*本文档由源码 `modular_nova/modules/player_ranks/`（968 行，7 文件）全量提取生成。数值与文案均按源码原文收录。*
*This document is a full extraction of `modular_nova/modules/player_ranks/` (968 lines, 7 files). All values and messages are recorded verbatim from source.*
*附录《导师系统全录》由源码 `modular_nova/modules/mentor/`（452 行，11 文件）全量提取生成。*
*The appendix (full mentor system record) is extracted from `modular_nova/modules/mentor/` (452 lines, 11 files).*
