---
title: Odkaz na službu Infrastructure Backup | Microsoft Docs
description: Referenční materiál pro službu Infrastructure Backup v Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: D6EC0224-97EA-446C-BC95-A3D32F668E2C
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 282d6f3a501550e49424c257b928e708f63ccadc
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974855"
---
# <a name="infrastructure-backup-service-reference"></a>Odkaz na službu Infrastructure Backup

## <a name="azure-backup-infrastructure"></a>Infrastruktura zálohování Azure

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Azure Stack se skládá z mnoha služeb, které tvoří portál (Azure Resource Manager) a celkové prostředí pro správu infrastruktury. Prostředí pro správu Azure Stack jako aplikace se zaměřuje na omezení složitosti vystavené pro operátor řešení.

Služba Infrastructure Backup je navržená tak, aby Internalize složitost zálohování a obnovování dat pro služby infrastruktury, a zajistila tak, že se operátoři můžou soustředit na správu řešení a údržbu smlouvy SLA pro uživatele.

Export zálohovaných dat do externí sdílené složky je nutný k tomu, aby se zabránilo ukládání záloh do stejného systému. Vyžadování externí sdílené složky dává správci flexibilitu při rozhodování, kam ukládat data na základě stávajících zásad nacházejících z existujících společností a zotavení po havárii.

### <a name="infrastructure-backup-service-components"></a>Součásti Infrastructure Backup služby

Služba Infrastructure Backup zahrnuje tyto komponenty:

 - **Řadič Infrastructure Backup**  
 Řadič Infrastructure Backup má vytvořenou instanci a je umístěn v každém Azure Stack cloudu.
 - **Poskytovatel prostředků pro zálohování**  
 Zprostředkovatel prostředků zálohování (záloha RP) se skládá z uživatelského rozhraní a rozhraní API, které zveřejňují základní funkce zálohování pro Azure Stack infrastrukturu.

#### <a name="infrastructure-backup-controller"></a>Řadič Infrastructure Backup

Řadič Infrastructure Backup je Service Fabric služba, která má vytvořenou instanci Azure Stackho cloudu. Prostředky zálohování se vytvářejí na regionální úrovni a zachytí data služeb specifická pro oblast ze služby AD, CA, Azure Resource Manager, CRP, SRP, NRP, Key Vault a RBAC.

### <a name="backup-resource-provider"></a>Poskytovatel prostředků pro zálohování

Zprostředkovatel prostředků zálohování prezentuje uživatelské rozhraní na portálu Azure Stack pro základní konfiguraci a seznam prostředků zálohy. Operátory mohou v uživatelském rozhraní provádět následující akce:

 - Zapněte zálohování poprvé tak, že zadáte umístění externího úložiště, přihlašovací údaje a šifrovací klíč.
 - Zobrazení dokončených prostředků zálohování a stavových prostředků v části Vytvoření.
 - Upravte umístění úložiště, ve kterém záložní řadič ukládá data záloh.
 - Upravte přihlašovací údaje, které řadič zálohování používá pro přístup k umístění externího úložiště.
 - Upravte šifrovací klíč, který řadič zálohování používá k šifrování záloh.


## <a name="backup-controller-requirements"></a>Požadavky na řadič zálohování

Tato část popisuje důležité požadavky služby Infrastructure Backup Service. Doporučujeme pečlivě zkontrolovat informace, než povolíte zálohování pro vaši instanci Azure Stack, a pak se na ni po nasazení a následné operaci vrátíte zpátky.

Mezi tyto požadavky patří:

  - **Požadavky na software** – popisuje podporovaná umístění úložiště a pokyny k velikosti. 
  - **Požadavky na síť** – popisuje požadavky na síť pro různá umístění úložiště.  

### <a name="software-requirements"></a>Požadavky na software

#### <a name="supported-storage-locations"></a>Podporovaná umístění úložiště

| Umístění úložiště                                                                 | Podrobnosti                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sdílená složka SMB hostovaná na úložném zařízení v rámci důvěryhodného síťového prostředí. | Sdílená složka SMB ve stejném datovém centru, kde je nasazený Azure Stack nebo v jiném datovém centru. Stejná sdílená složka může používat více instancí Azure Stack. |
| Sdílená složka SMB v Azure.                                                          | Aktuálně se nepodporuje.                                                                                                                                 |
| Úložiště objektů BLOB v Azure.                                                            | Aktuálně se nepodporuje.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Podporované verze protokolu SMB

| SMB | Version |
|-----|---------|
| SMB | 3.x     |

#### <a name="smb-encryption"></a>Šifrování protokolu SMB

**1907 a novější**

Služba Infrastructure Backup podporuje přenos zálohovaných dat do externího umístění úložiště s povoleným šifrováním SMB na straně serveru. Pokud server nepodporuje šifrování protokolu SMB nebo tato funkce není povolená, Infrastructure Backup služba se vrátí k přenosu nešifrovaných dat. Zálohovaná data umístěná v umístění externího úložiště se vždycky šifrují v klidovém stavu a nejsou závislá na šifrování SMB.

#### <a name="storage-location-sizing"></a>Velikost umístění úložiště

Doporučujeme, abyste se v poslední době provedli zálohování v posledních dvou dnech a zachovali jste několik dní zálohování. Toto je výchozí chování při povolování zálohování infrastruktury v Azure Stack.

**1907 a novější**

***Systém připojený k poskytovateli identit Azure AD***

| Škálování prostředí | Předpokládané velikosti zálohy | Celková velikost požadovaných míst |
|-------------------|--------------------------|--------------------------------|
| 4-16 uzlů/ASDK   | 1 GB                     | 20 GB                          |

***Systém připojený k podnikovému zprostředkovateli identity prostřednictvím služby ADFS***

| Škálování prostředí | Předpokládané velikosti zálohy | Celková velikost požadovaných míst |
|-------------------|--------------------------|--------------------------------|
| uzly 4-16        | 20 GB                    | 280 GB                        |
| ASDK              | 10 GB                    | 140 GB                        |

**Před 1907**

| Škálování prostředí | Předpokládané velikosti zálohy | Celková velikost požadovaných míst |
|-------------------|--------------------------|--------------------------------|
| uzly 4-16        | 20 GB                    | 280 GB                        |
| ASDK              | 10 GB                    | 140 GB                        |

### <a name="network-requirements"></a>Síťové požadavky

| Umístění úložiště                                                                 | Podrobnosti                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sdílená složka SMB hostovaná na úložném zařízení v rámci důvěryhodného síťového prostředí. | Pokud se instance Azure Stack nachází v prostředí brány firewall, je vyžadován port 445. Řadič Infrastructure Backup inicializuje připojení k souborovému serveru SMB přes port 445. |
| Aby bylo možné použít plně kvalifikovaný název domény souborového serveru, musí být název přeložitelný z PEP.             |                                                                                                                                                                                         |

> [!Note]  
> Nemusíte otevírat žádné příchozí porty.

### <a name="encryption-requirements"></a>Požadavky na šifrování

Počínaje 1901 bude služba Infrastructure Backup používat certifikát s veřejným klíčem (. CER) k šifrování zálohovaných dat a certifikátů pomocí privátního klíče (. PFX) k dešifrování zálohovaných dat během obnovení cloudu.

 - Certifikát se používá pro přenos klíčů a nepoužívá se k navázání zabezpečené ověřené komunikace. Z tohoto důvodu může být certifikát podepsaný svým držitelem. Azure Stack nemusí pro tento certifikát ověřit kořen nebo vztah důvěryhodnosti, takže externí přístup k Internetu není vyžadován.

Certifikát podepsaný svým držitelem se nachází ve dvou částech, jeden s veřejným klíčem a jedním s privátním klíčem:

 - Šifrovat zálohovaná data: Certifikát s veřejným klíčem (exportováno do. Soubor CER) slouží k šifrování zálohovaných dat.
 - Dešifrovat zálohovaná data: Certifikát s privátním klíčem (exportováno do. Soubor PFX) slouží k dešifrování zálohovaných dat.

Certifikát s veřejným klíčem (. CER) není spravováno interním otočením v tajných klíčích. Chcete-li certifikát otočit, je nutné vytvořit nový certifikát podepsaný svým držitelem a aktualizovat nastavení zálohování novým souborem (. CER). 
 
 - Všechny existující zálohy zůstanou šifrované pomocí předchozího veřejného klíče. Nové zálohy používají nový veřejný klíč.
 
Certifikát použitý při obnovení cloudu s privátním klíčem (. PFX) není Azure Stack z bezpečnostních důvodů trvalá. Tento soubor bude nutné zadat explicitně během obnovování cloudu.  

**Režim zpětné kompatibility** Od verze 1901 je podpora šifrovacího klíče zastaralá a v budoucí verzi se odebere. Pokud jste aktualizovali z 1811 se zálohou, která už je povolená pomocí šifrovacího klíče, Azure Stack bude i nadále používat šifrovací klíč. Režim zpětné kompatibility bude podporován minimálně pro tři verze. Po uplynutí této doby bude vyžadován certifikát.

 * Aktualizace z šifrovacího klíče na certifikát je jednosměrná operace.  
 * Všechny existující zálohy zůstanou šifrované pomocí šifrovacího klíče. Nové zálohy použijí certifikát. 

## <a name="infrastructure-backup-limits"></a>Omezení Infrastructure Backup

Při plánování, nasazování a provozování instancí Microsoft Azure Stack Vezměte v úvahu tato omezení. Následující tabulka popisuje tato omezení.

### <a name="infrastructure-backup-limits"></a>Omezení Infrastructure Backup

| Identifikátor omezení                                                 | Omezení        | Komentáře                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Typ zálohování                                                      | Pouze úplné    | Řadič Infrastructure Backup podporuje pouze úplné zálohování. Přírůstkové zálohování se nepodporuje.                                          |
| Naplánovaná zálohování                                                | Naplánováno a ruční  | Řadič zálohování podporuje plánované zálohy a zálohování na vyžádání.                                                                                 |
| Maximální počet souběžných úloh zálohování                                   | 1            | Pro každou instanci řadiče zálohování je podporována pouze jedna aktivní úloha zálohování.                                                                  |
| Konfigurace síťového přepínače                                     | Není v oboru | Správce musí zálohovat konfiguraci síťového přepínače pomocí nástrojů OEM. Informace o Azure Stack poskytovaných každým dodavatelem OEM najdete v dokumentaci. |
| Hostitel životního cyklu hardwaru                                          | Není v oboru | Správce musí zálohovat hostitele životního cyklu hardwaru pomocí nástrojů OEM. Informace o Azure Stack poskytovaných každým dodavatelem OEM najdete v dokumentaci.      |
| Maximální počet sdílených složek                                    | 1            | K ukládání zálohovaných dat lze použít pouze jednu sdílenou složku.                                                                                        |
| Data poskytovatele prostředků pro zálohování App Services, funkce, SQL, MySQL | Není v oboru | Informace najdete v pokynech publikovaných pro nasazení a správu hodnot – přidat RPs vytvořené Microsoftem.                                                  |
| Zálohování poskytovatelů prostředků třetích stran                              | Není v oboru | Informace najdete v pokynech publikovaných pro nasazení a správu hodnot – přidat RPs vytvořené dodavateli třetích stran.                                          |

## <a name="next-steps"></a>Další postup

 - Další informace o službě Infrastructure Backup najdete v tématu [zálohování a obnovení dat pro Azure Stack se službou Infrastructure Backup](azure-stack-backup-infrastructure-backup.md).
