
## PHPとNginxで作るWebサービスのコンテナ

NginxとPHPのコンテナの二つを使用して、動的Webページを表示するコンテナセットを作成します。

```
3-5-2-php$ tree
.
├── html
│   └── index.php
├── nginx
│   ├── config
│   │   ├── default.conf
│   │   └── nginx.conf
│   └── Dockerfile
├── php
│   ├── config
│   │   └── php.ini
│   └── Dockerfile
└── README.md
```


## イメージのビルド
```
docker build -t ex2-php:1.0 php
docker build -t ex2-nginx:1.0 nginx
```

## 実行
```
docker network create --driver bridge php-net
docker run -d -v ${PWD}/html:/usr/share/nginx/html --network=php-net --name php-fpm ex2-php:1.0
docker run -d -v ${PWD}/html:/usr/share/nginx/html --network=php-net --publish 9200:9200 --name nginx ex2-nginx:1.0
```

## アクセス

curlコマンド、または、ブラウザから以下のアドレスをアクセスします。
- curl http://localhost:9200/


## コンテナへ入る
```
docker exec -it nginx sh 
docker exec -it php-fpm sh
```

## イメージをレジストリへ登録
```
export CR_PAT=YOUR_TOKEN
export USERNAME=YOUR USERID 
echo $CR_PAT | docker login ghcr.io -u $USERNAME --password-stdin
docker tag ex2-php:1.0 ghcr.io/takara9/ex2-php:1.0
docker push ghcr.io/takara9/ex2-php:1.0
docker tag ex2-nginx:1.0 ghcr.io/takara9/ex2-nginx:1.0
docker push ghcr.io/takara9/ex2-nginx:1.0
```

## クリーンナップ
```
docker stop php-fpm
docker rm php-fpm
docker rmi ex2-php:1.0
docker stop nginx
docker rm nginx
docker rmi ex2-nginx:1.0
docker network rm php-net
docker rmi ghcr.io/takara9/ex2-php:1.0
docker rmi ghcr.io/takara9/ex2-nginx:1.0
```

## GitHubでのリリース方法
メインブランチへ移動してコードを最新化する。そして、ブランチを削除

```
$ git checkout main
$ git pull
$ git branch -d update_branch
```


リリースするTAGを設定する。
ここで付与するTAGはコンテナイメージのタグになるので、リポジトリを確認して、タグ名を決めること。

```
TAG=1.x
$ git tag -a $TAG -m "version $TAG"
$ git push origin $TAG
```

コンテナのイメージを、タグを選定してリリースする。
キャッシュされているので、失敗したときは、tagを一旦削除して、PRから作り直す
