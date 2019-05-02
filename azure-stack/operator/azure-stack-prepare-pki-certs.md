---
title: Příprava nasazení Azure Stack integrované systémy nebo skrytou otočení certifikáty infrastruktury veřejných klíčů Azure Stack | Dokumentace Microsoftu
description: Popisuje postup přípravy certifikátů infrastruktury veřejných KLÍČŮ Azure Stack pro integrované systémy Azure Stack.
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
ms.date: 01/30/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: 8008b42567a95b609941e1dabff08b76aa1af3e9
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984988"
---
# <a name="prepare-azure-stack-pki-certificates-for-use-in-deployment-or-rotation"></a>Přidejte certifikáty Azure Stack PKI pro použití v nasazení nebo otočení

Soubory certifikátů [získané z certifikační Autority podle výběru](azure-stack-get-pki-certs.md) musí importovat a exportovat s vlastnostmi odpovídající požadavky na certifikát služby Azure Stack.

## <a name="prepare-certificates-for-deployment"></a>Přidejte certifikáty pro nasazení

Pomocí těchto kroků můžete připravit a ověřit certifikáty infrastruktury veřejných KLÍČŮ Azure Stack, které se použijí k nasazení nového prostředí Azure Stack nebo pro výměnu tajné kódy ve stávajícím prostředí Azure Stack: 

### <a name="import-the-certificate"></a>Importovat certifikát

1. Kopírovat původní verze certifikát [získané z certifikační Autority podle výběru](azure-stack-get-pki-certs.md) do adresáře nasazení hostitele. 
   > [!WARNING]
   > Nekopírovat soubory, které již byly importovat, exportovat nebo změnit žádným způsobem ze souborů poskytovaných certifikační Autoritou.

1. Klikněte pravým tlačítkem na certifikát a vyberte **nainstalovat certifikát** nebo **instalovat soubor PFX** v závislosti na tom, jak byla doručena certifikát z certifikační Autority.

1. V **Průvodce importem certifikátu**vyberte **místního počítače** jako umístění importu. Vyberte **Další**. Na následujícím obrázku klikněte na tlačítko Další akci.

    ![Umístění importu v místním počítači](./media/prepare-pki-certs/1.png)

1. Zvolte **všechny certifikáty umístit v následujícím úložišti** a pak vyberte **vztah důvěryhodnosti podnikové** jako umístění. Klikněte na tlačítko **OK** zavřete dialogové okno pro výběr úložiště certifikátů a pak **Další**.

   ![Konfigurace úložiště certifikátů](./media/prepare-pki-certs/3.png)

   a. Pokud importujete PFX, zobrazí se další dialogového okna. Na **ochrana privátního klíče** stránky, zadejte heslo pro certifikát soubory a pak povolte **označit tento klíč jako exportovatelný. To umožňuje zálohovat nebo přenést vaše klíče později** možnost. Vyberte **Další**.

   ![Označit klíč jako exportovatelný](./media/prepare-pki-certs/2.png)

1. Kliknutím na tlačítko Dokončit bylo možné import dokončit.

### <a name="export-the-certificate"></a>Export certifikátu

Otevřete konzolu MMC Správce certifikátů a připojení k úložišti certifikátů místního počítače.

1. Otevřete konzoly Microsoft Management Console, ve Windows 10 klikněte pravým tlačítkem myši klikněte v nabídce Start a pak klikněte na tlačítko spustit. Typ **konzoly mmc** klikněte na tlačítko ok.

1. Klikněte na soubor, přidat nebo odebrat modul Snap-In, pak vyberte certifikáty, klikněte na tlačítko Přidat.

    ![Přidat modul Snap-in Certifikáty](./media/prepare-pki-certs/mmc-2.png)
 
1. Zvolte účet počítače, klikněte na tlačítko Další vybrat místní počítač pak dokončit. Klikněte na tlačítko ok zavřete stránku Přidat/odebrat modul Snap-In.

    ![Přidat modul Snap-in Certifikáty](./media/prepare-pki-certs/mmc-3.png)

1. Přejděte na certifikáty > důvěřovat Enterprise > umístění certifikátu. Ověřte, že se zobrazí váš certifikát na pravé straně.

1. V konzole Správce úloh panelu certifikátu vyberte **akce** > **všechny úkoly** > **exportovat**. Vyberte **Další**.

   > [!NOTE]
   > V závislosti na tom, kolik Azure Stack možná muset certifikáty, že který máte, musíte to provést více než jednou.

1. Vyberte **Ano, exportovat soukromý klíč**a potom klikněte na tlačítko **Další**.

1. V části formát souboru pro Export:
    
   - Vyberte **zahrnout všechny certifikáty v certifikátu, pokud je to možné**.  
   - Vyberte **exportovat všechny rozšířené vlastnosti**.  
   - Vyberte **povolit ochranu osobních údajů certifikát**.  
   - Klikněte na **Další**.  
    
     ![Průvodce exportem certifikátu se vybrané možnosti](./media/prepare-pki-certs/azure-stack-save-cert.png)

1. Vyberte **heslo** a zadejte heslo pro certifikáty. Vytvořte heslo, které splňuje následující požadavky na složitost hesla. Minimální délku osmi znaků. Heslo obsahuje alespoň tři z následujících možností: velká písmena, malé písmeno, číslice 0-9, speciální znaky, abecední znak, který není velká písmena ani malá písmena. Poznamenejte si toto heslo. Použijete ji jako parametr nasazení.

1. Vyberte **Další**.

1. Zvolte název souboru a umístění pro export souboru pfx. Vyberte **Další**.

1. Vyberte **Finish** (Dokončit).

## <a name="next-steps"></a>Další postup

[Ověření certifikátů infrastruktury veřejných KLÍČŮ](azure-stack-validate-pki-certs.md)