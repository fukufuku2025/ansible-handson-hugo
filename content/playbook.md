+++
date = '2025-02-08T22:25:02+09:00'
draft = false
title = 'Playbook'
+++

プレイブックを使って、Ansibleで実行するタスクを記述します。この例では、`httpd`をインストールするタスクをroleで定義したのでそれを呼ぶように記載します。

Playbookの作成

```yaml
- name: Deploy Apache Server
  hosts: all
  become: yes
  roles:
　  - httpd
```
          
        
このPlaybookは、`alll`ホスト（Dockerコンテナ）に対して`httpd`をインストールし、サービスを開始するタスクを実行します。

ホスト間の疎通確認の実行方法

```bash
$ ansible all -i inventory.ini -m ping
```
        