---
title: App Service obnovení v centru Azure Stack
description: Přečtěte si o zotavení po havárii pro App Service v centru Azure Stack.
author: bryanla
ms.topic: article
ms.date: 03/21/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: 7d8d994b7ca134d13c08a987e622027b5f4b7732
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76875177"
---
# <a name="app-service-recovery-on-azure-stack-hub"></a>App Service obnovení v centru Azure Stack

V tomto tématu najdete pokyny k tomu, jaké akce se App Service zotavení po havárii.

Aby bylo možné obnovit App Service v centru Azure Stack ze zálohy, je nutné provést následující akce:
1. Obnovte App Service databáze.
2. Obnovte obsah sdílené složky souborového serveru.
3. Obnovte App Service role a služby.

Pokud se pro úložiště Function Apps použilo úložiště centra Azure Stack, musíte také provést kroky k obnovení aplikací Function App.

## <a name="restore-the-app-service-databases"></a>Obnovení databází App Service
Databáze App Service SQL Server by měly být obnoveny na instanci SQL Server připravené pro produkční prostředí. 

Po [přípravě instance SQL Server](azure-stack-app-service-before-you-get-started.md#prepare-the-sql-server-instance) pro hostování databází App Service použijte následující postup obnovení databáze ze zálohy:

1. Přihlaste se k SQL Server, která bude hostovat obnovené databáze App Service s oprávněními správce.
2. Pomocí následujících příkazů obnovte App Service databáze z příkazového řádku spuštěného s oprávněními správce:
    ```dos
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_hosting FROM DISK='<full path to backup>' WITH REPLACE"
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_metering FROM DISK='<full path to backup>' WITH REPLACE"
    ```
3. Ověřte, zda byla úspěšně obnovena a ukončena SQL Server Management Studio databáze App Service.

> [!NOTE]
> Chcete-li provést obnovení z instance clusteru s podporou převzetí služeb při selhání, přečtěte si téma [obnovení z neúspěšné instance](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/recover-from-failover-cluster-instance-failure?view=sql-server-2017) 

## <a name="restore-the-app-service-file-share-content"></a>Obnovení obsahu App Service sdílené složky
Po [přípravě souborového serveru](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server) pro hostování App Service sdílené složky musíte obnovit obsah sdílené složky klienta ze zálohy. Můžete použít libovolnou metodu, kterou máte k dispozici ke zkopírování souborů do nově vytvořené App Service umístění sdílené složky. Po spuštění tohoto příkladu na souborovém serveru se k připojení ke vzdálené sdílené složce a zkopírování souborů do sdílené složky použijí PowerShell a Robocopy:

```powershell
$source = "<remote backup storage share location>"
$destination = "<local file share location>"
net use $source /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy /E $source $destination
net use $source /delete
```

Kromě kopírování obsahu sdílené složky musíte také resetovat oprávnění ke sdílené složce souborů. Chcete-li obnovit oprávnění, otevřete příkazový řádek správce na počítači souborového serveru a spusťte soubor **ReACL. cmd** . Soubor **ReACL. cmd** se nachází v instalačních souborech App Service v adresáři **BCDR** .

## <a name="restore-app-service-roles-and-services"></a>Obnovení rolí a služeb App Service
Po obnovení databází App Service a obsahu sdílené složky souborů je dál potřeba použít PowerShell k obnovení rolí a služeb App Service. Tyto kroky obnoví App Service tajných klíčů a konfigurací služeb.  

1. Přihlaste se k řadiči App Service **CN0-VM** VM jako **roleadmin** pomocí hesla, které jste zadali během instalace App Service. 
    > [!TIP]
    > Aby bylo možné připojení RDP, je nutné upravit skupinu zabezpečení sítě virtuálního počítače. 
2. Zkopírujte soubor **SystemSecrets. JSON** místně do virtuálního počítače kontroleru. V dalším kroku musíte zadat cestu k tomuto souboru jako parametr `$pathToExportedSecretFile`.
3. Pomocí následujících příkazů v okně konzoly PowerShellu se zvýšenými oprávněními obnovte App Service role a služby:

    ```powershell
    # Stop App Service services on the primary controller VM
    net stop WebFarmService
    net stop ResourceMetering
    net stop HostingVssService # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # Restore App Service secrets. Provide the path to the App Service secrets file copied from backup. For example, C:\temp\SystemSecrets.json.
    # Press ENTER when prompted to reconfigure App Service from backup 

    # If necessary, use -OverrideDatabaseServer <restored server> with Restore-AppServiceStamp when the restored database server has a different address than backed-up deployment.
    # If necessary, use -OverrideContentShare <restored file share path> with Restore-AppServiceStamp when the restored file share has a different path from backed-up deployment.
    Restore-AppServiceStamp -FilePath $pathToExportedSecretFile 

    # Restore App Service roles
    Restore-AppServiceRoles

    # Restart App Service services
    net start WebFarmService
    net start ResourceMetering
    net start HostingVssService  # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # After App Service has successfully restarted, and at least one management server is in ready state, synchronize App Service objects to complete the restore
    # Enter Y when prompted to get all sites and again for all ServerFarm entities.
    Get-AppServiceSite | Sync-AppServiceObject
    Get-AppServiceServerFarm | Sync-AppServiceObject
    ```

> [!TIP]
> Po dokončení příkazu se důrazně doporučuje tuto relaci PowerShellu zavřít.

## <a name="restore-function-apps"></a>Obnovení aplikací Function App 
App Service pro centrum Azure Stack nepodporuje obnovování uživatelských aplikací tenanta nebo dat kromě obsahu sdílení souborů. Všechna ostatní data musí být zálohována a obnovena mimo App Service operací zálohování a obnovení. Pokud se pro úložiště Function Apps použilo úložiště centra Azure Stack, je potřeba provést následující kroky pro obnovení ztracených dat:

1. Vytvořte nový účet úložiště, který bude Function App používat. Toto úložiště může být Azure Stack úložiště centra, úložiště Azure nebo jakékoli kompatibilní úložiště.
2. Načtěte připojovací řetězec pro úložiště.
3. Otevřete portál funkcí a přejděte do aplikace Function App.
4. Přejděte na kartu **funkce platformy** a klikněte na **nastavení aplikace**.
5. Změňte **AzureWebJobsDashboard** a **AzureWebJobsStorage** na nový připojovací řetězec a klikněte na **Uložit**.
6. Přepněte na **Přehled**.
7. Restartujte aplikaci. Vymazání všech chyb může trvat několik pokusů.

## <a name="next-steps"></a>Další kroky
[Přehled App Service v centru Azure Stack](azure-stack-app-service-overview.md)