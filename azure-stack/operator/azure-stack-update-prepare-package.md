---
title: Příprava balíčku aktualizace v centru Azure Stack
description: Naučte se připravit balíček aktualizace v centru Azure Stack.
author: sethmanheim
ms.topic: how-to
ms.date: 03/08/2021
ms.author: sethm
ms.lastreviewed: 03/10/2021
ms.reviewer: sranthar
ms.openlocfilehash: cdc0968519dc5ac50153fb5a14faaafd3b048843
ms.sourcegitcommit: e432e7f0a790bd6419987cbb5c5f3811e2e7a4a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102515776"
---
# <a name="prepare-an-azure-stack-hub-update-package"></a>Příprava balíčku aktualizace centra Azure Stack

Tento článek poskytuje přehled o přípravě balíčků aktualizací centra Azure Stack, aby se mohly použít k aktualizaci prostředí Azure Stack hub. Tento proces se skládá z následujících kroků:

- [Stáhněte balíček aktualizace](#download-the-update-package).
- [Importujte balíček aktualizace do prostředí Azure Stack hub pomocí portálu Azure Stack správce centra](#import-and-install-updates).

V systémech, které se můžou připojit k koncovým bodům automatických aktualizací, se automaticky stahují a připravují aktualizace softwaru centra Azure Stack a opravy hotfix. V systémech bez připojení a v případě jakékoli aktualizace od výrobce OEM (Original Equipment Manufacturer) musí být balíček aktualizace připravený, jak je vysvětleno v tomto článku.  

Následující tabulka ukazuje, kdy balíčky aktualizací vyžadují ruční přípravu a když se automaticky připravují.

| Typ aktualizace | Připojení | Požadována akce |
| --- | --- | --- |
| Aktualizace softwaru centra Azure Stack | Připojeno | Aktualizace je automaticky stažena a připravena při použití aktualizace. |
| Azure Stack opravy hotfix centra | Připojeno | Aktualizace je automaticky stažena a připravena při použití aktualizace. |
| Aktualizace balíčků OEM | Připojeno | Balíček aktualizace musí být připravený. Postupujte podle kroků v tomto článku. |
| Aktualizace softwaru centra Azure Stack | Odpojené nebo slabé připojení | Balíček aktualizace musí být připravený. Postupujte podle kroků v tomto článku. |
| Azure Stack opravy hotfix centra | Odpojené nebo slabé připojení | Balíček aktualizace musí být připravený. Postupujte podle kroků v tomto článku. |
| Aktualizace balíčků OEM | Odpojené nebo slabé připojení | Balíček aktualizace musí být připravený. Postupujte podle kroků v tomto článku. |

## <a name="download-the-update-package"></a>Stáhnout balíček aktualizací

Balíček aktualizace pro Azure Stack aktualizace centra a opravy hotfix jsou k dispozici pro připojené systémy přes okno aktualizace na portálu. Pokud aktualizujete balíček OEM nebo Pokud podporujete odpojený systém, Stáhněte balíček a přesuňte balíček do umístění, které je dostupné pro vaši instanci centra Azure Stack. Pokud používáte systém se přerušovaným připojením, možná budete muset stáhnout balíček a pak ho nahrát do přístupného umístění.

Zkontrolujte obsah balíčku. Balíček aktualizace se obvykle skládá z následujících souborů:

- **Samorozbalovací \<PackageName> soubor. zip**. Tento soubor obsahuje datovou část pro aktualizaci.
- **Soubor Metadata.xml**. Tento soubor obsahuje základní informace o aktualizaci. například adresa URL vydavatele, název, požadavek, velikost a podpora.

### <a name="automatic-download-and-preparation-for-update-packages"></a>Automatické stahování aktualizací a příprava na aktualizace

Aktualizace softwaru centra Azure Stack a opravy hotfix se automaticky připravují pro systémy s připojením ke **koncovým bodům automatických aktualizací centra Azure Stack**: `https://*.azureedge.net` a `https://aka.ms/azurestackautomaticupdate` . Další informace o nastavení připojení k **koncovým bodům automatických aktualizací centra Azure Stack** najdete v tématu **opravy a aktualizace** koncových bodů popsaných v tématu [Integrace brány firewall centra Azure Stack](./azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

### <a name="where-to-download-azure-stack-hub-update-packages"></a>Kde stahovat balíčky aktualizací Azure Stack centra

Aktualizace centra Azure Stack pro [úplné a expresní aktualizace](./azure-stack-updates.md#update-package-types) se hostují na zabezpečeném koncovém bodu Azure. Když budou aktualizace k dispozici, Azure Stack operátoři rozbočovače s připojenými instancemi uvidí [automaticky aktualizace centra Azure Stack se zobrazí na portálu pro správu](#automatic-download-and-preparation-for-update-packages). V případě odpojených systémů nebo systémů se slabým připojením k Internetu je možné balíčky aktualizací stáhnout pomocí [nástroje Azure Stack hub pro stažení aktualizací](https://aka.ms/azurestackupdatedownload). Balíčky aktualizací softwaru centra Azure Stack můžou obsahovat aktualizace pro Azure Stack služby centra a aktualizace operačního systému jednotek škálování centra Azure Stack.

>[!NOTE]
>Samotný balíček aktualizace a jeho obsah (například binární soubory, skripty prostředí PowerShell atd.) se podepisují pomocí certifikátů vlastněných společností Microsoft. Manipulace s balíčkem způsobí, že signatura není platná.

### <a name="where-to-download-azure-stack-hub-hotfix-packages"></a>Kde stahovat balíčky oprav hotfix centra Azure Stack

Balíčky pro [opravy hotfix centra Azure Stack](./azure-stack-updates.md#update-package-types) se hostují v rámci stejného zabezpečeného koncového bodu Azure jako aktualizace centra Azure Stack. Operátory centra Azure Stack s připojenými instancemi uvidí [aktualizace centra Azure Stack se automaticky zobrazí na portálu pro správu](#automatic-download-and-preparation-for-update-packages) , jakmile budou k dispozici. Můžete si je stáhnout pomocí integrovaných odkazů v každé z příslušných článků s opravou hotfix KB. Odkazy na články s opravou hotfix KB najdete také v poznámkách k verzi odpovídající vaší verzi centra Azure Stack.

### <a name="where-to-download-oem-update-packages"></a>Kam stáhnout balíčky aktualizací OEM

Dodavatel OEM může také vydávat aktualizace, jako jsou ovladače a aktualizace firmwaru. I když dodavatel hardwaru doručí tyto aktualizace jako samostatné [aktualizace balíčku OEM](./azure-stack-updates.md#update-package-types) , naimportují se, nainstalují a spravují stejným způsobem jako balíčky aktualizací od Microsoftu. V článku najdete seznam kontaktních odkazů dodavatele v tématu [Azure Stack aktualizace od výrobce OEM](./azure-stack-update-oem.md#oem-contact-information).

## <a name="import-and-install-updates"></a>Import a instalace aktualizací

Následující postup ukazuje, jak importovat a instalovat balíčky aktualizací na portálu pro správu.

> [!IMPORTANT]  
> Upozorněte uživatele na jakékoli operace údržby a nezapomeňte naplánovat normální časová období údržby v době, kdy je to možné, co nejvíc nepracovních hodin. Operace údržby mohou ovlivnit úlohy uživatelů i operace portálu.

1. Na portálu pro správu vyberte **všechny služby**. Pak v části kategorie **úložiště** vyberte **účty úložiště**. Nebo do pole Filtr začněte psát **účty úložiště** a pak ho vyberte.

    [![Aktualizace centra Azure Stack](./media/azure-stack-update-prepare-package/select-storage-small.png)](./media/azure-stack-update-prepare-package/select-storage.png#lightbox)

2. Do pole Filtr zadejte Update ( **aktualizovat**) a vyberte účet úložiště **updateadminaccount** .

3. Ve **všech službách** v části **Essentials** nebo **BLOB Service** vyberte **kontejnery**.

    [![Aktualizace centra Azure Stack – BLOB](./media/azure-stack-update-prepare-package/select-containers-small.png)](./media/azure-stack-update-prepare-package/select-containers.png#lightbox)

4. V **kontejnerech** vyberte **+ kontejner** a vytvořte kontejner. Zadejte název (například **Update-2102**) a pak vyberte **vytvořit**.

    [![Aktualizace centra Azure Stack – kontejner](./media/azure-stack-update-prepare-package/new-container-small.png)](./media/azure-stack-update-prepare-package/new-container.png#lightbox)

5. Po vytvoření kontejneru vyberte jeho název a potom vyberte **nahrát** a nahrajte soubory balíčku do kontejneru.

    [![Aktualizace centra Azure Stack – nahrání](./media/azure-stack-update-prepare-package/upload-package-small.png)](./media/azure-stack-update-prepare-package/upload-package.png#lightbox)

6. V části **nahrát objekt BLOB** vyberte ikonu složky, vyhledejte soubor. zip balíčku aktualizace a potom v okně Průzkumníka souborů vyberte **otevřít** .

7. V části **nahrát objekt BLOB** vyberte **nahrát**.

    ![Aktualizace centra Azure Stack – nahrání objektu BLOB](./media/azure-stack-update-prepare-package/upload-blob.png)

8. Opakujte kroky 6 a 7 pro soubor **Metadata.xml** a další soubory. zip v balíčku aktualizace. Neimportujte **doplňkový Notice.txt** soubor, pokud je zahrnutý.

9. Až budete hotovi, můžete si prohlédnout oznámení (vyberte ikonu zvonku v pravém horním rohu portálu). Oznámení by mělo značit, že nahrání bylo dokončeno.

10. Vraťte se na řídicí panel na okno **aktualizace** . V okně by se mělo zobrazit, že je k dispozici aktualizace. To znamená, že aktualizace byla úspěšně připravena. Výběrem okna si můžete prohlédnout nově přidaný balíček aktualizace.

11. Pokud chcete nainstalovat aktualizaci, vyberte balíček označený jako **připravený** a pak vyberte **aktualizovat hned**.

12. Když vyberete instalaci balíčku aktualizace, můžete zobrazit stav v oblasti **aktualizace podrobností o spuštění** . Odsud můžete také vybrat **Stáhnout souhrn** a stáhnout soubory protokolu. Protokoly z aktualizačních běhů jsou k dispozici po dobu šesti měsíců od ukončení pokusu.

13. Po dokončení aktualizace se zobrazí okno **aktualizace** s aktualizovanou verzí centra Azure Stack.

Aktualizace můžete ručně odstranit z účtu úložiště po jejich instalaci do centra Azure Stack. Centrum Azure Stack pravidelně kontroluje starší balíčky aktualizací a odebírá je ze služby Storage. Odebrání starých balíčků může trvat Azure Stack až dvou týdnů.

## <a name="next-steps"></a>Další kroky

[Použít aktualizaci](azure-stack-apply-updates.md)
