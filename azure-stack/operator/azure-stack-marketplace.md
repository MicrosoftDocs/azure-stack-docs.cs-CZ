---
title: Publikování vlastní položky Marketplace v Azure Stack (operátor cloudu) | Microsoft Docs
description: Jako operátor Azure Stack se dozvíte, jak publikovat vlastní položku Marketplace v Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: be62ddc3c3e5ea7180164cac95edd125030cbc6a
ms.sourcegitcommit: 58c28c0c4086b4d769e9d8c5a8249a76c0f09e57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68959502"
---
# <a name="azure-stack-marketplace-overview"></a>Přehled Azure Stack Marketplace

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Azure Stack Marketplace je kolekce služeb, aplikací a prostředků přizpůsobených pro Azure Stack. Prostředky zahrnují sítě, virtuální počítače, úložiště a další. Pomocí Marketplace můžete vytvářet nové prostředky a nasazovat nové aplikace. nebo vyhledejte a vyberte položky, které chcete použít. Chcete-li použít položku Marketplace, uživatelé se musí přihlásit k odběru nabídky, která jim udělí přístup k této položce.

Jako operátor Azure Stack rozhodujete, které položky Přidat (publikovat) na webu Marketplace. Můžete publikovat položky, jako jsou databáze, App Services a další. Publikování zpřístupňuje položky všem uživatelům. Můžete publikovat vlastní položky, které vytvoříte, nebo můžete publikovat položky z rostoucího [seznamu Azure Marketplace položek](azure-stack-marketplace-azure-items.md). Když publikujete položku na webu Marketplace, uživatelé ji uvidí během pěti minut.

> [!CAUTION]  
> Všechny artefakty položek galerie, včetně obrázků a souborů JSON, jsou přístupné bez ověřování po jejich zpřístupnění na Azure Stack Marketplace. Další informace o publikování vlastních položek Marketplace najdete v tématu [Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Pokud chcete otevřít Marketplace, na portálu pro správu vyberte **+ vytvořit prostředek**.

![Marketplace](media/azure-stack-marketplace/marketplace1.png)

## <a name="marketplace-items"></a>Položky Marketplace

Položka Azure Stack Marketplace je služba, aplikace nebo prostředek, kterou si uživatelé můžou stáhnout a používat. Všechny položky Azure Stack Marketplace jsou viditelné všem uživatelům, včetně položek pro správu, jako jsou plány a nabídky. Tyto položky správy nevyžadují zobrazení předplatného, ale uživatelům nejsou funkční.

Každá položka tržiště má:

* Šablona Azure Resource Manager pro zřizování prostředků
* Metadata, jako jsou řetězce, ikony a další marketingové materiály.
* Formátování informací pro zobrazení položky na portálu.

Každá položka publikovaná na webu Marketplace používá formát balíčku Galerie Azure (. azpkg). Přidejte prostředky pro nasazení nebo modul runtime (soubory s příponou. zip s software nebo image virtuálních počítačů), které se Azure Stack samostatně, nikoli jako součást položky Marketplace.

S verzí 1803 a novějším Azure Stack při stahování z Azure nebo při nahrávání vlastních imagí převádět obrázky na řídké soubory. Tento proces přidá čas při přidávání obrázku, ale šetří místo a zrychluje nasazení těchto imagí. Převod se vztahuje pouze na nové image. Existující image se nemění.

## <a name="next-steps"></a>Další kroky

* [Stažení položek z Marketplace](azure-stack-download-azure-marketplace-item.md)  
* [Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md)
