---
title: Nastavení brány VPN pro centrum Azure Stack | Microsoft Docs
description: Přečtěte si, jak nastavit bránu VPN pro centrum Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/03/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: e970dc46a4afdcc67e16c0239dc712da111c47a6
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75879117"
---
# <a name="setup-vpn-gateway-for-azure-stack-hub-using-fortigate-nva"></a>Nastavení brány VPN pro centrum Azure Stack s využitím FortiGate síťové virtuální zařízení

Tento článek popisuje, jak vytvořit připojení VPN k rozbočovači Azure Stack. Brána sítě VPN je typem brány virtuální sítě, která odesílá šifrovaný provoz mezi vaší virtuální sítí v centru Azure Stack a vzdálenou bránou VPN. Níže uvedený postup nasazuje jednu virtuální síť s FortiGate síťové virtuální zařízení, síťovým virtuálním zařízením v rámci skupiny prostředků. Poskytuje taky postup pro nastavení sítě VPN s protokolem IPSec na FortiGate síťové virtuální zařízení.

## <a name="prerequisites"></a>Požadavky

-  Přístup k integrovaným systémům Azure Stack hub s dostupnou kapacitou pro nasazení požadovaných požadavků na výpočetní výkon, síť a prostředky, které jsou potřebné pro toto řešení. 

    > [!Note]  
    > Tyto pokyny nebudou **fungovat s** Azure Stack Development Kit (ASDK) z důvodu omezení sítě v ASDK. Další informace najdete v tématu [požadavky a předpoklady pro ASDK](https://docs.microsoft.com/azure-stack/asdk/asdk-deploy-considerations).

-  Přístup k zařízení VPN v místní síti, která hostuje integrovaný systém Azure Stack hub. Zařízení musí vytvořit tunel IPSec, který splňuje parametry popsané v [parametrech nasazení](#deployment-parameters).

-  K dispozici je řešení síťového virtuálního zařízení (síťové virtuální zařízení) ve vašem tržišti centra Azure Stack. SÍŤOVÉ virtuální zařízení řídí tok síťového provozu z hraniční sítě do jiných sítí nebo podsítí. Tento postup využívá [řešení Fortinet FortiGate Next-Generation brány firewall s jedním virtuálním počítačem](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

    > [!Note]  
    > Pokud nemáte **Fortinet FortiGate-VM pro nasazení Azure BYOL** a **FORTIGATE NGFW (BYOL)** k dispozici ve vašem tržišti centra Azure Stack, obraťte se na svého operátora cloudu.

-  K aktivaci FortiGate síťové virtuální zařízení budete potřebovat alespoň jeden dostupný soubor s licencí FortiGate. Informace o tom, jak tyto licence získat, najdete v článku knihovna dokumentů Fortinet [registrace a stažení vaší licence](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

    Tato procedura používá [nasazení Single FortiGate-VM](ttps://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment). Postup, jak připojit FortiGate síťové virtuální zařízení k virtuální síti centra Azure Stack do místní sítě, najdete v tématu.

    Další informace o tom, jak nasadit řešení FortiGate v nastavení aktivní – pasivní (HA), najdete v podrobnostech v knihovně dokumentů Fortinet článku [ha pro FortiGate-VM v Azure](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/983245/ha-for-FortiGate-vm-on-azure).

## <a name="deployment-parameters"></a>Parametry nasazení

Následující tabulka shrnuje parametry, které jsou v těchto nasazeních použity pro referenci.

| Parametr | Hodnota |
|-----------------------------------|---------------------------|
| Název instance FortiGate | forti1 |
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
| Typ veřejné IP adresy | Statický |

> [!Note]
> \* zvolit jiný adresní prostor a předpony podsítě, pokud se `172.16.0.0/16` překrývají s místní sítí nebo s fondem IP adres centra Azure Stack.

## <a name="deploy-the-fortigate-ngfw-marketplace-items"></a>Nasazení položek webu Marketplace pro FortiGate NGFW

1. Otevřete portál Azure Stack hub User Portal.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image5.png)

1. Vyberte **vytvořit prostředek** a vyhledejte `FortiGate`.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image6.png)

2. Vyberte **FORTIGATE NGFW** a vyberte **vytvořit**.

3. Dokončete **základy** pomocí parametrů z tabulky [parametrů nasazení](#deployment-parameters) .

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image7.png)

1. Vyberte **OK**.

2. Zadejte podrobnosti o virtuální síti, podsítích a velikosti virtuálního počítače pomocí tabulky [parametrů nasazení](#deployment-parameters) .

    > [!Warning] 
    > Pokud se místní síť překrývá s `172.16.0.0/16`rozsah IP adres, musíte vybrat a nastavit jiný rozsah sítě a podsítě. Pokud chcete použít jiné názvy a rozsahy než ty, které jsou v tabulce [parametrů nasazení](#deployment-parameters) , použijte parametry, které **nebudou v konfliktu s** místní sítí. Při nastavování rozsahu IP adres virtuální sítě a rozsahů podsítí v rámci virtuální sítě se postarat. Nechcete, aby se rozsah překrýval s rozsahy IP adres, které existují ve vaší místní síti.

3. Vyberte **OK**.

4. Nakonfigurujte veřejnou IP adresu pro FortiGate síťové virtuální zařízení:

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image8.png)

5. Vyberte **OK**. A pak vyberte **OK**.

6. Vyberte **Vytvořit**.

    Nasazení bude trvat přibližně 10 minut.

## <a name="configure-routes-udr-for-the-vnet"></a>Konfigurace tras (UDR) pro virtuální síť

1. Otevřete portál Azure Stack hub User Portal.

2. Vyberte skupiny prostředků. Do filtru zadejte `forti1-rg1` a dvakrát klikněte na skupinu prostředků forti1-RG1.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image9.png)

2. Vyberte prostředek forti1-forti1-InsideSubnet-Routes-xxxx.

3. V části **Nastavení**vyberte **trasy** .

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image10.png)

4. Odstraňte trasu **k Internetu** .

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image11.png)

5. Vyberte *Ano*.

6. Vyberte **Přidat** a přidejte novou trasu.

7. Pojmenujte `to-onprem`trasy.

8. Zadejte rozsah IP adres, který definuje rozsah sítě místní sítě, ke které se připojí síť VPN.

9. Vyberte **virtuální zařízení** pro **typ dalšího segmentu směrování** a `172.16.1.4`. Rozsah IP adres použijte v případě, že používáte jiný rozsah IP adres.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image12.png)

10. Vyberte **Uložit**.

## <a name="activate-the-fortigate-nva"></a>Aktivace síťové virtuální ZAŘÍZENÍu FortiGate

Aktivujte síťové virtuální zařízení FortiGate a nastavte připojení VPN IPSec na každém síťové virtuální zařízení.

Chcete-li aktivovat jednotlivé FortiGate síťové virtuální zařízení, bude vyžadován platný soubor s licencí od Fortinet. Síťová virtuální zařízení **nebude fungovat,** dokud neaktivujete jednotlivé síťové virtuální zařízení. Další informace o tom, jak získat soubor s licencí a postup aktivace síťové virtuální zařízení, najdete v článku knihovna dokumentů Fortinet [registrace a stažení vaší licence](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license).

Po aktivaci služby síťová virtuální zařízení vytvořte tunel sítě VPN s protokolem IPSec v síťové virtuální zařízení.

1. Otevřete portál Azure Stack hub User Portal.

2. Vyberte skupiny prostředků. Do filtru zadejte `forti1` a dvakrát klikněte na skupinu prostředků forti1.

3. Dvakrát klikněte na virtuální počítač **forti1** v seznamu typů prostředků v okně Skupina prostředků.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image13.png)

4. Zkopírujte přiřazenou IP adresu, otevřete prohlížeč a vložte IP adresu do adresního řádku. Lokalita může aktivovat upozornění, že certifikát zabezpečení není důvěryhodný. Pokračujte i nadále.

5. Zadejte uživatelské jméno a heslo správce FortiGate, které jste zadali během nasazování.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image14.png)

6. Vyberte **systém** > **firmware**.

7. Zaškrtněte políčko, které zobrazuje nejnovější firmware, například `FortiOS v6.2.0 build0866`.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image15.png)

8. Vyberte možnost **Konfigurace zálohování a upgradovat** > **pokračovat**.

9. SÍŤOVÉ virtuální zařízení aktualizuje svůj firmware na nejnovější sestavení a restartování. Tento proces trvá přibližně pět minut. Přihlaste se zpátky do webové konzoly FortiGate.

10. Klikněte na průvodce **VPN** > **IPSec**.

11. Zadejte název sítě VPN, například `conn1` v **Průvodci vytvořením sítě VPN**.

12. Vyberte **Tento web je za překladem adres (NAT)** .

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image16.png)

13. Vyberte **Next** (Další).

14. Zadejte vzdálenou IP adresu místního zařízení VPN, ke kterému se budete připojovat.

15. Jako **odchozí rozhraní**vyberte **PORT1** .

16. Vyberte **předsdílený klíč** a zadejte (a zaznamenejte) předsdílený klíč. 

    > [!Note]  
    > Tento klíč budete potřebovat k nastavení připojení na místním zařízení VPN, to znamená, že se musí *přesně*shodovat.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image17.png)

17. Vyberte **Next** (Další).

18. Jako **místní rozhraní**vyberte **PORT2** .

19. Zadejte rozsah místní podsítě:
    - forti1:172.16.0.0/16
    - forti2:172.17.0.0/16

    Rozsah IP adres použijte v případě, že používáte jiný rozsah IP adres.

20. Zadejte odpovídající vzdálené podsítě, které reprezentují místní síť, ke které se připojíte prostřednictvím místního zařízení VPN.

    [](./media/azure-stack-network-howto-vnet-to-onprem/image18.png)

21. Vyberte **Vytvořit**.

22. Vyberte **síťová** > **rozhraní**.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image19.png)

23. Dvakrát klikněte na **PORT2**.

24. V seznamu **role** vyberte **místní síť** a **DHCP** pro režim adresování.

25. Vyberte **OK**.

## <a name="configure-the-on-premises-vpn"></a>Konfigurace místní sítě VPN

Místní zařízení VPN musí být nakonfigurované tak, aby vytvořilo tunelové propojení VPN IPSec. Následující tabulka uvádí parametry, které budete potřebovat k nastavení místního zařízení VPN. Informace o tom, jak nakonfigurovat místní zařízení VPN, najdete v části transakční dokumentace k vašemu zařízení.

| Parametr | Hodnota |
| --- | --- |
| IP adresa vzdálené brány | Veřejná IP adresa přiřazená k forti1 – viz [aktivovat FORTIGATE síťové virtuální zařízení](#activate-the-fortigate-nva). |
| Vzdálená síť IP | 172.16.0.0/16 (Pokud používáte rozsah IP adres v těchto pokynech pro virtuální síť). |
| Auth. Method = předsdílený klíč (PSK) | Z kroku 16.
| Verze IKE | 1\. místo |
| Režim IKE | Hlavní (ID ochrany) |
| Algoritmy návrhů 1. fáze | AES128-SHA256, AES256-SHA256, AES128-SHA1, AES256-SHA1 |
| Skupiny Diffie-Hellman | 14.5. |

## <a name="create-the-vpn-tunnel"></a>Vytvoření tunelu VPN

Jakmile je místní zařízení VPN správně nakonfigurované, může se teď vytvořit tunelové propojení VPN.

Z FortiGate síťové virtuální zařízení:

1. Ve webové konzole forti1 FortiGate **otevřete > monitorování** **protokolu IPSec**.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image20.png)

2. Zvýrazněte **conn1** a **Vyberte > ** **všechny selektory fáze 2**.

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image21.png)

## <a name="test-and-validate-connectivity"></a>Testování a ověření připojení

Mezi sítí virtuální sítě a místní sítí můžete směrovat prostřednictvím místního zařízení VPN.

Ověření připojení:

1. Vytvořte virtuální počítač v centru Azure Stack virtuální sítě a systému v místní síti. Můžete postupovat podle pokynů k vytvoření virtuálního počítače v části [rychlý Start: Vytvoření virtuálního počítače s Windows serverem pomocí portálu Azure Stack hub](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-windows-portal).

2. Při vytváření virtuálního počítače centra Azure Stack a přípravě místního systému ověřte:

-  Virtuální počítač centra Azure Stack je umístěný na **INSIDESUBNET** virtuální sítě.

-  Místní systém se umístí do místní sítě v rámci definovaného rozsahu IP adres, jak je definováno v konfiguraci protokolu IPSec. Také zajistěte, aby se místnímu systému poskytovala IP adresa místního rozhraní místního zařízení VPN jako trasa, která se může připojit k síti VNET centra Azure Stack, například `172.16.0.0/16`.

-  Při vytváření **nepoužívejte žádné** skupin zabezpečení sítě na virtuálním počítači centra Azure Stack. Pokud vytváříte virtuální počítač z portálu, možná budete muset odebrat NSG, který se ve výchozím nastavení přidá.

-  Zajistěte, aby místní operační systém a operační systém virtuálního počítače centra Azure Stack nepoužívali pravidla firewallu operačního systému, která by zakázala komunikaci, kterou budete používat k testování připojení. Pro účely testování se doporučuje zakázat bránu firewall zcela v rámci operačního systému obou systémů.

## <a name="next-steps"></a>Další kroky

[Rozdíly a požadavky pro sítě Azure Stack hub](azure-stack-network-differences.md)  
[Nabídka síťového řešení v centru Azure Stack s FortiGate Fortinet](../operator/azure-stack-network-solutions-enable.md)  
