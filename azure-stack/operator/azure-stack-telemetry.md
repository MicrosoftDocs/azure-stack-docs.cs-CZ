---
title: Konfigurace telemetrie centra Azure Stack
titleSuffix: Azure Stack
description: Přečtěte si o telemetrie centra Azure Stack a o tom, jak nakonfigurovat nastavení telemetrie pomocí PowerShellu.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 1/16/2020
ms.author: inhenkel
ms.reviewer: comartin
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: 6bc12104ef8ce325fe9b1773373ef235a33919e5
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "77696953"
---
# <a name="configure-azure-stack-hub-telemetry"></a>Konfigurace telemetrie centra Azure Stack

Telemetrii centra Azure Stack automaticky odesílá systémová data společnosti Microsoft prostřednictvím prostředí připojeného uživatele. Microsoft Teams používá data, která Azure Stack telemetrii centra shromažďují pro zlepšení zkušeností zákazníků. Tato data se taky používají k analýze zabezpečení, stavu, kvality a výkonu.

V případě operátoru centra Azure Stack může telemetrie poskytovat cenné přehledy o podnikových nasazeních a poskytuje hlas, který pomáhá vytvarovat budoucí verze centra Azure Stack.

> [!NOTE]
> Můžete také nakonfigurovat centrum Azure Stack k přeposílání informací o využití do Azure pro účely fakturace. Tato možnost je vyžadována pro zákazníky s více Azure Stack uzly, kteří používají účtování s průběžnými platbami. Vytváření sestav o využití se řídí nezávisle na telemetrie a není vyžadováno pro zákazníky s více uzly, kteří volí model kapacity nebo pro Azure Stack Development Kit uživatele. V těchto scénářích lze vytváření sestav využití vypnout [pomocí registračního skriptu](azure-stack-usage-reporting.md).

Telemetrie centra Azure Stack je založená na komponentě Windows Server 2016 s připojeným uživatelským prostředím a telemetrie. Tato součást používá technologii [trasování událostí pro Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) TraceLogging k shromažďování a ukládání událostí a dat. Azure Stack komponenty používají stejnou technologii pro publikování událostí a dat shromážděných pomocí protokolu událostí veřejného operačního systému a rozhraní API pro trasování. Mezi tyto součásti Azure Stack centra patří například tito poskytovatelé: síťový prostředek, prostředek úložiště, monitorovací prostředek a prostředek aktualizace. Rozhraní připojené uživatele a komponenta telemetrie šifrují data pomocí protokolu SSL a pomocí připnutí certifikátů odesílá data přes HTTPS službě Microsoft Správa dat Service.

> [!IMPORTANT]
> Aby bylo možné tok dat telemetrie povolit, musí být ve vaší síti otevřený port 443 (HTTPS). Komponenta s připojeným uživatelským prostředím a telemetrie se připojí ke službě `https://v10.events.data.microsoft.com`Microsoft Správa dat na adrese. Prostředí připojené uživatele a komponenta telemetrie se také připojí `https://settings-win.data.microsoft.com` ke stažení informací o konfiguraci. Další služby diagnostických dat `https://watson.telemetry.microsoft.com` se připojují pro zasílání zpráv o chybách.

## <a name="privacy-considerations"></a>Aspekty ochrany osobních údajů

Služba ETW směruje data telemetrie zpátky do chráněného cloudového úložiště. Objekt zabezpečení nejnižších průvodců oprávnění přistupuje k datům telemetrie. Přístup k datům telemetrie mají jenom pracovníci Microsoftu s platnými obchodními potřebami. Společnost Microsoft nesdílí osobní údaje o zákaznících s třetími stranami s výjimkou rozhodnutí zákazníka nebo omezeného účelu popsaného v [prohlášení společnosti Microsoft o zásadách ochrany osobních údajů](https://privacy.microsoft.com/PrivacyStatement). Obchodní sestavy, které jsou sdíleny s výrobci OEM a partnery, zahrnují agregovaná, anonymitná data. Rozhodnutí o sdílení dat provádí interní tým Microsoftu, včetně zúčastněných stran ochrany osobních údajů, právních předpisů a správy dat.

Společnost Microsoft se domnívá, že se jedná o postupy a jejich minimalizaci. Snažíme se shromáždit jenom potřebné informace a uložit je jenom tak dlouho, jak je potřeba k poskytování služby nebo k analýze. Většina informací o fungování systému centra Azure Stack a služeb Azure se odstraní do šesti měsíců. Souhrnná nebo agregovaná data budou uchována po delší dobu.

Chápeme, že ochrana osobních údajů a zabezpečení zákaznických informací je důležitá.  Microsoft využívá důkladné a komplexní přístup k ochraně osobních údajů zákazníků a ochraně zákaznických dat v centru Azure Stack. Správci IT mají k dispozici ovládací prvky pro přizpůsobení funkcí a nastavení ochrany osobních údajů. Náš závazek na transparentnost a důvěryhodnost je jasný:

- Otevíráme zákazníky o typech shromažďovaných dat.
- Podnikoví zákazníci umísťují řízení – můžou si přizpůsobit svoje vlastní nastavení ochrany osobních údajů.
- Nejdřív uvádíme ochranu osobních údajů a zabezpečení zákazníků.
- Transparentní je, jak se data telemetrie používají.
- Data telemetrie používáme ke zlepšování zkušeností zákazníků.

Společnost Microsoft nemá v úmyslu shromažďovat citlivá data, jako jsou čísla kreditních karet, uživatelská jména a hesla, e-mailové adresy nebo podobné citlivé informace. Pokud zjistíme, že se nechtěně přijaly citlivé informace, odstraníme je.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Příklady toho, jak Microsoft používá data telemetrie

Telemetrii hraje důležitou roli, která pomáhá rychle identifikovat a opravit kritické problémy spolehlivosti v nasazeních a konfiguracích zákazníků. Poznatky z dat telemetrie vám můžou pomáhat identifikovat problémy se službami nebo konfiguracemi hardwaru. Schopnost získat tato data od zákazníků a na základě vylepšení v ekosystému vyvolá panel s kvalitou integrovaných řešení Azure Stack centra.

Telemetrie taky pomáhá Microsoftu lépe pochopit, jak zákazníci nasazují komponenty, využívají funkce a využívají služby k dosažení svých obchodních cílů. Tyto přehledy vám pomůžou určit prioritu technologických investic v oblastech, které mohou přímo ovlivnit prostředí a úlohy zákazníků.

Mezi příklady patří zákaznické použití kontejnerů, úložišť a síťových konfigurací, které jsou přidružené k Azure Stack rolím centra. K vylepšení a inteligentním řešením Azure Stack správy a monitorování centra používáme také přehledy. Tato vylepšení usnadňují zákazníkům diagnostikovat problémy a ušetřit peníze tím, že v Microsoftu vyvolají méně volání podpory.

## <a name="manage-telemetry-collection"></a>Správa kolekce telemetrie

Nedoporučujeme vypínat telemetrii ve vaší organizaci. V některých případech ale může být potřeba.

V těchto scénářích můžete nakonfigurovat úroveň telemetrie odeslanou společnosti Microsoft pomocí nastavení registru před nasazením centra Azure Stack nebo pomocí koncových bodů telemetrie po nasazení Azure Stack centra.

### <a name="telemetry-levels-and-data-collection"></a>Úrovně telemetrie a shromažďování dat

Před změnou nastavení telemetrie byste měli pochopit úrovně telemetrie a shromažďovaná data na jednotlivých úrovních.

Nastavení telemetrie se seskupují na čtyři úrovně (0-3), které jsou kumulativní a kategorizované následujícím způsobem:

**0 (zabezpečení)**</br>
Pouze data zabezpečení. Informace, které jsou potřebné k zajištění zabezpečení operačního systému. To zahrnuje data o nastavení komponenty prostředí pro připojené uživatele a telemetrie a o programu Windows Defender. Na této úrovni se nevysílá žádná telemetrie specifická pro Azure Stack hub.

**1 (základní)**</br>
Data zabezpečení a základní data o stavu a kvalitě. Základní informace o zařízení, včetně dat týkajících se kvality, kompatibility aplikací, dat o využití aplikací a dat z úrovně **zabezpečení** . Nastavení úrovně telemetrie na Basic umožňuje telemetrii centra Azure Stack. Data shromážděná na této úrovni zahrnují:

- *Základní informace o zařízení* , které poskytují porozumění typům a konfiguracím nativních a virtuálních instancí Windows Server 2016 v ekosystému To zahrnuje:

  - Atributy počítače, jako je například výrobce OEM a model.
  - Síťové atributy, například počet síťových adaptérů a jejich rychlost.
  - Atributy procesoru a paměti, jako je počet jader a množství nainstalované paměti.
  - Atributy úložiště, například počet jednotek, typ jednotky a velikost jednotky.

- *Funkce telemetrie*, včetně procenta nahraných událostí, vyřazených událostí a času posledního nahrávání dat.
- *Informace související s kvalitou* , které pomáhají Microsoftu vyvíjet základní znalosti o tom, jak probíhá Azure Stack hub. Například počet kritických výstrah pro konkrétní hardwarovou konfiguraci.
- *Data kompatibility* , která pomáhají poskytnout informace o tom, které poskytovatelé prostředků jsou nainstalovaná v systému a virtuálním počítači (VM). Tato možnost identifikuje potenciální problémy s kompatibilitou.

**2 (rozšířené)**</br>
Další přehledy, včetně: jak se používají služby operačního systému a Azure Stack hub, jak tyto služby provádějí, pokročilá data o spolehlivosti a data z úrovní **zabezpečení** a úrovně **Basic** .

> [!NOTE]
> Toto je výchozí nastavení telemetrie.

**3 (plná)**</br>
Veškerá data potřebná pro identifikaci a pomoc při řešení problémů a data ze **zabezpečení**, úrovně **Basic**a **vylepšené** úrovně.

> [!IMPORTANT]
> Tyto úrovně telemetrie se vztahují jenom na komponenty centra Microsoft Azure Stack. Softwarové komponenty a služby od jiných výrobců než od Microsoftu, které jsou spuštěné v rámci životního cyklu hardwarových partnerů od Azure Stack hub mohou komunikovat s Cloud Services mimo tyto úrovně telemetrie. Měli byste pracovat s vaším poskytovatelem hardwarových řešení Azure Stack, abyste pochopili jejich zásady telemetrie a jak se můžete rozhodnout nebo odhlásit.

Vypnutí telemetrie Windows a centra Azure Stack taky zakáže telemetrii SQL. Další informace o důsledcích nastavení telemetrie Windows serveru najdete na stránce [White paper o telemetrie Windows](https://aka.ms/winservtelemetry).

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK: Nastavte úroveň telemetrie v registru Windows.

Můžete použít Editor registru Windows k ručnímu nastavení úrovně telemetrie na fyzickém hostitelském počítači před nasazením centra Azure Stack. Pokud zásada správy již existuje, například Zásady skupiny, přepíše toto nastavení registru.

Před nasazením centra Azure Stack na hostiteli vývojové sady spusťte nástroj CloudBuilder. vhdx a spusťte následující skript v okně PowerShellu se zvýšenými oprávněními:

```powershell
### Get current AllowTelemetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK a vícenásobný uzel: povolení nebo zakázání telemetrie po nasazení

Pokud chcete povolit nebo zakázat telemetrii po nasazení, potřebujete přístup k privilegovanému koncovému bodu (PEP), který je vystavený na virtuálních počítačích ERCS.

- Povolení:`Set-Telemetry -Enable`
- Chcete-li zakázat:`Set-Telemetry -Disable`

Podrobnosti parametru:
- `.PARAMETER Enable`-Zapnout nahrávání dat telemetrie
- `.PARAMETER Disable`-Vypnout nahrávání dat telemetrie  

**Skript pro povolení telemetrie:**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Enable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

**Skript pro vypnutí telemetrie:**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Disable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

## <a name="next-steps"></a>Další kroky

[Registrace centra Azure Stack s Azure](azure-stack-registration.md)