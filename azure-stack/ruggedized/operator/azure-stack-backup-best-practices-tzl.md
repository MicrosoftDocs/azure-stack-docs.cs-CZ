---
title: Osvědčené postupy Infrastructure Backup služby pro Azure Stack | Microsoft Docs
description: Při nasazení a správě Azure Stack použijte tyto osvědčené postupy, které vám pomůžou snížit riziko ztráty dat v případě závažného selhání.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 12/11/2019
ms.openlocfilehash: d54381c185b51b880d452ebf303581c068072f9a
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97909833"
---
# <a name="infrastructure-backup-service-best-practices---ruggedized"></a>Osvědčené postupy Infrastructure Backup služby – robustní

*Platí pro: modulární datové centrum, Azure Stack centrum je robustní*

Pravidelně kontrolujte tyto osvědčené postupy, abyste ověřili, jestli je instalace pořád v souladu s tím, jak se provádí změny v toku operací. Pokud narazíte na nějaké problémy při implementaci těchto osvědčených postupů, požádejte o nápovědu podpora Microsoftu.

## <a name="configuration-best-practices"></a>Osvědčené postupy konfigurace

Zálohování infrastruktury je ve výchozím nastavení povolené během nasazování nového systému a ukládá se interně. Pomocí portálu Azure Stack nebo PowerShellu můžete zadat externí umístění úložiště pro export záloh do sekundárního umístění.

### <a name="networking"></a>Sítě

Řetězec UNC (Universal Naming Convention) pro cestu musí používat plně kvalifikovaný název domény (FQDN). IP adresu je možné použít, pokud není možné překlad názvů \' . Řetězec UNC určuje umístění prostředků, jako jsou třeba sdílené soubory nebo zařízení.

### <a name="encryption"></a>Šifrování

Šifrovací certifikát se používá k šifrování zálohovaných dat, která se exportují do externího úložiště. Certifikát může být certifikát podepsaný svým držitelem, protože certifikát se používá jenom k přenosu klíčů. Další informace o tom, jak vytvořit certifikát, najdete v tématu New-SelfSignedCertificate.

Certifikát musí být uložený v zabezpečeném umístění. Formát CER certifikátu se používá jenom k zašifrování dat a nepoužívá se k navázání komunikace.

## <a name="operational-best-practices"></a>Osvědčené provozní postupy

### <a name="backups"></a>Zálohování

- Úlohy zálohování se spustí, když je systém spuštěný, takže nedochází \' k výpadkům v prostředí pro správu nebo k uživatelským aplikacím. Očekává, že úlohy zálohování budou trvat 20-40 minut pro řešení, které má \' za rozumnou zátěží.

- Další pokyny, které jsou k dispozici pro ruční zálohování síťových přepínačů a hostitele životního cyklu hardwaru (HLH).

### <a name="folder-names"></a>Názvy složek

- Infrastruktura vytváří složku MASBACKUP automaticky. Toto je sdílená složka spravovaná Microsoftem. Sdílené složky můžete vytvořit na stejné úrovni jako MASBACKUP. \'Nedoporučujeme vytvářet složky nebo data úložiště uvnitř MASBACKUP, která Azure Stack \' nevytvoří.

- Plně kvalifikovaný název domény a oblast v názvu složky pro rozlišení zálohovaných dat z různých cloudů. Plně kvalifikovaný název domény nasazení Azure Stack a koncových bodů je kombinací parametru region a parametru názvu externí domény. Další informace najdete v tématu [Azure Stack Integration Datacenter – DNS](../../operator/azure-stack-integrate-dns.md).

Například sdílená složka zálohy je AzSBackups hostovaná v fileserver01.contoso.com. V této sdílené složce může být nasazení složky na Azure Stack pomocí názvu externí domény a podsložky, která používá název oblasti.

- Plně kvalifikovaný název domény: contoso.com
- Oblast: NYC

```shell
\\fileserver01.contoso.com\AzSBackups
\\fileserver01.contoso.com\AzSBackups\contoso.com
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup
```

`MASBackup`Složka je místo, kde Azure Stack ukládá data záloh. Tuto složku nepoužívejte k ukládání vlastních dat. Výrobci OEM by tuto složku neměli používat ani k ukládání zálohovaných dat.

Výrobci OEM doporučujeme ukládat data záloh pro své komponenty do složky region. Každý síťový přepínač, hostitel životního cyklu hardwaru (HLH) a tak dále může být uložený ve své vlastní podsložce. Například:

```shell
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
\\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration
```

### <a name="monitoring"></a>Monitorování

Systém podporuje následující výstrahy:

| Výstrahy                                                   | Popis                                                                                     | Náprava                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Zálohování se nezdařilo, protože sdílená složka je mimo kapacitu. | Sdílená složka nemá kapacitu a řadič zálohování nemůže exportovat záložní soubory do umístění. | Přidejte větší kapacitu úložiště a zkuste zálohování zopakovat. Odstraňte stávající zálohy (od nejstarší prvního) a uvolněte místo.                    |
| Zálohování se nepovedlo kvůli problémům s připojením.             | V síti mezi Azure Stack a sdíleným souborem dochází k problémům.                          | Vyřešte problém se sítí a zkuste zálohování znovu.                                                                                            |
| Zálohování se nezdařilo z důvodu chyby v cestě.                | Cestu ke sdílené složce nelze přeložit.                                                          | Namapujte sdílenou složku z jiného počítače, aby bylo zajištěno, že je sdílená složka dostupná. Je možné, že budete muset cestu aktualizovat, pokud již není platná.       |
| Zálohování se nepovedlo kvůli problému s ověřováním.               | Může se jednat o problém s přihlašovacími údaji nebo se síťovým problémem, který ovlivňuje ověřování.    | Namapujte sdílenou složku z jiného počítače, aby bylo zajištěno, že je sdílená složka dostupná. Je možné, že budete muset aktualizovat přihlašovací údaje, pokud už nejsou platné. |
| Zálohování se nezdařilo z důvodu obecné chyby.                    | Neúspěšný požadavek může být způsoben přerušovaným problémem. Zkuste zálohování zopakovat.                    | Zavolejte podporu.                                                                                                                               |

### <a name="infrastructure-backup-service-components"></a>Součásti Infrastructure Backup služby

Služba Infrastructure Backup zahrnuje následující součásti:

- **Infrastructure Backup Controller**: vytváří se instance řadiče Infrastructure Backup a v každém Azure Stack cloudu se nachází.

- **Poskytovatel prostředků pro zálohování**: poskytovatel prostředků zálohování (záložní RP) se skládá z uživatelského rozhraní a rozhraní API, které zveřejňují základní funkce zálohování pro infrastrukturu Azure Stack.

### <a name="infrastructure-backup-controller"></a>Řadič Infrastructure Backup

Řadič Infrastructure Backup je Service Fabric služba, která má vytvořenou instanci Azure Stackho cloudu. Prostředky zálohování se vytvářejí na regionální úrovni a zachytí data služby specifická pro oblast ze služeb AD, CA, Azure Resource Manager, CRP, SRP, NRP, Key Vault a RBAC.

### <a name="backup-resource-provider"></a>Poskytovatel prostředků pro zálohování

Zprostředkovatel prostředků zálohování prezentuje uživatelské rozhraní na portálu Azure Stack pro základní konfiguraci a seznam prostředků zálohy. Operátory mohou v uživatelském rozhraní provádět následující akce:

- Zapněte zálohování poprvé tak, že zadáte umístění externího úložiště, přihlašovací údaje a šifrovací klíč.
- Zobrazení dokončených prostředků zálohování a stavových prostředků v části Vytvoření.
- Upravte umístění úložiště, ve kterém záložní řadič ukládá data záloh.
- Upravte přihlašovací údaje, které řadič zálohování používá pro přístup k umístění externího úložiště.
- Upravte šifrovací certifikát, který řadič zálohování používá k šifrování záloh.

## <a name="backup-controller-requirements"></a>Požadavky na řadič zálohování

Tato část popisuje důležité požadavky služby Infrastructure Backup. Doporučujeme pečlivě zkontrolovat informace, než povolíte zálohování pro vaši instanci Azure Stack, a pak se na ni po nasazení a následné operaci vrátíte zpátky.

Mezi tyto požadavky patří:

- **Požadavky na software**: popisuje podporovaná umístění úložiště a pokyny k velikosti.
- **Požadavky na síť**: popisuje požadavky na síť pro různá umístění úložiště.

### <a name="software-requirements"></a>Požadavky na software

#### <a name="supported-storage-locations"></a>Podporovaná umístění úložiště

| Umístění úložiště                                                                  | Podrobnosti                                                                                                                                                  |
|-----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sdílená složka SMB hostovaná na úložném zařízení v rámci důvěryhodného síťového prostředí. | Sdílená složka SMB ve stejném datovém centru, kde je nasazený Azure Stack nebo v jiném datovém centru. Stejná sdílená složka může používat více instancí Azure Stack. |
| Sdílená složka SMB v Azure.                                                          | Aktuálně se nepodporuje.                                                                                                                                 |
| Úložiště objektů BLOB v Azure.                                                            | Aktuálně se nepodporuje.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Podporované verze protokolu SMB

|SMB  |Verze  |
|---------|---------|
|SMB     |   3.x      |

#### <a name="smb-encryption"></a>Šifrování protokolu SMB

Služba Infrastructure Backup podporuje přenos zálohovaných dat do externího umístění úložiště s povoleným šifrováním SMB na straně serveru. Pokud server nepodporuje šifrování protokolu SMB nebo není povolená funkce, služba Infrastructure Backup se vrátí k přenosu nešifrovaných dat. Zálohovaná data umístěná v umístění externích úložišť se vždycky šifrují v klidovém stavu a nezávisí na šifrování SMB.

#### <a name="storage-location-sizing"></a>Velikost umístění úložiště

Doporučujeme, abyste zálohovali alespoň dvakrát denně a zachovali po dobu až sedmi dnů zálohování. Toto je výchozí chování při povolování zálohování infrastruktury v Azure Stack.


|Škálování prostředí  |Předpokládané velikosti zálohy  |Celková velikost požadovaných míst  |
|---------|---------|---------|
|uzly 4-16     |  20 GB       |  280 GB       |
<!-- TZLASDKFIX 
|ASDK     |   10 GB      |   140 GB     |
-->
### <a name="network-requirements"></a>Požadavky sítě

|Umístění úložiště  |Podrobnosti  |
|---------|---------|
|Sdílená složka SMB hostovaná na úložném zařízení v rámci důvěryhodného síťového prostředí.     |  Pokud se instance Azure Stack nachází v prostředí brány firewall, je vyžadován port 445. Řadič Infrastructure Backup inicializuje připojení k souborovému serveru SMB přes port 445.       |
| Chcete-li použít plně kvalifikovaný název domény souborového serveru, musí být název přeložitelný z PEP.     |         |

> [!NOTE]
> Nemusíte otevírat žádné příchozí porty.

### <a name="encryption-requirements"></a>Požadavky na šifrování

Služba Infrastructure Backup používá certifikát s veřejným klíčem (. CER) k šifrování zálohovaných dat. Certifikát se používá pro přenos klíčů a nepoužívá se k navázání zabezpečené ověřené komunikace. Z tohoto důvodu může být certifikát podepsaný svým držitelem. Azure Stack nemusí pro tento certifikát ověřit kořen nebo vztah důvěryhodnosti, proto není externí internetový přístup nutný.

Certifikát podepsaný svým držitelem se nachází ve dvou částech, jeden s veřejným klíčem a jedním s privátním klíčem:

- Šifrovat zálohovaná data: certifikát s veřejným klíčem (exportováno do. Soubor CER) slouží k šifrování zálohovaných dat.
- Dešifrovat zálohovaná data: certifikát s privátním klíčem (exportováno do. Soubor PFX) slouží k dešifrování zálohovaných dat.

Certifikát s veřejným klíčem (. CER) není spravován interním otočením v tajných klíčích. Chcete-li certifikát otočit, je nutné vytvořit nový certifikát podepsaný svým držitelem a aktualizovat nastavení zálohování novým souborem (. CER).

Všechny existující zálohy zůstanou šifrované pomocí předchozího veřejného klíče. Nové zálohy používají nový veřejný klíč.

Z bezpečnostních důvodů certifikát použitý při obnovení cloudu s privátním klíčem (. PFX) není trvale Azure Stack.

## <a name="infrastructure-backup-limits"></a>Omezení Infrastructure Backup

Při plánování, nasazování a provozování instancí Microsoft Azure Stack Vezměte v úvahu tato omezení. Následující tabulka popisuje tato omezení.

|Identifikátor omezení  |Omezení  |Komentáře  |
|---------|---------|---------|
|Typ zálohy     | Pouze úplné        | Řadič Infrastructure Backup podporuje pouze úplné zálohování. Přírůstkové zálohování se nepodporuje.        |
|Naplánovaná zálohování     | Naplánováno a ruční        | Řadič zálohování podporuje plánované zálohy a zálohování na vyžádání.        |
|Maximální počet souběžných úloh zálohování      | 1        | Pro každou instanci řadiče zálohování je podporována pouze jedna aktivní úloha zálohování.        |
|Konfigurace síťového přepínače     | Není v oboru         | Správce musí zálohovat konfiguraci síťového přepínače pomocí nástrojů OEM. Informace o Azure Stack poskytovaných každým dodavatelem OEM najdete v dokumentaci.        |
|Hostitel životního cyklu hardwaru     | Není v oboru         | Správce musí zálohovat hostitele životního cyklu hardwaru pomocí nástrojů OEM. Informace o Azure Stack poskytovaných každým dodavatelem OEM najdete v dokumentaci.        |
|Maximální počet sdílených složek     | 1        | K ukládání zálohovaných dat lze použít pouze jednu sdílenou složku.        |
|Hodnota zálohy – přidat poskytovatele prostředků     | V oboru        | Záloha infrastruktury zahrnuje zálohování pro Event Hubs RP IoT Hub RP Data Box Edge RP.        |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [službě Infrastructure Backup](../../operator/azure-stack-backup-reference.md).
