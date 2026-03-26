<p align="center">
  <h1 align="center">🔥 AI 健身私教 (Kaiji Fitness Coach)</h1>
  <p align="center">
    <strong>全流程 AI 健身私教技能</strong><br>
    新用户信息收集 → 个性化训练计划 → 训练进化调整 → 动作教学指导
  </p>
  <p align="center">
    <a href="https://clawhub.ai"><img src="https://img.shields.io/badge/ClawHub-Published-blue" alt="ClawHub"></a>
    <a href="https://github.com/Kaiji-Z/kaiji-fitness-coach/releases"><img src="https://img.shields.io/badge/version-1.0.0-green" alt="Version"></a>
    <a href="https://github.com/Kaiji-Z/kaiji-fitness-coach/blob/main/LICENSE"><img src="https://img.shields.io/badge/license-MIT-orange" alt="License"></a>
  </p>
</p>

---

## ✨ 特性

| 特性 | 说明 |
|------|------|
| 🎯 **智能用户画像** | 对话式收集健身经验、目标、器械、限制条件，不填表不打断 |
| 📋 **个性化计划生成** | 基于 800+ 动作数据库，根据用户情况自动生成训练计划 |
| 🔄 **训练进化系统** | 渐进超负荷、周期化、弱点强化，持续迭代优化 |
| 📸 **动作教学** | 详细步骤说明 + 示范图片，每个动作都教到位 |
| 📊 **多格式输出** | Markdown 人可读 + JSON 格式，支持导入训练 App |
| 🌐 **跨平台兼容** | Windows / Linux / macOS 全支持 |

## 🚀 快速开始

### 安装

```bash
# 通过 ClawHub 安装（推荐）
npx clawhub@latest install kaiji-fitness-coach

# 或从 GitHub 克隆
git clone https://github.com/Kaiji-Z/kaiji-fitness-coach.git
```

### 初始化数据库

首次使用需要下载动作数据库：

```bash
cd kaiji-fitness-coach
python scripts/setup_db.py
```

### 验证安装

```bash
# 检查数据库状态
python scripts/query_exercises.py --check-db

# 测试查询：找胸部哑铃动作
python scripts/query_exercises.py --muscle chest --equipment dumbbell
```

## 💡 使用方式

将 skill 安装到你的 [OpenClaw](https://openclaw.ai) agent 后，直接用自然语言对话即可：

```
你：我想开始健身，家里只有一对哑铃和一个上斜凳
教练：没问题 Bro！先了解一下你的情况...
```

```
你：给我设计一个增肌计划，每周练3天
教练：收到！根据你的情况，推荐 PPL 推拉腿计划...
```

```
你：上斜哑铃卧推怎么做？
教练：来，教你！[动作步骤 + 示范图片]
```

## 📁 项目结构

```
kaiji-fitness-coach/
├── SKILL.md                          # 技能主文件（Agent 读取入口）
├── README.md                         # 你正在看的文件
├── scripts/
│   ├── setup_db.py                   # 数据库下载与初始化
│   └── query_exercises.py            # 动作查询工具
├── references/
│   ├── user-onboarding.md            # 用户信息收集流程
│   ├── plan-design.md                # 训练计划设计参考
│   ├── progression.md                # 训练进阶策略
│   └── exercise-db-schema.md         # 数据库字段说明
├── assets/
│   └── plan-template.json            # JSON 计划模板
└── free-exercise-db/                 # 动作数据库（setup 后生成）
```

## 🏋️ 核心工作流

```
新用户 → 信息收集 → 生成计划 → 执行训练 → 进化调整
   ↑                                          ↓
   └──────────── 周期化训练循环 ←──────────────┘
```

### 第一阶段：用户信息收集
对话式收集，2-3个问题一轮，不填表不打断。优先收集核心信息（经验、目标、器械），即可开始。

### 第二阶段：计划生成
根据用户情况自动选择训练模式：

| 用户类型 | 推荐模式 | 频率 |
|----------|----------|------|
| 新手 | 全身训练 | 3天/周 |
| 进阶 | PPL（推拉腿）| 3-6天/周 |
| 时间少 | 上/下半身分化 | 4天/周 |

### 第三阶段：动作教学
从数据库调取专业资料，翻译为中文，配合示范图片讲解。

### 第四阶段：导出到 Workout Timer App 📱

训练计划支持 JSON 格式输出，可直接导入 [**Workout Timer**](https://github.com/Kaiji-Z/workout-timer) App：

```bash
# 1. 让教练生成 JSON 格式计划
"帮我生成一个 PPL 计划，要 JSON 格式的"

# 2. 在 Workout Timer App 中导入 JSON 文件
# 3. 开始训练！App 自动计时、记录、统计
```

**完整的训练闭环：**
```
AI 教练生成计划 → 导入 Workout Timer → 训练计时记录
        ↑                                    ↓
        ← 导出训练数据 → AI 教练分析优化 ←←←
```

> 💡 **Workout Timer** 是一款开源健身计时器 App，内置 870+ 动作库、训练计划管理、记录统计等功能。
> 数据源与本技能相同（free-exercise-db），配合使用体验拉满！

### 第五阶段：训练进化
- 连续 2 周完成目标次数 → 加重量
- 进入平台期 → 调整计划
- 弱点肌群 → 额外强化

## 🛠️ 技术栈

- **Agent 框架**: [OpenClaw](https://openclaw.ai) — AI Agent 运行时
- **技能分发**: [ClawHub](https://clawhub.ai) — 版本化技能仓库
- **动作数据库**: [free-exercise-db](https://gitee.com/kaiji1126/free-exercise-db) — 800+ 健身动作
- **查询工具**: Python 3.6+ 脚本，零依赖

## 🙏 致谢

本项目离不开以下优秀的开源项目：

- [**free-exercise-db**](https://gitee.com/kaiji1126/free-exercise-db) — 本项目核心数据来源，800+ 健身动作数据库，包含详细动作说明、肌群分类、器械需求和示范图片。开源健身数据的天花板。
- [**OpenClaw**](https://github.com/openclaw/openclaw) — 强大的 AI Agent 框架，让 AI 助手拥有技能系统和工具调用能力。本技能的运行时基础。
- [**ClawHub**](https://clawhub.ai) — Agent 技能分发平台，支持版本管理、向量搜索、一键安装。

特别感谢所有为开源健身生态做出贡献的开发者们！💪

## 📄 License

[MIT](LICENSE) — 自由使用，欢迎 Fork 和 PR！

---

<p align="center">
  <sub>Built with ❤️ and 💪 by <a href="https://github.com/Kaiji-Z">Kaiji-Z</a></sub>
</p>
