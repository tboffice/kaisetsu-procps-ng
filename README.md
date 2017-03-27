# 解説Procps-ng

このリポジトリは同人サークル[第7開発セクション](https://sites.google.com/site/dai7sec/ "第7開発セクション")が[技術書典3](https://techbookfest.org/event/tbf03) (2017/10/22)で発行した同人誌「解説Procps-ng」の原稿です。

[Procps-ng](https://gitlab.com/procps-ng/procps "procps")のマニュアルを筆者([@tboffice](https://twitter.com/tboffice))がひと通り読んで、便利そうなところや注意点などをまとめました。


# ビルド手順

- 自力で本文のPDFを作るときはこんな感じでできるはず
- sphinxとdvipdfmxが入った環境が必要です。あと日本語フォントの雑に設定しておくといいかも

```
rm -rf kaisetsu-procps-ng
git clone https://github.com/nanaka-inside/kaisetsu-procps-ng.git
cd kaisetsu-procps-ng
PATH=${PATH}:/usr/local/texlive/2015/bin/x86_64-linux/ sh ./latex-build.sh
```

# その他

- PRはいつでも歓迎
