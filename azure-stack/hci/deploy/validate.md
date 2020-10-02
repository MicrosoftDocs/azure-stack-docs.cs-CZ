---
title: Ověření Azure Stack clusteru HCI
description: Pochopení důležitosti ověření clusteru a jeho spuštění v existujícím clusteru Azure Stack HCI. Prozkoumejte scénáře pro řešení potíží s aktualizovaným serverovým clusterem.
author: JohnCobb1
ms.author: v-johcob
ms.topic: article
ms.date: 10/1/2020
ms.openlocfilehash: 784f34763f45e7096f72aa23698f9e78cf1bf9b4
ms.sourcegitcommit: 09572e1442c96a5a1c52fac8ee6b0395e42ab77d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91625851"
---
# <a name="validate-an-azure-stack-hci-cluster"></a>Ověření Azure Stack clusteru HCI

>Platí pro: Azure Stack HCI, verze v20H2; Windows Server 2019

Tento článek se zaměřuje na to, proč je ověření clusteru důležité a kdy ho spustit v existujícím clusteru Azure Stack HCI. Ověření clusteru doporučujeme provést v následujících primárních scénářích:
- Po nasazení serverového clusteru spusťte nástroj Validate-DCB pro otestování sítě.
- Po aktualizaci clusteru serveru v závislosti na vašem scénáři spusťte obě možnosti ověřování, abyste mohli řešit problémy s clustery.
- Po nastavení replikace pomocí repliky úložiště ověřte, že replikace probíhá normálně, a to tak, že zkontroluje některé konkrétní události a spustí několik příkazů.
- Po vytvoření serverového clusteru spusťte nástroj Validate-DCB před jeho umístěním do produkčního prostředí.

    Další informace o tom, jak nasadit cluster Azure Stack HCI, najdete v tématu [Přehled nasazení](/deploy/deployment-overview).

## <a name="what-is-cluster-validation"></a>Co je ověření clusteru?
Ověření clusteru má za cíl zachytit problémy s hardwarem nebo konfigurací, než cluster přejde do produkčního prostředí. Ověření clusteru pomáhá zajistit, že Azure Stack řešení HCI, které se chystáte nasadit, je skutečně závislé. V konfigurovaných clusterech s podporou převzetí služeb při selhání můžete použít také nástroj pro diagnostiku clusteru.

## <a name="specific-validation-scenarios"></a>Konkrétní scénáře ověřování
V této části jsou popsány scénáře, ve kterých je ověřování také nutné nebo užitečné.

- **Ověření před konfigurací clusteru:**
  - **Sada serverů připravená na zprovoznění clusteru s podporou převzetí služeb při selhání:** Toto je nejjednodušší scénář ověřování. Hardwarové součásti (systémy, sítě a úložiště) jsou připojené, ale systémy ještě nefungují jako cluster. Spuštění testů v této situaci nemá žádný vliv na dostupnost.
 
  - **Virtuální počítače serveru:** U virtualizovaných serverů v clusteru spusťte ověřování clusteru stejně jako v jakémkoli jiném novém clusteru. Požadavek na spuštění této funkce je stejný, ať už máte:
    - Hostitelský cluster, ve kterém dochází k převzetí služeb při selhání mezi dvěma fyzickými počítači.
    - "Hostující cluster", ve kterém dochází k převzetí služeb při selhání mezi hostovanými operačními systémy na stejném fyzickém počítači.
 
- **Ověření po nakonfigurování a použití clusteru:**

  - **Před přidáním serveru do clusteru:** Když přidáte server do clusteru, důrazně doporučujeme cluster ověřovat. Při spuštění ověřování clusteru zadejte jak existující členy clusteru, tak i nový server.
  
  - **Při přidávání jednotek:** Když do clusteru přidáte další jednotky, která se liší od nahrazení neúspěšných jednotek nebo vytváření virtuálních disků nebo svazků, které spoléhají na stávající jednotky, spusťte ověření clusteru a ověřte, že nové úložiště bude správně fungovat.

  - **Při provádění změn, které mají vliv na firmware nebo ovladače:** Pokud upgradujete nebo provedete změny v clusteru, které mají vliv na firmware nebo ovladače, je nutné spustit ověření clusteru, abyste potvrdili, že nová kombinace hardwaru, firmwaru, ovladačů a softwaru podporuje funkce clusteru s podporou převzetí služeb při selhání.

  - **Po obnovení systému ze zálohy:** Po obnovení systému ze zálohy spusťte ověřování clusteru a potvrďte, že systém funguje správně jako součást clusteru.

## <a name="validate-networking"></a>Ověřit sítě
Nástroj Microsoft Validate-DCB je navržený tak, aby ověřil konfiguraci přemostění datových Center (DCB) v clusteru. K tomu nástroj přebírá očekávanou konfiguraci jako vstup a pak testuje všechny servery v clusteru. Tato část popisuje, jak nainstalovat a spustit nástroj Validate-DCB, zkontrolovat výsledky a vyřešit chyby sítě, které nástroj identifikuje.

V síti má přímý přístup do paměti vzdáleného počítače (RDMA) přes sblíženou síť Ethernet (RoCE) k zajištění bezeztrátu síťových prostředků infrastruktury technologie DCB. A i když iWARP nevyžaduje DCB, pořád se to doporučuje. Konfigurace DCB ale může být složitá s přesnou konfigurací požadovanou v rámci:
- Každý server v clusteru
- Každý síťový port, který RDMA nachází přes prostředky infrastruktury

### <a name="prerequisites"></a>Požadavky
- Informace o nastavení sítě pro serverový cluster, který chcete ověřit, včetně:
    - Název hostitele nebo serverového clusteru
    - Název virtuálního přepínače
    - Názvy síťových adaptérů
    - Nastavení řízení toku priority (PFC) a vylepšeného výběru přenosů (ETS)
- Připojení k Internetu ke stažení modulu nástrojů v prostředí Windows PowerShell od společnosti Microsoft.

### <a name="install-and-run-the-validate-dcb-tool"></a>Instalace a spuštění nástroje Validate-DCB
Postup instalace a spuštění nástroje Validate-DCB:
1. Na počítači pro správu otevřete relaci prostředí Windows PowerShell jako správce a pak použijte následující příkaz pro instalaci nástroje.

    ```powershell
    Install-Module Validate-DCB
    ```

1. Přijměte žádosti o použití poskytovatele NuGet a přístup k úložišti pro instalaci nástroje.
1. Po připojení PowerShellu k síti Microsoftu ke stažení nástroje zadejte `Validate-DCB` a stisknutím klávesy **ENTER** spusťte Průvodce nástrojem.

    > [!NOTE]
    > Pokud nemůžete spustit skript nástroje Validate-DCB, možná budete muset upravit zásady spouštění prostředí PowerShell. Pomocí rutiny Get-ExecutionPolicy můžete zobrazit aktuální nastavení zásad spouštění skriptů. Informace o nastavení zásad spouštění v PowerShellu najdete v tématu [o zásadách spouštění](/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-7).

1. Na stránce Vítejte v Průvodci konfigurací ověření-DCB vyberte **Další**.
1. Na stránce clustery a uzly zadejte název serverového clusteru, který chcete ověřit, vyberte možnost **vyřešit** , aby se seznam na stránce vybral, a pak vyberte **Další**.

    :::image type="content" source="../media/validate/clusters-and-nodes.png" alt-text="Stránka clustery a uzly Průvodce konfigurací ověření – DCB":::

1. Na stránce adaptéry:
   1. Zaškrtněte políčko **připojeno k virtuálnímu přepínači** a zadejte název virtuálního přepínače.
   1. V části **název adaptéru**zadejte název každé fyzické síťové karty, v části **název hostitele vNIC název**, název každé virtuální síťové karty (vNIC) a v části **síť VLAN**se pro každý adaptér používá ID sítě VLAN.
   1. Rozbalte rozevírací seznam **typ RDMA** a vyberte odpovídající protokol: **roce** nebo **iWARP**. Nastavte také **rámce typu Jumbo** na odpovídající hodnotu pro vaši síť a potom vyberte možnost **Další**.

    :::image type="content" source="../media/validate/adapters.png" alt-text="Stránka clustery a uzly Průvodce konfigurací ověření – DCB" lightbox="../media/validate/adapters.png":::

    > [!NOTE]
    > - Další informace o tom, jak SR-IOV vylepšuje výkon sítě, najdete v tématu [Přehled rozhraní SR-IOV (single-root I/O Virtualization)](/windows-hardware/drivers/network/overview-of-single-root-i-o-virtualization--sr-iov-).

1. Na stránce přemostění datového centra upravte hodnoty tak, aby odpovídaly nastavení vaší organizace **priority**, **názvu zásad**a **rezervované šířky pásma**, a pak vyberte **Další**.

    :::image type="content" source="../media/validate/data-center-bridging.png" alt-text="Stránka clustery a uzly Průvodce konfigurací ověření – DCB" lightbox="../media/validate/data-center-bridging.png":::

    > [!NOTE]
    > Výběr možnosti RDMA přes RoCE na předchozí stránce průvodce vyžaduje DCB pro spolehlivost sítě na všech síťových rozhraních a switchports.

1. Na stránce Uložit a nasadit v poli cesta ke **konfiguračnímu souboru** uložte konfigurační soubor s příponou. ps1 do umístění, kde ho můžete později použít, a pokud je to potřeba, pak vyberte **exportovat** , aby se SPUSTIL nástroj Validate-DCB.

   - Konfigurační soubor můžete volitelně nasadit tak, že na stránce vyplníte část **nasadit konfiguraci do uzlů** , která zahrnuje možnost použít účet Azure Automation k nasazení konfigurace a pak ho ověřit. Pokud chcete začít pracovat s Azure Automation, přečtěte si téma [Vytvoření účtu Azure Automation](/azure/automation/automation-quickstart-create-account) .

    :::image type="content" source="../media/validate/save-and-deploy.png" alt-text="Stránka clustery a uzly Průvodce konfigurací ověření – DCB":::

### <a name="review-results-and-fix-errors"></a>Kontrola výsledků a oprava chyb
Nástroj Validate-DCB vytvoří výsledky ve dvou jednotkách:
1. [Globální jednotka] výsledky seznamu požadavků a požadavků pro spuštění modálních testů.
1. [Modální jednotka] výsledky poskytují zpětnou vazbu ke každé konfiguraci hostitele clusteru a osvědčeným postupům.

Tento příklad ukazuje úspěšné výsledky kontroly jednoho serveru pro všechny požadavky a modální testy jednotek tím, že označuje neúspěšný počet 0.

:::image type="content" source="../media/validate/global-unit-and-modal-unit-results.png" alt-text="Stránka clustery a uzly Průvodce konfigurací ověření – DCB":::

Následující kroky ukazují, jak identifikovat chybu paketů typu Jumbo z vNIC SMB02 a opravit ji:
1. Výsledky prověřování nástroje Validate-DCB zobrazují chybu neúspěšného počtu 1.

    :::image type="content" source="../media/validate/failed-count-error-1.png" alt-text="Stránka clustery a uzly Průvodce konfigurací ověření – DCB":::

1. Když se posunete zpátky pomocí výsledků, zobrazí se červená chyba s informacemi o tom, že paket typu Jumbo pro vNIC SMB02 na hostiteli S046036 je nastavený na výchozí velikost 1514, ale měla by být nastavená na 9014.

    :::image type="content" source="../media/validate/jumbo-packet-setting-error.png" alt-text="Stránka clustery a uzly Průvodce konfigurací ověření – DCB":::

1. Kontrola **upřesňujících** vlastností vNIC SMB02 na hostiteli S046036 ukazuje, že paket typu Jumbo je nastaven na výchozí hodnotu **disabled**.

    :::image type="content" source="../media/validate/hyper-v-advanced-properties-jumbo-packet-setting.png" alt-text="Stránka clustery a uzly Průvodce konfigurací ověření – DCB":::

1. Oprava této chyby vyžaduje povolení funkce paketu typu Jumbo a změnu její velikosti na 9014 bajtů. Opětovné spuštění kontroly na hostiteli S046036 potvrdí tuto změnu vrácením neúspěšného počtu 0.

    :::image type="content" source="../media/validate/jumbo-packet-error-fix-confirmation.png" alt-text="Stránka clustery a uzly Průvodce konfigurací ověření – DCB":::

Další informace o řešení chyb, které nástroj Validate-DCB identifikuje, najdete v následujícím videu.

> [!VIDEO https://www.youtube.com/embed/cC1uACvhPBs]

## <a name="validate-the-cluster"></a>Ověřit cluster
Pomocí následujících kroků ověříte servery v existujícím clusteru v centru pro správu systému Windows.

1. V centru pro správu systému Windows v části **všechna připojení**vyberte cluster s Azure Stack HCI, který chcete ověřit, a pak vyberte **připojit**.

    **Řídicí panel Správce clusteru** zobrazuje přehled informací o clusteru.

1. Na **řídicím panelu Správce clusterů**v části **nástroje**vyberte **servery**.
1. Na stránce **inventarizace** vyberte servery v clusteru, potom rozbalte podnabídku **Další** a vyberte **ověřit cluster**.
1. V automaticky otevíraném okně **ověřit cluster** vyberte **Ano**.

    :::image type="content" source="../media/validate/validate-cluster-pop-up.png" alt-text="Stránka clustery a uzly Průvodce konfigurací ověření – DCB":::

1. V místním okně **zprostředkovatel CredSSP (Credential Security Service Provider)** vyberte **Ano**.
1. Zadejte přihlašovací údaje pro povolení **zprostředkovatele CredSSP** a pak vyberte **pokračovat**.<br> Ověření clusteru běží na pozadí a po jeho dokončení vám zobrazí oznámení, jak je popsáno v následující části.

> [!NOTE]
> Po ověření clusterových serverů bude nutné z bezpečnostních důvodů zakázat zprostředkovatele CredSSP.

### <a name="disable-credssp"></a>Zakázat CredSSP
Po úspěšném ověření vašeho serverového clusteru budete muset pro účely zabezpečení zakázat protokol CredSSP (Security Support Provider) na každém serveru. Další informace najdete v článku [CVE-2018-0886](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-0886).

1. V centru pro správu systému Windows v části **všechna připojení**vyberte první server v clusteru a pak vyberte **připojit**.
1. Na stránce **Přehled** vyberte **Zakázat CredSSP**a potom v místním okně **vypnout zprostředkovatele CredSSP** vyberte **Ano**.

    Výsledkem kroku 2 je, že se v horní části stránky s **přehledem** na serveru zobrazí obrázek s červenou příponou **CredSSP** a na ostatních serverech se zakáže CredSSP.

### <a name="view-validation-reports"></a>Zobrazení sestav ověření
Teď jste připraveni zobrazit sestavu ověření clusteru.

Existuje několik způsobů, jak získat přístup k sestavám ověřování:
- Na stránce **inventarizace** rozbalte podnabídku **Další** a potom vyberte možnost **Zobrazit sestavy ověření**.


- V pravém horním rohu **centra pro správu systému Windows**vyberte ikonu zvonku **oznámení** , aby se zobrazilo podokno **oznámení** .
Vyberte **úspěšně ověřené upozornění clusteru** a pak vyberte **Přejít na sestavu ověření clusteru s podporou převzetí služeb při selhání**.

> [!NOTE]
> Dokončení procesu ověření clusteru serveru může nějakou dobu trvat. V centru pro správu Windows se během procesu přepněte na jiný nástroj. V podokně **oznámení** indikuje stavový řádek pod **ověřením oznámení clusteru** , kdy je postup dokončen.

## <a name="validate-the-cluster-using-powershell"></a>Ověření clusteru pomocí prostředí PowerShell
Prostředí Windows PowerShell můžete použít také ke spuštění ověřovacích testů na serverovém clusteru a zobrazení výsledků. Testy můžete spustit před i po nastavení clusteru.

Pokud chcete spustit ověřovací test na serverovém clusteru, vydejte rutiny PowerShellu **Get-cluster** a **Test-Cluster** <server clustername> z počítače pro správu nebo spusťte pouze rutinu **Test-Cluster** přímo v clusteru:

```PowerShell
$Cluster = Get-Cluster -Name 'server-cluster1'
Test-Cluster -InputObject $Cluster -Verbose
```

Další příklady a informace o použití naleznete v referenční dokumentaci k [testovacímu clusteru](/powershell/module/failoverclusters/test-cluster?view=win10-ps) .

## <a name="validate-replication-for-storage-replica"></a>Ověřit replikaci pro repliku úložiště
Pokud k replikaci svazků v roztaženém clusteru nebo clusteru mezi clustery používáte repliku úložiště, můžete získat stav replikace několika událostmi a rutinami, které můžete použít k získání stavu replikace. 

V následujícím scénáři jsme nakonfigurovali repliku úložiště vytvořením replikačních skupin (RGs) pro dvě lokality a pak zadáte datové svazky a svazky protokolů pro uzly zdrojového serveru v Site1 (Server1, Server2) a cílové (replikované) uzly serveru v site2 (Server3, v Server4).

Pokud chcete určit průběh replikace pro Server1 v Site1, spusťte příkaz GET-WinEvent a prověřte události 5015, 5002, 5004, 1237, 5001 a 2200:

```powershell
Get-WinEvent -ComputerName Server1 -ProviderName Microsoft-Windows-StorageReplica -max 20
```

Pro Server3 v site2 spusťte následující příkaz, který `Get-WinEvent` zobrazí události repliky úložiště, které ukazují vytvoření partnerství. Tato událost zobrazuje počet zkopírovaných bajtů a čas, který to zabralo. Například:

```powershell
Get-WinEvent -ComputerName Server3 -ProviderName Microsoft-Windows-StorageReplica | Where-Object {$_.ID -eq "1215"} | FL
```

Pro Server3 v site2 spusťte `Get-WinEvent` příkaz a prověřte události 5009, 1237, 5001, 5015, 5005 a 2200, abyste pochopili průběh zpracování. V této sekvenci by neměla být žádná upozornění ani chyby. Bude to znamenat spoustu 1237 událostí – tyto události znamenají průběh.

```powershell
Get-WinEvent -ComputerName Server3 -ProviderName Microsoft-Windows-StorageReplica | FL
```

Alternativně je cílová skupina serverů pro replika ve všech časech počet bajtů, které zbývá zkopírovat, a může být dotazován prostřednictvím PowerShellu pomocí `Get-SRGroup` . Například:

```powershell
(Get-SRGroup).Replicas | Select-Object numofbytesremaining
```

Pro Server3 Node v site2 spusťte následující příkaz a prověřte události 5009, 1237, 5001, 5015, 5005 a 2200, abyste pochopili průběh replikace. Nemusíte mít žádná upozornění na chyby. Ale bude to mnoho "1237" událostí – tyto události jednoduše naznačují průběh.

```powershell
Get-WinEvent -ComputerName Server3 -ProviderName Microsoft-Windows-StorageReplica | FL
```

Jako skript průběhu, který se neukončí:

```powershell
while($true) {
$v = (Get-SRGroup -Name "Replication2").replicas | Select-Object numofbytesremaining
[System.Console]::Write("Number of bytes remaining: {0}`r", $v.numofbytesremaining)
Start-Sleep -s 5
}
```

K získání stavu replikace v roztaženém clusteru použijte `Get-SRGroup` a `Get-SRPartnership` :

```powershell
Get-SRGroup -Cluster ClusterS1
```

```powershell
Get-SRPartnership -Cluster ClusterS1
```

```powershell
(Get-SRGroup).replicas -Cluster ClusterS1
```

Po potvrzení úspěšné replikace dat mezi lokalitami můžete vytvořit virtuální počítače a další úlohy.

## <a name="see-also"></a>Viz také:
- Testování výkonu na základě syntetických úloh v nově vytvořeném prostoru úložiště pomocí DiskSpd.exe. Další informace najdete v tématu [testování výkonu prostorů úložiště pomocí syntetických úloh ve Windows serveru](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn894707(v=ws.11)).
- Windows Server Assessment je služba Premier, která je dostupná pro zákazníky, kteří chtějí, aby Microsoft zkontroloval instalaci Windows serveru 2019. Další informace získáte od společnosti Microsoft Premier Support. Další informace najdete v tématu [Začínáme s vyhodnocením na vyžádání Windows serveru (Server, zabezpečení, Hyper-V, cluster s podporou převzetí služeb při selhání, IIS)](/services-hub/health/getting-started-windows-server).
