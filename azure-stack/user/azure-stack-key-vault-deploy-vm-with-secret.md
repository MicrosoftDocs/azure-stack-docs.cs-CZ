---
title: Nasazení virtuálního počítače centra Azure Stack pomocí hesla uloženého v Key Vault
description: Naučte se, jak nasadit virtuální počítač pomocí hesla uloženého v trezoru klíčů centra Azure Stack.
author: mattbriggs
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: 963a4d7bc6cd61ae724e4f087861e19d918fea7d
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95518173"
---
# <a name="deploy-an-azure-stack-hub-vm-using-a-password-stored-in-key-vault"></a>Nasazení virtuálního počítače centra Azure Stack pomocí hesla uloženého v Key Vault

Tento článek popisuje nasazení virtuálního počítače s Windows serverem pomocí hesla uloženého v Key Vault centra Azure Stack. Používání hesla trezoru klíčů je bezpečnější než předání hesla v prostém textu.

## <a name="overview"></a>Přehled

Do trezoru klíčů centra Azure Stack můžete ukládat hodnoty, jako je například heslo jako tajný kód. Po vytvoření tajného klíče na něj můžete odkazovat v Azure Resource Manager šablonách. Používání tajných kódů s Správce prostředků přináší následující výhody:

* Při každém nasazení prostředku není nutné zadávat tajný klíč ručně.
* Můžete určit, kteří uživatelé nebo instanční objekty budou mít přístup ke tajnému kódu.

## <a name="prerequisites"></a>Předpoklady

* Musíte se přihlásit k odběru nabídky, která zahrnuje službu Key Vault.
* [Nainstalujte PowerShell pro centrum Azure Stack.](../operator/powershell-install-az-module.md)
* [Nakonfigurujte prostředí PowerShell.](azure-stack-powershell-configure-user.md)

Následující kroky popisují proces potřebný k vytvoření virtuálního počítače načtením hesla uloženého v Key Vault:

1. Vytvořte Key Vault tajný klíč.
2. Aktualizujte `azuredeploy.parameters.json` soubor.
3. Nasazení šablony

> [!NOTE]  
> Pomocí těchto kroků můžete z Azure Stack Development Kit (ASDK) nebo z externího klienta, pokud jste připojení prostřednictvím sítě VPN.

## <a name="create-a-key-vault-secret"></a>Vytvoření tajného klíče Key Vault

Následující skript vytvoří Trezor klíčů a uloží heslo do trezoru klíčů jako tajný kód. `-EnabledForDeployment`Při vytváření trezoru klíčů použijte parametr. Tento parametr zajišťuje, že se Trezor klíčů může odkazovat z Azure Resource Manager šablon.

### <a name="az-modules"></a>[AZ modules](#tab/az1)

```powershell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm1)

```powershell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRMResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRMKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```
---

Po spuštění předchozího skriptu obsahuje výstup identifikátor URI tajného kódu (Uniform Resource Identifier). Poznamenejte si tento identifikátor URI. Je nutné, abyste na něj odkazovali v rámci [nasazení virtuálního počítače s Windows pomocí hesla v šabloně trezoru klíčů](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) . Stáhněte složku [101-VM-Secure-Password](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) do vývojového počítače. Tato složka obsahuje `azuredeploy.json` soubory a `azuredeploy.parameters.json` , které budete potřebovat v dalších krocích.

Upravte `azuredeploy.parameters.json` soubor podle hodnot vašich prostředí. Parametry zvláštního zájmu jsou název trezoru, skupina prostředků trezoru a identifikátor URI tajného kódu (jak je vygenerován předchozí skript). Níže uvedený soubor je příkladem souboru parametrů.

## <a name="update-the-azuredeployparametersjson-file"></a>Aktualizovat azuredeploy.parameters.jsv souboru

Aktualizujte `azuredeploy.parameters.json` soubor pomocí identifikátoru URI trezoru klíčů, tajného klíče, adminUsername hodnot virtuálních počítačů na základě vašeho prostředí. Následující soubor JSON ukazuje příklad souboru parametrů šablony:

```json
{
    "$schema":  "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
       "adminPassword":  {
         "reference":  {
            "keyVault":  {
              "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
              },
            "secretName":  "MySecret"
         }
       },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>Nasazení šablon

Nyní šablonu nasaďte pomocí následujícího skriptu prostředí PowerShell:

### <a name="az-modules"></a>[AZ modules](#tab/az2)

```powershell  
New-AzResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```
### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm2)

```powershell  
New-AzureRMResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

---


Po úspěšném nasazení šablony dojde k následujícímu výstupu:

![Výstup nasazení](media/azure-stack-key-vault-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>Další kroky

* [Nasazení ukázkové aplikace s Key Vault](azure-stack-key-vault-sample-app.md)
* [Nasazení virtuálního počítače s certifikátem Key Vault](azure-stack-key-vault-push-secret-into-vm.md)
