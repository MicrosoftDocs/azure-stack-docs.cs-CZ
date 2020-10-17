---
title: Poznámky k verzi centra Azure Stack
description: Poznámky k verzi pro integrované systémy Azure Stack hub, včetně aktualizací a oprav chyb.
author: sethmanheim
ms.topic: article
ms.date: 10/08/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 08/11/2020
ms.openlocfilehash: 1bea13f3ba7b9e210c52ac9cffc32c7d8e4a79c6
ms.sourcegitcommit: 82a8ddbf474a28cd551e86a69e0c41a624e0be2a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92116177"
---
# <a name="azure-stack-hub-release-notes"></a>Poznámky k verzi centra Azure Stack

Tento článek popisuje obsah balíčků aktualizací centra Azure Stack. Tato aktualizace zahrnuje vylepšení a opravy pro nejnovější vydání centra Azure Stack.

Pokud chcete získat přístup k poznámkám k verzi pro jinou verzi, použijte rozevírací nabídku selektor verzí nad obsahem vlevo.

::: moniker range=">=azs-1910"
> [!IMPORTANT]  
> Tento balíček aktualizace je určen pouze pro integrované systémy Azure Stack hub. Tento balíček aktualizace nepoužívejte na Azure Stack Development Kit (ASDK).
::: moniker-end
::: moniker range="<azs-1910"
> [!IMPORTANT]  
> Pokud je vaše instance centra Azure Stack za více než dvěma aktualizacemi, je považována za nedodržující předpisy. Aby bylo možné [získat podporu, musíte aktualizovat aspoň minimální podporovanou verzi](azure-stack-servicing-policy.md#keep-your-system-under-support).
::: moniker-end

## <a name="update-planning"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Známé problémy](known-issues.md)
- [Aktualizace zabezpečení](release-notes-security-updates.md)
- [Kontrolní seznam aktivit před a po instalaci aktualizace](release-notes-checklist.md)

Nápovědu k řešení potíží s aktualizacemi a procesu aktualizace najdete v tématu řešení potíží se [opravami a aktualizacemi pro centrum Azure Stack](azure-stack-troubleshooting.md).

## <a name="download-the-update"></a>Stáhnout aktualizaci

Balíček aktualizace centra Azure Stack můžete stáhnout pomocí [nástroje Azure Stack Center Update stahovacího](https://aka.ms/azurestackupdatedownload)programu.

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-2005"
## <a name="2005-build-reference"></a>2005 – odkaz na sestavení

Číslo buildu aktualizace centra Azure Stack 2005 je **1.2005.6.53**.

### <a name="update-type"></a>Typ aktualizace

Typ sestavení aktualizace centra Azure Stack 2005 je **plný**.

Velikost balíčku aktualizace 2005 je v porovnání s předchozími aktualizacemi větší. Zvýšení velikosti má za následek delší dobu stahování. Aktualizace zůstane ve stavu **přípravy** po dlouhou dobu a operátoři můžou očekávat, že tento proces trvá déle než u předchozích aktualizací. Aktualizace 2005 obsahovala v našem interním testování – 4 uzly následující očekávané moduly runtime: 13-20 hodin, 8 uzlů: 16-26 hodin, 12 uzlů: 19-32 hodiny, 16 uzlů: 22-38 hodin. Přesné běhové moduly pro aktualizaci jsou obvykle závislé na kapacitě používané v systému podle zatížení klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a specifikacemi hardwaru systému. Běhové moduly, které jsou kratší nebo delší než očekávaná hodnota, nejsou neobvyklé a nevyžadují akci Azure Stack operátory centra, pokud se aktualizace nezdařila. Tato přibližná doba běhu je specifická pro aktualizaci 2005 a neměla by se porovnávat s jinými aktualizacemi centra Azure Stack.

Další informace o typech sestavení aktualizací najdete v tématu [Správa aktualizací v centru Azure Stack](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novinky

<!-- What's new, also net new experiences and features. -->
- Toto sestavení nabízí podporu 3 nových typů virtuálních počítačů GPU: NCv3 (NVIDIA V100), NVv4 (AMD MI25) a NCas_v4 (NVIDIA T4) velikosti virtuálních počítačů. Nasazení virtuálních počítačů bude úspěšné pro uživatele, kteří mají správný hardware a jsou připojeni do programu Azure Stack hub GPU ve verzi Preview. Pokud vás zajímá, zaregistrujte se do programu pro zobrazení GPU v https://aka.ms/azurestackhubgpupreview . Další informace [najdete v tématu](../user/gpu-vms-about.md).
- Tato verze poskytuje novou funkci, která umožňuje autonomní funkci retušovací, která detekuje chyby, posuzuje dopad a bezpečně snižuje problémy se systémem. S touto funkcí pracujeme na zvýšené dostupnosti systému bez ručních zásahů. S vydáním verze 2005 a novějším budou zákazníci při snižování počtu výstrah vyskytnout. Jakékoli selhání v tomto kanálu nevyžaduje akci Azure Stack operátory centra, pokud není oznámeno.
- K dispozici je nová možnost na portálu pro správu centra Azure Stack pro zákazníky v gapped/odpojených Azure Stackch centra, aby bylo možné ukládat protokoly místně. Protokoly můžete ukládat do místní sdílené složky SMB, když je rozbočovač Azure Stack odpojený od Azure.
- Portál pro správu centra Azure Stack nyní blokuje určité operace, pokud již probíhá operace systému. Pokud například probíhá aktualizace, není možné přidat nový uzel jednotky škálování.
- Tato verze poskytuje větší konzistenci prostředků infrastruktury s Azure na virtuálních počítačích vytvořených před 1910. V 1910 společnost Microsoft oznámila, že všechny nově vytvořené virtuální počítače budou používat protokol wireserver, takže zákazníci budou moct používat stejné WALA agenta a agenta hosta Windows jako Azure, což usnadňuje používání imagí Azure v centru Azure Stack. V této verzi jsou všechny virtuální počítače vytvořené dříve než 1910 migrovány automaticky na používání protokolu wireserver. Tím se taky přináší spolehlivější vytváření virtuálních počítačů, nasazení rozšíření virtuálních počítačů a vylepšení v rovnovážné době provozu.
- Úložiště centra Azure Stack nyní podporuje rozhraní API služby Azure Storage Services verze 2019-02-02. Pro klientské knihovny Azure, které jsou kompatibilní s novou verzí REST API. Další informace najdete v tématu [vývojové nástroje pro úložiště Azure Stack hub](../user/azure-stack-storage-dev.md#azure-client-libraries).
- Centrum Azure Stack nyní podporuje nejnovější verzi [CreateUiDefinition (verze 2)](/azure/azure-resource-manager/managed-applications/create-uidefinition-overview).
- Nové pokyny pro nasazení dávkových virtuálních počítačů. Další informace [najdete v tomto článku](../operator/azure-stack-capacity-planning-compute.md).
- Položka CoreOS na webu Marketplace pro tržiště pro [se blíží Azure Stack ke konci životního cyklu](https://azure.microsoft.com/updates/flatcar-in-azure/). Další informace najdete v tématu [migrace z CoreOS Container Linux](https://docs.flatcar-linux.org/os/migrate-from-container-linux/).

### <a name="improvements"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Vylepšení protokolů a událostí Clusterové služby infrastruktury úložiště. Protokoly a události Clusterové služby infrastruktury úložiště se budou uchovávat až 14 dnů a získáte tak lepší diagnostiku a řešení potíží.
- Vylepšení, která zvyšují spolehlivost spouštění a zastavování centra Azure Stack.
- Vylepšení, která snižují modul runtime aktualizace pomocí decentralizace a odebírání závislostí. V porovnání s aktualizací 2002 se doba aktualizace značek 4 uzlů zkracuje z 15-42 hodin na 13-20 hodin. 8 uzlů se zkracuje z 20-50 hodin na 16-26 hodin. 12 uzlů se zkracuje z 20-60 hodin na 19-32 hodin. 16 uzlů se zkracuje z 25-70 hodin na 22-38 hodin. Přesné běhové moduly pro aktualizaci jsou obvykle závislé na kapacitě používané v systému podle zatížení klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a specifikacemi hardwaru systému.
- Pokud dojde k nějakým neobnovitelným chybám, aktualizace se teď v brzké době nezdařila.
- Vylepšená odolnost balíčku aktualizace při stahování z Internetu
- Vylepšení odolnosti proti chybám při zrušení přidělení virtuálního počítače
- Vylepšená odolnost agenta hostitele síťového adaptéru.
- Do datové části CEF zpráv syslog přidejte další pole, abyste nahlásili zdrojovou IP adresu a účet, který se používá pro připojení k privilegovanému koncovému bodu a ke koncovému bodu obnovení. Podrobnosti najdete v tématu [integrace Azure Stackho centra s řešeními monitorování pomocí předávání SYSLOG](azure-stack-integrate-security.md) .
- Události Windows Defenderu (ID události 5001, 5010, 5012) se přidaly do seznamu událostí vydaných přes klienta syslog.
- Přidání výstrah na portálu pro správu Azure Stack pro události související s Windows Defenderem pro hlášení o nekonzistenci verzí a nekonzistenci verzí a selhání při zpracování akcí zjištěného malwaru.
- Přidání podpory pro 4 hraniční zařízení při integraci Azure Stackho centra do vašeho datového centra.

### <a name="changes"></a>Změny

- Odeberou se akce, které se mají zastavit, vypnout a restartovat instanci role infrastruktury na portálu pro správu. V poskytovateli prostředků infrastruktury se odebraly taky odpovídající rozhraní API. Následující rutiny PowerShellu v modulu Správce RM a AZ Preview pro Azure Stack hub už nefungují: **stop-AzsInfrastructureRoleInstance**, **Disable-InfrastructureRoleInstance**a **restart-InfrastructureRoleInstance**. Tyto rutiny se odeberou z dalšího správce AZ Module Release for Azure Stack hub.
- Centrum Azure Stack 2005 teď podporuje [App Service na Azure Stack hub 2020 (verze 87. x)](app-service-release-notes-2020-Q2.md).

### <a name="fixes"></a>Opravy

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Opravili jsme problém, který by mohl způsobit selhání uzlu jednotky škálování v opravě, protože se nepovedlo najít cestu k základní imagi operačního systému.
- Opravili jsme problém se škálováním na více instancí a nahorizontálním navýšení kapacity pro roli infrastruktury podpory, která má kaskádový efekt na opravu uzlů jednotek škálování.
- Opravili jsme problém, ve kterém je. Rozšíření VHD (namísto. VHD) se nepovolilo, když operátoři přidali své vlastní image na portál Azure Stack Center pro správu na **všech službách > compute > VM images > přidat**.
- Opravili jsme problém, kdy předchozí operace restartování virtuálního počítače způsobila následné neočekávané restartování po jakékoli jiné operaci aktualizace virtuálních počítačů (přidávání disků, značek atd.).
- Opravili jsme problém, kdy vytvoření duplicitní zóny DNS způsobilo, že portál přestane reagovat. Měla by nyní zobrazit příslušnou chybu.
- Opravili jsme problém, kdy **příkaz Get-AzureStackLogs** neshromažďuje požadované protokoly pro řešení problémů se sítí. 
- Opravili jsme problém, kdy portál povoluje, aby bylo možné připojit méně síťových adaptérů, než kolik to skutečně povoluje. 
- Zásady integrity pevných kódů negenerují události porušení pro určitý interní software. Tím se snižuje šum v událostech porušení integrity kódu vysílaných prostřednictvím klienta syslog.
- Opravili jsme rutinu **set-TLSPolicy** , která vynutila nové zásady, aniž by bylo nutné restartovat službu HTTPS nebo restartovat hostitele.
- Opravili jsme problém, kdy při použití serveru Linux NTP server na portálu pro správu nesprávně vygeneroval výstrahy.  
- Opravili jsme problém, kdy převzetí služeb při selhání instance služby zálohovacího řadiče vedlo k automatickému zablokování zálohování.
- Opravili jsme problém, kdy dojde k chybě při rotaci interního tajného klíče, pokud služby infrastruktury nemají připojení k Internetu
- Opravili jsme problém, kdy uživatelé nemohli zobrazit oprávnění předplatného pomocí portálů Azure Stack hub.

## <a name="security-updates"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci centra Azure Stack najdete v tématu [aktualizace zabezpečení centra Azure Stack](release-notes-security-updates.md).

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack centrum pravidelně vydává opravy hotfix. Od verze 2005 se při aktualizaci na novou hlavní verzi (například 1.2002. x na 1.2005. x) automaticky nainstalují nejnovější opravy hotfix (pokud nějaké jsou) v nové hlavní verzi. Od tohoto okamžiku předem, pokud byla vydána oprava hotfix pro sestavení, měli byste ji nainstalovat.

> [!NOTE]
> Verze oprav hotfix centra Azure Stack jsou kumulativní; potřebujete jenom nainstalovat nejnovější opravu hotfix, abyste získali všechny opravy, které jsou součástí všech předchozích verzí oprav hotfix pro danou verzi.

Další informace najdete v našich [zásadách obsluhy](azure-stack-servicing-policy.md).

Opravy hotfix centra Azure Stack se vztahují pouze na integrované systémy Azure Stack hub. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="prerequisites-before-applying-the-2005-update"></a>Požadavky: před instalací aktualizace 2005

Verze 2005 centra Azure Stack se musí použít ve verzi 2002 s následujícími opravami hotfix:

- [1.2002.56.152 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4582983)

### <a name="after-successfully-applying-the-2005-update"></a>Po úspěšné instalaci aktualizace 2005

Od verze 2005 se při aktualizaci na novou hlavní verzi (například 1.2002. x na 1.2005. x) automaticky nainstalují nejnovější opravy hotfix (pokud nějaké jsou) v nové hlavní verzi.

Pokud se následně uvolní nějaké opravy hotfix 2005, měli byste po instalaci 2005 nainstalovat tyto opravy:

- [1.2005.18.78 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4584238)
::: moniker-end

::: moniker range="azs-2002"
## <a name="2002-build-reference"></a>2002 – odkaz na sestavení

Číslo buildu aktualizace centra Azure Stack 2002 je **1.2002.0.35**.

> [!IMPORTANT]  
> V rámci aktualizace centra Azure Stack 2002 společnost Microsoft dočasně rozšiřuje naše [příkazy zásad podpory centra Azure Stack](azure-stack-servicing-policy.md).  Spolupracujeme se zákazníky po celém světě, kteří reagují na COVID-19 a kteří můžou dělat důležitá rozhodnutí o svých Azure Stackch systémech, jak se aktualizují a spravují, a výsledkem je, že obchodní operace datového centra budou fungovat normálně. V rámci podpory našich zákazníků nabízí Microsoft dočasnou příponu pro změnu zásad, která zahrnuje tři předchozí verze aktualizací.  V důsledku toho bude podporována nově vydaná aktualizace 2002 a kterákoli ze tří předchozích verzí aktualizace (například 1910, 1908 a 1907).

### <a name="update-type"></a>Typ aktualizace

Typ sestavení aktualizace centra Azure Stack 2002 je **plný**.

Velikost balíčku aktualizace 2002 je v porovnání s předchozími aktualizacemi větší. Zvýšení velikosti má za následek delší dobu stahování. Aktualizace zůstane ve stavu **přípravy** po dlouhou dobu a operátoři můžou očekávat, že tento proces trvá déle než u předchozích aktualizací. Aktualizace 2002 obsahovala v našem interním testování – 4 uzly následující očekávané moduly runtime: 15-42 hodin, 8 uzlů: 20-50 hodin, 12 uzlů: 20-60 hodiny, 16 uzlů: 25-70 hodin. Přesné běhové moduly pro aktualizaci jsou obvykle závislé na kapacitě používané v systému podle zatížení klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a specifikacemi hardwaru systému. Běhové moduly, které jsou kratší nebo delší než očekávaná hodnota, nejsou neobvyklé a nevyžadují akci Azure Stack operátory centra, pokud se aktualizace nezdařila. Tato přibližná doba běhu je specifická pro aktualizaci 2002 a neměla by se porovnávat s jinými aktualizacemi centra Azure Stack.

Další informace o typech sestavení aktualizací najdete v tématu [Správa aktualizací v centru Azure Stack](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novinky

<!-- What's new, also net new experiences and features. -->

- K dispozici je nová verze (1.8.1) Azure Stack moduly PowerShellu pro správce centra založené na AzureRM.
- K dispozici je nová verze REST API Správce centra Azure Stack. Podrobnosti o koncových bodech a nekonečných změnách najdete v referenčních informacích k [rozhraní API](/rest/api/azure-stack/).
- Noví Azure PowerShell moduly tenanta budou vydány pro Azure Stack centra 15. dubna 2020. Aktuálně používané moduly Azure RM budou fungovat i nadále, ale po sestavení 2002 už se neaktualizují.
- Přidání nového upozornění na portál správce centra Azure Stack k hlášení problémů s připojením s nakonfigurovaným serverem syslog. Název výstrahy: **u klienta syslog došlo při odesílání zprávy syslog k potížím se sítí**.
- Přidání nového upozornění na portál správce centra Azure Stack k hlášení problémů s připojením k serveru NTP (Network Time Protocol). Název výstrahy je **neplatný čas zdroje na [název uzlu]**.
- [Sada Java SDK](https://azure.microsoft.com/develop/java/) vydala nové balíčky z důvodu zásadní změny v 2002 v souvislosti s omezeními TLS. Je nutné nainstalovat novou závislost sady Java SDK. Pokyny najdete v tématu [profily verze Java a rozhraní API](../user/azure-stack-version-profiles-java.md?view=azs-2002#java-and-api-version-profiles).
- K dispozici je nová verze (1.0.5.10) sady MP centra System Center Operations Manager-Azure Stack, která se vyžaduje pro všechny systémy se systémem 2002 z důvodu porušení změn rozhraní API. Změny rozhraní API mají vliv na řídicí panely pro zálohování a úložiště a před aktualizací MP doporučujeme nejprve aktualizovat všechny systémy na 2002.

### <a name="improvements"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Tato aktualizace obsahuje změny v procesu aktualizace, které významně zlepšují výkon budoucích úplných aktualizací. Tyto změny se projeví až po vydání 2002 a konkrétně mají za cíl zlepšit výkon fáze plné aktualizace, ve které jsou operační systémy hostitele aktualizovány. Zlepšení výkonu aktualizací hostitelských operačních systémů významně snižuje časový interval, během kterého jsou úlohy tenanta ovlivněny během úplných aktualizací.
- Nástroj pro kontrolu připravenosti centra Azure Stack nyní ověřuje integraci služby AD Graph pomocí všech portů IP protokolu TCP přidělených ke službě AD Graph.
- Nástroj pro syndikaci offline byl aktualizován o vylepšení spolehlivosti. Nástroj už není dostupný na GitHubu a [přesunul se do galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Azs.Syndication.Admin/). Další informace najdete v tématu [stažení položek Marketplace do centra Azure Stack](azure-stack-download-azure-marketplace-item.md).
- Zavádí se nová funkce monitorování. Upozornění na nedostatek místa na disku pro fyzické hostitele a virtuální počítače infrastruktury bude automaticky opraveno platformou a jenom v případě, že tato akce selže, výstraha se zobrazí na portálu pro správu centra Azure Stack, aby mohl obsluhu provést akci.
- Vylepšení [shromažďování protokolů diagnostiky](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002). Nové prostředí zjednodušuje a zjednodušuje shromažďování protokolů diagnostiky tím, že v předem odebere nutnost konfigurace účtu BLOB Storage. Prostředí úložiště je předem nakonfigurované tak, aby bylo možné odesílat protokoly před otevřením případu podpory a strávíte méně času při volání podpory.
- Čas potřebný pro [proaktivní shromažďování protokolů i pro shromažďování protokolů na vyžádání](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002)   byl snížen o 80%. Doba shromažďování protokolů může trvat déle než tato očekávaná hodnota, ale nevyžaduje akci Azure Stack operátory centra, pokud není shromažďování protokolů úspěšné.
- Průběh stahování balíčku aktualizace centra Azure Stack je teď v okně aktualizace po zahájení aktualizace viditelný. To platí jenom pro připojené systémy centra Azure Stack, které se rozhodnou [připravit balíčky aktualizací prostřednictvím automatického stažení](azure-stack-update-prepare-package.md#automatic-download-and-preparation-for-update-packages).
- Vylepšení spolehlivosti agenta hostitele síťového adaptéru.
- Představili jsme novou mikroslužbu nazvanou DNS Orchestrator, která během opravy a aktualizace vylepšuje logiku pro interní služby DNS.
- Přidání nového ověření žádosti za účelem selhání neplatných identifikátorů URI objektu BLOB pro parametr účtu úložiště diagnostiky spouštění při vytváření virtuálních počítačů.
- Přidání automatických oprav a vylepšení protokolování pro Rdagent a hostitelského agenta – dvě služby na hostiteli, které usnadňují operace CRUD virtuálních počítačů.
- Do správy Marketplace jsme přidali novou funkci, která umožňuje Microsoftu přidávat atributy, které správcům zablokují stažení produktů z Marketplace, které nejsou kompatibilní s jejich Azure Stack, z důvodu různých vlastností, jako je například Azure Stack verze nebo model fakturace. Tyto atributy může přidat jenom společnost Microsoft. Další informace najdete v tématu [použití portálu ke stažení položek Marketplace](azure-stack-download-azure-marketplace-item.md#use-the-portal-to-download-marketplace-items).

### <a name="changes"></a>Změny

- Portál pro správu nyní indikuje, zda operace probíhá, s ikonou vedle Azure Stack oblasti. Když na ikonu najedete myší, zobrazí se název operace. Díky tomu můžete identifikovat spuštěné systémové operace na pozadí. například úloha zálohování nebo rozšíření úložiště, které může běžet několik hodin.

- Následující rozhraní API pro správce jsou zastaralá:

  | Poskytovatel prostředků       | Prostředek              | Verze            |
  |-------------------------|-----------------------|--------------------|
  | Microsoft. Storage. admin | Fare                 | 2015-12-01 – Preview |
  | Microsoft. Storage. admin | farmy a akvizice    | 2015-12-01 – Preview |
  | Microsoft. Storage. admin | farmy a sdílené složky          | 2015-12-01 – Preview |
  | Microsoft. Storage. admin | farmy/storageaccounts | 2015-12-01 – Preview |

- Následující rozhraní API pro správu byla nahrazena novější verzí (2018-09-01):

  | Poskytovatel prostředků      | Prostředek              | Verze    |
  |------------------------|-----------------------|------------|
  | Microsoft. Backup. admin | backupLocation         | 2016-05-01 |
  | Microsoft. Backup. admin | vytvářet                | 2016-05-01 |
  | Microsoft. Backup. admin | Operace             | 2016-05-01 |

- Když vytváříte virtuální počítač s Windows pomocí PowerShellu, nezapomeňte přidat `provisionvmagent` příznak, pokud chcete, aby virtuální počítač nasadil rozšíření. Bez tohoto příznaku se virtuální počítač vytvoří bez agenta hosta, který odebere možnost nasazení rozšíření virtuálních počítačů:

   ```powershell
   $VirtualMachine = Set-AzureRmVMOperatingSystem `
     -VM $VirtualMachine `
     -Windows `
     -ComputerName "MainComputer" `
     -Credential $Credential -ProvisionVMAgent
  ```

### <a name="fixes"></a>Opravy

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Opravili jsme problém, kdy přidání více než jedné veřejné IP adresy na stejné síťové kartě na virtuálním počítači způsobilo problémy s připojením k Internetu. Síťové rozhraní se dvěma veřejnými IP adresami teď funguje podle očekávání.
- Opravili jsme problém, který způsobil, že systém vyvolal výstrahu oznamující, že je potřeba nakonfigurovat domovský adresář služby Azure AD.
- Opravili jsme problém, který způsobil, že se výstraha automaticky nezavřela. Výstraha ukázala, že je nutné nakonfigurovat domovský adresář služby Azure AD, ale nezavřeli jsme ani po vyřešení problému.
- Opravili jsme problém, který způsobil, že se aktualizace během fáze přípravy aktualizace nezdařila v důsledku interního selhání poskytovatele prostředků aktualizace.
- Opravili jsme problém způsobující selhání operací poskytovatele prostředků doplňku po provedení Azure Stack rotace tajného centra.
- Opravili jsme problém, který byl běžnou příčinou selhání aktualizace centra Azure Stack z důvodu přetížení paměti u role ERCS.
- Opravili jsme chybu v okně aktualizace, ve kterém se stav aktualizace zobrazil jako **instalace** namísto **přípravy během** přípravného fáze Azure Stack aktualizace centra.
- Opravili jsme problém, kdy funkce RSC ve virtuálních přepínačích vytvořila inconsistences a provedla přenos toků prostřednictvím nástroje pro vyrovnávání zatížení. Funkce RSC je teď ve výchozím nastavení zakázaná.
- Opravili jsme problém, kdy více konfigurací IP na síťovém adaptéru způsobilo nesměrování provozu a odchozí připojení. 
- Opravili jsme problém, kdy se adresa MAC síťové karty ukládala do mezipaměti a přiřazení této adresy jinému prostředku způsobilo selhání nasazení virtuálních počítačů.
- Opravili jsme problém, kdy image virtuálních počítačů s Windows z MALOOBCHODNÍho kanálu nemohla aktivovat svou licenci pomocí AVMA.
- Opravili jsme problém, kdy se virtuální počítače nepodaří vytvořit, pokud se počet virtuálních jader vyžadovaných virtuálním počítačem rovnal fyzickým jádrům uzlu. Virtuálním počítačům teď umožníme mít virtuální jádra větší nebo menší než fyzické jádra uzlu.
- Opravili jsme problém, kdy nepovolujeme, aby byl typ licence nastavený na hodnotu null, aby se přepnuly obrázky s průběžnými platbami na BYOL.
- Opravili jsme problém, aby bylo možné přidat rozšíření do sady škálování virtuálního počítače.

## <a name="security-updates"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci centra Azure Stack najdete v tématu [aktualizace zabezpečení centra Azure Stack](release-notes-security-updates.md).

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack centrum pravidelně vydává opravy hotfix. Před aktualizací centra Azure Stack na 2002 se ujistěte, že jste nainstalovali nejnovější opravu hotfix centra Azure Stack pro 1910.

> [!NOTE]
> Verze oprav hotfix centra Azure Stack jsou kumulativní; potřebujete jenom nainstalovat nejnovější opravu hotfix, abyste získali všechny opravy, které jsou součástí všech předchozích verzí oprav hotfix pro danou verzi.

Opravy hotfix centra Azure Stack se vztahují pouze na integrované systémy Azure Stack hub. Nepokoušejte se instalovat opravy hotfix na ASDK.

Další informace o opravách hotfix najdete v tématu [zásady obsluhy centra Azure Stack](azure-stack-servicing-policy.md#hotfixes).

### <a name="prerequisites-before-applying-the-2002-update"></a>Požadavky: před instalací aktualizace 2002

Verze 2002 centra Azure Stack se musí použít ve verzi 1910 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [1.1910.63.186 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4574735)

### <a name="after-successfully-applying-the-2002-update"></a>Po úspěšné instalaci aktualizace 2002

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix.

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [1.2002.56.152 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4582983)
::: moniker-end

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

- Portál pro správu nyní zobrazuje IP adresy privilegovaného koncového bodu v nabídce Vlastnosti oblasti pro snazší zjišťování. Kromě toho zobrazuje aktuální nakonfigurovaný časový server a servery DNS pro přeposílání. Další informace najdete v tématu [Použití privilegovaného koncového bodu ve službě Azure Stack Hub](azure-stack-privileged-endpoint.md).

- Pokud dojde k chybě, může teď systém monitorování stavu a monitorování centra Azure Stack vyvolat výstrahy pro různé hardwarové součásti. Tyto výstrahy vyžadují další konfiguraci. Další informace najdete v tématu [monitorování hardwarových komponent centra Azure Stack](azure-stack-hardware-monitoring.md).

- [Podpora Cloud-init pro Azure Stack hub](/azure/virtual-machines/linux/using-cloud-init): Cloud-init je široce používaný přístup k přizpůsobení virtuálního počítače se systémem Linux při prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Vzhledem k tomu, že se během procesu prvotního spuštění volá Cloud-init, neexistují žádné další kroky ani nepotřebné agenti pro použití konfigurace. Image Ubuntu na webu Marketplace byly aktualizovány tak, aby podporovaly Cloud-init pro zřizování.

- Centrum Azure Stack nyní podporuje všechny verze agenta Windows Azure Linux jako Azure.

- K dispozici je nová verze modulů PowerShellu pro správce centra Azure Stack. <!-- For more information, see -->

- Od 15. dubna 2020 byly vydány nové moduly tenanta Azure PowerShell pro centrum Azure Stack. Aktuálně používané moduly Azure RM budou fungovat i nadále, ale po sestavení 2002 už se neaktualizují.

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
  - Dostávat podpora Microsoftu a technické podpory pro jejich nasazení.

### <a name="improvements"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Centrum Azure Stack zlepšilo svou schopnost automaticky opravovat některé aktualizace a problémy s aktualizací, které dříve způsobily chyby aktualizace, nebo brání operátorům zahájit Azure Stack aktualizace centra. Výsledkem je, že ve skupině **test-AzureStack-UpdateReadiness** je k dispozici méně testů. Další informace najdete v tématu [ověření stavu systému centra Azure Stack](azure-stack-diagnostic-test.md#groups). Ve skupině **UpdateReadiness** zůstanou tyto tři testy:

  - **AzSInfraFileValidation**
  - **AzSActionPlanStatus**
  - **AzsStampBMCSummary**

- Přidalo se pravidlo auditování, které oznamuje, že externí zařízení (například klíč USB) je připojené k uzlu infrastruktury centra Azure Stack. Protokol auditu se vysílá přes protokol syslog a zobrazí se jako **Microsoft-Windows-Security-audit: 6416 | Technologie Plug and Play události** Další informace o tom, jak nakonfigurovat klienta syslog, najdete v tématu [předávání SYSLOG](azure-stack-integrate-security.md).

- Rozbočovač Azure Stack se přesouvá do 4096 klíčů RSA pro vnitřní certifikáty. Spuštění interního rotace tajných klíčů nahradí staré 2048 certifikátů pomocí 4096 bitů dlouhých certifikátů. Další informace o rotaci tajných kódů v centru Azure Stack najdete v tématu [otočení tajných kódů v centru Azure Stack](azure-stack-rotate-secrets.md).

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

   Tyto změny se projeví také ve výchozí dokumentaci k [návrhu protokolu IPSec/IKE](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters) .

- Služba infrastruktura zálohování vylepšuje logiku, která počítá požadované místo na zálohování místo spoléhání na pevnou prahovou hodnotu. Služba bude používat velikost zálohování, zásady uchovávání informací, rezervu a aktuální využití externího umístění úložiště k určení, jestli se má pro operátor vyvolat upozornění.

### <a name="changes"></a>Změny

- Při stahování položek z webu Marketplace z Azure do centra Azure Stack je k dispozici nové uživatelské rozhraní, které umožňuje zadat verzi položky, pokud existuje více verzí. Nové uživatelské rozhraní je k dispozici v rámci připojených i odpojených scénářů. Další informace najdete v tématu [stažení položek Marketplace z Azure do centra Azure Stack](azure-stack-download-azure-marketplace-item.md).  

- Počínaje verzí 1910 **vyžaduje** systém Azure Stack hub další/20 privátní interní IP místo. Další informace najdete v tématu [Plánování integrace sítě pro Azure Stack](azure-stack-network.md) .
  
- Služba zálohování infrastruktury odstraní částečně nahraná zálohovaná data v případě, že umístění externího úložiště během procesu nahrávání vyčerpá kapacitu.  

- Služba infrastruktura zálohování přidá službu identit do datové části zálohy pro nasazení AAD.  

- Modul PowerShellu centra Azure Stack se aktualizoval na verzi 1.8.0 pro verzi 1910.<br>Změny zahrnují:
   - **Nový modul Správce DRP**: poskytovatel prostředků nasazení (DRP) umožňuje Orchestrované nasazení poskytovatelů prostředků do centra Azure Stack. Tyto příkazy komunikují s Azure Resource Managerou vrstvou a komunikují s DRP.
   - **BRP**: <br />
           – Podporuje obnovení jedné role pro zálohování infrastruktury Azure Stack. <br />
           – Přidejte parametr `RoleName` do rutiny `Restore-AzsBackup` .
   - **FRP**: zásadní změny prostředků **jednotky** a **svazku** pomocí verze rozhraní API `2019-05-01` . Funkce jsou podporovány centrem Azure Stack 1910 a novějším: <br />
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

Informace o aktualizacích zabezpečení v této aktualizaci centra Azure Stack najdete v tématu [aktualizace zabezpečení centra Azure Stack](release-notes-security-updates.md).

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

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [1.1910.63.186 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4574735)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1908"
## <a name="1908-archived-release-notes"></a>1908 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1907"
## <a name="1907-archived-release-notes"></a>1907 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1906"
## <a name="1906-archived-release-notes"></a>1906 archivované poznámky k verzi
::: moniker-end
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

::: moniker range="<azs-1910"
Do Galerie TechNet můžete získat přístup ke [starším verzím poznámky k verzi centra Azure Stack](https://aka.ms/azsarchivedrelnotes). Tyto archivované dokumenty jsou k dispozici pouze pro referenční účely a neznamenají podporu těchto verzí. Informace o podpoře centra Azure Stack najdete v tématu [zásady obsluhy centra Azure Stack](azure-stack-servicing-policy.md). Pokud potřebujete další pomoc, obraťte se na službu zákaznické podpory společnosti Microsoft.
::: moniker-end
