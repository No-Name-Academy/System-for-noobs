## **3. Synthèse et exercices d’approfondissement**

### **3.1. Résumé des notions clés**
- PowerShell est un outil d’automatisation basé sur des **cmdlets** et un **pipeline d’objets**.
- Les cmdlets suivent une nomenclature **Verbe-Nom** et retournent des objets exploitables.
- **PowerShell ISE** est l’environnement idéal pour débuter avec les scripts.

### **3.2. Exercices supplémentaires**
1. **Script d’inventaire système** :
   Créer un script qui génère un rapport des disques locaux (lettre, capacité, espace libre) et l’exporte dans un fichier CSV.
   ```powershell
   Get-Volume | Select-Object DriveLetter, Size, SizeRemaining |
   Export-Csv -Path "C:\Temp\InventaireDisques.csv" -NoTypeInformation
   ```

2. **Gestion des processus** :
   Écrire un script qui tue tous les processus notepad.exe ouverts.
   ```powershell
   Stop-Process -Name "notepad" -Force
   ```

3. **Journalisation** :
   Modifier le script de l’**Exercice 3** pour enregistrer les actions dans un fichier log (`C:\Temp\ServiceLog.txt`).

---
