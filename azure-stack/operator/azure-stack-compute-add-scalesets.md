---
title: Zpřístupnit Škálovací sady virtuálních počítačů ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak můžete přidat Škálovací sady virtuálních počítačů na Azure Marketplace zásobníku operátor cloudu
services: azure-stack
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 06/04/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 98aae8378e4bd516181cae95b153e6ee445ce4bf
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692005"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Zpřístupnit škálovací sady virtuálních počítačů ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*
  
Škálovací sady virtuálních počítačů jsou výpočetní prostředek Azure Stack. Můžete využít k nasazení a správě sady identických virtuálních počítačů. Díky tomu, že všechny virtuální počítače mají stejnou konfiguraci, nevyžadují škálovací sady předběžné zřizování virtuálních počítačů. Je snazší zajistit rozsáhlé služby zaměřené na vysoký výpočetní výkon, velké objemy dat a kontejnerizované úlohy.

Tento článek vás provede procesem vytváření škálovací sady dostupné v Tržišti Azure Stack. Po dokončení tohoto postupu můžete přidat uživatele škálovacích sad virtuálních počítačů pro svá předplatná.

Škálovací sady virtuálních počítačů ve službě Azure Stack se podobají škálovací sady virtuálních počítačů v Azure. Další informace najdete v tématu následujícího videa:

* [Mark Russinovich hovoří o škálovacích sadách Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Guy bowerman sadách škálování virtuálních počítačů](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Ve službě Azure Stack nepodporují škálovací sady virtuálních počítačů automatického škálování. Přidání instancí na škálovací sadu pomocí šablony Resource Manageru, rozhraní příkazového řádku nebo Powershellu.

## <a name="prerequisites"></a>Požadavky

* **Na webu Marketplace:** Registrace Azure Stack s globální Azure a umožňuje dostupnost položky na webu Marketplace. Postupujte podle pokynů v [registrace Azure Stack s využitím Azure](azure-stack-registration.md).
* **Image operačního systému:** Před vytvořením škálovací sady virtuálních počítačů, je nutné stáhnout Image virtuálních počítačů pro použití v škálovací sady z [marketplace služby Azure Stack](azure-stack-download-azure-marketplace-item.md). Bitové kopie musí být již existovat předtím, než uživatel může vytvořit novou škálovací sadu.

## <a name="use-the-azure-stack-portal"></a>Použití portálu Azure Stack

>[!IMPORTANT]  
> Informace v této části platí, pokud používáte verzi služby Azure Stack 1808 nebo novější. Pokud je vaše verze 1807 nebo starší, přečtěte si téma [přidat Škálovací sady virtuálních počítačů (před. 1808)](#add-the-virtual-machine-scale-set-prior-to-version-1808).

1. Přihlaste se k portálu Azure Stack. Přejděte ke **všechny služby**, pak **škálovací sady virtuálních počítačů**a potom v části **COMPUTE**vyberte **škálovací sady virtuálních počítačů**.
   ![Vyberte virtuální počítač škálovací sady](media/azure-stack-compute-add-scalesets/all-services.png)

2. Vyberte vytvořit ***škálovací sady virtuálních počítačů***.
   ![Vytvoření škálovací sady virtuálních počítačů](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. Vyplňte pole prázdné, zvolte z rozevírací seznamy pro **image disku operačního systému**, **předplatné**, a **velikost Instance**. Vyberte **Ano** pro **použít spravované disky**. Potom klikněte na **vytvořit**.
    ![Nakonfigurování a vytvoření](media/azure-stack-compute-add-scalesets/create.png)

4. Chcete-li zobrazit vašeho nového virtuálního počítače škálovací sadu, přejděte na **všechny prostředky**, vyhledejte název virtuálního počítače škálovací sady a pak vyberte jeho jméno do hledání.
   ![Zobrazit škálovací sady](media/azure-stack-compute-add-scalesets/search.png)

## <a name="add-the-virtual-machine-scale-set-prior-to-version-1808"></a>Přidat škálovací sadu virtuálních počítačů (před verzí. 1808)

>[!IMPORTANT]  
> Informace v této části platí, pokud používáte verzi služby Azure Stack před. 1808. Pokud používáte verzi 1808 nebo novější, přečtěte si téma [pomocí portálu Azure Stack](#use-the-azure-stack-portal).

1. Otevřete na webu marketplace služby Azure Stack a připojte se k Azure. Vyberte **Marketplace správu**, pak klikněte na tlačítko **+ přidat z Azure**.

    ![Správa webu Marketplace](media/azure-stack-compute-add-scalesets/image01.png)

2. Přidat a stáhnout položky marketplace sady škálování virtuálního počítače.

    ![Škálovací sada virtuálních počítačů](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Aktualizovat Image ve škálovací sadě virtuálních počítačů

Když vytvoříte škálovací sadu virtuálních počítačů, mohou uživatelé aktualizovat Image ve škálovací sadě bez škálovací sady, by bylo nutné znovu vytvořit. Proces pro aktualizaci bitové kopie závisí na následujících scénářů:

1. Nasazení šablony virtuálního počítače škálovací sady Určuje **nejnovější** pro **verze**:  

   Když `version` je nastavena na **nejnovější** v `imageReference` části šablony škálovací sady, operace vertikální škálování ve škálovací sadě použijte nejnovější dostupné verzi image pro instance škálovací sady. Po dokončení horizontálního navýšení můžete odstranit starší instance sady škálování virtuálních počítačů. Hodnoty pro `publisher`, `offer`, a `sku` zůstanou beze změny.

   Následující příklad JSON Určuje `latest`:  

    ```json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   Vertikálně navýšit kapacitu mohli používat novou bitovou kopii, je nutné stáhnout této nové bitové kopie:  

   * Pokud image na webu marketplace je novější verze než image ve škálovací sadě, stáhněte si novou image, který nahrazuje starší obrázek. Po nahrazuje obrázek uživatele můžete přejít k vertikálně navýšit kapacitu.

   * Když se verze image na webu marketplace je stejné jako na obrázku ve škálovací sadě, odstranit image, která se používá ve škálovací sadě a pak si stáhnout novou bitovou kopii. V době mezi odebrání původní bitové kopie a stažení image nového nejde vertikálně navýšit kapacitu.

   Tento postup je nutný k opětovné publikování imagí, které usnadňují použití formátu zhuštěných souborů, představený poprvé ve verzi 1803.

2. Škálovací sady virtuálních počítačů šablony nasazení **neurčuje nejnovější** pro **verze** a místo toho určuje číslo verze:  

    Pokud si stáhnete obrázek z novější verze (která se změní na dostupnou verzi), nelze škálovat škálovací sady. Toto chování je záměrné, jako verze image zadané v šabloně škálovací sady, musí být k dispozici.  

Další informace najdete v tématu [disky operačního systému a image](../user/azure-stack-compute-overview.md#operating-system-disks-and-images).  

## <a name="scale-a-virtual-machine-scale-set"></a>Škálování škálovací sady virtuálních počítačů

Je možné škálovat velikost virtuálního počítače škálovací sady, aby byl větší nebo menší.

1. Na portálu vyberte svou škálovací sadu a pak vyberte **škálování**.

2. Použití panelu snímku nastavíte novou úroveň škálování pro tuto škálovací sadu virtuálních počítačů a klikněte na **Uložit**.

     ![Škálovací sady](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="remove-a-virtual-machine-scale-set"></a>Odstranit škálovací sadu virtuálních počítačů

K odebrání virtuálního počítače změnit velikost položky galerie sady, spusťte následující příkaz Powershellu:

```powershell  
Remove-AzsGalleryItem
```

> [!NOTE]
> Položka galerie nemusí být okamžitě odstraněna. Můžete potřebovat po aktualizaci portálu několikrát předtím, než se položka zobrazuje jako odebrané z webu Marketplace.

## <a name="next-steps"></a>Další postup

* [Stažení položek z marketplace z Azure do služby Azure Stack](azure-stack-download-azure-marketplace-item.md)
