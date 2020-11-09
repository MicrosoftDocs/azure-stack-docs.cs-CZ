---
title: Připojení GPU k virtuálnímu počítači se systémem Linux v Azure Stack HCI
description: Jak používat GPU s úlohami AI běžícími v Ubuntu Linuxm VIRTUÁLNÍm počítači v Azure Stack HCL.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/01/2020
ms.openlocfilehash: de2526b2807f4deff66efdf6db69bf4b791f5814
ms.sourcegitcommit: ce864e1d86ad05a03fe896721dea8f0cce92085f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94383679"
---
# <a name="attaching-a-gpu-to-an-ubuntu-linux-vm-on-azure-stack-hci"></a>Připojení GPU k virtuálnímu počítači s Ubuntu Linux v Azure Stack HCI

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

V tomto tématu najdete podrobné pokyny k instalaci a konfiguraci grafického procesoru (GPU) NVIDIA s Azure Stackm HCI pomocí technologie DDA (diskrétního přiřazení zařízení) pro virtuální počítač s Ubuntu (VM).
V tomto dokumentu se předpokládá, že máte nasazený cluster Azure Stack HCI a virtuální počítače jsou nainstalované.

## <a name="install-the-gpu-and-then-dismount-it-in-powershell"></a>Instalace GPU a jejich odpojení v PowerShellu

1. Nainstalujte grafické procesory NVIDIA fyzicky do příslušných serverů podle pokynů výrobce OEM a doporučení systému BIOS.
2. Zapněte na každém serveru.
3. Přihlaste se pomocí účtu s oprávněními správce k serveru s nainstalovaným grafickým procesorem NVIDIA.
4. Otevřete **Device Manager** a přejděte do části *Další zařízení* . Mělo by se zobrazit zařízení uvedené jako "3D video Controller".
5. Kliknutím pravým tlačítkem myši na 3D video Controller zobrazte stránku **vlastností** . Klikněte na tlačítko **Podrobnosti**. V rozevíracím seznamu pod položkou **vlastnost** vyberte možnost cesty umístění.
6. Všimněte si hodnoty pomocí řetězce PCIRoot, jak je zvýrazněno na snímku obrazovky níže. Klikněte pravým tlačítkem na **hodnotu** a zkopírujte nebo uložte.
    :::image type="content" source="media/attach-gpu-to-linux-vm/pciroot.png" alt-text="Snímek cesty k umístění":::
7. Otevřete prostředí Windows PowerShell se zvýšenými oprávněními a spusťte `Dismount-VMHostAssignableDevice` rutinu, která odpojí zařízení GPU pro položku DDA s virtuálním počítačem. Hodnotu *LocationPath* nahraďte hodnotou svého zařízení získaného v kroku 6.
    ```PowerShell
    Dismount-VMHostAssignableDevice -LocationPath "PCIROOT(16)#PCI(0000)#PCI(0000)" -force
    ```
8. Potvrďte, že je zařízení uvedené v části systémová zařízení v **Device Manager** jako odpojená.
    :::image type="content" source="media/attach-gpu-to-linux-vm/dismounted.png" alt-text="Snímek obrazovky odpojeného zařízení":::

## <a name="create-and-configure-an-ubuntu-virtual-machine"></a>Vytvoření a konfigurace virtuálního počítače s Ubuntu

1. Stáhněte si [Ubuntu Desktop Release 18.04.02 ISO](http://old-releases.ubuntu.com/releases/18.04.2/).
2. Otevřete **Správce technologie Hyper-V** v uzlu systému s nainstalovaným grafickým procesorem.
   > [!NOTE]
   > [DDA nepodporují převzetí služeb při selhání](/windows-server/virtualization/hyper-v/plan/plan-for-deploying-devices-using-discrete-device-assignment). Toto je omezení virtuálních počítačů s využitím DDA. Proto doporučujeme použít **Správce technologie Hyper-V** k nasazení virtuálního počítače na uzel místo **Správce clusteru s podporou převzetí služeb při selhání**. Použití **Správce clusteru s podporou převzetí služeb při selhání** s ÚDAJEm DDA se nezdaří a zobrazí se chybová zpráva oznamující, že virtuální počítač má zařízení, které nepodporuje vysokou dostupnost.
3. Pomocí Ubuntu ISO stažené v kroku 1 vytvořte nový virtuální počítač pomocí **Průvodce novým virtuálním počítačem** ve **Správci technologie Hyper-V** , abyste vytvořili virtuální počítač Ubuntu s 1 GB paměti a připojenou síťovou kartou.
4. V prostředí PowerShell přiřaďte zařízení odpojeného GPU k virtuálnímu počítači pomocí níže uvedených rutin a nahraďte hodnotu *LocationPath* hodnotou pro vaše zařízení.
    ```PowerShell
    # Confirm that there are no DDA devices assigned to the VM
    Get-VMAssignableDevice -VMName Ubuntu

    # Assign the GPU to the VM
    Add-VMAssignableDevice -LocationPath "PCIROOT(16)#PCI(0000)#PCI(0000)" -VMName Ubuntu

    # Confirm that the GPU is assigned to the VM
    Get-VMAssignableDevice -VMName Ubuntu
    ```

    Úspěšné přiřazení GPU k virtuálnímu počítači zobrazí výstup níže:  :::image type="content" source="media/attach-gpu-to-linux-vm/assign-gpu.png" alt-text="přiřadit obrázek GPU":::

    Další hodnoty nakonfigurujte v dokumentaci GPU [tady](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda):

   ```PowerShell
    # Enable Write-Combining on the CPU
    Set-VM -GuestControlledCacheTypes $true -VMName VMName

    # Configure the 32 bit MMIO space
    Set-VM -LowMemoryMappedIoSpace 3Gb -VMName VMName

    # Configure greater than 32 bit MMIO space
    Set-VM -HighMemoryMappedIoSpace 33280Mb -VMName VMName
   ```

   > [!NOTE]
   > Hodnota 33280Mb by měla postačovat pro většinu GPU, ale měla by být nahrazena hodnotou vyšší než vaše paměť GPU.

5. Pomocí Správce technologie Hyper-V se připojte k virtuálnímu počítači a spusťte instalaci operačního systému Ubuntu. Vyberte výchozí nastavení pro instalaci operačního systému Ubuntu ve virtuálním počítači.

6. Po dokončení instalace pomocí **Správce technologie Hyper-V** vypněte virtuální počítač a nakonfigurujte **akci automatického zastavení** pro virtuální počítač, aby se ukončil hostovaný operační systém, jako na následujícím snímku obrazovky: :::image type="content" source="media/attach-gpu-to-linux-vm/guest-shutdown.png" alt-text="snímek obrazovky pro vypnutí hostovaného operačního"::: systému.

7. Přihlaste se k Ubuntu a otevřete terminál pro instalaci SSH:

   ```shell
    $ sudo apt install openssh-server
   ```

8. Vyhledejte adresu TCP/IP pro instalaci Ubuntu pomocí příkazu **ifconfig** a zkopírujte IP adresu pro rozhraní **eth0** .

9. Použijte klienta SSH, jako je OpenSSH ssh.exe (ve výchozím nastavení nainstalovaný s Windows 10) [, nebo zadejte výstup pro](https://www.chiark.greenend.org.uk/~sgtatham/putty/) připojení k virtuálnímu počítači Ubuntu pro další konfiguraci.

10. Při přihlášení pomocí klienta SSH vydejte příkaz **lspci** a ověřte, jestli je grafický procesor NVIDIA uvedený jako "3D Controller".

    > [!IMPORTANT]
    > Pokud se grafický procesor NVIDIA nezobrazuje jako "prostorový kontroler", nepokračujte dále. Než budete pokračovat, ujistěte se, že výše uvedené kroky jsou následovány.

11. V rámci virtuálního počítače vyhledejte a otevřete **aktualizace softwaru &**. Přejděte na **Další ovladače** a pak zvolte nejnovější ovladače NVIDIA GPU uvedené v seznamu. Dokončete instalaci ovladače kliknutím na tlačítko **použít změny** .
    :::image type="content" source="media/attach-gpu-to-linux-vm/driver-install.png" alt-text="Snímek instalace ovladače":::

12. Po dokončení instalace ovladače restartujte virtuální počítač Ubuntu. Jakmile se virtuální počítač spustí, připojte se přes klienta SSH a vydejte příkaz **NVIDIA-SMI** , který ověří, jestli se instalace ovladače NVIDIA GPU úspěšně dokončila. Výstup by měl být podobný snímku obrazovky níže: :::image type="content" source="media/attach-gpu-to-linux-vm/nvidia-smi.png" alt-text="snímek obrazovky, který zobrazuje výstup z příkazu NVIDIA-SMI.":::

13. Pomocí klienta SSH nastavte úložiště a nainstalujte modul Docker CE:

    ```shell
    $ sudo apt-get update
    $ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
    ```
    Přidat oficiální klíč GPG Docker:

    ```shell
    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

    Vyhledejte si klíč s otiskem prstu 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88, a to tak, že vyhledáte posledních osm znaků otisku prstu:

    ```shell
    $ sudo apt-key fingerprint 0EBFCD88
    ```

    Výstup by měl vypadat nějak takto:

    ```shell
    pub   rsa4096 2017-02-22 [SCEA]
    9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
    uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
    sub   rsa4096 2017-02-22 [S]
    ```

    Nastavte stabilní úložiště pro architekturu Ubuntu AMD64:

    ```shell
    $ sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
    ```

    Aktualizujte balíčky a nainstalujte Docker CE:

    ```shell
    $ sudo apt-get update
    $ sudo apt-get install docker-ce docker-ce-cli containerd.io
    ```

    Ověřte instalaci Docker CE:

    ```shell
    $ sudo docker run hello-world
    ```

## <a name="configure-azure-iot-edge"></a>Konfigurace Azure IoT Edge

Pro přípravu této konfigurace si přečtěte nejčastější dotazy, které najdete v úložišti na GitHubu [NVIDIA-Deepstream-Azure-IoT-Edge-on-a-NVIDIA-Jetson-nano](https://github.com/Azure-Samples/NVIDIA-Deepstream-Azure-IoT-Edge-on-a-NVIDIA-Jetson-Nano) , které vysvětluje nutnost instalovat Docker místo Moby. Po kontrole přejděte k následujícímu postupu.

### <a name="install-nvidia-docker"></a>Nainstalovat Docker NVIDIA

1. Z klienta SSH přidejte úložiště balíčků:

    ```shell
    curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \
    sudo apt-key add -
    distribution=$(. /etc/os-release;echo $ID$VERSION_ID)

    curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
    sudo tee /etc/apt/sources.list.d/nvidia-docker.list

    sudo apt-get update
    ```

2. Nainstalujte NVIDIA-docker2 a znovu načtěte konfiguraci démona Docker:

    ```shell
    sudo apt-get install -y nvidia-docker2
    sudo pkill -SIGHUP dockerd
    ```

3. Restartujte virtuální počítač:

    ```shell
    sudo /sbin/shutdown -r now
    ```

4. Při restartování ověřte úspěšnou instalaci Docker pro NVIDIA:

    ```shell
    sudo docker run --runtime=nvidia --rm nvidia/cuda:9.0-base nvidia-smi
    ```

    Úspěšná instalace bude vypadat jako výstup na snímku obrazovky níže:  :::image type="content" source="media/attach-gpu-to-linux-vm/docker.png" alt-text="úspěšné instalace Docker instalačního snímku":::

5. Podle pokynů uvedených tady pokračujte v instalaci Azure IoT Edge a přeskočí se instalace modulu runtime:

    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list

    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/

    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    sudo apt-get update

    sudo apt-get install iotedge
    ```

    > [!NOTE]
    > Po instalaci Azure IoT Edge ověřte, že je na virtuálním počítači Ubuntu na adrese/etc/iotedge/config.yaml soubor config. yaml.

6. Vytvořte IoT Edge identitu zařízení v [Azure Portal níže uvedené pokyny.](/azure/iot-edge/how-to-register-device#register-in-the-azure-portal) Potom zkopírujte připojovací řetězec zařízení pro nově vytvořenou IoT Edge.

7. Pomocí klienta SSH aktualizujte připojovací řetězec zařízení v souboru config. yaml na virtuálním počítači Ubuntu:

    ```shell
    sudo nano /etc/iotedge/config.yaml
    ```

    Vyhledejte konfigurace zřizování souboru a odkomentujte oddíl ruční konfigurace zřizování. Aktualizujte hodnotu device_connection_string připojovacím řetězcem ze zařízení IoT Edge. Ujistěte se, že jsou všechny ostatní oddíly pro zřizování zakomentovány. Ujistěte se, že zřizování: řádek neobsahuje žádné předchozí prázdné znaky a že vnořené položky jsou odsazeny o dva mezery:

    :::image type="content" source="media/attach-gpu-to-linux-vm/manual-provisioning.png" alt-text="Snímek obrazovky konfigurace ručního zřizování":::

    Chcete-li vložit obsah schránky do nano, klikněte na tlačítko Shift + pravým tlačítkem nebo stiskněte klávesu SHIFT + INSERT. Uložte a zavřete soubor (CTRL + X, Y, ENTER).

8. Pomocí klienta SSH restartujte službu IoT Edge démon:

    ```shell
    sudo systemctl restart iotedge
    ```

    Ověřte instalaci a zkontrolujte stav procesu démona IoT Edge:

    ```shell
    systemctl status iotedge

    journalctl -u iotedge --no-pager --no-full
    ```

9. Pomocí klienta SSH vytvořte na virtuálním počítači s Ubuntu následující adresářovou strukturu:

    ```shell
    cd /var
    sudo mkdir deepstream
    mkdir ./deepstream/custom_configs
    cd /var/deepstream
    sudo mkdir custom_streams
    sudo chmod -R 777 /var/deepstream
    cd ./custom_streams
    ```

10. Ujistěte se, že je váš pracovní adresář/var/deepstream/custom_streams a Stáhněte si [soubor demonstračních videí](https://github.com/Azure-Samples/NVIDIA-Deepstream-Azure-IoT-Edge-on-a-NVIDIA-Jetson-Nano) spuštěním následujícího příkazu v klientovi SSH:

    ```shell
    wget -O cars-streams.tar.gz --no-check-certificate https://onedrive.live.com/download?cid=0C0A4A69A0CDCB4C&resid=0C0A4A69A0CDCB4C%21588371&authkey=AAavgrxG95v9gu0
    ```

    Zrušit komprimaci videosouborů:

    ```shell
    tar -xzvf cars-streams.tar.gz
    ```

    Obsah adresáře custom_streams/var/deepstream/by měl být podobný snímku obrazovky:

    :::image type="content" source="media/attach-gpu-to-linux-vm/custom-streams.png" alt-text="Snímek obrazovky vlastních streamů":::

11. Vytvořte nový soubor s názvem test5_config_file_src_infer_azure_iotedge_edited.txt v adresáři custom_configs/var/deepstream/. Pomocí textového editoru otevřete soubor a vložte následující kód a soubor uložte a zavřete.

    ```shell
    # Copyright (c) 2018 NVIDIA Corporation.  All rights reserved.
    #
    # NVIDIA Corporation and its licensors retain all intellectual property
    # and proprietary rights in and to this software, related documentation
    # and any modifications thereto.  Any use, reproduction, disclosure or
    # distribution of this software and related documentation without an express
    # license agreement from NVIDIA Corporation is strictly prohibited.

    [application]
    enable-perf-measurement=1
    perf-measurement-interval-sec=5
    #gie-kitti-output-dir=streamscl

    [tiled-display]
    enable=1
    rows=2
    columns=2
    width=1280
    height=720
    gpu-id=0
    #(0): nvbuf-mem-default - Default memory allocated, specific to particular platform
    #(1): nvbuf-mem-cuda-pinned - Allocate Pinned/Host cuda memory, applicable for Tesla
    #(2): nvbuf-mem-cuda-device - Allocate Device cuda memory, applicable for Tesla
    #(3): nvbuf-mem-cuda-unified - Allocate Unified cuda memory, applicable for Tesla
    #(4): nvbuf-mem-surface-array - Allocate Surface Array memory, applicable for Jetson
    nvbuf-memory-type=0

    [source0]
    enable=1
    #Type - 1=CameraV4L2 2=URI 3=MultiURI
    type=3
    uri=file://../../../../../samples/streams/sample_1080p_h264.mp4
    num-sources=2
    gpu-id=0
    nvbuf-memory-type=0

    [source1]
    enable=1
    #Type - 1=CameraV4L2 2=URI 3=MultiURI
    type=3
    uri=file://../../../../../samples/streams/sample_1080p_h264.mp4
    num-sources=2
    gpu-id=0
    nvbuf-memory-type=0

    [sink0]
    enable=0

    [sink3]
    enable=1
    #Type - 1=FakeSink 2=EglSink 3=File 4=RTSPStreaming
    type=4
    #1=h264 2=h265
    codec=1
    sync=0
    bitrate=4000000
    # set below properties in case of RTSPStreaming
    rtsp-port=8554
    udp-port=5400

    [sink1]
    enable=1
    #Type - 1=FakeSink 2=EglSink 3=File 4=UDPSink 5=nvoverlaysink 6=MsgConvBroker
    type=6
    msg-conv-config=../configs/dstest5_msgconv_sample_config.txt
    #(0): PAYLOAD_DEEPSTREAM - Deepstream schema payload
    #(1): PAYLOAD_DEEPSTREAM_MINIMAL - Deepstream schema payload minimal
    #(256): PAYLOAD_RESERVED - Reserved type
    #(257): PAYLOAD_CUSTOM   - Custom schema payload
    msg-conv-payload-type=1
    msg-broker-proto-lib=/opt/nvidia/deepstream/deepstream-4.0/lib/libnvds_azure_edge_proto.so
    topic=mytopic
    #Optional:
    #msg-broker-config=../../../../libs/azure_protocol_adaptor/module_client/cfg_azure.txt

    [sink2]
    enable=0
    type=3
    #1=mp4 2=mkv
    container=1
    #1=h264 2=h265 3=mpeg4
    ## only SW mpeg4 is supported right now.
    codec=3
    sync=1
    bitrate=2000000
    output-file=out.mp4
    source-id=0

    [osd]
    enable=1
    gpu-id=0
    border-width=1
    text-size=15
    text-color=1;1;1;1;
    text-bg-color=0.3;0.3;0.3;1
    font=Arial
    show-clock=0
    clock-x-offset=800
    clock-y-offset=820
    clock-text-size=12
    clock-color=1;0;0;0
    nvbuf-memory-type=0

    [streammux]
    gpu-id=0
    ##Boolean property to inform muxer that sources are live
    live-source=0
    batch-size=4
    ##time out in usec, to wait after the first buffer is available
    ##to push the batch even if the complete batch is not formed
    batched-push-timeout=40000
    ## Set muxer output width and height
    width=1920
    height=1080
    ##Enable to maintain aspect ratio wrt source, and allow black borders, works
    ##along with width, height properties
    enable-padding=0
    nvbuf-memory-type=0

    [primary-gie]
    enable=1
    gpu-id=0
    batch-size=4
    ## 0=FP32, 1=INT8, 2=FP16 mode
    bbox-border-color0=1;0;0;1
    bbox-border-color1=0;1;1;1
    bbox-border-color2=0;1;1;1
    bbox-border-color3=0;1;0;1
    nvbuf-memory-type=0
    interval=0
    gie-unique-id=1
    model-engine-file=../../../../../samples/models/Primary_Detector/resnet10.caffemodel_b4_int8.engine
    labelfile-path=../../../../../samples/models/Primary_Detector/labels.txt
    config-file=../../../../../samples/configs/deepstream-app/config_infer_primary.txt
    #infer-raw-output-dir=../../../../../samples/primary_detector_raw_output/

    [tracker]
    enable=1
    tracker-width=600
    tracker-height=300
    ll-lib-file=/opt/nvidia/deepstream/deepstream-4.0/lib/libnvds_mot_klt.so
    #ll-config-file required for DCF/IOU only
    #ll-config-file=tracker_config.yml
    #ll-config-file=iou_config.txt
    gpu-id=0
    #enable-batch-process applicable to DCF only
    enable-batch-process=0

    [tests]
    file-loop=1
    ```

12. Přejděte na Azure Portal. Vyberte **IoT Hub zřízené** , klikněte na **automatickou správu zařízení** a pak klikněte na **IoT Edge** :

    :::image type="content" source="media/attach-gpu-to-linux-vm/iot-edge.png" alt-text="Snímek obrazovky automatické správy zařízení":::

13. V pravém podokně vyberte identitu zařízení, jejíž připojovací řetězec zařízení se použil výše. Klikněte na nastavit moduly:

    :::image type="content" source="media/attach-gpu-to-linux-vm/set-modules.png" alt-text="Snímek obrazovky s nastavením modulů":::

14. V části IoT Edge moduly klikněte na a vyberte modul Marketplace:

    :::image type="content" source="media/attach-gpu-to-linux-vm/marketplace-module.png" alt-text="Snímek obrazovky modulu Marketplace":::

15. Vyhledejte NVIDIA a vyberte DeepStream SDK, jak je zobrazeno níže:

    :::image type="content" source="media/attach-gpu-to-linux-vm/deepstream.png" alt-text="Snímek obrazovky sady DeepStream SDK":::

16. Ujistěte se, že je modul NvidiaDeepStreamSDK uvedený v části IoT Edge moduly:

    :::image type="content" source="media/attach-gpu-to-linux-vm/edge-modules.png" alt-text="Snímek obrazovky s IoT Edgemi moduly":::

17. Klikněte na modul NVIDIADeepStreamSDK a vyberte možnost vytvořit kontejner možnosti. Výchozí konfigurace se zobrazí zde:

    :::image type="content" source="media/attach-gpu-to-linux-vm/container-create-options.png" alt-text="Snímek obrazovky s možnostmi vytvoření kontejneru":::

    Výše uvedenou konfiguraci nahraďte konfigurací uvedenou níže:

    ```shell
    {
      "ExposedPorts": {
        "8554/tcp": {}
      },
      "Entrypoint": [
        "/usr/bin/deepstream-test5-app",
        "-c",
        "test5_config_file_src_infer_azure_iotedge_edited.txt",
        "-p",
        "1",
        "-m",
        "1"
      ],
      "HostConfig": {
        "runtime": "nvidia",
        "Binds": [
          "/var/deepstream/custom_configs:/root/deepstream_sdk_v4.0.2_x86_64/sources/apps/sample_apps/deepstream-test5/custom_configs/",
          "/var/deepstream/custom_streams:/root/deepstream_sdk_v4.0.2_x86_64/sources/apps/sample_apps/deepstream-test5/custom_streams/"
        ],
        "PortBindings": {
          "8554/tcp": [
            {
              "HostPort": "8554"
            }
          ]
        }
      },
      "WorkingDir": "/root/deepstream_sdk_v4.0.2_x86_64/sources/apps/sample_apps/deepstream-test5/custom_configs/"
    }
    ```

18. Klikněte na tlačítko **zkontrolovat a vytvořit** a na další stránce klikněte na **vytvořit**. Nyní byste měli vidět tři níže uvedené moduly pro zařízení IoT Edge v Azure Portal:

    :::image type="content" source="media/attach-gpu-to-linux-vm/edge-hub-connections.png" alt-text="Snímek obrazovky moduly a IoT Edge připojení centra":::

19. Připojte se k virtuálnímu počítači s Ubuntu pomocí klienta SSH a ověřte, jestli jsou spuštěné správné moduly:

    ```shell
    sudo iotedge list
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify-modules-sudo.png" alt-text="Snímek obrazovky, který zobrazuje výstup ze seznamu iotedge.":::

    ```shell
    nvidia-smi
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify-modules-nvidia-smi.png" alt-text="snímek obrazovky NVIDIA-SMI":::

    > [!NOTE]
    > Stažení kontejneru NvidiaDeepstream bude trvat několik minut. Můžete ověřit stahování pomocí příkazu "journalctl-u iotedge--No-pager--ne-Full" a podívat se na protokoly iotedge démona.

20. Potvrďte, že je kontejner NvdiaDeepStreem funkční. Výstup příkazu na snímcích obrazovky níže indikuje úspěch.

    ```shell
    sudo iotedge list
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify1.png" alt-text="Snímek obrazovky s výstupem zobrazujícím kontejner NvdiaDeepStreem je funkční.":::

    ```shell
    sudo iotedge logs -f NVIDIADeepStreamSDK
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify2.png" alt-text="Snímek obrazovky, který zobrazuje výstup pro příkaz iotedge logs-f NVIDIADeepStreamSDK.":::

    ```shell
    nvidia-smi
    ```

    :::image type="content" source="media/attach-gpu-to-linux-vm/verify3.png" alt-text="Snímek obrazovky, který zobrazuje další výstup pro příkaz NVIDIA-SMI.":::

21. Pomocí příkazu **ifconfig** potvrďte adresu TCP/IP pro virtuální počítač s Ubuntu a vyhledejte adresu TCP/IP vedle rozhraní **eth0** .

22. Nainstalujte do pracovní stanice VLC Player. V VLC Playeru klikněte na **multimédia – > otevřít síťový Stream** a zadejte adresu v tomto formátu:

    rtsp://ipaddress:8554/ds-test

    kde IPAddress je adresa TCP/IP vašeho virtuálního počítače.

    :::image type="content" source="media/attach-gpu-to-linux-vm/vlc-player.png" alt-text="Snímek obrazovky přehrávače VLC":::

## <a name="next-steps"></a>Další kroky

Další informace o GPU a DDA najdete v tématu také:

- [Plánování nasazení zařízení pomocí diskrétního přiřazení zařízení](/windows-server/virtualization/hyper-v/plan/plan-for-deploying-devices-using-discrete-device-assignment)
- [Nasazení grafických zařízení pomocí přiřazení diskrétního zařízení](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda)
