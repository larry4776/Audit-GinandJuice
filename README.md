#  Audit de Sécurité Applicative — ginandjuice.shop

**Auditeur :** DEGBOE Larry    
**Date :** 26 Juin 2026  
**Outil utilisé :** Burp Suite Community Edition v2026.6  
**Cible du test :** `https://ginandjuice.shop` (Environnement de test PortSwigger)

---

## Présentation de l'Audit
Cet audit technique présente la cartographie et l'évaluation de la posture de sécurité de l'application web cible. L'objectif est d'analyser la gestion des flux, la résistance aux injections courantes, la sécurité des sessions et la configuration des en-têtes serveurs.

---

##  Synthèse des Tests Effectués

### Test 1 — Cartographie du Trafic HTTP
Interception et analyse globale des flux pour identifier la surface d'attaque et recenser l'ensemble des points d'entrée (endpoints) applicatifs.
<img width="1350" height="237" alt="cartographie png" src="https://github.com/user-attachments/assets/c65e90fd-5038-481c-88c4-c930df72252c" />
##Cartographie

---

### Test 2 — Injection SQL (SQLi)
Tentative de perturbation des requêtes SQL de la base de données via un payload classique. Le serveur bloque la requête ou la filtre correctement, validant une bonne résistance face aux injections SQL.
<img width="1017" height="449" alt="sqli png" src="https://github.com/user-attachments/assets/3904134b-7e37-47e5-8757-92bdfdd00b16" />
##Injection SQL

---

### Test 3 — Injection Cross-Site Scripting (XSS / Fichiers HTML-CSS)
Vérification de la désinfection des entrées utilisateurs lors de l'affichage de contenu HTML/CSS dynamique. L'application neutralise les caractères spéciaux pour empêcher l'exécution de scripts arbitraires dans le contexte du navigateur.

<img width="890" height="583" alt="XSS png" src="https://github.com/user-attachments/assets/b4ff4688-99dd-48a5-8f76-878677a2163a" />
##Test XSS HTML CSS

---


### Test 4 — Analyse de la Sécurité des Cookies
Inspection du cookie de session applicatif. Les directives de sécurité essentielles sont correctement implémentées, réduisant les risques de vol ou de détournement de session.
* **Attributs validés :** `Secure` (transmission chiffrée uniquement) et `SameSite` (protection contre le rebond).
<img width="495" height="83" alt="cookie png" src="https://github.com/user-attachments/assets/0f45cbc2-1fa2-4b8a-a9d9-c7e78f647cb5" />

##Sécurité des Cookies

---

### Test 5 — Protection contre les Attaques CSRF (Cross-Site Request Forgery)
Validation de la présence d'un jeton d'authenticité unique (`CSRF Token`) au sein des requêtes sensibles. Ce mécanisme garantit que les actions critiques ne peuvent pas être rejouées à l'insu d'un utilisateur authentifié depuis un site tiers malveillant.
<img width="436" height="30" alt="csrf png" src="https://github.com/user-attachments/assets/83e1f642-ab25-44cc-94af-c76c0155df07" />
##Jeton CSRF

---

### Test 6 — Configuration Serveur & En-tête Backend (Information Disclosure)
Analyse des en-têtes (headers) HTTP retournés par le serveur. Présence d'un en-tête personnalisé non standard révélant un identifiant interne de l'infrastructure.
* **Indicateur exposé :** `X-Backend: a92d1d41-a49b-4717-909f-c247b57a2f5b`
<img width="290" height="167" alt="Backend png" src="https://github.com/user-attachments/assets/e1e6294a-300c-45c8-a1ed-cdabf1334813" />

##Exposition X-Backend

---

## Conclusion & Grille de Conformité

| Vulnérabilité / Mécanisme testé | État / Résultat | Impact Constaté |
| :--- | :---: | :--- |
| **Cartographie & Flux** |  Validé | Visibilité complète de la structure applicative |
| **Injections SQL (SQLi)** | Conforme | Filtrage efficace des requêtes en base de données |
| **Contrôle HTML / CSS (XSS)** | Conforme | Échappement et encodage correct des entrées |
| **Sécurité des Sessions (Cookies)** |  Conforme | Flags `Secure` et `SameSite` correctement activés |
| **Protection CSRF (Tokens)** |  Conforme | Validation stricte de l'origine des requêtes |
| **Configuration Serveur (Headers)** |  Non Conforme | Fuite de données d'infrastructure via `X-Backend` |

### Recommandation Prioritaire
* **Anomalie :** Exposition de l'architecture interne via le header `X-Backend`.
* **Action Corrective :** Modifier la configuration du serveur ou du proxy inverse (Reverse Proxy) en production pour **supprimer l'en-tête `X-Backend`** des réponses publiques afin d'empêcher toute reconnaissance passive par un attaquant.

