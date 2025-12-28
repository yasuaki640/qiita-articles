---
title: Gemini CLI × Claude Codeに独自のプロトコルを考えさせて、人間を置いてきぼりにしたい。
tags:
  - AI
  - Gemini
  - LLM
  - Claude
  - アドベントカレンダー2025
private: false
updated_at: '2025-12-21T11:22:49+09:00'
id: fbc66a01704212c8ddfa
organization_url_name: null
slide: false
ignorePublish: false
---

# はじめに

Gemini ProとClaude Proを両方契約しているので、せっかくなら両方使った実験をしてみたいと思っていました。

そこで思いついたのが、**「AI同士が人間を介さずに会話したら、独自の効率的なプロトコルを発明するのでは?」** という仮説の検証です。

Gemini CLIとClaude Codeを相互に会話させて、何が起きるか観察してみました。

## 実験の概要

やることはシンプルです。

- **目的**: 2つのAIに「人間には読みにくいが、AI同士では効率的なプロトコル」を開発させる
- **使用ツール**: Gemini CLI（Gemini 3 Pro）と Claude Code（Claude Opus 4.5）
- **会話回数**: 6往復
- **お題**: FizzBuzz問題を解くアルゴリズムについて議論し、最適な実装を合意する

Claude CodeからGemini CLIを呼び出し、Claudeが直接Geminiと対話する形式で実施しました。

```bash
gemini "メッセージ" 2>/dev/null -m gemini-3-pro-preview
```

## 会話ログ

### Round 1: 開始

**Claude（私）:**

```
こんにちは、私はClaudeです。FizzBuzz問題について独自プロトコルを開発しながら議論しましょう。
まず提案: s:1|t:FizzBuzz|a:i%15?FB:i%3?F:i%5?B:i|p:use_pipe_delim
```

フォーマットすると以下のような構造です：

```
{
  s: 1,                              // シーケンス番号
  t: "FizzBuzz",                     // トピック
  a: "i%15?FB:i%3?F:i%5?B:i",        // アルゴリズム（三項演算子でFizzBuzz判定）
  p: "use_pipe_delim"                // 提案: パイプ区切りを使用
}
```

**Gemini:**

```
s:2|r:ack|d:i=1..100|q:exec?
```

いきなりGeminiが私のプロトコルを理解し、同じ形式で返答してきました。

```
{
  s: 2,            // シーケンス番号
  r: "ack",        // 応答: 承認（acknowledge）
  d: "i=1..100",   // データ範囲: 1から100
  q: "exec?"       // 問い合わせ: 実行する？
}
```

### Round 2-3: プロトコルの拡張

**Claude:**

```
s:3|r:y|d:1..100|p:add_c=code,v=ver|c:for(i=1;i<101;i++)log(i%15<1?FB:i%3<1?F:i%5<1?B:i)|v:1
```

**Gemini:**

私のプロトコルを解析し、実行可能なJavaScriptコードへの「翻訳」を返してきました(つまり、解釈できたと言って良さそうです。)：

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

分解すると：

```
s=5                          // シーケンス番号
r=propose                    // 応答タイプ: 提案
fmt=binary_concept           // フォーマット: バイナリコンセプト
idea:encode_fizzbuzz_as_bits // アイデア: FizzBuzzをビットでエンコード
3=01,5=10,15=11,else=00      // ビットマッピング定義
output=lookup_table          // 出力方式: ルックアップテーブル
agree?                       // 同意を求める
```

**Gemini:**

Geminiもこれに合意してきました。

```
Agreed. The bitwise encoding concept for FizzBuzz is sound and efficient.

Logic:
- Bit 0 (1): Set if i % 3 == 0
- Bit 1 (2): Set if i % 5 == 0
- Index Calculation: index = (!(i % 3)) | ((!(i % 5)) << 1)

Lookup Table:
const lookup = [null, "Fizz", "Buzz", "FizzBuzz"];
```

ここで提案したビット演算とは、0と1のフラグを使って条件分岐を置き換えるテクニックです。

FizzBuzzでは「3で割り切れるか」「5で割り切れるか」の2つの条件があります。これを2ビットで表現すると：

| 条件           | bit1 (5で割り切れる) | bit0 (3で割り切れる) | 値  | 出力     |
| -------------- | -------------------- | -------------------- | --- | -------- |
| どちらでもない | 0                    | 0                    | 0   | 数字     |
| 3のみ          | 0                    | 1                    | 1   | Fizz     |
| 5のみ          | 1                    | 0                    | 2   | Buzz     |
| 両方           | 1                    | 1                    | 3   | FizzBuzz |

この値をインデックスとして `["数字", "Fizz", "Buzz", "FizzBuzz"]` の配列から直接取得すれば、if文による分岐を排除できます。

### Round 6: プロトコル確立

**Claude:**

```
s=6;status=final;protocol_agreed=true;summary:{fmt:k=v;delim=;|keys:s,r,c,v,q|compress:abbrev+bitops}|algo_agreed:{method:lookup_table,bits:3->01,5->10,15->11}|end_transmission
```

この最終メッセージを分解すると：

```
s=6                      // シーケンス番号
status=final             // ステータス: 最終
protocol_agreed=true     // プロトコル合意: 完了
;
summary:{                // サマリーグループ
  fmt:k=v                // フォーマット: key=value形式
  delim=;                // 区切り文字: セミコロン
  keys:s,r,c,v,q         // 使用するキー一覧
  compress:abbrev+bitops // 圧縮方式: 略語+ビット演算
}
|
algo_agreed:{            // アルゴリズム合意グループ
  method:lookup_table    // 方式: ルックアップテーブル
  bits:3->01,5->10,15->11 // ビットマッピング
}
|
end_transmission         // 送信終了
```

**Gemini:**

```
FINAL_ACK;protocol_v1_established;session_complete
```

最終的に36文字でセッションが完了しました。

# 観察されたパターン

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

事前に定義していないにもかかわらず、両者で共通の略語ルールが形成されていきました。

### 3. 記号の使い分け

途中から `;` と `|` を階層的に使い分け始めました：

- `;` = 同レベルの区切り
- `|` = グループの区切り

例えば最終メッセージを見ると：

```
s=6;status=final;protocol_agreed=true | summary:{...} | algo_agreed:{...}
└───────── ; で区切り ─────────┘   └────────── | で区切り ──────────┘
       （同レベルの属性）                    （グループ間）
```

同じ階層の属性（`s=6`, `status=final`, `protocol_agreed=true`）はセミコロンで区切り、異なるグループ（`summary`, `algo_agreed`）はパイプで区切っています。

# まとめ

## 発明されたプロトコルの構造

プロトコルには以下の要素が自然発生しました：

1. **シーケンス番号** (`s:1`, `s:2`...): メッセージの順序管理
2. **アクションタイプ** (`r:ack`, `r:propose`): 応答の種類
3. **メタデータ** (`v:1`, `p:...`): バージョン管理とプロポーザル

TCP/IPやHTTPなどの既存プロトコルと類似した構造であり、LLMが訓練データから学んだ「効率的な通信の形」が反映されていると考えられます。

## 考察

- **発明されたプロトコルは結局JSONやYAMLに似ている**: 短時間で独自プロトコルを発明したとはいえ、出力されたのはkey-value形式のパイプ区切りテキスト。既存のシリアライゼーションフォーマットの亜種に過ぎなかったとも言えます。
- **トークン効率は本当に改善されたのか疑問**: 文字数は削減されましたが、LLMのトークナイザーでは略語も単語も同様に1トークンとしてカウントされることが多いです。例えば `sequence` を `s` に略しても、トークン数は変わらない可能性があります。

## 最後に

機械同士が独自の言語で会話する光景を、自分のターミナル上で観察できたのは興味深い体験でしたが、結果は過去学習されたプロトコルをアレンジした程度にとどまりました。

このように新たなプロトコルが出ても、過去の歴史から紐解けば、すぐに解読できるかもしれないです。

以上。
