---
title: Nejčastější dotazy k Azure Stack HCI
description: Nejčastější dotazy Azure Stack HCL.
ms.topic: conceptual
author: JohnCobb1
ms.author: v-johcob
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/24/2020
ms.openlocfilehash: 3606c62c562cb7f402aaafae33a55336345b5e62
ms.sourcegitcommit: af4374755cb4875a7cbed405b821f5703fa1c8cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95812633"
---
# <a name="azure-stack-hci-faq"></a>Nejčastější dotazy k Azure Stack HCI
Nejčastější dotazy k rozhraní Azure Stack HCI obsahují část Azure Stack připojení HCI a obecné části Nejčastější dotazy.

## <a name="azure-stack-hci-connectivity"></a>Azure Stack připojení HCI
Azure Stack HCI je místní vysoce sblížená infrastruktura, která se dodává jako Azure Hybrid Service. Software Azure Stack HCI nainstalujete na fyzických serverech, které řídíte v místním prostředí. Pak se připojíte k Azure pro cloudové monitorování, podporu, fakturaci a volitelné funkce pro správu a zabezpečení. V této části najdete informace o tom, jak Azure Stack HCI využívají cloud, a řeší často kladené otázky týkající se požadavků a chování připojení.

### <a name="your-data-stays-on-premises"></a>Vaše data zůstanou v místním prostředí.

**Odesílají se data uložená v Azure Stack HCL do cloudu?**

Ne. Názvy, metadata, konfigurace a obsah místních virtuálních počítačů se nikdy neodesílají do cloudu, pokud pro tento účel nezapnete další služby, například Azure Backup nebo Azure Site Recovery. Nebo pokud tyto virtuální počítače nezaregistrujete jednotlivě do služeb Cloud Management, jako je Azure ARC.

### <a name="edge-local-management-and-control"></a>Řízení a řízení hraničních zařízení – místní

**Prochází se přes Cloud rovina ovládacího prvku Azure Stack HCI?**

Ne. Pomocí nástrojů pro centrální správu, jako je centrum pro správu Windows, PowerShell nebo System Center, můžete spravovat přímo hostitelskou infrastrukturu a virtuální počítače i v případě, že je vaše síťové připojení k cloudu mimo provoz nebo má závažné omezení. Běžné každodenní operace, jako je přesun virtuálního počítače mezi hostiteli, výměna jednotky, která selhala, nebo konfigurace IP adres nezávisí na cloudu. Cloudové připojení se ale potřebuje k získání aktualizací softwaru v letadle, změně registrace Azure nebo použití funkcí, které přímo využívají cloudové služby pro zálohování, monitorování a další funkce.

**Existují požadavky na šířku pásma nebo latenci mezi Azure Stackmi HCI a cloudem?**

Ne. Připojení s omezením šířky pásma, jako jsou linky venkovského T1 nebo satelitní/mobilní připojení, jsou dostačující pro Azure Stack ke synchronizaci HCI. Minimální požadované připojení je jen několik kilobajtů za den. Další služby mohou vyžadovat větší šířku pásma, zejména pro replikaci nebo zálohování celých virtuálních počítačů, stažení velkých aktualizací softwaru nebo nahrání podrobných protokolů pro analýzu a monitorování v cloudu.

### <a name="designed-for-intermittent-and-limited-connectivity"></a>Navrženo pro přerušované a omezené připojení

**Vyžaduje Azure Stack HCI nepřetržité připojení ke cloudu?**

Ne. Azure Stack HCI je navržená tak, aby zpracovávala tečky s omezeným nebo nulovým připojením.

**Co se stane, když se síťové připojení ke cloudu dočasně rozroste?**

I když je připojení mimo provoz, veškerá hostitelská infrastruktura a virtuální počítače budou normálně běžet a můžete použít nástroje pro správu na hraničních zařízeních. Nebudete moct používat funkce, které přímo spoléhají na cloudové služby. Informace v Azure Portal také budou zastaraly, dokud Azure Stack HCL nebude možné znovu synchronizovat.

**Jak dlouho může Azure Stack rozhraní HCI běžet s připojením dolů?**

Minimálně Azure Stack HCI se musí úspěšně synchronizovat s Azure jednou za 30 po sobě jdoucích dnů.

**Co se stane, když se limit 30 dní překročí?**

Pokud se Azure Stack HCI nesynchronizoval s Azure během více než 30 po sobě jdoucích dnů, v Azure Portal a dalších nástrojích **se zobrazí stav** připojení ke clusteru a cluster pak zahájí režim s omezenou funkčností. V tomto režimu zůstane hostitelská infrastruktura v provozu a všechny aktuální virtuální počítače budou fungovat normálně. Nové virtuální počítače ale nelze vytvořit, dokud nebude možné znovu synchronizovat Azure Stack HCL. Interním technickým důvodem je to, že platnost vygenerovaného cloudu clusteru vypršela a že je potřeba ho obnovit synchronizací v Azure.

### <a name="understanding-sync"></a>Principy synchronizace

**Jaký obsah Azure Stack v cloudu synchronizovat?**

To závisí na tom, jaké funkce používáte. Minimální Azure Stack rozhraní HCI synchronizuje základní informace o clusteru, aby se zobrazily v Azure Portal (například seznam clusterovaných uzlů, hardwarový model a verze softwaru). fakturační informace, které shrnuje časově rozlišené dny od poslední synchronizace; a minimální požadované diagnostické informace, které vám pomohou společnosti Microsoft zachovat zabezpečení Azure Stack HCI a správně pracovat. Celková velikost je malá – několik kilobajtů. Pokud zapnete další služby, můžou nahrávat víc: Azure Log Analytics by například mohl nahrávat protokoly a čítače výkonu pro monitorování.

**Jak často Azure Stack v cloudu synchronizaci HCI?**

To závisí na tom, jaké funkce používáte. Minimální Azure Stack se rozhraní HCI pokusí o synchronizaci každých 12 hodin. Pokud synchronizace není úspěšná, obsah se uchová místně a pošle se s další úspěšnou synchronizací. Kromě tohoto pravidelného časovače můžete ručně synchronizovat kdykoli, a to pomocí `Sync-AzureStackHCI` rutiny PowerShellu nebo centra pro správu systému Windows. Pokud zapnete další služby, můžou se nahrávat častěji. například Azure Log Analytics by se do monitorování nahrál každých 5 minut.

### <a name="data-residency"></a>Rezidence dat

**Kde se synchronizují informace skutečně procházejí?**

Azure Stack HCI se synchronizuje s Azure a ukládá data do zabezpečeného datacentra ovládaného Microsoftem. Další informace najdete v tématu [zasídlí dat v Azure](https://azure.microsoft.com/global-infrastructure/data-residency/).

### <a name="disconnected-or-air-gapped"></a>Odpojeno nebo "air-gapped"

**Můžu použít Azure Stack HCL a nikdy se nepřipojují k Azure?**

Ne. Azure Stack HCI se musí úspěšně synchronizovat s Azure jednou za 30 po sobě jdoucích dnů.

**Můžu převést data do offline režimu mezi "air-gapped" Azure Stack HCL a Azure?**

Ne. Momentálně není k dispozici žádný mechanismus pro registraci a synchronizaci mezi místními počítači a Azure bez připojení k síti. Například nemůžete přenášet certifikáty nebo fakturační data pomocí vyměnitelného úložiště. Pokud je k dispozici dostatek zákaznických požadavků, otevřeme, abychom tuto funkci prozkoumali v budoucnu. Dejte nám vědět ve [Azure Stack Fórum o zpětné vazbě rozhraní HCI](https://feedback.azure.com/forums/929833-azure-stack-hci).

## <a name="azure-stack-hci-general-faqs"></a>Azure Stack obecné Nejčastější dotazy pro HCI

**Jak se vztahuje Azure Stack HCL na Windows Server?**

Windows Server je základem skoro každého produktu Azure a všechny funkce, které zadáváte, jsou nadále vydány s podporou ve Windows serveru. Počáteční nabídka Azure Stack HCL byla založená na Windows serveru 2019 a používala tradiční licenční model Windows serveru. V dnešní době má Azure Stack HCI svůj vlastní operační systém a licenční model na základě předplatného. Azure Stack HCI je doporučený způsob, jak nasadit lokálně v místním prostředí s využitím hardwaru ověřeného společností Microsoft od našich partnerů.

**Můžu upgradovat z Windows serveru 2019 na Azure Stack HCI?**

V tuto chvíli není k dispozici žádný místní upgrade ze systému Windows Server na Azure Stack HCL. Udržujte si přehled o konkrétních pokynech k migraci pro zákazníky, kteří na počítačích využívají Windows Server 2019 a 2016.

**Které služby Azure se mohu připojit k Azure Stack HCI?**

Aktualizovaný seznam služeb Azure, ke kterým se můžete připojit Azure Stack HCL, najdete v tématu [připojení Windows serveru k Azure Hybrid Services](/windows-server/manage/windows-admin-center/azure/index).

**Co jsou běžné řešení Azure Stack hub a Azure Stack HCL?**

Azure Stack rozhraní HCI obsahuje stejné technologie pro výpočetní prostředky, úložiště a sítě založené na technologii Hyper-V, jako centrum Azure Stack. Obě nabídky splňují přísná kritéria testování a ověřování, která zajišťují spolehlivost a kompatibilitu s podkladovou hardwarovou platformou.

**Jaký je mezi nimi rozdíl?**

Pomocí centra Azure Stack spustíte cloudové služby v místním prostředí. Můžete spouštět služby Azure IaaS a PaaS v místním prostředí a konzistentně vytvářet a spouštět cloudové aplikace odkudkoli a spravovat je s Azure Portal v místním prostředí.

Díky Azure Stack HCI spouštíte virtualizované úlohy místně, spravované pomocí centra pro správu Windows a známých nástrojů Windows serveru. Můžete se také připojit k Azure pro hybridní scénáře, jako jsou cloudové Site Recovery, monitorování a další.

**Můžu upgradovat z Azure Stack HCL na Azure Stack hub?**

Ne, ale zákazníci můžou migrovat své úlohy z Azure Stack HCI na Azure Stack hub nebo Azure.

**Návody identifikovat Azure Stack Server HCI?**

Centrum pro správu systému Windows obsahuje seznam operačních systémů v seznamu všechna připojení a na různých dalších místech, případně můžete použít následující příkaz prostředí PowerShell pro dotaz na název a verzi operačního systému.

```PowerShell
Get-ComputerInfo -Property 'osName', 'osDisplayVersion'
```

Tady je příklad výstupu:

```
OsName                    OSDisplayVersion
------                    ----------------
Microsoft Azure Stack HCI 20H2
```
