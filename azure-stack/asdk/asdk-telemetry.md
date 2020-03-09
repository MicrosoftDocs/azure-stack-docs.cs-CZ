---
title: Telemetrie Azure Stack
description: Přečtěte si, jak nakonfigurovat Azure Stack nastavení telemetrie pomocí prostředí PowerShell.
author: justinha
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: 59fe407c24e10c94d2a0d354c98a1dd13301c7a1
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364057"
---
# <a name="azure-stack-telemetry"></a>Telemetrie Azure Stack

Azure Stack systémová data nebo telemetrie se automaticky nahrají do Microsoftu prostřednictvím prostředí připojeného uživatele. Data shromážděná z telemetrie Azure Stack jsou primárně používána Microsoft Teams ke zlepšení prostředí pro zákazníky. Používá se také k analýze zabezpečení, stavu, kvality a výkonu.

Jako operátor Azure Stack může telemetrie poskytovat cenné přehledy o podnikových nasazeních a poskytuje hlas, který pomáhá vytvarovat budoucí verze Azure Stack.

> [!NOTE]
> Azure Stack taky můžete nakonfigurovat tak, aby se informace o využití přeposlaly do Azure pro účely fakturace. Tato možnost je vyžadována pro zákazníky s více Azure Stack uzly, kteří používají účtování s průběžnými platbami. Vytváření sestav o využití se řídí nezávisle na telemetrie a není vyžadováno pro zákazníky s více uzly, kteří volí model kapacity nebo pro uživatele Azure Stack Development Kit (ASDK). V těchto scénářích lze vytváření sestav využití vypnout [pomocí registračního skriptu](../operator/azure-stack-usage-reporting.md).

Telemetrie Azure Stack je založená na komponentě *Windows serveru 2016 s připojeným uživatelským prostředím a Telemetr*y, která využívá technologii protokolování trasování [událostí pro Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) ke shromažďování a ukládání událostí a dat telemetrie. Azure Stack komponenty používají stejnou protokolovací technologii k publikování událostí a dat, která se shromažďují pomocí rozhraní API pro veřejné protokolování událostí operačního systému a trasování. Mezi příklady součástí Azure Stack patří poskytovatel síťových prostředků, poskytovatel prostředků úložiště, poskytovatel prostředků monitorování a poskytovatel prostředků aktualizací. Rozhraní připojené uživatele a komponenta telemetrie šifrují data pomocí protokolu SSL a pomocí připnutí certifikátů přenáší data telemetrie přes HTTPS do služby Microsoft Správa dat.

> [!NOTE]
> Aby bylo možné podporovat tok dat telemetrie, musí být ve vaší síti otevřený port 443 (HTTPS). Komponenta prostředí připojeného uživatele a telemetrie se připojí ke službě Microsoft Správa dat na https://v10.vortex-win.data.microsoft.com a také https://settings-win.data.microsoft.com ke stažení informací o konfiguraci.

## <a name="privacy-considerations"></a>Požadavky na ochranu osobních údajů
Služba ETW směruje data ze telemetrie zpátky do chráněného cloudového úložiště. Princip nejnižších privilegovaných průvodců přistupuje k datům telemetrie. Přístup k datům telemetrie mají povolený jenom personál Microsoftu s platným obchodním potřebou. Microsoft nesdílí osobní údaje vašeho zákazníka s třetími stranami, s výjimkou rozhodnutí zákazníka nebo omezeného účelu popsaného v [Azure Stack prohlášení o zásadách ochrany osobních údajů](https://privacy.microsoft.com/PrivacyStatement). Obchodní sestavy sdílíme s výrobci OEM a partnery, kteří zahrnují agregované informace o propracované telemetrii. Rozhodnutí o sdílení dat provádí interní tým Microsoftu, včetně zúčastněných stran ochrany osobních údajů, právních předpisů a správy dat.

Microsoft se domnívá, že se jedná o minimalizaci informací a postupů. Snažíme se shromáždit jenom informace, které potřebujeme, a my ji uložíme jenom tak dlouho, jak je potřeba k poskytování služby nebo k analýze. Většina informací o fungování Azure Stack systému a služeb Azure se odstraní do šesti měsíců. Sumarizovaná nebo agregovaná data se uchovávají po delší dobu.

Chápeme, že soukromí a zabezpečení informací o našich zákaznících jsou důležité. Provedli jsme důkladné a komplexní přístup k ochraně osobních údajů zákazníků a ochraně zákaznických dat pomocí Azure Stack. Správci IT mají k dispozici ovládací prvky pro přizpůsobení funkcí a nastavení ochrany osobních údajů. Náš závazek na transparentnost a důvěryhodnost je jasný:
- Otevíráme zákazníky o typech shromažďovaných dat.
- Podnikoví zákazníci umísťují řízení – můžou si přizpůsobit svoje vlastní nastavení ochrany osobních údajů.
- Nejdřív uvádíme ochranu osobních údajů a zabezpečení zákazníků.
- Transparentní je použití telemetrie.
- Telemetrii využíváme ke zlepšování zkušeností zákazníků.

Microsoft nemá v úmyslu shromažďovat citlivé informace, jako jsou třeba čísla kreditních karet, uživatelská jména a hesla, e-mailové adresy. Pokud určíme, že citlivé údaje byly neúmyslně přijaty, odstraníme je.

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Příklady toho, jak Microsoft používá data telemetrie
Telemetrii hraje důležitou roli, která nám pomáhá rychle identifikovat a opravit kritické problémy s spolehlivostí v nasazeních a konfiguracích našich zákazníků. Přehledy o datech telemetrie, které nám nashromáždíme, nám pomůžou rychle identifikovat problémy se službami nebo konfiguracemi hardwaru. Schopnost získat tato data od zákazníků a na základě vylepšení ekosystému pomáhá zvýšit kvalitu našich integrovaných řešení Azure Stack.

Telemetrie taky pomáhá Microsoftu lépe pochopit, jak zákazníci nasazují komponenty, využívají funkce a využívají služby k dosažení svých obchodních cílů. Získání přehledů z těchto dat pomáhá určit prioritu investic do technického prostředí v oblastech, které mohou přímo ovlivnit zkušenosti a úlohy našich zákazníků.

Mezi příklady patří zákaznické využití kontejnerů, úložišť a síťových konfigurací, které jsou přidružené k rolím Azure Stack. K vylepšení a analýzám řešení pro správu a monitorování používáme také přehledy. Toto vylepšení pomáhá zákazníkům diagnostikovat problémy s kvalitou a ušetřit peníze tím, že do Microsoftu napomáhají méně volání podpory.

## <a name="manage-telemetry-collection"></a>Správa kolekce telemetrie
Nedoporučujeme, abyste v organizaci vypnuli telemetrii, protože telemetrie poskytuje data, která zajišťují lepší funkčnost a stabilitu produktu. Rozpoznáme ale, že v některých scénářích to může být potřeba.

V těchto případech můžete nakonfigurovat úroveň telemetrie odeslanou společnosti Microsoft pomocí nastavení registru přednasazení nebo pomocí koncových bodů telemetrie po nasazení.

### <a name="set-telemetry-level-in-the-windows-registry"></a>Nastavení úrovně telemetrie v registru Windows
Editor registru systému Windows slouží k ručnímu nastavení úrovně telemetrie na fyzickém hostitelském počítači před nasazením Azure Stack. Pokud zásada správy již existuje, například Zásady skupiny, přepíše toto nastavení registru.

Před nasazením Azure Stack na hostitele ASDK spusťte CloudBuilder. vhdx a spusťte následující skript v okně PowerShellu se zvýšenými oprávněními:

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

Úrovně telemetrie jsou kumulativní a kategorizované na čtyři úrovně (0-3):

**0 (zabezpečení)** : pouze data zabezpečení. Informace, které jsou potřebné k zajištění zabezpečení operačního systému, včetně údajů o nastaveních a prostředích souvisejících s připojeným uživatelem a komponentami telemetrie a programu Windows Defender. Na této úrovni se nevysílá žádná telemetrie Azure Stack žádná konkrétní.

**1 (základní)** : data zabezpečení a základní data o stavu a kvalitě. Základní informace o zařízení, včetně dat týkajících se kvality, kompatibility aplikací, dat o využití aplikací a dat z úrovně zabezpečení. Nastavení úrovně telemetrie na Basic umožní telemetrii Azure Stack. Data shromážděná na této úrovni zahrnují:

- **Základní informace o zařízení** , které pomáhají zajistit porozumění typům a konfiguracím nativních a virtualizovaných instancí Windows serveru 2016 v ekosystému, včetně těchto:
  - Atributy počítače, jako je například výrobce OEM a model.
  - Síťové atributy, jako je počet a rychlost síťových adaptérů.
  - Atributy procesoru a paměti, jako je počet jader a velikost paměti.
  - Atributy úložiště, jako je počet jednotek, typ a velikost.
- **Funkce telemetrie**, včetně procenta nahraných událostí, vyřazených událostí a času posledního nahrávání.
- **Informace související s kvalitou** , které pomáhají Microsoftu vyvíjet základní znalosti o tom, jak probíhá Azure Stack. Příkladem je počet kritických výstrah pro konkrétní hardwarovou konfiguraci.
- **Data kompatibility**, která pomáhají získat informace o tom, které poskytovatele prostředků se instalují do systému a virtuálního počítače a které identifikují potenciální problémy s kompatibilitou.

**2 (rozšířené)** : Další přehledy, včetně toho, jak se používají operační systém a další Azure Stack služby, jak provádějí, pokročilá data o spolehlivosti a data ze základní úrovně a úrovně zabezpečení.

**3 (úplné)** : všechna data potřebná pro identifikaci a pomoc při řešení problémů a data z úrovní zabezpečení, základní a rozšířené úrovně.

> [!NOTE]
> Výchozí hodnota úrovně telemetrie je 2 (rozšířené).

Vypnutí telemetrie Windows a Azure Stack vypíná telemetrie SQL. Další informace o důsledcích nastavení telemetrie Windows serveru najdete v dokumentu [White paper o telemetrie Windows](https://aka.ms/winservtelemetry).

> [!IMPORTANT]
> Tyto úrovně telemetrie platí jenom pro Microsoft Azure Stack komponenty. Softwarové komponenty a služby od jiných výrobců, které běží na hostiteli životního cyklu hardwaru od Azure Stack mohou komunikovat s jejich Cloud Services mimo tyto úrovně telemetrie. Měli byste pracovat s vaším poskytovatelem hardwarových řešení Azure Stack, abyste pochopili jejich zásady telemetrie a jak se můžete rozhodnout nebo odhlásit.

### <a name="enable-or-disable-telemetry-after-deployment"></a>Povolení nebo zakázání telemetrie po nasazení

Pokud chcete povolit nebo zakázat telemetrii po nasazení, musíte mít přístup k privilegovanému koncovému bodu (PEP), který je vystavený na virtuálních počítačích ERCS.
1.  Povolení: `Set-Telemetry -Enable`
2.  Zakázání: `Set-Telemetry -Disable`

Podrobnosti parametru:
> . PARAMETR enable-zapnout nahrávání dat telemetrie
> 
> . Parametr disable – vypnutí nahrávání dat telemetrie  

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
[Spuštění a zastavení ASDK](asdk-start-stop.md)
