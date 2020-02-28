---
title: Nasazení virtuálního počítače centra Azure Stack pomocí hesla uloženého v Key Vault
description: Naučte se, jak nasadit virtuální počítač pomocí hesla uloženého v trezoru klíčů centra Azure Stack.
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: 7c68c2b5ecc5a56449e67dd0ffa403b3363cfab9
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77702886"
---
# <a name="deploy-an-azure-stack-hub-vm-using-a-password-stored-in-key-vault"></a>Nasazení virtuálního počítače centra Azure Stack pomocí hesla uloženého v Key Vault

Tento článek popisuje nasazení virtuálního počítače s Windows serverem pomocí hesla uloženého v Key Vault centra Azure Stack. Používání hesla trezoru klíčů je bezpečnější než předání hesla v prostém textu.

## <a name="overview"></a>Přehled

Do trezoru klíčů centra Azure Stack můžete ukládat hodnoty, jako je například heslo jako tajný kód. Po vytvoření tajného klíče na něj můžete odkazovat v Azure Resource Manager šablonách. Používání tajných kódů s Správce prostředků přináší následující výhody:

* Při každém nasazení prostředku není nutné zadávat tajný klíč ručně.
* Můžete určit, kteří uživatelé nebo instanční objekty budou mít přístup ke tajnému kódu.

## <a name="prerequisites"></a>Požadavky

* Musíte se přihlásit k odběru nabídky, která zahrnuje službu Key Vault.
* [Nainstalujte PowerShell pro centrum Azure Stack.](../operator/azure-stack-powershell-install.md)
* [Nakonfigurujte prostředí PowerShell.](azure-stack-powershell-configure-user.md)

Následující kroky popisují proces potřebný k vytvoření virtuálního počítače načtením hesla uloženého v Key Vault:

1. Vytvořte Key Vault tajný klíč.
2. Aktualizujte soubor `azuredeploy.parameters.json`.
3. Nasaďte šablonu.

> [!NOTE]  
> Pomocí těchto kroků můžete z Azure Stack Development Kit (ASDK) nebo z externího klienta, pokud jste připojení prostřednictvím sítě VPN.

## <a name="create-a-key-vault-secret"></a>Vytvoření tajného klíče Key Vault

Následující skript vytvoří Trezor klíčů a uloží heslo do trezoru klíčů jako tajný kód. Při vytváření trezoru klíčů použijte parametr `-EnabledForDeployment`. Tento parametr zajišťuje, že se Trezor klíčů může odkazovat z Azure Resource Manager šablon.

```powershell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
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

Po spuštění předchozího skriptu obsahuje výstup identifikátor URI tajného kódu (Uniform Resource Identifier). Poznamenejte si tento identifikátor URI. Je nutné, abyste na něj odkazovali v rámci [nasazení virtuálního počítače s Windows pomocí hesla v šabloně trezoru klíčů](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) . Stáhněte složku [101-VM-Secure-Password](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) do vývojového počítače. Tato složka obsahuje soubory `azuredeploy.json` a `azuredeploy.parameters.json`, které budete potřebovat v dalších krocích.

Upravte soubor `azuredeploy.parameters.json` podle hodnot vašich prostředí. Parametry zvláštního zájmu jsou název trezoru, skupina prostředků trezoru a identifikátor URI tajného kódu (jak je vygenerován předchozí skript). Níže uvedený soubor je příkladem souboru parametrů.

## <a name="update-the-azuredeployparametersjson-file"></a>Aktualizace souboru azuredeploy. Parameters. JSON

Aktualizujte soubor `azuredeploy.parameters.json` pomocí identifikátoru URI trezoru klíčů, tajného klíče adminUsername hodnot virtuálních počítačů na základě vašeho prostředí. Následující soubor JSON ukazuje příklad souboru parametrů šablony:

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

## <a name="template-deployment"></a>Nasazení šablony

Nyní šablonu nasaďte pomocí následujícího skriptu prostředí PowerShell:

```powershell  
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Po úspěšném nasazení šablony dojde k následujícímu výstupu:

![Výstup nasazení](media/azure-stack-key-vault-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>Další kroky

* [Nasazení ukázkové aplikace s Key Vault](azure-stack-key-vault-sample-app.md)
* [Nasazení virtuálního počítače s certifikátem Key Vault](azure-stack-key-vault-push-secret-into-vm.md)
