---
title: Přidání Kubernetes do tržiště Azure Stack | Microsoft Docs
description: Naučte se, jak přidat Kubernetes do Marketplace Azure Stack.
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
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: fc83c8c68402622d721864f24a3ef9c5bab10479
ms.sourcegitcommit: 0d27456332031ab98ba2277117395ae5ffcbb79f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73047188"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Přidání Kubernetes do webu Azure Stack Marketplace

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

> [!note]  
> K nasazení clusterů jako zkušebního konceptu použijte jenom položku Kubernetes Azure Stack Marketplace. Pro podporované Kubernetes clustery v Azure Stack použijte [modul AKS](azure-stack-aks-engine.md).

Pro uživatele můžete nabízet Kubernetes jako položku Marketplace. Uživatelé můžou a pak nasazovat Kubernetes v rámci jediné koordinované operace.

Následující článek popisuje použití šablony Azure Resource Manager k nasazení a zřízení prostředků samostatného clusteru Kubernetes. Než začnete, Projděte si Azure Stack a globální nastavení klienta Azure. Shromážděte požadované informace o vašem Azure Stack. Přidejte potřebné prostředky do svého tenanta a na Azure Stack Marketplace. Cluster závisí na serveru Ubuntu, vlastním skriptu a položce tržiště clusteru Kubernetes na webu Marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Vytvoření plánu, nabídky a předplatného

Vytvořte plán, nabídku a předplatné pro položku Kubernetes Marketplace. Můžete také použít stávající plán a nabídku.

1. Přihlaste se k [portálu pro správu.](https://adminportal.local.azurestack.external)

1. Vytvořte plán jako základní plán. Pokyny najdete v tématu [Vytvoření plánu v Azure Stack](azure-stack-create-plan.md).

1. Vytvořte nabídku. Pokyny najdete v tématu [Vytvoření nabídky v Azure Stack](azure-stack-create-offer.md).

1. Vyberte **nabídky**a najděte nabídku, kterou jste vytvořili.

1. V okně nabídka vyberte **Přehled** .

1. Vyberte **změnit stav**. Vyberte možnost **veřejné**.

1. Vyberte **+ vytvořit prostředek** > **nabídky a plány** > **předplatné** k vytvoření předplatného.

    a. Zadejte **Zobrazovaný název**.

    b. Zadejte **uživatele**. Použijte účet Azure AD přidružený k vašemu tenantovi.

    c. **Popis poskytovatele**

    d. Nastavte **tenanta adresáře** na TENANTA Azure AD pro vaši Azure Stack. 

    e. Vyberte **nabídku**. Vyberte název nabídky, kterou jste vytvořili. Poznamenejte si ID předplatného.

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>Vytvoření instančního objektu a přihlašovacích údajů ve službě AD FS

Pokud pro službu správy identit používáte službu Active Directory federovaným Services (AD FS), budete muset vytvořit instanční objekt pro uživatele, kteří nasazují cluster Kubernetes. Vytvořte instanční objekt pomocí tajného klíče klienta. Pokyny najdete v tématu [Vytvoření instančního objektu pomocí tajného klíče klienta](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials).

## <a name="add-an-ubuntu-server-image"></a>Přidání image serveru Ubuntu

Do Marketplace přidejte následující image serveru Ubuntu:

1. Přihlaste se k [portálu pro správu](https://adminportal.local.azurestack.external).

1. Vyberte **všechny služby**a potom v kategorii **Správa** vyberte **Správa Marketplace**.

1. Vyberte **+ Přidat z Azure**.

1. Zadejte `Ubuntu Server`.

1. Vyberte nejnovější verzi serveru. Zkontrolujte plnou verzi a ujistěte se, že máte nejnovější verzi:
    - **Vydavatel**: kanonické
    - **Nabídka**: UbuntuServer
    - **Verze**: 16.04.201806120 (nebo nejnovější verze)
    - **SKU**: 16,04 – LTS

1. Vyberte **Stáhnout.**

## <a name="add-a-custom-script-for-linux"></a>Přidání vlastního skriptu pro Linux

Přidejte Kubernetes z webu Marketplace:

1. Otevřete [portál pro správu](https://adminportal.local.azurestack.external).

1. Vyberte **všechny služby** a potom v kategorii **Správa** vyberte **Správa Marketplace**.

1. Vyberte **+ Přidat z Azure**.

1. Zadejte `Custom Script for Linux`.

1. Vyberte skript s následujícím profilem:
   - **Nabídka**: vlastní skript pro Linux 2,0
   - **Verze**: 2.0.6 (nebo nejnovější verze)
   - **Vydavatel**: Microsoft Corp

     > [!Note]  
     > Je možné uvést více než jednu verzi vlastního skriptu pro systém Linux. Budete muset přidat poslední verzi položky.

1. Vyberte **Stáhnout.**


## <a name="add-kubernetes-to-the-marketplace"></a>Přidání Kubernetes do Marketplace

1. Otevřete [portál pro správu](https://adminportal.local.azurestack.external).

1. Vyberte **všechny služby** a potom v kategorii **Správa** vyberte **Správa Marketplace**.

1. Vyberte **+ Přidat z Azure**.

1. Zadejte `Kubernetes`.

1. Vyberte `Kubernetes Cluster`.

1. Vyberte **Stáhnout.**

    > [!note]  
    > Může trvat pět minut, než se položka Marketplace zobrazí na webu Marketplace.

    ![Kubernetes](../user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Aktualizace nebo odebrání Kubernetes 

Při aktualizaci položky Kubernetes odeberete předchozí položku na webu Marketplace. Podle pokynů v tomto článku přidejte do Marketplace aktualizaci Kubernetes.

Odebrání položky Kubernetes:

1. Připojte se k Azure Stack pomocí PowerShellu jako operátoru. Pokyny najdete v tématu [připojení k Azure Stack pomocí PowerShellu jako operátoru](azure-stack-powershell-configure-admin.md).

2. Najde aktuální položku clusteru Kubernetes v galerii.

    ```powershell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Název aktuální položky, například `Microsoft.AzureStackKubernetesCluster.0.3.0`

4. Odeberte položku pomocí následující rutiny prostředí PowerShell:

    ```powershell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Další kroky

[Nasazení Kubernetes pro Azure Stack](../user/azure-stack-solution-template-kubernetes-deploy.md)

[Přehled nabízených služeb v Azure Stack](service-plan-offer-subscription-overview.md)
