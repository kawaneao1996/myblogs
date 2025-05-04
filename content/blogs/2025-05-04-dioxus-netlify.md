+++
title="DioxusとNetlifyで作るWebアプリ"
[taxonomies]
tags = ["Rust", "Dioxus", "Netlify"]
[extra]
toc = true
+++

## 概要

- [Dioxus](https://dioxuslabs.com/)で Web アプリを作成したら[Netlify](https://www.netlify.com/)にデプロイするのが楽
- [成果物（ポモドーロタイマー）](https://astounding-gaufre-3c1912.netlify.app/)

## 背景

- `Dioxus`は Rust 製の GUI フレームワークで、マルチプラットフォームが特徴
  - コンポーネントの記述はほぼ`React`
- 当初は Github Actions から Github Pages にデプロイしようとしていた
  - `dioxus-cli`のインストールが 10 分くらいかかるし、結局うまくいかなかったので Netlify にデプロイ
  - （補足）[Dioxus Deploy](https://dioxuslabs.com/deploy/)が近日中にリリースされるらしい。それまでは Netlify でいいのでは

## 詳細

ここでは`Dioxus`のプロジェクト作成からデプロイまでを簡単に説明する。

説明にあたり簡単なポモドーロタイマーのアプリを Github Copilot に作ってもらった。

ソースコードは[pomodoro_timer](https://github.com/kawaneao1996/pomodoro_timer)に格納。

~~見るからに AI が作りました！というデザイン~~

### プロジェクトの作成

[公式の Getting Started](https://dioxuslabs.com/learn/0.6/getting_started/#)を参考に`dioxus-cli`をインストール。

ターミナルから`dx --version`が実行できればインストール完了。

次にプロジェクト作成のためにターミナルから`dx init <project名>`を実行する。

(今回サブテンプレートは Bare-Bones を選択した。)

※ サブテンプレートについては（参考 by Claude 3.7 Sonnet）Dioxus のサブテンプレートについて を参照

（`dx init`したら一度`dx serve`を実行して、サンプルページが表示されることを確認すると良き）

あとは`main.rs`や`cargo.toml`を作っていくだけ。

### 動作確認／bundle

動作確認にはターミナルから`dx serve`を実行する。

bundle するには`dx bundle --platform web`をターミナルで実行

→`target\dx\<project_name>\release\web\public`に html とその他の asset が生成される。

### Netlify にデプロイ

Netlify にログインして、前セクションの`public`ディレクトリをドラッグアンドドロップする。（`public`ディレクトリの中身ではなく、`public`フォルダ自体を入れる）

↓ にドラッグアンドドロップ
![Netlifyのドラッグアンドドロップ先](/images/2025-05-04/image.png)

これでデプロイが完了。

## （参考 by Claude 3.7 Sonnet）Dioxus のサブテンプレートについて

```markdown
# Dioxus のサブテンプレートについて

Dioxus の`dx new`コマンドで表示される「サブテンプレート」は、新しいプロジェクトの初期構造を決める選択肢です。調査によると、以下の 3 つのオプションがあります：

## 1. Bare-Bones（ベアボーン）

最もシンプルな構成です。

- main.rs ファイルと assets フォルダのみの基本的なセットアップ
- 最小限の依存関係
- 基本的な App コンポーネント以外の事前構築されたコンポーネントはなし

小規模なアプリケーションや、プロジェクト構造を一から構築したい場合に適しています。

## 2. Jumpstart（ジャンプスタート）

より整備された構造を提供します。

- 事前定義されたフォルダ構造
- サンプルコンポーネント
- ビューやページのための推奨構成
- アプリケーション構築のためのより構造化されたアプローチ

最初からいくつかのベストプラクティスを組み込みたい場合に便利です。

## 3. Workspace（ワークスペース）

最も包括的なオプションです。

- 異なるプラットフォーム（ウェブ、デスクトップ、モバイル）用の個別のクレートを持つ完全な Cargo ワークスペースを作成
- プラットフォーム固有のコードを維持しながら共通コンポーネントを共有する必要があるクロスプラットフォームアプリケーションに適している

## 選択後のプロセス

サブテンプレートを選択した後、CLI は通常以下のような追加の設定質問をします：

- Dioxus Fullstack を使用するかどうか
- Dioxus Router を使用するかどうか
- Tailwind CSS を使用するかどうか
- デフォルトのプラットフォーム（ウェブ、デスクトップなど）

## 推奨

ポモドーロタイマーのような比較的シンプルなアプリケーションを構築する場合は、Bare-Bones テンプレートで十分かもしれません。複数のビューやコンポーネントを持つより複雑なアプリケーションを計画している場合は、Jumpstart テンプレートがより良い初期構造を提供します。ウェブ、デスクトップ、潜在的にモバイルで動作するクロスプラットフォームアプリケーションを作成する予定がある場合は、Workspace テンプレートが最も適しています。
```
