---
title: Jak nainstalovat IoT Hub v Azure Stack Hubu
description: Naučte se instalovat poskytovatele prostředků IoT Hub v centru Azure Stack.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 47d06bc7363a9ce8d4de8971bf26c38bfdb16bcc
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256111"
---
# <a name="how-to-install-iot-hub-on-azure-stack-hub"></a>Jak nainstalovat IoT Hub v Azure Stack Hubu

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

V tomto článku se dozvíte, jak stáhnout a nainstalovat poskytovatele prostředků IoT Hub a zpřístupnit ho zákazníkům pro předplatné. Proces instalace IoT Hub trvá přibližně 2 hodiny.

## <a name="download-iot-hub"></a>Stáhnout IoT Hub

<!-- ### Connected Scenario -->
::: zone pivot="state-connected"
Pokud vaše centrum Azure Stack má přístup k Azure Marketplace, postupujte podle kroků v této části ke stažení a instalaci IoT Hub a jeho závislostí. 

Pokud chcete stáhnout IoT Hub pro připojené nasazení, proveďte následující kroky:

1. Přihlaste se ke Azure Stack portálu pro správu centra. 
2. Na levé straně vyberte **Správa Marketplace** a pak vyberte **poskytovatelé prostředků** a pak klikněte na **+ Přidat z Azure**.

    [![Hledání RP Marketplace](media/iot-hub-rp-install/marketplace-rp-add-from-azure.png)](media/iot-hub-rp-install/marketplace-rp-add-from-azure.png#lightbox)

3. V případě potřeby vyfiltrujte IoT Hub a pak vyberte balíček **IoT Hub** .

    [![Marketplace IoT Hub RP](../operator/media/iot-hub-rp-install/download1.png)](../operator/media/iot-hub-rp-install/download1.png#lightbox)

4. Na stránce **IoT Hub** balíček vyberte **Stáhnout** .

    [![Podrobnosti o IoT Hub balíčku](../operator/media/iot-hub-rp-install/download2.png)](../operator/media/iot-hub-rp-install/download2.png#lightbox)

5. Počkejte na dokončení stahování balíčku. Stav bude zobrazovat **stahování** a může trvat až 10 minut.

    [![Stav stahování balíčku IoT Hub](../operator/media/iot-hub-rp-install/download3.png)](../operator/media/iot-hub-rp-install/download3.png#lightbox)

6. Po stažení balíčku se stav změní na **Nenainstalováno** na stránce **správy Marketplace** .

    [![Stažení balíčku IoT Hub Nenainstalováno](../operator/media/iot-hub-rp-install/download4.png)](../operator/media/iot-hub-rp-install/download4.png#lightbox)
::: zone-end

<!-- ### Disconnected or partially connected scenario -->
::: zone pivot="state-disconnected"
Pokud chcete stáhnout IoT Hub pro odpojené nebo částečně připojené nasazení, napřed si balíčky Stáhněte do místního počítače. Po dokončení stahování je budete importovat do instance centra Azure Stack.

1. Pokud jste to ještě neudělali, postupujte podle pokynů v části [stažení položek Marketplace – odpojené nebo částečně propojené scénáře](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected). Tady si stáhnete a spustíte nástroj pro syndikaci na webu Marketplace, který vám umožní stáhnout IoT Hub balíčky.
2. Po otevření okna Azure Marketplace položky nástroje syndikace Najděte a vyberte "IoT Hub" a Stáhněte si požadované balíčky do místního počítače.
3. Po dokončení stahování naimportujete balíčky do své instance centra Azure Stack a publikujete na Marketplace.
::: zone-end

## <a name="install-iot-hub"></a>Nainstalovat IoT Hub

Na stránce **správy Marketplace** postupujte podle těchto kroků a nainstalujte balíček IoT Hub:

1. Klikněte na řádek **IoT Hub** a **Spusťte instalaci**.

    [![IoT Hub RP čeká na instalaci.](../operator/media/iot-hub-rp-install/install1.png)](../operator/media/iot-hub-rp-install/install1.png#lightbox)

2. Klikněte na **požadavky na instalaci**.

    [![Požadavky na instalaci IoT Hub RP](../operator/media/iot-hub-rp-install/install2.png)](../operator/media/iot-hub-rp-install/install2.png#lightbox)

3. Stav instalace se dá sledovat v podokně oznámení. Tento krok bude trvat přibližně 10 minut.

    [![Instalace požadovaných součástí instalace IoT Hub RP](../operator/media/iot-hub-rp-install/install3.png)](../operator/media/iot-hub-rp-install/install3.png#lightbox)

4. Počkejte, než se instalace požadovaných součástí dokončí. Tento krok obvykle trvá v závislosti na prostředí někde mezi 5-10 minutami.

    [![IoT Hub RP – Příprava tajných klíčů](../operator/media/iot-hub-rp-install/install4.png)](../operator/media/iot-hub-rp-install/install4.png#lightbox)

5. V části **Příprava tajných** kódů klikněte na **Přidat certifikáty** . Zadejte certifikát PFX vytvořený v části požadavky.

    [![IoT Hub RP Příprava tajných klíčů – nahrát certifikát](../operator/media/iot-hub-rp-install/install5.png)](../operator/media/iot-hub-rp-install/install5.png#lightbox)

6. Vyhledejte a zadejte PFX, který jste vytvořili, a heslo (vstup skriptu).

    [![IoT Hub RP Příprava tajných klíčů – vybrat certifikát](../operator/media/iot-hub-rp-install/install6.png)](../operator/media/iot-hub-rp-install/install6.png#lightbox)

    [![Příprava tajných klíčů IoT Hub RP – zadání hesla](../operator/media/iot-hub-rp-install/install61.png)](../operator/media/iot-hub-rp-install/install61.png#lightbox)

7. V části **instalovat poskytovatele prostředků** klikněte na **instalovat** .

    [![IoT Hub RP Příprava tajných klíčů – dokončeno](../operator/media/iot-hub-rp-install/install7.png)](../operator/media/iot-hub-rp-install/install7.png#lightbox)

8. Po zahájení instalace můžete stav nasazení najít na webu Marketplace nebo v podokně oznámení.

    [![Probíhá instalace IoT Hub RP.](../operator/media/iot-hub-rp-install/install8.png)](../operator/media/iot-hub-rp-install/install8.png#lightbox)

9. Instalace může trvat 90 až 120 minut. Počkejte na dokončení instalace.

    [![Instalace IoT Hub RP se dokončila.](../operator/media/iot-hub-rp-install/install91.png)](../operator/media/iot-hub-rp-install/install91.png#lightbox)

    [![Tržiště RPs – nainstalované RPs](../operator/media/iot-hub-rp-install/install92.png)](../operator/media/iot-hub-rp-install/install92.png#lightbox)

Poskytovatel prostředků IoT Hub se úspěšně nainstaloval. Začněte tím, že provedete následující kroky:

1. V případě potřeby postupujte podle pokynů k [Vytvoření plánu, nabídek a předplatného](./service-plan-offer-subscription-overview.md).

2. Pokud předplatné už existuje, aktualizujte přidruženou nabídku/plán tak, aby zahrnovala službu **Microsoft. Devices** . Přejít na **plán**  ->  **zvolit plán aktualizace**  ->  **služby a kvóty**

3. Přidejte **službu Microsoft. Devices** a klikněte na **Uložit**.

    [![Přidání služby IoT Hub do plánu](../operator/media/iot-hub-rp-install/pd2.png)](../operator/media/iot-hub-rp-install/pd2.png#lightbox)

4. Všechno je nastavené! Nyní je možné vytvořit centra IoT.

## <a name="using-iot-hub"></a>Použití IoT Hub

Informace o tom, jak používat IoT Hub, najdete v [dokumentaci k Azure IoT Hub](/azure/iot-hub).

## <a name="next-steps"></a>Další kroky

Další informace o správě IoT Hub v centru Azure Stack najdete v tématu [správa IoT Hub ve službě Azure Stack hub](iot-hub-rp-manage.md).