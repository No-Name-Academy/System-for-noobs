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