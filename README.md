# Ciallo.group

Ciallo～(∠・ω< )⌒☆

一個把「Ciallo～(∠・ω< )⌒☆」彈幕轟炸做成網站的小玩具。純靜態網頁，載入時隨機背景圖、五顏六色的 Ciallo 文字從右往左飛，配上隨機語音，還支援 Discord 的自訂連結預覽（Component Embed）。

---

## ✨ 功能

- **彈幕文字**：從 `data/ciallo_texts.json` 隨機抽一句 Ciallo 台詞，由右往左飛出。顏色（HSL 隨機色相）、字體（Bungee／Pacifico／手寫體等多種字型包）、粗體/斜體、大小、速度全部隨機，畫面不會有兩發長一樣。
- **配音**：每噴一發彈幕，就從 `data/music/` 隨機播一個音效。
- **隨機背景**：載入時從 `data/background/` 隨機挑一張圖當背景，每 20 秒自動換一張。
- **Discord 連結預覽**：把網址貼到 Discord 時，用 [Component Embed](https://github.com/discord/discord-api-docs/blob/anthony/embed-unfurl-components/developers/link-previews/component-embeds.mdx) 顯示自訂排版（標題、說明、頭像、背景圖、跳轉按鈕），而不是預設的陽春預覽。
- 純靜態檔案，不需要後端、不需要 build，改完直接上傳就能用。

---

## 📂 檔案結構

```
ciallo_site/
├── index.html                 ← 網站主頁（彈幕動畫、Discord embed 標籤都在這裡）
├── README.md
└── data/
    ├── ciallo_texts.json      ← 各種 Ciallo 台詞（可自行增減）
    ├── config.json            ← 設定：要用哪些背景圖 / 音效檔
    ├── background/            ← 放背景圖（.jpg/.png/.webp 皆可）
    ├── music/                 ← 放 Ciallo 配音音效（.mp3/.ogg/.wav 皆可）
    └── url_embed/
        ├── embed.json         ← Discord Component Embed 的排版設定
        ├── banner.jpg         ← 連結預覽的背景大圖（自行放入，支援 GIF）
        └── avatar.png         ← 連結預覽的頭像（自行放入，支援 GIF）
```

---

## 🚀 部署（Cloudflare Pages）

1. 註冊並登入 [Cloudflare](https://cloudflare.com)。若網域不是在 Cloudflare 買的，把它加進 Cloudflare 並將 Nameserver 改成 Cloudflare 提供的兩組。
2. 儀表板 →「Workers & Pages」→「建立應用程式」→「Pages」→「上傳資產」。
3. 把整個 repo（`index.html` 在最外層根目錄，`data/` 跟它同一層）拖進上傳區，點「部署」。
4. 到剛建立的 Pages 專案 →「自訂網域」→ 輸入 `ciallo.group`，DNS 與 SSL 會自動處理。
5. 之後要更新內容，本機改完檔案重新上傳（或用 `wrangler pages deploy ./ciallo_site --project-name=你的專案名`）即可，網域不用重設。

> 也可以用任何其他靜態空間（GitHub Pages、Vercel、Netlify…），流程大同小異：整個資料夾丟上去、確認 `index.html` 在網站根目錄就好。

---

## ⚙️ 使用方式 / 自訂

### 背景圖與音效
1. 圖片放進 `data/background/`，音效放進 `data/music/`。
2. 打開 `data/config.json`，把檔名填進 `backgrounds` 和 `music` 陣列：
   ```json
   {
     "backgrounds": ["background/illya.jpg", "background/miyu.png"],
     "music": ["music/ciallo1.mp3", "music/ciallo2.mp3"]
   }
   ```
   陣列裡填的是**相對於 `data/` 的路徑**，程式會自動補上 `data/` 前綴。

### Ciallo 台詞
直接編輯 `data/ciallo_texts.json`，是一個字串陣列，想加幾句都可以。

### 彈幕密度 / 字體 / 音量
在 `index.html` 裡：
- 密度：`setTimeout(loop, rand(450, 1400))` 的數字範圍（毫秒），數字越小彈幕越密。
- 字體：改 `FONTS` 陣列，或換 `<head>` 裡的 Google Fonts 連結。
- 音量：`audio.volume = 0.85;`。

### 自動播放限制
部分瀏覽器（尤其手機版）會擋掉「載入就自動播音效」。被擋下時畫面會跳出「🔊 點我開聲音」按鈕，點一下之後音效就能正常播放，這是瀏覽器政策，無法完全繞過。

---

## 🔗 Discord 連結預覽（Component Embed）

把 `https://ciallo.group` 貼到 Discord 時，會顯示自訂排版，而不是預設預覽。這是 Discord 較新、目前仍在草案階段的功能。

**運作方式**：`index.html` 的 `<head>` 有
```html
<link rel="discord:component-embed" type="application/json"
      href="https://ciallo.group/data/url_embed/embed.json">
```
指向 `data/url_embed/embed.json`，裡面用 Components V2 排版：Media Gallery（背景大圖）＋ Section（標題/說明文字＋頭像縮圖）＋ Separator ＋ Action Row（連結按鈕）。

**要做的事：**
1. 把圖放進 `data/url_embed/`：`banner.jpg`（背景大圖）、`avatar.png`（頭像）。兩者都支援 GIF，把副檔名跟 `embed.json` 裡的 `url` 改成 `.gif` 就行；動圖在 component embed 裡可能只顯示第一幀，不保證會播放。
2. 編輯 `embed.json`：
   - `"content"` 是標題/說明文字，支援 Discord Markdown（`#` 標題、`**粗體**`、`*斜體*`…）。
   - Action Row 裡每顆按鈕的 `url` 換成你要跳轉的連結，`label` 是按鈕文字。
   - `accent_color` 是側邊色條顏色，十六進位轉十進位表示（例如 `#ff3cac` → `16727212`）。

**前提條件（缺一不可）：**
- 網站要是 HTTPS（Cloudflare Pages 預設就是）。
- 外部連結的 JSON 檔（`embed.json`）原始位元組數不能超過 3000 bytes。
- Discord 爬蟲 User-Agent 是 `Discordbot/2.0`，抓網頁跟每張圖片都要在 10 秒內回應完畢。
- 圖片要公開可讀（不用登入）、格式限 PNG/GIF/JPEG/WebP/AVIF、網址長度上限 2048 字元。
- **用 Cloudflare 的話要特別注意**：Bot Fight Mode / WAF 有可能擋掉 `Discordbot`，如果設好後 Discord 完全不吃 component embed，先去 Security → Bots 確認沒擋到它，必要時加一條白名單規則放行。

改完內容或圖片後重新部署即可生效；Discord 端可能會快取舊預覽，重貼一次連結通常就會更新。

---

## 授權

自由取用、修改、部署到你自己的網域。Ciallo～(∠・ω< )⌒☆