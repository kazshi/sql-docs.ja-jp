---
title: データ ソース (SSAS 多次元) を作成する |Microsoft Docs
ms.custom: ''
ms.date: 03/06/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology: analysis-services
ms.topic: conceptual
f1_keywords:
- sql12.asvs.datasourcedesigner.f1
- sql12.asvs.sqlserverstudio.impersonationinfo.f1
- sql12.asvs.connectionmanager.f1
helpviewer_keywords:
- impersonation [Analysis Services]
- data sources [Analysis Services], creating
- security [Analysis Services], data source connections
ms.assetid: 9fab8298-10dc-45a9-9a91-0c8e6d947468
author: minewiskan
ms.author: owend
manager: craigg
ms.openlocfilehash: db9a94bf47071692b4ecf85e6bdb850132b8a417
ms.sourcegitcommit: 3026c22b7fba19059a769ea5f367c4f51efaf286
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2019
ms.locfileid: "66076519"
---
# <a name="create-a-data-source-ssas-multidimensional"></a>データ ソースの作成 (SSAS 多次元)
  [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] の多次元モデルでは、データ ソース オブジェクトが、処理 (またはインポート) するデータを持つデータ ソースへの接続を表します。 多次元モデルには少なくとも 1 つのデータ ソース オブジェクトが含まれている必要がありますが、複数のデータ ウェアハウスのデータを結合するために、データ ソース オブジェクトをさらに追加することもできます。 このトピックで説明する手順に従って、モデルのデータ ソース オブジェクトを作成します。 このオブジェクトのプロパティの設定の詳細については、「[データ ソースのプロパティの設定 &#40;SSAS 多次元&#41;](set-data-source-properties-ssas-multidimensional.md)」を参照してください。  
  
 このトピックのセクションは次のとおりです。  
  
 [データ プロバイダーの選択](#bkmk_provider)  
  
 [資格情報と権限借用オプションの設定](#bkmk_impersonation)  
  
 [接続プロパティの表示または編集](#bkmk_ConnectionString)  
  
 [データ ソース ウィザードによるデータ ソースの作成](#bkmk_steps)  
  
 [既存の接続を使用したデータ ソースの作成](#bkmk_connection)  
  
 [モデルへの複数のデータ ソースの追加](#bkmk_multipleDS)  
  
##  <a name="bkmk_provider"></a> データ プロバイダーの選択  
 マネージド [!INCLUDE[msCoName](../../includes/msconame-md.md)] .NET Framework またはネイティブ OLE DB プロバイダーを使用して接続できます。 SQL Server データ ソースでは、通常、SQL Server Native Client を使用するとパフォーマンスが向上するため、このデータ プロバイダーを使用することをお勧めします。  
  
 Oracle や他のサードパーティのデータ ソースの場合、そのサードパーティがネイティブ OLE DB プロバイダーを提供しているかどうかを確認し、まずそのプロバイダーを使用してみます。 エラーが発生した場合は、接続マネージャーで一覧に示されている他の .NET プロバイダーまたはネイティブ OLE DB プロバイダーを試してみます。 使用するデータ プロバイダーが、 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] ソリューションの開発と実行に使用するすべてのコンピューターにインストールされていることを確認してください。  
  
##  <a name="bkmk_impersonation"></a> 資格情報と権限借用オプションの設定  
 データ ソース接続では、Windows 認証またはデータベース管理システムによって提供される認証サービス (SQL Azure データベースに接続するときの SQL Server 認証など) を使用できる場合があります。 指定するアカウントには、リモート データベース サーバーのログイン権限と外部データベースの読み取り権限が必要です。  
  
### <a name="windows-authentication"></a>[Windows 認証]  
 Windows 認証を使用する接続は、データ ソース デザイナーの **[権限借用情報]** タブで指定されます。 このタブを使用して、外部データ ソースへの接続時に [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] の実行に使用するアカウントを指定する権限借用オプションを選択します。 すべてのシナリオですべてのオプションを使用できるわけではありません。 これらのオプションと使用する状況の詳細については、「[権限借用オプションの設定 &#40;SSAS - 多次元&#41;](set-impersonation-options-ssas-multidimensional.md)」を参照してください。  
  
### <a name="database-authentication"></a>データベース認証  
 Windows 認証の代わりに、データベース管理システムによって提供される認証サービスを使用する接続を指定できます。 場合によっては、データベース認証を使用することが必要となります。 データベース認証を使用する必要があるのは、SQL Server 認証を使用して Windows Azure SQL データベースに接続する場合や、別のオペレーティング システムまたは信頼されていないドメインで実行されているリレーショナル データ ソースにアクセスする場合などです。  
  
 データベース認証を使用するデータ ソースでは、接続文字列でデータベース ログインのユーザー名とパスワードが指定されます。 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] モデルでデータ ソース接続を設定する場合、接続マネージャーでユーザー名とパスワードを入力すると、接続文字列に資格情報が追加されます。 必ず、データの読み取り権限を持つユーザー ID を指定してください。  
  
 データを取得するときに、接続するクライアント ライブラリは接続文字列に資格情報が含まれた接続要求を作成します。 [権限借用情報] タブの Windows 認証資格情報のオプションは接続では使用されませんが、ローカル コンピューター上のリソースへのアクセスなど、他の操作に使用できます。 詳細については、「[権限借用オプションの設定 &#40;SSAS - 多次元&#41;](set-impersonation-options-ssas-multidimensional.md)」を参照してください。  
  
 モデルでデータ ソース オブジェクトを保存すると、接続文字列とパスワードが暗号化されます。  セキュリティを確保するために、表示されているパスワードのトレースは、その後、ツール、スクリプト、またはコードで表示するときに接続文字列からすべて削除されます。  
  
> [!NOTE]  
>  [!INCLUDE[ssBIDevStudioFull](../../includes/ssbidevstudiofull-md.md)] の既定では、接続文字列と共にパスワードは保存されません。 パスワードが保存されていない場合は、必要に応じてパスワードを入力するよう [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] から求められます。 パスワードを保存するように選択した場合、パスワードは暗号化形式でデータ接続文字列に保存されます。 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] では、データ ソースを含むデータベースの暗号化キーを使用して、データ ソースのパスワード情報が暗号化されます。 接続情報が暗号化されている場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 構成マネージャーを使用して、 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] サービス アカウントまたはパスワードを変更する必要があります。これを行わないと、暗号化された情報を復元できません。 詳細については、「 [SQL Server Configuration Manager](../../relational-databases/sql-server-configuration-manager.md)」を参照してください。  
  
### <a name="defining-impersonation-information-for-data-mining-objects"></a>データ マイニング オブジェクト用の権限借用情報の定義  
 データ マイニング クエリは、 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] サービス アカウントのコンテキストで実行できますが、クエリを送信するユーザーのコンテキスト、または指定されたユーザーのコンテキストでも実行できます。 クエリが実行されるコンテキストは、クエリの結果に影響する場合があります。 データ マイニングの `OPENQUERY` 型の演算では、サービス アカウントのコンテキストではなく、現在のユーザーのコンテキスト、または指定されたユーザー (クエリを実行するユーザーにかかわらず) のコンテキストでデータ マイニング クエリを実行できます。 これにより、限られたセキュリティ資格情報でクエリを実行することができます。 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] で現在のユーザーまたは指定されたユーザーの権限を借用するには、 **[特定のユーザー名とパスワードを使用する]** または **[現在のユーザーの資格情報を使用する]** オプションを選択します。  
  
##  <a name="bkmk_steps"></a> データ ソース ウィザードによるデータ ソースの作成  
  
1.  [!INCLUDE[ssBIDevStudio](../../includes/ssbidevstudio-md.md)]で [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] プロジェクトを開くか、またはデータ ソースを定義する [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] データベースに接続します。  
  
2.  **ソリューション エクスプローラー**で **[データ ソース]** フォルダーを右クリックし、 **[新しいデータ ソース]** をクリックして、 **データ ソース ウィザード**を開始します。  
  
3.  **[接続の定義方法を選択します]** ページで **[既存の接続または新しい接続に基づいてデータ ソースを作成する]** を選択し、次に **[新規作成]** をクリックして **接続マネージャー**を開きます。  
  
     新しい接続は、接続マネージャーで作成されます。 接続マネージャーでは、プロバイダーを選択し、そのプロバイダーが基になるデータに接続するために使用する接続文字列プロパティを指定します。 必要な情報は選択したプロバイダーによって異なりますが、通常は、サーバーまたはサービス インスタンス、サーバーまたはサービス インスタンスへのログオン情報、データベース名またはファイル名、その他のプロバイダー固有の設定を指定します。 この手順の残りの部分、SQL Server データベースの接続をものとします。  
  
4.  接続に使用する [!INCLUDE[msCoName](../../includes/msconame-md.md)] .NET Framework またはネイティブ OLE DB プロバイダーを選択します。  
  
     新しい接続の既定のプロバイダーは、ネイティブ OLE DB\\[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] Native Client プロバイダーです。 このプロバイダーは、OLE DB を使用して [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベース エンジンのインスタンスに接続するために使用します。 SQL Server リレーショナル データベースに接続する場合、通常は、Native OLE DB\SQL Server Native Client 11.0 を使用する方が、代替プロバイダーを使用するより高速です。  
  
     他のデータ ソースにアクセスするために、別のプロバイダーを選択できます。 プロバイダーとでサポートされているリレーショナル データベースの一覧については[!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)]を参照してください[サポートされるデータ ソース&#40;SSAS 多次元&#41;](supported-data-sources-ssas-multidimensional.md)します。  
  
5.  選択したプロバイダーから要求される情報を入力して、基になるデータ ソースに接続します。 **[ネイティブ OLE DB\SQL Server Native Client]** プロバイダーを選択した場合は、次の情報を入力します。  
  
    1.  **[サーバー名]** は、データベース エンジン インスタンスのネットワーク名です。 IP アドレス、コンピューターの NETBIOS 名、または完全修飾ドメイン名として指定できます。 場合は、サーバーを名前付きインスタンスとしてインストールすると、インスタンス名を含める必要があります (たとえば、 \<computername >\\< instancename\>)。  
  
    2.  **[サーバー ログオン]** は、接続の認証方法を指定します。 **[Windows 認証を使用する]** では、Windows 認証が使用されます。 **[SQL Server 認証を使用する]** では、Windows Azure SQL データベース、または混合モード認証をサポートする SQL Server インスタンスのデータベース ユーザー ログインを指定します。  
  
        > [!IMPORTANT]  
        >  接続マネージャーには、SQL Server 認証を使用する接続のための **[パスワードを保存する]** チェック ボックスがあります。 チェック ボックスは常に表示されていますが、常に使用されるわけではありません。  
        >   
        >  Analysis Services がこのチェック ボックスを使用しないのは、アクティブな Analysis Services データベースで使用されている SQL Server のリレーショナル データを更新または処理する場合などです。 **[パスワードを保存する]** をオフまたはオンのどちらにするかに関係なく、Analysis Services は常にパスワードを暗号化して保存します。 パスワードは、暗号化されて、.abf ファイルとデータ ファイルの両方に格納されます。 このような動作が行われるのは、Analysis Services がサーバーへのセッションベースのパスワードの保存をサポートしないためです。  
        >   
        >  この動作は、データベースが a) Analysis Services サーバー インスタンスに保存され、b) リレーショナル データを更新または処理するために SQL Server 認証を使用する場合にのみ適用されます。 [!INCLUDE[ssBIDevStudioFull](../../includes/ssbidevstudiofull-md.md)] でセットアップする、セッションの期間内だけ使用されるデータ ソース接続には、適用されません。 既に保存されているパスワードを削除する方法はありませんが、異なる資格情報または Windows 認証を使用して、現在データベースで保存されているユーザー情報を上書きできます。  
  
    3.  データベースを指定するには、 **[データベース名の選択または入力]** または **[データベース ファイルの添付]** を使用します。  
  
    4.  ダイアログ ボックスの左側で **[すべて]** をクリックして、このプロバイダーのすべての既定の設定など、この接続の追加の設定を表示します。  
  
    5.  使用環境に応じて設定を変更し、 **[OK]** をクリックします。  
  
         データ ソース ウィザードの **[接続の定義方法を選択します]** ページにある **[データ接続]** ペインに新しい接続が表示されます。  
  
6.  **[次へ]** をクリックします。  
  
7.  **[権限借用情報]** で、Analysis Services が外部データ ソースに接続する際に使用する Windows 資格情報またはユーザー ID を指定します。 データベース認証を使用する場合、接続でこれらの設定は無視されます。  
  
     権限借用のオプションを選択するためのガイドラインは、データ ソースをどのように使用するかによって異なります。 処理タスクの場合、 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] サービスは、データ ソースへの接続の際に、そのサービス アカウントまたは指定されたユーザー アカウントのいずれかのセキュリティ コンテキストで実行する必要があります。  
  
    -   最小特権資格情報の一意のセットを指定する場合は、 **[特定の Windows ユーザー名とパスワードを使用する]** 。  
  
    -   サービス ID を使用してデータを処理する場合は、 **[サービス アカウントを使用する]** 。  
  
     指定するアカウントは、データ ソースに対する読み取り権限を持っている必要があります。  
  
8.  **[次へ]** をクリックします。  **[ウィザードの完了]** で、データ ソースの名前を入力するか、既定の名前を使用します。 既定の名前は、接続で指定されたデータベース名になっています。 この新しいデータ ソースの接続文字列が **[プレビュー]** ペインに表示されます。  
  
9. **[完了]** をクリックします。  ソリューション エクスプローラーの **[データ ソース]** フォルダーに、新しいデータ ソースが表示されます。  
  
##  <a name="bkmk_connection"></a> 既存の接続を使用したデータ ソースの作成  
 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] プロジェクトで作業する際、データ ソースは、ソリューション内の既存のデータ ソースに基づく場合も、別の [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] プロジェクトに基づく場合もあります。 データ ソース ウィザードには、同じプロジェクトの既存の接続を使用するオプションなど、データ ソース オブジェクトを作成するためのオプションがいくつか用意されています。  
  
-   ソリューション内の既存のデータ ソースに基づくデータ ソースを作成する場合、その既存のデータ ソースに同期されたデータ ソースを定義できます。 この新しいデータ ソースを含んだプロジェクトを構築すると、基になるデータ ソースのデータ ソース設定が使用されます。  
  
-   [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] プロジェクトに基づくデータ ソースを作成する場合、現在のプロジェクト内で、ソリューション内の別の [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] プロジェクトを参照できます。 新しいデータ ソースは、選択したプロジェクトの `Data Source` プロパティと `Initial Catalog` プロパティから取得した `TargetServer` プロパティと `TargetDatabase` プロパティで MSOLAP プロバイダーを使用します。 この機能は、複数の [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] プロジェクトを使用してリモート パーティションを管理しているソリューションに役立ちます。 [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] の元のデータベースと作成するデータベースでは、リモート パーティションのストレージおよび処理をサポートするにあたって同等のデータ ソースが必要になるためです。  
  
 データ ソース オブジェクトを参照する場合は、参照されたオブジェクトまたはプロジェクトでのみそのオブジェクトを編集できます。 参照を含むデータ ソース オブジェクト内で接続情報を編集することはできません。 参照されたオブジェクトまたはプロジェクトでの接続情報の変更は、新しいデータ ソースの構築時にそれに反映されます。 プロジェクトのデータ ソース (.ds) ファイル内の接続文字列情報は、プロジェクトを作成するか、データ ソース デザイナーで参照をクリアしたときに同期されます。  
  
##  <a name="bkmk_ConnectionString"></a> 接続プロパティの表示または編集  
 接続文字列は、データ ソース デザイナーまたは新しいデータ ソース ウィザードで選択したプロパティに基づいて生成されます。 [!INCLUDE[ssBIDevStudio](../../includes/ssbidevstudio-md.md)]で、接続文字列やその他のプロパティを表示できます。  
  
 **接続文字列を編集するには**  
  
1.  [!INCLUDE[ssBIDevStudio](../../includes/ssbidevstudio-md.md)]のソリューション エクスプローラーで、データ ソース オブジェクトをダブルクリックします。  
  
2.  **[編集]** をクリックし、左側のナビゲーション ウィンドウで **[すべて]** をクリックします。  
  
3.  プロパティ グリッドが表示され、使用しているデータ プロバイダーの使用可能なプロパティが示されます。 これらのプロパティの詳細については、プロバイダーの製品ドキュメントを参照してください。  SQL Server Native Client については、「 [SQL Server Native Client での接続文字列キーワードの使用](../../relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client.md)」を参照してください。  
  
 ソリューションに複数のデータ ソース オブジェクトがあり、接続文字列を 1 か所に維持する場合、他のデータ ソース オブジェクトを参照するように現在のデータ ソースを構成できます。  
  
 *データ ソースの参照* は、同じソリューション内の別の [!INCLUDE[ssASnoversion](../../includes/ssasnoversion-md.md)] プロジェクトまたはデータ ソースへの関連付けです。 参照は、ソリューション内のオブジェクト間でデータ ソースを同期する手段を提供します。 接続文字列情報はプロジェクトをビルドするたびに同期されます。 別のオブジェクトを参照するデータ ソースの接続文字列を変更するには、参照先のオブジェクトの接続文字列を変更する必要があります。  
  
 チェック ボックスをオフにして、参照を削除できます。 これにより、オブジェクト間の同期が終了し、データ ソースの接続文字列を変更できます。  
  
##  <a name="bkmk_multipleDS"></a> モデルへの複数のデータ ソースの追加  
 追加のデータ ソースへの接続をサポートするために、複数のデータ ソース オブジェクトを作成できます。 各データ ソースには、リレーションシップを作成するために使用できる列が必要です。  
  
> [!NOTE]  
>  複数のデータ ソースが定義され、1 つのクエリで複数のソースからデータがクエリされた場合など、スノーフレーク ディメンションの必要がありますソースを定義するデータを使用してリモート クエリをサポートする`OpenRowset`します。 通常、これは Microsoft SQL Server データ ソースになります。  
  
 複数のデータ ソースを使用するための要件には、次のようなものがあります。  
  
-   プライマリ データ ソースとして、1 つのデータ ソースを指定します。 プライマリ データ ソースは、データ ソース ビューを作成するために使用されるデータ ソースです。  
  
-   プライマリ データ ソースは、`OpenRowset` 関数をサポートしている必要があります。  SQL Server のこの関数の詳細については、「 <xref:Microsoft.SqlServer.TransactSql.ScriptDom.TSqlTokenType.OpenRowSet>」を参照してください。  
  
 複数のデータ ソースのデータを結合するには、次の方法を使用します。  
  
1.  モデルにデータ ソースを作成します。  
  
2.  データ ソースとして SQL Server のリレーショナル データベースを使用して、データ ソース ビューを作成します。 これがプライマリ データ ソースになります。  
  
3.  データ ソース ビュー デザイナーで、作成したデータ ソース ビューを使用して、作業領域の任意の場所を右クリックし、 **[テーブルの追加と削除]** を選択します。  
  
4.  2 番目のデータ ソースを選択し、追加するテーブルを選択します。  
  
5.  追加したテーブルを探し、選択します。 テーブルを右クリックし、 **[新しいリレーションシップ]** を選択します。 一致するデータを含む、基になる列と対象になる列を選択します。  
  
## <a name="see-also"></a>関連項目  
 [サポートされるデータ ソース&#40;SSAS 多次元&#41;](supported-data-sources-ssas-multidimensional.md)   
 [多次元モデルのデータ ソース ビュー](data-source-views-in-multidimensional-models.md)  
  
  
