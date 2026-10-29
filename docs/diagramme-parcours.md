# Diagrammes — Parcours Scan&Ready (MVP V1)

> **Source unique** : [`conception-etape-0.md`](./conception-etape-0.md) (§2 Double parcours QR, §3 Parcours en 5 phases, §5 Périmètre MVP).
> **Format** : Mermaid (rendu natif GitHub / GitLab / VS Code). Une version éditable du parcours d'ensemble est fournie dans [`diagramme-parcours.drawio`](./diagramme-parcours.drawio).
> **Portée** : MVP V1 uniquement. Tout ce qui est V2 (comptes complexes, statistiques avancées, personnalisation) est exclu.

---

## 1. Vue d'ensemble du parcours

Carte des 5 écrans (A, B, C2, D, E), des deux méthodes de jonction client et des échanges avec le serveur.

```mermaid
flowchart TB

  subgraph MANAGER["Commerçant — Manager (caisse / tablette)"]
    direction TB
    A["Écran A — Tableau de bord<br/>Saisie · En attente · Prêt<br/>compteurs du jour"]
    B["Saisir n° de commande + durée<br/>5, 10, 15, 20, 30, 45, 60, 90 min<br/>ou « Prêt immédiatement »"]
    CARD["Écran B — Carte en attente<br/>n° · minuteur · barre de progression<br/>+5 min · Supprimer · Prêt"]
    READY["Colonne « Prêt pour le retrait »<br/>bouton « Commande récupérée »"]
    A --> B
    A --> CARD
    A --> READY
  end

  subgraph ACCES["Accès client"]
    TICKET["Ticket de caisse<br/>QR dynamique — /track/123"]
    STATIC["QR statique comptoir<br/>/track"]
  end

  subgraph CLIENT["Client — navigateur du téléphone, zéro installation"]
    direction TB
    C2["Écran C2 — Saisie<br/>pavé numérique « 000 »<br/>bouton inactif tant que vide"]
    D["Écran D — Suivi<br/>n° sur fond jaune · minuteur géant<br/>« Prêt vers 14h24 » · barre de progression"]
    E["Écran E — Alerte<br/>fond vert · anneaux pulsants<br/>son + vibration"]
    WL["Screen Wake Lock<br/>écran maintenu allumé"]
    G["Lien avis Google<br/>post-commande"]
    D --- WL
    E -.-> G
  end

  subgraph SERVEUR["Serveur"]
    direction TB
    API["API Next.js<br/>POST /api/orders<br/>GET /api/orders/:id"]
    WS["Socket.io<br/>salon restaurant + commande"]
    DB[("PostgreSQL<br/>Restaurant · Manager · Order")]
    JOB["Échéance du minuteur"]
    API --> DB
    API --> WS
  end

  %% Création de la commande
  B -->|"n° + durée"| API
  API -->|"order:created"| CARD
  API -->|"order:created"| D

  %% Jonction client — les deux méthodes de la doc
  TICKET -->|"Méthode A — scan direct, aucune saisie"| D
  STATIC -->|"Méthode B — scan"| C2
  C2 -->|"n° validé"| API

  %% Suivi temps réel
  WS -.->|"sync du minuteur"| CARD
  WS -.->|"order:updated / order:ready"| D

  %% Actions manager
  CARD -->|"Prêt"| API
  CARD -->|"+5 min / Supprimer"| API
  JOB -->|"temps écoulé"| API

  %% Notification et clôture
  API -->|"order:ready"| E
  API -->|"order:ready"| READY
  E -->|"« J'ai récupéré ma commande »"| API
  READY -->|"« Commande récupérée »"| API
  API -->|"order:collected"| READY
```

**Correspondance avec la doc**

| Élément | Section source |
|---|---|
| Écran A (3 zones, pavé, durées, compteurs) | §3.1 |
| Écran B / carte en attente (+5 min, Supprimer, Prêt) | §3.2 |
| Méthode A (QR dynamique `/track/123`) | §2 |
| Méthode B (QR statique `/track` + saisie) | §2, §3.1 |
| Écran D (minuteur, heure de fin, Wake Lock) | §3.4 |
| Écran E (vert, son, vibration, clôture, couper le son) | §3.5 |
| Avis Google | §5 |

---

## 2. Séquence temps réel

L'ordre exact des échanges, du premier clic du manager à la clôture. Le minuteur client est **recalculé depuis l'heure de fin** (`endsAt`), jamais décrémenté localement de façon autoritaire : c'est ce qui garantit la synchronisation avec la carte manager et évite la dérive quand le téléphone se met en veille.

```mermaid
sequenceDiagram
    autonumber
    actor M as Manager
    participant S as API + Socket.io
    participant DB as PostgreSQL
    actor C as Client

    Note over M: Écran A — tableau de bord
    M->>S: POST /api/orders { numéro, durée }
    S->>DB: INSERT Order (statut EN_ATTENTE, endsAt)
    DB-->>S: ok
    S-->>M: order:created
    Note over M: Écran B — carte « En attente »<br/>compteurs mis à jour

    alt Méthode A — QR dynamique
        C->>S: GET /track/123
    else Méthode B — QR statique
        C->>S: GET /track → saisie du n° → GET /api/orders/:id
    end
    S-->>C: état + endsAt
    Note over C: Écran D — minuteur calculé depuis endsAt<br/>Screen Wake Lock activé

    loop chaque seconde, côté client
        C->>C: restant = endsAt - maintenant
    end

    opt Prolongation
        M->>S: « +5 min »
        S->>DB: endsAt += 5 min
        S-->>M: order:updated
        S-->>C: order:updated
        Note over C: minuteur recalculé, aucune dérive
    end

    alt Clic du manager
        M->>S: « Prêt »
    else Échéance atteinte
        S->>S: endsAt dépassé
    end
    S->>DB: statut = PRET
    S-->>C: order:ready
    S-->>M: order:ready
    Note over C: Écran E — vert, son, vibration
    Note over M: carte déplacée vers « Prêt »

    C->>S: « J'ai récupéré ma commande »
    S->>DB: statut = RECUPERE
    S-->>M: order:collected
    Note over M: colonne « Prêt » vidée, compteurs à jour

    Note over C: lien avis Google (post-commande)
```

---

## 3. Cycle de vie d'une commande

Les statuts sont ceux imposés par le prompt technique : `EN_ATTENTE`, `PRET`, `RECUPERE`, `ANNULE`.

```mermaid
stateDiagram-v2
    [*] --> EN_ATTENTE: création — n° + durée, ou « immédiat »
    EN_ATTENTE --> EN_ATTENTE: « +5 min » — endsAt prolongé
    EN_ATTENTE --> PRET: « Prêt » (manager) ou échéance atteinte
    EN_ATTENTE --> ANNULE: « Supprimer » (manager)
    PRET --> RECUPERE: client « J'ai récupéré » ou manager
    PRET --> ANNULE: annulation tardive (hors MVP)
    RECUPERE --> [*]
    ANNULE --> [*]
```

---

## 4. Vue technique (stack de la doc, §4)

```mermaid
flowchart LR
  subgraph NAV["Navigateur — zéro installation"]
    PM["Manager<br/>tablette / caisse"]
    PC["Client<br/>téléphone"]
  end

  subgraph SRV["Serveur Node.js"]
    NX["Next.js App Router<br/>pages + API"]
    IO["Socket.io<br/>temps réel"]
  end

  DB[("PostgreSQL")]
  PUSH["Web Push / Service Worker<br/>(PWA, si retenu)"]

  PM -->|"HTTPS"| NX
  PC -->|"HTTPS"| NX
  IO -->|"WSS — push instantané"| PM
  IO -->|"WSS — push instantané"| PC
  NX --> DB
  NX --- IO
  NX -.->|"notification en arrière-plan"| PUSH
  PUSH -.->|"alerte"| PC
```

> ⚠️ Point à trancher : la doc hésite entre **Web Push (Service Worker)**, **ntfy** et **Socket.io** pour l'alerte. Socket.io seul ne réveille pas un téléphone verrouillé ; seule une notification push le fait. Ce schéma montre les deux canaux, mais un seul doit être retenu dans la spec finale.

---

## 5. Ce que ce diagramme ne couvre pas (volontairement)

- **Authentification** du manager et **multi-établissement** : non spécifiés dans `conception-etape-0.md`, à documenter avant l'implémentation.
- **Cas d'erreur** (numéro introuvable, commande expirée, deux clients sur le même numéro) : mentionnés dans le prompt technique, absents de la conception.
- **V2** : statistiques, fidélité, personnalisation graphique, intégration caisse.
