+++
date = '2025-02-08T22:24:57+09:00'
draft = false
title = 'Role'
+++

## 4.1 Roleの作成
Roleを使ってタスクを整理し、再利用可能な構造にします。まず、`httpd`を管理するRoleを作成します。

ディレクトリ構造

```
roles/
└── httpd/
    ├── tasks/
    │   └── main.yml
    ├── templates/
```
        
roles/httpd/tasks/main.yml の内容
作成した `main.yml` に、Apache（httpd）のインストールと起動のタスクを記述します。

**`main.yml`**
```yaml
- name: installed my httpd
 dnf:
    name: httpd
    state: installed
```
        
これにより、dnfを使ってApacheがインストールされ、httpdパッケージをインストールせよという状態になります。
