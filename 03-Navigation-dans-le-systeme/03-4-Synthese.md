## **4. Synthèse et bonnes pratiques**

### **4.1. Résumé des notions clés**
- **Fichiers et dossiers** : Utiliser `Get-ChildItem`, `New-Item`, `Remove-Item`, `Copy-Item`, et `Move-Item` pour manipuler les fichiers.
- **Processus et services** : Utiliser `Get-Process`, `Stop-Process`, `Get-Service`, et `Restart-Service` pour gérer les ressources système.
- **Scripts** : Automatiser les tâches répétitives (nettoyage, archivage, sauvegarde).

### **4.2. Bonnes pratiques**
- **Tester les scripts** avec `-WhatIf` avant de les exécuter.
- **Journaliser les actions** pour faciliter le débogage.
- **Gérer les erreurs** avec `-ErrorAction` et `Try/Catch`.
- **Documenter les scripts** avec des commentaires et des blocs d’aide.

### **4.3. Exercices d’approfondissement**
1. **Script de surveillance des disques** :
   Écrire un script qui alerte si l’espace disque libre est inférieur à 10%.

2. **Script de gestion des utilisateurs** :
   Lister les utilisateurs locaux et exporter la liste dans un CSV.

3. **Script de déploiement** :
   Copier un ensemble de fichiers vers plusieurs serveurs distants (utiliser `Invoke-Command` pour la gestion à distance).
