---
title: Otočit tajné kódy ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak otočit vaše tajné kódy ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.reviewer: ppacent
ms.author: mabrigg
ms.lastreviewed: 05/14/2019
ms.openlocfilehash: 4b758cce6741440f5b6a4c00de045e9a4fc8f530
ms.sourcegitcommit: 1655b2ef4d01d69ceeb52bc16f922bdc19cb968d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/15/2019
ms.locfileid: "65706326"
---
# <a name="rotate-secrets-in-azure-stack"></a>Otočit tajné kódy ve službě Azure Stack

*Tyto pokyny platí jenom pro Azure Stack integrované systémy verze 1803 a novější. Nepokoušejte tajných kódů otočení na verze pre-1802 Azure Stack*

Azure Stack používá různé tajných kódů k údržbě zabezpečenou komunikaci mezi prostředky infrastruktury Azure stacku a služeb.

- **Interní tajných kódů**

Všechny certifikáty, hesla, zabezpečené řetězce a klíčů používaných infrastruktury služby Azure Stack bez zásahu operátor Azure stacku.

- **Externích tajných klíčů**

Certifikáty služby infrastruktury externích služeb, které jsou k dispozici v operátor Azure stacku. Externích tajných klíčů rozsah zahrnuje certifikáty pro následující služby:

- Portál správce
- Veřejný portál
- Správce Azure Resource Manageru
- Globální Azure Resource Manageru
- Správce služby KeyVault
- KeyVault
- Admin Extension Host
- Služby ACS (včetně objektů blob, table a queue storage)
- ADFS *
- Graf *

\* Platí pouze pokud je zprostředkovatel identity prostředí Active Directory Federated Services (AD FS).

> [!Note]
> Všechny ostatní zabezpečení klíčů a řetězce, včetně BMC a přepněte hesla, správce ručně aktualizovat hesla k účtům uživatelů a správců.

> [!Important]
> Od verze Azure Stack 1811 tajných kódů otočení byl oddělen pro interní i externí certifikáty.

Aby bylo možné udržovat tak integritu infrastruktury Azure stacku, operátory nemusí pravidelně obměňovat svoji infrastrukturu tajných kódů v frekvence, které jsou v souladu s požadavky na zabezpečení organizace.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Otáčení tajných kódů pomocí externí certifikáty od certifikační autority nový

Azure Stack podporuje v následujících kontextů tajných kódů otočení s externí certifikáty od nového certifikátu autority (CA):

|Nainstalovaný certifikát certifikační Autority|Otočit na certifikační Autority.|Podporováno|Podporované verze Azure Stack|
|-----|-----|-----|-----|
|Z podepsaného svým držitelem|Do firemní sítě|Podporováno|1903 & později|
|Z podepsaného svým držitelem|Pro podepsaný svým držitelem|Nepodporuje se||
|Z podepsaného svým držitelem|Na veřejnou<sup>*</sup>|Podporováno|1803 & později|
|Z organizace|Do firemní sítě|Podporuje se. Z 1803 1903: podporované tak dlouho, dokud zákazníci využívat stejné podnikové certifikační Autority, jak použít při nasazení|1803 & později|
|Z organizace|Pro podepsaný svým držitelem|Nepodporuje se||
|Z organizace|Na veřejnou<sup>*</sup>|Podporováno|1803 & později|
|Z veřejné<sup>*</sup>|Do firemní sítě|Podporováno|1903 & později|
|Z veřejné<sup>*</sup>|Pro podepsaný svým držitelem|Nepodporuje se||
|Z veřejné<sup>*</sup>|Na veřejnou<sup>*</sup>|Podporováno|1803 & později|

<sup>*</sup>Označuje, že veřejné certifikační autority jsou ty, které jsou součástí programu Windows důvěryhodné kořenové. Úplný seznam najdete v článku [Microsoft Trusted Root Certificate Program: Účastníci (od 27. června 2017)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="alert-remediation"></a>Oprava výstrahy

Po do 30 dnů od vypršení platnosti tajných kódů tyto výstrahy jsou generovány v portálu správce:

- Vypršení platnosti hesla účtu čekající služby
- Vypršení platnosti vnitřní certifikát čekající na vyřízení
- Vypršení platnosti externí certifikát čekající na vyřízení

Spuštění tajných kódů otočení pomocí následujícího postupu se opravit tyto výstrahy.

> [!Note]
> Prostředí Azure Stack na 1811 předběžné verze se může zobrazit výstrahy pro čekající vnitřní certifikát nebo vypršení platnosti tajných kódů.
> Tyto výstrahy jsou nesprávné a je třeba ignorovat bez spuštění interní otočení tajného kódu.
> Upozornění nepřesné vypršení platnosti interní tajných kódů jsou známý problém, který je přeložen v 1811 – interní, tajných kódů nevyprší, dokud prostředí byl aktivní na dva roky.

## <a name="pre-steps-for-secret-rotation"></a>Kroky prováděné před zpracováním pro rotaci tajného kódu

   > [!IMPORTANT]
   > Pokud má už jsou hotové tajných kódů otočení ve vašem prostředí Azure Stack musíte aktualizovat systém na verzi 1811 nebo novější, ještě před spuštěním tajných kódů otočení znovu.
   > Tajný otočení musí provést prostřednictvím [privilegovaných koncový bod](azure-stack-privileged-endpoint.md) a vyžadují přihlašovací údaje Azure Stack operátor.
   > Pokud vaše prostředí Azure Stack operátory nejste jisti, zda byl spuštěn tajných kódů otočení ve vašem prostředí, aktualizujte na 1811 před provedením tajných kódů otočení znovu.

1. Důrazně doporučujeme aktualizovat na verzi 1811 vaší instance služby Azure Stack.

    > [!Note] 
    > Pro verze pre-1811 není potřeba otočit tajných kódů přidat certifikáty rozšíření hostitele. Postupujte podle pokynů v článku [Příprava hostitele rozšíření pro službu Azure Stack](azure-stack-extension-host-prepare.md) přidání rozšíření hostitele certifikátů.

2. Během obměny tajných klíčů služby Azure Stack si operátoři můžou všimnout otevírání a automatického zavírání upozornění.  Toto chování je očekávané a upozornění můžete ignorovat.  Operátory lze ověřit platnost těchto upozornění spuštěním **testovací AzureStack**.  Pro operátory pomocí System Center Operations Manager monitorovat systémy Azure Stack, uvedení v režimu údržby systému zabrání dosažení jejich systémům ITSM tyto výstrahy, ale bude nadále upozornění, pokud se nestane nedostupnou systémech pro Azure Stack.

3. Upozorněte uživatele na jakékoli operace údržby. Naplánujte běžné časová období údržby, co je to možné, během pracovní doby. Operace údržby může ovlivnit uživatelské úlohy a operací na portálu.

    > [!Note]
    > Další kroky se projeví pouze v případě otáčení externích tajných klíčů Azure Stack.

4. Spustit **[testovací AzureStack](azure-stack-diagnostic-test.md)** a potvrďte všechny výstupy testů jsou v dobrém stavu před otáčení tajných kódů.
5. Přidejte novou sadu nahrazení externí certifikáty. Nová sada odpovídá certifikát požadavky uvedené v [požadavky na certifikát Azure Stack PKI](azure-stack-pki-certs.md). Můžete generovat žádost o podepsání certifikátu (CSR) pro zakoupení nebo vytváří nové certifikáty pomocí postupu uvedeného v [vygenerovat certifikáty PKI](azure-stack-get-pki-certs.md) a je připravili k použití ve vašem prostředí Azure Stack pomocí kroků v [ Přidejte certifikáty infrastruktury veřejných KLÍČŮ služby Azure Stack](azure-stack-prepare-pki-certs.md). Ověřte certifikáty připravíte podle kroků uvedených v [ověřit certifikáty PKI](azure-stack-validate-pki-certs.md).
6. Store zálohováním do certifikátů používaných pro rotaci do zabezpečeného umístění. Pokud vaše otočení spustí a pak se nezdaří, nahraďte certifikáty ve sdílené složce záložní kopie předtím, než znovu spustíte otočení. Mějte na paměti, uchovávání záložních kopií v zabezpečené umístění zálohy.
7. Vytvoření sdílené složky, ke kterým přistupujete z ERCS virtuálních počítačů. Sdílené složky musí být čtení a zápisu pro **CloudAdmin** identity.
8. Otevřete konzoly ISE Powershellu na počítači, kde máte přístup ke sdílení souborů. Přejděte na vaši sdílenou složku.
9. Spustit **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** k vytvoření požadované adresáře pro externí certifikáty.

> [!IMPORTANT]
> Skript CertDirectoryMaker vytvoří strukturu složek, která budou dodržovat:
>
> **.\Certificates\AAD** nebo ***.\Certificates\ADFS*** v závislosti na váš zprostředkovatel Identity sloužící pro službu Azure Stack
>
> Je naprosto, který končí vaše struktura složky **AAD** nebo **služby AD FS** složky a všech podadresářích jsou v rámci této struktury; v opačném případě **Start-SecretRotation**se vytvoří s:
> ```powershell
> Cannot bind argument to parameter 'Path' because it is null.
> + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
> + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
> + PSComputerName        : xxx.xxx.xxx.xxx
> ```
>
> Jak je vidět masáž chyba by označoval, že dojde k nějakému problému, přístup k vaší sdílení souborů, ale ve skutečnosti je struktura složek, které je vynucuje tady.
> Další informace najdete v Kontrola připravenosti AzureStack Microsoft - [PublicCertHelper modulu](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.1811.1101.1/Content/CertificateValidation%5CPublicCertHelper.psm1)
>
> Je také důležité, že vaše struktura složky sdílenými složkami začíná **certifikáty** složky jinak také dojde k selhání ověřování.
> Připojení sdílené složky by měl vypadat jako **\\ \\ \<IPAddress >\\\<název_sdílené_položky >\\** a musí obsahovat složku  **Certificates\AAD** nebo **Certificates\ADFS** uvnitř.
>
> Příklad:
> - Sdílení souborů =  **\\ \\ \<IPAddress >\\\<název_sdílené_položky >\\**
> - CertFolder = **Certificates\AAD**
> - FullPath = **\\\\\<IPAddress>\\\<ShareName>\Certificates\AAD**

## <a name="rotating-external-secrets"></a>Otáčení externích tajných klíčů

Obměna externích tajných klíčů:

1. V rámci nově vytvořené **\Certificates\\\<IdentityProvider >** adresář vytvořený v kroky prováděné před zpracováním, umístěte do struktury adresářů podle novou sadu nahrazení externí certifikáty Formát uvedené v oddílu povinné certifikáty [požadavky na certifikát Azure Stack PKI](azure-stack-pki-certs.md#mandatory-certificates).

    Příklad strukturu složek pro zprostředkovatele Identity AAD:
    ```powershell
        <ShareName>
        │   │
        │   ├───Certificates
        │   └───AAD
        │       ├───ACSBlob
        │       │       <CertName>.pfx
        │       │
        │       ├───ACSQueue
        │       │       <CertName>.pfx
        │       │
        │       ├───ACSTable
        │       │       <CertName>.pfx
        │       │
        │       ├───Admin Extension Host
        │       │       <CertName>.pfx
        │       │
        │       ├───Admin Portal
        │       │       <CertName>.pfx
        │       │
        │       ├───ARM Admin
        │       │       <CertName>.pfx
        │       │
        │       ├───ARM Public
        │       │       <CertName>.pfx
        │       │
        │       ├───KeyVault
        │       │       <CertName>.pfx
        │       │
        │       ├───KeyVaultInternal
        │       │       <CertName>.pfx
        │       │
        │       ├───Public Extension Host
        │       │       <CertName>.pfx
        │       │
        │       └───Public Portal
        │               <CertName>.pfx

    ```

2. Vytvořte relaci Powershellu s [privilegovaných koncový bod](azure-stack-privileged-endpoint.md) pomocí **CloudAdmin** účtu a uložit tyto relace jako proměnnou. Tato proměnná bude používat jako parametr v dalším kroku.

    > [!IMPORTANT]  
    > Nezadávejte relace, relace uložit jako proměnnou.

3. Spustit  **[Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/Invoke-Command?view=powershell-5.1)**. Předání jako proměnnou relace Powershellu privilegovaných koncový bod **relace** parametru.

4. Spustit **Start SecretRotation** s následujícími parametry:
    - **PfxFilesPath**  
    Zadejte síťovou cestu k adresáři certifikáty vytvořili dříve.  
    - **PathAccessCredential**  
    Objekt PSCredential pro přihlašovací údaje ke sdílené složce.
    - **CertificatePassword**  
    Zabezpečený řetězec heslo použité pro všechny vytvořené soubory certifikátů pfx.

5. Počkejte, dokud otočit tajných klíčů. Externí tajných kódů otočení obvykle trvá přibližně jednu hodinu.

    Po úspěšném dokončení otočení tajného kódu se zobrazí konzola **celkový stav akce: Úspěch**.

    > [!Note]
    > Pokud selže tajných kódů otočení, postupujte podle pokynů v chybové zprávě a znovu spusťte **Start SecretRotation** s **– opětovné spuštění** parametr.

    ```powershell
    Start-SecretRotation -ReRun
    ```
    Obraťte se na podporu Pokud dochází k opakované otočení tajných kódů chyb.

6. Po úspěšném dokončení tajných kódů otáčení odebrat certifikáty ze sdílené složky vytvořené v kroku předběžné a uložit je do jejich zabezpečené umístění zálohy.

## <a name="walkthrough-of-secret-rotation"></a>Návod k otočení tajného kódu

Následující příklad PowerShell ukazuje, rutiny a parametry k otočení tajných klíčů.

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Run Secret Rotation
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

## <a name="rotating-only-internal-secrets"></a>Otáčení pouze interní tajných kódů

> [!Note]
> Interní otočení tajný kód lze provádět pouze pokud máte podezření, že interní tajného kódu došlo k napadení škodlivým entity, nebo Pokud dostanete výstrahu (při sestavování 1811 nebo novější) označující, že interní certifikáty se blíží vypršení platnosti.
> Prostředí Azure Stack na 1811 předběžné verze se může zobrazit výstrahy pro čekající vnitřní certifikát nebo vypršení platnosti tajných kódů.
> Tyto výstrahy jsou nesprávné a je třeba ignorovat bez spuštění interní otočení tajného kódu.
> Upozornění nepřesné vypršení platnosti interní tajných kódů jsou známý problém, který je přeložen v 1811 – interní, tajných kódů nevyprší, dokud prostředí byl aktivní na dva roky.

1. Vytvořte relaci Powershellu s [privilegovaných koncový bod](azure-stack-privileged-endpoint.md).
2. V relaci privilegovaných koncový bod spustit **Start SecretRotation-interní**.

    > [!Note]
    > Prostředí Azure Stack na verze pre-1811 nevyžadují **– interní** příznak. **Start-SecretRotation** otočí pouze interní tajných kódů.

3. Počkejte, dokud otočit tajných klíčů.

Po úspěšném dokončení otočení tajného kódu se zobrazí konzola **celkový stav akce: Úspěch**.
    > [!Note]
    > If secret rotation fails, follow the instructions in the error message and rerun **Start-SecretRotation** with the  **-Internal** and **-ReRun** parameters.  

```powershell
Start-SecretRotation -Internal -ReRun
```

Obraťte se na podporu Pokud dochází k opakované otočení tajných kódů chyb.

## <a name="start-secretrotation-reference"></a>Odkaz na začátku SecretRotation

Otočí tajných kódů systému Azure Stack. Provést pouze proti koncovému bodu privilegovaných pro Azure Stack.

### <a name="syntax"></a>Syntaxe

#### <a name="for-external-secret-rotation"></a>Pro externí otočení tajného kódu

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential <PSCredential>] [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>Pro interní otočení tajného kódu

```powershell
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>Pro externí tajných kódů otočení znovu spustit

```powershell
Start-SecretRotation [-ReRun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>Pro interní tajných kódů otočení znovu spustit

```powershell
Start-SecretRotation [-ReRun] [-Internal]
```

### <a name="description"></a>Popis

**Start SecretRotation** rutiny otočí infrastruktury tajemství systémech pro Azure Stack. Ve výchozím nastavení se otočí jenom certifikáty všechny koncové body externí síťové infrastruktury. Použít s interní příznak - interní infrastruktury tajných kódů otočí. Při obměně koncových bodů externí síťové infrastruktury, **Start SecretRotation** by měl být spuštěn s **Invoke-Command** blok skriptu s prostředím Azure Stack na privilegovaný koncový bod relace předané jako **relace** parametru.

### <a name="parameters"></a>Parametry

| Parametr | Type | Požaduje se | Umístění | Výchozí | Popis |
| -- | -- | -- | -- | -- | -- |
| `PfxFilesPath` | String  | False (Nepravda)  | s názvem  | Žádný  | Cesta sdílení souborů na **\Certificates** síťové adresáře, který obsahuje všechny externí certifikáty koncový bod. Povinné pouze v případě otáčení externích tajných klíčů. Adresář end musí být **\Certificates**. |
| `CertificatePassword` | SecureString | False (Nepravda)  | s názvem  | Žádný  | Heslo pro všechny certifikáty v PfXFilesPath – k dispozici. Požadovaná hodnota, pokud PfxFilesPath víceklientského externích tajných klíčů se otočí. |
| `Internal` | String | False (Nepravda) | s názvem | Žádný | Interní příznak musí použít, kdykoli operátory Azure stacku chce otočit interní infrastruktury tajných kódů. |
| `PathAccessCredential` | PSCredential | False (Nepravda)  | s názvem  | Žádný  | Přihlašovací údaje prostředí PowerShell pro sdílení souborů systému **\Certificates** síťové adresáře, který obsahuje všechny externí certifikáty koncový bod. Povinné pouze v případě otáčení externích tajných klíčů.  |
| `ReRun` | SwitchParameter | False (Nepravda)  | s názvem  | Žádný  | Opětovné spuštění se musí použít kdykoli otočení tajného kódu je opakování po neúspěšném pokusu. |

### <a name="examples"></a>Příklady

#### <a name="rotate-only-internal-infrastructure-secrets"></a>Otočit pouze interní infrastruktury tajných kódů

To je potřeba použít prostřednictvím služby Azure Stack [prostředí na privilegovaný koncový bod](azure-stack-privileged-endpoint.md).

```powershell
PS C:\> Start-SecretRotation -Internal
```

Tento příkaz otočí všechny tajné klíče infrastruktury vystavený k interní síti Azure Stack.

#### <a name="rotate-only-external-infrastructure-secrets"></a>Otočit pouze tajných kódů externí infrastruktury  

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

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

Tento příkaz otočí certifikáty protokolu TLS pro koncové body služby Azure Stack externí síťové infrastruktury.

#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>Otočit infrastruktury interních a externích tajných kódů (**pre-1811** jenom)

> [!IMPORTANT]
> Tento příkaz se týká pouze ke službě Azure Stack **pre-1811** jako rozdělil otočení pro interní i externí certifikáty.
>
> **Z *1811 +* nelze otočit i interní a externí certifikáty nic dalšího.**

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

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

Tento příkaz otočí všechny tajné klíče infrastruktury vystavený k interní síti Azure Stack, stejně jako certifikáty protokolu TLS pro koncové body služby Azure Stack externí síťové infrastruktury. Start-SecretRotation otočí všech tajných kódů generovaných zásobníku a vzhledem k tomu, že jsou k dispozici certifikáty, bude také otočen certifikáty externí koncový bod.  

## <a name="update-the-baseboard-management-controller-bmc-credential"></a>Aktualizovat pověření pro řadič pro správu základní desky

Řadič pro správu základní desky (BMC) monitoruje fyzickému stavu vašich serverů. Požadavky a pokyny, aktualizuje se uživatelské jméno a heslo pro správu základní desky se liší v závislosti na dodavatele hardwaru, výrobce OEM (OEM). Měli byste aktualizovat hesla pro Azure Stack součásti v pravidelných intervalech.

1. Aktualizujte správu základní desky na fyzických serverech služby Azure Stack podle pokynů vaší výrobce OEM. Uživatelské jméno a heslo pro každou BMC ve vašem prostředí musí být stejné. Všimněte si, že uživatelská jména BMC nesmí překročit 16 znaků.
2. Otevřete koncový bod privilegovaných ve službě Azure Stack relacích. Pokyny najdete v tématu [pomocí privilegovaných koncového bodu ve službě Azure Stack](azure-stack-privileged-endpoint.md).
3. Po Powershellu řádek byl změněn na **[adresa IP nebo virtuální počítač ERCS name]: PS >** nebo **[azs-ercs01]: PS >**, v závislosti na prostředí, spusťte `Set-BmcCredential` spuštěním `Invoke-Command`. Vaše proměnná privilegovaných koncový bod relace předáte jako parametr. Příklad:

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

    Statické verze prostředí PowerShell můžete použít také s hesla jako řádky kódu:

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

## <a name="next-steps"></a>Další postup

[Další informace o zabezpečení služby Azure Stack](azure-stack-security-foundations.md)
