---
title: Zpřístupnění sady škálování virtuálních počítačů v centru Azure Stack
description: Přečtěte si, jak operátor cloudu může přidat sady škálování virtuálních počítačů do webu Azure Stack hub Marketplace.
author: sethmanheim
ms.topic: article
ms.date: 02/08/2021
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 10/22/2019
ms.openlocfilehash: 08655238a3f0cb1212bde455a0f601211e6edc16
ms.sourcegitcommit: 824fd33fd5d6aa0c0dac06c21b592bdb60378940
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99850951"
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

1. Přihlaste se k portálu centra Azure Stack. Pak klikněte na **všechny služby**, pak na **Virtual Machine Scale Sets** a potom v části **COMPUTE** vyberte **Virtual Machine Scale Sets**.
   [![Výběr sady škálování virtuálních počítačů](media/azure-stack-compute-add-scalesets/all-services-small.png)](media/azure-stack-compute-add-scalesets/all-services.png#lightbox)

2. Vyberte **Přidat**.

   ![Vytvoření škálovací sady virtuálních počítačů](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. Vyplňte prázdná pole a vyberte z rozevíracích seznamů pro **image disku s operačním systémem**, **předplatné** a **velikost instance**. Vyberte **Ano** , pokud chcete **používat spravované disky**. Potom vyberte **Vytvořit**.
    [![Konfigurace a vytvoření sady škálování pro virtuální počítače](media/azure-stack-compute-add-scalesets/create-small.png)](media/azure-stack-compute-add-scalesets/create.png#lightbox)

4. Pokud chcete zobrazit novou sadu škálování virtuálních počítačů, přejděte na **všechny prostředky**, vyhledejte název sady škálování virtuálního počítače a potom vyberte jeho název ve vyhledávání.
   [![Zobrazit sadu škálování virtuálního počítače](media/azure-stack-compute-add-scalesets/search-small.png)](media/azure-stack-compute-add-scalesets/search.png#lightbox)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Aktualizace imagí v sadě škálování virtuálního počítače

Po vytvoření sady škálování virtuálních počítačů můžou uživatelé aktualizovat obrázky v sadě škálování, aniž by bylo nutné znovu vytvořit sadu škálování. Proces aktualizace bitové kopie závisí na následujících scénářích:

1. Šablona nasazení sady škálování virtuálního počítače určuje **nejnovější** **verzi** pro:  

   Když `version` je v části šablony pro sadu škálování nastavená na **nejnovější** `imageReference` , pro operace škálování v sadě škálování použijte nejnovější dostupnou verzi image pro instance sady škálování. Po dokončení škálování můžete odstranit starší instance sady Virtual Machine Scale Sets. Hodnoty pro `publisher` , `offer` a `sku` zůstávají beze změny.

   Následující příklad JSON Určuje `latest` :  

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

Velikost sady škálování virtuálního počítače můžete změnit tak, aby byla větší nebo menší.

1. Na portálu vyberte sadu škálování a pak vyberte **škálování**.

2. Pomocí panelu snímků nastavte novou úroveň škálování pro tuto sadu škálování virtuálního počítače a pak klikněte na **Uložit**.

     [![Škálovat sadu virtuálních počítačů](media/azure-stack-compute-add-scalesets/scale-small.png)](media/azure-stack-compute-add-scalesets/scale.png#lightbox)

## <a name="next-steps"></a>Další kroky

* [Stažení položek z webu Marketplace z Azure do centra Azure Stack](azure-stack-download-azure-marketplace-item.md)
