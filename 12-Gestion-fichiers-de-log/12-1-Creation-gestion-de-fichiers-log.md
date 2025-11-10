## **1. Création et gestion de fichiers de log**

### **1.1. Principes de base des fichiers de log**
Un **fichier de log** est un enregistrement chronologique des événements générés par un script ou un système. Il permet de :
- **Suivre l’exécution** des scripts.
- **Diagnostiquer les erreurs**.
- **Auditer les actions** (ex: modifications système).

**Bonnes pratiques** :
- Utiliser des **timestamps** pour chaque entrée.
- Définir des **niveaux de gravité** (INFO, WARNING, ERROR).
- **Séparer les logs** par jour ou par taille (rotation).

---

### **1.2. Écrire dans un fichier de log**
#### **1.2.1. Méthode basique avec `Add-Content`**
```powershell
# Chemin du fichier de log
$logFile = "C:\Logs\ScriptLog_$(Get-Date -Format 'yyyyMMdd').log"

# Écrire une entrée dans le log
$message = "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] [INFO] Début du script."
Add-Content -Path $logFile -Value $message
```
**Explications** :
- `Get-Date -Format 'yyyy-MM-dd HH:mm:ss'` : Génère un timestamp.
- `Add-Content` : Ajoute une ligne au fichier (crée le fichier s’il n’existe pas).

---

#### **1.2.2. Fonction réutilisable pour les logs**
```powershell
function Write-Log {
    param (
        [string]$message,
        [string]$level = "INFO",
        [string]$logFile = "C:\Logs\ScriptLog_$(Get-Date -Format 'yyyyMMdd').log"
    )

    # Créer le dossier si nécessaire
    if (-not (Test-Path -Path (Split-Path -Path $logFile -Parent))) {
        New-Item -ItemType Directory -Path (Split-Path -Path $logFile -Parent) -Force | Out-Null
    }

    # Formater le message avec timestamp et niveau
    $logEntry = "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] [$level] $message"

    # Écrire dans le fichier
    Add-Content -Path $logFile -Value $logEntry

    # Afficher dans la console (optionnel)
    switch ($level) {
        "ERROR" { Write-Host $logEntry -ForegroundColor Red }
        "WARNING" { Write-Host $logEntry -ForegroundColor Yellow }
        default { Write-Host $logEntry }
    }
}

# Exemple d'utilisation
Write-Log -message "Début du script de sauvegarde." -level "INFO"
Write-Log -message "Espace disque insuffisant sur C:!" -level "WARNING"
Write-Log -message "Échec de la connexion à la base de données." -level "ERROR"
```
**Explications** :
- **`$level`** : Définit le niveau de gravité (`INFO`, `WARNING`, `ERROR`).
- **Couleurs** : Affiche les messages dans la console avec des couleurs selon le niveau.

**Sortie dans le fichier** :
```
[2025-11-10 14:30:45] [INFO] Début du script de sauvegarde.
[2025-11-10 14:30:46] [WARNING] Espace disque insuffisant sur C:!
[2025-11-10 14:30:47] [ERROR] Échec de la connexion à la base de données.
```

---

#### **1.2.3. Utilisation avancée avec des objets**
```powershell
# Créer un objet pour structurer le log
$logEntry = [PSCustomObject]@{
    Timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    Level     = "INFO"
    Message   = "Début du script."
    Script    = $MyInvocation.MyCommand.Name
    User      = $env:USERNAME
}

# Convertir en JSON et écrire dans le log
$logEntry | ConvertTo-Json | Add-Content -Path $logFile
```
**Explications** :
- **`[PSCustomObject]`** : Crée un objet structuré avec des propriétés.
- **`ConvertTo-Json`** : Convertit l’objet en format JSON pour une analyse facile.

**Sortie dans le fichier** :
```json
{
    "Timestamp": "2025-11-10 14:30:45",
    "Level": "INFO",
    "Message": "Début du script.",
    "Script": "Backup.ps1",
    "User": "Admin"
}
```

---

### **1.3. Rotation des logs**
#### **1.3.1. Rotation par date**
```powershell
# Chemin du log avec la date du jour
$logFile = "C:\Logs\ScriptLog_$(Get-Date -Format 'yyyyMMdd').log"

# Supprimer les logs plus anciens que 30 jours
Get-ChildItem -Path "C:\Logs" -Filter "ScriptLog_*.log" | Where-Object {
    $_.LastWriteTime -lt (Get-Date).AddDays(-30)
} | Remove-Item -Force
```
**Explications** :
- **`ScriptLog_*.log`** : Filtre les fichiers de log.
- **`AddDays(-30)`** : Supprime les logs de plus de 30 jours.

---

#### **1.3.2. Rotation par taille**
```powershell
$logFile = "C:\Logs\ScriptLog.log"
$maxSize = 10MB  # Taille maximale du fichier

if (Test-Path -Path $logFile) {
    $fileSize = (Get-Item -Path $logFile).Length
    if ($fileSize -gt $maxSize) {
        $archiveName = "C:\Logs\ScriptLog_$(Get-Date -Format 'yyyyMMdd_HHmmss').archive.log"
        Rename-Item -Path $logFile -NewName $archiveName -Force
    }
}
```
**Explications** :
- Vérifie si le fichier dépasse **10 Mo**.
- **Archive** le fichier avec un timestamp avant de créer un nouveau log.

---

### **1.4. Travaux pratiques : Créer un système de log complet**
---
#### **Énoncé de l’exercice** :
**Objectif** :
1. Créer une **fonction `Write-Log`** qui :
   - Accepte un message, un niveau de gravité (`INFO`, `WARNING`, `ERROR`), et un chemin de log.
   - Écrit le message dans le fichier avec un **timestamp** et le **nom du script**.
   - Affiche le message dans la console avec une **couleur adaptée**.
2. **Archiver les logs** plus anciens que 7 jours.
3. **Tester la fonction** avec différents niveaux de gravité.

---
#### **Corrigé** :
```powershell
function Write-Log {
    param (
        [Parameter(Mandatory=$true)]
        [string]$message,

        [ValidateSet("INFO", "WARNING", "ERROR")]
        [string]$level = "INFO",

        [string]$logDir = "C:\Logs"
    )

    # Créer le dossier de logs s'il n'existe pas
    if (-not (Test-Path -Path $logDir)) {
        New-Item -ItemType Directory -Path $logDir -Force | Out-Null
    }

    # Chemin du log avec la date du jour
    $logFile = Join-Path -Path $logDir -ChildPath "ScriptLog_$(Get-Date -Format 'yyyyMMdd').log"

    # Formater le message
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $scriptName = $MyInvocation.MyCommand.Name
    $logEntry = "[$timestamp] [$level] [$scriptName] $message"

    # Écrire dans le fichier
    Add-Content -Path $logFile -Value $logEntry

    # Afficher dans la console avec couleur
    switch ($level) {
        "ERROR" { Write-Host $logEntry -ForegroundColor Red }
        "WARNING" { Write-Host $logEntry -ForegroundColor Yellow }
        default { Write-Host $logEntry }
    }

    # Archiver les logs plus anciens que 7 jours
    Get-ChildItem -Path $logDir -Filter "ScriptLog_*.log" | Where-Object {
        $_.LastWriteTime -lt (Get-Date).AddDays(-7)
    } | ForEach-Object {
        $archiveName = $_.FullName.Replace(".log", "_$(Get-Date -Format 'yyyyMMdd').archive.log")
        Rename-Item -Path $_.FullName -NewName $archiveName -Force
    }
}

# Exemples d'utilisation
Write-Log -message "Test du système de log (INFO)."
Write-Log -message "Attention, espace disque faible!" -level "WARNING"
Write-Log -message "Erreur critique: impossible de se connecter." -level "ERROR"
```

**Explications** :
- **`Join-Path`** : Construit le chemin du fichier de log.
- **`ValidateSet`** : Restreint les valeurs de `$level` à `INFO`, `WARNING`, ou `ERROR`.
- **Archivage automatique** : Les logs de plus de 7 jours sont renommés avec un suffixe `.archive.log`.

---