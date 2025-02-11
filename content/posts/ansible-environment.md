+++
date = '2025-02-08T22:24:02+09:00'
draft = false
title = 'Ansible Environment'
+++


この章では、開発に必要なツールをセットアップします。  
これらのツールを使用して、効率的に開発を進めるための準備を行います。  
ざっくり記載してるので環境構築の詳細はAIとかに聞いてください  

# 1.1: Visual Studio Code (VSCode) のインストール  
Visual Studio Code（VSCode）は、軽量で強力なコードエディタです。 以下の方法でインストールできます。  

- Microsoft Storeからインストールする場合：  
  Microsoft StoreからVSCodeをインストール  
  Microsoft Storeを開き、「Visual Studio Code」を検索し、インストール  
            
              
- 公式ページからインストールする場合：  
公式サイトからVSCodeをダウンロード
https://code.visualstudio.com/ 
からインストーラーをダウンロードし、インストール
            
# 1.2: Dockerのインストール  
Dockerを使用してコンテナを簡単に構築・管理できます。  
今回は下記のバージョンを使用します。 

```bash
$ docker -v
Docker version 26.1.3, build 26.1.3-0ubuntu1~24.04.1 
```
        
# 1.3: git リポジトリのクローン
git clone でリポジトリをalpien Linuxの中にクローンする

WSL2のalpine linuxの中でリポジトリをクローンするには下記のコマンドを実行します

```bash
$ git clone https://github.com/fukufuku2025/ansible-hands-on.git
```