---
title: Správa virtuálních počítačů pomocí centra pro správu Windows
description: Naučte se vytvářet a spravovat virtuální počítače v clusteru Azure Stack HCL pomocí centra pro správu systému Windows.
author: v-dasis
ms.topic: how-to
ms.date: 11/06/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 73d705bf5b36509b3aed31afb09105f2da91862f
ms.sourcegitcommit: 08ef9545316798c9a21c2f9bc1da8c15cb648982
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360223"
---
# <a name="manage-vms-with-windows-admin-center"></a>Správa virtuálních počítačů pomocí centra pro správu Windows

> Platí pro Azure Stack HCI, verze 20H2; Windows Server 2019

Centrum pro správu Windows se dá použít k vytváření a správě virtuálních počítačů na Azure Stack HCI.

## <a name="create-a-new-vm"></a>Vytvořit nový virtuální počítač

Nový virtuální počítač můžete snadno vytvořit pomocí centra pro správu systému Windows.

:::image type="content" source="media/manage-vm/new-vm.png" alt-text="Obrazovka nový virtuální počítač" lightbox="media/manage-vm/new-vm.png":::

1. Na domovské obrazovce centra pro správu Windows pod položkou **všechna připojení** vyberte server nebo cluster, na kterém chcete virtuální počítač vytvořit.
1. V části **nástroje** přejděte dolů a vyberte **virtuální počítače**.
1. V části **virtuální počítače** vyberte kartu **inventář** a pak vyberte **Přidat** a **Nový**.
1. V části **Nový virtuální počítač** zadejte název vašeho virtuálního počítače.
1. Vyberte **generaci 2 (doporučeno)**.
1. V části **hostitel** vyberte server, na kterém chcete virtuální počítač umístit.
1. V části **cesta** vyberte předem přiřazenou cestu k souboru z rozevíracího seznamu nebo klikněte na tlačítko **Procházet** a vyberte složku, do které chcete uložit konfiguraci virtuálního počítače a soubory virtuálního pevného disku (VHD). Můžete přejít k libovolné dostupné sdílené složce SMB v síti zadáním cesty jako *\\ server\share*.

1. V části **virtuální procesory** vyberte počet virtuálních procesorů a to, jestli chcete pro virtuální počítač povolit vnořenou virtualizaci.
1. V části **paměť** vyberte velikost spouštěcí paměti (4 GB se doporučuje jako minimální) a k virtuálnímu počítači se přidělí minimální a maximální rozsah dynamické paměti, která je k dispozici.
1. V části **síť** vyberte v rozevíracím seznamu síťový adaptér.
1. V části **úložiště** klikněte na **Přidat** a vyberte, jestli se má vytvořit nový prázdný virtuální pevný disk, nebo jestli se má použít stávající virtuální pevný disk. Pokud používáte existující virtuální pevný disk, klikněte na **Procházet** a vyberte platnou cestu k souboru.  
1. V části **operační systém** proveďte jednu z následujících akcí:
   - Pokud chcete nainstalovat operační systém pro virtuální počítač po vytvoření virtuálního počítače, vyberte **nainstalovat operační systém později** .
   - Vyberte možnost **nainstalovat operační systém ze souboru bitové kopie (*. ISO)** , klikněte na tlačítko **Procházet** a vyberte příslušný soubor bitové kopie ISO, který chcete použít.
1. Po dokončení klikněte na **vytvořit** a vytvořte virtuální počítač.
1. Pokud chcete virtuální počítač spustit, v seznamu **Virtual Machines** najeďte myší na nový virtuální počítač, na levé straně zaškrtněte políčko **Spustit**.
1. V části **stav** ověřte, že je **spuštěný** stav virtuálního počítače.

## <a name="get-a-list-of-vms"></a>Získání seznamu virtuálních počítačů

Všechny virtuální počítače můžete snadno zobrazit na serveru nebo v clusteru.

:::image type="content" source="media/manage-vm/vm-inventory.png" alt-text="Obrazovka virtuálních počítačů" lightbox="media/manage-vm/vm-inventory.png":::

1. V centru pro správu Windows v části **nástroje** přejděte dolů a vyberte **Virtual Machines**.
1. Na kartě  **inventarizace** na pravé straně je seznam všech virtuálních počítačů, které jsou k dispozici na aktuálním serveru nebo v clusteru, a poskytuje příkazy pro správu jednotlivých virtuálních počítačů. Máte následující možnosti:
    - Zobrazí seznam virtuálních počítačů spuštěných na aktuálním serveru nebo clusteru.
    - Pokud zobrazujete virtuální počítače pro cluster, zobrazte stav a hostitelský server virtuálního počítače. Podívejte se taky na využití CPU a paměti z perspektivy hostitele, včetně zatížení paměti, požadavků na paměť a přidělené paměti a stavu provozu virtuálního počítače, stavu prezenčního signálu a stavu ochrany (pomocí Azure Site Recovery).
    - Vytvořte nový virtuální počítač.
    - Odstranit, spustit, vypnout, vypnout, pozastavit, obnovit nebo přejmenovat virtuální počítač. Také Uložte virtuální počítač, odstraňte uložený stav nebo vytvořte kontrolní bod.
    - Změna nastavení pro virtuální počítač
    - Připojte se k konzole virtuálního počítače pomocí hostitele Hyper-V.
    - Replikace virtuálního počítače pomocí Azure Site Recovery.
    - Pro operace, které je možné spustit na několika virtuálních počítačích, jako je spuštění, vypnutí, uložení, pozastavení, odstranění nebo resetování, můžete vybrat několik virtuálních počítačů a tuto operaci spustit jednou.

## <a name="view-vm-details"></a>Zobrazit podrobnosti o virtuálním počítači

Můžete zobrazit podrobné informace a grafy výkonu pro konkrétní virtuální počítač ze své vyhrazené stránky.

:::image type="content" source="media/manage-vm/vm-details.png" alt-text="Informační obrazovka s podrobnými informacemi o virtuálních počítačích" lightbox="media/manage-vm/vm-details.png":::

1. V části **nástroje** přejděte dolů a vyberte **virtuální počítače**.
1. Klikněte na kartu **inventarizace** napravo a potom vyberte virtuální počítač. Na následující stránce můžete provést následující akce:

   - Zobrazení živých a historických datových grafů pro procesor, paměť, síť, IOPS a vstupně-výstupní propustnost (historická data jsou dostupná jenom pro clustery s více sblíženými)
   - Zobrazit, vytvořit, použít, přejmenovat a odstranit kontrolní body.
   - Zobrazit podrobnosti o souborech virtuálního pevného disku (. VHD), síťových adaptérech a hostitelském serveru.
   - Zobrazte stav virtuálního počítače.
   - Uložte virtuální počítač, odstraňte uložený stav, exportujte nebo naklonujte virtuální počítač.
   - Změňte nastavení virtuálního počítače.
   - Připojte se k konzole virtuálních počítačů pomocí VMConnect přes hostitele Hyper-V.
   - Replikujte virtuální počítač pomocí Azure Site Recovery.

## <a name="view-aggregate-vm-metrics"></a>Zobrazit agregované metriky virtuálních počítačů

Metriky využití prostředků a výkonu můžete zobrazit u všech virtuálních počítačů v clusteru.

:::image type="content" source="media/manage-vm/host-metrics.png" alt-text="obrazovka metriky hostitele" lightbox="media/manage-vm/host-metrics.png":::

1. V části **nástroje** přejděte dolů a vyberte **virtuální počítače**.
1. Karta **Souhrn** na pravé straně poskytuje holistický zobrazení prostředků hostitele Hyper-V a výkonu pro vybraný server nebo cluster, včetně následujících:
    - Počet virtuálních počítačů, které jsou spuštěny, zastaveny, pozastaveny a uloženy
    - Nedávné výstrahy týkající se stavu nebo události protokolu událostí technologie Hyper-V pro clustery
    - Využití CPU a paměti u hostitele vs – rozpis hosta
    - Živý a historický datový graf pro vstupně-výstupní operace a propustnost vstupně-výstupních operací pro clustery

## <a name="change-vm-settings"></a>Změnit nastavení virtuálního počítače

Existuje celá řada nastavení, která můžete pro virtuální počítač změnit.

> [!NOTE]
> Některá nastavení se nedají změnit u virtuálního počítače, na kterém běží, musíte nejdřív virtuální počítač zastavit.

1. V části **nástroje** přejděte dolů a vyberte **virtuální počítače**.
1. Klikněte na kartu **inventarizace** napravo, vyberte virtuální počítač a pak klikněte na **Nastavení**.

1. Chcete-li změnit akce spuštění/zastavení virtuálního počítače a obecná nastavení, vyberte možnost **Obecné** a proveďte následující kroky:
    - Pokud chcete změnit název virtuálního počítače, zadejte ho do pole **název** .
    - Chcete-li změnit výchozí akce spuštění/zastavení virtuálního počítače, vyberte odpovídající nastavení z rozevíracích seznamů.
    - Pokud chcete změnit časové intervaly pro pozastavení nebo spuštění virtuálního počítače, zadejte příslušné hodnoty do zobrazených polí.

        :::image type="content" source="media/manage-vm/vm-settings-general.png" alt-text="Obrazovka Obecné nastavení virtuálního počítače" lightbox="media/manage-vm/vm-settings-general.png":::

1. Vyberte **paměť** pro změnu spouštěcí paměti virtuálního počítače, rozsah dynamické paměti, procento vyrovnávací paměti a váhu paměti.

    :::image type="content" source="media/manage-vm/vm-settings-memory.png" alt-text="Obrazovka změnit nastavení paměti virtuálního počítače" lightbox="media/manage-vm/vm-settings-memory.png":::

1. Vyberte **procesory** , pokud chcete změnit počet virtuálních procesorů, povolit vnořenou virtualizaci nebo povolit souběžné MULTITHREADING (SMT).

    :::image type="content" source="media/manage-vm/vm-settings-processor.png" alt-text="Obrazovka změnit nastavení procesoru virtuálního počítače" lightbox="media/manage-vm/vm-settings-processor.png":::

1. Chcete-li změnit velikost existujícího disku, upravte hodnotu **Velikost (GB)**. Chcete-li přidat nový virtuální disk, vyberte **disky** a pak vyberte, zda chcete vytvořit prázdný virtuální disk nebo použít existující soubor bitové kopie (. ISO) pro virtuální disk nebo soubor ISO (. ISO). Klikněte na **Procházet** a vyberte cestu k virtuálnímu disku nebo souboru obrázku.

    :::image type="content" source="media/manage-vm/vm-settings-disk.png" alt-text="Obrazovka změnit nastavení disku virtuálního počítače" lightbox="media/manage-vm/vm-settings-disk.png":::

1. Chcete-li přidat, odebrat nebo změnit nastavení síťového adaptéru, vyberte položku **sítě** a proveďte následující kroky:
    - Zadejte virtuální přepínač, který se má použít, a jestli se má povolit identifikace virtuální sítě LAN (musíte taky zadat i identifikátor sítě VLAN).
    - Pokud chcete změnit další nastavení adaptéru síťového adaptéru, klikněte na **Upřesnit** , abyste mohli:
        - Vybrat mezi dynamickým nebo statickým typem adresy MAC
        - Povolit falšování adresy MAC
        - Povolit správu šířky pásma a zadat rozsah maximum/minimum

        :::image type="content" source="media/manage-vm/vm-settings-network.png" alt-text="Obrazovka změnit nastavení sítě virtuálních počítačů" lightbox="media/manage-vm/vm-settings-network.png":::

1. Chcete-li přidat spouštěcí zařízení nebo změnit sekvenci spouštění virtuálního počítače, vyberte možnost **pořadí spouštění** .

    :::image type="content" source="media/manage-vm/vm-settings-boot.png" alt-text="Obrazovka změnit pořadí spouštění virtuálního počítače" lightbox="media/manage-vm/vm-settings-boot.png":::

1. Vyberte **kontrolní body** , aby se povolily kontrolní body virtuálních počítačů, vyberte typ kontrolního bodu a zadejte umístění souboru kontrolního bodu.

    > [!NOTE]
    > Nastavení **produkčního** kontrolního bodu se doporučuje a v hostovaném operačním systému používá technologii zálohování k vytvoření kontrolních bodů konzistentních s daty. **Standardní** nastavení používá snímky VHD k vytváření kontrolních bodů se stavem aplikace a služby.

     :::image type="content" source="media/manage-vm/vm-settings-checkpoint.png" alt-text="Obrazovka změnit kontrolní body virtuálního počítače" lightbox="media/manage-vm/vm-settings-checkpoint.png":::

1. Vyberte **pravidla spřažení** a vytvořte tak pravidlo spřažení pro virtuální počítač. Další informace o vytváření pravidel spřažení najdete v tématu [Vytvoření pravidel spřažení serveru a lokality pro virtuální počítače](vm-affinity.md).

    :::image type="content" source="media/manage-vm/vm-affinity.png" alt-text="Obrazovka pravidla spřažení virtuálních počítačů" lightbox="media/manage-vm/vm-affinity.png":::

1. Pokud chcete změnit nastavení zabezpečení virtuálního počítače, vyberte **zabezpečení** a postupujte takto:
    - Vyberte **Povolit zabezpečené spouštění** , abyste zabránili spuštění neautorizovaného kódu při spuštění (doporučeno). V rozevíracím seznamu vyberte taky šablonu Microsoft nebo open source.
    - Pro **šablonu** vyberte šablonu zabezpečení, kterou chcete použít.

    - V části **podpora šifrování** můžete

        - Vyberte možnost **Povolit modul TPM (Trusted Platform Module** ), aby bylo možné používat hardwarový modul kryptografických služeb.

        - Povolení šifrování provozu migrace stavu a virtuálního počítače

        > [!NOTE]
        > Podpora šifrování vyžaduje pro virtuální počítač ochranu pomocí klíče (KP). Pokud ještě neexistuje, výběr jedné z těchto možností vygeneruje hodnotu KP, která umožňuje spustit virtuální počítač na tomto hostiteli.

    - V části **zásady zabezpečení** vyberte **Povolit stínění** pro další možnosti ochrany pro virtuální počítač.

        :::image type="content" source="media/manage-vm/vm-settings-security.png" alt-text="Změnit nastavení zabezpečení virtuálního počítače" lightbox="media/manage-vm/vm-settings-security.png":::

## <a name="move-a-vm-to-another-server-or-cluster"></a>Přesunutí virtuálního počítače na jiný server nebo cluster

Virtuální počítač můžete snadno přesunout na jiný server nebo jiný cluster následujícím způsobem:

1. V části **nástroje** přejděte dolů a vyberte **virtuální počítače**.
1. Na kartě **inventář** vyberte ze seznamu virtuální počítač a vyberte **Spravovat > přesunout**.
1. Zvolte server ze seznamu a vyberte **přesunout**.
1. Pokud chcete přesunout virtuální počítač i jeho úložiště, vyberte, jestli se má přesunout do jiného clusteru nebo na jiný server ve stejném clusteru.

    :::image type="content" source="media/manage-vm/vm-more-move.png" alt-text="Obrazovka přesunout virtuální počítač" lightbox="media/manage-vm/vm-more-move.png":::

1. Pokud chcete přesunout jenom úložiště virtuálního počítače, vyberte ho, aby se přesunul na stejnou cestu, nebo vyberte jiné cesty pro konfiguraci, kontrolní bod nebo inteligentní stránkování.

    :::image type="content" source="media/manage-vm/vm-move-storage.png" alt-text="Obrazovka přesunutí úložiště virtuálního počítače" lightbox="media/manage-vm/vm-move-storage.png":::

## <a name="join-a-vm-to-a-domain"></a>Připojení virtuálního počítače k doméně

Virtuální počítač můžete snadno připojit k doméně následujícím způsobem:

:::image type="content" source="media/manage-vm/vm-domain-join.png" alt-text="Obrazovka přesunout připojení k doméně virtuálního počítače" lightbox="media/manage-vm/vm-domain-join.png":::

1. V části **nástroje** přejděte dolů a vyberte **virtuální počítače**.
1. Na kartě **inventář** vyberte virtuální počítač ze seznamu a vyberte **Spravovat > připojení k doméně**.
1. Zadejte název domény, ke které se chcete připojit, spolu s uživatelským jménem a heslem domény.
1. Zadejte uživatelské jméno a heslo virtuálního počítače.
1. Po dokončení klikněte na tlačítko **připojit**.

## <a name="clone-a-vm"></a>Klonování virtuálního počítače

Virtuální počítač můžete snadno klonovat následujícím způsobem:

1. V části **nástroje** přejděte dolů a vyberte **virtuální počítače**.
1. Na pravé straně vyberte kartu **inventarizace** . Zvolte virtuální počítač ze seznamu a vyberte **spravovat > klonování**.
1. Zadejte název a cestu k klonovanému virtuálnímu počítači.
1. Pokud jste to ještě neudělali, spusťte na svém VIRTUÁLNÍm počítači nástroj Sysprep.

:::image type="content" source="media/manage-vm/vm-clone.png" alt-text="Obrazovka klonovat virtuální počítač" lightbox="media/manage-vm/vm-clone.png":::

## <a name="import-or-export-a-vm"></a>Import nebo Export virtuálního počítače

Můžete snadno importovat nebo exportovat virtuální počítač. Následující postup popisuje proces importu.

:::image type="content" source="media/manage-vm/vm-more-import.png" alt-text="Obrazovka Import virtuálního počítače" lightbox="media/manage-vm/vm-more-import.png":::

1. V části **nástroje** přejděte dolů a vyberte **virtuální počítače**.
1. Na kartě **inventarizace** vyberte **Přidat > importovat**.
1. Zadejte název složky, která obsahuje virtuální počítač, nebo klikněte na tlačítko **Procházet** a vyberte složku.
1. Vyberte virtuální počítač, který chcete naimportovat.
1. V případě potřeby vytvořte jedinečné ID virtuálního počítače.
1. Po dokončení vyberte **importovat**.

Pro export virtuálního počítače je tento postup podobný:

1. V části **nástroje** přejděte dolů a vyberte **virtuální počítače**.
1. Na kartě **inventarizace** v seznamu vyberte virtuální počítač, který se má exportovat.
1. Vyberte **spravovat > exportovat**.
1. Zadejte cestu k exportu virtuálního počítače.

:::image type="content" source="media/manage-vm/vm-export.png" alt-text="Obrazovka exportovat virtuální počítač" lightbox="media/manage-vm/vm-export.png":::

## <a name="view-vm-event-logs"></a>Zobrazit protokoly událostí virtuálních počítačů

Protokoly událostí virtuálních počítačů můžete zobrazit následujícím způsobem:

1. V části **nástroje** přejděte dolů a vyberte **virtuální počítače**.
1. Na kartě **Souhrn** na pravé straně vyberte **Zobrazit všechny události**.
1. Vyberte kategorii událostí a rozbalte zobrazení.

## <a name="connect-to-a-vm-by-using-remote-desktop"></a>Připojení k virtuálnímu počítači pomocí vzdálené plochy

Místo použití centra pro správu systému Windows můžete také spravovat virtuální počítače pomocí hostitele Hyper-V pomocí připojení protokol RDP (Remote Desktop Protocol) (RDP).

1. V části **nástroje** přejděte dolů a vyberte **virtuální počítače**.
1. Na kartě **inventář** vyberte ze seznamu možnost vybrat virtuální počítač a vyberte možnost **připojit > připojit** nebo **připojit > stáhnout soubor RDP** . Obě možnosti používají nástroj VMConnect pro připojení k virtuálnímu počítači hosta prostřednictvím hostitele Hyper-V a vyžadují, abyste zadali přihlašovací údaje uživatelského jména a hesla správce pro hostitele Hyper-V.

    - Možnost **připojit** se připojí k virtuálnímu počítači pomocí vzdálené plochy ve webovém prohlížeči.

    - Možnost **Stáhnout soubor RDP** stáhne soubor. RDP, který můžete otevřít pro připojení k aplikaci Připojení ke vzdálené ploše (mstsc.exe).

## <a name="protect-vms-with-azure-site-recovery"></a>Ochrana virtuálních počítačů pomocí Azure Site Recovery

Pomocí centra pro správu Windows můžete nakonfigurovat Azure Site Recovery a replikovat místní virtuální počítače do Azure. Toto je volitelná služba pro přidání hodnoty. Informace o tom, jak začít, najdete v tématu [ochrana virtuálních počítačů pomocí Azure Site Recovery](azure-site-recovery.md).

:::image type="content" source="media/manage-vm/vm-more-azure.png" alt-text="Instalační obrazovka Azure Site Recovery" lightbox="media/manage-vm/vm-more-azure.png":::

## <a name="next-steps"></a>Další kroky

Virtuální počítače taky můžete vytvářet a spravovat pomocí Windows PowerShellu. Další informace najdete v tématu [Správa virtuálních počítačů v Azure Stack HCI pomocí prostředí Windows PowerShell](vm-powershell.md).