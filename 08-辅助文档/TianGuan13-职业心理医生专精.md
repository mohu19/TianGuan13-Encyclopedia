# 天关 — 心理医生专精百科

**代码**: `code/modules/jobs/job_types/psychologist.dm` + `code/modules/library/skill_learning/job_skillchips/psychologist.dm` + `code/modules/power/supermatter/supermatter_extra_effects.dm` + `code/game/objects/items/storage/pillbottles.dm`

---

## 一、基础信息

| 项目 | 内容 |
|---|---|
| 岗位数量 | 1总/1出生 |
| 上级 | 人事部长 + 首席医疗官 |
| 部门 | 服务部 |
| 工资等级 | CREW |
| 发薪部门 | 服务部账目 |
| 经验类型 | 船员经验 |
| 家族遗物 | 药瓶 |

---

## 二、技能芯片 (Skillchip)

**代码**: `code/modules/library/skill_learning/job_skillchips/psychologist.dm`

```
/obj/item/skillchip/job/psychology
  name = "HYPERG1G4 skillchip"
  desc = "Learn to bend the abyss to your will."

  auto_traits = list(
    TRAIT_SUPERMATTER_SOOTHER,   // SM心理治疗 — 站在SM旁可降低发热+抑衰减
    TRAIT_MADNESS_IMMUNE,        // 疯狂免疫 — 不受SM幻觉影响
  )
```

心理医生自带**技能芯片植入**（slot = skillchips），出生即激活。

### 2.1 TRAIT_SUPERMATTER_SOOTHER — SM心理治疗

**代码**: `supermatter_extra_effects.dm` L76-90

```dm
psychological_examination():
    for each human in SM视野范围:
        if HAS_MIND_TRAIT(seen_by_sm, TRAIT_SUPERMATTER_SOOTHER):
            psy_coeff_diff = 0.05   // 有心理医生→ 每tick +0.05
        else:
            psy_coeff_diff = -0.05  // 无心理医生→ 每tick -0.05

    psy_coeff = clamp(psy_coeff + psy_coeff_diff, 0, 1)
```

**效果** (psy_coeff 0~1):
| 受影响项 | 效果 |
|---|---|
| **温度极限** | `temp_limit += psy_coeff × 45` (最多+45K耐热) |
| **废热倍率** | `waste_multiplier -= 0.2 × psy_coeff` (最多-20%废气) |
| **能量衰减** | `powerloss -= 0.2 × psy_coeff × 衰减量` (最多-20%衰减) |

**直观理解**: 心理医生站在SM附近→SM更稳定→更耐热→产更少废热→能量衰减更慢

### 2.2 TRAIT_MADNESS_IMMUNE — 疯狂免疫

**代码**: `supermatter.dm` L259

```dm
/examine:
    if HAS_MIND_TRAIT(user, TRAIT_MADNESS_IMMUNE):
        // 不显示疯狂文本
    else:
        // 显示"你感到一股不可名状的恐惧..."
```

心理医生**不会被SM的幻觉影响**——接近SM不会产生幻觉, 检查SM不会受到精神伤害。

---

## 三、初始药品

心理医生出生携带**5瓶专用药**, 分别对应5种常见的心理健康问题:

### 3.1 Mannitol (甘露醇) — 脑损伤药

| 项目 | 内容 |
|---|---|
| 对应 | **情绪低落/脑损伤** |
| 治疗 | 脑部物理损伤 + 脑腐烂病 |
| 备注 | 属于常见脑药 |

### 3.2 Happiness (快乐药)

| 项目 | 内容 |
|---|---|
| 对应 | **抑郁特质 (Depression)** |
| 效果 | 强行提升情绪 |
| 备注 | 官方名字"happiness pills" |

### 3.3 LSD (致幻剂)

| 项目 | 内容 |
|---|---|
| 对应 | **精神错乱特质 (Insanity)** |
| 效果 | 致幻体验 (以毒攻毒) |
| 备注 | 带有隐喻: 用受控幻觉对抗精神错乱 |

### 3.4 Pax (和平药)

| 项目 | 内容 |
|---|---|
| 对应 | **暴力倾向/敌对行为** |
| 效果 | 镇静/强制非暴力 |
| 备注 | Pax = 拉丁语"和平" |

### 3.5 Psicodine (精神安定)

| 项目 | 内容 |
|---|---|
| 对应 | **社交焦虑/恐惧症/幻觉症状** |
| 治疗 | 治愈导致幻觉的疾病症状 |
| 备注 | 最通用的精神疾病药 |

### 邮件礼物

| 物品 | 权重 | 说明 |
|---|---|---|
| Mannitol药瓶 | 30 | 最常见 |
| Happy药瓶 | 5 | 稀有 |
| 注射枪 | 1 | 极稀有 — 可以远程注射 |

---

## 四、装备详情

**代码**: `/datum/outfit/job/psychologist`

| 部位 | 物品 |
|---|---|
| ID | `/obj/item/card/id/advanced` — 高级ID卡 |
| ID权限 | `/datum/id_trim/job/psychologist` — 医疗+服务权限 |
| 制服 | 衬衫+西裤 |
| 腰带 | 心理医生PDA |
| 耳机 | 服务+医疗双频道耳机 (`headset_srvmed`) |
| 鞋子 | 系带皮鞋 |
| 左手 | 写字板 |
| 脖子 | 黑色领带 |
| 背包 | 医用背包 |

### 背包内容 (5瓶药)
```
/obj/item/storage/pill_bottle/happinesspsych  — 快乐药
/obj/item/storage/pill_bottle/lsdpsych        — LSD 
/obj/item/storage/pill_bottle/mannitol         — 甘露醇
/obj/item/storage/pill_bottle/paxpsych         — 和平药
/obj/item/storage/pill_bottle/psicodine        — 精神安定
```

---

## 五、肝特质

**代码**: `psychologist.dm` L18

```dm
liver_traits = list(TRAIT_MEDICAL_METABOLISM)
```

`TRAIT_MEDICAL_METABOLISM` — **药物代谢优化**: 摄入的药物代谢速度更快, 减少药物在体内的副作用时间。

---

## 六、Nova 扩展

**代码**: `modular_nova/.../psychologist.dm`

Nova版本给心理医生增加了 `messenger = /obj/item/storage/backpack/messenger/med`（医用斜挎包款式）。

---

## 七、一句话总结

**心理医生 = SM稳压器 + 疯狂免疫 + 5种精神药物 + 服务医疗双频道 + 医疗代谢**

不只是来看心理的——在工程部SM旁边站岗就是为全站做贡献。
