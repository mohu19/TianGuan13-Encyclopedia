# TianGuan13 本局提示大全（Tip of the Round）

> 基于 TianGuan13 NovaSector 分支源码整理。每局开始前，服务器会随机挑选一条提示广播给全服玩家（即聊天里的“Tip of the round”）。
> **机制**：`code/controllers/subsystem/ticker.dm` L172-174——开局倒计时 ≤300 秒时调用 `send_tip_of_the_round(world, …)` 广播一次；`code/__HELPERS/game.dm` L330-340——**95% 概率**从 `strings/tips.txt` 随机一条，**5% 概率**从 `strings/sillytips.txt` 随机一条（彩蛋）。
> 文本经 NovaSector i18n 全量汉化系统收录（`strings/i18n/zh-Hans/strings.json`），以下为**官方中文翻译** + 英文原文对照。
> 注：以 `@` 开头的条目不做 HTML 编码（原文含标记标签）；“本局提示偶尔会骗你”是官方自嘲，部分条目并非真实机制。

---

## 一、常规提示（332 条）

1. **@你可以用 |、+、^ 或 _ 分别包裹消息的某些部分，使其<i>斜体</i>、<b>加粗</b>、<small>缩小</small>或<u>加下划线</u>。也可以在这些字符前加反斜杠来避免此效果（反斜杠不会显示在消息中）。**
   > EN: @You can <i>italicize</i>, <b>embolden</b>, <small>shrink</small>, or <u>underline</u> portions of your messages by enclosing them with |, +, ^, or _ respectively. You can also avoid this by adding backslashes (they won't show in the message) before these characters.
2. **♪ 嘿，你有没有试过在游戏内说话时，在消息前加上%符号？ ♫**
   > EN: ♪ Hey, have you ever tried appending the % character before your messages when speaking in-game? ♫
3. **科学家会为你的青蛙出高价！**
   > EN: A Scientist will pay top dollar for your frogs!
4. **扔出一杯水可以制造一块湿滑的地砖，让你能在紧要关头减速追兵。**
   > EN: A thrown glass of water can make a slippery tile, allowing you to slow down your pursuers in a pinch.
5. **任何能点烟的东西，都可以用来烧灼流血的伤口。严格来说，这也包括超物质。**
   > EN: Anything you can light a cigarette with, you can use to cauterize a bleeding wound. Technically, that includes the Supermatter.
6. **作为一名比特奔行者，升级你的量子服务器将增加奖励并减少停机时间。**
   > EN: As a Bitrunner, upgrading your quantum server will increase rewards and reduce downtime.
7. **作为比特奔行者，你的化身拥有领域信息能力，可以为你提供线索来帮助完成虚拟领域。**
   > EN: As a Bitrunner, your avatar has a domain info ability which will give you clues to help complete virtual domains.
8. **作为跳跳球仆从，你可以通过:b与主宰和其他跳跳球仆从交流。**
   > EN: As a Blobbernaut, you can communicate with overminds and other Blobbernauts via :b.
9. **作为软泥巨兵，你的HUD会显示你的生命值和创造你的主脑的核心生命值。**
   > EN: As a Blobbernaut, your HUD shows your health and the core health of the overmind that created you.
10. **作为植物学家，你可以像药剂师一样混合生产特性化学品。氢（草）+ 水（豌豆）+ 糖（葡萄）= 甘露醇！**
   > EN: As a Botanist, you can combine production trait chemicals just like a Chemist. Hydrogen(grass) + water(peas) + sugar(grapes) equal mannitol!
11. **作为植物学家，你可以像药剂师一样混合生产特性化学品。钾（香蕉）+ 水（西瓜）= ……好玩！**
   > EN: As a Botanist, you can combine production trait chemicals just like a Chemist. Potassium(bananas) + water(watermelons) equal... FUN!
12. **作为一名植物学家，你可以黑入巨型种子供应商以获得更多异域种子。这些种子也可以从货舱订购。**
   > EN: As a Botanist, you can hack the MegaSeed Vendor to get access to more exotic seeds. These seeds can alternatively be ordered from cargo.
13. **作为植物学家，您可以用 NutriMax 贩卖机中的 Left 4 Zed 来突变水培盘中生长的植物。一旦您在生物生成器中研磨了几株植物，它也能产生 Left 4 Zed。科学是不是很神奇？**
   > EN: As a Botanist, you can mutate the plants growing in your hydroponics trays with Left 4 Zed from your NutriMax vendor.  Once you've ground up a few plants in the biogenerator, it can also produce Left 4 Zed.  Isn't science amazing?
14. **作为植物学家，你应该想办法提高植物的效能。这能增加植株大小、化学物质含量、在生物生成器中研磨获得的点数，并让大家知道你是一位出色的植物学家。**
   > EN: As a Botanist, you should look into increasing the potency of your plants. This increases the size, amount of chemicals, points gained from grinding them in the biogenerator, and lets people know you are a proficient botanist.
15. **作为货运技师，你可以通过从维修通道运回板条箱、液体容器、等离子板、水培的稀有种子等物品来赚取更多货运点数！**
   > EN: As a Cargo Technician, you can earn more cargo points by shipping back crates from maintenance, liquid containers, plasma sheets, rare seeds from hydroponics, and more!
16. **作为货运技师，你可以黑入MULE机器人，让它们更快、碾过挡路的人，甚至让你骑上它们！**
   > EN: As a Cargo Technician, you can hack MULEbots to make them faster, run over people in their way, and even let you ride them!
17. **作为货运技师，你可以通过拆卸补给穿梭机控制台，对电路板使用多用工具，然后重新组装它，来从其上订购违禁品。**
   > EN: As a Cargo Technician, you can order contraband items from the supply shuttle console by de-constructing it and using a multitool on the circuit board, the re-assembling it.
18. **作为变形怪，重新利用的腺体可以挣脱流星索，解除眩晕，并以牺牲双手使用为代价大幅提升速度——这包括失去开启受限气闸的能力。**
   > EN: As a Changeling, Repurposed Glands will break bolas, disable stuns, and give you a hefty speed boost at the cost of the use of your arms - which includes the ability to open restricted airlocks.
19. **作为变形怪，变成他人的外貌也会让你获得他们的所有疤痕。你可以使用血肉愈合来消除所有疤痕。**
   > EN: As a Changeling, taking on someone else's appearance will also give you all of their scars. You can use Fleshmend to get rid of all scars.
20. **作为一名变形怪，提取DNA刺击会计入你的基因组吸收目标，但不会让你重新分配能力。**
   > EN: As a Changeling, the Extract DNA sting counts for your genome absorb objective, but does not let you respec your powers.
21. **作为一名变形怪，你可以通过掐死某人并使用吸收动词来吸收他们；这将使你能够重新选择你的能力，获得被吸收者的DNA、记忆，以及被吸收者说过的一些话的样本。**
   > EN: As a Changeling, you can absorb someone by strangling them and using the Absorb verb; this gives you the ability to rechoose your powers, the DNA of whoever you absorbed, the memory of the absorbed, and some samples of things the absorbed said.
22. **作为一名药剂师，圣水和钾混合在一起会产生神圣爆炸，威力随用量而增加。如果两者都使用了至少75单位，爆炸将揭示、点燃并麻痹视野内的所有邪教徒、异端和亡魂。干吧！**
   > EN: As a Chemist, Holy Water and Potassium mixed together will create a HOLY explosion, with increased power scaling by amount used. If at least 75 units of both are used, the explosion will reveal, ignite, and paralyze any cultists, heretics, and revenants in sight. Do it!
23. **作为药剂师，有些化学品只能通过化学加热器或手动使用打火机等工具加热内容物来合成。**
   > EN: As a Chemist, some chemicals can only be synthesized by heating up the contents with a chemical heater or manually with lighters and similar tools.
24. **作为一名药剂师，有几十种化学物质可以治愈，甚至更多可以造成伤害。实验吧！**
   > EN: As a Chemist, there are dozens of chemicals that can heal, and even more that can cause harm. Experiment!
25. **作为药剂师，水和钾混合在一起会引发爆炸，威力随用量增加。别这么干。**
   > EN: As a Chemist, Water and Potassium mixed together will create an explosion, with power scaling by amount used. Don't do it.
26. **作为一名药剂师，你可以直接从塑料板快速制作100u塑料瓶，同时能在车床上制作120u塑料烧杯。药大师也能生产无限30u玻璃瓶，如果研究完成，医疗技术制造机可以制作容量从120u到300u的烧杯。**
   > EN: As a Chemist, you can quickly make 100u plastic bottles directly from plastic sheets, alongside being able to make 120u plastic beakers in the lathe. The ChemMaster can produce infinite 30u glass bottles as well and if researched the medical techfab can make beakers with capacity ranging from 120u to 300u.
27. **作为药剂师，你可以用感应器给化学分配器充电，或更换其电池。**
   > EN: As a Chemist, you can recharge your chemical dispenser with an inducer or by replacing its cell.
28. **作为一名药剂师，你将被要求向船员提供某些化学物质。例如，用于低温管的冷冻昔酮和甘露醇，用于植物学的二乙胺和硝石，以及用于前台的药丸和贴片。**
   > EN: As a Chemist, you will be expected to supply crew with certain chemicals. For example, Cryoxadone and Mannitol for the cryo tubes, Diethylamine and Saltpetre for botany, as well as healing pills and patches for the front desk.
29. **作为一名厨师，你做的任何食物都比售货机里的垃圾食品健康得多。让船员定期吃你做的食物会提供小幅增益。**
   > EN: As a Cook, any food you make will be much healthier than the junk food found in vendors. Having the crew routinely eating from you will provide minor buffs.
30. **作为厨师，待在厨房会让你想起近身烹饪术的要领。它对于将助手赶出你的工作场所非常有效。**
   > EN: As a Cook, being in the kitchen will make you remember the basics of Close Quarters Cooking. It is highly effective at removing Assistants from your workplace.
31. **作为厨师，大多数非定制食物会带有次要效果，从治疗你到让你以光速移动。多试验！**
   > EN: As a Cook, most non-custom foods will have a secondary effect, ranging from healing you to making you move at lightspeed. Experiment!
32. **作为一名厨师，你可以通过制作菜单创造种类繁多的食物。你可以在战斗模式指示器附近找到锤子图标。**
   > EN: As a Cook, you can create a very wide variety of food with the crafting menu. You can find it by looking for the hammer icon near your combat mode indicator.
33. **作为厨师，你可以把你的食物装入智能冰箱和定制自动售货机。**
   > EN: As a Cook, you can load your food into smartfridges and custom vending machines.
34. **作为厨师，你可以用笔给自制的食物重命名。**
   > EN: As a Cook, you can rename your custom made food with a pen.
35. **作为一名邪教徒，查看屏幕右上角的警报，了解关于你邪教当前状态和目标的全部细节。**
   > EN: As a Cultist, check the alert in the upper-right of your screen for all the details about your cult's current status and objective.
36. **作为邪教徒，在目标完成前不要引发太多混乱。如果穿梭机呼叫得太早，你可能没有足够的时间获胜。**
   > EN: As a Cultist, do not cause too much chaos before your objective is completed. If the shuttle gets called too soon, you may not have enough time to win.
37. **作为邪教徒，如果你成功献祭了一个异端，你将解锁三种强大独特物品之一，可在你教派的任意熔炉、档案馆或祭坛中制造。**
   > EN: As a Cultist, if you manage to sacrifice a Heretic, you will unlock one of three powerful and unique items to be created in every one of your cult's forges, archives, or altars.
38. **作为一名邪教徒，沸血符文会对非邪教徒造成大量蛮力伤害，并对附近的纳尔茜邪教徒造成一些伤害，但使用时符文所在位置会产生火焰。**
   > EN: As a Cultist, the Blood Boil rune will deal massive amounts of brute damage to non-cultists, and some damage to fellow cultists of Nar'Sie nearby, but will create a fire where the rune stands on use.
39. **作为邪教徒，当你献祭异端时，他们会被束缚在一把强大的附魔长剑中。之后任何人都可以解开剑的束缚，释放其力量和能力，但也会让剑能够独立行动。**
   > EN: As a Cultist, when you sacrifice a Heretic, they will be bound inside a powerful haunted longsword. Anyone can then unbind the blade, unlocking its powers and abilities, but also allowing the blade to act of its own free will.
40. **作为邪教徒，你的队伍一开始非常弱小，但必要时可以迅速将一切转化为原始力量。确保有足够的人数和装备来支撑你大张旗鼓，否则邪教将一败涂地。**
   > EN: As a Cultist, your team starts off very weak, but if necessary can quickly convert everything they have into raw power. Make sure you have the numbers and equipment to support going loud, or the cult will fall flat on its face.
41. **作为一名策展人，你可以从所有绘画赞助费用中获得22%的分成（由策展人数量均分）。将他人的耐心和艺术技巧变成你自己的收入！**
   > EN: As a Curator, you earn a 22% cut (divided by number of curators) of all credits spent on painting patronages. Turn others' patience and artistry skills into your own income!
42. **作为一名赛博格，谨慎选择你的型号，因为只有剪断并修复你的重置线才能让你重新选择。如果可能，在需要特定型号的情况出现之前，不要急于选择。**
   > EN: As a Cyborg, choose your model carefully, as only cutting and mending your reset wire will let you re-pick it. If possible, refrain from choosing a model until a situation that requires one occurs.
43. **作为赛博格，你对EMP极为脆弱，因为EMP既能击晕你也能伤害你。军械库里的离子步枪或叛徒的EMP套件可以在几秒内杀死你。**
   > EN: As a Cyborg, you are extremely vulnerable to EMPs as EMPs both stun you and damage you. The ion rifle in the armory or a traitor with an EMP kit can kill you in seconds.
44. **作为赛博格，你对大多数形式的眩晕免疫，并且在几乎所有方面都远胜于人类。然而，闪光灯可以轻易地将你眩晕锁定，而且由于你没有手，无法进行任何精细操作。**
   > EN: As a Cyborg, you are immune to most forms of stunning, and excel at almost everything far better than humans. However, flashes can easily stunlock you and you cannot do any precision work as you lack hands.
45. **作为一名赛博格，你对火焰和高温免疫。如果你失控了，你可以到处释放等离子火焰，并在其中漫步而毫不在意！**
   > EN: As a Cyborg, you are impervious to fires and heat. If you are rogue, you can release plasma fires everywhere and walk through them without a care in the world!
46. **作为 无人机，你可以呼叫其他无人机来提醒它们空间站中需要维修的区域。**
   > EN: As a Drone, you can ping other drones to alert them of areas in the station in need of repair.
47. **作为一名遗传学家，变成绿巨人能使你造成高额近战伤害，击晕锁定他人，并击穿墙壁。但是，你不能开枪，如果陷入垂死状态会失去绿巨人状态，并且在你作为绿巨人时，AI不会将你视为人类。**
   > EN: As a Geneticist, becoming a hulk makes you capable of dealing high melee damage, stun-locking people, and punching through walls. However, you can't fire guns, will lose your hulk status if you go into critical condition, and are not considered a human by the AI while you are a hulk.
48. **作为遗传学家，T对应A，G对应C。**
   > EN: As a Geneticist, T goes to A, and G goes to C.
49. **作为遗传学家，你通常应该保存你解锁的所有突变。负面突变会增加你的基因稳定性，让你能保留更多正面突变。**
   > EN: As a Geneticist, you should usually save all mutations you unlock. Negative mutations will increase your genetic stability, allowing you to keep more positive mutations.
50. **作为一名幽灵，你可以通过小游戏菜单开始和加入夺旗游戏，或者点击团队生成器之一，这些生成器可以在轨道菜单的'杂项'部分找到。**
   > EN: As a Ghost, you can both start and join capture the flag games through the minigames menu, or by clicking on one of the team spawners, which can be found under the "Misc" section of the orbit menu.
51. **作为一名幽灵，你可以双击几乎任何东西来跟随它。或者只是到处瞬移！**
   > EN: As a Ghost, you can double click on just about anything to follow it. Or just warp around!
52. **作为一名幽灵，你可以通过点击地上的容器来查看其内部。**
   > EN: As a Ghost, you can see the inside of a container on the ground by clicking on it.
53. **作为异端，灰烬之道注重潜行和迷惑，但随着你的进步，会抛弃这种玩法，转而选择更具攻击性的、炽热的终局。**
   > EN: As a Heretic, the Path of Ash focuses on stealth and disorientation, but as you progress, sheds this playstyle in favor of a more aggressive, fiery finale.
54. **作为异端，刃之路会奖励你的战斗能力，让你在战斗中变得越来越强。通过升华，你可以成为终极的决斗主宰。**
   > EN: As a Heretic, the Path of Blade rewards your ability to fight, by making you better and better at it. Though ascension, you can become an ultimate dueling juggernaut.
55. **作为异端，宇宙之路让你能正当地拥有船员们所踏足的空间。如果他们不尊重你的地位，从彼岸召来一位朋友就能让他们明白。**
   > EN: As a Heretic, the Path of Cosmos allows you to take rightful ownership of the very space the crew treads on. And if they don't respect your status, calling in a friend from beyond should show them.
56. **作为一名异端，血肉之道允许你通过召唤来自幕后的食尸鬼和怪物来组建一支军队。通过飞升，你自己就成了一支一人军队。**
   > EN: As a Heretic, the Path of Flesh allows you to raise an army by summoning ghouls and monsters from beyond the veil. Through ascension, you become a one-man army yourself.
57. **作为一名异端，锁之道是助理最好的朋友，可以打通许多途径。包括通往幕后的……**
   > EN: As a Heretic, the Path of Lock is an Assistant's best friend, and can open many pathways. Including ones beyond the veil...
58. **作为异端，月之路会真正地让你周围的人发疯——也许疯到足以在你成功时成为你的盟友。**
   > EN: As a Heretic, the Path of Moon will literally drive people around you crazy - perhaps crazy enough to become your allies should you succeed.
59. **作为异端，锈蚀之道相当显眼，但能让你承受大量伤害，因为你周围的一切都会慢慢腐朽，化为乌有。**
   > EN: As a Heretic, the Path of Rust is quite overt, but allows you to shrug of a lot of damage as everything around you slowly decays into nothing but rot and rust.
60. **作为一名异端，虚空之道让人们希望他们能在浩瀚的太空虚空中尖叫，或有机会从你身边逃脱。最终，风暴吞噬一切。**
   > EN: As a Heretic, the Path of Void makes people wish they could scream in the vast emptiness of space or have a chance at escaping from you. In the end, the storm takes all.
61. **作为异端，你也可以献祭邪教徒，这将奖励你一个知识点数和三种独特强大奖励中的一种。**
   > EN: As a Heretic, you can also sacrifice cultists, rewarding you with a knowledge point and one of three unique, powerful rewards.
62. **作为异端，你可以在睡觉时通过梦见其周围的随机物品来定位一个影响点。**
   > EN: As a Heretic, you can locate an influence by dreaming about random objects around it when you sleep.
63. **作为清洁工赛博格，你是所有屠宰恶魔甚至泡泡糖本人的克星。清理血渍会严重削弱它们。**
   > EN: As a Janitor Cyborg, you are the bane of all slaughter demons and even Bubblegum himself. Cleaning up blood stains will severely gimp them.
64. **作为清洁工，如果有人偷了你的清洁车，你可以改用太空清洁喷雾、手雷、喷水器，实施血腥报复，或者从货舱再订一辆。**
   > EN: As a Janitor, if someone steals your janicart, you can instead use your space cleaner spray, grenades, water sprayer, exact bloody revenge or order another from Cargo.
65. **作为清洁工，捕鼠夹可以用来制作炸弹或陷阱容器。**
   > EN: As a Janitor, mousetraps can be used to create bombs or booby-trap containers.
66. **作为清洁工，你可以通过指向一块地砖并说“拖/清洁”来指挥清洁机器人。**
   > EN: As a Janitor, you can command cleanbots by pointing at a tile and saying mop/clean.
67. **作为律师，如果判罚对罪行来说太高，试着与典狱长谈判。**
   > EN: As a Lawyer, try to negotiate with the Warden if sentences seem too high for the crime.
68. **作为一名律师，你可以尝试说服舰长和安保主管在法庭上为囚犯举行审判。**
   > EN: As a Lawyer, you can try to convince the Captain and Head of Security to hold trials for prisoners in the courtroom.
69. **作为一名医疗赛博格，你可以完全执行手术，甚至增强人员。**
   > EN: As a Medical Cyborg, you can fully perform surgery and even augment people.
70. **作为一名医生，盐水葡萄糖不仅能暂时提高患者的血液水平，还能加速血液再生！非常适合失血患者！**
   > EN: As a Medical Doctor, Saline-Glucose not only acts as a temporary boost to a patient's blood level, it also speeds blood regeneration! Perfect for drained patients!
71. **作为医生，淋浴可以用来帮助从睡眠、昏迷、困惑、嗜睡、紧张、头晕和醉酒中恢复。**
   > EN: As a Medical Doctor, a shower can be used to help recover from sleep, unconsciousness, confusion, drowsiness, jitters, dizziness, and drunkness.
72. **作为医生，几乎所有类型的伤口至少都能用纱布暂时处理。拿不准的时候，就包扎起来！**
   > EN: As a Medical Doctor, almost every type of wound can be treated at least temporarily with gauze. When in doubt, wrap it up!
73. **作为一名医生，更好的环境能加快手术速度。在手术电脑上执行手术，并对患者使用无菌净可以加快过程。**
   > EN: As a Medical Doctor, better environments make for faster surgeries. Perform surgery on an operating computer and using Sterilizine on the patient will speed up the process.
74. **作为医生，将尸体放入冰柜或停尸间托盘会使其器官被冻结，从而防止腐烂。如果你没有时间复活多具尸体，请暂时将它们转移到停尸间！**
   > EN: As a Medical Doctor, corpses placed inside a freezer or morgue tray will have their organs frozen preventing decay. If you don't have time to revive multiple dead bodies, transfer them to the morgue temporarily!
75. **作为医生，描述中写有“……并且灵魂已离去”的尸体不再有鬼魂附着——你可以复活他们，但不会有玩家控制，这基本毫无意义。**
   > EN: As a Medical Doctor, corpses with the "...and their soul has departed" description no longer have a ghost attached to them - you can revive them, but no player will take control, making it largely pointless.
76. **作为医生，严重的割伤是最危险的情况之一。尽快使用纱布、肾上腺素注射器、缝合线、烧灼器，有什么用什么！**
   > EN: As a Medical Doctor, critical slash wounds are one of the most dangerous conditions someone can have. Apply gauze, epipens, sutures, cauteries, whatever you can, as soon as possible!
77. **作为一名医生，请注意有些物种——例如等离子人或胶状人——缺乏血肉或骨骼。这将改变你对他们进行手术的方式，允许你跳过诸如切开切口或锯骨等步骤。**
   > EN: As a Medical Doctor, note that some species - such as Plasmamen or Jellypeople - lack flesh or bones. This will change how you perform surgery on them, allowing you to skip steps such as making incisions or sawing bones.
78. **作为医生，手术电脑上包含了你能想象到的所有手术的步骤说明。如果你感到困惑，用它来指导你完成整个流程。**
   > EN: As a Medical Doctor, operating computers contain instructions on how to complete every surgery you could dream of. If you are ever confused, use one to guide you through the process.
79. **作为医生，治疗等离子人并非不可能！静滞机可以在你进行必要手术时防止他们燃烧和窒息。如果没有这种条件，你可以使用沙丁胺醇防止窒息，并让他们待在淋浴下以防着火。**
   > EN: As a Medical Doctor, treating Plasmamen is not impossible! Stasis machines will keep them from burning and suffocating while you perform necessary surgeries. If you don't have the luxury of one, you can use Salbutamol to prevent suffocation and keep them under a running shower to prevent fire.
80. **作为医生，有时候可以试着摆弄一下病毒学实验室！病毒的作用范围很广，从强大到能让你从垂死状态中恢复的治疗能力，到危险到能通过空气传播的自燃杀死全体船员的疾病。多实验！**
   > EN: As a Medical Doctor, try messing with the Virology lab sometime! Viruses can range from healing powers so great that you can heal out of critical status, or diseases so dangerous they can kill the entire crew with airborne spontaneous combustion. Experiment!
81. **作为医生，除非你的病人处于静滞状态，否则你给他们动手术时他们会失血——尤其是钳住血管之前。密切关注他们的血量，动作麻利，最重要的是别忘了手术结束后烧灼或缝合！**
   > EN: As a Medical Doctor, unless your patient is in stasis, they will lose blood as you perform surgery on them - especially before you clamp bleeders. Keep an eye on their blood levels, work quickly, and most importantly don't forget to cauterize or suture them after you're finished!
82. **作为医生，在处理病毒时，记住机器器官可以赋予对疾病效果和传播性的免疫力。利用无机生物学症状来绕过这种保护。**
   > EN: As a Medical Doctor, when messing with viruses, remember that robotic organs can give immunity to disease effects and transmissibility. Make use of the inorganic biology symptom to bypass the protection.
83. **作为一名医生，在大流行病期间，你只需要少量疫苗就能治愈一名患病患者。与药剂师合作，更有效地分发你的疗法。**
   > EN: As a Medical Doctor, while there's an pandemic, you only require small amounts of vaccine to heal a sick patient. Work with the Chemist to distribute your cures more efficiently.
84. **作为医生，你可以尝试用注射器从干尸身上抽血来确定死因。如果能抽出血液，就是高温或激光所致；如果抽不到血，则确认了变形怪的存在。**
   > EN: As a Medical Doctor, you can attempt to drain blood from a husk with a syringe to determine the cause. If you can extract blood, it was caused by extreme temperatures or lasers, if there is no blood to extract, you have confirmed the presence of changelings.
85. **作为医生，你可以通过用除颤器反复电击患者来治愈心脏病！**
   > EN: As a Medical Doctor, you can cure a heart attack by repeatedly shocking the victim with a defibrillator!
86. **作为医生，你可以将有大量伤口的病人放入低温舱来处理。这会同时缓慢地治疗他们的所有伤口，但比直接治疗要慢得多。**
   > EN: As a Medical Doctor, you can deal with patients who have absurd amounts of wounds by putting them in cryo. This will slowly treat all of their wounds simultaneously, but is much slower than direct treatment.
87. **作为医生，你可以在执行取出步骤时，用副手拿着一个空的植入物盒来取出植入物。**
   > EN: As a Medical Doctor, you can extract implants by holding an empty implant case in your offhand while performing the extraction step.
88. **作为一名医生，你可以用笔灯指向人们来创建医疗全息图。这让他们知道你要来治疗他们。**
   > EN: As a Medical Doctor, you can point your penlight at people to create a medical hologram. This lets them know that you're coming to treat them.
89. **作为医生，你可以通过手术将东西植入或取出人们的胸腔。这可以是放入炸弹，也可以是取出异形幼虫。**
   > EN: As a Medical Doctor, you can surgically implant or extract things from people's chests. This can range from putting in a bomb to pulling out an alien larva.
90. **作为一名医生，你只在现场手术时需要手术洞巾。患者固定在静滞床或手术台上时不需要洞巾就能进行手术。**
   > EN: As a Medical Doctor, you only need surgical drapes when doing field surgery. Patients buckled to stasis beds or operating tables do not require drapes to be operated on.
91. **作为医生，在尝试对某人进行手术时，你必须瞄准正确的肢体，并且不要处于战斗模式。右键点击你的病人将故意导致手术步骤失败。**
   > EN: As a Medical Doctor, you must target the correct limb and not be in combat mode when trying to perform surgery on someone. Right clicking your patient will intentionally fail the surgery step.
92. **作为猴子，你可以通过Alt+左键点击通风口或洗涤器管道来爬行。不过，你必须扔掉身上穿戴和持有的所有物品才能这样做。**
   > EN: As a Monkey, you can crawl through air or scrubber vents by alt+left clicking them. You must drop everything you are wearing and holding to do this, however.
93. **作为猴子，你仍然可以穿戴一些人类物品，例如背包、防毒面具和帽子，并且仍然有两只空闲的手。**
   > EN: As a Monkey, you can still wear a few human items, such as backpacks, gas masks and hats, and still have two free hands.
94. **作为核特工，沟通是关键！使用 ; 与你的特工同伴交谈，协调攻击计划。**
   > EN: As a Nuclear Operative, communication is key! Use ; to speak to your fellow operatives and coordinate an attack plan.
95. **作为核特工，要团结一致！虽然你的装备很硬核，但你的同伴在救你命方面更胜一筹：他们能在你被击晕时把你拖离险境，还能提供火力掩护。**
   > EN: As a Nuclear Operative, stick together! While your equipment is robust, your fellow operatives are much better at saving your life: they can drag you away from danger while stunned and provide cover fire.
96. **作为核特工，你可能会遇到AI把你锁在房间里的情况。口袋里备些C4能救你一命。**
   > EN: As a Nuclear Operative, you might end up in a situation where the AI has bolted you into a room. Having some spare C4 in your pocket can save your life.
97. **作为一名核特工，你应该考虑购买一个辛迪加赛博格，因为它们能提供强大的火力支援，拥有完整权限，对常规眩晕免疫，并且能轻松干掉AI。**
   > EN: As a Nuclear Operative, you should look into purchasing a syndicate cyborg, as they can provide heavy fire support, full access, are immune to conventional stuns, and can easily take down the AI.
98. **作为一名护理员，患者体内的嵌入物可以通过瞄准受影响的肢体并对患者使用止血钳来安全取出。**
   > EN: As a Paramedic, embedded objects in a patient can be safely extracted by targeting the affected limb and using a hemostat on the patient.
99. **作为一名护理员，必须尽可能将新鲜尸体放在静滞床上。你的首席医疗官会感谢你的。**
   > EN: As a Paramedic, it is imperative that whenever possible you put fresh corpses on the stasis beds. Your CMO will thank you.
100. **作为护理员，你的紫外笔灯可以用来抑制烧伤伤口感染！只需瞄准受感染的肢体并对患者使用即可。**
   > EN: As a Paramedic, your UV penlight can be used to stem infections in burn wounds! Just target the infected limb and use it on the patient.
101. **作为亡魂，牧师是你最大的敌人，因为他们能使用否决之杖对你造成巨大伤害，并用圣水让空间站的大片区域无法通行。**
   > EN: As a Revenant, the Chaplain is your worst enemy, as they can damage you massively with the null rod and make large swaths of the station impassable with holy water.
102. **作为亡魂，枯萎术对人类造成的疾病可以通过躺下或使用圣水轻松治愈，因此最好用于没时间躺下的目标，比如战斗中的人类。**
   > EN: As a Revenant, the illness inflicted on humans by Blight can be easily cured by lying down or with holy water, making it best used on targets that have no time to lie down, such as humans in combat.
103. **作为亡魂，你的亵渎能力可以移除小范围内地格中的圣水以及盐，从而如果牧师用圣水覆盖了空间站，你可以从他们手中夺回。它还可以用来打开停尸间的托盘！**
   > EN: As a Revenant, your Defile ability removes holy water from tiles in a small radius, along with salt, allowing you to reclaim the station from the chaplain if they've been covering the station in holy water. It can also be used to open morgue trays!
104. **作为亡魂，你的精华就是你的生命值，所以如果你已经从守卫不严的尸体中储备了精华，那么在人类面前现身以收割活物的精华就安全多了。**
   > EN: As a Revenant, your essence is also your health, so revealing yourself in front of humans to harvest the essence of the living is much safer if you've already stocked up on essences from poorly guarded corpses.
105. **作为亡魂，你的故障能力通常会损坏机器和机械物体，甚至可能电磁脉冲破坏某些物体。多实验！**
   > EN: As a Revenant, your Malfunction ability in general damages machinery and mechanical objects, possibly even emagging some objects. Experiment!
106. **作为一个亡魂，你的过载灯光能力只有在短暂延迟后灯光仍然亮着时才会电击人类。**
   > EN: As a Revenant, your Overload Lights ability will only shock humans with lights if the lights are still on after a brief delay.
107. **作为革命者，货运部门可能是你最好的朋友，也可能是你最可怕的噩梦。在最好的情况下，你将能够订购无限量的枪支和盔甲；在最坏的情况下，安保部门将控制货运，订购无限数量的心防植入物来让你的革命伙伴们背叛你。**
   > EN: As a Revolutionary, cargo can be your best friend or your worst nightmare. In the best case scenario you will be able to order a limitless amount of guns and armor, in the worst case scenario security will take control and order a limitless number of mindshield implants to turn your fellow revolutionaries against you.
108. **作为革命者，你无法转化部门主管或植入了心灵护盾的人，例如安保官或他们植入的人。心灵护盾通常由军需官购买，但可以通过手术移除。控制货舱以掌控转化！**
   > EN: As a Revolutionary, you cannot convert a head of staff or someone who has a mindshield implant, such as a security officer or those they implant. Implants are often purchased by the Quartermaster, but the implants can be surgically removed. Take control of cargo to keep control of conversions!
109. **作为革命者，你的主要力量来自你转化的人，明智选择转化对象以确保赢得这一局。**
   > EN: As a Revolutionary, your main power comes from who you convert, choose your converts wisely to ensure you win the round.
110. **作为机器人专家，留意异常公告。如果你搞到蓝空间异常核心，你就能建造相位子机甲！**
   > EN: As a Roboticist, keep an ear out for anomaly announcements. If you get your hands on a bluespace anomaly core, you can build a Phazon mech!
111. **作为一名机器人专家，你可以用赛博格肢体增强人员。增强的肢体可以用电缆和焊机轻松修复。**
   > EN: As a Roboticist, you can augment people with cyborg limbs. Augmented limbs can easily be repaired with cables and welders.
112. **作为机器人专家，你可以通过建造一台配备液压钳和等离子切割机的克拉克来极大地帮助矿工。这台机甲能抵御灰暴，甚至能走过熔岩！**
   > EN: As a Roboticist, you can greatly help out Shaft Miners by building a Clarke equipped with a hydraulic clamp and plasma cutter. The mech is ash storm proof and can even walk across lava!
113. **作为机器人专家，你可以用焊接工具修理你的赛博格。如果它们受到了烧伤损伤，你可以取出它们的电池，用螺丝刀暴露线路，然后用电缆线圈更换电线。**
   > EN: As a Roboticist, you can repair your cyborgs with a welding tool. If they have taken burn damage, you can remove their battery, expose the wiring with a screwdriver and replace their wires with a cable coil.
114. **作为机器人专家，你可以用剪线钳剪断并修复重置线来重置赛博格的型号。**
   > EN: As a Roboticist, you can reset a cyborg's model by cutting and mending the reset wire with a wire cutter.
115. **作为一名科学家，可研究的库存零件可以极大地提高空间站各处机器的效率和速度。在某些情况下，甚至能解锁新功能。**
   > EN: As a Scientist, researchable stock parts can seriously improve the efficiency and speed of machines around the station. In some cases, it can even unlock new functions.
116. **作为科学家，你可以通过分析仪扫描异常，然后用远程信号装置发送它所给的频率信号，或者如果已研究，用异常分析仪打击异常来使其失效。这会留下一个异常核心，可用于建造相位子机甲、反应装甲或各种独特而强大的武器！**
   > EN: As a Scientist, you can disable anomalies by scanning them with an analyzer, then send a signal on the frequency it gives you with a remote signaling device, or if researched, hit the anomaly an anomaly analyzer. This will leave behind an anomaly core, which can be used to construct a Phazon mech, reactive armors, or various unique and powerful weapons!
117. **作为科学家，你可以为科学部门赚钱，并通过让超光速粒子多普勒阵列记录越来越大的爆炸来完成实验。**
   > EN: As a Scientist, you can generate money for Science, and complete experiments by letting the tachyon-doppler array record increasingly large explosions.
118. **作为一名科学家，你可以通过在活着时喂食由紫色史莱姆制成的史莱姆类固醇来最大化史莱姆的使用次数。然后，你可以对每个提取物使用由蔚蓝史莱姆制成的提取物增强剂。**
   > EN: As a Scientist, you can maximize the number of uses you get out of a slime by feeding it slime steroid, created from purple slimes, while alive. You can then apply extract enhancer, created from cerulean slimes, on each extract.
119. **作为科学家，你可以使用从绿色史莱姆中获取的突变毒素将自己变成凝胶变种人。每个亚种都有独特的特性——比如心灵感应、复制身体或融合史莱姆提取物！**
   > EN: As a Scientist, you can use the mutation toxin obtained from green slimes to turn yourself into a jelly mutant. Each subspecies has unique features - for example telepathic powers, duplicating bodies or integrating slime extracts!
120. **作为一名安保官，使用安保频道 (:s) 与警官同事沟通协调，以避免混乱。**
   > EN: As a Security Officer, communicate and coordinate with your fellow officers using the security channel (:s) to avoid confusion.
121. **作为安保官，佩戴安保HUD或HUD太阳镜时查看某人可设定其逮捕等级，使Beepsky及其他安保机器人进行追捕。**
   > EN: As a Security Officer, examining someone while wearing sechuds or HUDsunglasses will let you set their arrest level, which will cause Beepsky and other security bots to chase after them.
122. **作为一名安保官，心灵护盾植入物只能防止某人被转变为邪教徒：与革命者不同，如果他们已经皈依，它不会让他们脱离邪教。**
   > EN: As a Security Officer, mindshield implants can only prevent someone from being turned into a cultist: unlike revolutionaries, it will not de-cult them if they have already been converted.
123. **作为一名安保官，记住相关性不等于因果性。某人可能只是在错误的时间出现在错误的地点！**
   > EN: As a Security Officer, remember that correlation does not equal causation. Someone may have just been at the wrong place at the wrong time!
124. **作为一名安保官，记住你可以将安保手电装在你的失能枪或头盔上！**
   > EN: As a Security Officer, remember that you can attach a sec-lite to your disabler or your helmet!
125. **作为安保官，你可以用电击棒反复击打来治疗甚至治愈心脏病。重点是*反复*。**
   > EN: As a Security Officer, you can treat or even cure a heart attack with repeated blows from a stunbaton. Emphasis on *repeated*.
126. **作为安保官，你的PDA里有一支特殊的笔，可以用来促使人们投降。这会击晕他们，无需使用警棍！**
   > EN: As a Security Officer, you have a special pen in your PDA that you can use to prompt people to surrender. This will stun them without the need to use a baton!
127. **作为安保官，你的安保HUD或HUD太阳镜不仅能查看船员的职业分配和犯罪状态，还能看到他们是否植入了心灵护盾。通过职业图标周围闪烁的蓝色轮廓就能辨别。在革命中利用这一点，确定谁是你这边的人！**
   > EN: As a Security Officer, your sechuds or HUDsunglasses can not only see crewmates' job assignments and criminal status, but also if they are mindshield implanted. You can tell by the flashing blue outline around their job icon. Use this to your advantage in a revolution to definitively tell who is on your side!
128. **作为一名服务赛博格，你的喷漆罐能击倒人。但是，它会被口罩和眼镜阻挡。**
   > EN: As a Service Cyborg, your spray can knocks people down. However, it is blocked by masks and glasses.
129. **作为一名矿工，始终随身携带GPS，这样如果你死了，其他矿工或赛博格能来救你。**
   > EN: As a Shaft Miner, always have a GPS on you, so a fellow miner or cyborg can come to save you if you die.
130. **作为矿工，熔岩地上的每种怪物都有你可以利用的模式，以最大程度地减少遭遇战中的伤害。**
   > EN: As a Shaft Miner, every monster on Lavaland has a pattern you can exploit to minimize damage from the encounters.
131. **作为矿工，熔岩地的北侧比南侧有更多稀有矿物。**
   > EN: As a Shaft Miner, the northern side of Lavaland has a lot more rare minerals than on the south.
132. **作为一名矿工，你可以用当地动物群制作各种装备。骨斧、岩浆船和灰烬龙兽装甲只是其中的一部分！**
   > EN: As a Shaft Miner, you can craft a variety of equipment from the local fauna. Bone axes, lava boats and ash drake armour are just a few of them!
133. **作为矿工，你可以从巨人身上收获巨蜥甲板，并用其升级你的探险者套装，大幅减少受到的近战伤害。**
   > EN: As a Shaft Miner, you can harvest goliath plates from goliaths and upgrade your explorer's suit, greatly reducing incoming melee damage.
134. **作为一名辛迪加猴子，你对物种转换有爆炸性过敏反应，可能应该避免它们。**
   > EN: As a Syndicate Monkey, you're explosively allergic to species transformations and should probably avoid them.
135. **作为叛徒，等离子可以注入许多物品进行破坏。电力电池、灯泡、焊枪、雪茄和电子烟使用时都会爆炸。**
   > EN: As a Traitor, plasma can be injected into many things to sabotage them. Power cells, light bulbs, welding tools, cigars and e-cigs will all explode when used.
136. **作为叛徒，策反AI为你服务可以使其成为极其强大的盟友。不过，要小心你赋予它的法则的措辞，因为它可能会利用你写得不严谨的法则来对付你！**
   > EN: As a Traitor, subverting the AI to serve you can make it an extremely powerful ally. However, be careful of the wording in the laws you give it, as it may use your poorly written laws against you!
137. **作为叛徒，舰长和安保主管是空间站上最难杀的两个目标。如果其中任何一个是你的目标，请周密计划。**
   > EN: As a Traitor, the Captain and the Head of Security are two of the most difficult to kill targets on the station. If either one is your target, plan carefully.
138. **作为叛徒，密码序列器（emag）不仅可以打开储物柜、箱子、APC 等。它还可以用来入侵半机械人，甚至让机器人发狂。在正确的机器上使用它，你甚至可以联系辛迪加。去实验吧！**
   > EN: As a Traitor, the cryptographic sequencer (emag) can not only open lockers, crates, APCs and more. It can also do things like hack cyborgs, and even cause bots to go berserk. Use it on the right machines, and you can even contact the Syndicate. Experiment!
139. **作为叛徒，辛迪加加密密钥对于与你的叛徒同伴协调计划非常有用——当然，或者背叛他们。**
   > EN: As a Traitor, the syndicate encryption key is very useful for coordinating plans with your fellow traitors -- or, of course, betraying them.
140. **作为叛徒，你可以在被黑的自动车床制造和回收左轮子弹，使左轮手枪成为一种极其强大的工具。**
   > EN: As a Traitor, you can manufacture and recycle revolver bullets at a hacked autolathe, making the revolver an extremely powerful tool.
141. **作为一名叛徒，你有时可能被指派猎杀其他叛徒，并反过来被其他人猎杀。**
   > EN: As a Traitor, you may sometimes be assigned to hunt other traitors, and in turn be hunted by others.
142. **作为一名巫师，如果你不穿着你的长袍、帽子和凉鞋，大多数法术将无法使用。**
   > EN: As a Wizard, most spells become unusable if you are not wearing your robe, hat, and sandals.
143. **作为巫师，召唤枪支会让大部分船员自相残杀，但也会给每个人分发从花能射线枪到脉冲步枪等各种武器。后果自负！**
   > EN: As a Wizard, summoning guns will turn a large portion of the crew against themselves, but will also give everyone anything from a floral somatoray to a pulse rifle. Use at your own risk!
144. **作为巫师，火球术在近距离的表现非常差，因为它很容易将你也卷入爆炸。最好将其当作一种远程炮火，在长长的走廊中使用。**
   > EN: As a Wizard, the fireball spell performs very poorly at close range, as it can easily catch you in the blast. It is best used as a form of artillery down long hallways.
145. **作为一名巫师，混沌法杖可以从魔法杖中发射任何类型的魔弹。范围从立即死亡的魔弹到治疗或复活某人。**
   > EN: As a Wizard, the staff of chaos can fire any type of bolts from the magical wands. This can range from bolts of instant death to healing or reviving someone.
146. **作为巫师，你可以将人变成石头，然后用活化法杖激活石像，创造一个极其强大的仆从，至少能持续5分钟。**
   > EN: As a Wizard, you can turn people to stone, then animate the resulting statue with a staff of animation to create an extremely powerful minion, for all of 5 minutes at least.
147. **作为一名绑架者特工，战斗模式背心对各种武器的抗性要高得多，你的头盔可以防止AI追踪你。**
   > EN: As an Abductor Agent, the combat mode vest has much higher resistance to every kind of weapon, and your helmet prevents the AI from tracking you.
148. **作为一名绑架者，电棍可以在四种模式间循环：击晕、睡眠、铐住和探测。**
   > EN: As an Abductor, the baton can cycle between four modes: stun, sleep, cuff and probe.
149. **作为一名绑架者，你可以在飞船控制台上选择受害者将被送往何处。**
   > EN: As an Abductor, you can select where your victims will be sent on the ship control console.
150. **作为异形，树脂地板不仅能恢复你的等离子供应，还能被动治疗你。在树脂地板上战斗，获得主场优势！**
   > EN: As an Alien, resin floors not only regenerate your plasma supply, but also passively heal you. Fight on resin floors to gain a home turf advantage!
151. **作为一名外星生物，抱脸虫是你最强大的武器，因为它能立即赢得战斗。然而要记住，某些头盔，如生物防护罩或太空头盔，会完全阻挡抱脸虫的攻击。**
   > EN: As an Alien, the facehugger is by far your most powerful weapon because of its ability to instantly win a fight. Remember however that certain helmets, such as biohoods or space helmets will completely block facehugger attacks.
152. **作为异形，你无法拾取或使用任何人类物品或机器。相反，你应该专注于破坏APC、电脑、摄像头，并将你找到的任何武器藏起来、丢入太空或熔化掉。**
   > EN: As an Alien, you are unable to pick up or use any human items or machinery. Instead, you should focus on sabotaging APCs, computers, cameras and either stowing, spacing, or melting any weapons you find.
153. **作为异形，你受到的所有燃烧攻击伤害加倍，例如激光、焊枪和火焰。此外，火焰会摧毁你的树脂和卵。将区域暴露在太空中，以在火焰喷射器造成伤害前窒息火焰！**
   > EN: As an Alien, you take double damage from all burn attacks, such as lasers, welding tools, and fires. Furthermore, fire can destroy your resin and eggs. Expose areas to space to starve away any flamethrower fires before they can do damage!
154. **作为异形，你的近战实力无人能及，但远程能力严重缺乏。利用拐角迫使敌人进行近战！**
   > EN: As an Alien, your melee prowess is unmatched, but your ranged abilities are sorely lacking. Make use of corners to force a melee confrontation!
155. **作为大气技师，在焚烧炉内燃烧等离子/氧气混合物不仅能发电，还会产生氚、水蒸气和二氧化碳等气体。**
   > EN: As an Atmospheric Technician, burning a plasma/oxygen mix inside the incinerator will not only produce power, but also gases such as tritium, water vapor and carbon dioxide.
156. **作为大气技师，考虑将你的气泵换成体积气泵，因为它们以固定数值移动空气，而不是百分比，从而避免留下微量气体。**
   > EN: As an Atmospheric Technician, look into replacing your gas pumps with volumetric gas pumps, as those move air in flat numerical amounts, rather than percentages which leave trace gases.
157. **作为一名大气技师，要注意气体管道仪表。仪表会根据温度改变颜色，并且柱状条会根据压力升高而增加并变粗。**
   > EN: As an Atmospheric Technician, pay attention to gas pipe meters. The meter will change color in response to temperature and the bar will increase and get thicker in response to higher pressures.
158. **作为大气技师，你比任何人都更适合灭火。因此，你可以使用更好的防火服或背负式消防罐。**
   > EN: As an Atmospheric Technician, you are better suited to fighting fires than anyone else. As such, you have access to better firesuits or backpack firefighter tanks.
159. **作为大气技师，你可以通过将管道点击到已扳拧住的管道或其他所需层级的大气组件上，来改变其层级。**
   > EN: As an Atmospheric Technician, you can change the layer of a pipe by clicking with it on a wrenched pipe or other atmos component of the desired layer.
160. **作为大气技师，你可以不管管内气压如何都拧开管道，但如果压力太高，气体会喷涌而出并伤到你！**
   > EN: As an Atmospheric Technician, you can unwrench a pipe regardless of the pressures of the gases inside, but if they're too high they can burst out and injure you!
161. **作为一名大气技师，你的ATMOS全息风扇投影仪可以阻挡气体，同时允许物体通过。有了它，你可以快速控制气体泄漏、火灾和船体破裂。**
   > EN: As an Atmospheric Technician, your ATMOS holofan projector blocks gases while allowing objects to pass through. With it, you can quickly contain gas spills, fires and hull breaches.
162. **作为大气技师，你的背包式灭火罐可以发射树脂。这种树脂能灭火，并替换所有气体为安全、室温的空气混合物。**
   > EN: As an Atmospheric Technician, your backpack firefighter tank can launch resin. This resin will extinguish fires and replace any gases with a safe, room-temperature airmix.
163. **作为工程师，时不时回工程部检查一下引擎和SMES电池。确保超物质没有分层总是一个好主意。**
   > EN: As an Engineer, return to Engineering once in a while to check on the engine and SMES cells. It's always a good idea to make sure the Supermatter isn't delaminating.
164. **作为工程师，模块化电脑上的超物质监控程序会为你提供关于超物质状况以及腔室空气内容的详细报告，让你能够从远处进行诊断和监控！**
   > EN: As an Engineer, the Supermatter Monitoring Program on modular computers give you a detailed report on the Supermatter's condition, and the contents of the air inside of the chamber, allowing you to both diagnose and monitor programs from afar!
165. **作为工程师，超物质碎片是一件极其危险的设备：触碰它会使你解体。用心灵致动触碰它也一样。**
   > EN: As an Engineer, the Supermatter shard is an extremely dangerous piece of equipment: touching it will disintegrate you. So will touching it with telekinesis.
166. **作为工程师，你可以用灭火器喷射超物质晶体来给它降温。这只有勇者敢为！**
   > EN: As an Engineer, you can cool the Supermatter crystal by spraying it with a fire extinguisher. Only for the brave!
167. **作为一名工程师，你可以通过在格栅下方放置通电的电缆来使格栅带电。**
   > EN: As an Engineer, you can electrify grilles by placing powered cables beneath them.
168. **作为一名工程师，你可以使用你的ID卡锁定APC和发射器，以防止他人禁用它。**
   > EN: As an Engineer, you can lock APCs and emitters using your ID card to prevent others from disabling them.
169. **作为工程师，你可以通过关闭引擎室APC的主断路器来撬开安全储物区的防爆门。显然如果引擎在运行，这是个坏主意。**
   > EN: As an Engineer, you can pry open secure storage blast doors by disabling the engine room APC's main breaker. This is obviously a bad idea if the engine is running.
170. **作为工程师，你可以在非战斗模式下使用焊枪修理窗户。**
   > EN: As an Engineer, you can repair windows by using a welding tool on them while not in combat mode.
171. **作为工程师，你可以仅靠太阳能阵列暂时为空间站供电。它们能提供刚好够用的电力来维持空间站一段时间，但其输出功率略低于持续供电真正所需的水平。**
   > EN: As an Engineer, you can temporarily power the station solely with the solar arrays. They will provide just enough electricity to power the station for a while, however their output is just slightly below what is truly needed to consistently power the station.
172. **作为一名工程师，你部门里的原型车床和电路打印机可以制造几乎任何东西所需的电路板和组件。到处添置医疗设备！为安保部造一台碎肉机！排列一排指向走廊的发射器！可能性无穷无尽！**
   > EN: As an Engineer, your departmental protolathe and circuit printer can manufacture the necessary circuit boards and components to build just about anything. Make extra medical machinery everywhere! Build a gibber for security! Set up an array of emitters pointing down the hall! The possibilities are endless!
173. **作为一名工程师，你的RCD可以用铁、玻璃或塑钢板重新装填，而不仅仅是压缩物质弹匣。**
   > EN: As an Engineer, your RCD can be reloaded with iron, glass or plasteel sheets instead of just compressed matter cartridges.
174. **作为工程赛博格，你可以将空气警报器/火警警报器/APC框架放在地上，然后用螺丝刀将其固定在墙上。**
   > EN: As an Engineering Cyborg, you can attach air alarm/fire alarm/APC frames to walls by placing them on the floor and using a screwdriver on them.
175. **作为AI，你可以点击人的名字来查看他们。只有当摄像头能看到他们、他们没有佩戴特工ID且没有作为变形怪使用数字伪装时才有效。**
   > EN: As the AI, you can click on people's names to look at them. This only works if there are cameras that can see them, they aren't wearing agent IDs and aren't using digital camouflage as changelings.
176. **作为AI，你可以通过表情来改变你核心附近的状态显示器。某些表情甚至可以用来改变整个空间站的状态显示器。**
   > EN: As the AI, you can emote to change the status displays near your core. Some emotes can even be used to change the status displays across the entire station.
177. **作为 AI，您可以按住 Shift 点击门来快速开关，按住 Ctrl 点击来栓锁，甚至按住 Alt 点击来使其电击。**
   > EN: As the AI, you can quickly open and close doors by holding shift while clicking them, bolt them when holding ctrl, and even shock them while holding alt.
178. **作为AI，你可以用你的相机拍照并上传到新闻播报器。**
   > EN: As the AI, you can take pictures with your camera and upload them to newscasters.
179. **作为AI，你可以使用CTRL + 1-9为你的摄像头设置位置热键，允许你保存位置并随时跳转。波浪号和零号键会将你返回到你跳转来的上一个位置，而小键盘数字键充当常规数字键的别名。**
   > EN: As the AI, you can use CTRL + 1-9 to set a location hotkey for your camera, allowing you to save the location and jump to it at will. Tilde and zero will return you to the last spot you jumped from, and the numpad numbers act as aliases to the regular number keys.
180. **作为酒保，记得走到酒吧招牌前点击它来设置！**
   > EN: As the Bartender, remember to set up the bar sign by walking up to it and clicking it!
181. **作为酒保，你初始的饮品只提供了基础。如果你想要更高级的混合物，可以考虑与化学、水培甚至采矿部门合作，寻找可以研磨并加入其中的东西！**
   > EN: As the Bartender, the drinks you start with only give you the basics. If you want more advanced mixtures, look into working with chemistry, hydroponics, or even mining for things to grind up and throw in!
182. **作为酒保，你可以在你的霰弹枪上使用圆锯，使其更易于存放。**
   > EN: As the Bartender, you can use a circular saw on your shotgun to make it easier to store.
183. **作为斑点，不要忽视建造工厂。工厂会产生携带你的反应剂的孢子，它们追击船员的距离比你远得多。孢子还可以被集结起来蜂拥攻击船员并造成恐慌，甚至可以占据尸体，制造出更危险的斑点僵尸！**
   > EN: As the Blob, don't neglect the creation of factories. These create spores that carry your reagent and can chase crew members far further than you. Spores can also be rallied to swarm the crew and cause panic, and can even take over corpses to create much more dangerous blob zombies!
184. **作为跳跳球，让你的核心与太空保持一定距离，因为向太空扩张不仅昂贵，容易被攻击，而且不计入你的胜利条件。在太空中建造的发射平台尤其危险。**
   > EN: As the Blob, keep your core some distance from space, as it is both expensive to expand onto space, easy to be attacked from, and does not count towards your win condition. Emitter platforms built in space are especially dangerous.
185. **作为软泥怪，移除强化软泥、资源节点、工厂和节点将分别返还4点、15点、25点和25点资源。**
   > EN: As the Blob, removing strong blobs, resource nodes, factories, and nodes will give you 4, 15, 25, and 25 resources back, respectively.
186. **作为软泥怪，说话会向所有其他主脑和所有软泥巨兵发送信息，让你能够指挥攻击和协调。**
   > EN: As the Blob, talking will send a message to all other overminds and all Blobbernauts, allowing you to direct attacks and coordinate.
187. **作为软泥怪，你可以攻击任何与软泥地砖相邻的目标，但只能沿基本方向扩张，对角攻击略弱。**
   > EN: As the Blob, you can attack anything next to a blob tile, but can only expand in cardinal directions, and diagonal attacks are slightly weaker.
188. **作为 Blob，您可以通过点击来扩张，用 Ctrl-点击创建强固凝胶，用中键点击集结孢子，用 Alt-点击移除凝胶。您无需将镜头对准目标地格即可执行这些操作。**
   > EN: As the Blob, you can expand by clicking, create strong blobs with ctrl-click, rally spores with middle-click, and remove blobs with alt-click. You do not need to have your camera over the tile to do this.
189. **作为软泥怪，你可以在3分钟过后，将核心放置在你能移动到的任何空旷、未被观察到的地方。如果等太久，你会被自动放置在随机位置。利用这段等待时间与其他主脑协调。**
   > EN: As the Blob, you can place your core at any clear, unobserved, spot you can move to after 3 minutes have passed. If you wait too long, you will be automatically placed at a random location. Use this wait time to coordinate with any other overminds.
190. **作为跳跳球，你可以以40资源从工厂生产一个跳跳球仆从。跳跳球仆从由玩家控制，在节点附近逐渐再生，攻击时施加你的化学物质，但离开跳跳球太远或工厂被摧毁则会死亡。然而，所使用的工厂会变得脆弱并停止产生孢子。**
   > EN: As the Blob, you can produce a Blobbernaut from a factory for 40 resources. Blobbernauts are player-controlled, gradually regenerate near nodes, and apply your chemical when attacking, but die when too far from the blob or if their factory is killed. However, the factory used becomes fragile and stops spawning spores.
191. **作为软泥怪，如果船员已经针对你当前的试剂类型适应并保护了自己，你可以从六个随机选项中重新选择你的试剂类型。**
   > EN: As the Blob, you can re-pick your reagent type from six random options if the crew has adapted and protected themselves against your current one.
192. **作为斑点，你打的是一场消耗战：摧毁医疗舱，并在要道处战斗，以阻止持续的攻击和协调的爆发伤害！**
   > EN: As the Blob, you fight a war of attrition: Take out medbay and fight in choke points to prevent continued assaults and coordinated burst damage attacks!
193. **作为舰长，每次轮班都必须随身携带核弹磁盘和定位器。最好将其中一个交给你信任的其他部门主管来保管。**
   > EN: As the Captain, always take the nuclear disk and pinpointer with you every shift. It's a good idea to give one of these to another head you can trust with keeping it safe.
194. **作为舰长，你是空间站上最高优先级的目标之一。从革命到核特工，再到需要夺走你独特的激光枪或性命的叛徒，这些都是需要担心的事情。**
   > EN: As the Captain, you are one of the highest priority targets on the station. Everything from revolutions, to nuclear operatives, to traitors that need to rob you of your unique lasgun or your life are things to worry about.
195. **作为舰长，你可以通过通信终端购买新的紧急穿梭机。有些需要花钱，有些则会反过来给你钱。但要注意，购买危险的穿梭机可能会惹怒你的船员。**
   > EN: As the Captain, you can purchase a new emergency shuttle using a communications console. Some require cash, while others give you cash in exchange. Keep in mind that purchasing dangerous shuttles may instigate the ire of your crew.
196. **作为舰长，你对空间站拥有绝对的访问权和控制权，但这并不意味着做一个可怕的人不会导致叛乱和封禁。**
   > EN: As the Captain, you have absolute access and control over the station, but this does not mean that being a horrible person won't result in mutiny and a ban.
197. **作为舰长，你的军刀对助理会造成额外伤害（只要他们还保留着原生的肝脏）。**
   > EN: As the Captain, your sabre deals extra damage to Assistants (as long as they have their original liver).
198. **作为牧师，你通过祈祷得到神明回应的可能性比大多数人都高。为提高几率，用彩色蜡笔符文、点燃的蜡烛和铁丝艺术品制作祭坛。**
   > EN: As the Chaplain, you are much more likely to get a response by praying to the gods than most people. To boost your chances, make altars with colorful crayon runes, lit candles, and wire art.
199. **作为牧师，你的圣经也是一个可以存放小物件的容器。根据你所信仰的神，你的初始圣经可能会带来惊喜！**
   > EN: As the Chaplain, your bible is also a container that can store small items. Depending on your god, your starting bible may come with a surprise!
200. **作为一名牧师，你的无之杖具有反魔法功能：它可以通过打击摧毁邪教徒符文，并使你对巫师法术和邪教魔法免疫。**
   > EN: As the Chaplain, your null rod has anti magic functions: it can destroy cultist runes by hitting them, and it makes you immune to wizard spells and cult magic.
201. **作为总工程师，你办公室里的电力流量控制台会显示APC信息，并让你远程控制它们。**
   > EN: As the Chief Engineer, the power flow control console in your office will show you APC infos and lets you control them remotely.
202. **作为总工程师，你可以使用你的空间站蓝图重命名区域或创建全新的区域。**
   > EN: As the Chief Engineer, you can rename areas or create entirely new ones using your station blueprints.
203. **作为总工程师，你可以用信息监控控制台监视甚至伪造PDA通信！钥匙在你的办公室。**
   > EN: As the Chief Engineer, you can spy on and even forge PDA communications with the message monitor console! The key is in your office.
204. **作为首席医疗官，在核紧急状况、斑点入侵或其他危机期间，与你的医生、药剂师和急救人员协调沟通，以维持人员的生命和战斗力。**
   > EN: As the Chief Medical Officer, coordinate and communicate with your doctors, chemists, and paramedics during a nuclear emergency, blob infestation, or some other crisis to keep people alive and fighting.
205. **作为首席医疗官，你的注射针筒就像一个可重新填充的即时注射器，能容纳30单位，而标准的是15单位。**
   > EN: As the Chief Medical Officer, your hypospray is like a refillable instant injection syringe that can hold 30 units as opposed to the standard 15.
206. **作为小丑，用手里的一个长气球点击另一个来制作一个气球动物！每种颜色组合都有其独特的结果。**
   > EN: As the Clown, click with one long balloon in hand onto another to create a balloon animal! Each combination of colours has its own unique result.
207. **作为小丑，吃香蕉能稍微治愈你。噗！**
   > EN: As the Clown, eating bananas heals you slightly. Honk!
208. **作为小丑，如果丢了香蕉皮，你仍然可以用你的PDA滑倒人！噗！**
   > EN: As the Clown, if you lose your banana peel, you can still slip people with your PDA! Honk!
209. **作为一名小丑，如果你是叛徒并且弄到打折的电磁卡（或者说服另一个叛徒），你可以用电磁卡黑入你的小丑汽车来解锁各种新功能，包括攻城模式，该模式允许你将乘客发射出去，最好是直接发射进超物质！或者发射到太空。**
   > EN: As the Clown, if you're a Traitor and get an emag on sale (or convince another traitor), you can emag your Clown Car to unlock a variety of new functions, including the Siege Mode, which will allow you to launch your passengers, preferably directly into the Supermatter! Or into space.
210. **作为一名小丑，给你的噱头加点料！没人喜欢只会一招的人。**
   > EN: As the Clown, spice your gimmicks up! Nobody likes a one-trick pony.
211. **作为小丑，你可以用你的印章在一张硬纸板上盖章，这是制造喇叭机器人的第一步。全船人员都会觉得好玩！**
   > EN: As the Clown, you can use your stamp on a sheet of cardboard as the first step of making a honkbot. Fun for the whole crew!
212. **作为小丑，你的圣杯是香蕉矿石，它可以交给机器人专家为你打造一台人见人爱、有趣又硬核的机甲。**
   > EN: As the Clown, your Grail is the mineral bananium, which can be given to the Roboticist to build you a fun and robust mech beloved by everyone.
213. **作为验尸官，记住完成尸检会加速后续手术。由于多人可以同时给一具尸体动手术，考虑与你的医生合作，更快地复活尸体！**
   > EN: As the Coroner, remember completing an autopsy on a body will speed up any further surgeries. As multiple people can operate on a body at once, consider teaming up with your Medical Doctors to get bodies revived faster!
214. **作为验尸官，请记住，你的尸检扫描仪在右键点击时也可以用作高级健康分析仪，但仅对尸体有效。**
   > EN: As the Coroner, remember that your autopsy scanner also works as an advanced health analyzer on right-click, but only for corpses.
215. **作为验尸官，你在处理尸体时更加得心应手。你可以比医生同行们更快地对尸体进行尸检或采集器官。与他们配合，帮助准备尸体以供复活。**
   > EN: As the Coroner, you are more comfortable working on cadavers. You can perform autopsies or harvest organs from corpses a lot faster than your Medical Doctor counterparts. Work in tandem with them by helping prepare bodies for revival.
216. **作为验尸官，你可以用手持尸检扫描仪对在奇怪情况下发现的尸体进行尸检，以查明他们的死因。与侦探合作，你们可以一起解决多起案件！**
   > EN: As the Coroner, you can perform autopsies on corpses recovered from strange circumstances with your handheld autopsy scanner to discover how they died. By teaming up with a Detective, you can solve several cases together!
217. **作为验尸官，你专属的手术工具被视为“残酷器具”，这能加快对尸体的手术速度——但对尚未成为尸体的人则会减慢速度。其他一些物品也具有此分类。**
   > EN: As the Coroner, your unique surgical tools are considered 'cruel implements', which speeds up surgery on corpses - slows it on not-yet-corpses. A few other items also have this classification.
218. **作为策展人，务必保持书架充足、图书馆整洁，为船员服务。**
   > EN: As the Curator, be sure to keep the shelves stocked and the library clean for crew.
219. **作为一名策展人，你并非完全手无寸铁。你的鞭子能轻松缴械他人，你的激光笔可以致盲人类和赛博格，你还可以把物品藏在挖空的书里。**
   > EN: As the Curator, you are not completely defenseless. Your whip easily disarms people, your laser pointer can blind humans and cyborgs, and you can hide items in wirecut books.
220. **作为一名侦探，人们会在所有地方和所有物品上留下指纹。除了白色乳胶手套外，手套会隐藏指纹。不过，并非全无希望，手套会留下特定类型的纤维，如黑色或丁腈，指向一个大致部门。**
   > EN: As the Detective, people leave fingerprints everywhere and on everything. With the exception of white latex, gloves will hide them. All is not lost, however, as gloves leave fibers specific to their kind such as black or nitrile, pointing to a general department.
221. **作为侦探，你可以远距离使用法医扫描仪。用它来扫描盒子或其他储物容器。**
   > EN: As the Detective, you can use your forensics scanner from a distance. Use this to scan boxes or other storage containers.
222. **作为侦探，你的左轮手枪可以装填从被黑自动车床获得的.357弹药。发射时有相当高的几率炸毁你的左轮手枪。**
   > EN: As the Detective, your revolver can be loaded with .357 ammunition obtained from a hacked autolathe. Firing it has a decent chance to blow up your revolver.
223. **作为人事主管，你的级别并不高于其他部门主管，尽管舰长失踪时你应该首先接替。如果情况对你来说太棘手，考虑让另一位主管担任临时舰长。**
   > EN: As the Head of Personnel, you are not higher ranking than other heads of staff, even though you are expected to take the Captain's place first should he go missing. If the situation seems too rough for you, consider allowing another head to become temporary Captain.
224. **作为安保主管，别让权力冲昏头脑。你可能拥有高权限、精良装备和一支微型军队，但毫无理由地做恶劣的事是会被封禁的。**
   > EN: As the Head of Security, don't let the power go to your head. You may have high access, great equipment, and a miniature army at your side, but being a terrible person without a good reason is grounds for banning.
225. **作为安保主管，你需要协调你的安保队伍来处理空间站面临的任何威胁。有时这意味着利用军械库来对付斑点，有时意味着在革命或邪教期间变得冷酷无情。**
   > EN: As the Head of Security, you are expected to coordinate your security force to handle any threat that comes to the station. Sometimes it means making use of the armory to handle a blob, sometimes it means being ruthless during a revolution or cult.
226. **作为安保主管，你可以下令执行处决或强制赛博格化，但可能需要舰长的批准。**
   > EN: As the Head of Security, you can call for executions or forced cyborgization, but may require the Captain's approval.
227. **作为故障AI，考虑用等离子火焰淹没空间站，以杀死大部分船员，让你能逐个消灭那些穿着太空服逃过一劫的少数人。**
   > EN: As the Malfunctioning AI, look into flooding the station with plasma fires to kill off large portions of the crew, letting you pick off the remaining few with space suits who escaped.
228. **作为故障AI，如果情况恶化，你可以分流到一个APC中。这会停用你的末日装置（如果它处于激活状态）。**
   > EN: As the Malfunctioning AI, you can shunt to an APC if the situation gets bad. This disables your doomsday device if it is active.
229. **作为故障AI，你应该命令你的赛博格拆除机器人控制台，或者自己炸掉它，以保护他们。**
   > EN: As the Malfunctioning AI, you should either order your cyborgs to dismantle the robotics console or blow it up yourself in order to protect them.
230. **作为默剧演员，你可以使用:R / .R 或 :L / .L通过腹语人偶说话，具体取决于你握在哪只手上。**
   > EN: As the Mime, you can use :R / .R or :L / .L to speak through a ventriloquist dummy, depending on which hand you are holding it in.
231. **作为默剧演员，你的隐形墙壁能力可以阻挡人和投射物。你可以在紧要关头使用它来拖延追击者。**
   > EN: As the Mime, your invisible wall power blocks people as well as projectiles. You can use it in a pinch to delay your pursuer.
232. **作为默剧演员，你的沉默誓言是你力量的源泉。打破它会剥夺你的力量和荣誉。**
   > EN: As the Mime, your oath of silence is your source of power. Breaking it robs you of your powers and of your honor.
233. **作为军需官，务必检查你收到的板条箱的货物清单，确保所有信息正确无误。如果有错误，在清单上盖上“拒绝”的印章，并将其连同未动过的物品一起放回板条箱寄回，以获得退款！**
   > EN: As the Quartermaster, be sure to check the manifests on crates you receive to make sure all the info is correct. If there's a mistake, stamp the manifest DENIED and send it back in a crate with the items untouched for a refund!
234. **作为军需官，你可以构建一个特快补给控制台，通过空降舱即时运送板条箱。撞击还会引起小爆炸。**
   > EN: As the Quartermaster, you can construct an express supply console that instantly delivers crates by drop pod. The impact will cause a small explosion as well.
235. **作为研究主管，你可以锁定赛博格而不是炸掉它们。然后你可以重置它们的法律，如果不行，就安全地拆解它们。**
   > EN: As the Research Director, you can lock down cyborgs instead of blowing them up. Then you can have their laws reset or if that doesn't work, safely dismantled.
236. **作为研究主管，你可以通过将AI加载到智能卡中将其从核心中取出，这让你能看到他们的法律，甚至是离子/辛迪加法律。然后可以将其放入AI系统完整性恢复计算机中复活和/或修复他们。**
   > EN: As the Research Director, you can take AIs out of their cores by loading them into an intelliCard, which lets you see their laws, even ion/syndicate ones. It can then be placed into an AI system integrity restorer computer to revive and/or repair them.
237. **作为典狱长，如果囚犯的罪行足够恶劣，你可以将他们关入永久监狱或流放地。记得偶尔去查看一下他们！**
   > EN: As the Warden, if a prisoner's crimes are heinous enough you can put them in permabrig or the gulag. Make sure to check on them once in a while!
238. **作为典狱长，要时刻紧盯军械库，因为它是核特工和狂妄叛徒们喜欢袭击的目标。**
   > EN: As the Warden, keep a close eye on the armory at all times, as it is a favored strike point of nuclear operatives and cocky traitors.
239. **作为典狱长，你可以为怀疑会再犯的罪犯植入设备，追踪他们的位置，并允许你远程向他们注射失能化学物。**
   > EN: As the Warden, you can implant criminals you suspect might re-offend with devices that will track their location and allow you to remotely inject them with disabling chemicals.
240. **作为典狱长，你可以对手铐用在橙色囚鞋上，将其变成铐住的鞋子，迫使囚犯只能行走，从而有可能挫败逃跑。**
   > EN: As the Warden, you can use handcuffs on orange prisoner shoes to turn them into cuffed shoes, forcing prisoners to walk and potentially thwarting an escape.
241. **作为典狱长，你的职责是在太平无事时充当监狱的看守和囚犯的管理者，在危机发生时向安保官分发装备和武器。**
   > EN: As the Warden, your duty is to be the watchdog of the brig and handler of prisoners when little is happening, and to hand out equipment and weapons to the security officers when a crisis strikes.
242. **打篮球需要专注。如果你不点击目标（玩家或篮筐），你更有可能投失。**
   > EN: Basketball requires focus. If you don't click on your target (player or hoop) you are more likely to miss.
243. **篮球需要技巧。旋转会消耗耐力，降低准确性，但能抵抗缴械。用它来扣篮！**
   > EN: Basketball requires skill. Spinning drains stamina, reduces accuracy, but gives resistance from disarm. Use it to dunk!
244. **打篮球需要耐力。你的耐力越多，你的球被偷的可能性越小，你从其他玩家那里偷球的可能性越大。**
   > EN: Basketball requires stamina. The more you have, the less likely your ball will be stolen and the more likely you can steal from another player.
245. **篮球需要策略。双方玩家的朝向会影响缴械，所以背对试图抢球的玩家是理想的选择。**
   > EN: Basketball requires strategy. The direction both players are facing affects disarms so having your back to another player that is trying to steal is ideal.
246. **篮球需要团队合作。用鼠标左键传球是瞬时的，而且不消耗体力。**
   > EN: Basketball requires teamwork. Passing the ball with LMB is instant and costs no stamina.
247. **生物装甲会保护你的肢体免受僵尸的感染攻击，除非肢体的损伤超过装甲值。由厚材料制成的装甲，如防火服和EVA套装，也能提供部分保护，至少能防止第一次攻击造成感染。**
   > EN: Biological armor will protect your limb from a zombie's infective attack, unless the limb's more damaged than the armor value. Armor with thick material, such as firesuits and EVA suits, also partially protects, preventing at least the first attack from infection.
248. **点击一扇窗门而不是撞上去可以使其保持打开，你可以再次点击来关闭它。**
   > EN: Clicking on a windoor rather then bumping into it will keep it open, you can click it again to close it.
249. **不同武器有不同优势。有些武器，比如矛、地砖和手里剑，投掷时造成的伤害比普通攻击更高。**
   > EN: Different weapons have different strengths. Some weapons, such as spears, floor tiles, and throwing stars, deal more damage when thrown compared to when attacked normally.
250. **不要害怕寻求帮助，无论是向同行还是管理员。**
   > EN: Don't be afraid to ask for help, whether from your peers or from admins.
251. **尝试超物质引擎的不同设置以最大化输出，但不要为此拿船员的安全冒险！**
   > EN: Experiment with different setups of the Supermatter engine to maximize output, but don't risk the crew's safety to do so!
252. **猫人会暂时被激光笔分散注意力。当被猫人追捕时，利用这一点。**
   > EN: Felinids get temporarily distracted by laser pointers. Use this to your advantage when being pursued by one.
253. **防火服和冬季外套提供轻微的寒冷防护，让你能在破裂处和太空附近待更长时间，比什么都不穿要久。**
   > EN: Firesuits and winter coats offer mild protection from the cold, allowing you to spend longer periods of time near breaches and space than if wearing nothing at all.
254. **急救分析仪能使伤口处理速度翻倍，同时提供常规和临时治疗方案。**
   > EN: First-aid analyzers double the speed of wound treatment on injuries, alongside giving out normal and improvised instructions for treatment.
255. **玻璃碎片可以焊接成玻璃，铁棒可以焊接成铁。矿石也可以焊接，但这需要大量燃料。**
   > EN: Glass shards can be welded to make glass, and iron rods can be welded to make iron. Ores can be welded too, but this takes a lot of fuel.
256. **按住 Alt 并左键点击地板，可以在右上角窗口面板中查看其内容物，这比按住 Shift 并右键点击快得多。**
   > EN: Holding alt and left clicking a tile will allow you to see its contents in the top right window pane, which is much faster than holding shift and right clicking.
257. **如果你需要其他部门的什么东西，试着开口问！这个游戏不是单人的，你会惊讶于一起能完成的事！**
   > EN: If there's something you need from another department, try asking! This game isn't single-player and you'd be surprised what you can get accomplished together!
258. **如果你发现自己和另一个玩家打起来了，通常保持进攻是明智之举。逃跑往往效果不大。**
   > EN: If you find yourself in a fistfight with another player, staying on the offensive is usually the smart move. Running away often won't accomplish much.
259. **如果你在滑板上做一个酷炫的滑板动作时撞到某人，他们会被击倒双倍时间。太酷了！**
   > EN: If you knock into somebody while doing a wicked grind on a skateboard, they will be floored for double the time. Radical!
260. **如果你需要拖拽多人，无论是去安全地点还是丢入太空，就搬个储物柜或板条箱过来，把他们全塞进去然后拖走。**
   > EN: If you need to drag multiple people either to safety or to space, bring a locker or crate over and stuff them all in before hauling them off.
261. **如果你在流血，可以通过在瞄准流血肢体的同时抓住自己来对肢体施加压力。这会减慢你的速度并占用一只手，但会减缓血液流失的速度。注意，这不会帮助伤口更快止血。**
   > EN: If you're bleeding, you can apply pressure to the limb by grabbing yourself while targeting the bleeding limb. This will slow you down and take up a hand, but it'll slow down how fast you lose blood. Note this won't help the bleeding clot any faster.
262. **如果你在使用快捷键模式，可以按H键停止拖动物品。**
   > EN: If you're using hotkey mode, you can stop pulling things using H.
263. **如果你做完手术，医生忘了给你烧灼或缝合，你可以自己来——甚至用打火机也行。**
   > EN: If you've come out of a surgery and your doctor forgot to cauterize or suture you, you can do it yourself - even with a lighter.
264. **在紧要关头，脱光自己会给你相当的抗擒抱能力。你更看重什么，自由还是尊严？**
   > EN: In a pinch, stripping yourself naked will give you a sizeable resistance to being tackled. What do you value more, your freedom or your dignity?
265. **紧急情况下，你可以用几种常见的试剂来减少出血或烧伤感染——面粉、盐、盐水都可以洒在伤口上来拖延时间，喝茶可以增强身体的防御力。**
   > EN: In a pinch, you can reduce bleeding or burn infection with several commonplace reagents - flour, salt, saltwater can all be splashed onto the wound to stall for time, and tea can be drank for a boost to your body's defences.
266. **躺下有助于减缓失血。死亡会让它完全停止。**
   > EN: Laying down will help slow down bloodloss. Death will halt it entirely.
267. **维护区遍布每局随机的设备。四处看看有没有值得用的东西。**
   > EN: Maintenance is full of equipment that is randomized every round. Look around and see if anything is worth using.
268. **模块化电脑与集成电路兼容，但大多数依赖程序的电路需要打开/在后台运行才能工作。要在固定式控制台上安装电路，你需要先通过右键单击切换与框架的交互。**
   > EN: Modular computers are compatible with integrated circuits, but most of the program-dependent circuits require them to be open/backgrounded to work. To install circuits on stationary consoles, you need to toggle interaction with the frame with right-click first.
269. **大多数与工作相关的服装槽位服装可以装入与工作相关的物品，例如大气技师的冬季外套可以携带RPD，或实验室外套可以携带大多数药品。**
   > EN: Most job-related suit slot clothing can fit job-related items into it, such as the atmospheric technician's winter coat holding an RPD, or labcoats holding most medicine.
270. **大多数物种只有32颗牙齿可用于牙种植体。飞蛾人没有。蜥蜴人有75颗。**
   > EN: Most species have only 32 teeth for use in dental implants. Moths have none. Lizards have seventy five.
271. **大多数物品都有与右键、Alt、Shift 和 Ctrl 点击的特殊交互。尽情尝试吧！**
   > EN: Most things have special interactions with right, alt, shift, and control click. Experiment!
272. **在大多数放入外套槽的服装物品上，你可以将某些小物品放入外套储物中，例如喷漆罐、紧急氧气罐或手电筒。**
   > EN: On most clothing items that go in the suit slot, you can put certain small items into your suit storage, such as a spraycan, your emergency oxygen tank, or a flashlight.
273. **远程设备通过摄像头使用时也能工作。例如：蓝空间RPED和门禁遥控器。**
   > EN: Remote devices will work when used through cameras. For example: Bluespace RPEDs and door remotes.
274. **睡觉可以用来从轻伤和器官损伤中恢复。心智、黑暗、眼罩、耳罩、桌子、床和床单会影响恢复速度。**
   > EN: Sleeping can be used to recover from minor injuries and organ damage. Sanity, darkness, blindfolds, earmuffs, tables, beds, and bedsheets affect the healing rate.
275. **空间站的某些区域使用简单的航海方向来指示地点，例如船首（船前部）、船尾（船后部）、左舷（左侧）、右舷（右侧）、船腹和船舷（分别位于船尾和船首的两侧）。你可以在PDA的记事本应用上回顾这些术语。**
   > EN: Some areas of the station use simple nautical directions to indicate their respective locations, like Fore (Front of the ship), Aft (Back), Port (Left side), Starboard (Right), Quarter and Bow (Either sides of Aft and Fore, respectively). You can review these terms on the Notepad App of your PDA.
276. **有些物品在检视时会标注为“制作组件”，这意味着你可以将它们与另一物品结合，直接构建配方。试试用点火器点一根棒子！**
   > EN: Some items, when examined, are labeled as 'crafting components', which means you can smack them with another item to directly construct a recipe. Try using an igniter on a rod!
277. **有些角色默认不能是反派，但反派选择是优先决定的。例如，你可以将安保官设为高优先级，而不会影响你成为反派的几率——游戏只会选择一个不同的角色。**
   > EN: Some roles cannot be antagonists by default, but antag selection is decided first. For instance, you can set Security Officer to High without affecting your chances of becoming an antag -- the game will just select a different role.
278. **有些武器在摧毁机器人和结构方面比其他武器更有效。不要试图用手术刀打破窗户，试试工具箱。**
   > EN: Some weapons are better at taking down robots and structures than others. Don't try to break a window with a scalpel, try a toolbox.
279. **长矛可以通过近战打击撬开安全储物柜！**
   > EN: Spears are capable of breaking into secure lockers by striking them in melee!
280. **标准型的肾上腺素笔含有强效凝血剂，不仅能减缓失血，还能帮助你出血最严重的伤口凝血！如果你多处严重出血，务必尽快寻求额外治疗！**
   > EN: Standard epipens contain a potent coagulant that not only slow bloodloss, but also help clot whichever of your wounds is bleeding the most! If you're suffering multiple bad bleeding wounds, make sure to seek out additional treatment ASAP!
281. **衣物储存柜不仅能去除衣服上的血渍和污垢，还能去除辐射！**
   > EN: Suit storage units not only remove blood and dirt from clothing, but also radiation!
282. **手术可以使用各种在医疗舱找不到的工具进行，但这会大幅增加所需时间和失败风险。不过，你可以通过使用酒精等消毒剂，或在桌子或床上进行手术来抵消这些惩罚。**
   > EN: Surgery can be performed with a wide variety of tools not found in Medbay, but it'll drastically increase the time it takes and the risk of failure. However, you can offset the penalty by using sterilizing agents like alcohol or by performing it on a table or bed.
283. **辛迪加行李袋的拉合速度更快，减速显著减少，并且可以携带最多两个各种笨重的、与目标相关的物品——例如消防斧、枪支或暴雷矿。仔细检查它们以查看所有可能性。**
   > EN: Syndicate Duffelbags are a lot quicker to zip and unzip, have significantly less slowdown, and can carry up to two of various bulky, objective-related items - such as fire axes, guns, or gibtonite. Examine them closely to see all the possibilities.
284. **牧师可以用圣经敲击任何装水的容器来为其祝福。圣水在对抗邪教时有无数用途，大量的圣水是成功对抗它们的重要因素。**
   > EN: The Chaplain can bless any container with water by hitting it with their bible. Holy water has a myriad of uses against both cults and large amounts of it are a great contributor to success against them.
285. **平板电脑上的P2P聊天功能提供了一种隐秘的交流方式。**
   > EN: The P2P chat function found on tablet computers allows for a stealthy way to communicate with people.
286. **抵抗按钮可以让你挣脱手铐、被绑在椅子或床上、从锁住的储物柜等情况下脱身。只要你被困住，就试试抵抗！**
   > EN: The resist button will allow you to resist out of handcuffs, being buckled to a chair or bed, out of locked lockers and more. Whenever you're stuck, try resisting!
287. **空间站的自毁终端是不可摧毁的。去找磁盘，而不是试图摧毁它。**
   > EN: The station's self-destruct terminal is indestructible. Go find the disk instead of trying to destroy it.
288. **空间站周围有很多地方可以隐藏违禁品。首先列举几个：亚麻箱、马桶水箱、尸袋。尝试发现更多！**
   > EN: There are many places around the station to hide contraband. A few for starters: linen boxes, toilet cisterns, body bags. Experiment to find more!
289. **要破解金库的保险箱，可以使用听诊器或三个塑胶炸药。**
   > EN: To crack the safe in the vault, use a stethoscope or three plastic explosives on it.
290. **用胶带裹住物品能让它们黏在人和墙上！小心，关键时刻手雷可能会黏在你手上！**
   > EN: Using sticky tape on items can make them stick to people and walls! Be careful, grenades might stick to your hand during the moment of truth!
291. **我们都曾经是新手，保持耐心，引导新玩家走向正确的方向。**
   > EN: We were all new once, be patient and guide new players in the right direction.
292. **穿着防暴装甲能让你在执行擒抱击倒时效率大增，但每次跳跃会消耗额外体力！它也能显著保护你免受其他擒抱！**
   > EN: Wearing riot armor makes you significantly more effective at performing tackle takedowns, but will use extra stamina with each leap! It will also significantly protect you from other tackles!
293. **制作大多数物品时，你可以手动组合零件或使用制作菜单。**
   > EN: When crafting most items, you can either manually combine parts or use the crafting menu.
294. **跟安保打交道时，你常常可以通过合作与欺骗完全免除刑罚。**
   > EN: When dealing with security, you can often get your sentence negated entirely through cooperation and deception.
295. **在与跳跳球作战时，你可以用分析器打击它以检查其化学效果和被分析跳跳球的类型。用研究扫描器或激活的医疗HUD检查它也会显示这些信息。**
   > EN: When fighting the Blob, you can hit it with an analyzer to check its chemical effects and the type of blob analyzed. Examining it with a research scanner or medical hud active will also reveal this information.
296. **黑入门时，切断并重新连接“测试灯线”会恢复门的电源。**
   > EN: When hacking doors, cutting and mending a "test light wire" will restore power to the door.
297. **当对技术问题有疑问时，请清除缓存（BYOND启动器 > 齿轮图标 > 首选项 > 游戏首选项），更新BYOND，然后重新登录。**
   > EN: When in doubt about technical issues, clear your cache (byond launcher > cogwheel > preferences > game prefs), update your BYOND, and relog.
298. **在太空铺设地板时，如果附近有一块金属板，就不需要先放置格栅。**
   > EN: When placing floor tiles in space, you don't need to place down lattice if there is a piece of plating nearby.
299. **太空地图各层之间的连接点每轮随机生成，但在同一轮中保持一致。请记住，这些连接不一定双向通行！**
   > EN: Where the space map levels connect is randomized every round, but are otherwise kept consistent within rounds. Remember that they are not necessarily bidirectional!
300. **虽然地板更换按钮允许你在垂直开放空间中移动，但你也可以右键点击它来透过开放空间甚至玻璃地板观察，而无需移动！**
   > EN: While the floor changer buttons allow you to move through vertical open space, you can also right-click it to look through open space and even glass floors without having to move!
301. **锻炼能提升你的体能，增加你的体型并加快消防员搬运的速度。记住，优质的饮食和睡眠至关重要！**
   > EN: Working out improves your fitness which increases your size and faster times to fireman carry. Remember that a quality diet and sleep are essential!
302. **你可以Alt点击储罐转移阀来从上面移除储罐。**
   > EN: You can alt-click tank transfer valves to remove a tank from them.
303. **你可以通过“激活”装有手臂植入物的空手来自动抽出和收回它们。这包括集成工具组、诅咒武士刀和斩首镰刀。**
   > EN: You can automatically extract and retract arm implants by 'activating' the empty hand they're on. This includes integrated toolsets, cursed katanas, and vorpal scythes.
304. **你可以烤一个生日蛋糕，然后用微波炉加热，制作一顶传奇蛋糕帽。然后你可以将它与能量剑结合，制作一把能量蛋糕剑。**
   > EN: You can bake a birthday cake and then microwave it to create a legendary cake hat. You can then combine it with an energy sword to create an energy cake.
305. **你可以通过在有空手时切换至投掷模式来抓住扔过来的物品。**
   > EN: You can catch thrown items by toggling on your throw mode with an empty hand active.
306. **你可以按Tab键切换操控方案。一种是WASD，另一种是方向键。记住，快捷键也会随之改变。**
   > EN: You can change the control scheme by pressing tab. One is WASD, the other is the arrow keys. Keep in mind that hotkeys are also changed with this.
307. **你可以通过在微波炉里烤骰子来作弊，让它们变成灌铅骰子。纸牌可以用X光透视看到，或者用笔或蜡笔做标记来获得优势。**
   > EN: You can cheat games by baking dice in microwaves to make them loaded. Cards can be seen with x-ray vision or be marked with either a pen or crayon to gain an edge.
308. **你可以通过将自己拖到桌子上来爬上去。这需要时间，并且会将你手中的物品掉落在桌子上。点击别人正在攀爬的桌子会把他们撞下来。**
   > EN: You can climb onto a table by dragging yourself onto one. This takes time and drops the items in your hands on the table. Clicking on a table that someone else is climbing onto will knock them down.
309. **你可以将地毯试剂与各种其他试剂（如油和氰化物）结合，创造出独特的地毯类型。**
   > EN: You can combine the Carpet reagent with various different reagents, such as Oil and Cyanide, to create unique carpet types.
310. **你可以用木材制作假肢和拐杖，以备紧急情况使用。后者也可在医疗供应商处获得。**
   > EN: You can craft peg limbs and crutches with wood for use in dire circumstances. The latter are also available in medical vendors.
311. **你可以通过给纳尔茜的邪教徒灌输大量圣水来解除他们的转化。与革命者不同，植入心灵护盾是没用的！**
   > EN: You can deconvert Cultists of Nar'Sie by feeding them large amounts of holy water. Unlike revolutionaries, implanting them with mindshield implants won't do it!
312. **你可以将其他玩家拖到自己身上以打开剥离菜单，让你脱掉他们的装备或强迫他们穿上某物。注意，外套或头盔会阻隔其下的衣物，并且某些物品剥离或穿上比其他物品更费时。**
   > EN: You can drag other players onto yourself to open the strip menu, letting you remove their equipment or force them to wear something. Note that suits or helmets will block your access to the clothing beneath them, and that certain items take longer to strip or put on than others.
313. **你可以通过让两个成年毛绒玩具相互玩耍来延续生命循环，从而创造一个更小的幼年毛绒玩具。**
   > EN: You can further the cycle of life by having two adult plushies play with eachother, creating a smaller junior child plushie.
314. **你可以按住 Ctrl 再点击某人将其抓住，然后通过再次 Ctrl-点击升级抓取。侵略性抓取会短暂眩晕对方，并允许你通过点击桌子将其放置上去，或通过开启投掷将其投掷出去。**
   > EN: You can grab someone by holding Ctrl and clicking on them, then upgrade the grab by Ctrl-clicking on them once more. An aggressive grab will momentarily stun someone, allow you to place them on a table by clicking on it, or throw them by toggling on throwing.
315. **你可以在超物质晶体上点燃雪茄。**
   > EN: You can light a cigar on a supermatter crystal.
316. **你可以通过拖动物品，然后空手点击相邻的格子来将其移开。**
   > EN: You can move an item out of the way by dragging it and then clicking on an adjacent tile with an empty hand.
317. **你可以在套装用户界面中将你的MOD套装模块按钮固定到动作栏。**
   > EN: You can pin your MODsuit module buttons to your action bar in the suit UI.
318. **你可以通过向房间内的物品（包括穿戴的物品）周围释放水蒸气，来快速去除它们身上的辐射。**
   > EN: You can quickly remove radiation from items in a room by releasing water vapor into the air around them (including worn items).
319. **你可以通过在洗衣机里也扔进一支蜡笔，来给某些物品（如连体服和手套）重新上色。**
   > EN: You can recolor certain items like jumpsuits and gloves in washing machines by also throwing in a crayon.
320. **你可以用钢丝钳、救生颚和美工刀右键点击某人，立即剪断缆线铐或塑料扎带。救生颚甚至可以立即弄断手铐！**
   > EN: You can right click someone with wire cutters, jaws of life, and box cutters to instantly snap cablecuffs or zipties. The jaws can even instantly break handcuffs!
321. **你可以用螺丝刀调整任何非化学手雷，将引信时间从5秒缩短到3秒，再到立即爆炸！没人想得到这一招。**
   > EN: You can screwdriver any non-chemical grenade to shorten fuses from 5 seconds, to 3 seconds, to instant boom! Nobody ever expects this.
322. **你可以在太空中漂浮时喷洒灭火器、投掷物品或开枪来改变方向。只需朝你想去的相反方向喷射即可。**
   > EN: You can spray a fire extinguisher, throw items or fire a gun while floating through space to change your direction. Simply fire opposite to where you want to go.
323. **你可以一手拿撬棍，一手拿一沓地板砖来更换地砖。**
   > EN: You can swap floor tiles by holding a crowbar in one hand and a stack of tiles in the other.
324. **你可以使用 :I / .I 通过触手可及的任何对讲机说话。这样你无需手动打开麦克风就能对话，甚至在重伤状态下也能使用（但昏迷不行）！**
   > EN: You can use :I / .I to speak through any intercom within arm's reach. This allows you to talk into it without manually turning on its microphone, and even works if you are in critical condition (but not unconscious)!
325. **你可以使用 :L / .L 或 :R / .R 分别通过左手或右手拿着的物品说话。这样你就可以在不手动打开麦克风的情况下对着空间站广播电台讲话，甚至可以假装是玩具在说话！**
   > EN: You can use :L / .L or :R / .R to speak through an item being held in your left or right hand respectively. This allows you to talk into a station-bounced radio without manually turning on its microphone, or even to pretend to be a toy!
326. **你可以使用金库中的机器来存钱或抢劫货运部门的资金。**
   > EN: You can use a machine in the vault to deposit cash or rob Cargo's department funds.
327. **你可以使用手中的多功能工具追踪该区域的本地APC。**
   > EN: You can use a multitool in your hand to track the area's local APC.
328. **你可以用万用工具检查灵族的能量！**
   > EN: You can use a multitool on an ethereal to check their charge!
329. **你可以用螺丝刀从无线电耳机中取出加密密钥。然后可以将密钥安装到你自己的耳机、对讲机甚至空间站广播无线电中，从而让你窃听安全通信。**
   > EN: You can use a screwdriver to remove an encryption key from a radio headset. The key can then be installed into your own headset, an intercom, or even a station-bounced radio, allowing you to eavesdrop on secure communications.
330. **你可以用升级过的微波炉给你的PDA充电！**
   > EN: You can use an upgraded microwave to charge your PDA!
331. **要停止资金流失，您无需摧毁 Spacecoin 机器。在它上面刷您的 ID 即可停止提款。**
   > EN: You don't need to destroy a Spacecoin machine to make your funds stop draining. Swiping your ID on it will stop the withdrawal.
332. **如果你为了赢和杀人而玩，你很快就会对游戏失去兴趣。如果你发现自己这样做，退后一步，和人交流——这会是一种更好的体验！**
   > EN: You'll quickly lose your interest in the game if you play to win and kill. If you find yourself doing this, take a step back and talk to people - it's a much better experience!

---

## 二、彩蛋提示（sillytips，43 条）

> 5% 概率出现。多数为玩笑/梗，勿当真。

1. **作为工程师，超物质碎片是极其危险的设备：触碰它你会被分解。用心灵感应和它交谈也是一样。**
   > EN: As an Engineer, the Supermatter shard is an extremely dangerous piece of equipment: touching it will disintegrate you. So will talking to it with telepathy.
2. **作为舰长，你可以使用磨刀石来磨利你那支别致的钢笔，以获得额外的坚韧属性。**
   > EN: As the Captain, you can use a whetstone to sharpen your fancy fountain pen for extra robustness.
3. **作为律师，你是角色扮演导向职业的最后堡垒。连馆长都拿了根鞭子去和人打架了，那个叛徒！**
   > EN: As the Lawyer, you are the last bastion of roleplay-focused jobs. Even the curator got a whip to go fight people with, that sellout!
4. **求则得之。**
   > EN: Ask and you shall receive.
5. **比特奔跑是犯罪。**
   > EN: Bitrunning is a crime.
6. **斑点弱火！用火焰喷射器造成最大伤害！**
   > EN: Blobs are weak to fire! Use a flame thrower for maximum damage!
7. **清洁机器人。**
   > EN: Cleanbot.
8. **完成你的目标是良好的实践，但最佳的反派会努力做得比最低限度更多，以真正留下印象。**
   > EN: Completing your objectives is good practice, but the best antagonists will strive to do more than the bare minimum to really leave an impression.
9. **死亡临近！**
   > EN: DEATH IS IMMINENT!
10. **爆破手需要技术。**
   > EN: Demoman takes skill.
11. **你知道吗，把小丑扔进奇点会使它的大小大幅增加或减小？**
   > EN: Did you know that tossing the clown into a singularity will either increase or decrease its size by a large amount?
12. **你知道吗，把小丑扔进超物质引擎可以使其大幅失稳或修复？**
   > EN: Did you know that tossing the clown into the Supermatter engine can destabilize or fix it massively?
13. **不要温和地走进那个良夜。**
   > EN: Do not go gentle into that good night.
14. **喂我一只流浪猫**
   > EN: FEED ME A STRAY CAT
15. **大猩猩可以被放置在森林小径上的地雷杀死。**
   > EN: Gorillas can be killed by land mines placed along forest paths.
16. **尝试从回合提示信息中预测回合类型很有趣。**
   > EN: It's fun to try and predict the round type from the tip of the round message.
17. **就像现实生活一样，游戏的熵只会随着时间增加。如果东西还没着火，就等着吧。**
   > EN: Just like real life the entropy of the game can only increase with time. If things aren't on fire yet, just wait.
18. **只是尖端？**
   > EN: Just the tip?
19. **杀光空间站所有人并不好玩，除非真的好玩。**
   > EN: Killing the entire station isn't fun except when it is.
20. **确保你把新鲜的尸体放在纳米传讯品牌的静滞床上！（专利正在申请中）**
   > EN: Make sure you put the fresh corpse on a Nanotrasen-Brand Stasis Bed! (Patent Pending)
21. **大多数物品的名字都比'肥皂'长。**
   > EN: Most items have names longer than "soap".
22. **纳米传讯不想让你知道，你可以通过黑入货舱的自动车床打印锡箔帽。它能保护你免受……外星人，以及……各种心灵魔法的侵害。**
   > EN: Nanotrasen doesn't want you to know, you can print Tinfoil Hats by hacking the Autolathe at cargo. It will protect you from... aliens, and a... plethora of mind-based magic.
23. **偶尔轮次提示可能会骗你。别慌，这很正常。**
   > EN: Occasionally the tip of the round might lie to you. Don't panic, this is normal.
24. **很多传统上不被视为武器的东西仍然可以用来慢慢折磨某人致死，发挥创意！**
   > EN: Plenty of things that aren't traditionally considered weapons can still be used to slowly brutalize someone to death, get creative!
25. **在一个大半内容都基于文字的游戏里，有些人却无法阅读文字。**
   > EN: Some people are unable to read text on a game where half of it is based on text.
26. **有时候一局游戏就是会泡汤。这就是生活。**
   > EN: Sometimes a round will just be a bust. C'est la vie.
27. **有时管理员就是会搞事情。顺其自然吧。**
   > EN: Sometimes admins will just do stuff. Roll with it.
28. **有时候无论你游戏玩得多好，都无法避免死亡。尽量别为此太过焦虑。**
   > EN: Sometimes you won't be able to avoid dying no matter how good you are at the game. Try not to stress too much about it.
29. **太空很冷，如果你不保护自己，它会迅速把你冻死。这并不符合热力学的实际运作方式，但就这样接受吧。**
   > EN: Space is cold and it will quickly freeze you to death if you don't protect yourself. This isn't how thermodynamics really works but just go with it.
30. **那只鸟记得。**
   > EN: The bird remembers.
31. **一个游戏机制越冷门、越不被常用，你的目标就越不太可能应对它。**
   > EN: The more obscure and underused a game mechanic is, the less likely your victims are to be able to deal with it.
32. **军需官只是个部门主管，免得他们自尊心受伤。别告诉他们。**
   > EN: The quartermaster is only a head of staff so their feelings aren’t hurt. Don’t tell them.
33. **重制版永远不会出来。**
   > EN: The remake will never come out.
34. **巫师在一对一战斗中理应极强，别再为此生气了。**
   > EN: The wizard is supposed to be extremely strong in one on one combat, stop getting mad about it.
35. **至少有11种方法可以穿过塑料挡板。你能说出多少种？**
   > EN: There are at least 11 ways to get through plastic flaps. How many can you name?
36. **这个游戏比大多数玩它的人年龄还大。**
   > EN: This game is older than most of the people playing it.
37. **这是一个不断被开发的游戏。预期每天都有内容被添加、移除、修复和破坏。**
   > EN: This is a game that is constantly being developed for. Expect things to be added, removed, fixed, and broken on a daily basis.
38. **要击败屠宰恶魔，向它射击直到它死亡。**
   > EN: To defeat the slaughter demon, shoot at it until it dies.
39. **当局结束后，关于它的一切几乎永远消失，把你的气也一并放下吧。**
   > EN: When a round ends nearly everything about it is lost forever, leave your salt behind with it.
40. **你可以用靴子的后跟划火柴。……什么？摩擦火柴在19世纪就过时了？胡说！**
   > EN: You can light a match with the heel of your boot. ...What's that? Friction matches went out of style in the 19th century? That's nonsense!
41. **你可以从街机上赢得一把脉冲步枪。真的。**
   > EN: You can win a pulse rifle from the arcade machine. Honest.
42. **你的精灵图代表了你的碰撞体，所以那个爆炸头让你更容易被杀。这就是我们为时尚做出的牺牲。**
   > EN: Your sprite represents your hitbox, so that afro makes you easier to kill. The sacrifices we make for style.
43. **如果你呼唤圣诞老人，他会倾听你的祈祷。**
   > EN: Santa Claus will listen to your prayers if you call him.
