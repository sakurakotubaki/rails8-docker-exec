# Rails8 Docker Container
環境構築用ファイルを作成する。

1. database.yml
2. docker-compose.yml
3. Dockerfile
4. Dockerfile.dev
5. Gemfile

```shell
docker compose up -d
# docker-composeを再ビルドして起動
docker-compose build --no-cache
docker-compose up -d
```

コンテナに入る
```shell
# コンテナ名を調べる
docker ps
# コンテナ名を指定して内部に入る
docker exec -it rails8-docker-web-1 bash
```

コンテナ内部で作成する。
```shell
# Railsアプリケーションを新規作成
cd /rails
# コンテナ内で
bundle install
# create project
rails new myapp --force --database=postgresql

# myappへ移動して実行する。
cd myapp
# データベース作成
rails db:create

# サーバー起動確認
rails s -b 0.0.0.0
# ctrl + c 停止 \
# コンテナ内部から抜ける
exit
```

コンテナ作成に成功すると表示されるはずです。

<img src="screen-shot/created.png" />
<img src="screen-shot/rails-s.png" />