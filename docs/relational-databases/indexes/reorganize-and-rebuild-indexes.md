---
title: インデックスの再構成と再構築 | Microsoft Docs
ms.custom: ''
ms.date: 11/24/2017
ms.prod: sql
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.reviewer: ''
ms.technology: table-view-index
ms.topic: conceptual
f1_keywords:
- sql13.swb.index.rebuild.f1
- sql13.swb.indexproperties.fragmentation.f1
- sql13.swb.index.reorg.f1
helpviewer_keywords:
- large object defragmenting
- indexes [SQL Server], reorganizing
- index reorganization [SQL Server]
- reorganizing indexes
- defragmenting large object data types
- index fragmentation [SQL Server]
- index rebuilding [SQL Server]
- rebuilding indexes
- indexes [SQL Server], rebuilding
- defragmenting indexes
- nonclustered indexes [SQL Server], defragmenting
- fragmentation [SQL Server]
- index defragmenting [SQL Server]
- LOB data [SQL Server], defragmenting
- clustered indexes, defragmenting
ms.assetid: a28c684a-c4e9-4b24-a7ae-e248808b31e9
author: MikeRayMSFT
ms.author: mikeray
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||=sqlallproducts-allversions||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: 02cc19cb28a3141682786ca8192688c9c451fc43
ms.sourcegitcommit: 495913aff230b504acd7477a1a07488338e779c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68811163"
---
# <a name="reorganize-and-rebuild-indexes"></a>インデックスの再構成と再構築

[!INCLUDE[appliesto-ss-asdb-asdw-pdw-md](../../includes/appliesto-ss-asdb-asdw-pdw-md.md)]

この記事では、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] で [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] または [!INCLUDE[tsql](../../includes/tsql-md.md)] を使用して、断片化したインデックスを再構成または再構築する方法について説明します。 [!INCLUDE[ssDEnoversion](../../includes/ssdenoversion-md.md)] では、基になるデータに対して挿入、更新、または削除の各操作が行われるたびに、インデックスが自動的に変更されます。 このような変更が長期にわたると、インデックス内の情報がデータベース内に散在 (断片化) することになります。 インデックスに、キー値に基づく論理順序とデータ ファイル内の物理順序が一致しないページが存在すると、断片化が発生します。 インデックスが大量に断片化されると、クエリのパフォーマンスが低下し、アプリケーションの応答が遅くなる場合があります。特にスキャン操作が遅くなります。

インデックスを再構成するか、インデックスを再構築することにより、インデックスの断片化を解消できます。 パーティション構成に基づいて構築されたパーティション インデックスでは、完全なインデックスまたはインデックスの 1 つのパーティションで、再構成または再構築のいずれかを実行できます。
インデックスの再構築では、インデックスを削除し再作成します。 この操作では、断片化をなくし、指定されているか既に存在する FILL FACTOR 設定に基づいてページを圧縮することによりディスク領域を取り戻した後、連続するページにインデックス行を再び並べ替えます。 `ALL` を指定した場合、テーブル上のすべてのインデックスが、1 回のトランザクションで削除され再構築されます。
インデックスの再構成では、最小のシステム リソースが使用されます。 この操作では、リーフ レベル ページをリーフ ノードの論理順序 (左から右) に合わせて物理的に並べ替えることにより、テーブルやビュー上にあるクラスター化および非クラスター化インデックスのリーフ レベルをデフラグします。 再構成でも、インデックス ページは圧縮されます。 圧縮は既存の FILL FACTOR 値に基づいて行われます。

## <a name="BeforeYouBegin"></a> はじめに

### <a name="Fragmentation"></a> 断片化の検出

断片化を解消する方法を決める最初の手順は、断片化の程度を判断するためにインデックスを分析することです。 システム関数 [sys.dm_db_index_physical_stats](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md)を使用して、特定のインデックス、テーブルやインデックス付きビュー上のすべてのインデックス、データベース内のすべてのインデックス、またはすべてのデータベース内のすべてのインデックスの断片化を検出できます。 パーティション インデックスの場合は、 **sys.dm_db_index_physical_stats** でもパーティションごとの断片化情報が提供されます。

**sys.dm_db_index_physical_stats** 関数から返される結果セットに含まれる列を次に示します。

|[列]|[説明]|
|------------|-----------------|
|**avg_fragmentation_in_percent**|論理的な断片化 (インデックス内で順序が乱れたページ) の割合。|
|**fragment_count**|インデックス内の断片化 (物理的に連続したリーフ ページ) の数。|
|**avg_fragment_size_in_pages**|インデックス内の 1 つの断片化内の平均ページ数。|

断片化の程度がわかったら、次の表を使用して、断片化を解消するための最適な方法を決定します。

|**avg_fragmentation_in_percent** 値|断片化解消ステートメント|
|-----------------------------------------------|--------------------------|
|5 ～ 30%|ALTER INDEX REORGANIZE|
|> 30%|ALTER INDEX REBUILD WITH (ONLINE = ON) <sup>1</sup>|

<sup>1</sup> インデックスの再構築はオンラインでもオフラインでも実行できます。 インデックスの再構成は、常にオンラインで実行されます。 再構成オプションと同様の可用性を実現するには、インデックスをオンラインで再構築してください。

> [!TIP]
> これらの値は、`ALTER INDEX REORGANIZE` と `ALTER INDEX REBUILD` の使い分けの大まかな目安となります。 ただし、実際の値は状況によって変わります。 それぞれの環境で実際に試して最適なしきい値を特定することが重要です。 たとえば、特定のインデックスが主にスキャン操作に使用されている場合、断片化を解消すると、これらの操作のパフォーマンスが向上します。 主にシーク操作に使用されるインデックスについては、パフォーマンス上の利点は小さくなります。 同様に、ヒープ (クラスター化インデックスのないテーブル) 内の断片化の解消は、非クラスター化インデックスのスキャン操作では特に便利ですが、参照操作にはほとんど影響しません。

断片化のレベルが非常に低い場合 (5% 未満) は、通常、これらのコマンドのいずれも使用しないでください。インデックスの再構成や再構築には、ほとんどの場合、そのようなわずかな断片化を解消するには見合わないコストがかかります。 `ALTER INDEX REORGANIZE` と `ALTER INDEX REBUILD` の詳細については、「[ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)」を参照してください。

> [!NOTE]
> 小さなインデックスを再構築または再構成しても、多くの場合、断片化が解消することはありません。 小さなインデックスのページは、混合エクステントに格納される場合もあります。 混合エクステントは最大 8 つのオブジェクトで共有されるため、小さなインデックスを再構成または再構築しても、その断片化は解消されない場合があります。

### <a name="index-defragmentation-considerations"></a>インデックスの最適化に関する考慮事項
特定の条件下では、非クラスター化インデックス レコードに含まれる物理識別子または論理識別子を変更する必要がある場合に、クラスター化インデックスを再構築すると、クラスター化キーを参照する非クラスター化インデックスが自動的に再構築されます。

すべての非クラスター化インデックスを強制的にテーブル上に自動再構築するシナリオ:

-  テーブルに対するクラスター化インデックスの作成
-  テーブルがヒープとして格納される原因となる、クラスター化インデックスの削除
-  クラスター化キーの変更による列の挿入または除外

すべての非クラスター化インデックスをテーブル上で自動的に再構築する必要がないシナリオ:

-  一意のクラスター化インデックスの再構築
-  一意でないクラスター化インデックスの再構築
-  クラスター化インデックスへのパーティション構成の適用、クラスター化インデックスの別のファイルグループへの移動など、インデックス スキーマの変更

### <a name="Restrictions"></a> 制限事項と制約事項

128 エクステントを超えるインデックスは、論理フェーズと物理フェーズの 2 つの独立したフェーズで再構築されます。 論理フェーズでは、インデックスによって使用されている既存のアロケーション ユニットが、割り当て解除に設定されます。その後、データ行がコピーされ、並べ替えられてから、再構築されたインデックスを格納するために作成された新しいアロケーション ユニットに移動されます。 物理フェーズでは、バックグラウンドで行われる短いトランザクションで、以前に割り当て解除に設定されたアロケーション ユニットが物理的に削除され、ロックの必要はあまり多くありません。 エクステントの詳細については、「[ページとエクステントのアーキテクチャ ガイド](../../relational-databases/pages-and-extents-architecture-guide.md)」を参照してください。

この操作では、ファイル グループ内の他のファイルではなく、同じファイル上に一時的な作業ページを割り当てなければならないため、 `ALTER INDEX REORGANIZE` ステートメントには、使用可能な領域があるインデックスを含むデータ ファイルが必要です。 そのため、ファイル グループに空き容量があるとしても、エラー 1105 が発生することがあります。`Could not allocate space for object '###' in database '###' because the '###' filegroup is full. Create disk space by deleting unneeded files, dropping objects in the filegroup, adding additional files to the filegroup, or setting autogrowth on for existing files in the filegroup.`

固定されていないインデックスをパーティションが 1, 000 個以上あるテーブルに作成または再構築することは可能ですが、推奨されていません。 このような操作を行うと、操作中にパフォーマンスが低下したりメモリが過度に消費される可能性があります。

インデックスのあるファイル グループがオフラインであるか、または読み取り専用に設定されている場合、インデックスを再構成または再構築することはできません。 キーワード `ALL` を指定した場合で、1 つ以上のインデックスがオフラインまたは読み取り専用のファイル グループにある場合、ステートメントは失敗します。

> [!IMPORTANT]
> [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] では、インデックスが作成または再構築されたとき、テーブル内のすべての行をスキャンして統計が作成または更新されます。
>
> ただし、[!INCLUDE[ssSQL11](../../includes/sssql11-md.md)] 以降では、パーティション インデックスが作成または再構築されたとき、テーブル内のすべての行をスキャンして統計が作成または更新されることはありません。 代わりに、クエリ オプティマイザーが既定のサンプリング アルゴリズムを使用してこれらの統計を生成します。 テーブル内のすべての行をスキャンしてパーティション インデックスの統計を作成するには、`FULLSCAN` 句で `CREATE STATISTICS` または `UPDATE STATISTICS` を使用します。

### <a name="Security"></a> セキュリティ

#### <a name="Permissions"></a> Permissions

テーブルまたはビューに対する `ALTER` 権限が必要です。 ユーザーは次のロールの少なくとも 1 つのメンバーである必要があります。

- **db_ddladmin** データベース ロール <sup>1</sup>
- **db_owner** データベース ロール
- **sysadmin** サーバー ロール

<sup>1</sup>**db_ddladmin** データベース ロールには[最も低い特権レベル](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)が設定されています。

## <a name="SSMSProcedureFrag"></a> [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] を利用してインデックスの断片化を確認する

### <a name="to-check-the-fragmentation-of-an-index"></a>インデックスの断片化を確認するには

1. オブジェクト エクスプローラーで、インデックスの断片化を確認するテーブルが格納されているデータベースを展開します。
2. **[テーブル]** フォルダーを展開します。
3. インデックスの断片化を確認するテーブルを展開します。
4. **[インデックス]** フォルダーを展開します。
5. 断片化を確認するインデックスを右クリックし、 **[プロパティ]** を選択します。
6. **[ページの選択]** の **[断片化]** を選択します。

   **[断片化]** ページでは、次の情報を取得できます。

   **[ページのゆとり]** インデックス ページのゆとりの平均をパーセントで示します。 100% は、インデックス ページが完全にいっぱいであることを示します。 50% は、平均して各インデックス ページが半分埋まっていることを示します。

   **[全体の断片化]** 論理的な断片化の割合です。 この値は、順番に格納されないインデックス内のページ数を示します。

   **[行の平均サイズ]** リーフ レベルの行の平均サイズです。

   **[奥行]** インデックス内のレベルの数 (リーフ レベルを含む) です。

   **[転送されたレコード]** 別のデータの場所への転送ポインターを持つ、ヒープ内の転送されたレコード数です。 (この状態は、更新中に、新しい行を格納できる十分なスペースが元の場所にない場合に発生します)。

   **[非実体行]** 削除のマークが付けられている行の中で、まだ削除されていない行の数です。 これらの行は、サーバーが使用中でないときにクリーンアップ スレッドによって削除されます。 この値には、スナップショット分離トランザクションが未完了であるために保持される行は含まれません。

   **インデックスの種類** インデックスの種類です。 指定できる値は、 **[クラスター化]** 、 **[非クラスター化]** 、および **[プライマリ XML]** です。 テーブルは、ヒープ (インデックスなし) としても格納できますが、その場合は、この [インデックスのプロパティ] ページは開きません。

   **[リーフレベルの行]** リーフ レベルの行の数です。

   **[行の最大サイズ]** リーフレベルの行の最大サイズです。

   **[行の最小サイズ]** リーフレベルの行の最小サイズです。

   **[ページ]** データ ページ数の合計です。

   **[パーティション ID]** インデックスを含む B ツリーのパーティション ID です。

   **[バージョンの非実体行]** 未処理のスナップショット分離トランザクションが原因で保持されている非実体レコードの数です。

## <a name="TsqlProcedureFrag"></a> [!INCLUDE[tsql](../../includes/tsql-md.md)] を利用してインデックスの断片化を確認する

### <a name="to-check-the-fragmentation-of-an-index"></a>インデックスの断片化を確認するには

次の例では、AdventureWorks データベース内で、HumanResources.Employee テーブル内のすべてのインデックスの断片化の平均パーセンテージを検索します。

```sql
SELECT a.index_id, name, avg_fragmentation_in_percent
   FROM sys.dm_db_index_physical_stats
      (DB_ID
         (N'AdventureWorks2012')
         , OBJECT_ID(N'HumanResources.Employee')
         , NULL
         , NULL
         , NULL) AS a
   JOIN sys.indexes AS b
      ON a.object_id = b.object_id
      AND a.index_id = b.index_id;
```

前のステートメントによって、次のような結果セットが返されます。

```cmd
index_id    name                                                  avg_fragmentation_in_percent
----------- ----------------------------------------------------- ----------------------------
1           PK_Employee_BusinessEntityID                          0
2           IX_Employee_OrganizationalNode                        0
3           IX_Employee_OrganizationalLevel_OrganizationalNode    0
5           AK_Employee_LoginID                                   66.6666666666667
6           AK_Employee_NationalIDNumber                          50
7           AK_Employee_rowguid                                   0

(6 row(s) affected)
```

詳細については、[sys.dm_db_index_physical_stats](../../relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql.md) に関する記事をご覧ください。

## <a name="SSMSProcedureReorg"></a> [!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] を利用して断片化を取り除く

### <a name="to-reorganize-or-rebuild-an-index"></a>インデックスを再構成または再構築するには

1. オブジェクト エクスプローラーで、インデックスを再構成するテーブルが格納されているデータベースを展開します。
2. **[テーブル]** フォルダーを展開します。
3. インデックスを再構成するテーブルを展開します。
4. **[インデックス]** フォルダーを展開します。
5. 再構成するインデックスを右クリックし、 **[再構成]** を選択します。
6. **[インデックスの再構成]** ダイアログ ボックスで、 **[再構成するインデックス]** グリッドに目的のインデックスが表示されていることを確認し、 **[OK]** をクリックします。
7. **[ラージ オブジェクトの列データを圧縮する]** チェック ボックスをオンにして、ラージ オブジェクト (LOB) データを含むページもすべて圧縮することを指定します。
8. クリックして **OK.**

### <a name="to-reorganize-all-indexes-in-a-table"></a>テーブルのすべてのインデックスを再構成するには

1. オブジェクト エクスプローラーで、インデックスを再構成するテーブルが格納されているデータベースを展開します。
2. **[テーブル]** フォルダーを展開します。
3. インデックスを再構成するテーブルを展開します。
4. **[インデックス]** フォルダーを右クリックし、 **[すべて再構成]** を選択します。
5. **[インデックスの再構成]** ダイアログ ボックスで、 **[再構成するインデックス]** に目的のインデックスが表示されていることを確認します。 **[再構成するインデックス]** グリッドからインデックスを削除するには、インデックスを選択し、Del キーを押します。
6. **[ラージ オブジェクトの列データを圧縮する]** チェック ボックスをオンにして、ラージ オブジェクト (LOB) データを含むページもすべて圧縮することを指定します。
7. クリックして **OK.**

### <a name="to-rebuild-an-index"></a>インデックスを再構築するには

1. オブジェクト エクスプローラーで、インデックスを再構成するテーブルが格納されているデータベースを展開します。
2. **[テーブル]** フォルダーを展開します。
3. インデックスを再構成するテーブルを展開します。
4. **[インデックス]** フォルダーを展開します。
5. 再構成するインデックスを右クリックし、 **[再構築]** を選択します。
6. **[インデックスの再構築]** ダイアログ ボックスで、 **[再構築するインデックス]** グリッドに目的のインデックスが表示されていることを確認し、 **[OK]** をクリックします。
7. **[ラージ オブジェクトの列データを圧縮する]** チェック ボックスをオンにして、ラージ オブジェクト (LOB) データを含むページもすべて圧縮することを指定します。
8. クリックして **OK.**

## <a name="TsqlProcedureReorg"></a> [!INCLUDE[tsql](../../includes/tsql-md.md)] を利用して断片化を取り除く

### <a name="to-reorganize-a-fragmented-index"></a>断片化されたインデックスを再構成するには

次の例では、AdventureWorks データベース内で `HumanResources.Employee` テーブルに `IX_Employee_OrganizationalLevel_OrganizationalNode` インデックスを再構成します。

```sql
ALTER INDEX IX_Employee_OrganizationalLevel_OrganizationalNode
   ON HumanResources.Employee
   REORGANIZE
;
```

### <a name="to-reorganize-all-indexes-in-a-table"></a>テーブルのすべてのインデックスを再構成するには

次の例では、AdventureWorks データベース内で、HumanResources.Employee テーブルのすべてのインデックスを再構成します。

```sql
ALTER INDEX ALL ON HumanResources.Employee
   REORGANIZE
;
```

### <a name="to-rebuild-a-fragmented-index"></a>断片化されたインデックスを再構築するには

次の例では、AdventureWorks データベース内で、`Employee` テーブルの単一のインデックスを再構築します。

[!code-sql[IndexDDL#AlterIndex1](../../relational-databases/indexes/codesnippet/tsql/reorganize-and-rebuild-i_1.sql)]

### <a name="to-rebuild-all-indexes-in-a-table"></a>テーブルのすべてのインデックスを再構築するには

次の例では、`ALL` キーワードを使って、AdventureWorks データベース内のテーブルに関連付けられたすべてのインデックスを再構築します。 3 つのオプションが指定されます。

[!code-sql[IndexDDL#AlterIndex2](../../relational-databases/indexes/codesnippet/tsql/reorganize-and-rebuild-i_2.sql)]

詳細については、[ALTER INDEX](../../t-sql/statements/alter-index-transact-sql.md) に関する記事をご覧ください。

### <a name="automatic-index-and-statistics-management"></a>インデックスと統計の自動管理

[Adaptive Index Defrag](https://github.com/Microsoft/tigertoolbox/tree/master/AdaptiveIndexDefrag) のようなソリューションを活用し、1 つまたは複数のデータベースに対するインデックスの最適化と統計更新を自動管理します。 このプロシージャでは、断片化レベルやその他のパラメーターに基づいてインデックスを再構築または再構成するか、線形しきい値で統計を更新するかが自動的に選択されます。

## <a name="see-also"></a>参照

- [SQL Server インデックス デザイン ガイド](../../relational-databases/sql-server-index-design-guide.md)
- [ALTER INDEX &#40;Transact-SQL&#41;](../../t-sql/statements/alter-index-transact-sql.md)
- [Adaptive Index Defrag](https://github.com/Microsoft/tigertoolbox/tree/master/AdaptiveIndexDefrag)
- [CREATE STATISTICS &#40;Transact-SQL&#41;](../../t-sql/statements/create-statistics-transact-sql.md)
- [UPDATE STATISTICS &#40;Transact-SQL&#41;](../../t-sql/statements/update-statistics-transact-sql.md)
