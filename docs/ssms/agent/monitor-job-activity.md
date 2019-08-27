---
title: ジョブの利用状況の監視 | Microsoft Docs
ms.custom: ''
ms.date: 01/19/2017
ms.prod: sql
ms.prod_service: sql-tools
ms.reviewer: ''
ms.technology: ssms
ms.topic: conceptual
helpviewer_keywords:
- SQL Server Agent, monitoring
- jobs [SQL Server Agent], monitoring
- monitoring [SQL Server], jobs
- activity monitoring [SQL Server Agent]
- monitoring [SQL Server], SQL Server Agent
- monitoring [SQL Server Agent]
- SQL Server Agent Job Activity Monitor
- SQL Server Agent jobs, monitoring
- performance [SQL Server], jobs
- current activity
ms.assetid: 71cb432b-631d-4b8b-9965-e731b3d8266d
author: markingmyname
ms.author: maghan
monikerRange: = azuresqldb-mi-current || >= sql-server-2016 || = sqlallproducts-allversions
ms.openlocfilehash: a227b7ee1723dd8a77d7eb118476969575c5951c
ms.sourcegitcommit: e7d921828e9eeac78e7ab96eb90996990c2405e9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68267503"
---
# <a name="monitor-job-activity"></a>ジョブの利用状況の監視
[!INCLUDE[appliesto-ss-asdbmi-xxxx-xxx-md](../../includes/appliesto-ss-asdbmi-xxxx-xxx-md.md)]

> [!IMPORTANT]  
> [Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) では現在、すべてではありませんがほとんどの SQL Server エージェントの機能がサポートされています。 詳細については、「[Azure SQL Database Managed Instance と SQL Server の T-SQL の相違点](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#sql-server-agent)」を参照してください。

[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントのジョブの利用状況モニターを使用すると、[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] のインスタンスで定義されているすべてのジョブの現在の利用状況を監視できます。  
  
## <a name="sql-server-agent-sessions"></a>SQL Server エージェント セッション  
[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントでは、サービスが開始されるたびに新しいセッションが作成されます。 新しいセッションが作成されると、すべての既存の定義済みジョブが、 **msdb** データベースの **sysjobactivity** テーブルに設定されます。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントを再起動したときには、ジョブの前回の利用状況がこのテーブルに保持されています。 各セッションでは、 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェントの通常のジョブの利用状況が、開始から終了まで記録されます。 これらのセッションに関する情報は、 **msdb** データベースの **syssessions** テーブルに格納されます。  
  
## <a name="job-activity-monitor"></a>[ジョブの利用状況モニター]  
[!INCLUDE[ssManStudioFull](../../includes/ssmanstudiofull-md.md)] を使うと、ジョブの利用状況モニターで **sysjobactivity** テーブルを表示できるようになります。 サーバーのすべてのジョブを表示するか、またはフィルターを定義して表示されるジョブの数を制限できます。 また、 **[エージェント ジョブの利用状況]** グリッドの列見出しをクリックすることによって、ジョブ情報を並べ替えることもできます。 たとえば、 **[最終実行]** 列見出しをクリックすると、最後に実行された順にジョブを並べ替えることができます。 もう一度この列見出しをクリックすると、最終実行日時に基づいて、ジョブの昇順と降順が切り替わります。  
  
ジョブの利用状況モニターで実行できる操作は次のとおりです。  
  
-   ジョブを開始および停止を行う。  
  
-   ジョブのプロパティを表示する。  
  
-   特定のジョブの履歴を表示する。  
  
-   **[エージェント ジョブの利用状況]** グリッドの情報を手動で更新したり、 **[更新の設定を表示します]** をクリックして自動更新間隔を設定したりする。  
  
ジョブの利用状況モニターは、実行のスケジュールが設定されているジョブ、現在のセッション中に実行されたジョブの最終結果、および現在実行中であるかまたはアイドル状態のジョブを確認する場合に使用します。 [!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント サービスが予期せず停止した場合、ジョブの利用状況モニターで以前のセッションを参照することにより、サービスが停止したときに実行中であったジョブを確認できます。  
  
ジョブの利用状況モニターを開くには、 **のオブジェクト エクスプローラーで** [SQL Server エージェント] [!INCLUDE[ssManStudio](../../includes/ssmanstudio-md.md)] を展開します。次に、 **[ジョブの利用状況モニター]** を右クリックして、 **[ジョブの利用状況の表示]** をクリックします。  
  
ストアド プロシージャ **sp_help_jobactivity**を使用することによって、現在のセッションのジョブの利用状況を表示することもできます。  
  
## <a name="related-tasks"></a>Related Tasks  
  
|||  
|-|-|  
|**[説明]**|**トピック**|  
|[!INCLUDE[ssNoVersion](../../includes/ssnoversion-md.md)] エージェント ジョブの実行状態を表示する方法を説明します。|[[ジョブの利用状況の表示]](../../ssms/agent/view-job-activity.md)|  
  
## <a name="see-also"></a>参照  
[[ジョブの利用状況の表示]](../../ssms/agent/view-job-activity.md)  
[sysjobactivity (Transact-SQL)](https://msdn.microsoft.com/fd17cac9-5d1f-4b44-b2dc-ee9346d8bf1e)  
[syssessions (Transact-SQL)](https://msdn.microsoft.com/187819b6-c7f4-4a26-b74c-0a89e96695cf)  
[sp_help_jobactivity (Transact-SQL)](https://msdn.microsoft.com/d344864f-b4d3-46b1-8933-b81dec71f511)  
  
