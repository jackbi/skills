# CloudSIS 前端 Agent Skills

为 CloudSIS 项目（Vue 3 + TypeScript + Element Plus + 麦杰 Framework 5.x）提供的 AI 编码代理技能包，配合 `create-maguscloud-app` 的 sis 脚手架使用。

## 包含技能

| 技能 | 说明 |
|------|------|
| [cloudsis-components](./skills/cloudsis-components/SKILL.md) | SIS 项目前端开发规范、组件案例、框架 API 参考 |

## 安装

```bash
pnpx skills add jackbi/skills
```

## 手动安装

如果不使用 `skills` CLI，也可以手动将 `skills/cloudsis-components` 文件夹复制到对应 AI 工具的 skills 目录下：

| AI 工具 | 目标路径 |
|---------|---------|
| VSCode Copilot | `.github/skills/` |
| CodeBuddy | `.claude/skills/` |
| Trae | `.trae/skills/` |

## Agent 对话示范

```
根据 http://192.168.20.30:15000/doc.html 的接口规范和 cloudsis 项目规范，生成通知规则查询页面
```
