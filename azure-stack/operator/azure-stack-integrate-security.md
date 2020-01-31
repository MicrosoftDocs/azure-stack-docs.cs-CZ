---
title: Integrace centra Azure Stack s řešeními monitorování pomocí předávání syslog
description: Naučte se integrovat Azure Stack hub s řešeními monitorování pomocí předávání syslog.
author: ihenkel
ms.topic: article
ms.date: 01/10/2020
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 01/10/2020
ms.openlocfilehash: 2b8a6b06b0eca680a42d7c15bfe7fbb43afec29a
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882081"
---
# <a name="integrate-azure-stack-hub-with-monitoring-solutions-using-syslog-forwarding"></a>Integrace centra Azure Stack s řešeními monitorování pomocí předávání syslog

V tomto článku se dozvíte, jak pomocí protokolu syslog integrovat Azure Stack infrastrukturu centra s externími řešeními zabezpečení, která jsou už ve vašem datovém centru nasazená. Například systém správy událostí pro informace o zabezpečení (SIEM). Kanál syslog zpřístupňuje audity, výstrahy a protokoly zabezpečení ze všech součástí infrastruktury centra Azure Stack. Pomocí předávání syslog můžete integrovat řešení pro monitorování zabezpečení a načíst všechny audity, výstrahy a protokoly zabezpečení a ukládat je do uchování.

Počínaje aktualizací 1809 Azure Stack hub má integrovaného klienta syslog, který po nakonfigurování generuje zprávy syslog s datovou částí ve formátu Common Event Format (CEF).

Následující diagram popisuje integraci Azure Stackho centra s externím SIEM. Existují dva způsoby integrace, které je potřeba vzít v úvahu: první z nich (modrý) je infrastruktura centra Azure Stack, která zahrnuje virtuální počítače infrastruktury a uzly Hyper-V. Všechny audity, protokoly zabezpečení a výstrahy z těchto komponent jsou centrálně shromažďovány a zpřístupněny prostřednictvím protokolu syslog s CEF datovou částí. Tento vzor integrace je popsán na této stránce dokumentu.
Druhým modelem integrace je ten, který je znázorněný oranžová a pokrývá řadiče pro správu základní desky (BMC), hostitele životního cyklu hardwaru (HLH), virtuální počítače a virtuální zařízení, na kterých běží software pro monitorování a správu s hardwarovým partnerem. a přepínače pro začátek racku. Vzhledem k tomu, že jsou tyto součásti závislé na hardwaru, obraťte se na svého hardwarového partnera, který vám poskytne dokumentaci, jak je integrovat s externím SIEM.

![Diagram předávání syslog](media/azure-stack-integrate-security/syslog-forwarding.png)

## <a name="configuring-syslog-forwarding"></a>Konfigurace předávání syslog

Klient syslog v centru Azure Stack podporuje následující konfigurace:

1. **Syslog přes TCP se vzájemným ověřováním (klient a Server) a šifrování TLS 1,2:** V této konfiguraci může server syslog i klient syslog ověřit identitu sebe navzájem pomocí certifikátů. Zprávy se odesílají přes zašifrovaný kanál TLS 1,2.

2. **Syslog přes TCP s ověřováním serveru a šifrováním TLS 1,2:** V této konfiguraci může klient syslog ověřit identitu serveru syslog prostřednictvím certifikátu. Zprávy se odesílají přes zašifrovaný kanál TLS 1,2.

3. **Syslog přes TCP bez šifrování:** V této konfiguraci nejsou ověřeny identity klienta a serveru syslog. Zprávy se odesílají jako nešifrovaný text přes TCP.

4. **Syslog přes UDP bez šifrování:** V této konfiguraci nejsou ověřeny identity klienta a serveru syslog. Zprávy se odesílají jako nešifrovaný text přes UDP.

> [!IMPORTANT]
> Společnost Microsoft důrazně doporučuje používat protokol TCP s použitím ověřování a šifrování (konfigurace #1 nebo #2) pro produkční prostředí k ochraně před útoky prostředníkem a odposloucháváním zpráv.

### <a name="cmdlets-to-configure-syslog-forwarding"></a>Rutiny pro konfiguraci předávání syslogu
Konfigurace předávání syslog vyžaduje přístup k privilegovanému koncovému bodu (PEP). Do PEP byly přidány dvě rutiny prostředí PowerShell pro konfiguraci předávání syslog:


```powershell
### cmdlet to pass the syslog server information to the client and to configure the transport protocol, the encryption and the authentication between the client and the server

Set-SyslogServer [-ServerName <String>] [-ServerPort <UInt16>] [-NoEncryption] [-SkipCertificateCheck] [-SkipCNCheck] [-UseUDP] [-Remove]

### cmdlet to configure the certificate for the syslog client to authenticate with the server

Set-SyslogClient [-pfxBinary <Byte[]>] [-CertPassword <SecureString>] [-RemoveCertificate] [-OutputSeverity]
```
#### <a name="cmdlets-parameters"></a>Parametry rutin

Parametry pro rutinu *set-SyslogServer* :

| Parametr | Popis | Typ | Požaduje se |
|---------|---------|---------|---------|
|*ServerName* | Plně kvalifikovaný název domény nebo IP adresa serveru syslog | Řetězec | ano|
|*ServerPort* | Číslo portu, na kterém naslouchá Server syslog. | UInt16 | ano|
|*Šifrování*| Vynutit, aby klient odesílal zprávy syslog ve formátu prostého textu. | příznaků | ne|
|*SkipCertificateCheck*| Při počátečním ověřování TLS vynechejte ověření certifikátu poskytnutého serverem syslog. | příznaků | ne|
|*SkipCNCheck*| Při počátečním ověřování TLS vynechejte vynechání hodnoty pro běžný název certifikátu poskytnutého serverem syslog. | příznaků | ne|
|*UseUDP*| Použijte protokol syslog se UDP jako transportní protokol. |příznaků | ne|
|*odebrat*| Odeberte konfiguraci serveru z klienta a Zastavte předávání syslog.| příznaků | ne|

Parametry pro rutinu *set-SyslogClient* :

| Parametr | Popis | Typ |
|---------|---------| ---------|
| *pfxBinary* | Obsah souboru PFX předaných na Byte [] obsahující certifikát, který má klient používat jako identitu k ověřování na serveru syslog.  | Byte[] |
| *CertPassword* |  Heslo pro import privátního klíče, který je přidružen k souboru PFX. | SecureString |
|*RemoveCertificate* | Odeberte certifikát z klienta. | příznaků|
| *OutputSeverity* | Úroveň protokolování výstupu. Hodnoty jsou **výchozí** nebo **podrobné**. Výchozí hodnota zahrnuje úrovně závažnosti: upozornění, kritická nebo chyba. Verbose obsahuje všechny úrovně závažnosti: Verbose, informativní, Warning, kritická nebo chyba.  | Řetězec |
### <a name="configuring-syslog-forwarding-with-tcp-mutual-authentication-and-tls-12-encryption"></a>Konfigurace předávání syslogu pomocí protokolu TCP, vzájemného ověřování a šifrování TLS 1,2

V této konfiguraci klient syslog v Azure Stack centrum přepošle zprávy na server syslog přes protokol TCP s šifrováním TLS 1,2. Při počáteční signalizaci klient ověří, že server poskytuje platný důvěryhodný certifikát. Klient také poskytuje certifikát serveru jako důkaz své identity. Tato konfigurace je nejbezpečnější, protože poskytuje úplné ověření identity klienta i serveru a odesílá zprávy přes zašifrovaný kanál.

> [!IMPORTANT]
> Microsoft důrazně doporučuje použít tuto konfiguraci pro produkční prostředí. 

Pokud chcete nakonfigurovat předávání syslog pomocí protokolu TCP, vzájemného ověřování a šifrování TLS 1,2, spusťte obě tyto rutiny v relaci PEP:

```powershell
# Configure the server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>

# Provide certificate to the client to authenticate against the server
Set-SyslogClient -pfxBinary <Byte[] of pfx file> -CertPassword <SecureString, password for accessing the pfx file>
```

Klientský certifikát musí mít stejný kořenový adresář jako ten, který se zadal během nasazování centra Azure Stack. Musí také obsahovat privátní klíč.

```powershell
##Example on how to set your syslog client with the certificate for mutual authentication.
##This example script must be run from your hardware lifecycle host or privileged access workstation.

$ErcsNodeName = "<yourPEP>"
$password = ConvertTo-SecureString -String "<your cloudAdmin account password" -AsPlainText -Force
 
$cloudAdmin = "<your cloudAdmin account name>"
$CloudAdminCred = New-Object System.Management.Automation.PSCredential ($cloudAdmin, $password)
 
$certPassword = $password
$certContent = Get-Content -Path C:\cert\<yourClientCertificate>.pfx -Encoding Byte
 
$params = @{ 
    ComputerName = $ErcsNodeName 
    Credential = $CloudAdminCred 
    ConfigurationName = "PrivilegedEndpoint" 
}

$session = New-PSSession @params
 
$params = @{ 
    Session = $session 
    ArgumentList = @($certContent, $certPassword) 
}
Write-Verbose "Invoking cmdlet to set syslog client certificate..." -Verbose 
Invoke-Command @params -ScriptBlock { 
    param($CertContent, $CertPassword) 
    Set-SyslogClient -PfxBinary $CertContent -CertPassword $CertPassword }
```

### <a name="configuring-syslog-forwarding-with-tcp-server-authentication-and-tls-12-encryption"></a>Konfigurace předávání syslogu pomocí protokolu TCP, ověřování serveru a šifrování TLS 1,2

V této konfiguraci klient syslog v Azure Stack centrum přepošle zprávy na server syslog přes protokol TCP s šifrováním TLS 1,2. Při počáteční signalizaci klienta taky ověří, že server poskytuje platný důvěryhodný certifikát. Tato konfigurace brání klientovi v posílání zpráv do nedůvěryhodných cílů.
Protokol TCP používá k ověřování a šifrování výchozí konfiguraci a představuje minimální úroveň zabezpečení, kterou společnost Microsoft doporučuje pro produkční prostředí.

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
```

Pokud chcete otestovat integraci vašeho serveru syslog s klientem Azure Stack hub pomocí certifikátu podepsaného svým držitelem nebo nedůvěryhodného certifikátu, můžete tyto příznaky použít k přeskočení ověření serveru provedeného klientem během počáteční metody handshake.

```powershell
 #Skip validation of the Common Name value in the server certificate. Use this flag if you provide an IP address for your syslog server
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCNCheck

 #Skip entirely the server certificate validation
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCertificateCheck
```

> [!IMPORTANT]
> Společnost Microsoft doporučuje použití příznaku-SkipCertificateCheck pro produkční prostředí. 

### <a name="configuring-syslog-forwarding-with-tcp-and-no-encryption"></a>Konfigurace předávání syslogu pomocí protokolu TCP a bez šifrování

V této konfiguraci klient syslog v Azure Stack centrum přepošle zprávy na server syslog přes protokol TCP bez šifrování. Klient neověřuje identitu serveru ani neposkytuje vlastní identitu serveru pro ověřování.

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -NoEncryption
```

> [!IMPORTANT]
> Společnost Microsoft doporučuje použití této konfigurace pro produkční prostředí.


### <a name="configuring-syslog-forwarding-with-udp-and-no-encryption"></a>Konfigurace předávání syslogu pomocí protokolu UDP a bez šifrování

V této konfiguraci klient syslog v Azure Stack centrum přepošle zprávy na server syslog přes protokol UDP bez šifrování. Klient neověřuje identitu serveru ani neposkytuje vlastní identitu serveru pro ověřování.

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -UseUDP
```

I když je protokol UDP bez šifrování jednodušší nakonfigurovat, neposkytuje žádnou ochranu proti útokům prostředníkem a odposlouchávání zpráv.

> [!IMPORTANT]
> Společnost Microsoft doporučuje použití této konfigurace pro produkční prostředí.


## <a name="removing-syslog-forwarding-configuration"></a>Odebírá se konfigurace předávání syslog.

Chcete-li odebrat konfiguraci serveru syslog úplně a zastavit předávání syslogu, postupujte takto:

**Odebrání konfigurace serveru syslog z klienta**

```powershell  
Set-SyslogServer -Remove
```

**Odebrat klientský certifikát z klienta**

```powershell  
Set-SyslogClient -RemoveCertificate
```

## <a name="verifying-the-syslog-setup"></a>Ověření nastavení syslog

Pokud jste klienta syslog úspěšně připojili k serveru syslog, měli byste brzy začít přijímat události. Pokud se nezobrazí žádná událost, ověřte konfiguraci klienta syslog spuštěním následujících rutin:

**Ověření konfigurace serveru v klientovi syslog**

```powershell  
Get-SyslogServer
```

**Ověření nastavení certifikátu v klientovi syslog**

```powershell  
Get-SyslogClient
```

## <a name="syslog-message-schema"></a>Schéma zprávy syslog

Předávání syslogu infrastruktury centra Azure Stack odesílá zprávy naformátované ve formátu Common Event Format (CEF).
Jednotlivé zprávy syslog jsou strukturované na základě tohoto schématu:

```Syslog
<Time> <Host> <CEF payload>
```

Datová část CEF je založena na struktuře níže, ale mapování pro každé pole se liší v závislosti na typu zprávy (událost Windows, výstraha vytvořena, výstraha zavřena).

```CEF
# Common Event Format schema
CEF: <Version>|<Device Vendor>|<Device Product>|<Device Version>|<Signature ID>|<Name>|<Severity>|<Extensions>
* Version: 0.0
* Device Vendor: Microsoft
* Device Product: Microsoft Azure Stack Hub
* Device Version: 1.0
```

### <a name="cef-mapping-for-privileged-endpoint-events"></a>Mapování CEF pro události privilegovaného koncového bodu

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <PEP Event ID>
* Name: <PEP Task Name>
* Severity: mapped from PEP Level (details see the PEP Severity table below)
```

Tabulka událostí pro privilegovaný koncový bod:

| Událost | ID události PEP | Název úlohy PEP | Závažnost |
|-------|--------------| --------------|----------|
|PrivilegedEndpointAccessed|1 000|PrivilegedEndpointAccessedEvent|5|
|SupportSessionTokenRequested |1001|SupportSessionTokenRequestedEvent|5|
|SupportSessionDevelopmentTokenRequested |1002|SupportSessionDevelopmentTokenRequestedEvent|5|
|SupportSessionUnlocked |1003|SupportSessionUnlockedEvent|10|
|SupportSessionFailedToUnlock |1004|SupportSessionFailedToUnlockEvent|10|
|PrivilegedEndpointClosed |1005|PrivilegedEndpointClosedEvent|5|
|NewCloudAdminUser |1006|NewCloudAdminUserEvent|10|
|RemoveCloudAdminUser |1007|RemoveCloudAdminUserEvent|10|
|SetCloudAdminUserPassword |1008|SetCloudAdminUserPasswordEvent|5|
|GetCloudAdminPasswordRecoveryToken |1009|GetCloudAdminPasswordRecoveryTokenEvent|10|
|ResetCloudAdminPassword |1010|ResetCloudAdminPasswordEvent|10|
|PrivilegedEndpointSessionTimedOut |1017|PrivilegedEndpointSessionTimedOutEvent|5|

Tabulka PEP závažnosti:

| Závažnost | Úroveň | Číselná hodnota |
|----------|-------| ----------------|
|0|Nedefinováno|Hodnota: 0. Indikuje protokoly na všech úrovních.|
|10|Kritické|Hodnota: 1. Označuje protokoly pro kritickou výstrahu.|
|8|Chyba| Hodnota: 2. Označuje protokoly pro chybu.|
|5|Upozornění|Hodnota: 3. Indikuje protokoly pro upozornění.|
|2|Informace|Hodnota: 4. Označuje protokoly pro informační zprávu.|
|0|Podrobnosti|Hodnota: 5. Indikuje protokoly na všech úrovních.|

### <a name="cef-mapping-for-recovery-endpoint-events"></a>Mapování CEF pro události koncového bodu obnovení

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <REP Event ID>
* Name: <REP Task Name>
* Severity: mapped from REP Level (details see the REP Severity table below)
```

Tabulka událostí pro koncový bod obnovení:

| Událost | ID události zástupce | Název úlohy zástupce | Závažnost |
|-------|--------------| --------------|----------|
|RecoveryEndpointAccessed |1011|RecoveryEndpointAccessedEvent|5|
|RecoverySessionTokenRequested |1012|RecoverySessionTokenRequestedEvent |5|
|RecoverySessionDevelopmentTokenRequested |1013|RecoverySessionDevelopmentTokenRequestedEvent|5|
|RecoverySessionUnlocked |1014|RecoverySessionUnlockedEvent |10|
|RecoverySessionFailedToUnlock |1015|RecoverySessionFailedToUnlockEvent|10|
|RecoveryEndpointClosed |1016|RecoveryEndpointClosedEvent|5|

Tabulka závažnosti zástupce:

| Závažnost | Úroveň | Číselná hodnota |
|----------|-------| ----------------|
|0|Nedefinováno|Hodnota: 0. Indikuje protokoly na všech úrovních.|
|10|Kritické|Hodnota: 1. Označuje protokoly pro kritickou výstrahu.|
|8|Chyba| Hodnota: 2. Označuje protokoly pro chybu.|
|5|Upozornění|Hodnota: 3. Indikuje protokoly pro upozornění.|
|2|Informace|Hodnota: 4. Označuje protokoly pro informační zprávu.|
|0|Podrobnosti|Hodnota: 5. Indikuje protokoly na všech úrovních.|

### <a name="cef-mapping-for-windows-events"></a>Mapování CEF pro události Windows

```
* Signature ID: ProviderName:EventID
* Name: TaskName
* Severity: Level (for details, see the severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Tabulka závažnosti pro události systému Windows:

| Hodnota závažnosti CEF | Úroveň události Windows | Číselná hodnota |
|--------------------|---------------------| ----------------|
|0|Nedefinováno|Hodnota: 0. Indikuje protokoly na všech úrovních.|
|10|Kritické|Hodnota: 1. Označuje protokoly pro kritickou výstrahu.|
|8|Chyba| Hodnota: 2. Označuje protokoly pro chybu.|
|5|Upozornění|Hodnota: 3. Indikuje protokoly pro upozornění.|
|2|Informace|Hodnota: 4. Označuje protokoly pro informační zprávu.|
|0|Podrobnosti|Hodnota: 5. Indikuje protokoly na všech úrovních.|

Vlastní tabulka rozšíření pro události Windows v centru Azure Stack:

| Název vlastního rozšíření | Příklad události systému Windows | 
|-----------------------|---------|
|MasChannel | Systém|
|MasComputer | test.azurestack.contoso.com|
|MasCorrelationActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasCorrelationRelatedActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasEventData| svchost!!4132,G,0!!!!EseDiskFlushConsistency!!ESENT!!0x800000|
|MasEventDescription| Nastavení Zásady skupiny pro uživatele se úspěšně zpracovalo. Od posledního úspěšného zpracování Zásady skupiny se nezjistily žádné změny.|
|MasEventID|1501|
|MasEventRecordID|26637|
|MasExecutionProcessID | 29380|
|MasExecutionThreadID |25480|
|MasKeywords |0x8000000000000000|
|MasKeywordName |Úspěšný audit|
|MasLevel |4|
|MasOpcode |1\. místo|
|MasOpcodeName |info|
|MasProviderEventSourceName ||
|MasProviderGuid |AEA1B4FA-97D1-45F2-A64C-4D69FFFD92C9|
|MasProviderName |Microsoft-Windows-GroupPolicy|
|MasSecurityUserId |\<Windows SID\> |
|MasTask |0|
|MasTaskCategory| Vytváření procesů|
|MasUserData|KB4093112!!5112!!Installed!!0x0!!WindowsUpdateAgent Xpath: /Event/UserData/*|
|MasVersion|0|

### <a name="cef-mapping-for-alerts-created"></a>Mapování CEF pro vytvořené výstrahy

```
* Signature ID: Microsoft Azure Stack Hub Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Alert Severity (for details, see alerts severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Tabulka Závažnost výstrahy:

| Závažnost | Úroveň |
|----------|-------|
|0|Nedefinováno|
|10|Kritické|
|5|Upozornění|

Vlastní tabulka rozšíření pro výstrahy vytvořené v centru Azure Stack:

| Název vlastního rozšíření | Příklad: | 
|-----------------------|---------|
|MasEventDescription|Popis: byl vytvořen uživatelský účet \<TestUser\> pro \<TestDomain\>. Jde o potenciální bezpečnostní riziko. --NÁPRAVa: obraťte se na podporu. K vyřešení tohoto problému se vyžaduje zákaznická podpora. Nepokoušejte se tento problém vyřešit bez pomoci. Než otevřete žádost o podporu, spusťte proces shromažďování souborů protokolu pomocí pokynů z https://aka.ms/azurestacklogfiles.

### <a name="cef-mapping-for-alerts-closed"></a>Mapování CEF pro uzavřené výstrahy

```
* Signature ID: Microsoft Azure Stack Hub Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Information
```

Následující příklad ukazuje zprávu syslog s datovou částí CEF:
```
2018:05:17:-23:59:28 -07:00 TestHost CEF:0.0|Microsoft|Microsoft Azure Stack Hub|1.0|3|TITLE: User Account Created -- DESCRIPTION: A user account \<TestUser\> was created for \<TestDomain\>. It's a potential security risk. -- REMEDIATION: Please contact Support. Customer Assistance is required to resolve this issue. Do not try to resolve this issue without their assistance. Before you open a support request, start the log file collection process using the guidance from https://aka.ms/azurestacklogfiles|10
```

## <a name="next-steps"></a>Další kroky

[Zásady údržby](azure-stack-servicing-policy.md)
