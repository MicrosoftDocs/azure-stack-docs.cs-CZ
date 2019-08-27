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
ms.openlocfilehash: ab7b764e608ed1fb8008071296d0004f6ef65e7a
ms.sourcegitcommit: 1c45814696e70ba987dd39ce61d93ea4ef5222ea
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70029478"
---
# <a name="prepare-an-azure-stack-update-package"></a>Příprava balíčku aktualizace Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Tento článek poskytuje přehled o přípravě balíčků aktualizací Azure Stack, aby je bylo možné použít k aktualizaci Azure Stack prostředí. Tento proces se skládá z těchto součástí:

- [Stahuje se balíček aktualizace.](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#download-the-update-package)
- [Import balíčku aktualizací do prostředí Azure Stack pomocí portálu Azure Stack správce](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#import-and-install-updates)

Tento proces se provádí automaticky pro Azure Stack aktualizace softwaru a opravy hotfix v systémech s internetem connectivty do [koncových bodů Azure Stack automatické aktualizace](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages).

Následující tabulka zobrazuje, kdy balíčky aktualizací vyžadují ruční přípravu, a když se automaticky připravují:

| Typ aktualizace | Připojení prostředí Azure Stack k [Azure Stack koncovým bodům automatických aktualizací](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages) | Vyžaduje se akce |
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

### <a name="automatic-download-and-preparation-for-update-packages"></a>Automatické stažení a příprava pro balíčky aktualizací
Azure Stack aktualizace softwaru a opravy hotfix se automaticky připravují pro systémy s připojením k **Azure Stack koncovým bodům automatických aktualizací**: https://*. https://aka.ms/azurestackautomaticupdate azureedge. NET a. Další informace o nastavení připojení k **koncovým bodům Azure Stack automatické aktualizace**najdete v tématu o **opravách a aktualizacích** , které jsou uvedené v [Azure Stack Integrace brány firewall](https://docs.microsoft.com/azure-stack/operator/azure-stack-integrate-endpoints#ports-and-urls-outbound) .

### <a name="where-to-download-azure-stack-update-packages"></a>Kam stáhnout Azure Stack balíčky aktualizací

Azure Stack aktualizace pro [úplné a expresní aktualizace](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) se hostují na zabezpečeném koncovém bodu Azure. Operátory Azure Stack s připojenými instancemi uvidí, že [Azure Stack aktualizace se automaticky zobrazí na portálu pro správu](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages). V případě systémů odpojených přes Internet nebo systémů s slabým připojením k Internetu je možné balíčky aktualizací stáhnout pomocí nástroje pro stažení [aktualizací Azure Stack](https://aka.ms/azurestackupdatedownload). Azure Stack balíčky aktualizací softwaru mohou obsahovat aktualizace služeb Azure Stack a aktualizace operačního systému jednotek škálování vašeho Azure Stack.

### <a name="where-to-download-azure-stack-hotfix-packages"></a>Kde stáhnout Azure Stack balíčky oprav hotfix

Balíček pro [Azure Stack opravy hotfix](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) jsou hostovány ve stejném zabezpečeném koncovém bodu Azure jako pro Azure Stack aktualizace. Operátory Azure Stack s připojenými instancemi uvidí, že [Azure Stack aktualizace se automaticky zobrazí na portálu pro správu](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages). Můžete si je stáhnout pomocí vložených odkazů v každé z příslušných článků o opravě hotfix KB. například [Azure Stack hotfix 1.1906.11.52](https://support.microsoft.com/help/4515650). Opravy hotfix najdete v poznámkách k verzi odpovídající vaší Azure Stack verzi. Výrobce OEM – aktualizace poskytované dodavatelem

### <a name="where-to-download-oem-update-packages"></a>Kam stáhnout balíčky aktualizací OEM
Dodavatel OEM bude také vydávat aktualizace, například ovladače a aktualizace firmwaru. I když dodavatel hardwaru doručí tyto aktualizace jako samostatné [aktualizace balíčků OEM](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) , jsou pořád naimportované, nainstalované a spravované stejným způsobem jako balíčky aktualizací od Microsoftu. Seznam kontaktních odkazů dodavatele najdete na adrese [Azure Stack aktualizace OEM (Original Equipment Manufacturer)](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-oem#oem-contact-information).

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

## <a name="next-steps"></a>Další postup

[Použít aktualizaci](azure-stack-apply-updates.md)
