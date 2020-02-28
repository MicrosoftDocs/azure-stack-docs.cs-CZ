---
title: Zpráva k vydání verze Azure Stack centra aktualizace 1 App Service
description: Přečtěte si o vylepšeních, opravách a známých problémech v aktualizaci 1 pro App Service v centru Azure Stack.
author: bryanla
manager: stefsch
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: 44baf18b4e7b1acb39125a4ccdeb4228557de4d4
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703515"
---
# <a name="app-service-on-azure-stack-hub-update-1-release-notes"></a>Zpráva k vydání verze Azure Stack centra aktualizace 1 App Service

Tyto poznámky k verzi popisují vylepšení, opravy a známé problémy v Azure App Service v Azure Stack centra aktualizace 1. Známé problémy jsou rozdělené na tři části: problémy přímo související s nasazením, problémy s procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Před nasazením Azure App Service použijte aktualizaci 1802 pro integrovaný systém Azure Stack hub nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

## <a name="build-reference"></a>Referenční informace o buildu

App Service číslo buildu Azure Stack centra aktualizace 1 se **69.0.13698.9**.

### <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Nová nasazení Azure App Service v centru Azure Stack nyní vyžadují [certifikát se zástupným znakem tři subjektu](azure-stack-app-service-before-you-get-started.md#get-certificates) z důvodu vylepšení způsobu, jakým se v Azure App Service zpracovává jednotné přihlašování pro Kudu. Nový předmět je **\*. SSO. AppService.\<region\>.\<domainname\>.\<rozšíření\>**

Než začnete s nasazením, přečtěte si [požadavky pro nasazení App Service v centru Azure Stack](azure-stack-app-service-before-you-get-started.md) .

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service v centru Azure Stack aktualizace 1 obsahuje následující vylepšení a opravy:

- **Vysoká dostupnost Azure App Service** – úlohy Azure Stack centra 1802 s podporou aktualizace mají být nasazené napříč doménami selhání, což umožňuje, aby byla App Serviceá infrastruktura odolná proti chybám při nasazení napříč doménami selhání. Tato funkce má ve výchozím nastavení všechna nová nasazení Azure App Service. Pro nasazení dokončené před aktualizací Azure Stack centra 1802 se ale podívejte do [dokumentace k doméně selhání App Service](azure-stack-app-service-before-you-get-started.md).

- **Nasazení ve stávající virtuální síti** – zákazníci teď můžou nasazovat App Service v centru Azure Stack v existující virtuální síti. Nasazení ve stávající virtuální síti umožňuje zákazníkům připojit se k SQL Server a souborového serveru, který je vyžadován pro Azure App Service, prostřednictvím privátních portů. Během nasazování můžou zákazníci vybrat možnost nasazení v existující virtuální síti, ale [musí vytvořit podsítě pro použití App Service](azure-stack-app-service-before-you-get-started.md#virtual-network) před nasazením.

- Aktualizace pro **App Service klientů, správců, funkcí portálů a nástrojů Kudu** Konzistentní s verzí sady SDK portálu Azure Stack hub.

- Aktualizuje **Azure Functions runtime** na **v 1.0.11388**.

- **Aktualizace následujících aplikačních architektur a nástrojů**:
    - Přidala se podpora **.NET Core 2,0** .
    - Přidané verze **Node. js** :
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - Přidané verze **npm** :
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Přidané aktualizace **php** :
        - 5.6.32
        - 7.0.26 (x86 a x64)
        - 7.1.12 (x86 a x64)
    - Aktualizace **Gitu pro Windows** na v 2.14.1
    - Aktualizace **Mercurial** na verzi v 4.5.0

  - Přidání podpory funkce **pouze protokolu HTTPS** v rámci vlastní domény na portálu App Service User Portal.

  - Přidání ověření připojení úložiště v nástroji pro výběr vlastního úložiště pro Azure Functions.

#### <a name="fixes"></a>Opravy

- Při vytváření offline balíčku pro nasazení se uživatelům už při otevření složky z instalačního programu App Service neobdrží chybová zpráva o odepření přístupu.

- Vyřešené problémy při práci s funkcí vlastní domény na portálu App Service User Portal.

- Zabránit zákazníkům v používání rezervovaných názvů správců během instalace.

- Povoleno nasazení App Service se souborovým serverem **připojeným k doméně** .

- Vylepšené načítání kořenového certifikátu centra Azure Stack ve skriptu a přidání možnosti ověřit kořenový certifikát v instalačním programu App Service

- Opravil se nesprávný stav Azure Resource Manager, když se odstraní odběr, který obsahuje prostředky v oboru názvů Microsoft. Web.

### <a name="known-issues-with-the-deployment-process"></a>Známé problémy s procesem nasazení

- Chyby ověření certifikátu.

    Někteří zákazníci narazili na problémy při poskytování certifikátů instalačnímu programu App Service při nasazení v integrovaném systému z důvodu překročení omezení ověření v instalačním programu. Instalační program App Service byl znovu uvolněn a zákazníci by si měli [stáhnout aktualizovaný instalační program](https://aka.ms/appsvconmasinstaller). Pokud budete mít i nadále problémy s ověřováním certifikátů pomocí aktualizovaného instalačního programu, obraťte se na podporu.

- Při načítání kořenového certifikátu centra Azure Stack z integrovaného systému došlo k potížím.

    Chyba v Get-AzureStackRootCert. ps1 způsobila, že zákazníci při spouštění skriptu na počítači, ve kterém není nainstalován kořenový certifikát, nepodaří načíst kořenový certifikát centra Azure Stack. Nyní byl skript znovu vydán, což vyřeší tento problém. [Aktualizované pomocné skripty si můžete stáhnout tady](https://aka.ms/appsvconmashelpers). Pokud budete pokračovat v práci s načtením kořenového certifikátu s aktualizovaným skriptem, obraťte se na podporu.

### <a name="known-issues-with-the-update-process"></a>Známé problémy s proces aktualizace

- Neexistují žádné známé problémy pro aktualizaci Azure App Service v centru Azure Stack Update 1.

### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

- Swap slotu nefunguje.

V této verzi je prohození slotu webu přerušeno. Chcete-li obnovit funkčnost, proveďte následující kroky:

1. Upravte skupinu zabezpečení sítě ControllersNSG tak, aby **povolovala** připojení vzdálené plochy k instancím kontroleru App Service. Nahraďte AppService. Local názvem skupiny prostředků, kterou jste nasadili App Service.

    ```powershell
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

2. Přejděte na **CN0-VM** pod Virtual Machines na portálu Azure Stack správce centra a **kliknutím na připojit** otevřete relaci vzdálené plochy s instancí řadiče. Použijte přihlašovací údaje zadané při nasazení App Service.
3. Spusťte **PowerShell jako správce** a spusťte následující skript:

    ```powershell
        Import-Module appservice

        $sm = new-object Microsoft.Web.Hosting.SiteManager

        if($sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$true)
        {
          $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$false
        #  'Slot swap mode reverted'
        }
        
        # Confirm new setting is false
        $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus
        
        # Commit Changes
        $sm.CommitChanges()

        Get-AppServiceServer -ServerType ManagementServer | ForEach-Object Repair-AppServiceServer
        
    ```

4. Zavřete relaci vzdálené plochy.
5. Vraťte skupinu zabezpečení sítě ControllersNSG, aby **odmítala** připojení vzdálené plochy k instancím kontroleru App Service. Nahraďte AppService. Local názvem skupiny prostředků, kterou jste nasadili App Service.

    ```powershell

        Add-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

6. Pokud je App Service nasazená ve stávající virtuální síti a souborový server je k dispozici jenom v privátní síti, zaměstnanci nemůžou kontaktovat souborový server.

Pokud se rozhodnete nasadit do existující virtuální sítě a interní IP adresu pro připojení k souborovému serveru, musíte přidat odchozí pravidlo zabezpečení, které umožňuje provoz protokolu SMB mezi podsítí pracovních procesů a souborovým serverem. Na portálu pro správu přejdete na WorkersNsg a přidáte odchozí pravidlo zabezpečení s následujícími vlastnostmi:

- Zdroj: Any
- Rozsah zdrojových portů: *
- Cíl: IP adresy
- Rozsah cílových IP adres: rozsah IP adres pro souborový server
- Rozsah cílových portů: 445
- Protocol: TCP
- Akce: povolení
- Priorita: 700
- Název: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Známé problémy pro Cloud Admins, které pracují Azure App Service v centru Azure Stack

Informace najdete v dokumentaci k [verzi centra Azure Stack 1802](azure-stack-update-1903.md) .

## <a name="next-steps"></a>Další kroky

- Přehled Azure App Service najdete v tématu [Azure App Service v tématu Přehled centra Azure Stack](azure-stack-app-service-overview.md).
- Další informace o přípravě nasazení App Service v centru Azure Stack najdete v tématu [předpoklady pro nasazení App Service na Azure Stack hub](azure-stack-app-service-before-you-get-started.md).
