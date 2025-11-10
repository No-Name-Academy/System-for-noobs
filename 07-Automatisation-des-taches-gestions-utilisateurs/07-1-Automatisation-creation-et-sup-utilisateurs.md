## **1. Automatisation de la création et de la suppression d’utilisateurs**

### **1.1. Scripts pour la création d’utilisateurs en masse**
#### **1.1.1. Utilisateurs locaux**
**Objectif** :
Créer un script qui lit un fichier CSV et crée des utilisateurs locaux avec des attributs personnalisés.

**Fichier CSV (`utilisateurs_locaux.csv`)** :
```csv
NomUtilisateur,NomComplet,Description,MotDePasse,GroupePrincipal
user1,Utilisateur Un,Compte technique,Pass123!,Utilisateurs
user2,Utilisateur Deux,Compte standard,Pass123!,Utilisateurs
admin1,Admin Un,Compte administrateur,Admin123!,Administrateurs
```

**Script : Création d’utilisateurs locaux depuis un CSV**
```powershell
<#
.SYNOPSIS
    Crée des utilisateurs locaux à partir d'un fichier CSV.

.DESCRIPTION
    Ce script lit un fichier CSV et crée des utilisateurs locaux avec les attributs spécifiés.
    Il ajoute également les utilisateurs aux groupes indiqués.

.PARAMETER CsvPath
    Chemin vers le fichier CSV contenant les informations des utilisateurs.

.EXAMPLE
    .\New-LocalUsersFromCsv.ps1 -CsvPath "C:\Temp\utilisateurs_locaux.csv"
#>
param (
    [Parameter(Mandatory=$true)]
    [string]$CsvPath
)

# Importer les utilisateurs depuis le CSV
$users = Import-Csv -Path $CsvPath

foreach ($user in $users) {
    # Créer le mot de passe sécurisé
    $securePassword = ConvertTo-SecureString $user.MotDePasse -AsPlainText -Force

    # Créer l'utilisateur
    New-LocalUser -Name $user.NomUtilisateur -FullName $user.NomComplet -Description $user.Description -Password $securePassword -UserMayNotChangePassword

    # Ajouter l'utilisateur au groupe principal
    Add-LocalGroupMember -Group $user.GroupePrincipal -Member $user.NomUtilisateur

    # Journaliser l'action
    Write-Host "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] Utilisateur $($user.NomUtilisateur) créé et ajouté au groupe $($user.GroupePrincipal)." -ForegroundColor Green
}
```

**Exécution** :
```powershell
.\New-LocalUsersFromCsv.ps1 -CsvPath "C:\Temp\utilisateurs_locaux.csv"
```

---

#### **1.1.2. Utilisateurs Active Directory**
**Objectif** :
Créer un script pour importer des utilisateurs AD depuis un CSV, avec gestion des OU et des groupes.

**Fichier CSV (`utilisateurs_ad.csv`)** :
```csv
Prenom,Nom,Identifiant,OU,Email,GroupePrincipal,GroupeSecondaire
Benoit,Monteil,bmonteil,OU=Utilisateurs,benoit.monteil@domaine.local,Techniciens_IT,
Alice,Dupont,adupont,OU=Utilisateurs,alice.dupont@domaine.local,Utilisateurs,Stagiaires
```

**Script : Création d’utilisateurs AD depuis un CSV**
```powershell
<#
.SYNOPSIS
    Crée des utilisateurs Active Directory à partir d'un fichier CSV.

.DESCRIPTION
    Ce script lit un fichier CSV et crée des utilisateurs AD avec les attributs spécifiés.
    Il ajoute les utilisateurs aux groupes principaux et secondaires.

.PARAMETER CsvPath
    Chemin vers le fichier CSV contenant les informations des utilisateurs.

.EXAMPLE
    .\New-ADUsersFromCsv.ps1 -CsvPath "C:\Temp\utilisateurs_ad.csv"
#>
param (
    [Parameter(Mandatory=$true)]
    [string]$CsvPath
)

# Importer le module ActiveDirectory
Import-Module ActiveDirectory

# Importer les utilisateurs depuis le CSV
$users = Import-Csv -Path $CsvPath

foreach ($user in $users) {
    # Créer le mot de passe sécurisé
    $securePassword = ConvertTo-SecureString "MotDePasse123!" -AsPlainText -Force

    # Créer l'utilisateur AD
    New-ADUser -Name "$($user.Prenom) $($user.Nom)" -SamAccountName $user.Identifiant -GivenName $user.Prenom -Surname $user.Nom -Enabled $true -AccountPassword $securePassword -ChangePasswordAtLogon $true -Path $user.OU -EmailAddress $user.Email

    # Ajouter l'utilisateur au groupe principal
    Add-ADGroupMember -Identity $user.GroupePrincipal -Members $user.Identifiant

    # Ajouter l'utilisateur aux groupes secondaires (si spécifiés)
    if ($user.GroupeSecondaire) {
        $secondaryGroups = $user.GroupeSecondaire -split ','
        foreach ($group in $secondaryGroups) {
            Add-ADGroupMember -Identity $group -Members $user.Identifiant
        }
    }

    # Journaliser l'action
    Write-Host "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] Utilisateur AD $($user.Identifiant) créé et ajouté aux groupes $($user.GroupePrincipal), $($user.GroupeSecondaire)." -ForegroundColor Green
}
```

**Exécution** :
```powershell
.\New-ADUsersFromCsv.ps1 -CsvPath "C:\Temp\utilisateurs_ad.csv"
```

---

### **1.2. Scripts pour la suppression d’utilisateurs**
#### **1.2.1. Suppression d’utilisateurs locaux inactifs**
**Objectif** :
Supprimer les utilisateurs locaux qui ne se sont pas connectés depuis 30 jours.

**Script : Suppression des utilisateurs locaux inactifs**
```powershell
<#
.SYNOPSIS
    Supprime les utilisateurs locaux inactifs depuis 30 jours.

.DESCRIPTION
    Ce script identifie les utilisateurs locaux dont le dernier login remonte à plus de 30 jours et les supprime.

.PARAMETER DaysInactive
    Nombre de jours d'inactivité pour considérer un utilisateur comme inactif.

.EXAMPLE
    .\Remove-InactiveLocalUsers.ps1 -DaysInactive 30
#>
param (
    [int]$DaysInactive = 30
)

# Calculer la date seuil
$inactiveDate = (Get-Date).AddDays(-$DaysInactive)

# Récupérer les utilisateurs locaux
$users = Get-LocalUser | Where-Object { $_.LastLogon -lt $inactiveDate -and $_.Enabled -eq $true }

foreach ($user in $users) {
    # Supprimer l'utilisateur
    Remove-LocalUser -Name $user.Name -ErrorAction SilentlyContinue

    # Journaliser l'action
    Write-Host "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] Utilisateur $($user.Name) supprimé (inactif depuis plus de $DaysInactive jours)." -ForegroundColor Red
}
```

---

#### **1.2.2. Suppression d’utilisateurs AD désactivés**
**Objectif** :
Supprimer les utilisateurs AD désactivés depuis plus de 90 jours.

**Script : Suppression des utilisateurs AD désactivés**
```powershell
<#
.SYNOPSIS
    Supprime les utilisateurs AD désactivés depuis plus de 90 jours.

.DESCRIPTION
    Ce script identifie les utilisateurs AD désactivés depuis plus de 90 jours et les supprime.

.PARAMETER DaysDisabled
    Nombre de jours depuis la désactivation pour supprimer l'utilisateur.

.EXAMPLE
    .\Remove-DisabledADUsers.ps1 -DaysDisabled 90
#>
param (
    [int]$DaysDisabled = 90
)

# Importer le module ActiveDirectory
Import-Module ActiveDirectory

# Calculer la date seuil
$disabledDate = (Get-Date).AddDays(-$DaysDisabled)

# Récupérer les utilisateurs AD désactivés
$users = Get-ADUser -Filter {Enabled -eq $false -and whenChanged -lt $disabledDate} -Properties whenChanged

foreach ($user in $users) {
    # Supprimer l'utilisateur
    Remove-ADUser -Identity $user.SamAccountName -Confirm:$false

    # Journaliser l'action
    Write-Host "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] Utilisateur AD $($user.SamAccountName) supprimé (désactivé depuis plus de $DaysDisabled jours)." -ForegroundColor Red
}
```

---