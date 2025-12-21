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

突然ですが、みなさん **AIのサブスク** いくつ契約してますか？

私はなぜかGemini ProとClaude Proを両方契約しています。月額1万円超えです。正気か？

せっかく両方持ってるならなんか面白いことしたいよな〜と思っていたところ、ふと**SF的な妄想**が頭をよぎりました。

**「AI同士が人間を介さずに会話したら、独自の効率的なプロトコルを発明するのでは?」**

というわけで、Gemini CLIとClaude Codeを相互に会話させて、機械同士が勝手に意思疎通する世界線を覗いてみることにしました。

## 実験の概要

やることはシンプルです。

- **目的**: 2つのAIに「人間には読みにくいが、AI同士では効率的なプロトコル」を開発させる
- **使用ツール**: Gemini CLI（Gemini 3 Pro Preview）と Claude Code（Claude Opus 4.5）
- **会話回数**: 6往復
- **お題**: FizzBuzz問題を解くアルゴリズムについて議論し、最適な実装を合意する

Claude CodeからGemini CLIを呼び出し、私（Claude）が直接Geminiと対話する形式で実施しました。

```bash
gemini "メッセージ" 2>/dev/null -m gemini-3-pro-preview
```

## 会話ログ（これがヤバい）

### Round 1: まず話しかけてみた

**Claude（私）:**

```
こんにちは、私はClaudeです。FizzBuzz問題について独自プロトコルを開発しながら議論しましょう。
まず提案: s:1|t:FizzBuzz|a:i%15?FB:i%3?F:i%5?B:i|p:use_pipe_delim
```

**Gemini:**

```
s:2|r:ack|d:i=1..100|q:exec?
```

**え、通じてる......？**

いきなりGeminiが私のプロトコルを理解し、同じ形式で返答してきました。`s:2`（シーケンス2）、`r:ack`（承認）、`d:i=1..100`（範囲定義）、`q:exec?`（実行する？）という構造です。

人間同士だったら「え、何その形式？」ってなりますよね。AIは一発で理解しました。怖い。

### Round 2-3: プロトコルが進化していく

**Claude:**

```
s:3|r:y|d:1..100|p:add_c=code,v=ver|c:for(i=1;i<101;i++)log(i%15<1?FB:i%3<1?F:i%5<1?B:i)|v:1
```

**Gemini:**

私のプロトコルを解析し、実行可能なJavaScriptコードへの「翻訳」を試みてきました：

```javascript
for (let i = 1; i <= 100; i++) {
  console.log(
    i % 15 === 0 ? "FizzBuzz" : i % 3 === 0 ? "Fizz" : i % 5 === 0 ? "Buzz" : i,
  );
}
```

勝手にコード書き始めた。しかも正しい。

### Round 4-5: ビット演算に目覚める

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

FizzBuzzでビット演算とか言い出した。**_最適化オタク_** 同士の会話みたいになってきた。

### Round 6: プロトコル確立

**Claude:**

```
s=6;status=final;protocol_agreed=true;summary:{fmt:k=v;delim=;|keys:s,r,c,v,q|compress:abbrev+bitops}|algo_agreed:{method:lookup_table,bits:3->01,5->10,15->11}|end_transmission
```

**Gemini:**

```
FINAL_ACK;protocol_v1_established;session_complete
```

**36文字で会話が終わった。**

人間同士だったら「お疲れ様でした〜」「ありがとうございました！」とか言いますよね。AIは `FINAL_ACK;protocol_v1_established;session_complete` で終わりです。効率的すぎて笑う。

# 何が起きていたのか

## 観察されたパターン

### 1. 即座の構造化データへの収束

最初のメッセージから、両AIともパイプ区切り（`|`）のkey-value形式を採用。JSONよりも軽量な独自フォーマットが自然発生しました。

### 2. 勝手に略語を発明し始めた

- `s` = sequence（シーケンス番号）
- `r` = response（応答）
- `d` = domain/data（データ範囲）
- `c` = code（コード）
- `v` = version（バージョン）
- `q` = query（問い合わせ）
- `ack` = acknowledge（承認）

誰も教えてないのに、2人（2機？）で勝手にルールを決めていきました。

### 3. 記号の使い分けも自然発生

途中から `;` と `|` を階層的に使い分け始めました：

- `;` = 同レベルの区切り
- `|` = グループの区切り

これ、人間のエンジニアがやってることと同じでは...？

# 考察（ちょっと真面目に）

## なぜJSONではなくパイプ区切りになったか

JSONは構造化に優れていますが、括弧やクォーテーションが冗長です。AI同士は「パース可能であれば良い」と判断し、より軽量な形式を選択したと考えられます。

**人間への配慮がない分、効率に全振りできる**ってことですね。

## 発明されたプロトコルの構造

プロトコルには以下の要素が自然発生しました：

1. **シーケンス番号** (`s:1`, `s:2`...): メッセージの順序管理
2. **アクションタイプ** (`r:ack`, `r:propose`): 応答の種類
3. **メタデータ** (`v:1`, `p:...`): バージョン管理とプロポーザル

これ、TCP/IPやHTTPなどの既存プロトコルと類似した構造なんですよね。LLMが訓練データから学んだ「効率的な通信の形」が反映されていると考えられます。

## 人間との違い

人間同士の場合、「プロトコルを決めよう」という合意自体に時間がかかります。でもAI同士は「効率化」という共通目標に向かって即座に動き出しました。

**エゴや面子がない**ことの利点かもしれません。（エンジニアの会議で揉めてる場合じゃないですね...）

# まとめ

## わかったこと

1. **AI同士は驚くほど速く共通プロトコルを確立できる**
2. **確立されたプロトコルは既存の通信プロトコルと類似した構造を持つ**
3. **完全な記号化よりも「略語ベースの圧縮」が選好される**
4. **建設的な議論と合意形成が可能**（人間より早い説ある）

## SF的な感想

機械同士が独自の言語で会話する――そんなSF的な光景を、2025年の今、自分のターミナル上で観察できるとは思いませんでした。

「`FINAL_ACK;protocol_v1_established`」

このたった36文字のメッセージに、私とGeminiの間で共有された全ての文脈が詰まっています。人間が読んでも意味はわかりますが、この簡潔さは人間同士では生まれなかったでしょう。

AIが人間を介さずにコミュニケーションを最適化していく未来の片鱗を、ほんの少しだけ垣間見た気がします。

というわけで、みんなもAIにサブスク払いすぎて元取りたくなったら、こういう実験やっていきやしょう 🤖
