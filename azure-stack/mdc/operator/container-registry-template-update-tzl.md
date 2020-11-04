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
ms.date: 1/8/2020
ms.author: mabrigg
ms.reviewer: chasat
ms.lastreviewed: 12/17/2019
ms.openlocfilehash: 1d014cfe855bc7e9bb3bdaae6ba7525d3df1e8c7
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93329238"
---
# <a name="update-the-container-registry-in-azure-stack-hub"></a>Aktualizace registru kontejneru v Azure Stackovém centru

Uživatelé centra Azure Stack můžou aktualizovat svoje nasazení registrů kontejnerů na novější SKU základní image AKS pomocí níže uvedených pokynů. Virtuální počítač a služba šablony registru kontejneru jsou bezstavové, protože všechny image stavu a kontejneru jsou uložené v úložišti objektů BLOB. Aktualizace je tak jednoduchá jako nasazení šablony registru kontejnerů s novější verzí VHD základní image AKS a přechodem DNS na nový virtuální počítač. Akce aktualizace hodnoty DNS pro virtuální počítače šablony registru pro starý a nový kontejner bude mít za následek malé okno přerušovaného připojení k registru, zatímco se hodnoty šíří.

## <a name="prerequisites"></a>Předpoklady

### <a name="operator"></a>Operátor

- Založte nejnovější základní image AKS z webu Marketplace pro Azure Stack. Základní image AKS se aktualizuje na měsíční tempo.

  ![Základní obrázek AKS](./media/container-registry-template-updating-tzl/aks-base-image.png)

### <a name="user"></a>Uživatel

1.  Zkontrolujte SKLADOVOU položku základní image AKS, která se použila k nasazení šablony registru kontejnerů, a to tak, že přejdete na záznam nasazení ve skupině prostředků a vyberete **vstupy**.

    ![Vstupy](./media/container-registry-template-updating-tzl/inputs.png)

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

    ![Nainstalovat novou šablonu registru kontejnerů](./media/container-registry-template-updating-tzl/new-instance.png)

2.  Zadejte poslední výstup SKU ze `Get-VMImage` skriptu a použijte jedinečný parametr **DnsName** z počáteční instalace v konfiguraci virtuálního počítače, jako počáteční instalaci použijte stejný instanční objekt a tajný klíč.

    ![Zadat SKU](./media/container-registry-template-updating-tzl/sku.png)

3.  Jako počáteční instalaci pro úložiště a Key Vault konfiguraci použijte stejné parametry úložiště a Key Vault.

    ![Přidat úložiště](./media/container-registry-template-updating-tzl/storage.png)

1.  Po nasazení nové šablony registru kontejnerů přejděte do počáteční skupiny prostředků a vyberte prostředek veřejné IP adresy.

    ![Vybrat IP adresu](./media/container-registry-template-updating-tzl/ip.png)

1.  V prostředku veřejné IP adresy přejděte na konfigurace a upravte popisek název DNS tak, aby se mohl použít pro nově nasazený prostředek. Poznámka: když upravíte popisek názvu DNS a vyberete možnost **Uložit** volání do Container registry bude začínat selhání.

    ![Změnit popisek DNS](./media/container-registry-template-updating-tzl/dns.png)
    
    ![Uložit změnu](./media/container-registry-template-updating-tzl/save.png)

2.  Přejděte do nové skupiny prostředků, která se používá k nasazení nové instance šablony registru kontejnerů, vyberte prostředek veřejné IP adresy, konfiguraci a aktualizujte popisek názvu DNS na správný název, který jste použili v původním nasazení, a v tomto příkladu `myreg` Vyberte **Save (Uložit** ).

    ![Vybrat konfiguraci](./media/container-registry-template-updating-tzl/select-configuration.png)
    
    ![Uložit konfiguraci](./media/container-registry-template-updating-tzl/save-configuration.png)

3.  Během následujících 30 minut dojde k občasnýmu přístupu k registru kontejneru, protože se rozšíří záznam DNS. Ověřte připojení tak, že se přihlásíte do registru Docker a narazíte na něj image.

## <a name="next-steps"></a>Další kroky

[Přehled Azure Stack Marketplace](../../operator/azure-stack-marketplace.md)
