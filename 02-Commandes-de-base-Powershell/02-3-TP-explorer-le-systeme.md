## **3. Travaux pratiques : Explorer le système avec PowerShell**

### **3.1. Scénario 1 : Audit des ressources système**
**Objectif** : Générer un rapport sur l’utilisation des ressources (CPU, mémoire, disque).

#### **Script : Rapport système**
```powershell
# Récupérer les informations système
$os = Get-ComputerInfo | Select-Object OsName, OsVersion, OsArchitecture
$cpu = Get-WmiObject -Class Win32_Processor | Select-Object Name, NumberOfCores, MaxClockSpeed
$mem = Get-WmiObject -Class Win32_ComputerSystem | Select-Object TotalPhysicalMemory
$disks = Get-WmiObject -Class Win32_LogicalDisk | Where-Object { $_.DriveType -eq 3 } | Select-Object DeviceID, VolumeName, @{Name="Size(GB)"; Expression={[math]::Round($_.Size / 1GB, 2)}}, @{Name="FreeSpace(GB)"; Expression={[math]::Round($_.FreeSpace / 1GB, 2)}}

# Afficher le rapport
Write-Host "`n--- Rapport système ---"
Write-Host "Système d'exploitation : $($os.OsName) $($os.OsVersion) ($($os.OsArchitecture))"
Write-Host "Processeur : $($cpu.Name) ($($cpu.NumberOfCores) coeurs, $($cpu.MaxClockSpeed) MHz)"
Write-Host "Mémoire totale : $([math]::Round($mem.TotalPhysicalMemory / 1GB, 2)) Go"
Write-Host "`nDisques :"
$disks | Format-Table -AutoSize

# Exporter le rapport dans un fichier
$rapport = @"
Système d'exploitation : $($os.OsName) $($os.OsVersion) ($($os.OsArchitecture))
Processeur : $($cpu.Name) ($($cpu.NumberOfCores) coeurs, $($cpu.MaxClockSpeed) MHz)
Mémoire totale : $([math]::Round($mem.TotalPhysicalMemory / 1GB, 2)) Go

Disques :
$($disks | Out-String)
"@
$rapport | Out-File -FilePath "C:\Temp\RapportSysteme.txt"
```

---

### **3.2. Scénario 2 : Gestion des logs Windows**
**Objectif** : Explorer et filtrer les journaux d’événements Windows.

#### **Exercice 6 : Lire les logs système**
1. Lister les journaux d’événements disponibles :
   ```powershell
   Get-EventLog -List
   ```
2. Afficher les 10 dernières erreurs du journal système :
   ```powershell
   Get-EventLog -LogName System -EntryType Error -Newest 10
   ```
3. Exporter les erreurs dans un fichier CSV :
   ```powershell
   Get-EventLog -LogName System -EntryType Error -Newest 50 | Export-Csv -Path "C:\Temp\ErreursSysteme.csv" -NoTypeInformation
   ```

---

### **3.3. Scénario 3 : Automatisation de tâches courantes**
**Objectif** : Créer un script pour nettoyer les fichiers temporaires.

#### **Script : Nettoyage des fichiers temporaires**
```powershell
# Définir le chemin des fichiers temporaires
$tempPath = "C:\Temp"

# Supprimer les fichiers plus anciens que 7 jours
Get-ChildItem -Path $tempPath -File | Where-Object { $_.LastWriteTime -lt (Get-Date).AddDays(-7) } | Remove-Item -Force -WhatIf

# Afficher un résumé
Write-Host "Nettoyage terminé. Les fichiers suivants auraient été supprimés :"
Get-ChildItem -Path $tempPath -File | Where-Object { $_.LastWriteTime -lt (Get-Date).AddDays(-7) } | Select-Object Name, LastWriteTime
```
**Note** : Retirer `-WhatIf` pour exécuter la suppression.