---
title: Nasazení řešení pro detekci Footfall založené na AI pomocí Azure a centra Azure Stack
description: Naučte se, jak nasadit řešení pro detekci Footfall pomocí Azure a centra Azure Stack. Toto řešení se používá k analýze provozu návštěvníků v prodejních.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: d81fdf315e9bc834c46161d901d6ac10d736f7ab
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76875296"
---
# <a name="deploy-an-ai-based-footfall-detection-solution-using-azure-and-azure-stack-hub"></a>Nasazení řešení pro detekci Footfall založené na AI pomocí Azure a centra Azure Stack

Tento článek popisuje, jak nasadit řešení, které generuje přehledy z reálných akcí pomocí Azure, centra Azure Stack a Custom Vision AI dev Kit.

V tomto řešení se dozvíte, jak:

> [!div class="checklist"]
> - Nasaďte na hranici cloudové nativní sady aplikací (CNAB). 
> - Nasaďte aplikaci, která zahrnuje hranice cloudu.
> - Pro odvození na hranici použijte Custom Vision AI dev Kit.

> [!Tip]  
> ![hybridní pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Centrum Microsoft Azure Stack je rozšířením Azure. Centrum Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí. tím se umožní jenom hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace odkudkoli.  
> 
> Požadavky na [Návrh pro hybridní aplikace](overview-app-design-considerations.md) kontrolují pilíře kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) pro navrhování, nasazování a provozování hybridních aplikací. Pokyny k návrhu pomáhají při optimalizaci návrhu hybridní aplikace a minimalizaci výzev v produkčních prostředích.

## <a name="prerequisites"></a>Požadavky 

Než začnete s tímto průvodcem nasazením, nezapomeňte:

- Přečtěte si [Přehled řešení Footfall Detection](pattern-retail-footfall-detection.md) . 
- Získejte přístup uživatelů ke službě Azure Stack ASDK (hub Development Kit) nebo instanci integrovaného systému služby Azure Stack hub pomocí:
  - Azure App Service nainstalovaného [poskytovatele prostředků centra Azure Stack](../operator/azure-stack-app-service-overview.md) . K vaší instanci centra Azure Stack potřebujete přistupovat pomocí operátoru, nebo pokud chcete nainstalovat správce, spolupracujte se správcem.
  - Předplatné nabídky, které poskytuje App Service a kvótu úložiště. Chcete-li vytvořit nabídku, potřebujete přístup k operátoru.
- Získání přístupu k předplatnému Azure
  - Pokud ještě nemáte předplatné Azure, zaregistrujte si [bezplatný zkušební účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Vytvořte ve svém adresáři dva instanční objekty:
  - Jedna konfigurovaná pro použití s prostředky Azure s přístupem v oboru předplatného Azure. 
  - Jedna konfigurovaná pro použití s prostředky centra Azure Stack s přístupem v oboru předplatného centra Azure Stack. 
  - Další informace o vytváření instančních objektů a autorizaci přístupu najdete v tématu [použití identity aplikace pro přístup k prostředkům](../operator/azure-stack-create-service-principals.md). Pokud dáváte přednost použití rozhraní příkazového řádku Azure, přečtěte si téma [Vytvoření instančního objektu Azure pomocí Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).
- Nasazení Azure Cognitive Services v Azure nebo centra Azure Stack.
  - Nejprve [si přečtěte další informace o Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).
  - Potom navštivte [nasazení služby Azure Cognitive Services, abyste Azure Stack centrum](../user/azure-stack-solution-template-cognitive-services.md) nasadili Cognitive Services v Azure Stackovém centru. Nejprve se musíte zaregistrovat pro přístup k verzi Preview.
- Naklonujte nebo Stáhněte si nenakonfigurovanou sadu Azure Custom Vision AI dev Kit. Podrobnosti najdete v tématu [Vision AI DevKit](https://azure.github.io/Vision-AI-DevKit-Pages/).
- Zaregistrujte si účet Power BI.
- Klíč předplatného služby Azure Cognitive Services Face API a adresa URL koncového bodu. Můžete získat [Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api) bezplatnou zkušební verzi. Případně postupujte podle pokynů v části [Vytvoření účtu Cognitive Services](/azure/cognitive-services/cognitive-services-apis-create-account).
- Nainstalujte následující prostředky pro vývoj:
  - [Azure CLI 2.0](../user/azure-stack-version-profiles-azurecli2.md)
  - [Docker CE](https://hub.docker.com/search/?type=edition&offering=community).
  - [Porter](https://porter.sh/). Pomocí Porter můžete nasazovat cloudové aplikace s využitím manifestů CNAB sad, které jsou pro vás k dispozici.
  - [Visual Studio Code](https://code.visualstudio.com/)
  - [Nástroje Azure IoT pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
  - [Rozšíření Pythonu pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - [Python](https://www.python.org/)

## <a name="deploy-the-hybrid-cloud-application"></a>Nasazení hybridní cloudové aplikace

Nejprve pomocí rozhraní příkazového řádku Porter vygenerujte sadu přihlašovacích údajů a potom nasaďte cloudovou aplikaci.  

1. Naklonujte nebo Stáhněte vzorový kód řešení z https://github.com/azure-samples/azure-intelligent-edge-patterns. 

1. Porter vygeneruje sadu přihlašovacích údajů, které budou automatizovat nasazení aplikace. Před spuštěním příkazu pro generování přihlašovacích údajů se ujistěte, že máte k dispozici následující:

    - Instanční objekt pro přístup k prostředkům Azure, včetně identifikátoru ID objektu služby, klíče a DNS tenanta.
    - ID předplatného pro vaše předplatné Azure.
    - Instanční objekt pro přístup k prostředkům služby Azure Stack hub, včetně ID instančního objektu, klíče a DNS tenanta.
    - ID předplatného pro vaše předplatné centra Azure Stack.
    - Adresa URL vašeho klíče Face API a klíčového bodu prostředku služby Azure Cognitive Services.

1. Spusťte proces generování přihlašovacích údajů Porter a postupujte podle následujících pokynů:

   ```porter
   porter creds generate --tag intelligentedge/footfall-cloud-deployment:0.1.0
   ```

1. Porter také vyžaduje, aby bylo možné spustit sadu parametrů. Vytvořte textový soubor parametrů a zadejte následující páry název/hodnota. Pokud potřebujete pomoc s kteroukoli z požadovaných hodnot, požádejte správce centra Azure Stack.

   > [!NOTE] 
   > Hodnota `resource suffix` se používá k zajištění, že prostředky nasazení mají v rámci Azure jedinečné názvy. Musí se jednat o jedinečný řetězec písmen a číslic, který nesmí být delší než 8 znaků.

    ```
    azure_stack_tenant_arm="Your Azure Stack Hub tenant endpoint"
    azure_stack_storage_suffix="Your Azure Stack Hub storage suffix"
    azure_stack_keyvault_suffix="Your Azure Stack Hub keyVault suffix"
    resource_suffix="A unique string to identify your deployment"
    azure_location="A valid Azure region"
    azure_stack_location="Your Azure Stack Hub location identifier"
    powerbi_display_name="Your first and last name"
    powerbi_principal_name="Your Power BI account email address"
    ```
   Uložte textový soubor a poznamenejte si jeho cestu.

1. Nyní jste připraveni nasadit hybridní cloudovou aplikaci pomocí Porter. Spusťte příkaz Install a sledujte, jak se prostředky nasazují do Azure a centra Azure Stack:

    ```porter
    porter install footfall-cloud –tag intelligentedge/footfall-cloud-deployment:0.1.0 –creds footfall-cloud-deployment –param-file "path-to-cloud-parameters-file.txt"
    ```

1. Po dokončení nasazení si poznamenejte následující hodnoty:
    - Připojovací řetězec kamery.
    - Připojovací řetězec účtu úložiště imagí
    - Názvy skupin prostředků.

## <a name="prepare-the-custom-vision-ai-dev-kit"></a>Příprava Custom Vision AI dev Kit

Dále nastavte Custom Vision AI dev Kit, jak je znázorněno v kurzu [rychlý Start DevKit Vision AI](https://azure.github.io/Vision-AI-DevKit-Pages/docs/quick_start/). Pomocí připojovacího řetězce, který jste zadali v předchozím kroku, můžete také nastavit a otestovat fotoaparát.

## <a name="deploy-the-camera-application"></a>Nasazení aplikace kamery

Pomocí rozhraní příkazového řádku Porter vygenerujte sadu přihlašovacích údajů a potom Nasaďte aplikaci kamery.

1. Porter vygeneruje sadu přihlašovacích údajů, které budou automatizovat nasazení aplikace. Před spuštěním příkazu pro generování přihlašovacích údajů se ujistěte, že máte k dispozici následující:
    
    - Instanční objekt pro přístup k prostředkům Azure, včetně identifikátoru ID objektu služby, klíče a DNS tenanta.
    - ID předplatného pro vaše předplatné Azure.
    - Připojovací řetězec účtu úložiště imagí, který jste zadali při nasazení cloudové aplikace.

1. Spusťte proces generování přihlašovacích údajů Porter a postupujte podle následujících pokynů:

    ```porter
    porter creds generate --tag intelligentedge/footfall-camera-deployment:0.1.0
    ```

1. Porter také vyžaduje, aby bylo možné spustit sadu parametrů. Vytvořte textový soubor parametrů a zadejte následující text. Pokud neznáte některé z požadovaných hodnot, požádejte správce centra Azure Stack.

    > [!NOTE]
    > Hodnota `deployment suffix` se používá k zajištění, že prostředky nasazení mají v rámci Azure jedinečné názvy. Musí se jednat o jedinečný řetězec písmen a číslic, který nesmí být delší než 8 znaků.

    ```
    iot_hub_name="Name of the IoT Hub deployed"
    deployment_suffix="Unique string here"
    ```

    Uložte textový soubor a poznamenejte si jeho cestu.

4. Nyní jste připraveni nasadit aplikaci kamery pomocí Porter. Spusťte příkaz Install a sledujte, jak je vytvořeno nasazení IoT Edge.

    ```porter
    porter install footfall-camera –tag intelligentedge/footfall-camera-deployment:0.1.0 –creds footfall-camera-deployment –param-file "path-to-camera-parameters-file.txt"
    ```

5. Ověřte, že se nasazení kamery dokončilo, zobrazením informačního kanálu kamery na `https://<camera-ip>:3000/`, kde `<camara-ip>` je IP adresa kamery. Tento krok může trvat až 10 minut.

## <a name="configure-azure-stream-analytics"></a>Konfigurace Azure Stream Analytics

Teď, když data přecházejí z kamery Azure Stream Analytics, musíme je ručně autorizovat, aby komunikovala s Power BI.

1.  Z Azure Portal otevřít **všechny prostředky**a úlohu *proces-footfall\[yoursuffix\]* .

2.  V podokně úlohy Stream Analytics v části **Topologie úlohy** vyberte možnost **Výstupy**.

3.  Vyberte výstupní výstupní jímku **výstupních dat** .

4.  Vyberte **obnovit autorizaci** a přihlaste se ke svému účtu Power BI.
    
    ![výzva k obnovení autorizace](./media/solution-deployment-guide-retail-footfall-detection/image2.png)

5.  Uložte nastavení výstupu.

6.  V podokně **Přehled** vyberte **začít** a začněte odesílat data do Power BI.

7.  Vyberte **Nyní** pro čas spuštění výstupu úlohy a vyberte **Spustit**. Stav úlohy můžete sledovat v oznamovacím pruhu.

## <a name="create-a-power-bi-dashboard"></a>Vytvoření řídicího panelu Power BI

1.  Po úspěšném provedení úlohy přejděte do [Power BI](https://powerbi.com/) a přihlaste se pomocí svého pracovního nebo školního účtu. Pokud je v dotazu úlohy Stream Analytics výstupem výsledků, datová sada *Footfall-DataSet* , kterou jste vytvořili, existuje na kartě **datové sady** .

2.  V pracovním prostoru Power BI vyberte **+ vytvořit** a vytvořte nový řídicí panel s názvem *Analýza Footfall.*

3.  V horní části okna vyberte **Přidat dlaždici**. Potom vyberte **Vlastní streamovaná data** a **Další**. V **datových sadách**vyberte **Footfall-DataSet** . V rozevíracím seznamu **typ vizualizace** vyberte **karta** a do **polí**přidejte **věk** . Vyberte **Další**, zadejte název dlaždice a pak výběrem možnosti **Použít** dlaždici vytvořte.

4.  Podle potřeby můžete přidat další pole a karty.

## <a name="test-your-solution"></a>Testování řešení

Sledujte, jak se data na kartách, které jste vytvořili v Power BI mění, protože se před fotoaparátem prochází různí lidé. Odvození může trvat až 20 sekund, než se objeví po nahrání.

## <a name="remove-your-solution"></a>Odebrání řešení

Pokud chcete odebrat řešení, spusťte následující příkazy pomocí Porter pomocí stejných souborů parametrů, které jste vytvořili pro nasazení: 

```porter
porter uninstall footfall-cloud –tag intelligentedge/footfall-cloud-deployment:0.1.0 –creds footfall-cloud-deployment –param-file "path-to-cloud-parameters-file.txt"

porter uninstall footfall-camera –tag intelligentedge/footfall-camera-deployment:0.1.0 –creds footfall-camera-deployment –param-file "path-to-camera-parameters-file.txt"
```
## <a name="next-steps"></a>Další kroky

- Další informace o [požadavcích na návrh hybridních cloudových aplikací](overview-app-design-considerations.md)
- Přečtěte si a navrhněte vylepšení [kódu pro tuto ukázku na GitHubu](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/tree/master/footfall-analysis).
