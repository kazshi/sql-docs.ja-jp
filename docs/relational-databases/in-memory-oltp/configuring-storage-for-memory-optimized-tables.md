---
title: メモリ最適化テーブルのストレージの構成 | Microsoft Docs
ms.custom: ''
ms.date: 10/25/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: in-memory-oltp
ms.topic: conceptual
ms.assetid: 6e005de0-3a77-4b91-b497-14cc0f9f6605
author: CarlRabeler
ms.author: carlrab
ms.openlocfilehash: 3d4c7f50e791324d7e0a0a13164875c5095eb5d0
ms.sourcegitcommit: b2464064c0566590e486a3aafae6d67ce2645cef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67915287"
---
# <a name="configuring-storage-for-memory-optimized-tables"></a>メモリ最適化テーブルのストレージの構成
[!INCLUDE[appliesto-ss-xxxx-xxxx-xxx-md](../../includes/appliesto-ss-xxxx-xxxx-xxx-md.md)]
  記憶域の容量と 1 秒間の入出力操作 (IOPS) を構成する必要があります。  
  
## <a name="storage-capacity"></a>記憶域の容量  
 [メモリ最適化テーブルのメモリ必要量の推定](../../relational-databases/in-memory-oltp/estimate-memory-requirements-for-memory-optimized-tables.md) の情報を使用して、データベースの持続性のあるメモリ最適化テーブルのメモリ内サイズを推定します。 インデックスはメモリ最適化テーブルに対して永続化されないため、インデックスのサイズは含めません。 サイズを確認した後には、持続性のあるメモリ内テーブルのサイズの 4 倍のディスク領域を提供する必要があります。  
  
## <a name="storage-iops"></a>記憶域の IOPS  
 [!INCLUDE[hek_2](../../includes/hek-2-md.md)] は、ワークロードのスループットを大幅に上げることができます。 したがって、IO がボトルネックになっていないことを確認してください。  
  
-   ディスク ベース テーブルをメモリ最適化テーブルに移行するときは、トランザクション ログ アクティビティの増加をサポートできる記憶メディアにトランザクション ログがあることを確認してください。 たとえば、記憶メディアが 100 MB/秒のトランザクション ログの処理をサポートし、その結果としてメモリ最適化テーブルが 5 倍優れたパフォーマンスをもたらす場合、トランザクション ログ アクティビティがパフォーマンスのボトルネックになることを防ぐために、トランザクション ログの記憶メディアでも、5 倍のパフォーマンス向上をサポートできる必要があります。  
  
-   メモリ最適化テーブルは、1 つまたは複数のコンテナーに分散されたチェックポイント ファイルに保存されます。 各コンテナーは、通常、独自のストレージ デバイスにマップする必要があり、増加する記憶域容量と IOPS の向上の両方のために使用されます。 ストレージ メディアのシーケンシャル IOPS が、持続的トランザクション ログ スループットの最大 3 倍をサポートできることを確認する必要があります。 チェックポイント ファイルへの書き込みは、データ ファイルで 256 KB、デルタ ファイルで 4 KB です。
  
     - たとえば、メモリ最適化テーブルがトランザクション ログで持続的に 500 MB/秒のアクティビティを生成する場合、メモリ最適化テーブルのストレージは 1.5 GB/秒の IOPS をサポートする必要があります。 持続的なトランザクション ログのスループットの 3 倍をサポートする必要性は、次の観察点に由来します。すなわち、データとデルタ ファイルのペアは、まず初期データと共に書き込まれ、それからマージ操作の一環として読み取り/再書き込みされる必要があるという点です。  
  
- 記憶域の IOPS を見積もる際のもう 1 つの要因は、メモリ最適化テーブルの復旧時間です。 持続性のあるテーブルからのデータは、データベースがアプリケーションで使用される前にメモリに読み込む必要があります。 一般的に、メモリ最適化テーブルへのデータの読み込みは IOPS の速度で実行できます。 それで、持続性のあるメモリ最適化テーブルの合計の記憶域が 60 GB で、1 分でこのデータを読み込めるようにする場合は、記憶域の IOPS を 1 GB/秒に設定する必要があります。  
  
-   スペースが許す場合は、通常、チェックポイント ファイルはすべてのコンテナーに均等に分散されます。 SQL server 2014 では、均一な分散を実現するには奇数個のコンテナーが必要です。2016 以降では、コンテナーが奇数または偶数のどちらでも、均一に分散されます。
  
## <a name="encryption"></a>暗号化  
 [!INCLUDE[ssSQL15](../../includes/sssql15-md.md)] では、メモリ最適化テーブルのストレージの暗号化は、データベースで TDE を有効化する際に行われます。 詳細については、「[透過的なデータ暗号化 &#40;TDE&#41;](../../relational-databases/security/encryption/transparent-data-encryption.md)」を参照してください。 [!INCLUDE[ssSQL14](../../includes/sssql14-md.md)] では、データベースで TDE が有効になっている場合でも、チェックポイント ファイルは暗号化されません。
  
## <a name="see-also"></a>参照  
 [メモリ最適化オブジェクト用ストレージの作成と管理](../../relational-databases/in-memory-oltp/creating-and-managing-storage-for-memory-optimized-objects.md)  
  
  
