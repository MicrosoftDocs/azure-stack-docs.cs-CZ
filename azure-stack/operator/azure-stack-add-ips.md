---
title: Přidat veřejné IP adresy v Azure Stack | Microsoft Docs
description: Přečtěte si, jak přidat veřejné IP adresy do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 6d99e5b293f86f4bdb62d35fc111054f12d57172
ms.sourcegitcommit: e8f7fe07b32be33ef621915089344caf1fdca3fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118724"
---
# <a name="add-public-ip-addresses"></a>Přidání veřejné IP adresy
*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*  

V tomto článku odkazujeme na externí adresy jako veřejné IP adresy. V souvislosti s Azure Stack je veřejná IP adresa IP adresou, která je přístupná mimo Azure Stack. Jestli je tato externí síť veřejná internetovou směrovatelný nebo je v intranetu a používá pro účely tohoto článku privátní adresní prostor, jsou tyto kroky stejné.

## <a name="add-a-public-ip-address-pool"></a>Přidat fond veřejných IP adres
Veřejné IP adresy můžete do svého Azure Stack systému přidat kdykoli po počátečním nasazení Azure Stack systému. Podívejte se, jak [Zobrazit využití veřejné IP adresy](azure-stack-viewing-public-ip-address-consumption.md) , abyste zjistili, co je aktuální využití a dostupnost veřejné IP adresy v Azure Stack.

Proces přidání nového bloku veřejných IP adres na nejvyšší úrovni vám Azure Stack vypadat takto:

 ![Přidat tok IP](media/azure-stack-add-ips/flow.PNG)

## <a name="obtain-the-address-block-from-your-provider"></a>Získání bloku adres od poskytovatele
První věc, kterou je potřeba udělat, je získání bloku adres, který chcete přidat do Azure Stack. V závislosti na tom, odkud získáte blok adresy z, zvažte, co je doba realizace, a spravujte ji na základě sazby, na které používáte veřejné IP adresy v Azure Stack.

> [!IMPORTANT]
> Azure Stack bude souhlasit s případným blokem adres, který zadáte, protože se jedná o platný blok adres a nepřekrývá se s existujícím rozsahem adres v Azure Stack. Ujistěte se prosím, že jste obdrželi platný blok adres, který je směrovatelný a který se nepřekrývá s externí sítí, ke které je Azure Stack připojená. Jakmile přidáte rozsah do Azure Stack, nemůžete ho odebrat.

## <a name="add-the-ip-address-range-to-azure-stack"></a>Přidat rozsah IP adres do Azure Stack

1. V prohlížeči přejdete na řídicí panel portálu pro správu. V tomto příkladu budeme používat https://adminportal.local.azurestack.external.
2. Přihlaste se k portálu pro správu Azure Stack jako operátor cloudu.
3. Na výchozím řídicím panelu najděte seznam Správa oblastí a vyberte oblast, kterou chcete spravovat. V tomto příkladu používáme místní.
4. Vyhledejte dlaždici poskytovatelé prostředků a klikněte na poskytovatele síťových prostředků.
5. Klikněte na dlaždici využití fondů veřejných IP adres.
6. Klikněte na tlačítko Přidat fond IP adres.
7. Zadejte název fondu IP adres. Vámi zvolený název vám pomůže snadno identifikovat fond IP adres. Je dobrým zvykem, aby byl název stejný jako rozsah adres, ale to není vyžadováno.
8. Zadejte blok adres, který chcete přidat do zápisu CIDR. Příklad: 192.168.203.0/24
9. Když zadáte platný rozsah CIDR v poli Rozsah adres (blok CIDR), automaticky se naplní pole Počáteční IP adresa, koncová IP adresa a dostupná IP adresa. Jsou jen pro čtení a automaticky vygenerovány, takže tato pole nemůžete změnit, aniž by bylo nutné měnit hodnotu v poli Rozsah adres.
10. Po zkontrolování informací v okně a potvrzení, zda je vše správné, kliknutím na tlačítko **OK** potvrďte změnu a přidejte rozsah adres do Azure Stack.


## <a name="next-steps"></a>Další postup 
[Zkontrolujte akce uzlu škálování jednotky](azure-stack-node-actions.md).
