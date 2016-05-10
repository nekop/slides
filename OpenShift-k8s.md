# Beginners Guide to OpenShift



# Takayoshi Kimura
## OpenShift / JBoss / WildFlyの中の人
### twitter: [@nekop](https://twitter.com/nekop)



## OpenShiftとは
<!-- .slide: data-background="images/openshift-jjug/openshift_reverse.png" data-background-size="512px" data-background-position="top right" -->

- Docker / KubernetesベースのオープンソースPaaS (Platform as a Service)ソフトウェア
- Docker / Kubernetesに、「簡単にみんなで使う」ために欠けている要素を追加したもの



![origin](images/openshift-jjug/origin-overview.png)



## OpenShift v3
<!-- .slide: data-background="images/openshift-jjug/openshift_reverse.png" data-background-size="512px" data-background-position="top right" -->

- みんなで使えるDocker / Kubernetes
  - ユーザ管理、マルチテナント、ネットワークの提供とアクセスコントロール
- 基本的な機能
  - Dockerイメージのビルド、より柔軟なデプロイメント
- Win/Mac/Linuxどれでも動くポータブルクライアント `"oc"` とWeb Console
- 改良版Dockerレジストリとイメージのバージョン管理
  - イメージのセキュリティアップデートと自動リビルド/アップデート



## Build

- Source-to-Image (s2i)
- Docker
- Custom



## デモ

- ソースコードからビルド
- テンプレート指定してビルド
- リソースモニタリング
- ローリングアップデート
- オートスケール



## OpenShiftのつかいかた

- コマンドラインでソースコードからビルドしてデプロイ
  - `oc new-app https://github.com/nekop/hello-sinatra`
  - `oc expose service hello-sinatra`



![new-app](images/openshift-jjug/newapp.png)



## OpenShiftのつかいかた

- ソースツリーだけではなくDockerイメージやDockerfileも丸投げ可
  - `oc new-app sonatype/nexus`
  - `oc expose service nexus`



![nexus](images/openshift-jjug/nexus.png)



## OpenShiftのつかいかた

- Webコンソールから



![webconsole](images/openshift-jjug/webconsole.png)



## OpenShiftのつかいかた

- テンプレートでデータベースやストレージ利用の指定
  - `oc new-app -f https://raw.githubusercontent.com/goern/mattermost-openshift/centos7/mattermost.yaml`



![mattermost](images/openshift-jjug/mattermost.png)



![mattermost-webconsole](images/openshift-jjug/mattermost-webconsole.png)



## その他のトピック