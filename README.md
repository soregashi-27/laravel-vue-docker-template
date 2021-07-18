# composerのバージョンアップはやめとく

## きっかけ
LaravelをインストールするときにこのURL付きのメッセージが来たのでアップデートをしようか検討した。
https://blog.packagist.com/deprecating-composer-1-support/

## 調査
Composerのバージョンアップの方法、stable versionについて調査した。

コード状態を戻す場合に出てきた問題のメモ
```
laravelインストールの変更を戻すために触っているとこうなった。

VSCodeのSourceControlが５kと表示された時の対処法と今後の対策

https://bit.ly/3kwoLVh



ホームディレクトリに作成された.gitフォルダを削除

（[command] + [shift] + [.(ドット)]を同時押し）

→見つからなかったので、vscodeを再起動した。

→解決
```

## 調査結果：1系のままで開発をする
composerを1系から2系に上げたかったけど、調査をしてみると予期せぬエラーが出たりしそうなので、今回のポートフォリオ作成時のcomposerは1系でいくことにする。
予期せぬエラーで莫大な工数食いそうだったので、こういう判断をした。

参考: \
https://qiita.com/ucan-lab/items/5a48f1a3c7a4c358223e

https://qiita.com/nyokinyoki1848/items/8f7180919ffeb02c814d


Composer2系のパフォーマンスが良いのは明らかなので、LTSのバージョンが上がったら使ってみるのはあり。
https://blog.packagist.com/composer-2-0-is-now-available/

https://qiita.com/ucan-lab/items/289009ffe5bb417c808e
