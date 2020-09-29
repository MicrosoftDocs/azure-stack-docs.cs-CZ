---
title: Správa registrace Azure pro Azure Stack HCI
description: Jak spravovat registraci Azure pro Azure Stack HCI a pochopit stav registrace pomocí prostředí PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: 696ef552dcf49f31fb613a22393617e653f7e10d
ms.sourcegitcommit: eb91a28a19a74f799b093ae2a705f7f6e4c5cd49
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87436468"
---
# <a name="manage-azure-registration"></a>Správa registrace Azure

> Platí pro Azure Stack HCI v20H2

Po vytvoření clusteru Azure Stack HCI musíte [cluster zaregistrovat pomocí ARC Azure](../deploy/register-with-azure.md). Po registraci clusteru pravidelně synchronizuje informace mezi místním clusterem a cloudem. V tomto tématu se dozvíte, jak pochopit stav registrace a zrušit registraci clusteru, až budete připraveni vyřadit z provozu.

## <a name="understanding-registration-status"></a>Princip stavu registrace

Chcete-li pochopit stav registrace, použijte `Get-AzureStackHCI` rutinu prostředí PowerShell a `ClusterStatus` `RegistrationStatus` vlastnosti, a `ConnectionStatus` . Například po instalaci operačního systému Azure Stack HCI před vytvořením nebo připojením ke clusteru se v této `ClusterStatus` Vlastnosti zobrazuje stav není dosud:

:::image type="content" source="media/manage-azure-registration/1-get-azurestackhci.png" alt-text="Stav registrace Azure před vytvořením clusteru":::

Po vytvoření clusteru se `RegistrationStatus` zobrazí pouze stav "nedosud":

:::image type="content" source="media/manage-azure-registration/2-get-azurestackhci.png" alt-text="Stav registrace Azure po vytvoření clusteru":::

Azure Stack HCI se musí zaregistrovat do 30 dnů od instalace podle podmínek Azure Online Services. Pokud není cluster po 30 dnech zaregistrovaný, zobrazí se v `ClusterStatus` `OutOfPolicy` seznamu `RegistrationStatus` `OutOfPolicy` .

Po zaregistrování clusteru můžete zobrazit `ConnectionStatus` `LastConnected` čas a, který je obvykle během posledního dne, pokud není cluster dočasně odpojený od Internetu. Cluster Azure Stack HCI může plně fungovat po dobu až 30 po sobě jdoucích dnů.

:::image type="content" source="media/manage-azure-registration/3-get-azurestackhci.png" alt-text="Stav registrace Azure po registraci":::

Pokud je toto maximální období překročené, zobrazí se `ConnectionStatus` `OutOfPolicy` .

## <a name="azure-active-directory-permissions"></a>Azure Active Directory oprávnění

Kromě vytvoření prostředku Azure v rámci vašeho předplatného se při registraci Azure Stack HCI v tenantovi Azure Active Directory vytvoří identita aplikace, která se v tomto tenantovi podobá uživateli. Identita aplikace dědí název clusteru. Tato identita v rámci vašeho předplatného funguje podle potřeby v Azure Stack cloudové službě HCI.

Pokud je uživatel, který `Register-AzureStackHCI` je spuštěný jako správce Azure Active Directory nebo má delegovaná dostatečná oprávnění, dojde k tomu automaticky a nevyžaduje se žádná další akce. V takovém případě může správce Azure Active Directory pro dokončení registrace potřebovat schválení. Správce může buď výslovně udělit souhlas s aplikací, nebo může delegovat oprávnění, abyste mohli udělit souhlas aplikaci:

:::image type="content" source="media/manage-azure-registration/aad-permissions.png" alt-text="Azure Active Directory oprávnění a diagramu identity" border="false":::

Pokud chcete udělit souhlas, otevřete portal.azure.com a přihlaste se pomocí účtu Azure, který má dostatečná oprávnění pro Azure Active Directory. Přejděte na **Azure Active Directory**a pak **Registrace aplikací**. Vyberte identitu aplikace pojmenovanou po vašem clusteru a přejděte na **oprávnění rozhraní API**.

Aplikace vyžaduje dvě oprávnění:

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Census.Sync

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Billing.Sync
```

Hledání od správce Azure Active Directory může nějakou dobu trvat, takže `Register-AzureStackHCI` rutina ukončí a ponechá registraci ve stavu čeká na schválení správce, tj. částečně dokončená. Po udělení souhlasu jednoduše znovu spusťte `Register-AzureStackHCI` registraci a dokončete ji.

## <a name="unregister-azure-stack-hci-with-azure"></a>Zrušení registrace Azure Stack HCL v Azure

Až budete připraveni vyřadit z provozu cluster HCI Azure Stack, použijte `Unregister-AzStackHCI` rutinu pro zrušení registrace. Tím se zastaví všechny funkce monitorování, podpory a fakturace prostřednictvím ARC Azure. Prostředek Azure, který představuje cluster a identitu aplikace Azure Active Directory, se odstraní, ale skupina prostředků ne, protože by mohla obsahovat jiné nesouvisející prostředky.

Pokud spouštíte `Unregister-AzStackHCI` rutinu na uzlu clusteru, použijte tuto syntaxi a zadejte ID předplatného Azure a také název prostředku pro Azure Stack clusteru HCI, který chcete zrušit registraci:

```PowerShell
Unregister-AzStackHCI -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

Zobrazí se výzva k návštěvě microsoft.com/devicelogin na jiném zařízení (například na počítači nebo telefonu), zadejte kód a přihlaste se k ověřování pomocí Azure.

Pokud spouštíte rutinu z počítače pro správu, budete také muset zadat název serveru v clusteru:

```PowerShell
Unregister-AzStackHCI -ComputerName ClusterNode1 -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

Automaticky se otevře okno interaktivní přihlášení do Azure. Přesné výzvy, které vidíte, se budou lišit v závislosti na nastavení zabezpečení (například dvojúrovňové ověřování). Podle pokynů se přihlaste.

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Připojení Azure Stack HCl k Azure](../deploy/register-with-azure.md)
- [Monitorování Azure Stack HCL pomocí Azure Monitor](azure-monitor.md)
