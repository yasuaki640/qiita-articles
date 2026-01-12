---
title: asdfでElixirをサクッとインストールする
tags:
  - Erlang
  - 環境構築
  - 初心者
  - Elixir
  - asdf
private: false
updated_at: '2026-01-13T08:24:22+09:00'
id: 1d9d392b40ef721963f8
organization_url_name: null
slide: false
ignorePublish: false
---

asdfでElixirを入れるとき、Erlangも一緒に入れる必要があります。手順とポイントをまとめました。

## インストール手順

### 1. Erlangをインストール

```bash
asdf plugin add erlang
asdf install erlang 27.2
asdf set --home erlang 27.2
```

### 2. Elixirをインストール

```bash
asdf plugin add elixir
asdf install elixir 1.18.1-otp-27
asdf set --home elixir 1.18.1-otp-27
```

### 3. 動作確認

```bash
iex
```

`iex>` プロンプトが表示されればOKです。

## その他

### OTPバージョンって何？

`elixir 1.18.1-otp-27` の `-otp-27` は、「Erlang/OTP 27向けにビルドされたElixir」という意味です。

#### なぜ指定が必要か

ElixirはErlang VM（BEAM）上で動作するため、**ElixirとErlangのバージョンには互換性の制約**があります。

例えば：
- Elixir 1.18.x は OTP 25〜27 に対応
- Elixir 1.17.x は OTP 25〜27 に対応
- Elixir 1.15.x は OTP 24〜26 に対応

#### 確認方法

互換性マトリックスは公式ドキュメントで確認できます：
https://hexdocs.pm/elixir/compatibility-and-deprecations.html#compatibility-between-elixir-and-erlang-otp

asdfでインストール可能なバージョン一覧は以下で確認できます：

```bash
asdf list all elixir
```

## まとめ

- Elixirを入れる前にErlangを入れる
- Elixirのバージョン指定には `-otp-XX` を含める
- 互換性が不安なら公式ドキュメントを確認
