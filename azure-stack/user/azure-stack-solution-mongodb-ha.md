---
title: Nasazení vysoce dostupného řešení MongoDB do Azure a Azure Stack | Microsoft Docs
description: Naučte se nasadit řešení MongoDB s vysokou dostupností do Azure a Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 25be3914f58abad44b64870f9da94610a7498d52
ms.sourcegitcommit: 35b13ea6dc0221a15cd0840be796f4af5370ddaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68603034"
---
# <a name="deploy-a-highly-available-mongodb-solution-to-azure-and-azure-stack"></a>Nasazení vysoce dostupného řešení MongoDB do Azure a Azure Stack

Tento článek vás provede automatizovaným nasazením clusteru MongoDB s vysokou dostupností (HA) s lokalitou zotavení po havárii (DR) v rámci dvou Azure Stackch prostředí. Další informace o MongoDB a vysoké dostupnosti najdete v tématu věnovaném [členům sady replik](https://docs.mongodb.com/manual/core/replica-set-members/).

V tomto řešení vytvoříte ukázkové prostředí pro:

> [!div class="checklist"]
> - Orchestrace nasazení napříč dvěma zásobníky Azure
> - Minimalizace problémů se závislostmi pomocí profilů rozhraní Azure API pomocí Docker
> - Nasazení základního vysoce dostupného clusteru MongoDB s lokalitou pro zotavení po havárii


> [!Tip]  
> ![hybridní pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack je rozšířením Azure. Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí a umožňuje jenom hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace odkudkoli.  
> 
> Požadavky na [Návrh pro hybridní aplikace](azure-stack-edge-pattern-overview.md) kontrolují pilíře kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) pro navrhování, nasazování a provozování hybridních aplikací. Pokyny k návrhu pomáhají při optimalizaci návrhu hybridní aplikace a minimalizaci výzev v produkčních prostředích.



## <a name="architecture-for-mongodb-with-azure-stack"></a>Architektura pro MongoDB s Azure Stack

![vysoce dostupné MongoDB v Azure Stack](media/azure-stack-solution-mongdb-ha/image1.png)

## <a name="prerequisites-for-mongodb-with-azure-stack"></a>Předpoklady pro MongoDB s Azure Stack

  - Dva připojené Azure Stack integrované systémy (Azure Stack) Toto nasazení nefunguje v sadách Azure Stack Development Kit (ASDKs). Další informace o Azure Stack najdete v tématu [co je Azure Stack?](https://azure.microsoft.com/overview/azure-stack/)
      - Předplatné tenanta na každé Azure Stack.    
      - **Poznamenejte si každé ID předplatného a Azure Resource Manager koncový bod pro každé Azure Stack.**
  - Instanční objekt služby Azure Active Directory (Azure AD), který má oprávnění k předplatnému tenanta u každého Azure Stack. Je možné, že budete muset vytvořit dva instanční objekty, pokud jsou služby Azure Stacks nasazeny pro různé klienty Azure AD. Informace o tom, jak vytvořit instanční objekt pro Azure Stack, najdete v tématu [Vytvoření instančních objektů, které aplikacím umožňují přístup k prostředkům Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).    
      - **Poznamenejte si ID aplikace, tajný klíč klienta a název tenanta (xxxxx.onmicrosoft.com) daného instančního objektu.**
  - Ubuntu 16,04 se zaAzure Stack do každého tržiště. Další informace o syndikaci na webu Marketplace najdete v tématu [stažení položek Marketplace z Azure do Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
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

3.  Spusťte nasazení. Zadejte přihlašovací údaje a názvy prostředků tam, kde je to potřeba. HA odkazuje na Azure Stack, kde bude nasazen cluster HA, a DR do Azure Stack, kde bude nasazen cluster DR.

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

4.  Zadejte `Y` , pokud chcete, aby se nainstaloval poskytovatel NuGet, který se aktivuje z profilu rozhraní API "2018-03-01-hybrid" modulů, které se mají nainstalovat.

5.  Nejprve se nasadí prostředky HA. Monitorujte nasazení a počkejte, než se dokončí. Jakmile se zobrazí zpráva s oznámením, že nasazení HA bylo dokončeno, můžete na portálu HA Azure Stack zjistit, jestli jsou nasazené prostředky. 

6.  Pokračujte v nasazení prostředků zotavení po havárii a rozhodněte se, jestli chcete povolit pole pro skok na Azure Stack DR pro interakci s clusterem.

7.  Počkejte, než se dokončí nasazení prostředků DR.

8.  Po dokončení nasazení prostředků DR se kontejner ukončí.

  ```powershell
  exit
  ```

## <a name="next-steps"></a>Další postup

  - Pokud jste povolili virtuální počítač skokového pole na Azure Stack DR, můžete se připojit přes SSH a komunikovat s clusterem MongoDB instalací rozhraní příkazového řádku Mongo. Další informace o interakci s MongoDB najdete v tématu [prostředí Mongo](https://docs.mongodb.com/manual/mongo/).

  - Další informace o hybridních cloudových aplikacích najdete v tématu [hybridní cloudová řešení.](https://aka.ms/azsdevtutorials)

  - Upravte kód na tuto ukázku na [GitHubu](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
