# 「Docker&Kubernetesのきほんのきほん」のメモ

Dockerは概要と、超簡単な実行ができる。docker-composeからK8sへの移行ができないので、入門書を読んで移行しようという目論見。
なので、知っている体で重要部分だけをまとめる。

## 目次

1. [Dockerとはなんだろう](#1-dockerとはなんだろう)
2. [Dockerが動く仕組み](#2-dockerが動く仕組み)
3. [Dockerを使ってみよう](#3-dockerを使ってみよう)
4. [Dockerにコンテナを入れて動かしてみよう](#4-dockerにコンテナを入れて動かしてみよう)
5. [Dockerに複数のコンテナを入れて動かしてみよう](#5-dockerに複数のコンテナを入れて動かしてみよう)
6. [応用的なコンテナの使い方を身につけよう](#6-応用的なコンテナの使い方を身につけよう)
7. [Docker Composeについて学ぼう](#7-docker-composeについて学ぼう)
8. [Kubernetesについて学ぼう](#8-kubernetesについて学ぼう)

---

## 1: Dockerとはなんだろう

### 1-1: Dockerってなんだろう

**Dockerとは「データやプログラムを隔離できる」仕組み**で、隔離する単位を「コンテナ」と呼ぶ。主にサーバーで使用される。では、、、

**--隔離するメリットは？--**
端的に言えば、「競合を避けるため」。

例えば、2つのシステムA・Bで同じプログラムを使用していて、システムAがプログラムのver.5でのみ正常に作動し、システムBがプログラムのver.6でのみ正常に作動する場合がある。
この時、システムとプログラムをまとめて1つのコンテナとして隔離すれば、システムAとver.5のプログラムのコンテナと、システムBとver.6のプログラムのコンテナの2つのコンテナで、プログラムを競合することなくシステムを動かすことができる。

### 1-2: サーバーとDocker

**--まずはサーバーについて--**
**サーバーとは「何かサービスを提供するもの」で**、開発現場においては

- 機能的な意味（webページを返したり、メールを返したりする）
- 物理的な意味（現物としてのコンピュータ。クラウドの場合は会社にはない）

の2通りで使用される。物理的な1つのサーバーに複数のサーバー機能を同居させることができる。
**サーバーがサーバーとしての機能を果たすためには、専用のソフトをインストールする必要がある**（Apacheをインストールすればwebサーバーになる）。

サーバー例

- webサーバー
  webサイトの機能を提供。Apache,Nginx,IISなど。
- データベースサーバー
  データを保存・検索するデータベースを置くサーバー
- DNSサーバー
  IPアドレスとドメインを結びつけるサーバー
- プロキシサーバー
  通信を中継するサーバー。社内LANなどからインターネット上のサーバーに接続する際に経由する。接続先（インターネット）から身元（ローカルPC）の情報を隠せる。
- 認証サーバー
  ユーザー認証のためのサーバー。OpenLDAPやActiveDirectoryなど。

サーバーとして使用されるOSは基本的にはLinuxである。
なぜなら、画面描画的な機能がサーバーには必要ないから。代表的なOSはUNIX系である。

- UNIX系
  - Linux系
    - Ubuntu,Debian
    - Red Hat,CentOS
  - BSD系
    - macOS
    - FreeBSD
  - Solaris系

**--ではDockerの利点は？--**
「物理的な1つのサーバーに複数のサーバー機能を同居させることができる」が、物理的なサーバーあるいはOSを共有しているので、[1-1](#1-1-dockerってなんだろう)で述べたような「競合」が問題となる可能性がある。
**Dockerを使用すればこの問題を避け、1つの物理的なサーバーに複数のサーバー機能をコンテナとして「隔離」して同居させることができる。**

また、イメージとして保存しておくことで、別の物理的なサーバーに同じ機能を移し替えることができる。（Dockerさえ使えればどんな物理的サーバーでも同じサーバー機能を実行できる）

### メモ-1

- DockerはLinuxOSで使うことを前提としているので、Dockerを使用する際にはなんらかの形でLinuxOSが必要。解説は[2章で](#2-1-dockerが動く仕組み)。
- ApacheはWebサーバー機能を提供するソフトウェア。
- Dockerは仮想化技術（OSが仮想で存在する）ではない。実行環境を隔離するだけで、コンテナ上のOSは「OSっぽいもの」。
- AWSのEC2インスタンスは仮想化環境で、それぞれのインスタンスに完全に独立なOS（マシン）が動作する。

---

## 2: Dockerが動く仕組み

### 2-1: Dockerが動く仕組み

**--まずDockerの構成--**
Dockerを使用する際のサーバーマシン上の構成は以下のようになっている。

- コンテナ内アプリケーション（カスタマイズ）
- コンテナOS（LinuxOSっぽいもの）
- Dockerコンテナ
- Docker Engine
- サーバーマシンOS（LinuxOSのみ）
- サーバーマシン

「なぜコンテナ内のOSはOSっぽいものなのか？」と「なぜサーバーマシンOSはLinuxOSのみ許されるのか？」に答えるために

**--そもそもOSとは？--**
**OSとはソフトウェアの命令（プログラム？）をハードウェアに伝えるのも**であり、核となるカーネル（脳みそ）とディストリビューション（周辺部分）に分けられる。それぞれのおおまかな役割は

- カーネル
  - ハードウェアの操作
  - 計算
- ディストリビューション
  - ソフトウェアとカーネル間の命令・結果の伝達
  - ディスプレイへの表示
  - キーボード入力の受け取り

**--LinuxOSっぽいものとは？Linux上でしか動かないとは？--**
DockerコンテナにあるLinuxOSっぽいものとは、Linuxのディストリビューションのことで、カーネルは入っていない。
コンテナ内のLinuxディストリビューションは、コンテナ内で発行される命令をサーバーマシンOSのカーネルに伝える。つまり、計算自体はサーバーマシンのカーネルが行っている。

このことからサーバーマシンがLinuxでなければ、コンテナ内のディストリビューションとサーバーマシンのカーネルが疎通できなくなり、Dockerコンテナは動かせない。

つまり、「コンテナ内ディストリビューションがLinuxのものを使用」することと「計算自体はサーバーマシンのカーネルを使用」することから、DockerはLinuxOSの上でしか動かせなく、コンテナ内にはLinuxOSっぽいものが入っている。

ちなみにコンテナ内に入れるソフトウェアもLinux用のものでなければならない。
また、コンテナ内にカーネルごと入れないのは、コンテナを「軽く」するため。
また、Windowsやmac上でDockerを使っているときは、LinuxOSをどうにかして強引に入れている。

### 2-2: Docker Hubとイメージとコンテナ

**--イメージ--**
（コンテナ）イメージとはコンテナ作成のための設計図で、1つのイメージから同じコンテナを複数作ることができる。また、別のマシン上に同じコンテナを作成することもできる（コンテナは持ち運びできる、とよく言われるのはこれ）。

ちなみにコンテナからイメージを作ることもできる。つまりコンテナ上でカスタマイズして新しいイメージとすることができる。

**--Docker Hub--**
Docker Hubとは、Dockerが公式的に運用しているDockerレジストリ（Dockerイメージの配布場所）であり、さまざまなイメージを自由にアップロード・ダウンロードできる。
社内サーバーにプライベートなイメージのレジストリを作成することもできる。

公開されているイメージの種類はおおまかに

- OSっぽいもののみ
- OSっぽいもの+ソフトウェア
- OSっぽいもの+複数のソフトウェア

であり、入っているもののバージョンによって別のイメージとなるため、組み合わせを考えれば膨大な数が公開されている。

※誰でもイメージを公開できるという性質上、危険なイメージも公開されているので注意が必要。とりあえず公式が出しているイメージのみ使用するのが吉。

**--コンテナ--**
1つのコンテナには複数のアプリケーションを入れることができる。
ただし一般的なコンテナの構成としては、**1コンテナ=1アプリ**である。
例えばWordPressを構築したければ、MySQLコンテナ・Apacheコンテナ・WordPressコンテナの3つにする。

このようにコンテナを分ける利点は

- セキュリティ面の向上
- 運用（メンテナンス）が用意

※複数アプリの入ったオールインワンコンテナは、ちょっと試しに使いたい時に使用するのが良い。

### 2-3: Dockerコンテナのライフサイクルとデータの保存

**--コンテナの運用--**
コンテナ運用の基本は**作って捨てる**であり、同じコンテナをずっと使い回すことはしない。アップデートがあれば新たなイメージから新たなコンテナを作成する。

このような「作る」「起動する」「停止する」「破棄する」というコンテナの一連の流れを**コンテナのライフサイクル**と呼ぶ。

**--データの保存--**
コンテナを破棄すると、コンテナ内で編集した内容などは消えてしまう。
このようなデータを保存したければ、物理的なマシン上にマウントして保存する。

**マウントとは、繋げて書き込めるようにした状態。**
PCにUSBメモリをつけてデータをそこに保存するのと同じように、コンテナに物理マシンのデータ領域を繋げることでコンテナ外部にデータを保存できる。

### 2-4: Dockerのメリット・デメリット

**--Dockerたる所以とメリット--**
**DockerのDockerたる所以は「隔離できる」こと**で、これによって

- コンテナ同士は独立している
- イメージ化が可能
- コンテナにカーネルが不要でディストリビューションを変更可能

Dockerのメリットは（上であげたものはメリットでもある）

- 1つの物理的なマシンに複数のサーバーを独立した状態で載せられる。
- 独立しているので運用・保守が容易
- サーバーの細かい知識なしでサーバー構築ができる（ほんとかよ？）

**--デメリット--**
Dockerのデメリットは

- Linux用ソフトウェアしか対応していない。（UNIXとかWindowsのOffice製品とか無理）
- 物理マシンの不具合に影響を受ける
- 複数コンテナを使うことが前提

### メモ-2

- Linuxの有名なディストリビューションは「Ubuntu」「CentOS」「Red Hat」「Alpine」「Debian」
- WSL2はWindows Subsystem for Linux 2で、Windows上でLinuxを利用するための仕組み。

---

## 3: Dockerを使ってみよう

### 3-1: Dockerを使うには

Dockerを使うにはLinuxが必要だが、Windowsやmacでも使える方法がある。Dockerを使う方法は3つあり

1. LinuxのマシンでDockerを使う
2. 仮想マシンやレンタル環境にDockerを入れて、他のOSで操作する
3. Windows用やmac用のDockerを使う

以下では最も手軽な3について説明する。

**--Windows用やmac用のDocker--**
いわゆる「Docker Desktop for Windows(Mac)」を利用するのが主流。純粋なDockerだけではなく、Linux OSの実行環境なども入っているパッケージ化されたもの。

このDocker DesktopでDockerを動かす仕組みは、**Windows(Mac)マシン上に仮想のLinux環境を作って、その上でDockerを動かしている**。Dockerを使用する時、ユーザーはLinux OSの存在は意識する必要がない。

マシン上ソフトウェアの描像は（青がDocker Desktopでパッケージ化されているもの）
![imege of Docker Desktop](../img/book-summary/DockerAndKubeIntro/dockerDesktop.png)
WindowsにおいてはWSL2対応版が推奨されている。WSL2とは「Windows Subsystem for Linux 2」の略でWindows上でLinuxのソフトウェアを動かすための仕組み。このLinuxはMicrosoft社が提供するもので、DockerDesktopで入れるLinuxはDocker社が提供するもの。
※この情報は全体的に古いかも

**--Dockerの使用要件--**
Dockerを使用するために、Linux OSが必要という以外に細かい使用条件がある。

- Windows
  特に「64ビット　オペレーティングシステム、x64ベース　プロセッサ」は必要（多分もうこれが主流になっているのでほとんど気にしなくて良い）
  - 64bit版のOS
  - Hyper-VとContainersが有効
  - SLAT機能をサポートした64bitプロセッサ
  - 4GB以上のメモリ
  - BIOSでvirtualizationが有効
- Mac
  - 64bit版のOS
  - 2010年以降のモデル
  - 4GB以上のメモリ
- Linux
  - OSディストリビューションとバージョンが以下を満たす
    - CentOS: 7以降
    - Ubuntu（推奨）: 16.04以降
    - Debian: 9以降
    - Fedora: 30以降
  - Linuxカーネルが3.10以降
  - iptablesが1.4以降
  - Gitが1.7以降
  - XZ Utilsが4.9以降
  - procsとcgroups階層を取る

Linux OSのディストリビューションで**Ubuntuを推奨している理由は、Dockerで操作するコンテナで使用するLinux OSっぽいものと似ているから**。
ディストリビューションは`Red Hat`系（Cent OS, Fedoraなど）と`Debian`系（Debian, Ubuntuなど）の2つに分かれ、コマンドなどの細かい部分が異なる。コンテナ内で採用されるLinuxは`Debian`系のものが多いので、コンテナ内外で混乱しないためにはUbuntuが良い。

### 3-2: Dockerのインストール

本章は知識として蓄えるべきものはほとんどないので、概要だけ記してあとはスキップする。詳細は書籍を参照のこと。

「Docker Desktop for Windows」のインストール手順は

1. Windowsを最新版にする
2. WSL2を有効にする
3. Linuxカーネルをアップデート
4. Dockerをインストール

※WSL2で動いているかどうかはDocker DesktopのGeneralで確認できる

### 3-3: Dockerの操作方法とコマンドプロンプト/ターミナルの起動

Dockerは基本的にCUI(Character User Interface)で操作する。Windowsでは「コマンドプロンプト」で、Macでは「Terminal」。
※Docker DesktopでGUI(Graphical User Interface)操作もできめっちゃ簡単だが、将来的にLinux上のDockerを触ることを考えてCUIに慣れておく必要あり。

**--プロンプトとユーザー--**
プロンプトはコマンドを打ち込む際に左側に表示されるもので、ユーザー情報が載っている。

- Windows
  
  ```prompt
  C:\Users\${username}>
  ```

- Mac
  
  ```terminal
  ${computername}: ~${username} $
  ```

- Linux
  
  ```linux
  [${username}@${hostname}~]#
  [${username}@${hostname}~]$
  ```

その他、知っておくべき事項を箇条書きで記す。

- 実行したコマンドの終了後に次のプロンプトが表示される
- コマンドラインツールを終了してもDockerは終了しない

**--レンタル環境のDocker--**
[3-1](#3-1-dockerを使うには)でDockerを使う方法の1つとして、「レンタル環境にDockerを入れて、他のOSで操作する」という方法を挙げた。

具体的には、「クラウド」や「VPS」などのレンタル環境上にDockerを構築することを指す。
より具体的にはAWSの「EC2」にDockerを構築する。

利点は、自分の持っているPCのスペックに関係なく利用できること。
欠点は、費用がかかること。

### メモ-3

VPSとは「Virtual Private Server」の略で、直訳すると「仮想専用サーバー」。

```markdown
1台の物理サーバーを仮想化技術によって複数の仮想サーバーに分割し、
それぞれを独立したサーバーのように利用できる仕組みです。
ユーザーごとに専用のOS環境が提供され、
root権限（管理者権限）も付与されるため、
ソフトウェアのインストールや設定を自由に行うことができます。
```

[参照: VPSとは？仕組みと活用、選び方をわかりやすく解説](https://www.kagoya.jp/howto/it-glossary/server/basic-02/)

スキップ情報

- 3-1の「デスクトップ版を使う条件と不安定になるケース」はスキップ
- 3-1の「デスクトップ版とToolbox版の違い」はスキップ
- 3-3で必要ない部分は適宜スキップ

---

## 4: Dockerにコンテナを入れて動かしてみよう

#### 4-1: Docker Engineの起動と終了

インストールしたDocker Engineは基本的に24/7で動き続け、dockerコマンドを待機している（PCがシャットダウンされればDocker Engineも終了する）。

このDocker Engineの起動方法と（手動による）停止方法は

| OS | 起動方法 | 停止方法 |
| --- | --- | --- |
| Windows, Mac | Docker Desktopを起動 | `Quit Docker Desktop`を選択 |
| Linux | `sudo systemctl start docker`コマンドを実行 | `sudo systemctl stop docker`コマンドを実行 |

また、PCの起動をトリガーに自動でDocker Engineを起動するようにしておく設定も可能で、WindowsとMacではDocker Desktopの

```markdown
設定 -> General -> Start Docker Desktop when you log in
```

で切り替え可能。Linuxでは

```ubuntu
sudo systemctl enable docker
```

のコマンドで設定可能。

※**Docker Engineは自動起動の設定が可能だが、コンテナは自動起動するような設定はできない**ので、コンテナ自動起動のためにはスクリプトなどを用意する必要がある。

#### 4-2: コンテナ操作の基本（主要コマンド一覧）

ここではコンテナ操作を行うための主要コマンドを見ていく。
「Dockerコマンドの構造」を解説したのちに、「主要コマンド」を列挙する。

**Dockerコマンドの構造**
Dockerコマンドの構造は以下の通りである

```docker
# 概念系
docker コマンド　(オプション) 対象 (引数)
# 詳細
docker ${top-level solo commands} ${Sub-command} (${options}) ${対象} (${arguments})
# Ex
docker container run -d ${containername}
```

- コマンド
  コマンドは上位コマンド(`top-level solo commands`)と副コマンド(`Sub-command`)に分かれる。**実用上は上位コマンドを除いた省略形のコマンド（これは昔のコマンド）を用いることが多いが、今後この省略コマンドが廃止されるかのうせいがあるため、上位コマンドが存在することは知っておくべき**。
- オプション
  オプションは必ずしも必要ではなく、`-`か`--`がついたオプションを以下のように指定する。

  ```docker
  # パターン1: -
  -d
  # パターン2: --
  --all
  # パターン3: オプションに値を指定する
  --name ${name}
  # パターン4: -と1文字のオプションをまとめる
  -dit
  ```

- 対象
  対象にはコマンドを実行したい対象の名前やIDを指定する。
  例えば、コンテナ名・イメージ名・ボリューム名など
- 引数
  引数は必ずしも必要ではなく、`-`か`--`から始まることが多い。
  文字コードの指定やポート番号の指定に用いる。

**主要コマンド**
主要コマンドを上位コマンドごとに分けて列挙する。オプションについては説明しないが、以降のハンズオンで触れる。

- コンテナ操作系
  
  ```docker
  docker container ${Sub-command} ...
  ```

  | 副コマンド | 内容 | 省略 | 主なオプション |
  | --- | --- | --- | --- |
  | start | 停止しているコンテナを起動 | OK | -i |
  | stop | コンテナを停止 | OK | . |
  | create | イメージからコンテナを作成 | OK | --name -e -p -v |
  | run | （必要なら）イメージをダウンロードし、コンテナ作成と起動を行う | OK | --name -e -p -v -d -i -t |
  | rm | 停止したコンテナ削除 | OK | -f -v |
  | exec | 実行中のコンテナ内でプログラムを実行 | OK | -i -t |
  | ls | コンテナ一覧を表示 | OK(`ps`) | -a |
  | cp | コンテナとホスト間でファイルをコピー | OK | . |
  | commit | コンテナをイメージに変換 | OK | . |

- イメージ操作系
  
  ```docker
  docker image ${Sub-command} ...
  ```

  | 副コマンド | 内容 | 省略 | 主なオプション |
  | --- | --- | --- | --- |
  | pull | Docker Hubなどのリポジトリからイメージをダウンロード | OK | . |
  | rm | イメージを削除 | OK(`rmi`) | . |
  | ls | ダウンロードしたイメージ一覧を表示 | NG? `images`で見れる | . |
  | build | イメージを作成 | OK | -t |

- ボリューム操作系
  ボリュームとは、コンテナからマウントできるストレージのこと。

  ```docker
  docker volume ${Sub-command} ...
  ```

  | 副コマンド | 内容 | 省略 | 主なオプション |
  | --- | --- | --- | --- |
  | create | ボリュームを作成 | NG | --name |
  | inspect | ボリュームの詳細情報を表示 | NG | . |
  | ls | ボリュームの一覧を表示 | NG | -a |
  | prune | 現在マウントされていないボリュームを全て削除 | NG | . |
  | rm | 指定したボリュームを削除 | NG | . |

- ネットワーク操作系
  Dockerネットワークとは、Docker同士が接続するのに使う仮想的なネットワークのこと。

  ```docker
  docker network ${Sub-command} ...
  ```

  | 副コマンド | 内容 | 省略 | 主なオプション |
  | --- | --- | --- | --- |
  | connect | コンテナをネットワークに接続 | NG | . |
  | disconnect | コンテナをネットワークから切断 | NG | . |
  | create | ネットワークを作る | NG | . |
  | inspect | ネットワークの詳細情報を見る | NG | . |
  | ls | ネットワークの一覧を表示 | NG | . |
  | prune | 現在コンテナがつながっていないネットワークを全て削除 | NG | . |
  | rm | 指定したネットワークを削除 | NG | . |

このように主要なコマンドは`container`, `image`, `volume`, `network`の4つの上位コマンドに分類される。これ以外の上位コマンドは

| 上位コマンド | 内容 |
| --- | --- |
| checkpoint | 現状を一時的に保存し、後でその時点に戻すことができる |
| node | Docker Swarmのノード管理 |
| plugin | プラグイン管理 |
| secret | Docker Swarmのシークレット情報管理 |
| service | Docker Swarmのサービス管理 |
| stack | Docker SwarmやKubernetesでサービスをひとまとめにしたスタックを管理 |
| swarm | Docker Swarmの管理 |
| system | Docker Engineの情報取得 |

などがあるが、まだ使う機会は少ない。Docker SwarmとはKubernetesとは異なるオーケストレーション機能のこと。

例外となる単独コマンド（上位コマンドと副コマンドに分かれないもの）がいくつか存在する。

| 単独コマンド | 内容 | 主なオプション |
| --- | --- | --- |
| login | Dockerレジストリにログイン | -u -p |
| logout | Dockerレジストリからログアウト | . |
| search | Dockerレジストリで検索 | . |
| version | Docker Engineやコマンドのバージョンを表示 | . |

#### 4-3: コンテナの作成・削除と起動・停止

コンテナのライフサイクルは「作成」「起動」「停止」「破棄」である。
このライフサイクルを達成するためのコマンドを詳細に見ていく。
※コンテナを削除するには、事前に停止している必要がある。

**--docker run--**
`docker run`コマンドは`docker image pull`, `docker create`, `docker start`がひとまとめになったコマンドで、以下のようにコンテナを作成するためのイメージを対象に指定する。

```docker
docker run ${image}
docker container run ${image}
```

指定する主なオプションは

| オプション | 内容 |
| --- | --- |
| `--name ${コンテナ名}` | 作成するコンテナ名を指定 |
| `-p ${ホストのポート番号}:${コンテナのポート番号}`, `-publish ${ホストのポート番号}:${コンテナのポート番号}` | ポート番号を指定 |
| `-v ${ホストのディスク}:${コンテナのディレクトリ}`, `-volume ${ホストのディスク}:${コンテナのディレクトリ}` | ボリュームをマウント |
| `--net=${ネットワーク名}` | コンテナをネットワークに接続 |
| `-e ${環境変数名}=${値}`, `--env ${環境変数名}=${値}` | 環境変数を指定 |
| `-d`, `--detach` | デタッチモード（バックグラウンド）でコンテナを実行 |
| `-i`, `--interactive` | コンテナに操作端末（キーボード）を繋ぐ |
| `-t`, `--tty` | 特殊キーを使用可能にする |
| `-help` | 使い方表示 |

`-d`で指定する**バックグラウンド実行とは、起動したコンテナの実行完了を待たずにコマンドラインでコマンド実行が可能になるオプション**であり、一連の処理で実行完了するようなコンテナではこれを指定しないが、「デーモン」ソフトウェアとしてのコンテナは`-dit`を指定することが多い。
**デーモンとはUNIXやLinux上で動くプログラムの内、常に待ち受けして裏で動き続けるプログラム**のことである（mailer deamonなど）。
`-dit`によってデーモンソフトウェア実行と並行してコンテナ内に入って、キーボードでコマンド実行が可能になる。

**--docker stop--**
`docker stop`コマンドはコンテナを停止するコマンドで、以下のように停止するコンテナを指定する。

```docker
docker stop ${コンテナ名}
```

**--docker rm--**
`docker rm`コマンドはコンテナを削除するコマンドで、以下のように削除するコンテナを指定する。

```docker
docker rm ${コンテナ名}
```

**--docker ps--**
`docker ps`コマンドは動いているコンテナ一覧を表示するコマンドで、`-a`オプションをつけると停止しているコンテナも含めて存在するコンテナ全ての一覧を表示する。

#### 4-4: コンテナと通信

インターネットなどの外界からコンテナに接続するためにはポート（通信の出入り口）を設定する。

```docker
docker run -p ${host port}:${container port} ${image}
```

によって設定できる。
これは、**コンテナの指定ポートとホストマシンの指定ポートを繋いでいて、外界からホストマシンの指定ポートにアクセスすれば、コンテナの指定ポートに自動でアクセスを中継してくれる**。

**--Apacheのポート接続--**
httpdイメージから作成されるApacheコンテナでは、デフォルトで**80番ポートでアクセスを待機**している。
よってwebサーバーであるこのコンテナにアクセスするためには

```docker
docker run -d -p 8080:80 httpd
```

のように、ホストマシンの8080番ポート（予約ポートでなければ任意）とApacheコンテナの80番ポート（これは固定）を繋いで

```url
http://localhost:8080
```

をブラウザで検索すれば良い。

#### 4-5: コンテナ作成に慣れよう

有名なイメージ（コンテナ）を種類別に列挙する。

**--Linux OS--**
コンテナ内に入ってコマンドを実行する前提で作られているので

- `-it`オプションでキーボード操作を可能に
- `/bin/bash`などでシェルコマンドを指定

する必要がある。例えば

```docker
# 作成 & 起動 & コンテナ内に入る
docker run -it ubuntu bash

# コンテナから脱出
exit

# コンテナ内に入る（ここは奥が深いので盲信NG）
docker exec -it ${container name} bash
```

有名なLinux OSのコンテナは

| イメージ | コンテナ内容 |
| --- | --- |
| ubuntu | Ubuntu |
| centos | CentOS |
| debian | DebianOS |
| fedora | Fedora |
| busybox | BizyBox |
| alpine | Alpine linux |

**--Webサーバー--**
webサーバーでは外部からアクセスする必要があるので

- `-p`でポートを接続
- `-d`でバックグラウンド実行

を指定するのがほとんど。

| イメージ | コンテナ内容 |
| --- | --- |
| httpd | Apache |
| nginx | Nginx |

**--Databaseサーバー--**
Databaseサーバーではパスワードを設定する必要があるので、`-e ${password name}=${password}`でrootパスワードを指定すべき。これでも最小設定（ほかに設定すべき項目がある）。

| イメージ | コンテナ内容 | password名 |
| --- | --- | --- |
| mysql | MySQL | MYSQL_ROOT_PASSWORD |
| postgres | PostgreSQL | POSTGRES_PASSWORD |
| mariadb | MariaDB | MYSQL_ROOT_PASSWORD |

**--プログラム言語実行環境--**
プログラム実行環境もコンテナで作成することができる。

| イメージ | コンテナ内容 | 実行によく使われるオプションと引数 |
| --- | --- | --- |
| openjdk | Javaの実行環境 | `-d`を指定せず、引数にJavaコマンドなどを指定してツールを実行 |
| python | Pythonの実行環境 | `-d`を指定せず、引数にPythonコマンドなどを指定してツールを実行 |
| php | PHPの実行環境 | Webサーバー入りのものとコマンド入りのものなど、タグで分類分けされている |
| ruby | Rubyの実行環境 | Webサーバー入りのものとコマンド入りのものなど、タグで分類分けされている |
| perl | Perlの実行環境 | `-d`を指定せず、引数にPerlコマンドなどを指定してツールを実行 |
| gcc | C/C++コンパイラ | `-d`を指定せず、引数にgccコマンドなどを指定してツールを実行 |
| node | Node.js | `-d`を指定せず、引数にappコマンドなどを指定してツールを実行 |
| registry | Dockerレジストリ | `-d`と`-p` |
| wordpress | WordPress | `-d`と`-p`。MySQLかMariaDBが必要。接続パスワードは`-e`で指定。 |
| nextcloud | NextCloud | `-d`と`-p` |
| redmine | Redmine | `-d`と`-p`。MySQLかMariaDBが必要。 |

#### 4-6: イメージの削除

コンテナを削除してもイメージは削除されないので、意図的にイメージを削除しなければPCにイメージが溜まり続け、ストレージを圧迫する。

イメージ一覧表示は

```docker
docker image ls
docker images
```

で行い、削除するには`イメージID`を指定するか`イメージ名:TAG`で指定する

```docker
docker image rm ${image}
docker rmi ${image}
# 複数イメージの削除は、スペース区切りでイメージを指定すれば良い
```

#### メモ-4

- Apacheのコンテナを作るには`httpd`というイメージから作成する。

  ```docker
  docker run --name apa00ex1 -d httpd
  ```

- `stop`, `rm`などの既にあるコンテナを指定するコマンドでは、「コンテナID」や「コンテナIDの省略形（一意に指定できる頭文字数桁）」を指定しても実行できる
- ホストPCのポート番号が重複した時のエラーは
  
   ```error
   Error response from daemon: driver failed programming external connectivity on endpoint...
   : Bind for 0.0.0.0:8080 failed: port is already allocated.
   ```

---

## 5: Dockerに複数のコンテナを入れて動かしてみよう

#### 5-1: WordPressの構築と導入の流れ

**--WordPress--**
WordPressとは、Webサイトを作成できるソフトウェアで、WordPressを動かすには

- WordPressのプログラム
- Apache
- PHP実行環境
- database（MySQLかMariaDB）

が必要。
**WordPressの公式イメージで作成されるコンテナは、「WordPressプログラム」「Apache」「PHP実行環境」がまとめて入っている**ため、追加でDBを用意すれば良い。

**--Dockerネットワーク--**
デフォルトでは2つのコンテナは独立していて、コンテナ間の通信は不可能である。
Dockerネットワークにこの2つのコンテナを所属させることで、コンテナ間通信が可能になる。

Dockerネットワークのコマンドは

```docker
# Dockerネットワークの作成
docker network create ${network name}
# Dockerネットワークの削除
docker network rm ${network name}
```

**--WordPress実行コンテナ群--**
WordPress実行のコンテナ群を作成するための手順は以下である。

1. Dockerネットワークの作成
2. DBコンテナの起動

   ```docker
   docker run --name ${コンテナ名} -dit --net=${ネットワーク名} 
   -e MYSQL_ROOT_PASSWORD=${MySQLのルートパスワード} 
   -e MYSQL_DATABASE=${データベース（領域）名} 
   -e MYSQL_USER=${MySQLの一般ユーザー名} 
   -e MYSQL_PASSWORD=${MySQL一般ユーザーのパスワード}
   mysql --character-set-server=${文字コード} --collation-server=${照合順序}
   ```

3. WordPressコンテナの起動

   ```docker
   docker run --name ${コンテナ名} -dit --net=${ネットワーク} 
   -p ${ホストのポート}:80 
   -e WORDPRESS_DB_HOST=${DBコンテナ名} 
   -e WORDPRESS_DB_NAME=${データベース（領域）名} 
   -e WORDPRESS_DB_USER=${DBの一般ユーザー名} 
   -e WORDPRESS_DB_PASSWORD=${DB一般ユーザーのパスワード} 
   wordpress
   ```

※MySQLコンテナで指定した引数`--character-set-server`と`--collation-server`はMySQLのコンテナに固有のオプションであり、`UTF8`で設定したければ

```docker
--character-set-server=utf8mb4
--collation-server=utf8mb4_unicode_ci
```

※`-e`で指定するパスワード関係の環境変数はMySQLとWordPressのコンテナで一致させておく必要がある。

#### 5-2: WordPressコンテナとMySQLコンテナを作成し、動かしてみよう

上の手順を追うだけなのでスキップ。実行したコマンドは

```docker
docker run --name mysql000ex11 -dit --net=wordpress000net1 -e MYSQL_ROOT_PASSWORD=myrootpass -e MYSQL_DATABASE=wordpress000db -e MYSQL_USER=wordpress000kun -e MYSQL_PASSWORD=wkunpass mysql --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
```

```docker
docker run --name wordpress000ex12 -dit --net=wordpress000net1 -p 8085:80 -e WORDPRESS_DB_HOST=mysql000ex11 -e WORDPRESS_DB_NAME=wordpress000db -e WORDPRESS_DB_USER=wordpress000kun -e WORDPRESS_DB_PASSWORD=wkunpass wordpress
```

#### 5-3: コマンド文を書けるようになろう

**--LAMP環境--**
[5-2](#5-2-wordpressコンテナとmysqlコンテナを作成し動かしてみよう)で構築した「Webサーバー」「プログラム実行環境」「DB」「Linux」のような環境をLAMP環境という。ソフトウェアで言えば

- Linux
- DB: MySQL, MariaDB, PostgreSQL
- Webサーバー: Apache, Nginx
- プログラム実行環境: PHPなど

これはWebシステムに多い構成である。

これ以降の練習問題はスキップする。

#### 5-4: RedmineコンテナとMariaDBコンテナを作成し、練習してみよう

**--Redmine--**
Redmineとは、「チケット（誰が何をするのか示したToDo）」を管理するソフトウェア。
利用するには、WordPressと同様にデータベースが必要となる。

Redmineコンテナのデフォルトポート番号は`3000`であり、データベースとの接続のための環境変数は

| 項目 | 環境変数名 |
| --- | --- |
| データベースのコンテナ名 | REDMINE_DB_MYSQL |
| データベース（領域）名 | REDMINE_DB_DATABASE |
| データベースの一般ユーザー名 | REDMINE_DB_USERNAME |
| データベースのパスワード（一般ユーザーパスワード） | REDMINE_DB_PASSWORD |

**--MariaDB--**
MySQL開発者が開発したDBで、コンテナ作成時に指定するオプションはMySQLと似通っているので注意が必要。

| 項目 | 環境変数名 |
| --- | --- |
| rootパスワード | MYSQL_ROOT_PASSWORD |
| データベース（領域）名 | MYSQL_DATABASE |
| データベースの一般ユーザー名 | MYSQL_USER |
| データベースのパスワード | MYSQL_PASSWORD |

#### メモ-5

MariaDBはミカエル・ウィデニウスが開発している。この人は元々MySQLの開発者であるため、MySQLとMariaDBはよく似ている。

---

## 6: 応用的なコンテナの使い方を身につけよう

#### 6-1: 自分に必要な技術を整理しよう

Dockerについて、使うことと作ることは全く違う分野であり、作る方が難しい。
作るレベルで習得すべき職種は

- サーバー（インフラ）エンジニア
- セキュリティエンジニア

以降の章で取り扱う内容は

- コンテナとホストPC間のファイルコピー
- ボリュームのマウント（バインドマウントとボリュームマウント）
- コンテナのイメージ化
- コンテナの改造
- Docker Hubへの登録
- Docker Composeによるコンテナの同時起動、環境の大量作成
- Kubernetes（複数台のサーバーでコンテナを動かす際のオケストレーションツール）

#### 6-2: コンテナとホスト間でファイルをコピーする

コンテナ内のソフトウェアではなく、Dockerコマンドによるコンテナ-ホストPC間でのファイルコピーを見る。

コマンドは

```docker
# 汎用
docker cp ${copy_path_from} ${copy_path_to}

# コンテナ Path
${container_name}:${container_path}
```

である。**ホスト側のパスは、絶対パスかカレントディレクトリからの相対パスを指定する**。

※コンテナ内のパスは、先に調べて知っておく必要あり。
※HTMLファイルなどで検証する場合は`index.html`のような初期表示されるファイル名をきちんと指定する必要あり。

#### 6-3: ボリュームのマウント

**--ボリューム--**
**ボリュームとは、ストレージの1領域を区切ったもの**。

広義と狭義のボリュームがあり、

- 狭義のボリューム：「ボリュームマウント」で使用するDockerEngineの支配下にある記憶領域。
- 広義のボリューム：コンテナに外付けされる記憶領域総称。狭義のボリューム意外には、ディレクトリ、ファイル、メモリなどがある。

**--マウント--**
マウント（直訳は「取り付ける」）とは、対象を接続してOSやソフトウェアの支配下に置くことで、パソコンにUSBを差して外付けの記憶領域を持つことに類似する。
**Dockerの文脈でのマウントは、広義のボリュームをコンテナに外付け（マウント）し、データを永続化すること**。

マウントには、「ボリュームマウント」と「バインドマウント」の2種類が存在する。

- ボリュームマウント：Docker Engineが管理する領域にボリュームを作成して、ディスクとしてコンテナにマウントする。推奨されているマウント方法。
- バインドマウント：Docker Engineの管理外のディレクトリやファイル（大体はホストPCのディレクトリと思えば良い）をコンテナにマウントする。

![mount](../img/book-summary/DockerAndKubeIntro/mount.png)

| 項目 | ボリュームマウント | バインドマウント |
| --- | --- | --- |
| 編集 | Dockerコンテナ経由 | 通常のディレクトリと同様 |
| バックアップ | 複雑 | 通常のディレクトリと同様 |
| 利点 | ホストPCの環境に依存しない。手軽。 | ホストPCからの編集・操作が容易 |
| 用途 | 滅多に触らないが消してはいけないデータ | 頻繁に編集するデータ |

※別のマウントとしてtmpfs（一時メモリ）のマウントもあり、利点は高速な読み書きができることで、欠点はホストの再起動で消滅すること。

**--必要性・用途--**
コンテナのライフサイクルに則ると、比較的短いスパンでコンテナを削除することになる。この時、コンテナ内のデータも一緒にきえてしまう。
ソフトウェアのバージョンアップで毎回コンテナ内のデータが消えたり、データベースコンテナのデータが消えるのはまずいので、**コンテナの削除によって消えないような、永続化されたデータが欲しい**。

**--コマンド--**
ボリュームをマウントするには、先にボリュームを作成しておく必要がある（自動で作られるけど非推奨）。

狭義のボリュームの作成・削除コマンドは

```docker
# 作成
docker volume create ${volume_name}

# 削除
docker volume rm ${volume_name}
```

マウントはコンテナの起動（run）時に`-v`オプションで指定する。

```docker
# ボリュームマウント
docker run ... -v ${volume_name}:${container_path}

# バインドマウント
docker run ... -v ${host_pc_path}:${container_path}
```

マウント先のコンテナ内のパスは、コンテナ内ソフトウェアがコンテンツを保存する場所を指定することが多い。
コンテナのどこにどのようなデータが置かれているかは、コンテナイメージのドキュメントで調べる必要がある。

※Apacheでは`/usr/local/apache2/htdocs`、MySQLでは`/var/lib/mysql`。
※コンテナにマウントしているボリュームは`docker container inspect ${container_name}`の`Mounts`プロパティで確かめることができる。

**--ボリュームのバックアップ--**
バインドマウントするディレクトリはホストPCでコピーをとれば良いだけだが、狭義のボリュームはコンテナ経由でしかボリューム内にアクセスできないので、少し複雑な手順を要する。

具体的には、**ボリュームアクセス専用の一時的なLinuxコンテナを用意して、ボリュームのバックアップ（圧縮ファイル）をホストPCに保存する**。

```docker
# 1. まずはバックアップを取りたいボリュームがマウントしているコンテナを停止
docker stop ${container_name}

# 2. Linuxコンテナに①「バックアップを取りたいボリュームをマウント」
#    ②「バックアップを保存したいディレクトリもバインドマウント」
#.   ③ファイルを圧縮する引数を指定しつつ一時起動
docker run --rm 
  -v ${volume_name}:${Linux_dir1}
  -v ${host_dir}:${Linux_dir2}
  ${Linux_image}
  tar czvf ${Linux_dir2}/${backup_file_name}.tar.gz　-C ${Linux_dir1} .
```

- `--rm`：コンテナを作成してすぐ消す
- `tar czvf ${Linux_dir2}/${backup_file_name}.tar.gz`：ファイル名を指定して新しくgzip形式の圧縮ファイルをマウントしたコンテナ内ディレクトリに作成
  - `c`：createの略で新しいアーカイブを作成するオプション
  - `z`：gzipの略でgzip形式（`.tar.gz`拡張子）で圧縮するオプション
  - `v`：verboseの略で処理中のファイル名を表示するオプション
  - `f`：fileの略で出力ファイル名を指定するオプション
- `-C ${Linux_dir1}`：`tar`コマンドのオプションで作業ディレクトリを`${Linux_dir1}`に設定
- `.`：作業ディレクトリの中の全てを対象とする記述

![volumeBackup](../img/book-summary/DockerAndKubeIntro/volumeBackup.png)

※リストアコマンドは

  ```docker
  docker run --rm
    -v ${volume_name}:${Linux_dir1}
    -v ${host_dir}:${Linux_dir2}
    ${Linux_image}
    tar xzvf ${Linux_dir2}/${backup_file_name}.tar.gz　-C ${Linux_dir1}
  ```

#### 6-4: コンテナのイメージ化

特定のコンテナを複製・量産したい場合は、イメージ化してそのイメージからたくさん作れば良い。コンテナのイメージ化には

- commitによってコンテナからイメージを書き出す方法
- Dockerfileからイメージを作成する方法

の2つがある。

**--commit--**
コンテナを用意して`commit`コマンドを実行することで、現状のコンテナのイメージを書き出す方法。
すでにあるコンテナをイメージ化して複製したい場合に使用する。

使用するコマンドは

```docker
docker commit ${container_name} ${output_image_name}
```

※イメージはそのままではコピーできないので、Dockerレジストリを経由するか`tar`ファイルにして強引にファイル操作できるようにする必要がある。`tar`ファイルにするコマンドは

  ```docker
  docker save -o ${file_name}.tar ${image_name}
  ```

ファイルからイメージにするには`load`コマンドを使用する。

**--Dockerfile--**
コンテナイメージを作成するコマンドファイルであるDockerfileから、`build`コマンドでイメージを作成する方法。

コンテナ内にファイルなどをコピーしたい場合は、基本的にはそのファイル群と並列になる場所にDockerfileを配置する。プロジェクトにDockerfileが存在するのはこれに則っているため。

使用するコマンドは（`:${tag}`の指定は任意）

```docker
docker build -t ${output_image_name}:${tag} ${dockerfile_dir_path}
# -t はイメージ名を指定するためのオプション
```

Dockerfile内の記述に用いるコマンドは

| コマンド | 内容 |
| --- | --- |
| FROM | 元にするイメージを指定 |
| ADD | イメージにファイルやフォルダを追加（ローカルあるいはリモートからコピー） |
| COPY | イメージにファイルやフォルダを追加（ローカルからコピー） |
| RUN | build時にコマンドを実行 |
| CMD | コンテナ起動時に実行するコマンドを指定 |
| ENTRYPOINT | イメージを実行するコマンドを強要 |
| ONBUILD | build完了時の命令を指定 |
| EXPOSE | 通信を想定するポートをイメージ利用者に伝える |
| VOLUME | 永続データが保存される場所をイメージ利用者に伝える |
| ENV | 環境変数を定義 |
| WORKDIR | RUN,CMD,ENTRYPOINT,ADD,COPY時の作業ディレクトリを指定 |
| SHELL | build時のシェルを指定 |
| LABEL | 名前やバージョン番号、製作者情報などを設定 |
| USER | RUN,CMD,ENTRYPOINTで指定されるコマンドの実行ユーザー・グループを設定 |
| ARG | build時の引数を宣言 |
| STOPSIGNAL | stop時にコンテナで実行しているプログラムに対して送信するシグナルを変更 |
| HEALTHCHECK | ヘルスチェック（コンテナの死活監視）の方法をカスタマイズする |

#### 6-5: コンテナの改造

コンテナの改造とは、起動したコンテナに対する追加のカスタマイズのことで

- ファイルのやり取り([6-2](#6-2-コンテナとホスト間でファイルをコピーする),[6-3](#6-3-ボリュームのマウント))
- Linuxコマンドによる命令

の2つが主な手法である。ここでは後者の「Linuxコマンドによる命令」を扱う。

**--Linuxコマンドの叩き方--**
コンテナに対してLinuxコマンドを叩く際に使用するプログラムは`shell`であり、ほとんどのコンテナには`bash`が入っている。このbashに対してコマンドを実行することで、コンテナに対して命令を出すことができる。

bashコマンドを叩くためのコマンドは、dockerコマンドに引数として`/bin/bash`を指定する。bashコマンドを叩き終わったらコンテナから出る必要がある。

```docker
# 起動しているコンテナに、bash命令待機状態で入る
docker exec -it ${containeer_name} /bin/bash

# コンテナから出る
exit

# ↓ これは非推奨
# bashを起動する形でコンテナを作成（Apacheなどの他のソフトウェアは起動しない）
# bash操作後にコンテナを起動する必要あり
docker run ${image} /bin/bash
docker start ${container_name}
```

**--Docker Engineとコンテナの管轄領域--**
コンテナにbashコマンドを実行する必要が出たことで、dockerコマンドとの棲み分けを知っておく必要がある。

- dockerコマンド：Docker Engineに対する命令
  - Docker Engineの起動・停止
  - コンテナの起動・停止
  - コンテナとファイルのやり取りなど
- bash(sh)コマンド：コンテナ内のbash(sh)に対するコマンド
  - コンテナ内ファイル操作
  - ソフトウェアインストール
  - ソフトウェア実行・停止など

※bashがほとんどなのは、Docker公式が「特に理由がなければDebian系をベースにして」と言っているから。

#### 6-6: Docker Hubへの登録

`docker run`時にローカルにないイメージはDocker Hubからダウンロードする。
オリジナルのイメージもDocker Hubに置いたり、プライベートなレジストリを作成することもできる。

**--Dockerレジストリとレポジトリ--**
**Dockerレジストリとはイメージの配布場所**のことで、さまざまなレジストリがこの世に存在する。

- Docker Hub：Docker社公式のレジストリ
- その他：その他、企業や個人の作成したレジストリ

一方でレポジトリはレジストリの中を細かく区切った単位で、基本的にソフトウェア単位で作成される。

**--タグとアップロード--**
プライベートかどうかに関わらず、レジストリにイメージをアップロードする際はタグをつける必要がある。

タグは所謂イメージの正式名称のようなもので

```docker
${registory}/${repository}:${version}

# Docker Hub
${ID}/${repository}:${version}

# Private Registory
${domain}/${repository}:${version}
```

のように指定する。イメージをアップロードするには以下の手順で行う。

```docker
# 1.イメージにタグ名をつけて複製
docker tag ${image_name} ${registory}/${repository}:${version}

# 2.アップロード（アップロード先はタグ名から勝手に判断される）
docker push ${registory}/${repository}:${version}
```

**--レジストリの作成--**
プライベートレジストリを作るには、レジストリ用のコンテナを用いる。ポート番号は`5000`を使用する。

```docker
docker run -d -p 5000:5000 registry
```

Docker Hubをレジストリとして使用するには、メールアドレスを登録すれば無料プランで使用できる。
公開設定(public)にしておけば世界中に配布できる。

#### メモ-6

**--shellの種類--**
shellの種類は

- sh(Bourn Shell)：最古からあるシェル
- bash：最も人気なシェル
- csh：C言語風なスタイルのシェル
- tcsh：cshを改良したシェル

**--コマンドの文法--**
shコマンドは**コンテナに入っているLinuxディストリビューションの種類によって、多少コマンドの文法が異なる**。

dockerコマンドも同様に、Docker Engineのインストール方法によってWindows系・Mac系・Linux(Debian)系・Linux(Red Hat)系など、コマンドの文法が異なる。
dockerコマンドの文法の違いは、例えばディレクトリのパスの書き方の違いが挙げられる。その他、ユーザーが文法の違いを意識する必要がないようにイイ感じにDocker Engineが作られているはず。

---

## 7: Docker Composeについて学ぼう

#### 7-1: Docker Composeとは

#### 7-2: Docker Composeの使い方

#### 7-3: Docker Composeの使い方

#### 7-4: Docker Composeを実行してみる

---

## 8: Kubernetesについて学ぼう

#### 8-1: Kubernetesとは

#### 8-2: マスターノードとワーカーノード

#### 8-3: Kubernetesの構成と用語

#### 8-4: Kubernetesのインストールと使い方

#### 8-5: 定義ファイル（マニフェストファイル）の書き方

#### 8-6: Kubernetesのコマンド

#### 8-7: Kubernetesの操作を練習しよう

---

### 調べもの

- 認証サーバーとは？
- Dockerfileのコマンド
