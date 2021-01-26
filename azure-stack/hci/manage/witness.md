---
title: Nastavení určujícího clusteru
description: Přečtěte si, jak nastavit určující disk clusteru.
author: v-dasis
ms.topic: how-to
ms.date: 01/21/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: cb964bafae7dd9252b386c30a12251e89fc8ead5
ms.sourcegitcommit: e772df8ac78c86d834a68d1a8be83b7f738019b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98781948"
---
# <a name="set-up-a-cluster-witness"></a>Nastavení určujícího clusteru

> Platí pro Azure Stack HCI, verze 20H2; Windows Server 2019

Nastavení prostředku určujícího disku je pro všechny clustery povinné a po vytvoření clusteru je potřeba nastavit hned. Clustery se dvěma uzly potřebují určující určující, aby jeden server v režimu offline nezpůsobí, že druhý uzel nebude k dispozici také. Clustery se třemi a vyššími uzly potřebují určující, aby bylo možné vydržet dva servery, které jsou neúspěšné nebo offline.  

Můžete buď použít sdílenou složku SMB jako určující disk, nebo použít cloudovou kopii Azure. Doporučuje se sdílené složky Azure v cloudu, za předpokladu, že všechny uzly serveru v clusteru mají spolehlivé připojení k Internetu. Další informace najdete v tématu [nasazení určujícího cloudu pro cluster s podporou převzetí služeb při selhání](/windows-server/failover-clustering/deploy-cloud-witness).

Pro určující sdílené složky sdílení souborů platí požadavky na souborový server. Další informace najdete v části [požadavky na systém](../concepts/system-requirements.md) .

## <a name="set-up-a-witness-using-windows-admin-center"></a>Nastavení určujícího disku pomocí centra pro správu Windows

1. V centru pro správu systému Windows vyberte ze šipky nahoru v horním rozevíracím seznamu položku **Správce clusteru** .
1. V části **připojení clusteru** vyberte cluster.
1. V části **nástroje** vyberte **Nastavení**.
1. V pravém podokně vyberte **určující**.
1. Jako **Typ určující kopie** vyberte jednu z následujících možností:
      - **Disk s kopií cloudu** – zadejte název svého účtu služby Azure Storage, přístupový klíč a adresu URL koncového bodu, jak je popsáno níže.
      - **Určující sdílená složka** – zadejte cestu sdílení souborů (//server/share).

> [!NOTE]
> Třetí možnost, **určující disk** není vhodná pro použití v roztaženém clusteru.

## <a name="create-an-azure-storage-account-to-use-as-a-cloud-witness"></a>Vytvoření účtu Azure Storage pro použití jako určujícího cloudu

Tato část popisuje, jak vytvořit účet úložiště a jak zobrazit a zkopírovat adresy URL koncových bodů a přístupové klíče pro tento účet.

Pokud chcete konfigurovat disk s kopií cloudu, musíte mít platný účet Azure Storage, který se dá použít k uložení souboru objektu BLOB (používá se pro arbitráž). Disk s kopií cloudu vytvoří známý kontejner **MSFT-Cloud-určující disk** v účtu úložiště Microsoft. Disk s kopií cloudu zapisuje jeden soubor objektu BLOB s odpovídajícím jedinečným ID clusteru, který se používá jako název souboru objektu BLOB v tomto kontejneru **MSFT-Cloud-určující** . To znamená, že můžete použít stejný účet Microsoft Azure Storage ke konfiguraci určujícího cloudu pro více různých clusterů.

Když použijete stejný účet Azure Storage ke konfiguraci určujícího cloudu pro více různých clusterů, vytvoří se automaticky jeden kontejner protokolu **MSFT-Cloud-určující** . Tento kontejner bude obsahovat soubor s jedním objektem blob na cluster.

> [!NOTE]  
> Disk s kopií cloudu používá k navázání komunikace se službou Azure Blob Service protokol HTTPS (výchozí port 443). Zajistěte, aby byl port HTTPS přístupný prostřednictvím síťového proxy serveru.

### <a name="to-create-an-azure-storage-account"></a>Vytvoření účtu služby Azure Storage

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V nabídce centra vyberte nový-> data + úložiště-> účet úložiště.
1. Na stránce Vytvořit účet úložiště udělejte toto:
    1. Zadejte název účtu úložiště.
    <br>Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Název účtu úložiště musí být v rámci Azure taky jedinečný.
    1. Jako **druh účtu** vyberte **obecné účely**.
    <br>Účet úložiště BLOB nelze použít pro diskovou kopii cloudu.
    1. **Výkon** – vyberte **Standard**.
    <br>Pro disk s kopií cloudu nejde použít Azure Premium Storage.
    1. V případě **replikace** vyberte **místně redundantní úložiště (LRS)** .
    <br>Clustering s podporou převzetí služeb při selhání používá jako arbitrážní bod soubor objektu blob, který při čtení dat vyžaduje určité záruky konzistence. Proto musíte pro typ **replikace** vybrat **místně redundantní úložiště** .

### <a name="view-and-copy-storage-access-keys-for-your-azure-storage-account"></a>Zobrazení a zkopírování přístupových klíčů k úložišti pro Azure Storage účet

Když vytváříte účet Microsoft Azure Storage, je přidružen ke dvěma přístupovým klíčům, které se automaticky generují – primární přístupový klíč a sekundární přístupový klíč. Při prvním vytváření sdílené složky cloudu použijte **Primární přístupový klíč**. Neexistuje žádné omezení týkající se klíče, který se má použít pro disk s kopií cloudu.  

#### <a name="to-view-and-copy-storage-access-keys"></a>Zobrazení a zkopírování přístupových klíčů k úložišti

V Azure Portal přejděte na svůj účet úložiště, klikněte na **všechna nastavení** a potom klikněte na **přístupové klíče** , abyste mohli zobrazit, kopírovat a znovu vygenerovat přístupové klíče k účtu. Okno Přístupové klíče také obsahuje předem nakonfigurované připojovací řetězce s využitím primárního a sekundárního klíče, které můžete zkopírovat a používat ve svých aplikacích.

:::image type="content" source="media/witness/cloud-witness-1.png" alt-text="Přístupové klíče pro disk s kopií cloudu" lightbox="media/witness/cloud-witness-1.png":::

### <a name="view-and-copy-endpoint-url-links"></a>Zobrazení a kopírování odkazů na adresu URL koncového bodu

Při vytváření účtu úložiště se generují následující adresy URL ve formátu: `https://<Storage Account Name>.<Storage Type>.<Endpoint>`  

Disk s kopií cloudu vždycky jako typ úložiště používá **objekt BLOB** . Azure jako koncový bod používá **. Core.Windows.NET** . Při konfiguraci sdílené složky cloudu je možné ji nakonfigurovat s jiným koncovým bodem podle vašeho scénáře (například Microsoft Azure Datacenter v Číně má jiný koncový bod).  

> [!NOTE]  
> Adresa URL koncového bodu je generována automaticky prostředkem určujícím cloudu a není nutná žádná další konfigurace pro adresu URL.  

#### <a name="to-view-and-copy-endpoint-url-links"></a>Zobrazení a zkopírování odkazů na adresu URL koncového bodu

V Azure Portal přejděte na svůj účet úložiště, klikněte na **všechna nastavení** a potom klikněte na **vlastnosti** . zobrazí se a zkopírují se adresy URL koncových bodů.  

:::image type="content" source="media/witness/cloud-witness-2.png" alt-text="Adresa URL koncového bodu sdílené složky cloudu" lightbox="media/witness/cloud-witness-2.png":::  

## <a name="set-up-a-witness-using-windows-powershell"></a>Nastavení určujícího disku pomocí Windows PowerShellu

Pokud chcete nastavit určující cluster pomocí PowerShellu, spusťte jednu z následujících rutin.

Pomocí následující rutiny vytvořte sdílenou složku Azure v cloudu:

```powershell
Set-ClusterQuorum –Cluster "Cluster1" -CloudWitness -AccountName "AzureStorageAccountName" -AccessKey "AzureStorageAccountAccessKey"
```

Pomocí následující rutiny vytvořte určující sdílenou složku:

```powershell
Set-ClusterQuorum -FileShareWitness "\\fileserver\share" -Credential (Get-Credential)
```

## <a name="next-steps"></a>Další kroky

- Další informace o kvoru clusteru najdete v tématu [Principy kvora clusteru a fondu v Azure Stack HCI](../concepts/quorum.md).

- Další informace o vytváření a správě účtů Azure Storage najdete v tématu [o účtech Azure Storage](/azure/storage/common/storage-account-create).