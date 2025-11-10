## **2. Cmdlets de base pour la gestion des utilisateurs AD**

### **2.1. Lister les utilisateurs AD**
| Cmdlet               | Description                                      |
|----------------------|--------------------------------------------------|
| `Get-ADUser`         | Récupère un ou plusieurs utilisateurs AD.         |
| `Search-ADAccount`   | Recherche des comptes AD (utilisateurs, ordinateurs). |

**Exemples** :
```powershell
# Lister tous les utilisateurs AD
Get-ADUser -Filter *

# Lister les utilisateurs avec un nom spécifique
Get-ADUser -Filter "Name -like '*Monteil*'"

# Rechercher un utilisateur par son identifiant (sAMAccountName)
Get-ADUser -Identity "bmonteil" -Properties *
```

**Afficher des propriétés spécifiques** :
```powershell
Get-ADUser -Identity "bmonteil" -Properties Name, EmailAddress, Enabled | Select-Object Name, EmailAddress, Enabled
```

---

### **2.2. Créer un utilisateur AD**
**Syntaxe** :
```powershell
New-ADUser -Name "Nom Prénom" -SamAccountName "identifiant" -GivenName "Prénom" -Surname "Nom" -Enabled $true -AccountPassword (ConvertTo-SecureString "MotDePasse123!" -AsPlainText -Force) -ChangePasswordAtLogon $true
```

**Exemple complet** :
```powershell
$password = ConvertTo-SecureString "MotDePasse123!" -AsPlainText -Force
New-ADUser -Name "Benoit Monteil" -SamAccountName "bmonteil" -GivenName "Benoit" -Surname "Monteil" -Enabled $true -AccountPassword $password -ChangePasswordAtLogon $true -Path "OU=Utilisateurs,DC=domaine,DC=local"
```

**Explication des paramètres** :
- **`-Name`** : Nom complet de l’utilisateur.
- **`-SamAccountName`** : Identifiant de connexion (ex: `bmonteil`).
- **`-Path`** : Unité d’organisation (OU) où créer l’utilisateur.
- **`-AccountPassword`** : Mot de passe initial (doit être sécurisé avec `ConvertTo-SecureString`).
- **`-ChangePasswordAtLogon`** : Force l’utilisateur à changer son mot de passe à la première connexion.

---

### **2.3. Modifier un utilisateur AD**
**Cmdlet** : `Set-ADUser`

**Exemples** :
```powershell
# Changer le nom ou l’email
Set-ADUser -Identity "bmonteil" -GivenName "Benoit" -EmailAddress "b.monteil@domaine.local"

# Activer/désactiver un compte
Set-ADUser -Identity "bmonteil" -Enabled $false
Set-ADUser -Identity "bmonteil" -Enabled $true

# Déplacer un utilisateur vers une autre OU
Move-ADObject -Identity (Get-ADUser -Identity "bmonteil") -TargetPath "OU=NouvelleOU,DC=domaine,DC=local"
```

---

### **2.4. Supprimer un utilisateur AD**
**Cmdlet** : `Remove-ADUser`

**Exemple** :
```powershell
# Supprimer un utilisateur (avec confirmation)
Remove-ADUser -Identity "bmonteil" -Confirm

# Supprimer sans confirmation
Remove-ADUser -Identity "bmonteil" -Confirm:$false
```

---

### **2.5. Réinitialiser un mot de passe**
```powershell
$newPassword = ConvertTo-SecureString "NouveauMotDePasse123!" -AsPlainText -Force
Set-ADAccountPassword -Identity "bmonteil" -NewPassword $newPassword -Reset
```

---

### **2.6. Travaux pratiques : Script de création d’utilisateurs en masse**
**Objectif** :
Créer plusieurs utilisateurs à partir d’un fichier CSV.

#### **Fichier CSV (`utilisateurs_ad.csv`)** :
```csv
Prenom,Nom,Identifiant,OU,Email
Benoit,Monteil,bmonteil,OU=Utilisateurs,benoit.monteil@domaine.local
Alice,Dupont,adupont,OU=Utilisateurs,alice.dupont@domaine.local
```

#### **Script : Création d’utilisateurs depuis un CSV**
```powershell
$users = Import-Csv -Path "C:\Temp\utilisateurs_ad.csv"
foreach ($user in $users) {
    $password = ConvertTo-SecureString "MotDePasse123!" -AsPlainText -Force
    New-ADUser -Name "$($user.Prenom) $($user.Nom)" -SamAccountName $user.Identifiant -GivenName $user.Prenom -Surname $user.Nom -Enabled $true -AccountPassword $password -ChangePasswordAtLogon $true -Path $user.OU -EmailAddress $user.Email
    Write-Host "Utilisateur $($user.Identifiant) créé."
}
```

---