---
title: Nasazení sítě Ethereem blockchain v centru Azure Stack
description: Kurz použití vlastních šablon řešení k nasazení a konfiguraci sítě konsorcia Ethereem blockchain v Azure Stackovém centru
author: PatAltimore
ms.author: patricka
ms.date: 06/03/2019
ms.topic: tutorial
ms.reviewer: seyadava
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: a0a13af01fc539788d76377f357ca28db9b05234
ms.sourcegitcommit: bdd4d529bd3e115a9f76eece62b1613448d5d020
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "77703073"
---
# <a name="deploy-an-ethereum-blockchain-network-on-azure-stack-hub"></a>Nasazení sítě Ethereem blockchain v centru Azure Stack

Šablona řešení Ethereem je navržená tak, aby usnadnila a zjednodušila nasazení a konfiguraci blockchain sítě konsorcia Ethereem pro více členů s minimálními znalostmi Azure a Ethereem.

Díky několik uživatelským vstupům a nasazením jedním kliknutím prostřednictvím portálu tenanta centra Azure Stack může každý člen zřídit své síťové nároky. Síťové nároky jednotlivých členů se skládají ze tří věcí:

1. Sada uzlů transakcí s vyrovnáváním zatížení, se kterými může aplikace nebo uživatel interaktivně odeslat transakce.
2. Sada uzlů dolování pro záznam transakcí.
3. Síťové virtuální zařízení (síťové virtuální zařízení).

Později krok připojení propojuje síťová virtuální zařízení a vytvoří plně konfigurovanou blockchain síť s více členy.

Nastavení:

- Vyberte architekturu nasazení.
- Nasaďte samostatnou, vedoucího nebo členskou síť konsorcia.

## <a name="prerequisites"></a>Požadavky

Stáhněte si nejnovější položky [z webu Marketplace](../operator/azure-stack-download-azure-marketplace-item.md):

- Ubuntu Server 16,04 LTS
- Windows Server 2016
- Vlastní skript pro Linux 2,0
- Rozšíření vlastních skriptů pro virtuální počítače

Další informace o blockchainch scénářích najdete v tématu [Šablona řešení ethereem pro ověření úřadu](/azure/blockchain/templates/ethereum-poa-deployment).

## <a name="deployment-architecture"></a>Architektura nasazení

Tato šablona řešení může nasadit jednu nebo více Ethereemch sítí konsorcia pro více členů. Virtuální síť je připojená k topologii řetězení pomocí síťového virtuálního zařízení a prostředků připojení.

## <a name="deployment-use-cases"></a>Případy použití nasazení

Šablona může nasazovat Ethereem Consortium pro vedoucí a člen mnoha různými způsoby. Tady jsou ty, které jsme otestovali:

- V Azure Stackovém centru s více uzly se službou Azure AD nebo AD FS nasaďte zájemce a člena pomocí stejného předplatného nebo různých předplatných.
- V centru Azure Stack s jedním uzlem (s Azure AD) nasaďte vedoucí a člen pomocí stejného předplatného.

### <a name="standalone-and-consortium-leader-deployment"></a>Samostatné nasazení vedoucího a konsorcia

Šablona vedoucího nadřízeného nakonfiguruje v síti své nároky na prvního člena. 

1. Stáhněte si [šablonu vedoucí služby z GitHubu](https://aka.ms/aa6z619).
2. Na portálu tenanta centra Azure Stack vyberte **+ vytvořit prostředek > Template Deployment** k nasazení z vlastní šablony.
3. Vyberte **Upravit šablonu** a upravte novou vlastní šablonu.
4. V podokně úpravy na pravé straně zkopírujte a vložte šablonu JSON šablony, kterou jste stáhli dříve.
    
    ![Upravit šablonu vedoucího procesu](./media/azure-stack-ethereum/edit-leader-template.png)

5. Vyberte **Uložit**.
6. Vyberte **Upravit parametry** a dokončete parametry šablony pro vaše nasazení.
    
    ![Upravit parametry šablony vedoucího procesu](./media/azure-stack-ethereum/edit-leader-parameters.png)

    Název parametru | Popis | Povolené hodnoty | Ukázková hodnota
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Řetězec, který se používá jako základ pro pojmenování nasazených prostředků. | Alfanumerické znaky s délkou 1 až 6. | ETH
    TYP AUTHTYPE | Metoda, která se má ověřit pro virtuální počítač. | Heslo nebo veřejný klíč SSH. | Heslo
    ADMINUSERNAME | Uživatelské jméno správce všech nasazených virtuálních počítačů. | 1-64 znaků. | gethadmin
    ADMINPASSWORD (typ ověřování = heslo)| Heslo pro účet správce pro každý nasazený virtuální počítač. Heslo musí obsahovat 3 z těchto požadavků: 1 velké písmeno, 1 malé písmeno, 1 číslice a 1 speciální znak. <br />Všechny virtuální počítače mají zpočátku stejné heslo, ale po zřízení můžete heslo změnit.|12-72 znaků. |
    ADMINSSHKEY (typ ověřování = sshPublicKey) | Klíč zabezpečeného prostředí používaný pro vzdálené přihlášení | |
    GENESISBLOCK | Řetězec JSON představující vlastní blok Genesis  Zadání hodnoty pro tento parametr je volitelné. | |
    ETHEREUMACCOUNTPSSWD | Heslo správce, které se používá k zabezpečení účtu Ethereem. | |
    ETHEREUMACCOUNTPASSPHRASE | Přístupové heslo, které se používá ke generování privátního klíče přidruženého k účtu Ethereem. | |
    ETHEREUMNETWORKID | ID sítě konsorcia. | Použijte libovolnou hodnotu mezi 5 a 999 999 999. | 72
    CONSORTIUMMEMBERID | ID přidružené k jednotlivým členům sítě konsorcia.   | Toto ID by mělo být v síti jedinečné. | 0
    NUMMININGNODES | Počet uzlů dolování. | Mezi 2 a 15. | 2
    MNNODEVMSIZE | Velikost virtuálního počítače pro uzly dolování. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Výkon úložiště pro uzly dolování. | | Standard_LRS
    NUMTXNODES | Počet uzlů transakce. | Mezi 1 a 5. | 1
    TXNODEVMSIZE | Velikost virtuálního počítače v uzlech transakcí. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Výkon úložiště v uzlech transakce. | | Standard_LRS
    BASEURL | Základní adresa URL, ze které se mají získat šablony nasazení | Pokud nechcete přizpůsobit šablony nasazení, použijte výchozí hodnotu. | 

7. Vyberte **OK**.
8. Ve **vlastním nasazení**zadejte **předplatné**, **skupinu prostředků**a **umístění skupiny prostředků**.
    
    ![Parametry nasazení vedoucího procesu](./media/azure-stack-ethereum/leader-deployment-parameters.png)

    Název parametru | Popis | Povolené hodnoty | Ukázková hodnota
    ---------------|-------------|----------------|-------------
    Předplatné | Předplatné, ke kterému se má nasadit síť konsorcia | | Předplatné spotřeby
    Skupina prostředků | Skupina prostředků, do které se má nasadit síť konsorcia | | EthereumResources
    Umístění | Oblast Azure pro skupinu prostředků. | | local

8. Vyberte **Vytvořit**.

Nasazení může trvat až 20 minut nebo i déle.

Po dokončení nasazení si přečtěte část Souhrn nasazení pro **Microsoft. template** v části nasazení skupiny prostředků. Souhrn obsahuje výstupní hodnoty používané pro spojování členů konsorcia.

Pokud chcete ověřit nasazení vedoucího vedoucího, navštivte web správce vedoucího. Adresa webu pro správu se nachází v části výstup nasazení **Microsoft. template** .  

![Shrnutí nasazení vedoucího procesu](./media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>Spojování nasazení členů konsorcia

1. Stáhněte si [šablonu člena konsorcia z GitHubu](https://aka.ms/aa6zkua).
2. Na portálu tenanta centra Azure Stack vyberte **+ vytvořit prostředek > Template Deployment** k nasazení z vlastní šablony.
3. Vyberte **Upravit šablonu** a upravte novou vlastní šablonu.
4. V podokně úpravy na pravé straně zkopírujte a vložte JSON šablony, kterou jste si stáhli dříve.
5. Vyberte **Uložit**.
6. Vyberte **Upravit parametry** a dokončete parametry šablony pro vaše nasazení.

    Název parametru | Popis | Povolené hodnoty | Ukázková hodnota
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | Řetězec, který se používá jako základ pro pojmenování nasazených prostředků. | Alfanumerické znaky s délkou 1 až 6. | ETH
    TYP AUTHTYPE | Metoda ověřování pro virtuální počítač | Heslo nebo veřejný klíč SSH. | Heslo
    ADMINUSERNAME | Uživatelské jméno správce každého nasazeného virtuálního počítače | 1-64 znaků. | gethadmin
    ADMINPASSWORD (typ ověřování = heslo)| Heslo pro účet správce pro každý nasazený virtuální počítač. Heslo musí obsahovat 3 z těchto požadavků: 1 velké písmeno, 1 malé písmeno, 1 číslice a 1 speciální znak. <br />Všechny virtuální počítače mají zpočátku stejné heslo, ale po zřízení můžete heslo změnit.|12-72 znaků. |
    ADMINSSHKEY (typ ověřování = sshPublicKey) | Klíč zabezpečeného prostředí používaný pro vzdálené přihlášení | |
    CONSORTIUMMEMBERID | ID přidružené k jednotlivým členům sítě konsorcia.   | Toto ID by mělo být v síti jedinečné. | 0
    NUMMININGNODES | Počet uzlů dolování. | Mezi 2 a 15. | 2
    MNNODEVMSIZE | Velikost virtuálního počítače pro uzly dolování. | | Standard_A1
    MNSTORAGEACCOUNTTYPE | Výkon úložiště pro uzly dolování. | | Standard_LRS
    NUMTXNODES | Počet uzlů transakce. | Mezi 1 a 5. | 1
    TXNODEVMSIZE | Velikost virtuálního počítače v uzlech transakcí. | | Standard_A1
    TXSTORAGEACCOUNTTYPE | Výkon úložiště v uzlech transakce. | | Standard_LRS
    CONSORTIUMDATA | Adresa URL odkazující na relevantní konfigurační data konsorcia poskytnutá nasazením jiného člena. Tuto hodnotu najdete ve výstupu nasazení vedoucího procesu. | |
    REMOTEMEMBERVNETADDRESSSPACE | SÍŤOVÉ virtuální zařízení IP adresa vedoucího procesu. Tuto hodnotu najdete ve výstupu nasazení vedoucího procesu. | | 
    REMOTEMEMBERNVAPUBLICIP | SÍŤOVÉ virtuální zařízení IP adresa vedoucího procesu. Tuto hodnotu najdete ve výstupu nasazení vedoucího procesu. | | 
    CONNECTIONSHAREDKEY | Předem zavedený tajný klíč mezi členy konsorcia sítě, která vytváří připojení. | |
    BASEURL | Základní adresa URL šablony | Pokud nechcete přizpůsobit šablony nasazení, použijte výchozí hodnotu. | 

7. Vyberte **OK**.
8. Ve **vlastním nasazení**zadejte **předplatné**, **skupinu prostředků**a **umístění skupiny prostředků**.

    Název parametru | Popis | Povolené hodnoty | Ukázková hodnota
    ---------------|-------------|----------------|-------------
    Předplatné | Předplatné, ke kterému se má nasadit síť konsorcia | | Předplatné spotřeby
    Skupina prostředků | Skupina prostředků, do které se má nasadit síť konsorcia | | MemberResources
    Umístění | Oblast Azure pro skupinu prostředků. | | local

8. Vyberte **Vytvořit**.

Nasazení může trvat až 20 minut nebo i déle.

Po dokončení nasazení si přečtěte část Souhrn nasazení pro **Microsoft. template** v části nasazení skupiny prostředků. Souhrn obsahuje výstupní hodnoty používané pro připojení členů konsorcia.

Chcete-li ověřit nasazení člena, procházejte web pro správu člena. Adresu správce můžete najít v části výstup nasazení **Microsoft. template** .

![Souhrn nasazení členů](./media/azure-stack-ethereum/ethereum-node-status-2.png)

Jak je znázorněno na obrázku, stav uzlů členů není **spuštěno**. Tento stav je, protože spojení mezi členem a vedoucím není navázáno. Připojení mezi členem a vedoucím je obousměrné připojení. Když nasadíte člena, šablona automaticky vytvoří připojení od člena k vedoucímu. Pokud chcete vytvořit připojení od vedoucího ke členu, pokračujte na další krok.

### <a name="connect-member-and-leader"></a>Propojit člena a vedoucího

Tato šablona vytvoří připojení od vedoucího ke vzdálenému členovi. 

1. Stáhněte si [člen a šablonu vedoucího člena z GitHubu](https://aka.ms/aa6zdyt).
2. Na portálu tenanta centra Azure Stack vyberte **+ vytvořit prostředek > Template Deployment** k nasazení z vlastní šablony.
3. Vyberte **Upravit šablonu** a upravte novou vlastní šablonu.
4. V podokně úpravy na pravé straně zkopírujte a vložte JSON šablony, kterou jste si stáhli dříve.
    
    ![Upravit šablonu připojení](./media/azure-stack-ethereum/edit-connect-template.png)

5. Vyberte **Uložit**.
6. Vyberte **Upravit parametry** a dokončete parametry šablony pro vaše nasazení.
    
    ![Upravit parametry šablony připojení](./media/azure-stack-ethereum/edit-connect-parameters.png)

    Název parametru | Popis | Povolené hodnoty | Ukázková hodnota
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | Předpona názvu vedoucího. Tuto hodnotu najdete ve výstupu nasazení vedoucího procesu.  | Alfanumerické znaky s délkou 1 až 6. | |
    MEMBERROUTETABLENAME | Název směrovací tabulky vedoucího procesu. Tuto hodnotu najdete ve výstupu nasazení vedoucího procesu. |  | 
    REMOTEMEMBERVNETADDRESSSPACE | Adresní prostor člena. Tuto hodnotu lze najít ve výstupu nasazení člena. | |
    CONNECTIONSHAREDKEY | Předem zavedený tajný klíč mezi členy konsorcia sítě, která vytváří připojení.  | |
    REMOTEMEMBERNVAPUBLICIP | SÍŤOVÉ virtuální zařízení IP adresu člena. Tuto hodnotu lze najít ve výstupu nasazení člena. | |
    MEMBERNVAPRIVATEIP | Privátní IP adresa vedoucího síťové virtuální zařízení Tuto hodnotu najdete ve výstupu nasazení vedoucího procesu. | |
    UMÍSTĚNÍ | Umístění vašeho prostředí centra Azure Stack. | | local
    BASEURL | Základní adresa URL šablony | Pokud nechcete přizpůsobit šablony nasazení, použijte výchozí hodnotu. | 

7. Vyberte **OK**.
8. Ve **vlastním nasazení**zadejte **předplatné**, **skupinu prostředků**a **umístění skupiny prostředků**.
    
    ![Připojení parametrů nasazení](./media/azure-stack-ethereum/connect-deployment-parameters.png)

    Název parametru | Popis | Povolené hodnoty | Ukázková hodnota
    ---------------|-------------|----------------|-------------
    Předplatné | Předplatné vedoucího. | | Předplatné spotřeby
    Skupina prostředků | Skupina prostředků vedoucího procesu. | | EthereumResources
    Umístění | Oblast Azure pro skupinu prostředků. | | local

8. Vyberte **Vytvořit**.

Po dokončení nasazení trvá několik minut, než vedoucí a člen zahájí komunikaci. Chcete-li ověřit nasazení, obnovte web správce člena. Stav uzlů členů by měl být spuštěn.

![Ověření nasazení](./media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>Další kroky

Další informace o Ethereem a Azure najdete v tématu [technologie a aplikace blockchain](https://azure.microsoft.com/solutions/blockchain/).
