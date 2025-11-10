## **3. Travaux pratiques : Automatiser la gestion des utilisateurs avec un script modulaire**

### **3.1. Scénario : Script complet de gestion des utilisateurs AD**
**Objectif** :
Créer un script modulaire qui permet de :
1. Créer des utilisateurs AD depuis un CSV.
2. Ajouter des utilisateurs à des groupes.
3. Réinitialiser les mots de passe.
4. Supprimer les utilisateurs inactifs.

**Structure du script** :
```plaintext
UserManagement/
├── New-ADUsersFromCsv.ps1       # Création d'utilisateurs
├── Add-ADUsersToGroups.ps1      # Ajout aux groupes
├── Reset-ADUserPasswords.ps1    # Réinitialisation des mots de passe
├── Remove-InactiveADUsers.ps1   # Suppression des utilisateurs inactifs
└── UserManagement.ps1          # Script principal
```

---

#### **3.1.1. Script principal (`UserManagement.ps1`)**
```powershell
<#
.SYNOPSIS
    Script principal pour la gestion automatisée des utilisateurs AD.

.DESCRIPTION
    Ce script appelle les modules pour créer, modifier et supprimer des utilisateurs AD.

.PARAMETER Action
    Action à effectuer : Create, AddToGroups, ResetPasswords, RemoveInactive.

.PARAMETER CsvPath
    Chemin vers le fichier CSV (pour la création d'utilisateurs).

.PARAMETER OuPath
    Chemin de l'OU à traiter (pour les autres actions).

.PARAMETER DaysInactive
    Nombre de jours d'inactivité pour la suppression (optionnel).

.EXAMPLE
    .\UserManagement.ps1 -Action Create -CsvPath "C:\Temp\utilisateurs_ad.csv"
    .\UserManagement.ps1 -Action AddToGroups -OuPath "OU=Utilisateurs,DC=domaine,DC=local"
    .\UserManagement.ps1 -Action ResetPasswords -OuPath "OU=Utilisateurs,DC=domaine,DC=local"
    .\UserManagement.ps1 -Action RemoveInactive -OuPath "OU=Utilisateurs,DC=domaine,DC=local" -DaysInactive 90
#>
param (
    [Parameter(Mandatory=$true)]
    [ValidateSet("Create", "AddToGroups", "ResetPasswords", "RemoveInactive")]
    [string]$Action,

    [string]$CsvPath,
    [string]$OuPath,
    [int]$DaysInactive
)

# Importer le module ActiveDirectory
Import-Module ActiveDirectory

# Définir le chemin du log
$logFile = "C:\Temp\UserManagement_$(Get-Date -Format 'yyyyMMdd').log"

# Fonction pour journaliser les actions
function Write-Log {
    param (
        [string]$Message
    )
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $logMessage = "[$timestamp] $Message"
    Write-Host $logMessage
    Add-Content -Path $logFile -Value $logMessage
}

# Exécuter l'action demandée
switch ($Action) {
    "Create" {
        Write-Log "Début de la création des utilisateurs AD depuis $CsvPath"
        .\New-ADUsersFromCsv.ps1 -CsvPath $CsvPath
        Write-Log "Fin de la création des utilisateurs AD"
    }

    "AddToGroups" {
        Write-Log "Début de l'ajout des utilisateurs aux groupes dans $OuPath"
        .\Add-ADUsersToGroups.ps1 -OuPath $OuPath
        Write-Log "Fin de l'ajout des utilisateurs aux groupes"
    }

    "ResetPasswords" {
        $defaultPassword = Read-Host "Entrez le mot de passe par défaut" -AsSecureString | ConvertFrom-SecureString
        $defaultPasswordPlain = ConvertTo-SecureString $defaultPassword -AsPlainText -Force | ConvertFrom-SecureString
        Write-Log "Début de la réinitialisation des mots de passe dans $OuPath"
        .\Reset-ADUserPasswords.ps1 -DefaultPassword $defaultPasswordPlain -OuPath $OuPath
        Write-Log "Fin de la réinitialisation des mots de passe"
    }

    "RemoveInactive" {
        Write-Log "Début de la suppression des utilisateurs inactifs dans $OuPath (inactifs depuis $DaysInactive jours)"
        .\Remove-InactiveADUsers.ps1 -OuPath $OuPath -DaysInactive $DaysInactive
        Write-Log "Fin de la suppression des utilisateurs inactifs"
    }
}
```

---

#### **3.1.2. Script `Add-ADUsersToGroups.ps1`**
```powershell
<#
.SYNOPSIS
    Ajoute les utilisateurs AD à des groupes en fonction d'un mapping.

.DESCRIPTION
    Ce script ajoute les utilisateurs AD à des groupes prédéfinis.

.PARAMETER OuPath
    Chemin de l'OU contenant les utilisateurs à traiter.

.EXAMPLE
    .\Add-ADUsersToGroups.ps1 -OuPath "OU=Utilisateurs,DC=domaine,DC=local"
#>
param (
    [Parameter(Mandatory=$true)]
    [string]$OuPath
)

# Définir le mapping utilisateurs/groupes
$usersGroups = @{
    "bmonteil" = @("Administrateurs", "Techniciens_IT")
    "adupont"  = @("Utilisateurs", "Stagiaires")
}

# Importer le module ActiveDirectory
Import-Module ActiveDirectory

# Récupérer les utilisateurs AD dans l'OU spécifiée
$users = Get-ADUser -Filter * -SearchBase $OuPath -Properties SamAccountName

foreach ($user in $users) {
    if ($usersGroups.ContainsKey($user.SamAccountName)) {
        foreach ($group in $usersGroups[$user.SamAccountName]) {
            Add-ADGroupMember -Identity $group -Members $user.SamAccountName -ErrorAction SilentlyContinue
            Write-Log "Utilisateur $($user.SamAccountName) ajouté au groupe $group"
        }
    }
}
```

---

#### **3.1.3. Script `Remove-InactiveADUsers.ps1`**
```powershell
<#
.SYNOPSIS
    Supprime les utilisateurs AD inactifs depuis un nombre de jours spécifié.

.DESCRIPTION
    Ce script identifie et supprime les utilisateurs AD inactifs depuis plus de X jours.

.PARAMETER OuPath
    Chemin de l'OU contenant les utilisateurs à traiter.

.PARAMETER DaysInactive
    Nombre de jours d'inactivité pour considérer un utilisateur comme inactif.

.EXAMPLE
    .\Remove-InactiveADUsers.ps1 -OuPath "OU=Utilisateurs,DC=domaine,DC=local" -DaysInactive 90
#>
param (
    [Parameter(Mandatory=$true)]
    [string]$OuPath,

    [Parameter(Mandatory=$true)]
    [int]$DaysInactive
)

# Importer le module ActiveDirectory
Import-Module ActiveDirectory

# Calculer la date seuil
$inactiveDate = (Get-Date).AddDays(-$DaysInactive)

# Récupérer les utilisateurs AD inactifs
$users = Get-ADUser -Filter {LastLogonDate -lt $inactiveDate -and Enabled -eq $true} -SearchBase $OuPath -Properties LastLogonDate, SamAccountName

foreach ($user in $users) {
    # Supprimer l'utilisateur
    Remove-ADUser -Identity $user.SamAccountName -Confirm:$false
    Write-Log "Utilisateur AD $($user.SamAccountName) supprimé (inactif depuis plus de $DaysInactive jours)"
}
```

---

### **3.2. Exécution du script principal**
**Exemples d’utilisation** :
```powershell
# Créer des utilisateurs AD
.\UserManagement.ps1 -Action Create -CsvPath "C:\Temp\utilisateurs_ad.csv"

# Ajouter des utilisateurs aux groupes
.\UserManagement.ps1 -Action AddToGroups -OuPath "OU=Utilisateurs,DC=domaine,DC=local"

# Réinitialiser les mots de passe
.\UserManagement.ps1 -Action ResetPasswords -OuPath "OU=Utilisateurs,DC=domaine,DC=local"

# Supprimer les utilisateurs inactifs
.\UserManagement.ps1 -Action RemoveInactive -OuPath "OU=Utilisateurs,DC=domaine,DC=local" -DaysInactive 90
```

---