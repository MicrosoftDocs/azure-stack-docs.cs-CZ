---
title: Nasazení Kubernetes pro použití kontejnerů Azure Stack | Microsoft Docs
description: Naučte se, jak nasadit Kubernetes, abyste mohli používat kontejnery s Azure Stack.
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
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 74cad0b1f41c5c764bef361f3f521162eec59198
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277652"
---
# <a name="deploy-kubernetes-to-use-containers-with-azure-stack"></a>Nasazení Kubernetes pro použití kontejnerů s Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

> [!Note]  
> Kubernetes on Azure Stack je ve verzi Preview. V tuto chvíli není ve verzi Preview podporován Azure Stack odpojený scénář. Pro scénáře vývoje a testování používejte jenom položku Marketplace.

Postup v tomto článku můžete použít k nasazení a nastavení prostředků pro Kubernetes v rámci jediné koordinované operace. Kroky používají šablonu řešení Azure Resource Manager. Budete potřebovat shromáždit požadované informace o instalaci Azure Stack, vygenerovat šablonu a pak ji nasadit do svého cloudu. Šablona Azure Stack nepoužívá stejnou spravovanou službu AKS nabízenou v globálním Azure.

## <a name="kubernetes-and-containers"></a>Kubernetes a kontejnery

Kubernetes můžete nainstalovat pomocí šablon Azure Resource Manager generovaných modulem AKS v Azure Stack. [Kubernetes](https://kubernetes.io) je open source systém pro automatizaci nasazení, škálování a správu aplikací v kontejnerech. [Kontejner](https://www.docker.com/what-container) je v obrázku. Image kontejneru se podobá virtuálnímu počítači (VM), ale na rozdíl od virtuálního počítače, ale obsahuje pouze prostředky, které potřebuje ke spuštění aplikace, jako je například kód, modul runtime pro spuštění kódu, konkrétní knihovny a nastavení.

Kubernetes můžete použít k těmto akcím:

- Můžete vyvíjet široce škálovatelné, škálovatelné aplikace, které se dají nasadit během několika sekund. 
- Zjednodušte návrh aplikace a zvyšte jejich spolehlivost různými Helm aplikacemi. [Helm](https://github.com/kubernetes/helm) je open source nástroj pro balení, který vám pomůže s instalací a správou životního cyklu aplikací Kubernetes.
- Snadné monitorování a Diagnostika stavu aplikací.

Budou se vám účtovat jenom výpočetní využití vyžadovaná uzly podporujícími váš cluster. Další informace najdete v tématu [využití a fakturace v Azure Stack](../operator/azure-stack-billing-and-chargeback.md).

## <a name="deploy-kubernetes-to-use-containers"></a>Nasazení Kubernetes pro použití kontejnerů

Postup nasazení clusteru Kubernetes na Azure Stack bude záviset na vaší službě správy identit. Ověřte řešení správy identit používané instalací Azure Stack. Obraťte se na správce Azure Stack a ověřte vaši službu správy identit.

- **Azure Active Directory (Azure AD)**  
Pokyny k instalaci clusteru při použití Azure AD najdete v tématu [nasazení Kubernetes pro Azure Stack používání Azure Active Directory (Azure AD)](azure-stack-solution-template-kubernetes-azuread.md).

- **Federované služby Active Directory (AD FS)**  
Pokyny k instalaci clusteru při použití AD FS najdete v tématu [nasazení Kubernetes pro Azure Stack používání služby Active Directory federovaným Services (AD FS)](azure-stack-solution-template-kubernetes-adfs.md).

## <a name="connect-to-your-cluster"></a>Připojení ke clusteru

Nyní jste připraveni připojit se ke clusteru. Hlavní stránku najdete ve vaší skupině prostředků clusteru a má název `k8s-master-<sequence-of-numbers>`. Použijte klienta SSH pro připojení k hlavnímu serveru. V hlavní části můžete ke správě clusteru použít **kubectl**a klienta příkazového řádku Kubernetes. Pokyny najdete v tématu [Kubernetes.IO](https://kubernetes.io/docs/reference/kubectl/overview).

Můžete také najít správce balíčků **Helm** , který je užitečný pro instalaci a nasazení aplikací do clusteru. Pokyny k instalaci a používání Helm s clusterem najdete v tématu [Helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Další kroky

[Povolit řídicí panel Kubernetes](azure-stack-solution-template-kubernetes-dashboard.md)

[Přidání Kubernetesu na Marketplace (pro operátor Azure Stack)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[Nasazení Kubernetes pro Azure Stack pomocí Azure Active Directory (Azure AD)](azure-stack-solution-template-kubernetes-azuread.md)

[Nasazení Kubernetes k Azure Stack pomocí služby Active Directory federovaných služeb (AD FS)](azure-stack-solution-template-kubernetes-adfs.md)

[Kubernetes v Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
