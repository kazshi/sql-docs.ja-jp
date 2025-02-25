---
title: azdata bdc config リファレンス
titleSuffix: SQL Server big data clusters
description: azdata bdc コマンドの参照記事。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 08/21/2019
ms.topic: reference
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 73db63c485b7a6cd2e9355be935ff574ef827a5d
ms.sourcegitcommit: 5e838bdf705136f34d4d8b622740b0e643cb8d96
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69653517"
---
# <a name="azdata-bdc-config"></a>azdata bdc config

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx.md)]

次の記事では、**azdata** ツールでの **bdc config** コマンドのリファレンスを提供します。 他の **azdata** コマンドの詳細については、[azdata リファレンス](reference-azdata.md)に関するページを参照してください。

## <a name="commands"></a>コマンド
|     |     |
| --- | --- |
[azdata bdc config init](#azdata-bdc-config-init) | クラスター作成で使用できるビッグ データ クラスター構成プロファイルを初期化します。
[azdata bdc config list](#azdata-bdc-config-list) | 使用可能な構成プロファイルの選択肢を一覧表示します。
[azdata bdc config show](#azdata-bdc-config-show) | BDC の現在の構成、または指定したローカル ファイル (custom/cluster.json など) の構成を表示します。
[azdata bdc config add](#azdata-bdc-config-add) | 構成ファイル内の json パスの値を追加します。
[azdata bdc config remove](#azdata-bdc-config-remove) | 構成ファイル内の json パスの値を削除します。
[azdata bdc config replace](#azdata-bdc-config-replace) | 構成ファイル内の json パスの値を置き換えます。
[azdata bdc config patch](#azdata-bdc-config-patch) | json 修正プログラム ファイルに基づいて、構成ファイルに修正プログラムを適用します。
## <a name="azdata-bdc-config-init"></a>azdata bdc config init
クラスター作成で使用できるビッグ データ クラスター構成プロファイルを初期化します。 構成プロファイルの特定のソースを 3 とおりの引数で指定できます。
```bash
azdata bdc config init [--target -t] 
                       [--source -s]  
                       [--force -f]  
                       [--accept-eula -a]
```
### <a name="examples"></a>使用例
ガイド付きの BDC 構成の初期化エクスペリエンス - 必要な値の入力を求めるプロンプトが表示されます。
```bash
azdata bdc config init
```
引数を指定した BDC 構成の初期化では、./custom 内に aks-dev-test の構成プロファイルが作成されます。
```bash
azdata bdc config init --source aks-dev-test --target custom
```
### <a name="optional-parameters"></a>省略可能なパラメーター
#### `--target -t`
構成プロファイルが配置される場所のファイル パス。custom-config.json での既定値は cwd です。
#### `--source -s`
Config profile source: ['aks-dev-test', 'kubeadm-dev-test', 'minikube-dev-test']
#### `--force -f`
ターゲット ファイルを強制的に上書きします。
#### `--accept-eula -a`
ライセンス条項に同意しますか? [yes/no]. ([はい/いいえ]。) この引数を使用しない場合は、環境変数 ACCEPT_EULA を 'yes' に設定できます。 
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org/]) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 完全なデバッグ ログには --debug を使用します。
## <a name="azdata-bdc-config-list"></a>azdata bdc config list
`bdc config init` で使用可能な構成プロファイルの選択肢を一覧表示します。
```bash
azdata bdc config list [--config-profile -c] 
                       [--type -t]  
                       [--accept-eula -a]
```
### <a name="examples"></a>使用例
使用可能なすべての構成プロファイル名を表示します。
```bash
azdata bdc config list
```
特定の構成プロファイルの json を表示します。
```bash
azdata bdc config list --config-profile aks-dev-test
```
### <a name="optional-parameters"></a>省略可能なパラメーター
#### `--config-profile -c`
Default config profile: ['aks-dev-test', 'kubeadm-dev-test', 'minikube-dev-test']
#### `--type -t`
表示する構成の種類。
`cluster`
#### `--accept-eula -a`
ライセンス条項に同意しますか? [yes/no]. ([はい/いいえ]。) この引数を使用しない場合は、環境変数 ACCEPT_EULA を 'yes' に設定できます。 
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org/]) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 完全なデバッグ ログには --debug を使用します。
## <a name="azdata-bdc-config-show"></a>azdata bdc config show
BDC の現在の構成、または指定したローカル ファイル (custom/cluster.json など) の構成を表示します。 このコマンドでは、セクションのみを取得したい場合、json パスを取得することもできます。  また、出力先のターゲット ファイルを指定することもできます。  ターゲット ファイルが指定されていない場合は、ターミナルにのみ出力されます。
```bash
azdata bdc config show [--config-file -c] 
                       [--target -t]  
                       [--json-path -j]  
                       [--force -f]
```
### <a name="examples"></a>使用例
BDC 構成をコンソールに表示します
```bash
azdata bdc config show
```
ローカル構成ファイルで、単純な json キー パスの最後にある値を取得します。
```bash
azdata bdc config show --config-file custom-config/cluster.json --json-path 'metadata.name' --target section.json
```
ローカル構成ファイルで、条件文を含む json キー パスの最後にある値を取得します
```bash
azdata bdc config show --config-file custom-config/cluster.json  --json-path '$.spec.pools[?(@.spec.type=="Storage")].spec' --target section.json
```
### <a name="optional-parameters"></a>省略可能なパラメーター
#### `--config-file -c`
現在ログインしているクラスターの構成が必要ない場合は、ビッグ データ クラスター構成ファイルのパス (custom/cluster.json など)。
#### `--target -t`
結果を格納する出力ファイル。 既定値: stdout に転送されます。
#### `--json-path -j`
構成から取得したいセクションまたは値への json キー パス (key1.key2.key3 など)。 jsonpath クエリ言語 https://jsonpath.com/ (-j '$.spec.pools[?(@.spec.type == "Master")]..endpoints' など) を使用します。
#### `--force -f`
ターゲット ファイルを強制的に上書きします。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org/]) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 完全なデバッグ ログには --debug を使用します。
## <a name="azdata-bdc-config-add"></a>azdata bdc config add
構成ファイル内の json パスの値を追加します。  以下の例はすべて Bash で指定されています。  別のコマンド ラインを使用している場合は、引用符の適切なエスケープが必要になる場合があることに注意してください。  あるいは、修正プログラム ファイルの機能を使用することもできます。
```bash
azdata bdc config add --config-file -c 
                      --json-values -j
```
### <a name="examples"></a>使用例
例 1 - コントロール プレーン ストレージを追加します。
```bash
azdata bdc config add --config-file custom/control.json --json-values 'spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}'
```
### <a name="required-parameters"></a>必要なパラメーター
#### `--config-file -c`
設定する構成のビッグ データ クラスター構成ファイルのパス (custom/cluster.json など)。
#### `--json-values -j`
値への json パスのキー値ペア リスト: key1.subkey1=value1,key2.subkey2=value2。 次のようなインライン json 値を指定できます。key='{"kind":"cluster","name":"test-cluster"}' or provide a file path, such as key=./values.json。 追加では条件文はサポートされません。  パスの例については、 http://jsonpatch.com/ を参照してください。  配列にアクセスする場合は、インデックス (key.0=value など) を指定する必要があります。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org/]) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 完全なデバッグ ログには --debug を使用します。
## <a name="azdata-bdc-config-remove"></a>azdata bdc config remove
構成ファイル内の json パスの値を削除します。  以下の例はすべて Bash で指定されています。  別のコマンド ラインを使用している場合は、引用符の適切なエスケープが必要になる場合があることに注意してください。  あるいは、修正プログラム ファイルの機能を使用することもできます。
```bash
azdata bdc config remove --config-file -c 
                         --json-path -j
```
### <a name="examples"></a>使用例
例 1 - コントロール プレーン ストレージを削除します。
```bash
azdata bdc config remove --config-file custom/control.json --json-path '.spec.storage'
```
### <a name="required-parameters"></a>必要なパラメーター
#### `--config-file -c`
設定する構成のビッグ データ クラスター構成ファイルのパス (custom/cluster.json など)。
#### `--json-path -j`
どの値を削除するかを示す jsonpatch ライブラリに基づいた json パスのリスト (key1.subkey1,key2.subkey2 など)。 削除では条件文はサポートされません。 パスの例については、 http://jsonpatch.com/ を参照してください。  配列にアクセスする場合は、インデックス (key.0=value など) を指定する必要があります。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org/]) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 完全なデバッグ ログには --debug を使用します。
## <a name="azdata-bdc-config-replace"></a>azdata bdc config replace
構成ファイル内の json パスの値を置き換えます。  以下の例はすべて Bash で指定されています。  別のコマンド ラインを使用している場合は、引用符の適切なエスケープが必要になる場合があることに注意してください。  あるいは、修正プログラム ファイルの機能を使用することもできます。
```bash
azdata bdc config replace --config-file -c 
                          --json-values -j
```
### <a name="examples"></a>使用例
例 1 - 1 つのエンドポイント (コントローラー エンドポイント) のポートを置き換えます。
```bash
azdata bdc config replace --config-file custom/control.json --json-values '$.spec.endpoints[?(@.name=="Controller")].port=30080'
```
例 2 - コントロール プレーン ストレージを置き換えます。
```bash
azdata bdc config replace --config-file custom/control.json --json-values 'spec.storage={"accessMode":"ReadWriteOnce","className":"managed-premium","size":"10Gi"}'
```
例 3 - レプリカ (記憶域プール) を含むプール ストレージを置き換えます。
```bash
azdata bdc config replace --config-file custom/cluster.json --json-values '$.spec.pools[?(@.spec.type == "Storage")].spec={"replicas": 2,"storage": {"className": "managed-premium","size": "10Gi","accessMode": "ReadWriteOnce"},"type": "Storage"}'
```
### <a name="required-parameters"></a>必要なパラメーター
#### `--config-file -c`
設定する構成のビッグ データ クラスター構成ファイルのパス (custom/cluster.json など)。
#### `--json-values -j`
値への json パスのキー値ペア リスト: key1.subkey1=value1,key2.subkey2=value2。 次のようなインライン json 値を指定できます。key='{"kind":"cluster","name":"test-cluster"}' or provide a file path, such as key=./values.json。 置き換えでは jsonpath ライブラリによる条件文がサポートされます。  これを使用するには、パスを $ で始めます。 これにより、条件文 (-j $.key1.key2[?(@.key3=='someValue'].key4=value など) を実行できます。 以下の例を参照できます。 追加のヘルプについては、 https://jsonpath.com/ を参照してください。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org/]) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 完全なデバッグ ログには --debug を使用します。
## <a name="azdata-bdc-config-patch"></a>azdata bdc config patch
指定された修正プログラム ファイルに従って、構成ファイルに修正プログラムを適用します。 パスを構成する方法の詳細については、 http://jsonpatch.com/ を参照してください。 置き換え操作では、jsonpath ライブラリ https://jsonpath.com/ に従って、そのパス内で条件文を使用できます。 すべての修正プログラム json ファイルは、対応する操作 (add、replace、remove)、パス、および値で構成される修正プログラムの配列を含む "patch" のキーで始まる必要があります。 "remove" 操作には値は必要なく、パスだけが必要です。 以下の例を参照してください。
```bash
azdata bdc config patch --config-file -c 
                        --patch-file -p
```
### <a name="examples"></a>使用例
例 1 - 修正プログラム ファイルを使用して、1 つのエンドポイント (コントローラー エンドポイント) を置き換えます。
```bash
azdata bdc config patch --config-file custom/control.json --patch ./patch.json

    Patch File Example (patch.json): 
        {"patch":[{"op":"replace","path":"$.spec.endpoints[?(@.name=='Controller')].port","value":30080}]}
```
例 2 - 修正プログラム ファイルを使用して、コントロール プレーン ストレージを置き換えます。
```bash
azdata bdc config patch --config-file custom/control.json --patch ./patch.json

    Patch File Example (patch.json): 
        {"patch":[{"op":"replace","path":".spec.storage","value":{"accessMode":"ReadWriteMany","className":"managed-premium","size":"10Gi"}}]}
```
例 3 - 修正プログラム ファイルを使用して、レプリカ (記憶域プール) を含むプール ストレージを置き換えます。
```bash
azdata bdc config patch --config-file custom/cluster.json --patch ./patch.json

    Patch File Example (patch.json): 
        {"patch":[{"op":"replace","path":"$.spec.pools[?(@.spec.type == 'Storage')].spec","value":{"replicas": 2,"storage": {"className": "managed-premium","size": "10Gi","accessMode": "ReadWriteOnce"},"type": "Storage"}}]}
```
### <a name="required-parameters"></a>必要なパラメーター
#### `--config-file -c`
設定する構成のビッグ データ クラスター構成ファイルのパス (custom/cluster.json など)。
#### `--patch-file -p`
jsonpatch ライブラリに基づいた修正プログラム json ファイルへのパス (http://jsonpatch.com/ )。 修正プログラム json ファイルは、実行する修正プログラム適用操作の配列である値を持つ "patch" という名前のキーで始める必要があります。 修正プログラム適用操作のパスの場合は、ドット表記 (ほとんどの操作での key1.key2 など) を使用できます。 置き換え操作を実行するときに、条件文を必要とする配列内の値を置き換えている場合は、パスを $ で始めて jsonpath 表記を使用してください。 これにより、条件文 ($.key1.key2[?(@.key3=='someValue'].key4 など) を実行できます。 以下の例を参照してください。 条件文に関する追加のヘルプについては、 https://jsonpath.com/ を参照してください。
### <a name="global-arguments"></a>グローバル引数
#### `--debug`
すべてのデバッグ ログを表示するようにログの詳細レベルを上げます。
#### `--help -h`
このヘルプ メッセージを表示して終了します。
#### `--output -o`
出力形式。  使用できる値: json、jsonc、table、tsv。  既定値: json。
#### `--query -q`
JMESPath クエリ文字列。 詳細と例については、[http://jmespath.org/](http://jmespath.org/]) を参照してください。
#### `--verbose`
ログの詳細レベルを上げます。 詳細なデバッグ ログを表示するには --debug を使います。

## <a name="next-steps"></a>次の手順

他の **azdata** コマンドの詳細については、[azdata リファレンス](reference-azdata.md)に関するページを参照してください。 **Azdata**ツールをインストールする方法の詳細については、「[管理[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]する azdata をインストール](deploy-install-azdata.md)する」を参照してください。