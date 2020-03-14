---
title: Přidání hostitelských serverů pro poskytovatele prostředků SQL
titleSuffix: Azure Stack Hub
description: Naučte se, jak přidat hostitelské servery pro zřizování prostřednictvím adaptéru poskytovatele prostředků SQL.
author: bryanla
ms.topic: article
ms.date: 10/02/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: a30c3a4f745e46a5e7b58d4355f1c193d8702e28
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79294679"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Přidání hostitelských serverů pro poskytovatele prostředků SQL

Můžete vytvořit SQL Server hostitelské servery databáze na virtuálním počítači (VM) v [centru Azure Stack](azure-stack-overview.md)nebo na virtuálním počítači mimo prostředí centra Azure Stack, pokud se poskytovatel prostředků SQL může připojit k instanci.

> [!NOTE]
> Poskytovatel prostředků SQL by se měl vytvořit v předplatném výchozího poskytovatele, zatímco hostitelské servery SQL by se měly vytvářet v Fakturovatelné předplatné uživatele. Server poskytovatele prostředků by neměl být používán k hostování uživatelských databází.

## <a name="overview"></a>Přehled

Před přidáním hostitelského serveru SQL zkontrolujte následující povinné a obecné požadavky.

### <a name="mandatory-requirements"></a>Povinné požadavky

* Povolte ověřování SQL pro instanci SQL Server. Vzhledem k tomu, že virtuální počítač poskytovatele prostředků SQL není připojený k doméně, může se připojit k hostitelskému serveru jenom pomocí ověřování SQL.
* Nakonfigurujte IP adresy pro instance SQL jako veřejné, pokud jsou nainstalované v Azure Stack hub. Poskytovatel prostředků a uživatelé, jako jsou webové aplikace, komunikují přes síť uživatelů, takže je potřeba připojení k instanci SQL v této síti.

### <a name="general-requirements"></a>Obecné požadavky

* Vyhradit instanci SQL pro použití poskytovatelem prostředků a uživatelských úloh. Nemůžete použít instanci SQL, kterou používá žádný jiný uživatel. Toto omezení platí také pro App Services.
* Nakonfigurujte účet s příslušnými úrovněmi oprávnění pro poskytovatele prostředků (popsaný níže).
* Zodpovídáte za správu instancí SQL a jejich hostitelů. Poskytovatel prostředků například nepoužívá aktualizace, zpracovává zálohování nebo zpracovává rotaci přihlašovacích údajů.

### <a name="sql-server-vm-images"></a>SQL Server imagí virtuálních počítačů

Image virtuálních počítačů s IaaS SQL jsou k dispozici prostřednictvím funkce správy Marketplace. Tyto image jsou stejné jako virtuální počítače SQL, které jsou k dispozici v Azure.

Před nasazením virtuálního počítače SQL pomocí položky Marketplace si nezapomeňte vždycky stáhnout nejnovější verzi **rozšíření SQL IaaS** . Rozšíření IaaS a odpovídající vylepšení portálu poskytují další funkce, jako jsou automatické opravy a zálohování. Další informace o tomto rozšíření najdete v tématu [Automatizace úloh správy na virtuálních počítačích Azure s rozšířením agenta SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

> [!NOTE]
> Pro všechny image SQL na webu Marketplace se _vyžaduje_ rozšíření SQL IaaS. Pokud jste rozšíření nestáhli, virtuální počítač se nepodaří nasadit. Nepoužívá se pro image virtuálních počítačů SQL se systémem Linux.

K dispozici jsou další možnosti pro nasazení virtuálních počítačů SQL, včetně šablon v [galerii rychlý Start centra Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Všechny hostitelské servery nainstalované v rozbočovači Azure Stack s více uzly se musí vytvořit z předplatného uživatele a nikoli z výchozího předplatného poskytovatele. Je nutné je vytvořit z portálu User Portal nebo z relace prostředí PowerShell s odpovídajícím přihlašovacím jménem. Všechny hostitelské servery jsou Fakturovatelné virtuální počítače a musí mít odpovídající licence SQL. Správce služby _může_ být vlastníkem tohoto předplatného.

### <a name="required-privileges"></a>Požadovaná oprávnění

Můžete vytvořit uživatele s oprávněním správce s nižšími oprávněními, než má správce systému SQL. Uživatel potřebuje oprávnění pouze pro následující operace:

* Databáze: vytvořit, změnit s omezením (jenom pro Always On), vyřadit, zálohovat
* Skupina dostupnosti: změnit, připojit, přidat nebo odebrat databázi
* Přihlášení: vytvořit, vybrat, změnit, zrušit, odvolat
* Vyberte operace: \[hlavní\].\]\[sys.\[availability_group_listeners\] (AlwaysOn), sys. availability_replicas (AlwaysOn), sys. databases, \[Master\].\]\[sys.\[dm_os_sys_memory\], SERVERPROPERTY, \[hlavní\].\]\[sys.\[availability_groups\] (AlwaysOn), sys. master_files

### <a name="additional-security-information"></a>Další informace o zabezpečení

Následující informace poskytují další pokyny k zabezpečení:

* Všechna Azure Stacková úložiště centra se šifrují pomocí nástroje BitLocker, takže jakákoli instance SQL v centru Azure Stack bude používat šifrované úložiště objektů BLOB.
* Poskytovatel prostředků SQL plně podporuje TLS 1,2. Zajistěte, aby všechny SQL Server spravované prostřednictvím SQL RP byly nakonfigurované _jenom_ pro TLS 1,2 a RP na to bude mít výchozí hodnotu. Všechny podporované verze SQL Server podporují protokol TLS 1,2. Další informace najdete v tématu [Podpora TLS 1,2 pro Microsoft SQL Server](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).
* Pomocí SQL Server Configuration Manager nastavte možnost **ForceEncryption** , aby se zajistilo, že veškerá komunikace s SQL serverem je vždycky šifrovaná. Další informace najdete v tématu [Konfigurace serveru pro vynucení šifrovaných připojení](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017#to-configure-the-server-to-force-encrypted-connections).
* Zajistěte, aby všechny klientské aplikace komunikovaly i přes šifrované připojení.
* RP je nakonfigurován tak, aby důvěřoval certifikátů, které používá instance SQL Server.

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Poskytnutí kapacity připojením k samostatnému hostujícímu SQL serveru

Samostatné servery SQL (bez HA) můžete použít v jakékoli edici SQL Server 2014 nebo SQL Server 2016. Ujistěte se, že máte přihlašovací údaje k účtu s oprávněními správce systému.

Chcete-li přidat samostatný hostitelský server, který je již nastaven, postupujte takto:

1. Přihlaste se k portálu správce Azure Stackového centra jako správce služby.

2. Vyberte **všechny služby** &gt; **prostředky pro správu** &gt; **hostitelských serverech SQL**.

   ![Hostitelské servery SQL na portálu pro správu centra Azure Stack](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   V části **hostitelské servery SQL**můžete poskytovatele prostředků SQL připojit k instancím SQL Server, které budou sloužit jako back-end poskytovatele prostředků.

   ![Řídicí panel adaptéru SQL na portálu pro správu centra Azure Stack](./media/azure-stack-sql-rp-deploy/sql-rp-hosting-server.png)

3. Klikněte na **Přidat** a zadejte podrobnosti o připojení pro vaši instanci SQL Server v okně **Přidat hostitelský server SQL** .

   ![Přidání hostitelského serveru SQL na portále správce centra Azure Stack](./media/azure-stack-sql-rp-deploy/sql-rp-new-hosting-server.png)

    Volitelně můžete zadat název instance a zadat číslo portu, pokud není instance přiřazena k výchozímu portu 1433.

   > [!NOTE]
   > Pokud je k instanci SQL přístup Azure Resource Manager uživatel a správce, může být umístěn pod kontrolou poskytovatele prostředků. Instance SQL __musí__ být přidělena výhradně poskytovateli prostředků.

4. Když přidáváte servery, musíte je přiřadit k existující SKU nebo vytvořit novou SKU. V části **Přidat hostitelský server**vyberte **SKU**.

   * Pokud chcete použít existující SKU, zvolte dostupnou SKU a pak vyberte **vytvořit**.
   * Pokud chcete vytvořit SKU, vyberte **+ vytvořit novou skladovou**položku. V části **vytvořit SKU**zadejte požadované informace a pak vyberte **OK**.

     ![Vytvoření SKU na portálu pro správu centra Azure Stack](./media/azure-stack-sql-rp-deploy/sqlrp-new-sku.png)

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Zajištění vysoké dostupnosti pomocí skupin dostupnosti Always On SQL serveru

Konfigurace instancí SQL Always On vyžaduje další kroky a vyžaduje tři virtuální počítače (nebo fyzické počítače). V tomto článku se předpokládá, že už máte plnou znalost skupin dostupnosti Always On. Další informace najdete v následujících článcích:

* [Představujeme SQL Server skupiny dostupnosti Always On na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Skupiny dostupnosti Always On (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> Poskytovatel prostředků SQL adaptéru podporuje _jenom_ instance SQL 2016 SP1 Enterprise nebo novější pro skupiny dostupnosti Always On. Tato konfigurace adaptéru vyžaduje nové funkce SQL, jako je automatické osazení.

### <a name="automatic-seeding"></a>Automatické osazení

Pro každou instanci SQL Server musíte povolit [Automatické osazení](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) pro každou skupinu dostupnosti.

Pokud chcete povolit automatické osazení u všech instancí, upravte a pak spusťte následující příkaz SQL na primární replice pro každou sekundární instanci:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<secondary_node>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

Skupina dostupnosti musí být uzavřena do hranatých závorek.

Na sekundárních uzlech spusťte následující příkaz SQL:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="configure-contained-database-authentication"></a>Konfigurovat ověřování databáze s omezením

Před přidáním databáze s omezením do skupiny dostupnosti zajistěte, aby byla v každé instanci serveru, která hostuje repliku dostupnosti pro skupinu dostupnosti, nastavená možnost Server pro ověřování databáze s omezením na hodnotu 1. Další informace najdete v tématu věnovaném [Možnosti konfigurace serveru pro ověřování databáze](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Pomocí těchto příkazů nastavte u každé instance možnost Server pro ověřování databáze s omezením:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>Přidání serveru SQL Always na hostitelské servery

1. Přihlaste se k portálu správce Azure Stackového centra jako správce služby.

2. Vyberte **procházet** &gt; **prostředky pro správu** &gt; **hostitelských serverech SQL** &gt; **+ Přidat**.

   V části **hostitelské servery SQL**můžete poskytovatele prostředků SQL Server připojit k skutečným instancím SQL Server, které slouží jako back-end poskytovatele prostředků.

3. Vyplňte formulář s podrobnostmi o připojení pro vaši instanci SQL Server. Ujistěte se, že používáte adresu plně kvalifikovaného názvu domény pro naslouchací proces Always On (a volitelné číslo portu a název instance). Zadejte informace o účtu, který jste nakonfigurovali s oprávněními správce systému.

4. Pokud chcete povolit podporu instancí skupin dostupnosti Always On SQL, zaškrtněte políčko Skupina dostupnosti Always On.

   ![Povolit skupinu dostupnosti Always On na portálu pro správu centra Azure Stack](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Přidejte instanci SQL Always On do SKU.

   > [!IMPORTANT]
   > Nemůžete kombinovat samostatné servery s instancemi Always On ve stejné SKU. Při pokusu o kombinaci typů po přidání prvního hostitelského serveru dojde k chybě.

## <a name="sku-notes"></a>Poznámky SKU
Použijte název SKU, který popisuje možnosti serverů v SKU, jako je například kapacita a výkon. Název slouží jako pomůcka k tomu, aby uživatelé mohli nasadit své databáze do příslušné SKU. Například můžete použít názvy SKU k odlišení nabídek služeb následujícími charakteristikami:
  
* vysoká kapacita
* vysoký výkon
* vysoká dostupnost

Osvědčeným postupem je, že všechny hostitelské servery v SKU by měly mít stejné charakteristiky prostředků a výkonu.

SKU nelze přiřadit konkrétním uživatelům nebo skupinám.

SKU může trvat až hodinu, než se na portálu zobrazí. Uživatelé nemůžou vytvořit databázi, dokud se SKU nevytvoří úplně.

Chcete-li upravit SKLADOVOU položku, klikněte na **všechny služby** > **SQL Adapter** > **SKU**. Vyberte SKLADOVOU položku, kterou chcete upravit, proveďte potřebné změny a uložte změny kliknutím na **Uložit** . 

Pokud chcete odstranit SKU, které už nepotřebujete, přečtěte si **všechny služby** > **SQL Adapter** > **SKU**. Klikněte pravým tlačítkem na název SKU a vyberte **Odstranit** a odstraňte ho.

> [!IMPORTANT]
> Může trvat až hodinu, než se nové SKU zpřístupní na portálu User Portal.

## <a name="make-sql-databases-available-to-users"></a>Zpřístupnění databází SQL pro uživatele

Vytvořte plány a nabídky, které uživatelům zpřístupní databáze SQL. Přidejte do plánu službu **Microsoft. SqlAdapter** a vytvořte novou kvótu.

> [!IMPORTANT]
> Může trvat až dvě hodiny, než budou nové kvóty dostupné na portálu User Portal nebo před tím, než se vynutila změněná kvóta.

## <a name="next-steps"></a>Další kroky

[Přidat databáze](azure-stack-sql-resource-provider-databases.md)
