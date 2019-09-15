---
title: Připojte se k ASDK | Microsoft Docs
description: Přečtěte si, jak se připojit k Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: knithinc
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 8a9fae5324e20ecd808df1f73dc90555b4802da5
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974605"
---
# <a name="connect-to-the-asdk"></a>Připojení k ASDK

Pokud chcete spravovat prostředky, musíte se nejdřív připojit k Azure Stack Development Kit (ASDK). V tomto článku popisujeme kroky, pomocí kterých se můžete připojit k ASDK, a to pomocí následujících možností připojení:

* [Připojení ke vzdálené ploše (RDP)](#connect-with-rdp): Když se připojíte pomocí Připojení ke vzdálené ploše, může se jeden uživatel rychle připojit k ASDK.
* [Virtuální privátní síť (VPN)](#connect-with-vpn): Když se připojujete pomocí sítě VPN, může se souběžně připojit k Azure Stack portálům z klientů mimo Azure Stack infrastrukturu. Připojení k síti VPN vyžaduje některé nastavení.

<a name="connect-with-rdp"></a>
## <a name="connect-to-azure-stack-using-rdp"></a>Připojení k Azure Stack pomocí protokolu RDP

Jeden souběžný uživatel může spravovat prostředky na portálu pro správu Azure Stack nebo na portálu User Portal prostřednictvím Připojení ke vzdálené ploše přímo z hostitelského počítače ASDK.

> [!TIP]
> Tato možnost také umožňuje znovu použít protokol RDP při přihlášení k hostitelskému počítači ASDK pro přihlášení k virtuálním počítačům vytvořeným v hostitelském počítači ASDK.

1. Otevřete Připojení ke vzdálené ploše (mstc. exe) a připojte se k IP adrese hostitelského počítače ASDK. Ujistěte se, že používáte účet autorizovaný pro vzdálené přihlášení k hostitelskému počítači ASDK. Ve výchozím nastavení má **AzureStack\AzureStackAdmin** oprávnění ke vzdálenému přístupu k hostitelskému počítači ASDK.  

2. Na hostitelském počítači s ASDK otevřete Správce serveru (ServerManager. exe). Vyberte **místní server**, vypněte **konfiguraci rozšířeného zabezpečení aplikace Internet explorer**a zavřete Správce serveru.

3. Přihlaste se k portálu pro správu jako **AzureStack\CloudAdmin** nebo použijte jiné přihlašovací údaje operátora Azure Stack. Adresa portálu správce ASDK je [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).

4. Přihlaste se k portálu User Portal jako **AzureStack\CloudAdmin** nebo použijte jiné přihlašovací údaje uživatele Azure Stack. Adresa uživatelského portálu ASDK je [https://portal.local.azurestack.external](https://portal.local.azurestack.external).

> [!NOTE]
> Další informace o tom, kdy použít tento účet, najdete v tématu [základy správy ASDK](asdk-admin-basics.md#what-account-should-i-use).

<a name="connect-with-vpn"></a>
## <a name="connect-to-azure-stack-using-vpn"></a>Připojení k Azure Stack pomocí sítě VPN

Pro přístup k Azure Stack portálům a místně nainstalovaným nástrojům, jako je Visual Studio a PowerShell, můžete vytvořit připojení VPN typu dělené tunelové propojení k hostitelskému počítači s ASDK. Pomocí připojení VPN se může současně připojit více uživatelů, aby se Azure Stack prostředky hostované službou ASDK.

Připojení VPN se podporuje pro nasazení Azure AD i Active Directory Federation Services (AD FS) (AD FS).

> [!NOTE]
> Připojení VPN neposkytuje připojení k Azure Stack virtuálním počítačům. Při připojení přes VPN nebudete moct do Azure Stack virtuálních počítačů připojit protokol RDP.

### <a name="prerequisites"></a>Požadavky
Před nastavením připojení VPN k ASDK se ujistěte, že jste splnili následující požadavky:

- Instalace [Azure PowerShell kompatibilního s Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell) na místním počítači.  
- Ve službě [Azure Stack development Kit by měl být blobEndpoint](asdk-post-deploy.md#download-the-azure-stack-tools) .

### <a name="set-up-vpn-connectivity"></a>Nastavení připojení VPN

Pokud chcete vytvořit připojení VPN k ASDK, otevřete PowerShell jako správce na místním počítači se systémem Windows. Pak spusťte následující skript (aktualizujte hodnoty IP adresy a hesla pro vaše prostředí):

```powershell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the ASDK host computer's IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Pokud je instalace úspěšná, **Azure Stack** se zobrazí v seznamu připojení VPN:

![Síťová připojení](media/asdk-connect/vpn.png)  

### <a name="connect-to-azure-stack"></a>Připojení ke službě Azure Stack

  Připojte se k instanci Azure Stack pomocí jedné z následujících metod:  

  * `Connect-AzsVpn` Použijte příkaz:
      
    ```powershell
    Connect-AzsVpn `
      -Password $Password
    ```

  * V místním počítači vyberte **Nastavení** > sítě**VPN** > **Azure Stack** > **připojit**. Do příkazového řádku pro přihlášení zadejte uživatelské jméno (**AzureStack\AzureStackAdmin**) a heslo.

Při prvním připojení budete vyzváni k instalaci Azure Stack kořenového certifikátu z **AzureStackCertificateAuthority** do úložiště certifikátů v místním počítači. Tento krok přidá ASDK certifikační autoritu (CA) do seznamu důvěryhodných hostitelů. Certifikát nainstalujete kliknutím na **Ano** .

![Kořenový certifikát](media/asdk-connect/cert.png)  
  
  > [!IMPORTANT]
  > Výzva může být skrytá v okně PowerShellu nebo v jiných aplikacích.

### <a name="test-vpn-connectivity"></a>Testovat připojení VPN

Chcete-li otestovat připojení k portálu, otevřete prohlížeč a pak klikněte na portál User Portal (https://portal.local.azurestack.external/) nebo na https://adminportal.local.azurestack.external/) portál pro správu portálu.

Přihlaste se pomocí příslušných přihlašovacích údajů předplatného pro vytváření a správu prostředků.  

## <a name="next-steps"></a>Další postup

[Odstraňování potíží](asdk-troubleshooting.md)
