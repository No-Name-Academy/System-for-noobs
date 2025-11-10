## **1. Planification de tâches avec PowerShell**
### **1.1. Introduction aux tâches planifiées**
Les tâches planifiées permettent d’exécuter des scripts ou des commandes à des moments précis ou selon des déclencheurs spécifiques.
PowerShell offre deux méthodes principales :
- **Planificateur de tâches Windows** (`Task Scheduler`) via `SchTasks.exe` ou `Register-ScheduledJob`.
- **Jobs PowerShell** (`Scheduled Jobs`) avec les cmdlets dédiées.

---

### **1.1.1. Utilisation de `Register-ScheduledJob`**
**Cmdlets clés** :
| Cmdlet                     | Description                                      |
|----------------------------|--------------------------------------------------|
| `Register-ScheduledJob`    | Crée une tâche planifiée.                        |
| `Get-ScheduledJob`         | Liste les tâches planifiées.                     |
| `Start-Job`                | Lance un job manuellement.                       |
| `Get-Job`                  | Affiche l’état des jobs.                         |
| `Receive-Job`              | Récupère les résultats d’un job.                 |

**Exemple : Créer une tâche planifiée pour exécuter un script quotidiennement**
```powershell
# Créer une action pour exécuter un script
$action = New-ScheduledJobAction -Execute "PowerShell.exe" -Argument "-NoProfile -File `"C:\Scripts\NettoyageDisque.ps1`""

# Définir un déclencheur quotidien à 2h du matin
$trigger = New-JobTrigger -Daily -At 2am

# Enregistrer la tâche
Register-ScheduledJob -Name "NettoyageDisqueQuotidien" -Action $action -Trigger $trigger -MaxResultCount 30

# Vérifier que la tâche est créée
Get-ScheduledJob -Name "NettoyageDisqueQuotidien"
```

---

### **1.1.2. Utilisation de `SchTasks.exe`**
Pour une compatibilité avec les anciennes versions de Windows :
```powershell
# Créer une tâche avec SchTasks
$command = 'schtasks /create /tn "NettoyageDisqueQuotidien" /tr "powershell.exe -NoProfile -File `"C:\Scripts\NettoyageDisque.ps1`"" /sc daily /st 02:00'
Invoke-Expression $command
```

---

### **1.1.3. Gestion des tâches existantes**
```powershell
# Lister toutes les tâches planifiées
Get-ScheduledJob

# Démarrer une tâche manuellement
Start-ScheduledJob -Name "NettoyageDisqueQuotidien"

# Supprimer une tâche
Unregister-ScheduledJob -Name "NettoyageDisqueQuotidien" -Confirm:$false
```

---

### **1.1.4. Travaux pratiques : Planifier un script de nettoyage des logs**
**Script : `NettoyageLogs.ps1`**
```powershell
<#
.SYNOPSIS
    Supprime les fichiers de logs plus anciens que 30 jours dans C:\Logs.
#>
$logPath = "C:\Logs"
$daysToKeep = 30
$cutoffDate = (Get-Date).AddDays(-$daysToKeep)

Get-ChildItem -Path $logPath -File -Recurse | Where-Object {
    $_.LastWriteTime -lt $cutoffDate
} | ForEach-Object {
    Remove-Item -Path $_.FullName -Force
    Write-Output "Suppression de $($_.FullName)"
}
```

**Planification** :
```powershell
$action = New-ScheduledJobAction -Execute "PowerShell.exe" -Argument "-NoProfile -File `"C:\Scripts\NettoyageLogs.ps1`""
$trigger = New-JobTrigger -Daily -At 3am
Register-ScheduledJob -Name "NettoyageLogs" -Action $action -Trigger $trigger
```

---