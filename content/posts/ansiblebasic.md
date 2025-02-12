+++
date = '2025-02-08T22:24:17+09:00'
draft = false
title = 'Ansiblebasic'
+++

# 3.1: インベントリファイル　invetory.ini を作成・配置
Ansibleで管理するホストを指定するためのファイルを作成します。
`inventory.ini`は、変更対象のホストの情報を記述するために使います。

```
cd study
vi inventory.ini
```

テスト環境で使用するインベントリファイルの作成

**`inventory.ini`**
```yaml
[linux_servers]
svx01 ansible_host=192.168.18.10
svx02 ansible_host=192.168.18.11
```

        
このファイルにより、AnsibleがDockerコンテナに接続できるようになります。
## 3.2 ansible.cfg ファイルを作成・配置

下記のようにインベントリファイルのパスとロールのパスを記載しておきます。
```
[defaults]
inventory= ./inventory.ini
roles_path= ./study/roles
host_key_checking = False
log_path = ~/study/ansible.log
```

## 3.3 ディレクトリ構成とファイルの役割の再確認

以下のような構成でファイルを配置します。
※git リポジトリをクローンしてきた場合は既に下記のような構成含んだディレクトリとファイルが配置されています。

```
study/
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