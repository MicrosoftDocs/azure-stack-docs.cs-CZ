---
title: Nastavení pravidel spřažení virtuálních počítačů pomocí Windows PowerShellu
description: Zjistěte, jak nastavit pravidla spřažení virtuálních počítačů pomocí Windows PowerShellu.
author: v-dasis
ms.topic: how-to
ms.date: 10/14/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e2cb3fa0efb11664924431ed0434547c832ceab4
ms.sourcegitcommit: 8122672409954815e472a5b251bb7319fab8f951
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92060149"
---
# <a name="create-server-and-site-affinity-rules-for-vms"></a>Vytvořit pravidla spřažení serveru a lokality pro virtuální počítače

> Platí pro Azure Stack HCI, verze 20H2

Pomocí centra pro správu Windows nebo Windows PowerShellu můžete snadno vytvořit spřažení a pravidla spřažení pro virtuální počítače (VM) v clusteru.

Spřažení je pravidlo, které vytváří vztah mezi dvěma nebo více skupinami prostředků nebo rolemi, jako jsou virtuální počítače (VM), aby se zachovaly na stejném serveru, clusteru nebo lokalitě. Proti spřažení je opak v tom, že se používá k udržení zadaných virtuálních počítačů nebo skupin prostředků od sebe navzájem, například dvou řadičů domény umístěných na samostatných serverech nebo v samostatných lokalitách pro zotavení po havárii.

Spřažení a pravidla proti spřažení se používají podobně jako způsob, jakým Azure používá Zóny dostupnosti. V Azure můžete nakonfigurovat Zóny dostupnosti, aby byly virtuální počítače v různých zónách a od sebe navzájem i ve stejné zóně.  

Pomocí spřažení a pravidel proti spřažení by libovolný clusterový virtuální počítač zůstal ve stejném uzlu clusteru, nebo by se mu zabránilo ve stejném uzlu clusteru.  Tímto způsobem může být jediným způsobem, jak přesunout virtuální počítač z uzlu, to provést ručně.  Virtuální počítače můžete také udržovat společně s vlastním úložištěm, jako je například sdílený svazek clusteru (CSV), na kterém se nachází jeho VHDX.

Kombinací spřažení a pravidel proti spřažení můžete také nakonfigurovat roztažené clustery na dvě lokality a zachovat virtuální počítače v lokalitě, ve kterých se musí nacházet.

## <a name="using-windows-admin-center"></a>Použití centra pro správu Windows

Pomocí centra pro správu Windows můžete vytvořit základní spřažení a pravidla proti spřažení.

:::image type="content" source="media/vm-affinity/vm-affinity-rules.png" alt-text="Obrazovka virtuálních počítačů" lightbox="media/vm-affinity/vm-affinity-rules.png":::

1. V centru pro správu Windows Home v části **všechna připojení**vyberte server nebo cluster, pro který chcete vytvořit pravidlo virtuálního počítače.
1. V části **nástroje**vyberte **Nastavení**.
1. V části **Nastavení**vyberte **pravidla spřažení**a potom v části **pravidla spřažení**vyberte **vytvořit pravidlo** .
1. V části **název pravidla**zadejte název pravidla.
1. V části Typ pravidla vyberte možnost **společně (stejný server)** nebo **vzdálené (různé servery)** , aby se virtuální počítače umístily na stejný server nebo na různé servery.
1. V části **platí pro**vyberte virtuální počítače, na které se bude toto pravidlo vztahovat. K přidání dalších virtuálních počítačů do pravidla použijte tlačítko **Přidat** .
1. Po dokončení klikněte na **vytvořit pravidlo**.
1. Pokud chcete pravidlo odstranit, jednoduše ho vyberte a klikněte na **Odstranit pravidlo**.

## <a name="using-windows-powershell"></a>Použití Windows PowerShellu

Pomocí Windows PowerShellu můžete vytvořit složitější pravidla, než použijte centrum pro správu systému Windows. Obvykle nastavujete pravidla ze vzdáleného počítače, nikoli na hostitelském serveru v clusteru. Tento vzdálený počítač se nazývá počítač pro správu.

Pokud spouštíte příkazy prostředí Windows PowerShell z počítače pro správu, zahrňte `-Name` `-Cluster` parametr nebo s názvem clusteru, který spravujete. V případě potřeby budete při použití `-ComputerName` parametru pro uzel serveru taky muset zadat plně kvalifikovaný název domény (FQDN).

### <a name="new-powershell-cmdlets"></a>Nové rutiny PowerShellu

Pokud chcete vytvořit pravidla spřažení pro clustery, použijte následující nové rutiny PowerShellu:

#### <a name="new-clusteraffinityrule"></a>New-ClusterAffinityRule

**`New-ClusterAffinityRule`** Rutina se používá k vytváření nových pravidel.  Pomocí tohoto příkazu zadáte název pravidla a také typ pravidla, kde:

**`-Name`** je název pravidla

**`-RuleType`** hodnoty jsou `SameFaultDomain` | `SameNode` | `DifferentFaultDomain` | `DifferentNode`

Příklad:

```powershell
New-ClusterAffinityRule -Name -RuleType SameFaultDomain -Cluster Cluster1
```

#### <a name="set-clusteraffinityrule"></a>Set-ClusterAffinityRule

**`Set-ClusterAffinityRule`** Rutina slouží k povolení nebo zakázání pravidla, kde:

**`-Name`** je název pravidla, které se má povolit nebo zakázat.

**`-Enabled`** | **`Disabled`** povoluje nebo zakazuje pravidlo.

Příklad:

```powershell
Set-ClusterAffinityRule -Name -Enabled -Cluster Cluster1
```

#### <a name="get-clusteraffinityrule"></a>Get-ClusterAffinityRule

**`Get-ClusterAffinityRule`** Rutina se používá k zobrazení zadaného pravidla a typu, který je.  Pokud **`-Name`** parametr není zadán, budou uvedena všechna pravidla.

Příklad:

```powershell
Get-ClusterAffinityRule -Name -Cluster Cluster1
```

#### <a name="add-clustergrouptoaffinityrule"></a>Add-ClusterGroupToAffinityRule

**`Add-ClusterGroupToAffinityRule`** Rutina se používá k přidání role nebo názvu skupiny virtuálních počítačů do konkrétního pravidla spřažení, kde:

**`-Groups`** je název skupiny nebo role, která se má přidat do pravidla

**`-Name`** je název pravidla, do kterého se má přidat

Příklad:

```powershell
Add-ClusterGroupToAffinityRule -Groups -Name -Cluster Cluster1
```

#### <a name="add-clustersharedvolumetoaffinityrule"></a>Add-ClusterSharedVolumeToAffinityRule

**`Add-ClusterSharedVolumeToAffinityRule`** Umožňuje vašim virtuálním počítačům zůstat v kombinaci s sdílený svazek clusteru, na kterých se nachází VHDX, kde:

**`-ClusterSharedVolumes`** je disk (y) sdíleného svazku clusteru, který chcete přidat do pravidla

**`-Name`** je název pravidla, do kterého se má přidat

Příklad:

```powershell
Add-ClusterSharedVolumeToAffinityRule  -ClusterSharedVolumes -Name -Cluster Cluster1
```

#### <a name="remove-clusteraffinityrule"></a>Remove-ClusterAffinityRule

**`Remove-ClusterAffinityRule`** Odstraní zadané pravidlo, kde **`-Name`** je název pravidla.

Příklad:

```powershell
Remove-ClusterAffinityRule -Name -Cluster Cluster1
```

#### <a name="remove-clustergroupfromaffinityrule"></a>Remove-ClusterGroupFromAffinityRule

**`Remove-ClusterGroupFromAffinityRule`** Odebere skupinu virtuálních počítačů nebo roli z konkrétního pravidla, ale nezakáže ani neodstraní pravidlo, kde:

**`-Name`** je název pravidla

**`-Groups`** jsou skupiny nebo role, které chcete odebrat z pravidla

Příklad:

```powershell
Remove-ClusterGroupFromAffinityRule -Name -Groups -Cluster Cluster1
```

#### <a name="remove-clustersharedvolumefromaffinityrule"></a>Remove-ClusterSharedVolumeFromAffinityRule

**`Remove-ClusterSharedVolumeFromAffinityRule`** Rutina se používá k odebrání sdílených svazků clusteru z konkrétního pravidla, ale nezakáže ani neodstraní pravidlo, kde:

**`-ClusterSharedVolumes`** je disk (y) sdíleného svazku clusteru, který chcete z pravidla odebrat

**`-Name`** je název pravidla, do kterého se má přidat

Příklad:

```powershell
Remove-ClusterSharedVolumeFromAffinityRule -ClusterSharedVolumes -Name -Cluster Cluster1
```

### <a name="existing-powershell-cmdlets"></a>Existující rutiny PowerShellu

S nástupem nových rutin jsme také přidali další nové přepínače do několika existujících rutin.

#### <a name="move-clustergroup"></a>Move-ClusterGroup

Nový `-IgnoreAffinityRule` přepínač ignoruje pravidlo a přesune skupinu prostředků clusteru do jiného uzlu clusteru. Další informace o této rutině najdete v tématu [Move-clustering](/powershell/module/failoverclusters/move-clustergroup).

Příklad:

```powershell
Move-ClusterGroup -IgnoreAffinityRule -Cluster Cluster1
```

> [!NOTE]
> Pokud je pravidlo přesunutí platné (podporováno), přesunou se také všechny ovlivněné skupiny a role.  Pokud přesun virtuálního počítače bude vědomě porušovat pravidlo, ale je potřeba na jednorázovém čase, použijte `-IgnoreAffinityRule` přepínač a umožněte tak přechodu. V takovém případě se zobrazí upozornění na porušení virtuálního počítače. Pravidlo pak můžete podle potřeby povolit znovu.

#### <a name="start-clustergroup"></a>Start-ClusterGroup

Nový `-IgnoreAffinityRule` přepínač ignoruje pravidlo a do aktuálního umístění převede skupinu prostředků clusteru do online režimu. Další informace o této rutině najdete v tématu [Start-clustering](/powershell/module/failoverclusters/start-clustergroup).

Příklad:

```powershell
Start-ClusterGroup -IgnoreAffinityRule -Cluster Cluster1
```

## <a name="affinity-rule-examples"></a>Příklady pravidel spřažení

Pravidla spřažení jsou společně pravidla, která udržují prostředky na stejném serveru, clusteru nebo lokalitě. Tady je několik běžných scénářů nastavení pravidel spřažení.

### <a name="scenario-1"></a>Scénář 1

Předpokládejme, že máte virtuální počítač SQL Server a virtuální počítač s webovým serverem. Tyto dva virtuální počítače musí vždycky zůstat ve stejné lokalitě, ale nemusí být nutně na stejném uzlu clusteru v lokalitě.  `SameFaultDomain`To je možné, jak je znázorněno níže:

```powershell
New-ClusterAffinityRule -Name WebData -Ruletype SameFaultDomain -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,WEB1 –Name WebData -Cluster Cluster1

Set-ClusterAffinityRule -Name WebData -Enabled 1 -Cluster Cluster1
```

Chcete-li zobrazit toto pravidlo a jeho konfiguraci, použijte **`Get-ClusterAffinityRule`** k zobrazení výstupu rutinu:

```powershell
Get-ClusterAffinityRule -Name WebData -Cluster Cluster1

Name        RuleType          Groups        Enabled
----        ---------         ------        -------
WebData     SameFaultDomain   {SQL1, WEB1}     1
```

### <a name="scenario-2"></a>Scénář 2

Použijte stejný scénář výše, kromě určení, že virtuální počítače musí být umístěny ve stejném uzlu clusteru, ale nemusí být nutně ve stejné lokalitě. Pomocí `SameNode` můžete tuto možnost nastavit takto:

```powershell
New-ClusterAffinityRule -Name WebData1 -Ruletype SameNode -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,WEB1 –Name WebData1 -Cluster Cluster1

Set-ClusterAffinityRule -Name WebData1 -Enabled 1 -Cluster Cluster1
```

Chcete-li zobrazit pravidlo a jeho konfiguraci, použijte **`Get-ClusterAffinityRule`** k zobrazení výstupu rutinu:

```powershell
Get-ClusterAffinityRule -Name WebData1 -Cluster Cluster1

Name    RuleType    Groups        Enabled
----    --------    ------        -------
DC      SameNode    {SQL1, WEB1}     1
```

## <a name="anti-affinity-rule-examples"></a>Příklady pravidel ochrany proti spřažení

Pravidla proti spřažení jsou "odděleně" pravidla, která oddělují prostředky a umísťují je na různé servery, clustery nebo lokality.

### <a name="scenario-1"></a>Scénář 1
Každý spuštěný SQL Server máte dva virtuální počítače ve stejném clusteru s více lokalitami Azure Stack HCI.  Každý virtuální počítač využívá velký objem paměti, procesoru a prostředků úložiště.  Pokud se oba konce nacházejí ve stejném uzlu, může to způsobit problémy s výkonem jednoho nebo obou při jejich konkurenci pro paměť, procesor a cykly úložiště.  Když použijete pravidlo ochrany proti spřažení s `DifferentNode` jako typ pravidla, tyto virtuální počítače budou vždycky zůstat na různých uzlech clusteru.  

Příklady příkazů pro tento příkaz:

```powershell
New-ClusterAffinityRule -Name SQL -Ruletype DifferentNode -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,SQL2 –Name SQL -Cluster Cluster1

Set-ClusterAffinityRule -Name SQL -Enabled 1 -Cluster Cluster1
```

Chcete-li zobrazit pravidlo a jeho konfiguraci, použijte **`Get-ClusterAffinityRule`** k zobrazení výstupu rutinu:

```powershell
Get-ClusterAffinityRule -Name SQL -Cluster Cluster1

Name    RuleType        Groups        Enabled
----    -----------     -------       -------
SQL     DifferentNode   {SQL1, SQL2}     1
```

### <a name="scenario-2"></a>Scénář 2

Řekněme, že máte Azure Stack roztaženého clusteru HCI se dvěma lokalitami (domén selhání). Máte dva řadiče domény, které chcete uchovat v samostatných lokalitách. Pomocí pravidla ochrany proti spřažení s `DifferentFaultDomain` typem pravidla budou tyto řadiče domény vždycky zůstat v různých lokalitách.  Příklady příkazů pro tento příkaz:

```powershell
New-ClusterAffinityRule -Name DC -Ruletype DifferentFaultDomain -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups DC1,DC2 –Name DC -Cluster Cluster1

Set-ClusterAffinityRule -Name DC -Enabled 1 -Cluster Cluster1
```

Chcete-li zobrazit toto pravidlo a jeho konfiguraci, použijte **`Get-ClusterAffinityRule`** k zobrazení výstupu rutinu:

```powershell
Get-ClusterAffinityRule -Name DC -Cluster Cluster1

Name    RuleType                Groups        Enabled
----    --------                -------       -------
DC      DifferentFaultDomain    {DC1, DC2}       1
```

## <a name="combined-rule-examples"></a>Příklady kombinovaných pravidel

Kombinování vztahů a pravidel proti spřažení můžete snadno konfigurovat různé kombinace virtuálních počítačů v clusteru s více lokalitami.  V tomto scénáři má každá lokalita tři virtuální počítače: SQL Server (SQL), webový server (WEB) a řadič domény (DC).  Pro každou kombinaci můžete použít pravidla spřažení s `SameFaultDomain` a zajistit tak jejich všechny ve stejné lokalitě.  Pro každou lokalitu můžete také nastavit řadiče domény pomocí pravidel pro spřažení a `DifferentFaultDomain` zachovat virtuální počítače řadičů domény v samostatných lokalitách, jak je znázorněno níže:

```powershell
New-ClusterAffinityRule -Name Site1Trio -Ruletype SameFaultDomain -Cluster Cluster1

New-ClusterAffinityRule -Name Site2Trio -Ruletype SameFaultDomain -Cluster Cluster1

New-ClusterAffinityRule -Name TrioApart -Ruletype DifferentFaultDomain -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1,WEB1,DC1 –Name Site1Trio -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL2,WEB2,DC2 –Name Site2Trio -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups DC1,DC2 –Name TrioApart -Cluster Cluster1

Set-ClusterAffinityRule -Name Site1Trio -Enabled 1 -Cluster Cluster1

Set-ClusterAffinityRule -Name Site2Trio -Enabled 1 -Cluster Cluster1

Set-ClusterAffinityRule -Name TrioApart -Enabled 1 -Cluster Cluster1
```

Pokud chcete zobrazit pravidla a způsob jejich konfigurace, použijte **`Get-ClusterAffinityRule`** rutinu bez `-Name` přepínače a uvidíte všechna vytvořená pravidla a jejich výstup.

```powershell
Get-ClusterAffinityRule -Cluster Cluster1

Name        RuleType               Groups            Enabled
----        --------               ------            -------
Site1Trio   SameFaultDomain        {SQL1, WEB1, DC1}    1
Site2Trio   SameFaultDomain        {SQL2, WEB2, DC2}    1
TrioApart   DifferentFaultDomain   {DC1, DC2}           1
```

## <a name="storage-affinity-rules"></a>Pravidla spřažení úložiště

Virtuální počítač a jeho disk VHDX můžete také zachovat na sdílený svazek clusteru (CSV) na stejném uzlu clusteru. Tím se zabrání přesměrování sdíleného svazku clusteru, což může zpomalit spuštění nebo zastavení virtuálního počítače.  S ohledem na kombinované spřažení a scénář anti-spřažení si můžete zachovat virtuální počítač SQL a sdílený svazek clusteru na stejném uzlu clusteru.  K tomu použijte následující příkazy:

```powershell
New-ClusterAffinityRule -Name SQL1CSV1 -Ruletype SameNode -Cluster Cluster1

New-ClusterAffinityRule -Name SQL2CSV2 -Ruletype SameNode -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL1 –Name SQL1CSV1 -Cluster Cluster1

Add-ClusterGroupToAffinityRule -Groups SQL2 –Name SQL2CSV2 -Cluster Cluster1

Add-ClusterSharedVolumeToAffinityRule -ClusterSharedVolumes CSV1 -Name SQL1CSV1 -Cluster Cluster1

Add-ClusterSharedVolumeToAffinityRule -ClusterSharedVolumes CSV2 -Name SQL2CSV2 -Cluster Cluster1

Set-ClusterAffinityRule -Name SQL1CSV1 -Enabled 1 -Cluster Cluster1

Set-ClusterAffinityRule -Name SQL2CSV2 -Enabled 1 -Cluster Cluster1
```

Chcete-li zobrazit tato pravidla a způsob jejich konfigurace, použijte **`Get-ClusterAffinityRule`** rutinu bez přepínače-Name a zobrazte výstup.

```powershell
Get-ClusterAffinityRule -Cluster Cluster1

Name        RuleType               Groups            Enabled
----        --------               ------            -------
Site1Trio   SameFaultDomain        {SQL1, WEB1, DC1}    1
Site2Trio   SameFaultDomain        {SQL2, WEB2, DC2}    1
TrioApart   DifferentFaultDomain   {DC1, DC2}           1
SQL1CSV1    SameNode               {SQL1, <CSV1-GUID>}  1
SQL2CSV2    SameNode               {SQL2, <CSV2-GUID>}  1
```

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak spravovat virtuální počítače. Přečtěte si téma [Správa virtuálních počítačů v Azure Stack HCI pomocí centra pro správu systému Windows](../manage/vm.md).
