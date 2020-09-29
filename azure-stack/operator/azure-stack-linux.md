---
title: Přidání imagí pro Linux do webu centra Azure Stack Marketplace
description: Naučte se přidávat image Linux do Marketplace centra Azure Stack.
author: sethmanheim
ms.topic: article
ms.date: 08/24/2020
ms.author: sethm
ms.reviewer: ''
ms.lastreviewed: 11/16/2019
ms.openlocfilehash: 32e16bac548193a7c0af517c3a60e78a3e52f3ce
ms.sourcegitcommit: d73637146daaba0ef0ab1729683bb52c65466099
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88857531"
---
# <a name="add-linux-images-to-the-azure-stack-hub-marketplace"></a>Přidání imagí pro Linux do webu centra Azure Stack Marketplace

Virtuální počítače se systémem Linux můžete do centra Azure Stack nasadit přidáním image založené na systému Linux na tržiště Azure Stack hub. Nejjednodušší způsob, jak přidat image pro Linux do centra Azure Stack, je prostřednictvím správy Marketplace. Tyto Image byly připraveny a testovány kvůli kompatibilitě s Azure Stackm centrem.

## <a name="marketplace-management"></a>Správa Marketplace

Pokud chcete stáhnout image ze systému Linux z Azure Marketplace, přečtěte si téma [stažení položek Marketplace z Azure do centra Azure Stack](azure-stack-download-azure-marketplace-item.md). Vyberte image pro Linux, které chcete uživatelům nabídnout v centru Azure Stack.

Tyto image se často aktualizují, proto je pravidelně Projděte.

## <a name="prepare-your-own-image"></a>Příprava vlastní image

Pokud je to možné, Stáhněte si image dostupné prostřednictvím správy Marketplace. Tyto Image byly připraveny a testovány pro centrum Azure Stack.

### <a name="azure-linux-agent"></a>Agent Azure Linux

Je vyžadován agent Azure Linux (obvykle označovaný jako **WALinuxAgent** nebo **WALinuxAgent**) a ne všechny verze agenta fungují v centru Azure Stack. Verze mezi 2.2.21 a 2.2.34 (včetně) se v Azure Stackovém centru nepodporují. Pokud chcete použít nejnovější verze agenta výše než 2.2.35, použijte opravu hotfix 1901 hotfix/1902 nebo aktualizujte centrum Azure Stack na verzi 1903 (nebo novější). Všimněte si, že [Cloud-init](https://cloud-init.io/) se podporuje v Azure Stack vydáních centra později než 1910.

| Sestavování centra Azure Stack | Sestavení agenta Azure Linux |
| ------------- | ------------- |
| 1.1901.0.99 nebo starší | 2.2.20 |
| 1.1902.0.69  | 2.2.20  |
|  1.1901.3.105   | 2.2.35 nebo novější |
| 1.1902.2.73  | 2.2.35 nebo novější |
| 1.1903.0.35  | 2.2.35 nebo novější |
| Build po 1903 | 2.2.35 nebo novější |
| Nepodporováno | 2.2.21-2.2.34 |
| Build po 1910 | Všechny verze agenta Azure WALA|

Vlastní image pro Linux můžete připravit pomocí následujících pokynů:

* [Distribuce založené na CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES a openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="cloud-init"></a>Cloud-init

[Cloud-init](https://cloud-init.io/) se podporuje v Azure Stack vydání centra později než 1910. Pokud chcete použít Cloud-init k přizpůsobení virtuálního počítače se systémem Linux, můžete použít následující pokyny prostředí PowerShell.

### <a name="step-1-create-a-cloud-inittxt-file-with-your-cloud-config"></a>Krok 1: vytvoření souboru cloud-init.txt s cloudovou konfigurací

Vytvořte soubor s názvem cloud-init.txt a vložte následující konfiguraci cloudu:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
  ```
  
### <a name="step-2-reference-cloud-inittxt-during-the-linux-vm-deployment"></a>Krok 2: referenční cloud-init.txt během nasazování virtuálního počítače se systémem Linux

Nahrajte soubor do účtu služby Azure Storage, Azure Stack účtu úložiště centra nebo úložiště GitHub dosažitelného pomocí virtuálního počítače Azure Stack hub Linux.
V současné době se použití Cloud-init pro nasazení virtuálního počítače podporuje jenom v REST, PowerShellu a rozhraní příkazového řádku a nemá přidružené uživatelské rozhraní portálu na Azure Stack hub.

Podle [těchto pokynů](../user/azure-stack-quick-create-vm-linux-powershell.md) můžete vytvořit virtuální počítač Linux pomocí prostředí PowerShell, ale nezapomeňte na cloud-init.txt odkazovat jako na součást `-CustomData` příznaku:

```powershell
$VirtualMachine =Set-AzureRmVMOperatingSystem -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred -CustomData "#include https://cloudinitstrg.blob.core.windows.net/strg/cloud-init.txt"
```

## <a name="add-your-image-to-marketplace"></a>Přidání image do Marketplace

Postupujte podle pokynů [Přidat obrázek na Marketplace](azure-stack-add-vm-image.md). Ujistěte se, že `OSType` je parametr nastaven na hodnotu `Linux` .

Po přidání image na Marketplace se vytvoří položka Marketplace a uživatelé můžou nasadit virtuální počítač se systémem Linux.

## <a name="next-steps"></a>Další kroky

* [Stažení položek z webu Marketplace z Azure do centra Azure Stack](azure-stack-download-azure-marketplace-item.md)
* [Přehled centra Azure Stack Marketplace](azure-stack-marketplace.md)
