---
title: Úvod do sítě Azure Stack hub
description: Další informace o sítích centra Azure Stack
author: mattbriggs
ms.topic: conceptual
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: ca8c93e983290e925d3af38bef0799d11c4a745a
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247604"
---
# <a name="introduction-to-azure-stack-hub-networking"></a>Úvod do sítě Azure Stack hub

Centrum Azure Stack nabízí různé druhy síťových schopností, které se dají použít společně nebo samostatně:

- **Připojení mezi prostředky centra Azure Stack**  
    Propojte prostředky Azure společně v zabezpečené a privátní virtuální síti v cloudu.
- **Připojení k internetu**  
    Komunikace s prostředky centra Azure Stack a jejich využití přes Internet.
- **Místní připojení**  
    Připojte místní síť, abyste mohli Azure Stack prostředky centra prostřednictvím virtuální privátní sítě (VPN) přes Internet nebo prostřednictvím vyhrazeného připojení k Azure Stackmu centru. 
    > [!IMPORTANT]
    > Aby bylo možné získat přístup k místním prostředkům, je nutné vytvořit připojení VPN nebo veřejné IP adresy.
- **Vyrovnávání zatížení a směr provozu**  
    Vyrovnávání zatížení provozu na servery ve stejném umístění a přímý provoz na servery v různých umístěních.
- **Zabezpečení**  
    Filtrovat síťový provoz mezi síťovými podsítěmi nebo jednotlivými virtuálními počítači.
- **Směrování**  
    Použijte výchozí směrování nebo plně řídit směrování mezi Azure Stackm rozbočovačem a místními prostředky.
- **Možnosti správy**  
    Monitorujte a spravujte své síťové prostředky centra Azure Stack.
- **Nástroje pro nasazení a konfiguraci**  
    K nasazení a konfiguraci síťových prostředků použijte webové portály nebo nástroje příkazového řádku pro různé platformy.


## <a name="next-steps"></a>Další kroky

* [Požadavky na síť centra Azure Stack](azure-stack-network-differences.md)