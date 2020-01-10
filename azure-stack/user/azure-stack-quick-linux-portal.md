---
title: Vytvoření virtuálního počítače se systémem Linux pomocí centra Azure Stack | Microsoft Docs
description: Vytvořte virtuální počítač s Linux serverem pomocí centra Azure Stack.
services: azure-stack
cloud: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.custom: mvc
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 1edfff9f684bb5ca9ed46ee06e98685f026b5bea
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75819348"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-the-azure-stack-hub-portal"></a>Rychlý Start: Vytvoření virtuálního počítače s Linux serverem pomocí portálu Azure Stack hub

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack Development Kit*

Virtuální počítač s Ubuntu serverem 16,04 LTS můžete vytvořit pomocí portálu centra Azure Stack. V tomto článku vytvoříte a použijete virtuální počítač. Tento článek také ukazuje, jak:

* Připojte se k virtuálnímu počítači pomocí vzdáleného klienta.
* Instalace webového serveru NGINX
* Vyčistěte prostředky.

> [!NOTE]  
> Obrázky v tomto článku jsou aktualizované tak, aby odpovídaly změnám, které byly představeny v Azure Stack centra verze 1808. Verze 1808 kromě nespravovaných disků přidává podporu pro používání *spravovaných disků* . Pokud používáte starší verzi, obrázky pro některé úlohy, jako je výběr disku, se liší od toho, co se zobrazuje v uživatelském rozhraní.  

## <a name="prerequisites"></a>Požadavky

* Image Linux na webu centra Azure Stack Marketplace

   Tržiště centra Azure Stack ve výchozím nastavení nemá bitovou kopii operačního systému Linux. Použijte operátor centra Azure Stack, který obsahuje bitovou kopii Ubuntu serveru 16,04 LTS, kterou potřebujete. Operátor může použít pokyny v tématu [stažení položek Marketplace z Azure do centra Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md).

* Přístup k klientovi SSH

   Pokud používáte Azure Stack Development Kit (ASDK), možná nemáte přístup k klientovi Secure Shell (SSH). Pokud potřebujete klienta, obsahuje několik balíčků klienta SSH. Například výstup obsahuje klienta SSH a generátor klíčů SSH (PuTTYgen. exe). Další informace o dostupných balíčcích najdete v tématu [Jak používat veřejný klíč SSH](azure-stack-dev-start-howto-ssh-public-key.md).

* V tomto rychlém startu se k vygenerování klíčů SSH a připojení k virtuálnímu počítači serveru pro Linux používá výstup. [Stáhněte a nainstalujte PuTTY](https://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH

K dokončení všech kroků v tomto článku potřebujete pár klíčů SSH. Pokud už máte pár klíčů SSH, můžete tento krok přeskočit.

Vytvoření páru klíčů SSH:

1. Přejít do instalační složky pro výstupy (výchozí umístění je *C:\Program Files\PuTTY*) a spustit:

    `puttygen.exe`

1. V okně **generátoru klíčů** pro výstupy nastavte **typ klíče, který se má generovat** na **RSA**, a nastavte **počet bitů ve vygenerovaném klíči** na **2048**.

   ![Konfigurace generátoru klíčů pro výstupu](media/azure-stack-quick-linux-portal/Putty01.PNG)

1. Vyberte **Generovat**.

1. Pokud chcete vygenerovat klíč, v poli **klíč** přesuňte ukazatel náhodně.

1. Po dokončení generování klíče vyberte možnost **Uložit veřejný klíč**a potom vyberte **Uložit privátní klíč** a uložte klíče do souborů.

   ![Výsledky generátoru klíčů výstupu](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-hub-portal"></a>Přihlášení k portálu centra Azure Stack

Adresa portálu centra Azure Stack závisí na tom, k jakému produktu Azure Stack centra se připojujete:

* V případě ASDK přejít na https://portal.local.azurestack.external.

* V případě integrovaného systému služby Azure Stack hub přejít na adresu URL, kterou poskytl váš operátor centra Azure Stack.

## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

1. V levém horním rohu portálu centra Azure Stack vyberte **vytvořit prostředek**.

1. Vyberte **Compute** a potom vyberte **Ubuntu Server 16.04 LTS**.
   
   ![Vybrat server pro Linux](media/azure-stack-quick-linux-portal/select.png)

1. Vyberte **Vytvořit**.

1. Zadejte informace o virtuálním počítači. Jako **typ ověřování**vyberte **veřejný klíč SSH**, vložte veřejný klíč SSH, který jste uložili, a pak vyberte **OK**.

   > [!NOTE]
   > Ujistěte se, že jste pro tento klíč odebrali všechny úvodní a koncové prázdné znaky.

   ![Panel základy – konfigurace virtuálního počítače](media/azure-stack-quick-linux-portal/linux-01.PNG)

1. Jako virtuální počítač vyberte **D1** .

   ![Podokno velikost – Volba velikosti virtuálního počítače](media/azure-stack-quick-linux-portal/linux-02.PNG)

1. Na stránce **Nastavení** proveďte změny ve výchozích hodnotách.
   
   Od centra Azure Stack verze 1808 můžete nakonfigurovat **úložiště** a vybrat možnost použití *spravovaných disků*. V dřívějších verzích než 1808 lze použít pouze nespravované disky.

   ![Konfigurace úložiště pro spravované disky](media/azure-stack-quick-linux-portal/linux-03.PNG)
    
   Až budou vaše konfigurace připravené, pokračujte výběrem **OK** .

1. Na stránce **Souhrn** vyberte **OK** a spusťte nasazení virtuálního počítače.  

   ![Nasazení](media/azure-stack-quick-linux-portal/deploy.png)

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

1. Na stránce virtuální počítač vyberte **připojit** . Můžete najít připojovací řetězec SSH, který potřebujete připojit k virtuálnímu počítači. 

1. Na stránce **Konfigurace** pro výstupy přejděte v podokně **kategorie** dolů na a rozbalte položku **SSH**a pak vyberte **auth**. 

   ![Připojit virtuální počítač](media/azure-stack-quick-linux-portal/putty03.PNG)

1. Vyberte **Procházet**a pak vyberte soubor privátního klíče, který jste uložili.

1. V podokně **kategorie** se posuňte nahoru a vyberte možnost **relace**.

1. Do pole **název hostitele (nebo IP adresa)** vložte připojovací řetězec, který je zobrazený na portálu centra Azure Stack. V tomto příkladu je řetězec *asadmin@192.168.102.34* .

1. Výběrem **otevřít** otevřete relaci pro virtuální počítač.

   ![Relace Linux](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>Instalace webového serveru NGINX

Pokud chcete aktualizovat zdroje balíčků a nainstalovat na virtuální počítač nejnovější balíček NGINX, zadejte následující příkazy bash:

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Po dokončení instalace NGINX zavřete relaci SSH a otevřete stránku **Přehled** virtuálního počítače na portálu centra Azure Stack.

## <a name="open-port-80-for-web-traffic"></a>Otevření portu 80 pro webový provoz

Skupina zabezpečení sítě (NSG) zabezpečuje příchozí a odchozí provoz. Když se na portálu centra Azure Stack vytvoří virtuální počítač, vytvoří se příchozí pravidlo na portu 22 pro připojení SSH. Vzhledem k tomu, že tento virtuální počítač je hostitelem webového serveru, je nutné vytvořit pravidlo NSG, které povoluje webový provoz na portu 80.

1. Na stránce **Přehled** virtuálních počítačů vyberte název **skupiny prostředků**.

1. Vyberte **skupinu zabezpečení sítě** pro virtuální počítač. NSG můžete identifikovat pomocí sloupce **typ** .

1. V levém podokně v části **Nastavení**vyberte **příchozí pravidla zabezpečení**.

1. Vyberte **Přidat**.

1. Do pole **název** zadejte **http**. 

1. Ujistěte se, že **Rozsah portů** je nastavený na 80 a **Akce** je nastavená na hodnotu **povoleno**.

1. Vyberte **OK**.

## <a name="view-the-welcome-to-nginx-page"></a>Zobrazit stránku Vítá vás Nginx

Když jste nainstalovali NGINX a na VIRTUÁLNÍm počítači jste otevřeli port 80, můžete k webovému serveru přistupovat pomocí veřejné IP adresy virtuálního počítače. (Veřejná IP adresa se zobrazí na stránce **Přehled** virtuálního počítače.)

Otevřete webový prohlížeč a na *> http://\<veřejné IP adresy*.

![Úvodní stránka webového serveru NGINX](media/azure-stack-quick-linux-portal/linux-05.PNG)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte prostředky, které už nepotřebujete. Pokud chcete virtuální počítač a jeho prostředky odstranit, vyberte skupinu prostředků na stránce virtuální počítač a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili základní virtuální počítač s Linux serverem s webovým serverem. Další informace o Azure Stackch virtuálních počítačů centra najdete [v informacích o virtuálních počítačích v centru Azure Stack](azure-stack-vm-considerations.md).
