# docker-2025cloud-demo
2025 NTU Cloud Native HW4

本專案示範如何透過 Dockerfile 建置一個簡單的 Docker Image，並透過 GitHub Actions 自動建置與推送到 Docker Hub。

## 作業要求
| Category | Topic | Score | Remark | Down |
|----------|-------|-------|--------|------|
| Docker Hub (20) | 創建一個 Docker Hub Repo，並且有一個名為 2025cloud 的專案 | 10 | 必須要是 Public，助教才可以點選<br>Repo 名稱必須是 2025cloud | ✅ |
| | 專案內有兩個以上 (>=2) 的 Container Image | 10 | Tag/大小 都不限定，有兩個檔案即可 | ✅ |
| Git Repo<br>README &<br>Dockerfile (30) | GitHub 專案內有 Dockerfile | 10 | 有檔案即可，內容與否取決於你的應用程式 | ✅ |
| | README 有清楚描述如何透過 docker build 打包你的應用程式 | 10 | | ✅ |
| | README 有清楚描述如何透過 docker run 運行你的Container Image | 10 | 可以是直接放到 dockerhub 上的，也可以是前述 docker build 結果，總之就是要讓使用者有東西可以參考 | ✅ |
| GitHub Action 整合 (40) | GitHub Action 有辦法去自動執行 Docker Build | 10 | 1. 不限定觸發條件，有相關 Action 即可<br>Image Tag 沒有規定<br>2. 提交 GH Action 連結 | ✅ |
| | GitHub Action 有辦法去自動執行 Docker Push 將產生好的 Image 給推到前述的 2025cloud repo | 10 | 1. 遞交 GH Action 連結，過程要可以看到是推到哪個 Dockerhub/Repo/Tag<br>2. 搭配前述 DockerHub 的連結，要確認 Tag 有在上面 | ✅ |
| | 產生一個 Pull Request，裡面故意寫壞 Dockerfile 讓他失敗，GitHub Action 要有辦法偵測錯誤 | 20 | 1. 請遞交一個 Pull Request 的連結<br>2. PR 內敘述如何改壞 Dockerfile<br>3. GH 下要可以看到自動化執行失敗 | ✅ |
| 文件詳述 (10) | README 以圖文的方式描述自前專案自動化產生 Container Image 的邏輯，以及 Tag 的選擇邏輯 | 10 | 1. 什麼樣的情況會產生 Docker Image/Tag 的設計流程<br>2. 重點是能夠講清楚自己的設計，沒有對錯 | ✅ |

## 專案說明

此專案使用 [`alpine`](https://hub.docker.com/_/alpine) 作為基底映像，並執行一個簡單的 echo 指令：

```Dockerfile
FROM alpine

CMD ["echo", "Hello from 2025cloud!"]
```

## 🐳 Docker 映像使用方式

### 🔨 建立 Docker 映像

```bash
docker build -t <DOCKER_USERNAME>/2025cloud:<TAG> .
````

請將 `<DOCKER_USERNAME>` 替換為你的 Docker Hub 帳號，`<TAG>` 可自行指定，例如 `dev`、`latest` 或自動產生的時間戳。

範例：

```bash
docker build -t juliaouo/2025cloud:latest .
```

也可為映像檔加上額外的標籤
```bash
docker tag <DOCKER_USERNAME>/2025cloud:<TAG> <DOCKER_USERNAME>/2025cloud:<ANOTHER_TAG>
```

---

### ▶️ 執行容器

```bash
docker run --rm <DOCKER_USERNAME>/2025cloud:<TAG>
```

範例：

```bash
docker run --rm juliaouo/2025cloud:latest
```

輸出結果應為：

```
Hello from 2025cloud!
```

---

## ⚙️ GitHub Actions 自動化工作流程

### 觸發條件

本專案的 GitHub Actions workflow（見 `.github/workflows/docker.yaml`）在以下情況自動執行：

* 有程式碼 push 至 `main` 分支
* 建立對 `main` 的 Pull Request
* 手動觸發（workflow\_dispatch）

---

### 自動化步驟

1. Checkout 原始碼
2. 設定時區為台灣並產生 Tag（格式為 `YYYYMMDD-HHMMSS`）
3. 登入 Docker Hub（使用 `DOCKER_USERNAME` 和 `DOCKER_PASSWORD` secret）
4. 建置 Docker 映像並打上 Tag：

   ```text
   <DOCKER_USERNAME>/2025cloud:<TIMESTAMP_TAG>
   ```
5. 推送映像至 Docker Hub（包含自動產生的 tag 與 `latest`）

---

### 🏷️ Tag 規則

* 自動產生的 Tag：格式為 `%Y%m%d-%H%M%S`（範例：`20250507-123456`），以利版本追蹤。
* 同時更新 `latest` 標籤，方便使用者拉取最新版映像。

---

## 📦 Docker Hub 倉庫位置

[https://hub.docker.com/r/juliaouo/2025cloud](https://hub.docker.com/r/juliaouo/2025cloud)

---

## 🧾 Container Image 自動化產生設計說明

* 本專案設計目標為實現 push 以及 pull request 時自動產生 Docker 映像。
* Tag 命名採用「時間戳 + latest」雙重策略，利於 CI/CD 部署與版本追蹤。
* 可視專案特性修改 workflow 檔案，支援不同映像標籤策略或部署條件。
