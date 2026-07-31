# AGENTS.md — threekingdom_map

## 這是什麼

互動式三國兵裝據點地圖網頁（純前端，無 build 步驟）。三國地圖背景 + 可點擊據點標點；點擊據點顯示該處可造的兵裝與材料，點擊左側兵裝清單會在地圖高亮所有可製造該兵裝的據點。

## 位置對應

- 容器內：`/workspace/threekingdom_map`
- 使用者本機：`D:\DforAPP\Opencode_docker\threekingdom_map`（兩邊同一個資料夾）

## 目前狀態

- [x] 原型功能完整可運作（標點 ⇄ 兵裝雙向連動、材料顯示、高亮/變暗）
- [x] GitHub repo：`QAQRain/threekingdom_map`，branch `main`
- [x] GitHub Pages 已上線：https://qaqrain.github.io/threekingdom_map/（push main 自動部署）
- [x] **真實地圖已上線**：`assets/map.png`（1122x771，來源 `assets/map/new.png`）
- [x] **63 個真實據點座標已匯入** `data/markers.json`（名字已轉繁體）
- [x] **座標點選工具**：`tools/coord-picker.html`（載入地圖點擊標記＋輸入名字＋匯出 JSON）
- [ ] **真實兵裝資料尚未提供**（`data/items.json` 是示範資料）
- [ ] **據點 items 對應尚未提供**（目前所有據點 `items: []`）

## 檔案結構

- `index.html` / `style.css` / `app.js`：全部互動邏輯
- `data/items.json`：兵裝 `{ id, name, materials: {材料:數量} }`（目前為示範資料）
- `data/markers.json`：63 據點 `{ id, name, x, y, items: [兵裝id] }`，`x`/`y` 是地圖上的百分比（0–100，左上角 0,0）
- `assets/map.png`：正式地圖背景（1122x771）。換圖要同時改 `style.css` 的 `.map-wrap` `aspect-ratio`（目前 `1122 / 771`）
- `assets/map/`：地圖素材與截圖（**未 commit**，僅本機留存；含 `B.png`、`new.png`、6 張截圖、分析暫存圖）
- `tools/coord-picker.html`：據點座標點選工具，圖片來源 `../assets/map/new.png`，localStorage key `coord_picker_markers_new`
- `README.md`：給使用者看的資料編輯說明

## 本機驗證

- `python3 -m http.server 8000`，開 `http://localhost:8000`（直接開 `index.html` 會因 `fetch` 被擋而失效）
- JSON 交叉檢查：`python3 -c "import json; json.load(open('data/items.json'))"`

## Git / GitHub 操作

- remote：`https://github.com/QAQRain/threekingdom_map.git`（branch `main`）
- **push 需要 GitHub PAT**。先前 token 已給過，但使用者可能已撤銷；要 push 時需重新向使用者索取。
- 避免把 token 存進 remote URL：`git -C <dir> -c http.extraHeader="Authorization: Bearer <TOKEN>" push` 或內嵌 URL push 後立刻清除 remote URL。
- ⚠ 本環境實測：`http.extraHeader` 方式被 GitHub 拒為 `invalid credentials`；**改用一次性內嵌 URL** 可成功：
  `git -C <dir> push "https://<USER>:<TOKEN>@github.com/QAQRain/threekingdom_map.git" main`（remote 設定不受影響）
- repo 沒有本機 git 身份設定，commit 要用 `git -C <dir> -c user.name=QAQRain -c user.email=qaqrain@gmail.com commit ...` 或先設定 config。
- 容器預設沒有 git（`apk add --no-cache git`）。

## 容器環境注意

- 預設沒有 node/python/curl/git，需要時 `apk add --no-cache <套件>`（Alpine，root）。
- 本環境 bash 工具的 `workdir` 參數有時不生效，git 指令請一律用 `git -C /workspace/threekingdom_map`。
- 容器為 `--rm`，安裝的套件重啟後消失；`/workspace` 掛載本機資料夾，檔案會保留。

## 使用者偏好

- 一律使用繁體中文回覆。
