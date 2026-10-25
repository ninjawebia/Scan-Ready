# Étude des solutions open source pour Scan&Ready

> Projet : Scan&Ready — clone de Dorakjo / Ayyatli
> Objectif : identifier les projets open source et briques réutilisables pour accélérer la conception.
> Date : 2026-10-26

---

## 1. Projets open source très proches du besoin

### 1.1. `fftp` (Open Protocol for Ordering Food)

**Le plus proche de Scan&Ready.** Permet aux restaurants de gérer des commandes à emporter.

- **QR code** généré pour que les clients commandent depuis leur navigateur (sans app).
- **Notification sur le téléphone** du client (via `ntfy`) lorsque la commande est prête.
- **Tableau de bord** pour le personnel avec flux de commandes en temps réel.

**Lien** : [https://pypi.org/project/fftp/](https://pypi.org/project/fftp/) [reference:0]

**Intérêt pour Scan&Ready** : à étudier en priorité. C'est le projet le plus abouti et le plus proche de la vision.

### 1.2. `Queue-Smart`

Système de gestion de file d'attente basé sur des **QR codes** pour restaurants et cafés.

- Les clients rejoignent une file numérique.
- Notification (SMS via Twilio) lorsque la table est prête.
- **Stack** : React, Node.js, GraphQL, MongoDB.

**Lien GitHub** : [https://github.com/miloyang/Queue-Smart](https://github.com/miloyang/Queue-Smart) [reference:1][reference:2]

**Intérêt** : excellente base de code moderne à étudier et adapter.

### 1.3. `digital-queue-management-system`

Gestion de file d'attente avec portail client, panneau d'administration et notifications en temps réel.

- **Notifications en temps réel** (WebSockets) et SMS/Push.
- Architecture **modulaire et évolutive**.

**Lien GitHub** : [https://github.com/tazabreu/digital-queue-management-system](https://github.com/tazabreu/digital-queue-management-system) [reference:3][reference:4]

**Intérêt** : inspiration pour la structure et le temps réel.

### 1.4. `TasteUp-SignalR`

Solution en **ASP.NET Core** avec QR codes pour voir le menu, commander et suivre l'état de la commande en temps réel.

**Lien GitHub** : [https://github.com/DaviMaia10/TasteUp-SignalR](https://github.com/DaviMaia10/TasteUp-SignalR) [reference:5][reference:6]

**Intérêt** : référence si la stack Microsoft est retenue.

---

## 2. Briques techniques réutilisables

### 2.1. Notifications — `ntfy`

Service de notification push **open source**, simple et **auto-hébergeable**.

- Déjà utilisé par `fftp` pour alerter le client.
- Intégration facile.

**Lien GitHub** : [https://github.com/binwiederhier/ntfy](https://github.com/binwiederhier/ntfy) [reference:7][reference:8]

**Recommandation** : brique de notification principale pour Scan&Ready.

### 2.2. Minuteur + notifications navigateur — `Coffee Timer`

PWA open source qui gère un compte à rebours et envoie des **notifications push via Service Worker**, même onglet fermé.

**Lien GitHub** : [https://github.com/laststance/coffee-timer](https://github.com/laststance/coffee-timer) [reference:9][reference:10]

**Intérêt** : exemple parfait pour la partie « minuteur » côté client (PWA).

### 2.3. Temps réel — WebSockets

Utilisé par `digital-queue-management-system` et `Restaurant-orders` pour synchroniser l'état des commandes entre personnel et clients.

**Intérêt** : à considérer pour une expérience fluide.

---

## 3. Kits de démarrage (boilerplates) SaaS

### 3.1. `QR SaaS Starter Kit` (payant, ~29 $)

- **Stack** : Next.js, Supabase, Stripe.
- Inclut : **générateur de QR codes**, gestion des abonnements, authentification, panneau d'administration.
- **Bilingue anglais/arabe avec support RTL** natif.

**Lien** : [https://www.codester.com/items/51824/qr-saas-starter-kit-next-js-supabase-stripe](https://www.codester.com/items/51824/qr-saas-starter-kit-next-js-supabase-stripe) (page produit, pas de dépôt GitHub public car payant) [reference:11]

**Intérêt** : très pertinent si Scan&Ready vise le Moyen-Orient ou le Maghreb.

### 3.2. `open-saas` (gratuit, open source)

- **Stack** : React, Node.js, Prisma.
- Fournit : authentification, emails, paiements (Stripe), etc.

**Lien GitHub** : [https://github.com/wasp-lang/open-saas](https://github.com/wasp-lang/open-saas) 

**Intérêt** : alternative gratuite pour ne pas réinventer la plomberie SaaS.

---

## 4. Stratégie recommandée pour Scan&Ready

1. **Étudier `fftp` en priorité** — le projet le plus proche de la vision. Analyser code, architecture, gestion du QR code et notifications.
2. **S'inspirer de `Queue-Smart` et `digital-queue-management-system`** pour la file d'attente et le temps réel.
3. **Utiliser `ntfy`** pour les notifications, et s'inspirer de `Coffee Timer` pour le minuteur et les notifications client (PWA).
4. **Choisir un boilerplate SaaS** (`open-saas` ou `QR SaaS Starter Kit`) pour la gestion utilisateurs, abonnements et paiements.

**Approche par briques** → MVP livré plus vite, contrôle conservé sur l'expérience utilisateur (cœur de la valeur de Scan&Ready).

---

## 5. Prochaines étapes

- Approfondir l'architecture de `fftp`.
- Définir la stack technique de Scan&Ready.
- Définir le périmètre fonctionnel du MVP.