---
title: Přidání registru kontejneru do centra Azure Stack | Microsoft Docs
titleSuffix: Azure Stack
description: Naučte se, jak přidat registr kontejnerů do webu Azure Stack hub Marketplace.
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
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: chasat
ms.lastreviewed: 12/17/2019
ms.openlocfilehash: 875eaf68324993be7029cfedd3d376ea38184a06
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874366"
---
# <a name="add-a-container-registry-to-azure-stack-hub"></a>Přidání registru kontejneru do centra Azure Stack

Registr kontejnerů můžete přidat do svého tržiště centra Azure Stack, aby uživatelé mohli nasadit a udržovat vlastní registr kontejnerů. Tato šablona řešení nainstaluje a nakonfiguruje Open Source Docker Container Registry v předplatném uživatele, které běží na AKS Base Ubuntu 16,04-LTS. Šablona podporuje nasazení připojená i odpojená (air-gapped) a podporuje jak Azure Active Directory (AAD), tak i služba Active Directory federované služby (AD FS) nasazené Azure Stack rozbočovače.

## <a name="get-the-marketplace-item"></a>Získat položku Marketplace

Položku Marketplace **šablony Container Registry** můžete najít v následujícím úložišti GitHub: https://github.com/msazurestackworkloads/azurestack-gallery/releases/download/registry-v1.0.2/Microsoft.AzureStackContainerRegistry.1.0.2.azpkg . Položka Marketplace je k dispozici na portálu v části vybrat odběry.

Můžete také přidat položku (zatížení na straně) do Marketplace pomocí Microsoft. AzureStackDockerContainerRegistry. 1.0.2. azpkg. Ke skriptům v tomto článku lze získat pøístup stažením úložiště Git jako (ZIP) balíčku z https://github.com/msazurestackworkloads/azurestack-gallery/archive/master.zip a extrahováním souborů. Skript můžete najít ve `azurestack-gallery-master\registry\scripts` složce.

## <a name="prerequisites"></a>Požadavky

Před přidáním položky Container Registry Marketplace do centra Azure Stack budete muset mít následující položky.

| Položka | Typ | Podrobnosti |
| --- | --- | --- |
| Moduly PowerShellu centra Azure Stack (AZS. Gallery. admin) | Moduly PowerShellu | Vyžaduje se pouze v případě, že se na straně načítá položka galerie šablon registru kontejnerů, moduly PowerShellu Azure Stack centra slouží k přidávání a odebírání položek galerie.<br>[Instalace modulů Azure Stack PowerShellu](../../operator/azure-stack-powershell-install.md) |
| Šablona Container Registry | Položka Marketplace | Aby bylo možné nasadit registr kontejnerů jako uživatele centra Azure Stack, musí být položka Marketplace pro šablonu registru kontejneru k dispozici ve vašem předplatném, nebo ji ručně přidat (načetli) do svého tržiště Azure Stack hub. Při načítání na straně stran podle pokynů načtěte balíček v `readme.md` [úložišti GitHub](https://github.com/msazurestackworkloads/azurestack-gallery/releases/tag/registry-v1.0.1). |
| AKS Base Ubuntu 16,04 – obrázek LTS, minimální verze vydaná v září 2019 | Položka Marketplace | Aby mohli uživatelé centra Azure Stack nasadit registr kontejnerů, je nutné, aby byla na webu Marketplace dostupná základní bitová kopie AKS. Šablona registru kontejnerů používá bitovou kopii při Ubuntu virtuálním počítači, který hostuje binární soubory registru kontejneru Docker. |
| Rozšíření vlastních skriptů pro Linux 2,0 | Položka Marketplace | Aby mohli uživatelé centra Azure Stack nasadit registr kontejnerů, musíte na webu Marketplace zpřístupnit rozšíření vlastních skriptů pro Linux. Nasazení šablony registru kontejnerů používá rozšíření ke konfiguraci registru. |
| Certifikát SSL | Certifikát | Uživatelé, kteří nasazují šablonu registru kontejnerů, musí zadat certifikát PFX, který se používá při konfiguraci šifrování SSL pro službu registru. Pokud používáte skript, budete muset spustit relaci PowerShellu z příkazového řádku se zvýšenými oprávněními. To by nemělo být spuštěno na DVM nebo HLH.<br>Obecné pokyny týkající se požadavků na certifikáty PKI pro Azure Stack centra pomocí veřejných nebo privátních nebo podnikových certifikátů najdete v této dokumentaci v tématu [požadavky na certifikát infrastruktury veřejných klíčů (PKI) Azure Stack hub](../../operator/azure-stack-pki-certs.md) .<br>Plně kvalifikovaný název domény pro certifikát by měl postupovat podle tohoto vzoru, `<vmname>.<location>.cloudapp.<fqdn>` Pokud nepoužíváte vlastní položku domény nebo DNS pro koncový bod. Název by měl začínat písmenem a obsahovat alespoň dvě písmena, používejte jenom malá písmena a musí být dlouhé aspoň tři znaky. |
| Princip služby (SPN) | Registrace aplikace | Chcete-li nasadit a nakonfigurovat registr kontejnerů, je třeba vytvořit registraci aplikace, označovanou také jako instanční objekt (SPN). Tento hlavní název služby se používá při konfiguraci virtuálního počítače a registru pro přístup k Microsoft Azure prostředkům Key Vault a účtu úložiště vytvořeným před nasazením položky Marketplace.<br>Hlavní název služby (SPN) by se měl vytvořit v AAD v rámci tenanta, ke kterému se přihlašujete, na portálu pro uživatele centra Azure Stack. Pokud použijete AD FS, vytvoří se v místním adresáři.<br>Podrobnosti o tom, jak vytvořit hlavní název služby (SPN) pro metody ověřování AAD i AD FS, najdete [v následujících pokynech](../../operator/azure-stack-create-service-principals.md).<br> **Důležité**: Pokud chcete nasadit aktualizace, budete muset uložit ID a tajný kód aplikace hlavního názvu služby (SPN).<br> |
| Uživatelské jméno a heslo registru | Přihlašovací údaje | Je nasazený a nakonfigurovaný zdrojový registr kontejneru Docker, který má povolené základní ověřování. Chcete-li získat přístup k registru pomocí příkazů Docker pro odesílání a vyžádání imagí, je nutné zadat uživatelské jméno a heslo. Uživatelské jméno a heslo jsou bezpečně uložené v Key Vaultm úložišti.<br>**Důležité**: Pokud se chcete přihlásit k registru a vkládat a vyžádat image, budete muset uložit uživatelské jméno a heslo registru. |
| Veřejný a privátní klíč SSH | Přihlašovací údaje | K řešení problémů s nasazením nebo běhovými problémy s virtuálním počítačem je třeba poskytnout veřejný klíč SSH pro nasazení a odpovídající privátní klíč. Doporučuje se použít formát OpenSSH ssh-keygen pro vygenerování dvojice privátního a veřejného klíče, protože diagnostické skripty pro shromažďování protokolů tento formát vyžadují.<br>**Důležité**: abyste měli přístup k NASAZENému virtuálnímu počítači pro řešení potíží, budete muset mít přístup k veřejným a soukromým klíčům. |
| Přístup k portálům pro správu a uživatelským portálům a koncovým bodům správy | Připojení | Tato příručka předpokládá, že nasazujete a konfigurujete registr ze systému s možností připojení k systému Azure Stack hub. |

Skript `Pre-reqs` vytvoří jiné vstupy potřebné k nasazení položky Marketplace.

## <a name="installation-steps"></a>Kroky instalace

Instalace šablony registru kontejnerů vyžaduje vytvoření několika prostředků před nasazením.

1. Připojte se k Azure Stack hub jako uživatel pomocí PowerShellu a vyberte předplatné pomocí rutiny `Select-AzureRmSubscription –Subscription <subscription guid>` . Další informace o připojení jako uživatel k Azure Stack centra PowerShellu najdete v tématu [připojení k Azure Stack pomocí prostředí PowerShell jako uživatel](../../user/azure-stack-powershell-configure-user.md).

2. Spusťte `Import-Modules .\\pre-reqs.ps1` , chcete-li importovat moduly v rámci `pre-reqs.ps1` skriptu. Skript vytvoří skupinu prostředků, účet úložiště, kontejner objektů blob, Key Vault úložiště, přiřadí oprávnění k přístupu k hlavnímu názvu služby a zkopíruje certifikáty a uživatelské jméno a heslo pro registr do Key Vault úložiště.

3. Z příkazového řádku se zvýšenými oprávněními spusťte následující rutinu s použitím hodnot pro vaše prostředí pro parametry:

    ```powershell  
         Set-ContainerRegistryPrerequisites -Location Shanghai `
         -ServicePrincipalId <spn app id> `
         -ResourceGroupName newregreq1 `
         -StorageAccountName newregsa1 `
         -StorageAccountBlobContainer newregct1 `
         -KeyVaultName newregkv1 `
         -CertificateSecretName containersecret2 `
         -CertificateFilePath C:\crinstall\shanghairegcert.pfx `
         -CertificatePassword <cert password> `
         -RegistryUserName admin `
         -RegistryUserPassword <password> 
    ```

    | Parametr | Podrobnosti |
    | --- | --- |
    | $Location | Někdy se označuje jako název oblasti. |
    | $ResourceGroupName | Zadejte název skupiny prostředků, ve které chcete vytvořit účet úložiště a úložiště Key Vault. Při nasazování položky Marketplace budete určovat jinou skupinu prostředků. |
    | $StorageAccountName | Zadejte název účtu úložiště, který se má vytvořit pro registr kontejnerů, který se má použít při ukládání obrázků, které byly vloženy. |
    | $StorageAccountBlobContainer | Zadejte název kontejneru objektů blob, který se má vytvořit, který se používá pro úložiště imagí. |
    | $KeyVaultName | Zadejte název úložiště Key Vault, který se má vytvořit pro uložení certifikátu a hodnoty uživatelského jména a hesla. |
    | $CertificateSecretName | Zadejte název tajného klíče vytvořeného v Key Vault pro uložení certifikátu PFX. |
    | $CertificateFilePath | Zadejte cestu k certifikátu PFX. |
    | $CertificatePassword | Zadejte heslo pro certifikát PFX. |
    | $ServicePrincipalId | Zadejte AppID pro hlavní název služby (SPN). |
    | $RegistryUserName | Zadejte uživatelské jméno pro přístup ke službě registru pomocí základní autorizace. |
    | $RegistryUserPassword | Zadejte heslo pro uživatele registru. |

1. Po dokončení skriptu si všimněte konce skriptu včetně parametrů, které se použijí v nasazení šablony. Při kopírování a vkládání těchto hodnot může být zavedeno místo, pokud se hodnota zalomí.

    ```powershell  
    ----------------------------------------------------------------
    PFX KeyVaultResourceId       : /subscriptions/<subcription id>/resourceGroups/newr
    egreg1/providers/Microsoft.KeyVault/vaults/newregkv1
    PFX KeyVaultSecretUrl        : https://newregkv1.vault.shanghai.azurestack.corp.microsoft.com:443/secr
    ets/containersecret1/37cc2f7ea1c44ad7b930e2c237a14949
    PFX Certificate Thumbprint   : 64BD5F3BC41DCBC6495998900ED322D8110DE25E
    ----------------------------------------------------------------
    StorageAccountResourceId     : /subscriptions/<subcription id>/resourcegroups/newr
    egreg1/providers/Microsoft.Storage/storageAccounts/newregsa1
    Blob Container               : newregct1
    ----------------------------------------------------------------
    
    Skus : aks-ubuntu-1604-201909
    
    
    Skus : aks-ubuntu-1604-201910
    
    ---------------------------------------------------------------- 
    
    ```

1. Otevřete portál Azure Stack hub User Portal.

2. Vyberte **vytvořit**  >    >  **šablonu COMPUTE Container Registry**.

    ![Šablona registru kontejneru](./media/container-registry-template-install-tzl/image1.png)

3. Vyberte předplatné, skupinu prostředků a umístění pro nasazení šablony registru kontejnerů.

    ![Šablona registru kontejneru](./media/container-registry-template-install-tzl/image2.png)

4. Dokončete podrobnosti konfigurace virtuálního počítače. SKU image má výchozí hodnotu **AKS-Ubuntu-1604-201909**; výstup `Set-ContainerRegistryPrerequisites` funkce ale obsahuje seznam dostupných skladových položek, které se použijí pro nasazení. Pokud existuje více než jedna SKU, vyberte nejnovější SKU pro nasazení.

    ![Šablona registru kontejneru](./media/container-registry-template-install-tzl/image3.png)

    | Parametr | Podrobnosti |
    | --- | --- |
    | Uživatelské jméno | Zadejte uživatelské jméno pro přihlášení k virtuálnímu počítači. |
    | Veřejný klíč SSH | Zadejte veřejný klíč SSH, který se používá k ověření pomocí virtuálního počítače pomocí protokolu SSH. |
    | Velikost | Vyberte velikost virtuálního počítače, který se má nasadit. |
    | Veřejná IP adresa | Zadejte název a typ IP adresy (dynamické nebo statické) pro tento virtuální počítač. Název domény je neplatný. Název může obsahovat jenom malá písmena, číslice a pomlčky. Prvním znakem musí být písmeno. Posledním znakem musí být písmeno nebo číslo. Hodnota musí být dlouhá od 3 do 63 znaků.  |
    | Popisek názvu domény | Zadejte předponu DNS pro váš registr. Celý plně kvalifikovaný název domény by měl odpovídat hodnotě CN certifikátu PFX vytvořenému pro registr. |
    | Repliky | Zadejte počet replik kontejnerů, které se mají spustit. |
    | SKU image | Zadejte SKLADOVOU položku image, která se má použít pro nasazení. Dostupné SKU pro základní image AKS jsou uvedené `Set-ContainerRegistryPrerequisites` skriptem. |
    | ID klienta hlavního názvu služby (SPN) | Zadejte ID aplikace hlavního názvu služby (SPN). |
    | Heslo SPN/potvrzení hesla | Zadejte tajný kód ID aplikace hlavního názvu služby (SPN). |

1. Dokončete konfiguraci úložiště a Key Vault.

    ![Šablona registru kontejneru](./media/container-registry-template-install-tzl/image4.png)

    | Parametr | Podrobnosti |
    | --- | --- |
    | ID prostředku rozšířeného účtu úložiště | Zadejte ID prostředku účtu úložiště, které `pre-reqs` skript vrátil. |
    | Existující kontejner objektů BLOB back-endu | Zadejte název kontejneru objektů blob, který je uvedený ve výstupu skriptu pre-reqs. |
    | ID prostředku Key Vault certifikátu PFX | Zadejte ID prostředku Key Vault, které `pre-reqs` skript vrátil. |
    | Adresa URL tajného kódu certifikátu PFX Key Vault | Zadejte adresu URL certifikátu vrácenou skriptem pre-reqs. |
    | Kryptografický otisk certifikátu PFX | Zadejte kryptografický otisk certifikátu vrácený skriptem pre-reqs. |

1. Po zadání všech hodnot a nasazení šablony řešení bude trvat 10-15 minut, než se virtuální počítač nasadí a nakonfiguruje služba registru.

    ![Šablona registru kontejneru](./media/container-registry-template-install-tzl/image5.png)

2. Pokud chcete otestovat registr, otevřete instanci Docker CLI z počítače nebo virtuálního počítače s přístupem k adrese URL registru.

    > [!Note]
    >  Pokud jste použili certifikát podepsaný svým držitelem nebo certifikát, který není známý pro virtuální počítač, který používáte pro přístup k registru, budete ho muset nainstalovat na virtuální počítač a restartovat Docker.

## <a name="pushing-and-pulling-images-from-container-registry"></a>Doručování a přijímání imagí z registru kontejnerů

1. Přihlaste se pomocí `docker login –u \<username> -p \<password>` .
2. Vyžádání a obrázek ze známého registru.
3. Označte image, která se má cílit na nově nasazený registr kontejnerů Docker.
4. Nahrajte image do nového cílového registru.

Například:

```powershell  
PS C:\> docker pull mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
1.0: Pulling from azureiotedge-simulated-temperature-sensor
5d20c808ce19: Already exists
656de8e592c3: Already exists
1e1868d1f676: Already exists
f3fb1b0d620f: Pulling fs layer
26224c4fc11a: Pulling fs layer
c459a69d65b2: Pulling fs layer
c459a69d65b2: Verifying Checksum
c459a69d65b2: Download complete
f3fb1b0d620f: Download complete
f3fb1b0d620f: Pull complete
26224c4fc11a: Verifying Checksum
26224c4fc11a: Pull complete
c459a69d65b2: Pull complete
Digest: sha256:dd64ff0918459184574e840ee97aa9f1bacd40aa37c972984ea10f0ecd719d5f
Status: Downloaded newer image for mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

PS C:\> docker tag mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0    myreg.orlando.cloudapp.azurestack.corp.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

PS C:\> docker login -u admin -p admin myreg.orlando.cloudapp.azurestack.corp.microsoft.com
docker : WARNING! Using --password via the CLI is insecure. Use --password-stdin.
At line:1 char:1
+ docker login -u admin -p admin myreg.orlando.cloudapp.azurestack.corp ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (WARNING! Using ...password-stdin.:String) [], RemoteException
    + FullyQualifiedErrorId : NativeCommandError
 
Login Succeeded

PS C:\> docker push myreg.orlando.cloudapp.azurestack.corp.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
The push refers to repository [myreg.orlando.cloudapp.azurestack.corp.microsoft.com/azureiotedge-simulated-temperature-sensor]
d377c212e567: Preparing
0481685b758f: Preparing
15474c03a0b6: Preparing
8cdec5be5964: Preparing
79116d3fb0bf: Preparing
3fc64803ca2d: Preparing
3fc64803ca2d: Waiting
79116d3fb0bf: Mounted from azureiotedge-agent
8cdec5be5964: Mounted from azureiotedge-agent
15474c03a0b6: Pushed
d377c212e567: Pushed
3fc64803ca2d: Mounted from azureiotedge-agent
0481685b758f: Pushed
1.0: digest: sha256:f5fbc4a5c6806e12cafe1c363fea2b6cbd98a211b8153c5b19aca1386bfa6ecb size: 1576 

```

## <a name="known-issues"></a>Známé problémy

Verze služby Docker Container Registry nasazená touto šablonou je 2,7. Tato verze má známý problém, který znemožňuje vložení a nastavování imagí kontejnerů Windows. Problém je sledován s následující položkou GitHubu [https://github.com/docker/distribution-library-image/issues/89](https://github.com/docker/distribution-library-image/issues/89) .

## <a name="next-steps"></a>Další kroky

[Přehled Azure Stack Marketplace](../../operator/azure-stack-marketplace.md)
