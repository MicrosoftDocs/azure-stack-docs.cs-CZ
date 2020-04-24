---
title: Odebrat Azure App Service z centra Azure Stack
description: Informace o tom, jak odebrat Azure App Service z centra Azure Stack
author: apwestgarth
ms.topic: article
ms.date: 04/17/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/17/20207
ms.openlocfilehash: b371b319ae87bb112633b64d77d66f8916a13124
ms.sourcegitcommit: 355e21dd9b8c3f44e14abaae0b4f176443cf7495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81636469"
---
# <a name="remove-azure-app-service-from-azure-stack-hub"></a>Odebrat Azure App Service z centra Azure Stack

Tento článek popisuje, jak z centra Azure Stack odebrat poskytovatele prostředků Azure App Service a související součásti.

## <a name="remove-resource-provider"></a>Odebrat poskytovatele prostředků

> [!Important]
> Tato operace odebere všechny prostředky tenanta, odebere službu a kvóty ze všech plánů a v celém rozsahu odebere poskytovatele prostředků Azure App Service.  Pokud jste nasadili App Service vysoce dostupný souborový server a šablonu pro rychlý Start SQL Server, budou tyto prostředky také odebrány při jejich nasazení ve stejné skupině prostředků jako Azure App Service v Azure Stackovém centru.

Pokud chcete Azure App Service z centra Azure Stack odebrat, postupujte podle tohoto jednoho kroku:

1. Odstraňte skupinu prostředků, která obsahuje Azure App Service v prostředcích služby Azure Stack hub, například AppService. Local.

## <a name="remove-databases-and-file-share-content"></a>Odebrání databází a obsahu sdílení souborů

V této části je potřeba postupovat pouze v případě, že je váš SQL Server nebo souborový server nasazený mimo razítko nebo v jiné skupině prostředků, jinak pokračovat k další části.

### <a name="remove-databases-and-logins"></a>Odebrat databáze a přihlášení

1. Pokud používáte **SQL Server Always On**, odeberte databáze **AppService_Hosting** a **AppService_Metering** ze skupiny dostupnosti:

1. Chcete-li odebrat databáze a přihlášení, spusťte následující skript SQL

   ```sql
   --******************************************************************
   /*
   Script to clean up App Service objects (databases and logins).
   */
   USE [master]
   GO

   DROP DATABASE [appservice_hosting]
   GO

   DROP DATABASE [appservice_metering]
   GO

   DECLARE @sql NVARCHAR(MAX) = N'';    
 
   SELECT @sql += '
   DROP LOGIN [' + name + '];' 
   from master.sys.sql_logins
   WHERE name LIKE  '%_hosting_%' OR 
   name LIKE  '%_metering_%' OR
   name LIKE  '%WebWorker_%';

   PRINT @sql;
   EXEC sp_executesql @sql;
   PRINT 'Completed';

   --******************************************************************
   ```

### <a name="remove-the-application-file-content-from-the-file-server"></a>Odebrat obsah souboru aplikace ze souborového serveru

1. Odstraňte sdílenou složku obsahu ze souborového serveru.

## <a name="next-steps"></a>Další kroky

Pokud ho chcete znovu nainstalovat, vraťte se do části [požadavky na nasazení App Service v článku centra Azure Stack](azure-stack-app-service-before-you-get-started.md) .
