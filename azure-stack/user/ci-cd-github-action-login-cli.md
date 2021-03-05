---
title: Použití akce přihlášení Azure pomocí Azure CLI a PowerShellu v centru Azure Stack
description: Použití akce přihlášení Azure s Azure CLI a PowerShellem k vytvoření pracovního postupu průběžné integrace, průběžného nasazování (CI/CD) v centru Azure Stack
author: mattbriggs
ms.topic: how-to
ms.date: 1/11/2021
ms.author: mabrigg
ms.reviewer: gara
ms.lastreviewed: 1/11/2021
ms.openlocfilehash: 1421917f870d09d61f665a2cee6eb9b617ae75f3
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102187346"
---
# <a name="use-the-azure-login-action-with-azure-cli-and-powershell-on-azure-stack-hub"></a>Použití akce přihlášení Azure pomocí Azure CLI a PowerShellu v centru Azure Stack

Můžete nastavit akce GitHubu pro přihlášení k instanci centra Azure Stack, spuštění PowerShellu a následné spuštění skriptu Azure CLI. Můžete ho použít jako základ pracovního postupu průběžné integrace, průběžného nasazování (CI/CD) pro vaše řešení pomocí centra Azure Stack. Pomocí tohoto pracovního postupu můžete automatizovat sestavování, testování a nasazení vašeho řešení, abyste se mohli soustředit na psaní kódu. Například přidáním některých dalších akcí můžete použít tento pracovní postup spolu se šablonou Azure Resource Manager k zřízení virtuálního počítače, ověření úložiště aplikace a následnému nasazení aplikace na tento virtuální počítač při každém sloučení do konkrétní větve v GitHubu. Prozatím vám tento článek pomůže se seznámení s akcemi GitHubu a centrem Azure Stack.

Akce GitHubu jsou pracovní postupy tvořené akcemi, které umožňují automatizaci přímo uvnitř úložiště kódu. Pracovní postupy můžete aktivovat s událostmi v procesu vývoje GitHubu. Můžete provádět běžné úlohy automatizace DevOps, jako je testování, nasazení a průběžná integrace.

Pokud chcete použít akce GitHubu s Azure Stack hub, musíte použít instanční objekt (SPN) se specifickými požadavky. V tomto článku vytvoříte *spouštěč samoobslužného hostování*. GitHub vám umožňuje používat libovolný počítač, na který je možné oslovit GitHub ve svých akcích GitHubu. Můžete vytvořit virtuální počítač (VM) jako spouštěč v Azure, v Azure Stackovém centru nebo jinde.

Tento ukázkový pracovní postup obsahuje:
- Pokyny k vytvoření a ověření hlavního názvu služby (SPN).
- Konfigurace základního počítače se systémem Windows 2016 jako akce GitHub v rámci samoobslužného spouštěče pro práci s rozbočovačem Azure Stack.
- Pracovní postup, který používá:
    - Akce přihlášení k Azure
    - Akce skriptu PowerShellu

### <a name="azure-stack-hub-github-actions"></a>Akce GitHubu centra Azure Stack

Následující diagram znázorňuje různá prostředí a jejich vztahy.

![Části akce GitHub centra Azure Stack s ](.\media\ci-cd-github-action-login-cli\ash-github-actions-v1d1.svg) využitím spouštěče webhosta:

- Akce GitHubu hostované na GitHubu
- Samoobslužný spouštěč hostovaný v Azure
- Azure Stack Hub

Omezení použití akcí GitHubu s centrem Azure Stack je v tom, že proces vyžaduje použití centra Azure Stack připojeného k webu. Pracovní postup se aktivuje v úložišti GitHubu. Jako zprostředkovatele identity můžete použít jak Azure Active Directory (Azure AD), nebo službu Active Directory federované služby (AD FS).

I když se jedná o obor tohoto článku, váš spouštěč v místním prostředí může také pomocí virtuální privátní sítě připojit se k centru Azure Stack za bránou firewall.

## <a name="get-service-principal"></a>Získat instanční objekt

Hlavní název služby (SPN) poskytuje přihlašovací údaje založené na rolích, aby se procesy mimo Azure mohly připojit k prostředkům a pracovat s nimi. Budete potřebovat hlavní název služby (SPN) s přístupem přispěvatel a atributy uvedené v těchto pokynech pro použití s akcemi GitHubu.

Jako uživatel centra Azure Stack nemáte oprávnění k vytvoření hlavního názvu služby (SPN). Tuto zásadu budete muset požádat z operátora cloudu. Tady najdete pokyny, abyste mohli vytvořit hlavní název služby (SPN) v případě, že jste operátor cloudu, nebo můžete ověřit hlavní název služby (SPN), pokud jste vývojář pomocí hlavního názvu služby (SPN) v pracovním postupu, který poskytuje operátor cloudu.

Operátor cloudu bude muset vytvořit hlavní název služby (SPN) pomocí rozhraní příkazového řádku Azure CLI.

Následující fragmenty kódu se zapisují pro počítač s Windows pomocí příkazového řádku PowerShellu s Azure CLI. Pokud na počítači se systémem Linux a bash používáte rozhraní příkazového řádku (CLI), buď odeberte příponu linky, nebo je nahraďte příponou `\` .

1. Připravte hodnoty následujících parametrů použitých k vytvoření hlavního názvu služby (SPN):

    | Parametr | Příklad | Popis |
    | --- | --- | --- |
    koncový bod – Resource Manager | "https://management.orlando.azurestack.corp.microsoft.com"  | Koncový bod správy prostředků. |
    Přípona – Storage-koncový bod | "orlando.azurestack.corp.microsoft.com"  | Přípona koncového bodu pro účty úložiště. |
    Přípona – Trezor klíčů – DNS | ". vault.orlando.azurestack.corp.microsoft.com"  | Přípona DNS Key Vault služby. |
    koncový bod-Active-Directory-Graph-Resource-ID | "https://graph.windows.net/"  | Identifikátor prostředku služby Azure Active Directory. |
    koncový bod-SQL – Správa | https://notsupported  | Koncový bod správy SQL serveru. Nastavte tuto hodnotu na `https://notsupported` |
    profil | 2019-03-01 – Hybrid | Profil, který se má použít pro tento Cloud. |

2. Otevřete nástroj příkazového řádku, jako je Windows PowerShell nebo bash, a přihlaste se. Použijte následující příkaz:

    ```azurecli  
    az login
    ```

3. Použijte `register` příkaz pro nové prostředí nebo `update` příkaz, pokud používáte existující prostředí. Použijte následující příkaz.

    ```azurecli  
    az cloud register `
        -n "AzureStackUser" `
        --endpoint-resource-manager "https://management.<local>.<FQDN>" `
        --suffix-storage-endpoint ".<local>.<FQDN>" `
        --suffix-keyvault-dns ".vault.<local>.<FQDN>" `
        --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" `
        --endpoint-sql-management https://notsupported  `
        --profile 2019-03-01-hybrid
    ```

4. Získejte ID předplatného a skupinu prostředků, kterou chcete použít pro hlavní název služby (SPN).

5. Vytvořte hlavní název služby (SPN) pomocí následujícího příkazu s ID předplatného a skupinou prostředků:

    ```azurecli  
    az ad sp create-for-rbac --name "myApp" --role contributor `
        --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} `
        --sdk-auth
    ```

6. Zkontroluje výsledný objekt JSON. Pomocí objektu JSON vytvoříte v úložišti GitHub svůj tajný klíč, který bude obsahovat vaši akci. Objekt JSON by měl mít následující atributy:

    ```json
    {
      "clientId": <Application ID for the SPN>,
      "clientSecret": <Client secret for the SPN>,
      "subscriptionId": <Subscription ID for the SPN>,
      "tenantId": <Tenant ID for the SPN>,
      "activeDirectoryEndpointUrl": "https://login.microsoftonline.com/",
      "resourceManagerEndpointUrl": "https://management.<FQDN>",
      "activeDirectoryGraphResourceId": "https://graph.windows.net/",
      "sqlManagementEndpointUrl": "https://notsupported",
      "galleryEndpointUrl": "https://providers.<FQDN>:30016/",
      "managementEndpointUrl": "https://management.<FQDN>"
    }
    ```

## <a name="add-service-principal-to-repository"></a>Přidání instančního objektu do úložiště

K šifrování citlivých informací, které se mají použít ve svých akcích, můžete použít tajné kódy GitHubu. Vytvoříte tajný klíč, který bude obsahovat hlavní název služby (SPN), aby se akce mohla přihlásit ke své instanci centra Azure Stack.

> [!WARNING]  
> GitHub doporučuje, abyste nepoužívali samoobslužné spouštěče s veřejnými větvemi úložiště ve veřejném úložišti, a to vytvořením žádosti o přijetí změn, která spustí kód v pracovním postupu. Další informace najdete v tématu[o spouštěčích s místním hostováním](https://docs.github.com/en/free-pro-team@latest/github/automating-your-workflow-with-github-actions/about-self-hosted-runners#self-hosted-runner-security-with-public-repositories).

1. Otevřete nebo vytvořte úložiště GitHub. Pokud potřebujete pokyny k vytvoření úložiště v GitHubu, najdete [pokyny v dokumentaci k GitHubu](https://docs.github.com/en/free-pro-team@latest/github/getting-started-with-github/create-a-repo).
1. Nastavte úložiště na Private.
    1. Vyberte **Nastavení**  >  **změnit viditelnost úložiště**.
    1. Vyberte **nastavit jako soukromé**.
    1. Zadejte název úložiště.
    1. Vyberte možnost **rozumím, změňte viditelnost úložiště**.
1. Vyberte **Nastavení**.
1. Vyberte **tajné** kódy.
1. Vyberte **nový tajný klíč úložiště**.
    ![Přidejte tajný klíč akcí GitHubu.](.\media\ci-cd-github-action-login-cli\github-action-secret.png)
1. Pojmenujte svůj tajný klíč `AZURE_CREDENTIALS` .
1. Vložte objekt JSON, který představuje váš hlavní název služby (SPN).
1. Vyberte **Add secret** (Přidat tajný kód).

## <a name="create-your-vm-and-install-prerequisites"></a>Vytvoření virtuálního počítače a instalace požadovaných součástí

1. Vytvořte svůj samoobslužný spouštěč. 

    Tyto pokyny vytvoří Runner jako virtuální počítač s Windows v Azure. Pokud se potřebujete připojit ke svému rozbočovači Azure Stack hostovanému v datacentru, můžete vyžadovat připojení k síti VPN. Pokyny k povolení připojení najdete v části [Instalace nástrojů centra Azure Stack na váš spouštěč v místním prostředí](#optional-install-azure-stack-hub-tools-on-your-self-hosted-runner) , který může vyžadovat připojení k síti VPN.
    - Pokyny k vytvoření virtuálního počítače s Windows v Azure najdete v tématu [rychlý Start: Vytvoření virtuálního počítače s Windows v Azure Portal](/azure/virtual-machines/windows/quick-create-portal). Pokud budete postupovat podle těchto pokynů, nainstalujte Windows Server 2016 Core.
    - Pokyny k vytvoření virtuálního počítače s Windows v centru Azure Stack najdete v tématu [rychlý Start: Vytvoření virtuálního počítače s Windows serverem pomocí portálu služby Azure Stack hub](./azure-stack-quick-windows-portal.md). Pokud budete postupovat podle těchto pokynů, nainstalujte Windows Server 2016 Core.
1. Pomocí vzdáleného připojení se připojte k serveru Windows 2016 pomocí IP adresy serveru, uživatelského jména a hesla, které jste definovali při vytváření počítače.
1. Nainstalujte čokolády. Čokoláda je správce balíčků pro Windows, který můžete použít k instalaci a správě závislostí z příkazového řádku. Z příkazového řádku PowerShellu se zvýšenými oprávněními zadejte:
    ```powershell
    Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
    ```
1. Nainstalujte PowerShell Core. Z příkazového řádku PowerShellu se zvýšenými oprávněními zadejte:
    ```powershell  
    choco install powershell-core
    ```
1. Nainstalujte rozhraní příkazového řádku Azure CLI. Z příkazového řádku PowerShellu se zvýšenými oprávněními zadejte:
    ```powershell  
    choco install azure-cli
    ```
1. Nainstalujte PowerShell Azure Stack hub. Z příkazového řádku PowerShellu se zvýšenými oprávněními zadejte:
    ```powershell  
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

    Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
    Install-AzProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 2.0.2-preview -AllowPrerelease
    ```
    Další informace o použití centra Azure Stack AZ moduls naleznete v tématu [install PowerShell AZ Module for Azure Stack hub](../operator/powershell-install-az-module.md).
7. Restartujte počítač. Z příkazového řádku PowerShellu se zvýšenými oprávněními zadejte:
    ```powershell  
    shutdown /r
    ```
8. Přidejte počítač jako spouštěč v místním prostředí do úložiště GitHub. Pokyny k přidání samoobslužného spouštěče do dokumentů GitHubu najdete v dokumentaci k GitHubu. Další informace najdete v tématu [Přidání spouštěčů](https://docs.github.com/en/free-pro-team@latest/actions/hosting-your-own-runners/adding-self-hosted-runners)v místním prostředí.

    ![Runner naslouchá](.\media\ci-cd-github-action-login-cli\github-action-runner-listen.png)

9. Po dokončení ověřte, že je služba spuštěná a naslouchá službě. Dvojitou kontrolu spusťte `/run.cmd` z adresáře spouštěče.

### <a name="optional-install-azure-stack-hub-tools-on-your-self-hosted-runner"></a>Volitelné: Instalace nástrojů centra Azure Stack na váš samoobslužný spouštěč

Pokyny v tomto článku nevyžadují přístup k [nástrojům centra Azure Stack](../operator/azure-stack-powershell-download.md?tabs=az), ale při vývoji vlastního pracovního postupu možná budete muset nástroje použít. Následující pokyny vám pomůžou při instalaci nástrojů na spouštěč Windows v místním prostředí. Další informace o nástrojích centra Azure Stack najdete v tématu [Stažení nástrojů centra Azure Stack z GitHubu](../operator/azure-stack-powershell-download.md?tabs=az). V těchto pokynech se předpokládá, že máte nainstalovanou čokoláda správce balíčků.

1. Nainstalujte Git.
    ```powershell  
    choco install git
    ```

2. Z příkazového řádku PowerShellu se zvýšenými oprávněními zadejte:
    ```powershell
    # Change directory to the root directory.
    cd \
    
    # Download the tools archive.
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
    invoke-webrequest `
      https://github.com/Azure/AzureStack-Tools/archive/az.zip `
      -OutFile az.zip
    
    # Expand the downloaded files.
    expand-archive az.zip `
      -DestinationPath . `
      -Force
    
    # Change to the tools directory.
    cd AzureStack-Tools-az
    ```

3. Pokud potřebujete, aby se váš spouštěč připojil k instanci centra Azure Stack, můžete použít PowerShell. Pokyny najdete v článku [připojení k Azure Stack centru pomocí PowerShellu](../operator/azure-stack-powershell-configure-admin.md?tabs=az1%2Caz2%2Caz3).

## <a name="create-a-self-hosted-runner"></a>Vytvoření samoobslužného spouštěče

Můžete nastavit samoobslužný spouštěč v dokumentaci GitHubu. Samoobslužný Spouštěč se dá spustit na jakémkoli počítači, který se může připojit k GitHubu. Můžete použít samoobslužný spouštěč, pokud máte ve svém pracovním postupu úlohu automatizace, která vyžaduje rozsáhlé závislosti, konkrétní licenční požadavky, jako je například hardwarový klíč USB pro licenci na software nebo jiné požadavky na konkrétní počítač nebo software. Počítač může být fyzický počítač, virtuální počítač nebo kontejner. Spouštěče můžete umístit do svého datového centra nebo do cloudu.

V tomto článku budete používat virtuální počítač s Windows hostovaný v Azure, který se nakonfiguruje s požadavky na prostředí PowerShell specifické pro Azure Stack centra.

Pokyny k nastavení, konfiguraci a připojení spouštěče Webhostu k vašemu úložišti najdete v dokumentaci GitHubu "[o spouštěčích s vlastním hostováním](https://docs.github.com/en/free-pro-team@latest/actions/hosting-your-own-runners/about-self-hosted-runners)".

![Připojený k samoobslužnému hostovanému runneru](.\media\ci-cd-github-action-login-cli\github-actions-self-hosted-runner.png)

Poznamenejte si název a značky vašeho vlastního spouštěče. Pracovní postup v tomto článku bude volat pomocí značky `self-hosted` .

## <a name="add-the-workflow-to-your-repository"></a>Přidat pracovní postup do úložiště

Vytvořením nového pracovního postupu pomocí YAML v této části vytvoříte pracovní postup.

1. Otevřete své úložiště GitHub.
2. Vyberte **Akce**.
3. Vytvořte nový pracovní postup.
    - Pokud se jedná o váš první pracovní postup, vyberte **nastavit pracovní postup sami** v části **Zvolit šablonu pracovního postupu**.
    - Pokud máte existující pracovní postupy, vyberte **nový pracovní postup**  >  **nastavit pracovní postup sami**.

4. Do pole cesta název souboru `workflow.yml` .
5. Zkopírujte a vložte YML pracovního postupu.
    ```yaml  
    on: [push]
    
    env:
      ACTIONS_ALLOW_UNSECURE_COMMANDS: 'true'
     
    jobs: 
      azurestack-test:
        runs-on: self-hosted
        steps:
    
          - name: Login to AzureStack with Az Powershell
            uses: azure/login@releases/v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
              environment: 'AzureStack'
              enable-AzPSSession: true
          
          - name: Run Az PowerShell Script Against AzureStack
            uses: azure/powershell@v1
            with:
              azPSVersion: '3.1.0'
              inlineScript: |
                hostname
                Get-AzContext
                Get-AzResourceGroup
          
          - name: Login to AzureStack with CLI
            uses: azure/login@releases/v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
              environment: 'AzureStack'
              enable-AzPSSession: false
          
          - name: Run Azure CLI Script Against AzureStack
            run: |
              hostname
              az group list --output table
    ```
6. Vyberte **Start commit** (Spustit zápis).
7. Přidejte název potvrzení a volitelné podrobnosti a pak vyberte **Potvrdit nový soubor**.

Po spuštění akce ověřte, zda byla úspěšně spuštěna.

1. Otevřete své úložiště GitHub. Pracovní postup můžete aktivovat vložením do úložiště.
1. Vyberte **Akce**.
1. Vyberte název potvrzení v části **všechny pracovní postupy**.

    ![Zobrazit souhrn potvrzení změn](.\media\ci-cd-github-action-login-cli\github-actions-review-log-summary.png)
1. Vyberte název úlohy, **azurestack-test**.

    ![Podrobnosti o potvrzení změn](.\media\ci-cd-github-action-login-cli\github-action-success-screen.png)
1. Rozbalením těchto částí zkontrolujte vrácené hodnoty příkazů PowerShellu a rozhraní příkazového řádku.

Poznámky k souboru pracovního postupu a akci:

- Pracovní postup obsahuje jednu úlohu s názvem `azurestack-test` .
- Akce push aktivuje pracovní postup.
- Akce používá samoobslužný spouštěč, který byl nastaven v úložišti, a který je volán jmenovkou spouštěče v pracovním postupu s řádkem: `runs on: self-hosted` .
- Pracovní postup obsahuje tři akce.
- První akce volá akci přihlášení Azure, aby se mohla přihlásit pomocí PowerShellu s akcemi GitHubu pro Azure, můžete vytvářet pracovní postupy, které můžete nastavit v úložišti pro vytváření, testování, balení, vydávání a nasazování do Azure. Tato akce používá vaše Azure Stack přihlašovací údaje hlavního názvu služby (SPN) k připojení a otevření relace do prostředí Azure Stack hub. Další informace o tom, jak použít akci v GitHubu, najdete v části [Akce přihlášení k Azure](https://github.com/marketplace/actions/azure-login).
- Druhá akce používá Azure PowerShell. Tato akce používá moduly AZ PowerShell a spolupracuje s cloudy a Azure Stackmi rozbočovači. Po spuštění tohoto pracovního postupu zkontrolujte úlohu a ověřte, že skript shromáždil skupiny prostředků ve vašem prostředí Azure Stack hub. Další informace najdete v tématu [Azure PowerShell akci](https://github.com/marketplace/actions/azure-powershell-action) .
- Třetí akcí se pomocí Azure CLI přihlásí a připojí ke svému centru Azure Stack ke shromáždění skupin prostředků. Další informace najdete v tématu věnovaném [akci Azure CLI](https://github.com/marketplace/actions/azure-cli-action).
- Další informace o práci s akcemi GitHubu a s nástrojem pro samoobslužné hostování najdete v dokumentaci k [akcím GitHubu](https://github.com/features/actions) .

## <a name="next-steps"></a>Další kroky

- Další akce najdete na [webu GitHub Marketplace](https://github.com/marketplace).
- Informace o [běžných nasazeních centra Azure Stack](azure-stack-dev-start-deploy-app.md)  
- Další informace o [použití Azure Resource Manager šablon v centru Azure Stack](azure-stack-arm-templates.md)  
- Kontrola vzoru hybridního cloudu DevOps, [vzoru DevOps](/hybrid/app-solutions/pattern-cicd-pipeline)
