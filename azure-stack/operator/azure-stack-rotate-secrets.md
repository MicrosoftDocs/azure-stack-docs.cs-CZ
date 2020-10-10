---
title: Otočit tajné klíče
titleSuffix: Azure Stack Hub
description: Naučte se, jak tyto tajné klíče otočit z centra Azure Stack.
author: BryanLa
ms.topic: how-to
ms.date: 06/29/2020
ms.reviewer: ppacent
ms.author: bryanla
ms.lastreviewed: 08/15/2020
monikerRange: '>=azs-1803'
ms.openlocfilehash: aca163df1026193933ffb9d09dbdf4a854638a75
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91899801"
---
# <a name="rotate-secrets-in-azure-stack-hub"></a>Otočení tajných kódů v centru Azure Stack

*Tyto pokyny platí jenom pro Azure Stack integrované systémy centra verze 1803 a novější. Nepokusit se o otočení tajného kódu u verzí starších než 1803*

V tomto článku najdete pokyny a skript PowerShellu pro rotaci tajných klíčů, které vám pomůžou zajistit zabezpečenou komunikaci s prostředky a službami infrastruktury centra Azure Stack. 

## <a name="overview"></a>Přehled

Azure Stack hub používá k údržbě zabezpečených komunikací s prostředky a službami infrastruktury tajné klíče. Aby bylo možné zachovat integritu infrastruktury centra Azure Stack, musí operátoři střídat tajné klíče v frekvencích, které jsou v souladu s požadavky na zabezpečení jejich organizace.

### <a name="internal-vs-external-secrets"></a>Interní vs externí tajné klíče

Od verze 1811 jsou pro interní a externí certifikáty oddělená tajná rotace:

- **Interní tajné klíče**: certifikáty, hesla, zabezpečené řetězce a klíče používané infrastrukturou centra Azure Stack bez zásahu operátoru centra Azure Stack.

- **Externí tajná klíč**: certifikáty služby infrastruktury pro externí služby, které poskytuje operátor centra Azure Stack. Externí tajné klíče obsahují certifikáty pro následující služby:

    - Portál správce
    - Veřejný portál
    - Azure Resource Manager správce
    - Globální Azure Resource Manager
    - Key Vault správce
    - Key Vault
    - Hostitel rozšíření Správce
    - ACS (včetně objektů blob, Table a Queue Storage)
    - SLUŽBOU
    - Zapisovací
    
    \* Platí jenom v případě, že poskytovatel identity prostředí je ve službě Active Directory federované služby (AD FS).

> [!Important]
> Všechny ostatní zabezpečené klíče a řetězce jsou ručně aktualizovány správcem. To zahrnuje hesla účtu uživatele a správce, [hesla síťového přepínače a oprávnění](azure-stack-customer-defined.md)a přihlašovací údaje řadiče pro správu základní desky (BMC), které jsou [pokryté dále v tomto článku](#update-the-bmc-credential). 
>
>Tento článek navíc neřeší rotaci tajných kódů pro poskytovatele prostředků s hodnotou přidat. Pokud chcete tyto tajné kódy otočit, přečtěte si následující články:
>
> - [Obměna tajných klíčů a certifikátů služby App Service ve službě Azure Stack Hub](app-service-rotate-certificates.md)
> - [Poskytovatel prostředků MySQL – střídání tajných kódů](azure-stack-mysql-resource-provider-maintain.md#secrets-rotation)
> - [Poskytovatel prostředků SQL – střídání tajných kódů](azure-stack-sql-resource-provider-maintain.md#secrets-rotation)

### <a name="expiration-alerts"></a>Výstrahy vypršení platnosti

Když jsou tajná data do 30 dnů od vypršení platnosti, vygenerují se na portálu pro správu následující výstrahy:

- Čekání na vypršení platnosti hesla účtu služby
- Čeká se na vypršení platnosti interního certifikátu.
- Blížící se vypršení platnosti externího certifikátu

Při dokončení kroků střídání tajného klíče v následujících částech se tyto výstrahy vyřeší.

> [!Note]
> Prostředí centra Azure Stack ve verzích starších než 1811 mohou zobrazovat výstrahy na nevyřízené interní certifikáty nebo vypršení platnosti tajného kódu. Tyto výstrahy jsou nepřesné a měly by být ignorovány bez spuštění interního tajného klíče. Nepřesné upozornění na vypršení platnosti interního tajného klíče je známý problém, který je vyřešený v 1811. Platnost interních tajných kódů nekončí, pokud prostředí není aktivní po dobu dvou let.

### <a name="external-certificates-from-a-new-certificate-authority"></a>Externí certifikáty od nové certifikační autority

Centrum Azure Stack podporuje v následujících kontextech i střídání tajných kódů pomocí externích certifikátů z nové certifikační autority (CA):

|Nainstalovala se certifikační autorita.|CA pro otočení|Podporováno|Podporované verze centra Azure Stack|
|-----|-----|-----|-----|
|Z Self-Signed|Do Enterprise|Podporováno|1903 & později|
|Z Self-Signed|Pro Self-Signed|Nepodporuje se||
|Z Self-Signed|Na veřejné<sup>*</sup>|Podporováno|1803 & později|
|Z Enterprise|Do Enterprise|Podporuje se. Od 1803-1903: podporované, pokud zákazníci používají stejnou certifikační autoritu organizace jako při nasazení.|1803 & později|
|Z Enterprise|Pro Self-Signed|Nepodporuje se||
|Z Enterprise|Na veřejné<sup>*</sup>|Podporováno|1803 & později|
|Z veřejného<sup>*</sup>|Do Enterprise|Podporováno|1903 & později|
|Z veřejného<sup>*</sup>|Pro Self-Signed|Nepodporuje se||
|Z veřejného<sup>*</sup>|Na veřejné<sup>*</sup>|Podporováno|1803 & později|

<sup>*</sup>Označuje, že veřejné certifikační autority jsou součástí důvěryhodného kořenového programu systému Windows. Úplný seznam [účastníků – důvěryhodných kořenových programů společnosti Microsoft](/security/trusted-root/participants-list)najdete v seznamu.

## <a name="prerequisites"></a>Požadavky

Pro rotaci interních a externích tajných kódů:

1. Důrazně doporučujeme, abyste instanci centra Azure Stack napřed aktualizovali na nejnovější verzi.

    ::: moniker range="<azs-1811"  
    >[!IMPORTANT]
    > Pro verze starší než 1811:
    > - Je-li již provedeno střídání tajných klíčů, je nutné před opětovnou otočením v tajných klíčích aktualizovat na verzi 1811 nebo novější. Pomocí [privilegovaného koncového bodu](azure-stack-privileged-endpoint.md) se musí provádět rotace tajných klíčů a vyžaduje přihlašovací údaje operátora Azure Stack centra. Pokud si nejste jistí, jestli se ve vašem prostředí spustilo střídání tajných klíčů, aktualizujte na 1811.
    > - Pro přidání certifikátů hostitele rozšíření není nutné přetočit tajné klíče. Při přidávání certifikátů hostitele rozšíření byste měli postupovat podle pokynů v článku [Příprava hostitele rozšíření pro Azure Stack hub](azure-stack-extension-host-prepare.md) .
    ::: moniker-end

2. Upozorněte uživatele na plánované operace údržby. Naplánujte normální časová období údržby, co nejvíce, během nepracovních hodin. Operace údržby mohou ovlivnit úlohy uživatelů i operace portálu.

3. Při rotaci tajných kódů můžou operátory vyzradit otevřené a automaticky zavřené výstrahy. Jedná se o očekávané chování a tato upozornění můžete ignorovat. Operátoři můžou ověřit platnost těchto výstrah pomocí [rutiny PowerShellu test-AzureStack](azure-stack-diagnostic-test.md). Pro operátory, které používají System Center Operations Manager k monitorování systémů Azure Stack hub, se při umístění systému do režimu údržby zabrání tomu, aby tyto výstrahy dostaly do svých ITSM systémů, ale budou i nadále upozorňovány, pokud se systém Azure Stack hub stane nedostupným.

Pro rotaci externích tajných klíčů dokončete tyto další požadavky:

1. Spuštěním **[`Test-AzureStack`](azure-stack-diagnostic-test.md)** rutiny PowerShellu s použitím `-group SecretRotationReadiness` parametru potvrďte, že všechny výstupy testů jsou v pořádku před otočením tajných kódů.
2. Příprava nové sady náhradních externích certifikátů:
    - Nová sada musí odpovídat specifikacím certifikátu, které jsou uvedené v [požadavcích na certifikát PKI centra Azure Stack](azure-stack-pki-certs.md). 
    - Můžete vygenerovat žádost o podepsání certifikátu (CSR) k odeslání do certifikační autority (CA) pomocí postupu uvedeného v části [generování žádostí o podepsání certifikátu](azure-stack-get-pki-certs.md) a jejich příprava pro použití ve vašem prostředí Azure Stackho centra pomocí postupu v části [Příprava certifikátů PKI](azure-stack-prepare-pki-certs.md). 
    - Nezapomeňte ověřit certifikáty, které jste připravili s postupem popsaným v části [ověření certifikátů PKI](azure-stack-validate-pki-certs.md) .
    - Ujistěte se, že heslo neobsahuje žádné speciální znaky, jako je například `*` nebo `)` .
    - Ujistěte se, že šifrování PFX je **TripleDES-SHA1**. Pokud narazíte na problém, přečtěte si téma [řešení běžných potíží s certifikáty PKI služby Azure Stack hub](azure-stack-remediate-certs.md#pfx-encryption).
3. Uložte zálohu do certifikátů používaných k otočení v umístění zabezpečené zálohy. Pokud se vaše otočení spustí a pak se nepovede, nahraďte certifikáty ve sdílené složce záložními kopiemi a teprve potom znovu spusťte otočení. Uchovávejte záložní kopie v umístění zabezpečené zálohy.
4. Vytvořte sdílenou složku, ke které máte přístup z virtuálních počítačů s ERCS. Sdílená složka musí být čitelná a zapisovatelné pro **CloudAdmin** identitu.
5. Otevřete konzolu PowerShellu ISE z počítače, ke kterému máte přístup ke sdílené složce. Přejděte do sdílené složky, kde vytvoříte adresáře, kam chcete umístit své externí certifikáty.
6. Stáhněte **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** do síťové sdílené složky, ke které je možné přistupovat během rotace, a spusťte skript. Skript vytvoří strukturu složek, která bude vyhovovat ***.\Certificates\AAD*** nebo ***.\Certificates\ADFS***, v závislosti na vašem poskytovateli identity. Vaše struktura složky musí začínat složkou ** \\ certifikáty** , za kterou následuje jenom složka ** \\ AAD** nebo ** \\ ADFS** . Všechny další podadresáře jsou obsaženy v předchozí struktuře. Například:
    - Sdílená složka = **\\\\\<IPAddress>\\\<ShareName>**
    - Kořenová složka certifikátu pro Azure AD Provider = ** \\ Certificates\AAD**
    - Úplná cesta = ** \\ \\ \<IPAddress> \\ \<ShareName> \Certificates\AAD**

    > [!IMPORTANT]
    > Při `Start-SecretRotation` pozdějším spuštění bude ověřena struktura složky. Struktura složek, která nedodržuje předpisy, vyvolá následující chybu:
    >
    > ```powershell
    > Cannot bind argument to parameter 'Path' because it is null.
    > + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
    > + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
    > + PSComputerName        : xxx.xxx.xxx.xxx
    > ```

7. Zkopírujte novou sadu náhradních externích certifikátů vytvořených v kroku #2 do adresáře **\Certificates \\ \<IdentityProvider> ** vytvořeného v kroku #6. Nezapomeňte postupovat podle `cert.<regionName>.<externalFQDN>` formátu \<CertName\> . 

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

## <a name="rotate-external-secrets"></a>Otočení externích tajných klíčů

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

2. Rotace externích tajných klíčů trvá přibližně jednu hodinu. Po úspěšném dokončení se v konzole zobrazí Konzola `ActionPlanInstanceID ... CurrentStatus: Completed` , za kterou následuje `DONE` . Odeberte certifikáty ze sdílené složky vytvořené v části požadavky a uložte je do svého zabezpečeného umístění zálohy.

    > [!Note]
    > Pokud se rotace tajných klíčů nezdařila, postupujte podle pokynů v chybové zprávě a znovu spusťte `Start-SecretRotation` s `-ReRun` parametrem.
    >
    >```powershell
    >Start-SecretRotation -ReRun
    >```  
    >
    >Pokud se setkáte s opakovanými chybami při střídání tajných kódů, kontaktujte

## <a name="rotate-internal-secrets"></a>Otočení interních tajných kódů

Interní rotace tajných klíčů se vyžaduje jenom v případě, že máte podezření, že došlo k ohrožení zabezpečení nebo když jste dostali upozornění na vypršení platnosti. Verze starší než 1811 můžou zobrazovat výstrahy pro nevyřízené položky vnitřního certifikátu nebo vypršení platnosti tajného kódu. Tyto výstrahy jsou nepřesné a měly by se ignorovat a jedná se o známý problém vyřešený v 1811. Platnost interních tajných kódů nekončí, pokud prostředí není aktivní po dobu dvou let.

Odkazování na skript PowerShell v kroku 2 [otočení externích tajných klíčů](#rotate-external-secrets). Skript poskytuje příklad, který můžete přizpůsobit pro zajištění interního tajného klíče, a to provedením několika změn ke spuštění následujících kroků:

1. V části "spuštění tajného klíče" přidejte `-Internal` parametr do [rutiny Start-SecretRotation](../reference/pep-2002/start-secretrotation.md), například:

    ```powershell
    # Run Secret Rotation
    ...
    Invoke-Command -Session $PEPSession -ScriptBlock {
        Start-SecretRotation -Internal
    }
    ...
    ```

    ::: moniker range="<azs-1811"
    > [!Note]
    > Verze starší než 1811 nevyžadují `-Internal` příznak. 
    ::: moniker-end

3. Po úspěšném dokončení se v konzole zobrazí Konzola `ActionPlanInstanceID ... CurrentStatus: Completed` , za kterou následuje `DONE`

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
| `PfxFilesPath` | Řetězec  | Nepravda  | Jmenovanou  | Žádné  | Cesta ke sdílené složce adresáře **\Certificates** obsahující všechny certifikáty koncového bodu externí sítě. Vyžaduje se pouze při otáčení externích tajných klíčů. Koncový adresář musí být **\Certificates**. |
| `CertificatePassword` | SecureString | Nepravda  | Jmenovanou  | Žádné  | Heslo pro všechny certifikáty, které jsou k dispozici v-PfXFilesPath. Požadovaná hodnota, pokud je k dispozici PfxFilesPath při otočení externích tajných klíčů. |
| `Internal` | Řetězec | Nepravda | Jmenovanou | Žádné | Vnitřní příznak musí být použit v případě, že operátor centra Azure Stack chce otočit tajné tajné klíče interní infrastruktury. |
| `PathAccessCredential` | PSCredential | Nepravda  | Jmenovanou  | Žádné  | Přihlašovací údaje PowerShellu pro sdílenou složku adresáře **\Certificates** obsahující všechny certifikáty koncového bodu externí sítě. Vyžaduje se pouze při otáčení externích tajných klíčů.  |
| `ReRun` | Přepínací parametr | Nepravda  | Jmenovanou  | Žádné  | Po neúspěšném pokusu se musí použít neustále se střídání tajných klíčů. |

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
#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>Otočení interních a externích tajných kódů infrastruktury (jenom**před 1811** )

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