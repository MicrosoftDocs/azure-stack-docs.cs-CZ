---
title: Otočit tajných kódů
titleSuffix: Azure Stack Hub
description: Naučte se, jak tyto tajné klíče otočit z centra Azure Stack.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.reviewer: ppacent
ms.author: inhenkel
ms.lastreviewed: 12/13/2019
monikerRange: '>=azs-1802'
ms.openlocfilehash: d7179d8b87ee8e543fc7f8b78d1a8113e0e2746b
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367722"
---
# <a name="rotate-secrets-in-azure-stack-hub"></a>Otočení tajných kódů v centru Azure Stack

*Tyto pokyny platí jenom pro Azure Stack integrované systémy centra verze 1803 a novější. Nepokusit se o rotaci tajných kódů v před1802 Azure Stack verzích centra*

Tajné klíče vám pomůžou udržovat zabezpečenou komunikaci mezi prostředky a službami infrastruktury centra Azure Stack.

## <a name="rotate-secrets-overview"></a>Přehled střídání tajných klíčů

1. Připravte certifikáty, které se budou používat pro rotaci tajných klíčů.
2. Zkontrolujte [požadavky na certifikát infrastruktury veřejných klíčů](https://docs.microsoft.com/azure-stack/operator/azure-stack-pki-certs)centra Azure Stack.
3. [Pomocí privilegovaného koncového bodu](azure-stack-privileged-endpoint.md) a spuštěním rutiny **test-azurestack** ověřte, zda je vše v pořádku.  
4. Přečtěte si [předběžné kroky pro rotaci tajných](#pre-steps-for-secret-rotation)kódů.
5. [Ověřte certifikáty PKI Azure Stack hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-validate-pki-certs). Ujistěte se, že heslo neobsahuje žádné speciální znaky, například `*` nebo `)`.
6. Ujistěte se, že šifrování PFX je **TripleDES-SHA1**. Pokud narazíte na problém, přečtěte si téma [řešení běžných potíží s certifikáty PKI služby Azure Stack hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-remediate-certs#pfx-encryption).
7. Připravte strukturu složek.  Příklad najdete v části [střídání externích tajných klíčů](https://docs.microsoft.com/azure-stack/operator/azure-stack-rotate-secrets#rotating-external-secrets) .
8. [Spusťte rotaci tajných](#use-powershell-to-rotate-secrets)kódů.

## <a name="rotate-secrets"></a>Otočit tajných kódů

Azure Stack hub používá ke správě zabezpečené komunikace mezi prostředky infrastruktury a službami centra Azure Stack různé tajné kódy.

- **Interní tajné kódy**

    Všechny certifikáty, hesla, zabezpečené řetězce a klíče používané infrastrukturou centra Azure Stack bez zásahu operátoru centra Azure Stack.

- **Externí tajná klíčová**

    Certifikáty služby infrastruktury pro externí služby, které poskytuje operátor centra Azure Stack. Externí tajné klíče obsahují certifikáty pro následující služby:

    - Portál pro správu
    - Veřejný portál
    - Azure Resource Manager správce
    - Globální Azure Resource Manager
    - Key Vault správce
    - Key Vault
    - Hostitel rozšíření Správce
    - ACS (včetně objektů blob, Table a Queue Storage)
    - SLUŽBOU
    - Zapisovací
    
    \* platí jenom v případě, že poskytovatel identity prostředí je ve službě Active Directory federované služby (AD FS).

> [!Note]
> Všechny ostatní zabezpečené klíče a řetězce, včetně BMC, hesla pro přepínání a hesla účtů uživatelů a správců, jsou pořád aktualizovány ručně správcem.

> [!Important]
> Počínaje verzí 1811 centra Azure Stack se tajná rotace od interních a externích certifikátů oddělila.

Aby bylo možné zachovat integritu infrastruktury centra Azure Stack, operátoři potřebují schopnost pravidelně střídat tajné klíče infrastruktury v četnostích, které jsou v souladu s požadavky na zabezpečení jejich organizace.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Střídání tajných kódů pomocí externích certifikátů od nové certifikační autority

Centrum Azure Stack podporuje v následujících kontextech i střídání tajných kódů pomocí externích certifikátů z nové certifikační autority (CA):

|Nainstalovala se certifikační autorita.|CA pro otočení|Podporuje se|Podporované verze centra Azure Stack|
|-----|-----|-----|-----|
|Od sebe podepsané svým držitelem|Do Enterprise|Podporuje se|1903 & později|
|Od sebe podepsané svým držitelem|Na podepsaný svým držitelem|Nepodporuje se||
|Od sebe podepsané svým držitelem|Na veřejné<sup>*</sup>|Podporuje se|1803 & později|
|Z Enterprise|Do Enterprise|Podporuje se. Od 1803-1903: podporované, pokud zákazníci používají stejnou certifikační autoritu organizace jako při nasazení.|1803 & později|
|Z Enterprise|Na podepsaný svým držitelem|Nepodporuje se||
|Z Enterprise|Na veřejné<sup>*</sup>|Podporuje se|1803 & později|
|Z veřejných<sup>*</sup>|Do Enterprise|Podporuje se|1903 & později|
|Z veřejných<sup>*</sup>|Na podepsaný svým držitelem|Nepodporuje se||
|Z veřejných<sup>*</sup>|Na veřejné<sup>*</sup>|Podporuje se|1803 & později|

<sup>*</sup> Označuje, že veřejné certifikační autority jsou ty, které jsou součástí důvěryhodného kořenového programu systému Windows. Úplný seznam najdete v článku [program důvěryhodných kořenových certifikátů společnosti Microsoft: účastníci (od 27. června 2017)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).

## <a name="fixing-alerts"></a>Oprava výstrah

Když jsou tajná data do 30 dnů od vypršení platnosti, vygenerují se na portálu pro správu následující výstrahy:

- Čekání na vypršení platnosti hesla účtu služby
- Čeká se na vypršení platnosti interního certifikátu.
- Blížící se vypršení platnosti externího certifikátu

Tyto výstrahy opravíte spuštěním rotace tajného klíče pomocí níže uvedených pokynů.

> [!Note]
> Prostředí centra Azure Stack ve verzích starších než 1811 mohou zobrazovat výstrahy na nevyřízené interní certifikáty nebo vypršení platnosti tajného kódu. Tyto výstrahy jsou nepřesné a měly by být ignorovány bez spuštění interního tajného klíče. Nepřesné upozornění na vypršení platnosti interního tajného klíče je známý problém, který je vyřešený v 1811. Platnost interních tajných kódů nekončí, pokud prostředí není aktivní po dobu dvou let.

## <a name="pre-steps-for-secret-rotation"></a>Předběžné kroky pro rotaci tajných klíčů

   > [!IMPORTANT]
   > Pokud se v prostředí Azure Stackého centra už provedla rotace tajných klíčů, musíte systém aktualizovat na verzi 1811 nebo novější, než znovu spustíte přesměrování tajného klíče. Pomocí [privilegovaného koncového bodu](azure-stack-privileged-endpoint.md) se musí provádět rotace tajných klíčů a vyžaduje přihlašovací údaje operátora Azure Stack centra. Pokud vaše prostředí Azure Stack operátory rozbočovače neznají, jestli se ve vašem prostředí spustilo střídání tajných klíčů, aktualizujte na 1811, než se znovu spustí rotace.

1. Důrazně doporučujeme aktualizovat instanci centra Azure Stack na verzi 1811.

    > [!Note]
    > Pro verze starší než 1811 nemusíte při přidávání certifikátů hostitele rozšíření otáčet tajné klíče. Při přidávání certifikátů hostitele rozšíření byste měli postupovat podle pokynů v článku [Příprava hostitele rozšíření pro Azure Stack hub](azure-stack-extension-host-prepare.md) .

2. Operátoři si můžou všimnout, že se výstrahy otevřou a automaticky zavřou během rotace tajných kódů centra Azure Stack.  Toto chování je očekávané a výstrahy je možné ignorovat.  Operátoři můžou ověřit platnost těchto výstrah spuštěním **test-AzureStack**.  Pro operátory, které používají System Center Operations Manager k monitorování systémů Azure Stack hub, se při umístění systému do režimu údržby zabrání tomu, aby tyto výstrahy dostaly do svých ITSM systémů, ale budou i nadále upozorňovány, pokud se systém Azure Stack hub stane nedostupným.

3. Informujte uživatele o všech operacích údržby. Naplánujte normální časová období údržby, co nejvíce, během nepracovních hodin. Operace údržby mohou ovlivnit úlohy uživatelů i operace portálu.

    > [!Note]
    > Další kroky platí pouze při otáčení externích tajných kódů centra Azure Stack.

4. Spusťte **[test-AzureStack](azure-stack-diagnostic-test.md)** a potvrďte, že všechny výstupy testů jsou v pořádku před otočením tajných kódů.
5. Připravte novou sadu náhradních externích certifikátů. Nová sada odpovídá specifikacím certifikátu, které jsou uvedené v [požadavcích na certifikát PKI centra Azure Stack](azure-stack-pki-certs.md). Můžete vygenerovat žádost o podepsání certifikátu (CSR) k nákupu nebo vytváření nových certifikátů pomocí kroků uvedených v části [vygenerování certifikátů PKI](azure-stack-get-pki-certs.md) a jejich příprava pro použití v prostředí Azure Stackového centra pomocí postupu v části [Příprava certifikátů PKI služby Azure Stack hub](azure-stack-prepare-pki-certs.md). Nezapomeňte ověřit certifikáty, které jste připravili s postupem popsaným v části [ověření certifikátů PKI](azure-stack-validate-pki-certs.md).
6. Uložte zálohu do certifikátů používaných k otočení v umístění zabezpečené zálohy. Pokud se vaše otočení spustí a pak se nepovede, nahraďte certifikáty ve sdílené složce záložními kopiemi a teprve potom znovu spusťte otočení. Uchovávejte záložní kopie v umístění zabezpečené zálohy.
7. Vytvořte sdílenou složku, ke které máte přístup z virtuálních počítačů s ERCS. Sdílená složka musí být čitelná a zapisovatelné pro **CloudAdmin** identitu.
8. Otevřete konzolu PowerShellu ISE z počítače, ke kterému máte přístup ke sdílené složce. Přejděte do sdílené složky.
9. Spuštěním **[CertDirectoryMaker. ps1](https://www.aka.ms/azssecretrotationhelper)** vytvořte požadované adresáře pro externí certifikáty.

> [!IMPORTANT]
> Skript CertDirectoryMaker vytvoří strukturu složek, která bude vyhovovat:
>
> **.\Certificates\AAD** nebo ***.\Certificates\ADFS*** v závislosti na vašem zprostředkovateli identity, který se používá pro Azure Stack hub.
>
> Má nejvyšší důležitost, že struktura složek končí složkami **AAD** nebo **ADFS** a všechny podadresáře jsou v této struktuře. v opačném případě se **spustí – SecretRotation** :
>
> ```powershell
> Cannot bind argument to parameter 'Path' because it is null.
> + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
> + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
> + PSComputerName        : xxx.xxx.xxx.xxx
> ```
>
> Chyba Massage indikuje, že došlo k potížím při přístupu ke sdílené složce souborů, ale ve skutečnosti se tady vynutila struktura složek. Další informace najdete v modulu Microsoft AzureStack Readiness Checker – [PublicCertHelper](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.1811.1101.1/Content/CertificateValidation%5CPublicCertHelper.psm1).
>
> Je také důležité, aby struktura složek sdílené složky začínala složkou **certifikáty** , v opačném případě se také nezdaří ověření.
> Připojení sdílené složky by mělo vypadat jako **\\\\\<IPAddress >\\\<název_sdílené_položky >\\** a musí obsahovat složku **Certificates\AAD** nebo **Certificates\ADFS** uvnitř.
>
> Příklad:
> - Share = **\\\\\<IPAddress >\\\<název_sdílené_položky >\\**
> - CertFolder = **Certificates\AAD**
> - FullPath = **\\\\\<IPAddress >\\\<název_sdílené_položky > \Certificates\AAD**

## <a name="rotating-external-secrets"></a>Otáčení externích tajných klíčů

Postup při otočení externích tajných klíčů:

1. V nově vytvořeném **\Certificates\\\<IdentityProvider >** Directory vytvořená v krocích předem umístěte novou sadu náhradních externích certifikátů do adresářové struktury podle formátu popsaného v části **povinné certifikáty** pro [požadavky na certifikát PKI centra Azure Stack](azure-stack-pki-certs.md#mandatory-certificates).

    Příklad struktury složek pro zprostředkovatele identit Azure AD:
    ```powershell
        <ShareName>
        │   │
        │   └───Certificates
        │         └───AAD
        │             ├───ACSBlob
        │             │       <CertName>.pfx
        │             │
        │             ├───ACSQueue
        │             │       <CertName>.pfx
        │             │
        │             ├───ACSTable
        │             │       <CertName>.pfx
        │             │
        │             ├───Admin Extension Host
        │             │       <CertName>.pfx
        │             │
        │             ├───Admin Portal
        │             │       <CertName>.pfx
        │             │
        │             ├───ARM Admin
        │             │       <CertName>.pfx
        │             │
        │             ├───ARM Public
        │             │       <CertName>.pfx
        │             │
        │             ├───KeyVault
        │             │       <CertName>.pfx
        │             │
        │             ├───KeyVaultInternal
        │             │       <CertName>.pfx
        │             │
        │             ├───Public Extension Host
        │             │       <CertName>.pfx
        │             │
        │             └───Public Portal
        │                     <CertName>.pfx

    ```

2. Pomocí účtu **CloudAdmin** vytvořte relaci PowerShellu s [privilegovaným koncovým bodem](azure-stack-privileged-endpoint.md) a uložte tyto relace jako proměnnou. Tuto proměnnou použijete jako parametr v dalším kroku.

    > [!IMPORTANT]  
    > Nezadávejte relaci. Uložte relaci jako proměnnou.

3. Spusťte **[příkaz Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/Invoke-Command?view=powershell-5.1)** . Zadáte proměnnou relace PowerShellu privilegovaného koncového bodu jako parametr **relace** .

4. Spusťte rutinu **Start-SecretRotation** s následujícími parametry:
    - **PfxFilesPath**  
    Zadejte síťovou cestu k adresáři certifikátů, který jste vytvořili dříve.  
    - **PathAccessCredential**  
    Objekt PSCredential pro přihlašovací údaje ke sdílené složce.
    - **CertificatePassword**  
    Zabezpečený řetězec hesla použitého pro všechny vytvořené soubory certifikátů PFX.

5. Počkejte na otočení tajných kódů. Rotace externích tajných klíčů trvá přibližně jednu hodinu.

    Po úspěšném dokončení rotace tajného klíče se v konzole zobrazí **Celkový stav akce: úspěch**.

    > [!Note]
    > Pokud se rotace tajných klíčů nezdařila, postupujte podle pokynů v chybové zprávě a znovu spusťte rutinu **Start-SecretRotation** s parametrem **-** restart.

    ```powershell
    Start-SecretRotation -ReRun
    ```

    Pokud se setkáte s opakovanými chybami při střídání tajných kódů, kontaktujte

6. Po úspěšném dokončení rotace tajného klíče odeberte svoje certifikáty ze sdílené složky vytvořené v předběžné fázi a uložte je do svého zabezpečeného záložního umístění.

## <a name="use-powershell-to-rotate-secrets"></a>Použití PowerShellu k otočení tajných kódů

Následující příklad prostředí PowerShell ukazuje rutiny a parametry, které je třeba spustit, aby bylo možné tyto tajné kódy otočit.

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

## <a name="rotating-only-internal-secrets"></a>Otočení jenom interních tajných kódů

> [!Note]
> Vnitřní rotace tajných klíčů by se mělo provést jenom v případě, že se domníváte, že došlo k ohrožení interního tajného klíče, nebo pokud jste obdrželi upozornění (na buildu 1811 nebo novější), což značí, že se blíží vypršení platnosti vnitřních certifikátů. Prostředí centra Azure Stack ve verzích starších než 1811 mohou zobrazovat výstrahy na nevyřízené interní certifikáty nebo vypršení platnosti tajného kódu. Tyto výstrahy jsou nepřesné a měly by být ignorovány bez spuštění interního tajného klíče. Nepřesné upozornění na vypršení platnosti interního tajného klíče je známý problém, který je vyřešený v 1811. Platnost interních tajných kódů nekončí, pokud prostředí není aktivní po dobu dvou let.

1. Vytvořte relaci PowerShellu s [privilegovaným koncovým bodem](azure-stack-privileged-endpoint.md).
2. V relaci privilegovaného koncového bodu spusťte rutinu **Start-SecretRotation-Internal**.

    > [!Note]
    > Prostředí Azure Stack hub na verzích starších než 1811 nebudou vyžadovat příznak **-internal** . **Příkaz Start-SecretRotation** provede otočení pouze interních tajných kódů.

3. Počkejte na otočení tajných kódů.

   Po úspěšném dokončení rotace tajného klíče se v konzole zobrazí **Celkový stav akce: úspěch**.
    > [!Note]
    > Pokud se rotace tajných klíčů nezdařila, postupujte podle pokynů v chybové zprávě a znovu spusťte **příkaz Start-SecretRotation** s parametry **-internal** a-restart **-** .  

```powershell
Start-SecretRotation -Internal -ReRun
```

Pokud se setkáte s opakovanými chybami při střídání tajných kódů, kontaktujte

## <a name="start-secretrotation-reference"></a>Odkaz na začátek – SecretRotation

Otočí tajné klíče systému Azure Stack hub. Provede se jenom s privilegovaným koncovým bodem centra Azure Stack.

### <a name="syntax"></a>Syntaxe

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

### <a name="description"></a>Popis

Rutina **Start-SecretRotation** otočí tajné klíče infrastruktury Azure Stackho centrálního systému. Ve výchozím nastavení otočí jenom certifikáty všech koncových bodů infrastruktury externích sítí. V případě, že se používá s příznakem-Internal, budou se informace o interní infrastruktuře střídat. Při otáčení koncových bodů síťové infrastruktury externí sítě by se měl spustit **-SecretRotation** spustit pomocí bloku skriptu **Invoke-Command** s použitím privilegované relace koncového bodu Azure Stack centra prostředí, která byla předána jako parametr **relace** .

### <a name="parameters"></a>Parametry

| Parametr | Typ | Požaduje se | Pozice | Výchozí | Popis |
| -- | -- | -- | -- | -- | -- |
| `PfxFilesPath` | Řetězec  | False  | Jmenovanou  | Žádná  | Cesta ke sdílené složce adresáře **\Certificates** obsahující všechny certifikáty koncového bodu externí sítě. Vyžaduje se pouze při otáčení externích tajných klíčů. Koncový adresář musí být **\Certificates**. |
| `CertificatePassword` | SecureString | False  | Jmenovanou  | Žádná  | Heslo pro všechny certifikáty, které jsou k dispozici v-PfXFilesPath. Požadovaná hodnota, pokud je k dispozici PfxFilesPath při otočení externích tajných klíčů. |
| `Internal` | Řetězec | False | Jmenovanou | Žádná | Vnitřní příznak musí být použit v případě, že operátor centra Azure Stack chce otočit tajné tajné klíče interní infrastruktury. |
| `PathAccessCredential` | PSCredential | False  | Jmenovanou  | Žádná  | Přihlašovací údaje PowerShellu pro sdílenou složku adresáře **\Certificates** obsahující všechny certifikáty koncového bodu externí sítě. Vyžaduje se pouze při otáčení externích tajných klíčů.  |
| `ReRun` | Přepínací parametr | False  | Jmenovanou  | Žádná  | Po neúspěšném pokusu je třeba znovu spustit opětovné otočení tajného klíče. |

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

Tento příkaz otočí certifikáty TLS používané k externím koncovým bodům infrastruktury sítě Azure Stack hub.

#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>Otočení interních a externích tajných kódů infrastruktury (jenom**před 1811** )

> [!IMPORTANT]
> Tento příkaz platí jenom pro Azure Stack centra **1811** , protože rotace je rozdělená na interní a externí certifikáty.
>
> **Od *1811 +* už nemůžete otočit interní i externí certifikát!**

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

Tento příkaz otočí všechny tajné klíče infrastruktury vystavené pro Azure Stack interní síť centra a také certifikáty TLS používané pro koncové body infrastruktury externích sítí centra Azure Stack. Start-SecretRotation otočí všechny tajné klíče generované zásobníkem a protože jsou k dispozici nějaké certifikáty, budou se také otáčet certifikáty externích koncových bodů.  

## <a name="update-the-baseboard-management-controller-bmc-credential"></a>Aktualizace přihlašovacích údajů řadiče pro správu základní desky (BMC)

Řadič pro správu základní desky (BMC) monitoruje fyzický stav vašich serverů. Pokyny k aktualizaci názvu uživatelského účtu a hesla řadiče pro správu základní desky najdete v tématu dodavatel hardwaru výrobce OEM.

>[!NOTE]
> Výrobce OEM může poskytovat další aplikace pro správu. Aktualizace uživatelského jména nebo hesla pro jiné aplikace pro správu nemá žádný vliv na uživatelské jméno nebo heslo řadiče pro správu základní desky.

1. **Verze starší než 1910**: aktualizujte BMC na fyzických serverech centra Azure Stack, podle pokynů výrobce OEM. Uživatelské jméno a heslo pro každý řadič pro správu základní desky ve vašem prostředí musí být stejné. Uživatelská jména řadiče pro správu základní desky nesmí být delší než 16 znaků.

   **Verze 1910 a novější**: již není nutné, abyste nejprve aktualizovali přihlašovací údaje řadiče pro správu základní desky na fyzických serverech centra Azure Stack podle pokynů výrobce OEM. Uživatelské jméno a heslo pro každý řadič pro správu základní desky ve vašem prostředí musí být stejné. Uživatelská jména řadiče pro správu základní desky nesmí být delší než 16 znaků.

    | Parametr | Popis | Stav |
    | --- | --- | --- |
    | BypassBMCUpdate | Když použijete parametr, přihlašovací údaje v řadiči pro správu základní desky se neaktualizují. Aktualizuje se jenom interní úložiště dat centra Azure Stack. | Nepovinné |

2. Otevřete privilegovaný koncový bod v Azure Stack relace centra. Pokyny najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](azure-stack-privileged-endpoint.md).

3. Po změně výzvy PowerShellu na **[IP adresa nebo ERCS název virtuálního počítače]: ps >** nebo na **[AZS-ERCS01]: PS >** v závislosti na prostředí spusťte `Set-BmcCredential` spuštěním `Invoke-Command`. Předat proměnnou vaší privilegované relace koncového bodu jako parametr. Příklad:

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

    Můžete také použít statickou verzi prostředí PowerShell s hesly jako řádky kódu:

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

## <a name="next-steps"></a>Další kroky

[Další informace o zabezpečení centra Azure Stack](azure-stack-security-foundations.md)
