---
title: Nasazení Kubernetes do centra Azure Stack pomocí služby Active Directory federované služby (AD FS) | Microsoft Docs
description: Naučte se, jak nasadit Kubernetes do centra Azure Stack pomocí služby Active Directory federované služby (AD FS).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: nav
ms.topic: article
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 5bef03b0cb89a44cf6453bea9dbe4deeeb771430
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883299"
---
# <a name="deploy-kubernetes-to-azure-stack-hub-using-active-directory-federated-services"></a>Nasazení Kubernetes do centra Azure Stack pomocí federovaných služeb Active Directory

> [!Note]  
> K nasazení clusterů jako zkušebního konceptu použijte jenom položku Kubernetes Azure Stack Marketplace. Pro podporované Kubernetes clustery v Azure Stack použijte [modul AKS](azure-stack-kubernetes-aks-engine-overview.md).

Pro nasazení a nastavení prostředků pro Kubernetes můžete postupovat podle kroků v tomto článku. Tyto kroky použijte, když je služba Active Directory federované služby (AD FS) vaší službou správy identit.

## <a name="prerequisites"></a>Požadavky 

Pokud chcete začít, ujistěte se, že máte správná oprávnění a že je vaše centrum Azure Stack připravené.

1. Vygenerujte dvojici veřejného a privátního klíče SSH pro přihlášení k virtuálnímu počítači se systémem Linux v centru Azure Stack. Při vytváření clusteru budete potřebovat veřejný klíč.

    Pokyny k vygenerování klíče najdete v tématu [generování klíče SSH](azure-stack-dev-start-howto-ssh-public-key.md).

1. Ověřte, že máte platné předplatné na portálu tenanta centra Azure Stack a že máte k dispozici dostatek veřejných IP adres pro přidávání nových aplikací.

    Cluster se nedá nasadit do předplatného **správce** centra Azure Stack. Je nutné použít předplatné **uživatele** . 

1. Pokud na svém Marketplace nemáte cluster Kubernetes, obraťte se na správce centra Azure Stack.

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Abyste mohli nastavit instanční objekt při použití AD FS jako řešení identity, musíte pracovat se správcem centra Azure Stack. Instanční objekt poskytuje aplikaci přístup k prostředkům centra Azure Stack.

1. Správce centra Azure Stack vám poskytne informace o instančním objektu. Hlavní informace o instančním objektu by měly vypadat takto:

     ```Text  
       ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
       ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
       ClientSecret          : <your client secret>
       Thumbprint            : <often this value is empty>
       ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
       PSComputerName        : 192.168.200.224
       RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
     ```

2. Přiřaďte novému instančnímu objektu roli Přispěvatel k vašemu předplatnému. Pokyny najdete v tématu [přiřazení role](../operator/azure-stack-add-users-adfs.md).

## <a name="deploy-kubernetes"></a>Nasazení Kubernetes

1. Otevřete [portál centra Azure Stack](https://portal.local.azurestack.external).

1. Vyberte **+ vytvořit prostředek** > Cluster **COMPUTE** > **Kubernetes**. Vyberte **Vytvořit**.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. základní informace

1. V vytváření clusteru Kubernetes vyberte **základy** .

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Vyberte ID vašeho **předplatného** .

1. Zadejte název nové skupiny prostředků nebo vyberte existující skupinu prostředků. Název prostředku musí být alfanumerické znaky a malá písmena.

1. Vyberte **umístění** skupiny prostředků. Toto je oblast, kterou zvolíte pro instalaci centra Azure Stack.

### <a name="2-kubernetes-cluster-settings"></a>2. Kubernetes nastavení clusteru

1. V Kubernetes vytvořit cluster Kubernetes vyberte **Nastavení clusteru** .

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. Zadejte **uživatelské jméno správce virtuálního počítače se systémem Linux**. Uživatelské jméno pro virtuální počítače Linux, které jsou součástí clusteru Kubernetes a DVM.

1. Zadejte **veřejný klíč SSH** se používají pro autorizaci pro všechny počítače s Linuxem vytvořili jako součást clusteru Kubernetes a DVM.

1. Zadejte **předpony DNS profilu hlavní** , které je jedinečné pro danou oblast. Musí se jednat o jedinečný název oblasti, například `k8s-12345`. Zkuste zvolit stejný jako název skupiny prostředků jako osvědčený postup.

    > [!Note]  
    > Pro každý cluster použijte předponu DNS nových a jedinečných hlavní profilu.

1. Vyberte **počet profilů hlavního fondu Kubernetes**. Počet obsahuje počet uzlů v hlavním fondu. Může být od 1 do 7. Tato hodnota by měla být lichá číslice.

1. Vyberte **VMSize hlavních virtuálních počítačů Kubernetes**.

1. Vyberte **počet profilů fondu uzlů Kubernetes**. Počet obsahuje počet agentů v clusteru. 

1. Vyberte **VMSize virtuálních počítačů uzlů Kubernetes**. Tato hodnota určuje velikost virtuálních počítačů uzlů Kubernetes. 

1. Pro vaši instalaci centra Azure Stack vyberte **ADFS** pro **systém identit Azure Stack hub** .

1. Zadejte **instanční objekt** , který používá poskytovatel cloudu Azure Kubernetes. ID klienta identifikované jako ID aplikace, když správce centra Azure Stack vytvořil instanční objekt.

1. Zadejte **tajný klíč klienta instančního objektu**. Toto je tajný kód klienta, který jste získali pro váš princip AD FS služby od správce centra Azure Stack.

1. Zadejte **verzi Kubernetes**. Toto je verze zprostředkovatele služby Kubernetes Azure. Azure Stack centrum uvolní vlastní Kubernetes sestavení pro každou verzi centra Azure Stack.

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