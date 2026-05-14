# Ontologie de la donnée — opérations d'influence numérique
**Version :** 1  
**Statut :** Ébauche  
**Date :** 2026-05-13  
**Cadres de référence :** Diamond Model, DISARM red framework, DIMA, SAT (Structured Analytic Techniques)

> **Note de lecture :** les types `enum` marqués *[proposé]* n'existent pas en tant que liste fermée dans le code source — ce sont des valeurs éditoriales suggérées pour normalisation future. Les champs correspondants sont des `string` libres dans l'implémentation actuelle.

---

## 1. Entité racine : `Incident`

Représente une opération d'influence numérique identifiée et analysée.

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `code_operation` | string | 1 | Identifiant textuel (ex : `OP-2024-001`) |
| `date_creation` | date ISO | 1 | Date de création de la fiche |
| `date_derniere_maj` | date ISO | 1 | Date de dernière modification |
| `statut` | enum | 1 | `en_cours`, `terminee`, `en_analyse`, `archivee` |
| `date_debut` | date ISO | 0..1 | Début estimé de l'opération |
| `date_fin` | date ISO | 0..1 | Fin estimée de l'opération |
| `efficacite_estimee` | string | 0..1 | Appréciation qualitative de l'efficacité |
| `portee_estimee` | string | 0..1 | Appréciation qualitative de la portée |
| `commentaires` | text | 0..1 | Notes analytiques transversales |
| `mots_cles` | string[] | 0..N | Étiquettes libres pour la recherche |
| `mots_cles_text` | string | 0..1 | **Champ UI interne** — tampon de saisie des mots-clés, non exporté dans le JSON final |
| `evenements` | Evenement[] | 0..N | Jalons chronologiques |
| `deception_detection` | DeceptionDetection | 0..1 | Résultats de l'analyse de détection de tromperie (SAT 9.1) |
| `structured_techniques_applied` | TechniqueSATAppliquee[] | 0..N | Techniques SAT documentées sur ce dossier |

---

## 2. Composantes Diamond Model

### 2.1 `Attaquant` — Diamond : Adversary

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `nom` | string | 1 | Nom ou pseudonyme |
| `type` | enum *[proposé]* | 0..1 | `etat`, `groupe_para`, `acteur_prive`, `inconnu` |
| `pays_origine` | string | 0..1 | Pays ou zone géographique d'origine |
| `niveau_attribution` | enum | 0..1 | Voir §5 |
| `role_dans_operation` | string | 0..1 | Commanditaire, opérateur, relais... |
| `description` | text | 0..1 | Notes libres |

### 2.2 `Cible` — Diamond : Victim

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `pays` | string | 0..1 | Pays ciblé |
| `region` | string | 0..1 | Zone sub-nationale |
| `description_libre` | text | 0..1 | Description qualitative |
| `type_cible` | string[] | 0..N | Opinion publique, élites, diaspora... |

### 2.3 Infrastructure — Diamond : Infrastructure

#### `Canal`

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `canal` | string | 0..1 | Catégorie (réseaux sociaux, presse, messagerie...) |
| `plateforme` | string | 0..1 | Plateforme précise (Telegram, Facebook...) |
| `ampleur` | enum *[proposé]* | 0..1 | `faible`, `moderee`, `forte`, `massive` |

#### `Relais`

Entité intermédiaire qui amplifie ou relaie les contenus, avec ou sans conscience de son rôle.

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `nom` | string | 0..1 | Nom de l'entité |
| `type` | string | 0..1 | `media`, `influenceur`, `ONG`, `parti`, `individu`... |
| `role` | string | 0..1 | Rôle dans la chaîne de diffusion |
| `degre_conscience` | enum | 0..1 | `conscient`, `semi-conscient`, `inconscient` |

### 2.4 Capacité — Diamond : Capability

#### `Technique DISARM`

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `code_disarm` | string | 0..1 | Code DISARM (ex : `T0023`) |
| `nom` | string | 0..1 | Nom de la technique |
| `description` | text | 0..1 | Contextualisation pour cet incident |

#### `Technique DIMA`

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `code_dima` | string | 0..1 | Code DIMA (ex : `TE0331`) |
| `nom` | string | 0..1 | Nom du biais ou mécanisme cognitif |
| `description` | text | 0..1 | Contextualisation pour cet incident |

#### `FormatContenu`

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `format` | string | 0..1 | `image`, `video`, `texte`, `audio`, `deepfake`... |
| `langue` | string | 0..1 | Langue principale du contenu |

---

## 3. Dimension narrative et psychologique

### 3.1 `Objectif`

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `type_objectif` | string | 0..1 | `diviser`, `dissuader`, `decredibiliser`, `amplifier`, `recruter`... |
| `description` | text | 0..1 | Formulation précise |

### 3.2 `Narratif`

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `description` | text | 0..1 | Énoncé du narratif |
| `veracite` | enum *[proposé]* | 0..1 | `faux`, `trompeur`, `hors-contexte`, `vrai-detourne`, `indetermine` |
| `themes` | string[] | 0..N | Thèmes associés |

### 3.3 `LevierPsychologique`

Valeur parmi une liste fixe (principes de Cialdini) :

- Principe de réciprocité
- Principe d'autorité
- Principe de cohérence
- Principe de la preuve sociale
- Principe de rareté
- Principe de sympathie

---

## 4. Entités transverses

### 4.1 `Evenement`

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `date` | date ISO | 1 | Date de l'événement |
| `type` | enum | 1 | `start`, `end`, `custom`, `analysis`, `attribution` |
| `label` | string | 0..1 | Titre court |
| `description` | text | 0..1 | Détail |

### 4.2 `Document`

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `titre` | string | 0..1 | Titre de la source |
| `type` | enum | 0..1 | `rapport`, `article`, `archive`, `screenshot`, `autre` |
| `url` | URL | 0..1 | Lien https uniquement |

### 4.3 `DeceptionDetection`

Objet unique embarqué dans `Incident`. Correspond à la technique SAT 9.1 (Détection de tromperie). Les quatre indicateurs sont les cadres d'évaluation MOM / POP / MOSES / EVE.

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `mom_checked` | boolean | 1 | MOM — Motive, Opportunity, Means : l'acteur avait-il les moyens et l'intention de tromper ? |
| `pop_checked` | boolean | 1 | POP — Past Opposition Practices : l'acteur a-t-il déjà eu recours à la tromperie ? |
| `moses_checked` | boolean | 1 | MOSES — Manipulate, Observe, Signal, Evaluate, Survive : la tromperie sert-elle une stratégie de survie ou de manipulation observable ? |
| `eve_checked` | boolean | 1 | EVE — Evaluate Veracity of Evidence : les preuves résistent-elles à une évaluation d'authenticité ? |
| `notes` | text | 0..1 | Observations, indices identifiés, niveau de confiance |

### 4.4 `TechniqueSATAppliquee`

Enregistre une technique SAT (issue du référentiel `SAT_TECHNIQUES`) appliquée à ce dossier.

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `technique_id` | string | 0..1 | Identifiant SAT (ex : `4.3`) — référence vers `SAT_TECHNIQUES[].id` |
| `technique_nom` | string | 1 | Nom de la technique (ex : `Analyse des hypothèses concurrentes (ACH)`) |
| `date_application` | date ISO | 0..1 | Date à laquelle la technique a été appliquée |
| `analyste` | string | 0..1 | Identifiant ou initiales de l'analyste |
| `notes` | text | 0..1 | Résultats, conclusions, niveau de confiance |

---

## 5. Énumérations

### `niveau_attribution`

| Valeur | Couleur | Signification |
|---|---|---|
| `Confirmé` | `#C8372D` | Attribution établie |
| `Probable` | `#D97706` | Attribution très vraisemblable |
| `Possible` | `#CA8A04` | Attribution envisagée |
| `Non attribué` | `#9CA3AF` | Aucune attribution disponible |

### `statut`

| Valeur | Libellé UI | Signification |
|---|---|---|
| `en_cours` | En cours | Analyse en cours |
| `terminee` | Terminée | Analyse finalisée |
| `en_analyse` | En analyse | Dossier reçu, analyse non démarrée |
| `archivee` | Archivée | Dossier clos et archivé |

### `degre_conscience`

| Valeur | Signification |
|---|---|
| `conscient` | Le relais sait qu'il participe à l'opération |
| `semi-conscient` | Le relais est partiellement informé |
| `inconscient` | Le relais agit de bonne foi |

---

## 6. Schéma de relations

```
Incident
  ├── [0..N] Attaquant                  → Adversary
  ├── [0..N] Cible                      → Victim
  ├── [0..N] Canal (canaux_diffusion)   → Infrastructure
  ├── [0..N] Relais                     → Infrastructure
  ├── [0..N] Technique DISARM           → Capability
  ├── [0..N] Technique DIMA             → Capability
  ├── [0..N] FormatContenu              → Capability
  ├── [0..N] Objectif
  ├── [0..N] Narratif
  │              └── [0..N] themes (string)
  ├── [0..N] LevierPsychologique (string enum)
  ├── [0..N] Document
  ├── [0..N] Evenement
  ├── [0..1] DeceptionDetection         → SAT 9.1
  └── [0..N] TechniqueSATAppliquee      → SAT_TECHNIQUES[]
```

---

## 7. Exemple JSON minimal valide

```json
{
  "code_operation": "EXEMPLE-2025",
  "date_creation": "2025-01-15",
  "date_derniere_maj": "2025-01-15",
  "statut": "en_cours",
  "date_debut": "2024-09-01",
  "date_fin": "",
  "efficacite_estimee": "",
  "portee_estimee": "",
  "commentaires": "",
  "mots_cles": ["election", "desinformation"],
  "attaquants": [
    {
      "nom": "Acteur X",
      "type": "etat",
      "pays_origine": "Russie",
      "niveau_attribution": "Probable",
      "role_dans_operation": "Commanditaire",
      "description": ""
    }
  ],
  "cibles": [
    {
      "pays": "France",
      "region": "",
      "description_libre": "Opinion publique générale",
      "type_cible": ["opinion_publique"]
    }
  ],
  "canaux_diffusion": [
    { "canal": "Réseaux sociaux", "plateforme": "Telegram", "ampleur": "forte" }
  ],
  "relais": [
    {
      "nom": "Blog XYZ",
      "type": "media",
      "role": "Amplificateur",
      "degre_conscience": "inconscient"
    }
  ],
  "techniques": [
    { "code_disarm": "T0023", "nom": "Fabricate Quotes", "description": "" }
  ],
  "techniques_dima": [
    { "code_dima": "TE0332", "nom": "Effet de simple exposition", "description": "" }
  ],
  "formats_contenu": [
    { "format": "image", "langue": "fr" }
  ],
  "objectifs": [
    { "type_objectif": "diviser", "description": "Exacerber les tensions autour de l'immigration" }
  ],
  "narratifs": [
    {
      "description": "Les élites trahissent le peuple",
      "veracite": "faux",
      "themes": ["immigration", "souverainisme"]
    }
  ],
  "leviers_psychologiques": ["Principe de la preuve sociale"],
  "documents": [
    { "titre": "Rapport EU DisinfoLab", "type": "rapport", "url": "https://example.com" }
  ],
  "evenements": [
    { "date": "2024-09-01", "type": "start", "label": "Début de l'opération", "description": "" },
    { "date": "2024-11-03", "type": "custom", "label": "Pic de diffusion", "description": "Volume x10 en 48h" }
  ],
  "deception_detection": {
    "mom_checked": true,
    "pop_checked": true,
    "moses_checked": false,
    "eve_checked": false,
    "notes": "Historique de campagnes similaires documenté par l'UE. Moyens techniques confirmés."
  },
  "structured_techniques_applied": [
    {
      "technique_id": "4.3",
      "technique_nom": "Analyse des hypothèses concurrentes (ACH)",
      "date_application": "2025-01-10",
      "analyste": "MF",
      "notes": "Trois hypothèses évaluées. Attribution à acteur étatique retenue avec niveau Probable."
    }
  ]
}
```
