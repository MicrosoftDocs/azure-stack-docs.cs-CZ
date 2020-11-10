---
title: Poznámky k verzi Azure Stack HCI
description: Poznámky k verzi pro Azure Stack HCI, verze 20H2
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/10/2020
ms.openlocfilehash: eaa9417abd41828495d3cd685ecc1f56c42c92e1
ms.sourcegitcommit: 96bc36a203954622be411fdb038d601e49f97d4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94441170"
---
# <a name="release-notes-for-azure-stack-hci-public-preview"></a>Poznámky k verzi pro Azure Stack HCI Public Preview

> Platí pro: Azure Stack HCI, verze 20H2

Tento článek popisuje obsah rozhraní Azure Stack HCI Public Preview aktualizace balíčků.

## <a name="november-10-2020-security-update-kb4586811"></a>10. listopadu 2020 aktualizace zabezpečení (KB4586811)

Tato aktualizace zahrnuje vylepšení a opravy pro nejnovější verzi Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Vylepšení a opravy
Tato aktualizace obsahuje různá vylepšení zabezpečení vnitřních funkcí operačního systému. Pro tuto verzi nebyly zdokumentovány žádné další problémy.

Další informace o vyřešených chybách zabezpečení najdete v [příručce k aktualizaci zabezpečení](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Známé problémy v této aktualizaci
Společnost Microsoft v současné době neví o problémech s touto aktualizací.

### <a name="how-to-get-this-update"></a>Jak získat tuto aktualizaci
Aktualizace zabezpečení (KB4586811) 10. listopadu 2020 pro [Azure Stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se doručuje prostřednictvím web Windows Update. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1345), si můžete stáhnout z [informací o souboru kumulativní aktualizace 4586811](https://download.microsoft.com/download/8/f/2/8f2ce4bb-e113-4abc-b3ff-f0f4c4c71403/4586811.csv).

   > [!NOTE]
   > Některé soubory mají ve sloupci "verze souboru CSV" hodnotu "nelze použít". To může vést k falešně pozitivním nebo falešně negativním hodnotám při použití nástrojů pro detekci kontroly třetích stran k ověření sestavení.

## <a name="november-10-2020-servicing-stack-update-kb4590242"></a>10. listopadu 2020 aktualizace servisního zásobníku (KB4590242)

Tato aktualizace obsahuje vylepšení kvality pro nejnovější verzi Azure Stack HCL.

### <a name="improvements-and-fixes"></a>Vylepšení a opravy
Tato aktualizace přináší vylepšení kvality do zásobníku pro údržbu, což je součást, která instaluje aktualizace. Aktualizace cestou nadřazené (Servicing) zajistí, že máte robustní a spolehlivý zásobník pro údržbu, aby vaše zařízení mohla přijímat a instalovat aktualizace od Microsoftu.

### <a name="how-to-get-this-update"></a>Jak získat tuto aktualizaci
Aktualizace KB4590242 (Údržba Stack 2020) pro [Azure Stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se doručuje prostřednictvím web Windows Update. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="restart-information"></a>Informace o restartování
Po instalaci této aktualizace nemusíte restartovat počítač.

### <a name="removal-information"></a>Informace o odebrání
Aktualizace zásobníku pro obsluhu (SSUs) provádějí změny, jak jsou aktualizace nainstalované a ze zařízení je nelze odinstalovat.

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1342), si můžete stáhnout z [informací o souboru kumulativní aktualizace 4590242](https://download.microsoft.com/download/b/b/4/bb4fb4f5-c0ba-4e55-bada-d72310857982/4590242.csv).

### <a name="references"></a>Reference

Informace o SSUs najdete v následujících článcích:

- [Aktualizace zásobníku údržby](/windows/deployment/update/servicing-stack-updates)
- [Aktualizace zásobníku pro obsluhu (cestou nadřazené): nejčastější dotazy](https://support.microsoft.com/help/4535697)

Seznamte se s [terminologií](https://support.microsoft.com/help/824684) , kterou Microsoft používá k popisu aktualizací softwaru.

## <a name="october-20-2020-preview-update-kb4580388"></a>20. října 2020, aktualizace Preview (KB4580388)

Tato aktualizace zahrnuje vylepšení a opravy pro nejnovější verzi Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Vylepšení a opravy
Tato nezabezpečená aktualizace zahrnuje vylepšení kvality. Mezi klíčové změny patří:

- V této aktualizaci Azure Stack zákazníci se systémem HCI, kteří uchovávají platné licence na Windows Server 2019 Datacenter Edition, je můžou používat k pohodlné aktivaci virtuálních počítačů hostovaných na Azure Stack HCI, aniž by museli spravovat kódy Product Key pro každý virtuální počítač.

### <a name="known-issues-in-this-update"></a>Známé problémy v této aktualizaci

Společnost Microsoft si je vědoma jednoho problému s touto aktualizací.

#### <a name="symptom"></a>Příznak
Při použití Migrace za provozu k přesunu virtuálního počítače mezi systémy Windows Server a Azure Stack HCL se může zobrazit chyba "blokovaná operace migrace pro virtuální počítač, <vmname> protože migrace virtuálních počítačů mezi různými edicemi Windows není podporovaná (ID virtuálního počítače)."

Případně to může také způsobit selhání operace aktualizace pro clustery (CAU), pokud se očekává, že se některé z virtuálních počítačů Migrace za provozu během aktualizace pro clustery.

#### <a name="workaround"></a>Alternativní řešení

Místo Migrace za provozu použijte rychlou migraci. Pokud používáte funkci CAU, změňte výchozí chování dočasně tak, aby funkce CAU používala rychlou migraci.

Příklad:

```powershell
Get-ClusterResourceType "Virtual Machine" | Set-ClusterParameter MoveTypeThreshold 3001
```

`MoveTypeThreshold`Po úspěšném dokončení aktualizace pro clustery se doporučuje vrátit zpět na předchozí hodnotu.

Další informace najdete v tématu [Konfigurace způsobu přesunutí virtuálních počítačů při vyprázdnění uzlu](https://techcommunity.microsoft.com/t5/failover-clustering/configuring-how-vms-are-moved-when-a-node-is-drained/ba-p/371848).

### <a name="how-to-get-this-update"></a>Jak získat tuto aktualizaci
20. října 2020 aktualizace zabezpečení (KB4580388) pro [Azure Stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) je poskytována prostřednictvím web Windows Update. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1321), si můžete stáhnout z [informací o souboru kumulativní aktualizace 4580388](https://download.microsoft.com/download/2/f/b/2fb766d3-c4c8-4279-8718-8efbd0b6f211/4580388.csv).

   > [!NOTE]
   > Některé soubory mají ve sloupci "verze souboru CSV" hodnotu "nelze použít". To může vést k falešně pozitivním nebo falešně negativním hodnotám při použití nástrojů pro detekci kontroly třetích stran k ověření sestavení.

## <a name="october-13-2020-security-update-kb4580363"></a>13. října 2020 aktualizace zabezpečení (KB4580363)

Tato aktualizace zahrnuje vylepšení a opravy pro nejnovější verzi Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Vylepšení a opravy
Tato aktualizace obsahuje různá vylepšení zabezpečení vnitřních funkcí operačního systému. Pro tuto verzi nebyly zdokumentovány žádné další problémy.

Další informace o vyřešených chybách zabezpečení najdete v [příručce k aktualizaci zabezpečení](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Známé problémy v této aktualizaci
Společnost Microsoft v současné době neví o problémech s touto aktualizací.

### <a name="how-to-get-this-update"></a>Jak získat tuto aktualizaci
2020 aktualizace zabezpečení (KB4580363) pro [Azure Stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) je poskytována prostřednictvím web Windows Update. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1288), si můžete stáhnout z [informací o souboru kumulativní aktualizace 4580363](https://download.microsoft.com/download/f/7/8/f78801f0-e7e5-4a3d-a971-f642ccd24ee4/4580363.csv).

   > [!NOTE]
   > Některé soubory mají ve sloupci "verze souboru CSV" hodnotu "nelze použít". To může vést k falešně pozitivním nebo falešně negativním hodnotám při použití nástrojů pro detekci kontroly třetích stran k ověření sestavení.

## <a name="october-13-2020-servicing-stack-update-kb4583287"></a>13. října 2020 aktualizace sady Servicing (KB4583287)

Tato aktualizace obsahuje vylepšení kvality pro nejnovější verzi Azure Stack HCL.

### <a name="improvements-and-fixes"></a>Vylepšení a opravy
Tato aktualizace přináší vylepšení kvality do zásobníku pro údržbu, což je součást, která instaluje aktualizace. Aktualizace cestou nadřazené (Servicing) zajistí, že máte robustní a spolehlivý zásobník pro údržbu, aby vaše zařízení mohla přijímat a instalovat aktualizace od Microsoftu.

### <a name="how-to-get-this-update"></a>Jak získat tuto aktualizaci
Aktualizace KB4583287 (Servicing) pro Azure Stack v 2020 rozhraní [HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) je dodávána prostřednictvím web Windows Update. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="restart-information"></a>Informace o restartování
Po instalaci této aktualizace nemusíte restartovat počítač.

### <a name="removal-information"></a>Informace o odebrání
Aktualizace zásobníku pro obsluhu (SSUs) provádějí změny, jak jsou aktualizace nainstalované a ze zařízení je nelze odinstalovat.

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1287), si můžete stáhnout z [informací o souboru kumulativní aktualizace 4583287](https://download.microsoft.com/download/b/8/5/b85160fb-85d9-49f9-b9d5-7dbc0158a944/4583287.csv).

### <a name="references"></a>Reference

Informace o SSUs najdete v následujících článcích:

- [Aktualizace zásobníku údržby](/windows/deployment/update/servicing-stack-updates)
- [Aktualizace zásobníku pro obsluhu (cestou nadřazené): nejčastější dotazy](https://support.microsoft.com/help/4535697)

Seznamte se s [terminologií](https://support.microsoft.com/help/824684) , kterou Microsoft používá k popisu aktualizací softwaru.

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