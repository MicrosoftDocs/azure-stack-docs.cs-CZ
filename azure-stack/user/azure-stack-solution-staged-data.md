---
title: Nasazení řešení dvoufázové analýzy dat do Azure Stack | Microsoft Docs
description: Přečtěte si, jak nasadit řešení dvoufázové analýzy dat do Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.topic: conceptual
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 187fe88ad304804462cc4461b24eb10a7af7a299
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277731"
---
# <a name="deploy-a-staged-data-analytics-solution-to-azure-stack"></a>Nasazení řešení dvoufázové analýzy dat do Azure Stack

V tomto článku se dozvíte, jak nasadit řešení pro shromažďování dat, která v bodě shromažďování vyžadují analýzu, aby bylo možné provádět rychlé rozhodnutí. Tato kolekce dat často probíhá bez přístupu k Internetu. Po navázání připojení může být nutné provést analýzu dat náročných na prostředky, abyste získali další přehled.

V tomto řešení vytvoříte ukázkové prostředí pro:

> [!div class="checklist"]
> - Vytvořte objekt BLOB úložiště nezpracovaných dat.
> - Vytvořte novou funkci Azure Stack pro přesun čistých dat z Azure Stack do Azure.
> - Vytvoření funkce aktivované úložištěm objektů BLOB.
> - Vytvořte Azure Stack účet úložiště, který obsahuje objekt BLOB a frontu.
> - Vytvoření funkce aktivované frontou
> - Otestujte funkci aktivovanou ve frontě.

> [!Tip]  
> @no__t -0hybrid-Pillars. png @ no__t-1  
> Microsoft Azure Stack je rozšířením Azure. Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí a umožňuje jenom hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace odkudkoli.  
> 
> Požadavky na [Návrh pro hybridní aplikace](azure-stack-edge-pattern-overview.md) kontrolují pilíře kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) pro navrhování, nasazování a provozování hybridních aplikací. Pokyny k návrhu pomáhají při optimalizaci návrhu hybridní aplikace a minimalizaci výzev v produkčních prostředích.

## <a name="architecture-for-staged-data-analytics"></a>Architektura pro analýzu připravených dat

![Analýza připravených dat](media/azure-stack-solution-staged-data/image1.png)

## <a name="prerequisites-for-staged-data-analytics"></a>Předpoklady pro analýzu připravených dat

  - Předplatné Azure.
  - Instanční objekt služby Azure Active Directory (Azure AD), který má oprávnění k předplatnému tenanta v Azure a Azure Stack. Pokud Azure Stack používá jiného tenanta služby AAD než vaše předplatné Azure, možná budete muset vytvořit dva instanční objekty. Pokud se chcete dozvědět, jak vytvořit instanční objekt pro Azure Stack, přejděte v tématu [Vytvoření instančních objektů a poskytněte aplikacím přístup k prostředkům služby Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).
      - **Poznamenejte si ID aplikace, tajný klíč klienta, ID tenanta služby Azure AD a název tenanta (xxxxx.onmicrosoft.com).**
  - Budete muset zadat kolekci dat pro analýzu dat. Jsou k dispozici ukázková data.
  - [Docker for Windows](https://docs.docker.com/docker-for-windows/) nainstalované na místním počítači.

## <a name="get-the-docker-image"></a>Získat image Docker

Image Docker pro každé nasazení eliminují problémy závislosti mezi různými verzemi Azure PowerShell.
1.  Ujistěte se, že Docker for Windows používá kontejnery Windows.
2.  Spuštěním následujícího příkazu na příkazovém řádku se zvýšenými oprávněními Získejte kontejner Docker se skripty nasazení.

```
 docker pull intelligentedge/stageddatasolution:1.0.0
```

## <a name="deploy-the-solution"></a>Nasazení řešení

1.  Po úspěšném dokončení image kontejneru spusťte image.

      ```powershell  
      docker run -it intelligentedge/stageddatasolution:1.0.0 powershell
      ```

2.  Po spuštění kontejneru se v kontejneru udělí terminál PowerShellu se zvýšenými oprávněními. Změňte adresáře tak, aby se získaly do skriptu nasazení.

      ```powershell  
      cd .\SDDemo\
      ```

3.  Spusťte nasazení. Zadejte přihlašovací údaje a názvy prostředků tam, kde je to potřeba. HA odkazuje na Azure Stack, kde bude nasazen cluster HA, a DR do Azure Stack, kde bude nasazen cluster DR.

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

1.  Pokud se zobrazí výzva; Zadejte oblast pro nasazení Azure a Application Insights.

2.  Zadáním "Y" umožníte instalaci poskytovatele NuGet, čímž se zahájí instalace modulů rozhraní API "2018-03-01-hybrid", aby bylo možné nasadit nasazení do Azure a Azure Stack.

3.  Po nasazení prostředků otestujte, že se budou generovat data pro Azure Stack i pro Azure.

    ```powershell  
      .\TDAGenerator.exe
    ```

4.  Přečtěte si data, která jsou zpracovávána, pomocí přechodu na webové aplikace nasazené do Azure nebo Azure Stack.

### <a name="azure-web-app"></a>Webová aplikace Azure
 
![řešení dvoufázové analýzy dat](media/azure-stack-solution-staged-data/image2.png)
 
### <a name="azure-stack-web-app"></a>Azure Stack webové aplikace
 
![řešení dvoufázové analýzy dat pro Azure Stack](media/azure-stack-solution-staged-data/image3.png)

## <a name="next-steps"></a>Další kroky

  - Další informace o hybridních cloudových aplikacích najdete v tématu [hybridní cloudová řešení.](https://aka.ms/azsdevtutorials)

  - Použijte vlastní data nebo upravte kód v této ukázce na [GitHubu](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
