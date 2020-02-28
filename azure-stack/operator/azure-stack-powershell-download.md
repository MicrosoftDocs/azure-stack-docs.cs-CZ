---
title: Stažení nástrojů centra Azure Stack z GitHubu
description: Naučte se stahovat nástroje potřebné pro práci s Azure Stack hub.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: 8d2f79d85055ef15f7dc0af3e1b36434f9c63d79
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698279"
---
# <a name="download-azure-stack-hub-tools-from-github"></a>Stažení nástrojů centra Azure Stack z GitHubu

**AzureStack-Tools** je [úložiště GitHub](https://github.com/Azure/AzureStack-Tools) , které hostuje moduly PowerShellu pro správu a nasazování prostředků do centra Azure Stack. Pokud plánujete navázat připojení k síti VPN, můžete tyto moduly PowerShell stáhnout do Azure Stack Development Kit (ASDK) nebo do externího klienta založeného na systému Windows. Pokud chcete tyto nástroje získat, naklonujte úložiště GitHubu nebo Stáhněte složku **AzureStack-Tools** spuštěním následujícího skriptu:

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

Úložiště **AzureStack-Tools** obsahuje moduly prostředí PowerShell, které podporují následující funkce centra Azure Stack:  

| Funkce | Popis | Kdo může používat tento modul? |
| --- | --- | --- |
| [Cloudové možnosti](../user/azure-stack-validate-templates.md) | Tento modul slouží k získání cloudových možností cloudu. Můžete například získat cloudové možnosti, jako je verze rozhraní API a prostředky Azure Resource Manager. Můžete taky získat rozšíření virtuálních počítačů pro centra Azure Stack a cloudy Azure. | Operátoři cloudu a uživatelé |
| [Zásady Správce prostředků pro centrum Azure Stack](../user/azure-stack-policy-module.md) | Pomocí tohoto modulu můžete nakonfigurovat předplatné Azure nebo skupinu prostředků Azure se stejnou dostupností verzí a služeb jako centrum Azure Stack. | Operátoři cloudu a uživatelé |
| [Registrace v Azure](azure-stack-registration.md ) | Tento modul použijte k registraci instance ASDK v Azure. Po registraci můžete stáhnout Azure Marketplace položky, které se budou používat v centru Azure Stack. | Operátory cloudu |
| [Nasazení centra Azure Stack](../asdk/asdk-install.md) | Tento modul slouží k přípravě hostitelského počítače centra Azure Stack k nasazení a opětovnému nasazení pomocí image virtuálního pevného disku (VHD) Azure Stack hub. | Operátory cloudu|
| [Připojování k centru Azure Stack](azure-stack-powershell-install.md) | Pomocí tohoto modulu můžete nakonfigurovat připojení VPN k rozbočovači Azure Stack. | Operátoři cloudu a uživatelé |
| [Validátor šablon](../user/azure-stack-validate-templates.md) | Pomocí tohoto modulu můžete ověřit, jestli se dá nasadit existující nebo novou šablonu do centra Azure Stack. | Operátoři cloudu a uživatelé|

## <a name="next-steps"></a>Další kroky

- [Začínáme s PowerShellem v centru Azure Stack](../user/azure-stack-powershell-overview.md).
- [Nakonfigurujte prostředí PowerShell uživatele centra Azure Stack](../user/azure-stack-powershell-configure-user.md).
- [Připojení k Azure Stack Development Kit přes síť VPN](../asdk/asdk-connect.md).
