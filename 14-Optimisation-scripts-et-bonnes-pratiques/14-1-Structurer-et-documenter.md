## **1. Structurer et documenter un script**

### **1.1. Structure d’un script PowerShell professionnel**
Un script bien structuré doit inclure :
1. **En-tête de documentation** (bloc de commentaires).
2. **Déclaration des paramètres** (`param`).
3. **Fonctions modulaires** (une tâche = une fonction).
4. **Gestion des erreurs** (`try/catch`).
5. **Logging et reporting**.
6. **Exemples d’utilisation**.

---
### **1.2. Documentation avec les blocs de commentaires**
#### **1.2.1. Bloc `.SYNOPSIS` et `.DESCRIPTION`**
```powershell
<#
.SYNOPSIS
    Crée et configure des utilisateurs Active Directory à partir d'un fichier CSV.

.DESCRIPTION
    Ce script importe une liste d'utilisateurs depuis un fichier CSV,
    les crée dans Active Directory, les ajoute à des groupes,
    et configure leurs permissions sur des dossiers partagés.
    Il inclut une journalisation détaillée et une gestion des erreurs.

.PARAMETER CsvPath
    Chemin vers le fichier CSV contenant les données des utilisateurs.

.PARAMETER DefaultPassword
    Mot de passe par défaut pour les nouveaux utilisateurs (par défaut : "MotDePasse123!").

.EXAMPLE
    .\New-ADUsersFromCsv.ps1 -CsvPath "C:\Temp\Users.csv" -DefaultPassword "MonMotDePasse123!"

.EXAMPLE
    # Utilisation avec les paramètres par défaut
    .\New-ADUsersFromCsv.ps1 -CsvPath "C:\Temp\Users.csv"
#>
```
**Explications** :
- **`.SYNOPSIS`** : Résumé court du script.
- **`.DESCRIPTION`** : Description détaillée.
- **`.PARAMETER`** : Explication de chaque paramètre.
- **`.EXAMPLE`** : Exemples d’utilisation.

---
#### **1.2.2. Documentation des fonctions**
```powershell
function Create-ADUser {
    <#
    .SYNOPSIS
        Crée un utilisateur Active Directory.

    .DESCRIPTION
        Crée un utilisateur AD avec les attributs spécifiés (nom, prénom, OU, etc.).
        Gère les erreurs si l'utilisateur existe déjà.

    .PARAMETER UserData
        Objet contenant les données de l'utilisateur (Prenom, Nom, Identifiant, etc.).

    .PARAMETER DefaultPassword
        Mot de passe par défaut pour le nouvel utilisateur.

    .EXAMPLE
        Create-ADUser -UserData $user -DefaultPassword "MotDePasse123!"
    #>
    param (
        [Parameter(Mandatory=$true)]
        [object]$UserData,

        [string]$DefaultPassword = "MotDePasse123!"
    )

    # ... (code de la fonction)
}
```
**Explications** :
- Chaque fonction doit avoir sa propre **documentation**.
- Utilisez **`.PARAMETER`** pour décrire les paramètres.

---
### **1.3. Utilisation des paramètres avancés**
#### **1.3.1. Paramètres obligatoires et optionnels**
```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]$CsvPath,

    [string]$DefaultPassword = "MotDePasse123!",

    [ValidateSet("INFO", "WARNING", "ERROR")]
    [string]$LogLevel = "INFO",

    [switch]$WhatIf
)
```
**Explications** :
- **`Mandatory=$true`** : Rend le paramètre obligatoire.
- **`ValidateSet`** : Restreint les valeurs possibles (`INFO`, `WARNING`, `ERROR`).
- **`[switch]$WhatIf`** : Permet un mode "simulation" (`-WhatIf`).

---
#### **1.3.2. Paramètres de type `ValidateScript`**
```powershell
param (
    [Parameter(Mandatory=$true)]
    [ValidateScript({ Test-Path -Path $_ -PathType Leaf })]
    [string]$CsvPath
)
```
**Explications** :
- **`ValidateScript`** : Valide que le fichier CSV existe avant l’exécution.

---
### **1.4. Travaux pratiques : Documenter un script existant**
---
#### **Énoncé de l’exercice** :
**Objectif** :
1. Prenez le script suivant (simplifié) et **ajoutez une documentation complète** :
   - Bloc `.SYNOPSIS` et `.DESCRIPTION`.
   - Documentation pour chaque fonction.
   - Exemples d’utilisation.
2. Ajoutez un **paramètre `LogLevel`** pour contrôler le niveau de détail des logs.

**Script à documenter** :
```powershell
function Create-User {
    param ($userData)
    New-ADUser -Name "$($userData.Prenom) $($userData.Nom)" -SamAccountName $userData.Identifiant
}

$users = Import-Csv -Path "C:\Temp\Users.csv"
foreach ($user in $users) {
    Create-User -userData $user
}
```

---
#### **Corrigé** :
```powershell
<#
.SYNOPSIS
    Crée des utilisateurs Active Directory à partir d'un fichier CSV.

.DESCRIPTION
    Ce script importe une liste d'utilisateurs depuis un fichier CSV
    et les crée dans Active Directory.

.PARAMETER CsvPath
    Chemin vers le fichier CSV contenant les données des utilisateurs.

.PARAMETER LogLevel
    Niveau de détail des logs (INFO, WARNING, ERROR). Par défaut : INFO.

.EXAMPLE
    .\Create-Users.ps1 -CsvPath "C:\Temp\Users.csv"

.EXAMPLE
    .\Create-Users.ps1 -CsvPath "C:\Temp\Users.csv" -LogLevel "WARNING"
#>
param (
    [Parameter(Mandatory=$true)]
    [ValidateScript({ Test-Path -Path $_ -PathType Leaf })]
    [string]$CsvPath,

    [ValidateSet("INFO", "WARNING", "ERROR")]
    [string]$LogLevel = "INFO"
)

function Write-Log {
    param (
        [string]$message,
        [string]$level
    )

    if ($level -ge $LogLevel) {
        Write-Host "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] [$level] $message"
    }
}

function Create-User {
    <#
    .SYNOPSIS
        Crée un utilisateur Active Directory.

    .DESCRIPTION
        Crée un utilisateur AD avec les attributs spécifiés dans $userData.

    .PARAMETER UserData
        Objet contenant les données de l'utilisateur (Prenom, Nom, Identifiant, etc.).

    .EXAMPLE
        Create-User -UserData $user
    #>
    param (
        [Parameter(Mandatory=$true)]
        [object]$UserData
    )

    try {
        New-ADUser -Name "$($UserData.Prenom) $($UserData.Nom)" -SamAccountName $UserData.Identifiant -ErrorAction Stop
        Write-Log -message "Utilisateur $($UserData.Identifiant) créé avec succès." -level "INFO"
    }
    catch {
        Write-Log -message "Erreur lors de la création de $($UserData.Identifiant) : $_" -level "ERROR"
    }
}

# Importation des utilisateurs
try {
    $users = Import-Csv -Path $CsvPath -ErrorAction Stop
    Write-Log -message "Importation de $($users.Count) utilisateurs depuis $CsvPath." -level "INFO"
}
catch {
    Write-Log -message "Erreur lors de l'import du CSV : $_" -level "ERROR"
    exit
}

# Création des utilisateurs
foreach ($user in $users) {
    Create-User -UserData $user
}

Write-Log -message "Script terminé." -level "INFO"
```

---
