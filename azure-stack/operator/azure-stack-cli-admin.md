---
title: Povolení Azure CLI pro uživatele Azure Stack | Microsoft Docs
description: Naučte se, jak povolit rozhraní příkazového řádku (CLI) pro různé platformy pro správu a nasazení prostředků v Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 05/16/2019
ms.author: mabrigg
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 6e901b2d806e85f7bc394dc9bee6412270753649
ms.sourcegitcommit: c196463492732218d2474d3a964f88e995272c80
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71094302"
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Povolení Azure CLI pro uživatele Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Kořenový certifikát certifikační autority můžete poskytnout uživatelům Azure Stack, aby mohli na svých vývojových počítačích povolit Azure CLI. Uživatelé potřebují certifikát pro správu prostředků přes rozhraní příkazového řádku.

 - Pokud uživatelé používají CLI z pracovní stanice mimo Azure Stack Development Kit (ASDK), je vyžadován **kořenový certifikát certifikační autority. Azure Stack**  

 - **Koncový bod aliasů virtuálního počítače** poskytuje alias, jako je například "UbuntuLTS" nebo "Win2012Datacenter", který odkazuje na vydavatele image, nabídku, SKU a verzi jako jeden parametr při nasazování virtuálních počítačů.  

Následující části popisují, jak tyto hodnoty získat.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Export kořenového certifikátu certifikační autority Azure Stack

Pokud používáte integrovaný systém, nemusíte exportovat kořenový certifikát certifikační autority. Je potřeba exportovat kořenový certifikát certifikační autority na ASDK.

Pokud chcete exportovat kořenový certifikát ASDK ve formátu PEM, přihlaste se a spusťte následující skript:

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Write-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-vm-aliases-endpoint"></a>Nastavení koncového bodu aliasů virtuálních počítačů

Operátoři Azure Stack by měli nastavit veřejně přístupný koncový bod, který hostuje soubor s aliasem virtuálního počítače. Soubor aliasu virtuálního počítače je soubor JSON, který poskytuje běžný název pro obrázek. Název použijete při nasazení virtuálního počítače jako parametru Azure CLI.  

Před přidáním položky do souboru s aliasem se ujistěte, že jste [stáhli obrázky z Azure Marketplace](azure-stack-download-azure-marketplace-item.md) nebo jste publikovali vlastní [Image](azure-stack-add-vm-image.md). Pokud publikujete vlastní image, poznamenejte si informace o vydavateli, nabídce, SKU a verzi, které jste zadali během publikování. Pokud se jedná o image z webu Marketplace, můžete si informace zobrazit pomocí `Get-AzureVMImage` rutiny.  

K dispozici je [ukázkový soubor aliasu](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) s mnoha běžnými aliasy obrázků. Můžete ho použít jako výchozí bod. Tento soubor hostovat v prostoru, kde se k němu klienti rozhraní příkazového řádku můžou připojit. Jedním ze způsobů je hostovat soubor v účtu BLOB Storage a sdílet adresu URL s uživateli:

1. Stáhněte si [ukázkový soubor](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) z GitHubu.
2. Vytvořte účet úložiště v Azure Stack. Až to bude hotové, vytvořte kontejner objektů BLOB. Nastavte zásady přístupu na veřejné.  
3. Nahrajte soubor JSON do nového kontejneru. Až to uděláte, můžete zobrazit adresu URL objektu BLOB. Vyberte název objektu BLOB a potom vyberte adresu URL z vlastností objektu BLOB.

## <a name="next-steps"></a>Další postup

- [Nasazení šablon pomocí Azure CLI](../user/azure-stack-deploy-template-command-line.md )
- [Připojení přes PowerShell](azure-stack-powershell-install.md)
- [Správa uživatelských oprávnění](azure-stack-manage-permissions.md)
