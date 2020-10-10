---
title: Poznámky k verzi Azure Stack HCI
description: Poznámky k verzi pro Azure Stack HCI, verze 20H2
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/17/2020
ms.openlocfilehash: ffa5f5149a3ddd681d20ae11b0aa79ec9fed29d0
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91899563"
---
# <a name="release-notes-for-azure-stack-hci-public-preview"></a>Poznámky k verzi pro Azure Stack HCI Public Preview

> Platí pro: Azure Stack HCI, verze 20H2

Tento článek popisuje obsah rozhraní Azure Stack HCI Public Preview aktualizace balíčků.

## <a name="september-17-2020-preview-update-kb4577629"></a>17. září 2020 – aktualizace Preview (KB4577629)

Tato aktualizace zahrnuje vylepšení a opravy pro nejnovější verzi Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Vylepšení a opravy
Tato nezabezpečená aktualizace zahrnuje vylepšení kvality. Mezi klíčové změny patří:
- Vyřešili jsme problém, kdy se provoz softwaru Load Balancer (SLB) procházející průchozím Multiplexorem může přesměrovat na jiného hostitele, který může způsobit selhání připojení aplikace.

### <a name="known-issues-in-this-update"></a>Známé problémy v této aktualizaci
Společnost Microsoft v současné době neví o problémech s touto aktualizací.

### <a name="how-to-get-this-update"></a>Jak získat tuto aktualizaci
Aktualizace zabezpečení (KB4577629) pro [Azure Stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se doručí prostřednictvím web Windows Update. září 2020. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1259), si můžete stáhnout z [informací o souboru pro kumulativní aktualizaci 4577629](https://download.microsoft.com/download/9/1/a/91addcbb-2b36-408c-ab88-736de42edb98/4577629.csv) .

   > [!NOTE]
   > Některé soubory mají ve sloupci "verze souboru CSV" hodnotu "nelze použít". To může vést k falešně pozitivním nebo falešně negativním hodnotám při použití nástrojů pro detekci kontroly třetích stran k ověření sestavení.

## <a name="september-8-2020-security-update-kb4577470"></a>8. září 2020 aktualizace zabezpečení (KB4577470)

Tato aktualizace zahrnuje vylepšení a opravy pro nejnovější verzi Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Vylepšení a opravy
Tato aktualizace obsahuje různá vylepšení zabezpečení vnitřních funkcí operačního systému. Pro tuto verzi nebyly zdokumentovány žádné další problémy.

Další informace o vyřešených chybách zabezpečení najdete v [příručce k aktualizaci zabezpečení](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Známé problémy v této aktualizaci
Společnost Microsoft v současné době neví o problémech s touto aktualizací.

### <a name="how-to-get-this-update"></a>Jak získat tuto aktualizaci
Aktualizace zabezpečení (KB4577470 [Azure Stack](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) ) od 8. září 2020 se doručuje prostřednictvím web Windows Update. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1226), si můžete stáhnout z [informací o souboru kumulativní aktualizace 4577470](https://download.microsoft.com/download/3/c/4/3c468525-5867-4cc3-8d34-dba88989adab/4577470.csv).

   > [!NOTE]
   > Některé soubory mají ve sloupci "verze souboru CSV" hodnotu "nelze použít". To může vést k falešně pozitivním nebo falešně negativním hodnotám při použití nástrojů pro detekci kontroly třetích stran k ověření sestavení.

## <a name="september-8-2020-servicing-stack-update-kb4577558"></a>8. září 2020 aktualizace servisního zásobníku (KB4577558)

Tato aktualizace obsahuje vylepšení kvality pro nejnovější verzi Azure Stack HCL.

### <a name="improvements-and-fixes"></a>Vylepšení a opravy
Tato aktualizace přináší vylepšení kvality do zásobníku pro údržbu, což je součást, která instaluje aktualizace. Aktualizace cestou nadřazené (Servicing) zajistí, že máte robustní a spolehlivý zásobník pro údržbu, aby vaše zařízení mohla přijímat a instalovat aktualizace od Microsoftu.

### <a name="how-to-get-this-update"></a>Jak získat tuto aktualizaci
Aktualizace KB4577558 (Údržba Stack 2020) pro [Azure Stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se doručuje prostřednictvím web Windows Update. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="restart-information"></a>Informace o restartování 
Po instalaci této aktualizace nemusíte restartovat počítač.

### <a name="removal-information"></a>Informace o odebrání
Aktualizace zásobníku pro obsluhu (SSUs) provádějí změny, jak jsou aktualizace nainstalované a ze zařízení je nelze odinstalovat.

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1220), si můžete stáhnout z [informací o souboru kumulativní aktualizace 4577558](https://download.microsoft.com/download/8/f/6/8f612a9b-cb4e-4832-9397-156760848592/4577558.csv).

### <a name="references"></a>Reference

Informace o SSUs najdete v následujících článcích:

- [Aktualizace zásobníku údržby](/windows/deployment/update/servicing-stack-updates)
- [Aktualizace zásobníku pro obsluhu (cestou nadřazené): nejčastější dotazy](https://support.microsoft.com/help/4535697)

Seznamte se s [terminologií](https://support.microsoft.com/help/824684) , kterou Microsoft používá k popisu aktualizací softwaru.

## <a name="august-11-2020-security-update-kb4574585"></a>11. srpna 2020, aktualizace zabezpečení (KB4574585)

Tato aktualizace zahrnuje vylepšení a opravy pro nejnovější verzi Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Vylepšení a opravy
Tato aktualizace obsahuje různá vylepšení zabezpečení vnitřních funkcí operačního systému. Pro tuto verzi nebyly zdokumentovány žádné další problémy.

Další informace o vyřešených chybách zabezpečení najdete v [příručce k aktualizaci zabezpečení](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Známé problémy v této aktualizaci
Společnost Microsoft v současné době neví o problémech s touto aktualizací.

### <a name="how-to-get-this-update"></a>Jak získat tuto aktualizaci
Aktualizace zabezpečení (KB4574585) 11. srpna 2020 pro [Azure Stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se doručuje prostřednictvím web Windows Update. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1162), si můžete stáhnout z [informací o souboru kumulativní aktualizace 4574585](https://download.microsoft.com/download/7/f/4/7f451def-76c5-4cc0-9929-0c5efeb27d2f/4574585.csv).

   > [!NOTE]
   > Některé soubory mají ve sloupci "verze souboru CSV" hodnotu "nelze použít". To může vést k falešně pozitivním nebo falešně negativním hodnotám při použití nástrojů pro detekci kontroly třetích stran k ověření sestavení.