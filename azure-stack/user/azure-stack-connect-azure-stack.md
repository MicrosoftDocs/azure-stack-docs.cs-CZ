---
title: Připojení k centru Azure Stack
description: Naučte se připojit centrum Azure Stack.
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: 0a3dfcc7d12baa6616f57908e87d24afaf1e927e
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79294602"
---
# <a name="connect-to-azure-stack-hub"></a>Připojení k centru Azure Stack

Chcete-li spravovat prostředky, je nutné se připojit k Azure Stack Development Kit. Tento článek podrobně popisuje kroky potřebné pro připojení k vývojové sadě. Můžete použít kteroukoli z následujících možností připojení:

* Vzdálená plocha: umožňuje, aby se jeden souběžný uživatel rychle připojil z vývojové sady.
* Virtuální privátní síť (VPN): umožňuje více souběžným uživatelům připojit se od klientů mimo infrastrukturu centra Azure Stack (vyžaduje konfiguraci).

## <a name="connect-to-azure-stack-hub-with-remote-desktop"></a>Připojení k centru Azure Stack pomocí vzdálené plochy
S Připojení ke vzdálené ploše může jeden souběžný uživatel pracovat s portálem a spravovat prostředky.

1. Otevřete Připojení ke vzdálené ploše a připojte se k vývojové sadě. Jako uživatelské jméno zadejte **AzureStack\AzureStackAdmin** a heslo správce, které jste zadali během Azure Stack nastavení centra.  

2. V počítači vývojové sady otevřete Správce serveru, klikněte na **místní server**, vypněte rozšířené zabezpečení aplikace Internet Explorer a potom zavřete Správce serveru.

3. Portál otevřete tak, že přejdete na adresu (https://portal.local.azurestack.external/) a přihlásíte se pomocí přihlašovacích údajů uživatele).


## <a name="connect-to-azure-stack-hub-with-vpn"></a>Připojení k centru Azure Stack pomocí sítě VPN

K Azure Stack Development Kit můžete vytvořit připojení VPN typu dělené tunelové propojení. Prostřednictvím připojení VPN získáte přístup k portálu pro správu, uživatelskému portálu a místně nainstalovaným nástrojům, jako je Visual Studio a PowerShell, ke správě prostředků Azure Stack hub. Připojení VPN se podporuje v nasazeních založeném na Azure Active Directory (Azure AD) i v Active Directory Federation Services (AD FS) (AD FS). Připojení VPN umožňují, aby se několik klientů připojovalo k rozbočovači Azure Stack ve stejnou dobu. 

> [!NOTE] 
> Toto připojení VPN neposkytuje připojení k virtuálním počítačům infrastruktury služby Azure Stack hub. 

### <a name="prerequisites"></a>Předpoklady

* Nainstalujte [Azure PowerShell centra Azure Stack kompatibilní](../operator/azure-stack-powershell-install.md) na místním počítači.  
* Stáhněte si [nástroje, které jsou potřeba pro práci s rozbočovačem Azure Stack](../operator/azure-stack-powershell-download.md). 

### <a name="configure-vpn-connectivity"></a>Konfigurace připojení k síti VPN

Pokud chcete vytvořit připojení VPN k vývojové sadě, otevřete relaci PowerShellu se zvýšenými oprávněními z místního počítače se systémem Windows a spusťte následující skript (nezapomeňte aktualizovat IP adresu a heslo pro vaše prostředí):

```powershell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer's host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the IP address and password values for your environment. 

$hostIP = "<Azure Stack Hub host IP address>"

$Password = ConvertTo-SecureString `
  "<Administrator password provided when deploying Azure Stack Hub>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Pokud je nastavení úspěšné, zobrazí se `azurestack` v seznamu připojení VPN.

![Síťová připojení](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack-hub"></a>Připojení k centru Azure Stack

Připojte se k instanci centra Azure Stack pomocí některé z následujících dvou metod:  

* Pomocí příkazu `Connect-AzsVpn`: 
    
  ```powershell
  Connect-AzsVpn `
    -Password $Password
  ```

  Po zobrazení výzvy Důvěřujte hostiteli centra Azure Stack a nainstalujte certifikát z **AzureStackCertificateAuthority** do úložiště certifikátů místního počítače. Výzva se může zobrazit za oknem relace PowerShellu. 

* V místním počítači přejít na **nastavení sítě** > **VPN** > vyberte `azurestack` > **připojit**. Do příkazového řádku pro přihlášení zadejte uživatelské jméno (AzureStack\AzureStackAdmin) a heslo.

### <a name="test-the-vpn-connectivity"></a>Testování připojení VPN

Pokud chcete otestovat připojení k portálu, otevřete prohlížeč a navštivte Portál User Portal (https://portal.local.azurestack.external/), přihlaste se a pak vytvořte prostředky.  

## <a name="next-steps"></a>Další kroky



