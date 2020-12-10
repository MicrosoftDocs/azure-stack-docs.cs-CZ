---
title: Nejčastější dotazy týkající se Azure Stack Windows serveru | Microsoft Docs
description: Seznam nejčastějších dotazů na Azure Stack Marketplace pro Windows Server
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/16/2019
ms.openlocfilehash: 6286939eeedc263540018aeea3ed62d02b706f24
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939612"
---
# <a name="windows-server-in-azure-stack-marketplace-faq"></a>Nejčastější dotazy k Windows serveru v Azure Stack Marketplace

Tento článek obsahuje odpovědi na některé nejčastější dotazy týkající se imagí Windows serveru v [tržišti Azure Stack hub](../../operator/azure-stack-marketplace.md).

## <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack"></a>Jaké jsou možnosti licencování imagí Windows serveru Marketplace na Azure Stack?

Uživatelé MDC centra Azure Stack mají nárok na bezplatné použití Windows serveru jako hostovaný operační systém.

Microsoft nabízí dvě verze imagí Windows serveru na webu Azure Stack Marketplace. V kterémkoli daném Azure Stack prostředí se dá použít jenom jedna verze této image.

- **Platíte podle svých využití**: tyto Image by se neměly používat v Azure Stack zaMDCm nebo nem centru.

- **Přineste si vlastní licenci (BYOL)**: tyto Image je možné používat na Azure Stackovém centru robustní a MDC.

## <a name="what-about-other-vms-that-use-windows-server-such-as-sql-server"></a>Jaké jsou další virtuální počítače, které používají Windows Server, například SQL Server?

Licence na software Windows serveru se vztahuje jenom na operační systém Windows, ne na vrstvené produkty, jako je SQL, které vyžadují vlastní licenci.

## <a name="how-do-i-update-to-a-newer-windows-image"></a>Návody aktualizaci na novější bitovou kopii Windows?

Nejdřív Zjistěte, jestli některé šablony Azure Resource Manager odkazují na konkrétní verze. Pokud ano, aktualizujte tyto šablony nebo Udržujte starší verze imagí. Je nejvhodnější použít **verzi: nejnovější**.

Pokud navíc některé sady škálování virtuálních počítačů odkazují na konkrétní verzi, měli byste uvažovat o tom, jestli se budou škálovat později, a rozhodnout se, jestli chcete zachovat starší verze. Pokud se nepoužije žádná z těchto podmínek, před stažením novějších imagí na webu Marketplace odstraňte starší image. Pomocí správy Marketplace je odstraňte, pokud to znamená, jak byl původní stažený. Pak stáhněte novější verzi.

## <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>Co když jsem stáhl špatnou verzi, aby nabízela své klienty/uživatele?

Nejdřív odstraňte neplatnou verzi prostřednictvím správy Marketplace. Počkejte, než se dokončí (podívejte se na oznámení o dokončení, ne na okno **pro správu Marketplace** ). Pak stáhněte správnou verzi.

Pokud stáhnete obě verze image, budou koncovým zákazníkům v galerii Marketplace vidět jenom nejnovější verze.

## <a name="activation"></a>Aktivace

Chcete-li aktivovat virtuální počítač s Windows serverem na Azure Stack, musí být splněna následující podmínka:

- Systémy Windows Server 2012 R2 a Windows Server 2016 musí používat [automatickou aktivaci virtuálního počítače](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)). Služba správy klíčů (KMS) a další aktivační služby nejsou v Azure Stack podporovány.

## <a name="how-can-i-verify-that-my-virtual-machine-is-activated"></a>Jak se dá ověřit, jestli je můj virtuální počítač aktivovaný?

Z příkazového řádku s oprávněním vyšší úrovně spusťte následující příkaz:

```shell
slmgr /dlv
```

Pokud je tato možnost aktivována správně, zobrazí se tato jasně a název hostitele zobrazený ve výstupu příkazu Slmgr. Nezáleží na vodoznakech na zobrazení, protože nemusí být aktuální nebo se zobrazují z jiného virtuálního počítače na pozadí.

## <a name="my-vm-is-not-set-up-to-use-avma-how-can-i-fix-it"></a>Můj virtuální počítač není nastavený tak, aby používal AVMA, jak ho můžu opravit?

Z příkazového řádku s oprávněním vyšší úrovně spusťte následující příkaz:

```shell
slmgr /ipk <AVMA key>
```

Klíče, které se mají použít pro vaši image, najdete v článku věnovaném [automatické aktivaci virtuálního počítače](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) .

## <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>Můžu vytvořit vlastní image Windows serveru, jak se dá zajistit, aby používali AVMA?

Doporučuje se `slmgr /ipk` před spuštěním příkazu spustit příkaz s příslušným klíčem `sysprep` . Případně zahrňte klíč AVMA do libovolného souboru instalačního programu Unattend.exe.

## <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-it-is-not-activating-or-using-kms-activation"></a>Snažím se použít bitovou kopii Windows serveru 2016 vytvořenou v Azure a neaktivuje ani nepoužívá aktivaci pomocí služby správy klíčů.

Spusťte příkaz `slmgr /ipk`. Image Azure se nemusí správně vracet do AVMA, ale pokud se můžou dostat do systému správy klíčů Azure, aktivují se. Doporučuje se, abyste zajistili, že jsou tyto virtuální počítače nastavené na používání AVMA.

## <a name="i-have-performed-all-of-these-steps-but-my-virtual-machines-are-still-not-activating"></a>Provedli jste všechny tyto kroky, ale virtuální počítače se ještě neaktivují.

Chcete-li ověřit, zda byly nainstalovány správné značky systému BIOS, obraťte se na podporu společnosti Microsoft.

## <a name="what-about-earlier-versions-of-windows-server"></a>Co jsou starší verze Windows serveru?

[Automatická aktivace virtuálního počítače](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) není podporovaná ve verzích Windows serveru starších než 2012 R2. Virtuální počítače musíte aktivovat ručně pomocí klíče k vícenásobné aktivaci.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Přehled Azure Stack Marketplace](../../operator/azure-stack-marketplace.md)
- [Stažení položek z marketplace z Azure do služby Azure Stack](azure-stack-download-azure-marketplace-item-tca.md)
