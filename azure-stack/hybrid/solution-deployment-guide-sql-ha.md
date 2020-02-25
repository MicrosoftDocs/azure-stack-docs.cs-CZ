---
title: Nasazení skupiny dostupnosti SQL Server 2016 do Azure a centra Azure Stack
description: Přečtěte si, jak nasadit skupinu dostupnosti SQL Server 2016 do Azure a centrum Azure Stack
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: f1cb202a30e381abc498ba950820d5fbefca9e2a
ms.sourcegitcommit: a7db4594de43c31fe0c51e60e84fdaf4d41ef1bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2020
ms.locfileid: "77568465"
---
# <a name="deploy-a-sql-server-2016-availability-group-to-azure-and-azure-stack-hub"></a>Nasazení skupiny dostupnosti SQL Server 2016 do Azure a centra Azure Stack

Tento článek vás provede automatizovaným nasazením základního vysoce dostupného clusteru s vysokou dostupností (HA) SQL Server 2016 Enterprise s asynchronním serverem pro zotavení po havárii ve dvou Azure Stack hub prostředích. Další informace o SQL Server 2016 a vysoké dostupnosti najdete v tématu [skupiny dostupnosti Always On: řešení zotavení po havárii s vysokou dostupností](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2016).

V tomto řešení vytvoříte ukázkové prostředí pro:

> [!div class="checklist"]
> - Orchestrace nasazení v rámci dvou Azure Stack Center
> - Minimalizace problémů se závislostmi pomocí profilů rozhraní Azure API pomocí Docker
> - Nasazení základního vysoce dostupného clusteru SQL Server 2016 Enterprise s lokalitou pro obnovení po havárii

> [!Tip]  
> ![Hybrid-Pillars. png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Centrum Microsoft Azure Stack je rozšířením Azure. Centrum Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí. tím se umožní jenom hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace odkudkoli.  
> 
> Požadavky na [Návrh pro hybridní aplikace](overview-app-design-considerations.md) kontrolují pilíře kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) pro navrhování, nasazování a provozování hybridních aplikací. Pokyny k návrhu pomáhají při optimalizaci návrhu hybridní aplikace a minimalizaci výzev v produkčních prostředích.

## <a name="architecture-for-sql-server-2016"></a>Architektura pro SQL Server 2016

![Centrum Azure Stack SQL Server 2016 SQL HA](media/solution-deployment-guide-sql-ha/image1.png)

## <a name="prerequisites-for-sql-server-2016"></a>Předpoklady pro SQL Server 2016

  - Dva připojené systémy integrovaných Azure Stack hub (centrum Azure Stack) Toto nasazení nefunguje v sadách Azure Stack Development Kit (ASDKs). Další informace o centru Azure Stack najdete v tématu [co je Azure Stack hub?](https://azure.microsoft.com/overview/azure-stack/).
  - Předplatné tenanta v každém centru Azure Stack.    
      - **Poznamenejte si každé ID předplatného a Azure Resource Manager koncový bod pro každé centrum Azure Stack.**
  - Instanční objekt služby Azure Active Directory (Azure AD), který má oprávnění k předplatnému tenanta pro každé centrum Azure Stack. Pokud jsou centra Azure Stack nasazená v různých klientech služby Azure AD, možná budete muset vytvořit dva instanční objekty. Informace o tom, jak vytvořit instanční objekt pro centrum Azure Stack, najdete v tématu [Vytvoření instančních objektů a udělení přístupu aplikacím k prostředkům služby Azure Stack hub](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).
      - **Poznamenejte si ID aplikace, tajný klíč klienta a název tenanta (xxxxx.onmicrosoft.com) daného instančního objektu.**
  - SQL Server 2016 Enterprise se do každého tržiště centra Azure Stack zasyndikátoval. Další informace o syndikaci služby Marketplace najdete v tématu [stažení položek Marketplace z Azure do centra Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
    **Ujistěte se, že má vaše organizace odpovídající licence SQL.**
  - [Docker for Windows](https://docs.docker.com/docker-for-windows/) nainstalované na místním počítači.

## <a name="get-the-docker-image"></a>Získat image Docker

Image Docker pro každé nasazení eliminují problémy závislosti mezi různými verzemi Azure PowerShell.

1.  Ujistěte se, že Docker for Windows používá kontejnery Windows.
2.  Spuštěním následujícího skriptu na příkazovém řádku se zvýšenými oprávněními Získejte kontejner Docker se skripty nasazení.

```powershell  
 docker pull intelligentedge/sqlserver2016-hadr:1.0.0
```

## <a name="deploy-the-availability-group"></a>Nasazení skupiny dostupnosti

1.  Po úspěšném dokončení image kontejneru spusťte image.

      ```powershell  
      docker run -it intelligentedge/sqlserver2016-hadr:1.0.0 powershell
      ```

2.  Po spuštění kontejneru se v kontejneru udělí terminál PowerShellu se zvýšenými oprávněními. Změňte adresáře tak, aby se získaly do skriptu nasazení.

      ```powershell  
      cd .\SQLHADRDemo\
      ```

3.  Spusťte nasazení. Zadejte přihlašovací údaje a názvy prostředků tam, kde je to potřeba. HA odkazuje na centrum Azure Stack, ve kterém se cluster HA nasadí, a DR do centra Azure Stack, kde se nasadí cluster DR.

      ```powershell
      > .\Deploy-AzureResourceGroup.ps1 `
      -AzureStackApplicationId_HA "applicationIDforHAServicePrincipal" `
      -AzureStackApplicationSercet_HA "clientSecretforHAServicePrincipal" `
      -AADTenantName_HA "hatenantname.onmicrosoft.com" `
      -AzureStackResourceGroup_HA "haresourcegroupname" `
      -AzureStackArmEndpoint_HA "https://management.haazurestack.com" `
      -AzureStackSubscriptionId_HA "haSubscriptionId" `
      -AzureStackApplicationId_DR "applicationIDforDRServicePrincipal" `
      -AzureStackApplicationSercet_DR "ClientSecretforDRServicePrincipal" `
      -AADTenantName_DR "drtenantname.onmicrosoft.com" `
      -AzureStackResourceGroup_DR "drresourcegroupname" `
      -AzureStackArmEndpoint_DR "https://management.drazurestack.com" `
      -AzureStackSubscriptionId_DR "drSubscriptionId"
      ```

4.  Zadejte `Y`, aby se mohl nainstalovat poskytovatel NuGet. tím se zahájí instalace modulů rozhraní API "2018-03-01-hybrid".

5.  Počkejte, až se nasazení prostředků dokončí.

6.  Po dokončení nasazení prostředků DR se kontejner ukončí.

      ```powershell
      exit
      ```

7.  Prozkoumejte nasazení zobrazením prostředků na portálu centra Azure Stack. Připojte se k některé z instancí SQL v prostředí HA a prozkoumejte skupinu dostupnosti prostřednictvím SQL Server Management Studio (SSMS).

![SQL Server 2016 SQL HA](media/solution-deployment-guide-sql-ha/image2.png)

## <a name="next-steps"></a>Další kroky

  - Použití SQL Server Management Studio k ručnímu převzetí služeb při selhání clusteru najdete v tématu [provedení vynuceného ručního převzetí služeb při selhání skupiny dostupnosti Always On (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server?view=sql-server-2017) .
  - Další informace o hybridních cloudových aplikacích najdete v tématu [hybridní cloudová řešení.](https://aka.ms/azsdevtutorials)
  - Použijte vlastní data nebo upravte kód v této ukázce na [GitHubu](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
