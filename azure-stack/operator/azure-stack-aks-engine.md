---
title: Přidání požadavků modulu Azure Kubernetes Services (AKS) do webu služby Azure Stack hub Marketplace
description: Přečtěte si, jak přidat požadavky na modul AKS do tržiště centra Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/16/2020
ms.openlocfilehash: 12532ba9e38c37bdf3253a5e33576ca63116f1e0
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99246159"
---
# <a name="add-the-azure-kubernetes-services-aks-engine-prerequisites-to-the-azure-stack-hub-marketplace"></a>Přidání požadavků modulu Azure Kubernetes Services (AKS) do tržiště centra Azure Stack

Můžete nastavit modul AKS (Azure Kubernetes Services) pro vaše uživatele. Přidejte položky popsané v tomto článku do centra Azure Stack. Uživatelé pak mohou nasadit cluster Kubernetes v rámci jediné koordinované operace. Tento článek vás provede kroky potřebnými k tomu, aby byl modul AKS k dispozici pro vaše uživatele v připojených i odpojených prostředích. Modul AKS závisí na identitě služby a na webu Marketplace, ve vlastním rozšíření skriptu a na základní imagi AKS. Modul AKS vyžaduje, abyste spustili [Azure Stack Hub 1910](release-notes.md?view=azs-1910&preserve-view=true) nebo vyšší.

> [!NOTE]  
> Mapování Azure Stackho centra na číslo verze modulu AKS najdete v [poznámkách k verzi modulu AKS](../user/kubernetes-aks-engine-release-notes.md#aks-engine-and-azure-stack-version-mapping).

## <a name="check-your-users-service-offering"></a>Ověření nabídky služeb uživatele

Uživatelé budou potřebovat plán, nabídku a předplatné Azure Stack centra s dostatkem místa. Uživatelé často chtějí nasadit clustery až šesti virtuálních počítačů, které se skládají ze tří hlavních a tří pracovních uzlů. Budete chtít zajistit, aby měli dostatečně velkou kvótu.

Pokud potřebujete další informace o plánování a nastavování nabídky služeb, přečtěte si téma [Přehled nabízených služeb v centru Azure Stack](service-plan-offer-subscription-overview.md) .

## <a name="create-a-service-principal-and-credentials"></a>Vytvoření instančního objektu a přihlašovacích údajů

Cluster Kubernetes bude potřebovat instanční objekt (SPN) a oprávnění na základě rolí ve službě Azure Stack hub.

### <a name="create-an-spn-in-azure-ad"></a>Vytvoření hlavního názvu služby (SPN) ve službě Azure AD

Pokud pro službu správy identit používáte Azure Active Directory (Azure AD), budete muset vytvořit instanční objekt pro uživatele, kteří nasazují cluster Kubernetes. Vytvořte instanční objekt pomocí tajného klíče klienta. Pokyny najdete v tématu [Vytvoření instančního objektu, který používá pověření tajného klíče klienta](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-a-client-secret-credential).

### <a name="create-an-spn-in-ad-fs"></a>Vytvoření hlavního názvu služby (SPN) v AD FS

Pokud pro službu správy identit používáte službu Active Directory federovaným Services (AD FS), budete muset vytvořit instanční objekt pro uživatele, kteří nasazují cluster Kubernetes. Vytvořte instanční objekt pomocí tajného klíče klienta. Pokyny najdete v tématu [Vytvoření instančního objektu pomocí tajného klíče klienta](azure-stack-create-service-principals.md#create-a-service-principal-that-uses-client-secret-credentials).

## <a name="add-the-aks-base-image"></a>Přidat základní image AKS

Základní image AKS můžete na Marketplace přidat tak, že získáte položku z Azure. Pokud je ale vaše centrum Azure Stack odpojené, použijte tyto pokyny ke [stažení položky Marketplace z Azure](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected) a přidejte položku. Přidejte položku určenou v kroku 5.

Do Marketplace přidejte následující položku:

1. Přihlaste se k portálu pro správu `https://adminportal.local.azurestack.external` .

1. Vyberte **všechny služby** a potom v kategorii **Správa** vyberte **Správa Marketplace**.

1. Vyberte **+ Přidat z Azure**.

1. Zadejte `AKS Base`.

1. Vyberte verzi image, která odpovídá verzi modulu AKS. Výpis základní image AKS můžete najít ve verzi AKS Engine v [podporovaných verzích Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions). 

    V seznamu vyberte:
    - **Vydavatel**: Služba Azure Kubernetes
    - **Nabídka**: AKS
    - **Název**: AKS Base Ubuntu 16,04-LTS image distribuce, srpen 2020 (2020.09.14 nebo verze, která se MAPUJE na AKS Engine)
    - **Verze**: 2020.09.14

1. Vyberte **Stáhnout.**

## <a name="add-a-custom-script-extension"></a>Přidání rozšíření vlastních skriptů

Pomocí položky z Azure můžete přidat vlastní skript na Marketplace. Pokud je ale vaše centrum Azure Stack odpojené, přidejte položku pomocí pokynů ke [stažení položek z webu Marketplace z Azure](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected) .  Přidejte položku určenou v kroku 5.

1. Otevřete portál pro správu `https://adminportal.local.azurestack.external` .

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

[Co je modul AKS v centru Azure Stack?](../user/azure-stack-kubernetes-aks-engine-overview.md)

[Přehled nabízených služeb v centru Azure Stack](service-plan-offer-subscription-overview.md)