---
title: Nastavení určujícího clusteru
description: Přečtěte si, jak nastavit určující disk clusteru.
author: v-dasis
ms.topic: how-to
ms.date: 02/17/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 32d0f717e987d757f5315cfe048c75300ae9c776
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100647907"
---
# <a name="set-up-a-cluster-witness"></a>Nastavení určujícího clusteru

> Platí pro Azure Stack HCI, verze 20H2; Windows Server 2019

Nastavení prostředku určujícího disku se pro všechny clustery důrazně doporučuje a po vytvoření clusteru by se mělo nastavit hned. Clustery se dvěma uzly potřebují určující určující, aby jeden server v režimu offline nezpůsobí, že druhý uzel nebude k dispozici také. Clustery se třemi a vyššími uzly potřebují určující, aby bylo možné vydržet dva servery, které jsou neúspěšné nebo offline.  

Můžete buď použít sdílenou složku SMB, jako určující kopii nebo cloudovou kopii Azure. Doporučuje se sdílené složky Azure v cloudu, za předpokladu, že všechny uzly serveru v clusteru mají spolehlivé připojení k Internetu. Tento článek popisuje vytvoření sdílené složky v cloudu.

## <a name="before-you-begin"></a>Než začnete

Než budete moct vytvořit disk s kopií cloudu, musíte mít účet a předplatné Azure a zaregistrovat Azure Stack cluster HCI v Azure. Další informace najdete v následujících článcích:

- [Vytvoření účtu Azure](https://docs.microsoft.com/dotnet/azure/create-azure-account)
- Pokud je to možné, [vytvořte další předplatné Azure](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription) .
- [Připojení Azure Stack HCl k Azure](../deploy/register-with-azure.md)

Pro určující sdílené složky jsou požadavky na souborový server. Další informace najdete v části [požadavky na systém](../concepts/system-requirements.md) .

## <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure

Tato část popisuje, jak vytvořit účet služby Azure Storage. Tento účet se používá k uložení souboru objektu BLOB v Azure, který se používá pro arbitráž pro konkrétní cluster. Stejný účet služby Azure Storage můžete použít ke konfiguraci určujícího cloudu pro více clusterů.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V domovské nabídce Azure Portal v části **služby Azure** vyberte **účty úložiště**. Pokud tato ikona chybí, vyberte **vytvořit prostředek** a vytvořte nejprve prostředek *účtů úložiště* .

    :::image type="content" source="media/witness/cloud-witness-home.png" alt-text="Azure Portal domovskou obrazovku" lightbox="media/witness/cloud-witness-home.png":::

1. Na stránce **účty úložiště** vyberte **Nový**.

    :::image type="content" source="media/witness/cloud-witness-create.png" alt-text="Nový účet úložiště Azure" lightbox="media/witness/cloud-witness-create.png":::

1. Na stránce **vytvořit účet úložiště** proveďte následující:
    1. Vyberte **předplatné** Azure, na které se má účet úložiště použít.
    1. Vyberte **skupinu prostředků** Azure, na kterou se má účet úložiště použít.
    1. Zadejte **název účtu úložiště**.
    <br>Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Tento název musí být taky jedinečný v rámci Azure.
    1. Vyberte **umístění** , které je nejblíže vašemu fyzickému.
    1. **Výkon** – vyberte **Standard**.
    1. Jako **druh účtu** vyberte **úložiště pro obecné účely**.
    1. V případě **replikace** vyberte **místně redundantní úložiště (LRS)**.
    1. Po dokončení klikněte na tlačítko **zkontrolovat + vytvořit**.

    :::image type="content" source="media/witness/cloud-witness-create-storage-account.png" alt-text="Účet úložiště Azure pro vytvoření" lightbox="media/witness/cloud-witness-create-storage-account.png":::

1. Ujistěte se, že účet úložiště projde ověřením, a pak zkontrolujte nastavení účtu. Jakmile budete hotovi, klikněte na **Vytvořit**.

    :::image type="content" source="media/witness/cloud-witness-validation.png" alt-text="Ověřování účtu Azure Storage" lightbox="media/witness/cloud-witness-validation.png":::

1. Může to trvat několik sekund, než se nasazování účtu v Azure dostanou. Po dokončení nasazení klikněte na **Přejít k prostředku**.

    :::image type="content" source="media/witness/cloud-witness-deployment.png" alt-text="Nasazení účtu úložiště Azure" lightbox="media/witness/cloud-witness-deployment.png":::

## <a name="copy-the-access-key-and-endpoint-url"></a>Zkopírování přístupového klíče a adresy URL koncového bodu

Když vytvoříte účet úložiště Azure, proces automaticky vytvoří dva přístupové klíče, primární klíč (klíč1) a sekundární klíč (key2). Při prvním vytváření určujícího cloudu se používá **klíč1** . Adresa URL koncového bodu se také generuje automaticky.

Disk s kopií cloudu Azure používá pro úložiště soubor objektů BLOB s vygenerovaným koncovým bodem ve formátu *storage_account_name. blob. Core. Windows. NET* jako koncový bod. 

> [!NOTE]  
> Disk s kopií cloudu Azure používá k navázání komunikace se službou Azure Blob Service protokol HTTPS (výchozí port 443). Zajistěte, aby byl port HTTPS dostupný.

### <a name="copy-the-account-name-and-access-key"></a>Zkopírování názvu účtu a přístupového klíče

1. V Azure Portal v části **Nastavení** vyberte **přístupové klíče**.
1. Chcete-li zobrazit klíčové informace, vyberte možnost **Zobrazit** klíče.
1. Klikněte na ikonu kopírování a vložení napravo od polí **název účtu úložiště** a **klíč1** a vložte jednotlivé textové řetězce do poznámkového bloku nebo jiného textového editoru.

    :::image type="content" source="media/witness/cloud-witness-access-keys.png" alt-text="Přístupové klíče k účtu úložiště Azure" lightbox="media/witness/cloud-witness-access-keys.png":::

### <a name="copy-the-endpoint-url-optional"></a>Zkopírovat adresu URL koncového bodu (volitelné)

Adresa URL koncového bodu je volitelná a nemusí být potřebná pro disk s kopií cloudu.

1. V Azure Portal vyberte možnost **vlastnosti**.
1. Chcete-li zobrazit informace o koncovém bodu, vyberte možnost **Zobrazit klíče** .
1. V části **BLOB Service** klikněte na ikonu kopírování a vložení napravo od pole **BLOB Service** a vložte textový řetězec do poznámkového bloku nebo jiného textového editoru.

    :::image type="content" source="media/witness/cloud-witness-blob-service.png" alt-text="Koncový bod objektu blob Azure" lightbox="media/witness/cloud-witness-blob-service.png":::

## <a name="create-a-cloud-witness-using-windows-admin-center"></a>Vytvoření určujícího cloudu pomocí centra pro správu Windows

Nyní jste připraveni vytvořit instanci určujícího clusteru pomocí centra pro správu systému Windows.

1. V centru pro správu systému Windows vyberte ze šipky nahoru v horním rozevíracím seznamu položku **Správce clusteru** .
1. V části **připojení clusteru** vyberte cluster.
1. V části **nástroje** vyberte **Nastavení**.
1. V pravém podokně vyberte **určující**.
1. Jako **Typ určující kopie** vyberte jednu z následujících možností:
      - **Disk s kopií cloudu** – zadejte název svého účtu služby Azure Storage, přístupový klíč a adresu URL koncového bodu, jak je popsáno výše.
      - **Určující sdílená složka** – zadejte cestu sdílení souborů (//server/share).
1. V případě určujícího cloudu pro následující pole vložte textové řetězce, které jste zkopírovali dříve, pro:
    1. **Azure storage account name**
    1. **Přístupový klíč k Azure Storage**
    1. **Koncový bod služby Azure**

    :::image type="content" source="media/witness/cloud-witness-1.png" alt-text="Přístupové klíče pro disk s kopií cloudu" lightbox="media/witness/cloud-witness-1.png":::

1. Jakmile budete hotoví, klikněte na **Uložit**. Může chvíli trvat, než se informace rozšíří do Azure.

> [!NOTE]
> Třetí možnost, **určující disk** není vhodná pro použití v roztaženém clusteru.

## <a name="create-a-cloud-witness-using-windows-powershell"></a>Vytvoření určujícího cloudu pomocí Windows PowerShellu

Alternativně můžete vytvořit instanci určujícího clusteru pro cluster pomocí prostředí PowerShell.

K vytvoření sdílené složky Azure v cloudu použijte následující rutinu. Zadejte název účtu Azure Storage a přístupová práva k klíčům, jak je popsáno výše:

```powershell
Set-ClusterQuorum –Cluster "Cluster1" -CloudWitness -AccountName "AzureStorageAccountName" -AccessKey "AzureStorageAccountAccessKey"
```

Pomocí následující rutiny vytvořte určující sdílenou složku. Zadejte cestu ke sdílené složce na souborovém serveru:

```powershell
Set-ClusterQuorum -FileShareWitness "\\fileserver\share" -Credential (Get-Credential)
```

## <a name="next-steps"></a>Další kroky

- Další informace o kvoru clusteru najdete v tématu [Principy kvora clusteru a fondu v Azure Stack HCI](../concepts/quorum.md).

- Další informace o vytváření a správě účtů Azure Storage najdete v tématu [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-account-create).