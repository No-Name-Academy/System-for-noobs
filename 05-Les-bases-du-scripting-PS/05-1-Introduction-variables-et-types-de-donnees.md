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