## **2. Gestion des mots de passe et des politiques**

### **2.1. Réinitialisation des mots de passe**
#### **2.1.1. Réinitialiser les mots de passe des utilisateurs locaux**
**Script : Réinitialisation des mots de passe locaux**
```powershell
<#
.SYNOPSIS
    Réinitialise les mots de passe des utilisateurs locaux.

.DESCRIPTION
    Ce script réinitialise les mots de passe des utilisateurs locaux à une valeur par défaut.

.PARAMETER DefaultPassword
    Mot de passe par défaut à appliquer.

.EXAMPLE
    .\Reset-LocalUserPasswords.ps1 -DefaultPassword "NouveauMotDePasse123!"
#>
param (
    [Parameter(Mandatory=$true)]
    [string]$DefaultPassword
)

# Convertir le mot de passe en SecureString
$securePassword = ConvertTo-SecureString $DefaultPassword -AsPlainText -Force

# Récupérer les utilisateurs locaux
$users = Get-LocalUser | Where-Object { $_.Enabled -eq $true }

foreach ($user in $users) {
    # Réinitialiser le mot de passe
    Set-LocalUser -Name $user.Name -Password $securePassword

    # Journaliser l'action
    Write-Host "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] Mot de passe réinitialisé pour l'utilisateur $($user.Name)." -ForegroundColor Yellow
}
```

---

#### **2.1.2. Réinitialiser les mots de passe des utilisateurs AD**
**Script : Réinitialisation des mots de passe AD**
```powershell
<#
.SYNOPSIS
    Réinitialise les mots de passe des utilisateurs AD.

.DESCRIPTION
    Ce script réinitialise les mots de passe des utilisateurs AD à une valeur par défaut et force le changement au prochain login.

.PARAMETER DefaultPassword
    Mot de passe par défaut à appliquer.

.PARAMETER OuPath
    Chemin de l'OU contenant les utilisateurs à traiter.

.EXAMPLE
    .\Reset-ADUserPasswords.ps1 -DefaultPassword "NouveauMotDePasse123!" -OuPath "OU=Utilisateurs,DC=domaine,DC=local"
#>
param (
    [Parameter(Mandatory=$true)]
    [string]$DefaultPassword,

    [string]$OuPath = "OU=Utilisateurs,DC=domaine,DC=local"
)

# Importer le module ActiveDirectory
Import-Module ActiveDirectory

# Convertir le mot de passe en SecureString
$securePassword = ConvertTo-SecureString $DefaultPassword -AsPlainText -Force

# Récupérer les utilisateurs AD dans l'OU spécifiée
$users = Get-ADUser -Filter * -SearchBase $OuPath -Properties SamAccountName

foreach ($user in $users) {
    # Réinitialiser le mot de passe
    Set-ADAccountPassword -Identity $user.SamAccountName -NewPassword $securePassword -Reset
    Set-ADUser -Identity $user.SamAccountName -ChangePasswordAtLogon $true

    # Journaliser l'action
    Write-Host "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] Mot de passe réinitialisé pour l'utilisateur AD $($user.SamAccountName)." -ForegroundColor Yellow
}
```

---

### **2.2. Application des politiques de mot de passe**
#### **2.2.1. Vérifier la conformité des mots de passe**
**Script : Vérification de la complexité des mots de passe**
```powershell
<#
.SYNOPSIS
    Vérifie que les mots de passe des utilisateurs AD respectent les politiques de complexité.

.DESCRIPTION
    Ce script vérifie si les mots de passe des utilisateurs AD respectent les politiques de complexité définies.

.EXAMPLE
    .\Check-ADPasswordPolicy.ps1
#>
Import-Module ActiveDirectory

# Récupérer la politique de mot de passe du domaine
$passwordPolicy = (Get-ADDefaultDomainPasswordPolicy)

# Afficher les paramètres de la politique
Write-Host "Politique de mot de passe du domaine :"
Write-Host "Complexité requise : $($passwordPolicy.ComplexityEnabled)"
Write-Host "Longueur minimale : $($passwordPolicy.MinPasswordLength)"
Write-Host "Durée minimale du mot de passe : $($passwordPolicy.MinPasswordAge.Days) jours"
Write-Host "Durée maximale du mot de passe : $($passwordPolicy.MaxPasswordAge.Days) jours"
Write-Host "Historique des mots de passe : $($passwordPolicy.PasswordHistoryCount) mots de passe mémorisés"
```

---

#### **2.2.2. Forcer l’expiration des mots de passe**
**Script : Forcer l’expiration des mots de passe AD**
```powershell
<#
.SYNOPSIS
    Force l'expiration des mots de passe pour les utilisateurs AD.

.DESCRIPTION
    Ce script force l'expiration des mots de passe pour les utilisateurs AD spécifiés.

.PARAMETER OuPath
    Chemin de l'OU contenant les utilisateurs à traiter.

.EXAMPLE
    .\Expire-ADUserPasswords.ps1 -OuPath "OU=Utilisateurs,DC=domaine,DC=local"
#>
param (
    [string]$OuPath = "OU=Utilisateurs,DC=domaine,DC=local"
)

# Importer le module ActiveDirectory
Import-Module ActiveDirectory

# Récupérer les utilisateurs AD dans l'OU spécifiée
$users = Get-ADUser -Filter * -SearchBase $OuPath -Properties SamAccountName

foreach ($user in $users) {
    # Forcer l'expiration du mot de passe
    Set-ADUser -Identity $user.SamAccountName -ChangePasswordAtLogon $true

    # Journaliser l'action
    Write-Host "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] Expiration du mot de passe forcée pour l'utilisateur AD $($user.SamAccountName)." -ForegroundColor Yellow
}
```

---