---
title: Nasazení důvěryhodné virtualizace Enterprise v Azure Stack HCI
description: V tomto tématu najdete pokyny k plánování, konfiguraci a nasazení vysoce zabezpečené infrastruktury, která používá důvěryhodnou podnikovou virtualizaci v Azure Stack operační systém HCI.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/07/2021
ms.openlocfilehash: bd9675efc87929c020f3c1514d220fece54099a7
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254768"
---
# <a name="deploy-trusted-enterprise-virtualization-on-azure-stack-hci"></a>Nasazení důvěryhodné virtualizace Enterprise v Azure Stack HCI

>Platí pro: Azure Stack HCI, verze 20H2

V tomto tématu najdete pokyny k plánování, konfiguraci a nasazení vysoce zabezpečené infrastruktury, která používá důvěryhodnou podnikovou virtualizaci v Azure Stack operační systém HCI. Využijte své investice do Azure Stack HCI ke spouštění zabezpečených úloh na hardwaru, který používá [zabezpečení založené na virtualizaci (VBS)](/windows-hardware/design/device-experiences/oem-vbs) a hybridní cloudové služby prostřednictvím centra pro správu Windows a Azure Portal.

## <a name="overview"></a>Přehled
VBS je klíčovou součástí [investic do zabezpečení v Azure Stack HCL](/windows-server/get-started-19/whats-new-19#security) pro ochranu hostitelů a virtuálních počítačů před bezpečnostními hrozbami. Například [Příručka pro technické implementace zabezpečení (Stig)](https://nvd.nist.gov/ncp/checklist/914), která je publikována jako nástroj pro zlepšení zabezpečení informačních systémů oddělení obrany (DOD), uvádí v rámci obecných požadavků na zabezpečení seznamy vbs a [integrity kódu chráněného hypervisorem (hypervisorem hvci)](/windows-hardware/drivers/bringup/device-guard-and-credential-guard) . Pro ochranu úloh na virtuálních počítačích je nutné použít hostitelský hardware, který je povolený pro VBS a HYPERVISOREM HVCI, protože ohrožený hostitel nemůže zaručit ochranu virtuálního počítače.

VBS používá funkce virtualizace hardwaru k vytvoření a izolaci zabezpečené oblasti paměti z operačního systému. [Virtuální zabezpečený režim (VSM)](/virtualization/hyper-v-on-windows/tlfs/vsm) můžete v systému Windows použít k hostování řady řešení zabezpečení, která významně zvyšují ochranu před chybami zabezpečení operačního systému a škodlivými zneužitími.

VBS používá hypervisor Windows k vytváření a správě hranic zabezpečení v softwaru operačního systému, vymáhání omezení pro ochranu důležitých systémových prostředků a k ochraně prostředků zabezpečení, jako jsou například ověřené přihlašovací údaje uživatele. V případě nástroje VBS, i když malware získá přístup k jádru operačního systému, můžete významně omezit a obsahovat možná zneužití, protože hypervisor zabraňuje malwaru ve spouštění kódu nebo přístupu k tajným klíčům platformy.

Hypervisor, nejvyšší privilegovaná úroveň systémového softwaru, nastavuje a vynutila oprávnění stránky pro celou systémovou paměť. V VSM mohou být stránky spouštěny pouze po předání kontroly integrity kódu. I v případě ohrožení zabezpečení, například přetečení vyrovnávací paměti, které by mohlo způsobit pokus o změnu paměti malwarem, nelze změnit znakové stránky a nelze provést upravenou paměť. VBS a HYPERVISOREM HVCI významně posílí vynucování zásad integrity kódu. Všechny ovladače režimu jádra a binární soubory se zkontrolují předtím, než se můžou spustit, a nepodepsané ovladače nebo systémové soubory se nebudou moct načíst do systémové paměti.

## <a name="deploy-trusted-enterprise-virtualization"></a>Nasadit důvěryhodnou podnikovou virtualizaci
Tato část popisuje na vysoké úrovni, jak získat hardware pro nasazení vysoce zabezpečené infrastruktury, která používá důvěryhodnou podnikovou virtualizaci v Azure Stack HCI a centrum pro správu Windows pro správu.

### <a name="step-1-acquire-hardware-for-trusted-enterprise-virtualization-on-azure-stack-hci"></a>Krok 1: získání hardwaru pro důvěryhodnou podnikovou virtualizaci v Azure Stack HCI
Nejdřív budete muset zakoupit hardware. Nejjednodušší způsob, jak to udělat, je vyhledat preferovaného poskytovatele hardwaru Microsoftu v [katalogu Azure Stack HCI](https://hcicatalog.azurewebsites.net) a koupit integrovaný systém s předinstalovaným operačním systémem Azure Stack HCI. V katalogu můžete filtrovat, abyste viděli hardware od dodavatele optimalizovaného pro tento typ úlohy.

V opačném případě bude nutné nasadit operační systém Azure Stack HCI na svůj vlastní hardware. Podrobnosti o Azure Stack možností nasazení HCI a o instalaci centra pro správu systému Windows najdete v tématu [nasazení Azure Stack v operačním systému rozhraní HCI](./operating-system.md).

Dále pomocí centra pro správu systému Windows [vytvořte cluster Azure Stack HCI](./create-cluster.md).

Veškerý partnerský hardware pro Azure Stack HCI je certifikovaný s dodatečnou kvalifikací pro hardware Assurance. Proces kvalifikace testuje všechny požadované funkce [vbs](/windows-hardware/design/device-experiences/oem-vbs) . Nicméně VBS a HYPERVISOREM HVCI nejsou v Azure Stack HCL automaticky povolené. Další informace o další kvalifikaci k hardwarovému zabezpečení najdete v části "hardware Assurance" v části **systémy** v [katalogu Windows serveru](https://www.windowsservercatalog.com/content.aspx?ctf=AQinfo-systems.htm#:~:text=Hardware%20Assurance%20Windows%20Server%20systems%20that%20are%20awarded,of%20Windows%20Server%2C%20starting%20with%20Windows%20Server%202016).

   >[!WARNING]
   > HYPERVISOREM HVCI nemusí být kompatibilní s hardwarovými zařízeními, která nejsou uvedená v katalogu Azure Stack HCI. Důrazně doporučujeme používat pro důvěryhodnou infrastrukturu virtualizace rozlehlé sítě Azure Stack ověřený hardware od našich partnerů.

### <a name="step-2-enable-hvci"></a>Krok 2: povolení HYPERVISOREM HVCI
Povolte HYPERVISOREM HVCI na hardwaru serveru a virtuálních počítačích. Podrobnosti najdete v tématu [Povolení ochrany integrity kódu na základě virtualizace](/windows/security/threat-protection/device-guard/enable-virtualization-based-protection-of-code-integrity).

### <a name="step-3-set-up-azure-security-center-in-windows-admin-center"></a>Krok 3: nastavení Azure Security Center v centru pro správu Windows
V centru pro správu Windows nastavte [Azure Security Center](/azure/security-center/security-center-introduction) pro přidání ochrany před hrozbami a rychle vyhodnoťte stav zabezpečení vašich úloh.

Další informace najdete v tématu [Ochrana prostředků centra pro správu Windows pomocí Security Center](/azure/security-center/windows-admin-center-integration).

Jak začít s Security Center:
- Potřebujete předplatné Microsoft Azure. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free).
- Když buď navštívíte řídicí panel Azure Security Center ve Azure Portal nebo ho povolíte programově prostřednictvím rozhraní API, bude na všech vašich současných předplatných Azure povolená cenová úroveň Free Security Center.
Aby bylo možné využít pokročilé možnosti správy zabezpečení a detekce hrozeb, je nutné povolit Azure Defender. Azure Defender můžete používat po dobu 30 dnů zdarma. Další informace najdete v tématu [Security Center ceny](https://azure.microsoft.com/pricing/details/security-center).
- Pokud jste připraveni k povolení Azure Defenderu, přečtěte si [rychlý Start: nastavení Azure Security Center](/azure/security-center/security-center-get-started) , abyste provedli kroky.

Centrum pro správu systému Windows můžete použít také k nastavení dalších hybridních služeb Azure, jako je zálohování, Synchronizace souborů, Site Recovery, VPN typu Point-to-site, Update Management a Azure Monitor.

## <a name="next-steps"></a>Další kroky
Další informace související s důvěryhodnou podnikovou virtualizací najdete v těchto tématech:
- [Technologie Hyper-V v systému Windows Server](/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)