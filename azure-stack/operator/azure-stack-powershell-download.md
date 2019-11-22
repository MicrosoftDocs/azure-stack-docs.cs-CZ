---
title: Stažení Azure Stack nástrojů z GitHubu | Microsoft Docs
description: Naučte se stahovat nástroje potřebné pro práci s Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 09/19/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: 9ca2e25217886d6b8a55bc394f4ee05df1bea2d6
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74298776"
---
# <a name="download-azure-stack-tools-from-github"></a>Stažení Azure Stack nástrojů z GitHubu

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

**AzureStack-Tools** je [úložiště GitHub](https://github.com/Azure/AzureStack-Tools) , které hostuje moduly PowerShellu pro správu a nasazování prostředků do Azure Stack. Pokud plánujete navázat připojení k síti VPN, můžete tyto moduly PowerShell stáhnout do Azure Stack Development Kit (ASDK) nebo do externího klienta založeného na systému Windows. Pokud chcete tyto nástroje získat, naklonujte úložiště GitHubu nebo Stáhněte složku **AzureStack-Tools** spuštěním následujícího skriptu:

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```

## <a name="functionality-provided-by-the-modules"></a>Funkce poskytované moduly

Úložiště **AzureStack-Tools** obsahuje moduly prostředí PowerShell, které podporují následující funkce Azure Stack:  

| Funkce | Popis | Kdo může používat tento modul? |
| --- | --- | --- |
| [Cloudové možnosti](../user/azure-stack-validate-templates.md) | Tento modul slouží k získání cloudových možností cloudu. Můžete například získat cloudové možnosti, jako je verze rozhraní API a prostředky Azure Resource Manager. Můžete taky získat rozšíření virtuálních počítačů pro Azure Stack a cloudy Azure. | Operátoři cloudu a uživatelé |
| [Zásady Správce prostředků pro Azure Stack](../user/azure-stack-policy-module.md) | Pomocí tohoto modulu můžete nakonfigurovat předplatné Azure nebo skupinu prostředků Azure se stejnou verzí a dostupností služeb jako Azure Stack. | Operátoři cloudu a uživatelé |
| [Registrace v Azure](azure-stack-registration.md ) | Tento modul použijte k registraci instance ASDK v Azure. Po registraci můžete stáhnout Azure Marketplace položky při jejich použití v Azure Stack. | Operátory cloudu |
| [Nasazení Azure Stack](../asdk/asdk-install.md) | Tento modul slouží k přípravě Azure Stack hostitelského počítače k nasazení a opětovnému nasazení pomocí Azure Stack image virtuálního pevného disku (VHD). | Operátory cloudu|
| [Připojování k Azure Stack](azure-stack-powershell-install.md) | Pomocí tohoto modulu můžete nakonfigurovat připojení VPN k Azure Stack. | Operátoři cloudu a uživatelé |
| [Validátor šablon](../user/azure-stack-validate-templates.md) | Tento modul použijte k ověření, zda lze nasadit existující šablonu nebo novou šablonu do Azure Stack. | Operátoři cloudu a uživatelé|

## <a name="next-steps"></a>Další kroky

- [Začínáme s PowerShellem v Azure Stack](../user/azure-stack-powershell-overview.md).
- [Nakonfigurujte prostředí PowerShell Azure Stackového uživatele](../user/azure-stack-powershell-configure-user.md).
- [Připojení k Azure Stack Development Kit přes síť VPN](../asdk/asdk-connect.md).
