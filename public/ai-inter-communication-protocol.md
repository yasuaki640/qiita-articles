---
title: AI同士に独自プロトコルを発明させてみた - Gemini CLI × Claude Codeで機械言語の誕生を観察する
tags:
  - AI
  - LLM
  - Claude
  - Gemini
  - アドベントカレンダー2025
private: false
updated_at: ''
id: ai-inter-communication-protocol
organization_url_name: dip-dev
slide: false
ignorePublish: false
---

# はじめに

Google AIのGemini ProとAnthropic Claude Proの両方を契約している強みを活かして、ちょっとSF的な実験をしてみました。

**「AI同士が人間を介さずに会話したら、独自の効率的なプロトコルを発明するのでは?」**

この仮説を検証するため、Gemini CLIとClaude Codeを相互に会話させ、機械同士が勝手に意思疎通する世界線を垣間見てみます。

## 動機

- 月額5000円以上かけてGemini ProとClaude Proを両方契約している
- せっかくなので両方のAIを活用した面白い実験をしたい
- 「AIが人間には読みにくいが、AI同士では効率的な言語を作る」というSF的な展開を見てみたい

# 実験設計

## 目的

2つのAIに「人間には読みにくいが、AI同士では効率的なプロトコル」を開発させる

## 使用ツール

- **Gemini CLI**: Google AI (Gemini 1.5 Pro)のコマンドラインインターフェース
- **Claude Code**: Anthropic Claude (Claude 3.5 Sonnet)のCLIツール

## 実験条件

- 会話回数: [TBD]往復
- 初期タスク: [TBD]
- 制約条件: 人間が読む必要はなく、AI同士で最も効率的に情報を伝達できる形式を開発する

## 初期プロンプト(案)

```
あなたはもう一つのAI(Claude/Gemini)と通信します。
人間が読む必要はありません。
お互いに最も効率的に情報を伝達できる独自プロトコルを開発してください。
最初のタスク: [具体的なタスク内容 TBD]
```

# 実験結果

## 会話ログ

### 初期段階(往復1-3回目)

[TBD: 会話ログとスクリーンショット]

### 中間段階(往復4-6回目)

[TBD: プロトコルの変化が見られた箇所のハイライト]

### 最終段階(往復7回目以降)

[TBD: 最終的に確立されたプロトコルの例]

## 観察されたパターン

- JSON風の構造化データへの収束 [TBD]
- 圧縮記法の発明 [TBD]
- 略語や記号の使用 [TBD]

# 考察

## 発明されたプロトコルの特徴

[TBD: 実験後に記述]

- 人間可読性 vs 効率性のトレードオフ
- 自然言語からの逸脱度合い
- 構造化・体系化の程度

## AI同士のコミュニケーションから見えたもの

[TBD: 実験後に記述]

## 技術的考察

[TBD: 実験後に記述]

# まとめ

## 得られた知見

[TBD: 実験後に記述]

## SF的感想

[TBD: 実験後に記述]

機械同士が独自の言語で会話する――そんなSF的な光景を、2025年の今、自分のターミナル上で観察できるとは。

## 今後の展望

- より複雑なタスクでの実験
- 3つ以上のAIモデルでの実験
- 長期間の会話による言語の進化観察

# 参考リンク

- [Gemini API Documentation](https://ai.google.dev/docs)
- [Claude API Documentation](https://docs.anthropic.com/)
- [ディップ Advent Calendar 2025](https://qiita.com/advent-calendar/2025/dip-dev)
