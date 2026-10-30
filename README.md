# Outils de documentation des opérations d'influence
<!-- README v2 : 2026-10-30, outils v3 et schéma 1.1 -->

Deux fichiers HTML autonomes pour documenter, analyser et comparer des opérations d'influence numérique étudiées en sources ouvertes.

Pas d'installation, pas de serveur, pas de compte. On ouvre le fichier HTML dans un navigateur récent. Les données restent en mémoire dans l'onglet : rien n'est envoyé ni stocké côté navigateur, et fermer l'onglet sans exporter fait perdre la saisie.

---

## Cadres méthodologiques

- **Diamond Model** : adversaire, capacités, infrastructure, victime, avec un niveau d'attribution (confirmé, probable, possible, non attribué).
- **DISARM** : techniques offensives de manipulation de l'information, classées par tactique (391 techniques, avec description en français).
- **DIMA** : biais cognitifs exploités, regroupés en 4 familles (Détecter, Informer, Mémoriser, Agir) (56 techniques).
- **SAT** : techniques analytiques structurées de Pherson et Heuer (*Structured Analytic Techniques for Intelligence Analysis*, 4e éd., 2021), 48 techniques, plus une grille de détection de tromperie (MOM, POP, MOSES, EVE).
- **Cialdini** : 6 leviers psychologiques.

---

## Les outils

### `saisie-fiche-vX.html` : saisie d'une fiche

Formulaire de documentation d'une opération :

- attaquants et cibles, niveau d'attribution et confiance dans l'attribution (Diamond Model) ;
- techniques DISARM et DIMA mobilisées ;
- narratifs (véracité, confiance), objectifs, canaux de diffusion, relais (nature et automatisation), formats de contenu ;
- portée (exposition) et efficacité (effet obtenu), chacune avec un niveau, une confiance, une synthèse et des observables datés et sourcés ;
- chronologie : événements, analyses, attributions, et réponses (démenti, fact-check, déclaration, modération, sanction) ;
- techniques SAT appliquées et détection de tromperie ;
- sources et documents.

Les champs à valeurs prédéfinies sont des listes fermées, chacune accompagnée d'un champ de précisions libre. Les fiches restent ainsi comparables entre elles. Un observable sans nature ou sans source, ou un niveau donné sans aucun appui, est signalé à l'écran sans bloquer l'export.

**Import**
- une fiche JSON produite par l'outil, y compris par une version précédente. Les valeurs hors liste sont reprises sans perte (converties, ou déplacées dans le champ de précisions) et un rapport d'import les détaille ;
- une analyse exportée par l'[outil DIMA](https://m82-project.github.io/DIMA/) : ses techniques sont fusionnées dans les techniques cognitives de la fiche, les doublons ignorés.

**Export**
- **JSON** : la fiche, à réimporter ou à charger dans la visualisation ;
- **SVG** : le schéma Diamond Model ;
- **MediaWiki** : la fiche mise en forme en wikitexte (fichier `.txt`).

### `visualisation-fiche-vX.html` : visualisation comparative

- charge une ou plusieurs fiches JSON, par glisser-déposer ou depuis une URL Git (voir « Réseau ») ;
- affiche les opérations sur une chronologie commune, une ligne par opération, avec jalons et filtres (pays, type d'attaquant, technique, statut, réponses affichées ou masquées) ;
- vue détaillée d'une opération : Diamond Model, matrice des techniques, narratifs, portée et efficacité avec leurs observables, sources ;
- interface en français ou en anglais ;
- les bulles de la chronologie se déplacent à la main ; leurs positions peuvent être exportées en JSON (un fichier par opération) et réimportées.

---

## Installation

1. Sur la page du dépôt, téléchargez le dossier `tools/` **complet**, ou l'archive `tools.zip`.
2. Ouvrez le fichier HTML voulu dans le navigateur.

Les fichiers HTML chargent React et les polices depuis les sous-dossiers `vendor/` et `fonts/` placés à côté d'eux. Un fichier HTML copié seul, sans ces deux dossiers, ne fonctionne pas.

Le dossier `tools/` contient aussi les versions précédentes des outils, conservées pour relire les fiches anciennes. Utilisez le numéro de version le plus élevé.

---

## Format des données

Une fiche est un fichier **JSON** : lisible à la main, versionnable (diff ligne à ligne dans Git), sans format propriétaire.

Le format suit le **schéma 1.1** :
- [`annexes/schema-incident.md`](annexes/schema-incident.md) décrit les champs, les entités, les listes de valeurs et les règles de reprise des fiches anciennes ;
- [`annexes/schema-incident-1.1.json`](annexes/schema-incident-1.1.json) est le JSON Schema (draft 2020-12) correspondant, pour valider une fiche avec un outil tiers.

Les champs ajoutés par le schéma 1.1 sont optionnels : une fiche produite par une version précédente reste lisible.

Des fiches documentées avec ces outils sont consultables sur [diod.m82-project.org](https://diod.m82-project.org).

---

## Réseau

- **Saisie** : aucune connexion réseau. La politique de sécurité du fichier l'interdit (`connect-src 'none'`).
- **Visualisation** : aucune connexion, sauf si vous utilisez l'import par URL Git. Il fait alors une seule requête de lecture (GET), en HTTPS, vers `raw.githubusercontent.com` ou `gitlab.com` uniquement, pour un fichier `.json`, avec un délai maximal de 10 secondes. Tout autre domaine est refusé.

Aucune télémétrie, aucun appel à un service tiers, aucune police ou bibliothèque chargée depuis Internet.

---

## Sécurité

- **Content Security Policy** stricte : pas de script ni de ressource externe.
- **Contrôle des fichiers importés** : taille limitée à 10 Mo, structure vérifiée, chaînes nettoyées et tronquées, URL filtrées (http et https seulement), clés dangereuses rejetées. Les valeurs des listes fermées sont vérifiées et reprises si besoin. L'analyse elle-même (justesse des faits, qualité des sources) reste du ressort de l'analyste.
- **Champs inconnus** : un champ ajouté à la main dans le JSON est conservé par l'outil de saisie et réexporté tel quel, après le même nettoyage. Il n'apparaît pas dans le formulaire.

---

## Licence

- **Outils** (`tools/`) : [PolyForm Noncommercial 1.0.0](https://polyformproject.org/licenses/noncommercial/1.0.0/), voir le fichier `LICENSE`. Libre pour tout usage non commercial, y compris en situation réelle : particuliers, associations, établissements d'enseignement et de recherche publics, administrations, quel que soit leur financement. Usage commercial : contacter l'auteur (mathieu.ferasse@gmail.com).
- **Format de données** (`annexes/schema-incident.md`, `annexes/schema-incident-1.1.json`) : [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.fr), voir `annexes/LICENSE-schema`. Reprise libre, y compris commerciale, à condition de citer la source : tout outil peut lire ou produire des fiches compatibles.
