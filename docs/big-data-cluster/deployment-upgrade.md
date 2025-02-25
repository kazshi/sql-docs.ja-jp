---
title: 新しいリリースにアップグレードする
titleSuffix: SQL Server big data clusters
description: 新しいリリースにアップグレード[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ver15.md)] (プレビュー) する方法について説明します。
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: mihaelab
ms.date: 08/21/2019
ms.topic: conceptual
ms.prod: sql
ms.technology: big-data-cluster
ms.openlocfilehash: 867729b7d638960a2dbf2cb5f7544fecf698c94d
ms.sourcegitcommit: 5e838bdf705136f34d4d8b622740b0e643cb8d96
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69652334"
---
# <a name="how-to-upgrade-includebig-data-clusters-2019includesssbigdataclusters-ss-novermd"></a>アップグレードする方法[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)]

[!INCLUDE[tsql-appliesto-ssver15-xxxx-xxxx-xxx](../includes/tsql-appliesto-ssver15-xxxx-xxxx-xxx.md)]

この記事では、SQL Server ビッグ データ クラスターを新しいリリースにアップグレードする方法に関するガイダンスを提供します。 この記事の手順は、具体的にはプレビュー リリース間でアップグレードする方法に適用されます。

## <a name="backup-and-delete-the-old-cluster"></a>以前のクラスターをバックアップして削除する

現時点では、ビッグ データ クラスターを新しいリリースにアップグレードする唯一の方法は、クラスターを手動で削除して再作成することです。 各リリースには、以前のバージョンと互換性のない **azdata** の一意のバージョンがあります。 また、古いクラスターで新しいノードにイメージをダウンロードする必要があった場合は、最新のイメージがクラスター上の古いイメージと互換性がない可能性があります。 最新のリリースにアップグレードするには、次の手順に従います。

1. 以前のクラスターを削除する前に、SQL Server マスター インスタンス上と HDFS 上のデータをバックアップします。 SQL Server マスター インスタンスの場合は、[SQL Server のバックアップと復元](data-ingestion-restore-database.md)を使用できます。 HDFS の場合は、[**curl** を使用してデータをコピーできます](data-ingestion-curl.md)。

1. `azdata delete cluster` コマンドを使用して、古いクラスターを削除します。

   ```bash
    azdata bdc delete --name <old-cluster-name>
   ```

   > [!Important]
   > お使いのクラスターに一致する **azdata** のバージョンを使用します。 **azdata** の新しいバージョンがある古いクラスターは削除しないでください。

1. CTP 3.2 より前では、**azdata** は **mssqlctl** と呼ばれていました。 以前のリリースの **mssqlctl** または **azdata** がインストールされている場合は、**azdata** の最新バージョンをインストールする前に、まずこれらをアンインストールすることが重要です。

   CTP 2.3 以降の場合は、次のコマンドを実行します。 コマンド内の `ctp3.1` を、アンインストールする **mssqlctl** のバージョンに置き換えます。 バージョンが CTP 3.1 より前の場合は、バージョン番号の前にダッシュ (例: `ctp-2.5`) を追加します。

   ```powershell
   pip3 uninstall -r https://private-repo.microsoft.com/python/ctp3.1/mssqlctl/requirements.txt
   ```

1. **azdata** の最新バージョンをインストールします。 次のコマンドを実行すると、CTP 3.2 の **azdata** がインストールされます。

   **Windows:**

   ```powershell
   pip3 install -r https://aka.ms/azdata
   ```

   **Linux:**

   ```bash
   pip3 install -r https://aka.ms/azdata --user
   ```

   > [!IMPORTANT]
   > 各リリースで、**azdata** へのパスが変更されます。 以前に **azdata** または **mssqlctl** をインストールしている場合でも、新しいクラスターを作成する前に、最新のパスから再インストールする必要があります。

## <a id="azdataversion"></a> azdata のバージョンを確認する

新しいビッグ データ クラスターを展開する前に、`--version` パラメーターを指定して、最新バージョンの **azdata** を使用していることを確認します。

```bash
azdata --version
```

## <a name="install-the-new-release"></a>新しいリリースをインストールする

以前のビッグ データ クラスターを削除し、最新の **azdata** をインストールしたら、現在の展開手順に従って、新しいビッグ データ クラスターを展開します。 詳細については、「 [How [!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] to deploy on Kubernetes](deployment-guidance.md)」を参照してください。 次に、必要なデータベースまたはファイルを復元します。

## <a name="next-steps"></a>次の手順

ビッグデータクラスターの詳細について[は[!INCLUDE[big-data-clusters-2019](../includes/ssbigdataclusters-ss-nover.md)] ](big-data-cluster-overview.md)、「」を参照してください。
