+++
date = '2025-02-08T22:24:51+09:00'
draft = false
title = 'Modifyrole'
+++

httpd roleにtemplateディレクトリを追加し、httpd.conf.j2を作成します

ディレクトリ構成
以下のような構成でファイルを配置します。

```
ansible_project/
├── ansible.cfg
├── inventory.ini
├── playbook.yml
└── roles/
    └── httpd/
    　　├── defaults/
    　　│   └── main.yml
    　　├── handlers/
    　　│   └── main.yml
    　　├── meta/
    　　│   └── main.yml
    　　├── tasks/
    　　│   └── main.yml
    　　└── templates/
    　　    └── httpd.conf.j2
```
httpd roleにtemplateディレクトリを追加します
Ansibleで管理するホストを指定するためのファイルを作成します。`inventory.ini`は、ホストの情報を記述するために使います。

Inventoryファイルの作成

```yaml
[web_servers]
web1 ansible_host=192.168.1.33
web2 ansible_host=192.168.1.34
```