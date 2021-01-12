---
title: Přidání nebo odebrání serverů pro Azure Stack clusteru HCI
description: Naučte se přidávat nebo odebírat uzly serveru z clusteru v Azure Stack HCI.
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 01/06/2021
ms.openlocfilehash: 8b27859b7afab0a6e279774e43d0269f6d58065a
ms.sourcegitcommit: 1465bca8b7f87ea6f24faf47e86c2ba497943b28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98103122"
---
# <a name="add-or-remove-servers-for-an-azure-stack-hci-cluster"></a>Přidání nebo odebrání serverů pro Azure Stack clusteru HCI

> Platí pro: Azure Stack HCI, verze 20H2

Můžete snadno přidat nebo odebrat servery z clusteru v Azure Stack HCI. Mějte na paměti, že každý nový fyzický server musí přesně odpovídat zbývajícím serverům v clusteru, když přichází do typu procesoru, paměti, počtu jednotek a typu a velikosti jednotek.

Kdykoli přidáte nebo odeberete server, musíte také provést ověření clusteru, abyste zajistili, že cluster funguje normálně. To platí pro nestret a roztažené clustery.

## <a name="obtain-oem-hardware"></a>Získat hardware výrobce OEM

Prvním krokem je získání nového hardwaru HCL od původního výrobce OEM. Pokud přidáváte nový serverový hardware pro použití ve vašem clusteru, vždy si přečtěte dokumentaci k poskytované výrobci OEM.

1. Umístěte nový fyzický server do stojanu a zapojte ho správně.
1. Povolte porty fyzických přepínačů a v případě potřeby upravte seznamy řízení přístupu (ACL) a identifikátory sítě VLAN.
1. Nakonfigurujte správnou IP adresu v řadiči pro správu základní desky (BMC) a použijte všechna nastavení systému BIOS podle pokynů výrobce OEM.
1. Použijte aktuální standardní hodnotu firmwaru pro všechny komponenty pomocí nástrojů poskytovaných výrobcem OEM.
1. Spusťte ověřovací testy OEM, abyste zajistili stejnorodost s existujícími servery clusteru.

## <a name="add-a-server-to-a-cluster"></a>Přidání serveru do clusteru

Po správném vystavení serveru použijte centrum pro správu systému Windows k připojení serveru k vašemu clusteru.

:::image type="content" source="media/manage-cluster/add-server.png" alt-text="Obrazovka Přidat server" lightbox="media/manage-cluster/add-server.png":::

1. V **centru pro správu systému Windows** vyberte ze šipky nahoru v horním rozevíracím seznamu položku **Správce clusteru** .
1. V části **připojení clusteru** vyberte cluster.
1. V části **nástroje** vyberte **servery**.
1. V části **servery** vyberte kartu **inventář** .
1. Na kartě **inventář** vyberte **Přidat**.
1. Do pole **název serveru** zadejte plně kvalifikovaný název domény serveru, který chcete přidat, klikněte na **Přidat** a potom v dolní části klikněte na **Přidat** znovu.
1. Ověřte, že se server úspěšně přidal do vašeho clusteru.

## <a name="remove-a-server-from-a-cluster"></a>Odebrání serveru z clusteru

Postup odebrání serveru z clusteru je podobný jako při přidávání serveru do clusteru.

Pamatujte, že když odeberete server, odeberete také všechny virtuální počítače, jednotky a úlohy, které jsou přidruženy k serveru.

:::image type="content" source="media/manage-cluster/remove-server.png" alt-text="Dialogové okno odebrat server" lightbox="media/manage-cluster/remove-server.png":::

1. V **centru pro správu systému Windows** vyberte ze šipky nahoru v horním rozevíracím seznamu položku **Správce clusteru** .
1. V části **připojení clusteru** vyberte cluster.
1. V části **nástroje** vyberte **servery**.
1. V části **servery** vyberte kartu **inventář** .
1. Na kartě **inventář** vyberte server, který chcete odebrat, a pak vyberte **Odebrat**.
1. Pokud chcete odebrat i jakékoli serverové jednotky z fondu úložiště, povolte toto políčko.
1. Ověřte, že se server úspěšně odebral z clusteru.

Kdykoli přidáváte nebo odebíráte servery z clusteru, ujistěte se, že a následně spustíte test ověření clusteru.

## <a name="add-server-pairs-to-a-stretched-cluster"></a>Přidání dvojic serverů do roztaženého clusteru

Roztažené clustery vyžadují stejný počet uzlů serveru a stejný počet jednotek v každé lokalitě. Když přidáte dvojici serverů do roztaženého clusteru, jejich jednotky se okamžitě přidají do fondu úložiště obou lokalit v roztaženém clusteru. Pokud fond úložiště v každé lokalitě nemá stejnou velikost v době přidání, je odmítnut. Důvodem je to, že velikost fondu úložiště musí být stejná mezi lokalitami.

Pokud si chcete přehrát video při přidávání uzlů serveru do roztaženého clusteru, vezměte v úvahu několik minut:

> [!VIDEO https://www.youtube.com/embed/AVHPkRmsZ5Y]

Servery můžete přidat nebo odebrat do roztaženého clusteru pomocí prostředí Windows PowerShell. Pomocí rutin [Get-ClusterFaultDomainXML](https://docs.microsoft.com/powershell/module/failoverclusters/get-clusterfaultdomainxml) a [set-ClusterFaultDomainXML](https://docs.microsoft.com/powershell/module/failoverclusters/set-clusterfaultdomainxml) nejprve před přidáním serverů upravíte informace o lokalitě (doména selhání).

Pak můžete přidat dvojici serverů do každé lokality současně pomocí rutiny [Add-ClusterNode](https://docs.microsoft.com/powershell/module/failoverclusters/add-clusternode) a umožnit tak přidávání jednotlivých jednotek na novém serveru současně.

Obvykle budete spravovat clustery ze vzdáleného počítače, nikoli na serveru v clusteru. Tento vzdálený počítač se nazývá počítač pro správu.

> [!NOTE]
> Pokud spouštíte příkazy prostředí PowerShell z počítače pro správu, zahrňte `-Cluster` parametr s názvem clusteru, který spravujete.

OK, pojďme začít:

1. K určení stavu clusteru použijte následující rutiny PowerShellu:

    Vrátí seznam aktivních serverů v clusteru:

     ```powershell
    Get-ClusterNode
    ```

    Vrátí statistiku pro fond úložiště clusteru:

    ```powershell
    Get-StoragePool pool*
    ```

    Zobrazí seznam serverů, na kterých je lokalita (doména selhání):

    ```powershell
    Get-ClusterFaultDomain
    ```

1. Otevřete `Sites.xml` soubor v programu Poznámkový blok nebo jiném textovém editoru:

    ```powershell
    Get-ClusterFaultDomainXML | out-file sites.xml
    ```
 
    ```powershell
    notepad
    ```

1. Přejděte na místo, kde `Sites.xml` se soubor nachází místně na počítači pro správu, a otevřete soubor. `Sites.xml`Soubor bude vypadat podobně jako tento:

    ```
    <Topology>
        <Site Name="Site1" Description="" Location="">
            <Node Name="Server1" Description="" Location="">
            <Node Name="Server2" Description="" Location="">
        </Site>
        <Site Name="Site2" Description="" Location="">
            <Node Name="Server3" Description="" Location="">
            <Node Name="Server4" Description="" Location="">
        </Site>
    <Topology>
    ```

1. V tomto příkladu přidáte server do každé lokality ( `Server5` , `Server6` ) následujícím způsobem:

    ```
    <Topology>
        <Site Name="Site1" Description="" Location="">
            <Node Name="Server1" Description="" Location="">
            <Node Name="Server2" Description="" Location="">
            <Node Name="Server5" Description="" Location="">
        </Site>
        <Site Name="Site2" Description="" Location="">
            <Node Name="Server3" Description="" Location="">
            <Node Name="Server4" Description="" Location="">
            <Node Name="Server6" Description="" Location="">
        </Site>
    <Topology>
    ```

1. Upravte informace o aktuální lokalitě (doména selhání).  První příkaz nastaví proměnnou pro získání obsahu `Sites.xml` souboru a jeho výstup. Druhý příkaz nastaví změnu na základě proměnné `$XML` .

    ```
    $XML = Get-Content .\sites.xml | out-string
    Set-ClusterFaultDomainXML -xml $XML
    ```

1. Ověřte správnost provedených změn:

    ```
    Get-ClusterFaultDomain
    ```

1. Přidejte do clusteru dvojici serverů pomocí `Add-ClusterNode` rutiny:

    ```
    Add-ClusterNode -Name Server5,Server6
    ```

Po úspěšném přidání serverů jsou přidružené jednotky automaticky přidány do fondů úložiště jednotlivých lokalit. Nakonec Health Service vytvoří úlohu úložiště, která bude zahrnovat nové jednotky.

## <a name="remove-server-pairs-from-a-stretched-cluster"></a>Odebrání dvojic serveru z roztaženého clusteru

Odebrání dvojice serveru z roztaženého clusteru je podobný proces pro přidání dvojice serverů, ale místo toho použijte rutinu [Remove-ClusterNode](https://docs.microsoft.com/powershell/module/failoverclusters/remove-clusternode) .

1. K určení stavu clusteru použijte následující rutiny PowerShellu:

    Vrátí seznam aktivních serverů v clusteru:

     ```powershell
    Get-ClusterNode
    ```

    Vrátí statistiku pro fond úložiště clusteru:

    ```powershell
    Get-StoragePool pool*
    ```

    Zobrazí seznam serverů, na kterých je lokalita (doména selhání):

    ```powershell
    Get-ClusterFaultDomain
    ```

1. Otevřete `Sites.xml` soubor v programu Poznámkový blok nebo jiném textovém editoru:

    ```powershell
    Get-ClusterFaultDomainXML | out-file sites.xml
    ```
 
    ```powershell
    notepad
    ```

1. Pomocí předchozího příkladu v `Sites.xml` souboru odeberte `<Node Name="Server5" Description="" Location="">` a  `<Node Name="Server6" Description="" Location="">` položku XML pro každou lokalitu.
1. Upravte informace o aktuálním webu (doména selhání) pomocí následujících dvou rutin:

    ```
    $XML = Get-Content .\sites.xml | out-string
    Set-ClusterFaultDomainXML -xml $XML
    ```

1. Ověřte správnost provedených změn:

    ```
    Get-ClusterFaultDomain
    ```
1. Odeberte páry serveru z clusteru pomocí `Remove-ClusterNode` rutiny:

    ```
    Remove-ClusterNode -Name Server5,Server6
    ```

Po úspěšném odebrání serverů se přidružené jednotky z fondů webů automaticky odeberou. Nakonec Health Service vytvoří úlohu úložiště pro odebrání těchto jednotek.

## <a name="next-steps"></a>Další kroky

- Po přidání nebo odebrání serveru byste měli cluster ověřit. Další informace najdete v tématu [ověření clusteru](../deploy/validate.md) pro další informace.