---
title: Použití Azure Stack User Portalu | Microsoft Docs
description: Naučte se, jak přistupovat k portálu User Portal a používat ho v Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: efemmano
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 392d850e5d74a1bd069653aae3b9def6438c5288
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909579"
---
# <a name="use-the-azure-stack-user-portal"></a>Použití portálu Azure Stack User Portal

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Pomocí portálu Azure Stack můžete odebírat veřejné nabídky a využívat služby, které tyto nabídky poskytují. Pokud jste použili globální Azure Portal, již jste obeznámeni s tím, jak lokalita funguje.

## <a name="access-the-portal"></a>Přístup k portálu

Váš operátor Azure Stack (buď poskytovatele služeb nebo správce ve vaší organizaci), vám umožní znát správnou adresu URL pro přístup k portálu.

- V případě integrovaného systému se adresa URL liší v závislosti na oblasti vašeho operátoru a na externím názvu domény a bude ve formátu https://portal.&lt. *oblast.* &gt;&lt; *Plně kvalifikovaný název domény* &gt;.
- Pokud používáte Azure Stack Development Kit (ASDK), adresa portálu je https://portal.local.azurestack.external.
- Výchozí časové pásmo pro všechna Azure Stack nasazení je nastaveno na koordinovaný světový čas (UTC). Při instalaci Azure Stack můžete vybrat časové pásmo, ale při instalaci se automaticky obnoví standard UTC.

## <a name="customize-the-dashboard"></a>Přizpůsobení řídicího panelu

Řídicí panel obsahuje výchozí sadu dlaždic. Vyberte **Upravit řídicí panel** pro úpravu výchozího řídicího panelu nebo vyberte **nový řídicí panel** a vytvořte vlastní řídicí panel. Řídicí panel můžete snadno přizpůsobit přidáním nebo odebráním dlaždic. Pokud například chcete přidat dlaždici COMPUTE, vyberte **+ vytvořit prostředek**. Klikněte pravým tlačítkem na **COMPUTE**a potom vyberte **Připnout na řídicí panel**.

![Snímek obrazovky Azure Stack User Portal](media/azure-stack-use-portal/userportal.png)

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
- Pro služby konzistentní s Azure důrazně doporučujeme, abyste si nejdřív přečtěte dokumentaci Azure Stack. Chcete-li získat přístup k Azure Stack dokumentaci pro uživatele, vyberte ikonu pomoci ( **?** ) a pak vyberte možnost **help + podpora**.

    ![Možnost pomoci a podpory v uživatelském rozhraní](media/azure-stack-use-portal/HelpAndSupport.png)

    Konkrétně doporučujeme, abyste provedli následující články, které vám pomohou začít:

    - [Klíčové důležité pokyny: Použití služeb nebo sestavování aplikací](azure-stack-considerations.md)pro Azure Stack.
    - V části věnované **používání služeb** v dokumentaci jsou k dispozici články týkající se každé služby. Stránka s informacemi popisuje rozdíly mezi službou nabízenou v Azure a stejnou službou nabízenou v Azure Stack. Příklad najdete v tématu [požadavky na virtuální počítače](azure-stack-vm-considerations.md). V oddílu **Use Services** můžou být jiné informace, které jsou jedinečné pro Azure Stack.

      Dokumentaci k Azure můžete použít jako obecné referenční informace ke službě, ale musíte si být vědomi těchto rozdílů. Projistěte, aby se v dokumentaci na dlaždici úvodních **kurzů** v dokumentaci k Azure seznámila.

## <a name="get-support"></a>Získat podporu

Pokud potřebujete podporu, požádejte o pomoc svoji organizaci nebo poskytovatele služeb.

Pokud používáte Azure Stack Development Kit (ASDK), je [Azure Stack Fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) jediným zdrojem podpory.

## <a name="next-steps"></a>Další kroky

[Klíčové důležité pokyny: Použití služeb nebo sestavování aplikací pro Azure Stack](azure-stack-considerations.md)
