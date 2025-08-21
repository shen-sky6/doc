# git命令、规范

## 📌 常见的提交类型（type）

| 类型         | 说明              |
| ---------- | --------------- |
| `feat`     | 新功能（Feature）    |
| `fix`      | 修复 bug          |
| `refactor` | 重构（不影响功能的代码优化）  |
| `style`    | 样式调整（空格、缩进、格式等） |
| `docs`     | 文档修改            |
| `test`     | 测试相关（新增测试、修改测试） |
| `chore`    | 构建过程或辅助工具的变动    |
| `perf`     | 性能优化            |
| `ci`       | CI配置相关变更        |
| `revert`   | 回滚某次提交          |
| `merge`    | 合并分支            |
## 写错分支解决
```ruby 
	git stash
	git checkout master
	git stash pop
```

### git合并后没推送，怎么取消合并

### ✅ 情况一：**还没提交（`commit`）**

即你执行了 `git merge xxx`，但还没 `git commit`。

#### 👉 取消合并：

bash

复制编辑

`git merge --abort`

这会取消合并操作，回到合并前的状态。

---

### ✅ 情况二：**合并后已经提交，但还没 push**

即你 `git merge xxx` 然后 `git commit -m "merge xxx"`，但还没推送。

#### 👉 回退最近一次提交（保留修改）：

bash

复制编辑

`git reset --soft HEAD~1`

> 如果你不想保留合并带来的文件变动，可使用：

bash

复制编辑

`git reset --hard HEAD~1`

> ⚠️ 注意：`--hard` 会清除合并带来的文件变动，谨慎使用！

---

### ✅ 如何判断当前状态？

bash

复制编辑

`git status`

- 如果显示 `You have unmerged paths.` → 表示还没提交，适合用 `git merge --abort`
    
- 如果显示 `nothing to commit` 或 `working tree clean` → 表示已经提交，但未推送