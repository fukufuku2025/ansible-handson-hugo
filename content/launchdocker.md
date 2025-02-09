+++
date = '2025-02-08T22:24:43+09:00'
draft = false
title = 'Launchdocker'
+++

テスト用のDockerコンテナをリポジトリファイルをバインドして起動します。このコンテナ上でAnsibleを使用して操作します。

コンテナの作成と実行

```bash
$ docker compose up -d
$ docker exec -it ansible sh

# cd ansible_project
```
        
これで、`ansible`という名前のコンテナが起動し、その中にアクセスすることができます。