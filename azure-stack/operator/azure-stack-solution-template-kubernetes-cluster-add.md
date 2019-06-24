---
title: Přidat Kubernetes pro Azure Stack Marketplace | Dokumentace Microsoftu
description: Zjistěte, jak přidat Kubernetes pro Azure Stack Marketplace.
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
ms.date: 06/18/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 61d2739475a0593671e7a363671dd2859a6e6f24
ms.sourcegitcommit: 3f52cf06fb5b3208057cfdc07616cd76f11cdb38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2019
ms.locfileid: "67316252"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>Přidat Kubernetes na webu Marketplace služby Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

> [!note]  
> Kubernetes ve službě Azure Stack je ve verzi preview. Odpojené scénář Azure Stack není aktuálně podporován ve verzi preview. Pouze pomocí položky marketplace pro vývoj a testování scénářů.

Uživatelům můžete nabídnout Kubernetes jako položky Marketplace. Uživatelé můžou potom nasaďte Kubernetes v rámci jediné koordinované operace.

Následující článek se zabývá pomocí šablony Azure Resource Manageru k nasazení a zřizování prostředkům pro samostatného clusteru Kubernetes. Než začnete, zkontrolujte Azure Stack a nastavení globální tenanta Azure. Shromážděte požadované informace o Azure Stack. Přidáte prostředky potřebné pro vašeho tenanta a na Azure Marketplace zásobníku. Clusteru závisí na serveru se systémem Ubuntu, vlastní skript a položky Marketplace clusteru Kubernetes ve na webu marketplace.

## <a name="create-a-plan-an-offer-and-a-subscription"></a>Vytvořit plán, nabídky a předplatné

Vytvořte plán, nabídky a předplatné pro položky Kubernetes Marketplace. Můžete také použít existující plán a nabídnout.

1. Přihlaste se k [portál pro správu.](https://adminportal.local.azurestack.external)

1. Vytvoření plánu jako základní plán. Pokyny najdete v tématu [vytvoření plánu ve službě Azure Stack](azure-stack-create-plan.md).

1. Vytvořte nabídku. Pokyny najdete v tématu [vytvoření nabídky ve službě Azure Stack](azure-stack-create-offer.md).

1. Vyberte **nabízí**a najít vámi vytvořená nabídka.

1. Vyberte **přehled** v okně nabídky.

1. Vyberte **změnit stav**. Vyberte **veřejné**.

1. Vyberte **+ vytvořit prostředek** > **nabídky a plány** > **předplatné** vytvořit odběr.

    a. Zadejte **zobrazovaný název**.

    b. Zadejte **uživatele**. Použijte účet Azure AD přidružené k tenantovi.

    c. **Popis zprostředkovatele**

    d. Nastavte **adresář tenanta** do tenanta služby Azure AD pro Azure Stack. 

    e. Vyberte **nabízejí**. Vyberte název nabídky, která jste vytvořili. Poznamenejte si ID předplatného.

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>Vytvoření instančního objektu a přihlašovacích údajů ve službě AD FS

Pokud používáte Active Directory Federated Services (AD FS) pro vaši službu identity management, je potřeba vytvořit instanční objekt pro uživatele nasazení clusteru Kubernetes. Vytvoření instančního objektu, pomocí tajného klíče klienta. Pokyny najdete v tématu [vytvoření instančního objektu pomocí tajného klíče klienta](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials).

## <a name="add-an-ubuntu-server-image"></a>Přidat Ubuntu server image

Přidejte následující image Ubuntu Server na webu Marketplace:

1. Přihlaste se k [portál pro správu](https://adminportal.local.azurestack.external).

1. Vyberte **všechny služby**a potom v části **správu** vyberte **Marketplace správu**.

1. Vyberte **+ přidat z Azure**.

1. Zadejte `Ubuntu Server`.

1. Vyberte nejnovější verzi serveru. Zkontrolujte na plnou verzi a ujistěte se, že máte nejnovější verzi:
    - **Publisher**: Canonical
    - **Nabízejí**: UbuntuServer
    - **Verze**: 16.04.201806120 (nebo nejnovější verze)
    - **SKLADOVÁ POLOŽKA**: 16.04-LTS

1. Vyberte **stáhnout.**

## <a name="add-a-custom-script-for-linux"></a>Přidání vlastních skriptů pro Linux

Přidáte rozhraní Kubernetes na Marketplace:

1. Otevřít [portál pro správu](https://adminportal.local.azurestack.external).

1. Vyberte **všechny služby** a potom v části **správu** vyberte **Marketplace správu**.

1. Vyberte **+ přidat z Azure**.

1. Zadejte `Custom Script for Linux`.

1. Vyberte skript, který se následující profil:
   - **Nabízejí**: Vlastní skript pro Linux 2.0
   - **Verze**: 2.0.6 (nebo nejnovější verze)
   - **Publisher**: Microsoft Corp

     > [!Note]  
     > Může být uvedeno více než jednu verzi vlastních skriptů pro Linux. Je potřeba přidat poslední verzi položky.

1. Vyberte **stáhnout.**


## <a name="add-kubernetes-to-the-marketplace"></a>Přidat Kubernetes na webu Marketplace

1. Otevřít [portál pro správu](https://adminportal.local.azurestack.external).

1. Vyberte **všechny služby** a potom v části **správu** vyberte **Marketplace správu**.

1. Vyberte **+ přidat z Azure**.

1. Zadejte `Kubernetes`.

1. Vyberte `Kubernetes Cluster`.

1. Vyberte **stáhnout.**

    > [!note]  
    > Může trvat 5 minut, další položky marketplace se zobrazí na webu Marketplace.

    ![Kubernetes](../user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>Aktualizace nebo odebrání rozhraní Kubernetes 

Při aktualizaci položky Kubernetes, odeberete předchozí položky na webu Marketplace. Postupujte podle pokynů v tomto článku se přidat že kubernetes aktualizovat na webu Marketplace.

Odebrat položku Kubernetes:

1. Připojení ke službě Azure Stack pomocí prostředí PowerShell jako operátor. Pokyny naleznete v tématu [připojit ke službě Azure Stack pomocí prostředí PowerShell jako operátor](azure-stack-powershell-configure-admin.md).

2. Aktuální položka clusteru Kubernetes najdete v galerii.

    ```powershell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. Poznamenejte si název aktuální položky, jako například `Microsoft.AzureStackKubernetesCluster.0.3.0`

4. Použijte následující rutiny Powershellu pro odebrání položky:

    ```powershell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>Další postup

[Nasazení Kubernetes pro Azure Stack](../user/azure-stack-solution-template-kubernetes-deploy.md)

[Přehled nabízených služeb ve službě Azure Stack](azure-stack-offer-services-overview.md)
