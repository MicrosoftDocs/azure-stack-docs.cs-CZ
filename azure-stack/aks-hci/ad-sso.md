---
title: Použití jednotného přihlašování služby Active Directory v AKS pro Azure Stack HCL
description: Ověřování pomocí služby Active Directory pro zabezpečené připojení k serveru rozhraní API s přihlašovacími údaji jednotného přihlašování
author: v-susbo
ms.topic: how-to
ms.date: 02/12/2021
ms.author: v-susbo
ms.reviewer: ''
ms.openlocfilehash: fd4b8982ecaa9de1b3b63dd97cb460772e25971d
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873664"
---
# <a name="use-active-directory-single-sign-in-credentials-for-aks-on-azure-stack-hci"></a>Použití přihlašovacích údajů jednotného přihlašování služby Active Directory pro AKS na Azure Stack HCI

> Platí pro: AKS on Azure Stack HCI, AKS runtime na Windows serveru 2019 Datacenter

Bez ověřování pomocí služby Active Directory musí uživatelé při připojování k serveru rozhraní API spoléhat na soubor _kubeconfig_ založený na certifikátu, a to prostřednictvím `kubectl` příkazu. Soubor _kubeconfig_ obsahuje tajné kódy, jako jsou například soukromé klíče a certifikáty, které je třeba pečlivě distribuovat. To může představovat významné bezpečnostní riziko.

Jako alternativu k používání _kubeconfig_ založeného na certifikátech můžete jako zabezpečený způsob připojení k serveru rozhraní API použít přihlašovací údaje jednotného přihlašování (SSO) služby Active Directory (AD). Integrace AD se službou Azure Kubernetes Service on Azure Stack HCI umožňuje uživateli na počítači připojeném k doméně systému Windows připojit se k serveru rozhraní API (prostřednictvím `kubectl` ) pomocí svých přihlašovacích údajů jednotného přihlašování. Tím se eliminuje nutnost spravovat a distribuovat soubory _kubeconfig_ založené na certifikátech, které obsahují privátní klíče.

Integrace AD používá službu AD _kubeconfig_, která se liší od souborů _kubeconfig_ založených na certifikátech a neobsahuje žádné tajné kódy. Soubor _kubeconfig_ založený na certifikátech lze použít pro účely zálohování, jako je například řešení potíží, pokud dochází k problémům s připojením pomocí pověření služby Active Directory.

Další výhodou zabezpečení při integraci služby AD je, že uživatelé a skupiny se ukládají jako [identifikátory zabezpečení (SID)](https://docs.microsoft.com/troubleshoot/windows-server/identity/security-identifiers-in-windows). Na rozdíl od názvů skupin jsou identifikátory SID neměnné a jedinečné, a proto nepředstavují konflikty názvů.

> [!Note] 
> Pro tuto verzi Preview poskytujeme připojení AD jednotného přihlašování jenom pro clustery úloh. 

Tento dokument vás provede následujícími kroky k nastavení služby Active Directory jako zprostředkovatele identity a k povolení jednotného přihlašování prostřednictvím `kubectl` :

- Vytvořte účet AD pro Server rozhraní API a pak vytvořte soubor [keytab](https://web.mit.edu/kerberos/krb5-devel/doc/basic/keytab_def.html) přidružený k tomuto účtu. Pokud chcete vytvořit účet AD a vygenerovat soubor keytab, přečtěte si téma [Vytvoření služby AD auth pomocí souboru keytab](#create-ad-auth-using-the-keytab-file) .
- Pomocí souboru [keytab](https://web.mit.edu/kerberos/krb5-devel/doc/basic/keytab_def.html) nainstalujte službu AD auth do clusteru Kubernetes. V rámci tohoto kroku se automaticky vytvoří výchozí konfigurace řízení přístupu na základě role (RBAC).
- Převod názvů skupin na identifikátory SID a naopak při vytváření nebo úpravách konfigurací RBAC najdete v tématu [Vytvoření a aktualizace vazby role skupiny AD](#create-and-update-the-ad-group-role-binding) pro pokyny.
 
## <a name="before-you-begin"></a>Než začnete

Než začnete s konfigurací pověření jednotného přihlašování ke službě Active Directory, měli byste se ujistit, že máte k dispozici následující položky:

 - Je nainstalovaný nejnovější modul **prostředí PowerShell AKS-HCI** . Pokud to neuděláte, přečtěte si téma [Stažení a instalace modulu AksHci PowerShellu](./setup-powershell.md#step-1-download-and-install-the-akshci-powershell-module). 
 - Hostitel AKS je nainstalovaný a nakonfigurovaný. Pokud to neuděláte, postupujte podle pokynů [Konfigurace nasazení](./setup-powershell.md#step-4-configure-your-deployment).  
 - Ujistěte se, že je soubor keytab k dispozici pro použití. Pokud není, přečtěte si téma [Vytvoření účtu rozhraní API serveru a souboru keytab](#create-the-api-server-ad-account-and-the-keytab-file). 
 - Soubor keytab se vygeneruje pro konkrétní hlavní název služby (SPN) a tento hlavní název služby musí odpovídat účtu serveru služby AD API pro cluster úloh. Ujistěte se také, že se v rámci procesu ověřování AD používá stejný hlavní název služby (SPN). Soubor keytab by měl mít název _Current. keytab_.
 - Pro každý AKS Azure Stack v clusteru úlohy HCI zajistěte, aby byl k dispozici jeden účet serveru služby API.
 - Klientský počítač musí být počítač připojený k doméně systému Windows.

## <a name="create-ad-auth-using-the-keytab-file"></a>Vytvoření ověřování AD pomocí souboru keytab

### <a name="step-1-create-the-workload-cluster-and-enable-ad-authentication"></a>Krok 1: Vytvoření clusteru úloh a povolení ověřování AD

Než začnete s ověřováním služby AD, musíte nejdřív vytvořit AKS na Azure Stack clusteru pro úlohy HCI a povolit doplněk pro ověřování AD v clusteru. Pokud při vytváření nového clusteru nepovolíte ověřování služby Active Directory, nebudete ho moct později povolit.

Otevřete PowerShell jako správce a spusťte následující příkaz pomocí parametru **-enableADAuth** `New-AksHciCluster` příkazu:

```powershell
New-AksHciCluster -name mynewcluster1 -enableADAuth
```

Pro každý cluster úloh zajistěte, aby byl k dispozici jeden účet serveru služby API.

Podrobnosti o vytváření clusteru úloh najdete v tématu [Vytvoření clusterů Kubernetes pomocí Windows PowerShellu](./create-kubernetes-cluster-powershell.md).

### <a name="step-2-install-ad-authentication"></a>Krok 2: Instalace ověřování AD

Než budete moct nainstalovat ověřování AD, ujistěte se, že je cluster úloh nainstalovaný a že je v clusteru povolené ověřování AD. K instalaci ověřování AD použijte jednu z následujících možností.

#### <a name="option-1"></a>Možnost 1

V případě clusteru Azure Stack HCI připojeného k doméně otevřete PowerShell jako správce a spusťte následující příkaz:

```powershell
Install-AksHciAdAuth -name mynewcluster1 -keytab .\current.keytab -SPN k8s/apiserver@CONTOSO.COM -adminUser contoso\bob
```  

#### <a name="option-2"></a>Možnost 2

Pokud hostitel clusteru není připojený k doméně, použijte uživatelské jméno nebo název skupiny správce ve formátu SID, jak je znázorněno v následujícím příkladu:
 
```powershell
Install-AksHciAdAuth -name mynewcluster1 -keytab .\current.keytab -SPN k8
```  

Identifikátor SID pro uživatelský účet najdete v tématu [určení identifikátoru zabezpečení uživatele nebo skupiny](#determine-the-user-or-group-security-identifier). 

Než budete pokračovat k dalším krokům, měli byste si uvědomit následující položky:

- Ujistěte se, že soubor keytab má název _Current. keytab_.
- Nahraďte hlavní název služby (SPN), který odpovídá vašemu prostředí.
- **-adminUser** vytvoří odpovídající vazbu role pro ZADANOU skupinu AD s oprávněními správce clusteru. Nahraďte _contoso\bob_ skupinou nebo uživatelem služby AD, která odpovídá vašemu prostředí.

### <a name="step-3-test-the-ad-webhook-and-keytab-file"></a>Krok 3: testování Webhooku služby AD a souboru keytab

Musíte zajistit, aby Webhook služby AD běžel na serveru rozhraní API a aby byl soubor keytab uložený jako Kubernetes tajný klíč. K získání souboru _kubeconfig_ založeného na certifikátu pro cluster úloh použijte následující postup:

1. Získejte soubor _kubeconfig_ založený na certifikátech. Pomocí následujícího příkazu se připojte ke clusteru jako místní hostitel pomocí souboru _kubeconfig_ :

   ```powershell
   Get-AksHciCredential -name mynewcluster1
   ```  

2. Spusťte `kubectl` na serveru, ke kterému jste se připojili (pomocí souboru _kubeconfig_ založeného na certifikátu, který jste vytvořili dříve), a pak zkontrolujte nasazení Webhooku AD a ujistěte se, že je ve formátu _AD-auth-Webhook-xxxx_.  

    ```bash
    kubectl get pods -n=kube-system
    ```

3. Spusťte `kubectl` , chcete-li ověřit, zda je soubor keytab nasazený jako tajný klíč a je uveden jako Kubernetes tajný kód: 

   ```bash
   kubectl get secrets -n=kube-system
   ```

### <a name="step-4-create-the-ad-kubeconfig-file"></a>Krok 4: vytvoření souboru AD kubeconfig

Po úspěšném nasazení Webhooku služby Active Directory a keytab vytvořte soubor AD _kubeconfig_ .
Po vytvoření souboru zkopírujete soubor AD _kubeconfig_ do klientského počítače a použijete ho k ověření na serveru rozhraní API. Na rozdíl od souboru _kubeconfig_ založeného na certifikátu není služba AD _kubeconfig_ tajná a je bezpečná pro kopírování jako prostý text.

Otevřete PowerShell jako správce a spusťte následující příkaz:  

   ```powershell
   Get-AksHciCredential -name mynewcluster1 -outputLocation .\AdKubeconfig -adAuth
   ```

### <a name="step-5-copy-kubeconfig-and-other-files-to-the-client-machine"></a>Krok 5: kopírování kubeconfig a dalších souborů do klientského počítače

Níže uvedené tři soubory byste měli zkopírovat z Azure Stack clusteru HCI do klientského počítače:

- Zkopírujte soubor AD _kubeconfig_ , který jste vytvořili v předchozím kroku, abyste $ENV: USERPROFILE \. kube\config.

- Vytvořte cestu ke složce `c:\adsso` a zkopírujte následující soubory z clusteru Azure Stack HCI do klientského počítače.
  - Kubectl.exe v části `$env:ProgramFiles\AksHci` do c:\adsso
  - Kubectl-adsso.exe v části `$env:ProgramFiles\AksHci` do c:\adsso

### <a name="step-6-connect-to-the-api-server-from-the-client-machine"></a>Krok 6: připojení k serveru rozhraní API z klientského počítače

Po dokončení předchozích kroků se přihlaste k počítači klienta připojenému k doméně systému Windows pomocí přihlašovacích údajů jednotného přihlašování. Otevřete PowerShell a potom se pokuste získat přístup k serveru rozhraní API pomocí `kubectl` . Pokud budete vyzváni k ověření, pak jste úspěšně nastavili službu AD SSO.

## <a name="create-and-update-the-ad-group-role-binding"></a>Vytvoření a aktualizace vazby role skupiny AD

Jak je uvedeno v kroku 2, vytvoří se výchozí vazba role s oprávněními správce clusteru pro uživatele a/nebo skupinu, která byla k dispozici během instalace. Vazba role v Kubernetes definuje zásady přístupu pro skupiny AD. Tento krok popisuje, jak pomocí RBAC vytvořit nové vazby role skupiny AD v Kubernetes a upravit existující vazby rolí. Správce clusteru může například chtít udělit uživatelům další oprávnění pomocí skupin služby AD (což je proces efektivnější). Další informace o RBAC najdete v tématu [použití autorizace RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/).

Při vytváření nebo úpravách více položek RBAC skupiny AD by měl být název subjektu předem vyřešen " `microsoft:activedirectory:CONTOSO\\group name` ". Všimněte si, že názvy musí obsahovat název domény a předponu, které jsou uzavřeny dvojitými uvozovkami.

Tady jsou dva příklady:

**Příklad 1**

```bash
apiVersion: rbac.authorization.k8s.io/v1 
 kind: ClusterRoleBinding 
 metadata: 
   name: ad-user-cluster-admin 
 roleRef: 
   apiGroup: rbac.authorization.k8s.io 
   kind: ClusterRole 
   name: cluster-admin 
 subjects: 
 - apiGroup: rbac.authorization.k8s.io 
   kind: User 
   name: "microsoft:activedirectory:CONTOSO\Bob" 
```

**Příklad 2**

Následující příklad ukazuje, jak vytvořit vlastní vazbu role a role pro [obor názvů](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) se skupinou AD. V tomto příkladu je "SREGroup" již existující skupinou ve službě contoso Active Directory. Když se uživatelé přidají do skupiny AD, okamžitě se jim udělí oprávnění.

```bash
kind: Role 
 apiVersion: rbac.authorization.k8s.io/v1 
 metadata: 
   name: sre-user-full-access 
   namespace: sre 
 rules: 
 - apiGroups: ["", "extensions", "apps"] 
   resources: ["*"] 
   verbs: ["*"] 
 - apiGroups: ["batch"] 
   resources: 
   - jobs 
   - cronjobs 
   verbs: ["*"] 
 apiVersion: rbac.authorization.k8s.io/v1 
 kind: RoleBinding 
 namespace: sre 
 metadata: 
   name: ad-user-cluster-admin 
 roleRef: 
   apiGroup: rbac.authorization.k8s.io 
   kind: Role 
   name: sre-user-full-access 
 subjects: 
 - apiGroup: rbac.authorization.k8s.io 
   kind: User 
   name: "microsoft:activedirectory:CONTOSO\SREGroup" 
```

Před použitím souboru YAML by se **názvy skupin a uživatelů** měly vždycky převést na identifikátory SID pomocí příkazu:

```bash
kubectl-adsso nametosid <rbac.yml>
```  

Podobně platí, že pokud chcete aktualizovat existující RBAC, můžete před provedením změn převést identifikátor SID na uživatelsky přívětivý název skupiny. K převodu identifikátoru SID použijte příkaz: 

```bash
kubectl-adsso sidtoname <rbac.yml>
```

## <a name="change-the-ad-account-password-associated-with-the-api-server-account"></a>Změna hesla účtu služby Active Directory přidruženého k účtu serveru API

Po změně hesla pro účet serveru rozhraní API musíte odinstalovat doplněk ověřování AD a znovu ho nainstalovat pomocí aktualizované aktuální a předchozí karty. 

Pokaždé, když změníte heslo, musíte přejmenovat aktuální keytab (_Current. keytab_) na _předchozí. keytab_ a pak se ujistěte, že nové heslo zadáte jako _aktuální. keytab_.

> [!Important] 
> Soubory musí být pojmenovány _Current. keytab_ a _Previous. keytab_ v uvedeném pořadí. Stávající vazby role to neovlivní.

### <a name="uninstall-and-reinstall-ad-authentication"></a>Odinstalace a opětovná instalace ověřování AD

Je možné, že budete chtít znovu nainstalovat službu AD SSO, když se změní účet serveru rozhraní API, když se heslo aktualizuje nebo pokud chcete vyřešit chybu.

Pokud chcete odinstalovat ověřování AD, otevřete PowerShell jako správce a spusťte následující příkaz:  

```powershell
Uninstall-AksHciAdAuth -name mynewcluster1
```

Pokud chcete znovu nainstalovat ověřování AD, otevřete PowerShell jako správce a spusťte následující příkaz:

```powershell
Install-AksHciAdAuth -name mynewcluster1 -keytab <.\current.keytab> -previousKeytab <.\previous.keytab> -SPN <service/principal@CONTOSO.COM> -adminUser CONTOSO\Bob
```

> [!Note] 
> `-previousKeytab`Parametr je vyžadován pouze při změně hesla. To znamená, že se vyhnete výpadkům, pokud mají klienti uložené lístky do mezipaměti.

## <a name="create-the-api-server-ad-account-and-the-keytab-file"></a>Vytvoření účtu serveru API služby AD a souboru keytab

Tento proces se zabývá dvěma kroky. Nejdřív vytvořte nový účet AD/uživatele pro Server rozhraní API s hlavním názvem služby (SPN) a druhým vytvořením souboru keytab pro účet AD.

### <a name="step-1-create-a-new-ad-account-or-user-for-the-api-server"></a>Krok 1: vytvoření nového účtu služby AD nebo uživatele pro Server rozhraní API

Pomocí příkazu prostředí PowerShell [New-ADUser](https://docs.microsoft.com/powershell/module/addsadministration/new-aduser?view=win10-ps&preserve-view=true) vytvořte nový účet nebo uživatele služby Active Directory pomocí hlavního názvu služby (SPN). Tady je příklad: 

```powershell 
New-ADUser -Name apiserver -ServicePrincipalNames k8s/apiserver -AccountPassword (ConvertTo-SecureString "password" -AsPlainText -Force) -KerberosEncryptionType AES128 -Enabled 1
```

### <a name="step-2-create-the-keytab-file-for-the-ad-account"></a>Krok 2: vytvoření souboru keytab pro účet služby AD

K vytvoření souboru keytab použijte příkaz [Ktpass](https://docs.microsoft.com/windows-server/administration/windows-commands/ktpass) Windows. 

Tady je příklad s použitím Ktpass:

```bash
ktpass /out current.keytab /princ k8s/apiserver@BCONTOSO.COM /mapuser contoso\apiserver_acct /crypto all /pass p@$$w0rd /ptype KRB5_NT_PRINCIPAL
```

> [!NOTE]
> Pokud se zobrazí tato chyba, **DsCrackNames vrátila do položky název 0x2**, ujistěte se, že parametr pro `/mapuser` je ve formátu, `mapuser DOMAIN\user` kde doména je výstupem ozvěny `%userdomain%` .

  
## <a name="determine-the-user-or-group-security-identifier"></a>Určení identifikátoru zabezpečení uživatele nebo skupiny

Použijte jednu z následujících dvou možností k vyhledání identifikátoru SID pro váš účet nebo pro jiné účty.

Pokud chcete najít identifikátor SID přidružený k vašemu účtu, zadejte na příkazovém řádku domovského adresáře tento příkaz:

```bash
whoami /user
``` 

Pokud chcete najít identifikátor SID přidružený k jinému účtu, otevřete PowerShell jako správce a spusťte tento příkaz:

```powershell
(New-Object System.Security.Principal.NTAccount(<CONTOSO\Bob>)).Translate([System.Security.Principal.SecurityIdentifier]).value
```

## <a name="next-steps"></a>Další kroky 

V tomto průvodci jste zjistili, jak nakonfigurovat ověřování AD pro zabezpečené připojení k serveru API pomocí přihlašovacích údajů jednotného přihlašování. V dalším kroku můžete:

- [Nasaďte aplikace pro Linux do clusteru Kubernetes](./deploy-linux-application.md).
- [Nasaďte aplikaci Windows serveru v clusteru Kubernetes](./deploy-windows-application.md).
