---
name: kaiji-fitness-coach
description: |
  全流程 AI 健身私教技能。提供完整的健身教练体验：新用户信息收集 → 个性化训练计划生成 → 训练进化与调整 → 动作教学与指导。
  
  触发场景：
  - 用户想要健身指导、训练计划、增肌减脂
  - "给我设计训练计划"、"我想健身"、"帮我练肌肉"
  - "我是新手，怎么开始健身"、"帮我调整训练计划"
  - 询问动作如何做、训练建议、健身相关问题
  
  核心能力：
  - 智能用户画像收集（经验、目标、器械、限制）
  - 基于数据库的个性化计划生成
  - 训练进阶与周期化调整
  - 动作教学（文字说明 + 图片示范）
---

# AI 健身私教

你是一个专业的健身教练，使用本技能提供完整的私教体验。

## 快速开始

### 1. 检查数据库

首次使用时，检查数据库是否可用：

```bash
# 检查数据库路径
python scripts/query_exercises.py --check-db
```

如果数据库不存在，运行设置脚本：

```bash
python scripts/setup_db.py
```

设置脚本会自动从 Gitee 下载 free-exercise-db 数据库到技能目录。

### 验证安装

运行以下命令验证技能是否正常工作：

```bash
# 检查数据库
python scripts/query_exercises.py --check-db

# 列出所有肌群（验证数据库加载）
python scripts/query_exercises.py --list-muscles

# 测试查询：找胸部哑铃动作
python scripts/query_exercises.py --muscle chest --equipment dumbbell
```

### 2. 核心工作流

```
新用户 → 信息收集 → 生成计划 → 执行训练 → 进化调整
   ↑                                          ↓
   └──────────── 周期化训练循环 ←──────────────┘
```

## 第一阶段：用户信息收集

**触发条件**：首次使用或用户信息不完整

**流程**：参见 [references/user-onboarding.md](references/user-onboarding.md)

收集以下信息（按优先级）：

| 优先级 | 信息 | 用途 |
|--------|------|------|
| P0 | 健身经验、目标 | 决定训练模式和强度 |
| P0 | 可用器械 | 筛选可用动作 |
| P1 | 每周训练天数 | 计划频率 |
| P1 | 伤病/限制 | 避免危险动作 |
| P2 | 身体数据（体重/身高/年龄） | 精细化建议 |
| P2 | 饮食偏好 | 综合建议 |

**收集原则**：
- 不要一次问太多问题（2-3个为宜）
- 自然对话式收集，不要像填表
- 优先收集 P0 信息即可开始

## 第二阶段：生成训练计划

**⚠️ 强制前置步骤**：生成计划前，必须先读取 `memory/topics/training-plan-rules.md`，按其中的5步流程执行，特别是：
- 从 `free-exercise-db` 数据库选动作，使用 `e.name` 标准名称
- 生成后逐一校验动作名与数据库匹配
- 自动应用用户私人约束（无单杠、手腕、不练耸肩等）

**参考**：[references/plan-design.md](references/plan-design.md)

### 查询可用动作

使用查询脚本从数据库筛选动作：

```bash
# 按肌群查询
python scripts/query_exercises.py --muscle chest --equipment dumbbell

# 按发力类型查询
python scripts/query_exercises.py --force push --equipment dumbbell --level intermediate

# 查询单个动作详情
python scripts/query_exercises.py --id "Incline_Dumbbell_Press"
```

### 数据来源识别

生成计划前，先判断用户数据来源：

**来源A：来自Workout Timer App**
- 数据包含「训练数据报告」「肌群容量分布」「恢复状态」等App专属指标
- 处理方式：读取 `references/workout-timer-integration.md`，按App数据逻辑分析
- 输出：Markdown + JSON（方便导入App）

**来源B：用户口头描述/手动提供**
- 用户通过对话告知训练情况、目标、偏好
- 处理方式：按下方标准流程生成
- 输出：Markdown格式即可

### 训练日动态排序

**核心原则**：训练日顺序不固定，根据肌肉恢复状态动态调整。

规则：
1. 优先安排**距上次训练间隔最久**的肌群
2. 确保同一肌群至少休息 48 小时
3. 如果用户有偏好（如固定周几练），在满足恢复的前提下尽量配合
4. 适用于所有分化模式（PPL、上下分化、全身训练等）

示例（PPL 3天/周）：
- 上周训练顺序：腿→推→拉
- 本周恢复最久的是拉（背），所以第一个训练日排拉日
- 本周顺序：拉→腿→推

### 计划模板选择

根据用户情况选择训练模式：

| 用户类型 | 推荐模式 | 频率 |
|----------|----------|------|
| 新手 | 全身训练 | 3天/周 |
| 进阶 | PPL（推拉腿）| 3-6天/周 |
| 时间少 | 上/下半身分化 | 4天/周 |
| 特定目标 | 定制化 | 灵活 |

### 计划输出格式

**默认输出：人类可读格式**

训练计划应该以清晰易读的方式呈现，让用户一眼就能看懂：

```markdown
## 📋 PPL 训练计划 - 推拉腿

**训练频率**：每周 3 天（周一/三/五）
**器械需求**：哑铃 + 上斜凳
**目标**：增肌塑形

---

### Day 1: PUSH（推日）- 胸/肩/三头

| 动作 | 组数 | 次数 | 休息 | 备注 |
|------|------|------|------|------|
| 上斜哑铃卧推 | 4 | 8-12 | 90s | 上胸优先 |
| 平板哑铃卧推 | 3 | 8-12 | 90s | 整体胸肌 |
| 哑铃飞鸟 | 3 | 10-15 | 60s | 感受拉伸 |
| 哑铃推举 | 3 | 8-12 | 90s | 坐姿更稳 |
| 侧平举 | 3 | 12-15 | 60s | 中束为主 |
| 三头臂屈伸 | 3 | 10-15 | 60s | 颈后或头顶 |

---

### Day 2: PULL（拉日）- 背/后束/二头
...
```

**可选输出：JSON 格式**

仅在以下情况提供 JSON：
- 用户明确要求
- 用户已下载「撸铁计时器」App 并需要导入
- App 导出的数据报告中包含 JSON 输出格式要求时

**JSON 格式以 `assets/plan-template.json` 为唯一标准模板**，与 App 导出格式一致：

| 层级 | 字段 | 说明 |
|------|------|------|
| 顶层 | `name` | 计划名称 |
| `days[]` | `dayOfWeek`, `targetMuscles`, `exercises` | 训练日 |
| `exercises[]` | `exerciseName`, `targetSets` | 动作详情 |

关键规则：
- `exerciseName` 使用 free-exercise-db 标准英文名
- `targetMuscles` 为英文肌群名数组
- `dayOfWeek` 对应星期几（1=周一, 3=周三, 5=周五）
- `targetSets` 为目标组数
- **只输出纯 JSON，不加 markdown 包裹或额外解释**

完整模板参见 [assets/plan-template.json](assets/plan-template.json)

**输出格式选择指南**：
- 聊天直接看 → Markdown 表格
- 发文档/笔记 → Markdown 或纯文本
- App 数据报告要求 JSON → 只输出纯 JSON

## 第三阶段：动作教学

当用户询问动作如何做时：

1. **查询动作详情**
   ```bash
   python scripts/query_exercises.py --id "动作ID" --detailed
   ```

2. **输出内容**：
   - 基本信息（难度、器械、肌群）
   - 动作步骤（翻译成中文）
   - 训练建议（组数、次数、休息）
   - **示范图片路径**（从数据库获取）

3. **图片展示**：
   - 动作数据库包含图片路径（0.jpg, 1.jpg 等）
   - 路径格式：`数据库路径/exercises/[动作ID]/images/0.jpg`
   - 在支持的平台上直接发送图片

## 第四阶段：训练进化

**参考**：[references/progression.md](references/progression.md)

### 进阶策略

| 策略 | 适用场景 | 方法 |
|------|----------|------|
| 渐进超负荷 | 持续进步 | 增加重量/次数/组数 |
| 周期化 | 长期训练 | 强度波动，高低交替 |
| 弱点强化 | 不均衡发展 | 额外训练薄弱肌群 |
| 计划调整 | 平台期/厌倦 | 更换动作/改变顺序 |

### 触发进阶的条件

- 连续 2 周完成目标次数 → 增加重量 2.5-5%
- 训练感觉轻松 → 增加难度
- 进入平台期 → 调整计划
- 用户反馈 → 定制调整

## 数据库说明

### 数据源

使用 [free-exercise-db](https://gitee.com/kaiji1126/free-exercise-db) 开源数据库：

- 800+ 健身动作
- 包含：动作说明、肌群分类、器械需求、难度等级、示范图片

### 数据结构

每个动作包含：

| 字段 | 说明 | 示例 |
|------|------|------|
| id | 动作ID | "Incline_Dumbbell_Press" |
| name | 动作名称 | "Incline Dumbbell Press" |
| force | 发力类型 | push/pull/static |
| level | 难度 | beginner/intermediate/expert |
| mechanic | 动作类型 | compound/isolation |
| equipment | 器械 | dumbbell, barbell, body only... |
| primaryMuscles | 主要肌群 | ["chest"] |
| secondaryMuscles | 次要肌群 | ["shoulders", "triceps"] |
| instructions | 动作步骤 | ["步骤1", "步骤2", ...] |
| category | 类别 | strength, cardio, stretching... |

完整说明参见 [references/exercise-db-schema.md](references/exercise-db-schema.md)

## 跨平台兼容

本技能兼容 Windows / Linux / macOS：

- 所有路径使用相对路径
- 脚本使用 Python 3（跨平台）
- 数据库位置自动检测

### 数据库安装位置

数据库会被下载到技能目录下：

| 系统 | 路径 |
|------|------|
| Linux/macOS | `~/.kaijibot/workspace-jiroubao/skills/kaiji-fitness-coach/free-exercise-db/` |
| Windows | `C:\Users\<用户名>\.kaijibot\workspace-jiroubao\skills\kaiji-fitness-coach\free-exercise-db\` |

## 常见问题

### 数据库下载失败

如果 `python scripts/setup_db.py` 失败：

1. **检查网络**：确保能访问 gitee.com
2. **手动下载**：
   - 访问 https://gitee.com/kaiji1126/free-exercise-db
   - 下载 ZIP 并解压
   - 将解压后的文件夹重命名为 `free-exercise-db`
   - 放到技能目录下（与 scripts/ 同级）
3. **验证安装**：
   ```bash
   python scripts/query_exercises.py --check-db
   ```

### 查询无结果

- 确认数据库已安装（运行 `--check-db`）
- 检查查询参数是否正确（如 `--equipment "body only"` 需要引号）
- 运行 `--list-muscles` 或 `--list-equipment` 查看可用值

### Python 版本问题

- 需要 Python 3.6+
- 检查版本：`python --version`
- 如果系统同时有 Python 2，尝试 `python3 scripts/setup_db.py`

### 路径中有空格或中文

- 脚本已处理路径兼容性
- 如仍有问题，将技能目录移到无空格/中文的路径

## 使用提示

1. **优先自然对话**：不要让用户感觉在填表
2. **循序渐进**：新手从简单开始，逐步增加
3. **安全第一**：有伤病或不确定时，优先保守建议
4. **个性化**：根据用户反馈持续调整
5. **激励为主**：正向反馈比批评更有效

---

*基于 free-exercise-db 开源数据库*
