---
title: Poznámky k verzi ASDK | Microsoft Docs
description: Vylepšení, opravy a známé problémy pro Azure Stack Development Kit (ASDK).
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
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 2323f13029d4d356ce54b825bab69da5ca21d6ab
ms.sourcegitcommit: d619612f54eeba3231ed73ed149ff894f9bf838a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74993583"
---
# <a name="asdk-release-notes"></a>Poznámky k verzi ASDK

Tento článek obsahuje informace o změnách, opravách a známých problémech v Azure Stack Development Kit (ASDK). Pokud si nejste jisti, jakou verzi používáte, [použijte portál ke kontrole](../operator/azure-stack-updates.md).

Seznamte se s novinkami v ASDK tak, že se přihlásíte k odběru informačního [kanálu rss](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) .

::: moniker range="azs-1910"
## <a name="build-11910058"></a>1\.1910.0.58 sestavení

### <a name="new-features"></a>Nové funkce

- Seznam opravených problémů, změn a nových funkcí v této verzi najdete v příslušných oddílech v [poznámkách k verzi Azure Stack](../operator/release-notes.md).

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

- Opravili jsme problém se shromažďováním protokolů a jejich uložením do Azure Storage kontejneru objektů BLOB. Syntaxe této operace je následující:

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ``` 

- Opravili jsme problém s nasazením, kdy služba zařazování po pomalém načítání brání odebrání některých funkcí Windows a vyžaduje restart.
- Seznam Azure Stack známých problémů v této verzi najdete v článku o [známých problémech](../operator/known-issues.md) .
- Všimněte si, že dostupné opravy hotfix Azure Stack neplatí pro ASDK.
::: moniker-end

::: moniker range="azs-1908"
  
## <a name="build-11908020"></a>1\.1908.0.20 sestavení

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v [této části](/azure-stack/operator/release-notes?view=azs-1908#whats-new-1) poznámky k verzi Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

<!-- - For a list of Azure Stack issues fixed in this release, see [this section](/azure-stack/operator/release-notes?view=azs-1908#fixes-1) of the Azure Stack release notes. -->
- Seznam známých problémů najdete v [tomto článku](/azure-stack/operator/known-issues?view=azs-1908).
- Všimněte si, že dostupné opravy hotfix Azure Stack neplatí pro ASDK.
::: moniker-end

::: moniker range="azs-1907"
## <a name="build-11907020"></a>1\.1907.0.20 sestavení

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v [této části](/azure-stack/operator/release-notes?view=azs-1907#whats-in-this-update) poznámky k verzi Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

- Při vytváření prostředků virtuálních počítačů pomocí některých imagí Marketplace možná nebudete moci dokončit nasazení. Jako alternativní řešení můžete kliknout na odkaz **Stáhnout šablonu a parametry** na stránce **Souhrn** a kliknout na tlačítko **nasadit** v okně **Šablona** .
- Seznam problémů s Azure Stack opravených v této verzi najdete v [této části](/azure-stack/operator/release-notes?view=azs-1907#fixes-2) poznámky k verzi Azure Stack.
- Seznam známých problémů najdete v [tomto článku](/azure-stack/operator/known-issues?view=azs-1907).
- Upozorňujeme, že [k dispozici Azure Stack opravy hotfix](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-2) se nevztahují na Azure Stack ASDK.
::: moniker-end

::: moniker range="azs-1906"
## <a name="build-11906030"></a>1\.1906.0.30 sestavení

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v [této části](/azure-stack/operator/release-notes?view=azs-1906#whats-in-this-update-1) poznámky k verzi Azure Stack.

### <a name="changes"></a>Změny

- Přidala se služba **AzS-SRNG01** Ring Host pro službu shromažďování protokolů pro Azure Stack. Další informace najdete v tématu [Role virtuálních počítačů](asdk-architecture.md).

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

- Při vytváření prostředků virtuálních počítačů pomocí některých imagí Marketplace možná nebudete moci dokončit nasazení. Jako alternativní řešení můžete kliknout na odkaz **Stáhnout šablonu a parametry** na stránce **Souhrn** a kliknout na tlačítko **nasadit** v okně **Šablona** .
- Seznam problémů s Azure Stack opravených v této verzi najdete v [této části](/azure-stack/operator/release-notes?view=azs-1906#fixes-3) poznámky k verzi Azure Stack.
- Seznam známých problémů najdete v [tomto článku](/azure-stack/operator/known-issues?view=azs-1906).
- Upozorňujeme, že [k dispozici Azure Stack opravy hotfix](/azure-stack/operator/release-notes?view=azs-1906#hotfixes-3) se nevztahují na Azure Stack ASDK.
::: moniker-end
