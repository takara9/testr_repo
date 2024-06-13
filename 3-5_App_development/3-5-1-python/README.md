# 3.5.1 Pythonで開発するREST-APIサーバー

## イメージのビルドと実行

```
docker build -t ex1:1.6 .
docker run --name ex1 --publish 9100:9100 --detach ex1:1.6
```

## アクセス

```
curl http://localhost:9100/ping;echo
```

## コンテナへ入る

```
docker exec -it ex1 bash
```

## イメージをレジストリへ登録

```
export CR_PAT=YOUR_TOKEN
export USERNAME=YOUR USERID
echo $CR_PAT | docker login ghcr.io -u $USERNAME --password-stdin
docker tag ex1:1.6 ghcr.io/takara9/ex1:1.6
docker push ghcr.io/takara9/ex1:1.6
```

## クリーンナップ

```
docker stop ex1
docker rm ex1
docker rmi ghcr.io/takara9/ex1:1.6
docker rmi ex1:1.6
```


## コードの更新方法

```
$ git clone git@github.com:takara9/ex1.git
$ git checkout -b add_api
$ git branch

# <コードの更新や追加>

$ git add .
$ git status
$ git commit -m "Add new api"
```
CIが成功したら、add_apiブランチをmainブランチへマージする


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
TAG=1.6
$ git tag -a $TAG -m "version $TAG"
$ git push origin $TAG
```

コンテナのイメージを、タグを選定してリリースする。
