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
ms.openlocfilehash: 9d0820634a469f775c1e3b6637c604ae98681be2
ms.sourcegitcommit: 7d7a4c8c46613b6104caf23763bfd2275f6a826b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70188189"
---
# <a name="asdk-release-notes"></a>Poznámky k verzi ASDK

Tento článek obsahuje informace o změnách, opravách a známých problémech v Azure Stack Development Kit (ASDK). Pokud si nejste jisti, jakou verzi používáte, [použijte portál ke kontrole](../operator/azure-stack-updates.md).

Přihlaste se k odběru [ ![](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) informačního [kanálu RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)RSS a sledujte, co je nového v ASDK.

::: moniker range="azs-1908"
## <a name="build-11908020"></a>1\.1908.0.20 sestavení

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v [této části](../operator/azure-stack-release-notes-1908.md#whats-new) poznámky k verzi Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

<!-- - For a list of Azure Stack issues fixed in this release, see [this section](../operator/azure-stack-release-notes-1908.md#fixes) of the Azure Stack release notes. -->
- Seznam známých problémů najdete v [tomto článku](../operator/azure-stack-release-notes-known-issues-1908.md).
- Všimněte si, že dostupné opravy hotfix Azure Stack neplatí pro ASDK.
::: moniker-end

::: moniker range="azs-1907"
## <a name="build-11907020"></a>1\.1907.0.20 sestavení

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v [této části](../operator/azure-stack-release-notes-1907.md#whats-in-this-update) poznámky k verzi Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

- Při vytváření prostředků virtuálních počítačů pomocí některých imagí Marketplace možná nebudete moci dokončit nasazení. Jako alternativní řešení můžete kliknout na odkaz **Stáhnout šablonu a parametry** na stránce **Souhrn** a kliknout na tlačítko **nasadit** v okně **Šablona** .
- Seznam problémů s Azure Stack opravených v této verzi najdete v [této části](../operator/azure-stack-release-notes-1907.md#fixes) poznámky k verzi Azure Stack.
- Seznam známých problémů najdete v [tomto článku](../operator/azure-stack-release-notes-known-issues-1907.md).
- Upozorňujeme, že [k dispozici Azure Stack opravy hotfix](../operator/azure-stack-release-notes-1907.md#hotfixes) se nevztahují na Azure Stack ASDK.
::: moniker-end

::: moniker range="azs-1906"
## <a name="build-11906030"></a>1\.1906.0.30 sestavení

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v [této části](../operator/azure-stack-release-notes-1906.md#whats-in-this-update) poznámky k verzi Azure Stack.

### <a name="changes"></a>Změny

- Přidala se služba **AzS-SRNG01** Ring Host pro službu shromažďování protokolů pro Azure Stack. Další informace najdete v tématu [Role virtuálních počítačů](asdk-architecture.md).

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

- Při vytváření prostředků virtuálních počítačů pomocí některých imagí Marketplace možná nebudete moci dokončit nasazení. Jako alternativní řešení můžete kliknout na odkaz **Stáhnout šablonu a parametry** na stránce **Souhrn** a kliknout na tlačítko **nasadit** v okně **Šablona** .
- Seznam problémů s Azure Stack opravených v této verzi najdete v [této části](../operator/azure-stack-release-notes-1906.md#fixes) poznámky k verzi Azure Stack.
- Seznam známých problémů najdete v [tomto článku](../operator/azure-stack-release-notes-known-issues-1906.md).
- Upozorňujeme, že [k dispozici Azure Stack opravy hotfix](../operator/azure-stack-release-notes-1906.md#hotfixes) se nevztahují na Azure Stack ASDK.
::: moniker-end

::: moniker range="azs-1905"
## <a name="build-11905040"></a>1\.1905.0.40 sestavení

<!-- ### Changes -->

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v [této části](../operator/azure-stack-release-notes-1905.md#whats-in-this-update) poznámky k verzi Azure Stack.

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

- Opravili jsme problém, kdy jste museli upravit skript prostředí PowerShell **RegisterWithAzure. psm1** , aby bylo možné úspěšně [zaregistrovat ASDK](asdk-register.md) .
- Seznam dalších problémů Azure Stack opravených v této verzi najdete v [této části](../operator/azure-stack-release-notes-1905.md#fixes) poznámky k verzi Azure Stack.
- Seznam známých problémů najdete v [tomto článku](../operator/azure-stack-release-notes-known-issues-1905.md).
- Upozorňujeme, že [k dispozici Azure Stack opravy hotfix](../operator/azure-stack-release-notes-1905.md#hotfixes) se nevztahují na Azure Stack ASDK.
::: moniker-end
