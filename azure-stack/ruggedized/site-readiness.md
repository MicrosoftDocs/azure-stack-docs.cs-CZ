---
title: Azure Stack centrum robustní připravenost webů pro Azure Stack centra | Microsoft Docs
description: Seznamte se se specifikacemi připravenosti webu pro Azure Stackho centra.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2020
ms.author: wamota
ms.reviewer: wamota
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 02e549d4cae9dc0c543d4a21c48e972231257ced
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939596"
---
# <a name="azure-stack-hub-ruggedized-site-readiness"></a>Připravenost na robustní lokalitu centra Azure Stack

V tomto tématu se dozvíte o požadavcích na napájení a vyřazení z jednotky PDU pro prostředí centra Azure Stack. 

>[!NOTE]
>Tyto hodnoty jsou určené výhradně pro účely plánování zařízení a jsou přibližné a konzervativní. Skutečné požadavky se mohou lišit.

## <a name="environmental-requirements"></a>Požadavky na prostředí

V následující tabulce jsou uvedené požadavky na prostředí pro robustní řešení Azure Stackho centra s následující konfigurací:

- jednotka škálování na 14 TB
- 200 Volt Input napětí
- 35 oC maximální pokojová teplota

*Tabulka 1. Požadavky na vysokou a nízkou konfiguraci prostředí*

| Objekt                         | Robustní požadavky centra Azure Stack               |
|--------------------------------|--------------------------------|
|Provozní teplota           | NaAzure Stacka robustní provozní teplotu centra (s požadavky na topení):-32 oC (-25.6 °F) na 43 °C (109 °F).    |
|Vlhkost a vlhkost           | Úložiště: 5% až 95% RH s 33 oC (91 °F) maximálním bodem Dew. Atmosféra musí být neustále nekondenzovaná. <br> Operační systém: 5% až 85% RH s 29 oC (84.2 °F) maximálním bodem Dew.
|Fyzické připojení           | Robustní centrum Azure Stack se dá fyzicky propojit prostřednictvím následujících kroků: <br>4x10G SR SFP + <br>4x1000BASE-SX <br>4x 1000BASE-T
|Vstup napájení                     | Max 4,981 kW, průměr 4,391 KW<br> Vstupní konektor C13/C14<br> Vstup: 100 – 240V 50/60 Hz

## <a name="pdu-power-drop-requirements"></a>Požadavky na napájení PDU pro vyřazení

V následující tabulce je uveden seznam výpadků vyžadovaných pro robustní centrum Azure Stack.

*Tabulka 2. Požadovaný počet výpadků napájení*

| Konfigurace  | Jedna fáze  | 3Fázový rozdíl na třech fázích |Tři fáze Wye |
|----------------|---------------|-------------------|----------------|
|Vysoká/nízká        | 2             | 2                 | 2              |

Azure Stack integrovaný systém vám umožňuje používat různé typy konektorů PDU pro nejlepší integraci do vašeho datového centra. Následující tabulka uvádí typy konektorů:

*Tabulka 3. PDU a možnosti konektoru*

| Umístění     | Jedna fáze                                | 3Fázový rozdíl na třech fázích                                   | Tři fáze Wye                                        |
|--------------|---------------------------------------------|-----------------------------------------------------|-----------------------------------------------------------|
|Severní Amerika |- L630P<br>– L7 – 30P<br>- Russellstoll 3750DP |– Hubbell pro CS8365L<br>-Russellstoll 9P54U2T/1100 |- Hubbell C530P6S<br>- ABL Sursum S52S0A<br>– Plující zájemci |


