---
title: Odkaz na službu Infrastructure Backup
description: Referenční materiál pro službu Infrastructure Backup v centru Azure Stack
author: PatAltimore
ms.topic: article
ms.date: 02/12/2019
ms.author: patricka
ms.reviewer: hectorl
ms.lastreviewed: 10/25/2019
ms.openlocfilehash: 7fc20dfa20c0088f81d386d2b371b6a17bf45d5f
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871612"
---
# <a name="infrastructure-backup-service-reference"></a>Odkaz na službu Infrastructure Backup

## <a name="azure-backup-infrastructure"></a>Infrastruktura zálohování Azure

Centrum Azure Stack se skládá z mnoha služeb, které tvoří portál (Azure Resource Manager) a celkové prostředí pro správu infrastruktury. Prostředí pro správu Azure Stackového centra se zaměřuje na aplikace, aby se snížila složitost vystavená operátorovi řešení.

Služba Infrastructure Backup je navržená tak, aby Internalize složitost zálohování a obnovování dat pro služby infrastruktury, a zajistila tak, že se operátoři můžou soustředit na správu řešení a údržbu smlouvy SLA pro uživatele.

Export zálohovaných dat do externí sdílené složky je nutný k tomu, aby se zabránilo ukládání záloh do stejného systému. Vyžadování externí sdílené složky dává správci flexibilitu při rozhodování, kam ukládat data na základě stávajících zásad nacházejících z existujících společností a zotavení po havárii.

### <a name="infrastructure-backup-service-components"></a>Součásti Infrastructure Backup služby

Služba Infrastructure Backup zahrnuje tyto komponenty:

 - **Řadič Infrastructure Backup**  
 Řadič Infrastructure Backup má vytvořenou instanci a je umístěn v každém cloudu centra Azure Stack.
 - **Poskytovatel prostředků pro zálohování**  
 Zprostředkovatel prostředků zálohování (záložní RP) se skládá z uživatelského rozhraní a rozhraní API, které zveřejňují základní funkce zálohování pro Azure Stack infrastrukturu centra.

#### <a name="infrastructure-backup-controller"></a>Řadič Infrastructure Backup

Řadič Infrastructure Backup je Service Fabric služba, která vytváří instance pro cloud služby Azure Stack hub. Prostředky zálohování se vytvářejí na regionální úrovni a zachytí data služeb specifická pro oblast ze služby AD, CA, Azure Resource Manager, CRP, SRP, NRP, Key Vault a RBAC.

### <a name="backup-resource-provider"></a>Poskytovatel prostředků pro zálohování

Poskytovatel prostředků zálohování prezentuje uživatelské rozhraní na portálu centra Azure Stack pro základní konfiguraci a seznam prostředků zálohy. Operátory mohou v uživatelském rozhraní provádět následující akce:

 - Zapněte zálohování poprvé tak, že zadáte umístění externího úložiště, přihlašovací údaje a šifrovací klíč.
 - Zobrazení dokončených prostředků zálohování a stavových prostředků v části Vytvoření.
 - Upravte umístění úložiště, ve kterém záložní řadič ukládá data záloh.
 - Upravte přihlašovací údaje, které řadič zálohování používá pro přístup k umístění externího úložiště.
 - Upravte šifrovací klíč, který řadič zálohování používá k šifrování záloh.


## <a name="backup-controller-requirements"></a>Požadavky na řadič zálohování

Tato část popisuje důležité požadavky služby Infrastructure Backup Service. Doporučujeme pečlivě zkontrolovat informace, než povolíte zálohování pro vaši instanci centra Azure Stack, a pak se na ni v průběhu nasazení a následné operaci vrátíte zpátky.

Mezi tyto požadavky patří:

  - **Požadavky na software** – popisuje podporovaná umístění úložiště a pokyny k velikosti. 
  - **Požadavky na síť** – popisuje požadavky na síť pro různá umístění úložiště.  

### <a name="software-requirements"></a>Požadavky na software

#### <a name="supported-storage-locations"></a>Podporovaná umístění úložiště

| Umístění úložiště                                                                 | Podrobnosti                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sdílená složka SMB hostovaná na úložném zařízení v rámci důvěryhodného síťového prostředí. | Sdílená složka SMB ve stejném datovém centru, kde je nasazený Azure Stack rozbočovač nebo v jiném datovém centru. Stejná sdílená složka může používat více instancí centra Azure Stack. |
| Sdílená složka SMB v Azure.                                                          | Aktuálně se nepodporuje.                                                                                                                                 |
| Úložiště objektů BLOB v Azure.                                                            | Aktuálně se nepodporuje.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Podporované verze protokolu SMB

| SMB | Verze |
|-----|---------|
| SMB | 3.x     |

#### <a name="smb-encryption"></a>Šifrování protokolu SMB

::: moniker range=">=azs-1907"

Služba Infrastructure Backup podporuje přenos zálohovaných dat do externího umístění úložiště s povoleným šifrováním SMB na straně serveru. Pokud server nepodporuje šifrování protokolu SMB nebo tato funkce není povolená, Infrastructure Backup služba se vrátí k přenosu nešifrovaných dat. Zálohovaná data umístěná v umístění externího úložiště se vždycky šifrují v klidovém stavu a nejsou závislá na šifrování SMB.

::: moniker-end

#### <a name="storage-location-sizing"></a>Velikost umístění úložiště

Doporučujeme, abyste se v poslední době provedli zálohování v posledních dvou dnech a zachovali jste několik dní zálohování. Toto je výchozí chování při povolování zálohování infrastruktury v centru Azure Stack.

| Škálování prostředí | Předpokládané velikosti zálohy | Celková velikost požadovaných míst |
|-------------------|--------------------------|--------------------------------|
| uzly 4-16        | 20 GB                    | 280 GB                        |
| ASDK              | 10 GB                    | 140 GB                        |

### <a name="network-requirements"></a>Požadavky sítě

| Umístění úložiště                                                                 | Podrobnosti                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sdílená složka SMB hostovaná na úložném zařízení v rámci důvěryhodného síťového prostředí. | Port 445 se vyžaduje, pokud se instance centra Azure Stack nachází v prostředí s bránou firewall. Řadič Infrastructure Backup inicializuje připojení k souborovému serveru SMB přes port 445. |
| Aby bylo možné použít plně kvalifikovaný název domény souborového serveru, musí být název přeložitelný z PEP.             |                                                                                                                                                                                         |

#### <a name="firewall-rules"></a>Pravidla brány firewall
Ujistěte se, že jste nastavili pravidla brány firewall, která umožní připojení mezi virtuálními počítači s ERCS do umístění externího úložiště. 

| Zdroj | Cíl | Protokol/port |
|------------------|-----------------------|--------------------------------|
| VIRTUÁLNÍ POČÍTAČ ERCS 1        | Umístění úložiště      | 445/SMB                        |
| VIRTUÁLNÍ POČÍTAČ ERCS 2        | Umístění úložiště      | 445/SMB                        |
| VIRTUÁLNÍ POČÍTAČ ERCS 3        | Umístění úložiště      | 445/SMB                        |

> [!Note]  
> Nemusíte otevírat žádné příchozí porty.

### <a name="encryption-requirements"></a>Požadavky na šifrování

Počínaje 1901 bude služba Infrastructure Backup používat certifikát s veřejným klíčem (. CER) k šifrování zálohovaných dat a certifikátů pomocí privátního klíče (. PFX) k dešifrování zálohovaných dat během obnovení cloudu.

 - Certifikát se používá pro přenos klíčů a nepoužívá se k navázání zabezpečené ověřené komunikace. Z tohoto důvodu může být certifikát podepsaný svým držitelem. Rozbočovač Azure Stack nepotřebuje pro tento certifikát ověřit kořen nebo vztah důvěryhodnosti, aby externí internetový přístup nemusel.

Certifikát podepsaný svým držitelem se nachází ve dvou částech, jeden s veřejným klíčem a jedním s privátním klíčem:

 - Šifrovat zálohovaná data: certifikát s veřejným klíčem (exportováno do. Soubor CER) slouží k šifrování zálohovaných dat.
 - Dešifrovat zálohovaná data: certifikát s privátním klíčem (exportováno do. Soubor PFX) slouží k dešifrování zálohovaných dat.

Certifikát s veřejným klíčem (. CER) není spravováno interním otočením v tajných klíčích. Chcete-li certifikát otočit, je nutné vytvořit nový certifikát podepsaný svým držitelem a aktualizovat nastavení zálohování novým souborem (. CER). 
 
 - Všechny existující zálohy zůstanou šifrované pomocí předchozího veřejného klíče. Nové zálohy používají nový veřejný klíč.
 
Certifikát použitý při obnovení cloudu s privátním klíčem (. PFX) není z bezpečnostních důvodů trvale Azure Stack hub. Tento soubor bude nutné zadat explicitně během obnovování cloudu.  

**Režim zpětné kompatibility** Od verze 1901 je podpora šifrovacího klíče zastaralá a v budoucí verzi se odebere. Pokud jste aktualizovali z 1811 se zálohou, která už je povolená pomocí šifrovacího klíče, Azure Stack centrum bude dál používat šifrovací klíč. Režim zpětné kompatibility bude podporován minimálně pro tři verze. Po uplynutí této doby bude vyžadován certifikát.

 * Aktualizace z šifrovacího klíče na certifikát je jednosměrná operace.  
 * Všechny existující zálohy zůstanou šifrované pomocí šifrovacího klíče. Nové zálohy použijí certifikát. 

## <a name="infrastructure-backup-limits"></a>Omezení služby Infrastructure Backup

Při plánování, nasazování a provozování instancí centra Microsoft Azure Stack Vezměte v úvahu tato omezení. Následující tabulka popisuje tato omezení.

### <a name="infrastructure-backup-limits"></a>Omezení Infrastructure Backup

| Identifikátor omezení                                                 | Omezení        | Komentáře                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Typ zálohy                                                      | Pouze úplné    | Řadič Infrastructure Backup podporuje pouze úplné zálohování. Přírůstkové zálohování se nepodporuje.                                          |
| Naplánovaná zálohování                                                | Naplánováno a ruční  | Řadič zálohování podporuje plánované zálohy a zálohování na vyžádání.                                                                                 |
| Maximální počet souběžných úloh zálohování                                   | 1            | Pro každou instanci řadiče zálohování je podporována pouze jedna aktivní úloha zálohování.                                                                  |
| Konfigurace síťového přepínače                                     | Není v oboru | Správce musí zálohovat konfiguraci síťového přepínače pomocí nástrojů OEM. Informace najdete v dokumentaci pro centrum Azure Stack od každého dodavatele OEM. |
| Hostitel životního cyklu hardwaru                                          | Není v oboru | Správce musí zálohovat hostitele životního cyklu hardwaru pomocí nástrojů OEM. Informace najdete v dokumentaci pro centrum Azure Stack od každého dodavatele OEM.      |
| Maximální počet sdílených složek                                    | 1            | K ukládání zálohovaných dat lze použít pouze jednu sdílenou složku.                                                                                        |
| Data poskytovatele prostředků pro zálohování App Services, funkce, SQL, MySQL | Není v oboru | Informace najdete v pokynech publikovaných pro nasazení a správu hodnot – přidat RPs vytvořené Microsoftem.                                                  |
| Zálohování poskytovatelů prostředků třetích stran                              | Není v oboru | Informace najdete v pokynech publikovaných pro nasazení a správu hodnot – přidat RPs vytvořené dodavateli třetích stran.                                          |

## <a name="next-steps"></a>Další kroky

 - Další informace o službě Infrastructure Backup najdete v tématu [zálohování a obnovení dat pro Azure Stack centra pomocí služby Infrastructure Backup](azure-stack-backup-infrastructure-backup.md).
