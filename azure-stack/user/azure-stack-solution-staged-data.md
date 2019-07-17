---
title: Nasazení řešení pro analýzu pracovních dat do služby Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak nasadit řešení pro analýzu pracovních dat do služby Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 06/20/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: ca4c2480fff511ab3bad43ea82fc81522d9afba0
ms.sourcegitcommit: 2a4cb9a21a6e0583aa8ade330dd849304df6ccb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286750"
---
# <a name="deploy-a-staged-data-analytics-solution-to-azure-stack"></a>Nasazení řešení pro analýzu pracovních dat do služby Azure Stack

Tento článek vám ukáže postup nasazení řešení pro shromažďování dat, která vyžaduje analýzu místě kolekce tak, aby provádět rychlé rozhodování. Toto shromažďování dat často dochází k dispozici žádné internetové připojení. Když se naváže spojení, budete muset provádět náročné analýzu dat umožňuje získat další informace.

V tomto řešení vytvoříte ukázkové prostředí:

> [!div class="checklist"]
> - Vytvoření objektu blob úložiště nezpracovaná data.
> - Vytvořte novou funkci Azure Stack můžete do Azure přesunout vyčištění dat ze služby Azure Stack.
> - Vytvoření funkce aktivované úložiště Blob.
> - Vytvoření účtu úložiště Azure Stack, který obsahuje objekt blob a fronty.
> - Vytvoření funkce aktivované frontou.
> - Funkce aktivovaná testu do fronty.

> [!Tip]  
> ![hybridní pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack je rozšířením Azure. Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí, povolení ten jediný hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace kdekoli.  
> 
> Tento článek [aspekty návrhu pro hybridní aplikace](azure-stack-edge-pattern-overview.md) kontroly pro navrhování, nasazování a provozování hybridní pilířů kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) aplikace. Aspekty návrhu při optimalizaci návrhu hybridních aplikací, minimalizovat problémy v produkčním prostředí.

## <a name="architecture-for-staged-data-analytics"></a>Architektura pro analýzu dat dvoufázové instalace

![Analýza dat dvoufázové instalace](media/azure-stack-solution-staged-data/image1.png)

## <a name="prerequisites-for-staged-data-analytics"></a>Požadavky pro analýzu dat dvoufázové instalace

  - Předplatné Azure.
  - Instanční objekt Azure Active Directory (AAD), který má oprávnění pro tenanta předplatného Azure a Azure Stack. Budete muset vytvořit dvě instanční objekty Azure Stack je použití jiného tenanta AAD, než vaše předplatné Azure. Informace o vytvoření instančního objektu pro Azure Stack, [vytvoření instančních objektů poskytnout aplikace přístup k prostředkům Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).
      - **Poznamenejte si ID aplikace každý instanční objekt služby, tajný klíč klienta, ID Tenanta služby Azure AD a název tenanta (xxxxx.onmicrosoft.com).**
  - Musíte kvůli shromažďování dat pro analýzu dat. Nejsou k dispozici ukázková data.
  - [Docker pro Windows](https://docs.docker.com/docker-for-windows/) nainstalované na místním počítači.

## <a name="get-the-docker-image"></a>Získat image Dockeru

Image dockeru pro každé nasazení eliminovat problémy s závislostí mezi různými verzemi nástroje Azure Powershellu.
1.  Ujistěte se, že je Docker pro Windows pomocí kontejnerů Windows.
2.  Spusťte následující v příkazovém řádku se zvýšenými oprávněními pro získání kontejneru Dockeru se skripty nasazení.

```
 docker pull intelligentedge/stageddatasolution:1.0.0
```

## <a name="deploy-the-solution"></a>Nasazení řešení

1.  Po image kontejneru byla úspěšně stažena, spusťte na obrázku.

      ```powershell  
      docker run -it intelligentedge/stageddatasolution:1.0.0 powershell
      ```

2.  Po zahájení kontejneru, budete mít se zvýšenými oprávněními terminálu prostředí PowerShell v kontejneru. Změňte adresáře na získat skript nasazení.

      ```powershell  
      cd .\SDDemo\
      ```

3.  Spusťte nasazení. Zadejte přihlašovací údaje a prostředků příslušných místech názvy. HA odkazuje na službě Azure Stack, kam se nasadí cluster vysokou dostupnost a zotavení po Havárii do Azure stacku, kam se nasadí cluster zotavení po Havárii.

      ```powershell
      .\DeploySolution-Azure-AzureStack.ps1 `
      -AzureApplicationId "applicationIDforAzureServicePrincipal" `
      -AzureApplicationSercet "clientSecretforServicePrincipal" `
      -AzureTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -AzureStackAADTenantName "azurestacktenant.onmicrosoft.com" `
      -AzureStackTenantARMEndpoint "https://management.haazurestack.com" `
      -AzureStackApplicationId "applicationIDforStackServicePrincipal" `
      -AzureStackApplicationSercet "ClientSecretforStackServicePrincipal" `
      -AzureStackTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -ResourcePrefix "aPrefixForResources"
      ```

1.  Pokud se zobrazí výzva; Zadejte oblast pro nasazení v Azure a Application Insights.

2.  Zadejte "Y" umožňující poskytovateli NuGet k instalaci, která se spustí řízený moduly "2018-03-01hybridní" profil rozhraní API, které chcete možné povolit pro nasazení do Azure a Azure Stack.

3.  Po nasazení prostředků otestujte, že data se vygeneruje pro Azure Stack a Azure.

    ```powershell  
      .\TDAGenerator.exe
    ```

4.  Zobrazit data zpracovává tak, že přejdete do webové aplikace nasazené do Azure nebo ve službě Azure Stack.

### <a name="azure-web-app"></a>Webové aplikace Azure
 
![řešení pro analýzu dat dvoufázové instalace](media/azure-stack-solution-staged-data/image2.png)
 
### <a name="azure-stack-web-app"></a>Webové aplikace Azure Stack
 
![řešení pro analýzu dat připravené pro službu Azure Stack](media/azure-stack-solution-staged-data/image3.png)

## <a name="next-steps"></a>Další postup

  - Další informace o hybridních cloudových aplikací, přečtěte si téma [hybridní Cloudová řešení.](https://aka.ms/azsdevtutorials)

  - Použít vlastní data nebo upravit kód pro tuto ukázku na [Githubu](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
