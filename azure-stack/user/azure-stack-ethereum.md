---
title: Nasazení sítě Ethereem blockchain v centru Azure Stack
description: Kurz použití vlastních šablon řešení k nasazení a konfiguraci sítě konsorcia Ethereem blockchain v Azure Stackovém centru
author: PatAltimore
ms.author: patricka
ms.date: 10/07/2020
ms.topic: tutorial
ms.reviewer: seyadava
ms.lastreviewed: 10/07/2020
ms.openlocfilehash: f409a2629243c0eb1cfe15ad857aa3e229832bb5
ms.sourcegitcommit: 7b189e5317b8fe5f8ad825565da3607a39a1b899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94493697"
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

## <a name="prerequisites"></a>Předpoklady

Stáhněte si nejnovější položky [z webu Marketplace](../operator/azure-stack-download-azure-marketplace-item.md):

- Ubuntu Server 16,04 LTS
- Windows Server 2016
- Vlastní skript pro Linux 2,0
- Rozšíření vlastních skriptů pro virtuální počítače

Další informace o blockchainch scénářích najdete v tématu [Šablona řešení ethereem pro ověření úřadu](/azure/blockchain/templates/ethereum-poa-deployment).

## <a name="deployment-architecture"></a>Architektura nasazení

Tato šablona řešení může nasadit jednu nebo více Ethereemch sítí konsorcia pro více členů. Virtuální síť je připojená k topologii řetězení pomocí síťového virtuálního zařízení a prostředků připojení.

Šablona může nasazovat Ethereem Consortium pro vedoucí a člen mnoha různými způsoby. Tady jsou ty, které jsme otestovali:

- V Azure Stackovém centru s více uzly se službou Azure AD nebo AD FS nasaďte zájemce a člena pomocí stejného předplatného nebo různých předplatných.
- V centru Azure Stack s jedním uzlem (s Azure AD) nasaďte vedoucí a člen pomocí stejného předplatného.

## <a name="standalone-and-consortium-leader-deployment"></a>Samostatné nasazení vedoucího a konsorcia

Šablona vedoucího nadřízeného nakonfiguruje v síti své nároky na prvního člena. 

1. Stáhněte si [šablonu vedoucí služby z GitHubu](https://aka.ms/aa6z619).
1. Na portálu tenanta centra Azure Stack vyberte **+ vytvořit prostředek > Template Deployment** k nasazení z vlastní šablony.
1. **V editoru vyberte vytvořit vlastní šablonu** a upravte novou vlastní šablonu.
1. V podokně úpravy na pravé straně zkopírujte a vložte šablonu JSON šablony, kterou jste stáhli dříve.
    
    [![Upravit šablonu se vloženou šablonou vedoucího](./media/azure-stack-ethereum/edit-leader-template.png)](./media/azure-stack-ethereum/edit-leader-template.png#lightbox)

1. Vyberte **Uložit**.
1. Na kartě **základy** proveďte následující nastavení.

    Název parametru | Description | Ukázková hodnota
    ---------------|-------------|-------------
    Předplatné | Předplatné, ke kterému se má nasadit síť konsorcia | Předplatné spotřeby
    Skupina prostředků | Skupina prostředků, do které se má nasadit síť konsorcia | EthereumResources
    Oblast | Oblast Azure pro prostředky. | local
    Předpona názvu | Řetězec, který se používá jako základ pro pojmenování nasazených prostředků. Použijte maximálně šest alfanumerických znaků. | ETH
    Typ ověřování | Metoda, která se má ověřit pro virtuální počítač. Povolené hodnoty jsou heslo nebo veřejný klíč SSH. | Heslo
    Uživatelské jméno správce | Uživatelské jméno správce všech nasazených virtuálních počítačů. Použijte jeden až 64 znaků. | gethadmin
    Heslo správce (typ ověřování = heslo)| Heslo pro účet správce pro každý nasazený virtuální počítač. Heslo musí obsahovat 3 z těchto požadavků: 1 velké písmeno, 1 malé písmeno, 1 číslice a 1 speciální znak. <br />Všechny virtuální počítače mají zpočátku stejné heslo, ale po zřízení můžete heslo změnit. Použijte 12 až 72 znaků. |
    Správce – klíč SSH (typ ověřování = sshPublicKey) | Řetězec veřejného klíče RSA pro zabezpečené prostředí, který se používá pro vzdálené přihlášení. |
    Blok Genesis | Řetězec JSON představující vlastní blok Genesis  Zadání hodnoty pro tento parametr je volitelné. |
    Heslo účtu ethereem | Heslo správce, které se používá k zabezpečení účtu Ethereem. |
    Heslo účtu ethereem | Přístupové heslo, které se používá ke generování privátního klíče přidruženého k účtu Ethereem. Zvažte heslo s dostatečnou náhodností pro zajištění silného privátního klíče. |
    ID sítě ethereem | ID sítě konsorcia. Použijte libovolnou hodnotu mezi 5 a 999 999 999. | 72
    ID člena konsorcia | ID přidružené k jednotlivým členům sítě konsorcia. Toto ID by mělo být v síti jedinečné. | 0
    Počet uzlů dolování | Počet uzlů dolování pro každého člena konsorcia. Použijte hodnotu od 2 do 15. | 2
    Velikost virtuálního počítače uzlu dolování | Velikost virtuálního počítače pro uzly dolování. | Standard_A1
    Typ účtu úložiště dolování | Výkon úložiště pro uzly dolování. | Standard_LRS
    Počet uzlů pro odesílání | Počet uzlů transakcí s vyrovnáváním zatížení. Použijte hodnotu v rozmezí od 1 do 5. | 1
    Velikost virtuálního počítače uzlu TX | Velikost virtuálního počítače v uzlech transakcí. | Standard_A1
    Typ účtu úložiště pro odesílání | Výkon úložiště v uzlech transakce. | Standard_LRS
    Základní adresa URL | Základní adresa URL, kam se mají získat šablony nasazení Pokud nechcete přizpůsobit šablony nasazení, použijte výchozí hodnotu. |

1. Vyberte **Zkontrolovat a vytvořit**. Po úspěšném ověření vyberte **vytvořit**.

Nasazení může trvat až 20 minut nebo i déle.

Po dokončení nasazení si přečtěte část Souhrn nasazení pro **Microsoft. template** v části nasazení skupiny prostředků. Souhrn obsahuje výstupní hodnoty používané pro spojování členů konsorcia.

Pokud chcete ověřit nasazení vedoucího vedoucího, navštivte web správce vedoucího. Adresa webu pro správu se nachází v části výstup nasazení **Microsoft. template** .  

![Shrnutí nasazení vedoucího procesu](./media/azure-stack-ethereum/ethereum-node-status.png)

## <a name="joining-consortium-member-deployment"></a>Spojování nasazení členů konsorcia

1. Stáhněte si [šablonu člena konsorcia z GitHubu](https://aka.ms/aa6zkua).
1. Na portálu tenanta centra Azure Stack vyberte **+ vytvořit prostředek > Template Deployment** k nasazení z vlastní šablony.
1. **V editoru vyberte vytvořit vlastní šablonu** a upravte novou vlastní šablonu.
1. V podokně úpravy na pravé straně zkopírujte a vložte kód JSON šablony konsorcia, který jste předtím stáhli.
1. Vyberte **Uložit**.
1. Na kartě **základy** proveďte následující nastavení.

    Název parametru | Description | Ukázková hodnota
    ---------------|-------------|-------------
    Předplatné | Předplatné, ke kterému se má nasadit síť konsorcia | Předplatné spotřeby
    Skupina prostředků | Skupina prostředků, do které se má nasadit síť konsorcia | EthereumResources
    Oblast | Oblast Azure pro prostředky. | local
    Předpona názvu | Řetězec, který se používá jako základ pro pojmenování nasazených prostředků. Použijte maximálně šest alfanumerických znaků. | ETH
    Typ ověřování | Metoda, která se má ověřit pro virtuální počítač. Povolené hodnoty jsou heslo nebo veřejný klíč SSH. | Heslo
    Uživatelské jméno správce | Uživatelské jméno správce všech nasazených virtuálních počítačů. Použijte jeden až 64 znaků. | gethadmin
    Heslo správce (typ ověřování = heslo)| Heslo pro účet správce pro každý nasazený virtuální počítač. Heslo musí obsahovat 3 z těchto požadavků: 1 velké písmeno, 1 malé písmeno, 1 číslice a 1 speciální znak. <br />Všechny virtuální počítače mají zpočátku stejné heslo, ale po zřízení můžete heslo změnit. Použijte 12 až 72 znaků. |
    Správce – klíč SSH (typ ověřování = sshPublicKey) | Řetězec veřejného klíče RSA pro zabezpečené prostředí, který se používá pro vzdálené přihlášení. |
    Blok Genesis | Řetězec JSON představující vlastní blok Genesis Zadání hodnoty pro tento parametr je volitelné. |
    Heslo účtu ethereem | Heslo správce, které se používá k zabezpečení účtu Ethereem. |
    Heslo účtu ethereem | Přístupové heslo, které se používá ke generování privátního klíče přidruženého k účtu Ethereem. Zvažte heslo s dostatečnou náhodností pro zajištění silného privátního klíče. |
    ID člena konsorcia | ID přidružené k jednotlivým členům sítě konsorcia. Toto ID by mělo být v síti jedinečné. | 0
    Počet uzlů dolování | Počet uzlů dolování pro každého člena konsorcia. Použijte hodnotu od 2 do 15. | 2
    Velikost virtuálního počítače uzlu dolování | Velikost virtuálního počítače pro uzly dolování. | Standard_A1
    Typ účtu úložiště dolování | Výkon úložiště pro uzly dolování. | Standard_LRS
    Počet uzlů pro odesílání | Počet uzlů transakcí s vyrovnáváním zatížení. Použijte hodnotu v rozmezí od 1 do 5. | 1
    Velikost virtuálního počítače uzlu TX | Velikost virtuálního počítače v uzlech transakcí. | Standard_A1
    Typ účtu úložiště pro odesílání | Výkon úložiště v uzlech transakce. | Standard_LRS
    Data konsorcia | Adresa URL odkazující na relevantní konfigurační data konsorcia poskytnutá nasazením jiného člena. Tuto hodnotu najdete ve výstupu nasazení vedoucího procesu. |
    Adresní prostor vzdáleného člena virtuální sítě | Adresní prostor virtuální sítě vedoucího procesu. Tuto hodnotu najdete ve výstupu nasazení vedoucího procesu. |
    Veřejná IP adresa vzdáleného síťové virtuální zařízení | SÍŤOVÉ virtuální zařízení IP adresa vedoucího procesu. Tuto hodnotu najdete ve výstupu nasazení vedoucího procesu. |
    Sdílený klíč připojení | Předem zavedený tajný klíč mezi členy konsorcia sítě, který vytváří připojení brány. |
    Základní adresa URL | Základní adresa URL, kam se mají získat šablony nasazení Pokud nechcete přizpůsobit šablony nasazení, použijte výchozí hodnotu. |
1. Vyberte **Zkontrolovat a vytvořit**. Po úspěšném ověření vyberte **vytvořit**.

Nasazení může trvat až 20 minut nebo i déle.

Po dokončení nasazení si přečtěte část Souhrn nasazení pro **Microsoft. template** v části nasazení skupiny prostředků. Souhrn obsahuje výstupní hodnoty používané pro připojení členů konsorcia.

Chcete-li ověřit nasazení člena, procházejte web pro správu člena. Adresu správce můžete najít v části výstup nasazení **Microsoft. template** .

![Souhrn nasazení členů](./media/azure-stack-ethereum/ethereum-node-status-2.png)

Jak je znázorněno na obrázku, stav uzlů členů není **spuštěno**. Tento stav je, protože spojení mezi členem a vedoucím není navázáno. Připojení mezi členem a vedoucím je obousměrné připojení. Když nasadíte člena, šablona automaticky vytvoří připojení od člena k vedoucímu. Pokud chcete vytvořit připojení od vedoucího ke členu, pokračujte na další krok.

## <a name="connect-member-and-leader"></a>Propojit člena a vedoucího

Tato šablona vytvoří připojení od vedoucího ke vzdálenému členovi. 

1. Stáhněte si [člen a šablonu vedoucího člena z GitHubu](https://aka.ms/aa6zdyt).
1. Na portálu tenanta centra Azure Stack vyberte **+ vytvořit prostředek > Template Deployment** k nasazení z vlastní šablony.
1. **V editoru vyberte vytvořit vlastní šablonu** a upravte novou vlastní šablonu.
1. V podokně úpravy na pravé straně zkopírujte a vložte kód JSON šablony konsorcia, který jste předtím stáhli.
1. Vyberte **Uložit**.
1. Dokončete následující nastavení.

    Název parametru | Description | Ukázková hodnota
    ---------------|-------------|-------------
    Předplatné | Předplatné, ke kterému se má nasadit síť konsorcia | Předplatné spotřeby
    Skupina prostředků | Skupina prostředků, do které se má nasadit síť konsorcia | EthereumResources
    Oblast | Oblast Azure pro prostředky. | local
    Předpona názvu člena | Řetězec, který se používá jako základ pro pojmenování nasazených prostředků. Použijte maximálně šest alfanumerických znaků. | ETH
    Název tabulky směrování členů | Název směrovací tabulky vedoucího procesu. Tuto hodnotu najdete ve výstupu nasazení vedoucího procesu. |
    Adresní prostor vzdáleného člena virtuální sítě | Adresní prostor člena. Tuto hodnotu lze najít ve výstupu nasazení člena. |
    Veřejná IP adresa vzdáleného síťové virtuální zařízení | SÍŤOVÉ virtuální zařízení IP adresu, ke které se chcete připojit. Tuto hodnotu lze najít ve výstupu nasazení člena. |
    Sdílený klíč připojení | Předem zavedený tajný klíč mezi členy konsorcia sítě, která vytváří připojení.  |
    Privátní IP adresa člena síťové virtuální zařízení | SÍŤOVÉ virtuální zařízení IP adresu člena. Tuto hodnotu lze najít ve výstupu nasazení člena. |
    Základní adresa URL | Základní adresa URL, kam se mají získat šablony nasazení Pokud nechcete přizpůsobit šablony nasazení, použijte výchozí hodnotu. |
1. Vyberte **Zkontrolovat a vytvořit**. Po úspěšném ověření vyberte **vytvořit**.

Po dokončení nasazení trvá několik minut, než vedoucí a člen zahájí komunikaci. Chcete-li ověřit nasazení, obnovte web správce člena. Stav uzlů členů by měl být spuštěn.

![Ověření nasazení](./media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>Další kroky

Další informace o Ethereem a Azure najdete v tématu [technologie a aplikace blockchain](https://azure.microsoft.com/solutions/blockchain/).
