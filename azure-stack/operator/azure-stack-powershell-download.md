---
title: Stáhněte si nástroje pro Azure Stack z Githubu | Dokumentace Microsoftu
description: Zjistěte, jak chcete stáhnout nástroje, které jsou požadovány pro práci s Azure Stack.
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
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 722fea2777071fa78c89372a21fe52022a56f264
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782255"
---
# <a name="download-azure-stack-tools-from-github"></a>Stáhněte si nástroje pro Azure Stack z Githubu

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

**Nástroje AzureStack** je [úložiště GitHub](https://github.com/Azure/AzureStack-Tools) , který je hostitelem moduly Powershellu pro správu a nasazování prostředků do služby Azure Stack. Pokud máte v úmyslu vytvořit připojení k síti VPN, můžete stáhnout tyto moduly Powershellu pro Azure Stack Development Kit, nebo externí klienta se systémem Windows. Pokud chcete získat tyto nástroje, naklonujte úložiště GitHub nebo stáhnout **AzureStack nástroje** složku spuštěním následujícího skriptu:

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

## <a name="functionality-provided-by-the-modules"></a>Funkce poskytované službou moduly

**AzureStack nástroje** úložiště obsahuje moduly Powershellu, které podporují následující funkce pro Azure Stack:  

| Funkce | Popis | Tento modul, který můžete použít? |
| --- | --- | --- |
| [Cloudové funkce](../user/azure-stack-validate-templates.md) | Tento modul slouží k získání cloudové možnosti cloudu. Pomocí tohoto modulu, například můžete získat možnosti cloudu, jako je verze rozhraní API a prostředky Azure Resource Manageru. Rozšíření virtuálních počítačů pro Azure Stack a cloudy Azure můžete také získat pomocí tohoto modulu. | Operátoři cloudů a uživatelé |
| [Zásady Resource Manageru pro službu Azure Stack](../user/azure-stack-policy-module.md) | Tento modul slouží ke konfiguraci předplatné Azure nebo skupinu prostředků Azure s dostupností stejné správy verzí a služby jako Azure Stack. | Operátoři cloudů a uživatelé |
| [Zaregistrujte v Azure](azure-stack-registration.md ) | Tento modul slouží k registraci development kit instance v Azure. Po registraci můžete stažení položek z marketplace z Azure a jejich použití ve službě Azure Stack. | Operátoři cloudu |
| [Nasazení Azure Stack](../asdk/asdk-install.md) | Tento modul slouží k Příprava hostitelském počítači Azure Stack pro nasazení a znovu nasadit s použitím image virtuálního pevného disku (VHD) Azure Stack. | Operátoři cloudu|
| [Připojení k Azure Stack](azure-stack-powershell-install.md) | Tento modul slouží ke konfiguraci připojení VPN ke službě Azure Stack. | Operátoři cloudů a uživatelé |
| [Program pro ověření šablony](../user/azure-stack-validate-templates.md) | Tento modul slouží k ověření, pokud stávající nebo novou šablonu je možné nasadit do služby Azure Stack. | Operátoři cloudů a uživatelé|


## <a name="next-steps"></a>Další postup

- [Začínáme s prostředím PowerShell ve službě Azure Stack](../user/azure-stack-powershell-overview.md)
- [Konfigurace prostředí PowerShell uživatele Azure stacku](../user/azure-stack-powershell-configure-user.md)   
- [Připojení k Azure Stack Development Kit přes síť VPN](../asdk/asdk-connect.md)  
