---
title: Použijte akci nasazení Web Apps Azure v centru Azure Stack.
description: Použijte akci nasazení Web Apps Azure k vytvoření pracovního postupu průběžné integrace, průběžného nasazování (CI/CD) do centra Azure Stack.
author: mattbriggs
ms.topic: how-to
ms.date: 2/16/2021
ms.author: mabrigg
ms.reviewer: gara
ms.lastreviewed: 2/16/2021
ms.openlocfilehash: fec9acb7a3e156a8646aef88c53e681eed9e53da
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100655253"
---
# <a name="use-the-azure-web-app-deploy-action-with-azure-stack-hub"></a>Použití akce nasazení webové aplikace Azure v centru Azure Stack

Můžete nastavit akce GitHubu pro nasazení webové aplikace do vaší instance centra Azure Stack. Díky tomu můžete nastavit průběžnou integraci a nasazení pro vaši aplikaci. Tento článek vám pomůže začít pracovat s automatizovaným nasazením pomocí akcí GitHubu a centra Azure Stack. Vytvoříte webovou aplikaci a použijete profil publikování k vytvoření webové aplikace, která bude hostovat vaši aplikaci.

Akce GitHubu jsou pracovní postupy tvořené akcemi, které umožňují automatizaci přímo ve vašem úložišti kódu. Pracovní postupy můžete aktivovat s událostmi v procesu vývoje GitHubu. Můžete nastavit běžné úlohy automatizace DevOps, jako je testování, nasazení a průběžná integrace.

Tento ukázkový pracovní postup obsahuje:
- Pokyny k vytvoření a ověření objektu služby (SPN).
- Pokyny k vytvoření publikačního profilu webové aplikace
- Přidání pracovního postupu specifického pro modul runtime
- Přidání odpovídajícího pracovního postupu pomocí nasazení webové aplikace
## <a name="get-service-principal"></a>Získat instanční objekt

Hlavní název služby (SPN) poskytuje přihlašovací údaje založené na rolích, aby se procesy mimo Azure mohly připojit k prostředkům a pracovat s nimi. Budete potřebovat hlavní název služby (SPN) s přístupem přispěvatel a atributy uvedené v těchto pokynech pro použití s akcemi GitHubu.

Jako uživatel centra Azure Stack nemáte oprávnění k vytvoření hlavního názvu služby (SPN). Tuto zásadu budete muset požádat z operátora cloudu. Tady najdete pokyny, abyste mohli vytvořit hlavní název služby (SPN), pokud jste operátor cloudu. Nebo pokud jste vývojář, můžete ověřit hlavní název služby (SPN) poskytnutý operátorem cloudu.

Operátor cloudu bude muset vytvořit hlavní název služby (SPN) pomocí rozhraní příkazového řádku Azure CLI.

Následující fragmenty kódu se zapisují pro počítač s Windows pomocí příkazového řádku PowerShellu s Azure CLI. Pokud na počítači se systémem Linux a bash používáte rozhraní příkazového řádku (CLI), buď odeberte příponu linky, nebo je nahraďte příponou `\` .

1. Připravte hodnoty následujících parametrů použitých k vytvoření hlavního názvu služby (SPN):

    | Parametr | Příklad | Description |
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

    Pokud nemáte oprávnění operátora cloudu, můžete se také přihlásit pomocí hlavního názvu služby (SPN), který vám poskytl operátor cloudu. Budete potřebovat ID klienta, tajný klíč a ID tenanta. Pomocí těchto hodnot můžete pomocí následujících příkazů rozhraní příkazového řádku Azure vytvořit objekt JSON, který můžete přidat do úložiště GitHub jako tajný kód.

    ```azurecli  
    az login --service-principal -u "<client-id>" -p "<secret>" --tenant "<tenant-ID>" --allow-no-subscriptions
    az account show --sdk-auth
    ```

6. Zkontroluje výsledný objekt JSON. Pomocí objektu JSON vytvoříte v úložišti GitHub svůj tajný klíč, který obsahuje vaši akci. Objekt JSON by měl mít následující atributy:

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

## <a name="create-the-web-app-publish-profile"></a>Vytvoření publikačního profilu webové aplikace

### <a name="open-the-create-web-app-blade"></a>Otevřete okno vytvořit webovou aplikaci.

1. Přihlaste se k portálu centra Azure Stack.
1. Vyberte **vytvořit prostředek**  >  **web a mobilní zařízení**  >  **webovou aplikaci**.
    ![Vytvoření webové aplikace v centru Azure Stack](\media\ci-cd-github-action-webapp\create-web-app.png)
### <a name="to-create-your-web-app"></a>Vytvoření webové aplikace

1. Vyberte své **předplatné**.
1. Vytvořte nebo vyberte **skupinu prostředků**.
1. Zadejte **název** vaší aplikace. Název aplikace se zobrazí v adrese URL vaší aplikace, například `yourappname.appservice.<region>.<FQDN>`
1. Vyberte **zásobník modulu runtime** pro vaši aplikaci. Modul runtime musí odpovídat pracovnímu postupu, který používáte k zacílení vašeho publikačního profilu.
1. Vyberte **operační systém** (OS), který bude hostovat modul runtime a aplikaci.
1. Vyberte nebo zadejte **oblast** pro vaši instanci centra Azure Stack.
1. Vyberte plán založený na vaší instanci centra Azure Stack, oblasti a operačním systému aplikace.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Zkontrolujte svou webovou aplikaci. Vyberte **Vytvořit**.
    ![Kontrola webové aplikace v centru Azure Stack](\media\ci-cd-github-action-webapp\review-azure-stack-hub-web-app.png)
1. Vyberte **Přejít k prostředku**.
    ![Získat profil publikování v centru Azure Stack](\media\ci-cd-github-action-webapp\get-azure-stack-hub-web-app-publish-profile.png)
1. Vyberte **získat profil publikování**. Váš profil publikování stahuje a má název `<yourappname>.PublishSettings` . Soubor obsahuje XML s cílovými hodnotami vaší webové aplikace.
1. Uložte svůj publikační profil, abyste k němu mohli přistupovat při vytváření tajných kódů pro vaše úložiště.

## <a name="add-your-secrets-to-the-repository"></a>Přidání tajných kódů do úložiště

K šifrování citlivých informací, které se mají použít ve svých akcích, můžete použít tajné kódy GitHubu. Vytvoříte tajný klíč, který bude obsahovat váš hlavní název služby (SPN), a další tajný klíč, který bude obsahovat váš publikační profil vaší webové aplikace, aby se akce mohla přihlásit k vaší službě Azure Stack hub a sestavit aplikaci pro cíl webové aplikace.

1. Otevřete nebo vytvořte úložiště GitHub. Pokud potřebujete pokyny k vytvoření úložiště v GitHubu, najdete [pokyny v dokumentaci k GitHubu](https://docs.github.com/en/free-pro-team@latest/github/getting-started-with-github/create-a-repo).
1. Vyberte **Nastavení**.
1. Vyberte **tajné** kódy.
1. Vyberte **nový tajný klíč úložiště**.
    ![Přidejte tajný klíč akcí GitHubu.](.\media\ci-cd-github-action-login-cli\github-action-secret.png)
1. Pojmenujte svůj tajný klíč `AZURE_CREDENTIALS` .
1. Vložte objekt JSON, který představuje váš hlavní název služby (SPN).
1. Vyberte **Add secret** (Přidat tajný kód).
1. Vyberte **nový tajný klíč úložiště**.
1. Pojmenujte svůj tajný klíč `AZURE_WEBAPP_PUBLISH_PROFILE` .
1. Otevřete `<yourappname>.PublishSettings` v textovém editoru a zkopírujte a vložte XML do tajného kódu úložiště.
1. Vyberte **Add secret** (Přidat tajný kód).

## <a name="add-a-runtime-workflow"></a>Přidat pracovní postup modulu runtime

1. Vyberte šablonu z tabulky pro modul runtime webové aplikace.

    | Runtime (Modul runtime) | Template (Šablona) |
    | --- | --- |
    | DotNet | [dotnet. yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/asp.net-core-webapp-on-azure.yml) |
    | NodeJS | [Node. yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/node.js-webapp-on-azure.yml) |
    | Java | [java_jar. yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/java-jar-webapp-on-azure.yml) |
    | Java | [java_war. yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/java-war-webapp-on-azure.yml) |
    | Python | [Python. yml](https://github.com/Azure/actions-workflow-samples/tree/master/AppService/python-webapp-on-azure.yml) |
    | PHP | [php. yml](https://github.com/Azure/actions-workflow-samples/blob/master/AppService/php-webapp-on-azure.yml) |
    | Docker | [Docker. yml](https://github.com/Azure/actions-workflow-samples/blob/master/AppService/docker-webapp-container-on-azure.yml) |

2. Umístěte adresář pracovního postupu akce GitHubu šablony do úložiště projektu: `.github/workflows/<runtime.yml>` váš adresář pracovního postupu bude obsahovat dva pracovní postupy.

## <a name="add-the-web-app-deploy-action"></a>Přidat akci nasazení webové aplikace

Pomocí YAML v této části vytvoříte druhý pracovní postup. V tomto příkladu nasazujete webovou aplikaci v Pythonu. V závislosti na pracovním postupu byste si museli vybrat akci nastavení. Můžete najít odkazy na nastavení akcí pro různé moduly runtime v tabulce, [Akce nastavení pro různé moduly runtime](#setup-actions-for-different-runtimes), a to po krocích použitých k vytvoření akce.

### <a name="example-github-action-workflow"></a>Příklad pracovního postupu akce GitHubu

1. Otevřete své úložiště GitHub. Pokud jste ještě nepřidali prostředky aplikace webové aplikace, přidejte je nyní. V tomto příkladu používáme ukázku [Hello World baňky Pythonu](https://github.com/Azure-Samples/python-docs-hello-world) a přidali jsme `.gitignore` soubory Python, `app.py` a `requirements.txt` .

    ![Příkladu úložiště pomocí Pythonové baňky s Azure Stack hub](\media\ci-cd-github-action-webapp\example-of-repo.png)
1. Vyberte **Akce**.
1. Vyberte **nový pracovní postup**.
    - Pokud se jedná o váš první pracovní postup, vyberte **nastavit pracovní postup sami** v části **Zvolit šablonu pracovního postupu**.
    - Pokud máte existující pracovní postupy, vyberte **nový pracovní postup**  >  **nastavit pracovní postup sami**.

4. Do pole cesta název souboru `workflow.yml` .
5. Zkopírujte a vložte YML pracovního postupu.
    ```yaml  
    # .github/workflows/worfklow.yml
    on: push

    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout Github Action' 
          uses: actions/checkout@master
    
        - name: Setup Python 3.6
          uses: actions/setup-node@v1
          with:
            python-version: '3.6'
        - name: 'create a virtual environment and install dependencies'
          run: |
            python3 -m venv .venv
            source .venv/bin/activate
            pip install -r requirements.txt
    
        - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v2
          with:
            app-name: <YOURAPPNAME>
            publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
    ```

6. V části Workflow. yaml aktualizujte `<YOURAPPNAME>` název vaší aplikace.
7. Vyberte **Start commit** (Spustit zápis).
8. Přidejte název potvrzení a volitelné podrobnosti a pak vyberte **Potvrdit nový soubor**.


### <a name="setup-actions-for-different-runtimes"></a>Akce nastavení pro různé moduly runtime

Pokud chcete sestavit kód aplikace v konkrétním jazykovém prostředí, použijte instalační akce:

|  Runtime (Modul runtime) | Nastavení akcí |
|------------|---------|
| DotNet     | [Nastavení DotNet](https://github.com/actions/setup-dotnet) |
| NodeJS     | [Uzel instalace](https://github.com/actions/setup-node) |
| Java     | [Nastavení Java  ](https://github.com/actions/setup-java) |
| Python     | [Nastavení Pythonu ](https://github.com/actions/setup-python) |
| Docker | [Docker – přihlášení ](https://github.com/Azure/docker-login) |

Po dokončení akce přihlášení může další sada akcí v pracovním postupu provádět úlohy, jako je vytváření, tagování a vkládání kontejnerů. Další informace najdete v dokumentaci k [akci Azure WebApp](https://github.com/marketplace/actions/azure-webapp).
## <a name="trigger-your-deployment"></a>Aktivace nasazení

Po spuštění akce ověřte, zda byla úspěšně spuštěna.

1. Otevřete své úložiště GitHub. Pracovní postup můžete aktivovat vložením do úložiště.
1. Vyberte **Akce**.
1. Vyberte název potvrzení v části **všechny pracovní postupy**. Oba pracovní postupy zaznamenaly svůj stav.
    ![Kontrola stavu akce GitHubu](\media\ci-cd-github-action-webapp\workflow-success.png)
1. Vyberte název úlohy pro nasazení `.github/workflows/workflow.yml` .
1. Rozbalením částí můžete zkontrolovat vrácené hodnoty akcí pracovního postupu. Vyhledejte adresu URL pro nasazenou webovou aplikaci.
    ![Najít adresu URL vaší webové aplikace centra Azure Stack](\media\ci-cd-github-action-webapp\review-cli-log-and-get-url.png)
1. Otevřete webový prohlížeč a načtěte adresu URL.
## <a name="next-steps"></a>Další kroky

- Další akce najdete na [webu GitHub Marketplace](https://github.com/marketplace).
- Informace o [běžných nasazeních centra Azure Stack](azure-stack-dev-start-deploy-app.md)  
- Další informace o [použití Azure Resource Manager šablon v centru Azure Stack](azure-stack-arm-templates.md)  
- Kontrola vzoru hybridního cloudu DevOps, [vzoru DevOps](https://docs.microsoft.com/hybrid/app-solutions/pattern-cicd-pipeline)
