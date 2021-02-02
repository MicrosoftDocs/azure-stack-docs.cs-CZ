---
title: Spuštění virtuálního počítače s Windows na rozbočovači Azure Stack
description: Přečtěte si, jak spustit virtuální počítač s Windows na rozbočovači Azure Stack.
author: mattbriggs
ms.topic: how-to
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 3/9/2020
ms.openlocfilehash: 5bbad22a6a0998cd56fd3fefc309c14f665b2faa
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247536"
---
# <a name="run-a-windows-virtual-machine-on-azure-stack-hub"></a>Spuštění virtuálního počítače s Windows na rozbočovači Azure Stack

Zřizování virtuálního počítače v Azure Stackovém centru vyžaduje některé další součásti kromě samotného virtuálního počítače, včetně prostředků sítě a úložiště. Tento článek popisuje osvědčené postupy pro spuštění virtuálního počítače s Windows v Azure.

![Architektura pro virtuální počítač s Windows na rozbočovači Azure Stack](./media/iaas-architecture-vm-windows/image1.png)

## <a name="resource-group"></a>Skupina prostředků

[Skupina prostředků](/azure/azure-resource-manager/resource-group-overview) je logický kontejner, který obsahuje související prostředky centra Azure Stack. Obecně je potřeba seskupit prostředky na základě jejich životního cyklu a spravovat je.

Do jedné [skupiny prostředků](/azure/azure-resource-manager/resource-group-overview) byste měli umístit úzce související prostředky, které mají stejný životní cyklus. Skupiny prostředků umožňují nasadit a monitorovat prostředky jako skupinu a sledovat fakturační náklady podle skupin prostředků. Prostředky můžete také odstranit jako sadu, což je užitečné pro testovací nasazení. Přiřaďte prostředkům smysluplné názvy a zjednodušte tak vyhledání konkrétních prostředků a pochopení jejich rolí. Další informace najdete v tématu [Doporučené zásady vytváření názvů pro prostředky Azure](/azure/architecture/best-practices/naming-conventions).

## <a name="virtual-machine"></a>Virtuální počítač

Virtuální počítač můžete zřídit ze seznamu publikovaných imagí nebo z vlastního spravovaného Image nebo souboru virtuálního pevného disku (VHD), který se nahrál do úložiště objektů BLOB centra Azure Stack.

Centrum Azure Stack nabízí různé velikosti virtuálních počítačů z Azure. Další informace najdete v tématu [velikosti pro virtuální počítače v centru Azure Stack](./azure-stack-vm-sizes.md). Pokud přesouváte existující úlohu do centra Azure Stack, začněte s velikostí virtuálního počítače, která je nejvhodnější pro vaše místní servery nebo Azure. Pak změřte výkon vaší skutečné úlohy z hlediska využití procesoru, paměti a diskových vstupně-výstupních operací za sekundu (IOPS) a podle potřeby upravte velikost.

## <a name="disks"></a>Disky

Náklady závisí na kapacitě zřízeného disku. VSTUPNĚ-výstupní operace a propustnost (tj. přenos dat) závisí na velikosti virtuálního počítače, takže při zřizování disku Zvažte všechny tři faktory (kapacita, IOPS a propustnost).

Disk IOPS (vstupně-výstupní operace za sekundu) na rozbočovači Azure Stack je funkce [velikosti virtuálního počítače](./azure-stack-vm-sizes.md) místo typu disku. To znamená, že pro virtuální počítač s Standard_Fs Series bez ohledu na to, jestli pro daný typ disku zvolíte SSD nebo HDD, je limit IOPS pro jeden další datový disk 2300 IOPS. Stanovený limit IOPS je limit (maximální možný), aby se zabránilo sousedním sousedům. Nejedná se o záruku za IOPS, kterou získáte na konkrétní velikosti virtuálního počítače.

Doporučujeme také použít [Managed disks](./azure-stack-managed-disk-considerations.md). Spravované disky zjednodušují správu disků tím, že vám úložiště vycházejí. Spravované disky nevyžadují účet úložiště. Jednoduše zadáte velikost a typ disku a disk se potom nasadí jako prostředek s vysokou dostupností.

Disk s operačním systémem je virtuální pevný disk uložený v Azure Stack úložiště objektů BLOB centra, takže zůstane i v případě, že je hostitelský počítač mimo provoz. Doporučujeme také vytvořit jeden nebo více [datových disků](./azure-stack-manage-vm-disks.md), což jsou trvalé virtuální pevné disky používané pro data aplikací. Pokud je to možné, instalujte aplikace na datový disk, nikoli na disk operačního systému. U některých starších aplikací může být potřeba nainstalovat komponenty na jednotku C:; v takovém případě můžete s použitím PowerShellu [změnit velikost disku operačního systému](/azure/virtual-machines/windows/expand-os-disk).

Virtuální počítač je také vytvořen s dočasným diskem (jednotka D: ve Windows). Tento disk je uložený na dočasném svazku v Azure Stack infrastruktury úložiště centra. Může se odstranit během restartování a dalších událostí životního cyklu virtuálního počítače. Tento disk používejte jenom pro dočasná data, jako jsou stránkovací nebo odkládací soubory.

## <a name="network"></a>Síť

Síťové komponenty zahrnují následující prostředky:

-   **Virtuální síť:** Každý virtuální počítač je nasazený do virtuální sítě, která se dá rozdělit do několika podsítí.

-   **Síťové rozhraní (nic)**. Síťové rozhraní umožňuje virtuálnímu počítači komunikovat s virtuální sítí. Pokud pro virtuální počítač potřebujete více síťových rozhraní, uvědomte si, že pro jednotlivé  [velikosti virtuálních počítačů](./azure-stack-vm-sizes.md)je definován maximální počet síťových adaptérů.

-   **Veřejná IP adresa/VIP**. Veřejná IP adresa je potřeba ke komunikaci s VIRTUÁLNÍm počítačem – například prostřednictvím vzdálené plochy (RDP). Veřejná IP adresa může být dynamická nebo statická. Ve výchozím nastavení je dynamická.

-   Rezervujte [STATICKOU IP](/azure/virtual-network/virtual-networks-reserved-public-ip) adresu, pokud potřebujete pevnou IP adresu, která se nemění, třeba pokud potřebujete vytvořit záznam DNS ' a ' nebo přidat IP adresu do seznamu bezpečných adres.

-   Pro IP adresu můžete také vytvořit plně kvalifikovaný název domény (FQDN). Potom můžete v DNS zaregistrovat [záznam CNAME](https://en.wikipedia.org/wiki/CNAME_record), který na tento plně kvalifikovaný název odkazuje. Další informace najdete v tématu [Vytvoření plně kvalifikovaného názvu domény v Azure Portal](/azure/virtual-machines/windows/portal-create-fqdn).

-   **Skupina zabezpečení sítě (NSG)**. Skupin zabezpečení sítě slouží k povolení nebo zamítnutí síťového provozu do virtuálních počítačů. Skupin zabezpečení sítě je možné přidružit buď k podsítím, nebo k jednotlivým instancím virtuálních počítačů.

Každá skupina zabezpečení sítě obsahuje sadu [výchozích pravidel](/azure/virtual-network/security-overview#default-security-rules), včetně pravidla, které blokuje veškerou příchozí internetovou komunikaci. Výchozí pravidla nejde odstranit, ale ostatní pravidla je mohou potlačit. Pokud chcete povolit internetovou komunikaci, vytvořte pravidla, která povolí příchozí provoz na konkrétní porty, například port 80 pro protokol HTTP. Pokud chcete povolit RDP, přidejte pravidlo NSG, které povoluje příchozí přenosy na TCP port 3389.

## <a name="operations"></a>Operace

**Diagnostika**. Povolte monitorování a diagnostiku, včetně základních metrik stavu, diagnostických protokolů infrastruktury a  [diagnostiky spouštění](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Diagnostika spouštění vám pomůže zjistit chyby spouštění, pokud se virtuální počítač dostane do stavu, kdy ho nebude možné spustit. Vytvořte účet Azure Storage pro ukládání protokolů. Pro diagnostické protokoly stačí standardní účet místně redundantního úložiště (LRS). Další informace najdete v tématu [Povolení monitorování a diagnostiky](./azure-stack-metrics-azure-data.md).

**Dostupnost**. Váš virtuální počítač může být vystavený restartováním z důvodu plánované údržby, která je naplánována operátorem centra Azure Stack. Pro zajištění vysoké dostupnosti produkčního systému více virtuálních počítačů v Azure jsou virtuální počítače umístěné ve [skupině dostupnosti](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) , která je rozšíří napříč více doménami selhání a aktualizačními doménami. V menším měřítku centra Azure Stack se doména selhání ve skupině dostupnosti definuje jako jeden uzel v jednotce škálování.  

I když je infrastruktura centra Azure Stack už odolná vůči selháním, pak základní technologie (Clustering s podporou převzetí služeb při selhání) stále způsobí nějaké výpadky virtuálních počítačů na ovlivněném fyzickém serveru, pokud dojde k selhání hardwaru. Centrum Azure Stack podporuje skupinu dostupnosti s maximálním počtem tří domén selhání pro zajištění konzistence s Azure.

|                   |             |
|-------------------|-------------|
| **Domény selhání** | Virtuální počítače, které jsou umístěné ve skupině dostupnosti, se fyzicky izolují tak, že je rozšíříte tak, jak je to možné, do více domén selhání (Azure Stack uzly centra). Pokud dojde k selhání hardwaru, virtuální počítače z neúspěšné domény selhání se restartují v jiných doménách selhání. Budou se uchovávat v samostatných doménách selhání z ostatních virtuálních počítačů, ale ve stejné skupině dostupnosti, pokud je to možné. Když se hardware vrátí zpátky do online režimu, virtuální počítače se znovu vyrovnávají, aby se zachovala vysoká dostupnost. |
| **Aktualizační domény**| Aktualizační domény představují jiný způsob, jakým Azure poskytuje vysokou dostupnost ve skupinách dostupnosti. Aktualizační doména je logická skupina základního hardwaru, která může prostoupit v rámci údržby. Virtuální počítače umístěné ve stejné aktualizační doméně se při plánované údržbě restartují společně. Když klienti vytvářejí virtuální počítače v rámci skupiny dostupnosti, platforma Azure automaticky distribuuje virtuální počítače napříč těmito aktualizačními doménami. <br>V Azure Stackovém centru jsou virtuální počítače za provozu přenášeny v jiných online hostitelích v clusteru před tím, než se aktualizuje jejich podkladový hostitel. Vzhledem k tomu, že během aktualizace hostitele nedochází k výpadku tenanta, funkce aktualizační doména v centru Azure Stack existuje pouze pro kompatibilitu šablon s Azure. Virtuální počítače ve skupině dostupnosti budou na portálu zobrazeny jako číslo aktualizační domény. |

**Zálohy** Doporučení k ochraně virtuálních počítačů IaaS ve službě Azure Stack hub najdete [v referenční ochraně virtuálních počítačů nasazených v centru Azure Stack](azure-stack-manage-vm-protect.md).

**Zastavuje se virtuální počítač**. Azure rozlišuje mezi tím, když je virtuální počítač zastavený a když má zrušené přidělení. Když se virtuální počítač v zastaveném stavu, fakturuje se vám, ale když má zrušené přidělení, tak ne. Na portálu Azure Stack hub zruší přidělení virtuálního počítače tlačítkem **zastavit** . Pokud virtuální počítač vypnete pomocí operačního systému, když jste přihlášení, zastaví se, ale **neuvolní**, takže se vám bude nadále účtovat.

**Odstraňuje se virtuální počítač**. Pokud odstraníte virtuální počítač, disky virtuálních počítačů se neodstraní. To znamená, že virtuální počítač můžete bezpečně odstranit bez obav ze ztráty dat. Bude se vám ale účtovat poplatek za úložiště. Disk virtuálního počítače odstraníte tak, že odstraníte objekt spravovaného disku. Pokud chcete zabránit neúmyslnému odstranění, použijte *zámek prostředku* a uzamkněte celou skupinu prostředků nebo jenom vybrané prostředky, jako je třeba virtuální počítač.

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Připojte virtuální počítače k [Azure Security Center](/azure/security-center/quick-onboard-azure-stack) , abyste získali centrální pohled na stav zabezpečení vašich prostředků Azure. Security Center monitoruje potenciální potíže se zabezpečením a poskytuje ucelený přehled o stavu zabezpečení vašeho nasazení. Služba Security Center se konfiguruje na základě předplatného Azure. Povolte shromažďování dat zabezpečení, jak je popsáno v tématu připojení [předplatného Azure k Security Center Standard](/azure/security-center/security-center-get-started). Když je povolené shromažďování dat, Security Center automaticky prohledává všechny virtuální počítače vytvořené v rámci příslušného předplatného.

**Správa oprav**. Informace o konfiguraci správy oprav na VIRTUÁLNÍm počítači najdete v [tomto](./vm-update-management.md) článku. Pokud je povolené centrum Security Center, kontroluje, jestli nechybí žádné aktualizace zabezpečení a důležité aktualizace. K povolení automatických aktualizací systému použijte [nastavení zásad skupiny](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates) na virtuálním počítači.

**Antimalwarový software**. Pokud je povolené centrum Security Center, kontroluje, jestli je nainstalovaný software ochrany před malwarem. Security Center můžete použít také k instalaci antimalwarového softwaru z portálu Azure Portal.

**Řízení přístupu**. K řízení přístupu k prostředkům Azure použijte [řízení přístupu na základě role (RBAC)](/azure/active-directory/role-based-access-control-what-is) . RBAC umožňuje přiřazovat autorizační role jednotlivým členům vaše týmu DevOps. Třeba role čtenáře může zobrazovat prostředky Azure, ale nemůže je vytvářet, spravovat ani odstraňovat. Některá oprávnění jsou specifická pro typ prostředku Azure. Třeba role Přispěvatel virtuálních počítačů může restartovat nebo zrušit přidělení virtuálního počítače, resetovat heslo správce, vytvořit nový virtuální počítač a tak dále. Mezi další [předdefinované role RBAC](/azure/active-directory/role-based-access-built-in-roles), které můžou být užitečné pro tuto referenční architekturu, patří role [Uživatel služby DevTest Labs](/azure/active-directory/role-based-access-built-in-roles#devtest-labs-user) a [Přispěvatel sítě](/azure/active-directory/role-based-access-built-in-roles#network-contributor).

> [!NOTE]  
> RBAC neomezuje akce, které může uživatel přihlášený k virtuálnímu počítači provést. Tato oprávnění určuje typ účtu v hostovaném operačním systému.

**Protokoly auditu**. Pomocí [protokolů aktivit](./azure-stack-metrics-azure-data.md?#activity-log) můžete zobrazit akce zřizování a další události virtuálních počítačů.

**Šifrování dat:** Azure Stack hub používá šifrování AES 128-bit AES k ochraně dat uživatelů a infrastruktury v klidovém subsystému úložiště. Další informace najdete v tématu [šifrování dat v klidovém umístění v Azure Stackovém centru](../operator/azure-stack-security-bitlocker.md).


## <a name="next-steps"></a>Další kroky

- Další informace o virtuálních počítačích centra Azure Stack najdete v tématu [funkce virtuálních počítačů centra Azure Stack](azure-stack-vm-considerations.md).  
- Další informace o vzorech cloudu Azure najdete v tématu [vzory návrhu cloudu](/azure/architecture/patterns).
