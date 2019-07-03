---
title: Zpráva k vydání verze Microsoft Azure Stack Development Kit | Dokumentace Microsoftu
description: Vylepšení, oprav a známých problémů pro Azure Stack Development Kit.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 06/28/2019
ms.openlocfilehash: ba3ad4bf5e5d7f76d5d29e7967944be72e989c27
ms.sourcegitcommit: 068350a79805366e7e6536fb7df85a412bd0be99
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67511288"
---
# <a name="asdk-release-notes"></a>Zpráva k vydání verze ASDK

Tento článek obsahuje informace o změnách, opravy a známé problémy v Azure Stack Development Kit (ASDK). Pokud si nejste jistí, kterou verzi používáte, můžete si [použití portálu ke kontrole](../operator/azure-stack-updates.md#determine-the-current-version).

Udržujte si s tím, co se přihlásíte k odběru je novinkou ASDK [ ![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [informačního kanálu RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11906030"></a>Sestavení 1.1906.0.30

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v tématu [v této části](../operator/azure-stack-release-notes-1906.md#whats-in-this-update) poznámky k verzi služby Azure Stack.

### <a name="changes"></a>Změny

- Přidá **AzS-SRNG01** podporují aktualizačního kanálu virtuálního počítače hostující službu shromažďování protokolů pro službu Azure Stack. Další informace najdete v tématu [role virtuálních počítačů](asdk-architecture.md).

### <a name="fixed-and-known-issues"></a>Oprava a známé problémy

- Při vytváření prostředků virtuálního počítače pomocí Image na webu Marketplace, nemusí být možné dokončit nasazení. Jako alternativní řešení, můžete kliknout na **stáhnout šablonu a parametry** odkaz v **Souhrn** stránky a klikněte na **nasadit** tlačítko **šablony**  okno. 
- Seznam Azure Stack chyby opravené v této verzi najdete v tématu [v této části](../operator/azure-stack-release-notes-1906.md#fixes) poznámky k verzi služby Azure Stack.
- Seznam známých problémů najdete v tématu [v tomto článku](../operator/azure-stack-release-notes-known-issues-1906.md).
- Všimněte si, že [dostupných oprav hotfix Azure Stack](../operator/azure-stack-release-notes-1906.md#hotfixes) se nevztahují na Azure Stack ASDK.

## <a name="build-11905040"></a>Sestavení 1.1905.0.40

<!-- ### Changes -->

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v tématu [v této části](../operator/azure-stack-release-notes-1905.md#whats-in-this-update) poznámky k verzi služby Azure Stack.

### <a name="fixed-and-known-issues"></a>Oprava a známé problémy

- Opravili jsme problém, ve kterém jste měli upravit **RegisterWithAzure.psm1** skript prostředí PowerShell, aby se dalo [zaregistrovat ASDK](asdk-register.md) úspěšně.
- Seznam dalších problémů služby Azure Stack, opravené v této verzi najdete v tématu [v této části](../operator/azure-stack-release-notes-1905.md#fixes) poznámky k verzi služby Azure Stack.
- Seznam známých problémů najdete v tématu [v tomto článku](../operator/azure-stack-release-notes-known-issues-1905.md).
- Všimněte si, že [dostupných oprav hotfix Azure Stack](../operator/azure-stack-release-notes-1905.md#hotfixes) se nevztahují na Azure Stack ASDK.

## <a name="build-11904036"></a>Sestavení 1.1904.0.36

<!-- ### Changes -->

### <a name="new-features"></a>Nové funkce

- Seznam nových funkcí v této verzi najdete v tématu [v této části](../operator/azure-stack-release-notes-1904.md#whats-in-this-update) poznámky k verzi služby Azure Stack.

### <a name="fixed-and-known-issues"></a>Oprava a známé problémy

- Z důvodu služba hlavní časový limit při spuštění skriptu registraci za účelem [zaregistrovat ASDK](asdk-register.md) úspěšně musí upravit **RegisterWithAzure.psm1** skript prostředí PowerShell. Udělejte toto:

  1. Na hostitelském počítači ASDK, otevřete soubor **C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1** v editoru se zvýšenými oprávněními.
  2. Na řádku 1249, přidejte `-TimeoutInSeconds 1800` parametr na konci. To se vyžaduje kvůli časového limitu služby instančního objektu při spuštění skriptu registrace. Řádek 1249 by měl nyní vypadat následovně:

     ```powershell
      $servicePrincipal = Invoke-Command -Session $PSSession -ScriptBlock { New-AzureBridgeServicePrincipal -RefreshToken $using:RefreshToken -AzureEnvironment $using:AzureEnvironmentName -TenantId $using:TenantId -TimeoutInSeconds 1800 }
      ```

- Opravili jsme chybu připojení VPN identifikovat [zde ve verzi 1902](#known-issues).

- Seznam dalších problémů služby Azure Stack, opravené v této verzi najdete v tématu [v této části](../operator/azure-stack-release-notes-1904.md#fixes) poznámky k verzi služby Azure Stack.
- Seznam známých problémů najdete v tématu [v tomto článku](../operator/azure-stack-release-notes-known-issues-1904.md).
- Všimněte si, že [dostupných oprav hotfix Azure Stack](../operator/azure-stack-release-notes-1904.md#hotfixes) se nevztahují na Azure Stack ASDK.

## <a name="build-1903"></a>Build 1903

Datová část 1903 nezahrnuje o ASDK verzi.

### <a name="known-issues"></a>Známé problémy

- Se vyskytl problém s připojením VPN z jiného hostitele do ASDK pomocí postupu v [v tomto článku](asdk-connect.md). Při pokusu o připojení z klienta VPN k prostředí ASDK, zobrazí se chyba, která **uživatelské jméno nebo heslo není správné**, i v případě, že jste si jisti, máte použít správný účet a heslo zadali správně. Problém je, ne pomocí přihlašovacích údajů, ale s změnu ověřování protokol použitý pro připojení VPN ASDK. Chcete-li tento problém obejít, postupujte následovně:

   Nejprve proveďte změny použít na straně serveru ASDK ověřovací protokol:

   1. Připojení RDP k ASDK hostitele.
   2. Otevřete se zvýšenými oprávněními relaci Powershellu, přihlášení jako AzureStack\AzureStackAdmin pomocí hesla, které jste zadali v době nasazování.
   3. Spusťte následující příkazy:

      ```powershell
      netsh nps set np name = "Connections to Microsoft Routing and Remote Access server" profileid = "0x100a" profiledata = "1A000000000000000000000000000000" profileid = "0x1009" profiledata = "0x5"
      restart-service remoteaccess -force
      ```

   Dále upravte skript na straně klienta připojení. Nejjednodušší způsob, jak to provést, je provádět změny přímo do modulu skriptu C:\AzureStack-Tools-master\connect\azurestack.connect.psm1:

   1. Upravit **přidat AzsVpnConnection** rutiny změnit `AuthenticationMethod` parametr z `MsChapv2` k `EAP`:

      ```powershell
      $connection = Add-VpnConnection -Name $ConnectionName -ServerAddress $ServerAddress -TunnelType L2tp -EncryptionLevel Required -AuthenticationMethod Eap -L2tpPsk $PlainPassword -Force -RememberCredential -PassThru -SplitTunneling
      ```

   2. Změnit **připojit AzsVpn** rutiny pomocí `rasdial @ConnectionName $User $PlainPassword` pomocí `rasphone`, jak EAP vyžaduje interaktivní přihlašování:

      ```powershell
      rasphone $ConnectionName
      ```

   3. Uložte změny a pak znovu naimportovat **azurestack.connect.psm1** modulu.
   4. Postupujte podle pokynů v [v tomto článku](asdk-connect.md#set-up-vpn-connectivity).
   5. Když se připojíte k ASDK přes síť VPN, připojení tak, že přejdete Windows **síť a Internet nastavení**, pak **VPN**, místo připojování k zajištění, že se výzva k zadání na hlavním panelu přihlašovací údaje.

- Byl zjištěn problém, ve kterém se zahodí pakety více než 1450 bajtů pro interní zatížení nástroje pro vyrovnávání (ILB). Je problém způsobený nastavení jednotek MTU na hostiteli je příliš nízká tak, aby vyhovovaly VXLAN zapouzdřené pakety, které procházejí roli, která od 1901 byl přesunut do hostitele. Existují aspoň dva scénáře, které můžete setkat, ve kterých se objevuje tento problém projevit:

  - Dotazy SQL pro SQL Always On, který je za interní zatížení nástroje pro vyrovnávání (ILB) a více než 660 bajtů.
  - Nasazení Kubernetes nezdaří, pokud se pokusíte povolit více hlavních serverů.  

  Tento problém nastane, pokud máte komunikace mezi virtuálním Počítačem a ILB ve stejné virtuální síti, ale v různých podsítích. Tento problém můžete vyřešit spuštěním následujících příkazů v příkazovém řádku se zvýšenými oprávněními na hostiteli ASDK:

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```
