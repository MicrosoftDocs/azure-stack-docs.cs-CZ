---
title: Použití aktualizací ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak importovat a nainstalovat balíčky aktualizací společnosti Microsoft pro systém Azure Stack integrované.
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
ms.openlocfilehash: 124dc554e18f6d387a3e41d37809d43276094879
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490128"
---
# <a name="apply-updates-in-azure-stack"></a>Použití aktualizací ve službě Azure Stack

*Platí pro: Integrované systémy Azure Stack*

Můžete použít **aktualizovat** dlaždici v portálu pro správu použít Microsoft nebo výrobce OEM balíčky aktualizací pro Azure Stack.

Pokud používáte verzi integrovaných systémech 1807 nebo starší, musíte stáhnout balíček aktualizace, importovat soubory balíčku do služby Azure Stack a potom instalaci balíčku aktualizace. Pokyny najdete v tématu [aktualizace Azure Stack, stáhněte si balíček](#update-azure-stack-by-downloading-the-package)

Upgradujte tyto pokyny práce s integrované systémy Azure Stack. Pokud používáte systém Azure Stack Development, musíte stáhnout instalační balíček pro aktuální verzi. Pokyny najdete v tématu [instalace sady Azure Stack Development Kit](../asdk/asdk-install.md)

## <a name="update-azure-stack"></a>Aktualizace služby Azure Stack

### <a name="select-and-apply-an-update-package"></a>Výběr a použití balíčku aktualizace

1. Otevřete na portálu pro správu.

2. Vyberte **řídicí panel**. Vyberte **aktualizace** dlaždici.

    ![Azure je dostupná aktualizace zásobníku](media/azure-stack-apply-updates/azure-stack-updates-1901-dashboard.png)

3. Poznamenejte si aktuální verzi služby Azure Stack. Můžete aktualizovat na novou plnou verzi. Například pokud používáte Azure Stack 1811 další vydané verze je 1901.

    ![Použít aktualizace služby Azure Stack](media/azure-stack-apply-updates/azure-stack-updates-1901-updateavailable.png)

4. V seznamu aktualizací vyberte následující dostupnou verzi. Můžete vybrat **zobrazení** ve vydané verzi sloupec Poznámky k otevření tématu poznámky k vydání verze pro, pokud chcete zkontrolovat změny verze.

5. Vyberte aktualizaci. Aktualizace se spustí.

### <a name="review-update-history"></a>Kontrola historie aktualizace

1. Otevřete na portálu pro správu.

2. Vyberte **řídicí panel**. Vyberte **aktualizace** dlaždici.

3. Vyberte **aktualizace historie**.

![Historie aktualizace služby Azure Stack](media/azure-stack-apply-updates/azure-stack-update-history.PNG)

## <a name="update-azure-stack-by-downloading-the-package"></a>Stažení balíčku aktualizace služby Azure Stack

Pokud používáte verzi integrovaných systémech 1807 nebo starší, musíte stáhnout balíček aktualizace, importovat soubory balíčku do služby Azure Stack a potom instalaci balíčku aktualizace.

## <a name="download-the-update-package"></a>Stáhněte si balíček aktualizací

Pokud společnosti Microsoft nebo výrobce OEM balíčku aktualizace pro Azure Stack je k dispozici, stáhněte si balíček do umístění, který je dosažitelný z Azure Stack a zkontrolovat obsah balíčku. Balíček aktualizací se obvykle skládá z následujících souborů:

- Samoobslužné extrahování `<PackageName>.exe` souboru. Tento soubor obsahuje datovou část aktualizace, například nejnovější kumulativní aktualizaci pro Windows Server.

- Odpovídající `<PackageName>.bin` soubory. Tyto soubory poskytují kompresi pro datovou část, který je přidružen *název_balíčku*soubor .exe.

- A `Metadata.xml` souboru. Tento soubor obsahuje základní informace o aktualizaci, třeba vydavatele, název, požadovaných součástí, velikost a adresa URL podpory cestu.

> [!IMPORTANT]  
> Po použití balíčku aktualizace Azure Stack 1901 formát balení pro balíčky aktualizací pro Azure Stack přejde od .exe, .bin(s) a formátu XML .zip(s) a formátu XML. Operátoři Azure stacku, které se připojily razítka nebude mít vliv. Operátoři Azure stacku, které jsou odpojené jednoduše naimportuje soubory XML a .zip s použitím stejného procesu popsané níže.

## <a name="import-and-install-updates"></a>Import a aktualizace

Následující postup ukazuje, jak importovat a nainstalovat balíčky aktualizací na portálu pro správce.

> [!IMPORTANT]  
> Důrazně doporučujeme, upozornění uživatelů na jakékoli operace údržby a naplánovat normální správu a údržbu během mimo pracovní dobu co největší míře. Operace údržby může ovlivnit uživatelské úlohy a operací na portálu.

1. Na portálu správce, vyberte **všechny služby**. Potom v části **DATA + úložiště** vyberte **účty úložiště**. (Nebo do pole Filtr začněte zadávat text **účty úložiště**a vyberte ji.)

    ![Ukazuje, kde najdou na portálu účtů úložiště](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. V dialogovém okně Filtr zadejte **aktualizovat**a vyberte **updateadminaccount** účtu úložiště.

3. Ve službě storage account podrobnosti najdete v části **služby**vyberte **objekty BLOB**.
 
    ![Ukazuje, jak získat k objektům blob pro účet úložiště](media/azure-stack-apply-updates/ApplyUpdates3.png) 

4. V části **službu Blob service**vyberte **+ kontejner** k vytvoření kontejneru. Zadejte název (například *aktualizace 1811*) a pak vyberte **OK**.
 
     ![Ukazuje, jak přidat kontejner v účtu úložiště](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. Po vytvoření kontejneru, klikněte na název kontejneru a pak klikněte na tlačítko **nahrát** nahrát soubory balíčku do tohoto kontejneru.
 
    ![Ukazuje, jak nahrát soubory balíčku](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. V části **nahrát objekt blob**, klikněte na ikonu složky, vyhledejte soubor .exe balíček aktualizace a pak klikněte na tlačítko **otevřít** v okně Průzkumníka souborů.
  
7. V části **nahrát objekt blob**, klikněte na tlačítko **nahrát**.
  
    ![Ukazuje, kde kvůli nahrání každého souboru balíčku](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Zopakujte kroky 6 a 7 pro *název_balíčku*soubor .bin a soubory Metadata.xml. Pokud zahrnutý import souboru doplňkové Notice.txt.
9. Až budete hotovi, můžete zkontrolovat oznámení (ikona zvonku v pravém horním rohu portálu). Oznámení měl označovat, že se nahrávání dokončí.
10. Přejděte zpět na Aktualizovat dlaždici na řídicím panelu. Na dlaždici měl označovat, že je k dispozici aktualizace. Kliknutím na dlaždici ke kontrole nově přidané aktualizace balíčku.
11. K instalaci aktualizace, vyberte balíček, který je označen jako **připravené** a buď klikněte pravým tlačítkem na balíček a vyberte **aktualizovat**, nebo klikněte na tlačítko **aktualizovat** akce nahoře .
12. Po kliknutí na instalaci balíčku aktualizace, můžete zobrazit stav v **podrobnosti Hromadná postupná aktualizace** oblasti. Z tohoto místa můžete také kliknout na **stáhnout úplných protokolů** ke stažení souborů protokolu.
13. Po dokončení aktualizace, aktualizace dlaždice zobrazí aktualizovaná verze Azure Stack.

Aktualizace můžete ručně odstranit z účtu úložiště po dokončení instalace ve službě Azure Stack. Azure Stack pravidelně kontroluje starší balíčky aktualizací a odebere je ze služby storage. Azure Stack může trvat dva týdny, chcete-li odebrat starý balíčky.

## <a name="next-steps"></a>Další postup

- [Správa aktualizací v přehledu služby Azure Stack](azure-stack-updates.md)
- [Zásady údržby služby Azure Stack](azure-stack-servicing-policy.md)
