---
title: Nasazení pomocí Visual Studio Code pro Azure Stack | Microsoft Docs
description: Jako uživatel chci vytvořit šablonu Azure Resource Manager v Visual Studio Code a pomocí schématu nasazení připravit šablonu, která je kompatibilní s mojí verzí Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 914e3e8db57009d58a14aa87d24ff86a8291e52b
ms.sourcegitcommit: e8aa26b078a9bab09c8fafd888a96785cc7abb4d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71711081"
---
# <a name="deploy-with-visual-studio-code-to-azure-stack"></a>Nasazení pomocí Visual Studio Code pro Azure Stack

Pomocí Visual Studio Code a rozšíření Azure Resource Manager nástrojů můžete vytvářet a upravovat šablony Azure Resource Manager, které budou fungovat s vaší verzí Azure Stack. Šablony Resource Manageru můžete v nástroji Visual Studio Code vytvářet i bez tohoto rozšíření, poskytuje však možnosti automatického dokončování, které vývoj šablon zjednodušují. Kromě toho můžete zadat schéma nasazení, které vám pomůže pochopit prostředky dostupné v Azure Stack.

V tomto článku nasadíte virtuální počítač se systémem Windows.

## <a name="concepts-for-azure-stack-resource-manager"></a>Koncepty Azure Stack Správce prostředků

### <a name="azure-stack-resource-manager"></a>Azure Stack Správce prostředků

Informace o konceptech spojených s nasazením a správou řešení Azure v Azure Stack najdete v tématu [použití Azure Resource Manager šablon v Azure Stack](azure-stack-arm-templates.md).

### <a name="api-profiles"></a>Profily rozhraní API
Informace o konceptech přidružených k koordinaci poskytovatelů prostředků v Azure Stack najdete v tématu [Správa profilů verzí rozhraní API v Azure Stack](azure-stack-version-profiles.md).

### <a name="the-deployment-schema"></a>Schéma nasazení

Schéma nasazení Azure Stack podporuje hybridní profily prostřednictvím šablon Azure Resource Manager v Visual Studio Code. Můžete změnit jeden řádek v šabloně JSON, aby odkazoval na schéma, a potom můžete pomocí technologie IntelliSense zkontrolovat prostředek kompatibilní s Azure. Pomocí schématu zkontrolujte poskytovatele prostředků, typy a verze rozhraní API podporované v rámci vaší verze Azure Stack. Schéma závisí na profilu rozhraní API k načtení konkrétních verzí koncových bodů rozhraní API v poskytovatelích prostředků podporovaných ve vaší verzi Azure Stack. Můžete použít dokončování slov pro typ a apiVersion a pak budete omezeni na apiVersion a typy prostředků, které jsou k dispozici pro profil rozhraní API.

## <a name="prerequisites"></a>Požadavky

- [Visual Studio Code](https://code.visualstudio.com/)
- Přístup k Azure Stack
- [Azure Stack PowerShell nainstalovaný](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json) na počítači, který dosáhne koncových bodů správy.

## <a name="install-resource-manager-tools-extension"></a>Rozšíření pro instalaci Správce prostředků nástrojů

Chcete-li nainstalovat rozšíření Správce prostředků Tools, použijte následující postup:

1. Otevřete Visual Studio Code.
2. Stisknutím kombinace kláves CTRL + SHIFT + X otevřete podokno rozšíření.
3. Vyhledejte a pak vyberte nainstalovat. `Azure Resource Manager Tools`
4. Instalaci rozšíření dokončíte výběrem **Znovu načíst**.

## <a name="get-a-template"></a>Získat šablonu

Místo vytvoření zcela nové šablony otevřete šablonu z AzureStack-Start-Templates (https://github.com/Azure/AzureStack-QuickStart-Templates). AzureStack-Start-Templates je úložiště pro Správce prostředků šablony, které nasazují prostředky do Azure Stack. 

Šablona v tomto článku se nazývá `101-vm-windows-create`. Šablona definuje základní nasazení virtuálního počítače s Windows, které se má Azure Stack.  Tato šablona také nasadí virtuální síť (s DNS), skupinu zabezpečení sítě a síťové rozhraní.

1. Otevřete Visual Studio Code a přejděte do pracovní složky na vašem počítači.
2. V Visual Studio Code otevřete terminál Git bash.
3. Spuštěním následujícího příkazu načtěte Azure Stack úložiště pro rychlé zprovoznění.
    ```bash  
    Git clone https://github.com/Azure/AzureStack-QuickStart-Templates.git
    ```
4. Otevřete adresář obsahující úložiště.
    ```bash  
    CD AzureStack-QuickStart-Templates
    ```
5. Výběrem **otevřít** otevřete soubor v úložišti `/101-vm-windows-create/azuredeploy.json`.
6. Uložte soubor do vlastního pracovního prostoru, nebo pokud jste vytvořili větev úložiště, můžete pracovat na místě.
7. Když je soubor stále otevřený, změňte pole `$Schema` na `https://schema.management.azure.com/schemas/2019-03-01-hybrid/deploymentTemplate.json#`.
8. Můžete ověřit, zda schéma nasazení funguje tak, že vymažete hodnotu pole apiProfile.
    ```JSON  
    "apiProfile": ""
    ```
9. Umístěte kurzor mezi prázdné uvozovky a stiskněte kombinaci kláves CTRL + MEZERNÍK. Můžete si vybrat z platných profilů rozhraní API ve schématu nasazení pro Azure Stack. Tuto operaci můžete provést u každého poskytovatele prostředků v šabloně.

    ![Schéma nasazení Správce prostředků Azure Stack](./media/azure-stack-resource-manager-deploy-template-vscode/azure-stack-resource-manager-vscode-schema.png)

10. Až budete připraveni, můžete šablonu nasadit pomocí prostředí PowerShell. Postupujte podle pokynů v tématu [nasazení v prostředí PowerShell](azure-stack-deploy-template-powershell.md). Zadejte umístění šablony ve skriptu.
11. Po nasazení virtuálního počítače s Windows přejděte na portál Azure Stack a najděte skupinu prostředků. Chcete-li vymazat výsledek tohoto cvičení z Azure Stack, odstraňte skupinu prostředků.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure Stack správce prostředků šablonách](azure-stack-arm-templates.md).  
- Přečtěte si další informace o [profilech rozhraní API v Azure Stack](azure-stack-version-profiles.md).