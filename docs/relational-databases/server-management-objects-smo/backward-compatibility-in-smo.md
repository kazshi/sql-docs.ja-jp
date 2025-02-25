---
title: SMO の旧バージョンとの互換性 |マイクロソフトのドキュメント
ms.custom: ''
ms.date: 03/14/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: ''
ms.topic: reference
ms.assetid: 2f986436-aaf2-4eaf-9809-df849d97d4fb
author: stevestein
ms.author: sstein
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||=sqlallproducts-allversions||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 7a002b4f5d4b678171fc5dd396152d155779f78b
ms.sourcegitcommit: b2464064c0566590e486a3aafae6d67ce2645cef
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68126773"
---
# <a name="backward-compatibility-in-smo"></a>SMO の旧バージョンとの互換性
[!INCLUDE[appliesto-ss-asdb-asdw-xxx-md](../../includes/appliesto-ss-asdb-asdw-xxx-md.md)]

  [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の以前のバージョンを使用して記述されている SMO アプリケーションは、[!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] の SMO を使用して再コンパイルできます。  
  
## <a name="migrating-smo-applications"></a>SMO アプリケーションの移行  
 古いバージョンの [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] の SMO dll への参照は削除し、[!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] で提供される新しい SMO dll への参照を含める必要があります。  
  
 少なくとも、以下の参照を設定してください。  
  
-   Microsoft.SqlServer.ConnectionInfo  
  
-   Microsoft.SqlServer.Smo  
  
-   Microsoft.SqlServer.Management.Sdk.Sfc  
  
 これらのファイルは、接続クラス、SMO ユーティリティ クラス、および基盤クラスのために必要です。  
  
> [!NOTE]  
>  SmoEnum.dll は削除されたため、この dll への参照は SMO [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] プロジェクトから削除する必要があります。  
  
 名前空間も変更されたため、以下のものを使ってください。  
  
##### <a name="for-visual-c"></a>Visual C# 用  
  
```  
using Microsoft.SqlServer.Management.Smo;  
using Microsoft.SqlServer.Management.Common;  
```  
  
##### <a name="for-visual-basic"></a>Visual Basic 用  
  
```  
Imports Microsoft.SqlServer.Management.Smo  
Imports Microsoft.SqlServer.Management.Common  
```  
  
 コードがような Urn 機能を使用する場合**Server.GetSqlSmoObject(Urn)** 、Microsoft.SqlServer.Management.Sdk.Sfc 名前空間にリンクする必要があります。  
  
 コードで Transfer オブジェクトを直接使用している場合は、Microsoft.SqlServer.Management.SmoExtended 名前空間へのリンクが必要になります。  
  
 コードを移行するときに、コードの修正が必要になる場合があります。 これは、[!INCLUDE[ssVersion2005](../../includes/ssversion2005-md.md)] ではいくつかの [!INCLUDE[ssKatmai](../../includes/sskatmai-md.md)] 機能および [!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)] 機能が非推奨とされているためです。 非推奨の機能の詳細については、次を参照してください。 [SQL Server 2016 におけるデータベース エンジン機能を非推奨とされます](../../database-engine/deprecated-database-engine-features-in-sql-server-2016.md)で[!INCLUDE[ssCurrent](../../includes/sscurrent-md.md)]オンライン ブックの「します。  
  
  
