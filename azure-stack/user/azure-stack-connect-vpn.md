---
title: Připojení centra Azure Stack k Azure pomocí sítě VPN | Microsoft Docs
description: Postup připojení virtuálních sítí ve službě Azure Stack hub k virtuálním sítím v Azure pomocí sítě VPN.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 10/24/2018
ms.openlocfilehash: 75d6143d3ae1ce0eaf114bd537e0a4983f99e53c
ms.sourcegitcommit: ecb541f53255c6a4433724ad2d20fb93c4720ce1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76706894"
---
# <a name="connect-azure-stack-hub-to-azure-using-vpn"></a>Připojení centra Azure Stack k Azure pomocí sítě VPN

Tento článek popisuje, jak vytvořit síť VPN typu Site-to-site pro připojení virtuální sítě v Azure Stackovém centru k virtuální síti v Azure.

## <a name="before-you-begin"></a>Než začnete

Pokud chcete dokončit konfiguraci připojení, ujistěte se, že máte následující položky, než začnete:

* Nasazení integrovaných systémů Azure Stack hub (více uzlů), které jsou přímo připojené k Internetu. Váš externí veřejný rozsah IP adres musí být přímo dosažitelný z veřejného Internetu.
* Platné předplatné Azure. Pokud nemáte předplatné Azure, můžete [si tady vytvořit bezplatný účet Azure](https://azure.microsoft.com/free/?b=17.06).

### <a name="vpn-connection-diagram"></a>Diagram připojení k síti VPN

Následující obrázek ukazuje, co by konfigurace připojení měla vypadat, jako když jste hotovi:

![Konfigurace připojení VPN typu Site-to-site](media/azure-stack-connect-vpn/image2.png)

### <a name="network-configuration-example-values"></a>Příklady hodnot konfigurace sítě

Tabulka příklady konfigurace sítě obsahuje hodnoty, které se používají v příkladech v tomto článku. Tyto hodnoty můžete použít, nebo je můžete vykázat, abyste lépe porozuměli příkladům v tomto článku:

|   |Azure Stack Hub|Azure|
|---------|---------|---------|
|Název virtuální sítě     |Azs-VNet|AzureVNet |
|Adresní prostor virtuální sítě |10.1.0.0/16|10.100.0.0/16|
|Název podsítě     |FrontEnd|FrontEnd|
|Rozsah adres podsítě|10.1.0.0/24 |10.100.0.0/24 |
|Podsíť brány     |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>Vytvoření síťových prostředků v Azure

Nejdřív vytvořte síťové prostředky pro Azure. Následující pokyny ukazují, jak vytvořit prostředky pomocí [Azure Portal](https://portal.azure.com/).

### <a name="create-the-virtual-network-and-virtual-machine-vm-subnet"></a>Vytvoření virtuální sítě a podsítě virtuálních počítačů

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí svého účtu Azure.

2. Na portálu User Portal vyberte **+ vytvořit prostředek**.
3. Otevřete **web Marketplace**a pak vyberte **sítě**.
4. Vyberte **Virtuální síť**.
5. Pomocí informací z tabulky konfigurace sítě Identifikujte hodnoty pro **název**Azure, **adresní prostor**, **název podsítě**a **Rozsah adres podsítě**.
6. V případě **skupiny prostředků**vytvořte novou skupinu prostředků, nebo pokud ji už máte, vyberte **použít existující**.
7. Vyberte **umístění** vaší virtuální sítě.  Pokud používáte ukázkové hodnoty, vyberte **východní USA** nebo použijte jiné umístění.
8. Zaškrtněte **Připnout na řídicí panel**.
9. Vyberte **Create** (Vytvořit).

### <a name="create-the-gateway-subnet"></a>Vytvoření podsítě brány

1. Z řídicího panelu otevřete prostředek virtuální sítě, který jste vytvořili (**AzureVNet**).
2. V části **Nastavení** vyberte **podsítě**.
3. Výběrem **podsítě brány** přidejte podsíť brány do virtuální sítě.
4. Ve výchozím nastavení je název této podsítě nastavený na **GatewaySubnet**.

   >[!IMPORTANT]
   >Podsítě brány jsou speciální a musí mít tento konkrétní název, aby fungovaly správně.

5. V poli **Rozsah adres** ověřte, že je adresa **10.100.1.0/24**.
6. Vyberte **OK** a vytvořte podsíť brány.

### <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě

1. Na webu Azure Portal vyberte **+Vytvořit prostředek**.

2. Otevřete **web Marketplace**a pak vyberte **sítě**.
3. V seznamu síťových prostředků vyberte možnost **Brána virtuální sítě**.
4. Do pole **název** zadejte **Azure-GS**.
5. Chcete-li zvolit virtuální síť, vyberte možnost **virtuální síť**. Pak ze seznamu vyberte **AzureVnet** .
6. Vyberte **Veřejná IP adresa**. Po otevření oddílu **zvolit veřejnou IP adresu** vyberte **vytvořit novou**.
7. Do pole **název** zadejte **Azure-GS-PIP**a pak vyberte **OK**.
8. Ověřte, že nastavení **Předplatné** a **Umístění** jsou správná. Prostředek můžete připnout na řídicí panel. Vyberte **Create** (Vytvořit).

### <a name="create-the-local-network-gateway-resource"></a>Vytvoření prostředku brány místní sítě

1. Na webu Azure Portal vyberte **+Vytvořit prostředek**.

2. Otevřete **web Marketplace**a pak vyberte **sítě**.
3. V seznamu prostředků vyberte **Brána místní sítě**.
4. Do pole **název** zadejte **AZS-GS**.
5. Do pole **IP adresa** zadejte veřejnou IP adresu svého centra Azure Stack Virtual Network bránu, která je uvedena dříve v tabulce konfigurace sítě.
6. Do pole **adresní prostor** z centra Azure Stack zadejte **10.1.0.0/24** a **10.1.1.0/24** adresního prostoru pro **AzureVNet**.
7. Ověřte, jestli je vaše **předplatné**, **Skupina prostředků**a **umístění** správné, a pak vyberte **vytvořit**.

## <a name="create-the-connection"></a>Vytvoření připojení

1. Na portálu User Portal vyberte **+ vytvořit prostředek**.
2. Otevřete **web Marketplace**a pak vyberte **sítě**.
3. V seznamu prostředků vyberte možnost **připojení**.
4. V části **základní** nastavení pro **Typ připojení**vyberte možnost **site-to-Site (IPSec)** .
5. Vyberte **předplatné**, **skupinu prostředků**a **umístění**a pak vyberte **OK**.
6. V části **Nastavení** vyberte **Brána virtuální sítě**a potom vyberte **Azure-GS**.
7. Vyberte **Brána místní sítě**a pak vyberte **AZS-GS**.
8. Do **název připojení**zadejte **Azure-AZS**.
9. Do **sdíleného klíče (PSK)** zadejte **12345**a pak vyberte **OK**.

   >[!NOTE]
   >Pokud pro sdílený klíč použijete jinou hodnotu, mějte na paměti, že se musí shodovat s hodnotou pro sdílený klíč, který vytvoříte na druhém konci připojení.

10. Projděte si část **Souhrn** a pak vyberte **OK**.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Nyní vytvořte virtuální počítač v Azure a umístěte ho do podsítě virtuálních počítačů ve vaší virtuální síti.

1. Na webu Azure Portal vyberte **+Vytvořit prostředek**.
2. Přejít na **web Marketplace**a pak vybrat **COMPUTE**.
3. V seznamu imagí virtuálních počítačů vyberte Image **Windows Server 2016 Datacenter Eval** .
4. V části **základy** zadejte do pole **název** **AzureVM**.
5. Zadejte platné uživatelské jméno a heslo. Tento účet se používá pro přihlášení k virtuálnímu počítači po jeho vytvoření.
6. Zadejte **předplatné**, **skupinu prostředků**a **umístění**a pak vyberte **OK**.
7. V části **Velikost** vyberte velikost virtuálního počítače pro tuto instanci a pak vyberte **Vybrat**.
8. V části **Nastavení** můžete použít výchozí nastavení. Než vyberete **OK**, potvrďte, že:

   * Je vybraná virtuální síť **AzureVnet** .
   * Podsíť je nastavená na **10.100.0.0/24**.

   Vyberte **OK**.

9. Zkontrolujte nastavení v části **Souhrn** a pak vyberte **OK**.

## <a name="create-the-network-resources-in-azure-stack-hub"></a>Vytvoření síťových prostředků v centru Azure Stack

Dále vytvořte síťové prostředky v centru Azure Stack.

### <a name="sign-in-as-a-user"></a>Přihlaste se jako uživatel.

Správce služeb se může přihlásit jako uživatel, aby otestoval plány, nabídky a odběry, které mohou uživatelé používat. Pokud ho ještě nemáte, vytvořte si [uživatelský účet](../operator/azure-stack-add-new-user-aad.md) před tím, než se přihlásíte.

### <a name="create-the-virtual-network-and-a-vm-subnet"></a>Vytvoření virtuální sítě a podsítě virtuálních počítačů

1. K přihlášení k portálu User Portal použijte uživatelský účet.
2. Na portálu User Portal vyberte **+ vytvořit prostředek**.

    ![Vytvořit novou virtuální síť](media/azure-stack-connect-vpn/image3.png)

3. Otevřete **web Marketplace**a pak vyberte **sítě**.
4. Vyberte **Virtuální síť**.
5. V poli **název**, **adresní prostor**, **název podsítě**a **Rozsah adres podsítě**použijte hodnoty z tabulky konfigurace sítě.
6. V **předplatném**se zobrazí předplatné, které jste vytvořili dříve.
7. V případě **skupiny prostředků**můžete buď vytvořit skupinu prostředků, nebo pokud ji už máte, vyberte **použít existující**.
8. Ověřte výchozí umístění.
9. Zaškrtněte **Připnout na řídicí panel**.
10. Vyberte **Create** (Vytvořit).

### <a name="create-the-gateway-subnet"></a>Vytvoření podsítě brány

1. Na řídicím panelu otevřete prostředek virtuální síťové sítě AZS-VNet, který jste vytvořili.
2. V části **Nastavení** vyberte **podsítě**.
3. Chcete-li přidat podsíť brány do virtuální sítě, vyberte možnost **podsíť brány**.

    ![Přidat podsíť brány](media/azure-stack-connect-vpn/image4.png)

4. Ve výchozím nastavení je název podsítě nastavený na **GatewaySubnet**. Aby podsítě brány fungovaly správně, musí používat název **GatewaySubnet** .
5. V poli **Rozsah adres**ověřte, že je adresa **10.1.1.0/24**.
6. Vyberte **OK** a vytvořte podsíť brány.

### <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě

1. Na portálu centra Azure Stack vyberte **+ vytvořit prostředek**.
2. Otevřete **web Marketplace**a pak vyberte **sítě**.
3. V seznamu síťových prostředků vyberte možnost **Brána virtuální sítě**.
4. Do **název**zadejte **AZS-GS**.
5. Vyberte položku **virtuální síť** a zvolte virtuální síť. V seznamu vyberte **AZS-VNet** .
6. Vyberte položku nabídky **veřejné IP adresy** . Po otevření oddílu **zvolit veřejnou IP adresu** vyberte **vytvořit novou**.
7. Do **název**zadejte **AZS-GS-PIP**a pak vyberte **OK**.
8. Ve výchozím nastavení je pro **typ sítě VPN**vybraná možnost **Směrování** . Zachovejte typ sítě VPN **založený na trasách** .

9. Ověřte, že nastavení **Předplatné** a **Umístění** jsou správná. Prostředek můžete připnout na řídicí panel. Vyberte **Create** (Vytvořit).

### <a name="create-the-local-network-gateway"></a>Vytvoření brány místní sítě

Koncept *brány místní sítě* v centru Azure Stack je trochu jiný než v nasazení Azure.

V nasazení Azure představuje brána místní sítě místně (v umístění uživatele) fyzické zařízení, které se připojujete k bráně virtuální sítě v Azure. V Azure Stack hub na obou koncích připojení ale jsou brány virtuální sítě.

Obecnější popis je, že prostředek brány místní sítě vždycky na druhém konci připojení indikuje vzdálenou bránu.

### <a name="create-the-local-network-gateway-resource"></a>Vytvoření prostředku brány místní sítě

1. Přihlaste se k portálu centra Azure Stack.
2. Na portálu User Portal vyberte **+ vytvořit prostředek**.
3. Otevřete **web Marketplace**a pak vyberte **sítě**.
4. V seznamu prostředků vyberte **Brána místní sítě**.
5. Do pole **název** zadejte **Azure-GS**.
6. Do pole **IP adresa** zadejte veřejnou IP adresu pro bránu virtuální sítě v Azure **Azure-GS-PIP**. Tato adresa se zobrazí výše v tabulce konfigurace sítě.
7. Do pole **adresní prostor** zadejte v adresním prostoru virtuální sítě Azure, kterou jste vytvořili, typ **10.100.0.0/24** a **10.100.1.0/24**.

8. Ověřte, že vaše **předplatné**, **Skupina prostředků**a hodnoty **umístění** jsou správné, a pak vyberte **vytvořit**.

### <a name="create-the-connection"></a>Vytvoření připojení

1. Na portálu User Portal vyberte **+ vytvořit prostředek**.
2. Otevřete **web Marketplace**a pak vyberte **sítě**.
3. V seznamu prostředků vyberte možnost **připojení**.
4. V části **základní** nastavení pro **Typ připojení**vyberte **site-to-Site (IPSec)** .
5. Vyberte **předplatné**, **skupinu prostředků**a **umístění**a pak vyberte **OK**.
6. V části **Nastavení** vyberte **Brána virtuální sítě**a pak vyberte **AZS-GS**.
7. Vyberte **Brána místní sítě**a pak vyberte **Azure-GS**.
8. Do **název připojení**zadejte **AZS-Azure**.
9. Do **sdíleného klíče (PSK)** zadejte **12345**a pak vyberte **OK**.

10. V části **Souhrn** vyberte **OK**.

### <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Pokud chcete ověřit připojení k síti VPN, vytvořte dva virtuální počítače: jeden v Azure a druhý v Azure Stack hub. Po vytvoření těchto virtuálních počítačů je můžete použít k posílání a přijímání dat prostřednictvím tunelového připojení VPN.

1. Na webu Azure Portal vyberte **+Vytvořit prostředek**.
2. Přejít na **web Marketplace**a pak vybrat **COMPUTE**.
3. V seznamu imagí virtuálních počítačů vyberte Image **Windows Server 2016 Datacenter Eval** .
4. V části **základy** do pole **název**zadejte **AZS-VM**.
5. Zadejte platné uživatelské jméno a heslo. Tento účet se používá pro přihlášení k virtuálnímu počítači po jeho vytvoření.
6. Zadejte **předplatné**, **skupinu prostředků**a **umístění**a pak vyberte **OK**.
7. V části **Velikost** pro tuto instanci vyberte velikost virtuálního počítače a pak vyberte **Vybrat**.
8. V části **Nastavení** přijměte výchozí hodnoty. Ujistěte se, že je vybraná virtuální síť **AZS-VNet** . Ověřte, že je podsíť nastavená na **10.1.0.0/24**. Pak vyberte **OK**.

9. V části **Souhrn** zkontrolujte nastavení a pak vyberte **OK**.

## <a name="test-the-connection"></a>Otestování připojení

Po navázání připojení Site-to-site byste měli ověřit, že můžete v obou směrech získat tok dat. Nejjednodušší způsob, jak otestovat připojení, je provést test pomocí testu pro testování:

* Přihlaste se k virtuálnímu počítači, který jste vytvořili v centru Azure Stack a otestujte virtuální počítač v Azure.
* Přihlaste se k virtuálnímu počítači, který jste vytvořili v Azure, a otestujte virtuální počítač v Azure Stackovém centru.

>[!NOTE]
>Abyste se ujistili, že odesíláte přenosy přes připojení typu Site-to-site, otestujte adresu IP (DIP) virtuálního počítače ve vzdálené podsíti, nikoli VIP.

### <a name="sign-in-to-the-user-vm-in-azure-stack-hub"></a>Přihlaste se k virtuálnímu počítači uživatele v centru Azure Stack.

1. Přihlaste se k portálu centra Azure Stack.
2. V levém navigačním panelu vyberte **Virtual Machines**.
3. V seznamu virtuálních počítačů Najděte **AZS-VM** , který jste vytvořili dříve, a pak ho vyberte.
4. V části pro virtuální počítač vyberte **připojit**a pak otevřete soubor AZS-VM. RDP.

     ![Tlačítko Připojit](media/azure-stack-connect-vpn/image17.png)

5. Přihlaste se pomocí účtu, který jste nakonfigurovali při vytváření virtuálního počítače.
6. Otevřete příkazový řádek Windows PowerShellu se zvýšenými oprávněními.
7. Zadejte **ipconfig /all**.
8. Ve výstupu Najděte **adresu IPv4**a pak adresu uložte pro pozdější použití. Jedná se o adresu, kterou otestujete z Azure. V tomto ukázkovém prostředí je adresa **10.1.0.4**, ale ve vašem prostředí se může lišit. Měl by spadat do podsítě **10.1.0.0/24** , kterou jste předtím vytvořili.
9. Pokud chcete vytvořit pravidlo brány firewall, které umožní, aby virtuální počítač reagoval na příkazy, spusťte následující příkaz PowerShellu:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>Přihlaste se k virtuálnímu počítači tenanta v Azure.

1. Přihlaste se k portálu Azure.
2. V levém navigačním panelu vyberte **Virtual Machines**.
3. V seznamu virtuálních počítačů Najděte **Azure-VM** , které jste vytvořili dříve, a pak ho vyberte.
4. V části pro virtuální počítač vyberte **připojit**.
5. Přihlaste se pomocí účtu, který jste nakonfigurovali při vytváření virtuálního počítače.
6. Otevřete okno **Windows PowerShellu** se zvýšenými oprávněními.
7. Zadejte **ipconfig /all**.
8. Měla by se zobrazit adresa IPv4, která spadá do **10.100.0.0/24**. V ukázkovém prostředí je adresa **10.100.0.4**, ale vaše adresa může být odlišná.
9. Pokud chcete vytvořit pravidlo brány firewall, které umožní, aby virtuální počítač reagoval na příkazy, spusťte následující příkaz PowerShellu:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

10. Z virtuálního počítače v Azure pomocí tunelového propojení otestujte virtuální počítač v Azure Stackovém centru. Uděláte to tak, že otestujete adresu DIP, kterou jste si poznamenali v AZS-VM. V tomto ukázkovém prostředí se jedná o **10.1.0.4**, ale ujistěte se, že jste ověřili adresu, kterou jste si poznamenali v testovacím prostředí. Měl by se zobrazit výsledek, který vypadá jako na následujícím snímku obrazovky:

    ![Úspěšný test příkazů](media/azure-stack-connect-vpn/image19b.png)

11. Odpověď od vzdáleného virtuálního počítače indikuje úspěšný test. Okno virtuálního počítače můžete zavřít.

Měli byste taky dělat přísnější testování přenosu dat (například kopírování souborů s různou velikostí v obou směrech).

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Zobrazení statistiky přenosu dat prostřednictvím připojení brány

Pokud chcete zjistit, kolik dat prochází přes připojení mezi lokalitami, jsou tyto informace k dispozici v části **připojení** . Tento test je taky dalším způsobem, jak ověřit, že jste právě odeslali testovací test pomocí připojení VPN.

1. Když jste se přihlásili k virtuálnímu počítači uživatele v centru Azure Stack, přihlaste se k portálu User Portal pomocí svého uživatelského účtu.
2. Přejít na **všechny prostředky**a pak vyberte připojení **AZS-Azure** . Zobrazí se **připojení** .
3. V části **připojení** se zobrazí statistiky pro **data v** nástroji a **data ze** seznamu. Na následujícím snímku obrazovky se ve velkých číslech přinášejí další přenos souborů. V tuto chvíli byste měli vidět nenulové hodnoty.

    ![U dat a ven](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>Další kroky

* [Nasazení aplikací do Azure a centra Azure Stack](azure-stack-solution-pipeline.md)
