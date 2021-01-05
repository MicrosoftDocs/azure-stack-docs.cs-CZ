---
title: Příprava pro hostitele rozšíření v centru Azure Stack
description: Přečtěte si, jak připravit hostitele rozšíření v centru Azure Stack, který se automaticky povolí prostřednictvím balíčku aktualizace centra Azure Stack po verzi 1808.
author: PatAltimore
ms.author: patricka
ms.date: 1/22/2020
ms.topic: article
ms.reviewer: thoroet
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 43cb091b3076f26880599e35a770383182e944d0
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870609"
---
# <a name="prepare-for-extension-host-in-azure-stack-hub"></a>Příprava pro hostitele rozšíření v centru Azure Stack

Hostitel rozšíření zabezpečuje Azure Stack centra tím, že snižuje počet požadovaných portů TCP/IP. Tento článek se zabývá přípravou centra Azure Stack pro hostitele rozšíření, který se automaticky povolí prostřednictvím balíčku aktualizace centra Azure Stack po aktualizaci 1808. Tento článek se týká Azure Stack centra aktualizací 1808, 1809 a 1811.

## <a name="certificate-requirements"></a>Požadavky na certifikáty

Hostitel rozšíření implementuje dva nové obory názvů domény pro zaručení jedinečných hostitelských záznamů pro každé rozšíření portálu. Nové obory názvů domény vyžadují pro zajištění zabezpečené komunikace dva další certifikáty se zástupnými znaky.

V tabulce jsou uvedeny nové obory názvů a přidružené certifikáty:

| Složka pro nasazení | Požadovaný předmět certifikátu a alternativní názvy subjektu (SAN) | Rozsah (na oblast) | Obor názvů subdomény |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| Hostitel rozšíření Správce | *. adminhosting. \<region> .\<fqdn> (Zástupné certifikáty SSL) | Hostitel rozšíření Správce | adminhosting. \<region> ..\<fqdn> |
| Hostitel veřejného rozšíření | *. hostování. \<region> ..\<fqdn> (Zástupné certifikáty SSL) | Hostitel veřejného rozšíření | hostování. \<region> ..\<fqdn> |

Podrobné požadavky na certifikáty najdete v tématu [požadavky na certifikát infrastruktury veřejných klíčů služby Azure Stack hub](azure-stack-pki-certs.md).

## <a name="create-certificate-signing-request"></a>Vytvořit žádost o podepsání certifikátu

Nástroj pro kontrolu připravenosti centra Azure Stack umožňuje vytvořit žádost o podepsání certifikátu pro dva nové a požadované certifikáty SSL. Postupujte podle kroků v článku [generování žádosti o podepsání certifikátů centra Azure Stack](azure-stack-get-pki-certs.md).

> [!Note]  
> Tento krok můžete přeskočit v závislosti na tom, jak jste si vyžádali certifikáty SSL.

## <a name="validate-new-certificates"></a>Ověřit nové certifikáty

1. Otevřete PowerShell se zvýšenými oprávněními na hostiteli životního cyklu hardwaru nebo pracovní stanici správy centra Azure Stack.
2. Spuštěním následující rutiny nainstalujte nástroj pro kontrolu připravenosti centra Azure Stack:

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
    > Pokud nasazujete s Azure Active Directory federované služby (AD FS), musí se do **$Directories** ve skriptu přidat tyto adresáře: `ADFS` , `Graph` .

4. V příslušných adresářích umístěte existující certifikáty, které aktuálně používáte, do centra Azure Stack. Zadejte například certifikát **ARM správce** do `Arm Admin` složky. A potom umístěte nově vytvořené hostitelské certifikáty do `Admin extension host` `Public extension host` adresářů a.
5. Spuštěním následující rutiny spusťte kontrolu certifikátu:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ```

6. Zkontroluje výstup a pokud všechny certifikáty přejdou všechny testy.


## <a name="import-extension-host-certificates"></a>Importovat certifikáty hostitele rozšíření

Pro další kroky použijte počítač, který se může připojit k privilegovanému koncovému bodu centra Azure Stack. Ujistěte se, že máte přístup k novým souborům certifikátů z tohoto počítače.

1. Pro další kroky použijte počítač, který se může připojit k privilegovanému koncovému bodu centra Azure Stack. Ujistěte se, že máte přístup k novým souborům certifikátů z tohoto počítače.
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
Pokud jsou u jednotlivých hostitelů nakonfigurované záznamy pro publikování koncových bodů centra Azure Stack, je potřeba vytvořit dva další záznamy hostitele A:

| IP adresa | Název hostitele | Typ |
|----|------------------------------|------|
| \<IP> | *. Adminhosting. \<Region> ..\<FQDN> | A |
| \<IP> | *. Hostování. \<Region> ..\<FQDN> | A |

Přidělené IP adresy se dají načíst pomocí privilegovaného koncového bodu spuštěním rutiny **Get-AzureStackStampInformation**.

### <a name="ports-and-protocols"></a>Porty a protokoly

Článek [Azure Stack centra pro integraci Datacenter – publikování koncových bodů](azure-stack-integrate-endpoints.md) pokrývá porty a protokoly, které vyžadují příchozí komunikaci pro publikování Azure Stackho centra před zavedením hostitele rozšíření.

### <a name="publish-new-endpoints"></a>Publikování nových koncových bodů

Pomocí brány firewall musí být publikovány dva nové koncové body. Přidělené IP adresy z fondu veřejných VIP adres se dají načíst pomocí následujícího kódu, který se musí spustit z [privilegovaného koncového bodu prostředí](azure-stack-privileged-endpoint.md)Azure Stack hub.

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Obtain DNS Servers and extension host information from Azure Stack Hub Stamp Information and find the IPs for the Host Extension Endpoints
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

#### <a name="sample-output"></a>Vzorový výstup

```powershell
Can access AZS DNS
The IP for the Admin Extension Host is: *.adminhosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.adminhosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
The IP address for the Tenant Extension Host is *.hosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.hosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
```

> [!Note]  
> Tuto změnu udělejte před tím, než povolíte hostitele rozšíření. To umožňuje, aby byly portály centra Azure Stack nepřetržitě přístupné.

| Koncový bod (VIP) | Protokol | Porty |
|----------------|----------|-------|
| Hostování správců | HTTPS | 443 |
| Hosting | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>Aktualizovat existující pravidla publikování (povolení hostitele rozšíření)

> [!Note]  
> Balíček aktualizace centra 1808 **Azure Stack ještě nepovoluje hostitele** rozšíření. Umožňuje připravit hostitele rozšíření importem požadovaných certifikátů. Než bude hostitel rozšíření automaticky povolený prostřednictvím balíčku aktualizace centra Azure Stack po aktualizaci 1808, nezavírejte žádné porty.

Následující existující porty koncového bodu musí být uzavřeny ve stávajících pravidlech brány firewall.

> [!Note]  
> Po úspěšném ověření se tyto porty doporučuje zavřít.

| Koncový bod (VIP) | Protokol | Porty |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| Portál (správce) | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015 |
| Portál (uživatel) | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003 |
| Azure Resource Manager (správce) | HTTPS | 30024 |
| Azure Resource Manager (uživatel) | HTTPS | 30024 |

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [integraci brány firewall](azure-stack-firewall.md).
- Přečtěte si informace o [generování žádosti o podepsání certifikátů centra Azure Stack](azure-stack-get-pki-certs.md).
