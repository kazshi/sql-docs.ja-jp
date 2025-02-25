---
title: Windows Azure でのデータファイルの SQL Server |Microsoft Docs
ms.custom: ''
ms.date: 06/13/2017
ms.prod: sql-server-2014
ms.reviewer: ''
ms.technology: supportability
ms.topic: conceptual
ms.assetid: 38ffd9c2-18a5-43d2-b674-e425addec4e4
author: MikeRayMSFT
ms.author: mikeray
manager: craigg
ms.openlocfilehash: 36d940e71dc6a5816dc08fb79e045f46f783116e
ms.sourcegitcommit: 9348f79efbff8a6e88209bb5720bd016b2806346
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2019
ms.locfileid: "69028618"
---
# <a name="sql-server-data-files-in-windows-azure"></a>Windows Azure 内の SQL Server データ ファイル
  Windows Azure 内の SQL Server データ ファイルにより、Windows Azure BLOB として格納された SQL Server データベース ファイルに対するネイティブ サポートが有効になります。 この機能を使用すると、内部設置型の環境または Windows Azure 仮想マシンで実行されている SQL Server でデータベースを作成し、Windows Azure BLOB ストレージに専用のデータ保存場所を用意できます。 この機能強化では特に、デタッチとアタッチの操作を使用することにより、コンピューター間でのデータベース移動が容易になります。 また、Windows Azure ストレージを復元元または復元先として使用することで、データベースのバックアップ ファイルに代替の格納場所が提供されます。 このため、データの仮想化、移動、セキュリティ、および可用性の面での利点と、低コストと容易なメンテナンスで実現できる高可用性と柔軟なスケーリングにより、いくつかのハイブリッド ソリューションが有効になります。  
  
 このトピックでは、Windows Azure Storage サービスに SQL Server データファイルを格納する際の中心となる概念と考慮事項について説明します。  
  
 この新機能の使用方法に関する実際の実地体験については、「[チュートリアル:Windows Azure Storage サービス](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md)のデータファイルを SQL Server します。  
  
 次の図は、この機能強化によって、サーバーの場所に関係なく、SQL Server データベース ファイルを Windows Azure BLOB として Windows Azure ストレージに格納できることを示しています。  
  
 ![Windows Azure Storage との SQL Server 統合](../../database-engine/media/sql-server-dbfiles-stored-as-blobs.gif "Windows Azure Storage との SQL Server 統合")  
  
## <a name="benefits-of-using-sql-server-data-files-in-windows-azure"></a>Windows Azure で SQL Server データ ファイルを使用する利点  
  
-   **容易で迅速な移行の利点:** この機能を使うと、オンプレミスのコンピューターの間でも、またオンプレミス環境とクラウド環境の間でも、アプリケーションを変更することなくデータベースを 1 つずつ移動するため、移行プロセスが容易になります。 このため、既存の内部設置型のインフラストラクチャを維持しつつ、インクリメンタルな移行を行うことができます。 さらに、内部設置型の環境の複数拠点でアプリケーションを実行する必要がある場合も、集中管理されたデータ ストレージにアクセスできることで、アプリケーション ロジックが簡素化されます。 場合によっては、地理的に分散している各地にコンピューター センターを短期間でセットアップし、多種多様なソースからデータを収集することもできます。 この新しい機能強化を使用することにより、データをある場所から別の場所に移動する代わりに、多数のデータベースを Windows Azure BLOB として格納しておき、Transact-SQL スクリプトを実行して、ローカル コンピューターまたは仮想マシンにデータベースを作成することができます。  
  
-   **コストと無制限のストレージの利点:** この機能により、オンプレミスのコンピューティングリソースを活用しながら、Windows Azure に無制限のオフサイトストレージを使用できます。 格納場所として Windows Azure を使用すると、ハードウェア管理のオーバーヘッドがなく、アプリケーション ロジックに専念することが容易になります。 内部設置型のコンピューティング ノードが失われた場合は、データを移動することなく新しいノードをセットアップできます。  
  
-   **高可用性とディザスター リカバリーの利点:** Windows Azure 機能で SQL Server データファイルを使用すると、高可用性とディザスターリカバリーのソリューションが簡素化される可能性があります。 たとえば、Windows Azure の仮想マシンまたは SQL Server のインスタンスがクラッシュした場合、Windows Azure BLOB へのリンクを再設定するだけで、新しいマシンにデータベースを再作成できます。  
  
-   **セキュリティの利点:** この新しい機能強化では、コンピューティング インスタンスをストレージ インスタンスから分離できます。 データベースをフルに暗号化し、暗号化解除をストレージ インスタンスではなくコンピューティング インスタンスのみで行うことができます。 つまり、この新しい機能強化では、データとは物理的に分離された TDE (Transparent Data Encryption: 透過的なデータ暗号化) 証明書を使用して、パブリック クラウドに置くすべてのデータを暗号化できます。 TDE キーは、物理的に安全な内部設置型のコンピューターに格納されローカルでバックアップされている master データベースに保存できます。 これらのローカル キーを使用して、Windows Azure ストレージ上に存在するデータを暗号化することができます。 クラウド内のストレージ アカウントの資格情報が盗用されても、TDE 証明書は常に内部設置型の環境にあるため、データの安全性が維持されます。  
  
## <a name="concepts-and-requirements"></a>概念と要件  
  
### <a name="windows-azure-storage-concepts"></a>Microsoft Azure ストレージの概念  
 Microsoft Azure 機能で SQL Server データ ファイルを使用する場合は、Microsoft Azure 内でストレージ アカウントとコンテナーを作成する必要があります。 次に、SQL Server 資格情報を作成する必要があります。これには、コンテナーのポリシーに関する情報と、コンテナーにアクセスするために必要な Shared Access Signature が含まれます。  
  
 Microsoft Azure のストレージ アカウントは、BLOB にアクセスするための名前空間の最高レベルを表します。 ストレージ アカウントには、合計サイズが 500 TB までであれば、無制限の数のコンテナーを含めることができます。 ストレージの制限に関する最新情報については、「 [Azure のサブスクリプションとサービスの制限、クォータ、および制約](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/)」をご覧ください。 コンテナーでは、一連の BLOB をグループ化することができます。 BLOB はすべて、コンテナー内に存在する必要があります。 アカウントには、無制限の数のコンテナーを含めることができます。 同様に、コンテナーには、無制限の数の BLOB を格納できます。 Microsoft Azure ストレージに格納できる BLOB には、ブロック BLOB とページ BLOB の 2 種類があります。 この新しい機能ではページ BLOB が使用されます。ページ BLOB では 1 TB までのサイズがサポートされ、ファイル内のバイト範囲が頻繁に変更される場合はこちらの方が効率的です。 BLOB には、 `http://storageaccount.blob.core.windows.net/<container>/<blob>`という URL 形式を使用してアクセスできます。  
  
### <a name="windows-azure-billing-considerations"></a>Microsoft Azure の課金に関する注意点  
 Microsoft Azure サービスの使用コストを見積もることは、意思決定および計画のプロセスにおいて重要です。 Microsoft Azure ストレージに SQL Server データ ファイルを格納する場合は、ストレージとトランザクションに関連するコストを支払う必要があります。 さらに、Microsoft Azure のストレージ機能への SQL Server データ ファイルの格納を実装する場合は、45 ～ 60 秒ごとに BLOB のリースの暗黙的な更新が必要になります。 その結果、.mdf、.ldf などのデータベース ファイルごとのトランザクション コストも必要になります。 予測では、2 個のデータベース ファイル (.mdf と .ldf) のリースを更新するコストは、現在の価格のモデルで 1 か月につき約 2 セントになります。 Microsoft Azure ストレージと Microsoft Azure 仮想マシンの使用に関する月額コストを見積もるには、「 [Azure 料金](https://azure.microsoft.com/pricing/) 」の情報をご覧ください。  
  
### <a name="sql-server-concepts"></a>SQL サーバーの概念  
 この新しい機能強化を使用する場合は、次のことを行う必要があります。  
  
-   コンテナーのポリシーを作成し、Shared Access Signature (SAS) キーを生成する必要があります。  
  
-   データ ファイルまたはログ ファイルによって使用されるコンテナーごとに、名前がコンテナーのパスに一致する SQL Server 資格情報を作成する必要があります。  
  
-   Microsoft Azure ストレージ コンテナー、関連するポリシー名、および SAS キーに関する情報を SQL Server 資格情報ストアに格納する必要があります。  
  
 次の例では、Windows Azure ストレージ コンテナーが作成され、読み取り、書き込み、および一覧表示の権限でポリシーが作成されていることを前提としています。 コンテナーのポリシーを作成すると、SAS キーが生成されます。SAS キーは暗号化せずに安全にメモリ内に保持でき、SQL Server がコンテナー内の BLOB ファイルにアクセスする際に必要になります。 次のコード スニペットでは、 `'your SAS key'` を `'sr=c&si=<MYPOLICYNAME>&sig=<THESHAREDACCESSSIGNATURE>'`のようなエントリと置き換えます。 詳細については、「 [Shared Access Signature の作成と使用](https://msdn.microsoft.com/library/azure/jj721951.aspx)」を参照してください。  
  
```  
  
-- Create a credential  
CREATE CREDENTIAL [https://testdb.blob.core.windows.net/data]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'your SAS key'  
  
-- Create database with data and log files in Windows Azure container.  
CREATE DATABASE testdb   
ON  
( NAME = testdb_dat,  
    FILENAME = 'https://testdb.blob.core.windows.net/data/TestData.mdf' )  
 LOG ON  
( NAME = testdb_log,  
    FILENAME =  'https://testdb.blob.core.windows.net/data/TestLog.ldf')  
  
```  
  
 **重要な注意事項:** コンテナーのデータ ファイルに対するアクティブな参照が存在する場合、対応する SQL Server 資格情報を削除しようとすると失敗します。  
  
### <a name="security"></a>セキュリティ  
 Microsoft Azure ストレージに SQL Server データ ファイルを格納する場合のセキュリティに関する考慮事項と要件は次のとおりです。  
  
-   Microsoft Azure BLOB ストレージ サービスのコンテナーを作成する際は、アクセス権を private に設定することをお勧めします。 アクセス権を private に設定すると、コンテナーと BLOB データを読み取ることができるのは Microsoft Azure アカウントの所有者だけになります。  
  
-   SQL Server データベース ファイルを Microsoft Azure ストレージに格納する際には、Shared Access Signature (コンテナー、BLOB、キュー、およびテーブルへの制限付きアクセス権を付与する URI) を使用する必要があります。 Shared Access Signature を使用することで、Microsoft Azure ストレージ アカウント キーを共有せずに、SQL Server からストレージ アカウント内のリソースへのアクセスを可能にすることができます。  
  
-   これに加えて、従来型の内部設置環境でのセキュリティ対策もデータベースに適用することをお勧めします。  
  
### <a name="installation-prerequisites"></a>インストールの前提条件  
 SQL Server データファイルを Windows Azuree に格納する場合のインストールの前提条件は次のとおりです。  
  
-   **オンプレミスの SQL Server:** SQL Server 2014 バージョンには、この機能が含まれています。 SQL Server 2014 のダウンロード方法については、「 [SQL Server 2014](https://www.microsoft.com/sqlserver/sql-server-2014.aspx)」をご覧ください。  
  
-   Windows Azure 仮想マシンで実行されている SQL Server:Windows Azure 仮想マシンに SQL Server をインストールする場合は SQL Server 2014 をインストールするか、既存のインスタンスを更新します。 同様に、SQL Server 2014 CTP2 のプラットフォーム イメージを使用して Microsoft Azure に新しい仮想マシンを作成することもできます。 SQL Server 2014 のダウンロード方法については、「 [SQL Server 2014](https://www.microsoft.com/sqlserver/sql-server-2014.aspx)」をご覧ください。  
  
###  <a name="bkmk_Limitations"></a> 制限事項  
  
-   この機能の現在のリリースでは、Windows Azure ストレージに `FileStream` データを格納することはできません。 Windows Azure ストレージに統合されたローカル データベースに `Filestream` データを格納することはできますが、Windows Azure ストレージを使用してコンピューター間で Filestream データを移動することはできません。 `FileStream` データについては、従来の手法を使用して、Filestream に関連付けられたファイル (.mdf、.ldf) を異なるコンピューター間で移動することをお勧めします。  
  
-   現在、この新しい機能強化では、Microsoft Azure ストレージ内の同じデータベース ファイルに複数の SQL Server インスタンスで同時にアクセスすることはできません。 ServerA がアクティブなデータベース ファイルとオンライン接続されているときに、誤って起動された ServerB に同じデータ ファイルを指すデータベースがある場合、2 番目のサーバーでは、次のエラーでデータベースの起動に失敗します。エラー コード **5120 物理ファイル "%.\*ls" を開けません。オペレーティング システム エラー %d: "%ls"** 。  
  
-   Microsoft Azure 機能で SQL Server データ ファイルを使用して Microsoft Azure ストレージ内に格納できるのは、.mdf、.ldf、.ndf ファイルのみです。  
  
-   Microsoft Azure 機能で SQL Server データ ファイルを使用する場合は、ストレージ アカウントに対する地理的レプリケーションはサポートされません。 ストレージ アカウントで地理的レプリケーションが実行されている場合に、地理的フェールオーバーが発生すると、データの破損が発生する可能性があります。  
  
-   各 BLOB のサイズは、最大 1 TB にすることができます。 この値により、Microsoft Azure ストレージに格納できる個々のデータベース データとログ ファイルの上限が決定されます。  
  
-   Microsoft Azure ストレージ機能で SQL Server データ ファイルを使用する場合は、インメモリ OLTP データを Microsoft Azure BLOB ストレージ内に格納することはできません。 これは、インメモリ OLTP に `FileStream` に対する依存関係があり、この機能の現在のリリースでは、Windows Azure ストレージに `FileStream` データを格納することがサポートされていないためです。  
  
-   Windows Azure 機能で SQL Server データ ファイルを使用する場合は、SQL Server は `master` データベース内で設定された照合順序を使用して、すべての URL 比較やパス比較を実行します。  
  
-   `AlwaysOn Availability Groups`は、プライマリ データベースに新しいデータベース ファイルを追加しない限りサポートされます。 データベース操作により、プライマリ データベースに新しいファイルを作成する必要がある場合は、まずセカンダリ ノードで AlwaysOn 可用性グループを無効にします。 次に、プライマリ データベースに対してデータベース操作を実行し、プライマリ ノードでデータベースをバックアップします。 さらに、データベースをセカンダリ ノードに復元し、セカンダリ ノードで AlwaysOn 可用性グループを有効にします。 Windows Azure 機能で SQL Server データ ファイルを使用する場合は、AlwaysOn フェールオーバー クラスター インスタンスはサポートされません。  
  
-   通常の運用中、SQL Server では一時リースを使用して BLOB をストレージ用に予約し、各 BLOB リースを 45 ～ 60 秒ごとに更新します。 サーバーがクラッシュし、同じ BLOB を使用するように構成された別の SQL Server インスタンスが起動された場合、新しいインスタンスは、BLOB の既存リース期限が切れるまで最大 60 秒間待機します。 リース期限が 60 秒以内に切れるのを待機できない場合にデータベースを別のインスタンスにアタッチするには、BLOB のリースを明示的に終了してアタッチ操作でのエラーを回避することができます。  
  
## <a name="tools-and-programming-reference-support"></a>ツールおよびプログラミング リファレンスのサポート  
 ここでは、Microsoft Azure 機能で SQL Server データ ファイルを使用する場合に使用可能なツールとプログラミング リファレンス ライブラリについて説明します。  
  
### <a name="powershell-support"></a>PowerShell のサポート  
 SQL Server 2014 では、ファイル パスの代わりに BLOB ストレージの URL パスを参照することにより、PowerShell コマンドレットを使用して、SQL Server データ ファイルを Microsoft Azure BLOB ストレージ サービスに格納できます。 BLOB には、`: http://storageaccount.blob.core.windows.net/<container>/<blob>` という URL 形式を使用してアクセスできます。  
  
### <a name="sql-server-object-and-performance-counters-support"></a>SQL Server オブジェクトとパフォーマンス カウンターのサポート  
 SQL Server 2014 以降では、Microsoft Azure ストレージ機能内の SQL Server データ ファイルと組み合わせて使用する目的で、1 つの新しい SQL Server オブジェクトが追加されました。 新しい SQL Server オブジェクトは [SQL Server, HTTP_STORAGE_OBJECT](../performance-monitor/sql-server-http-storage-object.md) と呼ばれます。これをシステム モニターで使用すると、SQL Server を Microsoft Azure Storage と共に使用する場合のアクティビティを監視できます。  
  
### <a name="sql-server-management-studio-support"></a>SQL Server Management Studio のサポート  
 SQL Server Management Studio では、複数のダイアログ ウィンドウでこの機能を使用することができます。 たとえば、 `https://teststorageaccnt.blob.core.windows.net/testcontainer/` [新しいデータベース] **、** [データベースのアタッチ] **、** [データベースの復元] **など複数のダイアログ ウィンドウの**[パス] **として、ストレージ コンテナーの URL パス (** など) を入力できます。 詳しくは、「[チュートリアル: Windows Azure Storage サービス](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md)のデータファイルを SQL Server します。  
  
### <a name="sql-server-management-objects-support"></a>SQL Server 管理オブジェクトのサポート  
 Microsoft Azure 機能で SQL Server データ ファイルを使用する場合は、すべての SQL Server 管理オブジェクト (SMO) がサポートされます。 SMO オブジェクトにファイル パスが必要であれば、ローカル ファイル パスの代わりに BLOB の URL 形式 (`https://teststorageaccnt.blob.core.windows.net/testcontainer/` など) を使用します。 SQL Server 管理オブジェクト (SMO) の詳細については、SQL Server オンライン ブックの「[SQL Server 管理オブジェクト &#40;SMO&#41; プログラミング ガイド](../server-management-objects-smo/sql-server-management-objects-smo-programming-guide.md) 」をご覧ください。  
  
### <a name="transact-sql-support"></a>Transact-SQL のサポート  
 この新しい機能により、Transact-SQL の表層のセキュリティ構成が次のように変更されました。  
  
-   **sys.master_files** システム ビューに、新しい **int**列 **credential_id** が追加されました。 **credential_id** 列は、Azure Storage 対応データ ファイルが自身の資格状態を使用するために sys.credentials への相互参照を有効にする目的で使用されます。 資格情報を使用するデータベース ファイルが存在する場合に、この資格情報を削除できないなどのトラブルシューティングに使用できます。  
  
##  <a name="bkmk_Troubleshooting"></a>Windows Azure での SQL Server データファイルのトラブルシューティング  
 サポートされていない機能または制限事項によるエラーを回避するために、まず「 [Limitations](sql-server-data-files-in-microsoft-azure.md#bkmk_Limitations)」をご確認ください。  
  
 Microsoft Azure ストレージ機能で SQL Server データ ファイルを使用する場合に発生する可能性のあるエラーの一覧は、次のとおりです。  
  
 **認証エラー**  
  
-   *資格情報 '%.\*ls' を削除できません。この資格情報は、アクティブなデータベース ファイルで使用されています。*    
    解決方法:Windows Azure Storage でアクティブなデータベースファイルによって使用されている資格情報を削除しようとすると、このエラーが表示されることがあります。 資格情報を削除するには、まずこのデータベース ファイルのある関連 BLOB を削除する必要があります。 アクティブなリースを保持している BLOB を削除するには、先にリースを終了する必要があります。  
  
-   *コンテナーに対して Shared Access Signature が正しく作成されていません。*    
     解決方法:コンテナーに対して Shared Access Signature が正しく作成されていることを確認します。 「[チュートリアル:Windows Azure Storage サービス](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md)のデータファイルを SQL Server します。  
  
-   *SQL Server 資格情報が正しく作成されていません。*    
    解決方法: **[ID]** フィールドに 'Shared Access Signature' を使用して、シークレットが正しく作成されたことを確認します。 「[チュートリアル:Windows Azure Storage サービス](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md)のデータファイルを SQL Server します。  
  
 **BLOB リース エラー**  
  
-   同じ BLOB ファイルを使用する別のインスタンスの後に起動しようとして SQL Server がクラッシュしました。 解決方法:通常の運用中、SQL Server では一時リースを使用して BLOB をストレージ用に予約し、各 BLOB リースを 45 ～ 60 秒ごとに更新します。 サーバーがクラッシュし、同じ BLOB を使用するように構成された別の SQL Server インスタンスが起動された場合、新しいインスタンスは、BLOB の既存リース期限が切れるまで最大 60 秒間待機します。 リース期限が 60 秒以内に切れるのを待機できない場合にデータベースを別のインスタンスにアタッチするには、BLOB のリースを明示的に終了してアタッチ操作でのエラーを回避することができます。  
  
 **データベース エラー**  
  
1.  *データベースの作成中にエラーが発生しました*   
    解決方法:「[チュートリアル:Windows Azure Storage サービス](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md)のデータファイルを SQL Server します。  
  
2.  *ALTER ステートメントの実行中にエラーが発生しました*   
    解決方法:ALTER DATABASE ステートメントは、必ずデータベースがオンライン状態のときに実行してください。 データ ファイルを Microsoft Azure ストレージにコピーするときは常に、ブロック BLOB ではなくページ BLOB を作成します。 そうしないと、ALTER DATABASE は失敗します。 「[チュートリアル:Windows Azure Storage サービス](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md)のデータファイルを SQL Server します。  
  
3.  *エラー コード 5120 物理ファイル "%.\*ls" を開けません。オペレーティング システム エラー %d: "%ls"*    
    解決方法:現在、この新しい機能強化では、Microsoft Azure ストレージ内の同じデータベース ファイルに複数の SQL Server インスタンスで同時にアクセスすることはできません。 ServerA がアクティブなデータベース ファイルとオンライン接続されているときに、誤って起動された ServerB に同じデータ ファイルを指すデータベースがある場合、2 番目のサーバーでは、次のエラーでデータベースの起動に失敗します。エラー コード *5120 物理ファイル "%.\*ls" を開けません。オペレーティング システム エラー %d: "%ls"* 。  
  
     この問題を解決するには、まず、Microsoft Azure ストレージ内のデータベース ファイルに ServerA からアクセスする必要があるかどうかを決定します。 必要ない場合は、Microsoft Azure ストレージ内のデータベース ファイルと ServerA との間の接続を削除します。 これを行うには、次の手順を実行します。  
  
    1.  ALTER DATABASE ステートメントを使用して、ServerA のファイル パスをローカル フォルダーに設定します。  
  
    2.  ServerA でデータベースをオフラインに設定します。  
  
    3.  次に、Microsoft Azure ストレージから ServerA のローカル フォルダーにデータベース ファイルをコピーします。これにより、ServerA でデータベースのローカル コピーを確保できます。  
  
    4.  データベースをオンラインに設定します。  
  
## <a name="see-also"></a>関連項目  
 [チュートリアル: Windows Azure Storage サービスのデータファイルの SQL Server](../tutorial-use-azure-blob-storage-service-with-sql-server-2016.md)  
  
  
