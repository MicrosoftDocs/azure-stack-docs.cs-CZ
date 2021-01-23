---
title: Porovnání Azure Stack HCl k Windows serveru
description: Toto téma vám pomůže určit, jestli je pro vaši organizaci nejvhodnější Azure Stack HCI nebo Windows Server.
ms.topic: conceptual
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 01/21/2021
ms.openlocfilehash: c691424251d096794315880106c131ecaaea1f57
ms.sourcegitcommit: 925351b77490364b3d52746f788c4c1b93343631
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98705183"
---
# <a name="compare-azure-stack-hci-to-windows-server-2019"></a>Porovnat Azure Stack HCI s Windows serverem 2019

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

Toto téma vysvětluje hlavní rozdíly mezi Azure Stackmi HCI a Windows serverem 2019 a poskytuje pokyny, kdy je potřeba použít. Oba produkty jsou aktivně podporovány a spravovány společností Microsoft. Mnoho organizací se může rozhodnout nasadit obojí, protože jsou určené pro různé a doplňkové účely.

## <a name="when-to-use-azure-stack-hci"></a>Kdy použít Azure Stack HCL

Azure Stack HCI je špičkovou infrastrukturou platformy Microsoftu pro spouštění virtuálních počítačů nebo virtuálních ploch s připojeními k Azure Hybrid Services. Je to snadný způsob, jak modernizovat a zabezpečovat datová centra a firemní pobočky a dosáhnout nejlepšího výkonu s nízkou latencí a suverenitou dat.


:::image type="content" source="media/compare-windows-server/hci-scenarios.png" alt-text="Kdy použít Azure Stack HCI přes Windows Server 2019" border="false" lightbox="media/compare-windows-server/hci-scenarios.png":::


Použít Azure Stack HCL pro:

- Nejlepší Hostitel virtualizace k modernizovat vaší infrastruktury, a to buď pro existující úlohy ve vašem základním datovém centru, nebo na nově vznikající požadavky na pobočky a hraniční umístění
- Snadná rozšiřitelnost cloudu s pravidelným proudem inovací z předplatného Azure a konzistentní sadou nástrojů a prostředí
- Všechny výhody předem sblížené infrastruktury: jednodušší a pružnější architektura Datacenter s vysokou rychlostí úložiště a sítě

  >[!NOTE]
  >Vzhledem k tomu, že Azure Stack HCI je určeno pro použití jako hostitel virtualizace Hyper-V pro moderní, prosblíženou architekturu, nezahrnuje práva hostů. Z tohoto důvodu je Azure Stack HCL licencovaný jenom na spouštění malého počtu rolí serveru. všechny ostatní role musí běžet v rámci virtuálních počítačů.

## <a name="when-to-use-windows-server-2019"></a>Kdy použít Windows Server 2019

Windows Server 2019 je vysoce univerzálním a víceúčelovým operačním systémem s spoustou rolí a stovek funkcí, včetně oprávnění hostů. Počítače s Windows serverem můžou být v cloudu i v místním prostředí, včetně virtualizovaného rozhraní Azure Stack HCL.


:::image type="content" source="media/compare-windows-server/windows-server-scenarios.png" alt-text="Kdy použít Windows Server 2019 přes Azure Stack HCL" border="false" lightbox="media/compare-windows-server/windows-server-scenarios.png":::


Použijte Windows Server 2019 pro:

- Hostovaný operační systém uvnitř virtuálních počítačů (VM) nebo kontejnerů
- Jako modul runtime pro aplikaci pro Windows
- Použití jedné nebo více předdefinovaných rolí serveru, jako je služba Active Directory, souborové služby, DNS, DHCP nebo Internetová informační služba (IIS)
- Jako tradiční Server, jako je holý řadič domény nebo instalace SQL Server
- Pro tradiční infrastrukturu, jako jsou virtuální počítače připojené k technologie Fibre Channel úložiště SAN

## <a name="compare-product-positioning"></a>Porovnat umístění produktu

V následující tabulce je uvedeno balení produktu na nejvyšší úrovni pro Azure Stack HCI a Windows Server 2019.

| **Atribut**    | **Azure Stack HCI** | **Windows Server 2019** |
| ---------------- | ------------------- | ----------------------- |
| Typ produktu     | Cloudová služba, která zahrnuje operační systém a další | Operační systém |
| Právní informace            | Zahrnuté v rámci smlouvy o zákaznících Microsoftu nebo online předplatném | Má vlastní licenční smlouvu s koncovým uživatelem |
| Licensing        | Účtuje se do předplatného Azure. | Má svou vlastní placená licenci |
| Podpora          | Zahrnuto v části Podpora Azure | Můžou se vztahovat na různé smlouvy o podpoře, včetně Microsoft Premier Support |
| Kde je lze získat  | Stažení z [Azure.com/HCI](https://azure.com/hci)nebo předinstalováno v integrovaných systémech | Microsoft Volume Licensing Service Center nebo zkušební centrum |
| Spouští se na virtuálních počítačích      | Pouze pro vyhodnocení; určeno jako hostitelský operační systém | Ano, v cloudu nebo místně |
| Hardware         | Spouští se na jakémkoli z více než 200 předběžně ověřených řešení z [katalogu Azure Stack HCI](https://hcicatalog.azurewebsites.net) . | Spouští se na jakémkoli hardwaru s logem Certified for Windows Server 2019. |
| Zásady životního cyklu | Vždy aktuální s nejnovějšími funkcemi | Volba mezi [kanály pro údržbu Windows serveru](/windows-server/get-started-19/servicing-channels-19): kanál pro údržbu Long-Term (LTSC) a kanál Semi-Annual (SAC) |

## <a name="compare-technical-features"></a>Porovnání technických funkcí

V následující tabulce jsou porovnávány technické funkce Azure Stack HCI a Windows Server 2019.

| **Atribut** | **Azure Stack HCI** | **Windows Server 2019** |
| ------------- | ------------------- | ----------------------- |
| Základní Hyper-V | Ano | Ano |
| Základní Prostory úložiště s přímým přístupem | Ano | Ano |
| Základní SDN | Ano | Ano |
| Roztažené clustery pro zotavení po havárii | Ano | - |
| 4. pětinásobné rychlejší opravy prostorů úložiště | Ano | - |
| Integrované aktualizace ovladačů a firmwaru | Ano (jenom integrované systémy) | - |
| Průvodce nasazením | Ano | - |

## <a name="compare-management-options"></a>Porovnání možností správy

Následující tabulka porovnává možnosti správy pro Azure Stack HCI a Windows Server 2019. Oba produkty jsou navržené pro vzdálenou správu a dají se spravovat pomocí mnoha stejných nástrojů.

| **Atribut** | **Azure Stack HCI** | **Windows Server 2019** |
| ------------- | ------------------- | ----------------------- |
| Možnosti práce s počítačem | - | Ano |
| Centrum pro správu systému Windows | Ano | Ano |
| Microsoft System Center 2012 DPM | Ano (prodejně samostatně) | Ano (prodejně samostatně) |
| portál Azure | Ano (nativně) | Vyžaduje agenta ARC |
| Nástroje třetích stran | Ano | Ano |

## <a name="compare-product-pricing"></a>Porovnat ceny produktů

Následující tabulka porovnává ceny produktů Azure Stack HCI a Windows Server 2019. Podrobnosti najdete v tématu [Azure Stack ceny HCI](https://azure.microsoft.com/pricing/details/azure-stack/hci/).

| **Atribut** | **Azure Stack HCI** | **Windows Server 2019** |
| ------------- | ------------------- | ----------------------- |
| Typ ceny | Služba předplatného | Lišící se: nejčastěji jednorázová licence |
| Cenová struktura | Za jádro, za měsíc | Lišící se: obvykle na jádro |
| Cena | $10 USD za jádro za měsíc | Podívejte [se na ceny a licence pro Windows Server 2019](https://www.microsoft.com/windows-server/pricing) . |
| Zkušební/zkušební období | 30denní bezplatná zkušební verze po registraci | zkušební kopie 180 dne |
| Kanály | Smlouva Enterprise, poskytovatel cloudových služeb nebo přímá | Smlouva Enterprise/multilicenční smlouva, OEM, License Agreement pro poskytovatele služeb (SPLA) |

## <a name="next-steps"></a>Další kroky

- [Porovnat Azure Stack HCI s Azure Stack hub](compare-azure-stack-hub.md)
