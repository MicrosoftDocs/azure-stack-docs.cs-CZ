---
title: Správa clusterů Azure Stack HCI – centrum pro správu Windows
description: Naučte se spravovat clustery na Azure Stack HCI pomocí centra pro správu Windows.
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 01/22/2021
ms.openlocfilehash: 4b6e7537a1111ed2e38d1783acf7197a4249b2ac
ms.sourcegitcommit: e772df8ac78c86d834a68d1a8be83b7f738019b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98772107"
---
# <a name="manage-azure-stack-hci-clusters-using-windows-admin-center"></a>Správa clusterů Azure Stack HCI pomocí centra pro správu Windows

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

Centrum pro správu systému Windows lze použít ke správě Azure Stack clusterů HCI. Konkrétně použijete funkci Správce clusterů v centru pro správu systému Windows ke správě svých clusterů.

## <a name="view-the-cluster-dashboard"></a>Zobrazení řídicího panelu clusteru

Řídicí panel clusteru zobrazuje informace týkající se stavu a výkonu clusteru.

:::image type="content" source="media/manage-cluster/cluster-dashboard.png" alt-text="Obrazovka řídicího panelu clusteru" lightbox="media/manage-cluster/cluster-dashboard.png":::

Pokud si chcete zobrazit tyto informace, vyberte název clusteru v části **všechna připojení** a potom v části **nástroje** na levé straně vyberte **řídicí panel**. Můžete zobrazit následující:

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

## <a name="change-storage-settings"></a>Změnit nastavení úložiště

Můžete vybrat možnost použití paměti serveru pro ukládání častých čtení do mezipaměti a zadat maximální velikost paměti, která se má použít na server. Další informace najdete v tématu [Principy mezipaměti v Azure Stack HCI](../concepts/cache.md).

1. V centru pro správu systému Windows vyberte ze šipky nahoru v horním rozevíracím seznamu položku **Správce clusteru** .
1. V části **nástroje** vyberte **Nastavení** v dolní části.
1. Vyberte **mezipaměť v paměti** a zadejte nový název.

    :::image type="content" source="media/manage-cluster/cluster-settings-memory.png" alt-text="obrazovka mezipaměti v paměti clusteru" lightbox="media/manage-cluster/cluster-settings-memory.png":::

1. Název fondu úložiště, který Prostory úložiště s přímým přístupem používá, můžete změnit. Vyberte **fondy úložišť** a zadejte nový název. Tato funkce se vztahuje na roztažené clustery.

    :::image type="content" source="media/manage-cluster/cluster-settings-storage-pools.png" alt-text="obrazovka fondu úložiště clusteru" lightbox="media/manage-cluster/cluster-settings-storage-pools.png":::

1. Můžete změnit nastavení Prostory úložiště s přímým přístupem. Vyberte **prostory úložiště s přímým přístupem** a podle potřeby změňte následující nastavení:

    - **Trvalá mezipaměť** – povolí nebo zakáže trvalou mezipaměť.
    - **Režim mezipaměti pro pevný disk** – změna režimu mezipaměti pro jednotky HDD
    - **Režim mezipaměti pro SSD** – Změna mezipaměti pro jednotky SSD

    :::image type="content" source="media/manage-cluster/cluster-settings-storage-spaces-direct.png" alt-text="obrazovka Prostory úložiště s přímým přístupem clusteru" lightbox="media/manage-cluster/cluster-settings-storage-spaces-direct.png":::

## <a name="change-cluster-settings"></a>Změnit nastavení clusteru

Pro cluster se dá použít několik obecných nastavení. Tady je místo, kde můžete nastavit a spravovat přístupové body, chování při vypnutí uzlu, šifrování provozu, Vyrovnávání zatížení virtuálních počítačů a určující cluster.

1. V centru pro správu systému Windows vyberte ze šipky nahoru v horním rozevíracím seznamu položku **Správce clusteru** .
1. V části **nástroje** vyberte **Nastavení**.
1. Chcete-li změnit název clusteru, vyberte **přístupový bod** a zadejte nový název.

    :::image type="content" source="media/manage-cluster/cluster-settings-access.png" alt-text="Scénář aktivního/aktivního roztaženého clusteru" lightbox="media/manage-cluster/cluster-settings-access.png":::

1. Chcete-li řídit chování uzlu při vypnutí, vyberte možnost **chování při vypnutí uzlu** a ujistěte se, že je zaškrtávací políčko povoleno. Tím se nejprve přesune všechny virtuální počítače z uzlu, aby bylo možné bezproblémové vypnutí uzlu.

    :::image type="content" source="media/manage-cluster/cluster-settings-shutdown.png" alt-text="obrazovka chování při vypnutí uzlu clusteru" lightbox="media/manage-cluster/cluster-settings-shutdown.png":::

1. Pokud chcete zašifrovat připojení SMB používaná k posílání dat mezi uzly clusteru, vyberte **šifrování provozu clusteru** a pak v rozevíracích seznamech vyberte **Šifrovat** v následujících polích:

   - **Základní provoz** – šifruje provoz odeslaný přes NetFT (virtuální adaptér clusteru) na portu 3343.

   - **Provoz úložiště** – šifruje sdílený svazek clusteru (CSV) a přenosová vrstva sběrnice (SBL)

        :::image type="content" source="media/manage-cluster/cluster-settings-encryption.png" alt-text="obrazovka šifrování přenosu clusterových clusterů" lightbox="media/manage-cluster/cluster-settings-encryption.png":::

1. Chcete-li automaticky vyrovnávat zatížení virtuálních počítačů v rámci clusteru, vyberte možnost **Vyrovnávání zatížení virtuálního počítače** a proveďte následující kroky:

   - Pro **Vyrovnávání zatížení virtuálních počítačů** vyberte příslušnou akci.
   - V případě **agresivní** vyberte příslušné chování.

     Informace o tom, jak to funguje, najdete v tématu [Přehled vyrovnávání zatížení virtuálních počítačů](/windows-server/failover-clustering/vm-load-balancing-overview).

        :::image type="content" source="media/manage-cluster/cluster-settings-vm-load.png" alt-text="obrazovka pro vyrovnávání zatížení virtuálního počítače clusteru" lightbox="media/manage-cluster/cluster-settings-vm-load.png":::

1. Chcete-li vybrat typ určujícího disku kvora, vyberte možnost **určující disk** a pak jako **typ určujícího disku** vyberte jednu z následujících možností:

   - **Disk s kopií cloudu** – použití cloudového prostředku Azure jako určujícího disku
   - **Disk s kopií clusteru** – pro použití prostředku disku jako určujícího disku (nepoužívá se pro roztažené clustery)
   - **Určující sdílená složka** – pro použití sdílené složky jako určujícího umístění

        Podrobné informace o tom, jak nastavit určující disk, najdete v tématu [Nastavení určujícího clusteru](witness.md). Viz také [Principy kvora clusteru a fondu v Azure Stack HCI](../concepts/quorum.md).

        :::image type="content" source="media/manage-cluster/cluster-settings-witness.png" alt-text="Sdílená obrazovka clusteru" lightbox="media/manage-cluster/cluster-settings-witness.png":::

1. Pokud chcete použít pravidla spřažení k řízení umístění virtuálních počítačů mezi hostitelskými servery a lokalitami, vyberte **pravidla spřažení** a pak klikněte na **vytvořit pravidlo**. Podrobné informace o tom, jak nastavit pravidla, najdete v tématu [Vytvoření pravidel spřažení serveru a lokality pro virtuální počítače](vm-affinity.md).

    :::image type="content" source="media/manage-cluster/affinity-rules.png" alt-text="obrazovka pravidla spřažení clusteru" lightbox="media/manage-cluster/affinity-rules.png":::

1. Pokud chcete vybrat, kolik dat se má poslat Microsoftu pro diagnostiku, vyberte **diagnostická data** a pak vyberte jednu z následujících možností:

    - **Vypnutí diagnostických dat (zabezpečení)** – neodesílají se žádná data.
    - **Požadováno (základní)** – minimální počet odeslaných dat, aby se zajistila bezpečnost a aktuálnost
    - **Volitelné (úplné)** – všechna příslušná data byla odeslána.

    :::image type="content" source="media/manage-cluster/cluster-diagnostic-data.png" alt-text="obrazovka diagnostiky dat clusteru" lightbox="media/manage-cluster/cluster-diagnostic-data.png":::

## <a name="change-hyper-v-settings"></a>Změnit nastavení technologie Hyper-V

Existuje několik nastavení hostitele technologie Hyper-V, které lze použít pro váš cluster.

1. V centru pro správu systému Windows vyberte ze šipky nahoru v horním rozevíracím seznamu položku **Správce clusteru** .
1. V části **nástroje** vyberte **Nastavení**.
1. Vyberte **Obecné** a pak použijte následující nastavení:

   - **Cesta k virtuálním pevným** diskům – zadejte výchozí složku pro ukládání souborů virtuálního pevného disku.

   - **Cesta Virtual Machines** – zadejte výchozí složku pro ukládání konfiguračních souborů virtuálního počítače.

        :::image type="content" source="media/manage-cluster/cluster-settings-hyperv.png" alt-text="obrazovka Obecné nastavení clusteru Hyper-V" lightbox="media/manage-cluster/cluster-settings-hyperv.png":::

1. Pokud chcete zakázat přesměrování místních zařízení a prostředků z virtuálních počítačů, vyberte **režim rozšířené relace**. Všimněte si, že připojení režimu rozšířené relace vyžadují podporovaný hostovaný operační systém.

    :::image type="content" source="media/manage-cluster/cluster-settings-session.png" alt-text="obrazovka režimu rozšířené relace technologie Hyper-V clusteru" lightbox="media/manage-cluster/cluster-settings-session.png":::

1. Pokud chcete virtuálním počítačům přidělit Rozsah fyzických uzlů NUMA, vyberte pokrývání uzlů **NUMA**. Neuniformní architektura paměti (NUMA) může virtuálnímu počítači poskytnout více paměti, než kolik je k dispozici na jednom uzlu NUMA.

    :::image type="content" source="media/manage-cluster/cluster-settings-numa.png" alt-text="Pokrývání obrazovky NUMA clusteru" lightbox="media/manage-cluster/cluster-settings-numa.png":::

1. Pokud chcete určit počet virtuálních počítačů, které se dají při spuštění (za provozu) přesunout současně, vyberte **migrace za provozu**, vyberte číslo a pak zadejte následující:

   - v případě **ověřovacího protokolu** vyberte buď **CredSSP** , nebo **Kerberos**.

   - v případě **možností výkonu** vyberte možnost **Komprese** nebo **SMB**. Komprimovaná data se odesílají přes připojení TCP/IP.

   - Zaškrtnutím políčka **použít libovolné sítě** můžete použít jakoukoli dostupnou síť v uzlu k provedení migrace.

        :::image type="content" source="media/manage-cluster/cluster-settings-live-migration.png" alt-text="obrazovka Migrace za provozu clusteru" lightbox="media/manage-cluster/cluster-settings-live-migration.png":::

1. Chcete-li určit počet migrací úložiště, které lze provést současně, vyberte možnost **migrace úložiště** a pak vyberte číslo.

    :::image type="content" source="media/manage-cluster/cluster-settings-storage-migration.png" alt-text="obrazovka migrace úložiště clusteru" lightbox="media/manage-cluster/cluster-settings-storage-migration.png":::

## <a name="register-the-cluster-with-azure"></a>Registrace clusteru v Azure

Pokud chcete zaregistrovat nebo zrušit registraci clusteru v Azure, vyberte **Azure Stack registraci HCI**. Podrobné informace o tom, jak to udělat, najdete v tématu [připojení Azure Stack HCL do Azure](../deploy/register-with-azure.md).

:::image type="content" source="media/manage-cluster/cluster-registration.png" alt-text="obrazovka registrace clusteru Azure" lightbox="media/manage-cluster/cluster-registration.png":::

## <a name="next-steps"></a>Další kroky

Pokud chcete monitorovat svůj cluster, přečtěte si téma [monitorování Azure Stack HCL pomocí Azure monitor](azure-monitor.md).