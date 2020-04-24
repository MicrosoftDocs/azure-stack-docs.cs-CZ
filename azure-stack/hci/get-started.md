---
title: Začínáme se službou Azure Stack HCI a centrem pro správu Windows
description: Rychle se připojte ke stávajícímu clusteru Azure Stack HCI a pomocí centra pro správu systému Windows monitorujte výkon clusteru a úložiště.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: efd0922639f628bfea0f2c78755b10de0053bc1f
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "80986386"
---
# <a name="get-started-with-azure-stack-hci-and-windows-admin-center"></a>Začínáme se službou Azure Stack HCI a centrem pro správu Windows

> Platí pro: Windows Server 2019

Toto téma poskytuje pokyny pro instalaci centra pro správu systému Windows, připojení k Azure Stack clusteru HCI a monitorování výkonu clusteru a úložiště.

## <a name="install-windows-admin-center"></a>Nainstalovat centrum pro správu Windows

Nejjednodušší způsob, jak nainstalovat centrum pro správu Windows, je na místním počítači s Windows 10, i když musíte být členem skupiny místních správců.

1. Stáhněte si [Centrum pro správu Windows](https://www.microsoft.com/evalcenter/evaluate-windows-admin-center) z centra Microsoft Evaluation Center. I když říká "zahájit zkušební období", "Toto je všeobecně dostupná verze pro produkční použití, která je součástí licence Windows serveru.
2. Spusťte soubor WindowsAdminCenter. msi, který chcete nainstalovat.
3. Při prvním spuštění centra pro správu systému Windows se v oznamovací oblasti plochy zobrazí ikona. Klikněte na tuto ikonu pravým tlačítkem a výběrem možnosti Otevřít otevřete nástroj ve výchozím prohlížeči. Po zobrazení výzvy k výběru certifikátu nezapomeňte vybrat klientský certifikát centra pro správu systému Windows.

## <a name="add-and-connect-to-an-azure-stack-hci-cluster"></a>Přidání a připojení k Azure Stack clusteru HCI

Po dokončení instalace centra pro správu systému Windows můžete přidat cluster, který bude spravován na hlavní stránce s přehledem.

1. Klikněte na **+ Přidat** pod **všechna připojení**.

    :::image type="content" source="media/get-started/addcluster.png" alt-text="[Přidat snímek clusteru":::

2. Vyberte, pokud chcete přidat cluster Windows serveru:
    
    :::image type="content" source="media/get-started/chooseconnectiontype.png" alt-text="Vybrat typ připojení snímek obrazovky":::

3. Zadejte název clusteru, který chcete spravovat, a klikněte na **Přidat**. Cluster se přidá do vašeho seznamu připojení na stránce Přehled.

4. V části **všechna připojení**klikněte na název clusteru, který jste právě přidali. Centrum pro správu systému Windows spustí **Správce clusteru** a přejde přímo na řídicí panel centra pro správu systému Windows pro daný cluster.

## <a name="monitor-cluster-performance-with-the-windows-admin-center-dashboard"></a>Monitorování výkonu clusteru pomocí řídicího panelu centra pro správu systému Windows

Řídicí panel centra pro správu systému Windows poskytuje výstrahy a informace o stavu serverů, jednotek a svazků a také informace o využití procesoru, paměti a úložiště. Dolní část řídicího panelu zobrazuje informace o výkonu clusteru, jako je IOPS a latence, podle hodin, dnů, týdnů, měsíců nebo let.

:::image type="content" source="media/get-started/dashboard.png" alt-text="Snímek obrazovky řídicího panelu centra pro správu systému Windows":::

## <a name="monitor-performance-of-individual-components"></a>Monitorování výkonu jednotlivých komponent

Nabídka **nástroje** nalevo od řídicího panelu vám umožní přejít k podrobnostem jakékoli součásti clusteru a zobrazit tak souhrny a inventáře virtuálních počítačů, serverů, svazků a jednotek.

### <a name="virtual-machines"></a>Virtuální počítače

Pokud chcete zobrazit souhrn virtuálních počítačů, které jsou spuštěné v clusteru, klikněte na **virtuální počítače** v nabídce **nástroje** vlevo.

:::image type="content" source="media/get-started/vms-summary.png" alt-text="Souhrn virtuálního počítače":::

Pro úplný inventář virtuálních počítačů spuštěných v clusteru spolu s jejich stavem, hostitelským serverem, využitím procesoru, tlakem paměti, nároky na paměť, přiřazenou pamětí a dobou provozu klikněte v horní části stránky na **inventář** .

:::image type="content" source="media/get-started/vms-inventory.png" alt-text="Inventář virtuálních počítačů":::

### <a name="servers"></a>Servery

Chcete-li zobrazit souhrn serverů v clusteru, klikněte na tlačítko **servery** v nabídce **nástroje** vlevo.

:::image type="content" source="media/get-started/servers-summary.png" alt-text="Souhrn serverů":::

V případě kompletního inventáře serverů v clusteru, včetně jejich stavu, doby provozu, výrobce, modelu a sériového čísla, klikněte v horní části stránky na možnost **inventář** .

:::image type="content" source="media/get-started/servers-inventory.png" alt-text="Inventář serverů":::

### <a name="volumes"></a>Svazky

Pokud chcete zobrazit souhrn svazků v clusteru, klikněte na **svazky** v nabídce **nástroje** vlevo.

:::image type="content" source="media/get-started/volumes-summary.png" alt-text="Souhrn svazků":::

V případě kompletního inventáře svazků v clusteru, včetně jejich stavu, systému souborů, odolnosti, velikosti, využití úložiště a IOPS, klikněte v horní části stránky na **inventář** .

:::image type="content" source="media/get-started/volumes-inventory.png" alt-text="Inventář svazků":::

### <a name="drives"></a>Drives

Chcete-li zobrazit souhrn jednotek v clusteru, klikněte na **jednotky** v nabídce **nástroje** vlevo.

:::image type="content" source="media/get-started/drives-summary.png" alt-text="Souhrn jednotek":::

V případě kompletního inventáře jednotek v clusteru spolu s jejich sériovým číslem, stavem, modelem, velikostí, typem, použitím, umístěním, serverem a kapacitou klikněte v horní části stránky na **inventář** .

:::image type="content" source="media/get-started/drives-inventory.png" alt-text="Inventář jednotek":::

### <a name="virtual-switches"></a>Virtuální přepínače

Pokud chcete zobrazit nastavení pro virtuální přepínač v clusteru, klikněte na **virtuální přepínače** v nabídce **nástroje** vlevo a pak klikněte na název virtuálního přepínače, pro který chcete zobrazit nastavení. Centrum pro správu systému Windows zobrazí síťové adaptéry přidružené k virtuálnímu přepínači, včetně jejich IP adres, stavu připojení, rychlosti připojení a adresy MAC.

:::image type="content" source="media/get-started/virtual-switch-settings.png" alt-text="Nastavení virtuálního přepínače":::

## <a name="add-counters-with-the-performance-monitor-tool"></a>Přidání čítačů pomocí nástroje sledování výkonu

Pomocí nástroje sledování výkonu můžete zobrazit a porovnat čítače výkonu pro Windows, aplikace nebo zařízení v reálném čase.

1. V nabídce **nástroje** na levé straně vyberte **sledování výkonu** .
2. Kliknutím na možnost **prázdný pracovní prostor** spustíte nový pracovní prostor, nebo **obnovte předchozí** a obnovte předchozí pracovní prostor.
    :::image type="content" source="media/get-started/performance-monitor.png" alt-text="Snímek obrazovky sledování výkonu":::
3. Pokud vytváříte nový pracovní prostor, klikněte na tlačítko **Přidat čítač** a vyberte jeden nebo více zdrojových serverů, které chcete monitorovat, nebo vyberte celý cluster.
4. Vyberte objekt a instanci, kterou chcete monitorovat, a také typ čítače a grafu pro zobrazení dynamických informací o výkonu.
    :::image type="content" source="media/get-started/example-counter.png" alt-text="Ukázkový snímek obrazovky čítače":::
5. Uložte pracovní prostor kliknutím na **uložit > Uložit jako** v horní nabídce.

## <a name="use-azure-monitor-for-monitoring-and-alerts"></a>Použití Azure Monitor pro monitorování a výstrahy

Můžete také použít [Azure monitor](/windows-server/manage/windows-admin-center/azure/azure-monitor) (vyžaduje předplatné Azure) ke shromáždění událostí a čítačů výkonu pro účely analýzy a vytváření sestav, provedení akce při zjištění konkrétní podmínky a příjem oznámení prostřednictvím e-mailu. V nabídce **nástroje** klikněte na **Azure monitor** a připojte se přímo k Azure z centra pro správu Windows.

## <a name="collect-diagnostics-information"></a>Shromažďovat diagnostické informace

V nabídce **nástroje** vyberte **Diagnostika** a shromážděte informace pro řešení problémů s clusterem. Pokud zavoláte podpora Microsoftu, mohou se tyto informace vyžádat.

## <a name="next-steps"></a>Další kroky

Hlubší podrobně na sledování výkonu najdete zde:

- [Historie výkonu pro Prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/performance-history)
- [Monitorování Azure Stack HCL pomocí Azure Monitor](manage/azure-monitor.md)
