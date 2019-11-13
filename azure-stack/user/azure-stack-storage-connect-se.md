---
title: Připojení Průzkumník služby Storage k předplatnému Azure Stack nebo účtu úložiště | Microsoft Docs
description: Naučte se připojit Průzkumníka služby Storage k předplatnému Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 2fa9998a584bd76c8f9c875d3a2ac082c9ca812b
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955614"
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>Připojení Průzkumníka služby Storage k předplatnému Azure Stack nebo účtu úložiště

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

V tomto článku se dozvíte, jak se připojit k předplatným Azure Stack a účtům úložiště pomocí [Průzkumník služby Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer). Průzkumník služby Storage je samostatná aplikace, která umožňuje snadnou práci s Azure Stackmi daty úložiště v systémech Windows, macOS a Linux.

> [!NOTE]  
> K dispozici je několik nástrojů k přesunu dat do a z Azure Stack úložiště. Další informace najdete v tématu [Nástroje pro přenos dat pro Azure Stack Storage](azure-stack-storage-transfer.md).

Pokud ještě není nainstalovaná, [Stáhněte si Průzkumník služby Storage](https://www.storageexplorer.com/) a nainstalujte ji.

Po připojení k předplatnému Azure Stack nebo účtu úložiště můžete pomocí [Průzkumník služby Azure Storage článků](/azure/vs-azure-tools-storage-manage-with-storage-explorer) pracovat s daty Azure Stack. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Příprava na připojení k Azure Stack

Pro přístup k Azure Stack předplatného Průzkumník služby Storage potřebujete přímý přístup k Azure Stack nebo připojení k síti VPN. Další informace o tom, jak nastavit připojení VPN ke službě Azure Stack, najdete v tématu [Připojení ke službě Azure Stack pomocí sítě VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn).

> [!Note]  
> Pokud se ASDK připojujete k ASDK prostřednictvím sítě VPN, nepoužívejte kořenový certifikát (CA. cer), který byl vytvořen během procesu instalace sítě VPN.  Toto je certifikát s kódováním DER, který neumožňuje Průzkumník služby Storage načíst předplatná Azure Stack. Pomocí následujících kroků exportujte certifikát s kódováním Base-64 pro použití s Průzkumník služby Storage.

V případě integrovaných systémů, které jsou odpojené a pro ASDK, doporučujeme použít interní podnikovou certifikační autoritu k exportu kořenového certifikátu ve formátu Base-64 a pak ho naimportovat do Průzkumník služby Azure Storage.  

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Exportovat a potom importovat certifikát Azure Stack

Exportujte a pak importujte Azure Stack certifikát pro odpojené integrované systémy a pro ASDK. U připojených integrovaných systémů je certifikát veřejně podepsaný a tento krok není nezbytný.

1. Otevřete `mmc.exe` na Azure Stack hostitelském počítači nebo v místním počítači s připojením VPN k Azure Stack. 

2. V **souboru**vyberte **Přidat nebo odebrat modul snap-in**. V okně dostupné moduly snap-in vyberte **certifikáty** . 

3. Vyberte položku **účet počítače**a potom vyberte možnost **Další**. Vyberte položku **místní počítač**a pak vyberte **Dokončit**.

4.  V části **Konzola Root\Certificated (místní počítač) \Trusted Root Certification Authorities\Certificates** najít **AzureStackSelfSignedRootCert**.

    ![Načtení kořenového certifikátu služby Azure Stack pomocí mmc.exe](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

5. Klikněte na certifikát pravým tlačítkem, vyberte **všechny úlohy** > **exportovat**a pak podle pokynů exportujte certifikát s **kódováním Base-64 s kódováním X. 509 (. CER)** .

    Vyexportovaný certifikát se použije v dalším kroku.

6. Spusťte Průzkumník služby Storage. Pokud se zobrazí dialogové okno **připojit k Azure Storage** , zrušte ho.

7. V nabídce **Upravit** vyberte **certifikáty SSL**a pak vyberte **importovat certifikáty**. Pomocí dialogového okna pro výběr souborů najděte a otevřete certifikát, který jste exportovali v předchozím kroku.

    Po importu certifikátu budete vyzváni k restartování Průzkumník služby Storage.

    ![Import certifikátu do Průzkumníka služby Storage](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

8. Po Průzkumník služby Storage restartování vyberte nabídku **Upravit** a zkontrolujte, jestli je vybraná možnost **cílové Azure Stack rozhraní API** . Pokud ne, vyberte **cílovou Azure Stack**a pak Průzkumník služby Storage restartujte, aby se změna projevila. Tato konfigurace je nutná pro kompatibilitu s vaším prostředím Azure Stack.

    ![Kontrola, že položka Target Azure Stack je vybraná](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription-with-azure-ad"></a>Připojení k předplatnému Azure Stack pomocí Azure AD

Pomocí následujících kroků můžete připojit Průzkumník služby Storage k předplatnému Azure Stack, které patří k účtu Azure Active Directory (Azure AD).

1. V levém podokně Průzkumník služby Storage vyberte **Spravovat účty**.  
    Zobrazí se všechny předplatné Microsoft, které jste si přihlásili.

2. Pokud se chcete připojit k předplatnému Azure Stack, vyberte **Přidat účet**.

    ![Přidání účtu Azure Stack](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. V dialogovém okně připojit k Azure Storage v části **prostředí Azure**vyberte **Azure**, **Azure Čína 21Vianet**, **Azure Německo**, **Azure US státní správa USA**nebo **přidejte nové prostředí**. To závisí na použitém účtu Azure Stack. Vyberte **Přihlásit** se a přihlaste se pomocí účtu Azure Stack přidruženého k aspoň jednomu aktivnímu předplatnému Azure Stack.

    ![Připojení k úložišti Azure](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Po úspěšném přihlášení pomocí účtu Azure Stack se v levém podokně zobrazí předplatná Azure Stack přidružená k tomuto účtu. Vyberte předplatná Azure Stack, se kterými chcete pracovat, a pak vyberte **Apply** (Použít). (Zaškrtnutím nebo zrušením zaškrtnutí políčka **All subscriptions** (Všechna předplatná) přepínáte výběr všech nebo žádných z uvedených předplatných Azure Stack.)

    ![Výběr předplatných Azure Stack po vyplnění dialogového okna Custom Cloud Environment](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    V levém podokně se zobrazí všechny účty úložišť přidružené k vybraným předplatným Azure Stack.

    ![Seznam účtů úložiště, včetně účtů předplatného Azure Stack](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-subscription-with-ad-fs-account"></a>Připojení k předplatnému Azure Stack s účtem AD FS

> [!Note]  
> Prostředí pro přihlašování k federované službě Azure (AD FS) podporuje Průzkumník služby Storage 1.2.0 nebo novější verze s Azure Stack 1804 nebo novější aktualizací.
Pomocí následujících kroků se připojte Průzkumník služby Storage k předplatnému Azure Stack, které patří k účtu AD FS.

1. Vyberte **Spravovat účty**. V Průzkumníkovi jsou uvedena předplatná Microsoft, ke kterým jste se přihlásili.
2. Vyberte **Přidat účet** pro připojení k předplatnému Azure Stack.

    ![Přidat účet – Průzkumník služby Storage](media/azure-stack-storage-connect-se/add-an-account.png)

3. Vyberte **Další**. V dialogovém okně připojit k Azure Storage v části **prostředí Azure**vyberte **použít vlastní prostředí**a pak klikněte na **Další**.

    ![Připojení k Azure Storage](media/azure-stack-storage-connect-se/connect-to-azure-storage.png)

4. Zadejte požadované informace o Azure Stack vlastního prostředí. 

    | Pole | Poznámky |
    | ---   | ---   |
    | Název prostředí | Pole lze přizpůsobit uživatelem. |
    | Azure Resource Manager koncový bod | Ukázky Azure Resource Manager koncových bodů prostředků Azure Stack Development Kit.<br>Pro operátory: https://adminmanagement.local.azurestack.external <br> Pro uživatele: https://management.local.azurestack.external |

    Pokud pracujete na Azure Stack integrovaném systému a neznáte koncový bod správy, obraťte se na svého operátora.

    ![Přidat účet – vlastní prostředí](./media/azure-stack-storage-connect-se/custom-environments.png)

5. Vyberte **Přihlásit** se a připojte se k účtu Azure Stack, který je přidružený minimálně k jednomu aktivnímu předplatnému Azure Stack.



6. Vyberte Azure Stack odběry, se kterými chcete pracovat, a pak vyberte **použít**.

    ![Správa účtů](./media/azure-stack-storage-connect-se/account-management.png)

    V levém podokně se zobrazí všechny účty úložišť přidružené k vybraným předplatným Azure Stack.

    ![Seznam přidružených předplatných](./media/azure-stack-storage-connect-se/list-of-associated-subscriptions.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Připojení k Azure Stackmu účtu úložiště

Můžete se také připojit k Azure Stackmu účtu úložiště pomocí názvu účtu úložiště a páru klíčů.

1. V levém podokně Průzkumník služby Storage vyberte Spravovat účty. Zobrazí se všechny účty Microsoft, ke kterým jste se přihlásili.

    ![Přidat účet – Průzkumník služby Storage](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. Pokud se chcete připojit k předplatnému Azure Stack, vyberte **Přidat účet**.

    ![Přidat účet – připojení k Azure Storage](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. V dialogovém okně připojit k Azure Storage vyberte **použít název a klíč účtu úložiště**.

4. Zadejte název účtu do pole **název účtu** a vložte klíč účtu do textového pole **klíč účtu** . Pak v části **doména koncových bodů úložiště** vyberte **Další (níže zadejte)** a zadejte Azure Stack koncový bod.

    Azure Stack koncový bod obsahuje dvě části: název oblasti a doménu Azure Stack. V Azure Stack Development Kit je výchozím koncovým bodem **místní. azurestack. external**. Pokud si nejste jisti vaším koncovým bodem, obraťte se na správce cloudu.

    ![Připojit jméno a klíč](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Vyberte **Connect** (Připojit).
6. Po úspěšném připojení účtu úložiště se účet úložiště zobrazí s názvem (**externí, jiný**) připojeným k jeho názvu.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>Další kroky

* [Začínáme s Průzkumníkem služby Storage](/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Azure Stack úložiště: rozdíly a požadavky](azure-stack-acs-differences.md)
* Další informace o službě Azure Storage najdete v tématu [Úvod do služby Microsoft Azure Storage](/azure/storage/common/storage-introduction) .
