---
title: Zveřejnění položky marketplace vlastní ve službě Azure Stack (operátor cloudu) | Dokumentace Microsoftu
description: Jako operátory Azure stacku zjistěte, jak publikovat vlastní marketplace položku ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/09/2019
ms.date: 02/18/2019
ms.author: v-jay
ms.reviewer: unknown
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 6c1750e6a523828400b3d06d4e1c22fb34f8273f
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "64292703"
---
# <a name="azure-stack-marketplace-overview"></a>Přehled služby Azure Stack Marketplace

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tržiště Azure Stack je kolekce služeb, aplikací a prostředků, které jsou přizpůsobené pro službu Azure Stack. Prostředky patří sítě, virtuální počítače, úložiště a další. Tržiště můžete využít k vytvoření nových prostředků a nasazení nové aplikace; nebo procházet a vyberte položky, které chcete použít. Pokud chcete použít položky Marketplace, musí uživatelé přihlásit k nabídky, která jim uděluje přístup k položce.

Jako operátory Azure stacku, rozhodnete položky, které chcete přidat (publikování) na webu Marketplace. Můžete publikovat položky, jako jsou databáze, aplikační služby a tak dále. Publikování jsou viditelné všem uživatelům. Můžete publikovat vlastní položky, které vytvoříte, nebo můžete publikovat položky z stále se rozšiřující [seznam položek z Azure Marketplace](azure-stack-marketplace-azure-items.md). Při publikování položky Marketplace, uživatelé je můžou zobrazit během pěti minut.

> [!CAUTION]  
> Všechny artefakty položky galerie, včetně obrázků a soubory JSON, jsou přístupné bez ověřování po provedení dostupných v Tržišti Azure Stack. Další aspekty při publikování položky marketplace vlastní, naleznete v tématu [vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Chcete-li otevřít na webu Marketplace, v portálu vyberte správce **+ vytvořit prostředek**.

![Marketplace](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Položky Marketplace

Položka Azure Stack Marketplace je služby, aplikace nebo prostředku, které uživatelé můžou stáhnout a použít. Všechny položky Azure Marketplace zásobníku jsou viditelné pro všechny uživatele, včetně položky pro správu, jako je například plány a nabídky. Tyto položky se nevyžaduje předplatné zobrazení, ale jsou uživatelům nefunkční.

Má každá položka Marketplace:

* Šablony Azure Resource Manageru pro zřizování prostředků.
* Metadata, například řetězce, ikony a další marketingové materiály.
* Informace o formátování k zobrazení položek v portálu.

Všechny položky na webu Marketplace publikovat ve formátu balíčku Galerie Azure (.azpkg). Přidat nasazení nebo modul runtime prostředky (kód, soubory zip s softwaru nebo Image virtuálních počítačů) do služby Azure Stack samostatně, nikoli jako součást položky Marketplace.

Verze 1803 nebo novější Azure Stack imagí při převádí na zhuštěné soubory stáhnou z Azure nebo při nahrání vlastních imagí. Tento postup přidá čas při přidávání obrázku, ale šetří místo a urychluje nasazení těchto imagí. Převod platí jenom pro nové Image. Existující Image se nezmění.

## <a name="next-steps"></a>Další postup

* [Stažení položek z Marketplace](azure-stack-download-azure-marketplace-item.md)  
* [Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md)

<!-- Update_Description: wording update -->
