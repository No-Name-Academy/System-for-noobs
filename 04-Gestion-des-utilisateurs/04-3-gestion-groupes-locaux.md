## **3. Gestion des groupes locaux**

### **3.1. Lister les groupes locaux**
```powershell
Get-LocalGroup
```

---

### **3.2. Créer un groupe local**
```powershell
New-LocalGroup -Name "Techniciens" -Description "Groupe pour les techniciens IT"
```

---

### **3.3. Ajouter un utilisateur à un groupe**
```powershell
Add-LocalGroupMember -Group "Techniciens" -Member "BenoitM"
```

---

### **3.4. Retirer un utilisateur d’un groupe**
```powershell
Remove-LocalGroupMember -Group "Techniciens" -Member "BenoitM"
```

---

### **3.5. Lister les membres d’un groupe**
```powershell
Get-LocalGroupMember -Group "Techniciens"
```

---

### **3.6. Supprimer un groupe local**
```powershell
Remove-LocalGroup -Name "Techniciens"
```

---

### **3.7. Travaux pratiques : Script de gestion des groupes**
**Objectif** :
Créer un script pour ajouter des utilisateurs à des groupes en fonction de leur rôle.

#### **Script : Ajouter des utilisateurs à des groupes**
```powershell
# Définir les utilisateurs et leurs groupes
$usersGroups = @{
    "BenoitM" = "Administrateurs", "Techniciens"
    "User1"   = "Utilisateurs", "Techniciens"
}

foreach ($user in $usersGroups.Keys) {
    foreach ($group in $usersGroups[$user]) {
        Add-LocalGroupMember -Group $group -Member $user
        Write-Host "Utilisateur $user ajouté au groupe $group."
    }
}
```

---