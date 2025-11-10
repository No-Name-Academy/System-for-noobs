## **3. Travaux pratiques : Écrire un script pour une tâche d’administration complète**

---
### **Énoncé du projet** :
**Objectif** :
Créer un script qui :
1. **Importe une liste d’utilisateurs** depuis un fichier CSV (`C:\Temp\NewUsers.csv`).
2. **Crée les utilisateurs Active Directory** avec un mot de passe par défaut.
3. **Ajoute les utilisateurs à des groupes** (principal et secondaire).
4. **Configure les permissions** sur des dossiers partagés (ex: `C:\Projets\*`).
5. **Surveille et redémarre les services critiques** (`Spooler`, `DHCP`).
6. **Journalise toutes les actions** dans `C:\Logs\AdminTask.log`.
7. **Gère les erreurs** (ex: utilisateur déjà existant, dossier introuvable).

**Fichier CSV (`NewUsers.csv`)** :
```csv
Prenom,Nom,Identifiant,OU,Email,GroupePrincipal,GroupeSecondaire,DossierAcces,Permission
Pierre,Durand,pdurand,OU=Utilisateurs,pierre.durand@domaine.local,Developpement,Projets,C:\Projets\Dev,Modify
Sophie,Lambert,slambert,OU=Utilisateurs,sophie.lambert@domaine.local,Marketing,,C:\Projets\Marketing,Read
```

---
### **Corrigé** :
```powershell
# =============================================
# SCRIPT : AUTOMATISATION DE LA GESTION DES UTILISATEURS
# =============================================

# 1. Fonction de journalisation
function Write-Log {
    param (
        [string]$message,
        [ValidateSet("INFO", "WARNING", "ERROR")]
        [string]$level = "INFO",
        [string]$logFile = "C:\Logs\AdminTask_$(Get-Date -Format 'yyyyMMdd').log"
    )

    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $logEntry = "[$timestamp] [$level] $message"

    # Créer le dossier si nécessaire
    if (-not (Test-Path -Path (Split-Path -Path $logFile -Parent))) {
        New-Item -ItemType Directory -Path (Split-Path -Path $logFile -Parent) -Force | Out-Null
    }

    Add-Content -Path $logFile -Value $logEntry

    switch ($level) {
        "ERROR"   { Write-Host $logEntry -ForegroundColor Red }
        "WARNING" { Write-Host $logEntry -ForegroundColor Yellow }
        default   { Write-Host $logEntry }
    }
}

# 2. Importer les données utilisateurs
function Import-UserData {
    param ([string]$csvPath)

    if (-not (Test-Path -Path $csvPath)) {
        Write-Log -message "Fichier introuvable : $csvPath" -level "ERROR"
        return $null
    }

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

# 3. Créer les utilisateurs AD
function Create-ADUsers {
    param (
        [array]$users,
        [string]$defaultPassword = "MotDePasse123!"
    )

    $createdUsers = @()
    foreach ($user in $users) {
        try {
            $existingUser = Get-ADUser -Filter { SamAccountName -eq $user.Identifiant } -ErrorAction SilentlyContinue
            if ($existingUser) {
                Write-Log -message "L'utilisateur $($user.Identifiant) existe déjà." -level "WARNING"
                continue
            }

            $securePassword = ConvertTo-SecureString $defaultPassword -AsPlainText -Force
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

# 4. Ajouter les utilisateurs aux groupes
function Add-UsersToGroups {
    param ([array]$users)

    foreach ($user in $users) {
        try {
            if ($user.GroupePrincipal) {
                Add-ADGroupMember -Identity $user.GroupePrincipal -Members $user.Identifiant -ErrorAction Stop
                Write-Log -message "$($user.Identifiant) ajouté au groupe $($user.GroupePrincipal)."
            }

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

# 5. Configurer les permissions sur les dossiers
function Set-FolderPermissions {
    param ([array]$users)

    foreach ($user in $users) {
        try {
            if ($user.DossierAcces -and (Test-Path -Path $user.DossierAcces)) {
                $acl = Get-Acl -Path $user.DossierAcces

                $permissionLevel = switch ($user.Permission) {
                    "Read"       { "ReadAndExecute" }
                    "Modify"     { "Modify" }
                    "FullControl"{ "FullControl" }
                    default      { "ReadAndExecute" }
                }

                $rule = New-Object System.Security.AccessControl.FileSystemAccessRule(
                    "$($user.Identifiant)",
                    $permissionLevel,
                    "ContainerInherit, ObjectInherit",
                    "None",
                    "Allow"
                )

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

# 6. Surveiller les services critiques
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

# =============================================
# EXÉCUTION DU SCRIPT
# =============================================

# Chemin du fichier CSV
$csvPath = "C:\Temp\NewUsers.csv"

# 1. Importer les données
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

# 3. Ajouter aux groupes
Add-UsersToGroups -users $users

# 4. Configurer les permissions
Set-FolderPermissions -users $users

# 5. Surveiller les services
Monitor-CriticalServices

# 6. Fin du script
Write-Log -message "=== Script terminé avec succès ==="
```

---
### **Explications détaillées** :
1. **Modularité** :
   - Chaque fonction a un **rôle spécifique** (ex: `Create-ADUsers`, `Set-FolderPermissions`).
   - **Réutilisable** : Les fonctions peuvent être appelées indépendamment.

2. **Gestion des erreurs** :
   - **`try/catch`** : Capture les erreurs et les journalise.
   - **Vérifications** : Existence des utilisateurs, dossiers, services.

3. **Journalisation** :
   - **Niveaux de gravité** (`INFO`, `WARNING`, `ERROR`).
   - **Couleurs** dans la console pour une lecture facile.

4. **Automatisation complète** :
   - De la **création des utilisateurs** à la **configuration des permissions**.
   - **Surveillance des services** pour garantir leur disponibilité.

5. **Extensibilité** :
   - Ajoutez d’autres **fonctions** (ex: suppression d’utilisateurs, sauvegarde des logs).
   - **Paramétrez** le script pour adapter les chemins, mots de passe, etc.

---
### **Exemple de sortie dans le log** :
```
[2025-11-15 14:30:45] [INFO] Importation réussie de 2 utilisateurs depuis C:\Temp\NewUsers.csv
[2025-11-15 14:30:46] [INFO] Utilisateur pdurand créé avec succès.
[2025-11-15 14:30:47] [INFO] pdurand ajouté au groupe Developpement.
[2025-11-15 14:30:48] [INFO] Permissions Modify accordées à pdurand sur C:\Projets\Dev.
[2025-11-15 14:30:49] [INFO] Service Spooler est en cours d'exécution.
[2025-11-15 14:30:50] [INFO] === Script terminé avec succès ===
```

---

