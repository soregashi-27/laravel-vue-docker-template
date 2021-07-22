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
