## **2. Gestion des services et des performances système**
### **2.1. Surveillance et gestion des services**
**Cmdlets clés** :
| Cmdlet               | Description                                      |
|----------------------|--------------------------------------------------|
| `Get-Service`        | Liste les services.                              |
| `Start-Service`      | Démarre un service.                              |
| `Stop-Service`       | Arrête un service.                               |
| `Restart-Service`    | Redémarre un service.                            |
| `Set-Service`        | Modifie les propriétés d’un service.             |

**Exemple : Redémarrer les services critiques**
```powershell
# Lister les services critiques
$criticalServices = @("Spooler", "DHCP", "DNS")

foreach ($service in $criticalServices) {
    if ((Get-Service -Name $service).Status -ne "Running") {
        Start-Service -Name $service
        Write-Output "Service $service démarré."
    }
}
```

---

### **2.2. Surveillance des performances**
**Cmdlets clés** :
| Cmdlet                     | Description                                      |
|----------------------------|--------------------------------------------------|
| `Get-Counter`              | Récupère les compteurs de performance.           |
| `Measure-Command`          | Mesure le temps d’exécution d’une commande.      |

**Exemple : Surveiller l’utilisation CPU et mémoire**
```powershell
# Récupérer les compteurs CPU et mémoire
$cpuUsage = (Get-Counter '\Processor(_Total)\% Processor Time').CounterSamples.CookedValue
$memUsage = (Get-Counter '\Memory\Available MBytes').CounterSamples.CookedValue

Write-Output "Utilisation CPU : $cpuUsage %"
Write-Output "Mémoire disponible : $memUsage Mo"

# Exporter les données dans un fichier CSV
Get-Counter '\Processor(_Total)\% Processor Time', '\Memory\Available MBytes' -Continuous |
    Export-Counter -Path "C:\Temp\PerfStats.csv" -MaxSamples 10 -FileFormat CSV
```

---

### **2.3. Travaux pratiques : Script de surveillance des services et performances**
**Script : `MonitorServices.ps1`**
```powershell
<#
.SYNOPSIS
    Surveille les services critiques et les performances système.
#>
$criticalServices = @("Spooler", "DHCP", "DNS")
$logFile = "C:\Temp\ServiceMonitor.log"

foreach ($service in $criticalServices) {
    $status = (Get-Service -Name $service).Status
    if ($status -ne "Running") {
        Add-Content -Path $logFile -Value "$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss') - Service $service est $status"
        Start-Service -Name $service
    }
}

# Surveiller les performances
$cpuUsage = (Get-Counter '\Processor(_Total)\% Processor Time').CounterSamples.CookedValue
Add-Content -Path $logFile -Value "$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss') - Utilisation CPU : $cpuUsage %"
```

---

### **2.4. Planification de la surveillance**
```powershell
$action = New-ScheduledJobAction -Execute "PowerShell.exe" -Argument "-NoProfile -File `"C:\Scripts\MonitorServices.ps1`""
$trigger = New-JobTrigger -Once -At (Get-Date).AddMinutes(5) -RepetitionInterval (New-TimeSpan -Minutes 30) -RepetitionDuration (New-TimeSpan -Hours 8)
Register-ScheduledJob -Name "MonitorServices" -Action $action -Trigger $trigger
```

---