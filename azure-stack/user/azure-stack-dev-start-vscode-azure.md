---
title: Připojení ke službě Azure Stack pomocí rozšíření Azure Account ve Visual Studio Code | Dokumentace Microsoftu
description: Jako vývojář připojte se ke službě Azure Stack pomocí rozšíření Azure Account ve Visual Studio Code
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: Howto
ms.date: 05/31/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 8e64a570ab45e57e3cf58639bc2ec23d9b9bd81b
ms.sourcegitcommit: 07cc716d97bf484c7260eb165ae205ae25e09589
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2019
ms.locfileid: "66453491"
---
# <a name="connect-to-azure-stack-using-azure-account-extension-in-visual-studio-code"></a>Připojení ke službě Azure Stack pomocí rozšíření Azure Account ve Visual Studio Code

V tomto článku jsme vás provede postup připojení ke službě Azure Stack pomocí rozšíření Azure Account. Je potřeba aktualizovat nastavení aplikace Visual Studio Code (VS Code).

VS Code je editor nižšími nároky pro sestavování a ladění webových a cloudových aplikací. Používá se v ASP.NET Core, Python, Node.js, Go a jinými vývojáři. V rozšíření Azure Account, můžete použít jeden Azure Přihlaste se pomocí předplatného filtrování pro další rozšíření Azure. Rozšíření díky službě Azure Cloud Shell k dispozici, v terminálu VS Code integrované. Pomocí rozšíření můžete připojit k předplatnému Azure Stack pomocí Azure AD (Azure AD) a Active Directory Federated Services (AD FS) pro identity Manageru. To umožňuje přihlášení ke službě Azure Stack, vyberte své předplatné a otevřete nový příkazový řádek ve službě cloud shell. 

> [!Note]  
> Kroky v tomto článku slouží pro prostředí Active Directory Federated Services (AD FS). Použijte své přihlašovací údaje služby AD FS a koncových bodů.

## <a name="pre-requisites-for-the-azure-account-extension"></a>Požadavky na rozšíření Azure Account

1. Azure Stack prostředí 1904 sestavení nebo novější
2. [Visual Studio Code](https://code.visualstudio.com/)
3. [Azure Account Extension](https://github.com/Microsoft/vscode-azure-account)
4. [Předplatné služby Azure Stack](https://azure.microsoft.com/overview/azure-stack/)

## <a name="steps-to-connect-to-azure-stack"></a>Kroky pro připojení ke službě Azure Stack

1. Otevřít VS Code.

2. Vyberte **rozšíření** na levé straně rohu.

3. Do vyhledávacího pole zadejte `Azure Account`.

4. Vyberte **účet Azure** a vyberte **nainstalovat**.

      ![Azure Stack Visual Studio Code](media/azure-stack-dev-start-vscode-azure/image1.png)

5. Znovu spusťte VS Code pro načtení rozšíření.

6. Načtěte metadata pro připojení k Azure Resource Manageru v Azure stacku. 
    
    Microsoft Azure Resource Manageru je systém správy, který vám umožňuje nasadit, spravovat a monitorovat prostředky Azure.
    - Adresa URL Resource Manageru pro Azure Stack Development Kit (ASDK) je: `https://management.local.azurestack.external/` 
    - Adresa URL prostředku správce pro integrovaný systém je: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`
    - Přidejte následující text k adrese URL přístup k metadatům: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

    Adresa URL pro načtení metadat pro váš koncový bod Azure Resource Manageru může například vypadat přibližně jako: `https://management.local.azurestack.external/metadata/endpoints?api-version=1.0`

    Poznamenejte si vrácený kód JSON. Budete potřebovat hodnoty `loginEndpoint` a `loginEndgraphEndpointpoint` vlastnost.

7. Stisknutím klávesy **Ctrl + Shift + P** a vyberte **předvolby: Otevřete nastavení uživatele (JSON)** .

8. V editoru kódu následující fragment kódu JSON aktualizujte hodnoty pro vaše prostředí a vložte fragment kódu do bloku nastavení.

    - Hodnoty:

        | Parametr | Popis |
        | --- | --- |
        | `tenant-ID` | Výhody služby Azure Stack [ID tenanta](../operator/azure-stack-identity-overview.md). |
        | `activeDirectoryEndpointUrl` | Toto je adresa URL z loginEndpoint vlastnosti. |
        | `activeDirectoryResourceId` | Toto je adresa URL z loginEndgraphEndpointpoint vlastnosti.
        | `resourceManagerEndpointUrl` | Toto je kořenová adresa URL pro Azure Resource Manageru pro službu Azure Stack. | 

    - Fragment kódu JSON:

      ```JSON  
      "azure.tenant": "tenant-ID",
      "azure.ppe": {
          "activeDirectoryEndpointUrl": "Login endpoint",
          "activeDirectoryResourceId": "graph audience",
          "resourceManagerEndpointUrl": "Management Endpoint",
      },
      "azure.cloud": "AzurePPE"
      ```

8. Ukládat nastavení uživatele a použití **Ctrl + Shift + P** ještě jednou. Vyberte **Azure: Přihlaste se k Azure Cloud**. Nová možnost **AzurePPE**, se zobrazí v seznamu cílů.

9. Vyberte **AzurePPE**. Ověřovací stránka načte v prohlížeči. Přihlaste se do vašeho koncového bodu.

11. Chcete-li otestovat, že jste úspěšně přihlášení ke svému předplatnému Azure Stack, použijte **Ctrl + Shift + P** a vyberte **Azure: Vyberte předplatné** a zjistit, jestli máte předplatné k dispozici.

## <a name="commands"></a>Příkazy

| Azure: Přihlásit se | Přihlaste se ke svému předplatnému Azure. |
| --- | --- |
| Azure: Přihlaste se pomocí kódu zařízení | Přihlaste se k předplatnému Azure pomocí kódu zařízení. Použijte v nastavení kde příkazu Sign In nefunguje. |
| Azure: Přihlaste se do cloudu Azure | Přihlaste se ke svému předplatnému Azure v jednom z suverénních cloudech. |
| Azure: adresa URL | Odhlaste se z vašeho předplatného Azure. |
| Azure: Vyberte předplatné. | Vyberte sadu předplatná, která chcete pracovat. Rozšíření se zobrazí pouze prostředky v rámci filtrované předplatných. |
| Azure: Vytvoření účtu služby | Pokud nemáte účet Azure, můžete si [zaregistrovat](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-azure-account&mktingSource=vscode-azure-account) pro dnešní jeden den a přijímat \$200 bezplatný kredit v hodnotě. |
| Azure: Otevřete prostředí Bash ve službě Cloud Shell | Otevřete terminál nové spuštění prostředí Bash ve službě Cloud Shell. |
| Azure: Otevřete PowerShell ve službě Cloud Shell | Otevřete terminál nové spouštění prostředí PowerShell ve službě Cloud Shell. |
| Azure: Nahrajte do Cloud Shellu | Nahrání souboru do účtu úložiště Cloud Shell. |

## <a name="next-steps"></a>Další postup

[Nastavení vývojového prostředí ve službě Azure Stack ](azure-stack-dev-start.md)