---
title: Použít mezipaměť pro čtení v paměti sdíleného svazku clusteru s Azure Stack HCL
description: Toto téma popisuje, jak pomocí systémové paměti zvýšit výkon.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/04/2020
ms.openlocfilehash: 84d5292c3f812402027b310954a021752276a799
ms.sourcegitcommit: 01dcda15d88c8d44b4918e2f599daca462a8e3d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2020
ms.locfileid: "89493797"
---
# <a name="use-the-csv-in-memory-read-cache-with-azure-stack-hci"></a>Použít mezipaměť pro čtení v paměti sdíleného svazku clusteru s Azure Stack HCL

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

Toto téma popisuje, jak pomocí systémové paměti zvýšit výkon Azure Stack HCI.

Azure Stack HCI je kompatibilní s mezipamětí pro čtení sdílený svazek clusteru (CSV). Použití systémové paměti pro čtení do mezipaměti může zlepšit výkon aplikací, jako je technologie Hyper-V, které pro přístup k souborům VHD nebo VHDX používá nebufferované vstupně-výstupní operace. (Vstupně-výstupních operací s neuloženými do vyrovnávací paměti jsou všechny operace, které správce mezipaměti systému Windows neukládá do mezipaměti.)

Vzhledem k tomu, že mezipaměť v paměti je místní server, vylepšuje velikost dat: poslední čtení se ukládají do mezipaměti na stejném hostiteli, kde je virtuální počítač spuštěný, a snižuje tak, jak často čte přes síť. Výsledkem je nižší latence a lepší výkon úložiště.

Všimněte si, že mezipaměť pro čtení v paměti sdíleného svazku clusteru se liší od [mezipaměti fondu úložiště](../concepts/cache.md) v Azure Stack HCI.

## <a name="planning-considerations"></a>Aspekty plánování

Mezipaměť pro čtení v paměti je nejúčinnější pro úlohy náročné na čtení, jako je například Infrastruktura virtuálních klientských počítačů (VDI). Naopak, pokud je zatížení extrémně náročné na zápis, může mezipaměť způsobit větší režii než hodnota a měla by být zakázána.

Pro mezipaměť pro čtení v paměti sdíleného svazku clusteru můžete použít až 80% z celkové fyzické paměti. Buďte opatrní, abyste ponechali dostatek paměti pro virtuální počítače.

  > [!NOTE]
  > Některé nástroje mikrosrovnávacích testů, jako je DISKSPD a [flotila virtuálních počítačů](https://github.com/Microsoft/diskspd/tree/master/Frameworks/VMFleet) , můžou způsobit horší výsledky s povolenou mezipamětí pro čtení v paměti, než bez ní. Ve výchozím nastavení vytvoří loďstva virtuálního počítače 1 10 GiB VHDX na virtuální počítač – přibližně 1 TiB celkem pro virtuální počítače 100 – a pak do nich provede *jednotná náhodné* čtení a zápisy. Na rozdíl od reálných úloh čtení nedodržuje žádné předvídatelné ani opakující se vzor, takže mezipaměť v paměti není efektivní a právě se nejedná o režii.

## <a name="configuring-the-in-memory-read-cache"></a>Konfigurace mezipaměti pro čtení v paměti

Mezipaměť pro čtení v paměti sdíleného svazku clusteru je k dispozici v Azure Stack HCL, Windows Server 2019 a Windows Server 2016 se stejnou funkcí. V Azure Stack HCI a Windows Server 2019 je ve výchozím nastavení zapnuté s přidělenou hodnotou 1 gibibajt (GiB). Ve Windows serveru 2016 je ve výchozím nastavení vypnutá.

| Verze operačního systému          | Výchozí velikost mezipaměti sdílených svazků clusteru |
|---------------------|------------------------|
| Azure Stack HCI     | 1 GiB                  |
| Windows Server 2019 | 1 GiB                  |
| Windows Server 2016 | 0 (zakázáno)           |

Chcete-li zjistit, kolik paměti je přiděleno pomocí prostředí PowerShell, spusťte příkaz:

```PowerShell
(Get-Cluster).BlockCacheSize
```

Vrácená hodnota je v mebibytes (MiB) na server. Například `1024` reprezentuje 1 GiB.

Chcete-li změnit množství přidělené paměti, upravte tuto hodnotu pomocí prostředí PowerShell. Pokud třeba chcete přidělit 2 GiB na server, spusťte:

```PowerShell
(Get-Cluster).BlockCacheSize = 2048
```

Změny se projeví okamžitě tak, že pozastavíte a obnovíte svazky sdíleného svazku clusteru nebo je přesunete mezi servery. Pomocí tohoto fragmentu PowerShellu můžete například přesunout každý sdílený svazek clusteru do jiného uzlu serveru a znovu se vrátit:

```PowerShell
Get-ClusterSharedVolume | ForEach {
    $Owner = $_.OwnerNode
    $_ | Move-ClusterSharedVolume
    $_ | Move-ClusterSharedVolume -Node $Owner
}
```

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Pochopení mezipaměti fondu úložiště](../concepts/cache.md)
- [Použití sdílených svazků clusteru v clusteru s podporou převzetí služeb při selhání](/windows-server/failover-clustering/failover-cluster-csvs#enable-the-csv-cache-for-read-intensive-workloads-optional)
