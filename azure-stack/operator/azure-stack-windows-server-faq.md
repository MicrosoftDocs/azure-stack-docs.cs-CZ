---
title: Nejčastější dotazy týkající se Azure Stack centra pro Windows Server | Microsoft Docs
description: Seznam nejčastějších dotazů k webu centra Azure Stack pro Windows Server
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/27/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 08/29/2019
ms.openlocfilehash: ae3a1d602347a4cb4dcb6199075cd1e3dce9cb29
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76023223"
---
# <a name="windows-server-in-azure-stack-hub-marketplace-faq"></a>Nejčastější dotazy k Windows serveru na webu centra Azure Stack

Tento článek obsahuje odpovědi na některé nejčastější dotazy týkající se imagí Windows serveru v [tržišti Azure Stack hub](azure-stack-marketplace.md).

## <a name="marketplace-items"></a>Položky Marketplace

### <a name="how-do-i-update-to-a-newer-windows-image"></a>Návody aktualizaci na novější bitovou kopii Windows?

Nejdřív Zjistěte, jestli některé šablony Azure Resource Manager odkazují na konkrétní verze. Pokud ano, aktualizujte tyto šablony nebo Udržujte starší verze imagí. Je nejvhodnější použít **verzi: nejnovější**.

Pokud navíc některé sady škálování virtuálních počítačů odkazují na konkrétní verzi, měli byste uvažovat o tom, jestli se budou škálovat později, a rozhodnout se, jestli chcete zachovat starší verze. Pokud se nepoužije žádná z těchto podmínek, před stažením novějších imagí na webu Marketplace odstraňte starší image. Pomocí správy Marketplace je odstraňte, pokud to znamená, jak byl původní stažený. Pak stáhněte novější verzi.

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack-hub"></a>Jaké jsou možnosti licencování pro image Windows serveru Marketplace v centru Azure Stack?

Microsoft nabízí dvě verze imagí Windows serveru prostřednictvím tržiště centra Azure Stack. V prostředí Azure Stack hub se dá použít jenom jedna verze této image.  

- **Platíte podle svých využití**: tyto image spouštějí celé ceny Windows měřičů.
   Kdo by měl používat: zákazníci smlouva Enterprise (EA), kteří používají *Model fakturace spotřeby*; CSP, kteří nechtějí používat licencování SPLA.
- **Přineste si vlastní licenci (BYOL)** : tyto image spouštějí základní měřiče.
   Kdo by měl používat: zákazníci se smlouvou EA s licencí k Windows serveru; CSP, kteří používají licencování SPLA.

Zvýhodněné hybridní využití Azure (AHUB) se v centru Azure Stack nepodporuje. Zákazníci, kteří mají licenci prostřednictvím modelu "Capacity", musí používat Image BYOL. Pokud testujete pomocí Azure Stack Development Kit (ASDK), můžete použít kteroukoli z těchto možností.

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>Co když jsem stáhl špatnou verzi, aby nabízela své klienty/uživatele?

Nejdřív odstraňte neplatnou verzi prostřednictvím správy Marketplace. Počkejte, než se dokončí (podívejte se na oznámení o dokončení, ne na okno **pro správu Marketplace** ). Pak stáhněte správnou verzi.

Pokud stáhnete obě verze image, budou koncovým zákazníkům v galerii Marketplace vidět jenom nejnovější verze.

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>Co když můj uživatel nesprávně zaškrtne políčko mám licenci v předchozích sestaveních Windows a nemá licenci?

Pomocí následujícího skriptu můžete změnit atribut licenčního modelu tak, aby se přepnul z možnosti Přineste si vlastní licenci (BYOL) na model s průběžnými platbami (PAYG):

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

Typ licence vašeho virtuálního počítače můžete ověřit spuštěním následujících příkazů. Pokud licenční model uvádí **Windows_Server**, bude se vám účtovat cena za BYOL, jinak se vám bude účtovat měřič Windows za model PAYG:

```powershell
$vm | ft Name, VmId,LicenseType,ProvisioningState
```

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>Co když mám starší image a uživatel zapomněl, aby zkontroloval, že mám licenci, nebo používáme vlastní image a máme smlouva Enterprise nárok?

Atribut licenčního modelu můžete změnit na model Přineste si vlastní licenci tak, že spustíte následující příkazy:

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>Jaké jsou další virtuální počítače, které používají Windows Server, jako je SQL nebo Machine Learning Server?

Tyto image používají parametr **LicenseType** , takže budou platit při použití. Tento parametr můžete nastavit (viz předchozí odpověď na nejčastější dotazy). To platí jenom pro software Windows serveru, ne pro vrstvené produkty, jako je SQL, což vyžaduje, abyste si mohli přenést vlastní licenci. Platíte podle používání licencování se nevztahuje na vrstvené softwarové produkty.

Všimněte si, že je možné změnit pouze vlastnost **LicenseType** pro SQL Server imagí z webu Marketplace, pokud je verze xx. X. 20190410 nebo vyšší. Pokud používáte starší verzi SQL Server imagí z Marketplace, nemůžete změnit atribut **LicenseType** a musíte ho znovu nasadit pomocí nejnovějších imagí SQL Server z webu Marketplace.

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>Mám smlouva Enterprise (EA) a bude používat licenci na Windows Server pro EA. Jak zajistím, aby se obrázky správně účtují?

V šabloně Azure Resource Manager můžete přidat **LicenseType: Windows_Server** . Toto nastavení musí být přidáno do každého bloku prostředků virtuálního počítače.

## <a name="activation"></a>Aktivace

Pokud chcete aktivovat virtuální počítač s Windows serverem na rozbočovači Azure Stack, musí být splněné následující podmínky:

- Výrobce OEM nastavil odpovídající značku BIOS na každém hostitelském systému v Azure Stackovém centru.
- Systémy Windows Server 2012 R2 a Windows Server 2016 musí používat [automatickou aktivaci virtuálního počítače](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)). Služba správy klíčů (KMS) a další aktivační služby se v Azure Stackovém centru nepodporují.

### <a name="how-can-i-verify-that-my-virtual-machine-is-activated"></a>Jak se dá ověřit, jestli je můj virtuální počítač aktivovaný?

Z příkazového řádku s oprávněním vyšší úrovně spusťte následující příkaz:

```shell
slmgr /dlv
```

Pokud je tato možnost aktivována správně, zobrazí se tato jasně uvedená a název hostitele zobrazený ve výstupu `slmgr`. Nezáleží na vodoznakech na zobrazení, protože nemusí být aktuální nebo se zobrazují z jiného virtuálního počítače na pozadí.

### <a name="my-vm-is-not-set-up-to-use-avma-how-can-i-fix-it"></a>Můj virtuální počítač není nastavený tak, aby používal AVMA, jak ho můžu opravit?

Z příkazového řádku s oprávněním vyšší úrovně spusťte následující příkaz:

```shell
slmgr /ipk <AVMA key>
```

Klíče, které se mají použít pro vaši image, najdete v článku věnovaném [automatické aktivaci virtuálního počítače](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) .

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>Můžu vytvořit vlastní image Windows serveru, jak se dá zajistit, aby používali AVMA?

Před spuštěním příkazu `sysprep` se doporučuje spustit příkazový řádek `slmgr /ipk` s příslušným klíčem. Případně zahrňte klíč AVMA do libovolného instalačního souboru Unattend. exe.

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-it-is-not-activating-or-using-kms-activation"></a>Snažím se použít bitovou kopii Windows serveru 2016 vytvořenou v Azure a neaktivuje ani nepoužívá aktivaci pomocí služby správy klíčů.

Spusťte příkaz `slmgr /ipk`. Image Azure se nemusí správně vracet do AVMA, ale pokud se můžou dostat do systému správy klíčů Azure, aktivují se. Doporučuje se, abyste zajistili, že jsou tyto virtuální počítače nastavené na používání AVMA.

### <a name="i-have-performed-all-of-these-steps-but-my-virtual-machines-are-still-not-activating"></a>Provedli jste všechny tyto kroky, ale virtuální počítače se ještě neaktivují.

Obraťte se na dodavatele hardwaru a ověřte, zda byly nainstalovány správné značky systému BIOS.

### <a name="what-about-earlier-versions-of-windows-server"></a>Co jsou starší verze Windows serveru?

[Automatická aktivace virtuálního počítače](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) není v dřívějších verzích Windows serveru podporovaná. Virtuální počítače musíte aktivovat ručně.

## <a name="next-steps"></a>Další kroky

Další informace najdete v těchto článcích:

- [Přehled centra Azure Stack Marketplace](azure-stack-marketplace.md)
- [Stažení položek z webu Marketplace z Azure do centra Azure Stack](azure-stack-download-azure-marketplace-item.md)
