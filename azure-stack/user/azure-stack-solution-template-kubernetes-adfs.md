---
title: Nasazení Kubernetes pro Azure Stack pomocí Active Directory Federated Services (AD FS) | Dokumentace Microsoftu
description: Informace o nasazení Kubernetes pro Azure Stack pomocí Active Directory Federated Services (AD FS).
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
ms.date: 06/18/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 738a8ab4c43eac9cc68deb63f44577df0f512f0b
ms.sourcegitcommit: 759a01b566597a71b18fca25902cacc983a5a63b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67298072"
---
# <a name="deploy-kubernetes-to-azure-stack-using-active-directory-federated-services"></a>Nasazení do služby Azure Stack pomocí Active Directory Federated Services Kubernetes

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

> [!Note]  
> Kubernetes ve službě Azure Stack je ve verzi preview. Odpojené scénář Azure Stack není aktuálně podporován ve verzi preview. Pouze pomocí položky marketplace pro vývoj a testování scénářů. [AKS modul](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md) lze použít pro produkční prostředí.

Můžete postupovat podle kroků v tomto článku pro nasazení a nastavit prostředky pro Kubernetes. Pomocí těchto kroků při Active Directory Federated Services (AD FS) je služba pro správu identit.

## <a name="prerequisites"></a>Požadavky 

Abyste mohli začít, ujistěte se, že máte správná oprávnění a že služby Azure Stack je připravena.

1. Generování veřejného a privátního klíče pár SSH pro přihlášení do virtuálního počítače s Linuxem v Azure stacku. Veřejný klíč musíte při vytváření clusteru.

    Generuje se klíč, v tématu [vygenerování klíče SSH](azure-stack-dev-start-howto-ssh-public-key.md).

1. Zkontrolujte, zda máte platné předplatné na portálu Azure Stack tenant a, že máte dostatek veřejné IP adresy pro přidání nové aplikace k dispozici.

    Cluster se nedá nasadit do služby Azure Stack **správce** předplatného. Je nutné použít **uživatele** předplatného. 

1. Pokud jste Kubernetes Cluster v marketplace, obraťte se na správce služby Azure Stack.

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Potřebujete pracovat se správcem vaší služby Azure Stack a nastavení instančního objektu služby, při použití služby AD FS jako řešení identit. Služby, které poskytuje vaše aplikace přístup k prostředkům Azure Stack.

1. Správce služby Azure Stack vám poskytne informace pro instanční objekt. Informací o instančním objektu služby by měl vypadat:

     ```Text  
       ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
       ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
       ClientSecret          : <your client secret>
       Thumbprint            : <often this value is empty>
       ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
       PSComputerName        : 192.168.200.224
       RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
     ```

2. Přiřadíte nový instanční objekt služby roli Přispěvatel do vašeho předplatného. Pokyny najdete v tématu [přiřazení role](../operator/azure-stack-add-users-adfs.md).

## <a name="deploy-kubernetes"></a>Nasazení Kubernetes

1. Otevřít [portálu Azure Stack](https://portal.local.azurestack.external).

1. Vyberte **+ vytvořit prostředek** > **Compute** > **clusteru Kubernetes**. Vyberte **Vytvořit**.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Základy

1. Vyberte **Základy** ve vytvoření clusteru Kubernetes.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Vyberte vaše **předplatné** ID.

1. Zadejte název nové skupiny prostředků nebo vyberte existující skupinu prostředků. Název prostředku musí být alfanumerické znaky a malá písmena.

1. Vyberte **umístění** skupiny prostředků. Toto je oblast, kterou jste vybrali pro instalaci sady Azure Stack.

### <a name="2-kubernetes-cluster-settings"></a>2. Nastavení clusteru Kubernetes

1. Vyberte **nastavení clusteru Kubernetes** ve vytvoření clusteru Kubernetes.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. Zadejte **uživatelské jméno správce virtuálního počítače s Linuxem**. Uživatelské jméno pro virtuální počítače Linux, které jsou součástí clusteru Kubernetes a DVM.

1. Zadejte **veřejný klíč SSH** se používají pro autorizaci pro všechny počítače s Linuxem vytvořili jako součást clusteru Kubernetes a DVM.

1. Zadejte **předpony DNS profilu hlavní** , které je jedinečné pro danou oblast. Musí se jednat oblasti jedinečný název, jako například `k8s-12345`. Zkuste jste zvolili, je stejná jako skupina prostředků pojmenujte jako nejlepší praxe.

    > [!Note]  
    > Pro každý cluster použijte předponu DNS nových a jedinečných hlavní profilu.

1. Vyberte **fondu profilu počet hlavních Kubernetes**. Počet obsahuje počet uzlů ve fondu hlavní. Může být od 1 do 7. Tato hodnota by měla být liché číslo.

1. Vyberte **The VMSize hlavních virtuálních počítačů Kubernetes**.

1. Vyberte **počet profil fond uzlů Kubernetes**. Počet obsahuje počet agentů v clusteru. 

1. Vyberte **VMSize virtuálních počítačích uzlů Kubernetes**. Toto nastavení určuje virtuální počítače uzlů virtuálních počítačů velikosti Kubernetes. 

1. Vyberte **služby AD FS** pro **systém identit Azure Stack** pro vaši instalaci služby Azure Stack.

1. Zadejte **ID klienta instančního objektu služby** slouží od poskytovatele cloudu Kubernetes Azure. ID klienta označeny jako ID aplikace, když správce služby Azure Stack vytvořit instanční objekt služby.

1. Zadejte **tajný klíč klienta instančního objektu služby**. Toto je tajný kód klienta, které jste získali pro vaše instančnímu objektu služby AD FS od správce služby Azure Stack.

1. Zadejte **verze Kubernetes**. Toto je verze zprostředkovatele služby Kubernetes Azure. Azure Stack uvolní vlastního sestavení Kubernetes pro každou verzi služby Azure Stack.

### <a name="3-summary"></a>3. Souhrn

1. Vyberte souhrn. V okně zobrazí zprávu ověření pro nastavení konfigurace clusteru Kubernetes.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Zkontrolujte nastavení.

3. Vyberte **OK** k nasazení clusteru.

> [!TIP]  
>  Pokud máte dotazy k vašemu nasazení, můžete se zveřejněte svůj dotaz nebo se pokud někdo už odpověděl na dotaz v [fórum pro Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="next-steps"></a>Další postup

[Připojení k vašemu clusteru](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[Povolit řídicí panel Kubernetes](azure-stack-solution-template-kubernetes-dashboard.md)