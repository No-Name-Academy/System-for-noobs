## **1. Introduction au module Active Directory pour PowerShell**

### **1.1. Présentation du module ActiveDirectory**
Le module **ActiveDirectory** est un outil puissant pour administrer **Active Directory (AD)** depuis PowerShell.
Il est installé automatiquement sur les **contrôleurs de domaine** Windows Server et peut être ajouté via les **RSAT (Remote Server Administration Tools)** sur les postes clients.

**Vérification de l’installation** :
```powershell
# Lister les modules disponibles
Get-Module -ListAvailable -Name ActiveDirectory

# Importer le module (si nécessaire)
Import-Module ActiveDirectory
```

**Si le module n’est pas installé** :
```powershell
# Installer les RSAT (sur Windows 10/11 ou Windows Server)
Add-WindowsFeature -Name "RSAT-AD-PowerShell"  # Pour Windows Server
# Ou via "Ajouter des fonctionnalités" dans le Gestionnaire de serveur.
```

---