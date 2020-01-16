---
title: Vytvoření virtuálního počítače s Windows na Azure Stackovém centru pomocí Azure CLI | Microsoft Docs
description: Vytvoření virtuálního počítače s Windows v Azure Stackovém centru pomocí Azure CLI
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 6a5a389082fa89162023205a2784457b15ccca6b
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76023083"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-using-azure-cli-in-azure-stack-hub"></a>Rychlý Start: Vytvoření virtuálního počítače s Windows serverem pomocí rozhraní příkazového řádku Azure v centru Azure Stack

Virtuální počítač s Windows serverem 2016 můžete vytvořit pomocí rozhraní příkazového řádku Azure CLI. Pokud chcete vytvořit a použít virtuální počítač, postupujte podle kroků v tomto článku. Tento článek také obsahuje následující kroky:

* Připojte se k virtuálnímu počítači pomocí vzdáleného klienta.
* Nainstalujte webový server služby IIS a zobrazte výchozí domovskou stránku.
* Vyčistěte prostředky.

## <a name="prerequisites"></a>Požadavky

* Ujistěte se, že váš operátor centra Azure Stack přidal do tržiště centra Azure Stack image **Windows serveru 2016** .

* Aby bylo možné vytvořit a spravovat prostředky, Azure Stack centrum vyžaduje specifickou verzi rozhraní příkazového řádku Azure CLI. Pokud nemáte rozhraní příkazového řádku Azure nakonfigurované pro centrum Azure Stack, postupujte podle pokynů k [instalaci a konfiguraci rozhraní příkazového řádku Azure CLI](azure-stack-version-profiles-azurecli2.md).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém můžete nasazovat a spravovat prostředky centra Azure Stack. V prostředí Azure Stack hub spuštěním příkazu [AZ Group Create](/cli/azure/group#az-group-create) vytvořte skupinu prostředků.

> [!NOTE]
>  Hodnoty jsou přiřazeny pro všechny proměnné v příkladech kódu. V případě potřeby však můžete přiřadit nové hodnoty.

Následující příklad vytvoří skupinu prostředků s názvem myResourceGroup v místním umístění:

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Pomocí příkazu [AZ VM Create](/cli/azure/vm#az-vm-create) vytvořte virtuální počítač (VM). Následující příklad vytvoří virtuální počítač s názvem myVM. V tomto příkladu se používá myš pro uživatelské jméno správce a Demouser@123 jako heslo správce. Změňte tyto hodnoty na něco, co je vhodné pro vaše prostředí.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

Při vytvoření virtuálního počítače obsahuje parametr **PublicIPAddress** ve výstupu veřejnou IP adresu virtuálního počítače. Zapište tuto adresu, protože ji budete potřebovat k použití virtuálního počítače.

## <a name="open-port-80-for-web-traffic"></a>Otevření portu 80 pro webový provoz

Vzhledem k tomu, že tento virtuální počítač bude spouštět webový server služby IIS, musíte otevřít port 80 pro internetový provoz.

K otevření portu 80 použijte příkaz [AZ VM Open-port](/cli/azure/vm) :

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

Pomocí dalšího příkazu vytvořte připojení ke vzdálené ploše virtuálního počítače. Nahraďte "veřejnou IP adresou" IP adresou vašeho virtuálního počítače. Po zobrazení výzvy zadejte uživatelské jméno a heslo, které jste použili pro virtuální počítač.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Instalace služby IIS pomocí PowerShellu

Teď, když jste se přihlásili k virtuálnímu počítači, můžete k instalaci služby IIS použít PowerShell. Spusťte na virtuálním počítači PowerShell a spusťte následující příkaz:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Zobrazení úvodní stránky služby IIS

K zobrazení výchozí uvítací stránky služby IIS můžete použít prohlížeč podle svého výběru. K návštěvě výchozí stránky použijte veřejnou IP adresu uvedenou v předchozí části:

![Výchozí web služby IIS](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte prostředky, které už nepotřebujete. Pomocí příkazu [AZ Group Delete](/cli/azure/group#az-group-delete) odeberte skupinu prostředků, virtuální počítač a všechny související prostředky.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili základní virtuální počítač s Windows serverem. Pokud chcete získat další informace o virtuálních počítačích s Azure Stack hub, pokračujte [v otázkách Virtual Machines v centru Azure Stack](azure-stack-vm-considerations.md).
