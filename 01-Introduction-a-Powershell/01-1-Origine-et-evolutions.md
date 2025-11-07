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