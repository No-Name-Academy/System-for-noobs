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