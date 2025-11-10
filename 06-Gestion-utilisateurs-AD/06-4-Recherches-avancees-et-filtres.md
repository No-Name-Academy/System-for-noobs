## **4. Recherches avancées et filtres**

### **4.1. Utiliser des filtres LDAP**
**Exemple** :
```powershell
# Trouver les utilisateurs dont le prénom commence par "A"
Get-ADUser -LDAPFilter "(givenName=A*)" -Properties Name, EmailAddress

# Trouver les comptes désactivés
Get-ADUser -Filter {Enabled -eq $false} -Properties Name
```

---

### **4.2. Exporter les utilisateurs AD vers un CSV**
```powershell
Get-ADUser -Filter * -Properties Name, EmailAddress, Enabled | Select-Object Name, EmailAddress, Enabled | Export-Csv -Path "C:\Temp\utilisateurs_ad.csv" -NoTypeInformation
```

---

### **4.3. Travaux pratiques : Audit des comptes inactifs**
**Objectif** :
Lister les utilisateurs qui ne se sont pas connectés depuis 90 jours.

#### **Script : Audit des comptes inactifs**
```powershell
$daysInactive = 90
$inactiveDate = (Get-Date).AddDays(-$daysInactive)
Get-ADUser -Filter {LastLogonDate -lt $inactiveDate -and Enabled -eq $true} -Properties Name, LastLogonDate | Select-Object Name, @{Name="LastLogonDate"; Expression={$_.LastLogonDate}} | Export-Csv -Path "C:\Temp\comptes_inactifs.csv" -NoTypeInformation
Write-Host "Audit terminé. Résultats dans C:\Temp\comptes_inactifs.csv"
```

---