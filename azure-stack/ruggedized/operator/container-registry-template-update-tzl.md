---
title: Aktualizace registru kontejneru v centru Azure Stack | Microsoft Docs
titleSuffix: Azure Stack
description: Naučte se aktualizovat registr kontejneru v Azure Stack hub.
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
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: chasat
ms.lastreviewed: 12/17/2019
ms.openlocfilehash: b627b3f34b585189492cb3c3c3dfdef6965d5c61
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247400"
---
# <a name="update-the-container-registry-in-azure-stack-hub"></a>Aktualizace registru kontejneru v Azure Stackovém centru

Uživatelé centra Azure Stack můžou aktualizovat svoje nasazení registrů kontejnerů na novější SKU základní image AKS pomocí níže uvedených pokynů. Virtuální počítač a služba šablony registru kontejneru jsou bezstavové, protože všechny image stavu a kontejneru jsou uložené v úložišti objektů BLOB. Aktualizace je tak jednoduchá jako nasazení šablony registru kontejnerů s novější verzí VHD základní image AKS a přechodem DNS na nový virtuální počítač. Akce aktualizace hodnoty DNS pro virtuální počítače šablony registru pro starý a nový kontejner bude mít za následek malé okno přerušovaného připojení k registru, zatímco se hodnoty šíří.

## <a name="prerequisites"></a>Požadavky

### <a name="operator"></a>Operátor

1.  Založte nejnovější základní image AKS z webu Marketplace pro Azure Stack. Základní image AKS se aktualizuje na měsíční tempo.

> ![Snímek obrazovky zobrazující stránku přidat z Azure s výsledky hledání pro zobrazení ' AKS Base Ubu '.](./media/container-registry-template-updating-tzl/image1.png)

### <a name="user"></a>User

1.  Zkontrolujte SKLADOVOU položku základní image AKS, která se použila k nasazení šablony registru kontejnerů, a to tak, že přejdete na záznam nasazení ve skupině prostředků a vyberete **vstupy**.

    ![Snímek obrazovky zobrazující stránku vstupy](./media/container-registry-template-updating-tzl/image2.png)

2.  Zjistěte, jestli je k dispozici novější SKU základní image AKS pomocí funkce **Get-VMImageSku** , která vyžaduje `Import-Module .\pre-reqs.ps1` ze skriptů šablon registru kontejneru.

    ```powershell  
    PS C:\azurestack-galler-master\registry\Scripts> Get-VMImageSku -Location Shanghai
    
    Skus                  
    ----                  
    aks-ubuntu-1604-201909
    aks-ubuntu-1604-201910 
    ```

## <a name="parameters-required"></a>Požadované parametry

| Parametr | Podrobnosti |
| --- | --- |
| Uživatelské jméno | Zadejte uživatelské jméno pro přihlášení k virtuálnímu počítači. |
| Veřejný klíč SSH | Zadejte veřejný klíč SSH, který se používá k ověření pomocí virtuálního počítače pomocí protokolu SSH. |
| Velikost | Vyberte velikost virtuálního počítače, který se má nasadit. |
| Veřejná IP adresa | Zadejte název a typ IP adresy (dynamické/statické) pro tento virtuální počítač. |
| Popisek názvu domény | Zadejte předponu DNS pro váš registr. Celý plně kvalifikovaný název domény by měl odpovídat hodnotě CN certifikátu PFX vytvořenému pro registr. |
| Repliky | Zadejte počet replik kontejnerů, které se mají spustit. |
| SKU image | Zadejte SKLADOVOU položku image, která se má použít pro nasazení. Dostupné SKU pro základní image AKS jsou uvedené v rutině **Get-VMImageSku** prostředí PowerShell. |
| ID klienta instančního objektu | Zadejte ID aplikace instančního objektu (SPN), jak se používá v předchozím nasazení. |
| Heslo objektu služby/potvrzení hesla | Zadejte tajný klíč ID aplikace hlavního názvu služby (SPN), jak se používá v předchozím nasazení. |
| ID prostředku rozšířeného účtu úložiště | Zadejte ID prostředku účtu úložiště, které se používá v předchozím nasazení. |
| Existující kontejner objektů BLOB back-endu | Zadejte název kontejneru objektů blob, který se používá v předchozím nasazení. |
| ID prostředku Key Vault certifikátu PFX | Zadejte ID prostředku Microsoft Azure Key Vault, které se používá v předchozím nasazení. |
| Adresa URL tajného kódu certifikátu PFX Key Vault | Zadejte adresu URL certifikátu, jak se používá v předchozím nasazení. |
| Kryptografický otisk certifikátu PFX | Zadejte kryptografický otisk certifikátu, který se používá v předchozím nasazení. |

## <a name="installation"></a>Instalace

1.  Nainstalujte novou instanci šablony registru kontejneru do nové skupiny prostředků.

    ![Snímek obrazovky, který ukazuje stránku vytvořit Container Registry šablony – základy](./media/container-registry-template-updating-tzl/image3.png)

2.  Zadejte poslední výstup SKU ze `Get-VMImage` skriptu a použijte jedinečný parametr **DnsName** z počáteční instalace v konfiguraci virtuálního počítače, jako počáteční instalaci použijte stejný instanční objekt a tajný klíč.

    ![Snímek obrazovky zobrazující stránku vytvořit Container Registry šablonu – konfigurace virtuálního počítače](./media/container-registry-template-updating-tzl/image4.png)

3.  Jako počáteční instalaci pro úložiště a Key Vault konfiguraci použijte stejné parametry úložiště a Key Vault.

    ![Snímek obrazovky zobrazující stránku vytvořit Container Registry šablonu – úložiště a Key Vault konfigurace.](./media/container-registry-template-updating-tzl/image5.png)

1.  Po nasazení nové šablony registru kontejnerů přejděte do počáteční skupiny prostředků a vyberte prostředek veřejné IP adresy.

    ![Snímek obrazovky, který zobrazuje seznam prostředků adresy veřejné I P.](./media/container-registry-template-updating-tzl/image6.png)

1.  V prostředku veřejné IP adresy přejděte na konfigurace a upravte popisek název DNS tak, aby se mohl použít pro nově nasazený prostředek. Poznámka: když upravíte popisek názvu DNS a vyberete možnost **Uložit** volání do Container registry bude začínat selhání.

    ![Snímek obrazovky zobrazující stránku prostředku "veřejná I P-Address" s vybraným "static".](./media/container-registry-template-updating-tzl/image7.png)
    
    ![Snímek obrazovky zobrazující stránku prostředku "veřejná I P Address" s popiskem N S názvem (volitelné).](./media/container-registry-template-updating-tzl/image8.png)

2.  Přejděte do nové skupiny prostředků, která se používá k nasazení nové instance šablony registru kontejnerů, vyberte prostředek veřejné IP adresy, konfiguraci a aktualizujte popisek názvu DNS na správný název, který jste použili v původním nasazení, a v tomto příkladu `myreg` Vyberte **Save (Uložit**).

    ![Snímek obrazovky zobrazující stránku prostředku "veřejná I P adresa" s původním názvem D N S jmenovkou.](./media/container-registry-template-updating-tzl/image9.png)
    
    ![Šablona registru kontejneru](./media/container-registry-template-updating-tzl/image10.png)

3.  Během následujících 30 minut dojde k občasnýmu přístupu k registru kontejneru, protože se rozšíří záznam DNS. Ověřte připojení tak, že se přihlásíte do registru Docker a narazíte na něj image.

## <a name="next-steps"></a>Další kroky

[Přehled Azure Stack Marketplace](../../operator/azure-stack-marketplace.md)
