---
title: Navázání připojení virtuální sítě z centra Azure Stack k virtuální síti pomocí Fortinet FortiGate síťové virtuální zařízení
description: Naučte se navázat připojení VNET-to-VNET v Azure Stack hub pomocí Fortinet FortiGate síťové virtuální zařízení
author: mattbriggs
ms.topic: how-to
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: d8c20d3fe7b80a7ace90422a622c4f067f631954
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94547105"
---
# <a name="vnet-to-vnet-connectivity-between-azure-stack-hub-instances-with-fortinet-fortigate-nva"></a>Připojení virtuální sítě k virtuální síti mezi Azure Stackmi instancemi centra pomocí Fortinet FortiGate síťové virtuální zařízení

V tomto článku připojíte virtuální síť v jednom Azure Stackovém centru k virtuální síti v jiném centru Azure Stack pomocí Fortinet FortiGate síťové virtuální zařízení, síťového virtuálního zařízení.

Tento článek se zabývá aktuálním omezením Azure Stack centra, které umožňuje klientům nastavit jenom jedno připojení k síti VPN napříč dvěma prostředími. Uživatelé se dozvíte, jak nastavit vlastní bránu na virtuálním počítači se systémem Linux, který umožní více připojení VPN v různých Azure Stack hub. Postup v tomto článku nasadí dvě virtuální sítěy s FortiGate síťové virtuální zařízení v každé virtuální síti: jedno nasazení na Azure Stack hub prostředí. Také podrobně popisuje změny potřebné k nastavení sítě VPN IPSec mezi dvěma virtuální sítě. Kroky v tomto článku by se měly opakovat pro každou virtuální síť v každém centru Azure Stack. 

## <a name="prerequisites"></a>Požadavky

-  Přístup k integrovaným systémům Azure Stack hub s dostupnou kapacitou pro nasazení požadovaných požadavků na výpočetní výkon, síť a prostředky, které jsou potřebné pro toto řešení. 

    > [!NOTE]  
    > Tyto pokyny nebudou **fungovat s** Azure Stack Development Kit (ASDK) z důvodu omezení sítě v ASDK. Další informace najdete v tématu [požadavky a předpoklady pro ASDK](../asdk/asdk-deploy-considerations.md).

-  Řešení síťového virtuálního zařízení (síťové virtuální zařízení) se stáhlo a publikovalo na tržišti centra Azure Stack. SÍŤOVÉ virtuální zařízení řídí tok síťového provozu z hraniční sítě do jiných sítí nebo podsítí. Tento postup využívá [řešení Fortinet FortiGate Next-Generation brány firewall s jedním virtuálním počítačem](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

-  K aktivaci FortiGate síťové virtuální zařízení aktivujte aspoň dva dostupné FortiGate licenčních souborů. Informace o tom, jak tyto licence získat, najdete v článku knihovna dokumentů Fortinet [registrace a stažení vaší licence](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

    Tato procedura používá [nasazení Single FortiGate-VM](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment). Postup, jak připojit FortiGate síťové virtuální zařízení k virtuální síti centra Azure Stack do místní sítě, najdete v tématu.

    Další informace o tom, jak nasadit řešení FortiGate v nastavení aktivní – pasivní (HA), najdete v článku o knihovně dokumentů Fortinet v článku [ha pro FortiGate-VM v Azure](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/983245/ha-for-FortiGate-vm-on-azure).

## <a name="deployment-parameters"></a>Parametry nasazení

Následující tabulka shrnuje parametry, které se v těchto nasazeních používají pro referenci:

### <a name="deployment-one-forti1"></a>Nasazení One: Forti1

| Název instance FortiGate | Forti1 |
|-----------------------------------|---------------------------|
| Licence a verze BYOL | 6.0.3 |
| Uživatelské jméno správce FortiGate | fortiadmin |
| Název skupiny prostředků | forti1-rg1 |
| Název virtuální sítě | forti1vnet1 |
| Adresní prostor virtuální sítě | 172.16.0.0/16 * |
| Název podsítě veřejné virtuální sítě | forti1-PublicFacingSubnet |
| Předpona adresy veřejné virtuální sítě | 172.16.0.0/24 * |
| Název podsítě virtuální sítě | forti1-InsideSubnet |
| Předpona podsítě virtuální sítě | 172.16.1.0/24 * |
| Velikost virtuálního počítače pro FortiGate síťové virtuální zařízení | Standardní F2s_v2 |
| Název veřejné IP adresy | forti1-publicip1 |
| Typ veřejné IP adresy | Static |

### <a name="deployment-two-forti2"></a>Nasazení dvě: Forti2

| Název instance FortiGate | Forti2 |
|-----------------------------------|---------------------------|
| Licence a verze BYOL | 6.0.3 |
| Uživatelské jméno správce FortiGate | fortiadmin |
| Název skupiny prostředků | forti2-rg1 |
| Název virtuální sítě | forti2vnet1 |
| Adresní prostor virtuální sítě | 172.17.0.0/16 * |
| Název podsítě veřejné virtuální sítě | forti2-PublicFacingSubnet |
| Předpona adresy veřejné virtuální sítě | 172.17.0.0/24 * |
| Název podsítě virtuální sítě | Forti2-InsideSubnet |
| Předpona podsítě virtuální sítě | 172.17.1.0/24 * |
| Velikost virtuálního počítače pro FortiGate síťové virtuální zařízení | Standardní F2s_v2 |
| Název veřejné IP adresy | Forti2-publicip1 |
| Typ veřejné IP adresy | Static |

> [!NOTE]
> \* Vyberte jinou sadu adresních prostorů a prefixů podsítě, pokud se výše překrývají jakýmkoli způsobem pomocí místního síťového prostředí, včetně fondu VIP z Azure Stackového centra. Také se ujistěte, že se rozsahy adres nepřesahují mezi sebou. * *

## <a name="deploy-the-fortigate-ngfw-marketplace-items"></a>Nasazení položek webu Marketplace pro FortiGate NGFW

Opakujte tyto kroky pro prostředí Azure Stack hub. 

1. Otevřete portál Azure Stack hub User Portal. Nezapomeňte použít přihlašovací údaje, které mají alespoň práva přispěvatele k předplatnému.

1. Vyberte **vytvořit prostředek** a vyhledejte `FortiGate` .

    ![Snímek obrazovky zobrazuje jeden řádek výsledků hledání "Fortigate". Název nalezené položky je "FortiGate NGFW-Single VM Deployment (BYOL)".](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image6.png)

1. Vyberte **FORTIGATE NGFW** a vyberte **vytvořit**.

1. Dokončete **základy** pomocí parametrů z tabulky [parametrů nasazení](#deployment-parameters) .

    Formulář by měl obsahovat následující informace:

    ![Textová pole (například název instance a licence BYOL) dialogového okna základy jsou vyplněna hodnotami z tabulky nasazení.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image7.png)

1. Vyberte **OK**.

1. Zadejte podrobnosti o virtuální síti, podsítích a velikosti virtuálního počítače z [parametrů nasazení](#deployment-parameters).

    Pokud chcete používat jiné názvy a rozsahy, dbejte na to, abyste nepoužívali parametry, které budou v konfliktu s jinými prostředky virtuální sítě a FortiGate v prostředí centra Azure Stack. To platí hlavně při nastavování rozsahu IP adres virtuální sítě a rozsahů podsítí v rámci virtuální sítě. Ověřte, že se nepřekrývají s rozsahy IP adres pro jinou virtuální síť, kterou vytvoříte.

1. Vyberte **OK**.

1. Konfigurace veřejné IP adresy, která se bude používat pro FortiGate síťové virtuální zařízení:

    ![Textové pole "název veřejné IP adresy" v dialogovém okně přiřazení IP adresy zobrazuje hodnotu "forti1-publicip1" (z tabulky nasazení).](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image8.png)

1. Vyberte **OK** a pak vyberte **OK**.

1. Vyberte **Vytvořit**.

Nasazení bude trvat přibližně 10 minut. Nyní můžete opakováním kroků vytvořit další nasazení FortiGate síťové virtuální zařízení a VNET v jiném prostředí centra Azure Stack.

## <a name="configure-routes-udrs-for-each-vnet"></a>Konfigurace tras (udr) pro každou virtuální síť

Proveďte tyto kroky pro obě nasazení, forti1-RG1 a forti2-RG1.

1. Přejděte do skupiny prostředků forti1-RG1 na portálu centra Azure Stack.

    ![Toto je snímek obrazovky se seznamem prostředků ve skupině prostředků forti1-RG1.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image9.png)

2. Vyberte v prostředku forti1-forti1-InsideSubnet-Routes-xxxx.

3. V části **Nastavení** vyberte **trasy** .

    ![Snímek obrazovky ukazuje zvýrazněnou položku trasy nastavení.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image10.png)

4. Odstraňte trasu **k Internetu** .

    ![Snímek obrazovky ukazuje zvýrazněnou trasu k Internetu. Tlačítko Odstranit je k dispozici.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image11.png)

5. Vyberte **Ano**.

6. Vyberte **Přidat**.

7. Pojmenujte **trasu** `to-forti1` nebo `to-forti2` . Rozsah IP adres použijte v případě, že používáte jiný rozsah IP adres.

8. Zadejte:
    - forti1: `172.17.0.0/16`  
    - forti2: `172.16.0.0/16`  

    Rozsah IP adres použijte v případě, že používáte jiný rozsah IP adres.

9. Vyberte **virtuální zařízení** pro **typ dalšího segmentu směrování**.
    - forti1: `172.16.1.4`  
    - forti2: `172.17.0.4`  

    Rozsah IP adres použijte v případě, že používáte jiný rozsah IP adres.

    ![Dialogové okno Upravit trasu pro forti2 má textová pole s hodnotami. Předpona adresy je 172.17.0.0/16, typ dalšího segmentu směrování je virtuální zařízení a adresa dalšího směrování je 172.16.1.4.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image12.png)

10. Vyberte **Uložit**.

Opakujte postup pro každou skupinu prostředků v každé trase **InsideSubnet** .

## <a name="activate-the-fortigate-nvas-and-configure-an-ipsec-vpn-connection-on-each-nva"></a>Aktivace síťová virtuální zařízeníu FortiGate a konfigurace připojení VPN pomocí protokolu IPSec v každém síťové virtuální zařízení

 Pro aktivaci každého FortiGate síťové virtuální zařízení budete potřebovat platný soubor s licencí od Fortinet. Síťová virtuální zařízení **nebude fungovat,** dokud neaktivujete jednotlivé síťové virtuální zařízení. Další informace o tom, jak získat soubor s licencí a postup aktivace síťové virtuální zařízení, najdete v článku knihovna dokumentů Fortinet [registrace a stažení vaší licence](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

Pro každou síťové virtuální zařízení se bude muset získat dva licenční soubory – jedna.

## <a name="create-an-ipsec-vpn-between-the-two-nvas"></a>Vytvoření sítě VPN s protokolem IPSec mezi dvěma síťová virtuální zařízení

Po aktivaci síťová virtuální zařízení postupujte podle těchto kroků a vytvořte síť VPN s protokolem IPSec mezi dvěma síťová virtuální zařízení.

Následující kroky proveďte jak pro forti1 síťové virtuální zařízení, tak pro forti2 síťové virtuální zařízení:

1. Přiřazenou veřejnou IP adresu získáte tak, že přejdete na stránku Přehled virtuálního počítače fortiX:

    ![Na stránce Přehled forti1 se zobrazuje skupina prostředků, stav a tak dále.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image13.png)

1. Zkopírujte přiřazenou IP adresu, otevřete prohlížeč a vložte adresu do adresního řádku. Prohlížeč vás může zobrazit upozornění, že certifikát zabezpečení není důvěryhodný. Pokračujte i nadále.

1. Zadejte uživatelské jméno a heslo správce FortiGate, které jste zadali během nasazování.

    ![Snímek obrazovky je přihlašovací obrazovka, která má pro uživatelské jméno a heslo tlačítko pro přihlášení a textová pole.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image14.png)

1. Vyberte **systémové**  >  **firmware**.

1. Zaškrtněte políčko, které zobrazuje nejnovější firmware, například `FortiOS v6.2.0 build0866` .

    ![Snímek obrazovky s firmwarem "FortiOS v 6.2.0 build0866" obsahuje odkaz na poznámky k verzi a dvě tlačítka: "záložní konfigurace a upgrade" a "upgrade".](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image15.png)

1. Po zobrazení výzvy vyberte možnost **Konfigurace zálohování a upgradovat** a pokračovat.

1. SÍŤOVÉ virtuální zařízení aktualizuje svůj firmware na nejnovější sestavení a restartování. Tento proces trvá přibližně pět minut. Přihlaste se zpátky do webové konzoly FortiGate.

1. Klikněte **VPN** na  >  **Průvodce protokolem IPSec** sítě VPN.

1. Zadejte název sítě VPN, například `conn1` v **Průvodci vytvořením sítě VPN**.

1. Vyberte **Tento web je za překladem adres (NAT)**.

    ![Snímek obrazovky Průvodce vytvořením sítě VPN zobrazuje v prvním kroku nastavení sítě VPN. Jsou vybrány následující hodnoty: "Site to Site" pro typ šablony, "FortiGate" pro typ vzdáleného zařízení a "Tato lokalita je za překladem adres (NAT)" pro konfiguraci překladu adres (NAT).](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image16.png)

1. Vyberte **Další**.

1. Zadejte vzdálenou IP adresu místního zařízení VPN, ke kterému se budete připojovat.

1. Jako **odchozí rozhraní** vyberte **PORT1** .

1. Vyberte **předsdílený klíč** a zadejte (a zaznamenejte) předsdílený klíč. 

    > [!NOTE]  
    > Tento klíč budete potřebovat k nastavení připojení na místním zařízení VPN, to znamená, že se musí *přesně* shodovat.

    ![Snímek obrazovky Průvodce vytvořením sítě VPN se zobrazí v druhém kroku, ověřování a vybrané hodnoty budou zvýrazněny.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image17.png)

1. Vyberte **Další**.

1. Jako **místní rozhraní** vyberte **PORT2** .

1. Zadejte rozsah místní podsítě:
    - forti1:172.16.0.0/16
    - forti2:172.17.0.0/16

    Rozsah IP adres použijte v případě, že používáte jiný rozsah IP adres.

1. Zadejte odpovídající vzdálené podsítě, které reprezentují místní síť, ke které se připojíte prostřednictvím místního zařízení VPN.
    - forti1:172.16.0.0/16
    - forti2:172.17.0.0/16

    Rozsah IP adres použijte v případě, že používáte jiný rozsah IP adres.

    ![Snímek obrazovky Průvodce vytvořením sítě VPN ukazuje, že se jedná o třetí krok, zásady & směrování, ve kterém se zobrazují vybrané a zadané hodnoty.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image18.png)

1. Vyberte **Vytvořit**.

1. Vyberte **Síťová**  >  **rozhraní**.  

    ![Seznam rozhraní zobrazuje dvě rozhraní: PORT1, která byla nakonfigurována a Port2, což ještě není. K dispozici jsou tlačítka pro vytváření, úpravy a odstraňování rozhraní.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image19.png)

1. Dvakrát klikněte na **PORT2**.

1. V seznamu **role** vyberte **místní síť** a **DHCP** pro režim adresování.

1. Vyberte **OK**.

Opakujte postup pro ostatní síťové virtuální zařízení.


## <a name="bring-up-all-phase-2-selectors"></a>Vyvolat všechny selektory fáze 2 

Po výše uvedeném případě se u **obou** síťová virtuální zařízení dokončilo:

1.  Na webové konzole forti2 Fortigate vyberte možnost **monitorování**  >  **protokolu IPSec**. 

    ![Zobrazí se monitorování pro conn1 připojení VPN. Zobrazuje se jako nefunkční, stejně jako odpovídající selektor fáze 2.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image20.png)

2.  Zvýrazněte `conn1` a vyberte **Bring Up**  >  **možnost zahrnout všechny selektory fáze 2**.

    ![Sestavování monitorování a fáze 2 se zobrazuje současně.](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image21.png)


## <a name="test-and-validate-connectivity"></a>Testování a ověření připojení

Nyní byste měli být schopni směrovat mezi každou virtuální sítí přes FortiGate síťová virtuální zařízení. Pokud chcete připojení ověřit, vytvořte virtuální počítač centra Azure Stack v každé virtuální síti InsideSubnet. Vytvoření virtuálního počítače centra Azure Stack se dá udělat prostřednictvím portálu, rozhraní příkazového řádku nebo PowerShellu. Při vytváření virtuálních počítačů:

-   Virtuální počítače centra Azure Stack jsou umístěné na **InsideSubnet** každé virtuální sítě.

-   Při vytváření virtuálního počítače **nepoužíváte** žádné skupin zabezpečení sítě (to znamená, že při vytváření virtuálního počítače z portálu odeberete NSG, které se ve výchozím nastavení přidá.

-   Ujistěte se, že pravidla brány firewall virtuálních počítačů umožňují komunikaci, kterou budete používat k testování připojení. Pro účely testování se doporučuje vypnout bránu firewall zcela v operačním systému, pokud je to možné.

## <a name="next-steps"></a>Další kroky

[Rozdíly a požadavky pro sítě Azure Stack hub](azure-stack-network-differences.md)  
[Nabídka síťového řešení v centru Azure Stack s FortiGate Fortinet](../operator/azure-stack-network-solutions-enable.md)  
