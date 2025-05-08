# Use Active Record
RailsとPostgreコンテナが作成されている状態で行う。

[bundle execとは？](https://learning-next.app/blog/backend-development/bundle-exec-definition-rails)

## 新規で作成した方が良さそうだった。
> コマンド一発でテーブル作成まで行う手順

以下のコマンドを順番に実行することで、モデル作成からテーブル作成まで自動で行えます（`myapp`ディレクトリ内で実行）:

```bash
# モデルとマイグレーションファイルを生成
bundle exec rails generate model FoodReservation customer_name:string reservation_date:date number_of_guests:integer special_requests:text
```

## データベースとテーブルの確認

以下のコマンドでデータベースが存在するか確認できます:

```bash
rails db:exists
```

また、テーブルが存在するか確認するには以下を使用します:

```bash
rails db:migrate:status
```

これにより、現在のマイグレーションの状態を確認し、テーブルが作成されているかどうかを確認できます。

## 既存テーブルの確認

すでにテーブルが存在する場合、Railsコンソールやpsqlコマンドで確認できます。

### Railsコンソールで確認

```bash
cd myapp
bundle exec rails console
```

コンソール内で以下を実行します:

```ruby
ActiveRecord::Base.connection.tables
```

### psqlコマンドで確認

```bash
psql -U general_user -h db dev_db
```

psqlプロンプトで以下を実行します:

```sql
\dt
```

これで、現在存在するテーブル一覧が表示されます。

## 既存テーブルがある場合の対応

既存のテーブルがある場合でも、`rails db:migrate`を実行して問題ありません。  
マイグレーションファイルの内容とDBの状態が一致していれば、既存テーブルが再作成されることはありません。  
マイグレーションの状態は以下で確認できます:

```bash
bundle exec rails db:migrate:status
```

もしマイグレーションが未適用の場合は、以下で適用できます:

```bash
bundle exec rails db:migrate
```

既存テーブルがマイグレーションと一致していれば、エラーなく接続・操作できます。  
テーブル構造が異なる場合は、マイグレーションファイルを調整するか、DBのテーブル構造を合わせてください。

## Railsコマンドが見つからない場合の対処

コンテナ内で`rails`コマンドが見つからない場合は、アプリケーションディレクトリ（例: `myapp`）に移動してから、`bundle exec rails`を実行してください。

```bash
cd myapp
rails db:exists
```

存在しなかった場合は、myappの中で実行する。
```shell
bundle install
```

または、`ls myapp/bin`で`rails`ファイルが存在するか確認し、以下のように実行できます:

```bash
rails db:migrate:status
```

## PostgreSQLへの接続確認

PostgreSQLに直接接続できるか確認するには、以下のコマンドを使います:

```bash
psql -U general_user -h db dev_db
```

`psql`が見つからない場合は、`apt-get update && apt-get install -y postgresql-client`でインストールできます。

## コマンドのキャンセル方法

コマンド実行中にキャンセルしたい場合は、`Ctrl + C` を押してください。  
これで現在実行中のコマンドを中断できます。

## food_reservationのデータ取得と新規データ登録

### モデルがない場合

`food_reservation`テーブル用のモデルが存在しない場合は、以下のコマンドでモデルを作成してください（`myapp`ディレクトリ内で実行）:

```bash
rails generate model FoodReservation
```

このコマンドで`app/models/food_reservation.rb`が作成されます。  
必要に応じて、生成されたマイグレーションファイルやモデルファイルを編集してください。

#### テーブルを手動で作成する場合

もしマイグレーションを使わず手動でテーブルを作成したい場合は、psql等で以下のSQLを実行してください:

```sql
CREATE TABLE food_reservations (
    id SERIAL PRIMARY KEY,
    customer_name VARCHAR(100) NOT NULL,
    reservation_date DATE NOT NULL,
    number_of_guests INT NOT NULL,
    special_requests TEXT
);
```

#### マイグレーションでテーブルを作成する場合

まず、以下のコマンドでマイグレーションファイルを作成します（`myapp` ディレクトリ内で実行）:

```bash
rails generate migration CreateFoodReservations
```

Railsのマイグレーションファイル（例: `db/migrate/xxxxxx_create_food_reservations.rb`）を以下のように編集します:

```ruby
class CreateFoodReservations < ActiveRecord::Migration[8.0]
  def change
    create_table :food_reservations do |t|
      t.string :customer_name, null: false, limit: 100
      t.date :reservation_date, null: false
      t.integer :number_of_guests, null: false
      t.text :special_requests

      t.timestamps
    end
  end
end
```

編集後、以下を実行してテーブルを作成します:

```bash
bundle exec rails db:migrate
```

> **エラー対処:**  
> すでに`FoodReservation`モデルやマイグレーションファイルが存在する場合、  
> `The name 'FoodReservation' is either already used in your application or reserved by Ruby on Rails.`  
> というエラーが表示されます。  
> 
> **対応方法:**  
> - 既存のモデルやマイグレーションファイルを確認し、不要であれば削除してください。  
> - 既存のファイルを残したまま強制的に上書きしたい場合は、`--force`オプションを付けて再実行できます:
>
>   ```bash
>   bundle exec rails generate model FoodReservation customer_name:string reservation_date:date number_of_guests:integer special_requests:text --force
>   ```
> - 既存のファイルを編集して必要なカラムを追加することもできます。

```bash
# マイグレーションを適用してテーブルを作成
ails db:migrate
```

これで`food_reservations`テーブルが作成されます。

#### 生成されるマイグレーションファイル例

```ruby
class CreateFoodReservations < ActiveRecord::Migration[8.0]
  def change
    create_table :food_reservations do |t|
      t.string :customer_name, null: false, limit: 100
      t.date :reservation_date, null: false
      t.integer :number_of_guests, null: false
      t.text :special_requests

      t.timestamps
    end
  end
end
```

必要に応じて、生成されたマイグレーションファイルやモデルファイルを編集してください。

### データ取得

モデルが正しく定義されていれば、以下でデータ取得できます:

```ruby
FoodReservation.all
```

#### テーブルが存在しない場合のエラー

もし `FoodReservation.all` 実行時に  
`PG::UndefinedTable: ERROR:  relation "food_reservations" does not exist`  
のようなエラーが出る場合は、`food_reservations` テーブルがまだ作成されていません。

#### 対処方法

マイグレーションを適用してテーブルを作成してください（`myapp` ディレクトリ内で実行）:

```bash
bundle exec rails db:migrate
```

これで `food_reservations` テーブルが作成され、再度 `FoodReservation.all` でデータ取得できるようになります。

### 新規データ登録

Railsコンソールで新しいデータを登録するには以下を実行します:

```ruby
FoodReservation.create(attribute1: 'value1', attribute2: 'value2')
```

`attribute1`や`attribute2`はテーブルのカラム名に置き換え、`value1`や`value2`は登録したい値に置き換えてください。

### 注意

データ登録時にバリデーションエラーが発生した場合は、モデルに定義されたバリデーションルールを確認してください。

## テーブルを削除（drop）したい場合

特定のテーブルを削除したい場合は、Railsマイグレーションまたはpsqlコマンドで対応できます。

### Railsマイグレーションでテーブルを削除

マイグレーションファイルを作成し、`drop_table`を記述します。

```bash
rails generate migration DropFoodReservationsTable
```

生成されたマイグレーションファイル（例: `db/migrate/xxxxxx_drop_food_reservations_table.rb`）を以下のように編集します:

```ruby
class DropFoodReservationsTable < ActiveRecord::Migration[7.0]
  def change
    drop_table :food_reservations
  end
end
```

編集後、以下を実行してテーブルを削除します:

```bash
rails db:migrate
```

### psqlコマンドで直接削除

psqlに接続し、SQLでテーブルを削除できます。

```sql
DROP TABLE food_reservations;
```

シーケンスも削除したい場合は、以下も実行します:

```sql
DROP SEQUENCE food_reservations_id_seq;
```

## マイグレーションのロールバック・キャンセル

直前のマイグレーションを取り消したい場合は、以下のコマンドを使います:

```bash
rails db:rollback
```

複数回分戻したい場合は、`STEP`オプションを指定します（例: 2回分戻す場合）:

```bash
rails db:rollback STEP=2
```

マイグレーションをすべて取り消して初期状態に戻す場合は:

```bash
rails db:migrate:reset
```

> **注意:** `db:migrate:reset`は全テーブルを削除し、再作成します。データも消えるので注意してください。

> **補足:**  
> `rails db:migrate:reset`は**マイグレーションで管理されているテーブルのみ**を削除・再作成します。  
> もし手動で作成したテーブル（例: `food_reservation`）や、マイグレーションで管理されていないテーブル・シーケンスは**消えません**。  
> その場合はpsqlなどで手動で削除してください。

> **エラー対処:**  
> `rails db:drop`や`rails db:migrate:reset`で「database is being accessed by other users」エラーが出る場合、  
> 他のセッションやpsql接続がDBを使用中です。  
>  
> **対処方法:**  
> 1. すべてのpsql接続やRailsコンソールなど、DBに接続しているプロセスを終了する  
> 2. それでも解決しない場合は、以下のSQLで強制切断できます（psqlで`postgres`など別DBに接続して実行）:
>
>    ```sql
>    SELECT pg_terminate_backend(pid)
>    FROM pg_stat_activity
>    WHERE datname = 'dev_db' AND pid <> pg_backend_pid();
>    ```
> 3. その後、再度`rails db:drop`や`rails db:migrate:reset`を実行してください。