---
title: Archiv poznámek k verzi Azure Stack HCI
description: Archivovaná zpráva k vydání verze pro Azure Stack HCL verze 20H2 Public Preview.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/21/2020
ms.openlocfilehash: 5125e9b27fbb9cc17e9381a9617fefce6409418f
ms.sourcegitcommit: a745662c7a5a18f135accf3f70d8508b57e83e2b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97737942"
---
# <a name="archived-release-notes-for-azure-stack-hci-version-20h2-public-preview"></a>Archivované poznámky k verzi pro Azure Stack HCI, verze 20H2 Public Preview

> Platí pro: Azure Stack HCI, verze 20H2

Tento článek popisuje obsah balíčků aktualizací pro Azure Stack HCI, verze 20H2 Public Preview vydané před vydáním všeobecné dostupnosti (GA) od 10. prosince 2020. Můžete si také prohlédnout aktuální [poznámky k verzi pro Azure Stack HCI](https://support.microsoft.com/help/4595086/).

## <a name="december-8-2020-security-update-kb4592441"></a>8. prosince 2020 aktualizace zabezpečení (KB4592441)

Tato aktualizace zahrnuje vylepšení a opravy pro Public Preview vydání Azure Stack HCI. 

### <a name="improvements-and-fixes"></a>Vylepšení a opravy
Tato aktualizace obsahuje různá vylepšení zabezpečení vnitřních funkcí operačního systému. Řeší chybu zabezpečení tím, že brání aplikacím, které běží jako systémový účet, z tisku na porty "FILE:". Aby bylo možné tento problém vyřešit v budoucnu, ujistěte se, že vaše aplikace nebo služby běží jako konkrétní účet uživatele nebo služby.

Pro tuto verzi nebyly zdokumentovány žádné další problémy.

Další informace o vyřešených chybách zabezpečení najdete v [příručce k aktualizaci zabezpečení](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Známé problémy v této aktualizaci
Společnost Microsoft v současné době neví o problémech s touto aktualizací.

### <a name="how-to-get-this-update"></a>Jak získat tuto aktualizaci
Aktualizace zabezpečení (KB4592441 [Azure Stack](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) ) 8. prosince 2020 je poskytována prostřednictvím web Windows Update. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1408), si můžete stáhnout z [informací o souboru kumulativní aktualizace 4592441](https://download.microsoft.com/download/2/7/2/272ea75f-1657-43ce-a7a4-a17d51463a94/4592441.csv).

   > [!NOTE]
   > Některé soubory mají ve sloupci "verze souboru CSV" hodnotu "nelze použít". To může vést k falešně pozitivním nebo falešně negativním hodnotám při použití nástrojů pro detekci kontroly třetích stran k ověření sestavení.

## <a name="november-23-2020-preview-update-kb4586852"></a>23. listopadu 2020 aktualizace Preview (KB4586852)

Tato aktualizace zahrnuje vylepšení a opravy pro Public Preview vydání Azure Stack HCI. 

   > [!IMPORTANT]
   > Pokud Azure Stack jste nakonfigurovali a zaregistrovali clustery HCI pomocí Public Preview Image, musíte po instalaci aktualizace KB4586852 opravit registraci Azure, aby bylo možné používat nové funkce, které aktualizace nabízí. Po instalaci aktualizace postupujte podle těchto kroků u každého clusteru:
   >
   > 1. Zajistěte, aby byly všechny servery v clusteru aktualizované na KB4586852. Pokud ne, oprava se nezdaří a označí uzly, které je třeba aktualizovat.
   >
   > 2. Připojte se k jednomu z uzlů clusteru místně nebo pomocí `Enter-PSSession <server-name>`
   >
   > 3. Stáhněte registrační modul AzStackHCI v 0.4.1 (nebo novější) z Galerie prostředí PowerShell. Spusťte, `Install-Module -Name Az.StackHCI` abyste získali nejnovější modul.
   >
   > 4. Spusťte následující příkaz, který opraví registraci. Použijte ID předplatného, které se použilo k registraci clusteru původně. `Get-AzureStackHCI` zobrazuje aktuální Azure Resource Manager identifikátor URI, který obsahuje informace o předplatném.
   >
   >   ```PowerShell
   >   Register-AzStackHCI -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -RepairRegistration
   >   ```
   > 

### <a name="improvements-and-fixes"></a>Vylepšení a opravy
Tato aktualizace bez zabezpečení zahrnuje vylepšení kvality. Mezi klíčové změny patří: 

- V této aktualizaci Azure Stack zákazníci se systémem HCI, kteří uchovávají platné licence na Windows Server 2019 Datacenter Edition, je můžou používat k pohodlné aktivaci virtuálních počítačů hostovaných na Azure Stack HCI, aniž by museli spravovat kódy Product Key pro každý jednotlivý virtuální počítač. Konkrétně můžete použít Centrum pro správu Windows nebo PowerShell k zadání nepoužívaného aktivačního klíče Windows serveru 2019 Datacenter Edition přímo do Azure Stack hostitele HCI, který umožní automatickou aktivaci virtuálního počítače (AVMA). Virtuální počítače se systémem Windows Server 2019 nebo starším pak mohou dědit aktivaci z hostitele. Vydaný klíč může být pomocí klíče k vícenásobné aktivaci, který se získal z multilicenčního střediska, a klíč vytištěný na nálepce certifikátu pravosti (COA), který se použije pro server výrobce OEM, nebo klíč z maloobchodních krabicových kopií Windows serveru 2019 Datacenter Edition. V této verzi nejsou podporované GVLK (Generic Volume License Key).

- Azure Stack HCL nyní shromažďuje požadovaná diagnostická data, což jsou minimální data potřebná k zajištění zabezpečení zařízení, aktuální aktuálnost a provádění podle očekávání. Požadovaná diagnostická data shromažďují omezené množství dat, která jsou kritická pro porozumění zařízení a jeho konfiguraci. Tato data pomáhají identifikovat problémy, ke kterým může dojít při konkrétní konfiguraci hardwaru nebo softwaru.  

### <a name="known-issues-in-this-update"></a>Známé problémy v této aktualizaci
Společnost Microsoft v současné době neví o problémech s touto aktualizací.

### <a name="how-to-get-this-update"></a>Jak získat tuto aktualizaci 
23. listopadu 2020 se aktualizace zabezpečení (KB4586852) pro [Azure Stack HCI Preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) doručuje prostřednictvím web Windows Update. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1381), si můžete stáhnout z [informací o souboru kumulativní aktualizace 4586852](https://download.microsoft.com/download/5/c/6/5c6f8c37-3e0b-4239-a6d9-9c709e18e869/4586852.csv).

   > [!NOTE]
   > Některé soubory mají ve sloupci "verze souboru CSV" hodnotu "nelze použít". To může vést k falešně pozitivním nebo falešně negativním hodnotám při použití nástrojů pro detekci kontroly třetích stran k ověření sestavení.

## <a name="november-10-2020-security-update-kb4586811"></a>10. listopadu 2020 aktualizace zabezpečení (KB4586811)

Tato aktualizace zahrnuje vylepšení a opravy pro Public Preview vydání Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Vylepšení a opravy
Tato aktualizace obsahuje různá vylepšení zabezpečení vnitřních funkcí operačního systému. Řeší chybu zabezpečení tím, že brání aplikacím, které běží jako systémový účet, z tisku na místní porty, které odkazují na soubor. Selhání protokolu tiskových úloh – chyba 50, "požadavek není podporován" v ID události 372 v protokolu událostí PrintService\Admin. Aby bylo možné tento problém vyřešit v budoucnu, ujistěte se, že vaše aplikace nebo služby běží jako konkrétní účet uživatele nebo služby.

Pro tuto verzi nebyly zdokumentovány žádné další problémy.

Další informace o vyřešených chybách zabezpečení najdete v [příručce k aktualizaci zabezpečení](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Známé problémy v této aktualizaci
Společnost Microsoft v současné době neví o problémech s touto aktualizací.

### <a name="how-to-get-this-update"></a>Jak získat tuto aktualizaci
10. listopadu 2020 aktualizace zabezpečení (KB4586811) pro [Azure Stack HCL](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se doručuje prostřednictvím web Windows Update. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1345), si můžete stáhnout z [informací o souboru kumulativní aktualizace 4586811](https://download.microsoft.com/download/8/f/2/8f2ce4bb-e113-4abc-b3ff-f0f4c4c71403/4586811.csv).

   > [!NOTE]
   > Některé soubory mají ve sloupci "verze souboru CSV" hodnotu "nelze použít". To může vést k falešně pozitivním nebo falešně negativním hodnotám při použití nástrojů pro detekci kontroly třetích stran k ověření sestavení.

## <a name="november-10-2020-servicing-stack-update-kb4590242"></a>10. listopadu 2020 aktualizace servisního zásobníku (KB4590242)

Tato aktualizace zahrnuje vylepšení kvality Public Preview vydání Azure Stack HCL.

### <a name="improvements-and-fixes"></a>Vylepšení a opravy
Tato aktualizace přináší vylepšení kvality do zásobníku pro údržbu, což je součást, která instaluje aktualizace. Aktualizace cestou nadřazené (Servicing) zajistí, že máte robustní a spolehlivý zásobník pro údržbu, aby vaše zařízení mohla přijímat a instalovat aktualizace od Microsoftu.

### <a name="how-to-get-this-update"></a>Jak získat tuto aktualizaci
Aktualizace KB4590242 (Údržba Stack 2020) pro [Azure Stack HCL](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se doručuje prostřednictvím web Windows Update. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="restart-information"></a>Informace o restartování
Po instalaci této aktualizace nemusíte restartovat počítač.

### <a name="removal-information"></a>Informace o odebrání
Aktualizace zásobníku pro obsluhu (SSUs) provádějí změny, jak jsou aktualizace nainstalované a ze zařízení je nelze odinstalovat.

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1342), si můžete stáhnout z [informací o souboru kumulativní aktualizace 4590242](https://download.microsoft.com/download/b/b/4/bb4fb4f5-c0ba-4e55-bada-d72310857982/4590242.csv).

### <a name="references"></a>Odkazy

Informace o SSUs najdete v následujících článcích:

- [Aktualizace zásobníku údržby](/windows/deployment/update/servicing-stack-updates)
- [Aktualizace zásobníku pro obsluhu (cestou nadřazené): nejčastější dotazy](https://support.microsoft.com/help/4535697)

Seznamte se s [terminologií](https://support.microsoft.com/help/824684) , kterou Microsoft používá k popisu aktualizací softwaru.

## <a name="october-20-2020-preview-update-kb4580388"></a>20. října 2020, aktualizace Preview (KB4580388)

Tato aktualizace zahrnuje vylepšení a opravy pro Public Preview vydání Azure Stack HCI.

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
20. října 2020 aktualizace zabezpečení (KB4580388) pro [Azure Stack HCL](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se doručuje prostřednictvím web Windows Update. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1321), si můžete stáhnout z [informací o souboru kumulativní aktualizace 4580388](https://download.microsoft.com/download/2/f/b/2fb766d3-c4c8-4279-8718-8efbd0b6f211/4580388.csv).

   > [!NOTE]
   > Některé soubory mají ve sloupci "verze souboru CSV" hodnotu "nelze použít". To může vést k falešně pozitivním nebo falešně negativním hodnotám při použití nástrojů pro detekci kontroly třetích stran k ověření sestavení.

## <a name="october-13-2020-security-update-kb4580363"></a>13. října 2020 aktualizace zabezpečení (KB4580363)

Tato aktualizace zahrnuje vylepšení a opravy pro Public Preview vydání Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Vylepšení a opravy
Tato aktualizace obsahuje různá vylepšení zabezpečení vnitřních funkcí operačního systému. Pro tuto verzi nebyly zdokumentovány žádné další problémy.

Další informace o vyřešených chybách zabezpečení najdete v [příručce k aktualizaci zabezpečení](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Známé problémy v této aktualizaci
Společnost Microsoft v současné době neví o problémech s touto aktualizací.

### <a name="how-to-get-this-update"></a>Jak získat tuto aktualizaci
Aktualizace zabezpečení (KB4580363 [Azure Stack](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) ) od 13. října 2020 je poskytována prostřednictvím web Windows Update. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1288), si můžete stáhnout z [informací o souboru kumulativní aktualizace 4580363](https://download.microsoft.com/download/f/7/8/f78801f0-e7e5-4a3d-a971-f642ccd24ee4/4580363.csv).

   > [!NOTE]
   > Některé soubory mají ve sloupci "verze souboru CSV" hodnotu "nelze použít". To může vést k falešně pozitivním nebo falešně negativním hodnotám při použití nástrojů pro detekci kontroly třetích stran k ověření sestavení.

## <a name="october-13-2020-servicing-stack-update-kb4583287"></a>13. října 2020 aktualizace sady Servicing (KB4583287)

Tato aktualizace zahrnuje vylepšení kvality Public Preview vydání Azure Stack HCL.

### <a name="improvements-and-fixes"></a>Vylepšení a opravy
Tato aktualizace přináší vylepšení kvality do zásobníku pro údržbu, což je součást, která instaluje aktualizace. Aktualizace cestou nadřazené (Servicing) zajistí, že máte robustní a spolehlivý zásobník pro údržbu, aby vaše zařízení mohla přijímat a instalovat aktualizace od Microsoftu.

### <a name="how-to-get-this-update"></a>Jak získat tuto aktualizaci
Aktualizace KB4583287 (Servicing [Azure Stack](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) ) od 13. října 2020 se doručuje prostřednictvím web Windows Update. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="restart-information"></a>Informace o restartování
Po instalaci této aktualizace nemusíte restartovat počítač.

### <a name="removal-information"></a>Informace o odebrání
Aktualizace zásobníku pro obsluhu (SSUs) provádějí změny, jak jsou aktualizace nainstalované a ze zařízení je nelze odinstalovat.

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1287), si můžete stáhnout z [informací o souboru kumulativní aktualizace 4583287](https://download.microsoft.com/download/b/8/5/b85160fb-85d9-49f9-b9d5-7dbc0158a944/4583287.csv).

### <a name="references"></a>Odkazy

Informace o SSUs najdete v následujících článcích:

- [Aktualizace zásobníku údržby](/windows/deployment/update/servicing-stack-updates)
- [Aktualizace zásobníku pro obsluhu (cestou nadřazené): nejčastější dotazy](https://support.microsoft.com/help/4535697)

Seznamte se s [terminologií](https://support.microsoft.com/help/824684) , kterou Microsoft používá k popisu aktualizací softwaru.

## <a name="september-17-2020-preview-update-kb4577629"></a>17. září 2020 – aktualizace Preview (KB4577629)

Tato aktualizace zahrnuje vylepšení a opravy pro Public Preview vydání Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Vylepšení a opravy
Tato nezabezpečená aktualizace zahrnuje vylepšení kvality. Mezi klíčové změny patří:
- Vyřešili jsme problém, kdy se provoz softwaru Load Balancer (SLB) procházející průchozím Multiplexorem může přesměrovat na jiného hostitele, který může způsobit selhání připojení aplikace.

### <a name="known-issues-in-this-update"></a>Známé problémy v této aktualizaci
Společnost Microsoft v současné době neví o problémech s touto aktualizací.

### <a name="how-to-get-this-update"></a>Jak získat tuto aktualizaci
Aktualizace zabezpečení (KB4577629) pro [Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se doručí prostřednictvím web Windows Update. září 2020. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1259), si můžete stáhnout z [informací o souboru pro kumulativní aktualizaci 4577629](https://download.microsoft.com/download/9/1/a/91addcbb-2b36-408c-ab88-736de42edb98/4577629.csv) .

   > [!NOTE]
   > Některé soubory mají ve sloupci "verze souboru CSV" hodnotu "nelze použít". To může vést k falešně pozitivním nebo falešně negativním hodnotám při použití nástrojů pro detekci kontroly třetích stran k ověření sestavení.

## <a name="september-8-2020-security-update-kb4577470"></a>8. září 2020 aktualizace zabezpečení (KB4577470)

Tato aktualizace zahrnuje vylepšení a opravy pro Public Preview vydání Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Vylepšení a opravy
Tato aktualizace obsahuje různá vylepšení zabezpečení vnitřních funkcí operačního systému. Pro tuto verzi nebyly zdokumentovány žádné další problémy.

Další informace o vyřešených chybách zabezpečení najdete v [příručce k aktualizaci zabezpečení](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Známé problémy v této aktualizaci
Společnost Microsoft v současné době neví o problémech s touto aktualizací.

### <a name="how-to-get-this-update"></a>Jak získat tuto aktualizaci
Aktualizace zabezpečení (KB4577470) pro [Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se doručí prostřednictvím web Windows Update. září 2020. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1226), si můžete stáhnout z [informací o souboru kumulativní aktualizace 4577470](https://download.microsoft.com/download/3/c/4/3c468525-5867-4cc3-8d34-dba88989adab/4577470.csv).

   > [!NOTE]
   > Některé soubory mají ve sloupci "verze souboru CSV" hodnotu "nelze použít". To může vést k falešně pozitivním nebo falešně negativním hodnotám při použití nástrojů pro detekci kontroly třetích stran k ověření sestavení.

## <a name="september-8-2020-servicing-stack-update-kb4577558"></a>8. září 2020 aktualizace servisního zásobníku (KB4577558)

Tato aktualizace zahrnuje vylepšení kvality Public Preview vydání Azure Stack HCL.

### <a name="improvements-and-fixes"></a>Vylepšení a opravy
Tato aktualizace přináší vylepšení kvality do zásobníku pro údržbu, což je součást, která instaluje aktualizace. Aktualizace cestou nadřazené (Servicing) zajistí, že máte robustní a spolehlivý zásobník pro údržbu, aby vaše zařízení mohla přijímat a instalovat aktualizace od Microsoftu.

### <a name="how-to-get-this-update"></a>Jak získat tuto aktualizaci
Aktualizace KB4577558 (Údržba Stack 2020) pro [Azure Stack HCL](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) se doručuje prostřednictvím web Windows Update. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="restart-information"></a>Informace o restartování 
Po instalaci této aktualizace nemusíte restartovat počítač.

### <a name="removal-information"></a>Informace o odebrání
Aktualizace zásobníku pro obsluhu (SSUs) provádějí změny, jak jsou aktualizace nainstalované a ze zařízení je nelze odinstalovat.

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1220), si můžete stáhnout z [informací o souboru kumulativní aktualizace 4577558](https://download.microsoft.com/download/8/f/6/8f612a9b-cb4e-4832-9397-156760848592/4577558.csv).

### <a name="references"></a>Odkazy

Informace o SSUs najdete v následujících článcích:

- [Aktualizace zásobníku údržby](/windows/deployment/update/servicing-stack-updates)
- [Aktualizace zásobníku pro obsluhu (cestou nadřazené): nejčastější dotazy](https://support.microsoft.com/help/4535697)

Seznamte se s [terminologií](https://support.microsoft.com/help/824684) , kterou Microsoft používá k popisu aktualizací softwaru.

## <a name="august-11-2020-security-update-kb4574585"></a>11. srpna 2020, aktualizace zabezpečení (KB4574585)

Tato aktualizace zahrnuje vylepšení a opravy pro Public Preview vydání Azure Stack HCI.

### <a name="improvements-and-fixes"></a>Vylepšení a opravy
Tato aktualizace obsahuje různá vylepšení zabezpečení vnitřních funkcí operačního systému. Pro tuto verzi nebyly zdokumentovány žádné další problémy.

Další informace o vyřešených chybách zabezpečení najdete v [příručce k aktualizaci zabezpečení](https://portal.msrc.microsoft.com/security-guidance).

### <a name="known-issues-in-this-update"></a>Známé problémy v této aktualizaci
Společnost Microsoft v současné době neví o problémech s touto aktualizací.

### <a name="how-to-get-this-update"></a>Jak získat tuto aktualizaci
Aktualizace zabezpečení (KB4574585 [Azure Stack](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) ) 11. srpna 2020 je poskytována prostřednictvím web Windows Update. Pokud ho chcete nainstalovat do clusteru Azure Stack HCI, přečtěte si téma [aktualizace Azure Stackch clusterů HCI](manage/update-cluster.md).

### <a name="file-information"></a>Informace o souboru
Seznam souborů, které jsou k dispozici v této aktualizaci (OS Build 17784,1162), si můžete stáhnout z [informací o souboru kumulativní aktualizace 4574585](https://download.microsoft.com/download/7/f/4/7f451def-76c5-4cc0-9929-0c5efeb27d2f/4574585.csv).

   > [!NOTE]
   > Některé soubory mají ve sloupci "verze souboru CSV" hodnotu "nelze použít". To může vést k falešně pozitivním nebo falešně negativním hodnotám při použití nástrojů pro detekci kontroly třetích stran k ověření sestavení.
