## **2. Interface utilisateur : PowerShell ISE**

### **2.1. Présentation de PowerShell ISE**
**PowerShell ISE (Integrated Scripting Environment)** est un environnement graphique dédié à l’écriture, au test et au débogage de scripts PowerShell. Il est particulièrement adapté aux débutants grâce à ses fonctionnalités intégrées :
- **Éditeur de script** : Colorisation syntaxique, complétion automatique (IntelliSense).
- **Console interactive** : Exécution directe des commandes.
- **Débogueur** : Points d’arrêt, exécution pas à pas.

#### **2.1.1. Lancement de PowerShell ISE**
- Via le menu Démarrer : `Windows PowerShell > Windows PowerShell ISE`.
- Via la ligne de commande :
  ```powershell
  powershell_ise
  ```

#### **2.1.2. Fonctionnalités clés**
| Fonctionnalité          | Description                                                                 |
|-------------------------|-----------------------------------------------------------------------------|
| **Onglets**             | Permet d’ouvrir plusieurs scripts en parallèle.                           |
| **Exécution partielle** | Sélectionner une partie du script et appuyer sur **F8** pour l’exécuter.   |
| **IntelliSense**        | Complétion automatique des cmdlets et de leurs paramètres (tabulation).  |
| **Débogueur**           | Points d’arrêt, exécution pas à pas (F9 pour ajouter un point d’arrêt).     |

---

### **2.2. Mise en pratique : Écriture et exécution d’un script**

#### **Exercice 3 : Création d’un script simple**
1. Ouvrir **PowerShell ISE**.
2. Créer un nouveau fichier (`Fichier > Nouveau`) et copier le script suivant :
   ```powershell
   <#
   .SYNOPSIS
       Script pour lister et démarrer les services arrêtés.

   .DESCRIPTION
       Ce script identifie les services arrêtés et les démarre un par un.
   #>

   # Récupérer la liste des services arrêtés
   $stoppedServices = Get-Service | Where-Object { $_.Status -eq "Stopped" }

   # Démarrer chaque service et afficher un message
   foreach ($service in $stoppedServices) {
       Write-Host "Démarrage du service : $($service.DisplayName)"
       Start-Service -Name $service.Name
   }

   # Afficher un résumé
   Write-Host "`n$($stoppedServices.Count) service(s) démarré(s)." -ForegroundColor Green
   ```
3. Enregistrer le script sous `C:\Scripts\DemarrerServicesArrêtes.ps1`.
4. Exécuter le script en appuyant sur **F5** ou via le bouton **Exécuter**.

#### **Exercice 4 : Utilisation des points d’arrêt pour le débogage**
1. Ajouter un point d’arrêt à la ligne `Start-Service` (clic gauche dans la marge ou **F9**).
2. Lancer le script en mode débogage (**F5**).
3. Utiliser **F10** pour exécuter le script pas à pas et observer les valeurs des variables.

---

### **2.3. Bonnes pratiques pour l’utilisation de l’ISE**
- **Commentaires** : Utiliser `#` pour les commentaires sur une ligne et `<# ... #>` pour les blocs de commentaires.
- **Indentation** : Structurer le code pour une meilleure lisibilité.
- **Sauvegarde** : Enregistrer régulièrement les scripts (`Ctrl + S`).
- **Tests** : Exécuter des portions de code avant de lancer le script entier.

---