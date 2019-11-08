---
title: Příprava Azure Stack certifikátů PKI pro nasazení nebo otočení | Microsoft Docs
titleSuffix: Azure Stack
description: Naučte se, jak připravit certifikáty PKI pro nasazení Azure Stack integrovaných systémů nebo pro střídání tajných kódů v existujícím Azure Stackm prostředí.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2019
ms.author: justinha
ms.reviewer: ppacent
ms.lastreviewed: 09/16/2019
ms.openlocfilehash: a63e0e3a2246cc3c3c659f9671afdf4be0cc93cd
ms.sourcegitcommit: ca358ea5c91a0441e1d33f540f6dbb5b4d3c92c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802378"
---
# <a name="prepare-azure-stack-pki-certificates-for-deployment-or-rotation"></a>Příprava Azure Stack certifikátů PKI pro nasazení nebo otočení

Soubory certifikátů [získané z vaší certifikační autority (CA), které si vyberete](azure-stack-get-pki-certs.md) , se musí importovat a exportovat s vlastnostmi, které odpovídají požadavkům na certifikát Azure Stack.

## <a name="prepare-certificates-for-deployment"></a>Příprava certifikátů pro nasazení

Pomocí následujících kroků Připravte a ověříte Azure Stack certifikáty PKI, které se budou používat k nasazení nového Azure Stack prostředí nebo k přepravaci tajných kódů v existujícím Azure Stackm prostředí.

### <a name="import-the-certificate"></a>Importovat certifikát

1. Zkopírujte původní verze certifikátů [získané z vaší certifikační autority podle vlastního výběru](azure-stack-get-pki-certs.md) do adresáře na hostiteli nasazení. 
   > [!WARNING]
   > Nekopírujte soubory, které již byly naimportovány, exportovány nebo změněny jakýmkoli způsobem ze souborů poskytnutých přímo certifikační autoritou.

1. Pravým tlačítkem myši klikněte na certifikát a vyberte **instalovat certifikát** nebo **nainstalovat PFX**v závislosti na tom, jak byl certifikát dodán z vaší certifikační autority.

1. V **Průvodci importem certifikátu**vyberte jako umístění importu možnost **místní počítač** . Vyberte **Další**. Na následující obrazovce znovu vyberte Další.

    ![Umístění importu místního počítače pro certifikát](./media/prepare-pki-certs/1.png)

1. Zvolte možnost **umístit všechny certifikáty do následujícího úložiště** a pak jako umístění vyberte **důvěřovat podnikové síti** . Kliknutím na **OK** zavřete dialogové okno Výběr úložiště certifikátů a pak vyberte **Další**.

   ![Konfigurace úložiště certifikátů pro import certifikátu](./media/prepare-pki-certs/3.png)

   a. Při importu PFX se zobrazí další dialog. Na stránce **ochrana privátního klíče** zadejte heslo pro soubory certifikátů a potom povolte možnost **Označit tento klíč jako exportovatelný. Díky tomu můžete klíče zálohovat nebo přenést později** . Vyberte **Další**.

   ![Označit klíč jako exportovatelný](./media/prepare-pki-certs/2.png)

1. Kliknutím na **Dokončit** dokončete import.

> [!NOTE]
> Po importu certifikátu pro Azure Stack se privátní klíč certifikátu uloží jako soubor PKCS 12 (PFX) do clusterovaného úložiště.

### <a name="export-the-certificate"></a>Exportujte certifikát.

Otevřete konzolu MMC Správce certifikátů a připojte se k úložišti certifikátů místního počítače.

1. Otevřete konzolu Microsoft Management Console. Chcete-li otevřít konzolu nástroje ve Windows 10, klikněte pravým tlačítkem myši na **nabídku Start**, vyberte příkaz **Spustit**a zadejte příkaz **MMC** a stiskněte klávesu ENTER.

2. Vyberte **soubor** > **Přidat nebo odebrat modul snap-in**, pak vyberte **certifikáty** a vyberte **Přidat**.

    ![Přidat modul snap-in Certifikáty v konzole Microsoft Management Console](./media/prepare-pki-certs/mmc-2.png)

3. Vyberte položku **účet počítače**a pak vyberte **Další**. Vyberte **místní počítač** a pak **Dokončit**. Výběrem **OK** zavřete stránku přidat nebo odebrat modul snap-in.

    ![Vybrat účet pro modul snap-in Certifikáty v konzole Microsoft Management Console](./media/prepare-pki-certs/mmc-3.png)

4. Přejděte na **certifikáty** > **podnikové důvěryhodnosti** > **umístění certifikátu**. Ověřte, že na pravé straně vidíte svůj certifikát.

5. Z hlavního panelu konzoly Správce certifikátů vyberte **akce** > **všechny úlohy** > **exportovat**. Vyberte **Další**.

   > [!NOTE]
   > V závislosti na tom, kolik certifikátů Azure Stack máte, možná budete muset tento proces dokončit více než jednou.

6. Vyberte **Ano, exportovat privátní klíč**a potom klikněte na **Další**.

7. V části formát souboru pro export:
    
   - **Pokud je to možné, vyberte zahrnout všechny certifikáty do certifikátu**.  
   - Vyberte **exportovat všechny rozšířené vlastnosti**.  
   - Vyberte možnost **Povolit ochranu osobních údajů certifikátu**.  
   - Klikněte na **Další**.  
    
     ![Průvodce exportem certifikátu s vybranými možnostmi](./media/prepare-pki-certs/azure-stack-save-cert.png)

8. Vyberte **heslo** a zadejte heslo pro certifikáty. Vytvořte heslo, které splňuje následující požadavky na složitost hesla:

    * Minimální délka je osm znaků.
    * Aspoň tři z následujících znaků: velké písmeno, malé písmeno, číslice od 0-9, speciální znaky, abecední znak, který není velká nebo malá písmena.

    Poznamenejte si toto heslo. Použijete ho jako parametr nasazení.

9. Vyberte **Další**.

10. Vyberte název souboru a umístění souboru PFX, který chcete exportovat. Vyberte **Další**.

11. Vyberte **Finish** (Dokončit).

## <a name="next-steps"></a>Další kroky

[Ověření certifikátů PKI](azure-stack-validate-pki-certs.md)