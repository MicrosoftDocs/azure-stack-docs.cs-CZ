---
title: Poznámky k verzi Microsoft Azure Stack Development Kit | Microsoft Docs
description: Vylepšení, opravy a známé problémy pro Azure Stack Development Kit.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 058eff278dbfb7834e3b929967851f998b15f91b
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "70008246"
---
# <a name="asdk-release-notes"></a>Poznámky k verzi ASDK

Tento článek obsahuje informace o změnách, opravách a známých problémech v Azure Stack Development Kit (ASDK). Pokud si nejste jistí, kterou verzi používáte, můžete ji [zkontrolovat pomocí portálu](../operator/azure-stack-updates.md).

Přihlaste se k odběru [ ![](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) informačního [kanálu RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)RSS a sledujte, co je nového v ASDK.

## <a name="build-11907020"></a>1\.1907.0.20 sestavení

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v [této části](../operator/azure-stack-release-notes-1907.md#whats-in-this-update) poznámky k verzi Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

- Při vytváření prostředků virtuálního počítače pomocí některých imagí z Marketplace možná nebudete moci dokončit nasazení. Jako alternativní řešení můžete kliknout na odkaz **Stáhnout šablonu a parametry** na stránce **Souhrn** a kliknout na tlačítko **nasadit** v okně **Šablona** .
- Seznam problémů s Azure Stack opravených v této verzi najdete v [této části](../operator/azure-stack-release-notes-1907.md#fixes) poznámky k verzi Azure Stack.
- Seznam známých problémů najdete v [tomto článku](../operator/azure-stack-release-notes-known-issues-1907.md).
- Upozorňujeme, že [k dispozici Azure Stack opravy hotfix](../operator/azure-stack-release-notes-1907.md#hotfixes) se nevztahují na Azure Stack ASDK.

## <a name="build-11906030"></a>1\.1906.0.30 sestavení

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v [této části](../operator/azure-stack-release-notes-1906.md#whats-in-this-update) poznámky k verzi Azure Stack.

### <a name="changes"></a>Změny

- Přidala se služba **AzS-SRNG01** Ring Host pro službu shromažďování protokolů pro Azure Stack. Další informace najdete v tématu [Role virtuálních počítačů](asdk-architecture.md).

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

- Při vytváření prostředků virtuálního počítače pomocí některých imagí z Marketplace možná nebudete moci dokončit nasazení. Jako alternativní řešení můžete kliknout na odkaz **Stáhnout šablonu a parametry** na stránce **Souhrn** a kliknout na tlačítko **nasadit** v okně **Šablona** .
- Seznam problémů s Azure Stack opravených v této verzi najdete v [této části](../operator/azure-stack-release-notes-1906.md#fixes) poznámky k verzi Azure Stack.
- Seznam známých problémů najdete v [tomto článku](../operator/azure-stack-release-notes-known-issues-1906.md).
- Upozorňujeme, že [k dispozici Azure Stack opravy hotfix](../operator/azure-stack-release-notes-1906.md#hotfixes) se nevztahují na Azure Stack ASDK.

## <a name="build-11905040"></a>1\.1905.0.40 sestavení

<!-- ### Changes -->

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v [této části](../operator/azure-stack-release-notes-1905.md#whats-in-this-update) poznámky k verzi Azure Stack.

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

- Opravili jsme problém, kdy jste museli upravit skript prostředí PowerShell **RegisterWithAzure. psm1** , aby bylo možné úspěšně [zaregistrovat ASDK](asdk-register.md) .
- Seznam dalších problémů Azure Stack opravených v této verzi najdete v [této části](../operator/azure-stack-release-notes-1905.md#fixes) poznámky k verzi Azure Stack.
- Seznam známých problémů najdete v [tomto článku](../operator/azure-stack-release-notes-known-issues-1905.md).
- Upozorňujeme, že [k dispozici Azure Stack opravy hotfix](../operator/azure-stack-release-notes-1905.md#hotfixes) se nevztahují na Azure Stack ASDK.

## <a name="build-11904036"></a>1\.1904.0.36 sestavení

<!-- ### Changes -->

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v [této části](../operator/azure-stack-release-notes-1904.md#whats-in-this-update) poznámky k verzi Azure Stack.

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

- Vzhledem k tomu, že při spuštění registračního skriptu vypršel časový limit instančního objektu, musíte upravit skript prostředí PowerShell **RegisterWithAzure. psm1** , aby bylo možné úspěšně [zaregistrovat ASDK](asdk-register.md) . Udělejte toto:

  1. V hostitelském počítači ASDK otevřete soubor **C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1** v editoru se zvýšenými oprávněními.
  2. Na řádku 1249 přidejte `-TimeoutInSeconds 1800` parametr na konec. Tato hodnota je vyžadována z důvodu vypršení časového limitu objektu služby při spuštění registračního skriptu. Řádek 1249 by teď měl vypadat takto:

     ```powershell
      $servicePrincipal = Invoke-Command -Session $PSSession -ScriptBlock { New-AzureBridgeServicePrincipal -RefreshToken $using:RefreshToken -AzureEnvironment $using:AzureEnvironmentName -TenantId $using:TenantId -TimeoutInSeconds 1800 }
      ```

- Opravili jsme problém s připojením k síti VPN identifikovaný ve verzi 1902.

- Seznam dalších problémů Azure Stack opravených v této verzi najdete v [této části](../operator/azure-stack-release-notes-1904.md#fixes) poznámky k verzi Azure Stack.
- Seznam známých problémů najdete v [tomto článku](../operator/azure-stack-release-notes-known-issues-1904.md).
- Upozorňujeme, že [k dispozici Azure Stack opravy hotfix](../operator/azure-stack-release-notes-1904.md#hotfixes) se nevztahují na Azure Stack ASDK.

