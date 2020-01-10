---
title: Zpřístupnění sady škálování virtuálních počítačů v centru Azure Stack | Microsoft Docs
description: Přečtěte si, jak operátor cloudu může přidat sady škálování virtuálních počítačů do webu Azure Stack hub Marketplace.
services: azure-stack
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 10/04/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 906c004754bb5bd83283a2be738e812d96ee34bf
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75803785"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack-hub"></a>Zpřístupnění sady škálování virtuálních počítačů v centru Azure Stack

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack Development Kit*
  
Služby Virtual Machine Scale Sets jsou výpočetním prostředkem Azure Stack hub. Můžete je použít k nasazení a správě sady identických virtuálních počítačů (VM). U všech virtuálních počítačů nakonfigurovaných stejným způsobem sady škálování nevyžadují předběžné zřizování virtuálních počítačů. Sestavování rozsáhlých služeb, které cílí na Big COMPUTE, velké objemy dat a kontejnerové úlohy, je snazší.

Tento článek vás provede procesem vytváření sad škálování v tržišti Azure Stack hub. Po dokončení tohoto postupu můžou uživatelé přidat ke svým předplatným sadu škálování virtuálních počítačů.

Sada škálování virtuálních počítačů na rozbočovači Azure Stack je podobná službě Virtual Machine Scale Sets v Azure. Další informace najdete v následujících videích:

* [Mark Russinovich hovoří o škálovacích sadách Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Virtual Machine Scale Sets s Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

V Azure Stackovém centru nepodporuje Virtual Machine Scale Sets automatické škálování. Do sady škálování můžete přidat další instance pomocí Správce prostředků šablon, rozhraní příkazového řádku nebo PowerShellu.

## <a name="prerequisites"></a>Požadavky

* **Tržiště centra Azure Stack:** Pokud chcete povolit dostupnost položek v tržišti centra Azure Stack, zaregistrujte Azure Stack centrum s globálním Azure. Postupujte podle pokynů v části [registrace centra Azure Stack s Azure](azure-stack-registration.md).
* **Bitová kopie operačního systému:** Než začnete vytvářet škálu virtuálních počítačů, musíte si stáhnout image virtuálních počítačů pro použití v sadě škálování z [webu centra Azure Stack](azure-stack-download-azure-marketplace-item.md). Předtím, než může uživatel vytvořit novou sadu škálování, již musí být image přítomné.

## <a name="use-the-azure-stack-hub-portal"></a>Použití portálu centra Azure Stack

>[!IMPORTANT]  
> Informace v této části se vztahují na použití centra Azure Stack verze 1808 nebo novější. Pokud je vaše verze 1807 nebo starší, přečtěte si téma [Přidání sady škálování virtuálních počítačů (před 1808)](#add-the-virtual-machine-scale-set-prior-to-version-1808).

1. Přihlaste se k portálu centra Azure Stack. Pak klikněte na **všechny služby**, pak na **Virtual Machine Scale Sets**a potom v části **COMPUTE**vyberte **Virtual Machine Scale Sets**.
   ![vybrat Virtual Machine Scale Sets](media/azure-stack-compute-add-scalesets/all-services.png)

2. Vyberte ***vytvořit Virtual Machine Scale Sets***.
   ![Vytvoření škálovací sady virtuálních počítačů](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. Vyplňte prázdná pole a vyberte z rozevíracích seznamů pro **image disku s operačním systémem**, **předplatné**a **velikost instance**. Vyberte **Ano** , pokud chcete **používat spravované disky**. Poté klikněte na možnost **Vytvořit**.
    ![nakonfigurovat a vytvořit Virtual Machine Scale Sets](media/azure-stack-compute-add-scalesets/create.png)

4. Pokud chcete zobrazit novou sadu škálování virtuálních počítačů, přejděte na **všechny prostředky**, vyhledejte název sady škálování virtuálního počítače a potom vyberte jeho název ve vyhledávání.
   ![zobrazit sadu škálování virtuálního počítače](media/azure-stack-compute-add-scalesets/search.png)

## <a name="add-the-virtual-machine-scale-set-prior-to-version-1808"></a>Přidat sadu škálování virtuálního počítače (před verzí 1808)

>[!IMPORTANT]  
> Informace v této části se vztahují na použití verze centra Azure Stack před 1808. Pokud používáte verzi 1808 nebo novější, přečtěte si téma [použití portálu Azure Stack hub](#use-the-azure-stack-hub-portal).

1. Otevřete Marketplace centra Azure Stack a připojte se k Azure. Vyberte **Správa Marketplace**a pak klikněte na **+ Přidat z Azure**.

    ![Správa Marketplace centra Azure Stack](media/azure-stack-compute-add-scalesets/image01.png)

2. Přidejte a stáhněte položku Marketplace sady škálování virtuálního počítače.

    ![Položka Marketplace sady škálování virtuálního počítače](media/azure-stack-compute-add-scalesets/image02.png)

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

   Předtím, než může horizontální navýšení kapacity použít novou image, je nutné si stáhnout tuto novou image:  

   * Když je image na webu centra Azure Stack novější verze než image v sadě škálování, Stáhněte novou image, která nahradí starší image. Po nahrazení obrázku může uživatel pokračovat v horizontálním navýšení kapacity.

   * Když je verze image na webu centra Azure Stack shodná s imagí v sadě škálování, odstraňte image, která se používá v sadě škálování, a pak stáhněte novou image. V době mezi odebráním původní image a stažením nového obrázku nemůžete škálovat.

   Tento proces je nutný k resyndikátování imagí, které využívají formát zhuštěného souboru, představený s verzí 1803.

2. Šablona nasazení sady škálování virtuálních počítačů **neurčuje** pro **verzi** hodnotu nejnovější a místo toho Určuje číslo verze:  

    Pokud si stáhnete image s novější verzí (která mění dostupnou verzi), sada škálování se nedá škálovat nahoru. Jedná se o návrh, protože je k dispozici verze image určená v šabloně sady škálování.  

Další informace najdete v tématu [disky a image operačního systému](../user/azure-stack-compute-overview.md#operating-system-disks-and-images).  

## <a name="scale-a-virtual-machine-scale-set"></a>Škálování sady škálování virtuálních počítačů

Velikost sady škálování virtuálního počítače můžete škálovat tak, aby byla větší nebo menší.

1. Na portálu vyberte sadu škálování a pak vyberte **škálování**.

2. Pomocí panelu snímků nastavte novou úroveň škálování pro tuto sadu škálování virtuálního počítače a pak klikněte na **Uložit**.

     ![Škálovat sadu virtuálních počítačů](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="remove-a-virtual-machine-scale-set"></a>Odebrat sadu škálování virtuálního počítače

Pokud chcete odebrat položku galerie škálování sady virtuálních počítačů, spusťte následující příkaz prostředí PowerShell:

```powershell  
Remove-AzsGalleryItem
```

> [!NOTE]
> Položka Galerie nemusí být okamžitě odebrána. Možná budete muset portál aktualizovat několikrát, než se položka zobrazí jako odebraná z webu centra Azure Stack.

## <a name="next-steps"></a>Další kroky

* [Stažení položek z webu Marketplace z Azure do centra Azure Stack](azure-stack-download-azure-marketplace-item.md)
