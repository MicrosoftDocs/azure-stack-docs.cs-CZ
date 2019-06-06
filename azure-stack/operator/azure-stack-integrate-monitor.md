---
title: Integrace externí řešení pro monitorování pomocí služby Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak Azure Stack integrujte externí řešení monitorování ve vašem datovém centru.
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
ms.openlocfilehash: e0c3c4740a1bc8073e827ff9809cf1aafa029792
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691690"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integrace externí řešení pro monitorování pomocí služby Azure Stack

Pro externí monitorování infrastruktury Azure stacku, budete muset monitorovat software Azure Stack, fyzické počítače a fyzické síťové přepínače. Každá z těchto oblastí poskytuje metodu pro načtení informací o stavu a výstrahy:

- Azure Stack software nabízí rozhraní API založené na protokolu REST pro získání stavu a výstrahy. Použití technologie softwarově definovaných jako prostory úložiště – přímé, stav úložiště a výstrahy jsou součástí softwaru monitorování.
- Fyzické počítače zpřístupnit stavu a informace o výstrahách pomocí řadiče pro správu základní desky (BMC).
- Fyzická síťová zařízení můžete zpřístupnit stavu a informace o výstrahách prostřednictvím protokolu SNMP.

Každé řešení Azure Stack se dodává s hostiteli životního cyklu hardwaru. Tento hostitel spouští monitorování softwaru výrobce OEM dodavatele hardwaru pro fyzické servery a síťová zařízení. Zkontrolujte u svého poskytovatele výrobce OEM, pokud jejich řešení monitorování můžete integrovat se stávajícími řešeními monitorování ve vašem datovém centru.

> [!IMPORTANT]
> Externí řešení pro monitorování, které používáte, musí být bez agentů. Nemůžete instalovat agenty třetích stran v Azure stacku komponenty.

Následující diagram znázorňuje tok přenosů mezi systémech pro Azure Stack integrované, životní cyklus hostitelský hardware, externí řešení monitorování a systém kolekce externí zpracování lístků nebo data.

![Diagram znázorňující provoz mezi Azure Stack, monitorování a vytváření tiketů řešení.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

> [!NOTE]
> Externí integraci přímo s fyzickými servery monitorování není povolené a blokované aktivně pomocí seznamů řízení přístupu (ACL).  Externí monitorování integraci přímo s fyzických síťových zařízení je podporována, Zkontrolujte prosím se svým poskytovatelem OEM o tom, jak povolit tuto funkci.

Tento článek vysvětluje, jak Azure Stack integrujte externí monitorovacích řešení, jako je System Center Operations Manager a Nagios. Zahrnuje také tom, jak pracovat s výstrahami prostřednictvím kódu programu pomocí Powershellu nebo pomocí volání rozhraní REST API.

## <a name="integrate-with-operations-manager"></a>Integrace s Operations Managerem

Nástroj Operations Manager můžete použít pro externí monitorování služby Azure Stack. System Center Management Pack for Microsoft Azure Stack umožňuje monitorovat více nasazení Azure Stack s jednou instancí nástroje Operations Manager. Sada management pack používá ke komunikaci s Azure Stack stav poskytovatele prostředků a aktualizace poskytovatele prostředků rozhraní REST API. Pokud chcete obejít OEM monitorování software, který běží na hostiteli životního cyklu hardwaru, můžete nainstalovat sady management Pack dodavatele monitorovat fyzické servery. Zjišťování síťových zařízení nástroje Operations Manager můžete použít také k monitorování síťových přepínačů.

Sada management pack pro službu Azure Stack nabízí následující možnosti:

- Může spravovat více nasazení Azure Stack
- Je dostupná podpora pro Azure Active Directory (Azure AD) a Active Directory Federation Services (AD FS)
- Můžete načíst a zavřít upozornění
- Stavu a kapacitu řídicího panelu
- Zahrnuje režim údržby automatického zjišťování pro při opravy a aktualizace (P & U) je v průběhu
- Zahrnuje vynucení aktualizace úlohy pro nasazení a oblast
- Můžete přidat vlastní informace do oblasti
- Podporuje oznámení a vytváření sestav

System Center Management Pack si můžete stáhnout pro Microsoft Azure Stack a přidružené [uživatelská příručka](https://www.microsoft.com/en-us/download/details.aspx?id=55184), nebo přímo z nástroje Operations Manager.

Vytváření tiketů řešení můžete integrovat nástroje Operations Manager pomocí System Center Service Manager. Produkt integrovaný konektor umožňuje obousměrnou komunikaci, která umožňuje zavření výstrahy ve službě Azure Stack a nástroje Operations Manager po vyřešení žádosti o službu na portálu Service Manager.

Následující diagram znázorňuje integrace služby Azure Stack s existujícím nasazením nástroje System Center. Můžete automatizovat další Service Manageru pomocí nástroje System Center Orchestrator nebo Service Management Automation (SMA) pro spouštění operací ve službě Azure Stack.

![Diagram znázorňující integrace s Operations MANAGERU, portálu Service Manager a služby SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integrace s Nagios

Můžete nastavit a nakonfigurovat modul plug-in Nagios pro Microsoft Azure Stack.

Nagios monitorování modulů plug-in byla vyvinuta spolu s Cloudbase partnerských řešení, která je k dispozici v rámci licence MIT bezplatného softwaru - MIT (Massachusetts Institute of Technology).

Modul plug-in je napsaný v Pythonu a využívá rozhraní REST API poskytovatele prostředků stavu. Poskytuje základní funkce pro načtení a zavírání výstrah ve službě Azure Stack. Jako je System Center management pack umožňuje vám přidat více nasazení Azure Stack a odesílání oznámení.

Verze 1.2 Azure Stack – modul plug-in Nagios využívá knihovna Microsoft ADAL a podporuje ověřování pomocí instančního objektu s tajný klíč nebo certifikát. Navíc konfigurace zjednodušili jsme pomocí nové parametry jednom konfiguračním souboru. Teď podporuje nasazení Azure Stack pomocí AAD & služby AD FS jako systém identit.

Modul plug-in funguje s Nagios 4 x a XI. Můžete ji stáhnout [tady](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Server pro stahování obsahuje také podrobnosti o instalaci a konfiguraci.

### <a name="requirements-for-nagios"></a>Požadavky pro Nagios

1.  Minimální verze Nagios je 4.x

2.  Knihovna Microsoft Azure Active Directory Python. Lze ji nainstalovat pomocí Python PIP.

```bash  
sudo pip install adal pyyaml six
```

### <a name="install-plugin"></a>Instalace modulu plug-in

Tato část popisuje, jak nainstalovat modul plug-in Azure Stack, za předpokladu, že výchozí instalace Nagios.

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

1.  Zkopírujte modul plug-in `azurestack_plugin.py` do následujícího adresáře `/usr/local/nagios/libexec`.

2.  Zkopírujte obslužnou rutinu `azurestack_handler.sh` do následujícího adresáře `/usr/local/nagios/libexec/eventhandlers`.

3.  Ujistěte se, modul plug-in soubor jako spustitelný soubor

    ```bash
      sudo cp azurestack_plugin.py <PLUGINS_DIR>
      sudo chmod +x <PLUGINS_DIR>/azurestack_plugin.py
    ```

### <a name="configure-plugin"></a>Konfigurace modulu plug-in

Následující parametry jsou k dispozici v souboru azurestack.cfg. Parametry tučným písmem je potřeba nakonfigurovat nezávisle na model ověřování, kterou zvolíte.

Podrobné informace o tom, jak vytvořit hlavní název služby je popsána [tady](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals).

| Parametr | Popis | Authentication |
| --- | --- | --- |
| **External_domain_fqdn ** | Plně kvalifikovaný název domény tak, že je externí |    |
| **region: ** | Název oblasti |    |
| **tenant_id: ** | ID tenanta\* |    |
| client_id: | ID klienta | Hlavní název služby s tajným klíčem |
| client_secret: | Heslo klienta | Hlavní název služby s tajným klíčem |
| client_cert\*\*: | Cesta k certifikátu | Hlavní název služby pomocí certifikátu |
| client_cert_thumbprint\*\*: | Kryptografický otisk certifikátu | Hlavní název služby pomocí certifikátu |

\*ID tenanta není vyžadován pro nasazení Azure Stack s AD FS.

\*\* Tajný klíč klienta a klientský certifikát se vzájemně vylučují.

Další konfigurační soubory obsahují nastavení volitelné konfigurace, jak se dají konfigurovat v Nagios a.

> [!Note]  
> Zkontrolujte cílové umístění v azurestack_hosts.cfg a azurestack_services.cfg.

| Konfigurace | Popis |
| --- | --- |
| azurestack_commands.cfg | Konfiguraci obslužné rutiny nevyžaduje změny |
| azurestack_contacts.cfg | Nastavení oznámení |
| azurestack_hosts.cfg | Azure Stack – nasazování pojmenování |
| azurestack_services.cfg | Konfigurace služby |

### <a name="setup-steps"></a>Postup instalace

1.  Upravte konfigurační soubor.

2.  Zkopírujte upravený konfigurační soubory do následující složky `/usr/local/nagios/etc/objects`.

### <a name="update-nagios-configuration"></a>Aktualizovat konfiguraci Nagios

Konfiguraci Nagios je potřeba aktualizovat, aby službě Azure Stack – modul plug-in Nagios je načteno.

1.  Otevřete tento soubor

```bash  
/usr/local/nagios/etc/nagios.cfg
```

1.  Přidejte následující položku

```bash  
  #load the Azure Stack Plugin Configuration
  cfg_file=/usr/local/Nagios/etc/objects/azurestack_contacts.cfg
  cfg_file=/usr/local/Nagios/etc/objects/azurestack_commands.cfg
  cfg_file=/usr/local/Nagios/etc/objects/azurestack_hosts.cfg
  cfg_file=/usr/local/Nagios/etc/objects/azurestack_services.cfg
```

1.  Znovu načíst Nagios

```bash  
sudo service nagios reload
```

### <a name="manually-close-active-alerts"></a>Ruční zavření aktivní výstrahy

Aktivní výstrahy může být ukončeny během Nagios pomocí funkce vlastní oznámení. Vlastní oznámení musí být:

```
  /close-alert <ALERT_GUID>
```

Výstrahu můžete zavřít také pomocí terminálu následující příkaz:

```bash
  /usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Close --alert-id <ALERT_GUID>
```

### <a name="troubleshooting"></a>Řešení potíží

Řešení potíží s modul plug-in může být provedeno volání modul plug-in ručně v terminálu. Použijte následující metodu:

```bash
  /usr/local/nagios/libexec/azurestack_plugin.py --config-file /usr/local/nagios/etc/objects/azurestack.cfg --action Monitor
```

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Použití Powershellu k monitorování stavu a výstrahy

Pokud nepoužíváte nástroj Operations Manager, Nagios nebo řešení Nagios, můžete použít PowerShell umožňuje širokou škálu řešení pro integraci s Azure Stackem monitorování.

1. Jak pomocí prostředí PowerShell, ujistěte se, že máte [PowerShell nainstalovaný a nakonfigurovaný](azure-stack-powershell-install.md) pro operátor prostředí Azure Stack. Instalace prostředí PowerShell na místní počítač, který můžete dosáhnout koncového bodu Resource Manager (správce) (https://adminmanagement. [ Oblast]. [External_FQDN]).

2. Spusťte následující příkazy pro připojení k prostředí Azure Stack jako operátory Azure stacku:

   ```powershell
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https:\//adminmanagement.[Region].[External_FQDN] `
      -AzureKeyVaultDnsSuffix adminvault.[Region].[External_FQDN] `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. Použití příkazů jako jsou následující příklady pro práci s výstrahami:
   ```powershell
    #Retrieve all alerts
    $Alerts = Get-AzsAlert
    $Alerts

    #Filter for active alerts
    $Active = $Alerts | Where-Object { $_.State -eq "active" }
    $Active

    #Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    $RPHealth = Get-AzsRPHealth
    $RPHealth

    #Retrieve infrastructure role instance health
    $FRPID = $RPHealth | Where-Object { $_.DisplayName -eq "Capacity" }
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId
    ```

## <a name="learn-more"></a>Víc se uč

Informace o předdefinovaných stavu monitorování najdete v tématu [monitorovat stav a výstrahy ve službě Azure Stack](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Další postup

[Integrace zabezpečení](azure-stack-integrate-security.md)
