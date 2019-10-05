---
title: Seznámení s Azure Stackmi virtuálními počítači | Microsoft Docs
description: Přečtěte si o Azure Stack virtuálních počítačích.
services: azure-stack
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: de96b74351fa3becd0b066da4430e42cb2a9cea7
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961686"
---
# <a name="introduction-to-azure-stack-vms"></a>Seznámení s Azure Stackmi virtuálními počítači

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit @ no__t-0

Azure Stack nabízí virtuálním počítačům (VM) jako typ škálovatelných výpočetních prostředků na vyžádání. Pokud potřebujete větší kontrolu nad výpočetním prostředím, můžete vybrat virtuální počítač. Tento článek poskytuje podrobné informace před vytvořením prvního virtuálního počítače.

Virtuální počítač s Azure Stack poskytuje flexibilitu virtualizace bez nutnosti spravovat clustery nebo jednotlivé počítače. Přesto ale budete muset virtuální počítač udržovat i tak, že provádíte úlohy, jako je konfigurace, opravy a aktualizace, a instalace softwaru, který na něm běží.

Azure Stack virtuálních počítačů můžete použít několika způsoby. Příklad:

- **Vývoj a testování**: Azure Stack virtuálních počítačů vám umožní vytvořit počítač s konkrétní konfigurací nutnou k vytváření kódu a testování aplikace.

- **Aplikace v cloudu**: Vzhledem k tomu, že poptávka po vaší aplikaci může kolísat, může být ekonomicky vhodná pro její spuštění na virtuálním počítači v Azure Stack. Platíte za další virtuální počítače, když je potřebujete, a když je neuděláte, můžete je vypnout.

- **Rozšířené datové centrum**: Virtuální počítače ve virtuální síti Azure Stack se můžou připojit k síti vaší organizace nebo k Azure.

Virtuální počítače, které vaše aplikace používá, umožňují horizontální navýšení nebo navýšení kapacity, aby vyhovovaly vašim potřebám.

## <a name="before-creating-a-vm"></a>Před vytvořením virtuálního počítače

Při vytváření infrastruktury aplikace v Azure Stack jsou vždy k dispozici návrhové faktory. Než začnete vytvářet infrastrukturu, je důležité zvážit tyto aspekty virtuálního počítače:

- Názvy prostředků vaší aplikace.
- Velikost virtuálního počítače.
- Maximální počet virtuálních počítačů, které lze vytvořit.
- Operační systém, který běží na virtuálním počítači.
- Konfigurace virtuálního počítače po jeho spuštění.
- Související prostředky, které virtuální počítač potřebuje.

### <a name="naming"></a>pojmenování

K VIRTUÁLNÍmu počítači je přiřazený název a má název počítače nakonfigurovaný jako součást operačního systému. Název virtuálního může být až 15 znaků dlouhý.

Pokud k vytvoření disku operačního systému použijete Azure Stack, název počítače a název virtuálního počítače budou stejné. Pokud nahrajete a použijete vlastní image, která obsahuje dříve nakonfigurovaný operační systém a použijete ji k vytvoření virtuálního počítače, názvy se můžou lišit. Když nahráváte vlastní soubor bitové kopie, doporučujeme, aby název počítače v operačním systému odpovídal názvu virtuálního počítače.

### <a name="vm-size"></a>Velikost virtuálního počítače

Velikost virtuálního počítače, který použijete, je určená úlohou, kterou chcete spustit. Velikost, kterou vyberete, pak určuje další faktory, jako například výpočetní výkon, paměť a kapacitu úložiště. Azure Stack nabízí různé druhy velikostí, které podporují mnoho typů použití.

### <a name="vm-limits"></a>Omezení virtuálních počítačů

Vaše předplatné má nastavené výchozí limity kvót, které mohou mít vliv na nasazení virtuálních počítačů pro váš projekt. Aktuální limit jednoho předplatného je 20 virtuálních počítačů na oblast.

### <a name="operating-system-disks-and-images"></a>Disky a image operačních systémů

Virtuální počítače používají virtuální pevné disky (VHD) k ukládání jejich operačního systému (OS) a dat. Virtuální pevné disky se používají také pro image, ze kterých si zvolíte, a nainstalujte operační systém. Azure Stack poskytuje Marketplace k používání různých verzí a typů operačních systémů. Image z Marketplace se identifikují jako vydavatel obrázku, nabídka, SKU a verze (obvykle se jedná o nejnovější verzi, která je určená jako **nejnovější**).

Následující tabulka ukazuje, jak najít informace pro obrázek:

|Metoda|Popis|
|---------|---------|
|Portál Azure Stack|Hodnoty se pro vás zadají automaticky, když vyberete image, která se má použít.|
|Azure Stack PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|Rozhraní REST API     |[Vypsat vydavatele imagí](/rest/api/compute/platformimages/platformimages-list-publishers)<br>[Vypsat nabídky imagí](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[Výpis SKU imagí](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

Můžete zvolit nahrávání a používání vlastní image. V takovém případě se nepoužijí název vydavatele, nabídka a SKU.

### <a name="extensions"></a>Rozšíření

Rozšíření virtuálních počítačů poskytují vašim VIRTUÁLNÍm počítačům další možnosti prostřednictvím konfigurace po nasazení a automatizovaných úloh.
Pomocí rozšíření můžete provádět tyto běžné úlohy:

- **Spustit vlastní skripty**: Rozšíření vlastních skriptů vám pomůže nakonfigurovat úlohy na VIRTUÁLNÍm počítači spuštěním skriptu, když se zřídí virtuální počítač.

- **Nasazení a Správa konfigurací**: Rozšíření konfigurace požadovaného stavu prostředí PowerShell pomáhá nastavit DSC na virtuálním počítači pro správu konfigurací a prostředí.

- **Shromažďování diagnostických dat**: Rozšíření Azure Diagnostics vám pomůže nakonfigurovat virtuální počítač pro shromažďování diagnostických dat, která se dají použít k monitorování stavu vaší aplikace.

### <a name="related-resources"></a>Související prostředky

Prostředky v následující tabulce používá virtuální počítač, musí existovat nebo se vytvořit při vytvoření virtuálního počítače:

|Resource|Požadováno|Popis|
|---------|---------|---------|
|Resource group|Ano|Virtuální počítač musí být součástí skupiny prostředků.|
|Účet úložiště|Ne|Virtuální počítač nepotřebuje účet úložiště k ukládání virtuálních pevných disků, pokud se používá služba Managed disks. <br>Virtuální počítač potřebuje účet úložiště pro ukládání virtuálních pevných disků, pokud používají nespravované disky.|
|Virtuální síť|Ano|Virtuální počítač musí být členem virtuální sítě.|
|Veřejná IP adresa|Ne|Virtuální počítač může mít přiřazenou veřejnou IP adresu pro umožnění vzdáleného přístupu.|
|Síťové rozhraní|Ano|Virtuální počítač potřebuje síťové rozhraní ke komunikaci v síti.|
|Datové disky|Ne|Virtuální počítač může zahrnovat datové disky pro rozšíření možností úložiště.|

## <a name="create-your-first-vm"></a>Vytvoření prvního virtuálního počítače

Máte několik možností, jak vytvořit virtuální počítač. Vaše volba závisí na vašem prostředí. Následující tabulka poskytuje informace, které vám pomůžou začít vytvářet virtuální počítač:

|Metoda|Článek|
|---------|---------|
|Portál Azure Stack|Vytvoření virtuálního počítače s Windows pomocí portálu Azure Stack<br>[Vytvoření virtuálního počítače se systémem Linux pomocí Azure Stackového portálu](azure-stack-quick-linux-portal.md)|
|Šablony|Šablony pro rychlý Start Azure Stack jsou umístěny na adrese:<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStarvirtualt-Templates)|
|PowerShell|[Vytvoření virtuálního počítače s Windows pomocí PowerShellu v Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)<br>[Vytvoření virtuálního počítače se systémem Linux pomocí prostředí PowerShell v Azure Stack](azure-stack-quick-create-vm-linux-powershell.md)|
|Rozhraní příkazového řádku|[Vytvoření virtuálního počítače s Windows pomocí rozhraní příkazového řádku v Azure Stack](azure-stack-quick-create-vm-windows-cli.md)<br>[Vytvoření virtuálního počítače se systémem Linux pomocí rozhraní příkazového řádku v Azure Stack](azure-stack-quick-create-vm-linux-cli.md)|

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
|Portál Azure Stack|V nabídce centra klikněte na **Virtual Machines** a ze seznamu vyberte virtuální počítač. Na stránce pro virtuální počítač máte přístup k informacím o přehledu, nastavení hodnot a metrikách monitorování.|
|Azure PowerShell|Správa virtuálních počítačů je v Azure a Azure Stack podobná. Další informace o používání PowerShellu najdete v následujícím tématu Azure:<br>[Vytvoření a správa virtuálních počítačů s Windows pomocí modulu Azure PowerShell](/azure/virtual-machines/windows/tutorial-manage-vm#understand-vm-sizes)|
|Klientské sady SDK|Použití C# ke správě virtuálních počítačů je v Azure a Azure Stack podobné. Další informace najdete v následujícím tématu Azure:<br>[Vytváření a správa virtuálních počítačů s Windows v Azure pomocíC#](/azure/virtual-machines/windows/csharp)|

### <a name="connect-to-your-vm"></a>Připojení k virtuálnímu počítači

Pomocí tlačítka **připojit** na portálu Azure Stack se můžete připojit k virtuálnímu počítači.

## <a name="next-steps"></a>Další kroky

- [Požadavky na virtuální počítače v Azure Stack](azure-stack-vm-considerations.md)
