## **3. Travaux pratiques : Créer des scripts avec des sorties formatées pour un reporting clair**

---
### **Énoncé du projet** :
**Objectif** :
Créer un **script de rapport système complet** qui :
1. **Récupère les informations** :
   - Utilisation CPU/mémoire.
   - Espace disque (tous les volumes).
   - Services critiques (`Spooler`, `DHCP`, `DNS`).
   - Dernières erreurs du journal **System** (5 dernières).
2. **Formate les sorties** :
   - **Tableau** dans la console pour un aperçu rapide.
   - **CSV** pour une analyse ultérieure (`C:\Temp\SystemReport.csv`).
   - **HTML** pour un rapport visuel (`C:\Temp\SystemReport.html`).
3. **Personnalise les messages** :
   - Couleurs pour les alertes (rouge/jaune/vert).
   - Barre de progression pendant la collecte des données.

---
### **Corrigé** :
```powershell
<#
.SYNOPSIS
    Génère un rapport système complet avec sorties formatées.

.DESCRIPTION
    Ce script collecte les métriques système (CPU, mémoire, disques, services, erreurs)
    et génère des rapports en tableau, CSV et HTML.
#>
$reportDate = Get-Date -Format "yyyy-MM-dd_HH-mm-ss"
$csvPath = "C:\Temp\SystemReport_$reportDate.csv"
$htmlPath = "C:\Temp\SystemReport_$reportDate.html"

# Fonction pour ajouter une section au rapport HTML
function Add-HtmlSection {
    param (
        [string]$title,
        [object]$data,
        [string]$html
    )

    $section = @"
<h2>$title</h2>
$(ConvertTo-Html -InputObject $data -Fragment)
"@
    return $html.Replace("</body>", "$section</body>")
}

# 1. Collecte des données avec barre de progression
$tasks = @(
    { Get-Counter '\Processor(_Total)\% Processor Time' | Select-Object -ExpandProperty CounterSamples },
    { Get-Counter '\Memory\Available MBytes' | Select-Object -ExpandProperty CounterSamples },
    { Get-Volume },
    { Get-Service -Name "Spooler", "DHCP", "DNS" },
    { Get-WinEvent -LogName System -FilterXPath "*[System[Level=2]]" -MaxEvents 5 }
)

$results = @()
$i = 0

foreach ($task in $tasks) {
    $i++
    $percentComplete = ($i / $tasks.Count) * 100
    Write-Progress -Activity "Collecte des données..." -PercentComplete $percentComplete

    $results += Invoke-Command -ScriptBlock $task
}

# 2. Préparer les données pour les rapports
# CPU
$cpu = $results[0].CookedValue
$cpuObj = [PSCustomObject]@{
    Metric = "CPU Usage"
    Value  = "$([math]::Round($cpu, 1))%"
    Status = if ($cpu -gt 90) { "High" } else { "Normal" }
}

# Mémoire
$mem = $results[1].CookedValue
$memObj = [PSCustomObject]@{
    Metric = "Available Memory (Mo)"
    Value  = [math]::Round($mem, 1)
    Status = if ($mem -lt 1024) { "Low" } else { "Normal" }
}

# Disques
$disks = $results[2] | ForEach-Object {
    [PSCustomObject]@{
        Volume      = $_.DriveLetter
        Total       = "$([math]::Round($_.Size / 1GB, 2)) Go"
        Free        = "$([math]::Round($_.SizeRemaining / 1GB, 2)) Go"
        FreePercent = "$([math]::Round(($_.SizeRemaining / $_.Size) * 100, 1))%"
        Status      = if (($_.SizeRemaining / $_.Size) * 100 -lt 20) { "Low" } else { "Normal" }
    }
}

# Services
$services = $results[3] | ForEach-Object {
    [PSCustomObject]@{
        Name   = $_.Name
        Status = $_.Status
    }
}

# Erreurs
$errors = $results[4] | ForEach-Object {
    [PSCustomObject]@{
        Time    = $_.TimeCreated
        Id      = $_.Id
        Source  = $_.ProviderName
        Message = $_.Message.Substring(0, [math]::Min(50, $_.Message.Length))
    }
}

# 3. Afficher un résumé en console (avec couleurs)
Write-Host "`n--- Rapport Système ($reportDate) ---" -ForegroundColor Cyan
Write-Host "CPU: $([math]::Round($cpu, 1))%" -ForegroundColor $(if ($cpu -gt 90) { "Red" } else { "Green" })
Write-Host "Mémoire disponible: $([math]::Round($mem, 1)) Mo" -ForegroundColor $(if ($mem -lt 1024) { "Red" } else { "Green" })

Write-Host "`nDisques:" -ForegroundColor Cyan
$disks | Format-Table -AutoSize

Write-Host "`nServices critiques:" -ForegroundColor Cyan
$services | Format-Table -AutoSize

Write-Host "`nDernières erreurs système:" -ForegroundColor Cyan
$errors | Format-Table -AutoSize

# 4. Exporter en CSV
@($cpuObj, $memObj) |
    Export-Csv -Path $csvPath -NoTypeInformation -Encoding UTF8 -Append
$disks | Export-Csv -Path $csvPath -NoTypeInformation -Encoding UTF8 -Append
$services | Export-Csv -Path $csvPath -NoTypeInformation -Encoding UTF8 -Append
$errors | Export-Csv -Path $csvPath -NoTypeInformation -Encoding UTF8 -Append

# 5. Générer le rapport HTML
$html = ConvertTo-Html -Title "Rapport Système - $($env:COMPUTERNAME) - $reportDate" -Head @"
<style>
    body { font-family: Arial; }
    h1 { color: #0066cc; }
    h2 { color: #333; }
    table { border-collapse: collapse; width: 100%; }
    th { background-color: #0066cc; color: white; }
    td { border: 1px solid #ddd; padding: 8px; }
    tr:nth-child(even) { background-color: #f2f2f2; }
</style>
"@ -Body "<h1>Rapport Système - $($env:COMPUTERNAME)</h1>"

$html = Add-HtmlSection -title "CPU et Mémoire" -data @($cpuObj, $memObj) -html $html
$html = Add-HtmlSection -title "Disques" -data $disks -html $html
$html = Add-HtmlSection -title "Services Critiques" -data $services -html $html
$html = Add-HtmlSection -title "Dernières Erreurs Système" -data $errors -html $html

$html | Out-File -FilePath $htmlPath

Write-Host "`nRapport généré :`n- CSV: $csvPath`n- HTML: $htmlPath" -ForegroundColor Green
```

---
### **Explications détaillées** :
1. **Collecte des données** :
   - Utilise `Invoke-Command` pour exécuter chaque tâche dans une boucle.
   - `Write-Progress` affiche une barre de progression pendant la collecte.

2. **Préparation des objets** :
   - **CPU/Mémoire** : Crée des objets personnalisés avec un statut (`High`/`Low`/`Normal`).
   - **Disques** : Calcule l’espace libre en Go et en %.
   - **Services/Erreurs** : Formate les données pour une sortie claire.

3. **Sortie console** :
   - Affiche un **résumé coloré** des métriques critiques.

4. **Export CSV** :
   - Combine toutes les données dans un seul fichier CSV.

5. **Rapport HTML** :
   - Utilise `ConvertTo-Html` avec un **style CSS intégré** pour un rendu professionnel.
   - `Add-HtmlSection` : Ajoute des sections titrées au rapport.

6. **Sortie finale** :
   - Affiche les chemins des fichiers générés.

---
### **Bonus : Automatisation avec une tâche planifiée**
Pour exécuter ce script **quotidiennement à 8h** :
```powershell
$action = New-ScheduledJobAction -Execute "PowerShell.exe" -Argument "-NoProfile -File `"C:\Scripts\SystemReport.ps1`""
$trigger = New-JobTrigger -Daily -At 8am
Register-ScheduledJob -Name "DailySystemReport" -Action $action -Trigger $trigger
```

---
