---
title: Příprava certifikátů infrastruktury veřejných klíčů Azure Stack pro nasazení Azure Stack integrovaných systémů nebo pro rotaci tajných klíčů | Microsoft Docs
description: Popisuje postup přípravy Azure Stack certifikátů PKI pro Azure Stack integrovaných systémů.
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
ms.date: 09/10/2019
ms.author: justinha
ms.reviewer: ppacent
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 6b7d7a3e70d78b8ab943224babc515395319914e
ms.sourcegitcommit: 38f21e0bcf7b593242ad615c9d8ef8a1ac19c734
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70902715"
---
# <a name="prepare-azure-stack-pki-certificates-for-use-in-deployment-or-rotation"></a>Příprava Azure Stack certifikátů PKI pro použití při nasazení nebo rotaci

Soubory certifikátů [získané z vaší certifikační autority si](azure-stack-get-pki-certs.md) musí být naimportovány a exportovány s vlastnostmi, které odpovídají požadavkům na certifikát Azure Stack.

## <a name="prepare-certificates-for-deployment"></a>Příprava certifikátů pro nasazení

Pomocí následujících kroků Připravte a ověříte Azure Stack certifikáty PKI, které se budou používat k nasazení nového Azure Stack prostředí nebo k přepravaci tajných kódů v existujícím Azure Stackm prostředí. 


### <a name="import-the-certificate"></a>Importovat certifikát

1. Zkopírujte původní verze certifikátů [získané z vaší certifikační autority podle vlastního výběru](azure-stack-get-pki-certs.md) do adresáře na hostiteli nasazení. 
   > [!WARNING]
   > Nekopírujte soubory, které již byly naimportovány, exportovány nebo změněny jakýmkoli způsobem ze souborů poskytnutých přímo certifikační autoritou.

1. Pravým tlačítkem myši klikněte na certifikát a vyberte **instalovat certifikát** nebo **nainstalovat PFX** v závislosti na tom, jak byl certifikát dodán z vaší certifikační autority.

1. V **Průvodci importem certifikátu**vyberte jako umístění importu možnost **místní počítač** . Vyberte **Další**. Na následující obrazovce klikněte znovu na další.

    ![Umístění importu místního počítače](./media/prepare-pki-certs/1.png)

1. Zvolte možnost **umístit všechny certifikáty do následujícího úložiště** a pak jako umístění vyberte **důvěřovat podnikové síti** . Kliknutím na **OK** zavřete dialogové okno Výběr úložiště certifikátů a potom klikněte na **Další**.

   ![Konfigurace úložiště certifikátů](./media/prepare-pki-certs/3.png)

   a. Pokud importujete PFX, zobrazí se další dialog. Na stránce **ochrana privátního klíče** zadejte heslo pro soubory certifikátů a potom povolte možnost **označit tento klíč jako exportovatelný. Díky tomu můžete klíče zálohovat nebo přenést později** . Vyberte **Další**.

   ![Označit klíč jako exportovatelný](./media/prepare-pki-certs/2.png)

1. Dokončete import kliknutím na tlačítko Dokončit.

> [!NOTE]
> Po importu certifikátu pro Azure Stack je privátní klíč certifikátu uložen jako soubor PKCS 12 (. pfx) v clusterovém úložišti.

### <a name="export-the-certificate"></a>Exportujte certifikát.

Otevřete konzolu MMC Správce certifikátů a připojte se k úložišti certifikátů místního počítače.

1. Otevřete konzolu Microsoft Management Console, v nabídce Windows 10 klikněte pravým tlačítkem myši na Start a pak klikněte na spustit. Zadejte **MMC** a klikněte na OK.

1. Klikněte na soubor, přidat nebo odebrat modul snap-in a pak vyberte certifikáty klikněte na Přidat.

    ![Přidat modul snap-in Certifikáty](./media/prepare-pki-certs/mmc-2.png)
 
1. Vyberte možnost účet počítače, klikněte na tlačítko Další a pak na položku Dokončit. Kliknutím na tlačítko OK zavřete stránku přidat nebo odebrat modul snap-in.

    ![Přidat modul snap-in Certifikáty](./media/prepare-pki-certs/mmc-3.png)

1. Přejděte na certifikáty > podnikové důvěryhodnosti > umístění certifikátu. Ověřte, že na pravé straně vidíte svůj certifikát.

1. V hlavním panelu konzoly Správce certifikátů vyberte **Akce** > **všechny úkoly** > **exportovat**. Vyberte **Další**.

   > [!NOTE]
   > V závislosti na tom, kolik certifikátů Azure Stack máte, možná budete muset tento proces dokončit více než jednou.

1. Vyberte **Ano, exportovat privátní klíč**a potom klikněte na **Další**.

1. V části formát souboru pro export:
    
   - **Pokud je to možné, vyberte zahrnout všechny certifikáty do certifikátu**.  
   - Vyberte **exportovat všechny rozšířené vlastnosti**.  
   - Vyberte možnost **Povolit ochranu osobních údajů certifikátu**.  
   - Klikněte na **Další**.  
    
     ![Průvodce exportem certifikátu s vybranými možnostmi](./media/prepare-pki-certs/azure-stack-save-cert.png)

1. Vyberte **heslo** a zadejte heslo pro certifikáty. Vytvořte heslo, které splňuje následující požadavky na složitost hesla. Minimální délka je osm znaků. Heslo obsahuje minimálně tři z následujících znaků: velká písmena, malá písmena, číslice od 0-9, speciální znaky, abecední znak, který není velká ani malá. Poznamenejte si toto heslo. Budete ho používat jako parametr nasazení.

1. Vyberte **Další**.

1. Vyberte název souboru a umístění souboru PFX, který chcete exportovat. Vyberte **Další**.

1. Vyberte **Finish** (Dokončit).

## <a name="next-steps"></a>Další postup

[Ověření certifikátů PKI](azure-stack-validate-pki-certs.md)