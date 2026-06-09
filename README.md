# kaiji-fitness-coach

基于 [free-exercise-db](https://gitee.com/kaiji1126/free-exercise-db)（800+ 动作）的 AI 健身教练知识体系。

覆盖训练计划、动作教学、数据分析、营养指导、进阶周期化全流程。

## 安装

1. 安装数据库：`python scripts/setup_db.py`
2. 验证：`python scripts/query_exercises.py --check-db`

## 目录结构

```
kaiji-fitness-coach/
├── SKILL.md              # 主 Skill（操作规范 + Reference 路由）
├── references/           # 按需加载的参考资料
│   ├── onboarding.md     # 用户画像收集
│   ├── plan-generator.md # 训练计划生成
│   ├── exercise-teaching.md # 动作教学
│   ├── data-analyst.md   # 训练数据分析
│   ├── progression.md    # 进阶与周期化
│   ├── nutrition-advisor.md # 营养指导
│   ├── workout-timer-integration.md # App 数据对接
│   ├── plan-design-principles.md # 计划设计原则
│   ├── muscle-reference.md # 肌群对照与 MEV 基准
│   └── exercise-db-schema.md # 数据库字段文档
├── assets/               # JSON 模板
├── scripts/              # 数据库脚本
├── free-exercise-db/     # 数据库（不提交）
└── .gitignore
```

## License

MIT
