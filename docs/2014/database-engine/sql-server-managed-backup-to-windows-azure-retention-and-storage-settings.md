---
title: Windows Azure へのマネージバックアップの SQL Server-保有期間とストレージの設定 |Microsoft Docs
ms.custom: ''
ms.date: 08/23/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology: backup-restore
ms.topic: conceptual
ms.assetid: c4aa26ea-5465-40cc-8b83-f50603cb9db1
author: mashamsft
ms.author: mathoma
manager: craigg
ms.openlocfilehash: c1d7949fb3077204d6f05331ac29fa03b8caaa4f
ms.sourcegitcommit: 3be14342afd792ff201166e6daccc529c767f02b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68307567"
---
# <a name="sql-server-managed-backup-to-windows-azure---retention-and-storage-settings"></a>Windows Azure への SQL Server マネージド バックアップ - 保有期間とストレージの設定
  このトピックでは、データベースの [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] を構成する基本手順と、インスタンスの既定の設定を構成する手順について説明します。 また、インスタンスの [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] サービスを一時停止して再開するために必要な手順についても説明します。  
  
 セットアップ[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]の完全なチュートリアルについては、「 [windows azure への管理](../relational-databases/backup-restore/enable-sql-server-managed-backup-to-microsoft-azure.md)されたバックアップ SQL Server のセットアップ」 SQL Server と「 [windows azure への管理](../../2014/database-engine/setting-up-sql-server-managed-backup-to-windows-azure-for-availability-groups.md)されたバックアップのセットアップ」を参照してください。  
  
 
  
##  <a name="BeforeYouBegin"></a> はじめに  
  
###  <a name="Restrictions"></a> 制限事項と制約事項  
  
-   現在メンテナンス プランまたはログ配布を使用しているデータベースで [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] を有効にしないでください。 他の SQL Server 機能との相互運用性と共存の詳細に[ついては、「Windows Azure へのマネージバックアップの SQL Server」を参照してください。相互運用性と共存](../../2014/database-engine/sql-server-managed-backup-to-windows-azure-interoperability-and-coexistence.md)  
  
###  <a name="Prerequisites"></a> 前提条件  
  
-   SQL Server エージェントを実行している必要があります。  
  
    > [!WARNING]  
    >  SQL Server エージェントが一定期間停止された後に再起動された場合、SQL エージェントの停止から起動までの経過時間によっては、バックアップ アクティビティが増加する可能性があり、実行を待機しているログ バックアップのバックログが存在することがあります。 起動時に SQL Server エージェントが自動的に開始されるように構成することを検討してください。  
  
-   [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]を構成する前に、Windows Azure ストレージ アカウントと、認証情報をストレージ アカウントに格納する SQL 資格情報の両方を作成しておく必要があります。 詳細については、「 **SQL Server Backup to URL** 」の「[主要なコンポーネントと概念](../relational-databases/backup-restore/sql-server-backup-to-url.md#intorkeyconcepts)の[概要」、および「レッスン 2:SQL Server 資格情報](../../2014/tutorials/lesson-2-create-a-sql-server-credential.md)を作成します。  
  
    > [!IMPORTANT]  
    >  [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]は、バックアップを格納するために必要なコンテナーを作成します。 コンテナー名は、"コンピューター名-インスタンス名" の形式を使用して作成されます。 AlwaysOn 可用性グループの場合、コンテナーの名前には、可用性グループの GUID が使用されます。  
  
###  <a name="Security"></a> セキュリティ  
  
####  <a name="Permissions"></a> Permissions  
 [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]を有効にするストアドプロシージャを実行するに`System Administrator`は、 **ALTER ANY CREDENTIAL**権限を`EXECUTE`持つまたは**db_backupoperator**データベースロールのメンバーであるか、 **sp_delete_ に対する権限である必要があります。backuphistory**、および`smart_admin.sp_backup_master_switch`ストアドプロシージャ。  既存の設定を確認するために使用するストアド プロシージャと関数には、通常、ストアド プロシージャに対する `Execute` 権限と、関数に対する `Select` 権限がそれぞれ必要です。  
  

  
###  <a name="Considerations"></a>データベースとインスタンス[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]に対してを有効にする場合の考慮事項  
 [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]は、個々のデータベースに対して個別に有効にするか、インスタンス全体に対して有効にすることができます。 どちらを選択するかは、インスタンスにおけるデータベースの復旧要件、複数データベースおよびインスタンスの管理要件、および Windows Azure ストレージの戦略的な使用によって異なります。  
  
#### <a name="enabling-includesssmartbackupincludesss-smartbackup-mdmd-at-the-database-level"></a>データベース レベルで [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] を有効にする  
 インスタンス上の他のデータベースとは異なる、バックアップと保有期間 (復旧 SLA) の特定の要件があるデータベースの場合は、このデータベースに対して、データベース レベルで [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] を構成します。 データベース レベルの設定は、インスタンス レベルの構成設定をオーバーライドします。 ただし、これらのオプションは同じインスタンスで一緒に使用することができます。 [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] をデータベース レベルで有効にした場合の利点と注意点を次に示します。  
  
-   より詳細な方法:各データベースの構成設定を分割します。 個々のデータベースに対して異なる保有期間をサポートできます。  
  
-   データベースのインスタンス レベルの設定をオーバーライドします。  
  
-   バックアップするデータベースを個別に選択することで、ストレージ コストを削減できます。  
  
-   各データベースの管理が必要です。  
  
#### <a name="enabling-includesssmartbackupincludesss-smartbackup-mdmd-at-the-instance-level-with-default-settings"></a>既定の設定を使用してインスタンス レベルで [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] を有効にする  
 インスタンス内のほとんどのデータベースでバックアップと保有ポリシーの要件が同じ場合、または新しいデータベース インスタンスが作成時に自動的にバックアップされるようにする場合は、この設定を使用します。 ポリシーの例外となる少数のデータベースを個別に構成することもできます。 [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] をインスタンス レベルで有効にした場合の利点と注意点を次に示します。  
  
-   インスタンスレベルでのオートメーション:後で追加される新しいデータベースに対して自動的に適用される共通設定。  
  
-   新しいデータベースは、インスタンスに作成されるとすぐに自動的にバックアップされます。  
  
-   保有期間の要件が同じであるデータベースに適用できます。  
  
-   既定の設定を使用してインスタンス レベルで [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] のバックアップが有効になっている場合でも、別の保有期間を必要とするデータベースを個別に構成できます。 バックアップに Windows Azure ストレージを使用しない場合は、データベースについて [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] を無効にすることもできます。  
  
##  <a name="DatabaseConfigure"></a>データベースに対し[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]てを有効にして構成する  
 特定のデータベースについて [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]を有効にするには、システム ストアド プロシージャ `smart_admin.sp_set_db_backup` を使用します。 データベースについて初めて [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] を有効にする場合は、 [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]の有効化に加えて、次の情報の指定が必要です。  
  
-   データベースの名前。  
  
-   保有期間。  
  
-   Windows Azure ストレージ アカウントへの認証に使用する SQL 資格情報。  
  
-   *@encryption_algorithm* **NO_ENCRYPTION**を使用して = 暗号化しないように指定するか、サポートされている暗号化アルゴリズムを指定してください。 暗号化の詳細については、「 [Backup Encryption](../relational-databases/backup-restore/backup-encryption.md)」を参照してください。  
  
 データベース レベル構成の [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]は、Transact-SQL でのみサポートされます。  
  
 データベースで [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] が有効になると、この情報は保持されます。 構成を変更する場合は、データベース名と変更する設定のみが必要です。他のパラメーターについては、指定がなければ、 [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] では既存値がそのまま使用されます。  
  
> [!IMPORTANT]  
>  データベースで [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] を構成する前に、既存の構成 (ある場合) が役に立つことがあります。 データベースの構成設定を確認する手順については、後で説明します。  
  
-   **Transact-sql の使用:**  
  
     を初めて有効[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]にする場合、必要なパラメーター *@database_name* は、 *@enable_backup* *@credential_name* *@encryption_algorithm* *@storage_url* 、、です。このパラメーターは省略可能です。 @storage_urlパラメーターの値を指定しない場合、値は SQL 資格情報のストレージアカウント情報を使用して取得されます。 ストレージ URL を指定する場合、指定する必要があるのはストレージ アカウントのルート URL のみです。この値は、指定した SQL 資格情報と一致する必要があります。  
  
    1.  [!INCLUDE[ssDE](../includes/ssde-md.md)]に接続します。  
  
    2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
    3.  次の例をコピーし、クエリウィンドウに貼り付け`Execute`て、をクリックします。 この例で[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]は、データベース ' TestDB ' に対してを有効にします。 保有期間は 30 日に設定されています。 このサンプルでは、暗号化アルゴリズムおよび暗号化機能情報を指定して暗号化オプションを使用します。  
  
    ```  
    Use msdb;  
    GO  
    EXEC smart_admin.sp_set_db_backup   
                    @database_name='TestDB'   
                    ,@enable_backup=1  
                    ,@retention_days =30   
                    ,@credential_name ='MyCredential'  
                    ,@encryption_algorithm ='AES_256'  
                    ,@encryptor_type= 'Certificate'  
                    ,@encryptor_name='MyBackupCert'  
    GO  
  
    ```  
  
    > [!IMPORTANT]  
    >  保有期間には、1 ～ 30 日までの任意の値を設定できます。  
    >   
    >  暗号化に使用する証明書の作成の詳細については、「 [Create an Encrypted Backup](../relational-databases/backup-restore/create-an-encrypted-backup.md)」の「バックアップ証明書の作成」を参照してください。  
  
     このストアドプロシージャの詳細については、「smart_admin」を参照してください[ &#40;。&#41; ](https://msdn.microsoft.com/library/dn451013(v=sql.120).aspx)  
  
     データベースの構成設定を確認するには、次のクエリを使用します。  
  
    ```  
    Use msdb  
    GO  
    SELECT * FROM smart_admin.fn_backup_db_config('TestDB')  
    ```  
  
##  <a name="InstanceConfigure"></a>インスタンスの既定[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]の設定を有効にして構成する  
 インスタンスレベルで既定[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]の設定を有効にして構成するには、次の2つの方法があります。システムストアドプロシージャ`smart_admin.set_instance_backup`または**SQL Server Management Studio**を使用する。 これら 2 つの方法について以下で説明します。  
  
 **smart_admin (_s): を設定し**ます。 パラメーターに *@enable_backup* 値**1**を指定することによって、バックアップを有効にし、既定の構成を設定することができます。 これらの既定の設定は、インスタンス レベルで適用すると、このインスタンスに追加されるすべての新しいデータベースに適用されます。  初めて [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] を有効にする場合は、インスタンスでの [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] の有効化に加えて、次の情報の指定が必要です。  
  
-   保有期間。  
  
-   Windows Azure ストレージ アカウントへの認証に使用する SQL 資格情報。  
  
-   暗号化オプション。 *@encryption_algorithm* **NO_ENCRYPTION**を使用して = 暗号化しないように指定するか、サポートされている暗号化アルゴリズムを指定してください。 暗号化の詳細については、「 [Backup Encryption](../relational-databases/backup-restore/backup-encryption.md)」を参照してください。  
  
 有効にすると、これらの設定は保持されます。 構成を変更する場合は、データベース名と変更する設定のみが必要です。 指定がなければ、[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]では既存値がそのまま使用されます。  
  
> [!IMPORTANT]  
>  インスタンスで [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] を構成する前に、既存の構成 (ある場合) を確認すると役に立つことがあります。 データベースの構成設定を確認する手順については、後で説明します。  
  
 **SQL Server Management Studio:** このタスクを SQL Server Management Studio で実行するには、オブジェクトエクスプローラーで **[管理]** ノードを展開し、 **[マネージバックアップ]** を右クリックします。 **[構成]** を選択します。 **[マネージド バックアップ]** ダイアログ ボックスが開きます。 このダイアログ ボックスを使用して、保持期間、SQL 資格情報、ストレージ URL、暗号化の設定を指定します。 このダイアログの具体的なヘルプについては、「 [Configure Managed Backup &#40;&#41;SQL Server Management Studio](configure-managed-backup-sql-server-management-studio.md)」を参照してください。  
  
#### <a name="using-transact-sql"></a>Transact-SQL の使用  
  
1.  [!INCLUDE[ssDE](../includes/ssde-md.md)]に接続します。  
  
2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次の例をコピーし、クエリウィンドウに貼り付け`Execute`て、をクリックします。  
  
```  
Use msdb;  
Go  
   EXEC smart_admin.sp_set_instance_backup  
                @retention_days=30   
                ,@credential_name='sqlbackuptoURL'  
                ,@encryption_algorithm ='AES_128'  
                ,@encryptor_type= 'Certificate'  
                ,@encryptor_name='MyBackupCert'  
                ,@enable_backup=1;  
GO  
  
```  
  
> [!IMPORTANT]  
>  保有期間には、1 ～ 30 日までの任意の値を設定できます。  
>   
>  暗号化に使用する証明書の作成の詳細については、「 [Create an Encrypted Backup](../relational-databases/backup-restore/create-an-encrypted-backup.md)」の「バックアップ証明書の作成」を参照してください。  
  
 インスタンスの既定の構成設定を確認するには、次のクエリを使用します。  
  
```  
Use msdb;  
GO  
SELECT * FROM smart_admin.fn_backup_instance_config ();  
  
```  
  
#### <a name="using-powershell"></a>PowerShell の使用  
  
1.  PowerShell インスタンスを起動します。  
  
2.  次のスクリプトを設定に合わせて変更してから実行します。  
  
    ```  
    C:\ PS> cd SQLSERVER:\SQL\Computer\MyInstance   
    $encryptionOption = New-SqlBackupEncryptionOption -EncryptionAlgorithm Aes128 -EncryptorType ServerCertificate -EncryptorName "MyBackupCert"  
    Get-SqlSmartAdmin | Set-SqlSmartAdmin -BackupEnabled $True -BackupRetentionPeriodInDays 10 -EncryptionOption $encryptionOption  
    ```  
  
> [!IMPORTANT]  
>  既定の設定を構成した後に新しいデータベースを作成すると、データベースが既定の設定で構成されるまで最大 15 分かかる場合があります。 これは、復旧モデルが **Simple** から **Full** または **Bulk-Logged** に変更されるデータベースにも適用されます。  
  
##  <a name="DatabaseDisable"></a> データベースに対して [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] を無効にする  
 `sp_set_db_backup`システムストアドプロシージャ[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]を使用して設定を無効にすることができます。 は *@enableparameter* 、特定のデータベースの構成[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]を有効または無効にするために使用されます。1に設定すると、構成設定が無効になります。  
  
#### <a name="to-disable-includesssmartbackupincludesss-smartbackup-mdmd-for-a-specific-database"></a>特定のデータベースに対して [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] を無効にするには  
  
1.  [!INCLUDE[ssDE](../includes/ssde-md.md)]に接続します。  
  
2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次の例をコピーし、クエリウィンドウに貼り付け`Execute`て、をクリックします。  
  
```  
Use msdb;  
Go  
EXEC smart_admin.sp_set_db_backup   
                @database_name='TestDB'   
                ,@enable_backup=0;  
GO  
  
```  
  
##  <a name="DatabaseAllDisable"></a> インスタンス上のすべてのデータベースについて [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] を無効にする  
 インスタンスで現在 [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] が有効になっているすべてのデータベースから [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] の構成設定を無効にするための手順を次に示します。  ストレージ URL、保有期間、SQL 資格情報などの構成設定はメタデータに残るため、後で [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] をデータベースに対して有効にした場合に使用できます。 [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] サービスを一時的に停止するだけの場合は、このトピックの以降のセクションで説明するマスターの切り替えを使用できます。  
  
#### <a name="to-disable-includesssmartbackupincludesss-smartbackup-mdmdfor-all-the-databases"></a>すべてのデータベースについて [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]を無効にするには:  
  
1.  [!INCLUDE[ssDE](../includes/ssde-md.md)]に接続します。  
  
2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次の例をコピーし、クエリウィンドウに貼り付け`Execute`て、をクリックします。 次の例では、[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]がインスタンス レベルで構成されているかどうかと、インスタンス上のすべての [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]対応データベースを識別し、システム ストアド プロシージャ `sp_set_db_backup` を実行して [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]を無効にします。  
  
```  
-- Create a working table to store the database names  
Declare @DBNames TABLE  
  
       (  
             RowID int IDENTITY PRIMARY KEY  
             ,DBName varchar(500)  
  
       )  
-- Define the variables  
DECLARE @rowid int  
DECLARE @dbname varchar(500)  
DECLARE @SQL varchar(2000)  
-- Get the database names from the system function  
  
INSERT INTO @DBNames (DBName)  
  
SELECT db_name  
       FROM   
  
       smart_admin.fn_backup_db_config (NULL)  
       WHERE is_smart_backup_enabled = 1  
  
       --Select DBName from @DBNames  
  
       select @rowid = min(RowID)  
       FROM @DBNames  
  
       WHILE @rowID IS NOT NULL  
       Begin  
  
             Set @dbname = (Select DBName From @DBNames Where RowID = @rowid)  
             Begin  
             Set @SQL = 'EXEC smart_admin.sp_set_db_backup    
                @database_name= '''+'' + @dbname+ ''+''',   
                @enable_backup=0'  
  
            EXECUTE (@SQL)  
  
             END  
             Select @rowid = min(RowID)  
             From @DBNames Where RowID > @rowid  
  
       END  
  
```  
  
 インスタンスのすべてのデータベースの構成設定を確認するには、次のクエリを使用します。  
  
```  
Use msdb;  
GO  
SELECT * FROM smart_admin.fn_backup_db_config (NULL);  
GO  
  
```  
  
##  <a name="InstanceDisable"></a> インスタンスについて [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] の既定設定を無効にする  
 インスタンス レベルの既定の設定は、そのインスタンスに作成されたすべての新しいデータベースに適用されます。  既定の設定が必要なくなった場合は、 **smart_admin.sp_set_instance_backup** システム ストアド プロシージャを使用してこの構成を無効にすることができます。 無効にしても、ストレージ URL、保有期間の設定、SQL 資格情報名など、その他の構成設定は削除されません。 これらの設定は、後でインスタンスに対して [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] を有効にする場合に使用されます。  
  
#### <a name="to-disable-includesssmartbackupincludesss-smartbackup-mdmd-default-configuration-settings"></a>[!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] の既定の構成設定を無効にするには  
  
1.  [!INCLUDE[ssDE](../includes/ssde-md.md)]に接続します。  
  
2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次の例をコピーし、クエリウィンドウに貼り付け`Execute`て、をクリックします。  
  
    ```  
    Use msdb;  
    Go  
    EXEC smart_admin.sp_set_instance_backup  
                    @enable_backup=0;  
    GO  
  
    ```  
  
#### <a name="using-powershell"></a>PowerShell の使用  
  
1.  PowerShell インスタンスを起動します。  
  
2.  次のスクリプトを実行します。  
  
    ```  
    C:\ PS> cd SQLSERVER:\SQL\Computer\MyInstance   
    Set-SqlSmartAdmin -BackupEnabled $False  
    ```  
  
##  <a name="InstancePause"></a> インスタンス レベルで [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] を一時停止する  
 一時的に少しの間 [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] サービスを停止する必要が生じる場合があります。  `smart_admin.sp_backup_master_switch` システム ストアド プロシージャを使用すると、インスタンス レベルで [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] サービスを無効にできます。  [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)]を再開するには、同じストアド プロシージャを使用します。 [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] を無効にするか有効にするかを定義するには、@state パラメーターが使用されます。  
  
#### <a name="to-pause-includesssmartbackupincludesss-smartbackup-mdmd-services-using-transact-sql"></a>Transact-SQL を使用して [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] サービスを一時停止するには  
  
1.  [!INCLUDE[ssDE](../includes/ssde-md.md)]に接続します。  
  
2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次の例をコピーし、クエリウィンドウに貼り付けてから、[] をクリックします。`Execute`  
  
```  
Use msdb;  
GO  
EXEC smart_admin.sp_backup_master_switch @new_state=0;  
Go  
  
```  
  
#### <a name="to-pause-includesssmartbackupincludesss-smartbackup-mdmd-using-powershell"></a>PowerShell を使用して [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] を一時停止するには  
  
1.  PowerShell インスタンスを起動します。  
  
2.  次のスクリプトを設定に合わせて変更してから実行します。  
  
    ```  
    C:\ PS> cd SQLSERVER:\SQL\Computer\MyInstance   
    Get-SqlSmartAdmin | Set-SqlSmartAdmin -MasterSwitch $False  
    ```  
  
#### <a name="to-resume-includesssmartbackupincludesss-smartbackup-mdmd-using-transact-sql"></a>Transact-SQL を使用して [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] を再開するには  
  
1.  [!INCLUDE[ssDE](../includes/ssde-md.md)]に接続します。  
  
2.  [標準] ツール バーの **[新しいクエリ]** をクリックします。  
  
3.  次の例をコピーし、クエリウィンドウに貼り付けて`Execute`、をクリックします。  
  
```  
Use msdb;  
Go  
EXEC smart_admin. sp_backup_master_switch @new_state=1;  
GO  
  
```  
  
#### <a name="to-resume-includesssmartbackupincludesss-smartbackup-mdmd-using-powershell"></a>PowerShell を使用して [!INCLUDE[ss_smartbackup](../includes/ss-smartbackup-md.md)] を再開するには  
  
1.  PowerShell インスタンスを起動します。  
  
2.  次のスクリプトを設定に合わせて変更してから実行します。  
  
    ```  
    C:\ PS> cd SQLSERVER:\SQL\Computer\MyInstance   
    Get-SqlSmartAdmin | Set-SqlSmartAdmin -MasterSwitch $True  
    ```  
  
  
