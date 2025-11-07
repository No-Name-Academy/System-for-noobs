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