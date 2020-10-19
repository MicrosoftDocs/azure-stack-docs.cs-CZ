---
title: Azure Stack – aktualizace firmwaru pro modulární úložiště s rozšířeným datovým centrem
description: Tento článek poskytuje pokyny, jak provádět aktualizace firmwaru v rozšířeném úložišti pro modulární úložiště objektů BLOB datového centra.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: justinha
ms.reviewer: karlt
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 23430fd05254b1509677bf2d7ddedab75c7c2355
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92182974"
---
# <a name="updating-onefs-and-firmware-for-the-datacenter-extended-storage"></a>Aktualizuje se OneFS a firmware pro rozšířené úložiště Datacenter.

Tento článek poskytuje pokyny pro aktualizaci OneFS a firmware hardwaru rozšířeného úložiště Datacenter. 

Poslední kroky v rozšířené integraci a nasazení hardwaru rozšířeného úložiště jsou kroky, které ověřují hardware v pořádku a jsou připravené ke konfiguraci. Postup je popsaný v tématu [Kontrola stavu pro rozšířené úložiště](extended-storage-health-checks.md).

## <a name="update-onefs"></a>Aktualizovat OneFS

Aktualizujte OneFS, takže před aktualizací firmwaru se aplikují nejnovější opravy operačního systému. 

1. Ověřte, zda má cluster dostatek volného místa pro instalaci této opravy.
1. Otevřete připojení SSH na jakémkoli uzlu v clusteru a přihlaste se pomocí kořenového účtu.

   >[!NOTE]
   >Pokud je oprava nainstalovaná v clusteru režimu dodržování předpisů, přihlaste se pomocí účtu compadmin.

1. Zkopírujte soubor 8.2.2_GA-RUP_2020-07_PSP -40. tgz do adresáře Isilon_Support/IFS/data/v clusteru.
1. Spuštěním následujícího příkazu přejděte do adresáře/IFS/data/Isilon_Support: 

   ```
   /ifs/data/Isilon_Support
   pwd
   ```

1. Chcete-li extrahovat soubor opravy, spusťte následující příkaz:

   ```
   -zxvf 8.2.2_GA-RUP_2020-07_PSP-40.tgz
   ```

1. Chcete-li nainstalovat tuto opravu, spusťte následující příkaz:
   >[!WARNING]
   >Následující příkaz provede restart všech uzlů v clusteru současně. Chcete-li nainstalovat opravu a poté restartovat každý uzel po úspěchu, vynechejte `-simultaneous` parametr. Další informace najdete v části soubor READme s dopady na instalaci/odebrání.

   ```
   isi upgrade patches install 8.2.2_GA-RUP_2020-07_PSP-40.pkg --simultaneous
   ```

1. Chcete-li ověřit, zda je tato oprava nainstalována, spusťte následující příkaz:

   ```
   isi upgrade patches list
   ```

1. Ověřte, že se v seznamu nainstalovaných balíčků zobrazí 8.2.2_GA-RUP_2020-07_PSP-40 a stav je nastaven na nainstalováno.

## <a name="update-firmware-on-nodes"></a>Aktualizovat firmware na uzlech

   1. Projděte si aktuální firmware jednotek a disky pro podporu [balíčků](https://aka.ms/currentextstoragerelease).
   1. Stáhněte si soubory do místního počítače.  
   1. Nahrajte balíčky jednotky a firmware uzlu do sdílené složky Isilon a umístěte soubory do následujících příslušných adresářů:
    
        * Drive_Support_ *. tgz:/IFS/data/Isilon_Support/DSP/
        * IsiFw_Package_ *. tar:/IFS/data 

## <a name="verify-disk-drive-firmware"></a>Ověřit firmware diskové jednotky

Nejprve proveďte podporu aktualizace/ověřování na jednotce. Tím se zjistí, jestli je firmware diskové jednotky v aktuálním balíčku aktuální. V takovém případě se balíček dá použít k provedení nezbytných aktualizací:

   1. Z aktivní relace SSH spusťte:

       ```
       cd /ifs/data/Isilon_Support/dsp/
       ```

   1. Ujistěte se, že Drive_Support_ *. tgz je v tomto adresáři, spuštěním příkazu ls.
   1. Rozbalte soubor. tgz spuštěním následujícího příkazu. Ujistěte se, že máte úplný název souboru a nahraďte * skutečným číslem verze balíčku:
       ```
       tar -zxvf Drive_Support_v1.*.tgz
       ```
       Pokud je například verze balíčku Drive_Support_v1 .33. tgz, spusťte následující příkaz:
       ```
       tar -zxvf Drive_Support_v1.33.tgz
       ```
   1. Tím se rozšíří na tři soubory:
    
        * Drive_Support_v1.33. tar
        * Drive_Support_v1.33. tar. MD5
        * Drive_Support_v1.33. tar. SHA256
   1. Spusťte následující příkaz, který nainstaluje nebo ověří software podpory jednotky:
       ```
       isi_dsp_install Drive_Support_v1.*.tar
       ```
       Pokud je například verze balíčku Drive_Support_v1 .33. tgz, spusťte následující příkaz:
       ```
       isi_dsp_install Drive_Support_v1.33.tar
       ```
   1. Tento příkaz spustí kontroly a požádá o instalaci balíčku pro podporu disků. Pokud je balíček potřeba, nainstaluje se. v opačném případě se zobrazí, že je nainstalován a přeskočí všechny další aktualizace pro software podpory jednotky. Výstup příkladu pro v 1.33 je následující:
    
        ```
        Running local CHECKS for patch Drive_Support_v1.33
        INSTALL operation has been requested for patch Drive_Support_v1.33
        Installed Drive_Support_v1.33
        DELETE operation has been requested for patch
        Drive_Support_v1.33
        Removed Drive_Support_v1.33 package files
        DSP Install Succeeded, 170.605088949 sec elapsed
        ```
        
   1. Konečné potvrzení můžete provést spuštěním následujícího příkazu. Jakékoli jiné než znak "-" ve sloupci "žádoucí" značí, že jednotka musí být aktualizována:
       ```
       isi devices drive firmware list --node-lnn all
       ```
   1. Pokud existují některé jednotky, které je třeba aktualizovat, je třeba použít následující příkaz k provedení aktualizací firmwaru disku:
       ```
       isi devices drive firmware update start all --node-lnn all
       ```
   1. Tento proces se dá monitorovat pomocí následujícího příkazu:
       ```
       isi devices drive firmware update list
       ```
   1. A po dokončení se stav jednotek dá ověřit pomocí následujícího příkazu:
       ```
       isi devices drive list --node-lnn all
       ```
## <a name="install-node-firmware"></a>Nainstalovat firmware uzlu

Po dokončení nebo ověření aktualizací podpory jednotky můžete firmware uzlu nainstalovat s využitím IsiFw_Package_ *. tar.

   1. Z aktivní relace SSH spusťte:
        ```
        cd /ifs/data/
        ```
   1. Ujistěte se, že IsiFw_Package_ *. tar je v tomto adresáři, spuštěním příkazu ls.
   1. Spusťte následující příkaz pro přípravu a instalaci firmwaru napříč uzly. Ujistěte se, že máte úplný název souboru a nahraďte * skutečným číslem verze balíčku:
       ```
       isi upgrade patches install IsiFw_Package_v*.tar --rolling=false
       ```
       Pokud je číslo verze například v 10.3.3. tar, spusťte následující příkaz:
       ```
       isi upgrade patches install IsiFw_Package_v10.3.3.tar --rolling=false
       ```
   1. Po spuštění se jako potvrzení pošle následující zpráva:
       ```
        Requested install of patch IsiFw_Package_v10.3.3.
       ```
   1. Ověřte stav fáze/instalace pomocí následujícího příkazu:
       ```
       isi upgrade patches list
       ```
   1. Sloupec "stav" se po dokončení procesu aktualizuje, stav zobrazený bude: čeká na vyřízení, neznámý, instalace a nainstalováno, jak je vidět v následujícím příkladu výstupu:
        
       ![Stav aktualizace firmwaru](media/extended-storage-firmware-updates/firmware-update-status.png)
        
   1. Po přípravě nebo instalaci IsiFw_Package *. tar byl možné spustit nasazení aktualizovaného firmwaru v rámci uzlů, a to pomocí následujícího příkazu:
        ```
        isi upgrade cluster firmware start --no-verify
        ```
   1. Zobrazí se výzva k následujícímu potvrzení, pokud chcete pokračovat, zadejte "Ano":
       ```
       You are about to start a Rolling Firmware UPGRADE, are you sure? (yes/[no]):
       ```
        
       > [!NOTE]
       > Aktualizace firmwaru uzlu může trvat několik hodin a uzly budou v průběhu procesu přepnuty do offline režimu. Při provádění všech těchto příkazů z uzlu 1 bude připojení vyřazeno, protože se jedná o první uzel, který se má aktualizovat. Totéž platí pro portál OneFS – počkejte 5-10 minut, než se pokusíte znovu připojit ke stavu kontroly (Pokud je to potřeba, připojení přes jiné rozhraní pro správu, které se neaktualizuje), je také možné.
   1. V aktivním rozhraní pro správu/připojení můžete sledovat průběžný stav této aktualizace firmwaru pomocí následujícího příkazu:
       ```
       isi upgrade cluster nodes firmware progress list
       ```
       Nebo pomocí portálu OneFS v části Správa clusteru – opravy a firmware:
        
       ![Správa clusteru](media/extended-storage-firmware-updates/cluster-management.png)
        
       Po dokončení se stav změní z **Neznámý** na **nainstalováno**.

## <a name="remove-firmware-packages"></a>Odebrat balíčky firmwaru

Odeberte balíčky firmwaru po jejich úplné instalaci mezi jednotkami a uzly pomocí následujících příkazů. Ujistěte se, že máte úplný název souboru a nahraďte v * skutečnou verzí balíčku:
   ```
   isi upgrade patches uninstall IsiFw_Package_ v*.tar --rolling=false
   ```
   Pokud je například balíček verze IsiFw_Package_ v 10.3.1. tar, spusťte následující příkaz:
   ```
   isi upgrade patches uninstall IsiFw_Package_ v10.3.1.tar --rolling=false
   ```
Z aktivní relace SSH spusťte:
   ```
   cd /ifs/data/
   rm -f Drive_Support_v*.tgz
   rm -f Drive_Support_v*.tar
   rm -f Drive_Support_v*.tar.md5
   rm -f Drive_Support_v*.tar.sha256
   ```
   Například:
   ```
   cd /ifs/data/
   rm -f Drive_Support_v10.3.1.tgz
   rm -f Drive_Support_v10.3.1.tar
   rm -f Drive_Support_v10.3.1.tar.md5
   rm -f Drive_Support_v10.3.1.tar.sha256
   ```


        
<!-- ## Contact Dell EMC for support -->

Kontaktujte podpora Microsoftu a Získejte pomoc s případnými problémy.

## <a name="next-steps"></a>Další kroky

- [Rozšířené kontroly stavu úložiště](extended-storage-health-checks.md)
