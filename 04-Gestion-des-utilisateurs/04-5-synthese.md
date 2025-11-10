## **5. Synthèse et bonnes pratiques**

### **5.1. Résumé des notions clés**
- **Utilisateurs locaux** : Utiliser `New-LocalUser`, `Set-LocalUser`, et `Remove-LocalUser`.
- **Groupes locaux** : Utiliser `New-LocalGroup`, `Add-LocalGroupMember`, et `Remove-LocalGroupMember`.
- **Permissions** : Utiliser `Get-Acl` et `Set-Acl` pour gérer les accès.

### **5.2. Bonnes pratiques**
- **Sécuriser les mots de passe** : Utiliser `ConvertTo-SecureString` et éviter de stocker les mots de passe en clair.
- **Journaliser les actions** : Enregistrer les modifications apportées aux utilisateurs et groupes.
- **Tester les scripts** : Utiliser `-WhatIf` pour valider les actions avant de les exécuter.

### **5.3. Exercices d’approfondissement**
1. **Script d’audit des utilisateurs locaux** :
   Exporter la liste des utilisateurs locaux et leurs groupes dans un fichier CSV.

2. **Script de réinitialisation des mots de passe** :
   Réinitialiser les mots de passe des utilisateurs inactifs depuis plus de 30 jours.

3. **Script de création de groupes standardisés** :
   Créer des groupes locaux (ex: "Dev", "Ops", "ReadOnly") et y ajouter des utilisateurs en fonction de leur rôle.

---