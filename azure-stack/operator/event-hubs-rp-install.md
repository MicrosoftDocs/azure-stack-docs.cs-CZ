---
title: Postup instalace Event Hubs v centru Azure Stack
description: Naučte se instalovat poskytovatele prostředků Event Hubs v centru Azure Stack.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: e07d311c8edbe140834a020af489ae49d8380d86
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "80423968"
---
# <a name="how-to-install-event-hubs-on-azure-stack-hub"></a>Postup instalace Event Hubs v centru Azure Stack

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

V tomto článku se dozvíte, jak stáhnout a nainstalovat poskytovatele prostředků Event Hubs a zpřístupnit ho zákazníkům pro předplatné.

## <a name="download-packages"></a>Stáhnout balíčky

Než budete moct nainstalovat Event Hubs do centra Azure Stack, musíte stáhnout poskytovatele prostředků a jeho závislé balíčky. V závislosti na vaší situaci nebo požadavcích máte dvě možnosti:

- Stažení Event Hubs v rámci připojeného scénáře.
- Stažení Event Hubs v rámci odpojeného nebo částečně připojeného scénáře.

Pokud nejste obeznámeni s funkcí **správy Marketplace** na portálu správce centra Azure Stack, věnujte si čas [na stažení položek Marketplace z Azure a publikování do centra Azure Stack](azure-stack-download-azure-marketplace-item.md). Tento článek vás provede procesem stahování položek z Azure do tržiště centra Azure Stack. Zahrnuje jak připojené, tak odpojené scénáře. 

### <a name="download-event-hubs---connected-scenario"></a>Stáhnout scénář s připojením Event Hubs

> [!NOTE]
> Proces stahování může trvat 30 minut až 2 hodiny v závislosti na latenci sítě a existujících balíčcích vaší instance centra Azure Stack. 

Pokud vaše Azure Stack rozbočovač má připojení k Internetu, postupujte podle těchto pokynů:

1. Přihlaste se k portálu pro správu centra Azure Stack.
2. Na levé straně vyberte **Správa Marketplace** .
3. Vyberte **Poskytovatelé prostředků**.
4. Vyberte **+ Přidat z Azure**.
5. Vyhledejte "Event Hubs" pomocí panelu hledání.
6. Ve výsledcích hledání vyberte řádek Event Hubs. 
7. Na stránce pro stažení Event Hubs vyberte verzi Event Hubs, kterou chcete nainstalovat, a potom v dolní části stránky vyberte **Stáhnout** . 
   [![Balíčky správy Marketplace](media/event-hubs-rp-install/1-marketplace-management-download.png)](media/event-hubs-rp-install/1-marketplace-management-download.png#lightbox)

Všimněte si, že další balíčky softwaru se stáhnou spolu s Event Hubs, včetně těchto:

- POUZE interní doplněk Microsoft Azure Stack hub RP Windows serveru
- Konfigurace požadovaného stavu prostředí PowerShell

Po dokončení procesu stahování přejděte na [oddíl instalovat předpoklady](#install-prerequisites).

### <a name="download-event-hubs---disconnected-or-partially-connected-scenario"></a>Stáhnout scénář Event Hubs – odpojeno nebo částečně připojeno

Nejdřív si balíčky stáhnete do svého místního počítače a pak je naimportujete do své instance centra Azure Stack.

1. Pokud jste to ještě neudělali, postupujte podle pokynů v části [stažení položek Marketplace – odpojené nebo částečně propojené scénáře](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario). Tady si stáhnete a spustíte nástroj pro syndikaci na webu Marketplace, který vám umožní stáhnout Event Hubs balíčky.
2. Po otevření okna Azure Marketplace položky nástroje syndikace Najděte a vyberte "Event Hubs" a Stáhněte si požadované balíčky do místního počítače.
3. Po dokončení stahování naimportujete balíčky do své instance centra Azure Stack a publikujete na Marketplace. 

## <a name="installation"></a>Instalace 

1. Pokud jste to ještě neudělali, přihlaste se na portál pro správu centra Azure Stack.
2. Na levé straně vyberte **Správa Marketplace** a pak vyberte **poskytovatelé prostředků**.
3. Po stažení Event Hubs a dalšího požadovaného softwaru by měla **Správa Marketplace** zobrazovat balíčky "Event Hubs" se stavem "Nenainstalováno". Mohou existovat další balíčky, které zobrazují stav "staženo". Vyberte řádek Event Hubs, který chcete nainstalovat.
   [![Stažené balíčky správy Marketplace](media/event-hubs-rp-install/2-marketplace-management-downloaded.png)](media/event-hubs-rp-install/2-marketplace-management-downloaded.png#lightbox)
 
4. Na stránce Event Hubs instalace balíčku by se měl zobrazit modrý banner v horní části. Vyberte banner a spusťte instalaci Event Hubs.
   [![Centra událostí správy Marketplace – spustit instalaci](media/event-hubs-rp-install/3-marketplace-management-install-ready.png)](media/event-hubs-rp-install/3-marketplace-management-install-ready.png#lightbox)

### <a name="install-prerequisites"></a>Požadavky na instalaci

1. Dál jste přenesli na stránku instalace. Kliknutím na **instalovat požadavky** zahajte proces instalace.
   ![Centra událostí správy Marketplace – požadavky](media/event-hubs-rp-install/4-marketplace-management-install-prereqs-start.png)
 
2. Počkejte, dokud nebude instalace požadovaných součástí úspěšná. Před pokračováním na další krok by se měla zobrazit zelená značka zaškrtnutí vedle políčka **nainstalovat požadavky** .

   ![Centra událostí správy Marketplace – požadavky](media/event-hubs-rp-install/5-marketplace-management-install-prereqs-succeeded.png)

### <a name="prepare-secrets"></a>Příprava tajných kódů 

1. V části **2. Připravte krok tajné klíče** , vyberte **Přidat certifikát**a zobrazí se panel **Přidat certifikát** .
   ![Centra událostí správy Marketplace – Příprava tajných klíčů](media/event-hubs-rp-install/6-marketplace-management-install-prepare-secrets.png)

2. V části **Přidat certifikát**klikněte na tlačítko Procházet, a to těsně napravo od pole název souboru certifikátu.
3. Vyberte soubor certifikátu. pfx, který jste si vybrali při dokončování požadavků. Další informace najdete v tématu [předpoklady pro instalaci](event-hubs-rp-prerequisites.md). 

4. Zadejte heslo, které jste zadali, abyste vytvořili zabezpečený řetězec pro Event Hubs certifikát SSL. Pak vyberte **Přidat**.
   ![Centra událostí správy Marketplace – přidat certifikát](media/event-hubs-rp-install/7-marketplace-management-install-prepare-secrets-add-cert.png)

### <a name="install-resource-provider"></a>Nainstalovat poskytovatele prostředků

1. Po úspěšném dokončení instalace certifikátu se zobrazí zelená značka zaškrtnutí vedle **Příprava tajných** kódů, než budete pokračovat k dalšímu kroku. Nyní vyberte tlačítko **instalovat** vedle **3 instalace poskytovatele prostředků**.
   ![Centra událostí správy Marketplace – spustit instalaci](media/event-hubs-rp-install/8-marketplace-management-install-start.png)
 
2. V dalším kroku se zobrazí následující stránka, která indikuje, že se instaluje Event Hubs poskytovatel prostředků.
   [![Centra událostí správy Marketplace – instalace](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png)](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png#lightbox)
 
3. Počkejte, až se dokončí oznámení instalace. Tento proces obvykle trvá jednu nebo více hodin v závislosti na typu centra Azure Stack. 
   [![Centra událostí správy Marketplace – instalace dokončena](media/event-hubs-rp-install/10-marketplace-management-install-complete.png)](media/event-hubs-rp-install/10-marketplace-management-install-complete.png#lightbox)

4. Vraťte se na stránku **správy Marketplace**, **Ověřte, že** instalace Event Hubs proběhla úspěšně. Stav Event Hubs by měl obsahovat "nainstalované".
   ![Dostupná centra událostí správy Marketplace](media/event-hubs-rp-install/11-marketplace-management-rps-installed.png)

## <a name="register-event-hubs"></a>Registrovat Event Hubs

Teď je potřeba zaregistrovat poskytovatele prostředků Event Hubs. Registrace umožňuje používat stránku správy Event Hubs ke správě služby.

1. Na portálu pro správu vyberte v levém horním rohu **všechny služby** .
2. Vyberte **Předplatná**. Zobrazí se seznam předplatných. 
   > [!NOTE]
   > Ujistěte se, že nechcete vybrat **odběry uživatelů** .
3. Na stránce **předplatná** vyberte **výchozí předplatné poskytovatele**.
4. Na levé straně **výchozího předplatného poskytovatele** vyberte **poskytovatelé prostředků**.
5. V poli **filtrovat podle názvu** v horní části vyhledejte řetězec "EventHub".
6. Podívejte se na sloupec **stav** v řádcích poskytovatele prostředků Microsoft. Eventhub a Microsoft. Eventhub. admin.
7. Pokud má některý z nich stav "nezaregistrován", vyberte jednotlivé poskytovatele a pak vyberte **Registrovat**. 
   ![Neregistrovaní poskytovatelé prostředků](media/event-hubs-rp-install/12-default-subscription-rps-unregistered.png)
8. Po několika sekundách vyberte **aktualizovat**. Nyní byste měli vidět poskytovatele prostředků se stavem Registrováno. 
9. Nyní byste měli vidět Microsoft. EventHub a Microsoft. EventHub. admin s stavem "registrováno".
   ![Registrovaní poskytovatelé prostředků](media/event-hubs-rp-install/13-default-subscription-rps-registered.png)

10. Vraťte se na stránku **všechny služby** .
11. Vyhledejte "Event Hubs". Nyní byste měli vidět "Event Hubs", což je váš vstupní bod pro Event Hubs stránku pro správu. 
   ![Dostupné služby – centra událostí](media/event-hubs-rp-install/14-all-service-event-hubs.png)
 
## <a name="next-steps"></a>Další kroky

Předtím, než mohou uživatelé nasadit prostředky Event Hubs, je nutné vytvořit jeden nebo více plánů, nabídek a odběrů. 

- Pokud službu nabízíte poprvé, začněte s kurzem [služeb nabídek pro uživatele](tutorial-offer-services.md) . Pak pokračujte dalším kurzem, [otestujte nabídku služeb](tutorial-test-offer.md).
- Jakmile se seznámíte s konceptem nabídky služby, vytvořte nabídku a plán, který zahrnuje poskytovatele prostředků Event Hubs. Pak vytvořte předplatné pro uživatele nebo poskytněte jim informace nabídky, aby si mohli vytvořit své vlastní. Další informace najdete v článku [Přehled služeb, plánů, nabídek a předplatných](service-plan-offer-subscription-overview.md).

Chcete-li vyhledat aktualizace a [aktualizovat Event Hubs v centru Azure Stack](resource-provider-apply-updates.md).

Pokud potřebujete odebrat poskytovatele prostředků, přečtěte si téma [odebrání poskytovatele prostředků Event Hubs](event-hubs-rp-remove.md) .

Další informace o činnostech uživatelů najdete v části [Event Hubs v článku Přehled centra Azure Stack](../user/event-hubs-overview.md) v dokumentu uživatele.