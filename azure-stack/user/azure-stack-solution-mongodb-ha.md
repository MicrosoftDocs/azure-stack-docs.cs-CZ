---
title: Nasazení vysoce dostupné řešení MongoDB do Azure a Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak nasadit řešení s vysokou dostupností MongoDB do Azure a Azure Stack
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
ms.openlocfilehash: aa48e3b40afc841a26f15ce06870d002261c5932
ms.sourcegitcommit: 2a4cb9a21a6e0583aa8ade330dd849304df6ccb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286842"
---
# <a name="deploy-a-highly-available-mongodb-solution-to-azure-and-azure-stack"></a>Nasazení vysoce dostupné řešení MongoDB do Azure a Azure Stack

Tento článek vás postupně prostřednictvím automatizovaných nasazení základní clusteru s vysokou dostupností (HA) MongoDB s lokalitou pro zotavení po havárii napříč dvěma prostředími Azure Stack. Další informace o MongoDB a vysoké dostupnosti najdete v tématu [členy sady replik](https://docs.mongodb.com/manual/core/replica-set-members/).

V tomto řešení vytvoříte ukázkové prostředí:

> [!div class="checklist"]
> - Orchestrace nasazení napříč dvěma zásobníky Azure
> - Použití Docker, abyste minimalizovali problémy s závislostí pomocí profilů rozhraní API Azure
> - Nasazení clusteru s vysokou dostupností základní MongoDB s lokalitou pro zotavení po havárii


> [!Tip]  
> ![hybridní pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack je rozšířením Azure. Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí, povolení ten jediný hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace kdekoli.  
> 
> Tento článek [aspekty návrhu pro hybridní aplikace](azure-stack-edge-pattern-overview.md) kontroly pro navrhování, nasazování a provozování hybridní pilířů kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) aplikace. Aspekty návrhu při optimalizaci návrhu hybridních aplikací, minimalizovat problémy v produkčním prostředí.



## <a name="architecture-for-mongodb-with-azure-stack"></a>Architektura pro MongoDB pomocí služby Azure Stack

![MongoDB s vysokou dostupností v Azure stacku](media/azure-stack-solution-mongdb-ha/image1.png)

## <a name="prerequisites-for-mongodb-with-azure-stack"></a>Předpoklady pro MongoDB pomocí služby Azure Stack

  - Dva propojené Stack integrované systémy ve službě Azure (Azure Stack), toto nasazení nelze použít u Azure Stack Development Kit (ASDKs). Další informace o službě Azure Stack, najdete v článku [co je Azure Stack?](https://azure.microsoft.com/overview/azure-stack/)
      - Předplatné klienta v jednotlivých Azure stacku.    
      - **Poznamenejte každé ID předplatného a koncový bod Azure Resource Manageru pro každé služby Azure Stack.**
  - Objekt služby Azure Active Directory (Azure AD), který má oprávnění k předplatnému klienta v jednotlivých Azure stacku. Budete muset vytvoření dvou instančních objektů v případě zásobníky Azure se nasazují proti různých služeb Azure AD tenantů. Zjistěte, jak vytvořit instanční objekt pro Azure Stack, najdete v článku [vytvoření instančních objektů poskytnout aplikace přístup k prostředkům Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).    
      - **Poznamenejte si ID aplikace, tajný klíč klienta a název tenanta (xxxxx.onmicrosoft.com) každý instanční objekt služby.**
  - Ubuntu 16.04 syndikovat do Tržiště Azure Stack. Další informace o marketplace syndikace, naleznete v tématu [stažení položek z marketplace z Azure do služby Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
  - [Docker pro Windows](https://docs.docker.com/docker-for-windows/) nainstalované na místním počítači.

## <a name="get-the-docker-image"></a>Získat image Dockeru

Image dockeru pro každé nasazení eliminovat problémy s závislostí mezi různými verzemi nástroje Azure Powershellu.
1.  Ujistěte se, že je Docker pro Windows pomocí kontejnerů Windows.
2.  Spusťte následující v příkazovém řádku se zvýšenými oprávněními pro získání kontejneru Dockeru se skripty nasazení.
```powershell  
docker pull intelligentedge/mongodb-hadr:1.0.0
```

## <a name="deploy-the-clusters"></a>Nasazení clusterů

1.  Po image kontejneru byla úspěšně stažena, spusťte na obrázku. \

    ```powershell  
    docker run -it intelligentedge/mongodb-hadr:1.0.0 powershell
    ```

2.  Po zahájení kontejneru, budete mít se zvýšenými oprávněními terminálu prostředí PowerShell v kontejneru. Změňte adresáře na získat skript nasazení.

    ```powershell  
    cd .\MongoHADRDemo\
    ```

3.  Spusťte nasazení. Zadejte přihlašovací údaje a prostředků příslušných místech názvy. HA odkazuje na službě Azure Stack, kam se nasadí cluster vysokou dostupnost a zotavení po Havárii do Azure stacku, kam se nasadí cluster zotavení po Havárii.

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

4.  Typ `Y` umožňující NuGet zprostředkovatele, který se nainstalují, která se spustí řízený profil rozhraní API "2018-03-01hybridní" moduly, které chcete nainstalovat.

5.  Prostředky HA se nejprve nasadit. Monitorujte nasazení a počkejte na její dokončení. Jakmile budete mít zpráva oznamující, že dokončení nasazení vysokou dostupnost, můžete zkontrolovat HA Azure Stack na portálu, abyste zjistili prostředky nasazené. 

6.  Pokračujte s nasazováním prostředků zotavení po Havárii a rozhodnout, jestli chcete povolit jump box v Azure stacku zotavení po Havárii pro interakci s clusterem.

7.  Vyčkat, než k dokončení nasazení prostředků zotavení po Havárii.

8.  Po dokončení nasazení zotavení po Havárii prostředků, ukončete kontejneru.

  ```powershell
  exit
  ```

## <a name="next-steps"></a>Další postup

  - Pokud jste povolili jump box virtuálních počítačů v Azure stacku zotavení po Havárii, můžete připojit přes SSH a interakci s clusterem MongoDB pomocí instalace rozhraní příkazového řádku mongo. Další informace o práci s MongoDB, naleznete v tématu [prostředí mongo](https://docs.mongodb.com/manual/mongo/).

  - Další informace o hybridních cloudových aplikací, přečtěte si téma [hybridní Cloudová řešení.](https://aka.ms/azsdevtutorials)

  - Upravit kód pro tuto ukázku na [Githubu](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).