---
title: Instalace ASDK
description: Přečtěte si, jak nainstalovat Azure Stack Development Kit (ASDK).
author: justinha
ms.topic: article
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: b85279aff5427e0f3dbdc15b979a00a41db43e57
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77695848"
---
# <a name="install-the-asdk"></a>Instalace ASDK
Po [přípravě hostitelského počítače s ASDK](asdk-prepare-host.md)se dá Azure Stack Development Kit (ASDK) nasadit do image CloudBuilder. vhdx pomocí následujících kroků v tomto článku.

## <a name="install-the-asdk"></a>Instalace ASDK
Kroky v tomto článku vám ukážou, jak nasadit ASDK pomocí grafického uživatelského rozhraní (GUI) poskytovaného stažením a spuštěním skriptu PowerShellu **asdk-Installer. ps1** .

> [!NOTE]
> Uživatelské rozhraní instalačního programu pro ASDK je open source skript založený na WCF a PowerShellu.


1. Po úspěšném spuštění hostitelského počítače do image CloudBuilder. vhdx se přihlaste pomocí přihlašovacích údajů správce zadaných při [přípravě hostitelského počítače ASDK](asdk-prepare-host.md) pro instalaci ASDK. To by mělo být stejné jako přihlašovací údaje místního správce hostitele ASDK.
2. Otevřete konzolu nástroje PowerShell se zvýšenými oprávněními a spusťte **&lt;písmeno jednotky > \ AzureStack_Installer \asdk-Installer.ps1** skriptu PowerShellu. Uvědomte si, že skript může být na jiné jednotce než C:\. v imagi CloudBuilder. vhdx. Klikněte na **Nainstalovat**.

    ![Nainstalovat ASDK](media/asdk-install/1.PNG) 

3. V rozevíracím seznamu **typ** zprostředkovatele identity vyberte **Azure Čína Cloud**, **Azure usa pro státní**správu, **AD FS**nebo **cloud Azure**. V části **heslo místního správce** zadejte heslo místního správce (které se musí shodovat s aktuálním nakonfigurovaným heslem místního správce) v poli **heslo** a potom klikněte na tlačítko **Další**.

    ![Rozevírací seznam typu zprostředkovatele identity v ASDK](media/asdk-install/2.PNG) 
  
    Pokud zvolíte poskytovatele identity předplatného Azure, budete potřebovat připojení k Internetu, úplné jméno tenanta Azure AD ve formátu *DomainName*. onmicrosoft.com nebo ověřený název vlastní domény Azure AD. Pro zadaný adresář budete také potřebovat přihlašovací údaje globálního správce.

    Po nasazení se oprávnění globálního správce služby Azure Active Directory (Azure AD) nevyžadují. Některé operace ale můžou vyžadovat přihlašovací údaje globálního správce. Například skript instalačního programu poskytovatele prostředků nebo nová funkce vyžadující udělené oprávnění. Můžete buď dočasně obnovit oprávnění globálního správce účtu, nebo použít samostatný účet globálního správce, který je vlastníkem *výchozího předplatného poskytovatele*.

    Při použití AD FS jako poskytovatele identity se použije výchozí adresářová služba razítka. Výchozí účet, pomocí kterého se přihlašujete, je azurestackadmin@azurestack.locala heslo, které se má použít, je ten, který jste zadali jako součást instalace.

   > [!NOTE]
   > Pro dosažení co nejlepších výsledků, a to i v případě, že chcete jako poskytovatele identity použít odpojené Azure Stack prostředí AD FS, je nejlepší nainstalovat ASDK při připojení k Internetu. Tímto způsobem můžete v době nasazení aktivovat zkušební verzi Windows serveru 2016, která je součástí instalace ASDK.

4. Vyberte síťový adaptér, který se má použít pro ASDK, a pak klikněte na **Další**.

    ![Vyberte síťový adaptér pro ASDK.](media/asdk-install/3.PNG)

5. Na stránce **Konfigurace sítě** zadejte platnou **časovou IP adresu serveru** . Toto povinné pole nastaví časový server, který má ASDK používat. Tento parametr se musí zadat jako platná časová IP adresa serveru. Názvy serverů nejsou podporované.

      > [!TIP]
      > Pokud chcete najít IP adresu časového serveru, navštivte [ntppool.org](https://www.ntppool.org/) nebo otestujte Time.Windows.com. 

    **Volitelně**můžete zadat IP adresu **serveru DNS pro přeposílání** . Server DNS se vytvoří jako součást nasazení Azure Stack. Pokud chcete počítačům v řešení umožnit překlad názvů mimo razítko, zadejte svůj stávající server DNS infrastruktury. Server DNS v rámci razítka přepošle neznámé požadavky na překlad názvů na tento server.

    ![Služba DNS pro přeposílání a konfiguraci sítě v ASDK](media/asdk-install/4.PNG)

6. Na stránce **ověřování vlastností síťové karty** se zobrazí indikátor průběhu. Po dokončení ověřování klikněte na **Další**.

    ![Ověřování vlastností síťového rozhraní v ASDK](media/asdk-install/5.PNG)

7. Na stránce **Souhrn** kliknutím na **nasadit** spusťte instalaci ASDK na hostitelském počítači ASDK.

    ![Shrnutí skriptu před nasazením v ASDK](media/asdk-install/6.PNG)

    > [!TIP]
    > Tady můžete také zkopírovat příkazy pro instalaci PowerShellu, které se použijí k instalaci ASDK. To je užitečné, pokud někdy potřebujete [znovu nasadit ASDK v hostitelském počítači pomocí prostředí PowerShell](asdk-deploy-powershell.md).

8. Pokud provádíte nasazení služby Azure AD, zobrazí se výzva k zadání přihlašovacích údajů globálního správce služby Azure AD pár minut po spuštění instalačního programu.

9. Proces nasazení bude trvat několik hodin, během kterých se hostitelský počítač automaticky restartuje. Pokud chcete monitorovat průběh nasazení, přihlaste se po restartování hostitele ASDK jako azurestack\AzureStackAdmin. Po úspěšném nasazení se v konzole PowerShellu zobrazí: **dokončeno: akce nasazení**. 
    > [!IMPORTANT]
    > Pokud se přihlásíte jako místní správce poté, co je počítač připojený k doméně azurestack, neuvidíte průběh nasazení. Neprovádějte znovu nasazení, místo toho se přihlaste jako azurestack\AzureStackAdmin, abyste ověřili, že je spuštěný.

    ![ASDK nasazení bylo úspěšné.](media/asdk-install/7.PNG)

Gratulujeme, úspěšně jste nainstalovali ASDK.

Pokud se nasazení z nějakého důvodu nepovede, můžete ho znovu [nasadit](asdk-redeploy.md) od začátku nebo pomocí následujících příkazů PowerShellu restartovat nasazení z posledního úspěšného kroku. Příkazy lze použít ze stejného okna prostředí PowerShell se zvýšenými oprávněními:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>Další kroky
[Konfigurace po nasazení](asdk-post-deploy.md)
