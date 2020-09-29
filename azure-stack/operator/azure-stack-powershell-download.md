---
title: Stažení nástrojů centra Azure Stack z GitHubu
description: Naučte se stahovat nástroje potřebné pro práci s Azure Stack hub.
author: mattbriggs
ms.topic: article
ms.date: 8/28/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 8/28/2020
ms.openlocfilehash: 8aa2c3cad35af9d6c887217ea977a92eae7428cf
ms.sourcegitcommit: 7c01ab4b2e2250a7acd67d1c5ba27d15c1e8bce0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89448448"
---
# <a name="download-azure-stack-hub-tools-from-github"></a>Stažení nástrojů centra Azure Stack z GitHubu

**AzureStack-Tools** je [úložiště GitHub](https://github.com/Azure/AzureStack-Tools) , které hostuje moduly PowerShellu pro správu a nasazování prostředků do centra Azure Stack. Pokud plánujete navázat připojení k síti VPN, můžete tyto moduly PowerShell stáhnout do Azure Stack Development Kit (ASDK) nebo do externího klienta založeného na systému Windows. 

## <a name="get-tools-for-azure-stack-hub-azurerm-module"></a>Získat nástroje pro modul AzureRM centra pro Azure Stack

Pokud chcete tyto nástroje získat, naklonujte úložiště GitHub z `master` větve nebo Stáhněte složku **AzureStack-Tools** spuštěním následujícího skriptu na příkazovém řádku PowerShellu se zvýšenými oprávněními:

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
Další informace o použití modulu AzureRM pro centrum Azure Stack najdete v tématu [Instalace modulu PowerShell AzureRM pro centrum Azure Stack](azure-stack-powershell-install.md) .

## <a name="get-tools-for-azure-stack-hub-az-preview-module"></a>Získat nástroje pro centrum Azure Stack – modul AZ (Preview)

Pokud chcete tyto nástroje získat, naklonujte úložiště GitHub z `az` větve nebo Stáhněte složku **AzureStack-Tools** spuštěním následujícího skriptu:

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/az.zip `
  -OutFile az.zip

# Expand the downloaded files.
expand-archive az.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-az

```

Další informace o použití modulu AZ Module for Azure Stack hub najdete v tématu [install PowerShell AZ Preview Module for Azure Stack hub](powershell-install-az-module.md).

## <a name="functionality-provided-by-the-modules"></a>Funkce poskytované moduly

Úložiště **AzureStack-Tools** obsahuje moduly prostředí PowerShell, které podporují následující funkce centra Azure Stack:  

| Funkce | Popis | Kdo může používat tento modul? |
| --- | --- | --- |
| [Funkce cloudu](../user/azure-stack-validate-templates.md) | Tento modul slouží k získání cloudových možností cloudu. Můžete například získat cloudové možnosti, jako je verze rozhraní API a prostředky Azure Resource Manager. Můžete taky získat rozšíření virtuálních počítačů pro centra Azure Stack a cloudy Azure. | Operátoři cloudu a uživatelé |
| [Zásady Správce prostředků pro centrum Azure Stack](../user/azure-stack-policy-module.md) | Pomocí tohoto modulu můžete nakonfigurovat předplatné Azure nebo skupinu prostředků Azure se stejnou dostupností verzí a služeb jako centrum Azure Stack. | Operátoři cloudu a uživatelé |
| [Registrace v Azure](azure-stack-registration.md ) | Tento modul použijte k registraci instance ASDK v Azure. Po registraci můžete stáhnout Azure Marketplace položky, které se budou používat v centru Azure Stack. | Operátory cloudu |
| [Nasazení centra Azure Stack](../asdk/asdk-install.md) | Tento modul slouží k přípravě hostitelského počítače centra Azure Stack k nasazení a opětovnému nasazení pomocí image virtuálního pevného disku (VHD) Azure Stack hub. | Operátory cloudu|
| [Připojování k centru Azure Stack](azure-stack-powershell-install.md) | Pomocí tohoto modulu můžete nakonfigurovat připojení VPN k rozbočovači Azure Stack. | Operátoři cloudu a uživatelé |
| [Validátor šablon](../user/azure-stack-validate-templates.md) | Pomocí tohoto modulu můžete ověřit, jestli se dá nasadit existující nebo novou šablonu do centra Azure Stack. | Operátoři cloudu a uživatelé|

## <a name="next-steps"></a>Další kroky

- [Začínáme s PowerShellem v centru Azure Stack](../user/azure-stack-powershell-overview.md).
- [Nakonfigurujte prostředí PowerShell uživatele centra Azure Stack](../user/azure-stack-powershell-configure-user.md).
- [Připojení k Azure Stack Development Kit přes síť VPN](../asdk/asdk-connect.md).
