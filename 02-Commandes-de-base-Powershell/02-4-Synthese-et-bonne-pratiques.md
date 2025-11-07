## **4. Synthèse et bonnes pratiques**

### **4.1. Résumé des notions clés**
- Les cmdlets suivent une structure **Verbe-Nom** et utilisent des **paramètres** pour préciser leur comportement.
- Les **alias** permettent de gagner du temps pour les commandes courantes.
- Les **modules** étendent les fonctionnalités de PowerShell (intégrés ou tiers).
- PowerShell permet d’automatiser des tâches d’administration système (processus, services, fichiers, logs).

### **4.2. Bonnes pratiques**
- **Toujours tester les commandes** avec `-WhatIf` avant de les exécuter.
- **Documenter les scripts** avec des commentaires et des blocs d’aide.
- **Utiliser les modules** pour accéder à des fonctionnalités avancées (Active Directory, Hyper-V, Azure, etc.).
- **Exporter les résultats** pour les analyser ultérieurement (CSV, TXT).

### **4.3. Exercices d’approfondissement**
1. **Script d’inventaire logiciel** :
   Lister tous les logiciels installés sur un serveur et exporter la liste dans un fichier CSV.
   ```powershell
   Get-WmiObject -Class Win32_Product | Select-Object Name, Version | Export-Csv -Path "C:\Temp\LogicielsInstalles.csv" -NoTypeInformation
   ```

2. **Surveillance des services critiques** :
   Créer un script qui vérifie l’état des services critiques (ex: `DHCP`, `DNS`) et envoie une alerte si l’un d’eux est arrêté.

3. **Planification de tâches** :
   Utiliser `Task Scheduler` pour exécuter un script PowerShell quotidiennement (ex: nettoyage des logs).

---