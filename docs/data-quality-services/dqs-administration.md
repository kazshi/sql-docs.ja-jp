---
title: DQS 管理 | Microsoft Docs
ms.custom: ''
ms.date: 10/01/2012
ms.prod: sql
ms.prod_service: data-quality-services
ms.reviewer: ''
ms.technology: data-quality-services
ms.topic: conceptual
helpviewer_keywords:
- dqs administration
- administration
- dqs,adminstration
ms.assetid: 9940ef5d-f6f6-4dec-9414-1077a4d7f12b
author: lrtoyou1223
ms.author: lle
ms.openlocfilehash: a84ff61d1656743953f5f854a1b658b303a7acf1
ms.sourcegitcommit: b2464064c0566590e486a3aafae6d67ce2645cef
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67992133"
---
# <a name="dqs-administration"></a>DQS 管理

[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md-winonly](../includes/appliesto-ss-xxxx-xxxx-xxx-md-winonly.md)]

  [!INCLUDE[ssDQSnoversion](../includes/ssdqsnoversion-md.md)] (DQS) では、 [!INCLUDE[ssDQSServer](../includes/ssdqsserver-md.md)]で実行されるさまざまな DQS アクティビティを管理し、DQS アクティビティに関連するサーバー レベルのプロパティを構成し、参照データ サービスの設定を構成し、DQS ログの設定を構成できます。 これらの作業は、 **の** 管理 [!INCLUDE[ssDQSClient](../includes/ssdqsclient-md.md)]機能を使用して行います。 DQS でのセキュリティ アクセス (ロール) に基づいて、特定の管理機能へのアクセスが許可/禁止されます。  
  
 これらの管理機能とは別に、このトピックでは、 [!INCLUDE[ssDQSClient](../includes/ssdqsclient-md.md)]を使用して実行しない管理アクティビティである DQS データベースのバックアップと復元についても説明します。  
  
 [!INCLUDE[ssDQSClient](../includes/ssdqsclient-md.md)] での管理機能には次のような利点があります。  
  
-   データ スチュワードは、 [!INCLUDE[ssDQSServer](../includes/ssdqsserver-md.md)] から [!INCLUDE[ssDQSClient](../includes/ssdqsclient-md.md)]でのさまざまな DQS アクティビティを監視できます。  
  
-   DQS 管理者は、 [!INCLUDE[ssDQSServer](../includes/ssdqsserver-md.md)] から [!INCLUDE[ssDQSClient](../includes/ssdqsclient-md.md)]での DQS アクティビティを監視でき、必要に応じて、実行中のアクティビティを *終了* させたり、アクティビティ内で実行中のプロセスを *停止* させたりできます。  
  
-   Windows Azure Marketplace との接続の設定や、ダイレクト サード パーティ参照データ サービス プロバイダーの管理など、参照データ サービスの設定を構成します。  
  
-   クレンジングおよび照合アクティビティのしきい値を構成します。  
  
-   [!INCLUDE[ssDQSClient](../includes/ssdqsclient-md.md)]での通知を有効または無効にします。  
  
-   イベントの重大度レベルに基づくログを構成します。  
  
##  <a name="AdminUsingClent"></a> Data Quality Client を使用した管理アクティビティ  
 以下のアクティビティは、 **の** 管理 [!INCLUDE[ssDQSClient](../includes/ssdqsclient-md.md)]機能を使用して行います。  
  
### <a name="activity-monitoring"></a>[アクティビティ監視]  
 **の** [アクティビティ監視] [!INCLUDE[ssDQSClient](../includes/ssdqsclient-md.md)] 画面には、 [!INCLUDE[ssDQSServer](../includes/ssdqsserver-md.md)]で実行されている各アクティビティについての詳細な情報が表示されます。 この画面は、 [!INCLUDE[ssDQSServer](../includes/ssdqsserver-md.md)] アプリケーションが接続されている [!INCLUDE[ssDQSClient](../includes/ssdqsclient-md.md)] 上で実行されているすべてのアクティビティに関する高レベルの監視を実行するために主にデータ スチュワードによって使用されます。 この画面では、システム レベルの監視は提供されません。 また、この画面を使用すると、DQS 管理者は、必要に応じて、実行中のアクティビティを終了したり、アクティビティ内で実行中のプロセスを停止したりして、アクティビティまたはアクティビティ内のプロセスを制御できます。 ナレッジの検出、ドメインの管理、照合ポリシー、クレンジング、照合、および SQL Server Integration Services (SSIS) ベースのクレンジングに関するデータが表示されます。  
  
### <a name="configuration"></a>構成  
 **の** [構成] [!INCLUDE[ssDQSClient](../includes/ssdqsclient-md.md)] 画面では、DQS 管理者は次のことを行うことができます。  
  
-   **[参照データ]** : 参照データ サービス プロバイダー(Windows Azure Marketplace プロバイダーまたはダイレクト参照データ サービス プロバイダー) を構成します。 参照データ サービス プロバイダーを設定した後は、ナレッジ ベースでのドメイン管理アクティビティの間にドメイン/複合ドメインと参照データをマップし、データ品質プロジェクトでのクレンジング アクティビティに同じナレッジ ベースを使用できます。 また、インターネットに接続して Windows Azure Marketplace を使用するためのプロキシ設定を指定できます。  
  
-   **[全般設定]** :データ クレンジングおよびデータ照合のしきい値、および [!INCLUDE[ssDQSClient](../includes/ssdqsclient-md.md)] でのプロファイリングに対する通知を有効にするかどうかを指定できます。 これらのしきい値は、データ品質プロジェクトでのコンピューター支援型のクレンジングおよび照合アクティビティの間に、DQS によって使用されます。  
  
-   **[ログの設定]** :DQS のログ ファイルには DQS で実行されたアクティビティが記録され、メンテナンスおよびトラブルシューティングの間に運用上の問題を追跡するのに役立ちます。 さまざまな DQS 機能 (ドメイン管理、ナレッジ検出、クレンジング、照合、参照データ サービス) および DQS モジュールに関してログに記録するメッセージを、イベントの重大度レベルに基づいてフィルターできます。  
  
> [!NOTE]  
>  **[構成]** 画面は、DQS_MAIN データベースの dqs_administrator ロールを持つユーザーのみ使用できます。  
  
##  <a name="AdminOutsideClient"></a> Data Quality Client の外部での管理アクティビティ  
 アクティビティは Data Quality Client の外部で実行されます。  
  
-   **DQS データベースのバックアップと復元**:DQS データベースのバックアップと復元は、他の SQL Server データベースのバックアップおよび復元と同じですが、DQS に固有の考慮事項がいくつかあります。  
  
-   **DQS のデータベースのデタッチとアタッチ**:DQS データベースをデタッチおよびアタッチする手順は、他の SQL Server データベースのデタッチおよびアタッチと同じですが、DQS に固有の考慮事項がいくつかあります。  
  
 詳細については、「 [Manage DQS Databases](../data-quality-services/manage-dqs-databases.md)」を参照してください。  
  
## <a name="related-tasks"></a>Related Tasks  
  
|タスクの説明|トピック|  
|----------------------|-----------|  
|DQS のアクティビティを監視する方法について説明します。|[DQS アクティビティの監視](../data-quality-services/monitor-dqs-activities.md)|  
|DQS で参照データの設定を構成する方法について説明します。|[参照データを使用するように DQS を構成する](../data-quality-services/configure-dqs-to-use-reference-data.md)|  
|クレンジングおよび照合アクティビティのしきい値を構成する方法について説明します。|[クレンジングと照合のしきい値の構成](../data-quality-services/configure-threshold-values-for-cleansing-and-matching.md)|  
|DQS で通知を有効または無効にする方法について説明します。|[DQS のプロファイル通知の有効化または無効化](../data-quality-services/enable-or-disable-profiling-notifications-in-dqs.md)|  
|イベントの重大度レベルに基づいて DQS のログを構成する方法について説明します。|[DQS ログ ファイルの重大度レベルの構成](../data-quality-services/configure-severity-levels-for-dqs-log-files.md)|  
|DQS ログの詳細設定を構成する方法について説明します。|[DQS ログ ファイルの詳細設定の構成](../data-quality-services/configure-advanced-settings-for-dqs-log-files.md)|  
|DQS データベースのバックアップと復元の方法について説明します。|[DQS データベースのバックアップと復元](../data-quality-services/backing-up-and-restoring-dqs-databases.md)|  
|DQS データベースをデタッチおよびアタッチする方法について説明します。|[DQS データベースのデタッチとアタッチ](../data-quality-services/detaching-and-attaching-dqs-databases.md)|  
  
## <a name="see-also"></a>関連項目  
 [DQS の参照データ サービス](../data-quality-services/reference-data-services-in-dqs.md)   
 [DQS ログ ファイルの管理](../data-quality-services/manage-dqs-log-files.md)   
 [DQS データベースの管理](../data-quality-services/manage-dqs-databases.md)  
  
  
