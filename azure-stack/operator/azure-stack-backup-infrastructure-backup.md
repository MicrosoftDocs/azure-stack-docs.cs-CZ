---
title: Obnovení dat v Azure Stack pomocí služby Infrastructure Backup | Microsoft Docs
description: Naučte se zálohovat a obnovovat data konfigurace a služby v Azure Stack pomocí služby Infrastructure Backup.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 832d2146e79f3724c5f1b3bf9da1776388636da8
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974708"
---
# <a name="recover-data-in-azure-stack-with-the-infrastructure-backup-service"></a>Obnovení dat v Azure Stack pomocí služby Infrastructure Backup

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Data konfigurace a služby můžete zálohovat a obnovovat pomocí služby Azure Stack Infrastructure Backup. Každá instalace Azure Stack obsahuje instanci služby. Zálohy vytvořené službou můžete použít pro opětovné nasazení cloudu Azure Stack pro obnovení dat identity, zabezpečení a Azure Resource Manager.

Pokud jste připraveni do provozu převést do produkčního prostředí, povolte zálohování. Pokud plánujete provádět testování a ověřování po dlouhou dobu, nepovolujte zálohování.

Než povolíte službu zálohování, ujistěte se, že máte zavedené [požadavky](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Služba Infrastructure Backup nezahrnuje uživatelská data a aplikace. Další informace o tom, jak chránit IaaS aplikace založené na virtuálních počítačích, najdete v tématu [ochrana virtuálních počítačů nasazených v Azure Stack](../user/azure-stack-manage-vm-protect.md). Ucelený přehled o tom, jak chránit aplikace v Azure Stack, najdete v tématu [Azure Stack důležitých informací o provozní kontinuitě a dokumentu White paper pro zotavení po havárii](https://aka.ms/azurestackbcdrconsiderationswp).

## <a name="the-infrastructure-backup-service"></a>Služba Infrastructure Backup

Služba obsahuje následující funkce:

| Funkce                                            | Popis                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Služby infrastruktury zálohování                     | Koordinovat zálohování v rámci podmnožiny služby infrastruktury v Azure Stack. Pokud dojde k havárii, můžete data obnovit v rámci opětovného nasazení. |
| Komprese a šifrování exportovaných zálohovaných dat | Před exportem do externího úložiště, které poskytuje správce, se zálohovaná data komprimují a šifrují systémem.                |
| Monitorování úlohy zálohování                              | Systém upozorní vás, když úlohy zálohování selžou a jak problém vyřešit.                                                                                                |
| Prostředí pro správu zálohování                       | Záložní RP podporuje povolení zálohování.                                                                                                                         |
| Cloud Recovery                                     | Pokud dojde ke závažné ztrátě dat, můžete zálohy použít k obnovení základních Azure Stack informací jako součást nasazení.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Ověření požadavků pro službu Infrastructure Backup

- **Umístění úložiště**  
  Potřebujete ke sdílení souborů přístup z Azure Stack, která může obsahovat sedm záloh. Každá záloha je přibližně 10 GB. Vaše sdílená složka by měla být schopná uchovávat 140 GB záloh. Další informace o výběru umístění úložiště pro službu Infrastructure Backup najdete v tématu požadavky na [kontrolér zálohování](azure-stack-backup-reference.md#backup-controller-requirements).
- **Přihlašovací údaje**  
  Potřebujete účet uživatele domény a přihlašovací údaje. Můžete například použít přihlašovací údaje správce Azure Stack.
- **Certifikát šifrování**  
  Záložní soubory se šifrují pomocí veřejného klíče v certifikátu. Nezapomeňte tento certifikát uložit na bezpečném místě. 


## <a name="next-steps"></a>Další kroky

Naučte se, jak [Povolit zálohování pro Azure Stack z portálu pro správu](azure-stack-backup-enable-backup-console.md).

Naučte se, jak [Povolit zálohování pro Azure Stack pomocí PowerShellu](azure-stack-backup-enable-backup-powershell.md).

Naučte se, jak [zálohovat Azure Stack](azure-stack-backup-back-up-azure-stack.md).

Naučte se [obnovit z závažné ztráty dat](azure-stack-backup-recover-data.md).
