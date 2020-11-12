---
title: Poznámky k verzi ASDK
description: Vylepšení, opravy a známé problémy pro Azure Stack Development Kit (ASDK).
author: sethmanheim
ms.topic: article
ms.date: 11/11/2020
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 08/10/2020
ms.openlocfilehash: f4e97cfaba4e3cde832a318647b72d96cbca8965
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543404"
---
# <a name="asdk-release-notes"></a>Poznámky k verzi ASDK

Tento článek obsahuje informace o změnách, opravách a známých problémech v Azure Stack Development Kit (ASDK). Pokud si nejste jisti, jakou verzi používáte, [použijte portál ke kontrole](../operator/azure-stack-updates.md).

Přihlaste se k odběru informačního ![ ](./media/asdk-release-notes/feed-icon-14x14.png) [kanálu RSS](https://docs.microsoft.com/api/search/rss?search=ASDK+release+notes&locale=en-us#)RSS a sledujte, co je nového v ASDK.

::: moniker range="azs-2008"
## <a name="build-120081388"></a>1.2008.13.88 sestavení

### <a name="new-features"></a>Nové funkce

- Seznam opravených problémů, změn a nových funkcí v této verzi najdete v příslušných oddílech v [poznámkách k verzi Azure Stack](../operator/release-notes.md).

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

::: moniker-end

::: moniker range="azs-2005"
## <a name="build-12005040"></a>1.2005.0.40 sestavení

### <a name="new-features"></a>Nové funkce

- Seznam opravených problémů, změn a nových funkcí v této verzi najdete v příslušných oddílech v [poznámkách k verzi Azure Stack](../operator/release-notes.md).

### <a name="fixed-and-known-issues"></a>Opravené a známé problémy

- Heslo certifikátu dešifrování je nová možnost pro zadání hesla pro certifikát podepsaný svým držitelem (. pfx), který obsahuje privátní klíč potřebný k dešifrování zálohovaných dat. Toto heslo se vyžaduje jenom v případě, že je záloha zašifrovaná pomocí certifikátu.
- Opravili jsme problém, který způsobil selhání cloudového obnovení v případě, že se původní heslo k externímu certifikátu změnilo ve zdrojovém systému s více uzly. 
- Seznam Azure Stack známých problémů v této verzi najdete v článku o [známých problémech](../operator/known-issues.md) .
- Všimněte si, že dostupné opravy hotfix Azure Stack neplatí pro ASDK.

#### <a name="initial-configuration-fails-in-asdk"></a>Počáteční konfigurace se v ASDK nezdařila.

- Při nasazování ASDK se může zobrazit stav chybové zprávy **' Deployment-Phase0-DeployBareMetal ' error '** a **stav ' Deployment-InitialSteps ' je ' error** '.

- Jako alternativní řešení:

1. Otevřete soubor v C:\CloudDeployment\Roles\PhysicalMachines\Tests\BareMetal.Tests.ps1 v libovolném editoru s čítačem řádku, jako je například prostředí PowerShell ISE.

2. Nahraďte řádek 822:

   ```powershell

   PartNumber = if($_.PartNumber) {$_.PartNumber.Trim()} else {""};

   ```  
::: moniker-end

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
