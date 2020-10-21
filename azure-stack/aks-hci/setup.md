---
title: Rychlý Start k nastavení služby Azure Kubernetes v Azure Stack HCI pomocí centra pro správu Windows
description: Přečtěte si, jak nastavit službu Azure Kubernetes v Azure Stack HCI pomocí centra pro správu Windows.
author: davannaw-msft
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: 2ac65146c78c2ed1aaf9b98ee50392a13be050c4
ms.sourcegitcommit: be445f183d003106192f039990d1fb8ee151c8d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253991"
---
# <a name="quickstart-set-up-azure-kubernetes-service-on-azure-stack-hci-using-windows-admin-center"></a>Rychlý Start: nastavení služby Azure Kubernetes v Azure Stack HCI pomocí centra pro správu Windows

> Platí pro: Azure Stack HCI

V tomto rychlém startu nastavíte službu Azure Kubernetes na Azure Stack HCI pomocí centra pro správu systému Windows. Pokud chcete místo toho použít PowerShell, přečtěte si téma [nastavení pomocí PowerShellu](setup-powershell.md).

Nastavení zahrnuje následující úlohy:

* Stažení služby Azure Kubernetes na Azure Stack HCL
* Nastavte centrum pro správu Windows, pokud jste to ještě neudělali.
* Instalace služby Azure Kubernetes pro Azure Stack rozšíření HCI pro centrum pro správu Windows
* Nastavte hostitele služby Azure Kubernetes v systému, do kterého chcete nasadit cluster Kubernetes.

Než začnete, ujistěte se, že jste splnili všechny požadavky na stránce [požadavky na systém](.\system-requirements.md) .

## <a name="download-azure-kubernetes-service-on-azure-stack-hci"></a>Stažení služby Azure Kubernetes na Azure Stack HCL

Pokud jste si ještě nestáhli software verze Preview, přečtěte si téma [vyhodnocení AKS na Azure Stack HCI](https://aka.ms/AKS-HCI-Evaluate). Budete požádáni, abyste si stáhli AKS na Azure Stack HCL i v centru pro správu systému Windows.

## <a name="setting-up-windows-admin-center"></a>Nastavení centra pro správu Windows

Pokud jste ještě nenainstalovali centrum pro správu systému Windows, přečtěte si téma [Instalace centra pro správu systému Windows](/windows-server/manage/windows-admin-center/deploy/install). V případě veřejné verze Preview služby Azure Kubernetes Service na Azure Stack HCI je nutné stáhnout a spustit centrum pro správu systému Windows na počítači s Windows 10. Pouze režim Desktop Center pro správu systému Windows je kompatibilní se službou Azure Kubernetes Service v Azure Stack HCL hned. Služba Azure Kubernetes v Azure Stack funkce HCI je k dispozici pouze v centru pro správu systému Windows Build 2009 nebo vyšší.

## <a name="installing-the-azure-kubernetes-service-extension"></a>Instalace rozšíření služby Azure Kubernetes

Jakmile obdržíte službu Azure Kubernetes na Azure Stack soubory ve verzi Public Preview, musíte `.nupkg` soubor uložit místně nebo do sdílené složky SMB a přidat cestu k souboru do seznamu "informační kanály" ve Správci rozšíření v centru pro správu systému Windows. `.nupkg`Soubor je balíček NuGet, který obsahuje rozšíření centra pro správu systému Windows.

Pokud chcete získat přístup k existujícímu informačnímu kanálu rozšíření, otevřete centrum pro správu Windows a v pravém horním rohu obrazovky vyberte ozubené kolečko. Tím přejdete do nabídky nastavení. Informační kanály rozšíření najdete v části **Brána** v nabídce **rozšíření** . Přejděte na kartu **informační kanály** a vyberte **Přidat**. V tomto podokně vložte cestu k souboru do vaší kopie služby Azure Kubernetes v Azure Stack rozšíření HCI a vyberte **Přidat**. Pokud se vaše cesta k souboru úspěšně přidala, obdržíte oznámení o úspěšném dokončení. 

Teď, když jsme přidali informační kanál, bude v seznamu dostupných rozšíření dostupná služba Azure Kubernetes na Azure Stack rozšíření HCI. Po výběru rozšíření vyberte v horní části tabulky **instalovat** a nainstalujte toto rozšíření. Centrum pro správu systému Windows bude po dokončení instalace znovu načteno. 

[![Zobrazení seznamu dostupných rozšíření ve Správci rozšíření centra pro správu systému Windows. ](.\media\setup\extension-manager.png)](.\media\setup\extension-manager.png#lightbox)

## <a name="setting-up-an-azure-kubernetes-service-host"></a>Nastavení hostitele služby Azure Kubernetes

Před vytvořením clusteru Kubernetes je třeba provést jeden finální krok. V systému, do kterého chcete nasadit cluster Kubernetes, musíte nastavit hostitele služby Azure Kubernetes. Tento systém musí být Azure Stack cluster HCI. 

> [!NOTE] 
> Nastavení hostitelů služby Azure Kubernetes na dva nezávislé systémy a jejich záměr sloučení během vytváření clusteru Kubernetes není podporovaným scénářem. 

Tato nastavení se dají udělat pomocí nového nástroje Azure Kubernetes Service. 

Tento nástroj nainstaluje a stáhne potřebné balíčky a také vytvoří cluster pro správu, který poskytuje základní služby Kubernetes Services a orchestruje zatížení aplikací. 

Před použitím tohoto nástroje otevřete PowerShell a na každém uzlu spusťte následující příkaz, abyste zajistili, že přihlášení do Azure není blokované žádným nastavením počítače:
```PowerShell
az login
```

Teď, když jsme ověřili naše nastavení systému, můžeme začít: 
1. Vyberte **nastavit** pro spuštění Průvodce nastavením.
2. Projděte si požadavky na počítač, na kterém je spuštěno centrum pro správu systému Windows, Azure Stack cluster HCI, ke kterému jste se připojili, a také na síť. Dále se ujistěte, že jste se přihlásili k účtu Azure v centru pro správu Windows a že předplatné Azure, které plánujete použít, nevypršelo. Až budete hotovi, vyberte **Další**.
3. Na stránce **Kontrola systému** v průvodci proveďte všechny požadované akce, například připojení brány centra pro správu Windows k Azure. Tento krok zkontroluje, jestli má centrum pro správu Windows a systém, který bude hostovat službu Azure Kubernetes, správné konfigurace, aby bylo možné pokračovat. Až skončíte s prováděním akce, vyberte **Další**.
4. Nakonfigurujte počítač, který bude hostovat službu Azure Kubernetes, v kroku **Konfigurace hostitele** . V této části doporučujeme vybrat možnost **automaticky stahovat aktualizace** . Až budete hotovi, vyberte **Další** . Tento krok průvodce vás vyzve ke konfiguraci následujících podrobností:
    * Podrobnosti o hostiteli, jako je název clusteru pro správu a složka pro ukládání imagí virtuálních počítačů
    * Sítě virtuálních počítačů, které se budou vztahovat na všechny virtuální počítače se systémem Linux a Windows (uzly) vytvořené pro spouštění kontejnerů a orchestrace správy kontejnerů. 
    * nastavení nástroje pro vyrovnávání zatížení, které definuje fond adres používaných pro externí služby

    ![Ukazuje krok konfigurace hostitele Průvodce hostitelem služby Azure Kubernetes.](.\media\setup\host-configuration.png)

5. Zaregistrujte se v Azure a pomocí kroku **Registrace do Azure** můžete odeslat diagnostická data do Microsoftu. I když se tato stránka zeptá na předplatné Azure a skupinu prostředků, ve verzi Public Preview se neúčtují žádné náklady na nastavení a používání služby Azure Kubernetes. Diagnostická data, která odesíláte společnosti Microsoft, budou sloužit k zajištění zabezpečení a aktuálnosti služby, řešení potíží a vylepšení produktu. Po provedení výběru vyberte **Další**.
6. Projděte si všechny vaše výběry, zatím v kroku **Kontrola a vytvoření** . Pokud jste s vybranými možnostmi spokojeni, vyberte **nastavit** pro zahájení instalace hostitele. 
7. Na stránce **průběh instalace** můžete sledovat průběh instalace hostitele. V tuto chvíli jste připraveni otevřít Centrum pro správu Windows na nové kartě a pokračovat v úlohách správy. 
8. Pokud je nasazení úspěšné, zobrazí se další kroky a tlačítko **Dokončit** bude povolené. Výběrem možnosti **Stáhnout cluster pro správu kubeconfig** v části **Další kroky** zahájíte stahování a nebudete přesměrováni z průvodce. 

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nainstalovali centrum pro správu systému Windows a službu Azure Kubernetes pro Azure Stack rozšíření HCI. Nakonfigurovali jste také hostitele služby Azure Kubernetes v systému, do kterého budete cluster Kubernetes nasazovat.

Nyní můžete začít s [vytvářením clusteru Kubernetes v centru pro správu Windows](create-kubernetes-cluster.md).