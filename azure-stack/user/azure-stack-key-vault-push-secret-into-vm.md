---
title: Nasazení virtuálního počítače s zabezpečeným uloženým certifikátem v centru Azure Stack
description: Přečtěte si, jak nasadit virtuální počítač a vložit do něj certifikát pomocí trezoru klíčů v centru Azure Stack.
author: sethmanheim
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: sethm
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: a326004b5ed100b0fc7eeb841dd0fbc4ded9ee5a
ms.sourcegitcommit: b50dd116d6d1f89d42bd35ad0f85bb25c5192921
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "95518156"
---
# <a name="deploy-a-vm-with-a-securely-stored-certificate-on-azure-stack-hub"></a>Nasazení virtuálního počítače s zabezpečeným uloženým certifikátem v centru Azure Stack

Tento článek popisuje, jak nasadit virtuální počítač s Azure Stack hub s nainstalovaným certifikátem Key Vault.

## <a name="overview"></a>Přehled

Certifikáty se používají v mnoha scénářích, jako je ověřování ve službě Active Directory nebo šifrování webového provozu. Certifikáty můžete bezpečně ukládat jako tajné klíče v trezoru klíčů centra Azure Stack. Mezi výhody použití centra Azure Stack Key Vault patří:

* Certifikáty nejsou zveřejněné ve skriptu, historii příkazového řádku nebo šabloně.
* Proces správy certifikátů je zjednodušený.
* Máte kontrolu nad klíči, které přistupují k certifikátům.

## <a name="process-description"></a>Popis procesu

Následující kroky popisují proces vyžadovaný k odeslání certifikátu do virtuálního počítače:

1. Vytvoření tajného klíče trezoru klíčů
2. Aktualizuje **azuredeploy.parameters.jsv** souboru.
3. Nasazení šablony

> [!NOTE]
> Pomocí těchto kroků můžete z Azure Stack Development Kit (ASDK) nebo z externího klienta, pokud jste připojení prostřednictvím sítě VPN.

## <a name="prerequisites"></a>Předpoklady

* Musíte se přihlásit k odběru nabídky, která zahrnuje službu Key Vault.
* [Nainstalujte PowerShell pro centrum Azure Stack](../operator/powershell-install-az-module.md).
* [Nakonfigurujte prostředí PowerShell uživatele centra Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="create-a-key-vault-secret"></a>Vytvoření tajného klíče trezoru klíčů

Následující skript vytvoří certifikát ve formátu. pfx, vytvoří Trezor klíčů a uloží certifikát do trezoru klíčů jako tajný kód. `contentType`Tajný klíč musí být nastaven na hodnotu `pfx` .

> [!IMPORTANT]
> `-EnabledForDeployment`Při vytváření trezoru klíčů musíte použít parametr. Tento parametr zajišťuje, aby se Trezor klíčů mohl odkazovat z Azure Resource Manager šablon.

### <a name="az-modules"></a>[AZ modules](#tab/az)

```powershell
# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<certificate thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path to where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path to where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret
```
### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm)

```powershell
# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<certificate thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path to where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path to where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRMResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRMKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret
```

---



Při spuštění tohoto skriptu obsahuje výstup identifikátor URI tajného kódu. Poznamenejte si tento identifikátor URI, protože ho musíte odkázat do [šablony push Certificate pro Windows Správce prostředků](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate). Stáhněte si do vývojového počítače složku [VM-push-Certificate-Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) Template. Tato složka obsahuje **azuredeploy.js** a **azuredeploy.parameters.js** se soubory, které potřebujete v následujících krocích.

Upravte **azuredeploy.parameters.js** souboru podle hodnot vašich prostředí. Důležité parametry jsou název trezoru, skupina prostředků trezoru a identifikátor URI tajného kódu (jak je vygenerován předchozí skript). Následující část ukazuje příklad souboru parametrů.

## <a name="update-the-azuredeployparametersjson-file"></a>Aktualizovat azuredeploy.parameters.jsv souboru

Aktualizujte **azuredeploy.parameters.jsv** souboru pomocí `vaultName` identifikátoru URI, tajného klíče, `VmName` a dalších parametrů, které jsou na vašem prostředí. Následující soubor JSON ukazuje příklad souboru parametrů šablony:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>Nasazení šablony

Nasaďte šablonu pomocí následujícího skriptu prostředí PowerShell:

### <a name="az-modules"></a>[AZ modules](#tab/az2)

```powershell
# Deploy a Resource Manager template to create a VM and push the secret to it
New-AzResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```
### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm2)

```powershell
# Deploy a Resource Manager template to create a VM and push the secret to it
New-AzureRMResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```
---



Po úspěšném nasazení šablony se zobrazí následující výstup:

![Výsledky Template deployment](media/azure-stack-key-vault-push-secret-into-vm/deployment-output.png)

Centrum Azure Stack během nasazování vloží certifikát do virtuálního počítače. Umístění certifikátu závisí na operačním systému virtuálního počítače:

* V systému Windows se certifikát přidá do umístění certifikátu **LocalMachine** s úložištěm certifikátů, které zadal uživatel.
* V systému Linux je certifikát umístěn v adresáři **/var/lib/waagent** s názvem souboru **UppercaseThumbprint. CRT** pro soubor certifikátu x509 a **UppercaseThumbprint. prv** pro privátní klíč.

## <a name="retire-certificates"></a>Vyřazení certifikátů

Vyřazení certifikátů je součástí procesu správy certifikátů. Starší verzi certifikátu nelze odstranit, ale můžete ji zakázat pomocí `Set-AzureKeyVaultSecretAttribute` rutiny.

Následující příklad ukazuje, jak zakázat certifikát. Použijte vlastní hodnoty pro `VaultName` `Name` parametry, a `Version` .

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Další kroky

* [Nasazení virtuálního počítače s heslem Key Vaultu](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Povolení přístupu aplikace k Key Vault](azure-stack-key-vault-sample-app.md)
