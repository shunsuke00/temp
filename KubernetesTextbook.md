# 「Kubernetesの教科書」のメモ

docker-composeからK8sへの移行を習得するために入門書を読んだが、K8sの知識がまだまだ足りないと感じたので今度はK8sの入門書を読もうという魂胆。

## 目次

- [「Kubernetesの教科書」のメモ](#kubernetesの教科書のメモ)
  - [目次](#目次)
  - [1: Kubernetes入門](#1-kubernetes入門)
    - [1-1: Kubernetesを構成する重要な用語](#1-1-kubernetesを構成する重要な用語)
    - [1-2: Kubernetes: クラウドのオペレーティングシステム](#1-2-kubernetes-クラウドのオペレーティングシステム)
  - [2: Kubernetesの動作原理](#2-kubernetesの動作原理)
    - [2-1: Kubernetesの全体像](#2-1-kubernetesの全体像)
    - [2-2:　コントロールプレーンとワーカーノード](#2-2コントロールプレーンとワーカーノード)
    - [2-3:　Pod](#2-3pod)
    - [2-4: デプロイ](#2-4-デプロイ)
    - [2-5: サービスオブジェクトと安定したネットワーク](#2-5-サービスオブジェクトと安定したネットワーク)
  - [3: Kubernetesの入手](#3-kubernetesの入手)
    - [3-1: Docker Desktopで全てをインストール](#3-1-docker-desktopで全てをインストール)
    - [3-2: Linode CloudでLinode Kubernetes Engine(LKE)クラスターを構築する](#3-2-linode-cloudでlinode-kubernetes-enginelkeクラスターを構築する)
    - [3-6: kubctlとkubeconfigファイルの詳細](#3-6-kubctlとkubeconfigファイルの詳細)
  - [4: Podの操作](#4-podの操作)
    - [4-1: Podの理論](#4-1-podの理論)
    - [4-2: マルチコンテナPod](#4-2-マルチコンテナpod)
    - [4-3: Podの実践](#4-3-podの実践)
  - [5: Namespaceを持つ仮想クラスター](#5-namespaceを持つ仮想クラスター)
    - [5-1: Namespaceの概要](#5-1-namespaceの概要)
    - [5-2: Namespaceの作成と管理](#5-2-namespaceの作成と管理)
    - [5-3: Namespaceへのデプロイ](#5-3-namespaceへのデプロイ)
  - [6: Kubernetesのデプロイ](#6-kubernetesのデプロイ)
    - [6-1: デプロイ理論](#6-1-デプロイ理論)
    - [6-2: Deploymentの作成](#6-2-deploymentの作成)
    - [6-3: ローリングアップデートを実行する](#6-3-ローリングアップデートを実行する)
    - [6-4: ロールバックを実行する](#6-4-ロールバックを実行する)
  - [7: Kubernetesのサービス](#7-kubernetesのサービス)
    - [7-1: サービス理論](#7-1-サービス理論)
    - [7-2: Serviceの実践編](#7-2-serviceの実践編)
  - [8: Ingress](#8-ingress)
    - [8-1: Ingressの環境設定](#8-1-ingressの環境設定)
    - [8-2: Ingressアーキテクチャ](#8-2-ingressアーキテクチャ)
    - [8-3: Ingressの実践](#8-3-ingressの実践)
  - [9: Kubernetes上のWasm](#9-kubernetes上のwasm)
    - [9-1: Wasmプライマー](#9-1-wasmプライマー)
    - [9-2: Kubernetes上のWasmを理解する](#9-2-kubernetes上のwasmを理解する)
    - [9-3: Kubernetes上のWasmを実際に使ってみる](#9-3-kubernetes上のwasmを実際に使ってみる)
  - [10: サービス検出の詳細](#10-サービス検出の詳細)
    - [10.1: シーンを設定する](#101-シーンを設定する)
    - [10.2: サービスレジストリ](#102-サービスレジストリ)
    - [10.3: サービス検出](#103-サービス検出)
    - [10.4: サービス検出とNamespace](#104-サービス検出とnamespace)
    - [10.5: サービス検出のトラブルシューティング](#105-サービス検出のトラブルシューティング)
  - [11: Kubernetesストレージ](#11-kubernetesストレージ)
    - [11-1: 全体像](#11-1-全体像)
    - [11-2: ストレージプロバイダー](#11-2-ストレージプロバイダー)
    - [11-3: コンテナストレージインターフェース(CSI)](#11-3-コンテナストレージインターフェースcsi)
    - [11-4: Kubernetes永続ボリュームサブシステム](#11-4-kubernetes永続ボリュームサブシステム)
    - [11-5: ストレージクラスによる動的プロビジョニング](#11-5-ストレージクラスによる動的プロビジョニング)
    - [11-6: 実践編](#11-6-実践編)
  - [12: ConfigMapとSecret](#12-configmapとsecret)
    - [12-1: 全体像](#12-1-全体像)
    - [12-2: ConfigMap 理論](#12-2-configmap-理論)
    - [12-3: ConfigMapの実践](#12-3-configmapの実践)
    - [12-4: Secretを実際に体験](#12-4-secretを実際に体験)
  - [13: StatefulSet](#13-statefulset)
    - [13-1: StatefulSet理論](#13-1-statefulset理論)
    - [13-2: StatefulSetの実践](#13-2-statefulsetの実践)
  - [14: APIセキュリティとRBAC](#14-apiセキュリティとrbac)
  - [15: Kubernetes API](#15-kubernetes-api)
  - [16: Kubernetesの脅威モデル化](#16-kubernetesの脅威モデル化)
  - [17: 実際のKubernetesセキュリティ](#17-実際のkubernetesセキュリティ)
  - [メモ](#メモ)
    - [豆知識](#豆知識)
    - [単語](#単語)

---

## 1: Kubernetes入門

### 1-1: Kubernetesを構成する重要な用語

**--オーケストレーション--**
オーケストレーションとは

- アプリケーションのデプロイ
- スケールアップ・スケールダウン
- 自動修復
- ロールアウト・ロールバック

などの「変更に対する動的な対応」を行うことで、K8sはこれを自動で行う。

ロールアウトとは、大まかに「システムの運用開始（新規リリースや新バージョンを含む）」を指す。

**--クラウドネイティブ--**
クラウドネイティブアプリケーションは

- 自動スケーリング
- 自己修復
- 自動更新
- ロールバック

などの機能を備えたアプリケーション。
（通常のアプリケーションをパブリッククラウドで実行することはクラウドネイティブとは違う）

**--マイクロサービス--**
マイクロサービスアプリケーションとは、多数の小さな専門的で独立したパーツから構成され、それらが連携して形成される汎用背の高いアプリケーションのこと。

**--Kubernetesとは--**
Kubernetesとは、「コンテナ化されたクラウドネイティブなマイクロサービスアプリケーションのオーケストレーター」。
噛み砕くと、**「個々のアプリケーション機能がコンテナとしてパッケージ化され、デプロイされたアプリケーションをデプロイ・スケール・自己修復・更新するソフトウェア」**。

**--K8sとDocker--**
Kubernetesクラスターはコンテナランタイム（コンテナの作成、実行、停止などを管理するための基盤技術）として「containerd」をデフォルト（他も選択可能）で採用している。

![実行基盤](../img/book-summary/KubernetesTextbook/1/platform.png)

containerdはDockerをK8s上で稼働させるための最小限の機能を有するDockerのカスタマイズバージョン（そのため、K8s上でDockerコンテナが動かせる）。
Dockerを使わない理由は、Dockerでは機能が多すぎてK8sの稼働に余分な付加がかかってしまうから。

### 1-2: Kubernetes: クラウドのオペレーティングシステム

**K8sはクラウドリソースを抽象化し、アプリケーションマイクロサービスをスケジュールする。**

具体例を挙げれば、

AWSやAzureなどのどのデータセンターでアプリが実行されているか気にする必要なく、マルチクラウド構成やクラウド移行を実現できる。
さらにデータセンターのリソースさえ、利用者は意識せずにアプリケーションを実行できる。

---

## 2: Kubernetesの動作原理

### 2-1: Kubernetesの全体像

**--Kubernetesクラスター--**
Kubernetesクラスターは、アプリケーションで使用するリソースを提供する1つ以上のノードで構成され、ノードの種類は「コントロールプレーンノード」と「ワーカーノード」の2つがある。

- コントロールプレーンノード（マスターノード）
  - Linux
  - クラスターに最低1つ必要
  - システムサービスを提供
    - （Kubernetesの）APIサーバー
    - クラスターストア
    - コントローラー
    - スケジューラー
- ワーカーノード
  - Linux or Windows
  - （ビジネス）アプリケーションの実行場所

ノードに使用する物理的なものは、物理サーバー・仮想マシン・クラウドインスタンスなどである。

![cluster](../img/book-summary/KubernetesTextbook/2/cluster.png)

**--Kubernetesオーケストレーター--**
KubernetesオーケストレーターはK8sにおける、アプリケーションをデプロイ・管理するシステム。
具体的には、アプリケーションの機能的なデプロイ、障害発生時の修復、負荷の増減に応じたスケーリングなどを行う。

### 2-2:　コントロールプレーンとワーカーノード

**--コントロールプレーンの概要--**
クラスターにはコントロールプレーンが1つ以上である。
ベストプラクティスでは

- 3つ以上の奇数個のコントロールプレーンノードをクラスターに実行
- 各コントロールプレーンノードを異なるアベイラビリティゾーンに分散
- （ビジネス）アプリケーションの実行はワーカーノードのみで行い、コントロールプレーンノードはクラスター関連の操作のみに集中させる

**--コントロールプレーンのシステムサービス--**
コントロールプレーンのサービスとして、APIサーバー・クラスターストア・コントローラー・スケジューラーを説明する。

- APIサーバー
  - Kubernetesに対してコマンド・リクエストを実行するために経由するサーバー（内部のシステムサービスもこのAPIサーバーを経由する）
  - HTTPS経由のRESTful API
  - デプロイ・アップデートのプロセス
    - YAMLファイル記述
    - APIサーバーにYAMLファイルを送信
    - リクエストの承認
    - 定義がクラスターストアに保存
    - コンテナがノードにスケジュール
- クラスターストア
  - 全てのアプリケーションとクラスターコンポーネントの最適な状態を保持する
  - etcd分散データベースに基づく
- コントローラー
  - コントロールプレーン上でクラスターの機能を実装する
  - 「Deploymentコントローラー」「StatefulSetコントローラ」「ReplicaSetコントローラー」など様々存在
  - 例）レプリカ数の監視と調整など
  - 個々のコントローラーの生成・管理を担当するコントローラマネージャーが存在
- スケジューラー
  - APIサーバー上で新しいタスクを監視、ノードの特定、ノードにタスク割り当てを行う

![controlPlane](../img/book-summary/KubernetesTextbook/2/controlPlane.png)

**--ワーカーノード--**
ワーカーノードの主要コンポーネントは

- Kubelet
  - クラスターとの全ての通信を処理しプロセスは
    - APIサーバーで新しいタスクを監視
    - ランタイムにタスクの実行を指示
    - タスクステータスをAPIサーバーに報告
- ランタイム
  - コンテナイメージのPull
  - コンテナの起動・停止
  - デフォルトでcontainerdランタイムが備わっている
- Kube-proxy
  - ノード上で実行されるタスクへのトラフィックの負荷分散を行う

![workerNode](../img/book-summary/KubernetesTextbook/2/workerNode.png)

**--アプリの実行（パッケージ化）方法--**
Kubernetes上でアプリ（コンテナ・仮想マシン・Wasmアプリ）を実行するにはPodというリソースにラップする必要がある。
Podは、様々な種類のタスクを抽象化してKubernetes上で実行できるようにするラッパー（ある程度どんなタスクやアプリでもPodという形にすることでKubernetesで実行できるようになる）。

Podに対して「スケーリング」「自己修復」などのより高度な機能を実現するためには、（Deploymentなど）高位のコントローラーにラップすれば良い。
このラップの構造はマニフェストファイルで定義される。

各レイヤー（リソース）の機能は

- Deployment
  - Podをラップ
  - スケーリング・自己修復機能
- Pod
  - コンテナをラップ
  - Kubernetes上でアプリを実行
- コンテナ
  - アプリをラップ
  - 依存関係の提供

![wrap](../img/book-summary/KubernetesTextbook/2/wrap.png)

### 2-3:　Pod

**--Podの仕組みとコンテナ--**
Podは「1つ以上のコンテナのための共有実行環境」。
ほとんどはシングルコンテナが採用されるが、マルチコンテナPodの用途は

- サービスメッシュ
  - トラフィック（サービス間通信）に対する機能（暗号化など）をメインコンテナから隔離し、他のコンテナにその機能を持たせる。
  - メインコンテナがその本質となる機能に集中できるようになる
- 環境初期化のヘルパーサービス
- 密結合されたヘルパー機能のアプリ

一方、実行環境として共有されるものは

- ネットワーク
- スタック
- ボリューム
- 共有メモリ

など。特にネットワークにおいてはIPアドレスが共有され、ポート番号で区別する。コンテナ同士はlocalhostで通信できる。

**--スケジューリングとスケーリング--**
KubernetesにおいてPodはスケジュールをする最小単位なので、マルチコンテナPodの場合は同じPod内のコンテナは単一のノードにスケジュールされる。

また、スケジュールの最小単位ということはスケーリングの最小単位でもあり、Podに1つのアプリケーションを搭載し、スケーリングの設定をすることで、複数台のPod（アプリケーション）を実行することができる。

**--Podのライフサイクル--**
Podは一度実行されたら、その機能や情報は変更されない。
変更の必要がある場合は、古いPodを削除して新たなPodを生成する必要がある。
このPodの削除->生成の過程で、常に新しいIDとIPが振られる。

### 2-4: デプロイ

Podをデプロイするほとんどの場合では

- Deployment
- StatefulSet
- DaemonSet

などの上位の**コントローラー**を介してデプロイする。
特にDeploymentは「スケーリング」「自己修復」「ローリングアップデート」「バージョン管理によるロールバック」の機能をPodに追加する。

### 2-5: サービスオブジェクトと安定したネットワーク

ServiceはPodのグループに対して信頼性の高いネットワークを提供する。
具体的には、フロントエンドとしては、固定のDNS名・IPアドレス・ポートを所有する。バックエンドとしては、正常かつアクティブなPodのみに対してトラフィックの付加分散を行う。

Serviceの存在意義は、例えばPodの削除が行われた場合に新しいPodは新しいIPアドレスを持つため、アクセスするべきIPアドレスが変わってしまう上に、削除されたPodへアクセスしてしまえばエラーになってしまうことが挙げられる。
Serviceが存在すれば、固定のIPアドレスで正常稼働しているPodに常にアクセスできるようになる。

---

## 3: Kubernetesの入手

### 3-1: Docker Desktopで全てをインストール

**--Docker DesktopでマルチノードKubernetesクラスターを実行--**
Docker DesktopでマルチノードのKubernetesクラスターを実行できる。その手順は

1. Docker Desktopでサインイン
2. `General`設定において`Use containerd for pulling and storing images`を有効化
3. サイドバーから`Kubernetes`を選択
4. `Create Cluster`をクリック
5. Cluster Type: `kind`を選択し、`Advanced Settings`で`Show system containers (advanced)`を有効化
6. `Create`

このようにすると、コントロールプレーンノードとワーカーノードがコンテナとして実行される。

### 3-2: Linode CloudでLinode Kubernetes Engine(LKE)クラスターを構築する

まだ使用しないのでスキップ。

### 3-6: kubctlとkubeconfigファイルの詳細

kubectlコマンド実行時に裏で動いているプロセスは

- コマンドをHTTP RESTリクエストに変換
- kubeconfigファイルのcurrent-contextで定義されているクラスターにリクエストを送信
- kubeconfigファイルのcurrent-contextで指定された認証情報を使用

kubeconfingファイルとは、`.kube/config`ファイルであり

- クラスター
- ユーザー（認証情報）
- コンテキスト
- カレントコンテキスト

が定義されている。
このconfigファイルを確認するコマンドは

```sh
kubectl config view
```

でDocker Desktopで作成したクラスター情報は

```sh
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://127.0.0.1:6443
  name: docker-desktop
contexts:
- context:
    cluster: docker-desktop
    user: docker-desktop
  name: docker-desktop
current-context: docker-desktop
kind: Config
users:
- name: docker-desktop
  user:
    client-certificate-data: DATA+OMITTED
    client-key-data: DATA+OMITTED
```

カレントコンテキストを確認するコマンドは

```sh
kubectl config current-context
```

---

## 4: Podの操作

### 4-1: Podの理論

Kubernetesがアプリケーションコンテナをラップして、Podというリソースを定義している意義は

- アプリケーションの抽象化
- 複数コンテナのリソース共有
- 高度なスケジューリング機能
- Kubernetes専用のライフサイクルの定義

などが挙げられる。以下ではPodの説明を交えつつ上の利点を見ていく。

Pod属性を出力するコマンド

```sh
# 完全版を表示
kubectl explain pods --recursive | more

# 各属性のサポート値の確認
kubectl explain pod.${property}
```

**--アプリケーションの抽象化--**
Podでは「コンテナ」「仮想マシン(VM)」「サーバーレス関数」「Wasmアプリ」を実行できる。これらの様々な種類のアプリケーション？をPodとして抽象化することで、KubernetesはPodの中身を気にすることなく、Podとしてデプロイと管理に集中できる。
つまり、**中身がなんであろうとPodという形に落とし込むことができれば、Kubernetesで全て同様に実行可能**ということ。

![抽象化](../img/book-summary/KubernetesTextbook/4/abstraction.png)

※「コンテナ」「Wasmアプリ」はKubernetesの標準セットで動作するが、「サーバーレス関数と」「仮想マシン(VM)」は特別処置が必要。

- サーバーレス関数
  - Kubernetes APIを拡張するためにKnativeなどが必要？
- 仮想マシン(VM)
  - Kubernetes APIを拡張するためにKubeVirtなどが必要？
  - PodではなくVirtualMachineInstance(VMI)で実行される

**--複数コンテナのリソース共有--**
Podには1つ以上のコンテナを実行することができ、これらのコンテナは同一Pod内で以下を共有する。

- 共有ファイルシステム
- ボリューム
- 共有ネットワークスタック
- 共有メモリ
- 共有プロセスツリー
- 共有ホスト名（IPアドレス）

メインコンテナは`8080`番ポートで、サイドカーコンテナは`5005`番ポートで利用できる。
同一Pod内のコンテナ同士は`localhost`で通信できる。

複数コンテナの利用例は[4-2節](#4-2-マルチコンテナpod)を参照。

**--高度なスケジューリング機能--**
まずPodのスケジューリングの基礎として、Podはノード上で実行される。つまり、Podはノード上にスケジューリングされる。つまり、**Pod内の全てのコンテナが同じノード上にスケジュールされる。**
ここにおけるノードとは「物理サーバー」「仮想マシン」「クラウドインスタンス」のいづれかである。

![node](../img/book-summary/KubernetesTextbook/4/node.png)

一方、Podを特定のノード上にスケジュールさせるためにはオプションを指定する必要がある。

- nodeSelector
  Podにラベルのリストを指定することで、その全てのラベルを有するノードにのみPodをスケジュールすることができる。
- 親和性と反親和性
  - Podにラベルのリストとリソース（NodeあるいはPod）を指定することで、スケジュール先を限定することができる。
    - 親和性のルールを付加すれば、指定した全てのラベルを持つ指定したリソースと同じ場所にスケジュールする
    - 反親和性のルールを付加すれば、指定したラベルを持たないリソースと同じ場所にスケジュールする。
  - 「ハードルール」あるいは「ソフトルール」を設定することで、このルールの厳密さを決めることができる。
    - ハードルールを設定すれば、ラベルによるスケジュール先の限定を厳密に守り、スケジュールできるノードがなければPodはスケジュールされない。
    - ソフトルールを設定すれば、条件に合うノードがあればそこにスケジュールされ、条件に合うノードがない場合でもどれかのノードにはPodがスケジュールされる。
- 接続分散制約
  「高可用性」「低レイテンシー」などのために複数のアベイラビリティゾーンに跨ってPodをスケジュールできる。
- リソース要求とリソース制限
  Podに必要なCPUとメモリを設定し、必要なリソースが確保できるノードにPodをスケジュールすることができる。
  **全てのPodでこの機能を使用すべき。**

![scheduling](../img/book-summary/KubernetesTextbook/4/scheduling.png)

**--デプロイとライフサイクル--**
Podをデプロイする方法は以下の2つ

- Podマニフェスト経由で直接デプロイ
- ワークロードリソース・コントローラー経由で間接デプロイ（推奨）

後者が推奨される理由は、**間接デプロイではPodがコントローラーにラップされることで様々な機能が使用できるようになる**から。
一方、前者ではPodがスケジュールされたノードのkubeletによってのみ管理され、以下のような欠点があるからである。

- 「自己修復」「スケーリング」「ローリングアップデート」などの機能が使用できない
- ノードに障害が発生するとkubeletも死ぬのでPodの管理が不可能（対してコントローラーはノードを跨ぐ上位の存在なのでノードの障害に対応可能）

次に、Podのデプロイのプロセスは

1. YAMLマニフェストファイルでPodを定義
2. マニフェストをAPIサーバーに投稿
3. リクエストの認証
4. Podの仕様の検証
5. スケジューラーがノードをフィルタリング
6. Podがノードにスケジュールされる
7. ノード上のkubeletがAPIサーバーを監視してPod割り当てを認識
8. kubeletがPodの仕様をダウンロードし、ローカルランタイムに起動要求
9. kubeletがPodのステータスを監視し、APIサーバーに報告

![deploy](../img/book-summary/KubernetesTextbook/4/deploy.png)

最後にKubernetesが定義するPodのライフサイクルを見ていく。
Podの原則は以下の2つである。

- mortal（死にゆく）
  Podを再起動することはなく、必ず「削除」と「新規作成」でPodを運用する。
- immutable（不変）
  デプロイされたPodは変更されることはなく、新バージョンなどにしたい場合も「削除」と「新規作成」で運用する。

これによってPodのライフサイクルは簡潔になる。

1. `Pending`: スケジュールされるまで
2. `Running`: Pod内アプリケーションが実行されている状態（サーバーなら無期限、単発実行なら処理完了まで）
3. `Succeed`/`Fail`: 削除されるまで？

**--再起動ポリシー--**
Podには再起動ポリシーを付与することができる。
しかしこれは**Podに対する再起動ではなく、Pod内の（Initコンテナを除く）全てのコンテナに対する再起動**である。

再起動ポリシーを設定するには`spex.restartPolicy`を以下のいずれかに設定する必要がある。

- `Always`
  常に再起動を試行。webサーバー・データストア・メッセージキューなどの無期限実行用のアプリケーションに設定。
- `Never`
  再起動を試行しない。単発実行かつ失敗しても良い処理を行うPodに設定。
- `OnFailure`
  コンテナがエラーコードで失敗した場合のみ再起動。

### 4-2: マルチコンテナPod

コンテナを複数持つPodの使用パターンは主に2つある。どちらの場合も、それぞれのコンテナが明確に定義された単一の責任を持つように設計することができる（単一責任の原則）。

**--Initコンテナ--**
これはKubernetesによって定義された特別なコンテナであり、**メインのアプリケーションコンテナ起動前に1度だけInitコンテナを起動・完了する**（逆に言えばInitコンテナが完了するまでメインのコンテナは起動しない）。
Initコンテナの存在意義は、環境の初期化・準備である。
`spec.initContainers`で定義する。

特記事項は

- Initコンテナは複数設定可能
- Podマニフェストに記述された順序でInitコンテナを実行
- メインコンテナは全てのInitコンテナが完了した後に起動する
- Initコンテナが失敗した場合、KubernetesがPodの再起動を行う

![init](../img/book-summary/KubernetesTextbook/4/init.png)

具体的な使用例は以下

- リモートAPIが接続受け入れ可能状態になった時のみ起動するアプリケーション
  リモートAPIの応答をチェックするロジックをInitコンテナに分離することで、APIコンテナがシンプルに保たれる。
- 起動前にリモートリポジトリのクローンが必要なアプリケーション
  リポジトリクローンのための証明書や認証情報を分離することでメインコンテナをシンプルがシンプルに保たれる。

**--サイドカー--**
Initコンテナがメインコンテナの実行前にそのタスクを完了するのに対し、**サイドカーコンテナはメインコンテナと並行してそのタスクを実行**する。
並行して実行するタスクは「ログ収集」「接続の仲介」「トラフィックの暗号化」「監視・同期」など。

マニフェストでの定義方法は、Initコンテナにオプションを追加する形で行う？？
具体的には、`restartPolicy: Always`のInitコンテナを定義する。このようにすると

- メインアプリケーションコンテナより前に起動
- メインアプリケーションコンテナと並行して実行
- メインアプリケーションコンテナより後に終了

される。

具体的な使用例は例えば、ネットワークトラフィックを傍受して暗号化するサービスメッシュサイドカー。
つまり、メインアプリケーションコンテナが行うネットワーク通信は全てサイドカー経由で行われる。

![sidecar](../img/book-summary/KubernetesTextbook/4/sidecar.png)

### 4-3: Podの実践

**--kubectlコマンド--**
podに関するkubectlコマンドは

```sh
# デプロイ
kubectl apply -f ${file_path}

# ----------------------------------------------
# 取得、ステータス確認
kubectl get pod
# wideオプションで追加情報も表示
kubectl get pod -o wide
# yamlオプションでpodに関する全情報を表示。specには理想の状態が表示され、statusには観測された状態が表示される。
kubectl get pod -o yaml
# podに関する情報から好きな情報をカスタムして表示
# ex)NAME:.metadata.name,INIT:.spec.initContainers[*].name,
kubectl get pod -o custom-columns=${output_column_name1}:.${property1},...
# Podの情報変更を監視し、変更に伴って出力行を追加
kubectl get pod --watch

# ----------------------------------------------
# ライフサイクルイベントとPod情報を見やすい形で表示
kubectl describe pod ${pod_name}

# ----------------------------------------------
# ログ取得
kubectl logs ${pod_name}
# コンテナを指定してログ取得
kubectl logs ${pod_name} --container ${container_name}

# ----------------------------------------------
# ローカルシェルからコンテナにコマンドを送信
kubectl exec ${pod_name} -- ${command}
# コマンドを実行するコンテナを指定してコマンド送信（デフォルトは最初のコンテナ）
kubectl exec ${pod_name} --container ${container_name} -- ${command}

# コンテナのターミナルにローカルを接続し、コンテナとの対話型のセッションを確立
kubectl exec -it ${pod_name} -- sh
# コンテナ内で環境変数を確認
env
# セッションを切断
exit

# ----------------------------------------------
# 稼働中のPodを編集
kubectl edit pod ${pod_name}

# ----------------------------------------------
# 削除
kubectl delete pod ${pod_name}
# YAML経由で削除
kubectl delete -f ${file_path}
```

**--ステータス--**
`kubectl get pod`を実行した時に表示される`STATUS`が何を表しているのか説明する。

- `Init:0/1`
  **Initコンテナの1つ目が実行中**。`kubectl describe pod`で取得できるPod全体のステータスとしては`Pending`。
- `PodInitializing`
- `Running`

**--Podホストネーム--**
Pod名を指定する`metadata.name`は実はPod内の全てのコンテナのホスト名にも使用される。
注意すべきは、Pod名は有効なDNS名である必要があること。つまり（`a-z0-9-.`、正規表現では`^[a-z0-9]([a-z0-9-]*[a-z0-9])?(\.[a-z0-9]([a-z0-9-]*[a-z0-9])?)*$`）。

実際はKubernetesのリソース名の制約はこれを十分に満たしていて、リソース名の制限を表す正規表現は`^[a-z0-9]([-a-z0-9]*[a-z0-9])?$`（かつ63字以内）である。

**--リソース要求・制限--**
YAMLにて設定するリソース値は以下のように設定する。

```yaml
spec:
  containers:
    resources:
      requests:
        memory: ...Mi
        cpu: ...
      limits:
        memory: ...Mi
        cpu: ...
```

`requests`は最小値（指定しなければKubernetesが勝手に`limits`値に合わせて設定する）を表し、`limits`は最大値を表す。

---

## 5: Namespaceを持つ仮想クラスター

### 5-1: Namespaceの概要

**--概要--**
Kubernetesにおける**Namespaceは、Kubernetesクラスターを仮想クラスターに分割する手法**であり、軽量かつ簡易な分離を実現する方法である。

Kubernetesクラスター内を複数のNamespaceに分離することで、アプリケーションなどを大きな括りに分離することができる。
ただし、特定のNamespaceの影響は同一クラスターの異なるNamespaceにも及ぶ可能性がある。その意味で分離性が弱いと言われる。
一方で、分離性の強い分離法はKubernetesクラスターごと分離する方法である。

![namespace](../img/book-summary/KubernetesTextbook/5/namespace.png)

Kubernetesの各リソースがNamespaceで管理されているかどうかを知るには、以下コマンドを実行すれば良い。

```sh
kubectl api-resources
```

**--defaultネームスペース--**
Kubernetesには事前に作成されているネームスペースが存在する。それは

- `default`
  Kubernetesリソースを作成する際にネームスペースを指定せずに作成される場所。
- `kube-system`
  コントロールプレーンコンポーネントが実行される場所。
- `kube-public`
  誰でも読み取り可能なオブジェクトが存在する場所。
- `kube-node-lease`
  ノードのハートビートとノードリースの管理に使用。？？

**--コマンド--**
Namespaceに関するコマンドは以下である。`namespace`は`ns`と省略できる。

```sh
# namespace取得
kubectl get namespaces

# namespaceの詳細表示
kubectl describe namespace ${namespace}

# フィルタリング（あるいはネームスペース指定）
kubectl get ${kind} -n ${namespace}
kubectl get ${kind} --Namespace ${namespace}
# 全ネームスペース指定
kubectl get ${kind} -A
```

### 5-2: Namespaceの作成と管理

Namespaceを作成・削除・バックグラウンド指定するコマンドは

```sh
# 作成
kubectl create ns ${namespace}
kubectl apply -f ${file_path}

# 削除
kubectl delete ns ${namespace}

# バックグラウンド指定（-nオプションによるフィルタを決めておく）
kubectl config set-context --current -n ${namespace}
```

ここでマニフェストファイルに書く内容は例えば

```yaml
kind: Namespace
apiVersion: v1
metadata:
  name: ${namespace}
  labels:
  env: ${environment_var}
```

### 5-3: Namespaceへのデプロイ

ここまでで、ネームスペースによるリソースのフィルタリングやネームスペースを指定しなければ`default`ネームスペースにリソースが作成されることを説明した。

では、特定のネームスペースにリソースを作成（デプロイ）するにはどうすれば良いのか。
`create`コマンドでは`-n`オプションを指定すれば良い。具体的には

```sh
kubectl create ${kind} -n ${namespace}
```

ということだ。問題はマニフェストファイルのapplyのやり方である。

マニフェストファイルで定義したリソースを**特定のネームスペースにデプロイするには、マニフェストファイルの中身でネームスペースを指定**する必要がある。
設定するプロパティは`metadata.namespace`である。例えば

```yaml
...
apiVersion: v1
metadata:
  namespace: ${namespace}
  name: ${name}
...
```

のように指定すれば良い。

---

## 6: Kubernetesのデプロイ

### 6-1: デプロイ理論

DeploymentはPodに対して「自己修復」「スケーリング」「ロールアウト」「ロールバック」などの機能を追加する。

1つのアプリケーション（同一構成のPod）に対して1つのDeploymentを使用する。一方で、各Deploymentの監視・管理はDeploymentコントローラーが行う。

![overview](../img/book-summary/KubernetesTextbook/6/overview.png)

以下ではDeploymentとPodの関係や、その機能についてみていく。

**DeploymentとReplicaSetとPod**
DeploymentによってPodに自己修復、スケーリング、ロールアウト、ロールバックなどの機能が追加されるが

- 自己修復
- スケーリング

については、DeploymentではなくReplicaSetが提供する機能である。ただし、ReplicaSetはDeploymentの下位に存在するようなリソースであり、広義で言えばDeploymentがPodに自己修復とスケーリングの機能を追加することには変わりない。

![deploy-rs-pod](../img/book-summary/KubernetesTextbook/6/deploy-rs-pod.png)

この構造と同様に、DeploymentのマニフェストファイルもReplicaSetとPodをラップ（包含）している。
具体的には例えば

```sh
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ${deployment_name}
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      macUnavailable: ${num}
      maxSurge: ${num}
# 以下ReplicaSetの定義
  replicas: ${replica_num}
  selector:
    matchLabels:
      app: ${pod_label}
# 以下Podの定義      
  template:
    metadata:
      labels:
        app: ${pod_label}
    spec:
      ...
```

のようにする。`replicas`,`selector`でReplicaSetのための設定を付け、`template`でPodの定義を付ける。

**--スケーリング--**
ここではDeploymentが提供するスケーリング機能に限らず、Kubernetes全体としてのスケーリングに焦点を当てる。

Kubernetesには、自動的にスケーリングするためのオートスケーラーが複数存在する。

- HPA(Horizontal Pod Autoscaler)
  Podへのアクセス数や負荷に合わせて、自動的にPodの数を増減させる。
- VPA(Vertical Pod Autoscaler)
  Podへのアクセス数や負荷に合わせて、自動的にPodのCPUとメモリを増減させる。デフォルトでOFFだし非推奨。
- CA(Cluster Autoscaler)
  スケジュールされたPodの数に応じて、自動的にクラスターノードの数を増減させる。

※「多次元オートスケーリング」とは、複数のオートスケーリング手法を組み合わせたオートスケーリング手法のこと。

**--ローリングアップデート--**
Deploymentが提供するローリングアップデートは、実行するアプリケーションに以下2つの（クラウドネイティブなマイクロサービスアプリの）性質を担保することで、ダウンタイムゼロのローリングアップデートが実現できる。

- APIによる疎結合
  各アプリケーションの接続が、明確に定義されたAPIのみを介して行われること。
- 後方互換性と前方互換性
  （ある程度）どのバージョンであっても、他のアプリケーションがそのアプリケーションを利用できること。

これらによって実現される、ゼロダウンタイムローリングアップデートの描像は以下である。

1. 元々、`v1.0.0`のイメージを使用するコンテナをラップしたPodのためのDeploymentが存在していたとする。レプリカ数は2。
2. Podの使用するイメージバージョンを`v2.0.0`に更新してDeploymentに再度適用。
3. Deploymentは古いReplicaSetは削除せずに、新しいReplicaSetを作成する。
4. 古いPodを1つ削除するのと同時に新しいPodを1つ作成する。
5. この時、新旧のPodが1つずつクラスターに存在しているが、後方・前方互換性が担保されていれば、このPodを使用するサービスはPodの新旧に影響されずに稼働し続ける。（つまり、クラスター全体として、サービスは停止していない）
6. 古いPodを削除し、新しいPodを生成
7. 全てのPodが新しいバージョンになる。

※古いReplicaSetは削除されず、Pod0台のまま残る。ただしこれはDeploymentの設定に依存する。

![rollingUpdate0](../img/book-summary/KubernetesTextbook/6/rollingUpdate0.png)

**--ロールバック--**
上で説明したローリングアップデートの話が理解できていればロールバックも簡単である。
**新しいReplicaSetのPodを0台にし、古いReplicaSetのPodを増やせば良い。**
Deploymentが古いReplicaSetを削除しないのはこのためで、いわゆる前のバージョンを保存しておいて、すぐ元に戻せるようにするためである。

### 6-2: Deploymentの作成

**--マニフェストファイル--**
Deploymentのマニフェストファイルの構成は、

- `matadata.name`にDeployment名
- `spec.replicas`にレプリカ数
- `spec.selector.matchLabels.app`にReplicaSetが管理するPodの`metadata.labels.app`を指定
- `spec.revisionHistoryLimit`に保存しておくReplicaSetの前バージョンの数を定義
- `spec.progressDeadlineSeconds`にPodの起動猶予を定義。つまり、新しいPodに生まれ変わったらこの時間は異常があっても上に報告しない。
- `spec.strategy`にローリングアップデートの定義
- `spec.template`にPodの定義

を書く。具体的には例えば

```sh
apiVersion: apps/v1
kind: Deployment
metadata:
  # Deployment名
  name: hello-deploy
spec:
  # Podレプリカ数
  replicas: 10
  # ReplicaSetが管理するPodを指定する（template.metadata.labels.appと一致させる）
  selector:
    matchLabels:
      app: hello-world
  # 保持するReplicaSetの数（5回前までロールバックできる）
  revisionHistoryLimit: 5
  # Podレプリカの起動待ち限界
  progressDeadlineSeconds: 300
  minReadySeconds: 10
  # ローリングアップデートの定義
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
  # Podのテンプレート定義
  template:
    metadata:
      labels:
        app: hello-world
    spec:
      containers:
      - name: hello-pod
        image: nigelpoulton/k8sbook:1.0
        ports:
        - containerPort: 8080
        resources:
          limits:
            memory: 128Mi
            cpu: 0.1
```

**--コマンド--**
Deploymentに関するkubectlコマンドは

```sh
# マニフェストの宣言
kubectl apply -f ${file_path}

# 取得
kubectl get deploy ${deployment_name}

# 詳細情報を見やすい形で表示
kubectl describe deploy ${deployment_name}
```

ReplicaSetに関するkubectlコマンドは

```sh
# 取得
# ReplicaSet名はDeployment名に（Pod情報の）ハッシュを追加したもの
kubectl get rs

# 詳細情報を見やすい形で表示
kubectl describe rs ${replicaset_name}
```

**--スケーリング方法--**
Podレプリカ数を変える方法において、宣言型はもちろんマニフェストファイルを経由する方法である。
Deploymentのマニフェストにおいて`spec.replicas`の値を変更し、`apply`コマンドで登録すれば良い。Kubernetesもこれを推奨している。

一方命令型では、`scale`コマンドによって以下のように手動でスケーリングすることができる。

```sh
kubectl scale deploy ${deploy_name} --replicas ${replica_num}
```

### 6-3: ローリングアップデートを実行する

**--概要--**
ローリングアップデートとはわかりにくく言えば「段階的なアップデート」のことであり、ロールアウト、リリース、ゼロダウンタイムアップデートなどは全て同義である。

ローリングアップデートは、Podのアプリケーションのバージョンアップなどを行う時に実行されるもの。
アプリのバージョンを更新する場合は、古いPodを削除して新バージョンのPodをデプロイするが、Podを一気に削除した後に新バージョンのPodをデプロイすると、その間アプリケーションを使用できなくなってしまう。
1つずつ（数は指定できる）削除と新規作成を実施することで、アプリケーションが利用可能な状態を維持しつつアプリケーションのバージョンアップを行うことができる。
この時新しいReplicaSetが作成され、古いバージョンと新しいバージョンを管理するReplicaSetが区別される。

![rollingUpdate](../img/book-summary/KubernetesTextbook/6/rollingUpdate.png)

開発者はローリングアップデートを実行するという意識を持つ必要はなく、Podの`containers.image`などが更新された新たなDeploymentのマニフェストを`apply`するだけで勝手にローリングアップデートが実行される。
※ただし、ローリングアップデートの仕方をDeploymentマニフェストで定義する必要あり。

余談だが、ローリングアップデート中にスケールアップ（Podの数が増加）が発生した場合、新たなPodレプリカは新旧2つのReplicaSetに振り分けられる。
振り分けの割合は、新旧2つのReplicaSetの保有するPod数らしい。（つまり3:6だったら、追加されたPodも3:6で振り分けられる）

**--マニフェスト--**
ローリングアップデートの実行方法を定義するには、Deploymentマニフェストに以下のように記述する必要がある。

```sh
...
revisionHistoryLimit: 5
progressDeadlineseconds: 300
# レプリカ置き換えの待機時間
minReadySeconds: 10
strategy:
  # ローリングアップデートの実行方法
  type: RolingUpdate
  rollingUpdate:
    # ローリングアップデート中に指定レプリカ数を下回って良い限界数（何個ずつ削除するか）
    maxUnavailable: 1
    # ローリングアップデート中に指定レプリカ数を上回って良い限界数（何個ずつ生成するか）
    maxSurge: 1
...
```

- `minReadySeconds`
  新規作成されたPodレプリカが`Ready`状態になり、ここで設定した秒数だけ待機して問題なければこのPodを正常とみなし、次のPodの更新に移る。
- `maxUnavailable`
  AI曰く、古いPodを同時に削除できる数。
- `maxSurge`
  AI曰く、新しいPodを同時に生成できる数。

![minReadySeconds](../img/book-summary/KubernetesTextbook/6/minReadySeconds.png)

**--コマンド--**
ローリングアップデートに関するコマンドは

```sh
# ローリングアップデートの進行状況の監視
kubectl rollout status deploy ${deploy_name}

# ローリングアップデート更新情報の取得
# UP-TO-DATEが新バージョンのPod数
kubectl get deploy ${deploy_name}
# ローリングアップデート更新情報の詳細取得
kubectl describe deploy ${deploy_name}

# ローリングアップデート一時停止
kubectl rollout pause deploy ${deploy_name}
# ローリングアップデート再開
kubectl rollout resume deploy ${deploy_name}
```

### 6-4: ロールバックを実行する

**--概要--**
ロールバックとは、大まかに「以前のバージョンに戻すこと」である。

**Kubernetesにおいては、以前のバージョンのReplicaSet情報を保存しておくことで、ロールバックを可能にしている。**
各バージョンのReplicaSet名は`${deployment}-${hash}`であり、バージョンごとに異なるハッシュが割り振られる。
このハッシュ値は

- Podの`label.pod-template-hash`
- ReplicaSetの`Selector`

に勝手に設定される。これによって各Replicasetが管理するバージョンのPodを識別できる。
ちなみに、Pod名は`${deployment}-${hash}-${hash2}`?でどのReplicaSetに管理されているか一目で分かるようになっている。

**--コマンド--**
Kubernetesにおけるロールバックは、ローリングアップデートと同じ`rollout`コマンドによって実行される（`undo`は付くが、、、）。
これは現在のバージョンのPodを終了し、別のバージョンのPodをデプロイするという点でロールバックが「アップデート」の枠組みに入るからである。

ロールバックに関するコマンドは

```sh
# Deploymentの履歴（revision）を確認
kubectl rollout history deploy ${deploy_name}

# ReplicaSet（の履歴）を確認
kubectl get rs

# ロールバック
kubectl rollout undo deploy ${deploy_name} --to-revision=${revision}
```

---

## 7: Kubernetesのサービス

### 7-1: サービス理論

**--Serviceの必要性--**
「スケールダウン」「ローリングアップデート」「ロールバック」「ノードのメンテ」「障害」などのイベントが発生すると、Podは削除される。
このように**KubernetesにとってPodは一時的なオブジェクトであり、信頼性が低く、常にリクエストを処理してくれるとは限らない**。

この問題を解決するためにKubernetesには、**常にリクエストを処理してくれる信頼性の高いアプリケーションのDNS名を提供するリソース**が存在する。これがServiceである。つまり、Serviceによって管理されるDNS名にリクエストを送れば、いつでもそのリクエストを処理してくれる。

![serviceOverview](../img/book-summary/KubernetesTextbook/7/serviceOverview.png)

**--Podの特定方法--**
Serviceはアクセス（リクエスト）を受け取り、それを送信するPodをラベルセレクターによって特定している（Deploymentが管理するPodを特定しているのと同じ）。

その指定方法は、Podに以下のように`project`と`zone`のラベルを指定したとして

```yaml
# Deployment
...
template:
  metadata:
    labels:
      project: tkb
      zone: prod
...
```

Serviceで以下のように、`selector`プロパティを指定することで、ラベルを持つPodにトラフィックを送信するように設定できる。

```yaml
# Service
...
spec:
  selector:
    project: tkb
    zone: prod
...
```

Podの特定の際、Serviceの`selector`に指定した全てのラベルを持つPodにのみトラフィックを送信する。指定したラベルに加えて他のラベルを持つPodにも送信する。

![selector](../img/book-summary/KubernetesTextbook/7/selector.png)

**--Endpointslices--**
実はServiceとPodの間にEndpointSliceというリソースが存在する。これは、Kubernetes（EndpointSliceコントローラー）がServiceの作成を感知して自動的に作成されるリソースであり、**Serviceが`selector`で指定するラベルを持ち且つ正常な（リクエストを処理できる）Podの一覧を保持する**もの。

![endpointslice](../img/book-summary/KubernetesTextbook/7/endpointslice.png)

※古いバージョンのKubernetesではEndpointSliceではなくEndpointというリソース？を使用していたが、現在では非推奨。

**--Serviceの種類--**
Serviceには「ClusterIP」「NodePort」「LoadBalancer」の3つの種類が存在する。最も有用なのはLoadBalancerであるが、他2つの種類の進化系なので1つずつみていく。

マニフェストファイルにおいては`spec.type`に以下のように指定する。

```yaml
...
spec:
  # ClusterIP
  type: ClusterIP
  # NodePort
  type: NodePort
  # LoadBalancer
  type: LoadBalancer
...
```

- ClusterIP
  デフォルトのServiceタイプで、**Cluster内部でのみ使用できるDNS名を持つ**（Cluster内部DNSに自動で登録される）。つまりクラスター上の全アプリケーションがそのDNS名でアクセスできるようになるが、クラスター外からはそのDNS名でアクセスできない。
- NodePort
  ClusterIPの進化版で、**ClusterIPに外部からアクセスするための専用ポート(NodePort)を各クラスターノードに追加**する。外部からのアクセスはNodePortを経由してClusterIPにアクセスできる。
  ただしNodePortは`30000~32767`の番号を使用する必要があり、外部からのアクセス時にノードが正常であることを知っておく必要がある。
- LoadBalancer
  NodePortの進化版で、**パブリックなDNS名と低いポート番号を持ち、正常なNodeにトラフィックを制御する高可用なロードバランサーを追加**する。
  マニフェストの書き方は

  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    # クラスターDNSに登録する名前
    name: ${service_name}
  spec:
    type: LoadBalancer
    ports:
    - port: ${LoadBalancer_port}
      targetPort: ${application_container_port}
  ...
  ```

![loadBalancer](../img/book-summary/KubernetesTextbook/7/loadBalancer.png)

### 7-2: Serviceの実践編

Servicesをデプロイする前にPod(Deployment)をデプロイしている必要がある。なぜなら、Servicesが操作するトラフィック先が存在する必要があり、Podのラベルでアクセス先をフィルターしているからである。

**--コマンド--**
Servicesに関連するコマンドは

```sh
# マニフェストを使用せずServicesをデプロイ
kubectl expose deploy ${deployment_name} --type=${service_type}
# マニフェスト経由でデプロイ
kubectl apply -f ${file_path}

# IP系の情報を含めてsvcを取得
kubectl get svc -o wide

# Endpointslice取得
kubectl get endpointslices

# 削除
kubectl delete svc ${svc_name}
# マニフェスト経由で削除（Serviceを削除すると関連するEndpointsliceは自動で削除される）
kubectl delete -f ${file_path}
```

**--kubernetesシステムサービス--**
`kubectl get svc`コマンドを実行すると、`kubernetes`というServicesが存在することがわかる。これは、Cluster上の全PodとコンテナにKubernetes APIを公開するためのシステムService。

---

## 8: Ingress

Ingressは1つのLoadBalancerServiceを通じて複数のWebアプリケーションにアクセスするためのリソース。
サービスメッシュの機能と一部重複しているので、どちらか一方でいいかも。

### 8-1: Ingressの環境設定

ここではIngressの存在意義について述べる。

NodePortやLoadBalancerタイプのServiceを用いることで、外部からアプリケーションに接続することができるようになるが

- NodePortにアクセスするにはNodeのIPアドレスを知っておく必要がある
- LoadBalancerはアプリケーション1つにつき、クラウドの提供するロードバランサーが1つ必要になる

というような面倒事が存在する。
Ingressはこれらの問題を解決する。つまり、**クラウドの提供するロードバランサー（ポートは`80`か`443`）を1つだけ使用して、複数のアプリケーションに対して外部からNodeのIPアドレスを知らずにアクセスできる**ようになる。

![ingressOverview](../img/book-summary/KubernetesTextbook/8/ingressOverview.png)

### 8-2: Ingressアーキテクチャ

Ingressには通常Ingressコントローラーが追加で必要であるが、Kubernetesには組み込まれていないので別途でインストールする必要がある。
Ingressコントローラー作成後に、IngressをデプロイすることでルーティングのルールをIngressコントローラーに伝えることができる。
Ingressコントローラーはロードバランサーに来たリクエストのホスト名を見て、ルールに基づいて各サービスにルーティングする。

※ここにおけるロードバランサーとは、例えばELBなどの外部のロードバランサーであり、Kubernetesリソースとは別物。ただしDockerDesktopにおいてはIngressコントローラーがロードバランサーの役割も兼ねる。

※Ingressは実体はなく、ルーティングのルールを指す。どちらかと言えばIngressコントローラーの方が実体がある。こいつがルールに基づいてトラフィックを操作する。

![ingressController](../img/book-summary/KubernetesTextbook/8/ingressController.png)

### 8-3: Ingressの実践

**--Ingressコントローラー--**
**IngressコントローラーはKubernetesの標準リソースではなく、Kubernetesリソース(deploy,svc,po,configmap)で作成されたアプリケーション**である。

>ではなぜIngressは標準リソースなのに、その利用には「外部アプリ」であるIngressコントローラーが必要なのか？

に対する答えはおそらくPod内のコンテナにKubernetesが関与しないのと同じように、Kubernetesは「アプリケーションの実装には立ち入らない」から。
Ingressコントローラーはさまざまなリクエストを処理するルーティング方法に関係するものなので、アプリケーション側なのだろう。

ここでは有名なNginxベースのIngressコントローラーをインストール（デプロイ）する方法を見る

```sh
# インストール
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.12.0/deploy/static/provider/cloud/deploy.yaml

namespace/ingress-nginx created
serviceaccount/ingress-nginx created
serviceaccount/ingress-nginx-admission created
role.rbac.authorization.k8s.io/ingress-nginx created
role.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrole.rbac.authorization.k8s.io/ingress-nginx created
clusterrole.rbac.authorization.k8s.io/ingress-nginx-admission created
rolebinding.rbac.authorization.k8s.io/ingress-nginx created
rolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
configmap/ingress-nginx-controller created
service/ingress-nginx-controller created
service/ingress-nginx-controller-admission created
deployment.apps/ingress-nginx-controller created
job.batch/ingress-nginx-admission-create created
job.batch/ingress-nginx-admission-patch created
ingressclass.networking.k8s.io/nginx created
validatingwebhookconfiguration.admissionregistration.k8s.io/ingress-nginx-admission created

# 確認
kubectl get pod -n ingress-nginx

# 削除
kubectl delete -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.12.0/deploy/static/provider/cloud/deploy.yaml
```

確認コマンドからわかるように、Nginx IngressコントローラーはPodで実行されるアプリケーションである。

**--Ingressクラス--**
Ingressclassは**IngressがどのIngressコントローラーで実装するかを指定するためのKubernetesリソース**であり、Ingressコントローラーと1対1で存在するもの。

Ingressコントローラーがアプリケーションである以上、さまざまな種類が存在し、1つのCluster内に複数のIngressコントローラーが存在する可能性がある。この時、Ingressを`apply`した時にどのIngressコントローラーでそのIngressのルーティングルールを実現するかを指定するためにIngressclassが必要である。

![ingressclass](../img/book-summary/KubernetesTextbook/8/ingressclass.png)

マニフェストは例えば

```yaml
apiVersion: networking.k8s.io/v1
kind: IngressClass
metadata:
  name: nginx
spec:
  controller: k8s.io/ingress-nginx
```

コマンドは

```sh
# 取得
kubectl get ingressclass

# 詳細
kubectl describe ingressclass ${ingressclass_name}
```

**--Ingressオブジェクトの作成--**
Ingressに関するコマンドは

```sh
# デプロイ
kubectl apply -f ${file_path}

# 取得
kubectl get ing
```

マニフェストは

```sh
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ${ingress_name}
  annotations:
    # コントローラーにアプリが期待するパス"/"に書き換えるための指示
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: ${ingressclass_name}
  rules:
  # host名の指定。複数指定できる
  - host: ${host_name}
    http:
      paths:
      # path指定。複数指定できる
      - path: /
        pathType: Prefix
        # 指定portで待機しているClusterIPServiceにトラフィックを送信
        backend:
          service:
            name: ${service_name}$
            port:
              number: ${port}$
```

**--DNS名前解決--**
Service, Ingressコントローラー, Ingressclass, Ingressの作成で、ロードバランサーから来たトラフィックをルールに則ってServiceにルーティングすることができるようになる。

「ロードバランサーから来た」は厳密には、「指定したロードバランサーに来たリクエスト」のことを指していて、このロードバランサーのIPアドレスは`kubectl get ing`の`ADDRESS`にて指定されている。

この状態ではまだルーティングが上手くいかない場合が多い。**残る設定は、Ingressで指定したホスト名やパスが先の`ADDRESS`のロードバランサーにトラフィックが送信されるようにすること**である。これは所謂、IPアドレスとURIのDNS名前解決を実施すべきということである。

※ローカルPCで指定のIPアドレスに名前解決したければ

```sh
sodu vi /etc/hosts

${ADDRESS} ${host_name} 
```

のように設定しておく必要がある。

---

## 9: Kubernetes上のWasm

WasmとはWebAssemblyの略で、クラウドコンピューティングにおいて「コンパクト化」「高速化」「移植性向上」が進み

![evolveHistory](../img/book-summary/KubernetesTextbook/9/evolveHistory.png)

のように進化してきた。つまりWasmはコンテナの進化版。

### 9-1: Wasmプライマー

**--単語--**
まずWasmを説明するために必要な単語の意味について説明しておく。

- バイナリ
  2進数
- ISA(Instruction Set Architecture)
  命令セットアーキテクチャー。CPUが理解・実行できる言語やその体系。
- ARM
  macbookやiPhoneなどで使用されているISA。
- x64
  windowsなどで使用されているISA。macも昔はx64を使用していた（x64の時はIntelが入っていてOpenMPも使えた？）。
- ネイティブコード
  CPUが直接実行できるコード。ほぼ機械語。
- サンドボックス
  ンピュータシステムにおいて、アプリケーションやプロセスを隔離された環境（外部への接続が制限された領域）で実行するための技術。
- runc
  コンテナを起動するためのツール。OCI準拠のランタイムでコンテナ界隈共通の資産。
- shim
  containerdがコンテナを管理するための中間プロセスで、実際のコンテナ管理を行うもの。

**--概要--**
Wasmとは、2017年に登場したブラウザやNode.jsで実行できる低レイヤーな実行形式であり、高速処理を目的とした技術である。特徴は

- 高速に実行できる
  - バイナリ形式（テキストではない）
  - C・Rust・Goなどの複数言語からコンパイル可能
- デフォルトで全て拒否するサンドボックス内で実行
- ビルドすればどこでも実行可能
- 起動が1秒未満

この高速性により、ブラウザ上でも高負荷な処理が可能になり、以下の用途で活用されている。

- 画像処理・編集
- 動画処理・編集
- ゲーム

一方で、UI操作やDOM操作は不可能であるため、JavaScriptと組み合わせて使用される。JS内でWasmに重い計算をしてもらうでwebアプリが実装される。

Wasmは、ARMやx86のような実際のCPUの命令セットではなく、仮想的な命令セット（仮想ISA）である。
そのため、どの環境でも同じWasmコードを実行できる（例えばWasmにビルドすればx64でもARMでも実行できる）。

通常のJavaScriptは以下の流れで実行される：

構文解析（パース）
インタプリタ実行
JITコンパイル（最適化）

一方、Wasmはあらかじめコンパイルされた構造を持っているため、
JavaScriptに比べて最適化しやすく、効率よくネイティブコードへ変換できる。

このため、結果として高速に実行される。

**--セキュリティ--**
コンテナはデフォルトでオープンなセキュリティポリシーを採用しているが、**Wasmはデフォルトでクローズド**なセキュリティポリシーを採用している。

Wasmアプリはサンドボックス内で実行され、Wasmで使用されるサンドボックスは完全に外部との接続が遮断されており、外部との接続のためにはJSやWASIが必要。
WASI(WebAssembly System Interface)とは、サンドボックス化されたWasmアプリが外部サービスに安全にできるようにするもの。

**--移植性--**
コンテナは異なる種類のCPU(Windows,Linuxなど)に移植することはできない。同じCPUであれば持ち運びができるだけ。
一方でWasmは、**一度ビルドすればWasmランタイムを搭載するシステムではどこでも実行可能**なので移植できる。

### 9-2: Kubernetes上のWasmを理解する

**--コンテナの管理描像--**
Kubernetesにおいて通常のコンテナを運用する際の描像は以下。

![containerManage](../img/book-summary/KubernetesTextbook/9/containerManage.png)

重要なのは**Kubernetesはcontainerdに命令を出すだけで、それ以下のruncやshimの存在は全く意識していない**こと。

**--Wasmの実行方法--**
KubernetesにおいてWasmアプリを実行するためには、containerdが管理するruncとshimをWasm shim（Wasm用のランタイムとshimを含む単一のもの）に置き換えれば良い。

![replacement](../img/book-summary/KubernetesTextbook/9/replacement.png)

このWasm shimは様々な種類が提供されていて、Spin shimやSlight shimなどがある。

このように異なるWasm shimが共存するKubernetes Clusterでは、**Kubernetesが適切なWasm shimが存在するNodeにスケジュールできるようにNodeにラベルを付与し、RuntimeClassオブジェクトを設定**する必要がある。

**--Wasmの実行手順--**
containerdを使用してKubernetesでWasmアプリをデプロイする手順（流れ）は

1. アプリを作成し、Wasmとしてコンパイル
2. コンパイルしたWasmをOCIイメージとしてパッケージ化
3. OCIイメージをレジストリに保存
4. 適切なWasm shimをNodeにインストール
5. Nodeにラベルを付与
6. NodeラベルとWasm shimを指定するRuntimeClassを作成
7. Wasmアプリ用のPodを作成
8. PodでRuntimeClassを参照
9. Podをデプロイ
    - KubernetesはRuntimeClassのNodeセレクターに一致するNodeにPodをスケジュール
    - kubeletがRuntimeClassのshim情報を使用してcontainerdに命令
    - containerdは要求されたshimを使用してアプリを起動

### 9-3: Kubernetes上のWasmを実際に使ってみる

---

## 10: サービス検出の詳細

### 10.1: シーンを設定する

**--Pod間通信の概要--**
Kubernetesクラスターに異なるアプリケーションA,Bをデプロイしている場合を考える。
A-B間でリクエストを送信するのに必要なのは「他のアプリのService名」のみで、これさえ知っていればKubernetes側でいい感じにリクエストの送信を実施してくれる。
本章では、Kubernetes側で行っているService名からPodへの通信の仕組みを見ていく。

概要としては

1. 開発者がアプリケーション内に通信したい他アプリケーションのService名を設定する
2. アプリケーションがKubernetesサービスレジストリに他アプリケーションのService名を渡してIPアドレスをリクエスト
3. KubernetesサービスレジストリがIPアドレスを返却
4. アプリケーションはIPアドレスによって他アプリケーションにリクエスト

![serviceDetectOverview](../img/book-summary/KubernetesTextbook/10/serviceDetectOverview.png)

### 10.2: サービスレジストリ

**--サービスレジストリとは--**
Kubernetesにおけるサービスレジストリとは、その名前の通り「Service名とそれに紐づくIPアドレスを保管しておくもの」。
Kubernetesの用語としては「クラスターDNS」と呼ばれ、コントロールプレーン上で実行される。

クラスターDNSの構成は

| リソース | リソース名 | ネームスペース | 備考 |
| - | - | - | - |
| Deploy | coredns or kube-dns | kube-system | レプリカ数2 |
| Service | kube-dns | kube-system | Podへのアクセス管理 |

![clusterDNS](../img/book-summary/KubernetesTextbook/10/clusterDNS.png)

10.1 で言っていた、「アプリケーションがKubernetesサービスレジストリにリクエスト」というのは、kube-dns Serviceにリクエストを送っているということである。

**--サービス登録--**
クラスターDNSの機能は「Service名からIPアドレスにマッピングできる」ことである。では、このセットはどのように登録されるのだろうか。
結論は、「Serviceが生成されたら自動で登録される」だ。その流れは以下の通り

1. YAMLによってServiceをデプロイ（Service名は開発者が命名済み）
2. KubernetesがServiceにIPアドレス(ClusterIP)を割り振る+クラスタストアに保存
3. クラスターDNSはServiceの生成を検知し、Service名とそれに紐づくIPアドレスを保存
4. Serviceに伴うEndpointSliceが生成
5. kube-proxyがServiceのIPに対するリクエストをPodへルーティングできるように待機

※クラスターDNSがService名とそれに紐づくIPアドレスを保存する際、具体的には`DNS A レコード`と`SRV レコード`を登録している。

**--コマンド--**
クラスターDNSのリソース取得コマンドは以下。

```sh
# coredns Podの取得
kubectl get pod -n kube-system -l k8s-app=kube-dns

# coredns Deploymentの取得
kubectl get deploy -n kube-system -l k8s-app=kube-dns

# kube-dns Serviceの取得
kubectl get svc -n kube-system -l k8s-app=kube-dns
```

### 10.3: サービス検出

**--サービス検出の手法--**
10.1 で述べたように、クラスター上のアプリケーションはService名をクラスターDNSに送信することで、他アプリケーションのIPアドレスを取得している。
ここでの疑問は、すべてのアプリケーションはクラスターDNSにどのようにリクエストを送っているのか？ということである。

その答えは「Kubernetesが自動で全てのコンテナに以下のような設定をすることで、クラスターDNSにリクエストを送信するようにになる」

```conf
; in container in pod
; cat /etc/resolv.conf
search ${namespace}.svc.cluster.local svc.cluster.local cluster.local
nameserver ${クラスターDNSのIP}  ; kube-dns service のClusterIPに一致
options ndots:5
```

**--ClusterIP ルーティング--**
ここで言うClusterIPはkube-dnsのものだけでなくアプリケーションServiceのものも含まれる（多分）。

ClusterIPはサービスネットワーク上にあり、ClusterIPへトラフィックを送るためのルーティングがない。
そこで、コンテナがClusterIPにリクエストを送る際にはデフォルトゲートウェイに送信する。
コンテナのデフォルトゲートウェイはコンテナの稼働しているNodeである。
Nodeも自身のデフォルトゲートウェイに送信し、最終的にNodeのカーネルがトラフィックを処理する。

↑これではPodまでトラフィックが行かないと思われるが、kube-proxyがClusterIPトラフィックを検知して適切なPodのIPにリダイレクトする。つまり、NodeのカーネルでClusterIPトラフィックを処理するたびにkube-proxyが横入してPod IPを返すようになっている。

![clusterIPRouting](../img/book-summary/KubernetesTextbook/10/clusterIPRouting.png)

### 10.4: サービス検出とNamespace

**--Namespaceを考慮したサービス検出--**
ここまでで他アプリケーションにリクエストを送るためにService名が必要であることを述べた。
これは同一Namespceに属するアプリケーション同士の通信の話である。
ではNamespaceを横断する通信はどのように実現されるのか。

結論は「完全修飾ドメイン名(FQDN/Fully Qualified Domain Name)で指定する」。
KubernetsにおけるServiceの完全修飾ドメイン名は以下の形式である。

```sh
${service_name}.${namespace}.svc.cluster.local
```

同一NamespaceではService名で指定していた部分を、異なるNamespaceでは上のような形式で指定する必要がある。

![serviceDetectWithNamespace](../img/book-summary/KubernetesTextbook/10/serviceDetectWithNamespace.png)

※`cluster.local`はクラスタードメインと呼ばれる

### 10.5: サービス検出のトラブルシューティング

**--調査対象リソース--**
サービス検出においてトラブルが発生した際の調査対象リソースは以下。

| リソース | リソース名 | ネームスペース | 備考 |
| - | - | - | - |
| Pod | coredns-... | kube-system | coredns Deploymentで管理される |
| Service | kube-dns | kube-system | ClusterIP Service |
| EndpointSlice | kube-dns-... | kube-system | |

**--トラブルシューティング--**
まずはPodを確認

```sh
# 実行中であることの確認
kubectl get deploy -n kube-system -l k8s-app=kube-dns
kubectl get pod -n kube-system -l k8s-app=kube-dns

# ログの確認
kubectl logs coredns-... -n kube-system
```

次にServiceとEndpointSliceを確認

```sh
# 稼働中であることとClusterIPがコンテナの/etc/resolve.confと一致していることを確認
kubectl get svc kube-dns -n kube-system
kubectl get endpointslice -n kube-sytem -l k8s-app=kube-dns
```

ここまで正常ならKubernetes Serviceの名前解決を行って機能しているか確認
あるいはcoredns Podの再起動

```sh
# 再起動
kubectl delete pod -n kube-system -l k8s-app=kube-dns
```

---

## 11: Kubernetesストレージ

本章ではKubernetesで（AWSやAzureなどの）外部ストレージを利用する方法を見る。
基本的には外部側が、自身が提供するストレージをKubernetes上で利用するためのプラグインを提供しているので、そのプラグインを使用することで外部ストレージとKubernetesを接続することができるようになる。
※外部ストレージは外部側に依存するので全てのKubernetesで共通の構成はここでは説明できない。GKEとLKEの例で説明する。

### 11-1: 全体像

**--外部ストレージの例と用途--**
ここで言う外部ストレージとは、クラウドとオンプレミスに限らず以下のようなものを内包している

- ブロックストレージ（AWS EBSなど）
- ファイルストレージ（AWS EFSなど）
- オブジェクトストレージ（AWS S3など）

外部ストレージの用途は例えば、クラスター上で永続ボリュームを使用したいアプリケーション（DBなど）を運用する際にボリュームを外部に置くことで、Nodeのメモリ圧迫を防いだりできる。

**--構成概要--**
Kubernetesで外部ストレージを使用する際は以下のような構成になる。

![storageArchitecture](../img/book-summary/KubernetesTextbook/11/storageArchitecture.png)

各用語は

- CSI(Container Storage Interface)
    外部ストレージとKubernetesのインターフェース。
- SC(StorageClasses)
    外部ストレージのクラス（どのような設定のストレージか）を定義し、CSIとやり取りする。
- PV(PersistentVolumes)
    外部ストレージのマッピング。Podにマウントする実質的なKubernetes上のボリューム。
- PVC(PersistentVolumeClaims)
    PodがPVにアクセスする時に許可を出すもの。

**--永続ボリューム構築フロー--**
上記の構成を構築する際、そのほとんどは自動化されている。自動的な手続きを含め以下のような流れで構築される。
Kubernetes上にCSIプラグインをインストールし、SCがデプロイされている状況から始める。

1. 外部ストレージを必要とするPodと関連するPVCをデプロイ。
2. PodはPVCを参照しており、PVCは指定したSCに対してPVの作成を要求。
3. SCはCSIプラグインを介して外部ストレージ作成を要求
4. CSIプラグインは外部ストレージ（AWS EBSなど）を作成し、SCに報告
5. SCはPVを作成し、外部ストレージをPVにマッピング
6. PodはPVをマウントし、ボリュームとして使用

※正確にはSCは定義が書かれたドキュメントで、永続ボリュームサブシステムや外部との処理をするのはcsi-controller（CSIプラグインの一部）。CSIプラグインはCSI Driverと言ったりする。

![buildFlow](../img/book-summary/KubernetesTextbook/11/buildFlow.png)

### 11-2: ストレージプロバイダー

ストレージプロバイダー（ストレージプロビジョナー）とは、外部システムのストレージの総称である。
各プロバイダーごとに独自のCSIプラグイン（ebs-csiのような粒度）が公開されており、自身のKubernetesにインストールすると `kube-system` Namespace上にCSIプラグインPodが生成される。

![image](../img/book-summary/KubernetesTextbook/11/csiPlugin.png)

### 11-3: コンテナストレージインターフェース(CSI)

11.2節と同じ。CSIプラグインはCSI Driverとも呼ばれる。

例としてebs-csi Podやefs-csi PodなどがKubernetesで稼働する。
恐らくNodeに1つずつ存在する。

### 11-4: Kubernetes永続ボリュームサブシステム

**永続ボリュームサブシステムとは、アプリケーションがストレージを要求・アクセスできるようにするAPIオブジェクトセット**であり、主にPV,PVC,SCで構成される。

※Kubernetesの仕組みで、複数のPodが同じPVに書き込むのは禁止される。そのためDBを冗長化する際、書き込み（CRUDの内CUD）操作ができるのは1つのPodだけ。

**--PVC--**
PersistentVolumeClaimsの略で、PodからPVへのアクセス許可をするもの

![pvc](../img/book-summary/KubernetesTextbook/11/pvc.png)

**--PV--**
PersistentVolumesの略で、Kubernetes上で外部ストレージをボリュームとして使用できるようにするもの。外部ストレージがマッピングされる。このマッピングは1対1。
pvはNamespace管理ではない。外部ストレージがClusterとは切り離されていることからも明らか。

![pv](../img/book-summary/KubernetesTextbook/11/pv.png)

※ボリュームのメモリはNodeのメモリを消費せず、外部ストレージのメモリを消費する。Podに割り振られているメモリはDBのキャッシュなどに使用されている。

**--SC--**
StorageClassesの略で、外部ストレージインスタンスとPVを動的に作成するためのもの。
外部ストレージの種類によって異なるSCを定義する必要がある。

### 11-5: ストレージクラスによる動的プロビジョニング

**--ストレージクラス--**
ストレージクラスのマニフェストファイルは

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
    name: ${sc_name}
provisioner: ${csi_plugin}
parameters:
    # 使用する外部ストレージごとに専用のパラメータを設定する
allowedTopologies:  # ボリュームとレプリカを配置する場所
...
```

その他、ストレージクラスについて特記事項は

- 一度デプロイすると変更できない
- Namespace管理ではない
- parameterはプラグイン固有のためドキュメントを参照すべき
- 「プロビジョナー」「プラグイン」「ドライバー」は同じ意味で使用されることがある

**--動的プロビジョニング--**
SCを使用して外部ストレージとPVのセッティングを自動化（動的プロビジョニング）する際は以下の3つを行えばよい。

1. CSIプラグインをClusterにインストール・設定
2. SCをデプロイ
3. 「SC経由でボリュームを要求するPVC」を持つPodをデプロイ

※手順1について、EKSなどのクラウドKubernetesサービスでは、同クラウドの外部ストレージ用（EBSなど）のCSIプラグインがプリインストールされているため、簡単に使用できる。

上記手順の2,3を満たすマニフェストファイルは以下

```yaml
apiVersion: v1
kind: Pod
metadata:
    name: ${pod_name}
spec:
    volumes:
        - neme: ${volume_name}
            # 参照するPVCの設定
            persistentVolumeClaim:
                claimName: ${pvc_name}
...
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
    name: ${pvc_name}   # Podで指定したclaimNameに一致させる
spec:
...
# PodからPVへのアクセスのモードを設定
accessModes:
- ReadWriteOnce
    resources:
    requests:
    storage: ${storage_memory}
    # 参照するSCの名前
    storageClassName: ${sc_name}
---
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
    name: ${sc_name}    # Pvcで指定したstorageClassNameに一致させる
provisioner: ${csi_plugin}
parameters:
...
# ストレージの処理方法
reclaimPolicy: ${delete_or_retain}
```

**--accessMode--**
accessModeはPVCに設定し、PodからPVへのアクセスを取り決めるもので、以下の3種類が設定可能

- ReadWriteOnce(RWO)
    PVC１つだけを読み取り/書き込み(R/W)モードでボリュームにバインドできる
- ReadWriteMany(RWM)
    複数のPVCを読み取り/書き込み(R/W)モードでボリュームにバインドできる
- ReadOnlyMany(ROM)
    複数のPVCを読み取り専用(R/O)モードでボリュームにバインドできる

※PV側は1つのモードでしか開くことができないので、PVCのaccessModeは混在させない方が良い

**--reclaimPolicy--**
reclaimPolicyはPVCが解放（削除？）された際に、関連するPVと外部ストレージを削除するかどうかを設定する。
`Delete`で一緒に削除、`Retain`で保持。

### 11-6: 実践編

**--volumeBindingModeオプション--**
SCに設定するvolumeBindingModeオプションはボリュームの作成タイミングをコントロールできる。
`Immediate`では、Podのデプロイを待たずにPVCが作成されたタイミングで外部ストレージとPVを作成する設定。
`WaitForFirstConsumer`では、Podのデプロイ後に外部ストレージとPVを作成する設定。

`WaitForFirstConsumer`でPodデプロイ前の状態ではPVCがPending状態になる。

**--コマンド--**
sc,pvc,pv関連のコマンドは

```sh
# sc取得
kubectl get sc

# sc詳細取得
kubectl describe sc ${sc_name}

# pvc取得
kubectl get pvc -n ${namespace}

# pvc削除
kubectl delete pvc -n ${namespace} ${pvc_name}

# pv取得
kubectl get pv
```

---

## 12: ConfigMapとSecret

### 12-1: 全体像

**--config分離の必要性と利点--**
アプリケーションは、アプリケーションバイナリ（多分ソースコードロジック部分）と、config（ネットワーク・認証・セキュリティの設定などアーキテクチャー寄りの部分？）で構成されている。

この2部構成のアプリケーションをイメージ化してしまうと、環境ごとに異なるイメージが必要となってしまう。
しかし理想は、configは環境ごとに用意し、アプリケーションバイナリを共通イメージとして使用することである。こうすることで以下の利点が得られる。

- テスト回数の削減
- イメージ数の削減
- アプリケーションバイナリに機密データが含まれないことによる公開安全性

Kubernetes ConfigMapは、この分離を実現するのに必要なリソースである。

### 12-2: ConfigMap 理論

**--ConfigMapとは--**
**ConfigMap(cm)とは、Podの外部に（機密性の無い）configデータを保存しておきPod実行時に挿入するためのリソース**である。
ここで言う機密性のないデータとは

- 環境変数
- webサーバー構成・DB構成などの構成ファイル
- ホスト名
- サービス名とサービスポート番号
- アカウント名

などである。
※証明書やパスワードなどの機密データの格納にはSecretを使用するべき。

**--ConfigMapマニフェスト--**
ConfigMapに格納するデータは複数のキーと値を持つオブジェクトである。
マニフェストファイルの`data`プロパティ配下に以下のように記述する。

```yaml
# 値が文字列・数値の場合
kind: ConfigMap
apiVersion: v1
metadata:
    name: ${configmap_name}
data:
    ${key1}: ${value1}
    ${key2}: ${value2}
    ...

# 値がファイルの場合
kind: ConfigMap
apiVersion: v1
metadata:
    name: ${configmap_name}
data:
    ${file_name}: |
        ${file_src_line1}
        ${file_src_line2}
        ...
```

※spec,statusがないのは、目的の状態と現在の状態の概念がなく自動復旧などの機能を備える必要がないから。

**--Podへの挿入手法--**
Podへのデータの挿入手法は以下の3つ

- 環境変数
- コンテナ起動コマンドのオプション
- ボリューム内のファイル

※ボリューム内のファイルとして挿入した場合のみ、ConfigMapのデータの変更を検知してボリューム内のファイルも自動更新される。

### 12-3: ConfigMapの実践

**--kubectlコマンド--**
kubectlコマンドにおいて、リソース名`configmap`は`cm`に省略可能である。
ConfigMapの作成コマンドは

```sh
# コマンドから生成（値が文字列・数値の場合）
kubectl create cm ${configmap_name} --from-literal ${key1}=${value1} --from-literal ${key2}=${value2} ...
# コマンドから生成（値がファイルの場合）
kubectl create cm ${configmap_name} --from-file ${file_path}

# マニフェストファイルからの宣言的な作成
kubectl apply -f ${file_name}
```

ConfigMapの取得コマンドは

```sh
# 取得
kubectl get cm -n ${namespace}

# 詳細取得
kubectl describe cm ${configmap_name} -n ${namespace}
```

**--Podへの挿入方法（環境変数）--**
Podのマニフェストファイルにおける`containers/env`にconfigMap由来の環境変数を指定する。

```yaml
apiVersion: v1
kind: Pod
...
spec:
  containers:
    - name: ${container_name}
      env:
        - name: ${environment_name1}
          valueFrom:
            configMapKeyRef:
              name: ${configmap_name}
              key: ${key1}
        - name: ${environment_name2}
          valueFrom:
            configMapKeyRef:
              name: ${configmap_name}
              key: ${key2}
...
```

Podがデプロイされ、該当のコンテナが起動する際にこれらの環境変数が注入される。
Podのコンテナ環境変数を確認したければ

```sh
kubectl exec ${pod_name} -n ${namespace} -- env
kubectl describe pod ${pod_name} -n ${namespace}
```

**--Podへの挿入方法（コンテナ起動コマンドオプション）--**
環境変数として挿入した際と同様にPodのマニフェストファイルにおける`ocntainers/env`に環境変数を指定する。異なるのは、起動コマンドでその環境変数を指定することである。

```yaml
...
spec:
  containers:
    - name: ${container_name}
      image: ${image}
      env:
        - name: ${environment_name1}
          valueFrom:
            configMapKeyRef:
              name: ${configmap_name}
              key: ${key1}
        - name: ${environment_name2}
          valueFrom:
            configMapKeyRef:
              name: ${configmap_name}
              key: ${key2}
      command: [ "/bin/sh", "-c", "echo $(${environment_name1}), $(${environment_name2})"]
...
```

**--Podへの挿入方法（ボリューム）--**
ボリューム経由でPodのコンテナにConfigMapデータを挿入するには、**PodのマニフェストでConfigMapボリュームを定義し、そのボリュームをコンテナにマウント**すれば良い。
ConfigMapの各エントリ（設定値の塊）はコンテナ内ファイルとして挿入・同期される。

![volume](../img/book-summary/KubernetesTextbook/12/volume.png)

Podのマニフェストは以下のような記述になる

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ${pod_name}
spec:
  volumes:
    - name: ${volume_name}
      configMap:
        name: ${configmap_name}
  container:
    - name: ${container_name}
      image: ${image}
      volumeMounts:
        - name: ${volume_name}
          mountPath: ${container_directory_path}
...
```

Pod内にファイルが配置されたかの確認は

```sh
kubectl exec ${pod_name} -n ${pod_name} -- ls ${container_directory_path}
kubectl exec ${pod_name} -n ${pod_name} -- cat ${container_file_path}
```

### 12-4: Secretを実際に体験

**--Secretとは--**
**Secretとは、機密データを保存しておく用のConfigMap。リソースとして区別しているだけ。**
機密データは例えば

- パスワード
- 証明書
- トークン

PodにSecretデータが挿入されるフローは

1. Secretが作成されると、Kubernetesは暗号化されていないオブジェクトとしてクラスタストアに保存
2. Podをスケジュール
3. Kubernetesは、暗号化されていないSecretをネットワーク経由でPod実行Nodeに転送
4. Node上のkubeletはPodとコンテナを起動
5. コンテナランタイムは、メモリ内のtmpfsファイルシステムを介してSecretをコンテナにマウントし、base64からプレーンテキストにデコード
6. アプリケーションがSecretを使用
7. Podの削除に伴い、Node上のSecretコピーが削除される（クラスタストアには保存されている）

**--Kubernetes Secretの改善点--**
KubernetesClusterを新規構築した場合、使用できるSecretはあまり安全ではない。改善点は

- クラスタストアに保存ｓㇾているSecretの暗号化
- ネットワーク上で転送中のSecretの暗号化
- Node,Pod,コンテナに表示されたSecretの保護
- 最小権限RBACによるSecretへのAPIアクセス制御
- etcdノード（クラスタストア）へのアクセス制御
- 特権コンテナによるSecretへのアクセス防止
- ソースコードリポジトリを介した情報漏洩防止
- 不要なSecretの安全な削除

※EncreptionConfiguration,Vault,サービスメッシュで検索！！

**--コマンド--**
作成はConfigMapと同様に行う。コマンドで作成する場合

```sh
kubectl create secret generic ${secret_name} --from-literal ${key}=${value} 
```

マニフェストファイルから宣言的に作成する場合は

```sh
kubectl apply -f ${file_path}
```

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: ${secret_name}
type: Opaque
# エンコードされたデータの場合
data:
  ${key1}: ${encoded_value1}
  ${key2}: ${encoded_value2}
# プレーンテキストデータの場合
stringData:
  ${key1}: ${value1}
  ${key2}: ${value2}
```

Secretデータの確認は、`get -o yaml`で行うがBase64エンコードされているのでデコードが必要（base64ユーティリティのインストールが必要）

```sh
kubectl get secret ${secret_name} -n ${namespace} -o yaml
echo ${decoded_value} | base64 -d
```

**--PodでのSecret使用--**
ConfigMapと同様に環境変数・コンテナ起動コマンド・ボリュームの3種類の手法でSecretデータを挿入できる。
ただしマニフェストファイルが少し変わるだけ。

ボリュームで使用する場合のマニフェストは例えば

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ${pod_name}
spec:
  volumes:
  - name: ${volume_name}
    secret:
      secretName: ${secret_name}
  containers:
  - name: ${container_name}
    image: ${image}
    volumeMounts:
    - name: ${volume_name}
      mountPath: ${container_directory_path}
...
```

---

## 13: StatefulSet

### 13-1: StatefulSet理論

**--StatefulSetとは--**
**StatefulSetとは、Kubernetes上でステートフルアプリケーションを管理するためのリソースであり、Podに自己修復・スケーリング・ロールアウトの機能を追加する。**Deploymentのちょっと違うバージョン。
ステートフルアプリケーションとは、データベース・キーバリューストアなどの、貴重なデータを作成・保存するアプリケーションのことである。

StatefulSetはDeploymentに以下の機能を追加したものと考えると覚えやすい。

- 予測可能で永続的なPod名とDNS名の付与
- 予測可能で永続的なボリュームバインディング
- 予測可能な起動およびシャットダウン順序

StatefulSetのマニフェストの例は

```yaml
apiVersion: apps.v1
kind: StatefulSet
metadata:
  name: ${statefulset_name}
spec:
  selector:
    matchLabels:
      ${labels}
  serviceName: ${headless_service_name}
  replicas: ${replica_number}
  template:
    metadata:
      labels:
        ${labels}
    spec:
      containers:
      - name: ${pod_container_name}
        image: ${image}
        ...
```

**--StatefulSet Podの命名（予測可能で永続的なPod名とDNS名の付与）--**
**StatefulSet Pod名は `${statefulset_name}-${index_from_0}` で表される。**

**--Podの作成と削除（予測可能な起動およびシャットダウン順序）--**
StatefulSet Podが作成・削除される際、付与されている番号順に実行される。
具体的には以下のようなフローである。

- Pod作成フロー
  - 番号の最も若いPodが作成される
  - 作成したPodが正常な実行状態になるまで待機
  - 次に番号の若いPodが作成される
  - ...
- Pod削除フロー
  - 番号の最も大きいPodが削除される
  - 削除Podが完全に終了するまで待機
  - 次に番号の大きいPodが削除される
  - ...

**--StatefulSetの削除--**
以下の2点に注意が必要

- StatefulSetを削除してもPodは削除されないので、先にレプリカ数を0にしておく必要がある
- `terminationGracePeriodSeconds`を最低10秒以上に指定してデータへの書き込みをコミットする時間を確保しておく必要がある

**--ボリューム（予測可能で永続的なボリュームバインディング）--**
StatefulSet Podが作成されると、各Podにリンクするボリューム(PV)も自動で作成される。ここで付与されるボリューム名は `${templateで記載したvolume名}-${corresponding_pod_name}` のように完全に予測可能な名前である。
これらのボリュームはPodの障害や削除では削除されず、存続する。この利点として、代替となる新しいPod（削除されたPodと同名のPodが作成される）が作成されたときに、存続しているボリュームに自動でアタッチされる。

![image](../img/book-summary/KubernetesTextbook/13/image.png)

**--Headless service--**
**Headless Serviceとは、ClusterIPアドレスを持たないServiceオブジェクトのこと。**
Headless ServiceはStatefulSetで使用され、各Podに予測可能なDNS名を付与し、Podへのトラフィックの負荷分散を行わないようなServiceである。
具体的には、ラベルセレクターに一致する全Podに対してDNSレコードを作成し、他アプリケーションから直接Podへアクセスできるようにする。

※通常のService経由のPodは個別のDNSレコードが生成されずPodにFQDNは存在しない。ServiceのFQDNは`${service_name}.${namespace}.svc.cluster.local`である。
一方でHeadlessService経由のPodは個別にDNSレコードが生成され、PodのFQDNは`${pod_name}.${service_name}.${namespace}.svc.cluster.local`となる。

StatefulSetでHeadless Serviceを使用するためのマニフェストの例は

```yaml
apiVersion: v1
kind: Service
metadata:
  name: ${service_name}
spec:
  clusterIP: None
  selector:
    ${label}
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: ${statefulset_name}
spec:
  serviceName: ${service_name}
```

### 13-2: StatefulSetの実践

**--デプロイ方法--**
まずはStorageClassをデプロイ

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ${storageclass_name}
provisioner: ${csipluguin}
allVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
reclaimPolicy: Delete
```

次にHeadlessServiceをデプロイ

```yaml
apiVersion: v1
kind: Service
metadata:
  name: ${service_name}
spec:
  ports:
  - port: 80
    name: ${name}
  clusterIP: None
  selector:
    ${label}
```

次にStatefulSetをデプロイ

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: ${statefulset_name}
spec:
  replicas: ${replica_number}
  # statefulsetがどのpodを管理するか指定
  selector:
    matchLabels:
      ${label}
  serviceName: ${service_name}
  # podテンプレート
  template:
    metadata:
      labels:
        ${label}
    spec:
      terminationGracePeriodSeconds: ${seconds}
      containers:
      - name: ${container_name}
        image: ${image}
        ports:
          - containerPort: 80
            name: ${name}
        volumeMounts:
        - name: ${volume_Name}
          mountPath: ${container_directory_path}
  # Pod固有のPVCを作成するための設定
  volumeClaimTemplates:
    metadata:
      name: ${volume_name}
    spec:
      accessModes: [ "${access_mode}" ]
      storageClassName: ${storageclass_name}
      resources:
        requests:
          storage: ${Bytes}
```

これによって作成されるKubernetesリソースの名前は

| resource | name | index |
| - | - | - |
| sc | ${storageclass_name} | none |
| svc | ${service_name} | none |
| sts | ${statefulset_name} | none |
| pod | ${statefulset_name}-${index} | 0,1,,... |
| pvc | ${volume_name}-${statefulset_name}-${index} | 0,1,,... |
| pv | pvc-${random} | none |

**--スケーリング--**
上記のようなStatefulsetPodのレプリカ数を増やした場合（スケールアップ）、新しいPod・PVC・PVが作成される。
一方でレプリカ数を減らした場合（スケールダウン）、indexの大きい順にPodのみ削除される。再度スケールアップするとPodのみ作成され、残存しているPVCに自動で接続される。

※残存して接続されていないPVCを`kubectl get`しても、STATUSカラムは`Bound`表記のままであることに注意。Podに接続しているかどうかを判断するには

```sh
kubectl describe pvc ${pvc_name} -n ${namespace} | grep Used
```

でPod名が指定されているかどうかを確かめる必要がある。

スケーリング（のみ）において、StatefulSetPodの起動・停止方法を設定できる`spec.podManagementPolicy`プロパティが存在する。

- OrderdReady：前のPodの操作が完了してから1つずつPodを起動・停止する。デフォルト設定。
- Parallel：同時に複数のPodを起動・停止する。

---

## 14: APIセキュリティとRBAC

---

## 15: Kubernetes API

---

## 16: Kubernetesの脅威モデル化

---

## 17: 実際のKubernetesセキュリティ

---

## メモ

### 豆知識

- K8sはGoogleが開発した。今はCloud Native Computing Foundation が所有している。
- 命名の由来はギリシャ語の「舵取り」
- 「K8s」の呼び方は「ケイツ」
- K8sは「Apache 2.0」ライセンスに基づくオープンソース

### 単語

**--Inclusive Naming Initiative(INI)--**
2020年にIBMやRed Hat、VMware、Cisco、Linux Foundationなどの主要IT企業が結成した団体？で

```md
ソフトウェア業界では"ブラックリスト"や"スレーブ"など、感情的／歴史的に重い負荷をもたらす単語を使ってきた。
そうしたメタファや口語表現を使わなくとも、明確に技術的な意味を伝えられるフレーズはあるし、それを選んでいきたい。
問題のある用語を廃する、そしてより明快な用語でのコミュニケーションを行う、
とくに第2言語を使って働く人々にとって明確に意味が伝わるようにする。
```

という趣旨のもと活動している。ざっくり言えば、**master（奴隷）などのコンプラ違反っぽい単語は使わずに、誰も嫌な気持ちにならない単語を使うようにしましょう**。という活動をしている。

例えば

- GitHubのデフォルトブランチが`master`->`main`
- Kubernetesの`masterNode`->`controllPlaneNode`

のように名前が変わったりする。

[参考サイト1](https://gihyo.jp/admin/clip/01/linux_dt/202011/24)
[参考サイト2](https://flagship.cc/blogs/columns/what-is-inclusive-naming)
[公式サイト](https://inclusivenaming.org/)
[GitHub](https://github.com/inclusivenaming)

**--Open Container Initiative(OCI)--**
a

**--etcd（分散データベース）--**
a

**--スプリットブレイン状態--**
スプリットブレインとはノード間通信が途絶えた際などに、複数のノードが同時に稼働状態になることで、これによりディスクへの同時書き込みやIPアドレスの競合などが起こる。
このときに過半数かどうかの確信が持てない状態（スプリットブレイン状態）を避けるべき。
なぜなら、スプリットブレイン状態になるとetcdが読み取り専用モードになり、Kubernetesがアプリケーションの更新やスケーリングができなくなるから。

ノード（やetcd）を奇数にしておくと、このような場合でも過半数の状態が存在するので、スプリットブレイン状態にならない。

**--ラウンドロビン方式の負荷分散--**
あ

**--W3C--**
あ

**--DNS A レコード--**
一般的なDNSレコード。ドメインとIPアドレスを登録。

**--SRV レコード--**
サービスレコード。サービス名・ホスト名・ポート番号などを登録。

**--デフォルトゲートウェイ--**
ネットワークトラフィックの送信先がわからない場合にデフォルトで送信する場所。「自分じゃどこに送ればいいかわからないから上司に代わりに送ってもらおう」的な。
