---
title: Konfigurace nastavení proxy serveru pro AKS na Azure Stack HCL
description: Naučte se, jak naplánovat a nakonfigurovat podporu proxy serveru pro připojení k Internetu.
author: abha
ms.topic: how-to
ms.date: 01/27/2021
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 83a3a4928f5df1b6b4520cdc6ffd277e497425ff
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873743"
---
# <a name="configure-proxy-settings-on-aks-on-azure-stack-hci"></a>Konfigurace nastavení proxy serveru na AKS při Azure Stack HCI

Pokud vaše síť vyžaduje použití proxy server pro připojení k Internetu, tento dokument vás provede kroky potřebnými k nastavení podpory proxy na AKS na Azure Stack HCL pomocí modulu AksHci a WinInetProxy PowerShellu. 

Pokud nechcete používat WinInetProxy, můžete také použít **Vlastnosti Internetu** (inetcpl.cpl) a kliknutím na kartu **připojení** a **Nastavení místní sítě** nakonfigurovat a veriy nastavení proxy server.


## <a name="before-you-begin"></a>Než začnete

Nainstalujte modul prostředí PowerShell pro [WinInetProxy](https://www.powershellgallery.com/packages/WinInetProxy/0.1.0) a nakonfigurujte proxy server spuštěním následujícího příkazu v okně pro správu prostředí PowerShell:

```Powershell
Install-Module -Name WinInetProxy -Repository PSGallery
```

Pro jiné požadavky si Projděte [požadavky na systém](./system-requirements.md). Pokud si chcete stáhnout modul prostředí PowerShell pro AksHci, přejděte na [nastavení modulu PowerShellu pro AksHci](./setup-powershell.md).

## <a name="configure-a-proxy-server-without-authentication"></a>Konfigurace proxy server bez ověřování

Pokud chcete nakonfigurovat proxy server, která nevyžaduje ověření, spusťte na každém Azure Stack uzlu clusteru HCI následující příkaz:

```powershell
Set-WinInetProxy -ProxyServer http://proxy.contoso.com:3128 -ProxyBypass "local"
```

## <a name="configure-a-proxy-server-with-authentication"></a>Konfigurace proxy server s ověřováním

Chcete-li nakonfigurovat proxy server, který vyžaduje ověření, je nutné nakonfigurovat přihlašovací údaje pro AKS na Azure Stack hostitele, aby je bylo možné použít s proxy WinINet. To se liší v závislosti na tom, jaký typ ověřování proxy vyžaduje, a může být buď NTLM/Kerberos, nebo základní ověřování.

> [!NOTE]
> Pokud chcete pro připojení k proxy server použít certifikát, zodpovídáte za zřízení tohoto certifikátu pro příslušné úložiště certifikátů na hostitelích, abyste měli jistotu, že je důvěryhodný.

### <a name="configure-a-proxy-server-with-ntlmkerberos-authentication"></a>Konfigurace proxy server s ověřováním pomocí protokolu NTLM/Kerberos

Pomocí níže uvedeného příkazu přidejte nové přihlašovací údaje pro Windows. Po zobrazení výzvy zadejte své zabezpečené heslo. Případně můžete použít také správce přihlašovacích údajů systému Windows pod položkou **přihlašovací údaje systému** Windows a přidat novou položku pro přihlašovací údaje systému Windows. 

```powershell
cmdkey /generic:proxy.contoso.com /user:username /pass
```
Když spustíte tento příkaz, zobrazí se výzva k zadání hesla.

V profilu PowerShellu přidejte následující příkaz, který povolí agentům AKS-HCI používat přihlašovací údaje uložené v mezipaměti:

```powershell
Set-WinInetProxy -ProxyServer http://proxy.contoso.com:3128 -ProxyBypass "local"
notepad $PROFILE
[System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials
```  

### <a name="configure-a-proxy-server-with-basic-authentication"></a>Konfigurace proxy server se základním ověřováním

Musíte přidat základní přihlašovací údaje pro ověřování, které je potřeba přidat do profilu PowerShellu, aby je mohl modul pro stažení agenta použít. 

> [!NOTE]
> Přihlašovací údaje v profilu PowerShellu se nešifrují a zobrazují se jako nešifrovaný text. Ujistěte se, že je profil prostředí PowerShell chráněný pomocí seznamů řízení přístupu (ACL), takže je můžou zobrazit jenom správci a účet LocalSystem.

Upravte profil PowerShellu tak, aby nahradil uživatelské jméno a heslo základními přihlašovacími údaji pro ověřování, jak je znázorněno níže:

```powershell
Set-WinInetProxy -ProxyServer http://proxy.contoso.com:3128 -ProxyBypass "local"
notepad $profile
[System.Net.WebRequest]::DefaultWebProxy.Credentials = new-object System.Net.NetworkCredential("username", "password")
```

## <a name="deploy-aks-on-azure-stack-hci-host-using-a-proxy-server"></a>Nasazení AKS na Azure Stack hostitele HCI pomocí proxy server

Po nakonfigurování proxy server můžete nastavit konfiguraci proxy serveru pro instalaci hostitele AKS pomocí `Set-AksHciConfig` příkazu. V závislosti na tom, zda proxy server vyžaduje ověření, existují různé sady kroků.

Po nakonfigurování nasazení pomocí níže uvedených možností můžete [nainstalovat hostitele AKS na Azure Stack HCI](./setup-powershell.md) a [vytvořit clustery AKS pomocí prostředí PowerShell](./create-kubernetes-cluster-powershell.md).

### <a name="configure-an-aks-host-for-a-proxy-server-with-basic-authentication"></a>Konfigurace hostitele AKS pro proxy server se základním ověřováním  

Pokud vaše proxy server vyžaduje ověření, spusťte následující příkazy, abyste získali přihlašovací údaje a pak nastavili podrobnosti konfigurace.

```powershell
$proxyCred = Get-Credential
Set-AksHciConfig -proxyServerHTTP "http://proxy.contoso.com:8888" -proxyServerHTTPS "http://proxy.contoso.com:8888" -proxyServerCredential $ProxyCred
```

## <a name="configure-an-aks-host-for-a-proxy-server-with-ntlmkerberos-authentication"></a>Konfigurace hostitele AKS pro proxy server s ověřováním pomocí protokolu NTLM/Kerberos

```powershell
$credential = Get-Credential # get the credential for the proxy server
Set-AksHciConfig -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerCredential $credential
```

### <a name="configure-an-aks-host-for-a-proxy-server-without-authentication"></a>Konfigurace hostitele AKS pro proxy server bez ověřování  

Pokud proxy server nevyžaduje ověřování, otevřete PowerShell jako správce a spusťte následující příkaz:

```powershell
Set-AksHciConfig -proxyServerHTTP "http://proxy.contoso.com:8888" -proxyServerHTTPS "http://proxy.contoso.com:8888"
```

### <a name="configure-an-aks-host-for-a-proxy-server-with-a-trusted-certificate"></a>Konfigurace hostitele AKS pro proxy server s důvěryhodným certifikátem

Pokud proxy server vyžaduje, aby klienti proxy důvěřovali certifikátu, určete soubor certifikátu při spuštění `Set-AksHciConfig` . Formát souboru certifikátu je *Base-64 kódovaný X. 509*. Umožní nám zřídit a důvěřovat certifikátu v rámci našeho zásobníku:

```powershell
Set-AksHciConfig -proxyServerHTTP "http://proxy.contoso.com:8888" -proxyServerHTTPS "http://proxy.contoso.com:8888" -proxyServerCertFile "C:\proxycertificate.crt"
```

> [!NOTE]
> Proxy certifikáty ještě nejsou zřízené nebo důvěryhodné na uzlech pracovních procesů Windows Kubernetes. Podpora pracovníků Windows bude povolená v budoucí verzi.


## <a name="exclude-specific-hosts-or-domains-from-using-the-proxy-server"></a>Vyloučení konkrétních hostitelů nebo domén z použití proxy server

Ve většině sítí budete muset vyloučit určité sítě, hostitele nebo domény, aby byly dostupné prostřednictvím proxy server. Můžete to provést tak, že vyloučíte řetězce adres pomocí `-proxyServerNoProxy` parametru v `Set-AksHciConfig` .

Výchozí hodnota pro `proxyServerNoProxy` je `localhost,127.0.0.1,.svc,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16`

Při spuštění tohoto příkazu se vylučují následující:

- Provoz místního hostitele (localhost, 127.0.0.1)
- Interní provoz služby Kubernetes (. svc), kde _. svc_ představuje zástupný název. To se podobá říká *. svc, ale* v tomto schématu se nepoužívá.
- Adresní prostor privátní sítě (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16). Všimněte si, že adresní prostor privátní sítě obsahuje důležité sítě, například Kubernetes Service CIDR (10.96.0.0/12) a Kubernetes POD CIDR (10.244.0.0/16).

I když budou tyto výchozí hodnoty fungovat pro mnoho sítí, možná budete muset do seznamu výjimek přidat další rozsahy podsítí nebo názvy. Můžete například chtít, aby se obor názvů vaší organizace (. contoso.com) vykázal prostřednictvím proxy serveru. Můžete to dosáhnout zadáním hodnot v seznamu proxyServerNoProxy:

```powershell
Set-AksHciConfig -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerNoProxy "localhost,127.0.0.1,.svc,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16,.contoso.com"
```

## <a name="next-steps"></a>Další kroky

- [Nasaďte aplikaci pro Linux do clusteru Kubernetes](./deploy-linux-application.md).
- [Nasaďte aplikaci pro Windows do clusteru Kubernetes](./deploy-windows-application.md).
