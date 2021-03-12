---
title: Otočit tajné klíče
titleSuffix: Azure Stack Hub
description: Naučte se, jak tyto tajné klíče otočit z centra Azure Stack.
author: BryanLa
ms.topic: how-to
ms.date: 03/09/2021
ms.reviewer: fiseraci
ms.author: bryanla
ms.lastreviewed: 01/19/2021
monikerRange: '>=azs-1803'
ms.openlocfilehash: d473301265c8b7c1c3b48386cfbc8f4986283a8e
ms.sourcegitcommit: e7d6f953e7014900b4e7d710340cfa98d253fce9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102637591"
---
# <a name="rotate-secrets-in-azure-stack-hub"></a>Obměna tajných klíčů ve službě Azure Stack Hub

V tomto článku najdete pokyny k provádění rotace tajných kódů, které vám pomůžou zajistit zabezpečenou komunikaci s prostředky infrastruktury Azure Stack a službami.

## <a name="overview"></a>Přehled

Azure Stack hub používá k údržbě zabezpečených komunikací s prostředky a službami infrastruktury tajné klíče. Aby bylo možné zachovat integritu infrastruktury centra Azure Stack, musí operátoři střídat tajné klíče v frekvencích, které jsou v souladu s požadavky na zabezpečení jejich organizace.

V případě blížících se k vypršení platnosti tajných klíčů se na portálu pro správu generují následující výstrahy. Dokončení rotace tajných klíčů vyřeší tyto výstrahy:

- Čekání na vypršení platnosti hesla účtu služby
- Blížící se vypršení platnosti interního certifikátu
- Blížící se vypršení platnosti externího certifikátu

> [!WARNING]
> Před vypršením platnosti se na portálu pro správu spouštějí 2 fáze výstrah:
> - 90 dní před vypršením platnosti výstražné výstrahy je vygenerováno upozornění.
> - 30 dní před vypršením platnosti je vygenerována kritická výstraha. 
>
> **Pokud tato oznámení obdržíte, je *důležité* , abyste dokončili přeměnu tajných klíčů. V takovém případě může dojít ke ztrátě zatížení a možné Azure Stack centra znovu nasazovat na vaše vlastní náklady!**

Další informace o monitorování a nápravě výstrah najdete [v tématu monitorování stavu a výstrah v centru Azure Stack](azure-stack-monitor-health.md).

::: moniker range="<azs-1811"  
> [!NOTE]
> Prostředí centra Azure Stack ve verzích starších než 1811 mohou zobrazovat výstrahy na nevyřízené interní certifikáty nebo vypršení platnosti tajného kódu. Tyto výstrahy jsou nepřesné a měly by být ignorovány bez spuštění interního tajného klíče. Nepřesné upozornění na vypršení platnosti interního tajného klíče je známý problém, který je vyřešený v 1811. Platnost interních tajných kódů nekončí, pokud prostředí není aktivní po dobu dvou let.
::: moniker-end

## <a name="prerequisites"></a>Požadavky

1. Důrazně doporučujeme, abyste spustili podporovanou verzi centra Azure Stack a měli byste použít nejnovější dostupnou opravu hotfix pro verzi centra Azure Stack, na které je instance spuštěná. Pokud například používáte 2008, ujistěte se, že máte nainstalovanou nejnovější opravu hotfix, která je k dispozici pro 2008.

    ::: moniker range="<azs-1811"  
    >[!IMPORTANT]
    > Pro verze starší než 1811:
    > - Je-li již provedeno střídání tajných klíčů, je nutné před opětovnou otočením v tajných klíčích aktualizovat na verzi 1811 nebo novější. Pomocí [privilegovaného koncového bodu](azure-stack-privileged-endpoint.md) se musí provádět rotace tajných klíčů a vyžaduje přihlašovací údaje operátora Azure Stack centra. Pokud si nejste jistí, jestli se ve vašem prostředí spustilo střídání tajných klíčů, aktualizujte na 1811.
    > - Pro přidání certifikátů hostitele rozšíření není nutné přetočit tajné klíče. Při přidávání certifikátů hostitele rozšíření byste měli postupovat podle pokynů v článku [Příprava hostitele rozšíření pro Azure Stack hub](azure-stack-extension-host-prepare.md) .
    ::: moniker-end

2. Upozorněte uživatele na plánované operace údržby. Naplánujte normální časová období údržby, co nejvíce, během nepracovních hodin. Operace údržby mohou ovlivnit úlohy uživatelů i operace portálu.

3. Při rotaci tajných kódů můžou operátory vyzradit otevřené a automaticky zavřené výstrahy. Jedná se o očekávané chování a tato upozornění můžete ignorovat. Operátoři můžou ověřit platnost těchto výstrah pomocí [rutiny PowerShellu test-AzureStack](azure-stack-diagnostic-test.md). Pro operátory, které používají System Center Operations Manager k monitorování systémů Azure Stack hub, se při umístění systému do režimu údržby zabrání tomu, aby tyto výstrahy dostaly do svých ITSM systémů, ale budou i nadále upozorňovány, pokud se systém Azure Stack hub stane nedostupným.

::: moniker range=">=azs-1811"
## <a name="rotate-external-secrets"></a>Otočení externích tajných klíčů

> [!Important]
> Rotace externího tajného klíče pro:
> - **Tajné kódy, jako jsou zabezpečené klíče a řetězce** , musí správce provést ručně. Sem patří hesla uživatele a účtu správce a [hesla přepínače sítě](azure-stack-customer-defined.md).
> - **Tajným klíčům s hodnotou přidat poskytovatele prostředků (RP)** se zabývá samostatné doprovodné materiály:
>    - [App Service v Azure Stack Hubu](app-service-rotate-certificates.md)
>    - [Event Hubs v Azure Stack Hubu](event-hubs-rp-rotate-secrets.md)
>    - [IoT Hub v Azure Stack Hubu](iot-hub-rp-rotate-secrets.md)
>    - [MySQL v centru Azure Stack](azure-stack-mysql-resource-provider-maintain.md#secrets-rotation)
>    - [SQL v centru Azure Stack](azure-stack-sql-resource-provider-maintain.md#secrets-rotation)
> - **Přihlašovací údaje řadiče pro správu základní desky (BMC)** jsou také ručním procesem [popsaným dále v tomto článku](#update-the-bmc-credential). 

Tato část popisuje rotaci certifikátů používaných k zabezpečení externích služeb. Tyto certifikáty poskytuje operátor centra Azure Stack pro následující služby:

- Portál správce
- Veřejný portál
- Azure Resource Manager správce
- Globální Azure Resource Manager
- Key Vault správce
- Key Vault
- Hostitel rozšíření Správce
- ACS (včetně objektů blob, Table a Queue Storage)
- SLUŽBOU<sup>*</sup>
- Zapisovací<sup>*</sup>

<sup>*</sup>Platí při používání služby Active Directory federovaných služeb (AD FS).

### <a name="preparation"></a>Příprava

Před otočením externích tajných klíčů:

1. Spuštěním **[`Test-AzureStack`](azure-stack-diagnostic-test.md)** rutiny PowerShellu s použitím `-group SecretRotationReadiness` parametru potvrďte, že všechny výstupy testů jsou v pořádku před otočením tajných kódů.
2. Příprava nové sady náhradních externích certifikátů:
   - Nová sada musí odpovídat specifikacím certifikátu, které jsou uvedené v [požadavcích na certifikát PKI centra Azure Stack](azure-stack-pki-certs.md). 
   - Vygenerujte žádost o podepsání certifikátu (CSR), která odešlete certifikační autoritě (CA). Postupujte podle kroků uvedených v části [generování žádostí o podepsání certifikátu](azure-stack-get-pki-certs.md) a jejich příprava pro použití ve vašem prostředí Azure Stackho centra pomocí postupu v části [Příprava certifikátů PKI](azure-stack-prepare-pki-certs.md). Centrum Azure Stack podporuje v následujících kontextech i rotaci tajných kódů externích certifikátů z nové certifikační autority (CA):

     |Otočení od certifikační autority|Otočit na CA|Podpora verze centra Azure Stack|
     |-----|-----|-----|-----|
     |Self-Signed|Enterprise| 1903 & později|
     |Self-Signed|Self-Signed|Nepodporuje se|
     |Self-Signed|Republik<sup>*</sup>|1803 & později|
     |Enterprise|Enterprise|1803 & později; 1803-1903, pokud je stejná certifikační autorita organizace použitá při nasazení|
     |Enterprise|Self-Signed|Nepodporuje se|
     |Enterprise|Republik<sup>*</sup>|1803 & později|
     |Republik<sup>*</sup>|Enterprise|1903 & později|
     |Republik<sup>*</sup>|Self-Signed|Nepodporuje se|
     |Republik<sup>*</sup>|Republik<sup>*</sup>|1803 & později|

     <sup>*</sup>Součást [důvěryhodného kořenového programu systému Windows](/security/trusted-root/participants-list).

   - Nezapomeňte ověřit certifikáty, které jste připravili s postupem popsaným v části [ověření certifikátů PKI](azure-stack-validate-pki-certs.md) .
   - Ujistěte se, že heslo neobsahuje žádné speciální znaky, jako je například `*` nebo `)` .
   - Ujistěte se, že šifrování PFX je **TripleDES-SHA1**. Pokud narazíte na problém, přečtěte si téma [řešení běžných potíží s certifikáty PKI služby Azure Stack hub](azure-stack-remediate-certs.md#pfx-encryption).

3. Uložte zálohu do certifikátů používaných k otočení v umístění zabezpečené zálohy. Pokud se vaše otočení spustí a pak se nepovede, nahraďte certifikáty ve sdílené složce záložními kopiemi a teprve potom znovu spusťte otočení. Uchovávejte záložní kopie v umístění zabezpečené zálohy.
4. Vytvořte sdílenou složku, ke které máte přístup z virtuálních počítačů s ERCS. Sdílená složka musí být čitelná a zapisovatelné pro **CloudAdmin** identitu.
5. Otevřete konzolu PowerShellu ISE z počítače, ke kterému máte přístup ke sdílené složce. Přejděte do sdílené složky, kde vytvoříte adresáře, kam chcete umístit své externí certifikáty.
6. Stáhněte si **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** do síťové sdílené složky a spusťte skript. Skript vytvoří strukturu složek, která bude vyhovovat **_.\Certificates\AAD_*_ nebo _*_.\Certificates\ADFS_*_, a to v závislosti na vašem poskytovateli identity. Vaše struktura složky musí začínat* složkou \\ certifikátů _** následovaný pouze složkou **\\ AAD** nebo **\\ ADFS** . Všechny zbývající podadresáře jsou obsaženy v předchozí struktuře. Například:
    - Sdílená složka = **\\\\\<IPAddress>\\\<ShareName>**
    - Kořenová složka certifikátu pro Azure AD Provider = **\\ Certificates\AAD**
    - Úplná cesta = **\\ \\ \<IPAddress> \\ \<ShareName> \Certificates\AAD**

    > [!IMPORTANT]
    > Při `Start-SecretRotation` pozdějším spuštění bude ověřena struktura složky. Struktura složek, která nedodržuje předpisy, vyvolá následující chybu:
    >
    > ```powershell
    > Cannot bind argument to parameter 'Path' because it is null.
    > + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
    > + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
    > + PSComputerName        : xxx.xxx.xxx.xxx
    > ```

7. Zkopírujte novou sadu náhradních externích certifikátů vytvořených v kroku #2 do adresáře **\Certificates \\ \<IdentityProvider>** vytvořeného v kroku #6. Nezapomeňte postupovat podle `cert.<regionName>.<externalFQDN>` formátu \<CertName\> . 

    Tady je příklad struktury složek pro zprostředkovatele identit Azure AD:
    ```powershell
        <ShareName>
            │
            └───Certificates
                  └───AAD
                      ├───ACSBlob
                      │       <CertName>.pfx
                      │
                      ├───ACSQueue
                      │       <CertName>.pfx
                      │
                      ├───ACSTable
                      │       <CertName>.pfx
                      │
                      ├───Admin Extension Host
                      │       <CertName>.pfx
                      │
                      ├───Admin Portal
                      │       <CertName>.pfx
                      │
                      ├───ARM Admin
                      │       <CertName>.pfx
                      │
                      ├───ARM Public
                      │       <CertName>.pfx
                      │
                      ├───KeyVault
                      │       <CertName>.pfx
                      │
                      ├───KeyVaultInternal
                      │       <CertName>.pfx
                      │
                      ├───Public Extension Host
                      │       <CertName>.pfx
                      │
                      └───Public Portal
                              <CertName>.pfx

    ```

### <a name="rotation"></a>Obměna

K otočení externích tajných kódů proveďte následující kroky:

1. K otočení tajných kódů použijte následující skript PowerShellu. Skript vyžaduje přístup k relaci s privilegovaným koncovým bodem (PEP). K PEP se přistupoval prostřednictvím vzdálené relace PowerShellu na virtuálním počítači, který hostuje PEP. Pokud používáte integrovaný systém, existují tři instance PEP, z nichž každý běží v rámci virtuálního počítače (předpona-ERCS01, prefix-ERCS02 nebo prefix-ERCS03) na různých hostitelích. Pokud používáte ASDK, tento virtuální počítač má název AzS-ERCS01. Aktualizujte `<placeholder>` hodnoty před spuštěním:

    ```powershell
    # Create a PEP Session
    winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
    $PEPCreds = Get-Credential
    $PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS_Machine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    # Run Secret Rotation
    $CertPassword = ConvertTo-SecureString "<Cert_Password>" -AsPlainText -Force
    $CertShareCreds = Get-Credential
    $CertSharePath = "<Network_Path_Of_CertShare>"
    Invoke-Command -Session $PEPSession -ScriptBlock {
        Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
    }
    Remove-PSSession -Session $PEPSession
    ```

    Skript provede následující kroky:

    - Vytvoří relaci PowerShellu s [privilegovaným koncovým bodem](azure-stack-privileged-endpoint.md) pomocí účtu **CloudAdmin** a uloží relaci jako proměnnou. Tato proměnná se používá jako parametr v dalším kroku. 

    - Spustí [příkaz Invoke-Command](/powershell/module/microsoft.powershell.core/Invoke-Command), který předá PROMĚNNOU relace PEP jako `-Session` parametr.

    - Spouští se `Start-SecretRotation` v relaci PEP pomocí následujících parametrů:
        - `-PfxFilesPath`: Dříve vytvořená síťová cesta k adresáři certifikátů.  
        - `-PathAccessCredential`: Objekt PSCredential pro přihlašovací údaje ke sdílené složce.
        - `-CertificatePassword`: Zabezpečený řetězec hesla použitého pro všechny vytvořené soubory certifikátů PFX.

2. Rotace externích tajných klíčů trvá přibližně jednu hodinu. Po úspěšném dokončení se v konzole zobrazí `ActionPlanInstanceID ... CurrentStatus: Completed` zpráva, za kterou následuje `DONE` . Odeberte certifikáty ze sdílené složky vytvořené v přípravné části a uložte je do svého zabezpečeného záložního umístění.

    > [!Note]
    > Pokud se rotace tajných klíčů nezdařila, postupujte podle pokynů v chybové zprávě a znovu spusťte `Start-SecretRotation` s `-ReRun` parametrem.
    >
    >```powershell
    >Start-SecretRotation -ReRun
    >```  
    >
    >Pokud se setkáte s opakovanými chybami při střídání tajných kódů, kontaktujte
::: moniker-end

## <a name="rotate-internal-secrets"></a>Otočení interních tajných kódů

Interní tajné klíče zahrnují certifikáty, hesla, zabezpečené řetězce a klíče, které používá infrastruktura centra Azure Stack, bez zásahu operátoru centra Azure Stack. Interní rotace tajných klíčů se vyžaduje jenom v případě, že máte podezření, že došlo k ohrožení zabezpečení nebo když jste dostali upozornění na vypršení platnosti. 
::: moniker range="<azs-1811"  
Nasazení z verze pre-1811 mohou zobrazovat výstrahy na nevyřízené interní certifikáty nebo vypršení platnosti tajného kódu. Tyto výstrahy jsou nepřesné a měly by se ignorovat a jedná se o známý problém vyřešený v 1811.
::: moniker-end

K otočení interních tajných kódů proveďte následující kroky:

1. Spusťte následující skript prostředí PowerShell. Při rotaci interního tajného klíče se v části "spustit rotace tajného klíče" používá pouze `-Internal` parametr [rutiny Start-SecretRotation](../reference/pep-2002/start-secretrotation.md):

    ```powershell
    # Create a PEP Session
    winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
    $PEPCreds = Get-Credential
    $PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS_Machine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    # Run Secret Rotation
    Invoke-Command -Session $PEPSession -ScriptBlock {
        Start-SecretRotation -Internal
    }
    Remove-PSSession -Session $PEPSession
    ```

    ::: moniker range="<azs-1811"
    > [!Note]
    > Verze starší než 1811 nevyžadují `-Internal` příznak. 
    ::: moniker-end


2. Po úspěšném dokončení se v konzole zobrazí `ActionPlanInstanceID ... CurrentStatus: Completed` zpráva, za kterou následuje `DONE` .

    > [!Note]
    > Pokud se rotace tajných klíčů nezdařila, postupujte podle pokynů v chybové zprávě a znovu spusťte `Start-SecretRotation` pomocí  `-Internal` `-ReRun` parametrů a.  
    >
    >```powershell
    >Start-SecretRotation -Internal -ReRun
    >```
    >
    > Pokud se setkáte s opakovanými chybami při střídání tajných kódů, kontaktujte

## <a name="update-the-bmc-credential"></a>Aktualizace přihlašovacích údajů řadiče pro správu základní desky

Řadič pro správu základní desky monitoruje fyzický stav vašich serverů. Pokyny k aktualizaci názvu uživatelského účtu a hesla řadiče pro správu základní desky najdete v tématu dodavatel hardwaru výrobce OEM.

>[!NOTE]
> Výrobce OEM může poskytovat další aplikace pro správu. Aktualizace uživatelského jména nebo hesla pro jiné aplikace pro správu nemá žádný vliv na uživatelské jméno nebo heslo řadiče pro správu základní desky. 

::: moniker range="<azs-1910"
1. Aktualizujte BMC na fyzických serverech centra Azure Stack podle pokynů výrobce OEM. Uživatelské jméno a heslo pro každý řadič pro správu základní desky ve vašem prostředí musí být stejné. Uživatelská jména řadiče pro správu základní desky nesmí být delší než 16 znaků.
::: moniker-end

::: moniker range=">=azs-1910"
1. Už není potřeba, abyste nejdřív aktualizovali přihlašovací údaje řadiče pro správu základní desky na fyzických serverech centra Azure Stack, a to podle pokynů výrobce OEM. Uživatelské jméno a heslo pro každý řadič pro správu základní desky ve vašem prostředí musí být stejné a nesmí mít víc než 16 znaků. 
::: moniker-end

2. Otevřete privilegovaný koncový bod v Azure Stack relace centra. Pokyny najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](azure-stack-privileged-endpoint.md). 

3. Po otevření privilegované relace koncového bodu spusťte jeden z následujících skriptů PowerShellu, který pomocí Invoke-Command spustí rutinu Set-BmcCredential. Pokud použijete volitelný parametr-BypassBMCUpdate s set-BMCCredential, přihlašovací údaje v řadiči pro správu základní desky se neaktualizují. Aktualizuje se jenom interní úložiště dat centra Azure Stack. Předat proměnnou vaší privilegované relace koncového bodu jako parametr.

    Tady je ukázkový skript PowerShellu, který zobrazí výzvu k zadání uživatelského jména a hesla: 

    ```powershell
    # Interactive Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPCreds = Get-Credential "<Domain>\CloudAdmin" -Message "PEP Credentials"
    $NewBmcPwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString
    $NewBmcUser = Read-Host -Prompt "Enter New BMC user name"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

    Uživatelské jméno a heslo můžete také kódovat v proměnných, které mohou být méně bezpečné:

    ```powershell
    # Static Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEPPwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PEPCreds = New-Object System.Management.Automation.PSCredential ($PEPUser, $PEPPwd)
    $NewBmcPwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force
    $NewBmcUser = "<New BMC User name>"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="reference-start-secretrotation-cmdlet"></a>Reference: Start-SecretRotation – rutina

[Rutina Start-SecretRotation](../reference/pep-2002/start-secretrotation.md) otočí tajné klíče infrastruktury Azure Stackho centrálního systému. Tuto rutinu je možné provést jenom proti PEP koncovému bodu s privilegovaným centrem Azure Stack pomocí  `Invoke-Command` bloku skriptu, který v parametru předává relaci `-Session` . Ve výchozím nastavení otočí jenom certifikáty všech koncových bodů infrastruktury externích sítí.

| Parametr | Typ | Vyžadováno | Pozice | Výchozí | Description |
|--|--|--|--|--|--|
| `PfxFilesPath` | Řetězec  | Ne  | Jmenovanou  | Žádné  | Cesta ke sdílené složce adresáře **\Certificates** obsahující všechny certifikáty koncového bodu externí sítě. Vyžaduje se pouze při otáčení externích tajných klíčů. Koncový adresář musí být **\Certificates**. |
| `CertificatePassword` | SecureString | Ne  | Jmenovanou  | Žádné  | Heslo pro všechny certifikáty, které jsou k dispozici v-PfXFilesPath. Požadovaná hodnota, pokud je k dispozici PfxFilesPath při otočení externích tajných klíčů. |
| `Internal` | Řetězec | Ne | Jmenovanou | Žádné | Vnitřní příznak musí být použit v případě, že operátor centra Azure Stack chce otočit tajné tajné klíče interní infrastruktury. |
| `PathAccessCredential` | PSCredential | Ne  | Jmenovanou  | Žádné  | Přihlašovací údaje PowerShellu pro sdílenou složku adresáře **\Certificates** obsahující všechny certifikáty koncového bodu externí sítě. Vyžaduje se pouze při otáčení externích tajných klíčů.  |
| `ReRun` | Přepínací parametr | Ne  | Jmenovanou  | Žádné  | Po neúspěšném pokusu se musí použít neustále se střídání tajných klíčů. |

### <a name="syntax"></a>Syntax

#### <a name="for-external-secret-rotation"></a>Pro rotaci externích tajných klíčů

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential <PSCredential>] [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>Pro otočení interního tajného klíče

```powershell
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>Opětovné spuštění pro vyhledání externích tajných klíčů

```powershell
Start-SecretRotation [-ReRun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>Pro opětovné spuštění rotace interního tajného klíče

```powershell
Start-SecretRotation [-ReRun] [-Internal]
```

### <a name="examples"></a>Příklady

#### <a name="rotate-only-internal-infrastructure-secrets"></a>Otočit jenom tajná klíčová tajemství interní infrastruktury

Tento příkaz musí běžet prostřednictvím [privilegovaného koncového bodu prostředí](azure-stack-privileged-endpoint.md)Azure Stack hub.

```powershell
PS C:\> Start-SecretRotation -Internal
```

Tento příkaz otočí všechny tajné klíče infrastruktury vystavené interní síti centra Azure Stack.

#### <a name="rotate-only-external-infrastructure-secrets"></a>Otočení jenom externích tajných kódů infrastruktury  

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {  
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

Tento příkaz otočí certifikáty TLS používané k externím koncovým bodům infrastruktury sítě Azure Stack hub.

::: moniker range="<azs-1811"
#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>Otočení interních a externích tajných kódů infrastruktury (jenom **před 1811** )

> [!IMPORTANT]
> Tento příkaz platí jenom pro Azure Stack centra **1811** , protože rotace je rozdělená na interní a externí certifikáty.
>
> **Od *1811 +* už nemůžete otočit interní i externí certifikát!**

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

Tento příkaz otočí tajné klíče infrastruktury vystavené interní síti centra Azure Stack a certifikáty TLS používané pro koncové body infrastruktury externích sítí centra Azure Stack. Start-SecretRotation otočí všechny tajné klíče generované zásobníkem a protože jsou k dispozici nějaké certifikáty, budou se také otáčet certifikáty externích koncových bodů.  
::: moniker-end

## <a name="next-steps"></a>Další kroky

[Další informace o zabezpečení centra Azure Stack](azure-stack-security-foundations.md)
