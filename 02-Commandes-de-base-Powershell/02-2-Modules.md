## **2. Modules et importation de modules**

### **2.1. Qu’est-ce qu’un module PowerShell ?**
Un **module** est un ensemble de cmdlets, fonctions, variables et autres ressources regroupées pour étendre les fonctionnalités de PowerShell.
- **Modules intégrés** : Fournis avec Windows (ex: `ActiveDirectory`, `Hyper-V`).
- **Modules tiers** : Téléchargés depuis des galeries comme [PowerShell Gallery](https://www.powershellgallery.com/).

#### **2.1.1. Lister les modules disponibles**
```powershell
# Lister tous les modules installés
Get-Module -ListAvailable

# Lister les modules actuellement chargés
Get-Module
```

#### **2.1.2. Importer un module**
```powershell
# Importer un module (ex: ActiveDirectory)
Import-Module ActiveDirectory

# Vérifier que le module est chargé
Get-Module -Name ActiveDirectory
```

#### **2.1.3. Installer un module depuis PowerShell Gallery**
```powershell
# Installer un module (nécessite une connexion Internet)
Install-Module -Name PSReadLine -Force -AllowClobber

# Importer le module
Import-Module PSReadLine
```

---

### **2.2. Mise en pratique : Utilisation des modules**
**Objectif** : Étendre PowerShell avec des modules pour administrer des rôles serveurs.

#### **Exercice 4 : Utilisation du module ActiveDirectory**
1. Importer le module `ActiveDirectory` (disponible sur les contrôleurs de domaine) :
   ```powershell
   Add-WindowsFeature -Name "RSAT-AD-PowerShell" –IncludeAllSubFeature
   Enable-WindowsOptionalFeature -Online -FeatureName RSATClient-Roles-AD-Powershell
   Import-Module ActiveDirectory
   Get-Command -Module ActiveDirectory
   ```
2. Lister tous les utilisateurs du domaine :
   ```powershell
   Get-ADUser -Filter *
   ```
3. Rechercher un utilisateur spécifique :
   ```powershell
   Get-ADUser -Identity "bmonteil" -Properties *
   ```

#### **Exercice 5 : Installation et utilisation d’un module tiers**
1. Installer le module `AzureAD` (pour gérer Azure Active Directory) :
   ```powershell
   Install-Module -Name AzureAD -Force
   ```
2. Importer le module :
   ```powershell
   Import-Module AzureAD
   ```
3. Se connecter à Azure AD :
   ```powershell
   Connect-AzureAD
   ```