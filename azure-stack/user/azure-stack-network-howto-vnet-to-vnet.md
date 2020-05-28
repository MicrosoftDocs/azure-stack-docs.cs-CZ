---
title: Jak propojit dvě centra Azure Stack prostřednictvím partnerského vztahu virtuální sítě
description: Přečtěte si, jak propojit dvě centra Azure Stack prostřednictvím partnerského vztahu virtuální sítě.
author: mattbriggs
ms.topic: how-to
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: 144d03f3b80e25f3db77cd6cbfba5ec7225a8496
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84111660"
---
# <a name="connect-two-vnets-through-peering"></a>Propojení dvou virtuální sítě prostřednictvím partnerského vztahu

Tento článek popisuje, jak vytvořit připojení mezi dvěma virtuálními sítěmi ve stejném prostředí. Při nastavování připojení se dozvíte, jak fungují brány VPN ve službě Azure Stack hub. Připojení dvou virtuální sítě ve stejném prostředí centra Azure Stack pomocí Fortinet FortiGate. Tento postup nasadí dvě virtuální sítě pomocí FortiGate síťové virtuální zařízení, síťového virtuálního zařízení v každé virtuální síti v rámci samostatné skupiny prostředků. Také podrobně popisuje změny potřebné k nastavení sítě VPN IPSec mezi dvěma virtuální sítě. Opakujte kroky v tomto článku pro každé nasazení virtuální sítě.

## <a name="prerequisites"></a>Požadavky

-   Přístup k systému s dostupnou kapacitou k nasazení požadovaných výpočetních, síťových a prostředků potřebných pro toto řešení.

-  Řešení síťového virtuálního zařízení (síťové virtuální zařízení) se stáhlo a publikovalo na tržišti centra Azure Stack. SÍŤOVÉ virtuální zařízení řídí tok síťového provozu z hraniční sítě do jiných sítí nebo podsítí. Tento postup využívá [řešení Fortinet FortiGate Next-Generation brány firewall s jedním virtuálním počítačem](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

-  K aktivaci FortiGate síťové virtuální zařízení aktivujte aspoň dva dostupné FortiGate licenčních souborů. Informace o tom, jak tyto licence získat, najdete v článku knihovna dokumentů Fortinet [registrace a stažení vaší licence](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

    Tato procedura používá [nasazení Single FortiGate-VM](ttps://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment). Postup, jak připojit FortiGate síťové virtuální zařízení k virtuální síti centra Azure Stack do místní sítě, najdete v tématu.

    Další informace o tom, jak nasadit řešení FortiGate v nastavení aktivní – pasivní (HA), najdete v podrobnostech v knihovně dokumentů Fortinet článku [ha pro FortiGate-VM v Azure](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/983245/ha-for-FortiGate-vm-on-azure).

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

> [!Note]
> \*Vyberte jinou sadu adresních prostorů a prefixů podsítě, pokud se výše překrývají jakýmkoli způsobem pomocí místního síťového prostředí, včetně fondu VIP z Azure Stackového centra. Také se ujistěte, že se rozsahy adres nepřesahují mezi sebou.

## <a name="deploy-the-fortigate-ngfw"></a>Nasazení FortiGate NGFW

1.  Otevřete portál Azure Stack hub User Portal.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image5.png)

2.  Vyberte **vytvořit prostředek** a vyhledejte `FortiGate` .

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image6.png)

3.  Vyberte **FORTIGATE NGFW** a vyberte **vytvořit**.

4.  Dokončete **základy** pomocí parametrů z tabulky [parametrů nasazení](#deployment-parameters) .

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image7.png)

5.  Vyberte **OK**.

6.  Zadejte podrobnosti o virtuální síti, podsítích a velikosti virtuálního počítače pomocí tabulky [parametrů nasazení](#deployment-parameters) .

    > [!Warning] 
    > Pokud se místní síť překrývá s rozsahem IP adres `172.16.0.0/16` , musíte vybrat a nastavit jiný rozsah sítě a podsítě. Pokud chcete použít jiné názvy a rozsahy než ty, které jsou v tabulce [parametrů nasazení](#deployment-parameters) , použijte parametry, které **nebudou v konfliktu s** místní sítí. Při nastavování rozsahu IP adres virtuální sítě a rozsahů podsítí v rámci virtuální sítě se postarat. Nechcete, aby se rozsah překrýval s rozsahy IP adres, které existují ve vaší místní síti.

7.  Vyberte **OK**.

8.  Nakonfigurujte veřejnou IP adresu pro Fortigate síťové virtuální zařízení:

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image8.png)

9.  Vyberte **OK**. A pak vyberte **OK**.

10.  Vyberte **Vytvořit**.

Nasazení bude trvat přibližně 10 minut.

## <a name="configure-routes-udrs-for-each-vnet"></a>Konfigurace tras (udr) pro každou virtuální síť

Proveďte tyto kroky pro obě nasazení, forti1-RG1 a forti2-RG1.

1. Otevřete portál Azure Stack hub User Portal.

2. Vyberte Skupiny prostředků. Zadejte `forti1-rg1` Filtr a dvakrát klikněte na skupinu prostředků forti1-RG1.

    ![skupina prostředků](./media/azure-stack-network-howto-vnet-to-onprem/image9.png)

2. Vyberte prostředek **forti1-forti1-InsideSubnet-Routes-xxxx** .

3. V části **Nastavení**vyberte **trasy** .

    ![Trasy](./media/azure-stack-network-howto-vnet-to-onprem/image10.png)

4. Odstraňte trasu **k Internetu** .

    ![k Internetu](./media/azure-stack-network-howto-vnet-to-onprem/image11.png)

5. Vyberte *Ano*.

6. Vyberte **Přidat** a přidejte novou trasu.

7. Pojmenujte trasu `to-onprem` .

8. Zadejte rozsah IP adres, který definuje rozsah sítě místní sítě, ke které se připojí síť VPN.

9. Vyberte **virtuální zařízení** pro **typ dalšího segmentu směrování** a `172.16.1.4` . Rozsah IP adres použijte v případě, že používáte jiný rozsah IP adres.

    ![Typ dalšího segmentu](./media/azure-stack-network-howto-vnet-to-onprem/image12.png)

10. Vyberte **Uložit**.

K aktivaci každého FortiGate síťové virtuální zařízení budete potřebovat platný soubor s licencí od Fortinet. Síťová virtuální zařízení **nebude fungovat,** dokud neaktivujete jednotlivé síťové virtuální zařízení. Další informace o tom, jak získat soubor s licencí a postup aktivace síťové virtuální zařízení, najdete v článku knihovna dokumentů Fortinet [registrace a stažení vaší licence](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

Pro každou síťové virtuální zařízení se bude muset získat dva licenční soubory – jedna.

## <a name="create-an-ipsec-vpn-between-the-two-nvas"></a>Vytvoření sítě VPN s protokolem IPSec mezi dvěma síťová virtuální zařízení

Po aktivaci síťová virtuální zařízení postupujte podle těchto kroků a vytvořte síť VPN s protokolem IPSec mezi dvěma síťová virtuální zařízení.

Následující kroky proveďte jak pro forti1 síťové virtuální zařízení, tak pro forti2 síťové virtuální zařízení:

1.  Přiřazenou veřejnou IP adresu získáte tak, že přejdete na stránku Přehled virtuálního počítače fortiX:

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image13.png)

2.  Zkopírujte přiřazenou IP adresu, otevřete prohlížeč a vložte adresu do adresního řádku. Prohlížeč vás může zobrazit upozornění, že certifikát zabezpečení není důvěryhodný. Pokračujte i nadále.

4.  Zadejte uživatelské jméno a heslo správce FortiGate, které jste zadali během nasazování.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image14.png)

5.  Vyberte **systémové**  >  **firmware**.

6.  Zaškrtněte políčko, které zobrazuje nejnovější firmware, například `FortiOS v6.2.0 build0866` .

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image15.png)

7.  Vyberte možnost **Konfigurace zálohování a**  >  **pokračovat**v upgradu.

8.  SÍŤOVÉ virtuální zařízení aktualizuje svůj firmware na nejnovější sestavení a restartování. Tento proces trvá přibližně pět minut. Přihlaste se zpátky do webové konzoly FortiGate.

10.  Klikněte **VPN**na  >  **Průvodce protokolem IPSec**sítě VPN.

11. Zadejte název sítě VPN, například `conn1` v **Průvodci vytvořením sítě VPN**.

12. Vyberte **Tento web je za překladem adres (NAT)**.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image16.png)

13. Vyberte **Další**.

14. Zadejte vzdálenou IP adresu místního zařízení VPN, ke kterému se budete připojovat.

15. Jako **odchozí rozhraní**vyberte **PORT1** .

16. Vyberte **předsdílený klíč** a zadejte (a zaznamenejte) předsdílený klíč. 

    > [!Note]  
    > Tento klíč budete potřebovat k nastavení připojení na místním zařízení VPN, to znamená, že se musí *přesně*shodovat.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image17.png)

17. Vyberte **Další**.

18. Jako **místní rozhraní**vyberte **PORT2** .

19. Zadejte rozsah místní podsítě:
    - forti1:172.16.0.0/16
    - forti2:172.17.0.0/16

    Rozsah IP adres použijte v případě, že používáte jiný rozsah IP adres.

20. Zadejte odpovídající vzdálené podsítě, které reprezentují místní síť, ke které se připojíte prostřednictvím místního zařízení VPN.
    - forti1:172.16.0.0/16
    - forti2:172.17.0.0/16

    Rozsah IP adres použijte v případě, že používáte jiný rozsah IP adres.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image18.png)

21. Vyberte **vytvořit** .

22. Vyberte **Síťová**  >  **rozhraní**.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image19.png)

23. Dvakrát klikněte na **PORT2**.

24. V seznamu **role** vyberte **místní síť** a **DHCP** pro režim adresování.

25. Vyberte **OK**.

Opakujte postup pro ostatní síťové virtuální zařízení.

## <a name="bring-up-all-phase-2-selectors"></a>Vyvolat všechny selektory fáze 2 

Po výše uvedeném případě se u *obou* síťová virtuální zařízení dokončilo:

1.  Na webové konzole forti2 Fortigate vyberte možnost **monitorování**  >  **protokolu IPSec**. 

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image20.png)

2.  Zvýrazněte `conn1` a vyberte **Bring Up**  >  **možnost zahrnout všechny selektory fáze 2**.

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image21.png)

## <a name="test-and-validate-connectivity"></a>Testování a ověření připojení

Nyní byste měli být schopni směrovat mezi každou virtuální sítí přes FortiGate síťová virtuální zařízení. Pokud chcete připojení ověřit, vytvořte virtuální počítač centra Azure Stack v každé virtuální síti InsideSubnet. Vytvoření virtuálního počítače centra Azure Stack se dá udělat prostřednictvím portálu, rozhraní příkazového řádku nebo PowerShellu. Při vytváření virtuálních počítačů:

-   Virtuální počítače centra Azure Stack jsou umístěné na **InsideSubnet** každé virtuální sítě.

-   Při vytváření virtuálního počítače **nepoužijete** žádné skupin zabezpečení sítě (to znamená, že odeberete NSG, které se ve výchozím nastavení přidá, pokud virtuální počítač vytvoříte z portálu.

-   Ujistěte se, že pravidla brány firewall virtuálních počítačů umožňují komunikaci, kterou budete používat k testování připojení. Pro účely testování se doporučuje vypnout bránu firewall zcela v operačním systému, pokud je to možné.

## <a name="next-steps"></a>Další kroky

[Rozdíly a požadavky pro sítě Azure Stack hub](azure-stack-network-differences.md)  
[Nabídka síťového řešení v centru Azure Stack s FortiGate Fortinet](../operator/azure-stack-network-solutions-enable.md)  