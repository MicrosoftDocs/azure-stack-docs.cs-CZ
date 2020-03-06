---
title: Funkce virtuálního počítače centra Azure Stack
description: Přečtěte si o různých funkcích a ohledech při práci s virtuálními počítači v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 2/3/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 10/09/2019
ms.openlocfilehash: 611fec639fbcec478b79d44975b24f2d806df5bc
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364798"
---
# <a name="azure-stack-hub-vm-features"></a>Funkce virtuálního počítače centra Azure Stack

Virtuální počítače centra Azure Stack poskytují škálovatelné výpočetní prostředky na vyžádání. Než nasadíte virtuální počítače, měli byste se seznámit s rozdíly mezi funkcemi virtuálních počítačů, které jsou dostupné v Azure Stack hub a Microsoft Azure. Tento článek popisuje tyto rozdíly a identifikuje klíčové důležité důvody pro plánování nasazení virtuálních počítačů. Další informace o hlavních rozdílech mezi Azure Stack hub a Azure najdete v článku [klíčové důležité informace](azure-stack-considerations.md) .

## <a name="vm-differences"></a>Rozdíly virtuálních počítačů

| Funkce | Azure (Global) | Centrum Azure Stack |
| --- | --- | --- |
| Image virtuálních počítačů | Azure Marketplace obsahuje obrázky, které můžete použít k vytvoření virtuálního počítače. Pokud chcete zobrazit seznam imagí, které jsou k dispozici v Azure Marketplace, zobrazte stránku [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) . | Ve výchozím nastavení nejsou k dispozici žádné image v tržišti Azure Stack hub. Aby ho uživatelé mohli používat, musí správce cloudu Azure Stack publikovat nebo stahovat image na webu Centrum Azure Stack. |
| Generování VHD | Generace dvou virtuálních počítačů podporuje klíčové funkce, které nejsou podporované v generaci jednoho virtuálního počítače. Mezi tyto funkce patří zvýšené množství paměti, rozšíření Intel software Guard (Intel SGX) a virtualizovaná trvalá paměť (vPMEM). Generace dvou virtuálních počítačů spuštěných v místním prostředí obsahuje některé funkce, které ještě nejsou v Azure podporované. Další informace najdete v tématu [Podpora virtuálních počítačů 2. generace v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) .  | Centrum Azure Stack podporuje jenom jednu generaci virtuálních počítačů. Generaci jednoho virtuálního počítače můžete převést z VHDX na formát souboru VHD a z dynamického rozšiřování na disk s pevnou velikostí. Nemůžete změnit generaci virtuálního počítače. Další informace najdete v tématu [Podpora pro virtuální počítače 2. generace v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2). |
| Velikosti virtuálních počítačů | Azure podporuje širokou škálu velikostí virtuálních počítačů. Další informace o dostupných velikostech a možnostech najdete v tématech velikosti virtuálních počítačů s [Windows](/azure/virtual-machines/virtual-machines-windows-sizes) a [velikosti virtuálních počítačů Linux](/azure/virtual-machines/linux/sizes) . | Centrum Azure Stack podporuje podmnožinu velikostí virtuálních počítačů, které jsou k dispozici v Azure. Pokud chcete zobrazit seznam podporovaných velikostí, přečtěte si část [velikosti virtuálních počítačů](#vm-sizes) v tomto článku. |
| Kvóty virtuálních počítačů | [Omezení kvót](/azure/azure-resource-manager/management/azure-subscription-service-limits#managing-limits) nastavuje Microsoft. | Správce cloudu Azure Stackového centra musí před tím, než nabídne virtuální počítač svým uživatelům, přiřadit kvóty. |
| Rozšíření virtuálních počítačů |Azure podporuje širokou škálu rozšíření virtuálních počítačů. Další informace o dostupných rozšířeních najdete v článku o [rozšířeních virtuálních počítačů a funkcích](/azure/virtual-machines/windows/extensions-features) .| Centrum Azure Stack podporuje podmnožinu rozšíření, která jsou k dispozici v Azure, přičemž každé rozšíření má konkrétní verze. Správce cloudu centra Azure Stack může zvolit, která rozšíření budou pro své uživatele k dispozici. Pokud chcete zobrazit seznam podporovaných rozšíření, přečtěte si část [rozšíření virtuálních počítačů](#vm-extensions) v tomto článku. |
| Síť virtuálního počítače | Veřejné IP adresy přiřazené k virtuálnímu počítači tenanta jsou přístupné přes Internet.<br><br><br>Virtuální počítače Azure mají pevný název DNS. | Veřejné IP adresy přiřazené k virtuálnímu počítači tenanta jsou přístupné jenom v prostředí Azure Stack Development Kit. Uživatel musí mít přístup k Azure Stack Development Kit přes [RDP](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) nebo [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) , aby se mohl připojit k virtuálnímu počítači vytvořenému v centru Azure Stack.<br><br>Virtuální počítače vytvořené v rámci konkrétní instance centra Azure Stack mají název DNS na základě hodnoty, která je nakonfigurovaná správcem cloudu. |
| Úložiště virtuálního počítače | Podporuje [spravované disky.](/azure/virtual-machines/windows/managed-disks-overview) | Spravované disky jsou podporované v Azure Stackovém centru s verzí 1808 a novější. |
| Výkon disku virtuálního počítače | Závisí na typu a velikosti disku. | Závisí na velikosti virtuálního počítače, ke kterému jsou disky připojené. Další informace najdete v článku o [velikostech virtuálních počítačů podporovaných v článku centra Azure Stack](azure-stack-vm-sizes.md) .
| Verze rozhraní API | Azure má vždycky nejnovější verze rozhraní API pro všechny funkce virtuálních počítačů. | Centrum Azure Stack podporuje pro tyto služby specifické služby Azure a konkrétní verze rozhraní API. Pokud chcete zobrazit seznam podporovaných verzí rozhraní API, přečtěte si část [verze rozhraní API](#api-versions) tohoto článku. |
| Instance Metadata Service Azure | Azure Instance Metadata Service poskytuje informace o spuštěných instancích virtuálních počítačů, které se dají použít ke správě a nastavení virtuálního počítače.  | Azure Instance Metadata Service se v centru Azure Stack nepodporuje. |
| Skupiny dostupnosti virtuálních počítačů|Více domén selhání (2 nebo 3 podle oblasti).<br>Více aktualizačních domén.|Více domén selhání (2 nebo 3 podle oblasti).<br>Jediná aktualizační doména s migrací za provozu k ochraně úloh během aktualizace. 20 aktualizačních domén podporovaných pro kompatibilitu šablon.<br>Skupina virtuálních počítačů a dostupnosti by měla být ve stejném umístění a skupině prostředků.|
| Škálovací sady virtuálních počítačů|Automatické škálování je podporováno.|Automatické škálování se nepodporuje.<br><br>Přidejte další instance do sady škálování pomocí portálu, Správce prostředků šablon nebo PowerShellu. |
| Disk s kopií cloudu | Vyberte koncové body z vlastností účtu úložiště, které jsou k dispozici v Azure Stack hub. | [Určující Cloud](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) je typ určujícího disku kvora clusteru s podporou převzetí služeb při selhání, který používá Microsoft Azure k poskytnutí hlasu kvora clusteru.<br>Koncové body v globálním Azure v porovnání s Azure Stack hub můžou vypadat takto:<br>Pro globální Azure:<br>`https://mywitness.blob.core.windows.net/`<br>Pro centrum Azure Stack:<br>`https://mywitness.blob.<region>.<FQDN>/`|
| Diagnostika virtuálního počítače | Diagnostika virtuálního počítače se systémem Linux je podporována. | Diagnostika virtuálního počítače se systémem Linux není v centru Azure Stack podporována. Při nasazení virtuálního počítače s Linuxem s povolenou diagnostikou virtuálního počítače, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem prostřednictvím nastavení diagnostiky. |

## <a name="vm-sizes"></a>Velikost virtuálních počítačů

Rozbočovač Azure Stack ukládá omezení prostředků, aby nedocházelo k využití prostředků (místní server a úroveň služeb). Tato omezení zlepšují prostředí tenanta tím, že snižují vliv spotřeby prostředků na ostatní klienty.

- U odchozích síťových přenosů z virtuálního počítače je na místě zakončení šířky pásma. Velká písmena v Azure Stackovém centru jsou stejná jako u velkých písmen v Azure.
- U prostředků úložiště Azure Stack hub implementuje omezení úložiště IOPS (vstupně-výstupní operace za sekundu), aby se předešlo základní přečerpání prostředků podle klientů pro použití úložiště.
- Pro disky virtuálních počítačů je IOPS disku na rozbočovači Azure Stack funkce velikosti virtuálního počítače místo typu disku. To znamená, že pro virtuální počítač s Standard_Fs Series bez ohledu na to, jestli pro daný typ disku zvolíte SSD nebo HDD, je limit IOPS pro druhý datový disk 2300 IOPS.

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
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

Pokud zřízení rozšíření na nasazení virtuálního počítače trvá moc dlouho, nechte časový limit zřizování místo toho, abyste se pokoušeli zastavit proces navrácení nebo odstranění virtuálního počítače.

## <a name="api-versions"></a>Verze rozhraní API

Funkce virtuálních počítačů v centru Azure Stack podporují následující verze API:

"2017-12-01", "2017-03-30", "2016-03-30", "2015-06-15"

Pomocí následujícího skriptu PowerShellu získáte verze rozhraní API pro funkce virtuálních počítačů, které jsou k dispozici ve vašem prostředí Azure Stack hub:

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```

Seznam podporovaných typů prostředků a verzí rozhraní API se může lišit, pokud operátor cloudu aktualizuje vaše prostředí Azure Stack hub na novější verzi.

## <a name="windows-activation"></a>Aktivace Windows

Produkty systému Windows musí být používány v souladu s právy k používání produktů a licenčními podmínkami společnosti Microsoft. Azure Stack hub používá k aktivaci virtuálních počítačů s Windows serverem [automatickou aktivaci virtuálního počítače](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA).

- Hostitel centra Azure Stack aktivuje Windows pomocí klíčů AVMA pro Windows Server 2016. Všechny virtuální počítače, na kterých běží Windows Server 2012 R2 nebo novější, se automaticky aktivují.
- Virtuální počítače s Windows Serverem 2012 nebo starším se neaktivují automaticky a musí se aktivovat pomocí aktivace pomocí kódu [MAK](https://technet.microsoft.com/library/ff793438.aspx). Pokud chcete použít aktivaci pomocí klíče k vícenásobné aktivaci, musíte zadat vlastní kód Product Key.

Microsoft Azure používá k aktivaci virtuálních počítačů s Windows aktivaci pomocí služby správy klíčů. Pokud přesunete virtuální počítač z centra Azure Stack do Azure a dojde k problémům s aktivací, přečtěte si téma řešení potíží s [aktivací virtuálních počítačů Azure s Windows](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems). Další informace najdete v příspěvku na blogu týmu podpory pro Azure na virtuálních počítačích Azure, které se [týkají potíží s aktivací Windows](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) .

## <a name="next-steps"></a>Další kroky

[Vytvoření virtuálního počítače s Windows pomocí PowerShellu v centru Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)
