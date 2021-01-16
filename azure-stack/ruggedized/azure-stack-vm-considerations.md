---
title: Funkce Azure Stack virtuálního počítače | Microsoft Docs
description: Přečtěte si o různých funkcích a ohledech při práci s virtuálními počítači v centru Azure Stack.
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
ms.date: 10/14/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 79071cb54da88b842bcf149e72874b75107a0766
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256026"
---
# <a name="azure-stack-vm-features"></a>Funkce Azure Stack virtuálního počítače

Virtuální počítače s Azure Stack poskytují škálovatelné výpočetní prostředky na vyžádání. Než nasadíte virtuální počítače, měli byste se seznámit s rozdíly mezi funkcemi virtuálních počítačů, které jsou dostupné v Azure Stack a Microsoft Azure. Tento článek popisuje tyto rozdíly a identifikuje klíčové důležité důvody pro plánování nasazení virtuálních počítačů. Další informace o rozdílech na vysoké úrovni mezi Azure Stack a Azure najdete v článku [klíčové důležité informace](../user/azure-stack-considerations.md) .

## <a name="vm-differences"></a>Rozdíly virtuálních počítačů

| Příznak | Azure (Global) | Azure Stack |
| --- | --- | --- |
| Image virtuálních počítačů | Azure Marketplace obsahuje obrázky, které můžete použít k vytvoření virtuálního počítače. Pokud chcete zobrazit seznam imagí, které jsou k dispozici v Azure Marketplace, zobrazte stránku [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) . | Ve výchozím nastavení nejsou k dispozici žádné obrázky na webu Azure Stack Marketplace. Správce cloudu Azure Stack musí publikovat nebo stahovat image na webu Azure Stack Marketplace, aby je mohli uživatelé používat. |
| Velikosti virtuálních počítačů | Azure podporuje širokou škálu velikostí virtuálních počítačů. Další informace o dostupných velikostech a možnostech najdete v tématech velikosti virtuálních počítačů s [Windows](/azure/virtual-machines/sizes) a [velikosti virtuálních počítačů Linux](/azure/virtual-machines/linux/sizes) . | Azure Stack podporuje podmnožinu velikostí virtuálních počítačů, které jsou k dispozici v Azure. Pokud chcete zobrazit seznam podporovaných velikostí, přečtěte si část [velikosti virtuálních počítačů](#vm-sizes) v tomto článku. |
| Kvóty virtuálních počítačů | [Omezení kvót](/azure/azure-subscription-service-limits#service-specific-limits) nastavuje Microsoft. | Správce cloudu Azure Stack musí před tím, než nabídne virtuální počítač svým uživatelům, přiřadit kvóty. |
| Rozšíření virtuálních počítačů |Azure podporuje širokou škálu rozšíření virtuálních počítačů. Další informace o dostupných rozšířeních najdete v článku o [rozšířeních virtuálních počítačů a funkcích](/azure/virtual-machines/windows/extensions-features) .| Azure Stack podporuje podmnožinu rozšíření, která jsou k dispozici v Azure, přičemž každé rozšíření má konkrétní verze. Správce cloudu Azure Stack může zvolit, která rozšíření budou uživatelům k dispozici. Pokud chcete zobrazit seznam podporovaných rozšíření, přečtěte si část [rozšíření virtuálních počítačů](#vm-extensions) v tomto článku. |
| Síť virtuálního počítače | Veřejné IP adresy přiřazené k virtuálnímu počítači tenanta jsou přístupné přes Internet.<br><br><br>Virtuální počítače Azure mají pevný název DNS. | <!-- TZLASDKFIX Public IP addresses assigned to a tenant VM are accessible within the Azure Stack Development Kit environment only. A user must have access to the Azure Stack Development Kit via [RDP](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) or [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) to connect to a VM that is created in Azure Stack.--><br><br>Virtuální počítače vytvořené v rámci konkrétní instance Azure Stack mají název DNS na základě hodnoty, která je nakonfigurovaná správcem cloudu. |
| Úložiště virtuálního počítače | Podporuje [spravované disky.](/azure/virtual-machines/windows/managed-disks-overview) | Spravované disky jsou podporované v Azure Stack s verzí 1808 a novější. |
| Výkon disku virtuálního počítače | Závisí na typu a velikosti disku. | Závisí na velikosti virtuálního počítače, ke kterému jsou disky připojené. Další informace najdete [v článku velikosti virtuálních počítačů podporované v Azure Stack](azure-stack-vm-sizes.md) .
| Verze rozhraní API | Azure má vždycky nejnovější verze rozhraní API pro všechny funkce virtuálních počítačů. | Azure Stack podporuje pro tyto služby specifické služby Azure a konkrétní verze rozhraní API. Pokud chcete zobrazit seznam podporovaných verzí rozhraní API, přečtěte si část [verze rozhraní API](#api-versions) tohoto článku. |
| Azure Instance Metadata Service | Azure Instance Metadata Service poskytuje informace o spuštěných instancích virtuálních počítačů, které se dají použít ke správě a nastavení virtuálního počítače.  | Azure Instance Metadata Service se v Azure Stack nepodporuje. |
| Skupiny dostupnosti virtuálních počítačů|Více domén selhání (2 nebo 3 podle oblasti).<br>Více aktualizačních domén.|Více domén selhání (2 nebo 3 podle oblasti).<br>Jediná aktualizační doména s migrací za provozu k ochraně úloh během aktualizace. 20 aktualizačních domén podporovaných pro kompatibilitu šablon.<br>Skupina virtuálních počítačů a dostupnosti by měla být ve stejném umístění a skupině prostředků.|
| Škálovací sady virtuálních počítačů|Automatické škálování je podporováno.|Automatické škálování se nepodporuje.<br><br>Přidejte další instance do sady škálování pomocí portálu, Správce prostředků šablon nebo PowerShellu. |
| Disk s kopií cloudu | Vyberte koncové body z vlastností účtu úložiště, které jsou k dispozici v Azure Stack. | [Určující Cloud](/windows-server/failover-clustering/deploy-cloud-witness) je typ určujícího disku kvora clusteru s podporou převzetí služeb při selhání, který používá Microsoft Azure k poskytnutí hlasu kvora clusteru.<br>Koncové body v globálním Azure v porovnání s Azure Stack můžou vypadat takto:<br>Pro globální Azure:<br>`https://mywitness.blob.core.windows.net/`<br>Pro Azure Stack:<br>`https://mywitness.blob.<region>.<FQDN>/`|
| Diagnostika virtuálního počítače | Diagnostika virtuálního počítače se systémem Linux je podporována. | Diagnostika virtuálního počítače se systémem Linux není v Azure Stack podporována. Když nasadíte virtuální počítač se systémem Linux s povoleným diagnostikou virtuálních počítačů, nasazení se nezdařilo. Nasazení se také nepovede, pokud povolíte základní metriky virtuálního počítače se systémem Linux prostřednictvím nastavení diagnostiky. |

## <a name="vm-sizes"></a>Velikost virtuálních počítačů

Azure Stack ukládá omezení prostředků, aby nedocházelo k využití prostředků (místní server a úroveň služeb). Tato omezení zlepšují prostředí tenanta tím, že snižují vliv spotřeby prostředků na ostatní klienty.

- U odchozích síťových přenosů z virtuálního počítače je na místě zakončení šířky pásma. Velká písmena v Azure Stack jsou stejná jako u velkých písmen v Azure.
- U prostředků úložiště Azure Stack implementuje omezení pro IOPS úložiště (vstupní/výstupní operace za sekundu), aby se předešlo základní přečerpání prostředků podle tenantů pro použití úložiště.
- Pro disky virtuálních počítačů je IOPS disku na Azure Stack funkcí velikosti virtuálního počítače místo typu disku. To znamená, že pro virtuální počítač s Standard_Fs Series bez ohledu na to, jestli pro daný typ disku zvolíte SSD nebo HDD, je limit IOPS pro druhý datový disk 2300 IOPS.

V následující tabulce jsou uvedené virtuální počítače, které jsou podporované na Azure Stack, spolu s jejich konfigurací:

| Typ            | Velikost          | Rozsah podporovaných velikostí |
| ----------------| ------------- | ------------------------ |
|Obecné účely  |Basic A        |[A0 – A4](azure-stack-vm-sizes.md#basic-a)                   |
|Obecné účely  |Standardní A     |[A0 – A7](azure-stack-vm-sizes.md#standard-a)              |
|Obecné účely  |Av2-series     |[A1_v2 – A8m_v2](azure-stack-vm-sizes.md#av2-series)     |
|Obecné účely  |D-series       |[D1 – D4](azure-stack-vm-sizes.md#d-series)              |
|Obecné účely  |Dv2-series     |[D1_v2 – D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|Obecné účely  |DS-series      |[DS1 – DS4](azure-stack-vm-sizes.md#dv2-series)            |
|Obecné účely  |DSv2-series    |[DS1_v2 – DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|Optimalizované pro paměť. |D-series       |[D11 – D14](azure-stack-vm-sizes.md#mo-d)            |
|Optimalizované pro paměť. |DS-series      |[DS11 – DS14](azure-stack-vm-sizes.md#mo-ds)|
|Optimalizované pro paměť. |Dv2-series     |[D11_v2 – DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|Optimalizované pro paměť. |DSv2-series    |[DS11_v2 – DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |
|Optimalizované pro výpočty.|F-series       |[F1 – F16 ÚROVNĚ](azure-stack-vm-sizes.md#f-series)    |
|Optimalizované pro výpočty.|Řada FS      |[F1s úrovně – F16s úrovně](azure-stack-vm-sizes.md#fs-series)    |
|Optimalizované pro výpočty.|Řada Fsv2    |[F2s_v2 – F64s_v2](azure-stack-vm-sizes.md#fsv2-series)    |

Velikosti virtuálních počítačů a jejich přidružená množství prostředků jsou konzistentní mezi Azure Stack a Azure. Tato konzistence zahrnuje množství paměti, počet jader a počet a velikost datových disků, které lze vytvořit. Výkon virtuálních počítačů se stejnou velikostí ale závisí na základních charakteristikách konkrétního Azure Stackho prostředí.

## <a name="vm-extensions"></a>Rozšíření virtuálních počítačů

Azure Stack obsahuje malou sadu rozšíření. Aktualizace a další rozšíření jsou k dispozici prostřednictvím syndikace webu Marketplace.

Pomocí následujícího skriptu PowerShellu Získejte seznam rozšíření virtuálních počítačů, která jsou k dispozici ve vašem prostředí Azure Stack:

Pokud zřízení rozšíření na nasazení virtuálního počítače trvá příliš dlouho, nechte místo toho časový limit pro zrušení přidělení nebo odstranění virtuálního počítače.

### <a name="az-modules"></a>[AZ modules](#tab/az1)

```powershell
Get-AzVmImagePublisher -Location local | `
  Get-AzVMExtensionImageType | `
  Get-AzVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm1)

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

---

## <a name="api-versions"></a>Verze rozhraní API

Funkce virtuálních počítačů v Azure Stack podporují následující verze rozhraní API:

"2017-12-01", "2017-03-30", "2016-03-30", "2015-06-15"

K získání verzí rozhraní API pro funkce virtuálních počítačů, které jsou k dispozici ve vašem prostředí Azure Stack, můžete použít následující skript prostředí PowerShell:

### <a name="az-modules"></a>[AZ modules](#tab/az2)

```powershell
Get-AzResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```
### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm2)

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like "Microsoft.compute"}
```

---


Seznam podporovaných typů prostředků a verzí rozhraní API se může lišit, pokud operátor cloudu aktualizuje vaše Azure Stack prostředí na novější verzi.

## <a name="windows-activation"></a>Aktivace Windows

Produkty systému Windows musí být používány v souladu s právy k používání produktů a licenčními podmínkami společnosti Microsoft. Azure Stack pro aktivaci virtuálních počítačů s Windows serverem používá [automatickou aktivaci virtuálního počítače](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v%3dws.11)) (AVMA).

- Azure Stack hostitel aktivuje Windows pomocí klíčů AVMA pro Windows Server 2016. Všechny virtuální počítače, na kterých běží Windows Server 2012 R2 nebo novější, se automaticky aktivují.
- Virtuální počítače s Windows Serverem 2012 nebo starším se neaktivují automaticky a musí se aktivovat pomocí aktivace pomocí kódu [MAK](/previous-versions/tn-archive/ff793438(v=technet.10)). Pokud chcete použít aktivaci pomocí klíče k vícenásobné aktivaci, musíte zadat vlastní kód Product Key.

Microsoft Azure používá k aktivaci virtuálních počítačů s Windows aktivaci pomocí služby správy klíčů. Pokud přesunete virtuální počítač z Azure Stack do Azure a dojde k problémům s aktivací, přečtěte si téma řešení potíží s [aktivací virtuálních počítačů Azure s Windows](/azure/virtual-machines/windows/troubleshoot-activation-problems). Další informace najdete v příspěvku na blogu týmu podpory pro Azure na virtuálních počítačích Azure, které se [týkají potíží s aktivací Windows](/archive/blogs/mast/troubleshooting-windows-activation-failures-on-azure-vms) .

## <a name="next-steps"></a>Další kroky

[Vytvoření virtuálního počítače s Windows pomocí PowerShellu v Azure Stack](../user/azure-stack-quick-create-vm-windows-powershell.md)