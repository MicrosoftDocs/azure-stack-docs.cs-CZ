---
title: Nasazení clusteru Kubernetes do vlastní virtuální sítě v centru Azure Stack
description: Naučte se, jak nasadit cluster Kubernetes do vlastní virtuální sítě v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 3/19/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/19/2020
ms.openlocfilehash: aac2f9a0991bdae7f15d7fc54517a880ab384785
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "80068945"
---
# <a name="deploy-a-kubernetes-cluster-to-a-custom-virtual-network-on-azure-stack-hub"></a>Nasazení clusteru Kubernetes do vlastní virtuální sítě v centru Azure Stack 

Cluster Kubernetes můžete nasadit pomocí modulu Azure Kubernetes Service (AKS) ve vlastní virtuální síti. V tomto článku se podíváme na hledání potřebných informací ve vaší virtuální síti. Můžete najít postup pro výpočet IP adres používaných clusterem, nastavení šířky v modelu rozhraní API a nastavení tabulky směrování a skupiny zabezpečení sítě.

Cluster Kubernetes v centru Azure Stack s použitím modulu AKS používá modul plug-in kubenet Network. Diskuzi o sítích modulu plug-in kubenet Network plugin v Azure najdete v tématu [použití sítě kubenet s vlastními rozsahy IP adres ve službě Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/configure-kubenet).

## <a name="create-custom-virtual-network"></a>Vytvoření vlastní virtuální sítě

V instanci centra Azure Stack musíte mít vlastní virtuální síť. Další informace najdete v tématu [rychlý Start: vytvoření virtuální sítě pomocí Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal).

Vytvořte ve virtuální síti novou podsíť. Budete potřebovat získat ID prostředku podsítě a rozsah IP adres. Při nasazení clusteru použijete ID a rozsah prostředků v modelu rozhraní API.

1. V instanci centra Azure Stack otevřete portál User Portal Azure Stack hub.
2. Vyberte **Všechny prostředky**.
3. Do vyhledávacího pole zadejte název vaší virtuální sítě.
4. Vyberte **podsítě** > a**podsítě** a přidejte tak podsíť.
5. Přidejte **název** a **Rozsah adres** pomocí zápisu CIDR. Vyberte **OK**.
4. V okně **virtuální sítě** vyberte **vlastnosti** . Zkopírujte **ID prostředku**a pak ho přidejte `/subnets/<nameofyoursubnect>`. Tuto hodnotu použijete jako hodnotu pro `vnetSubnetId` klíč v modelu rozhraní API pro váš cluster. ID prostředku pro podsíť používá následující formát:<br>`/subscriptions/SUB_ID/resourceGroups/RG_NAME/providers/Microsoft.Network/virtualNetworks/VNET_NAME/subnets/SUBNET_NAME`

    ![ID prostředku virtuální sítě](media/kubernetes-aks-engine-custom-vnet/virtual-network-id.png)

5. V okně **virtuální sítě** vyberte **podsítě** . Vyberte název podsítě, například výchozí.
    
    ![blok CIDR virtuální sítě](media/kubernetes-aks-engine-custom-vnet/virtual-network-cidr-block.png)
    
6. V okně podsíť si poznamenejte rozsah adres a blok CIDR virtuální sítě, například: `10.1.0.0 - 10.1.0.255 (256 addresses)` a. `10.1.0.0/24`



## <a name="get-the-ip-address-block"></a>Získat blok IP adres

Modul AKS podporuje nasazení do existující virtuální sítě. Při nasazování do existující podsítě bude cluster používat blok po sobě jdoucích IP adres pro agenty a další pro hlavní servery.

Budete muset nastavit dvě hodnoty. Budete potřebovat znát počet IP adres, které budete muset pro svůj cluster rezervovat, a první po sobě jdoucí statickou IP adresu v rámci rozsahu IP adres podsítě.

Pokud používáte více hlavních uzlů, modul AKS vyžaduje rozsah až 16 nevyužitých IP adres. Cluster bude používat jednu IP adresu pro každou hlavní stránku a až pět hlavních serverů. Modul AKS bude také vyžadovat další 10 IP adres po posledním serveru pro rezervaci IP adresy. Nakonec bude nástroj pro vyrovnávání zatížení za celkem 16 využíván dodatečnou IP adresou.

Při umísťování bloku IP adres vyžaduje podsíť následující přidělení stávajících IP adres:
 - První čtyři IP adresy a poslední IP adresa jsou vyhrazené a nedají se použít v žádné podsíti Azure.
 - Zbývá otevřít vyrovnávací paměť 16 IP adres.
 - Hodnota první IP adresy vašeho clusteru by měla být na konci adresního prostoru, aby nedocházelo ke konfliktům IP adres. Pokud je to možné, přiřaďte `firstConsecutiveStaticIP` vlastnost k IP adrese na *konci* dostupného adresního prostoru IP adres v podsíti.

V následujícím příkladu vidíte, jak tyto různé okolnosti doplňují rozsah IP adres v podsíti. Toto je pro tři hlavní servery. Pokud používáte podsíť s 256 adresami, například 10.1.0.0/24, budete muset nastavit první po sobě jdoucí statickou IP adresu na 207. V následující tabulce jsou uvedené adresy a požadavky:

| Rozsah pro podsíť/24 | Číslo | Poznámka |
| --- | --- | --- |
| 10.1.0.0 – 10.1.03 | 4 | Vyhrazeno v podsíti Azure. |
| **10.1.0.224**– 10.1.0.238 | 14 | Počet IP adres pro cluster definovaný modulem AKS<br><br> 3. IP adresy pro 3 hlavní servery<br>10 IP adres pro rezervy<br>1 IP adresa pro nástroj pro vyrovnávání zatížení |
| 10.1.0.239 - 10.1.0.255 | 16 | 16 vyrovnávací paměti IP adres. |
| 10.1.0.256 | 1 | Vyhrazeno v podsíti Azure. |

V tomto příkladu by `firstConsecutiveStaticIP` vlastnost byla. `10.1.0.224`

U větších podsítí, například/16 s více než 60000 adres, se možná nebudete muset setkat s tím, že by se vaše statické IP adresy nastavily na konec síťového prostoru. Nastavte rozsah statických IP adres clusteru mimo prvních 24 adres v prostoru IP adres, aby cluster mohl být odolný při deklaracích adres.


## <a name="update-the-api-model"></a>Aktualizace modelu rozhraní API

Aktualizujte model rozhraní API, který se používá k nasazení clusteru z modulu AKS do vlastní virtuální sítě.

V **profilech masterprofile** nastavte následující hodnoty:

| Pole | Příklad | Popis |
| --- | --- | --- |
| vnetSubnetId | `/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default` | Zadejte ID prostředku podsítě.  |
| firstConsecutiveStaticIP | 10.1.0.224 | Do vlastnosti `firstConsecutiveStaticIP` konfigurace přiřaďte IP adresu na *konci* dostupného adresního prostoru IP adres v požadované podsíti. `firstConsecutiveStaticIP`platí jenom pro hlavní fond. |

V **agentPoolProfiles** nastavte následující hodnoty:

| Pole | Příklad | Popis |
| --- | --- | --- |
| vnetSubnetId | `/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default` | Zadejte adresu Azure Resource Manager ID podsítě.  |

Příklad:

```json
"masterProfile": {
  ...
  "vnetSubnetId": "/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default",
  "firstConsecutiveStaticIP": "10.1.0.224",
  ...
},
...
"agentPoolProfiles": [
  {
    ...
    "vnetSubnetId": "/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default",
    ...
  },

```

## <a name="deploy-your-cluster"></a>Nasazení clusteru

Po přidání hodnot do modelu rozhraní API můžete cluster nasadit z klientského počítače pomocí `deploy` příkazu pomocí modulu AKS. Pokyny najdete v tématu [nasazení clusteru Kubernetes](azure-stack-kubernetes-aks-engine-deploy-cluster.md#deploy-a-kubernetes-cluster).

## <a name="set-the-route-table-and-network-security-group"></a>Nastavení tabulky směrování a skupiny zabezpečení sítě

Po nasazení clusteru se vraťte k virtuální síti na portálu Azure Stack User Portal. V okně podsíť nastavte jak tabulku směrování, tak skupinu zabezpečení sítě (NSG). Pokud nepoužíváte Azure CNI, například `networkPlugin`: `kubenet` v objektu konfigurace modelu `kubernetesConfig` rozhraní API. Po úspěšném nasazení clusteru do vlastní virtuální sítě Získejte ID prostředku směrovací tabulky ze **sítě** v okně skupiny prostředků clusteru.

1. V instanci centra Azure Stack otevřete portál User Portal Azure Stack hub.
2. Vyberte **Všechny prostředky**.
3. Do vyhledávacího pole zadejte název vaší virtuální sítě.
4. Vyberte **podsítě** a pak vyberte název podsítě, která obsahuje váš cluster.
    
    ![tabulka směrování a skupina zabezpečení sítě](media/kubernetes-aks-engine-custom-vnet/virtual-network-rt-nsg.png)
    
5. Vyberte **směrovací tabulka** a pak vyberte směrovací tabulku pro svůj cluster.
6. Vyberte **Skupina zabezpečení sítě** a potom vyberte NSG pro váš cluster.

> [!Note]  
> Vlastní virtuální síť pro cluster Windows Kubernetes má [známý problém](https://github.com/Azure/aks-engine/issues/371).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o modulu [AKS v centru Azure Stack](azure-stack-kubernetes-aks-engine-overview.md) .  
- Přehled o [Azure monitor for Containers](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)