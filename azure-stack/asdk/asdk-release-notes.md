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
ms.date: 08/30/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 08/30/2019
ms.openlocfilehash: 98e43cfe0226e06ca936484a78da5a61915f5797
ms.sourcegitcommit: c46d913ebfa4cb6c775c5117ac5c9e87d032a271
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101034"
---
# <a name="asdk-release-notes"></a>Poznámky k verzi ASDK

Tento článek obsahuje informace o změnách, opravách a známých problémech v Azure Stack Development Kit (ASDK). Pokud si nejste jisti, jakou verzi používáte, [použijte portál ke kontrole](../operator/azure-stack-updates.md).

Přihlaste se k odběru [ ![](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) informačního [kanálu RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)RSS a sledujte, co je nového v ASDK.

::: moniker range="azs-1908"
## <a name="build-11908020"></a>1\.1908.0.20 sestavení

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v [této části](/azure-stack/operator/release-notes?view=azs-1908#whats-new-1908) poznámky k verzi Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

<!-- - For a list of Azure Stack issues fixed in this release, see [this section](/azure-stack/operator/release-notes?view=azs-1908#fixes-1908) of the Azure Stack release notes. -->
- Seznam známých problémů najdete v [tomto článku](/azure-stack/operator/known-issues?view=azs-1908).
- Všimněte si, že dostupné opravy hotfix Azure Stack neplatí pro ASDK.
::: moniker-end

::: moniker range="azs-1907"
## <a name="build-11907020"></a>1\.1907.0.20 sestavení

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v [této části](/azure-stack/operator/release-notes?view=azs-1907#whats-in-this-update-1907) poznámky k verzi Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

- Při vytváření prostředků virtuálních počítačů pomocí některých imagí Marketplace možná nebudete moci dokončit nasazení. Jako alternativní řešení můžete kliknout na odkaz **Stáhnout šablonu a parametry** na stránce **Souhrn** a kliknout na tlačítko **nasadit** v okně **Šablona** .
- Seznam problémů s Azure Stack opravených v této verzi najdete v [této části](/azure-stack/operator/release-notes?view=azs-1907#fixes-1907) poznámky k verzi Azure Stack.
- Seznam známých problémů najdete v [tomto článku](/azure-stack/operator/known-issues?view=azs-1907).
- Upozorňujeme, že [k dispozici Azure Stack opravy hotfix](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-1907) se nevztahují na Azure Stack ASDK.
::: moniker-end

::: moniker range="azs-1906"
## <a name="build-11906030"></a>1\.1906.0.30 sestavení

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v [této části](/azure-stack/operator/release-notes?view=azs-1906#whats-in-this-update-1906) poznámky k verzi Azure Stack.

### <a name="changes"></a>Změny

- Přidala se služba **AzS-SRNG01** Ring Host pro službu shromažďování protokolů pro Azure Stack. Další informace najdete v tématu [Role virtuálních počítačů](asdk-architecture.md).

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

- Při vytváření prostředků virtuálních počítačů pomocí některých imagí Marketplace možná nebudete moci dokončit nasazení. Jako alternativní řešení můžete kliknout na odkaz **Stáhnout šablonu a parametry** na stránce **Souhrn** a kliknout na tlačítko **nasadit** v okně **Šablona** .
- Seznam problémů s Azure Stack opravených v této verzi najdete v [této části](/azure-stack/operator/release-notes?view=azs-1906#fixes-1906) poznámky k verzi Azure Stack.
- Seznam známých problémů najdete v [tomto článku](/azure-stack/operator/known-issues?view=azs-1906).
- Upozorňujeme, že [k dispozici Azure Stack opravy hotfix](/azure-stack/operator/release-notes?view=azs-1906#hotfixes-1906) se nevztahují na Azure Stack ASDK.
::: moniker-end

::: moniker range="azs-1905"
## <a name="build-11905040"></a>1\.1905.0.40 sestavení

<!-- ### Changes -->

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v [této části](/azure-stack/operator/release-notes?view=azs-1905#whats-in-this-update-1905) poznámky k verzi Azure Stack.

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

- Opravili jsme problém, kdy jste museli upravit skript prostředí PowerShell **RegisterWithAzure. psm1** , aby bylo možné úspěšně [zaregistrovat ASDK](asdk-register.md) .
- Seznam dalších problémů Azure Stack opravených v této verzi najdete v [této části](/azure-stack/operator/release-notes?view=azs-1905#fixes-1905) poznámky k verzi Azure Stack.
- Seznam známých problémů najdete v [tomto článku](/azure-stack/operator/known-issues?view=azs-1905).
- Upozorňujeme, že [k dispozici Azure Stack opravy hotfix](/azure-stack/operator/release-notes?view=azs-1905#hotfixes-1905) se nevztahují na Azure Stack ASDK.
::: moniker-end
