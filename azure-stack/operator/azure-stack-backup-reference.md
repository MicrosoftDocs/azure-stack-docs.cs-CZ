---
title: Referenční informace k Azure Stack infrastruktura zálohování služba | Dokumentace Microsoftu
description: Tento článek obsahuje referenční materiály služby zálohování Azure stacku infrastruktury.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: D6EC0224-97EA-446C-BC95-A3D32F668E2C
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 7b95dba4b3462b2c0e36aa1464faf217fccd7a6a
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617797"
---
# <a name="infrastructure-backup-service-reference"></a>Odkaz na službu Backup infrastruktury

## <a name="azure-backup-infrastructure"></a>Zálohování infrastruktury Azure

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Azure Stack se skládá z mnoha služeb, které tvoří na portálu Azure Resource Manageru a prostředí správy infrastruktury. Prostředí správy zařízení jako služby Azure Stack se zaměřuje na snížení složitosti vystaveni operátor řešení.

Zálohování infrastruktury slouží k internalize složitost zálohování a obnovení dat pro služby infrastruktury, zajištění operátory soustředit se na řešení pro správu a údržbu smlouvu SLA pro uživatele.

Export data záloh do externí sdílené složky se vyžaduje, aby ukládání záloh ve stejném systému. Správce vyžaduje externí sdílené složky poskytuje flexibilitu při určení místa k uložení dat na základě existujících zásad společnosti BC/zotavení po Havárii. 

### <a name="infrastructure-backup-components"></a>Součásti infrastruktury zálohování

Zálohování infrastruktury zahrnuje následující součásti:

 - **Záložní řadič infrastruktury**  
 Záložní řadič infrastruktury je vytvořena instance s a je každý Cloud Azure Stack.
 - **Poskytovatel prostředků zálohování**  
 Zálohování poskytovatele prostředků (RP zálohování) se skládá z uživatelského rozhraní a aplikací programu rozhraní (API) s odhalují základní funkce zálohování infrastruktury Azure stacku.

#### <a name="infrastructure-backup-controller"></a>Záložní řadič infrastruktury

Záložní řadič infrastruktury je Service Fabric pro cloudu Azure Stack získá vytvořit instanci služby. Záložní prostředky se vytvářejí na místní úrovni a zachycení oblast služby data ze Správce prostředků Azure AD, certifikační Autority, CRP, SRP, NRP, trezor klíčů, RBAC. 

### <a name="backup-resource-provider"></a>Poskytovatel prostředků zálohování

Zálohování poskytovatele prostředků uvede uživatelského rozhraní na portálu Azure Stack pro základní konfiguraci a seznam záložní prostředky. Operátor může provádět následující operace v uživatelském rozhraní:

 - Povolit zálohování poprvé zadáním externím úložišti, přihlašovací údaje a šifrovacího klíče
 - Zobrazit dokončené vytvořené zálohy a stav prostředků v části Vytvoření
 - Změnit umístění úložiště, kde záložní řadič umístí zálohovaných dat
 - Upravit přihlašovací údaje, které záložní řadič se používá pro přístup k externím úložišti
 - Změnit šifrovací klíč, který používá záložní řadič pro šifrování záloh 


## <a name="backup-controller-requirements"></a>Požadavky na zálohování Kontroleru

Tato část popisuje důležité požadavky pro zálohování infrastruktury. Doporučujeme, abyste si informace o pečlivě před povolit zálohování vaší instance služby Azure Stack a pak zpátky na ni odkazovat podle potřeby během nasazení a následná operace.

Požadavky zahrnují:

  - **Požadavky na software** -popisuje podporované úložištím a pokyny k nastavení velikosti. 
  - **Požadavky na síťovou** -popisuje požadavky na síť pro umístění jiného úložiště.  

### <a name="software-requirements"></a>Požadavky na software

#### <a name="supported-storage-locations"></a>Umístění úložiště podporuje

| Umístění úložiště                                                                 | Podrobnosti                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sdílené složky SMB hostované na zařízení úložiště v rámci prostředí důvěryhodné sítě | Sdílená složka protokolu SMB ve stejném datacentru, které se nasadí Azure Stack nebo v jiném datovém centru. Více instancí služby Azure Stack můžete použít stejnou sdílenou složku. |
| Sdílené složky SMB v Azure                                                          | Není aktuálně podporováno.                                                                                                                                 |
| Úložiště objektů BLOB v Azure                                                            | Není aktuálně podporováno.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Podporované verze protokolu SMB

| SMB | Version |
|-----|---------|
| SMB | 3.x     |

#### <a name="storage-location-sizing"></a>Nastavení velikosti umístění úložiště 

Infrastruktura zálohování řadič bude zálohovat data na vyžádání. Doporučuje se zálohování alespoň dvakrát den a udržování maximálně sedmidenní zálohy. 

**1811 a novější**

| Škálování prostředí | Očekávaná velikost zálohy | Celkové množství místa potřebné |
|-------------------|--------------------------|--------------------------------|
| 4 až 16 uzlů        | 20 GB                    | 280 GB                        |
| ASDK              | 10 GB                    | 140 GB                        |

**Pre-1811**

| Škálování prostředí | Očekávaná velikost zálohy | Celkové množství místa potřebné |
|-------------------|--------------------------|--------------------------------|
| 4 až 16 uzlů, ASDK  | 10 GB                     | 140 GB                        |

### <a name="network-requirements"></a>Síťové požadavky

| Umístění úložiště                                                                 | Podrobnosti                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sdílené složky SMB hostované na zařízení úložiště v rámci prostředí důvěryhodné sítě | Port 445 je vyžadována, pokud instance služby Azure Stack se nachází v prostředí s bránou firewall. Infrastruktura zálohování řadič bude inicializovat připojení a souborový server SMB přes port 445. |
| Pokud chcete použít plně kvalifikovaný název domény souborový server, musí jít přeložit z období název             |                                                                                                                                                                                         |

> [!Note]  
> Žádné příchozí porty musí být otevřen.

### <a name="encryption-requirements"></a>Požadavky na šifrování

Od 1901 se zálohovací služby infrastruktury budete používat certifikát s veřejným klíčem (. CER) k šifrování zálohovaných dat a certifikát s privátním klíčem (. PFX) k dešifrování záloh dat během obnovení cloudu.   
 - Certifikát se používá pro přenos klíče a se používá k navázání zabezpečené ověřené komunikaci. Z tohoto důvodu může být certifikát certifikát podepsaný svým držitelem. Azure Stack není potřeba ověřte kořenové nebo vztah důvěryhodnosti pro tento certifikát, aby nebylo třeba externí přístup k Internetu.
 
Certifikát podepsaný svým držitelem je k dispozici ve dvou částech, jednu s veřejným klíčem a jednu s privátním klíčem:
 - Šifrování zálohovaných dat: Certifikát s veřejným klíčem (exportovat do. Soubor CER) se používá k šifrování zálohovaných dat
 - Dešifrovat data záloh: Certifikát s privátním klíčem (exportovat do. Soubor PFX) slouží k dekódování dat záloh

Certifikát s veřejným klíčem (. Interní tajných kódů otočení nespravuje CER). Obměna certifikátu, budete muset vytvořit nový certifikát podepsaný svým držitelem a aktualizovat nastavení zálohování pomocí nového souboru (. CER).  
 - Všechny existující zálohy zůstanou zašifrována pomocí předchozí veřejného klíče. Nové zálohování použije nový veřejný klíč. 
 
Certifikát používaný při obnovení cloudu s privátním klíčem (. PFX) není z bezpečnostních důvodů trvalý ve službě Azure Stack. Tento soubor bude potřeba explicitně zadat při obnovení cloudu.  

**Zpětně režim kompatibility** od 1901 se šifrovací klíč podpory je zastaralá a bude v budoucí verzi odebrána. Pokud jste aktualizovali z 1811 pomocí služby backup už povolená pomocí šifrovacího klíče, Azure Stack bude nadále používat šifrovací klíč. Režim kompatibility zpětně bude podporována po dobu aspoň 3 verze. Po uplynutí této doby se bude vyžadovat certifikát. 
 * Aktualizace z šifrovací klíč k certifikátu je jednosměrná operace.  
 * Všechny existující zálohy zůstanou šifrovaná pomocí šifrovacího klíče. Certifikát používají nových záloh. 

## <a name="infrastructure-backup-limits"></a>Omezení infrastruktury zálohování

Tato omezení zvažte při plánování, nasazení a provozování vašich instancí služby Microsoft Azure Stack. Následující tabulka popisuje tyto limity.

### <a name="infrastructure-backup-limits"></a>Omezení infrastruktury zálohování

| Identifikátor omezení                                                 | Omezení        | Komentáře                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Typ zálohování                                                      | Pouze úplné    | Infrastruktura zálohování kontroler podporuje pouze úplné zálohy. Přírůstkové zálohování se nepodporují.                                          |
| Naplánované zálohování                                                | Plánovaná a ruční  | Záložní řadič podporuje zálohování na vyžádání a plánované                                                                                 |
| Maximální počet souběžných úloh zálohování                                   | 1            | Na instanci zálohování řadiče je podporována pouze jedna aktivní úloha zálohování.                                                                  |
| Konfigurace přepínače sítě                                     | Není v oboru | Správce musí zálohování konfigurace přepínače sítě pomocí nástrojů pro výrobce OEM. V dokumentaci pro Azure Stack vám poskytne dodavatel každý výrobce OEM. |
| Hardware Lifecycle Host                                          | Není v oboru | Správce musí zálohovat hostitele životního cyklu hardwaru pomocí nástroje pro výrobce OEM. V dokumentaci pro Azure Stack vám poskytne dodavatel každý výrobce OEM.      |
| Maximální počet sdílených složek                                    | 1            | Pouze jedné sdílené složce je možné ukládat data záloh                                                                                        |
| Zálohování SQL App Services, funkce, data poskytovatele prostředků mysql | Není v oboru | Přečtěte si pokyny k publikování pro nasazení a správa hodnotu – přidejte RPs vytvořené microsoftem.                                                  |
| Poskytovatelé prostředků zálohování třetích stran                              | Není v oboru | Najdete pokyny k publikování pro nasazení a správě přidanou hodnotu RPs vytvořené pomocí jiných výrobců.                                          |

## <a name="next-steps"></a>Další postup

 - Další informace o službě zálohování infrastruktury najdete v tématu [zálohování a obnovení dat pro Azure Stack pomocí infrastruktury služby Backup](azure-stack-backup-infrastructure-backup.md).
