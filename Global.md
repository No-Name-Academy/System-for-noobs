# **I - Introduction à Powershell**
## **1. Qu’est-ce que PowerShell ? Historique et fonctionnalités**

### **1.1. Origines et évolution de PowerShell**
PowerShell est un **shell de ligne de commande** et un **langage de script** développé par Microsoft, conçu pour répondre aux limites des outils traditionnels comme `cmd.exe`. Son développement a été motivé par le besoin d’un outil moderne, flexible et puissant pour l’administration système sous Windows.

#### **1.1.1. Historique des versions**
| Version  | Année   | Principales innovations                                                                 |
|----------|---------|------------------------------------------------------------------------------------------|
| 1.0      | 2006    | Introduction des cmdlets, du pipeline d’objets et du langage de script.                  |
| 2.0      | 2009    | Intégration dans Windows 7/Server 2008 R2, ajout des jobs en arrière-plan et des modules. |
| 3.0      | 2012    | Amélioration des performances, introduction de `Scheduled Jobs` et de `CIM`.            |
| 4.0      | 2013    | Prise en charge de `Desired State Configuration (DSC)`.                                   |
| 5.1      | 2016    | Dernière version intégrée à Windows (Windows 10/Server 2016), ajout de `PowerShellGet`. |
| Core 6.0 | 2018    | Version open source et multiplateforme (Windows, Linux, macOS).                         |
| 7.x      | 2020+   | Compatibilité ascendante, performances accrues, et intégration avec .NET Core.          |

#### **1.1.2. Pourquoi PowerShell ?**
- **Automatisation** : Remplace les tâches manuelles répétitives par des scripts.
- **Gestion unifiée** : Interface commune pour administrer les systèmes locaux et distants.
- **Extensibilité** : Possibilité d’ajouter des modules pour étendre les fonctionnalités (Active Directory, Exchange, Azure, etc.).
- **Intégration avec .NET** : Accès direct aux bibliothèques .NET pour des fonctionnalités avancées.

---

### **1.2. Concepts fondamentaux**

#### **1.2.1. Les cmdlets**
Les **cmdlets** (prononcé "command-lets") sont des commandes spécialisées dans PowerShell. Elles suivent une nomenclature **Verbe-Nom** (ex: `Get-Process`, `Stop-Service`). Chaque cmdlet retourne un **objet** (et non du texte), ce qui permet un traitement avancé des données.

**Exemples de cmdlets courantes** :
| Cmdlet               | Description                                      |
|----------------------|--------------------------------------------------|
| `Get-Command`        | Liste toutes les cmdlets disponibles.            |
| `Get-Help`           | Affiche l’aide pour une cmdlet.                  |
| `Get-Process`        | Liste les processus en cours d’exécution.        |
| `Get-Service`        | Affiche les services Windows.                    |
| `Select-Object`      | Filtre les propriétés d’un objet.                |
| `Where-Object`       | Filtre les objets selon des critères.            |

#### **1.2.2. Le pipeline**
Le pipeline (`|`) permet de **transmettre les résultats d’une cmdlet à une autre** pour un traitement en chaîne. Contrairement aux shells traditionnels (comme `cmd.exe`), PowerShell transmet des **objets** plutôt que du texte, ce qui offre une grande flexibilité.

**Exemple** :
```powershell
# Lister tous les processus et filtrer ceux qui utilisent plus de 100 Mo de mémoire
Get-Process | Where-Object { $_.WorkingSet -gt 100MB } | Select-Object Name, Id, WorkingSet
```

#### **1.2.3. Travail avec des objets**
PowerShell manipule des **objets .NET**, ce qui permet d’accéder à leurs propriétés et méthodes. Par exemple :
```powershell
# Récupérer des informations détaillées sur un service
$service = Get-Service -Name "Spooler"
$service.Status  # Affiche le statut du service
$service.Stop()  # Arrête le service
```

---

### **1.3. Mise en pratique : Premières commandes**
**Objectif** : Se familiariser avec l’exécution de cmdlets et la manipulation des objets.

#### **Exercice 1 : Découverte des cmdlets de base**
1. Ouvrir une session PowerShell :
   - `Win + R` > Taper `powershell` > Valider.
2. Exécuter les commandes suivantes et analyser les résultats :
   ```powershell
   # Lister les cmdlets disponibles pour la gestion des services
   Get-Command -Verb Get -Noun Service

   # Afficher l’aide détaillée pour Get-Service
   Get-Help Get-Service -Detailed

   # Lister les services dont le statut est "Running"
   Get-Service | Where-Object { $_.Status -eq "Running" }

   # Afficher les 5 processus consommant le plus de mémoire
   Get-Process | Sort-Object -Property WorkingSet -Descending | Select-Object -First 5
   ```

#### **Exercice 2 : Redirection et export des résultats**
1. Exporter la liste des services dans un fichier CSV :
   ```powershell
   Get-Service | Export-Csv -Path "C:\Temp\Services.csv" -NoTypeInformation
   ```
2. Lire le contenu du fichier CSV :
   ```powershell
   Import-Csv -Path "C:\Temp\Services.csv" | Format-Table -AutoSize
   ```
## **2. Interface utilisateur : PowerShell ISE**

### **2.1. Présentation de PowerShell ISE**
**PowerShell ISE (Integrated Scripting Environment)** est un environnement graphique dédié à l’écriture, au test et au débogage de scripts PowerShell. Il est particulièrement adapté aux débutants grâce à ses fonctionnalités intégrées :
- **Éditeur de script** : Colorisation syntaxique, complétion automatique (IntelliSense).
- **Console interactive** : Exécution directe des commandes.
- **Débogueur** : Points d’arrêt, exécution pas à pas.

#### **2.1.1. Lancement de PowerShell ISE**
- Via le menu Démarrer : `Windows PowerShell > Windows PowerShell ISE`.
- Via la ligne de commande :
  ```powershell
  powershell_ise
  ```

#### **2.1.2. Fonctionnalités clés**
| Fonctionnalité          | Description                                                                 |
|-------------------------|-----------------------------------------------------------------------------|
| **Onglets**             | Permet d’ouvrir plusieurs scripts en parallèle.                           |
| **Exécution partielle** | Sélectionner une partie du script et appuyer sur **F8** pour l’exécuter.   |
| **IntelliSense**        | Complétion automatique des cmdlets et de leurs paramètres (tabulation).  |
| **Débogueur**           | Points d’arrêt, exécution pas à pas (F9 pour ajouter un point d’arrêt).     |

---

### **2.2. Mise en pratique : Écriture et exécution d’un script**

#### **Exercice 3 : Création d’un script simple**
1. Ouvrir **PowerShell ISE**.
2. Créer un nouveau fichier (`Fichier > Nouveau`) et copier le script suivant :
   ```powershell
   <#
   .SYNOPSIS
       Script pour lister et démarrer les services arrêtés.

   .DESCRIPTION
       Ce script identifie les services arrêtés et les démarre un par un.
   #>

   # Récupérer la liste des services arrêtés
   $stoppedServices = Get-Service | Where-Object { $_.Status -eq "Stopped" }

   # Démarrer chaque service et afficher un message
   foreach ($service in $stoppedServices) {
       Write-Host "Démarrage du service : $($service.DisplayName)"
       Start-Service -Name $service.Name
   }

   # Afficher un résumé
   Write-Host "`n$($stoppedServices.Count) service(s) démarré(s)." -ForegroundColor Green
   ```
3. Enregistrer le script sous `C:\Scripts\DemarrerServicesArrêtes.ps1`.
4. Exécuter le script en appuyant sur **F5** ou via le bouton **Exécuter**.

#### **Exercice 4 : Utilisation des points d’arrêt pour le débogage**
1. Ajouter un point d’arrêt à la ligne `Start-Service` (clic gauche dans la marge ou **F9**).
2. Lancer le script en mode débogage (**F5**).
3. Utiliser **F10** pour exécuter le script pas à pas et observer les valeurs des variables.

---

### **2.3. Bonnes pratiques pour l’utilisation de l’ISE**
- **Commentaires** : Utiliser `#` pour les commentaires sur une ligne et `<# ... #>` pour les blocs de commentaires.
- **Indentation** : Structurer le code pour une meilleure lisibilité.
- **Sauvegarde** : Enregistrer régulièrement les scripts (`Ctrl + S`).
- **Tests** : Exécuter des portions de code avant de lancer le script entier.

---
## **3. Synthèse et exercices d’approfondissement**

### **3.1. Résumé des notions clés**
- PowerShell est un outil d’automatisation basé sur des **cmdlets** et un **pipeline d’objets**.
- Les cmdlets suivent une nomenclature **Verbe-Nom** et retournent des objets exploitables.
- **PowerShell ISE** est l’environnement idéal pour débuter avec les scripts.

### **3.2. Exercices supplémentaires**
1. **Script d’inventaire système** :
   Créer un script qui génère un rapport des disques locaux (lettre, capacité, espace libre) et l’exporte dans un fichier CSV.
   ```powershell
   Get-Volume | Select-Object DriveLetter, Size, SizeRemaining |
   Export-Csv -Path "C:\Temp\InventaireDisques.csv" -NoTypeInformation
   ```

2. **Gestion des processus** :
   Écrire un script qui tue tous les processus notepad.exe ouverts.
   ```powershell
   Stop-Process -Name "notepad" -Force
   ```

3. **Journalisation** :
   Modifier le script de l’**Exercice 3** pour enregistrer les actions dans un fichier log (`C:\Temp\ServiceLog.txt`).

---
# **II - Commandes de base PowerShell**
## **1. Cmdlets : Structure et syntaxe**

### **1.1. Anatomie d’une cmdlet**
Les cmdlets PowerShell suivent une **nomenclature standardisée** : **Verbe-Nom**. Cette convention rend PowerShell intuitif et cohérent.

#### **1.1.1. Structure d’une cmdlet**
```powershell
Verbe-Nom [-Paramètre1 Valeur1] [-Paramètre2 Valeur2] ...
```
- **Verbe** : Décrit l’action (ex: `Get`, `Set`, `Start`, `Stop`).
- **Nom** : Décrit l’objet sur lequel agit la cmdlet (ex: `Service`, `Process`, `Item`).
- **Paramètres** : Permettent de préciser le comportement de la cmdlet.

**Exemples** :
| Cmdlet          | Description                                  |
|-----------------|----------------------------------------------|
| `Get-Process`   | Liste les processus en cours.                |
| `Stop-Service`  | Arrête un service Windows.                   |
| `Set-Item`      | Modifie la valeur d’un élément (fichier, clé de registre). |

#### **1.1.2. Paramètres courants**
| Paramètre       | Description                                  | Exemple                                      |
|-----------------|----------------------------------------------|----------------------------------------------|
| `-Name`         | Spécifie le nom d’un objet.                  | `Get-Service -Name "Spooler"`                |
| `-Path`         | Indique un chemin (fichier, dossier, registre). | `Get-ChildItem -Path "C:\Temp"`           |
| `-Force`        | Force l’exécution (ignore les avertissements). | `Stop-Process -Name "notepad" -Force`     |
| `-WhatIf`       | Simule l’exécution sans appliquer les changements. | `Remove-Item -Path "C:\Temp\fichier.txt" -WhatIf` |
| `-Confirm`      | Demande une confirmation avant exécution.    | `Stop-Service -Name "Spooler" -Confirm`     |

---

### **1.2. Syntaxe avancée**
#### **1.2.1. Alias**
PowerShell permet d’utiliser des **alias** pour raccourcir les commandes courantes.
**Exemples** :
| Alias   | Cmdlet équivalente       |
|---------|--------------------------|
| `gcm`   | `Get-Command`            |
| `ps`    | `Get-Process`            |
| `ls`    | `Get-ChildItem`          |
| `dir`   | `Get-ChildItem`          |

**Afficher la liste des alias** :
```powershell
Get-Alias
```

#### **1.2.2. Utilisation des guillemets**
- **Guillemets simples (`' '`)** : Chaîne littérale (pas d’interprétation des variables).
  ```powershell
  Write-Host 'La variable $var ne sera pas interprétée.'
  ```
- **Guillemets doubles (`" "`)** : Interprète les variables et les caractères spéciaux.
  ```powershell
  $nom = "Monteil"
  Write-Host "Bonjour, $nom !"
  ```

#### **1.2.3. Caractères spéciaux**
| Caractère | Description                     | Exemple                          |
|-----------|---------------------------------|----------------------------------|
| `$`       | Préfixe pour les variables.     | `$maVariable = "Valeur"`         |
| `@`       | Tableau ou "splat" des paramètres. | `@(1, 2, 3)` ou `@params = @{}`  |
| `` ` ``   | Caractère d’échappement.        | `` Get-ChildItem `"Mon Dossier`" `` |

---

### **1.3. Mise en pratique : Utilisation des cmdlets de base**
**Objectif** : Explorer le système avec des cmdlets courantes.

#### **Exercice 1 : Gestion des processus**
1. Lister tous les processus :
   ```powershell
   Get-Process
   ```
2. Filtrer les processus par nom :
   ```powershell
   Get-Process -Name "chrome"
   ```
3. Arrêter un processus (ex: `notepad`) :
   ```powershell
   Stop-Process -Name "notepad" -Force
   ```

#### **Exercice 2 : Gestion des services**
1. Lister tous les services :
   ```powershell
   Get-Service
   ```
2. Filtrer les services en cours d’exécution :
   ```powershell
   Get-Service | Where-Object { $_.Status -eq "Running" }
   ```
3. Démarrer un service arrêté (ex: `Spooler`) :
   ```powershell
   Start-Service -Name "Spooler"
   ```

#### **Exercice 3 : Gestion des fichiers et dossiers**
1. Lister les fichiers dans `C:\Temp` :
   ```powershell
   Get-ChildItem -Path "C:\Temp"
   ```
2. Créer un nouveau dossier :
   ```powershell
   New-Item -Path "C:\Temp\NouveauDossier" -ItemType Directory
   ```
3. Supprimer un fichier (avec confirmation) :
   ```powershell
   Remove-Item -Path "C:\Temp\fichier.txt" -Confirm
   ```
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

   Get-WmiObject -Class Win32_Product | Select-Object Name, Version | Export-Csv -Path "C:\Temp\LogicielsInstalles.csv" -NoTypeInformation
# **III - Navigation dans le système**
## **1. Manipulation des fichiers et des dossiers**

### **1.1. Introduction aux fournisseurs PowerShell**
PowerShell utilise des **fournisseurs** (Providers) pour accéder à différentes sources de données (fichiers, registre, certificats, etc.).
Pour lister les fournisseurs disponibles :
```powershell
Get-PSProvider
```
Le fournisseur **FileSystem** est utilisé pour manipuler les fichiers et dossiers.

---

### **1.2. Navigation dans l’arborescence des fichiers**
#### **1.2.1. Commandes de base**
| Cmdlet               | Description                                      | Exemple                                      |
|----------------------|--------------------------------------------------|----------------------------------------------|
| `Set-Location`       | Change le répertoire courant (`cd`).             | `Set-Location "C:\Temp"` ou `cd C:\Temp`     |
| `Get-Location`       | Affiche le répertoire courant (`pwd`).           | `Get-Location` ou `pwd`                      |
| `Get-ChildItem`      | Liste les fichiers et dossiers (`dir` ou `ls`).  | `Get-ChildItem` ou `ls`                      |
| `Push-Location`      | Sauvegarde le répertoire courant (`pushd`).       | `Push-Location`                              |
| `Pop-Location`       | Retourne au répertoire sauvegardé (`popd`).      | `Pop-Location`                               |

**Exemple** :
```powershell
# Naviguer vers C:\Temp
Set-Location -Path "C:\Temp"

# Lister les fichiers et dossiers
Get-ChildItem

# Retourner au répertoire précédent
Pop-Location
```

---

#### **1.2.2. Filtrer les résultats**
| Paramètre            | Description                                      | Exemple                                      |
|----------------------|--------------------------------------------------|----------------------------------------------|
| `-File`              | Affiche uniquement les fichiers.                 | `Get-ChildItem -File`                        |
| `-Directory`         | Affiche uniquement les dossiers.                 | `Get-ChildItem -Directory`                   |
| `-Filter`            | Filtre par nom (supporte les wildcards).          | `Get-ChildItem -Filter "*.txt"`              |
| `-Recurse`           | Liste récursivement les sous-dossiers.           | `Get-ChildItem -Recurse -File`               |

**Exercice 1** :
Lister tous les fichiers `.log` dans `C:\Temp` et ses sous-dossiers :
```powershell
Get-ChildItem -Path "C:\Temp" -Filter "*.log" -Recurse
```

---

### **1.3. Création, suppression et modification**
#### **1.3.1. Créer des fichiers et dossiers**
| Cmdlet               | Description                                      | Exemple                                      |
|----------------------|--------------------------------------------------|----------------------------------------------|
| `New-Item`           | Crée un fichier ou un dossier.                   | `New-Item -Path "C:\Temp\NouveauDossier" -ItemType Directory` |
| `Remove-Item`        | Supprime un fichier ou un dossier.               | `Remove-Item -Path "C:\Temp\fichier.txt"`    |
| `Rename-Item`        | Renomme un fichier ou un dossier.                | `Rename-Item -Path "ancien.txt" -NewName "nouveau.txt"` |
| `Copy-Item`          | Copie un fichier ou un dossier.                  | `Copy-Item -Path "source.txt" -Destination "C:\Temp\destination.txt"` |
| `Move-Item`          | Déplace un fichier ou un dossier.                | `Move-Item -Path "fichier.txt" -Destination "C:\Temp\fichier_deplace.txt"` |

**Exercice 2** :
Créer une arborescence de dossiers pour un projet :
```powershell
$dossiers = @("ProjetA", "ProjetA\Docs", "ProjetA\Scripts", "ProjetA\Logs")
foreach ($dossier in $dossiers) {
    New-Item -Path "C:\Temp\$dossier" -ItemType Directory -Force
}
```

---

#### **1.3.2. Lire et écrire dans des fichiers**
| Cmdlet               | Description                                      | Exemple                                      |
|----------------------|--------------------------------------------------|----------------------------------------------|
| `Get-Content`        | Lit le contenu d’un fichier.                     | `Get-Content -Path "C:\Temp\fichier.txt"`    |
| `Set-Content`        | Écrit ou remplace le contenu d’un fichier.       | `Set-Content -Path "C:\Temp\fichier.txt" -Value "Bonjour, PowerShell !"` |
| `Add-Content`        | Ajoute du contenu à un fichier.                  | `Add-Content -Path "C:\Temp\fichier.txt" -Value "Nouvelle ligne"` |

**Exercice 3** :
Créer un fichier de log et y ajouter des entrées :
```powershell
$logFile = "C:\Temp\script_log.txt"
$timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
Add-Content -Path $logFile -Value "$timestamp - Début du script"
Add-Content -Path $logFile -Value "$timestamp - Action 1 terminée"
```

---

### **1.4. Gestion des attributs et permissions**
#### **1.4.1. Modifier les attributs d’un fichier**
```powershell
# Ajouter l’attribut "Lecture seule"
Set-ItemProperty -Path "C:\Temp\fichier.txt" -Name IsReadOnly -Value $true

# Vérifier les attributs
Get-ItemProperty -Path "C:\Temp\fichier.txt" -Name Attributes
```

#### **1.4.2. Gérer les permissions (ACL)**
```powershell
# Récupérer les permissions actuelles
$acl = Get-Acl -Path "C:\Temp\fichier.txt"

# Ajouter une permission pour un utilisateur
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule("DOM\bmonteil", "FullControl", "Allow")
$acl.SetAccessRule($rule)
Set-Acl -Path "C:\Temp\fichier.txt" -AclObject $acl
```

---

### **1.5. Travaux pratiques : Script de gestion des fichiers**
**Objectif** :
Écrire un script pour nettoyer les fichiers temporaires plus anciens que 7 jours et archiver les fichiers `.log` dans un dossier dédié.

#### **Script : Nettoyage et archivage des fichiers**
```powershell
# Définir les chemins
$tempPath = "C:\Temp"
$archivePath = "C:\Temp\Archive"
$logFile = "C:\Temp\nettoyage_log.txt"

# Créer le dossier d’archive s’il n’existe pas
if (-not (Test-Path -Path $archivePath)) {
    New-Item -Path $archivePath -ItemType Directory | Out-Null
}

# Nettoyer les fichiers temporaires
Get-ChildItem -Path $tempPath -File | Where-Object {
    $_.LastWriteTime -lt (Get-Date).AddDays(-7)
} | ForEach-Object {
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    Add-Content -Path $logFile -Value "$timestamp - Suppression de $($_.Name)"
    Remove-Item -Path $_.FullName -Force
}

# Archiver les fichiers .log
Get-ChildItem -Path $tempPath -Filter "*.log" | ForEach-Object {
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $dest = Join-Path -Path $archivePath -ChildPath $_.Name
    Add-Content -Path $logFile -Value "$timestamp - Archivage de $($_.Name) vers $dest"
    Move-Item -Path $_.FullName -Destination $dest -Force
}

Write-Host "Nettoyage et archivage terminés. Voir $logFile pour les détails."
```

---
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
## **3. Travaux pratiques : Écrire un script pour naviguer et gérer les fichiers**

### **3.1. Scénario : Script d’inventaire et de nettoyage**
**Objectif** :
Créer un script qui :
1. Parcourt un dossier et ses sous-dossiers.
2. Génère un rapport des fichiers par extension.
3. Supprime les fichiers temporaires (.tmp) plus anciens que 30 jours.
4. Archive les fichiers .log dans un dossier dédié.

#### **Script complet : Gestion des fichiers et des logs**
```powershell
<#
.SYNOPSIS
    Script pour inventorier, nettoyer et archiver les fichiers.

.DESCRIPTION
    Ce script parcourt un dossier, génère un rapport des fichiers par extension,
    supprime les fichiers temporaires anciens et archive les fichiers .log.
#>

# Paramètres
$rootPath = "C:\Temp"
$archivePath = "C:\Temp\Archive_Logs"
$logFile = "C:\Temp\inventaire_log.txt"
$daysToKeep = 30

# Créer le dossier d’archive s’il n’existe pas
if (-not (Test-Path -Path $archivePath)) {
    New-Item -Path $archivePath -ItemType Directory | Out-Null
}

# Fonction pour générer un rapport des fichiers par extension
function Generate-FileReport {
    param($path)
    $report = Get-ChildItem -Path $path -File -Recurse | Group-Object -Property Extension
    $report | ForEach-Object {
        $extension = if ($_.Name -eq "") { "Sans extension" } else { $_.Name }
        Add-Content -Path $logFile -Value "Extension : $extension - Nombre de fichiers : $($_.Count)"
    }
}

# Fonction pour nettoyer les fichiers temporaires
function Clean-TempFiles {
    param($path, $days)
    Get-ChildItem -Path $path -File -Recurse -Filter "*.tmp" | Where-Object {
        $_.LastWriteTime -lt (Get-Date).AddDays(-$days)
    } | ForEach-Object {
        $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
        Add-Content -Path $logFile -Value "$timestamp - Suppression de $($_.FullName)"
        Remove-Item -Path $_.FullName -Force
    }
}

# Fonction pour archiver les fichiers .log
function Archive-LogFiles {
    param($sourcePath, $destPath)
    Get-ChildItem -Path $sourcePath -File -Recurse -Filter "*.log" | ForEach-Object {
        $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
        $dest = Join-Path -Path $destPath -ChildPath $_.Name
        Add-Content -Path $logFile -Value "$timestamp - Archivage de $($_.FullName) vers $dest"
        Move-Item -Path $_.FullName -Destination $dest -Force -ErrorAction SilentlyContinue
    }
}

# Exécution
Add-Content -Path $logFile -Value "=== Début du script - $(Get-Date -Format 'yyyy-MM-dd HH:mm:ss') ==="
Generate-FileReport -path $rootPath
Clean-TempFiles -path $rootPath -days $daysToKeep
Archive-LogFiles -sourcePath $rootPath -destPath $archivePath
Add-Content -Path $logFile -Value "=== Fin du script - $(Get-Date -Format 'yyyy-MM-dd HH:mm:ss') ==="
Write-Host "Script terminé. Voir $logFile pour les détails."
```

---

### **3.2. Scénario : Script de sauvegarde incrémentielle**
**Objectif** :
Créer un script qui copie les fichiers modifiés dans les dernières 24 heures vers un dossier de sauvegarde.

#### **Script : Sauvegarde incrémentielle**
```powershell
$sourcePath = "C:\Temp\ProjetA"
$backupPath = "C:\Temp\Backup"
$logFile = "C:\Temp\backup_log.txt"

# Créer le dossier de sauvegarde s’il n’existe pas
if (-not (Test-Path -Path $backupPath)) {
    New-Item -Path $backupPath -ItemType Directory | Out-Null
}

# Copier les fichiers modifiés dans les dernières 24h
Get-ChildItem -Path $sourcePath -File -Recurse | Where-Object {
    $_.LastWriteTime -gt (Get-Date).AddHours(-24)
} | ForEach-Object {
    $dest = Join-Path -Path $backupPath -ChildPath $_.FullName.Substring($sourcePath.Length)
    $destDir = Split-Path -Path $dest -Parent
    if (-not (Test-Path -Path $destDir)) {
        New-Item -Path $destDir -ItemType Directory -Force | Out-Null
    }
    Copy-Item -Path $_.FullName -Destination $dest -Force
    Add-Content -Path $logFile -Value "$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss') - Copie de $($_.FullName) vers $dest"
}

Write-Host "Sauvegarde incrémentielle terminée. Voir $logFile pour les détails."
```

---
## **4. Synthèse et bonnes pratiques**

### **4.1. Résumé des notions clés**
- **Fichiers et dossiers** : Utiliser `Get-ChildItem`, `New-Item`, `Remove-Item`, `Copy-Item`, et `Move-Item` pour manipuler les fichiers.
- **Processus et services** : Utiliser `Get-Process`, `Stop-Process`, `Get-Service`, et `Restart-Service` pour gérer les ressources système.
- **Scripts** : Automatiser les tâches répétitives (nettoyage, archivage, sauvegarde).

### **4.2. Bonnes pratiques**
- **Tester les scripts** avec `-WhatIf` avant de les exécuter.
- **Journaliser les actions** pour faciliter le débogage.
- **Gérer les erreurs** avec `-ErrorAction` et `Try/Catch`.
- **Documenter les scripts** avec des commentaires et des blocs d’aide.

### **4.3. Exercices d’approfondissement**
1. **Script de surveillance des disques** :
   Écrire un script qui alerte si l’espace disque libre est inférieur à 10%.

2. **Script de gestion des utilisateurs** :
   Lister les utilisateurs locaux et exporter la liste dans un CSV.

3. **Script de déploiement** :
   Copier un ensemble de fichiers vers plusieurs serveurs distants (utiliser `Invoke-Command` pour la gestion à distance).

# **IV - Gestion des utilisateurs locaux**
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
## **2. Création, suppression et modification d’utilisateurs locaux**

### **2.1. Lister les utilisateurs locaux**
Pour afficher la liste des utilisateurs locaux :
```powershell
Get-LocalUser
```
**Filtrer par nom** :
```powershell
Get-LocalUser -Name "Utilisateur*"
```

---

### **2.2. Créer un utilisateur local**
**Syntaxe** :
```powershell
New-LocalUser -Name "NomUtilisateur" -Description "Description" -NoPassword
```
**Exemple** :
```powershell
New-LocalUser -Name "BenoitM" -Description "Compte technique pour Benoit Monteil" -NoPassword
```
**Créer un utilisateur avec mot de passe** :
```powershell
$password = ConvertTo-SecureString "MotDePasse123!" -AsPlainText -Force
New-LocalUser -Name "BenoitM" -Password $password -FullName "Benoit Monteil" -Description "Compte technique"
```

---

### **2.3. Modifier un utilisateur local**
**Changer le nom complet ou la description** :
```powershell
Set-LocalUser -Name "BenoitM" -FullName "Benoit Monteil (Admin)" -Description "Compte administrateur technique"
```
**Changer le mot de passe** :
```powershell
$newPassword = ConvertTo-SecureString "NouveauMotDePasse123!" -AsPlainText -Force
Set-LocalUser -Name "BenoitM" -Password $newPassword
```

---

### **2.4. Supprimer un utilisateur local**
```powershell
Remove-LocalUser -Name "BenoitM"
```
**Suppression avec confirmation** :
```powershell
Remove-LocalUser -Name "BenoitM" -Confirm
```

---

### **2.5. Désactiver ou activer un utilisateur**
```powershell
# Désactiver un utilisateur
Disable-LocalUser -Name "BenoitM"

# Activer un utilisateur
Enable-LocalUser -Name "BenoitM"
```

---

### **2.6. Travaux pratiques : Script de création d’utilisateurs en masse**
**Objectif** :
Créer un script pour ajouter plusieurs utilisateurs à partir d’un fichier CSV.

#### **Fichier CSV (`utilisateurs.csv`)** :
```csv
NomUtilisateur,NomComplet,Description,MotDePasse
User1,Utilisateur Un,Compte technique,Pass123!
User2,Utilisateur Deux,Compte standard,Pass123!
```

#### **Script : Création d’utilisateurs depuis un CSV**
```powershell
$users = Import-Csv -Path "C:\Temp\utilisateurs.csv"
foreach ($user in $users) {
    $password = ConvertTo-SecureString $user.MotDePasse -AsPlainText -Force
    New-LocalUser -Name $user.NomUtilisateur -FullName $user.NomComplet -Description $user.Description -Password $password
    Write-Host "Utilisateur $($user.NomUtilisateur) créé."
}
```

---

## **3. Gestion des groupes locaux**

### **3.1. Lister les groupes locaux**
```powershell
Get-LocalGroup
```

---

### **3.2. Créer un groupe local**
```powershell
New-LocalGroup -Name "Techniciens" -Description "Groupe pour les techniciens IT"
```

---

### **3.3. Ajouter un utilisateur à un groupe**
```powershell
Add-LocalGroupMember -Group "Techniciens" -Member "BenoitM"
```

---

### **3.4. Retirer un utilisateur d’un groupe**
```powershell
Remove-LocalGroupMember -Group "Techniciens" -Member "BenoitM"
```

---

### **3.5. Lister les membres d’un groupe**
```powershell
Get-LocalGroupMember -Group "Techniciens"
```

---

### **3.6. Supprimer un groupe local**
```powershell
Remove-LocalGroup -Name "Techniciens"
```

---

### **3.7. Travaux pratiques : Script de gestion des groupes**
**Objectif** :
Créer un script pour ajouter des utilisateurs à des groupes en fonction de leur rôle.

#### **Script : Ajouter des utilisateurs à des groupes**
```powershell
# Définir les utilisateurs et leurs groupes
$usersGroups = @{
    "BenoitM" = "Administrateurs", "Techniciens"
    "User1"   = "Utilisateurs", "Techniciens"
}

foreach ($user in $usersGroups.Keys) {
    foreach ($group in $usersGroups[$user]) {
        Add-LocalGroupMember -Group $group -Member $user
        Write-Host "Utilisateur $user ajouté au groupe $group."
    }
}
```

---
## **4. Gestion des permissions**

### **4.1. Introduction aux ACL (Access Control Lists)**
Les **ACL** définissent les permissions sur les fichiers, dossiers et clés de registre.
PowerShell utilise les cmdlets `Get-Acl` et `Set-Acl` pour gérer les permissions.

---

### **4.2. Afficher les permissions d’un fichier ou dossier**
```powershell
$acl = Get-Acl -Path "C:\Temp\DossierSensible"
$acl.Access | Format-Table -AutoSize
```

---

### **4.3. Ajouter une permission pour un utilisateur**
```powershell
$acl = Get-Acl -Path "C:\Temp\DossierSensible"
$rule = New-Object System.Security.AccessControl.FileSystemAccessRule("BenoitM", "FullControl", "Allow")
$acl.AddAccessRule($rule)
Set-Acl -Path "C:\Temp\DossierSensible" -AclObject $acl
```

---

### **4.4. Supprimer une permission**
```powershell
$acl = Get-Acl -Path "C:\Temp\DossierSensible"
$acl.Access | Where-Object { $_.IdentityReference -eq "DOM\BenoitM" } | ForEach-Object { $acl.RemoveAccessRule($_) }
Set-Acl -Path "C:\Temp\DossierSensible" -AclObject $acl
```

---

### **4.5. Travaux pratiques : Script de gestion des permissions**
**Objectif** :
Automatiser l’attribution de permissions sur un dossier partagé.

#### **Script : Appliquer des permissions à un dossier**
```powershell
$folderPath = "C:\Temp\PartageTechnique"
$usersPermissions = @{
    "BenoitM" = "FullControl"
    "User1"   = "ReadAndExecute"
}

foreach ($user in $usersPermissions.Keys) {
    $acl = Get-Acl -Path $folderPath
    $rule = New-Object System.Security.AccessControl.FileSystemAccessRule($user, $usersPermissions[$user], "Allow")
    $acl.AddAccessRule($rule)
    Set-Acl -Path $folderPath -AclObject $acl
    Write-Host "Permission $($usersPermissions[$user]) attribuée à $user sur $folderPath."
}
```

---
## **5. Synthèse et bonnes pratiques**

### **5.1. Résumé des notions clés**
- **Utilisateurs locaux** : Utiliser `New-LocalUser`, `Set-LocalUser`, et `Remove-LocalUser`.
- **Groupes locaux** : Utiliser `New-LocalGroup`, `Add-LocalGroupMember`, et `Remove-LocalGroupMember`.
- **Permissions** : Utiliser `Get-Acl` et `Set-Acl` pour gérer les accès.

### **5.2. Bonnes pratiques**
- **Sécuriser les mots de passe** : Utiliser `ConvertTo-SecureString` et éviter de stocker les mots de passe en clair.
- **Journaliser les actions** : Enregistrer les modifications apportées aux utilisateurs et groupes.
- **Tester les scripts** : Utiliser `-WhatIf` pour valider les actions avant de les exécuter.

### **5.3. Exercices d’approfondissement**
1. **Script d’audit des utilisateurs locaux** :
   Exporter la liste des utilisateurs locaux et leurs groupes dans un fichier CSV.

2. **Script de réinitialisation des mots de passe** :
   Réinitialiser les mots de passe des utilisateurs inactifs depuis plus de 30 jours.

3. **Script de création de groupes standardisés** :
   Créer des groupes locaux (ex: "Dev", "Ops", "ReadOnly") et y ajouter des utilisateurs en fonction de leur rôle.

---
# **V - Les bases du scripting**
## **1. Introduction aux variables et types de données**

### **1.1. Déclarer et utiliser des variables**
En PowerShell, une **variable** est un conteneur qui stocke une valeur. Les variables commencent par le symbole `$`.

**Syntaxe** :
```powershell
$nomVariable = valeur
```

**Exemples** :
```powershell
# Variable de type chaîne de caractères
$nom = "Benoit Monteil"

# Variable de type entier
$age = 30

# Variable de type tableau
$langages = @("PowerShell", "Python", "Bash")

# Variable de type booléen
$estAdmin = $true
```

**Afficher une variable** :
```powershell
Write-Host "Nom : $nom, Âge : $age"
```

---

### **1.2. Types de données courants**
| Type               | Exemple de déclaration                     | Description                                  |
|--------------------|--------------------------------------------|----------------------------------------------|
| **String**         | `$nom = "Benoit"`                          | Chaîne de caractères.                        |
| **Int**            | `$age = 30`                                | Nombre entier.                               |
| **Array**          | `$langages = @("PS", "Python")`            | Tableau (liste de valeurs).                  |
| **Bool**           | `$estAdmin = $true`                        | Booléen (`$true` ou `$false`).                |
| **Hashtable**      | `$utilisateur = @{Nom="Benoit"; Age=30}`   | Dictionnaire (clé/valeur).                   |

**Exemple avec une hashtable** :
```powershell
$utilisateur = @{
    Nom = "Benoit"
    Age = 30
    Rôle = "Administrateur"
}
Write-Host "Nom : $($utilisateur.Nom), Rôle : $($utilisateur.Rôle)"
```

---

### **1.3. Portée des variables**
- **Locale** : Accessible uniquement dans le script ou la fonction courante.
- **Globale** : Accessible partout dans la session PowerShell.
- **Script** : Accessible dans tout le script.

**Exemple** :
```powershell
# Variable globale
$global:chemin = "C:\Temp"

# Variable locale (par défaut)
$script:compte = 0
```

---

### **1.4. Travaux pratiques : Utilisation des variables**
**Exercice** :
Créer une variable pour stocker le chemin d’un dossier et lister ses fichiers.
```powershell
$dossier = "C:\Temp"
Get-ChildItem -Path $dossier
```

---

## **2. Le pipeline et la manipulation d’objets**

### **2.1. Principe du pipeline**
Le **pipeline** (`|`) permet de **chaîner des commandes** en transmettant les résultats d’une cmdlet à une autre.
PowerShell transmet des **objets** (et non du texte), ce qui permet un traitement avancé.

**Exemple** :
```powershell
# Lister les processus et filtrer ceux qui utilisent plus de 100 Mo de mémoire
Get-Process | Where-Object { $_.WorkingSet -gt 100MB } | Select-Object Name, Id, WorkingSet
```

- **`Get-Process`** : Récupère les objets `Process`.
- **`Where-Object`** : Filtre les objets selon une condition.
- **`Select-Object`** : Sélectionne des propriétés spécifiques.

---

### **2.2. Utilisation de `$_` dans le pipeline**
`$_` représente **l’objet courant** dans le pipeline.

**Exemple** :
```powershell
# Afficher le nom et la taille des fichiers dans C:\Temp
Get-ChildItem -Path "C:\Temp" | ForEach-Object {
    Write-Host "Fichier : $($_.Name) - Taille : $($_.Length) octets"
}
```

---

### **2.3. Redirection et export**
| Cmdlet          | Description                                      | Exemple                                      |
|-----------------|--------------------------------------------------|----------------------------------------------|
| `Out-File`      | Redirige la sortie vers un fichier.              | `Get-Process | Out-File -FilePath "C:\Temp\processus.txt"` |
| `Export-Csv`    | Exporte les objets vers un fichier CSV.          | `Get-Service | Export-Csv -Path "C:\Temp\services.csv"`    |

**Exercice** :
Exporter la liste des services en cours d’exécution dans un CSV.
```powershell
Get-Service | Where-Object { $_.Status -eq "Running" } | Export-Csv -Path "C:\Temp\services_running.csv" -NoTypeInformation
```

---

elseif ($age -ge 18 -and $age -lt 65) {

## **4. Fonctions et scripts réutilisables**

### **4.1. Créer une fonction**
Une **fonction** est un bloc de code réutilisable.

**Syntaxe** :
```powershell
function NomFonction {
    param (
        [type]$param1,
        [type]$param2
    )
    # Code de la fonction
}
```

**Exemple** :
```powershell
function Get-FichiersVolumineux {
    param (
        [string]$dossier,
        [int]$seuilMo = 1
    )
    Get-ChildItem -Path $dossier -File | Where-Object { $_.Length -gt ($seuilMo * 1MB) } | Select-Object Name, Length
}

# Appel de la fonction
Get-FichiersVolumineux -dossier "C:\Temp" -seuilMo 2
```

---

### **4.2. Paramètres et valeurs par défaut**
**Exemple avec paramètres** :
```powershell
function New-Utilisateur {
    param (
        [string]$nom,
        [string]$role = "Utilisateur"
    )
    Write-Host "Création de l'utilisateur $nom avec le rôle $role"
}

# Appel de la fonction
New-Utilisateur -nom "Benoit"
New-Utilisateur -nom "Alice" -role "Administrateur"
```

---

### **4.3. Retourner une valeur**
Utilisez `return` pour retourner une valeur.

**Exemple** :
```powershell
function Calculer-TailleTotale {
    param ([string]$dossier)
    $taille = (Get-ChildItem -Path $dossier -File | Measure-Object -Property Length -Sum).Sum
    return $taille
}

$tailleTotale = Calculer-TailleTotale -dossier "C:\Temp"
Write-Host "Taille totale : $tailleTotale octets"
```

---

### **4.4. Travaux pratiques : Script modulaire**
**Exercice** :
Créer une fonction pour sauvegarder les fichiers modifiés dans les dernières 24 heures.
```powershell
function Backup-FichiersRecents {
    param (
        [string]$source,
        [string]$destination
    )
    $fichiers = Get-ChildItem -Path $source -File | Where-Object { $_.LastWriteTime -gt (Get-Date).AddHours(-24) }
    foreach ($fichier in $fichiers) {
        $dest = Join-Path -Path $destination -ChildPath $fichier.Name
        Copy-Item -Path $fichier.FullName -Destination $dest -Force
        Write-Host "Copie de $($fichier.Name) vers $dest"
    }
}

# Appel de la fonction
Backup-FichiersRecents -source "C:\Temp\Projet" -destination "C:\Temp\Backup"
```

---


## **5. Synthèse et bonnes pratiques**

### **5.1. Résumé des notions clés**
- **Variables** : Utilisez `$` pour déclarer des variables et choisissez des noms explicites.
- **Pipeline** : Chaînez les cmdlets avec `|` pour traiter des objets.
- **Boucles** : Utilisez `foreach` ou `ForEach-Object` pour itérer sur des collections.
- **Conditions** : Utilisez `if`, `elseif`, et `else` pour contrôler le flux du script.
- **Fonctions** : Créez des fonctions pour rendre votre code réutilisable et modulaire.

### **5.2. Bonnes pratiques**
- **Commentaires** : Documentez votre code avec `#` ou `<# ... #>`.
- **Indentation** : Structurez votre code pour une meilleure lisibilité.
- **Gestion des erreurs** : Utilisez `Try/Catch` pour gérer les exceptions.
- **Tests** : Validez vos scripts avec `-WhatIf` avant de les exécuter.

**Exemple de gestion d’erreur** :
```powershell
try {
    Get-ChildItem -Path "C:\Inexistant" -ErrorAction Stop
}
catch {
    Write-Host "Erreur : $_" -ForegroundColor Red
}
```

---
# **VI - Gestion des utilisateurs AD**
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
## **2. Cmdlets de base pour la gestion des utilisateurs AD**

### **2.1. Lister les utilisateurs AD**
| Cmdlet               | Description                                      |
|----------------------|--------------------------------------------------|
| `Get-ADUser`         | Récupère un ou plusieurs utilisateurs AD.         |
| `Search-ADAccount`   | Recherche des comptes AD (utilisateurs, ordinateurs). |

**Exemples** :
```powershell
# Lister tous les utilisateurs AD
Get-ADUser -Filter *

# Lister les utilisateurs avec un nom spécifique
Get-ADUser -Filter "Name -like '*Monteil*'"

# Rechercher un utilisateur par son identifiant (sAMAccountName)
Get-ADUser -Identity "bmonteil" -Properties *
```

**Afficher des propriétés spécifiques** :
```powershell
Get-ADUser -Identity "bmonteil" -Properties Name, EmailAddress, Enabled | Select-Object Name, EmailAddress, Enabled
```

---

### **2.2. Créer un utilisateur AD**
**Syntaxe** :
```powershell
New-ADUser -Name "Nom Prénom" -SamAccountName "identifiant" -GivenName "Prénom" -Surname "Nom" -Enabled $true -AccountPassword (ConvertTo-SecureString "MotDePasse123!" -AsPlainText -Force) -ChangePasswordAtLogon $true
```

**Exemple complet** :
```powershell
$password = ConvertTo-SecureString "MotDePasse123!" -AsPlainText -Force
New-ADUser -Name "Benoit Monteil" -SamAccountName "bmonteil" -GivenName "Benoit" -Surname "Monteil" -Enabled $true -AccountPassword $password -ChangePasswordAtLogon $true -Path "OU=Utilisateurs,DC=domaine,DC=local"
```

**Explication des paramètres** :
- **`-Name`** : Nom complet de l’utilisateur.
- **`-SamAccountName`** : Identifiant de connexion (ex: `bmonteil`).
- **`-Path`** : Unité d’organisation (OU) où créer l’utilisateur.
- **`-AccountPassword`** : Mot de passe initial (doit être sécurisé avec `ConvertTo-SecureString`).
- **`-ChangePasswordAtLogon`** : Force l’utilisateur à changer son mot de passe à la première connexion.

---

### **2.3. Modifier un utilisateur AD**
**Cmdlet** : `Set-ADUser`

**Exemples** :
```powershell
# Changer le nom ou l’email
Set-ADUser -Identity "bmonteil" -GivenName "Benoit" -EmailAddress "b.monteil@domaine.local"

# Activer/désactiver un compte
Set-ADUser -Identity "bmonteil" -Enabled $false
Set-ADUser -Identity "bmonteil" -Enabled $true

# Déplacer un utilisateur vers une autre OU
Move-ADObject -Identity (Get-ADUser -Identity "bmonteil") -TargetPath "OU=NouvelleOU,DC=domaine,DC=local"
```

---

### **2.4. Supprimer un utilisateur AD**
**Cmdlet** : `Remove-ADUser`

**Exemple** :
```powershell
# Supprimer un utilisateur (avec confirmation)
Remove-ADUser -Identity "bmonteil" -Confirm

# Supprimer sans confirmation
Remove-ADUser -Identity "bmonteil" -Confirm:$false
```

---

### **2.5. Réinitialiser un mot de passe**
```powershell
$newPassword = ConvertTo-SecureString "NouveauMotDePasse123!" -AsPlainText -Force
Set-ADAccountPassword -Identity "bmonteil" -NewPassword $newPassword -Reset
```

---

### **2.6. Travaux pratiques : Script de création d’utilisateurs en masse**
**Objectif** :
Créer plusieurs utilisateurs à partir d’un fichier CSV.

#### **Fichier CSV (`utilisateurs_ad.csv`)** :
```csv
Prenom,Nom,Identifiant,OU,Email
Benoit,Monteil,bmonteil,OU=Utilisateurs,benoit.monteil@domaine.local
Alice,Dupont,adupont,OU=Utilisateurs,alice.dupont@domaine.local
```

#### **Script : Création d’utilisateurs depuis un CSV**
```powershell
$users = Import-Csv -Path "C:\Temp\utilisateurs_ad.csv"
foreach ($user in $users) {
    $password = ConvertTo-SecureString "MotDePasse123!" -AsPlainText -Force
    New-ADUser -Name "$($user.Prenom) $($user.Nom)" -SamAccountName $user.Identifiant -GivenName $user.Prenom -Surname $user.Nom -Enabled $true -AccountPassword $password -ChangePasswordAtLogon $true -Path $user.OU -EmailAddress $user.Email
    Write-Host "Utilisateur $($user.Identifiant) créé."
}
```

---
## **3. Cmdlets pour la gestion des groupes AD**

### **3.1. Lister les groupes AD**
```powershell
# Lister tous les groupes
Get-ADGroup -Filter *

# Lister les groupes avec un nom spécifique
Get-ADGroup -Filter "Name -like '*Admin*'"
```

---

### **3.2. Créer un groupe AD**
**Cmdlet** : `New-ADGroup`

**Exemple** :
```powershell
New-ADGroup -Name "Techniciens_IT" -GroupScope Global -GroupCategory Security -Path "OU=Groupes,DC=domaine,DC=local"
```

**Explication des paramètres** :
- **`-GroupScope`** : Portée du groupe (`DomainLocal`, `Global`, `Universal`).
- **`-GroupCategory`** : Type de groupe (`Security` ou `Distribution`).

---

### **3.3. Ajouter un utilisateur à un groupe**
**Cmdlet** : `Add-ADGroupMember`

**Exemple** :
```powershell
Add-ADGroupMember -Identity "Techniciens_IT" -Members "bmonteil"
```

---

### **3.4. Retirer un utilisateur d’un groupe**
**Cmdlet** : `Remove-ADGroupMember`

**Exemple** :
```powershell
Remove-ADGroupMember -Identity "Techniciens_IT" -Members "bmonteil" -Confirm:$false
```

---

### **3.5. Lister les membres d’un groupe**
```powershell
Get-ADGroupMember -Identity "Techniciens_IT" | Select-Object Name, SamAccountName
```

---

### **3.6. Travaux pratiques : Script de gestion des groupes**
**Objectif** :
Ajouter des utilisateurs à des groupes en fonction de leur rôle.

#### **Script : Ajouter des utilisateurs à des groupes**
```powershell
$usersGroups = @{
    "bmonteil" = "Administrateurs", "Techniciens_IT"
    "adupont"  = "Utilisateurs", "Stagiaires"
}

foreach ($user in $usersGroups.Keys) {
    foreach ($group in $usersGroups[$user]) {
        Add-ADGroupMember -Identity $group -Members $user
        Write-Host "Utilisateur $user ajouté au groupe $group."
    }
}
```

---
## **4. Recherches avancées et filtres**

### **4.1. Utiliser des filtres LDAP**
**Exemple** :
```powershell
# Trouver les utilisateurs dont le prénom commence par "A"
Get-ADUser -LDAPFilter "(givenName=A*)" -Properties Name, EmailAddress

# Trouver les comptes désactivés
Get-ADUser -Filter {Enabled -eq $false} -Properties Name
```

---

### **4.2. Exporter les utilisateurs AD vers un CSV**
```powershell
Get-ADUser -Filter * -Properties Name, EmailAddress, Enabled | Select-Object Name, EmailAddress, Enabled | Export-Csv -Path "C:\Temp\utilisateurs_ad.csv" -NoTypeInformation
```

---

### **4.3. Travaux pratiques : Audit des comptes inactifs**
**Objectif** :
Lister les utilisateurs qui ne se sont pas connectés depuis 90 jours.

#### **Script : Audit des comptes inactifs**
```powershell
$daysInactive = 90
$inactiveDate = (Get-Date).AddDays(-$daysInactive)
Get-ADUser -Filter {LastLogonDate -lt $inactiveDate -and Enabled -eq $true} -Properties Name, LastLogonDate | Select-Object Name, @{Name="LastLogonDate"; Expression={$_.LastLogonDate}} | Export-Csv -Path "C:\Temp\comptes_inactifs.csv" -NoTypeInformation
Write-Host "Audit terminé. Résultats dans C:\Temp\comptes_inactifs.csv"
```

---


## **5. Synthèse et bonnes pratiques**

### **5.1. Résumé des cmdlets clés**
| Action                     | Cmdlet                          |
|----------------------------|---------------------------------|
| Lister les utilisateurs    | `Get-ADUser`                    |
| Créer un utilisateur       | `New-ADUser`                    |
| Modifier un utilisateur    | `Set-ADUser`                    |
| Supprimer un utilisateur   | `Remove-ADUser`                 |
| Lister les groupes         | `Get-ADGroup`                   |
| Créer un groupe            | `New-ADGroup`                   |
| Ajouter à un groupe        | `Add-ADGroupMember`             |
| Retirer d’un groupe        | `Remove-ADGroupMember`          |

### **5.2. Bonnes pratiques**
- **Testez toujours vos scripts** avec `-WhatIf` si possible.
- **Journalisez les actions** pour auditer les modifications.
- **Utilisez des OU dédiées** pour organiser les utilisateurs et groupes.
- **Sécurisez les mots de passe** : Ne stockez jamais les mots de passe en clair dans les scripts.

**Exemple de journalisation** :
```powershell
$logFile = "C:\Temp\ad_operations.log"
Add-Content -Path $logFile -Value "$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss') - Création de l'utilisateur $identifiant"
```

---

### **5.3. Exercices d’approfondissement**
1. **Script de réinitialisation des mots de passe** :
   Réinitialiser les mots de passe des utilisateurs inactifs depuis 30 jours.

2. **Script de création d’OU** :
   Créer une structure d’OU pour un nouveau département et y ajouter des utilisateurs.

3. **Script de synchronisation** :
   Synchroniser les groupes AD avec une liste externe (ex: fichier CSV).

---

# **VII - Automatisation des taches de gestions utilisateurs**
## **1. Automatisation de la création et de la suppression d’utilisateurs**

### **1.1. Scripts pour la création d’utilisateurs en masse**
#### **1.1.1. Utilisateurs locaux**
**Objectif** :
Créer un script qui lit un fichier CSV et crée des utilisateurs locaux avec des attributs personnalisés.

**Fichier CSV (`utilisateurs_locaux.csv`)** :
```csv
NomUtilisateur,NomComplet,Description,MotDePasse,GroupePrincipal
user1,Utilisateur Un,Compte technique,Pass123!,Utilisateurs
user2,Utilisateur Deux,Compte standard,Pass123!,Utilisateurs
admin1,Admin Un,Compte administrateur,Admin123!,Administrateurs
```

**Script : Création d’utilisateurs locaux depuis un CSV**
```powershell
<#
.SYNOPSIS
    Crée des utilisateurs locaux à partir d'un fichier CSV.

.DESCRIPTION
    Ce script lit un fichier CSV et crée des utilisateurs locaux avec les attributs spécifiés.
    Il ajoute également les utilisateurs aux groupes indiqués.

.PARAMETER CsvPath
    Chemin vers le fichier CSV contenant les informations des utilisateurs.

.EXAMPLE
    .\New-LocalUsersFromCsv.ps1 -CsvPath "C:\Temp\utilisateurs_locaux.csv"
#>
param (
    [Parameter(Mandatory=$true)]
    [string]$CsvPath
)

# Importer les utilisateurs depuis le CSV
$users = Import-Csv -Path $CsvPath

foreach ($user in $users) {
    # Créer le mot de passe sécurisé
    $securePassword = ConvertTo-SecureString $user.MotDePasse -AsPlainText -Force

    # Créer l'utilisateur
    New-LocalUser -Name $user.NomUtilisateur -FullName $user.NomComplet -Description $user.Description -Password $securePassword -UserMayNotChangePassword

    # Ajouter l'utilisateur au groupe principal
    Add-LocalGroupMember -Group $user.GroupePrincipal -Member $user.NomUtilisateur

    # Journaliser l'action
    Write-Host "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] Utilisateur $($user.NomUtilisateur) créé et ajouté au groupe $($user.GroupePrincipal)." -ForegroundColor Green
}
```

**Exécution** :
```powershell
.\New-LocalUsersFromCsv.ps1 -CsvPath "C:\Temp\utilisateurs_locaux.csv"
```

---

#### **1.1.2. Utilisateurs Active Directory**
**Objectif** :
Créer un script pour importer des utilisateurs AD depuis un CSV, avec gestion des OU et des groupes.

**Fichier CSV (`utilisateurs_ad.csv`)** :
```csv
Prenom,Nom,Identifiant,OU,Email,GroupePrincipal,GroupeSecondaire
Benoit,Monteil,bmonteil,OU=Utilisateurs,benoit.monteil@domaine.local,Techniciens_IT,
Alice,Dupont,adupont,OU=Utilisateurs,alice.dupont@domaine.local,Utilisateurs,Stagiaires
```

**Script : Création d’utilisateurs AD depuis un CSV**
```powershell
<#
.SYNOPSIS
    Crée des utilisateurs Active Directory à partir d'un fichier CSV.

.DESCRIPTION
    Ce script lit un fichier CSV et crée des utilisateurs AD avec les attributs spécifiés.
    Il ajoute les utilisateurs aux groupes principaux et secondaires.

.PARAMETER CsvPath
    Chemin vers le fichier CSV contenant les informations des utilisateurs.

.EXAMPLE
    .\New-ADUsersFromCsv.ps1 -CsvPath "C:\Temp\utilisateurs_ad.csv"
#>
param (
    [Parameter(Mandatory=$true)]
    [string]$CsvPath
)

# Importer le module ActiveDirectory
Import-Module ActiveDirectory

# Importer les utilisateurs depuis le CSV
$users = Import-Csv -Path $CsvPath

foreach ($user in $users) {
    # Créer le mot de passe sécurisé
    $securePassword = ConvertTo-SecureString "MotDePasse123!" -AsPlainText -Force

    # Créer l'utilisateur AD
    New-ADUser -Name "$($user.Prenom) $($user.Nom)" -SamAccountName $user.Identifiant -GivenName $user.Prenom -Surname $user.Nom -Enabled $true -AccountPassword $securePassword -ChangePasswordAtLogon $true -Path $user.OU -EmailAddress $user.Email

    # Ajouter l'utilisateur au groupe principal
    Add-ADGroupMember -Identity $user.GroupePrincipal -Members $user.Identifiant

    # Ajouter l'utilisateur aux groupes secondaires (si spécifiés)
    if ($user.GroupeSecondaire) {
        $secondaryGroups = $user.GroupeSecondaire -split ','
        foreach ($group in $secondaryGroups) {
            Add-ADGroupMember -Identity $group -Members $user.Identifiant
        }
    }

    # Journaliser l'action
    Write-Host "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] Utilisateur AD $($user.Identifiant) créé et ajouté aux groupes $($user.GroupePrincipal), $($user.GroupeSecondaire)." -ForegroundColor Green
}
```

**Exécution** :
```powershell
.\New-ADUsersFromCsv.ps1 -CsvPath "C:\Temp\utilisateurs_ad.csv"
```

---

### **1.2. Scripts pour la suppression d’utilisateurs**
#### **1.2.1. Suppression d’utilisateurs locaux inactifs**
**Objectif** :
Supprimer les utilisateurs locaux qui ne se sont pas connectés depuis 30 jours.

**Script : Suppression des utilisateurs locaux inactifs**
```powershell
<#
.SYNOPSIS
    Supprime les utilisateurs locaux inactifs depuis 30 jours.

.DESCRIPTION
    Ce script identifie les utilisateurs locaux dont le dernier login remonte à plus de 30 jours et les supprime.

.PARAMETER DaysInactive
    Nombre de jours d'inactivité pour considérer un utilisateur comme inactif.

.EXAMPLE
    .\Remove-InactiveLocalUsers.ps1 -DaysInactive 30
#>
param (
    [int]$DaysInactive = 30
)

# Calculer la date seuil
$inactiveDate = (Get-Date).AddDays(-$DaysInactive)

# Récupérer les utilisateurs locaux
$users = Get-LocalUser | Where-Object { $_.LastLogon -lt $inactiveDate -and $_.Enabled -eq $true }

foreach ($user in $users) {
    # Supprimer l'utilisateur
    Remove-LocalUser -Name $user.Name -ErrorAction SilentlyContinue

    # Journaliser l'action
    Write-Host "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] Utilisateur $($user.Name) supprimé (inactif depuis plus de $DaysInactive jours)." -ForegroundColor Red
}
```

---

#### **1.2.2. Suppression d’utilisateurs AD désactivés**
**Objectif** :
Supprimer les utilisateurs AD désactivés depuis plus de 90 jours.

**Script : Suppression des utilisateurs AD désactivés**
```powershell
<#
.SYNOPSIS
    Supprime les utilisateurs AD désactivés depuis plus de 90 jours.

.DESCRIPTION
    Ce script identifie les utilisateurs AD désactivés depuis plus de 90 jours et les supprime.

.PARAMETER DaysDisabled
    Nombre de jours depuis la désactivation pour supprimer l'utilisateur.

.EXAMPLE
    .\Remove-DisabledADUsers.ps1 -DaysDisabled 90
#>
param (
    [int]$DaysDisabled = 90
)

# Importer le module ActiveDirectory
Import-Module ActiveDirectory

# Calculer la date seuil
$disabledDate = (Get-Date).AddDays(-$DaysDisabled)

# Récupérer les utilisateurs AD désactivés
$users = Get-ADUser -Filter {Enabled -eq $false -and whenChanged -lt $disabledDate} -Properties whenChanged

foreach ($user in $users) {
    # Supprimer l'utilisateur
    Remove-ADUser -Identity $user.SamAccountName -Confirm:$false

    # Journaliser l'action
    Write-Host "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] Utilisateur AD $($user.SamAccountName) supprimé (désactivé depuis plus de $DaysDisabled jours)." -ForegroundColor Red
}
```

---
## **2. Gestion des mots de passe et des politiques**

### **2.1. Réinitialisation des mots de passe**
#### **2.1.1. Réinitialiser les mots de passe des utilisateurs locaux**
**Script : Réinitialisation des mots de passe locaux**
```powershell
<#
.SYNOPSIS
    Réinitialise les mots de passe des utilisateurs locaux.

.DESCRIPTION
    Ce script réinitialise les mots de passe des utilisateurs locaux à une valeur par défaut.

.PARAMETER DefaultPassword
    Mot de passe par défaut à appliquer.

.EXAMPLE
    .\Reset-LocalUserPasswords.ps1 -DefaultPassword "NouveauMotDePasse123!"
#>
param (
    [Parameter(Mandatory=$true)]
    [string]$DefaultPassword
)

# Convertir le mot de passe en SecureString
$securePassword = ConvertTo-SecureString $DefaultPassword -AsPlainText -Force

# Récupérer les utilisateurs locaux
$users = Get-LocalUser | Where-Object { $_.Enabled -eq $true }

foreach ($user in $users) {
    # Réinitialiser le mot de passe
    Set-LocalUser -Name $user.Name -Password $securePassword

    # Journaliser l'action
    Write-Host "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] Mot de passe réinitialisé pour l'utilisateur $($user.Name)." -ForegroundColor Yellow
}
```

---

#### **2.1.2. Réinitialiser les mots de passe des utilisateurs AD**
**Script : Réinitialisation des mots de passe AD**
```powershell
<#
.SYNOPSIS
    Réinitialise les mots de passe des utilisateurs AD.

.DESCRIPTION
    Ce script réinitialise les mots de passe des utilisateurs AD à une valeur par défaut et force le changement au prochain login.

.PARAMETER DefaultPassword
    Mot de passe par défaut à appliquer.

.PARAMETER OuPath
    Chemin de l'OU contenant les utilisateurs à traiter.

.EXAMPLE
    .\Reset-ADUserPasswords.ps1 -DefaultPassword "NouveauMotDePasse123!" -OuPath "OU=Utilisateurs,DC=domaine,DC=local"
#>
param (
    [Parameter(Mandatory=$true)]
    [string]$DefaultPassword,

    [string]$OuPath = "OU=Utilisateurs,DC=domaine,DC=local"
)

# Importer le module ActiveDirectory
Import-Module ActiveDirectory

# Convertir le mot de passe en SecureString
$securePassword = ConvertTo-SecureString $DefaultPassword -AsPlainText -Force

# Récupérer les utilisateurs AD dans l'OU spécifiée
$users = Get-ADUser -Filter * -SearchBase $OuPath -Properties SamAccountName

foreach ($user in $users) {
    # Réinitialiser le mot de passe
    Set-ADAccountPassword -Identity $user.SamAccountName -NewPassword $securePassword -Reset
    Set-ADUser -Identity $user.SamAccountName -ChangePasswordAtLogon $true

    # Journaliser l'action
    Write-Host "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] Mot de passe réinitialisé pour l'utilisateur AD $($user.SamAccountName)." -ForegroundColor Yellow
}
```

---

### **2.2. Application des politiques de mot de passe**
#### **2.2.1. Vérifier la conformité des mots de passe**
**Script : Vérification de la complexité des mots de passe**
```powershell
<#
.SYNOPSIS
    Vérifie que les mots de passe des utilisateurs AD respectent les politiques de complexité.

.DESCRIPTION
    Ce script vérifie si les mots de passe des utilisateurs AD respectent les politiques de complexité définies.

.EXAMPLE
    .\Check-ADPasswordPolicy.ps1
#>
Import-Module ActiveDirectory

# Récupérer la politique de mot de passe du domaine
$passwordPolicy = (Get-ADDefaultDomainPasswordPolicy)

# Afficher les paramètres de la politique
Write-Host "Politique de mot de passe du domaine :"
Write-Host "Complexité requise : $($passwordPolicy.ComplexityEnabled)"
Write-Host "Longueur minimale : $($passwordPolicy.MinPasswordLength)"
Write-Host "Durée minimale du mot de passe : $($passwordPolicy.MinPasswordAge.Days) jours"
Write-Host "Durée maximale du mot de passe : $($passwordPolicy.MaxPasswordAge.Days) jours"
Write-Host "Historique des mots de passe : $($passwordPolicy.PasswordHistoryCount) mots de passe mémorisés"
```

---

#### **2.2.2. Forcer l’expiration des mots de passe**
**Script : Forcer l’expiration des mots de passe AD**
```powershell
<#
.SYNOPSIS
    Force l'expiration des mots de passe pour les utilisateurs AD.

.DESCRIPTION
    Ce script force l'expiration des mots de passe pour les utilisateurs AD spécifiés.

.PARAMETER OuPath
    Chemin de l'OU contenant les utilisateurs à traiter.

.EXAMPLE
    .\Expire-ADUserPasswords.ps1 -OuPath "OU=Utilisateurs,DC=domaine,DC=local"
#>
param (
    [string]$OuPath = "OU=Utilisateurs,DC=domaine,DC=local"
)

# Importer le module ActiveDirectory
Import-Module ActiveDirectory

# Récupérer les utilisateurs AD dans l'OU spécifiée
$users = Get-ADUser -Filter * -SearchBase $OuPath -Properties SamAccountName

foreach ($user in $users) {
    # Forcer l'expiration du mot de passe
    Set-ADUser -Identity $user.SamAccountName -ChangePasswordAtLogon $true

    # Journaliser l'action
    Write-Host "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] Expiration du mot de passe forcée pour l'utilisateur AD $($user.SamAccountName)." -ForegroundColor Yellow
}
```

---
## **3. Travaux pratiques : Automatiser la gestion des utilisateurs avec un script modulaire**

### **3.1. Scénario : Script complet de gestion des utilisateurs AD**
**Objectif** :
Créer un script modulaire qui permet de :
1. Créer des utilisateurs AD depuis un CSV.
2. Ajouter des utilisateurs à des groupes.
3. Réinitialiser les mots de passe.
4. Supprimer les utilisateurs inactifs.

**Structure du script** :
```plaintext
UserManagement/
├── New-ADUsersFromCsv.ps1       # Création d'utilisateurs
├── Add-ADUsersToGroups.ps1      # Ajout aux groupes
├── Reset-ADUserPasswords.ps1    # Réinitialisation des mots de passe
├── Remove-InactiveADUsers.ps1   # Suppression des utilisateurs inactifs
└── UserManagement.ps1          # Script principal
```

---

#### **3.1.1. Script principal (`UserManagement.ps1`)**
```powershell
<#
.SYNOPSIS
    Script principal pour la gestion automatisée des utilisateurs AD.

.DESCRIPTION
    Ce script appelle les modules pour créer, modifier et supprimer des utilisateurs AD.

.PARAMETER Action
    Action à effectuer : Create, AddToGroups, ResetPasswords, RemoveInactive.

.PARAMETER CsvPath
    Chemin vers le fichier CSV (pour la création d'utilisateurs).

.PARAMETER OuPath
    Chemin de l'OU à traiter (pour les autres actions).

.PARAMETER DaysInactive
    Nombre de jours d'inactivité pour la suppression (optionnel).

.EXAMPLE
    .\UserManagement.ps1 -Action Create -CsvPath "C:\Temp\utilisateurs_ad.csv"
    .\UserManagement.ps1 -Action AddToGroups -OuPath "OU=Utilisateurs,DC=domaine,DC=local"
    .\UserManagement.ps1 -Action ResetPasswords -OuPath "OU=Utilisateurs,DC=domaine,DC=local"
    .\UserManagement.ps1 -Action RemoveInactive -OuPath "OU=Utilisateurs,DC=domaine,DC=local" -DaysInactive 90
#>
param (
    [Parameter(Mandatory=$true)]
    [ValidateSet("Create", "AddToGroups", "ResetPasswords", "RemoveInactive")]
    [string]$Action,

    [string]$CsvPath,
    [string]$OuPath,
    [int]$DaysInactive
)

# Importer le module ActiveDirectory
Import-Module ActiveDirectory

# Définir le chemin du log
$logFile = "C:\Temp\UserManagement_$(Get-Date -Format 'yyyyMMdd').log"

# Fonction pour journaliser les actions
function Write-Log {
    param (
        [string]$Message
    )
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $logMessage = "[$timestamp] $Message"
    Write-Host $logMessage
    Add-Content -Path $logFile -Value $logMessage
}

# Exécuter l'action demandée
switch ($Action) {
    "Create" {
        Write-Log "Début de la création des utilisateurs AD depuis $CsvPath"
        .\New-ADUsersFromCsv.ps1 -CsvPath $CsvPath
        Write-Log "Fin de la création des utilisateurs AD"
    }

    "AddToGroups" {
        Write-Log "Début de l'ajout des utilisateurs aux groupes dans $OuPath"
        .\Add-ADUsersToGroups.ps1 -OuPath $OuPath
        Write-Log "Fin de l'ajout des utilisateurs aux groupes"
    }

    "ResetPasswords" {
        $defaultPassword = Read-Host "Entrez le mot de passe par défaut" -AsSecureString | ConvertFrom-SecureString
        $defaultPasswordPlain = ConvertTo-SecureString $defaultPassword -AsPlainText -Force | ConvertFrom-SecureString
        Write-Log "Début de la réinitialisation des mots de passe dans $OuPath"
        .\Reset-ADUserPasswords.ps1 -DefaultPassword $defaultPasswordPlain -OuPath $OuPath
        Write-Log "Fin de la réinitialisation des mots de passe"
    }

    "RemoveInactive" {
        Write-Log "Début de la suppression des utilisateurs inactifs dans $OuPath (inactifs depuis $DaysInactive jours)"
        .\Remove-InactiveADUsers.ps1 -OuPath $OuPath -DaysInactive $DaysInactive
        Write-Log "Fin de la suppression des utilisateurs inactifs"
    }
}
```

---

#### **3.1.2. Script `Add-ADUsersToGroups.ps1`**
```powershell
<#
.SYNOPSIS
    Ajoute les utilisateurs AD à des groupes en fonction d'un mapping.

.DESCRIPTION
    Ce script ajoute les utilisateurs AD à des groupes prédéfinis.

.PARAMETER OuPath
    Chemin de l'OU contenant les utilisateurs à traiter.

.EXAMPLE
    .\Add-ADUsersToGroups.ps1 -OuPath "OU=Utilisateurs,DC=domaine,DC=local"
#>
param (
    [Parameter(Mandatory=$true)]
    [string]$OuPath
)

# Définir le mapping utilisateurs/groupes
$usersGroups = @{
    "bmonteil" = @("Administrateurs", "Techniciens_IT")
    "adupont"  = @("Utilisateurs", "Stagiaires")
}

# Importer le module ActiveDirectory
Import-Module ActiveDirectory

# Récupérer les utilisateurs AD dans l'OU spécifiée
$users = Get-ADUser -Filter * -SearchBase $OuPath -Properties SamAccountName

foreach ($user in $users) {
    if ($usersGroups.ContainsKey($user.SamAccountName)) {
        foreach ($group in $usersGroups[$user.SamAccountName]) {
            Add-ADGroupMember -Identity $group -Members $user.SamAccountName -ErrorAction SilentlyContinue
            Write-Log "Utilisateur $($user.SamAccountName) ajouté au groupe $group"
        }
    }
}
```

---

#### **3.1.3. Script `Remove-InactiveADUsers.ps1`**
```powershell
<#
.SYNOPSIS
    Supprime les utilisateurs AD inactifs depuis un nombre de jours spécifié.

.DESCRIPTION
    Ce script identifie et supprime les utilisateurs AD inactifs depuis plus de X jours.

.PARAMETER OuPath
    Chemin de l'OU contenant les utilisateurs à traiter.

.PARAMETER DaysInactive
    Nombre de jours d'inactivité pour considérer un utilisateur comme inactif.

.EXAMPLE
    .\Remove-InactiveADUsers.ps1 -OuPath "OU=Utilisateurs,DC=domaine,DC=local" -DaysInactive 90
#>
param (
    [Parameter(Mandatory=$true)]
    [string]$OuPath,

    [Parameter(Mandatory=$true)]
    [int]$DaysInactive
)

# Importer le module ActiveDirectory
Import-Module ActiveDirectory

# Calculer la date seuil
$inactiveDate = (Get-Date).AddDays(-$DaysInactive)

# Récupérer les utilisateurs AD inactifs
$users = Get-ADUser -Filter {LastLogonDate -lt $inactiveDate -and Enabled -eq $true} -SearchBase $OuPath -Properties LastLogonDate, SamAccountName

foreach ($user in $users) {
    # Supprimer l'utilisateur
    Remove-ADUser -Identity $user.SamAccountName -Confirm:$false
    Write-Log "Utilisateur AD $($user.SamAccountName) supprimé (inactif depuis plus de $DaysInactive jours)"
}
```

---

### **3.2. Exécution du script principal**
**Exemples d’utilisation** :
```powershell
# Créer des utilisateurs AD
.\UserManagement.ps1 -Action Create -CsvPath "C:\Temp\utilisateurs_ad.csv"

# Ajouter des utilisateurs aux groupes
.\UserManagement.ps1 -Action AddToGroups -OuPath "OU=Utilisateurs,DC=domaine,DC=local"

# Réinitialiser les mots de passe
.\UserManagement.ps1 -Action ResetPasswords -OuPath "OU=Utilisateurs,DC=domaine,DC=local"

# Supprimer les utilisateurs inactifs
.\UserManagement.ps1 -Action RemoveInactive -OuPath "OU=Utilisateurs,DC=domaine,DC=local" -DaysInactive 90
```

---

## **4. Synthèse et bonnes pratiques**

### **4.1. Résumé des notions clés**
- **Automatisation** : Utilisez des scripts pour créer, modifier et supprimer des utilisateurs en masse.
- **Gestion des mots de passe** : Réinitialisez et forcez l’expiration des mots de passe selon les politiques.
- **Modularité** : Structurez vos scripts en modules pour une maintenance plus facile.
- **Journalisation** : Enregistrez toutes les actions pour un audit ultérieur.

### **4.2. Bonnes pratiques**
- **Testez toujours vos scripts** dans un environnement de test avant de les exécuter en production.
- **Utilisez des paramètres** pour rendre vos scripts flexibles et réutilisables.
- **Sécurisez les mots de passe** : Ne stockez jamais les mots de passe en clair dans les scripts.
- **Documentation** : Commentez vos scripts et utilisez des blocs d’aide (`<# ... #>`).
- **Gestion des erreurs** : Utilisez `Try/Catch` et `-ErrorAction` pour gérer les exceptions.

**Exemple de gestion d’erreur** :
```powershell
try {
    New-ADUser -Name "Test User" -SamAccountName "testuser" -ErrorAction Stop
}
catch {
    Write-Log "Erreur lors de la création de l'utilisateur : $_"
}
```

---

### **4.3. Exercices d’approfondissement**
1. **Script de synchronisation avec un annuaire externe** :
   Écrire un script qui synchronise les utilisateurs AD avec une liste externe (ex: fichier CSV, base de données).

2. **Script de création d’OU et de groupes** :
   Automatiser la création d’une structure d’OU et de groupes pour un nouveau département.

3. **Script de rapport d’audit** :
   Générer un rapport des utilisateurs AD avec leurs groupes, dates de dernier login, et statut.

---
# **VIII - Automatisation des processus système**
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
## **3. Synthèse et bonnes pratiques**
- **Planifiez les tâches** pendant les heures creuses pour éviter les perturbations.
- **Journalisez les actions** pour un suivi et un débogage faciles.
- **Testez les scripts** avant de les planifier en production.

---
# **IX - Scripts de maintenance serveur**
## **1. Automatisation des sauvegardes**
### **1.1. Sauvegarde de fichiers avec PowerShell**
**Exemple : Sauvegarder un dossier vers un partage réseau**
```powershell
<#
.SYNOPSIS
    Sauvegarde un dossier local vers un partage réseau.
#>
$source = "C:\Data"
$destination = "\\Server\Backup\Data_$(Get-Date -Format 'yyyyMMdd')"
$logFile = "C:\Temp\BackupLog.txt"

# Créer le dossier de destination
New-Item -Path $destination -ItemType Directory -Force | Out-Null

# Copier les fichiers
Copy-Item -Path "$source\*" -Destination $destination -Recurse -Force

# Journaliser l’action
Add-Content -Path $logFile -Value "$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss') - Sauvegarde de $source vers $destination terminée."
```

---

### **1.2. Sauvegarde avec `robocopy`**
```powershell
$source = "C:\Data"
$destination = "\\Server\Backup\Data_$(Get-Date -Format 'yyyyMMdd')"
$logFile = "C:\Temp\RobocopyLog.txt"

# Utiliser robocopy pour une copie robuste
& robocopy $source $destination /E /Z /R:3 /W:5 /LOG:$logFile /TEE
```

---

### **1.3. Travaux pratiques : Script de sauvegarde incrémentielle**
**Script : `BackupIncremental.ps1`**
```powershell
<#
.SYNOPSIS
    Effectue une sauvegarde incrémentielle des fichiers modifiés dans les dernières 24 heures.
#>
$source = "C:\Data"
$destination = "\\Server\Backup\Incremental_$(Get-Date -Format 'yyyyMMdd')"
$logFile = "C:\Temp\IncrementalBackupLog.txt"

# Créer le dossier de destination
New-Item -Path $destination -ItemType Directory -Force | Out-Null

# Copier les fichiers modifiés dans les dernières 24 heures
Get-ChildItem -Path $source -File -Recurse | Where-Object {
    $_.LastWriteTime -gt (Get-Date).AddHours(-24)
} | ForEach-Object {
    $destPath = Join-Path -Path $destination -ChildPath $_.FullName.Substring($source.Length)
    New-Item -Path (Split-Path -Path $destPath -Parent) -ItemType Directory -Force | Out-Null
    Copy-Item -Path $_.FullName -Destination $destPath -Force
    Add-Content -Path $logFile -Value "$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss') - Copie de $($_.FullName) vers $destPath"
}
```

---

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

## **3. Travaux pratiques : Script de maintenance basique pour un serveur**
### **3.1. Script complet : `ServerMaintenance.ps1`**
```powershell
<#
.SYNOPSIS
    Effectue une maintenance basique du serveur : nettoyage, sauvegarde et mises à jour.
#>
$logFile = "C:\Temp\ServerMaintenance_$(Get-Date -Format 'yyyyMMdd').log"

function Write-Log {
    param ([string]$message)
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $logMessage = "[$timestamp] $message"
    Write-Output $logMessage
    Add-Content -Path $logFile -Value $logMessage
}

# 1. Nettoyage des logs
Write-Log "Début du nettoyage des logs..."
$logPath = "C:\Logs"
$daysToKeep = 30
Get-ChildItem -Path $logPath -File -Recurse | Where-Object {
    $_.LastWriteTime -lt (Get-Date).AddDays(-$daysToKeep)
} | ForEach-Object {
    Remove-Item -Path $_.FullName -Force
    Write-Log "Suppression de $($_.FullName)"
}

# 2. Sauvegarde des données
Write-Log "Début de la sauvegarde des données..."
$source = "C:\Data"
$destination = "\\Server\Backup\Data_$(Get-Date -Format 'yyyyMMdd')"
New-Item -Path $destination -ItemType Directory -Force | Out-Null
Copy-Item -Path "$source\*" -Destination $destination -Recurse -Force
Write-Log "Sauvegarde de $source vers $destination terminée."

# 3. Vérification des services critiques
Write-Log "Vérification des services critiques..."
$criticalServices = @("Spooler", "DHCP", "DNS")
foreach ($service in $criticalServices) {
    $status = (Get-Service -Name $service).Status
    if ($status -ne "Running") {
        Write-Log "Service $service est $status - Redémarrage..."
        Start-Service -Name $service
    }
}

# 4. Installation des mises à jour (optionnel)
Write-Log "Vérification des mises à jour..."
if (Get-Module -ListAvailable -Name PSWindowsUpdate) {
    Import-Module PSWindowsUpdate
    $updates = Get-WindowsUpdate
    if ($updates) {
        Write-Log "Mises à jour disponibles - Installation..."
        Install-WindowsUpdate -AcceptAll -AutoReboot
    }
} else {
    Write-Log "Module PSWindowsUpdate non installé - Mises à jour ignorées."
}

Write-Log "Maintenance terminée."
```

---

### **3.2. Planification du script de maintenance**
```powershell
$action = New-ScheduledJobAction -Execute "PowerShell.exe" -Argument "-NoProfile -File `"C:\Scripts\ServerMaintenance.ps1`""
$trigger = New-JobTrigger -Weekly -DaysOfWeek Sunday -At 1am
Register-ScheduledJob -Name "ServerMaintenance" -Action $action -Trigger $trigger
```

---


## **4. Synthèse et bonnes pratiques**
- **Sauvegardez régulièrement** les données critiques.
- **Testez les scripts de maintenance** dans un environnement non productif.
- **Journalisez toutes les actions** pour un suivi et un audit.
- **Planifiez les tâches** pendant les fenêtres de maintenance.

---
# **X - Gestion avancée en Powershell**
## **1. Gestion des journaux d’événements**

### **1.1. Introduction aux journaux d’événements**
Les **journaux d’événements** (Event Logs) sont une source essentielle d’informations pour le diagnostic et la surveillance des systèmes Windows.
PowerShell permet d’interagir avec ces journaux via les cmdlets du module **Microsoft.PowerShell.Diagnostics**.

**Cmdlets clés** :
| Cmdlet               | Description                                      |
|----------------------|--------------------------------------------------|
| `Get-EventLog`       | Liste les événements d’un journal spécifique.    |
| `Get-WinEvent`       | Récupère les événements (plus flexible et moderne). |
| `Filter-Xml`         | Filtre les événements avec des requêtes XML.     |
| `Export-Csv`         | Exporte les événements vers un fichier CSV.      |

---

### **1.2. Lister les journaux disponibles**
```powershell
# Lister tous les journaux d'événements disponibles
Get-EventLog -List

# Lister les journaux avec Get-WinEvent (plus complet)
Get-WinEvent -ListLog *
```

**Explication** :
- `Get-EventLog -List` : Affiche les journaux classiques (Application, Système, Sécurité).
- `Get-WinEvent -ListLog *` : Affiche **tous** les journaux, y compris ceux spécifiques aux applications et services.

---

### **1.3. Lire les événements d’un journal**
#### **1.3.1. Utilisation de `Get-EventLog`**
```powershell
# Lister les 10 derniers événements du journal "System"
Get-EventLog -LogName System -Newest 10

# Filtrer les événements d’erreur
Get-EventLog -LogName System -EntryType Error -Newest 5
```

**Explication** :
- `-LogName` : Spécifie le journal (ex: `System`, `Application`, `Security`).
- `-EntryType` : Filtre par type (`Error`, `Warning`, `Information`).
- `-Newest` : Limite le nombre d’entrées retournées.

---

#### **1.3.2. Utilisation de `Get-WinEvent` (recommandé)**
```powershell
# Lister les 20 derniers événements du journal "Application"
Get-WinEvent -LogName Application -MaxEvents 20

# Filtrer les événements d’erreur avec un ID spécifique
Get-WinEvent -LogName System -FilterXPath "*[System[Level=2]]" | Where-Object { $_.Id -eq 6005 }
```

**Explication** :
- `Level=2` correspond aux **erreurs** (1=Information, 2=Error, 3=Warning).
- `-FilterXPath` permet des requêtes avancées en XML.

---

### **1.4. Filtrer les événements par date**
```powershell
# Récupérer les événements des dernières 24 heures
$startTime = (Get-Date).AddHours(-24)
Get-WinEvent -LogName System -FilterHashtable @{ StartTime = $startTime } | Where-Object { $_.LevelDisplayName -eq "Error" }
```

**Explication** :
- `@{ StartTime = $startTime }` : Filtre les événements postérieurs à `$startTime`.
- `$_.LevelDisplayName -eq "Error"` : Filtre les erreurs.

---

### **1.5. Exporter les événements vers un CSV**
```powershell
# Exporter les erreurs du journal "System" vers un CSV
Get-WinEvent -LogName System -FilterXPath "*[System[Level=2]]" |
    Select-Object TimeCreated, Id, ProviderName, Message |
    Export-Csv -Path "C:\Temp\SystemErrors.csv" -NoTypeInformation -Encoding UTF8
```

**Explication** :
- `Select-Object` : Sélectionne les propriétés à exporter.
- `-NoTypeInformation` : Évite d’inclure des métadonnées inutiles dans le CSV.

---

### **1.6. Travaux pratiques : Analyser les erreurs récurrentes**
---
#### **Énoncé de l’exercice** :
**Objectif** :
Écrire un script qui :
1. Récupère les **100 derniers événements d’erreur** du journal **System**.
2. Compte le nombre d’occurrences de chaque **ID d’événement**.
3. Exporte les résultats dans un fichier CSV (`C:\Temp\ErrorStats.csv`).
4. Affiche un résumé à l’écran.

**Contraintes** :
- Utiliser `Get-WinEvent`.
- Inclure dans le CSV : `ID`, `Nombre d’occurrences`, `Message` (premier événement de chaque ID).

---
#### **Corrigé** :
```powershell
<#
.SYNOPSIS
    Analyse les erreurs récurrentes dans le journal System.

.DESCRIPTION
    Ce script récupère les 100 derniers événements d'erreur du journal System,
    compte les occurrences par ID, et exporte les résultats dans un CSV.
#>

# Récupérer les 100 derniers événements d'erreur
$errors = Get-WinEvent -LogName System -FilterXPath "*[System[Level=2]]" -MaxEvents 100

# Compter les occurrences par ID
$errorStats = $errors | Group-Object -Property Id | Select-Object Name, Count, @{
    Name = "Message"
    Expression = { ($_.Group | Select-Object -First 1).Message }
}

# Exporter vers un CSV
$errorStats | Export-Csv -Path "C:\Temp\ErrorStats.csv" -NoTypeInformation -Encoding UTF8

# Afficher un résumé
Write-Host "`n--- Résumé des erreurs récurrentes ---"
$errorStats | Sort-Object -Property Count -Descending | Format-Table -AutoSize

Write-Host "`nLes résultats ont été exportés vers C:\Temp\ErrorStats.csv"
```

**Explications** :
- `Group-Object -Property Id` : Regroupe les événements par ID.
- `Select-Object` : Sélectionne le nombre d’occurrences et le message du premier événement de chaque groupe.
- `Sort-Object -Descending` : Trie par nombre d’occurrences (du plus fréquent au moins fréquent).

---

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

## **4. Synthèse et bonnes pratiques**

### **4.1. Résumé des notions clés**
- **Journaux d’événements** : Utilisez `Get-WinEvent` pour une analyse avancée.
- **Surveillance des ressources** : `Get-Counter` et `Get-Volume` sont essentiels.
- **Alertes** : Journalisez et notifiez les problèmes critiques (email, logs).
- **Automatisation** : Planifiez les scripts pour une surveillance continue.

### **4.2. Bonnes pratiques**
- **Testez les scripts** dans un environnement non productif avant déploiement.
- **Limitez les alertes** pour éviter la surcharge (ex: 1 email/h par type d’alerte).
- **Centralisez les logs** : Utilisez un serveur dédié pour stocker les logs de tous les serveurs.
- **Sécurisez les emails** : Utilisez des comptes dédiés et des canaux chiffrés (TLS) pour l’envoi d’alertes.

### **4.3. Exercices d’approfondissement**
1. **Script de surveillance multi-serveurs** :
   Utiliser `Invoke-Command` pour surveiller plusieurs serveurs à distance.
   ```powershell
   $servers = @("Server1", "Server2", "Server3")
   Invoke-Command -ComputerName $servers -ScriptBlock { Get-Counter '\Processor(_Total)\% Processor Time' }
   ```

2. **Intégration avec un SIEM** :
   Exporter les événements critiques vers un système de gestion des informations et événements de sécurité (SIEM).

3. **Tableau de bord PowerShell** :
   Créer un script qui affiche un **tableau de bord** en temps réel avec les métriques système (utiliser `Out-GridView` ou une interface graphique basique).

---
# **XI - Formater les sorties des scripts**
## **1. Utilisation des formats de sortie**

### **1.1. Formats de base : Tableau, Liste, Large**
PowerShell propose plusieurs formats natifs pour afficher les données :
- **Tableau** (`Format-Table`) : Idéal pour comparer des objets avec des propriétés communes.
- **Liste** (`Format-List`) : Affiche toutes les propriétés d’un objet sous forme de liste.
- **Large** (`Format-Wide`) : Affiche une seule propriété par ligne (compact).

---

#### **1.1.1. Format Tableau (`Format-Table`)**
**Exemple** :
```powershell
# Lister les processus avec un format tableau
Get-Process | Format-Table -Property Id, Name, CPU, WorkingSet -AutoSize

# Limiter à 10 processus et trier par CPU
Get-Process | Sort-Object -Property CPU -Descending | Select-Object -First 10 | Format-Table -AutoSize
```
**Explications** :
- `-Property` : Spécifie les propriétés à afficher.
- `-AutoSize` : Ajuste la largeur des colonnes automatiquement.

**Sortie** :
```
 Id    Name                     CPU WorkingSet
 --    ----                     --- ----------
1234  chrome                  25.5   12345678
5678  powershell               8.2    567890
...
```

---

#### **1.1.2. Format Liste (`Format-List`)**
**Exemple** :
```powershell
# Afficher les détails d'un service en liste
Get-Service -Name "Spooler" | Format-List -Property *
```
**Explications** :
- Affiche **toutes les propriétés** (`*`) ou celles spécifiées.
- Utile pour voir les **détails complets** d’un objet.

**Sortie** :
```
Name        : Spooler
DisplayName : Spouleur d'impression
Status      : Running
...
```

---

#### **1.1.3. Format Large (`Format-Wide`)**
**Exemple** :
```powershell
# Lister uniquement les noms des services (format compact)
Get-Service | Format-Wide -Property Name -Column 3
```
**Explications** :
- `-Column` : Définit le nombre de colonnes (ici, 3).

**Sortie** :
```
AeLookupSvc       AppIDSvc          Appinfo
AppMgmt           AppReadiness      AppXSvc
...
```

---

#### **1.1.4. Personnalisation avancée avec `@{}`**
**Exemple** :
```powershell
# Afficher les processus avec une colonne "Mem (Mo)" calculée
Get-Process | Format-Table -Property Name, Id, @{
    Name = "Mem (Mo)"
    Expression = { [math]::Round($_.WorkingSet / 1MB, 2) }
}, CPU
```
**Explications** :
- `@{ Name = "Mem (Mo)"; Expression = { ... } }` : Crée une colonne personnalisée.
- `$_.WorkingSet / 1MB` : Convertit la mémoire de octets en Mo.

**Sortie** :
```
Name                     Id Mem (Mo)   CPU
----                     -- --------   ---
chrome                 1234    120.5 25.5
powershell              5678     55.3  8.2
...
```

---

### **1.2. Export vers des fichiers (CSV, HTML, TXT)**
#### **1.2.1. Export CSV (`Export-Csv`)**
**Exemple** :
```powershell
# Exporter les services en cours d'exécution vers un CSV
Get-Service | Where-Object { $_.Status -eq "Running" } |
    Select-Object -Property Name, DisplayName, Status |
    Export-Csv -Path "C:\Temp\ServicesRunning.csv" -NoTypeInformation -Encoding UTF8
```
**Explications** :
- `-NoTypeInformation` : Supprime la ligne d’en-tête de type.
- `-Encoding UTF8` : Garantit la compatibilité avec Excel.

**Contenu du CSV** :
```csv
"Name","DisplayName","Status"
"AeLookupSvc","Application Experience","Running"
"Appinfo","Service d'informations d'application","Running"
...
```

---

#### **1.2.2. Export HTML (`ConvertTo-Html`)**
**Exemple** :
```powershell
# Générer un rapport HTML des disques
Get-Volume | Select-Object -Property DriveLetter, FileSystemLabel, @{
    Name = "Size (Go)"
    Expression = { [math]::Round($_.Size / 1GB, 2) }
}, @{
    Name = "Free (Go)"
    Expression = { [math]::Round($_.SizeRemaining / 1GB, 2) }
} | ConvertTo-Html -Title "Rapport Disques" | Out-File "C:\Temp\DisksReport.html"
```
**Explications** :
- `ConvertTo-Html` : Convertit les objets en tableau HTML.
- `-Title` : Ajoute un titre au rapport.

**Sortie** :
![Exemple de rapport HTML](https://i.imgur.com/XYZ1234.png)
*(Un tableau HTML avec les colonnes spécifiées.)*

---

#### **1.2.3. Export TXT (`Out-File`)**
**Exemple** :
```powershell
# Exporter la liste des utilisateurs locaux vers un fichier texte
Get-LocalUser | Format-Table -AutoSize | Out-File "C:\Temp\LocalUsers.txt"
```
**Explications** :
- `Out-File` : Redirige la sortie vers un fichier texte.

---

### **1.3. Travaux pratiques : Formater la sortie des journaux d’événements**
---
#### **Énoncé de l’exercice** :
**Objectif** :
1. Récupérer les **10 dernières erreurs** du journal **System**.
2. Formater la sortie pour afficher :
   - `TimeCreated` (date/heure),
   - `Id` (ID de l’événement),
   - `Message` (tronqué à 50 caractères),
   - `ProviderName` (source).
3. Exporter les résultats :
   - En **tableau** dans la console.
   - En **CSV** (`C:\Temp\SystemErrors.csv`).
   - En **HTML** (`C:\Temp\SystemErrors.html`) avec un titre.

---
#### **Corrigé** :
```powershell
# Récupérer les 10 dernières erreurs
$errors = Get-WinEvent -LogName System -FilterXPath "*[System[Level=2]]" -MaxEvents 10

# Afficher en tableau dans la console
$errors | Format-Table -Property TimeCreated, Id, @{
    Name = "Message"
    Expression = { $_.Message.Substring(0, [math]::Min(50, $_.Message.Length)) }
}, ProviderName -AutoSize

# Exporter en CSV
$errors | Select-Object -Property TimeCreated, Id, @{
    Name = "Message"
    Expression = { $_.Message.Substring(0, [math]::Min(50, $_.Message.Length)) }
}, ProviderName |
    Export-Csv -Path "C:\Temp\SystemErrors.csv" -NoTypeInformation -Encoding UTF8

# Exporter en HTML
$errors | Select-Object -Property TimeCreated, Id, @{
    Name = "Message"
    Expression = { $_.Message.Substring(0, [math]::Min(50, $_.Message.Length)) }
}, ProviderName |
    ConvertTo-Html -Title "Dernières erreurs système" |
    Out-File "C:\Temp\SystemErrors.html"
```

**Explications** :
- `Substring(0, [math]::Min(50, $_.Message.Length))` : Tronque le message à 50 caractères.
- `ConvertTo-Html` : Génère un rapport HTML avec un titre.

---

## **2. Personnalisation des messages**

### **2.1. Utilisation des couleurs (`Write-Host`)**
**Exemple** :
```powershell
# Afficher un message d'alerte en rouge
Write-Host "ALERT: Espace disque faible!" -ForegroundColor Red -BackgroundColor White

# Afficher un succès en vert
Write-Host "SUCCÈS: Sauvegarde terminée." -ForegroundColor Green
```
**Explications** :
- `-ForegroundColor` : Couleur du texte (`Red`, `Green`, `Yellow`, etc.).
- `-BackgroundColor` : Couleur de fond.

---

### **2.2. Messages dynamiques avec variables**
**Exemple** :
```powershell
# Afficher l'état des services critiques
$criticalServices = @("Spooler", "DHCP", "DNS")
foreach ($service in $criticalServices) {
    $status = (Get-Service -Name $service).Status
    if ($status -eq "Running") {
        Write-Host "Service $service : $status" -ForegroundColor Green
    } else {
        Write-Host "Service $service : $status" -ForegroundColor Red
    }
}
```
**Sortie** :
```
Service Spooler : Running  # En vert
Service DHCP : Stopped     # En rouge
```

---

### **2.3. Barres de progression (`Write-Progress`)**
**Exemple** :
```powershell
# Simuler une tâche longue avec une barre de progression
for ($i = 1; $i -le 100; $i++) {
    Write-Progress -Activity "Traitement en cours..." -Status "$i% terminé" -PercentComplete $i
    Start-Sleep -Milliseconds 50
}
```
**Explications** :
- `-Activity` : Description de la tâche.
- `-PercentComplete` : Pourcentage d’avancement.

---

### **2.4. Travaux pratiques : Script avec messages personnalisés**
---
#### **Énoncé de l’exercice** :
**Objectif** :
Créer un script qui :
1. Vérifie l’**espace disque** sur tous les volumes.
2. Affiche un message :
   - **Vert** si l’espace libre > 20%.
   - **Jaune** si 10% < espace libre ≤ 20%.
   - **Rouge** si espace libre ≤ 10%.
3. Affiche une **barre de progression** pendant la vérification.

---
#### **Corrigé** :
```powershell
# Récupérer tous les volumes
$volumes = Get-Volume
$i = 0

foreach ($volume in $volumes) {
    $i++
    $percentFree = ($volume.SizeRemaining / $volume.Size) * 100
    $percentComplete = ($i / $volumes.Count) * 100

    # Mettre à jour la barre de progression
    Write-Progress -Activity "Vérification des disques..." -Status "$($volume.DriveLetter):" -PercentComplete $percentComplete

    # Afficher l'état avec couleur
    if ($percentFree -gt 20) {
        Write-Host "Disque $($volume.DriveLetter) : $([math]::Round($percentFree, 1))% libre" -ForegroundColor Green
    } elseif ($percentFree -gt 10) {
        Write-Host "Disque $($volume.DriveLetter) : $([math]::Round($percentFree, 1))% libre" -ForegroundColor Yellow
    } else {
        Write-Host "Disque $($volume.DriveLetter) : $([math]::Round($percentFree, 1))% libre" -ForegroundColor Red
    }
}
```

**Explications** :
- `Write-Progress` : Affiche une barre de progression mise à jour à chaque itération.
- Les couleurs indiquent **l’urgence** de l’alerte.

---

## **3. Travaux pratiques : Créer des scripts avec des sorties formatées pour un reporting clair**

---
### **Énoncé du projet** :
**Objectif** :
Créer un **script de rapport système complet** qui :
1. **Récupère les informations** :
   - Utilisation CPU/mémoire.
   - Espace disque (tous les volumes).
   - Services critiques (`Spooler`, `DHCP`, `DNS`).
   - Dernières erreurs du journal **System** (5 dernières).
2. **Formate les sorties** :
   - **Tableau** dans la console pour un aperçu rapide.
   - **CSV** pour une analyse ultérieure (`C:\Temp\SystemReport.csv`).
   - **HTML** pour un rapport visuel (`C:\Temp\SystemReport.html`).
3. **Personnalise les messages** :
   - Couleurs pour les alertes (rouge/jaune/vert).
   - Barre de progression pendant la collecte des données.

---
### **Corrigé** :
```powershell
<#
.SYNOPSIS
    Génère un rapport système complet avec sorties formatées.

.DESCRIPTION
    Ce script collecte les métriques système (CPU, mémoire, disques, services, erreurs)
    et génère des rapports en tableau, CSV et HTML.
#>
$reportDate = Get-Date -Format "yyyy-MM-dd_HH-mm-ss"
$csvPath = "C:\Temp\SystemReport_$reportDate.csv"
$htmlPath = "C:\Temp\SystemReport_$reportDate.html"

# Fonction pour ajouter une section au rapport HTML
function Add-HtmlSection {
    param (
        [string]$title,
        [object]$data,
        [string]$html
    )

    $section = @"
<h2>$title</h2>
$(ConvertTo-Html -InputObject $data -Fragment)
"@
    return $html.Replace("</body>", "$section</body>")
}

# 1. Collecte des données avec barre de progression
$tasks = @(
    { Get-Counter '\Processor(_Total)\% Processor Time' | Select-Object -ExpandProperty CounterSamples },
    { Get-Counter '\Memory\Available MBytes' | Select-Object -ExpandProperty CounterSamples },
    { Get-Volume },
    { Get-Service -Name "Spooler", "DHCP", "DNS" },
    { Get-WinEvent -LogName System -FilterXPath "*[System[Level=2]]" -MaxEvents 5 }
)

$results = @()
$i = 0

foreach ($task in $tasks) {
    $i++
    $percentComplete = ($i / $tasks.Count) * 100
    Write-Progress -Activity "Collecte des données..." -PercentComplete $percentComplete

    $results += Invoke-Command -ScriptBlock $task
}

# 2. Préparer les données pour les rapports
# CPU
$cpu = $results[0].CookedValue
$cpuObj = [PSCustomObject]@{
    Metric = "CPU Usage"
    Value  = "$([math]::Round($cpu, 1))%"
    Status = if ($cpu -gt 90) { "High" } else { "Normal" }
}

# Mémoire
$mem = $results[1].CookedValue
$memObj = [PSCustomObject]@{
    Metric = "Available Memory (Mo)"
    Value  = [math]::Round($mem, 1)
    Status = if ($mem -lt 1024) { "Low" } else { "Normal" }
}

# Disques
$disks = $results[2] | ForEach-Object {
    [PSCustomObject]@{
        Volume      = $_.DriveLetter
        Total       = "$([math]::Round($_.Size / 1GB, 2)) Go"
        Free        = "$([math]::Round($_.SizeRemaining / 1GB, 2)) Go"
        FreePercent = "$([math]::Round(($_.SizeRemaining / $_.Size) * 100, 1))%"
        Status      = if (($_.SizeRemaining / $_.Size) * 100 -lt 20) { "Low" } else { "Normal" }
    }
}

# Services
$services = $results[3] | ForEach-Object {
    [PSCustomObject]@{
        Name   = $_.Name
        Status = $_.Status
    }
}

# Erreurs
$errors = $results[4] | ForEach-Object {
    [PSCustomObject]@{
        Time    = $_.TimeCreated
        Id      = $_.Id
        Source  = $_.ProviderName
        Message = $_.Message.Substring(0, [math]::Min(50, $_.Message.Length))
    }
}

# 3. Afficher un résumé en console (avec couleurs)
Write-Host "`n--- Rapport Système ($reportDate) ---" -ForegroundColor Cyan
Write-Host "CPU: $([math]::Round($cpu, 1))%" -ForegroundColor $(if ($cpu -gt 90) { "Red" } else { "Green" })
Write-Host "Mémoire disponible: $([math]::Round($mem, 1)) Mo" -ForegroundColor $(if ($mem -lt 1024) { "Red" } else { "Green" })

Write-Host "`nDisques:" -ForegroundColor Cyan
$disks | Format-Table -AutoSize

Write-Host "`nServices critiques:" -ForegroundColor Cyan
$services | Format-Table -AutoSize

Write-Host "`nDernières erreurs système:" -ForegroundColor Cyan
$errors | Format-Table -AutoSize

# 4. Exporter en CSV
@($cpuObj, $memObj) |
    Export-Csv -Path $csvPath -NoTypeInformation -Encoding UTF8 -Append
$disks | Export-Csv -Path $csvPath -NoTypeInformation -Encoding UTF8 -Append
$services | Export-Csv -Path $csvPath -NoTypeInformation -Encoding UTF8 -Append
$errors | Export-Csv -Path $csvPath -NoTypeInformation -Encoding UTF8 -Append

# 5. Générer le rapport HTML
$html = ConvertTo-Html -Title "Rapport Système - $($env:COMPUTERNAME) - $reportDate" -Head @"
<style>
    body { font-family: Arial; }
    h1 { color: #0066cc; }
    h2 { color: #333; }
    table { border-collapse: collapse; width: 100%; }
    th { background-color: #0066cc; color: white; }
    td { border: 1px solid #ddd; padding: 8px; }
    tr:nth-child(even) { background-color: #f2f2f2; }
</style>
"@ -Body "<h1>Rapport Système - $($env:COMPUTERNAME)</h1>"

$html = Add-HtmlSection -title "CPU et Mémoire" -data @($cpuObj, $memObj) -html $html
$html = Add-HtmlSection -title "Disques" -data $disks -html $html
$html = Add-HtmlSection -title "Services Critiques" -data $services -html $html
$html = Add-HtmlSection -title "Dernières Erreurs Système" -data $errors -html $html

$html | Out-File -FilePath $htmlPath

Write-Host "`nRapport généré :`n- CSV: $csvPath`n- HTML: $htmlPath" -ForegroundColor Green
```

---
### **Explications détaillées** :
1. **Collecte des données** :
   - Utilise `Invoke-Command` pour exécuter chaque tâche dans une boucle.
   - `Write-Progress` affiche une barre de progression pendant la collecte.

2. **Préparation des objets** :
   - **CPU/Mémoire** : Crée des objets personnalisés avec un statut (`High`/`Low`/`Normal`).
   - **Disques** : Calcule l’espace libre en Go et en %.
   - **Services/Erreurs** : Formate les données pour une sortie claire.

3. **Sortie console** :
   - Affiche un **résumé coloré** des métriques critiques.

4. **Export CSV** :
   - Combine toutes les données dans un seul fichier CSV.

5. **Rapport HTML** :
   - Utilise `ConvertTo-Html` avec un **style CSS intégré** pour un rendu professionnel.
   - `Add-HtmlSection` : Ajoute des sections titrées au rapport.

6. **Sortie finale** :
   - Affiche les chemins des fichiers générés.

---
### **Bonus : Automatisation avec une tâche planifiée**
Pour exécuter ce script **quotidiennement à 8h** :
```powershell
$action = New-ScheduledJobAction -Execute "PowerShell.exe" -Argument "-NoProfile -File `"C:\Scripts\SystemReport.ps1`""
$trigger = New-JobTrigger -Daily -At 8am
Register-ScheduledJob -Name "DailySystemReport" -Action $action -Trigger $trigger
```

---
## **4. Synthèse et bonnes pratiques**

### **4.1. Résumé des formats et outils**
| Format          | Cmdlet               | Utilisation typique                          |
|-----------------|----------------------|-----------------------------------------------|
| Tableau         | `Format-Table`       | Comparaison rapide de plusieurs objets.      |
| Liste           | `Format-List`        | Détails complets d’un objet.                  |
| CSV             | `Export-Csv`         | Analyse ultérieure (Excel, Power BI).        |
| HTML            | `ConvertTo-Html`     | Rapports visuels pour partage.               |
| Texte           | `Out-File`           | Logs ou sorties brutes.                       |
| Couleurs        | `Write-Host`         | Mise en évidence des alertes.                 |
| Barre de progression | `Write-Progress` | Feedback visuel pour les tâches longues. |

### **4.2. Bonnes pratiques**
- **Choisissez le bon format** :
  - **Tableau** pour les comparaisons.
  - **Liste** pour les détails.
  - **CSV/HTML** pour les rapports.
- **Personnalisez les colonnes** :
  - Utilisez `@{ Name = "..."; Expression = { ... } }` pour calculer des valeurs.
- **Ajoutez des couleurs** :
  - Rouge pour les erreurs, vert pour les succès.
- **Documentation** :
  - Ajoutez des en-têtes et des titres clairs.
- **Automatisez** :
  - Planifiez la génération de rapports (ex: quotidiennement).

### **4.3. Exercices d’approfondissement**
1. **Rapport des utilisateurs AD** :
   - Exporter la liste des utilisateurs AD avec leurs groupes et dernière date de connexion.
   - Utiliser `Get-ADUser` et `Get-ADGroupMember`.

2. **Suivi des performances** :
   - Créer un script qui enregistre l’utilisation CPU/mémoire toutes les heures dans un CSV.
   - Générer un graphique avec Excel ou Power BI.

3. **Notification par email** :
   - Étendre le script pour envoyer le rapport HTML par email (`Send-MailMessage`).

---
# **XII - Gestion des fichiers de log**
## **1. Création et gestion de fichiers de log**

### **1.1. Principes de base des fichiers de log**
Un **fichier de log** est un enregistrement chronologique des événements générés par un script ou un système. Il permet de :
- **Suivre l’exécution** des scripts.
- **Diagnostiquer les erreurs**.
- **Auditer les actions** (ex: modifications système).

**Bonnes pratiques** :
- Utiliser des **timestamps** pour chaque entrée.
- Définir des **niveaux de gravité** (INFO, WARNING, ERROR).
- **Séparer les logs** par jour ou par taille (rotation).

---

### **1.2. Écrire dans un fichier de log**
#### **1.2.1. Méthode basique avec `Add-Content`**
```powershell
# Chemin du fichier de log
$logFile = "C:\Logs\ScriptLog_$(Get-Date -Format 'yyyyMMdd').log"

# Écrire une entrée dans le log
$message = "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] [INFO] Début du script."
Add-Content -Path $logFile -Value $message
```
**Explications** :
- `Get-Date -Format 'yyyy-MM-dd HH:mm:ss'` : Génère un timestamp.
- `Add-Content` : Ajoute une ligne au fichier (crée le fichier s’il n’existe pas).

---

#### **1.2.2. Fonction réutilisable pour les logs**
```powershell
function Write-Log {
    param (
        [string]$message,
        [string]$level = "INFO",
        [string]$logFile = "C:\Logs\ScriptLog_$(Get-Date -Format 'yyyyMMdd').log"
    )

    # Créer le dossier si nécessaire
    if (-not (Test-Path -Path (Split-Path -Path $logFile -Parent))) {
        New-Item -ItemType Directory -Path (Split-Path -Path $logFile -Parent) -Force | Out-Null
    }

    # Formater le message avec timestamp et niveau
    $logEntry = "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] [$level] $message"

    # Écrire dans le fichier
    Add-Content -Path $logFile -Value $logEntry

    # Afficher dans la console (optionnel)
    switch ($level) {
        "ERROR" { Write-Host $logEntry -ForegroundColor Red }
        "WARNING" { Write-Host $logEntry -ForegroundColor Yellow }
        default { Write-Host $logEntry }
    }
}

# Exemple d'utilisation
Write-Log -message "Début du script de sauvegarde." -level "INFO"
Write-Log -message "Espace disque insuffisant sur C:!" -level "WARNING"
Write-Log -message "Échec de la connexion à la base de données." -level "ERROR"
```
**Explications** :
- **`$level`** : Définit le niveau de gravité (`INFO`, `WARNING`, `ERROR`).
- **Couleurs** : Affiche les messages dans la console avec des couleurs selon le niveau.

**Sortie dans le fichier** :
```
[2025-11-10 14:30:45] [INFO] Début du script de sauvegarde.
[2025-11-10 14:30:46] [WARNING] Espace disque insuffisant sur C:!
[2025-11-10 14:30:47] [ERROR] Échec de la connexion à la base de données.
```

---

#### **1.2.3. Utilisation avancée avec des objets**
```powershell
# Créer un objet pour structurer le log
$logEntry = [PSCustomObject]@{
    Timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    Level     = "INFO"
    Message   = "Début du script."
    Script    = $MyInvocation.MyCommand.Name
    User      = $env:USERNAME
}

# Convertir en JSON et écrire dans le log
$logEntry | ConvertTo-Json | Add-Content -Path $logFile
```
**Explications** :
- **`[PSCustomObject]`** : Crée un objet structuré avec des propriétés.
- **`ConvertTo-Json`** : Convertit l’objet en format JSON pour une analyse facile.

**Sortie dans le fichier** :
```json
{
    "Timestamp": "2025-11-10 14:30:45",
    "Level": "INFO",
    "Message": "Début du script.",
    "Script": "Backup.ps1",
    "User": "Admin"
}
```

---

### **1.3. Rotation des logs**
#### **1.3.1. Rotation par date**
```powershell
# Chemin du log avec la date du jour
$logFile = "C:\Logs\ScriptLog_$(Get-Date -Format 'yyyyMMdd').log"

# Supprimer les logs plus anciens que 30 jours
Get-ChildItem -Path "C:\Logs" -Filter "ScriptLog_*.log" | Where-Object {
    $_.LastWriteTime -lt (Get-Date).AddDays(-30)
} | Remove-Item -Force
```
**Explications** :
- **`ScriptLog_*.log`** : Filtre les fichiers de log.
- **`AddDays(-30)`** : Supprime les logs de plus de 30 jours.

---

#### **1.3.2. Rotation par taille**
```powershell
$logFile = "C:\Logs\ScriptLog.log"
$maxSize = 10MB  # Taille maximale du fichier

if (Test-Path -Path $logFile) {
    $fileSize = (Get-Item -Path $logFile).Length
    if ($fileSize -gt $maxSize) {
        $archiveName = "C:\Logs\ScriptLog_$(Get-Date -Format 'yyyyMMdd_HHmmss').archive.log"
        Rename-Item -Path $logFile -NewName $archiveName -Force
    }
}
```
**Explications** :
- Vérifie si le fichier dépasse **10 Mo**.
- **Archive** le fichier avec un timestamp avant de créer un nouveau log.

---

### **1.4. Travaux pratiques : Créer un système de log complet**
---
#### **Énoncé de l’exercice** :
**Objectif** :
1. Créer une **fonction `Write-Log`** qui :
   - Accepte un message, un niveau de gravité (`INFO`, `WARNING`, `ERROR`), et un chemin de log.
   - Écrit le message dans le fichier avec un **timestamp** et le **nom du script**.
   - Affiche le message dans la console avec une **couleur adaptée**.
2. **Archiver les logs** plus anciens que 7 jours.
3. **Tester la fonction** avec différents niveaux de gravité.

---
#### **Corrigé** :
```powershell
function Write-Log {
    param (
        [Parameter(Mandatory=$true)]
        [string]$message,

        [ValidateSet("INFO", "WARNING", "ERROR")]
        [string]$level = "INFO",

        [string]$logDir = "C:\Logs"
    )

    # Créer le dossier de logs s'il n'existe pas
    if (-not (Test-Path -Path $logDir)) {
        New-Item -ItemType Directory -Path $logDir -Force | Out-Null
    }

    # Chemin du log avec la date du jour
    $logFile = Join-Path -Path $logDir -ChildPath "ScriptLog_$(Get-Date -Format 'yyyyMMdd').log"

    # Formater le message
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $scriptName = $MyInvocation.MyCommand.Name
    $logEntry = "[$timestamp] [$level] [$scriptName] $message"

    # Écrire dans le fichier
    Add-Content -Path $logFile -Value $logEntry

    # Afficher dans la console avec couleur
    switch ($level) {
        "ERROR" { Write-Host $logEntry -ForegroundColor Red }
        "WARNING" { Write-Host $logEntry -ForegroundColor Yellow }
        default { Write-Host $logEntry }
    }

    # Archiver les logs plus anciens que 7 jours
    Get-ChildItem -Path $logDir -Filter "ScriptLog_*.log" | Where-Object {
        $_.LastWriteTime -lt (Get-Date).AddDays(-7)
    } | ForEach-Object {
        $archiveName = $_.FullName.Replace(".log", "_$(Get-Date -Format 'yyyyMMdd').archive.log")
        Rename-Item -Path $_.FullName -NewName $archiveName -Force
    }
}

# Exemples d'utilisation
Write-Log -message "Test du système de log (INFO)."
Write-Log -message "Attention, espace disque faible!" -level "WARNING"
Write-Log -message "Erreur critique: impossible de se connecter." -level "ERROR"
```

**Explications** :
- **`Join-Path`** : Construit le chemin du fichier de log.
- **`ValidateSet`** : Restreint les valeurs de `$level` à `INFO`, `WARNING`, ou `ERROR`.
- **Archivage automatique** : Les logs de plus de 7 jours sont renommés avec un suffixe `.archive.log`.

---

## **2. Exportation des données pour le reporting**

### **2.1. Analyser les logs avec PowerShell**
#### **2.1.1. Compter les erreurs dans un log**
```powershell
# Compter le nombre d'erreurs dans un fichier de log
$logFile = "C:\Logs\ScriptLog_20251110.log"
$errorCount = (Get-Content -Path $logFile | Where-Object { $_ -match '\[ERROR\]' }).Count
Write-Host "Nombre d'erreurs : $errorCount"
```
**Explications** :
- **`-match '\[ERROR\]'`** : Filtre les lignes contenant `[ERROR]`.

---

#### **2.1.2. Extraire les logs d’un niveau spécifique**
```powershell
# Extraire tous les messages de niveau WARNING
Get-Content -Path $logFile | Where-Object { $_ -match '\[WARNING\]' } | ForEach-Object {
    Write-Host $_ -ForegroundColor Yellow
}
```

---

#### **2.1.3. Générer un rapport à partir des logs**
```powershell
# Compter les occurrences de chaque niveau
$logStats = Get-Content -Path $logFile | ForEach-Object {
    if ($_ -match '\[(INFO|WARNING|ERROR)\]') {
        $matches[1]
    }
} | Group-Object | Select-Object Name, Count

# Afficher les statistiques
$logStats | Format-Table -AutoSize

# Exporter en CSV
$logStats | Export-Csv -Path "C:\Logs\LogStats_$(Get-Date -Format 'yyyyMMdd').csv" -NoTypeInformation
```
**Sortie** :
```
Name    Count
----    -----
INFO       42
WARNING     5
ERROR       2
```

---

### **2.2. Exporter les logs vers Excel**
#### **2.2.1. Utiliser `ImportExcel` (module externe)**
```powershell
# Installer le module (si nécessaire)
Install-Module -Name ImportExcel -Force -Scope CurrentUser

# Importer les logs et exporter vers Excel
$logs = Get-Content -Path $logFile | ForEach-Object {
    $parts = $_ -split '\] '
    $timestamp = $parts[0].TrimStart('[')
    $level = ($parts[1] -split ' ')[0].TrimStart('[')
    $message = $parts[2]

    [PSCustomObject]@{
        Timestamp = $timestamp
        Level     = $level
        Message   = $message
    }
}

$logs | Export-Excel -Path "C:\Logs\LogReport_$(Get-Date -Format 'yyyyMMdd').xlsx" -WorksheetName "Logs" -AutoSize
```
**Explications** :
- **`ImportExcel`** : Module pour exporter vers Excel (plus flexible que CSV).
- **`-AutoSize`** : Ajuste la largeur des colonnes.

---

#### **2.2.2. Créer un rapport HTML**
```powershell
# Convertir les logs en HTML
$logs | ConvertTo-Html -Title "Rapport des logs - $(Get-Date -Format 'yyyy-MM-dd')" |
    Out-File -FilePath "C:\Logs\LogReport_$(Get-Date -Format 'yyyyMMdd').html"
```

---

### **2.3. Travaux pratiques : Analyser les logs système**
---
#### **Énoncé de l’exercice** :
**Objectif** :
1. **Lire un fichier de log** (`C:\Logs\ScriptLog_20251110.log`).
2. **Compter le nombre d’entrées** par niveau (`INFO`, `WARNING`, `ERROR`).
3. **Exporter les résultats** :
   - En **CSV** (`C:\Logs\LogStats.csv`).
   - En **Excel** (`C:\Logs\LogReport.xlsx`).
4. **Afficher un résumé** dans la console avec des couleurs.

---
#### **Corrigé** :
```powershell
# Chemin du fichier de log
$logFile = "C:\Logs\ScriptLog_$(Get-Date -Format 'yyyyMMdd').log"

# Vérifier si le fichier existe
if (-not (Test-Path -Path $logFile)) {
    Write-Host "Fichier de log introuvable : $logFile" -ForegroundColor Red
    exit
}

# Analyser les logs
$logEntries = Get-Content -Path $logFile | ForEach-Object {
    if ($_ -match '\[(INFO|WARNING|ERROR)\]') {
        $timestamp = ($_ -split '\]')[0].TrimStart('[')
        $level = ($_ -split '\]')[1].TrimStart(' [').Split(' ')[0]
        $message = ($_ -split '\] ')[1]

        [PSCustomObject]@{
            Timestamp = $timestamp
            Level     = $level
            Message   = $message
        }
    }
}

# Compter les occurrences par niveau
$logStats = $logEntries | Group-Object -Property Level | Select-Object Name, Count

# Afficher un résumé coloré
$logStats | ForEach-Object {
    switch ($_.Name) {
        "ERROR" { Write-Host "$($_.Name): $($_.Count)" -ForegroundColor Red }
        "WARNING" { Write-Host "$($_.Name): $($_.Count)" -ForegroundColor Yellow }
        default { Write-Host "$($_.Name): $($_.Count)" }
    }
}

# Exporter en CSV
$logStats | Export-Csv -Path "C:\Logs\LogStats_$(Get-Date -Format 'yyyyMMdd').csv" -NoTypeInformation

# Exporter en Excel (si le module est installé)
if (Get-Module -ListAvailable -Name ImportExcel) {
    Import-Module ImportExcel
    $logEntries | Export-Excel -Path "C:\Logs\LogReport_$(Get-Date -Format 'yyyyMMdd').xlsx" -WorksheetName "Logs" -AutoSize
    Write-Host "Rapport Excel généré." -ForegroundColor Green
} else {
    Write-Host "Module ImportExcel non installé. Utilisez 'Install-Module ImportExcel' pour exporter vers Excel." -ForegroundColor Yellow
}
```

**Explications** :
- **Regex** : Extrait le timestamp, le niveau et le message de chaque ligne.
- **`Group-Object`** : Compte les occurrences de chaque niveau.
- **Export Excel** : Optionnel (nécessite le module `ImportExcel`).

---

## **3. Travaux pratiques : Écrire un script qui génère un fichier de log avec des métriques système**

---
### **Énoncé du projet** :
**Objectif** :
Créer un script qui :
1. **Surveille les métriques système** toutes les 5 minutes :
   - Utilisation CPU (`% Processor Time`).
   - Mémoire disponible (`Available MBytes`).
   - Espace disque libre sur `C:` (en %).
   - Statut des services critiques (`Spooler`, `DHCP`, `DNS`).
2. **Écrit les métriques dans un fichier de log** (`C:\Logs\SystemMetrics.log`) avec :
   - Un **timestamp**.
   - Un **niveau de gravité** (`INFO`, `WARNING`, `ERROR`).
3. **Génère un rapport quotidien** :
   - **CSV** avec les métriques (`C:\Logs\SystemMetrics_Report.csv`).
   - **HTML** avec un résumé visuel (`C:\Logs\SystemMetrics_Report.html`).
4. **Archive les logs** plus anciens que 7 jours.

---
### **Corrigé** :
```powershell
<#
.SYNOPSIS
    Script de surveillance des métriques système avec logging et reporting.

.DESCRIPTION
    Ce script surveille le CPU, la mémoire, l'espace disque et les services critiques,
    puis génère des logs et des rapports quotidiens.
#>

# Chemin des fichiers
$logDir = "C:\Logs"
$logFile = Join-Path -Path $logDir -ChildPath "SystemMetrics_$(Get-Date -Format 'yyyyMMdd').log"
$csvReport = Join-Path -Path $logDir -ChildPath "SystemMetrics_Report_$(Get-Date -Format 'yyyyMMdd').csv"
$htmlReport = Join-Path -path $logDir -ChildPath "SystemMetrics_Report_$(Get-Date -Format 'yyyyMMdd').html"

# Services critiques à surveiller
$criticalServices = @("Spooler", "DHCP", "DNS")

# Créer le dossier de logs s'il n'existe pas
if (-not (Test-Path -Path $logDir)) {
    New-Item -ItemType Directory -Path $logDir -Force | Out-Null
}

# Fonction pour écrire dans le log
function Write-MetricLog {
    param (
        [string]$message,
        [string]$level = "INFO"
    )

    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $logEntry = "[$timestamp] [$level] $message"
    Add-Content -Path $logFile -Value $logEntry

    # Afficher dans la console
    switch ($level) {
        "ERROR" { Write-Host $logEntry -ForegroundColor Red }
        "WARNING" { Write-Host $logEntry -ForegroundColor Yellow }
        default { Write-Host $logEntry }
    }
}

# Fonction pour surveiller les métriques
function Monitor-System {
    # 1. CPU
    $cpu = (Get-Counter '\Processor(_Total)\% Processor Time').CounterSamples.CookedValue
    $cpuStatus = if ($cpu -gt 90) { "WARNING" } else { "INFO" }
    Write-MetricLog -message "CPU: $([math]::Round($cpu, 1))%" -level $cpuStatus

    # 2. Mémoire
    $mem = (Get-Counter '\Memory\Available MBytes').CounterSamples.CookedValue
    $memStatus = if ($mem -lt 1024) { "WARNING" } else { "INFO" }
    Write-MetricLog -message "Mémoire disponible: $([math]::Round($mem, 1)) Mo" -level $memStatus

    # 3. Espace disque C:
    $disk = Get-Volume -DriveLetter C -ErrorAction SilentlyContinue
    if ($disk) {
        $freePercent = ($disk.SizeRemaining / $disk.Size) * 100
        $diskStatus = if ($freePercent -lt 20) { "WARNING" } else { "INFO" }
        Write-MetricLog -message "Disque C: $([math]::Round($freePercent, 1))% libre" -level $diskStatus
    } else {
        Write-MetricLog -message "Disque C: introuvable" -level "ERROR"
    }

    # 4. Services critiques
    foreach ($service in $criticalServices) {
        $serviceObj = Get-Service -Name $service -ErrorAction SilentlyContinue
        if ($serviceObj) {
            $serviceStatus = if ($serviceObj.Status -eq "Running") { "INFO" } else { "ERROR" }
            Write-MetricLog -message "Service $service: $($serviceObj.Status)" -level $serviceStatus
        } else {
            Write-MetricLog -message "Service $service: introuvable" -level "ERROR"
        }
    }
}

# Fonction pour générer le rapport quotidien
function Generate-Report {
    # Lire les logs de la journée
    $logs = Get-Content -Path $logFile | ForEach-Object {
        if ($_ -match '\[(INFO|WARNING|ERROR)\]') {
            $timestamp = ($_ -split '\]')[0].TrimStart('[')
            $level = ($_ -split '\]')[1].TrimStart(' [').Split(' ')[0]
            $message = ($_ -split '\] ')[1]

            [PSCustomObject]@{
                Timestamp = $timestamp
                Level     = $level
                Metric    = ($message -split ': ')[0]
                Value     = ($message -split ': ')[1]
            }
        }
    }

    # Exporter en CSV
    $logs | Export-Csv -Path $csvReport -NoTypeInformation -Encoding UTF8

    # Générer un rapport HTML
    $htmlContent = @"
<!DOCTYPE html>
<html>
<head>
    <title>Rapport des métriques système - $(Get-Date -Format 'yyyy-MM-dd')</title>
    <style>
        body { font-family: Arial; margin: 20px; }
        h1 { color: #0066cc; }
        table { border-collapse: collapse; width: 100%; }
        th { background-color: #0066cc; color: white; padding: 8px; }
        td { border: 1px solid #ddd; padding: 8px; }
        tr:nth-child(even) { background-color: #f2f2f2; }
        .warning { background-color: #ffeb3b; }
        .error { background-color: #ff9999; }
    </style>
</head>
<body>
    <h1>Rapport des métriques système - $(Get-Date -Format 'yyyy-MM-dd')</h1>
    <table>
        <tr><th>Timestamp</th><th>Niveau</th><th>Métrique</th><th>Valeur</th></tr>
"@

    $logs | ForEach-Object {
        $rowClass = if ($_.Level -eq "ERROR") { "error" } elseif ($_.Level -eq "WARNING") { "warning" } else { "" }
        $htmlContent += "<tr class='$rowClass'><td>$($_.Timestamp)</td><td>$($_.Level)</td><td>$($_.Metric)</td><td>$($_.Value)</td></tr>`n"
    }

    $htmlContent += @"
    </table>
</body>
</html>
"@

    $htmlContent | Out-File -FilePath $htmlReport -Encoding UTF8

    Write-Host "Rapport généré : $htmlReport" -ForegroundColor Green
}

# Exécution principale
Write-MetricLog -message "=== Début de la surveillance ==="

# Surveiller les métriques toutes les 5 minutes pendant 1 heure (pour test)
for ($i = 0; $i -lt 12; $i++) {
    $currentTime = Get-Date -Format "HH:mm:ss"
    Write-MetricLog -message "--- Itération $($i + 1) ($currentTime) ---"

    Monitor-System

    if ($i -lt 11) {
        Start-Sleep -Seconds 300  # Attendre 5 minutes (300 secondes)
    }
}

# Générer le rapport
Generate-Report

# Archiver les logs plus anciens que 7 jours
Get-ChildItem -Path $logDir -Filter "SystemMetrics_*.log" | Where-Object {
    $_.LastWriteTime -lt (Get-Date).AddDays(-7)
} | ForEach-Object {
    $archiveName = $_.FullName.Replace(".log", "_$(Get-Date -Format 'yyyyMMdd').archive.log")
    Rename-Item -Path $_.FullName -NewName $archiveName -Force
}

Write-MetricLog -message "=== Fin de la surveillance ==="
```

---
### **Explications détaillées** :
1. **Fonction `Write-MetricLog`** :
   - Écrit les métriques dans le log avec un **timestamp** et un **niveau de gravité**.
   - Affiche les messages dans la console avec des **couleurs**.

2. **Fonction `Monitor-System`** :
   - **CPU** : Utilise `Get-Counter` pour récupérer l’utilisation.
   - **Mémoire** : Vérifie la mémoire disponible en Mo.
   - **Disque** : Calcule l’espace libre en % sur `C:`.
   - **Services** : Vérifie le statut des services critiques.

3. **Fonction `Generate-Report`** :
   - Lit le fichier de log et **parse** chaque ligne pour extraire les données.
   - Génère un **rapport HTML** avec un style CSS pour mettre en évidence les alertes.
   - Exporte aussi les données en **CSV**.

4. **Boucle principale** :
   - Exécute `Monitor-System` toutes les **5 minutes** (12 itérations pour 1 heure de test).
   - À la fin, génère le rapport et **archive les anciens logs**.

5. **Sortie HTML** :
   - Utilise des **classes CSS** (`warning`, `error`) pour colorer les lignes.
   - Affiche un tableau clair avec les métriques.

---
### **Planification du script**
Pour exécuter ce script **toutes les 5 minutes** en arrière-plan :
```powershell
$action = New-ScheduledJobAction -Execute "PowerShell.exe" -Argument "-NoProfile -File `"C:\Scripts\MonitorSystemMetrics.ps1`""
$trigger = New-JobTrigger -Once -At (Get-Date) -RepetitionInterval (New-TimeSpan -Minutes 5) -RepetitionDuration (New-TimeSpan -Hours 24)
Register-ScheduledJob -Name "SystemMetricsMonitor" -Action $action -Trigger $trigger
```

---

## **4. Synthèse et bonnes pratiques**

### **4.1. Résumé des concepts clés**
- **Logs structurés** : Utilisez des **timestamps**, des **niveaux de gravité** et des **messages clairs**.
- **Rotation des logs** : Archivez ou supprimez les anciens logs pour éviter la surcharge.
- **Analyse des logs** : Utilisez PowerShell pour filtrer, compter et exporter les données.
- **Rapports** : Générez des **CSV/HTML** pour un suivi visuel.

### **4.2. Bonnes pratiques**
- **Centralisez les logs** : Stockez-les dans un dossier dédié (ex: `C:\Logs`).
- **Sécurisez les logs** : Restreignez l’accès aux fichiers sensibles.
- **Automatisez** : Planifiez la collecte des métriques et la génération de rapports.
- **Testez** : Vérifiez que les logs sont lisibles et exploitables.

### **4.3. Exercices d’approfondissement**
1. **Logs distants** :
   - Configurer un script pour envoyer les logs vers un **serveur central** (via `Invoke-Command` ou un partage réseau).

2. **Intégration avec un SIEM** :
   - Exporter les logs vers un **système de gestion des événements** (ex: Splunk, ELK).

3. **Alertes en temps réel** :
   - Étendre le script pour envoyer des **notifications** (email, Teams) en cas d’erreur critique.

---
# **XIII - Etude de cas d'automatisation des taches courantes**
## **1. Contexte et objectifs du script**
### **1.1. Scénario**
**Problématique** :
Un administrateur système doit **automatiser la création de comptes utilisateurs**, **configurer leurs permissions**, et **surveiller les services critiques** sur un serveur Windows.
Le script doit :
- Créer des utilisateurs **Active Directory** depuis un fichier CSV.
- Ajouter ces utilisateurs à des **groupes spécifiques**.
- Configurer des **permissions sur des dossiers partagés**.
- **Surveiller les services critiques** et redémarrer ceux qui sont arrêtés.
- **Journaliser toutes les actions** pour un audit ultérieur.

---
### **1.2. Structure du script**
Le script sera **modulaire** avec les fonctions suivantes :
1. **`Import-UserData`** : Importer les données utilisateurs depuis un CSV.
2. **`Create-ADUsers`** : Créer les utilisateurs Active Directory.
3. **`Add-UsersToGroups`** : Ajouter les utilisateurs aux groupes AD.
4. **`Set-FolderPermissions`** : Configurer les permissions sur les dossiers.
5. **`Monitor-CriticalServices`** : Surveiller et redémarrer les services critiques.
6. **`Write-Log`** : Journaliser les actions et erreurs.

---

## **2. Création du script**

### **2.1. Préparation de l’environnement**
#### **2.1.1. Fichier CSV d’entrée (`Users.csv`)**
```csv
Prenom,Nom,Identifiant,OU,Email,GroupePrincipal,GroupeSecondaire,DossierAcces,Permission
Jean,Dupont,jdupont,OU=Utilisateurs,jean.dupont@domaine.local,Developpement,Projets,C:\Projets\Dev,Modify
Marie,Martin,mmartin,OU=Utilisateurs,marie.martin@domaine.local,Marketing,Projets,C:\Projets\Marketing,Read
```
**Explications** :
- **`DossierAcces`** : Dossier pour lequel configurer les permissions.
- **`Permission`** : Niveau de permission (`Read`, `Modify`, `FullControl`).

---

#### **2.1.2. Module ActiveDirectory**
```powershell
# Vérifier et importer le module ActiveDirectory
if (-not (Get-Module -Name ActiveDirectory -ListAvailable)) {
    Write-Host "Le module ActiveDirectory est requis. Installez-le via RSAT ou un contrôleur de domaine." -ForegroundColor Red
    exit
}
Import-Module ActiveDirectory
```

---

### **2.2. Fonction de journalisation (`Write-Log`)**
```powershell
function Write-Log {
    param (
        [string]$message,
        [ValidateSet("INFO", "WARNING", "ERROR")]
        [string]$level = "INFO",
        [string]$logFile = "C:\Logs\AdminTask_$(Get-Date -Format 'yyyyMMdd').log"
    )

    # Créer le dossier de logs s'il n'existe pas
    if (-not (Test-Path -Path (Split-Path -Path $logFile -Parent))) {
        New-Item -ItemType Directory -Path (Split-Path -Path $logFile -Parent) -Force | Out-Null
    }

    # Formater le message
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $logEntry = "[$timestamp] [$level] $message"

    # Écrire dans le fichier
    Add-Content -Path $logFile -Value $logEntry

    # Afficher dans la console avec couleur
    switch ($level) {
        "ERROR"   { Write-Host $logEntry -ForegroundColor Red }
        "WARNING" { Write-Host $logEntry -ForegroundColor Yellow }
        default   { Write-Host $logEntry }
    }
}
```
**Explications** :
- **`ValidateSet`** : Restreint `$level` aux valeurs `INFO`, `WARNING`, ou `ERROR`.
- **Couleurs** : Affiche les messages dans la console avec une couleur adaptée au niveau.

---

### **2.3. Importation des données utilisateurs (`Import-UserData`)**
```powershell
function Import-UserData {
    param (
        [Parameter(Mandatory=$true)]
        [string]$csvPath
    )

    # Vérifier que le fichier existe
    if (-not (Test-Path -Path $csvPath)) {
        Write-Log -message "Fichier introuvable : $csvPath" -level "ERROR"
        return $null
    }

    # Importer le CSV
    try {
        $users = Import-Csv -Path $csvPath
        Write-Log -message "Importation réussie de $($users.Count) utilisateurs depuis $csvPath"
        return $users
    }
    catch {
        Write-Log -message "Erreur lors de l'import du CSV : $_" -level "ERROR"
        return $null
    }
}
```
**Explications** :
- **`Test-Path`** : Vérifie si le fichier CSV existe.
- **`Import-Csv`** : Charge les données du CSV dans un tableau d’objets.

---

### **2.4. Création des utilisateurs AD (`Create-ADUsers`)**
```powershell
function Create-ADUsers {
    param (
        [Parameter(Mandatory=$true)]
        [array]$users,

        [string]$defaultPassword = "MotDePasse123!"
    )

    $createdUsers = @()
    foreach ($user in $users) {
        try {
            # Vérifier si l'utilisateur existe déjà
            $existingUser = Get-ADUser -Filter { SamAccountName -eq $user.Identifiant } -ErrorAction SilentlyContinue
            if ($existingUser) {
                Write-Log -message "L'utilisateur $($user.Identifiant) existe déjà." -level "WARNING"
                continue
            }

            # Créer le mot de passe sécurisé
            $securePassword = ConvertTo-SecureString $defaultPassword -AsPlainText -Force

            # Créer l'utilisateur
            New-ADUser -Name "$($user.Prenom) $($user.Nom)" `
                       -SamAccountName $user.Identifiant `
                       -GivenName $user.Prenom `
                       -Surname $user.Nom `
                       -Enabled $true `
                       -AccountPassword $securePassword `
                       -ChangePasswordAtLogon $true `
                       -Path $user.OU `
                       -EmailAddress $user.Email `
                       -ErrorAction Stop

            Write-Log -message "Utilisateur $($user.Identifiant) créé avec succès."
            $createdUsers += $user.Identifiant
        }
        catch {
            Write-Log -message "Erreur lors de la création de $($user.Identifiant) : $_" -level "ERROR"
        }
    }

    return $createdUsers
}
```
**Explications** :
- **`Get-ADUser`** : Vérifie si l’utilisateur existe déjà.
- **`New-ADUser`** : Crée l’utilisateur avec les attributs spécifiés.
- **`ChangePasswordAtLogon`** : Force l’utilisateur à changer son mot de passe à la première connexion.

---

### **2.5. Ajout des utilisateurs aux groupes (`Add-UsersToGroups`)**
```powershell
function Add-UsersToGroups {
    param (
        [Parameter(Mandatory=$true)]
        [array]$users
    )

    foreach ($user in $users) {
        try {
            # Ajouter au groupe principal
            if ($user.GroupePrincipal) {
                Add-ADGroupMember -Identity $user.GroupePrincipal -Members $user.Identifiant -ErrorAction Stop
                Write-Log -message "$($user.Identifiant) ajouté au groupe $($user.GroupePrincipal)."
            }

            # Ajouter aux groupes secondaires
            if ($user.GroupeSecondaire) {
                $secondaryGroups = $user.GroupeSecondaire -split ','
                foreach ($group in $secondaryGroups) {
                    Add-ADGroupMember -Identity $group -Members $user.Identifiant -ErrorAction Stop
                    Write-Log -message "$($user.Identifiant) ajouté au groupe secondaire $group."
                }
            }
        }
        catch {
            Write-Log -message "Erreur lors de l'ajout de $($user.Identifiant) aux groupes : $_" -level "ERROR"
        }
    }
}
```
**Explications** :
- **`Add-ADGroupMember`** : Ajoute un utilisateur à un groupe AD.
- **`-split ','`** : Sépare les groupes secondaires (si plusieurs).

---

### **2.6. Configuration des permissions sur les dossiers (`Set-FolderPermissions`)**
```powershell
function Set-FolderPermissions {
    param (
        [Parameter(Mandatory=$true)]
        [array]$users
    )

    foreach ($user in $users) {
        try {
            if ($user.DossierAcces -and (Test-Path -Path $user.DossierAcces)) {
                # Récupérer l'ACL actuelle
                $acl = Get-Acl -Path $user.DossierAcces

                # Définir la règle de permission
                $permissionLevel = switch ($user.Permission) {
                    "Read"       { "ReadAndExecute" }
                    "Modify"     { "Modify" }
                    "FullControl"{ "FullControl" }
                    default      { "ReadAndExecute" }
                }

                # Créer la règle
                $rule = New-Object System.Security.AccessControl.FileSystemAccessRule(
                    "$($user.Identifiant)",
                    $permissionLevel,
                    "ContainerInherit, ObjectInherit",
                    "None",
                    "Allow"
                )

                # Appliquer la règle
                $acl.AddAccessRule($rule)
                Set-Acl -Path $user.DossierAcces -AclObject $acl

                Write-Log -message "Permissions $permissionLevel accordées à $($user.Identifiant) sur $($user.DossierAcces)."
            } else {
                Write-Log -message "Dossier $($user.DossierAcces) introuvable pour $($user.Identifiant)." -level "WARNING"
            }
        }
        catch {
            Write-Log -message "Erreur lors de la configuration des permissions pour $($user.Identifiant) : $_" -level "ERROR"
        }
    }
}
```
**Explications** :
- **`Get-Acl` / `Set-Acl`** : Récupère et applique les permissions sur un dossier.
- **`FileSystemAccessRule`** : Définit les droits d’accès (ex: `ReadAndExecute`, `Modify`).
- **`ContainerInherit`** : Applique les permissions aux sous-dossiers.

---

### **2.7. Surveillance des services critiques (`Monitor-CriticalServices`)**
```powershell
function Monitor-CriticalServices {
    param (
        [array]$services = @("Spooler", "DHCP", "DNS")
    )

    foreach ($service in $services) {
        try {
            $serviceObj = Get-Service -Name $service -ErrorAction Stop
            if ($serviceObj.Status -ne "Running") {
                Start-Service -Name $service -ErrorAction Stop
                Write-Log -message "Service $service redémarré (statut précédent : $($serviceObj.Status))."
            } else {
                Write-Log -message "Service $service est en cours d'exécution."
            }
        }
        catch {
            Write-Log -message "Erreur lors de la surveillance du service $service : $_" -level "ERROR"
        }
    }
}
```
**Explications** :
- **`Get-Service`** : Récupère l’état du service.
- **`Start-Service`** : Redémarre le service s’il est arrêté.

---

### **2.8. Script principal (`Main`)**
```powershell
# =============================================
# SCRIPT PRINCIPAL
# =============================================

# Chemin du fichier CSV
$csvPath = "C:\Temp\Users.csv"

# 1. Importer les données utilisateurs
$users = Import-UserData -csvPath $csvPath
if (-not $users) {
    Write-Log -message "Aucun utilisateur à traiter. Arrêt du script." -level "ERROR"
    exit
}

# 2. Créer les utilisateurs AD
$createdUsers = Create-ADUsers -users $users
if (-not $createdUsers) {
    Write-Log -message "Aucun utilisateur créé. Vérifiez les erreurs." -level "WARNING"
}

# 3. Ajouter les utilisateurs aux groupes
Add-UsersToGroups -users $users

# 4. Configurer les permissions sur les dossiers
Set-FolderPermissions -users $users

# 5. Surveiller les services critiques
Monitor-CriticalServices

# 6. Fin du script
Write-Log -message "=== Script terminé avec succès ==="
```

---

## **3. Travaux pratiques : Écrire un script pour une tâche d’administration complète**

---
### **Énoncé du projet** :
**Objectif** :
Créer un script qui :
1. **Importe une liste d’utilisateurs** depuis un fichier CSV (`C:\Temp\NewUsers.csv`).
2. **Crée les utilisateurs Active Directory** avec un mot de passe par défaut.
3. **Ajoute les utilisateurs à des groupes** (principal et secondaire).
4. **Configure les permissions** sur des dossiers partagés (ex: `C:\Projets\*`).
5. **Surveille et redémarre les services critiques** (`Spooler`, `DHCP`).
6. **Journalise toutes les actions** dans `C:\Logs\AdminTask.log`.
7. **Gère les erreurs** (ex: utilisateur déjà existant, dossier introuvable).

**Fichier CSV (`NewUsers.csv`)** :
```csv
Prenom,Nom,Identifiant,OU,Email,GroupePrincipal,GroupeSecondaire,DossierAcces,Permission
Pierre,Durand,pdurand,OU=Utilisateurs,pierre.durand@domaine.local,Developpement,Projets,C:\Projets\Dev,Modify
Sophie,Lambert,slambert,OU=Utilisateurs,sophie.lambert@domaine.local,Marketing,,C:\Projets\Marketing,Read
```

---
### **Corrigé** :
```powershell
# =============================================
# SCRIPT : AUTOMATISATION DE LA GESTION DES UTILISATEURS
# =============================================

# 1. Fonction de journalisation
function Write-Log {
    param (
        [string]$message,
        [ValidateSet("INFO", "WARNING", "ERROR")]
        [string]$level = "INFO",
        [string]$logFile = "C:\Logs\AdminTask_$(Get-Date -Format 'yyyyMMdd').log"
    )

    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $logEntry = "[$timestamp] [$level] $message"

    # Créer le dossier si nécessaire
    if (-not (Test-Path -Path (Split-Path -Path $logFile -Parent))) {
        New-Item -ItemType Directory -Path (Split-Path -Path $logFile -Parent) -Force | Out-Null
    }

    Add-Content -Path $logFile -Value $logEntry

    switch ($level) {
        "ERROR"   { Write-Host $logEntry -ForegroundColor Red }
        "WARNING" { Write-Host $logEntry -ForegroundColor Yellow }
        default   { Write-Host $logEntry }
    }
}

# 2. Importer les données utilisateurs
function Import-UserData {
    param ([string]$csvPath)

    if (-not (Test-Path -Path $csvPath)) {
        Write-Log -message "Fichier introuvable : $csvPath" -level "ERROR"
        return $null
    }

    try {
        $users = Import-Csv -Path $csvPath
        Write-Log -message "Importation réussie de $($users.Count) utilisateurs depuis $csvPath"
        return $users
    }
    catch {
        Write-Log -message "Erreur lors de l'import du CSV : $_" -level "ERROR"
        return $null
    }
}

# 3. Créer les utilisateurs AD
function Create-ADUsers {
    param (
        [array]$users,
        [string]$defaultPassword = "MotDePasse123!"
    )

    $createdUsers = @()
    foreach ($user in $users) {
        try {
            $existingUser = Get-ADUser -Filter { SamAccountName -eq $user.Identifiant } -ErrorAction SilentlyContinue
            if ($existingUser) {
                Write-Log -message "L'utilisateur $($user.Identifiant) existe déjà." -level "WARNING"
                continue
            }

            $securePassword = ConvertTo-SecureString $defaultPassword -AsPlainText -Force
            New-ADUser -Name "$($user.Prenom) $($user.Nom)" `
                       -SamAccountName $user.Identifiant `
                       -GivenName $user.Prenom `
                       -Surname $user.Nom `
                       -Enabled $true `
                       -AccountPassword $securePassword `
                       -ChangePasswordAtLogon $true `
                       -Path $user.OU `
                       -EmailAddress $user.Email `
                       -ErrorAction Stop

            Write-Log -message "Utilisateur $($user.Identifiant) créé avec succès."
            $createdUsers += $user.Identifiant
        }
        catch {
            Write-Log -message "Erreur lors de la création de $($user.Identifiant) : $_" -level "ERROR"
        }
    }

    return $createdUsers
}

# 4. Ajouter les utilisateurs aux groupes
function Add-UsersToGroups {
    param ([array]$users)

    foreach ($user in $users) {
        try {
            if ($user.GroupePrincipal) {
                Add-ADGroupMember -Identity $user.GroupePrincipal -Members $user.Identifiant -ErrorAction Stop
                Write-Log -message "$($user.Identifiant) ajouté au groupe $($user.GroupePrincipal)."
            }

            if ($user.GroupeSecondaire) {
                $secondaryGroups = $user.GroupeSecondaire -split ','
                foreach ($group in $secondaryGroups) {
                    Add-ADGroupMember -Identity $group -Members $user.Identifiant -ErrorAction Stop
                    Write-Log -message "$($user.Identifiant) ajouté au groupe secondaire $group."
                }
            }
        }
        catch {
            Write-Log -message "Erreur lors de l'ajout de $($user.Identifiant) aux groupes : $_" -level "ERROR"
        }
    }
}

# 5. Configurer les permissions sur les dossiers
function Set-FolderPermissions {
    param ([array]$users)

    foreach ($user in $users) {
        try {
            if ($user.DossierAcces -and (Test-Path -Path $user.DossierAcces)) {
                $acl = Get-Acl -Path $user.DossierAcces

                $permissionLevel = switch ($user.Permission) {
                    "Read"       { "ReadAndExecute" }
                    "Modify"     { "Modify" }
                    "FullControl"{ "FullControl" }
                    default      { "ReadAndExecute" }
                }

                $rule = New-Object System.Security.AccessControl.FileSystemAccessRule(
                    "$($user.Identifiant)",
                    $permissionLevel,
                    "ContainerInherit, ObjectInherit",
                    "None",
                    "Allow"
                )

                $acl.AddAccessRule($rule)
                Set-Acl -Path $user.DossierAcces -AclObject $acl

                Write-Log -message "Permissions $permissionLevel accordées à $($user.Identifiant) sur $($user.DossierAcces)."
            } else {
                Write-Log -message "Dossier $($user.DossierAcces) introuvable pour $($user.Identifiant)." -level "WARNING"
            }
        }
        catch {
            Write-Log -message "Erreur lors de la configuration des permissions pour $($user.Identifiant) : $_" -level "ERROR"
        }
    }
}

# 6. Surveiller les services critiques
function Monitor-CriticalServices {
    param (
        [array]$services = @("Spooler", "DHCP", "DNS")
    )

    foreach ($service in $services) {
        try {
            $serviceObj = Get-Service -Name $service -ErrorAction Stop
            if ($serviceObj.Status -ne "Running") {
                Start-Service -Name $service -ErrorAction Stop
                Write-Log -message "Service $service redémarré (statut précédent : $($serviceObj.Status))."
            } else {
                Write-Log -message "Service $service est en cours d'exécution."
            }
        }
        catch {
            Write-Log -message "Erreur lors de la surveillance du service $service : $_" -level "ERROR"
        }
    }
}

# =============================================
# EXÉCUTION DU SCRIPT
# =============================================

# Chemin du fichier CSV
$csvPath = "C:\Temp\NewUsers.csv"

# 1. Importer les données
$users = Import-UserData -csvPath $csvPath
if (-not $users) {
    Write-Log -message "Aucun utilisateur à traiter. Arrêt du script." -level "ERROR"
    exit
}

# 2. Créer les utilisateurs AD
$createdUsers = Create-ADUsers -users $users
if (-not $createdUsers) {
    Write-Log -message "Aucun utilisateur créé. Vérifiez les erreurs." -level "WARNING"
}

# 3. Ajouter aux groupes
Add-UsersToGroups -users $users

# 4. Configurer les permissions
Set-FolderPermissions -users $users

# 5. Surveiller les services
Monitor-CriticalServices

# 6. Fin du script
Write-Log -message "=== Script terminé avec succès ==="
```

---
### **Explications détaillées** :
1. **Modularité** :
   - Chaque fonction a un **rôle spécifique** (ex: `Create-ADUsers`, `Set-FolderPermissions`).
   - **Réutilisable** : Les fonctions peuvent être appelées indépendamment.

2. **Gestion des erreurs** :
   - **`try/catch`** : Capture les erreurs et les journalise.
   - **Vérifications** : Existence des utilisateurs, dossiers, services.

3. **Journalisation** :
   - **Niveaux de gravité** (`INFO`, `WARNING`, `ERROR`).
   - **Couleurs** dans la console pour une lecture facile.

4. **Automatisation complète** :
   - De la **création des utilisateurs** à la **configuration des permissions**.
   - **Surveillance des services** pour garantir leur disponibilité.

5. **Extensibilité** :
   - Ajoutez d’autres **fonctions** (ex: suppression d’utilisateurs, sauvegarde des logs).
   - **Paramétrez** le script pour adapter les chemins, mots de passe, etc.

---
### **Exemple de sortie dans le log** :
```
[2025-11-15 14:30:45] [INFO] Importation réussie de 2 utilisateurs depuis C:\Temp\NewUsers.csv
[2025-11-15 14:30:46] [INFO] Utilisateur pdurand créé avec succès.
[2025-11-15 14:30:47] [INFO] pdurand ajouté au groupe Developpement.
[2025-11-15 14:30:48] [INFO] Permissions Modify accordées à pdurand sur C:\Projets\Dev.
[2025-11-15 14:30:49] [INFO] Service Spooler est en cours d'exécution.
[2025-11-15 14:30:50] [INFO] === Script terminé avec succès ===
```

---

## **4. Synthèse et bonnes pratiques**

### **4.1. Résumé des concepts clés**
- **Modularité** : Divisez le script en **fonctions réutilisables**.
- **Gestion des erreurs** : Utilisez `try/catch` et vérifiez les conditions avant d’agir.
- **Journalisation** : Enregistrez **toutes les actions** pour un audit.
- **Automatisation** : Combinez plusieurs tâches (utilisateurs, permissions, services).

### **4.2. Bonnes pratiques**
- **Testez en environnement sécurisé** avant de déployer en production.
- **Documentation** : Commentez chaque fonction et ajoutez des exemples d’utilisation.
- **Sécurité** :
  - **Mots de passe** : Ne les stockez pas en clair (utilisez `ConvertTo-SecureString`).
  - **Permissions** : Appliquez le principe du **moindre privilège**.
- **Maintenance** :
  - **Archivez les logs** pour éviter la surcharge.
  - **Mettez à jour le script** régulièrement (ex: ajouter de nouveaux services à surveiller).

### **4.3. Exercices d’approfondissement**
1. **Suppression des utilisateurs inactifs** :
   - Étendre le script pour **supprimer les utilisateurs inactifs depuis 90 jours**.

2. **Sauvegarde des logs** :
   - Configurer une **tâche planifiée** pour archiver les logs mensuellement.

3. **Intégration avec un SIEM** :
   - Exporter les logs vers un **système de gestion des événements** (ex: Splunk).

4. **Notifications en temps réel** :
   - Ajouter des **alertes par email** ou **notifications Windows** pour les erreurs critiques.

---
# **XIV - Optimisation des scripts et bonnes pratiques**
## **1. Structurer et documenter un script**

### **1.1. Structure d’un script PowerShell professionnel**
Un script bien structuré doit inclure :
1. **En-tête de documentation** (bloc de commentaires).
2. **Déclaration des paramètres** (`param`).
3. **Fonctions modulaires** (une tâche = une fonction).
4. **Gestion des erreurs** (`try/catch`).
5. **Logging et reporting**.
6. **Exemples d’utilisation**.

---
### **1.2. Documentation avec les blocs de commentaires**
#### **1.2.1. Bloc `.SYNOPSIS` et `.DESCRIPTION`**
```powershell
<#
.SYNOPSIS
    Crée et configure des utilisateurs Active Directory à partir d'un fichier CSV.

.DESCRIPTION
    Ce script importe une liste d'utilisateurs depuis un fichier CSV,
    les crée dans Active Directory, les ajoute à des groupes,
    et configure leurs permissions sur des dossiers partagés.
    Il inclut une journalisation détaillée et une gestion des erreurs.

.PARAMETER CsvPath
    Chemin vers le fichier CSV contenant les données des utilisateurs.

.PARAMETER DefaultPassword
    Mot de passe par défaut pour les nouveaux utilisateurs (par défaut : "MotDePasse123!").

.EXAMPLE
    .\New-ADUsersFromCsv.ps1 -CsvPath "C:\Temp\Users.csv" -DefaultPassword "MonMotDePasse123!"

.EXAMPLE
    # Utilisation avec les paramètres par défaut
    .\New-ADUsersFromCsv.ps1 -CsvPath "C:\Temp\Users.csv"
#>
```
**Explications** :
- **`.SYNOPSIS`** : Résumé court du script.
- **`.DESCRIPTION`** : Description détaillée.
- **`.PARAMETER`** : Explication de chaque paramètre.
- **`.EXAMPLE`** : Exemples d’utilisation.

---
#### **1.2.2. Documentation des fonctions**
```powershell
function Create-ADUser {
    <#
    .SYNOPSIS
        Crée un utilisateur Active Directory.

    .DESCRIPTION
        Crée un utilisateur AD avec les attributs spécifiés (nom, prénom, OU, etc.).
        Gère les erreurs si l'utilisateur existe déjà.

    .PARAMETER UserData
        Objet contenant les données de l'utilisateur (Prenom, Nom, Identifiant, etc.).

    .PARAMETER DefaultPassword
        Mot de passe par défaut pour le nouvel utilisateur.

    .EXAMPLE
        Create-ADUser -UserData $user -DefaultPassword "MotDePasse123!"
    #>
    param (
        [Parameter(Mandatory=$true)]
        [object]$UserData,

        [string]$DefaultPassword = "MotDePasse123!"
    )

    # ... (code de la fonction)
}
```
**Explications** :
- Chaque fonction doit avoir sa propre **documentation**.
- Utilisez **`.PARAMETER`** pour décrire les paramètres.

---
### **1.3. Utilisation des paramètres avancés**
#### **1.3.1. Paramètres obligatoires et optionnels**
```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]$CsvPath,

    [string]$DefaultPassword = "MotDePasse123!",

    [ValidateSet("INFO", "WARNING", "ERROR")]
    [string]$LogLevel = "INFO",

    [switch]$WhatIf
)
```
**Explications** :
- **`Mandatory=$true`** : Rend le paramètre obligatoire.
- **`ValidateSet`** : Restreint les valeurs possibles (`INFO`, `WARNING`, `ERROR`).
- **`[switch]$WhatIf`** : Permet un mode "simulation" (`-WhatIf`).

---
#### **1.3.2. Paramètres de type `ValidateScript`**
```powershell
param (
    [Parameter(Mandatory=$true)]
    [ValidateScript({ Test-Path -Path $_ -PathType Leaf })]
    [string]$CsvPath
)
```
**Explications** :
- **`ValidateScript`** : Valide que le fichier CSV existe avant l’exécution.

---
### **1.4. Travaux pratiques : Documenter un script existant**
---
#### **Énoncé de l’exercice** :
**Objectif** :
1. Prenez le script suivant (simplifié) et **ajoutez une documentation complète** :
   - Bloc `.SYNOPSIS` et `.DESCRIPTION`.
   - Documentation pour chaque fonction.
   - Exemples d’utilisation.
2. Ajoutez un **paramètre `LogLevel`** pour contrôler le niveau de détail des logs.

**Script à documenter** :
```powershell
function Create-User {
    param ($userData)
    New-ADUser -Name "$($userData.Prenom) $($userData.Nom)" -SamAccountName $userData.Identifiant
}

$users = Import-Csv -Path "C:\Temp\Users.csv"
foreach ($user in $users) {
    Create-User -userData $user
}
```

---
#### **Corrigé** :
```powershell
<#
.SYNOPSIS
    Crée des utilisateurs Active Directory à partir d'un fichier CSV.

.DESCRIPTION
    Ce script importe une liste d'utilisateurs depuis un fichier CSV
    et les crée dans Active Directory.

.PARAMETER CsvPath
    Chemin vers le fichier CSV contenant les données des utilisateurs.

.PARAMETER LogLevel
    Niveau de détail des logs (INFO, WARNING, ERROR). Par défaut : INFO.

.EXAMPLE
    .\Create-Users.ps1 -CsvPath "C:\Temp\Users.csv"

.EXAMPLE
    .\Create-Users.ps1 -CsvPath "C:\Temp\Users.csv" -LogLevel "WARNING"
#>
param (
    [Parameter(Mandatory=$true)]
    [ValidateScript({ Test-Path -Path $_ -PathType Leaf })]
    [string]$CsvPath,

    [ValidateSet("INFO", "WARNING", "ERROR")]
    [string]$LogLevel = "INFO"
)

function Write-Log {
    param (
        [string]$message,
        [string]$level
    )

    if ($level -ge $LogLevel) {
        Write-Host "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] [$level] $message"
    }
}

function Create-User {
    <#
    .SYNOPSIS
        Crée un utilisateur Active Directory.

    .DESCRIPTION
        Crée un utilisateur AD avec les attributs spécifiés dans $userData.

    .PARAMETER UserData
        Objet contenant les données de l'utilisateur (Prenom, Nom, Identifiant, etc.).

    .EXAMPLE
        Create-User -UserData $user
    #>
    param (
        [Parameter(Mandatory=$true)]
        [object]$UserData
    )

    try {
        New-ADUser -Name "$($UserData.Prenom) $($UserData.Nom)" -SamAccountName $UserData.Identifiant -ErrorAction Stop
        Write-Log -message "Utilisateur $($UserData.Identifiant) créé avec succès." -level "INFO"
    }
    catch {
        Write-Log -message "Erreur lors de la création de $($UserData.Identifiant) : $_" -level "ERROR"
    }
}

# Importation des utilisateurs
try {
    $users = Import-Csv -Path $CsvPath -ErrorAction Stop
    Write-Log -message "Importation de $($users.Count) utilisateurs depuis $CsvPath." -level "INFO"
}
catch {
    Write-Log -message "Erreur lors de l'import du CSV : $_" -level "ERROR"
    exit
}

# Création des utilisateurs
foreach ($user in $users) {
    Create-User -UserData $user
}

Write-Log -message "Script terminé." -level "INFO"
```

---
## **2. Réutilisation et modularité des scripts**

### **2.1. Principes de modularité**
- **Une fonction = une tâche** : Chaque fonction doit faire une seule chose.
- **Paramètres flexibles** : Permettent d’adapter le comportement.
- **Retour de valeurs** : Utilisez `return` pour renvoyer des résultats.
- **Réutilisabilité** : Écrivez des fonctions génériques (ex: `Write-Log` peut être utilisée dans n’importe quel script).

---
### **2.2. Exemple : Module de logging réutilisable**
#### **2.2.1. Script : `LoggingModule.psm1`**
```powershell
<#
.MODULE
    Name: LoggingModule
    Description: Module pour la journalisation des scripts PowerShell.
#>

function Write-Log {
    <#
    .SYNOPSIS
        Écrit un message dans un fichier de log avec un niveau de gravité.

    .PARAMETER Message
        Message à journaliser.

    .PARAMETER Level
        Niveau de gravité (INFO, WARNING, ERROR). Par défaut : INFO.

    .PARAMETER LogFile
        Chemin du fichier de log. Par défaut : C:\Logs\ScriptLog.log.

    .EXAMPLE
        Write-Log -Message "Début du script." -Level "INFO"
    #>
    param (
        [Parameter(Mandatory=$true)]
        [string]$Message,

        [ValidateSet("INFO", "WARNING", "ERROR")]
        [string]$Level = "INFO",

        [string]$LogFile = "C:\Logs\ScriptLog_$(Get-Date -Format 'yyyyMMdd').log"
    )

    # Créer le dossier si nécessaire
    if (-not (Test-Path -Path (Split-Path -Path $LogFile -Parent))) {
        New-Item -ItemType Directory -Path (Split-Path -Path $LogFile -Parent) -Force | Out-Null
    }

    # Formater le message
    $timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $logEntry = "[$timestamp] [$Level] $Message"

    # Écrire dans le fichier
    Add-Content -Path $LogFile -Value $logEntry

    # Afficher dans la console avec couleur
    switch ($Level) {
        "ERROR"   { Write-Host $logEntry -ForegroundColor Red }
        "WARNING" { Write-Host $logEntry -ForegroundColor Yellow }
        default   { Write-Host $logEntry }
    }
}

Export-ModuleMember -Function Write-Log
```

---
#### **2.2.2. Utilisation du module**
```powershell
# Importer le module
Import-Module .\LoggingModule.psm1 -Force

# Utiliser la fonction Write-Log
Write-Log -Message "Début du script." -Level "INFO"
Write-Log -Message "Attention, espace disque faible!" -Level "WARNING"
```

---
### **2.3. Gestion des dépendances**
#### **2.3.1. Vérification des modules requis**
```powershell
# Vérifier si le module ActiveDirectory est disponible
if (-not (Get-Module -Name ActiveDirectory -ListAvailable)) {
    Write-Log -Message "Le module ActiveDirectory est requis. Installez-le via RSAT." -Level "ERROR"
    exit
}
Import-Module ActiveDirectory
```

---
#### **2.3.2. Installation automatique des modules manquants**
```powershell
# Installer un module si nécessaire (ex: ImportExcel)
$requiredModule = "ImportExcel"
if (-not (Get-Module -Name $requiredModule -ListAvailable)) {
    Write-Log -Message "Installation du module $requiredModule..." -Level "INFO"
    Install-Module -Name $requiredModule -Force -Scope CurrentUser
}
Import-Module $requiredModule
```

---
### **2.4. Travaux pratiques : Modulariser un script existant**
---
#### **Énoncé de l’exercice** :
**Objectif** :
1. Prenez le script suivant et **modularisez-le** :
   - Créez un **module** pour la gestion des logs (`LoggingModule.psm1`).
   - Créez une **fonction** pour la création des utilisateurs (`New-ADUserFromCsv`).
   - Utilisez des **paramètres** pour rendre le script flexible.
2. Ajoutez une **gestion des erreurs** avec `try/catch`.

**Script à modulariser** :
```powershell
$users = Import-Csv -Path "C:\Temp\Users.csv"
foreach ($user in $users) {
    New-ADUser -Name "$($user.Prenom) $($user.Nom)" -SamAccountName $user.Identifiant
}
```

---
#### **Corrigé** :
**Fichier : `LoggingModule.psm1`** (voir section 2.2.1).

**Fichier : `UserManagement.psm1`**
```powershell
<#
.MODULE
    Name: UserManagement
    Description: Module pour la gestion des utilisateurs Active Directory.
#>

function New-ADUserFromCsv {
    <#
    .SYNOPSIS
        Crée des utilisateurs AD à partir d'un fichier CSV.

    .PARAMETER CsvPath
        Chemin vers le fichier CSV.

    .PARAMETER DefaultPassword
        Mot de passe par défaut pour les nouveaux utilisateurs.

    .EXAMPLE
        New-ADUserFromCsv -CsvPath "C:\Temp\Users.csv" -DefaultPassword "MotDePasse123!"
    #>
    param (
        [Parameter(Mandatory=$true)]
        [ValidateScript({ Test-Path -Path $_ -PathType Leaf })]
        [string]$CsvPath,

        [string]$DefaultPassword = "MotDePasse123!"
    )

    try {
        $users = Import-Csv -Path $CsvPath -ErrorAction Stop
        Write-Log -Message "Importation de $($users.Count) utilisateurs depuis $CsvPath." -Level "INFO"

        foreach ($user in $users) {
            try {
                $securePassword = ConvertTo-SecureString $DefaultPassword -AsPlainText -Force
                New-ADUser -Name "$($user.Prenom) $($user.Nom)" `
                           -SamAccountName $user.Identifiant `
                           -AccountPassword $securePassword `
                           -ChangePasswordAtLogon $true `
                           -ErrorAction Stop
                Write-Log -Message "Utilisateur $($user.Identifiant) créé avec succès." -Level "INFO"
            }
            catch {
                Write-Log -Message "Erreur lors de la création de $($user.Identifiant) : $_" -Level "ERROR"
            }
        }
    }
    catch {
        Write-Log -Message "Erreur lors de l'import du CSV : $_" -Level "ERROR"
    }
}

Export-ModuleMember -Function New-ADUserFromCsv
```

**Script principal : `Main.ps1`**
```powershell
# Importer les modules
Import-Module .\LoggingModule.psm1 -Force
Import-Module .\UserManagement.psm1 -Force

# Exécuter la création des utilisateurs
New-ADUserFromCsv -CsvPath "C:\Temp\Users.csv" -DefaultPassword "MotDePasse123!"
```

---

## **3. Travaux pratiques : Améliorer un script existant**

---
### **Énoncé du projet** :
**Objectif** :
Améliorez le script suivant en ajoutant :
1. **Documentation complète** (bloc `.SYNOPSIS`, `.DESCRIPTION`, `.EXAMPLE`).
2. **Gestion des erreurs** (`try/catch`).
3. **Logging avancé** (niveaux `INFO`, `WARNING`, `ERROR`).
4. **Paramètres flexibles** :
   - Chemin du fichier CSV.
   - Mot de passe par défaut.
   - Niveau de log.
5. **Modularité** :
   - Séparer la logique dans des fonctions.
   - Utiliser un module de logging.

**Script de base** :
```powershell
$users = Import-Csv -Path "C:\Temp\Users.csv"
foreach ($user in $users) {
    New-ADUser -Name "$($user.Prenom) $($user.Nom)" -SamAccountName $user.Identifiant
}
```

---
### **Corrigé** :
**Structure des fichiers** :
```
Project/
├── Modules/
│   ├── LoggingModule.psm1
│   └── UserManagement.psm1
└── Main.ps1
```

**1. `LoggingModule.psm1`** (voir section 2.2.1).

**2. `UserManagement.psm1`** (voir section 2.4).

**3. `Main.ps1`** :
```powershell
<#
.SYNOPSIS
    Script principal pour la gestion des utilisateurs Active Directory.

.DESCRIPTION
    Ce script utilise les modules LoggingModule et UserManagement
    pour créer des utilisateurs AD à partir d'un fichier CSV.

.PARAMETER CsvPath
    Chemin vers le fichier CSV contenant les utilisateurs.

.PARAMETER DefaultPassword
    Mot de passe par défaut pour les nouveaux utilisateurs.

.PARAMETER LogLevel
    Niveau de détail des logs (INFO, WARNING, ERROR).

.EXAMPLE
    .\Main.ps1 -CsvPath "C:\Temp\Users.csv" -DefaultPassword "MotDePasse123!" -LogLevel "INFO"
#>
param (
    [Parameter(Mandatory=$true)]
    [ValidateScript({ Test-Path -Path $_ -PathType Leaf })]
    [string]$CsvPath,

    [string]$DefaultPassword = "MotDePasse123!",

    [ValidateSet("INFO", "WARNING", "ERROR")]
    [string]$LogLevel = "INFO"
)

# Importer les modules
Import-Module .\Modules\LoggingModule.psm1 -Force
Import-Module .\Modules\UserManagement.psm1 -Force

# Exécuter la création des utilisateurs
New-ADUserFromCsv -CsvPath $CsvPath -DefaultPassword $DefaultPassword
```

---
### **Explications détaillées** :
1. **Documentation** :
   - Chaque script et fonction a un **bloc de commentaires** complet.
   - **Exemples d’utilisation** pour faciliter la prise en main.

2. **Modularité** :
   - **`LoggingModule.psm1`** : Gère les logs de manière centralisée.
   - **`UserManagement.psm1`** : Contient la logique métier (création d’utilisateurs).
   - **`Main.ps1`** : Point d’entrée du script, appelle les modules.

3. **Gestion des erreurs** :
   - **`try/catch`** dans chaque fonction pour capturer les erreurs.
   - **Logs détaillés** pour le débogage.

4. **Paramètres flexibles** :
   - **`CsvPath`** : Chemin personnalisable du fichier CSV.
   - **`DefaultPassword`** : Mot de passe configurable.
   - **`LogLevel`** : Contrôle le niveau de détail des logs.

5. **Réutilisabilité** :
   - Les modules peuvent être **réutilisés** dans d’autres scripts.
   - **Facile à maintenir** : Chaque composant est isolé.

---

## **4. Bonnes pratiques et optimisations avancées**

### **4.1. Résumé des bonnes pratiques**
| Pratique                  | Description                                                                 |
|---------------------------|-----------------------------------------------------------------------------|
| **Documentation**         | Utilisez des blocs `.SYNOPSIS`, `.DESCRIPTION`, et `.EXAMPLE`.             |
| **Modularité**            | Séparez la logique en fonctions/modules réutilisables.                       |
| **Gestion des erreurs**   | Utilisez `try/catch` et `ErrorAction`.                                    |
| **Logging**               | Journalisez les actions et erreurs avec des niveaux de gravité.         |
| **Paramètres**            | Utilisez des paramètres pour rendre les scripts flexibles.                |
| **Validation**            | Validez les entrées avec `ValidateScript`, `ValidateSet`.                 |
| **Tests**                 | Testez les scripts dans un environnement non productif.                   |
| **Performance**           | Évitez les boucles inutiles, utilisez le pipeline.                        |
| **Sécurité**              | Ne stockez pas les mots de passe en clair.                                |
| **Compatibilité**         | Testez sur différentes versions de PowerShell/Windows.                   |

---
### **4.2. Optimisations avancées**
#### **4.2.1. Utilisation du pipeline pour la performance**
```powershell
# Exemple non optimisé (boucle foreach)
$users = Import-Csv -Path "C:\Temp\Users.csv"
foreach ($user in $users) {
    New-ADUser -Name "$($user.Prenom) $($user.Nom)" -SamAccountName $user.Identifiant
}

# Exemple optimisé (pipeline)
Import-Csv -Path "C:\Temp\Users.csv" | ForEach-Object {
    New-ADUser -Name "$($_.Prenom) $($_.Nom)" -SamAccountName $_.Identifiant
}
```
**Explications** :
- Le **pipeline** est plus efficace pour traiter des collections.

---
#### **4.2.2. Parallelisation avec `ForEach-Object -Parallel` (PowerShell 7+)**
```powershell
# Nécessite PowerShell 7+
$users = Import-Csv -Path "C:\Temp\Users.csv"
$users | ForEach-Object -Parallel {
    New-ADUser -Name "$($_.Prenom) $($_.Nom)" -SamAccountName $_.Identifiant
} -ThrottleLimit 5
```
**Explications** :
- **-Parallel** : Exécute les opérations en parallèle (plus rapide pour les grandes listes).
- **-ThrottleLimit** : Limite le nombre de threads simultanés.

---
#### **4.2.3. Utilisation de `Splatting` pour les paramètres**
```powershell
# Sans splatting
New-ADUser -Name "Jean Dupont" -SamAccountName "jdupont" -Enabled $true -Path "OU=Utilisateurs,DC=domaine,DC=local"

# Avec splatting
$userParams = @{
    Name             = "Jean Dupont"
    SamAccountName  = "jdupont"
    Enabled          = $true
    Path             = "OU=Utilisateurs,DC=domaine,DC=local"
}
New-ADUser @userParams
```
**Explications** :
- **`@userParams`** : Passe un dictionnaire de paramètres à la cmdlet.
- **Lisibilité** : Plus clair pour les appels avec beaucoup de paramètres.

---


### **4.3. Exercices d’approfondissement**
1. **Optimisation des performances** :
   - Comparez les temps d’exécution d’un script avec et sans pipeline/parallelisation.

2. **Intégration avec des API** :
   - Étendez un script pour interagir avec une **API REST** (ex: Microsoft Graph pour Office 365).

3. **Tests unitaires** :
   - Utilisez **Pester** pour écrire des tests unitaires pour vos fonctions.
   ```powershell
   Install-Module -Name Pester -Force
   Describe "Test de la fonction Create-ADUser" {
       It "Doit créer un utilisateur avec succès" {
           $user = @{ Prenom = "Test"; Nom = "User"; Identifiant = "tuser" }
           $result = Create-ADUser -UserData $user -ErrorAction SilentlyContinue
           $result | Should -Not -BeNullOrEmpty
       }
   }
   ```

4. **Déploiement continu** :
   - Automatisez le déploiement de vos scripts avec **Azure DevOps** ou **GitHub Actions**.

---
