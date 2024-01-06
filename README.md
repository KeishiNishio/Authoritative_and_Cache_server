# NSDベースのDNSサーバー構築プロジェクト

このリポジトリは、NSD (Name Server Daemon) を使用してDNSサーバーを構築するための設定ファイル群。
Dockerを使用して独自のDNSサーバー環境を構築し、NSDを権威DNSサーバーとして、UnboundをキャッシュDNSサーバーとして設定

## 技術スタック

- Docker
- Docker Compose
- NSD (権威DNSサーバー)
- Unbound (キャッシュDNSサーバー)

## システム構成

- **権威DNSサーバー（NSD）**: ドメインに対する名前解決行う。 `example.com`のゾーンファイルを含んでおり、このドメインに関する情報を管理 
- **キャッシュDNSサーバー（Unbound）**: DNSクエリの結果を一時的に保存し、効率的な名前解決を実現。キャッシュサーバーは外部DNSサーバーへのクエリ転送も行う


### **システム構成図**
![システム構成図](https://github.com/KeishiNishio/Authoritative_and_Cache_server/blob/main/systemimage.png)

### **NSDの選択理由**

従来は、BINDがDNSの実装に広く使用されてきたようですが、BINDは脆弱性が度々発見され、セキュリティ的に運用が難しいことから、以下の観点から優れた代替手段としてNSDを選定しました。

- BINDに比べて、高度なセキュリティ
- 読み取り専用の機能に特化し、大規模な環境でも高速で安定したパフォーマンス
- BINDと比較し、設定がシンプル

参考サイト

[https://qiita.com/ohhara_shiojiri/items/497aaba989151fa84b3d](https://qiita.com/ohhara_shiojiri/items/497aaba989151fa84b3d)


## ファイル構成
以下に、主要なファイルとディレクトリの概要を示す

- `docker-compose.yml`
  - このDocker Composeファイルには、NSD（Name Server Daemon）およびUnbound DNSサービスのDocker設定。このファイルでは、各サービスのビルド設定、ポートマッピング、ネットワーク設定などを定義。

- `authoritative/`
  - 権威DNSサーバーに関連するファイル群
    - `Dockerfile`: NSDのインストールと設定ファイルの配置を指示するDockerファイル
    - `nsd.conf`: NSDの設定を定義
    - `zonefile.zone`: DNSゾーン情報

- `cache/`
  - このディレクトリには、キャッシュDNSサーバーに関連
    - `Dockerfile`: Unbound DNSキャッシュサーバーのインストールと設定ファイルの配置を指示するDockerファイル
    - `unbound.conf`: Unboundの設定を定義

- `README.md`
- `component.txt`: ファイル構成を記述

## 挙動

1. クライアントからのDNSクエリはまずキャッシュサーバー（Unbound）に送られる
2. キャッシュサーバーは、キャッシュに回答がない場合、権威サーバー（NSD）にクエリを転送 
3. 権威サーバーは`example.com`に関するクエリに回答し、その結果はキャッシュサーバーによって保存される

## 確認方法

システムの動作確認は以下の手順で行い

1. DockerおよびDocker Composeを使用してサーバーを起動 
2. `dig`コマンドを使用して、`example.com`に対するクエリをキャッシュサーバーに送信 
3. 同じクエリを再度送信し、応答時間が短縮されていることを確認（キャッシュの効果）。

## セットアップ手順
    
1. **Dockerイメージのビルド**: 次に、提供された`Dockerfile`を使用してDockerイメージをビルド 
    
    ```
    docker-compose build --no-cache
    ```
    
2. **Dockerコンテナの起動**: 以下のコマンドでDockerコンテナを起動 
    
    ```
    docker-compose up -d
    ```


### 挙動の確認

以下のコマンドを実行すると、対応するIPアドレスが返送される

```bash
dig @localhost example.com
dig @localhost google.com

```


## 他便利なコマンド

1. システム全体のDockerキャッシュを削除

```bash
docker system prune --all --force
```

1. 設定したネットワークにコンテナが所属しているか確認

```bash
docker network inspect nginx-network
```

1. コンテナの状況確認

```bash
docker ps -a
```

1. 存在する全てのコンテナを停止

```bash
docker stop $(docker ps -aq) || true
```

1. 存在する全てのコンテナを削除

```bash
docker rm -f $(docker ps -aq) || true
```
