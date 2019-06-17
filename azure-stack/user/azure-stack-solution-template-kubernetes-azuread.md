---
title: Nasazení Kubernetes pro Azure Stack pomocí Azure Active Directory (Azure AD) | Dokumentace Microsoftu
description: Informace o nasazení Kubernetes pro Azure Stack pomocí Azure Active Directory (Azure AD).
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
ms.date: 05/17/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 05/17/2019
ms.openlocfilehash: a4fe557175aaa4e2faa6c120645c409c0b3449f5
ms.sourcegitcommit: ad2f2cb4dc8d5cf0c2c37517d5125921cff44cdd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67138935"
---
# <a name="deploy-kubernetes-to-azure-stack-using-azure-active-directory"></a>Nasazení Kubernetes pro Azure Stack pomocí Azure Active Directory

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

> [!Note]  
> Kubernetes ve službě Azure Stack je ve verzi preview. Azure Stack odpojené scénář není aktuálně podporován ve verzi preview.

Postupujte podle kroků v tomto článku pro nasazení a nastavit prostředky pro Kubernetes, při použití služby Azure Active Directory (Azure AD) jako služba pro správu identit. 

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, ujistěte se, že máte správná oprávnění a že služby Azure Stack je připravena.

1. Ověřte, že můžete vytvářet aplikace ve vašem tenantovi Azure Active Directory (Azure AD). Tato oprávnění potřebných pro nasazení Kubernetes.

    Kontrolují se oprávnění, v tématu [zkontrolujte Azure Active Directory oprávnění](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

1. Generovat veřejných a privátních pár klíčů SSH pro přihlášení k virtuálnímu počítači (VM) Linux) v Azure stacku. Veřejný klíč budete potřebovat při vytváření clusteru.

    Generuje se klíč, v tématu [vygenerování klíče SSH](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation).

1. Zkontrolujte, zda máte platné předplatné na portálu Azure Stack tenant a, že máte dostatek veřejné IP adresy pro přidání nové aplikace k dispozici.

    Cluster se nedá nasadit do služby Azure Stack **správce** předplatného. Je nutné použít **uživatele** předplatného. 

1. Pokud nemáte clusteru Kubernetes v marketplace, obraťte se na svého správce služby Azure Stack.

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Služby, které poskytuje vaše aplikace přístup k prostředkům Azure Stack. 

1. Přihlaste se na globální [webu Azure portal](https://portal.azure.com).

1. Zkontrolujte, že jste přihlášení pomocí tenanta Azure AD přidružené k instanci služby Azure Stack. Vaše přihlášení můžete přepnout kliknutím na ikonu filtru v panelu nástrojů webu Azure.

    ![Vyberte tenanta AD](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

1. Vytvořte aplikaci Azure AD.

    a. Přihlaste se ke svému účtu Azure prostřednictvím [webu Azure portal](https://portal.azure.com).  
    b. Vyberte **Azure Active Directory** > **registrace aplikací** > **registrace nové**.  
    c. Zadejte název a adresu URL pro aplikaci.  
    d. Vyberte **podporovaných typů účtu**.  
    e.  Přidat `http://localhost` pro identifikátor URI pro aplikaci. Vyberte **webové** pro typ aplikace, kterou chcete vytvořit. Po nastavení hodnot, vyberte **zaregistrovat**.

1. Poznamenejte si **ID aplikace**. ID budete potřebovat při vytváření clusteru. ID je odkazováno jako **ID klienta instančního objektu**.

1. V okně pro instančnímu objektu. Vyberte **nový tajný kód klienta** > **nastavení** > **klíče** a potom generovat ověřovací klíč pro instančnímu objektu.

    a. Zadejte **popis**.

    b. Vyberte **nikdy nevyprší platnost** pro **Expires**.

    c. Vyberte **Přidat**. Ujistěte se, poznamenejte si řetězec klíče. Řetězec klíče budete potřebovat při vytváření clusteru. Klíč se odkazuje jako **tajný kód klienta instančního objektu**.

## <a name="give-the-service-principal-access"></a>Poskytnout přístup instančního objektu služby

Poskytněte přístup instančního objektu služby do vašeho předplatného, tak, aby objekt zabezpečení může vytvářet prostředky.

1.  Přihlaste se k [portálu Azure Stack](https://portal.local.azurestack.external/).

1. Vyberte **všechny služby** > **předplatná**.

1. Vyberte předplatné vytvořené operátor pro použití clusteru Kubernetes.

1. Vyberte **řízení přístupu (IAM)** > vyberte **přidat přiřazení role**.

1. Vyberte **Přispěvatel** role.

1. Vyberte název aplikace vytvořené pro vaši službu objektu zabezpečení. Bude pravděpodobně nutné do vyhledávacího pole zadejte název.

1. Klikněte na **Uložit**.

## <a name="deploy-kubernetes"></a>Nasazení Kubernetes

1. Otevřít [portálu Azure Stack](https://portal.local.azurestack.external).

1. Vyberte **+ vytvořit prostředek** > **Compute** > **clusteru Kubernetes**. Poté klikněte na **Vytvořit**.

    ![Vytvoření clusteru Kubernetes ve službě Azure Stack](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1. Základy

1. Vyberte **Základy** ve vytvoření clusteru Kubernetes.

    ![Konfigurace základního nastavení - clusteru Kubernetes](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. Vyberte vaše **předplatné** ID.

1. Zadejte název nové skupiny prostředků nebo vyberte existující skupinu prostředků. Název prostředku musí být alfanumerické znaky a malá písmena.

1. Vyberte **umístění** skupiny prostředků. Toto umístění je oblast, kterou jste vybrali pro instalaci sady Azure Stack.

### <a name="2-kubernetes-cluster-settings"></a>2. Nastavení clusteru Kubernetes

1. Vyberte **nastavení clusteru Kubernetes** ve vytvoření clusteru Kubernetes.

    ![Nastavení clusteru Kubernetes](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-aad.png)

1. Zadejte **uživatelské jméno správce virtuálního počítače Linux**. Tento název je uživatelské jméno pro Linuxové virtuální počítače, které jsou součástí clusteru Kubernetes a DVM.

1. Zadejte **veřejný klíč SSH** se používají pro autorizaci pro všechny počítače s Linuxem vytvořili jako součást clusteru Kubernetes a DVM.

1. Zadejte **předpony DNS hlavního uzlu profilu**. Tento název musí být oblasti jedinečný, jako například `k8s-12345`. Pokusí se provede porovnání názvu skupiny prostředků jako osvědčený postup.

    > [!Note]  
    > Pro každý cluster použijte předponu DNS nových a jedinečných hlavní profilu.

1. Vyberte **počet profilů fondu hlavní Kubernetes**. Počet obsahuje počet uzlů ve fondu hlavní. Může být od 1 do 7. Tato hodnota by měla být liché číslo.

1. Vyberte **The VMSize hlavních virtuálních počítačů Kubernetes**.

1. Vyberte **počet profil fond uzlů Kubernetes**. Počet obsahuje počet agentů v clusteru. 

1. Vyberte **profil úložiště**. Můžete zvolit **objektu Blob disku** nebo **spravovaného disku**. Tento profil určuje virtuální počítače uzlů virtuálních počítačů velikosti Kubernetes. 

1. Vyberte **Azure AD** pro **systém identit Azure Stack** pro vaši instalaci služby Azure Stack. 

1. Zadejte **ID klienta instančního objektu**. Tento identifikátor se používá od poskytovatele cloudu Kubernetes Azure. ID klienta se označuje jako ID aplikace, při vytváření instančního objektu služby.

1. Zadejte **tajný kód klienta instančního objektu** , který jste vytvořili při vytváření instančního objektu služby.

1. Zadejte **verze zprostředkovatele služby Kubernetes Azure cloudu**. Toto je číslo verze zprostředkovatele služby Kubernetes Azure. Azure Stack uvolní vlastního sestavení Kubernetes pro každou verzi služby Azure Stack.

### <a name="3-summary"></a>3. Souhrn

1. Vyberte souhrn. V okně zobrazí zprávu ověření pro nastavení konfigurace clusteru Kubernetes.

    ![Nasadit šablonu řešení](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. Zkontrolujte nastavení.

3. Vyberte **OK** k nasazení clusteru.

> [!TIP]  
>  Pokud máte nějaké otázky k vašemu nasazení, zveřejněte svůj dotaz nebo se pokud někdo už odpověděl na dotaz v [fórum pro Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).


## <a name="next-steps"></a>Další postup

[Připojení k vašemu clusteru](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[Povolit řídicí panel Kubernetes](azure-stack-solution-template-kubernetes-dashboard.md)
