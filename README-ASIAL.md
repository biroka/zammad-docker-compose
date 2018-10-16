# zammad-docker-composeの改良版


## 設定

workディレクトリ以下に、stable-asial版のzammadを配置して下さい。

例：

```
$ cd work
$ git clone git@github.com:asial/zammad.git
$ cd zammad
$ git checkout -b stable-asial origin/stable-asial
```


ローカルで利用する場合は、ホストOSの8000ポートからゲストOSの80ポートにフォワーティングするために、
docker-compose.ymlのzammad-nginxのexposeのあとに

```
    ports:
      - "8000:80"
```

を追加して下さい。(expose自体は残しておく)

## 起動・終了・リスタート

```
$ docker-compose up -d
```

```
$ docker-compose down
```

```
$ docker-compose restart
```

## そのほかの注意事項

Dockerが利用出来るメモリも4G以上として下さい。

`vm.max_map_count=262144`としてください。

```
sysctl -w vm.max_map_count=262144
```

参考ページ：
https://docs.zammad.org/en/latest/install-docker-compose.html


## 改修のポイント

現在、起動処理が結構重いです。その理由は、起動スクリプト内で、

GEMのインストール
```
  bundle install
```

アセットのビルド
```
  bundle exec rake assets:precompile
```

などが行われているため。（特に、Nokogiri-1.8.5が重い）

起動スクリプトは
```
init-scripts/docker-entrypoint-asial.sh
```
が利用されている。(zammad-init, zammad-railsserver, zammad-scheduler, zammad-websocketの4サーバーのみ)
必要に応じて、この起動スクリプトを変更すればOK

将来的には、Nokogiri-1.8.5が組み込み済みのイメージを使った方が良いと思われる。そうすれば、`bundle install`の処理を
起動スクリプトから外すことが出来る。


