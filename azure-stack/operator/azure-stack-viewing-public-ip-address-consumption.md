---
title: Správa síťových prostředků
titleSuffix: Azure Stack Hub
description: Naučte se spravovat síťové prostředky, včetně fondu adres MAC a spotřeby veřejných IP adres v oblasti.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: inhenkel
ms.reviewer: scottnap
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: b96485e74286cbbbd2ff20f2378dc7c452849b52
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487392"
---
# <a name="manage-network-resources-in-azure-stack-hub"></a>Správa síťových prostředků v centru Azure Stack

## <a name="mac-address-pool"></a>Fond adres MAC

Azure Stack hub používá ke automatickému vygenerování a přiřazování adres MAC k virtuálním počítačům statický fond adres MAC. Tento fond adres MAC je automaticky vygenerován během nasazování a používá následující rozsah:

- StartMacAddress: 00-1D-D8-B7-00-00
- EndMacAddress: 00-1D-D8-F4-FF-FF

> [!Note]  
> Tento fond adres MAC je stejný pro každý Azure Stack systém centra a nedá se konfigurovat.

V závislosti na tom, jak se virtuální sítě připojí k existujícím firemním sítím, můžete očekávat duplicitní adresy MAC virtuálních počítačů.

Další informace najdete v části využití fondu adres MAC pomocí rutiny [Get-AzsMacAddressPool](/powershell/module/azs.fabric.admin/get-azsmacaddresspool) v modulu PowerShell pro správce centra Azure Stack.

## <a name="view-public-ip-address-consumption-in-azure-stack-hub"></a>Zobrazení spotřeby veřejných IP adres v centru Azure Stack

Jako správce cloudu můžete zobrazit:
 - Počet veřejných IP adres, které byly přiděleny klientům.
 - Počet veřejných IP adres, které jsou stále k dispozici pro přidělení.
 - Procento veřejných IP adres, které byly přiděleny v tomto umístění.

Na dlaždici **využití fondů veřejné IP** adresy se zobrazuje počet veřejných IP adres spotřebovaných napříč fondy veřejných IP adres. Pro každou IP adresu dlaždice zobrazuje využití pro instance virtuálních počítačů IaaS tenanta, služby infrastruktury prostředků infrastruktury a prostředky veřejné IP adresy, které byly explicitně vytvořeny klienty.

Účelem dlaždice je poskytnout operátorům Azure Stackového rozbočovače smysl počtu veřejných IP adres, které se používají v tomto umístění. Číslo pomáhá správcům určit, jestli mají nedostatek na tomto prostředku.

Položka nabídky **veřejné IP adresy** v části **prostředky tenanta** uvádí jenom veřejné IP adresy, které byly *explicitně vytvořené klienty*. Položku nabídky najdete v podokně síť **poskytovatelé prostředků**  ->  **Network** . Počet **použitých** veřejných IP adres na dlaždici **využití fondů veřejné IP** adresy se vždycky liší od (větší než) čísla na dlaždici **veřejné IP adresy** v části **prostředky tenanta**.

### <a name="view-the-public-ip-address-usage-information"></a>Zobrazit informace o využití veřejné IP adresy

Zobrazení celkového počtu veřejných IP adres, které byly spotřebovány v oblasti:

1. Na portálu Azure Stack správce centra vyberte **všechny služby**. Pak v kategorii **Správa** vyberte **síť**.
1. V podokně **síť** se zobrazí dlaždice **využití fondů veřejných IP adres** v části **Přehled** .

    ![Podokno poskytovatele síťových prostředků na portálu pro správu centra Azure Stack](media/azure-stack-viewing-public-ip-address-consumption/ip-address-consumption-01.png)

**Použité** číslo představuje počet přiřazených veřejných IP adres z fondů veřejných IP adres. **Bezplatné** číslo představuje počet veřejných IP adres z fondů veřejných IP adres, které nebyly přiřazeny a jsou stále k dispozici. **Použité číslo%** představuje počet využitých nebo přiřazených adres jako procento celkového počtu veřejných IP adres ve fondech veřejných IP adres v tomto umístění.

### <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>Zobrazení veřejných IP adres vytvořených předplatnými tenanta

V části **prostředky tenanta**vyberte **veřejné IP adresy** . Projděte si seznam veřejných IP adres, které jsou explicitně vytvořeny pomocí odběrů klientů v konkrétní oblasti.

![Veřejné IP adresy tenanta na portálu pro správu centra Azure Stack](media/azure-stack-viewing-public-ip-address-consumption/ip-address-consumption-02.png)

Můžete si všimnout, že některé veřejné IP adresy, které byly dynamicky přiděleny, se zobrazí v seznamu. Adresa ale ještě není přidružená. Prostředek adresy byl vytvořen v poskytovateli síťových prostředků, ale ještě není v síťovém adaptéru.

Síťový adaptér nepřiřazuje k prostředku adresu, dokud se neváže k rozhraní, síťové kartě (NIC), nástroji pro vyrovnávání zatížení nebo bráně virtuální sítě. Když se veřejná IP adresa váže k rozhraní, síťový adaptér přidělí IP adresu. Adresa se zobrazí v poli **adresa** .

### <a name="view-the-public-ip-address-information-summary-table"></a>Zobrazit tabulku souhrnu informací o veřejné IP adrese

V různých případech jsou k disadrese veřejné IP adresy, které určují, jestli se adresa zobrazuje v jednom seznamu nebo jiné.

| **Případ přiřazení veřejné IP adresy** | **Zobrazuje se v souhrnu využití.** | **Zobrazí se v seznamu veřejných IP adres tenanta.** |
| --- | --- | --- |
| Dynamická veřejná IP adresa není ještě přiřazená síťovému rozhraní nebo nástroji pro vyrovnávání zatížení (dočasné). |Ne |Ano |
| Dynamická veřejná IP adresa přiřazená síťovému rozhraní nebo nástroji pro vyrovnávání zatížení. |Ano |Ano |
| Statická veřejná IP adresa přiřazená síťové kartě klienta nebo nástroji pro vyrovnávání zatížení. |Ano |Ano |
| Statická veřejná IP adresa přiřazená ke koncovému bodu služby infrastruktury prostředků infrastruktury. |Ano |Ne |
| Veřejná IP adresa se implicitně vytvořila pro instance virtuálních počítačů s IaaS a používá se pro odchozí překlad adres (NAT) ve virtuální síti. Ty se vytvářejí na pozadí vždy, když tenant vytvoří instanci virtuálního počítače, aby virtuální počítače mohli odesílat informace na Internet. |Ano |No |

## <a name="next-steps"></a>Další kroky

[Správa účtů úložiště v centru Azure Stack](azure-stack-manage-storage-accounts.md)
