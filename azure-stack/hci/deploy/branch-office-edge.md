---
title: Nasazení pobočky a Edge na Azure Stack HCI
description: V tomto tématu najdete pokyny k plánování, konfiguraci a nasazení firemních scénářů a hraničních scénářů v Azure Stack operační systém HCI.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 672a97a9804de324edde7c3802849a32ea44c0c4
ms.sourcegitcommit: dd34ae1c6207aafb5218c31658123e913f51bf7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98691042"
---
# <a name="deploy-branch-office-and-edge-on-azure-stack-hci"></a>Nasazení pobočky a Edge na Azure Stack HCI

>Platí pro: Azure Stack HCI, verze 20H2

V tomto tématu najdete pokyny k plánování, konfiguraci a nasazení firemních scénářů a hraničních scénářů v Azure Stack operační systém HCI. V těchto pokynech je ve vaší organizaci možné spouštět složité a vysoce dostupné úlohy ve virtuálních počítačích a kontejnerech ve vzdálených nasazeních pobočky a hraničních nasazení. Výpočet na hraničních zařízeních posouvá většinu zpracování dat z centralizovaného systému na hranici sítě, blíže k zařízení nebo systému, který vyžaduje data rychle.

Pomocí Azure Stack HCL spouštějte virtualizované aplikace a úlohy s vysokou dostupností na doporučeném hardwaru. Hardware podporuje clustery skládající se ze dvou serverů konfigurovaných s vnořenou odolností pro úložiště, jednoduchého clusteru s nízkými náklady na jednotku USB s nízkou cenou a správu prostřednictvím centra pro správu Windows v prohlížeči. Podrobnosti o vytvoření určujícího clusteru zařízení USB najdete v tématu [nasazení určující sdílené složky](https://docs.microsoft.com/windows-server/failover-clustering/file-share-witness).

Azure IoT Edge přesouvá Cloud Analytics a vlastní obchodní logiku do zařízení, takže se můžete soustředit na obchodní přehledy namísto správy dat. Azure IoT Edge kombinuje AI, cloudové a hraniční výpočetní prostředí v kontejnerových úlohách, jako jsou Azure Cognitive Services, Machine Learning, Stream Analytics a Functions. Úlohy se můžou spouštět na zařízeních v rozsahu od Malin. PI do sblíženého hraničního serveru. Pomocí služby [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) můžete spravovat aplikace a zařízení Edge.

Přidání Azure IoT Edge do Azure Stack modernizesch firemních poboček a nasazení Edge do vašeho prostředí, aby podporovalo rozhraní pro nasazení aplikace [kanálu CI/CD](https://docs.microsoft.com/azure/iot-edge/how-to-continuous-integration-continuous-deployment) . DevOps zaměstnanci ve vaší organizaci můžou nasazovat a iterovat kontejnery aplikací, které sestaví a podporují prostřednictvím tradičních procesů a nástrojů správy virtuálních počítačů.

Primární funkce Azure IoT Edge:
- Open source software od Microsoftu
- Spouští se v systému Windows nebo Linux.
- Spustí na hraničních zařízeních, aby poskytovala odpovědi téměř v reálném čase.
- Zabezpečení softwaru a hardwarových mechanismů
- K dispozici v sadě [AI Toolkit pro Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge)
- Otevřená Podpora programovatelnosti pro: Java, .NET Core 2,0, Node.js, C a Python
- Podpora offline a přerušované konektivity
- Nativní Správa z Azure IoT Hub

Další informace najdete v tématu [co je Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/about-iot-edge).

## <a name="deploy-branch-office-and-edge"></a>Nasazení poboček a hraničních zařízení
Tato část popisuje na vysoké úrovni, jak získat hardware pro nasazení firemních poboček a hran na Azure Stack HCI a použít Centrum pro správu Windows ke správě. Zahrnuje také nasazení Azure IoT Edge pro správu kontejnerů v cloudu.

### <a name="step-1-acquire-hardware-from-the-azure-stack-hci-catalog"></a>Krok 1: získání hardwaru z katalogu Azure Stack HCI
Nejdřív budete muset zakoupit hardware. Nejjednodušší způsob, jak to udělat, je vyhledat preferovaného poskytovatele hardwaru Microsoftu v [katalogu Azure Stack HCI](https://hcicatalog.azurewebsites.net) a koupit integrovaný systém s předinstalovaným operačním systémem Azure Stack HCI. V katalogu můžete filtrovat, abyste viděli hardware od dodavatele optimalizovaného pro tento typ úlohy.

V opačném případě bude nutné nasadit operační systém Azure Stack HCI na svůj vlastní hardware. Podrobnosti o Azure Stack možností nasazení HCI a o instalaci centra pro správu systému Windows najdete v tématu [nasazení Azure Stack v operačním systému rozhraní HCI](./operating-system.md).

Dále pomocí centra pro správu systému Windows [vytvořte cluster Azure Stack HCI](./create-cluster.md).

### <a name="step-2-set-up-azure-monitor-in-windows-admin-center"></a>Krok 2: nastavení Azure Monitor v centru pro správu Windows
V centru pro správu Windows nastavte Azure Monitor, abyste získali přehled o stavu aplikace, sítě a serveru Azure Stack HCI nasazení firemních poboček a hraničních zařízení.

Další informace najdete v tématu [monitorování Azure Stack HCL pomocí Azure monitor](../manage/azure-monitor.md).

Centrum pro správu systému Windows můžete použít také k nastavení dalších hybridních služeb Azure, jako je zálohování, Synchronizace souborů, Site Recovery, VPN typu Point-to-site, Update Management a Security Center.

### <a name="step-3-use-container-based-apps-and-iot-data-processing"></a>Krok 3: použití aplikací založených na kontejnerech a zpracování dat IoT
Nyní jste připraveni použít moderní aplikace pro vývoj a zpracování dat v kontejnerech. Postup v této části použijte v centru pro správu systému Windows k nasazení virtuálního počítače se systémem Azure IoT Edge.

Další informace najdete v tématu [co je Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/about-iot-edge).

Nasazení Azure IoT Edge v Azure Stack HCI:
1. Pomocí centra pro správu systému Windows [vytvořte nový virtuální počítač v Azure Stack HCI](https://docs.microsoft.com/windows-server/manage/windows-admin-center/use/manage-virtual-machines#create-a-new-virtual-machine).

    Informace o podporovaných verzích operačních systémů, typech virtuálních počítačů, architekturách procesorů a požadavcích na systém najdete v tématu [Azure IoT Edge podporovaných systémech](https://docs.microsoft.com/azure/iot-edge/support).

1. Pokud ještě nemáte účet Azure, spusťte [bezplatný účet](https://azure.microsoft.com/free).
1. V Azure Portal vytvořte službu [Azure IoT Hub](https://docs.microsoft.com/azure/iot-edge/quickstart#create-an-iot-hub).
1. V Azure Portal [Zaregistrujte zařízení IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart#register-an-iot-edge-device).

    >[!NOTE]
    > Zařízení IoT Edge se nachází na virtuálním počítači se systémem Windows nebo Linux v Azure Stack HCI.

1. Na virtuálním počítači, který jste vytvořili v kroku 1, [nainstalujte a spusťte modul runtime IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart#install-and-start-the-iot-edge-runtime).

   >[!IMPORTANT]
   > K připojení modulu runtime k Azure IoT Hub budete potřebovat řetězec zařízení, který jste vytvořili v kroku 4.

1. [Nasaďte modul](https://docs.microsoft.com/azure/iot-edge/quickstart#deploy-a-module) pro Azure IoT Edge.

    Předem připravené moduly můžete nastavovat a nasazovat z části [IoT Edge moduly](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) v Azure Marketplace.

## <a name="next-steps"></a>Další kroky
Další informace o pobočce a hraničních aplikacích a Azure IoT Edge najdete v tématech:
- [Rychlý Start: nasazení prvního modulu IoT Edge do virtuálního zařízení se systémem Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux?view=iotedge-2018-06&preserve-view=true)
- [Rychlý Start: nasazení prvního modulu IoT Edge do zařízení s Windows](https://docs.microsoft.com/azure/iot-edge/quickstart?view=iotedge-2018-06&preserve-view=true)
