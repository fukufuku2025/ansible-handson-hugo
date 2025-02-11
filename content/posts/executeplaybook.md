+++
date = '2025-02-08T22:24:26+09:00'
draft = false
title = 'Executeplaybook'
+++

Roleを更新し実行します。

```bash
$ docker exec -it dev-ansible /bin/bash
[root@ccf3d9c92518 /]# su - ansible
[ansible@ccf3d9c92518 ~]$ cd study
$ ansible-playbook -i inventory.ini playbook.yml
```

実行結果を確認します
```bash
[ansible@ccf3d9c92518 study]$ ansible-playbook -i inventory.ini playbook.yml
[WARNING]: log file at /home/ansible/ansible_projects/ansible.log is not writeable and we cannot create it, aborting


PLAY [Deploy Apache Server] ************************************************************************************************************************************************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************************************************************************************************************************************************
ok: [svx02]
ok: [svx01]

TASK [httpd : Install Apache] **********************************************************************************************************************************************************************************************************************************************
ok: [svx02]
ok: [svx01]

TASK [ensure may httpd config] *********************************************************************************************************************************************************************************************************************************************
changed: [svx02]
changed: [svx01]

PLAY RECAP *****************************************************************************************************************************************************************************************************************************************************************
svx01                      : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
svx02                      : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

テンプレートの配布を確認します。

```bash
[ansible@ccf3d9c92518 study]$ ssh ansible@svx01
Last login: Tue Feb 11 08:00:17 2025 from 192.168.18.100
[ansible@5e6c6bb7e57d ~]$ ls -la /etc/httpd/conf
total 28
drwxr-xr-x 2 root root  4096 Feb 11 08:00 .
drwxr-xr-x 5 root root  4096 Feb 11 07:26 ..
-rw-r--r-- 1 root root  1084 Feb 11 08:00 httpd.conf
-rw-r--r-- 1 root root 13064 Nov 12  2021 magic
[ansible@5e6c6bb7e57d ~]$ ls -la /etc/httpd/conf/httpd.conf
-rw-r--r-- 1 root root 1084 Feb 11 08:00 /etc/httpd/conf/httpd.conf
[ansible@5e6c6bb7e57d ~]$ cat /etc/httpd/conf/httpd.conf
# Apache HTTP Server Configuration Template

ServerRoot "/etc/httpd"

Listen 80

# ServerName を変数で動的に指定
ServerName localhost

# ドキュメントルートを変数で指定
DocumentRoot "/var/www/html"

<Directory "/var/www/html">
    AllowOverride None
    Require all granted
</Directory>

# ログの設定
ErrorLog "/var/log/httpd/error.log"
CustomLog "/var/log/httpd/access.log" combined

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
    ServerAdmin webmaster@localhost
    DocumentRoot "/var/www/html"
    ServerName localhost

    ErrorLog "/var/log/httpd/error.log"
    CustomLog "/var/log/httpd/access.log" combined
</VirtualHost>

```


```bash
[ansible@ccf3d9c92518 study]$ ssh ansible@svx02
Last login: Tue Feb 11 08:00:17 2025 from 192.168.18.100
[ansible@8d05b3e5ed52 ~]$ cat /etc/httpd/conf/httpd.conf
# Apache HTTP Server Configuration Template

ServerRoot "/etc/httpd"

Listen 80

# ServerName を変数で動的に指定
ServerName localhost

# ドキュメントルートを変数で指定
DocumentRoot "/var/www/html"

<Directory "/var/www/html">
    AllowOverride None
    Require all granted
</Directory>

# ログの設定
ErrorLog "/var/log/httpd/error.log"
CustomLog "/var/log/httpd/access.log" combined

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
    ServerAdmin webmaster@localhost
    DocumentRoot "/var/www/html"
    ServerName localhost

    ErrorLog "/var/log/httpd/error.log"
    CustomLog "/var/log/httpd/access.log" combined
</VirtualHost>
```

環境から出ます
```bash

[ansible@8d05b3e5ed52 ~]$ exit
logout
Connection to svx02 closed.
[ansible@ccf3d9c92518 study]$ exit
logout
[root@ccf3d9c92518 /]# exit
exit
```