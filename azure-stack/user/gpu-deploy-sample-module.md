---
title: Nasazení modulu IoT s povoleným grafickým procesorem (GPU) do centra Azure Stack
description: Jak nasadit modul IoT (Graphical Processing Unit) s povoleným grafickým procesorem (GPU) do centra Azure Stack
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/13/2020
ms.reviewer: gara
ms.lastreviewed: 11/13/2020
ms.openlocfilehash: b8aef224c5694cd6b8408538473dce3b86996c4d
ms.sourcegitcommit: 8187658b1d45dceed727aca3ae1b9b57aca04392
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2020
ms.locfileid: "94632788"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-hub"></a>Nasazení modulu IoT s povoleným grafickým procesorem do centra Azure Stack

Pomocí rozbočovače Azure Stack s podporou GPU můžete nasadit moduly náročné na procesor na zařízení se systémem Linux spuštěná v IoT Edge. Velikosti virtuálních počítačů optimalizované pro GPU jsou specializované virtuální počítače dostupné s jedním nebo několika grafickými procesory NVIDIA. V tomto článku se naučíte, jak používat optimalizované virtuální počítače GPU ke spouštění úloh náročných na výpočetní a datovou grafiku a vizualizaci.

Než začnete, budete potřebovat předplatné služby Azure Active Directory (Azure AD) s přístupem ke globálnímu centru Azure a Azure Stack, Azure Container Registry (ACR) a službě IoT Hub.

V tomto článku:
  - Nainstalujte virtuální počítač Linux s podporou GPU a nainstalujte správné ovladače.
  - Nainstalujte Docker a povolte GPU v modulu runtime.
  - Připojte své zařízení IoT ke službě iOT hub a nainstalujte si z webu iOT Marketplace model: `Getting started with GPUs` .
  - Nainstalujte a monitorujte zařízení z místního počítače pomocí Azure IoT Exploreru.
  - A volitelně můžete zařízení nainstalovat a monitorovat pomocí rozšíření Azure IoT v Visual Studio Code.

## <a name="prerequisites"></a>Předpoklady

V rámci vaší instance centra Azure Stack, globálního Azure a místního vývojového počítače budete potřebovat následující prostředky.

### <a name="azure-stack-hub-and-azure"></a>Centrum Azure Stack a Azure

  - Předplatné jako uživatel, který používá Azure Active Directory (Azure AD) v integrovaném systému Azure Stack hub s grafickým procesorem NVIDA. Následující čipy pracují se službou iOT hub:
    - NCv3
    - NCas_v4

    Další informace o GPU v Azure Stackovém centru najdete v tématu [virtuální počítač GPU (Graphics Processing Unit) na rozbočovači Azure Stack](gpu-vms-about.md).
  - Globální předplatné Azure. Pokud nemáte globální předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- [Azure Container Registry (ACR)](/). Poznamenejte si server pro přihlášení ACR, uživatelské jméno a heslo.
-   Služby [IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) ve službě Global Azure na úrovni Free nebo Standard.

### <a name="a-development-machine"></a>Vývojový počítač

-   V závislosti na vašich předvolbách pro vývoj můžete použít svůj vlastní počítač nebo virtuální počítač. Váš vývojový počítač bude muset podporovat vnořenou virtualizaci. Tato funkce je nutná pro spuštění Docker, modul kontejneru, který se používá v tomto článku.

  - Váš vývojový počítač bude potřebovat následující zdroje:
      - [Python 3.x](https://www.python.org/downloads/)
      - [PIP](https://pypi.org/project/pip/) pro instalaci balíčků Pythonu Tato instalace se instaluje s instalací Pythonu. Pokud máte verzi PIP, možná budete chtít upgradovat na nejnovější verzi. Můžete upgradovat pomocí nástroje pip samotného. Zadejte: `pip install --upgrade pip` .
      - [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest)
      - [Git](https://git-scm.com/downloads)
      - [Docker](https://docs.docker.com/get-docker/)
      - [Visual Studio Code](https://code.visualstudio.com/)
      - [Nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pro Visual Studio Code
      - [Balíček rozšíření Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python-extension-pack) pro Visual Studio Code

## <a name="register-an-iot-edge-device"></a>Zaregistrovat zařízení IoT Edge

Pro hostování zařízení IoT Edge použijte samostatné zařízení. Když použijete samostatné zařízení, budete mít k dispozici oddělení vašeho vývojového počítače a zařízení IoT Edge přesněji zrcadlit scénář nasazení. 

Vytvořte zařízení IoT Edge v Azure s virtuálním počítačem Linux:

1.  [Vytvořte virtuální počítač s N-Series Linux serverem](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal) na rozbočovači Azure Stack. Když instalujete součásti serveru, budete pracovat se serverem přes SSH. Další informace najdete v tématu [použití veřejného klíče SSH](/azure-stack/user/azure-stack-dev-start-howto-ssh-public-key).

2.  [Vytvoření a registrace zařízení IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-register-device)

## <a name="prepare-a-gpu-enabled-vm"></a>Příprava virtuálního počítače s podporou GPU

1. Nainstalujte ovladače GPU NVIDA na server s N-Series Linux podle kroků v článku [instalace ovladačů NVIDIA GPU pro virtuální počítače řady N-Series se systémem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup).

    > [!NOTE]  
    > K instalaci softwaru použijete příkazový řádek bash. Poznamenejte si příkazy, protože použijete stejné příkazy k instalaci ovladačů do kontejneru běžícího v Docker na VIRTUÁLNÍm počítači s podporou GPU.

2.  Nainstalujte nejnovější IoT Edge runtime na server s N-Series Linux v centru Azure Stack. Pokyny najdete v tématu [Instalace modulu runtime Azure IoT Edge v systémech Linux založených na Debian](/azure/iot-edge/how-to-install-iot-edge-linux#install-the-latest-runtime-version) .

## <a name="install-docker"></a>Instalace Dockeru

Nainstalujte Docker na virtuální počítač s podporou GPU. Chystáte se modul spustit z webu IoT Edge Marketplace v kontejneru na VIRTUÁLNÍm počítači.

Je nutné nainstalovat Docker 19,02 nebo vyšší. Modul runtime Docker teď podporuje grafické procesory NVIDIA. Další informace o bout GPU v Docker najdete v článku v dokumentaci Docker, [Možnosti modulu runtime s pamětí, procesory a GPU](https://docs.docker.com/config/containers/resource_constraints/#gpu).

### <a name="install-docker"></a>Instalace Dockeru

Kontejnery Docker můžou běžet kdekoli, místně v datacentru zákazníka, v externím poskytovateli služeb nebo v cloudu v Azure. Kontejnery imagí Docker mohou běžet nativně v systémech Linux a Windows. Image Windows ale můžou běžet jenom na hostitelích s Windows a image Linux můžou běžet na hostitelích Linux a na hostitelích se systémem Windows (s využitím virtuálního počítače Hyper-V Linux), kde hostitel znamená Server nebo virtuální počítač. Další informace najdete v tématu [co je Docker?](https://docs.microsoft.com/dotnet/architecture/microservices/container-docker-introduction/docker-defined).

1. Připojte se k serveru pro N-Series Linux pomocí klienta SSH.

1.  Aktualizujte index a seznam apt.

    ```bash  
    sudo apt-get update
    ```

1.  Načte nové verze existujících balíčků v počítači.

    ```bash  
    sudo apt-get upgrade
    ```

2.  Nainstalujte závislosti potřebné k přidání úložiště apt Docker.

    ```bash  
    sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
    ```

3.  Přidat klíč GPG Docker

    ```bash  
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

4.  Přidejte úložiště apt Docker.

    ```bash  
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

5.  Aktualizujte index a seznamy apt a nainstalujte verzi Docker Community Edition.

    ```bash  
    sudo apt-get update 
    sudo apt-get install docker-ce
    ```

6.  Ověřte instalaci kontrolou verze Docker.

    ```bash  
    docker -v
    ```

7. Zpřístupněte dostupné prostředky GPU v Docker.

    ```bash
    docker run -it --rm --gpus all ubuntu nvidia-smi
    ```

## <a name="get-the-item-from-the-marketplace"></a>Získat položku z Marketplace

Vraťte se do Azure Portal a přidejte model do svého hraničního zařízení pomocí webu iOT Marketplace. Z nabídky vyberte **modul Marketplace** . A vyhledejte a `Getting started with GPUs` postupujte podle pokynů pro přidání modulu.

Pokyny najdete v tématu [Výběr zařízení a přidání modulů](/azure/iot-edge/how-to-deploy-modules-portal#select-device-and-add-modules) .

## <a name="enable-monitoring"></a>Povolení monitorování

1. Stáhněte si [Azure IoT Explorer](/azure/iot-pnp/howto-use-iot-explorer)a připojte aplikaci k vašemu IoT Hub.

2. Vyberte zařízení IoT a přejděte k telemetrie z navigační nabídky.

3. Vyberte **začít** a začněte sledovat výstup ze zařízení IoT Edge.

![platná instalace](media/gpu-deploy-sample-module/user-azure-iot-explorer-gpu.png)

## <a name="monitor-the-module-optional"></a>Monitorování modulu (volitelné)  

1. Na paletě příkazů VS Code spusťte **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: Vybrat IoT Hub).

2. Zvolte předplatné a centrum IoT obsahující zařízení IoT Edge, které chcete nakonfigurovat. V takovém případě vyberte předplatné, které se používá k nasazení Azure Stack hraničního zařízení, a vyberte IoT Edge zařízení vytvořené pro Azure Stack hraniční zařízení. K tomu dochází při konfiguraci výpočetní kapacity prostřednictvím Azure Portal v předchozích krocích.

3. V Průzkumníku VS Code rozbalte část Azure IoT Hub. V části **zařízení** by se mělo zobrazit zařízení IoT Edge odpovídající vašemu zařízení Azure Stack Edge. 

    1. Vyberte toto zařízení, klikněte pravým tlačítkem myši a vyberte možnost **Spustit sledování integrovaného koncového bodu události**.

    2. Přejděte na **zařízení > moduly** a měli byste vidět, že je spuštěný **modul GPU** .

    3. VS Code terminál by měl také zobrazit IoT Hub události jako výstup monitorování pro zařízení Azure Stack Edge.

    ![platná instalace je](media/gpu-deploy-sample-module/gpu-monitor-events-output.png)

    Zjistíte, že čas potřebný k provedení stejné sady operací (5000 iterací transformace obrazce) procesorem GPU je větší než procesor.

## <a name="next-steps"></a>Další kroky

  - Další informace o [virtuálním počítači GPU (Graphics Processing Unit) na rozbočovači Azure Stack](gpu-vms-about.md)

  - Další informace o Azure Stackovém centru, Data Box Edge a inteligentním hraničním prostředí, [budoucí výpočetní prostředí: inteligentní cloudová a inteligentní hrana](https://azure.microsoft.com/overview/future-of-cloud)

  - Další informace o hybridních cloudových aplikacích najdete v tématu [hybridní cloudová řešení](https://docs.microsoft.com/hybrid/app-solutions/) .