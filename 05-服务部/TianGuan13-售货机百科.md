# 天关 — 售货机百科
> **项目**: TianGuan13 (Nova Sector → /tg/station)
> **代码**: `code/modules/vending/`（5,408 行）+ `code/modules/religion/`（4,441 行）
> **范围**: **售货机**（66 种）+ **宗教/牧师**（10 教派+14 仪式）
>
> **关联文档**：售货机补货与《供应系统》联动；牧师职业与《职业百科》联动。

---

## 目录

- [一、售货机全录（66 种）](#一售货机全录66-种)
- [1.1 售货机分类总览](#11-售货机分类总览)
- [1.2 售货机全录（66 种）](#12-售货机全录66-种)
- [1.3-1.7 机制与特殊功能](#13-17-机制与特殊功能)
- [附录 · 代码路径索引](#附录--代码路径索引售货机)

---

## 一、售货机全录

**代码**: `code/modules/vending/`（5,408 行，43 文件）——**67 种售货机**（含 admin 调试机 subtype_vendor）

## 1.1 售货机分类总览

| 类别 | 数量 | 说明 |
|---|---|---|
| 服装衣柜（*Drobe） | 21 | 各职业制服售货机 |
| 食品饮料 | 20 | 零食/汽水/咖啡/热狗/烟草/游戏（games 机归食品）/烟草 |
| 医疗 | 5 | NanoMed/急诊/药品/辛迪加医疗 |
| 工程科研 | 12 | 工具/零件/机器人/等离子研究/PTech/大种子 |
| 安保 | 2 | SecTech/解放站（武器） |
| 特殊 | 12 | 符文/合成/自定义/辛迪加等 |

## 1.2 售货机全录（67 种）

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
| **Subtype Vendor** | 调试 | **admin 调试机**：所有商品免费，右键选择类型（R_SERVER 权限），源码 `vending/subtype.dm` |


## 1.3 售货机机制

**代码**: `vending/vendor/`（1,582 行）

| 机制 | 说明 |
|---|---|
| 投币/支付 | 现金/银行支付 |
| 商品 | 常规/违禁（EMAG）/高级 |
| 损坏 | 砸机出商品（部分） |
| 补货 | 补货罐（vending_refill） |

---




## 1.4 售货机机制细节

| 机制 | 值 |
|---|---|
| 商品记录 | vending_product datum（商品/价格/库存） |
| **付款部门** | 默认服务账户（ACCOUNT_SRV） |
| 广告 | 随机广告语（屏幕） |
| 自定义 | Custom Vendor 可自设（selectable） |
| 交互 | interaction/库存/投掷/倾斜/UI 5 个子系统 |

> **EMAG 售货机**：解锁违禁品栏（辛迪加商品）。

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
| 退货 | 免费退货（LIFO：先退的免费再卖） |
| 非本站售货机 | 自动免费（`all_products_free`，is_station_level 判定） |
| EMAG | 仅解锁补货仓访问（compartmentLoadAccessCheck）+ 违禁目录（extended_inventory wire/contraband 列表）；**无硬币槽**（"coin_records" 实为高级标签页） |

> **接线细节**（`code/datums/wires/vending.dm`）：5 条真线（THROW 投掷/SHOCK 电击/SPEAKER 扬声器/CONTRABAND 违禁/IDSCAN ID 扫描）+ 1 装饰线；AGE 线是脉冲线，SPEAKER 线循环切换语言。

> **邮件分拣机**（mailsorter）不是 `/vending` 子类型——它是独立机器（`obj/machinery/mailsorter`），仅因功能相关被列在售货机类。

## 1.6 特殊功能售货机（18 项）

| 售货机 | 特殊功能 |
|---|---|
| **自定义售货机** | 绑账户；**自毁爆炸仅"已绑定账户被强行拆解"时触发**（explosion(dev=-1, light=3)），非通用自毁 |
| **不可摧毁售货机** | INDESTRUCTIBLE 抗性（custom/unbreakable） |
| **Greed 售货机** | 金制/700 耐久/可移动/神祇命名（custom/greed） |
| **热狗机** | 手工特质 |
| **汽水机** | 加料：仅 `STATION_TRAIT_SPIKED_DRINKS` 生效 + 65% 概率 + 非酒精（Soda 汽水机） |
| **香布勒汽水机** | 会说话的售货机（speak 覆写） |
| **香烟机** | 抛掷打火机自动点燃（pre_throw） |
| **辛迪加香烟机** | 辛迪加商品（Waffle Co） |
| **符文机** | 爆炸脉冲 |
| **SecTech** | 喷货拉环手雷 + **照明抛掷手电** |
| **生存口粮机** | 囚犯限定（非囚犯拒绝售货） |
| **劳改营机** | LP 支付 |
| **飞蛾衣柜** | mothroach 特殊生物衣柜（回合开始 5% 概率出现） |
| **邮件分拣机** | 邮件分发 |
| **辛迪加酒机** | 无年龄限制（Booze-O-Mat syndicate） |
| **辛迪加医疗机** | 辛迪加医疗（SyndiMed Plus） |
| **巫师机** | 魔法物品/全抗 100（MagiVend） |
| **解放站** | 爱国武器/高护甲（Liberation Station） |

## 1.7 NOVA 售货机修改

**代码**: `modular_nova/modules/modular_vending/` + `imported_vendors/` + `mapping/code/wardrobes.dm`

### 1.7.1 基础机制修改（8 项）

①**RoboDrobe 改造成机器人急救站**（大量医疗用品：麻醉罐/呼吸面罩/神经盘/吗啡/利多卡因等，products_nova 追加）
②**Engi-Vend RCD 删除又加回**（上游 `//NOVA EDIT REMOVAL` 注释掉，NOVA 通过 `products_nova = list(.../construction/rcd/loaded = 3)` 重新加回）
③**Games 机删俄罗斯左轮**（`contraband -= /obj/...russian_revolver`）
④**ClothesMate 库存≥5 但防弹衣/手套除外**（防刷绝缘手套）
⑤**SynDrobe/InterDrobe 新增**（`wardrobe/syndie_wardrobe` + `wardrobe/interdyne` 名 "InterDrobe"，mapping/code/wardrobes.dm）
⑥**MINIMUM_CLOTHING_STOCK=5**（服装售货机最低库存）
⑦**modular_vending 模块**（products_nova/product_categories_nova/premium_nova/contraband_nova 四类在 Initialize 合并）
⑧**sec_haul 全面覆写**（/security + sec_wardrobe 的 products/contraband/premium 全部替换，另有 security/noaccess 变体）

### 1.7.2 补充细节

- **excluded_products**：build_inventory 跳过类型缓存排除列表
- **allow_increase() 豁免**：防弹衣/头盔/手套/太阳镜不参与库存增加检查
- **boozeomat/cafe**：扩展库存 + 无年龄限制 + 矿物高级商品

### 1.7.3 NOVA 新增机器（~18 个）

| 机器 | 位置 | 说明 |
|---|---|---|
| **CommanDrobe** | access/command | 指挥层制服 |
| **Fab-O-Vend** | barbervend | 美容沙龙售货机（理发师） |
| **LustWish** | dorms | 宿舍区 |
| **DeForest Med-Vend** | deforest | 森林医疗售货机 |
| **AshClothingVendor** | ash | 灰烬衣物售货机 |
| **Catgirl Wardrobe** | catgirl | 野猫族衣柜 |
| **NT Sustenance Supplier** | imported_vendors | 进口生存补给（NT） |
| **Fudobenda** | imported_vendors | 进口自动面档（Fudobenda） |
| **Nomad Fleet Ration Chit Exchange** | imported_vendors | 游牧舰队配给券兑换 |
| **Tiziran Imported Delicacies** | imported_vendors | 提兹兰进口美食 |
| **Bitrunning 售货机 ×4** | bitrunning | 虚拟域售货机（`all_products_free` 全部免费） |
| **Subtype Vendor** | subtype.dm | admin 调试机（见 1.2 表） |

---

# 附录 · 代码路径索引（售货机）

| 系统 | 文件 | 行数 |
|---|---|---|
| 售货机核心 | `vending/vendor/` | 1,582 |
| **售货机全录** | `vending/`（43 文件） | **5,408** |
