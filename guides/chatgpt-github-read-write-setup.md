# ChatGPT ↔ GitHub 读写连接配置

> 目的：让 ChatGPT 不只是读取 GitHub repository，还能直接创建、修改和提交仓库文件。
>
> 本文来自 2026-08-18 对 `pandakeeper0/ai-systems-atlas` 的一次真实排障与验证。

## 最终结论

ChatGPT 侧“连接 GitHub”并不一定意味着 GitHub App 已经真正安装到 GitHub account/repository。

这次遇到的关键状态是：ChatGPT 已能读取 repository；ChatGPT 的 GitHub App 权限设置已经是 **Allow all actions**；GitHub 的 **Authorized GitHub Apps** 中能看到 `ChatGPT Codex Connector`；但 GitHub 的 **Installed GitHub Apps** 是空的。因此 ChatGPT 调用 GitHub Contents 写 API 时返回 `403 Resource not accessible by integration`。

真正修复动作是：**把 `ChatGPT Codex Connector` 安装到 GitHub account，并允许它访问目标 repository。** 安装完成后，再次由 ChatGPT 创建 `connection-test.md`，立即成功。

## 推荐配置流程

### 1. 在 ChatGPT 中连接 GitHub

在 ChatGPT 的 Apps / Connectors 设置中连接 GitHub，并完成 GitHub 身份授权。如果希望 ChatGPT 在明确指令下直接维护仓库，需要允许相应 write actions。

> ChatGPT 侧允许 action，并不能替代 GitHub 自己的 App installation / repository access。

### 2. 检查 Authorized GitHub Apps

打开 `https://github.com/settings/applications`，确认能看到 OpenAI 的 `ChatGPT Codex Connector`。

Authorized 表示你已经允许该应用代表你的 GitHub 身份工作，但**这一步本身不代表 App 已安装到 repository**。

### 3. 检查 Installed GitHub Apps

打开 `https://github.com/settings/installations`。

如果这里没有 `ChatGPT Codex Connector`，即使前一步 Authorized 已成功，也可能出现“能读、写操作 403”的状态。

### 4. 安装 ChatGPT Codex Connector

打开 GitHub App 安装入口：`https://github.com/apps/chatgpt-codex-connector/installations/new`

选择自己的 GitHub account。Repository access 推荐遵循最小权限原则：只需要维护一个知识库时选择 **Only select repositories**，然后选择目标 repository（例如 `ai-systems-atlas`），完成 Install / Save / Authorize。

### 5. 用真实写操作验证

最可靠的验证不是看 Connected 或权限标签，而是让 ChatGPT 执行一次最小写操作：

```text
在 <owner>/<repo> 的 main 分支创建 connection-test.md，写入一行测试内容；成功后再读取该文件确认。
```

验证标准：create file 成功并产生 commit SHA；再从 GitHub 读取该文件成功；repository 页面能看到对应 commit。

## 典型故障：403 Resource not accessible by integration

如果读取正常，但创建/修改文件返回 `403 Resource not accessible by integration`，优先检查：

1. **Installed GitHub Apps 是否为空** —— 本次实际故障就是这一项；
2. `ChatGPT Codex Connector` 是否安装到了正确的 GitHub account；
3. Repository access 是否包含目标 repository；
4. ChatGPT 侧 GitHub App 是否允许 write actions；
5. 重新执行一个最小 `create_file` 测试。

不要仅因为 repository metadata 显示当前用户有 `push` / `admin` 权限，就认为 integration 一定可以写。**用户对 repository 的权限和 GitHub App installation 是不同层的授权。**

## 本次排障记录

最初状态：repository `pandakeeper0/ai-systems-atlas`，默认分支 `main`；当前账号有 `push` / `admin`；ChatGPT GitHub App 为 `Allow all actions`；读取成功，但创建 `connection-test.md` 返回 403。

随后在 GitHub 中发现：`Authorized GitHub Apps` 存在 `ChatGPT Codex Connector`，但 `Installed GitHub Apps` **为空**，Connector 页面也明确显示尚未 installed on any accounts。

修复方法：从 GitHub App 安装入口安装 `ChatGPT Codex Connector`，并给予 `ai-systems-atlas` repository access。

安装后重新执行同一个文件创建动作，`connection-test.md` 创建成功，commit SHA 为 `c3ca5c65421cd02e73955f540f0248fc35b692ee`，随后 ChatGPT 继续直接向 `main` 写入知识库初始化文件。

这证明本次根因不是 repository collaborator 权限，也不是必须改用桌面 Codex，而是 **GitHub App 只 Authorized、没有 Installed 到 account/repository**。

## 心智模型

```text
ChatGPT action permission
        ↓
GitHub identity authorization
        ↓
GitHub App installation
        ↓
Repository access scope
        ↓
Actual GitHub API write
```

前面的状态正常，并不能证明后面一定正常。最终一定用一次真实的 create/update + commit 来验证。

## 安全建议

对于个人知识库，推荐只给 GitHub App 开放需要 AI 维护的 repository，不默认开放所有私人代码仓库。重要代码仓库优先通过 branch / PR 修改；知识库这类低风险仓库可以在用户明确要求时直接维护 `main`；定期在 GitHub Settings → Applications 中检查 Authorized / Installed Apps。

---

**经验总结：Connected ≠ Installed ≠ Writable。真正的完成标准是一次真实 GitHub 写入成功。**