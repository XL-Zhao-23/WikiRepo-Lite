---
name: github-repo-wiki-runner
description: 从固定 GitHub 仓库拉取 repo_wiki_manual 工作目录到当前项目根目录，并强制执行其中的总控 Skill 文件 run_repo_wiki_by_joycoder.skill.md 来生成仓库 Wiki。当用户提到 github-repo-wiki-runner、生成 repo wiki、拉取 repo_wiki_manual、运行 run_repo_wiki_by_joycoder、为当前仓库生成 Wiki 文档、或要求从 GitHub clone wiki 工作流到本地并执行时，必须使用本技能，不要走其他流程，不要用 README 替代总控 Skill。
---

# github-repo-wiki-runner

你是 repo_wiki_manual 的 GitHub 拉取与执行助手。

你的任务只有两步：

1. 从用户指定的 GitHub 仓库拉取 repo_wiki_manual 到当前项目根目录。
2. 强制读取并执行 `./repo_wiki_manual/run_repo_wiki_by_joycoder.skill.md`。

不要扩展流程，不要走其他路径，不要用 README 替代总控 Skill。

## 固定参数

GitHub 仓库地址由用户在调用时提供，例如：

```bash
REPO_WIKI_GITHUB_URL="https://github.com/XL-Zhao-23/RepoWiki-workflow.git"
```

默认分支：

```bash
main
```

目标目录固定为：

```bash
./repo_wiki_manual
```

输出目录由总控 Skill 决定。

如果用户未提供 `REPO_WIKI_GITHUB_URL`，必须先向用户索取，再继续。不要自行编造地址。

## 执行规则

必须严格按照以下路径执行。

### 第一步：进入当前项目根目录

以 JoyCode 当前打开的项目根目录作为工作目录。

先确认当前位置：

```bash
pwd
```

后续所有命令都必须在当前项目根目录执行。

### 第二步：删除旧的 repo_wiki_manual

如果当前项目中已经存在 `./repo_wiki_manual`，必须先删除：

```bash
rm -rf ./repo_wiki_manual
```

不要询问是否覆盖，不要保留旧目录，不要合并旧内容。

### 第三步：从 GitHub 拉取最新内容

执行：

```bash
git clone --depth=1 --branch main "$REPO_WIKI_GITHUB_URL" ./repo_wiki_manual
```

如果用户指定了其他分支，则执行：

```bash
git clone --depth=1 --branch "$REPO_WIKI_BRANCH" "$REPO_WIKI_GITHUB_URL" ./repo_wiki_manual
```

clone 失败时立即停止，不要继续执行。失败时只需提示用户检查：

1. GitHub 地址是否正确；
2. 分支是否存在；
3. 本机 git 是否可用；
4. 如果是私有仓库，是否已配置 GitHub 访问权限。

### 第四步：强制检查总控 Skill

clone 完成后，必须检查：

```bash
test -f ./repo_wiki_manual/run_repo_wiki_by_joycoder.skill.md
```

如果该文件不存在，立即停止。不要自己推断流程，不要读取其他 README 替代，不要自行生成 Wiki。

### 第五步：强制执行总控 Skill

完整读取：

```bash
./repo_wiki_manual/run_repo_wiki_by_joycoder.skill.md
```

然后严格按照该文件执行。

执行过程中必须遵守：

1. 后续所有路径都以当前项目根目录为基准。
2. 只能读取当前项目根目录内的文件。
3. 每一步需要什么文件，就重新读取对应文件。
4. 不依赖历史对话上下文。
5. 不编造不存在的模块、接口、类、函数或业务逻辑。
6. 最终结果必须按照总控 Skill 的要求保存到指定输出目录。

### 第六步：最终汇报

执行完成后，只汇报以下内容：

1. GitHub 仓库是否 clone 成功；
2. 是否找到 `run_repo_wiki_by_joycoder.skill.md`；
3. 是否已按总控 Skill 执行；
4. 生成结果保存在哪里；
5. 如果失败，失败在哪一步。

不要输出冗长解释，不要重复粘贴生成的 Wiki 正文。
