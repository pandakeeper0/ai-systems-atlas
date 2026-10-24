# Git Storage Model · Git 存储原理

> Git 最核心的设计可以概括成：**内容寻址的对象数据库 + refs + working tree/index**。commit 记录的是一个完整快照的入口，而不是一份 diff。

## 1. 核心对象模型

Git 对象数据库里最重要的对象有：

- **blob**：文件内容；
- **tree**：目录快照，记录名字、mode、对象 ID；
- **commit**：一次提交，指向 root tree，并记录 parent、author、message 等；
- **tag object**：annotated tag 使用的对象。

典型结构：

\`\`\`text
commit
  |
  v
root tree
├── README.md -> blob
└── src       -> tree
                 ├── main.py -> blob
                 └── util.py -> blob
\`\`\`

所以：

\[
\boxed{\text{目录 = tree，普通文件内容 = blob}}
\]

blob 不保存文件名。文件名和目录名存在父 tree 的 entry 里。tree entry 可以理解为：

\`\`\`text
mode + name + object-id
\`\`\`

常见 mode：

\`\`\`text
100644  普通文件
100755  可执行文件
040000  tree / 目录
120000  symlink
160000  gitlink / submodule
\`\`\`

## 2. Symlink 怎么存？

symlink 在 tree 里使用 mode 120000，并指向一个 blob。这个 blob 存的不是目标文件内容，也不是目标 blob ID，而是 **symlink target 路径字符串**。

例如：

\`\`\`text
repo/
└── a/
    ├── target.txt
    └── link -> target.txt
\`\`\`

Git 大致保存：

\`\`\`text
tree a
├── target.txt -> blob A
└── link       -> blob B   # mode 120000
\`\`\`

blob B 的内容就是：

\`\`\`text
target.txt
\`\`\`

checkout 时 Git 按该字符串创建 symlink；真正如何解析路径由操作系统 / 文件系统负责。相对 symlink 是相对于 **symlink 所在目录** 解释的，不是相对于仓库根目录。

## 3. Object ID 怎么计算？

Git 是 content-addressed storage。

以 blob 为例，Git 会先构造：

\`\`\`text
blob <content_length>\0<content>
\`\`\`

再计算：

\[
\boxed{OID=HASH(\text{header}+\text{content})}
\]

传统 Git 仓库使用 SHA-1，新格式仓库也支持 SHA-256。

tree 的 entry 会按规定序列化：

\`\`\`text
<mode> <filename>\0<raw object-id bytes>
\`\`\`

commit 的内容则逻辑上包括：

\`\`\`text
tree <root-tree-oid>
parent <parent-commit-oid>
author ...
committer ...

commit message
\`\`\`

因此：

\[
\text{blob 变化}
\rightarrow
\text{tree OID 变化}
\rightarrow
\text{root tree OID 变化}
\rightarrow
\text{commit OID 变化}
\]

单个 commit 的目录快照很像 Merkle Tree，但 Git 整体因为对象共享、merge commit 多 parent，更准确地说是：

\[
\boxed{\text{Merkle DAG}}
\]

## 4. git add、Index 和 git commit

新建或修改文件时，它首先只存在 Working Tree。

执行：

\`\`\`bash
git add hello.txt
\`\`\`

Git 实际做两件事：

1. 把文件当前内容写成 blob，放进本地 object database；
2. 更新 index，记录这个路径下一次 commit 要使用哪个 blob。

可以理解为：

\[
\boxed{\text{Index[path]}=(mode,\ blob\ OID)}
\]

所以暂存区不是“临时存 blob 的地方”，而更像：

\[
\boxed{\text{Index = 下一次 commit 的快照清单}}
\]

然后：

\`\`\`bash
git commit
\`\`\`

根据 index 生成 tree objects，再创建 commit object，并更新当前 branch ref：

\[
\boxed{
\text{Working Tree}
\xrightarrow{\texttt{git add}}
\text{blob + index}
\xrightarrow{\texttt{git commit}}
\text{tree + commit + ref update}
}
\]

日常使用可以继续保留“四个区”模型：

\[
\boxed{
\text{Working Tree}
\rightarrow
\text{Index / Staging Area}
\rightarrow
\text{Local Repository}
\rightarrow
\text{Remote Repository}
}
\]

## 5. diff 和 checkout

Git 比较两个 commit 时，可以递归比较 tree；如果某个 subtree 的 OID 完全相同：

\[
OID_{tree,A}=OID_{tree,B}
\]

就可以直接跳过整个子树，不必继续看里面的文件。

所以 diff 的核心之一是：

\[
\boxed{\text{递归比较 tree + 相同 OID 剪枝}}
\]

checkout 则可以理解为：

\[
\boxed{
\text{commit}
\rightarrow
\text{root tree}
\rightarrow
\text{递归 materialize 文件系统快照}
}
\]

实际实现会结合 index 和当前 Working Tree 做增量更新。

## 6. refs、branch、HEAD 和 tag

ref 的本质：

\[
\boxed{\text{name}\rightarrow\text{object ID}}
\]

本地 branch：

\`\`\`text
refs/heads/main -> commit OID
\`\`\`

远端跟踪分支：

\`\`\`text
refs/remotes/origin/main
\`\`\`

正常 HEAD 通常是：

\`\`\`text
ref: refs/heads/main
\`\`\`

所以：

\[
\boxed{HEAD\rightarrow branch\ ref\rightarrow commit}
\]

直接 checkout 某个 commit 时可能进入 detached HEAD：

\[
\boxed{HEAD\rightarrow commit}
\]

Lightweight tag 大致是：

\[
\text{tag ref}\rightarrow\text{commit}
\]

Annotated tag 则是：

\[
\text{tag ref}\rightarrow\text{tag object}\rightarrow\text{commit}
\]

tag object 还能保存 tagger、时间、message、签名等信息。

ref 不一定永远以独立文件存在，也可能被压缩进 \`packed-refs\`。

## 7. git cat-file

查看对象类型：

\`\`\`bash
git cat-file -t <oid>
\`\`\`

Pretty-print 对象：

\`\`\`bash
git cat-file -p <oid>
\`\`\`

查看对象大小：

\`\`\`bash
git cat-file -s <oid>
\`\`\`

这组命令可以手工沿着：

\`\`\`text
commit -> tree -> subtree/blob
\`\`\`

观察 Git 的真实对象关系。

## 8. stash 怎么存？

stash 不属于另一个独立存储系统。数据仍然是普通 Git objects，真正存在 object database 中。

Git 用特殊 ref：

\`\`\`text
refs/stash
\`\`\`

指向最新 stash，历史主要由：

\`\`\`text
logs/refs/stash
\`\`\`

也就是 reflog 保存。

所以：

\[
\boxed{\text{stash = 特殊 ref + 一组普通 Git objects}}
\]

一个 stash 往往不止一个简单 commit，因为要同时表达 working tree、index，必要时还有 untracked 状态。

## 9. Submodule 怎么存？

submodule 在主仓库 tree 中使用 mode：

\`\`\`text
160000
\`\`\`

对应一个 gitlink：

\`\`\`text
160000 commit <submodule-commit-oid> path/to/submodule
\`\`\`

所以主仓库真正 pin 的是：

\[
\boxed{\text{submodule 的具体 commit OID}}
\]

而主仓库根目录的 \`.gitmodules\` 主要保存：

\`\`\`ini
[submodule "third_party/foo"]
    path = third_party/foo
    url = https://example.com/foo.git
    branch = main
\`\`\`

因此：

\[
\boxed{.gitmodules = 去哪里找 / 路径 / 可选跟踪分支}
\]

\[
\boxed{gitlink = 当前主仓库版本真正锁定的 commit}
\]

本地 submodule 的 Git 元数据通常放在：

\`\`\`text
.git/modules/<submodule-path>/
\`\`\`

初始化并递归对齐父仓库 pin 的版本：

\`\`\`bash
git submodule update --init --recursive
\`\`\`

clone 时直接递归：

\`\`\`bash
git clone --recurse-submodules <repo>
\`\`\`

如果要主动跟踪 submodule 配置分支的最新 commit：

\`\`\`bash
git submodule update --remote --recursive
\`\`\`

但随后仍要在主仓库：

\`\`\`bash
git add path/to/submodule
git commit
\`\`\`

才能把新的 gitlink commit 正式记录下来。

普通 embedded/nested Git repo 可能只留下 gitlink，却没有 \`.gitmodules\` 中的 URL 信息，因此跨机器协作时通常是不完整、不可自动复现的配置。

## 10. Remote Repository

远端常见为 bare repository，没有普通 Working Tree。

核心仍然是：

\`\`\`text
objects/
refs/
HEAD
packed-refs
...
\`\`\`

push 主要做两件事：

1. 上传远端缺少的 Git objects；
2. 更新远端 refs。

所以本地仓库不是远端缓存，而是一个完整 Git repository。

## 11. Sparse Checkout · 少展开

Sparse Checkout 控制：

\[
\boxed{\text{哪些路径真正 materialize 到 Working Tree}}
\]

典型命令：

\`\`\`bash
git sparse-checkout init --cone
git sparse-checkout set backend
\`\`\`

单独使用 Sparse Checkout 时，本地 object database 仍可能已经拥有普通 clone 会拿到的大量对象。

它主要减少：

- Working Tree 文件数量和磁盘占用；
- checkout / switch 的文件系统开销；
- IDE indexing；
- file watcher；
- antivirus 扫描；
- 某些 working-tree 状态操作。

所以：

\[
\boxed{\text{Sparse Checkout = 少“展开”}}
\]

## 12. Partial Clone · 少下载

Partial Clone 控制的是一开始真正下载到本地的对象。

常见：

\`\`\`bash
git clone --filter=blob:none <repo>
\`\`\`

这会尽量先拿 commit/tree 等结构信息，不一次性获取大量 blob。

后续某个操作真的需要缺失 blob 时，Git 会通过 promisor remote 自动 lazy fetch：

\`\`\`text
需要 blob X
   ↓
本地没有
   ↓
promisor remote
   ↓
自动 fetch X
   ↓
继续原操作
\`\`\`

所以：

\[
\boxed{\text{Partial Clone = 少“下载”}}
\]

## 13. Shallow Clone · 少历史

Shallow Clone 控制 commit history 深度。

例如：

\`\`\`bash
git clone --depth=1 <repo>
\`\`\`

完整历史：

\`\`\`text
A -> B -> C -> D -> E
\`\`\`

depth=1 大致只保留 E 这一层历史，但为了 checkout E，仍需要它当前快照所需的 tree/blob。

所以：

\[
\boxed{\text{Shallow Clone = 少历史}}
\]

它特别适合历史很深、旧历史里存在大量后来已删除 blob 的仓库。

## 14. 三种大仓库优化的区别

| 机制 | 主要减少什么 | 典型命令 |
| --- | --- | --- |
| Sparse Checkout | Working Tree 文件 | \`git sparse-checkout set ...\` |
| Partial Clone | 下载的对象 / blob | \`git clone --filter=blob:none\` |
| Shallow Clone | commit 历史深度 | \`git clone --depth=N\` |

可以组合：

\`\`\`bash
git clone --depth=1 --filter=blob:none --sparse <repo>
git sparse-checkout set backend
\`\`\`

即：

\[
\boxed{\text{历史少拉 + blob 少拉 + 工作区少展开}}
\]

## 15. 大 blob、reachability 和 GC

如果旧 commit 仍然引用大 blob，即使当前版本已经删除文件，这个 blob 仍然可能保留。

真正释放空间通常需要：

\[
\boxed{
\text{重写历史}
\rightarrow
\text{让旧对象 unreachable}
\rightarrow
\text{GC / prune}
}
\]

例如：

\`\`\`bash
git filter-repo --path big.bin --invert-paths
git reflog expire --expire=now --all
git gc --prune=now
\`\`\`

或按大小清理：

\`\`\`bash
git filter-repo --strip-blobs-bigger-than 100M
\`\`\`

远端托管平台上，force push 只是让公开 refs 指向新历史：

\[
\boxed{\text{force push} \neq \text{旧 blob 已立即从服务器磁盘删除}}
\]

平台还可能有隐藏 refs、PR refs、保留机制和后台 GC。

reflog 的作用是保存 ref 以前指向哪里，方便恢复误操作。它会影响本地对象何时可以被 GC，但远端服务器自己的 reflog 不会因为普通 fetch 就自动传给客户端。

## 16. 最终 Mental Model

Git 可以压缩成四层：

\[
\boxed{
\text{Objects}
+
\text{Refs}
+
\text{Index}
+
\text{Working Tree}
}
\]

其中：

\[
\boxed{\text{Objects}=\text{commit/tree/blob/tag}}
\]

\[
\boxed{\text{Refs}=\text{branch/tag/stash 等名字到对象的引用}}
\]

\[
\boxed{\text{Index}=\text{下一次 commit 的快照描述}}
\]

\[
\boxed{\text{Working Tree}=\text{真正展开给用户编辑的文件系统状态}}
\]

大仓库优化分别作用在不同层：

\[
\boxed{\text{Shallow}=\text{少历史}}
\]

\[
\boxed{\text{Partial}=\text{少对象下载}}
\]

\[
\boxed{\text{Sparse}=\text{少 Working Tree materialization}}
\]

这套模型也是理解 CI、monorepo、AI agent workspace 和未来 Git 存储优化的基础。
