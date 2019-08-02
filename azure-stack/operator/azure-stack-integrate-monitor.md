---
title: Integrace řešení pro externí monitorování s Azure Stack | Microsoft Docs
description: Naučte se integrovat Azure Stack s externím řešením monitorování ve vašem datovém centru.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 06/05/2019
ms.author: jeffgilb
ms.reviewer: thoroet
ms.lastreviewed: 06/05/2019
ms.openlocfilehash: 7b5bfb39c3ec14c23b1df54c13f2733724fcfe05
ms.sourcegitcommit: ddb625bb01de11bfb75d9f7a1cc61d5814b3bc31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68712907"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integrace řešení pro externí monitorování pomocí Azure Stack

Pro externí monitorování infrastruktury Azure Stack musíte monitorovat software Azure Stack, fyzické počítače a fyzické síťové přepínače. Každá z těchto oblastí nabízí metodu pro načtení informací o stavu a upozornění:

- Azure Stack software nabízí rozhraní API založené na REST, které načte stav a výstrahy. Používání softwarově definovaných technologií, jako je Prostory úložiště s přímým přístupem, stav úložiště a výstrahy, jsou součástí monitorování softwaru.
- Fyzické počítače mohou zpřístupnit informace o stavu a výstrahách prostřednictvím řadičů pro správu základní desky (BMC).
- Zařízení fyzické sítě mohou zpřístupnit informace o stavu a upozornění prostřednictvím protokolu SNMP.

Každé Azure Stack řešení se dodává s hostitelem životního cyklu hardwaru. Tento hostitel spustí monitorovací software výrobce OEM (Original Equipment Manufacturer) dodavatele hardwaru pro fyzické servery a síťová zařízení. Pokud jejich řešení pro monitorování můžete integrovat se stávajícími řešeními monitorování ve vašem datovém centru, obraťte se na poskytovatele OEM.

> [!IMPORTANT]
> Externí řešení monitorování, které používáte, musí být bez agenta. Do komponent Azure Stack nemůžete instalovat agenty třetích stran.

Následující diagram znázorňuje tok přenosů mezi Azure Stack integrovaným systémem, hostitelem životního cyklu hardwaru, externím řešením monitorování a externím systémem pro vytváření lístků nebo sběru dat.

![Diagram znázorňující provoz mezi Azure Stack, monitorováním a lístkem řešení.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

> [!NOTE]
> Integrace externích monitorování na fyzických serverech není povolená a aktivně blokována seznamy Access Control (ACL).  Externí integrace monitorování se podporuje přímo s fyzickými síťovými zařízeními. Obraťte se prosím na svého poskytovatele OEM, abyste tuto funkci povolili.

Tento článek vysvětluje, jak integrovat Azure Stack s externími monitorovacími řešeními, jako jsou System Center Operations Manager a Nagios. Obsahuje také informace o tom, jak pracovat s výstrahami programově pomocí prostředí PowerShell nebo prostřednictvím REST API volání.

## <a name="integrate-with-operations-manager"></a>Integrace s Operations Manager

Operations Manager můžete použít pro externí monitorování Azure Stack. Sada System Center Management Pack pro Microsoft Azure Stack umožňuje monitorovat více nasazení Azure Stack s jednou instancí Operations Manager. Management Pack využívá poskytovatele prostředků stavu a aktualizuje rozhraní REST API poskytovatele prostředků ke komunikaci s Azure Stack. Pokud se chystáte obejít monitorovací software OEM, který běží na hostiteli životního cyklu hardwaru, můžete nainstalovat sady Management Pack dodavatele a monitorovat fyzické servery. K monitorování síťových přepínačů můžete použít taky Operations Manager zjišťování síťových zařízení.

Management Pack pro Azure Stack poskytuje následující možnosti:

- Můžete spravovat víc nasazení Azure Stack.
- Podporuje se Azure Active Directory (Azure AD) a Active Directory Federation Services (AD FS) (AD FS).
- Výstrahy můžete načíst a zavřít.
- Na řídicím panelu pro stav a kapacitu
- Zahrnuje detekci režimu automatické údržby pro dobu, kdy probíhá oprava a aktualizace (P & U).
- Zahrnuje vynucené úlohy aktualizace pro nasazení a oblast.
- Do oblasti můžete přidat vlastní informace.
- Podporuje oznamování a vytváření sestav

Můžete si stáhnout sadu System Center Management Pack pro Microsoft Azure Stack a přidruženou [uživatelskou příručku](https://www.microsoft.com/en-us/download/details.aspx?id=55184)nebo přímo z Operations Manager.

Pro řešení lístků můžete Operations Manager integrovat s System Center Service Manager. Integrovaný konektor produktů umožňuje obousměrnou komunikaci, která umožňuje Zavřít výstrahu v Azure Stack a Operations Manager po vyřešení žádosti o službu v Service Manager.

Následující diagram znázorňuje integraci Azure Stack s existujícím nasazením nástroje System Center. Pomocí nástroje System Center Orchestrator nebo Service Management Automation (SMA) můžete automatizovat Service Manager operací v Azure Stack.

![Diagram znázorňující integraci s OM, Service Manager a SMA](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integrace s Nagios

Můžete nastavit a nakonfigurovat modul plug-in Nagios pro Microsoft Azure Stack.

Modul plug-in Nagios monitoring byl vyvinut společně s partnerskými řešeními Cloudbase, které je k dispozici v rámci opravňující bezplatné softwarové licence – MIT (Massachusetts Institute of Technology).

Modul plug-in je napsaný v Pythonu a využívá poskytovatele prostředků stavu REST API. Nabízí základní funkce pro načítání a zavírání výstrah v Azure Stack. Podobně jako v rámci nástroje System Center Management Pack umožňuje přidat více nasazení Azure Stack a odesílat oznámení.

S verzí 1,2 Azure Stack modul plug-in Nagios využívá knihovnu Microsoft ADAL Library a podporuje ověřování pomocí instančního objektu s tajným kódem nebo certifikátem. Konfigurace byla také zjednodušená pomocí jednoho konfiguračního souboru s novými parametry. Nyní podporuje Azure Stack nasazení pomocí služby AAD & služby AD FS jako systému identit.

Modul plug-in funguje s Nagios 4x a XI. Můžete si ho stáhnout [tady](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Lokalita ke stažení obsahuje také podrobnosti o instalaci a konfiguraci.

### <a name="requirements-for-nagios"></a>Požadavky na Nagios

1.  Minimální verze Nagios je 4. x

2.  Microsoft Azure Active Directory knihovna Pythonu. Tato možnost se dá nainstalovat pomocí PIP Pythonu.

    ```bash  
    sudo pip install adal pyyaml six
    ```

### <a name="install-plugin"></a>Nainstalovat modul plug-in

Tato část popisuje, jak nainstalovat modul plug-in Azure Stack za předpokladu, že se jedná o výchozí instalaci systému Nagios.

Balíček modulu plug-in obsahuje následující soubory:

```
azurestack_plugin.py
azurestack_handler.sh
samples/etc/azurestack.cfg
samples/etc/azurestack_commands.cfg
samples/etc/azurestack_contacts.cfg
samples/etc/azurestack_hosts.cfg
samples/etc/azurestack_services.cfg
```

1.  Zkopírujte modul plug `azurestack_plugin.py` -in do následujícího `/usr/local/nagios/libexec`adresáře.

2.  Zkopírujte obslužnou rutinu `azurestack_handler.sh` do následujícího adresáře `/usr/local/nagios/libexec/eventhandlers`.

3.  Nastavit soubor modulu plug-in jako spustitelný

    ```bash
    sudo cp azurestack_plugin.py <PLUGINS_DIR>
    sudo chmod +x <PLUGINS_DIR>/azurestack_plugin.py
    ```

### <a name="configure-plugin"></a>Konfigurace modulu plug-in

Následující parametry jsou k dispozici pro konfiguraci v souboru azurestack. cfg. Parametry tučného písma je nutné nakonfigurovat nezávisle na zvoleném modelu ověřování.

Podrobné informace o tom, jak vytvořit hlavní název služby (SPN), [najdete tady](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals).

| Parametr | Popis | Ověřování |
| --- | --- | --- |
| **External_domain_fqdn ** | Plně kvalifikovaný název domény externí domény |    |
| **region: ** | Název oblasti |    |
| **tenant_id: ** | ID tenanta\* |    |
| client_id: | ID klienta | SPN s tajným klíčem |
| client_secret: | Heslo klienta | SPN s tajným klíčem |
| client_cert\*\*: | Cesta k certifikátu | Hlavní název služby s certifikátem |
| client_cert_thumbprint\*\*: | Kryptografický otisk certifikátu | Hlavní název služby s certifikátem |

\*Pro nasazení Azure Stack se službou AD FS se nevyžaduje ID tenanta.

\*\*Tajný klíč klienta a klientský certifikát se vzájemně vylučují.

Ostatní konfigurační soubory obsahují volitelná nastavení konfigurace, která je možné nakonfigurovat i v Nagios.

> [!Note]  
> Ověřte cíl umístění v azurestack_hosts. cfg a azurestack_services. cfg.

| Konfiguraci | Popis |
| --- | --- |
| azurestack_commands.cfg | Konfigurace obslužné rutiny beze změn – požadavek |
| azurestack_contacts.cfg | Nastavení oznámení |
| azurestack_hosts.cfg | Azure Stack pojmenovávání nasazení |
| azurestack_services.cfg | Konfigurace služby |

### <a name="setup-steps"></a>Kroky pro instalaci

1.  Úprava konfiguračního souboru

2.  Zkopírujte upravené konfigurační soubory do následující složky `/usr/local/nagios/etc/objects`.

### <a name="update-nagios-configuration"></a>Aktualizovat konfiguraci Nagios

Konfigurace Nagios se musí aktualizovat, aby se zajistilo, že se načte modul plug-in Azure Stack – Nagios.

1.  Otevřete následující soubor

```bash  
/usr/local/nagios/etc/nagios.cfg
```

2.  Přidat následující položku

```bash  
# Load the Azure Stack Plugin Configuration
cfg_file=/usr/local/Nagios/etc/objects/azurestack_contacts.cfg
cfg_file=/usr/local/Nagios/etc/objects/azurestack_commands.cfg
cfg_file=/usr/local/Nagios/etc/objects/azurestack_hosts.cfg
cfg_file=/usr/local/Nagios/etc/objects/azurestack_services.cfg
```

3.  Znovu načíst Nagios

```bash  
sudo service nagios reload
```

### <a name="manually-close-active-alerts"></a>Ručně zavřít aktivní výstrahy

Aktivní výstrahy lze v rámci Nagios uzavřít pomocí funkce vlastní oznámení. Vlastní oznámení musí být:

```
/close-alert <ALERT_GUID>
```

Výstrahu můžete také uzavřít pomocí terminálu s následujícím příkazem:

```bash
/usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Close --alert-id <ALERT_GUID>
```

### <a name="troubleshooting"></a>Řešení potíží

Řešení potíží s modulem plug-in je možné provést ručně v terminálu. Použijte následující metodu:

```bash
/usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Monitor
```

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Použití PowerShellu k monitorování stavu a výstrah

Pokud nepoužíváte Operations Manager, Nagios nebo řešení založené na Nagios, můžete pomocí PowerShellu povolit širokou škálu řešení monitorování, která se budou integrovat s Azure Stack.

1. Pokud chcete použít PowerShell, ujistěte se, že máte [nainstalovaný PowerShell a nakonfigurovat](azure-stack-powershell-install.md) ho pro prostředí Azure Stack operator. Instalace prostředí PowerShell na místní počítač, který můžete dosáhnout koncového bodu Resource Manager (správce) (https://adminmanagement. [ Oblast]. [External_FQDN]).

2. Spusťte následující příkazy pro připojení k Azure Stack prostředí jako operátor Azure Stack:

   ```powershell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN] `
      -AzureKeyVaultDnsSuffix adminvault.[Region].[External_FQDN] `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.[Region].[External_FQDN]

   Connect-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. Pro práci s výstrahami použijte příkazy, jako jsou následující příklady:
   ```powershell
    # Retrieve all alerts
    $Alerts = Get-AzsAlert
    $Alerts

    # Filter for active alerts
    $Active = $Alerts | Where-Object { $_.State -eq "active" }
    $Active

    # Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    $RPHealth = Get-AzsRPHealth
    $RPHealth

    # Retrieve infrastructure role instance health
    $FRPID = $RPHealth | Where-Object { $_.DisplayName -eq "Capacity" }
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId
    ```

## <a name="learn-more"></a>Víc se uč

Informace o integrovaném monitorování stavu najdete v tématu [monitorování stavu a výstrah v Azure Stack](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Další postup

[Integrace zabezpečení](azure-stack-integrate-security.md)
