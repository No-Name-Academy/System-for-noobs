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