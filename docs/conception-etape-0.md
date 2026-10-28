# Document de Conception Global — Scan&Ready (Étape 0)

> **Statut** : Validé
> **Date** : 2026-10-28
> **Auteur** : Équipe Scan&Ready
> **Objectif** : Spécification fonctionnelle et UI/UX complète du MVP, basée sur l'analyse des concurrents et le parcours utilisateur validé.

---

## 1. Vision du Projet

**Scan&Ready** est une application web (PWA) de gestion de file d'attente et de notification client pour les cafés, restaurants et commerces de proximité. 
L'objectif est de remplacer les téléavertisseurs matériels (buzzers) par le téléphone du client, sans aucune installation d'application ni achat de matériel.

*   **Promesse client** : Zéro application, zéro compte, zéro attente debout.
*   **Promesse commerçant** : Installation en 5 minutes, formation du personnel quasi nulle, aucun abonnement contraignant.

---

## 2. Le Double Parcours de Suivi Client (Différenciation Scan&Ready)

Pour s'adapter à tous les types de commerces, Scan&Ready propose deux méthodes d'accès au suivi de commande :

1.  **Méthode A : QR Code Dynamique (sur le ticket)**
    *   La caisse génère un ticket avec un QR code unique encodant l'URL (ex: `app.scanready.com/track/123`).
    *   Le client scanne le ticket et arrive *directement* sur son écran de suivi. Aucune saisie manuelle.
2.  **Méthode B : QR Code Statique (sur le comptoir)**
    *   Un QR code fixe est affiché en boutique (ex: `app.scanready.com/track`).
    *   Le client le scanne et doit saisir le numéro inscrit sur son ticket via un pavé numérique.

---

## 3. Parcours Utilisateur Détaillé (Basé sur les 5 étapes de la démo)

Le parcours s'articule autour de deux interfaces synchronisées en temps réel via WebSockets : **Le Manager** (caisse/tablette) et **Le Client** (navigateur du téléphone).

### 3.1. Phase 1 : État Initial (Aucune commande)
*   **Côté Manager (Écran A)** : 
    *   Interface divisée en 3 zones : Saisie (droite), En attente (centre), Prêt (gauche).
    *   Pavé numérique géant pour taper le numéro de commande.
    *   Boutons de temps prédéfinis (5, 10, 15, 20, 30, 45, 60, 90 min).
    *   Bouton **"Prêt immédiatement"** pour les commandes express.
    *   Compteurs en haut : 0 en attente, 0 prêt, total du jour.
*   **Côté Client (Écran C2 - Saisie)** : 
    *   Design imitant un ticket de caisse (bordure dentelée).
    *   Champ de saisie "000" avec curseur clignotant.
    *   Bouton "Suivre ma commande" *grisé/inactif* tant que le champ est vide.

### 3.2. Phase 2 : Création de l'instance (Côté Manager)
*   Le manager tape "123", choisit "5 min" et valide.
*   Une **carte interactive** apparaît dans la colonne "En attente" (Écran B).
*   La carte affiche : Le numéro (123), un compte à rebours (4:31), et une fine barre de progression visuelle.
*   La carte propose des actions rapides : **"+5 min"** (prolonger), **"Supprimer"** (annuler), et **"Prêt"** (notifier).

### 3.3. Phase 3 : Jonction Client
*   **Côté Client** : Le client tape "123". Le bouton devient actif (couleur foncée). Un message de réassurance s'affiche : *"Nous vous appellerons quand c'est prêt"*.
*   Le client clique sur "Suivre". 

### 3.4. Phase 4 : Suivi en temps réel (Synchronisation)
*   **Côté Client (Écran D)** : 
    *   L'écran se transforme. Le numéro "123" reste en haut sur fond jaune.
    *   Un **minuteur géant** au centre affiche le temps restant (ex: 4:47).
    *   L'heure de fin estimée est affichée (ex: "Prêt vers 14h24").
    *   Une barre de progression avance de "En préparation" à "Prêt".
    *   *Note technique* : Utilisation de l'API `Screen Wake Lock` pour empêcher le téléphone de s'éteindre.
*   **Côté Manager** : Le minuteur de la carte se synchronise exactement avec celui du client.

### 3.5. Phase 5 : Notification et Clôture
*   Le manager clique sur "Prêt" (ou le temps arrive à zéro).
*   **Côté Client (Écran E - Alerte)** :
    *   L'écran devient **vert vif** avec des anneaux pulsants autour du numéro.
    *   Message : *"C'est votre tour ! Commande prête"*.
    *   Le téléphone **sonne et vibre**.
    *   Actions : Bouton *"J'ai récupéré ma commande"* (clôture) et lien *"Couper le son"*.
*   **Côté Manager** :
    *   La commande 123 quitte "En attente" et glisse dans "Prêt pour le retrait".
    *   Les compteurs du haut se mettent à jour.
    *   La carte propose le bouton *"Commande récupérée"* (si le client oublie de cliquer).

---

## 4. Exigences Techniques et Stack Pressentie

Pour garantir cette expérience fluide, la stack technique suivante est recommandée :

*   **Frontend (Client & Manager)** : Next.js / React (pour la PWA et le rendu rapide).
*   **Backend & Temps réel** : Node.js avec **Socket.io** (indispensable pour la synchronisation des minuteurs et l'envoi des alertes instantanées).
*   **Notifications** : Service Workers natifs (PWA) pour l'alerte sonore en arrière-plan, ou intégration de **ntfy** (open source).
*   **Base de données** : PostgreSQL (via Supabase pour la rapidité).
*   **Génération QR Code** : Librairie JS (ex: `qrcode`).

---

## 5. Périmètre du MVP (V1)

*   [x] Interface Manager : Pavé numérique, minuteurs, bouton "Prêt immédiat", actions sur carte (+5 min, annuler).
*   [x] Interface Client : Saisie manuelle (Méthode B) et suivi direct (Méthode A).
*   [x] Design "Ticket" et minuteur géant côté client.
*   [x] Synchronisation temps réel (WebSockets) et alerte sonore/vibration.
*   [x] Compteurs dynamiques côté manager.
*   [x] Lien vers les avis Google (post-commande).

*(Reportés en V2 : Gestion de compte complexe, statistiques avancées, personnalisation graphique poussée).*

---

## 6. Prochaines Étapes (Étape 1)

1.  Création de la charte graphique (couleurs, typographies, icônes).
2.  Réalisation des Wireframes (maquettes filaires) en français/anglais pour Scan&Ready.
3.  Définition du schéma de base de données (Tables `Restaurants`, `Commandes`, `Managers`).
4.  Initialisation du dépôt de code et configuration de l'environnement.