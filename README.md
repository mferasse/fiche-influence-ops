# Outils de documentation des opérations d'influence
<!-- README v0 — 2026-05-08 -->

Outils HTML autonomes pour documenter, analyser et visualiser des opérations d'influence numérique ayant fait l'objet d'une analyse en sources ouvertes.

Aucune installation. Aucune dépendance réseau. Aucun backend. Ouvrez le fichier HTML dans un navigateur moderne et c'est opérationnel.

---

## Contexte

Ces outils s'appuient sur des méthodologies analytiques reconnues :

- **Diamond Model** — attribution et caractérisation des acteurs de la menace
- **DISARM Framework** — classification des techniques offensives de manipulation de l'information
- **DIMA** — analyse des biais cognitifs exploités

---

## Outils

### `saisie-fiche-vX.html` — Saisie d'incident

Formulaire de documentation d'une opération d'influence. Permet de renseigner :

- Identité et niveau d'attribution des attaquants et des cibles (Diamond Model)
- Techniques DISARM mobilisées (~391 techniques référencées)
- Techniques cognitives DIMA (~40 biais)
- Narratifs, jalons chronologiques, événements clés

Génère un export **JSON** (format fiche) et un export graphique **Diamond Model en SVG**.

---

### `visualisation-fiche-vX.html` — Visualisation comparative

Outil de visualisation de plusieurs fiches en parallèle. Permet de :

- Importer un ou plusieurs fichiers JSON Fiche
- Afficher une timeline interactive multi-opérations en swimlanes
- Importer directement depuis une URL Git (GitHub / GitLab, fichiers `.json` uniquement)

---

## Installation

> Les outils sont des fichiers HTML autonomes. **Aucun clone du dépôt n'est nécessaire.**

### Méthode recommandée — Téléchargement du répertoire `tools`

1. Rendez-vous sur la page du dépôt GitHub
2. Naviguez dans le dossier `tools/`
3. Téléchargez le dossier complet via **Code > Download ZIP** ou via un outil dédié (voir ci-dessous)
4. Décompressez et ouvrez le fichier HTML dans votre navigateur

**Téléchargement ciblé du dossier `tools/` uniquement (sans cloner le dépôt) :**

```bash
# Avec git sparse-checkout (Git 2.25+)
git clone --filter=blob:none --sparse https://github.com/VOTRE_ORG/VOTRE_DEPOT.git
cd VOTRE_DEPOT
git sparse-checkout set tools
```

Ou, avec [DownGit](https://downgit.github.io/) ou [GitZip](https://kinolien.github.io/gitzip/), collez l'URL du dossier `tools/` pour en télécharger une archive directe.

---

## Utilisation

Ouvrez simplement le fichier HTML dans un navigateur moderne :

```bash
# Linux
xdg-open tools/saisie-fiche-vX.html
xdg-open tools/visualisation-fiche-vX.html

# macOS
open tools/saisie-fiche-vX.html
open tools/visualisation-fiche-vX.html

# Windows
start tools\saisie-fiche-vX.html
```

Navigateurs supportés : **Chrome 118+ / Firefox 119+ / Edge 118+**

> **Firefox — import depuis une URL Git :** si le bouton d'import Git ne répond pas en contexte `file://`, lancez un serveur local minimal :
>
> ```bash
> python3 -m http.server 8000
> # puis ouvrir : http://localhost:8000/tools/visualisation-fiche-vX.html
> ```

---

## Format des données

Chaque fiche documentée est exportée au format **JSON**. Ce format est :

- **lisible à la main** — pas de binaire, pas de propriétaire
- **versionnable** — adapté au suivi Git (diff ligne à ligne)
- **interopérable** — importable directement dans `visualisation-fiche-vX.html`

---

## Sécurité

Les outils appliquent une politique de sécurité stricte :

- **Content Security Policy (CSP)** — pas d'exécution de code externe
- **Validation de schéma** — tout JSON importé est contrôlé avant traitement
- **Pas de télémétrie** — aucune donnée ne quitte le navigateur
- **Pas de dépendance réseau à l'exécution** — fonctionnement intégralement hors ligne

---

## Licence

**Business Source License 1.1** — voir fichier `LICENSE`.

Usage non commercial et non productif : libre.
Usage commercial ou productif : contacter le licencié.
