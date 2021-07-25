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

```
php artisan migrate
```
appコンテナ内で`php artisan migrate`

よく起こるmigration errorはwikiに切り出す
