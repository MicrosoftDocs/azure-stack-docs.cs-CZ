---
title: Vytvoření virtuálního počítače pomocí šablony komunity
description: Naučte se používat Azure Stack Development Kit (ASDK) k vytvoření virtuálního počítače pomocí předdefinované šablony a vlastní šablony GitHubu.
author: sethmanheim
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/13/2019
ms.openlocfilehash: 25619c88f0550b839e711cd059cf1aa25412b972
ms.sourcegitcommit: 69c700a456091adc31e4a8d78e7a681dfb55d248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100013302"
---
# <a name="tutorial-create-a-vm-using-a-community-template"></a>Kurz: Vytvoření virtuálního počítače pomocí šablony komunity

Jako operátor nebo uživatel Azure Stackového centra můžete vytvořit virtuální počítač pomocí [vlastních šablon rychlý Start pro GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates) místo ručního nasazení šablony z webu centra Azure Stack.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Použití šablon rychlý Start centra Azure Stack
> * Vytvoření virtuálního počítače pomocí vlastní šablony GitHubu
> * Spuštění Minikube a instalace aplikace

## <a name="azure-stack-hub-quickstart-templates"></a>Šablony rychlý Start centra Azure Stack

Šablony pro rychlý Start centra Azure Stack jsou uložené v [úložišti šablon pro rychlé zprovoznění centra globálního Azure Stack centra](https://github.com/Azure/AzureStack-QuickStart-Templates)pro GitHub. Toto úložiště obsahuje Azure Resource Manager šablony nasazení, které byly testovány pomocí sady Microsoft Azure Stack Development Kit (ASDK). Můžete je použít ke snazšímu vyhodnocení Azure Stack centra a k použití prostředí ASDK.

V průběhu času se k úložišti přispělo spousta uživatelů GitHubu, což vedlo ke kolekci více než 400 šablon nasazení. Toto úložiště je dobrým výchozím bodem pro porozumění, jak můžete nasadit různé druhy prostředí do centra Azure Stack.

> [!IMPORTANT]
> Některé z těchto šablon vytváří členové komunity a ne společnost Microsoft. Každá šablona je licencovaná v rámci licenční smlouvy od jejího vlastníka, nikoli od společnosti Microsoft. Společnost Microsoft není zodpovědná za tyto šablony a není na obrazovce pro zabezpečení, kompatibilitu nebo výkon. Šablony komunity nejsou podporované v rámci žádného programu nebo služby podpory společnosti Microsoft a jsou dostupné "tak, jak jsou", bez záruky jakéhokoli druhu.

Pokud chcete přispívat Azure Resource Manager šablon do GitHubu, udělejte svůj příspěvek do úložiště [šablon AzureStack-Starter](https://github.com/Azure/AzureStack-QuickStart-Templates) . Další informace o tomto úložišti a o tom, jak na něj přispívat, najdete v [souboru Readme](https://aka.ms/aa6zktg).

## <a name="create-a-vm-using-a-custom-github-template"></a>Vytvoření virtuálního počítače pomocí vlastní šablony GitHubu

V tomto ukázkovém kurzu se šablona pro rychlý Start centra [101-VM-Linux-minikube](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-linux-minikube) Azure Stack slouží k nasazení virtuálního počítače Ubuntu 16,04 do centra Azure Stack se systémem minikube pro správu clusteru Kubernetes.

Minikube je nástroj, který usnadňuje místní spuštění Kubernetes. Minikube spouští cluster Kubernetes s jedním uzlem v rámci virtuálního počítače, který vám umožní vyzkoušet si Kubernetes nebo vyvíjet s využitím každodenního provozu. Podporuje jednoduchý cluster Kubernetes s jedním uzlem běžícího na virtuálním počítači se systémem Linux. Minikube je nejrychlejší a nejpřímější způsob, jak získat plně funkční cluster Kubernetes se systémem. Umožňuje vývojářům vyvíjet a testovat nasazení svých aplikací založených na Kubernetes na místních počítačích. V rámci architektury Minikube virtuální počítač spouští součásti hlavního uzlu i uzlu agenta místně:

* Komponenty hlavního uzlu, jako je Server API, Scheduler a [Etcd Server](https://coreos.com/etcd/) , se spouštějí v jednom procesu Linux s názvem **LocalKube**.
* Komponenty uzlu agenta jsou spouštěny uvnitř kontejnerů Docker přesně tak, jak by běžely v normálním uzlu agenta. Z pohledu nasazení aplikace není žádný rozdíl mezi nasazením aplikace na Minikube nebo v běžném clusteru Kubernetes.

Tato šablona nainstaluje následující součásti:

* Virtuální počítač Ubuntu 16,04 LTS
* [Docker – CE](https://download.docker.com/linux/ubuntu)
* [Kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl)
* [Minikube](https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64)
* xFCE4
* xRDP

> [!IMPORTANT]
> Image virtuálního počítače s Ubuntu (Ubuntu Server 16,04 LTS, v tomto příkladu) už musí být před provedením těchto kroků přidaná na web centra Azure Stack.

1. Vyberte **+ vytvořit prostředek**, pak **vlastní** a pak **template Deployment**.

   [![Vytvoření šablony](media/azure-stack-create-vm-template/vm-template-1.png)](media/azure-stack-create-vm-template/vm-template-1-expanded.png#lightbox)

2. Zadejte název šablony, která se má načíst. v tomto příkladu je to **101-VM-Linux-minikube**. Pak vyberte **Upravit šablonu**.

    [![Úprava šablony](media/azure-stack-create-vm-template/vm-template-2.png)](media/azure-stack-create-vm-template/vm-template-2-expanded.png#lightbox)

3. Vyberte **šablonu pro rychlý Start**. Pak z dostupných šablon vyberte z rozevíracího seznamu **Vybrat šablonu** **101-VM-Linux-minikube** a pak klikněte na **OK**.

    [![Šablona pro rychlý start](media/azure-stack-create-vm-template/vm-template-3.png)](media/azure-stack-create-vm-template/vm-template-3-expanded.png#lightbox)

4. Pokud chcete provést úpravy JSON šablony, můžete to udělat. Pokud ne, nebo po dokončení vyberte **Uložit** a zavřete dialogové okno **Upravit šablonu** .

5. Vyberte **parametry**, vyplňte nebo podle potřeby upravte pole k dispozici a pak klikněte na **OK**.

6. Zvolte předplatné, které chcete použít, a pak vytvořte nebo zvolte existující název skupiny prostředků. Pak vyberte **vytvořit** a spusťte tak nasazení šablony.

    [![Zvolit předplatné](media/azure-stack-create-vm-template/vm-template-4.png)](media/azure-stack-create-vm-template/vm-template-4-expanded.png#lightbox)

7. Nasazení skupiny prostředků trvá několik minut, než se vytvoří vlastní virtuální počítač založený na šablonách. Stav instalace můžete monitorovat prostřednictvím oznámení a z vlastností skupiny prostředků.

    >[!NOTE]
    > Po dokončení nasazení se virtuální počítač spustí.

## <a name="start-minikube-and-install-an-application"></a>Spuštění Minikube a instalace aplikace

Teď, když byl virtuální počítač se systémem Linux úspěšně vytvořen, se můžete přihlásit a spustit Minikube a nainstalovat aplikaci.

1. Po dokončení nasazení vyberte **připojit** a ZOBRAZTE veřejnou IP adresu, která se bude používat pro připojení k virtuálnímu počítači se systémem Linux.

    [![Síti](media/azure-stack-create-vm-template/vm-template-5.png)](media/azure-stack-create-vm-template/vm-template-5-expanded.png#lightbox)

2. Z příkazového řádku se zvýšenými oprávněními spusťte **mstsc.exe** a otevřete připojení ke vzdálené ploše a připojte se k veřejné IP adrese virtuálního počítače se systémem Linux zjištěné v předchozím kroku. Po zobrazení výzvy k přihlášení k xRDP použijte přihlašovací údaje, které jste zadali při vytváření virtuálního počítače.

    ![Vzdálené](media/azure-stack-create-vm-template/10.PNG)

3. Otevřete emulátor terminálu a zadáním následujících příkazů spusťte Minikube:

    ```shell
    sudo minikube start --vm-driver=none
    sudo minikube addons enable dashboard
    sudo minikube dashboard --url
    ```

    ![Spouštění příkazů](media/azure-stack-create-vm-template/11.PNG)

4. Otevřete prohlížeč a použijte adresu řídicího panelu Kubernetes. Blahopřejeme, nyní máte plně funkční instalaci Kubernetes s využitím Minikube.

    ![Řídicí panel](media/azure-stack-create-vm-template/12.PNG)

5. Pokud chcete nasadit ukázkovou aplikaci, přejděte na stránku oficiální dokumentace Kubernetes a přeskočte oddíl "vytvořit Minikube cluster", protože jste ho už vytvořili. V části [vytvořte aplikaci Node.js](https://kubernetes.io/docs/tutorials/stateless-application/hello-minikube/).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Další informace o šablonách rychlého startu centra Azure Stack
> * Vytvoření virtuálního počítače pomocí vlastní šablony GitHubu
> * Spuštění minikube a instalace aplikace
