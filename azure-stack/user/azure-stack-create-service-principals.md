---
title: Vytvoření instančního objektu pro Azure Stack | Dokumentace Microsoftu
description: Popisuje, jak vytvořit instanční objekt, který lze použít s řízením přístupu na základě role v Azure Resource Manageru pro správu přístupu k prostředkům.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/15/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 12/12/2018
ms.openlocfilehash: 3860f8dcc99f3505fe2f838e75f5e8f09b78aefe
ms.sourcegitcommit: 8cb2b567e9914d4d07e754d95c0864aa55868579
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65855379"
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>Přístup aplikací k prostředkům Azure Stack tak, že vytvoříte instanční objekty

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Aplikaci přístup k prostředkům Azure Stack nabídnou ve vytváření instančního objektu, která používá Azure Resource Manageru. Instanční objekt služby umožňuje delegovat konkrétní oprávnění pomocí [řízení přístupu na základě rolí](azure-stack-manage-permissions.md).

Jako osvědčený postup byste měli použít instanční objekty pro vaše aplikace. Instanční objekty jsou upřednostňovány vůči spuštění aplikace pomocí vlastních přihlašovacích údajů z následujících důvodů:

* Můžete přiřadit oprávnění pro instanční objekt, který se liší od účtu oprávnění. Oprávnění objektu služby jsou obvykle omezené na přesně co aplikace potřebuje umět.
* Není potřeba změnit přihlašovací údaje aplikace, pokud role a zodpovědnosti změnit.
* Certifikát můžete použít k automatizaci ověřování při spuštění bezobslužného skriptu.

## <a name="example-scenario"></a>Příklad scénáře

Máte aplikaci pro správu konfigurace, kterou je potřeba inventář prostředků Azure pomocí Azure Resource Manageru. Můžete vytvořit instanční objekt a přiřaďte ho k roli Čtenář. Tato role poskytuje aplikaci přístup jen pro čtení k prostředkům Azure.

## <a name="getting-started"></a>Začínáme

Jako vodítko použijte postup v tomto článku:

* Vytvoření instančního objektu pro vaši aplikaci.
* Svou aplikaci zaregistrovat a vytvořit ověřovací klíč.
* Přiřazení aplikace k roli.

Způsob, jakým jste nakonfigurovali Active Directory pro Azure Stack Určuje, jak vytvořit instanční objekt služby. Vyberte si jednu z následujících možností:

* Vytvořit instanční objekt pro [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad).
* Vytvořit instanční objekt pro [Active Directory Federation Services (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

Postup pro přiřazení instančního objektu k roli stejný pro Azure AD a AD FS. Po vytvoření instančního objektu můžete [delegovat oprávnění](azure-stack-create-service-principals.md) po přiřazení k roli.

## <a name="create-service-principal-for-azure-ad"></a>Vytvoření instančního objektu pro službu Azure AD

Pokud služby Azure Stack používá jako úložiště identit Azure AD, které můžete vytvořit instanční objekt pomocí stejného postupu jako v Azure pomocí webu Azure portal.

> [!NOTE]
> Zkontrolujte, že máte [požadovaná oprávnění Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions) předtím, než začnete vytvářet instanční objekt služby.

### <a name="create-service-principal"></a>Vytvořit instanční objekt

Vytvoření instančního objektu pro aplikaci:

1. Přihlaste se ke svému účtu Azure prostřednictvím [webu Azure portal](https://portal.azure.com).
2. Vyberte **Azure Active Directory** > **registrace aplikací** > **registrace nové**.
3. Zadejte název.
4. Vyberte **podporovaných typů účtu**.
5.  Přidejte identifikátor URI pro aplikaci. Vyberte **webové** pro typ aplikace, kterou chcete vytvořit. Po nastavení hodnot, vyberte **zaregistrovat**.

Vytvoříte instanční objekt služby pro vaši aplikaci.

### <a name="get-credentials"></a>Získat přihlašovací údaje

Při přihlašování prostřednictvím kódu programu, použijte ID pro vaše aplikace a ověřovacího klíče. K získání těchto hodnot:

1. Z **registrace aplikací** ve službě Active Directory, vyberte svou aplikaci.

2. Zkopírujte **ID aplikace** a uložte ho v kódu aplikace. Aplikace v ukázkových aplikací používají **ID klienta** k odkazování na **ID aplikace**.

     ![ID aplikace pro aplikaci](./media/azure-stack-create-service-principals/image12.png)
3. Pokud chcete generovat ověřovací klíč, vyberte **certifikáty a tajné kódy**.

4. Vyberte **Nový tajný klíč klienta**.

5. Zadejte popis klíče, vyberte dobu platnosti pro klíče a vyberte **přidat**. 

6. Až budete hotovi, zobrazí se hodnota tajného kódu. Poznamenejte si tuto hodnotu vzhledem k tomu, že klíč nelze načíst později. Hodnotu klíče uložte na místo, odkud ji aplikace může načíst.

![Klíč-hodnota upozornění pro klíč uložený.](./media/azure-stack-create-service-principals/image15.png)

Posledním krokem je [přiřazení role aplikace](azure-stack-create-service-principals.md).

## <a name="create-service-principal-for-ad-fs"></a>Vytvoření instančního objektu služby AD FS

Pokud jste nasadili Azure Stack pomocí služby AD FS jako úložiště identit, můžete použít prostředí PowerShell pro následující úlohy:

* Vytvoření instančního objektu.
* Přiřadíte roli instančnímu objektu služby.
* Přihlaste se pomocí instančního objektu identity.

Podrobnosti o tom, jak vytvořit instanční objekt služby najdete v tématu [vytvořit instanční objekt služby AD FS](../operator/azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

## <a name="assign-the-service-principal-to-a-role"></a>Přiřazení instančního objektu k roli

Pro přístup k prostředkům ve vašem předplatném, musíte přiřadit aplikace k roli. Rozhodněte, jakou roli představuje správná oprávnění pro aplikaci. Další informace o dostupných rolí, najdete v článku [RBAC: Vestavěné role](/azure/role-based-access-control/built-in-roles).

> [!NOTE]
> Můžete nastavit obor této role na úrovni předplatného, skupinu prostředků nebo prostředek. Oprávnění se dědí do oboru na nižších úrovních. Aplikaci se službou role Čtenář pro skupinu prostředků například znamená, že aplikace může číst libovolný z prostředků ve skupině prostředků.

Následující postup použijte jako vodítko pro přiřazení role instančnímu objektu služby.

1. Na portálu Azure Stack přejděte na úroveň oboru, kterou chcete přiřadit aplikaci. Například vyberte přiřazení role v oboru předplatného, **předplatná**.

2. Vyberte předplatné, které chcete přiřadit aplikaci. V tomto příkladu je předplatné sady Visual Studio Enterprise.

     ![Vyberte předplatné sady Visual Studio Enterprise pro přiřazení](./media/azure-stack-create-service-principals/image16.png)

3. Vyberte **řízení přístupu (IAM)** pro předplatné.

4. Vyberte **přidat přiřazení role**.

5. Vyberte roli, kterou chcete přiřadit k aplikaci.

6. Vyhledávání pro vaši aplikaci a vyberte ji.

7. Vyberte **OK** k dokončení přiřazení role. Zobrazí se vaše aplikace v seznamu Uživatelé přiřazení k roli pro tento obor.

Teď, když máte vytvořený instanční objekt služby a přiřazenou roli, může vaše aplikace přístup k prostředkům Azure Stack.

## <a name="next-steps"></a>Další postup

[Správa uživatelských oprávnění](azure-stack-manage-permissions.md)
