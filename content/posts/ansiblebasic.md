+++
date = '2025-02-08T22:24:17+09:00'
draft = false
title = 'Ansiblebasic'
+++

# 2.1: インベントリファイル　invetory.ini を作成
Ansibleで管理するホストを指定するためのファイルを作成します。
`inventory.ini`は、変更対象のホストの情報を記述するために使います。

テスト環境で使用するインベントリファイルの作成

```yaml
[linux_servers]
web1 ansible_host=192.168.1.10
web2 ansible_host=192.168.1.11
```

        
このファイルにより、AnsibleがDockerコンテナに接続できるようになります。

ディレクトリ構成

以下のような構成でファイルを配置します。
git リポジトリをクローンしてきた場合は既に下記のような構成でディレクトリとファイルが配置されています。

```
ansible_project/
├── ansible.cfg
├── inventory.ini
├── playbook.yml
└── roles/
    └── httpd/
        └── tasks/
            └── main.yml
```
        
各ファイルの役割
ansible.cfg - Ansibleの設定ファイル
inventory.ini - 管理対象ホストのリスト
playbook.yml - 自動化タスクを定義
roles/httpd/tasks/main.yml - HTTPD（Apache）をインストール・設定するタスク