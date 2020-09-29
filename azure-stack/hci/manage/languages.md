---
title: Změna jazyků v Azure Stack HCI
description: V tomto tématu najdete pokyny, jak změnit jazyky v operačním systému Azure Stack HCI, Windows 10, centrum pro správu Windows a Microsoft Edge.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: 09245e4498e87896b95df993e23693ae1ed23eaf
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947329"
---
# <a name="change-languages-in-azure-stack-hci"></a>Změna jazyků v Azure Stack HCI

>Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

Preferovaný jazyk můžete změnit v počítači pro správu, centru pro správu systému Windows, Microsoft Edge a Azure Stack v operačním systému HCI na serverech, které spravujete. Centrum pro správu Windows zůstává ve vašem jazyce podle vlastního výběru bez ohledu na jazykové změny v těchto dalších prostředcích.

Tento článek vysvětluje, jak změnit jazyky v nástroji:
- Windows 10
- Centrum pro správu systému Windows
- Microsoft Edge
- Jádro serveru v Azure Stack operační systém HCI

## <a name="change-the-language-in-the-management-pc"></a>Změna jazyka v počítači pro správu
Pokud na počítači pro správu používáte Windows 10, můžete nainstalovat jazykovou sadu pro použití v systému Windows 10, používané aplikace a navštívených webech. Můžete také změnit jazyk klávesnice a nastavit vstupní jazyk v pořadí podle priority pro weby a aplikace.

Změna jazyka používaného v systému Windows 10 nebo jazyka klávesnice nemá vliv na jazyk zobrazení v centru pro správu systému Windows.

   >[!IMPORTANT]
   > Po změně jazyka ve Windows 10 doporučujeme nastavit preferovaný jazyk klávesnice jako výchozí před odhlášením, aby nedocházelo k problémům s vstupem klávesnice.

Další informace najdete v tématu [Správa nastavení vstupního a zobrazovaného jazyka ve Windows 10](https://support.microsoft.com/help/4496404/windows-10-manage-the-input-and-display-language).

## <a name="change-the-language-in-windows-admin-center"></a>Změna jazyka v centru pro správu Windows
Jazyk v centru pro správu Windows a podle potřeby můžete změnit podle svého umístění. Změna některé z těchto možností v centru pro správu Windows nemá žádný vliv na nastavení jazyka počítače pro správu s Windows 10.

Změna jazyka v centru pro správu Windows:
1. V podokně **všechna připojení** vyberte ikonu ozubeného kola **Nastavení** a potom v části **Nastavení**vyberte **jazyk/oblast**.
1. Rozbalením rozevíracího seznamu **jazyk** vyberte preferovaný jazyk a potom v rozevíracím seznamu **místní formát** vyberte v případě potřeby jinou oblast.
1. Vyberte **Uložit a znovu načíst**.

    :::image type="content" source="media/languages/language-region.png" alt-text="Stránka jazyk/oblast v centru pro správu Windows":::

Další informace najdete v tématu [Nastavení centra pro správu systému Windows](/windows-server/manage/windows-admin-center/configure/settings).

## <a name="change-the-language-in-microsoft-edge"></a>Změna jazyka v Microsoft Edge
Můžete přidat podporované jazyky do Microsoft Edge a změnit pořadí jazykových předvoleb v prohlížeči. Můžete také přidat rozšíření překladatele pro cizí jazyky do prohlížeče, abyste získali překlady.

Další informace najdete v tématu [Podpora jazyka Microsoft Edge](/deployedge/microsoft-edge-supported-languages).

## <a name="change-the-language-in-server-core"></a>Změna jazyka v jádru serveru
Pokud potřebujete změnit jazyk v jádru serveru operačního systému Azure Stack HCI, doporučujeme to udělat po clusteringu serverů. Do jádra serveru můžete přidat podporované jazykové sady a potom změnit jazyk a rozložení klávesnice na ten, který chcete použít. Můžete také použít příkaz prostředí Windows PowerShell k přepsání aktuálního jazyka a vstupní metody klávesnice.

Jednotlivé jazykové sady jsou nainstalovány v adresáři *%SystemRoot%\System32 \\ % Language-ID%*. Například *C:\Windows\System32\es-ES* je umístění španělské jazykové sady. Každá jazyková sada má přibližně 50 MB. Pokud chcete nainstalovat všechny jazykové sady 38, je velikost požadované image, kterou vytvoříte, přibližně 2 GB.

Další informace najdete v tématu [dostupné jazyky pro Windows](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

Chcete-li ručně získat a přidat jazykové sady do operačního systému:

1. Pomocí nástroje **DISM/Add-WindowsPackage** přidejte do jádra serveru jazykovou sadu. `Add-WindowsPackage`Příkaz prostředí PowerShell je ekvivalentem spustitelného souboru DISM.

    Další informace najdete v tématu [Přidání jazyků do imagí Windows](/windows-hardware/manufacture/desktop/add-language-packs-to-windows).

1. Pro umožnění dalších funkcí jazyka, který jste přidali, doporučujeme přidat jazykové funkce na vyžádání (FODs), jak je uvedeno v následujícím příkladu:

    ```DOS
    dism /online /add-capability /capabilityname:Language.Basic~~~de-de~0.0.1.0
    ```

    Další informace najdete v tématu [funkce jazyků a oblastí na vyžádání (francouzské verze)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod).

1. K změně jazyka uživatelského rozhraní systému Windows v operačním systému aktuálního uživatelského účtu můžete použít rutinu PowerShellu **set-WinUILanguageOverride** . V následujícím příkladu `de-DE` určuje jako aktuální nastavení jazyka v operačním systému možnost Němčina:

    ```PowerShell
    Set-WinUILanguageOverride de-DE
    ```

    Další informace najdete v tématu [set-WinUILanguageOverride](/powershell/module/international/set-winuilanguageoverride?view=win10-ps).

## <a name="next-steps"></a>Další kroky
Další informace najdete v tématu také:

- [Přidání nebo odebrání serverů pro Azure Stack clusteru HCI](./add-cluster.md)
