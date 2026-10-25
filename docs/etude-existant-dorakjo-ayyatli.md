# Étude de l'existant — Dorakjo & Ayyatli

> Analyse comparative de deux applications web de notification de commande prête pour cafés et restaurants.
> Date : 2026-10-25
> Auteure de l'analyse : Architecte web & mobile (assisté)

---

## 1. Présentation des deux applications

| Application | URL | Marché | Langue | Devise |
|---|---|---|---|---|
| **Dorakjo** | https://dorakjo.com/ | Jordanie | Arabe (dialecte jordanien) | Dinar jordanien (JOD) |
| **Ayyatli** | https://ayyatli.com/ | Tunisie | Arabe (dialecte tunisien) | Dinar tunisien (TND) |

Les deux applications résolvent **exactement le même problème** : remplacer les téléavertisseurs (buzzers) dans les cafés et restaurants par le téléphone du client, sans installation matérielle ni application à télécharger.

---

## 2. Concept commun

- **Problème adressé** : les téléavertisseurs coûtent cher, se perdent, se cassent, tombent en panne de batterie, et le client ne sait pas combien de temps il doit attendre.
- **Solution** : le client scanne un QR code sur le comptoir, saisit le numéro de sa facture (ou ticket), et voit un minuteur sur son téléphone. Lorsque la commande est prête, son téléphone sonne et/ou vibre.
- **Promesse** : pas d'application, pas de matériel, installation en 5 minutes, formation du personnel quasi nulle.

---

## 3. Fonctionnement détaillé

| Étape | Côté personnel (café/restaurant) | Côté client |
|---|---|---|
| 1 | Le caissier saisit le numéro de facture et choisit une durée estimée (ex. 10 minutes). | – |
| 2 | Le client scanne le QR code affiché sur le comptoir et entre le même numéro de facture. | Le client voit un compte à rebours sur son téléphone. |
| 3 | Lorsque la commande est prête, le personnel clique sur « Prêt » (ou « جاهز »). | Le téléphone du client sonne/vibre, affichant que la commande est prête. |

---

## 4. Fonctionnalités clés

- **Aucune application à installer** : tout se fait dans le navigateur du téléphone.
- **Aucun matériel à acheter** : fonctionne sur n'importe quel appareil avec un navigateur (PC, tablette, téléphone).
- **Minuteur en temps réel** sur le téléphone du client.
- **Notification « commande prête »** (sonnerie + vibration).
- **Lien direct vers Google Maps pour laisser un avis** après réception de la commande (spécifique à Dorakjo).
- **Gestion de la sécurité des appareils du personnel** : chaque appareil est lié à un code, déconnexion en un clic (spécifique à Dorakjo).
- **Modes d'affichage flexibles** pour le personnel : barre latérale sur l'écran de caisse, fenêtre flottante, ou appareil séparé (spécifique à Dorakjo).

---

## 5. Modèle économique et ciblage

| Critère | Dorakjo | Ayyatli |
|---|---|---|
| **Marché** | Jordanie (dinar jordanien) | Tunisie (dinar tunisien) |
| **Langue** | Arabe (dialecte jordanien) | Arabe (dialecte tunisien) |
| **Prix** | 20 JOD/mois (≈28 USD) | 45 TND/mois ou 450 TND/an |
| **Essai gratuit** | 1 semaine | 7 jours |
| **Engagement** | Sans contrat, résiliable à tout moment | Sans contrat, résiliable à tout moment |
| **Cible** | Cafés et commerces de services | Cafés et restaurants (clients, « الكليون ») |

---

## 6. Différences notables

- **Dorakjo** met davantage l'accent sur l'intégration avec le système de caisse existant (barre latérale, fenêtre flottante) et sur l'obtention d'avis Google.
- **Ayyatli** insiste sur la simplicité extrême (« 4 étapes, c'est tout ») et sur le fait que le client peut rester où il veut (« il s'assoit où il veut et son téléphone l'appelle »).
- Les deux utilisent un discours marketing très proche, presque du copier-coller adapté au dialecte local.

---

## 7. Cadrage du projet à venir

### 7.1. Domaine / besoin principal
**Gestion de file d'attente et notification client** dans le secteur de la restauration rapide et des cafés. Le besoin : informer le client que sa commande est prête sans utiliser de téléavertisseur, sans application mobile, et sans perturber le système de caisse existant.

### 7.2. Cible utilisateurs
**B2B2C** :
- **Professionnels (B2B)** : cafés, restaurants, boulangeries, fast-foods, tout commerce avec retrait de commande sur place.
- **Grand public (B2C)** : les clients de ces établissements, qui utilisent leur propre téléphone.

### 7.3. Priorité plateforme
**Web d'abord, et web uniquement** (dans un premier temps). Les deux applications existantes sont **100 % web** : ni les clients ni le personnel n'installent d'application. Tout passe par le navigateur (responsive). Une **PWA** (Progressive Web App) serait la suite logique pour ajouter des notifications push et un mode hors-ligne, sans passer par les stores. Une application native n'est **pas nécessaire** pour le MVP.

### 7.4. Contraintes de conception implicites fortes
- **Zéro installation** côté client et côté personnel.
- **Zéro matériel** à fournir.
- **Installation en 5 minutes** maximum.
- **Formation du personnel quasi nulle**.
- **Fonctionnement sur tout appareil avec navigateur** (PC, tablette, téléphone).
- **Résiliation sans contrat**.

### 7.5. Objectif de l'étude
**S'inspirer fonctionnellement** de Dorakjo et Ayyatli pour concevoir une application **similaire mais adaptée à un marché ou un positionnement différenciant**. Options possibles :
- **Combiner leurs forces** : la flexibilité d'intégration de Dorakjo + la simplicité d'Ayyatli.
- **Faire mieux sur un point précis** : par exemple, une interface encore plus intuitive, un mode hors-ligne, des statistiques pour le commerçant, ou une expansion vers d'autres langues/marchés.
- **Cibler un marché non couvert** par ces deux apps (Afrique francophone, Maghreb, Europe, etc.).

---

## 8. Synthèse

Le créneau est **clair, validé et simple techniquement**. La valeur ne réside pas dans la complexité technique, mais dans :
- **l'expérience utilisateur** (simplicité extrême, fiabilité de la notification),
- **la confiance du commerçant** (aucun matériel, aucun engagement).

### Décisions à prendre pour la suite

1. **Positionnement** : application quasi identique pour un marché non couvert, ou ajout de fonctionnalités différenciantes dès le MVP ?
2. **Fonctionnalités différenciantes éventuelles** : statistiques, fidélité, commande à emporter, paiement mobile, etc. — ou MVP ultra-simple ?
3. **Marché géographique et langue** prioritaires : cela déterminera devises, dialectes, habitudes locales et intégrations avec les caisses locales.

---

## 9. Prochaines étapes proposées

1. Analyse des deux apps ✅
2. Définition des utilisateurs et besoins ✅
3. **Périmètre fonctionnel du MVP** ⬅️ à venir
4. Parcours utilisateurs
5. Architecture technique
6. Choix web/mobile
7. Maquettes et plan de réalisation