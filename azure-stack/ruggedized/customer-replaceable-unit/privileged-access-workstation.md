---
title: Přístup k pracovní stanici privilegovaného přístupu a privilegovanému koncovému bodu
description: Přečtěte si o přístupu k privilegovanému přístupu z pracovní stanice a privilegovaného koncového bodu
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 96a4e944b6b86c8b5db314141fd3473a8512d518
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256196"
---
# <a name="privileged-access-workstation-and-privileged-endpoint-access"></a>Přístup k pracovní stanici privilegovaného přístupu a privilegovanému koncovému bodu

## <a name="overview"></a>Přehled

Pro tento postup je nutné se připojit k pracovní stanici privilegovaného přístupu (PRIVILEGOVANÝM přístupem). Zákazník bude muset poskytnout možnost připojit se k PRIVILEGOVANÝM přístupem pomocí vzdálené plochy.

## <a name="configuring-the-winrm"></a>Konfigurace služby WinRM

Aby bylo možné v PRIVILEGOVANÝM přístupem připojení k privilegovanému koncovému bodu, ujistěte se, že IP adresy privilegovaného koncového bodu, jak jsou definovány na portálu pro správu centra Azure Stack, jsou v PRIVILEGOVANÝM přístupem nastaveny jako důvěryhodného hostitele. Pokyny pro získání těchto IP adres z portálu pro správu jsou v tématu ověření přístupu k uzlu jednotky škálování a stavu na stránce 16.

Pokud chcete zobrazit nebo upravit důvěryhodné hostitele WinRM, spusťte relaci PowerShellu se zvýšenými oprávněními:

-   Zobrazit důvěryhodné hostitele.

Pokud chcete zobrazit aktuální důvěryhodné hostitele, v PowerShellu spusťte:

-   Upravte důvěryhodné hostitele.

Pokud nejsou k dispozici IP adresy serveru konzoly pro nouzové obnovení (ERCS), spusťte následující příkaz, kterým nastavíte novou hodnotu pro důvěryhodné hostitele, nahradíte * \< ERCS01_IP \* , * \< ERCS02_IP \* a * \< ERCS03_IP \* se třemi privilegovanými IP adresami, které jsou definovány na portálu pro správu centra Azure Stack:

## <a name="connect-to-the-privileged-endpoint"></a>Připojení k privilegovanému koncovému bodu

V PRIVILEGOVANÝM přístupem otevřete relaci PowerShellu se zvýšenými oprávněními a spusťte následující dva příkazy. Nahraďte * \< ERCS_IP \* IP adresou jedné z privilegovaných koncových bodů, jak je uvedeno výše v tomto postupu. Po zobrazení výzvy zadejte pověření privilegovaného koncového bodu (PEP) zadané zákazníkem.

## <a name="close-the-privileged-endpoint"></a>Zavření privilegovaného koncového bodu

Chcete-li zavřít relaci privilegovaného koncového bodu, spusťte následující příkaz:

## <a name="further-reading"></a>Další materiály

Další informace o připojení a práci s privilegovaným koncovým bodem najdete v tématu [použití privilegovaného koncového bodu v](../../operator/azure-stack-privileged-endpoint.md) 
 [centru](../../operator/azure-stack-privileged-endpoint.md)Azure Stack.