# TianGuan13 开局特质完全百科（Quirks 全量手册）

> 从 TianGuan13（NovaSector 分支）源码全量提取的开局特质（Round-start Quirks）百科。
> 覆盖 `code/datums/quirks/`（主树 98 个文件）与 `modular_nova/`（NOVA 追加：traits 目录、master_files、modules 各 quirk 模块）全部可开局选择的特质，**无省略、无'等 N 种'**。

## 统计总览

| 类别 | 数量 | 说明 |
|---|---|---|
| 🔵 正面特质 | **46** | 消耗配点购买优势 |
| ⚪ 中性特质 | **50** | 配点为 0，RP/外观/语言向 |
| 🔴 负面特质 | **72** | 返还配点换取点数 |
| **合计** | **168** | 全部可选开局特质（不含抽象基类） |

> 说明：源码中另有一个抽象基类 `/datum/quirk/item_quirk/addict`（name="Addict"，`abstract_type` 标记，不可选择），其三个可选的子类为 酒鬼/瘾君子/烟鬼，已分别收录。NOVA 版另有 7 个特质被 `hidden_quirk = TRUE` 隐藏（标注 ⚠️）。

## 图例

- **配点评级（value）**：`>0` 正面（花费点数）、`0` 中性（免费）、`<0` 负面（返还点数）。`(继承)` 表示从父类继承数值。
- **quirk_flags**：
  - `仅限人类`：仅人类/人形可选（所有特质默认值，未标注也默认生效）
  - `持续处理`：每 tick 处理，持续生效（关服/停摆时暂停）
  - `改变外观`：改变角色外观（用于生成测试假人）
  - `仅影响心情`：只影响心情系统（关闭心情系统时禁用）
  - `扫描隐藏`：健康扫描仪/HUD 不显示（不算医疗状况）
  - `类脑伤`：类似脑部创伤，显示在医疗指南中
  - `不可转移`：不可通过 quirk 转移机制转移到其他角色
- **医疗记录**：所有带 `medical_record_text` 的特质都会写入医疗扫描结果（除非带 `扫描隐藏` 标记）；`类脑伤` 类还会附带医学症状文本。
- **点评星级**：★ 为笔者主观实用度评级（1~5），仅供参考。

## 目录

- [正面特质](#一正面特质positive-quirks)
- [中性特质](#二中性特质neutral-quirks)
- [负面特质](#三负面特质negative-quirks)

## 一、正面特质（Positive Quirks）

> 正面特质消耗配点（value > 0 或官方归入 positive），开局自带优势。

共 **46** 个特质：

- [酒精耐受（Alcohol Tolerance）](#datum/quirk/alcohol_tolerance)
- [多语种（Bilingual）](#datum/quirk/bilingual)
- [芯片接口（Chip Connector）](#datum/quirk/chip_connector)
- [芯片植入者（Chipped）](#datum/quirk/chipped)
- [小丑爱好者（Clown Enjoyer）](#datum/quirk/item_quirk/clown_enjoyer)
- [哑剧粉丝（Mime Fan）](#datum/quirk/item_quirk/mime_fan)
- [音乐家（Musician）](#datum/quirk/item_quirk/musician)
- [醉汉自愈（Drunken Resilience）](#datum/quirk/drunkhealing)
- [共情者（Empath）](#datum/quirk/empath)
- [自由奔跑（Freerunning）](#datum/quirk/freerunning)
- [友善（Friendly）](#datum/quirk/friendly)
- [乐天派（Jolly）](#datum/quirk/jolly)
- [灵敏嗅觉（Keen Nose）](#datum/quirk/keen_nose)
- [轻手轻脚（Light Step）](#datum/quirk/light_step)
- [海报男孩（Poster Boy）](#datum/quirk/item_quirk/poster_boy)
- [辐射狂人（Rad Fiend）](#datum/quirk/rad_fiend)
- [自我认知（Self-Aware）](#datum/quirk/selfaware)
- [开拓者后代（Settler）](#datum/quirk/settler)
- [手语者（Signer）](#datum/quirk/item_quirk/signer)
- [胆小如鼠（Skittish）](#datum/quirk/skittish)
- [太空人（Spacer）](#datum/quirk/spacer_born)
- [铁胃（Strong Stomach）](#datum/quirk/strong_stomach)
- [涂鸦大师（Tagger）](#datum/quirk/item_quirk/tagger)
- [投掷臂（Throwing Arm）](#datum/quirk/throwingarm)
- [饕餮（Voracious）](#datum/quirk/voracious)
- [念动力持物（Psionic Holding）](#datum/quirk/floating_items)
- [适应性肺（Adapted Lungs）](#datum/quirk/adapted_lungs)
- [基因突变（Genetic Mutation）](#datum/quirk/genetic_mutation)
- [毒牙（Venomous Bite）](#datum/quirk/venomous_bite)
- [夜视（Night Vision）](#datum/quirk/night_vision)
- [变形者（Shapeshifter）](#datum/quirk/shapeshifter)
- [熟练工（Skilled）](#datum/quirk/skilled)
- [伪装进食（Masquerade）](#datum/quirk/masquerade_food)
- [心灵感应（Telepathic）](#datum/quirk/telepathic)
- [回声定位（Echolocation）](#datum/quirk/echolocation)
- [咬人（Bitey）](#datum/quirk/bitey)
- [铁脚板（Hardened Soles）](#datum/quirk/hard_soles)
- [语言学家（Linguist）](#datum/quirk/linguist)
- [利爪（Sharp Claws）](#datum/quirk/sharpclaws)
- [阑尾幸存者（Appendicitis Survivor）](#datum/quirk/no_appendix)
- [灵敏听觉（Sensitive Hearing）](#datum/quirk/sensitive_hearing)
- [灰烬之相（Ash aspect (Emotes)）](#datum/quirk/ash_aspect)
- [繁花之相（Floral aspect (Emotes)）](#datum/quirk/floral_aspect)
- [闪耀之相（Sparkle aspect (Emotes)）](#datum/quirk/sparkle_aspect)
- [御水之相（Water aspect (Emotes)）](#datum/quirk/water_aspect)
- [织网之相（Webbing aspect (Emotes)）](#datum/quirk/webbing_aspect)

<a id="datum/quirk/alcohol_tolerance"></a>
### 酒精耐受 · Alcohol Tolerance

| 项目 | 内容 |
|---|---|
| 配点评级 | `4` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 体检显示患者对酒精有很高的耐受性。 |
| 效果 | 你醉得更慢，且受酒精的负面影响更少。 |
| 代价/加成 | 正面增益：不易醉酒；自带酒类品鉴技能芯片邮件福利（wine_taster 技能芯片）。 |
| 点评 | 酒鬼流招牌特质，喝酒打架两不误，性价比极高。（★★★★★） |
| 源码路径 | `code/datums/quirks/positive_quirks/alcohol_tolerance.dm` |

<a id="datum/quirk/bilingual"></a>
### 多语种 · Bilingual

| 项目 | 内容 |
|---|---|
| 配点评级 | `4` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者会说多种语言。 |
| 效果 | 多年的积累让你多掌握一门额外语言（开局可在语言选择中配置）。 |
| 代价/加成 | 正面增益：多一门语言，与外星种族/其他语种交流更顺畅。 |
| 点评 | 语言 RP 与实用并重，配合外星种族队友价值更高。（★★★★☆） |
| 源码路径 | `code/datums/quirks/positive_quirks/bilingual.dm` |

<a id="datum/quirk/chip_connector"></a>
### 芯片接口 · Chip Connector

| 项目 | 内容 |
|---|---|
| 配点评级 | `4` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者后脑勺装有可以随意装卸技能芯片的赛博植入体。 |
| 效果 | 你安装了可手动装卸技能芯片的设备，可随时更换技能芯片。 |
| 代价/加成 | 代价：靠近电磁脉冲（EMP）会出问题；后脑有可见植入体。 |
| 点评 | 技能芯片自由更换，全站最强工具人潜力股。（★★★★☆） |
| 源码路径 | `code/datums/quirks/positive_quirks/chip_connector.dm` |

<a id="datum/quirk/chipped"></a>
### 芯片植入者 · Chipped

| 项目 | 内容 |
|---|---|
| 配点评级 | `2` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者最近赶上了'技能芯片热潮'，脑袋里被塞了个没什么用的芯片。 |
| 效果 | 几年前你跟风植入了商用技能芯片，现在脑内自带一枚芯片。 |
| 代价/加成 | 中性偏正：白送一枚芯片（可拆），无实质代价。 |
| 点评 | 白嫖一枚技能芯片，2 点评级几乎等于免费。（★★★★☆） |
| 源码路径 | `code/datums/quirks/positive_quirks/chipped.dm` |

<a id="datum/quirk/item_quirk/clown_enjoyer"></a>
### 小丑爱好者 · Clown Enjoyer

| 项目 | 内容 |
|---|---|
| 配点评级 | `2` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者自称是小丑的忠实粉丝。 |
| 效果 | 你热爱小丑的把戏，佩戴小丑胸针时会获得心情加成。 |
| 代价/加成 | 正面增益：开局赠送小丑主题物品；戴胸针提升心情。 |
| 点评 | 搞笑担当必备，心情流小增益。（★★★☆☆） |
| 源码路径 | `code/datums/quirks/positive_quirks/clown_enjoyer.dm` |

<a id="datum/quirk/item_quirk/mime_fan"></a>
### 哑剧粉丝 · Mime Fan

| 项目 | 内容 |
|---|---|
| 配点评级 | `2` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者自称是哑剧演员的狂热粉丝。 |
| 效果 | 你是哑剧艺术的粉丝，佩戴哑剧胸针时会获得心情加成。 |
| 代价/加成 | 正面增益：开局赠送哑剧主题物品；戴胸针提升心情。 |
| 点评 | 与小丑爱好者对应，安静文艺流可选。（★★★☆☆） |
| 源码路径 | `code/datums/quirks/positive_quirks/mime_fan.dm` |

<a id="datum/quirk/item_quirk/musician"></a>
### 音乐家 · Musician

| 项目 | 内容 |
|---|---|
| 配点评级 | `2` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 脑部扫描显示患者拥有高度发达的听觉通路。 |
| 效果 | 你可以为手持乐器调音，演奏出能解除部分负面状态、抚慰心灵的旋律。 |
| 代价/加成 | 正面增益：开局自带乐器；演奏可解除特定负面效果并安抚他人情绪。 |
| 点评 | 团队型辅助特质，酒吧/休息区氛围担当。（★★★★☆） |
| 源码路径 | `code/datums/quirks/positive_quirks/musician.dm` |

<a id="datum/quirk/drunkhealing"></a>
### 醉汉自愈 · Drunken Resilience

| 项目 | 内容 |
|---|---|
| 配点评级 | `8` |
| quirk_flags | 仅人类、持续处理 |
| 医疗记录 | 患者肝脏代谢异常高效，饮酒可缓慢再生伤口。 |
| 效果 | 只要喝醉，你就会缓慢恢复伤势——没有什么比一杯好酒更让人精神了。 |
| 代价/加成 | 正面增益：醉酒时持续回血回伤；代价：需要保持醉酒状态。 |
| 点评 | 肉盾流神技，配酒精耐受/酒吧职位可常驻醉酒回血。（★★★★★） |
| 源码路径 | `code/datums/quirks/positive_quirks/drunk_healing.dm` |

<a id="datum/quirk/empath"></a>
### 共情者 · Empath

| 项目 | 内容 |
|---|---|
| 配点评级 | `8` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者对社交线索高度敏感，或可能拥有 ESP，需进一步检测。 |
| 效果 | 无论靠第六感还是肢体语言研究，你只需瞥一眼就能判断他人当下的情绪状态。 |
| 代价/加成 | 正面增益：可随时查看他人心情（mood），社交/医生/安保皆有用。 |
| 点评 | 信息型特质天花板，一眼看穿他人状态，辅助与 RP 通吃。（★★★★★） |
| 源码路径 | `code/datums/quirks/positive_quirks/empath.dm` |

<a id="datum/quirk/freerunning"></a>
### 自由奔跑 · Freerunning

| 项目 | 内容 |
|---|---|
| 配点评级 | `8` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者在有氧测试中得分极高。 |
| 效果 | 你擅长快速移动：翻越桌子更快，短距离坠落不受伤害。 |
| 代价/加成 | 正面增益：爬桌/翻越速度提升；低空坠落免伤。 |
| 点评 | 跑路保命神技，配合飞檐走壁流玩法极佳。（★★★★★） |
| 源码路径 | `code/datums/quirks/positive_quirks/freerunning.dm` |

<a id="datum/quirk/friendly"></a>
### 友善 · Friendly

| 项目 | 内容 |
|---|---|
| 配点评级 | `2` |
| quirk_flags | 仅人类、心情类 |
| 医疗记录 | 患者肢体接触无碍且臂力发达，请求换一位医生接手本病例。 |
| 效果 | 你擅长拥抱，心情好时能给出最棒的拥抱（给他人加心情）。 |
| 代价/加成 | 正面增益：拥抱可提升他人心情；自身心情高时效果更好。 |
| 点评 | 社交 RP 向，暖男暖女标配。（★★★☆☆） |
| 源码路径 | `code/datums/quirks/positive_quirks/friendly.dm` |

<a id="datum/quirk/jolly"></a>
### 乐天派 · Jolly

| 项目 | 内容 |
|---|---|
| 配点评级 | `4` |
| quirk_flags | 仅人类、心情类、持续处理、类脑伤 |
| 医疗记录 | 患者表现出与环境不符的持续情绪高涨，说实话有点过了。 |
| 效果 | 你有时会无缘无故地感到开心，随机获得正面心情加成。 |
| 代价/加成 | 正面增益：周期性心情大礼包；代价：无（仅情绪波动）。 |
| 点评 | 心情流白嫖加成，与抑郁/过敏体质互斥。（★★★★☆） |
| 源码路径 | `code/datums/quirks/positive_quirks/jolly.dm` |

<a id="datum/quirk/keen_nose"></a>
### 灵敏嗅觉 · Keen Nose

| 项目 | 内容 |
|---|---|
| 配点评级 | `3` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者拥有异常敏锐的嗅觉系统。 |
| 效果 | 你的鼻子非常灵敏：只需检视手中打开的容器，就能闻出里面的内容物。 |
| 代价/加成 | 正面增益：鉴定容器内容物（试剂、毒药等）无需开盖。 |
| 点评 | 化学家/厨师/安保的偷看神器，情报价值高。（★★★★☆） |
| 源码路径 | `code/datums/quirks/positive_quirks/keen_nose.dm` |

<a id="datum/quirk/light_step"></a>
### 轻手轻脚 · Light Step

| 项目 | 内容 |
|---|---|
| 配点评级 | `4` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者的灵巧程度暗示其具备很强的潜行能力。 |
| 效果 | 你步伐轻柔：脚步声更小、踩到尖锐物更不痛，踩血也不会弄脏手脚和衣服。 |
| 代价/加成 | 正面增益：潜行能力提升；踩碎玻璃/血迹不留痕迹。 |
| 点评 | 潜入流与伪装流必备，犯罪与安保两开花。（★★★★☆） |
| 源码路径 | `code/datums/quirks/positive_quirks/light_step.dm` |

<a id="datum/quirk/item_quirk/poster_boy"></a>
### 海报男孩 · Poster Boy

| 项目 | 内容 |
|---|---|
| 配点评级 | `4` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者表示渴望用自制物品贴满墙壁。 |
| 效果 | 你随身携带一批超棒的海报，贴出来能让所有人心情愉快。 |
| 代价/加成 | 正面增益：开局赠送海报；贴出后为周围人提供心情加成。 |
| 点评 | 团体心情增益，公共区域装饰大师。（★★★☆☆） |
| 源码路径 | `code/datums/quirks/positive_quirks/poster_boy.dm` |

<a id="datum/quirk/rad_fiend"></a>
### 辐射狂人 · Rad Fiend

| 项目 | 内容 |
|---|---|
| 配点评级 | `6` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者散发出轻微的放射性光环，无害。 |
| 效果 | 你被切伦科夫之光祝福：辐射无法穿透你的防护屏障，完全免疫辐射伤害。 |
| 代价/加成 | 正面增益：辐射免疫；代价：自带轻微辐射光环（无害）。 |
| 点评 | 工程/矿工/废墟探索者的生存保障。（★★★★★） |
| 源码路径 | `code/datums/quirks/positive_quirks/radfiend.dm` |

<a id="datum/quirk/selfaware"></a>
### 自我认知 · Self-Aware

| 项目 | 内容 |
|---|---|
| 配点评级 | `8` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者表现出不可思议的自我诊断能力。 |
| 效果 | 你非常了解自己的身体，能够准确评估自己伤势的严重程度。 |
| 代价/加成 | 正面增益：随时精确查看自身各部位健康/伤势数值。 |
| 点评 | 医生与独行侠的神技，受伤后自救决策更精准。（★★★★★） |
| 源码路径 | `code/datums/quirks/positive_quirks/self_aware.dm` |

<a id="datum/quirk/settler"></a>
### 开拓者后代 · Settler

| 项目 | 内容 |
|---|---|
| 配点评级 | `4` |
| quirk_flags | 仅人类、改变外观 |
| 医疗记录 | 患者长期暴露于行星环境，体格异常敦实。 |
| 效果 | 你出身于最早一批太空移民家族：身高比同族矮，但野外生存与负重能力极强，与动物相处融洽；缺点是腿短走得慢。 |
| 代价/加成 | 正面增益：野外生存/搬运加成、动物亲和；代价：移动速度偏慢。 |
| 点评 | 矮个子大力士，后勤与野外作业流。（★★★★☆） |
| 源码路径 | `code/datums/quirks/positive_quirks/settler.dm` |

<a id="datum/quirk/item_quirk/signer"></a>
### 手语者 · Signer

| 项目 | 内容 |
|---|---|
| 配点评级 | `4` |
| quirk_flags | 仅人类、改变外观 |
| 医疗记录 | 患者可熟练使用手语交流。 |
| 效果 | 你精通手语，具备出色的手语沟通能力。 |
| 代价/加成 | 正面增益：精通手语（与聋哑人/静音环境沟通无障碍）。 |
| 点评 | 配合耳聋/哑巴特质或安保审讯场景极佳。（★★★★☆） |
| 源码路径 | `code/datums/quirks/positive_quirks/signer.dm` |

<a id="datum/quirk/skittish"></a>
### 胆小如鼠 · Skittish

| 项目 | 内容 |
|---|---|
| 配点评级 | `8` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者表现出对危险的高度回避，自述曾因恐惧躲进容器。 |
| 效果 | 你极易受惊，会频繁躲藏：只要你有权限，撞进关闭的储物柜就能跳进去躲起来（也可步行移动避免触发）。 |
| 代价/加成 | 正面增益：一键躲柜逃生；代价：受惊频繁、行动受限。 |
| 点评 | 怂人保命流，遇事不决钻柜子。（★★★★☆） |
| 源码路径 | `code/datums/quirks/positive_quirks/skittish.dm` |

<a id="datum/quirk/spacer_born"></a>
### 太空人 · Spacer

| 项目 | 内容 |
|---|---|
| 配点评级 | `5` |
| quirk_flags | 改变外观 |
| 医疗记录 | 患者已良好适应非陆地环境。 |
| 效果 | 你出生在太空，从未体验过行星重力：更适应零重力/人造重力，对太空环境更抗性；但长时间待在地表会感到不适生病。 |
| 代价/加成 | 正面增益：太空/无重力环境适应；代价：行星地表久留会恶心。 |
| 点评 | 太空站主场作战神器，太空站内几乎全程收益。（★★★★★） |
| 源码路径 | `code/datums/quirks/positive_quirks/spacer.dm` |

<a id="datum/quirk/strong_stomach"></a>
### 铁胃 · Strong Stomach

| 项目 | 内容 |
|---|---|
| 配点评级 | `4` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者的免疫系统对食物中毒的抵抗力高于常人。 |
| 效果 | 你可以吃地上捡的食物而不生病，呕吐对你的影响也更小。 |
| 代价/加成 | 正面增益：吃脏食物不中毒；呕吐惩罚降低。 |
| 点评 | 厨师试吃员、垃圾桶美食家必备。（★★★★☆） |
| 源码路径 | `code/datums/quirks/positive_quirks/strong_stomach.dm` |

<a id="datum/quirk/item_quirk/tagger"></a>
### 涂鸦大师 · Tagger

| 项目 | 内容 |
|---|---|
| 配点评级 | `4` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者曾因疑似吸食油漆味气体前来就诊。 |
| 效果 | 你是经验丰富的涂鸦艺术家：你的涂鸦会让人印象深刻，且绘画工具的使用次数翻倍、耗时减半。 |
| 代价/加成 | 正面增益：涂鸦效率与效果翻倍；绘画消耗减半。 |
| 点评 | 艺术流与匿名信息传递两相宜。（★★★☆☆） |
| 源码路径 | `code/datums/quirks/positive_quirks/tagger.dm` |

<a id="datum/quirk/throwingarm"></a>
### 投掷臂 · Throwing Arm

| 项目 | 内容 |
|---|---|
| 配点评级 | `7` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者展示出对投掷球类物体的精通。 |
| 效果 | 你的手臂力量十足：投掷物飞得比任何人都远，而且从不会投偏。 |
| 代价/加成 | 正面增益：投掷距离大幅提升、投掷必中。 |
| 点评 | 投掷武器流（飞刀/手雷/化学瓶）核心特质。（★★★★★） |
| 源码路径 | `code/datums/quirks/positive_quirks/throwing_arm.dm` |

<a id="datum/quirk/voracious"></a>
### 饕餮 · Voracious

| 项目 | 内容 |
|---|---|
| 配点评级 | `4` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者对食物和饮料的欣赏程度高于常人。 |
| 效果 | 没有什么能阻挡你与食物之间：你吃得比别人快，还能暴食垃圾食品，变胖也毫无压力。 |
| 代价/加成 | 正面增益：进食速度提升；可暴食垃圾食品快速补充营养。 |
| 点评 | 干饭人干饭魂，饥饿管理毫无压力。（★★★★☆） |
| 源码路径 | `code/datums/quirks/positive_quirks/voracious.dm` |

<a id="datum/quirk/floating_items"></a>
### 念动力持物 · Psionic Holding

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 受试者的意念拥有极其有限的隔空取物能力。 |
| 效果 | 你觉得用手拿东西太麻烦了，改用念动力悬浮持物。 |
| 代价/加成 | 外观向：手持物品呈漂浮状态；无实质增益。 |
| 点评 | 纯 RP/外观特质，悬浮物品视觉效果拉满。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/positive_quirks/floating_items.dm` |

<a id="datum/quirk/adapted_lungs"></a>
### 适应性肺 · Adapted Lungs 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者拥有异常肺部。 |
| 效果 | 你的肺经过适应，对某种特定大气条件（高温/低温/低氧/毒气，开局可选）具有抗性，但代价是对其他条件更脆弱。 |
| 代价/加成 | 代价：适应一种环境，同时更容易受其他环境伤害。 |
| 点评 | 针对性生存特质，配对应环境工种食用。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/quirks/positive_quirks/adapted_lungs.dm` |

<a id="datum/quirk/genetic_mutation"></a>
### 基因突变 · Genetic Mutation 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `6` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者拥有异常的基因序列。 |
| 效果 | 你拥有罕见的基因突变，开局自带一个随机基因突变（gene mutation）。 |
| 代价/加成 | 正面增益：白送随机基因突变（可能有惊喜或惊吓）。 |
| 点评 | 抽卡式特质，欧皇起飞非酋流泪。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/quirks/positive_quirks/genetic_freak.dm` |

<a id="datum/quirk/venomous_bite"></a>
### 毒牙 · Venomous Bite 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `8` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者体内拥有毒腺。 |
| 效果 | 你长有毒腺，可以咬人并向其注射你选择的毒素。 |
| 代价/加成 | 正面增益：咬伤附带可自定义毒素；代价：需要选择合适的毒素对象。 |
| 点评 | 近战阴人流顶级特质，毒素种类自由选择。（★★★★★） |
| 源码路径 | `modular_nova/master_files/code/datums/quirks/positive_quirks/venomous/venomous_bite_quirk.dm` |

<a id="datum/quirk/night_vision"></a>
### 夜视 · Night Vision 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `4` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者眼睛表现出高于常人的暗适应能力。 |
| 效果 | 你在完全黑暗中比大多数人看得更清楚。 |
| 代价/加成 | 正面增益：黑暗环境视野更清晰。 |
| 点评 | 矿工/安保/夜行生物流实用特质。（★★★★☆） |
| 源码路径 | `modular_nova/modules/night_vision/night_vision.dm` |

<a id="datum/quirk/shapeshifter"></a>
### 变形者 · Shapeshifter 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `8` |
| quirk_flags | 仅人类 |
| 医疗记录 | 患者拥有可自由改变身体形态的异常生理结构。 |
| 效果 | 你可以随心所欲地改变自己的外貌（变脸/变体型等）。 |
| 代价/加成 | 正面增益：自由变形外观；代价：需要熟悉变形界面操作。 |
| 点评 | RP 与伪装流之王，间谍玩法核心。（★★★★★） |
| 源码路径 | `modular_nova/modules/shapeshifter_quirk/code/shapeshifter_quirk.dm` |

<a id="datum/quirk/skilled"></a>
### 熟练工 · Skilled 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `4` |
| quirk_flags | 隐藏扫描(不显示于健康扫描) |
| 医疗记录 | 患者总在强调自己是顶尖专业人士，即使与体检无关。 |
| 效果 | 上岗前你已将某项技能打磨到远超普通太空人的水平，开局可在技能配置中额外加点。 |
| 代价/加成 | 正面增益：额外技能点分配，开局就比别人专业。 |
| 点评 | 万金油特质，任何职业都能用。（★★★★☆） |
| 源码路径 | `modular_nova/modules/quirk_skilled/skilled.dm` |

<a id="datum/quirk/masquerade_food"></a>
### 伪装进食 · Masquerade 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `2` |
| quirk_flags | 仅人类 |
| 医疗记录 | 患者可不掺血直接食用普通食物饮料，但无法从中获得营养。 |
| 效果 | 身为血族（hemophage），你适应了食用普通食物与饮料——纯粹为享受而吃，无任何营养收益。 |
| 代价/加成 | 正面增益：血族也能正常吃喝（社交不掉链子）；代价：无营养。 |
| 点评 | 血族角色社交伪装必备。（★★★★☆） |
| 源码路径 | `modular_nova/modules/quirk_masquerade/masquerade.dm` |

<a id="datum/quirk/telepathic"></a>
### 心灵感应 · Telepathic 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `2` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者大脑中布罗卡氏区域异常发达，似乎能通过超感官方式交流。 |
| 效果 | 你可以将思想直接传递给其他生物，进行无声交流。 |
| 代价/加成 | 正面增益：心灵传讯（与队友无声沟通）；可被心灵阻尼器克制。 |
| 点评 | 潜行队/间谍队通讯神器，注意对方可带阻尼器反制。（★★★★☆） |
| 源码路径 | `modular_nova/modules/telepathy_quirk/code/telepathy_quirk.dm` |

<a id="datum/quirk/echolocation"></a>
### 回声定位 · Echolocation 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 仅人类、改变外观 |
| 医疗记录 | 患者眼睛在生物学上已失效，听力测试显示近乎超自然的敏锐。 |
| 效果 | 你的眼睛已无法工作，但通过某种超感官回声定位与灵敏听觉弥补。一旦失聪，回声定位也会暂时失效直到恢复！ |
| 代价/加成 | 代价：视觉完全丧失（等同失明）；收益：靠回声感知周围。 |
| 点评 | 盲人玩家的替代方案，但听不见=彻底瞎，风险高。（★★★☆☆） |
| 源码路径 | `modular_nova/modules/echolocation_quirk/code/echolocation.dm` |

<a id="datum/quirk/bitey"></a>
### 咬人 · Bitey 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者拥有可切换撕咬行为的能力。 |
| 效果 | 你可以切换徒手攻击为撕咬攻击，该能力独立于其他猫科特质。 |
| 代价/加成 | 外观/战斗向：咬人攻击开关；无实质代价。 |
| 点评 | 猫猫人卖萌与自卫两用。（★★★☆☆） |
| 源码路径 | `modular_nova/modules/bitey_quirk/code/bitey_quirk.dm` |

<a id="datum/quirk/hard_soles"></a>
### 铁脚板 · Hardened Soles 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `2` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者足部对摩擦的抵抗力更强。 |
| 效果 | 你习惯了赤脚行走，不会再受到赤脚行走的负面影响。 |
| 代价/加成 | 正面增益：赤脚免疫扎脚/地面伤害。 |
| 点评 | 赤脚流派与 RP 兽人必备。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/good.dm` |

<a id="datum/quirk/linguist"></a>
### 语言学家 · Linguist 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者展现出极高的语言学习大脑可塑性。 |
| 效果 | 你是多语言学者，开局自带额外语言点数。 |
| 代价/加成 | 正面增益：额外语言点（比多语种更灵活）。 |
| 点评 | 语言流进阶版，点选自由度高。（★★★★☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/good.dm` |

<a id="datum/quirk/sharpclaws"></a>
### 利爪 · Sharp Claws 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `2` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者抓穿了检查台坐垫，建议其修剪指甲。 |
| 效果 | 无论出于猎手本能还是拒绝剪指甲，你的徒手攻击更加锋利，会让目标流血。 |
| 代价/加成 | 正面增益：徒手攻击附加出血。 |
| 点评 | 格斗流小增强，配武术特长更佳。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/good.dm` |

<a id="datum/quirk/no_appendix"></a>
### 阑尾幸存者 · Appendicitis Survivor 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者曾患阑尾炎并已手术切除阑尾。 |
| 效果 | 你过去与阑尾炎打过交道，如今已经没有阑尾了。 |
| 代价/加成 | 正面增益：永不得阑尾炎。 |
| 点评 | 几乎白嫖的小特质，免去急诊烦恼。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/good.dm` |

<a id="datum/quirk/sensitive_hearing"></a>
### 灵敏听觉 · Sensitive Hearing ⚠️ 已隐藏 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `6` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者在听力测试中得分极高。 |
| 效果 | 你能听到最细微的声音，但因此对听力损伤更脆弱。对天生拥有灵敏听觉的种族是纯降级。 |
| 代价/加成 | 正面增益：听觉范围大幅提升；代价：更易受高分贝伤害。 |
| 点评 | ⚠️ NOVA 版已隐藏（注释：重做前禁用），仅在旧版本可用。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/good.dm` |
| 状态 | ⚠️ 本分支（NOVA/天关13）已隐藏，**不可在开局选择** |

<a id="datum/quirk/ash_aspect"></a>
### 灰烬之相 · Ash aspect (Emotes) 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者拥有蜥蜴人常见的喷火腺体。 |
| 效果 | （蜥蜴人天赋）掌握锻造灰烬与火焰的力量——大多用于表演。（说话输入 *turf 施放） |
| 代价/加成 | 正面增益：可喷火/制造灰烬（RP 向施法）。 |
| 点评 | 蜥蜴人 RP 专属，火焰特效华丽。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/good.dm` |

<a id="datum/quirk/floral_aspect"></a>
### 繁花之相 · Floral aspect (Emotes) 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者可快速光合作用以生长藤蔓。 |
| 效果 | （豆荚人天赋）快速光合作用科技已就绪！（说话输入 *turf 施放） |
| 代价/加成 | 正面增益：快速生长藤蔓（RP 向）。 |
| 点评 | 豆荚人 RP 专属。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/good.dm` |

<a id="datum/quirk/sparkle_aspect"></a>
### 闪耀之相 · Sparkle aspect (Emotes) 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者看起来非常耀眼。 |
| 效果 | （飞蛾人天赋）像蛾翅上的鳞粉一样闪闪发光。（说话输入 *turf 施放） |
| 代价/加成 | 正面增益：全身闪光特效（RP 向）。 |
| 点评 | 飞蛾人 RP 专属，全站最靓的仔。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/good.dm` |

<a id="datum/quirk/water_aspect"></a>
### 御水之相 · Water aspect (Emotes) 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者体内存有一批可合成 H2O 的纳米机器人。 |
| 效果 | （水生种族天赋）水下社会是你的家，太空也没什么不同。（说话输入 *turf 施放） |
| 代价/加成 | 正面增益：可操控水流（RP 向）。 |
| 点评 | 水生种族 RP 专属。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/good.dm` |

<a id="datum/quirk/webbing_aspect"></a>
### 织网之相 · Webbing aspect (Emotes) 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者可用天然合成的丝线编织蛛网。 |
| 效果 | （虫族天赋）会织网的虫族从不缺人羡慕——毕竟不是人人都有天然 3D 打印机。（说话输入 *turf 施放） |
| 代价/加成 | 正面增益：可编织蛛网（RP 向）。 |
| 点评 | 虫族 RP 专属。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/good.dm` |

## 二、中性特质（Neutral Quirks）

> 中性特质配点为 0，不赚不亏，多为外观、语言、饮食或 RP 向。

共 **50** 个特质：

- [光头（Smooth-Headed）](#datum/quirk/item_quirk/bald)
- [发光体（Illuminated）](#datum/quirk/cosglow)
- [怪味癖好（Deviant Tastes）](#datum/quirk/deviant_tastes)
- [天生邪恶（Fundamentally Evil）](#datum/quirk/evil)
- [外国人（Foreigner）](#datum/quirk/foreigner)
- [游戏宅（Gamer）](#datum/quirk/gamer)
- [异色瞳（Heterochromatic）](#datum/quirk/heterochromatic)
- [全色盲（Monochromacy）](#datum/quirk/monochromatic)
- [味觉缺失（Ageusia）](#datum/quirk/no_taste)
- [恐惧症（Phobia）](#datum/quirk/phobia)
- [摄影师（Photographer）](#datum/quirk/item_quirk/photographer)
- [菠萝厌恶（Ananas Aversion）](#datum/quirk/pineapple_hater)
- [菠萝喜爱（Ananas Affinity）](#datum/quirk/pineapple_liker)
- [贼眼（Shifty Eyes）](#datum/quirk/shifty_eyes)
- [亡者失忆症（Thanatorenasia）](#datum/quirk/death_dnr_poll)
- [超人类主义者（Transhumanist）](#datum/quirk/transhumanist)
- [素食主义者（Vegetarian）](#datum/quirk/vegetarian)
- [蹦蹦跳跳（Bouncy!）](#datum/quirk/bouncy)
- [不眨眼（Unblinking）](#datum/quirk/unblinking)
- [氮气呼吸（Nitrogen Breather）](#datum/quirk/item_quirk/breather/nitrogen_breather)
- [等离子呼吸（Plasma Breather）](#datum/quirk/item_quirk/breather/plasma_breather)
- [水中呼吸（Water Breather）](#datum/quirk/item_quirk/breather/water_breather)
- [便当盒用户（Lunchbox User）](#datum/quirk/item_quirk/lunchbox_owner)
- [拒绝亲昵（Affection Aversion）](#datum/quirk/affectionaversion)
- [易兴奋（Excitable!）](#datum/quirk/excitable)
- [个人空间（Personal Space）](#datum/quirk/personalspace)
- [拒绝复活（Do Not Revive）](#datum/quirk/dnr)
- [假性延髓情感（Pseudobulbar Affect）](#datum/quirk/item_quirk/joker)
- [猫之相（Feline Traits）](#datum/quirk/feline_aspect)
- [犬之相（Canidae Traits）](#datum/quirk/canine_aspect)
- [鸟之相（Avian Traits）](#datum/quirk/avian_aspect)
- [敏感吻部（Sensitive Snout）](#datum/quirk/sensitivesnout)
- [超重（Overweight）](#datum/quirk/overweight)
- [异常体温（Abnormal body temperature）](#datum/quirk/bodytemp)
- [定制舌头（Custom Tongue）](#datum/quirk/custom_tongue)
- [体格沉重（Heavyset）](#datum/quirk/heavyset)
- [九头蛇头（Hydra Heads）](#datum/quirk/hydra)
- [宠物主人（Pet Owner）](#datum/quirk/item_quirk/pet_owner)
- [配给券领取者（Ration Ticket Receiver）](#datum/quirk/item_quirk/ration_system)
- [地下世界人脉（Underworld Connections）](#datum/quirk/item_quirk/underworld_connections)
- [灵能阻尼器（Psionic Dampener）](#datum/quirk/psionic_dampener)
- [超大体型（Oversized）](#datum/quirk/oversized)
- [机械肢快拆接口（Cybernetic Limb Mounts）](#datum/quirk/robot_limb_detach)
- [重心不稳（Unsteady）](#datum/quirk/unsteady)
- [异常生物化学（Unusual Biochemistry）](#datum/quirk/unusual_biochemistry)
- [声优（Voice Actor）](#datum/quirk/voice_actor)
- [受虐癖（Masochism）](#datum/quirk/masochism)
- [施虐癖（Sadism）](#datum/quirk/sadism)
- [绳缚爱好者（Rope bunny）](#datum/quirk/ropebunny)
- [绳艺师（Rigger）](#datum/quirk/rigger)

<a id="datum/quirk/item_quirk/bald"></a>
### 光头 · Smooth-Headed

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 仅人类、改变外观 |
| 医疗记录 | 检查期间患者坚决拒绝摘除头饰。 |
| 效果 | 你没有头发，并且对此相当不自信！请戴好假发，或至少遮住头顶。 |
| 代价/加成 | 外观向：开局无发并强制佩戴帽子/假发（自动装备）；摘下帽子会不安。 |
| 点评 | 纯 RP/外观特质，帽子收藏家福音。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/neutral_quirks/bald.dm` |

<a id="datum/quirk/cosglow"></a>
### 发光体 · Illuminated

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者散发出微弱的自发光晕。 |
| 效果 | 你散发着可自定义颜色的柔和光芒！亮度不足以替代手电筒。 |
| 代价/加成 | 外观向：全身自发光；颜色可自定义。 |
| 点评 | 黑暗中自带氛围灯，外观流首选。（★★★☆☆） |
| 源码路径 | `code/datums/quirks/neutral_quirks/cosglow.dm` |

<a id="datum/quirk/deviant_tastes"></a>
### 怪味癖好 · Deviant Tastes ⚠️ 已隐藏 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者表现出不寻常的营养偏好。 |
| 效果 | 你讨厌大多数人喜欢的食物，却觉得他们不爱吃的东西很美味。 |
| 代价/加成 | 饮食向：口味反转。 |
| 点评 | ⚠️ NOVA 版已隐藏——由食物偏好系统取代，不可选。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/neutral_quirks/deviant_tastes.dm` |
| 状态 | ⚠️ 本分支（NOVA/天关13）已隐藏，**不可在开局选择** |

<a id="datum/quirk/evil"></a>
### 天生邪恶 · Fundamentally Evil ⚠️ 已隐藏 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者以全优成绩通过社会适应测试，但共情测试表现不佳。 |
| 效果 | 你的灵魂所在之处是一片墨黑虚空。你维持着社会形象，但任何凝视你冰冷双眼的人都将明白真相——你发自内心地选择了邪恶。 |
| 代价/加成 | RP 向：邪恶阵营标签（影响邪教/圣光等互动）。 |
| 点评 | ⚠️ NOVA 版已隐藏——与严肃服务器氛围不符、易诱发恶意行为，不可选。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/neutral_quirks/evil.dm` |
| 状态 | ⚠️ 本分支（NOVA/天关13）已隐藏，**不可在开局选择** |

<a id="datum/quirk/foreigner"></a>
### 外国人 · Foreigner ⚠️ 已隐藏 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者不会说银河通用语，可能需要翻译。 |
| 效果 | 你不是本地人：完全不会说银河通用语！ |
| 代价/加成 | 语言向：开局不会通用语，只会母语。 |
| 点评 | ⚠️ NOVA 版已隐藏——NOVA 已用另一种方式（语言系统）实现，不可选。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/neutral_quirks/foreigner.dm` |
| 状态 | ⚠️ 本分支（NOVA/天关13）已隐藏，**不可在开局选择** |

<a id="datum/quirk/gamer"></a>
### 游戏宅 · Gamer

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者患有严重的电子游戏成瘾。 |
| 效果 | 你是硬核玩家，有强烈的游戏需求：爱赢恨输，只爱吃'玩家食品'（游戏零食）。 |
| 代价/加成 | 饮食/情绪向：只喜欢特定垃圾食品；输游戏会心态崩。 |
| 点评 | 梗特质，游戏零食挑食流。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/neutral_quirks/gamer.dm` |

<a id="datum/quirk/heterochromatic"></a>
### 异色瞳 · Heterochromatic

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 仅人类、改变外观 |
| 医疗记录 | 患者双瞳颜色不同。 |
| 效果 | 你的一只眼睛与另一只颜色不同！ |
| 代价/加成 | 外观向：双色瞳孔（可自定义颜色）。 |
| 点评 | 颜值流必备，猫眼异瞳控福音。（★★★☆☆） |
| 源码路径 | `code/datums/quirks/neutral_quirks/heretochromatic.dm` |

<a id="datum/quirk/monochromatic"></a>
### 全色盲 · Monochromacy

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者患有近乎完全的颜色失认。 |
| 效果 | 你患有全色盲，眼中世界几乎只有黑白。 |
| 代价/加成 | 代价：无法分辨颜色（读药瓶标签/看 UI 颜色会困惑）。 |
| 点评 | 硬核视觉挑战向，色盲模拟器。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/neutral_quirks/monochromatic.dm` |

<a id="datum/quirk/no_taste"></a>
### 味觉缺失 · Ageusia

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者患有味觉缺失，无法品尝食物或试剂。 |
| 效果 | 你尝不出任何味道！有毒食物依然会毒死你。 |
| 代价/加成 | 代价：尝不出食物/药剂味道（无法靠尝识别）。 |
| 点评 | 对厨师/医生是减分项，纯 RP 向。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/neutral_quirks/no_taste.dm` |

<a id="datum/quirk/phobia"></a>
### 恐惧症 · Phobia

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者对某种事物存在非理性恐惧。 |
| 效果 | 你对某种事物（开局自定义选择）怀有非理性的恐惧，见到时会触发恐慌反应。 |
| 代价/加成 | 代价：遇到恐惧源会恐慌（移动/操作受限）。 |
| 点评 | 自定义 RP 恐惧，花样作死指南。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/neutral_quirks/phobia.dm` |

<a id="datum/quirk/item_quirk/photographer"></a>
### 摄影师 · Photographer

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者将摄影作为减压爱好。 |
| 效果 | 你随身携带相机和个人相册，你的剪贴簿在同事间堪称传奇。 |
| 代价/加成 | 增益：开局赠送相机与相册，可拍照记录。 |
| 点评 | 记录员/记者 RP 必备，留存珍贵影像。（★★★☆☆） |
| 源码路径 | `code/datums/quirks/neutral_quirks/photographer.dm` |

<a id="datum/quirk/pineapple_hater"></a>
### 菠萝厌恶 · Ananas Aversion ⚠️ 已隐藏 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者正确地认为菠萝很恶心。 |
| 效果 | 你对菠萝属水果深恶痛绝——到底什么人会觉得这玩意好吃？又有哪个疯子敢把它放上披萨！？ |
| 代价/加成 | 饮食向：拒绝菠萝；吃菠萝会心情变差。 |
| 点评 | 梗特质，披萨战争参战资格证。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/neutral_quirks/pineapple_hater.dm` |
| 状态 | ⚠️ 本分支（NOVA/天关13）已隐藏，**不可在开局选择** |

<a id="datum/quirk/pineapple_liker"></a>
### 菠萝喜爱 · Ananas Affinity ⚠️ 已隐藏 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者表现出对菠萝的病态热爱。 |
| 效果 | 你对菠萝属水果无比喜爱，永远吃不够它们甜蜜的滋味！ |
| 代价/加成 | 饮食向：菠萝狂热粉；吃菠萝心情愉悦。 |
| 点评 | 梗特质，菠萝披萨正统拥护者。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/neutral_quirks/pineapple_liker.dm` |
| 状态 | ⚠️ 本分支（NOVA/天关13）已隐藏，**不可在开局选择** |

<a id="datum/quirk/shifty_eyes"></a>
### 贼眼 · Shifty Eyes

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 那混蛋整个体检都在盯着我看。我写下这个诊断只是因为比相信他是有意的要少些尴尬。 |
| 效果 | 你的眼神总是不由自主地四处乱瞟，让别人以为你在盯着他们看——其实并没有。 |
| 代价/加成 | 外观/社交向：眼神漂移特效；他人误以为被注视。 |
| 点评 | 喜剧效果拉满，嫌疑人气质自选。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/neutral_quirks/shifty_eyes.dm` |

<a id="datum/quirk/death_dnr_poll"></a>
### 亡者失忆症 · Thanatorenasia

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 不可转移 |
| 医疗记录 | 患者患有亡者失忆症——在其死亡并复活时，记录可能不完整。 |
| 效果 | 每当你死亡并选择'拒绝抢救（DNR）'时，复活后的身体可能被另一个鬼魂接管——获得全新的性格与记忆。 |
| 代价/加成 | RP 向：死亡复活可能换人（失忆 RP）。 |
| 点评 | 剧本杀玩家狂喜，每次死亡都是新故事。（★★★☆☆） |
| 源码路径 | `code/datums/quirks/neutral_quirks/thanatorenasia.dm` |

<a id="datum/quirk/transhumanist"></a>
### 超人类主义者 · Transhumanist

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 仅人类、持续处理、心情类 |
| 医疗记录 | 患者自述憎恨那些可怜的肉与骨生物。 |
| 效果 | 你视硅基生命为完美形态，厌恶有机血肉：与机器人相处更开心，与有机物相处会烦躁。你渴望用完美硅基替换腐朽肉身，开局自带一件机械义体。 |
| 代价/加成 | 代价：与人类相处心情差；收益：开局白送机械改造件。 |
| 点评 | 机械改造流核心，与身体纯粹主义者互斥。（★★★☆☆） |
| 源码路径 | `code/datums/quirks/neutral_quirks/transhumanist.dm` |

<a id="datum/quirk/vegetarian"></a>
### 素食主义者 · Vegetarian ⚠️ 已隐藏 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者自述为素食饮食。 |
| 效果 | 你觉得吃肉在道德和生理上都令人反感。 |
| 代价/加成 | 饮食向：拒绝肉类；吃肉会恶心。 |
| 点评 | ⚠️ NOVA 版已隐藏——可通过食物偏好系统成为纯素者，不可选。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/neutral_quirks/vegetarian.dm` |
| 状态 | ⚠️ 本分支（NOVA/天关13）已隐藏，**不可在开局选择** |

<a id="datum/quirk/bouncy"></a>
### 蹦蹦跳跳 · Bouncy! 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者走路姿态不规则。 |
| 效果 | 你的步伐带着弹性！ |
| 代价/加成 | 外观向：走路蹦跳特效。 |
| 点评 | 快乐风 RP，走路带风。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/quirks/neutral_quirks/bouncy.dm` |

<a id="datum/quirk/unblinking"></a>
### 不眨眼 · Unblinking 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者无法眨眼。 |
| 效果 | 出于某种原因，你没有眼皮，因此无法眨眼。 |
| 代价/加成 | 外观向：永不眨眼；与闪光凝视（氟化物凝视）互斥。 |
| 点评 | 恐怖片氛围拉满。（★★☆☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/quirks/neutral_quirks/unblinking.dm` |

<a id="datum/quirk/item_quirk/breather/nitrogen_breather"></a>
### 氮气呼吸 · Nitrogen Breather 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者只能呼吸氮气。 |
| 效果 | 你呼吸氮气（即使你的种族通常不这么做），氧气对你而言是毒气。 |
| 代价/加成 | 代价：只能在氮气环境中生存，氧气=中毒。 |
| 点评 | 工程/等离子区特化生存流，换气需谨慎。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/quirks/neutral_quirks/breather/nitrogen_breather.dm` |

<a id="datum/quirk/item_quirk/breather/plasma_breather"></a>
### 等离子呼吸 · Plasma Breather 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者只能呼吸等离子体。 |
| 效果 | 你呼吸等离子体（即使你的种族通常不这么做），氧气对你而言是毒气。 |
| 代价/加成 | 代价：只能在等离子环境中生存，氧气=中毒。 |
| 点评 | 等离子工程师特化，作死与专业一线之隔。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/quirks/neutral_quirks/breather/plasma_breather.dm` |

<a id="datum/quirk/item_quirk/breather/water_breather"></a>
### 水中呼吸 · Water Breather 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者身上长有一对鳃。 |
| 效果 | 你长有一对鳃，只能通过水中的氧气呼吸——保持湿润才能呼吸！ |
| 代价/加成 | 代价：离开水/不保持湿润会窒息；可搭配恐水症互斥。 |
| 点评 | 水生种族/潜水员特化，鱼人 RP 必备。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/quirks/neutral_quirks/breather/water_breather.dm` |

<a id="datum/quirk/item_quirk/lunchbox_owner"></a>
### 便当盒用户 · Lunchbox User 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 仅人类、隐藏扫描 |
| 医疗记录 | 患者提到自己偏爱外带食物与饮料。 |
| 效果 | 你给自己带了一份午餐便当（自制与否皆可），开局自带便当盒。 |
| 代价/加成 | 增益：开局赠送便当盒及食物。 |
| 点评 | 干饭人专属，开局自带干粮。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/quirks/neutral_quirks/lunch_box/lunchbox.dm` |

<a id="datum/quirk/affectionaversion"></a>
### 拒绝亲昵 · Affection Aversion 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者已登记进入'禁止舔舐'与'禁止蹭鼻'名录。 |
| 效果 | 你拒绝被四足机器人舔或蹭鼻子。 |
| 代价/加成 | 社交向：免疫/拒绝动物型机仆的亲昵行为。 |
| 点评 | 个人边界捍卫者，机仆受害者福音。（★★☆☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/neutral.dm` |

<a id="datum/quirk/excitable"></a>
### 易兴奋 · Excitable! 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者似乎很容易兴奋。 |
| 效果 | 被摸头会让你的尾巴摇起来！你非常容易兴奋！摇摇摇！ |
| 代价/加成 | 社交向：摸头兴奋特效（有尾巴的种族效果更佳）。 |
| 点评 | 毛茸茸种族卖萌标配。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/neutral.dm` |

<a id="datum/quirk/personalspace"></a>
### 个人空间 · Personal Space 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者对自己的臀部被触碰表现出负面反应。 |
| 效果 | 你希望别人不要碰你的臀部。 |
| 代价/加成 | 社交向：被摸臀会心情变差/触发反应。 |
| 点评 | 安全边界宣言，防咸猪手。（★★☆☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/neutral.dm` |

<a id="datum/quirk/dnr"></a>
### 拒绝复活 · Do Not Revive 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者为 DNR（拒绝复苏）状态，无法以任何方式复活。 |
| 效果 | 出于某种原因，你无法以任何方式被复活。 |
| 代价/加成 | 重大代价：死亡即结束（无法被克隆/复活/还魂）。 |
| 点评 | 硬核模式玩家专属，一条命通关。（★★☆☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/neutral.dm` |

<a id="datum/quirk/item_quirk/joker"></a>
### 假性延髓情感 · Pseudobulbar Affect 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 仅人类、持续处理 |
| 医疗记录 | 患者患有突发且无法控制的爆笑症状。 |
| 效果 | 你会随机不受控制地爆发大笑。 |
| 代价/加成 | 代价：随机强制大笑（可能暴露潜行位置）。 |
| 点评 | 欢乐喜剧人，藏不住的快乐。（★★☆☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/neutral.dm` |

<a id="datum/quirk/feline_aspect"></a>
### 猫之相 · Feline Traits 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者似乎拥有类似猫科的行为模式。 |
| 效果 | 你表现得像只猫（无论原因），此特质会取代大部分基于舌头的语音特质（换成猫舌）。 |
| 代价/加成 | 外观/语音向：猫舌+猫系行为；怕水。 |
| 点评 | 猫猫人标配，注意会怕水。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/neutral.dm` |

<a id="datum/quirk/canine_aspect"></a>
### 犬之相 · Canidae Traits 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 有人看见患者翻垃圾桶，请盯紧他们。 |
| 效果 | 汪汪。你似乎表现得像只狗（无论原因），此特质会取代大部分基于舌头的语音特质（换成犬舌）。 |
| 代价/加成 | 外观/语音向：犬舌+犬系行为。 |
| 点评 | 狗狗人标配。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/neutral.dm` |

<a id="datum/quirk/avian_aspect"></a>
### 鸟之相 · Avian Traits 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者表现出鸟类相关举止。 |
| 效果 | 你是个鸟脑子（或拥有鸟的脑子），此特质会取代大部分基于舌头的语音特质（换成鸟舌）。 |
| 代价/加成 | 外观/语音向：鸟舌+鸟系行为。 |
| 点评 | 鸟鸟人标配，叫声奇特。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/neutral.dm` |

<a id="datum/quirk/sensitivesnout"></a>
### 敏感吻部 · Sensitive Snout 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者鼻尖似乎有一簇神经，建议避免直接接触。 |
| 效果 | 你的脸部一直很敏感，被人戳鼻子真的很痛！ |
| 代价/加成 | 社交向：被戳鼻子会痛/触发反应。 |
| 点评 | 兽人 RP 专属，戳鼻警告。（★★☆☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/neutral.dm` |

<a id="datum/quirk/overweight"></a>
### 超重 · Overweight 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者体重高于平均水平。 |
| 效果 | 你比同体型平均体重更重，早已习惯于此。 |
| 代价/加成 | 体感向：体重增加；撞击/扑倒能力有特殊表现。 |
| 点评 | 坦克体型 RP，扑倒流隐藏加成。（★★★☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/neutral.dm` |

<a id="datum/quirk/bodytemp"></a>
### 异常体温 · Abnormal body temperature 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者的体温对其种族而言异常。 |
| 效果 | 你的体温与种族基线相比存在偏移（可在 -40 到 +70 之间自定义）。极端数值可能导致轻微烫伤。不建议冷血种族选择。 |
| 代价/加成 | 代价：体温异常，极端值有轻微副作用。 |
| 点评 | 冷热环境适应 RP 向，数值党可玩出花。（★★☆☆☆） |
| 源码路径 | `modular_nova/modules/quirk_bodytemp/body_temp.dm` |

<a id="datum/quirk/custom_tongue"></a>
### 定制舌头 · Custom Tongue 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者说话有点怪。 |
| 效果 | 你的舌头并非标准配置：其形状与质感独一无二，影响你的说话方式（可自定义舌型）。 |
| 代价/加成 | 外观/语音向：自定义舌头形状，影响发音。 |
| 点评 | furry/异种 RP 定制神器。（★★★☆☆） |
| 源码路径 | `modular_nova/modules/quirk_customtongue/customtongue.dm` |

<a id="datum/quirk/heavyset"></a>
### 体格沉重 · Heavyset 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者体重远超平均水平。 |
| 效果 | 你比大多数人重得多：自己移动更费劲，别人想搬动你也更难。 |
| 代价/加成 | 代价：移动变慢；收益：更难被拖动/扑倒。 |
| 点评 | 重量级选手，守点流可选。（★★★☆☆） |
| 源码路径 | `modular_nova/modules/quirk_heavyset/heavyset.dm` |

<a id="datum/quirk/hydra"></a>
### 九头蛇头 · Hydra Heads 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 一个身体上附着多个头颅与多个人格。 |
| 效果 | 你是三头生物。使用格式：名字写成（Rucks-Sucks-Ducks）这样的三连格式。 |
| 代价/加成 | 外观/语音向：三头三人格 RP。 |
| 点评 | 多人格 RP 玩家狂喜，一人分饰三角。（★★★★☆） |
| 源码路径 | `modular_nova/modules/hydra/code/neutral.dm` |

<a id="datum/quirk/item_quirk/pet_owner"></a>
### 宠物主人 · Pet Owner 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者提到自己非常喜爱宠物。 |
| 效果 | 你把宠物带到工作地点，让它也体验一把太空站生活的危险。 |
| 代价/加成 | 增益：开局自带一只宠物（可选种类）。 |
| 点评 | 摸鱼快乐源泉，站宠文化传播者。（★★★★☆） |
| 源码路径 | `modular_nova/modules/pet_owner/pet_owner.dm` |

<a id="datum/quirk/item_quirk/ration_system"></a>
### 配给券领取者 · Ration Ticket Receiver 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 仅人类、隐藏扫描 |
| 医疗记录 | 已登记加入配给券计划。 |
| 效果 | 出于某些生活原因，你加入了配给券计划：薪水减半，换取可在货运控制台兑换食物等物品的配给券。 |
| 代价/加成 | 代价：工资减半；收益：每周配给券（换食物/物资）。 |
| 点评 | 穷苦人设 RP，吃货补贴计划。（★★☆☆☆） |
| 源码路径 | `modular_nova/modules/paycheck_rations/code/quirk.dm` |

<a id="datum/quirk/item_quirk/underworld_connections"></a>
### 地下世界人脉 · Underworld Connections 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 隐藏扫描(不显示于健康扫描) |
| 医疗记录 | 患者档案疑似曾被篡改。 |
| 效果 | 你与银河黑社会关系密切：开局自带可自定义的黑市联络器，还能接触掌握船员把柄的情报贩子。但安保部门对你有疑虑，且你可能难以获得武器许可证。 |
| 代价/加成 | 收益：黑市渠道+情报；代价：安保怀疑、武器证难办。 |
| 点评 | 灰色地带 RP 核心，商人/线人双面人生。（★★★★☆） |
| 源码路径 | `modular_nova/modules/underworld_connections/code/underworld_connections_quirk.dm` |

<a id="datum/quirk/psionic_dampener"></a>
### 灵能阻尼器 · Psionic Dampener 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 受试者表现出持续的皮层共振抑制，脑图谱显示近乎完全免疫心灵/灵能接触。 |
| 效果 | 你的心智对灵能入侵异常抵抗，心灵感应通讯无法触及你。 |
| 代价/加成 | 防御向：免疫心灵感应（防读心）。 |
| 点评 | 对抗心灵感应者的反制牌。（★★★☆☆） |
| 源码路径 | `modular_nova/modules/telepathy_quirk/code/telepathy_block_quirk.dm` |

<a id="datum/quirk/oversized"></a>
### 超大体型 · Oversized 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 仅人类、改变外观 |
| 医疗记录 | 患者异常高大。 |
| 效果 | 你（无论原因）实在太高了，会因此遇到一些麻烦的处境。 |
| 代价/加成 | 代价：体型过大，进出狭窄区域/气闸困难。 |
| 点评 | 巨人 RP 流，体积即正义。（★★★☆☆） |
| 源码路径 | `modular_nova/modules/oversized/code/oversized_quirk.dm` |

<a id="datum/quirk/robot_limb_detach"></a>
### 机械肢快拆接口 · Cybernetic Limb Mounts 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 仅人类 |
| 医疗记录 | 患者带有快拆式肢体关节机械改造。 |
| 效果 | 只要机械肢体状态良好，你就能以极低的代价拆卸和重装任何已安装的机械肢体。 |
| 代价/加成 | 增益：机械肢体可自由快拆快装。 |
| 点评 | 赛博格流便利选项，随时换装义体。（★★★☆☆） |
| 源码路径 | `modular_nova/modules/robot_limb_detach/code/robot_limb_detach_quirk.dm` |

<a id="datum/quirk/unsteady"></a>
### 重心不稳 · Unsteady 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者极易因外力而摔倒。 |
| 效果 | 你很容易被击倒或吓到，受伤时经常摔倒。 |
| 代价/加成 | 代价：被击中时更容易倒地。 |
| 点评 | 喜剧摔跤手，自带倒地特效。（★★☆☆☆） |
| 源码路径 | `modular_nova/modules/quirk_unsteady/unsteady.dm` |

<a id="datum/quirk/unusual_biochemistry"></a>
### 异常生物化学 · Unusual Biochemistry 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 仅人类 |
| 医疗记录 | 患者拥有异常生物化学特征，输血可能需要化学师协助。 |
| 效果 | 你血液的化学成分与普通船员不同。 |
| 代价/加成 | 代价：血液特殊，输血需化学师调配相容血型。 |
| 点评 | 异种 RP 向，医生头疼系列。（★★☆☆☆） |
| 源码路径 | `modular_nova/modules/unusual_biochemistry/unusual_biochemistry.dm` |

<a id="datum/quirk/voice_actor"></a>
### 声优 · Voice Actor 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 仅人类 |
| 医疗记录 | （无医疗记录文本） |
| 效果 | 你可以切换到第二套聊天颜色、TTS 语音和语音表情。 |
| 代价/加成 | 外观向：双语音配置（第二声线）。 |
| 点评 | RP 配音演员，一人两角。（★★★☆☆） |
| 源码路径 | `modular_nova/modules/voice_actor_quirk/code/voice_actor_quirk.dm` |

<a id="datum/quirk/masochism"></a>
### 受虐癖 · Masochism 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 受试者患有受虐癖。 |
| 效果 | 疼痛带给你难以言喻的快感。 |
| 代价/加成 | 情绪向：受伤时心情反而变好。 |
| 点评 | R18 向 RP 特质（NOVA 涩涩模块）。（★★☆☆☆） |
| 源码路径 | `modular_nova/modules/modular_items/lewd_items/code/lewd_quirks.dm` |

<a id="datum/quirk/sadism"></a>
### 施虐癖 · Sadism 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 受试者患有施虐癖。 |
| 效果 | 看到他人痛苦时你会感到愉悦。 |
| 代价/加成 | 情绪向：他人受伤/痛苦时你心情变好。 |
| 点评 | R18 向 RP 特质（NOVA 涩涩模块），反派心态流。（★★☆☆☆） |
| 源码路径 | `modular_nova/modules/modular_items/lewd_items/code/lewd_quirks.dm` |

<a id="datum/quirk/ropebunny"></a>
### 绳缚爱好者 · Rope bunny 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 受试者对束缚物有特殊偏好。 |
| 效果 | 你热爱被捆绑的感觉。 |
| 代价/加成 | 情绪向：被束缚时心情愉悦。 |
| 点评 | R18 向 RP 特质（NOVA 涩涩模块）。（★★☆☆☆） |
| 源码路径 | `modular_nova/modules/modular_items/lewd_items/code/lewd_quirks.dm` |

<a id="datum/quirk/rigger"></a>
### 绳艺师 · Rigger 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 受试者打结时灵巧度更高。 |
| 效果 | 你觉得在身体上编织绳结美妙极了。 |
| 代价/加成 | 增益：打结/绳索操作更灵巧。 |
| 点评 | R18 向 RP 特质（NOVA 涩涩模块）。（★★☆☆☆） |
| 源码路径 | `modular_nova/modules/modular_items/lewd_items/code/lewd_quirks.dm` |

## 三、负面特质（Negative Quirks）

> 负面特质返还配点（value < 0），以代价换取更多点数，用于购买正面特质。

共 **72** 个特质：

- [熬夜冠军（All Nighter）](#datum/quirk/all_nighter)
- [腰背劳损（Bad Back）](#datum/quirk/badback)
- [血液缺乏症（Blood Deficiency）](#datum/quirk/blooddeficiency)
- [身体纯粹主义者（Body Purist）](#datum/quirk/body_purist)
- [幽闭恐惧症（Claustrophobia）](#datum/quirk/claustrophobia)
- [笨手笨脚（Clumsy）](#datum/quirk/clumsy)
- [通用语非母语（Common Second Language）](#datum/quirk/csl)
- [厄运缠身（Cursed）](#datum/quirk/cursed)
- [抑郁症（Depression）](#datum/quirk/depression)
- [情绪不稳（Erratic）](#datum/quirk/erratic)
- [脆弱体质（Frail）](#datum/quirk/frail)
- [玻璃下巴（Glass Jaw）](#datum/quirk/glass_jaw)
- [半身瘫痪（Hemiplegic）](#datum/quirk/hemiplegic)
- [饿鬼附体（Hungry）](#datum/quirk/hungry)
- [高敏感（Hypersensitive）](#datum/quirk/hypersensitive)
- [文盲（Illiterate）](#datum/quirk/illiterate)
- [负债累累（Indebted）](#datum/quirk/indebted)
- [现实解离综合征（Reality Dissociation Syndrome）](#datum/quirk/insanity)
- [感官处理故障（Sensory Processing Fault）](#datum/quirk/insanity/synth)
- [酒鬼（Alcoholic）](#datum/quirk/item_quirk/addict/alcoholic)
- [瘾君子（Junkie）](#datum/quirk/item_quirk/addict/junkie)
- [烟鬼（Smoker）](#datum/quirk/item_quirk/addict/smoker)
- [极端药物过敏（Extreme Medicine Allergy）](#datum/quirk/item_quirk/allergic)
- [嗅觉缺失（Anosmia）](#datum/quirk/item_quirk/anosmia)
- [哮喘（Asthma）](#datum/quirk/item_quirk/asthma)
- [失明（Blind）](#datum/quirk/item_quirk/blindness)
- [脑瘤（Brain Tumor）](#datum/quirk/item_quirk/brainproblems)
- [正电子级联异常（Positronic Cascade Anomaly）](#datum/quirk/item_quirk/brainproblems/synth)
- [灭绝性慢性病（Eradicative Chronic Illness）](#datum/quirk/item_quirk/chronic_illness)
- [耳聋（Deaf）](#datum/quirk/item_quirk/deafness)
- [传家宝（Family Heirloom）](#datum/quirk/item_quirk/family_heirloom)
- [闪光凝视（Fluoride Stare）](#datum/quirk/item_quirk/fluoride_stare)
- [食物过敏（Food Allergy）](#datum/quirk/item_quirk/food_allergic)
- [免疫缺陷（Immunodeficiency）](#datum/quirk/item_quirk/immunodeficiency)
- [一杯倒（Light Drinker）](#datum/quirk/light_drinker)
- [孤独恐惧症（Monophobia）](#datum/quirk/monophobia)
- [哑巴（Mute）](#datum/quirk/mute)
- [和平主义者（Pacifist）](#datum/quirk/nonviolent)
- [痛觉缺失（Numb）](#datum/quirk/numb)
- [黑暗恐惧症（Nyctophobia）](#datum/quirk/nyctophobia)
- [截瘫（Paraplegic）](#datum/quirk/paraplegic)
- [畏光症（Photophobia）](#datum/quirk/photophobia)
- [暴风兵枪法（Stormtrooper Aim）](#datum/quirk/poor_aim)
- [面容失认症（Prosopagnosia）](#datum/quirk/prosopagnosia)
- [义肢（Prosthetic Limb）](#datum/quirk/prosthetic_limb)
- [人造器官（Prosthetic Organ）](#datum/quirk/prosthetic_organ)
- [软柿子（Pushover）](#datum/quirk/pushover)
- [四肢截肢（Quadruple Amputee）](#datum/quirk/quadruple_amputee)
- [社交焦虑（Social Anxiety）](#datum/quirk/social_anxiety)
- [细声细气（Soft-Spoken）](#datum/quirk/softspoken)
- [铁皮人（Tin Man）](#datum/quirk/tin_man)
- [触觉依赖（Touchy）](#datum/quirk/touchy)
- [精神不稳（Unstable）](#datum/quirk/unstable)
- [述情障碍（Alexithymia）](#datum/quirk/alexithymia)
- [脆骨症（Fragility）](#datum/quirk/fragile)
- [禁用枪械（No Guns）](#datum/quirk/no_guns)
- [沉睡者（Heavy Sleeper）](#datum/quirk/heavy_sleeper)
- [神经钉（Nerve Stapled）](#datum/quirk/equipping/nerve_staple)
- [访客身份（Visitor ID）](#datum/quirk/visitor)
- [死亡退化症（Death Degradation Disorder）](#datum/quirk/death_consequences)
- [恐水症（Hydrophobia）](#datum/quirk/hydrophobia)
- [跛腿（Limp Leg）](#datum/quirk/item_quirk/limp_leg)
- [神经性手抖（Nervous Tremble）](#datum/quirk/nervous_aim)
- [系统休克（System Shock）](#datum/quirk/system_shock)
- [偷窃癖（Kleptomaniac）](#datum/quirk/kleptomaniac)
- [占有欲（Possessive）](#datum/quirk/possessive)
- [活体棺葬（Entombed）](#datum/quirk/equipping/entombed)
- [嗜睡症（Narcolepsy）](#datum/quirk/item_quirk/narcolepsy)
- [伪中断错误（Spurious Interrupt Error）](#datum/quirk/item_quirk/narcolepsy/synth)
- [近视（Nearsighted）](#datum/quirk/item_quirk/nearsighted)
- [伤疤眼（Scarred Eye）](#datum/quirk/item_quirk/scarred_eye)
- [跛行者（Limper）](#datum/quirk/item_quirk/limper)

<a id="datum/quirk/all_nighter"></a>
### 熬夜冠军 · All Nighter

| 项目 | 内容 |
|---|---|
| 配点评级 | `-4` |
| quirk_flags | 仅人类、改变外观、心情类、持续处理 |
| 医疗记录 | 患者疑似长期睡眠不足。 |
| 效果 | 你昨晚根本没睡，谁都能看出来！你会一直心情糟糕、睡得更久。兴奋剂或小憩或许能帮上忙。 |
| 代价/加成 | 代价：常驻坏心情+嗜睡；可用兴奋剂/补觉缓解。 |
| 点评 | 换点利器，自虐流开局。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/all_nighter.dm` |

<a id="datum/quirk/badback"></a>
### 腰背劳损 · Bad Back

| 项目 | 内容 |
|---|---|
| 配点评级 | `-8` |
| quirk_flags | 仅人类、心情类 |
| 医疗记录 | 扫描显示患者患有严重的慢性背痛。 |
| 效果 | 拜糟糕的姿势所赐，背包和其他袋子永远背不舒服。重量分布均匀的物品则没事。 |
| 代价/加成 | 代价：背包/挎包类物品佩戴不适（心情惩罚）；腰部挂载物品受限。 |
| 点评 | 后勤职业慎选，背包流劝退。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/bad_back.dm` |

<a id="datum/quirk/blooddeficiency"></a>
### 血液缺乏症 · Blood Deficiency

| 项目 | 内容 |
|---|---|
| 配点评级 | `-8` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者因造血不足需定期接受失血治疗。 |
| 效果 | 你的身体无法产生足够的血液来维持自身。 |
| 代价/加成 | 代价：持续缓慢失血，需定期补血（铁剂/输血）。 |
| 点评 | 医疗开销大户，血包专业户。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/blood_deficiency.dm` |

<a id="datum/quirk/body_purist"></a>
### 身体纯粹主义者 · Body Purist

| 项目 | 内容 |
|---|---|
| 配点评级 | `-2` |
| quirk_flags | 仅人类、心情类 |
| 医疗记录 | 患者公开表示极度憎恨非自然身体部件与改造。 |
| 效果 | 你坚信身体是圣殿、天然形态是完美的化身，因此厌恶任何非自然的部件——机械义体、假肢等一切改造。 |
| 代价/加成 | 代价：身上有义体/假肢时心情持续变差。 |
| 点评 | 与义体流/超人类主义者互斥，纯 RP 向。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/body_purist.dm` |

<a id="datum/quirk/claustrophobia"></a>
### 幽闭恐惧症 · Claustrophobia

| 项目 | 内容 |
|---|---|
| 配点评级 | `-4` |
| quirk_flags | 仅人类、类脑伤 |
| 医疗记录 | 患者表现出对狭小空间的恐惧。 |
| 效果 | 你极度恐惧狭小空间：被放进任何容器、储物柜或机器内部时，会触发惊恐发作并呼吸困难。 |
| 代价/加成 | 代价：被关容器/柜子会恐慌、喘不上气。 |
| 点评 | 被绑架/恶作剧时体验加倍刺激。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/claustrophobia.dm` |

<a id="datum/quirk/clumsy"></a>
### 笨手笨脚 · Clumsy

| 项目 | 内容 |
|---|---|
| 配点评级 | `-8` |
| quirk_flags | 类脑伤 |
| 医疗记录 | 患者表现出极高的精细运动难度与批判性思维缺失。 |
| 效果 | 你就是个笨手笨脚的活宝。别指望你的双手能胜任任何需要一丁点灵巧度的工作。 |
| 代价/加成 | 代价：精细操作（拆弹、手术、上膛等）容易翻车。 |
| 点评 | 喜剧核心，锅从天降常客。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/clumsy.dm` |

<a id="datum/quirk/csl"></a>
### 通用语非母语 · Common Second Language

| 项目 | 内容 |
|---|---|
| 配点评级 | `-2` |
| quirk_flags | 隐藏扫描(不显示于健康扫描) |
| 医疗记录 | 患者为 CSL（通用语第二语言使用者）。 |
| 效果 | 通用语不是你的母语——是后来才学会的。某些通用语词汇在你耳中很生疏，焦虑或激动时你可能会滑回母语。 |
| 代价/加成 | 代价：通用语理解有障碍，情绪激动时说出母语。 |
| 点评 | 外星 RP 流，语言障碍扮演。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/csl.dm` |

<a id="datum/quirk/cursed"></a>
### 厄运缠身 · Cursed

| 项目 | 内容 |
|---|---|
| 配点评级 | `-8` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者被厄运诅咒。 |
| 效果 | 你被坏运气诅咒：更容易遭遇事故和不幸。屋漏偏逢连夜雨。 |
| 代价/加成 | 代价：随机事故/坏事件概率大幅提升。 |
| 点评 | 全自动喜剧发生器，灾难片主角。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/cursed.dm` |

<a id="datum/quirk/depression"></a>
### 抑郁症 · Depression

| 项目 | 内容 |
|---|---|
| 配点评级 | `-3` |
| quirk_flags | 仅人类、心情类、持续处理、类脑伤 |
| 医疗记录 | 患者患有轻度情绪障碍，会周期性发作急性抑郁。 |
| 效果 | 你有时就是讨厌生活。 |
| 代价/加成 | 代价：周期性心情低谷。 |
| 点评 | 与乐天派互斥，心情流反向。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/depression.dm` |

<a id="datum/quirk/erratic"></a>
### 情绪不稳 · Erratic

| 项目 | 内容 |
|---|---|
| 配点评级 | `-3` |
| quirk_flags | 仅人类、心情类、持续处理、类脑伤 |
| 医疗记录 | 患者患有双相人格障碍。 |
| 效果 | 你的情绪像钟摆一样摇摆，时不时性格突变。 |
| 代价/加成 | 代价：随机人格切换（影响言行）。 |
| 点评 | 演技派 RP 最爱，一人千面。（★★★☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/erratic.dm` |

<a id="datum/quirk/frail"></a>
### 脆弱体质 · Frail

| 项目 | 内容 |
|---|---|
| 配点评级 | `-6` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者极易受伤，请竭尽所能避免医疗事故诉讼。 |
| 效果 | 你有纸一样的皮肤和玻璃一样的骨头！比大多数人更容易受伤。 |
| 代价/加成 | 代价：受到伤害时更容易造成伤口。 |
| 点评 | 玻璃人设，医生最怕的病人。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/frail.dm` |

<a id="datum/quirk/glass_jaw"></a>
### 玻璃下巴 · Glass Jaw

| 项目 | 内容 |
|---|---|
| 配点评级 | `-4` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者极易被打晕，请勿让其靠近拳击场。 |
| 效果 | 你的下巴非常脆弱，任何足够用力的头部打击都可能让你昏过去。 |
| 代价/加成 | 代价：头部受击更容易被击晕。 |
| 点评 | 近战职业的致命短板。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/glass_jaw.dm` |

<a id="datum/quirk/hemiplegic"></a>
### 半身瘫痪 · Hemiplegic

| 项目 | 内容 |
|---|---|
| 配点评级 | `-10` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者半边身体存在不可治愈的运动功能障碍。 |
| 效果 | 你的半边身体无法工作，任何方法都无法修复。 |
| 代价/加成 | 代价：半身瘫痪（一侧手脚残疾）。 |
| 点评 | 重度残疾扮演流，轮椅兄弟。（★☆☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/hemiplegic.dm` |

<a id="datum/quirk/hungry"></a>
### 饿鬼附体 · Hungry

| 项目 | 内容 |
|---|---|
| 配点评级 | `-2` |
| quirk_flags | 仅人类 |
| 医疗记录 | 患者饥饿速度远快于常人。 |
| 效果 | 你食欲无穷无尽，胃是个无底洞，需要比常人吃多得多才能压住饥饿。 |
| 代价/加成 | 代价：饥饿速度翻倍，吃喝开销大。 |
| 点评 | 吃货反向版，干饭成本翻倍。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/hungry.dm` |

<a id="datum/quirk/hypersensitive"></a>
### 高敏感 · Hypersensitive

| 项目 | 内容 |
|---|---|
| 配点评级 | `-2` |
| quirk_flags | 类脑伤 |
| 医疗记录 | 患者表现出高水平的情绪波动性。 |
| 效果 | 无论好坏，一切事物对你心情的影响都远超常人。 |
| 代价/加成 | 代价：心情波动幅度翻倍（好更好、坏更坏）。 |
| 点评 | 情绪过山车，配抑郁/乐天服用效果更佳。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/hypersensitive.dm` |

<a id="datum/quirk/illiterate"></a>
### 文盲 · Illiterate

| 项目 | 内容 |
|---|---|
| 配点评级 | `-8` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者不识字。 |
| 效果 | 你辍学了，无法读写。这影响阅读、书写、使用电脑和其他电子设备。 |
| 代价/加成 | 代价：无法读写文字、操作电脑/电子设备。 |
| 点评 | 硬核生存挑战，现代文盲。（★☆☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/illiterate.dm` |

<a id="datum/quirk/indebted"></a>
### 负债累累 · Indebted

| 项目 | 内容 |
|---|---|
| 配点评级 | `-2` |
| quirk_flags | 仅人类、隐藏扫描 |
| 医疗记录 | 唉，患者连体检费都凑不出来。 |
| 效果 | 糟糕的人生决策、医疗账单、学生贷款——不管因为什么，你欠了一大笔债。你收入的一部分将持续用于还债。 |
| 代价/加成 | 代价：工资被扣一部分还债。 |
| 点评 | 穷鬼人设，开局负债。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/indebted.dm` |

<a id="datum/quirk/insanity"></a>
### 现实解离综合征 · Reality Dissociation Syndrome

| 项目 | 内容 |
|---|---|
| 配点评级 | `-8` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者患有急性现实解离综合征，会出现生动幻觉，说话可能困难。 |
| 效果 | 你患有严重的妄想障碍，会产生非常逼真的幻觉，表达想法也有困难。心灵破坏毒素可抑制其效果，且你免疫心灵破坏的致幻特性。注意：这不是恶意搞事的许可证。 |
| 代价/加成 | 代价：随机幻觉+言语障碍；心灵破坏毒素可压制。 |
| 点评 | 高难度 RP 向，慎选慎玩。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/insanity.dm` |

<a id="datum/quirk/insanity/synth"></a>
### 感官处理故障 · Sensory Processing Fault 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `-8 (继承)` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者发生类似现实解离综合征的故障，会出现生动幻觉，说话可能困难。 |
| 效果 | （合成人版妄想症）你的处理器出现感官处理故障，症状类似现实解离综合征。 |
| 代价/加成 | 代价：同妄想症，但为机械故障原因。 |
| 点评 | 合成人专属版妄想症。（★★☆☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/quirks/negative_quirks/insanity.dm` |

<a id="datum/quirk/item_quirk/addict/alcoholic"></a>
### 酒鬼 · Alcoholic

| 项目 | 内容 |
|---|---|
| 配点评级 | `-4` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者是一名酒鬼。 |
| 效果 | 你离不开酒精。你的肝脏是把乙醇变成乙醛的机器。 |
| 代价/加成 | 代价：酒精成瘾，不喝难受（会强制寻找酒精）。 |
| 点评 | 开局自带酒类补给，瘾君子 RP。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/addict.dm` |

<a id="datum/quirk/item_quirk/addict/junkie"></a>
### 瘾君子 · Junkie

| 项目 | 内容 |
|---|---|
| 配点评级 | `-6` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者有硬性毒品史。 |
| 效果 | 你对硬毒品欲罢不能。 |
| 代价/加成 | 代价：药物成瘾，需定期嗑药；开局带部分补给。 |
| 点评 | 高危 RP，医疗系统常客。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/addict.dm` |

<a id="datum/quirk/item_quirk/addict/smoker"></a>
### 烟鬼 · Smoker

| 项目 | 内容 |
|---|---|
| 配点评级 | `-4` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者目前吸烟。 |
| 效果 | 有时候你就是想抽根烟。可能对你的肺不太好。 |
| 代价/加成 | 代价：尼古丁成瘾，需定期吸烟。 |
| 点评 | 烟草公司最爱的员工。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/addict.dm` |

<a id="datum/quirk/item_quirk/allergic"></a>
### 极端药物过敏 · Extreme Medicine Allergy

| 项目 | 内容 |
|---|---|
| 配点评级 | `-6` |
| quirk_flags | 仅人类、持续处理 |
| 医疗记录 | 患者的免疫系统对某些化学物质反应剧烈。 |
| 效果 | 从小你就对某些化学物质过敏（开局自定义选择过敏药物）。 |
| 代价/加成 | 代价：特定药物=剧毒，医疗时需避开。 |
| 点评 | 医生杀手，自选死因。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/allergic.dm` |

<a id="datum/quirk/item_quirk/anosmia"></a>
### 嗅觉缺失 · Anosmia

| 项目 | 内容 |
|---|---|
| 配点评级 | `-2` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者丧失了嗅觉。 |
| 效果 | 不知为何，你什么味道都闻不到。 |
| 代价/加成 | 代价：无法闻味（闻不到毒气/食物气味）。 |
| 点评 | 与灵敏嗅觉互斥，安全感知下降。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/anosmia.dm` |

<a id="datum/quirk/item_quirk/asthma"></a>
### 哮喘 · Asthma

| 项目 | 内容 |
|---|---|
| 配点评级 | `-4` |
| quirk_flags | 仅人类 |
| 医疗记录 | 患者患有哮喘。 |
| 效果 | 你患有哮喘——一种让气管收缩的炎症性疾病！远离烟雾！ |
| 代价/加成 | 代价：烟雾/粉尘环境触发哮喘发作（呼吸困难）；可带吸入器缓解。 |
| 点评 | 烟雾缭绕区慎入，自带吸入器。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/asthma.dm` |

<a id="datum/quirk/item_quirk/blindness"></a>
### 失明 · Blind

| 项目 | 内容 |
|---|---|
| 配点评级 | `-16` |
| quirk_flags | 仅人类、改变外观 |
| 医疗记录 | 患者永久性失明。 |
| 效果 | 你完全失明，任何方法都无法逆转。 |
| 代价/加成 | 代价：永久全盲；开局赠送白盲杖+墨镜。 |
| 点评 | 最大代价特质，硬核挑战流。（★☆☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/blindness.dm` |

<a id="datum/quirk/item_quirk/brainproblems"></a>
### 脑瘤 · Brain Tumor

| 项目 | 内容 |
|---|---|
| 配点评级 | `-12` |
| quirk_flags | 仅人类、持续处理 |
| 医疗记录 | 患者脑内有肿瘤，正缓慢走向脑死亡。 |
| 效果 | 你的大脑里有个'小伙伴'正在慢慢摧毁它。最好带点甘露醇！ |
| 代价/加成 | 代价：脑损伤持续累积，需定期注射甘露醇治疗。 |
| 点评 | 医疗开销巨大，行走的定时炸弹。（★☆☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/brain_problems.dm` |

<a id="datum/quirk/item_quirk/brainproblems/synth"></a>
### 正电子级联异常 · Positronic Cascade Anomaly 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `-12 (继承)` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者的[合成脑]出现故障，正缓慢导致脑死亡。 |
| 效果 | （合成人版脑瘤）你的正电子脑出现级联故障，缓慢走向脑死亡。 |
| 代价/加成 | 代价：同脑瘤，机械版。 |
| 点评 | 合成人专属版脑瘤。（★☆☆☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/quirks/negative_quirks/brainproblems.dm` |

<a id="datum/quirk/item_quirk/chronic_illness"></a>
### 灭绝性慢性病 · Eradicative Chronic Illness

| 项目 | 内容 |
|---|---|
| 配点评级 | `-12` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者患有需要持续服药控制的异常慢性病。 |
| 效果 | 你患有一种异常的慢性病，需要持续服药控制，否则会触发时间流修正。 |
| 代价/加成 | 代价：断药会触发'时间流修正'（消失/重置类惩罚）。 |
| 点评 | 吃药不能停，断药=人间蒸发。（★☆☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/chronic_illness.dm` |

<a id="datum/quirk/item_quirk/deafness"></a>
### 耳聋 · Deaf

| 项目 | 内容 |
|---|---|
| 配点评级 | `-8` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者的听神经已不可治愈地损伤。 |
| 效果 | 你永久失聪。 |
| 代价/加成 | 代价：完全听不到声音；可配合手语者特质。 |
| 点评 | 无声世界挑战流，配手语队友食用。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/deafness.dm` |

<a id="datum/quirk/item_quirk/family_heirloom"></a>
### 传家宝 · Family Heirloom

| 项目 | 内容 |
|---|---|
| 配点评级 | `-2` |
| quirk_flags | 仅人类、持续处理、心情类 |
| 医疗记录 | 患者对一件传家宝表现出异常的依恋。 |
| 效果 | 你是一件世代相传的传家宝的现任主人，必须保护好它！ |
| 代价/加成 | 代价：传家宝丢失/损坏时心情崩溃；开局带一件随机传家宝。 |
| 点评 | 责任心训练器，丢宝=心态爆炸。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/family_heirloom.dm` |

<a id="datum/quirk/item_quirk/fluoride_stare"></a>
### 闪光凝视 · Fluoride Stare

| 项目 | 内容 |
|---|---|
| 配点评级 | `-6` |
| quirk_flags | 仅人类 |
| 医疗记录 | 患者在一次可怕的事故中失去了眼睑。 |
| 效果 | 你在一次可怕的事故中失去了眼睑（至少你是这么说的）。你需要时不时用生理盐水手动湿润眼睛！ |
| 代价/加成 | 代价：无法眨眼，需定期滴眼药水保湿。 |
| 点评 | 恐怖片眼神，与不眨眼互斥。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/fluoride_stare.dm` |

<a id="datum/quirk/item_quirk/food_allergic"></a>
### 食物过敏 · Food Allergy

| 项目 | 内容 |
|---|---|
| 配点评级 | `-2` |
| quirk_flags | 仅人类 |
| 医疗记录 | 患者的免疫系统对某些食物反应剧烈。 |
| 效果 | 从小你就对某些食物过敏（开局自定义选择过敏食物）。 |
| 代价/加成 | 代价：特定食物=中毒反应。 |
| 点评 | 厨师噩梦，自助餐刺客。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/food_allergy.dm` |

<a id="datum/quirk/item_quirk/immunodeficiency"></a>
### 免疫缺陷 · Immunodeficiency

| 项目 | 内容 |
|---|---|
| 配点评级 | `-10` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者患有慢性免疫缺陷。 |
| 效果 | 无论慢性病还是基因使然，你的身体是细菌、病毒和寄生虫的 24/7 旅馆。即使按规定使用免疫增强剂，你也会比别人病得更重。 |
| 代价/加成 | 代价：感染/疾病抵抗力大幅下降。 |
| 点评 | 行走的病原体培养皿，医生常客。（★☆☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/immunodeficiency.dm` |

<a id="datum/quirk/light_drinker"></a>
### 一杯倒 · Light Drinker

| 项目 | 内容 |
|---|---|
| 配点评级 | `-2` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者对酒精耐受性低。（怂包） |
| 效果 | 你根本喝不了酒，很快就会醉。 |
| 代价/加成 | 代价：极易醉酒。 |
| 点评 | 与酒精耐受互斥，酒吧战五渣。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/light_drinker.dm` |

<a id="datum/quirk/monophobia"></a>
### 孤独恐惧症 · Monophobia

| 项目 | 内容 |
|---|---|
| 配点评级 | `-3` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者极度恐惧独处。 |
| 效果 | 你极度害怕孤独，总是试图待在人群中。 |
| 代价/加成 | 代价：独处时恐慌/心情恶化。 |
| 点评 | 群居动物，落单就慌。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/monophobia.dm` |

<a id="datum/quirk/mute"></a>
### 哑巴 · Mute

| 项目 | 内容 |
|---|---|
| 配点评级 | `-4` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者完全无法使用嗓音。 |
| 效果 | 出于某种原因，你完全无法说话。 |
| 代价/加成 | 代价：无法发声交流；可配合手语/打字。 |
| 点评 | 无声 RP 挑战，打字侠上线。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/mute.dm` |

<a id="datum/quirk/nonviolent"></a>
### 和平主义者 · Pacifist

| 项目 | 内容 |
|---|---|
| 配点评级 | `-8` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者异常和平主义，无法对他人造成身体伤害。 |
| 效果 | 暴力的念头让你恶心——恶心到根本无法伤害任何人。 |
| 代价/加成 | 代价：无法主动攻击/伤害他人（防身也不行）。 |
| 点评 | 佛系玩家，纯防守流。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/non_violent.dm` |

<a id="datum/quirk/numb"></a>
### 痛觉缺失 · Numb

| 项目 | 内容 |
|---|---|
| 配点评级 | `-4` |
| quirk_flags | 类脑伤 |
| 医疗记录 | 患者患有先天性痛觉减退，对疼痛刺激不敏感。 |
| 效果 | 你完全感觉不到疼痛。 |
| 代价/加成 | 代价：受伤时无痛觉反馈（容易忽视致命伤）。 |
| 点评 | 双刃剑：不怕疼但也感知不到危险。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/numb.dm` |

<a id="datum/quirk/nyctophobia"></a>
### 黑暗恐惧症 · Nyctophobia

| 项目 | 内容 |
|---|---|
| 配点评级 | `-3` |
| quirk_flags | 仅人类、类脑伤 |
| 医疗记录 | 患者表现出对黑暗的恐惧。（认真的？） |
| 效果 | 从记事起你就怕黑。在无光源的黑暗中，你会本能地谨慎行动，并持续感到恐惧。 |
| 代价/加成 | 代价：黑暗中行动受限+持续恐惧。 |
| 点评 | 怕黑星人，夜班劝退。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/nyctophobia.dm` |

<a id="datum/quirk/paraplegic"></a>
### 截瘫 · Paraplegic

| 项目 | 内容 |
|---|---|
| 配点评级 | `-12` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者下肢存在不可治愈的运动功能障碍。 |
| 效果 | 你的双腿无法工作，任何方法都无法修复。不过，免费轮椅！ |
| 代价/加成 | 代价：双腿瘫痪，无法行走；开局赠送轮椅。 |
| 点评 | 轮椅战士，机动性全靠轮椅。（★☆☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/paraplegic.dm` |

<a id="datum/quirk/photophobia"></a>
### 畏光症 · Photophobia

| 项目 | 内容 |
|---|---|
| 配点评级 | `-4` |
| quirk_flags | 仅人类、类脑伤 |
| 医疗记录 | 患者对光有急性恐惧，并坚称光线对其身体有害。 |
| 效果 | 明亮的光线对你的困扰远超常人，也许是一种医学状况。 |
| 代价/加成 | 代价：强光环境不适/视野受损。 |
| 点评 | 暗夜行者，白天地狱。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/photophobia.dm` |

<a id="datum/quirk/poor_aim"></a>
### 暴风兵枪法 · Stormtrooper Aim

| 项目 | 内容 |
|---|---|
| 配点评级 | `-4` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者双手存在强烈震颤。 |
| 效果 | 你这辈子从没打中过瞄准的目标。 |
| 代价/加成 | 代价：射击准度大幅下降。 |
| 点评 | 枪战游戏反向天赋，喜剧担当。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/poor_aim.dm` |

<a id="datum/quirk/prosopagnosia"></a>
### 面容失认症 · Prosopagnosia

| 项目 | 内容 |
|---|---|
| 配点评级 | `-4` |
| quirk_flags | 仅人类、类脑伤 |
| 医疗记录 | 患者患有面容失认症，无法识别面孔。 |
| 效果 | 你患有精神障碍，完全无法识别面孔。 |
| 代价/加成 | 代价：认不出任何人（通过外观识别他人失效）。 |
| 点评 | 人脸识别终结者，全靠衣服认人。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/prosopagnosia.dm` |

<a id="datum/quirk/prosthetic_limb"></a>
### 义肢 · Prosthetic Limb

| 项目 | 内容 |
|---|---|
| 配点评级 | `-3` |
| quirk_flags | 仅人类、改变外观 |
| 医疗记录 | 患者在[部位]使用廉价义肢。 |
| 效果 | 一场事故让你失去了一条肢体，因此你现在有一条'过剩'的廉价义肢！ |
| 代价/加成 | 代价：某部位为廉价义肢（性能低于原生）；可自定义部位。 |
| 点评 | 赛博格流起点，机械改造前置。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/prosthetic_limb.dm` |

<a id="datum/quirk/prosthetic_organ"></a>
### 人造器官 · Prosthetic Organ

| 项目 | 内容 |
|---|---|
| 配点评级 | `-3` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 体检发现患者体内有廉价人造器官。 |
| 效果 | 一场事故让你失去了一个器官，因此你现在有一个'过剩'的廉价人造器官！ |
| 代价/加成 | 代价：某器官为廉价人造器官（需维护/性能较低）。 |
| 点评 | 机械内脏流，器官可换新。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/prosthetic_organ.dm` |

<a id="datum/quirk/pushover"></a>
### 软柿子 · Pushover

| 项目 | 内容 |
|---|---|
| 配点评级 | `-8` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者表现出明显的不自信人格，容易被操控。 |
| 效果 | 你的第一反应永远是任人摆布。挣脱擒抱明显更困难。 |
| 代价/加成 | 代价：被擒抱/控制时更难挣脱。 |
| 点评 | 社交废人设，被抓就认命。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/pushover.dm` |

<a id="datum/quirk/quadruple_amputee"></a>
### 四肢截肢 · Quadruple Amputee

| 项目 | 内容 |
|---|---|
| 配点评级 | `-6` |
| quirk_flags | 仅人类、改变外观 |
| 医疗记录 | 体检发现患者四肢均为廉价义肢。 |
| 效果 | 哎呀！全是义肢！由于某种残酷的宇宙惩罚，你的四肢全部被换成了廉价义肢。 |
| 代价/加成 | 代价：四肢全是廉价义肢（性能低于原生）。 |
| 点评 | 机械飞升第一步，全身义体化。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/quadruple_amputee.dm` |

<a id="datum/quirk/social_anxiety"></a>
### 社交焦虑 · Social Anxiety

| 项目 | 内容 |
|---|---|
| 配点评级 | `-3` |
| quirk_flags | 仅人类、类脑伤 |
| 医疗记录 | 患者在社交场合通常焦虑并倾向于回避。 |
| 效果 | 与人交谈对你来说非常困难，你常常僵住——尤其是被飞吻或偶然对视时。 |
| 代价/加成 | 代价：社交互动触发焦虑反应。 |
| 点评 | 社恐人本人，i 人鉴定器。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/social_anxiety.dm` |

<a id="datum/quirk/softspoken"></a>
### 细声细气 · Soft-Spoken

| 项目 | 内容 |
|---|---|
| 配点评级 | `-2` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者说话细声细气，难以听清。 |
| 效果 | 你说话轻声细语，声音很难被听到。 |
| 代价/加成 | 代价：语音音量降低（远处/嘈杂环境听不清）。 |
| 点评 | 文静人设，说话靠喊。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/softspoken.dm` |

<a id="datum/quirk/tin_man"></a>
### 铁皮人 · Tin Man

| 项目 | 内容 |
|---|---|
| 配点评级 | `-6` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 体检发现患者体内有大量廉价人造内脏器官。 |
| 效果 | 哎呀！全是人造的！由于某种残酷的宇宙惩罚，你的大部分内脏器官都被换成了廉价人造器官。 |
| 代价/加成 | 代价：多个内脏为廉价人造器官（需维护）。 |
| 点评 | 机械内脏套餐，进击的机器人。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/tin_man.dm` |

<a id="datum/quirk/touchy"></a>
### 触觉依赖 · Touchy

| 项目 | 内容 |
|---|---|
| 配点评级 | `-2` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者无法远距离分辨物体。 |
| 效果 | 你非常'手贱'：必须亲手摸到东西才能完成检视。 |
| 代价/加成 | 代价：远程检视（隔空查看物品信息）不可用。 |
| 点评 | 近战检视流，扫描全靠摸。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/unusual.dm` |

<a id="datum/quirk/unstable"></a>
### 精神不稳 · Unstable

| 项目 | 内容 |
|---|---|
| 配点评级 | `-10` |
| quirk_flags | 类脑伤 |
| 医疗记录 | 患者心智处于脆弱状态，无法从创伤事件中恢复。 |
| 效果 | 由于过往的麻烦，一旦失去理智就再也无法恢复。请非常小心地管理心情！ |
| 代价/加成 | 代价：理智崩溃后永久疯狂，无法自愈。 |
| 点评 | 高压环境慎选，疯一次就完了。（★☆☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/unstable.dm` |

<a id="datum/quirk/alexithymia"></a>
### 述情障碍 · Alexithymia 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `-4` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者无法表达自己的情绪。 |
| 效果 | 你无法准确评估自己的感受。 |
| 代价/加成 | 代价：无法查看自己当前的心情状态。 |
| 点评 | 情绪盲盒，心情系统黑屏。（★★☆☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/negative.dm` |

<a id="datum/quirk/fragile"></a>
### 脆骨症 · Fragility 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `-6` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者的身体适应了低重力，而低重力环境不利于骨骼发育。 |
| 效果 | 你感觉自己无比脆弱，烧伤和瘀伤比常人更疼！ |
| 代价/加成 | 代价：伤害承受更差（烧伤/瘀伤加成）。 |
| 点评 | 与脆弱体质类似的玻璃人，低重力出身设定。（★★☆☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/negative.dm` |

<a id="datum/quirk/no_guns"></a>
### 禁用枪械 · No Guns 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `-6` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者无法使用枪械，原因不明。 |
| 效果 | 出于某种原因（原因由你自定），你完全无法使用枪械。 |
| 代价/加成 | 代价：无法持枪/开枪。 |
| 点评 | 反枪人设，冷兵器爱好者。（★★☆☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/traits/negative.dm` |

<a id="datum/quirk/heavy_sleeper"></a>
### 沉睡者 · Heavy Sleeper 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `-2` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者睡眠研究结果异常，难以唤醒。 |
| 效果 | 你睡得像个死人！每次被麻醉或打晕，都需要更长时间才能醒来。 |
| 代价/加成 | 代价：昏迷/睡眠后苏醒时间延长。 |
| 点评 | 麻醉抵抗者，睡神转世。（★★☆☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/quirks/negative_quirks/heavy_sleeper.dm` |

<a id="datum/quirk/equipping/nerve_staple"></a>
### 神经钉 · Nerve Stapled 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `-10` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者被神经钉钉住，无法伤害他人。 |
| 效果 | 你是和平主义者——不是出于自愿，而是因为钉在眼睛里的装置。 |
| 代价/加成 | 代价：无法伤害他人（强制和平）。 |
| 点评 | 强制和平协议，奴役 RP 道具。（★☆☆☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/quirks/negative_quirks/nerve_staple.dm` |

<a id="datum/quirk/visitor"></a>
### 访客身份 · Visitor ID 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `-2` |
| quirk_flags | 隐藏扫描(不显示于健康扫描) |
| 医疗记录 | 患者是空间站访客，持有访客 ID。 |
| 效果 | 作为助手，你拿到的是信息有限的访客 ID。你的档案很简略，需要在当班期间自行补全。 |
| 代价/加成 | 代价：初始档案信息稀少；身份受限。 |
| 点评 | 神秘人设，失忆开局。（★★☆☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/quirks/negative_quirks/visitor/code/visitor.dm` |

<a id="datum/quirk/death_consequences"></a>
### 死亡退化症 · Death Degradation Disorder 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 死亡会对身体造成难以修复的长期损伤。 |
| 效果 | 每次死亡，你的身体都会累积难以修复的长期损伤。 |
| 代价/加成 | 代价：死亡惩罚累积（复活后身体越来越残）。 |
| 点评 | 死亡惩罚加深，硬核模式伴侣。（★☆☆☆☆） |
| 源码路径 | `modular_nova/modules/death_consequences_perk/death_consequences.dm` |

<a id="datum/quirk/hydrophobia"></a>
### 恐水症 · Hydrophobia 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `-2` |
| quirk_flags | 仅人类 |
| 医疗记录 | 患者患有恐水症，对水源表现出极度焦虑。 |
| 效果 | 你极度恐惧水！对史莱姆混血而言，这也意味着你无法排斥水分。 |
| 代价/加成 | 代价：靠近水/遇水触发恐惧反应。 |
| 点评 | 与水呼吸互斥，洗澡困难户。（★★☆☆☆） |
| 源码路径 | `modular_nova/modules/quirk_hydrophobia/hydrophobia.dm` |

<a id="datum/quirk/item_quirk/limp_leg"></a>
### 跛腿 · Limp Leg 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `-4` |
| quirk_flags | 仅人类 |
| 医疗记录 | 患者因跛腿行动能力受损。 |
| 效果 | 出于某种原因，你的一条腿是瘸的。拐杖能帮你跟上其他人。 |
| 代价/加成 | 代价：移动速度下降；开局赠送拐杖。 |
| 点评 | 单腿战士，拐杖流。（★★☆☆☆） |
| 源码路径 | `modular_nova/modules/limp_leg_quirk/code/limp_leg_quirk.dm` |

<a id="datum/quirk/nervous_aim"></a>
### 神经性手抖 · Nervous Tremble 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `-6` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者手部有随压力加重的神经性震颤记录。 |
| 效果 | 当你的精神状态不在最佳时，你的准头会习惯性变得灾难性。 |
| 代价/加成 | 代价：精神状态差时射击准度崩盘。 |
| 点评 | 压力越大枪越歪，心态流反向。（★★☆☆☆） |
| 源码路径 | `modular_nova/modules/quirk_nervous_tremble/nervous_tremble.dm` |

<a id="datum/quirk/system_shock"></a>
### 系统休克 · System Shock 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `-8` |
| quirk_flags | 仅人类 |
| 医疗记录 | 患者的处理器绝缘异常不佳。 |
| 效果 | 你和电的关系很不稳定，一点火花就能强制重启你的系统。注意：此特质仅对合成人有效。 |
| 代价/加成 | 代价：触电即强制重启（合成人）。 |
| 点评 | 合成人专属，电击=关机。（★☆☆☆☆） |
| 源码路径 | `modular_nova/modules/system_shock/code/system_shock_quirk.dm` |

<a id="datum/quirk/kleptomaniac"></a>
### 偷窃癖 · Kleptomaniac 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 受试者表现出偷窃癖。 |
| 效果 | 你有一股强烈的冲动去捡起周围任何东西，而且常常是无意识的。 |
| 代价/加成 | 代价：不由自主地拾取物品（可能暴露/惹祸）。 |
| 点评 | 全自动拾荒者，手不受控制。（★★☆☆☆） |
| 源码路径 | `modular_nova/modules/trauma_quirks/trauma_quirks.dm` |

<a id="datum/quirk/possessive"></a>
### 占有欲 · Possessive 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 受试者对物品表现出占有倾向。 |
| 效果 | 你对任何属于自己的物品都有强烈的依恋，常常觉得无法放手。 |
| 代价/加成 | 代价：难以丢弃/放下已持有的物品。 |
| 点评 | 仓鼠症患者，满身家当。（★★☆☆☆） |
| 源码路径 | `modular_nova/modules/trauma_quirks/trauma_quirks.dm` |

<a id="datum/quirk/equipping/entombed"></a>
### 活体棺葬 · Entombed 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `0` |
| quirk_flags | 仅人类、持续处理 |
| 医疗记录 | 患者生理上依赖 MOD 外骨骼维持生命，请勿尝试移除。 |
| 效果 | 你与一套 MOD 外骨骼永久融合（或依赖其生存），它永远无法被脱下。一旦电量耗尽或关机，你开始死亡！ |
| 代价/加成 | 代价：永久绑定 MOD 套装，断电即死。 |
| 点评 | 机甲共生体，断电倒计时。（★☆☆☆☆） |
| 源码路径 | `modular_nova/master_files/code/modules/entombed_quirk/code/entombed.dm` |

<a id="datum/quirk/item_quirk/narcolepsy"></a>
### 嗜睡症 · Narcolepsy

| 项目 | 内容 |
|---|---|
| 配点评级 | `-8` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者可能在正常活动中不自觉地睡着，随时感到困倦。 |
| 效果 | 你经常昏昏欲睡，随时可能睡着。保持咖啡因、走动，或用兴奋剂（处方与否皆可）压制症状，能帮你撑过当班。 |
| 代价/加成 | 代价：随机突然睡着（危险场景致命）。 |
| 点评 | 移动瞌睡虫，咖啡因续命。（★☆☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/narcolepsy.dm` |

<a id="datum/quirk/item_quirk/narcolepsy/synth"></a>
### 伪中断错误 · Spurious Interrupt Error 🔶NOVA

| 项目 | 内容 |
|---|---|
| 配点评级 | `-8 (继承)` |
| quirk_flags | 默认(仅人类) |
| 医疗记录 | 患者出现故障，可能在正常运行时意外重启。 |
| 效果 | （合成人版嗜睡症）你的系统出现伪中断错误，可能在正常运行中意外重启。 |
| 代价/加成 | 代价：随机强制重启（合成人）。 |
| 点评 | 合成人专属版嗜睡症。（★☆☆☆☆） |
| 源码路径 | `modular_nova/master_files/code/datums/quirks/negative_quirks/narcolepsy.dm` |

<a id="datum/quirk/item_quirk/nearsighted"></a>
### 近视 · Nearsighted

| 项目 | 内容 |
|---|---|
| 配点评级 | `-4` |
| quirk_flags | 仅人类、改变外观 |
| 医疗记录 | 患者需要配戴处方眼镜来矫正近视。 |
| 效果 | 没有处方眼镜你就看不清远处，不过你开局自带一副。 |
| 代价/加成 | 代价：裸眼远视模糊；开局配镜。 |
| 点评 | 眼镜娘/眼镜仔标配，眼镜可自定义。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/nearsighted.dm` |

<a id="datum/quirk/item_quirk/scarred_eye"></a>
### 伤疤眼 · Scarred Eye

| 项目 | 内容 |
|---|---|
| 配点评级 | `-2` |
| quirk_flags | 仅人类、改变外观 |
| 医疗记录 | 患者一只眼睛有严重疤痕，导致部分视力丧失。 |
| 效果 | 过去的一场意外让你失去了一只眼睛，但你得到了一只酷酷的眼罩。哟呵！ |
| 代价/加成 | 代价：单眼部分失明（视野受限）；开局赠送眼罩。 |
| 点评 | 海盗人设，独眼龙气质。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/scarred_eye.dm` |

<a id="datum/quirk/item_quirk/limper"></a>
### 跛行者 · Limper

| 项目 | 内容 |
|---|---|
| 配点评级 | `-6` |
| quirk_flags | 仅人类 |
| 医疗记录 | 患者腿部疑似无力。 |
| 效果 | 你走路有明显跛行，这会大大拖慢你的速度。还好你带了手杖。 |
| 代价/加成 | 代价：移动速度大幅下降；开局赠送手杖。 |
| 点评 | 慢生活代言人，手杖绅士。（★★☆☆☆） |
| 源码路径 | `code/datums/quirks/negative_quirks/limper.dm` |

---
*本文档由源码自动提取 + 人工编译生成。统计：正面 46 / 中性 50 / 负面 72 / 合计 168。*
*提取脚本：`extract_quirks.py`（扫描 `code/datums/quirks/**`、`modular_nova/master_files/code/datums/{quirks,traits}/**`、`modular_nova/modules/**` 中所有含 name 字段的 `/datum/quirk/*` 定义）。*
