---
title: Zpřístupnění sady škálování virtuálních počítačů v centru Azure Stack | Microsoft Docs
description: Přečtěte si, jak operátor cloudu může přidat sady škálování virtuálních počítačů do webu Azure Stack hub Marketplace.
services: azure-stack
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: dcd4f1d6ff9eaae4dce629ce576495e3304403aa
ms.sourcegitcommit: ecb541f53255c6a4433724ad2d20fb93c4720ce1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76706843"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack-hub"></a>Zpřístupnění sady škálování virtuálních počítačů v centru Azure Stack

Služby Virtual Machine Scale Sets jsou výpočetním prostředkem Azure Stack hub. Sady škálování můžete použít k nasazení a správě sady identických virtuálních počítačů (VM). U všech virtuálních počítačů nakonfigurovaných stejným způsobem sady škálování nevyžadují předběžné zřizování virtuálních počítačů. Sestavování rozsáhlých služeb, které cílí na Big COMPUTE, velké objemy dat a kontejnerové úlohy, je snazší.

Tento článek vás provede procesem vytváření sad škálování v tržišti Azure Stack hub. Po dokončení tohoto postupu můžou uživatelé přidat ke svým předplatným sadu škálování virtuálních počítačů.

Sada škálování virtuálních počítačů na rozbočovači Azure Stack je podobná službě Virtual Machine Scale Sets v Azure. Další informace najdete v následujících videích:

* [Mark Russinovich hovoří o škálovacích sadách Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Virtual Machine Scale Sets s Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

V Azure Stackovém centru nepodporuje Virtual Machine Scale Sets automatické škálování. Do sady škálování můžete přidat další instance pomocí Správce prostředků šablon, rozhraní příkazového řádku nebo PowerShellu.

## <a name="prerequisites"></a>Požadavky

* **Tržiště centra Azure Stack:** Pokud chcete povolit dostupnost položek na webu centra Azure Stack, zaregistrujte Azure Stack hub s globálním Azure. Postupujte podle pokynů v části [registrace centra Azure Stack s Azure](azure-stack-registration.md).
* **Bitová kopie operačního systému:** Než začnete vytvářet škálu virtuálních počítačů, musíte si stáhnout image virtuálních počítačů pro použití v sadě škálování z [webu centra Azure Stack](azure-stack-download-azure-marketplace-item.md). Předtím, než může uživatel vytvořit novou sadu škálování, již musí být image přítomné.

## <a name="use-the-azure-stack-hub-portal"></a>Použití portálu centra Azure Stack

>[!IMPORTANT]  
> Informace v této části se vztahují na použití centra Azure Stack verze 1808 nebo novější.

1. Přihlaste se k portálu centra Azure Stack. Pak klikněte na **všechny služby**, pak na **Virtual Machine Scale Sets**a potom v části **COMPUTE**vyberte **Virtual Machine Scale Sets**.
   ![vybrat Virtual Machine Scale Sets](media/azure-stack-compute-add-scalesets/all-services.png)

2. Vyberte ***vytvořit Virtual Machine Scale Sets***.
   ![Vytvoření škálovací sady virtuálních počítačů](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. Vyplňte prázdná pole a vyberte z rozevíracích seznamů pro **image disku s operačním systémem**, **předplatné**a **velikost instance**. Vyberte **Ano** , pokud chcete **používat spravované disky**. Poté klikněte na možnost **Vytvořit**.
    ![nakonfigurovat a vytvořit Virtual Machine Scale Sets](media/azure-stack-compute-add-scalesets/create.png)

4. Pokud chcete zobrazit novou sadu škálování virtuálních počítačů, přejděte na **všechny prostředky**, vyhledejte název sady škálování virtuálního počítače a potom vyberte jeho název ve vyhledávání.
   ![zobrazit sadu škálování virtuálního počítače](media/azure-stack-compute-add-scalesets/search.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Aktualizace imagí v sadě škálování virtuálního počítače

Po vytvoření sady škálování virtuálních počítačů můžou uživatelé aktualizovat obrázky v sadě škálování, aniž by bylo nutné znovu vytvořit sadu škálování. Proces aktualizace bitové kopie závisí na následujících scénářích:

1. Šablona nasazení sady škálování virtuálního počítače určuje **nejnovější** **verzi**pro:  

   Pokud je v části `imageReference` šablony pro sadu škálování nastavená `version` na **nejnovější** , pro operace škálování v sadě škálování použijte nejnovější dostupnou verzi image pro instance sady škálování. Po dokončení škálování můžete odstranit starší instance sady Virtual Machine Scale Sets. Hodnoty pro `publisher`, `offer`a `sku` zůstávají beze změny.

   Následující příklad JSON určuje `latest`:  

    ```json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

2. Šablona nasazení sady škálování virtuálních počítačů **neurčuje** pro **verzi** hodnotu nejnovější a místo toho Určuje číslo verze:  

    Pokud operátor Azure Stack stáhne obrázek v novější verzi (a odstraní starší verzi), sada škálování se nedá škálovat nahoru. Jedná se o návrh, protože je k dispozici verze image určená v šabloně sady škálování.  

Další informace najdete v tématu [disky a image operačního systému](../user/azure-stack-compute-overview.md#operating-system-disks-and-images).  

## <a name="scale-a-virtual-machine-scale-set"></a>Škálování sady škálování virtuálních počítačů

Velikost sady škálování virtuálního počítače můžete škálovat tak, aby byla větší nebo menší.

1. Na portálu vyberte sadu škálování a pak vyberte **škálování**.

2. Pomocí panelu snímků nastavte novou úroveň škálování pro tuto sadu škálování virtuálního počítače a pak klikněte na **Uložit**.

     ![Škálovat sadu virtuálních počítačů](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="next-steps"></a>Další kroky

* [Stažení položek z webu Marketplace z Azure do centra Azure Stack](azure-stack-download-azure-marketplace-item.md)
