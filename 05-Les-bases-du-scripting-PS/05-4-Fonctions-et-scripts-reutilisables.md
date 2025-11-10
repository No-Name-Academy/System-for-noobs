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