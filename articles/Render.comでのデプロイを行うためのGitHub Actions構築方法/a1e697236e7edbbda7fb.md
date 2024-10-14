---
id: a1e697236e7edbbda7fb
title: Render.comでのデプロイを行うためのGitHub Actions構築方法
created_at: 2024-10-10T18:52:08+09:00
updated_at: 2024-10-10T18:52:08+09:00
tags: [{"name":"deploy","versions":[]},{"name":"GitHubActions","versions":[]},{"name":"#render","versions":[]},{"name":"#render.com","versions":[]}]
private: false
url: https://qiita.com/ora_yoshito/items/a1e697236e7edbbda7fb
likes_count: 0
---

# なぜ、Render.comへのデプロイを行う事になったのか?
#### HerokuなどのクラウドベースのSaaSが無料サービスを終了したため、代替サービスを探しました。
#### Render.comは無料でWebサービスをホスティングでき、以下のような特徴があります
- 月間使用時間： 最大750時間
- リソースの制限： 1つのインスタンスに対して512MBのメモリと0.5vCPUが提供される
#### 無料で使用できる範囲としては非常に良心的なサービスだと感じました。また、将来的に数百～数千の同時接続が発生する可能性はありますが、今回使用するPythonのFlaskアプリケーションはメモリ消費が少ないため、Render.comを選びました。

# 自作ワークフローの作成と既存Actionsの組み合わせ
#### 今回は、Githubでのマーケットプレイス検索で「Render Deploy」としました。先人たちが作ってくれたActionがあったのですが何かと機能が満足するようなものがなかったので、色々調べていると...
https://github.com/ttskch/render-deploy
#### この方が作成した、シンプルかつオプション設定が出来るのを見つけたので使用する事にしました。

# Render.comへのデプロイを行うActionのワークフローについて
```
name: Render Deploy

on:
  push:
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      # コードのチェックアウト
      - name: Checkout code
        uses: actions/checkout@v3

      # Dockerイメージのビルド
      - name: Build Docker image
        run: |
          docker build -t inoue/my_flask_hp:latest .

      # Renderへのデプロイ
      - uses: ttskch/render-deploy@v1.0.0
        with:
          api-key: ${{ secrets.RENDER_API_KEY }}
          service-id: ${{ secrets.RENDER_SERVICE_ID }}
          interval: 20
          timeout: 300 
```
このようなワークフローにしました。
- `on`: pushおよびpull requestでトリガーされます
- `jobs`: 最新のUbuntuを使用してコードのチェックアウトを行い、Dockerイメージをビルドし、Renderへデプロイします
- `uses`: 先述したttskch/render-deploy Actionを使用しています
#### `api-key`と`service-id`には、GitHubリポジトリ内のActions secrets and variablesに設定したシークレット変数を使用しています。これらのシークレット変数は、コード上で直接参照されないようにしつつ、同じ名前でリポジトリに設定する必要があります。
#### 必須パラメーターは、
- api-key Render.comのAPIキー
- service-id デプロイ対象のサービスのサービスID
#### これらは、Render.comのAPIを使用してデプロイを実行するために必須となります。

# 最後に
今回は、Render.comでのデプロイを行うためにGitHub Actionsを構築する方法を紹介しました。シンプルかつ機能的なActionを利用して、効率的なデプロイ環境を実現できたと感じています。

