---
title: 参照データ (外部) のナレッジを使用したデータのクレンジング | Microsoft Docs
ms.custom: ''
ms.date: 06/13/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology: data-quality-services
ms.topic: conceptual
ms.assetid: 158009e9-8069-4741-8085-c14a5518d3fc
author: lrtoyou1223
ms.author: lle
manager: craigg
ms.openlocfilehash: 7d7ab8fee8dfb9aabd32922d297f61ae493a5bd0
ms.sourcegitcommit: 3026c22b7fba19059a769ea5f367c4f51efaf286
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2019
ms.locfileid: "65480915"
---
# <a name="cleanse-data-using-reference-data-external-knowledge"></a>参照データ (外部) のナレッジを使用したデータのクレンジング
  このトピックでは、参照データ プロバイダーから提供されるナレッジを使用してデータをクレンジングする方法について説明します。 クレンジング アクティビティを実行する手順は、参照データ プロバイダーから提供されるナレッジを使ってデータをクレンジングする場合も「[DQS &#40;内部&#41; ナレッジを使用したデータのクレンジング](../../2014/data-quality-services/cleanse-data-using-dqs-internal-knowledge.md)」で説明した手順とすべて同じですが、このトピックでは、[!INCLUDE[ssDQSnoversion](../includes/ssdqsnoversion-md.md)] (DQS) での参照データ サービスを使ったデータ クレンジングに固有の情報を示します。  
  
 DQS の参照データ サービス機能を使用してデータをクレンジングする場合、DQS のクレンジング プロセスで、マップされたドメイン値がバッチ要求として参照データ サービス プロバイダーに送信されます。 参照データ サービスから、次の情報を含む応答が返されます。  
  
-   修正案  
  
-   [信頼度]  
  
-   マップされたドメインに関する追加情報。 参照データでは、この追加データを使用してソースを標準化、解析、または強化することもできます。 この情報は応答の追加フィールドに記載されています。  
  
 参照データ サービスから応答を受け取った後、DQS のクレンジング アクティビティで次の処理が行われます。  
  
-   ドメインを参照データ サービスにマップするときに指定した **[自動修正しきい値]** と **[最小信頼度]** の値に基づいて、ドメイン値が信頼レベルに応じて自動的に修正または提案されます。  
  
    > [!NOTE]  
    >  参照データ サービスのナレッジを使用してデータをクレンジングするときは、 **[全般設定]** タブの **[構成]** セクションで指定したしきい値ではなく、参照データ サービスにドメインをマップするときに指定したしきい値が適用されます。 参照データのクレンジングのしきい値の値を指定する方法の詳細については、手順 9. を参照してください。[参照データへのドメインまたは複合ドメインのアタッチ](../../2014/data-quality-services/attach-a-domain-or-composite-domain-to-reference-data.md)します。  
  
-   ドメイン値が"**提案**"、"**新規**"、"**無効**"、"**修正済み**"、および "**適切**" に分類されます。  
  
-   追加データがソースに追加され、クレンジングされたデータと一緒に情報をエクスポートできるようになります。  
  
## <a name="before-you-begin"></a>はじめに  
  
###  <a name="Prerequisites"></a> 前提条件  
 DQS のナレッジ ベース内の必要なドメインを適切な参照データ サービスにマップしておく必要があります。 また、クレンジングするデータの種類に関するナレッジがナレッジ ベースに含まれている必要があります。 たとえば、米国の住所が格納されたソース データをクレンジングする場合は、米国の住所に関する高品質データを提供する参照データ サービス プロバイダーにドメインをマップする必要があります。 詳細については、次を参照してください。[参照データへのドメインまたは複合ドメインのアタッチ](../../2014/data-quality-services/attach-a-domain-or-composite-domain-to-reference-data.md)します。  
  
###  <a name="Security"></a> セキュリティ  
  
####  <a name="Permissions"></a> Permissions  
 データ クレンジングを実行するには、DQS_MAIN データベースの dqs_kb_editor ロールまたは dqs_kb_operator ロールが必要です。  
  
##  <a name="Cleanse"></a> 参照データのナレッジを使用したデータのクレンジング  
 前のトピックでは、マップしたドメインを使用したのと同じ例では、引き続き[参照データへのドメインまたは複合ドメインのアタッチ](../../2014/data-quality-services/attach-a-domain-or-composite-domain-to-reference-data.md)、Windows Azure Marketplace の Melissa Data サービスとします。 ここでは、同じドメインを使用して、いくつかのサンプルの米国の住所をクレンジングします。 データをクレンジングする手順は、「[DQS &#40;内部&#41; ナレッジを使用したデータのクレンジング](../../2014/data-quality-services/cleanse-data-using-dqs-internal-knowledge.md)」で説明されているものと同じです。 処理中に注意が必要な箇所には説明を補足しています。  
  
1.  データ品質プロジェクトを作成し、 **[クレンジング]** アクティビティを選択します。 「 [Create a Data Quality Project](../../2014/data-quality-services/create-a-data-quality-project.md)」を参照してください。  
  
2.  **[マップ]** ページで、**Address Line**、**City**、**State**、および **Zip** の 4 つのドメインをソース データの適切な列にマップします。 [**次へ**] をクリックします。  
  
    > [!NOTE]  
    >  **Address Verification** 複合ドメイン内の 4 つのドメインをすべてマップしているため、データ クレンジングは、個々のドメイン レベルではなく、複合ドメイン レベルで実行されます。  
  
3.  **[最適化]** ページで、 **[開始]** をクリックしてコンピューター支援型のクレンジング プロセスを実行します。 クレンジング プロセスが完了したら、 **[次へ]** をクリックします。  
  
    > [!NOTE]  
    >  **[最適化]** ページには、参照データ サービスにアタッチされているドメインに関する情報が次の 2 とおりの方法で表示されます。  
    >   
    >  -   **[開始]** ボタンの下に、"ドメイン \<Domain1>、\<Domain2>、...\<DomainN> を、参照データ サービス プロバイダーを使用してクレンジングします" というメッセージが表示されます。 この例の場合、"ドメイン Address Verification を、参照データ サービス プロバイダーを使用してクレンジングします" というメッセージが表示されます。  
    > -   参照データ サービス プロバイダーにアタッチされているドメインに対して、**[プロファイラー]** 領域にアイコン ![RDS にドメインがアタッチされている](../../2014/data-quality-services/media/dqs-rdsindicator.JPG "RDS にドメインがアタッチされている") が表示されます。 この例の場合、 **Address Verification** 複合ドメインに対してこのアイコンが表示されます。  
  
4.  **[結果の管理と表示]** ページで、ドメイン値を確認します。 参照データ サービスでは、値に対する提案が複数ある場合、参照データ サービスにドメインをマップするときに **[提案された候補]** ボックスで指定した提案の最大数に応じて表示できます。 たとえば、次の米国の住所に対しては 2 つの提案が表示されます。  
  
     **元の値:**  
  
    |Address Line|City|State|Zip|  
    |------------------|----------|-----------|---------|  
    |1 msft way|Redmond||98052|  
  
     **提案される値:**  
  
    |Address Line|City|State|Zip|  
    |------------------|----------|-----------|---------|  
    |1 Microsoft Way|Redmond|WA|98052|  
    |PO Box 1|Redmond|WA|98073|  
  
     ![参照データ サービスを使ったクレンジング](../../2014/data-quality-services/media/dqs-rdscleansing.JPG "参照データ サービスを使ったクレンジング")  
  
    > [!NOTE]  
    >  複合ドメインについては、さらに、コンピューター支援型のクレンジング プロセスで修正された個々のドメインが別の色で強調表示されます。 たとえば、この例では、 **Address Line** ドメインと **State** ドメインが修正されているため、それらのドメインがシアンで強調表示されます。  
  
5.  すべてのドメイン値の確認が完了したら、 **[次へ]** をクリックしてデータをエクスポートします。  
  
6.  **[エクスポート]** ページに、各ドメインのクレンジング アクティビティに関する通常の情報 (ソース、理由、信頼度、およびステータス) に加え、住所データに関して Melissa Data 参照データ サービスから提供された追加の情報が表示されます。これには、住所の経度と緯度、郡の名前、住所タイプ (高層ビルや番地) などが含まれます。  
  
7.  目的のエクスポート先 (SQL Server、CSV、または Excel) にデータをエクスポートし、 **[完了]** をクリックしてプロジェクトを閉じます。  
  
    > [!IMPORTANT]  
    >  Excel の 64 ビット版を使用している場合、クレンジングされたデータは Excel ファイルにエクスポートできません。SQL Server データベースまたは .csv ファイルにのみエクスポートできます。  
  
  
