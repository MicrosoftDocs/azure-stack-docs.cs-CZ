---
title: Aktualizovat antivirovou ochranu v programu Windows Defender
titleSuffix: Azure Stack Hub
description: Naučte se aktualizovat antivirovou ochranu v programu Windows Defender v centru Azure Stack.
author: justinha
ms.topic: article
ms.date: 12/04/2019
ms.author: justinha
ms.reviewer: fiseraci
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 8f0ad292f8d9772c53c332d2cad7af8bd606594a
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77697735"
---
# <a name="update-windows-defender-antivirus-on-azure-stack-hub"></a>Aktualizace antivirové ochrany v programu Windows Defender v centru Azure Stack

[Antivirová ochrana v programu Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) je antimalwarové řešení, které zajišťuje zabezpečení a antivirovou ochranu. Každá součást infrastruktury centra Azure Stack (hostitelé a virtuální počítače Hyper-V) je chráněná pomocí antivirové ochrany v programu Windows Defender. V případě aktuálnosti ochrany budete potřebovat pravidelné aktualizace definic, strojů a platforem v programu Windows Defender. Způsob použití aktualizací závisí na konfiguraci.

## <a name="connected-scenario"></a>Připojený scénář

[Poskytovatel prostředků aktualizace](azure-stack-updates.md#the-update-resource-provider) centra Azure Stack stahuje aktualizace antimalwaru a stroje několikrát denně. Každá součást infrastruktury centra Azure Stack získá aktualizaci od poskytovatele prostředků aktualizace a automaticky použije aktualizaci.

Pro ta Azure Stack nasazení centra, která jsou připojená k veřejnému Internetu, použijte [měsíční aktualizaci centra Azure Stack](azure-stack-apply-updates.md). Měsíční aktualizace centra Azure Stack obsahuje aktualizace antivirové ochrany v programu Windows Defender pro daný měsíc.

## <a name="disconnected-scenario"></a>Odpojený scénář

U těchto Azure Stackch nasazení centra, která nejsou připojená k veřejnému Internetu (například datacentra air gapped), počínaje verzí 1910 můžou zákazníci při publikování použít antimalwarové definice a aktualizace stroje. 

Chcete-li aktualizace použít pro řešení centra Azure Stack, je nutné je nejprve stáhnout z webu společnosti Microsoft (odkazy níže) a následně je importovat do kontejneru objektů BLOB úložiště v rámci *updateadminaccount*. Naplánovaná úloha prohledává kontejner objektů BLOB každých 30 minut a v případě, že se najde nové definice a aktualizace modulu Defenderu, aplikují se na infrastrukturu centra Azure Stack. 

U těch odpojených nasazení, která ještě nejsou v 1910 nebo novějších nebo které nemají možnost stahovat definice Defenderu a aktualizace motoru každý den, zahrnuje měsíční aktualizace centra Azure Stack definice antivirové ochrany v programu Windows Defender, modul a aktualizace platformy v měsíci. 


### <a name="set-up-windows-defender-for-manual-updates"></a>Nastavení programu Windows Defender pro ruční aktualizace 

S vydáním verze 1910 byly do privilegovaného koncového bodu přidány dvě nové rutiny pro konfiguraci ruční aktualizace programu Windows Defender v Azure Stackovém centru. 

```powershell 
### cmdlet to configure the storage blob container for the Defender updates 
Set-AzsDefenderManualUpdate [-Container <string>] [-Remove]  
### cmdlet to retrieve the configuration of the Defender manual update settings 
Get-AzsDefenderManualUpdate  
``` 

Následující postup ukazuje, jak nastavit ruční aktualizaci v programu Windows Defender. 

1. Připojte se k privilegovanému koncovému bodu a spuštěním následující rutiny zadejte název kontejneru objektů BLOB úložiště, kam se budou nahrávat aktualizace v Defenderu. 

   > [!NOTE] 
   > Postup ruční aktualizace popsaný níže funguje jenom v odpojených prostředích, kde není povolený přístup k go.microsoft.com. Pokus o spuštění rutiny Set-AzsDefenderManualUpdate v připojených prostředích způsobí chybu. 

   ```powershell 
   ### Configure the storage blob container for the Defender updates 
   Set-AzsDefenderManualUpdate -Container <yourContainerName>
   ``` 

2. Stáhněte si dva balíčky aktualizací v programu Windows Defender a uložte je do umístění, které je dostupné na portálu pro správu centra Azure Stack.  

   * Mpam-FE. exe z [https://go.microsoft.com/fwlink/?LinkId=121721&arch=x64](https://go.microsoft.com/fwlink/?LinkId=121721&arch=x64) 
   * nis_full. exe z [https://go.microsoft.com/fwlink/?LinkId=197094](https://go.microsoft.com/fwlink/?LinkId=197094) 

   > [!NOTE] 
   > Tyto dva soubory budete muset stáhnout **pokaždé** , když chcete aktualizovat signatury Defenderu. 

3. Na portálu pro správu vyberte **všechny služby**. Pak v části kategorie **úložiště dat + úložiště** vyberte **účty úložiště**. (Nebo do pole Filtr začněte psát **účty úložiště**a vyberte ho.) 

   ![Centrum Azure Stack Defender – všechny služby](./media/azure-stack-security-av/image1.png)  

4. Do pole Filtr zadejte Update ( **aktualizovat**) a vyberte účet úložiště **updateadminaccount** . 

5. V podrobnostech účtu úložiště v části **služby**vyberte **objekty blob**. 

   ![Centrum Azure Stack Defender – BLOB](./media/azure-stack-security-av/image2.png) 

6. V části **BLOB Service**vyberte **+ kontejner** a vytvořte kontejner. Zadejte název, který byl zadán pomocí Set-AzsDefenderManualUpdate (v tomto příkladu *defenderupdates*) a pak vyberte **OK**. 

   ![Centrum Azure Stack Defender – kontejner](./media/azure-stack-security-av/image3.png) 

7. Po vytvoření kontejneru klikněte na název kontejneru a potom klikněte na tlačítko **nahrát** a nahrajte soubory balíčku do kontejneru. 

   ![Centrum Azure Stack Defender – nahrání](./media/azure-stack-security-av/image4.png) 

8. V části **nahrát objekt BLOB**klikněte na ikonu složky, vyhledejte soubory aktualizace *Mpam-FE. exe* v programu Windows Defender a pak klikněte na **otevřít** v okně Průzkumník souborů. 

9. V části **nahrát objekt BLOB**klikněte na **nahrát**. 

   ![Centrum Azure Stack Defender – nahrání blob1](./media/azure-stack-security-av/image5.png) 

1. Opakujte kroky 8 a 9 pro soubor *nis_full. exe* . 

   ![Centrum Azure Stack Defender – nahrání blob2](./media/azure-stack-security-av/image6.png)

Naplánovaná úloha vyhledá kontejner objektů BLOB každých 30 minut a použije všechny nové balíčky Windows Defenderu.  

## <a name="next-steps"></a>Další kroky

[Další informace o zabezpečení centra Azure Stack](azure-stack-security-foundations.md)
