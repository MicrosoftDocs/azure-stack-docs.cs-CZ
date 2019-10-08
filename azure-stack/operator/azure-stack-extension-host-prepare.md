---
title: Příprava pro hostitele rozšíření v Azure Stack | Microsoft Docs
description: Přečtěte si, jak připravit hostitele rozšíření v Azure Stack, který se automaticky povolí prostřednictvím balíčku aktualizace Azure Stack po verzi 1808.
services: azure-stack
keywords: ''
author: mattbriggs
ms.author: mabrigg
ms.date: 10/02/2019
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: femila
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 75070550f1863457c3a2aaf9ab5915536372d55b
ms.sourcegitcommit: 451cfaa24b349393f36ae9d646d4d311a14dd1fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "72019269"
---
# <a name="prepare-for-extension-host-in-azure-stack"></a>Příprava na hostitele rozšíření v Azure Stack

Hostitel rozšíření je zabezpečený Azure Stack snížením počtu požadovaných portů TCP/IP. V tomto článku se podíváme na přípravu Azure Stack pro hostitele rozšíření, který se automaticky povolí prostřednictvím balíčku aktualizace Azure Stack po aktualizaci 1808. Tento článek se týká Azure Stack aktualizací 1808, 1809 a 1811.

## <a name="certificate-requirements"></a>Požadavky na certifikát

Hostitel rozšíření implementuje dva nové obory názvů domény pro zaručení jedinečných hostitelských záznamů pro každé rozšíření portálu. Nové obory názvů domény vyžadují pro zajištění zabezpečené komunikace dva další certifikáty se zástupnými znaky.

V tabulce jsou uvedeny nové obory názvů a přidružené certifikáty:

| Složka pro nasazení | Požadovaný předmět certifikátu a alternativní názvy subjektu (SAN) | Rozsah (na oblast) | Obor názvů subdomény |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| Hostitel rozšíření Správce | *. adminhosting. \<region >. \<fqdn > (zástupné certifikáty SSL) | Hostitel rozšíření Správce | adminhosting.\<region>.\<fqdn> |
| Hostitel veřejného rozšíření | *. Hosting. \<region >. \<fqdn > (zástupné certifikáty SSL) | Hostitel veřejného rozšíření | hostování. \<region >. \<fqdn > |

Podrobné požadavky na certifikáty najdete v tématu [Azure Stack požadavky na certifikát infrastruktury veřejných klíčů](azure-stack-pki-certs.md).

## <a name="create-certificate-signing-request"></a>Vytvořit žádost o podepsání certifikátu

Nástroj pro kontrolu připravenosti na Azure Stack umožňuje vytvořit žádost o podepsání certifikátu pro dva nové a požadované certifikáty SSL. Postupujte podle kroků v článku [generování žádosti o podepsání certifikátů Azure Stack certifikáty](azure-stack-get-pki-certs.md).

> [!Note]  
> Tento krok můžete přeskočit v závislosti na tom, jak jste si vyžádali certifikáty SSL.

## <a name="validate-new-certificates"></a>Ověřit nové certifikáty

1. Otevřete PowerShell se zvýšenými oprávněními na hostiteli životního cyklu hardwaru nebo na pracovní stanici pro správu Azure Stack.
2. Spuštěním následující rutiny nainstalujte nástroj pro kontrolu připravenosti na Azure Stack:

    ```powershell  
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

3. Spuštěním následujícího skriptu vytvořte požadovanou strukturu složek:

    ```powershell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal', 'Admin extension host', 'Public extension host'

    $destination = 'c:\certificates'

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```

    > [!Note]  
    > Pokud nasazujete s Azure Active Directory federované služby (AD FS), musí se do **$Directories** ve skriptu přidat tyto adresáře: `ADFS`, `Graph`.

4. V příslušných adresářích umístěte existující certifikáty, které aktuálně používáte v Azure Stack. Zadejte například certifikát **ARM správce** do složky `Arm Admin`. A pak vložte nově vytvořené hostitelské certifikáty do adresářů `Admin extension host` a `Public extension host`.
5. Spuštěním následující rutiny spusťte kontrolu certifikátu:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ```

6. Zkontroluje výstup a pokud všechny certifikáty přejdou všechny testy.


## <a name="import-extension-host-certificates"></a>Importovat certifikáty hostitele rozšíření

Použijte počítač, který se může připojit k Azure Stack privilegovanému koncovému bodu pro další kroky. Ujistěte se, že máte přístup k novým souborům certifikátů z tohoto počítače.

1. Použijte počítač, který se může připojit k Azure Stack privilegovanému koncovému bodu pro další kroky. Ujistěte se, že máte přístup k novým souborům certifikátů z tohoto počítače.
2. Otevřete PowerShell ISE a spusťte další bloky skriptu.
3. Importujte certifikát pro koncový bod hostování správce.

    ```powershell  

    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$AdminHostingCertContent = [Byte[]](Get-Content c:\certificate\myadminhostingcertificate.pfx -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($AdminHostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($AdminHostingCertContent, $CertPassword)
            Import-AdminHostingServiceCert $AdminHostingCertContent $certPassword
    }
    ```
4. Importujte certifikát pro hostitelský koncový bod.
    ```powershell  
    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$HostingCertContent = [Byte[]](Get-Content c:\certificate\myhostingcertificate.pfx  -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($HostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($HostingCertContent, $CertPassword)
            Import-UserHostingServiceCert $HostingCertContent $certPassword
    }
    ```

### <a name="update-dns-configuration"></a>Aktualizovat konfiguraci DNS

> [!Note]  
> Tento krok není nutný, pokud jste použili delegování zóny DNS pro integraci DNS.
Pokud je u jednotlivých hostitelů nakonfigurovaných pro publikování Azure Stackch koncových bodů, je potřeba vytvořit dva další záznamy hostitele A:

| IP | Název hostitele | type |
|----|------------------------------|------|
| \<IP> | *. Adminhosting. \<Region >. \<FQDN > | A |
| \<IP> | *. Hostování. \<Region >. \<FQDN > | A |

Přidělené IP adresy se dají načíst pomocí privilegovaného koncového bodu spuštěním rutiny **Get-AzureStackStampInformation**.

### <a name="ports-and-protocols"></a>Porty a protokoly

Článek [Azure Stack integrace Datacenter – koncové body pro publikování](azure-stack-integrate-endpoints.md) zahrnují porty a protokoly, které pro publikování Azure Stack před zavedením hostitele rozšíření vyžadují příchozí komunikaci.

### <a name="publish-new-endpoints"></a>Publikování nových koncových bodů

Pomocí brány firewall musí být publikovány dva nové koncové body. Přidělené IP adresy z fondu veřejných VIP adres se dají načíst pomocí následujícího kódu, který se musí spustit z [privilegovaného koncového bodu](azure-stack-privileged-endpoint.md)Azure Stackho prostředí.

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Obtain DNS Servers and extension host information from Azure Stack Stamp Information and find the IPs for the Host Extension Endpoints
$StampInformation = Invoke-Command $PEPSession {Get-AzureStackStampInformation} | Select-Object -Property ExternalDNSIPAddress01, ExternalDNSIPAddress02, @{n="TenantHosting";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://*.","testdnsentry"-replace "/"}},  @{n="AdminHosting";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://*.","testdnsentry"-replace "/"}},@{n="TenantHostingDNS";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://",""-replace "/"}},  @{n="AdminHostingDNS";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://",""-replace "/"}}
If (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting -ErrorAction SilentlyContinue) {
    Write-Host "Can access AZS DNS" -ForegroundColor Green
    $AdminIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress02 -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Else {
    Write-Host "Cannot access AZS DNS" -ForegroundColor Yellow
    $AdminIP = (Resolve-DnsName -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Remove-PSSession -Session $PEPSession
```

#### <a name="sample-output"></a>Ukázkový výstup

```powershell
Can access AZS DNS
The IP for the Admin Extension Host is: *.adminhosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.adminhosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
The IP address for the Tenant Extension Host is *.hosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.hosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
```

> [!Note]  
> Tuto změnu udělejte před tím, než povolíte hostitele rozšíření. To umožňuje, aby byly portály Azure Stack nepřetržitě přístupné.

| Koncový bod (VIP) | Protocol | Porty |
|----------------|----------|-------|
| Hostování správců | HTTPS | 443 |
| Hostování | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>Aktualizovat existující pravidla publikování (povolení hostitele rozšíření)

> [!Note]  
> Balíček aktualizace 1808 **Azure Stack ještě nepovoluje hostitele** rozšíření. Umožňuje připravit hostitele rozšíření importem požadovaných certifikátů. Než bude hostitel rozšíření automaticky povolený prostřednictvím balíčku aktualizací Azure Stack po aktualizaci 1808, nezavírejte žádné porty.

Následující existující porty koncového bodu musí být uzavřeny ve stávajících pravidlech brány firewall.

> [!Note]  
> Po úspěšném ověření se tyto porty doporučuje zavřít.

| Koncový bod (VIP) | Protocol | Porty |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| Portál (správce) | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015 |
| Portál (uživatel) | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003 |
| Azure Resource Manager (správce) | HTTPS | 30024 |
| Azure Resource Manager (uživatel) | HTTPS | 30024 |

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [integraci brány firewall](azure-stack-firewall.md).
- Přečtěte si informace o [generování žádosti o podepsání certifikátů Azure Stack](azure-stack-get-pki-certs.md).
