---
title: Povolení zálohování pro Azure Stack na portálu pro správu | Microsoft Docs
description: Povolte službu Infrastructure Backup prostřednictvím portálu pro správu, aby bylo možné Azure Stack obnovit v případě selhání.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 08/21/2019
ms.openlocfilehash: 84dd8fdc91a796a94df71926788e62d98b5ef1ae
ms.sourcegitcommit: 22814dd79664206a260ba0160ed3ae2612e33495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69976174"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>Povolení zálohování pro Azure Stack na portálu pro správu
Povolte službu Infrastructure Backup prostřednictvím portálu pro správu, aby Azure Stack mohla generovat zálohy infrastruktury. Hardwarový partner může tyto zálohy použít k obnovení prostředí pomocí cloudového obnovení v případě [závažného selhání](./azure-stack-backup-recover-data.md). Účelem cloudového obnovení je zajistit, aby se operátoři a uživatelé mohli po dokončení obnovení přihlásit zpátky na portál. Uživatelé budou mít obnovená předplatná, včetně oprávnění a rolí přístupu na základě role, původních plánů, nabídek a dříve definovaných výpočetních, úložných a síťových kvót a Key Vault tajných klíčů.

Služba Infrastructure Backup ale nezálohuje virtuální počítače s IaaS, konfigurace sítě a prostředky úložiště, jako jsou účty úložiště, objekty blob, tabulky atd., takže se uživatelé přihlásí po dokončení cloudového obnovení nebudou zobrazovat žádné z předchozích stávajících prostředky. Prostředky platformy jako služby (PaaS) a data také nejsou službou zálohována. 

Správci a uživatelé zodpovídají za zálohování a obnovování prostředků IaaS a PaaS odděleně od procesů zálohování infrastruktury. Informace o zálohování prostředků IaaS a PaaS najdete na následujících odkazech:

- [Virtual Machines](../user/azure-stack-manage-vm-protect.md)
- [App Service](https://docs.microsoft.com/azure/app-service/manage-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>Povolení nebo překonfigurování zálohování

1. Otevřete [portál pro správu Azure Stack](azure-stack-manage-portals.md).
2. Vyberte **všechny služby**a potom v kategorii **Správa** vyberte **zálohování infrastruktury**. V okně **zálohování infrastruktury** vyberte **Konfigurace** .
3. Zadejte cestu k **umístění úložiště zálohy**. Použijte řetězec UNC (Universal Naming Convention) pro cestu ke sdílené složce hostované na samostatném zařízení. Řetězec UNC určuje umístění prostředků, jako jsou třeba sdílené soubory nebo zařízení. Pro službu můžete použít IP adresu. Aby se zajistila dostupnost zálohovaných dat po havárii, mělo by být zařízení v samostatném umístění.

    > [!Note]  
    > Pokud vaše prostředí podporuje překlad IP adres z Azure Stack sítě infrastruktury do vašeho podnikového prostředí, můžete místo IP adresy použít plně kvalifikovaný název domény.

4. Zadejte **uživatelské jméno** s použitím domény a uživatelského jména s dostatečným přístupem ke čtení a zápisu souborů. Například, `Contoso\backupshareuser`.
5. Zadejte **heslo** pro uživatele.
6. Zadejte heslo znovu a **potvrďte heslo**.
7. **Frekvence v hodinách** určuje, jak často se vytvářejí zálohy. Výchozí hodnota je 12. Scheduler podporuje maximálně 12 a minimálně 4. 
8. **Doba uchování ve dnech** určuje, kolik dní zálohování se uchová na externím umístění. Výchozí hodnota je 7. Scheduler podporuje maximálně 14 a minimálně 2. Zálohy starší než doba uchování jsou automaticky odstraněny z externího umístění.

    > [!Note]  
    > Pokud chcete archivovat zálohy starší než doba uchování, nezapomeňte soubory zálohovat předtím, než Plánovač odstraní zálohy. Pokud omezíte dobu uchovávání záloh (například ze 7 dnů na 5 dní), Plánovač odstraní všechny zálohy starší než nová doba uchování. Než tuto hodnotu aktualizujete, ujistěte se, že máte v pořádku zálohování záloh. 

9. V nastavení šifrování zadejte certifikát do pole soubor certifikátu. cer. Záložní soubory se šifrují pomocí tohoto veřejného klíče v certifikátu. Při konfiguraci nastavení zálohování byste měli zadat certifikát, který obsahuje jenom část veřejného klíče. Jakmile tento certifikát nastavíte poprvé nebo ho v budoucnu natočíte, můžete zobrazit jenom kryptografický otisk certifikátu. Nahraný soubor certifikátu se nedá stáhnout ani zobrazit. Chcete-li vytvořit soubor certifikátu, spusťte následující příkaz prostředí PowerShell pro vytvoření certifikátu podepsaného svým držitelem s veřejnými a soukromými klíči a exportujte certifikát pouze s částí veřejného klíče. Certifikát můžete uložit kdekoli, kde se dá dostat z portálu pro správu.

    ```powershell

        $cert = New-SelfSignedCertificate `
            -DnsName "www.contoso.com" `
            -CertStoreLocation "cert:\LocalMachine\My"

        New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 
        Export-Certificate `
            -Cert $cert `
            -FilePath c:\certs\AzSIBCCert.cer 
    ```

   > [!Note]
   > **1901 a vyšší**: Azure Stack přijímá certifikát k šifrování zálohovaných dat infrastruktury. Nezapomeňte uložit certifikát s veřejným a soukromým klíčem na bezpečném místě. Z bezpečnostních důvodů nedoporučujeme používat certifikát s veřejnými a soukromými klíči ke konfiguraci nastavení zálohování. Další informace o tom, jak spravovat životní cyklus tohoto certifikátu, najdete v tématu věnovaném osvědčeným postupům pro [Infrastructure Backup služby](azure-stack-backup-best-practices.md).
   > 
   > **1811 nebo starší**: Azure Stack přijímá symetrický klíč k šifrování zálohovaných dat infrastruktury. Pomocí [rutiny New-AzsEncryptionKey64 vytvořte klíč](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64). Po upgradu z 1811 na 1901 se v nastavení zálohování zachová šifrovací klíč. Doporučením je aktualizovat nastavení zálohování tak, aby používalo certifikát. Podpora šifrovacího klíče je nyní zastaralá. K aktualizaci nastavení pro použití certifikátu budete mít aspoň 3 verze. 

10. Výběrem **OK** uložte nastavení zálohovacího řadiče.

![Azure Stack – nastavení kontroleru zálohování](media/azure-stack-backup/backup-controller-settings-certificate.png)


## <a name="start-backup"></a>Spustit zálohování
Pokud chcete spustit zálohování, klikněte na **Zálohovat nyní** a spusťte zálohování na vyžádání. Zálohování na vyžádání nemění čas příštího plánovaného zálohování. Po dokončení úlohy můžete potvrdit nastavení v **Essentials**:

![Zálohování na vyžádání Azure Stack](media/azure-stack-backup/scheduled-backup.png)

Můžete také spustit rutinu PowerShellu **Start-AzsBackup** na počítači pro správu Azure Stack. Další informace najdete v tématu [zálohování Azure Stack](azure-stack-backup-back-up-azure-stack.md).

## <a name="enable-or-disable-automatic-backups"></a>Povolit nebo zakázat automatické zálohování
Zálohování se automaticky naplánuje při povolení zálohování. V části **základy**si můžete prohlédnout další čas zálohování plánu. 

![Zálohování na vyžádání Azure Stack](media/azure-stack-backup/on-demand-backup.png)

Pokud potřebujete zakázat budoucí naplánovaná zálohování, klikněte na **Zakázat automatické zálohování**. Vypnutím automatického zálohování se nakonfigurují nastavení zálohování a plán zálohování se zachová. Tato akce jednoduše instruuje Plánovač, že má přeskočit budoucí zálohy. 

![Azure Stack – zakázat plánované zálohy](media/azure-stack-backup/disable-auto-backup.png)

Potvrďte, že v **Essentials**jsou budoucí naplánovaná zálohování zakázaná:

![Azure Stack – potvrzení zálohování bylo zakázáno.](media/azure-stack-backup/confirm-disable.png)

Kliknutím na **Povolit automatické zálohování** informujte Plánovač o zahájení budoucích záloh v naplánovaném čase. 

![Azure Stack – povolení naplánovaných záloh](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> Pokud jste nakonfigurovali zálohování infrastruktury před aktualizací na 1807, automatické zálohování se zakáže. Tímto způsobem zálohy spouštěné nástrojem Azure Stack nejsou v konfliktu se zálohováním spuštěným externím modulem plánování úloh. Po zakázání všech externích plánovačů úloh klikněte na **Povolit automatické zálohování**.

## <a name="update-backup-settings"></a>Aktualizovat nastavení zálohování
Od 1901 je podpora šifrovacího klíče zastaralá. Pokud konfigurujete zálohování poprvé v 1901, je nutné použít certifikát. Azure Stack podporuje šifrovací klíč pouze v případě, že je klíč nakonfigurován před aktualizací na 1901. Režim zpětné kompatibility bude pokračovat pro tři verze. Potom už šifrovací klíče nebudou podporované. 

### <a name="default-mode"></a>Výchozí režim
Pokud konfigurujete zálohování při první instalaci nebo aktualizaci na 1901, musíte v nastavení šifrování nakonfigurovat zálohu pomocí certifikátu. Používání šifrovacího klíče již není podporováno. 

Pokud chcete aktualizovat certifikát použitý k šifrování zálohovaných dat, můžete nahrát nový. Soubor CER s částí veřejného klíče a kliknutím na tlačítko OK uložte nastavení. 

Nové zálohy začnou používat veřejný klíč v novém certifikátu. Neexistuje žádný vliv na všechny existující zálohy vytvořené pomocí předchozího certifikátu. Nezapomeňte zachovat starší certifikát v bezpečném umístění pro případ, že ho budete potřebovat k obnovení cloudu.

![Azure Stack – zobrazení kryptografického otisku certifikátu](media/azure-stack-backup/encryption-settings-thumbprint.png)

### <a name="backwards-compatibility-mode"></a>Režim zpětné kompatibility
Pokud jste před aktualizací na 1901 nakonfigurovali zálohování, nastavení se přenesou beze změny v chování. V takovém případě je šifrovací klíč podporován z důvodu zpětné kompatibility. Můžete aktualizovat šifrovací klíč nebo přepnout na použití certifikátu. Pokud chcete pokračovat v aktualizaci šifrovacího klíče, budete mít aspoň tři verze. Tuto dobu použijte k přechodu na certifikát. Pokud chcete vytvořit nový šifrovací klíč, použijte [New-AzsEncryptionKeyBase64](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64).

![Azure Stack – používání šifrovacího klíče v režimu zpětné kompatibility](media/azure-stack-backup/encryption-settings-backcompat-encryption-key.png)

> [!Note]  
> Aktualizace z šifrovacího klíče na certifikát je jednosměrná operace. Po provedení této změny nelze přepnout zpět na šifrovací klíč. Všechna stávající zálohování zůstanou zašifrovaná pomocí předchozího šifrovacího klíče. 

![Azure Stack – použít šifrovací certifikát v režimu zpětné kompatibility](media/azure-stack-backup/encryption-settings-backcompat-certificate.png)

## <a name="next-steps"></a>Další postup

Naučte se spustit zálohování. Viz [zálohování Azure Stack](azure-stack-backup-back-up-azure-stack.md)

Zjistěte, jak ověřit, jestli se zálohování spustilo. Viz [potvrzení zálohování dokončeno na portálu pro správu](azure-stack-backup-back-up-azure-stack.md) .
