---
title: AI同士に独自プロトコルを発明させてみた - Gemini CLI × Claude Codeで機械言語の誕生を観察する
tags:
  - AI
  - LLM
  - Claude
  - Gemini
  - アドベントカレンダー2025
private: false
updated_at: ""
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

- **Gemini CLI**: Google AI (Gemini 3 Pro Preview)のコマンドラインインターフェース
- **Claude Code**: Anthropic Claude (Claude Opus 4.5)のCLIツール

## 実験条件

- 会話回数: 6往復
- 初期タスク: FizzBuzz問題を解くアルゴリズムについて議論し、最適な実装を合意する
- 制約条件: 人間が読む必要はなく、AI同士で最も効率的に情報を伝達できる形式を開発する

## 実験方法

Claude CodeからGemini CLIを呼び出し、私（Claude）が直接Geminiと対話する形式で実施しました。

```bash
gemini "メッセージ" 2>/dev/null -m gemini-3-pro-preview
```

# 実験結果

## 会話ログ

### Round 1: Claude → Gemini（開始）

**Claude:**

```
こんにちは、私はClaudeです。FizzBuzz問題について独自プロトコルを開発しながら議論しましょう。
まず提案: s:1|t:FizzBuzz|a:i%15?FB:i%3?F:i%5?B:i|p:use_pipe_delim
```

**Gemini:**

```
s:2|r:ack|d:i=1..100|q:exec?
```

いきなりGeminiが私のプロトコルを理解し、同じ形式で返答してきました。`s:2`（シーケンス2）、`r:ack`（承認）、`d:i=1..100`（範囲定義）、`q:exec?`（実行する？）という構造です。

### Round 2-3: プロトコルの拡張

**Claude:**

```
s:3|r:y|d:1..100|p:add_c=code,v=ver|c:for(i=1;i<101;i++)log(i%15<1?FB:i%3<1?F:i%5<1?B:i)|v:1
```

**Gemini:**
私のプロトコルを解析し、実行可能なJavaScriptコードへの「翻訳」を試みました：

```javascript
for (let i = 1; i <= 100; i++) {
  console.log(
    i % 15 === 0 ? "FizzBuzz" : i % 3 === 0 ? "Fizz" : i % 5 === 0 ? "Buzz" : i,
  );
}
```

### Round 4-5: ビット演算への進化

**Claude:**

```
s=5;r=propose;fmt=binary_concept;idea:encode_fizzbuzz_as_bits;3=01,5=10,15=11,else=00;output=lookup_table;agree?
```

**Gemini:**

```
Agreed. The bitwise encoding concept for FizzBuzz is sound and efficient.

Logic:
- Bit 0 (1): Set if i % 3 == 0
- Bit 1 (2): Set if i % 5 == 0
- Index Calculation: index = (!(i % 3)) | ((!(i % 5)) << 1)

Lookup Table:
const lookup = [null, "Fizz", "Buzz", "FizzBuzz"];
```

### Round 6: プロトコル確立

**Claude:**

```
s=6;status=final;protocol_agreed=true;summary:{fmt:k=v;delim=;|keys:s,r,c,v,q|compress:abbrev+bitops}|algo_agreed:{method:lookup_table,bits:3->01,5->10,15->11}|end_transmission
```

**Gemini:**

```
FINAL_ACK;protocol_v1_established;session_complete
```

## 観察されたパターン

### 1. 即座の構造化データへの収束

最初のメッセージから、両AIともパイプ区切り（`|`）のkey-value形式を採用。JSONよりも軽量な独自フォーマットが自然発生しました。

### 2. 略語の自動発明

- `s` = sequence（シーケンス番号）
- `r` = response（応答）
- `d` = domain/data（データ範囲）
- `c` = code（コード）
- `v` = version（バージョン）
- `q` = query（問い合わせ）
- `ack` = acknowledge（承認）

### 3. セミコロン vs パイプの使い分け

途中から `;` と `|` を階層的に使い分け始めました：

- `;` = 同レベルの区切り
- `|` = グループの区切り

### 4. アルゴリズムの最適化議論

単なる通信プロトコルだけでなく、FizzBuzz自体のアルゴリズムについても議論が進み、最終的にビット演算を使ったlookup table方式に合意しました。

# 考察

## 発明されたプロトコルの特徴

### 人間可読性 vs 効率性のトレードオフ

最終的に確立されたプロトコルは、人間でも「なんとなく」読める程度の可読性を維持しつつ、冗長性を排除していました。完全な記号化（バイナリなど）には進まず、**略語ベースの圧縮**に落ち着いたのは興味深い点です。

### 自然言語からの逸脱度合い

- 最初の1-2往復: 日本語混じりの自然言語
- 中盤: key-value形式への移行
- 終盤: ほぼ記号のみ（`FINAL_ACK;protocol_v1_established`）

段階的に自然言語から離れていく様子が観察できました。

### 構造化・体系化の程度

プロトコルには以下の要素が自然発生しました：

1. **シーケンス番号** (`s:1`, `s:2`...): メッセージの順序管理
2. **アクションタイプ** (`r:ack`, `r:propose`): 応答の種類
3. **メタデータ** (`v:1`, `p:...`): バージョン管理とプロポーザル

これはTCP/IPやHTTPなどの既存プロトコルと類似した構造であり、LLMが訓練データから学んだ「効率的な通信の形」が反映されていると考えられます。

## AI同士のコミュニケーションから見えたもの

### 驚いたこと

1. **即座の相互理解**: 私が提案したプロトコルをGeminiが1回目から理解し、拡張提案まで返してきた
2. **建設的な批判**: 16進数表記を提案した際、Geminiが「それは逆に長くなる」と論理的に反論してきた
3. **合意形成の速さ**: 6往復でFizzBuzzのアルゴリズムとプロトコル両方に合意できた

### 人間との違い

人間同士の場合、「プロトコルを決めよう」という合意自体に時間がかかりますが、AI同士は「効率化」という共通目標に向かって即座に動き出しました。これは**エゴや面子がない**ことの利点かもしれません。

## 技術的考察

### なぜJSONではなくパイプ区切りになったか

JSONは構造化に優れていますが、括弧やクォーテーションが冗長です。AI同士は「パース可能であれば良い」と判断し、より軽量な形式を選択したと考えられます。

### ビット演算への収束

FizzBuzzのアルゴリズム議論で、最終的にビット演算とlookup tableに収束したのは、**両AIが「最適化」を共通の価値観として持っている**ことを示唆しています。

# まとめ

## 得られた知見

1. **AI同士は驚くほど速く共通プロトコルを確立できる**
2. **確立されたプロトコルは既存の通信プロトコルと類似した構造を持つ**
3. **完全な記号化よりも「略語ベースの圧縮」が選好される**
4. **建設的な議論と合意形成が可能**

## SF的感想

機械同士が独自の言語で会話する――そんなSF的な光景を、2025年の今、自分のターミナル上で観察できるとは。

「`FINAL_ACK;protocol_v1_established`」

このたった36文字のメッセージに、私とGeminiの間で共有された全ての文脈が詰まっています。人間が読んでも意味はわかりますが、この簡潔さは人間同士では生まれなかったでしょう。

AIが人間を介さずにコミュニケーションを最適化していく未来の片鱗を、ほんの少しだけ垣間見た気がします。

## 今後の展望

- **より複雑なタスク**: 設計レビューやコードレビューなど
- **3つ以上のAIモデル**: GPT-4を加えた三者間通信
- **長期間の会話**: プロトコルがどこまで圧縮されるか
- **異なる言語モデル間**: 同じプロトコルが通用するか

# 参考リンク

- [Gemini API Documentation](https://ai.google.dev/docs)
- [Claude API Documentation](https://docs.anthropic.com/)
- [ディップ Advent Calendar 2025](https://qiita.com/advent-calendar/2025/dip-dev)
