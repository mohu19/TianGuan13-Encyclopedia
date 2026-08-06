# TianGuan13 神秘学道具百科 (Occult Items Encyclopedia)

> 基于 TianGuan13 NovaSector 分支源码全量整理。核心：`code/modules/cards/deck/tarot.dm`（84 行）+ `code/game/objects/structures/spirit_board.dm`（112 行）+ `code/modules/antagonists/wizard/equipment/artefact.dm`（巫师天眼段）。
> **范围**：占卜/通灵类道具——塔罗牌（普通+闹鬼）、通灵板、巫师天眼。卡牌游戏玩法（扑克/TCG/科塔希对战）见《小游戏百科》第二卷，本章聚焦**神秘学机制**。

## 概述

天关的"神秘学道具"散落在三个模块：

| 道具 | 模块 | 行数 | 神秘学功能 |
|---|---|---|---|
| **塔罗牌**（tarot） | `code/modules/cards/deck/tarot.dm` | 84 | 占卜牌组；闹鬼版**授予第六感** |
| **通灵板**（spirit board） | `code/game/objects/structures/spirit_board.dm` | 112 | 与幽灵交流的字母板 |
| **巫师天眼**（scrying orb） | `code/modules/antagonists/wizard/equipment/artefact.dm` | ~45 | 远视 + 第六感 + X 光 |

> **关联**：普通卡牌（deck 基类 54 张 NT 牌/辛迪加 3 秒洗牌/全息甲板版）、科塔希（kotahi）、CAS（Cards Against Spess 150 白牌+50 黑牌）、Wiz-Off、自定义空白牌（blank 25 张）属**卡牌游戏玩法**，见《小游戏百科》第二卷；本章只覆盖神秘学机制（塔罗占卜/通灵板/天眼）。

---

## 一、塔罗牌（Tarot Deck）

### 1.1 普通塔罗牌

**代码**: `code/modules/cards/deck/tarot.dm` L5-47

| 属性 | 值 |
|---|---|
| 名称 | tarot game deck（塔罗占卜牌组） |
| 描述 | "一副完整的 78 张塔罗牌：4 花色 × 14 张 + 全套大阿卡那" |
| 牌数 | **78 张**（4 花色 × 14 + 22 张大阿卡那） |
| 卡组风格 | tarot |
| 占卜描述 | "tarot card reading"（塔罗占卜） |

**牌组构成**（`initialize_cards()`）：

- **4 花色 × 14 张**：
  - 数字 1-10（Hearts 红心 / Pikes 黑桃 / Clovers 梅花 / Tiles 方块）
  - 宫廷牌 4 张（Valet 侍从 / Chevalier 骑士 / Dame 王后 / Roi 国王）
- **22 张大阿卡那（Trump）**：魔术师 The Magician / 女祭司 The High Priestess / 女皇 The Empress / 皇帝 The Emperor / 教皇 The Hierophant / 恋人 The Lover / 战车 The Chariot / 正义 Justice / 隐士 The Hermit / 命运之轮 The Wheel of Fortune / 力量 Strength / 倒吊人 The Hanged Man / 死亡 Death / 节制 Temperance / 恶魔 The Devil / 高塔 The Tower / 星星 The Star / 月亮 The Moon / 太阳 The Sun / 审判 Judgement / 世界 The World / 愚者 The Fool

**随机翻转**（`draw()`）：抽牌 50% 概率牌面旋转 180°（倒置牌——占卜中的逆位牌）。

**诅咒加成**（`pick_card()`）：持有 `TRAIT_CURSED`（被诅咒）的玩家抽牌时——
- 全牌组（≥56 张）时：**24% 概率**强制抽到 Death（死亡）或 The Tower（高塔）
- 小牌组时：4% 概率
- 上限 33%（`min(33, ...)`）

> **直观理解**：被诅咒的人抽塔罗牌容易抽到"死亡/高塔"这种凶兆牌——占卜加成，纯彩蛋。

### 1.2 闹鬼塔罗牌（Haunted Tarot）

**代码**: `tarot.dm` L49-84

| 属性 | 值 |
|---|---|
| 名称 | haunted tarot game deck（闹鬼塔罗牌组） |
| 描述 | "一副阴森的塔罗牌。你能感觉到与牌相关的超自然存在……" |
| 机制 | 双手持握组件（two_handed） |

**效果**：

1. **持握（on_wield）**：
   - `ADD_TRAIT(user, TRAIT_SIXTHSENSE, MAGIC_TRAIT)` —— **授予第六感**（能听到幽灵对话，见《濒死体验与通灵听觉百科》）
   - 提示"你能感觉到超自然存在……"
   - **首次持握通知幽灵**：`notify_ghosts("有人开始玩一副闹鬼塔罗牌了！")`，附带幽灵音效（ghost2.ogg），音量 75
   - 冷却 **666 秒**（11 分 6 秒，`TAROT_GHOST_TIMER`）——每回合最多通知一次

2. **放下（on_unwield）**：`REMOVE_TRAIT(user, TRAIT_SIXTHSENSE, MAGIC_TRAIT)` —— 第六感消失

> **直观理解**：双手握住闹鬼塔罗牌 = 变成灵媒，能听到死者的声音；放下即失效。每 11 分 6 秒会"惊动"全服幽灵。

---

## 二、通灵板（Spirit Board）

**代码**: `code/game/objects/structures/spirit_board.dm`（112 行）

| 属性 | 值 |
|---|---|
| 名称 | spirit board（通灵板/灵应盘） |
| 描述 | "一块刻有字母的木制板，用于降神会" |
| 可燃 | `FLAMMABLE`（可燃） |
| 密度 | TRUE（实体阻挡） |
| 锚定 | FALSE（可移动） |
| 指针初始 | "A"（字母板） |

### 2.1 指针选项（ghosty_options）

| 类别 | 选项 |
|---|---|
| 字母 | A-Z（26 个） |
| 数字 | 0-9（10 个） |
| 答案 | Yes / No（2 个） |

### 2.2 通灵会机制（spirit_board_pick_letter）

**参与者**：活人（attack_hand）或幽灵（attack_ghost）都能操作指针。

**条件检查**（`spirit_board_checks`）：
1. **冷却**：`next_use` 冷却（3-5 秒随机，`COOLDOWN_START(src, next_use, rand(3 SECONDS, 5 SECONDS))`）；同人连用额外 +1 秒惩罚
2. **黑暗**：所在格亮度必须 < 0.2（`get_lumcount() <= 0.2`）——**通灵需要在黑暗中进行**
3. **围观人数**：`required_user_count = 2` —— 通灵板旁 **1 格内至少 2 名非 AFK、清醒、未束缚的玩家**（死尸/挂机/戴手铐的不算）
4. 满足后：弹出选择界面（tgui_input_list），选字母/数字/Yes/No

**交互流程**：
- 首次使用（`virgin = TRUE`）：`notify_ghosts("有人开始在[区域]玩通灵板了！")` 通知全服幽灵
- 指针移动：选中的字母显示在板上
- **旁观者反馈**（2 格内）：清醒或死亡的活人看到"指针缓缓移动……停在字母 X"；盲人听到"刮擦声……"
- 记录：`log_message` 记录谁选了哪个字母（游戏日志）

**彩蛋**：1% 概率板名变为 **"luigi board"**（路易吉板——致敬马里奥鬼屋的恶搞）。

> **直观理解**：通灵板是"幽灵打字机"——活人摸板触发，幽灵（或活人）在黑暗中有 2 个以上围观时，通过指针拼出字母交流。现实中降神会的玩法。

---

## 三、巫师天眼（Scrying Orb）

**代码**: `code/modules/antagonists/wizard/equipment/artefact.dm` L164-210

| 属性 | 值 |
|---|---|
| 名称 | scrying orb（天眼水晶球） |
| 来源 | 巫师装备（Scrying 段） |
| 效果 | 远视 + 第六感 + X 光视觉 |

**机制**（`attack_self` + `process`）：
- 激活后：`ADD_TRAIT(user, TRAIT_SIXTHSENSE, SCRYING_ORB)` + `TRAIT_XRAY_VISION`（X 光视觉）
- **远视**：可隔墙观察远处
- 停用时移除两个 trait（`remove_traits(list(TRAIT_SIXTHSENSE, TRAIT_XRAY_VISION), SCRYING_ORB)`）
- 持续 `process()` 维护效果

> **直观理解**：巫师的水晶球——拿着它看穿墙壁 + 听到死者 + 感知超自然。塔罗牌/通灵板是弱化平民版，天眼是强化反派版。

---

## 四、NOVA 关联

| 位置 | 关联 |
|---|---|
| `modular_nova/modules/loadouts/loadout_items/loadout_datum_toys.dm` | 配装系统可选**塔罗牌（tarot）/ 科塔希（kotahi）/ Wiz-Off（wizoff_deck）** |
| `modular_nova/modules/customization/modules/clothing/outfits/bunny.dm` | 兔女郎装左侧口袋预装**塔罗牌** |
| `modular_nova/modules/modular_implants/code/nifsofts/prop_summoner.dm` | NIFSoft 召唤器可召唤**塔罗牌 nanite 版**（"奥术是揭示一切的途径"） |
| `code/game/objects/effects/spawners/random/structure.dm` | 随机结构生成器可刷出**通灵板** |

---

## 五、一句话总结

**塔罗牌占卜（78 张 + 闹鬼版第六感）/ 通灵板降神会（黑暗 + 2 围观 + 幽灵拼字）/ 巫师天眼（远视 + X 光 + 第六感）** —— 三种占卜道具，从平民娱乐到反派侦查的完整神秘学链条。

> 源码索引：`tarot.dm`（塔罗 84 行）/ `spirit_board.dm`（通灵板 112 行）/ `artefact.dm`（巫师天眼 ~45 行）/ NOVA loadouts + bunny + prop_summoner + spawners
