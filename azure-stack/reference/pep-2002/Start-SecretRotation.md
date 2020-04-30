---
title: Zavřít-PrivilegedEndpoint privilegovaný koncový bod pro centrum Azure Stack
description: Referenční informace pro Azure Stack privilegovaného koncového bodu prostředí PowerShell – Close-PrivilegedEndpoint
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: b61955c6fce760654430b999c3b393e9093db535
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563452"
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
Vyvolá proces rotace tajných kódů.

## <a name="parameters"></a>Parametry

### <a name="-internal"></a>– Interní
 

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
    # Rotates external certificates only
    Start-SecretRotation -PfxFilesPath \<String\> -PathAccessCredential \<PSCredential\> -CertificatePassword \<SecureString\>

    # Rotates internal secrets only
    Start-SecretRotation -Internal  

    # Reruns internal secrets only
    Start-SecretRotation -Internal -ReRun 

    # Reruns external certificates only
    Start-SecretRotation -ReRun

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

Informace o přístupu a používání privilegovaného koncového bodu najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).