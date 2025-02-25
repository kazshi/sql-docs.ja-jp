---
title: azdata をインストールする
titleSuffix: SQL Server big data clusters
description: のインストールと管理[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)] (プレビュー) 用の azdata ツールをインストールする方法について説明します。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 08/21/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: e95fe15877dd6d22ca575b79f9fb213b6104d3aa
ms.sourcegitcommit: 5e838bdf705136f34d4d8b622740b0e643cb8d96
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69652415"
---
# <a name="install-azdata-to-manage-includebig-data-clusters-2019includesssbigdataclusters-ss-novermd"></a>管理する azdata をインストールする[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx.md)]

この記事では、CTP 3.2 用の **azdata** ツールを Windows または Linux にインストールする方法について説明します。

## <a id="prerequisites"></a> 前提条件

**azdata** は Python で記述されたコマンドライン ユーティリティです。クラスター管理者はこれを使用すると、REST API 経由でビッグ データ クラスターをブートストラップし、管理できるようになります。 最低限必要な Python バージョンは v3.5 です。 また、**azdata** ツールのダウンロードとインストールに使用する `pip` も必要です。 以下の手順は、Windows と Ubuntu の例です。 他のプラットフォームに Python をインストールする方法については、[Python のドキュメント](https://wiki.python.org/moin/BeginnersGuide/Download)を参照してください。
さらに、最新バージョンの *requests* Python パッケージをインストールして更新する必要もあります。
```bash
pip3 install -U requests
```

> [!IMPORTANT]
> 新しいバージョンのビッグ データ クラスターをインストールする場合は、**azdata** をアップグレードして新しいリリースをインストールする "*前に*"、データをバックアップして以前のクラスターを削除する必要があります。 詳細については、[新しいリリースへのアップグレード](deployment-upgrade.md)に関するページを参照してください。

## <a id="windows"></a> Windows azdata のインストール

1. Windows クライアントで、[https://www.python.org/downloads/](https://www.python.org/downloads/) から必要な Python パッケージをダウンロードします。 Python 3.5.3 以降では、Python をインストールするときに pip3 もインストールされます。 

   > [!TIP] 
   > Python3 をインストールするときに、**PATH** に Python を追加することを選択します。 そうしない場合は、後で pip3 が配置されている場所を見つけて、**PATH** に手動で追加することができます。

1. 新しい Windows PowerShell セッションを開き、Python で最新のパスを取得します。

1. 以前のリリースのツールがインストールされている場合 (旧称は **mssqlctl**)、まずそれをアンインストールしてから **azdata** の最新バージョンをインストールすることが重要です。

   CTP 3.1 以前の場合は、次のコマンドを実行します。 コマンド内の `ctp3.1` を、アンインストールする **mssqlctl** のバージョンに置き換えます。 

   ```powershell
   pip3 uninstall -r https://private-repo.microsoft.com/python/ctp3.1/mssqlctl/requirements.txt
   ```

1. 以前のリリースの **azdata** がインストールされている場合は、まずそれをアンインストールしてから最新バージョンをインストールすることが重要です。

   CTP 3.2 以降の場合は、次のコマンドを実行します。 コマンド内の `ctp3.2` を、アンインストールする **azdata** のバージョンに置き換えます。

   ```powershell
   pip3 uninstall -r https://aka.ms/azdata
   ```

1. 次のコマンドを使用して **azdata** をインストールします。

   ```powershell
   pip3 install -r https://aka.ms/azdata
   ```

## <a id="linux"></a> Linux azdata のインストール

Linux では、Python 3.5 をインストールしてから、pip をアップグレードする必要があります。 次の例は、Ubuntu で動作するコマンドを示しています。 その他の Linux プラットフォームについては、[Python のドキュメント](https://wiki.python.org/moin/BeginnersGuide/Download)を参照してください。

1. 必要な Python パッケージをインストールします。

   ```bash
   sudo apt-get update && \
   sudo apt-get install -y python3 && \
   sudo apt-get install -y python3-pip
   ```

1. pip3 をアップグレードする:

   ```bash
   sudo -H pip3 install --upgrade pip
   ```

1. 以前のリリースのツールがインストールされている場合 (旧称は **mssqlctl**)、まずそれをアンインストールしてから **azdata** の最新バージョンをインストールすることが重要です。

   CTP 3.1 以前の場合は、次のコマンドを実行します。 コマンド内の `ctp3.1` を、アンインストールする **mssqlctl** のバージョンに置き換えます。 

   ```powershell
   pip3 uninstall -r https://private-repo.microsoft.com/python/ctp3.1/mssqlctl/requirements.txt
   ```

1. 以前のリリースの **azdata** がインストールされている場合は、まずそれをアンインストールしてから最新バージョンをインストールすることが重要です。

   CTP 3.2 以降の場合は、次のコマンドを実行します。 コマンド内の `ctp3.2` を、アンインストールする **azdata** のバージョンに置き換えます。

   ```powershell
   pip3 uninstall -r https://aka.ms/azdata
   ```

1. 次のコマンドを使用して **azdata** をインストールします。

   ```bash
   pip3 install -r https://aka.ms/azdata --user
   ```

   > [!NOTE]
   > `--user` スイッチを指定すると、azdata が Python ユーザー インストール ディレクトリにインストールされます。 通常、Linux 上ではこれは `~/.local/bin` です。 このディレクトリをパスに追加するか、ユーザー インストール ディレクトリに移動して、そこから `./azdata` を実行します。

## <a name="next-steps"></a>次の手順

ビッグデータクラスターの詳細について[は[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)]](big-data-cluster-overview.md)、「」を参照してください。
