# laravel-vue-docker-template-v1
Dev-template-Ver.1 with laravel6.* on vue2.* based on docker.
Branch of 'template' means plane version.(Don't have a image of Node.js)


![Laravel 6.20.30](https://img.shields.io/badge/Laravel-6.20.30-red)
<img src="https://img.shields.io/badge/-Docker-EEE.svg?logo=docker&style=flat">

※編集中

・バージョン（Docker for Mac, Docker, Docker Compose, ...）
・環境構築方法 \
git clone \
composer install \
db containerの設定 

---------

READMEに残す内容【編集中】

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

### Composer installする
```
composer install
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
https://bit.ly/3x4egvf

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



### テーブルの中身を確認
```
show tables;
```

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


### テーブルの構造を確認する
```
desc users;
```

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


**それぞれの項目の意味**
表形式に変える↓

Field：カラム名

Type：データ型

Null：Nullの値を入れられるかどうか。できる場合は `YES`、できない場合は `NO`。

Key：インデックス設定されているかどうか。主キー（PRIMARY KEY）の場合は `PRI`、 UNIQUEインデックスの場合は `UNI`、同じ値が現れることが許可されているインデックスの場合は `MUL`。

Default：デフォルト値。なにも設定されていない場合は、最初に `NULL` が格納される

Extra：追加情報。`auto_increment` （指定したカラムに対してMySQLが自動的に一意のシーケンス番号を生成する機能）など。



### Laravelのログをコンテナログに表示
`backend/.env` を修正する

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


**logをみる方法3パターン**

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
+ laravel UIを追加する時の注意点
+ laravel6.*の場合はver.1がmust. これ以外のバージョンは非対応 
```
composer require laravel/ui "1.x" --dev
```

Laravelの初期設定。（wikiリンク→準備中）
