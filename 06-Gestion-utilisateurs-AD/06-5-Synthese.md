
## **5. Synthèse et bonnes pratiques**

### **5.1. Résumé des cmdlets clés**
| Action                     | Cmdlet                          |
|----------------------------|---------------------------------|
| Lister les utilisateurs    | `Get-ADUser`                    |
| Créer un utilisateur       | `New-ADUser`                    |
| Modifier un utilisateur    | `Set-ADUser`                    |
| Supprimer un utilisateur   | `Remove-ADUser`                 |
| Lister les groupes         | `Get-ADGroup`                   |
| Créer un groupe            | `New-ADGroup`                   |
| Ajouter à un groupe        | `Add-ADGroupMember`             |
| Retirer d’un groupe        | `Remove-ADGroupMember`          |

### **5.2. Bonnes pratiques**
- **Testez toujours vos scripts** avec `-WhatIf` si possible.
- **Journalisez les actions** pour auditer les modifications.
- **Utilisez des OU dédiées** pour organiser les utilisateurs et groupes.
- **Sécurisez les mots de passe** : Ne stockez jamais les mots de passe en clair dans les scripts.

**Exemple de journalisation** :
```powershell
$logFile = "C:\Temp\ad_operations.log"
Add-Content -Path $logFile -Value "$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss') - Création de l'utilisateur $identifiant"
```

---

### **5.3. Exercices d’approfondissement**
1. **Script de réinitialisation des mots de passe** :
   Réinitialiser les mots de passe des utilisateurs inactifs depuis 30 jours.

2. **Script de création d’OU** :
   Créer une structure d’OU pour un nouveau département et y ajouter des utilisateurs.

3. **Script de synchronisation** :
   Synchroniser les groupes AD avec une liste externe (ex: fichier CSV).

---