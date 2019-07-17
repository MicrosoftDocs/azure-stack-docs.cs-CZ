---
title: Nasadit skupiny dostupnosti SQL serveru 2016 do Azure a Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak nasadit skupiny dostupnosti SQL serveru 2016 do Azure a Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: solution
ms.date: 06/20/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 4565615caf4e4c13bda84e6596e23d523225d888
ms.sourcegitcommit: 2a4cb9a21a6e0583aa8ade330dd849304df6ccb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286901"
---
# <a name="deploy-a-sql-server-2016-availability-group-to-azure-and-azure-stack"></a>Nasadit skupiny dostupnosti SQL serveru 2016 do Azure a Azure Stack

Tento článek vás postupně prostřednictvím automatizovaných nasazení základní clusteru s vysokou dostupností (HA) SQL serveru 2016 Enterprise s webem zotavení po havárii asynchronní napříč dvěma prostředími Azure Stack. Další informace o SQL serveru 2016 a vysoké dostupnosti najdete v tématu [skupin dostupnosti Always On: řešení vysoké dostupnosti a zotavení po havárii](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2016).

V tomto řešení vytvoříte ukázkové prostředí:

> [!div class="checklist"]
> - Orchestrace nasazení napříč dvěma zásobníky Azure
> - Použití Docker, abyste minimalizovali problémy s závislostí pomocí profilů rozhraní API Azure
> - Nasazení základní clusteru SQL serveru 2016 Enterprise s vysokou dostupností s lokalitou pro zotavení po havárii

> [!Tip]  
> ![hybridní pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack je rozšířením Azure. Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí, povolení ten jediný hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace kdekoli.  
> 
> Tento článek [aspekty návrhu pro hybridní aplikace](azure-stack-edge-pattern-overview.md) kontroly pro navrhování, nasazování a provozování hybridní pilířů kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) aplikace. Aspekty návrhu při optimalizaci návrhu hybridních aplikací, minimalizovat problémy v produkčním prostředí.

## <a name="architecture-for-sql-server-2016"></a>Architektura pro SQL Server 2016

![SQL Server 2016 SQL vysokou dostupnost Azure Stack](media/azure-stack-solution-sql-ha/image1.png)

## <a name="prerequisites-for-sql-server-2016"></a>Požadavky pro SQL Server 2016

  - Dva propojené Stack integrované systémy ve službě Azure (Azure Stack), toto nasazení nelze použít u Azure Stack Development Kit (ASDKs). Další informace o službě Azure Stack, najdete v článku [co je Azure Stack?](https://azure.microsoft.com/overview/azure-stack/).
  - Předplatné klienta v jednotlivých Azure stacku.    
      - **Poznamenejte každé ID předplatného a koncový bod Azure Resource Manageru pro každé služby Azure Stack.**
  - Objekt služby Azure Active Directory (Azure AD), který má oprávnění k předplatnému klienta v jednotlivých Azure stacku. Budete muset vytvoření dvou instančních objektů v případě zásobníky Azure se nasazují proti různých služeb Azure AD tenantů. Zjistěte, jak vytvořit instanční objekt pro Azure Stack, najdete v článku [vytvoření instančních objektů poskytnout aplikace přístup k prostředkům Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).
      - **Poznamenejte si ID aplikace, tajný klíč klienta a název tenanta (xxxxx.onmicrosoft.com) každý instanční objekt služby.**
  - SQL Server 2016 Enterprise syndikovat do Tržiště Azure Stack. Další informace o marketplace syndikace, naleznete v tématu [stažení položek z marketplace z Azure do služby Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
    **Ujistěte se, že vaše organizace má příslušné licence SQL.**
  - [Docker pro Windows](https://docs.docker.com/docker-for-windows/) nainstalované na místním počítači.

## <a name="get-the-docker-image"></a>Získat image Dockeru

Image dockeru pro každé nasazení eliminovat problémy s závislostí mezi různými verzemi nástroje Azure Powershellu.

1.  Ujistěte se, že je Docker pro Windows pomocí kontejnerů Windows.
2.  Spusťte následující v příkazovém řádku se zvýšenými oprávněními pro získání kontejneru Dockeru se skripty nasazení.

```powershell  
 docker pull intelligentedge/sqlserver2016-hadr:1.0.0
```

## <a name="deploy-the-availability-group"></a>Nasazení skupiny dostupnosti

1.  Po image kontejneru byla úspěšně stažena, spusťte na obrázku.

      ```powershell  
      docker run -it intelligentedge/sqlserver2016-hadr:1.0.0 powershell
      ```

2.  Po zahájení kontejneru, budete mít se zvýšenými oprávněními terminálu prostředí PowerShell v kontejneru. Změňte adresáře na získat skript nasazení.

      ```powershell  
      cd .\SQLHADRDemo\
      ```

3.  Spusťte nasazení. Zadejte přihlašovací údaje a prostředků příslušných místech názvy. HA odkazuje na službě Azure Stack, kam se nasadí cluster vysokou dostupnost a zotavení po Havárii do Azure stacku, kam se nasadí cluster zotavení po Havárii.

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

4.  Typ `Y` umožňující NuGet zprostředkovatele, který se nainstalují, která se spustí řízený profil rozhraní API "2018-03-01hybridní" moduly, které chcete nainstalovat.

5.  Vyčkat, než k dokončení nasazení prostředků.

6.  Po dokončení nasazení zotavení po Havárii prostředků, ukončete kontejneru.

      ```powershell
      exit
      ```

7.  Zkontrolujte nasazení zobrazením prostředků na portálu Azure Stack. Připojte se k jedné z instancí SQL na vysokou dostupnost prostředí a zkontrolujete skupinu dostupnosti pomocí SQL Server Management Studio (SSMS).

![SQL Server 2016 SQL HA](media/azure-stack-solution-sql-ha/image2.png)

## <a name="next-steps"></a>Další postup

  - Pomocí SQL Server Management Studio ruční převzetí služeb při selhání clusteru, přečtěte si téma [provést vynucené ruční převzetí služeb při vždy na skupiny dostupnosti (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server?view=sql-server-2017)
  - Další informace o hybridních cloudových aplikací, přečtěte si téma [hybridní Cloudová řešení.](https://aka.ms/azsdevtutorials)
  - Použít vlastní data nebo upravit kód pro tuto ukázku na [Githubu](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
