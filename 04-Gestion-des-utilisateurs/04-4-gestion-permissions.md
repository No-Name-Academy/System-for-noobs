## **4. Gestion des permissions**

### **4.1. Introduction aux ACL (Access Control Lists)**
Les **ACL** définissent les permissions sur les fichiers, dossiers et clés de registre.
PowerShell utilise les cmdlets `Get-Acl` et `Set-Acl` pour gérer les permissions.

---

### **4.2. Afficher les permissions d’un fichier ou dossier**
```powershell
$acl = Get-Acl -Path "C:\Temp\DossierSensible"
$acl.Access | Format-Table -AutoSize
```

---

### **4.3. Ajouter une permission pour un utilisateur**
```powershell
$acl = Get-Acl -Path "C:\Temp\DossierSensible"
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule("BenoitM", "FullControl", "Allow")
$acl.AddAccessRule($rule)
Set-Acl -Path "C:\Temp\DossierSensible" -AclObject $acl
```

---

### **4.4. Supprimer une permission**
```powershell
$acl = Get-Acl -Path "C:\Temp\DossierSensible"
$acl.Access | Where-Object { $_.IdentityReference -eq "DOM\BenoitM" } | ForEach-Object { $acl.RemoveAccessRule($_) }
Set-Acl -Path "C:\Temp\DossierSensible" -AclObject $acl
```

---

### **4.5. Travaux pratiques : Script de gestion des permissions**
**Objectif** :
Automatiser l’attribution de permissions sur un dossier partagé.

#### **Script : Appliquer des permissions à un dossier**
```powershell
$folderPath = "C:\Temp\PartageTechnique"
$usersPermissions = @{
    "BenoitM" = "FullControl"
    "User1"   = "ReadAndExecute"
}

foreach ($user in $usersPermissions.Keys) {
    $acl = Get-Acl -Path $folderPath
    $rule = New-Object System.Security.AccessControl.FileSystemAccessRule($user, $usersPermissions[$user], "Allow")
    $acl.AddAccessRule($rule)
    Set-Acl -Path $folderPath -AclObject $acl
    Write-Host "Permission $($usersPermissions[$user]) attribuée à $user sur $folderPath."
}
```

---