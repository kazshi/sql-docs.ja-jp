---
title: SQL Server Native Client によるアプリケーションの構築 |マイクロソフトのドキュメント
ms.custom: ''
ms.date: 03/16/2017
ms.prod: sql
ms.reviewer: ''
ms.technology: native-client
ms.topic: reference
helpviewer_keywords:
- data access [SQL Server Native Client], building applications
- SQLNCLI, building applications
- applications [SQL Server Native Client]
- SQL Server Native Client, building applications
ms.assetid: 254a2b48-f0e3-43b5-a48d-3d666c2a779f
author: MightyPen
ms.author: genemi
monikerRange: '>=aps-pdw-2016||=azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||=sqlallproducts-allversions||>=sql-server-linux-2017||=azuresqldb-mi-current'
ms.openlocfilehash: eac83c7dd853e2cff17198d37c47d54fc5919324
ms.sourcegitcommit: b2464064c0566590e486a3aafae6d67ce2645cef
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68069366"
---
# <a name="building-applications-with-sql-server-native-client"></a>SQL Server Native Client を使用したアプリケーションのビルド
[!INCLUDE[appliesto-ss-asdb-asdw-pdw-md](../../../includes/appliesto-ss-asdb-asdw-pdw-md.md)]
[!INCLUDE[SNAC_Deprecated](../../../includes/snac-deprecated.md)]

  [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client ライブラリを使用するアプリケーションを開発する際には、いくつかの課題があります。 このセクションのトピックでは、MDAC から [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client へのアップグレード、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client ヘッダー ファイルやライブラリ ファイルの使用など、これらの課題の多くについて説明します。また、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client と併用できるさまざまな接続文字列の概要についても説明します。  
  
## <a name="in-this-section"></a>このセクションの内容  
 [SQL Server Native Client のインストール](../../../relational-databases/native-client/applications/installing-sql-server-native-client.md)  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client をインストールする方法、各種コンポーネントがインストールされる場所、および [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client をアンインストールする方法について説明します。  
  
 [SQL Server Native Client のコンポーネント](../../../relational-databases/native-client/applications/components-of-sql-server-native-client.md)  
 ライブラリ ファイル、リソース ファイル、ヘルプ ファイル、ヘッダー ファイルなど、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client を構成するコンポーネントについて説明します。  
  
 [SQL Server Native Client での接続文字列キーワードの使用](../../../relational-databases/native-client/applications/using-connection-string-keywords-with-sql-server-native-client.md)  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client 経由でデータベースに接続するときに使用できる、さまざまな接続文字列について説明します。  
  
 [SQL Server Native Client ヘッダー ファイルとライブラリ ファイルの使用](../../../relational-databases/native-client/applications/using-the-sql-server-native-client-header-and-library-files.md)  
 アプリケーション内で [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client ヘッダー ファイルとライブラリ ファイルを使用する方法について説明します。  
  
 [MDAC から SQL Server Native Client へのアプリケーションの更新](../../../relational-databases/native-client/applications/updating-an-application-to-sql-server-native-client-from-mdac.md)  
 [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client と MDAC の違い、および MDAC から [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client にアップグレードするときに考慮すべき点について説明します。  
  
 [SQL Server 2005 Native Client からのアプリケーションの更新](../../../relational-databases/native-client/applications/updating-an-application-from-sql-server-2005-native-client.md)  
 [!INCLUDE[ssVersion2005](../../../includes/ssversion2005-md.md)] Native Client から [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の [!INCLUDE[ssSQL11](../../../includes/sssql11-md.md)] Native Client にアップグレードするときに考慮すべき点について説明します。  
  
 [SQL Server Native Client と ADO の併用](../../../relational-databases/native-client/applications/using-ado-with-sql-server-native-client.md)  
 ADO で [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client を使用して [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] の機能にアクセスし、それらの機能を使用する方法について説明します。  
  
 [SQL Server Native Client のサポート ポリシー](../../../relational-databases/native-client/applications/support-policies-for-sql-server-native-client.md)  
 さまざまなデータ アクセス コンポーネントを、[!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] Native Client の各バージョンで使用する方法について説明します。  
  
 [SQL Server Native Client を使用した Windows Azure SQL Database への接続](../../../relational-databases/native-client/applications/connecting-to-a-windows-azure-sql-database-using-sql-server-native-client.md)  
 [!INCLUDE[ssSDS](../../../includes/sssds-md.md)] Native Client を使用して [!INCLUDE[ssNoVersion](../../../includes/ssnoversion-md.md)] に接続する方法について説明します。  
  
## <a name="see-also"></a>関連項目  
 [SQL Server Native Client プログラミング](../../../relational-databases/native-client/sql-server-native-client-programming.md)   
 [ODBC の操作方法に関するトピック](../../../relational-databases/native-client-odbc-how-to/odbc-how-to-topics.md)   
 [OLE DB の使用法に関するトピック](../../../relational-databases/native-client-ole-db-how-to/ole-db-how-to-topics.md)  
  
  
