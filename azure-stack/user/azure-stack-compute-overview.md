---
title: Úvod k virtuálním počítačům centra Azure Stack | Microsoft Docs
description: Přečtěte si o Azure Stackch virtuálních počítačích centra.
services: azure-stack
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 7b072b95cae7148080ee0c9b1195960a3df041fc
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883792"
---
# <a name="introduction-to-azure-stack-hub-vms"></a>Úvod k virtuálním počítačům centra Azure Stack

Centrum Azure Stack nabízí virtuálním počítačům (VM) jako typ škálovatelných výpočetních prostředků na vyžádání. Pokud potřebujete větší kontrolu nad výpočetním prostředím, můžete vybrat virtuální počítač. Tento článek poskytuje podrobné informace před vytvořením prvního virtuálního počítače.

Virtuální počítač centra Azure Stack poskytuje flexibilitu virtualizace bez nutnosti spravovat clustery nebo jednotlivé počítače. Přesto ale budete muset virtuální počítač udržovat i tak, že provádíte úlohy, jako je konfigurace, opravy a aktualizace, a instalace softwaru, který na něm běží.

Virtuální počítače centra Azure Stack můžete použít několika způsoby. Příklad:

- **Vývoj a testování**: virtuální počítače centra Azure Stack umožňují vytvořit počítač s konkrétní konfigurací, která je nutná k vytváření kódu a testování aplikace.

- **Aplikace v cloudu**: vzhledem k tomu, že poptávka po vaší aplikaci může kolísat, může být ekonomicky vhodná pro její spuštění na virtuálním počítači v Azure Stackovém centru. Platíte za další virtuální počítače, když je potřebujete, a když je neuděláte, můžete je vypnout.

- **Rozšířené datové centrum**: virtuální počítače ve virtuální síti centra Azure Stack se můžou připojit k síti vaší organizace nebo k Azure.

Virtuální počítače, které vaše aplikace používá, umožňují horizontální navýšení nebo navýšení kapacity, aby vyhovovaly vašim potřebám.

## <a name="before-creating-a-vm"></a>Před vytvořením virtuálního počítače

Při sestavování infrastruktury aplikace v centru Azure Stack se vždy vyskytnou faktory návrhu. Než začnete vytvářet infrastrukturu, je důležité zvážit tyto aspekty virtuálního počítače:

- Názvy prostředků vaší aplikace.
- Velikost virtuálního počítače.
- Maximální počet virtuálních počítačů, které lze vytvořit.
- Operační systém, který běží na virtuálním počítači.
- Konfigurace virtuálního počítače po jeho spuštění.
- Související prostředky, které virtuální počítač potřebuje.

### <a name="naming"></a>Pojmenování

K VIRTUÁLNÍmu počítači je přiřazený název a má název počítače nakonfigurovaný jako součást operačního systému. Název virtuálního může být až 15 znaků dlouhý.

Pokud k vytvoření disku operačního systému použijete centrum Azure Stack, název počítače a název virtuálního počítače budou stejné. Pokud nahrajete a použijete vlastní image, která obsahuje dříve nakonfigurovaný operační systém a použijete ji k vytvoření virtuálního počítače, názvy se můžou lišit. Když nahráváte vlastní soubor bitové kopie, doporučujeme, aby název počítače v operačním systému odpovídal názvu virtuálního počítače.

### <a name="vm-size"></a>Velikost virtuálního počítače

Velikost virtuálního počítače, který použijete, je určená úlohou, kterou chcete spustit. Velikost, kterou vyberete, pak určuje další faktory, jako například výpočetní výkon, paměť a kapacitu úložiště. Centrum Azure Stack nabízí různé druhy velikostí, které podporují mnoho typů použití.

### <a name="vm-limits"></a>Omezení virtuálních počítačů

Vaše předplatné má nastavené výchozí limity kvót, které mohou mít vliv na nasazení virtuálních počítačů pro váš projekt. Aktuální limit jednoho předplatného je 20 virtuálních počítačů na oblast.

### <a name="operating-system-disks-and-images"></a>Disky a image operačních systémů

Virtuální počítače používají virtuální pevné disky (VHD) k ukládání jejich operačního systému (OS) a dat. Virtuální pevné disky se používají také pro image, ze kterých si zvolíte, a nainstalujte operační systém. Centrum Azure Stack poskytuje Marketplace pro použití s různými verzemi a typy operačních systémů. Image z Marketplace se identifikují jako vydavatel obrázku, nabídka, SKU a verze (obvykle se jedná o nejnovější verzi, která je určená jako **nejnovější**).

Následující tabulka ukazuje, jak najít informace pro obrázek:

|Metoda|Popis|
|---------|---------|
|Portál centra Azure Stack|Hodnoty se pro vás zadají automaticky, když vyberete image, která se má použít.|
|Prostředí PowerShell centra Azure Stack|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|Rozhraní REST API     |[Vypsat vydavatele imagí](/rest/api/compute/platformimages/platformimages-list-publishers)<br>[Vypsat nabídky imagí](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[Výpis SKU imagí](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

Můžete zvolit nahrávání a používání vlastní image. V takovém případě se nepoužijí název vydavatele, nabídka a SKU.

### <a name="extensions"></a>Rozšíření

Rozšíření virtuálních počítačů poskytují vašim VIRTUÁLNÍm počítačům další možnosti prostřednictvím konfigurace po nasazení a automatizovaných úloh.
Pomocí rozšíření můžete provádět tyto běžné úlohy:

- **Spustit vlastní skripty**: rozšíření vlastních skriptů vám pomůže nakonfigurovat úlohy na virtuálním počítači spuštěním skriptu, když se zřídí virtuální počítač.

- **Nasazení a Správa konfigurací**: rozšíření konfigurace požadovaného stavu prostředí PowerShell pomáhá nastavit DSC na virtuálním počítači pro správu konfigurací a prostředí.

- **Shromažďování diagnostických dat**: rozšíření Azure Diagnostics pomáhá nakonfigurovat virtuální počítač pro shromažďování diagnostických dat, která lze použít k monitorování stavu aplikace.

### <a name="related-resources"></a>Související materiály

Prostředky v následující tabulce používá virtuální počítač, musí existovat nebo se vytvořit při vytvoření virtuálního počítače:

|Prostředek|Požaduje se|Popis|
|---------|---------|---------|
|Skupina prostředků|Ano|Virtuální počítač musí být součástí skupiny prostředků.|
|Účet úložiště|Ne|Virtuální počítač nepotřebuje účet úložiště k ukládání virtuálních pevných disků, pokud se používá služba Managed disks. <br>Virtuální počítač potřebuje účet úložiště pro ukládání virtuálních pevných disků, pokud používají nespravované disky.|
|Virtuální síť|Ano|Virtuální počítač musí být členem virtuální sítě.|
|Veřejná IP adresa|Ne|Virtuální počítač může mít přiřazenou veřejnou IP adresu pro umožnění vzdáleného přístupu.|
|Síťové rozhraní|Ano|Virtuální počítač potřebuje síťové rozhraní ke komunikaci v síti.|
|Datové disky|Ne|Virtuální počítač může zahrnovat datové disky pro rozšíření možností úložiště.|

## <a name="create-your-first-vm"></a>Vytvoření prvního virtuálního počítače

Máte několik možností, jak vytvořit virtuální počítač. Vaše volba závisí na vašem prostředí. Následující tabulka poskytuje informace, které vám pomůžou začít vytvářet virtuální počítač:

|Metoda|Článek|
|---------|---------|
|Portál centra Azure Stack|Vytvoření virtuálního počítače s Windows pomocí portálu Azure Stack hub<br>[Vytvoření virtuálního počítače se systémem Linux pomocí portálu Azure Stack hub](azure-stack-quick-linux-portal.md)|
|Šablony|Šablony pro rychlý Start centra Azure Stack najdete na adrese:<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStarvirtualt-Templates)|
|PowerShell|[Vytvoření virtuálního počítače s Windows pomocí PowerShellu v Azure Stackovém centru](azure-stack-quick-create-vm-windows-powershell.md)<br>[Vytvoření virtuálního počítače se systémem Linux pomocí prostředí PowerShell v Azure Stackovém centru](azure-stack-quick-create-vm-linux-powershell.md)|
|Rozhraní příkazového řádku|[Vytvoření virtuálního počítače s Windows pomocí rozhraní příkazového řádku v Azure Stackovém centru](azure-stack-quick-create-vm-windows-cli.md)<br>[Vytvoření virtuálního počítače se systémem Linux pomocí rozhraní příkazového řádku v Azure Stack hub](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>Správa virtuálního počítače

Virtuální počítače můžete spravovat pomocí portálu založeného na prohlížeči, nástrojů příkazového řádku s podporou pro skriptování, nebo přímo prostřednictvím rozhraní API. Mezi obvyklé úlohy správy, které byste mohli udělat, patří:

- Získání informací o virtuálním počítači
- Připojení k virtuálnímu počítači
- Správa dostupnosti
- Vytváření záloh

### <a name="get-information-about-your-vm"></a>Získání informací o VIRTUÁLNÍm počítači

Následující tabulka uvádí některé způsoby, jak můžete získat informace o virtuálním počítači.

|Metoda|Popis|
|---------|---------|
|Portál centra Azure Stack|V nabídce centra klikněte na **Virtual Machines** a ze seznamu vyberte virtuální počítač. Na stránce pro virtuální počítač máte přístup k informacím o přehledu, nastavení hodnot a metrikách monitorování.|
|Azure PowerShell|Správa virtuálních počítačů je v Azure a centru Azure Stack podobná. Další informace o používání PowerShellu najdete v následujícím tématu Azure:<br>[Vytvoření a správa virtuálních počítačů s Windows pomocí modulu Azure PowerShell](/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|Klientské sady SDK|Použití C# ke správě virtuálních počítačů je podobné jako u Azure a centra Azure Stack. Další informace najdete v následujícím tématu Azure:<br>[Vytváření a správa virtuálních počítačů s Windows v Azure pomocíC#](/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-your-vm"></a>Připojení k virtuálnímu počítači

K připojení k VIRTUÁLNÍmu počítači můžete použít tlačítko **připojit** na portálu centra Azure Stack.

## <a name="next-steps"></a>Další kroky

- [Požadavky na virtuální počítače v centru Azure Stack](azure-stack-vm-considerations.md)
