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
