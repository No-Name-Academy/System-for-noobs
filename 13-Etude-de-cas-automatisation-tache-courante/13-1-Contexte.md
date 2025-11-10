## **1. Contexte et objectifs du script**
### **1.1. Scénario**
**Problématique** :
Un administrateur système doit **automatiser la création de comptes utilisateurs**, **configurer leurs permissions**, et **surveiller les services critiques** sur un serveur Windows.
Le script doit :
- Créer des utilisateurs **Active Directory** depuis un fichier CSV.
- Ajouter ces utilisateurs à des **groupes spécifiques**.
- Configurer des **permissions sur des dossiers partagés**.
- **Surveiller les services critiques** et redémarrer ceux qui sont arrêtés.
- **Journaliser toutes les actions** pour un audit ultérieur.

---
### **1.2. Structure du script**
Le script sera **modulaire** avec les fonctions suivantes :
1. **`Import-UserData`** : Importer les données utilisateurs depuis un CSV.
2. **`Create-ADUsers`** : Créer les utilisateurs Active Directory.
3. **`Add-UsersToGroups`** : Ajouter les utilisateurs aux groupes AD.
4. **`Set-FolderPermissions`** : Configurer les permissions sur les dossiers.
5. **`Monitor-CriticalServices`** : Surveiller et redémarrer les services critiques.
6. **`Write-Log`** : Journaliser les actions et erreurs.

---
