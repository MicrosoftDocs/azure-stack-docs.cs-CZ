---
title: Aktualizace Azure Stackch clusterů HCI
description: Jak použít aktualizace operačního systému a firmwaru Azure Stack HCL pomocí centra pro správu Windows a PowerShellu.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 06a5a1ccf59b5d5c34ef1d2e36feeb1000b49776
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572632"
---
# <a name="update-azure-stack-hci-clusters"></a>Aktualizace Azure Stackch clusterů HCI

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

Když aktualizujete clustery Azure Stack HCI, cílem je zachovat dostupnost tím, že se v clusteru aktualizuje jenom jeden server. Mnoho aktualizací operačního systému vyžaduje převedení serveru do režimu offline, například k restartování nebo k aktualizaci softwaru, například síťového zásobníku. Doporučujeme používat funkci [aktualizace pro clustery (CAU)](/windows-server/failover-clustering/cluster-aware-updating), která usnadňuje instalaci aktualizací Windows na všechny servery v clusteru a přitom udržuje spuštěné aplikace automatizací procesu aktualizace softwaru. Aktualizace pro clustery se dají používat ve všech edicích Windows serveru, včetně instalací jádra serveru, a dá se iniciovat buď prostřednictvím centra pro správu Windows, nebo pomocí PowerShellu.

## <a name="update-a-cluster-using-windows-admin-center"></a>Aktualizace clusteru pomocí centra pro správu Windows

Centrum pro správu Windows usnadňuje aktualizaci clusteru a instalaci operačního systému a řešení pomocí jednoduchého uživatelského rozhraní. Pokud jste si zakoupili integrovaný systém od hardwarového partnera Microsoftu, můžete k tomu snadno získat nejnovější ovladače, firmware a další aktualizace přímo z centra pro správu Windows, a to tak, že nainstalujete příslušné rozšíření partnerské aktualizace. Pokud jste hardware nekoupili jako integrovaný systém, může být nutné provést aktualizace firmwaru a ovladačů samostatně, a to podle doporučení dodavatele hardwaru.

Centrum pro správu systému Windows zkontroluje, jestli je cluster správně nakonfigurovaný tak, aby spouštěl aktualizace pro clustery, a v případě potřeby se zobrazí dotaz, jestli chcete pro vás nakonfigurovat funkci aktualizace pro clustery (CAU), včetně instalace role clusteru CAU a povolení požadovaných pravidel brány firewall.

1. Když se připojíte ke clusteru, řídicí panel centra pro správu systému Windows vás upozorní, pokud má jeden nebo více serverů aktualizace připravené k instalaci, a poskytne odkaz na aktualizaci nyní. Alternativně můžete vybrat **aktualizace** z nabídky **nástroje** vlevo.
1. Chcete-li použít nástroj pro aktualizaci pro clustery v centru pro správu systému Windows, je nutné povolit zprostředkovatele CredSSP (Credential Security Service Provider) a zadat explicitní přihlašovací údaje. Až se zobrazí dotaz, jestli by se měl povolit CredSSP, klikněte na **Ano**.
1. Zadejte své uživatelské jméno a heslo a klikněte na **pokračovat**.
1. Zobrazí se všechny dostupné aktualizace. Kliknutím na možnost **ověřit dostupné aktualizace** seznam aktualizujte.
1. Vyberte aktualizace, které chcete nainstalovat, a klikněte na **použít všechny aktualizace**. Tím se aktualizace nainstalují na každý server v clusteru. Pokud je potřeba restartování, role clusteru, jako jsou virtuální počítače, se nejprve přesunou na jiný server, aby se zabránilo jakémukoli přerušení.
1. Pokud chcete zvýšit zabezpečení, zakažte zprostředkovatele CredSSP hned po dokončení instalace aktualizací:
    - V centru pro správu systému Windows v části **všechna připojení**vyberte první server v clusteru a pak vyberte **připojit**.
    - Na stránce **Přehled** vyberte **Zakázat CredSSP**a potom v místním okně **vypnout zprostředkovatele CredSSP** vyberte **Ano**.

## <a name="update-a-cluster-using-powershell"></a>Aktualizace clusteru pomocí PowerShellu

Než budete moct aktualizovat cluster pomocí aktualizace pro clustery, musíte nejdřív nainstalovat **Nástroje clusteringu s podporou převzetí služeb při selhání**, které jsou součástí **Nástroje pro vzdálenou správu serveru (RSAT)** , a zahrnout software aktualizace pro clustery. Pokud aktualizujete existující cluster, tyto nástroje již mohou být nainstalovány.

Pokud chcete otestovat, jestli je cluster s podporou převzetí služeb při selhání správně nastavený na použití aktualizací softwaru pomocí aktualizace pro clustery, spusťte rutinu PowerShellu **Test-CauSetup** , která provádí kontrolu v clusteru s podporou převzetí služeb při selhání a v síťovém prostředí Analyzátor osvědčených postupů (BPA) a upozorní vás na všechna upozornění a chyby:

```PowerShell
Test-CauSetup -ClusterName Cluster1
```

Pokud potřebujete nainstalovat funkce, nástroje nebo role, přečtěte si další části. V opačném případě přeskočte dopředu a [vyhledejte aktualizace pomocí PowerShellu](#check-for-updates-with-powershell).

### <a name="install-failover-clustering-and-failover-clustering-tools-using-powershell"></a>Instalace nástrojů clusteringu s podporou převzetí služeb při selhání a clustering s podporou převzetí služeb

Pokud chcete zjistit, jestli má cluster nebo Server funkce clusteringu s podporou převzetí služeb při selhání a nástroje clusteringu s podporou převzetí služeb při selhání nainstalované, vydejte na **`Get-WindowsFeature`** počítači pro správu rutinu PowerShellu (nebo ji spusťte přímo na clusteru nebo na serveru s vynecháním parametru-ComputerName):

```PowerShell
Get-WindowsFeature -Name Failover*, RSAT-Clustering* -ComputerName Server1
```

Ujistěte se, že je nainstalovaná možnost stav instalace a že se X zobrazí před clusteringem s podporou převzetí služeb při selhání i clusterem s podporou převzetí služeb při selhání pro Windows

```
Display Name                                            Name                       Install State
------------                                            ----                       -------------
[X] Failover Clustering                                 Failover-Clustering            Installed
        [X] Failover Clustering Tools                   RSAT-Clustering                Installed
            [X] Failover Cluster Module for Windows ... RSAT-Clustering-Powe...        Installed
            [ ] Failover Cluster Automation Server      RSAT-Clustering-Auto...        Available
            [ ] Failover Cluster Command Interface      RSAT-Clustering-CmdI...        Available
```

Pokud funkce clusteringu s podporou převzetí služeb při selhání není nainstalovaná, nainstalujte ji na každý server v clusteru pomocí **`Install-WindowsFeature`** rutiny pomocí parametrů-IncludeAllSubFeature a-IncludeManagementTools:

```PowerShell
Install-WindowsFeature –Name Failover-Clustering -IncludeAllSubFeature –IncludeManagementTools -ComputerName Server1
```

Tento příkaz nainstaluje také modul clusteru s podporou převzetí služeb při selhání pro PowerShell, který zahrnuje rutiny prostředí PowerShell pro správu clusterů s podporou převzetí služeb při selhání a modul aktualizace pro clustery pro PowerShell pro instalaci aktualizací softwaru v clusterech s podporou převzetí

Pokud je funkce clusteringu s podporou převzetí služeb při selhání už nainstalovaná, ale modul clusteru s podporou převzetí služeb při selhání pro prostředí Windows PowerShell není, stačí na každý server v clusteru nainstalovat rutinu **Install-WindowsFeature** :

```PowerShell
Install-WindowsFeature –Name RSAT-Clustering-PowerShell -ComputerName Server1
```

### <a name="choose-an-updating-mode"></a>Zvolit režim aktualizace

Aktualizace pro clustery může koordinovat úplnou operaci aktualizace clusteru ve dvou režimech:  
  
-   **Režim automatických aktualizací** V tomto režimu je Clusterová role aktualizace pro clustery nakonfigurovaná jako zatížení v clusteru s podporou převzetí služeb při selhání, který se má aktualizovat, a je definovaný přidružený plán aktualizací. Cluster se aktualizuje v naplánovaném čase pomocí výchozího nebo vlastního profilu hromadné postupné aktualizace. Během hromadné postupné aktualizace se proces koordinátoru aktualizace pro clustery aktualizuje na uzlu, který aktuálně vlastní clusterovou roli aktualizace pro clustery, a proces provede aktualizace postupně na všech uzlech clusteru. Pokud chcete aktualizovat aktuální uzel clusteru, převezme Clusterová role aktualizace pro clustery převzetí služeb při selhání jiným uzlem clusteru a nový proces koordinátora aktualizace v tomto uzlu předpokládá kontrolu nad spuštěním hromadné postupné aktualizace. V režimu automatických aktualizací může aktualizace pro clustery aktualizovat cluster s podporou převzetí služeb při selhání pomocí plně automatizovaného a kompletního procesu aktualizace. Správce může v tomto režimu taky aktivovat aktualizace na vyžádání, nebo pokud to bude potřeba, jednoduše použijte postup vzdálené aktualizace.
  
-   **Režim vzdálené aktualizace** V tomto režimu se jedná o počítač pro vzdálenou správu (obvykle počítač s Windows 10), který má síťové připojení k clusteru s podporou převzetí služeb při selhání, ale není členem clusteru s podporou převzetí služeb při selhání s nástroji clusteringu. V počítači pro vzdálenou správu, který se označuje jako koordinátor aktualizace, správce aktivuje hromadnou postupnou aktualizaci na vyžádání pomocí výchozího nebo vlastního profilu hromadné postupné aktualizace. Režim vzdálených aktualizací je užitečný pro sledování průběhu hromadné postupné aktualizace v reálném čase a pro clustery, které běží na instalacích jádra serveru.  

   > [!NOTE]
   > Počínaje verzí Windows 10 říjen 2018 se RSAT nabízí jako sada funkcí na vyžádání přímo z Windows 10. Stačí přejít na **nastavení > aplikace > aplikace & funkce > volitelné funkce > přidat funkci > RSAT: Nástroje clusteringu s podporou převzetí služeb při selhání**a vybrat **nainstalovat**. Chcete-li zobrazit průběh instalace, klikněte na tlačítko zpět a zobrazte stav na stránce Správa volitelných funkcí. Nainstalovaná funkce bude zachována v rámci upgradu verze Windows 10. Pokud chcete nainstalovat RSAT pro Windows 10 před aktualizací z října 2018, [Stáhněte si balíček pro vzdálenou správu](https://www.microsoft.com/download/details.aspx?id=45520).

### <a name="add-cau-cluster-role-to-the-cluster"></a>Přidání role clusteru funkce CAU do clusteru

Pro režim automatických aktualizací se vyžaduje role clusteru aktualizace pro clustery. Pokud k provedení aktualizací používáte centrum pro správu systému Windows, bude role clusteru automaticky přidána.

**`Get-CauClusterRole`** Rutina zobrazí vlastnosti konfigurace role clusteru aktualizace pro clustery v zadaném clusteru.

```PowerShell
Get-CauClusterRole -ClusterName Cluster1
```

Pokud tato role v clusteru ještě není nakonfigurovaná, zobrazí se tato chybová zpráva:

```Get-CauClusterRole : The current cluster is not configured with a Cluster-Aware Updating clustered role.```

Chcete-li přidat roli clusteru aktualizace pro clustery pro režim automatických aktualizací pomocí prostředí PowerShell, použijte tuto **`Add-CauClusterRole`** rutinu a zadejte příslušné [parametry](/powershell/module/clusterawareupdating/add-cauclusterrole#parameters), jako v následujícím příkladu:

```PowerShell
Add-CauClusterRole -ClusterName Cluster1 -MaxFailedNodes 0 -RequireAllNodesOnline -EnableFirewallRules -VirtualComputerObjectName Cluster1-CAU -Force -CauPluginName Microsoft.WindowsUpdatePlugin -MaxRetriesPerNode 3 -CauPluginArguments @{ 'IncludeRecommendedUpdates' = 'False' } -StartDate "3/2/2020 3:00:00 AM" -DaysOfWeek 4 -WeeksOfMonth @(3) -verbose
```

   > [!NOTE]
   > Výše uvedený příkaz je třeba spustit z počítače pro správu nebo z řadiče domény.

### <a name="enable-firewall-rules-to-allow-remote-restarts"></a>Povolit pravidla brány firewall pro povolení vzdáleného restartování

V průběhu procesu aktualizace budete muset servery nechat vzdáleně restartovat. Pokud k provedení aktualizací používáte centrum pro správu systému Windows, pravidla brány Windows firewall budou automaticky aktualizována na každém serveru, aby bylo možné vzdálené restartování. Pokud aktualizujete pomocí PowerShellu, buď povolte skupinu pravidel brány firewall vzdáleného vypnutí v bráně Windows Firewall, nebo předejte parametr-EnableFirewallRules rutině, například v předchozím příkladu.

## <a name="check-for-updates-with-powershell"></a>Vyhledat aktualizace pomocí PowerShellu

Pomocí **`Invoke-CAUScan`** rutiny můžete vyhledat příslušné aktualizace a získat seznam počáteční sady aktualizací, které se použijí na každém serveru v zadaném clusteru:

```PowerShell
Invoke-CauScan -ClusterName Cluster1 -CauPluginName Microsoft.WindowsUpdatePlugin -Verbose
```

Dokončení generování seznamu může trvat několik minut. Seznam náhledu obsahuje jenom počáteční sadu aktualizací. nezahrnuje aktualizace, které se můžou použít po instalaci počátečních aktualizací.

## <a name="install-updates-with-powershell"></a>Instalace aktualizací pomocí PowerShellu

Chcete-li vyhledat příslušné aktualizace serveru a provést úplnou hromadnou postupnou aktualizaci v zadaném clusteru, použijte **`Invoke-CAURun`** rutinu:

```PowerShell
Invoke-CauRun -ClusterName Cluster1 -CauPluginName Microsoft.WindowsUpdatePlugin -MaxFailedNodes 1 -MaxRetriesPerNode 3 -RequireAllNodesOnline -EnableFirewallRules -Force
```

Tento příkaz provede kontrolu a úplnou hromadnou postupnou aktualizaci spuštěnou v clusteru s názvem Cluster1. Tato rutina používá modul plug-in Microsoft. WindowsUpdatePlugin a před spuštěním této rutiny vyžaduje, aby byly všechny uzly clusteru online. Kromě toho tato rutina neumožňuje více než tři opakované pokusy na uzel před označením uzlu jako neúspěšné a umožňuje, aby nedošlo k selhání více než jednoho uzlu před označením celé spuštěné hromadné postupné aktualizace jako nezdařené. Umožňuje taky pravidla firewallu, která umožní vzdálené restartování serverů. Vzhledem k tomu, že příkaz určuje parametr Force, rutina se spustí bez zobrazení výzev k potvrzení.

Proces hromadné postupné aktualizace zahrnuje následující: 
- Vyhledávání a stahování příslušných aktualizací na každém serveru v clusteru
- Přesun aktuálně spuštěných clusterových rolí mimo jednotlivé servery
- Instalace aktualizací na každý server
- Restartování serveru v případě potřeby nainstalovaných aktualizací
- Přesun clusterovaných rolí zpátky na původní server

Proces hromadné postupné aktualizace zahrnuje i jistotu, že je kvorum zachováno, probíhá kontrola dalších aktualizací, které lze nainstalovat pouze po instalaci počáteční sady aktualizací a uložení sestavy provedených akcí.

## <a name="check-on-the-status-of-an-updating-run"></a>Podívejte se na stav hromadné postupné aktualizace.

Správce může získat souhrnné informace o probíhající hromadné postupné aktualizaci spuštěním **`Get-CauRun`** rutiny:

```PowerShell
Get-CauRun -ClusterName Cluster1
```

Zde je ukázkový výstup:

```
RunId                   : 834dd11e-584b-41f2-8d22-4c9c0471dbad 
RunStartTime            : 10/13/2019 1:35:39 PM 
CurrentOrchestrator     : NODE1 
NodeStatusNotifications : { 
Node      : NODE1 
Status    : Waiting 
Timestamp : 10/13/2019 1:35:49 PM 
} 
NodeResults             : { 
Node                     : NODE2 
Status                   : Succeeded 
ErrorRecordData          : 
NumberOfSucceededUpdates : 0 
NumberOfFailedUpdates    : 0 
InstallResults           : Microsoft.ClusterAwareUpdating.UpdateInstallResult[] 
}
```

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Aktualizuje se firmware jednotky v Prostory úložiště s přímým přístupem](/windows-server/storage/update-firmware)
- [Ověření Azure Stack clusteru HCI](../deploy/validate.md)
