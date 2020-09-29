---
title: Osvědčené postupy pro Infrastructure Backup služby – centrum Azure Stack
description: Při nasazení a správě centra Azure Stack použijte tyto osvědčené postupy, které vám pomůžou snížit riziko ztráty dat v případě závažného selhání.
author: justinha
ms.topic: article
ms.date: 02/08/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: fe0fa50ca2dfd69475fe2726042332c6ce9f51ad
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573118"
---
# <a name="infrastructure-backup-service-best-practices"></a>Osvědčené postupy pro službu Infrastructure Backup

Při nasazení a správě centra Azure Stack použijte tyto osvědčené postupy, které vám pomůžou snížit riziko ztráty dat v případě závažného selhání.

Projděte si osvědčené postupy pravidelně a ověřte, že instalace stále dodržuje předpisy při provádění změn v průběhu provozu. Pokud při implementaci těchto osvědčených postupů podržíte všechny problémy, požádejte o nápovědu podpora Microsoftu.

## <a name="configuration-best-practices"></a>Osvědčené postupy konfigurace

### <a name="deployment"></a>Nasazení

Po nasazení každého cloudu centra Azure Stack povolte Infrastructure Backup. Pomocí centra Azure Stack PowerShell můžete naplánovat zálohování z libovolného klienta nebo serveru s přístupem ke koncovému bodu rozhraní API pro správu operátorů.

### <a name="networking"></a>Sítě

Řetězec UNC (Universal Naming Convention) pro cestu musí používat plně kvalifikovaný název domény (FQDN). IP adresu je možné použít, pokud není možné překlad názvů. Řetězec UNC určuje umístění prostředků, jako jsou třeba sdílené soubory nebo zařízení.

### <a name="encryption"></a>Šifrování

#### <a name="version-1901-and-newer"></a>Verze 1901 a novější

Šifrovací certifikát se používá k šifrování zálohovaných dat, která se exportují do externího úložiště. Certifikát může být certifikát podepsaný svým držitelem, protože certifikát se používá jenom k přenosu klíčů. Další informace o tom, jak vytvořit certifikát, najdete v článku New-SelfSignedCertificate.
  
Klíč musí být uložený v zabezpečeném umístění (například globální Azure Key Vault certifikát). Formát CER certifikátu slouží k šifrování dat. Formát PFX se musí použít během nasazování Cloud Recovery Azure Stack hub k dešifrování zálohovaných dat.

![Certifikát uložený na bezpečném místě.](media/azure-stack-backup/azure-stack-backup-encryption-store-cert.png)

#### <a name="1811-and-older"></a>1811 a starší

Šifrovací klíč se používá k šifrování zálohovaných dat, která se exportují do externího úložiště. Klíč se vygeneruje jako součást [Povolení zálohování pro Azure Stackho centra pomocí PowerShellu](azure-stack-backup-enable-backup-powershell.md).

Klíč musí být uložený v zabezpečeném umístění (například globální Azure Key Vault tajný klíč). Tento klíč se musí použít při opětovném nasazení centra Azure Stack.

![Klíč byl uložen do zabezpečeného umístění.](media/azure-stack-backup/azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>Osvědčené provozní postupy

### <a name="backups"></a>Zálohování

 - Úlohy zálohování se spustí, když je systém spuštěný, takže nedochází k výpadkům v prostředí pro správu nebo k uživatelským aplikacím. Očekává se, že úlohy zálohování budou trvat 20-40 minut pro řešení, které je pod rozumnou zátěží.
 - Automatické zálohování se nespustí během oprav a operací aktualizace a jednotky FRU. Ve výchozím nastavení se úlohy naplánovaných záloh přeskočí. Žádosti o zálohování na vyžádání se zablokují i během těchto operací.    
 - Pomocí uvedených pokynů výrobce OEM, ručně zálohovaných síťových přepínačů a hostitele životního cyklu (HLH) by měly být uloženy ve stejné sdílené složce zálohy, kde řadič Infrastructure Backup ukládá data zálohy roviny ovládacího prvku. Zvažte ukládání konfigurací přepínačů a HLH do složky region (oblast). Pokud máte ve stejné oblasti více instancí centra Azure Stack, zvažte použití identifikátoru pro každou konfiguraci, která patří do jednotky škálování.

### <a name="folder-names"></a>Názvy složek

 - Infrastruktura vytváří složku MASBACKUP automaticky. Toto je sdílená složka spravovaná Microsoftem. Sdílené složky můžete vytvořit na stejné úrovni jako MASBACKUP. Nedoporučujeme vytvářet složky nebo data úložiště uvnitř MASBACKUP, která se Azure Stack hub nevytváří.
 -  Plně kvalifikovaný název domény a oblast v názvu složky pro rozlišení zálohovaných dat z různých cloudů. Plně kvalifikovaný název domény nasazení a koncových bodů centra Azure Stack je kombinací parametru region a parametru názvu externí domény. Další informace najdete v tématu [integrace služby Azure Stack hub Datacenter – DNS](azure-stack-integrate-dns.md).

Například sdílená složka zálohy je AzSBackups hostovaná v fileserver01.contoso.com. V této sdílené složce může být pro každé Azure Stack nasazení centra pomocí názvu externí domény a podsložky, která používá název oblasti.

Plně kvalifikovaný název domény: contoso.com  
Oblast: NYC

```console
    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup
```

Složka MASBackup je místo, kde Azure Stack hub ukládá data záloh. Tuto složku nepoužívejte k ukládání vlastních dat. Výrobci OEM by tuto složku neměli používat k ukládání zálohovaných dat buď.

Výrobci OEM doporučujeme ukládat data záloh pro své komponenty do složky region. Každý síťový přepínač, hostitel životního cyklu hardwaru (HLH) a tak dále může být uložený ve své vlastní podsložce. Příklad:

```console
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
| Zálohování se nepovedlo kvůli problémům s připojením.             | V síti mezi Azure Stackovým centrem a sdíleným souborem dochází k problémům.                          | Vyřešte problém se sítí a zkuste zálohování znovu.                                                                                            |
| Zálohování se nezdařilo z důvodu chyby v cestě.                | Cestu ke sdílené složce nelze přeložit.                                                          | Namapujte sdílenou složku z jiného počítače, aby bylo zajištěno, že je sdílená složka dostupná. Je možné, že budete muset cestu aktualizovat, pokud již není platná.       |
| Zálohování se nepovedlo kvůli problému s ověřováním.               | Může se jednat o problém s přihlašovacími údaji nebo se síťovým problémem, který ovlivňuje ověřování.    | Namapujte sdílenou složku z jiného počítače, aby bylo zajištěno, že je sdílená složka dostupná. Je možné, že budete muset aktualizovat přihlašovací údaje, pokud už nejsou platné. |
| Zálohování se nezdařilo z důvodu obecné chyby.                    | Neúspěšný požadavek může být způsoben přerušovaným problémem. Zkuste zálohování zopakovat.                    | Zavolejte podporu.                                                                                                                               |

## <a name="next-steps"></a>Další kroky

Projděte si referenční materiály pro [službu Infrastructure Backup](azure-stack-backup-reference.md).

Povolte [službu Infrastructure Backup](azure-stack-backup-enable-backup-console.md).
