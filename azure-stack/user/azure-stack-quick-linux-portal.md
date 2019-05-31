---
title: Vytvoření virtuálního počítače s Linuxem serverem pomocí služby Azure Stack | Dokumentace Microsoftu
description: Vytvoření virtuálního počítače s Linuxem serverem pomocí služby Azure Stack.
services: azure-stack
cloud: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.custom: mvc
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: ce06ffbe48848a30de98025c42711e25ca9a312a
ms.sourcegitcommit: 2ee75ded704e8cfb900d9ac302d269c54a5dd9a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394441"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-the-azure-stack-portal"></a>Rychlý start: Vytvoření serveru virtuálního počítače s Linuxem pomocí portálu Azure Stack

*Platí pro: Azure Stack integrované systémy a sady Azure Stack Development Kit*

Virtuálního počítače s Ubuntu Server 16.04 LTS (VM) můžete vytvořit pomocí portálu Azure Stack. V tomto článku vytvořit a použít virtuální počítač. Tento článek také popisuje, jak do:

* Připojení k virtuálnímu počítači pomocí vzdáleného klienta.
* Nainstalujte webový server NGINX.
* Vyčištění prostředků.

> [!NOTE]  
> Bitové kopie v tomto článku jsou aktualizovány tak, aby odpovídaly změn představených ve verzi služby Azure Stack. 1808. Verze. 1808 přidává podporu pro používání *spravované disky* kromě nespravované disky. Pokud používáte starší verzi, liší se bitové kopie pro některé úlohy, jako je například výběr disku, co se zobrazí v uživatelském rozhraní.  

## <a name="prerequisites"></a>Požadavky

* Image Linuxu v Tržišti Azure Stack

   Azure Marketplace zásobníku, ve výchozím nastavení, nemá image Linuxu. Mají operátory Azure stacku, použijte image Ubuntu Server 16.04 LTS, co potřebujete. Operátor, který můžete použít pokyny v [Marketplace stažení položek z Azure do služby Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md).

* Přístup k klienta SSH

   Pokud používáte Azure Stack Development Kit (ASDK), nebudete mít přístup ke klientovi Secure Shell (SSH). Pokud budete potřebovat klienta, patří několik balíčků klienta SSH. Například obsahuje PuTTY SSH Key Generator (puttygen.exe) a služby klienta SSH. Další informace o dostupných balíčků, najdete v části [jak použít veřejný klíč SSH](azure-stack-dev-start-howto-ssh-public-key.md).

* Tento rychlý start využívá PuTTY generování klíčů SSH a pro připojení k virtuálnímu počítači Linux serverem. [Stáhněte a nainstalujte PuTTY](https://www.putty.org).

## <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH

K dokončení všech kroků v tomto článku, potřebujete pár klíčů SSH. Pokud už máte pár klíčů SSH, můžete tento krok přeskočit.

Postup vytvoření páru klíčů SSH:

1. Přejděte do složky instalace PuTTY (výchozí umístění je *C:\Program Files\PuTTY*) a spusťte:

    `puttygen.exe`

1. V **generátor klíče PuTTY** okno, nastavte **typ klíče pro generování** k **RSA**a nastavte **počet bitů v vygenerovaný klíč** k **2048**.

   ![Konfigurace puTTY Key Generator](media/azure-stack-quick-linux-portal/Putty01.PNG)

1. Vyberte **generovat**.

1. Vygenerujte klíč, v **klíč** pole, přesuňte ukazatel náhodně.

1. Po dokončení generování klíčů, vyberte **uložit veřejný klíč**a pak vyberte **uložit privátní klíč** uložení klíče do souborů.

   ![PuTTY Key Generator výsledky](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>Přihlaste se k portálu Azure Stack

Adresa na portálu Azure Stack závisí, který produkt Azure Stack se připojujete k:

* Pro ASDK, přejděte na https://portal.local.azurestack.external.

* Pro systém Azure Stack integrované přejděte na adresu URL, kterou poskytuje vaší operátory Azure stacku.

## <a name="create-the-vm"></a>Vytvořte virtuální počítač.

1. V levém horním rohu portálu Azure Stack, vyberte **vytvořit prostředek**.

1. Vyberte **Compute** a potom vyberte **Ubuntu Server 16.04 LTS**.
   
   ![Vyberte server pro Linux](media/azure-stack-quick-linux-portal/select.png)

1. Vyberte **Vytvořit**.

1. Zadejte informace o virtuálním počítači. Pro **typ ověřování**vyberte **veřejný klíč SSH**, vložit SSH veřejného klíče, který jste uložili a pak vyberte **OK**.

   > [!NOTE]
   > Ujistěte se, že odeberete všechny úvodní a koncové prázdné znaky pro klíč.

   ![Základní informace o panelu – Konfigurace virtuálního počítače](media/azure-stack-quick-linux-portal/linux-01.PNG)

1. Vyberte **D1** pro virtuální počítač.

   ![Velikost podokna – výběr velikosti virtuálního počítače](media/azure-stack-quick-linux-portal/linux-02.PNG)

1. Na **nastavení** stránce, provést změny výchozích hodnot.
   
   Počínaje verzí Azure Stack. 1808, můžete nakonfigurovat **úložiště** a zvolíte použití *spravované disky*. Ve starších verzích než. 1808 je možné jenom nespravované disky.

   ![Konfigurace úložiště pro spravované disky](media/azure-stack-quick-linux-portal/linux-03.PNG)
    
   Pokud vaše konfigurace jsou připravené, vyberte **OK** pokračujte.

1. Na **Souhrn** stránce **OK** a spusťte nasazování virtuálního počítače.  

   ![Nasazení](media/azure-stack-quick-linux-portal/deploy.png)

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

1. Vyberte **připojit** na stránce virtuálního počítače. Můžete najít připojovací řetězec SSH, které potřebujete pro připojení k virtuálnímu počítači. 

1. Na **konfigurace PuTTY** stránku, **kategorie** podokně přejděte dolů k položce a rozbalte **SSH**a pak vyberte **Auth**. 

   ![Připojení virtuálního počítače](media/azure-stack-quick-linux-portal/putty03.PNG)

1. Vyberte **Procházet**a potom vyberte soubor privátního klíče, který jste uložili.

1. V **kategorie** podokno, posuňte se až a vyberte **relace**.

1. V **název hostitele (nebo IP adresa)** vložte připojovací řetězec, který se zobrazí na portálu Azure Stack. V tomto příkladu je řetězec *asadmin@192.168.102.34* .

1. Vyberte **otevřete** otevřít relaci pro virtuální počítač.

   ![Linux relace](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>Instalace webového serveru NGINX

Pokud chcete aktualizaci zdrojů balíčku a nainstalujete nejnovější balíček NGINX na virtuálním počítači, zadejte následující příkazy bash:

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

Po dokončení instalace serveru NGINX, ukončete relaci SSH a otevření virtuálního počítače **přehled** stránky na portálu Azure Stack.

## <a name="open-port-80-for-web-traffic"></a>Otevření portu 80 pro webový provoz

Skupina zabezpečení sítě (NSG) zabezpečuje příchozí a odchozí provoz. Když je virtuální počítač vytvořený na portálu Azure Stack, se vytvoří příchozí pravidlo na portu 22 pro připojení SSH. Protože tento virtuální počítač hostitelem webového serveru, pravidlo skupiny zabezpečení sítě je potřeba vytvořit pro povolení webového provozu na portu 80.

1. Na virtuálním počítači **přehled** stránky, vyberte název **skupiny prostředků**.

1. Vyberte **skupinu zabezpečení sítě** pro virtuální počítač. NSG můžete identifikovat pomocí **typ** sloupce.

1. V levém podokně v části **nastavení**vyberte **příchozí pravidla zabezpečení**.

1. Vyberte **Přidat**.

1. V **název** zadejte **http**. 

1. Ujistěte se, že **rozsah portů** je nastavený na 80 a **akce** je nastavena na **povolit**.

1. Vyberte **OK**.

## <a name="view-the-welcome-to-nginx-page"></a>Zobrazit úvodní stránku serveru nginx

NGINX nainstalovaný a port 80, otevřete na svém virtuálním počítači můžete přístup k webovému serveru s použitím veřejné IP adresy Virtuálního počítače. (Veřejná IP adresa se zobrazí na Virtuálního počítače **přehled** stránky.)

Otevřete webový prohlížeč a přejděte na *http://\<veřejná IP adresa >* .

![Úvodní stránku serveru NGINX webového serveru](media/azure-stack-quick-linux-portal/linux-05.PNG)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte prostředky, které už nepotřebujete. Odstranění virtuálního počítače a její prostředky, vyberte skupinu prostředků, na stránce virtuální počítač a potom vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nasadili základní server Linux virtuálního počítače s webovým serverem. Další informace o virtuálních počítačích Azure Stack, [aspekty virtuálních počítačů ve službě Azure Stack](azure-stack-vm-considerations.md).
