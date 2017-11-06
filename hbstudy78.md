# Kubernetes
# アンチパターン



# Takayoshi Kimura
# [@nekop](https://twitter.com/nekop)



## お前誰よ？

- Takayoshi Kimura [@nekop](https://twitter.com/nekop)
- OpenShift Senior Software Maintenance Engineer, Red Hat
- Kubernetes/OpenShiftﾁｮｯﾄﾃﾞｷﾙ
- Java EEﾁｮｯﾄﾃﾞｷﾙ



## OpenShiftって何よ？

- Enterprise Kubernetes for Developers
- UI強化、yamlほとんど書かなくていい
- コンテナイメージのビルドやってくれる
- リクエストルーティング、レジストリ、ネットワーキング、ログ集約、メトリクスなど標準装備



## 問題

- ノードのディスクフル確定の200GBのイメージをk8sクラスタへデプロイしたらどうなる？




## Kubernetesアンチパターン

- セットアップと運用
- リソース
- Application



## セットアップと運用

- インストーラ選択
  - HA構成
  - マスター追加、削除
  - ノード追加、削除
  - etcd追加、削除
  - アップグレード



## セットアップと運用

- The Hard Way
- Kops
- Kube-aws
- Kubespray
- Rancher
- etc.



## セットアップと運用

- K8sクラスタが有効活用されてくると、死んだ時のダメージが大きい
- バックアップリストア検証大事
- 開発、テスト、プロダクションクラスタを分ける



## サイジング

- ノードがn台死んでも大丈夫なようにリソースに余裕を持たせる
- マスター/etcdはノード100台までなら16GB、それ以上なら32GB
- 普段はメモリそんなに消費していないので油断しがち、だけどフェイルオーバ時にメモリいっぱい使って連鎖障害することがあるから余裕大事



## etcd

- HA構成時メンバー数は奇数(3, 5, 7)である必要がある
- 2メンバーだと片方落ちたら機能しない
- AZ障害耐性を持たせるためのクロスデータセンターセットアップの場合は少なくとも3つのAZが必要
- メンバー間ネットワークレイテンシ2ms以下が推奨



## セキュリティ

- コンテナイメージの脆弱性対応とリビルドプロセス
  - コンテナスキャンソフトウェアなど導入
- アクセス制限
  - オープンなk8sクラスタでBitCoin掘るの流行ってる
- コンテナホストのアップデート
  - セキュリティ修正などのパッケージアップデートを適用するつもりで実行してDockerアップデートされて死亡
  - 最近はk8sとDockerバージョンの非互換問題は少ない




## リソース

- CPU
- メモリ
- ディスク
- ネットワーク



## CPU

- 無防備なk8sへCPUを全て消費するアプリをデプロイ
  - ノードがNotReadyとなる
  - Eviction
  - アプリが別ノードへ再スケジュールされる
  - 繰り返し
  - 元ノードは復活



## ノード保護

- アプリに全CPUが持って行かれてノード障害にならないように事前予約

```
--kube-reserved="cpu=100m,memory=100Mi,ephemeral-storage=1Gi"
--system-reserved="cpu=100m,memory=100Mi,ephemeral-storage=1Gi"
```

https://kubernetes.io/docs/tasks/administer-cluster/reserve-compute-resources/



## メモリ

- メモリ不足の場合は基本的にアプリケーションのコンテナからoom-killerに殺される
- OpenShift 3.6から、Kubernetes 1.8からスワップ無効必須
- スワップがあると意図しないパフォーマンスダウン



## ディスク

- ディスクを多く消費するPodを事前に知るのは難しいので、ディスクの領域は多め(200GB+)に
  - イメージがでかい
  - ログが大量
  - Volume以外への書き込み、キャッシュをテンポラリファイルとして保持などなど



## PodのCPU, メモリ, ディスク制限

- Quotaで割り当てできるリソース総量を制限
  - Auto ScaleやRolling Updateできる余地を残す必要があることに注意
- LimitRangeを定義してCPUメモリ制限
- Podに個々にresource requests/limits定義
- PV以外のディスクの制限はいまのところできないので監視でがんばる

https://kubernetes.io/docs/tasks/administer-cluster/memory-default-namespace/



## LimitRange

- CPUのrequestsのminとmaxは設定、limitsはなくてもいい
  - limitsがないので空いているCPUは全部使っていい
- Memoryはlimitsを設定
  - メモリがBurstableであってもなくてもいい、みたいなアプリはあまりないのでrequestsとlimitsは基本同値



## CPU CFS Quota

- LimitRangeなどに設定するCPUの単位はコア秒
  - 8コアのマシンなら1秒あたり8コア秒 = 8000mが最大
  - アプリに1コア秒を設定しても1コアを1秒利用するわけではない
    - CPU pinning
  - 1コア秒の設定で8コアを0.12秒使ってCPU利用率が800%



## Autoscale

- デフォルト30秒ごとにReady状態のPodのCPU利用を取得
- 実際のターゲットは指定されたターゲットから上下10%幅
  - 70%なら77%でscale up, 63%でscale down
- scale up後は3分、scale down後は5分再スケールしない

https://github.com/kubernetes/community/blob/master/contributors/design-proposals/autoscaling/horizontal-pod-autoscaler.md#autoscaling-algorithm



## Podのネットワーク制限

- ネットワーク帯域制限が必要ならアノテーション
  - tcコマンドが発行される

```
kubernetes.io/ingress-bandwidth=10M
kubernetes.io/egress-bandwidth=10M
```



## メモリとディスク

- ノードのメモリやディスク不足時にPodをEvictionする

```
--eviction-hard="memory.available<4%,nodefs.available<4%,nodefs.inodesFree<4%,imagefs.available<4%,imagefs.inodesFree<4%"
--eviction-soft="memory.available<8%,nodefs.available<8%,nodefs.inodesFree<8%,imagefs.available<8%,imagefs.inodesFree<8%"
--eviction-soft-grace-period="memory.available=1m30s,nodefs.available=1m30s,nodefs.inodesFree=1m30s,imagefs.available=1m30s,imagefs.inodesFree=1m30s"
```

https://kubernetes.io/docs/tasks/administer-cluster/out-of-resource/



## アプリケーション

- ノードメンテ、ノード障害時のサービス断を防ぐにはPodはreplicaを2以上に
  - Scale対応しているPod、かつReadWriteOnce以外のPVを利用する必要がある。つまり一般的なDBは×
- メンテでのpod移動のためにPod Disruption Budgetを設定
- livenessProbe, readinessProbe, terminationGracePeriodSecondsなどはきちんと指定しておく

https://kubernetes.io/docs/concepts/workloads/pods/disruptions/



## アプリケーション

- スケールダウンは実はスケジューラの条件を見ない
  - 3ノードに6レプリカ配置、うまく分散
  - 2レプリカに減少したらなぜか1ノード上に2レプリカ、気づかずノード障害やメンテで止めたらアクセス不可



## DBをk8s上で動かす

- NFSなどのネットワークファイルシステムのPVを使ってしまいデータ破損
  - fsync使うようなDB, ElasticSearch, Cassandraなどのアプリケーションでネットワークファイルシステムを使うと危険
- ReadWriteOnceなブロックデバイスPVを使おう



## Scale非対応アプリとノード障害

- フェイルオーバに6分ほどかかるので注意
  - ノードから40秒status updateがないとNotReady
    - `--node-monitor-grace-period=40s`
  -ノードが5分間NotReadyなら障害とみなしPodをEvict
    - `--pod-eviction-timeout=5m0s`

https://kubernetes.io/docs/admin/kube-controller-manager/



## ReadWriteOnceなPV

- 単一ノードからしかマウントできない
- Pod移動したらVolumeもdetach/attachされる
- Scaleしても全部同じノードにスケジュールされる
- Scale不要なアプリ、Scale非対応アプリに利用



## PV

- リサイズは現状できないので多めに
  - Snapshot and resize機能は1.8でAlpha



## Javaアプリ

- CPUコア数からスレッド数オートチューニング
- Heapはcgroupsのメモリ割り当ての50%程度が妥当
  - Java 8u131以降なら`-XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap`
- 起動スクリプトなどで`/sys/fs/cgroups`を参照して計算して設定する
- 無設定だとGCが一瞬で割り当てCPU量を使い果たしてしまいアプリにまったくCPUが割当たらなくて数秒止まる



## Javaアプリ

- GCだけではなく各種ライブラリでも利用されているので注意

```
Runtime.getRuntime().getMaxMemory()
Runtime.getRuntime().availableProcessors()
```


# おしまい