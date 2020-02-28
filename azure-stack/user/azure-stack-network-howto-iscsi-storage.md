---
title: Jak se připojit k úložišti iSCSI pomocí centra Azure Stack
description: Přečtěte si, jak se připojit k úložišti iSCSI pomocí centra Azure Stack.
author: mattbriggs
ms.topic: how-to
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 7451338194742723d6b669f94dc11b4449570eda
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703838"
---
# <a name="connect-to-iscsi-storage-with-azure-stack-hub"></a>Připojení k úložišti iSCSI pomocí centra Azure Stack

Pomocí šablony v tomto článku můžete připojit virtuální počítač centra Azure Stack k místnímu cíli iSCSI a nastavit virtuální počítač tak, aby používal úložiště hostované mimo naše Azure Stack centra a kdekoli ve vašem datovém centru. Tento článek se zabývá tím, že se jako cíl iSCSI používá počítač se systémem Windows.

Šablonu najdete v **lucidqdreams** rozvětvení úložiště GitHub ve [vzorcích Azure Intelligent Edge](https://github.com/lucidqdreams/azure-intelligent-edge-patterns) . Šablona se nachází ve složce **úložiště – iSCSI** . Šablona byla navržena tak, aby na straně centra Azure Stack nastavila infrastrukturu potřebnou k připojení k cíli iSCSI. To zahrnuje virtuální počítač, který bude sloužit jako iniciátor iSCSI, spolu s doprovodnou sítí VNet, NSG, PIP a Storage. Po nasazení šablony musí být spuštěny dva skripty prostředí PowerShell, aby se konfigurace dokončila. Jeden skript se spustí na místním virtuálním počítači (cíl) a jeden se spustí na virtuálním počítači centra Azure Stack (iniciátor). Po dokončení budete mít k VIRTUÁLNÍmu počítači centra Azure Stack přidat místní úložiště. 

## <a name="overview"></a>Přehled

Diagram zobrazuje virtuální počítač hostovaný v centru Azure Stack s připojeným diskem iSCSI z počítače s Windows místně (fyzický nebo virtuální) Azure Stack, což umožňuje, aby se externí úložiště připojilo k VIRTUÁLNÍmu počítači, který je hostitelem vašeho centra Azure Stack, přes protokol iSCSI.

![alternativní text](./media/azure-stack-network-howto-iscsi-storage/overview.png)

### <a name="requirements"></a>Požadavky

- Místní počítač (fyzický nebo virtuální) se systémem Windows Server 2016 Datacenter nebo Windows Server 2019 Datacenter.
- Požadované položky centra Azure Stack na webu Marketplace:
    -  Windows Server 2016 Datacenter nebo Windows Server 2019 Datacenter (doporučuje se nejnovější sestavení).
    -  Rozšíření PowerShell DSC
    -  Rozšíření vlastních skriptů.
    -  Existující virtuální nebo fyzický počítač. V ideálním případě bude mít tento počítač dva síťové adaptéry. Může to být také jiný cíl iSCSI, jako je například síť SAN pro instanci.

### <a name="things-to-consider"></a>Co je potřeba zvážit

- V podsíti šablony se použije skupina zabezpečení sítě. Projděte si tuto kontrolu a podle potřeby proveďte další příspěvky.
- Pravidlo zamítnutí protokolu RDP se aplikuje na NSG tunelu a bude potřeba ho nastavit na povolit, pokud máte v úmyslu získat přístup k virtuálním počítačům přes veřejnou IP adresu.
- Toto řešení nebere v úvahu překlad názvů DNS.
- Měli byste změnit své následujícího příkazu a Chappassword. Chappassword musí mít délku 12 až 16 znaků.
- Tato šablona používá pro virtuální počítač statickou IP adresu, protože připojení iSCSI používá místní adresu v konfiguraci.
- Tato šablona používá licenci Windows BYOL.
- Systémy se systémem Linux můžete také připojit k cílům iSCSI. Pokyny najdete v článku o [iniciátoru iSCSI](https://help.ubuntu.com/lts/serverguide/iscsi-initiator.html) v dokumentaci k Ubuntu.

### <a name="options"></a>Možnosti

- Pomocí parametrů **_artifactsLocation** a **_artifactsLocationSasToken** můžete použít vlastní účet úložiště objektů BLOB a token SAS a možnost používat vlastní objekt BLOB úložiště s tokenem SAS.
- Tato šablona poskytuje výchozí hodnoty pro pojmenování virtuální sítě a IP adresování.
- Tato konfigurace má jenom jednu síťovou kartu iSCSI, která přichází od klienta iSCSI. Otestovali jsme několik konfigurací, které využívají samostatné podsítě a síťové karty, ale byly problémy s několika branami a snaží se vytvořit samostatnou podsíť úložiště pro izolaci provozu a skutečně je opravdu redundantní. 
- Buďte opatrní, abyste tyto hodnoty zachovali v rámci platné podsítě a rozsahů adres, protože může dojít k selhání nasazení. 
- Primárním účelem balíčků prostředí PowerShell DSC je vyhledání nedokončených restartování. Tento DSC můžete v případě potřeby upravit. Další informace najdete v tématu [omputerManagementDsc](https://github.com/PowerShell/ComputerManagementDsc/).

### <a name="resource-group-template-iscsi-client"></a>Šablona skupiny prostředků (klient iSCSI)

Diagram zobrazuje prostředky nasazené ze šablony za účelem vytvoření klienta iSCSI, který můžete použít pro připojení k cíli iSCSI. Tato šablona nasadí virtuální počítač a další prostředky, navíc spustí prepare-iSCSIClient. ps1 a virtuální počítač se restartuje.

![alternativní text](./media/azure-stack-network-howto-iscsi-storage/iscsi-file-server.png)

### <a name="the-deployment-process"></a>Proces nasazení

Šablona skupiny prostředků vygeneruje výstup, který je určen jako vstup pro další krok. Primárně se zaměřuje na název serveru a veřejnou IP adresu centra Azure Stack, kde provoz iSCSI pochází. V tomto příkladu:

1. Nasaďte šablonu infrastruktury.
2. Nasaďte virtuální počítač centra Azure Stack do virtuálního počítače hostovaného jinde ve vašem datovém centru. 
3. Spusťte `Create-iSCSITarget.ps1` s použitím výstupů IP adres a serverů z šablony jako parametrů pro výstup pro skript v cíli iSCSI, což může být virtuální počítač nebo fyzický server.
4. Pro spuštění skriptu `Connect-toiSCSITarget.ps1` použijte externí IP adresu nebo adresy cílového serveru iSCSI jako vstupy. 

![alternativní text](./media/azure-stack-network-howto-iscsi-storage/process.png)

### <a name="inputs-for-azuredeployjson"></a>Vstupy pro azuredeploy. JSON

|**Parametry**|**výchozí**|**název**|
|------------------|---------------|------------------------------|
|WindowsImageSKU         |2019 – Datacenter   |Vyberte prosím základní image virtuálního počítače s Windows.
|VMSize                  |Standard_D2_v2    |Zadejte prosím velikost virtuálního počítače.
|VMName                  |Souborového serveru        |název virtuálního počítače
|adminUsername           |storageadmin      |Jméno správce nového virtuálního počítače
|adminPassword           |                  |Heslo pro účet správce pro nové virtuální počítače. Výchozí hodnota je ID předplatného.
|VNetName                |Úložiště           |Název virtuální sítě. Tato akce bude sloužit k označení prostředků.
|VNetAddressSpace        |10.10.0.0/23      |Adresní prostor pro virtuální síť
|VNetInternalSubnetName  |Interní          |Název interní podsítě virtuální sítě
|VNetInternalSubnetRange |10.10.1.0/24      |Rozsah adres pro interní podsíť virtuální sítě
|InternalVNetIP          |10.10.1.4         |Statická adresa pro interní IP adresu souborového serveru.
|_artifactsLocation      ||
|_artifactsLocationSasToken||

### <a name="deployment-steps"></a>Kroky nasazení

1. Nasazení infrastruktury klienta iSCSI pomocí `azuredeploy.json`
2. Spusťte `Create-iSCSITarget.ps1` v cíli iSCSI na místním serveru. Až se šablona dokončí, budete muset spustit Create-iSCSITarget. ps1 na cílovém serveru iSCSI na místním serveru pomocí výstupů z prvního kroku.
3. Spusťte `Connect-toiSCSITarget.ps1` na klientovi iSCSI. zruší Connect-toiSCSITarget. ps1 na klientovi iSCSI s podrobnostmi o cíli iSCSI.

## <a name="adding-iscsi-storage-to-existing-vms"></a>Přidání úložiště iSCSI do stávajících virtuálních počítačů

Můžete také spustit skripty na existujícím virtuálním počítači a připojit se z klienta iSCSI k cíli iSCSI. Tento tok je v případě, že vytváříte cíl iSCSI sami. Tento diagram znázorňuje tok spouštění pro skripty PowerShellu. Tyto skripty najdete v adresáři skriptu:

![alternativní text](./media/azure-stack-network-howto-iscsi-storage/script-flow.png)

### <a name="prepare-iscsiclientps1"></a>Prepare-iSCSIClient. ps1

Skript `Prepare-iSCSIClient.ps1` nainstaluje požadavky na klienta iSCSI, mezi které patří:
- instalace služeb Multipath-v/v
- nastavení služby iniciátoru iSCSI na automatické spuštění
- povolení podpory funkce Multipath MPIO pro iSCSI
- Povolit automatické uplatnění nároku na všechny svazky iSCSI
- Nastavte časový limit disku na 60 sekund.

Po instalaci těchto požadavků je důležité restartovat systém. Zásady vyrovnávání zatížení funkce MPIO vyžadují restart, aby bylo možné je nastavit.

### <a name="create-iscsitargetps1"></a>Create-iSCSITarget. ps1

Skript `Create-iSCSITarget.ps1 `musí být spuštěný v systému, který obsluhuje úložiště. Můžete vytvořit více disků a cílů omezených iniciátory. Tento skript můžete spustit vícekrát, abyste mohli vytvořit mnoho virtuálních disků, které můžete připojit k různým cílům. Můžete připojit více disků k jednomu cíli. 

|**Input** (Vstup)|**výchozí**|**název**|
|------------------|---------------|------------------------------|
|Vzdálený_server         |Souborového serveru               |Název serveru připojujícího se k cíli iSCSI
|RemoteServerIPs      |1.1.1.1                  |IP adresa, ze které bude přenosy iSCSI přijít
|DiskFolder           |C:\iSCSIVirtualDisks     |Složka a jednotka, kde budou virtuální disky uloženy
|DiskName             |DiskName                 |Název souboru VHDX disku
|DiskSize             |5 GB                      |Velikost disku VHDX
|TargetName           |RemoteTarget01           |Cílový název, který slouží k definování cílové konfigurace pro klienta iSCSI. 
|Následujícího příkazu         |uživatelské jméno                 |Uživatelské jméno pro ověřování protokolem CHAP
|ChapPassword         |userP@ssw0rd!            |Název hesla pro ověřování protokolem CHAP. Musí mít 12 až 16 znaků.

### <a name="connect-toiscsitargetps1"></a>Connect-toiSCSITarget. ps1

`Connect-toiSCSITarget.ps1` je finální skript, který se spouští na klientovi iSCSI a připojuje disk prezentovaný cílem iSCSI k klientovi iSCSI.

|**Input** (Vstup)|**výchozí**|**název**|
|------------------|---------------|------------------------------|
|TargetiSCSIAddresses   |"2.2.2.2","2.2.2.3"    |IP adresy cíle iSCSI
|LocalIPAddresses       |"10.10.1.4"            |Jedná se o interní IP adresu, ze které budou přenosy iSCSI přijít.
|LoadBalancePolicy      |C:\iSCSIVirtualDisks   |IP adresa, ze které bude přenosy iSCSI přijít
|Následujícího příkazu           |uživatelské jméno               |Uživatelské jméno pro ověřování protokolem CHAP
|ChapPassword           |userP@ssw0rd!          |Název hesla pro ověřování protokolem CHAP. Musí mít 12 až 16 znaků.

## <a name="next-steps"></a>Další kroky

[Rozdíly a požadavky pro sítě Azure Stack hub](azure-stack-network-differences.md)  