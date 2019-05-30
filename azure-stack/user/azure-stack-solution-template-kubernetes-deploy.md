---
title: Nasazení Kubernetes pro kontejnery služby Azure Stack | Dokumentace Microsoftu
description: Informace o nasazení Kubernetes pomocí služby Azure Stack pomocí kontejnerů.
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
ms.date: 02/27/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 672528f05a4fb3214e9a97e78922eeb12739c44f
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269546"
---
# <a name="deploy-kubernetes-to-use-containers-with-azure-stack"></a>Nasazení Kubernetes pomocí služby Azure Stack pomocí kontejnerů

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

> [!Note]  
> Kubernetes ve službě Azure Stack je ve verzi preview. Odpojené scénář Azure Stack není aktuálně podporován ve verzi preview.

Můžete postupovat podle kroků v tomto článku pro nasazení a nastavit prostředky pro Kubernetes v rámci jediné koordinované operace. Kroky pomocí šablony Azure Resource Manageru řešení. Budete potřebovat získat požadované informace o instalaci Azure Stack, generovat šablony a pak nasadit do cloudu. Šablony služby Azure Stack nepoužívá stejnou spravované služby AKS nabízí na global Azure.

## <a name="kubernetes-and-containers"></a>Kubernetes a kontejnerů

Můžete nainstalovat Kubernetes pomocí šablon Azure Resource Manageru vygenerované modulem AKS ve službě Azure Stack. [Kubernetes](https://kubernetes.io) je open source systém pro automatizaci nasazení, škálování a správu aplikací v kontejnerech. A [kontejneru](https://www.docker.com/what-container) je do image. Image kontejneru se podobá virtuální počítač (VM), ale na rozdíl od virtuálního počítače, kontejneru obsahuje pouze prostředky, které potřebuje ke spuštění aplikace, jako je například kód runtime umožňující spouštění kódu, konkrétní knihovny a nastavení.

Můžete použít Kubernetes:

- Vývoj široce škálovatelné upgradovatelných, aplikací, které je možné nasadit v řádu sekund. 
- Zjednodušit návrh aplikace a zvýšit jeho spolehlivost různými aplikacemi Helm. [Příkaz Helm](https://github.com/kubernetes/helm) je balení open source nástroj, který vám pomůže nainstalovat a spravovat životní cyklus aplikací Kubernetes.
- Snadno monitorovat a diagnostikovat stav vašich aplikací.

Můžete pouze účtovat výpočetní využití vyžadované uzly podpora clusteru. Další informace najdete v tématu [využití a fakturace ve službě Azure Stack](../operator/azure-stack-billing-and-chargeback.md).

## <a name="deploy-kubernetes-to-use-containers"></a>Nasazení Kubernetes pro kontejnery

Kroky k nasazení clusteru Kubernetes ve službě Azure Stack, bude záviset na vaši službu identity management. Ověřte řešení správy identit používané pro vaši instalaci služby Azure Stack. Obraťte se na správce služby Azure Stack k ověření služby identity management.

- **Azure Active Directory (Azure AD)**  
Pokyny k instalaci clusteru při použití služby Azure AD najdete v tématu [nasazení Kubernetes pomocí služby Azure Active Directory (Azure AD) služby Azure Stack](azure-stack-solution-template-kubernetes-azuread.md).

- **Služby Active Directory Federated Services (AD FS)**  
Pokyny k instalaci clusteru při použití služby AD FS najdete v tématu [nasazení Kubernetes pomocí Active Directory Federated Services (AD FS) služby Azure Stack](azure-stack-solution-template-kubernetes-adfs.md).

## <a name="connect-to-your-cluster"></a>Připojit ke clusteru

Nyní jste připraveni připojit ke clusteru. Hlavní najdete ve vaší skupině prostředků clusteru a názvem `k8s-master-<sequence-of-numbers>`. Pro připojení k hlavnímu serveru pomocí klienta SSH. Na hlavním serveru, můžete použít **kubectl**, klienta příkazového řádku Kubernetes pro správu clusteru. Pokyny najdete v tématu [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Můžete také zjistit **Helm** Správce balíčků, které jsou užitečné pro instalaci a nasazování aplikací do clusteru. Pokyny k instalaci a pomocí Helm clusteru najdete v tématu [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Další postup

[Povolit řídicí panel Kubernetes](azure-stack-solution-template-kubernetes-dashboard.md)

[Přidání Kubernetes na webu Marketplace (pro operátory Azure stacku)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[Nasazení Kubernetes pro Azure Stack pomocí Azure Active Directory (Azure AD)](azure-stack-solution-template-kubernetes-azuread.md)

[Nasazení Kubernetes pro Azure Stack pomocí Active Directory Federated Services (AD FS)](azure-stack-solution-template-kubernetes-adfs.md)

[Kubernetes v Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
