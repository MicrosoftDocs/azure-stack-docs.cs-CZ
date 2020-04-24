---
title: Vytvoření clusteru Event Hubs pomocí portálu
description: Naučte se vytvořit cluster Event Hubs pomocí uživatelského portálu Azure Stack hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: quickstart
ms.date: 01/22/2020
ms.reviewer: bryanla
ms.lastreviewed: 01/22/2020
ms.openlocfilehash: 6af9ef6f562c9b31d7310b7f35d7b6b0533472ea
ms.sourcegitcommit: 4138a2a15f78e7db38b3a29acc963a71937146fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "80479255"
---
# <a name="quickstart-create-an-event-hubs-cluster-using-the-azure-stack-hub-portal"></a>Rychlý Start: Vytvoření clusteru Event Hubs pomocí portálu centra Azure Stack

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

V tomto rychlém startu se dozvíte, jak vytvořit cluster Event Hubs pomocí uživatelského portálu Azure Stack hub. 

Clustery Event Hubs nabízejí nasazení s jedním nájemcem pro nejnáročnější potřeby streamování. Cluster Event Hubs může příchozí miliony událostí za sekundu se zaručenou kapacitou a latencí druhé sekundy. Event Hubs clustery obsahují všechny oblíbené funkce a nabízejí paritu funkcemi, které nabízí edice Azure Event Hubs.

## <a name="prerequisites"></a>Požadavky

Než budete moct dokončit tento rychlý Start, musíte zajistit, aby byla ve vašem předplatném dostupná služba Event Hubs. Pokud ne, ve spolupráci s vaším správcem [nainstalujte Event Hubs do poskytovatele prostředků služby Azure Stack hub](../operator/event-hubs-rp-overview.md). Postup instalace také pokryje vytvoření nabídky, která zahrnuje službu Event Hubs. 

Jakmile je nabídka k dispozici, může správce vytvořit nebo aktualizovat vaše předplatné, aby zahrnovalo Event Hubs. Alternativně se můžete [přihlásit k odběru nové nabídky a vytvořit vlastní předplatné](azure-stack-subscribe-services.md).

## <a name="overview"></a>Přehled

Clustery Event Hubs jsou vytvářeny zadáním kapacitních jednotek (kapacitní jednotky). CU je předem přidělené množství prostředků procesoru, úložiště a paměti. Clustery Event Hubs se účtují podle CPU a hr. Při výběru počtu kapacitní jednotky (velikost clusteru) během vytváření clusteru se zobrazí počet jader (CPU) používaných clusterem. Podrobnější informace o využití prostředků clusteru najdete v tématu [postup plánování kapacity pro Event Hubs v centru Azure Stack](../operator/event-hubs-rp-capacity-planning.md). 

V tomto rychlém startu se dozvíte, jak pomocí portálu Azure Stack User Portal:
- Vytvořte cluster Event Hubs 1 CU.
- Vytvořte v clusteru obor názvů.
- Vytvořte centrum událostí v oboru názvů.
- Odstranění clusteru Event Hubs

## <a name="create-an-event-hubs-cluster"></a>Vytvoření clusteru Event Hubs

Cluster Event Hubs poskytuje jedinečný obor kontejneru, ve kterém můžete vytvořit jeden nebo více oborů názvů. Provedením následujících kroků vytvořte cluster Event Hubs: 

1. Přihlaste se k portálu pro uživatele centra Azure Stack.
2. V levém navigačním podokně vyberte **všechny služby** , do vyhledávacího panelu zadejte "Event Hubs clustery" a vyberte položku **Event Hubs clustery** ze seznamu výsledků.
3. Jednou na stránce **Event Hubs clustery** vyberte v horní nabídce **+ Přidat** . Otevře se panel **vytvořit Event Hubs cluster** na pravé straně.
4. Na kartě **základy** stránky **vytvořit Event Hubs clusteru** :  
   - **Název clusteru**: zadejte název. Systém okamžitě kontroluje, jestli je název dostupný. Pokud je k dispozici, zobrazí se značka zaškrtnutí na pravé straně pole. 
   - **Předplatné**: vyberte předplatné, ve kterém chcete cluster vytvořit. 
   - **Skupina prostředků**: Vytvořte nebo vyberte skupinu prostředků, ve které chcete cluster vytvořit. 
   - Pokračujte výběrem tlačítka **Další: značky >**  v dolní části stránky. Je možné, že budete muset počkat, až systém bude plně zřizovat prostředky. 

   [![Vytvoření základních informací o clusteru centra událostí](media/event-hubs-quickstart-cluster-portal/1-create-cluster-basics.png)](media/event-hubs-quickstart-cluster-portal/1-create-cluster-basics.png#lightbox)

5. Na kartě **značky** : 
   - Volitelně můžete zadat páry název/hodnota pro značky prostředku.  
   - Pokračujte výběrem tlačítka **Další: zkontrolovat + vytvořit >**  . 

   [![Vytvoření značek clusteru centra událostí – značky](media/event-hubs-quickstart-cluster-portal/1-create-cluster-tags.png)](media/event-hubs-quickstart-cluster-portal/1-create-cluster-tags.png#lightbox)

6. Na kartě **Revize + vytvořit** by se v horní části stránky měla zobrazit zpráva "ověření proběhlo úspěšně. Po zkontrolování podrobností vyberte **vytvořit** , až budete připraveni vytvořit cluster. 

   [![Vytvoření clusteru centra událostí – kontrola + vytvoření](media/event-hubs-quickstart-cluster-portal/1-create-cluster-review.png)](media/event-hubs-quickstart-cluster-portal/1-create-cluster-review.png#lightbox)

   >[!NOTE]
   > Dokončení nasazení clusteru Event Hubs může trvat několik minut, obvykle minimálně 45 minut.

7. Během nasazení se zobrazí stránka stav **nasazení probíhá** . Po dokončení nasazení se stránka změní v **nasazení na dokončeno**. Než budete pokračovat k další části, kliknutím na tlačítko **Přejít k prostředku** zobrazíte nový cluster.

   [![Vytvoření clusteru centra událostí – nasazení dokončeno](media/event-hubs-quickstart-cluster-portal/1-deployment-complete.png)](media/event-hubs-quickstart-cluster-portal/1-deployment-complete.png#lightbox)


## <a name="create-a-namespace"></a>Vytvoření oboru názvů

Nyní vytvoříte obor názvů v rámci nového clusteru:

1. Na stránce **Přehled** **Event Hubs clusteru**v horní nabídce vyberte **+ obor názvů** . 

   [![Clustery Event Hubs – výběr clusteru](media/event-hubs-quickstart-cluster-portal/2-view-cluster.png)](media/event-hubs-quickstart-cluster-portal/2-view-cluster.png#lightbox)

2. Na panelu **cluster vytvořit** na panelu clusteru:

   - **Název**: zadejte název oboru názvů. Systém okamžitě kontroluje, jestli je název dostupný. Pokud je k dispozici, zobrazí se značka zaškrtnutí na pravé straně pole. 
   - **Seznam vlastností a hodnot**: obor názvů dědí následující vlastnosti: 
     - ID předplatného 
     - Skupina prostředků 
     - Umístění 
     - Název clusteru 

   - Vyberte **vytvořit** a vytvořte obor názvů:

   [![Clustery Event Hubs – vytvoření oboru názvů](media/event-hubs-quickstart-cluster-portal/2-view-cluster-create-namespace.png)](media/event-hubs-quickstart-cluster-portal/2-view-cluster-create-namespace.png#lightbox)

3. Poznamenejte si nový obor názvů v části **obory názvů clusteru**. Než budete pokračovat k další části, vyberte odkaz pro nový obor názvů. 

   [![Event Hubs clustery – zobrazení oboru názvů](media/event-hubs-quickstart-cluster-portal/2-view-cluster-with-namespace.png)](media/event-hubs-quickstart-cluster-portal/2-view-cluster-with-namespace.png#lightbox)

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

1. Na stránce **Přehled** **oboru názvů Event Hubs**v horní nabídce vyberte **+ centrum událostí** .  

   [![Event Hubs – Přehled oboru názvů](media/event-hubs-quickstart-cluster-portal/3-event-hubs-namespace-overview.png)](media/event-hubs-quickstart-cluster-portal/3-event-hubs-namespace-overview.png#lightbox)

2. Na panelu **vytvořit centrum událostí** :
   - **Název**: zadejte název centra událostí. Název může obsahovat jenom písmena, číslice, tečky, spojovníky a podtržítka. Název musí začínat a končit písmenem nebo číslicí. Systém okamžitě kontroluje, jestli je název dostupný. Pokud je k dispozici, zobrazí se značka zaškrtnutí na pravé straně pole.
   - Vyberte **vytvořit** a vytvořte centrum událostí.

   [![Event Hubs – Přehled oboru názvů vytvoření centra událostí](media/event-hubs-quickstart-cluster-portal/3-event-hubs-namespace-overview-create-event-hub.png)](media/event-hubs-quickstart-cluster-portal/3-event-hubs-namespace-overview-create-event-hub.png#lightbox)

## <a name="delete-an-event-hubs-cluster"></a>Odstranění clusteru Event Hubs

Postup odstranění clusteru:

1. Znovu vyberte **všechny služby** z levého navigačního podokna. Do panelu vyhledávání zadejte "Event Hubs clustery" a vyberte položku **Event Hubs clustery** ze seznamu výsledků.
2. Na stránce **clustery centra událostí** vyhledejte a vyberte cluster, který jste vytvořili dříve.

   [![Clustery Event Hubs – odstranění clusteru](media/event-hubs-quickstart-cluster-portal/4-delete-cluster-clusters.png)](media/event-hubs-quickstart-cluster-portal/4-delete-cluster-clusters.png#lightbox)

3. Na stránce **přehled** **Event Hubs clusteru**:
   - V horní nabídce vyberte **Odstranit** .  
   - Na pravé straně se otevře panel **Odstranit cluster** s potvrzovací zprávou o odstranění. 
   - Zadejte název clusteru a vyberte **Odstranit** , aby se cluster odstranil. 

   [![Clustery Event Hubs – odstranění clusteru](media/event-hubs-quickstart-cluster-portal/4-delete-cluster-delete.png)](media/event-hubs-quickstart-cluster-portal/4-delete-cluster-delete.png#lightbox)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit cluster Event Hubs pomocí portálu. Teď pokračujte rychlými starty pro posílání a přijímání událostí počínaje prvními:  

> [!div class="nextstepaction"]
> [Odeslat události do nebo přijímat události z Event Hubs](/azure/event-hubs/get-started-dotnet-standard-send-v2)
