# Ontologie de la donnée : opérations d'influence numérique
**Version :** 2 (schéma 1.1)  
**Statut :** référence, alignée sur `saisie-fiche-v3.html` et `visualisation-fiche-v3.html`  
**Date :** 2026-10-30  
**Cadres de référence :** Diamond Model, DISARM red framework, DIMA, SAT (Structured Analytic Techniques)  
**Schéma formel :** [`schema-incident-1.1.json`](schema-incident-1.1.json) (JSON Schema draft 2020-12)  
**Licence :** [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.fr), © 2026 Mathieu Ferasse. Reprise libre, y compris commerciale, avec mention de la source.

> **Règle de compatibilité.** Tous les champs ajoutés par le schéma 1.1 sont optionnels : une fiche 1.0 reste lisible par les outils v3. Le JSON Schema, lui, ne valide une fiche 1.0 que si ses valeurs sont dans les listes. À l'import, une valeur hors liste n'est jamais rejetée ni effacée : elle est reprise (§6). Les champs inconnus de l'outil sont conservés et réexportés tels quels (§7).

---

## 1. Entité racine : `Incident`

Représente une opération d'influence numérique identifiée et analysée.

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `version_schema` | string | 0..1 | `"1.1"`, écrit à l'export. Absent = fiche 1.0 |
| `code_operation` | string | 1 | Identifiant textuel (ex : `incident-2026-doppelganger-france-ru`) |
| `date_creation` | date ISO | 1 | Date de création de la fiche |
| `date_derniere_maj` | date ISO | 1 | Date de dernière modification |
| `statut` | enum | 1 | Voir §5 |
| `date_debut` | date ISO | 0..1 | Début estimé de l'opération |
| `date_fin` | date ISO | 0..1 | Fin estimée de l'opération |
| `portee` | Evaluation | 0..1 | Exposition : qui a vu, où, avec quel engagement (§4.5) |
| `efficacite` | Evaluation | 0..1 | Effet obtenu chez le public (§4.5), avec en plus `origine` et `origine_source` |
| `portee_estimee` | string | 0..1 | **Dérivé**, réécrit à l'export depuis `portee` (niveau, sinon début de la synthèse). Conservé pour les outils v2 |
| `efficacite_estimee` | string | 0..1 | **Dérivé**, même règle depuis `efficacite` |
| `commentaires` | text | 0..1 | Notes analytiques transversales |
| `mots_cles` | string[] | 0..N | Étiquettes libres pour la recherche |
| `mots_cles_text` | string | 0..1 | Champ interne au formulaire, non exporté |
| `evenements` | Evenement[] | 0..N | Jalons chronologiques |
| `deception_detection` | DeceptionDetection | 0..1 | Détection de tromperie (SAT 9.1) |
| `structured_techniques_applied` | TechniqueSATAppliquee[] | 0..N | Techniques SAT documentées sur ce dossier |

Plus les listes décrites aux §2 et §3 (`attaquants`, `cibles`, `canaux_diffusion`, `relais`, `techniques`, `techniques_dima`, `formats_contenu`, `objectifs`, `narratifs`, `leviers_psychologiques`, `documents`).

---

## 2. Composantes Diamond Model

### 2.1 `Attaquant` (Diamond : Adversary)

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `nom` | string | 1 | Nom ou pseudonyme |
| `type` | enum `attaquant_type` | 0..1 | Voir §5 |
| `type_precisions` | string | 0..1 | Texte libre ; reçoit aussi une valeur reprise à l'import |
| `pays_origine` | string | 0..1 | Pays ou zone géographique d'origine |
| `niveau_attribution` | enum | 0..1 | Probabilité de l'attribution, voir §5 |
| `confiance` | enum `confiance` | 0..1 | Solidité des preuves de l'attribution, voir §5 |
| `confiance_justification` | string | 0..1 | Sur quoi repose la confiance |
| `role_dans_operation` | string | 0..1 | Commanditaire, opérateur, relais... |
| `description` | text | 0..1 | Notes libres |

`niveau_attribution` dit **à quel point c'est probable**, `confiance` dit **à quel point la preuve est solide**. Les deux se renseignent séparément.

### 2.2 `Cible` (Diamond : Victim)

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `pays` | string | 0..1 | Pays ciblé |
| `region` | string | 0..1 | Zone sub-nationale |
| `description_libre` | text | 0..1 | Description qualitative |
| `type_cible` | string[] | 0..N | Opinion publique, élites, diaspora... |

### 2.3 Infrastructure (Diamond : Infrastructure)

#### `Canal` (`canaux_diffusion[]`)

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `canal` | string | 0..1 | Catégorie (réseau social, presse, messagerie...) |
| `plateforme` | string | 0..1 | Plateforme précise (Telegram, X...) |
| `ampleur` | enum `ampleur` | 0..1 | Niveau, voir §5 |
| `ampleur_precisions` | string | 0..1 | Volumes, vues, texte libre. Un chiffre d'audience gagne à devenir un observable de `portee` |

#### `Relais` (`relais[]`)

Entité intermédiaire qui amplifie ou relaie les contenus, avec ou sans conscience de son rôle. Deux axes indépendants : ce qu'est le relais (`nature`) et comment il est opéré (`automatisation`).

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `nom` | string | 0..1 | Nom de l'entité |
| `nature` | enum `relais_nature` | 0..1 | Média, influenceur, ONG... voir §5 |
| `automatisation` | enum `relais_automatisation` | 0..1 | Humain, automatisé, mixte, inconnu |
| `type` | string | 0..1 | **Dérivé** : copie de `automatisation` écrite à l'export, pour les outils v2. Dans une fiche 1.0, ancien champ libre repris à l'import (§6) |
| `role` | string | 0..1 | Rôle dans la chaîne de diffusion |
| `degre_conscience` | enum | 0..1 | Voir §5 |
| `precisions` | string | 0..1 | Texte libre, unique pour le relais |

### 2.4 Capacité (Diamond : Capability)

#### `Technique DISARM` (`techniques[]`)

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `code_disarm` | string | 0..1 | Code DISARM (ex : `T0023`) |
| `nom` | string | 0..1 | Nom de la technique |
| `description` | text | 0..1 | Contextualisation pour cet incident |

#### `Technique DIMA` (`techniques_dima[]`)

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `code_dima` | string | 0..1 | Code DIMA (ex : `TE0331`) |
| `nom` | string | 0..1 | Nom du biais ou mécanisme cognitif |
| `description` | text | 0..1 | Contextualisation pour cet incident |

#### `FormatContenu` (`formats_contenu[]`)

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `format` | enum `format` | 0..1 | Voir §5 |
| `format_precisions` | string | 0..1 | Texte libre |
| `genere_ia` | boolean | 0..1 | Contenu généré ou modifié par IA (deepfake, image ou texte synthétique). Une vidéo générée reste une `video` |
| `langue` | string | 0..1 | Langue principale du contenu |

---

## 3. Dimension narrative et psychologique

### 3.1 `Objectif` (`objectifs[]`)

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `type_objectif` | enum `type_objectif` | 0..1 | **Effet** recherché, voir §5. Un moyen (désinformer, saturer de faux comptes) se documente dans les techniques |
| `type_objectif_precisions` | string | 0..1 | Texte libre |
| `description` | text | 0..1 | Formulation précise |

### 3.2 `Narratif` (`narratifs[]`)

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `description` | text | 0..1 | Énoncé du narratif |
| `veracite` | enum `veracite` | 0..1 | Voir §5 |
| `veracite_precisions` | string | 0..1 | Texte libre |
| `confiance` | enum `confiance` | 0..1 | Solidité de l'établissement du narratif et de sa véracité |
| `themes` | string[] | 0..N | Thèmes associés |

### 3.3 `LevierPsychologique` (`leviers_psychologiques[]`)

Valeur parmi une liste fixe (principes de Cialdini) :

- Principe de réciprocité
- Principe d'autorité
- Principe de cohérence
- Principe de la preuve sociale
- Principe de rareté
- Principe de sympathie

---

## 4. Entités transverses

### 4.1 `Evenement` (`evenements[]`)

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `date` | date ISO | 1 | Date de l'événement |
| `type` | enum | 1 | `custom` (événement), `analysis`, `attribution`, `reponse`. `start` et `end` sont générés à l'affichage depuis `date_debut` / `date_fin`, ils ne sont pas saisis |
| `label` | string | 0..1 | Titre court |
| `description` | text | 0..1 | Détail |
| `nature_reponse` | enum `nature_reponse` | 0..1 | Pour `type: reponse` uniquement, voir §5 |
| `acteur_reponse` | string | 0..1 | Pour `type: reponse` : qui répond (média, ministère, plateforme...) |
| `source_url` | URL http(s) | 0..1 | Pour `type: reponse` : source de la réponse |

Une réponse (démenti, fact-check, déclaration, modération, sanction) est un fait daté. Son effet éventuel sur le public relève de `efficacite`, pas de l'événement : une réponse institutionnelle ne vaut pas effet sur le public.

### 4.2 `Document` (`documents[]`)

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `titre` | string | 0..1 | Titre de la source |
| `type` | enum `document_type` | 0..1 | Voir §5 |
| `type_precisions` | string | 0..1 | Texte libre |
| `url` | URL http(s) | 0..1 | Lien vers la source |

### 4.3 `DeceptionDetection`

Objet unique embarqué dans `Incident`. Correspond à la technique SAT 9.1 (Détection de tromperie). Les quatre indicateurs sont les cadres d'évaluation MOM / POP / MOSES / EVE (Pherson et Heuer).

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `mom_checked` | boolean | 1 | MOM, Motive, Opportunity and Means : l'acteur avait-il les moyens et l'intention de tromper ? |
| `pop_checked` | boolean | 1 | POP, Past Opposition Practices : l'acteur a-t-il déjà eu recours à la tromperie ? |
| `moses_checked` | boolean | 1 | MOSES, Manipulability of Sources : les sources peuvent-elles être manipulées, compromises, contraintes ou utilisées comme vecteurs de tromperie ? |
| `eve_checked` | boolean | 1 | EVE, Evaluation of Evidence : les preuves sont-elles trop parfaites, trop cohérentes, ou arrivent-elles trop opportunément ? |
| `notes` | text | 0..1 | Observations, indices identifiés, niveau de confiance |

### 4.4 `TechniqueSATAppliquee` (`structured_techniques_applied[]`)

Enregistre une technique SAT (issue du référentiel `SAT_TECHNIQUES`) appliquée à ce dossier.

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `technique_id` | string | 0..1 | Identifiant SAT (ex : `4.3`), référence vers `SAT_TECHNIQUES[].id` |
| `technique_nom` | string | 1 | Nom de la technique (ex : `Analyse des hypothèses concurrentes (ACH)`) |
| `date_application` | date ISO | 0..1 | Date à laquelle la technique a été appliquée |
| `analyste` | string | 0..1 | Identifiant ou initiales de l'analyste |
| `notes` | text | 0..1 | Résultats, conclusions, niveau de confiance |

### 4.5 `Evaluation` (`portee`, `efficacite`)

- **Portée** = exposition : qui a vu, où, avec quel engagement. Se documente.
- **Efficacité** = effet : cru, changé d'avis, agi. Rarement mesurée, le plus souvent déduite.
- Le **périmètre** distingue la contribution propre de l'opération du phénomène général dans lequel elle s'inscrit.

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `niveau` | enum `niveau_evaluation` | 0..1 | Voir §5 |
| `perimetre` | enum `perimetre` | 0..1 | Voir §5 |
| `confiance` | enum `confiance` | 0..1 | Voir §5 |
| `synthese` | text | 0..1 | Justification du niveau retenu |
| `observables` | Observable[] | 0..50 | Faits qui fondent le niveau |
| `origine` | enum `origine_evaluation` | 0..1 | **`efficacite` seulement** : observation directe ou analyse externe |
| `origine_source` | string | 0..1 | **`efficacite` seulement** : quelle analyse externe (auteur, date) |

#### `Observable`

| Attribut | Type | Cardinalité | Description |
|---|---|---|---|
| `description` | string | 0..1 | Ce qui est observé, avec le chiffre s'il existe |
| `date` | date ISO | 0..1 | Date de l'observation |
| `nature` | enum `nature_observable` | 0..1 | Voir §5. Attendu |
| `source_url` | URL http(s) | 0..1 | Attendu |
| `source_titre` | string | 0..1 | Média, rapport, auteur |

Un observable sans `nature` ou sans `source_url` est signalé « incomplet » par l'outil de saisie, sans bloquer l'export. Un `niveau` renseigné sans observable ni synthèse est signalé aussi. Ce garde-fou vaut pour une saisie humaine comme pour une saisie assistée par LLM : une évaluation sans source ni nature n'est pas fondée.

Règles de lecture :
- l'engagement ne vaut pas croyance ;
- une réponse institutionnelle ne vaut pas effet sur le public.

---

## 5. Énumérations

Les valeurs sont les clés exportées dans le JSON. Les libellés sont ceux affichés par les outils.

### `statut`

| Valeur | Libellé | Signification |
|---|---|---|
| `en_cours` | En cours | Analyse en cours |
| `terminee` | Terminée | Analyse finalisée |
| `en_analyse` | En analyse | Dossier reçu, analyse non démarrée |
| `archivee` | Archivée | Dossier clos et archivé |

### `niveau_attribution`

| Valeur | Couleur | Signification |
|---|---|---|
| `Confirmé` | `#C8372D` | Attribution établie |
| `Probable` | `#D97706` | Attribution très vraisemblable |
| `Possible` | `#CA8A04` | Attribution envisagée |
| `Non attribué` | `#9CA3AF` | Aucune attribution disponible |

### `confiance`

Échelle unique pour toute la fiche. Elle qualifie la solidité de la preuve, pas la probabilité.

| Valeur | Libellé | Signification |
|---|---|---|
| `elevee` | Élevée | Plusieurs sources indépendantes et de qualité, peu d'hypothèses alternatives crédibles |
| `moderee` | Modérée | Sources crédibles mais peu nombreuses, ou corroboration partielle |
| `faible` | Faible | Source unique, fragmentaire ou de fiabilité incertaine |

### `attaquant_type`

`etat` (État), `groupe_mercenaire` (Groupe mercenaire), `organisation_privee` (Organisation privée), `groupe_activiste` (Groupe activiste), `individu` (Individu), `inconnu` (Inconnu).

### `ampleur`

`faible`, `moderee` (Modérée), `forte`, `massive`.

### `relais_nature`

`media` (Média), `influenceur`, `individu`, `ONG`, `parti`, `institution`, `reseau_comptes` (Réseau de comptes), `plateforme`, `autre`.

### `relais_automatisation`

`humain`, `automatise` (Automatisé), `mixte`, `inconnu`.

### `degre_conscience`

| Valeur | Signification |
|---|---|
| `conscient` | Le relais sait qu'il participe à l'opération |
| `semi_conscient` | Le relais est partiellement informé |
| `inconscient` | Le relais agit de bonne foi |

### `format`

`texte`, `image`, `video` (Vidéo), `audio`, `article`, `site_web` (Site web), `publication_reseau` (Publication sur un réseau), `courriel`, `document`, `autre`.

### `type_objectif`

Effets recherchés : `polarisation`, `delegitimation` (Délégitimation), `soutien_politique` (Soutien politique), `perturbation`, `dissuader`, `recruter`, `saturer`, `autre`.

### `veracite`

`faux`, `partiellement_vrai` (Partiellement vrai), `trompeur`, `hors_contexte` (Hors contexte), `vrai`, `indetermine` (Indéterminé).

### `document_type`

`rapport`, `article`, `analyse`, `video` (Vidéo), `archive`, `capture` (Capture d'écran), `autre`.

### `nature_reponse`

`dementi` (Démenti), `fact_check` (Fact-check), `declaration_officielle` (Déclaration officielle), `moderation_plateforme` (Modération de plateforme), `sanction`, `autre`.

### `niveau_evaluation`

`faible`, `moyenne`, `elevee` (Élevée), `tres_elevee` (Très élevée), `indeterminee` (Indéterminée).

### `perimetre`

`operation` (contribution propre de l'opération), `phenomene` (phénomène général).

### `origine_evaluation`

`observation_directe` (Observation directe), `analyse_externe` (Analyse externe).

### `nature_observable`

| Valeur | Signification |
|---|---|
| `substrat` | Conditions préexistantes que l'opération exploite (inquiétude, crise, rumeur déjà présente) |
| `attention` | Exposition : vues, audience, nombre de comptes ou de pays touchés |
| `engagement` | Interactions : partages, commentaires, réactions |
| `croyance` | Adhésion mesurée : sondage, étude d'opinion |
| `comportement` | Action observée : rassemblement, boycott, signalement, vote |
| `reponse` | Réaction d'une institution, d'un média ou d'une plateforme : démenti, fact-check, modération |

---

## 6. Reprise à l'import

Les outils v3 lisent les fiches 1.0 et les fiches écrites à la main. Pour chaque champ énuméré, dans l'ordre :

1. valeur exacte de la liste : gardée ;
2. même valeur après passage en minuscules, suppression des accents, tiret ou espace remplacé par `_` : normalisée (`Semi-conscient` -> `semi_conscient`, `hors-contexte` -> `hors_contexte`) ;
3. correspondance connue : convertie ;
4. sinon : champ vide, valeur d'origine recopiée dans le champ de précisions voisin.

Correspondances connues (la valeur d'origine est gardée en précisions quand la conversion perd de l'information) :

| Champ | Valeur d'origine | Devient | Origine gardée |
|---|---|---|---|
| `attaquants[].type` | `acteur_prive` | `organisation_privee` | non |
| `attaquants[].type` | `groupe_para` | `groupe_mercenaire` | oui |
| `objectifs[].type_objectif` | `desinformation` | `autre` | oui |
| `narratifs[].veracite` | `vrai-detourne` | `trompeur` | oui |
| `documents[].type` | `screenshot` | `capture` | non |
| `relais[].automatisation` | `bot`, `bots` | `automatise` | non |
| `formats_contenu[].format` | `deepfake`, `deepfake video` | `video` + `genere_ia` | oui |
| `formats_contenu[].format` | `deepfake audio` | `audio` + `genere_ia` | oui |
| `formats_contenu[].format` | `meme`, `mème` | `image` | oui |
| `niveau` (portée, efficacité) | `moderee` | `moyenne` | non |

Cas particuliers :
- **Relais d'une fiche 1.0** (ni `nature` ni `automatisation`) : l'ancien `type` libre va sur l'axe qu'il désigne exactement après normalisation (`Humain` -> `automatisation: humain`, `media` -> `nature: media`), sinon dans `precisions`. L'outil ne devine pas.
- **Portée et efficacité d'une fiche 1.0** (pas de bloc) : si `portee_estimee` / `efficacite_estimee` est exactement un niveau, il devient `niveau` ; sinon le texte devient `synthese`. Une phrase qui commence par un niveau (« Faible. ... ») va entière dans la synthèse.
- **Valeurs invalides sans champ de précisions dédié** : dans un bloc d'évaluation, ajoutées à `synthese` ; nature d'observable ou de réponse, ajoutée à la description entre crochets ; confiance d'un narratif, ajoutée à `veracite_precisions`.
- Les URL qui ne sont pas en `http(s)` sont vidées.

La saisie affiche un rapport d'import : nombre de valeurs reprises, détail champ par champ, nombre de champs conservés sans être gérés.

---

## 7. Champs inconnus

Un champ que l'outil ne connaît pas, à la racine ou dans un élément de liste, est **conservé** et réexporté tel quel par la saisie. Il n'est ni affiché ni modifiable dans le formulaire. Il passe par le même nettoyage que le reste :

- chaînes nettoyées (HTML retiré) et tronquées à 10 000 caractères ;
- clés `__proto__`, `constructor`, `prototype` rejetées ;
- 4 niveaux d'imbrication au plus (au-delà : `null`) ;
- 200 clés et 100 éléments de liste au plus par objet.

La visualisation, qui n'exporte pas de fiche, ne les conserve pas.

---

## 8. Schéma de relations

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
  ├── [0..N] Evenement                  (dont type « reponse »)
  ├── [0..1] Evaluation « portee »
  │              └── [0..50] Observable
  ├── [0..1] Evaluation « efficacite »
  │              └── [0..50] Observable
  ├── [0..1] DeceptionDetection         → SAT 9.1
  └── [0..N] TechniqueSATAppliquee      → SAT_TECHNIQUES[]
```

---

## 9. Exemple JSON 1.1

```json
{
  "version_schema": "1.1",
  "code_operation": "incident-2025-exemple-france-xx",
  "date_creation": "2025-01-15",
  "date_derniere_maj": "2025-01-15",
  "statut": "en_cours",
  "date_debut": "2024-09-01",
  "date_fin": "",
  "portee": {
    "niveau": "faible",
    "perimetre": "operation",
    "confiance": "moderee",
    "synthese": "Exposition organique limitée, relais principal peu repris.",
    "observables": [
      {
        "description": "105 partages sur le relais principal",
        "date": "2024-09-05",
        "nature": "engagement",
        "source_url": "https://example.com/verification",
        "source_titre": "Service de vérification"
      }
    ]
  },
  "efficacite": {
    "niveau": "indeterminee",
    "perimetre": "phenomene",
    "confiance": "faible",
    "synthese": "Aucune mesure de l'effet sur le public.",
    "observables": [],
    "origine": "analyse_externe",
    "origine_source": "Rapport d'un observatoire, novembre 2024"
  },
  "portee_estimee": "faible",
  "efficacite_estimee": "indeterminee",
  "commentaires": "",
  "mots_cles": ["election", "desinformation"],
  "attaquants": [
    {
      "nom": "Acteur X",
      "type": "etat",
      "type_precisions": "",
      "pays_origine": "Pays X",
      "niveau_attribution": "Probable",
      "confiance": "moderee",
      "confiance_justification": "Recoupement de deux rapports indépendants",
      "role_dans_operation": "Commanditaire",
      "description": ""
    }
  ],
  "cibles": [
    { "pays": "France", "region": "", "description_libre": "Opinion publique générale", "type_cible": ["opinion_publique"] }
  ],
  "canaux_diffusion": [
    { "canal": "Réseau social", "plateforme": "Telegram", "ampleur": "forte", "ampleur_precisions": "" }
  ],
  "relais": [
    {
      "nom": "Blog XYZ",
      "nature": "media",
      "automatisation": "humain",
      "type": "humain",
      "role": "Amplificateur",
      "degre_conscience": "inconscient",
      "precisions": ""
    }
  ],
  "techniques": [
    { "code_disarm": "T0023", "nom": "Distort Facts", "description": "" }
  ],
  "techniques_dima": [
    { "code_dima": "TE0332", "nom": "Effet de simple exposition", "description": "" }
  ],
  "formats_contenu": [
    { "format": "video", "format_precisions": "", "genere_ia": true, "langue": "fr" }
  ],
  "objectifs": [
    { "type_objectif": "polarisation", "type_objectif_precisions": "", "description": "Exacerber les tensions autour d'un sujet de société" }
  ],
  "narratifs": [
    {
      "description": "Les élites trahissent le peuple",
      "veracite": "faux",
      "veracite_precisions": "",
      "confiance": "elevee",
      "themes": ["souverainisme"]
    }
  ],
  "leviers_psychologiques": ["Principe de la preuve sociale"],
  "documents": [
    { "titre": "Rapport d'analyse", "type": "rapport", "type_precisions": "", "url": "https://example.com/rapport" }
  ],
  "evenements": [
    { "date": "2024-09-03", "type": "custom", "label": "Pic de diffusion", "description": "Volume x10 en 48 h", "nature_reponse": "", "acteur_reponse": "", "source_url": "" },
    { "date": "2024-09-06", "type": "reponse", "label": "Démenti du média usurpé", "description": "", "nature_reponse": "dementi", "acteur_reponse": "Média usurpé", "source_url": "https://example.com/dementi" }
  ],
  "deception_detection": {
    "mom_checked": true,
    "pop_checked": true,
    "moses_checked": false,
    "eve_checked": false,
    "notes": "Historique de campagnes similaires documenté. Moyens techniques confirmés."
  },
  "structured_techniques_applied": [
    {
      "technique_id": "4.3",
      "technique_nom": "Analyse des hypothèses concurrentes (ACH)",
      "date_application": "2025-01-10",
      "analyste": "AB",
      "notes": "Trois hypothèses évaluées. Attribution étatique retenue au niveau Probable."
    }
  ]
}
```
