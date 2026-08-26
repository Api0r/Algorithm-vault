
**1. 初始化与配置**

  

- `git init`：在当前目录初始化一个新的本地仓库。
    
      
    
- `git clone <url>`：克隆远程仓库到本地。
    
      
    
- `git config --global user.name "你的名字"`：配置全局提交者姓名。
    
      
    
- `git config --global user.email "你的邮箱"`：配置全局提交者邮箱。
    
      
    

**2. 常用工作流（暂存与提交）**

  

- `git status`：查看工作区与暂存区的状态。
    
      
    
- `git add <file>`：将指定文件添加到暂存区（`git add .` 添加所有改动）。
    
      
    
- `git commit -m "提交信息"`：将暂存区内容提交到本地仓库。
    
      
    
- `git commit -am "提交信息"`：直接暂存并提交已追踪的修改文件（跳过 `git add`）。
    
      
    
- `git diff`：查看工作区与暂存区的文件改动对比。
    
      
    
- `git diff --staged`：查看暂存区与上一次提交之间的对比。
    
      
    

**3. 分支操作**

  

- `git branch`：查看所有本地分支（`-a` 查看包含远程的所有分支）。
    
      
    
- `git branch <branch-name>`：创建新分支。
    
      
    
- `git switch <branch-name>` 或 `git checkout <branch-name>`：切换到指定分支。
    
      
    
- `git switch -c <branch-name>` 或 `git checkout -b <branch-name>`：创建并立即切换到新分支。
    
      
    
- `git merge <branch-name>`：将指定分支合并到当前分支。
    
      
    
- `git branch -d <branch-name>`：删除已被合并的本地分支（`-D` 强制删除）。
    
      
    

**4. 远程同步**

  

- `git remote -v`：查看绑定的远程仓库地址。
    
      
    
- `git fetch`：拉取远程所有分支的最新更新（不自动合并）。
    
      
    
- `git pull`：拉取远程更新并直接合并到当前分支（推荐 `git pull --rebase` 保持提交线整洁）。
    
      
    
- `git push origin <branch-name>`：推送本地分支到远程仓库。
    
      
    
- `git push -u origin <branch-name>`：首次推送并建立上游追踪关系。
    
      
    

**5. 撤销与暂存**

  

- `git restore <file>`：放弃工作区未暂存的修改。
    
      
    
- `git restore --staged <file>`：将文件移出暂存区，保留修改。
    
      
    
- `git reset --soft HEAD~1`：撤销最近一次 commit，但保留工作区和暂存区的代码。
    
      
    
- `git reset --hard HEAD~1`：**危险**：彻底回退到上一个版本，丢弃后续所有修改。
    
      
    
- `git stash`：把当前未提交的改动临时保存到堆栈区。
    
      
    
- `git stash pop`：恢复并弹出最近一次暂存的改动。
    
      
    

**6. 查看历史**

  

- `git log`：查看提交历史记录。
    
      
    
- `git log --oneline --graph --all`：单行树状图显示所有分支的提交图谱。