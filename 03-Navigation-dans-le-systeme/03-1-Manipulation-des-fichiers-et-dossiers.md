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