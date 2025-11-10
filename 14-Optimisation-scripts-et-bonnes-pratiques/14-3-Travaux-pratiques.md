## **3. Travaux pratiques : Améliorer un script existant**

---
### **Énoncé du projet** :
**Objectif** :
Améliorez le script suivant en ajoutant :
1. **Documentation complète** (bloc `.SYNOPSIS`, `.DESCRIPTION`, `.EXAMPLE`).
2. **Gestion des erreurs** (`try/catch`).
3. **Logging avancé** (niveaux `INFO`, `WARNING`, `ERROR`).
4. **Paramètres flexibles** :
   - Chemin du fichier CSV.
   - Mot de passe par défaut.
   - Niveau de log.
5. **Modularité** :
   - Séparer la logique dans des fonctions.
   - Utiliser un module de logging.

**Script de base** :
```powershell
$users = Import-Csv -Path "C:\Temp\Users.csv"
foreach ($user in $users) {
    New-ADUser -Name "$($user.Prenom) $($user.Nom)" -SamAccountName $user.Identifiant
}
```

---
### **Corrigé** :
**Structure des fichiers** :
```
Project/
├── Modules/
│   ├── LoggingModule.psm1
│   └── UserManagement.psm1
└── Main.ps1
```

**1. `LoggingModule.psm1`** (voir section 2.2.1).

**2. `UserManagement.psm1`** (voir section 2.4).

**3. `Main.ps1`** :
```powershell
<#
.SYNOPSIS
    Script principal pour la gestion des utilisateurs Active Directory.

.DESCRIPTION
    Ce script utilise les modules LoggingModule et UserManagement
    pour créer des utilisateurs AD à partir d'un fichier CSV.

.PARAMETER CsvPath
    Chemin vers le fichier CSV contenant les utilisateurs.

.PARAMETER DefaultPassword
    Mot de passe par défaut pour les nouveaux utilisateurs.

.PARAMETER LogLevel
    Niveau de détail des logs (INFO, WARNING, ERROR).

.EXAMPLE
    .\Main.ps1 -CsvPath "C:\Temp\Users.csv" -DefaultPassword "MotDePasse123!" -LogLevel "INFO"
#>
param (
    [Parameter(Mandatory=$true)]
    [ValidateScript({ Test-Path -Path $_ -PathType Leaf })]
    [string]$CsvPath,

    [string]$DefaultPassword = "MotDePasse123!",

    [ValidateSet("INFO", "WARNING", "ERROR")]
    [string]$LogLevel = "INFO"
)

# Importer les modules
Import-Module .\Modules\LoggingModule.psm1 -Force
Import-Module .\Modules\UserManagement.psm1 -Force

# Exécuter la création des utilisateurs
New-ADUserFromCsv -CsvPath $CsvPath -DefaultPassword $DefaultPassword
```

---
### **Explications détaillées** :
1. **Documentation** :
   - Chaque script et fonction a un **bloc de commentaires** complet.
   - **Exemples d’utilisation** pour faciliter la prise en main.

2. **Modularité** :
   - **`LoggingModule.psm1`** : Gère les logs de manière centralisée.
   - **`UserManagement.psm1`** : Contient la logique métier (création d’utilisateurs).
   - **`Main.ps1`** : Point d’entrée du script, appelle les modules.

3. **Gestion des erreurs** :
   - **`try/catch`** dans chaque fonction pour capturer les erreurs.
   - **Logs détaillés** pour le débogage.

4. **Paramètres flexibles** :
   - **`CsvPath`** : Chemin personnalisable du fichier CSV.
   - **`DefaultPassword`** : Mot de passe configurable.
   - **`LogLevel`** : Contrôle le niveau de détail des logs.

5. **Réutilisabilité** :
   - Les modules peuvent être **réutilisés** dans d’autres scripts.
   - **Facile à maintenir** : Chaque composant est isolé.

---

