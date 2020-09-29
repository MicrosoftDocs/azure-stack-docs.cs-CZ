---
title: Set-SyslogServer
description: Referenční informace pro Azure Stack privilegovaného koncového bodu prostředí PowerShell – Close-PrivilegedEndpoint
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 328862a4d6d598781a88597ea1d03fbaf50a4885
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742685"
---
# <a name="set-syslogserver"></a>Set-SyslogServer

## <a name="synopsis"></a>Stručný obsah
Nastaví koncový bod serveru syslog.

## <a name="syntax"></a>Syntaxe

```
Set-SyslogServer [-Remove] [[-ServerName] <Object>] [-NoEncryption] [-SkipCertificateCheck] [-UseUDP]
 [-SkipCNCheck] [[-ServerPort] <Object>] [-AsJob]
```


## <a name="examples"></a>Příklady

### <a name="example-1"></a>Příklad 1

```
Set-SyslogServer -ServerName <FQDN or IP address of Syslog server>
```

### <a name="example-2"></a>Příklad 2
```
-NoEncryption
```

### <a name="example-3"></a>Příklad 3
```
-SkipCertificateCheck
```

### <a name="example-4"></a>Příklad 4
```
-SkipCNCheck
```

### <a name="example-5"></a>Příklad 5
```
-UseUDP
```

### <a name="example-6"></a>Příklad 6
```
Set-SyslogServer -Remove
```

## <a name="parameters"></a>Parametry

### <a name="-servername"></a>-Servername
Plně kvalifikovaný název domény nebo adresa IPv4 serveru syslog.
Bez dalšího parametru je výchozí protokol TCP s povoleným šifrováním; Ověření certifikátu; Ověření názvu certifikátu.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-serverport"></a>-ServerPort
Port serveru syslog serveru.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: 0
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-noencryption"></a>– Šifrování
Pokud si přejete zakázat šifrování pro přenosy TCP, zadejte nešifrované.

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

### <a name="-skipcncheck"></a>-SkipCNCheck
Pokud si přejete Přeskočit kontrolu názvu certifikátu, zadejte SkipCNCheck.

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

### <a name="-skipcertificatecheck"></a>-SkipCertificateCheck
Pokud si přejete Přeskočit kontrolu certifikátu serveru syslog, zadejte SkipCertificateCheck.

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

### <a name="-useudp"></a>-UseUDP
Pokud server syslog používá protokol UDP, zadejte UseUDP.

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

### <a name="-remove"></a>-Remove
 

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
