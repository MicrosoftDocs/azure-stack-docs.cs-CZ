---
title: Poznámky k verzi Azure Stack | Microsoft Docs
description: Seznamte se s aktualizacemi Azure Stack integrovaných systémů, včetně co je nového a kde stáhnout aktualizaci.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 11/22/2019
ms.openlocfilehash: a0e925d0c7a8401ea6d3f14f82cdb01bba4b354f
ms.sourcegitcommit: 55ec59f831a98c42a4e9ff0dd954bf10adb98ff1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74540336"
---
# <a name="azure-stack-updates-release-notes"></a>Aktualizace Azure Stack: poznámky k verzi

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje obsah balíčků aktualizací Azure Stack. Tato aktualizace obsahuje novinky a opravy pro tuto verzi Azure Stack.

Pokud chcete získat přístup k poznámkám k verzi pro jinou verzi, použijte rozevírací nabídku selektor verzí nad obsahem vlevo.

::: moniker range=">=azs-1906"
> [!IMPORTANT]  
> Tento balíček aktualizace je určen pouze pro Azure Stack integrovaných systémů. Nepoužívejte tento balíček aktualizace na Azure Stack Development Kit.
::: moniker-end
::: moniker range="<azs-1906"
> [!IMPORTANT]  
> Pokud je vaše instance Azure Stack za více než dvěma aktualizacemi, je považována za nedodržující předpisy. Aby bylo možné [získat podporu, musíte aktualizovat aspoň minimální podporovanou verzi](azure-stack-servicing-policy.md#keep-your-system-under-support).
::: moniker-end

## <a name="update-planning"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Známé problémy](known-issues.md)
- [Aktualizace zabezpečení](release-notes-security-updates.md)
- [Kontrolní seznam aktivit před a po instalaci aktualizace](release-notes-checklist.md)

Nápovědu k řešení potíží s aktualizacemi a procesu aktualizace najdete v tématu [řešení potíží se opravami a aktualizacemi pro Azure Stack](azure-stack-updates-troubleshoot.md).

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-1910"
## <a name="1910-build-reference"></a>1910 – odkaz na sestavení

Číslo buildu aktualizace Azure Stack 1910 je **1.1910.0.58**.

### <a name="update-type"></a>Typ aktualizace

Od 1908 se na systém Windows Server 2019 aktualizoval příslušný operační systém, na kterém Azure Stack běžet. To umožňuje základní vylepšení základních funkcí a také možnost přinášet další možnosti Azure Stack v blízké budoucnosti.

Typ sestavení aktualizace Azure Stack 1910 je **Express**.

Velikost balíčku aktualizace 1910 je v porovnání s předchozími aktualizacemi větší. Zvýšení velikosti má za následek delší dobu stahování. Aktualizace zůstane ve stavu **přípravy** po dlouhou dobu a operátoři můžou očekávat, že tento proces trvá déle než u předchozích aktualizací. Očekávaný čas potřebný k dokončení aktualizace 1910 je přibližně 10 hodin, bez ohledu na počet fyzických uzlů v prostředí Azure Stack. Přesné běhové moduly pro aktualizaci jsou obvykle závislé na kapacitě používané v systému podle zatížení klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a specifikacemi hardwaru systému. Běhové moduly trvající déle než očekávaná hodnota nejsou běžné a nevyžadují akci Azure Stack operátory, pokud se aktualizace nezdařila. Tato přibližná doba běhu je specifická pro aktualizaci 1910 a neměla by se porovnávat s jinými aktualizacemi Azure Stack.

Další informace o typech sestavení aktualizací najdete v tématu [Správa aktualizací v Azure Stack](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novinky

<!-- What's new, also net new experiences and features. -->

- Portál pro správu nyní zobrazuje IP adresy privilegovaného koncového bodu v nabídce Vlastnosti oblasti pro snazší zjišťování. Kromě toho zobrazuje aktuální nakonfigurovaný časový server a servery DNS pro přeposílání.

- Systém Azure Stack stav a monitorování teď může vyvolávat výstrahy pro různé hardwarové součásti, pokud dojde k chybě. To vyžaduje další konfiguraci. Další informace najdete v tématu [monitorování Azure Stack hardwarových komponent](azure-stack-hardware-monitoring.md).

- [Podpora Cloud-init pro Azure Stack](/azure/virtual-machines/linux/using-cloud-init): Cloud-init je široce používaný přístup k přizpůsobení virtuálního počítače se systémem Linux při prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Vzhledem k tomu, že se během procesu prvotního spuštění volá Cloud-init, neexistují žádné další kroky ani nepotřebné agenti pro použití konfigurace. Image Ubuntu na webu Marketplace byly aktualizovány tak, aby podporovaly Cloud-init pro zřizování.

- Azure Stack teď podporuje všechny verze agenta Windows Azure Linux jako Azure.

- K dispozici je nová verze modulů PowerShellu pro správce Azure Stack. <!-- For more information, see -->

- Přidání rutiny **set-AzSDefenderManualUpdate** do privilegovaného koncového bodu (PEP) ke konfiguraci ruční aktualizace definicí programu Windows Defender v infrastruktuře Azure Stack. Další informace najdete v tématu [aktualizace antivirové ochrany v programu Windows Defender na Azure Stack](azure-stack-security-av.md).

- Přidání rutiny **Get-AzSDefenderManualUpdate** do privilegovaného koncového bodu (PEP), který načte konfiguraci ruční aktualizace definicí programu Windows Defender v infrastruktuře Azure Stack. Další informace najdete v tématu [aktualizace antivirové ochrany v programu Windows Defender na Azure Stack](azure-stack-security-av.md).

- Přidání rutiny **set-AzSDnsForwarder** do privilegovaného koncového bodu (PEP) pro změnu nastavení služby pro přeposílání serverů DNS v Azure Stack. Další informace o konfiguraci DNS najdete v tématu [Azure Stack integrace služby DNS Datacenter](azure-stack-integrate-dns.md).

- Přidání rutiny **Get-AzSDnsForwarder** do privilegovaného koncového bodu (PEP), aby se načetlo nastavení služby pro přeposílání serverů DNS v Azure Stack. Další informace o konfiguraci DNS najdete v tématu [Azure Stack integrace služby DNS Datacenter](azure-stack-integrate-dns.md).


### <a name="improvements"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Přidání pravidla auditování k vytvoření sestavy, když je externí zařízení (třeba klíč USB) připojené k uzlu infrastruktury Azure Stack. Protokol auditu se vysílá přes protokol syslog a zobrazí se jako **Microsoft-Windows-Security-audit: 6416 | Technologie Plug and Play události** Další informace o tom, jak nakonfigurovat klienta syslog, najdete v tématu [předávání SYSLOG](azure-stack-integrate-security.md).

- Azure Stack se přesouvá na 4096 bitů klíčů RSA pro vnitřní certifikáty. Spouštění interního tajného klíče nahradí staré 2048 bitové certifikáty pomocí 4096 bitového dlouhého certifikátu. Další informace o rotaci tajných kódů v Azure Stack najdete v tématu [otočení tajných kódů v Azure Stack](azure-stack-rotate-secrets.md).

- Upgrady na složitost kryptografických algoritmů a složitosti klíčů pro několik interních součástí, aby byly v rozporu s výborem pro národní systémy zabezpečení – zásady 15 (CNSSP-15), které poskytují osvědčené postupy pro používání veřejných standardů pro zabezpečení sdílení informací. Mezi vylepšeními je AES256 ověřování protokolem Kerberos a SHA384 pro šifrování pomocí sítě VPN. Další informace o CNSSP-15 najdete na stránce věnované [národním bezpečnostním systémům a zásadám](http://www.cnss.gov/CNSS/issuances/Policies.cfm).

- V důsledku výše uvedeného upgradu teď Azure Stack mít nové výchozí hodnoty pro konfigurace protokolu IPsec/IKEv2. Nové výchozí hodnoty, které se používají na straně Azure Stack, jsou následující:

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

- Služba Backup v infrastruktuře vylepšuje logiku, která počítá požadované místo na zálohování místo spoléhání na pevnou prahovou hodnotu. Služba bude používat velikost zálohování, zásady uchovávání informací, rezervu a aktuální využití externího umístění úložiště k určení, jestli je potřeba vystavit upozornění pro daný provoz. 

### <a name="changes"></a>Provedeny

- Při stahování položek Marketplace z Azure do Azure Stack je k dispozici nové uživatelské rozhraní, které umožňuje zadat verzi položky, pokud existuje více verzí. Nové uživatelské rozhraní je k dispozici v rámci připojených i odpojených scénářů. Další informace najdete v tématu [stažení položek Marketplace z Azure do Azure Stack](azure-stack-download-azure-marketplace-item.md).  

- Počínaje verzí 1910 vyžaduje systém Azure Stack další/20 privátní interní IP místo. Tato síť je soukromá pro Azure Stack systém a je možné ji znovu použít na více Azure Stack systémů v rámci vašeho datového centra. I když je síť soukromá, aby Azure Stack, nesmí se překrývat se sítí ve vašem datovém centru. Privátní IP místo/20 je rozdělené do několika sítí, které umožňují provozování infrastruktury Azure Stack v kontejnerech (jak je uvedeno výše v [poznámkách k verzi 1905](release-notes.md?view=azs-1905)). Cílem provozování infrastruktury Azure Stack v kontejnerech je optimalizace využití a zvýšení výkonu. Kromě toho privátní IP místo/20 slouží také k umožnění průběžného úsilí, které před nasazením zmenší požadované IP místo pro směrování.

  - Upozorňujeme, že vstup/20 slouží jako předpoklad pro další Azure Stack aktualizace po 1910. Po vydání další Azure Stack aktualizace po 1910 a provedení pokusu o její instalaci se aktualizace nezdaří, pokud jste nedokončili vstup/20, jak je popsáno níže v tématu nápravné kroky. V portálu pro správu bude k dispozici výstraha, dokud se výše uvedené kroky nápravy nedokončily. Informace o tom, jak se bude tento nový privátní prostor spotřebovat, najdete v článku věnovaném [integraci sítě Datacenter](azure-stack-network.md#private-network) . 

  - Postup nápravy: Pokud chcete problém vyřešit, postupujte podle pokynů a [otevřete relaci PEP](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Připravte velikost [privátního interního rozsahu IP adres](azure-stack-network.md#logical-networks) /20 a v relaci PEP spusťte následující rutinu (k dispozici pouze počínaje 1910) ve formátu: `Set-AzsPrivateNetwork -UserSubnet 100.87.0.0/20`. Pokud se operace provede úspěšně, obdržíte do **Konfigurace přidaný rozsah interní sítě AZS**zprávy. Po úspěšném dokončení se výstraha zavře na portálu pro správu. Systém Azure Stack bude nyní moci aktualizovat na další verzi.
  
- Služba zálohování infrastruktury odstraní částečně nahraná zálohovaná data v případě, že umístění externího úložiště během procesu odeslání nevyužívá kapacitu.  

- Služba zálohování infrastruktury přidá službu identit do datové části zálohování pro nasazení AAD.  

- Modul AzureStack PowerShell byl aktualizován na verzi 1.8.0 pro verzi 1910.<br>Změny zahrnují:
   - **Nový modul Správce DRP**: poskytovatel prostředků nasazení (DRP) umožňuje Orchestrované nasazení poskytovatelů prostředků Azure Stack. Tyto příkazy komunikují s Azure Resource Managerou vrstvou a komunikují s DRP.
   - **BRP**: <br />
           – Podporuje obnovení jedné role pro zálohování infrastruktury Azure Stack. <br />
           – Přidejte parametr `RoleName` do `Restore-AzsBackup`rutiny.
   - **FRP**: zásadní změny prostředků **jednotky** a **svazku** pomocí rozhraní API verze `2019-05-01`. Funkce jsou podporovány Azure Stack 1910 a novějším: <br />
            -Byla změněna hodnota `ID`, `Name`, `HealthStatus` a `OperationalStatus`. <br />
            – Podporované nové vlastnosti `FirmwareVersion`, `IsIndicationEnabled`, `Manufacturer`a `StoragePool` pro prostředky **jednotky** . <br />
            -Vlastnosti `CanPool` a `CannotPoolReason` prostředků **jednotky** jsou zastaralé; místo toho použijte `OperationalStatus`.

### <a name="fixes"></a>Řeší

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Opravili jsme problém, který zabránil vynucování zásad TLS 1,2 v prostředích nasazených před vydáním Azure Stack 1904.
- Opravili jsme problém, kdy se virtuální počítač s Ubuntu 18,04 vytvořený s povoleným autorizací SSH neumožňuje používat klíče SSH k přihlášení. 
- **Heslo pro resetování** se odebralo z uživatelského rozhraní sady škálování virtuálního počítače.
- Opravili jsme problém, kdy odstranění nástroje pro vyrovnávání zatížení z portálu nezpůsobilo odstranění objektu ve vrstvě infrastruktury.
- Opravili jsme problém, který ukázal nepřesné procento výstrahy využití fondu brány na portálu pro správu.
- Opravili jsme problém, kdy přidání více než jedné veřejné IP adresy na stejné síťové kartě na virtuálním počítači způsobilo problémy s připojením k Internetu. Nyní by síťová karta se dvěma veřejnými IP adresami měla fungovat podle očekávání.

## <a name="security-updates"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci Azure Stack najdete v tématu [Azure Stack aktualizace zabezpečení](release-notes-security-updates.md).

## <a name="update-planning"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Známé problémy](known-issues.md)
- [Aktualizace zabezpečení](release-notes-security-updates.md)
- [Kontrolní seznam aktivit před a po instalaci aktualizace](release-notes-checklist.md)

## <a name="download-the-update"></a>Stáhnout aktualizaci

Balíček aktualizace Azure Stack 1910 můžete stáhnout ze [stránky pro stažení Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack pravidelně vydává opravy hotfix. Před aktualizací Azure Stack na 1910 nezapomeňte nainstalovat nejnovější Azure Stack opravu hotfix pro 1908.

Azure Stack oprav hotfix se týkají pouze Azure Stack integrovaných systémů. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="prerequisites-before-applying-the-1910-update"></a>Požadavky: před instalací aktualizace 1910

Vydání verze 1910 Azure Stack se musí použít ve verzi 1908 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1908.9.43](https://support.microsoft.com/help/4531007)

### <a name="after-successfully-applying-the-1910-update"></a>Po úspěšné instalaci aktualizace 1910

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- Pro 1910 není k dispozici žádná oprava hotfix.
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-build-reference"></a>1908 – odkaz na sestavení

Číslo buildu aktualizace Azure Stack 1908 je **1.1908.4.33**.

### <a name="update-type"></a>Typ aktualizace

V 1908 se základní operační systém, na kterém Azure Stack běžet, aktualizoval na Windows Server 2019. To umožňuje základní vylepšení základních funkcí a také možnost přinášet další možnosti Azure Stack v blízké budoucnosti.

Typ sestavení aktualizace Azure Stack 1908 je **plný**. V důsledku toho má aktualizace 1908 delší dobu běhu než expresní aktualizace jako 1906 a 1907. Přesné moduly runtime pro úplné aktualizace obvykle závisí na počtu uzlů, které vaše instance Azure Stack obsahuje, na kapacitě používané v systému podle úloh klientů, na síťovém připojení vašeho systému (Pokud je připojené k Internetu) a na vašem systémovém hardwaru. rozšířeného. Aktualizace 1908 měla během interního testování následující očekávané moduly runtime: 4 uzly-42 hodiny, 8 uzlů – 50 hodin, 12 uzlů – 60 hodiny, 16 uzlů – 70 hodin. Aktualizační moduly, které trvají déle než tyto očekávané hodnoty, nejsou Neběžné a nevyžadují akci Azure Stack operátory, pokud aktualizace neproběhne úspěšně.

Další informace o typech sestavení aktualizací najdete v tématu [Správa aktualizací v Azure Stack](azure-stack-updates.md).

- Přesné běhové moduly pro aktualizaci jsou obvykle závislé na kapacitě používané systémem v rámci úloh klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a hardwarové konfiguraci systému.
- Běhové moduly, které trvají déle než obvykle, nejsou běžné a nevyžadují akci Azure Stack operátory, pokud se aktualizace nezdařila.
- Tato přibližná doba běhu je specifická pro aktualizaci 1908 a neměla by se porovnávat s jinými aktualizacemi Azure Stack.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novinky

<!-- What's new, also net new experiences and features. -->

- Pro 1908 si všimněte, že základní operační systém, na kterém Azure Stack běžet, byl aktualizován na Windows Server 2019. To umožňuje základní vylepšení základních funkcí a také možnost přinášet další možnosti Azure Stack v blízké budoucnosti.
- Všechny součásti infrastruktury Azure Stack nyní fungují v režimu FIPS 140-2.
- Azure Stack operátory teď můžou odebrat uživatelská data portálu. Další informace najdete v tématu [vymazání uživatelských dat portálu z Azure Stack](azure-stack-portal-clear.md).

### <a name="improvements"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->
- Vylepšení v oblasti šifrování uložených dat Azure Stack pro zachování tajných kódů do hardwaru Trusted Platform Module (TPM) fyzických uzlů.

### <a name="changes"></a>Provedeny

- Poskytovatelé hardwaru budou vycházet z balíčku rozšíření OEM 2,1 nebo novějšího ve stejnou dobu jako verze Azure Stack 1908. Balíček rozšíření OEM 2,1 nebo novější je předpokladem pro Azure Stack verze 1908. Další informace o tom, jak stáhnout balíček rozšíření OEM 2,1 nebo novější, získáte od poskytovatele hardwaru vašeho systému a v článku věnovaném [aktualizacím OEM](azure-stack-update-oem.md#oem-contact-information) .  

### <a name="fixes"></a>Řeší

- Opravili jsme problém s kompatibilitou s budoucími Azure Stack aktualizacemi OEM a problémem s nasazením virtuálního počítače pomocí uživatelských imagí zákazníka. Tento problém byl nalezen v 1907 a opraven v opravě hotfix [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44)  
- Opravili jsme problém s aktualizací firmwaru OEM a opravili jsme chybnou diagnostiku v test-AzureStack pro Fabric Ring stav. Tento problém byl nalezen v 1907 a opraven v opravě hotfix [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35)
- Opravili jsme problém s procesem aktualizace firmwaru OEM. Tento problém byl nalezen v 1907 a opraven v opravě hotfix [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37)

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci Azure Stack najdete v tématu [Azure Stack aktualizace zabezpečení](release-notes-security-updates.md).

## <a name="download-the-update-1908"></a>Stáhnout aktualizaci

Balíček aktualizace Azure Stack 1908 můžete stáhnout ze [stránky pro stažení Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack pravidelně vydává opravy hotfix. Před aktualizací Azure Stack na 1908 nezapomeňte nainstalovat nejnovější Azure Stack opravu hotfix pro 1907.

Azure Stack oprav hotfix se týkají pouze Azure Stack integrovaných systémů. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="prerequisites-before-applying-the-1908-update"></a>Požadavky: před instalací aktualizace 1908

Vydání verze 1908 Azure Stack se musí použít ve verzi 1907 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1907.18.56](https://support.microsoft.com/help/4528552)

Aktualizace Azure Stack 1908 vyžaduje **Azure Stack OEM verze 2,1 nebo novější** od poskytovatele hardwaru vašeho systému. Aktualizace OEM zahrnují aktualizace ovladačů a firmwaru pro váš Azure Stack systémový hardware. Další informace o použití aktualizací OEM naleznete v tématu [apply Azure Stack Updates Original Equipment Updates](azure-stack-update-oem.md) .

### <a name="after-successfully-applying-the-1908-update"></a>Po úspěšné instalaci aktualizace 1908

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1908.9.43](https://support.microsoft.com/help/4531007)
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-build-reference"></a>1907 – odkaz na sestavení

Číslo buildu aktualizace Azure Stack 1907 je **1.1907.0.20**.

### <a name="update-type"></a>Typ aktualizace

Typ sestavení aktualizace Azure Stack 1907 je **Express**. Další informace o typech sestavení aktualizace naleznete v článku [Správa aktualizací v Azure Stack](azure-stack-updates.md) . Na základě interního testování je očekávaný čas potřebný k dokončení aktualizace 1907 přibližně 13 hodin.

- Přesné běhové moduly pro aktualizaci jsou obvykle závislé na kapacitě používané systémem v rámci úloh klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a hardwarové konfiguraci systému.
- Běhové moduly, které trvají déle než obvykle, nejsou běžné a nevyžadují akci Azure Stack operátory, pokud se aktualizace nezdařila.
- Tato přibližná doba běhu je specifická pro aktualizaci 1907 a neměla by se porovnávat s jinými aktualizacemi Azure Stack.

## <a name="whats-in-this-update"></a>Co je v této aktualizaci

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novinky

<!-- What's new, also net new experiences and features. -->

- Verze služby pro diagnostiku protokolu Azure Stack pro obecné dostupnosti pro usnadnění a zlepšení shromažďování protokolů diagnostiky. Služba Azure Stack Diagnostic log Collection nabízí zjednodušený způsob shromažďování a sdílení diagnostických protokolů pomocí služeb zákaznické podpory Microsoftu (CSS). Tato služba Shromažďování protokolů diagnostiky poskytuje nové prostředí pro uživatele na portálu Azure Stack správce, které umožňuje operátorům nastavit automatické nahrávání diagnostických protokolů do objektu BLOB úložiště při vyvolání určitých kritických výstrah nebo provedení stejných operace na vyžádání Další informace naleznete v článku [shromažďování diagnostických protokolů](azure-stack-diagnostic-log-collection-overview.md) .

- Verze ověřování infrastruktury Azure Stack sítě v rámci obecné dostupnosti jako součást nástroje pro ověření Azure Stack **test-AzureStack**. Azure Stack síťová infrastruktura bude součástí **AzureStack testu**, aby bylo možné zjistit, jestli dojde k selhání v síťové infrastruktuře Azure Stack. Test kontroluje konektivitu síťové infrastruktury tím, že obchází Azure Stack softwarově definované sítě. Ukazuje připojení z veřejné virtuální IP adresy k nakonfigurovaným serverům DNS pro přeposílání, serverům NTP a koncovým bodům identity. Kromě toho kontroluje připojení k Azure při použití Azure AD jako zprostředkovatele identity nebo federovaného serveru při používání služby AD FS. Další informace najdete v článku [Nástroj pro ověření Azure Stack](azure-stack-diagnostic-test.md) .

- Přidání interního postupu pro rotaci tajných kódů pro otočení vnitřních certifikátů protokolu SQL TLS, jak je potřeba při aktualizaci systému.

### <a name="improvements"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Okno Azure Stack aktualizace nyní zobrazuje čas **dokončení posledního kroku** pro aktivní aktualizace. To lze zobrazit tak, že přejdete do okna aktualizace a kliknete na běžící aktualizaci. **Poslední krok** je pak k dispozici v části **Podrobnosti o spuštění aktualizace** .

- Vylepšení akcí operátoru **Start-AzureStack** a **stop-AzureStack** . Čas spuštění Azure Stack se snížil průměrem 50%. Čas pro vypnutí Azure Stack byl snížen o průměr 30%. Průměrná doba spouštění a vypínání zůstane stejná jako počet uzlů, které se zvyšují v jednotce škálování.

- Vylepšené zpracování chyb pro nástroj na odpojeném webu Marketplace. Pokud se stahování při použití **Export-AzSOfflineMarketplaceItem**nezdaří nebo částečně proběhne úspěšně, zobrazí se podrobná chybová zpráva s dalšími podrobnostmi o chybách a krocích při zmírňování.

- Vylepšili jsme výkon při vytváření spravovaných disků z velkého objektu BLOB nebo snímku stránky. Dřív při vytváření velkého disku aktivoval časový limit.  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- Vylepšená kontroly stavu virtuálních disků před vypnutím uzlu, aby nedocházelo k neočekávanému odpojení virtuálního disku.

- Vylepšené úložiště vnitřních protokolů pro operace správy. Výsledkem je vyšší výkon a spolehlivost během operací správy tím, že minimalizují spotřebu paměti a úložiště interních procesů protokolů. Můžete si také všimnout lepších časů načítání stránky v okně aktualizace na portálu pro správu. V rámci tohoto vylepšení už nebudou v systému k dispozici protokoly aktualizací starší než 6 měsíců. Pokud pro tyto aktualizace požadujete protokoly, nezapomeňte [si stáhnout Shrnutí](azure-stack-apply-updates.md) pro všechny aktualizace, které jsou starší než 6 měsíců před provedením aktualizace 1907.

### <a name="changes"></a>Provedeny

- Azure Stack verze 1907 obsahuje varovnou výstrahu, která dává operátorům pokyn, aby před aktualizací na verzi 1908 aktualizovala balíček OEM svého systému na verzi 2,1 nebo novější. Další informace o tom, jak použít Azure Stack aktualizace OEM, najdete v části [použití Azure Stack původní aktualizace výrobce zařízení](azure-stack-update-oem.md).

- Bylo přidáno nové odchozí pravidlo (HTTPS) pro povolení komunikace pro Azure Stack službu shromažďování protokolů diagnostiky. Další informace najdete v tématu [Azure Stack integrace Datacenter – publikování koncových bodů](azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

- Služba zálohování infrastruktury nyní odstraní částečně nahrané zálohy, pokud umístění externího úložiště vyčerpá kapacitu.

- Zálohy infrastruktury již nezahrnují zálohování dat služby Domain Services. To platí jenom pro systémy, které používají Azure Active Directory jako zprostředkovatele identity.

- Nyní Ověřujeme, že se obrázek ingestuje do okna **COMPUTE-> bitové kopie virtuálního počítače** , který je typu objekt blob stránky.

- Sada privilegovaných koncových bodů **set-BmcCredential** nyní aktualizuje přihlašovací údaje v řadiči pro správu základní desky.

### <a name="fixes"></a>Řeší

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- Opravili jsme problém, kdy byl Vydavatel, nabídka a SKU ošetřen v Správce prostředků šabloně s rozlišením malých a velkých písmen: image nebyla načtena pro nasazení, pokud by parametry Image byly stejného případu jako u vydavatele, nabídky a SKU.

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/129536438/home -->
- Opravili jsme problém se selháním zálohování s chybovou zprávou **PartialSucceeded** z důvodu vypršení časových limitů při zálohování metadat služby úložiště.  

- Opravili jsme problém, kdy výsledkem odstranění předplatných uživatelů byly osamocené prostředky.

- Opravili jsme problém, kdy pole Popis nebyl při vytváření nabídky uložené.

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

Informace o aktualizacích zabezpečení v této aktualizaci Azure Stack najdete v tématu [Azure Stack aktualizace zabezpečení](release-notes-security-updates.md).

## <a name="update-planning"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Známé problémy](known-issues.md)
- [Aktualizace zabezpečení](release-notes-security-updates.md)
- [Kontrolní seznam aktivit před a po instalaci aktualizace](release-notes-checklist.md)

## <a name="download-the-update"></a>Stáhnout aktualizaci

Balíček aktualizace Azure Stack 1907 můžete stáhnout ze [stránky pro stažení Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack pravidelně vydává opravy hotfix. Před aktualizací Azure Stack na 1907 nezapomeňte nainstalovat nejnovější Azure Stack opravu hotfix pro 1906.

Azure Stack oprav hotfix se týkají pouze Azure Stack integrovaných systémů. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="before-applying-the-1907-update"></a>Před použitím aktualizace 1907

Vydání verze 1907 Azure Stack se musí použít ve verzi 1906 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1906.15.60](https://support.microsoft.com/help/4524559)

### <a name="after-successfully-applying-the-1907-update"></a>Po úspěšné instalaci aktualizace 1907

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1907.18.56](https://support.microsoft.com/help/4528552)
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-build-reference"></a>1906 – odkaz na sestavení

Číslo buildu aktualizace Azure Stack 1906 je **1.1906.0.30**.

### <a name="update-type"></a>Typ aktualizace

Typ sestavení aktualizace Azure Stack 1906 je **Express**. Další informace o typech sestavení aktualizace naleznete v článku [Správa aktualizací v Azure Stack](azure-stack-updates.md) . Očekávaný čas potřebný k dokončení aktualizace 1906 je přibližně 10 hodin, bez ohledu na počet fyzických uzlů v prostředí Azure Stack. Přesné běhové moduly pro aktualizace budou obvykle záviset na kapacitě používané v systému podle zatížení klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a specifikacemi hardwaru systému. Běhové moduly trvající déle než očekávaná hodnota nejsou běžné a nevyžadují akci Azure Stack operátory, pokud se aktualizace nezdařila. Tato přibližná doba běhu je specifická pro aktualizaci 1906 a neměla by se porovnávat s jinými aktualizacemi Azure Stack.

## <a name="whats-in-this-update"></a>Co je v této aktualizaci

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Do privilegovaného koncového bodu (PEP) se přidala rutina **set-TLSPolicy** , která vynutí TLS 1,2 u všech koncových bodů. Další informace najdete v tématu [Azure Stack kontroly zabezpečení](azure-stack-security-configuration.md).

- Do privilegovaného koncového bodu (PEP) se přidala rutina **Get-TLSPolicy** , která načte použité zásady TLS. Další informace najdete v tématu [Azure Stack kontroly zabezpečení](azure-stack-security-configuration.md).

- Přidání interního postupu pro rotaci tajných kódů pro otočení vnitřních certifikátů TLS podle požadavků při aktualizaci systému.

- Přidání ochrany, která zabraňuje vypršení platnosti interních tajných kódů tím, že vynucuje interní rotaci tajných kódů pro případ, že kritická výstraha na vypršení platnosti Tato činnost by se neměla spoléhat jako na běžný operační postup. Rotace tajných kódů by měla být naplánována během časového období údržby. Další informace najdete v tématu [Azure Stack rotaci tajných klíčů](azure-stack-rotate-secrets.md).

- Visual Studio Code se teď podporuje s nasazením Azure Stack pomocí AD FS.

### <a name="improvements"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Rutina **Get-GraphApplication** v privilegovaném koncovém bodu teď zobrazuje kryptografický otisk aktuálně používaného certifikátu. To zlepšuje správu certifikátů pro instanční objekty, když Azure Stack nasadíte pomocí AD FS.

- Byla přidána nová pravidla monitorování stavu, která ověřují dostupnost služby AD Graph a AD FS, včetně možnosti vyvolat výstrahy.

- Vylepšení spolehlivosti poskytovatele prostředků zálohování, když se služba infrastruktura zálohování přesune na jinou instanci.

- Optimalizace výkonu pro postup při rotaci externích tajných klíčů, který poskytuje jednotnou dobu provádění, která usnadňuje plánování časového období údržby.

- Rutina **test-AzureStack** nyní hlásí interní tajné klíče, jejichž platnost brzy vyprší (kritické výstrahy).

- V rámci privilegovaného koncového bodu je k dispozici nový parametr pro rutinu **Register-CustomAdfs** , která umožňuje přeskočí kontrolu seznamu odvolaných certifikátů při konfiguraci důvěryhodnosti federace pro AD FS.

- Vydání 1906 přináší lepší přehled o průběhu aktualizace, takže můžete mít jistotu, že se aktualizace nepozastavuje. Výsledkem je zvýšení celkového počtu kroků aktualizace zobrazených u operátorů v okně **aktualizace** . Můžete si také všimnout dalších kroků aktualizace, které probíhají paralelně než v předchozích aktualizacích.

#### <a name="networking-updates"></a>Aktualizace sítě

- Byla aktualizována doba zapůjčení nastavená v respondéru DHCP, aby byla konzistentní s Azure.

- Vylepšené sazby opakování pro poskytovatele prostředků ve scénáři selhání nasazení prostředků.

- Odebrali jsme možnost **Standard** SKU z nástroje pro vyrovnávání zatížení i z veřejné IP adresy, protože se v tuto chvíli nepodporuje.

### <a name="changes"></a>Provedeny

- Vytvoření prostředí účtu úložiště je teď konzistentní s Azure.

- Aktivační události změněné výstrahy pro vypršení platnosti interních tajných kódů:
  - Upozornění na upozornění se teď zvyšují 90 dní před vypršením platnosti tajných kódů.
  - Kritické výstrahy jsou nyní vyvolány 30 dní před vypršením platnosti tajných kódů.

- Aktualizované řetězce v poskytovateli prostředků zálohování infrastruktury pro konzistentní terminologii.

### <a name="fixes"></a>Řeší

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Opravili jsme problém, kdy změna velikosti virtuálního počítače spravovaného disku selhala s **chybou vnitřní operace**.

- Opravili jsme problém, kdy vytvoření image uživatele, která selhala, umístí službu, která spravuje image, je ve špatném stavu. Tím se odstraní neúspěšné image a vytváření nových imagí. Tato oprava je také opravena v 1905.

- Aktivní výstrahy týkající se vypršení platnosti interních tajných kódů se teď po úspěšném provedení interního přestupnosti automaticky zavřou.

- Opravili jsme problém, ve kterém doba aktualizace na kartě Historie aktualizace ořízne první číslici, pokud byla aktualizace spuštěná déle než 99 hodin.

- Okno **aktualizace** obsahuje možnost **obnovení** pro neúspěšné aktualizace.

- V portálu pro správu a uživatele byly vyřešeny problémy v Marketplace, ve kterých bylo rozšíření Docker nesprávně vráceno ze služby Search, ale nebylo možné provést žádnou další akci, protože není k dispozici v Azure Stack.

- Opravili jsme problém v uživatelském rozhraní nasazení šablony, které neplní parametry, pokud název šablony začíná podtržítkem _.

- Opravili jsme problém, kdy prostředí pro vytváření sady škálování virtuálních počítačů poskytuje CentOS 7,2 jako možnost nasazení. CentOS 7,2 není na Azure Stack k dispozici. Jako naši možnost pro nasazení teď poskytujeme CentOS 7,5.

- V okně **Virtual Machine Scale Sets** teď můžete odebrat sadu škálování.

## <a name="security-updates"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci Azure Stack najdete v tématu [Azure Stack aktualizace zabezpečení](release-notes-security-updates.md).

## <a name="update-planning"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Známé problémy](known-issues.md)
- [Aktualizace zabezpečení](release-notes-security-updates.md)
- [Kontrolní seznam aktivit před a po instalaci aktualizace](release-notes-checklist.md)

## <a name="download-the-update"></a>Stáhnout aktualizaci

Balíček aktualizace Azure Stack 1906 můžete stáhnout ze [stránky pro stažení Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack pravidelně vydává opravy hotfix. Před aktualizací Azure Stack na 1906 nezapomeňte nainstalovat nejnovější Azure Stack opravu hotfix pro 1905. Po aktualizaci nainstalujte všechny [dostupné opravy hotfix pro 1906](#after-successfully-applying-the-1906-update).

Azure Stack oprav hotfix se týkají pouze Azure Stack integrovaných systémů. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="before-applying-the-1906-update"></a>Před použitím aktualizace 1906

Vydání verze 1906 Azure Stack se musí použít ve verzi 1905 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>Po úspěšné instalaci aktualizace 1906

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1906.15.60](https://support.microsoft.com/help/4524559)
::: moniker-end

::: moniker range=">=azs-1906"
## <a name="automatic-update-notifications"></a>Oznámení automatických aktualizací

Systémy, které mají přístup k Internetu ze sítě infrastruktury, uvidí na portálu operátora zprávu **k aktualizaci k dispozici** . Systémy bez přístupu k Internetu můžou stáhnout a naimportovat soubor. zip s odpovídající příponou. XML.

> [!TIP]  
> Přihlaste se k odběru následujících informačních kanálů *RSS* nebo *Atom* , abyste zachovali Azure Stack opravy hotfix:
>
> - [OBSAH](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Počtu](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archive"></a>Archiv

Pokud chcete získat přístup k archivovaným dokumentům k verzi pro starší verzi, použijte rozevírací nabídku selektor verzí nad obsahem vlevo a vyberte verzi, kterou chcete zobrazit.

## <a name="next-steps"></a>Další kroky

- Přehled správy aktualizací v Azure Stack najdete v tématu [Správa aktualizací v Azure Stack přehledu](azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace s Azure Stack, najdete v tématu věnovaném [použití aktualizací v Azure Stack](azure-stack-apply-updates.md).
- Informace o zásadách obsluhy pro Azure Stack integrovaných systémech a o tom, co je potřeba udělat, abyste zachovali svůj systém v podporovaném stavu, najdete v tématu [zásady pro obsluhu Azure Stack](azure-stack-servicing-policy.md).  
- Postup pro monitorování a obnovení aktualizací pomocí privilegovaného koncového bodu (PEP) najdete v tématu [monitorování aktualizací v Azure Stack pomocí privilegovaného koncového bodu](azure-stack-monitor-update.md).
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
Do Galerie TechNet můžete získat přístup ke [starším verzím Azure Stack](https://aka.ms/azsarchivedrelnotes). Tyto archivované dokumenty jsou k dispozici pouze pro referenční účely a neznamenají podporu těchto verzí. Informace o podpoře Azure Stack najdete v tématu [zásady pro obsluhu Azure Stack](azure-stack-servicing-policy.md). Pokud potřebujete další pomoc, obraťte se na službu zákaznické podpory společnosti Microsoft.
::: moniker-end


