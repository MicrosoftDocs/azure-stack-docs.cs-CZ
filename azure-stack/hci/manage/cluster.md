---
title: Správa clusterů Azure Stack HCI pomocí centra pro správu Windows
description: Naučte se spravovat clustery na Azure Stack HCI pomocí centra pro správu Windows.
ms.topic: article
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 05/20/2020
ms.openlocfilehash: 55ce577cbedde36d271ab6fc13234b009c825d7e
ms.sourcegitcommit: 7c10a45a8de0c5c7649e5329ca5b69a0791e37b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83729278"
---
# <a name="manage-azure-stack-hci-clusters-using-windows-admin-center"></a>Správa clusterů Azure Stack HCI pomocí centra pro správu Windows

> Platí pro: Windows Server 2019

Centrum pro správu Windows se dá použít ke správě clusterů v Azure Stack HCI. Konkrétně použijete rozšíření Správce clusterů v centru pro správu systému Windows ke správě clusterů.

## <a name="view-the-cluster-dashboard"></a>Zobrazení řídicího panelu clusteru

Řídicí panel clusteru zobrazuje informace týkající se stavu a výkonu clusteru.

:::image type="content" source="media/manage-cluster/cluster-dashboard.png" alt-text="Obrazovka řídicího panelu clusteru":::

Pokud si chcete zobrazit tyto informace, vyberte název clusteru v části **všechna připojení**a potom v části **nástroje** na levé straně vyberte **řídicí panel**. Můžete zobrazit následující:

- Výstrahy událostí clusteru
- Seznam serverů připojených ke clusteru
- Seznam virtuálních počítačů spuštěných v clusteru
- Seznam diskových jednotek dostupných v clusteru
- Seznam svazků dostupných v clusteru
- Celkové využití procesoru clusterů pro cluster
- Celkové využití paměti clusteru pro cluster
- Celkové využití úložiště clusteru pro cluster
- Celkový počet vstupně-výstupních operací clusteru za sekundu (IOPS)
- Průměrná latence clusteru v milisekundách

## <a name="change-cluster-general-settings"></a>Změna obecných nastavení clusteru

Pro cluster se dá použít pět obecných nastavení.

1. V centru pro správu systému Windows klikněte na **Správce clusterů** z horního rozevíracího seznamu šipky.
1. V části **nástroje**klikněte na **Nastavení**.
1. Chcete-li změnit název clusteru, vyberte **přístupový bod** a zadejte nový název.

    :::image type="content" source="media/manage-cluster/cluster-settings-access.png" alt-text="obrazovka přístupového bodu clusteru":::

1. Chcete-li řídit chování uzlu při vypnutí, vyberte možnost **chování při vypnutí uzlu** a ujistěte se, že je zaškrtávací políčko povoleno. Tím se nejprve přesune všechny virtuální počítače z uzlu, aby bylo možné bezproblémové vypnutí uzlu.

    :::image type="content" source="media/manage-cluster/cluster-settings-shutdown.png" alt-text="obrazovka chování při vypnutí uzlu clusteru":::

1. Pokud chcete zašifrovat připojení SMB používaná k posílání dat mezi uzly clusteru, vyberte **šifrování provozu clusteru**a pak v rozevíracích seznamech vyberte **Šifrovat** v následujících polích:

   - **Základní provoz** – šifruje provoz odeslaný přes NetFT (virtuální adaptér clusteru) na portu 3343.

   - **Přenosy serverů** – šifruje sdílený svazek clusteru (CSV) a přenosová vrstva sběrnice (SBL)

        :::image type="content" source="media/manage-cluster/cluster-settings-encryption.png" alt-text="obrazovka šifrování přenosu clusterových clusterů":::

1. Chcete-li automaticky vyrovnávat zatížení virtuálních počítačů v rámci clusteru, vyberte možnost **Vyrovnávání zatížení virtuálního počítače**a proveďte následující kroky:

   - Pro **Vyrovnávání zatížení virtuálních počítačů**vyberte příslušnou akci.
   - V případě **agresivní**vyberte příslušné chování.

     Informace o tom, jak to funguje, najdete v tématu [Přehled vyrovnávání zatížení virtuálních počítačů](https://docs.microsoft.com/windows-server/failover-clustering/vm-load-balancing-overview).

        :::image type="content" source="media/manage-cluster/cluster-settings-vm-load.png" alt-text="obrazovka pro vyrovnávání zatížení virtuálního počítače clusteru":::

1. Pokud chcete vybrat typ určujícího disku kvora, vyberte **určující disk**a pak vyberte jednu z následujících možností:

   - **Disk s kopií cloudu** – použití cloudového prostředku Azure jako určujícího disku
   - **Disk s kopií clusteru** – pro použití diskového prostředku jako určujícího disku
   - **Určující sdílená složka** – pro použití sdílené složky jako určujícího umístění

        Další informace najdete v tématu [Konfigurace a Správa kvora](https://docs.microsoft.com/windows-server/failover-clustering/manage-cluster-quorum).

        :::image type="content" source="media/manage-cluster/cluster-settings-witness.png" alt-text="Sdílená obrazovka clusteru":::

## <a name="change-cluster-hyper-v-settings"></a>Změna nastavení technologie Hyper-V clusteru

Existuje pět nastavení hostitele technologie Hyper-V, které lze použít pro váš cluster.

1. V centru pro správu systému Windows klikněte na **Správce clusterů** z horního rozevíracího seznamu šipky.
1. V části **nástroje**klikněte na **Nastavení**.
1. Vyberte **Obecné** a pak použijte následující nastavení:

   - **Cesta k virtuálním pevným** diskům – zadejte výchozí složku pro ukládání souborů virtuálního pevného disku.

   - **Cesta Virtual Machines** – zadejte výchozí složku pro ukládání konfiguračních souborů virtuálního počítače.

   - **Typ plánovače hypervisoru** – vyberte **základní Plánovač** nebo **klasický Plánovač**. Tím se určuje, jak hypervisor naplánuje spuštění virtuálních procesů na fyzických procesorech používajících simultánní vícevláknové zpracování (označované také jako SMT nebo Hyper-Threading).

        :::image type="content" source="media/manage-cluster/cluster-settings-hyperv.png" alt-text="obrazovka Obecné nastavení clusteru Hyper-V":::

1. Pokud chcete zakázat přesměrování místních zařízení a prostředků z virtuálních počítačů, vyberte **režim rozšířené relace**. Všimněte si, že připojení režimu rozšířené relace vyžadují podporovaný hostovaný operační systém.

    :::image type="content" source="media/manage-cluster/cluster-settings-session.png" alt-text="obrazovka režimu rozšířené relace technologie Hyper-V clusteru":::

1. Pokud chcete virtuálním počítačům přidělit Rozsah fyzických uzlů NUMA, vyberte pokrývání uzlů **NUMA**. Neuniformní architektura paměti (NUMA) může virtuálnímu počítači poskytnout více paměti, než kolik je k dispozici na jednom uzlu NUMA.

    :::image type="content" source="media/manage-cluster/cluster-settings-numa.png" alt-text="Pokrývání obrazovky NUMA clusteru":::

1. Pokud chcete určit počet virtuálních počítačů, které se dají při spuštění (za provozu) přesunout současně, vyberte **migrace za provozu**, vyberte číslo a pak zadejte následující:, vyberte **migrace za provozu**, vyberte číslo a pak zadejte následující:

   - v případě **ověřovacího protokolu**vyberte buď **CredSSP** , nebo **Kerberos**.

   - pro **možnost výkon**vyberte buď **kompresi** , nebo protokol **SMB**. Komprimovaná data se odesílají přes připojení TCP/IP.

   - Zaškrtnutím políčka **použít libovolné sítě** můžete použít jakoukoli dostupnou síť v uzlu k provedení migrace.

        :::image type="content" source="media/manage-cluster/cluster-settings-liv-migration.png" alt-text="obrazovka Migrace za provozu clusteru":::

1. Chcete-li určit počet migrací úložiště, které lze provést současně, vyberte možnost **migrace úložiště**a pak vyberte číslo.

    :::image type="content" source="media/manage-cluster/cluster-settings-sto-migration.png" alt-text="obrazovka migrace úložiště clusteru":::

## <a name="change-cluster-storage-settings"></a>Změnit nastavení úložiště clusteru

Existují dvě nastavení, která můžete změnit v souvislosti s Prostory úložiště s přímým přístupem, kterou je možné použít pro váš cluster.

1. V centru pro správu systému Windows klikněte na **Správce clusterů** z horního rozevíracího seznamu šipky.
1. V části **nástroje**klikněte na **Nastavení** v dolní části.
1. Mezipaměť úložiště nakonfigurujete tak, že vyberete **prostory úložiště s přímým přístupem**a pak nakonfigurujete následující:

   - v případě **trvalé mezipaměti**vyberte možnost **povoleno** nebo **zakázáno** .

   - pro **režim mezipaměti pro pevný disk**vyberte jen **pro čtení**, **jen pro zápis**nebo **čtení a zápis** .

   - pro **režim mezipaměti pro SSD**vyberte jen **pro čtení**, **jenom zápis**nebo **čtení a zápis** .

        :::image type="content" source="media/manage-cluster/cluster-settings-ssd.png" alt-text="obrazovka Prostory úložiště s přímým přístupem clusteru":::

1. Chcete-li použít paměť serveru pro ukládání častých čtení do mezipaměti, vyberte **mezipaměť v paměti** a zadejte maximální velikost paměti, která se má použít na server. Viz také [použití prostory úložiště s přímým přístupem s mezipamětí pro čtení v paměti CSV](https://docs.microsoft.com/windows-server/storage/storage-spaces/csv-cache).

    :::image type="content" source="media/manage-cluster/cluster-settings-memory.png" alt-text="obrazovka mezipaměti v paměti clusteru":::

## <a name="next-steps"></a>Další kroky

- Informace o monitorování výkonu clusteru najdete v tématu [monitorování výkonu clusteru](https://docs.microsoft.com/azure-stack/hci/get-started#monitor-cluster-performance-with-the-windows-admin-center-dashboard) .