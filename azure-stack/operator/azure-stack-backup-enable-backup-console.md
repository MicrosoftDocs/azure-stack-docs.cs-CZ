---
title: Povolení zálohování centra Azure Stack z portálu pro správu
description: Naučte se, jak povolit službu Infrastructure Backup z portálu pro správu, aby bylo možné v případě selhání obnovit centrum Azure Stack.
author: justinha
ms.topic: article
ms.date: 08/21/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 08/21/2019
ms.openlocfilehash: ce401b20d6baa66807e6ee5f7ee1e94503b653af
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "77703158"
---
# <a name="enable-backup-for-azure-stack-hub-from-the-administrator-portal"></a>Povolení zálohování centra Azure Stack z portálu pro správu

Službu Infrastructure Backup můžete povolit z portálu pro správu, aby centrum Azure Stack mohl generovat zálohy infrastruktury. Hardwarový partner může tyto zálohy použít k obnovení prostředí pomocí cloudového obnovení v případě [závažného selhání](./azure-stack-backup-recover-data.md). Účelem cloudového obnovení je zajistit, aby se operátoři a uživatelé mohli po dokončení obnovení přihlásit zpátky na portál. Uživatelé budou mít obnovená předplatná, včetně:

- Oprávnění a role přístupu na základě role.
- Původní plány a nabídky
- Dříve definované kvóty pro výpočetní prostředky, úložiště a sítě.
- Key Vault tajných klíčů.

Služba Infrastructure Backup ale nezálohuje virtuální počítače s IaaS, konfigurace sítě a prostředky úložiště, jako jsou účty úložiště, objekty blob, tabulky a tak dále. Uživatelům, kteří se přihlašují po obnovení cloudu, se nezobrazují žádné z těchto dříve existujících prostředků. Prostředky platformy jako služby (PaaS) a data také nejsou službou zálohována.

Správci a uživatelé zodpovídají za zálohování a obnovování prostředků IaaS a PaaS odděleně od procesů zálohování infrastruktury. Informace o zálohování prostředků IaaS a PaaS najdete na následujících odkazech:

- [Ochrana virtuálních počítačů nasazených v centru Azure Stack](../user/azure-stack-manage-vm-protect.md)
- [Zálohování aplikace v Azure](https://docs.microsoft.com/azure/app-service/manage-backup)
- [Co je SQL Server na virtuálních počítačích Azure? Systému](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>Povolení nebo překonfigurování zálohování

1. Otevřete [portál Azure Stack centrum pro správu](azure-stack-manage-portals.md).
2. Vyberte **všechny služby**a potom v kategorii **Správa** vyberte **zálohování infrastruktury**. V okně **zálohování infrastruktury** vyberte **Konfigurace** .
3. Zadejte cestu k **umístění úložiště zálohy**. Použijte řetězec UNC (Universal Naming Convention) pro cestu ke sdílené složce hostované na samostatném zařízení. Řetězec UNC určuje umístění prostředků, jako jsou třeba sdílené soubory nebo zařízení. Pro službu můžete použít IP adresu. Aby se zajistila dostupnost zálohovaných dat po havárii, mělo by být zařízení v samostatném umístění.

    > [!Note]  
    > Pokud vaše prostředí podporuje rozlišení názvů z sítě infrastruktury centra Azure Stack do vašeho podnikového prostředí, můžete místo IP adresy použít plně kvalifikovaný název domény (FQDN).

4. Zadejte **uživatelské jméno** s použitím domény a uživatelského jména s dostatečným přístupem ke čtení a zápisu souborů. Například, `Contoso\backupshareuser`.
5. Zadejte **heslo** pro uživatele.
6. Zadejte heslo znovu a **potvrďte heslo**.
7. **Frekvence v hodinách** určuje, jak často se vytvářejí zálohy. Výchozí hodnota je 12. Scheduler podporuje maximálně 12 a minimálně 4. 
8. **Doba uchování ve dnech** určuje, kolik dní zálohování se uchová na externím umístění. Výchozí hodnota je 7. Scheduler podporuje maximálně 14 a minimálně 2. Zálohy starší než doba uchování jsou automaticky odstraněny z externího umístění.

    > [!Note]  
    > Pokud chcete archivovat zálohy starší než doba uchování, nezapomeňte zálohovat soubory předtím, než Plánovač odstraní zálohy. Pokud omezíte dobu uchovávání záloh (například ze 7 dnů na 5 dní), Plánovač odstraní všechny zálohy starší než nová doba uchování. Před aktualizací této hodnoty se ujistěte, že jste v pořádku, jak se zálohují.

9. V nastavení šifrování zadejte certifikát do pole soubor certifikátu. cer. Záložní soubory se šifrují pomocí tohoto veřejného klíče v certifikátu. Zadejte certifikát, který obsahuje jenom část veřejného klíče při konfiguraci nastavení zálohování. Jakmile tento certifikát nastavíte poprvé nebo ho v budoucnu natočíte, můžete zobrazit jenom kryptografický otisk certifikátu. Nahraný soubor certifikátu se nedá stáhnout ani zobrazit. Chcete-li vytvořit soubor certifikátu, spusťte následující příkaz prostředí PowerShell pro vytvoření certifikátu podepsaného svým držitelem s veřejnými a soukromými klíči a exportujte certifikát pouze s částí veřejného klíče. Certifikát můžete uložit kdekoli, kde se dá dostat z portálu pro správu.

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
   > **1901 a vyšší**: centrum Azure Stack akceptuje certifikát k šifrování zálohovaných dat infrastruktury. Nezapomeňte uložit certifikát s veřejným a soukromým klíčem na bezpečném místě. Z bezpečnostních důvodů nedoporučujeme používat certifikát s veřejnými a soukromými klíči ke konfiguraci nastavení zálohování. Další informace o tom, jak spravovat životní cyklus tohoto certifikátu, najdete v tématu věnovaném [osvědčeným postupům pro Infrastructure Backup služby](azure-stack-backup-best-practices.md).
   > 
   > **1811 nebo starší**: centrum Azure Stack akceptuje symetrický klíč k šifrování zálohovaných dat infrastruktury. Pomocí [rutiny New-AzsEncryptionKey64 vytvořte klíč](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64). Po upgradu z 1811 na 1901 se v nastavení zálohování zachová šifrovací klíč. Doporučujeme, abyste nastavení zálohování aktualizovali na použití certifikátu. Podpora šifrovacího klíče je nyní zastaralá. K aktualizaci nastavení pro použití certifikátu máte aspoň 3 verze.

10. Výběrem **OK** uložte nastavení zálohovacího řadiče.

![Centrum Azure Stack – nastavení záložního kontroleru](media/azure-stack-backup/backup-controller-settings-certificate.png)


## <a name="start-backup"></a>Spustit zálohování
Pokud chcete spustit zálohování, klikněte na **Zálohovat nyní** a spusťte zálohování na vyžádání. Zálohování na vyžádání nemění čas příštího plánovaného zálohování. Po dokončení úlohy můžete potvrdit nastavení v **Essentials**:

![Azure Stack zálohování na vyžádání do centra](media/azure-stack-backup/scheduled-backup.png)

Rutinu PowerShellu **Start-AzsBackup** můžete spustit také na počítači správce centra Azure Stack. Další informace najdete v tématu [zálohování centra Azure Stack](azure-stack-backup-back-up-azure-stack.md).

## <a name="enable-or-disable-automatic-backups"></a>Povolit nebo zakázat automatické zálohování
Zálohování se automaticky naplánuje při povolení zálohování. V části **základy**si můžete prohlédnout další čas zálohování plánu. 

![Azure Stack zálohování na vyžádání do centra](media/azure-stack-backup/on-demand-backup.png)

Pokud potřebujete zakázat budoucí naplánovaná zálohování, klikněte na **Zakázat automatické zálohování**. Zakázání automatického zálohování udržuje nastavení zálohování nakonfigurované a zachovává plán zálohování. Tato akce jednoduše instruuje Plánovač, že má přeskočit budoucí zálohy.

![Azure Stack centrum – zakázat plánované zálohy](media/azure-stack-backup/disable-auto-backup.png)

Potvrďte, že v **Essentials**jsou budoucí naplánovaná zálohování zakázaná:

![Centrum Azure Stack – potvrzení zálohování je zakázané.](media/azure-stack-backup/confirm-disable.png)

Kliknutím na **Povolit automatické zálohování** informujte Plánovač o zahájení budoucích záloh v naplánovaném čase. 

![Centrum Azure Stack – povolení plánovaných záloh](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> Pokud jste nakonfigurovali zálohování infrastruktury před aktualizací na 1807, automatické zálohování se zakáže. Tímto způsobem se zálohování zahájené pomocí centra Azure Stack nekoliduje se zálohami spuštěnými externím modulem plánování úloh. Po zakázání všech externích plánovačů úloh klikněte na **Povolit automatické zálohování**.

## <a name="update-backup-settings"></a>Aktualizovat nastavení zálohování
Od 1901 je podpora šifrovacího klíče zastaralá. Pokud provádíte konfiguraci zálohování poprvé v 1901, je nutné použít certifikát. Centrum Azure Stack podporuje šifrovací klíč pouze v případě, že je klíč nakonfigurován před aktualizací na 1901. Režim zpětné kompatibility bude pokračovat pro tři verze. Potom už šifrovací klíče nebudou podporované.

### <a name="default-mode"></a>Výchozí režim
Pokud při konfiguraci zálohování infrastruktury poprvé po instalaci nebo aktualizaci na 1901 konfigurujete zálohu infrastruktury, musíte v nastavení šifrování nakonfigurovat certifikát. Používání šifrovacího klíče již není podporováno.

Pokud chcete aktualizovat certifikát použitý k šifrování zálohovaných dat, nahrajte nový. Soubor CER s částí veřejného klíče a kliknutím na tlačítko OK uložte nastavení.

Nové zálohy začnou používat veřejný klíč v novém certifikátu. Neexistují žádné důsledky pro všechny existující zálohy vytvořené pomocí předchozího certifikátu. Nezapomeňte zachovat starší certifikát v bezpečném umístění pro případ, že ho budete potřebovat k obnovení cloudu.

![Azure Stack hub – zobrazení kryptografického otisku certifikátu](media/azure-stack-backup/encryption-settings-thumbprint.png)

### <a name="backwards-compatibility-mode"></a>Režim zpětné kompatibility
Pokud jste před aktualizací na 1901 nakonfigurovali zálohování, nastavení se přenesou beze změny v chování. V takovém případě je šifrovací klíč podporován z důvodu zpětné kompatibility. Můžete aktualizovat šifrovací klíč nebo přepnout na použití certifikátu. Chcete-li pokračovat v aktualizaci šifrovacího klíče, máte alespoň tři verze. Tuto dobu použijte k přechodu na certifikát. Pokud chcete vytvořit nový šifrovací klíč, použijte [New-AzsEncryptionKeyBase64](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64).

![Azure Stack hub – použití šifrovacího klíče v režimu zpětné kompatibility](media/azure-stack-backup/encryption-settings-backcompat-encryption-key.png)

> [!Note]  
> Aktualizace z šifrovacího klíče na certifikát je jednosměrná operace. Po provedení této změny nebudete moct přepnout zpátky na šifrovací klíč. Všechna stávající zálohování zůstanou zašifrovaná pomocí předchozího šifrovacího klíče.

![Azure Stack hub – použít šifrovací certifikát v režimu zpětné kompatibility](media/azure-stack-backup/encryption-settings-backcompat-certificate.png)

## <a name="next-steps"></a>Další kroky

Naučte se spustit zálohování. Viz [zálohování centra Azure Stack](azure-stack-backup-back-up-azure-stack.md).

Zjistěte, jak ověřit, jestli se zálohování spustilo. Viz [potvrzení zálohování dokončeno na portálu pro správu](azure-stack-backup-back-up-azure-stack.md).
