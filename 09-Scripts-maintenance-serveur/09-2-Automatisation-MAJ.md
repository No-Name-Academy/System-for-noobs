## **2. Automatisation des mises à jour**
### **2.1. Installation des mises à jour Windows**
**Cmdlets clés** :
| Cmdlet                     | Description                                      |
|----------------------------|--------------------------------------------------|
| `Get-WindowsUpdateLog`     | Affiche le journal des mises à jour.             |
| `Install-WindowsUpdate`    | Installe les mises à jour (nécessite le module PSWindowsUpdate). |

**Installation du module PSWindowsUpdate** :
```powershell
Install-Module -Name PSWindowsUpdate -Force
Import-Module PSWindowsUpdate
```

**Exemple : Installer les mises à jour critiques**
```powershell
# Lister les mises à jour disponibles
Get-WindowsUpdate

# Installer les mises à jour critiques
Install-WindowsUpdate -MicrosoftUpdate -AcceptAll -AutoReboot
```

---

### **2.2. Planification des mises à jour**
```powershell
$action = New-ScheduledJobAction -Execute "PowerShell.exe" -Argument "-NoProfile -Command `"Install-WindowsUpdate -MicrosoftUpdate -AcceptAll -AutoReboot`""
$trigger = New-JobTrigger -Weekly -DaysOfWeek Saturday -At 2am
Register-ScheduledJob -Name "InstallWindowsUpdates" -Action $action -Trigger $trigger
```

---