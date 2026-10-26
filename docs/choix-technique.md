# Choix technique — Scan&Ready

## Table des matières

1. [Analyse des besoins fonctionnels du MVP](#1-analyse-des-besoins-fonctionnels-du-mvp)
2. [Évaluation des solutions open source](#2-evaluation-des-solutions-open-source)
3. [Évaluation du développement de A à Z](#3-evaluation-du-developpement-de-a-z)
4. [Comparaison finale et recommandation](#4-comparaison-finale-et-recommandation)
5. [Décision recommandée](#5-décision-recommande)

---

## 1. Analyse des besoins fonctionnels du MVP

### 1.1 Résumé des besoins fonctionnels

Le MVP de **Scan&Ready** doit permettre aux clients de cafés et restaurants de commander via un QR code sur leur comptoir, de voir un minuteur en temps réel sur leur téléphone, et de recevoir une notification (sonnerie + vibration) lorsque leur commande est prête.

Les fonctionnalités **strictement nécessaires au MVP** sont :
- Génération et affichage d'un QR code pour le personnel
- Saisie du numéro de facture/ticket par le client
- Affichage d'un compte à rebours (minuteur) sur le téléphone du client
- Notification push (sonnerie + vibration) lorsque la commande est prête
- Interface simple pour le personnel pour valider la commande

Les fonctionnalités **nice-to-have** (à repousser en V2) incluent :
- Lien direct vers Google Maps pour laisser un avis
- Gestion de sécurité des appareils du personnel (code de liaison, déconnexion)
- Modes d'affichage flexibles pour le personnel (barre latérale, fenêtre flottante)
- Tableau de bord pour le personnel avec flux de commandes en temps réel
- Intégration avec le système de caisse existant

### 1.2 Contraintes techniques et non techniques

| Catégorie | Contraintes |
|-----------|-------------|
| **Technique** | - Zéro installation côté client et côté personnel (tout dans le navigateur) |
| | - Zéro matériel à fournir (fonctionne sur PC, tablette, téléphone) |
| | - Installation en 5 minutes maximum |
| | - Formation du personnel quasi nulle |
| | - Fonctionnement sur tout appareil avec navigateur |
| | - Résiliation sans contrat, sans engagement |
| **Non technique** | - Positionnement B2B2C (cafés, restaurants, clients) |
| | - Marché cible : Moyen-Orient / Maghreb (Arabe, dialectes locaux) |
| | - Priorité : Web d'abord, puis PWA pour notifications push et mode hors-ligne |
| | - Simplicité extrême : cœur de la valeur de l'application |

---

## 2. Évaluation des solutions open source

### 2.1 `fftp` (Open Protocol for Ordering Food)

- **Stack technique** : Python, Flask, React, PostgreSQL
- **Adéquation au besoin** : **Fort** — Le projet est le plus proche de Scan&Ready. Génère des QR codes pour commander, envoie des notifications via `ntfy` au client, et propose un tableau de bord pour le personnel.
- **Qualité du code** : Bonne structure, documentation claire, activité de dépôt régulière.
- **Coût d'adaptation** : Moyen — Il faudra adapter l'architecture pour un MVP ultra-simple, mais le cœur (QR + notifications) est prêt.
- **Risques** : Licence (MIT), dépendance à `ntfy` pour les notifications. Le projet est actif et bien maintenu.
- **Verdict** : **À réutiliser et adapter** — C'est la base de code la plus pertinente.

### 2.2 `Queue-Smart`

- **Stack technique** : React, Node.js, GraphQL, MongoDB
- **Adéquation au besoin** : **Moyen** — Gère les files d'attente avec QR codes et notifications SMS (Twilio). Très moderne, mais plus complexe que nécessaire pour un MVP.
- **Qualité du code** : Bonne architecture modulaire, bien documenté.
- **Coût d'adaptation** : Élevé — Nécessite une refonte importante pour un MVP minimaliste.
- **Risques** : Courbe d'apprentissage, dépendance à Twilio pour les SMS.
- **Verdict** : **À forker et adapter** — Utile pour l'architecture de la file d'attente, mais pas pour le MVP complet.

### 2.3 `digital-queue-management-system`

- **Stack technique** : Node.js, Express, MongoDB
- **Adéquation au besoin** : **Moyen** — Gestion de file d'attente avec notifications en temps réel (WebSockets) et SMS/Push.
- **Qualité du code** : Architecture modulaire et évolutive.
- **Coût d'adaptation** : Élevé — Projet plus grand que nécessaire pour un MVP.
- **Risques** : Complexité excessive pour un MVP.
- **Verdict** : **À forker et adapter** — Pour la partie file d'attente, mais pas pour l'ensemble.

### 2.4 `TasteUp-SignalR`

- **Stack technique** : ASP.NET Core, SignalR
- **Adéquation au besoin** : **Faible** — Stack Microsoft, pas adapté si le projet ne cible pas le marché américain/Europe.
- **Qualité du code** : Bonne structure, bien documenté.
- **Coût d'adaptation** : Élevé — Nécessite une migration complète.
- **Risques** : Dépendance au stack .NET, pas aligné avec le positionnement arabe du projet.
- **Verdict** : **À écarter** — Pas pertinent pour le marché cible.

### 2.5 `Coffee Timer`

- **Stack technique** : PWA (React), Service Worker
- **Adéquation au besoin** : **Fort** pour la partie minuteur — Gère un compte à rebours et envoie des notifications push via Service Worker, même onglet fermé.
- **Qualité du code** : Exemple parfait de PWA pour le minuteur.
- **Coût d'adaptation** : Faible — On peut réutiliser la logique de minuteur.
- **Risques** : Dépendance au Service Worker (navigateurs supportant bien cette fonctionnalité).
- **Verdict** : **À réutiliser** — Pour la composante minuteur du MVP.

### 2.6 `open-saas`

- **Stack technique** : React, Node.js, Prisma, Stripe
- **Adéquation au besoin** : **Moyen** — Fournit authentification, paiements, emails. Utile pour la gestion des utilisateurs et des abonnements.
- **Qualité du code** : Bonne structure, bien documenté.
- **Coût d'adaptation** : Moyen — Il faut l'intégrer pour la gestion des utilisateurs et paiements.
- **Risques** : Licence (MIT), dépendance à Stripe.
- **Verdict** : **À intégrer** — Pour la partie gestion des utilisateurs et des paiements.

### 2.7 `QR SaaS Starter Kit`

- **Stack technique** : Next.js, Supabase, Stripe
- **Adéquation au besoin** : **Moyen** — Générateur de QR codes, gestion des abonnements, authentification, panneau d'administration.
- **Qualité du code** : Bonne structure, moderne.
- **Coût d'adaptation** : Élevé — C'est un kit payant (~$29), mais la base est solide.
- **Risques** : Coût, dépendance à Supabase/Stripe.
- **Verdict** : **À considérer** — Si le budget le permet et que le marché cible est le Moyen-Orient (bilingue anglais/arabe avec RTL).

---

## 3. Évaluation du développement de A à Z

### 3.1 Stack technique proposée

| Brique | Choix | Justification |
|--------|-------|---------------|
| **Frontend** | **React + Vite** | Léger, rapide à développer, excellent support pour les PWA, communauté active. Compatible avec les briques `Coffee Timer` et `QR SaaS Starter Kit` |
| **Backend** | **Node.js + Express** | Simplicité, écosystème riche, facile à intégrer avec les briques open source |
| **Base de données** | **PostgreSQL** | Fiabilité, support JSON, adapté aux données de commandes et utilisateurs |
| **Notifications** | **ntfy** | Open source, auto-hébergeable, simple à intégrer, utilisé par `fftp` |
| **Minuteur/PWA** | **Coffee Timer** | Exemple parfait pour la logique de minuteur avec Service Worker |
| **Authentification** | **JWT + Passport.js** | Léger, adapté à un MVP sans besoin de système d'abonnement complexe au départ |
| **Paiements** | **Stripe** | Standard pour les paiements, support RTL (arabe) via Next.js |
| **PWA** | **Workbox** | Gère les Service Worker, caching, notifications push de manière robuste |

### 3.2 Estimation de l'effort pour le MVP

- **Temps total estimé** : 4-6 semaines (2-3 sprints de 2 semaines)
- **Sprints** :
  1. **Sprint 1** (2 semaines) : MVP fonctionnel basique
    - QR code génération et affichage
    - Saisie du numéro de facture
    - Compte à rebours (minuteur) côté client
    - Notification push via ntfy
    - Interface simple pour le personnel
  2. **Sprint 2** (2 semaines) : Affinage et intégration
    - Tableau de bord pour le personnel
    - Intégration avec `Coffee Timer` pour le minuteur
    - Gestion des utilisateurs et authentification
    - Tests et corrections

### 3.3 Risques et points de vigilance

1. **Complexité de l'architecture** : Même si on utilise des briques open source, il faut les intégrer correctement. Risque de sur-ingénierie.
2. **Notifications push** : La compatibilité des Service Worker entre navigateurs peut varier. Il faut tester sur plusieurs navigateurs.
3. **Gestion des QR codes** : Assurer que les QR codes sont générés de manière sécurisée et que les clients ne peuvent pas les dupliquer.
4. **Multilinguisme** : Support des dialectes arabes (jordanien, tunisien) et RTL.
5. **Hébergement** : Choisir un hébergeur qui supporte les PWA et les bases de données PostgreSQL.

---

## 4. Comparaison finale et recommandation

| Critère | Partir d'un open source | Développer de A à Z |
|---------|-------------------------|---------------------|
| **Time-to-market** | **Rapide** — On peut utiliser `fftp` comme base et adapter rapidement | **Plus long** — Il faut construire l'architecture de zéro, mais on gagne en contrôle total |
| **Maîtrise UX** | **Moyen** — On hérite d'une partie de l'UX, mais il faut l'adapter | **Élevé** — On peut concevoir l'expérience utilisateur de zéro, optimisée pour la simplicité extrême |
| **Coût de développement** | **Faible** — On réutilise une grande partie du code | **Élevé** — Il faut développer l'architecture, les intégrations, les tests |
| **Dette technique initiale** | **Faible** — Le code open source est généralement propre | **Élevée** — Il faut créer et maintenir une base de code cohérente |
| **Flexibilité / évolutivité** | **Moyen** — Dépendance aux briques choisies | **Élevée** — On peut choisir les technologies les plus adaptées à chaque besoin |
| **Risques** | **Faibles** — Bases de code éprouvées, communauté active | **Élevés** — Risque de sur-ingénierie, bugs à corriger, intégration complexe |

### Recommandation finale

**Approche retenue : Développer de A à Z avec une stack légère et modulaire**

Pourquoi ?
- Le MVP doit être livré rapidement (5 minutes d'installation, formation quasi nulle).
- La simplicité extrême est le cœur de la valeur de Scan&Ready — une architecture complexe ne peut pas être réellement simple.
- Les briques open source (`fftp`, `Coffee Timer`, `ntfy`) sont excellentes à réutiliser, mais elles ne couvrent pas toutes les fonctionnalités nécessaires (gestion des utilisateurs, authentification avancée, paiements).
- En développant de A à Z, on peut créer une architecture sur mesure qui est parfaitement adaptée au positionnement B2B2C et au marché du Moyen-Orient.

### Les 3-5 premières actions concrètes à lancer demain matin

1. **Initialiser le projet** : Créer un nouveau dépôt Git avec la structure de base (frontend React + backend Node.js).
2. **Configurer le backend** : Express + PostgreSQL + JWT pour l'authentification simple.
3. **Implémenter la génération de QR codes** : API qui génère des QR codes uniques pour chaque commande.
4. **Développer le minuteur PWA** : Intégrer `Coffee Timer` pour le compte à rebours avec notifications push.
5. **Créer l'interface client** : Page de scan QR + compte à rebours + bouton "Prêt".

### Décisions à confirmer avant de coder

- **Choix de la base de données** : PostgreSQL (recommandé pour la fiabilité et le support JSON).
- **Stratégie de notification** : Utiliser `ntfy` (open source, auto-hébergeable) plutôt que Twilio pour réduire les coûts et la dépendance.
- **Architecture du minuteur** : Utiliser `Coffee Timer` comme référence pour le Service Worker et les notifications push.
- **Cible linguistique** : Développer d'abord en anglais, puis ajouter le support arabe (RTL) selon les retours.
- **Hébergement** : Choisir un hébergeur compatible avec les PWA (ex: Vercel, Netlify) et PostgreSQL (ex: Neon, PlanetScale).

---

## Décision recommandée

**Stack technique retenue** : **React + Node.js + PostgreSQL + ntfy + Coffee Timer**

**Plan d'action des 5 premières actions** (à démarrer demain matin) :

1. Initialiser le projet avec une structure monolithique (frontend/backend séparés).
2. Configurer l'API backend avec Express, PostgreSQL et JWT pour l'authentification.
3. Implémenter l'API de génération de QR codes.
4. Développer le minuteur PWA en intégrant `Coffee Timer`.
5. Créer l'interface client simple (scan QR + compte à rebours + notification).

**Risques principaux à surveiller** : compatibilité Service Worker, gestion des erreurs de QR code, et performance du minuteur sur les appareils mobiles.

Ce choix permet de livrer un MVP fonctionnel en 4-6 semaines, avec une expérience utilisateur ultra-simple et une base de code maintenable et évolutive.