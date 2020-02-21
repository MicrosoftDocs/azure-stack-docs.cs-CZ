---
title: Příprava balíčku aktualizace centra Azure Stack
description: Naučte se připravit balíček aktualizace centra Azure Stack.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 1/22/2020
ms.author: inhenkel
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 5532345fe383b19bb96ea2f240634e15004a22b4
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509020"
---
# <a name="prepare-an-azure-stack-hub-update-package"></a>Příprava balíčku aktualizace centra Azure Stack

Tento článek poskytuje přehled o přípravě balíčků aktualizací centra Azure Stack, aby je bylo možné použít k aktualizaci prostředí centra Azure Stack. Tento proces se skládá z těchto součástí:

- [Stahuje se balíček aktualizace.](#download-the-update-package)
- [Import balíčku aktualizací do prostředí Azure Stack hub prostřednictvím portálu Azure Stack správce centra](#import-and-install-updates)

V systémech, které se můžou připojit k koncovým bodům automatických aktualizací, se automaticky stahují a připravují aktualizace softwaru centra Azure Stack a opravy hotfix. V systémech bez připojení a pro jakékoli aktualizace od výrobce OEM musí být balíček aktualizace připravený, jak je vysvětleno v tomto tématu.  

Následující tabulka ukazuje, kdy balíčky aktualizací vyžadují ruční přípravu a kdy se připravují automaticky.

| Typ aktualizace | Připojení | Požadována akce |
| --- | --- | --- |
| Aktualizace softwaru centra Azure Stack | Připojeno | Aktualizace je automaticky stažena a připravena při použití aktualizace. |
| Azure Stack opravy hotfix centra | Připojeno | Aktualizace je automaticky stažena a připravena při použití aktualizace. |
| Aktualizace balíčků OEM | Připojeno | Balíček aktualizace musí být připravený. Postupujte podle kroků v tomto článku. |
| Aktualizace softwaru centra Azure Stack | Odpojené nebo slabé připojení | Balíček aktualizace musí být připravený. Postupujte podle kroků v tomto článku. |
| Azure Stack opravy hotfix centra | Odpojené nebo slabé připojení | Balíček aktualizace musí být připravený. Postupujte podle kroků v tomto článku. |
| Aktualizace balíčků OEM | Odpojené nebo slabé připojení | Balíček aktualizace musí být připravený. Postupujte podle kroků v tomto článku. |

## <a name="download-the-update-package"></a>Stáhnout balíček aktualizací
Balíček aktualizace pro Azure Stack aktualizace centra a opravy hotfix jsou k dispozici v okně aktualizace pro připojené systémy. Pokud aktualizujete balíček OEM nebo Pokud podporujete odpojený systém, budete muset balíček stáhnout a přesunout do umístění, které je dostupné pro vaši instanci centra Azure Stack. Pokud používáte systém se přerušovaným připojením, možná budete muset stáhnout balíček a pak ho nahrát do přístupného umístění.

Zkontrolujte obsah balíčku. Balíček aktualizace se obvykle skládá z následujících souborů:

-   **Samorozbalovací soubor \<>. zip**. Tento soubor obsahuje datovou část pro aktualizaci.
- **Soubor metadata. XML**. Tento soubor obsahuje základní informace o aktualizaci, například o vydavateli, názvu, požadavku, velikosti a adrese URL cesty pro podporu.

### <a name="automatic-download-and-preparation-for-update-packages"></a>Automatické stažení a příprava pro balíčky aktualizací
Aktualizace softwaru centra Azure Stack a opravy hotfix se automaticky připravují pro systémy s připojením k **koncovým bodům automatických aktualizací centra Azure Stack**: https://*. azureedge. net a https://aka.ms/azurestackautomaticupdate. Další informace o nastavení připojení k **koncovým bodům automatických aktualizací centra Azure Stack**najdete v tématu **opravy a aktualizace** koncových bodů popsaných v tématu [Integrace brány firewall centra Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-integrate-endpoints#ports-and-urls-outbound) .

### <a name="where-to-download-azure-stack-hub-update-packages"></a>Kde stahovat balíčky aktualizací Azure Stack centra

Aktualizace centra Azure Stack pro [úplné a expresní aktualizace](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) se hostují na zabezpečeném koncovém bodu Azure. Operátory centra Azure Stack s připojenými instancemi uvidí [aktualizace centra Azure Stack se automaticky zobrazí na portálu pro správu](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages). V případě systémů odpojených přes Internet nebo systémů s slabým připojením k Internetu je možné balíčky aktualizací stáhnout pomocí [nástroje Azure Stack Center pro stažení aktualizací](https://aka.ms/azurestackupdatedownload). Balíčky aktualizací softwaru Azure Stack hub můžou obsahovat aktualizace pro služby centra Azure Stack a aktualizace operačního systému jednotek škálování centra Azure Stack.

>[!NOTE]
>Samotný balíček aktualizace a jeho obsah (například binární soubory, skripty prostředí PowerShell atd.) se podepisují pomocí certifikátů vlastněných společností Microsoft. Manipulace s balíčkem způsobí, že signatura není platná.


### <a name="where-to-download-azure-stack-hub-hotfix-packages"></a>Kde stahovat balíčky oprav hotfix centra Azure Stack

Balíček pro [opravy hotfix centra Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) se hostují ve stejném zabezpečeném koncovém bodu Azure jako pro Azure Stack aktualizace centra. Operátory centra Azure Stack s připojenými instancemi uvidí [aktualizace centra Azure Stack se automaticky zobrazí na portálu pro správu](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-prepare-package#automatic-download-and-preparation-for-update-packages). Můžete si je stáhnout pomocí integrovaných odkazů v každé z příslušných článků s opravou hotfix KB, například [Azure Stack 1.1906.11.52 centra hotfix](https://support.microsoft.com/help/4515650). Opravy hotfix najdete v poznámkách k verzi odpovídající vaší verzi centra Azure Stack.

### <a name="where-to-download-oem-update-packages"></a>Kam stáhnout balíčky aktualizací OEM
Dodavatel OEM bude také vydávat aktualizace, například ovladače a aktualizace firmwaru. I když dodavatel hardwaru doručí tyto aktualizace jako samostatné [aktualizace balíčků OEM](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates#update-package-types) , jsou pořád naimportované, nainstalované a spravované stejným způsobem jako balíčky aktualizací od Microsoftu. Seznam kontaktních odkazů dodavatele najdete v části [použití aktualizací pro výrobce OEM (Original Equipment Manufacturer) Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-update-oem#oem-contact-information).

## <a name="import-and-install-updates"></a>Import a instalace aktualizací

Následující postup ukazuje, jak importovat a instalovat balíčky aktualizací na portálu pro správu.

> [!Important]  
> Upozorněte uživatele na jakékoli operace údržby a naplánujte normální časová období údržby v době, kdy je to možné, co nejvíc nepracovních hodin. Operace údržby mohou ovlivnit úlohy uživatelů i operace portálu.

1.  Na portálu pro správu vyberte **všechny služby**. Pak v části kategorie **úložiště dat + úložiště** vyberte **účty úložiště**. (Nebo do pole Filtr začněte psát **účty úložiště**a vyberte ho.)

    ![Aktualizace centra Azure Stack](./media/azure-stack-update-prepare-package/image1.png) 

1.  Do pole Filtr zadejte Update ( **aktualizovat**) a vyberte účet úložiště **updateadminaccount** .

2.  V podrobnostech účtu úložiště v části **služby**vyberte **objekty blob**.

    ![Aktualizace centra Azure Stack – BLOB](./media/azure-stack-update-prepare-package/image2.png)

1.  V části **BLOB Service**vyberte **+ kontejner** a vytvořte kontejner. Zadejte název (například *Update-1811*) a pak vyberte **OK**.

    ![Aktualizace centra Azure Stack – kontejner](./media/azure-stack-update-prepare-package/image3.png)

1.  Po vytvoření kontejneru klikněte na název kontejneru a potom klikněte na tlačítko **nahrát** a nahrajte soubory balíčku do kontejneru.

    ![Aktualizace centra Azure Stack – nahrání](./media/azure-stack-update-prepare-package/image4.png)

1.  V části **nahrát objekt BLOB**klikněte na ikonu složky, vyhledejte soubor. zip balíčku aktualizace a potom v okně Průzkumníka souborů klikněte na **otevřít** .

2.  V části **nahrát objekt BLOB**klikněte na **nahrát**.

    ![Aktualizace centra Azure Stack – nahrání objektu BLOB](./media/azure-stack-update-prepare-package/image5.png)

1.  Opakujte kroky 6 a 7 pro soubor metadata. XML a všechny další soubory. zip v balíčku aktualizace. Neimportujte soubor doplňkového oznámení. txt, pokud je zahrnutý.

2.  Až budete hotovi, můžete si prohlédnout oznámení (ikona zvonku v pravém horním rohu portálu). Oznámení by měla značit, že nahrání bylo dokončeno.

3.  Přejděte zpět do okna aktualizace na řídicím panelu. Okno by mělo značit, že je k dispozici aktualizace. To znamená, že aktualizace byla úspěšně připravena. Kliknutím na okno si můžete prohlédnout nově přidaný balíček aktualizace.

4.  Pokud chcete nainstalovat aktualizaci, vyberte balíček označený jako **připravený** a buď klikněte pravým tlačítkem na balíček a vyberte **aktualizovat hned**, nebo klikněte na akci **aktualizovat hned** v horní části.

5.  Po kliknutí na instalaci balíčku aktualizace můžete zobrazit stav v oblasti **aktualizace podrobností o spuštění** . Tady můžete také kliknout na **Stáhnout souhrn** a stáhnout soubory protokolu. Protokoly z aktualizačních běhů jsou k dispozici po dobu 6 měsíců od ukončení pokusu.

6.  Po dokončení aktualizace se zobrazí okno aktualizace s aktualizovanou verzí centra Azure Stack.

Aktualizace můžete ručně odstranit z účtu úložiště, až se nainstalují do centra Azure Stack. Centrum Azure Stack pravidelně kontroluje starší balíčky aktualizací a odebírá je ze služby Storage. Odebrání starých balíčků může trvat Azure Stack centra za dva týdny.

## <a name="next-steps"></a>Další kroky

[Použít aktualizaci](azure-stack-apply-updates.md)
