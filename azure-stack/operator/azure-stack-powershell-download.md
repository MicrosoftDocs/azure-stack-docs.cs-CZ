---
title: Stažení Azure Stack nástrojů z GitHubu | Microsoft Docs
description: Naučte se stahovat nástroje, které jsou potřeba pro práci s Azure Stack.
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
ms.date: 09/18/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/18/2019
ms.openlocfilehash: c6939a28b150073d08a4f8c8dbc2d15dfd153957
ms.sourcegitcommit: c46d913ebfa4cb6c775c5117ac5c9e87d032a271
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71100928"
---
# <a name="download-azure-stack-tools-from-github"></a>Stažení Azure Stack nástrojů z GitHubu

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

**AzureStack-Tools** je [úložiště GitHub](https://github.com/Azure/AzureStack-Tools) , které hostuje moduly PowerShellu pro správu a nasazování prostředků do Azure Stack. Pokud plánujete navázat připojení k síti VPN, můžete tyto moduly PowerShell stáhnout do Azure Stack Development Kit nebo do externího klienta založeného na systému Windows. Pokud chcete tyto nástroje získat, naklonujte úložiště GitHubu nebo Stáhněte složku **AzureStack-Tools** spuštěním následujícího skriptu:

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

Úložiště **AzureStack-Tools** obsahuje moduly PowerShellu, které podporují následující funkce Azure Stack:  

| Funkce | Popis | Kdo může používat tento modul? |
| --- | --- | --- |
| [Cloudové možnosti](../user/azure-stack-validate-templates.md) | Tento modul slouží k získání cloudových možností cloudu. Například pomocí tohoto modulu můžete získat cloudové možnosti, jako je verze rozhraní API a prostředky Azure Resource Manager. Rozšíření virtuálních počítačů pro Azure Stack a cloudy Azure můžete také získat pomocí tohoto modulu. | Operátoři cloudu a uživatelé |
| [Zásady Správce prostředků pro Azure Stack](../user/azure-stack-policy-module.md) | Pomocí tohoto modulu můžete nakonfigurovat předplatné Azure nebo skupinu prostředků Azure se stejnou verzí a dostupností služeb jako Azure Stack. | Operátoři cloudu a uživatelé |
| [Registrace v Azure](azure-stack-registration.md ) | Tento modul použijte k registraci instance vývojářské sady v Azure. Po registraci můžete z Azure stáhnout položky z webu Marketplace a použít je v Azure Stack. | Operátory cloudu |
| [Nasazení Azure Stack](../asdk/asdk-install.md) | Tento modul slouží k přípravě Azure Stack hostitelského počítače k nasazení a opětovnému nasazení pomocí Azure Stack image virtuálního pevného disku (VHD). | Operátory cloudu|
| [Připojování k Azure Stack](azure-stack-powershell-install.md) | Pomocí tohoto modulu můžete nakonfigurovat připojení VPN k Azure Stack. | Operátoři cloudu a uživatelé |
| [Validátor šablon](../user/azure-stack-validate-templates.md) | Tento modul použijte k ověření, zda lze nasadit existující šablonu nebo novou šablonu do Azure Stack. | Operátoři cloudu a uživatelé|


## <a name="next-steps"></a>Další postup

- [Začínáme s PowerShellem v Azure Stack](../user/azure-stack-powershell-overview.md)
- [Konfigurace prostředí PowerShellu Azure Stackho uživatele](../user/azure-stack-powershell-configure-user.md)   
- [Připojení k Azure Stack Development Kit přes síť VPN](../asdk/asdk-connect.md)  
