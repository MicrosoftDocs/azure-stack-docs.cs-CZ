---
title: Vytvoření virtuálního počítače s Linuxem pomocí Azure CLI ve službě Azure Stack | Dokumentace Microsoftu
description: Vytvoření virtuálního počítače s Linuxem pomocí Azure CLI ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/16/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: d47e5908e674a8b57b9e6d686e4596e1002b67c9
ms.sourcegitcommit: 2ee75ded704e8cfb900d9ac302d269c54a5dd9a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394399"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-the-azure-cli-in-azure-stack"></a>Rychlý start: Vytvoření virtuálního počítače s Linuxem serverem pomocí Azure CLI ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a sady Azure Stack Development Kit*

Virtuálního počítače s Ubuntu Server 16.04 LTS (VM) můžete vytvořit pomocí rozhraní příkazového řádku Azure. V tomto článku vytvořit a použít virtuální počítač. Tento článek také popisuje, jak do:

* Připojení k virtuálnímu počítači pomocí vzdáleného klienta.
* Nainstalovat webový server NGINX a zobrazit výchozí domovskou stránku.
* Vyčištění nevyužitých prostředků.

## <a name="prerequisites"></a>Požadavky

* Image Linuxu v Tržišti Azure Stack

   Azure Marketplace zásobník neobsahuje image Linuxu ve výchozím nastavení. Mají operátory Azure stacku, použijte image Ubuntu Server 16.04 LTS, co potřebujete. Operátor, který můžete použít pokyny v [Marketplace stažení položek z Azure do služby Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md).

* Azure Stack vyžaduje určitou verzi rozhraní příkazového řádku Azure můžete vytvořit a spravovat její prostředky. Pokud nemáte rozhraní příkazového řádku Azure, který je nakonfigurovaný pro službu Azure Stack, přihlaste se k [Azure Stack Development Kit](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) (nebo na základě Windows externí klienta Pokud jste [připojené prostřednictvím sítě VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)) a postupujte podle pokynů pro [instalace a konfigurace rozhraní příkazového řádku Azure](azure-stack-version-profiles-azurecli2.md).

* Veřejný klíč Secure Shell (SSH) s názvem *id_rsa.pub* uložené v *.ssh* adresáře vašeho profilu uživatele Windows. Podrobné informace o vytváření klíčů SSH najdete v tématu [použít veřejný klíč SSH](azure-stack-dev-start-howto-ssh-public-key.md).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém můžete nasadit a spravovat prostředky služby Azure Stack. Z vývojová sada nebo ve službě Azure Stack integrovaného systému, spustit [vytvořit skupiny az](/cli/azure/group#az-group-create) příkazu vytvořte skupinu prostředků.

> [!NOTE]
> Přiřadili jsme hodnoty pro všechny proměnné v následujících příkladech kódu. Však můžete přiřadit vlastní hodnoty.

Následující příklad vytvoří skupinu prostředků myResourceGroup v místním umístění: 

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače pomocí [az vm vytvořit](/cli/azure/vm#az-vm-create) příkazu. Následující příklad vytvoří virtuální počítač s názvem můjvp přesměrovat. V příkladu *Demouser* jako uživatelské jméno správce a *Demouser@123* jako heslo správce. Tyto hodnoty změňte na něco, co je pro vaše prostředí vhodný.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

Veřejnou IP adresu se vrátí v **PublicIpAddress** parametru. Poznamenejte si adresu pro pozdější použití s virtuálním počítačem.

## <a name="open-port-80-for-web-traffic"></a>Otevření portu 80 pro webový provoz

Protože tento virtuální počítač bude spouštět na webovém serveru IIS, budete muset otevřít port 80 pro přenosy z Internetu. Chcete-li otevřít port, použijte [az vm open-port](/cli/azure/vm) příkaz: 

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Pomocí SSH se připojte k virtuálnímu počítači

Z klientského počítače pomocí protokolu SSH, nainstalovat připojte k virtuálnímu počítači. Pokud pracujete na klientovi Windows, použijte [PuTTY](https://www.putty.org/) k vytvoření připojení. Pro připojení k virtuálnímu počítači, použijte následující příkaz:

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>Instalace webového serveru NGINX

Pokud chcete aktualizaci zdrojů balíčku a nainstalujete nejnovější balíček NGINX, spusťte následující skript:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Zobrazení úvodní stránky serveru NGINX

Webový server NGINX nainstalovaný a port 80 otevřený ve vašem virtuálním počítači můžete přístup k webovému serveru s použitím veřejné IP adresy virtuálního počítače. Uděláte to tak, otevřete prohlížeč a přejděte na ```http://<public IP address>```.

![Úvodní stránku serveru NGINX webového serveru](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte prostředky, které už nepotřebujete. Můžete použít [odstranění skupiny az](/cli/azure/group#az-group-delete) příkaz, který je odebrat. Spusťte následující příkaz:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nasadili základní server virtuálního počítače s Linuxem s webovým serverem. Další informace o virtuálních počítačích Azure Stack, najdete v článku [důležité informace týkající se virtuálních počítačů ve službě Azure Stack](azure-stack-vm-considerations.md).
