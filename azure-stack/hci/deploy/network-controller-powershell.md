---
title: Nasazení síťového adaptéru pomocí Windows PowerShellu
description: Informace o nasazení síťového adaptéru pomocí Windows PowerShellu
author: v-dasis
ms.topic: how-to
ms.date: 09/22/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e217c8b3e2a67dafa121fe752b66af9f24f888a1
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91899546"
---
# <a name="deploy-network-controller-using-windows-powershell"></a>Nasazení síťového adaptéru pomocí Windows PowerShellu

> Platí pro Azure Stack HCL verze 20H2; Windows Server 2019

Toto téma poskytuje pokyny k nasazení síťového adaptéru na jeden nebo více virtuálních počítačů, které jsou spuštěny v clusteru Azure Stack HCI, pomocí prostředí Windows PowerShell. Síťový adaptér je součástí softwarově definovaných sítí (SDN).

>[!NOTE]
>Síťový adaptér můžete nasadit také pomocí Průvodce vytvořením clusteru v centru pro správu systému Windows. Další informace najdete v tématu [Vytvoření clusteru Azure Stack HCI pomocí centra pro správu systému Windows](create-cluster.md).

## <a name="using-windows-powershell"></a>Použití Windows PowerShellu

PowerShell můžete buď spustit místně v relaci vzdálené plochy (RDP) na hostitelském serveru, nebo můžete spustit PowerShell vzdáleně z počítače pro správu.

Při spuštění prostředí PowerShell z počítače pro správu zahrňte `-Name` `-Cluster` parametr nebo s názvem serveru nebo clusteru, který spravujete. Kromě toho může být při použití parametru pro server potřeba zadat plně kvalifikovaný název domény (FQDN) `-ComputerName` .

Pro Hyper-V a clustering s podporou převzetí služeb při selhání budete potřebovat také rutiny Nástroje pro vzdálenou správu serveru (RSAT) a moduly prostředí PowerShell. Pokud nejsou již v relaci PowerShellu v počítači pro správu k dispozici, můžete je přidat pomocí následujícího příkazu: `Add-WindowsFeature RSAT-Clustering-PowerShell` .

## <a name="install-the-network-controller-server-role"></a>Instalace role serveru síťového adaptéru

Tento postup slouží k instalaci role serveru síťového adaptéru na virtuálním počítači (VM).

>[!IMPORTANT]
>Nesaďte roli serveru síťového adaptéru na fyzických hostitelích. K nasazení síťového adaptéru je potřeba nainstalovat roli serveru síťového adaptéru na virtuální počítač Hyper-V, který je nainstalovaný na hostiteli Hyper-V. Po nainstalování síťového adaptéru na virtuálních počítačích na třech různých hostitelích Hyper-V je nutné povolit hostitele Hyper-V pro softwarově definované sítě (SDN) přidáním hostitelů do síťového adaptéru. Tím umožníte, aby software SDN Load Balancer fungovat.

K provedení tohoto postupu se vyžaduje členství ve skupině **Administrators** nebo ekvivalentní.  

>[!NOTE]
>Pokud chcete k instalaci síťového adaptéru použít Správce serveru místo Windows PowerShellu, přečtěte si téma [instalace role serveru síťového adaptéru pomocí Správce serveru](/windows-server/networking/sdn/technologies/network-controller/install-the-network-controller-server-role-using-server-manager)

Síťový adaptér nainstalujete tak, že zadáte následující příkazy:

```powershell
Install-WindowsFeature -Name NetworkController -IncludeManagementTools
```

Instalace síťového adaptéru vyžaduje restartování počítače. Uděláte to tak, že zadáte tento příkaz:

```powershell
Restart-Computer
```

## <a name="configure-the-network-controller-cluster"></a>Konfigurace clusteru síťového adaptéru

Cluster síťového adaptéru poskytuje vysokou dostupnost a škálovatelnost aplikace síťového adaptéru, kterou můžete nakonfigurovat po vytvoření clusteru a který je hostovaný na clusteru.

>[!NOTE]
>Postupy v následujících částech můžete provádět buď přímo na VIRTUÁLNÍm počítači, na který jste nainstalovali síťový adaptér, nebo můžete provádět tyto postupy ze vzdáleného počítače, na kterém běží centrum pro správu systému Windows. K provedení tohoto postupu se navíc vyžaduje členství ve skupině **Administrators** nebo ekvivalentní. Pokud je počítač nebo virtuální počítač, na který jste nainstalovali síťový adaptér, připojený k doméně, musí být uživatelský účet členem skupiny **Domain Users** .

Cluster síťového adaptéru můžete vytvořit tak, že vytvoříte objekt uzlu a pak nakonfigurujete cluster.

### <a name="create-a-node-object"></a>Vytvoření objektu Node

Musíte vytvořit objekt Node pro každý virtuální počítač, který je členem clusteru síťového adaptéru.

Chcete-li vytvořit objekt uzlu, zadejte následující příkaz. Ujistěte se, že používáte hodnoty pro každý parametr, který je vhodný pro vaše nasazení.  

```powershell
New-NetworkControllerNodeObject -Name <string> -Server "ServerName" -FaultDomain "SiteName" -RestInterface "Name" [-NodeCertificate <X509Certificate2>]
```

Následující tabulka uvádí popisy pro každý parametr `New-NetworkControllerNodeObject` příkazu.

|Parametr|Popis|
|-------------|---------------|
|Název|Parametr **Name** Určuje popisný název serveru, který chcete přidat do clusteru.|
|Server|Parametr **serveru** Určuje název hostitele, plně kvalifikovaný název domény (FQDN) nebo IP adresu serveru, který chcete přidat do clusteru. Pro počítače připojené k doméně se vyžaduje plně kvalifikovaný název domény.|
|FaultDomain|Parametr **FaultDomain** určuje doménu selhání serveru, který přidáváte do clusteru. Tento parametr definuje servery, které se můžou setkat s selháním ve stejnou dobu jako server, který přidáváte do clusteru. Tato chyba může být způsobena sdílenými fyzickými závislostmi, jako jsou zdroje napájení a sítě. Domény selhání typicky představují hierarchie, které se vztahují k těmto sdíleným závislostem. další servery pravděpodobně selžou společně z vyššího bodu stromu domén selhání. Během běhu se síťový adaptér považuje za domény selhání v clusteru a pokusí se rozšířit služby síťového adaptéru tak, aby byly v samostatných doménách selhání. Tento postup pomáhá zajistit, aby v případě selhání některé domény selhání nebyla ohrožena dostupnost této služby a jejího stavu. Doména selhání je určena v hierarchickém formátu. Například: "FD:/DC1/Rack1/Hostitel1", kde DC1 je název datového centra, Rack1 je název stojanu a Hostitel1 je název hostitele, ve kterém je uzel umístěn.|
|RestInterface|Parametr **RestInterface** Určuje název rozhraní v uzlu, ve kterém je ukončená komunikace Rest (Presentation state transfer). Toto rozhraní síťového adaptéru přijímá požadavky rozhraní API Northbound z vrstvy správy sítě.|
|NodeCertificate|Parametr **NodeCertificate** Určuje certifikát, který používá síťový adaptér pro ověřování počítače. Certifikát je vyžadován, pokud pro komunikaci v rámci clusteru používáte ověřování pomocí certifikátů. certifikát se používá také k šifrování provozu mezi službami síťového adaptéru. Název subjektu certifikátu musí být stejný jako název DNS uzlu.|

### <a name="configure-the-cluster"></a>Konfigurace clusteru

Pokud chcete nakonfigurovat cluster, zadejte následující příkaz. Ujistěte se, že používáte hodnoty pro každý parametr, který je vhodný pro vaše nasazení.

```powershell
Install-NetworkControllerCluster -Node "NetworkControllerNodeName" -ClusterAuthentication "ClusterAuthenticationType" [-ManagementSecurityGroup <string>][-DiagnosticLogLocation <string>][-LogLocationCredential <PSCredential>] [-CredentialEncryptionCertificate <X509Certificate2>][-Credential <PSCredential>][-CertificateThumbprint <String>] [-UseSSL][-ComputerName <string>][-LogSizeLimitInMBs<UInt32>] [-LogTimeLimitInDays<UInt32>]
```

Následující tabulka uvádí popisy pro každý parametr `Install-NetworkControllerCluster` příkazu.
  
|Parametr|Popis|
|-------------|---------------|
|ClusterAuthentication|Parametr **ClusterAuthentication** určuje typ ověřování, který se používá k zabezpečení komunikace mezi uzly a používá se také pro šifrování provozu mezi službami síťového adaptéru. Podporovány jsou hodnoty **Kerberos**, **x509** a **none**. Ověřování protokolem Kerberos používá doménové účty a dá se použít jenom v případě, že uzly síťového adaptéru jsou připojené k doméně. Pokud zadáte ověřování na základě x509, je nutné zadat certifikát v objektu NetworkControllerNode. Kromě toho musíte certifikát před spuštěním tohoto příkazu ručně zřídit.|
|ManagementSecurityGroup|Parametr **ManagementSecurityGroup** Určuje název skupiny zabezpečení, která obsahuje uživatele, kteří mají povolené spouštět rutiny správy ze vzdáleného počítače. To platí pouze v případě, že ClusterAuthentication je Kerberos. V místním počítači je nutné zadat skupinu zabezpečení domény a nikoli skupinu zabezpečení.|
|Node|Parametr **Node** určuje seznam uzlů síťového adaptéru, které jste vytvořili pomocí příkazu **New-NetworkControllerNodeObject** .|
|DiagnosticLogLocation|Parametr **DiagnosticLogLocation** určuje umístění sdílené složky, kde jsou protokoly diagnostiky pravidelně odesílány. Pokud nezadáte hodnotu pro tento parametr, protokoly budou uloženy místně na každém uzlu. Protokoly se ukládají místně do složky%systemdrive%\Windows\tracing\SDNDiagnostics. Protokoly clusteru se ukládají místně ve složce%systemdrive%\ProgramData\Microsoft\Service Fabric\log\Traces..|
|LogLocationCredential|Parametr **LogLocationCredential** určuje přihlašovací údaje, které jsou požadovány pro přístup k umístění sdílené složky, kde jsou uloženy protokoly.|
|CredentialEncryptionCertificate|Parametr **CredentialEncryptionCertificate** Určuje certifikát, který síťový adaptér používá k šifrování přihlašovacích údajů, které se používají pro přístup ke binárním souborům síťového adaptéru a **LogLocationCredential**, pokud je zadané. Certifikát musí být zřízen na všech uzlech síťového adaptéru před spuštěním tohoto příkazu a stejný certifikát musí být zaregistrovaný na všech uzlech clusteru. Použití tohoto parametru k ochraně binárních souborů a protokolů síťového adaptéru se doporučuje v produkčních prostředích. Bez tohoto parametru jsou přihlašovací údaje uložené ve formě prostého textu a můžou je nepoužívat neoprávněný uživatel.|
|Přihlašovací údaj|Tento parametr je vyžadován pouze v případě, že tento příkaz spouštíte ze vzdáleného počítače. Parametr **Credential** Určuje uživatelský účet, který má oprávnění ke spuštění tohoto příkazu v cílovém počítači.|
|CertificateThumbprint|Tento parametr je vyžadován pouze v případě, že tento příkaz spouštíte ze vzdáleného počítače. Parametr **CertificateThumbprint** Určuje certifikát digitálního veřejného klíče (x509) uživatelského účtu, který má oprávnění ke spuštění tohoto příkazu v cílovém počítači.|
|UseSSL|Tento parametr je vyžadován pouze v případě, že tento příkaz spouštíte ze vzdáleného počítače. Parametr **UseSSL** určuje protokol SSL (SSL (Secure Sockets Layer)), který se používá k navázání připojení ke vzdálenému počítači. Ve výchozím nastavení se SSL nepoužívá.|
|ComputerName|Parametr **ComputerName** určuje uzel síťového adaptéru, na kterém je tento příkaz spuštěný. Pokud hodnotu pro tento parametr nezadáte, použije se ve výchozím nastavení místní počítač.|
|LogSizeLimitInMBs|Tento parametr určuje maximální velikost protokolu (v MB), kterou může síťový adaptér ukládat. Protokoly se ukládají cyklickým způsobem. Pokud je k dispozici DiagnosticLogLocation, výchozí hodnota tohoto parametru je 40 GB. Pokud není zadaný DiagnosticLogLocation, protokoly se ukládají na uzlech síťového adaptéru a výchozí hodnota tohoto parametru je 15 GB.|
|LogTimeLimitInDays|Tento parametr určuje limit doby trvání ve dnech, pro které jsou uloženy protokoly. Protokoly se ukládají cyklickým způsobem. Výchozí hodnota tohoto parametru je 3 dny.|

## <a name="configure-the-network-controller-application"></a>Konfigurace aplikace síťového adaptéru

Chcete-li nakonfigurovat aplikaci síťového adaptéru, zadejte následující příkaz. Ujistěte se, že používáte hodnoty pro každý parametr, který je vhodný pro vaše nasazení.

```powershell
Install-NetworkController -Node <NetworkControllerNode[]> -ClientAuthentication <ClientAuthentication>  [-ClientCertificateThumbprint <string[]>]  [-ClientSecurityGroup <string>] -ServerCertificate <X509Certificate2> [-RESTIPAddress <String>] [-RESTName <String>] [-Credential <PSCredential>][-CertificateThumbprint <String> ] [-UseSSL]
```

Následující tabulka uvádí popisy pro každý parametr `Install-NetworkController` příkazu.

|Parametr|Popis|
|-------------|---------------|
|Ověřeníklienta|Parametr **ověřeníklienta** určuje typ ověřování, který se používá k zabezpečení komunikace mezi Rest a síťovým adaptérem. Podporovány jsou hodnoty **Kerberos**, **x509** a **none**. Ověřování protokolem Kerberos používá doménové účty a dá se použít jenom v případě, že uzly síťového adaptéru jsou připojené k doméně. Pokud zadáte ověřování na základě x509, je nutné zadat certifikát v objektu NetworkControllerNode. Kromě toho musíte certifikát před spuštěním tohoto příkazu ručně zřídit.|
|Node|Parametr **Node** určuje seznam uzlů síťového adaptéru, které jste vytvořili pomocí příkazu **New-NetworkControllerNodeObject** .|
|ClientCertificateThumbprint|Tento parametr je vyžadován pouze v případě, že používáte ověřování založené na certifikátech pro klienty síťového adaptéru. Parametr **ClientCertificateThumbprint** určuje kryptografický otisk certifikátu, který je zaregistrovaný do klientů ve vrstvě Northbound.|
|ServerCertificate|Parametr **ServerCertificate** Určuje certifikát, který síťový adaptér používá k prokázání jeho identity klientům. Certifikát serveru musí zahrnovat účel ověřování serveru v rozšířeních použití rozšířeného klíče a musí být vydaný pro síťový adaptér certifikační autoritou, která je pro klienty důvěryhodná.|
|RESTIPAddress|Nemusíte zadávat hodnotu pro **RESTIPAddress** s nasazením síťového adaptéru s jedním uzlem. U nasazení s více uzly určuje parametr **RESTIPADDRESS** IP adresu koncového bodu REST v zápisu CIDR. Například 192.168.1.10/24. Hodnota názvu subjektu **ServerCertificate** musí překládat na hodnotu parametru **RESTIPAddress** . Tento parametr je nutné zadat pro všechna nasazení síťového adaptéru s více uzly, pokud jsou všechny uzly ve stejné podsíti. Pokud jsou uzly v různých podsítích, je nutné použít parametr **odpočinku** namísto použití **RESTIPAddress**.|
|RestName|V rámci nasazení síťového adaptéru s jedním uzlem nemusíte zadávat hodnotu pro hodnoty pro **Nastavení** . Jediným okamžikem, kdy je nutné zadat hodnotu pro pole pro **odpočinek** , je, že nasazení na více uzlech má uzly v různých podsítích. U nasazení s více uzly určuje parametr **REST** plně kvalifikovaný název domény pro cluster síťového adaptéru.|
|ClientSecurityGroup|Parametr **ClientSecurityGroup** Určuje název skupiny zabezpečení služby Active Directory, jejíž členové jsou klienti síťového adaptéru. Tento parametr je vyžadován pouze v případě, že používáte ověřování protokolem Kerberos pro **ověřeníklienta**. Skupina zabezpečení musí obsahovat účty, ze kterých jsou k rozhraní REST API přistupované, a před spuštěním tohoto příkazu musíte vytvořit skupinu zabezpečení a přidat členy.|
|Přihlašovací údaj|Tento parametr je vyžadován pouze v případě, že tento příkaz spouštíte ze vzdáleného počítače. Parametr **Credential** Určuje uživatelský účet, který má oprávnění ke spuštění tohoto příkazu v cílovém počítači.|
|CertificateThumbprint|Tento parametr je vyžadován pouze v případě, že tento příkaz spouštíte ze vzdáleného počítače. Parametr **CertificateThumbprint** Určuje certifikát digitálního veřejného klíče (x509) uživatelského účtu, který má oprávnění ke spuštění tohoto příkazu v cílovém počítači.|
|UseSSL|Tento parametr je vyžadován pouze v případě, že tento příkaz spouštíte ze vzdáleného počítače. Parametr **UseSSL** určuje protokol SSL (SSL (Secure Sockets Layer)), který se používá k navázání připojení ke vzdálenému počítači. Ve výchozím nastavení se SSL nepoužívá.|

Po dokončení konfigurace aplikace síťového adaptéru se nasazení síťového adaptéru dokončí.

## <a name="network-controller-deployment-validation"></a>Ověření nasazení síťového adaptéru

K ověření nasazení síťového adaptéru můžete přidat přihlašovací údaje k síťovému adaptéru a pak načíst přihlašovací údaje.

Pokud jako typ používáte protokol Kerberos `ClientAuthentication` , je nutné k provedení tohoto postupu použít členství ve skupině **ClientSecurityGroup** , kterou jste vytvořili.

1. Pokud v klientském počítači používáte jako `ClientAuthentication` typ Kerberos, přihlaste se pomocí uživatelského účtu, který je členem vaší skupiny **ClientSecurityGroup** .

1. V PowerShellu zadejte následující příkazy. Ujistěte se, že používáte hodnoty pro každý parametr, který je vhodný pro vaše nasazení.

    ```powershell
    $cred=New-Object Microsoft.Windows.Networkcontroller.credentialproperties
    $cred.type="usernamepassword"
    $cred.username="admin"
    $cred.value="abcd"

    New-NetworkControllerCredential -ConnectionUri "https://networkcontroller"-Properties $cred -ResourceId "cred1"
    ```

1. Pokud chcete načíst přihlašovací údaje, které jste přidali do síťového adaptéru, zadejte následující příkaz. Ujistěte se, že používáte hodnoty pro každý parametr, který je vhodný pro vaše nasazení.

    ```powershell
    Get-NetworkControllerCredential -ConnectionUri https://networkcontroller -ResourceId cred1  
    ```

1. Zkontrolujte výstup příkazu, který by měl být podobný následujícímu příkladu výstupu.

    ```powershell
    Tags                   :
    ResourceRef     : /credentials/cred1
    CreatedTime    : 1/1/0001 12:00:00 AM
    InstanceId        : e16ffe62-a701-4d31-915e-7234d4bc5a18
    Etag                  : W/"1ec59631-607f-4d3e-ac78-94b0822f3a9d"
    ResourceMetadata :
    ResourceId       : cred1
    Properties       : Microsoft.Windows.NetworkController.CredentialProperties
    ```

    > [!NOTE]
    > Když `Get-NetworkControllerCredential` příkaz spustíte, můžete k proměnné přiřadit výstup příkazu pomocí operátoru tečka a zobrazit tak vlastnosti přihlašovacích údajů. Například: `$cred.Properties`.

## <a name="additional-powershell-commands-for-network-controller"></a>Další příkazy PowerShellu pro síťový adaptér

Po nasazení síťového adaptéru můžete pomocí příkazů PowerShellu spravovat a upravovat svoje nasazení. Níže jsou uvedené některé změny, které můžete provést v nasazení.

- Úprava nastavení uzlu síťového adaptéru, clusteru a aplikace

- Odebrat cluster síťového adaptéru a aplikaci

- Spravujte uzly clusteru síťového adaptéru, včetně přidávání, odebírání, povolování a zakazování uzlů.

Následující tabulka uvádí syntaxi příkazů PowerShellu, které můžete použít k provedení těchto úloh.

|Úloha|Příkaz|Syntax|
|--------|-------|----------|
|Úprava nastavení clusteru síťového adaptéru|Set-NetworkControllerCluster|`Set-NetworkControllerCluster [-ManagementSecurityGroup <string>][-Credential <PSCredential>] [-computerName <string>][-CertificateThumbprint <String> ] [-UseSSL]`
|Upravit nastavení aplikace síťového adaptéru|Set-NetworkController|`Set-NetworkController [-ClientAuthentication <ClientAuthentication>] [-Credential <PSCredential>] [-ClientCertificateThumbprint <string[]>] [-ClientSecurityGroup <string>] [-ServerCertificate <X509Certificate2>] [-RestIPAddress <String>] [-ComputerName <String>][-CertificateThumbprint <String> ] [-UseSSL]`
|Změnit nastavení uzlu síťového adaptéru|Set-NetworkControllerNode|`Set-NetworkControllerNode -Name <string> > [-RestInterface <string>] [-NodeCertificate <X509Certificate2>] [-Credential <PSCredential>] [-ComputerName <string>][-CertificateThumbprint <String> ] [-UseSSL]`
|Upravit nastavení diagnostiky síťového adaptéru|Set-NetworkControllerDiagnostic|`Set-NetworkControllerDiagnostic [-LogScope <string>] [-DiagnosticLogLocation <string>] [-LogLocationCredential <PSCredential>] [-UseLocalLogLocation] >] [-LogLevel <loglevel>][-LogSizeLimitInMBs <uint32>] [-LogTimeLimitInDays <uint32>] [-Credential <PSCredential>] [-ComputerName <string>][-CertificateThumbprint <String> ] [-UseSSL]`
|Odebrání aplikace síťového adaptéru|Uninstall-NetworkController|`Uninstall-NetworkController [-Credential <PSCredential>][-ComputerName <string>] [-CertificateThumbprint <String> ] [-UseSSL]`
|Odebrat cluster síťového adaptéru|Uninstall-NetworkControllerCluster|`Uninstall-NetworkControllerCluster [-Credential <PSCredential>][-ComputerName <string>][-CertificateThumbprint <String> ] [-UseSSL]`
|Přidání uzlu do clusteru síťového adaptéru|Add-NetworkControllerNode|`Add-NetworkControllerNode -FaultDomain <String> -Name <String> -RestInterface <String> -Server <String> [-CertificateThumbprint <String> ] [-ComputerName <String> ] [-Credential <PSCredential> ] [-Force] [-NodeCertificate <X509Certificate2> ] [-PassThru] [-UseSsl]`
|Zakázat uzel clusteru síťového adaptéru|Disable-NetworkControllerNode|`Disable-NetworkControllerNode -Name <String> [-CertificateThumbprint <String> ] [-ComputerName <String> ] [-Credential <PSCredential> ] [-PassThru] [-UseSsl]`
|Povolit uzel clusteru síťového adaptéru|Enable-NetworkControllerNode|`Enable-NetworkControllerNode -Name <String> [-CertificateThumbprint <String> ] [-ComputerName <String> ] [-Credential <PSCredential> ] [-PassThru] [-UseSsl]`
|Odebrání uzlu síťového adaptéru z clusteru|Remove-NetworkControllerNode|`Remove-NetworkControllerNode [-CertificateThumbprint <String> ] [-ComputerName <String> ] [-Credential <PSCredential> ] [-Force] [-Name <String> ] [-PassThru] [-UseSsl]`

Další informace najdete v referenční dokumentaci k prostředí Windows PowerShell pro síťový adaptér na adrese [NetworkController](/powershell/module/networkcontroller/?view=win10-ps).

## <a name="sample-network-controller-configuration-script"></a>Ukázkový skript konfigurace síťového adaptéru

Následující ukázkový konfigurační skript ukazuje, jak vytvořit cluster síťového adaptéru s více uzly a nainstalovat aplikaci síťového adaptéru. Kromě toho `$cert` Proměnná vybere certifikát z úložiště certifikátů místního počítače, které se shoduje s řetězcem názvu předmětu "networkController.contoso.com".

```powershell
$a = New-NetworkControllerNodeObject -Name "Node1" -Server "NCNode1.contoso.com" -FaultDomain "fd:/rack1/host1" -RestInterface Internal
$b = New-NetworkControllerNodeObject -Name "Node2" -Server "NCNode2.contoso.com" -FaultDomain "fd:/rack1/host2" -RestInterface Internal
$c = New-NetworkControllerNodeObject -Name "Node3" -Server "NCNode3.contoso.com" -FaultDomain "fd:/rack1/host3" -RestInterface Internal

$cert= get-item Cert:\LocalMachine\My | get-ChildItem | where {$_.Subject -imatch "networkController.contoso.com" }

Install-NetworkControllerCluster -Node @($a,$b,$c)  -ClusterAuthentication Kerberos -DiagnosticLogLocation \\share\Diagnostics - ManagementSecurityGroup Contoso\NCManagementAdmins -CredentialEncryptionCertificate $cert  
Install-NetworkController -Node @($a,$b,$c) -ClientAuthentication Kerberos -ClientSecurityGroup Contoso\NCRESTClients -ServerCertificate $cert -RestIpAddress 10.0.0.1/24
```

## <a name="next-steps"></a>Další kroky

Pokud při nasazení síťového adaptéru nepoužíváte protokol Kerberos, musíte nasadit certifikáty. Další informace najdete v tématu [kroky po nasazení pro síťový adaptér](/windows-server/networking/sdn/technologies/network-controller/post-deploy-steps-nc).