# OpenShift 4のCluster Version Operator



# Takayoshi Kimura
## OpenShiftの中の人
### twitter: [@nekop](https://twitter.com/nekop)



## OpenShiftのCluster Version Operator

- OpenShift 4のクラスタ自体を構成、運用するオペレータ
- CVOと略される
- インストールもbootstrapというシングルマスターを起動して、CVOを配置するだけ
- CVOが全てあるべき姿へと自動修復する形で構成
- etcd、マスター、各機能、コンピュートノードなどなどのセットアップ
- バージョンアップも同様 `oc adm upgrade`



## CVOが利用するCRD

- ClusterVersion
- ClusterOperator

```
$ oc get clusterversion
NAME      VERSION   AVAILABLE   PROGRESSING   SINCE   STATUS
version   4.1.6     True        False         3h11m   Cluster version is 4.1.6
```



## ClusterOperator一覧

```
$ oc get clusteroperator --no-headers | wc -l
25
$ oc get clusteroperator
NAME                                 VERSION   AVAILABLE   PROGRESSING   DEGRADED   SINCE
authentication                       4.1.6     True        False         False      50d
cloud-credential                     4.1.6     True        False         False      50d
cluster-autoscaler                   4.1.6     True        False         False      50d
console                              4.1.6     True        False         False      50d
dns                                  4.1.6     True        False         False      50d
image-registry                       4.1.6     True        False         False      3h24m
ingress                              4.1.6     True        False         False      37d
kube-apiserver                       4.1.6     True        False         False      50d
kube-controller-manager              4.1.6     True        False         False      50d
kube-scheduler                       4.1.6     True        False         False      50d
machine-api                          4.1.6     True        False         False      50d
machine-config                       4.1.6     True        False         False      43d
marketplace                          4.1.6     True        False         False      3h24m
monitoring                           4.1.6     True        False         False      3h21m
network                              4.1.6     True        False         False      50d
node-tuning                          4.1.6     True        False         False      3h58m
openshift-apiserver                  4.1.6     True        False         False      3h24m
openshift-controller-manager         4.1.6     True        False         False      50d
openshift-samples                    4.1.6     True        False         False      3h58m
operator-lifecycle-manager           4.1.6     True        False         False      50d
operator-lifecycle-manager-catalog   4.1.6     True        False         False      50d
service-ca                           4.1.6     True        False         False      50d
service-catalog-apiserver            4.1.6     True        False         False      50d
service-catalog-controller-manager   4.1.6     True        False         False      50d
storage                              4.1.6     True        False         False      3h58m
```



## 各ClusterOperatorの機能

- ぐぐって
- "openshift cluster operator オペレータ名"
- https://github.com/openshift/cloud-credential-operator



## CVOの情報

```
$ oc adm release info
Name:      4.1.6
Digest:    sha256:aa955a9ec40e55e5d9c0203a995b398e8c1031473dae24ed405efe9a95b43186
Created:   2019-07-12T18:36:40Z
OS/Arch:   linux/amd64
Manifests: 288

Pull From: quay.io/openshift-release-dev/ocp-release@sha256:aa955a9ec40e55e5d9c0203a995b398e8c1031473dae24ed405efe9a95b43186

Release Metadata:
  Version:  4.1.6
  Upgrades: 4.1.0, 4.1.2, 4.1.3, 4.1.4
  Metadata:
    description: 
  Metadata:
    url: https://access.redhat.com/errata/RHBA-2019:1766

Component Versions:
  Kubernetes 1.13.4
```



## CVO本体のmanifests

```
$ oc project openshift-cluster-version
$ oc rsh deploy/cluster-version-operator ls -1 /manifests/
0000_00_cluster-version-operator_00_namespace.yaml
0000_00_cluster-version-operator_01_clusteroperator.crd.yaml
0000_00_cluster-version-operator_01_clusterversion.crd.yaml
0000_00_cluster-version-operator_02_roles.yaml
0000_00_cluster-version-operator_03_deployment.yaml
0001_00_cluster-version-operator_03_service.yaml
```



## CVOが作成するクラスタオペレータ群のmanifests

```
$ oc rsh deploy/cluster-version-operator ls -1 /release-manifests/ | wc -l
290
$ oc rsh deploy/cluster-version-operator ls -1 /release-manifests/ | head
0000_03_authorization-openshift_01_rolebindingrestriction.crd.yaml
0000_03_quota-openshift_01_clusterresourcequota.crd.yaml
0000_03_security-openshift_01_scc.crd.yaml
0000_05_config-operator_02_apiserver.cr.yaml
0000_05_config-operator_02_authentication.cr.yaml
0000_05_config-operator_02_build.cr.yaml
0000_05_config-operator_02_console.cr.yaml
0000_05_config-operator_02_dns.cr.yaml
0000_05_config-operator_02_featuregate.cr.yaml
0000_05_config-operator_02_image.cr.yaml
```



## まとめ

- OpenShift本体はCluster Version Operatorにより構成される
- インストールはCVOをセットアップするだけ
- CVOの実体はocp-releaseイメージ、各バージョンに対応するイメージがある
