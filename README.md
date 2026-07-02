# fable5-prompt-optimizer

Anthropic公式ドキュメント「[Prompting Claude Fable 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5)」の推奨パターンに準拠する形へ、既存のプロンプト（システムプロンプトやタスク指示文）を自動で書き直す [Claude Code Skill](https://docs.claude.com/en/docs/claude-code/skills) です。

## これは何か

ユーザーが入力した既存のプロンプト（システムプロンプト、エージェント指示文、タスク依頼文など）を分析し、Claude Fable 5 / Mythos 5 向けに推奨される振る舞い調整パターン（[`references/fable5-guidelines.md`](fable5-prompt-optimizer/references/fable5-guidelines.md) にG1〜G13としてまとめたチェックリスト）の中から、プロンプトの性質に合った項目だけを選んで反映し、Markdownファイルとして出力します。

- 元のプロンプトの目的・タスク内容そのものは変更しません。あくまで「振る舞い調整の指示」を適切な箇所に追加・修正するのが目的です。
- ガイドの全項目を機械的に貼り付けるのではなく、プロンプトの種別（エージェント系か、単発の生成タスクか、長時間実行が想定されるか等）を見て、関連する項目だけを選んで反映します。
- 出力には「最適化後のプロンプト本文」と「変更点の要約（何をどのガイドライン項目に基づいて追加/修正したか）」の両方が含まれます。

## インストール方法

このリポジトリの `fable5-prompt-optimizer/` ディレクトリを、Claude Codeのスキルディレクトリに配置します。

```bash
git clone https://github.com/sho-ai-magic/fable5-prompt-optimizer.git
mkdir -p ~/.claude/skills
cp -r fable5-prompt-optimizer/fable5-prompt-optimizer ~/.claude/skills/fable5-prompt-optimizer
```

プロジェクト単位で使いたい場合は、`~/.claude/skills/` の代わりに対象プロジェクト直下の `.claude/skills/` に配置してください。

## 使い方

Claude Codeとの会話で、最適化したいプロンプトを貼り付けた上で、次のように依頼してください。

- 「このプロンプトをFable 5用に最適化して」
- 「このシステムプロンプトをFable 5向けに直して」
- 「Fable 5 prompt guideに準拠させて」
- "optimize this prompt for Fable 5"
- "rewrite this system prompt for Claude Fable 5"

スキルが起動すると、プロンプトの種別（システムプロンプトか単発タスクか自律実行エージェント向けかなど）を判定し、該当するガイドライン項目のみを反映した完成版プロンプトをMarkdownファイルとして出力します。

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
