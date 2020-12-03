---
title: Nasazení základních vzorů do centra Azure Stack
description: Naučte se nasazovat základní modely pomocí centra Azure Stack.
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 5609d9195a909a20a54917555a7309fe0901d7fa
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525468"
---
# <a name="deploy-foundational-patterns-overview"></a>Přehled nasazení základních vzorů


Každý z těchto vzorů obsahuje doprovodné materiály, Azure Resource Manager šablony a kurzy. Pomocí těchto vzorů spolu s aplikacemi třetích stran můžete vytvářet nabídky, které se Azure Stack ještě nepodporují. Například operátoři často řeší složitost při nastavení virtuální privátní sítě (VPN) na jednu instanci centra Azure Stack, mnohem méně vytváření sítě VPN, která zahrnuje dvě nebo více prostředí. Operátory můžou při pokusu o vytvoření nástroje pro vyrovnávání zatížení před Azure Stack centra pro správu úloh přijít mezi problémy. Následující doprovodné materiály vám pomůžou zrychlit nasazení, aby se uvolnily úlohy připravené pro produkční prostředí.

## <a name="networking"></a>Sítě

Pomocí vzorů sítě najdete pokyny k vytvoření partnerského vztahu virtuálních sítí pomocí centra Azure Stack. Partnerský vztah virtuálních sítí umožňuje propojit dvě virtuální sítě, aby se zobrazily jako jediná síť. Připojení Site-to-site mezi virtuálními sítěmi se provádí prostřednictvím služby Remote and Routing Service (RRAS). Služba RRAS umožňuje virtuálním počítačům s Windows pracovat jako se směrovači. Pomocí těchto skriptů můžete nasadit dvě virtuální sítě napříč skupinami prostředků v jedné Azure Stack skupiny prostředků centra, mezi předplatnými a mezi dvěma Azure Stackmi instancemi centra. Skripty můžete nasadit do centra Azure Stack a do globální služby Azure. 

Každý článek se zabývá běžnými aspekty, jako například: 
- Měřítko
- Šířka pásma
- Zabezpečení
- Kontinuita podnikových procesů

|  Peering virtuálních sítí  |  Síť VPN  |  Nástroj pro vyrovnávání zatížení  |
| --- | --- | --- |
| ![Partnerský vztah virtuální sítě s virtuálními počítači](media/deploy-foundational-patterns/icon-networking-61-virtual-networks.svg)<br>[Partnerský vztah virtuální sítě s virtuálními počítači](azure-stack-network-howto-vnet-peering.md) | ![Nastavení sítě VPN na Prem](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Nastavit VPN na Prem](azure-stack-network-howto-vnet-to-onprem.md) | ![F5 pro vyrovnávání zatížení](media/deploy-foundational-patterns/icon-networking-62-load-balancers.svg)<br>[F5 pro vyrovnávání zatížení](network-howto-f5.md) |
| ![Partnerský vztah virtuální sítě s FortiGate](media/deploy-foundational-patterns/icon-networking-61-virtual-networks.svg)<br>[Partnerský vztah virtuální sítě s FortiGate](azure-stack-network-howto-vnet-to-vnet.md) | ![Virtuální privátní síť](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Připojení virtuální sítě k virtuální síti](azure-stack-network-howto-vnet-to-vnet-stacks.md) |  |
|  | ![Vytvoření tunelu VPN (GRE)](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Vytvoření tunelu VPN (GRE)](network-howto-vpn-tunnel-gre.md) | |
|  | ![Nastavení více sítí VPN typu Site-to-site](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Nastavení více sítí VPN typu Site-to-site](network-howto-vpn-tunnel.md) | |
|  | ![Vytvoření tunelu VPN (IPSEC)](media/deploy-foundational-patterns/icon-networking-63-virtual-network-gateways.svg)<br>[Vytvoření tunelu VPN (IPSEC)](network-howto-vpn-tunnel-ipsec.md)| |


## <a name="storage"></a>Úložiště

Pomocí vzorů úložiště zvyšte možnosti úložiště pomocí centra Azure Stack. V Azure Stack je úložiště centra omezené. Připojte se k prostředkům v existujícím datovém centru. Přečtěte si pokyny k vytvoření virtuálního počítače s Windows v centru Azure Stack pro připojení k externímu cíli iSCSI. Informace o tom, jak povolit klíčové funkce, jako je funkce Multipath I/O (MPIO), vám umožní optimalizovat výkon a možnosti připojení mezi virtuálním počítačem a externím úložištěm.

| úložiště iSCSI | Rozšiřování úložiště |
| --- | --- |
| ![Připojení k úložišti iSCSI](media/deploy-foundational-patterns/icon-storage-87-storage-accounts-classic.svg)<br>[Připojení k úložišti iSCSI](azure-stack-network-howto-iscsi-storage.md) | ![Rozšiřování datacentra](media/deploy-foundational-patterns/icon-storage-88-recovery-services-vaults.svg)<br>[Rozšiřování datacentra](azure-stack-network-howto-extend-datacenter.md) |

## <a name="backup"></a>Backup

K kopírování všech prostředků v předplatném do Azure nebo jiné instance centra Azure Stack můžete použít vzory zálohování a zotavení po havárii. Tyto vzory se při replikaci informací uložených v rámci virtuálních počítačů do jiného prostředí prohledají pomocí CommVault Live Sync. Můžete najít skripty pro vytvoření účtu úložiště a účet úložiště pro zálohování, který bude odesílat data. S modulem replikátoru předplatného Azure můžete orchestrovat replikaci prostředků a můžete přizpůsobit procesor tak, aby zpracovával nejrůznější prostředky. 



|  Zálohování  |  Kopírovat  |
| --- | --- |
| ![Zálohování virtuálního počítače v Azure Stack hub pomocí CommVault](media/deploy-foundational-patterns/icon-storage-100-import-export-jobs.svg)<br>[Zálohování virtuálního počítače v Azure Stack hub pomocí CommVault](azure-stack-network-howto-backup-commvault.md) | ![Kopírovat prostředky předplatného](media/deploy-foundational-patterns/icon-storage-94-data-box.svg)<br>[Kopírovat prostředky předplatného](azure-stack-network-howto-backup-replicator.md) |
|  ![Zálohování účtů úložiště v centru Azure Stack](media/deploy-foundational-patterns/icon-storage-93-storage-sync-services.svg)<br>[Zálohování účtů úložiště v centru Azure Stack](azure-stack-network-howto-backup-storage.md)  | |

## <a name="github-samples"></a>Ukázky Githubu

Šablony najdete v úložišti [GitHub ve vzorcích Azure Intelligent Edge](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) .

## <a name="next-steps"></a>Další kroky

[Dokumentace k hybridním vzorům a řešením Azure](/hybrid/app-solutions)
