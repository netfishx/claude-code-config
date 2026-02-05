# Claude Code 配置

个人 Claude Code 配置备份。

## 目录结构

```
.claude/
├── rules/           # 全局规则文件
├── skills/          # 技能定义
├── plugins/         # 插件配置
├── settings.json    # 权限和插件设置
└── prompting.md     # 提示工程指南
```

## 使用方法

```bash
# 克隆到 ~/.claude
git clone https://github.com/netfishx/claude-code-config.git ~/.claude
```

## 规则说明

| 文件 | 用途 |
|------|------|
| `language.md` | 中文语言偏好 |
| `coding-style.md` | 代码风格（不可变性、文件组织） |
| `git-workflow.md` | Git 工作流程 |
| `testing.md` | 测试要求（80% 覆盖率、TDD） |
| `performance.md` | 性能优化和模型选择 |
| `patterns.md` | 常用设计模式 |
| `hooks.md` | Hooks 系统说明 |
| `agents.md` | Agent 编排指南 |
| `security.md` | 安全检查清单 |
| `css-layout.md` | CSS Zero-Margin 布局规范 |
| `claude-code-tips.md` | Claude Code 使用技巧 |
