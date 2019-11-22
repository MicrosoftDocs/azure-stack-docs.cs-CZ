---
title: Přidání požadavků modulu Azure Kubernetes Services (AKS) do tržiště Azure Stack | Microsoft Docs
description: Přečtěte si, jak přidat požadavky na modul AKS do tržiště Azure Stack.
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
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 823ea20bbddaceda970d95008e1214e77ca51f7c
ms.sourcegitcommit: 0b783e262ac87ae67929dbd4c366b19bf36740f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74310120"
---
# <a name="add-the-azure-kubernetes-services-aks-engine-prerequisites-to-the-azure-stack-marketplace"></a>Přidání požadavků modulu Azure Kubernetes Services (AKS) do tržiště Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Přidáním položek popsaných v tomto článku k vašemu Azure Stack můžete uživatelům povolit, aby si nastavili modul Azure Kubernetes Services (AKS). Uživatelé pak mohou nasadit cluster Kubernetes v rámci jediné koordinované operace. Tento článek vás provede kroky potřebnými k tomu, aby byl modul AKS k dispozici pro vaše uživatele v připojených i odpojených prostředích. Modul AKS závisí na identitě služby a na webu Marketplace, ve vlastním rozšíření skriptu a na základní imagi AKS.

> [!IMPORTANT]
> Modul AKS je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="check-your-users-service-offering"></a>Ověření nabídky služeb uživatele

Uživatelé budou potřebovat plán, nabídku a předplatné, které Azure Stack s dostatkem místa. Uživatelé často chtějí nasadit clustery až šesti virtuálních počítačů, které se skládají ze tří hlavních a tří pracovních uzlů. Budete chtít zajistit, aby měli dostatečně velkou kvótu.

Pokud potřebujete další informace o plánování a nastavování nabídky služeb, přečtěte si téma [Přehled nabízených služeb v Azure Stack](service-plan-offer-subscription-overview.md)

## <a name="create-a-service-principal-and-credentials"></a>Vytvoření instančního objektu a přihlašovacích údajů

Cluster Kubernetes bude potřebovat instanční objekt (SPN) a oprávnění na základě rolí v Azure Stack.

### <a name="create-an-spn-in-azure-ad"></a>Vytvoření hlavního názvu služby (SPN) ve službě Azure AD

Pokud pro službu správy identit používáte Azure Active Directory (Azure AD), budete muset vytvořit instanční objekt pro uživatele, kteří nasazují cluster Kubernetes. Vytvořte instanční objekt pomocí tajného klíče klienta. Pokyny najdete v tématu [Vytvoření instančního objektu, který používá pověření tajného klíče klienta](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-a-client-secret-credential).

### <a name="create-an-spn-in-ad-fs"></a>Vytvoření hlavního názvu služby (SPN) v AD FS

Pokud pro službu správy identit používáte službu Active Directory federovaným Services (AD FS), budete muset vytvořit instanční objekt pro uživatele, kteří nasazují cluster Kubernetes. Vytvořte instanční objekt pomocí tajného klíče klienta. Pokyny najdete v tématu [Vytvoření instančního objektu pomocí tajného klíče klienta](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials).

## <a name="add-the-aks-base-image"></a>Přidat základní image AKS

Základní image AKS můžete na Marketplace přidat tak, že získáte položku z Azure. Pokud je ale váš Azure Stack odpojený, použijte tyto pokyny ke [stažení položky Marketplace z Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-1908#disconnected-or-a-partially-connected-scenario) a přidejte položku. Přidejte položku určenou v kroku 5.

Do Marketplace přidejte následující položku:

1. Přihlaste se k [portál pro správu](https://adminportal.local.azurestack.external).

1. Vyberte **všechny služby**a potom v kategorii **Správa** vyberte **Správa Marketplace**.

1. Vyberte **+ Přidat z Azure**.

1. Zadejte `AKS Base`.

1. Vyberte verzi image, která odpovídá verzi modulu AKS. Výpis základní image AKS můžete najít ve verzi AKS Engine v [podporovaných verzích Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). 

    V seznamu vyberte:
    - **Vydavatel**: Služba Azure Kubernetes
    - **Nabídka**: AKS
    - **Verze**: AKS Base image 16,04-LTS image distribuce, říjen 2019 (2019.10.24 nebo verze, která se MAPUJE na AKS Engine)

1. Vyberte **Stáhnout.**

## <a name="add-a-custom-script-extension"></a>Přidání rozšíření vlastních skriptů

Pomocí položky z Azure můžete přidat vlastní skript na Marketplace. Pokud je ale váš Azure Stack odpojený, přidejte položku pomocí pokynů ke [stažení položek z webu Marketplace z Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-1908#disconnected-or-a-partially-connected-scenario) .  Přidejte položku určenou v kroku 5.

1. Otevřete [portál pro správu](https://adminportal.local.azurestack.external).

1. Vyberte **všechny služby** a potom v kategorii **Správa** vyberte **Správa Marketplace**.

1. Vyberte **+ Přidat z Azure**.

1. Zadejte `Custom Script for Linux`.

1. Vyberte skript s následujícím profilem:
   - **Nabídka**: vlastní skript pro Linux 2,0
   - **Verze**: 2.0.6 (nebo nejnovější verze)
   - **Vydavatel**: Microsoft Corp

     > [!Note]  
     > Je možné uvést více než jednu verzi vlastního skriptu pro Linux. Budete muset přidat poslední verzi položky.

1. Vyberte **Stáhnout.**

## <a name="next-steps"></a>Další kroky

[Co je modul AKS v Azure Stack?](../user/azure-stack-kubernetes-aks-engine-overview.md)

[Přehled nabízených služeb v Azure Stack](service-plan-offer-subscription-overview.md)
