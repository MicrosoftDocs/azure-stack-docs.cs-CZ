---
title: Povolit zálohování pro Azure Stack z portálu pro správu | Microsoft Docs
description: Naučte se, jak povolit službu Infrastructure Backup z portálu pro správu, aby bylo možné Azure Stack obnovit v případě selhání.
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
ms.date: 12/16/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 12/16/2019
ms.openlocfilehash: 3864183ecda856500db1fcbfe38df84d70f3bae9
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93329203"
---
# <a name="configure-backup-for-azure-stack-hub-from-the-administrator-portal"></a>Konfigurace zálohování pro centrum Azure Stack z portálu pro správu

*Platí pro: modulární datové centrum, Azure Stack centrum je robustní*

Službu Infrastructure Backup můžete nakonfigurovat z portálu pro správu a exportovat tak zálohy infrastruktury do umístění externího úložiště. Zálohy infrastruktury je možné využívat prostřednictvím podpory k nápravě degradované služby.

## <a name="configure-backup"></a>Konfigurace zálohování

1. Otevřete [portál Azure Stack správce](../../operator/azure-stack-manage-portals.md).

2. Vyberte **všechny služby** a potom v kategorii **Správa** vyberte **zálohování infrastruktury**. V okně **zálohování infrastruktury** vyberte **Konfigurace** .

3. Zadejte cestu k **umístění úložiště zálohy**. Použijte řetězec UNC (Universal Naming Convention) pro cestu ke sdílené složce hostované na samostatném zařízení. Řetězec UNC určuje umístění prostředků, jako jsou třeba sdílené soubory nebo zařízení. Pro službu můžete použít IP adresu. Aby se zajistila dostupnost zálohovaných dat po havárii, mělo by být zařízení v samostatném umístění.

    > [!NOTE]  
    > Pokud vaše prostředí podporuje překlad IP adres z Azure Stack sítě infrastruktury do vašeho podnikového prostředí, můžete místo IP adresy použít plně kvalifikovaný název domény (FQDN).

4. Zadejte **uživatelské jméno** s použitím domény a uživatelského jména s dostatečným přístupem ke čtení a zápisu souborů. například **Contoso\backupshareuser**.

5. Zadejte **heslo** pro uživatele.

6. Zadejte heslo znovu a **potvrďte heslo**.

7. **Frekvence v hodinách** určuje, jak často se vytvářejí zálohy. Výchozí hodnota je 12. Plánovač podporuje minimálně 4 a maximálně 12.

8. **Doba uchování ve dnech** určuje, kolik dní zálohování se uchová na externím umístění. Výchozí hodnota je 7. Scheduler podporuje minimálně 2 a maximálně 14. Zálohy starší než doba uchování jsou automaticky odstraněny z externího umístění.

   > [!NOTE]
   > Pokud chcete archivovat zálohy starší než doba uchování, nezapomeňte zálohovat soubory předtím, než Plánovač odstraní zálohy. Pokud omezíte dobu uchovávání záloh (například ze 7 dnů na 5 dní), Plánovač odstraní všechny zálohy starší než nová doba uchování. Před aktualizací této hodnoty se ujistěte, že jste v pořádku, jak se zálohují.

9. V **nastavení šifrování** je kryptografický otisk veřejného certifikátu pro certifikát poskytnutý během nasazování. Není potřeba aktualizovat existující certifikát.

10. Výběrem **OK** uložte nastavení zálohovacího řadiče.

    ![Azure Stack – nastavení kontroleru zálohování](media/azure-stack-backup-enable-backup-console-tzl/backup-controller-settings-certificate.png)

## <a name="enable-or-disable-automatic-backups"></a>Povolit nebo zakázat automatické zálohování

Zálohy jsou během nasazení automaticky povolené. Po nakonfigurování umístění úložiště budou automatické zálohy exportovat zálohy do externího úložiště podle plánu, a to na základě nastavení četnosti. Další čas plánovaného zálohování můžete zjistit v okně **základy** .

![Zálohování na vyžádání Azure Stack](media/azure-stack-backup-enable-backup-console-tzl/on-demand-backup.png)

Pokud potřebujete zakázat budoucí naplánovaná zálohování, vyberte možnost **Zakázat automatické zálohování**. Zakázání automatického zálohování udržuje nastavení zálohování nakonfigurované a zachovává plán zálohování. Tato akce instruuje Scheduler, aby vynechal budoucí zálohy.

![Azure Stack – zakázat plánované zálohy](media/azure-stack-backup-enable-backup-console-tzl/disable-auto-backup.png)

Potvrďte, že v **Essentials** jsou budoucí naplánovaná zálohování zakázaná:

![Azure Stack – potvrzení zálohování bylo zakázáno.](media/azure-stack-backup-enable-backup-console-tzl/confirm-disable.png)

Vyberte možnost **Povolit automatické zálohování** a informování plánovače o zahájení budoucích záloh v naplánovaném čase.

![Azure Stack – povolení naplánovaných záloh](media/azure-stack-backup-enable-backup-console-tzl/enable-auto-backup.png)

## <a name="update-backup-settings"></a>Aktualizovat nastavení zálohování

Pokud chcete aktualizovat certifikát použitý k šifrování zálohovaných dat, nahrajte nový. Soubor CER s částí veřejného klíče a kliknutím na tlačítko OK uložte nastavení.

Nové zálohy začnou používat veřejný klíč v novém certifikátu. Neexistuje žádný vliv na všechny existující zálohy vytvořené pomocí předchozího certifikátu. Nezapomeňte zachovat starší certifikát v zabezpečeném umístění pro případ, že ho budete potřebovat k obnovení cloudu.

![Azure Stack – zobrazení kryptografického otisku certifikátu](media/azure-stack-backup-enable-backup-console-tzl/encryption-settings-thumbprint.png)

## <a name="next-steps"></a>Další kroky

Pokud chcete ověřit, že se zálohování spustilo, přečtěte si téma [potvrzení zálohování dokončeno na portálu pro správu](../../operator/azure-stack-backup-back-up-azure-stack.md).
