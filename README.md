# Rapport de Lab — Proxy d'observation Android / Burp Suite

## Périmètre

- **Environnement** : Émulateur Android (Android Studio / AVD) sur machine hôte Windows
- **Cible** : Site de test autorisé (cible de formation)
- **Outil** : Burp Suite Community Edition
- **Date** : 07/06/2025

---

## Configuration

| Paramètre | Valeur |
|---|---|
| IP hôte (proxy) | 192.168.146.1 |
| Port proxy | 8080 |
| Interface réseau | VMware VMnet8 |
| Listener Burp | All interfaces — Enabled |
| Proxy Android | Manuel — 192.168.146.1:8080 |

---

## Résultats observés

- Le trafic HTTP de l'émulateur transite bien par Burp (requêtes visibles dans HTTP history).
- Chaque requête expose : méthode (GET/POST), URL complète, en-têtes (User-Agent, Accept, Cookie).
- Les cookies de session apparaissent en clair sur HTTP non chiffré.
- Les paramètres d'URL sont lisibles sans déchiffrement.
- L'intercept a été activé brièvement : la requête se met en attente côté Burp, confirmant le rôle de point de passage du proxy.
- Sur HTTPS, le navigateur refuse le proxy sans certificat CA de confiance installé.

---

## Analyse des risques

- Tokens ou identifiants passés en URL → visibles en clair dans les logs proxy.
- Cookies sans attribut `Secure` → interceptables sur HTTP.
- Absence d'en-têtes de sécurité côté client (ex. `Strict-Transport-Security`) sur trafic HTTP pur.

---

## Recommandations

1. Forcer HTTPS sur toutes les communications applicatives.
2. Ajouter les attributs `Secure`, `HttpOnly`, `SameSite` sur les cookies.
3. Ne jamais passer de tokens sensibles dans l'URL.
4. Utiliser le Network Security Config Android pour restreindre les CA de confiance.

---

## Nettoyage effectué

- Proxy Android remis sur "None".
- Intercept désactivé.
- Projet Burp fermé.

---

*Lab de formation — environnement isolé — cible autorisée uniquement.*
