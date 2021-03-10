---
title: Předpoklady nasazení Azure App Service v centru Azure Stack
description: Před nasazením Azure App Service v centru Azure Stack se naučíte splnění požadovaných kroků.
author: BryanLa
ms.topic: article
ms.date: 10/28/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 10/28/2019
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 82c97abf81226c22e2878bb6e6947d53f79cba77
ms.sourcegitcommit: 02a4c34fb829e053016912a4fffcc51e32685425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102532439"
---
# <a name="prerequisites-for-deploying-app-service-on-azure-stack-hub"></a>Požadavky na nasazení služby App Service ve službě Azure Stack Hub

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

Před nasazením Azure App Service v centru Azure Stack je nutné provést požadované kroky v tomto článku.

## <a name="before-you-get-started"></a>Než začnete 

V této části jsou uvedeny předpoklady pro nasazení integrovaných systémů a Azure Stack Development Kit (ASDK).

### <a name="resource-provider-prerequisites"></a>Požadavky poskytovatele prostředků

[!INCLUDE [Common RP prerequisites](../includes/resource-provider-prerequisites.md)]

### <a name="installer-and-helper-scripts"></a>Instalační a pomocné skripty

1. Stáhněte si [App Service v Azure Stackch pomocných skriptech nasazení centra](https://aka.ms/appsvconmashelpers).
2. Stáhněte [App Service v instalačním programu centra Azure Stack](https://aka.ms/appsvconmasinstaller).
3. Extrahujte soubory ze souboru pomocných skriptů. zip. Extrahovány jsou následující soubory a složky:

   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Složka modulů
     - GraphAPI. psm1

<!-- MultiNode Only --->
## <a name="certificates-and-server-configuration-integrated-systems"></a>Certifikáty a konfigurace serveru (integrované systémy)

V této části jsou uvedené požadavky na nasazení integrovaných systémů.

### <a name="certificate-requirements"></a>Požadavky na certifikáty

Pokud chcete spustit poskytovatele prostředků v produkčním prostředí, musíte zadat tyto certifikáty:

- Výchozí certifikát domény
- Certifikát rozhraní API
- Publikování certifikátu
- Certifikát identity

Kromě specifických požadavků uvedených v následujících oddílech také použijte nástroj později k otestování obecných požadavků. Úplný seznam ověření najdete v tématu [ověření certifikátů infrastruktury veřejných klíčů Azure Stack hub](azure-stack-validate-pki-certs.md) , včetně:
- **Formát souboru** . PFX
- **Použití klíče** nastaveného na ověřování serveru a klienta
- a několik dalších

#### <a name="default-domain-certificate"></a>Výchozí certifikát domény

Výchozí certifikát domény je umístěn na front-end roli. Uživatelské aplikace pro zástupný znak nebo výchozí doména, které Azure App Service použít tento certifikát. Certifikát se používá také pro operace správy zdrojových kódů (Kudu).

Certifikát musí být ve formátu. pfx a měl by se jednat o certifikát zástupných znaků se třemi tématy. Tento požadavek umožňuje jednomu certifikátu pokrýt jak výchozí doménu, tak koncový bod SCM pro operace správy zdrojových kódů.

| Formát | Příklad |
| --- | --- |
| `*.appservice.<region>.<DomainName>.<extension>` | `*.appservice.redmond.azurestack.external` |
| `*.scm.appservice.<region>.<DomainName>.<extension>` | `*.scm.appservice.redmond.azurestack.external` |
| `*.sso.appservice.<region>.<DomainName>.<extension>` | `*.sso.appservice.redmond.azurestack.external` |

#### <a name="api-certificate"></a>Certifikát rozhraní API

Certifikát rozhraní API se umístí do role správy. Poskytovatel prostředků ho používá k zajištění zabezpečení volání rozhraní API. Certifikát pro publikování musí obsahovat předmět, který odpovídá položce DNS rozhraní API.

| Formát | Příklad |
| --- | --- |
| API. AppService. \<region\> . \<DomainName\> .\<extension\> | API. AppService. Redmond. azurestack. external |

#### <a name="publishing-certificate"></a>Publikování certifikátu

Certifikát pro roli vydavatele zabezpečuje přenos FTPS pro vlastníky aplikací při nahrávání obsahu. Certifikát pro publikování musí obsahovat předmět, který odpovídá položce DNS FTPS.

| Formát | Příklad |
| --- | --- |
| FTP. AppService. \<region\> . \<DomainName\> .\<extension\> | FTP. AppService. Redmond. azurestack. external |

#### <a name="identity-certificate"></a>Certifikát identity

Certifikát pro aplikaci identity umožňuje:

- Integrace mezi adresářem Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) (AD FS), Azure Stackm centrem a App Service pro podporu integrace se zprostředkovatelem výpočetních prostředků.
- Scénáře jednotného přihlašování pro pokročilé vývojářské nástroje v Azure App Service v centru Azure Stack

Certifikát pro identitu musí obsahovat předmět, který odpovídá následujícímu formátu.

| Formát | Příklad |
| --- | --- |
| SSO. AppService. \<region\> . \<DomainName\> .\<extension\> | SSO. AppService. Redmond. azurestack. external |

### <a name="validate-certificates"></a>Ověřit certifikáty

Než nasadíte poskytovatele prostředků App Service, měli byste [ověřit certifikáty, které se mají použít](azure-stack-validate-pki-certs.md) , pomocí nástroje pro kontrolu připravenosti centra Azure Stack dostupného z [Galerie prostředí PowerShell](https://aka.ms/AzsReadinessChecker). Nástroj pro kontrolu připravenosti centra Azure Stack ověří, že vygenerované certifikáty PKI jsou vhodné pro App Service nasazení.

V rámci osvědčeného postupu se při práci s některým z nezbytných [certifikátů PKI centra Azure Stack](azure-stack-pki-certs.md)v případě potřeby měli naplánovat dostatek času na testování a vystavování certifikátů.

### <a name="prepare-the-file-server"></a>Příprava souborového serveru

Azure App Service vyžaduje použití souborového serveru. U produkčních nasazení je třeba souborový server nakonfigurovat tak, aby byl vysoce dostupný a schopný zpracovávat chyby.

#### <a name="quickstart-template-for-highly-available-file-server-and-sql-server"></a>Šablona pro rychlý Start pro souborový server s vysokou dostupností a SQL Server

K dispozici je teď [Šablona pro rychlý Start referenční architektury](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/appservice-fileserver-sqlserver-ha) , která nasadí souborový server a SQL Server. Tato šablona podporuje infrastrukturu služby Active Directory ve virtuální síti nakonfigurované na podporu vysoce dostupného nasazení Azure App Service v centru Azure Stack.

> [!NOTE]
> Aby bylo možné dokončit nasazení, musí být integrovaná instance systému schopna stahovat prostředky z GitHubu.

#### <a name="steps-to-deploy-a-custom-file-server"></a>Postup nasazení vlastního souborového serveru

>[!IMPORTANT]
> Pokud se rozhodnete nasadit App Service do existující virtuální sítě, musí být souborový server nasazený do samostatné podsítě od App Service.

>[!NOTE]
> Pokud jste se rozhodli nasadit souborový server pomocí některé ze šablon rychlý Start uvedených výše, můžete tento oddíl přeskočit, protože souborové servery jsou nakonfigurovány jako součást nasazení šablony.

##### <a name="provision-groups-and-accounts-in-active-directory"></a>Zřizování skupin a účtů ve službě Active Directory

1. Vytvořte následující globální skupiny zabezpečení služby Active Directory:

   - FileShareOwners
   - FileShareUsers

2. Vytvořte následující účty služby Active Directory jako účty služeb:

   - Přihlašovací
   - Přihlašovací

   Z hlediska zabezpečení by měl být uživatel pro tyto účty (a pro všechny webové role) jedinečný a musí mít silné uživatelské jméno a hesla. Nastavte hesla pomocí následujících podmínek:

   - Povolení **hesla je platné** stále.
   - Možnost povolit **uživateli nemůže měnit heslo**.
   - **Při dalším přihlášení musí uživatel zakázat změnu hesla**.

3. Účty přidejte do členství ve skupině následujícím způsobem:

   - Přidejte **přihlašovací** do skupiny **FileShareOwners** .
   - Přidejte **přihlašovací** do skupiny **FileShareUsers** .

##### <a name="provision-groups-and-accounts-in-a-workgroup"></a>Zřizování skupin a účtů v pracovní skupině

>[!NOTE]
> Při konfiguraci souborového serveru spusťte z **příkazového řádku správce** všechny následující příkazy. <br>**_Nepoužívejte PowerShell._**

Při použití šablony Azure Resource Manager jsou uživatelé již vytvořeni.

1. Spuštěním následujících příkazů vytvořte účty přihlašovací a přihlašovací. Nahraďte `<password>` vlastními hodnotami.

   ``` DOS
   net user FileShareOwner <password> /add /expires:never /passwordchg:no
   net user FileShareUser <password> /add /expires:never /passwordchg:no
   ```

2. Pomocí následujících příkazů nástroje WMIC nastavte hesla pro účty, na jejichž vypršení má být nikdy neukončeno:

   ``` DOS
   WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
   WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
   ```

3. Vytvořte místní skupiny FileShareUsers a FileShareOwners a přidejte do nich účty v prvním kroku:

   ``` DOS
   net localgroup FileShareUsers /add
   net localgroup FileShareUsers FileShareUser /add
   net localgroup FileShareOwners /add
   net localgroup FileShareOwners FileShareOwner /add
   ```

#### <a name="provision-the-content-share"></a>Zřízení sdílené složky obsahu

Sdílená složka obsahuje obsah webu tenanta. Postup zřízení sdílené složky obsahu na jednom souborovém serveru je stejný pro prostředí Active Directory i pracovní skupiny. Liší se ale pro cluster s podporou převzetí služeb při selhání ve službě Active Directory.

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>Zřízení sdílené složky obsahu na jednom souborovém serveru (ve službě Active Directory nebo v pracovní skupině)

Na jediném souborovém serveru spusťte na příkazovém řádku se zvýšenými oprávněními následující příkazy. Nahraďte hodnotu pro `C:\WebSites` odpovídajícími cestami ve vašem prostředí.

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="configure-access-control-to-the-shares"></a>Konfigurace řízení přístupu ke sdíleným složkám

Spusťte následující příkazy na příkazovém řádku se zvýšenými oprávněními na souborovém serveru nebo na uzlu clusteru s podporou převzetí služeb při selhání, který je aktuálním vlastníkem prostředku clusteru. Nahraďte hodnoty kurzívou hodnotami, které jsou specifické pro vaše prostředí.

#### <a name="active-directory"></a>Active Directory

```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>Pracovní skupina

```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

### <a name="prepare-the-sql-server-instance"></a>Příprava instance SQL Serveru

> [!NOTE]
> Pokud jste se rozhodli nasadit šablonu pro rychlé zprovoznění pro souborový server s vysokou dostupností a SQL Server, můžete tuto část přeskočit, protože šablona se nasazuje a nakonfiguruje SQL Server v konfiguraci HA.

Pro Azure App Service v Azure Stack hostujícím centru a databázích měření musíte připravit instanci SQL Server, aby obsahovala databáze App Service.

Pro účely produkčního prostředí a vysoké dostupnosti byste měli použít plnou verzi SQL Server 2014 SP2 nebo novější, povolit ověřování ve smíšeném režimu a nasadit v [konfiguraci s vysokou dostupností](/sql/sql-server/failover-clusters/high-availability-solutions-sql-server).

Instance SQL Server pro Azure App Service v centru Azure Stack musí být přístupná ze všech rolí App Service. SQL Server můžete nasadit v rámci předplatného výchozího poskytovatele v centru Azure Stack. Nebo můžete využít stávající infrastrukturu v rámci vaší organizace (Pokud je k dispozici připojení k Azure Stack hub). Pokud používáte bitovou kopii Azure Marketplace, nezapomeňte patřičně nakonfigurovat bránu firewall.

> [!NOTE]
> Pomocí funkce správy Marketplace je k dispozici několik imagí virtuálních počítačů s IaaS SQL. Před nasazením virtuálního počítače pomocí položky Marketplace se ujistěte, že jste vždycky stáhli nejnovější verzi rozšíření SQL IaaS. Image SQL jsou stejné jako virtuální počítače SQL, které jsou k dispozici v Azure. Pro virtuální počítače SQL vytvořené z těchto imagí rozšíření IaaS a odpovídající vylepšení portálu poskytují funkce, jako jsou automatické opravy a možnosti zálohování.
>
> Pro kteroukoli z SQL Server rolí můžete použít výchozí instanci nebo pojmenovanou instanci. Pokud používáte pojmenovanou instanci, nezapomeňte ručně spustit službu SQL Server Browser a otevřete port 1434.

Instalační program App Service zkontroluje, jestli má SQL Server povolený obsah databáze. Pokud chcete povolit zahrnutí databáze na SQL Server, která bude hostovat databáze App Service, spusťte tyto příkazy SQL:

```sql
sp_configure 'contained database authentication', 1;
GO
RECONFIGURE;
GO
```

<!-- ASDK Only --->
## <a name="certificates-and-server-configuration-asdk"></a>Certifikáty a konfigurace serveru (ASDK)

V této části jsou uvedeny předpoklady pro nasazení ASDK. 

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>Certifikáty vyžadované pro ASDK nasazení Azure App Service

*Create-AppServiceCerts.ps1* skript spolupracuje s certifikační autoritou centra Azure Stack k vytvoření čtyř certifikátů, které App Service potřebují.

| Název souboru | Použití |
| --- | --- |
| _. AppService. Local. azurestack. external. pfx | App Service výchozí certifikát SSL |
| API. AppService. Local. azurestack. external. pfx | Certifikát SSL pro App Service rozhraní API |
| FTP. AppService. Local. azurestack. external. pfx | App Service certifikát SSL vydavatele |
| SSO. AppService. Local. azurestack. external. pfx | Certifikát aplikace App Service identity |

K vytvoření certifikátů použijte následující postup:

1. Přihlaste se k hostiteli ASDK pomocí účtu AzureStack\AzureStackAdmin.
2. Otevřete relaci PowerShellu se zvýšenými oprávněními.
3. Spusťte skript *Create-AppServiceCerts.ps1* ze složky, do které jste extrahovali pomocné skripty. Tento skript vytvoří čtyři certifikáty ve stejné složce, jako je skript, který App Service potřebuje k vytváření certifikátů.
4. Zadejte heslo pro zabezpečení souborů. pfx a poznamenejte si ho. Je nutné zadat ho později v App Service v instalačním programu centra Azure Stack.

#### <a name="create-appservicecertsps1-script-parameters"></a>Parametry skriptu Create-AppServiceCerts.ps1

| Parametr | Požadované nebo volitelné | Výchozí hodnota | Description |
| --- | --- | --- | --- |
| pfxPassword | Vyžadováno | Null | Heslo, které pomáhá chránit privátní klíč certifikátu |
| DomainName | Vyžadováno | Local. azurestack. external | Oblast centra Azure Stack a přípona domény |

### <a name="quickstart-template-for-file-server-for-deployments-of-azure-app-service-on-asdk"></a>Šablona pro rychlý Start pro souborový server pro nasazení Azure App Service v ASDK.

Jenom pro nasazení ASDK můžete použít [ukázkový Azure Resource Manager šablonu nasazení](https://aka.ms/appsvconmasdkfstemplate) k nasazení nakonfigurovaného souborového serveru s jedním uzlem. Souborový server s jedním uzlem bude v pracovní skupině.  

> [!NOTE]
> Instance ASDK musí být schopná stáhnout prostředky z GitHubu, aby bylo možné dokončit nasazení.

### <a name="sql-server-instance"></a>Instance SQL Server

Pro Azure App Service v Azure Stack hostujícím centru a databázích měření musíte připravit instanci SQL Server, aby obsahovala databáze App Service.

Pro nasazení ASDK můžete použít SQL Server Express 2014 SP2 nebo novější. SQL Server musí být nakonfigurovaná tak, **aby podporovala** ověřování ve **smíšeném režimu** , protože App Service v Azure Stackovém centru nepodporuje ověřování systému Windows.

Instance SQL Server pro Azure App Service v centru Azure Stack musí být přístupná ze všech rolí App Service. SQL Server můžete nasadit v rámci předplatného výchozího poskytovatele v centru Azure Stack. Nebo můžete využít stávající infrastrukturu v rámci vaší organizace (Pokud je k dispozici připojení k Azure Stack hub). Pokud používáte bitovou kopii Azure Marketplace, nezapomeňte patřičně nakonfigurovat bránu firewall.

> [!NOTE]
> Pomocí funkce správy Marketplace je k dispozici několik imagí virtuálních počítačů s IaaS SQL. Před nasazením virtuálního počítače pomocí položky Marketplace se ujistěte, že jste vždycky stáhli nejnovější verzi rozšíření SQL IaaS. Image SQL jsou stejné jako virtuální počítače SQL, které jsou k dispozici v Azure. Pro virtuální počítače SQL vytvořené z těchto imagí rozšíření IaaS a odpovídající vylepšení portálu poskytují funkce, jako jsou automatické opravy a možnosti zálohování.
>
> Pro kteroukoli z SQL Server rolí můžete použít výchozí instanci nebo pojmenovanou instanci. Pokud používáte pojmenovanou instanci, nezapomeňte ručně spustit službu SQL Server Browser a otevřete port 1434.

Instalační program App Service zkontroluje, jestli má SQL Server povolený obsah databáze. Pokud chcete povolit zahrnutí databáze na SQL Server, která bude hostovat databáze App Service, spusťte tyto příkazy SQL:

```sql
sp_configure 'contained database authentication', 1;
GO
RECONFIGURE;
GO

```

## <a name="licensing-concerns-for-required-file-server-and-sql-server"></a>Aspekty licencování požadovaného souborového serveru a SQL Serveru

Azure App Service v centru Azure Stack vyžaduje souborový server a SQL Server k provozu. Nebudete používat už existující prostředky nacházející se mimo vaše nasazení centra Azure Stack nebo nasazovat prostředky v rámci předplatného výchozího poskytovatele služby Azure Stack hub.

Pokud se rozhodnete nasadit prostředky v rámci předplatného výchozího poskytovatele centra Azure Stack, jsou licence pro tyto prostředky (licence na Windows Server a licence SQL Server) zahrnuté do nákladů na Azure App Service v Azure Stackovém centru v závislosti na následujících omezeních:

- infrastruktura se nasadí do výchozího předplatného poskytovatele;
- infrastruktura se používá výhradně Azure App Service v poskytovateli prostředků Azure Stack hub. Tuto infrastrukturu můžou používat i žádné jiné úlohy, administrativní (jiní poskytovatelé prostředků, například: SQL-RP) nebo tenant (například klientské aplikace, které vyžadují databázi).

## <a name="operational-responsibility-of-file-and-sql-servers"></a>Provozní zodpovědnost souborů a serverů SQL

Operátoři cloudu zodpovídají za údržbu a provoz souborového serveru a SQL Server.  Poskytovatel prostředků tyto prostředky nespravuje.  Operátor cloudu zodpovídá za zálohování databází App Service a sdílené složky obsahu tenanta.

## <a name="retrieve-the-azure-resource-manager-root-certificate-for-azure-stack-hub"></a>Načtení kořenového certifikátu Azure Resource Manager pro centrum Azure Stack

Otevřete relaci PowerShellu se zvýšenými oprávněními na počítači, který se může spojit s privilegovaným koncovým bodem Azure Stack v integrovaném systému nebo hostiteli ASDK hub.

Spusťte skript *Get-AzureStackRootCert.ps1* ze složky, do které jste extrahovali pomocné skripty. Skript vytvoří kořenový certifikát ve stejné složce, jako je skript, který App Service potřebuje k vytváření certifikátů.

Když spustíte následující příkaz PowerShellu, musíte zadat privilegovaný koncový bod a přihlašovací údaje pro AzureStack\CloudAdmin..

```powershell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Parametry skriptu Get-AzureStackRootCert.ps1

| Parametr | Požadované nebo volitelné | Výchozí hodnota | Description |
| --- | --- | --- | --- |
| PrivilegedEndpoint | Vyžadováno | AzS-ERCS01 | Privilegovaný koncový bod |
| CloudAdminCredential | Vyžadováno | AzureStack\CloudAdmin | Přihlašovací údaje účtu domény pro správce cloudu Azure Stack hub |

## <a name="network-and-identity-configuration"></a>Konfigurace sítě a identity

### <a name="virtual-network"></a>Virtuální síť

> [!NOTE]
> Předvytvoření vlastní virtuální sítě je volitelné, protože Azure App Service v centru Azure Stack může vytvořit požadovanou virtuální síť, ale bude muset komunikovat s SQL a souborovým serverem prostřednictvím veřejných IP adres.  Pokud chcete nasadit požadované prostředky SQL a souborového serveru, použijte App Service souborový server HA a šablonu SQL Server pro rychlý Start, šablona také nasadí virtuální síť.

Azure App Service v centru Azure Stack umožňuje nasadit poskytovatele prostředků do existující virtuální sítě nebo umožňuje vytvořit virtuální síť jako součást nasazení. Použití existující virtuální sítě umožňuje používat interní IP adresy pro připojení k souborovému serveru a SQL Server vyžaduje Azure App Service v Azure Stackm centru. Aby bylo možné Azure App Service do centra Azure Stack nainstalovat, musí být virtuální síť nakonfigurovaná s následujícím rozsahem adres a podsítí.

Virtuální síť-/16

Podsítě

- ControllersSubnet/24
- ManagementServersSubnet/24
- FrontEndsSubnet/24
- PublishersSubnet/24
- WorkersSubnet/21

>[!IMPORTANT]
> Pokud se rozhodnete nasadit App Service do existující virtuální sítě, SQL Server by se měl nasadit do samostatné podsítě ze App Service a souborového serveru.
>

### <a name="create-an-identity-application-to-enable-sso-scenarios"></a>Vytvoření aplikace identity pro povolení scénářů jednotného přihlašování

Azure App Service používá aplikaci identit (instanční objekt) k podpoře následujících operací:

- Integrace sady škálování virtuálních počítačů na úrovních pracovního procesu.
- Jednotné přihlašování pro portál Azure Functions a nástroje Advanced Developer Tools (Kudu).

V závislosti na tom, který poskytovatel identity Azure Stack centrum používá, Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) (ADFS), musíte podle následujících pokynů vytvořit instanční objekt pro použití Azure App Service na poskytovateli prostředků centra Azure Stack.

::: zone pivot="state-connected"
#### <a name="create-an-azure-ad-app"></a>Vytvoření Aplikace Azure AD

Pomocí těchto kroků vytvořte instanční objekt v tenantovi služby Azure AD:

1. Otevření instance prostředí PowerShell jako azurestack\AzureStackAdmin.
1. Přejít do umístění skriptů, které jste stáhli a extrahovali v [kroku požadavků](azure-stack-app-service-before-you-get-started.md).
1. [Nainstalujte PowerShell pro centrum Azure Stack](powershell-install-az-module.md).
1. Spusťte skript **Create-AADIdentityApp.ps1** . Po zobrazení výzvy zadejte ID tenanta Azure AD, které používáte pro nasazení centra Azure Stack. Zadejte například **myazurestack.onmicrosoft.com**.
1. V okně **pověření** zadejte účet správce služby Azure AD a heslo. Vyberte **OK**.
1. Zadejte cestu k souboru certifikátu a heslo certifikátu pro certifikát, který jste [vytvořili dříve](azure-stack-app-service-before-you-get-started.md). Certifikát vytvořený pro tento krok ve výchozím nastavení je **SSO. AppService. Local. azurestack. external. pfx**.
1. Poznamenejte si ID aplikace, které se vrátí ve výstupu PowerShellu. Pomocí ID v následujících krocích můžete vyjádřit souhlas s oprávněními aplikace a během instalace. 
1. Otevřete nové okno prohlížeče a přihlaste se k [Azure Portal](https://portal.azure.com) jako správce služby Azure Active Directory.
1. Otevřete službu Azure Active Directory.
1. V levém podokně vyberte **Registrace aplikací** .
1. Vyhledejte ID aplikace, které jste si poznamenali v kroku 7. 
1. V seznamu vyberte App Service registraci aplikace.
1. V levém podokně vyberte **oprávnění rozhraní API** .
1. Vyberte **udělit souhlas správce pro \<tenant\>**, kde \<tenant\> je název vašeho tenanta Azure AD. Kliknutím na **Ano** potvrďte udělení souhlasu.

```powershell
    Create-AADIdentityApp.ps1
```

| Parametr | Požadované nebo volitelné | Výchozí hodnota | Description |
| --- | --- | --- | --- |
| DirectoryTenantName | Vyžadováno | Null | ID tenanta Azure AD. Zadejte GUID nebo řetězec. Příkladem je myazureaaddirectory.onmicrosoft.com. |
| AdminArmEndpoint | Vyžadováno | Null | Správce Azure Resource Manager koncový bod. Příkladem je adminmanagement. Local. azurestack. external. |
| TenantARMEndpoint | Vyžadováno | Null | Koncový bod klienta Azure Resource Manager. Příkladem může být Management. Local. azurestack. external. |
| AzureStackAdminCredential | Vyžadováno | Null | Přihlašovací údaje správce služby Azure AD. |
| CertificateFilePath | Vyžadováno | Null | **Úplná cesta** k souboru certifikátu aplikace identity vygenerovaného dříve. |
| CertificatePassword | Vyžadováno | Null | Heslo, které pomáhá chránit privátní klíč certifikátu. |
| Prostředí | Volitelné | AzureCloud | Název podporovaného cloudového prostředí, ve kterém je dostupná cílová služba Azure Active Directory Graph.  Povolené hodnoty: "AzureCloud", "AzureChinaCloud", "AzureUSGovernment", "AzureGermanCloud".|
::: zone-end

#### <a name="create-an-adfs-app"></a>Vytvoření aplikace ADFS

1. Otevření instance prostředí PowerShell jako azurestack\AzureStackAdmin.
1. Přejít do umístění skriptů, které jste stáhli a extrahovali v [kroku požadavků](azure-stack-app-service-before-you-get-started.md).
1. [Nainstalujte PowerShell pro centrum Azure Stack](powershell-install-az-module.md).
1. Spusťte skript **Create-ADFSIdentityApp.ps1** .
1. V okně **přihlašovací údaje** zadejte AD FS účet správce cloudu a heslo. Vyberte **OK**.
1. Zadejte cestu k souboru certifikátu a heslo certifikátu pro certifikát, který jste [vytvořili dříve](azure-stack-app-service-before-you-get-started.md). Certifikát vytvořený pro tento krok ve výchozím nastavení je **SSO. AppService. Local. azurestack. external. pfx**.

```powershell
    Create-ADFSIdentityApp.ps1
```

| Parametr | Požadované nebo volitelné | Výchozí hodnota | Description |
| --- | --- | --- | --- |
| AdminArmEndpoint | Vyžadováno | Null | Správce Azure Resource Manager koncový bod. Příkladem je adminmanagement. Local. azurestack. external. |
| PrivilegedEndpoint | Vyžadováno | Null | Privilegovaný koncový bod. Příkladem je AzS-ERCS01. |
| CloudAdminCredential | Vyžadováno | Null | Přihlašovací údaje účtu domény pro cloudové správce Azure Stack hub Příkladem je Azurestack\CloudAdmin. |
| CertificateFilePath | Vyžadováno | Null | **Úplná cesta** k souboru PFX pro certifikát aplikace identity |
| CertificatePassword | Vyžadováno | Null | Heslo, které pomáhá chránit privátní klíč certifikátu. |

<!--Connected/Disconnected-->

### <a name="download-items-from-the-azure-marketplace"></a>Stáhnout položky z Azure Marketplace

Azure App Service v centru Azure Stack vyžaduje [stažení položek z Azure Marketplace](azure-stack-download-azure-marketplace-item.md), takže jsou k dispozici v Marketplace centra Azure Stack. Tyto položky je nutné stáhnout před zahájením nasazení nebo upgradu Azure App Service v centru Azure Stack:

<!-- Connected --->
::: zone pivot="state-connected"
> [!IMPORTANT]
> Jádro Windows serveru není podporovaná image platformy pro použití s Azure App Service v centru Azure Stack.
>
> Nepoužívejte zkušební image pro produkční nasazení.
>
1. **Nejnovější verze image virtuálního počítače datacenter Windows serveru 2016**
::: zone-end

::: zone pivot="state-disconnected"
<!-- Disconnected --->
1. **Windows Server 2016 Datacenter Full VM Image s aktivovanou Microsoft.NET 3.5.1 SP1**  Azure App Service v centru Azure Stack vyžaduje, aby byla na imagi používané k nasazení aktivována součást Microsoft .NET 3.5.1 SP1. Tržiště: v předdefinovaných bitových kopiích Windows serveru 2016 není tato funkce povolená a v odpojených prostředích není možné získat přístup Microsoft Update ke stažení balíčků pro instalaci prostřednictvím DISM. Proto je nutné vytvořit a použít bitovou kopii systému Windows Server 2016 s touto funkcí, která je předem povolena s odpojeným nasazením.

   Podrobnosti o vytvoření vlastní image a přidání do Marketplace najdete v tématu [Přidání vlastní image virtuálního počítače do centra Azure Stack](azure-stack-add-vm-image.md) . Při přidávání image do Marketplace Nezapomeňte zadat následující vlastnosti:

   - Vydavatel = MicrosoftWindowsServer
   - Nabídka = WindowsServer
   - SKU = 2016 – Datacenter
   - Verze = zadejte nejnovější verzi

::: zone-end

<!-- For All --> 
2. **Rozšíření vlastních skriptů v 1.9.1 nebo novějším**. Tato položka je rozšířením virtuálního počítače.

## <a name="next-steps"></a>Další kroky

[Instalace poskytovatele prostředků App Service](azure-stack-app-service-deploy.md)
