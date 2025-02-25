---
title: バックアップ デバイス [SQL Server] | Microsoft Docs
ms.custom: ''
ms.date: 06/13/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology: backup-restore
ms.topic: conceptual
helpviewer_keywords:
- tape backup devices, about tape backup devices
- backup devices [SQL Server]
- disk backup devices [SQL Server]
- database backups [SQL Server], backup devices
- logical devices [SQL Server]
- backup devices [SQL Server], about backup devices
- backing up [SQL Server], backup devices
- removable media [SQL Server]
- network shares [SQL Server]
- backups [SQL Server], backup devices
- tape backup devices
- physical devices [SQL Server]
- backing up databases [SQL Server], backup devices
- devices [SQL Server]
ms.assetid: 35a8e100-3ff2-4844-a5da-dd088c43cba4
author: MikeRayMSFT
ms.author: mikeray
manager: craigg
ms.openlocfilehash: 7cd01f1a3c98bcf0d67ab0224772538a7a82514d
ms.sourcegitcommit: 3026c22b7fba19059a769ea5f367c4f51efaf286
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2019
ms.locfileid: "62922200"
---
# <a name="backup-devices-sql-server"></a>バックアップ デバイス (SQL Server)
  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データベース上でのバックアップ操作中、バックアップ対象のデータ (*backup*) は、物理バックアップ デバイスに書き込まれます。 この物理バックアップ デバイスは、メディア セットの最初のバックアップが書き込まれるときに初期化されます。 単一または一連のバックアップ デバイス上にあるバックアップによって、1 つのメディア セットが構成されます。  
  
 **このトピックの内容**  
  
-   [用語と定義](#TermsAndDefinitions)  
  
-   [ディスク バックアップ デバイスの使用](#DiskBackups)  
  
-   [テープ デバイスの使用](#TapeDevices)  
  
-   [論理バックアップ デバイスの使用](#LogicalBackupDevice)  
  
-   [バックアップ メディア セットをミラー化](#MirroredMediaSets)  
  
-   [SQL Server のバックアップのアーカイブ](#Archiving)  
  
-   [関連タスク](#RelatedTasks)  
  
##  <a name="TermsAndDefinitions"></a> 用語と定義  
 バックアップ ディスク (backup disk)  
 1 つ以上のバックアップ ファイルを含むハード ディスクまたはその他のディスク記憶メディア。 バックアップ ファイルは正規のオペレーティング システム ファイルです。  
  
 メディア セット (media set)  
 テープやディスク ファイルなどのバックアップ メディアに順序を付けてまとめたもの。決まった種類のバックアップ デバイスが複数使用されます。 メディア セットの詳細については、「 [メディア セット、メディア ファミリ、およびバックアップ セット &#40;SQL Server&#41;](media-sets-media-families-and-backup-sets-sql-server.md)」をご覧ください。  
  
 物理バックアップ デバイス (physical backup device)  
 オペレーティング システムによって提供されるテープ ドライブまたはディスク ファイル。 バックアップは 1 ～ 64 個のバックアップ デバイスに書き込むことができます。 バックアップに複数のバックアップ デバイスが必要な場合、デバイスはすべて 1 種類のデバイス (ディスクまたはテープ) に対応する必要があります。  
  
 ディスクまたはテープに加えて SQL Server のバックアップも Microsoft Azure BLOB ストレージ サービスに書き込むことができます。  
  
##  <a name="DiskBackups"></a> ディスク バックアップ デバイスの使用  
 **このセクションの内容**  
  
-   [物理名 (TRANSACT-SQL) を使用してバックアップ ファイルの指定](#BackupFileUsingPhysicalName)  
  
-   [ディスク バックアップ ファイルのパスを指定します。](#BackupFileDiskPath)  
  
-   [ネットワーク共有上のファイルへのバックアップ](#NetworkShare)  
  
 バックアップ操作によってバックアップがメディア セットに追加されているときにディスク ファイルがいっぱいになると、バックアップ操作は失敗します。 バックアップ ファイルの最大サイズはディスク デバイス上の使用可能な空き領域によって決まるため、バックアップ ディスク デバイスの適切なサイズは、バックアップのサイズによって異なります。  
  
 ディスク バックアップ デバイスには、ATA ドライブなどの単純なディスク デバイスを使用できます。 また、ホットスワップ可能なディスク ドライブも使用できます。この場合、ドライブ上のいっぱいになったディスクを空のディスクと交換する作業を透過的に行えます。 バックアップ ディスクには、サーバー上のローカル ディスクや、共有ネットワーク リソースであるリモート ディスクを使用できます。 リモート ディスクの使用方法については、このトピックの「 [ネットワーク共有のファイルへのバックアップ](#NetworkShare)」をご覧ください。  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] 管理ツールを使用すると、タイムスタンプの付いた名前がディスク ファイルに対して自動的に生成されるため、ディスク バックアップ デバイスの操作を柔軟に行うことができます。  
  
> [!IMPORTANT]  
>  バックアップ ディスクには、データベースのデータ ディスクやログ ディスクとは別のディスクを使用することをお勧めします。 これは、データ ディスクやログ ディスクで障害が発生した場合に確実にバックアップにアクセスできるようにするために必要です。  
  
###  <a name="BackupFileUsingPhysicalName"></a> 物理名 (TRANSACT-SQL) を使用してバックアップ ファイルの指定  
 物理デバイス名を使用してバックアップ ファイルを指定するための基本的な [BACKUP](/sql/t-sql/statements/backup-transact-sql) 構文は次のとおりです。  
  
 BACKUP DATABASE *database_name*  
  
 TO DISK **=** { **'** _physical_backup_device_name_ **'**  |  **@** _physical_backup_device_name_var_ }  
  
 例 :  
  
```  
BACKUP DATABASE AdventureWorks2012   
   TO DISK = 'Z:\SQLServerBackups\AdventureWorks2012.bak';  
GO  
```  
  
 [RESTORE](/sql/t-sql/statements/restore-statements-transact-sql) ステートメントで物理ディスク デバイスを指定するための基本構文は次のとおりです。  
  
 RESTORE { DATABASE | LOG } *database_name*  
  
 FROM DISK **=** { **'** _physical_backup_device_name_ **'**  |  **@** _physical_backup_device_name_var_ }  
  
 例を次に示します。  
  
```  
RESTORE DATABASE AdventureWorks2012   
   FROM DISK = 'Z:\SQLServerBackups\AdventureWorks2012.bak';   
```  
  
###  <a name="BackupFileDiskPath"></a> ディスク バックアップ ファイルのパスを指定します。  
 バックアップ ファイルを指定する場合、その完全パスとファイル名を入力する必要があります。 ファイルへのバックアップ時にファイル名または相対パスだけを指定すると、バックアップ ファイルは既定のバックアップ ディレクトリに配置されます。 既定のバックアップ ディレクトリは、C:\Program Files\Microsoft SQL Server\MSSQL.*n*\MSSQL\Backup です ( *n* はサーバー インスタンスの番号です)。 そのため、既定のサーバー インスタンスの既定のバックアップ ディレクトリはします。C:\Program Files\Microsoft SQL Server\MSSQL12.MSSQLSERVER\MSSQL\Backup.  
  
 指定があいまいになる状態を避けるために、特にスクリプトでは、バックアップ ディレクトリのパスを各 DISK 句で明示的に指定することをお勧めします。 ただし、これは、クエリ エディターを使用している場合はそれほど重要ではありません。 この場合、バックアップ ファイルが既定のバックアップ ディレクトリに存在することがわかっていれば、DISK 句からパスを省略できます。 たとえば、次の `BACKUP` ステートメントでは、 [!INCLUDE[ssSampleDBobject](../../includes/sssampledbobject-md.md)] データベースが、既定のバックアップ ディレクトリにバックアップされます。  
  
```  
BACKUP DATABASE AdventureWorks2012   
   TO DISK = 'AdventureWorks2012.bak';  
GO  
```  
  
> [!NOTE]  
>  既定の場所は、 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\MSSQL.n\MSSQLServer** の **BackupDirectory**レジストリ キーに格納されます。  
  
###  <a name="NetworkShare"></a> ネットワーク共有上のファイルへのバックアップ  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] からリモート ディスク ファイルにアクセスするには、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービス アカウントにネットワーク共有へのアクセス権が必要です。 これには、バックアップ操作によるネットワーク共有への書き込みに必要な権限、および復元操作によるネットワーク共有からの読み取りに必要な権限も含まれます。 ネットワーク ドライブおよび権限の可用性は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] サービスが実行されている状況によって異なります。  
  
-   [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] がドメイン ユーザー アカウントで実行されているときにネットワーク ドライブにバックアップするには、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] が実行されているセッションのネットワーク ドライブとして共有ドライブをマップする必要があります。 コマンド ラインから Sqlservr.exe を起動すると、ログイン セッションでマップしたすべてのドライブが [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で認識されます。  
  
-   Sqlservr.exe をサービスとして実行する場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] はログイン セッションと関係のない個別のセッションで実行されます。 サービスが実行されているセッションには、マップされた独自のドライブがある場合がありますが、通常はありません。  
  
-   ドメイン ユーザーではなくコンピューター アカウントを使用することにより、ネットワーク サービス アカウントで接続できます。 特定のコンピューターから共有ドライブへのバックアップを可能にするには、そのコンピューター アカウントにアクセス権を与えます。 バックアップを書き込んでいる Sqlservr.exe のプロセスにアクセス権がある限り、BACKUP コマンドを送信するユーザーにアクセス権があるかどうかが問題になることはありません。  
  
    > [!IMPORTANT]  
    >  ネットワーク経由でデータをバックアップすると、ネットワーク エラーの影響を受ける場合があります。そのため、リモート ディスクを使用する際はバックアップ操作を終了後に検証することをお勧めします。 詳細については、「[RESTORE VERIFYONLY &#40;Transact-SQL&#41;](/sql/t-sql/statements/restore-statements-verifyonly-transact-sql)」をご覧ください。  
  
#### <a name="specifying-a-universal-naming-convention-unc-name"></a>UNC (汎用名前付け規則) 名の指定  
 バックアップ コマンドや復元コマンドでネットワーク共有を指定するには、ファイルの完全修飾 UNC (汎用名前付け規則) 名をバックアップ デバイスに使用する必要があります。 UNC 名の形式は、 **\\\\** _Systemname_ **\\** _ShareName_ **\\** _Path_ **\\** _FileName_です。  
  
 以下に例を示します。  
  
```  
BACKUP DATABASE AdventureWorks2012   
   TO DISK = '\\BackupSystem\BackupDisk1\AW_backups\AdventureWorksData.Bak';  
GO  
```  
  
##  <a name="TapeDevices"></a> テープ デバイスの使用  
  
> [!NOTE]  
>  テープ バックアップ デバイスは、将来のバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]でサポートされなくなる予定です。 新規の開発作業ではこの機能を使用しないようにし、現在この機能を使用しているアプリケーションは修正することを検討してください。  
  
 **このセクションの内容**  
  
-   [物理名 (TRANSACT-SQL) を使用してバックアップ テープの指定](#BackupTapeUsingPhysicalName)  
  
-   [テープ固有の BACKUP と RESTORE 操作 (TRANSACT-SQL)](#TapeOptions)  
  
-   [開いているテープの管理](#OpenTapes)  
  
 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] データをテープにバックアップするには、テープ ドライブが [!INCLUDE[msCoName](../../includes/msconame-md.md)] Windows オペレーティング システムでサポートされている必要があります。 また、特定のテープ ドライブでは、ドライブの製造元で推奨されているテープのみを使用することをお勧めします。 テープ ドライブのインストール方法の詳細については、Windows オペレーティング システムのマニュアルを参照してください。  
  
 テープ ドライブを使用する場合、バックアップ操作は 1 つのテープがいっぱいになると、別のテープで続行できます。 各テープには、メディア ヘッダーが含まれています。 最初に使用されるメディアは *先頭テープ*といいます。 その後の各テープは *後続テープ* と呼ばれ、前のテープより 1 つ大きいメディア シーケンス番号が付けられます。 たとえば、4 つのテープ デバイスに関連付けられているメディア セットには、少なくとも 4 つの先頭テープ (およびデータベースが収まらない場合は 4 組の後続テープ) が含まれます。 バックアップ セットを追加する場合は、その組に最終テープをセットする必要があります。 最終テープがセットされていない場合、 [!INCLUDE[ssDE](../../includes/ssde-md.md)] は、セットされているテープの終わりまでスキャンし、テープを変更するように要求します。 その時点で、最終テープをセットします。  
  
 テープ バックアップ デバイスは、次の点を除いて、ディスク デバイスと同じように使用します。  
  
-   テープ デバイスは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)]のインスタンスが動作しているコンピューターに物理的に接続する必要があります。 リモートのテープ デバイスへのバックアップはサポートされません。  
  
-   バックアップ操作中にテープ バックアップ デバイスがいっぱいになっても、まだデータを書き込む必要がある場合、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] は新しいテープを挿入するように要求するメッセージを表示し、新しいテープが挿入された後にバックアップ操作を続行します。  
  
###  <a name="BackupTapeUsingPhysicalName"></a> 物理名 (TRANSACT-SQL) を使用してバックアップ テープの指定  
 テープ ドライブの物理デバイス名を使用してバックアップ テープを指定するための基本的な [BACKUP](/sql/t-sql/statements/backup-transact-sql) 構文は次のとおりです。  
  
 BACKUP { DATABASE | LOG } *database_name*  
  
 TO TAPE **=** { **'** _physical_backup_device_name_ **'**  |  **@** _physical_backup_device_name_var_ }  
  
 以下に例を示します。  
  
```  
BACKUP LOG AdventureWorks2012   
   TO TAPE = '\\.\tape0';  
GO  
```  
  
 [RESTORE](/sql/t-sql/statements/restore-statements-transact-sql) ステートメントで物理テープ デバイスを指定するための基本構文は次のとおりです。  
  
 RESTORE { DATABASE | LOG } *database_name*  
  
 FROM TAPE **=** { **'** _physical_backup_device_name_ **'**  |  **@** _physical_backup_device_name_var_ }  
  
###  <a name="TapeOptions"></a> テープ固有の BACKUP と RESTORE 操作 (TRANSACT-SQL)  
 テープ管理を容易にするには、BACKUP ステートメントで次のテープ固有のオプションを指定します。  
  
-   { NOUNLOAD | **UNLOAD** }  
  
     バックアップ操作や復元操作の後にバックアップ テープをテープ ドライブから自動的にアンロードするかどうかを制御できます。 UNLOAD または NOUNLOAD はセッションの設定であり、セッションが終了するまで、または代わりとなるオプションの指定によりリセットされるまで有効です。  
  
-   { **REWIND** | NOREWIND }  
  
     [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] がバックアップ操作や復元操作の後にテープを開いたままにするかどうか、またはその操作が失敗した後にテープを解放して巻き戻すかどうかを制御できます。 既定の動作では、テープを巻き戻します (REWIND)。  
  
> [!NOTE]  
>  BACKUP 構文および引数の詳細については、「[BACKUP &#40;Transact-SQL&#41;](/sql/t-sql/statements/backup-transact-sql)」をご覧ください。 RESTORE 構文および引数の詳細については、それぞれ「[RESTORE &#40;Transact-SQL&#41;](/sql/t-sql/statements/restore-statements-transact-sql)」と「[RESTORE の引数 &#40;Transact-SQL&#41;](/sql/t-sql/statements/restore-statements-arguments-transact-sql)」をご覧ください。  
  
###  <a name="OpenTapes"></a> 開いているテープの管理  
 開いているテープ デバイスの一覧と、マウント要求の状態を確認するには、 [sys.dm_io_backup_tapes](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-backup-tapes-transact-sql) 動的管理ビューに対してクエリを実行します。 このビューでは、開いているすべてのテープが表示されます。 これには、次の BACKUP 操作または RESTORE 操作を待機して一時的にアイドル状態になっている使用中のテープも含まれます。  
  
 テープが誤って開いたまま、テープを解放する最も簡単な方法は、次のコマンドを使用しては。RESTORE REWINDONLY FROM TAPE **=** _backup_device_name_します。 詳細については、「[RESTORE REWINDONLY &#40;Transact-SQL&#41;](/sql/t-sql/statements/restore-statements-rewindonly-transact-sql)」を参照してください。  
  
## <a name="using-the-windows-azure-blob-storage-service"></a>Windows Azure BLOB ストレージ サービスの使用  
 SQL Server のバックアップを Windows Azure BLOB ストレージ サービスに書き込むことができます。  Windows Azure BLOB ストレージ サービスを使用したバックアップについては、「 [Windows Azure BLOB ストレージ サービスを使用した SQL Server のバックアップと復元](sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service.md)」を参照してください。  
  
##  <a name="LogicalBackupDevice"></a> 論理バックアップ デバイスの使用  
 " *論理バックアップ デバイス* " とは、特定の物理バックアップ デバイス (ディスク ファイルやテープ ドライブ) を示す、省略可能なユーザー定義名です。 論理バックアップ デバイスにより、対応する物理バックアップ デバイスを参照する際に間接指定を使用できます。  
  
 論理バックアップ デバイスの定義には、物理デバイスへの論理名の割り当てが含まれます。 たとえば、論理デバイス AdventureWorksBackups が Z:\SQLServerBackups\AdventureWorks2012.bak ファイルまたは \\\\.\tape0 テープ ドライブを指すように定義されたとします。 その後のバックアップ コマンドおよび復元コマンドでは、DISK = 'Z:\SQLServerBackups\AdventureWorks2012.bak' や TAPE = '\\\\.\tape0' ではなく、AdventureWorksBackups をバックアップ デバイスとして指定できます。  
  
 論理デバイス名は、サーバー インスタンス上のすべての論理バックアップ デバイスで一意になる必要があります。 既存の論理デバイス名を表示するには、 [sys.backup_devices](/sql/relational-databases/system-catalog-views/sys-backup-devices-transact-sql) カタログ ビューに対してクエリを実行します。 このビューでは、各論理バックアップ デバイスの名前が表示され、対応する物理バックアップ デバイスの種類と物理的なファイル名またはパスが示されます。  
  
 論理バックアップ デバイスを定義した後、BACKUP コマンドまたは RESTORE コマンドでは、デバイスの物理名ではなく論理バックアップ デバイスを指定できます。 たとえば、次のステートメントでは、 `AdventureWorks2012` データベースが `AdventureWorksBackups` 論理バックアップ デバイスにバックアップされます。  
  
```  
BACKUP DATABASE AdventureWorks2012   
   TO AdventureWorksBackups;  
GO  
```  
  
> [!NOTE]  
>  特定の BACKUP ステートメントまたは RESTORE ステートメントでは、論理バックアップ デバイス名と対応する物理バックアップ デバイス名を交換できます。  
  
 論理バックアップ デバイスを使用する 1 つの利点は、長い物理パスを使用するよりも使いやすいことです。 一連のバックアップを同じパスまたはテープ デバイスに書き込む予定がある場合は、論理バックアップ デバイスを使用すると便利です。 論理バックアップ デバイスは、テープ バックアップ デバイスを識別する際に特に役立ちます。  
  
 バックアップ スクリプトは、特定の論理バックアップ デバイスを使用するように記述できます。 これにより、スクリプトを更新せずに、新しい物理バックアップ デバイスに切り替えることができます。 切り替えには、次の処理が必要です。  
  
1.  元の論理バックアップ デバイスを削除します。  
  
2.  新しい論理バックアップ デバイスを定義します。このデバイスは、元の論理デバイス名を使用しますが、別の物理バックアップ デバイスにマップされているものです。 論理バックアップ デバイスは、テープ バックアップ デバイスを識別する際に特に役立ちます。  
  
##  <a name="MirroredMediaSets"></a> バックアップ メディア セットをミラー化  
 バックアップ メディア セットをミラー化すると、バックアップ デバイスの誤動作の影響を軽減できます。 バックアップはデータ損失に対する最後の防護策なので、このような誤動作は非常に深刻です。 データベースのサイズが大きくなるにつれて、バックアップ デバイスやメディアの障害によってバックアップを復元できなくなる可能性が高くなります。 バックアップ メディアをミラー化すると、物理バックアップ デバイスの冗長性が実現され、バックアップの信頼性が向上します。 詳細については、「[ミラー化バックアップ メディア セット &#40;SQL Server&#41;](mirrored-backup-media-sets-sql-server.md)」を参照してください。  
  
> [!NOTE]  
>  ミラー化バックアップ メディア セットは、 [!INCLUDE[ssEnterpriseEd2005](../../includes/ssenterpriseed2005-md.md)] 以降のバージョンのみでサポートされています。  
  
##  <a name="Archiving"></a> SQL Server のバックアップのアーカイブ  
 ファイル システム バックアップ ユーティリティを使用してディスクのバックアップをアーカイブし、アーカイブをオフサイトに保存することをお勧めします。 ディスクを使用することには、アーカイブしたバックアップをネットワーク経由でオフサイトのディスクに書き込めるという利点があります。 Microsoft Azure BLOB ストレージ サービスは、オフサイト保存のオプションとして使用できます。  ディスク バックアップをアップロードするか、Microsoft Azure BLOB ストレージ サービスにバックアップを直接書き込むことができます。  
  
 もう 1 つの一般的なアーカイブ方法は、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] バックアップをローカルのバックアップ ディスクに書き込み、そのバックアップをテープにアーカイブして、そのテープをオフサイトで保存するという形態です。  
  
##  <a name="RelatedTasks"></a> 関連タスク  
 **ディスク デバイスを使用するには (SQL Server Management Studio)**  
  
-   [バックアップ先としてディスクまたはテープを指定する &#40;SQL Server&#41;](specify-a-disk-or-tape-as-a-backup-destination-sql-server.md)  
  
 **テープ デバイスを使用するには (SQL Server Management Studio)**  
  
-   [バックアップ先としてディスクまたはテープを指定する &#40;SQL Server&#41;](specify-a-disk-or-tape-as-a-backup-destination-sql-server.md)  
  
 **論理バックアップ デバイスを定義するには**  
  
-   [sp_addumpdevice &#40;Transact-SQL&#41;](/sql/relational-databases/system-stored-procedures/sp-addumpdevice-transact-sql)  
  
-   [ディスク ファイルの論理バックアップ デバイスの定義 &#40;SQL Server&#41;](define-a-logical-backup-device-for-a-disk-file-sql-server.md)  
  
-   [テープ ドライブの論理バックアップ デバイスの定義 &#40;SQL Server&#41;](define-a-logical-backup-device-for-a-tape-drive-sql-server.md)  
  
-   <xref:Microsoft.SqlServer.Management.Smo.BackupDevice> (SMO)  
  
 **論理バックアップ デバイスを使用するには**  
  
-   [バックアップ先としてディスクまたはテープを指定する &#40;SQL Server&#41;](specify-a-disk-or-tape-as-a-backup-destination-sql-server.md)  
  
-   [デバイスからのバックアップ復元 &#40;SQL Server&#41;](restore-a-backup-from-a-device-sql-server.md)  
  
-   [BACKUP &#40;Transact-SQL&#41;](/sql/t-sql/statements/backup-transact-sql)  
  
-   [RESTORE &#40;Transact-SQL&#41;](/sql/t-sql/statements/restore-statements-transact-sql)  
  
 **バックアップ デバイスの情報を表示するには**  
  
-   [バックアップの履歴とヘッダーの情報 &#40;SQL Server&#41;](backup-history-and-header-information-sql-server.md)  
  
-   [論理バックアップ デバイスのプロパティと内容の表示 &#40;SQL Server&#41;](view-the-properties-and-contents-of-a-logical-backup-device-sql-server.md)  
  
-   [バックアップ テープまたはバックアップ ファイルの内容の表示 &#40;SQL Server&#41;](view-the-contents-of-a-backup-tape-or-file-sql-server.md)  
  
 **論理バックアップ デバイスを削除するには**  
  
-   [sp_dropdevice &#40;Transact-SQL&#41;](/sql/relational-databases/system-stored-procedures/sp-dropdevice-transact-sql)  
  
-   [バックアップ デバイスの削除 &#40;SQL Server&#41;](delete-a-backup-device-sql-server.md)  
  
## <a name="see-also"></a>参照  
 [SQL Server: Backup Device オブジェクト](../performance-monitor/sql-server-backup-device-object.md)   
 [BACKUP &#40;Transact-SQL&#41;](/sql/t-sql/statements/backup-transact-sql)   
 [メンテナンス プラン](../maintenance-plans/maintenance-plans.md)   
 [メディア セット、メディア ファミリ、およびバックアップ セット &#40;SQL Server&#41;](media-sets-media-families-and-backup-sets-sql-server.md)   
 [RESTORE &#40;Transact-SQL&#41;](/sql/t-sql/statements/restore-statements-transact-sql)   
 [RESTORE LABELONLY &#40;Transact-SQL&#41;](/sql/t-sql/statements/restore-statements-labelonly-transact-sql)   
 [sys.backup_devices &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/sys-backup-devices-transact-sql)   
 [sys.dm_io_backup_tapes &#40;Transact-SQL&#41;](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-backup-tapes-transact-sql)   
 [Mirrored Backup Media Sets &#40;SQL Server&#41;](mirrored-backup-media-sets-sql-server.md)  
  
  
