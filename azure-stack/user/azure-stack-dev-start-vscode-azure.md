---
title: Připojení k Azure Stack pomocí rozšíření účtu Azure v Visual Studio Code | Microsoft Docs
description: Jako vývojář se můžete připojit k Azure Stack pomocí rozšíření účtu Azure v Visual Studio Code
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 08/05/2019
ms.openlocfilehash: 44d35d59b2b50682dd6911f6d2b08fea8e005938
ms.sourcegitcommit: a0dcb61890ad0f7b8e1f738f7186198681adcc2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68820818"
---
# <a name="connect-to-azure-stack-using-azure-account-extension-in-visual-studio-code"></a>Připojení k Azure Stack pomocí rozšíření účtu Azure v Visual Studio Code

V tomto článku Vás provedeme, jak se připojit k Azure Stack pomocí rozšíření účtu Azure. Budete muset aktualizovat nastavení Visual Studio Code (VS Code).

VS Code je zjednodušený editor pro sestavování a ladění webových a cloudových aplikací. ASP.NET Core, Python, NodeJS, přejít a další vývojáři používají VS Code. Pomocí rozšíření účtu Azure můžete použít jedno přihlášení Azure s filtrováním předplatného pro další rozšíření Azure. Rozšíření zpřístupňuje Azure Cloud Shell v terminálu integrovaném s VS Code. Pomocí tohoto rozšíření se můžete ke svému předplatnému Azure Stack připojit pomocí Azure AD (Azure AD) i služby Active Directory federovaných služeb (AD FS) pro správce identit. Můžete se přihlásit k Azure Stack, vybrat předplatné a otevřít nový příkazový řádek ve službě cloud Shell. 

> [!Note]  
> Kroky v tomto článku můžete použít pro prostředí služby Active Directory federované služby (AD FS). Použijte své AD FS přihlašovací údaje a koncové body.

## <a name="pre-requisites-for-the-azure-account-extension"></a>Předpoklady pro rozšíření účtu Azure

1. Azure Stack prostředí 1904 Build nebo novější
2. [Visual Studio Code](https://code.visualstudio.com/)
3. [Rozšíření účtu Azure](https://github.com/Microsoft/vscode-azure-account)
4. [Předplatné Azure Stack](https://azure.microsoft.com/overview/azure-stack/)

## <a name="steps-to-connect-to-azure-stack"></a>Postup připojení k Azure Stack

1. Spusťte skript **identity** z Azure Stack nástrojů na GitHubu.

    - Před spuštěním skriptu budete muset mít nainstalovaný a nakonfigurovaný PowerShell pro vaše prostředí. Pokyny najdete v tématu [instalace PowerShellu pro Azure Stack](../operator/azure-stack-powershell-install.md).

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

      ![Azure Stack Visual Studio Code](media/azure-stack-dev-start-vscode-azure/image1.png)

6. Pro načtení rozšíření restartujte VS Code.

7. Načtěte metadata pro připojení k Azure Resource Manager v Azure Stack. 
    
    Microsoft Azure Správce prostředků je rozhraní pro správu, které umožňuje nasazovat, spravovat a monitorovat prostředky Azure.
    - Správce prostředků adresa URL pro Azure Stack Development Kit (ASDK) je:`https://management.local.azurestack.external/` 
    - Adresa URL Správce prostředků pro integrovaný systém je:`https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`
    - Přidejte do adresy URL následující text pro přístup k metadatům:`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

    Například adresa URL pro načtení metadat pro váš koncový bod Azure Resource Manager může vypadat přibližně takto:`https://management.local.azurestack.external/metadata/endpoints?api-version=1.0`

    Poznamenejte si návratový kód JSON. Budete potřebovat hodnoty pro `loginEndpoint` vlastnost a. `audiences`

8. Stiskněte **kombinaci kláves CTRL + SHIFT + P** a **vyberte předvolby: Otevřete nastavení uživatele (JSON)** .

9. V editoru kódu aktualizujte následující fragment kódu JSON hodnotami pro vaše prostředí a vložte fragment kódu do bloku nastavení.

    - Hodnota

        | Parametr | Popis |
        | --- | --- |
        | `tenant-ID` | Hodnota vašeho [ID tenanta](../operator/azure-stack-identity-overview.md)Azure Stack. |
        | `activeDirectoryEndpointUrl` | Toto je adresa URL z vlastnosti loginEndpoint. |
        | `activeDirectoryResourceId` | Toto je adresa URL z vlastnosti cílové skupiny.
        | `resourceManagerEndpointUrl` | Toto je kořenová adresa URL pro Azure Resource Manager Azure Stack. | 

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

10. Uložte nastavení uživatele a znovu použijte **kombinaci kláves CTRL + SHIFT + P** . Vyberte **Azure: Přihlaste se ke**cloudu Azure. V seznamu cílů se zobrazí nová možnost **AzurePPE**.

11. Vyberte **AzurePPE**. Ověřovací stránka se načte v prohlížeči. Přihlaste se ke svému koncovému bodu.

12. K otestování úspěšného přihlášení k předplatnému Azure Stack použijte **kombinaci kláves CTRL + SHIFT + P** a **vyberte Azure: Vyberte předplatné** a zkontrolujte, jestli je předplatné dostupné.

## <a name="commands"></a>Příkazy

| Azure: Přihlásit se | Přihlásit k předplatnému Azure |
| --- | --- |
| Azure: Přihlášení pomocí kódu zařízení | Přihlaste se ke svému předplatnému Azure pomocí kódu zařízení. V instalačních sestavách použijte kód zařízení, kde příkaz Sign in nefunguje. |
| Azure: Přihlaste se ke cloudu Azure | Přihlaste se k předplatnému Azure v jednom z cloudů z svrchovaného prostředí. |
| Azure: Odhlásit | Odhlaste se od předplatného Azure. |
| Azure: Vybrat odběry | Vyberte sadu odběrů, se kterými chcete pracovat. Rozšíření zobrazuje pouze prostředky v rámci filtrovaných odběrů. |
| Azure: Vytvořit účet | Pokud účet Azure nemáte, můžete si ho [zaregistrovat](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-azure-account&mktingSource=vscode-azure-account) ještě dnes a získat \$200 na bezplatné kredity. |
| Azure: Otevřete bash v Cloud Shell | V Cloud Shell otevřete nový terminál s operačním systémem bash. |
| Azure: Otevřít PowerShell v Cloud Shell | Otevřete v Cloud Shell nový terminál, na kterém běží PowerShell. |
| Azure: Odeslat do Cloud Shell | Nahrajte soubor do svého účtu úložiště Cloud Shell. |

## <a name="next-steps"></a>Další postup

[Nastavení vývojového prostředí v Azure Stack](azure-stack-dev-start.md)
