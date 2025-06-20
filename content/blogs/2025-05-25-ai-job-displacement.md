+++
title="ブログのデプロイ先を Netlify にした"
[taxonomies]
tags = ["Netlify"]
[extra]
toc = true
+++

## ブログのデプロイ先を Netlify にした

もともとは zola でビルドした成果物を Github Pages にデプロイしていたがルーティング周りが独特な仕様だからか画像リンクが表示されないとか、マークダウンのリンクが正しく動かないとか色々問題があった。

勉強がてら AWS S3 + CloudFront にデプロイとかしてみたかったけど時間無いので Netlify を選んだ。

Github のリポジトリを連携させて、`netlify.toml`をこんな感じで置いておくだけ。めっちゃ楽。

```toml
[build]
# This assumes that the Zola site is in a docs folder. If it isn't, you don't need
# to have a `base` variable but you do need the `publish` and `command` variables.
publish = "public"
command = "zola build"

[build.environment]
# Set the version name that you want to use and Netlify will automatically use it.
ZOLA_VERSION = "0.20.0"

# The magic for deploying previews of branches.
# We need to override the base url with whatever url Netlify assigns to our
# preview site.  We do this using the Netlify environment variable
# `$DEPLOY_PRIME_URL`.

[context.deploy-preview]
command = "zola build --base-url $DEPLOY_PRIME_URL"

```

> [出典：Zola - Netlify](https://www.getzola.org/documentation/deployment/netlify/)
