---
title: Služba Azure Kubernetes s požadavky na Azure Stack HCL
description: Než začnete službu Azure Kubernetes Service na Azure Stack HCL
ms.topic: conceptual
author: abhilashaagarwala
ms.author: abha
ms.date: 12/02/2020
ms.openlocfilehash: 71c842cf44963988da7926003646b246bf80f802
ms.sourcegitcommit: 8776cbe4edca5b63537eb10bcd83be4b984c374a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2021
ms.locfileid: "98175732"
---
# <a name="system-requirements-for-azure-kubernetes-service-on-azure-stack-hci"></a>Požadavky na systém pro službu Azure Kubernetes ve Azure Stack HCL

> Platí pro: Azure Stack HCI, Windows Server 2019 Datacenter

Tento článek popisuje požadavky na nastavení služby Azure Kubernetes na Azure Stack HCI nebo Windows Server 2019 Datacenter a jejich použití k vytváření clusterů Kubernetes. Přehled služby Azure Kubernetes ve službě Azure Stack HCI najdete v tématu [AKS na Azure Stack HCL – přehled](overview.md).

## <a name="determine-hardware-requirements"></a>Určení hardwarových požadavků

Společnost Microsoft doporučuje, abyste od našich partnerů nakoupili ověřené Azure Stack hardwarového a softwarového řešení HCI. Tato řešení jsou navržená, sestavená a ověřená v rámci naší referenční architektury za účelem zajištění kompatibility a spolehlivosti, abyste mohli rychle začít pracovat. Ověřte, že systémy, součásti, zařízení a ovladače, které používáte, jsou systémy Windows Server 2019 s certifikací na základě katalogu Windows serveru. Ověřená řešení najdete na webu [řešení Azure Stack HCL](https://azure.microsoft.com/overview/azure-stack/hci) .

## <a name="general-requirements"></a>Obecné požadavky

Pro Azure Kubernetes Service na Azure Stack HCI nebo Windows Server 2019 Datacenter, aby fungovala optimálně v prostředí služby Active Directory, ujistěte se, že jsou splněné následující požadavky: 

 - Ujistěte se, že je nastavená synchronizace času a rozdíl není delší než 2 minuty napříč všemi uzly clusteru a řadičem domény. Informace o nastavení synchronizace času najdete v tématu [Služba Systémový čas](/windows-server/networking/windows-time-service/windows-time-service-top). 

 - Ujistěte se, že uživatelské účty, které přidávají aktualizace, a spravují službu Azure Kubernetes na Azure Stack HCI nebo Windows Server 2019 Datacenter mají správná oprávnění ve službě Active Directory. Pokud ke správě zásad skupiny pro servery a služby používáte organizační jednotky (OU), uživatelské účty budou vyžadovat oprávnění list, číst, upravit a odstranit u všech objektů v organizační jednotce. 

 - Pro servery a služby, na které přidáváte službu Azure Kubernetes, doporučujeme pro clustery datacenter Azure Stack HCI nebo Windows Server 2019 použít samostatnou organizační jednotku. Použití samostatné organizační jednotky vám umožní řídit přístup a oprávnění s větší členitosti.

 - Pokud používáte šablony objektů zásad skupiny v kontejnerech ve službě Active Directory, zajistěte, aby se zásady nasadily pomocí AKS-HCI. Posílení zabezpečení serveru bude k dispozici v další verzi Preview.

## <a name="compute-requirements"></a>Požadavky na výpočetní výkon

 - Azure Stack cluster HCI nebo cluster s podporou převzetí služeb při selhání datacenter Windows serveru 2019 s maximálně čtyřmi servery v clusteru. Doporučujeme, aby každý server v clusteru měl alespoň 24 jader procesoru a minimálně 256 GB RAM.

 - I když můžete technicky spouštět službu Azure Kubernetes v jednom uzlu Windows Server 2019 Datacenter, nedoporučujeme to dělat. Službu Azure Kubernetes však můžete spustit v jednom uzlu Windows Server 2019 Datacenter pro účely vyhodnocení.

 - Další požadavky na výpočetní výkon pro službu Azure Kubernetes v Azure Stack HCI jsou v souladu s požadavky Azure Stack HCL. Další podrobnosti o požadavcích na Azure Stack HCI serveru najdete v [Azure Stack požadavky na systém HCI](../hci/concepts/system-requirements.md#server-requirements) .

 - Tato verze Preview vyžaduje, abyste na každém serveru v clusteru nainstalovali operační systém Azure Stack HCI s použitím oblastí EN-US a výběr jazyka. Změna po instalaci v tuto chvíli nestačí.

## <a name="network-requirements"></a>Požadavky sítě 

Následující požadavky platí pro Azure Stack cluster HCI a také pro cluster Windows Server 2019 Datacenter: 

 - Ověřte, jestli máte existující externí virtuální přepínač nakonfigurovaný, pokud používáte centrum pro správu Windows. U Azure Stack clusterů HCI musí být tento přepínač a jeho název stejný jako u všech uzlů clusteru. 

 - Ověřte, že je na všech síťových adaptérech zakázaný protokol IPv6. 

 - V případě úspěšného nasazení musí mít uzly clusteru Azure Stack HCI a virtuální počítače clusteru Kubernetes k dispozici externí připojení k Internetu.
  
 - Ujistěte se, že je mezi Azure Stack hostitele HCI a virtuálními počítači klienta síťové připojení.

 - Překlad názvů DNS se vyžaduje, aby všechny uzly mohly vzájemně komunikovat. V případě překladu externích IP adres Kubernetes použijte servery DNS poskytované serverem DHCP, když se IP adresa získá. V případě překladu interního názvu Kubernetes použijte výchozí řešení založené na DNS Kubernetes. 

 - Pro tuto verzi Preview poskytujeme jenom jednu podporu sítě VLAN pro celé nasazení. 

 - Pro tuto verzi Preview máme pro clustery Kubernetes vytvořené prostřednictvím PowerShellu omezené podpory proxy. 
 
### <a name="ip-address-assignment"></a>Přiřazení IP adresy  
 
V rámci úspěšného AKS nasazení na Azure Stack HCI doporučujeme nakonfigurovat rozsah virtuálních IP adres na server DHCP. Doporučuje se také nakonfigurovat tři až pět uzlů řídicí plochy s vysokou dostupností pro všechny clustery úloh. 

> [!NOTE]
> Použití samotného přiřazování statických IP adres se nepodporuje. Jako součást této verze Preview musíte nakonfigurovat server DHCP.

#### <a name="dhcp"></a>DHCP
Při přiřazování IP adres v celém clusteru postupujte podle těchto požadavků:  

 - Síť musí mít dostupný server DHCP, aby poskytovala virtuálním počítačům a hostitelům virtuálních počítačů adresy TCP/IP. Server DHCP by měl také obsahovat informace o protokolu NTP (Network Time Protocol) a DNS host.
 
 - Server DHCP s vyhrazeným oborem adres IPv4, který je přístupný pro Azure Stack clusteru HCI.
 
 - Adresy IPv4, které poskytuje server DHCP, by měly být směrovatelné a mít vypršení 30denní doby zapůjčení, aby se zabránilo ztrátě připojení IP v případě aktualizace nebo opětovného zřizování virtuálního počítače.  

Měli byste rezervovat minimálně následující počet adres DHCP:

| Typ clusteru  | Řídicí uzel roviny | Pracovní uzel | Aktualizace | Nástroj pro vyrovnávání zatížení  |
| ------------- | ------------------ | ---------- | ----------| -------------|
| Hostitel AKS |  1  |  0  |  2  |  0  |
| Cluster úloh  |  1 na uzel  | 1 na uzel |  5  |  1  |

V závislosti na počtu clusterů úloh a roviny řízení a pracovních uzlů, které máte ve vašem prostředí, můžete zjistit, jak je počet požadovaných IP adres proměnný. Doporučujeme, abyste ve fondu IP adres DHCP zavedli IP adresy 256 (/24 podsítí).
  
    
#### <a name="vip-pool-range"></a>Rozsah fondu VIP

Fondy virtuálních IP adres (VIP) se důrazně doporučují pro AKS nasazení HCI v Azure Stack. Fondy VIP představují rozsah rezervovaných statických IP adres, které se používají pro dlouhodobá nasazení, která zaručují, že vaše úlohy nasazení a aplikace jsou vždycky dostupné. V současné době podporujeme jenom IPv4 adresy, proto musíte ověřit, že jste na všech síťových adaptérech zakázali protokol IPv6. Také se ujistěte, že vaše virtuální IP adresy nejsou součástí rezervy IP adresy DHCP.

Přinejmenším byste měli rezervovat jednu IP adresu na cluster (zatížení a hostitele AKS) a jednu IP adresu na službu Kubernetes. Počet požadovaných IP adres v rozsahu fondu VIP se liší v závislosti na počtu clusterů úloh a Kubernetes služeb, které máte ve vašem prostředí. Pro nasazení AKS-HCI doporučujeme zahradit 16 statických IP adres. 

Při nastavování hostitele AKS použijte `-vipPoolStartIp` parametry a v části `-vipPoolEndIp` a `Set-AksHciConfig` vytvořte fond VIP.

#### <a name="mac-pool-range"></a>Rozsah fondu adres MAC
V rozsahu doporučujeme mít minimálně 16 adres MAC, aby bylo možné v jednotlivých clusterech určit více uzlů roviny ovládacího prvku. Při nastavování hostitele AKS použijte `-macPoolStart` parametry a v části a `-macPoolEnd` `Set-AksHciConfig` rezervujte adresy Mac z fondu adres MAC DHCP pro služby Kubernetes.
  
### <a name="network-port-and-url-requirements"></a>Požadavky na síťový port a adresu URL 

Při vytváření clusteru Azure Kubernetes na Azure Stack HCI se na každém serveru v clusteru automaticky otevřou tyto porty brány firewall. 


| Port brány firewall               | Description     | 
| ---------------------------- | ------------ | 
| 45000           | port serveru wssdagent GPRC     |
| 45001             | Port ověřování wssdagent GPRC  | 
| 55000           | port serveru wssdcloudagent GPRC      |
| 65000            | Port ověřování wssdcloudagent GPRC  | 


Pro počítač centra pro správu systému Windows a všechny uzly v clusteru Azure Stack HCI jsou vyžadovány výjimky adresy URL brány firewall. 

| URL        | Port | Služba | Poznámky |
| ---------- | ---- | --- | ---- |
https://helm.sh/blog/get-helm-sh/  | 443 | Stažení agenta, WAC | Používá se ke stažení binárních souborů Helm. 
https://storage.googleapis.com/  | 443 | Inicializace cloudu | Stahování binárních souborů Kubernetes 
https://azurecliprod.blob.core.windows.net/ | 443 | Inicializace cloudu | Stahování binárních souborů a kontejnerů 
https://aka.ms/installazurecliwindows | 443 | WAC | Stahuje se Azure CLI. 
https://:443 | 443 | TCP | Slouží k podpoře agentů ARC Azure.  
*.blob.core.windows.net | 443 | TCP | Požadováno ke stažení
*. api.cdp.microsoft.com, *. dl.delivery.mp.microsoft.com, *. emdl.ws.microsoft.com | 80, 443 | Stáhnout agenta | Stahují se metadata 
*. dl.delivery.mp.microsoft.com, *. do.dsp.mp.microsoft.com. | 80, 443 | Stáhnout agenta | Stahují se image VHD. 
ecpacr.azurecr.io | 443 | Kubernetes | Stahování imagí kontejneru 
git://:9418 | 9418 | TCP | Slouží k podpoře agentů ARC Azure. 

## <a name="storage-requirements"></a>Požadavky na úložiště 

Služba Azure Kubernetes v Azure Stack HCL podporuje následující implementace úložiště: 

|  Name                         | Typ úložiště | Požadovaná kapacita |
| ---------------------------- | ------------ | ----------------- |
| Azure Stack clusteru HCI          | CSV          | 1 TB              |
| Cluster s podporou převzetí služeb při selhání datacenter Windows serveru 2019          | CSV          | 1 TB              |
| Windows Server 2019 Datacenter s jedním uzlem | Přímo připojené úložiště | 500 GB|

## <a name="review-maximum-supported-hardware-specifications"></a>Kontrola maximálního počtu podporovaných specifikací hardwaru 

Služba Azure Kubernetes na Azure Stack rozhraní HCI, která překračují následující specifikace, se nepodporují: 

| Prostředek                     | Maximum |
| ---------------------------- | --------|
| Fyzické servery na cluster | 4       |
| Clustery Kubernetes            | 4       |
| Celkový počet virtuálních počítačů          | 200     |

## <a name="windows-admin-center-requirements"></a>Požadavky centra pro správu systému Windows

Centrum pro správu Windows je uživatelské rozhraní pro vytváření a správu služby Azure Kubernetes v Azure Stack HCL. Pokud chcete používat centrum pro správu Windows se službou Azure Kubernetes v Azure Stack HCI, musíte splnit všechna kritéria uvedená v seznamu níže. 

Tady jsou požadavky na počítač, na kterém je spuštěná brána centra pro správu Windows: 

 - Počítač s Windows 10 nebo Windows Server (v současnosti nepodporujeme centrum pro správu Windows v clusteru Azure Stack HCI nebo Windows Server 2019 Datacenter)
 - 60 GB volného místa
 - Registrováno v Azure
 - Ve stejné doméně jako cluster Azure Stack HCI nebo Windows Server 2019 Datacenter

## <a name="next-steps"></a>Další kroky 

Po splnění všech výše uvedených požadavků můžete nastavit hostitele služby Azure Kubernetes na Azure Stack HCL pomocí:
 - [Centrum pro správu systému Windows](setup.md)
 - [PowerShell](setup-powershell.md)