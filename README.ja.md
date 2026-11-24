<p align="center"><img src="assets/quark-symbol.svg" width="72" height="72" alt=""></p>

<h1 align="center">Quark</h1>

<p align="center">
  <a href="https://skills.sh/sik-limited/quark"><img src="assets/skills-sh-badge.svg" width="136" height="20" alt="skills.sh"></a>
</p>

<p align="center"><a href="README.md">English</a> · <a href="README.ko.md">한국어</a> · <strong>日本語</strong> · <a href="README.zh-TW.md">繁體中文</a></p>

<p align="center">Codex、Claude Code、Cursorでエージェンティックデザインを行うための、オープンソースの<a href="https://agentskills.io/">Agent Skills</a>と<code>DESIGN.md</code>リファレンスです。</p>

<p align="center"><a href="https://design.sik.limited/"><strong>Quarkのウェブサイト</strong></a> · UIコンポーネントとデザイントークンをプレビュー</p>

```sh
npx skills add sik-limited/quark
```

## スキル

| スキル | 用途 |
| --- | --- |
| [`quark-ui`](skills/quark-ui/SKILL.md) | QuarkのコンポーネントとデザイントークンでUIを作る |
| [`design-to-code`](skills/design-to-code/SKILL.md) | スクリーンショット、Figmaフレーム、デザイン仕様をコードに落とし込む |
| [`design-check`](skills/design-check/SKILL.md) | 既存のUIを点検し、修正の優先順位を付ける |
| [`q-clean`](skills/q-clean/SKILL.md) | UIをコンポーネントに整理し、状態管理、モーション、レスポンシブ表示、アクセシビリティの問題を修正する |
| [`component-api`](skills/component-api/SKILL.md) | 複数のフレームワークで使えるコンポーネントのプロパティ、状態、合成方法を設計する |
| [`design-tokens`](skills/design-tokens/SKILL.md) | 色、余白、角丸、シャドウ、モーション、テーマのトークンを定義する |
| [`animate`](skills/animate/SKILL.md) | トランジション、スプリング、ジェスチャーを実装し、モーションの低減設定にも対応する |
| [`interface-polish`](skills/interface-polish/SKILL.md) | 角丸、視覚的な位置合わせ、枠線、アイコンの細部を整える |
| [`typography`](skills/typography/SKILL.md) | 文字サイズ、行間、折り返し、数字の表示、フォントの読み込みを点検する |
| [`layout`](skills/layout/SKILL.md) | グループ化、配置、余白、レスポンシブレイアウト、右から左への表示を点検する |
| [`accessibility`](skills/accessibility/SKILL.md) | WCAG 2.2 AAに沿って、セマンティクス、フォーカス、キーボード操作、タッチ領域を確認する |
| [`interface-copy`](skills/interface-copy/SKILL.md) | ラベル、エラー、空の状態、確認ダイアログの文言を書く |
| [`stress-test`](skills/stress-test/SKILL.md) | 長文、データなし、狭い画面、エラー状態でUIが崩れる箇所を探す |
| [`interface-review`](skills/interface-review/SKILL.md) | 変更内容をレビューし、影響の大きさに応じて改善点を整理する |

## インストール

Node.jsとnpmをインストールしたうえで、プロジェクトのディレクトリから使用するエージェントのコマンドを実行します。ページ上部のコマンドでは、スキルとエージェントの両方を選べます。 [Skills CLIのドキュメント](https://www.skills.sh/docs/cli)

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

`--skill quark-ui`を付けるとそのスキルだけを、`--skill '*'`を付けるとすべてのスキルをインストールできます。複数のプロジェクトで使う場合は`--global`を付けてください。`q-clean`と`design-check`は他のQuarkスキルも使うため、一括インストールをおすすめします。

### Quarkのウェブサイトからインストール

[Quark UIのスキルページ](https://design.sik.limited/en/skills/quark-ui/)から`SKILL.md`をダウンロードするか、ファイルのURLをSkills CLIに直接渡せます。リンク先は英語です。

```sh
npx skills add https://design.sik.limited/skills/quark-ui/SKILL.md
```

手動でインストールする場合は、`skills/`から目的のスキルフォルダをエージェントのスキルディレクトリにコピーしてください。Web版ClaudeではスキルフォルダをZIPにしてアップロードできます。Web版ChatGPTでは、ワークスペースがスキルのアップロードに対応している場合に利用できます。 [Claudeヘルプ](https://support.claude.com/en/articles/12512180-use-skills-in-claude) · [ChatGPTヘルプ](https://help.openai.com/en/articles/20001066-skills-in-chatgpt)

## スキルを試す

エージェントに確認したい画面を指定し、次のように依頼してください。

```text
design-checkを使って、この設定画面のモバイルレイアウト、キーボードフォーカス、エラー時の表示を確認して。見つかった問題と修正の優先順位を教えて。
```

## DESIGN.md

参考にする文書を選び、プロジェクトのルートに`DESIGN.md`として保存します。UIの作業を始める前に、エージェントにそのファイルを読むよう指示してください。Quarkが公開デザインシステムと実際の製品画面をもとにまとめた独自の参考資料です。プレビューは英語です。

| リファレンス | 参考にできる要素 | リンク |
| --- | --- | --- |
| Claude | 温かみのある配色、コーラルのボタン、エディトリアルなタイポグラフィ | [文書](designs/claude/DESIGN.md) · [プレビュー](https://design.sik.limited/en/themes/claude/) |
| KRDS | 公共サービスのUI、アクセシビリティ、フォーム、ナビゲーション | [文書](designs/krds/DESIGN.md) · [プレビュー](https://design.sik.limited/en/themes/krds/) |
| Pinterest | 画像を中心とした探索、メイソンリーグリッド、フィルター | [文書](designs/pinterest/DESIGN.md) · [プレビュー](https://design.sik.limited/en/themes/pinterest/) |
| RIDI | コンテンツの閲覧、書影カード、ランキング | [文書](designs/ridi/DESIGN.md) · [プレビュー](https://design.sik.limited/en/themes/ridi/) |
| SEED | セマンティックカラー、グリッド、韓国語のUXライティング | [文書](designs/seed/DESIGN.md) · [プレビュー](https://design.sik.limited/en/themes/seed/) |
| Toss | 金融サービスのUI、モバイルのタッチ操作、ボタンと入力欄の状態 | [文書](designs/toss/DESIGN.md) · [プレビュー](https://design.sik.limited/en/themes/toss/) |
| Wanted (Montage) | レスポンシブグリッド、タイポグラフィ、共通コンポーネントの状態 | [文書](designs/wanted/DESIGN.md) · [プレビュー](https://design.sik.limited/en/themes/wanted/) |
| xAI | ダークUI、白いボタン、細い枠線、フラットなパネル | [文書](designs/xai/DESIGN.md) · [プレビュー](https://design.sik.limited/en/themes/xai/) |

## ライセンス

Quarkが作成したスキルとデザインリファレンスは、[MITライセンス](LICENSE)で公開しています。ブランド名、商標、フォント、第三者の資料に関する権利は、それぞれの権利者に帰属します。
