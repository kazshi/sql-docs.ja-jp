---
title: BACPAC ファイルのインポートによる新しいユーザー データベースの作成 | Microsoft Docs
ms.custom: ''
ms.date: 06/13/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology: ''
ms.topic: conceptual
f1_keywords:
- sql12.swb.importdac.progress.f1
- sql12.swb. importdac.settings.f1
- sql12.swb.importdac.storagebrowser.f1
- sql12.swb. importdac.summary.f1
- sql12.swb.importdac.results.f1
- sql12.swb. importdac.progress.f1
- sql12.swb.importdac.welcome.f1
- sql12.swb.importdac.settings.f1
- sql12.swb. importdac.results.f1
- sql12.swb.importdac.summary.f1
helpviewer_keywords:
- Data-tier application
- SQL Server DAC
- Migrate database
- DAC
ms.assetid: 736d8d9a-39f1-4bf8-b81f-2e56c134d12e
author: stevestein
ms.author: sstein
manager: craigg
ms.openlocfilehash: 12e5d699615018c2d9e20a8fd49953931850a106
ms.sourcegitcommit: 3026c22b7fba19059a769ea5f367c4f51efaf286
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2019
ms.locfileid: "62918186"
---
# <a name="import-a-bacpac-file-to-create-a-new-user-database"></a>BACPAC ファイルのインポートによる新しいユーザー データベースの作成
  データ層アプリケーション (DAC) ファイル (.bacpac ファイル) をインポートすると、データを含んだ元のデータベースのコピーを、[!INCLUDE[ssDE](../../includes/ssde-md.md)] の新しいインスタンス上または [!INCLUDE[ssSDSfull](../../includes/sssdsfull-md.md)] に作成することができます。 エクスポートとインポートという操作を組み合わせることで、DAC またはデータベースをインスタンス間で移行したり論理バックアップを作成したりすることが可能です。たとえば、 [!INCLUDE[ssSDS](../../includes/sssds-md.md)]に配置されているデータベースの社内用コピーを作成することもできます。  
  
## <a name="before-you-begin"></a>はじめに  
 インポート プロセスでは、2 つの段階を経て新しい DAC が構築されます。  
  
1.  エクスポート ファイルに格納されている DAC 定義を使用して、新しい DAC および関連するデータベースを作成します。DAC の配置時には、DAC パッケージ ファイル内の定義から新しい DAC が作成されますが、その場合と同じ方法で作成されます。  
  
2.  エクスポート ファイルからデータを一括コピーします。  
  
 
## <a name="sql-server-utility"></a>SQL Server ユーティリティ (SQL Server Utility)  
 データベース エンジンのマネージド インスタンスに DAC をインポートした場合、そのインポートした DAC は、次回ユーティリティ コレクション セットがインスタンスからユーティリティ コントロール ポイントへと送信されるときに SQL Server ユーティリティに組み込まれます。 その後、DAC は、 **の** ユーティリティ エクスプローラー [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] **Utility Explorer** and reported in the **の** details page.  
  
## <a name="database-options-and-settings"></a>データベースのオプションと設定  
 既定では、インポート時に作成されたデータベースには、CREATE DATABASE ステートメントによる既定の設定がすべて適用されます。ただし、データベースの照合順序および互換性レベルは、DAC のエクスポート ファイルで定義された値に設定されます。 DAC のエクスポート ファイルには、元のデータベースに基づく値が使用されます。  
  
 TRUSTWORTHY、DB_CHAINING、HONOR_BROKER_PRIORITY など、データベース オプションによっては、インポート作業中の調整はできない場合があります。 ファイル グループの数、ファイルの数やサイズなどの物理プロパティは、インポート作業中に変更することはできません。 インポートが完了すれば、ALTER DATABASE ステートメント、 [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)]、または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] PowerShell を使用して、データベースを調整できます。 詳細については、「 [Databases](../databases/databases.md)」を参照してください。  
  
## <a name="limitations-and-restrictions"></a>制限事項と制約事項  
 DAC は、 [!INCLUDE[ssSDS](../../includes/sssds-md.md)]にインポートできるほか、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] Service Pack 4 (SP4) 以降を実行する [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] のインスタンスにインポートすることができます。 新しいバージョンから DAC をエクスポートした場合、 [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)]ではサポートされないオブジェクトが DAC に含まれている可能性があります。 このような DAC を [!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)]のインスタンスに配置することはできません。  
  
## <a name="prerequisites"></a>前提条件  
 ソースが不明または信頼されていない DAC エクスポート ファイルはインポートしないことをお勧めします。 こうしたファイルには、意図しない Transact-SQL コードを実行したり、スキーマを変更してエラーを発生させるような、悪意のあるコードが含まれている可能性があります。 エクスポート ファイルのソースが不明または信頼されていない場合は、使用する前に、DAC をアンパックして、ストアド プロシージャやその他のユーザー定義コードなどのコードも確認してください。 これらのチェックの実行方法の詳細については、「 [Validate a DAC Package](validate-a-dac-package.md)」をご覧ください。  
  
## <a name="security"></a>セキュリティ  
 セキュリティを強化するために、SQL Server 認証のログインは、パスワードなしで DAC エクスポート ファイルに格納されます。 ファイルがインポートされると、ログインは、生成されたパスワードを伴う無効なログインとして作成されます。 ログインを有効にするには、ALTER ANY LOGIN 権限を持つユーザーとしてログインし、ALTER LOGIN を使用してログインを有効にします。さらに、新しいパスワードを割り当て、そのパスワードを該当ユーザーに通知します。 Windows 認証ログインの場合、ログインのパスワードは SQL Server で管理されていないため、この操作は必要ありません。  
  
## <a name="permissions"></a>アクセス許可  
 DAC をインポートできるのは、 **sysadmin** または **serveradmin** 固定サーバー ロールのメンバーか、 **dbcreator** 固定サーバー ロールに存在する ALTER ANY LOGIN 権限を持つログインのみです。 あらかじめ登録された [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] システム管理者アカウント ( **sa** ) も DAC をインポートできます。 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] へのログインが含まれる DAC をインポートするには、loginmanager ロールまたは serveradmin ロールのメンバーシップが必要です。 [!INCLUDE[ssSDS](../../includes/sssds-md.md)] へのログインが含まれない DAC をインポートするには、dbmanager ロールまたは serveradmin ロールのメンバーシップが必要です。  
  
## <a name="using-the-import-data-tier-application-wizard"></a>データ層アプリケーションのインポート ウィザードの使用  
 **ウィザードを起動するには、次の手順を実行します。**  
  
1.  内部設置型または [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]内で、 [!INCLUDE[ssSDS](../../includes/sssds-md.md)]のインスタンスに接続します。  
  
2.  **オブジェクト エクスプローラー**で、 **[データベース]** を右クリックしてから、 **[データ層アプリケーションのインポート]** メニュー項目を選択してウィザードを起動します。  
  
3.  ウィザードの各ダイアログの手順を実行します。  
  
    -   [[説明] ページ](#Introduction)  
  
    -   [[インポートの設定] ページ](#Import_settings)  
  
    -   [[データベースの設定] ページ](#Database_settings)  
  
    -   [[概要] ページ](#Summary)  
  
    -   [[進行状況] ページ](#Progress)  
  
    -   [[結果] ページ](#Results)  
  
###  <a name="Introduction"></a> [説明] ページ  
 このページには、データ層アプリケーションのインポート ウィザードの手順が表示されます。  
  
 **オプション**  
  
-   **[次回からこのページを表示しない]** : 今後 [説明] ページを表示しないようにするには、このチェック ボックスをオンにします。  
  
-   **[次へ]** : **[インポートの設定]** ページに進みます。  
  
-   **[キャンセル]** : 操作を取り消し、ウィザードを閉じます。  
  
###  <a name="Import_settings"></a> [インポートの設定] ページ  
 このページを使用して、インポートする .bacpac ファイルの場所を指定します。  
  
-   **[ローカル ディスクからインポート]** : **[参照]** をクリックしてローカル コンピューター内を参照するか、用意されている領域にパスを指定します。 パス名には、ファイル名および .bacpac 拡張子を含める必要があります。  
  
-   **Windows Azure からインポート**-Windows Azure コンテナーから BACPAC ファイルをインポートします。 このオプションを検証するためには、Windows Azure コンテナーに接続する必要があります。 このオプションでは、一時ファイル用のローカル ディレクトリを指定する必要もあります。 一時ファイルは、指定した場所に作成され、操作の完了後も残ります。  
  
     Windows Azure を参照するときに、1 つのアカウント内のコンテナーを切り替えることができます。 インポート操作を続行するには、1 つの .bacpac ファイルを指定する必要があります。 列は、 **名前**、 **サイズ**、または **更新日時**で並べ替えることができます。  
  
     続行するには、インポートする .bacpac ファイルを指定し、 **[開く]** をクリックします。  
  
###  <a name="Database_settings"></a> [データベースの設定] ページ  
 このページを使用すると、作成されるデータベースの詳細を指定できます。  
  
 **SQLServer のローカル インスタンスの場合**  
  
-   **[新しいデータベース名]** : インポートするデータベースの名前を指定します。  
  
-   **[データ ファイルのパス]** : データ ファイル用のローカル ディレクトリを指定します。 **[参照]** をクリックしてローカル コンピューター内を参照するか、用意されている領域にパスを指定します。  
  
-   **[ログ ファイルのパス]** : ログ ファイル用のローカル ディレクトリを指定します。 **[参照]** をクリックしてローカル コンピューター内を参照するか、用意されている領域にパスを指定します。  
  
 続行するには、 **[次へ]** をクリックします。  
  
 **SQL database の場合。**  
  
-   **[新しいデータベース名]** : インポートするデータベースの名前を指定します。  
  
-   **エディションの[!INCLUDE[ssSDS](../../includes/sssds-md.md)]**  -指定[!INCLUDE[ssSDS](../../includes/sssds-md.md)]ビジネスまたは[!INCLUDE[ssSDS](../../includes/sssds-md.md)]Web です。 [!INCLUDE[ssSDS](../../includes/sssds-md.md)]のエディションの詳細については、Web サイト「 [SQL データベース](http://www.windowsazure.com/home/tour/database/) 」を参照してください。  
  
-   **データベースの最大サイズ (GB)** -ドロップダウン メニューを使用して、データベースの最大サイズを指定します。  
  
 続行するには、 **[次へ]** をクリックします。  
  
### <a name="validation-page"></a>[検証] ページ  
 このページを使用して、操作の妨げとなる問題を確認します。 続行するには、妨げとなる問題を解決し、 **[検証の再実行]** をクリックして、検証が成功したことを確認します。  
  
 続行するには、 **[次へ]** をクリックします。  
  
###  <a name="Summary"></a> [概要] ページ  
 このページを使用すると、操作の指定ソースとターゲットの設定を確認できます。 指定した設定でインポート操作を実行するには、 **[完了]** をクリックします。 インポート操作をキャンセルしてウィザードを終了するには、 **[キャンセル]** をクリックします。  
  
###  <a name="Progress"></a> [進行状況] ページ  
 このページには、操作の進行状況を示す進行状況バーが表示されます。 詳細な状態を表示するには、 **[詳細表示]** をクリックします。  
  
 続行するには、 **[次へ]** をクリックします。  
  
###  <a name="Results"></a> [結果] ページ  
 このページでは、データベースのインポートや作成操作の成功と失敗が報告され、各アクションの成功または失敗が示されます。 エラーが発生したアクションには、 **[結果]** 列にリンクが表示されます。 そのアクションのエラーのレポートを表示するには、リンクをクリックします。  
  
 **[閉じる]** をクリックしてウィザードを閉じます。  
  
## <a name="see-also"></a>関連項目  
 [[データ層アプリケーション]](data-tier-applications.md)   
 [データ層アプリケーションのエクスポート](export-a-data-tier-application.md)  
  
  
