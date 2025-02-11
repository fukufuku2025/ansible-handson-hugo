+++
date = '2025-02-08T22:24:43+09:00'
draft = false
title = 'Launchdocker'
+++


## 2.1 docker-compose.ymlの記載
検証環境の構築はdocker compose を使用して構築します。
docker-compose.ymlファイルの中身を確認しておきます。
のちほどビルドするansible-study-image:latest の名前とタグで構築したイメージを使用し
ターゲット用のコンテナとansibleサーバー用のコンテナ、dockerネットワークを作成します。

```yml
version: '3.8'

services:
  svx01:
    image: ansible-study-image:latest
    command: /sbin/init
    privileged: true
    container_name: ${EMPLOYEE_NUMBER}-svx01
    networks:
      priv:
        ipv4_address: ${IPV4_SUBNET}.10

  svx02:
    image: ansible-study-image:latest
    command: /sbin/init
    privileged: true
    container_name: ${EMPLOYEE_NUMBER}-svx02
    networks:
      priv:
        ipv4_address: ${IPV4_SUBNET}.11

  ansible:
    image: docker.io/pycontribs/centos:8
    command: /sbin/init
    privileged: true
    container_name: ${EMPLOYEE_NUMBER}-ansible
    networks:
      priv:
        ipv4_address: ${IPV4_SUBNET}.100

networks:
  priv:
    name: ${EMPLOYEE_NUMBER}_priv
    driver: bridge
    ipam:
      config:
        - subnet: ${IPV4_SUBNET}.0/24
          gateway: ${IPV4_SUBNET}.254
```


クローンしたリポジトリの.envファイルを確認しておきます。
下記のようになっていればOKです。
```bash
EMPLOYEE_NUMBER=dev
IPV4_SUBNET=192.168.18
```

## 2.2 検証用イメージの作成
検証用のイメージを作成するDockerfileの内容も確認します。
DockerHubに登録してあるcentos:centos8.4.2105のイメージをベースに
openssh-clients,openssh-serverを追加し有効化したイメージを作成します。
```Dockerfile
FROM centos:centos8.4.2105

RUN sed -i -e 's/^enabled\s*=\s*1/enabled = 0/g' \
  /etc/yum.repos.d/CentOS-Linux-Extras.repo && \
  sed -i -e 's/^mirrorlist=\(.*\)$/#mirrorlist=\1/g' \
  -e 's|^#baseurl=http://mirror.centos.org\(.*\)$|baseurl=https://vault.centos.org\1|g' \
  /etc/yum.repos.d/CentOS-Linux-BaseOS.repo \
  /etc/yum.repos.d/CentOS-Linux-AppStream.repo

RUN dnf -y install sudo openssh-clients openssh-server && \
  systemctl enable sshd

CMD ["/sbin/init"]

```

検証環境用のdocker imageをbuildしておきます。

```bash
docker build -t ansible-study-image:latest .
```


テスト用のDockerコンテナをリポジトリファイルをバインドして起動します。このコンテナ上でAnsibleを使用して操作します。

コンテナの作成と実行

```bash
$ docker compose up -d
```

下記のような感じでコンテナとネットワークがビルドされるはずです。
```bash
[+] Running 4/4
 ✔ ansible Pulled                                                                                                                                                                                                                                                     18.1s 
   ✔ 7a0437f04f83 Pull complete                                                                                                                                                                                                                                        5.7s 
   ✔ 5758c96730b4 Pull complete                                                                                                                                                                                                                                        6.0s 
   ✔ a8e565328d4f Pull complete                                                                                                                                                                                                                                       15.4s 
[+] Running 5/5
 ✔ Network dev_priv                                                                                                                                       Created                                                                                                      0.0s 
 ✔ Container dev-svx02                                                                                                                                    Started                                                                                                      0.6s 
 ✔ Container dev-svx01                                                                                                                                    Started                                                                                                      0.5s 
 ✔ Container dev-ansible                                                                                                                                  Started  
 ```

 ## 2.3 ansibleユーザーの作成
 
今回は学習用として、手動で ansible ユーザを作成してみます
docker container cp にてローカル環境のファイルを ansible コンテナへコピーします
docker container exec にて先ほどコピーした shell をコンテナで実行させます
dev は　.env に記載した文字列と置き換えて実行してください
```bash
% for svr in svx01 svx02 ansible; do
> echo $svr
> docker container cp ./ansible_client_setup.sh dev-${svr}:/tmp/
> docker container exec dev-${svr} sh /tmp/ansible_client_setup.sh
> done
```

正しくansibleユーザーが作成されたか確認します。
成功していれば下記のように出力されます。
```bash
svx01
Successfully copied 2.56kB to dev-svx01:/tmp/
groupadd ansible
useradd ansible
created /etc/sudoers.d/ansible
svx02
Successfully copied 2.56kB to dev-svx02:/tmp/
groupadd ansible
useradd ansible
created /etc/sudoers.d/ansible
ansible
Successfully copied 2.56kB to dev-ansible:/tmp/
groupadd ansible
useradd ansible
created /etc/sudoers.d/ansible
```



## 2.4 ssh暗号鍵設定
Docker コンテナへの接続
docker container exec にて ansible 実行コンテナに接続
ansible ユーザにスイッチし、公開鍵を作成、svx01, svx02 のサーバにコピーし、コンテナの接続から抜けます

```
% docker container exec -it dev-ansible /bin/bash
[root@ccf3d9c92518 /]# su - ansible
[ansible@ccf3d9c92518 ~]$ mkdir -m 700 .ssh
[ansible@ccf3d9c92518 ~]$ ssh-keygen -t rsa -f .ssh/id_rsa -N ''
[ansible@ccf3d9c92518 ~]$ ssh-copy-id svx01
[ansible@ccf3d9c92518 ~]$ ssh-copy-id svx02
[ansible@ccf3d9c92518 ~]$ exit
[root@ccf3d9c92518 /]# exit
```

        
ansible@ccf3d9c92518 はコンテナ ID なので、コンテナ毎に変わります
ssh-copy-id コマンドで ansible ユーザのパスワードが聞かれたら ansiblePassw0rd と入力してください。



## 2.5 studyディレクトリのコピーとansible pingでの実行環境確認
docker exec で ansible 実行サーバにログインし、ansbile ユーザへスイッチ、ディレクトリを移動します
playbook を実行する前に、疎通がとれるかを ansible コマンドにて確認します
```bash
$ docker container cp ../study/ dev-ansible:/home/ansible/
$ docker container exec -it dev-ansible /bin/bash
[root@ccf3d9c92518  study]# su - ansible
[ansible@accf3d9c92518  ~]$ cd study/
[ansible@ccf3d9c92518  study]$ ansible all -i inventory.ini -m ping
```

ansible ping が成功した際には下記のようにpongの返信があります。
```bash
svx02 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": false,
    "ping": "pong"
}
svx01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/libexec/platform-python"
    },
    "changed": false,
    "ping": "pong"
}
```




## 2.6 コンテナ環境を削除する場合削除


コンテナから抜けて、dockerb compose にてコンテナを削除します

[ansible@ccf3d9c92518 study]$ exit
[root@accf3d9c92518 /]#
$
$ docker compose down