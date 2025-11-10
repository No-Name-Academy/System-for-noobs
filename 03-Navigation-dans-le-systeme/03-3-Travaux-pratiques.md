## **3. Travaux pratiques : Écrire un script pour naviguer et gérer les fichiers**

### **3.1. Scénario : Script d’inventaire et de nettoyage**
**Objectif** :
Créer un script qui :
1. Parcourt un dossier et ses sous-dossiers.
2. Génère un rapport des fichiers par extension.
3. Supprime les fichiers temporaires (.tmp) plus anciens que 30 jours.
4. Archive les fichiers .log dans un dossier dédié.

#### **Script complet : Gestion des fichiers et des logs**
```powershell
<#
.SYNOPSIS
    Script pour inventorier, nettoyer et archiver les fichiers.

.DESCRIPTION
    Ce script parcourt un dossier, génère un rapport des fichiers par extension,
    supprime les fichiers temporaires anciens et archive les fichiers .log.
#>

# Paramètres
$rootPath = "C:\Temp"
$archivePath = "C:\Temp\Archive_Logs"
$logFile = "C:\Temp\inventaire_log.txt"
$daysToKeep = 30

# Créer le dossier d’archive s’il n’existe pas
if (-not (Test-Path -Path $archivePath)) {
    New-Item -Path $archivePath -ItemType Directory | Out-Null
}

# Fonction pour générer un rapport des fichiers par extension
function Generate-FileReport {
    param($path)
    $report = Get-ChildItem -Path $path -File -Recurse | Group-Object -Property Extension
    $report | ForEach-Object {
        $extension = if ($_.Name -eq "") { "Sans extension" } else { $_.Name }
        Add-Content -Path $logFile -Value "Extension : $extension - Nombre de fichiers : $($_.Count)"
    }
}

# Fonction pour nettoyer les fichiers temporaires
function Clean-TempFiles {
    param($path, $days)
    Get-ChildItem -Path $path -File -Recurse -Filter "*.tmp" | Where-Object {
        $_.LastWriteTime -lt (Get-Date).AddDays(-$days)
    } | ForEach-Object {
        $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
        Add-Content -Path $logFile -Value "$timestamp - Suppression de $($_.FullName)"
        Remove-Item -Path $_.FullName -Force
    }
}

# Fonction pour archiver les fichiers .log
function Archive-LogFiles {
    param($sourcePath, $destPath)
    Get-ChildItem -Path $sourcePath -File -Recurse -Filter "*.log" | ForEach-Object {
        $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
        $dest = Join-Path -Path $destPath -ChildPath $_.Name
        Add-Content -Path $logFile -Value "$timestamp - Archivage de $($_.FullName) vers $dest"
        Move-Item -Path $_.FullName -Destination $dest -Force -ErrorAction SilentlyContinue
    }
}

# Exécution
Add-Content -Path $logFile -Value "=== Début du script - $(Get-Date -Format 'yyyy-MM-dd HH:mm:ss') ==="
Generate-FileReport -path $rootPath
Clean-TempFiles -path $rootPath -days $daysToKeep
Archive-LogFiles -sourcePath $rootPath -destPath $archivePath
Add-Content -Path $logFile -Value "=== Fin du script - $(Get-Date -Format 'yyyy-MM-dd HH:mm:ss') ==="
Write-Host "Script terminé. Voir $logFile pour les détails."
```

---

### **3.2. Scénario : Script de sauvegarde incrémentielle**
**Objectif** :
Créer un script qui copie les fichiers modifiés dans les dernières 24 heures vers un dossier de sauvegarde.

#### **Script : Sauvegarde incrémentielle**
```powershell
$sourcePath = "C:\Temp\ProjetA"
$backupPath = "C:\Temp\Backup"
$logFile = "C:\Temp\backup_log.txt"

# Créer le dossier de sauvegarde s’il n’existe pas
if (-not (Test-Path -Path $backupPath)) {
    New-Item -Path $backupPath -ItemType Directory | Out-Null
}

# Copier les fichiers modifiés dans les dernières 24h
Get-ChildItem -Path $sourcePath -File -Recurse | Where-Object {
    $_.LastWriteTime -gt (Get-Date).AddHours(-24)
} | ForEach-Object {
    $dest = Join-Path -Path $backupPath -ChildPath $_.FullName.Substring($sourcePath.Length)
    $destDir = Split-Path -Path $dest -Parent
    if (-not (Test-Path -Path $destDir)) {
        New-Item -Path $destDir -ItemType Directory -Force | Out-Null
    }
    Copy-Item -Path $_.FullName -Destination $dest -Force
    Add-Content -Path $logFile -Value "$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss') - Copie de $($_.FullName) vers $dest"
}

Write-Host "Sauvegarde incrémentielle terminée. Voir $logFile pour les détails."
```

---