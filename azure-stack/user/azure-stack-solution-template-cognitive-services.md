---
title: Nasazení služby Azure Cognitive Services do centra Azure Stack
description: Přečtěte si, jak nasadit Azure Cognitive Services do centra Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 10/09/2020
ms.author: mabrigg
ms.reviewer: guanghu
ms.lastreviewed: 10/09/2020
ms.openlocfilehash: 4dc6ad30d54b61e05f54be4a4f1301472794db60
ms.sourcegitcommit: e13f27291bab236aac5d8b05401056961e9cc1e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697687"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack-hub"></a>Nasazení služby Azure Cognitive Services do centra Azure Stack

Můžete použít Azure Cognitive Services s podporou kontejneru v centru Azure Stack. Podpora kontejnerů v Azure Cognitive Services umožňuje používat stejná bohatá rozhraní API, která jsou k dispozici v Azure. Vaše používání kontejnerů umožňuje flexibilitu při nasazení a hostování služeb dodaných v [kontejnerech Docker](https://www.docker.com/what-container). 

Kontejnering je přístup k distribuci softwaru, ve kterém se aplikace nebo služba, včetně jejích závislostí a konfigurace, balí jako image kontejneru. S minimální nebo žádnou úpravou můžete nasadit image do hostitele kontejneru. Každý kontejner je izolovaný od jiných kontejnerů a od základního operačního systému. Samotný systém má pouze součásti potřebné ke spuštění vaší image. Hostitel kontejneru má menší nároky, než je virtuální počítač. Můžete také vytvořit kontejnery z imagí pro krátkodobé úlohy a odstranit je, pokud už nepotřebujete.

Podpora kontejnerů je aktuálně k dispozici pro podmnožinu Azure Cognitive Services:

- **Speech**
    - Převod řeči na text (Standard)
    - Převod řeči na text (vlastní)
    - Převod textu na řeč (standardně)
    - Převod textu na řeč (neuronové)
- **Jazyk**
    - Rozpoznávání jazyka
    - Analýza textu (analýza mínění)
- **Rozhodnutí**
    - Detektor anomálií


> [!IMPORTANT]
> Podmnožina Azure Cognitive Services pro centrum Azure Stack je aktuálně ve verzi Public Preview.
> Revize verze se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Podpora kontejnerů je aktuálně ve verzi Public Preview pro podmnožinu Azure Cognitive Services:

- **Počítačové zpracování obrazu**
    - Přečíst 3,0, přečíst 3,1
    - Prostorová analýza (novinka)
- **Jazyk**
    - Extrakce klíčových frází
    - Rozpoznávání jazyka
    - Analýza textu pro stav
- **Speech**
    - Rozpoznávání mluveného jazyka
    - Neuronové TTS
    - Převod textu na řeč (vlastní)

## <a name="use-containers-with-cognitive-services-on-azure-stack-hub"></a>Použití kontejnerů s Cognitive Services v centru Azure Stack

- **Kontrola nad daty**  
  Umožněte uživatelům vaší aplikace ovládat svá data při použití Cognitive Services. Můžete doručovat Cognitive Services uživatelům aplikace, kteří nemůžou odesílat data do globálního Azure nebo veřejného cloudu.

- **Kontrola aktualizací modelu**  
  Poskytněte uživatelům aplikace aktualizace verze v modelech nasazených v jejich řešení.

- **Přenosná architektura**  
  Povolte vytvoření přenosové architektury aplikace, abyste mohli své řešení nasadit do veřejného cloudu, do místního privátního cloudu nebo na hranici. Svůj kontejner můžete nasadit do služby Azure Kubernetes, Azure Container Instances nebo do clusteru Kubernetes v centru Azure Stack. Další informace najdete v tématu [nasazení Kubernetes do centra Azure Stack](azure-stack-solution-template-kubernetes-deploy.md).

- **Vysoká propustnost a nízká latence**  
   Poskytněte uživatelům aplikace možnost škálování s využitím špičky v provozu při vysoké propustnosti a nízké latenci. Umožněte, aby Cognitive Services běžely ve službě Azure Kubernetes, fyzicky blízko své aplikační logiky a data.

Pomocí centra Azure Stack nasaďte Cognitive Services kontejnery v clusteru Kubernetes spolu s kontejnery aplikací pro zajištění vysoké dostupnosti a elastického škálování. Můžete vyvíjet aplikace kombinováním služeb rozpoznávání se součástmi postavenými na App Services, funkcích, BLOB Storage, SQL nebo MySQL databáze.

Další podrobnosti o Cognitive Services kontejnerech najdete [v podpoře kontejnerů ve službě Azure Cognitive Services](/azure/cognitive-services/cognitive-services-container-support).

## <a name="deploy-the-azure-face-api"></a>Nasazení Azure Face API

Tento článek popisuje, jak nasadit Azure Face API v clusteru Kubernetes v centru Azure Stack. Stejný přístup můžete použít k nasazení jiných kontejnerů služby pro rozpoznávání na Azure Stack clustery Kubernetes hub.

## <a name="prerequisites"></a>Požadavky

Než začnete, budete potřebovat:

1.  Vyžádejte si přístup k registru kontejneru, který načte image kontejneru z Azure Cognitive Services Container Registry. Podrobnosti najdete v tématu [vyžádání přístupu k privátnímu registru kontejneru](/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry).

2.  Připravte cluster Kubernetes na rozbočovači Azure Stack. Můžete postupovat podle článku [nasazení Kubernetes do centra Azure Stack](azure-stack-solution-template-kubernetes-deploy.md).

## <a name="create-azure-resources"></a>Vytvoření prostředků Azure

Vytvořte prostředek služby vnímání v Azure a zobrazte si náhled kontejnerů pro obličej, Language Understanding (LUIS) nebo Rozpoznávání textu. K vytvoření instance kontejnerů služby pro rozpoznávání bude nutné použít klíč předplatného a adresu URL koncového bodu z prostředku.

1. Vytvořte prostředek Azure v Azure Portal. Pokud chcete zobrazit náhled kontejnerů obličeje, musíte nejprve vytvořit odpovídající prostředek Face v Azure Portal. Další informace najdete v tématu [rychlý Start: vytvoření účtu Cognitive Services v Azure Portal](/azure/cognitive-services/cognitive-services-apis-create-account).

   > [!NOTE]
   >  Prostředek Face nebo Počítačové zpracování obrazu musí používat cenovou úroveň F0.

2. Získejte adresu URL koncového bodu a klíč předplatného pro prostředek Azure. Po vytvoření prostředku Azure použijte klíč předplatného a adresu URL koncového bodu z tohoto prostředku k vytvoření instance odpovídajícího kontejneru Face, LUIS nebo Rozpoznávání textu pro verzi Preview.

## <a name="create-a-kubernetes-secret"></a>Vytvoření tajného klíče Kubernetes 

K přístupu k soukromému registru kontejneru použijte příkaz Kubectl Create Secret. Nahraďte `<username>` uživatelským jménem a `<password>` heslem, které jste zadali v přihlašovacích údajích, které jste dostali od týmu Azure Cognitive Services.

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>Příprava souboru konfigurace YAML

Pomocí souboru konfigurace YAML zjednodušíte nasazení služby rozpoznávání v clusteru Kubernetes.

Tady je ukázkový soubor YAML konfigurace pro nasazení služby Faceer do centra Azure Stack:

```Yaml  
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: <deploymentName>
spec:
  replicas: <replicaNumber>
  template:
    metadata:
      labels:
        app: <appName>
    spec:
      containers:
      - name: <containersName>
        image: <ImageLocation>
        env: 
        - name: EULA
          value: accept 
        - name: Billing
          value: <billingURL> 
        - name: apikey
          value: <apiKey>
        tty: true
        stdin: true
        ports:
        - containerPort: 5000 
      imagePullSecrets:
        - name: <secretName>
---
apiVersion: v1
kind: Service
metadata:
  name: <LBName>
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort : 5000
    name: <PortsName>
  selector:
    app: <appName>
```

V této YAML konfiguraci souboru použijte tajný klíč, který jste použili k získání imagí kontejneru služby vnímání z Azure Container Registry. Tajný soubor se používá k nasazení konkrétní repliky kontejneru. Můžete také vytvořit nástroj pro vyrovnávání zatížení, abyste měli jistotu, že uživatelé budou mít přístup k této službě externě.

Podrobnosti o klíčových polích:

| Pole | Poznámky |
| --- | --- |
| replicaNumber | Definuje počáteční repliky instancí, které se mají vytvořit. Můžete ho škálovat později po nasazení. |
| ImageLocation | Označuje umístění konkrétního obrazu kontejneru služby rozpoznávání v ACR. Například služba obličeje: `aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |Adresa URL koncového bodu, kterou jste si poznamenali v kroku [vytvoření prostředku Azure](#create-azure-resources) |
| ApiKey | Klíč předplatného, který jste si poznamenali v kroku [vytvoření prostředku Azure](#create-azure-resources) |
| SecretName | Název tajného klíče, který jste vytvořili v kroku, [vytvoří tajný klíč Kubernetes](#create-a-kubernetes-secret) . |

## <a name="deploy-the-cognitive-service"></a>Nasazení služby pro rozpoznávání

K nasazení kontejnerů služby pro rozpoznávání použijte následující příkaz:

```bash  
    Kubectl apply -f <yamlFileName>
```
Pomocí následujícího příkazu můžete monitorovat, jak se nasazuje: 
```bash  
    Kubectl get pod - watch
```

## <a name="configure-http-proxy-settings"></a>Konfigurace nastavení proxy serveru HTTP

Pracovní uzly potřebují proxy a SSL. Pokud chcete nakonfigurovat proxy server HTTP pro vytváření odchozích požadavků, použijte tyto dva argumenty:

- **Http_proxy** – proxy server, který se má použít, například `https://proxy:8888`
- **HTTP_PROXY_CREDS** – například jakékoli přihlašovací údaje potřebné k ověření vůči proxy serveru `username:password` .

### <a name="set-up-the-proxy"></a>Nastavení proxy serveru

1. Přidat `http-proxy.conf` soubor do obou umístění:
    - `/etc/system/systemd/docker.service.d/`
    - `/cat/etc/environment/`

2. Ověřte, že se k kontejneru můžete přihlásit pomocí přihlašovacích údajů poskytnutých Cognitive Services týmem a udělat `docker pull` v následujícím kontejneru: 

    `docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest`

    Spusťte tento příkaz:

    `docker run hello-world pull`

### <a name="ssl-interception-setup"></a>Nastavení zachycení SSL

1. Přidejte do úložiště certifikát pro **zachycení https** `/usr/local/share/ca-certificates` a aktualizujte ho `update-ca-certificates` . 

## <a name="test-the-cognitive-service"></a>Testování služby pro rozpoznávání

Přístup k [specifikaci openapi](https://swagger.io/docs/specification/about/) z RELATIVNÍho identifikátoru URI **/Swagger** pro daný kontejner. Tato specifikace, dříve označovaná jako specifikace Swagger, popisuje operace podporované vytvořeným kontejnerem. Například následující identifikátor URI poskytuje přístup k specifikaci OpenAPI pro kontejner Analýza mínění, který byl vytvořen v předchozím příkladu:

```HTTP  
http:<External IP>:5000/swagger
```

Externí IP adresu můžete získat z následujícího příkazu: 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>Vyzkoušejte si služby pomocí Pythonu

Můžete se pokusit ověřit službu rozpoznávání na svém Azure Stackovém centru spuštěním některých jednoduchých skriptů Pythonu. K dispozici jsou oficiální úvodní ukázky Pythonu pro [počítačové zpracování obrazu](/azure/cognitive-services/computer-vision/home), [obličej](/azure/cognitive-services/face/overview), [Analýza textu](/azure/cognitive-services/text-analytics/overview)a [Language Understanding](/azure/cognitive-services/luis/luis-container-howto) (Luis) pro váš odkaz.

Existují dvě věci, které je potřeba vzít v úvahu při ověřování služeb spuštěných v kontejnerech pomocí aplikací v Pythonu: 
1. Vyměnitelné služby v kontejnerech nepotřebují pro ověřování podklíče, ale pro splnění sady SDK stále vyžadují libovolný řetězec jako zástupný text. 
2. Nahraďte base_URL skutečnou IP adresou koncového bodu služby.

Tady je ukázkový skript Pythonu, který používá sadu Python SDK pro rozpoznávání a orámování plošek v imagi:

```Python  
import cognitive_face as CF

# Cognitive Services in container do not need sub keys for authentication
# Keep the invalid key to satisfy the SDK inputs requirement. 
KEY = '0'  #  (keeping the quotes in place).
CF.Key.set(KEY)

# Get your actual Ip Address of service endpoint of your cognitive service on Azure Stack Hub
BASE_URL = 'http://<svc IP Address>:5000/face/v1.0/'  
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)

```

## <a name="next-steps"></a>Další kroky

[Jak nainstalovat a spustit rozhraní API pro počítačové zpracování obrazu kontejnery.](/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)

[Jak nainstalovat a spustit Face API kontejnery](/azure/cognitive-services/face/face-how-to-install-containers)

[Jak nainstalovat a spustit rozhraní API pro analýzu textu kontejnery](/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)

[Instalace a spuštění kontejnerů Language Understanding (LIUS)](/azure/cognitive-services/luis/luis-container-howto)
