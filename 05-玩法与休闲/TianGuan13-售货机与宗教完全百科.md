# 天关 — 售货机与宗教完全百科

> **项目**: TianGuan13 (Nova Sector → /tg/station)
> **代码**: `code/modules/vending/`（5,408 行）+ `code/modules/religion/`（4,441 行）
> **范围**: **售货机**（65 种）+ **宗教/牧师**（10 教派+14 仪式）
>
> **关联文档**：售货机补货与《供应系统》联动；牧师职业与《职业百科》联动。

---

## 目录

- [第一卷 · 售货机全录](#第一卷--售货机全录)（65 种+机制/特殊 18 项）
- [第二卷 · 宗教系统](#第二卷--宗教系统)（10 教派详表+物品+梦境）
- [附录 · 代码路径索引](#附录--代码路径索引)

---

# 第一卷 · 售货机全录

**代码**: `code/modules/vending/`（5,408 行，43 文件）——**65 种售货机**

## 1.1 售货机分类总览

| 类别 | 数量 | 说明 |
|---|---|---|
| 服装衣柜（*Drobe） | 21 | 各职业制服售货机 |
| 食品饮料 | 15 | 零食/汽水/咖啡/热狗/烟草 |
| 医疗 | 5 | NanoMed/急诊/药品/辛迪加医疗 |
| 工程科研 | 11 | 工具/零件/机器人/等离子研究 |
| 安保 | 2 | SecTech/解放站（武器） |
| 特殊 | 12 | 符文/合成/自定义/辛迪加等 |

## 1.2 售货机全录（66 种）

| 售货机 | 类型 | 说明 |
|---|---|---|
| **AtmosDrobe** | 衣柜 | 大气工程师制服 |
| **AutoDrobe** | 衣柜 | 自动服装 |
| **BODA** | 饮料 | 苏维埃苏打 |
| **BarDrobe** | 衣柜 | 调酒师制服 |
| **Bombuddy 3000** | 科研 | 等离子研究 |
| **Booze-O-Mat** | 饮料 | 酒类 |
| **CargoDrobe** | 衣柜 | 货运制服 |
| **CentDrobe** | 衣柜 | 中央制服 |
| **ChefDrobe** | 衣柜 | 厨师制服 |
| **ChemDrobe** | 衣柜 | 化学师制服 |
| **ClothesMate** | 服装 | 通用服装 |
| **CuraDrobe** | 衣柜 | 策展人制服 |
| **Custom Vendor** | 自定义 | 玩家自设售货机 |
| **Consecrated Vendor** | 自定义 | Greed 神售货机（金制/700 耐久/可移动） |
| **CytoPro** | 科研 | 细胞学 |
| **Deluxe Silicate** | 电脑 | 模块电脑配件 |
| **DetDrobe** | 衣柜 | 侦探制服 |
| **DeusVend** | 衣柜 | 牧师制服 |
| **Donk Co Vendor** | 食品 | 唐克零食 |
| **Donksoft Toy** | 玩具 | 唐克玩具枪 |
| **Emergency NanoMed** | 医疗 | 急诊药品 |
| **Engi-Vend** | 工程 | 工程用品 |
| **EngiDrobe** | 衣柜 | 工程制服 |
| **GeneDrobe** | 衣柜 | 基因学制服 |
| **Getmore Chocolate** | 食品 | 零食巧克力 |
| **Good Clean Fun** | 游戏 | 游戏用品 |
| **Hotdoggo-Vend** | 食品 | 热狗 |
| **Indestructible Vendor** | 自定义 | 不可摧毁版自定义售货机 |
| **Hydrobe** | 衣柜 | 水培制服 |
| **JaniDrobe** | 衣柜 | 清洁工制服 |
| **Labor Camp Sustenance** | 食品 | 劳改营 |
| **LawDrobe** | 衣柜 | 律师制服 |
| **Liberation Station** | 安保 | **解放站（武器！）** |
| **MagiVend** | 魔法 | 巫师用品 |
| **MediDrobe** | 衣柜 | 医疗制服 |
| **MegaSeed Servitor** | 水培 | 种子 |
| **MortiDrobe** | 衣柜 | 验尸官制服 |
| **NanoDrug Plus** | 医疗 | 药品 |
| **NanoMed Plus** | 医疗 | 医疗用品 |
| **NutriMax** | 水培 | 肥料 |
| **PTech** | 电脑 | 墨粉/纸 |
| **Part-Mart** | 工程 | 机器零件 |
| **Plasteel Chef's** | 餐饮 | 餐具 |
| **Pwr Game** | 饮料 | 能量汽水 |
| **Robco Tool Maker** | 工程 | 工具 |
| **RoboDrobe** | 衣柜 | 机器人学制服 |
| **Robotech Deluxe** | 科研 | 机器人零件 |
| **Robust Softdrinks** | 饮料 | 汽水 |
| **Runic Vending** | 魔法 | **符文武器** |
| **SciDrobe** | 衣柜 | 科研制服 |
| **SecDrobe** | 衣柜 | 安保制服 |
| **SecTech** | 安保 | 安保用品 |
| **ShadyCigs Deluxe** | 烟草 | 香烟 |
| **ShadyCigs Ultra** | 烟草 | 海滩版香烟（拉瓦兰海滩废墟） |
| **Shambler's Vendor** | 饮料 | 香布勒汽水 |
| **Solar's Best** | 饮料 | 咖啡 |
| **Space Cola** | 饮料 | 太空可乐 |
| **Space-up!** | 饮料 | 太空汽水 |
| **Star-kist** | 饮料 | 星际汽水 |
| **Sustenance** | 食品 | 生存口粮 |
| **SyndiChem** | 医疗 | **辛迪加药品** |
| **SyndiMed Plus** | 医疗 | **辛迪加医疗** |
| **Syndicate Donksoft** | 玩具 | 辛迪加玩具枪 |
| **ViroDrobe** | 衣柜 | 病毒学制服 |
| **Waffle Co** | 烟草 | **辛迪加香烟** |
| **YouTool** | 工程 | 工具 |


## 1.4 售货机机制细节

| 机制 | 值 |
|---|---|
| 商品记录 | vending_product datum（商品/价格/库存） |
| **付款部门** | 默认服务账户（ACCOUNT_SRV） |
| 广告 | 随机广告语（屏幕） |
| 自定义 | Custom Vendor 可自设（selectable） |
| 交互 | interaction/库存/投掷/倾斜/UI 5 个子系统 |

> **EMAG 售货机**：解锁违禁品栏（辛迪加商品）。

## 1.3 售货机机制

**代码**: `vending/vendor/`（1,582 行）

| 机制 | 说明 |
|---|---|
| 投币/支付 | 现金/银行支付 |
| 商品 | 常规/违禁（EMAG）/高级 |
| 损坏 | 砸机出商品（部分） |
| 补货 | 补货罐（vending_refill） |

---


## 1.5 售货机核心机制

| 机制 | 值 |
|---|---|
| **支付** | ID 刷卡/部门 8 折/20% 入机内信用池 |
| 年龄限制 | 部分商品年龄限制+安保广播 |
| 通胀 | 市场崩盘联动 |
| **接线黑客** | 5 条线+装饰线（脉冲/剪断效果） |
| 损坏 | 攻击概率表/推倒 75 伤/倾倒 16 件 |
| EMP | 电击 |
| **补货** | 补货罐/RPED/手工装填/拆解回收 |
| 退货 | 免费退货 |

## 1.6 特殊功能售货机（18 项）

| 售货机 | 特殊功能 |
|---|---|
| **自定义售货机** | 绑账户/自毁爆炸/Greed 版 |
| **不可摧毁售货机** | INDESTRUCTIBLE 抗性（custom/unbreakable） |
| **Greed 售货机** | 金制/700 耐久/可移动/神祇命名（custom/greed） |
| **热狗机** | 手工特质 |
| **汽水机** | 加料 |
| **香布勒汽水机** | 会说话的售货机（speak 覆写） |
| **香烟机** | 抛掷打火机自动点燃（pre_throw） |
| **辛迪加香烟机** | 辛迪加商品（Waffle Co） |
| **符文机** | 爆炸脉冲 |
| **SecTech** | 喷货拉环手雷 |
| **生存口粮机** | 囚犯限定（非囚犯拒绝售货） |
| **劳改营机** | LP 支付 |
| **飞蛾/蟑螂衣柜** | 特殊生物衣柜 |
| **邮件分拣机** | 邮件分发 |
| **辛迪加酒机** | 无年龄限制（Booze-O-Mat syndicate） |
| **辛迪加医疗机** | 辛迪加医疗（SyndiMed Plus） |
| **巫师机** | 魔法物品/全抗 100（MagiVend） |
| **解放站** | 爱国武器/高护甲（Liberation Station） |

## 1.7 NOVA 售货机修改

①**RoboDrobe 改造成机器人急救站**（大量医疗用品）②Engi-Vend RCD 删除又加回 ③Games 机删俄罗斯左轮 ④ClothesMate 库存≥5 但防弹衣/手套除外（防刷绝缘手套）⑤SynDrobe/InterDrobe 新增 ⑥MINIMUM_CLOTHING_STOCK=5 ⑦modular_vending 模块（products_nova 四类追加）⑧sec_haul 全面覆写。


# 第二卷 · 宗教系统

**代码**: `code/modules/religion/`（4,441 行）——牧师职业玩法

## 2.1 教派全录（10 种）

| 教派 | 代码 | 教义 |
|---|---|---|
| **NT 批准神** | puritanism | 标准 NT 宗教 |
| **机械神** | mechanical | 崇拜机械 |
| **火神** | pyre | 火葬/火堆 |
| **贪婪神** | greed | 商业/售货机 |
| **惩罚神** | burden | 负担/赎罪 |
| **荣誉神** | honorbound | 荣誉约束 |
| **维护神** | maintenance | 维护间信仰 |
| **切磋神** | spar | 战斗切磋 |
| **音乐神** | music | 节日/音乐 |
| **梦神** | dreams | 梦境 |

## 2.2 仪式全录（14 种）

| 仪式 | 代码 | 效果 |
|---|---|---|
| **合成转化** | synthconversion | 机器人转化 |
| **接受祝福** | machine_blessing | 机器祝福 |
| **购买售货托盘** | greed/vendatray | 售货机 |
| **购买个人售货机** | greed/custom_vending | 私人售货机 |
| **维护适应** | maint_adaptation | 维护适应 |
| **适应之眼** | adapted_eyes | 夜视 |
| **改造食物** | adapted_food | 食物改造 |
| **创造仪式图腾** | ritual_totem | 图腾 |
| **召唤切磋契约** | sparring_contract | 切磋契约 |
| **宣布竞技场** | declare_arena | 竞技场 |
| **锻造仪式装备** | ceremonial_weapon | 仪式武器 |
| **成为不可破坏** | unbreakable | 不可破坏 |
| **成为坚韧** | tenacious | 坚韧 |

## 2.3 宗教结构

**代码**: `religion_structures.dm`——神龛/祭坛/神器。

---


## 2.4 教派全录（10 个，含数值）

| 教派 | 阵营 | 名言 | 献祭 | 祝福 | 专属仪式 | 备注 |
|---|---|---|---|---|---|---|
| **NT 批准神（清教）** | 善良 | "Nanotrasen Recommends!" | 无 | 治疗 10/肢体 | 仅 Deaconize | 基础教派 |
| **机械神** | 中立 | "May you find peace in a metal shell." | 电池（电量≥30%） | 机仆充能 5%/以太 6%；**只修复机械肢体** | 合成体转化（1000 favor：活人→安卓）、机械祝福（2000：随机义体） | favor 上限 2500 |
| **火堆神** | 中立 | "It must burn!" | 点燃蜡烛 +40/根 | 标准 | 不熔护甲/燃烧献祭/**烈焰之星（1500：圣箭附魔）** | **favor 上限 10000**；圣物选神圣弓解锁烈焰之星 |
| **贪婪神** | 邪恶 | "Greed is good." | 财富即 favor | **烧钱治疗**（50 信用点→30 伤） | 购买售货机（300/1000 信用点，用钱不用 favor） | 收银机音效 |
| **受罚神** | 中立 | "To feel the freedom..." | 无 | **痛苦转移**（按负担等级减免） | 变形术（负担 9：圣物→牧师左轮） | 入教得鞭笞强迫症 |
| **荣誉神** | 善良 | "A good, honorable crusade..." | 无 | 标准 | **加入圣战**（无限转化+300 favor）、宽恕、召唤圣典 | 入教得教条强迫症 |
| **维护神** | 邪恶 | "Your kingdom in the darkness." | 有机浆料（≥5u） | 注入 5u 维护污泥 | 维护适应（150）/适应之眼（300）/发霉/仪式图腾（100） | 浆料=老鼠浸焊料 |
| **切磋神** | 中立 | "Your next swing must be faster..." | 无 | 标准 | 切磋契约/仪式刃（×10）/宣布竞技场/坚韧/不灭 | 输 3 场逐出教会 |
| **节日神** | 善良 | "Everything follows a rhythm..." | 无 | 标准 | 神圣小提琴/**5 种调音仪式**（福音圣歌/照明独奏/谋杀和弦/精神摇篮曲） | 送乐器信标 |
| **梦神** | 善良 | "The dream is a window into the soul." | 无 | **梦境祝福**（+20 favor/睡着×2） | 梦境执事化（限 3 人）/放逐梦魇/梦预兆/投影/庇护/睡衣派对 | 敲头 20% 失败 |

## 2.5 宗教物品全录

| 物品 | 说明 |
|---|---|
| **圣书 Bible（19 皮肤）** | 敲人祝福/水变圣水/子弹拦截 1 次/烧了诅咒 |
| **酒圣经** | 内藏威士忌 |
| **辛迪加魔典** | 反派版（读 1 次成祭司） |
| **众神祭坛** | 选教派/献祭/仪式中枢 |
| **仪式图腾** | 便携祭坛（1 次反魔法） |
| **圣水** | TRAIT_HOLY/邪教剧痛/删除血魔法 |
| **圣物 Nullrod（18 伤）** | 19 种可选：圣克莱莫剑/链锯剑/能量剑/**神圣弓**/虚空镰刀/圣滑板 |
| **牧师左轮** | 负担 9+变形术；.38 幸运弹（1/38 秒杀）；祈祷 5 秒补弹 |
| **神圣之心** | 3 次魔法格挡充能 |
| **灵能大脑/头颅** | 负担 9 最终形态；3 灵能法术（精神投射/精神增幅/灵能墙） |
| **仪式刃** | 切磋 ×10 |
| **圣典** | 4 条戒律 |
| **烈焰之箭** | 命中点燃 |

## 2.6 梦境系统

| 机制 | 值 |
|---|---|
| 做梦 | 心跳 10% 概率（碎片序列梦境） |
| 模糊预兆 | 10% 概率混入梦境池（反派提示） |
| **梦预兆仪式** | 50 favor：强制做梦得特定预兆（核弹/邪教/异端/法师进度） |
| 梦境投影 | 100 favor：灵魂出窍附身信徒 |
| **梦境庇护** | 200 favor：全教派睡眠受伤 ×0.75 |
| 睡衣派对 | 200 favor：半径 5 格入睡 20 秒共享梦境 |
| 放逐梦魇 | 尸体 +200 favor/心脏 +100+神圣之心 |

## 2.7 子系统

| 系统 | 说明 |
|---|---|
| **负担** | 自残提升 0-9 级；Lv3 心灵感应/Lv6 隔空取物/Lv9 灵能化 |
| **切磋** | 契约 1v1 圣战；4 种赌注（金钱/灵魂）；输 3 场逐出 |
| **荣誉** | 禁攻未准备者/正义者/无辜者；宣告邪恶 150 favor |
| **节日** | 5 种调音仪式（歌曲长度 ≥170 触发终曲） |
| **火堆** | 蜡烛献祭/不朽蜡烛 |

## 2.8 NOVA 修改

①继任机制（老牧师 cryo→自动继任）②4 文件未 I18N（slumber_party/dream_protection/ceremonial_gear/festival_violin）③NOVA 宗教教派覆写 ④RoboDrobe 改造成机器人急救站 ⑤Engi-Vend RCD 删除又加回 ⑥Games 机删俄罗斯左轮 ⑦ClothesMate 防弹衣/手套防刷 ⑧MINIMUM_CLOTHING_STOCK=5。


# 附录 · 代码路径索引

| 系统 | 文件 | 行数 |
|---|---|---|
| 售货机核心 | `vending/vendor/` | 1,582 |
| **售货机全录** | `vending/`（43 文件） | **5,408** |
| 教派 | `religion/religion_sects.dm` | — |
| 仪式 | `religion/rites.dm` | — |
| 结构 | `religion/religion_structures.dm` | — |
| 牧师化 | `religion/deaconize.dm` | — |
| 梦境 | `religion/dreams/` | 933 |
| 负担 | `religion/burdened/` | 681 |
| 切磋 | `religion/sparring/` | 525 |
| 荣誉 | `religion/honorbound/` | 484 |
| 节日 | `religion/festival/` | 245 |
| 火堆 | `religion/pyre/` | 152 |
