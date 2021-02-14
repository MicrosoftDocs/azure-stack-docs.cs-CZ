---
title: Přístup k pracovní stanici privilegovaného přístupu a privilegovanému koncovému bodu
description: Přečtěte si o přístupu k privilegovanému přístupu z pracovní stanice a privilegovaného koncového bodu
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: b17bf72931d5e80f9cfebe6df54655856695dba2
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487982"
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

## <a name="further-reading"></a>Další čtení

Další informace o připojení a práci s privilegovaným koncovým bodem najdete v tématu [použití privilegovaného koncového bodu v](../../operator/azure-stack-privileged-endpoint.md) 
 [centru](../../operator/azure-stack-privileged-endpoint.md)Azure Stack.