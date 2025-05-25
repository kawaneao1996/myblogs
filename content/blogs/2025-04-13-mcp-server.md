+++
title="MCP 使ってみた"
[taxonomies]
tags = ["generative ai"]
[extra]
toc = true
+++

## 概要

- VS Code の[Github Copilot agent](https://docs.github.com/ja/copilot/building-copilot-extensions/building-a-copilot-agent-for-your-copilot-extension/about-copilot-agents)と [Github MCP Server](https://github.com/github/github-mcp-server)を連携させることで[Devin](https://docs.devin.fm/jp)のような AI Agent を安価に構築することを試した
  - Github 上の issue を読み込んで Pull Request を作成することに成功した
  - Pull Request の精度は良くなかった。これは MCP というよりコンテキストの渡し方に問題があったと考えられる

## 背景

商用の AI エージェント、[Devin](https://docs.devin.fm/jp)等は高コストであり、気軽に利用することはできなかった。

しかし最近登場した MCP と Copilot agent を組み合わせ、低コストの AI エージェントを作ることができればより効率のよい開発に繋がると考えた。

## 方法

### 前提条件

- VSCode の agent mode が有効になっていること
  - やり方はググって
    - 参考：
      - <https://code.visualstudio.com/blogs/2025/02/24/introducing-copilot-agent-mode/>
      - <https://github.blog/jp/2025-04-07-github-copilot-agent-mode-activated/>
- [Github MCP Server 利用の前提条件](https://github.com/github/github-mcp-server#prerequisites)をクリアしていること

### Github のアクセストークン取得

Copilot に利用させるアクセストークンを Github で生成する。
（PC）Github のページ右上のアイコン押下 -> `Setting` -> `Developer Settings` -> `Personal access tokens`から`Fine-grained tokens`を作成
![Githubの右上のアイコンをクリック](/images/2025-04-12/1.png)

#### 設定例

アクセストークンにはエージェントに必要な最低限の権限のみを付与する。
今回はプライベートリポジトリにある issue と PR を扱うための権限のみを設定した。以下に設定例を記載しておく。

![アクセストークン設定例](/images/2025-04-12/2.png)

補足：

- リポジトリは適当（一応固定したけどしなくてもいいかも）
- code, issues, pull requests に関して権限を付与

### `settings.json`へ MCP Server の設定を追記

VSCode の設定(`Ctrl-,`)から`settings.json`を開き以下を記載する。
アクセストークンには Github で作成したアクセストークンを貼り付ける。

```json
  "mcp": {
    "servers": {
      "github": {
        "command": "docker",
        "args": [
          "run",
          "-i",
          "--rm",
          "-e",
          "GITHUB_PERSONAL_ACCESS_TOKEN",
          "ghcr.io/github/github-mcp-server"
        ],
        "env": {
          "GITHUB_PERSONAL_ACCESS_TOKEN": "<アクセストークン>"
        }
      }
    }
  }
```

参考：<https://github.com/github/github-mcp-server#usage-with-vs-code>

### MCP サーバの起動／動作確認

※Docker を起動しておく

MCP サーバを起動するやり方はいくつかある。
`settings.json`に`起動`ボタンが表示されているなら押下して起動できる。
もしくはコマンドパレット`Ctrl + Shift + P`から`MCP: サーバの一覧表示`を出して github を選択してもできる。

MCP サーバを起動させたら、Copilot を agent モードにして、以下のプロンプトを入力し Github MCP Server が動いているか確認する。

（リポジトリ URL にプライベートリポジトリを指定する。 MCP のアクセストークンが正しく設定できていなければタスクが失敗する。）

プロンプト：

```text
<リポジトリURL>のissue一覧を表示して
```

結果（参考）：

```md
以下は、リポジトリ「memo-todo-app」の現在のオープンな Issue 一覧です：

1. **アプリ公開のロードマップ** (#1)

   - **内容**: アプリ公開に向けたタスク一覧が記載されています。
     - コードレビュー
     - GCP にデプロイ
     - 動作確認
     - 広告を搭載
     - 課金機能実装
     - ドメインの取得
     - LP の作成／マーケティング
     - リリース
   - **作成日**: 2025 年 4 月 13 日
   - [Issue リンク](https://github.com/kawaneao1996/memo-todo-app/issues/1)

2. **ソースコード上の`any`型を無くしコードの可読性を向上させる** (#2)

   - **内容**: `any`型を具体的な型に置き換え、コードの可読性と型安全性を向上させる提案。
     - `any`型の箇所を特定
     - 適切な型定義を作成
     - テストを実行し動作確認
   - **ラベル**: `enhancement`, `refactor`
   - **作成日**: 2025 年 4 月 13 日
   - [Issue リンク](https://github.com/kawaneao1996/memo-todo-app/issues/2)

3. **アプリケーションのタイトルと Favicon を設定する** (#3)
   - **内容**: アプリのタイトルを「Google Drive マークダウンメモアプリ」に変更し、Favicon を設定する。
     - 必要に応じて`index.html`を編集
   - **作成日**: 2025 年 4 月 13 日
   - [Issue リンク](https://github.com/kawaneao1996/memo-todo-app/issues/3)

必要な詳細や対応があればお知らせください！
```

### Pull Request 作成の依頼

MCP サーバが動いているので、issue の内容を読み込み Pull Request を作成させた。

ただし Pull Request の精度は悪かった。原因としては以下が考えられる：

- コンテキストが不足していた
- プロンプトが不十分だった
- タスクの分割が不十分だった

上記に関しては MCP とは関係がないのでここにはこれ以上書かないが、継続して改善していきたい。

## 考察

Copilot agent でタスクをこなすには、Claude Code や他のエージェントより細かい指示だしが必要という体感があった。
推論して試行錯誤する機能が他のエージェントと比較して弱いのだろうか？

ただここは改善される可能性があるので Copilot には今後も期待したい。

## 結論

GitHub Copilot Agent と GitHub MCP Server を用いた AI エージェント構築の可能性を示した。今後はコンテキストの渡し方を改善し、精度向上を目指す。

## 蛇足

- 最近 VSCode の利用アンケートに回答した。その内容から MS は Copilot を従量課金にするか、もっと値上げしたプランで Agent を使わせたいのかなって思った。

- MCP が勃興すると SaaS が死ぬという言説をどっかの YouTube で見た。 SaaS の UI が AI Agent に置き換わる可能性は大いにあるよなあ。

  - 一方で MCP の仕様が大きく変わる可能性もあるので今 MCP でスタートアップに全力注ぐのはどうなの？って言説もあった。

- 個人的にプログラミングや AI 関連で検索エンジン叩くと広告しか出てこない現状に死ぬほどイライラするので、検索はすべてエージェントにさせる未来が来てほしい

  - Google ではなく DuckDuckGo 使うか...

- あとこの記事には書いていないが、[Claude Code](https://docs.anthropic.com/ja/docs/agents-and-tools/claude-code/overview) も試してみてなかなか良かった。Agent としては Cline よりも精度が高く速い気がした。

- Pull Request の作成は Claude Code のほうが精度がいいので Claude Code を MCP サーバとして利用すれば改善するかも

  - （でもそれなら Claude Code を使えばいいじゃんという..）

- ちなみに Claude Code を MCP サーバとして利用できる（≠ Claude Desktop で MCP サーバを利用する）という話はどこかで読んだけど、出典はどっか行った。ウソかもしれん。

- Claude Code から MCP サーバは利用できるのだろうか？（Claude Desktop からは利用できる）あとで調べたい。
