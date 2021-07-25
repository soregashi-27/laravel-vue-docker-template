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

・もう一度ローカルホストへ接続して500 | servert errorが出ることを確認する（正しい）

　→`composer install` 時は `.env` 環境変数ファイルは作成されないので、 `.env.example` を元にコピーして作成する

・app container内で`.envファイル`を作成 `cp .env.example .env`を入力

　→Your app is missingというエラーが出る（これも正しい）

・アプリケーションKeyを作成するために、app containerを立ち上げる`docker compose exec app bash`

・`php artisan key:generate`でアプリケーションKeyを作成する

　→
