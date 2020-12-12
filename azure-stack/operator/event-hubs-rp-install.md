---
title: Postup instalace Event Hubs v centru Azure Stack
description: Naučte se instalovat poskytovatele prostředků Event Hubs v centru Azure Stack.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/02/2020
ms.reviewer: jfggdl
ms.lastreviewed: 09/02/2020
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 975ab63a6af9f895f01d4607f998bca9fc52cebf
ms.sourcegitcommit: f56a5b287c90b2081ae111385c8b7833931d4059
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97343814"
---
# <a name="how-to-install-event-hubs-on-azure-stack-hub"></a>Postup instalace Event Hubs v centru Azure Stack

V tomto článku se dozvíte, jak stáhnout a nainstalovat poskytovatele prostředků Event Hubs a zpřístupnit ho zákazníkům pro předplatné.

## <a name="download-packages"></a>Stáhnout balíčky

Než budete moct nainstalovat Event Hubs do centra Azure Stack, musíte stáhnout poskytovatele prostředků a jeho závislé balíčky pomocí funkce správy Marketplace. Pokud nejste obeznámeni se správou Marketplace, věnujte čas na [stažení položek Marketplace z Azure a publikování do centra Azure Stack](azure-stack-download-azure-marketplace-item.md). Tato část vás provede procesem stahování položek z webu Azure Marketplace. 

> [!NOTE]
> Proces stahování může trvat 30 minut až 2 hodiny v závislosti na latenci sítě a existujících balíčcích vaší instance centra Azure Stack. 

::: zone pivot="state-connected"
V případě připojení můžete stáhnout položky z webu Azure Marketplace přímo na tržišti centra Azure Stack:

1. Přihlaste se k portálu pro správu služby Azure Stack Hub.
2. Na levé straně vyberte **Správa Marketplace** .
3. Vyberte **Poskytovatelé prostředků**.
4. Vyberte **+ Přidat z Azure**.
5. Vyhledejte "Event Hubs" pomocí panelu hledání.
6. Ve výsledcích hledání vyberte řádek Event Hubs. 
7. Na stránce pro stažení Event Hubs vyberte verzi Event Hubs, kterou chcete nainstalovat, a potom v dolní části stránky vyberte **Stáhnout** . 
   [![Balíčky správy Marketplace](media/event-hubs-rp-install/1-marketplace-management-download.png)](media/event-hubs-rp-install/1-marketplace-management-download.png#lightbox)

Všimněte si, že další balíčky softwaru se stáhnou spolu s Event Hubs, včetně těchto:

- Centrum Microsoft Azure Stack Add-On RP jenom v systému Windows Server
- Konfigurace požadovaného stavu prostředí PowerShell
::: zone-end

::: zone pivot="state-disconnected"
V případě odpojeného nebo částečně připojeného scénáře můžete balíčky stáhnout do místního počítače a pak je importovat do svého tržiště centra Azure Stack:

1. Pokud jste to ještě neudělali, postupujte podle pokynů v části [stažení položek Marketplace – odpojené nebo částečně propojené scénáře](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected). Tady si stáhnete a spustíte nástroj pro syndikaci na webu Marketplace, který vám umožní stáhnout Event Hubs balíčky.
2. Po otevření okna Azure Marketplace položky nástroje syndikace Najděte a vyberte "Event Hubs" a Stáhněte si požadované balíčky do místního počítače.
3. Po dokončení stahování naimportujete balíčky do své instance centra Azure Stack a publikujete na Marketplace. 
::: zone-end

## <a name="installation"></a>Instalace 

1. Pokud jste to ještě neudělali, přihlaste se na portál pro správu centra Azure Stack, na levé straně vyberte **Správa Marketplace** a pak vyberte **poskytovatelé prostředků**.
2. Po stažení Event Hubs a dalším požadovanému softwaru zobrazí **Správa Marketplace** balíčky "Event Hubs" se stavem "Nenainstalováno". Mohou existovat další balíčky, které zobrazují stav "staženo". Vyberte řádek Event Hubs, který chcete nainstalovat.
   [![Stažené balíčky správy Marketplace](media/event-hubs-rp-install/2-marketplace-management-downloaded.png)](media/event-hubs-rp-install/2-marketplace-management-downloaded.png#lightbox)
 
3. Stránka Event Hubs instalace balíčku zobrazuje modrý banner v horní části. Vyberte banner a spusťte instalaci Event Hubs.
   [![Centra událostí správy Marketplace – zahájení instalace](media/event-hubs-rp-install/3-marketplace-management-install-ready.png)](media/event-hubs-rp-install/3-marketplace-management-install-ready.png#lightbox)

### <a name="install-prerequisites"></a>Požadavky na instalaci

1. Dál jste přenesli na stránku instalace. Kliknutím na **instalovat požadavky** zahajte proces instalace.
   ![Centra událostí správy Marketplace – požadavky na instalaci](media/event-hubs-rp-install/4-marketplace-management-install-prereqs-start.png)
 
2. Počkejte, dokud nebude instalace požadovaných součástí úspěšná. Před pokračováním na další krok by se měla zobrazit zelená značka zaškrtnutí vedle políčka **nainstalovat požadavky** .

   ![Centra událostí správy Marketplace – požadavky na instalaci byly úspěšné](media/event-hubs-rp-install/5-marketplace-management-install-prereqs-succeeded.png)

### <a name="prepare-secrets"></a>Příprava tajných kódů 

1. V části **2. Připravte krok tajné klíče** , vyberte **Přidat certifikát** a zobrazí se panel **Přidat certifikát** .
   ![Centra událostí správy Marketplace – Příprava tajných klíčů](media/event-hubs-rp-install/6-marketplace-management-install-prepare-secrets.png)

2. V části **Přidat certifikát** klikněte na tlačítko Procházet, a to těsně napravo od pole název souboru certifikátu.
3. Vyberte soubor certifikátu. pfx, který jste si vybrali při dokončování požadavků. Další informace najdete v tématu [předpoklady pro instalaci](event-hubs-rp-prerequisites.md). 

4. Zadejte heslo, které jste zadali, abyste vytvořili zabezpečený řetězec pro Event Hubs certifikát SSL. Pak vyberte **Přidat**.
   ![Centra událostí správy Marketplace – přidat certifikát](media/event-hubs-rp-install/7-marketplace-management-install-prepare-secrets-add-cert.png)

### <a name="install-resource-provider"></a>Nainstalovat poskytovatele prostředků

1. Po úspěšném dokončení instalace certifikátu se zobrazí zelená značka zaškrtnutí vedle **Příprava tajných** kódů, než budete pokračovat k dalšímu kroku. Nyní vyberte tlačítko **instalovat** vedle **3 instalace poskytovatele prostředků**.
   ![Instalace centra událostí správy Marketplace – spuštění RP](media/event-hubs-rp-install/8-marketplace-management-install-start.png)
 
2. V dalším kroku se zobrazí následující stránka, která indikuje, že se instaluje Event Hubs poskytovatel prostředků.
   [![Centra událostí správy Marketplace – instalace RP](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png)](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png#lightbox)
 
3. Počkejte, až se dokončí oznámení instalace. Tento proces obvykle trvá jednu nebo více hodin v závislosti na typu centra Azure Stack. 
   [![Centra událostí správy Marketplace – instalace RP dokončena](media/event-hubs-rp-install/10-marketplace-management-install-complete.png)](media/event-hubs-rp-install/10-marketplace-management-install-complete.png#lightbox)

4. Vraťte se na stránku **správy Marketplace**, **Ověřte, že** instalace Event Hubs proběhla úspěšně. Stav Event Hubs by měl obsahovat "nainstalované".
   ![Dostupná centra událostí správy Marketplace](media/event-hubs-rp-install/11-marketplace-management-rps-installed.png)

## <a name="next-steps"></a>Další kroky

Předtím, než mohou uživatelé nasadit prostředky Event Hubs, je nutné vytvořit jeden nebo více plánů, nabídek a odběrů. 

- Pokud službu nabízíte poprvé, začněte s kurzem [služeb nabídek pro uživatele](tutorial-offer-services.md) . Pak pokračujte dalším kurzem, [otestujte nabídku služeb](tutorial-test-offer.md).
- Jakmile se seznámíte s konceptem nabídky služby, vytvořte nabídku a plán, který zahrnuje poskytovatele prostředků Event Hubs. Pak vytvořte předplatné pro uživatele nebo poskytněte jim informace nabídky, aby si mohli vytvořit své vlastní. Další informace najdete v článku [Přehled služeb, plánů, nabídek a předplatných](service-plan-offer-subscription-overview.md).

Chcete-li vyhledat aktualizace a [aktualizovat Event Hubs v centru Azure Stack](resource-provider-apply-updates.md).

Pokud potřebujete odebrat poskytovatele prostředků, přečtěte si téma [odebrání poskytovatele prostředků Event Hubs](event-hubs-rp-remove.md) .

Další informace o činnostech uživatelů najdete v části [Event Hubs v článku Přehled centra Azure Stack](../user/event-hubs-overview.md) v dokumentu uživatele.