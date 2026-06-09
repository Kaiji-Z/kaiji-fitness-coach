# kaiji-fitness-coach

基于 [free-exercise-db](https://gitee.com/kaiji1126/free-exercise-db)（800+ 健身动作）的 AI 健身教练知识体系。

## 安装

1. 安装数据库：`python scripts/setup_db.py`
2. 验证：`python scripts/query_exercises.py --check-db`

## 子 Skill

| 子 Skill | 用途 |
|----------|------|
| onboarding | 新用户画像收集 |
| plan-generator | 训练计划生成 |
| exercise-teaching | 动作教学与示范 |
| data-analyst | 训练数据分析 |
| progression | 进阶与周期化 |
| nutrition-advisor | 营养指导 |

## 目录结构

```
kaiji-fitness-coach/
├── SKILL.md              # 主 Skill
├── skills/               # 子 Skill
├── references/           # 参考资料
├── assets/               # JSON 模板
├── scripts/              # 数据库脚本
├── free-exercise-db/     # 数据库（不提交）
└── .gitignore
```

## License

MIT
