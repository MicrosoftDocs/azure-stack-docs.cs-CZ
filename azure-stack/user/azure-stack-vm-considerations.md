---
title: Funkce Azure Stack virtuálního počítače | Microsoft Docs
description: Přečtěte si o různých funkcích a ohledech při práci s virtuálními počítači v Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 07/16/2019
ms.openlocfilehash: 09e38de68f740cab50e7a3e0ee8cc7364a9909b9
ms.sourcegitcommit: 4139b507d6da98a086929da48e3b4661b70bc4f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68299434"
---
# <a name="azure-stack-vm-features"></a>Funkce Azure Stack virtuálního počítače

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Virtuální počítače s Azure Stack poskytují škálovatelné výpočetní prostředky na vyžádání. Než nasadíte virtuální počítače, měli byste se seznámit s rozdíly mezi funkcemi virtuálních počítačů, které jsou dostupné v Azure Stack a Microsoft Azure. Tento článek popisuje tyto rozdíly a identifikuje klíčové důležité důvody pro plánování nasazení virtuálních počítačů. Další informace o rozdílech na vysoké úrovni mezi Azure Stack a Azure najdete v článku [klíčové důležité informace](azure-stack-considerations.md) .

## <a name="vm-differences"></a>Rozdíly virtuálních počítačů

| Funkce | Azure (Global) | Azure Stack |
| --- | --- | --- |
| Image virtuálních počítačů | Azure Marketplace obsahuje obrázky, které můžete použít k vytvoření virtuálního počítače. Pokud chcete zobrazit seznam imagí, které jsou k dispozici v Azure Marketplace, zobrazte stránku [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) . | Ve výchozím nastavení nejsou k dispozici žádné obrázky na webu Azure Stack Marketplace. Správce cloudu Azure Stack musí publikovat nebo stahovat image na webu Azure Stack Marketplace, aby je mohli uživatelé používat. |
| Velikosti virtuálních počítačů | Azure podporuje širokou škálu velikostí virtuálních počítačů. Další informace o dostupných velikostech a možnostech najdete v tématech velikosti virtuálních počítačů s [Windows](/azure/virtual-machines/virtual-machines-windows-sizes) a [velikosti virtuálních počítačů Linux](/azure/virtual-machines/linux/sizes) . | Azure Stack podporuje podmnožinu velikostí virtuálních počítačů, které jsou k dispozici v Azure. Pokud chcete zobrazit seznam podporovaných velikostí, přečtěte si část [velikosti virtuálních počítačů](#vm-sizes) v tomto článku. |
| Kvóty virtuálních počítačů | [Omezení kvót](/azure/azure-subscription-service-limits#service-specific-limits) nastavuje Microsoft. | Správce cloudu Azure Stack musí před tím, než nabídne virtuální počítač svým uživatelům, přiřadit kvóty. |
| Rozšíření virtuálních počítačů |Azure podporuje širokou škálu rozšíření virtuálních počítačů. Další informace o dostupných rozšířeních najdete v článku o [rozšířeních virtuálních počítačů a funkcích](/azure/virtual-machines/windows/extensions-features) .| Azure Stack podporuje podmnožinu rozšíření, která jsou k dispozici v Azure, přičemž každé rozšíření má konkrétní verze. Správce cloudu Azure Stack může zvolit, která rozšíření budou uživatelům k dispozici. Pokud chcete zobrazit seznam podporovaných rozšíření, přečtěte si část [rozšíření virtuálních počítačů](#vm-extensions) v tomto článku. |
| Síť virtuálního počítače | Veřejné IP adresy přiřazené k virtuálnímu počítači tenanta jsou přístupné přes Internet.<br><br><br>Virtuální počítače Azure mají pevný název DNS. | Veřejné IP adresy přiřazené k virtuálnímu počítači tenanta jsou přístupné jenom v prostředí Azure Stack Development Kit. Uživatel musí mít přístup k Azure Stack Development Kit přes [RDP](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) nebo [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) , aby se mohl připojit k virtuálnímu počítači vytvořenému v Azure Stack.<br><br>Virtuální počítače vytvořené v rámci konkrétní instance Azure Stack mají název DNS na základě hodnoty, která je nakonfigurovaná správcem cloudu. |
| Úložiště virtuálního počítače | Podporuje [spravované disky.](/azure/virtual-machines/windows/managed-disks-overview) | Spravované disky jsou podporované v Azure Stack s verzí 1808 a novější. |
| Výkon disku virtuálního počítače | Závisí na typu a velikosti disku. | Závisí na velikosti virtuálního počítače, ke kterému jsou disky připojené. Další informace najdete [v článku velikosti virtuálních počítačů podporované v Azure Stack](azure-stack-vm-sizes.md) .
| Verze rozhraní API | Azure má vždycky nejnovější verze rozhraní API pro všechny funkce virtuálních počítačů. | Azure Stack podporuje pro tyto služby specifické služby Azure a konkrétní verze rozhraní API. Pokud chcete zobrazit seznam podporovaných verzí rozhraní API, přečtěte si část [verze rozhraní API](#api-versions) tohoto článku. |
| Instance Metadata Service Azure | Azure Instance Metadata Service poskytuje informace o spuštěných instancích virtuálních počítačů, které se dají použít ke správě a nastavení virtuálního počítače.  | Azure Instance Metadata Service se v Azure Stack nepodporuje. |
| Skupiny dostupnosti virtuálních počítačů|Více domén selhání (2 nebo 3 podle oblasti).<br>Více aktualizačních domén.|Více domén selhání (2 nebo 3 podle oblasti).<br>Více aktualizačních domén (až 20).|
| Škálovací sady virtuálních počítačů|Automatické škálování je podporováno.|Automatické škálování se nepodporuje.<br><br>Přidejte další instance do sady škálování pomocí portálu, Správce prostředků šablon nebo PowerShellu. |
| Disk s kopií cloudu | Vyberte koncové body z vlastností účtu úložiště, které jsou k dispozici v Azure Stack. | [Určující Cloud](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) je typ určujícího disku kvora clusteru s podporou převzetí služeb při selhání, který používá Microsoft Azure k poskytnutí hlasu kvora clusteru.<br>Koncové body v globálním Azure v porovnání s Azure Stack můžou vypadat takto:<br>Pro globální Azure:<br>`https://mywitness.blob.core.windows.net/`<br>Pro Azure Stack:<br>`https://mywitness.blob.<region>.<FQDN>/`|
| Diagnostika virtuálního počítače | Diagnostika virtuálního počítače se systémem Linux je podporována. | Diagnostika virtuálního počítače se systémem Linux není v Azure Stack podporována. Při nasazení virtuálního počítače s Linuxem s povolenou diagnostikou virtuálního počítače, nasazení se nezdaří. Nasazení se také nezdaří, pokud povolíte základní metriky virtuálního počítače s Linuxem prostřednictvím nastavení diagnostiky. |

## <a name="vm-sizes"></a>Velikost virtuálních počítačů

Azure Stack ukládá omezení prostředků, aby nedocházelo k využití prostředků (místní server a úroveň služeb). Tato omezení zlepšují prostředí tenanta tím, že snižují vliv spotřeby prostředků na ostatní klienty.

- U odchozích síťových přenosů z virtuálního počítače je na místě zakončení šířky pásma. Velká písmena v Azure Stack jsou stejná jako u velkých písmen v Azure.
- U prostředků úložiště Azure Stack implementuje omezení pro IOPS úložiště (vstupní/výstupní operace za sekundu), aby se předešlo základní přečerpání prostředků podle tenantů pro použití úložiště.
- Pro disky virtuálních počítačů je IOPS disku na Azure Stack funkcí velikosti virtuálního počítače místo typu disku. To znamená, že pro virtuální počítač Standard_Fs Series bez ohledu na to, jestli pro daný typ disku zvolíte SSD nebo HDD, je limit IOPS pro druhý datový disk 2300 IOPS.

V následující tabulce jsou uvedené virtuální počítače, které jsou podporované na Azure Stack, spolu s jejich konfigurací:

| type           | Size          | Rozsah podporovaných velikostí |
| ---------------| ------------- | ------------------------ |
|Obecné účely |Basic A        |[A0 – A4](azure-stack-vm-sizes.md#basic-a)                   |
|Obecné účely |Standardní A     |[A0 – A7](azure-stack-vm-sizes.md#standard-a)              |
|Obecné účely |D-series       |[D1 – D4](azure-stack-vm-sizes.md#d-series)              |
|Obecné účely |Dv2-series     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Obecné účely |DS-series      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Obecné účely |DSv2-series    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Optimalizované z hlediska paměti|D-series       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|Optimalizované z hlediska paměti|DS-series      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|Optimalizované z hlediska paměti|Dv2-series     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Optimalizované z hlediska paměti|DSv2-Series-  |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

Velikosti virtuálních počítačů a jejich přidružená množství prostředků jsou konzistentní mezi Azure Stack a Azure. Tato konzistence zahrnuje množství paměti, počet jader a počet a velikost datových disků, které lze vytvořit. Výkon virtuálních počítačů se stejnou velikostí ale závisí na základních charakteristikách konkrétního Azure Stackho prostředí.

## <a name="vm-extensions"></a>Rozšíření virtuálních počítačů

Azure Stack obsahuje malou sadu rozšíření. Aktualizace a další rozšíření jsou k dispozici prostřednictvím syndikace webu Marketplace.

Pomocí následujícího skriptu PowerShellu Získejte seznam rozšíření virtuálních počítačů, která jsou k dispozici ve vašem prostředí Azure Stack:

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

Pokud zřízení rozšíření na nasazení virtuálního počítače trvá moc dlouho, nechte časový limit zřizování místo toho, abyste se pokoušeli zastavit proces navrácení nebo odstranění virtuálního počítače.

## <a name="api-versions"></a>Verze rozhraní API

Funkce virtuálních počítačů v Azure Stack podporují následující verze rozhraní API:

![Typy prostředků virtuálních počítačů](media/azure-stack-vm-considerations/vm-resoource-types.png)

K získání verzí rozhraní API pro funkce virtuálních počítačů, které jsou k dispozici ve vašem prostředí Azure Stack, můžete použít následující skript prostředí PowerShell:

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```

Seznam podporovaných typů prostředků a verzí rozhraní API se může lišit, pokud operátor cloudu aktualizuje vaše Azure Stack prostředí na novější verzi.

## <a name="windows-activation"></a>Aktivace Windows

Produkty systému Windows musí být používány v souladu s právy k používání produktů a licenčními podmínkami společnosti Microsoft. Azure Stack pro aktivaci virtuálních počítačů s Windows serverem používá [automatickou aktivaci virtuálního počítače](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA).

- Azure Stack hostitel aktivuje Windows pomocí klíčů AVMA pro Windows Server 2016. Všechny virtuální počítače, na kterých běží Windows Server 2012 R2 nebo novější, se automaticky aktivují.
- Virtuální počítače s Windows Serverem 2012 nebo starším se neaktivují automaticky a musí se aktivovat pomocí aktivace pomocí kódu [MAK](https://technet.microsoft.com/library/ff793438.aspx). Pokud chcete použít aktivaci pomocí klíče k vícenásobné aktivaci, musíte zadat vlastní kód Product Key.

Microsoft Azure používá k aktivaci virtuálních počítačů s Windows aktivaci pomocí služby správy klíčů. Pokud přesunete virtuální počítač z Azure Stack do Azure a dojde k problémům s aktivací, přečtěte si téma řešení potíží s [aktivací virtuálních počítačů Azure s Windows](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-activation-problems). Další informace najdete v příspěvku na blogu týmu podpory pro Azure na virtuálních počítačích Azure, které se [týkají potíží s aktivací Windows](https://blogs.msdn.microsoft.com/mast/2017/06/14/troubleshooting-windows-activation-failures-on-azure-vms/) .

## <a name="next-steps"></a>Další postup

[Vytvoření virtuálního počítače s Windows pomocí PowerShellu v Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)
