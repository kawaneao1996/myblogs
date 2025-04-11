+++
title = "zolaで始めるブログ"
[taxonomies]
  tags = ["Rust", "zola"]
[extra]
  toc = true
+++

## 技術スタック

### SSG フレームワーク

→[zola](https://www.getzola.org/)

以下特徴：

- Rust 製の SSG フレームワーク
- markdown で記事を書くことができる
- Rust 製だから早い
- 有志のテーマが充実（個人的感想）
- 日本語のドキュメントがほぼないのがちょっとマイナス
  - でも mermaid の導入とか日本語の記事があったり
    - [Rust 製の Zola でテックブログをつくった感想](https://tech.yukashikado.co.jp/posts/powered-by-zola/)

### 使用テーマ

→[anemone(github repository)](https://github.com/Speyll/anemone.git)

正直、めっちゃおしゃれだと思う。開発者さんに感謝。

## 経緯

エンジニアとして得た知見を発信したかったため。

以前は[deno/fresh](https://fresh.deno.dev/)を使って markdown からサイトを表示する SSG を自作していた。

シンタックスハイライトや、mermaid.js の組み込みを頑張ったりしていたが、記事のタグ付けの実装あたりで車輪の再発明が辛くなった。

また deno deploy ができて便利だったが、金欠のため課金をやめてしまったのでそのタイミングで zola に切り替えた。

これを書いている段階では公開されてないが、これから github actions で公開する予定。

## zola を使ったブログの始め方

基本は[公式の手順](https://www.getzola.org/documentation/getting-started/overview/)の通り。

個人的なアドバイスポイントは、

- `zola init` の直後に `zola serve` を行い、続けて theme をセッティングする
  - 公式の通りに進めても、`zola serve` が意図しないタイミングでこけた
  - チュートリアル完了後に theme を設定しても反映されなかった（なので`zola init`の直後に theme を設定する。これだとうまくいく）
    - 俗に言う「おまかん」：お前の環境だけだ！の可能性あり

1. zola のインストール [インストール方法（公式）](https://www.getzola.org/documentation/getting-started/installation/)
2. `zola init myblogs`
3. ここですぐに`zola serve`
4. （任意の theme を設定）⇦ ここは任意かも
   1. [テーマ一覧](https://www.getzola.org/themes/)から好きなテーマを探す
   2. `/themes/`以下にリポジトリをクローン
   3. （追加の処理があれば）テーマのリポジトリの指示を実行
5. 後はチュートリアル通り

## その他補足

- 作成したブログのリポジトリ(`zola init`したディレクトリ)をクローンする際には themes 以下をサブモジュール化する

  - > In either case, it seems to work best if you use git submodule to include your theme, e.g.:

    > `git submodule add https://github.com/getzola/after-dark.git themes/after-dark`

    - 引用：<https://www.getzola.org/documentation/deployment/github-pages/>

- サブモジュールが含まれるリポジトリのクローン方法
  1. `git submodule init`
  2. `git submodule update`
