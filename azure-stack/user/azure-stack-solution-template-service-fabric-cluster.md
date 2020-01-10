---
title: Nasazení zabezpečeného clusteru Service Fabric v centru Azure Stack | Microsoft Docs
description: Naučte se, jak nasadit zabezpečený Cluster Service Fabric v centru Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: shnatara
ms.lastreviewed: 09/25/2019
ms.openlocfilehash: bb0e9fdb3e1ce1c3778d1167ca76cddae3d67aa7
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75819195"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack-hub"></a>Nasazení clusteru Service Fabric v centru Azure Stack

Pomocí položky **Service Fabric clusteru** z Azure Marketplace nasaďte zabezpečený Service Fabric Cluster v Azure Stack hub. 

Další informace o práci s Service Fabric najdete v tématu Přehled [scénářů zabezpečení](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) [azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) a Service Fabric v dokumentaci k Azure.

Cluster Service Fabric v centru Azure Stack nepoužívá poskytovatele prostředků Microsoft. ServiceFabric. Místo toho se v centru Azure Stack Service Fabric cluster používá sada škálování virtuálních počítačů s předinstalovaným softwarem pomocí [Konfigurace požadovaného stavu (DSC)](https://docs.microsoft.com/powershell/scripting/dsc/overview/overview).

## <a name="prerequisites"></a>Požadavky

K nasazení Service Fabric clusteru se vyžadují tyto požadavky:
1. **Certifikát clusteru**  
   Jedná se o certifikát serveru X. 509, který přidáte do Key Vault při nasazování Service Fabric. 
   - **Propojené sítě** v tomto certifikátu se musí shodovat s plně kvalifikovaným názvem domény (FQDN) Service Fabricho clusteru, který vytvoříte. 
   - Formát certifikátu musí být PFX, protože jsou vyžadovány veřejné i privátní klíče. 
     Viz [požadavky](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) pro vytvoření tohoto certifikátu na straně serveru.

     > [!NOTE]  
     > Pro účely testování můžete použít místo certifikátu serveru X. 509 podepsaného svým držitelem (samo-signed certificate). Certifikáty podepsané svým držitelem nemusí odpovídat plně kvalifikovanému názvu domény clusteru.

1. **Certifikát klienta správce**  
   Jedná se o certifikát, který klient používá k ověření v clusteru Service Fabric, který může být podepsaný svým držitelem. Viz [požadavky](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) pro vytvoření certifikátu klienta.

1. **V tržišti centra Azure Stack musí být k dispozici následující položky:**
    - **Windows server 2016** – šablona používá k vytvoření clusteru image Windows serveru 2016.  
    - **Rozšíření vlastních skriptů** – rozšíření virtuálního počítače od Microsoftu  
    - **Konfigurace požadovaného stádia prostředí PowerShell** – rozšíření virtuálního počítače od Microsoftu


## <a name="add-a-secret-to-key-vault"></a>Přidání tajného klíče do služby Key Vault
Chcete-li nasadit cluster Service Fabric, je nutné zadat správný *identifikátor Key Vault tajného klíče* nebo adresu URL pro Service Fabric clusteru. Šablona Azure Resource Manager jako vstup převezme Key Vault. Pak šablona načte certifikát clusteru při instalaci Service Fabricho clusteru.

> [!IMPORTANT]  
> K přidání tajného klíče do Key Vault pro použití s Service Fabric je nutné použít PowerShell. Nepoužívejte portál.  

Pomocí následujícího skriptu vytvořte Key Vault a přidejte do něj *certifikát clusteru* . (Viz [požadavky](#prerequisites).) Před spuštěním skriptu zkontrolujte vzorový skript a aktualizujte uvedené parametry tak, aby odpovídaly vašemu prostředí. Tento skript také vypíše hodnoty, které je třeba zadat do šablony Azure Resource Manager. 

> [!TIP]  
> Předtím, než může být skript úspěšný, musí existovat veřejná nabídka, která zahrnuje služby pro výpočetní prostředky, síť, úložiště a Key Vault. 

  ```powershell
    function Get-ThumbprintFromPfx($PfxFilePath, $Password) 
        {
            return New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($PfxFilePath, $Password)
        }
    
    function Publish-SecretToKeyVault ($PfxFilePath, $Password, $KeyVaultName)
       {
            $keyVaultSecretName = "ClusterCertificate"
            $certContentInBytes = [io.file]::ReadAllBytes($PfxFilePath)
            $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    
            $jsonObject = ConvertTo-Json -Depth 10 ([pscustomobject]@{
                data     = $pfxAsBase64EncodedString
                dataType = 'pfx'
                password = $Password
            })
    
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName -SecretValue $secret
            
            $pfxCertObject = Get-ThumbprintFromPfx -PfxFilePath $PfxFilePath -Password $Password
    
            Write-Host "KeyVault id: " -ForegroundColor Green
            (Get-AzureRmKeyVault -VaultName $KeyVaultName).ResourceId
            
            Write-Host "Secret Id: " -ForegroundColor Green
            (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName).id
    
            Write-Host "Cluster Certificate Thumbprint: " -ForegroundColor Green
            $pfxCertObject.Thumbprint
       }
    
    #========================== CHANGE THESE VALUES ===============================
    $armEndpoint = "https://management.local.azurestack.external"
    $tenantId = "your_tenant_ID"
    $location = "local"
    $clusterCertPfxPath = "Your_path_to_ClusterCert.pfx"
    $clusterCertPfxPassword = "Your_password_for_ClusterCert.pfx"
    #==============================================================================
    
    Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint $armEndpoint
    Login-AzureRmAccount -Environment AzureStack -TenantId $tenantId
    
    $rgName = "sfvaultrg"
    Write-Host "Creating Resource Group..." -ForegroundColor Yellow
    New-AzureRmResourceGroup -Name $rgName -Location $location
    
    Write-Host "Creating Key Vault..." -ForegroundColor Yellow
    $Vault = New-AzureRmKeyVault -VaultName sfvault -ResourceGroupName $rgName -Location $location -EnabledForTemplateDeployment -EnabledForDeployment -EnabledForDiskEncryption
    
    Write-Host "Publishing certificate to Vault..." -ForegroundColor Yellow
    Publish-SecretToKeyVault -PfxFilePath $clusterCertPfxPath -Password $clusterCertPfxPassword -KeyVaultName $vault.VaultName
   ``` 


Další informace najdete v tématu [správa Key Vault v centru Azure Stack pomocí PowerShellu](azure-stack-key-vault-manage-powershell.md).

## <a name="deploy-the-marketplace-item"></a>Nasadit položku Marketplace

1. Na portálu User Portal přejít na **+ vytvořit prostředek** > **COMPUTE** > **Service Fabric clusteru**. 

   ![Vybrat Cluster Service Fabric](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

2. Pro každou stránku, například *základy*, vyplňte formulář nasazení. Pokud si nejste jisti hodnotou, použijte výchozí nastavení.

    Pro nasazení do odpojeného centra Azure Stack nebo pro nasazení jiné verze Service Fabric si Stáhněte balíček Service Fabric pro nasazení a jeho odpovídající balíček runtime a zahostte ho v Azure Stack objekt BLOB centra. Zadejte tyto hodnoty do polí **Adresa URL balíčku pro nasazení Service Fabric** a **Adresa URL balíčku za běhu Service Fabric** .
    > [!NOTE]  
    > Existují problémy s kompatibilitou mezi nejnovějším vydáním Service Fabric a odpovídající sadou SDK. Dokud se tento problém nevyřeší, zadejte prosím následující parametry URL balíčku pro nasazení a adresu URL balíčku za běhu. V opačném případě se nasazení nezdaří.
    > - Adresa URL balíčku nasazení Service Fabric: <https://download.microsoft.com/download/8/3/6/836E3E99-A300-4714-8278-96BC3E8B5528/6.5.641.9590/Microsoft.Azure.ServiceFabric.WindowsServer.6.5.641.9590.zip>
    > - Adresa URL balíčku Service Fabric Runtime: <https://download.microsoft.com/download/B/0/B/B0BCCAC5-65AA-4BE3-AB13-D5FF5890F4B5/6.5.641.9590/MicrosoftAzureServiceFabric.6.5.641.9590.cab>
    >
    > V případě odpojených nasazení stáhněte tyto balíčky ze zadaného umístění a hostovat je místně na Azure Stackovém objektu BLOB centra.

   ![Základy](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

    
3. Na stránce *nastavení sítě* můžete zadat konkrétní porty, které se otevřou pro aplikace:

   ![Nastavení sítě](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

4. Na stránce *zabezpečení* přidejte hodnoty, které jste získali z [Vytvoření Azure Key Vault](#add-a-secret-to-key-vault) a odeslání tajného klíče.

   Pro *kryptografický otisk certifikátu klienta správce*zadejte kryptografický otisk *certifikátu klienta správce*. (Viz [požadavky](#prerequisites).)
   
   - Zdroj Key Vault: zadejte celý `keyVault id` řetězec z výsledků skriptu. 
   - Adresa URL certifikátu clusteru: zadejte celou adresu URL z `Secret Id` z výsledků skriptu. 
   - Kryptografický otisk certifikátu clusteru: Určete *kryptografický otisk certifikátu clusteru* z výsledků skriptu.
   - Adresa URL certifikátu serveru: Pokud chcete použít samostatný certifikát z certifikátu clusteru, nahrajte certifikát do trezoru klíčů a zadejte úplnou adresu URL tajného kódu. 
   - Kryptografický otisk certifikátu serveru: zadejte kryptografický otisk pro certifikát serveru.
   - Kryptografické otisky klientského certifikátu pro správce: Určete *kryptografický otisk certifikátu klienta pro správu* , který je vytvořený v části požadavky. 

   ![Výstup skriptu](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![Zabezpečení](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

5. Dokončete průvodce a pak vyberte **vytvořit** a nasaďte cluster Service Fabric.



## <a name="access-the-service-fabric-cluster"></a>Přístup ke clusteru Service Fabric
Ke clusteru Service Fabric můžete přistupovat pomocí Service Fabric Explorer nebo Service Fabric PowerShellu.


### <a name="use-service-fabric-explorer"></a>Použít Service Fabric Explorer
1.  Zajistěte, aby prohlížeč měl přístup k vašemu klientskému certifikátu správce a mohl by se ověřit do vašeho clusteru Service Fabric.  

    a. Otevřete Internet Explorer a přejít na **Možnosti internetu** > **obsahu** > **certifikáty**.
  
    b. V části certifikáty vyberte **importovat** a spusťte *Průvodce importem certifikátu*a potom klikněte na **Další**. Na stránce *importovat soubor* klikněte na **Procházet**a vyberte **certifikát klienta správce** , který jste zadali pro šablonu Azure Resource Manager.
        
       > [!NOTE]  
       > Tento certifikát není certifikátem clusteru, který byl dříve přidán do Key Vault.  

    c. Ujistěte se, že je v rozevíracím seznamu rozšíření okna Průzkumníka souborů vybrána možnost "osobní informace Exchange".  

       ![Personal Information Exchange](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. Na stránce *úložiště certifikátů* vyberte **osobní**a pak dokončete průvodce.  
       ![úložiště certifikátů](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
1. Zjištění plně kvalifikovaného názvu domény Service Fabric clusteru:  

    a. Přejděte do skupiny prostředků, která je přidružená k vašemu Service Fabric clusteru, a vyhledejte prostředek *veřejné IP adresy* . Vyberte objekt přidružený k veřejné IP adrese a otevřete tak okno *veřejné IP adresy* .  

      ![Veřejná IP adresa](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. V okně veřejná IP adresa se plně kvalifikovaný název domény zobrazí jako *název DNS*.  

      ![Název DNS](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

1. Pokud chcete najít adresu URL pro Service Fabric Explorer a koncový bod připojení klienta, Projděte si výsledky Template deployment.

1. V prohlížeči přejděte na <https://*FQDN*:19080>. V kroku 2 nahraďte *plně kvalifikovaný název* domény plně kvalifikovaným názvem domény vašeho clusteru Service Fabric.   
   Pokud jste použili certifikát podepsaný svým držitelem, zobrazí se upozornění, že připojení není zabezpečené. Chcete-li pokračovat na web, vyberte **Další informace**a potom **přejděte na webovou stránku**. 

1. Chcete-li provést ověření v lokalitě, je nutné vybrat certifikát, který chcete použít. Vyberte **Další možnosti**, vyberte příslušný certifikát a potom se kliknutím na tlačítko **OK** připojte k Service Fabric Explorer. 

   ![Ověření](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



### <a name="use-service-fabric-powershell"></a>Použití prostředí Service Fabric PowerShell

1. Nainstalujte *sadu Microsoft Azure Service Fabric SDK* z části [Příprava vývojového prostředí ve Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) v dokumentaci k Azure Service Fabric.  

1. Po dokončení instalace nakonfigurujte proměnné prostředí systému, aby se zajistilo, že jsou rutiny Service Fabric dostupné z PowerShellu.  
    
    a. V **Ovládacích panelech** > **systém a zabezpečení** > **systém**a pak vyberte **Upřesnit nastavení systému**.  
    
      ![Ovládací panely](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. Na kartě **Upřesnit** v okně *Vlastnosti systému*vyberte **proměnné prostředí**.  

    c. V případě *systémových proměnných*upravte **cestu** a ujistěte se, že **C:\\programové soubory\\Microsoft Service Fabric\\bin\\Fabric\\Fabric. kód** je na začátku seznamu proměnných prostředí.  

      ![Seznam proměnných prostředí](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

1. Po změně pořadí proměnných prostředí restartujte PowerShell a potom spuštěním následujícího skriptu PowerShellu Získejte přístup ke clusteru Service Fabric:

   ```powershell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ```
   
   > [!NOTE]  
   > Před názvem clusteru ve skriptu není žádný *https://* . Je vyžadován port 19000.

## <a name="next-steps"></a>Další kroky

[Nasazení Kubernetes do centra Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)
