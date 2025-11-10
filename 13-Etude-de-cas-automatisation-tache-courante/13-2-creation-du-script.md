## **2. Création du script**

### **2.1. Préparation de l’environnement**
#### **2.1.1. Fichier CSV d’entrée (`Users.csv`)**
```csv
Prenom,Nom,Identifiant,OU,Email,GroupePrincipal,GroupeSecondaire,DossierAcces,Permission
Jean,Dupont,jdupont,OU=Utilisateurs,jean.dupont@domaine.local,Developpement,Projets,C:\Projets\Dev,Modify
Marie,Martin,mmartin,OU=Utilisateurs,marie.martin@domaine.local,Marketing,Projets,C:\Projets\Marketing,Read
```
**Explications** :
- **`DossierAcces`** : Dossier pour lequel configurer les permissions.
- **`Permission`** : Niveau de permission (`Read`, `Modify`, `FullControl`).

---

#### **2.1.2. Module ActiveDirectory**
```powershell
# Vérifier et importer le module ActiveDirectory
if (-not (Get-Module -Name ActiveDirectory -ListAvailable)) {
    Write-Host "Le module ActiveDirectory est requis. Installez-le via RSAT ou un contrôleur de domaine." -ForegroundColor Red
    exit
}
Import-Module ActiveDirectory
```

---

### **2.2. Fonction de journalisation (`Write-Log`)**
```powershell
function Write-Log {
    param (
        [string]$message,
        [ValidateSet("INFO", "WARNING", "ERROR")]
        [string]$level = "INFO",
        [string]$logFile = "C:\Logs\AdminTask_$(Get-Date -Format 'yyyyMMdd').log"
    )

    # Créer le dossier de logs s'il n'existe pas
    if (-not (Test-Path -Path (Split-Path -Path $logFile -Parent))) {
        New-Item -ItemType Directory -Path (Split-Path -Path $logFile -Parent) -Force | Out-Null
    }

    # Formater le message
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $logEntry = "[$timestamp] [$level] $message"

    # Écrire dans le fichier
    Add-Content -Path $logFile -Value $logEntry

    # Afficher dans la console avec couleur
    switch ($level) {
        "ERROR"   { Write-Host $logEntry -ForegroundColor Red }
        "WARNING" { Write-Host $logEntry -ForegroundColor Yellow }
        default   { Write-Host $logEntry }
    }
}
```
**Explications** :
- **`ValidateSet`** : Restreint `$level` aux valeurs `INFO`, `WARNING`, ou `ERROR`.
- **Couleurs** : Affiche les messages dans la console avec une couleur adaptée au niveau.

---

### **2.3. Importation des données utilisateurs (`Import-UserData`)**
```powershell
function Import-UserData {
    param (
        [Parameter(Mandatory=$true)]
        [string]$csvPath
    )

    # Vérifier que le fichier existe
    if (-not (Test-Path -Path $csvPath)) {
        Write-Log -message "Fichier introuvable : $csvPath" -level "ERROR"
        return $null
    }

    # Importer le CSV
    try {
        $users = Import-Csv -Path $csvPath
        Write-Log -message "Importation réussie de $($users.Count) utilisateurs depuis $csvPath"
        return $users
    }
    catch {
        Write-Log -message "Erreur lors de l'import du CSV : $_" -level "ERROR"
        return $null
    }
}
```
**Explications** :
- **`Test-Path`** : Vérifie si le fichier CSV existe.
- **`Import-Csv`** : Charge les données du CSV dans un tableau d’objets.

---

### **2.4. Création des utilisateurs AD (`Create-ADUsers`)**
```powershell
function Create-ADUsers {
    param (
        [Parameter(Mandatory=$true)]
        [array]$users,

        [string]$defaultPassword = "MotDePasse123!"
    )

    $createdUsers = @()
    foreach ($user in $users) {
        try {
            # Vérifier si l'utilisateur existe déjà
            $existingUser = Get-ADUser -Filter { SamAccountName -eq $user.Identifiant } -ErrorAction SilentlyContinue
            if ($existingUser) {
                Write-Log -message "L'utilisateur $($user.Identifiant) existe déjà." -level "WARNING"
                continue
            }

            # Créer le mot de passe sécurisé
            $securePassword = ConvertTo-SecureString $defaultPassword -AsPlainText -Force

            # Créer l'utilisateur
            New-ADUser -Name "$($user.Prenom) $($user.Nom)" `
                       -SamAccountName $user.Identifiant `
                       -GivenName $user.Prenom `
                       -Surname $user.Nom `
                       -Enabled $true `
                       -AccountPassword $securePassword `
                       -ChangePasswordAtLogon $true `
                       -Path $user.OU `
                       -EmailAddress $user.Email `
                       -ErrorAction Stop

            Write-Log -message "Utilisateur $($user.Identifiant) créé avec succès."
            $createdUsers += $user.Identifiant
        }
        catch {
            Write-Log -message "Erreur lors de la création de $($user.Identifiant) : $_" -level "ERROR"
        }
    }

    return $createdUsers
}
```
**Explications** :
- **`Get-ADUser`** : Vérifie si l’utilisateur existe déjà.
- **`New-ADUser`** : Crée l’utilisateur avec les attributs spécifiés.
- **`ChangePasswordAtLogon`** : Force l’utilisateur à changer son mot de passe à la première connexion.

---

### **2.5. Ajout des utilisateurs aux groupes (`Add-UsersToGroups`)**
```powershell
function Add-UsersToGroups {
    param (
        [Parameter(Mandatory=$true)]
        [array]$users
    )

    foreach ($user in $users) {
        try {
            # Ajouter au groupe principal
            if ($user.GroupePrincipal) {
                Add-ADGroupMember -Identity $user.GroupePrincipal -Members $user.Identifiant -ErrorAction Stop
                Write-Log -message "$($user.Identifiant) ajouté au groupe $($user.GroupePrincipal)."
            }

            # Ajouter aux groupes secondaires
            if ($user.GroupeSecondaire) {
                $secondaryGroups = $user.GroupeSecondaire -split ','
                foreach ($group in $secondaryGroups) {
                    Add-ADGroupMember -Identity $group -Members $user.Identifiant -ErrorAction Stop
                    Write-Log -message "$($user.Identifiant) ajouté au groupe secondaire $group."
                }
            }
        }
        catch {
            Write-Log -message "Erreur lors de l'ajout de $($user.Identifiant) aux groupes : $_" -level "ERROR"
        }
    }
}
```
**Explications** :
- **`Add-ADGroupMember`** : Ajoute un utilisateur à un groupe AD.
- **`-split ','`** : Sépare les groupes secondaires (si plusieurs).

---

### **2.6. Configuration des permissions sur les dossiers (`Set-FolderPermissions`)**
```powershell
function Set-FolderPermissions {
    param (
        [Parameter(Mandatory=$true)]
        [array]$users
    )

    foreach ($user in $users) {
        try {
            if ($user.DossierAcces -and (Test-Path -Path $user.DossierAcces)) {
                # Récupérer l'ACL actuelle
                $acl = Get-Acl -Path $user.DossierAcces

                # Définir la règle de permission
                $permissionLevel = switch ($user.Permission) {
                    "Read"       { "ReadAndExecute" }
                    "Modify"     { "Modify" }
                    "FullControl"{ "FullControl" }
                    default      { "ReadAndExecute" }
                }

                # Créer la règle
                $rule = New-Object System.Security.AccessControl.FileSystemAccessRule(
                    "$($user.Identifiant)",
                    $permissionLevel,
                    "ContainerInherit, ObjectInherit",
                    "None",
                    "Allow"
                )

                # Appliquer la règle
                $acl.AddAccessRule($rule)
                Set-Acl -Path $user.DossierAcces -AclObject $acl

                Write-Log -message "Permissions $permissionLevel accordées à $($user.Identifiant) sur $($user.DossierAcces)."
            } else {
                Write-Log -message "Dossier $($user.DossierAcces) introuvable pour $($user.Identifiant)." -level "WARNING"
            }
        }
        catch {
            Write-Log -message "Erreur lors de la configuration des permissions pour $($user.Identifiant) : $_" -level "ERROR"
        }
    }
}
```
**Explications** :
- **`Get-Acl` / `Set-Acl`** : Récupère et applique les permissions sur un dossier.
- **`FileSystemAccessRule`** : Définit les droits d’accès (ex: `ReadAndExecute`, `Modify`).
- **`ContainerInherit`** : Applique les permissions aux sous-dossiers.

---

### **2.7. Surveillance des services critiques (`Monitor-CriticalServices`)**
```powershell
function Monitor-CriticalServices {
    param (
        [array]$services = @("Spooler", "DHCP", "DNS")
    )

    foreach ($service in $services) {
        try {
            $serviceObj = Get-Service -Name $service -ErrorAction Stop
            if ($serviceObj.Status -ne "Running") {
                Start-Service -Name $service -ErrorAction Stop
                Write-Log -message "Service $service redémarré (statut précédent : $($serviceObj.Status))."
            } else {
                Write-Log -message "Service $service est en cours d'exécution."
            }
        }
        catch {
            Write-Log -message "Erreur lors de la surveillance du service $service : $_" -level "ERROR"
        }
    }
}
```
**Explications** :
- **`Get-Service`** : Récupère l’état du service.
- **`Start-Service`** : Redémarre le service s’il est arrêté.

---

### **2.8. Script principal (`Main`)**
```powershell
# =============================================
# SCRIPT PRINCIPAL
# =============================================

# Chemin du fichier CSV
$csvPath = "C:\Temp\Users.csv"

# 1. Importer les données utilisateurs
$users = Import-UserData -csvPath $csvPath
if (-not $users) {
    Write-Log -message "Aucun utilisateur à traiter. Arrêt du script." -level "ERROR"
    exit
}

# 2. Créer les utilisateurs AD
$createdUsers = Create-ADUsers -users $users
if (-not $createdUsers) {
    Write-Log -message "Aucun utilisateur créé. Vérifiez les erreurs." -level "WARNING"
}

# 3. Ajouter les utilisateurs aux groupes
Add-UsersToGroups -users $users

# 4. Configurer les permissions sur les dossiers
Set-FolderPermissions -users $users

# 5. Surveiller les services critiques
Monitor-CriticalServices

# 6. Fin du script
Write-Log -message "=== Script terminé avec succès ==="
```

---

