---
title: Funkce virtuálního počítače centra Azure Stack
description: Přečtěte si o různých funkcích a ohledech při práci s virtuálními počítači v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 10/09/2019
ms.openlocfilehash: 2fbdc058781b4aefbcf4a289e907bcbb4b63f301
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546986"
---
# <a name="azure-stack-hub-vm-features"></a>Funkce virtuálního počítače centra Azure Stack

Virtuální počítače centra Azure Stack poskytují škálovatelné výpočetní prostředky na vyžádání. Než nasadíte virtuální počítače, měli byste se seznámit s rozdíly mezi funkcemi virtuálních počítačů, které jsou dostupné v Azure Stack hub a Microsoft Azure. Tento článek popisuje tyto rozdíly a identifikuje klíčové důležité důvody pro plánování nasazení virtuálních počítačů. Další informace o hlavních rozdílech mezi Azure Stack hub a Azure najdete v článku [klíčové důležité informace](azure-stack-considerations.md) .

## <a name="vm-differences"></a>Rozdíly virtuálních počítačů

| Příznak | Azure (Global) | Azure Stack Hub |
| --- | --- | --- |
| Image virtuálních počítačů | Azure Marketplace obsahuje obrázky, které můžete použít k vytvoření virtuálního počítače. Pokud chcete zobrazit seznam imagí, které jsou k dispozici v Azure Marketplace, zobrazte stránku [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) . | Ve výchozím nastavení nejsou k dispozici žádné image v tržišti Azure Stack hub. Aby ho uživatelé mohli používat, musí správce cloudu Azure Stack publikovat nebo stahovat image na webu Centrum Azure Stack. |
| Generování VHD | Generace dvou virtuálních počítačů podporuje klíčové funkce, které nejsou podporované v generaci jednoho virtuálního počítače. Mezi tyto funkce patří zvýšené množství paměti, rozšíření Intel software Guard (Intel SGX) a virtualizovaná trvalá paměť (vPMEM). Generace dvou virtuálních počítačů spuštěných v místním prostředí obsahuje některé funkce, které ještě nejsou v Azure podporované. Další informace najdete v tématu [Podpora virtuálních počítačů 2. generace v Azure](/azure/virtual-machines/windows/generation-2) .  | Centrum Azure Stack podporuje jenom jednu generaci virtuálních počítačů. Generaci jednoho virtuálního počítače můžete převést z VHDX na formát souboru VHD a z dynamického rozšiřování na disk s pevnou velikostí. Nemůžete změnit generaci virtuálního počítače. Další informace najdete v tématu [Podpora pro virtuální počítače 2. generace v Azure](/azure/virtual-machines/windows/generation-2). |
| Velikosti virtuálních počítačů | Azure podporuje širokou škálu velikostí virtuálních počítačů. Další informace o dostupných velikostech a možnostech najdete v tématu [velikosti virtuálních počítačů Azure](/azure/virtual-machines/sizes). | Centrum Azure Stack podporuje podmnožinu velikostí virtuálních počítačů, které jsou k dispozici v Azure. Pokud chcete zobrazit seznam podporovaných velikostí, přečtěte si část [velikosti virtuálních počítačů](#vm-sizes) v tomto článku. |
| Kvóty virtuálních počítačů | [Omezení kvót](/azure/azure-resource-manager/management/azure-subscription-service-limits#managing-limits) nastavuje Microsoft. | Správce cloudu Azure Stackového centra musí před tím, než nabídne virtuální počítač svým uživatelům, přiřadit kvóty. |
| Rozšíření virtuálních počítačů |Azure podporuje širokou škálu rozšíření virtuálních počítačů. Další informace o dostupných rozšířeních najdete v článku o [rozšířeních virtuálních počítačů a funkcích](/azure/virtual-machines/windows/extensions-features) .| Centrum Azure Stack podporuje podmnožinu rozšíření, která jsou k dispozici v Azure, přičemž každé rozšíření má konkrétní verze. Správce cloudu centra Azure Stack může zvolit, která rozšíření budou pro své uživatele k dispozici. Pokud chcete zobrazit seznam podporovaných rozšíření, přečtěte si část [rozšíření virtuálních počítačů](#vm-extensions) v tomto článku. |
| Síť virtuálního počítače | Veřejné IP adresy přiřazené k virtuálnímu počítači tenanta jsou přístupné přes Internet.<br><br><br>Virtuální počítače Azure mají pevný název DNS. | Veřejné IP adresy přiřazené k virtuálnímu počítači tenanta jsou přístupné jenom v prostředí Azure Stack Development Kit. Uživatel musí mít přístup k Azure Stack Development Kit přes [RDP](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) nebo [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) , aby se mohl připojit k virtuálnímu počítači vytvořenému v centru Azure Stack.<br><br>Virtuální počítače vytvořené v rámci konkrétní instance centra Azure Stack mají název DNS na základě hodnoty, která je nakonfigurovaná správcem cloudu. |
| Úložiště virtuálního počítače | Podporuje [spravované disky.](/azure/virtual-machines/windows/managed-disks-overview) | Spravované disky jsou podporované v Azure Stackovém centru s verzí 1808 a novější. |
| Výkon disku virtuálního počítače | Závisí na typu a velikosti disku. | Závisí na velikosti virtuálního počítače, ke kterému jsou disky připojené. Další informace najdete v článku o [velikostech virtuálních počítačů podporovaných v článku centra Azure Stack](azure-stack-vm-sizes.md) .
| Verze rozhraní API | Azure má vždycky nejnovější verze rozhraní API pro všechny funkce virtuálních počítačů. | Centrum Azure Stack podporuje pro tyto služby specifické služby Azure a konkrétní verze rozhraní API. Pokud chcete zobrazit seznam podporovaných verzí rozhraní API, přečtěte si část [verze rozhraní API](#api-versions) tohoto článku. |
| Azure Instance Metadata Service | Azure Instance Metadata Service poskytuje informace o spuštěných instancích virtuálních počítačů, které se dají použít ke správě a nastavení virtuálního počítače.  | Azure Instance Metadata Service se v centru Azure Stack nepodporuje. |
| Skupiny dostupnosti virtuálních počítačů|Více domén selhání (2 nebo 3 podle oblasti).<br>Více aktualizačních domén.|Více domén selhání (2 nebo 3 podle oblasti).<br>Jediná aktualizační doména s migrací za provozu k ochraně úloh během aktualizace. 20 aktualizačních domén podporovaných pro kompatibilitu šablon.<br>Skupina virtuálních počítačů a dostupnosti by měla být ve stejném umístění a skupině prostředků.|
| Škálovací sady virtuálních počítačů|Automatické škálování je podporováno.|Automatické škálování se nepodporuje.<br><br>Přidejte další instance do sady škálování pomocí portálu, Správce prostředků šablon nebo PowerShellu. |
| Disk s kopií cloudu | Vyberte koncové body z vlastností účtu úložiště, které jsou k dispozici v Azure Stack hub. | [Určující Cloud](/windows-server/failover-clustering/deploy-cloud-witness) je typ určujícího disku kvora clusteru s podporou převzetí služeb při selhání, který používá Microsoft Azure k poskytnutí hlasu kvora clusteru.<br>Koncové body v globálním Azure v porovnání s Azure Stack hub můžou vypadat takto:<br>Pro globální Azure:<br>`https://mywitness.blob.core.windows.net/`<br>Pro centrum Azure Stack:<br>`https://mywitness.blob.<region>.<FQDN>/`|
| Diagnostika virtuálního počítače | Diagnostika virtuálního počítače se systémem Linux je podporována. | Diagnostika virtuálního počítače se systémem Linux není v centru Azure Stack podporována. Když nasadíte virtuální počítač se systémem Linux s povoleným diagnostikou virtuálních počítačů, nasazení se nezdařilo. Nasazení se také nepovede, pokud povolíte základní metriky virtuálního počítače se systémem Linux prostřednictvím nastavení diagnostiky. |
| Vnořené velikosti virtuálních počítačů virtualizace | Podporováno | Nepodporováno |

## <a name="vm-sizes"></a>Velikost virtuálních počítačů

Rozbočovač Azure Stack ukládá omezení prostředků, aby nedocházelo k využití prostředků (místní server a úroveň služeb). Tato omezení zlepšují prostředí tenanta tím, že snižují vliv spotřeby prostředků na ostatní klienty.

- U odchozích síťových přenosů z virtuálního počítače je na místě zakončení šířky pásma. Velká písmena v Azure Stackovém centru jsou stejná jako u velkých písmen v Azure.
- U prostředků úložiště Azure Stack hub implementuje omezení úložiště IOPS (vstupně-výstupní operace za sekundu), aby se předešlo základní přečerpání prostředků podle klientů pro použití úložiště.
- Pro disky virtuálních počítačů je IOPS disku na rozbočovači Azure Stack funkce velikosti virtuálního počítače místo typu disku. To znamená, že pro virtuální počítač s Standard_Fs Series bez ohledu na to, jestli pro daný typ disku zvolíte SSD nebo HDD, je limit IOPS pro druhý datový disk 2300 IOPS.
- Dočasné disky připojené k virtuálnímu počítači nejsou trvalé a je možné je ztratit na operacích řídicích roviny, jako je změna velikosti nebo zastavení přidělení.

V následující tabulce jsou uvedené virtuální počítače, které jsou podporované v Azure Stackovém centru spolu s jejich konfigurací:

| Typ            | Velikost          | Rozsah podporovaných velikostí |
| ----------------| ------------- | ------------------------ |
|Obecné účely  |Basic A        |[A0 – A4](azure-stack-vm-sizes.md#basic-a)                   |
|Obecné účely  |Standardní A     |[A0 – A7](azure-stack-vm-sizes.md#standard-a)              |
|Obecné účely  |Av2-series     |[A1_v2 – A8m_v2](azure-stack-vm-sizes.md#av2-series)     |
|Obecné účely  |D-series       |[D1 – D4](azure-stack-vm-sizes.md#d-series)              |
|Obecné účely  |Dv2-series     |[D1_v2 – D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Obecné účely  |DS-series      |[DS1 – DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Obecné účely  |DSv2-series    |[DS1_v2 – DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Optimalizované z hlediska paměti |D-series       |[D11 – D14](azure-stack-vm-sizes.md#mo-d)            |
|Optimalizované z hlediska paměti |DS-series      |[DS11 – DS14](azure-stack-vm-sizes.md#mo-ds)|
|Optimalizované z hlediska paměti |Dv2-series     |[D11_v2 – DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Optimalizované z hlediska paměti |DSv2-series    |[DS11_v2 – DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |
|Optimalizované z hlediska výpočetních služeb|F-series       |[F1 – F16 ÚROVNĚ](azure-stack-vm-sizes.md#f-series)    |
|Optimalizované z hlediska výpočetních služeb|Řada FS      |[F1s úrovně – F16s úrovně](azure-stack-vm-sizes.md#fs-series)    |
|Optimalizované z hlediska výpočetních služeb|Řada Fsv2    |[F2s_v2 – F64s_v2](azure-stack-vm-sizes.md#fsv2-series)    |

Velikosti virtuálních počítačů a jejich přidružených prostředků jsou konzistentní mezi Azure Stackm rozbočovačem a Azure. Tato konzistence zahrnuje množství paměti, počet jader a počet a velikost datových disků, které lze vytvořit. Výkon virtuálních počítačů se stejnou velikostí ale závisí na základních charakteristikách konkrétního prostředí centra Azure Stack.

## <a name="vm-extensions"></a>Rozšíření virtuálních počítačů

Centrum Azure Stack zahrnuje malou sadu rozšíření. Aktualizace a další rozšíření jsou k dispozici prostřednictvím syndikace webu Marketplace.

Pomocí následujícího skriptu PowerShellu Získejte seznam rozšíření virtuálních počítačů, které jsou k dispozici v prostředí Azure Stack hub:

```powershell
Get-AzVmImagePublisher -Location local | `
  Get-AzVMExtensionImageType | `
  Get-AzVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

Pokud zřízení rozšíření na nasazení virtuálního počítače trvá moc dlouho, nechte časový limit zřizování místo toho, abyste se pokoušeli zastavit proces navrácení nebo odstranění virtuálního počítače.

## <a name="api-versions"></a>Verze rozhraní API

Funkce virtuálních počítačů v centru Azure Stack podporují následující verze API:

"2017-12-01", "2017-03-30", "2016-03-30", "2015-06-15"

Pomocí následujícího skriptu PowerShellu získáte verze rozhraní API pro funkce virtuálních počítačů, které jsou k dispozici ve vašem prostředí Azure Stack hub:

```powershell
Get-AzResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```

Seznam podporovaných typů prostředků a verzí rozhraní API se může lišit, pokud operátor cloudu aktualizuje vaše prostředí Azure Stack hub na novější verzi.

## <a name="windows-activation"></a>Aktivace Windows

Produkty systému Windows musí být používány v souladu s právy k používání produktů a licenčními podmínkami společnosti Microsoft. Azure Stack hub používá k aktivaci virtuálních počítačů s Windows serverem [automatickou aktivaci virtuálního počítače](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn303421(v%3dws.11)) (AVMA).

- Hostitel centra Azure Stack aktivuje Windows pomocí klíčů AVMA pro Windows Server 2016. Všechny virtuální počítače, na kterých běží Windows Server 2012 R2 nebo novější, se automaticky aktivují.
- Virtuální počítače s Windows Serverem 2012 nebo starším se neaktivují automaticky a musí se aktivovat pomocí aktivace pomocí kódu [MAK](/previous-versions/tn-archive/ff793438(v=technet.10)). Pokud chcete použít aktivaci pomocí klíče k vícenásobné aktivaci, musíte zadat vlastní kód Product Key.

Microsoft Azure používá k aktivaci virtuálních počítačů s Windows aktivaci pomocí služby správy klíčů. Pokud přesunete virtuální počítač z centra Azure Stack do Azure a dojde k problémům s aktivací, přečtěte si téma řešení potíží s [aktivací virtuálních počítačů Azure s Windows](/azure/virtual-machines/windows/troubleshoot-activation-problems). Další informace najdete v příspěvku na blogu týmu podpory pro Azure na virtuálních počítačích Azure, které se [týkají potíží s aktivací Windows](/archive/blogs/mast/troubleshooting-windows-activation-failures-on-azure-vms) .

## <a name="high-availability"></a>Vysoká dostupnost

Váš virtuální počítač může být vystavený restartováním z důvodu plánované údržby, která je naplánována operátorem centra Azure Stack. Pro zajištění vysoké dostupnosti produkčního systému více virtuálních počítačů v Azure jsou virtuální počítače umístěné ve [skupině dostupnosti](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) , která je rozšíří napříč více doménami selhání a aktualizačními doménami. V menším měřítku centra Azure Stack se doména selhání ve skupině dostupnosti definuje jako jeden uzel v jednotce škálování.  

I když je infrastruktura centra Azure Stack už odolná vůči selháním, pak základní technologie (Clustering s podporou převzetí služeb při selhání) stále způsobí nějaké výpadky virtuálních počítačů na ovlivněném fyzickém serveru, pokud dojde k selhání hardwaru. Centrum Azure Stack podporuje skupinu dostupnosti s maximálním počtem tří domén selhání pro zajištění konzistence s Azure.

|                   |             |
|-------------------|-------------|
| **Domény selhání** | Virtuální počítače, které jsou umístěné ve skupině dostupnosti, se fyzicky izolují tak, že je rozšíříte tak, jak je to možné, do více domén selhání (Azure Stack uzly centra). Pokud dojde k selhání hardwaru, virtuální počítače z neúspěšné domény selhání se restartují v jiných doménách selhání. Budou se uchovávat v samostatných doménách selhání z ostatních virtuálních počítačů, ale ve stejné skupině dostupnosti, pokud je to možné. Když se hardware vrátí zpátky do online režimu, virtuální počítače se znovu vyrovnávají, aby se zachovala vysoká dostupnost. |
| **Aktualizační domény**| Aktualizační domény představují jiný způsob, jakým Azure poskytuje vysokou dostupnost ve skupinách dostupnosti. Aktualizační doména je logická skupina základního hardwaru, která může prostoupit v rámci údržby. Virtuální počítače umístěné ve stejné aktualizační doméně se při plánované údržbě restartují společně. Když klienti vytvářejí virtuální počítače v rámci skupiny dostupnosti, platforma Azure automaticky distribuuje virtuální počítače napříč těmito aktualizačními doménami. <br>V Azure Stackovém centru jsou virtuální počítače za provozu přenášeny v jiných online hostitelích v clusteru před tím, než se aktualizuje jejich podkladový hostitel. Vzhledem k tomu, že během aktualizace hostitele nedochází k výpadku tenanta, funkce aktualizační doména v centru Azure Stack existuje pouze pro kompatibilitu šablon s Azure. Virtuální počítače ve skupině dostupnosti budou na portálu zobrazeny jako číslo aktualizační domény. |

## <a name="next-steps"></a>Další kroky

[Vytvoření virtuálního počítače s Windows pomocí PowerShellu v centru Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)
