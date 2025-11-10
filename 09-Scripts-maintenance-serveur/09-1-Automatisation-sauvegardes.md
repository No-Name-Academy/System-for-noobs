## **1. Automatisation des sauvegardes**
### **1.1. Sauvegarde de fichiers avec PowerShell**
**Exemple : Sauvegarder un dossier vers un partage réseau**
```powershell
<#
.SYNOPSIS
    Sauvegarde un dossier local vers un partage réseau.
#>
$source = "C:\Data"
$destination = "\\Server\Backup\Data_$(Get-Date -Format 'yyyyMMdd')"
$logFile = "C:\Temp\BackupLog.txt"

# Créer le dossier de destination
New-Item -Path $destination -ItemType Directory -Force | Out-Null

# Copier les fichiers
Copy-Item -Path "$source\*" -Destination $destination -Recurse -Force

# Journaliser l’action
Add-Content -Path $logFile -Value "$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss') - Sauvegarde de $source vers $destination terminée."
```

---

### **1.2. Sauvegarde avec `robocopy`**
```powershell
$source = "C:\Data"
$destination = "\\Server\Backup\Data_$(Get-Date -Format 'yyyyMMdd')"
$logFile = "C:\Temp\RobocopyLog.txt"

# Utiliser robocopy pour une copie robuste
& robocopy $source $destination /E /Z /R:3 /W:5 /LOG:$logFile /TEE
```

---

### **1.3. Travaux pratiques : Script de sauvegarde incrémentielle**
**Script : `BackupIncremental.ps1`**
```powershell
<#
.SYNOPSIS
    Effectue une sauvegarde incrémentielle des fichiers modifiés dans les dernières 24 heures.
#>
$source = "C:\Data"
$destination = "\\Server\Backup\Incremental_$(Get-Date -Format 'yyyyMMdd')"
$logFile = "C:\Temp\IncrementalBackupLog.txt"

# Créer le dossier de destination
New-Item -Path $destination -ItemType Directory -Force | Out-Null

# Copier les fichiers modifiés dans les dernières 24 heures
Get-ChildItem -Path $source -File -Recurse | Where-Object {
    $_.LastWriteTime -gt (Get-Date).AddHours(-24)
} | ForEach-Object {
    $destPath = Join-Path -Path $destination -ChildPath $_.FullName.Substring($source.Length)
    New-Item -Path (Split-Path -Path $destPath -Parent) -ItemType Directory -Force | Out-Null
    Copy-Item -Path $_.FullName -Destination $destPath -Force
    Add-Content -Path $logFile -Value "$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss') - Copie de $($_.FullName) vers $destPath"
}
```

---