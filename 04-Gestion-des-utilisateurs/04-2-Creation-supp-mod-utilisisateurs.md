## **2. Création, suppression et modification d’utilisateurs locaux**

### **2.1. Lister les utilisateurs locaux**
Pour afficher la liste des utilisateurs locaux :
```powershell
Get-LocalUser
```
**Filtrer par nom** :
```powershell
Get-LocalUser -Name "Utilisateur*"
```

---

### **2.2. Créer un utilisateur local**
**Syntaxe** :
```powershell
New-LocalUser -Name "NomUtilisateur" -Description "Description" -NoPassword
```
**Exemple** :
```powershell
New-LocalUser -Name "BenoitM" -Description "Compte technique pour Benoit Monteil" -NoPassword
```
**Créer un utilisateur avec mot de passe** :
```powershell
$password = ConvertTo-SecureString "MotDePasse123!" -AsPlainText -Force
New-LocalUser -Name "BenoitM" -Password $password -FullName "Benoit Monteil" -Description "Compte technique"
```

---

### **2.3. Modifier un utilisateur local**
**Changer le nom complet ou la description** :
```powershell
Set-LocalUser -Name "BenoitM" -FullName "Benoit Monteil (Admin)" -Description "Compte administrateur technique"
```
**Changer le mot de passe** :
```powershell
$newPassword = ConvertTo-SecureString "NouveauMotDePasse123!" -AsPlainText -Force
Set-LocalUser -Name "BenoitM" -Password $newPassword
```

---

### **2.4. Supprimer un utilisateur local**
```powershell
Remove-LocalUser -Name "BenoitM"
```
**Suppression avec confirmation** :
```powershell
Remove-LocalUser -Name "BenoitM" -Confirm
```

---

### **2.5. Désactiver ou activer un utilisateur**
```powershell
# Désactiver un utilisateur
Disable-LocalUser -Name "BenoitM"

# Activer un utilisateur
Enable-LocalUser -Name "BenoitM"
```

---

### **2.6. Travaux pratiques : Script de création d’utilisateurs en masse**
**Objectif** :
Créer un script pour ajouter plusieurs utilisateurs à partir d’un fichier CSV.

#### **Fichier CSV (`utilisateurs.csv`)** :
```csv
NomUtilisateur,NomComplet,Description,MotDePasse
User1,Utilisateur Un,Compte technique,Pass123!
User2,Utilisateur Deux,Compte standard,Pass123!
```

#### **Script : Création d’utilisateurs depuis un CSV**
```powershell
$users = Import-Csv -Path "C:\Temp\utilisateurs.csv"
foreach ($user in $users) {
    $password = ConvertTo-SecureString $user.MotDePasse -AsPlainText -Force
    New-LocalUser -Name $user.NomUtilisateur -FullName $user.NomComplet -Description $user.Description -Password $password
    Write-Host "Utilisateur $($user.NomUtilisateur) créé."
}
```

---