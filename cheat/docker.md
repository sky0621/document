# バージョン
<pre>
$ sudo docker version
Client:
 Version:      17.06.0-ce
 Go version:   go1.8.3
 OS/Arch:      linux/amd64

Server:
 Version:      17.06.0-ce
 Go version:   go1.8.3
 OS/Arch:      linux/amd64
</pre>

# 利用できるイメージ

$ sudo docker images

# イメージの詳細

$ sudo docker inspect s12v/elasticmq

# イメージを探す

$ sudo docker search elasticmq

# イメージを取得

$ sudo docker pull s12v/elasticmq

# プロセス確認

$ sudo docker ps

# コンテナ起動

$ sudo docker run -p 9324:9324 s12v/elasticmq

# コンテナ外と通信

$ docker port xxxxx

# Dockerfileに基づきイメージ作成

$ sudo docker build -t xxxx .

# 不要になったローカルのイメージファイルを削除

$ sudo docker rmi xxxx


