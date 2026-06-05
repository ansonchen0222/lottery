# Git 使用指南（按情境分類）

## 情境一：全新專案第一次上傳到 GitHub

當你在本地寫好了一個專案，想放到 GitHub 讓別人看到或備份。

```bash
# 1. 進入專案資料夾
cd /你的專案路徑

# 2. 初始化 git（讓這個資料夾開始被 git 追蹤）
git init
# 用意：在資料夾裡建立 .git 隱藏目錄，git 所有版本紀錄都存在這裡

# 3. 把要上傳的檔案加入暫存區
git add index.html
# 或者加入所有檔案：
git add .
# 用意：告訴 git「這些檔案我要納入下一次提交」
# git add 不會直接存檔，只是標記「準備好了」

# 4. 提交（建立一個版本快照）
git commit -m "initial commit"
# 用意：正式把暫存區的變更存成一個版本節點，附上說明訊息
# 之後任何時候都可以回到這個狀態

# 5. 把預設分支命名為 main
git branch -M main
# 用意：GitHub 現在預設分支叫 main（不是舊的 master），統一命名避免混淆

# 6. 連結遠端 repo（你在 GitHub 網站上建好的那個）
git remote add origin https://github.com/你的帳號/你的repo名.git
# 用意：告訴本地 git「遠端伺服器在這裡」，之後 push/pull 都走這個地址
# origin 是遠端的代號（慣例名稱，可以取別的但沒人這樣做）

# 7. 推上去
git push -u origin main
# 用意：把本地 main 分支的所有 commit 上傳到 GitHub
# -u 的意思是「設定追蹤關係」，之後只需要打 git push 就好，不用再指定 origin main
```

---

## 情境二：日常修改後上傳

你改了幾個檔案，想把最新版本同步到 GitHub。

```bash
# 1. 看看改了什麼
git status
# 用意：列出哪些檔案被修改、新增、刪除
# 紅色 = 還沒加入暫存區，綠色 = 已加入準備提交

# 2. 看具體改了哪些內容
git diff
# 用意：逐行顯示修改前後的差異，方便你確認沒有改錯

# 3. 加入暫存區
git add index.html
# 用意：只提交你確定要的檔案，避免不小心把密碼或暫存檔推上去

# 4. 提交
git commit -m "add undo button and matrix rain background"
# 用意：每次 commit 是一個可回溯的節點，訊息要寫清楚「做了什麼」
# 好的訊息讓你三個月後還看得懂為什麼改

# 5. 推到 GitHub
git push
# 用意：本地的 commit 同步到遠端，別人才看得到
# 如果第一次已經 -u 過了，之後直接 git push 就好
```

---

## 情境三：多人協作 / 遠端有更新

你的同事也在改同一個 repo，你要先拉最新版本再開始改。

```bash
# 1. 拉取遠端最新版本
git pull
# 用意：把 GitHub 上別人推的新 commit 下載到你本地，並自動合併
# 等於 git fetch + git merge 的組合

# 如果有衝突（兩個人改了同一行）：
# git 會在檔案裡標記 <<<<<<< 和 >>>>>>>
# 你需要手動決定保留哪個版本，改完後：
git add 衝突的檔案
git commit -m "resolve merge conflict"
git push
```

---

## 情境四：想嘗試新功能但不影響主版本

你想加一個實驗性功能，萬一搞砸了不想弄髒 main。

```bash
# 1. 建立新分支並切換過去
git checkout -b feature/new-animation
# 用意：從 main 複製一份出來獨立開發
# 分支上的任何改動都不會影響 main

# 2. 正常開發、add、commit
git add .
git commit -m "experiment with confetti animation"

# 3. 開發完確認沒問題，切回 main 合併
git checkout main
git merge feature/new-animation
# 用意：把分支的所有 commit 合進 main

# 4. 推上去
git push

# 5. 刪掉用完的分支（可選）
git branch -d feature/new-animation
# 用意：清理，避免分支越來越多
```

---

## 情境五：改壞了想回到之前的版本

```bash
# 查看歷史 commit
git log --oneline
# 輸出類似：
# a1b2c3d add undo button
# e4f5g6h initial commit

# 方法 A：只想看之前的版本（不改動）
git checkout a1b2c3d
# 用意：暫時跳到那個時間點看看，不會刪除任何東西
# 看完後回來：
git checkout main

# 方法 B：撤銷最近一次 commit（保留檔案修改）
git reset --soft HEAD~1
# 用意：commit 被撤銷，但你的修改還在暫存區
# 適合「commit 訊息寫錯了」或「忘了加某個檔案」

# 方法 C：徹底回到某個版本（慎用，會丟失之後的修改）
git reset --hard a1b2c3d
# 用意：強制回到那個版本，之後的所有修改都消失
# 只在你確定不要後面的東西時用
```

---

## 情境六：不小心把不該推的檔案推上去了

例如 `.env`、密碼檔、大檔案。

```bash
# 1. 加到 .gitignore 防止以後再犯
echo ".env" >> .gitignore
echo "*.csv" >> .gitignore
git add .gitignore
git commit -m "add gitignore"

# 2. 從 git 追蹤中移除（但本地檔案保留）
git rm --cached .env
git commit -m "remove .env from tracking"
git push

# 注意：GitHub 歷史裡還是看得到那個檔案
# 如果是密碼外洩，需要立即改密碼，不能只靠 git 刪除
```

---

## 情境七：GitHub Pages 部署更新

每次你改完 `index.html` 想讓線上版本更新：

```bash
git add index.html
git commit -m "update lottery UI"
git push
# GitHub Pages 會在 1-2 分鐘內自動重新部署，不需要其他操作
```

---

## 常用指令速查表

| 指令 | 用途 |
|------|------|
| `git status` | 看目前狀態（改了什麼、暫存了什麼） |
| `git log --oneline` | 看歷史 commit（精簡版） |
| `git diff` | 看具體改了哪些行 |
| `git add .` | 把所有修改加入暫存區 |
| `git add 檔名` | 只加特定檔案 |
| `git commit -m "訊息"` | 建立版本快照 |
| `git push` | 上傳到 GitHub |
| `git pull` | 從 GitHub 下載最新 |
| `git checkout -b 分支名` | 建立並切換到新分支 |
| `git checkout main` | 切回主分支 |
| `git stash` | 暫時藏起修改（還沒準備好 commit） |
| `git stash pop` | 把藏起來的修改拿回來 |

---

## .gitignore 建議模板

在專案根目錄建一個 `.gitignore` 檔案：

```
# 系統檔
.DS_Store
Thumbs.db

# 環境/密鑰
.env
*.ini
credentials*

# IDE
.idea/
.vscode/
*.code-workspace

# Python
__pycache__/
*.pyc
.venv/

# 資料檔（通常太大不適合放 git）
*.csv
*.xlsx
*.parquet

# Node
node_modules/
```

---

## 心智模型

```
工作目錄 (你看到的檔案)
    │
    │  git add
    ▼
暫存區 (Staging Area)
    │
    │  git commit
    ▼
本地 repo (.git 目錄裡)
    │
    │  git push
    ▼
遠端 repo (GitHub)
```

每一步都是獨立的。`git add` 不等於提交，`git commit` 不等於上傳。這個分層設計讓你可以精確控制「什麼東西在什麼時候對外可見」。
