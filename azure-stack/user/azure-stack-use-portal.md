---
title: Použití uživatelského portálu Azure Stack hub
description: Naučte se, jak získat přístup k portálu User Portal a používat ho v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: efemmano
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: c5f3a357295647f557e0b4ef9819aa1781779434
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77705300"
---
# <a name="use-the-azure-stack-hub-user-portal"></a>Použití uživatelského portálu Azure Stack hub

Pomocí portálu Azure Stack hub se přihlaste k odběru veřejných nabídek a použijte služby, které tyto nabídky poskytují. Pokud jste použili globální Azure Portal, již jste obeznámeni s tím, jak lokalita funguje.

## <a name="access-the-portal"></a>Přístup k portálu

Váš operátor centra Azure Stack (ve vaší organizaci buď poskytovatel služeb nebo správce), vám umožní znát správnou adresu URL pro přístup k portálu.

- V případě integrovaného systému se adresa URL liší v závislosti na oblasti vašeho operátoru a na externím názvu domény a bude ve formátu https://portal.&lt;&gt;*oblasti*&lt;*plně kvalifikovaný název domény*&gt;.
- Pokud používáte Azure Stack Development Kit (ASDK), adresa portálu se https://portal.local.azurestack.external.
- Výchozí časové pásmo pro všechna nasazení centra Azure Stack je nastavené na koordinovaný světový čas (UTC). Při instalaci Azure Stackového centra můžete vybrat časové pásmo, ale při instalaci se automaticky obnoví na standard UTC.

## <a name="customize-the-dashboard"></a>Přizpůsobení řídicího panelu

Řídicí panel obsahuje výchozí sadu dlaždic. Vyberte **Upravit řídicí panel** pro úpravu výchozího řídicího panelu nebo vyberte **nový řídicí panel** a vytvořte vlastní řídicí panel. Řídicí panel můžete snadno přizpůsobit přidáním nebo odebráním dlaždic. Pokud například chcete přidat dlaždici COMPUTE, vyberte **+ vytvořit prostředek**. Klikněte pravým tlačítkem na **COMPUTE**a potom vyberte **Připnout na řídicí panel**.

![Snímek obrazovky s uživatelským portálem Azure Stack hub](media/azure-stack-use-portal/userportal.png)

Obnovení původního nastavení řídicího panelu:
1.  Vyberte **Upravit řídicí panel**. 
2.  Klikněte pravým tlačítkem a vyberte **obnovit do výchozího stavu**.

## <a name="create-subscription-and-browse-available-resources"></a>Vytvoření předplatného a procházení dostupných prostředků

Pokud předplatné ještě nemáte, musíte se přihlásit k odběru nabídky. Potom můžete procházet dostupné prostředky. K procházení a vytváření prostředků použijte některý z následujících přístupů:

- Na řídicím panelu vyberte dlaždici **Marketplace** .
- Na dlaždici **všechny prostředky** vyberte **vytvořit prostředky**.
- V levém navigačním podokně vyberte **+ vytvořit prostředek**.

## <a name="learn-how-to-use-available-services"></a>Naučte se používat dostupné služby.

Pokud potřebujete Rady, jak používat dostupné služby, můžou vám být k dispozici různé možnosti.

- Vaše organizace nebo poskytovatel služeb může poskytovat vlastní dokumentaci, což je obvykle případ, že nabízí přizpůsobené služby nebo aplikace.
- Aplikace třetích stran mají svou vlastní dokumentaci.
- Pro služby konzistentní s Azure důrazně doporučujeme, abyste si nejdřív přečtěte dokumentaci centra Azure Stack. Chcete-li získat přístup k dokumentaci pro uživatele centra Azure Stack, vyberte ikonu pomoci ( **?** ) a pak vyberte možnost **help + podpora**.

    ![Možnost pomoci a podpory v uživatelském rozhraní](media/azure-stack-use-portal/HelpAndSupport.png)

    Konkrétně doporučujeme, abyste provedli následující články, které vám pomohou začít:

    - [Klíčové důležité pokyny: použití služeb nebo sestavování aplikací pro centrum Azure Stack](azure-stack-considerations.md).
    - V části věnované **používání služeb** v dokumentaci jsou k dispozici články týkající se každé služby. Stránka s informacemi popisuje rozdíly mezi službou nabízenou v Azure a stejnou službou nabízenou v centru Azure Stack. Příklad najdete v tématu [požadavky na virtuální počítače](azure-stack-vm-considerations.md). V oddílu **Use Services** můžou být jiné informace, které jsou jedinečné pro Azure Stack hub.

      Dokumentaci k Azure můžete použít jako obecné referenční informace ke službě, ale musíte si být vědomi těchto rozdílů. Projistěte, aby se v dokumentaci na dlaždici úvodních **kurzů** v dokumentaci k Azure seznámila.

## <a name="get-support"></a>Získat podporu

Pokud potřebujete podporu, požádejte o pomoc svoji organizaci nebo poskytovatele služeb.

Pokud používáte Azure Stack Development Kit (ASDK), je [Fórum centra Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) jediným zdrojem podpory.

## <a name="next-steps"></a>Další kroky

[Klíčové doporučení: použití služeb nebo sestavování aplikací pro centrum Azure Stack](azure-stack-considerations.md)
