---
title: Poznámky k verzi ASDK
description: Vylepšení, opravy a známé problémy pro Azure Stack Development Kit (ASDK).
author: sethmanheim
ms.topic: article
ms.date: 04/06/2020
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: bbe37512d943a45b5981f4e862f55440ade3b08f
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "80806707"
---
# <a name="asdk-release-notes"></a>Poznámky k verzi ASDK

Tento článek obsahuje informace o změnách, opravách a známých problémech v Azure Stack Development Kit (ASDK). Pokud si nejste jisti, jakou verzi používáte, [použijte portál ke kontrole](../operator/azure-stack-updates.md).

Přihlaste se k odběru [ ![](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) informačního [kanálu RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)RSS a sledujte, co je nového v ASDK.

::: moniker range="azs-2002"
## <a name="build-12002035"></a>1.2002.0.35 sestavení

### <a name="new-features"></a>Nové funkce

- Seznam opravených problémů, změn a nových funkcí v této verzi najdete v příslušných oddílech v [poznámkách k verzi Azure Stack](../operator/release-notes.md).

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

- Heslo certifikátu dešifrování je nová možnost pro zadání hesla pro certifikát podepsaný svým držitelem (. pfx), který obsahuje privátní klíč potřebný k dešifrování zálohovaných dat. Toto heslo se vyžaduje jenom v případě, že je záloha zašifrovaná pomocí certifikátu.
- Seznam Azure Stack známých problémů v této verzi najdete v článku o [známých problémech](../operator/known-issues.md) .
- Všimněte si, že dostupné opravy hotfix Azure Stack neplatí pro ASDK.

#### <a name="sql-vm-provision-fails-in-asdk"></a>Zřizování virtuálního počítače SQL selhalo v ASDK

- Platí: Tento problém se týká ASDK 2002.
- Příčina: při vytváření nového virtuálního počítače SQL v ASDK 2002 se může zobrazit **přípona chybové zprávy s vydavatelem Microsoft. SqlServer. Management, typ SqlIaaSAgent a verze obslužné rutiny typu 2,0 se v úložišti rozšíření** nepovedlo najít. V Azure Stackovém centru neexistuje žádný **SqlIaaSAgent** 2,0.
::: moniker-end

::: moniker range="azs-1910"
## <a name="build-11910058"></a>1.1910.0.58 sestavení

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
  
## <a name="build-11908020"></a>1.1908.0.20 sestavení

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v [této části](/azure-stack/operator/release-notes?view=azs-1908#whats-new-2) poznámky k verzi Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

<!-- - For a list of Azure Stack issues fixed in this release, see [this section](/azure-stack/operator/release-notes?view=azs-1908#fixes-1) of the Azure Stack release notes. -->
- Seznam známých problémů najdete v [tomto článku](/azure-stack/operator/known-issues?view=azs-1908).
- Všimněte si, že dostupné opravy hotfix Azure Stack neplatí pro ASDK.
::: moniker-end

::: moniker range="azs-1907"
## <a name="build-11907020"></a>1.1907.0.20 sestavení

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v [této části](/azure-stack/operator/release-notes?view=azs-1907#whats-in-this-update) poznámky k verzi Azure Stack.

<!-- ### Changes -->

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

- Při vytváření prostředků virtuálních počítačů pomocí některých imagí Marketplace možná nebudete moci dokončit nasazení. Jako alternativní řešení můžete kliknout na odkaz **Stáhnout šablonu a parametry** na stránce **Souhrn** a kliknout na tlačítko **nasadit** v okně **Šablona** .
- Seznam problémů s Azure Stack opravených v této verzi najdete v [této části](/azure-stack/operator/release-notes?view=azs-1907#fixes-3) poznámky k verzi Azure Stack.
- Seznam známých problémů najdete v [tomto článku](/azure-stack/operator/known-issues?view=azs-1907).
- Upozorňujeme, že [k dispozici Azure Stack opravy hotfix](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-3) se nevztahují na Azure Stack ASDK.
::: moniker-end
