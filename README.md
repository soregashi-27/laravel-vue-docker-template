# laravel-vue-docker-template-v1
Dev template with laravel and vue based on docker. Version1

※編集中

・バージョン（Laravel:6.20.30, Docker, Docker Compose, ...）
・環境構築方法 \
git clone \
composer install \
db containerの設定 

・環境構築の注意点 \
db containerの設定をする前にbuildしないこと（想定されるエラーが出る可能性あり）


・想定されるエラー \
buildしてdocker volumesが残っちゃっててコードの更新が反映されてないことに気づかず古いコンテナで動かしちゃってるパターン \
→wikiに詳細を追加してreadmeにはリンクを入れるだけでOK

---------

wikiに書く内容 \
コード内容に関する説明

+ laravel UIを追加する時の注意点
+ laravel6.*の場合はver.1がmust. これ以外のバージョンは非対応 

---------

READMEに残す内容

## Docker Containerを立ち上げる
### git cloneする
```
git clone 編集中
```

### cloneしたディレクトリに移動する（clone時にdir nameを変えた場合は`cd changeDirName`）
```
cd laravel-vue-docker-template-v1
```

### Containerを立ち上げる`docker compose up -d --build`する
```
docker compose up -d --build
```

### ローカルホストに接続するけどつながらないことを確認する
（正しい：git cloneが終わった状態では `app` コンテナ内に `/work/vendor` ディレクトリが存在しないため）
<br />
<br />
## Laravelをインストールする
### appコンテナに入るために`docker compose exec app bash`する
```
docker compose exec app bash
```

### Laravel6系が入ったかを確認する
```
php artisan -V
```
（Laravel Framework 6.20.30と出るはず。「7/24現在」）

### もう一度ローカルホストへ接続
500 | servert errorが出ることを確認する（正しい）
`composer install` 時は `.env` 環境変数ファイルは作成されないので、 `.env.example` を元にコピーして作成する必要がある。

### app container内で`.envファイル`を作成 
```
cp .env.example .envを入力
```
Your app is missingというエラーが出ることを確認する（これも正しい）

### アプリケーションKeyを作成するために、app containerを立ち上げる
```
docker compose exec app bash
```

### アプリケーションKeyを作成する
```
php artisan key:generate
```

暗号化の際に使われる。SessionやAuth機能など。App Keyが確実に指定されていれば暗号化された値は安全。

### シンボリックリンクを作成する
```
php artisan storage:link
```
`public/storage` から `storage/app/public` へのシンボリックリンクをつくる
システムで生成したファイル等をブラウザからアクセスできるよう公開するため。`strorage dir`にアクセスするためのもの。

Laravelで作ったプリケーションが公開されるとき、公開されるのは一番上の階層にある public ディレクトリのみ。 \
public ディレクトリにファイルや処理のすべてが集約される。保存した画像も public ディレクトリ内に存在しないとアクセスすることがで着なくなってしまうので、その予防策。

### 書き込み権限の追加
```
chmod -R 777 storage bootstrap/cache
```
-R : recursive \ 　
`storage,bootstrap/cache`はフレームワークからファイル書き込みが発生するので、書き込み権限を与える。


### migrationする
`docker compose exec app bash` からスタート。

※appコンテナに移動する（Docker containerを立ち上げた後に行う）
```
docker compose exec app bash
```

```
php artisan migrate
```
appコンテナ内で`php artisan migrate`

よく起こるmigration errorはwikiに切り出す


ーーーーWikiに追加ーーーーーーー

よく起こしがちな **マイグレーションエラーの補足**

Host '172.27.0.2' is not allowed to connect to this MySQL server

```
$ php artisan migrate
   Illuminate\Database\QueryException  : SQLSTATE[HY000] [1130] Host '172.27.0.2' is not allowed to connect to this MySQL server (SQL: select * from information_schema.tables where table_schema = homestead and table_name = migrations and table_type = 'BASE TABLE')

  at /work/vendor/laravel/framework/src/Illuminate/Database/Connection.php:664
    660|         // If an exception occurs when attempting to run a query, we'll format the error
    661|         // message to include the bindings with SQL, which will make this exception a
    662|         // lot more helpful to the developer instead of just the database's errors.
    663|         catch (Exception $e) {
  > 664|             throw new QueryException(
    665|                 $query, $this->prepareBindings($bindings), $e
    666|             );
    667|         }
    668| 

  Exception trace:

  1   PDOException::("SQLSTATE[HY000] [1130] Host '172.27.0.2' is not allowed to connect to this MySQL server")
      /work/vendor/laravel/framework/src/Illuminate/Database/Connectors/Connector.php:70

  2   PDO::__construct("mysql:host=db;port=3306;dbname=homestead", "homestead", "secret", [])
      /work/vendor/laravel/framework/src/Illuminate/Database/Connectors/Connector.php:70

  Please use the argument -v to see more details.
```

このエラーが発生した場合は `my.cnf` を作成する前に `docker compose up -d` でビルドしてしまった可能性が高い。（大体このケース）

```
$ docker compose down --volumes --rmi all
$ docker compose up -d --build
```

設定ファイルがない状態でMySQLの初期化が行われたでデータが永続化されてしまってるので一度ボリューム毎削除してビルドし直せばok。

ーーーーWikiに追加 finーーーーー

### Migrationに成功したらContainerを落とす
```
docker compose down
```

<br />
<br />

## MySQLに接続してみる
### local環境からdb containerにアクセスしてMySQLにログイン
```
docker compose exec db bash -c 'mysql -u${MYSQL_USER} -p${MYSQL_PASSWORD} ${MYSQL_DATABASE}'
```

あらかじめこのtemplateでUSER, PASSWORD, DATABASEは決めているのでログインできる。
※適宜名前は変更すること。



・テーブルの中身を確認`show tables;`

```
+-------------------------+
| Tables_in_laravel_local |
+-------------------------+
| failed_jobs             |
| migrations              |
| password_resets         |
| users                   |
+-------------------------+
```



・テーブルの構造を確認する`desc users;`

DESCはdescribeの略。

```
+-------------------+-----------------+------+-----+---------+----------------+
| Field             | Type            | Null | Key | Default | Extra          |
+-------------------+-----------------+------+-----+---------+----------------+
| id                | bigint unsigned | NO   | PRI | NULL    | auto_increment |
| name              | varchar(255)    | NO   |     | NULL    |                |
| email             | varchar(255)    | NO   | UNI | NULL    |                |
| email_verified_at | timestamp       | YES  |     | NULL    |                |
| password          | varchar(255)    | NO   |     | NULL    |                |
| remember_token    | varchar(100)    | YES  |     | NULL    |                |
| created_at        | timestamp       | YES  |     | NULL    |                |
| updated_at        | timestamp       | YES  |     | NULL    |                |
+-------------------+-----------------+------+-----+---------+----------------+
```



それぞれの項目の意味

Field：カラム名

Type：データ型

Null：Nullの値を入れられるかどうか。できる場合は `YES`、できない場合は `NO`。

Key：インデックス設定されているかどうか。主キー（PRIMARY KEY）の場合は `PRI`、 UNIQUEインデックスの場合は `UNI`、同じ値が現れることが許可されているインデックスの場合は `MUL`。

Default：デフォルト値。なにも設定されていない場合は、最初に `NULL` が格納される

Extra：追加情報。`auto_increment` （指定したカラムに対してMySQLが自動的に一意のシーケンス番号を生成する機能）など。



・Laravelのログをコンテナログに表示

`backend/.env` を修正

```
LOG_CHANNEL=stderr
```



backend/routes/web.php

```
Route::get('/', function () {
    logger('welcome route.');
    return view('welcome');
});
```



logをみる方法3パターン

```
$ docker compose logs

# -f でログウォッチ
$ docker compose logs -f

# サービス名を指定してログを表示
$ docker compose logs -f app
```

`docker compose logs -f app`のあとに、`http://127.0.0.1:8080/`に接続するlog上にこんな感じで表示される

```
app_1  | [2021-07-25 05:48:53] local.DEBUG: welcome route.  
app_1  | 172.20.0.3 -  25/Jul/2021:05:48:51 +0000 "GET /index.php" 200
```



開発環境が整ったので開発スタート。


vue、vuetifyを追加（wikiリンク→準備中）

Laravelの初期設定。（wikiリンク→準備中）
