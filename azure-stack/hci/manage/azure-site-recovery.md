---
title: Ochrana Azure Stack virtuálních počítačů HCI pomocí Azure Site Recovery
description: Použijte centrum pro správu systému Windows k ochraně Azure Stack virtuálních počítačů HCI pomocí Azure Site Recovery.
ms.topic: article
author: davannaw-msft
ms.author: dawhite
ms.date: 04/30/2020
ms.localizationpriority: low
ms.openlocfilehash: 01b6f16b3812b5f11f95d9d11f6563a1631fd690
ms.sourcegitcommit: 21cdab346fc242b8848a04a124bc16c382ebc6f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783932"
---
# <a name="protect-azure-stack-hci-vms-using-azure-site-recovery"></a>Ochrana Azure Stack virtuálních počítačů HCI pomocí Azure Site Recovery

>Platí pro: Windows Server 2019

Centrum pro správu systému Windows zjednodušuje proces replikace virtuálních počítačů na serverech nebo clusterech, což vám dává nárok na využití Azure z vlastního datového centra. K automatizaci instalačního programu připojíte centrum pro správu Windows k Azure.

V tomto článku se dozvíte, jak pomocí Azure Site Recovery nakonfigurovat nastavení replikace a vytvořit plán obnovení v Azure Portal. Dokončení těchto úloh umožní centru pro správu systému Windows spustit replikaci virtuálních počítačů, aby chránila vaše virtuální počítače. Azure Site Recovery také chrání fyzické servery a uzly clusteru.

Další informace najdete v tématu [propojení Windows serveru s Azure Hybrid Services](/windows-server/manage/windows-admin-center/azure/).

## <a name="how-azure-site-recovery-works-with-windows-admin-center"></a>Jak Azure Site Recovery funguje s centrem pro správu Windows
*Azure Site Recovery* je služba Azure, která replikuje úlohy běžící na virtuálních počítačích, aby byla vaše důležitá firemní infrastruktura chráněná před havárií. Další informace najdete v tématu [o Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview).

Azure Site Recovery se skládá ze dvou součástí: *replikace* a *převzetí služeb při selhání*. Část replikace chrání vaše virtuální počítače před havárií tím, že replikuje virtuální pevný disk cílového virtuálního počítače do účtu služby Azure Storage. Potom můžete virtuální počítače převzít služby při selhání a v případě havárie je spouštět v Azure. Testovací převzetí služeb při selhání můžete provést i bez vlivu na vaše primární virtuální počítače a otestovat tak proces obnovení v Azure.

Dokončení instalace komponenty replikace je dostatečné pro ochranu virtuálních počítačů před havárií. Virtuální počítače v Azure ale nemůžete spustit, dokud nenastavíte část procesu převzetí služeb při selhání.

Část převzetí služeb při selhání můžete nastavit, pokud chcete převzít služby při selhání virtuálního počítače Azure. při počátečním nastavení se nevyžaduje. Pokud hostitelský server přestane pracovat, můžete v daném čase nakonfigurovat komponentu převzetí služeb při selhání a získat přístup k úlohám chráněného virtuálního počítače. Před havárií však doporučujeme nakonfigurovat nastavení související s převzetím služeb při selhání.

## <a name="prerequisites-and-planning"></a>Předpoklady a plánování
K provedení kroků v tomto článku jsou potřeba následující:

- Cílové servery, které hostují virtuální počítače, které chcete chránit, musí mít přístup k Internetu, aby je bylo možné replikovat do Azure.
- Připojení z centra pro správu Windows do Azure. Další informace najdete v tématu [Konfigurace integrace Azure](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-integration).
- Projděte si nástroj pro plánování kapacity a vyhodnoťte požadavky na úspěšnou replikaci a převzetí služeb při selhání. Další informace najdete v tématu [o Plánovač nasazení služby Azure Site Recovery zotavení po havárii technologie Hyper-V do Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-site-walkthrough-capacity).

## <a name="step-1-set-up-vm-protection-on-your-target-host"></a>Krok 1: nastavení ochrany virtuálních počítačů na cílovém hostiteli
Proveďte následující kroky jednou pro každý hostitelský server nebo cluster obsahující virtuální počítače, které chcete zacílit na ochranu:
1. V centru pro správu Windows na stránce **všechna připojení** se připojte k serveru nebo clusteru, který hostuje virtuální počítače, které chcete chránit.
1. V části **nástroje**vyberte **virtuální počítače**a pak vyberte kartu **inventář** .
1. Vyberte libovolný virtuální počítač (nemusí se jednat o virtuální počítač, který chcete chránit).
1. Rozbalte podnabídku **Další** a pak vyberte **nastavit ochranu virtuálního počítače**.

    :::image type="content" source="media/azure-site-recovery/set-up-vm-protection.png" alt-text="Možnost nastavení podnabídky ochrana virtuálního počítače v centru pro správu systému Windows.":::

1. Přihlaste se ke svému účtu Azure.
1. Na stránce nastavení hostitele pomocí Azure Site Recovery zadejte požadované informace a pak vyberte **nastavit**:

   - **Předplatné:** Předplatné Azure, které chcete použít pro replikaci virtuálních počítačů na tomto hostiteli.
   - **Skupina prostředků:** Název nové skupiny prostředků.
   - **Recovery Services trezor:** Název trezoru Azure Site Recovery pro chráněné virtuální počítače na tomto hostiteli.  
   - **Umístění:** Oblast Azure, ve které by se měly vytvořit prostředky Azure Site Recovery.

    :::image type="content" source="media/azure-site-recovery/set-up-host-with-asr.png" alt-text="Stránka nastavení hostitele s Azure Site Recovery v centru pro správu systému Windows.":::

1. Počkejte, dokud neuvidíte oznámení: **nastavení Site Recovery dokončeno**.
 
Tento proces může trvat až 10 minut. Průběh můžete sledovat tak, že přejdete na **oznámení** (ikona zvonku v pravém horním rohu centra pro správu systému Windows).

>[!NOTE]
> Tento proces automaticky nainstaluje agenta Azure Site Recovery do cílového serveru nebo uzlů (Pokud konfigurujete cluster) a vytvoří **skupinu prostředků** se zadaným **účtem úložiště** a **trezorem**v zadaném **umístění**. Registruje také cílového hostitele s Azure Site Recovery službu a nakonfiguruje výchozí zásady replikace.

## <a name="step-2-select-vms-to-protect"></a>Krok 2: vyberte virtuální počítače, které chcete chránit.
K ochraně virtuálních počítačů proveďte následující kroky:
1. V centru pro správu systému Windows se vraťte k serveru nebo clusteru, který jste nakonfigurovali v předchozím úkolu.
1. V části **nástroje**vyberte **virtuální počítače**a pak vyberte kartu **inventář** .
1. Vyberte virtuální počítač, který chcete chránit, rozbalte podnabídku **Další** a pak vyberte **chránit virtuální počítač**.

    :::image type="content" source="media/azure-site-recovery/protect-vm-setting.png" alt-text="Možnost chránit nastavení virtuálního počítače v centru pro správu systému Windows.":::

1. Zkontrolujte požadavky na kapacitu pro ochranu virtuálního počítače. Další informace najdete v tématu [plánování kapacity pro zotavení po havárii virtuálního počítače Hyper-V](https://docs.microsoft.com/azure/site-recovery/site-recovery-capacity-planner).

    Pokud chcete použít účet Premium Storage, vytvořte ho v Azure Portal. Další informace najdete v části **SSD úrovně Premium** o tom, [Jaké typy disků jsou dostupné v Azure?](https://docs.microsoft.com/azure/storage/common/storage-premium-storage) Možnost **vytvořit novou** v centru pro správu Windows vytvoří účet úložiště úrovně Standard.

1. Zadejte název **účtu úložiště** , který se má použít pro replikaci tohoto virtuálního počítače, a pak vyberte **chránit virtuální počítač** pro povolení replikace pro virtuální počítač.

    :::image type="content" source="media/azure-site-recovery/protect-vm-setting-asr.png" alt-text="Definování účtu úložiště pro Azure Site Recovery pro ochranu virtuálního počítače v centru pro správu systému Windows.":::

    Azure Site Recovery spustí proces replikace. Virtuální počítač je chráněný, pokud se hodnota v **chráněném** sloupci **skladové mřížky virtuálního počítače** změní na **Ano**. Může to trvat několik minut.  

## <a name="step-3-configure-and-run-a-test-failover-in-the-azure-portal"></a>Krok 3: konfigurace a spuštění testovacího převzetí služeb při selhání v Azure Portal
Před spuštěním replikace virtuálního počítače není nutné tento krok dokončit. Virtuální počítač je chráněný pouze replikací. Při nastavování Azure Site Recovery však doporučujeme nakonfigurovat nastavení převzetí služeb při selhání.
 
Při přípravě převzetí služeb při selhání na virtuální počítač Azure proveďte následující kroky:
1. Nastavte síť Azure, kterou se pro virtuální počítač s podporou převzetí služeb při selhání připojí k této virtuální síti. Další informace najdete v tématu [Nastavení zotavení po havárii místních virtuálních počítačů Hyper-V do Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-site-walkthrough-prepare-azure).

    >[!NOTE]
    > Centrum pro správu systému Windows automaticky dokončí kroky v tomto prostředku. Stačí nastavit síť Azure.

1. Spusťte testovací převzetí služeb při selhání. Další informace najdete v tématu [spuštění postupu zotavení po havárii do Azure](https://docs.microsoft.com/azure/site-recovery/hyper-v-site-walkthrough-test-failover).

## <a name="step-4-create-recovery-plans"></a>Krok 4: vytvoření plánů obnovení
Plány obnovení v Azure Site Recovery umožňují převzít služby při selhání a obnovit kolekci virtuálních počítačů celé aplikace. Chráněné virtuální počítače je možné obnovit jednotlivě. Lepším způsobem je přidat do svého plánu obnovení virtuální počítače aplikace. V rámci plánu obnovení pak můžete převzít služby při selhání celé aplikace. K otestování obnovení aplikace můžete použít také funkci testovací převzetí služeb při selhání v plánu obnovení.

Plány obnovení umožňují seskupovat virtuální počítače, sekvencovat pořadí, ve kterém by se měly začít během převzetí služeb při selhání, a automatizovat další kroky obnovení. Po provedení ochrany virtuálních počítačů můžete v Azure Portal přejít do trezoru Azure Site Recovery a vytvořit tak pro ně plány obnovení. Další informace najdete v tématu [Vytvoření a přizpůsobení plánů obnovení](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans).

## <a name="step-5-monitor-replicated-vms-in-azure"></a>Krok 5: monitorování replikovaných virtuálních počítačů v Azure
Pokud nechcete v procesu registrace serveru ověřit žádné chyby, otevřete **Azure Portal**, vyberte **všechny prostředky**, vyberte **Recovery Services trezor**, vyberte **úlohy**a pak vyberte **Site Recovery úlohy**. Název **trezoru Recovery Services** je ten, který jste zadali v kroku 6 prvního úkolu v tomto článku.

Pokud chcete monitorovat replikaci virtuálních počítačů, přečtěte si **Recovery Services trezor**a potom **replikované položky**.

Pokud chcete zobrazit všechny servery, které jsou zaregistrované v trezoru, přejděte na **Recovery Services trezor**, **Site Recovery infrastruktura**a pak na **hostitelích Hyper-v** (V části lokality technologie Hyper-v).

## <a name="known-issue"></a>Známý problém ##
Pokud zaregistrujete Azure Site Recovery s clusterem, pokud se uzel nepovede nainstalovat Azure Site Recovery nebo zaregistrovat službu Azure Site Recovery, vaše virtuální počítače možná nebudou chráněné. Chcete-li ověřit, zda jsou všechny uzly v clusteru registrovány v Azure Portal, klikněte na **Recovery Services trezor**, **úlohy**a potom **Site Recovery úlohy**.

## <a name="next-steps"></a>Další kroky
Další informace o Azure Site Recovery najdete v tématu také:

- [Obecné otázky týkající se Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-faq)
