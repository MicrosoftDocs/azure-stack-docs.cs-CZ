---
title: Poznámky k verzi centra Azure Stack
description: Poznámky k verzi pro integrované systémy Azure Stack hub, včetně aktualizací a oprav chyb.
author: sethmanheim
ms.topic: article
ms.date: 12/11/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ms.openlocfilehash: c94b91d2144248dd154da5b630b99470461dda67
ms.sourcegitcommit: 56d7da84672261e2490138011bd8dd4eaf81851d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97492768"
---
# <a name="azure-stack-hub-release-notes"></a>Poznámky k verzi centra Azure Stack

Tento článek popisuje obsah balíčků aktualizací centra Azure Stack. Tato aktualizace zahrnuje vylepšení a opravy pro nejnovější vydání centra Azure Stack.

Pokud chcete získat přístup k poznámkám k verzi pro jinou verzi, použijte rozevírací nabídku selektor verzí nad obsahem vlevo.

::: moniker range=">=azs-2002"
> [!IMPORTANT]  
> Tento balíček aktualizace je určen pouze pro integrované systémy Azure Stack hub. Tento balíček aktualizace nepoužívejte na Azure Stack Development Kit (ASDK).
::: moniker-end
::: moniker range="<azs-2002"
> [!IMPORTANT]  
> Pokud je vaše instance centra Azure Stack za více než dvěma aktualizacemi, je považována za nedodržující předpisy. Aby bylo možné [získat podporu, musíte aktualizovat aspoň minimální podporovanou verzi](azure-stack-servicing-policy.md#keep-your-system-under-support).
::: moniker-end

## <a name="update-planning"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Kontrolní seznam aktivit před a po instalaci aktualizace](release-notes-checklist.md)
- [Známé problémy](known-issues.md)
- [Opravy hotfix](#hotfixes)
- [Aktualizace zabezpečení](release-notes-security-updates.md)

Nápovědu k řešení potíží s aktualizacemi a procesu aktualizace najdete v tématu řešení potíží se [opravami a aktualizacemi pro centrum Azure Stack](azure-stack-troubleshooting.md).

## <a name="download-the-update"></a>Stáhnout aktualizaci

Balíček aktualizace centra Azure Stack můžete stáhnout pomocí [nástroje Azure Stack Center Update stahovacího](https://aka.ms/azurestackupdatedownload)programu.

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-2008"
## <a name="2008-build-reference"></a>2008 – odkaz na sestavení

Číslo buildu aktualizace centra Azure Stack 2008 je **1.2008.13.88**.

### <a name="update-type"></a>Typ aktualizace

Typ sestavení aktualizace centra Azure Stack 2008 je **plný**.

Velikost balíčku aktualizace 2008 je v porovnání s předchozími aktualizacemi větší. Zvýšení velikosti má za následek delší dobu stahování. Aktualizace zůstane ve stavu **přípravy** po dlouhou dobu a operátoři můžou očekávat, že tento proces trvá déle než u předchozích aktualizací. Aktualizace 2008 obsahovala v našem interním testování – 4 uzly následující očekávané moduly runtime: 13-20 hodin, 8 uzlů: 16-26 hodin, 12 uzlů: 19-32 hodiny, 16 uzlů: 22-38 hodin. Přesné běhové moduly pro aktualizaci jsou obvykle závislé na kapacitě používané v systému podle zatížení klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a specifikacemi hardwaru systému. Běhové moduly, které jsou kratší nebo delší než očekávaná hodnota, nejsou neobvyklé a nevyžadují akci Azure Stack operátory centra, pokud se aktualizace nezdařila. Tato přibližná doba běhu je specifická pro aktualizaci 2008 a neměla by se porovnávat s jinými aktualizacemi centra Azure Stack.

Další informace o typech sestavení aktualizací najdete v tématu [Správa aktualizací v centru Azure Stack](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Co je nového

<!-- What's new, also net new experiences and features. -->
- Azure Stack hub teď podporuje partnerský vztah VNET, který umožňuje připojení virtuální sítě bez síťového virtuálního zařízení (síťové virtuální zařízení). Další informace najdete v [dokumentaci nové sítě VNet peering](../user/virtual-network-peering.md).
- Úložiště objektů BLOB centra Azure Stack teď umožňuje uživatelům používat neměnné objekty blob. Nastavením neměnných zásad na kontejneru můžete ukládat datové objekty kritické pro podnikání do ČERVa (napsat jednou, číst mnoho). V této verzi se neměnné zásady dají nastavit jenom prostřednictvím REST API nebo klientských sad SDK. V této verzi nejsou taky možné připojovat zápisy objektů BLOB. Další informace o neměnných objektech blob najdete v tématu [ukládání důležitých podnikových dat objektů BLOB s neměnném úložištěm](/azure/storage/blobs/storage-blob-immutable-storage).
- Úložiště centra Azure Stack nyní podporuje rozhraní API služby Azure Storage Services verze **2019-07-07**. Pro klientské knihovny Azure, které jsou kompatibilní s novou verzí REST API, přečtěte si téma [vývojové nástroje pro úložiště Azure Stack hub](../user/azure-stack-storage-dev.md#azure-client-libraries). Pro rozhraní API pro správu služby Azure Storage Services byla **2018-02-01** přidána podpora s podmnožinou celkových dostupných funkcí.
- Azure Stack Center COMPUTE teď podporuje Azure COMPUTE API verze **2020-06-01** s podmnožinou celkových dostupných funkcí.
- Spravované disky centra Azure Stack nyní podporují rozhraní API disku Azure verze **2019-03-01** s podmnožinou dostupných funkcí.
- Verze Preview centra pro správu Windows, která se teď může připojit k Azure Stackmu centru, aby poskytovala podrobné přehledy o infrastruktuře během operací podpory (vyžaduje se rozbití).
- Možnost přidání přihlašovacího nápisu do privilegovaného koncového bodu (PEP) v době nasazení.
- Vydávalo více **exkluzivních provozních** proužků, což vylepší viditelnost operací, které se v systému v současné době děje, a zakažte uživatelům spouštění (a následně neúspěšných) všech dalších exkluzivních operací.
- Zavedli jsme dvě nové nápisy na stránce produktu Azure Stack centra na webu Marketplace. Pokud dojde k chybě při stahování Marketplace, můžou operátoři zobrazit podrobnosti o chybě a zkusit problém vyřešit pomocí doporučených kroků.
- Vydali jste nástroj pro hodnocení, který zákazníkům poskytuje zpětnou vazbu. To umožní centru Azure Stack měřit a optimalizovat prostředí pro zákazníky.
- Tato verze centra Azure Stack obsahuje soukromou verzi Preview služby Azure Kubernetes (AKS) a Azure Container Registry (ACR). Účelem privátní verze Preview je shromažďování názorů o kvalitě, funkcích a činnostech uživatelů AKS a ACR v centru Azure Stack.
- Tato verze zahrnuje veřejnou verzi Preview kontejnerů Azure CNI a Windows pomocí [AKS Engine v 0.55.4](../user/kubernetes-aks-engine-release-notes.md). Příklad jejich použití ve vašem modelu rozhraní API [najdete v tomto příkladu na GitHubu](https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-windows.json).
- Nyní podporuje [nasazení Istio 1,3](https://github.com/Azure/aks-engine/tree/master/examples/service-mesh) v clusterech nasazených pomocí [AKS Engine v 0.55.4](../user/kubernetes-aks-engine-release-notes.md). Další informace najdete v [těchto pokynech](../user/kubernetes-aks-engine-service-account.md).
- Nyní podporuje nasazení [privátních clusterů](https://github.com/Azure/aks-engine/blob/master/docs/topics/features.md#private-cluster) pomocí nástroje [AKS Engine v 0.55.4](../user/kubernetes-aks-engine-release-notes.md).
- Tato verze zahrnuje podporu pro [Kubernetes konfiguračních tajných](https://github.com/Azure/aks-engine/blob/master/docs/topics/keyvault-secrets.md#use-key-vault-as-the-source-of-cluster-configuration-secrets) kódů z Azure a Key Vaultch instancí centra Azure Stack.

### <a name="improvements"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->
- Bylo implementováno interní monitorování pro síťový adaptér a agenty hostitele SLB, takže služby budou automaticky opraveny, pokud někdy vstoupí do stavu Zastaveno.
- Active Directory Federation Services (AD FS) (AD FS) nyní načte nový podpisový certifikát tokenu grafice zákazník ho otočí na svém vlastním serveru AD FS. Aby bylo možné využít tuto novou funkci pro už nakonfigurované systémy, musí být AD FS integrace nakonfigurována znovu. Další informace najdete v tématu [integrace AD FS identity s vaším centrem Azure Stack hub](azure-stack-integrate-identity.md).
- Změny v procesu spuštění a vypnutí u instancí rolí infrastruktury a jejich závislosti na uzlech jednotek škálování. Tím se zvyšuje spolehlivost při spuštění a vypnutí centra Azure Stack.
- Sada **AzSScenarios** nástroje **test-AzureStack** Validation Tool se aktualizovala tak, aby poskytovatelé cloudových služeb mohli úspěšně spouštět tuto sadu s ověřováním službou Multi-Factor Authentication u všech zákaznických účtů.
- Vylepšená spolehlivost výstrah přidáním logiky potlačení za 29 zákaznických výstrah během operací životního cyklu.
- Teď si můžete zobrazit podrobnou sestavu protokolu HTML pro shromažďování protokolů, která poskytuje podrobné informace o rolích, trvání a stavu shromažďování protokolů. Účelem této sestavy je pomáhat uživatelům poskytnout Shrnutí shromážděných protokolů. Služba zákaznické podpory Microsoftu pak může sestavu rychle posoudit, aby vyhodnotila data protokolu, a pomohl vyřešit problémy se systémem a zmírnit je.
- Pokrytí detekce chyb infrastruktury bylo rozšířeno o přidání dalších 7 nových monitorování napříč uživatelskými scénáři, jako je využití procesoru a spotřeba paměti, což pomáhá zvýšit spolehlivost detekce chyb.

### <a name="changes"></a>Změny

- Vlastnost typu prostředku účtu úložiště **supportHttpsTrafficOnly** v rozhraní SRP API verze **2016-01-01** a **2016-05-01** je povolená, ale tato vlastnost není v Azure Stackovém centru podporovaná.
- Vyvolala prahovou hodnotu pro výstrahu využití kapacity svazku z 80% (upozornění) a 90% (kritické) na 90% (Warning) a 95% (kritická). Další informace najdete v tématu [Upozornění na místo v úložišti](azure-stack-manage-storage-shares.md#storage-space-alerts) .
- Kroky konfigurace služby AD Graph se v této verzi mění. Další informace najdete v tématu [integrace AD FS identity s vaším centrem Azure Stack hub](azure-stack-integrate-identity.md).
- Aby bylo možné zarovnat se k aktuálním osvědčeným postupům definovaným pro Windows Server 2019, Azure Stack centrálního centra se mění tak, aby se k dalšímu oddělení komunikace mezi serverem a serverem podporovala další třída provozu a Priorita. Výsledek těchto změn zajišťuje lepší odolnost proti komunikaci clusteru s podporou převzetí služeb při selhání. Tato konfigurace rezervace třídy provozu a šířky pásma se provádí změnou v přepínačích rozhraní příkazového centra pro Azure Stack a na hostiteli nebo serverech centra Azure Stack.

  Všimněte si, že tyto změny jsou přidány na úrovni hostitele systému Azure Stack hub. Obraťte se na výrobce OEM, aby bylo možné provést požadované změny v přepínačích sítě v horní části racku. Tato změna mandátu se dá provést buď před aktualizací verze 2008, nebo po aktualizaci na 2008. Další informace najdete v [dokumentaci k integraci sítě](azure-stack-network.md).

- Velikosti virtuálních počítačů podporující GPU **NCas_v4 (NVIDIA T4)** se v tomto sestavení nahradily **NCasT4_v3** velikosti virtuálních počítačů, aby byly konzistentní s Azure. Všimněte si, že tyto prvky ještě nejsou zobrazené na portálu a dají se použít jenom prostřednictvím šablon Azure datamanageru.

### <a name="fixes"></a>Opravy

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- Opravili jsme problém, kdy se nepovedlo odstranit NSG síťové karty, která není připojená k běžícímu virtuálnímu počítači.
- Opravili jsme problém, kdy se upraví hodnota **IdleTimeoutInMinutes** pro veřejnou IP adresu, která je přidružená k nástroji pro vyrovnávání zatížení, uvedla veřejnou IP adresu ve stavu selhání.
- Opravili jsme rutinu **Get-AzsDisk** , která pro připojené spravované disky vrátí správný stav **připojení** místo **OnlineMigration**.

## <a name="security-updates"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci centra Azure Stack najdete v tématu [aktualizace zabezpečení centra Azure Stack](release-notes-security-updates.md).

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack centrum pravidelně vydává opravy hotfix. Od verze 2005 se při aktualizaci na novou hlavní verzi (například 1.2005. x na 1.2008. x) automaticky nainstalují nejnovější opravy hotfix (pokud nějaké jsou) v nové hlavní verzi. Od tohoto okamžiku předem, pokud byla vydána oprava hotfix pro sestavení, měli byste ji nainstalovat.

> [!NOTE]
> Verze oprav hotfix centra Azure Stack jsou kumulativní; potřebujete jenom nainstalovat nejnovější opravu hotfix, abyste získali všechny opravy, které jsou součástí všech předchozích verzí oprav hotfix pro danou verzi.

Další informace najdete v našich [zásadách obsluhy](azure-stack-servicing-policy.md).

Opravy hotfix centra Azure Stack se vztahují pouze na integrované systémy Azure Stack hub. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="hotfix-prerequisites-before-applying-the-2008-update"></a>Požadavky na opravu hotfix: před instalací aktualizace 2008

Verze 2008 centra Azure Stack se musí použít ve verzi 2005 s následujícími opravami hotfix:

- [1.2005.25.92 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4595074)

### <a name="after-successfully-applying-the-2008-update"></a>Po úspěšné instalaci aktualizace 2008

Při aktualizaci na novou hlavní verzi (například 1.2005. x na 1.2008. x) se automaticky nainstalují nejnovější opravy hotfix (pokud nějaké jsou) v nové hlavní verzi. Od tohoto okamžiku předem, pokud byla vydána oprava hotfix pro sestavení, měli byste ji nainstalovat.

Pokud se následně uvolní nějaké opravy hotfix 2008, měli byste po instalaci 2008 nainstalovat tyto opravy:

- [1.2008.20.102 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4595075)
::: moniker-end

::: moniker range="azs-2005"
## <a name="2005-build-reference"></a>2005 – odkaz na sestavení

Číslo buildu aktualizace centra Azure Stack 2005 je **1.2005.6.53**.

### <a name="update-type"></a>Typ aktualizace

Typ sestavení aktualizace centra Azure Stack 2005 je **plný**.

Velikost balíčku aktualizace 2005 je v porovnání s předchozími aktualizacemi větší. Zvýšení velikosti má za následek delší dobu stahování. Aktualizace zůstane ve stavu **přípravy** po dlouhou dobu a operátoři můžou očekávat, že tento proces trvá déle než u předchozích aktualizací. Aktualizace 2005 obsahovala v našem interním testování – 4 uzly následující očekávané moduly runtime: 13-20 hodin, 8 uzlů: 16-26 hodin, 12 uzlů: 19-32 hodiny, 16 uzlů: 22-38 hodin. Přesné běhové moduly pro aktualizaci jsou obvykle závislé na kapacitě používané v systému podle zatížení klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a specifikacemi hardwaru systému. Běhové moduly, které jsou kratší nebo delší než očekávaná hodnota, nejsou neobvyklé a nevyžadují akci Azure Stack operátory centra, pokud se aktualizace nezdařila. Tato přibližná doba běhu je specifická pro aktualizaci 2005 a neměla by se porovnávat s jinými aktualizacemi centra Azure Stack.

Další informace o typech sestavení aktualizací najdete v tématu [Správa aktualizací v centru Azure Stack](azure-stack-updates.md).

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Co je nového

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

- Odeberou se akce, které se mají zastavit, vypnout a restartovat instanci role infrastruktury na portálu pro správu. V poskytovateli prostředků infrastruktury se odebraly taky odpovídající rozhraní API. Následující rutiny PowerShellu v modulu Správce RM a AZ Preview pro Azure Stack hub už nefungují: **stop-AzsInfrastructureRoleInstance**, **Disable-InfrastructureRoleInstance** a **restart-InfrastructureRoleInstance**. Tyto rutiny se odeberou z dalšího správce AZ Module Release for Azure Stack hub.
- Centrum Azure Stack 2005 teď podporuje [App Service na Azure Stack hub 2020 (verze 87. x)](app-service-release-notes-2020-Q2.md).
- Nastavení šifrování uživatele vyžadované pro monitorování hardwaru bylo změněno z DES na AES, aby se zvýšilo zabezpečení. Obraťte se na svého hardwarového partnera, kde se dozvíte, jak změnit nastavení v řadiči pro správu základní desky (BMC). Po provedení změny v řadiči pro správu základní desky může být potřeba znovu spustit rutinu **set-BmcCredential** pomocí koncového bodu privilegovaná. Další informace najdete v tématu [otočení tajných kódů v centru Azure Stack](azure-stack-rotate-secrets.md) .

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

- [1.2002.63.167 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4595072)

### <a name="after-successfully-applying-the-2005-update"></a>Po úspěšné instalaci aktualizace 2005

Od verze 2005 se při aktualizaci na novou hlavní verzi (například 1.2002. x na 1.2005. x) automaticky nainstalují nejnovější opravy hotfix (pokud nějaké jsou) v nové hlavní verzi.

Pokud se následně uvolní nějaké opravy hotfix 2005, měli byste po instalaci 2005 nainstalovat tyto opravy:

- [1.2005.25.92 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4595074)
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

### <a name="whats-new"></a>Co je nového

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
- Čas potřebný pro [proaktivní shromažďování protokolů i pro shromažďování protokolů na vyžádání](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002) byl snížen o 80%. Doba shromažďování protokolů může trvat déle než tato očekávaná hodnota, ale nevyžaduje akci Azure Stack operátory centra, pokud není shromažďování protokolů úspěšné.
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
- [1.1910.84.230 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4592243)

### <a name="after-successfully-applying-the-2002-update"></a>Po úspěšné instalaci aktualizace 2002

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix.

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [1.2002.63.167 opravy hotfix centra Azure Stack](https://support.microsoft.com/help/4595072)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1910"
## <a name="1910-archived-release-notes"></a>1910 archivované poznámky k verzi
::: moniker-end
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

::: moniker range="<azs-2002"
Do Galerie TechNet můžete získat přístup ke [starším verzím poznámky k verzi centra Azure Stack](https://aka.ms/azsarchivedrelnotes). Tyto archivované dokumenty jsou k dispozici pouze pro referenční účely a neznamenají podporu těchto verzí. Informace o podpoře centra Azure Stack najdete v tématu [zásady obsluhy centra Azure Stack](azure-stack-servicing-policy.md). Pokud potřebujete další pomoc, obraťte se na službu zákaznické podpory společnosti Microsoft.
::: moniker-end
