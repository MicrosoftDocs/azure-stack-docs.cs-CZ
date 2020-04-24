---
title: Nejčastější dotazy k webu Azure Stack hub Marketplace
titleSuffix: Azure Stack Hub
description: Seznam nejčastějších dotazů k webu centra Azure Stack pro Windows Server.
author: sethmanheim
ms.topic: article
ms.date: 03/19/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 08/29/2019
ms.openlocfilehash: 95719c6b0651932ab41cef5321db06b77eb4fc63
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "80069445"
---
# <a name="azure-stack-hub-marketplace-faq"></a>Nejčastější dotazy k webu Azure Stack hub Marketplace

Tento článek obsahuje odpovědi na některé nejčastější dotazy týkající se položek Marketplace na [webu centrum Azure Stack](azure-stack-marketplace.md).

## <a name="marketplace-items"></a>Položky Marketplace

### <a name="who-should-i-contact-for-support-issues-with-azure-stack-hub-marketplace-items"></a>Koho se mám obrátit na problémy podpory s položkami Azure Stack centra Marketplace?

Pokyny k podpoře Azure Marketplace se týkají také položek centra pro Azure Stack na webu Marketplace. Vydavatelé zodpovídají za poskytování technické podpory svých produktů na webu centra Azure Stack. Další informace o pokynech k podpoře pro Azure Marketplace položky najdete v [části věnované podpoře](/azure/marketplace/marketplace-faq-publisher-guide#customer-support) v článku Azure Marketplace Nejčastější dotazy.

### <a name="how-do-i-update-to-a-newer-windows-image"></a>Návody aktualizaci na novější bitovou kopii Windows?

Nejdřív Zjistěte, jestli některé šablony Azure Resource Manager odkazují na konkrétní verze. Pokud ano, aktualizujte tyto šablony nebo Udržujte starší verze imagí. Je nejvhodnější použít **verzi: nejnovější**.

Pokud navíc některé sady škálování virtuálních počítačů odkazují na konkrétní verzi, měli byste uvažovat o tom, jestli se budou škálovat později, a rozhodnout se, jestli chcete zachovat starší verze. Pokud se nepoužijí žádné z těchto podmínek, před stažením novějších verzí odstraňte starší image na webu Azure Stack hub Marketplace. Pomocí správy Marketplace je odstraňte, pokud je to způsob stažení originálu. Pak stáhněte novější verzi.

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack-hub"></a>Jaké jsou možnosti licencování pro image Windows serveru Marketplace v centru Azure Stack?

Microsoft nabízí dvě verze imagí Windows serveru prostřednictvím tržiště centra Azure Stack. V prostředí Azure Stack hub se dá použít jenom jedna verze této image.  

- **Průběžné platby dle aktuálního využití (PAYG)**: tyto image spouštějí celé ceny Windows měřičů.
   Kdo má použít tuto možnost: zákazníci smlouva Enterprise (EA), kteří používají *Model fakturace spotřeby*; CSP, kteří nechtějí používat licencování SPLA
- **Přineste si vlastní licenci (BYOL)**: tyto image spouštějí základní měřiče.
   Kdo má použít tuto možnost: zákazníci se smlouvou EA s licencí k Windows serveru; CSP, kteří používají licencování SPLA.

Zvýhodněné hybridní využití Azure (AHUB) se u centra Azure Stack nepodporuje. Zákazníci, kteří mají licenci prostřednictvím modelu "Capacity", musí používat Image BYOL. Pokud testujete pomocí Azure Stack Development Kit (ASDK), můžete použít kteroukoli z těchto možností.

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>Co když jsem stáhl špatnou verzi, aby nabízela své klienty/uživatele?

Nejdřív odstraňte neplatnou verzi prostřednictvím správy Marketplace. Počkejte, než se dokončí (podívejte se na oznámení o dokončení, ne na okno **pro správu Marketplace** ). Pak stáhněte správnou verzi.

Pokud stáhnete obě verze image, budou koncovým zákazníkům v tržišti centra Azure Stack viditelné jenom nejnovější verze.

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>Co když můj uživatel nesprávně zaškrtne políčko mám licenci v předchozích sestaveních Windows a nemá licenci?

Atribut licenčního modelu můžete změnit tak, aby se přepnul z BYOL na model PAYG spuštěním následujícího skriptu:

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

Typ licence vašeho virtuálního počítače můžete ověřit spuštěním následujících příkazů. Pokud licenční model uvádí **Windows_Server**, bude se vám účtovat cena za BYOL. V opačném případě se vám bude účtovat měřič Windows podle modelu PAYG:

```powershell
$vm | ft Name, VmId,LicenseType,ProvisioningState
```

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>Co když mám starší image a uživatel zapomněl, aby zkontroloval, že mám licenci, nebo používáme vlastní image a máme smlouva Enterprise nárok?

Atribut licenčního modelu můžete změnit na model BYOL spuštěním následujících příkazů:

```powershell
$vm= Get-Azurermvm -ResourceGroup "<your RG>" -Name "<your VM>"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName "<your RG>" -VM $vm
```

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>Jaké jsou další virtuální počítače, které používají Windows Server, jako je SQL nebo Machine Learning Server?

Tyto image používají parametr **LicenseType** , aby byly PAYG. Tento parametr můžete nastavit (viz předchozí odpověď na nejčastější dotazy). To platí jenom pro software Windows serveru, ne pro vrstvené produkty, jako je SQL, což vyžaduje, abyste si mohli přenést vlastní licenci. Licencování PAYG se nevztahuje na vrstvené softwarové produkty.

Vlastnost **LicenseType** pro image SQL Server můžete změnit jenom z webu služby Azure Stack hub Marketplace, pokud je verze xx. X. 20190410 nebo vyšší. Pokud používáte starší verzi SQL Server imagí z webu služby Azure Stack hub Marketplace, nemůžete změnit atribut **LicenseType** a musíte ho znovu nasadit pomocí nejnovějších SQL Server imagí z webu centra pro Azure Stack.

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>Mám smlouva Enterprise (EA) a bude používat licenci na Windows Server pro EA. Jak zajistím, aby se obrázky správně účtují?

V šabloně Azure Resource Manager můžete přidat **LicenseType: Windows_Server** . Toto nastavení musí být přidáno do každého bloku prostředků virtuálního počítače.

## <a name="activation"></a>Aktivace

Pokud chcete aktivovat virtuální počítač s Windows serverem na rozbočovači Azure Stack, musí být splněné následující podmínky:

- Výrobce OEM nastavil odpovídající značku BIOS na každém hostitelském systému v Azure Stackovém centru.
- Systémy Windows Server 2012 R2 a Windows Server 2016 musí používat [automatickou aktivaci virtuálního počítače](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)). Služba správy klíčů (KMS) a další aktivační služby nejsou podporovány v Azure Stackm centru.

### <a name="how-can-i-verify-that-my-vm-is-activated"></a>Jak můžu ověřit, jestli je můj virtuální počítač aktivovaný?

Z příkazového řádku s oprávněním vyšší úrovně spusťte následující příkaz:

```shell
slmgr /dlv
```

Pokud je tato možnost aktivována správně, zobrazí se tato jasně zpráva a název hostitele zobrazený ve `slmgr` výstupu. Nezáleží na vodoznakech na zobrazení, protože nemusí být aktuální nebo se zobrazují z jiného virtuálního počítače na pozadí.

### <a name="my-vm-isnt-set-up-to-use-avma-how-can-i-fix-it"></a>Můj virtuální počítač není nastavený tak, aby používal AVMA, jak ho můžu opravit?

Z příkazového řádku s oprávněním vyšší úrovně spusťte následující příkaz:

```shell
slmgr /ipk <AVMA key>
```

Klíče, které se mají použít pro vaši image, najdete v článku věnovaném [automatické aktivaci virtuálního počítače](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) .

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>Můžu vytvořit vlastní image Windows serveru, jak se dá zajistit, aby používali AVMA?

Před spuštěním `slmgr /ipk` `sysprep` příkazu doporučujeme spustit příkazový řádek s příslušným klíčem. Případně zahrňte klíč AVMA do libovolného instalačního souboru Unattend. exe.

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-its-not-activating-or-using-kms-activation"></a>Snažím se použít bitovou kopii Windows serveru 2016 vytvořenou v Azure a aktivace pomocí služby správy klíčů se neaktivuje.

Spusťte příkaz `slmgr /ipk`. Image Azure se nemusí správně vracet do AVMA, ale pokud se můžou dostat do systému správy klíčů Azure, aktivují se. Doporučuje se, abyste zajistili, že jsou tyto virtuální počítače nastavené na používání AVMA.

### <a name="i-have-performed-all-of-these-steps-but-my-vms-are-still-not-activating"></a>Provedli jste všechny tyto kroky, ale virtuální počítače se ještě neaktivují.

Obraťte se na dodavatele hardwaru a ověřte, zda byly nainstalovány správné značky systému BIOS.

### <a name="what-about-earlier-versions-of-windows-server"></a>Co jsou starší verze Windows serveru?

[Automatická aktivace virtuálního počítače](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) se v dřívějších verzích Windows serveru nepodporuje. Virtuální počítače musíte aktivovat ručně.

## <a name="next-steps"></a>Další kroky

Další informace najdete v těchto článcích:

- [Přehled centra Azure Stack Marketplace](azure-stack-marketplace.md)
- [Stažení položek z webu Marketplace z Azure do centra Azure Stack](azure-stack-download-azure-marketplace-item.md)
