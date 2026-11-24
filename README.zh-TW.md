<p align="center"><img src="assets/quark-symbol.svg" width="72" height="72" alt=""></p>

<h1 align="center">Quark</h1>

<p align="center">
  <a href="https://skills.sh/sik-limited/quark"><img src="assets/skills-sh-badge.svg" width="136" height="20" alt="skills.sh"></a>
</p>

<p align="center"><a href="README.md">English</a> · <a href="README.ko.md">한국어</a> · <a href="README.ja.md">日本語</a> · <strong>繁體中文</strong></p>

<p align="center">開放原始碼的 <a href="https://agentskills.io/">Agent Skills</a> 與 <code>DESIGN.md</code> 設計參考，讓你使用 Codex、Claude Code 和 Cursor 進行 agentic design（代理式設計）。</p>

<p align="center"><a href="https://design.sik.limited/"><strong>Quark 網站</strong></a> · 預覽 UI 元件與 Design Tokens</p>

```sh
npx skills add sik-limited/quark
```

## 技能

| 技能 | 用途 |
| --- | --- |
| [`quark-ui`](skills/quark-ui/SKILL.md) | 用 Quark 元件與 Design Tokens 建立介面 |
| [`design-to-code`](skills/design-to-code/SKILL.md) | 將螢幕截圖、Figma 畫框與設計規格轉成程式碼 |
| [`design-check`](skills/design-check/SKILL.md) | 檢查現有介面並排定修正優先順序 |
| [`q-clean`](skills/q-clean/SKILL.md) | 將介面重構為元件，修正狀態、動態效果、響應式版面與無障礙問題 |
| [`component-api`](skills/component-api/SKILL.md) | 設計跨框架的元件 API，包括屬性、狀態與組合方式 |
| [`design-tokens`](skills/design-tokens/SKILL.md) | 定義色彩、間距、圓角、陰影、動態效果與主題的 Design Tokens |
| [`animate`](skills/animate/SKILL.md) | 實作介面轉場、彈簧動畫及手勢，並支援減少動態效果的設定 |
| [`interface-polish`](skills/interface-polish/SKILL.md) | 調整圓角、視覺對齊、邊框與圖示等細節 |
| [`typography`](skills/typography/SKILL.md) | 檢查字級、行高、換行、數字顯示及字型載入 |
| [`layout`](skills/layout/SKILL.md) | 檢查分組、對齊、間距、響應式版面及由右至左的文字方向 |
| [`accessibility`](skills/accessibility/SKILL.md) | 依照 WCAG 2.2 AA 檢查語意、焦點、鍵盤操作與觸控範圍 |
| [`interface-copy`](skills/interface-copy/SKILL.md) | 撰寫標籤、錯誤訊息、空白狀態與確認對話框的文案 |
| [`stress-test`](skills/stress-test/SKILL.md) | 找出介面在長文字、無資料、窄螢幕與錯誤狀態下的問題 |
| [`interface-review`](skills/interface-review/SKILL.md) | 檢視變更，依影響程度整理改善項目 |

## 安裝

安裝 Node.js 與 npm 後，進入專案目錄並執行對應工具的指令。頁面上方的指令可以讓你同時選擇要安裝的技能與使用的代理。 [Skills CLI 文件](https://www.skills.sh/docs/cli)

### Codex

```sh
npx skills add sik-limited/quark --agent codex
```

### Claude Code

```sh
npx skills add sik-limited/quark --agent claude-code
```

### Cursor

```sh
npx skills add sik-limited/quark --agent cursor
```

加上 `--skill quark-ui` 只安裝這個技能，加上 `--skill '*'` 則安裝全部技能。若要跨專案使用，請加上 `--global`。`q-clean` 與 `design-check` 會搭配其他 Quark 技能，建議安裝完整套件。

### 從 Quark 網站安裝

你可以從 [Quark UI 技能頁面](https://design.sik.limited/en/skills/quark-ui/)下載 `SKILL.md`，或直接將檔案網址交給 Skills CLI。連結頁面為英文。

```sh
npx skills add https://design.sik.limited/skills/quark-ui/SKILL.md
```

若要手動安裝，請將 `skills/` 中選定的技能資料夾複製到代理的技能目錄。使用 Claude 網頁版時，可以將技能資料夾壓縮成 ZIP 上傳。使用 ChatGPT 網頁版時，工作區必須支援技能上傳。 [Claude 說明](https://support.claude.com/en/articles/12512180-use-skills-in-claude) · [ChatGPT 說明](https://help.openai.com/en/articles/20001066-skills-in-chatgpt)

## 試用技能

在代理工具中指定要檢查的畫面，試試這個提示詞。

```text
請用 design-check 檢查這個設定畫面的行動版配置、鍵盤焦點與錯誤狀態，列出發現的問題並排定修正優先順序。
```

## DESIGN.md

選一份參考文件，以 `DESIGN.md` 為檔名儲存在專案根目錄，並請代理在開始修改介面前閱讀。這些是 Quark 根據公開設計系統與產品畫面整理的獨立參考資料。預覽頁面為英文。

| 設計參考 | 可參考的元素 | 連結 |
| --- | --- | --- |
| Claude | 暖色調、珊瑚色按鈕與編輯風格的排版 | [文件](designs/claude/DESIGN.md) · [預覽](https://design.sik.limited/en/themes/claude/) |
| KRDS | 公共服務介面、無障礙、表單與導覽 | [文件](designs/krds/DESIGN.md) · [預覽](https://design.sik.limited/en/themes/krds/) |
| Pinterest | 以圖片為主的內容探索、瀑布流格線與篩選器 | [文件](designs/pinterest/DESIGN.md) · [預覽](https://design.sik.limited/en/themes/pinterest/) |
| RIDI | 內容瀏覽、書封卡片與排行榜 | [文件](designs/ridi/DESIGN.md) · [預覽](https://design.sik.limited/en/themes/ridi/) |
| SEED | 語意色彩、格線與韓文 UX 文案 | [文件](designs/seed/DESIGN.md) · [預覽](https://design.sik.limited/en/themes/seed/) |
| Toss | 金融介面、行動裝置觸控操作，以及按鈕與輸入欄位的狀態 | [文件](designs/toss/DESIGN.md) · [預覽](https://design.sik.limited/en/themes/toss/) |
| Wanted (Montage) | 響應式格線、文字排版與共用元件狀態 | [文件](designs/wanted/DESIGN.md) · [預覽](https://design.sik.limited/en/themes/wanted/) |
| xAI | 深色介面、白色按鈕、細邊框與平面面板 | [文件](designs/xai/DESIGN.md) · [預覽](https://design.sik.limited/en/themes/xai/) |

## 授權

Quark 原創的技能與設計參考文件以 [MIT 授權條款](LICENSE)發布。品牌名稱、商標、字型與第三方素材的權利歸各權利人所有。
