+++
date = '2025-02-08T22:24:51+09:00'
draft = false
title = 'Modifyrole'
+++


## 6.1 roleのタスクの追記

roleのタスク定義にテンプレートファイルから設定ファイルを配布するタスクを追記します。

**`main.yml`**
```yaml
- name: installed my httpd
 dnf:
    name: httpd
    state: installed

- name: ensure may httpd config
  template:
    src: httpd.conf.j2
    dest: /etc/httpd/conf/httpd.conf
```



## 6.2 Templateファイルの配置
httpd roleにtemplatesディレクトリを追加し、httpd.conf.j2を作成します

ディレクトリ構成
以下のような構成でファイルを配置します。

```
study/
├── ansible.cfg
├── inventory.ini
├── playbook.yml
└── roles/
    └── httpd/
    　　├── tasks/
    　　│   └── main.yml
    　　└── templates/
    　　    └── httpd.conf.j2
```
httpd roleにtemplateディレクトリを追加します
Ansibleで管理するホストを指定するためのファイルを作成します。`inventory.ini`は、ホストの情報を記述するために使います。


再度コンテナにroleのmain.ymlとhttd.conf.j2を含むディレクトリをコピーしておきます。
```bash
$ docker container cp ../study/ dev-ansible:/home/ansible/
```

httpd.conf.j2の実態はApacheのconfファイルです。

**`httpd.conf.j2`**
```conf
# Apache HTTP Server Configuration Template

ServerRoot "/etc/httpd"

Listen 80

# ServerName を変数で動的に指定
{% if server_name is defined %}
ServerName {{ server_name }}
{% else %}
ServerName localhost
{% endif %}

# ドキュメントルートを変数で指定
DocumentRoot "{{ document_root | default('/var/www/html') }}"

<Directory "{{ document_root | default('/var/www/html') }}">
    AllowOverride None
    Require all granted
</Directory>

# ログの設定
ErrorLog "{{ log_dir | default('/var/log/httpd') }}/error.log"
CustomLog "{{ log_dir | default('/var/log/httpd') }}/access.log" combined

# MIME タイプ設定
TypesConfig /etc/mime.types

# モジュールの読み込み
LoadModule dir_module modules/mod_dir.so
LoadModule mime_module modules/mod_mime.so
LoadModule log_config_module modules/mod_log_config.so

# デフォルトのインデックスページ
<IfModule dir_module>
    DirectoryIndex index.html index.php
</IfModule>

# KeepAlive 設定
KeepAlive On
MaxKeepAliveRequests 100
KeepAliveTimeout 5

# 仮想ホストの設定
<VirtualHost *:80>
    ServerAdmin {{ admin_email | default('webmaster@localhost') }}
    DocumentRoot "{{ document_root | default('/var/www/html') }}"
    ServerName {{ server_name | default('localhost') }}

    ErrorLog "{{ log_dir | default('/var/log/httpd') }}/error.log"
    CustomLog "{{ log_dir | default('/var/log/httpd') }}/access.log" combined
</VirtualHost>
```