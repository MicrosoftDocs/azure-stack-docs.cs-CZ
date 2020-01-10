---
title: Zálohování App Service v centru Azure Stack | Microsoft Docs
description: Naučte se, jak zálohovat App Services v centru Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: a0a1d2b1025e814152b2a9b643683c5ed68bf5ff
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75804533"
---
# <a name="back-up-app-service-on-azure-stack-hub"></a>Zálohování App Service v centru Azure Stack

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack Development Kit*  

Tento dokument poskytuje pokyny, jak zálohovat App Service v centru Azure Stack.

> [!IMPORTANT]
> App Service v centru Azure Stack se nezálohují jako součást [zálohování infrastruktury centra Azure Stack](azure-stack-backup-infrastructure-backup.md). Jako operátor centra Azure Stack musíte provést kroky, abyste zajistili, že App Service se v případě potřeby dají úspěšně obnovit.

Azure App Service v centru Azure Stack mají čtyři hlavní součásti, které je potřeba vzít v úvahu při plánování zotavení po havárii:
1. Infrastruktura poskytovatele prostředků; role serveru, úrovně pracovního procesu atd. 
2. App Service tajných kódů.
3. App Service SQL Server hostování a měření databází.
4. Obsah úlohy App Service uživatele uložený ve sdílené složce App Service

## <a name="back-up-app-service-secrets"></a>Zálohování App Service tajných kódů
Při obnovování App Service ze zálohy je nutné zadat App Service klíče používané počátečním nasazením. Tyto informace by měly být uloženy ihned po úspěšném nasazení App Service a uložené na bezpečném místě. Konfigurace infrastruktury poskytovatele prostředků se znovu vytvoří ze zálohy během obnovení pomocí rutin PowerShellu pro obnovení App Service.

Použijte portál pro správu k zálohování tajných kódů App Service pomocí následujících kroků: 

1. Přihlaste se k portálu správce Azure Stackového centra jako správce služby.

2. Vyhledejte **App Service** -> **tajných**kódů. 

3. Vyberte **Stáhnout tajné**kódy.

   ![Stažení tajných kódů na portálu pro správu centra Azure Stack](./media/app-service-back-up/download-secrets.png)

4. Až budou tajná klíčová místa připravená ke stažení, klikněte na **Uložit** a uložte soubor App Service tajných kódů (**SystemSecrets. JSON**) do bezpečného umístění. 

   ![Uložení tajných klíčů na portále správce centra Azure Stack](./media/app-service-back-up/save-secrets.png)

> [!NOTE]
> Tyto kroky opakujte při každém otočení App Servicech tajných kódů.

## <a name="back-up-the-app-service-databases"></a>Zálohování databází App Service
K obnovení App Service budete potřebovat zálohy databáze **Appservice_hosting** a **Appservice_metering** . Doporučujeme použít SQL Server plány údržby nebo Azure Backup Server, abyste zajistili, že se tyto databáze budou zálohovat a bezpečně ukládat v pravidelných intervalech. Je však možné použít jakoukoli metodu zajištění pravidelného zálohování SQL.

K ručnímu zálohování těchto databází při přihlášení k SQL Server použijte následující příkazy PowerShellu:

  ```powershell
  $s = "<SQL Server computer name>"
  $u = "<SQL Server login>" 
  $p = read-host "Provide the SQL admin password"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_hosting TO DISK = '<path>\hosting.bak'"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_metering TO DISK = '<path>\metering.bak'"
  ```

> [!NOTE]
> Pokud potřebujete zálohovat databáze SQL AlwaysOn, postupujte podle [těchto pokynů](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server?view=sql-server-2017). 

Po úspěšném zálohování všech databází zkopírujte soubory. bak do bezpečného umístění spolu s informacemi App Service tajných kódů.

## <a name="back-up-the-app-service-file-share"></a>Zálohování App Service sdílené složky
App Service ukládá informace o aplikaci tenanta do sdílené složky. Tato sdílená složka se musí pravidelně zálohovat spolu s App Service databází, takže pokud se vyžaduje obnovení, dojde ke ztrátě co možná malého množství dat.

Pokud chcete zálohovat App Service obsahu sdílené složky, použijte Azure Backup Server nebo jinou metodu k pravidelnému kopírování obsahu sdílené složky do umístění, kam jste uložili všechny předchozí informace o obnovení.

Pomocí těchto kroků můžete například použít příkaz Robocopy z relace konzoly Windows PowerShell (nikoli PowerShell ISE):

```powershell
$source = "<file share location>"
$destination = "<remote backup storage share location>"
net use $destination /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy $source $destination
net use $destination /delete
```

## <a name="next-steps"></a>Další kroky
[Obnovit App Service v centru Azure Stack](app-service-recover.md)