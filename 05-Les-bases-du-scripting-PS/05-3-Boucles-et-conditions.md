## **3. Boucles et conditions**

### **3.1. Boucle `foreach`**
La boucle `foreach` permet d’itérer sur une collection d’objets.

**Syntaxe** :
```powershell
foreach ($item in $collection) {
    # Traiter $item
}
```

**Exemple** :
```powershell
$fichiers = Get-ChildItem -Path "C:\Temp" -File
foreach ($fichier in $fichiers) {
    Write-Host "Fichier : $($fichier.Name) - Taille : $($fichier.Length) octets"
}
```

---

### **3.2. Boucle `ForEach-Object`**
`ForEach-Object` est utilisé dans le pipeline pour traiter chaque objet.

**Exemple** :
```powershell
Get-ChildItem -Path "C:\Temp" -File | ForEach-Object {
    Write-Host "Fichier : $($_.Name)"
}
```

---

### **3.3. Conditions (`if`, `elseif`, `else`)**
**Syntaxe** :
```powershell
if (condition) {
    # Code si condition vraie
}
elseif (autre_condition) {
    # Code si autre_condition vraie
}
else {
    # Code si aucune condition vraie
}
```

**Exemple** :
```powershell
$age = 25
if ($age -lt 18) {
    Write-Host "Mineur"
}
elseif ($age -ge 18 -and $age -lt 65) {
    Write-Host "Adulte"
}
else {
    Write-Host "Senior"
}
```

**Opérateurs de comparaison** :
| Opérateur | Description               |
|-----------|---------------------------|
| `-eq`     | Égal à                    |
| `-ne`     | Différent de              |
| `-gt`     | Supérieur à               |
| `-lt`     | Inférieur à               |
| `-ge`     | Supérieur ou égal à       |
| `-le`     | Inférieur ou égal à       |

---

### **3.4. Boucle `while`**
La boucle `while` exécute un bloc tant qu’une condition est vraie.

**Exemple** :
```powershell
$compte = 0
while ($compte -lt 5) {
    Write-Host "Compte : $compte"
    $compte++
}
```

---

### **3.5. Travaux pratiques : Script avec boucles et conditions**
**Exercice** :
Écrire un script qui liste les fichiers d’un dossier et affiche un message si la taille dépasse 1 Mo.
```powershell
$dossier = "C:\Temp"
Get-ChildItem -Path $dossier -File | ForEach-Object {
    if ($_.Length -gt 1MB) {
        Write-Host "Fichier volumineux : $($_.Name) - Taille : $($_.Length / 1MB) Mo" -ForegroundColor Red
    }
    else {
        Write-Host "Fichier : $($_.Name) - Taille : $($_.Length) octets"
    }
}
```

---