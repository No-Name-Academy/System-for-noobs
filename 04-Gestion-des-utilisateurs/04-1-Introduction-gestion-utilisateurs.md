## **1. Introduction à la gestion des utilisateurs locaux**

### **1.1. Présentation des cmdlets dédiées**
PowerShell propose des cmdlets spécifiques pour gérer les **utilisateurs locaux** et les **groupes locaux** sur un système Windows.
Ces cmdlets sont disponibles via le module **Microsoft.PowerShell.LocalAccounts** (intégré à Windows 10/Server 2016 et versions ultérieures).

| Cmdlet                     | Description                                      |
|----------------------------|--------------------------------------------------|
| `Get-LocalUser`            | Liste les utilisateurs locaux.                   |
| `New-LocalUser`            | Crée un nouvel utilisateur local.                |
| `Set-LocalUser`            | Modifie un utilisateur local existant.           |
| `Remove-LocalUser`         | Supprime un utilisateur local.                   |
| `Add-LocalGroupMember`     | Ajoute un utilisateur à un groupe local.         |
| `Get-LocalGroup`           | Liste les groupes locaux.                        |
| `New-LocalGroup`           | Crée un nouveau groupe local.                    |
| `Remove-LocalGroupMember`  | Retire un utilisateur d’un groupe local.         |

**Vérification du module** :
```powershell
Get-Module -Name Microsoft.PowerShell.LocalAccounts -ListAvailable
```

---