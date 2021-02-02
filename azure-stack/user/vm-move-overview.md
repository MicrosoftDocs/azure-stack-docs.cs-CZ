---
title: Přesunutí virtuálního počítače do centra Azure Stack
description: Přečtěte si o různých způsobech, kterými můžete virtuální počítač přesunout do centra Azure Stack.
author: mattbriggs
ms.topic: conceptual
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: cfcb5b494374fedaa5bdced131a8a42203aa5eb0
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99246567"
---
# <a name="move-a-vm-to-azure-stack-hub-overview"></a>Přesunutí virtuálního počítače do centra Azure Stack – přehled

Virtuální počítače (VM) s můžete přesunout z vašeho prostředí do centra Azure Stack. Při plánování přesunu vašich úloh je potřeba očekávat některá omezení. V tomto článku jsou uvedené požadavky na virtuální pevný disk (VHD) s v Azure Stackovém centru. Azure Stack hub vyžaduje generaci jednoho virtuálního pevného disku (1). Váš virtuální počítač bude muset být zobecněný nebo specializovaný. Pro virtuální počítače vytvořené v Azure Stack použijte generalizované virtuální počítače jako základní. Specializovaný virtuální počítač obsahuje uživatelské účty. Pokud chcete migrovat, připravit a stahovat VHD, ověřte, že virtuální pevný disk splňuje požadavky, nahrajte image do účtu úložiště v centru Azure Stack a pak vytvořte virtuální počítač v cloudu. Pokud máte složitější úlohu migrace, najdete kompletní diskuzi na dokumentu White Paper *k migraci na centrum Azure Stack* .

Vlastní image se přidávají ve dvou formách: **generalizovaná** a **specializovaná**.

- **Zobecněná bitová kopie**  
  Zobecněná bitová kopie disku je ta, která byla připravena nástrojem **Sysprep** pro odebrání jedinečných informací (například uživatelských účtů), což umožňuje opakované použití pro vytvoření více virtuálních počítačů. Zobecněné virtuální pevné disky jsou vhodné při vytváření imagí, které používají operátory cloudu Azure Stack hub jako položky Marketplace. Obrázky nabízené prostřednictvím portálu správce nebo koncových bodů správce jsou **Image platformy**.

- **Specializovaný obrázek**  
  Specializovaná image disku je kopie virtuálního pevného disku (VHD) ze stávajícího virtuálního počítače, který obsahuje uživatelské účty, aplikace a další údaje o stavu z původního virtuálního počítače. Obvykle se jedná o formát, ve kterém jsou virtuální počítače migrovány do centra Azure Stack. Specializované virtuální pevné disky jsou vhodné pro potřebu migrace virtuálních počítačů z místního prostředí do centra Azure Stack.

Když přesunete obrázek do centra Azure Stack, zvažte, jak byste chtěli použít image.

- **Osobní zatížení**  
    Můžete mít počítač v místním prostředí nebo v globálním Azure, který používáte pro vývoj nebo konkrétní úkoly, a chcete využít jeho hostování v privátním cloudu pomocí centra Azure Stack. Chcete zachovat data a uživatelské účty v počítači. Tento počítač byste chtěli přesunout do centra Azure Stack jako speciální obrázek.

- **Zlatý obrázek**  
    Můžete chtít sdílet společnou konfiguraci virtuálních počítačů a sadu aplikací v rámci pracovní skupiny. Nebudete potřebovat sdílet image s uživateli mimo vaši doménu centra Azure Stack (tenant adresáře). V takovém případě budete chtít zobecnit bitovou kopii odebráním dat a uživatelských účtů. Tuto image pak můžete sdílet s dalšími uživateli ve vašem tenantovi.

- **Nabídka webu Marketplace centra Azure Stack**  
    Váš operátor cloudu může používat zobecněnou Image jako základ nabídky na webu Marketplace. Po přípravě image může operátor cloudu použít vlastní image k vytvoření nabídky Marketplace pro vaši instanci centra Azure Stack. Uživatelé si můžou z image vytvořit vlastní virtuální počítač, protože to budou všechny ostatní nabídky na webu Marketplace. Abyste mohli vytvořit tuto nabídku, budete muset pracovat s operátorem cloudu.

## <a name="verify-vhd-requirements"></a>Ověřit požadavky na virtuální pevný disk

> [!IMPORTANT]  
> Při přípravě virtuálního pevného disku je nutné, aby byly k dispozici následující požadavky nebo vaše virtuální pevný disk nebude možné použít v Azure Stackovém centru.
> Před nahráním image Vezměte v úvahu následující skutečnosti:
> - Rozbočovač Azure Stack podporuje jenom image z generace jednoho (1) virtuálních počítačů.
> - Virtuální pevný disk je pevného typu. Centrum Azure Stack nepodporuje virtuální pevné disky (VHD) dynamického disku.
> - Virtuální pevný disk má minimální virtuální velikost alespoň 20 MB.
> - Virtuální pevný disk je zarovnán, to znamená, že virtuální velikost musí být násobkem 1 MB.
> - Délka objektu BLOB VHD = virtuální velikost + délka zápatí VHD (512). Malé zápatí na konci objektu BLOB popisuje vlastnosti VHD. 

Kroky k opravě VHD najdete v [ověření virtuálního pevného disku](vm-move-from-azure.md#verify-your-vhd) .

## <a name="methods-of-moving-a-vm"></a>Metody přesunu virtuálního počítače

Virtuální počítač můžete ručně přesunout do centra Azure Stack s následujícími scénáři:

| Scenario | Pokyny |
| --- | --- |
| Globální centrum pro Azure do Azure Stack | Připravte virtuální pevný disk v globálním Azure a pak ho nahrajte do centra Azure Stack. Další informace najdete v tématu [přesun virtuálního počítače z Azure do centra Azure Stack](vm-move-from-azure.md). |
| Zobecnění z místního prostředí do centra Azure Stack | Připravte virtuální pevný disk a proveďte v prostředí Hyper-V lokálně virtuální pevný disk a potom ho nahrajte do centra Azure Stack. Další informace najdete v tématu [Přesun generalizované virtuální počítače z místního počítače do centra Azure Stack](vm-move-generalized.md). |
| Místní specializované na centrum Azure Stack | Připravte specializovaný virtuální pevný disk místně v Hyper-V a pak ho nahrajte do centra Azure Stack. Další informace najdete v tématu [přesunutí specializovaného virtuálního počítače z místního počítače do centra Azure Stack](vm-move-specialized.md). |

## <a name="migrate-to-azure-stack-hub"></a>Migrovat do centra Azure Stack

Podrobnosti, kontrolní seznamy a osvědčené postupy pro plánování a migraci hromadných úloh můžete získat do Azure Stack centra v průvodci, který napsali odborníci na zákaznického poradního v globálním Azure. Tato příručka se zaměřuje na migraci stávajících aplikací, které běží na fyzických serverech nebo na stávajících virtualizačních platformách. Přesunem těchto úloh do prostředí Azure Stack Hub IaaS získají týmy výhody v podobě plynulejšího provozu, samoobslužných nasazení, standardizovaných konfigurací hardwaru a konzistence Azure.

[Získejte dokument White Paper.](https://azure.microsoft.com/resources/migrate-to-azure-stack-hub-patterns-and-practices-checklists/)

Pokyny k migraci můžete najít také v rozhraní pro přijetí do cloudu. Další informace najdete v tématu [Plánování migrace centra Azure Stack](/azure/cloud-adoption-framework/scenarios/azure-stack/plan). 

## <a name="next-steps"></a>Další kroky

[Úvod k virtuálním počítačům centra Azure Stack](azure-stack-compute-overview.md)

[Přidání vlastní image virtuálního počítače do centra Azure Stack](../operator/azure-stack-add-vm-image.md)