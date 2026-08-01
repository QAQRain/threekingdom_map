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
- [x] **據點已全量重新標點**：使用者用工具「1:1」模式重標 63 點（commit 182ceac），以房子圖示底部對準座標（style.css `.marker` 錨點修正，commit 237cff6），使用者確認位置 OK
- [x] **兵裝截圖已上線**：`assets/soldier/`（檔名已由使用者改中文 → 轉拼音，如 丹陽兵=`danyangbing.png`），`data/items.json` 含 38 筆 `{id(拼音), name(中文), image, materials:{}, craftable}`。其中 **7 個非兵裝單位**（刀牌手、工匠團、弓箭衛、弩衛、採藥隊、突騎營、長槍營）標 `craftable: false`，顯示在列表最底部、點擊無作用（app.js `renderSidebar` 排序 + `.inactive` 樣式）
- [x] **兵裝清單改為純圖片顯示**（app.js `renderSidebar` 用 `<img>`，圖上本身有兵裝名稱；commit 15f66b5）
- [x] **手機版優化**（commit b4d0184、4194fb1）：地圖可縮放（右下角 ＋/−/100% 按鈕，**標點隨地圖同步縮放**）、桌面滑鼠**拖曳移動**地圖（drag-to-pan，拖曳不會誤觸據點）、手機彈窗**固定左上角**、手機縮放按鈕改放右上並觸控最佳化（touch-action/safe-area）。主要使用場景為電腦。
- [x] **據點⇄兵裝對應已完成**（63 據點全填，依使用者提供的區域組合：冀州9、涼州10、中原14、青州9、蜀6、江東15；commit 待 push）
- [ ] 兵裝材料（items 的 `materials`）部分提供：7/31 有材料（丹陽兵、黃巾浮水團、大漢羽林軍、解煩兵、橫江射士、解煩火部、江東飛槊，來源為新野的資料），其餘 24 個待補

## 檔案結構

- `index.html` / `style.css` / `app.js`：全部互動邏輯
- `data/items.json`：兵裝 `{ id, name, image, materials: {材料:數量}, craftable? }`，`id` 為拼音檔名，`image` 指向 `assets/soldier/<拼音>.png`。目前 38 筆；31 個可製作，7 個 `craftable: false`（非兵裝單位，僅留存譜面）
- `data/markers.json`：63 據點 `{ id, name, x, y, items: [兵裝id] }`，`x`/`y` 是地圖上的百分比（0–100，左上角 0,0）
- `assets/map.png`：正式地圖背景（1122x771）。換圖要同時改 `style.css` 的 `.map-wrap` `aspect-ratio`（目前 `1122 / 771`）
- `assets/soldier/`：38 個兵裝截圖（拼音檔名，**已 commit**）
- `assets/map/`：地圖素材與截圖（**未 commit**，僅本機留存；含 `B.png`、`new.png`、截圖等）
- `tools/coord-picker.html`：據點座標點選工具，圖片來源 `../assets/map/new.png`，localStorage key `coord_picker_markers_new`，有「1:1」按鈕以原始尺寸檢視
- `README.md`：給使用者看的資料編輯說明

## 本機驗證

- `python3 -m http.server 8000`，開 `http://localhost:8000`（直接開 `index.html` 會因 `fetch` 被擋而失效）
- JSON 交叉檢查：`python3 -c "import json; json.load(open('data/items.json'))"`

## Git / GitHub 操作

- remote：`https://github.com/QAQRain/threekingdom_map.git`（branch `main`）
- **push 需要 GitHub PAT**。先前 token 已給過，但使用者可能已撤銷；要 push 時需重新向使用者索取。
- ⚠ **push 被拒經驗**（repository rule violations）：曾因 `git add -A` 把 `assets/map/` 未 commit 素材一起加入而導致 push 被拒；**務必只 add 要上傳的檔案**（如 `git add data/markers.json app.js style.css assets/soldier/`），不要 `add -A`。
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

## 下次對話主要任務

1. **兵裝材料**：使用者陸續補材料（格式：`普50`＝皮50 鐵50、`普200 高20`＝皮200 鐵200 韌皮20 精鐵20）。已完成的 7 個見上。填完 `items.json` 的 `materials` 即全地圖生效。
2. 將本次已完成的據點⇄兵裝對應 commit + push。⚠ 只能 add 指定檔案（`data/`），不能 `add -A`。

## 環境限制（重要）

- **模型無法讀取圖片**（工具能 read 圖片檔但模型無法辨識內容）；分辨兵裝截圖需仰賴使用者提供對應名稱，或由使用者自行將檔名改為中文後再由 AI 轉拼音。
