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
