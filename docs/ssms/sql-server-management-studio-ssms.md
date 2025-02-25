---
title: SQL Server Management Studio (SSMS)
description: SQL Server Management Studio (SSMS) の概要とその機能に関する説明。
ms.prod: sql
ms.technology: ssms
ms.topic: overview
author: markingmyname
ms.author: maghan
ms.reviewer: ''
f1_keywords:
- sql13.ssms.viewhelp.f1
helpviewer_keywords:
- SQL Server Management Studio
- SQL Server Management Studio for Integration Services
- SQL Server Management Studio for Reporting Services
- SQL Server Management Studio for Analysis Services
ms.custom: ''
ms.date: 05/29/2019
ms.openlocfilehash: 8632bd86a19bdce57114e36247f0b805da6804e7
ms.sourcegitcommit: a1adc6906ccc0a57d187e1ce35ab7a7a951ebff8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68893263"
---
# <a name="what-is-sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS) とは何か? 

[!INCLUDE[appliesto-ss-asdb-asdw-xxx-md.md](../includes/appliesto-ss-asdb-asdw-xxx-md.md)]

[!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)] (SSMS) は、SQLインフラストラクチャを管理するための統合環境です。 SSMS を使用して、[!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]、Azure SQL Database、および SQL Data Warehouse のすべてコンポーネントのアクセス、構成、管理、運営、および開発を行います。 SSMS には、さまざまなグラフィック ツールと、機能の豊富な多くのスクリプト エディターを結合して、あらゆるスキル レベルの開発者やデータベース管理者が [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] にアクセスできる包括的なユーティリティが 1 つ用意されています。

- [**SQL Server Management Studio (SSMS) のダウンロード**](download-sql-server-management-studio-ssms.md)
- [**SQL Server Developer のダウンロード**](https://my.visualstudio.com/Downloads?q=SQL%20Server%20Developer)
- [**Visual Studio のダウンロード**](https://www.visualstudio.com/downloads/)

## <a name="sql-server-management-studio-components"></a>SQL Server Management Studio のコンポーネント  
  
|[説明]|コンポーネント|  
|---------------|---------|  
|**オブジェクト エクスプローラー** を使用して、 [!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)]の 1 つ以上のインスタンスに存在するすべてのオブジェクトを表示および管理します。|[[オブジェクト エクスプローラー]](../ssms/object/object-explorer.md)|  
|**テンプレート エクスプローラー**を使用し、クエリとスクリプトの開発を迅速化する定型句ファイルを作成し、管理する方法。|[テンプレート エクスプローラー](../ssms/template/template-explorer.md)|  
|将来非推奨となっている **ソリューション エクスプローラー** を使用して、スクリプトやクエリなどの管理アイテムを扱うためのプロジェクトを構築する方法。|[ソリューション エクスプローラー](../ssms/solution/solution-explorer.md)|  
|[!INCLUDE[ssManStudio](../includes/ssmanstudio-md.md)]に付属のビジュアル デザイン ツールを使用する方法。|[Visual Database Tools](../ssms/visual-db-tools/visual-database-tools.md)|  
|[!INCLUDE[ssManStudio](../includes/ssmanstudio-md.md)] 言語エディターを使用し、クエリおよびスクリプトを対話形式でビルド、デバッグする方法。|[クエリ エディターとテキスト エディター (SQL Server Management Studio)](../relational-databases/scripting/query-and-text-editors-sql-server-management-studio.md)|

## <a name="sql-server-management-studio-for-business-intelligence"></a>ビジネス インテリジェンスのための SQL Server Management Studio

[!INCLUDE[ssASnoversion](../includes/ssasnoversion_md.md)]、[!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)]、および [!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)] のアクセス、構成、管理を行うには、[!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)] を使用します。 この 3 つのビジネス インテリジェンス テクノロジは [!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)]を使用しますが、各テクノロジに関連付けられている管理タスクは少しずつ異なります。
  
> [!NOTE]
> [!INCLUDE[ssASnoversion](../includes/ssasnoversion_md.md)]、 [!INCLUDE[ssRSnoversion](../includes/ssrsnoversion-md.md)]、および [!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)] の各ソリューションを作成および変更するには、 [!INCLUDE[ssBIDevStudioFull](../includes/ssbidevstudiofull_md.md)]ではなく [!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)]を使用します。 [!INCLUDE[ssBIDevStudioFull](../includes/ssbidevstudiofull_md.md)] は、 [!INCLUDE[msCoName](../includes/msconame_md.md)][!INCLUDE[vsprvs](../includes/vsprvs-md.md)]をベースとする開発環境です。
  
### <a name="managing-analysis-services-solutions-using-sql-server-management-studio"></a>SQL Server Management Studio を使用した Analysis Services ソリューションの管理

[!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)] では、 [!INCLUDE[ssASnoversion](../includes/ssasnoversion_md.md)] オブジェクトの管理を行うことができます (バックアップの実行やオブジェクトの処理など)。
  
[!INCLUDE[ssManStudio](../includes/ssmanstudio-md.md)] では、多次元式 (MDX)、データ マイニング拡張機能 (DMX)、および XML for Analysis (XMLA) で作成されるスクリプトの開発と保存を行う [!INCLUDE[ssASnoversion](../includes/ssasnoversion_md.md)] スクリプト プロジェクトが提供されます。 [!INCLUDE[ssASnoversion](../includes/ssasnoversion_md.md)] スクリプト プロジェクトは、 [!INCLUDE[ssASnoversion](../includes/ssasnoversion_md.md)] インスタンスで管理タスクを実行したり、データベースやキューブなどのオブジェクトを再作成したりする場合に使用します。 たとえば、既存の [!INCLUDE[ssASnoversion](../includes/ssasnoversion_md.md)] インスタンスで直接新しいオブジェクトを作成する XMLA スクリプトを [!INCLUDE[ssASnoversion](../includes/ssasnoversion_md.md)] スクリプト プロジェクトで開発することができます。 [!INCLUDE[ssASnoversion](../includes/ssasnoversion_md.md)] スクリプト プロジェクトは、ソリューションの一部として保存し、ソース コード コントロールに統合できます。
  
[!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)]の使用方法について詳しくは、「 [SQL Server Management Studio を使用した開発と実装](https://docs.microsoft.com/analysis-services/instances/analysis-services-scripts-project-in-sql-server-management-studio)」をご覧ください。
  
### <a name="managing-integration-services-solutions-using-sql-server-management-studio"></a>SQL Server Management Studio を使用した Integration Services ソリューションの管理

[!INCLUDE[ssManStudioFull](../includes/ssmanstudiofull-md.md)] では、 [!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)] サービスを使用して、パッケージの管理および実行中のパッケージの監視を行うことができます。 また [!INCLUDE[ssManStudio](../includes/ssmanstudio-md.md)] では、パッケージをフォルダーに編成できるほか、パッケージの実行、インポート、エクスポート、データ変換サービス (DTS) パッケージの移行、および [!INCLUDE[ssISnoversion](../includes/ssisnoversion-md.md)] パッケージのアップグレードを行うことができます。

### <a name="managing-reporting-services-projects-using-sql-server-management-studio"></a>SQL Server Management Studio を使用した Reporting Services プロジェクトの管理

SQL Server Management Studio では、Reporting Services の機能の有効化、サーバーとデータベースの管理、およびロールとジョブの管理を行うことができます。

[共有スケジュール] フォルダーを使用して共有スケジュールを管理し、レポート サーバー データベース (ReportServer、ReportServerTempdb) を管理します。 また、レポート サーバー データベースを新規または別の SQL Server データベース エンジン ([!INCLUDE[ssNoVersion](../includes/ssnoversion-md.md)] [!INCLUDE[ssDE](../includes/ssde_md.md)]) に移動する場合は、master システム データベースで RSExecRole を作成します。 これらの作業の詳細については、次のトピックを参照してください。  

- [Management Studio の操作方法に関するトピック](https://msdn.microsoft.com/60685458-9108-47bf-820a-5e7db454d408)
- [レポート サーバー データベースの管理](../reporting-services/report-server/administer-a-report-server-database-ssrs-native-mode.md)
- [方法:RSExecRole を作成する](../reporting-services/security/create-the-rsexecrole.md)
  
また、各種機能の有効化と構成、サーバーの既定値の設定、およびロールとジョブの管理を行うことで、サーバーを管理します。 これらの作業の詳細については、次のトピックを参照してください。

- [方法:レポート サーバーのプロパティを設定する (Management Studio)](https://msdn.microsoft.com/1ed0f84b-b12a-4e49-b65c-a11a99f9093f)
- [方法:ロールを作成、削除、または変更する (Management Studio)](https://msdn.microsoft.com/3d1d56e6-a283-44a7-8417-36cb4d2c74d1)
- [Reporting Services のクライアント側印刷機能の有効化と無効化](https://msdn.microsoft.com/0e709c96-7517-4547-8ef6-5632f8118524)

## <a name="non-english-language-versions-of-sql-server-management-studio-ssms"></a>英語以外のバージョンの SQL Server Management Studio (SSMS)

言語が混在するセットアップに関するブロックが解除されました。 フランス語の Windows にドイツ語の SSMS をインストールすることができます。 OS の言語が SSMS の言語と異なる場合、ユーザーは [ツール]、[オプション]、[国際対応の設定] の下で言語を変更する必要があります。 変更しないと、SSMS の UI が英語で表示されます。

さまざまなロケールと以前のバージョンについては、「[英語以外の言語バージョンの SQL Server Management Studio (SSMS) をインストールする](install-other-languages.md)」を参照してください。

## <a name="support-policy-for-ssms"></a>SSMS のサポート ポリシー

- SSMS 17.0 以降、SQL Tools チームは [Microsoft モダン ライフサイクル ポリシー](https://support.microsoft.com/help/30881/modern-lifecycle-policy)を採用しました。
- 元の[モダン ライフサイクル ポリシーに関するお知らせ](https://support.microsoft.com/help/447912/announcing-microsoft-modern-lifecycle-policy)を参照してください。
- 詳細については、「[モダン ライフサイクル ポリシーに関する FAQ](https://support.microsoft.com/help/30882/modern-lifecycle-policy-faq)」を参照してください。

[!INCLUDE[get-help-options](../includes/paragraph-content/get-help-options.md)]

## <a name="next-steps"></a>次の手順

- [英語以外の言語バージョンの SQL Server Management Studio (SSMS) をインストールする](install-other-languages.md)
- [チュートリアル: SQL Server Management Studio](tutorials/tutorial-sql-server-management-studio.md)
- [チュートリアル: Transact-SQL ステートメントの作成](https://msdn.microsoft.com/2addc9be-67d0-423d-a457-192fe9d7d058)
- [SQL Server Data Tools を使用した開発と実装](https://docs.microsoft.com/analysis-services/multidimensional-models/creating-multidimensional-models-using-sql-server-data-tools-ssdt)
- [SQL Server Data Tools の Reporting Services](../reporting-services/tools/reporting-services-in-sql-server-data-tools-ssdt.md)
- [チュートリアル: SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/tutorials/tutorial-sql-server-management-studio)

[!INCLUDE[contribute-to-content](../includes/paragraph-content/contribute-to-content.md)]