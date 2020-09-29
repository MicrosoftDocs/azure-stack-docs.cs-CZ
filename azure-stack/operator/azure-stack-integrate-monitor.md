---
title: Integrace řešení pro externí monitorování pomocí centra Azure Stack
description: Naučte se integrovat Azure Stack hub s externím řešením monitorování ve vašem datovém centru.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 04/10/2020
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 06/05/2019
ms.openlocfilehash: 231e4ac3b0bc8e0d43c608ff252f7d4c274e84a8
ms.sourcegitcommit: 1c5e7d8419037c0f3ef6fe9d8e6bfb6a59659c84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89428545"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack-hub"></a>Integrace řešení pro externí monitorování pomocí centra Azure Stack

Pro externí monitorování infrastruktury centra Azure Stack musíte monitorovat software Azure Stack hub, fyzické počítače a fyzické síťové přepínače. Každá z těchto oblastí nabízí metodu pro načtení informací o stavu a upozornění:

- Azure Stack hub software nabízí rozhraní API založené na REST, které načte stav a upozornění. Používání softwarově definovaných technologií, jako je Prostory úložiště s přímým přístupem, stav úložiště a výstrah, je součástí monitorování softwaru.
- Fyzické počítače mohou zpřístupnit informace o stavu a výstrahách prostřednictvím řadičů pro správu základní desky (BMC).
- Zařízení fyzické sítě mohou zpřístupnit informace o stavu a upozornění prostřednictvím protokolu SNMP.

Každé řešení centra Azure Stack se dodává s hostitelem životního cyklu hardwaru. Tento hostitel spustí monitorovací software výrobce OEM (Original Equipment Manufacturer) dodavatele hardwaru pro fyzické servery a síťová zařízení. Pokud jejich řešení pro monitorování můžete integrovat se stávajícími řešeními monitorování ve vašem datovém centru, obraťte se na poskytovatele OEM.

> [!IMPORTANT]
> Externí řešení monitorování, které používáte, musí být bez agenta. Do komponent centra Azure Stack nemůžete instalovat agenty třetích stran.

Následující diagram znázorňuje tok provozu mezi integrovaným systémem centra Azure Stack, hostitelem životního cyklu hardwaru, externím řešením monitorování a externím systémem pro sběr lístků a dat.

![Diagram znázorňující provoz mezi Azure Stackm centrem, monitorováním a řešením lístků.](media/azure-stack-integrate-monitor/monitoringintegration.svg)  

> [!NOTE]
> Integrace externích monitorování na fyzických serverech není povolená a aktivně blokovaná pomocí seznamů Access Control (ACL). Externí integrace monitorování je podporovaná přímo s fyzickými síťovými zařízeními. Pokud chcete tuto funkci povolit, obraťte se na svého poskytovatele OEM.

Tento článek vysvětluje, jak integrovat Azure Stack centra s externími řešeními monitorování, jako jsou System Center Operations Manager a Nagios. Obsahuje také informace o tom, jak pracovat s výstrahami programově pomocí prostředí PowerShell nebo prostřednictvím REST API volání.

## <a name="integrate-with-operations-manager"></a>Integrace s Operations Manager

K externímu monitorování centra Azure Stack můžete použít Operations Manager. Sada System Center Management Pack pro Microsoft Azure Stack hub umožňuje monitorovat více nasazení centra Azure Stack pomocí jediné instance Operations Manager. Management Pack používá poskytovatele prostředků stavu a aktualizuje rozhraní REST API poskytovatele prostředků ke komunikaci se službou Azure Stack hub. Pokud se chystáte obejít monitorovací software OEM, který běží na hostiteli životního cyklu hardwaru, můžete nainstalovat sady Management Pack dodavatele a monitorovat fyzické servery. K monitorování síťových přepínačů můžete použít taky Operations Manager zjišťování síťových zařízení.

Management Pack pro centrum Azure Stack nabízí následující možnosti:

- Můžete spravovat několik nasazení centra Azure Stack.
- Podporuje se Azure Active Directory (Azure AD) a Active Directory Federation Services (AD FS) (AD FS).
- Výstrahy můžete načíst a zavřít.
- Existuje řídicí panel pro stav a kapacitu.
- Zahrnuje detekci režimu automatické údržby pro dobu, kdy probíhá oprava a aktualizace (P&U).
- Zahrnuje vynucené úlohy aktualizace pro nasazení a oblast.
- Do oblasti můžete přidat vlastní informace.
- Podporuje oznamování a vytváření sestav.

Pokud chcete stáhnout sadu System Center Management Pack a přidruženou uživatelskou příručku, přečtěte si téma [Stažení sady Management Pack pro System Center pro Microsoft Azure Stack hub](https://www.microsoft.com/en-us/download/details.aspx?id=55184). Můžete ho také stáhnout přímo z Operations Manager.

Pro řešení lístků můžete Operations Manager integrovat s System Center Service Manager. Integrovaný konektor produktů umožňuje obousměrnou komunikaci, která umožňuje Zavřít výstrahu v Azure Stack hub a Operations Manager po vyřešení žádosti o službu v Service Manager.

Následující diagram znázorňuje integraci Azure Stackho centra s existujícím nasazením nástroje System Center. Pomocí nástroje System Center Orchestrator nebo Service Management Automation (SMA) můžete automatizovat Service Manager a spouštět operace v centru Azure Stack.

![Diagram znázorňující integraci s OM, Service Manager a SMA](media/azure-stack-integrate-monitor/systemcenterintegration.svg)

## <a name="integrate-with-nagios"></a>Integrace s Nagios

Můžete nastavit a nakonfigurovat modul plug-in Nagios pro Centrum Microsoft Azure Stack.

Modul plug-in Nagios monitoring byl vyvinut společně s partnerskými řešeními Cloudbase, které je k dispozici v rámci opravňující bezplatné softwarové licence – MIT (Massachusetts Institute of Technology).

Modul plug-in je napsaný v Pythonu a využívá poskytovatele prostředků stavu REST API. Nabízí základní funkce pro načítání a zavírání výstrah v Azure Stackovém centru. Podobně jako v rámci nástroje System Center Management Pack umožňuje přidat více nasazení centra Azure Stack a odesílat oznámení.

S verzí 1,2 Azure Stack hub – modul plug-in Nagios využívá knihovnu Microsoft ADAL Library a podporuje ověřování pomocí instančního objektu s tajným kódem nebo certifikátem. Konfigurace byla také zjednodušená pomocí jednoho konfiguračního souboru s novými parametry. Teď podporuje Azure Stack nasazení centra pomocí Azure AD a AD FS jako systém identit.

> [!IMPORTANT]
> AD FS podporuje pouze interaktivní přihlašovací relace. Pokud vyžadujete neinteraktivní přihlášení k automatizovanému scénáři, je nutné použít hlavní název služby (SPN).

Modul plug-in funguje s Nagios 4x a XI. Pokud chcete stáhnout modul plug-in, přečtěte si téma [monitorování výstrah centra Azure Stack](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Lokalita ke stažení obsahuje také podrobnosti o instalaci a konfiguraci.

### <a name="requirements-for-nagios"></a>Požadavky na Nagios

1. Minimální verze Nagios je 4. x

2. Microsoft Azure Active Directory knihovna Pythonu. Tuto knihovnu je možné nainstalovat pomocí Python PIP.

    ```bash  
    sudo pip install adal pyyaml six
    ```

### <a name="install-plugin"></a>Nainstalovat modul plug-in

V této části se dozvíte, jak nainstalovat modul plug-in centra Azure Stack za předpokladu výchozí instalace Nagios.

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

1. Zkopírujte modul plug-in `azurestack_plugin.py` do následujícího adresáře: `/usr/local/nagios/libexec` .

2. Zkopírujte obslužnou rutinu `azurestack_handler.sh` do následujícího adresáře: `/usr/local/nagios/libexec/eventhandlers` .

3. Ujistěte se, že je soubor modulu plug-in nastavený jako spustitelný:

    ```bash
    sudo cp azurestack_plugin.py <PLUGINS_DIR>
    sudo chmod +x <PLUGINS_DIR>/azurestack_plugin.py
    ```

### <a name="configure-plugin"></a>Konfigurace modulu plug-in

Následující parametry jsou k dispozici pro konfiguraci v souboru azurestack. cfg. Parametry tučného písma je potřeba nakonfigurovat nezávisle na zvoleném modelu ověřování.

Další informace o tom, jak vytvořit hlavní název služby (SPN), najdete v tématu [použití identity aplikace pro přístup k prostředkům](azure-stack-create-service-principals.md).

| Parametr | Popis | Authentication |
| --- | --- | --- |
| * * External_domain_fqdn * * | Plně kvalifikovaný název domény externí domény |    |
| * * oblast: * * | Název oblasti |    |
| * * tenant_id: * * | ID tenanta\* |    |
| client_id: | ID klienta | SPN s tajným klíčem |
| client_secret: | Heslo klienta | SPN s tajným klíčem |
| client_cert \* \* : | Cesta k certifikátu | Hlavní název služby s certifikátem |
| client_cert_thumbprint \* \* : | Kryptografický otisk certifikátu | Hlavní název služby s certifikátem |

\*ID tenanta se nevyžaduje pro Azure Stack nasazení centra pomocí AD FS.

\*\* Tajný klíč klienta a klientský certifikát se vzájemně vylučují.

Ostatní konfigurační soubory obsahují volitelná nastavení konfigurace, která je možné nakonfigurovat i v Nagios.

> [!Note]  
> Ověřte umístění cíle v azurestack_hosts. cfg a azurestack_services. cfg.

| Konfigurace | Popis |
| --- | --- |
| azurestack_commands. cfg | Konfigurace obslužné rutiny beze změn – požadavek |
| azurestack_contacts. cfg | Nastavení oznámení |
| azurestack_hosts. cfg | Azure Stack pojmenovávání nasazení centra |
| azurestack_services. cfg | Konfigurace služby |

### <a name="setup-steps"></a>Postup nastavení

1. Upravte konfigurační soubor.

2. Zkopírujte upravené konfigurační soubory do následující složky: `/usr/local/nagios/etc/objects` .

### <a name="update-nagios-configuration"></a>Aktualizovat konfiguraci Nagios

Konfigurace Nagios se musí aktualizovat, aby se zajistilo, že se načte modul plug-in centra Azure Stack – Nagios.

1. Otevřete následující soubor:

   ```bash  
   /usr/local/nagios/etc/nagios.cfg
   ```

2. Přidejte následující položku:

   ```bash  
   # Load the Azure Stack Hub Plugin Configuration
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_contacts.cfg
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_commands.cfg
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_hosts.cfg
   cfg_file=/usr/local/Nagios/etc/objects/azurestack_services.cfg
   ```

3. Znovu načíst Nagios.

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

Řešení potíží s modulem plug-in je prováděno ručním voláním modulu plug-in v terminálu. Použijte následující metodu:

```bash
/usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Monitor
```

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Použití PowerShellu k monitorování stavu a výstrah

Pokud nepoužíváte Operations Manager, Nagios nebo řešení založené na Nagios, můžete pomocí PowerShellu povolit širokou škálu řešení monitorování pro integraci se službou Azure Stack hub.

1. Pokud chcete používat PowerShell, ujistěte se, že máte [nainstalovaný PowerShell a nakonfigurovat](azure-stack-powershell-install.md) ho pro prostředí operátora centra Azure Stack. Nainstalujte PowerShell do místního počítače, který se může připojit ke koncovému bodu Správce prostředků (správce) ( https://adminmanagement . [ oblast]. [External_FQDN]).

2. Spusťte následující příkazy, které se připojí k prostředí Azure Stack hub jako operátor centra Azure Stack:

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

## <a name="learn-more"></a>Další informace

Informace o integrovaném monitorování stavu najdete v tématu [monitorování stavu a výstrah v centru Azure Stack](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Další kroky

[Integrace zabezpečení](azure-stack-integrate-security.md)
