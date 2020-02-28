---
title: Nasazení vysoce dostupného řešení MongoDB do Azure a centra Azure Stack
description: Naučte se nasadit řešení MongoDB s vysokou dostupností do Azure a centra Azure Stack.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: d9a0447938c44eec90d1d5cb2898e950cddbd7bf
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77701373"
---
# <a name="deploy-a-highly-available-mongodb-solution-to-azure-and-azure-stack-hub"></a>Nasazení vysoce dostupného řešení MongoDB do Azure a centra Azure Stack

Tento článek vás provede automatizovaným nasazením clusteru MongoDB s vysokou dostupností (HA) s lokalitou zotavení po havárii (DR) v rámci dvou Azure Stack hub prostředí. Další informace o MongoDB a vysoké dostupnosti najdete v tématu věnovaném [členům sady replik](https://docs.mongodb.com/manual/core/replica-set-members/).

V tomto řešení vytvoříte ukázkové prostředí pro:

> [!div class="checklist"]
> - Orchestrace nasazení v rámci dvou Azure Stack Center
> - Minimalizace problémů se závislostmi pomocí profilů rozhraní Azure API pomocí Docker
> - Nasazení základního vysoce dostupného clusteru MongoDB s lokalitou pro zotavení po havárii


> [!Tip]  
> ![Hybrid-Pillars. png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Centrum Microsoft Azure Stack je rozšířením Azure. Centrum Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí. tím se umožní jenom hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace odkudkoli.  
> 
> Požadavky na [Návrh pro hybridní aplikace](overview-app-design-considerations.md) kontrolují pilíře kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) pro navrhování, nasazování a provozování hybridních aplikací. Pokyny k návrhu pomáhají při optimalizaci návrhu hybridní aplikace a minimalizaci výzev v produkčních prostředích.



## <a name="architecture-for-mongodb-with-azure-stack-hub"></a>Architektura pro MongoDB s rozbočovačem Azure Stack

![vysoce dostupné MongoDB v centru Azure Stack](media/solution-deployment-guide-mongodb-ha/image1.png)

## <a name="prerequisites-for-mongodb-with-azure-stack-hub"></a>Předpoklady pro MongoDB s rozbočovačem Azure Stack

  - Dva připojené systémy integrovaných Azure Stack hub (centrum Azure Stack) Toto nasazení nefunguje v sadách Azure Stack Development Kit (ASDKs). Další informace o centru Azure Stack najdete v tématu [co je Azure Stack hub?](https://azure.microsoft.com/overview/azure-stack/)
      - Předplatné tenanta v každém centru Azure Stack.    
      - **Poznamenejte si každé ID předplatného a Azure Resource Manager koncový bod pro každé centrum Azure Stack.**
  - Instanční objekt služby Azure Active Directory (Azure AD), který má oprávnění k předplatnému tenanta pro každé centrum Azure Stack. Pokud jsou centra Azure Stack nasazená v různých klientech služby Azure AD, možná budete muset vytvořit dva instanční objekty. Informace o tom, jak vytvořit instanční objekt pro centrum Azure Stack, najdete v tématu [Vytvoření instančních objektů a udělení přístupu aplikacím k prostředkům služby Azure Stack hub](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).    
      - **Poznamenejte si ID aplikace, tajný klíč klienta a název tenanta (xxxxx.onmicrosoft.com) daného instančního objektu.**
  - Ubuntu 16,04 se zaAzure Stack do každého tržiště centra. Další informace o syndikaci služby Marketplace najdete v tématu [stažení položek Marketplace z Azure do centra Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
  - [Docker for Windows](https://docs.docker.com/docker-for-windows/) nainstalované na místním počítači.

## <a name="get-the-docker-image"></a>Získat image Docker

Image Docker pro každé nasazení eliminují problémy závislosti mezi různými verzemi Azure PowerShell.
1.  Ujistěte se, že Docker for Windows používá kontejnery Windows.
2.  Spuštěním následujícího příkazu na příkazovém řádku se zvýšenými oprávněními Získejte kontejner Docker se skripty nasazení.
```powershell  
docker pull intelligentedge/mongodb-hadr:1.0.0
```

## <a name="deploy-the-clusters"></a>Nasazení clusterů

1.  Po úspěšném dokončení image kontejneru spusťte image. \

    ```powershell  
    docker run -it intelligentedge/mongodb-hadr:1.0.0 powershell
    ```

2.  Po spuštění kontejneru se v kontejneru udělí terminál PowerShellu se zvýšenými oprávněními. Změňte adresáře tak, aby se získaly do skriptu nasazení.

    ```powershell  
    cd .\MongoHADRDemo\
    ```

3.  Spusťte nasazení. Zadejte přihlašovací údaje a názvy prostředků tam, kde je to potřeba. HA odkazuje na centrum Azure Stack, ve kterém se cluster HA nasadí, a DR do centra Azure Stack, kde se nasadí cluster DR.

    ```powershell
    .\Deploy-AzureResourceGroup.ps1 `
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

5.  Nejprve se nasadí prostředky HA. Monitorujte nasazení a počkejte, než se dokončí. Jakmile se zobrazí zpráva s oznámením, že nasazení HA bylo dokončeno, můžete na portálu HA Azure Stack centra ověřit, jestli jsou nasazené prostředky. 

6.  Pokračujte v nasazení prostředků zotavení po havárii a rozhodněte se, jestli chcete povolit pole pro skok v Azure Stackovém centru DR pro interakci s clusterem.

7.  Počkejte, než se dokončí nasazení prostředků DR.

8.  Po dokončení nasazení prostředků DR se kontejner ukončí.

  ```powershell
  exit
  ```

## <a name="next-steps"></a>Další kroky

  - Pokud jste v Azure Stackovém centru pro zotavení po havárii povolili virtuální počítač se seznamem odkazů, můžete se připojit přes SSH a komunikovat s clusterem MongoDB instalací rozhraní příkazového řádku Mongo. Další informace o interakci s MongoDB najdete v tématu [prostředí Mongo](https://docs.mongodb.com/manual/mongo/).

  - Další informace o hybridních cloudových aplikacích najdete v tématu [hybridní cloudová řešení.](https://aka.ms/azsdevtutorials)

  - Upravte kód na tuto ukázku na [GitHubu](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
