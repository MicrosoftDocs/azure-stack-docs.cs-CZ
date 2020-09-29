---
title: Služba Azure Kubernetes s požadavky na Azure Stack HCL
description: Než začnete službu Azure Kubernetes Service na Azure Stack HCL
ms.topic: conceptual
author: abhilashaagarwala
ms.author: abha
ms.date: 09/22/2020
ms.openlocfilehash: b4f79281987b425b907126081e74ce32bf9ee14d
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948953"
---
# <a name="system-requirements-for-azure-kubernetes-service-on-azure-stack-hci"></a>Požadavky na systém pro službu Azure Kubernetes ve Azure Stack HCL

> Platí pro: Azure Stack HCI

Tento článek se zabývá požadavky na nastavení služby Azure Kubernetes na rozhraní HCI Azure Stack a jejich použití k vytváření clusterů Kubernetes. Přehled služby Azure Kubernetes ve službě Azure Stack HCI najdete v tématu [AKS na Azure Stack HCL – přehled](overview.md).

## <a name="determine-hardware-requirements"></a>Určení hardwarových požadavků

Společnost Microsoft doporučuje, abyste od našich partnerů nakoupili ověřené Azure Stack hardwarového a softwarového řešení HCI. Tato řešení jsou navržená, sestavená a ověřená v rámci naší referenční architektury za účelem zajištění kompatibility a spolehlivosti, abyste mohli rychle začít pracovat. Ověřte, že systémy, součásti, zařízení a ovladače, které používáte, jsou systémy Windows Server 2019 s certifikací na základě katalogu Windows serveru. Ověřená řešení najdete na webu [řešení Azure Stack HCL](https://azure.microsoft.com/overview/azure-stack/hci) .

### <a name="general-requirements"></a>Obecné požadavky

Aby služba Azure Kubernetes Service na Azure Stack HCL fungovala optimálně v prostředí služby Active Directory, ujistěte se, že jsou splněné následující požadavky: 

 - Ujistěte se, že je nastavení synchronizace času nastaveno a rozdíl není delší než 2 minuty v rámci všech uzlů clusteru a řadiče domény. Informace o nastavení synchronizace času najdete ve [službě Systémový čas systému Windows](/windows-server/networking/windows-time-service/windows-time-service-top). 

 - Ujistěte se, že uživatelské účty, které přidávají, aktualizují a spravují službu Azure Kubernetes ve Azure Stack clustery HCI mají správná oprávnění ve službě Active Directory. Pokud ke správě zásad skupiny pro servery a služby používáte organizační jednotky (OU), uživatelské účty budou vyžadovat oprávnění list, číst, upravit a odstranit u všech objektů v organizační jednotce. 

 - Pro servery a služby, které přidáváte do Azure Stack služby Azure Kubernetes, doporučujeme použít samostatnou organizační jednotku. To vám umožní řídit přístup a oprávnění s větší členitosti.

 - Pokud používáte šablony objektů zásad skupiny v kontejnerech ve službě Active Directory, zajistěte, aby se z těchto zásad vyloučila implementace AKS-HCI. Posílení zabezpečení serveru bude k dispozici v další verzi Preview.

### <a name="compute-requirements"></a>Požadavky na výpočetní výkon

 - Azure Stack cluster HCI s maximálně čtyřmi servery v clusteru. Doporučujeme, aby každý server v clusteru měl alespoň 24 jader procesoru a minimálně 512 GB RAM.

 - I když můžete technicky spouštět službu Azure Kubernetes na jednom uzlu Azure Stack HCI Server, nedoporučujeme to.

 - Další požadavky na výpočetní výkon pro službu Azure Kubernetes v Azure Stack HCI jsou v souladu s požadavky Azure Stack HCL. Další podrobnosti o požadavcích na server Azure Stack HCL najdete v [Azure Stack požadavků na HCI](../hci/deploy/before-you-start.md) .  

 - Tato verze Preview vyžaduje, abyste na každém serveru v clusteru nainstalovali operační systém Azure Stack HCI s použitím oblastí EN-US a výběr jazyka. Změna po instalaci v tuto chvíli nestačí.

### <a name="network-requirements"></a>Požadavky sítě 

Služba Azure Kubernetes v Azure Stack HCI vyžaduje spolehlivé síťové připojení s nízkou šířkou pásma mezi jednotlivými uzly serveru. Ověřte následující: 

 - Ověřte, jestli máte existující externí virtuální přepínač nakonfigurovaný, pokud používáte centrum pro správu Windows. U Azure Stack clusterů HCI musí být tento přepínač stejný ve všech uzlech clusteru. 

 - Ověřte, že je na všech síťových adaptérech zakázaný protokol IPv6. 

 - Síť musí mít dostupný server DHCP, aby poskytovala virtuálním počítačům a hostitelům virtuálních počítačů adresy TCP/IP. Server DHCP by měl obsahovat i informace o hostiteli NTP a DNS. 

 - Doporučujeme také mít server DHCP s vyhrazeným rozsahem adres IPv4, který je přístupný v clusteru Azure Stack HCI. Můžete například rezervovat 10.0.1.1 pro výchozí bránu 10.0.1.2 na 10.0.1.102 pro služby Kubernetes a používat 10.0.1.103-10.0.1.254 pro virtuální počítače clusteru Kubernetes. 

 - Adresy IPv4, které poskytuje server DHCP, by měly být směrovatelné a mít dobu zapůjčení 7 dní, aby nedošlo ke ztrátě připojení IP v případě aktualizace nebo opětovného zřizování virtuálního počítače.  

 - Nedoporučujeme mít značky VLAN. Použijte přístup nebo neoznačené porty ve Azure Stack síťových přepínačích HCI v clusteru. 

 - Během instalace nedoporučujeme používat vyhrazený statický virtuální IP fond pro virtuální IP fond vyrovnávání zátěže. Fond IP adres DHCP se používá pro virtuální počítače, zatímco virtuální fond IP adres se používá pro nástroj pro vyrovnávání zatížení a musí být směrovatelný. Fond IP adres DHCP nemusí být směrovatelný na externí Internet.

 - Překlad názvů DNS se vyžaduje, aby všechny uzly mohly vzájemně komunikovat. V případě překladu externích IP adres Kubernetes používáme servery DNS poskytované serverem DHCP, když se IP adresa získá. V případě překladu interního názvu Kubernetes používáme výchozí řešení založené na DNS základní Kubernetes. 

 - Pro tuto verzi Preview nepodporujeme použití proxy server k připojení centra pro správu Windows, Azure Stack uzlů clusteru HCI a služby Azure Kubernetes na Azure Stack uzlech clusteru HCI na internetu.

### <a name="network-port-and-url-requirements"></a>Požadavky na síťový port a adresu URL 

Při vytváření clusteru Azure Kubernetes na Azure Stack HCI se na každém serveru v clusteru automaticky otevřou následující porty brány firewall. 


| Port brány firewall               | Popis         | 
| ---------------------------- | ------------ | 
| 45000           | port serveru wssdagent GPRC           |
| 45001             | Port ověřování wssdagent GPRC  | 
| 55000           | port serveru wssdcloudagent GPRC           |
| 55001             | Port ověřování wssdcloudagent GPRC  | 


Pro počítač centra pro správu systému Windows a všechny uzly v clusteru Azure Stack HCI jsou vyžadovány výjimky adresy URL brány firewall. 

| URL        | Port | Služba | Poznámky |
| ---------- | ---- | --- | ---- |
https://get.helm.sh/  | 443 | Stažení agenta, WAC | Používá se ke stažení binárních souborů Helm. 
https://storage.googleapis.com/  | 443 | Inicializace cloudu | Stahování binárních souborů Kubernetes 
https://azurecliprod.blob.core.windows.net/ | 443 | Inicializace cloudu | Stahování binárních souborů a kontejnerů 
https://aka.ms/installazurecliwindows | 443 | WAC | Stahuje se Azure CLI. 
*. api.cdp.microsoft.com, *. dl.delivery.mp.microsoft.com, *. emdl.ws.microsoft.com | 80, 443 | Stáhnout agenta | Stahují se metadata 
*. dl.delivery.mp.microsoft.com, *. do.dsp.mp.microsoft.com. | 80, 443 | Stáhnout agenta | Stahují se image VHD. 
ecpacr.azurecr.io | 443 | Kubernetes | Stahování imagí kontejneru 

### <a name="storage-requirements"></a>Požadavky na úložiště 

Služba Azure Kubernetes v Azure Stack HCL podporuje následující implementace úložiště: 

|  Název                         | Typ úložiště | Požadovaná kapacita |
| ---------------------------- | ------------ | ----------------- |
| Azure Stack clusteru HCI          | CSV          | 1 TB              |
| Jeden uzel Azure Stack HCI | Přímo připojené úložiště | 500 GB|

### <a name="review-maximum-supported-hardware-specifications"></a>Kontrola maximálního počtu podporovaných specifikací hardwaru 

Služba Azure Kubernetes na Azure Stack rozhraní HCI, která překračují následující specifikace, se nepodporují: 

| Prostředek                     | Maximum |
| ---------------------------- | --------|
| Fyzické servery na cluster | 4       |
| Clustery Kubernetes            | 4       |
| Celkový počet virtuálních počítačů          | 200     |

### <a name="windows-admin-center"></a>Centrum pro správu systému Windows 

Centrum pro správu Windows je uživatelské rozhraní pro vytváření a správu služby Azure Kubernetes v Azure Stack HCL. Pokud chcete používat centrum pro správu Windows se službou Azure Kubernetes v Azure Stack HCI, musíte splnit všechna kritéria uvedená v seznamu níže. 

#### <a name="on-your-windows-admin-center-system"></a>V systému centra pro správu systému Windows

Počítač, na kterém je spuštěná brána centra pro správu Windows, musí: 

 - Windows 10 (v současnosti nepodporujeme servery centra pro správu systému Windows.)
 - 60 GB volného místa
 - Registrováno v Azure
 - Ve stejné doméně jako cluster s Azure Stack HCI

## <a name="next-steps"></a>Další kroky 

Po splnění všech výše uvedených požadavků můžete nastavit hostitele služby Azure Kubernetes na Azure Stack HCL pomocí:
 - [Centrum pro správu systému Windows](setup.md)
 - [PowerShell](setup-powershell.md)
