---
title: DevBoxを使ってMacOSでAtomVMをビルド・実行する
tags:
  - Erlang
  - macOS
  - Elixir
  - AtomVM
  - Devbox
private: false
updated_at: '2026-01-21T09:26:36+09:00'
id: 9cdcbc8f84d8eb979b1a
organization_url_name: null
slide: false
ignorePublish: false
---

# はじめに

この記事では、DevBoxを使ってMacOS上にAtomVMの開発環境を構築し、Erlang/ElixirのHello Worldを動かすまでの手順を紹介する。

## AtomVMとは

[AtomVM](https://github.com/atomvm/AtomVM)は、マイクロコントローラ向けに設計された最小限のErlang VM。ESP32、STM32、Raspberry Pi Picoなどの組み込みデバイスでErlang/Elixirを動かすことができる。

## 環境構築

### 前提条件

- macOS (Apple Silicon)

### DevBoxのインストール

```bash
curl -fsSL https://get.jetify.com/devbox | bash
```

### ソースコード取得

```bash
git clone https://github.com/atomvm/AtomVM.git
cd AtomVM
```

### devbox.jsonの作成

AtomVMリポジトリには`devbox.json`が含まれていないため、リポジトリのルートディレクトリで手動で作成する。

```bash
devbox init
devbox add cmake@latest gperf@latest github:NixOS/nixpkgs/31cab29#zlib.dev mbedtls@latest erlang@latest rebar3@latest github:NixOS/nixpkgs/31cab29#elixir
```

作成される`devbox.json`:

```json
{
  "$schema": "https://raw.githubusercontent.com/jetify-com/devbox/0.16.0/.schema/devbox.schema.json",
  "packages": [
    "cmake@latest",
    "gperf@latest",
    "github:NixOS/nixpkgs/31cab29#zlib.dev",
    "mbedtls@latest",
    "erlang@latest",
    "rebar3@latest",
    "github:NixOS/nixpkgs/31cab29#elixir"
  ]
}
```

:::note warn
一部パッケージはNix Flakeで固定リビジョンを指定している：

- **zlib**: `zlib@latest`ではなく`github:NixOS/nixpkgs/31cab29#zlib.dev`を使用。開発用ヘッダー（zlib.h）を含むパッケージが必要なため。
- **Elixir**: `elixir@latest`ではなく`github:NixOS/nixpkgs/31cab29#elixir`を使用。DevBoxのElixirプラグインにバグがあるため。詳細は[こちらのPR](https://github.com/jetify-com/devbox/pull/2764)を参照。
  :::

## ビルド

### DevBox環境に入る

```bash
devbox shell
```

初回は依存パッケージのダウンロードに時間がかかる。

### ビルド実行

リポジトリのルートディレクトリで以下を実行する。

```bash
cmake .
make -j 8
```

`-j 8`はCPUコア数に応じて調整可能。

### ビルド確認

```bash
ls -la ./src/AtomVM
```

`AtomVM`バイナリが生成されていれば成功。

## 動作確認

```bash
./src/AtomVM ./examples/elixir/HelloWorld.avm
```

![Elixir HelloWorld実行結果](https://raw.githubusercontent.com/yasuaki640/qiita-articles/main/public/atomvm-elixir-hello-world.png)

## トラブルシューティング

### ZLIBが見つからないエラー

```
Could NOT find ZLIB (missing: ZLIB_INCLUDE_DIR)
```

`zlib@latest`ではなく`github:NixOS/nixpkgs/31cab29#zlib.dev`を使用する。`zlib@latest`にはヘッダーファイル（zlib.h）が含まれていない。

### Elixirのビルドエラー

Elixirパッケージの互換性問題が発生する場合は、`devbox.json`のElixirリビジョンを確認。`elixir@latest`ではなく、固定リビジョン（`github:NixOS/nixpkgs/31cab29#elixir`）を使用すること。

## まとめ

DevBoxを使うことで、AtomVMのビルドに必要な依存関係を簡単にセットアップできた。

## 参考

- [AtomVM GitHub](https://github.com/atomvm/AtomVM)
- [AtomVM Documentation](https://www.atomvm.net/doc/master/)
- [DevBox](https://www.jetify.com/devbox)
