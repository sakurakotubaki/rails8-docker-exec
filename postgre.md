# PostgreSQL References
DockerでPostgreSQLを使う。

[- itオプションについて](https://zenn.dev/swata_dev/articles/2f85a3f4b3022c)

exec -it オプションの説明
docker exec -it コマンドの -it は2つのオプションが組み合わさっています：

-i (interactive): コンテナの標準入力（STDIN）をオープンに保ちます。これにより、ユーザーからの入力をコンテナに送信できます。
-t (tty): 疑似TTY（ターミナル）を割り当てます。これによりプロンプトが正しく表示され、コマンド実行時のフォーマットが整います。
この2つを組み合わせることで、ターミナルでコンテナと対話的にやり取りできるようになります。

使用例
PostgreSQLコンテナに入るなら例えば：

-it なしで実行すると、単にコマンドを実行してその結果を返すだけで、対話的な操作ができません。対話的なシェルやDBクライアントを使う場合は -it が必要です。

コンテナ内部に入る
```shell
# コンテナ名を調べる
docker ps
# Postgre内部に入る
# NAMESの場合rails8-docker-exec-db-1
docker exec -it rails8-docker-exec-db-1 bash
```

# PostgreSQL ユーザー管理コマンド
PostgreSQLコンテナ内に入った後、以下のコマンドでユーザー管理ができます。

## PostgreSQLの対話モードに入る
まず、PostgreSQLの対話モードに入ります：

```shell
psql -U postgres
```

## ユーザー作成コマンド
### 基本的なユーザー作成
```sql
CREATE USER username WITH PASSWORD 'password';
```

### 権限付きユーザー作成
```sql
CREATE USER username WITH PASSWORD 'password' CREATEDB;
```

### 一般ユーザー作成 (General User)
```sql
-- 基本的な一般ユーザーの作成
CREATE USER general_user WITH PASSWORD 'password';

-- 特定のデータベースへの接続権限を付与
GRANT CONNECT ON DATABASE database_name TO general_user;

-- テーブルへの読み取り権限のみを付与
GRANT SELECT ON ALL TABLES IN SCHEMA public TO general_user;

-- または特定のテーブルへの読み書き権限を付与
GRANT SELECT, INSERT, UPDATE ON table_name TO general_user;
```

### ユーザーに全ての権限を付与
```sql
-- スーパーユーザー権限を付与する方法
ALTER USER general_user WITH SUPERUSER;

-- または、データベースごとに全ての権限を付与
GRANT ALL PRIVILEGES ON DATABASE database_name TO general_user;

-- スキーマ内のすべてのテーブルに対して全ての権限を付与
GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO general_user;
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO general_user;
GRANT ALL PRIVILEGES ON ALL FUNCTIONS IN SCHEMA public TO general_user;

-- 新規作成されるオブジェクトに対しても自動的に権限を付与
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL PRIVILEGES ON TABLES TO general_user;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL PRIVILEGES ON SEQUENCES TO general_user;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT ALL PRIVILEGES ON FUNCTIONS TO general_user;
```

## ユーザー確認コマンド
```sql
\du
```

### 特定のユーザーの権限を確認
```sql
SELECT usename, usecreatedb, usesuper FROM pg_user WHERE usename = 'username';
```

## PostgreSQL対話モードを終了
```sql
\q
```

これらのコマンドはすべてPostgreSQLの対話モード内で実行します。

```sql
-- データベース新規作成
CREATE DATABASE dev_db;
-- 作成されているデータベースを確認
\l
```

## データベース変更コマンド
PostgreSQLの対話モード内で、データベースを変更するには以下のコマンドを使用します：

```sql
\c database_name
-- dev_dbに変更
\c dev_db
```

## テーブル作成コマンド
`food_reservation` テーブルを作成するには以下のクエリを使用します：

```sql
CREATE TABLE food_reservation (
    id SERIAL PRIMARY KEY,
    customer_name VARCHAR(100) NOT NULL,
    reservation_date DATE NOT NULL,
    number_of_guests INT NOT NULL,
    special_requests TEXT
);
```

## テーブル一覧表示コマンド
現在のデータベース内のテーブル一覧を表示するには以下のコマンドを使用します：

```sql
\dt
```

## ダミーデータ挿入コマンド
`food_reservation` テーブルにダミーの予約情報を追加するには以下のクエリを使用します：

```sql
INSERT INTO food_reservation (customer_name, reservation_date, number_of_guests, special_requests)
VALUES 
    ('山田 太郎', '2023-11-01', 4, '誕生日ケーキあり'),
    ('佐藤 花子', '2023-11-02', 2, '誕生日ケーキなし'),
    ('鈴木 一郎', '2023-11-03', 3, '誕生日ケーキあり');
```

これにより、3件のダミー予約情報がテーブルに追加されます。

追加されたデータの確認
```sql
-- もし仮にデータが１００件あって取得すると良くないので３件と指定
SELECT * FROM food_reservation LIMIT 3;
```