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