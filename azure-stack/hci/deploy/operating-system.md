---
title: Nasazení operačního systému Azure Stack HCI
description: Naučte se nasadit Azure Stack operační systém HCI a pak se pomocí centra pro správu Windows připojit k vašim serverům. Naučte se vytvořit serverový cluster a zjistěte, jak získat nejnovější aktualizace a firmware pro Windows pro vaše servery.
author: JohnCobb1
ms.author: v-johcob
ms.topic: tutorial
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/7/2020
ms.openlocfilehash: 51a4411e95207d2f7b544fdf507fe8bd8fc98f2e
ms.sourcegitcommit: 61556b7b6e029e3a26a4b7ef97f0b13fbe7cd5a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96761707"
---
# <a name="deploy-the-azure-stack-hci-operating-system"></a>Nasazení operačního systému Azure Stack HCI

> Platí pro: Azure Stack HCI, verze 20H2

Prvním krokem při nasazení Azure Stack HCI je [stažení Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) a instalace operačního systému na každém serveru, který chcete clusterovat. Tento článek popisuje různé způsoby nasazení operačního systému a připojení k serverům pomocí centra pro správu systému Windows.

> [!NOTE]
> Pokud jste si koupili Azure Stack hardware integrovaného systémového řešení z [katalogu Azure Stack HCI](https://aka.ms/azurestackhcicatalog) přes preferovaný hardwarový partner společnosti Microsoft, měl by být operační systém Azure Stack HCI předem nainstalován. V takovém případě můžete tento krok přeskočit a přejít na, aby se [vytvořil cluster Azure Stack HCI](create-cluster.md).

## <a name="prerequisites"></a>Předpoklady

Před nasazením operačního systému Azure Stack HCI budete potřebovat:

- Určení, jestli hardware splňuje požadavky pro Azure Stack clustery HCI
- Shromáždění požadovaných informací pro úspěšné nasazení
- Instalace centra pro správu Windows na počítač nebo na server pro správu

Informace o požadavcích na Azure Stack HCL pro službu Azure Kubernetes najdete v tématu [AKS požadavky na Azure Stack HCL](../../aks-hci/overview.md#what-you-need-to-get-started).

### <a name="determine-hardware-requirements"></a>Určení hardwarových požadavků

Společnost Microsoft doporučuje, abyste od našich partnerů nakoupili ověřené Azure Stack hardwarového a softwarového řešení HCI. Tato řešení jsou navržená, sestavená a ověřená v rámci naší referenční architektury za účelem zajištění kompatibility a spolehlivosti, takže můžete rychle začít pracovat. Ověřte, že systémy, součásti, zařízení a ovladače, které používáte, jsou systémy Windows Server 2019 s certifikací na základě katalogu Windows serveru. Ověřená řešení najdete na webu [řešení Azure Stack HCL](https://azure.microsoft.com/overview/azure-stack/hci) .

Minimálně budete potřebovat dva servery, spolehlivé síťové připojení s nízkou šířkou pásma a nízkou latencí mezi servery a disky SATA, SAS, NVMe nebo trvalé paměti, které jsou fyzicky připojené pouze k jednomu serveru.

Požadavky na hardware se ale můžou lišit v závislosti na velikosti a konfiguraci clusterů, které chcete nasadit. Pokud se chcete ujistit, že nasazení proběhlo úspěšně, zkontrolujte [požadavky na systém](../concepts/system-requirements.md)Azure Stack HCI.

### <a name="gather-information"></a>Shromažďování informací

Při přípravě na nasazení shromážděte následující podrobnosti o vašem prostředí:

- **Názvy serverů:** Seznamte se s zásadami pro pojmenovávání počítačů, souborů, cest a dalších prostředků ve vaší organizaci. Budete muset zřídit několik serverů, z nichž každý má jedinečné názvy.
- **Název domény:** Seznamte se se zásadami vaší organizace pro pojmenovávání domén a připojení k doméně. Připojíte servery k doméně a budete muset zadat název domény.
- **Statické IP adresy:** Azure Stack HCI vyžaduje statické IP adresy pro provoz úložiště a zatížení a nepodporuje přiřazování dynamických IP adres prostřednictvím protokolu DHCP pro tuto vysokorychlostní síť. Protokol DHCP můžete použít pro síťový adaptér pro správu, pokud nepoužíváte dva v týmu. v takovém případě je nutné použít statické IP adresy. Informace o IP adrese, kterou byste měli použít pro každý server v clusteru, získáte od správce sítě.
- **Sítě RDMA:** Existují dva typy protokolů RDMA: iWarp a RoCE. Všimněte si, že jeden ze síťových adaptérů používá, a pokud RoCE, poznamenejte si také verzi (v1 nebo v2). V případě RoCE si také poznamenejte model přepínače horního stojanu.
- **ID sítě VLAN:** Poznamenejte si ID sítě VLAN, které se má použít pro síťové adaptéry na serverech (pokud nějaké existují). To by mělo být možné získat od správce sítě.
- **Názvy webů:** Pro roztažené clustery se pro zotavení po havárii používají dvě lokality. Můžete nastavit lokality pomocí [Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview), nebo Průvodce vytvořením clusteru může automaticky nastavit pro vás. Informace o nastavování lokalit vám poskytne správce domény.

### <a name="install-windows-admin-center"></a>Nainstalovat centrum pro správu Windows

Centrum pro správu Windows je místně nasazená aplikace založená na prohlížeči pro správu Azure Stack HCI. Nejjednodušší způsob, jak [nainstalovat centrum pro správu Windows](/windows-server/manage/windows-admin-center/deploy/install) , je na místním počítači pro správu (desktopový režim), ale můžete ho taky nainstalovat na server (režim služby).

Pokud nainstalujete centrum pro správu Windows na server, úlohy vyžadující CredSSP, jako je vytvoření clusteru a instalace aktualizací a rozšíření, vyžadují použití účtu, který je členem skupiny Správci brány na serveru centra pro správu systému Windows. Další informace najdete v prvním dvou částech [Konfigurace uživatelských Access Control a oprávnění](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions).

## <a name="prepare-hardware-for-deployment"></a>Příprava hardwaru pro nasazení

Po získání hardwaru serveru pro řešení Azure Stack HCI je čas ho zazálohovat a pak ho zapojte do racku. Pomocí následujících kroků Připravte serverový hardware pro nasazení operačního systému.

1. Zastojanujte všechny uzly serveru, které chcete použít v serverovém clusteru.
1. Připojte uzly serveru k síťovým přepínačům.
1. Nakonfigurujte systém BIOS nebo rozhraní UEFI (Unified Extensible Firmware Interface) (UEFI) serverů podle doporučení Azure Stack HCL pro výrobce hardwaru, aby se maximalizoval výkon a spolehlivost.

## <a name="operating-system-deployment-options"></a>Možnosti nasazení operačního systému

Operační systém Azure Stack HCI můžete nasadit stejným způsobem, jaký jste použili k nasazení jiných operačních systémů společnosti Microsoft:

- Předběžná instalace výrobce serveru.
- Bezobslužné nasazení pomocí souboru odpovědí.
- System Center Virtual Machine Manager (VMM).
- Nasazení sítě.
- Ruční nasazení připojením klávesnice a monitoru přímo k hardwaru serveru ve vašem datovém centru nebo připojením zařízení KVM k hardwaru serveru.

### <a name="server-manufacturer-pre-installation"></a>Předběžná instalace výrobce serveru

V případě podnikového nasazení Azure Stackho operačního systému HCI doporučujeme Azure Stack hardwaru integrovaných systémových řešení systému HCI od preferovaného hardwarového partnera. Hardware řešení dorazí na předinstalovaný operační systém a pomocí centra pro správu systému Windows nasaďte a aktualizujte ovladače a firmware od výrobce hardwaru.

V případě hardwarových řešení se rozsahy od 2 do 16 uzlů testuje a ověřuje i dodavatelé partnerů společnosti Microsoft a partnerů. Pokud chcete najít Azure Stack hardware řešení HCI od preferovaného hardwarového partnera, přečtěte si téma [Azure Stack v katalogu HCI](https://www.microsoft.com/cloud-platform/azure-stack-hci-catalog).

### <a name="headless-deployment"></a>Bezobslužné nasazení

K nasazení operačního systému bez periferních souborů můžete použít soubor odpovědí. Soubor odpovědí používá formát XML k definování konfiguračních nastavení a hodnot během bezobslužné instalace operačního systému.

Pro tuto možnost nasazení můžete použít Správce bitových kopií systému Windows k vytvoření souboru odpovědí unattend.xml k nasazení operačního systému na serverech. Správce bitových kopií systému Windows vytvoří soubor odpovědí bezobslužné instalace prostřednictvím grafického nástroje s oddíly komponenty k definování "odpovědí" na otázky konfigurace a pak zajistěte správný formát a syntaxi souboru.
Nástroj Windows System Image Manager je k dispozici v sadě Windows Assessment and Deployment Kit (Windows ADK). Začněte: Stáhněte si [a nainstalujte Windows ADK](/windows-hardware/get-started/adk-install).

### <a name="system-center-virtual-machine-manager-vmm-deployment"></a>Nasazení System Center Virtual Machine Manager (VMM)

System Center Virtual Machine Manager můžete použít k nasazení Azure Stack operačního systému HCI na holý hardware a také pro clusterování serverů. Další informace o nástroji VMM najdete v tématu [požadavky na systém pro System Center Virtual Machine Manager](/system-center/vmm/system-requirements).

Další informace o použití nástroje VMM k úplnému nasazení operačního systému najdete v tématu [zřízení hostitele nebo clusteru Hyper-V z holých počítačů](/system-center/vmm/hyper-v-bare-metal).

### <a name="network-deployment"></a>Nasazení sítě

Další možností je instalace operačního systému Azure Stack HCI přes síť pomocí [služby pro nasazení systému Windows](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831764(v=ws.11)).

### <a name="manual-deployment"></a>Ruční nasazení

Pokud chcete ručně nasadit Azure Stack operační systém HCI na systémovou jednotku každého serveru, který chcete seskupit do clusteru, nainstalujte operační systém přes upřednostňovanou metodu, například spuštění z disku DVD nebo USB. Dokončete proces instalace pomocí nástroje Konfigurace serveru (sconfig) a připravte servery na clusteringu. Další informace o tomto nástroji najdete v tématu [konfigurace instalace jádra serveru pomocí sconfig](/windows-server/get-started/sconfig-on-ws2016).

Ruční instalace Azure Stackho operačního systému HCI:
1. Spusťte Průvodce instalací Azure Stack HCL na systémové jednotce serveru, na který chcete nainstalovat operační systém.
1. Zvolte jazyk, který chcete nainstalovat, nebo přijměte výchozí nastavení jazyka, klikněte na tlačítko **Další** a potom na další stránce průvodce vyberte **Instalovat nyní**.

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-language.png" alt-text="Na stránce jazyk v Průvodci instalací rozhraní HCI pro instalaci Azure Stack.":::

1. Na stránce příslušné oznámení a licenční podmínky si přečtěte licenční podmínky, zaškrtněte políčko **Přijímám licenční podmínky** a potom vyberte **Další**.
1. Na jakém typu instalace chcete nainstalovat? Vyberte možnost **vlastní: Nainstalujte novější verzi Azure Stack pouze HCI (rozšířené)**.

    > [!NOTE]
    > Instalace upgradu se v této verzi operačního systému nepodporují.

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-which-type.png" alt-text="Stránka možností typu instalace v Průvodci instalací rozhraní HCI pro instalaci Azure Stack.":::

1. Na místě, kam chcete nainstalovat Azure Stack HCI? potvrďte umístění jednotky, kam chcete operační systém nainstalovat, nebo ho aktualizujte a pak vyberte **Další**.

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-where.png" alt-text="Stránka umístění jednotky v Průvodci instalací rozhraní HCI pro instalaci Azure Stack.":::

1. Na stránce instalace Azure Stack HCL se zobrazí stav pro zobrazení stavu procesu.

    :::image type="content" source="../media/operating-system/azure-stack-hci-installing.png" alt-text="Stavová stránka průvodce nainstalovat Azure Stack HCL.":::

    > [!NOTE]
    > Proces instalace pro dokončení procesu dvakrát restartuje operační systém a před otevřením příkazového řádku správce zobrazí oznámení o spuštění služeb.

1. Na příkazovém řádku správce vyberte **OK** , abyste změnili heslo uživatele před přihlášením k operačnímu systému a stisknutím klávesy ENTER.

    :::image type="content" source="../media/operating-system/azure-stack-hci-change-admin-password.png" alt-text="Výzva ke změně hesla.":::

1. Do příkazového řádku zadat nové přihlašovací údaje pro správce zadejte nové heslo, zadejte ho znovu a potvrďte ho a potom stiskněte klávesu ENTER.
1. Na výzvu k potvrzení změny hesla stiskněte klávesu ENTER.

    :::image type="content" source="../media/operating-system/azure-stack-hci-admin-password-changed.png" alt-text="Výzva k potvrzení změny hesla":::

Teď jste připraveni k provádění důležitých úkolů pomocí nástroje pro konfiguraci serveru (sconfig). Pokud chcete používat sconfig, přihlaste se k serveru, na kterém běží operační systém Azure Stack HCI. To může být lokálně prostřednictvím klávesnice a monitoru nebo pomocí řadiče pro vzdálenou správu (bezobslužné nebo BMC) nebo vzdálené plochy. Nástroj sconfig se automaticky otevře při přihlášení k serveru.

:::image type="content" source="../media/operating-system/azure-stack-hci-sconfig-screen.png" alt-text="Rozhraní nástroje pro konfiguraci serveru." lightbox="../media/operating-system/azure-stack-hci-sconfig-screen.png":::

Na hlavní stránce nástroje sconfig můžete provádět následující úlohy počáteční konfigurace:
- Nakonfigurujte sítě nebo ověřte, že se síť nakonfigurovali automaticky pomocí protokolu DHCP (Dynamic Host Configuration Protocol).
- Přejmenujte Server, pokud výchozí automaticky vygenerovaný název serveru neodpovídá vašemu.
- Připojte Server k doméně služby Active Directory.
- Přidejte účet uživatele domény nebo určenou skupinu domény do místních správců.
- Pokud plánujete Server spravovat mimo místní podsíť a teprve potom se rozhodnete, že se ještě nepřipojíte k doméně, povolte přístup k Vzdálená správa systému Windows (WinRM). (Výchozí pravidla brány firewall umožňují správu jak z místní podsítě, tak z jakékoli podsítě v rámci služby Active Directory Domain Services.)

Po nakonfigurování operačního systému podle potřeby pomocí nástroje sconfig na každém serveru jste připraveni pomocí Průvodce vytvořením clusteru v centru pro správu Windows clusterovat servery.

## <a name="next-steps"></a>Další kroky

Postup při provádění další úlohy správy související s tímto článkem najdete v následujících tématech:
> [!div class="nextstepaction"]
> [Vytvoření clusteru Azure Stack HCI](../deploy/create-cluster.md)
