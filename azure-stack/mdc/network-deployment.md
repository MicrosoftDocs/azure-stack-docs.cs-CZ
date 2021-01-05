---
title: Azure Stack nasazení sítě pro MDC
description: Přečtěte si o Azure Stack nasazení sítě pro zařízení MDC.
author: PatAltimore
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: patricka
ms.reviewer: shisab
ms.lastreviewed: 01/17/2020
ms.openlocfilehash: a0de01ce240248767ebfd8e558a83408c84282be
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872207"
---
# <a name="network-deployment"></a>Nasazení sítě

Toto téma pokrývá přístupová oprávnění k přepínačům MANDÁTu, přiřazování IP adres a dalším úlohám nasazení sítě.

## <a name="plan-configuration-deployment"></a>Plánování nasazení konfigurace

Další části obsahují oprávnění a přiřazení IP adres.

### <a name="physical-switch-access-control-list"></a>Seznam řízení přístupu fyzických přepínačů

K ochraně Azure Stack řešení jsme na přepínačích MANDÁTu implementovali seznamy řízení přístupu (ACL). Tato část popisuje, jak je toto zabezpečení implementováno. V následující tabulce jsou uvedeny zdroje a cíle každé sítě v rámci Azure Stack řešení:

![Diagram seznamů řízení přístupu v přepínačích MANDÁTu](media/network-deployment/acls.png)

Následující tabulka koreluje odkazy seznamu řízení přístupu (ACL) k sítím Azure Stack.

| Interní Správa řadiče pro správu základní desky                            | Provoz je omezený jenom na interní.                                                                                                                                      |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Externí Správa řadiče pro správu základní desky                            | Seznam ACL umožňuje přístup mimo hraniční zařízení.                                                                                                                            |   |   |   |   |   |
| Rozšířené správy úložiště                        | Vyhrazená rozhraní pro správu rozšířeného úložného systému                                                                                                           |   |   |   |   |   |
| Přepnout správu                                  | Rozhraní pro správu vyhrazených přepínačů.                                                                                                                                   |   |   |   |   |   |
| Azure Stack infrastruktura                 | Služby infrastruktury Azure Stack a virtuální počítače s omezenou sítí                                                                                                          |   |   |   |   |   |
| Veřejná infrastruktura Azure Stack (PEP/ERCS) | Azure Stack chráněný koncový bod, Server konzoly pro nouzové zotavení. Zákazník může otevřít seznam ACL a povolit tak provoz do sítě pro správu Datacenter.                        |   |   |   |   |   |
| Tor1,Tor2 RouterIP                           | Rozhraní zpětné smyčky přepínače používaného pro partnerský vztah protokolu BGP mezi SLB a přepínačem a směrovačem. Zákazník bude mít na hranici bránu firewall, aby tyto IP adresy vypnula. |   |   |   |   |   |
| Úložiště                                      | Privátní IP adresy nejsou směrované mimo oblast.                                                                                                                             |   |   |   |   |   |
| Interní VIP                                | Privátní IP adresy nejsou směrované mimo oblast.                                                                                                                             |   |   |   |   |   |
| Veřejné virtuální IP adresy                                  | Adresní prostor klientské sítě, který je spravovaný síťovým adaptérem.                                                                                                           |   |   |   |   |   |
| VIP – veřejné správy                            | Malá podmnožina adres ve fondu tenantů, které jsou nutné pro komunikaci s Internal-VIPs a Azure Stack infrastruktury                                                    |   |   |   |   |   |
| Povolené sítě                           | Síť definovaná zákazníkem.                                                                                                                                                 |   |   |   |   |   |
| 0.0.0.0                                      | V perspektivě Azure Stack 0.0.0.0 je hraniční zařízení.                                                                                                         |   |   |   |   |   |
| **Uvádění**                                   | Povolení provozu je povolené, ale ve výchozím nastavení je zakázaný přístup přes SSH.                                                                                                           |   |   |   |   |   |
| **Žádná trasa**                                     | Trasy nejsou šířeny mimo Azure Stack prostředí.                                                                                                         |   |   |   |   |   |
| **SEZNAM ACL PRO MUX**                                      | Jsou využívány seznamy ACL pro Azure Stack MUX.                                                                                                                                       |   |   |   |   |   |
| **–**                                          | Nejedná se o součást seznamu ACL sítě VLAN.                                                                                                                                                 |   |   |   |   |   |
|                                              |                                                                                                                                                                           |   |   |   |   |   |

### <a name="ip-address-assignments"></a>Přiřazení IP adres

V listu nasazení budete požádáni o zadání následujících síťových adres pro podporu procesu nasazení Azure Stack. Tým nasazení používá nástroj list nasazení k přerušení sítí protokolu IP ve všech menších sítích vyžadovaných systémem.

V tomto příkladu vyplníme kartu nastavení sítě v listu nasazení s následujícími hodnotami:

-   Síť BMC: 10.193.132.0/27

-   Interní virtuální IP adresy v & síti úložiště privátní sítě: 11.11.128.0/20

-   Síť infrastruktury: 12.193.130.0/24

-   Veřejná virtuální IP (VIP) síť: 13.200.132.0/24

-   Přepnout síť infrastruktury: 10.193.132.128/26

Když spustíte funkci Generate v nástroji pro nasazení listu, vytvoří se v tabulce dvě nové karty. První karta je souhrn podsítě a ukazuje, jakým způsobem byly sítě rozdělené, aby se vytvořily všechny sítě, které systém vyžaduje. V našem příkladu níže je na této kartě nalezeno pouze podmnožina sloupců. Skutečný výsledek obsahuje další podrobnosti o každé síti v seznamu:

| **Stojan** | **Typ podsítě** | **Název**                                   | **Podsíť IPv4**   | **Adresy IPv4** |
|----------|-----------------|--------------------------------------------|-------------------|--------------------|
| Ohraničení   | Propojení P2P        | P2P_Border/Border1_To_Rack1/TOR1           | 10.193.132.128/30 | 4                  |
| Ohraničení   | Propojení P2P        | P2P_Border/Border1_To_Rack1/TOR2           | 10.193.132.132/30 | 4                  |
| Ohraničení   | Propojení P2P        | P2P_Border/Border2_To_Rack1/TOR1           | 10.193.132.136/30 | 4                  |
| Ohraničení   | Propojení P2P        | P2P_Border/Border2_To_Rack1/TOR2           | 10.193.132.140/30 | 4                  |
| Ohraničení   | Propojení P2P        | P2P_Rack1/TOR1_To_Rack1/BMC                | 10.193.132.144/30 | 4                  |
| Ohraničení   | Propojení P2P        | P2P_Rack1/TOR2_To_Rack1/BMC                | 10.193.132.148/30 | 4                  |
| Rack1    | Zpětné smyčky        | Loopback0_Rack1_TOR1                       | 10.193.132.152/32 | 1                  |
| Rack1    | Zpětné smyčky        | Loopback0_Rack1_TOR2                       | 10.193.132.153/32 | 1                  |
| Rack1    | Zpětné smyčky        | Loopback0_Rack1_BMC                        | 10.193.132.154/32 | 1                  |
| Rack1    | Propojení P2P        | P2P_Rack1/TOR1-ibgp-1_To_Rack1/TOR2-ibgp-1 | 10.193.132.156/30 | 4                  |
| Rack1    | Propojení P2P        | P2P_Rack1/TOR1-ibgp-2_To_Rack1/TOR2-ibgp-2 | 10.193.132.160/30 | 4                  |
| Rack1    | REŽIM            | BMCMgmt                                    | 10.193.132.0/27   | 32                 |
| Rack1    | REŽIM            | SwitchMgmt                                 | 10.193.132.168/29 | 8                  |
| Rack1    | REŽIM            | CL01-RG01-SU01-Storage                     | 11.11.128.0/25    | 128                |
| Rack1    | REŽIM            | CL01-RG01-SU01 – infračervené                       | 12.193.130.0/24   | 256                |
| Rack1    | Ostatní           | CL01-RG01-SU01-VIP                        | 13.200.132.0/24   | 256                |
| Rack1    | Ostatní           | CL01-RG01-SU01-InternalVIPS                | 11.11.128.128/25  | 128                |

Druhá karta využívá **využití IP adres** a ukazuje, jak se spotřebovávají IP adresy:

#### <a name="bmc-network"></a>Síť řadiče pro správu základní desky

Tuto nadsíť pro síť řadiče pro správu základní desky vyžaduje minimálně síť/26. Brána používá první IP adresu v síti, po které následuje zařízení řadiče pro správu základní desky v racku. Hostitel životního cyklu hardwaru má k této síti přiřazeno několik adres a dá se použít k nasazení, monitorování a podpoře racku. Tyto IP adresy jsou distribuované do 3 skupin: DVM, InternalAccessible a ExternalAccessible.

- Stojan: Rack1         
- Název: BMCMgmt      

| **Přiřazený pro**      | **Adresa IPv4** |
|----------------------|------------------|
| Síť              | 10.193.132.0     |
| brána              | 10.193.132.1     |
| HLH – BMC              | 10.193.132.2     |
| AzS-Node01           | 10.193.132.3     |
| AzS-Node02           | 10.193.132.4     |
| AzS-Node03           | 10.193.132.5     |
| AzS-Node04           | 10.193.132.6     |
| ExternalAccessible-1 | 10.193.132.19    |
| ExternalAccessible – 2 | 10.193.132.20    |
| ExternalAccessible-3 | 10.193.132.21    |
| ExternalAccessible-4 | 10.193.132.22    |
| ExternalAccessible-5 | 10.193.132.23    |
| InternalAccessible-1 | 10.193.132.24    |
| InternalAccessible – 2 | 10.193.132.25    |
| InternalAccessible-3 | 10.193.132.26    |
| InternalAccessible-4 | 10.193.132.27    |
| InternalAccessible-5 | 10.193.132.28    |
| CL01-RG01-SU01-DVM00 | 10.193.132.29    |
| HLH – OS               | 10.193.132.30    |
| To            | 10.193.132.31    |

#### <a name="storage-network"></a>Síť úložiště

Síť úložiště je privátní síť a není určená k směrování nad rámec racku. Je to první polovina tuto nadsíť privátní sítě a používá se v přepínači distribuovaném, jak je znázorněno v následující tabulce. Brána je první IP adresa v podsíti. Druhá polovina použitá pro interní VIP je privátní fond adres, který je spravovaný nástrojem Azure Stack SLB, se na kartě využití IP adres nezobrazuje. Tyto sítě podporují Azure Stack a v přepínačích MANDÁTu jsou seznamy ACL, které brání inzerování nebo přístupu k těmto sítím mimo řešení.

- Stojan: Rack1                                    
- Název: CL01-RG01-SU01-Storage 

| **Přiřazený pro**              | **Adresa IPv4** |
|------------------------------|------------------|
| Síť                      | 11.11.128.0      |
| brána                      | 11.11.128.1      |
| TOR1                         | 11.11.128.2      |
| TOR2                         | 11.11.128.3      |
| To                    | 11.11.128.127    |

#### <a name="azure-stack-infrastructure-network"></a>Síť Azure Stack infrastruktury

Síť infrastruktury tuto nadsíť vyžaduje síť/24 a ta bude po spuštění nástroje listu nasazení stále a/24. Brána bude první IP adresa v podsíti.

- Stojan: Rack1               
- Název: CL01-RG01-SU01-infračervené 

| **Přiřazený pro**            | **Adresa IPv4** |
|----------------------------|------------------|
| Síť                    | 12.193.130.0     |
| brána                    | 12.193.130.1     |
| TOR1                       | 12.193.130.2     |
| TOR2                       | 12.193.130.3     |
| To                  | 12.193.130.255   |

#### <a name="switch-infrastructure-network"></a>Přepnout síť infrastruktury

Síť infrastruktury je rozdělená do několika sítí používaných infrastrukturou fyzického přepínače. To se liší od infrastruktury Azure Stack, která podporuje jenom Azure Stack software. Infračervená síť přepínače podporuje jenom infrastrukturu fyzického přepínače. Sítě, které jsou podporovány v rámci infračerveného navýšení:

| **Název**                                   | **Podsíť IPv4**   |
|--------------------------------------------|-------------------|
| P2P_Border/Border1_To_Rack1/TOR1           | 10.193.132.128/30 |
| P2P_Border/Border1_To_Rack1/TOR2           | 10.193.132.132/30 |
| P2P_Border/Border2_To_Rack1/TOR1           | 10.193.132.136/30 |
| P2P_Border/Border2_To_Rack1/TOR2           | 10.193.132.140/30 |
| P2P_Rack1/TOR1_To_Rack1/BMC                | 10.193.132.144/30 |
| P2P_Rack1/TOR2_To_Rack1/BMC                | 10.193.132.148/30 |
| Loopback0_Rack1_TOR1                       | 10.193.132.152/32 |
| Loopback0_Rack1_TOR2                       | 10.193.132.153/32 |
| Loopback0_Rack1_BMC                        | 10.193.132.154/32 |
| P2P_Rack1/TOR1-ibgp-1_To_Rack1/TOR2-ibgp-1 | 10.193.132.156/30 |
| P2P_Rack1/TOR1-ibgp-2_To_Rack1/TOR2-ibgp-2 | 10.193.132.160/30 |
| SwitchMgmt                                 | 10.193.132.168/29 |
|                                            |                   |

-   Point-to-Point (P2P): tyto sítě umožňují připojení mezi všemi přepínači. Velikost podsítě je síť/30 pro každý P2P. Nejnižší IP adresa je vždy přiřazena k nadřazenému (severnímu) zařízení v zásobníku.

-   Zpětná smyčka: tyto adresy jsou sítě/32, které jsou přiřazeny ke každému přepínači použitému v racku. Na hraničních zařízeních nejsou přiřazena žádná zpětná smyčka, protože se neočekávají jako součást Azure Stackho řešení.

-   Přepnutí správy nebo správy přepínačů: Tato/29 síť podporuje vyhrazená rozhraní pro správu přepínačů v racku. IP adresy jsou přiřazené následujícím způsobem: tuto tabulku můžete také najít na kartě využití IP adres v listu nasazení:

- Stojan: Rack1      
- Název: SwitchMgmt

| **Přiřazený pro**  | **Adresa IPv4** |
|------------------|------------------|
| Síť          | 10.193.132.168   |
| brána          | 10.193.132.169   |
| TOR1             | 10.193.132.170   |
| TOR2             | 10.193.132.171   |
| To        | 10.193.132.175   |

## <a name="prepare-environment"></a>Příprava prostředí

Bitová kopie hostitele životního cyklu hardwaru obsahuje požadovaný kontejner pro Linux, který se používá ke generování konfigurace přepínače fyzické sítě.

Nejnovější sada partnerských nástrojů pro nasazení zahrnuje nejnovější image kontejneru.
Image kontejneru na hostiteli životního cyklu hardwaru se dá nahradit, když je potřeba vygenerovat aktualizovanou konfiguraci přepínače.

Postup aktualizace image kontejneru:

1.  Stažení Image kontejneru

2.  Nahraďte image kontejneru v následujícím umístění.

## <a name="generate-configuration"></a>Generovat konfiguraci

Tady vás provedeme kroky, jak vygenerovat soubory JSON a konfigurační soubory síťového přepínače:

1.  Otevření listu nasazení

2.  Vyplnit všechna povinná pole na všech kartách

3.  Vyvolejte funkci Generate v listu nasazení.  
    Vytvoří se dvě další karty zobrazující vygenerované podsítě IP a přiřazení.

4.  Zkontrolujte data a jakmile se potvrdí, vyvolejte funkci Export.  
    Zobrazí se výzva k zadání složky, do které se budou ukládat soubory JSON.

5.  Spusťte kontejner pomocí Invoke-SwitchConfigGenerator.ps1. Tento skript vyžaduje spuštění konzoly PowerShellu se zvýšenými oprávněními a vyžaduje, aby byly provedeny následující parametry.

    -   ContainerName – název kontejneru, který bude generovat konfigurace přepínače.

    -   ConfigurationData – cesta k ConfigurationData.jsv souboru exportovaném z listu nasazení.

    -   OutputDirectory – cesta k výstupnímu adresáři.

    -   Offline – signalizuje, že skript běží v offline režimu.

    ```powershell
    C:\WINDOWS\system32> .\Invoke-SwitchConfigGenerate.ps1 -ContainerName generalonrampacr.azurecr.io/master -ConfigurationData .\ConfigurationData.json -OutputDirectory c:\temp -Offline
    ```

Po dokončení skriptu vytvoří soubor zip s předponou použitou v listu. 

```console
C:\WINDOWS\system32> .\Invoke-SwitchConfigGenerate.ps1 -ContainerName generalonrampacr.azurecr.io/master -ConfigurationData .\ConfigurationData.json -OutputDirectory c:\temp -Offline                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         
Seconds : 2
Section : Validation
Step    : WindowsRequirement
Status  : True
Detail  : @{CurrentImage=10.0.18363.0}


Seconds : 2
Section : Validation
Step    : DockerService
Status  : True
Detail  : @{Status=Running}


Seconds : 9
Section : Validation
Step    : DockerSetup
Status  : True
Detail  : @{CPU=4; Memory=4139085824; OS=Docker Desktop; OSType=linux}


Seconds : 9
Section : Validation
Step    : DockerImage
Status  : True
Detail  : @{Container=generalonrampacr.azurecr.io/master:1.1910.78.1}


Seconds : 10
Section : Run
Step    : Container
Status  : True
Detail  : @{ID=2a20ba622ef9f58f9bcd069c3b9af7ec076bae36f12c5653f9469b988c01706c; ExternalPort=32768}


Seconds : 38
Section : Generate
Step    : Config
Status  : True
Detail  : @{OutputFile=c:\temp\N22R19.zip}


Seconds : 38
Section : Exit
Step    : StopContainer
Status  : True
Detail  : @{ID=2a20ba622ef9f58f9bcd069c3b9af7ec076bae36f12c5653f9469b988c01706c}
```

### <a name="custom-configuration"></a>Vlastní konfigurace

Pro konfiguraci přepínače Azure Stack můžete upravit několik nastavení prostředí. Můžete určit, která nastavení můžete v šabloně změnit. Tento článek vysvětluje každé z těchto přizpůsobitelných nastavení a způsob, jakým můžou změny ovlivnit vaše Azure Stack. Mezi tato nastavení patří aktualizace hesla, server syslog, monitorování protokolu SNMP, ověřování a seznam řízení přístupu.

Při nasazení Azure Stack řešení vytvoří výrobce OEM (Original Equipment Manufacturer) a použije konfiguraci přepínače pro tory i BMC. Výrobce OEM používá nástroj Azure Stack Automation k ověření, zda jsou požadované konfigurace na těchto zařízeních správně nastaveny. Konfigurace vychází z informací v listu nasazení Azure Stack. 

>[!NOTE]
>**Neměňte konfiguraci** bez souhlasu výrobce OEM ani Microsoft Azure Stack technický tým. Změna konfigurace síťového zařízení může významně ovlivnit provoz nebo řešení potíží se sítí ve vaší instanci Azure Stack. Pokud chcete získat další informace o těchto funkcích v síťovém zařízení, jak tyto změny provést, obraťte se na dodavatele hardwaru OEM nebo na podporu Microsoftu. Výrobce OEM má konfigurační soubor vytvořený pomocí nástroje pro automatizaci na základě vašeho listu nasazení Azure Stack. 

Existují však některé hodnoty, které lze přidat, odebrat nebo změnit v konfiguraci síťových přepínačů.

#### <a name="password-update"></a>Aktualizace hesla

Operátor může kdykoli aktualizovat heslo pro každého uživatele v síťových přepínačích. Není nutné měnit žádné informace v Azure Stackm systému ani postupovat podle pokynů pro otočení tajných kódů v Azure Stack.

#### <a name="syslog-server"></a>Server syslogu

Operátory mohou přesměrovat protokoly přepínačů na server syslog na svém datacentru.
Pomocí této konfigurace můžete zajistit, aby se protokoly z určitého bodu v čase mohly použít k řešení potíží. Ve výchozím nastavení jsou protokoly uloženy v přepínačích. kapacita pro ukládání protokolů je omezená. Informace o tom, jak nakonfigurovat oprávnění pro přístup ke správě přepínačů, najdete v části aktualizace seznamu řízení přístupu.

#### <a name="snmp-monitoring"></a>Monitorování SNMP

Operátor může nakonfigurovat protokol SNMP (Simple Network Management Protocol) v2 nebo v3 pro monitorování síťových zařízení a odesílání depeší do aplikace monitorování sítě v datacentru. Z bezpečnostních důvodů použijte protokol SNMPv3, protože je bezpečnější než v2. Pokud potřebujete MIB a konfiguraci, obraťte se na svého poskytovatele hardwaru OEM.
Informace o tom, jak nakonfigurovat oprávnění pro přístup ke správě přepínačů, najdete v části aktualizace seznamu řízení přístupu.

#### <a name="authentication"></a>Ověřování

Operátor může nakonfigurovat buď protokol RADIUS, nebo TACACS pro správu ověřování na síťových zařízeních. Podporované metody a konfigurace si můžete prostudovat u svého poskytovatele hardwaru OEM. Informace o tom, jak nakonfigurovat oprávnění pro přístup ke správě přepínačů, najdete v části aktualizace seznamu řízení přístupu.

#### <a name="access-control-list-updates"></a>Aktualizace seznamu řízení přístupu

Operátor může změnit některý seznam řízení přístupu (ACL) s tím, aby povoloval přístup k rozhraním pro správu síťových zařízení a k hostiteli životního cyklu hardwaru (HLH) z rozsahu důvěryhodné sítě Datacenter. Operátor může vybrat, která komponenta bude dosažitelná a kde. V seznamu řízení přístupu může operátor dovolit, aby JumpBox virtuální počítače pro správu v rámci určitého rozsahu sítě pro přístup k rozhraní pro správu přepínače a k rozhraní HLH a HLH BMC.

Další podrobnosti najdete v [seznamu řízení přístupu fyzických přepínačů](#physical-switch-access-control-list).

#### <a name="tacacs-radius-and-syslog"></a>TACACS, RADIUS a syslog

Řešení Azure Stack nebude dodány s řešením typu TACACS nebo RADIUS pro řízení přístupu zařízení, jako jsou přepínače a směrovače, ani řešení syslog pro zachycení protokolů přepnutí, ale všechna tato zařízení podporují tyto služby.
Abychom vám pomohli integrovat s existujícím serverem TACACS, RADIUS a/nebo syslog ve vašem prostředí, poskytneme další soubor s konfigurací síťového přepínače, který umožní technikovi na pracovišti přizpůsobit přepínač potřebám zákazníka.

## <a name="next-steps"></a>Další kroky

[Integrace sítě](network-integration.md)
