## **3. Travaux pratiques : Script de maintenance basique pour un serveur**
### **3.1. Script complet : `ServerMaintenance.ps1`**
```powershell
<#
.SYNOPSIS
    Effectue une maintenance basique du serveur : nettoyage, sauvegarde et mises à jour.
#>
$logFile = "C:\Temp\ServerMaintenance_$(Get-Date -Format 'yyyyMMdd').log"

function Write-Log {
    param ([string]$message)
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $logMessage = "[$timestamp] $message"
    Write-Output $logMessage
    Add-Content -Path $logFile -Value $logMessage
}

# 1. Nettoyage des logs
Write-Log "Début du nettoyage des logs..."
$logPath = "C:\Logs"
$daysToKeep = 30
Get-ChildItem -Path $logPath -File -Recurse | Where-Object {
    $_.LastWriteTime -lt (Get-Date).AddDays(-$daysToKeep)
} | ForEach-Object {
    Remove-Item -Path $_.FullName -Force
    Write-Log "Suppression de $($_.FullName)"
}

# 2. Sauvegarde des données
Write-Log "Début de la sauvegarde des données..."
$source = "C:\Data"
$destination = "\\Server\Backup\Data_$(Get-Date -Format 'yyyyMMdd')"
New-Item -Path $destination -ItemType Directory -Force | Out-Null
Copy-Item -Path "$source\*" -Destination $destination -Recurse -Force
Write-Log "Sauvegarde de $source vers $destination terminée."

# 3. Vérification des services critiques
Write-Log "Vérification des services critiques..."
$criticalServices = @("Spooler", "DHCP", "DNS")
foreach ($service in $criticalServices) {
    $status = (Get-Service -Name $service).Status
    if ($status -ne "Running") {
        Write-Log "Service $service est $status - Redémarrage..."
        Start-Service -Name $service
    }
}

# 4. Installation des mises à jour (optionnel)
Write-Log "Vérification des mises à jour..."
if (Get-Module -ListAvailable -Name PSWindowsUpdate) {
    Import-Module PSWindowsUpdate
    $updates = Get-WindowsUpdate
    if ($updates) {
        Write-Log "Mises à jour disponibles - Installation..."
        Install-WindowsUpdate -AcceptAll -AutoReboot
    }
} else {
    Write-Log "Module PSWindowsUpdate non installé - Mises à jour ignorées."
}

Write-Log "Maintenance terminée."
```

---

### **3.2. Planification du script de maintenance**
```powershell
$action = New-ScheduledJobAction -Execute "PowerShell.exe" -Argument "-NoProfile -File `"C:\Scripts\ServerMaintenance.ps1`""
$trigger = New-JobTrigger -Weekly -DaysOfWeek Sunday -At 1am
Register-ScheduledJob -Name "ServerMaintenance" -Action $action -Trigger $trigger
```

---