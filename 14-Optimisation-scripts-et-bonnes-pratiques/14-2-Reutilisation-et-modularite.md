## **2. Réutilisation et modularité des scripts**

### **2.1. Principes de modularité**
- **Une fonction = une tâche** : Chaque fonction doit faire une seule chose.
- **Paramètres flexibles** : Permettent d’adapter le comportement.
- **Retour de valeurs** : Utilisez `return` pour renvoyer des résultats.
- **Réutilisabilité** : Écrivez des fonctions génériques (ex: `Write-Log` peut être utilisée dans n’importe quel script).

---
### **2.2. Exemple : Module de logging réutilisable**
#### **2.2.1. Script : `LoggingModule.psm1`**
```powershell
<#
.MODULE
    Name: LoggingModule
    Description: Module pour la journalisation des scripts PowerShell.
#>

function Write-Log {
    <#
    .SYNOPSIS
        Écrit un message dans un fichier de log avec un niveau de gravité.

    .PARAMETER Message
        Message à journaliser.

    .PARAMETER Level
        Niveau de gravité (INFO, WARNING, ERROR). Par défaut : INFO.

    .PARAMETER LogFile
        Chemin du fichier de log. Par défaut : C:\Logs\ScriptLog.log.

    .EXAMPLE
        Write-Log -Message "Début du script." -Level "INFO"
    #>
    param (
        [Parameter(Mandatory=$true)]
        [string]$Message,

        [ValidateSet("INFO", "WARNING", "ERROR")]
        [string]$Level = "INFO",

        [string]$LogFile = "C:\Logs\ScriptLog_$(Get-Date -Format 'yyyyMMdd').log"
    )

    # Créer le dossier si nécessaire
    if (-not (Test-Path -Path (Split-Path -Path $LogFile -Parent))) {
        New-Item -ItemType Directory -Path (Split-Path -Path $LogFile -Parent) -Force | Out-Null
    }

    # Formater le message
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $logEntry = "[$timestamp] [$Level] $Message"

    # Écrire dans le fichier
    Add-Content -Path $LogFile -Value $logEntry

    # Afficher dans la console avec couleur
    switch ($Level) {
        "ERROR"   { Write-Host $logEntry -ForegroundColor Red }
        "WARNING" { Write-Host $logEntry -ForegroundColor Yellow }
        default   { Write-Host $logEntry }
    }
}

Export-ModuleMember -Function Write-Log
```

---
#### **2.2.2. Utilisation du module**
```powershell
# Importer le module
Import-Module .\LoggingModule.psm1 -Force

# Utiliser la fonction Write-Log
Write-Log -Message "Début du script." -Level "INFO"
Write-Log -Message "Attention, espace disque faible!" -Level "WARNING"
```

---
### **2.3. Gestion des dépendances**
#### **2.3.1. Vérification des modules requis**
```powershell
# Vérifier si le module ActiveDirectory est disponible
if (-not (Get-Module -Name ActiveDirectory -ListAvailable)) {
    Write-Log -Message "Le module ActiveDirectory est requis. Installez-le via RSAT." -Level "ERROR"
    exit
}
Import-Module ActiveDirectory
```

---
#### **2.3.2. Installation automatique des modules manquants**
```powershell
# Installer un module si nécessaire (ex: ImportExcel)
$requiredModule = "ImportExcel"
if (-not (Get-Module -Name $requiredModule -ListAvailable)) {
    Write-Log -Message "Installation du module $requiredModule..." -Level "INFO"
    Install-Module -Name $requiredModule -Force -Scope CurrentUser
}
Import-Module $requiredModule
```

---
### **2.4. Travaux pratiques : Modulariser un script existant**
---
#### **Énoncé de l’exercice** :
**Objectif** :
1. Prenez le script suivant et **modularisez-le** :
   - Créez un **module** pour la gestion des logs (`LoggingModule.psm1`).
   - Créez une **fonction** pour la création des utilisateurs (`New-ADUserFromCsv`).
   - Utilisez des **paramètres** pour rendre le script flexible.
2. Ajoutez une **gestion des erreurs** avec `try/catch`.

**Script à modulariser** :
```powershell
$users = Import-Csv -Path "C:\Temp\Users.csv"
foreach ($user in $users) {
    New-ADUser -Name "$($user.Prenom) $($user.Nom)" -SamAccountName $user.Identifiant
}
```

---
#### **Corrigé** :
**Fichier : `LoggingModule.psm1`** (voir section 2.2.1).

**Fichier : `UserManagement.psm1`**
```powershell
<#
.MODULE
    Name: UserManagement
    Description: Module pour la gestion des utilisateurs Active Directory.
#>

function New-ADUserFromCsv {
    <#
    .SYNOPSIS
        Crée des utilisateurs AD à partir d'un fichier CSV.

    .PARAMETER CsvPath
        Chemin vers le fichier CSV.

    .PARAMETER DefaultPassword
        Mot de passe par défaut pour les nouveaux utilisateurs.

    .EXAMPLE
        New-ADUserFromCsv -CsvPath "C:\Temp\Users.csv" -DefaultPassword "MotDePasse123!"
    #>
    param (
        [Parameter(Mandatory=$true)]
        [ValidateScript({ Test-Path -Path $_ -PathType Leaf })]
        [string]$CsvPath,

        [string]$DefaultPassword = "MotDePasse123!"
    )

    try {
        $users = Import-Csv -Path $CsvPath -ErrorAction Stop
        Write-Log -Message "Importation de $($users.Count) utilisateurs depuis $CsvPath." -Level "INFO"

        foreach ($user in $users) {
            try {
                $securePassword = ConvertTo-SecureString $DefaultPassword -AsPlainText -Force
                New-ADUser -Name "$($user.Prenom) $($user.Nom)" `
                           -SamAccountName $user.Identifiant `
                           -AccountPassword $securePassword `
                           -ChangePasswordAtLogon $true `
                           -ErrorAction Stop
                Write-Log -Message "Utilisateur $($user.Identifiant) créé avec succès." -Level "INFO"
            }
            catch {
                Write-Log -Message "Erreur lors de la création de $($user.Identifiant) : $_" -Level "ERROR"
            }
        }
    }
    catch {
        Write-Log -Message "Erreur lors de l'import du CSV : $_" -Level "ERROR"
    }
}

Export-ModuleMember -Function New-ADUserFromCsv
```

**Script principal : `Main.ps1`**
```powershell
# Importer les modules
Import-Module .\LoggingModule.psm1 -Force
Import-Module .\UserManagement.psm1 -Force

# Exécuter la création des utilisateurs
New-ADUserFromCsv -CsvPath "C:\Temp\Users.csv" -DefaultPassword "MotDePasse123!"
```

---

