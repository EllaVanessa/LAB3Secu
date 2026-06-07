# Rapport de Lab — Proxy d'observation Android / Burp Suite

## Périmètre

- **Environnement** : Émulateur Android (Android Studio / AVD) sur machine hôte Windows
- **Cible** : Application/site de test autorisé (cible de formation)
- **Outil** : Burp Suite Community Edition
- **Objectif** : Observer le trafic HTTP transitant entre l'émulateur et la cible via un proxy d'interception

---

## Configuration

| Paramètre        | Valeur              |
|------------------|---------------------|
| Burp version     | Community Edition   |
| IP hôte (proxy)  | 192.168.146.1       |
| Port proxy       | 8080                |
| Interface réseau | VMware VMnet8       |
| Date             | 07/06/2025          |

---

## Étapes réalisées

### Étape 1 — Préparation de Burp Suite
- Lancement de Burp Suite Community
- Création d'un projet temporaire de labo
- Vérification de l'onglet **Proxy** → état initial : **Intercept is off**

### Étape 2 — Vérification du Proxy Listener
- Accès à **Proxy settings > Proxy listeners**
- Listener actif (Enabled) sur `0.0.0.0:8080` (All interfaces)
- Port noté : **8080**

### Étape 3 — Identification de l'adresse IP hôte
- Commande utilisée : `ipconfig` dans l'invite de commandes Windows
- Interface retenue : **VMware Network Adapter VMnet8**
- Adresse IPv4 : **192.168.146.1**
- Raison : l'émulateur Android Studio partage ce réseau VMware avec la machine hôte

### Étape 4 — Configuration du proxy côté Android Emulator
- Paramètres Wi-Fi de l'émulateur → réseau actif → **options avancées**
- Proxy : **Manuel**
- Proxy hostname : `192.168.146.1`
- Proxy port : `8080`
- Paramètre enregistré

### Étape 5 — Premier test de capture HTTP
- Navigateur de l'émulateur ouvert
- Accès à une cible autorisée (site de test)
- Vérification dans Burp → **HTTP history** → requêtes visibles ✅
- Méthode, URL, statut et taille confirmés

### Étape 6 — Lecture d'une requête (mode analyste)
- Sélection d'une requête dans HTTP history
- Onglet **Raw** : méthode GET/POST, chemin, en-têtes (User-Agent, Accept, Cookie)
- Onglet **Inspector** : query parameters, cookies, headers structurés
- Observation : paramètres en clair, cookies de session présents

### Étape 7 — Interception contrôlée (mode pédagogique)
- Intercept activé brièvement
- Rafraîchissement d'une page autorisée dans l'émulateur
- Requête observée "en attente" dans Burp
- Intercept désactivé immédiatement après observation
- Différence constatée : mode **passif** (history) vs mode **actif** (intercept)

### Étape 8 — HTTPS et certificat CA (principe)
- Observation de l'écran "Install a certificate" dans l'émulateur
- Types identifiés : CA certificate, VPN & app user certificate, Wi-Fi certificate
- Compréhension : le navigateur refuse le proxy HTTPS sans certificat CA de confiance
- Note : certificat non installé dans ce lab (observation théorique uniquement)

---

## Preuves (à compléter)

| # | Description | Capture |
|---|-------------|---------|
| 1 | HTTP history Burp — liste de requêtes capturées | *(screenshot à insérer)* |
| 2 | Détail d'une requête — onglet Raw (headers + URL) | *(screenshot à insérer)* |
| 3 | Configuration proxy Android (IP + port) | *(screenshot à insérer)* |

---

## Analyse

### Données observées en transit
- Méthodes HTTP : GET (navigation classique)
- En-têtes exposés : `User-Agent`, `Accept-Language`, `Accept-Encoding`
- Cookies de session potentiellement visibles en clair (HTTP non chiffré)
- Paramètres d'URL visibles dans le chemin de la requête

### Risques identifiés
- **Tokens ou identifiants en URL** : si présents, ils apparaissent en clair dans les logs proxy
- **Absence d'en-têtes de sécurité côté client** : pas de `Strict-Transport-Security` observable sur HTTP pur
- **Cookies sans attribut `Secure`** : transmis en HTTP, donc interceptables

---

## Recommandations défensives

1. **Minimiser les données exposées** : éviter de passer des tokens ou IDs sensibles dans l'URL
2. **Sécuriser les cookies côté serveur** : attributs `Secure`, `HttpOnly`, `SameSite`
3. **Forcer HTTPS** : tout trafic applicatif doit passer en HTTPS pour résister à l'interception proxy
4. **Bonnes pratiques Android** : utiliser le Network Security Config pour restreindre les CA de confiance

---

## Checkpoints de validation

- [x] Burp capture au moins une requête dans HTTP history
- [x] Proxy listener actif et documenté (192.168.146.1:8080)
- [x] Proxy Android configuré en mode Manuel
- [x] Intercept utilisé seulement pour démonstration, puis désactivé
- [ ] Rapport court produit (preuve + contexte) ← *captures à ajouter*
- [ ] Nettoyage de fin de séance effectué

---

## Nettoyage (fin de lab)

- [ ] Proxy Android remis sur "None" / "Proxy off"
- [ ] Certificat de labo retiré (si installé)
- [ ] Projet Burp fermé / seules les preuves nécessaires archivées

---
