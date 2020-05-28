---
title: Nasazení pomocí Visual Studio Code pro Azure Stack centra
description: Jako uživatel chci vytvořit šablonu Azure Resource Manager v Visual Studio Code a pomocí schématu nasazení připravit šablonu, která je kompatibilní s moji verzí centra Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 8f0205425b49ee9e828056e5613a8166d02d95d9
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84111627"
---
# <a name="deploy-with-visual-studio-code-to-azure-stack-hub"></a>Nasazení pomocí Visual Studio Code pro Azure Stack centra

Pomocí Visual Studio Code a rozšíření Azure Resource Manager nástrojů můžete vytvářet a upravovat šablony Azure Resource Manager, které budou fungovat s vaší verzí Azure Stack hub. Šablony Resource Manageru můžete v nástroji Visual Studio Code vytvářet i bez tohoto rozšíření, poskytuje však možnosti automatického dokončování, které vývoj šablon zjednodušují. Kromě toho můžete zadat schéma nasazení, které vám pomůže pochopit prostředky dostupné v centru Azure Stack.

V tomto článku nasadíte virtuální počítač se systémem Windows.

## <a name="concepts-for-azure-stack-hub-resource-manager"></a>Koncepty Správce prostředků centra Azure Stack

### <a name="azure-stack-hub-resource-manager"></a>Správce prostředků centra Azure Stack

Pokud chcete pochopit koncepty spojené s nasazením a správou řešení Azure v centru Azure Stack, přečtěte si téma [použití Azure Resource Manager šablon v centru Azure Stack](azure-stack-arm-templates.md).

### <a name="api-profiles"></a>Profily rozhraní API
Informace o konceptech přidružených k koordinaci poskytovatelů prostředků v centru Azure Stack najdete v tématu [Správa profilů verzí rozhraní API v centru Azure Stack](azure-stack-version-profiles.md).

### <a name="the-deployment-schema"></a>Schéma nasazení

Schéma nasazení centra Azure Stack podporuje hybridní profily prostřednictvím šablon Azure Resource Manager v Visual Studio Code. Můžete změnit jeden řádek v šabloně JSON, aby odkazoval na schéma, a potom můžete pomocí technologie IntelliSense zkontrolovat prostředek kompatibilní s Azure. Pomocí schématu zkontrolujte poskytovatele prostředků, typy a verze rozhraní API podporované ve vaší verzi centra Azure Stack. Schéma závisí na profilu rozhraní API k načtení konkrétních verzí koncových bodů rozhraní API v poskytovatelích prostředků podporovaných ve vaší verzi centra Azure Stack. Můžete použít dokončování slov pro typ a apiVersion a pak budete omezeni na apiVersion a typy prostředků, které jsou k dispozici pro profil rozhraní API.

## <a name="prerequisites"></a>Požadavky

- [Visual Studio Code](https://code.visualstudio.com/)
- Přístup k centru Azure Stack
- [Prostředí PowerShell centra Azure Stack nainstalované](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json) na počítači, který dosáhne koncových bodů správy

## <a name="install-resource-manager-tools-extension"></a>Rozšíření pro instalaci Správce prostředků nástrojů

Chcete-li nainstalovat rozšíření Správce prostředků Tools, použijte následující postup:

1. Otevřete Visual Studio Code.
2. Stisknutím CTRL+SHIFT+X otevřete podokno Rozšíření.
3. Vyhledejte `Azure Resource Manager Tools` a pak vyberte **nainstalovat**.
4. Instalaci rozšíření dokončíte výběrem **Znovu načíst**.

## <a name="get-a-template"></a>Získat šablonu

Místo vytvoření zcela nové šablony otevřete šablonu z AzureStack-Start-Templates ( https://github.com/Azure/AzureStack-QuickStart-Templates) . AzureStack-Start-Templates je úložiště pro Správce prostředků šablony, které nasazují prostředky do centra Azure Stack. 

Šablona v tomto článku se nazývá `101-vm-windows-create` . Šablona definuje základní nasazení virtuálního počítače s Windows pro Azure Stack hub.  Tato šablona také nasadí virtuální síť (s DNS), skupinu zabezpečení sítě a síťové rozhraní.

1. Otevřete Visual Studio Code a přejděte do pracovní složky na vašem počítači.
2. V Visual Studio Code otevřete terminál Git bash.
3. Spuštěním následujícího příkazu načtěte úložiště rychlého startu centra Azure Stack.
    ```bash  
    Git clone https://github.com/Azure/AzureStack-QuickStart-Templates.git
    ```
4. Otevřete adresář obsahující úložiště.
    ```bash  
    CD AzureStack-QuickStart-Templates
    ```
5. Výběrem **otevřít** otevřete soubor v `/101-vm-windows-create/azuredeploy.json` úložišti.
6. Uložte soubor do vlastního pracovního prostoru, nebo pokud jste vytvořili větev úložiště, můžete pracovat na místě.
7. Když je soubor stále otevřený, změňte `$Schema` pole na `https://schema.management.azure.com/schemas/2019-03-01-hybrid/deploymentTemplate.json#` .
8. Můžete ověřit, zda schéma nasazení funguje tak, že vymažete hodnotu pole apiProfile.
    ```JSON  
    "apiProfile": ""
    ```
9. Umístěte kurzor mezi prázdné uvozovky a stiskněte kombinaci kláves CTRL + MEZERNÍK. Můžete si vybrat z platných profilů rozhraní API ve schématu nasazení pro centrum Azure Stack. Tuto operaci můžete provést u každého poskytovatele prostředků v šabloně.

    ![Schéma nasazení Správce prostředků centra Azure Stack](./media/azure-stack-resource-manager-deploy-template-vscode/azure-stack-resource-manager-vscode-schema.png)

10. Až budete připraveni, můžete šablonu nasadit pomocí prostředí PowerShell. Postupujte podle pokynů v tématu [nasazení v prostředí PowerShell](azure-stack-deploy-template-powershell.md). Zadejte umístění šablony ve skriptu.
11. Po nasazení virtuálního počítače s Windows přejděte na portál centra Azure Stack a najděte skupinu prostředků. Chcete-li vymazat výsledek tohoto cvičení z centra Azure Stack, odstraňte skupinu prostředků.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [šablonách správce prostředků centra Azure Stack](azure-stack-arm-templates.md).  
- Přečtěte si další informace o [profilech rozhraní API v centru Azure Stack](azure-stack-version-profiles.md).