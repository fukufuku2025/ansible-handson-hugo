+++
date = '2025-02-08T22:24:17+09:00'
draft = false
title = 'Ansiblebasic'
+++

Ansibleで管理するホストを指定するためのファイルを作成します。`inventory.ini`は、ホストの情報を記述するために使います。

Inventoryファイルの作成

```yaml
[web_servers]
web1 ansible_host=192.168.1.33
web2 ansible_host=192.168.1.34
```

        
このファイルにより、AnsibleがDockerコンテナに接続できるようになります。

ディレクトリ構成
以下のような構成でファイルを配置します。

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