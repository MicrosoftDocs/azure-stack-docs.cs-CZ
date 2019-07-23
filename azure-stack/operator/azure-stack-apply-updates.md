---
title: Použít aktualizace v Azure Stack | Microsoft Docs
description: Naučte se importovat a instalovat balíčky Microsoft Update pro Azure Stack integrovaný systém.
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
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: justini
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: e5dd8078a17b6a10ac75eeee8d8a6eef77108606
ms.sourcegitcommit: 159da88a52701679571bbedde1c36b72bbfe32dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68380496"
---
# <a name="apply-updates-in-azure-stack"></a>Použít aktualizace v Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Pomocí dlaždice **aktualizace** na portálu pro správu můžete použít balíčky aktualizací Microsoft nebo OEM pro Azure Stack.

Pokud používáte integrované systémy verze 1807 nebo starší, je nutné stáhnout balíček aktualizace, importovat soubory balíčku do Azure Stack a následně nainstalovat balíček aktualizace. Pokyny najdete v tématu [aktualizace Azure Stack stažením balíčku](#update-azure-stack-by-downloading-the-package) .

Tyto pokyny k upgradu fungují s Azure Stack integrovanými systémy. Pokud používáte vývojový systém Azure Stack, je nutné stáhnout instalační balíček pro aktuální verzi. Pokyny najdete v tématu [instalace Azure Stack Development Kit](../asdk/asdk-install.md) .

## <a name="update-azure-stack"></a>Aktualizovat Azure Stack

### <a name="select-and-apply-an-update-package"></a>Výběr a použití balíčku aktualizace

1. Otevřete portál pro správu.

2. Vyberte **řídicí panel**. Vyberte dlaždici **aktualizace** .

    ![Je dostupná aktualizace Azure Stack.](media/azure-stack-apply-updates/azure-stack-updates-1901-dashboard.png)

3. Poznamenejte si aktuální verzi Azure Stack. Můžete aktualizovat na další plnou verzi. Pokud například používáte Azure Stack 1811, bude další vydaná verze 1901.

    ![Azure Stack použít aktualizaci](media/azure-stack-apply-updates/azure-stack-updates-1901-updateavailable.png)

4. V seznamu aktualizací vyberte další dostupnou verzi. Pokud chcete zkontrolovat změny verze, můžete vybrat **zobrazení** ve sloupci poznámky k verzi pro otevření tématu poznámky k verzi pro tuto verzi.

5. Vyberte aktualizovat hned. Aktualizace se spustí.

### <a name="review-update-history"></a>Zkontrolovat historii aktualizací

1. Otevřete portál pro správu.

2. Vyberte **řídicí panel**. Vyberte dlaždici **aktualizace** .

3. Vyberte **historii aktualizací**.

![Historie aktualizace Azure Stack](media/azure-stack-apply-updates/azure-stack-update-history.PNG)

## <a name="update-azure-stack-by-downloading-the-package"></a>Aktualizace Azure Stack stažením balíčku

Pokud používáte integrované systémy verze 1807 nebo starší, je nutné stáhnout balíček aktualizace, importovat soubory balíčku do Azure Stack a následně nainstalovat balíček aktualizace.

## <a name="download-the-update-package"></a>Stáhnout balíček aktualizací

Pokud je k dispozici balíček aktualizace Microsoft nebo OEM pro Azure Stack, Stáhněte balíček do umístění, které je dosažitelné z Azure Stack a zkontrolujte obsah balíčku. Balíček aktualizace se obvykle skládá z následujících souborů:

- Samorozbalovací `<PackageName>.zip` soubor. Tento soubor obsahuje datovou část pro aktualizaci, například nejnovější kumulativní aktualizaci pro Windows Server.

- Odpovídající `<PackageName>.bin` soubory. Tyto soubory poskytují kompresi pro datovou část, která je přidružená k souboru *Package*. zip.

- `Metadata.xml` Soubor. Tento soubor obsahuje základní informace o aktualizaci, jako je například Vydavatel, název, požadavek, velikost a adresa URL pro cestu k podpoře.

> [!IMPORTANT]  
> Po použití balíčku aktualizace Azure Stack 1901 se formát balíčku pro balíčky aktualizací Azure Stack přesune z formátu. zip,. bin a formátu. XML do formátu. zip a. XML. Neovlivní Azure Stack operátory s připojenými razítky. Nepřipojené operátory Azure Stack naimportují jednoduše soubory. XML a. zip pomocí stejného procesu popsaného níže.

## <a name="import-and-install-updates"></a>Import a instalace aktualizací

Následující postup ukazuje, jak importovat a instalovat balíčky aktualizací na portálu pro správu.

> [!IMPORTANT]  
> Důrazně doporučujeme, abyste uživatelům informovali o všech operacích údržby a naplánovali normální časová období údržby v době nepracovních hodin co nejvíce. Operace údržby mohou ovlivnit úlohy uživatelů i operace portálu.

1. Na portálu pro správu vyberte **všechny služby**. Pak v části kategorie **úložiště dat + úložiště** vyberte **účty úložiště**. (Nebo do pole Filtr začněte psát **účty úložiště**a vyberte ho.)

    ![Ukazuje, kde najít účty úložiště na portálu.](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. Do pole Filtr zadejte Update ( **aktualizovat**) a vyberte účet úložiště **updateadminaccount** .

3. V podrobnostech účtu úložiště v části **služby**vyberte **objekty blob**.
 
    ![Ukazuje, jak získat objekty blob pro účet úložiště.](media/azure-stack-apply-updates/ApplyUpdates3.png) 

4. V části **BLOB Service**vyberte **+ kontejner** a vytvořte kontejner. Zadejte název (například *Update-1811*) a pak vyberte **OK**.
 
     ![Ukazuje, jak přidat kontejner do účtu úložiště.](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. Po vytvoření kontejneru klikněte na název kontejneru a potom klikněte na tlačítko **nahrát** a nahrajte soubory balíčku do kontejneru.
 
    ![Ukazuje, jak nahrát soubory balíčku.](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. V části **nahrát objekt BLOB**klikněte na ikonu složky, vyhledejte soubor. zip balíčku aktualizace a potom v okně Průzkumníka souborů klikněte na **otevřít** .
  
7. V části **nahrát objekt BLOB**klikněte na **nahrát**.
  
    ![Zobrazuje, kam se mají nahrát jednotlivé soubory balíčku.](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Opakujte kroky 6 a 7 pro soubory *Package*. bin a metadata. XML. Neimportujte soubor doplňkového oznámení. txt, pokud je zahrnutý. Všimněte si, že soubory budou. zip od 1901 do. bin a. zip – i nadále importujte soubor. XML obvyklým způsobem.

9. Až budete hotovi, můžete si prohlédnout oznámení (ikona zvonku v pravém horním rohu portálu). Oznámení by měla značit, že nahrání bylo dokončeno.
10. Přejděte zpátky na dlaždici aktualizace na řídicím panelu. Dlaždice by měla označovat, že je k dispozici aktualizace. Kliknutím na dlaždici si můžete prohlédnout nově přidaný balíček aktualizace.
11. Pokud chcete nainstalovat aktualizaci, vyberte balíček označený jako **připravený** a buď klikněte pravým tlačítkem na balíček a vyberte **aktualizovat hned**, nebo klikněte na akci **aktualizovat hned** v horní části.
12. Po kliknutí na instalaci balíčku aktualizace můžete zobrazit stav v oblasti **aktualizace podrobností o spuštění** . Odtud můžete také kliknout na možnost **Stáhnout úplné protokoly** a stáhnout soubory protokolu. Protokoly z aktualizačních běhů jsou k dispozici po dobu 6 měsíců od ukončení pokusu. 
13. Po dokončení aktualizace se na dlaždici aktualizace zobrazí aktualizovaná verze Azure Stack.

Aktualizace můžete ručně odstranit z účtu úložiště poté, co byly nainstalovány v Azure Stack. Azure Stack pravidelně kontroluje starší balíčky aktualizací a odebírá je ze služby Storage. Odebrání starých balíčků může trvat Azure Stack dvou týdnů.

## <a name="next-steps"></a>Další kroky

- [Správa aktualizací v přehledu služby Azure Stack](azure-stack-updates.md)
- [Zásady údržby služby Azure Stack](azure-stack-servicing-policy.md)
