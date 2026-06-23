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
    - [10-1: シーンを設定する](#10-1-シーンを設定する)
    - [10-2: サービスレジストリ](#10-2-サービスレジストリ)
    - [10-3: サービス検出](#10-3-サービス検出)
    - [10-4: サービス検出とNamespace](#10-4-サービス検出とnamespace)
    - [10-5: サービス検出のトラブルシューティング](#10-5-サービス検出のトラブルシューティング)
  - [11: Kubernetesストレージ](#11-kubernetesストレージ)
    - [11-1: 全体像](#11-1-全体像)
    - [11-2: ストレージプロバイダー](#11-2-ストレージプロバイダー)
    - [11-3: コンテナストレージインターフェース(CSI)](#11-3-コンテナストレージインターフェースcsi)
    - [11-4: Kubernetes永続ボリュームサブシステム](#11-4-kubernetes永続ボリュームサブシステム)
    - [11-5: ストレージクラスによる動的プロビジョニング](#11-5-ストレージクラスによる動的プロビジョニング)
    - [11-6: 実践編](#11-6-実践編)
  - [12: ConfigMapとSecret](#12-configmapとsecret)
  - [13: StatefulSet](#13-statefulset)
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

### 10-1: シーンを設定する

### 10-2: サービスレジストリ

### 10-3: サービス検出

### 10-4: サービス検出とNamespace

### 10-5: サービス検出のトラブルシューティング

---

## 11: Kubernetesストレージ

### 11-1: 全体像

### 11-2: ストレージプロバイダー

### 11-3: コンテナストレージインターフェース(CSI)

### 11-4: Kubernetes永続ボリュームサブシステム

### 11-5: ストレージクラスによる動的プロビジョニング

### 11-6: 実践編

---

## 12: ConfigMapとSecret

---

## 13: StatefulSet

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
