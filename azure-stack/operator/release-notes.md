---
title: Poznámky k verzi centra Azure Stack
description: Poznámky k verzi pro integrované systémy Azure Stack hub, včetně aktualizací a oprav chyb.
author: sethmanheim
ms.topic: article
ms.date: 02/03/2020
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 11/22/2019
ms.openlocfilehash: 40c727c97035b0f843ce84b0eafad47c77560938
ms.sourcegitcommit: 3974fc544986a5eccfac0ba7c4441b05279ae226
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "77001637"
---
# <a name="azure-stack-hub-release-notes"></a>Poznámky k verzi centra Azure Stack

Tento článek popisuje obsah balíčků aktualizací centra Azure Stack. Tato aktualizace zahrnuje vylepšení a opravy pro nejnovější vydání centra Azure Stack.

Pokud chcete získat přístup k poznámkám k verzi pro jinou verzi, použijte rozevírací nabídku selektor verzí nad obsahem vlevo.

::: moniker range=">=azs-1906"
> [!IMPORTANT]  
> Tento balíček aktualizace je určen pouze pro integrované systémy Azure Stack hub. Nepoužívejte tento balíček aktualizace na Azure Stack Development Kit (ASDK).
::: moniker-end
::: moniker range="<azs-1906"
> [!IMPORTANT]  
> Pokud je vaše instance centra Azure Stack za více než dvěma aktualizacemi, je považována za nedodržující předpisy. Aby bylo možné [získat podporu, musíte aktualizovat aspoň minimální podporovanou verzi](azure-stack-servicing-policy.md#keep-your-system-under-support).
::: moniker-end

## <a name="update-planning"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Známé problémy](known-issues.md)
- [Aktualizace zabezpečení](release-notes-security-updates.md)
- [Kontrolní seznam aktivit před a po instalaci aktualizace](release-notes-checklist.md)

Nápovědu k řešení potíží s aktualizacemi a procesu aktualizace najdete v tématu řešení potíží se [opravami a aktualizacemi pro centrum Azure Stack](azure-stack-updates-troubleshoot.md).

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-1910"
## <a name="1910-build-reference"></a>1910 – odkaz na sestavení

Číslo buildu aktualizace centra Azure Stack 1910 je **1.1910.0.58**.

### <a name="update-type"></a>Typ aktualizace

Počínaje 1908 se příslušný operační systém, na kterém Azure Stack centra spouští, aktualizoval na Windows Server 2019. Tato aktualizace umožňuje základní základní vylepšení a možnost přinášet další možnosti do centra Azure Stack.

Typ sestavení aktualizace centra Azure Stack 1910 je **Express**.

Balíček aktualizace 1910 je v porovnání s předchozími aktualizacemi větší, což má za následek delší dobu stahování. Tato aktualizace zůstane ve stavu **Příprava** pro dlouhou dobu a operátoři můžou očekávat, že tento proces trvá déle než předchozí aktualizace. Očekávaná doba dokončení aktualizace 1910 je přibližně 10 hodin, bez ohledu na počet fyzických uzlů ve vašem prostředí Azure Stack hub. Přesné běhové moduly pro aktualizaci jsou obvykle závislé na kapacitě používané v systému podle zatížení klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a specifikacemi hardwaru systému. Běhové moduly trvající déle než očekávaná hodnota nejsou běžné a nevyžadují akci Azure Stack operátory centra, pokud se aktualizace nezdařila. Tato přibližná doba běhu je specifická pro aktualizaci 1910 a neměla by se porovnávat s jinými aktualizacemi centra Azure Stack.

Další informace o typech sestavení aktualizací najdete v tématu [Správa aktualizací v centru Azure Stack](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novinky

<!-- What's new, also net new experiences and features. -->

- Portál pro správu nyní zobrazuje IP adresy privilegovaného koncového bodu v nabídce Vlastnosti oblasti pro snazší zjišťování. Kromě toho zobrazuje aktuální nakonfigurovaný časový server a servery DNS pro přeposílání. Další informace najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](azure-stack-privileged-endpoint.md).

- Pokud dojde k chybě, může teď systém monitorování stavu a monitorování centra Azure Stack vyvolat výstrahy pro různé hardwarové součásti. Tyto výstrahy vyžadují další konfiguraci. Další informace najdete v tématu [monitorování hardwarových komponent centra Azure Stack](azure-stack-hardware-monitoring.md).

- [Podpora Cloud-init pro Azure Stack hub](/azure/virtual-machines/linux/using-cloud-init): Cloud-init je široce používaný přístup k přizpůsobení virtuálního počítače se systémem Linux při prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Protože cloud-init je volána v průběhu procesu prvotního spuštění, nejsou žádné další kroky ani agenty vyžaduje použití vaší konfigurace. Image Ubuntu na webu Marketplace byly aktualizovány tak, aby podporovaly Cloud-init pro zřizování.

- Centrum Azure Stack nyní podporuje všechny verze agenta Windows Azure Linux jako Azure.

- K dispozici je nová verze modulů PowerShellu pro správce centra Azure Stack. <!-- For more information, see -->

- Přidání rutiny **set-AzSDefenderManualUpdate** do privilegovaného koncového bodu (PEP) ke konfiguraci ruční aktualizace definicí programu Windows Defender v infrastruktuře centra Azure Stack. Další informace najdete v tématu [aktualizace antivirové ochrany v programu Windows Defender v centru Azure Stack](azure-stack-security-av.md).

- Přidání rutiny **Get-AzSDefenderManualUpdate** do privilegovaného koncového bodu (PEP), který načte konfiguraci ruční aktualizace definicí programu Windows Defender v infrastruktuře centra Azure Stack. Další informace najdete v tématu [aktualizace antivirové ochrany v programu Windows Defender v centru Azure Stack](azure-stack-security-av.md).

- Přidání rutiny **set-AzSDnsForwarder** do privilegovaného koncového bodu (PEP) pro změnu nastavení serveru DNS pro server DNS v centru Azure Stack. Další informace o konfiguraci DNS najdete v tématu [Azure Stack integrace služby DNS centra Datacenter](azure-stack-integrate-dns.md).

- Přidání rutiny **Get-AzSDnsForwarder** do privilegovaného koncového bodu (PEP), aby se načetlo nastavení serveru DNS pro servery DNS v centru Azure Stack. Další informace o konfiguraci DNS najdete v tématu [Azure Stack integrace služby DNS centra Datacenter](azure-stack-integrate-dns.md).

- Přidání podpory pro správu **clusterů Kubernetes** pomocí [modulu AKS](../user/azure-stack-kubernetes-aks-engine-overview.md) Od této aktualizace můžou zákazníci nasazovat produkční clustery Kubernetes. Modul AKS umožňuje uživatelům provádět tyto akce:
  - Spravujte životní cyklus svých clusterů Kubernetes. Můžou vytvářet, aktualizovat a škálovat clustery.
  - Spravujte své clustery pomocí spravovaných imagí vyprodukovaných AKS a týmy centra Azure Stack.
  - Využijte poskytovatele cloudu Kubernetes integrovaného s Azure Resource Manager, který sestaví clustery s využitím nativních prostředků Azure.
  - Nasaďte a spravujte svoje clustery v připojených nebo odpojených Azure Stackch razítkech rozbočovače.
  - Použití hybridních funkcí Azure:
    - Integrace se službou Azure ARC.
    - Integrace s Azure Monitor pro kontejnery.
  - Používejte kontejnery Windows s modulem AKS.
  - Podpora šablon stylů CSS a inženýrství pro jejich nasazení.

### <a name="improvements"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Centrum Azure Stack zlepšilo svou schopnost automaticky opravovat některé aktualizace a problémy s aktualizací, které dříve způsobily chyby aktualizace, nebo brání operátorům zahájit Azure Stack aktualizace centra. Výsledkem je, že ve skupině **test-AzureStack-UpdateReadiness** je k dispozici méně testů. Další informace najdete v tématu [ověření stavu systému centra Azure Stack](azure-stack-diagnostic-test.md#groups). Ve skupině **UpdateReadiness** zůstanou tyto tři testy:

  - **AzSInfraFileValidation**
  - **AzSActionPlanStatus**
  - **AzsStampBMCSummary**

- Přidalo se pravidlo auditování, které oznamuje, že externí zařízení (například klíč USB) je připojené k uzlu infrastruktury centra Azure Stack. Protokol auditu se vysílá přes protokol syslog a zobrazí se jako **Microsoft-Windows-Security-audit: 6416 | Technologie Plug and Play události** Další informace o tom, jak nakonfigurovat klienta syslog, najdete v tématu [předávání SYSLOG](azure-stack-integrate-security.md).

- Rozbočovač Azure Stack se přesouvá do 4096 klíčů RSA pro vnitřní certifikáty. Spuštění interního rotace tajných klíčů nahradí staré 2048 certifikátů pomocí 4096 bitů dlouhých certifikátů. Další informace o rotaci tajných kódů v centru Azure Stack najdete v tématu [otočení tajných kódů v centru Azure Stack](azure-stack-rotate-secrets.md).

- Upgrady na složitost kryptografických algoritmů a složitosti klíčů pro několik interních součástí, aby byly v rozporu s výborem pro národní systémy zabezpečení – zásady 15 (CNSSP-15), které poskytují osvědčené postupy pro používání veřejných standardů pro zabezpečení sdílení informací. Mezi vylepšeními je AES256 ověřování protokolem Kerberos a SHA384 pro šifrování pomocí sítě VPN. Další informace o CNSSP-15 najdete na stránce věnované [národním bezpečnostním systémům a zásadám](http://www.cnss.gov/CNSS/issuances/Policies.cfm).

- Z důvodu výše uvedeného upgradu Azure Stack hub teď má nové výchozí hodnoty pro konfigurace protokolu IPsec/IKEv2. Nové výchozí hodnoty, které se používají na straně centra Azure Stack, jsou následující:

   **Parametry protokolu IKE fáze 1 (hlavní režim)**

   | Vlastnost              | Hodnota|
   |-|-|
   | Verze IKE           | IKEv2 |
   |Skupina Diffie-Hellman   | ECP384 |
   | Metoda ověřování | Předsdílený klíč |
   |Algoritmy šifrování a hash | AES256, SHA384 |
   |Životnost SA (čas)     | 28 800 sekund|

   **Parametry protokolu IKE fáze 2 (rychlý režim)**

   | Vlastnost| Hodnota|
   |-|-|
   |Verze IKE |IKEv2 |
   |Šifrování & algoritmy hash (šifrování)     | GCMAES256|
   |Šifrování šifrovacích & algoritmů hash (ověřování) | GCMAES256|
   |Životnost SA (čas)  | 27 000 sekund  |
   |Životnost SA (kilobajty) | 33 553 408     |
   |Metoda Perfect Forward Secrecy (PFS) | ECP384 |
   |Detekce mrtvých partnerských zařízení | Podporováno|

   Tyto změny se projeví také ve výchozí dokumentaci k [návrhu protokolu IPSec/IKE](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters) .

- Služba infrastruktura zálohování vylepšuje logiku, která počítá požadované místo na zálohování místo spoléhání na pevnou prahovou hodnotu. Služba bude používat velikost zálohování, zásady uchovávání informací, rezervu a aktuální využití externího umístění úložiště k určení, jestli se má pro operátor vyvolat upozornění.

### <a name="changes"></a>Změny

- Při stahování položek z webu Marketplace z Azure do centra Azure Stack je k dispozici nové uživatelské rozhraní, které umožňuje zadat verzi položky, pokud existuje více verzí. Nové uživatelské rozhraní je k dispozici v rámci připojených i odpojených scénářů. Další informace najdete v tématu [stažení položek Marketplace z Azure do centra Azure Stack](azure-stack-download-azure-marketplace-item.md).  

- Počínaje verzí 1910 **vyžaduje** systém Azure Stack hub další/20 privátní interní IP místo. Tato síť je soukromá pro systém Azure Stack hub a je možné ji znovu použít na více systémech centra Azure Stack v rámci vašeho datového centra. I když je síť soukromá pro Azure Stack hub, nesmí se překrývat se sítí ve vašem datovém centru. Privátní IP adresa/20 je rozdělená do několika sítí, které umožňují provozování infrastruktury centra Azure Stack v kontejnerech (jak je uvedeno výše v [poznámkách k verzi 1905](release-notes.md?view=azs-1905)). Cílem provozování infrastruktury centra Azure Stack v kontejnerech je optimalizace využití a zvýšení výkonu. Kromě toho privátní IP místo/20 slouží také k povolení průběžného úsilí, které před nasazením omezí požadovaný adresní prostor IP adres.

  - Upozorňujeme, že vstup/20 slouží jako předpoklad pro příští aktualizaci centra Azure Stack po 1910. Když se další aktualizace centra Azure Stack po 1910 vydání a pokusíte se ji nainstalovat, aktualizace se nezdaří, pokud jste nedokončili vstup/20, jak je popsáno v následujících krocích oprav. V portálu pro správu bude k dispozici výstraha, dokud výše uvedené kroky nápravy nejsou dokončeny. V článku věnovaném [integraci sítě Datacenter](azure-stack-network.md#private-network) se dozvíte, jak bude tento nový privátní prostor použit.

  - Postup nápravy: Pokud chcete problém vyřešit, postupujte podle pokynů a [otevřete relaci PEP](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Připravte velikost [privátního interního rozsahu IP adres](azure-stack-network.md#logical-networks) /20 a v relaci PEP spusťte následující rutinu (k dispozici pouze počínaje 1910) pomocí následujícího příkladu: `Set-AzsPrivateNetwork -UserSubnet 100.87.0.0/20`. Pokud se operace provede úspěšně, obdržíte do **Konfigurace přidaný rozsah interní sítě AZS**. Po úspěšném dokončení se výstraha zavře na portálu pro správu. Systém centra Azure Stack se teď může aktualizovat na další verzi.
  
- Služba zálohování infrastruktury odstraní částečně nahraná zálohovaná data v případě, že umístění externího úložiště během procesu nahrávání vyčerpá kapacitu.  

- Služba infrastruktura zálohování přidá službu identit do datové části zálohy pro nasazení AAD.  

- Modul PowerShellu centra Azure Stack se aktualizoval na verzi 1.8.0 pro verzi 1910.<br>Změny zahrnují:
   - **Nový modul Správce DRP**: poskytovatel prostředků nasazení (DRP) umožňuje Orchestrované nasazení poskytovatelů prostředků do centra Azure Stack. Tyto příkazy komunikují s Azure Resource Managerou vrstvou a komunikují s DRP.
   - **BRP**: <br />
           – Podporuje obnovení jedné role pro zálohování infrastruktury Azure Stack. <br />
           – Přidejte parametr `RoleName` do `Restore-AzsBackup`rutiny.
   - **FRP**: zásadní změny prostředků **jednotky** a **svazku** pomocí rozhraní API verze `2019-05-01`. Funkce jsou podporovány centrem Azure Stack 1910 a novějším: <br />
            -Byla změněna hodnota `ID`, `Name`, `HealthStatus`a `OperationalStatus`. <br />
            – Podporované nové vlastnosti `FirmwareVersion`, `IsIndicationEnabled`, `Manufacturer`a `StoragePool` pro prostředky **jednotky** . <br />
            -Vlastnosti `CanPool` a `CannotPoolReason` prostředků **jednotky** jsou zastaralé; místo toho použijte `OperationalStatus`.

### <a name="fixes"></a>Opravy

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->

- Opravili jsme problém, který zabránil vynucování zásad TLS 1,2 v prostředích nasazených před vydáním Azure Stack centra 1904.
- Opravili jsme problém, kdy se virtuální počítač s Ubuntu 18,04 vytvořený s povoleným autorizací SSH neumožňuje používat klíče SSH k přihlášení.
- **Heslo pro resetování** se odebralo z uživatelského rozhraní sady škálování virtuálního počítače.
- Opravili jsme problém, kdy odstranění nástroje pro vyrovnávání zatížení z portálu nezpůsobilo odstranění objektu ve vrstvě infrastruktury.
- Opravili jsme problém, který ukázal nepřesné procento výstrahy využití fondu brány na portálu pro správu.
<!-- Fixed an issue where adding more than one public IP on the same NIC on a Virtual Machine resulted in internet connectivity issues. Now, a NIC with two public IPs should work as expected.[This fix actually didn't go in 1910 due to build issues, commenting out until next build (2002) ] -->

## <a name="security-updates"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci centra Azure Stack najdete v tématu [aktualizace zabezpečení centra Azure Stack](release-notes-security-updates.md).

## <a name="update-planning"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Známé problémy](known-issues.md)
- [Aktualizace zabezpečení](release-notes-security-updates.md)
- [Kontrolní seznam aktivit před a po instalaci aktualizace](release-notes-checklist.md)

## <a name="download-the-update"></a>Stáhnout aktualizaci.

Balíček aktualizace centra Azure Stack 1910 můžete stáhnout ze [stránky pro stažení centra Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack centrum pravidelně vydává opravy hotfix. Před aktualizací centra Azure Stack na 1910 se ujistěte, že jste nainstalovali nejnovější opravu hotfix centra Azure Stack pro 1908.

Opravy hotfix centra Azure Stack se vztahují pouze na integrované systémy Azure Stack hub. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="prerequisites-before-applying-the-1910-update"></a>Požadavky: před instalací aktualizace 1910

Verze 1910 centra Azure Stack se musí použít ve verzi 1908 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [1.1908.14.53 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4537661)

### <a name="after-successfully-applying-the-1910-update"></a>Po úspěšné instalaci aktualizace 1910

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [1.1910.17.95 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4537833)
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-build-reference"></a>1908 – odkaz na sestavení

Číslo buildu aktualizace centra Azure Stack 1908 je **1.1908.4.33**.

### <a name="update-type"></a>Typ aktualizace

V 1908 se základní operační systém, na kterém Azure Stack centra spouští, aktualizoval na Windows Server 2019. Tato aktualizace umožňuje základní základní vylepšení a možnost přinášet další možnosti do centra Azure Stack.

Typ sestavení aktualizace centra Azure Stack 1908 je **plný**. V důsledku toho má aktualizace 1908 delší dobu běhu než expresní aktualizace jako 1906 a 1907. Přesné moduly runtime pro úplné aktualizace obvykle závisí na počtu uzlů, které vaše instance centra Azure Stack obsahuje, na kapacitě používané v systému podle úloh klientů, na síťovém připojení vašeho systému (Pokud je připojené k Internetu) a na vašem systémovém hardwaru. rozšířeného. Aktualizace 1908 měla během interního testování následující očekávané moduly runtime: 4 uzly-42 hodiny, 8 uzlů – 50 hodin, 12 uzlů – 60 hodiny, 16 uzlů – 70 hodin. Aktualizační moduly, které trvají déle než tyto očekávané hodnoty, nejsou Neběžné a nevyžadují akci Azure Stack operátory centra, pokud se aktualizace nezdařila.

Další informace o typech sestavení aktualizací najdete v tématu [Správa aktualizací v centru Azure Stack](azure-stack-updates.md).

- Přesné běhové moduly pro aktualizaci jsou obvykle závislé na kapacitě používané systémem v rámci úloh klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a hardwarové konfiguraci systému.
- Běhové moduly trvající déle než neočekávané jsou Neběžné a nevyžadují akci Azure Stack operátory centra, pokud se aktualizace nezdařila.
- Tato přibližná doba běhu je specifická pro aktualizaci 1908 a neměla by se porovnávat s jinými aktualizacemi centra Azure Stack.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novinky

<!-- What's new, also net new experiences and features. -->

- Pro 1908 si všimněte, že základní operační systém, na kterém Azure Stack centra běžela, byl aktualizován na Windows Server 2019. Tato aktualizace umožňuje základní základní vylepšení a možnost přinášet další možnosti do centra Azure Stack.
- Všechny součásti infrastruktury centra Azure Stack nyní fungují v režimu FIPS 140-2.
- Operátory centra Azure Stack teď můžou odebrat uživatelská data portálu. Další informace najdete v tématu [vymazání uživatelských dat portálu z centra Azure Stack](azure-stack-portal-clear.md).

### <a name="improvements"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->
- Vylepšení v oblasti šifrování uložených dat v Azure Stackovém centru pro zachování tajných kódů do čipu TPM (Trusted Platform Module) fyzických uzlů.

### <a name="changes"></a>Změny

- Poskytovatelé hardwaru budou vycházet z balíčku rozšíření OEM 2,1 nebo novějšího ve stejnou dobu jako centrum Azure Stack verze 1908. Balíček rozšíření OEM 2,1 nebo novější je předpokladem pro Azure Stack centra verze 1908. Další informace o tom, jak stáhnout balíček rozšíření OEM 2,1 nebo novější, získáte od poskytovatele hardwaru vašeho systému a v článku věnovaném [aktualizacím OEM](azure-stack-update-oem.md#oem-contact-information) .  

### <a name="fixes"></a>Opravy

- Opravili jsme problém s kompatibilitou s budoucími aktualizacemi OEM centra Azure Stack a problémem s nasazením virtuálního počítače pomocí uživatelských imagí zákazníka. Tento problém byl nalezen v 1907 a opraven v opravě hotfix [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44)  
- Opravili jsme problém s aktualizací firmwaru OEM a opravili jsme chybnou diagnostiku v test-AzureStack pro Fabric Ring stav. Tento problém byl nalezen v 1907 a opraven v opravě hotfix [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35)
- Opravili jsme problém s procesem aktualizace firmwaru OEM. Tento problém byl nalezen v 1907 a opraven v opravě hotfix [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37)

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->

## <a name="security-updates"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci centra Azure Stack najdete v tématu [aktualizace zabezpečení centra Azure Stack](release-notes-security-updates.md).

## <a name="download-the-update-1908"></a>Stáhnout aktualizaci

Balíček aktualizace centra Azure Stack 1908 můžete stáhnout ze [stránky pro stažení centra Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack centrum pravidelně vydává opravy hotfix. Před aktualizací centra Azure Stack na 1908 se ujistěte, že jste nainstalovali nejnovější opravu hotfix centra Azure Stack pro 1907.

Opravy hotfix centra Azure Stack se vztahují pouze na integrované systémy Azure Stack hub. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="prerequisites-before-applying-the-1908-update"></a>Požadavky: před instalací aktualizace 1908

Verze 1908 centra Azure Stack se musí použít ve verzi 1907 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [1.1907.18.56 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4528552)

Aktualizace centra Azure Stack 1908 vyžaduje od poskytovatele hardwaru vašeho systému **Azure Stack centra OEM verze 2,1 nebo novější** . Aktualizace OEM zahrnují aktualizace ovladačů a firmwaru do vašeho systémového hardwaru Azure Stack hub. Další informace o použití aktualizací OEM naleznete v tématu [apply Azure Stack Center Original Equipment Update Manufacturer Updates](azure-stack-update-oem.md) .

### <a name="after-successfully-applying-the-1908-update"></a>Po úspěšné instalaci aktualizace 1908

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [1.1908.14.53 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4537661)
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-build-reference"></a>1907 – odkaz na sestavení

Číslo buildu aktualizace centra Azure Stack 1907 je **1.1907.0.20**.

### <a name="update-type"></a>Typ aktualizace

Typ sestavení aktualizace centra Azure Stack 1907 je **Express**. Další informace o typech sestavení aktualizací naleznete v článku [Správa aktualizací v Azure Stack centru](azure-stack-updates.md) . Na základě interního testování je očekávaný čas potřebný k dokončení aktualizace 1907 přibližně 13 hodin.

- Přesné běhové moduly pro aktualizaci jsou obvykle závislé na kapacitě používané systémem v rámci úloh klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a hardwarové konfiguraci systému.
- Běhové moduly trvající déle než neočekávané jsou Neběžné a nevyžadují akci Azure Stack operátory centra, pokud se aktualizace nezdařila.
- Tato přibližná doba běhu je specifická pro aktualizaci 1907 a neměla by se porovnávat s jinými aktualizacemi centra Azure Stack.

## <a name="whats-in-this-update"></a>Co je v této aktualizaci

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novinky

<!-- What's new, also net new experiences and features. -->

- Verze služby pro shromažďování protokolů diagnostiky centra Azure Stack pro obecné dostupnosti, která usnadňuje a vylepšuje shromažďování protokolů diagnostiky. Služba Shromažďování protokolů diagnostiky centra Azure Stack poskytuje zjednodušený způsob shromažďování a sdílení diagnostických protokolů pomocí služeb zákaznické podpory Microsoftu (CSS). Tato služba Shromažďování protokolů diagnostiky poskytuje nové prostředí uživatele na portálu pro správu centra Azure Stack, které umožňuje operátorům nastavit automatické nahrávání diagnostických protokolů do objektu BLOB úložiště při vyvolání určitých kritických výstrah. Službu je také možné použít k provedení stejné operace na vyžádání. Další informace naleznete v článku [shromažďování diagnostických protokolů](azure-stack-diagnostic-log-collection-overview.md) .

- Verze ověřování infrastruktury Azure Stack sítě centra pro obecné dostupnosti jako součást testovacího nástroje Azure Stack centra **pro ověřování – AzureStack**. Síťová infrastruktura centra Azure Stack bude součástí **AzureStack testu**, aby zjistila, jestli dojde k selhání v síťové infrastruktuře centra Azure Stack. Test kontroluje konektivitu síťové infrastruktury tím, že obchází síťovou síť definovanou Azure Stack hub. Ukazuje připojení z veřejné virtuální IP adresy k nakonfigurovaným serverům DNS pro přeposílání, serverům NTP a koncovým bodům identity. Také kontroluje připojení k Azure při použití Azure AD jako zprostředkovatele identity nebo federovaného serveru při používání služby AD FS. Další informace najdete v článku [Nástroj pro ověření centra Azure Stack](azure-stack-diagnostic-test.md) .

- Přidání interního postupu pro rotaci tajných kódů pro otočení vnitřních certifikátů protokolu SQL TLS, jak je potřeba při aktualizaci systému.

### <a name="improvements"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Okno aktualizace centra Azure Stack nyní zobrazuje čas **dokončení posledního kroku** pro aktivní aktualizace. Toto sčítání se dá zobrazit tak, že přejdete do okna aktualizace a kliknete na běžící aktualizaci. **Poslední krok** je pak k dispozici v části **Podrobnosti o spuštění aktualizace** .

- Vylepšení akcí operátoru **Start-AzureStack** a **stop-AzureStack** . Čas spuštění centra Azure Stack se snížil průměrem 50%. Čas pro vypnutí centra Azure Stack se snížil na průměr 30%. Průměrná doba spouštění a vypínání zůstane stejná jako počet uzlů, které se zvyšují v jednotce škálování.

- Vylepšené zpracování chyb pro nástroj na odpojeném webu Marketplace. Pokud se stahování při použití možnosti **Export-AzSOfflineMarketplaceItem**nezdaří nebo částečně selže, zobrazí se podrobná chybová zpráva s dalšími podrobnostmi o chybě a jakémkoli možném postupu zmírnění.

- Vylepšili jsme výkon při vytváření spravovaných disků z velkého objektu BLOB nebo snímku stránky. Dřív při vytváření velkého disku aktivoval časový limit.  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- Vylepšená kontroly stavu virtuálních disků před vypnutím uzlu, aby nedocházelo k neočekávanému odpojení virtuálního disku.

- Vylepšené úložiště vnitřních protokolů pro operace správy. Tím se zvyšuje výkon a spolehlivost během operací správy tím, že minimalizují spotřebu paměti a úložiště interních procesů protokolů. Můžete si také všimnout lepších časů načítání stránky v okně aktualizace na portálu pro správu. V rámci tohoto vylepšení už nebudou v systému k dispozici protokoly aktualizací starší než šest měsíců. Pokud pro tyto aktualizace požadujete protokoly, nezapomeňte [si stáhnout Shrnutí](azure-stack-apply-updates.md) pro všechny aktualizace, které jsou starší než šest měsíců před provedením aktualizace 1907.

### <a name="changes"></a>Změny

- Centrum Azure Stack verze 1907 obsahuje výstražné upozornění, které dává operátorům pokyn, aby před aktualizací na verzi 1908 aktualizovali balíček OEM svého systému na verzi 2,1 nebo novější. Další informace o tom, jak použít aktualizace centra Azure Stack pro výrobce OEM, najdete v tématu [použití aktualizace výrobce původního vybavení centra Azure Stack](azure-stack-update-oem.md).

- Bylo přidáno nové odchozí pravidlo (HTTPS) pro povolení komunikace pro službu shromažďování protokolů diagnostiky centra Azure Stack. Další informace najdete v tématu [integrace centrálního centra Azure Stack – publikování koncových bodů](azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

- Služba zálohování infrastruktury nyní odstraní částečně nahrané zálohy, pokud umístění externího úložiště vyčerpá kapacitu.

- Zálohy infrastruktury již nezahrnují zálohování dat služby Domain Services. Tato změna se vztahuje pouze na systémy, které používají Azure Active Directory jako zprostředkovatele identity.

- Nyní Ověřujeme, že se obrázek ingestuje do okna **COMPUTE-> bitové kopie virtuálního počítače** , který je typu objekt blob stránky.

- Sada privilegovaných koncových bodů **set-BmcCredential** nyní aktualizuje přihlašovací údaje v řadiči pro správu základní desky.

### <a name="fixes"></a>Opravy

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->
- Opravili jsme problém, kdy se Vydavatel, nabídka a SKU zpracovaly v Správce prostředků šabloně, jako rozlišuje velká a malá písmena: image se pro nasazení nenačítal, pokud by parametry image nebyly stejného případu jako u vydavatele, nabídky a SKU.

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/129536438/home -->
- Opravili jsme problém se selháním zálohování s chybovou zprávou **PartialSucceeded** z důvodu vypršení časových limitů při zálohování metadat služby úložiště.  

- Opravili jsme problém, kdy výsledkem odstranění předplatných uživatelů byly osamocené prostředky.

- Opravili jsme problém, kdy se pole Popis při vytváření nabídky neuložilo.

- Opravili jsme problém, kdy uživatel s oprávněním **jen pro čtení** mohl vytvořit, upravit a odstranit prostředky. Nyní může uživatel vytvořit prostředky pouze v případě, že je přiřazeno oprávnění **Přispěvatel** . 

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127772311/home -->
- Opravili jsme problém, kdy se aktualizace nezdařila z důvodu souboru DLL uzamčeného hostitelem zprostředkovatele rozhraní WMI.

- Opravili jsme problém ve službě aktualizace, který znemožňuje zobrazení dostupných aktualizací na dlaždici aktualizace nebo poskytovateli prostředků. Tento problém byl nalezen v 1906 a opraven v [KB4511282](https://support.microsoft.com/help/4511282/)oprav hotfix.

- Opravili jsme problém, který by mohl způsobit selhání aktualizací z důvodu nesprávné konfigurace roviny správy, která je v pořádku. Tento problém byl nalezen v 1906 a opraven v [KB4512794](https://support.microsoft.com/help/4512794/)oprav hotfix.

- Opravili jsme problém, který uživatelům zabránil v dokončení nasazení imagí od jiných výrobců z webu Marketplace. Tento problém byl nalezen v 1906 a opraven v [KB4511259](https://support.microsoft.com/help/4511259/)oprav hotfix.

- Opravili jsme problém, který by mohl způsobit selhání vytváření virtuálních počítačů ze spravovaných imagí z důvodu chyby služby správce uživatelských imagí. Tento problém byl nalezen v 1906 a opraven v opravě hotfix [KB4512794](https://support.microsoft.com/help/4512794/)

- Opravili jsme problém, kdy se operace CRUD virtuálních počítačů nepovedly kvůli neobnovování mezipaměti služby App Gateway. Tento problém byl nalezen v 1906 a opraven v opravě hotfix [KB4513119](https://support.microsoft.com/en-us/help/4513119/)

- Opravili jsme problém poskytovatele prostředků stavu, který ovlivnil dostupnost oblasti a oken výstrah na portálu pro správu. Tento problém byl nalezen v 1906 a opraven v [KB4512794](https://support.microsoft.com/help/4512794)oprav hotfix.

## <a name="security-updates"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci centra Azure Stack najdete v tématu [aktualizace zabezpečení centra Azure Stack](release-notes-security-updates.md).

## <a name="update-planning"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Známé problémy](known-issues.md)
- [Aktualizace zabezpečení](release-notes-security-updates.md)
- [Kontrolní seznam aktivit před a po instalaci aktualizace](release-notes-checklist.md)

## <a name="download-the-update"></a>Stáhnout aktualizaci.

Balíček aktualizace centra Azure Stack 1907 můžete stáhnout ze [stránky pro stažení centra Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack centrum pravidelně vydává opravy hotfix. Před aktualizací centra Azure Stack na 1907 se ujistěte, že jste nainstalovali nejnovější opravu hotfix centra Azure Stack pro 1906.

Opravy hotfix centra Azure Stack se vztahují pouze na integrované systémy Azure Stack hub. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="before-applying-the-1907-update"></a>Před použitím aktualizace 1907

Verze 1907 centra Azure Stack se musí použít ve verzi 1906 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [1.1906.15.60 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4524559)

### <a name="after-successfully-applying-the-1907-update"></a>Po úspěšné instalaci aktualizace 1907

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [1.1907.18.56 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4528552)
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-build-reference"></a>1906 – odkaz na sestavení

Číslo buildu aktualizace centra Azure Stack 1906 je **1.1906.0.30**.

### <a name="update-type"></a>Typ aktualizace

Typ sestavení aktualizace centra Azure Stack 1906 je **Express**. Další informace o typech sestavení aktualizací naleznete v článku [Správa aktualizací v Azure Stack centru](azure-stack-updates.md) . Očekávaný čas potřebný k dokončení aktualizace 1906 je přibližně 10 hodin, bez ohledu na počet fyzických uzlů ve vašem prostředí Azure Stack hub. Přesné běhové moduly pro aktualizace budou obvykle záviset na kapacitě používané v systému podle zatížení klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a specifikacemi hardwaru systému. Běhové moduly trvající déle než očekávaná hodnota nejsou běžné a nevyžadují akci Azure Stack operátory centra, pokud se aktualizace nezdařila. Tato přibližná doba běhu je specifická pro aktualizaci 1906 a neměla by se porovnávat s jinými aktualizacemi centra Azure Stack.

## <a name="whats-in-this-update"></a>Co je v této aktualizaci

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Do privilegovaného koncového bodu (PEP) se přidala rutina **set-TLSPolicy** , která vynutí TLS 1,2 u všech koncových bodů. Další informace najdete v tématu [ovládací prvky zabezpečení centra Azure Stack](azure-stack-security-configuration.md).

- Do privilegovaného koncového bodu (PEP) se přidala rutina **Get-TLSPolicy** , která načte použité zásady TLS. Další informace najdete v tématu [ovládací prvky zabezpečení centra Azure Stack](azure-stack-security-configuration.md).

- Přidání interního postupu pro rotaci tajných kódů pro otočení vnitřních certifikátů TLS podle požadavků při aktualizaci systému.

- Přidání ochrany, která zabraňuje vypršení platnosti interních tajných kódů tím, že vynucuje interní rotaci tajných kódů pro případ, že kritická výstraha na vypršení platnosti K tomuto zabezpečení by se neměl spoléhat jako na běžný operační postup. Rotace tajných kódů by měla být naplánována během časového období údržby. Další informace najdete v tématu [rotace tajného kódu centra Azure Stack](azure-stack-rotate-secrets.md).

- Visual Studio Code se teď podporuje s nasazením centra Azure Stack pomocí AD FS.

### <a name="improvements"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Rutina **Get-GraphApplication** v privilegovaném koncovém bodu teď zobrazuje kryptografický otisk aktuálně používaného certifikátu. Tato aktualizace vylepšuje správu certifikátů pro instanční objekty, když se Azure Stack centrum nasazuje s AD FS.

- Byla přidána nová pravidla monitorování stavu, která ověřují dostupnost služby AD Graph a AD FS, včetně možnosti vyvolat výstrahy.

- Vylepšení spolehlivosti poskytovatele prostředků zálohování, když se služba infrastruktura zálohování přesune na jinou instanci.

- Optimalizace výkonu pro postup při rotaci externích tajných klíčů, který poskytuje jednotnou dobu provádění, která usnadňuje plánování časového období údržby.

- Rutina **test-AzureStack** nyní hlásí interní tajné klíče, jejichž platnost brzy vyprší (kritické výstrahy).

- V rámci privilegovaného koncového bodu je k dispozici nový parametr pro rutinu **Register-CustomAdfs** , která umožňuje přeskočí kontrolu seznamu odvolaných certifikátů při konfiguraci důvěryhodnosti federace pro AD FS.

- Vydání 1906 přináší lepší přehled o průběhu aktualizace, takže můžete mít jistotu, že se aktualizace nepozastavuje. Tato aktualizace vede ke zvýšení celkového počtu kroků aktualizace zobrazených u operátorů v okně **aktualizace** . Můžete si také všimnout dalších kroků aktualizace, které probíhají paralelně než v předchozích aktualizacích.

#### <a name="networking-updates"></a>Aktualizace sítě

- Byla aktualizována doba zapůjčení nastavená v respondéru DHCP, aby byla konzistentní s Azure.

- Vylepšené sazby opakování pro poskytovatele prostředků ve scénáři selhání nasazení prostředků.

- Odebrali jsme možnost **Standard** SKU z nástroje pro vyrovnávání zatížení i z veřejné IP adresy, protože se v tuto chvíli nepodporuje.

### <a name="changes"></a>Změny

- Vytvoření prostředí účtu úložiště je teď konzistentní s Azure.

- Aktivační události změněné výstrahy pro vypršení platnosti interních tajných kódů:
  - Upozornění na upozornění se teď zvyšují 90 dní před vypršením platnosti tajných kódů.
  - Kritické výstrahy jsou nyní vyvolány 30 dní před vypršením platnosti tajných kódů.

- Aktualizované řetězce v poskytovateli prostředků zálohování infrastruktury pro konzistentní terminologii.

### <a name="fixes"></a>Opravy

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->

- Opravili jsme problém, kdy změna velikosti virtuálního počítače spravovaného disku selhala s **chybou vnitřní operace**.

- Opravili jsme problém, kdy vytvoření image uživatele, která selhala, umístí službu, která spravuje image, je ve špatném stavu. Tím se odstraní neúspěšné image a vytváření nových imagí. Tento problém je také opravený v opravě 1905.

- Aktivní výstrahy týkající se vypršení platnosti interních tajných kódů se teď po úspěšném provedení interního přestupnosti automaticky zavřou.

- Opravili jsme problém, ve kterém doba aktualizace na kartě Historie aktualizace ořízne první číslici, pokud byla aktualizace spuštěná déle než 99 hodin.

- Okno **aktualizace** obsahuje možnost **obnovení** pro neúspěšné aktualizace.

- V portálu pro správu a uživatele byly vyřešeny problémy v Marketplace, ve kterých bylo rozšíření Docker nesprávně vráceno ze služby Search, ale nebylo možné provést žádnou další akci, protože není k dispozici v centru Azure Stack.

- Opravili jsme problém v uživatelském rozhraní nasazení šablony, který neplní parametry, pokud název šablony začíná podtržítkem _.

- Opravili jsme problém, kdy prostředí pro vytváření sady škálování virtuálních počítačů poskytuje CentOS 7,2 jako možnost nasazení. CentOS 7,2 není k dispozici v centru Azure Stack. Jako naši možnost pro nasazení teď poskytujeme CentOS 7,5.

- V okně **Virtual Machine Scale Sets** teď můžete odebrat sadu škálování.

## <a name="security-updates"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci centra Azure Stack najdete v tématu [aktualizace zabezpečení centra Azure Stack](release-notes-security-updates.md).

## <a name="update-planning"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Známé problémy](known-issues.md)
- [Aktualizace zabezpečení](release-notes-security-updates.md)
- [Kontrolní seznam aktivit před a po instalaci aktualizace](release-notes-checklist.md)

## <a name="download-the-update"></a>Stáhnout aktualizaci.

Balíček aktualizace centra Azure Stack 1906 můžete stáhnout ze [stránky pro stažení centra Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack centrum pravidelně vydává opravy hotfix. Před aktualizací centra Azure Stack na 1906 se ujistěte, že jste nainstalovali nejnovější opravu hotfix centra Azure Stack pro 1905. Po aktualizaci nainstalujte všechny [dostupné opravy hotfix pro 1906](#after-successfully-applying-the-1906-update).

Opravy hotfix centra Azure Stack se vztahují pouze na integrované systémy Azure Stack hub. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="before-applying-the-1906-update"></a>Před použitím aktualizace 1906

Verze 1906 centra Azure Stack se musí použít ve verzi 1905 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [1.1905.3.48 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>Po úspěšné instalaci aktualizace 1906

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [1.1906.15.60 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4524559)
::: moniker-end

::: moniker range=">=azs-1906"
## <a name="automatic-update-notifications"></a>Oznámení automatických aktualizací

Systémy, které mají přístup k Internetu ze sítě infrastruktury, uvidí na portálu operátora zprávu **k aktualizaci k dispozici** . Systémy bez přístupu k Internetu můžou stáhnout a naimportovat soubor. zip s odpovídající příponou. XML.

> [!TIP]  
> Přihlaste se k odběru následujících informačních kanálů *RSS* nebo *Atom* , abyste zachovali Azure Stack opravy hotfix centra:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Počtu](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archive"></a>Archiv

Pokud chcete získat přístup k archivovaným dokumentům k verzi pro starší verzi, použijte rozevírací nabídku selektor verzí nad obsahem vlevo a vyberte verzi, kterou chcete zobrazit.

## <a name="next-steps"></a>Další kroky

- Přehled správy aktualizací v centru Azure Stack najdete v tématu [Správa aktualizací v centru Azure Stack – přehled](azure-stack-updates.md).  
- Další informace o tom, jak pomocí centra Azure Stack použít aktualizace, najdete v tématu věnovaném [použití aktualizací v centru Azure Stack](azure-stack-apply-updates.md).
- Pokud si chcete projít zásady obsluhy pro Azure Stack centrum a co musíte udělat, abyste systém zachovali v podporovaném stavu, přečtěte si téma [zásady obsluhy centra Azure Stack](azure-stack-servicing-policy.md).  
- Postup pro monitorování a obnovení aktualizací pomocí privilegovaného koncového bodu (PEP) najdete v tématu [monitorování aktualizací v centru Azure Stack pomocí privilegovaného koncového bodu](azure-stack-monitor-update.md).
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1905"
## <a name="1905-archived-release-notes"></a>1905 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1904"
## <a name="1904-archived-release-notes"></a>1904 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-release-notes"></a>1903 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-release-notes"></a>1902 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-release-notes"></a>1901 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-release-notes"></a>1811 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-release-notes"></a>1809 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-release-notes"></a>1808 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-release-notes"></a>1807 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-release-notes"></a>1805 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-release-notes"></a>1804 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-release-notes"></a>1803 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-release-notes"></a>1802 archivované poznámky k verzi
::: moniker-end

::: moniker range="<azs-1906"
Do Galerie TechNet můžete získat přístup ke [starším verzím poznámky k verzi centra Azure Stack](https://aka.ms/azsarchivedrelnotes). Tyto archivované dokumenty jsou k dispozici pouze pro referenční účely a neznamenají podporu těchto verzí. Informace o podpoře centra Azure Stack najdete v tématu [zásady obsluhy centra Azure Stack](azure-stack-servicing-policy.md). Pokud potřebujete další pomoc, obraťte se na službu zákaznické podpory společnosti Microsoft.
::: moniker-end


