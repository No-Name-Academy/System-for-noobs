## **3. Travaux pratiques : Écrire un script qui génère un fichier de log avec des métriques système**

---
### **Énoncé du projet** :
**Objectif** :
Créer un script qui :
1. **Surveille les métriques système** toutes les 5 minutes :
   - Utilisation CPU (`% Processor Time`).
   - Mémoire disponible (`Available MBytes`).
   - Espace disque libre sur `C:` (en %).
   - Statut des services critiques (`Spooler`, `DHCP`, `DNS`).
2. **Écrit les métriques dans un fichier de log** (`C:\Logs\SystemMetrics.log`) avec :
   - Un **timestamp**.
   - Un **niveau de gravité** (`INFO`, `WARNING`, `ERROR`).
3. **Génère un rapport quotidien** :
   - **CSV** avec les métriques (`C:\Logs\SystemMetrics_Report.csv`).
   - **HTML** avec un résumé visuel (`C:\Logs\SystemMetrics_Report.html`).
4. **Archive les logs** plus anciens que 7 jours.

---
### **Corrigé** :
```powershell
<#
.SYNOPSIS
    Script de surveillance des métriques système avec logging et reporting.

.DESCRIPTION
    Ce script surveille le CPU, la mémoire, l'espace disque et les services critiques,
    puis génère des logs et des rapports quotidiens.
#>

# Chemin des fichiers
$logDir = "C:\Logs"
$logFile = Join-Path -Path $logDir -ChildPath "SystemMetrics_$(Get-Date -Format 'yyyyMMdd').log"
$csvReport = Join-Path -Path $logDir -ChildPath "SystemMetrics_Report_$(Get-Date -Format 'yyyyMMdd').csv"
$htmlReport = Join-Path -path $logDir -ChildPath "SystemMetrics_Report_$(Get-Date -Format 'yyyyMMdd').html"

# Services critiques à surveiller
$criticalServices = @("Spooler", "DHCP", "DNS")

# Créer le dossier de logs s'il n'existe pas
if (-not (Test-Path -Path $logDir)) {
    New-Item -ItemType Directory -Path $logDir -Force | Out-Null
}

# Fonction pour écrire dans le log
function Write-MetricLog {
    param (
        [string]$message,
        [string]$level = "INFO"
    )

    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $logEntry = "[$timestamp] [$level] $message"
    Add-Content -Path $logFile -Value $logEntry

    # Afficher dans la console
    switch ($level) {
        "ERROR" { Write-Host $logEntry -ForegroundColor Red }
        "WARNING" { Write-Host $logEntry -ForegroundColor Yellow }
        default { Write-Host $logEntry }
    }
}

# Fonction pour surveiller les métriques
function Monitor-System {
    # 1. CPU
    $cpu = (Get-Counter '\Processor(_Total)\% Processor Time').CounterSamples.CookedValue
    $cpuStatus = if ($cpu -gt 90) { "WARNING" } else { "INFO" }
    Write-MetricLog -message "CPU: $([math]::Round($cpu, 1))%" -level $cpuStatus

    # 2. Mémoire
    $mem = (Get-Counter '\Memory\Available MBytes').CounterSamples.CookedValue
    $memStatus = if ($mem -lt 1024) { "WARNING" } else { "INFO" }
    Write-MetricLog -message "Mémoire disponible: $([math]::Round($mem, 1)) Mo" -level $memStatus

    # 3. Espace disque C:
    $disk = Get-Volume -DriveLetter C -ErrorAction SilentlyContinue
    if ($disk) {
        $freePercent = ($disk.SizeRemaining / $disk.Size) * 100
        $diskStatus = if ($freePercent -lt 20) { "WARNING" } else { "INFO" }
        Write-MetricLog -message "Disque C: $([math]::Round($freePercent, 1))% libre" -level $diskStatus
    } else {
        Write-MetricLog -message "Disque C: introuvable" -level "ERROR"
    }

    # 4. Services critiques
    foreach ($service in $criticalServices) {
        $serviceObj = Get-Service -Name $service -ErrorAction SilentlyContinue
        if ($serviceObj) {
            $serviceStatus = if ($serviceObj.Status -eq "Running") { "INFO" } else { "ERROR" }
            Write-MetricLog -message "Service $service: $($serviceObj.Status)" -level $serviceStatus
        } else {
            Write-MetricLog -message "Service $service: introuvable" -level "ERROR"
        }
    }
}

# Fonction pour générer le rapport quotidien
function Generate-Report {
    # Lire les logs de la journée
    $logs = Get-Content -Path $logFile | ForEach-Object {
        if ($_ -match '\[(INFO|WARNING|ERROR)\]') {
            $timestamp = ($_ -split '\]')[0].TrimStart('[')
            $level = ($_ -split '\]')[1].TrimStart(' [').Split(' ')[0]
            $message = ($_ -split '\] ')[1]

            [PSCustomObject]@{
                Timestamp = $timestamp
                Level     = $level
                Metric    = ($message -split ': ')[0]
                Value     = ($message -split ': ')[1]
            }
        }
    }

    # Exporter en CSV
    $logs | Export-Csv -Path $csvReport -NoTypeInformation -Encoding UTF8

    # Générer un rapport HTML
    $htmlContent = @"
<!DOCTYPE html>
<html>
<head>
    <title>Rapport des métriques système - $(Get-Date -Format 'yyyy-MM-dd')</title>
    <style>
        body { font-family: Arial; margin: 20px; }
        h1 { color: #0066cc; }
        table { border-collapse: collapse; width: 100%; }
        th { background-color: #0066cc; color: white; padding: 8px; }
        td { border: 1px solid #ddd; padding: 8px; }
        tr:nth-child(even) { background-color: #f2f2f2; }
        .warning { background-color: #ffeb3b; }
        .error { background-color: #ff9999; }
    </style>
</head>
<body>
    <h1>Rapport des métriques système - $(Get-Date -Format 'yyyy-MM-dd')</h1>
    <table>
        <tr><th>Timestamp</th><th>Niveau</th><th>Métrique</th><th>Valeur</th></tr>
"@

    $logs | ForEach-Object {
        $rowClass = if ($_.Level -eq "ERROR") { "error" } elseif ($_.Level -eq "WARNING") { "warning" } else { "" }
        $htmlContent += "<tr class='$rowClass'><td>$($_.Timestamp)</td><td>$($_.Level)</td><td>$($_.Metric)</td><td>$($_.Value)</td></tr>`n"
    }

    $htmlContent += @"
    </table>
</body>
</html>
"@

    $htmlContent | Out-File -FilePath $htmlReport -Encoding UTF8

    Write-Host "Rapport généré : $htmlReport" -ForegroundColor Green
}

# Exécution principale
Write-MetricLog -message "=== Début de la surveillance ==="

# Surveiller les métriques toutes les 5 minutes pendant 1 heure (pour test)
for ($i = 0; $i -lt 12; $i++) {
    $currentTime = Get-Date -Format "HH:mm:ss"
    Write-MetricLog -message "--- Itération $($i + 1) ($currentTime) ---"

    Monitor-System

    if ($i -lt 11) {
        Start-Sleep -Seconds 300  # Attendre 5 minutes (300 secondes)
    }
}

# Générer le rapport
Generate-Report

# Archiver les logs plus anciens que 7 jours
Get-ChildItem -Path $logDir -Filter "SystemMetrics_*.log" | Where-Object {
    $_.LastWriteTime -lt (Get-Date).AddDays(-7)
} | ForEach-Object {
    $archiveName = $_.FullName.Replace(".log", "_$(Get-Date -Format 'yyyyMMdd').archive.log")
    Rename-Item -Path $_.FullName -NewName $archiveName -Force
}

Write-MetricLog -message "=== Fin de la surveillance ==="
```

---
### **Explications détaillées** :
1. **Fonction `Write-MetricLog`** :
   - Écrit les métriques dans le log avec un **timestamp** et un **niveau de gravité**.
   - Affiche les messages dans la console avec des **couleurs**.

2. **Fonction `Monitor-System`** :
   - **CPU** : Utilise `Get-Counter` pour récupérer l’utilisation.
   - **Mémoire** : Vérifie la mémoire disponible en Mo.
   - **Disque** : Calcule l’espace libre en % sur `C:`.
   - **Services** : Vérifie le statut des services critiques.

3. **Fonction `Generate-Report`** :
   - Lit le fichier de log et **parse** chaque ligne pour extraire les données.
   - Génère un **rapport HTML** avec un style CSS pour mettre en évidence les alertes.
   - Exporte aussi les données en **CSV**.

4. **Boucle principale** :
   - Exécute `Monitor-System` toutes les **5 minutes** (12 itérations pour 1 heure de test).
   - À la fin, génère le rapport et **archive les anciens logs**.

5. **Sortie HTML** :
   - Utilise des **classes CSS** (`warning`, `error`) pour colorer les lignes.
   - Affiche un tableau clair avec les métriques.

---
### **Planification du script**
Pour exécuter ce script **toutes les 5 minutes** en arrière-plan :
```powershell
$action = New-ScheduledJobAction -Execute "PowerShell.exe" -Argument "-NoProfile -File `"C:\Scripts\MonitorSystemMetrics.ps1`""
$trigger = New-JobTrigger -Once -At (Get-Date) -RepetitionInterval (New-TimeSpan -Minutes 5) -RepetitionDuration (New-TimeSpan -Hours 24)
Register-ScheduledJob -Name "SystemMetricsMonitor" -Action $action -Trigger $trigger
```

---

