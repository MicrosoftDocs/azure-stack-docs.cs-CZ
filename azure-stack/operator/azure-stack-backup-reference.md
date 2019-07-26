---
title: Odkaz na službu Azure Stack Infrastructure Backup | Microsoft Docs
description: Tento článek obsahuje referenční materiál pro službu Azure Stack Infrastructure Backup.
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
ms.openlocfilehash: d45b11eb70533125ff8136763be24a3333c1f7dc
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68493950"
---
# <a name="infrastructure-backup-service-reference"></a>Odkaz na službu Infrastructure Backup

## <a name="azure-backup-infrastructure"></a>Infrastruktura zálohování Azure

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Azure Stack se skládá z mnoha služeb, které tvoří portál, Azure Resource Manager a prostředí správy infrastruktury. Prostředí pro správu na základě zařízení Azure Stack se zaměřuje na omezení složitosti vystavené pro operátor řešení.

Infrastructure Backup je navržená tak, aby Internalize složitost zálohování a obnovování dat pro služby infrastruktury, a zajistila tak, že se operátoři můžou soustředit na správu řešení a údržbu smlouvy SLA pro uživatele.

Export zálohovaných dat do externí sdílené složky je nutný k tomu, aby se zabránilo ukládání záloh do stejného systému. Vyžadování externí sdílené složky umožňuje správcům flexibilitu určit, kam se mají ukládat data na základě stávajících zásad BC/DR společnosti. 

### <a name="infrastructure-backup-components"></a>Infrastructure Backup komponenty

Infrastructure Backup obsahuje následující součásti:

 - **Řadič Infrastructure Backup**  
 Řadič Infrastructure Backup má vytvořenou instanci a je umístěn v každém Azure Stack cloudu.
 - **Poskytovatel prostředků pro zálohování**  
 Zprostředkovatel prostředků zálohování (Backup RP) se skládá z uživatelského rozhraní a rozhraní API (Application Program Interface), které zveřejňuje základní funkce zálohování pro Azure Stack infrastrukturu.

#### <a name="infrastructure-backup-controller"></a>Řadič Infrastructure Backup

Řadič Infrastructure Backup je Service Fabric služba, která se má vytvořit pro Azure Stack Cloud. Prostředky zálohování se vytvářejí na regionální úrovni a shromažďují data služeb specifická pro oblast ze služby AD, CA, Azure Resource Manager, CRP, SRP, NRP, trezoru klíčů a RBAC. 

### <a name="backup-resource-provider"></a>Poskytovatel prostředků pro zálohování

Zprostředkovatel prostředků zálohování prezentuje uživatelské rozhraní na portálu Azure Stack pro základní konfiguraci a seznam prostředků zálohy. Operátor může v uživatelském rozhraní provádět následující operace:

 - Povolit zálohování poprvé, protože poskytuje umístění externího úložiště, přihlašovací údaje a šifrovací klíč
 - Zobrazení dokončených prostředků zálohování a stavových prostředků v části Vytvoření
 - Úprava umístění úložiště, kde záložní řadič ukládá data záloh
 - Úprava přihlašovacích údajů, které řadič zálohování používá pro přístup k umístění externího úložiště
 - Úprava šifrovacího klíče, který řadič zálohování používá k šifrování záloh 


## <a name="backup-controller-requirements"></a>Požadavky na řadič zálohování

Tato část popisuje důležité požadavky na Infrastructure Backup. Doporučujeme, abyste pečlivě prostudovali informace před povolením zálohování pro instanci Azure Stack a pak se na ni později v průběhu nasazení a následné operace odkázat.

Mezi tyto požadavky patří:

  - **Požadavky na software** – popisuje podporovaná umístění úložiště a pokyny k velikosti. 
  - **Požadavky na síť** – popisuje požadavky na síť pro různá umístění úložiště.  

### <a name="software-requirements"></a>Požadavky na software

#### <a name="supported-storage-locations"></a>Podporovaná umístění úložiště

| Umístění úložiště                                                                 | Podrobnosti                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sdílená složka SMB hostovaná na úložném zařízení v rámci důvěryhodného síťového prostředí | Sdílená složka SMB ve stejném datovém centru, kde je nasazený Azure Stack nebo v jiném datovém centru. Stejná sdílená složka může používat více instancí Azure Stack. |
| Sdílená složka SMB v Azure                                                          | Aktuálně se nepodporuje.                                                                                                                                 |
| Úložiště objektů BLOB v Azure                                                            | Aktuálně se nepodporuje.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Podporované verze protokolu SMB

| SMB | Version |
|-----|---------|
| SMB | 3.x     |

#### <a name="smb-encryption"></a>Šifrování protokolu SMB

**1907 a novější**

Služba zálohování infrastruktury podporuje přenos zálohovaných dat do externího umístění úložiště s povoleným šifrováním SMB na straně serveru. Pokud server nepodporuje šifrování protokolu SMB nebo nemá povolenou funkci, služba zálohování infrastruktury se vrátí k přenosu nešifrovaných dat. Zálohovaná data umístěná v umístění externích úložišť se vždycky šifrují v klidovém stavu a nezávisí na šifrování SMB. 

#### <a name="storage-location-sizing"></a>Velikost umístění úložiště 

Doporučení se zálohuje v posledních dvou dnech a zachová se jenom po dobu sedmi dnů zálohování. Toto je výchozí chování při povolování zálohování infrastruktury v Azure Stack. 

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
| Sdílená složka SMB hostovaná na úložném zařízení v rámci důvěryhodného síťového prostředí | Pokud se instance Azure Stack nachází v prostředí brány firewall, je vyžadován port 445. Řadič Infrastructure Backup inicializuje připojení k souborovému serveru SMB přes port 445. |
| Aby bylo možné použít plně kvalifikovaný název domény souborového serveru, musí být název přeložitelný z PEP.             |                                                                                                                                                                                         |

> [!Note]  
> Nemusíte otevírat žádné příchozí porty.

### <a name="encryption-requirements"></a>Požadavky na šifrování

Od 1901. služba zálohování infrastruktury použije certifikát s veřejným klíčem (. CER) k šifrování zálohovaných dat a certifikátů pomocí privátního klíče (. PFX) k dešifrování zálohovaných dat během obnovení cloudu.   
 - Certifikát se používá pro přenos klíčů a nepoužívá se k navázání zabezpečené ověřené komunikace. Z tohoto důvodu může být certifikát podepsaný svým držitelem. Azure Stack nemusí pro tento certifikát ověřovat kořen nebo vztah důvěryhodnosti, takže není vyžadován externí přístup k Internetu.
 
Certifikát podepsaný svým držitelem se nachází ve dvou částech, jeden s veřejným klíčem a jedním s privátním klíčem:
 - Šifrovat zálohovaná data: Certifikát s veřejným klíčem (exportováno do. Soubor CER) se používá k šifrování zálohovaných dat.
 - Dešifrovat zálohovaná data: Certifikát s privátním klíčem (exportováno do. Soubor PFX) se používá k dešifrování zálohovaných dat.

Certifikát s veřejným klíčem (. CER) není spravován interním otočením v tajných klíčích. Pokud chcete certifikát otočit, budete muset vytvořit nový certifikát podepsaný svým držitelem a aktualizovat nastavení zálohování novým souborem (. CER).  
 - Všechny existující zálohy zůstanou šifrované pomocí předchozího veřejného klíče. Nové zálohy budou používat nový veřejný klíč. 
 
Certifikát použitý při obnovení cloudu s privátním klíčem (. PFX) není Azure Stack z bezpečnostních důvodů trvalá. Tento soubor bude nutné zadat explicitně během obnovování cloudu.  

**Režim zpětné kompatibility** Od verze 1901 je podpora šifrovacího klíče zastaralá a v budoucí verzi se odebere. Pokud jste aktualizovali z 1811 se zálohou, která už je povolená pomocí šifrovacího klíče, Azure Stack bude i nadále používat šifrovací klíč. Režim zpětné kompatibility bude podporován minimálně pro 3 verze. Po uplynutí této doby bude vyžadován certifikát. 
 * Způsob aktualizace ze šifrovacího klíče na certifikát je jednosměrná operace.  
 * Všechny existující zálohy zůstanou šifrované pomocí šifrovacího klíče. Nové zálohy použijí certifikát. 

## <a name="infrastructure-backup-limits"></a>Omezení Infrastructure Backup

Při plánování, nasazování a provozování instancí Microsoft Azure Stack Vezměte v úvahu tato omezení. Následující tabulka popisuje tato omezení.

### <a name="infrastructure-backup-limits"></a>Omezení Infrastructure Backup

| Identifikátor omezení                                                 | Omezení        | Komentáře                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Typ zálohování                                                      | Pouze úplné    | Řadič Infrastructure Backup podporuje pouze úplné zálohování. Přírůstkové zálohování se nepodporuje.                                          |
| Naplánovaná zálohování                                                | Naplánováno a ruční  | Kontrolér zálohování podporuje plánované zálohy a zálohování na vyžádání.                                                                                 |
| Maximální počet souběžných úloh zálohování                                   | 1            | Pro každou instanci řadiče zálohování je podporována pouze jedna aktivní úloha zálohování.                                                                  |
| Konfigurace síťového přepínače                                     | Není v oboru | Správce musí zálohovat konfiguraci síťového přepínače pomocí nástrojů OEM. Informace o Azure Stack poskytovaných každým dodavatelem OEM najdete v dokumentaci. |
| Hostitel životního cyklu hardwaru                                          | Není v oboru | Správce musí zálohovat hostitele životního cyklu hardwaru pomocí nástrojů OEM. Informace o Azure Stack poskytovaných každým dodavatelem OEM najdete v dokumentaci.      |
| Maximální počet sdílených složek                                    | 1            | K ukládání zálohovaných dat se dá použít jenom jedna sdílená složka.                                                                                        |
| Data poskytovatele prostředků pro zálohování App Services, funkce, SQL, MySQL | Není v oboru | Informace najdete v pokynech publikovaných pro nasazení a správu hodnot – přidat RPs vytvořené Microsoftem.                                                  |
| Zálohování poskytovatelů prostředků třetích stran                              | Není v oboru | Informace najdete v pokynech publikovaných pro nasazení a správu hodnot – přidat RPs vytvořené dodavateli třetích stran.                                          |

## <a name="next-steps"></a>Další postup

 - Další informace o službě Infrastructure Backup najdete v tématu [zálohování a obnovení dat pro Azure Stack se službou Infrastructure Backup](azure-stack-backup-infrastructure-backup.md).
