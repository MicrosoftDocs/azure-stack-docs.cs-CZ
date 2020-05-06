---
title: Nasazení ASDK z příkazového řádku pomocí PowerShellu
description: Přečtěte si, jak nasadit ASDK z příkazového řádku pomocí PowerShellu.
author: justinha
ms.topic: article
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: d6c7e29f83700eb2eb897bcd4b8bfdd65c7dfb4c
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847142"
---
# <a name="deploy-asdk-from-the-command-line-using-powershell"></a>Nasazení ASDK z příkazového řádku pomocí PowerShellu

Azure Stack Development Kit (ASDK) je testovací a vývojové prostředí, které můžete nasadit pro vyhodnocení a předvedení Azure Stack funkcí a služeb. Pokud ho chcete začít používat, musíte připravit hardware prostředí a spustit některé skripty. Spuštění skriptů trvá několik hodin. Potom se můžete přihlásit k portálům pro správu a uživatele a začít používat Azure Stack.

## <a name="prerequisites"></a>Požadavky

Připravte hostitelský počítač ASDK. Naplánujte svůj hardware, software a síť. Počítač, který je hostitelem ASDK, musí splňovat požadavky na hardware, software a síť. Vyberte si mezi používáním Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) (AD FS). Před zahájením nasazení Nezapomeňte dodržovat tyto požadavky, aby proces instalace běžel plynule.

Před nasazením ASDK zajistěte, aby konfigurace hardwaru, operačního systému, účtu a sítě hostitelského počítače ASDK splňovaly minimální požadavky pro instalaci ASDK.

**[Projděte si požadavky a předpoklady pro nasazení ASDK](asdk-deploy-considerations.md)**.

> [!TIP]
> Po instalaci operačního systému můžete použít [Nástroj pro kontrolu požadavků na nasazení Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) , abyste ověřili, že hardware splňuje všechny požadavky.

## <a name="download-and-extract-the-deployment-package"></a>Stažení a extrakce balíčku pro nasazení
Až ověříte, že váš hostitelský počítač ASDK splňuje základní požadavky na instalaci ASDK, je dalším krokem stažení a extrakce balíčku pro nasazení ASDK. Balíček pro nasazení obsahuje soubor Cloudbuilder. vhdx, což je virtuální pevný disk, který obsahuje spustitelný operační systém a instalační soubory Azure Stack.

Balíček pro nasazení si můžete stáhnout do hostitele ASDK nebo do jiného počítače. Extrahované soubory nasazení zabírají 60 GB volného místa na disku, takže použití jiného počítače může pomoci snižovat požadavky na hardware pro hostitele ASDK.

**[Stažení a extrakce Azure Stack Development Kit (ASDK)](asdk-download.md)**

## <a name="prepare-the-asdk-host-computer"></a>Připravte hostitelský počítač sady ASDK.
Než budete moct nainstalovat ASDK na hostitelský počítač, musí být prostředí připravené a systém nakonfigurovaný tak, aby se spouštěl z VHD. Po provedení tohoto kroku se hostitel ASDK spustí do souboru Cloudbuilder. vhdx (virtuální pevný disk, který obsahuje spouštěcí operační systém a instalační soubory Azure Stack).

Pomocí PowerShellu nakonfigurujte hostitelský počítač ASDK, který se má spustit z CloudBuilder. vhdx. Tyto příkazy konfigurují hostitelský počítač s ASDK pro spouštění ze stažených a extrahovaných Azure Stack Virtual doporučené (CloudBuilder. vhdx). Po dokončení těchto kroků restartujte hostitelský počítač ASDK.

Konfigurace hostitelského počítače ASDK pro spouštění z CloudBuilder. vhdx:

  1. Spusťte příkazový řádek jako správce.
  2. Spusťte `bcdedit /copy {current} /d "Azure Stack"`.
  3. Kopírovat (CTRL + C) vrácenou hodnotu CLSID, včetně požadované složené závorky (`{}`). Tato hodnota se označuje jako `{CLSID}` a musí být vložena do (CTRL + V nebo na pravé straně) ve zbývajících krocích.
  4. Spusťte `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx`.
  5. Spusťte `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx`.
  6. Spusťte `bcdedit /set {CLSID} detecthal on`.
  7. Spusťte `bcdedit /default {CLSID}`.
  8. Chcete-li ověřit nastavení spouštění `bcdedit`, spusťte příkaz.
  9. Zajistěte, aby byl soubor CloudBuilder. vhdx přesunut do kořenového adresáře C:\. Drive (`C:\CloudBuilder.vhdx`) a restartujte hostitelský počítač ASDK. Po restartování počítače hostitele ASDK by se měl spustit z pevného disku virtuálního počítače CloudBuilder. vhdx a zahájit ASDK nasazení.

> [!IMPORTANT]
> Před restartováním počítače zajistěte, aby byl k hostitelskému počítači s ASDK přímý přístup fyzický nebo KVM. Po prvním spuštění virtuálního počítače se zobrazí výzva k dokončení instalačního programu systému Windows Server. Zadejte stejné přihlašovací údaje správce, které jste použili k přihlášení do hostitelského počítače ASDK.

### <a name="prepare-the-asdk-host-using-powershell"></a>Příprava hostitele ASDK pomocí prostředí PowerShell 
Po úspěšném spuštění hostitelského počítače ASDK do image CloudBuilder. vhdx se přihlaste se stejnými přihlašovacími údaji místního správce, které jste použili pro přihlášení k hostitelskému počítači ASDK. Jsou to taky stejné přihlašovací údaje, které jste zadali v rámci dokončení instalace Windows serveru, když se hostitelský počítač spustí z VHD.

> [!NOTE]
> Volitelně můžete také nakonfigurovat [Azure Stack nastavení telemetrie](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *před* instalací rozhraní ASDK.

Otevřete konzolu konzoly PowerShell se zvýšenými oprávněními a spuštěním příkazů v této části nasaďte ASDK na hostitele ASDK.

> [!IMPORTANT]
> Instalace ASDK podporuje pro sítě právě jednu síťovou kartu (NIC). Pokud máte více síťových adaptérů, ujistěte se, že je před spuštěním skriptu nasazení povolená jenom jedna (a všechny ostatní jsou zakázané).

Azure Stack můžete nasadit s využitím služby Azure AD nebo Windows Server AD FS jako zprostředkovatele identity. Azure Stack, poskytovatelé prostředků a další aplikace fungují stejným způsobem s oběma.

> [!TIP]
> Pokud nezadáte žádné parametry instalace (viz volitelné parametry InstallAzureStackPOC. ps1 a příklady níže), zobrazí se výzva k zadání požadovaných parametrů.

### <a name="deploy-azure-stack-using-azure-ad"></a>Nasazení Azure Stack pomocí Azure AD 
Pokud chcete nasadit Azure Stack **s využitím služby Azure AD jako zprostředkovatele identity**, musíte mít připojení k Internetu, a to buď přímo, nebo prostřednictvím transparentního proxy serveru. 

Spusťte následující příkazy PowerShellu k nasazení ASDK pomocí služby Azure AD:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

Několik minut do instalace ASDK budete vyzváni k zadání přihlašovacích údajů Azure AD. Zadejte přihlašovací údaje globálního správce pro vašeho tenanta Azure AD.

Po nasazení se Azure Active Directory oprávnění globálního správce nevyžadují. Některé operace ale můžou vyžadovat přihlašovací údaje globálního správce. Příklady takových operací zahrnují skript instalačního programu poskytovatele prostředků nebo novou funkci, která vyžaduje udělení oprávnění. Můžete buď dočasně obnovit oprávnění globálního správce účtu, nebo použít samostatný účet globálního správce, který je vlastníkem *výchozího předplatného poskytovatele*.

### <a name="deploy-azure-stack-using-ad-fs"></a>Nasazení Azure Stack pomocí AD FS 
Pokud chcete nasadit ASDK **pomocí AD FS jako poskytovatele identity**, spusťte následující příkazy PowerShellu (stačí přidat jenom parametr-UseADFS):

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

V AD FS nasazení se jako zprostředkovatel identity používá výchozí adresářová služba razítek. Výchozí účet pro přihlášení je azurestackadmin@azurestack.locala heslo je nastavené na to, co jste zadali jako součást příkazů pro instalaci PowerShellu.

Proces nasazení může trvat několik hodin, během kterých se systém automaticky restartuje. Po úspěšném nasazení se v konzole PowerShellu zobrazí: **dokončeno: akce nasazení**. Pokud se nasazení nepovede, zkuste skript znovu spustit pomocí parametru-rerunning. Nebo můžete [znovu nasadit ASDK](asdk-redeploy.md) od začátku.

> [!IMPORTANT]
> Pokud chcete monitorovat průběh nasazení po restartování hostitele ASDK, musíte se přihlásit jako AzureStack\AzureStackAdmin.. Pokud se přihlásíte jako místní správce po restartování hostitelského počítače (a připojíte se k azurestack. místní doméně), neuvidíte průběh nasazení. Neprovádějte znovu nasazení, místo toho se přihlaste jako AzureStack\AzureStackAdmin se stejným heslem jako místní správce, aby se ověřilo, že je instalace spuštěná.


#### <a name="azure-ad-deployment-script-examples"></a>Příklady skriptu nasazení služby Azure AD
Můžete skriptovat celé nasazení služby Azure AD. Tady je několik příkladů s komentáři, které obsahují některé volitelné parametry.

Pokud je vaše identita Azure AD přidružená jenom k **jednomu** adresáři služby Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Pokud je vaše identita Azure AD přidružená k více **než jednomu** adresáři služby Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Pokud vaše prostředí nemá protokol DHCP povolený, musíte do jedné z výše uvedených možností zahrnout následující další parametry (příklad poskytnutého použití): 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>ASDK InstallAzureStackPOC. ps1 – nepovinné parametry

|Parametr|Požadováno/volitelné|Popis|
|-----|-----|-----|
|AdminPassword|Požaduje se|Nastaví účet místního správce a všechny ostatní uživatelské účty na všech virtuálních počítačích vytvořených jako součást nasazení ASDK. Toto heslo se musí shodovat s aktuálním místním heslem správce na hostiteli.|
|InfraAzureDirectoryTenantName|Požaduje se|Nastaví adresář tenanta. Pomocí tohoto parametru můžete zadat konkrétní adresář, ve kterém má účet Azure AD oprávnění ke správě více adresářů. Úplný název tenanta Azure AD ve formátu. onmicrosoft.com nebo název vlastní domény ověřený službou Azure AD.|
|TimeServer|Požaduje se|Pomocí tohoto parametru můžete zadat konkrétní časový server. Tento parametr se musí zadat jako platná časová IP adresa serveru. Názvy serverů nejsou podporované.|
|InfraAzureDirectoryTenantAdminCredential|Nepovinné|Nastaví Azure Active Directory uživatelské jméno a heslo. Tyto přihlašovací údaje Azure musí být ID organizace.|
|InfraAzureEnvironment|Nepovinné|Vyberte prostředí Azure, ve kterém chcete zaregistrovat toto Azure Stack nasazení. Mezi možnosti patří globální Azure, Azure-Čína, Azure-US státní správa.|
|DNSForwarder|Nepovinné|Server DNS se vytvoří jako součást nasazení Azure Stack. Pokud chcete počítačům v řešení umožnit překlad názvů mimo razítko, zadejte svůj stávající server DNS infrastruktury. Server DNS v rámci razítka přepošle neznámé požadavky na překlad názvů na tento server.|
|Znovu spustit|Nepovinné|Pomocí tohoto příznaku znovu spusťte nasazení. Použije se veškerý předchozí vstup. Opětovné zadání dříve zadaných dat není podporováno, protože je generováno několik jedinečných hodnot a použito pro nasazení.|


## <a name="perform-post-deployment-configurations"></a>Provedení konfigurací po nasazení
Po instalaci ASDK je potřeba provést několik doporučených kontrol po instalaci a změn konfigurace. Pomocí rutiny Test-AzureStack ověřte, že se instalace úspěšně nainstalovala, a pak Azure Stack nainstalujte nástroje PowerShell a GitHub.

Doporučujeme, abyste obnovili zásadu vypršení platnosti hesla, abyste se ujistili, že heslo pro hostitele ASDK nevyprší před skončením zkušebního období.

> [!NOTE]
> Volitelně můžete také nakonfigurovat [Azure Stack nastavení telemetrie](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *po* instalaci nástroje ASDK.

**[Úkoly nasazení po ASDK](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Registrace v Azure
Azure Stack s Azure je nutné zaregistrovat, aby bylo možné [stáhnout Azure Marketplace položky](../operator/azure-stack-create-and-publish-marketplace-item.md) do Azure Stack.

**[Registrace Azure Stack s využitím Azure](asdk-register.md)**

## <a name="next-steps"></a>Další kroky
Blahopřejeme! Po dokončení tohoto postupu budete mít prostředí ASDK s portálem pro správu `https://adminportal.local.azurestack.external` i uživatele. `https://portal.local.azurestack.external` 

[Úlohy konfigurace instalace po ASDK](asdk-post-deploy.md)

