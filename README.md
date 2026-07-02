日本語 | [English](README.en.md)

# fable5-prompt-optimizer

「〇〇をしたい」というやりたいことを伝えるだけで、Anthropic公式ドキュメント「[Prompting Claude Fable 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5)」の推奨パターンに準拠したプロンプトを作ってくれる [Claude Skill](https://docs.claude.com/en/docs/claude-code/skills) です。すでに書いたプロンプトがあれば、それをガイド準拠の形に書き直すこともできます。[claude.ai（チャット）](https://claude.ai)・[Claude Code](https://docs.claude.com/en/docs/claude-code) の両方で利用できます。

## 概要説明

次の2つの入力に対応しています。

- **やりたいことの説明のみ**（例:「返品対応をするカスタマーサポートのチャットボット用プロンプトを作って」）→ 目的を満たすプロンプトをゼロから起草し、Fable 5向けの推奨パターンを反映して仕上げます。
- **既存のプロンプト**（システムプロンプト、エージェント指示文、タスク依頼文など）→ 内容や意図はそのままに、Fable 5 / Mythos 5 向けに推奨される振る舞い調整パターンを反映して書き直します。

どちらの場合も、Claude Fable 5 / Mythos 5 向けの振る舞い調整パターン（[`references/fable5-guidelines.md`](fable5-prompt-optimizer/references/fable5-guidelines.md) にG1〜G13としてまとめたチェックリスト）の中から、プロンプトの性質に合った項目だけを選んで反映し、Markdownファイルとして出力します。

- ユーザーが伝えた目的・元のプロンプトの内容そのものは変更しません。あくまで「振る舞い調整の指示」を適切な箇所に追加・修正するのが目的です。
- ガイドの全項目を機械的に貼り付けるのではなく、プロンプトの種別（エージェント系か、単発の生成タスクか、長時間実行が想定されるか等）を見て、関連する項目だけを選んで反映します。
- 出力には「最終プロンプト本文」と「変更点の要約（何をどのガイドライン項目に基づいて追加/修正したか）」の両方が含まれます。

## インストール方法

### Claude Codeで使う場合

このリポジトリの `fable5-prompt-optimizer/` ディレクトリを、Claude Codeのスキルディレクトリに配置します。

```bash
git clone https://github.com/sho-ai-magic/fable5-prompt-optimizer.git
mkdir -p ~/.claude/skills
cp -r fable5-prompt-optimizer/fable5-prompt-optimizer ~/.claude/skills/fable5-prompt-optimizer
```

プロジェクト単位で使いたい場合は、`~/.claude/skills/` の代わりに対象プロジェクト直下の `.claude/skills/` に配置してください。

### claude.ai（チャット）で使う場合

claude.aiの「設定」→「カスタマイズ」→「スキル」から `fable5-prompt-optimizer/` フォルダをアップロードしてください（[スキルの作成方法](https://support.claude.com/en/articles/12512180)を参照）。

## 使い方

Claude Code・claude.aiどちらでも同じ発話で起動します。

**やりたいことを伝えて新規作成する場合**（プロンプト本文なしでOK）

- 「返品対応をするカスタマーサポートのチャットボット用プロンプトを、Fable 5向けに作って」
- 「一晩中動かすコーディングエージェント用のプロンプトを、Fable 5用に作って」
- "create a Fable 5 prompt for a customer support chatbot that handles returns"

**すでに書いたプロンプトを書き直す場合**（プロンプト本文を貼り付け）

- 「このプロンプトをFable 5用に最適化して」
- 「このシステムプロンプトをFable 5向けに直して」
- 「Fable 5 prompt guideに準拠させて」
- "optimize this prompt for Fable 5"
- "rewrite this system prompt for Claude Fable 5"

スキルが起動すると、まず入力が「やりたいことの説明」か「既存プロンプト」かを判定します。前者の場合は目的を満たすプロンプトをゼロから起草し、後者の場合はその内容をそのまま活かします。その上でプロンプトの種別（システムプロンプトか単発タスクか自律実行エージェント向けかなど）を判定し、該当するガイドライン項目のみを反映した完成版プロンプトをMarkdownファイルとして出力します。

## ガイドライン項目一覧（G1〜G13）

`references/fable5-guidelines.md` に定義されている各チェック項目の概要です。プロンプトの種別に応じて、該当する項目だけが自動で選ばれます。

| # | 項目名 | 概要 |
|---|---|---|
| G1 | 全体方針 | Fable 5は旧モデルより高難度・長時間のタスクを任せられる。控えめすぎる指示になっていないか確認する前提の項目（直接プロンプトに追記するものではない） |
| G2 | 過剰計画の抑制 | 自律実行・長時間タスクで、曖昧な状況でも選択肢を検討し続けず「十分な情報が揃ったら行動に移る」よう促す |
| G3 | 簡潔な指示への集約 | 指示追従性の高さを活かし、個別の禁止事項を並べる代わりに「結論から述べる」「読みやすさを優先しつつ余計な情報を削る」という短い方針で冗長な出力を抑える |
| G4 | 事実に基づく進捗報告 | 長時間の自律実行中に、実際には確認していない完了報告をしないよう、ツール実行結果と照合してから報告させる |
| G5 | 越権行為の防止 | メール送信やシステム状態変更など、頼まれていない行動を自発的に取らないよう境界線を明示する |
| G6 | サブエージェントの並列活用 | 独立したサブタスクをサブエージェントに委任し、並列処理を積極的に行わせる |
| G7 | セッションをまたぐメモリ | 複数セッションにまたがる自律エージェント向けに、学んだ教訓をファイルへ記録・参照させる |
| G8 | 早期停止・過剰確認の防止 | 完全自律実行のパイプラインで、宣言だけして実行を止めたり不要な許可確認で停止したりしないよう促す |
| G9 | 対話的プロンプトでの確認ポイント明確化 | 破壊的操作やスコープ変更時のみ確認を求め、それ以外は自律的に進めさせる |
| G10 | コンテキスト予算への言及回避 | 残りトークン数などが見えるハーネスで、残量を理由に作業を切り詰めないよう促す |
| G11 | 意図・背景の伝達 | タスクの背景・目的（何のための作業で、成果物は誰のためのものか）を冒頭で伝え、精度を上げる |
| G12 | 最終報告の読みやすさ | 作業中の省略記法をそのまま流用せず、ユーザー向けの最終報告は完全な文で分かりやすく書き直させる |
| G13 | 途中経過をユーザーに届けるツール設計 | ハーネス開発者向け。ターンを終えずに成果物を届ける専用ツールの使い分けを明示する（該当する場合のみ） |

## ファイル構成

```
fable5-prompt-optimizer/
├── SKILL.md                          # スキル本体の定義・手順
└── references/
    └── fable5-guidelines.md          # G1〜G13のガイドラインチェックリスト
```

## 参考

このスキルはAnthropic公式ドキュメント「[Prompting Claude Fable 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5)」の推奨パターンを独自に要約・整理したものに基づいています。原文の逐語引用ではなく、要点を再構成した独自のチェックリストです。

## ライセンス

[MIT License](LICENSE)
