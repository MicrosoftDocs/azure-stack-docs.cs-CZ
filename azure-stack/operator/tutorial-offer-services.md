---
title: Vytvoření nabídky služeb pro uživatele
titleSuffix: Azure Stack Hub
description: Naučte se, jak vytvořit nabídku služeb v Azure Stackovém centru pomocí nabídek, plánů a služeb.
author: BryanLa
ms.author: bryanla
ms.topic: tutorial
ms.date: 10/16/2019
ms.reviewer: shriramnat
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 772871cffa3d5394d1d8b939fa22ae91bc90d4ac
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423891"
---
# <a name="create-a-service-offering-for-users-in-azure-stack-hub"></a>Vytvoření nabídky služeb pro uživatele v centru Azure Stack

V tomto kurzu se zobrazuje operátor, jak vytvořit nabídku. Nabídka zpřístupňuje dostupnost služeb uživatelům na základě předplatného. Po přihlášení k odběru nabídky má uživatel nárok na vytvoření a nasazení prostředků v rámci služeb určených nabídkou.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte nabídku.
> * Vytvořte plán.
> * Přiřaďte k plánu služby a kvóty.
> * Přiřaďte k nabídce plán.

## <a name="overview"></a>Přehled

Nabídka se skládá z jednoho nebo více plánů. Plán opravňuje přístup k jedné nebo více službám zadáním odpovídajícího poskytovatele prostředků a kvóty jednotlivých služeb. Plány se dají přidat do nabídky jako základní plán, případně můžou nabídku zvětšit jako doplněk. Další informace najdete v tématu [Přehled služeb, plánů, nabídek a předplatných](service-plan-offer-subscription-overview.md).

![Předplatná, nabídky a plány v centru Azure Stack](media/azure-stack-key-features/image4.png)

### <a name="resource-providers"></a>Poskytovatelé prostředků

Poskytovatel prostředků podporuje vytváření, nasazování a správu svých prostředků jako služeb. Běžným příkladem je poskytovatel prostředků Microsoft. COMPUTE, který nabízí možnost vytvářet a nasazovat virtuální počítače (VM). Přehled modelu správy prostředků Azure najdete v tématu [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) .

V centru Azure Stack jsou k dispozici dvě obecné kategorie poskytovatelů prostředků: ty, které nasazují prostředky jako základní služby, a ty, které se nasazují jako služby s hodnotou přidat.

### <a name="foundational-services"></a>Základní služby

>[!NOTE]
> V tomto kurzu se naučíte, jak vytvořit nabídku založenou na základních službách. 

Základní služby jsou podporovány následujícími poskytovateli prostředků, které jsou k dispozici nativně při každé instalaci centra Azure Stack:

| Poskytovatel prostředků | Příklady prostředků |
| ----------------- | ------------------|
| Microsoft.Compute | Virtuální počítače, disky a sady škálování virtuálních počítačů |
| Microsoft.KeyVault | Trezory klíčů, tajné kódy |
| Microsoft.Network | Virtuální sítě, veřejné IP adresy, nástroje pro vyrovnávání zatížení |
| Microsoft.Storage | Účty úložiště, objekty blob, fronty, tabulky |

### <a name="value-add-services"></a>Hodnota – přidat služby

>[!NOTE]
> Aby bylo možné nabídnout službu pro přidávání hodnot, musí být nejprve na webu služby Azure Stack hub Marketplace nainstalován odpovídající poskytovatel prostředků. Po nainstalování se prostředky uživatelům nabídnou stejným způsobem jako základní služby. V části s návody pro aktuální sadu poskytovatelů prostředků, které podporují nabídky služby Value-Add, se podívejte na část s **návody k průvodcům** v obsahu.

Poskytovatelé prostředků, kteří jsou nainstalováni po nasazení centra Azure Stack, podporují služby Value-Add. Příklady obsahují:

| Poskytovatel prostředků | Příklady prostředků |
| ----------------- | ------------------------- |
| Microsoft.Web | App Service aplikace Function App, webové aplikace a API Apps | 
| Microsoft. MySqlAdapter | Hostující Server MySQL, databáze MySQL | 
| Microsoft. SqlAdapter | SQL Server hostitelský server, SQL Server databáze |
| Microsoft.EventHub | Event Hubs |

::: moniker range=">=azs-1902"

## <a name="create-an-offer"></a>Vytvoření nabídky

Během procesu vytváření nabídky vytvoříte nabídku i plán. Plán se používá jako základní plán nabídky. Během vytváření plánu určíte služby, které jsou k dispozici v plánu, a jejich příslušné kvóty.

1. Přihlaste se k portálu pro správu pomocí účtu správce cloudu.

    - V případě integrovaného systému se adresa URL liší v závislosti na oblasti a názvu externí domény vašeho operátora. Adresa URL používá `https://adminportal.<region>.<FQDN>`formátu.
    - Pokud používáte Azure Stack Development Kit, adresa URL je `https://adminportal.local.azurestack.external`.

    Pak vyberte **+ vytvořit prostředek** > **nabídky + plány** > **nabídky**.

    ![Nová nabídka na portálu pro správu centra Azure Stack](media/tutorial-offer-services/1-create-resource-offer.png)

1. V části **vytvořit novou nabídku** na kartě **základy** zadejte **Zobrazovaný název**, **název prostředku**a vyberte existující nebo vytvořte novou **skupinu prostředků**. Zobrazovaný název je popisný název nabídky. Pouze operátor cloudu může zobrazit název prostředku, což je název, který správci používají pro práci s nabídkou jako s prostředkem Azure Resource Manager.

   ![Zobrazovaný název na portálu pro správu centra Azure Stack](media/tutorial-offer-services/2-create-new-offer.png)

1. Vyberte kartu **základní plány** a pak výběrem **vytvořit nový plán** vytvořte nový plán. Plán se také přidá do nabídky jako základní plán.

   ![Přidání plánu na portálu pro správu centra Azure Stack](media/tutorial-offer-services/3-create-new-offer-base-plans.png)

1. V části **Nový plán** na kartě **základy** zadejte **Zobrazovaný název** a **název prostředku**. Zobrazovaný název je popisný název plánu, který se uživatelům zobrazí. Pouze operátor cloudu může zobrazit název prostředku, což je název, který operátory cloudu používají pro práci s plánem jako s Azure Resource Managerm prostředkem. **Skupina prostředků** se nastaví na hodnotu zadanou pro nabídku.

   ![Zobrazovaný název plánu na portálu pro správu centra Azure Stack](media/tutorial-offer-services/4-create-new-plan-basics.png)

1. Vyberte kartu **služby** a zobrazí se seznam služeb, které jsou k dispozici od instalovaných poskytovatelů prostředků. Vyberte **Microsoft. COMPUTE**, **Microsoft. Network**a **Microsoft. Storage**.

   ![Plánování služeb na portálu pro správu centra Azure Stack](media/tutorial-offer-services/5-create-new-plan-services.png)

1. Vyberte kartu **kvóty** a zobrazí se seznam služeb, které jste pro tento plán povolili. Vyberte **vytvořit novou** a zadejte vlastní kvótu pro **Microsoft. COMPUTE**. **Název** kvóty je povinný. můžete přijmout nebo změnit každou hodnotu kvóty. Po dokončení vyberte **OK** a potom tento postup opakujte pro zbývající služby.

   ![Vytvoření kvóty COMPUTE na portálu pro správu centra Azure Stack](media/tutorial-offer-services/6-create-new-plan-quotas.png)

1. Vyberte kartu **Revize + vytvořit** . V horní části by se měla zobrazit zpráva zelená "ověření proběhla", což znamená, že nový základní plán je připravený k vytvoření. Vyberte **Vytvořit**. Mělo by se zobrazit také oznámení oznamující, že byl plán vytvořen.

   ![Vytvoření nového plánu na portálu pro správu centra Azure Stack](media/tutorial-offer-services/7-create-new-plan-review-create.png)

1. Po návratu na kartu **základní plány** stránky pro **Vytvoření nové nabídky** si všimněte, že byl vytvořen plán. Ujistěte se, že nový plán je vybraný pro zahrnutí do nabídky jako základní plán, a pak vyberte **zkontrolovat + vytvořit**.

   ![Přidat základní plán na portál pro správu centra Azure Stack](media/tutorial-offer-services/8-create-new-offer-base-plans-done.png)

1. Na kartě **Revize + vytvořit** by se v horní části měla zobrazit zpráva o zeleném ověření proběhlo. Přečtěte si informace v části "základní" a "základní plány" a po přípravě vyberte **vytvořit** .

   ![Vytvoření nové nabídky na portálu pro správu centra Azure Stack](media/tutorial-offer-services/9-create-new-offer-review-create.png)

1. Po nasazení nabídky se zpočátku zobrazí stránka "vaše nasazení probíhá", po které následuje "nasazení bylo dokončeno". Vyberte název nabídky pod sloupcem **prostředek** .

   ![Nasazení nabídky dokončeno na portálu pro správu centra Azure Stack](media/tutorial-offer-services/10-offer-deployment-complete.png)

1. Všimněte si nápisu, zobrazení vaší nabídky je stále soukromé, což zabrání uživatelům v přihlášení k odběru. Změňte ji na veřejné, a to tak, že vyberete **změnit stav**a pak zvolíte **veřejné**.

    ![Veřejný stav na portálu pro správu centra Azure Stack](media/tutorial-offer-services/11-offer-change-state.png)

::: moniker-end

::: moniker range="<=azs-1901"

## <a name="create-an-offer-1901-and-earlier"></a>Vytvoření nabídky (1901 a starší)

Během procesu vytváření nabídky vytvoříte nabídku i plán. Plán se používá jako základní plán nabídky. Během vytváření plánu určíte služby, které jsou k dispozici v plánu, a jejich příslušné kvóty.

1. Přihlaste se k portálu pro správu pomocí účtu správce cloudu.

    - V případě integrovaného systému se adresa URL liší v závislosti na oblasti vašeho operátoru a názvu externí domény pomocí formátu `https://adminportal.<region>.<FQDN>`.
    - Pokud používáte Azure Stack Development Kit, adresa URL je <https://adminportal.local.azurestack.external>.

    Pak vyberte **+ vytvořit prostředek** > **nabídky + plány** > **nabídky**.

    ![Nová nabídka na portálu pro správu centra Azure Stack](media/tutorial-offer-services/image01.png)

1. V **nabídce nová nabídka**zadejte **Zobrazovaný název** a **název prostředku**a potom vyberte novou nebo existující **skupinu prostředků**. Zobrazovaný název je popisný název nabídky. Pouze operátor cloudu může zobrazit název prostředku, což je název, který správci používají pro práci s nabídkou jako s prostředkem Azure Resource Manager.

   ![Zobrazovaný název na portálu pro správu centra Azure Stack](media/tutorial-offer-services/image02.png)

1. Vyberte **základní plány**a v části **plán** vyberte **Přidat** a přidejte do nabídky nový plán.

   ![Přidání plánu na portálu pro správu centra Azure Stack](media/tutorial-offer-services/image03.png)

1. V části **Nový plán** vyplňte **zobrazované jméno** a **název prostředku**. Zobrazovaný název je popisný název plánu, který se uživatelům zobrazí. Pouze operátor cloudu může zobrazit název prostředku, což je název, který operátory cloudu používají pro práci s plánem jako s Azure Resource Managerm prostředkem.

   ![Zobrazovaný název plánu na portálu pro správu centra Azure Stack](media/tutorial-offer-services/image04.png)

1. Vyberte **služby**. V seznamu služeb vyberte **Microsoft. COMPUTE**, **Microsoft. Network**a **Microsoft. Storage**. Zvolte **Vybrat** a přidejte tyto služby do plánu.

   ![Plánování služeb na portálu pro správu centra Azure Stack](media/tutorial-offer-services/image05.png)

1. Vyberte **kvóty**a pak vyberte první službu, pro kterou chcete vytvořit kvótu. V případě kvóty IaaS použijte následující příklad jako vodítko pro konfiguraci kvót pro výpočetní, síťové a úložné služby.

    - Nejdřív vytvořte kvótu pro výpočetní službu. V seznamu obor názvů vyberte **Microsoft. COMPUTE** a pak vyberte **vytvořit novou kvótu**.
    
      ![Vytvořit novou kvótu](media/tutorial-offer-services/image06.png)

   - Do pole **Vytvořit kvótu**zadejte název kvóty. Můžete změnit nebo přijmout kteroukoli z hodnot kvót, které jsou zobrazeny. V tomto příkladu přijměte výchozí nastavení a vyberte **OK**.
   
      ![Název kvóty](media/tutorial-offer-services/image07.png)
       
    - V seznamu obor názvů vyberte **Microsoft. COMPUTE** a potom vyberte kvótu, kterou jste vytvořili. Tento krok propojí kvótu s výpočetní službou.
    
      ![Vybrat kvótu](media/tutorial-offer-services/image08.png)
        
        Opakujte tyto kroky pro službu sítě a úložiště. Až budete hotovi, vyberte **OK** v části **kvóty** pro uložení všech kvót.

1. V **novém plánu**vyberte **OK**.

1. V části **plán**vyberte nový plán a pak **Vyberte**.

1. V **nabídce nová nabídka**vyberte **vytvořit**. Po vytvoření nabídky se zobrazí oznámení.

1. V nabídce řídicího **panelu vyberte nabídky a potom vyberte nabídku** , kterou jste vytvořili.

1. Vyberte možnost **změnit stav**a pak zvolte možnost **veřejné**.

    ![Veřejný stav](media/tutorial-offer-services/image09.png)

::: moniker-end
 
## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořte nabídku.
> * Vytvořte plán.
> * Přiřaďte k plánu služby a kvóty.
> * Přiřaďte k nabídce plán.

Přejděte k dalšímu kurzu, kde se dozvíte, jak:
> [!div class="nextstepaction"]
> [Testování služeb, které jsou v tomto kurzu nabídnuty](tutorial-test-offer.md)
