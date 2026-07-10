# 个人 Skills 仓库设计

## 目标

将当前仓库建设成可长期维护的个人 Skills 源码库，同时满足三类需求：

- 隔离个人创建的 Skill 与第三方开源 Skill。
- 追踪第三方 Skill 的来源、版本和许可证。
- 提供轻量的创建、校验和本地安装能力。

仓库采用轻量治理方式，不引入 Git submodule 或复杂的包管理系统。

## 目录结构

```text
agent-skills-yh/
├── README.md
├── mine/
│   └── .gitkeep
├── open-source/
│   └── .gitkeep
├── templates/
│   └── skill/
│       └── SKILL.md
├── docs/
│   ├── open-source-recommendations.md
│   ├── personal-skill-ideas.md
│   └── superpowers/specs/
├── scripts/
│   ├── new-skill.sh
│   ├── install-skills.sh
│   └── validate-skills.sh
├── catalog.yaml
├── CONTRIBUTING.md
└── .gitignore
```

现有 `openSource/` 重命名为 `open-source/`。Skill 目录统一使用小写 kebab-case。

## 核心文件

### catalog.yaml

作为仓库级索引，记录每个 Skill 的名称、目录、归属、来源、版本、许可证和维护状态。Skill 名在整个仓库内全局唯一。

### Skill 模板

模板包含合法的 YAML frontmatter 和精简的正文骨架。每个 Skill 以 `SKILL.md` 为唯一必需入口；详细资料、可执行逻辑和输出资源按需放入 `references/`、`scripts/` 和 `assets/`。

### new-skill.sh

接收 Skill 名称，在 `mine/` 中创建新目录并从模板生成 `SKILL.md`。脚本拒绝非法名称和已存在的名称。

### validate-skills.sh

执行以下确定性检查：

- Skill 目录名符合小写 kebab-case。
- 每个 Skill 存在 `SKILL.md`。
- frontmatter 包含 `name` 和 `description`。
- frontmatter 中的名称与目录名一致。
- `mine/` 与 `open-source/` 之间不存在重名。
- `catalog.yaml` 中声明的本地路径存在。

脚本在发现错误时返回非零退出码，并输出具体文件和原因。

### install-skills.sh

将 Skill 软链接到 `${CODEX_HOME:-$HOME/.codex}/skills`。支持安装全部 Skill 或按名称安装；遇到非本仓库管理的同名目标时拒绝覆盖。

## 内容指南

`docs/open-source-recommendations.md` 收录经过联网核实的开源 Skill 来源，说明用途、地址和引入注意事项，不自动下载或复制第三方代码。

`docs/personal-skill-ideas.md` 按科研、实习与工作、软件开发、知识管理分类，列出具有复用价值的个人 Skill 选题、触发场景和建议优先级。

## 安全和边界

- 第三方 Skill 保留原始许可证和署名，不在 `open-source/` 中直接做个人定制。
- 定制版本复制到 `mine/` 并使用新的唯一名称。
- 安装脚本不覆盖未知文件或目录。
- 仓库不保存密钥、Cookie、Token 或内部系统凭据。
- `.DS_Store` 加入忽略规则，但不修改或删除用户当前的本地文件。

## 验证

实现完成后进行以下验证：

1. 运行仓库校验脚本并确认成功。
2. 在临时目录中测试创建合法 Skill、拒绝非法名称和拒绝重名。
3. 使用临时 `CODEX_HOME` 测试安装全部及指定 Skill。
4. 检查安装脚本不会覆盖未知同名目标。
5. 检查所有 shell 脚本的语法。

