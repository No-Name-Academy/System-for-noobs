## **2. Gestion des processus et des services**

### **2.1. Gestion avancée des processus**
#### **2.1.1. Lister et filtrer les processus**
```powershell
# Lister les processus par utilisation CPU
Get-Process | Sort-Object -Property CPU -Descending | Select-Object -First 10

# Filtrer les processus par nom
Get-Process | Where-Object { $_.ProcessName -like "*edge*" }
```

#### **2.1.2. Arrêter et démarrer des processus**
```powershell
# Arrêter un processus
Stop-Process -Name "notepad" -Force

# Démarrer une application
Start-Process -FilePath "C:\Windows\System32\notepad.exe"
```

---

#### **2.1.3. Surveiller les processus**
```powershell
# Surveiller l’utilisation CPU d’un processus
$process = Get-Process -Name "chrome"
$process | Select-Object Id, ProcessName, CPU, @{Name="Mem(Mo)"; Expression={[math]::Round($_.WorkingSet / 1MB, 2)}}
```

---

### **2.2. Gestion avancée des services**
#### **2.2.1. Lister et filtrer les services**
```powershell
# Lister les services démarrés automatiquement
Get-Service | Where-Object { $_.StartType -eq "Automatic" -and $_.Status -eq "Running" }

# Exporter la liste des services dans un CSV
Get-Service | Export-Csv -Path "C:\Temp\services.csv" -NoTypeInformation
```

#### **2.2.2. Modifier le type de démarrage d’un service**
```powershell
# Changer le type de démarrage d’un service
Set-Service -Name "Spooler" -StartupType Automatic
```

---

#### **2.2.3. Redémarrer un service et ses dépendances**
```powershell
# Redémarrer un service et ses dépendances
$service = Get-Service -Name "Spooler"
$service.DependentServices | ForEach-Object { Restart-Service -Name $_.Name -Force }
Restart-Service -Name "Spooler" -Force
```

---

### **2.3. Travaux pratiques : Script de surveillance des services critiques**
**Objectif** :
Créer un script qui vérifie l’état des services critiques et les redémarre si nécessaire.

#### **Script : Surveillance des services critiques**
```powershell
# Définir la liste des services critiques
$servicesCritiques = @("DHCP", "DNS", "Spooler")

# Vérifier et redémarrer les services si nécessaire
foreach ($service in $servicesCritiques) {
    $svc = Get-Service -Name $service
    if ($svc.Status -ne "Running") {
        Write-Host "$service est arrêté. Tentative de redémarrage..."
        Start-Service -Name $service
        Add-Content -Path "C:\Temp\service_log.txt" -Value "$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss') - $service a été redémarré."
    }
}
```

---