# Qiita_Cli_App
「qiita-cli でできること」
ローカル環境で記事を新規作成/修正し、cli 経由で投稿することができる.
VScode 等の補完や整形が markdown に適用できるエディタを用いての効率的な執筆や, 記事修正がお手軽にできる.
一方, qiita-cli では手動のコマンド実行で同期を行います. なので、git レポジトリに上げて管理も可能ですし、アップはぜずにローカルだけで管理も可能.

・インストール
以下コマンドでまず qiit-cli をグローバルにインストールする
```
npm install -g qiita-cli
```
・初期設定
```
qiita init
```
・記事のローカル保存
```
qiita pull:article
```
・記事の新規作成
```
qiita new:article
```
・新規作成記事の投稿
```
qiita post:article
```
・既存記事と修正と投稿
```
qiita patch:article
```
上記 + その他のコマンドを、
```
qiita --help
```
で確認できます。

