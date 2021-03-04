---
title: Install-AksHciAdAuth
author: jessicaguan
description: Příkaz Install-AksHciAdAuth PowerShellu nainstaluje ověřování služby Active Directory.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: d89861da54cf7fef44d8da57077aba9f372022c3
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873687"
---
# <a name="install-akshciadauth"></a>Install-AksHciAdAuth

## <a name="synopsis"></a>Stručný obsah
Nainstalujte ověřování služby Active Directory.

## <a name="syntax"></a>Syntax

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminUser <String>
                     [-TTL]    
                    
```

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminGroup <String>    
                     [-TTL]    
                    
```

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminUserSID <String>
                     [-TTL]    
                    
```

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminGroupSID <String>    
                     [-TTL]    
                    
```

## <a name="description"></a>Popis
Nainstalujte ověřování služby Active Directory.

## <a name="examples"></a>PŘÍKLADY

### <a name="example"></a>Příklad
```powershell
PS C:\> Install-AksHciAdAuth -name mynewcluster1 -keytab <.\current.keytab> -previousKeytab <.\previous.keytab> -SPN <service/principal@CONTOSO.COM> -adminUser CONTOSO\Bob
```

## <a name="parameters"></a>Parametry

### <a name="-name"></a>– název
Alfanumerický název clusteru Kubernetes.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-keytab"></a>– keytab
Cesta k souboru, který obsahuje soubor keytab. Ujistěte se, že je soubor pojmenován `current.keytab` .

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-previouskeytab"></a>-previousKeytab
Předchozí keytab, která byla použita před aktualizací hesla účtu služby Active Directory. Tato změna je nutná při změně hesla služby Active Directory.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-spn"></a>– SPN
Název instančního objektu, který je přidružený k účtu služby API serveru API.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-adminuser"></a>– adminUser
Uživatelské jméno správce bude mít oprávnění správce clusteru.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-admingroup"></a>– Admins
Název skupiny, kterým se mají udělit oprávnění správce clusteru

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-adminusersid"></a>-adminUserSID
Identifikátor SID uživatele, kterému má být uděleno oprávnění správce clusteru.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-admingroupsid"></a>-adminGroupSID
Identifikátor SID skupiny, kterému má být uděleno oprávnění správce clusteru.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-ttl"></a>-TTL
Doba do provozu (v hodinách), `-previousKeytab` Pokud je zadána. Výchozí hodnota je 10 hodin.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 10
Accept pipeline input: False
Accept wildcard characters: False
```
