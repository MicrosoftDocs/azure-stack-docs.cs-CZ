---
title: Vytvoření virtuálního počítače se systémem Linux pomocí rozhraní příkazového řádku Azure v centru Azure Stack
description: Vytvořte virtuální počítač se systémem Linux pomocí rozhraní příkazového řádku Azure v centru Azure Stack.
author: mattbriggs
ms.topic: quickstart
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: e1444688d529b2bce148e96dff745b0a86e37a18
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884523"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-the-azure-cli-in-azure-stack-hub"></a>Rychlý Start: Vytvoření virtuálního počítače s Linux serverem pomocí rozhraní příkazového řádku Azure v centru Azure Stack

Virtuální počítač s Ubuntu serverem 16,04 LTS můžete vytvořit pomocí rozhraní příkazového řádku Azure. V tomto článku vytvoříte a použijete virtuální počítač. Tento článek také ukazuje, jak:

* Připojte se k virtuálnímu počítači pomocí vzdáleného klienta.
* Nainstalujte webový server NGINX a zobrazte výchozí domovskou stránku.
* Vyčistit nepoužívané prostředky.

## <a name="prerequisites"></a>Požadavky

* Image Linux na webu centra Azure Stack Marketplace

   Tržiště centra Azure Stack ve výchozím nastavení neobsahuje image Linux. Použijte operátor centra Azure Stack, který obsahuje bitovou kopii Ubuntu serveru 16,04 LTS, kterou potřebujete. Operátor může použít pokyny v tématu [stažení položek Marketplace z Azure do centra Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md).

* Aby bylo možné vytvářet a spravovat prostředky, Azure Stack centrum vyžaduje specifickou verzi rozhraní příkazového řádku Azure. Pokud nemáte rozhraní příkazového řádku Azure nakonfigurované pro centrum Azure Stack, přihlaste se k [Azure Stack Development Kit](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) (nebo externímu klientovi se systémem Windows, pokud jste [připojení prostřednictvím sítě VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)) a postupujte podle pokynů k [instalaci a konfiguraci rozhraní příkazového řádku Azure CLI](azure-stack-version-profiles-azurecli2.md).

* Klíč veřejného Secure Shell (SSH) s názvem *id_rsa. pub* uložený v adresáři *. ssh* uživatelského profilu Windows. Podrobné informace o vytváření klíčů SSH najdete v tématu [použití veřejného klíče SSH](azure-stack-dev-start-howto-ssh-public-key.md).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém můžete nasazovat a spravovat prostředky centra Azure Stack. Z vývojové sady nebo integrovaného systému centra Azure Stack vytvořte skupinu prostředků spuštěním příkazu [AZ Group Create](/cli/azure/group#az-group-create) .

> [!NOTE]
> V následujících příkladech kódu jsme přiřadili hodnoty pro všechny proměnné. Můžete ale přiřadit vlastní hodnoty.

Následující příklad vytvoří skupinu prostředků s názvem myResourceGroup v místním umístění: 

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Pomocí příkazu [AZ VM Create](/cli/azure/vm#az-vm-create) vytvořte virtuální počítač. Následující příklad vytvoří virtuální počítač s názvem myVM. V příkladu se jako uživatelské jméno správce používá *myš* a jako heslo správce *Demouser@123* . Změňte tyto hodnoty na něco, co je vhodné pro vaše prostředí.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

Veřejná IP adresa se vrátí v parametru **PublicIpAddress** . Poznamenejte si adresu pro pozdější použití s virtuálním počítačem.

## <a name="open-port-80-for-web-traffic"></a>Otevření portu 80 pro webový provoz

Vzhledem k tomu, že tento virtuální počítač bude spouštět webový server služby IIS, je nutné otevřít port 80 pro internetový provoz. Chcete-li otevřít port, použijte příkaz [AZ VM Open-port](/cli/azure/vm) : 

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>Použití SSH pro připojení k virtuálnímu počítači

V klientském počítači s nainstalovaným SSH se připojte k virtuálnímu počítači. Pokud pracujete na klientovi Windows, vytvořte připojení pomocí [výstupu](https://www.putty.org/) . Pokud se chcete připojit k virtuálnímu počítači, použijte následující příkaz:

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>Instalace webového serveru NGINX

Pokud chcete aktualizovat prostředky balíčku a nainstalovat nejnovější balíček NGINX, spusťte následující skript:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Zobrazení úvodní stránky serveru NGINX

Po instalaci webového serveru NGINX a otevření portu 80 na virtuálním počítači můžete k webovému serveru přistupovat pomocí veřejné IP adresy virtuálního počítače. Provedete to tak, že otevřete prohlížeč a přejdete na ```http://<public IP address>```.

![Úvodní stránka webového serveru NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte prostředky, které už nepotřebujete. K jejich odebrání můžete použít příkaz [AZ Group Delete](/cli/azure/group#az-group-delete) . Spusťte následující příkaz:

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili základní virtuální počítač s Linux serverem s webovým serverem. Další informace o virtuálních počítačích s Azure Stack hub najdete [v tématu informace o virtuálních počítačích v centru Azure Stack](azure-stack-vm-considerations.md).
