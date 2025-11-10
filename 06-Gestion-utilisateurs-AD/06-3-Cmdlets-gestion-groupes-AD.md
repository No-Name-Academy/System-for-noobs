## **3. Cmdlets pour la gestion des groupes AD**

### **3.1. Lister les groupes AD**
```powershell
# Lister tous les groupes
Get-ADGroup -Filter *

# Lister les groupes avec un nom spécifique
Get-ADGroup -Filter "Name -like '*Admin*'"
```

---

### **3.2. Créer un groupe AD**
**Cmdlet** : `New-ADGroup`

**Exemple** :
```powershell
New-ADGroup -Name "Techniciens_IT" -GroupScope Global -GroupCategory Security -Path "OU=Groupes,DC=domaine,DC=local"
```

**Explication des paramètres** :
- **`-GroupScope`** : Portée du groupe (`DomainLocal`, `Global`, `Universal`).
- **`-GroupCategory`** : Type de groupe (`Security` ou `Distribution`).

---

### **3.3. Ajouter un utilisateur à un groupe**
**Cmdlet** : `Add-ADGroupMember`

**Exemple** :
```powershell
Add-ADGroupMember -Identity "Techniciens_IT" -Members "bmonteil"
```

---

### **3.4. Retirer un utilisateur d’un groupe**
**Cmdlet** : `Remove-ADGroupMember`

**Exemple** :
```powershell
Remove-ADGroupMember -Identity "Techniciens_IT" -Members "bmonteil" -Confirm:$false
```

---

### **3.5. Lister les membres d’un groupe**
```powershell
Get-ADGroupMember -Identity "Techniciens_IT" | Select-Object Name, SamAccountName
```

---

### **3.6. Travaux pratiques : Script de gestion des groupes**
**Objectif** :
Ajouter des utilisateurs à des groupes en fonction de leur rôle.

#### **Script : Ajouter des utilisateurs à des groupes**
```powershell
$usersGroups = @{
    "bmonteil" = "Administrateurs", "Techniciens_IT"
    "adupont"  = "Utilisateurs", "Stagiaires"
}

foreach ($user in $usersGroups.Keys) {
    foreach ($group in $usersGroups[$user]) {
        Add-ADGroupMember -Identity $group -Members $user
        Write-Host "Utilisateur $user ajouté au groupe $group."
    }
}
```

---