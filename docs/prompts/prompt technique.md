# Rôle
Tu es un Développeur Full-Stack Senior et Architecte Logiciel. Tu maîtrises parfaitement l'écosystème JavaScript/TypeScript moderne (Next.js, Node.js, WebSockets, PWA).

# Contexte du Projet
Nous développons **Scan&Ready**, une application web (PWA) B2B2C qui remplace les téléavertisseurs matériels (buzzers) dans les cafés et restaurants. 
Le manager utilise un tableau de bord pour créer des commandes et lancer des minuteurs. Le client utilise son propre téléphone (sans rien installer) pour scanner un QR code, suivre le minuteur en temps réel, et recevoir une alerte (son + vibration) quand sa commande est prête.

# Stack Technique Imposée
*   **Frontend** : Next.js (App Router), React, Tailwind CSS (pour un design rapide et responsive).
*   **Backend** : Node.js avec Express ou Next.js API Routes.
*   **Temps Réel** : Socket.io (indispensable pour synchroniser les minuteurs et envoyer les alertes instantanées).
*   **Base de données** : PostgreSQL (via Supabase pour l'hébergement et l'ORM Prisma).
*   **PWA / Notifications** : Service Workers (pour le son/vibration en arrière-plan) et API Screen Wake Lock (pour empêcher le téléphone du client de s'éteindre).
*   **Génération QR Code** : Librairie `qrcode` (côté serveur/client).

# Spécifications Fonctionnelles à Implémenter (MVP)

## 1. Interface Manager (Tableau de bord)
*   **Création** : Pavé numérique pour saisir le numéro de commande, boutons de temps prédéfinis (5, 10, 15, 20 min) et bouton "Prêt immédiatement".
*   **Colonnes** : "En attente" (avec minuteur en direct) et "Prêt" (commandes terminées).
*   **Carte Commande (En attente)** : Affiche le numéro, le temps restant (mm:ss), une barre de progression, et 3 boutons : "Prêt", "+5 min" (prolonger), "Annuler" (supprimer).
*   **Compteurs** : En-tête affichant le nombre de commandes en attente, prêtes et le total du jour.

## 2. Interface Client (Mobile-First)
*   **Double parcours QR** :
    *   *Méthode A (Dynamique)* : URL `/track/[orderId]` (accès direct).
    *   *Méthode B (Statique)* : URL `/track` (affiche un champ de saisie du numéro).
*   **Écran de Suivi** : Design de type "ticket de caisse". Minuteur géant au centre. Affichage de l'heure de fin estimée (ex: "Prêt vers 14h24").
*   **Écran d'Alerte** : Fond vert vif, message "C'est votre tour !", déclenchement du son et de la vibration via le navigateur. Boutons "J'ai récupéré ma commande" et "Couper le son".

# Ce que je te demande de générer

## Étape 1 : Modélisation des Données (Schéma Prisma)
Génère le schéma de base de données (Prisma) nécessaire. 
*   Tables attendues : `Restaurant`, `Manager`, `Order`.
*   La table `Order` doit gérer : l'ID, le numéro de commande, le statut (EN_ATTENTE, PRET, RECUPERE, ANNULE), l'heure de création, la durée en minutes, et l'heure de fin estimée.

## Étape 2 : Architecture des WebSockets (Socket.io)
Décris la logique des événements Socket.io nécessaires pour la synchronisation :
*   Comment le client rejoint une "room" spécifique à son restaurant ou à sa commande.
*   Les événements pour : créer une commande, prolonger (+5 min), passer au statut "Prêt", et annuler.

## Étape 3 : Code du Backend (API & WebSockets)
Génère le code TypeScript pour :
*   L'endpoint de création de commande (génération de l'heure de fin estimée).
*   La logique Socket.io pour écouter le clic du manager sur "Prêt" et émettre l'événement au client.

## Étape 4 : Code du Frontend (Client PWA)
Génère le composant React (Next.js) de l'écran de suivi client :
*   Utilisation de `useEffect` pour écouter l'événement Socket.io.
*   Logique du compte à rebours (calcul basé sur l'heure de fin estimée).
*   Intégration de l'API `navigator.wakeLock` pour garder l'écran allumé.
*   Logique pour déclencher un son (HTML5 Audio API) et une vibration (`navigator.vibrate()`) quand le statut passe à "PRET".

# Contraintes de Développement
*   Le code doit être écrit en **TypeScript**.
*   L'interface client doit être extrêmement légère (pas de framework lourd, utilisation de Tailwind pour le style).
*   Utilise des composants fonctionnels React avec Hooks.
*   Pense à la gestion des erreurs (ex: commande introuvable, timeout).

Commence par me présenter l'arborescence du projet, puis génère le schéma Prisma et les événements Socket.io.