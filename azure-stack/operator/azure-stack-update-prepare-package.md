---
title: Příprava balíčku aktualizace Azure Stack | Microsoft Docs
description: Naučte se připravit balíček aktualizace Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2019
ms.author: mabrigg
ms.lastreviewed: 08/15/2019
ms.reviewer: ppacent
ms.openlocfilehash: 969aea2134f7980eb2b3a5b6e8d00a987c410744
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "70010228"
---
# <a name="prepare-an-azure-stack-update-package"></a>Příprava balíčku aktualizace Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Tento článek poskytuje přehled o přípravě balíčků aktualizací Azure Stack, aby je bylo možné použít k aktualizaci Azure Stack prostředí. Tento proces se provádí automaticky pro Azure Stack aktualizace softwaru a opravy hotfix v systémech připojených k Internetu. Pro všechny aktualizace balíčků OEM a Azure Stack aktualizace softwaru a opravy hotfix v systémech a systémech odpojených přes Internet, které mají slabý nebo přerušovaný Internet connectivty, se balíčky aktualizací připravují stažením balíčku aktualizace Azure Stack nebo aktualizace OEM. balíček a Import balíčku do úložiště tak, aby k němu měl k dispozici poskytovatel Azure Stack Update. Tabulka se zobrazí v případě, že by se měly provést kroky v tomto článku:

| Typ aktualizace | Připojení k Internetu | Action |
| --- | --- | --- |
| Azure Stack aktualizace softwaru | Připojeno | Aktualizace je automaticky stažena a připravena při použití aktualizace. |
| Azure Stack opravy hotfix | Připojeno | Aktualizace je automaticky stažena a připravena při použití aktualizace. |
| Aktualizace balíčků OEM | Připojeno | Balíček aktualizace musí být připravený. Postupujte podle kroků v tomto článku. |
| Azure Stack aktualizace softwaru | Odpojené nebo slabé připojení | Balíček aktualizace musí být připravený. Postupujte podle kroků v tomto článku. |
| Azure Stack opravy hotfix | Odpojené nebo slabé připojení | Balíček aktualizace musí být připravený. Postupujte podle kroků v tomto článku. |
| Aktualizace balíčků OEM | Odpojené nebo slabé připojení | Balíček aktualizace musí být připravený. Postupujte podle kroků v tomto článku. |

## <a name="download-the-update-package"></a>Stáhnout balíček aktualizací
Balíček aktualizace pro Azure Stack aktualizace a opravy hotfix jsou k dispozici v okně aktualizace pro připojené systémy. Pokud aktualizujete balíček OEM nebo Pokud podporujete odpojený systém, budete muset balíček stáhnout a přesunout do umístění, které je dostupné pro vaši instanci Azure Stack. Pokud používáte systém se přerušovaným připojením, možná budete muset stáhnout balíček a pak ho nahrát do přístupného umístění.

Zkontrolujte obsah balíčku. Balíček aktualizace se obvykle skládá z následujících souborů:

-   **Samorozbalovací\<soubor >. zip s balíčkem**. Tento soubor obsahuje datovou část pro aktualizaci.
- **Soubor metadata. XML**. Tento soubor obsahuje základní informace o aktualizaci, například o vydavateli, názvu, požadavku, velikosti a adrese URL cesty pro podporu.

## <a name="azure-stack-software-updates"></a>Azure Stack aktualizace softwaru

Azure Stack aktualizací softwaru hostuje zabezpečený koncový bod Azure. Operátory Azure Stack s připojenými instancemi uvidí, že Azure Stack aktualizace se automaticky zobrazí na portálu pro správu, kde je **dostupná aktualizace**zprávy. Aktualizace Azure Stack se při použití aktualizace automaticky stáhnou na systémy připojené k Internetu. V případě systémů odpojených přes Internet nebo systémů s slabým připojením k Internetu je možné balíčky aktualizací stáhnout pomocí nástroje pro stažení [aktualizací Azure Stack](https://aka.ms/azurestackupdatedownload). Azure Stack balíčky aktualizací softwaru mohou obsahovat aktualizace služeb Azure Stack a aktualizace operačního systému jednotek škálování vašeho Azure Stack.

### <a name="azure-stack-hotfixes"></a>Azure Stack opravy hotfix 

Balíčky aktualizací oprav hotfix jsou hostovány ve stejném zabezpečeném koncovém bodu Azure. U operátorů Azure Stack s připojenými instancemi se automaticky zobrazí tyto opravy hotfix v portálu pro správu s **dostupnou aktualizací**zpráv. Azure Stack opravy hotfix se při použití aktualizace automaticky stáhnou na systémy připojené k Internetu. Můžete si je stáhnout pomocí vložených odkazů v každé z příslušných článků o opravě hotfix KB. například [Azure Stack hotfix 1.1906.11.52](https://support.microsoft.com/help/4515650). Opravy hotfix najdete v poznámkách k verzi odpovídající vaší Azure Stack verzi. Výrobce OEM – aktualizace poskytované dodavatelem

Dodavatel OEM bude také vydávat aktualizace, například ovladače a aktualizace firmwaru. I když jsou tyto aktualizace dodávány jako samostatné balíčky podle dodavatele, některé jsou importovány, instalovány a spravovány stejným způsobem jako balíčky aktualizací od společnosti Microsoft. Seznam kontaktních odkazů dodavatele najdete na adrese [Azure Stack aktualizace OEM (Original Equipment Manufacturer)](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-oem#oem-contact-information).

## <a name="import-and-install-updates"></a>Import a instalace aktualizací

Následující postup ukazuje, jak importovat a instalovat balíčky aktualizací na portálu pro správu.

> [!Important]  
> Upozorněte uživatele na jakékoli operace údržby a naplánujte normální časová období údržby v době, kdy je to možné, co nejvíc nepracovních hodin. Operace údržby mohou ovlivnit úlohy uživatelů i operace portálu.

1.  Na portálu pro správu vyberte **všechny služby**. Pak v části kategorie **úložiště dat + úložiště** vyberte **účty úložiště**. (Nebo do pole Filtr začněte psát **účty úložiště**a vyberte ho.)

    ![Aktualizace Azure Stack](./media/azure-stack-update-prepare-package/image1.png) 

1.  Do pole Filtr zadejte Update ( **aktualizovat**) a vyberte účet úložiště **updateadminaccount** .

2.  V podrobnostech účtu úložiště v části **služby**vyberte **objekty blob**.

    ![Aktualizace Azure Stack – BLOB](./media/azure-stack-update-prepare-package/image2.png)

1.  V části **BLOB Service**vyberte **+ kontejner** a vytvořte kontejner. Zadejte název (například *Update-1811*) a pak vyberte **OK**.

    ![Azure Stack aktualizace – kontejner](./media/azure-stack-update-prepare-package/image3.png)

1.  Po vytvoření kontejneru klikněte na název kontejneru a potom klikněte na tlačítko **nahrát** a nahrajte soubory balíčku do kontejneru.

    ![Aktualizace Azure Stack – nahrávání](./media/azure-stack-update-prepare-package/image4.png)

1.  V části **nahrát objekt BLOB**klikněte na ikonu složky, vyhledejte soubor. zip balíčku aktualizace a potom v okně Průzkumníka souborů klikněte na **otevřít** .

2.  V části **nahrát objekt BLOB**klikněte na **nahrát**.

    ![Azure Stack aktualizace – nahrát objekt BLOB](./media/azure-stack-update-prepare-package/image5.png)

1.  Opakujte kroky 6 a 7 pro soubor metadata. XML a všechny další soubory. zip v balíčku aktualizace. Neimportujte soubor doplňkového oznámení. txt, pokud je zahrnutý.

2.  Až budete hotovi, můžete si prohlédnout oznámení (ikona zvonku v pravém horním rohu portálu). Oznámení by měla značit, že nahrání bylo dokončeno.

3.  Přejděte zpět do okna aktualizace na řídicím panelu. Okno by mělo značit, že je k dispozici aktualizace. To znamená, že aktualizace byla úspěšně připravena. Kliknutím na okno si můžete prohlédnout nově přidaný balíček aktualizace.

4.  Pokud chcete nainstalovat aktualizaci, vyberte balíček označený jako **připravený** a buď klikněte pravým tlačítkem na balíček a vyberte **aktualizovat hned**, nebo klikněte na akci **aktualizovat hned** v horní části.

5.  Po kliknutí na instalaci balíčku aktualizace můžete zobrazit stav v oblasti **aktualizace podrobností o spuštění** . Tady můžete také kliknout na **Stáhnout souhrn** a stáhnout soubory protokolu. Protokoly z aktualizačních běhů jsou k dispozici po dobu 6 měsíců od ukončení pokusu.

6.  Po dokončení aktualizace se v okně aktualizace zobrazí aktualizovaná verze Azure Stack.

Aktualizace můžete ručně odstranit z účtu úložiště poté, co byly nainstalovány v Azure Stack. Azure Stack pravidelně kontroluje starší balíčky aktualizací a odebírá je ze služby Storage. Odebrání starých balíčků může trvat Azure Stack dvou týdnů.

## <a name="next-steps"></a>Další kroky

[Použít aktualizaci](azure-stack-apply-updates.md)
