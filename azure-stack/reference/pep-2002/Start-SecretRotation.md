---
title: Rutina SecretRotation pro privilegované koncové body pro centrum Azure Stack
description: Referenční informace o privilegovaném koncovém bodu centra Azure Stack PowerShellu – Start-SecretRotation
author: mattbriggs
ms.topic: reference
ms.date: 07/29/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 8a3dee9ad26c37626c0f3383b874f149be4fac01
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742602"
---
# <a name="start-secretrotation"></a>Spustit – SecretRotation

## <a name="synopsis"></a>Stručný obsah
Spustí rotaci tajného kódu razítka.

## <a name="syntax"></a>Syntaxe

```
Start-SecretRotation [-PathAccessCredential <Object>] [-ReRun] [-CertificatePassword <Object>] [-Internal]
 [-PfxFilesPath <Object>] [-AsJob]
```

## <a name="description"></a>Popis
Vyvolá proces střídání tajných klíčů pro tajné klíče infrastruktury Azure Stackho centrálního systému. Ve výchozím nastavení otočí pouze certifikáty koncových bodů infrastruktury externích sítí. Další podrobnosti najdete v tématu o [otočení tajných kódů v centru Azure Stack](../../operator/azure-stack-rotate-secrets.md) .

## <a name="parameters"></a>Parametry

### <a name="-internal"></a>– Interní
Přetočit tajné klíče pro koncové body infrastruktury interní sítě.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-pfxfilespath"></a>-PfxFilesPath
Cesta k novým souborům PFX sdíleného pro otočení externích certifikátů
Je povinná, pokud je žádoucí otočení externích certifikátů.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-pathaccesscredential"></a>-PathAccessCredential
Přihlašovací údaje pro přístup k PfxFilesPath.
Je povinná, pokud je žádoucí otočení externích certifikátů.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-certificatepassword"></a>– CertificatePassword
Heslo pro nové zadané soubory PFX.
Je povinná, pokud je žádoucí otočení externích certifikátů.
Může se lišit od původního hesla PFX zadaného v počátečním čase nasazení.
Znovu vygenerujeme soubory PFX se správným heslem certifikační autority.

Použití::

```console
    # Rotates external certificates only
    Start-SecretRotation -PfxFilesPath \<String\> -PathAccessCredential \<PSCredential\> -CertificatePassword \<SecureString\>

    # Rotates internal secrets only
    Start-SecretRotation -Internal  

    # Reruns internal secrets only
    Start-SecretRotation -Internal -ReRun 

    # Reruns external certificates only
    Start-SecretRotation -ReRun
```

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-rerun"></a>-Znovu spustit
 

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-asjob"></a>– AsJob


```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="next-steps"></a>Další kroky

Informace o přístupu a používání privilegovaného koncového bodu najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](../../operator/azure-stack-privileged-endpoint.md).
