# 🛡️ Red Team / Active Directory CheatsheetGuide opérationnel  pour missions red-team : checklists

![Description de l'image](./images/5654334534-588895003.jpg)

Un guide pratique pour les opérations Red Team dans des environnements Active Directory.  
Accessible aux débutants, utile comme checklist, référence rapide pour chaque étape d'une intrusion simulée.


# 📌 Définitions simples en Active Directory (AD)

## 1. Domaine
**Définition :** Un domaine est un ensemble d’ordinateurs, d’utilisateurs et de ressources qui partagent un même espace de noms et des règles de sécurité communes.  
**Acronyme :** Aucun spécifique, mais souvent appelé *domain*.  
**Exemple :** `entreprise.local` → tous les utilisateurs et machines de l’entreprise y sont enregistrés.

---

## 2. Forêt (Forest)
**Définition :** Une forêt est l’ensemble de tous les domaines reliés entre eux dans Active Directory. Elle constitue le plus grand conteneur logique.  
**Exemple :** Une entreprise peut avoir plusieurs domaines : `entreprise.local` et `paris.entreprise.local` regroupés dans la même forêt.

---

## 3. Arbre (Tree)
**Définition :** Un arbre est un groupe de domaines partageant le même espace de noms hiérarchique.  
**Exemple :** `entreprise.local` → `paris.entreprise.local` → `it.paris.entreprise.local`.

---

## 4. Unité d’Organisation (OU)
**Définition :** Une OU est un dossier logique qui permet d’organiser utilisateurs, groupes et ordinateurs dans un domaine.  
**Acronyme :** OU = *Organizational Unit*  
**Exemple :**  
- `OU=RH, DC=entreprise, DC=local` → tous les utilisateurs du département RH  
- `OU=IT, DC=entreprise, DC=local` → tous les utilisateurs et serveurs du département IT

---

## 5. Contrôleur de domaine (DC)
**Définition :** Le serveur qui gère l’authentification et la sécurité des utilisateurs et des machines dans un domaine.  
**Acronyme :** DC = *Domain Controller*  
**Exemple :** Lorsqu’un utilisateur se connecte à son ordinateur, le DC vérifie son mot de passe et applique les droits associés.

---

## 6. Objet
**Définition :** Tout ce qui existe dans AD est un objet.  
**Exemples :**  
- Utilisateur (`User`)  
- Groupe (`Group`)  
- Ordinateur (`Computer`)  
- Imprimante (`Printer`)

---

## 7. Utilisateur
**Définition :** Un compte qui représente une personne, avec un identifiant (login) et un mot de passe.  
**Exemple :** `jdupont@entreprise.local`

---

## 8. Groupe
**Définition :** Ensemble d’utilisateurs, ordinateurs ou autres groupes. Sert à attribuer des droits à plusieurs comptes en même temps.  
**Exemple :** Groupe `IT_Admins` → tous les membres ont des droits administratifs sur les serveurs du département IT.

---

## 9. ACL (Access Control List)
**Définition :** Liste de règles qui définit qui peut faire quoi sur un objet dans AD (fichier, dossier, utilisateur, groupe, etc.).  
**Acronyme :** ACL = *Access Control List*, chaque règle = ACE (*Access Control Entry*).  
**Exemple :**  
- Utilisateur A → peut lire un fichier  
- Utilisateur B → peut modifier le fichier  
- Utilisateur C → aucun accès

---

## 💡 Astuce visuelle pour mémoriser
```text
Forêt
 └─ Arbre
     └─ Domaine
         └─ OU
             ├─ Utilisateurs
             └─ Groupes



#  Schéma mécanique : Flux typique d’une intrusion Red Team

Voici un **processus étape-par-étape** montrant comment une attaque Red Team se déroule généralement dans un environnement Active Directory.  
Chaque étape indique l’objectif, les techniques utilisées, les outils courants, et le résultat attendu.

---

## 1) Reconnaissance (Recon)

**Objectif :** Identifier les hôtes, comptes et services exposés.  
**Techniques / Outils :**  
- Scan réseau → `Nmap`, `CrackMapExec`  
- Enumeration LDAP → `ldapsearch`, BloodHound intake  
- SMB / HTTP enumeration  

**Résultat attendu :**  
- Liste de machines, utilisateurs, SPN (Service Principal Names), CAs, et services exposés.

** Stratégique :** Cette étape est passive ou semi-passive pour collecter un maximum d’informations sans se faire détecter.

---

## 2) Collecte d’utilisateurs / comptes

**Objectif :** Obtenir les comptes utilisateurs à cibler pour les étapes suivantes.  
**Techniques / Outils :**  
- LDAP enumeration  
- `enum4linux`  
- CrackMapExec `users`  
- BloodHound ingestion  

**Lien stratégique :** Fournit la cible pour **Password Spraying** ou **Kerberoast**.

---

## 3) Password Spraying / Credential Stuffing

**Objectif :** Compromettre des comptes avec des mots de passe faibles ou par réutilisation sans déclencher de lockout.  
**Techniques / Outils :**  
- Password spraying → `Hydra`, `CrackMapExec`, custom scripts  
- Credential stuffing ciblé sur les comptes collectés  

**Lien stratégique :** Si succès → accès initial sur une machine utilisateur.

---

## 4) LLMNR / NBT-NS / WPAD poisoning (initial)

**Objectif :** Capturer des hashes NTLM ou provoquer des connexions qui exposent des credentials.  
**Techniques / Outils :**  
- `Responder`  
- `Inveigh`  

**Lien stratégique :** Souvent réalisé depuis le réseau interne après compromis d’une machine utilisateur, fournit des hashes pour **Pass-the-Hash** ou **NTLM relay**.

---

## 5) Credential Dumping (post-compromise)

**Objectif :** Récupérer mots de passe, hashes, tickets en mémoire (LSASS) ou fichiers (NTDS.dit).  
**Techniques / Outils :**  
- `Mimikatz`  
- Impacket `secretsdump.py`  
- Snapshot tools  

**Lien stratégique :** Ces credentials permettent PtH, PtT, DCSync et escalade.

---

## 6) Pass-the-Hash / Over-Pass-the-Hash / Pass-the-Ticket

**Objectif :** Réutiliser les creds/hashes/tickets pour se déplacer latéralement sans mot de passe clair.  
**Techniques / Outils :**  
- Impacket tools  
- CrackMapExec  
- Rubeus, Mimikatz  

**Lien stratégique :** Permet accès à d’autres hôtes et escalade vers des serveurs sensibles.

---

## 7) Kerberos attacks (Kerberoast, AS-REP Roasting, SPN scanning, Golden/Silver Tickets)

**Objectif :** Extraire des secrets exploitables via Kerberos pour récupérer des passwords ou forger des tickets.  
**Techniques / Outils :**  
- Kerberoasting → `Get-TGS`, export TGS, crack offline  
- AS-REP Roasting → comptes sans pre-auth  
- Rubeus, Kerberoast scripts  

**Lien stratégique :** Succès → mots de passe de services ou possibilité de forger **Silver/Golden Tickets**, accès étendu.

---

## 8) Escalade de privilèges locale / via vulnérabilités

**Objectif :** Obtenir droits SYSTEM / admin sur une machine ou un DC.  
**Techniques / Outils :**  
- Exploitation vulnérabilités locales / serveur (ex. PrintNightmare)  
- Abuse de services mal configurés / vulnérabilités non patchées  

**Lien stratégique :** Escalade souvent nécessaire pour DCSync, dump NTDS, ou persistance profonde.

---

## 9) DCSync / DC compromise / NTDS extraction

**Objectif :** Récupérer hachages de tous les comptes (escalade domain-wide).  
**Techniques / Outils :**  
- DCSync via comptes avec `Replicating Directory Changes` rights  
- Extraire NTDS.dit  

**Lien stratégique :** Accès quasi-total au domaine si réussi.

---

## 10) Persistence et abus d’ACL / AD CS / Delegation

**Objectif :** Maintenir l’accès en créant des comptes, modifiant ACL, ou abusant des certificats.  
**Techniques / Outils :**  
- Modification DACL  
- Création de service accounts  
- AD CS abuse (certificats)  
- DCShadow  

**Lien stratégique :** Rend difficile la suppression de l’accès par les défenseurs.

---

## 11) Lateral movement & pivoting

**Objectif :** Atteindre systèmes cibles (DCs, serveurs critiques).  
**Techniques / Outils :**  
- PsExec, WinRM/Evil-WinRM, RDP, WMI  
- Combinaison avec PtH / PtT  

**Lien stratégique :** Déplacement latéral sécurisé sans mot de passe clair.

---

## 12) Domain dominance (Golden / Silver Tickets)

**Objectif :** Contrôle prolongé du domaine pour accès indétectable et durable.  
**Techniques :**  
- Golden Ticket → forge TGT avec krbtgt hash  
- Silver Ticket → accès service ciblé  

**Lien stratégique :** Persistance complète et indétectable si bien configuré.

---

## 13) Cleanup / exfiltration

**Objectif :** Exfiltrer données et couvrir traces.  
**Techniques / Outils :**  
- Exfiltration via tunnels, proxy, SOCKS  
- Suppression des logs  
- Extraction de données métiers (NTDS, fichiers sensibles)  

**Lien stratégique :** Finaliser la mission et réduire les chances d’être détecté.

---

### 💡 Astuce pour les débutants :
- Chaque étape construit sur la précédente → comprendre le **flux logique** aide à anticiper les prochaines actions.  
- Toujours commencer par la **reconnaissance passive** pour éviter de déclencher les alertes.  
- Note les outils utilisés pour chaque étape → utile pour ton “checklist Red Team” sur GitHub.
