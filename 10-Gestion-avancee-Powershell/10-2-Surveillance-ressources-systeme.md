## **2. Surveillance des ressources système**

### **2.1. Surveillance du CPU et de la mémoire**
#### **2.1.1. Récupérer l’utilisation CPU**
```powershell
# Utilisation CPU globale
(Get-Counter '\Processor(_Total)\% Processor Time').CounterSamples.CookedValue

# Utilisation CPU par processus
Get-Process | Sort-Object -Property CPU -Descending | Select-Object -First 10 Name, CPU, Id
```

**Explication** :
- `Get-Counter` : Récupère les compteurs de performance.
- `CookedValue` : Valeur formatée du compteur.

---

#### **2.1.2. Récupérer l’utilisation mémoire**
```powershell
# Mémoire totale et disponible
$memStats = Get-Counter '\Memory\Available MBytes', '\Memory\Committed Bytes', '\Memory\% Committed Bytes In Use'
$memStats.CounterSamples | ForEach-Object {
    Write-Host "$($_.Path) : $([math]::Round($_.CookedValue, 2))"
}

# Top 10 des processus consommant le plus de mémoire
Get-Process | Sort-Object -Property WorkingSet -Descending | Select-Object -First 10 Name, @{
    Name = "Mem(Mo)"
    Expression = { [math]::Round($_.WorkingSet / 1MB, 2) }
}
```

**Explication** :
- `WorkingSet` : Mémoire physique utilisée par un processus (en octets).
- `[math]::Round($_.WorkingSet / 1MB, 2)` : Convertit en Mo et arrondit à 2 décimales.

---

### **2.2. Surveillance des disques**
#### **2.2.1. Espace disque disponible**
```powershell
# Lister les disques et leur espace libre
Get-Volume | Select-Object DriveLetter, FileSystemLabel, @{
    Name = "Size(Go)"
    Expression = { [math]::Round($_.Size / 1GB, 2) }
}, @{
    Name = "Free(Go)"
    Expression = { [math]::Round($_.SizeRemaining / 1GB, 2) }
}, @{
    Name = "Free(%)"
    Expression = { [math]::Round(($_.SizeRemaining / $_.Size) * 100, 2) }
}
```

**Explication** :
- `Size` : Taille totale du volume.
- `SizeRemaining` : Espace libre.

---

#### **2.2.2. Activité disque (lecture/écriture)**
```powershell
# Activité disque (nécessite des droits admin)
Get-Counter '\PhysicalDisk(_Total)\Disk Read Bytes/sec', '\PhysicalDisk(_Total)\Disk Write Bytes/sec' -Continuous |
    ForEach-Object {
        $read = [math]::Round($_.CounterSamples[0].CookedValue / 1KB, 2)
        $write = [math]::Round($_.CounterSamples[1].CookedValue / 1KB, 2)
        Write-Host "Lecture : $read Ko/s - Écriture : $write Ko/s"
        Start-Sleep -Seconds 1
    }
```

**Explication** :
- `-Continuous` : Met à jour en temps réel (Ctrl+C pour arrêter).
- `Start-Sleep` : Pause d’1 seconde entre chaque mise à jour.

---

### **2.3. Surveillance du réseau**
```powershell
# Trafic réseau (en Ko/s)
Get-Counter '\Network Interface(*)\Bytes Received/sec', '\Network Interface(*)\Bytes Sent/sec' -Continuous |
    ForEach-Object {
        $rx = [math]::Round(($_.CounterSamples | Where-Object { $_.Path -like '*Received*' }).CookedValue / 1KB, 2)
        $tx = [math]::Round(($_.CounterSamples | Where-Object { $_.Path -like '*Sent*' }).CookedValue / 1KB, 2)
        Write-Host "Réception : $rx Ko/s - Envoi : $tx Ko/s"
        Start-Sleep -Seconds 1
    }
```

---

### **2.4. Travaux pratiques : Script de surveillance en temps réel**
---
#### **Énoncé de l’exercice** :
**Objectif** :
Créer un script qui :
1. **Surveille en temps réel** (toutes les 2 secondes) :
   - Utilisation CPU (`% Processor Time`).
   - Mémoire disponible (`Available MBytes`).
   - Espace disque libre sur `C:` (en %).
2. **Affiche les valeurs** dans la console.
3. **Génère une alerte** (message en rouge) si :
   - CPU > 90% **ou**
   - Mémoire disponible < 10% **ou**
   - Espace disque libre sur `C:` < 20%.
4. **Journalise les alertes** dans un fichier (`C:\Temp\ResourceAlerts.log`).

**Contraintes** :
- Utiliser `Get-Counter`.
- La surveillance doit durer **1 minute** (ou jusqu’à ce que l’utilisateur appuie sur Ctrl+C).

---
#### **Corrigé** :
```powershell
<#
.SYNOPSIS
    Surveille les ressources système en temps réel et génère des alertes.

.DESCRIPTION
    Ce script surveille le CPU, la mémoire et l'espace disque toutes les 2 secondes,
    et génère des alertes si les seuils sont dépassés.
#>
$logFile = "C:\Temp\ResourceAlerts.log"
$duration = 60  # Durée en secondes
$interval = 2   # Intervalle entre les mesures

# Fonction pour journaliser les alertes
function Write-Alert {
    param ([string]$message)
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    Write-Host $message -ForegroundColor Red
    Add-Content -Path $logFile -Value "[$timestamp] ALERT: $message"
}

# Boucle de surveillance
$endTime = (Get-Date).AddSeconds($duration)
while ((Get-Date) -lt $endTime) {
    # Récupérer les métriques
    $cpu = (Get-Counter '\Processor(_Total)\% Processor Time').CounterSamples.CookedValue
    $mem = (Get-Counter '\Memory\Available MBytes').CounterSamples.CookedValue
    $disk = (Get-Volume -DriveLetter C).SizeRemaining / (Get-Volume -DriveLetter C).Size * 100

    # Afficher les valeurs
    Write-Host "`n[$(Get-Date -Format 'HH:mm:ss')] CPU: $([math]::Round($cpu, 1))% | Mem: $([math]::Round($mem, 1)) Mo | Disk C: $([math]::Round($disk, 1))%"

    # Vérifier les seuils
    if ($cpu -gt 90) { Write-Alert "CPU > 90% (Actuel: $cpu%)" }
    if ($mem -lt 1024) { Write-Alert "Mémoire disponible < 10% (Actuel: $mem Mo)" }  # 1024 Mo = ~1 Go
    if ($disk -lt 20) { Write-Alert "Espace disque C: < 20% (Actuel: $([math]::Round($disk, 1))%)" }

    Start-Sleep -Seconds $interval
}

Write-Host "`nSurveillance terminée. Les alertes ont été enregistrées dans $logFile"
```

**Explications** :
- `Get-Volume -DriveLetter C` : Récupère les infos du disque `C:`.
- `Write-Host ... -ForegroundColor Red` : Affiche les alertes en rouge.
- `Add-Content` : Journalise les alertes dans un fichier.

---