## **3. Travaux pratiques : Créer un script pour surveiller les ressources critiques**

---
### **Énoncé du projet** :
**Objectif** :
Créer un **script complet** qui :
1. **Surveille les ressources critiques** :
   - CPU (> 90% pendant 5 minutes).
   - Mémoire disponible (< 10%).
   - Espace disque sur `C:` (< 20%).
   - Erreurs dans le journal **System** (nouveaux événements d’erreur).
2. **Envoie un email d’alerte** si un seuil est dépassé (utiliser `Send-MailMessage`).
3. **Journalise toutes les actions** dans un fichier (`C:\Temp\ServerMonitor.log`).
4. **S’exécute en arrière-plan** comme une tâche planifiée (toutes les 15 minutes).

**Contraintes** :
- Le script doit être **modulaire** (fonctions pour chaque type de surveillance).
- Utiliser des **paramètres** pour configurer les seuils et l’email de destination.
- **Ne pas envoyer plus d’un email par heure** pour le même type d’alerte.

---
### **Corrigé** :
```powershell
<#
.SYNOPSIS
    Script de surveillance avancée des ressources serveur.

.DESCRIPTION
    Ce script surveille le CPU, la mémoire, l'espace disque et les erreurs système.
    Il envoie des alertes par email et journalise les actions.

.PARAMETER SmtpServer
    Serveur SMTP pour l'envoi des emails.

.PARAMETER FromEmail
    Adresse email de l'expéditeur.

.PARAMETER ToEmail
    Adresse email du destinataire.

.PARAMETER LogFile
    Chemin du fichier de log.

.EXAMPLE
    .\Monitor-ServerResources.ps1 -SmtpServer "smtp.domaine.com" -FromEmail "monitor@domaine.com" -ToEmail "admin@domaine.com"
#>
param (
    [Parameter(Mandatory=$true)]
    [string]$SmtpServer,

    [Parameter(Mandatory=$true)]
    [string]$FromEmail,

    [Parameter(Mandatory=$true)]
    [string]$ToEmail,

    [string]$LogFile = "C:\Temp\ServerMonitor.log"
)

# Import du module pour Send-MailMessage (si PS < 7)
if (-not (Get-Command Send-MailMessage -ErrorAction SilentlyContinue)) {
    Write-Host "Le module Send-MailMessage n'est pas disponible. Utilisez PowerShell 5.1 ou installez le module 'PSSmtp'."
    exit
}

# Variables globales
$lastAlertTime = @{}

# Fonction pour journaliser les actions
function Write-Log {
    param ([string]$message)
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $logMessage = "[$timestamp] $message"
    Write-Output $logMessage
    Add-Content -Path $LogFile -Value $logMessage
}

# Fonction pour envoyer une alerte (max 1/h par type)
function Send-Alert {
    param (
        [string]$alertType,
        [string]$message
    )

    $now = Get-Date
    if (-not $lastAlertTime.ContainsKey($alertType) -or ($now - $lastAlertTime[$alertType]).TotalHours -ge 1) {
        $subject = "[ALERT] $alertType sur $($env:COMPUTERNAME)"
        Send-MailMessage -SmtpServer $SmtpServer -From $FromEmail -To $ToEmail -Subject $subject -Body $message
        Write-Log "ALERT EMAIL SENT: $subject - $message"
        $lastAlertTime[$alertType] = $now
    }
}

# Fonction pour surveiller le CPU
function Monitor-CPU {
    $cpu = (Get-Counter '\Processor(_Total)\% Processor Time').CounterSamples.CookedValue
    if ($cpu -gt 90) {
        $message = "CPU > 90% (Actuel: $cpu%)"
        Write-Log "ALERT: $message"
        Send-Alert -alertType "High CPU" -message $message
    }
}

# Fonction pour surveiller la mémoire
function Monitor-Memory {
    $mem = (Get-Counter '\Memory\Available MBytes').CounterSamples.CookedValue
    $totalMem = (Get-CimInstance Win32_ComputerSystem).TotalPhysicalMemory / 1MB
    $memPercent = ($mem / $totalMem) * 100
    if ($memPercent -lt 10) {
        $message = "Mémoire disponible < 10% (Actuel: $([math]::Round($memPercent, 1))% - $mem Mo)"
        Write-Log "ALERT: $message"
        Send-Alert -alertType "Low Memory" -message $message
    }
}

# Fonction pour surveiller l'espace disque
function Monitor-Disk {
    $disk = Get-Volume -DriveLetter C
    $freePercent = ($disk.SizeRemaining / $disk.Size) * 100
    if ($freePercent -lt 20) {
        $message = "Espace disque C: < 20% (Actuel: $([math]::Round($freePercent, 1))%)"
        Write-Log "ALERT: $message"
        Send-Alert -alertType "Low Disk Space" -message $message
    }
}

# Fonction pour surveiller les erreurs système
function Monitor-Errors {
    $lastCheckTime = (Get-Date).AddMinutes(-15)
    $newErrors = Get-WinEvent -LogName System -FilterHashtable @{
        StartTime = $lastCheckTime
        Level = 2  # Erreurs
    }

    if ($newErrors) {
        $message = "$($newErrors.Count) nouvelles erreurs dans le journal System.`nPremière erreur : $($newErrors[0].Message)"
        Write-Log "ALERT: $message"
        Send-Alert -alertType "System Errors" -message $message
    }
}

# Exécution principale
Write-Log "Début de la surveillance..."

# Surveiller chaque ressource
Monitor-CPU
Monitor-Memory
Monitor-Disk
Monitor-Errors

Write-Log "Fin de la surveillance."
```

---
### **Explications détaillées** :
1. **Paramètres** :
   - `$SmtpServer`, `$FromEmail`, `$ToEmail` : Configurent l’envoi des emails.
   - `$LogFile` : Chemin du fichier de log (par défaut : `C:\Temp\ServerMonitor.log`).

2. **Fonction `Write-Log`** :
   - Journalise les actions avec un timestamp.

3. **Fonction `Send-Alert`** :
   - Envoie un email **uniquement si la dernière alerte du même type date de plus d’1 heure** (`$lastAlertTime`).
   - Utilise `Send-MailMessage` (nécessite un serveur SMTP configuré).

4. **Fonctions de surveillance** :
   - `Monitor-CPU` : Vérifie si l’utilisation CPU dépasse 90%.
   - `Monitor-Memory` : Vérifie si la mémoire disponible est < 10%.
   - `Monitor-Disk` : Vérifie si l’espace disque libre sur `C:` est < 20%.
   - `Monitor-Errors` : Vérifie les nouvelles erreurs dans le journal **System** (depuis la dernière exécution).

5. **Exécution** :
   - Le script peut être **planifié** avec `Register-ScheduledJob` pour s’exécuter toutes les 15 minutes :
     ```powershell
     $action = New-ScheduledJobAction -Execute "PowerShell.exe" -Argument "-NoProfile -File `"C:\Scripts\Monitor-ServerResources.ps1`" -SmtpServer `"smtp.domaine.com`" -FromEmail `"monitor@domaine.com`" -ToEmail `"admin@domaine.com`""
     $trigger = New-JobTrigger -Once -At (Get-Date) -RepetitionInterval (New-TimeSpan -Minutes 15) -RepetitionDuration (New-TimeSpan -Days 365)
     Register-ScheduledJob -Name "ServerMonitor" -Action $action -Trigger $trigger
     ```

---