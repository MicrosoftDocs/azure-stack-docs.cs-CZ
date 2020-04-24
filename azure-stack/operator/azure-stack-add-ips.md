---
title: Přidat veřejné IP adresy do centra Azure Stack
description: Naučte se přidávat veřejné IP adresy do centra Azure Stack.
author: justinha
ms.topic: article
ms.date: 04/20/2020
ms.author: justinha
ms.reviewer: scottnap
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: efa240fbec361657362b936594ccee4c79f70afe
ms.sourcegitcommit: a3ae6dd8670f8fb24224880df7eee256ebbcc4ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772478"
---
# <a name="add-public-ip-addresses"></a>Přidat veřejné IP adresy

V tomto článku odkazujeme na externí adresy jako veřejné IP adresy. V kontextu centra Azure Stack je veřejná IP adresa IP adresou, která je přístupná mimo centrum Azure Stack. Jestli je tato externí síť veřejná internetovou směrovatelný nebo je v intranetu a používá pro účely tohoto článku privátní adresní prostor, jsou tyto kroky stejné.

> [!IMPORTANT]
> Kroky v tomto článku se vztahují jenom na systémy, které se nasadily pomocí sady partner Toolkit verze 1809 nebo novější. Systémy nasazené před verzí 1809 vyžadují, aby se aktualizovaly seznamy řízení přístupu (ACL) přepínače pro nejvyšší úrovni, aby bylo možné nový rozsah veřejných VIP adres aktualizovat. Pokud používáte starší konfigurace přepínačů, spolupracujte se svým výrobcem OEM a buď přidejte příslušné seznamy ACL pro povolení pro nový fond IP adres, nebo překonfigurujte přepínač pomocí nejnovější sady partner Toolkit, abyste zabránili zablokování nových veřejných IP adres.

## <a name="add-a-public-ip-address-pool"></a>Přidat fond veřejných IP adres
Veřejné IP adresy můžete přidat do systému Azure Stack hub kdykoli po počátečním nasazení Azure Stack systému centra. Podívejte se, jak [Zobrazit využití veřejné IP adresy](azure-stack-viewing-public-ip-address-consumption.md) , abyste zjistili, co je aktuální dostupnost a veřejná IP adresa v centru Azure Stack.

Proces přidání nového bloku veřejných IP adres do centra Azure Stack na nejvyšší úrovni vypadá takto:

 ![Přidat tok IP](media/azure-stack-add-ips/flow.svg)

## <a name="obtain-the-address-block-from-your-provider"></a>Získání bloku adres od poskytovatele
První věc, kterou je potřeba udělat, je získání bloku adres, který chcete přidat do centra Azure Stack. V závislosti na tom, kde získáte blok adresy z, zvažte, co je doba realizace, a spravujte ji na základě sazby, na které používáte veřejné IP adresy v centru Azure Stack.

> [!IMPORTANT]
> Azure Stack centrum bude akceptovat libovolný blok adres, který zadáte, pokud se jedná o platný blok adres a nepřekrývá se s existujícím rozsahem adres v centru Azure Stack. Ujistěte se prosím, že jste obdrželi platný blok adres, který je směrovatelný a který se nepřekrývá s externí sítí, ke které je připojený rozbočovač Azure Stack. Když přidáte rozsah do centra Azure Stack, nemůžete ho odebrat.

## <a name="add-the-ip-address-range-to-azure-stack-hub"></a>Přidat rozsah IP adres do centra Azure Stack

1. V prohlížeči přejdete na řídicí panel portálu pro správu. V tomto příkladu budeme používat https://adminportal.local.azurestack.external.
2. Přihlaste se k portálu správce Azure Stackového centra jako operátor cloudu.
3. Na výchozím řídicím panelu najděte seznam Správa oblastí a vyberte oblast, kterou chcete spravovat. V tomto příkladu používáme místní.
4. Vyhledejte dlaždici poskytovatelé prostředků a klikněte na poskytovatele síťových prostředků.
5. Klikněte na dlaždici využití fondů veřejných IP adres.
6. Klikněte na tlačítko Přidat fond IP adres.
7. Zadejte název fondu IP adres. Vámi zvolený název vám pomůže snadno identifikovat fond IP adres. Je dobrým zvykem, aby byl název stejný jako rozsah adres, ale to není vyžadováno.
8. Zadejte blok adres, který chcete přidat do zápisu CIDR. Příklad: 192.168.203.0/24
9. Když zadáte platný rozsah CIDR v poli Rozsah adres (blok CIDR), automaticky se naplní pole Počáteční IP adresa, koncová IP adresa a dostupná IP adresa. Jsou jen pro čtení a automaticky vygenerovány, takže tato pole nemůžete změnit, aniž by bylo nutné měnit hodnotu v poli Rozsah adres.
10. Po kontrole informací v okně a potvrzení, že vše vypadá správně, vyberte **OK** a potvrďte změnu a přidejte rozsah adres do centra Azure Stack.


## <a name="next-steps"></a>Další kroky 
[Zkontrolujte akce uzlu škálování jednotky](azure-stack-node-actions.md).
