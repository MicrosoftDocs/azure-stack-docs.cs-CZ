---
title: Rychlý Start k nastavení služby Azure Kubernetes v Azure Stack HCI pomocí centra pro správu Windows
description: Přečtěte si, jak nastavit službu Azure Kubernetes v Azure Stack HCI pomocí centra pro správu Windows.
author: davannaw-msft
ms.topic: quickstart
ms.date: 01/22/2021
ms.author: dawhite
ms.openlocfilehash: de54cf3f93462cfc63b6f7f2074343feacd25479
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101840458"
---
# <a name="quickstart-set-up-azure-kubernetes-service-on-azure-stack-hci-using-windows-admin-center"></a>Rychlý Start: nastavení služby Azure Kubernetes v Azure Stack HCI pomocí centra pro správu Windows

> Platí pro: Azure Stack HCI, Windows Server 2019 Datacenter

V tomto rychlém startu nastavíte službu Azure Kubernetes na Azure Stack HCI pomocí centra pro správu systému Windows. Pokud chcete místo toho použít PowerShell, přečtěte si téma [nastavení pomocí PowerShellu](setup-powershell.md).

Nastavení zahrnuje následující úlohy:

* Stažení služby Azure Kubernetes na Azure Stack HCL
* Nastavte centrum pro správu Windows, pokud jste to ještě neudělali.
* Instalace rozšíření služby Azure Kubernetes pro centrum pro správu systému Windows
* Nastavte hostitele služby Azure Kubernetes v systému, do kterého chcete nasadit cluster Kubernetes.

Než začnete, ujistěte se, že jste splnili všechny požadavky na stránce [požadavky na systém](.\system-requirements.md) .

## <a name="download-azure-kubernetes-service-on-azure-stack-hci"></a>Stažení služby Azure Kubernetes na Azure Stack HCL

Pokud jste si ještě nestáhli software verze Preview, přečtěte si téma [vyhodnocení AKS na Azure Stack HCI](https://aka.ms/AKS-HCI-Evaluate). Budete požádáni, abyste si stáhli AKS na Azure Stack HCI a také v centru pro správu systému Windows.

## <a name="setting-up-windows-admin-center"></a>Nastavení centra pro správu Windows

Pokud jste ještě nenainstalovali centrum pro správu systému Windows, přečtěte si téma [Instalace centra pro správu systému Windows](/windows-server/manage/windows-admin-center/deploy/install). Centrum pro správu systému Windows můžete spustit buď na počítači s Windows 10, nebo na serveru. Služba Azure Kubernetes v Azure Stack funkce HCI je k dispozici pouze v centru pro správu systému Windows Build 2009 nebo vyšší.

## <a name="installing-the-azure-kubernetes-service-extension"></a>Instalace rozšíření služby Azure Kubernetes

Jakmile obdržíte službu Azure Kubernetes na Azure Stack soubory ve verzi Public Preview, musíte `.nupkg` soubor uložit místně nebo do sdílené složky SMB a přidat cestu k souboru do seznamu "informační kanály" ve Správci rozšíření v centru pro správu systému Windows. `.nupkg`Soubor je balíček NuGet, který obsahuje rozšíření centra pro správu systému Windows.

Pokud chcete získat přístup k existujícímu informačnímu kanálu rozšíření, otevřete centrum pro správu Windows a v pravém horním rohu obrazovky vyberte ozubené kolečko. Tím přejdete do nabídky nastavení. Informační kanály rozšíření najdete v části **Brána** v nabídce **rozšíření** . Přejděte na kartu **informační kanály** a vyberte **Přidat**. V tomto podokně vložte cestu k souboru do vaší kopie rozšíření služby Azure Kubernetes a vyberte **Přidat**. Pokud se vaše cesta k souboru úspěšně přidala, obdržíte oznámení o úspěšném dokončení. 

Teď, když jsme přidali informační kanál, bude rozšíření služby Azure Kubernetes dostupné v seznamu dostupných rozšíření. Po výběru rozšíření vyberte v horní části tabulky **instalovat** a nainstalujte toto rozšíření. Centrum pro správu systému Windows bude po dokončení instalace znovu načteno. 

[![Zobrazení seznamu dostupných rozšíření ve Správci rozšíření centra pro správu systému Windows. ](.\media\setup\extension-manager.png)](.\media\setup\extension-manager.png#lightbox)

## <a name="setting-up-an-azure-kubernetes-service-host"></a>Nastavení hostitele služby Azure Kubernetes

Před vytvořením clusteru Kubernetes je třeba provést jeden finální krok. V systému, do kterého chcete nasadit cluster Kubernetes, musíte nastavit hostitele služby Azure Kubernetes. Tato možnost je také označována jako nastavení služby platformy nebo clusteru pro správu.  

[![Obrázek diagramu architektury, který zvýrazní část služeb platformy](.\media\setup\aks-hci-architecture-focused.png)](.\media\setup\aks-hci-architecture-focused.png) 

Tímto systémem může být cluster datacenter Windows serveru 2019, jeden uzel Windows Server 2019 Datacenter nebo uzel 2-4 Azure Stack HCL. 

> [!NOTE] 
> Nastavení hostitelů služby Azure Kubernetes na dva nezávislé systémy a jejich záměr sloučení během vytváření clusteru Kubernetes není podporovaným scénářem. 

Tato nastavení se dají udělat pomocí nového nástroje Azure Kubernetes Service. 

Tento nástroj nainstaluje a stáhne potřebné balíčky a také vytvoří hostitelský cluster AKS, který poskytuje základní služby Kubernetes a orchestruje zatížení aplikací. 

Teď, když jsme ověřili naše nastavení systému, můžeme začít: 
1. Vyberte **nastavit** pro spuštění Průvodce nastavením.
2. Přečtěte si požadavky na počítač, ke kterému je spuštěno centrum pro správu systému Windows, cluster, ke kterému jste připojeni, a na síť. Dále se ujistěte, že jste se přihlásili k účtu Azure v centru pro správu Windows a že předplatné Azure, které plánujete použít, nevypršelo. Až budete hotovi, vyberte **Další**.

> [!WARNING]
> Pokud používáte cluster 2-4 Azure Stack HCL, ujistěte se, že jste nakonfigurovali aspoň jeden externí virtuální přepínač předtím, než budete pokračovat v tomto kroku, nebo nebudete moct úspěšně nastavit hostitele služby Azure Kubernetes.

3. Na stránce **Kontrola systému** v průvodci proveďte všechny požadované akce, například [připojení brány centra pro správu Windows k Azure](/windows-server/manage/windows-admin-center/azure/azure-integration). Tento krok zkontroluje, jestli má centrum pro správu Windows a systém, který bude hostovat službu Azure Kubernetes, správné konfigurace, aby bylo možné pokračovat. Až skončíte s prováděním akce, vyberte **Další**.
4. Nakonfigurujte počítač, který bude hostovat službu Azure Kubernetes, v kroku **Konfigurace hostitele** . V této části doporučujeme vybrat možnost **automaticky stahovat aktualizace** . Tento krok průvodce vás vyzve ke konfiguraci následujících podrobností:
    * **Podrobnosti o hostiteli**, jako je název clusteru hostitelů AKS a adresář imagí, kde se budou ukládat image virtuálních počítačů. Adresář imagí musí ukazovat na cestu ke sdílenému úložišti nebo na sdílenou složku protokolu SMB, ke které má hostitelský počítač přístup.
    * **Sítě virtuálních počítačů**, které se budou vztahovat na všechny virtuální počítače se systémem Linux a Windows (uzly) vytvořené pro spouštění kontejnerů a orchestrace správy kontejnerů. Patří sem pole pro virtuální přepínač s připojením k Internetu, povolení pro identifikaci virtuální sítě LAN, metoda přidělování IP adres a Cloudagent IP adresa. Cloudagent IP adresu můžete použít k poskytnutí statické IP adresy službě CloudAgent. To platí bez ohledu na výběr přidělení IP adresy. Pokud jste vybrali metodu přidělování statických IP adres, je třeba zadat několik dalších polí:
      - **Předpona podsítě**, rozsah IP adres, který není v konfliktu s jinými adresami
      - **Brána**, přes kterou se budou směrovat pakety mimo počítač
      - **Servery DNS**, IP adresy serverů DNS v síti
      - **Spuštění fondu IP adres uzlu Kubernetes**, rozsah spuštění fondu pro IP adresy, které používá clustery Kubernetes
      - **Konec fondu IP adres uzlu Kubernetes**, koncový rozsah fondu pro IP adresy, které používá clustery Kubernetes
    * **Nastavení nástroje pro vyrovnávání zatížení**, které definuje fond adres používaných pro externí služby. Pokud jste v části sítě virtuálních počítačů vybrali statickou konfiguraci, musí být počátek a konec fondu adres v rozsahu podsítě zadaném v této části. 

    ![Ukazuje krok konfigurace hostitele Průvodce hostitelem služby Azure Kubernetes.](.\media\setup\host-configuration.png)
    
    Až budete hotovi, vyberte **Další** .

5. Na stránce průvodce **registrace Azure** zadejte podrobnosti o předplatném a skupině prostředků, které chcete použít pro tuto službu. I když je služba Azure Kubernetes ve verzi Preview, nebudeme vám nic účtovat. Až budete hotovi, vyberte **Další**.
6. Projděte si všechny vaše výběry v kroku **Revize + vytvořit** . Pokud jste s vybranými možnostmi spokojeni, vyberte **Další** a spusťte instalaci hostitele. 
7. Na stránce **průběh instalace** můžete sledovat průběh instalace hostitele. V tuto chvíli jste připraveni otevřít Centrum pro správu Windows na nové kartě a pokračovat v úlohách správy. 
8. Pokud je nasazení úspěšné, vyberte *Dokončit* a zobrazí se řídicí panel pro správu, kde můžete vytvářet a spravovat clustery Kubernetes. Tento řídicí panel, podobně jako zbytek služeb Azure Kubernetes ve Azure Stack HCI, je ve verzi Preview a bude v budoucích verzích aktualizovat s dalšími funkcemi.
 
  ![Ukazuje služby Azure Kubernetes na řídicím panelu pro správu HCI Azure Stack.](.\media\setup\dashboard.png)
 
## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nainstalovali centrum pro správu systému Windows a rozšíření služby Azure Kubernetes. Nakonfigurovali jste také hostitele služby Azure Kubernetes v systému, do kterého budete cluster Kubernetes nasazovat.

Nyní můžete začít s [vytvářením clusteru Kubernetes v centru pro správu Windows](create-kubernetes-cluster.md).
