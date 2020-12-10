---
title: Obnovení dat v Azure Stack pomocí služby Infrastructure Backup | Microsoft Docs
description: Naučte se zálohovat a obnovovat data konfigurace a služby v Azure Stack pomocí služby Infrastructure Backup.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/29/2020
ms.openlocfilehash: 1941a1347e7b6ce3943a8cfae02e07a6b41fa46f
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939552"
---
# <a name="recover-data-in-azure-stack-with-the-infrastructure-backup-service"></a>Obnovení dat v Azure Stack pomocí služby Infrastructure Backup

*Platí pro: modulární datová centra a centrum Azure Stack, které je robustní*

Metadata služby infrastruktury můžete zálohovat pomocí služby Azure Stack Infrastructure Backup. Tyto zálohy slouží k nápravě degradované služby infrastruktury. Záloha zahrnuje pouze data ze služeb infrastruktury interních do systému. Data zálohy neobsahují data uživatelů a aplikací. Data uživatelů a aplikací se musí chránit samostatně.

Ve výchozím nastavení je zálohování infrastruktury povolené v době nasazení. Tyto zálohy se ukládají do systému jako interní a jsou dostupné jenom během pokročilých případů podpory. Pokud má systém přístup k externímu úložišti, dá se službě infrastruktura zálohování dát pokyn exportovat zálohu do umístění úložiště jako sekundární kopie.

Než povolíte službu zálohování, ujistěte se, že máte zavedené [požadavky](../../operator/azure-stack-backup-reference.md#backup-controller-requirements).

> [!NOTE]
> Služba Infrastructure Backup nezahrnuje uživatelská data a aplikace. Další informace o tom, jak chránit IaaS aplikace založené na virtuálních počítačích, najdete v následujících článcích:
>
> - [Ochrana virtuálních počítačů nasazených ve službě Azure Stack](../../user/azure-stack-manage-vm-protect.md)
> - Ochrana dat Event Hubs časové řady Azure Stack
> - Chránit data objektů BLOB v Azure Stack

## <a name="the-infrastructure-backup-service"></a>Služba Infrastructure Backup

Služba obsahuje následující funkce:

| Funkce                                            | Popis                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Služby infrastruktury zálohování                     | Koordinuje zálohování napříč podmnožinou služeb infrastruktury v Azure Stack. |
| Komprese a šifrování zálohovaných dat | Zálohovaná data se komprimují a šifrují systémem předtím, než se \' uloží interně nebo exportují do externího úložiště v umístění, které poskytuje operátor.                |
| Monitorování úlohy zálohování                              | Systém upozorní vás, když úlohy zálohování selžou a jak problém vyřešit.                                                                                                |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Ověření požadavků pro službu Infrastructure Backup

- **Umístění úložiště** Pokud je k externímu úložišti k dispozici spolehlivý přístup, potřebujete ke sdílení souborů přístup z infrastruktury Azure Stack, která může ukládat více záloh. Další informace o výběru umístění úložiště pro službu Infrastructure Backup najdete v tématu požadavky na [kontrolér zálohování](../../operator/azure-stack-backup-reference.md#backup-controller-requirements).

- **Přihlašovací údaje** Potřebujete přihlašovací údaje uživatelského účtu, které mají přístup k umístění úložiště.

## <a name="next-steps"></a>Další kroky

- Naučte se, jak [Povolit zálohování pro Azure Stack z portálu pro správu](../../operator/azure-stack-backup-enable-backup-console.md).

- Naučte se, jak [Povolit zálohování pro Azure Stack pomocí PowerShellu](../../operator/azure-stack-backup-enable-backup-powershell.md).
