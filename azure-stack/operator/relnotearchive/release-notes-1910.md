---
title: Zpráva k vydání verze centra Azure Stack 1910
description: zpráva k vydání verze 1910 pro integrované systémy Azure Stack hub, včetně aktualizací a oprav chyb.
author: sethmanheim
ms.topic: article
ms.date: 01/25/2021
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ROBOTS: NOINDEX
ms.openlocfilehash: ced055a2983fb4aabb1c31f314ccb4587f3801cc
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248107"
---
# <a name="azure-stack-hub-1910-release-notes"></a>Zpráva k vydání verze centra Azure Stack 1910

Tento článek popisuje obsah balíčků aktualizací centra Azure Stack. Tato aktualizace zahrnuje vylepšení a opravy pro nejnovější vydání centra Azure Stack.

> [!IMPORTANT]  
> Tento balíček aktualizace je určen pouze pro integrované systémy Azure Stack hub. Tento balíček aktualizace nepoužívejte na Azure Stack Development Kit (ASDK).

> [!IMPORTANT]  
> Pokud je vaše instance centra Azure Stack za více než dvěma aktualizacemi, je považována za nedodržující předpisy. Aby bylo možné [získat podporu, musíte aktualizovat aspoň minimální podporovanou verzi](../azure-stack-servicing-policy.md#keep-your-system-under-support).

## <a name="update-planning"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Kontrolní seznam aktivit před a po instalaci aktualizace](../release-notes-checklist.md)
- [Známé problémy](../known-issues.md)
- [Opravy hotfix](#hotfixes)
- [Aktualizace zabezpečení](../release-notes-security-updates.md)

Nápovědu k řešení potíží s aktualizacemi a procesu aktualizace najdete v tématu řešení potíží se [opravami a aktualizacemi pro centrum Azure Stack](../azure-stack-troubleshooting.md).

## <a name="download-the-update"></a>Stáhnout aktualizaci

Balíček aktualizace centra Azure Stack můžete stáhnout pomocí [nástroje Azure Stack Center Update stahovacího](https://aka.ms/azurestackupdatedownload)programu.

## <a name="1910-build-reference"></a>1910 – odkaz na sestavení

Číslo buildu aktualizace centra Azure Stack 1910 je **1.1910.0.58**.

### <a name="update-type"></a>Typ aktualizace

Počínaje 1908 se příslušný operační systém, na kterém Azure Stack centra spouští, aktualizoval na Windows Server 2019. Tato aktualizace umožňuje základní základní vylepšení a možnost přinášet další možnosti do centra Azure Stack.

Typ sestavení aktualizace centra Azure Stack 1910 je **Express**.

Balíček aktualizace 1910 je v porovnání s předchozími aktualizacemi větší, což má za následek delší dobu stahování. Tato aktualizace zůstane ve stavu **Příprava** pro dlouhou dobu a operátoři můžou očekávat, že tento proces trvá déle než předchozí aktualizace. Očekávaná doba dokončení aktualizace 1910 je přibližně 10 hodin, bez ohledu na počet fyzických uzlů ve vašem prostředí Azure Stack hub. Přesné běhové moduly pro aktualizaci jsou obvykle závislé na kapacitě používané v systému podle zatížení klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a specifikacemi hardwaru systému. Běhové moduly trvající déle než očekávaná hodnota nejsou běžné a nevyžadují akci Azure Stack operátory centra, pokud se aktualizace nezdařila. Tato přibližná doba běhu je specifická pro aktualizaci 1910 a neměla by se porovnávat s jinými aktualizacemi centra Azure Stack.

Další informace o typech sestavení aktualizací najdete v tématu [Správa aktualizací v centru Azure Stack](../azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novinky

<!-- What's new, also net new experiences and features. -->

- Portál pro správu nyní zobrazuje IP adresy privilegovaného koncového bodu v nabídce Vlastnosti oblasti pro snazší zjišťování. Kromě toho zobrazuje aktuální nakonfigurovaný časový server a servery DNS pro přeposílání. Další informace najdete v tématu [Použití privilegovaného koncového bodu ve službě Azure Stack Hub](../azure-stack-privileged-endpoint.md).

- Pokud dojde k chybě, může teď systém monitorování stavu a monitorování centra Azure Stack vyvolat výstrahy pro různé hardwarové součásti. Tyto výstrahy vyžadují další konfiguraci. Další informace najdete v tématu [monitorování hardwarových komponent centra Azure Stack](../azure-stack-hardware-monitoring.md).

- [Podpora Cloud-init pro Azure Stack hub](/azure/virtual-machines/linux/using-cloud-init): Cloud-init je široce používaný přístup k přizpůsobení virtuálního počítače se systémem Linux při prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Vzhledem k tomu, že se během procesu prvotního spuštění volá Cloud-init, neexistují žádné další kroky ani nepotřebné agenti pro použití konfigurace. Image Ubuntu na webu Marketplace byly aktualizovány tak, aby podporovaly Cloud-init pro zřizování.

- Centrum Azure Stack nyní podporuje všechny verze agenta Windows Azure Linux jako Azure.

- K dispozici je nová verze modulů PowerShellu pro správce centra Azure Stack. <!-- For more information, see -->

- Od 15. dubna 2020 byly vydány nové moduly tenanta Azure PowerShell pro centrum Azure Stack. Aktuálně používané moduly Azure RM budou fungovat i nadále, ale po sestavení 2002 už se neaktualizují.

- Přidání rutiny **set-AzSDefenderManualUpdate** do privilegovaného koncového bodu (PEP) ke konfiguraci ruční aktualizace definicí programu Windows Defender v infrastruktuře centra Azure Stack. Další informace najdete v tématu [aktualizace antivirové ochrany v programu Windows Defender v centru Azure Stack](../azure-stack-security-av.md).

- Přidání rutiny **set-AzSDnsForwarder** do privilegovaného koncového bodu (PEP) pro změnu nastavení serveru DNS pro server DNS v centru Azure Stack. Další informace o konfiguraci DNS najdete v tématu [Azure Stack integrace služby DNS centra Datacenter](../azure-stack-integrate-dns.md).

- Přidání podpory pro správu **clusterů Kubernetes** pomocí [modulu AKS](../../user/azure-stack-kubernetes-aks-engine-overview.md) Od této aktualizace můžou zákazníci nasazovat produkční clustery Kubernetes. Modul AKS umožňuje uživatelům provádět tyto akce:
  - Spravujte životní cyklus svých clusterů Kubernetes. Můžou vytvářet, aktualizovat a škálovat clustery.
  - Spravujte své clustery pomocí spravovaných imagí vyprodukovaných AKS a týmy centra Azure Stack.
  - Využijte poskytovatele cloudu Kubernetes integrovaného s Azure Resource Manager, který sestaví clustery s využitím nativních prostředků Azure.
  - Nasaďte a spravujte svoje clustery v připojených nebo odpojených Azure Stackch razítkech rozbočovače.
  - Použití hybridních funkcí Azure:
    - Integrace se službou Azure ARC.
    - Integrace s Azure Monitor pro kontejnery.
  - Používejte kontejnery Windows s modulem AKS.
  - Dostávat podpora Microsoftu a technické podpory pro jejich nasazení.

### <a name="improvements"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Centrum Azure Stack zlepšilo svou schopnost automaticky opravovat některé aktualizace a problémy s aktualizací, které dříve způsobily chyby aktualizace, nebo brání operátorům zahájit Azure Stack aktualizace centra. Výsledkem je, že ve skupině **test-AzureStack-UpdateReadiness** je k dispozici méně testů. Další informace najdete v tématu [ověření stavu systému centra Azure Stack](../azure-stack-diagnostic-test.md#groups). Ve skupině **UpdateReadiness** zůstanou tyto tři testy:

  - **AzSInfraFileValidation**
  - **AzSActionPlanStatus**
  - **AzsStampBMCSummary**

- Přidalo se pravidlo auditování, které oznamuje, že externí zařízení (například klíč USB) je připojené k uzlu infrastruktury centra Azure Stack. Protokol auditu se vysílá přes protokol syslog a zobrazí se jako **Microsoft-Windows-Security-audit: 6416 | Technologie Plug and Play události** Další informace o tom, jak nakonfigurovat klienta syslog, najdete v tématu [předávání SYSLOG](../azure-stack-integrate-security.md).

- Rozbočovač Azure Stack se přesouvá do 4096 klíčů RSA pro vnitřní certifikáty. Spuštění interního rotace tajných klíčů nahradí staré 2048 certifikátů pomocí 4096 bitů dlouhých certifikátů. Další informace o rotaci tajných kódů v centru Azure Stack najdete v tématu [otočení tajných kódů v centru Azure Stack](../azure-stack-rotate-secrets.md).

- Provede upgrade na složitost kryptografických algoritmů a složitosti klíčů pro několik interních součástí, aby splňovala předpisy výboru pro národní systémy zabezpečení – zásady 15 (CNSSP-15), které poskytují osvědčené postupy pro používání veřejných standardů pro bezpečné sdílení informací. Mezi vylepšeními je AES256 ověřování protokolem Kerberos a SHA384 pro šifrování pomocí sítě VPN. Další informace o CNSSP-15 najdete na stránce věnované [národním bezpečnostním systémům a zásadám](http://www.cnss.gov/CNSS/issuances/Policies.cfm).

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

   Tyto změny se projeví také ve výchozí dokumentaci k [návrhu protokolu IPSec/IKE](../../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters) .

- Služba infrastruktura zálohování vylepšuje logiku, která počítá požadované místo na zálohování místo spoléhání na pevnou prahovou hodnotu. Služba bude používat velikost zálohování, zásady uchovávání informací, rezervu a aktuální využití externího umístění úložiště k určení, jestli se má pro operátor vyvolat upozornění.

### <a name="changes"></a>Změny

- Při stahování položek z webu Marketplace z Azure do centra Azure Stack je k dispozici nové uživatelské rozhraní, které umožňuje zadat verzi položky, pokud existuje více verzí. Nové uživatelské rozhraní je k dispozici v rámci připojených i odpojených scénářů. Další informace najdete v tématu [stažení položek Marketplace z Azure do centra Azure Stack](../azure-stack-download-azure-marketplace-item.md).  

- Počínaje verzí 1910 **vyžaduje** systém Azure Stack hub další/20 privátní interní IP místo. Další informace najdete v tématu [Plánování integrace sítě pro Azure Stack](../azure-stack-network.md) .
  
- Služba zálohování infrastruktury odstraní částečně nahraná zálohovaná data v případě, že umístění externího úložiště během procesu nahrávání vyčerpá kapacitu.  

- Služba infrastruktura zálohování přidá službu identit do datové části zálohy pro nasazení AAD.  

- Modul PowerShellu centra Azure Stack se aktualizoval na verzi 1.8.0 pro verzi 1910.<br>Zahrnuté změny:
   - **Nový modul DRP pro správu:** Poskytovatel prostředků nasazení (DRP, Deployment Resource Provider) umožňuje orchestrovaná nasazení poskytovatelů prostředků do Azure Stack Hubu. Tyto příkazy komunikují s úrovní Azure Resource Manageru pro interakci s DRP.
   - **BRP**: <br />
           – Podporuje obnovení jedné role pro zálohování infrastruktury Azure Stack. <br />
           – Přidejte parametr `RoleName` do rutiny `Restore-AzsBackup` .
   - **FRP**: zásadní změny prostředků **jednotky** a **svazku** pomocí verze rozhraní API `2019-05-01` . Tyto funkce podporuje Azure Stack Hub 1910 a novější: <br />
            – Hodnota,, `ID` `Name` a byla `HealthStatus` `OperationalStatus` změněna. <br />
            – Podporované nové vlastnosti `FirmwareVersion` , `IsIndicationEnabled` , `Manufacturer` a `StoragePool` pro prostředky **jednotky** . <br />
            – Vlastnosti `CanPool` a `CannotPoolReason` prostředky **jednotky** jsou zastaralé. `OperationalStatus` místo toho použijte.

### <a name="fixes"></a>Opravy

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there's an SR/ICM associated to it. -->

- Opravili jsme problém, který zabránil vynucování zásad TLS 1,2 v prostředích nasazených před vydáním Azure Stack centra 1904.
- Opravili jsme problém, kdy se virtuální počítač s Ubuntu 18,04 vytvořený s povoleným autorizací SSH neumožňuje používat klíče SSH k přihlášení.
- **Heslo pro resetování** se odebralo z uživatelského rozhraní sady škálování virtuálního počítače.
- Opravili jsme problém, kdy odstranění nástroje pro vyrovnávání zatížení z portálu nezpůsobilo odstranění objektu ve vrstvě infrastruktury.
- Opravili jsme problém, který ukázal nepřesné procento výstrahy využití fondu brány na portálu pro správu.
<!-- Fixed an issue where adding more than one public IP on the same NIC on a Virtual Machine resulted in internet connectivity issues. Now, a NIC with two public IPs should work as expected.[This fix actually didn't go in 1910 due to build issues, commenting out until next build (2002) ] -->

## <a name="security-updates"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci centra Azure Stack najdete v tématu [aktualizace zabezpečení centra Azure Stack](../release-notes-security-updates.md).

Zprávu o ohrožení zabezpečení Qualys pro tuto verzi je možné stáhnout z [webu Qualys](https://www.qualys.com/azure-stack/).

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack centrum pravidelně vydává opravy hotfix. Před aktualizací centra Azure Stack na 1910 se ujistěte, že jste nainstalovali nejnovější opravu hotfix centra Azure Stack pro 1908.

> [!NOTE]
> Verze oprav hotfix centra Azure Stack jsou kumulativní; potřebujete jenom nainstalovat nejnovější opravu hotfix, abyste získali všechny opravy, které jsou součástí všech předchozích verzí oprav hotfix pro danou verzi.

Opravy hotfix centra Azure Stack se vztahují pouze na integrované systémy Azure Stack hub. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="prerequisites-before-applying-the-1910-update"></a>Požadavky: před instalací aktualizace 1910

Verze 1910 centra Azure Stack se musí použít ve verzi 1908 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [1.1908.51.133 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4574734)

### <a name="after-successfully-applying-the-1910-update"></a>Po úspěšné instalaci aktualizace 1910

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](../azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [1.1910.63.186 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4574735)
