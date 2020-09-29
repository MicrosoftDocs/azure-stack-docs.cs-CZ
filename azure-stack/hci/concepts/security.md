---
title: Předpoklady zabezpečení Azure Stack HCI
description: Toto téma poskytuje pokyny týkající se zabezpečení pro Azure Stack operační systém HCI.
author: JohnCobb1
ms.author: v-johcob
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: 6a8a0c3068061c599ac18e160ebd32c7b5cc8eda
ms.sourcegitcommit: b147d617c32cea138b5bd4bab568109282e44317
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90010811"
---
# <a name="azure-stack-hci-security-considerations"></a>Předpoklady zabezpečení Azure Stack HCI

>Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

V tomto tématu jsou uvedeny požadavky na zabezpečení a doporučení týkající se Azure Stackho operačního systému HCI:
- Část 1 zahrnuje základní nástroje zabezpečení a technologie pro posílení operačního systému a ochranu dat a identit, aby bylo možné efektivně vytvořit zabezpečenou základ pro vaši organizaci.
- Část 2 pokrývá prostředky dostupné prostřednictvím Azure Security Center.
- Část 3 pokrývá pokročilejší požadavky na zabezpečení, aby bylo možné dále posílit stav zabezpečení vaší organizace v těchto oblastech.

## <a name="why-are-security-considerations-important"></a>Proč jsou důležité důležité informace o zabezpečení?
Zabezpečení má vliv na uživatele ve vaší organizaci na vyšší úroveň správy na informačního pracovníka. Nedostatečné zabezpečení je skutečné riziko pro organizace, protože porušení zabezpečení může mít za následek narušení všech normálních firem a zaznamenání vaší organizace na zastavení. Čím dřív můžete detekovat potenciální útok, tím rychleji můžete zmírnit případné ohrožení zabezpečení.

Po prohledání slabých bodů prostředí za účelem jejich zneužití může útočník obvykle během 24 až 48 hodin od počátečních oprávnění k eskalaci ohrožení převzít kontrolu nad systémy v síti. Dobrá bezpečnostní opatření posílí systémy v prostředí, aby rozšířily dobu, po kterou by útočník mohl převzít kontrolu z hodin na týdny nebo dokonce i po dobu blokování pohybů útočníka. Implementace doporučení zabezpečení v tomto tématu umožňuje vaší organizaci co nejrychleji zjistit a reagovat na tyto útoky.

## <a name="part-1-build-a-secure-foundation"></a>Část 1: Vytvoření zabezpečeného základu
V následujících částech jsou doporučené nástroje a technologie zabezpečení pro vytvoření zabezpečeného základu pro servery, na kterých běží operační systém Azure Stack HCI ve vašem prostředí.

### <a name="harden-the-environment"></a>Posílit zabezpečení prostředí
Tato část popisuje, jak chránit služby a virtuální počítače běžící v operačním systému:
- **Azure Stack s certifikací HCL certifikovaný hardware** poskytuje ve svém poli konzistentní zabezpečené spouštění, rozhraní UEFI a nastavení TPM. Kombinování zabezpečení založeného na virtualizaci a certifikovaného hardwaru pomáhá chránit úlohy citlivé na zabezpečení. Tato důvěryhodná infrastruktura se taky dá připojit k Azure Security Center, aby se aktivovaly funkce analýzy chování a vytváření sestav, které vám umožní rychle se měnící zatížení a hrozby.

    - *Zabezpečené spouštění* je bezpečnostní standard vyvinutý v oboru počítačů, který umožňuje zajistit, že se zařízení spouští jenom pomocí softwaru, který je důvěryhodný pro výrobce OEM (Original Equipment Manufacturer). Další informace najdete v tématu [Zabezpečené spouštění](/windows-hardware/design/device-experiences/oem-secure-boot).
    - *Rozhraní UEFI (United Extensible Firmware Interface)* řídí spouštěcí proces serveru a pak předá řízení buď systému Windows, nebo jinému operačnímu systému. Další informace najdete v tématu [požadavky na firmware rozhraní UEFI](/windows-hardware/design/device-experiences/oem-uefi).
    - Technologie *TPM (Trusted Platform Module)* poskytuje funkce související se zabezpečením založené na hardwaru. Čip TPM je zabezpečený kryptografický procesor, který generuje, ukládá a omezuje použití kryptografických klíčů. Další informace najdete v tématu [Přehled technologie Trusted Platform Module](/windows/security/information-protection/tpm/trusted-platform-module-overview).

    Další informace o Azure Stackch certifikovaných hardwarových zprostředkovatelů naleznete na webu [řešení Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/) .

- **Ochrana zařízení** a **ochrana Credential Guard**. Ochrana zařízení chrání před malwarem bez známého podpisu, nepodepsaného kódu a malwaru, který získává přístup k jádru pro zachycení citlivých informací nebo poškození systému. Ochrana Credential Guard v programu Windows Defender používá zabezpečení na základě virtualizace k izolaci tajných kódů, aby k nim měli přístup jenom privilegovaný systémový software.

    Další informace najdete v tématech [Správa ochrany přihlašovacích údajů v programu Windows Defender](/windows/security/identity-protection/credential-guard/credential-guard-manage) a stažení [nástroje Device Readiness](https://www.microsoft.com/en-us/download/details.aspx?id=53337)Guard pro ochranu před hardwarem.

- Aktualizace pro **Windows** a **firmware** jsou nezbytné v clusterech, serverech (včetně virtuálních počítačů hosta) a počítačích, které vám pomohou zajistit, aby byl operační systém i systémový hardware chráněn před útočníky. Pomocí nástroje **aktualizace** centra pro správu systému Windows můžete použít aktualizace pro jednotlivé systémy. Pokud váš poskytovatel hardwaru zahrnuje podporu centra pro správu systému Windows pro získání aktualizací ovladačů, firmwaru a řešení, můžete získat tyto aktualizace ve stejnou dobu jako aktualizace systému Windows, jinak je přímo od dodavatele získat.

    Další informace najdete v tématu [aktualizace clusteru](../manage/update-cluster.md).

    Pokud chcete spravovat aktualizace na několika clusterech a serverech najednou, zvažte přihlášení k odběru volitelné služby Azure Update Management, která je integrovaná s centrem pro správu systému Windows. Další informace najdete v tématu [Azure Update Management pomocí centra pro správu systému Windows](https://www.thomasmaurer.ch/2018/11/azure-update-management-windows-admin-center).

### <a name="protect-data"></a>Ochrana dat
Tato část popisuje, jak pomocí centra pro správu systému Windows chránit data a úlohy v operačním systému:

- **BitLocker pro prostory úložiště** chrání data v klidovém umístění. Nástroj BitLocker můžete použít k šifrování obsahu datových svazků prostorů úložiště v operačním systému. Použití nástroje BitLocker k ochraně dat může organizacím pomoci zajistit dodržování předpisů pro státní, regionální a průmyslové standardy, jako je FIPS 140-2 nebo HIPAA.
 
    Další informace o používání BitLockeru v centru pro správu Windows najdete v tématu [povolení šifrování svazků, odstraňování duplicit a komprimace](../manage/volume-encryption-deduplication.md) .

- Šifrování **protokolu SMB** pro sítě Windows chrání data při přenosu. *SMB (Server Message Block)* je protokol pro sdílení souborů v síti, který umožňuje aplikacím na počítači číst soubory a zapisovat do nich a požadovat služby od serverových programů v počítačové síti.

    Pokud chcete povolit šifrování protokolu SMB, přečtěte si téma [vylepšení zabezpečení protokolu SMB](/windows-server/storage/file-server/smb-security).

- **Antivirová ochrana v programu Windows Defender** v centru pro správu Windows chrání operační systém na klientech a serverech před viry, malwarem, spywarem a dalšími hrozbami. Další informace najdete v tématu [antivirová ochrana v programu Microsoft Defender v systému Windows Server 2016 a 2019](/windows/security/threat-protection/microsoft-defender-antivirus/microsoft-defender-antivirus-on-windows-server-2016).

### <a name="protect-identities"></a>Ochrana identit
Tato část popisuje, jak pomocí centra pro správu systému Windows chránit privilegované identity:

- **Řízení přístupu** může zlepšit zabezpečení vaší krajiny pro správu. Pokud používáte server centra pro správu Windows (vs. běží na počítači s Windows 10), můžete řídit dvě úrovně přístupu přímo do centra pro správu Windows: uživatelé brány a správci brány. Mezi možnosti zprostředkovatele identity Správce brány patří:
    - Skupiny služby Active Directory nebo místní počítač pro vymáhání ověřování pomocí čipové karty
    - Azure Active Directory k vykonání podmíněného přístupu a vícefaktorového ověřování.
 
    Další informace najdete v tématu [Možnosti uživatelského přístupu v centru pro správu Windows](/windows-server/manage/windows-admin-center/plan/user-access-options) a [Konfigurace uživatelských Access Control a oprávnění](/windows-server/manage/windows-admin-center/configure/user-access-control).

- **Provoz prohlížeče** v centru pro správu systému Windows používá protokol HTTPS. Provoz z centra pro správu Windows na spravované servery používá standardní PowerShell a rozhraní WMI (Windows Management Instrumentation) (WMI) přes Vzdálená správa systému Windows (WinRM). Centrum pro správu systému Windows podporuje řešení hesla místního správce (LAPS), omezené delegování založené na prostředku, řízení přístupu brány pomocí služby Active Directory (AD) nebo Microsoft Azure Active Directory (Azure AD) a řízení přístupu na základě role (RBAC) pro správu cílových serverů.

    Centrum pro správu Windows podporuje Microsoft Edge (Windows 10, verze 1709 nebo novější), Google Chrome a Microsoft Edge Insider ve Windows 10. Centrum pro správu Windows můžete nainstalovat buď na počítač s Windows 10, nebo na Windows Server.

    Pokud nainstalujete centrum pro správu Windows na server, který spouští jako bránu, bez uživatelského rozhraní na hostitelském serveru. V tomto scénáři se správci mohou přihlásit k serveru prostřednictvím relace HTTPS zabezpečeného certifikátem zabezpečení podepsanému svým držitelem na hostiteli. Je ale lepší použít pro proces přihlašování vhodný certifikát SSL od důvěryhodné certifikační autority, protože podporované prohlížeče považují připojení podepsané svým držitelem jako nezabezpečené, i když je připojení k místní IP adrese přes důvěryhodnou síť VPN.

    Další informace o možnostech instalace vaší organizace najdete v tématu [jaký typ instalace je pro vás nejvhodnější?](/windows-server/manage/windows-admin-center/plan/installation-options).

- **CredSSP** je poskytovatel ověřování, který centrum pro správu systému Windows používá v několika případech k předání přihlašovacích údajů do počítačů nad rámec konkrétního serveru, na který cílíte na správu. Centrum pro správu systému Windows v současnosti vyžaduje zprostředkovatele CredSSP pro:
    - Vytvořte nový cluster.
    - Přístup k nástroji **Updates** pro použití funkce clusteringu s podporou převzetí služeb při selhání nebo aktualizace pro clustery.
    - Spravujte na virtuálních počítačích neagregované úložiště SMB.

    Další informace najdete v tématu o tom, jak [Centrum pro správu Windows používá CredSSP?](/windows-server/manage/windows-admin-center/understand/faq#does-windows-admin-center-use-credssp)

- **Řízení přístupu na základě role (RBAC)** v centru pro správu systému Windows umožňuje uživatelům omezený přístup k serverům, které potřebují ke správě, a nikoli k tomu, aby je nemuseli používat pro úplné místní správce. Pokud chcete použít RBAC v centru pro správu Windows, nakonfigurujete každý spravovaný server pomocí PowerShellu, který je právě dostatečným koncovým bodem správy.

    Další informace najdete v tématu [řízení přístupu na základě role](/windows-server/manage/windows-admin-center/plan/user-access-options#role-based-access-control) a [jen dostatečná Správa](/powershell/scripting/learn/remoting/jea/overview?view=powershell-7).

- **Nástroje zabezpečení** v centru pro správu systému Windows, které můžete použít ke správě a ochraně identit, zahrnují službu Active Directory, certifikáty, bránu firewall, místní uživatele a skupiny a další.

    Další informace najdete v tématu [Správa serverů pomocí centra pro správu systému Windows](/windows-server/manage/windows-admin-center/use/manage-servers).

## <a name="part-2-use-azure-security-center"></a>Část 2: použití Azure Security Center
*Azure Security Center* je jednotný systém pro správu zabezpečení infrastruktury, který posílí stav zabezpečení vašich datových center a poskytuje rozšířenou ochranu před hrozbami napříč vašimi hybridními úlohami v cloudu i místně. Security Center poskytuje nástroje k vyhodnocení stavu zabezpečení vaší sítě, ochraně úloh, vyvolávání výstrah zabezpečení a dodržování specifických doporučení k nápravě útoků a řešení budoucích hrozeb. Security Center provádí všechny tyto služby s vysokou rychlostí v cloudu bez režie nasazení prostřednictvím automatického zřizování a ochrany se službami Azure.

Security Center chrání virtuální počítače pro servery Windows i Linux servery instalací agenta Log Analytics na tyto prostředky. Azure koreluje události, které agenti shromažďují do doporučení (posílení úloh), které provádíte, aby vaše úlohy byly zabezpečené. Úlohy posílení zabezpečení založené na osvědčených postupech zabezpečení zahrnují správu a vynucování zásad zabezpečení. Pak můžete sledovat výsledky a spravovat dodržování předpisů a zásad správného řízení prostřednictvím Security Center monitorování a zároveň snížit plochu pro útok napříč všemi vašimi prostředky.

Správa přístupu uživatelů k prostředkům a předplatným Azure představuje důležitou součást strategie zásad správného řízení Azure. Hlavní metodou správy přístupu v Azure je řízení přístupu na základě role (RBAC). Další informace najdete v tématu [Správa přístupu k prostředí Azure pomocí řízení přístupu na základě role](/azure/cloud-adoption-framework/ready/azure-setup-guide/manage-access).

Práce s Security Center v centru pro správu Windows vyžaduje předplatné Azure. Informace o tom, jak začít, najdete v tématu [integrace Azure Security Center s centrem pro správu systému Windows](/azure/security-center/windows-admin-center-integration).

Po registraci, přístup k Security Center v centru pro správu Windows: na stránce **všechna připojení** vyberte server nebo virtuální počítač, v části **nástroje**vyberte **Azure Security Center**a pak vyberte **Přihlásit se k Azure**.

Další informace najdete v tématu [co je Azure Security Center?](/azure/security-center/security-center-intro)

## <a name="part-3-add-advanced-security"></a>Část 3: Přidání rozšířeného zabezpečení
V následujících částech jsou doporučené rozšířené nástroje zabezpečení a technologie pro další posílení serverů, na kterých běží operační systém Azure Stack HCI ve vašem prostředí.

### <a name="harden-the-environment"></a>Posílit zabezpečení prostředí
- **Základní hodnoty zabezpečení Microsoftu** jsou založené na doporučeních zabezpečení od Microsoftu získaných prostřednictvím partnerství s komerčními organizacemi a vládou USA, jako je Ministerstvo obrany. Standardní hodnoty zabezpečení zahrnují doporučené nastavení zabezpečení pro bránu Windows Firewall, program Windows Defender a mnoho dalších.

    Směrné plány zabezpečení jsou k dispozici jako zálohy objektů Zásady skupiny objektů (GPO), které můžete importovat do Active Directory Domain Services (služba AD DS), a potom je nasadit na servery připojené k doméně za účelem posílení prostředí. Pomocí nástrojů pro místní skripty můžete také nakonfigurovat samostatné servery (nepřipojené k doméně) pomocí standardních hodnot zabezpečení. Pokud chcete začít používat standardní hodnoty zabezpečení, Stáhněte si [sadu Microsoft Security Security Toolkit 1,0](https://www.microsoft.com/download/details.aspx?id=55319).

    Další informace najdete v tématu [základní údaje o zabezpečení společnosti Microsoft](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines).

### <a name="protect-data"></a>Ochrana dat
- **Posílení zabezpečení prostředí Hyper-V** vyžaduje posílení zabezpečení Windows serveru běžícího na virtuálním počítači stejně, jako byste zjednodušili operační systém běžící na fyzickém serveru. Vzhledem k tomu, že virtuální prostředí mají obvykle více virtuálních počítačů sdílejících stejný fyzický hostitel, je nezbytné chránit jak fyzického hostitele, tak i virtuální počítače, které jsou v něm spuštěné. Útočník, který ohrožuje hostitele, může ovlivnit více virtuálních počítačů s větším dopadem na úlohy a služby. Tato část pojednává o následujících metodách, které můžete použít k posílení Windows serveru v prostředí Hyper-V:

    - **Chráněné prostředky infrastruktury a stíněné virtuální počítače** posílí zabezpečení virtuálních počítačů, které běží v prostředích Hyper-V, tím, že útočníkům brání v úpravách souborů virtuálního počítače. *Chráněné prostředky infrastruktury* se skládají ze služby strážce hostitele (HGS), což je obvykle cluster tří uzlů, jeden nebo více chráněných hostitelů a sada chráněných virtuálních počítačů. Služba ověření identity vyhodnocuje platnost žádostí hostitelů, zatímco služba ochrany klíčů určuje, jestli se mají vydávat klíče, které můžou chráněné hostitele použít ke spuštění stíněného virtuálního počítače.

        Další informace najdete v tématu [Přehled chráněných prostředků infrastruktury a chráněných virtuálních počítačů](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms).
     
     - **Virtual Trusted Platform Module (vTPM)** ve Windows serveru podporuje čip TPM pro virtuální počítače, který umožňuje používat pokročilé technologie zabezpečení, jako je třeba BitLocker na virtuálních počítačích. Podporu čipu TPM můžete povolit pro libovolný virtuální počítač Hyper-V generace 2 pomocí Správce technologie Hyper-V nebo `Enable-VMTPM` rutiny prostředí Windows PowerShell.
     
        Další informace najdete v tématu [Enable-VMTPM](/powershell/module/hyper-v/enable-vmtpm?view=win10-ps).
     
     - **Softwarově definované sítě (SDN)** v Azure Stack HCI a Windows Server centrálně konfigurují a spravují fyzická a virtuální síťová zařízení, jako jsou směrovače, přepínače a brány ve vašem datovém centru. Prvky virtuální sítě, jako je virtuální přepínač Hyper-V, virtualizace sítě Hyper-V a brána služby RAS, jsou navržené tak, aby byly integrálními prvky vaší infrastruktury SDN.

        Další informace najdete v tématu [softwarově definované sítě (SDN)](/windows-server/networking/sdn/).

### <a name="protect-identities"></a>Ochrana identit
- **Řešení hesel místního správce (LAPS)** je zjednodušený mechanismus pro systémy připojené k doméně služby Active Directory, který pravidelně nastavuje místní heslo účtu správce počítače na novou náhodnou a jedinečnou hodnotu. Hesla se ukládají do zabezpečeného důvěrného atributu u odpovídajícího objektu počítače ve službě Active Directory, kde je můžou načítat jenom oprávnění uživatelé. LAPS používá místní účty pro vzdálenou správu počítačů způsobem, který nabízí některé výhody při používání doménových účtů. Další informace najdete v tématu [vzdálené použití místních účtů: LAPS mění vše](/archive/blogs/secguide/remote-use-of-local-accounts-laps-changes-everything).

    Pokud chcete začít používat LAPS, Stáhněte si [řešení hesla místního správce (LAPS)](https://www.microsoft.com/download/details.aspx?id=46899).

- **Microsoft Advanced Threat Analytics (ATA)** je místní produkt, který můžete použít k detekci útočníků, kteří se pokoušejí ohrozit privilegované identity. ATA analyzuje síťový provoz pro protokoly ověřování, autorizace a shromažďování informací, jako jsou Kerberos a DNS. ATA používá data k vytváření profilů chování uživatelů a dalších entit v síti za účelem detekce anomálií a známých vzorů útoku.
    
    Další informace najdete v tématu [co je Advanced Threat Analytics?](/advanced-threat-analytics/what-is-ata).

- Ochrana před vzdálenými **přihlašovacími údaji v programu Windows Defender** chrání přihlašovací údaje přes připojení ke vzdálené ploše přesměrováním požadavků protokolu Kerberos zpět na zařízení, které žádá o připojení. Poskytuje taky jednotné přihlašování (SSO) pro relace vzdálené plochy. Pokud dojde k ohrožení zabezpečení cílového zařízení v relaci vzdálené plochy, vaše přihlašovací údaje se nezveřejňují, protože pověření i deriváty přihlašovacích údajů se přes síť nikdy předávají cílovému zařízení.

    Další informace najdete v tématu [Správa ochrany přihlašovacích údajů v programu Windows Defender](/windows/security/identity-protection/credential-guard/credential-guard-manage).

## <a name="next-steps"></a>Další kroky
Další informace o dodržování předpisů týkajících se zabezpečení a dodržování předpisů najdete zde:
- [Zabezpečení a záruky](/windows-server/security/security-and-assurance)
- [Osvědčené postupy zabezpečení pro řešení Azure](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/)
