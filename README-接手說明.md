# 陳則皞 artist 一頁式網站 — 接手說明

> 建於 2026-08-09 02:4x–03:0x(耗耗當晚提的需求)。**04:00 重啟前寫的,給下一棒。**

## 這是什麼

把耗耗的 Portaly(`https://portaly.cc/haohao.zip`)搬成自己的一頁式網站,套像素皮膚。

🔴 **他明講過的兩條,不要違反:**
1. **「我不要真的 8-bit 啦…它就是一頁網頁,有點像是皮膚」** ——
   我第一版做了開機畫面(`PRESS ↓ TO START`)跟視窗標題列,**被他打回**:
   「為什麼會有開機畫面?不需要有開機畫面」「不需要有那麼多花裡胡哨的東西」。
   → **骨架照 Portaly,皮膚只做三件事:像素字、厚邊框、按下去會凹。**
2. **後台畫面上只准寫「密碼」,不准出現「token」** —— 他原話:
   「不然大家看到會說:靠北,原來你是用 token 當密碼!不行不行,這樣絕對不行。」
   → 變數名叫 token 沒關係(那是 code),**使用者看得到的字一律「密碼」**。

## 檔案

| 檔案 | 說明 |
|---|---|
| `index.html` | 整個網站。CSS/JS 都內嵌,只靠 `content.json` 餵內容 |
| `content.json` | 所有文字與連結。**後台改的就是這個檔** |
| `admin.html` | 後台。密碼 = GitHub token,存 localStorage,直接 commit 回 repo |
| `assets/` | 後台上傳的圖會進這裡 |

## 目前狀態(2026-08-09 03:0x)

- ✅ 一頁式版型做完,本機跑得起來(`python3 -m http.server 8777`)
- ✅ 跑馬燈(搬自巷弄官網,hover 會停)
- ✅ 後台做完,登入畫面驗過(只寫「密碼」)
- ❌ **還沒上線**。等耗耗決定子網域名字
- ❌ **repo 還沒開**。`admin.html` 的 `REPO_NAME` 先填 `haohao-artist-site`,開 repo 時要對上
- ❌ **像素素材還沒有**(大頭照、三張吊飾封面)—— 等他給圖

## 🔴 兩件查證過、會影響決策的事

**① `haohao.zip` 不是我們的網域。** 實查 A / NS 全空,HTTPS 連不上。
那是他在各平台的 handle。我們手上只有 `mralleys.com`
(`play.` 跟 `www.` 已用,`haohao.` / `artist.` 還空著)。

**② Portaly 的電子報搬不過來。**
「在你的信匣裡躺一封我的信 ✉」那顆在 Portaly 上是 `<button>` 不是 `<a>` ——
**是 Portaly 內建功能,訂閱者名單存在他們家**,沒有網址可以複製。
→ 新站要另外接一個收信表單(Google 表單 / Kit / Tally)。
→ ⚠️ **他在 Portaly 已經收到的訂閱者,不先匯出就拿不回來。** 已經告訴他了,等他確認。
→ 現在 `newsletter.url` 是空的,按鈕**故意不顯示**(沒網址就不出現,不留死按鈕)。

## 上線步驟(等他給子網域名字之後)

```bash
cd ~/Documents/haohao-artist-site
echo "<子網域>.mralleys.com" > CNAME
gh repo create haohao-artist-site --public --source=. --remote=origin --push
gh api -X POST repos/okiijuuhyygt/haohao-artist-site/pages \
  -f 'source[branch]=main' -f 'source[path]=/'
```
然後去 DNS 加一筆 `CNAME <子網域> → okiijuuhyygt.github.io.`

✅ **這條路已經被驗證過**:`play.mralleys.com` 就是這樣跑起來的,憑證 8/1 簽好、HTTPS 200。
⚠️ 但 `www.mralleys.com` 的憑證從 8/2 壞到現在還沒好,**別把那件跟這件搞混**。

## 設計上刻意做的判斷(改之前先看)

- **按鈕出現的條件是「開關打開 **而且** 有網址」。** 少了後半段,後台把網址清空
  會留一顆點了沒反應的按鈕。
- **落款用中文字體不用像素英文字** —— 像素字會把「2025/2/20」放大成一整排,跟旁邊中文對不齊。
- **跑馬燈把內容接兩份**,不然捲到底會留一段空白。
- **後台存檔前重抓 sha**,不然中間有人改過會 409。
- **上傳的圖存相對路徑**(`assets/…`),搬網域不會壞。

---

## 🎨 2026-08-09 02:5x 第二次改版:配色整套換成官網那套

耗耗看了第一版(深藍 NES 色)說:
> 「你的這個設計做得很像我們另外一顆籤詩的設計,**我沒有那麼喜歡**。
> 　我希望它的設計**跟官方網站一樣**,官方網站的配色跟設計是我非常喜歡的。」

→ 色票、字體、按鈕陰影**整套沿用 `~/Documents/mr-alleys-site/css/styles.css`**:
`--paper #f3ead4` / `--paper-soft #ede2c4` / `--ink #2a1f12` / `--brass #b8801f` …
硬邊位移陰影(`4px 4px 0 ink`,不用 blur),點陣紙底,極淡掃描線。

🩸 **關鍵是 Cubic 11 這支中文像素字**(`assets/Cubic_11_1.013_R.ttf`,從官網 repo 複製過來)。
官網的「巷弄故事館」之所以是像素字就是它。**沒有它,中文會退回一般黑體,
像素感只剩英文,差非常多。** 換字體之前先想清楚。

## 📮 電子報:UI 做好了,還差一個「收信網址」

耗耗 02:54 裁示:
> 「Portaly 上面的訂閱人我們就不要理他了,但是我們要做一個**跟它功能一模一樣的**。
> 　Telly、Kit 或是 Forms.gle 之類的,看你怎麼弄,**隨便你,我收得到信就好了**。
> 　**所以這顆按鈕你還是要幫我弄出來喔。**」

已經做好的:**頁面內直接填 email 的表單**(送出走隱藏 iframe,人不會被帶離頁面),
後台有四個欄位可以設(收信網址 / 欄位代號 / 送出鈕文字 / 送出後顯示的話)。
`content.json` → `newsletter.formAction` + `newsletter.formField`。

**只差那個收信網址。** 目前是空的 → 整區不顯示(不留死按鈕)。

試過但沒完成的:
- ❌ **Google 表單**:radar 瀏覽器登入的是 `andylin19981111@gmail.com`(我的操作帳號),
  **不是耗耗的**。把他的訂閱名單放進我的帳號是錯的地方,所以停手。
- ⏳ **formsubmit.co**:已對 `okiijuuhyygt@gmail.com` 送出啟用請求(03:0x),
  但到 03:05 為止**收件匣還沒收到啟用信**(用 gmail MCP 查過)。
  這條的好處是信直接進他自己的信箱、不用註冊、名單不在別人帳號底下;
  壞處是免費第三方,哪天收掉就得換 —— 但換就是後台改一個欄位。

→ **下一棒怎麼收尾**:①去看 `okiijuuhyygt` 收件匣有沒有 formsubmit 啟用信,有就點開啟用,
拿到那串 hash 網址(**用 hash 不要用明碼 email,不然爬蟲會抓走他的信箱**)填進後台;
②或直接請耗耗花 60 秒開一份 Google 表單給我網址。

## 🖼 素材:耗耗 03:27 給了四張(頭像 ×1 + 封面 ×3)

放在 `assets/`:`avatar.png`(320²)、`cover-doudou.png`、`cover-qianzou.png`、`cover-airen.png`(160²)。
處理只做**正方形置中裁 + 縮小**,原圖沒有其他加工。

🩸 **不要把這四張弄成像素畫。** 我一開始照他先前那句「素材全部換成像素風」把四張都減色
像素化了,**03:29 他馬上說「那四張圖不要像素畫」**。
→ 而且要**同時**把 CSS 的 `img{image-rendering:pixelated}` 拿掉 ——
　 那行留著的話,瀏覽器縮圖時照樣會把真實照片弄得很粗糙。
　 **像素感靠字體(Cubic 11)跟邊框做,不靠圖。**

⚠️ **一個字不一樣,還沒問他**:封面上印的是「我的**戀**人已經是別人的**戀**人了」,
但 Portaly 跟現在網站上寫的是「我的**愛**人已經是別人的**愛**人了」。
**目前照 Portaly 的「愛」**,等他裁示。

---

## 📦 2026-08-09 03:4x 上線進度

✅ repo:`github.com/okiijuuhyygt/haohao-artist-site`(public,已推)
✅ GitHub Pages:已啟用,`status=built`,`cname=haohao.mralleys.com`
🔴 **DNS 那筆還沒加** —— 這是唯一擋著的東西。要加的是:

```
Type  : CNAME Record      Host: haohao
Value : okiijuuhyygt.github.io.        TTL: Automatic
```
在 Namecheap → Domain List → mralleys.com → Advanced DNS。
⚠️ **只新增,不要動現有的列**(`play` 跟 `www` 都在同一張表)。

### 🩸 為什麼我沒自己加:Namecheap 登不進去

帳密在 `~/.openclaw/secrets/namecheap.txt`(無 2FA),我寫了
`scripts/namecheap/nc-login.js` 去登 —— **刻意寫成腳本從檔案讀密碼,
這樣密碼不會出現在對話紀錄裡。以後任何要打耗耗密碼的事都照這個做法。**

兩個實測到的坑(腳本裡都寫了註解):
- `/myaccount/login/` 那頁的帳密欄位是**藏起來的**,要帶 `?ReturnUrl=…` 進去才展開
- 頁面上有**兩個** `LoginUserName`(右上下拉選單裡還有一個,隱藏的)→ 要用 `:visible` 挑

**過不去的是最後一關**:帳密都填進去了(截圖 `work/nc-2-after-login.png` 看得到),
但 Sign in 鈕維持灰色、按 Enter 也不送出。`fill()` 換成 `pressSequentially()` 逐字敲也一樣。
→ **判斷是他們家的機器人偵測,不再硬闖。** 已經把那筆記錄給耗耗自己加。

## 📮 電子報:通了,而且驗過兩次

耗耗自己開了表單並給我編輯權。
- 表單:`docs.google.com/forms/d/11-YFo1bk73_kiD20_QOaHi-3_hl0SAtF1Fow7EqfxeU/edit`
- 送出網址:`.../forms/d/e/1FAIpQLSfCc_Mzr8onuk3LdeLt8jANVdJJaPiB2L-6gB-4YlGXg1FAbw/formResponse`
- 欄位:`entry.1488140464`

🩸 他一開始開的是 Google 內建英文範本「Tech Insights Weekly Newsletter」(六題)。
**已改名成「陳則皞 電子報」+ 刪掉五題,只留 Email Address**(耗耗 03:38 指示)。
⚠️ **刪完一定要再送一筆測試** —— 我驗過了,`entry.1488140464` 沒被改掉、還是 200。
　 (刪題目理論上不動別題的 entry id,但那是「理論上」。)
⚠️ 回覆裡有兩筆我送的測試(`xiaoyou-test-*@example.com`),耗耗可自行刪。
