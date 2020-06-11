---
title: Nasazení Kubernetes do centra Azure Stack pomocí Azure Active Directory (Azure AD)
description: Naučte se, jak nasadit Kubernetes do centra Azure Stack pomocí Azure Active Directory (Azure AD).
author: mattbriggs
ms.topic: article
ms.date: 3/12/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/12/2020
ms.openlocfilehash: ee0410467a7e2608580da5c209a97f86a60b675a
ms.sourcegitcommit: 6306e0c2506106ad01ff50010f36466f3325d0a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84631202"
---
# <a name="deploy-kubernetes-to-azure-stack-hub-using-azure-active-directory"></a>Nasazení Kubernetes do centra Azure Stack pomocí Azure Active Directory

> [!Note]  
> K nasazení clusterů jako zkušebního konceptu použijte jenom položku Kubernetes Azure Stack Marketplace. Pro podporované Kubernetes clustery v Azure Stack použijte [modul AKS](azure-stack-kubernetes-aks-engine-overview.md).

Postup v tomto článku můžete použít k nasazení a nastavení prostředků pro Kubernetes, při použití Azure Active Directory (Azure AD) jako služby správy identit, v jediné koordinované operaci.

## <a name="prerequisites"></a>Požadavky

Pokud chcete začít, ujistěte se, že máte správná oprávnění a že je vaše centrum Azure Stack připravené.

1. Ověřte, že můžete vytvářet aplikace v tenantovi Azure Active Directory (Azure AD). Tato oprávnění budete potřebovat pro nasazení Kubernetes.

    Pokyny k kontrole oprávnění najdete v tématu [kontrola Azure Active Directory oprávnění](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

1. Vygenerujte dvojici veřejného a privátního klíče SSH pro přihlášení k virtuálnímu počítači se systémem Linux v centru Azure Stack. Při vytváření clusteru budete potřebovat veřejný klíč.

    Pokyny k vygenerování klíče najdete v tématu [generování klíče SSH](azure-stack-dev-start-howto-ssh-public-key.md).

1. Ověřte, že máte platné předplatné na portálu tenanta centra Azure Stack a že máte k dispozici dostatek veřejných IP adres pro přidávání nových aplikací.

    Cluster se nedá nasadit do předplatného **správce** centra Azure Stack. Je nutné použít předplatné **uživatele** . 

1. Pokud na svém Marketplace nemáte cluster Kubernetes, obraťte se na správce centra Azure Stack.

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Nastavte instanční objekt v Azure. Instanční objekt poskytuje aplikaci přístup k prostředkům centra Azure Stack.

1. Přihlaste se k globálním [Azure Portal](https://portal.azure.com).

1. Ověřte, že jste přihlášeni pomocí tenanta Azure AD přidruženého k instanci centra Azure Stack. Přihlášení můžete přepnout kliknutím na ikonu filtru na panelu nástrojů Azure.

    ![Vyberte tenanta AD.](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

1. Vytvořte aplikaci Azure AD.

    a. Přihlaste se ke svému účtu Azure prostřednictvím [Azure Portal](https://portal.azure.com).  
    b. Vyberte **Azure Active Directory**  >  **Registrace aplikací**  >  **novou registraci**.  
    c. Zadejte název a URL aplikace.  
    d. Vyberte **podporované typy účtů**.  
    e.  Přidejte `http://localhost` pro identifikátor URI pro aplikaci. Vyberte **Web** pro typ aplikace, kterou chcete vytvořit. Po nastavení hodnot vyberte **Registrovat**.

1. Poznamenejte si **ID aplikace**. Při vytváření clusteru budete potřebovat ID. ID je odkazováno jako **ID klienta instančního objektu**.

1. V okně pro princip služby vyberte **nový tajný klíč klienta**. **Nastavení**  >  **Klíče**. Pro princip služby musíte vygenerovat ověřovací klíč.

    a. Zadejte **Popis**.

    b. Pro **vypršení platnosti**vyberte možnost **nikdy nevyprší platnost** .

    c. Vyberte možnost **Přidat**. Poznamenejte si řetězec klíče. Při vytváření clusteru budete potřebovat řetězec klíče. Na klíč se odkazuje jako na **tajný klíč klienta instančního objektu**.

## <a name="give-the-service-principal-access"></a>Poskytnutí přístupu k instančnímu objektu

Udělte instančnímu objektu přístup k vašemu předplatnému, aby objekt zabezpečení mohl vytvářet prostředky.

1.  Přihlaste se k portálu centra Azure Stack `https://portal.local.azurestack.external/` .

1. Vyberte **všechny**  >  **odběry**služeb.

1. Vyberte předplatné vytvořené vaším operátorem pro použití clusteru Kubernetes.

1. Vyberte **řízení přístupu (IAM)** > vyberte **Přidat přiřazení role**.

1. Vyberte roli **Přispěvatel** .

1. Vyberte název aplikace vytvořený pro objekt služby. Do vyhledávacího pole možná budete muset zadat název.

1. Klikněte na **Uložit**.

## <a name="deploy-kubernetes"></a>Nasazení Kubernetes

1. Otevřete portál centra Azure Stack `https://portal.local.azurestack.external` .

1. Vyberte **+ vytvořit prostředek**  >  **COMPUTE**  >  **Kubernetes cluster**. Klikněte na **Vytvořit**.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. základní informace

1. V vytváření clusteru Kubernetes vyberte **základy** .

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Vyberte ID vašeho **předplatného** .

1. Zadejte název nové skupiny prostředků nebo vyberte existující skupinu prostředků. Název prostředku musí být alfanumerický a malý.

1. Vyberte **umístění** skupiny prostředků. Toto je oblast, kterou zvolíte pro instalaci centra Azure Stack.

### <a name="2-kubernetes-cluster-settings"></a>2. Kubernetes nastavení clusteru

1. V Kubernetes vytvořit cluster Kubernetes vyberte **Nastavení clusteru** .

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-aad.png)

1. Zadejte **uživatelské jméno správce virtuálního počítače se systémem Linux**. Uživatelské jméno pro Linux Virtual Machines, které jsou součástí clusteru Kubernetes a DVM.

1. Zadejte **veřejný klíč SSH** , který se používá pro autorizaci pro všechny počítače se systémem Linux vytvořené jako součást clusteru KUBERNETES a DVM.

1. Zadejte **předponu DNS hlavního profilu** , která je pro oblast jedinečná. Musí se jednat o jedinečný název oblasti, například `k8s-12345` . Zkuste zvolit stejný jako název skupiny prostředků jako osvědčený postup.

    > [!Note]  
    > Pro každý cluster použijte novou a jedinečnou předponu DNS hlavního profilu.

1. Vyberte **počet profilů hlavního fondu Kubernetes**. Počet obsahuje počet uzlů v hlavním fondu. Může být od 1 do 7. Tato hodnota by měla být lichá číslice.

1. Vyberte **VMSize hlavních virtuálních počítačů Kubernetes**. Tato hodnota určuje velikost virtuálního počítače Kubernetes Master. 

1. Vyberte **počet profilů fondu uzlů Kubernetes**. Počet obsahuje počet agentů v clusteru. 

1. Vyberte **VMSize virtuálních počítačů uzlů Kubernetes**. Tato hodnota určuje velikost virtuálních počítačů uzlů Kubernetes. 

1. Pro vaši instalaci centra Azure Stack vyberte **Azure AD** pro **Azure Stack systému identit centra** .

1. Zadejte **instanční objekt** , který používá poskytovatel cloudu Azure Kubernetes. ID klienta identifikované jako ID aplikace, když správce centra Azure Stack vytvořil instanční objekt.

1. Zadejte **tajný klíč klienta instančního objektu**. Toto je tajný kód klienta, který jste nastavili při vytváření služby.

1. Zadejte **verzi Kubernetes**. Toto je verze poskytovatele Azure Kubernetes. Azure Stack centrum uvolní vlastní Kubernetes sestavení pro každou verzi centra Azure Stack.

### <a name="3-summary"></a>3. souhrn

1. Vyberte souhrn. Okno zobrazuje ověřovací zprávu pro nastavení konfigurace clusteru Kubernetes.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Zkontrolujte nastavení.

3. Vyberte **OK** a nasaďte svůj cluster.

> [!TIP]  
>  Pokud máte dotazy k vašemu nasazení, můžete odeslat svůj dotaz nebo zjistit, jestli už někdo na dotaz na [fóru centra Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)odpověděl.


## <a name="next-steps"></a>Další kroky

[Připojení ke clusteru](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[Povolit řídicí panel Kubernetes](azure-stack-solution-template-kubernetes-dashboard.md)
