## **4. Synthèse et bonnes pratiques**

### **4.1. Résumé des notions clés**
- **Journaux d’événements** : Utilisez `Get-WinEvent` pour une analyse avancée.
- **Surveillance des ressources** : `Get-Counter` et `Get-Volume` sont essentiels.
- **Alertes** : Journalisez et notifiez les problèmes critiques (email, logs).
- **Automatisation** : Planifiez les scripts pour une surveillance continue.

### **4.2. Bonnes pratiques**
- **Testez les scripts** dans un environnement non productif avant déploiement.
- **Limitez les alertes** pour éviter la surcharge (ex: 1 email/h par type d’alerte).
- **Centralisez les logs** : Utilisez un serveur dédié pour stocker les logs de tous les serveurs.
- **Sécurisez les emails** : Utilisez des comptes dédiés et des canaux chiffrés (TLS) pour l’envoi d’alertes.

### **4.3. Exercices d’approfondissement**
1. **Script de surveillance multi-serveurs** :
   Utiliser `Invoke-Command` pour surveiller plusieurs serveurs à distance.
   ```powershell
   $servers = @("Server1", "Server2", "Server3")
   Invoke-Command -ComputerName $servers -ScriptBlock { Get-Counter '\Processor(_Total)\% Processor Time' }
   ```

2. **Intégration avec un SIEM** :
   Exporter les événements critiques vers un système de gestion des informations et événements de sécurité (SIEM).

3. **Tableau de bord PowerShell** :
   Créer un script qui affiche un **tableau de bord** en temps réel avec les métriques système (utiliser `Out-GridView` ou une interface graphique basique).

---