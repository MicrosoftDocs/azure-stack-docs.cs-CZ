---
title: Správa clusterů Azure Stack HCI – centrum pro správu Windows
description: Naučte se spravovat clustery na Azure Stack HCI pomocí centra pro správu Windows.
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 01/11/2021
ms.openlocfilehash: a2c07e171468aad411bed1b752834939827be971
ms.sourcegitcommit: 1465bca8b7f87ea6f24faf47e86c2ba497943b28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98103188"
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

## <a name="view-cluster-resources"></a>Zobrazení prostředků clusteru

Na stránce Přehled clusteru se zobrazují informace o prostředcích clusteru, jako jsou servery, infrastruktura a určující disk kvora.

:::image type="content" source="media/manage-cluster/cluster-overview.png" alt-text="Obrazovka s přehledem clusteru" lightbox="media/manage-cluster/cluster-overview.png":::

Pokud si chcete zobrazit tyto informace, vyberte název clusteru v části **všechna připojení** a potom v části **nástroje** na levé straně vyberte **Přehled**.

## <a name="change-storage-settings"></a>Změnit nastavení úložiště

Můžete vybrat možnost použití paměti serveru pro ukládání častých čtení do mezipaměti a zadat maximální velikost paměti, která se má použít na server. Další informace najdete v tématu [Principy mezipaměti v Azure Stack HCI](../concepts/cache.md).

1. V centru pro správu systému Windows vyberte ze šipky nahoru v horním rozevíracím seznamu položku **Správce clusteru** .
1. V části **nástroje** vyberte **Nastavení** v dolní části.
1. Vyberte **mezipaměť v paměti** a zadejte nový název.

    :::image type="content" source="media/manage-cluster/cluster-settings-memory.png" alt-text="obrazovka mezipaměti v paměti clusteru" lightbox="media/manage-cluster/cluster-settings-memory.png":::

1. Název fondu úložiště, který Prostory úložiště s přímým přístupem používá, můžete změnit. Vyberte **fondy úložišť** a zadejte nový název.

    :::image type="content" source="media/manage-cluster/cluster-settings-ssd.png" alt-text="obrazovka fondu úložiště clusteru" lightbox="media/manage-cluster/cluster-settings-ssd.png":::

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

   - **Přenosy serverů** – šifruje sdílený svazek clusteru (CSV) a přenosová vrstva sběrnice (SBL)

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

        Podrobné informace o tom, jak nastavit určující disk, najdete v tématu [Nastavení určujícího clusteru](../deploy/witness.md). Viz také [Principy kvora clusteru a fondu v Azure Stack HCI](../concepts/quorum.md).

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

   - **Typ plánovače hypervisoru** – vyberte **základní Plánovač** nebo **klasický Plánovač**. Tím se určuje, jak hypervisor naplánuje spuštění virtuálních procesů na fyzických procesorech používajících simultánní vícevláknové zpracování (označované také jako SMT nebo Hyper-Threading). Doporučuje se základní plánování.

        :::image type="content" source="media/manage-cluster/cluster-settings-hyperv.png" alt-text="obrazovka Obecné nastavení clusteru Hyper-V" lightbox="media/manage-cluster/cluster-settings-hyperv.png":::

1. Pokud chcete zakázat přesměrování místních zařízení a prostředků z virtuálních počítačů, vyberte **režim rozšířené relace**. Všimněte si, že připojení režimu rozšířené relace vyžadují podporovaný hostovaný operační systém.

    :::image type="content" source="media/manage-cluster/cluster-settings-session.png" alt-text="obrazovka režimu rozšířené relace technologie Hyper-V clusteru" lightbox="media/manage-cluster/cluster-settings-session.png":::

1. Pokud chcete virtuálním počítačům přidělit Rozsah fyzických uzlů NUMA, vyberte pokrývání uzlů **NUMA**. Neuniformní architektura paměti (NUMA) může virtuálnímu počítači poskytnout více paměti, než kolik je k dispozici na jednom uzlu NUMA.

    :::image type="content" source="media/manage-cluster/cluster-settings-numa.png" alt-text="Pokrývání obrazovky NUMA clusteru" lightbox="media/manage-cluster/cluster-settings-numa.png":::

1. Pokud chcete určit počet virtuálních počítačů, které se dají při spuštění (za provozu) přesunout současně, vyberte **migrace za provozu**, vyberte číslo a pak zadejte následující:

   - v případě **ověřovacího protokolu** vyberte buď **CredSSP** , nebo **Kerberos**.

   - pro **možnost výkon** vyberte buď **kompresi** , nebo protokol **SMB**. Komprimovaná data se odesílají přes připojení TCP/IP.

   - Zaškrtnutím políčka **použít libovolné sítě** můžete použít jakoukoli dostupnou síť v uzlu k provedení migrace.

        :::image type="content" source="media/manage-cluster/cluster-settings-liv-migration.png" alt-text="obrazovka Migrace za provozu clusteru" lightbox="media/manage-cluster/cluster-settings-liv-migration.png":::

1. Chcete-li určit počet migrací úložiště, které lze provést současně, vyberte možnost **migrace úložiště** a pak vyberte číslo.

    :::image type="content" source="media/manage-cluster/cluster-settings-sto-migration.png" alt-text="obrazovka migrace úložiště clusteru" lightbox="media/manage-cluster/cluster-settings-sto-migration.png":::

## <a name="manage-cluster-resources"></a>Správa prostředků clusteru

Pokud chcete spustit, zastavit, odebrat nebo simulovat selhání prostředku clusteru, udělejte toto:

1. Vyberte **Přehled** a potom v části **prostředky clusteru** vyberte prostředek a vyberte **Spustit**, **zastavit** nebo **Odebrat**.

    :::image type="content" source="media/manage-cluster/cluster-overview.png" alt-text="obrazovka Správa prostředků clusteru" lightbox="media/manage-cluster/cluster-overview.png":::

1. Vyberte **Simulovat selhání** pro simulaci selhání clusteru:

    :::image type="content" source="media/manage-cluster/cluster-simulate-failure.png" alt-text="nasimulovat obrazovku selhání prostředku" lightbox="media/manage-cluster/cluster-simulate-failure.png":::

## <a name="validate-the-cluster"></a>Ověřit cluster

Pokud chcete cluster ověřit, vyberte **Přehled** a pak vyberte **ověřit cluster**. Podrobné informace o ověřování clusteru najdete v tématu [připojení Azure Stack HCL do Azure](../deploy/validate.md).

:::image type="content" source="media/manage-cluster/validate-cluster.png" alt-text="obrazovka ověřit cluster" lightbox="media/manage-cluster/validate-cluster.png":::

Chcete-li zobrazit a stáhnout sestavy ověření clusteru, vyberte **sestavy ověření** a pak vyberte **Stáhnout sestavu**.

:::image type="content" source="media/manage-cluster/validation-reports.png" alt-text="obrazovka sestavy ověření" lightbox="media/manage-cluster/validation-reports.png":::

## <a name="register-the-cluster-with-azure"></a>Registrace clusteru v Azure

Pokud chcete zaregistrovat nebo zrušit registraci clusteru v Azure, vyberte **Azure Stack registraci HCI**. Podrobné informace o tom, jak to udělat, najdete v tématu [připojení Azure Stack HCL do Azure](../deploy/register-with-azure.md).

:::image type="content" source="media/manage-cluster/cluster-registration.png" alt-text="obrazovka registrace clusteru Azure" lightbox="media/manage-cluster/cluster-registration.png":::

## <a name="remove-the-cluster"></a>Odebrání clusteru

Pokud chcete odebrat (zničit) cluster, vyberte **Přehled** a pak vyberte **Odebrat cluster**.

:::image type="content" source="media/manage-cluster/remove-cluster.png" alt-text="obrazovka odebrání clusteru" lightbox="media/manage-cluster/remove-cluster.png":::

## <a name="next-steps"></a>Další kroky

- Pokud chcete monitorovat svůj cluster, přečtěte si téma [monitorování Azure Stack HCL pomocí Azure monitor](azure-monitor.md).

- Řešení potíží se zprávami o ověření clusteru najdete v tématu [řešení potíží s vytvářením sestav ověření clusteru](validate-qos.md)
