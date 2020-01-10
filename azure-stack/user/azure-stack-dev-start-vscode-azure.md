---
title: Připojení k centru Azure Stack pomocí rozšíření účtu Azure v Visual Studio Code | Microsoft Docs
description: Jako vývojář se můžete připojit k centru Azure Stack pomocí rozšíření účtu Azure v Visual Studio Code
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 62e334ca62d4fddcd14357f45f00d309e8440103
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75820521"
---
# <a name="connect-to-azure-stack-hub-using-azure-account-extension-in-visual-studio-code"></a>Připojení k centru Azure Stack pomocí rozšíření účtu Azure v Visual Studio Code

V tomto článku Vás provedeme postupem připojení k centru Azure Stack pomocí rozšíření účtu Azure. Budete muset aktualizovat nastavení Visual Studio Code (VS Code).

VS Code je zjednodušený editor pro sestavování a ladění webových a cloudových aplikací. ASP.NET Core, Python, NodeJS, přejít a další vývojáři používají VS Code. Pomocí rozšíření účtu Azure můžete použít jedno přihlášení Azure s filtrováním předplatného pro další rozšíření Azure. Rozšíření zpřístupňuje Azure Cloud Shell v terminálu integrovaném s VS Code. Pomocí tohoto rozšíření se můžete ke svému předplatnému centra Azure Stack připojit pomocí Azure AD (Azure AD) i služby Active Directory federovaných služeb (AD FS) pro správce identit. Můžete se přihlásit do centra Azure Stack, vybrat své předplatné a otevřít nový příkazový řádek ve službě cloud Shell. 

> [!Note]  
> Kroky v tomto článku můžete použít pro prostředí služby Active Directory federované služby (AD FS). Použijte své AD FS přihlašovací údaje a koncové body.

## <a name="pre-requisites-for-the-azure-account-extension"></a>Předpoklady pro rozšíření účtu Azure

1. Prostředí centra Azure Stack 1904 Build nebo novější
2. [Visual Studio Code](https://code.visualstudio.com/)
3. [Rozšíření účtu Azure](https://github.com/Microsoft/vscode-azure-account)
4. [Předplatné centra Azure Stack](https://azure.microsoft.com/overview/azure-stack/)

## <a name="steps-to-connect-to-azure-stack-hub"></a>Postup připojení k centru Azure Stack

1. Spusťte skript **identity** z Azure Stack nástrojů centra na GitHubu.

    - Před spuštěním skriptu budete muset mít nainstalovaný a nakonfigurovaný PowerShell pro vaše prostředí. Pokyny najdete v tématu [instalace PowerShellu pro Azure Stack hub](../operator/azure-stack-powershell-install.md).

    - Pokyny a skripty pro **identitu** najdete v tématu [AzureStack-Tools/identity](https://github.com/Azure/AzureStack-Tools/tree/master/Identity).

    - Ve stejné relaci spusťte:

    ```powershell  
    Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
    -DirectoryTenantName $homeDirectoryTenantName -Verbose
    Register-AzsWithMyDirectoryTenant -TenantResourceManagerEndpoint $tenantARMEndpoint `
    -DirectoryTenantName $guestDirectoryTenantName
    ```

2. Otevřete VS Code.

3. V levém horním rohu vyberte **rozšíření** .

4. Do vyhledávacího pole zadejte `Azure Account`.

5. Vyberte **účet Azure** a vyberte **nainstalovat**.

      ![Visual Studio Code centra Azure Stack](media/azure-stack-dev-start-vscode-azure/image1.png)

6. Pro načtení rozšíření restartujte VS Code.

7. Načtěte metadata pro připojení k Azure Resource Manager v centru Azure Stack. 
    
    Microsoft Azure Správce prostředků je rozhraní pro správu, které umožňuje nasazovat, spravovat a monitorovat prostředky Azure.
    - Správce prostředků adresa URL pro Azure Stack Development Kit (ASDK) je: `https://management.local.azurestack.external/` 
    - Adresa URL Správce prostředků pro integrovaný systém je: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`
    - Přidejte do adresy URL následující text pro přístup k metadatům: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

    Například adresa URL pro načtení metadat pro váš koncový bod Azure Resource Manager může vypadat přibližně takto: `https://management.local.azurestack.external/metadata/endpoints?api-version=1.0`

    Poznamenejte si návratový kód JSON. Budete potřebovat hodnoty pro vlastnost `loginEndpoint` a `audiences`.

8. Stiskněte **kombinaci kláves CTRL + SHIFT + P** a vyberte **Předvolby: otevřít uživatelské nastavení (JSON)** .

9. V editoru kódu aktualizujte následující fragment kódu JSON hodnotami pro vaše prostředí a vložte fragment kódu do bloku nastavení.

    - Hodnoty:

        | Parametr | Popis |
        | --- | --- |
        | `tenant-ID` | Hodnota [ID tenanta](../operator/azure-stack-identity-overview.md)centra Azure Stack. |
        | `activeDirectoryEndpointUrl` | Toto je adresa URL z vlastnosti loginEndpoint. |
        | `activeDirectoryResourceId` | Toto je adresa URL z vlastnosti cílové skupiny.
        | `resourceManagerEndpointUrl` | Toto je kořenová adresa URL Azure Resource Manager pro centrum Azure Stack. | 

    - Fragment kódu JSON:

      ```JSON  
      "azure.tenant": "tenant-ID",
      "azure.ppe": {
          "activeDirectoryEndpointUrl": "Login endpoint",
          "activeDirectoryResourceId": "This is the URL from the audiences property.",
          "resourceManagerEndpointUrl": "Aure Resource Management Endpoint",
      },
      "azure.cloud": "AzurePPE"
      ```

10. Uložte nastavení uživatele a znovu použijte **kombinaci kláves CTRL + SHIFT + P** . Vyberte **Azure: Přihlaste se ke cloudu Azure**. V seznamu cílů se zobrazí nová možnost **AzurePPE**.

11. Vyberte **AzurePPE**. Ověřovací stránka se načte v prohlížeči. Přihlaste se ke svému koncovému bodu.

12. K otestování úspěšného přihlášení k předplatnému centra Azure Stack použijte **kombinaci kláves CTRL + SHIFT + P** a vyberte **Azure: vyberte předplatné** a podívejte se, jestli je předplatné k dispozici.

## <a name="commands"></a>Příkazy

| Azure: přihlásit se | Přihlaste se ke svému předplatnému Azure. |
| --- | --- |
| Azure: přihlášení pomocí kódu zařízení | Přihlaste se ke svému předplatnému Azure pomocí kódu zařízení. V instalačních sestavách použijte kód zařízení, kde příkaz Sign in nefunguje. |
| Azure: Přihlaste se ke cloudu Azure. | Přihlaste se k předplatnému Azure v jednom z cloudů z svrchovaného prostředí. |
| Azure: odhlásit se | Odhlaste se od předplatného Azure. |
| Azure: výběr předplatných | Vyberte sadu odběrů, se kterými chcete pracovat. Rozšíření zobrazuje pouze prostředky v rámci filtrovaných odběrů. |
| Azure: vytvoření účtu | Pokud nemáte účet Azure, můžete si ho [zaregistrovat](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-azure-account&mktingSource=vscode-azure-account) ještě dnes a získat \$200 na bezplatné kredity. |
| Azure: Otevřete bash v Cloud Shell | V Cloud Shell otevřete nový terminál s operačním systémem bash. |
| Azure: Otevřete PowerShell v Cloud Shell | Otevřete v Cloud Shell nový terminál, na kterém běží PowerShell. |
| Azure: nahrání do Cloud Shell | Nahrajte soubor do svého účtu úložiště Cloud Shell. |

## <a name="next-steps"></a>Další kroky

[Nastavení vývojového prostředí v centru Azure Stack](azure-stack-dev-start.md)
