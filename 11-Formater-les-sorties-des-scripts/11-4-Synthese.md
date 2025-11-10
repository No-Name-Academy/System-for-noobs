## **4. Synthèse et bonnes pratiques**

### **4.1. Résumé des formats et outils**
| Format          | Cmdlet               | Utilisation typique                          |
|-----------------|----------------------|-----------------------------------------------|
| Tableau         | `Format-Table`       | Comparaison rapide de plusieurs objets.      |
| Liste           | `Format-List`        | Détails complets d’un objet.                  |
| CSV             | `Export-Csv`         | Analyse ultérieure (Excel, Power BI).        |
| HTML            | `ConvertTo-Html`     | Rapports visuels pour partage.               |
| Texte           | `Out-File`           | Logs ou sorties brutes.                       |
| Couleurs        | `Write-Host`         | Mise en évidence des alertes.                 |
| Barre de progression | `Write-Progress` | Feedback visuel pour les tâches longues. |

### **4.2. Bonnes pratiques**
- **Choisissez le bon format** :
  - **Tableau** pour les comparaisons.
  - **Liste** pour les détails.
  - **CSV/HTML** pour les rapports.
- **Personnalisez les colonnes** :
  - Utilisez `@{ Name = "..."; Expression = { ... } }` pour calculer des valeurs.
- **Ajoutez des couleurs** :
  - Rouge pour les erreurs, vert pour les succès.
- **Documentation** :
  - Ajoutez des en-têtes et des titres clairs.
- **Automatisez** :
  - Planifiez la génération de rapports (ex: quotidiennement).

### **4.3. Exercices d’approfondissement**
1. **Rapport des utilisateurs AD** :
   - Exporter la liste des utilisateurs AD avec leurs groupes et dernière date de connexion.
   - Utiliser `Get-ADUser` et `Get-ADGroupMember`.

2. **Suivi des performances** :
   - Créer un script qui enregistre l’utilisation CPU/mémoire toutes les heures dans un CSV.
   - Générer un graphique avec Excel ou Power BI.

3. **Notification par email** :
   - Étendre le script pour envoyer le rapport HTML par email (`Send-MailMessage`).

---