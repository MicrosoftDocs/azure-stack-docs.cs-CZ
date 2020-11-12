---
title: Příprava certifikátů PKI Azure Stack hub pro nasazení nebo otočení
titleSuffix: Azure Stack Hub
description: Naučte se, jak připravit certifikáty PKI pro nasazení Azure Stackho centra nebo pro otáčení tajných klíčů.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 10/19/2020
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: 86e3a87bf869d6bd9980746742a7ba03d142d5fe
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545019"
---
# <a name="prepare-azure-stack-hub-pki-certificates-for-deployment-or-rotation"></a>Příprava certifikátů PKI Azure Stack hub pro nasazení nebo otočení

Soubory certifikátů [získané od certifikační autority (CA)](azure-stack-get-pki-certs.md) musí být importovány a exportovány s vlastnostmi, které odpovídají požadavkům na certifikát centra Azure Stack.

V tomto článku se dozvíte, jak importovat, zabalit a ověřovat certifikáty, abyste se připravili na Azure Stack nasazení centra nebo rotaci tajných kódů. 

## <a name="prerequisites"></a>Požadavky

Před sbalením certifikátů PKI pro nasazení centra Azure Stack musí systém splňovat následující požadavky:

- Certifikáty vrácené certifikační autoritou se ukládají do jednoho adresáře ve formátu. cer (další konfigurovatelné formáty jako. CERT,. sst nebo. pfx).
- Windows 10 nebo Windows Server 2016 nebo novější
- Použijte stejný systém, který vygeneroval žádost o podepsání certifikátu (Pokud necílíte na certifikát, který je předbalený do PFXs).

Přejděte k příslušnému oddílu [Příprava certifikátů (Azure Stack Checker)](#prepare-certificates-azure-stack-readiness-checker) nebo [Příprava certifikátů (ruční kroky)](#prepare-certificates-manual-steps) .

## <a name="prepare-certificates-azure-stack-readiness-checker"></a>Příprava certifikátů (Kontrola připravenosti na Azure Stack)

Pomocí těchto kroků můžete balíčky certifikátů pomocí rutin PowerShellu pro kontrolu připravenosti Azure Stack:

1. Spuštěním následující rutiny nainstalujte modul pro kontrolu připravenosti na Azure Stack z příkazového řádku PowerShellu (5,1 nebo vyšší):

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
    ```
2. Zadejte **cestu** k souborům certifikátu. Například:

    ```powershell  
        $Path = "$env:USERPROFILE\Documents\AzureStack"
    ```

3. Deklarujte třídu **pfxPassword**. Například:

    ```powershell  
        $pfxPassword = Read-Host -AsSecureString -Prompt "PFX Password"
    ```
4. Deklarujete **ExportPath** , do kterého bude výsledný PFXs exportován. Například:

    ```powershell  
        $ExportPath = "$env:USERPROFILE\Documents\AzureStack"
    ```

5. Převod certifikátů na certifikáty centra Azure Stack. Například:

    ```powershell  
        ConvertTo-AzsPFX -Path $Path -pfxPassword $pfxPassword -ExportPath $ExportPath
    ```
8.  Zkontrolujte výstup:

    ```powershell  
    ConvertTo-AzsPFX v1.2005.1286.272 started.

    Stage 1: Scanning Certificates
        Path: C:\Users\[*redacted*]\Documents\AzureStack Filter: CER Certificate count: 11
        adminmanagement_east_azurestack_contoso_com_CertRequest_20200710235648.cer
        adminportal_east_azurestack_contoso_com_CertRequest_20200710235645.cer
        management_east_azurestack_contoso_com_CertRequest_20200710235644.cer
        portal_east_azurestack_contoso_com_CertRequest_20200710235646.cer
        wildcard_adminhosting_east_azurestack_contoso_com_CertRequest_20200710235649.cer
        wildcard_adminvault_east_azurestack_contoso_com_CertRequest_20200710235642.cer
        wildcard_blob_east_azurestack_contoso_com_CertRequest_20200710235653.cer
        wildcard_hosting_east_azurestack_contoso_com_CertRequest_20200710235652.cer
        wildcard_queue_east_azurestack_contoso_com_CertRequest_20200710235654.cer
        wildcard_table_east_azurestack_contoso_com_CertRequest_20200710235650.cer
        wildcard_vault_east_azurestack_contoso_com_CertRequest_20200710235647.cer

    Detected ExternalFQDN: east.azurestack.contoso.com

    Stage 2: Exporting Certificates
        east.azurestack.contoso.com\Deployment\ARM Admin\ARMAdmin.pfx
        east.azurestack.contoso.com\Deployment\Admin Portal\AdminPortal.pfx
        east.azurestack.contoso.com\Deployment\ARM Public\ARMPublic.pfx
        east.azurestack.contoso.com\Deployment\Public Portal\PublicPortal.pfx
        east.azurestack.contoso.com\Deployment\Admin Extension Host\AdminExtensionHost.pfx
        east.azurestack.contoso.com\Deployment\KeyVaultInternal\KeyVaultInternal.pfx
        east.azurestack.contoso.com\Deployment\ACSBlob\ACSBlob.pfx
        east.azurestack.contoso.com\Deployment\Public Extension Host\PublicExtensionHost.pfx
        east.azurestack.contoso.com\Deployment\ACSQueue\ACSQueue.pfx
        east.azurestack.contoso.com\Deployment\ACSTable\ACSTable.pfx
        east.azurestack.contoso.com\Deployment\KeyVault\KeyVault.pfx

    Stage 3: Validating Certificates.

    Validating east.azurestack.contoso.com-Deployment-AAD certificates in C:\Users\[*redacted*]\Documents\AzureStack\east.azurestack.contoso.com\Deployment 

    Testing: KeyVaultInternal\KeyVaultInternal.pfx
    Thumbprint: E86699****************************4617D6
        PFX Encryption: OK
        Expiry Date: OK
        Signature Algorithm: OK
        DNS Names: OK
        Key Usage: OK
        Key Length: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: ARM Public\ARMPublic.pfx
        ...
    Log location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    ConvertTo-AzsPFX Completed
    ```
    > [!NOTE]
    > Pro další využití použijte příkaz Get-Help ConvertTo-AzsPFX-Full pro další použití, například zakázání ověřování nebo filtrování pro různé formáty certifikátů.

    Po úspěšném ověření je možné provést nasazení nebo otočení bez jakýchkoli dalších kroků.

## <a name="prepare-certificates-manual-steps"></a>Příprava certifikátů (ruční postup)

Pomocí těchto kroků můžete balíčky certifikátů pro nové Azure Stack centra PKI použít k zabalení pomocí ručních kroků.

### <a name="import-the-certificate"></a>Importovat certifikát

1. Zkopírujte původní verze certifikátů [získané z vaší certifikační autority podle vlastního výběru](azure-stack-get-pki-certs.md) do adresáře na hostiteli nasazení. 
   > [!WARNING]
   > Nekopírujte soubory, které již byly naimportovány, exportovány nebo změněny jakýmkoli způsobem ze souborů poskytnutých přímo certifikační autoritou.

1. Pravým tlačítkem myši klikněte na certifikát a vyberte **instalovat certifikát** nebo **nainstalovat PFX** v závislosti na tom, jak byl certifikát dodán z vaší certifikační autority.

1. V **Průvodci importem certifikátu** vyberte jako umístění importu možnost **místní počítač** . Vyberte **Další**. Na následující obrazovce znovu vyberte Další.

    ![Umístění importu místního počítače pro certifikát](./media/prepare-pki-certs/1.png)

1. Zvolte možnost **umístit všechny certifikáty do následujícího úložiště** a pak jako umístění vyberte **důvěřovat podnikové síti** . Kliknutím na **OK** zavřete dialogové okno Výběr úložiště certifikátů a pak vyberte **Další**.

   ![Konfigurace úložiště certifikátů pro import certifikátu](./media/prepare-pki-certs/3.png)

   a. Při importu PFX se zobrazí další dialog. Na stránce **ochrana privátního klíče** zadejte heslo pro soubory certifikátů a potom povolte možnost **Označit tento klíč jako exportovatelný.** možnost, která vám umožní zálohovat nebo přenášet klíče později. Vyberte **Další**.

   ![Označit klíč jako exportovatelný](./media/prepare-pki-certs/2.png)

1. Kliknutím na **Dokončit** dokončete import.

> [!NOTE]
> Po importu certifikátu pro centrum Azure Stack se privátní klíč certifikátu uloží jako soubor PKCS 12 (PFX) do clusterovaného úložiště.

### <a name="export-the-certificate"></a>Export certifikátu

Otevřete konzolu MMC Správce certifikátů a připojte se k úložišti certifikátů místního počítače.

1. Otevřete konzolu Microsoft Management Console. Chcete-li otevřít konzolu nástroje ve Windows 10, klikněte pravým tlačítkem myši na **nabídku Start** , vyberte příkaz **Spustit** a zadejte příkaz **MMC** a stiskněte klávesu ENTER.

2. Vyberte **soubor**  >  **Přidat/odebrat modul snap-in** , pak vyberte **certifikáty** a vyberte **Přidat**.

    ![Přidat modul snap-in Certifikáty v konzole Microsoft Management Console](./media/prepare-pki-certs/mmc-2.png)

3. Vyberte položku **účet počítače** a pak vyberte **Další**. Vyberte **místní počítač** a pak **Dokončit**. Výběrem **OK** zavřete stránku přidat nebo odebrat Snap-In.

    ![Vybrat účet pro modul snap-in Certifikáty v konzole Microsoft Management Console](./media/prepare-pki-certs/mmc-3.png)

4. Přejděte na **certifikáty**  >  **důvěřovat**  >  **umístění certifikátu** organizace. Ověřte, že na pravé straně vidíte svůj certifikát.

5. Z hlavního panelu konzoly Správce certifikátů vyberte **Akce**  >  **všechny úkoly**  >  **exportovat**. Vyberte **Další**.

   > [!NOTE]
   > V závislosti na tom, kolik certifikátů centra Azure Stack máte, možná budete muset tento proces dokončit více než jednou.

6. Vyberte **Ano, exportovat privátní klíč** a pak vyberte **Další**.

7. V části formát souboru pro export:
    
   - **Pokud je to možné, vyberte zahrnout všechny certifikáty do certifikátu**.  
   - Vyberte **exportovat všechny rozšířené vlastnosti**.  
   - Vyberte možnost **Povolit ochranu osobních údajů certifikátu**.  
   - Vyberte **Další**.  
    
     ![Průvodce exportem certifikátu s vybranými možnostmi](./media/prepare-pki-certs/azure-stack-save-cert.png)

8. Vyberte **heslo** a zadejte heslo pro certifikáty. Vytvořte heslo, které splňuje následující požadavky na složitost hesla:

    * Minimální délka je osm znaků.
    * Alespoň tři z následujících znaků: velké písmeno, malé písmeno, číslice od 0-9, speciální znaky, abecední znak, který není velká a malá písmena.

    Poznamenejte si toto heslo. Použijete ho jako parametr nasazení.

9. Vyberte **Další**.

10. Vyberte název souboru a umístění souboru PFX, který chcete exportovat. Vyberte **Další**.

11. Vyberte **Dokončit**.

## <a name="next-steps"></a>Další kroky

[Ověření certifikátů PKI](azure-stack-validate-pki-certs.md)
