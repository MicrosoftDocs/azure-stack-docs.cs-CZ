---
title: Spuštění virtuálního počítače se systémem Linux v centru Azure Stack
description: Přečtěte si, jak spustit virtuální počítač se systémem Linux v centru Azure Stack.
author: mattbriggs
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: 91c312bd3709dd4117b7f371c1fd2cd49822331a
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81659915"
---
# <a name="run-a-linux-virtual-machine-on-azure-stack-hub"></a>Spuštění virtuálního počítače se systémem Linux v centru Azure Stack

Zřizování virtuálního počítače v Azure Stackovém centru, jako je Azure, vyžaduje některé další komponenty kromě samotného virtuálního počítače, včetně síťových prostředků a prostředků úložiště. Tento článek popisuje osvědčené postupy pro spuštění virtuálního počítače se systémem Linux v centru Azure Stack.

![Architektura pro virtuální počítač se systémem Linux v centru Azure Stack](./media/iaas-architecture-vm-linux/image1.png)

## <a name="resource-group"></a>Skupina prostředků

[Skupina prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) je logický kontejner, který obsahuje související prostředky centra Azure Stack. Obecně je potřeba seskupit prostředky na základě jejich životního cyklu a spravovat je.

Do jedné [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) byste měli umístit úzce související prostředky, které mají stejný životní cyklus. Skupiny prostředků umožňují nasadit a monitorovat prostředky jako skupinu a sledovat fakturační náklady podle skupin prostředků. Prostředky můžete také odstranit jako sadu, což je užitečné pro testovací nasazení. Přiřaďte prostředkům smysluplné názvy a zjednodušte tak vyhledání konkrétních prostředků a pochopení jejich rolí. Další informace najdete v tématu [Doporučené zásady vytváření názvů pro prostředky Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="virtual-machine"></a>Virtuální počítač

Virtuální počítač můžete zřídit ze seznamu publikovaných imagí nebo z vlastního spravovaného Image nebo souboru virtuálního pevného disku (VHD), který se nahrál do úložiště objektů BLOB centra Azure Stack. Centrum Azure Stack podporuje spouštění různých oblíbených distribucí systému Linux, včetně CentOS, Debian, Red Hat Enterprise, Ubuntu a SUSE. Další informace najdete v tématu [Linux v centru Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-linux). Můžete se také rozhodnout, že budete publikovat jednu z publikovaných imagí Linux, které jsou k dispozici na webu centra Azure Stack.

Centrum Azure Stack nabízí různé velikosti virtuálních počítačů z Azure. Další informace najdete v tématu [velikosti pro virtuální počítače v centru Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes). Pokud přesouváte existující úlohu do centra Azure Stack, začněte s velikostí virtuálního počítače, která je nejvhodnější pro vaše místní servery nebo Azure. Pak změřte výkon vaší skutečné úlohy z hlediska využití procesoru, paměti a diskových vstupně-výstupních operací za sekundu (IOPS) a podle potřeby upravte velikost.

## <a name="disks"></a>Disky

Náklady závisí na kapacitě zřízeného disku. VSTUPNĚ-výstupní operace a propustnost (tj. přenos dat) závisí na velikosti virtuálního počítače, takže při zřizování disku Zvažte všechny tři faktory (kapacita, IOPS a propustnost).

Disk IOPS (vstupně-výstupní operace za sekundu) na rozbočovači Azure Stack je funkce [velikosti virtuálního počítače](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes) místo typu disku. To znamená, že pro virtuální počítač s Standard_Fs Series bez ohledu na to, jestli pro daný typ disku zvolíte SSD nebo HDD, je limit IOPS pro jeden další datový disk 2300 IOPS. Stanovený limit IOPS je limit (maximální možný), aby se zabránilo sousedním sousedům. Nejedná se o záruku za IOPS, kterou získáte na konkrétní velikosti virtuálního počítače.

Doporučujeme také použít [Managed disks](https://docs.microsoft.com/azure-stack/user/azure-stack-managed-disk-considerations). Spravované disky zjednodušují správu disků tím, že vám úložiště vycházejí. Spravované disky nevyžadují účet úložiště. Jednoduše zadáte velikost a typ disku a disk se potom nasadí jako prostředek s vysokou dostupností.

Disk s operačním systémem je virtuální pevný disk uložený v [Azure Stack úložiště centra](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-overview), takže zůstane i v případě, že je hostitelský počítač mimo provoz. Pro virtuální počítače se systémem Linux je disk s operačním systémem/dev/sda1.. Doporučujeme také vytvořit jeden nebo více [datových disků](https://docs.microsoft.com/azure-stack/user/azure-stack-manage-vm-disks), což jsou trvalé virtuální pevné disky používané pro data aplikací.

Když vytvoříte virtuální pevný disk, je neformátovaný. Přihlaste se k virtuálnímu počítači a disk naformátujte. V prostředí Linux se datové disky zobrazují jako/dev/sdc,/dev/SDD a tak dále. Můžete spustit lsblk a zobrazit tak seznam blokovaných zařízení, včetně disků. Pokud chcete použít datový disk, vytvořte oddíl a souborový systém a potom disk připojte. Příklad:

```bash
# Create a partition.
sudo fdisk /dev/sdc \# Enter 'n' to partition, 'w' to write the change.

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

Když přidáte datový disk, přiřadí se mu ID logické jednotky (LUN). Volitelně můžete zadat ID logické jednotky (LUN), například Pokud nahrazujete disk a chcete zachovat stejné ID logické jednotky (LUN), nebo máte aplikaci, která hledá konkrétní ID logické jednotky (LUN). Nezapomeňte ale, že hodnota LUN ID musí být pro každý disk jedinečná.

Virtuální počítač se vytvoří s dočasným diskem. Tento disk je uložený na dočasném svazku Azure Stack infrastruktury úložiště centra. Může se odstranit během restartování a dalších událostí životního cyklu virtuálního počítače. Tento disk používejte jenom pro dočasná data, jako jsou stránkovací nebo odkládací soubory. Pro virtuální počítače se systémem Linux je dočasný disk adresář/dev/sdb1 a je připojen k/mnt/Resource nebo/mnt.

## <a name="network"></a>Síť

Síťové komponenty zahrnují následující prostředky:

-   **Virtuální síť**. Každý virtuální počítač je nasazený do virtuální sítě, která se dá rozdělit do několika podsítí.

-   **Síťové rozhraní (NIC):** Síťové rozhraní umožňuje virtuálnímu počítači komunikovat s virtuální sítí. Pokud pro virtuální počítač potřebujete více síťových rozhraní, uvědomte si, že pro jednotlivé [velikosti virtuálních počítačů](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)je definován maximální počet síťových adaptérů.

-   **Veřejná IP adresa/VIP**. Veřejná IP adresa je potřeba ke komunikaci s VIRTUÁLNÍm počítačem – například prostřednictvím vzdálené plochy (RDP). Veřejná IP adresa může být dynamická nebo statická. Ve výchozím nastavení je dynamická. Pokud pro virtuální počítač potřebujete více síťových rozhraní, uvědomte si, že pro jednotlivé [velikosti virtuálních počítačů](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)je definován maximální počet síťových adaptérů.

-   Pro IP adresu můžete také vytvořit plně kvalifikovaný název domény (FQDN). Potom můžete v DNS zaregistrovat [záznam CNAME](https://en.wikipedia.org/wiki/CNAME_record), který na tento plně kvalifikovaný název odkazuje. Další informace najdete v tématu [Vytvoření plně kvalifikovaného názvu domény v Azure Portal](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-portal-create-fqdn).

-   **Skupina zabezpečení sítě (NSG).** Skupiny zabezpečení sítě slouží k povolení nebo zamítnutí síťového provozu do virtuálních počítačů. Skupin zabezpečení sítě je možné přidružit buď k podsítím, nebo k jednotlivým instancím virtuálních počítačů.

Každá skupina zabezpečení sítě obsahuje sadu [výchozích pravidel](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules), včetně pravidla, které blokuje veškerou příchozí internetovou komunikaci. Výchozí pravidla nejde odstranit, ale ostatní pravidla je mohou potlačit. Pokud chcete povolit internetovou komunikaci, vytvořte pravidla, která povolí příchozí provoz na konkrétní porty, například port 80 pro protokol HTTP. Pokud chcete povolit SSH, přidejte pravidlo NSG, které povoluje příchozí přenosy na port TCP 22.

## <a name="operations"></a>Operace

**SSH**. Než vytvoříte virtuální počítač s Linuxem, vygenerujte pár klíčů (veřejný a privátní) pomocí 2048bitového algoritmu RSA. Soubor veřejného klíče potom použijte při vytváření virtuálního počítače. Další informace najdete v tématu [Použití SSH se systémem Linux v Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys).

**Diagnostika**. Povolte monitorování a diagnostiku, včetně základních metrik stavu, diagnostických protokolů infrastruktury a [diagnostiky spouštění](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Diagnostika spouštění vám pomůže zjistit chyby spouštění, pokud se virtuální počítač dostane do stavu, kdy ho nebude možné spustit. Vytvořte účet Azure Storage pro ukládání protokolů. Pro diagnostické protokoly stačí standardní účet místně redundantního úložiště (LRS). Další informace najdete v tématu [Povolení monitorování a diagnostiky](https://docs.microsoft.com/azure-stack/user/azure-stack-metrics-azure-data).

**Dostupnost**. Váš virtuální počítač může být vystavený restartováním z důvodu plánované údržby, která je naplánována operátorem centra Azure Stack. Pro zajištění vyšší dostupnosti nasaďte několik virtuálních počítačů ve [skupině dostupnosti](https://docs.microsoft.com/azure-stack/operator/app-service-deploy-ha).

**Zálohy** Doporučení k ochraně virtuálních počítačů IaaS ve službě Azure Stack hub najdete v [tomto](https://docs.microsoft.com/azure-stack/user/azure-stack-manage-vm-protect) článku.

**Zastavuje se virtuální počítač**. Azure rozlišuje mezi tím, když je virtuální počítač zastavený a když má zrušené přidělení. Když se virtuální počítač v zastaveném stavu, fakturuje se vám, ale když má zrušené přidělení, tak ne. Na portálu Azure Stack hub zruší přidělení virtuálního počítače tlačítkem **zastavit** . Pokud virtuální počítač vypnete pomocí operačního systému, když jste přihlášení, zastaví se, ale **neuvolní**, takže se vám bude nadále účtovat.

**Odstraňuje se virtuální počítač**. Pokud odstraníte virtuální počítač, disky virtuálních počítačů se neodstraní. To znamená, že virtuální počítač můžete bezpečně odstranit bez obav ze ztráty dat. Bude se vám ale účtovat poplatek za úložiště. Disk virtuálního počítače odstraníte tak, že odstraníte objekt spravovaného disku. Pokud chcete zabránit neúmyslnému odstranění, použijte [zámek prostředku](https://docs.microsoft.com/azure/resource-group-lock-resources) a uzamkněte celou skupinu prostředků nebo jenom vybrané prostředky, jako je třeba virtuální počítač.

## <a name="security-considerations"></a>Aspekty zabezpečení

Připojte virtuální počítače k [Azure Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack) , abyste získali centrální pohled na stav zabezpečení vašich prostředků Azure. Security Center monitoruje potenciální potíže se zabezpečením a poskytuje ucelený přehled o stavu zabezpečení vašeho nasazení. Služba Security Center se konfiguruje na základě předplatného Azure. Povolte shromažďování dat zabezpečení, jak je popsáno v tématu připojení [předplatného Azure k Security Center Standard](https://docs.microsoft.com/azure/security-center/security-center-get-started). Když je povolené shromažďování dat, Security Center automaticky prohledává všechny virtuální počítače vytvořené v rámci příslušného předplatného.

**Správa oprav**. Informace o konfiguraci správy oprav na VIRTUÁLNÍm počítači najdete v [tomto](https://docs.microsoft.com/azure-stack/user/vm-update-management) článku. Pokud je povolené centrum Security Center, kontroluje, jestli nechybí žádné aktualizace zabezpečení a důležité aktualizace. K povolení automatických aktualizací systému použijte [nastavení zásad skupiny](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates) na virtuálním počítači.

**Antimalwarový software**. Pokud je povolené centrum Security Center, kontroluje, jestli je nainstalovaný software ochrany před malwarem. Security Center můžete použít také k instalaci antimalwarového softwaru z portálu Azure Portal.

**Řízení přístupu**. K řízení přístupu k prostředkům Azure použijte [řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is) . RBAC umožňuje přiřazovat autorizační role jednotlivým členům vaše týmu DevOps. Třeba role čtenáře může zobrazovat prostředky Azure, ale nemůže je vytvářet, spravovat ani odstraňovat. Některá oprávnění jsou specifická pro typ prostředku Azure. Třeba role Přispěvatel virtuálních počítačů může restartovat nebo zrušit přidělení virtuálního počítače, resetovat heslo správce, vytvořit nový virtuální počítač a tak dále. Mezi další [předdefinované role RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles), které můžou být užitečné pro tuto referenční architekturu, patří role [Uživatel služby DevTest Labs](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#devtest-labs-user) a [Přispěvatel sítě](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#network-contributor).

> [!Note]  
> RBAC neomezuje akce, které může uživatel přihlášený k virtuálnímu počítači provést. Tato oprávnění určuje typ účtu v hostovaném operačním systému.

**Protokoly auditu**. Pomocí [protokolů aktivit](https://docs.microsoft.com/azure-stack/user/azure-stack-metrics-azure-data?#activity-log) můžete zobrazit akce zřizování a další události virtuálních počítačů.

**Šifrování dat**. Centrum Azure Stack chrání data uživatelů a infrastruktury na úrovni subsystému úložiště pomocí šifrování v klidovém prostředí. Subsystém úložiště Azure Stackového rozbočovače je šifrován pomocí nástroje BitLocker s 128 šifrováním AES. Další podrobnosti najdete v [tomto](https://docs.microsoft.com/azure-stack/operator/azure-stack-security-bitlocker) článku.

## <a name="next-steps"></a>Další kroky

- Další informace o virtuálních počítačích centra Azure Stack najdete v tématu [funkce virtuálních počítačů centra Azure Stack](azure-stack-vm-considerations.md).  
- Další informace o vzorech cloudu Azure najdete v tématu [vzory návrhu cloudu](https://docs.microsoft.com/azure/architecture/patterns).
