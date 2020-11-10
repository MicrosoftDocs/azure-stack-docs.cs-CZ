---
title: Přehled centra Azure Stack Marketplace
description: Přehled položek centra Azure Stack Marketplace a Marketplace.
author: sethmanheim
ms.topic: article
ms.date: 11/09/2020
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 99dc19841bb57beae626ed7ca0a0c18b38ad4f1d
ms.sourcegitcommit: 980be7813e6f39fb59926174a5d3e0d392b04293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414179"
---
# <a name="azure-stack-hub-marketplace-overview"></a>Přehled centra Azure Stack Marketplace

Tržiště centra Azure Stack je kolekce služeb, aplikací a prostředků přizpůsobených pro centrum Azure Stack. Prostředky zahrnují sítě, virtuální počítače (VM), úložiště a další. Pomocí tržiště Azure Stack Hub můžete vytvářet nové prostředky a nasazovat nové aplikace, nebo procházet a vybírat položky, které chcete použít. Chcete-li použít položku Marketplace, uživatelé se musí přihlásit k odběru nabídky, která jim udělí přístup k této položce.

Jako operátor centra Azure Stack se rozhodujete, které položky Přidat (publikovat) do centra Azure Stack hub Marketplace. Můžete publikovat položky, jako jsou databáze, App Services a další. Publikování zpřístupňuje položky všem uživatelům. Můžete publikovat vlastní položky, které vytvoříte, nebo můžete publikovat položky z rostoucího [seznamu Azure Marketplace položek](azure-stack-marketplace-azure-items.md). Když publikujete položku na tržišti centra Azure Stack, uživatelé ji uvidí během pěti minut.

> [!CAUTION]  
> Všechny artefakty položek galerie, včetně obrázků a souborů JSON, jsou přístupné bez ověřování po jejich zpřístupnění v tržišti Azure Stack hub. Další informace o publikování vlastních položek Marketplace najdete v tématu [Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Pokud chcete otevřít Marketplace, na portálu pro správu vyberte **+ vytvořit prostředek**.

![Vytvoření prostředku na portálu pro správu centra Azure Stack](media/azure-stack-marketplace/marketplace1.png)

## <a name="marketplace-items"></a>Položky Marketplace

Položka webu Marketplace centra Azure Stack je služba, aplikace nebo prostředek, které si uživatelé můžou stáhnout a používat. Všechny položky Marketplace centra Azure Stack jsou viditelné všem uživatelům, včetně administrativních položek, jako jsou plány a nabídky. Tyto položky správy nevyžadují zobrazení předplatného, ale nefungují pro uživatele, kteří nejsou funkční.

Každá položka tržiště má:

* Šablona Azure Resource Manager pro zřizování prostředků
* Metadata, jako jsou řetězce, ikony a další marketingové materiály.
* Formátování informací pro zobrazení položky na portálu.

Každá položka publikovaná na tržišti centra Azure Stack používá formát balíčku Galerie Azure (. azpkg). Přidejte prostředky pro nasazení nebo modul runtime (soubory s příponou. zip se softwarem nebo image virtuálních počítačů), aby se Azure Stack hub samostatně, nikoli jako součást položky Marketplace.

S verzí 1803 a novějším Azure Stack hub při stahování z Azure nebo při nahrávání vlastních imagí převede obrázky na řídké soubory. Tento proces přidá čas při přidávání obrázku, ale šetří místo a zrychluje nasazení těchto imagí. Převod se vztahuje pouze na nové image. Existující image se nemění.

## <a name="next-steps"></a>Další kroky

* [Stažení stávajících položek Marketplace z Azure a publikování do centra Azure Stack](azure-stack-download-azure-marketplace-item.md)  
* [Vytvoření a publikování položky Marketplace pro vlastní Azure Stack centra](azure-stack-create-and-publish-marketplace-item.md)
