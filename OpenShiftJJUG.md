# OpenShiftでかんたんDocker PaaS



# Takayoshi Kimura
## OpenShift / JBoss / WildFlyの中の人
### twitter: @nekop



## OpenShiftとは

- Docker / KubernetesベースのPaaS (Platform as a Service)ソフトウェア
- Docker / Kubernetesに、「みんなで使う」ために欠けている要素を追加したもの



## 対象ユーザおよび組織

- Dockerをもっと気軽に使いたい
- Dockerのビルドもデプロイも自動的にやってほしい
- プロジェクトごとのソフトウェアスタックをいちいちセットアップするの面倒
- リソースをもっと集約してコンピューティングリソースを増やしたい
- オンプレ(非従量課金)で開発でもガンガン使えるPaaS欲しい
- チャットやイシュートラッカーのようなソフトウェアを気軽にホストしたい
- CI / CD基盤が欲しい



## OpenShift

- Origin
  - 開発コミュニティ
- Online (現状旧バージョン)
  - Red Hatがホスティングするpublic PaaS
- Enterprise
  - オンプレミス向けPaaS製品
- Dedicated
  - Red HatがAWS上でホスティングするEnterprise



## OpenShift Online (旧バージョン)

- Dockerではないバージョン, RHEL 6ベース
- 3ギアまで無料
  - 1ギア = 1 CPU, 512 MB mem, 1GB disk
- これはこれで便利
- クライアントコマンドが"rhc"
  - v3は"oc"



## Docker

- ポータブルなイメージフォーマットと共有サービス
- セットアップガイド、インストールガイドの不要な世界
- 「ほぼセットアップ済み」ソフトウェアの流通
- VMより軽量




## Docker

- 基本LinuxなのでWin/MacではVMでLinuxを利用するしかない
  - セットアップが手間
  - VMより軽量という利点が消える



## Kubernetes

- Docker + クラウドコンピューティング
- 柔軟なコンテナのデプロイ



## OpenShift

- みんなで使えるDocker / Kubernetes
  - ユーザ管理、マルチテナント、ネットワークの提供
- Win/Mac/Linuxどれでも動くポータブルクライアントとWeb Console
- 改良版Dockerレジストリとイメージのバージョン管理
- セットアップ簡単



## 開発者から見たOpenShift

- OnlineはJava EEアプリケーションを無料でデプロイできる数少ないPaaS
- 個人マシンにRDBMSとかセットアップするの面倒
- コンパイル、テスト、デプロイなどのワークロードは個人マシンではなく余裕のあるクラスタへ投げたい
- プロジェクトの増援部隊に環境をすぐにセットアップして渡したい
- 開発者レベルでは従量課金クラウドは自由に使えない、オンプレのコンピューティングリソースを有効活用したい



## OpenShiftのつかいかた

- コマンドラインでソースコードからビルドしてデプロイ
  - `oc new-app https://github.com/nekop/hello-javaee`
  - `oc expose service hello-javaee`
- ソースツリーだけではなくDockerfileも丸投げ可

TODO some pics of web console



## OpenShiftのつかいかた

- Webコンソールから

TODO some pics of web console



## OpenShiftのつかいかた

- テンプレートでデータベースやストレージ利用の指定
  - `oc new-app -f https://github.com/nekop/openshift-sandbox/blob/master/apps/mattermost/mattermost.yaml`

TODO some pics of mattermost
TODO some pics of web console



## OpenShift Online v3

### 近日リリース予定！


