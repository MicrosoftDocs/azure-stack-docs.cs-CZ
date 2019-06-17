---
title: Plánování úložiště kapacity Azure Stack | Dokumentace Microsoftu
description: Další informace o plánování kapacity pro nasazení Azure Stack.
services: azure-stack
documentationcenter: ''
author: prchint
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 2845a90f97c1b859269f73333448bf42ff699da9
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131087"
---
# <a name="azure-stack-storage"></a>Úložiště Azure Stack

Azure Stack úložiště plánování kapacity informace jako pomoc při plánování tohoto řešení se úložiště udržet pod naleznete v následujících částech.

## <a name="uses-and-organization-of-storage-capacity"></a>Použití a organizace kapacita úložiště
Konfigurace hyperkonvergovaného služby Azure Stack umožňuje sdílení fyzickými úložnými zařízeními. Existují tři hlavní rozdělení na dostupné úložiště, které je možné sdílet: infrastrukturu, dočasné úložiště tenantské virtuální počítače a úložiště, zálohování, objekty BLOB, tabulky a fronty služby konzistentní úložiště Azure (ACS).

## <a name="storage-spaces-direct-cache-and-capacity-tiers"></a>Prostorů s přímým přístupem mezipaměti a kapacitu vrstvy úložiště
Je kapacita úložiště používá pro operační systém, místní protokolování, výpisů paměti a jiné dočasné infrastruktury úložiště potřebám. Tato kapacita místní úložiště je nezávislá na infrastruktuře (zařízení a kapacita) úložných zařízení, převedené pod správu konfigurace prostorů úložiště s přímým. Zbývající část úložných zařízení nachází v jeden fond úložiště kapacitu, bez ohledu na počet serverů v jednotce škálování.

Tato zařízení jsou dvou typů: mezipaměti a kapacity. Prostory úložiště – přímé využívá pro ukládání do mezipaměti se zpětným zápisem a další zařízení. Kapacity těchto zařízení, i když je použít, nejsou potvrzeny formátovaný, "visible" kapacita formátovaný virtuálních disků. Naopak prostorů úložiště s přímým úložných zařízení pro tento účel použít, "domovské umístění" spravované data.

Infrastruktura Azure stacku přímo přiděluje a spravuje všechny kapacity úložiště. Operátor, který se nemusí rozhodovat o konfiguraci, přidělení, rozšiřování kapacity. Azure Stack automatizuje těchto rozhodnutí o návrhu, aby bylo v souladu s požadavky na řešení během počáteční instalace a nasazení nebo rozšiřování kapacity. Azure Stack bere v úvahu odolnost proti chybám, rezervované kapacity pro znovu sestaví a další podrobnosti, jako součást návrhu. 

Operátory můžete zvolit buď *všechny flash* nebo *hybridní* konfiguraci úložiště:

![Diagram plánování kapacity služby Azure storage](media/azure-stack-capacity-planning/storage.png)

V konfiguraci všech flash může být konfigurace dvě úrovně nebo Jednoúrovňová konfigurace. Pokud konfigurace je Jednoúrovňová, jsou všechny kapacitou zařízení stejného typu (například NVMe nebo SATA SSD nebo SAS SSD) a zařízení nepoužívají. Ve všech dvouvrstvé je NVMe flash konfigurace, Typická konfigurace jako mezipaměť zařízení a potom buď SATA nebo SAS SSD jako úložných zařízení.

V hybridním nasazení dvouvrstvé konfigurace mezipaměti je volba mezi NVMe nebo SATA, SAS SSD a kapacita je pevný disk. 

Stručný přehled prostorů úložiště s přímým a konfiguraci úložiště služby Azure Stack je následujícím způsobem:
- Jeden fond prostory úložiště – přímé za škálovací jednotku (všechna zařízení úložiště jsou nakonfigurované v rámci jeden fond).
- Virtuální disky se vytvoří jako tři kopie zrcadlení pro nejvyšší výkon a odolnost proti chybám.
- Každý virtuální disk je formátován jako systém souborů ReFS.
- Kapacita virtuálního disku je počítá a přidělených způsobem, ponechat jeden kapacity zařízení množství datové kapacity volné ve fondu. Jedná se o ekvivalent jednu jednotku kapacity na server.
- Každý systém souborů ReFS má nástroj BitLocker povoleno šifrování dat v klidovém stavu. 

Virtuální disky automaticky vytvořen a jejich kapacity jsou následující:

|Name|Výpočet kapacity|Popis|
|-----|-----|-----|
|Místní/spouštěcí zařízení|Minimální 340 GB<sup>1</sup>|Úložiště jednotlivých serverů pro bitové kopie operačního systému a "local" infrastrukturu virtuálních počítačů.|
|Infrastruktura|3,5 TB|Veškeré využití infrastruktury Azure stacku.|
|VmTemp|Viz níže<sup>2</sup>|Tenantské virtuální počítače mají dočasný disk připojený, a tato data uložená v těchto virtuálních disků.|
|ACS|Viz níže <sup>3</sup>|Azure konzistentní kapacitou pro obsluhu objekty BLOB, tabulky a fronty.|

<sup>1</sup> minimální kapacitu úložiště vyžaduje partnera řešení Azure Stack.

<sup>2</sup> velikost virtuálního disku použité pro dočasné disky virtuálního počítače tenanta se počítá jako poměr fyzické paměti serveru. Dočasný disk je poměr fyzické paměti přidělené virtuálnímu počítači. Přidělení Hotovo "temp disku" úložiště ve službě Azure Stack zaznamená většinu případů použití, ale nemusí splňovat všechny požadavky na úložiště dočasného disku. Poměr je kompromis mezi zpřístupnění dočasné úložiště a ne spotřebovává většinu úložnou kapacitu řešení pro dočasné diskové kapacity pouze. Jeden server v jednotce škálování se vytvoří jeden disk dočasného úložiště. Kapacita dočasného úložiště není nárůst 10 procent celkové dostupné kapacity úložiště ve fondu úložiště jednotce škálování. Výpočet je něco jako v následujícím příkladu:

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> virtuální disky vytvořené pro použití službou ACS jsou jednoduché dělení zbývající kapacity. Jak je uvedeno, všechny virtuální disky jsou třícestný zrcadlový svazek a za jednu kapacitu jednotky kapacity pro každý server je volné. Různé dříve uvedené virtuální disky se přiděluje první a zbývající kapacity se pak použije virtuálních disků služby ACS.


## <a name="next-steps"></a>Další postup
Další informace o [Capacity Planner služby Azure Stack](azure-stack-capacity-planner.md).
