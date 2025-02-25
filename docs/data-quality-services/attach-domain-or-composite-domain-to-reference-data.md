---
title: 参照データへのドメインまたは複合ドメインのアタッチ | Microsoft Docs
ms.custom: ''
ms.date: 03/01/2017
ms.prod: sql
ms.prod_service: data-quality-services
ms.reviewer: ''
ms.technology: data-quality-services
ms.topic: conceptual
f1_keywords:
- sql13.dqs.dm.refdata.f1
- sql13.dqs.dm.refcatalog.f1
ms.assetid: 36af981c-d0d0-4dc6-afe5-bbb3c97845dc
author: lrtoyou1223
ms.author: lle
ms.openlocfilehash: 8171d552d850ec929f7aba5b55b6f0e2c6ae7265
ms.sourcegitcommit: b2464064c0566590e486a3aafae6d67ce2645cef
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67935635"
---
# <a name="attach-domain-or-composite-domain-to-reference-data"></a>参照データへのドメインまたは複合ドメインのアタッチ

[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md-winonly](../includes/appliesto-ss-xxxx-xxxx-xxx-md-winonly.md)]

  このトピックでは、データ品質ナレッジ ベースのドメインと複合ドメインを Windows Azure Marketplace の参照データ サービスにアタッチして、高品質参照データに対するナレッジを構築する方法について説明します。 各参照データ サービスには、スキーマ (データ列) が含まれています。 ドメインまたは複合ドメインを参照データ サービスにアタッチしたら、アタッチしたドメインまたはアタッチした複合ドメイン内の個々のドメインを参照データ サービス スキーマの適切な列にマップする必要があります。 複合ドメインを参照データ サービスにアタッチすると、参照データ サービスに 1 つだけドメインをアタッチして、複合ドメイン内の個々のドメインを参照データ サービス スキーマの適切な列にマップできます。  

> [!IMPORTANT]
> この記事では、以前は Azure DataMarket から利用できたサード パーティ参照データ サービスについて説明します。 DataMarket および Data Services (Melissa アドレス データなどを含む) は、2016 年 12 月 31 日以降廃止となりました。 その結果、DataMarket から指定されたサービスを使用して、この記事に示されている例を実行できなくなりました。 サード パーティ参照データ プロバイダーからオンラインで直接利用可能な参照データ サービスは引き続き使用できます。

> [!WARNING]  
>  参照データ サービスにアタッチされた複合ドメインは、ドメインを参照データ サービス スキーマの列にマップするときに、ドメインのドロップダウン リストで使用できます。 複合ドメインを参照データ サービス スキーマの列にマップしないでください。複合ドメイン内の個々のドメインのみを参照データ サービス スキーマの適切な列にマップする必要があります。 それ以外の場合、エラーが発生します。  
  
 参照データ サービス スキーマには、参照データ サービスを使用する場合に適切なドメインにマップする必要がある必須列が含まれている場合があります。 参照データ スキーマの必須列には列名に "(M)" と表示されます。 たとえば、**AddressLine** は **Melissa Data - Address Data** の必須スキーマ列で、**CompanyName** は **Digital Trowel Inc. - Us companies and professional data for SQL users** の必須スキーマ列です。  
  
 このトピックでは、複合ドメイン **Address Verification** に 4 つのドメイン (**Address Line**、**City**、**State**、**Zip**) を作成し、複合ドメインを **Melissa Data - Address Check** 参照データ サービスにアタッチした後、複合ドメイン内の個々のドメインを参照データ サービス スキーマの適切な列にマップします。  
  
## <a name="before-you-begin"></a>はじめに  
  
###  <a name="Prerequisites"></a> 前提条件  
 参照データ サービスを使用するように [!INCLUDE[ssDQSnoversion](../includes/ssdqsnoversion-md.md)] (DQS) を構成しておく必要があります。 「[参照データを使用する DQS の構成](../data-quality-services/configure-dqs-to-use-reference-data.md)」をご覧ください。  
  
###  <a name="Security"></a> セキュリティ  
  
#### <a name="permissions"></a>アクセス許可  
 参照データにドメインをマップするには、DQS_MAIN データベースの dqs_kb_editor ロールが必要です。  
  
##  <a name="Map"></a> Melissa Data の参照データへのドメインのマップ  
  
1.  [!INCLUDE[ssDQSInitialStep](../includes/ssdqsinitialstep-md.md)]「[Data Quality Client アプリケーションの実行](../data-quality-services/run-the-data-quality-client-application.md)」をご覧ください。  
  
2.  [!INCLUDE[ssDQSClient](../includes/ssdqsclient-md.md)] のホーム画面で、 **[ナレッジ ベース管理]** の **[新しいナレッジ ベース]** をクリックします。  
  
3.  **[新しいナレッジ ベース]** 画面で、新しいナレッジ ベースの名前を入力し、 **[ドメイン管理]** アクティビティをクリックして **[作成]** をクリックします。  
  
4.  **[ドメイン管理]** 画面で、 **[ドメインの作成]** アイコンをクリックしてドメインを作成します。 4 つのドメイン、**Address Line**、**City**、**State**、**Zip** を作成します。  
  
5.  **[複合ドメインの作成]** アイコンをクリックして複合ドメインを作成します。 **[複合ドメインの作成]** ダイアログ ボックスで、 **[複合ドメイン名]** ボックスに「 **Address Verification** 」と入力し、手順 3. で作成したすべてのドメインを複合ドメインに含めます。 **[OK]** をクリックします。  
  
6.  左側の **[ドメイン]** ペインで、 **[Address Verification]** をクリックして複合ドメインを選択し、右側の **[参照データ]** タブをクリックします。  
  
7.  **[参照]** アイコンをクリックします。  
  
8.  **[オンライン参照データ プロバイダーのカタログ]** ダイアログ ボックスで以下を行います。  
  
    1.  **[DataMarket Data Quality Services]** で **[メリッサ データ - アドレスをチェック]** ボックスをオンにします。  
  
    2.  Melissa Data - Address Check 参照データ サービスの列を適切なドメイン (Address Line、City、State、および Zip) にマップします。 列をマップするには、 **[RDS スキーマ]** 列で参照データ サービス列を選択し、 **[ドメイン]** 列で適切なドメインを選択します。 テーブルに行を追加するには、 **[スキーマ エントリの追加]** アイコンをクリックします。  
  
    3.  **[OK]** をクリックして変更を保存し、 **[オンライン参照データ プロバイダーのカタログ]** ダイアログ ボックスを閉じます。  
  
         ![[オンライン参照データ プロバイダーのカタログ] ダイアログ ボックス](../data-quality-services/media/dqs-onlinereferencedataproviderscatalog.gif "[オンライン参照データ プロバイダーのカタログ] ダイアログ ボックス")  
  
        > [!NOTE]  
        >  -   **[オンライン参照データ プロバイダーのカタログ]** ダイアログ ボックスでは、Windows Azure Marketplace でサブスクライブしているすべての参照データ サービス プロバイダーが **[DataMarket Data Quality Services]** ノードに表示されます。 ダイレクト オンライン サード パーティ参照データ サービス プロバイダーを DQS で構成している場合は、 **[サード パーティのダイレクト オンライン プロバイダー]** という別のノードに表示されます (ここでは、ダイレクト オンライン サード パーティ参照データ サービス プロバイダーを DQS で構成していないため表示されません)。  
  
9. **[参照データ]** タブに戻ります。 **[プロバイダーの設定]** 領域で、必要に応じて以下のボックスの値を変更します。  
  
    -   **[自動修正しきい値]** : 参照データ サービスの修正のうち、信頼レベルがこのしきい値を超える修正は自動的に実行されます。 割合値に相当する値を 10 進数表記で入力します。 たとえば、90% であれば「0.9」と入力します。  
  
    -   **[提案された候補]** : 参照データ サービスから提案された候補を表示する数です。  
  
    -   **[最小信頼度]** : 参照データ サービスの提案のうち、信頼レベルがこの値に満たない提案は無視されます。 割合値に相当する値を 10 進数表記で入力します。 たとえば、60% であれば「0.6」と入力します。  
  
10. **[完了]** をクリックしてナレッジ ベースを発行します。 ナレッジ ベースが正常に発行されると、確認のメッセージが表示されます。  
  
 このナレッジ ベースをデータ品質プロジェクトのクレンジング アクティビティに使用できるようになりました。Windows Azure Marketplace を通じて Melissa Data から提供されるナレッジに基づいて、ソース データに含まれる米国の住所を標準化およびクレンジングできます。  
  
##  <a name="FollowUp"></a>補足情報: 参照データにドメインをマップした後  
 データ品質プロジェクトを作成し、このトピックで作成したナレッジ ベースと照らし合わせて、米国の住所を含むソース データに対するクレンジング アクティビティを実行します。 「[参照データ &#40;外部&#41; のナレッジを使用したデータのクレンジング](../data-quality-services/cleanse-data-using-reference-data-external-knowledge.md)」をご覧ください。  
  
## <a name="see-also"></a>関連項目  
 [DQS の参照データ サービス](../data-quality-services/reference-data-services-in-dqs.md)   
 [データ クレンジング](../data-quality-services/data-cleansing.md)  
  
  
