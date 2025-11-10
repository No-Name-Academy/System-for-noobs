## **2. Exportation des données pour le reporting**

### **2.1. Analyser les logs avec PowerShell**
#### **2.1.1. Compter les erreurs dans un log**
```powershell
# Compter le nombre d'erreurs dans un fichier de log
$logFile = "C:\Logs\ScriptLog_20251110.log"
$errorCount = (Get-Content -Path $logFile | Where-Object { $_ -match '\[ERROR\]' }).Count
Write-Host "Nombre d'erreurs : $errorCount"
```
**Explications** :
- **`-match '\[ERROR\]'`** : Filtre les lignes contenant `[ERROR]`.

---

#### **2.1.2. Extraire les logs d’un niveau spécifique**
```powershell
# Extraire tous les messages de niveau WARNING
Get-Content -Path $logFile | Where-Object { $_ -match '\[WARNING\]' } | ForEach-Object {
    Write-Host $_ -ForegroundColor Yellow
}
```

---

#### **2.1.3. Générer un rapport à partir des logs**
```powershell
# Compter les occurrences de chaque niveau
$logStats = Get-Content -Path $logFile | ForEach-Object {
    if ($_ -match '\[(INFO|WARNING|ERROR)\]') {
        $matches[1]
    }
} | Group-Object | Select-Object Name, Count

# Afficher les statistiques
$logStats | Format-Table -AutoSize

# Exporter en CSV
$logStats | Export-Csv -Path "C:\Logs\LogStats_$(Get-Date -Format 'yyyyMMdd').csv" -NoTypeInformation
```
**Sortie** :
```
Name    Count
----    -----
INFO       42
WARNING     5
ERROR       2
```

---

### **2.2. Exporter les logs vers Excel**
#### **2.2.1. Utiliser `ImportExcel` (module externe)**
```powershell
# Installer le module (si nécessaire)
Install-Module -Name ImportExcel -Force -Scope CurrentUser

# Importer les logs et exporter vers Excel
$logs = Get-Content -Path $logFile | ForEach-Object {
    $parts = $_ -split '\] '
    $timestamp = $parts[0].TrimStart('[')
    $level = ($parts[1] -split ' ')[0].TrimStart('[')
    $message = $parts[2]

    [PSCustomObject]@{
        Timestamp = $timestamp
        Level     = $level
        Message   = $message
    }
}

$logs | Export-Excel -Path "C:\Logs\LogReport_$(Get-Date -Format 'yyyyMMdd').xlsx" -WorksheetName "Logs" -AutoSize
```
**Explications** :
- **`ImportExcel`** : Module pour exporter vers Excel (plus flexible que CSV).
- **`-AutoSize`** : Ajuste la largeur des colonnes.

---

#### **2.2.2. Créer un rapport HTML**
```powershell
# Convertir les logs en HTML
$logs | ConvertTo-Html -Title "Rapport des logs - $(Get-Date -Format 'yyyy-MM-dd')" |
    Out-File -FilePath "C:\Logs\LogReport_$(Get-Date -Format 'yyyyMMdd').html"
```

---

### **2.3. Travaux pratiques : Analyser les logs système**
---
#### **Énoncé de l’exercice** :
**Objectif** :
1. **Lire un fichier de log** (`C:\Logs\ScriptLog_20251110.log`).
2. **Compter le nombre d’entrées** par niveau (`INFO`, `WARNING`, `ERROR`).
3. **Exporter les résultats** :
   - En **CSV** (`C:\Logs\LogStats.csv`).
   - En **Excel** (`C:\Logs\LogReport.xlsx`).
4. **Afficher un résumé** dans la console avec des couleurs.

---
#### **Corrigé** :
```powershell
# Chemin du fichier de log
$logFile = "C:\Logs\ScriptLog_$(Get-Date -Format 'yyyyMMdd').log"

# Vérifier si le fichier existe
if (-not (Test-Path -Path $logFile)) {
    Write-Host "Fichier de log introuvable : $logFile" -ForegroundColor Red
    exit
}

# Analyser les logs
$logEntries = Get-Content -Path $logFile | ForEach-Object {
    if ($_ -match '\[(INFO|WARNING|ERROR)\]') {
        $timestamp = ($_ -split '\]')[0].TrimStart('[')
        $level = ($_ -split '\]')[1].TrimStart(' [').Split(' ')[0]
        $message = ($_ -split '\] ')[1]

        [PSCustomObject]@{
            Timestamp = $timestamp
            Level     = $level
            Message   = $message
        }
    }
}

# Compter les occurrences par niveau
$logStats = $logEntries | Group-Object -Property Level | Select-Object Name, Count

# Afficher un résumé coloré
$logStats | ForEach-Object {
    switch ($_.Name) {
        "ERROR" { Write-Host "$($_.Name): $($_.Count)" -ForegroundColor Red }
        "WARNING" { Write-Host "$($_.Name): $($_.Count)" -ForegroundColor Yellow }
        default { Write-Host "$($_.Name): $($_.Count)" }
    }
}

# Exporter en CSV
$logStats | Export-Csv -Path "C:\Logs\LogStats_$(Get-Date -Format 'yyyyMMdd').csv" -NoTypeInformation

# Exporter en Excel (si le module est installé)
if (Get-Module -ListAvailable -Name ImportExcel) {
    Import-Module ImportExcel
    $logEntries | Export-Excel -Path "C:\Logs\LogReport_$(Get-Date -Format 'yyyyMMdd').xlsx" -WorksheetName "Logs" -AutoSize
    Write-Host "Rapport Excel généré." -ForegroundColor Green
} else {
    Write-Host "Module ImportExcel non installé. Utilisez 'Install-Module ImportExcel' pour exporter vers Excel." -ForegroundColor Yellow
}
```

**Explications** :
- **Regex** : Extrait le timestamp, le niveau et le message de chaque ligne.
- **`Group-Object`** : Compte les occurrences de chaque niveau.
- **Export Excel** : Optionnel (nécessite le module `ImportExcel`).

---