---
title: Příprava hostitelského počítače s ASDK
description: Přečtěte si, jak připravit hostitelský počítač Azure Stack Development Kit (ASDK) pro instalaci ASDK.
author: justinha
ms.topic: article
ms.date: 08/28/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 08/28/2019
ms.openlocfilehash: 81e8e8f470988d84441d238ef677950df2d2334b
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873613"
---
# <a name="prepare-the-asdk-host-computer"></a>Příprava hostitelského počítače s ASDK
Než budete moct na hostitelský počítač nainstalovat Azure Stack Development Kit (ASDK), musí být hostitel ASDK připravený k instalaci. Po přípravě hostitele se spustí z pevného disku virtuálního počítače CloudBuilder. vhdx, který zahájí ASDK nasazení.

## <a name="prepare-the-development-kit-host-computer"></a>Příprava hostitelského počítače pro vývojovou sadu
Než budete moct nainstalovat ASDK na hostitelský počítač, musí být připravený prostředí hostitelského počítače ASDK. K přípravě tohoto prostředí použijte následující postup:

1. Přihlaste se jako místní správce k hostitelskému počítači ASDK.
2. Zajistěte, aby byl soubor CloudBuilder. vhdx přesunut do kořenového adresáře C:\. jednotka (`C:\CloudBuilder.vhdx`).
3. Spusťte následující skript pro stažení instalačního souboru ASDK (asdk-Installer. ps1) [Azure Stack z úložiště nástrojů GitHubu](https://github.com/Azure/AzureStack-Tools) do složky **AzureStack_Installer** na hostitelském počítači s ASDK:

   > [!IMPORTANT]
   > Nezapomeňte si stáhnout soubor asdk-Installer. ps1 pokaždé, když nainstalujete ASDK. V tomto skriptu jsou k disASDK časté změny a nejaktuálnější verze by se měly používat pro každé nasazení. Starší verze skriptu nemusí fungovat s nejaktuálnější verzí.

   ```powershell
   # Variables
   $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
   $LocalPath = 'C:\AzureStack_Installer'
   # Create folder
   New-Item $LocalPath -Type directory
   # Enforce usage of TLSv1.2 to download from GitHub
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   # Download file
   Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
   ```

4. Z konzoly PowerShellu se zvýšenými oprávněními spusťte skript **c:\ AzureStack_Installer \asdk-Installer.ps1** a pak klikněte na **připravit prostředí**.

    ![Příprava prostředí v ASDK](media/asdk-prepare-host/1.PNG) 

5. Na stránce **Vyberte Cloudbuilder VHDX** instalačního programu vyhledejte a vyberte soubor **Cloudbuilder. vhdx** , který jste stáhli a extrahovali v [předchozích krocích](asdk-download.md). Na této stránce můžete také povolit zaškrtávací políčko **Přidat ovladače** , pokud potřebujete přidat další ovladače do hostitelského počítače sady ASDK Kit. Klikněte na **Další**.  

    ![Vyberte cloudbuilder. vhdx a přidejte do ASDK ovladače.](media/asdk-prepare-host/2.PNG)

6. Na stránce **volitelná nastavení** zadejte informace o účtu místního správce pro hostitelský počítač ASDK a potom klikněte na **Další**.

    Pokud v tomto kroku nezadáte přihlašovací údaje místního správce, budete po restartování počítače potřebovat přímý nebo KVM přístup k hostiteli, a to jako součást nastavení ASDK.

   ![Volitelná nastavení v ASDK – zadejte informace o účtu místního správce.](media/asdk-prepare-host/3.PNG)

    Můžete také zadat hodnoty pro následující volitelná nastavení:
    - **Názevpočítače**: Tato možnost nastaví název hostitele ASDK. Název musí splňovat požadavky na plně kvalifikovaný název domény a musí být delší než 15 znaků. Výchozím nastavením je náhodný název počítače generovaný systémem Windows.

        - Vyberte síťový adaptér. Před kliknutím na tlačítko **Další**se ujistěte, že se můžete připojit k adaptéru.

            ![Snímek obrazovky s nastavením síťového adaptéru](media/asdk-prepare-host/step-four-network-adapter.png)

        - Zkontrolujte, zda jsou zobrazené hodnoty **IP adresy**, **brány**a **DNS** správné, zadejte platnou adresu **IP serveru** a klikněte na tlačítko **Další**.

            >[!TIP]
            >Pokud chcete najít IP adresu časového serveru, navštivte [ntppool.org](https://www.ntppool.org/) nebo otestujte Time.Windows.com. 

            ![Snímek obrazovky s nastavením konfigurace protokolu IP](media/asdk-prepare-host/step-five-host-ip-config.png)

7. Kliknutím na tlačítko **Další** zahájíte proces přípravy.
8. Až se příprava zobrazí jako **Dokončená**, klikněte na **Další**.

    ![Příprava prostředí v ASDK](media/asdk-prepare-host/4.PNG)

9. Kliknutím na **restartovat nyní** spusťte hostitelský počítač ASDK do souboru cloudbuilder. vhdx a [pokračujte v procesu nasazení](asdk-install.md).

    ![Restartujte ASDK](media/asdk-prepare-host/5.PNG)


## <a name="next-steps"></a>Další kroky
[Instalace ASDK](asdk-install.md)
