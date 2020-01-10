---
title: Vytvoření připojení VPN typu Site-to-site mezi dvěma virtuálními sítěmi v různých prostředích ASDK | Microsoft Docs
description: Kurz pro operátory cloudu pro vytvoření připojení VPN typu Site-to-site mezi dvěma prostředími ASDK (Single-Node Azure Stack Development Kit).
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: justinha
ms.reviewer: tbd
ms.lastreviewed: 09/12/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 81e6e51c602909421e40b4c1e1d5e6ec796f7839
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75817903"
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-asdk-environments"></a>Vytvoření připojení VPN typu Site-to-site mezi dvěma virtuálními sítěmi v různých prostředích ASDK

## <a name="overview"></a>Přehled

Tento článek popisuje, jak vytvořit připojení VPN typu Site-to-site mezi dvěma virtuálními sítěmi ve dvou samostatných Azure Stack Development Kit (ASDK) prostředí. Když nakonfigurujete připojení, zjistíte, jak fungují brány VPN ve službě Azure Stack hub.

### <a name="connection"></a>Připojení

Následující obrázek ukazuje, jak by konfigurace připojení měla vypadat, jako když jste hotovi.

![Konfigurace připojení VPN typu Site-to-site](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>Než začnete

Pokud chcete dokončit konfiguraci připojení, ujistěte se, že máte následující položky, než začnete:

* Dva servery a další požadavky, které splňují požadavky na hardware ASDK, jak je popsáno v tématu [rychlý Start: vyhodnocení Azure Stack Development Kit](../asdk/asdk-download.md).
* Balíček pro nasazení [ASDK](https://azure.microsoft.com/overview/azure-stack/try/) .

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Nasazení Azure Stack Development Kit prostředí

Pokud chcete dokončit konfiguraci připojení, musíte nasadit dvě prostředí ASDK.

> [!NOTE]
> Pro každý ASDK, který nasadíte, postupujte podle [pokynů pro nasazení](../asdk/asdk-install.md). V tomto článku se ASDK prostředí nazývají **POC1** a **POC2**.

## <a name="prepare-an-offer-on-poc1-and-poc2"></a>Příprava nabídky na POC1 a POC2

V POC1 i POC2 Připravte nabídku tak, aby se uživatel mohl přihlásit k odběru nabídky a nasazovat virtuální počítače (VM). Informace o tom, jak vytvořit nabídku, najdete v článku [zpřístupnění virtuálních počítačů pro uživatele centra Azure Stack](azure-stack-tutorial-tenant-vm.md).

## <a name="review-and-complete-the-network-configuration-table"></a>Kontrola a dokončení tabulky konfigurace sítě

Následující tabulka shrnuje konfiguraci sítě pro prostředí ASDK. Použijte postup, který se zobrazí za tabulkou, a přidejte externí adresu BGPNAT, která je specifická pro vaši síť.

### <a name="network-configuration-table"></a>Tabulka konfigurace sítě

|   |POC1|POC2|
|---------|---------|---------|
|Název virtuální sítě     |VNET-01|VNET-02 |
|Adresní prostor virtuální sítě |10.0.10.0/23|10.0.20.0/23|
|Název podsítě     |Subnet-01|Subnet-02|
|Rozsah adres podsítě|10.0.10.0/24 |10.0.20.0/24 |
|Podsíť brány     |10.0.11.0/24|10.0.21.0/24|
|Externí BGPNAT adresa     |         |         |

> [!NOTE]
> Externí IP adresy BGPNAT v tomto ukázkovém prostředí jsou 10.16.167.195 pro POC1 a 10.16.169.131 pro POC2. Pomocí následujícího postupu určete externí IP adresy BGPNAT pro hostitele ASDK a pak je přidejte do předchozí tabulky konfigurace sítě.

### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Získání IP adresy externího adaptéru virtuálního počítače pro překlad adres

1. Přihlaste se k fyzickému počítači centra Azure Stack pro POC1.
2. Upravte následující kód prostředí PowerShell a přidejte heslo správce a potom spusťte kód na hostiteli pro ověření koncepce:

   ```powershell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "AzS-bgpnat01" `
    -Password $Password
   ```

3. Přidejte IP adresu do tabulky konfigurace sítě, která se zobrazí v předchozí části.

4. Tento postup opakujte na POC2.

## <a name="create-the-network-resources-in-poc1"></a>Vytvoření síťových prostředků v POC1

Nyní můžete vytvořit POC1 síťové prostředky, které potřebujete k nastavení bran. Následující pokyny popisují, jak vytvořit prostředky pomocí uživatelského portálu Azure Stack hub. K vytvoření prostředků můžete také použít kód prostředí PowerShell.

![Pracovní postup vytvoření prostředků](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>Přihlaste se jako tenant.

Správce služeb se může přihlásit jako tenant, aby otestoval plány, nabídky a odběry, které můžou klienti používat. Pokud ho ještě nemáte, vytvořte si [účet tenanta](azure-stack-add-new-user-aad.md) a teprve potom se přihlaste.

### <a name="create-the-virtual-network-and-vm-subnet"></a>Vytvoření virtuální sítě a podsítě virtuálních počítačů

1. Pomocí účtu tenanta se přihlaste k portálu User Portal.
2. Na portálu User Portal vyberte **+ vytvořit prostředek**.
3. Otevřete **web Marketplace**a pak vyberte **sítě**.
4. Vyberte **Virtuální síť**.
5. V poli **název**, **adresní prostor**, **název podsítě**a **Rozsah adres podsítě**použijte hodnoty, které se objeví dříve v tabulce konfigurace sítě.
6. V **předplatném**se zobrazí předplatné, které jste vytvořili dříve.
7. V případě **skupiny prostředků**můžete buď vytvořit skupinu prostředků, nebo pokud ji už máte, vyberte **použít existující**.
8. Ověřte výchozí umístění.
9. Zaškrtněte **Připnout na řídicí panel**.
10. Vyberte **Vytvořit**.

### <a name="create-the-gateway-subnet"></a>Vytvoření podsítě brány

1. Na řídicím panelu otevřete prostředek virtuální síťové sítě VNET-01, který jste vytvořili dříve.
2. V okně **Nastavení** vyberte **Podsítě**.
3. Chcete-li přidat podsíť brány do virtuální sítě, vyberte možnost **podsíť brány**.

    ![Přidat podsíť brány](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Ve výchozím nastavení je název podsítě nastavený na **GatewaySubnet**. Podsítě brány jsou speciální. Aby fungovala správně, musí používat název **GatewaySubnet** .
5. V poli **Rozsah adres**ověřte, že je adresa **10.0.11.0/24**.
6. Vyberte **OK** a vytvořte podsíť brány.

### <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě

1. Na webu Azure Portal vyberte **+Vytvořit prostředek**.
2. Otevřete **web Marketplace**a pak vyberte **sítě**.
3. V seznamu síťových prostředků vyberte možnost **Brána virtuální sítě**.
4. Do **název**zadejte **gw1**.
5. Vyberte položku **virtuální síť** a zvolte virtuální síť. Ze seznamu vyberte **VNet-01** .
6. Vyberte položku nabídky **veřejné IP adresy** . Po otevření okna **zvolit veřejnou IP adresu** vyberte **vytvořit novou**.
7. Do **název**zadejte **gw1-PIP**a pak vyberte **OK**.
8. Ve výchozím nastavení je vybrána možnost **typ sítě VPN**, který je **založený na trasách** . Zachovejte typ sítě VPN **založený na trasách** .
9. Ověřte, že nastavení **Předplatné** a **Umístění** jsou správná. Prostředek můžete připnout na řídicí panel. Vyberte **Vytvořit**.

### <a name="create-the-local-network-gateway"></a>Vytvoření brány místní sítě

Implementace *brány místní sítě* v tomto nasazení vyzkoušení centra Azure Stack se trochu liší od skutečného nasazení Azure.

V nasazení Azure představuje brána místní sítě místně (u tenanta) fyzické zařízení, které používáte pro připojení k bráně virtuální sítě v Azure. V tomto nasazení vyzkoušení centra Azure Stack oba konce připojení jsou brány virtuální sítě.

K této obecnější úvahě se můžete představit tak, že prostředek brány místní sítě vždycky na druhém konci připojení indikuje vzdálenou bránu. Vzhledem k tomu, jak je ASDK navržený, musíte zadat IP adresu externího síťového adaptéru na virtuálním počítači překladu síťových adres (NAT) druhého ASDK jako veřejnou IP adresu brány místní sítě. Pak na virtuálním počítači NAT vytvoříte mapování NAT, abyste se ujistili, že oba konce budou správně připojené.

### <a name="create-the-local-network-gateway-resource"></a>Vytvoření prostředku brány místní sítě

1. Přihlaste se k fyzickému počítači centra Azure Stack pro POC1.
2. Na portálu User Portal vyberte **+ vytvořit prostředek**.
3. Otevřete **web Marketplace**a pak vyberte **sítě**.
4. V seznamu prostředků vyberte **Brána místní sítě**.
5. Do **název**zadejte **POC2-GS**.
6. Do pole **IP adresa**zadejte externí BGPNAT adresu pro POC2. Tato adresa se zobrazí výše v tabulce konfigurace sítě.
7. V **adresním prostoru**zadejte do adresního prostoru virtuální sítě POC2, kterou vytvoříte později, **10.0.20.0/23**.
8. Ověřte, že vaše **předplatné**, **Skupina prostředků**a hodnoty **umístění** jsou správné, a pak vyberte **vytvořit**.

### <a name="create-the-connection"></a>Vytvoření připojení

1. Na portálu User Portal vyberte **+ vytvořit prostředek**.
2. Otevřete **web Marketplace**a pak vyberte **sítě**.
3. V seznamu prostředků vyberte možnost **připojení**.
4. V okně **základní** nastavení pro **Typ připojení**vyberte **site-to-Site (IPSec)** .
5. Vyberte **předplatné**, **skupinu prostředků**a **umístění**a pak vyberte **OK**.
6. V okně **Nastavení** vyberte **Brána virtuální sítě**a pak vyberte **gw1**.
7. Vyberte **Brána místní sítě**a pak vyberte **POC2-GS**.
8. Do **název připojení**zadejte **POC1-POC2**.
9. Do **sdíleného klíče (PSK)** zadejte **12345**a pak vyberte **OK**.
10. V okně **Souhrn** vyberte **OK**.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

K ověření dat, která se posílají prostřednictvím připojení VPN, potřebujete virtuální počítače pro posílání a přijímání dat v jednotlivých ASDK. Vytvořte virtuální počítač v POC1 nyní a potom ve své virtuální síti ho umístěte do podsítě virtuálních počítačů:

1. Na webu Azure Portal vyberte **+Vytvořit prostředek**.
2. Přejít na **web Marketplace**a pak vybrat **COMPUTE**.
3. V seznamu imagí virtuálních počítačů vyberte Image **Windows Server 2016 Datacenter Eval** .
4. V okně **základy** do pole **název**zadejte **VM01**.
5. Zadejte platné uživatelské jméno a heslo. Tento účet se používá pro přihlášení k virtuálnímu počítači po jeho vytvoření.
6. Zadejte **předplatné**, **skupinu prostředků**a **umístění**a pak vyberte **OK**.
7. V okně **Velikost** pro tuto instanci vyberte velikost virtuálního počítače a pak vyberte **Vybrat**.
8. V okně **Nastavení** přijměte výchozí hodnoty. Ujistěte se, že je vybraná virtuální síť **VNet-01** . Ověřte, že je podsíť nastavená na **10.0.10.0/24**. Pak vyberte **OK**.
9. V okně **Souhrn** zkontrolujte nastavení a pak vyberte **OK**.

## <a name="create-the-network-resources-in-poc2"></a>Vytvoření síťových prostředků v POC2

Dalším krokem je vytvoření síťových prostředků pro POC2. Následující pokyny ukazují, jak vytvořit prostředky pomocí portálu User Portal.

### <a name="sign-in-as-a-tenant-again"></a>Přihlaste se jako tenant znovu.

Správce služeb se může přihlásit jako tenant, aby otestoval plány, nabídky a odběry, které můžou klienti používat. Pokud ho ještě nemáte, vytvořte si [účet tenanta](azure-stack-add-new-user-aad.md) a teprve potom se přihlaste.

### <a name="create-virtual-network-and-vm-subnet"></a>Vytvoření virtuální sítě a podsítě virtuálních počítačů

1. Přihlaste se pomocí účtu tenanta.
2. Na portálu User Portal vyberte **+ vytvořit prostředek**.
3. Otevřete **web Marketplace**a pak vyberte **sítě**.
4. Vyberte **Virtuální síť**.
5. Použijte informace uvedené dříve v tabulce konfigurace sítě k identifikaci hodnot pro **název**POC2, **adresní prostor**, **název podsítě**a **Rozsah adres podsítě**.
6. V **předplatném**se zobrazí předplatné, které jste vytvořili dříve.
7. V případě **skupiny prostředků**vytvořte novou skupinu prostředků, nebo pokud ji už máte, vyberte **použít existující**.
8. Ověřte výchozí **umístění**.
9. Zaškrtněte **Připnout na řídicí panel**.
10. Vyberte **Vytvořit**.

### <a name="create-gateway-subnet"></a>Vytvořit podsíť brány

1. Z řídicího panelu otevřete prostředek virtuální sítě, který jste vytvořili (**VNet-02**).
2. V okně **Nastavení** vyberte **Podsítě**.
3. Výběrem **podsítě brány** přidejte podsíť brány do virtuální sítě.
4. Ve výchozím nastavení je název této podsítě nastavený na **GatewaySubnet**. Podsítě brány jsou speciální a musí mít tento konkrétní název, aby fungovaly správně.
5. V poli **Rozsah adres** ověřte, že je adresa **10.0.21.0/24**.
6. Vyberte **OK** a vytvořte podsíť brány.

### <a name="create-virtual-network-gateway"></a>Vytvořit bránu virtuální sítě

1. Na webu Azure Portal vyberte **+Vytvořit prostředek**.  
2. Otevřete **web Marketplace**a pak vyberte **sítě**.
3. V seznamu síťových prostředků vyberte možnost **Brána virtuální sítě**.
4. Do **název**zadejte **GW2**.
5. Chcete-li zvolit virtuální síť, vyberte možnost **virtuální síť**. Pak ze seznamu vyberte **VNet-02** .
6. Vyberte **Veřejná IP adresa**. Když se otevře okno **zvolit veřejnou IP adresu** , vyberte **vytvořit nové**.
7. Do **název**zadejte **GW2-PIP**a pak vyberte **OK**.
8. Ve výchozím nastavení je pro **typ sítě VPN**vybraná možnost **Směrování** . Zachovejte typ sítě VPN **založený na trasách** .
9. Ověřte, že nastavení **Předplatné** a **Umístění** jsou správná. Prostředek můžete připnout na řídicí panel. Vyberte **Vytvořit**.

### <a name="create-local-network-gateway-resource"></a>Vytvoření prostředku brány místní sítě

1. V uživatelském portálu POC2 vyberte **+ vytvořit prostředek**.
2. Otevřete **web Marketplace**a pak vyberte **sítě**.
3. V seznamu prostředků vyberte **Brána místní sítě**.
4. Do **název**zadejte **POC1-GS**.
5. Do pole **IP adresa**zadejte externí BGPNAT adresu pro POC1, která je uvedena dříve v tabulce konfigurace sítě.
6. Do pole **adresní prostor**z POC1 zadejte adresní prostor **10.0.10.0/23** **virtuální sítě VNet-01**.
7. Ověřte, jestli je vaše **předplatné**, **Skupina prostředků**a **umístění** správné, a pak vyberte **vytvořit**.

## <a name="create-connection"></a>Vytvoření připojení

1. Na portálu User Portal vyberte **+ vytvořit prostředek**.
2. Otevřete **web Marketplace**a pak vyberte **sítě**.
3. V seznamu prostředků vyberte možnost **připojení**.
4. V okně **základní** nastavení pro **Typ připojení**vyberte možnost **site-to-Site (IPSec)** .
5. Vyberte **předplatné**, **skupinu prostředků**a **umístění**a pak vyberte **OK**.
6. V okně **Nastavení** vyberte **Brána virtuální sítě**a pak vyberte **GW2**.
7. Vyberte **Brána místní sítě**a pak vyberte **POC1-GS**.
8. Do **název připojení**zadejte **POC2-POC1**.
9. Do **sdíleného klíče (PSK)** zadejte **12345**. Pokud zvolíte jinou hodnotu, zapamatujte si, že se musí shodovat s hodnotou pro sdílený klíč, který jste vytvořili v POC1. Vyberte **OK**.
10. Zkontrolujte okno **Souhrn** a pak vyberte **OK**.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Nyní vytvořte virtuální počítač v POC2 a umístěte ho do podsítě virtuálních počítačů ve vaší virtuální síti:

1. Na webu Azure Portal vyberte **+Vytvořit prostředek**.
2. Přejít na **web Marketplace**a pak vybrat **COMPUTE**.
3. V seznamu imagí virtuálních počítačů vyberte Image **Windows Server 2016 Datacenter Eval** .
4. V okně **základy** do pole **název**zadejte **VM02**.
5. Zadejte platné uživatelské jméno a heslo. Tento účet se používá pro přihlášení k virtuálnímu počítači po jeho vytvoření.
6. Zadejte **předplatné**, **skupinu prostředků**a **umístění**a pak vyberte **OK**.
7. V okně **Velikost** vyberte velikost virtuálního počítače pro tuto instanci a pak vyberte **Vybrat**.
8. V okně **Nastavení** můžete přijmout výchozí hodnoty. Ujistěte se, že je vybraná virtuální síť **VNet-02** , a ověřte, že je podsíť nastavená na **10.0.20.0/24**. Vyberte **OK**.
9. Zkontrolujte nastavení v okně **Souhrn** a pak vyberte **OK**.

## <a name="configure-the-nat-vm-on-each-asdk-for-gateway-traversal"></a>Konfigurace virtuálního počítače NAT v každé ASDK pro průchod branou

Vzhledem k tomu, že ASDK je samostatný a izolovaný od sítě, ve které je nasazený fyzický hostitel, *externí* síť VIP, ke které jsou brány připojené, není ve skutečnosti externí. Místo toho je síť VIP skrytá za směrovačem, který provádí překlad síťových adres.

Směrovač je virtuální počítač s Windows serverem nazvaný **AzS-bgpnat01**, který spouští roli služby Směrování a vzdálený přístup (RRAS) v infrastruktuře ASDK. Na virtuálním počítači AzS-bgpnat01 musíte nakonfigurovat překlad adres (NAT), aby připojení VPN typu Site-to-site bylo možné připojit na obou koncích.

Pokud chcete nakonfigurovat připojení VPN, musíte vytvořit trasu mapy statického překladu adres (NAT), která mapuje externí rozhraní na virtuálním počítači BGPNAT na virtuální IP adresu fondu bran hraniční brány. Pro každý port v připojení VPN se vyžaduje trasa mapy statického překladu adres (NAT).

> [!NOTE]
> Tato konfigurace se vyžaduje jenom pro prostředí ASDK.

### <a name="configure-the-nat"></a>Konfigurace překladu adres (NAT)

> [!IMPORTANT]
> Tento postup je nutné provést pro prostředí ASDK.

1. Určete **interní IP adresu** , která se má použít v následujícím skriptu PowerShellu. Otevřete bránu virtuální sítě (GW1 a GW2). V okně **Přehled** uložte hodnotu pro **veřejnou IP adresu** pro pozdější použití.

   ![Interní IP adresa](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)

2. Přihlaste se k fyzickému počítači centra Azure Stack pro POC1.
3. Zkopírujte a upravte následující skript prostředí PowerShell. Pokud chcete nakonfigurovat překlad adres (NAT) na každém ASDK, spusťte skript v Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell se zvýšenými oprávněními. Do skriptu přidejte hodnoty do `External BGPNAT address` a `Internal IP address` zástupné symboly:

   ```powershell
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

4. Tento postup opakujte na POC2.

## <a name="test-the-connection"></a>Otestování připojení

Teď, když je navázáno připojení Site-to-site, byste měli ověřit, že můžete přes něj získat provoz. Pokud se chcete ověřit, přihlaste se k jednomu z virtuálních počítačů, které jste vytvořili v ASDK prostředí. Pak otestujte virtuální počítač, který jste vytvořili v jiném prostředí.

Aby bylo zajištěno, že budete odesílat data prostřednictvím připojení typu Site-to-site, ujistěte se, že otestujete adresu IP (DIP) virtuálního počítače ve vzdálené podsíti, nikoli VIP. Provedete to tak, že na druhém konci připojení vyhledáte adresu DIP. Uložte adresu pro pozdější použití.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>Přihlaste se k virtuálnímu počítači tenanta v POC1

1. Přihlaste se k fyzickému počítači centra Azure Stack pro POC1 a pak pomocí účtu tenanta se přihlaste k portálu User Portal.
2. V levém navigačním panelu vyberte **COMPUTE**.
3. V seznamu virtuálních počítačů Najděte **VM01** , který jste vytvořili dříve, a pak ho vyberte.
4. V okně pro virtuální počítač klikněte na **připojit**a pak otevřete soubor VM01. RDP.

     ![Tlačítko Připojit](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)

5. Přihlaste se pomocí účtu, který jste nakonfigurovali při vytváření virtuálního počítače.
6. Otevřete okno **Windows PowerShellu** se zvýšenými oprávněními.
7. Zadejte **ipconfig/all**.
8. Ve výstupu Najděte **adresu IPv4**a pak adresu uložte pro pozdější použití. Jedná se o adresu, kterou budete testovat pomocí nástroje POC2. V našem ukázkovém prostředí je touto adresou **10.0.10.4**, ale ve vašem prostředí tato hodnota může být jiná. Měl by spadat do podsítě **10.0.10.0/24** , kterou jste předtím vytvořili.
9. Pokud chcete vytvořit pravidlo brány firewall, které umožní, aby virtuální počítač reagoval na příkazy, spusťte následující příkaz PowerShellu:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>Přihlaste se k virtuálnímu počítači tenanta v POC2

1. Přihlaste se k fyzickému počítači centra Azure Stack pro POC2 a pak pomocí účtu tenanta se přihlaste k portálu User Portal.
2. V levém navigačním panelu klikněte na **COMPUTE**.
3. V seznamu virtuálních počítačů Najděte **VM02** , který jste vytvořili dříve, a pak ho vyberte.
4. V okně pro virtuální počítač klikněte na **připojit**.
5. Přihlaste se pomocí účtu, který jste nakonfigurovali při vytváření virtuálního počítače.
6. Otevřete okno **Windows PowerShellu** se zvýšenými oprávněními.
7. Zadejte **ipconfig/all**.
8. Zobrazí se adresa IPv4, která spadá do **10.0.20.0/24**. V ukázkovém prostředí je adresa **10.0.20.4**, ale vaše adresa může být odlišná.
9. Pokud chcete vytvořit pravidlo brány firewall, které umožní, aby virtuální počítač reagoval na příkazy, spusťte následující příkaz PowerShellu:

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

10. Z virtuálního počítače v POC2 otestujte pomocí tunelového propojení virtuální počítač v POC1. Uděláte to tak, že otestujete adresu DIP, kterou jste si poznamenali z VM01. V tomto ukázkovém prostředí se jedná o **touto adresou 10.0.10.4**, ale ujistěte se, že jste ověřili adresu, kterou jste si poznamenali v testovacím prostředí. Měl by se zobrazit výsledek, který vypadá jako v následujícím příkladu:

    ![Úspěšný test příkazů](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. Odpověď od vzdáleného virtuálního počítače indikuje úspěšný test. Okno virtuálního počítače můžete zavřít. Chcete-li otestovat připojení, můžete vyzkoušet jiné druhy přenosů dat, jako je například kopírování souborů.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Zobrazení statistiky přenosu dat prostřednictvím připojení brány

Pokud chcete zjistit, kolik dat prochází přes připojení mezi lokalitami, jsou tyto informace k dispozici v okně **připojení** . Tento test je také dalším způsobem, jak ověřit, zda právě odeslaný test testů prostřednictvím připojení k síti VPN skutečně provedl.

1. Když jste se přihlásili k virtuálnímu počítači tenanta v POC2, použijte účet tenanta pro přihlášení k portálu User Portal.
2. Přejít na **všechny prostředky**a pak vyberte připojení **POC2-POC1** . Zobrazí se **připojení** .
3. V okně **připojení** se zobrazí statistiky pro **data v** nástroji a **data** ze seznamu. Na následujícím snímku obrazovky se ve velkých číslech přinášejí další přenos souborů. V tuto chvíli byste měli vidět nenulové hodnoty.

    ![U dat a ven](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
