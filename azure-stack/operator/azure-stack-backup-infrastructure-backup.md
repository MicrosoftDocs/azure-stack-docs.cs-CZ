---
title: Obnovení dat v Azure Stack centru pomocí služby Infrastructure Backup
description: Naučte se zálohovat a obnovovat data konfigurace a služby v Azure Stack hub pomocí služby Infrastructure Backup.
author: justinha
ms.topic: article
ms.date: 05/16/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: a9ebb725bed0472eaacc80e821840746966104dd
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76874327"
---
# <a name="recover-data-in-azure-stack-hub-with-the-infrastructure-backup-service"></a>Obnovení dat v Azure Stack centru pomocí služby Infrastructure Backup

Data konfigurace a služby můžete zálohovat a obnovovat pomocí služby Azure Stack hub Infrastructure Backup. Každá instalace centra Azure Stack obsahuje instanci služby. Zálohy vytvořené službou můžete použít pro opětovné nasazení cloudu centra Azure Stack pro obnovení dat identity, zabezpečení a Azure Resource Manager.

Pokud jste připraveni do provozu převést do produkčního prostředí, povolte zálohování. Pokud plánujete provádět testování a ověřování po dlouhou dobu, nepovolujte zálohování.

Než povolíte službu zálohování, ujistěte se, že máte zavedené [požadavky](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Služba Infrastructure Backup nezahrnuje uživatelská data a aplikace. Další informace o tom, jak chránit IaaS aplikace založené na virtuálních počítačích, najdete v tématu [ochrana virtuálních počítačů nasazených v centru Azure Stack](../user/azure-stack-manage-vm-protect.md). Ucelený přehled o tom, jak chránit aplikace v Azure Stackovém centru, najdete v tématu [požadavky centra Azure Stack na pracovní postupy pro provozní kontinuitu a zotavení po havárii](https://aka.ms/azurestackbcdrconsiderationswp).

## <a name="the-infrastructure-backup-service"></a>Služba Infrastructure Backup

Služba obsahuje následující funkce:

| Funkce                                            | Popis                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Služby infrastruktury zálohování                     | Koordinovat zálohování v rámci podmnožiny služby infrastruktury v centru Azure Stack. Pokud dojde k havárii, můžete data obnovit v rámci opětovného nasazení. |
| Komprese a šifrování exportovaných zálohovaných dat | Před exportem do externího úložiště, které poskytuje správce, se zálohovaná data komprimují a šifrují systémem.                |
| Monitorování úlohy zálohování                              | Systém upozorní vás, když úlohy zálohování selžou a jak problém vyřešit.                                                                                                |
| Prostředí pro správu zálohování                       | Záložní RP podporuje povolení zálohování.                                                                                                                         |
| Cloud Recovery                                     | Pokud dojde ke závažné ztrátě dat, můžou se zálohy použít k obnovení informací v základní Azure Stack centra jako součást nasazení.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Ověření požadavků pro službu Infrastructure Backup

- **Umístění úložiště**  
  Potřebujete sdílení souborů, které je dostupné z centra Azure Stack, které může obsahovat sedm záloh. Každá záloha je přibližně 10 GB. Vaše sdílená složka by měla být schopná uchovávat 140 GB záloh. Další informace o výběru umístění úložiště pro službu Infrastructure Backup najdete v tématu požadavky na [kontrolér zálohování](azure-stack-backup-reference.md#backup-controller-requirements).
- **Přihlašovací údaje**  
  Potřebujete účet uživatele domény a přihlašovací údaje. Můžete například použít svoje přihlašovací údaje správce centra Azure Stack.
- **Certifikát šifrování**  
  Záložní soubory se šifrují pomocí veřejného klíče v certifikátu. Nezapomeňte tento certifikát uložit na bezpečném místě. 


## <a name="next-steps"></a>Další kroky

Naučte se, jak [Povolit zálohování centra Azure Stack z portálu pro správu](azure-stack-backup-enable-backup-console.md).

Naučte se, jak [Povolit zálohování pro Azure Stack centrum pomocí PowerShellu](azure-stack-backup-enable-backup-powershell.md).

Naučte se [zálohovat centrum Azure Stack](azure-stack-backup-back-up-azure-stack.md).

Naučte se [obnovit z závažné ztráty dat](azure-stack-backup-recover-data.md).
