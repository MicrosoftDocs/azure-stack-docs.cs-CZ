---
title: Azure Stack plánování kapacity úložiště centra
description: Přečtěte si o plánování kapacity úložiště pro Azure Stack nasazení centra.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: d7776f07745a2f76bc447df3f29a54578624aca6
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "78935038"
---
# <a name="azure-stack-hub-storage-capacity-planning"></a>Azure Stack plánování kapacity úložiště centra

V následujících částech najdete Azure Stack informací pro plánování kapacity úložiště centra, které vám pomůžou při plánování požadavků na úložiště řešení.

## <a name="uses-and-organization-of-storage-capacity"></a>Využití a organizace kapacity úložiště

Nakonvergovaná konfigurace centra Azure Stack umožňuje sdílení fyzických úložných zařízení. Existují tři hlavní divize dostupného úložiště, které lze sdílet: infrastruktura, dočasné úložiště virtuálních počítačů klientů a úložiště, které zálohuje objekty blob, tabulky a fronty služby ACS (Azure konzistentní úložiště).

## <a name="storage-spaces-direct-cache-and-capacity-tiers"></a>Prostory úložiště s přímým přístupem mezipaměti a úrovně kapacity

Kapacita úložiště se používá pro operační systém, místní protokolování, výpisy paměti a další požadavky na dočasné úložiště infrastruktury. Tato kapacita místního úložiště je oddělená (zařízení a kapacita) od úložných zařízení, která jsou v souladu se správou konfigurace Prostory úložiště s přímým přístupem. Ostatní úložná zařízení se nacházejí v jednom fondu kapacity úložiště, bez ohledu na počet serverů v jednotce škálování.

Tato zařízení mají dva typy: mezipaměť a kapacita. Prostory úložiště s přímým přístupem spotřebovává mezipaměť pro zpětný zápis a čtení do mezipaměti. Kapacity těchto zařízení mezipaměti se při použití neukládají do formátované a "viditelné" kapacity formátovaných virtuálních disků. Naproti tomu Prostory úložiště s přímým přístupem pro tento účel použít kapacitní zařízení, které poskytuje "umístění" na místě pro spravovaná data.

Infrastruktura centra Azure Stack přímo přiděluje a spravuje veškerou kapacitu úložiště. Operátor nemusí dělat volby týkající se konfigurace, přidělení a rozšíření kapacity. Centrum Azure Stack automatizuje tato rozhodnutí při návrhu v souladu s požadavky na řešení během počáteční instalace a nasazení nebo rozšíření kapacity. Azure Stack rozbočovač bere v úvahu odolnost, rezervovanou kapacitu pro opětovné sestavení a další podrobnosti jako součást návrhu.

Operátory si můžou vybrat mezi všemi konfiguracemi *Flash* nebo *hybridního* úložiště:

![Diagram plánování kapacity úložiště Azure](media/azure-stack-capacity-planning/storage.png)

V konfiguraci všech aplikací Flash může být konfigurace buď dvě vrstva, nebo jedna vrstva. Pokud je konfigurací jedna vrstva, všechna úložná zařízení mají stejný typ (například *NVMe* nebo *SATA SSD* nebo *SAS SSD*) a zařízení mezipaměti se nepoužívají. V konfiguraci se dvěma vrstvami je typická konfigurace NVMe jako zařízení mezipaměti a pak jako úložná zařízení SSD SATA nebo SAS.

V hybridní konfiguraci se dvěma vrstvami se mezipaměť volí mezi NVMe, SATA nebo SAS SSD a kapacita je hard.

Stručný přehled konfigurace úložiště Prostory úložiště s přímým přístupem a centra Azure Stack je následující:
- Jeden Prostory úložiště s přímým přístupem fond na jednotku škálování (všechna zařízení úložiště jsou nakonfigurovaná v rámci jednoho fondu).
- Virtuální disky se vytvoří jako tři kopie zrcadlového zrcadlu pro zajištění nejlepšího výkonu a odolnosti.
- Každý virtuální disk je naformátován jako systém souborů ReFS.
- Kapacita virtuálního disku se počítá a přiděluje způsobem, aby bylo možné ve fondu opustit množství nepřidělené kapacity dat kapacity zařízení. Toto je ekvivalent jedné jednotky kapacity na server.
- Každý systém souborů ReFS má povolený nástroj BitLocker pro šifrování při neaktivním umístění dat. 

Virtuální disky vytvořené automaticky a jejich kapacita jsou následující:

|Název|Výpočet kapacity|Popis|
|-----|-----|-----|
|Místní/spouštěcí zařízení|Minimálně 340 GB<sup>1</sup>|Samostatné serverové úložiště pro image operačního systému a virtuální počítače infrastruktury "místní".|
|Infrastruktura|3,5 TB|Všechna využití infrastruktury centra Azure Stack.|
|VmTemp|Viz níže<sup>2</sup> .|Virtuální počítače klienta mají připojen dočasný disk a tato data jsou uložena na těchto virtuálních discích.|
|ACS|Viz níže <sup>3</sup>|Kapacita úložiště konzistentní s Azure pro obsluhu objektů blob, tabulek a front.|

<sup>1</sup> minimální úložná kapacita požadovaná partnerem řešení Azure Stack hub.

<sup>2</sup> velikost virtuálního disku, která se používá pro dočasné disky virtuálního počítače klienta, se počítá jako poměr fyzické paměti serveru. Dočasný disk je poměr fyzické paměti přiřazené k virtuálnímu počítači. Přidělení pro ukládání "dočasného disku" v centru Azure Stack zachycuje většinu případů použití, ale nemusí být schopné splnit všechny požadavky na dočasné úložiště disku. Poměr je kompromis mezi tím, že je dočasné úložiště dostupné a nespotřebovává většinu kapacity úložiště řešení jenom pro kapacitu dočasného disku. V jednotce škálování se vytvoří jeden disk s dočasným úložištěm na serveru. Kapacita dočasného úložiště nepřesáhne 10 procent celkové dostupné kapacity úložiště ve fondu úložiště jednotky škálování. Výpočet je podobný jako v následujícím příkladu:

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> virtuální disky vytvořené pro použití službou ACS jsou jednoduchou oblastí zbývající kapacity. Jak je uvedeno, všechny virtuální disky jsou třícestným zrcadlem a kapacita jedné kapacity jednotky kapacity pro každý server je nepřidělená. Napřed se přidělují různé virtuální disky, které se dřív vytvořily, a zbývající kapacita se pak použije pro virtuální disky ACS.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [Capacity Planner centra Azure Stack](azure-stack-capacity-planner.md).
