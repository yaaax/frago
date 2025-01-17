# Génération de rapports de synthèse pour le suivi et l’amélioration de l’accessibilité web.

> FRAGO : French governmental Reports for Accessibility compliance with goHugo

![Logo de Frago](/images/frago.png)


## Présentation

Le thème Frago a pour objectif d’aider à la génération de synthèses pour la documentation du suivi d’amélioration des démarches en ligne du gouvernement français.

Le thème a été conçu pour être multi-projets. Pourquoi ? Parce que dans le cadre d’un défi de mise en conformité, l’équipe peut être amenée à travailler sur des sujets connexes, des interfaces intermédiaires/liées sur lesquelles il est nécessaire de fournir un audit en particulier… de plus le projet peut aider à suivre aussi plusieurs défis en parallèle.

> Attention, goHugo est très sensible à l’architecture des contenus. Si les contenus sont absents ou présentent des erreurs le site peut planter. Si l’architecture du thème change, de même, il n’est plus possible d’utiliser les dernières versions du thème (sans modifier l’architecture de contenu).

## Démarrage (Configuration simplifiée)

### Architecture de contenu

```
.
├── config.toml
└── content
    ├── _index.md // Page d’accueil
    └── audits
         ├── projet1
         │     ├── index.md // Pour lister les pages sur l’accueil et avoir la page intermédiaire qui liste tous les rapports :: ajouter une entête
         │     └── accessibility
         │          ├── 2020-10-15.csv
         │          └── 2020-11-15.csv
         └── projet2
                ├── index.md // Pour lister les pages sur l’accueil et avoir la page intermédiaire qui liste tous les rapports :: ajouter une entête
                └── accessibility
                    ├── 2020-10-15.csv
                    └── 2020-11-15.csv
```

### Configuration

#### Ajouter le fichier : `config.toml`

```toml
[module]
  [[module.imports]]
    path = "github.com/disic/frago"

title = "Mon administration"
theme = "frago"

[params]
  organisation = "Le nom complet de mon administration"
```

#### Appeler le thème Hugo (avec le système de module `Go`)

##### Créer le fichier `go.mod` (commande dans le terminal)

```bash
hugo mod init github.com/disic/frago
```

Modifier le contenu du fichier `go.mod` (Ajouter https://) :  
`module github.com/DISIC/frago => module https://github.com/DISIC/frago`

##### Créer le fichier `go.sum`  (commande dans le terminal)

```bash
hugo mod get -u https://github.com/disic/frago
```

#### Appeler le thème Hugo (avec un submodule)

```bash
git submodule add https://github.com/disic/frago.git/ themes/frago && git submodule update --init --recursive
```

#### Génération du site (mise en ligne)

Ligne de commande pour construire les pages du site.

```bash
hugo --buildFuture --gc --minify
```

  * `--buildFuture` permet de publier des éléments avec une date future dans la partie [phase](#phases) (optionnel).
  * `--gc` permet de remettre à jour le répertoire de génération du code (au cas ou du code d’une précédente génération serait encore présent).
  * `--minify` permet de compresser le code `HTML` en ligne.

##### Héberger avec Github

Consulter la page [Host on GitHub](https://gohugo.io/hosting-and-deployment/hosting-on-github/) pour plus d‘informations.

Github utilise son système interne GitHub Action. Un exemple de fichier de configuration est disponible : [gh-pages.yml](exampleSite/.github/workflows/gh-pages.yml).

##### Héberger avec Gitlab

Consulter la page [Host on Gitlab](https://gohugo.io/hosting-and-deployment/hosting-on-gitlab/) pour plus d‘informations

Github utilise son système interne Gitlab CI. Un exemple de fichier de configuration est disponible : [.gitlab-ci.yml](exampleSite/.gitlab-ci.yml).

### Audits

#### Accessibilité

Le thème interprète un fichier d’audit `csv` avec une structure fixe. Les intitulés de colonnes doivent respecter un nommage précis (Thématiques, Critères, Tests,… puis le titre de chaque page).

Le titre d’une page peut être suivi de l’URL de la page ; titre et URL séparés par le caractère `|`.

**Exemple d’un tableau avec uniquement la prise en compte des critères**

| Thématiques | Critères | Accueil।https://monsite.fr |
| :---------: | :------: | :------------------------: |
|      1      |    1     |              nc            |
|      1      |    2     |              c             |
|      1      |    3     |              na            |
|      1      |    4     |              nt            |
|      …      |    …     |              …             |
|      13     |    12    |              nc            |

**Liste des fichiers exemples à télécharger**

  * [Grille critères RGAA 4 vierge](/static/grille-criteres-rgaa.csv)
  * [Grille tests RGAA 4.0 vierge](/static/grille-tests-rgaa4.0.csv)
  * [Grille tests RGAA 4.1 vierge](/static/grille-tests-rgaa4.1.csv)

Dans chacun des ces fichiers figurent des numéros qui correspondent aux notions d’audit rapide (25 critères), intermédiaire (50 critères), complémentaire (81 critères).  
Exemple, Pour un audit rapide, ne traiter que les cases avec le numéro **25**.

### API

La configuration de l’API est contenu dans le fichier `config.toml` du thème. Il existe une configuration par défaut qui peut-être surchargée en fonction de la structure de fichier. Pour la structure simplifié, il n’y a rien à ajouter.

L‘idée est d’éviter de créer des pages uniquement pour avoir une URL disponible lors du build du site (seuls défauts, une export de page peut-être vide si l’audit correspondant n’existe pas ; et on perd les `pretty` url pour les exports `HTML`).

Cette API permet d’obtenir des fichiers `json` de type :

 * https://monurl.com/index.json
 * https://monurl.com/audits/monprojet/index.json
 * https://monurl.com/audits/monprojet/accessibility.json
 * https://monurl.com/audits/monprojet/quality.json
 * https://monurl.com/audits/monprojet/performance.json

Mais aussi des affichages de pages de type :

 * https://monurl.com/audits/monprojet/accessibility.html
 * https://monurl.com/audits/monprojet/quality.html
 * https://monurl.com/audits/monprojet/performance.html
 * https://monurl.com/audits/monprojet/declaration.html
 * https://monurl.com/audits/monprojet/declaration.txt

**Exemple de configuration de l’API (Voir la Documentation goHugo sur les Output Format).**

```
[outputs]
 home    = ["HTML","JSON"]
 page    = ["HTML","JSON","declaration","declarationpage"]
```

## Configuration détaillée

### Architecture de contenu

Les différents types de contenus servent à la publication d’audits, l’analyse de résultats ou la présentation de la démarche UX. L’objectif est de synthétiser de l’information technique pour des personnes éloignées du développement de sites web.

#### Arborescence

##### Architecture simplifiée

```
.
├── config.toml
└── content
    ├── _index.md // page d’accueil du site :: ⚠️ Ajouter `type: projects` dans l'entête du fichier en cas de mono projet => va afficher directement la page de synthèse de tous les audits
    ├── audits // Type de contenu audits, les projets doivent être dans un répertoire avec le nom audits
    │     ├── projet1
    │     │      ├── index.md // ⚠️  Page intermédiaire qui liste les sous rapports (accessibilité, performance…)
    │     │      ├── accessibility
    │     │      │   ├── 2020-10-15.csv
    │     │      │   └── 2020-11-15.csv
    │     │      ├── lighthouse
    │     │      │   ├── 2020-10-15.csv
    │     │      │   └── 2020-11-15.csv
    │     │      └── quality
    │     │          ├── 2020-10-15.csv
    │     │          └── 2020-11-15.csv
    │     │
    │     └── projet2
    │          ├── index.md
    │          ├── accessibility
    │          │   ├── 2020-10-15.csv
    │          │   └── 2020-11-15.csv
    │          ├── lighthouse
    │          │   ├── 2020-10-15.csv
    │          │   └── 2020-11-15.csv
    │          └── quality
    │              ├── 2020-10-15.csv
    │              └── 2020-11-15.csv
    ┋
    ┋ ⚠️ Autre choix d’arborescence avec un seul projet ⚠️
    ┋
    └── audits // Les pages pour afficher les audits (accessibilité, qualité, performance…)
          ├── index.md
          ├── accessibility
          │   ├── 2020-10-15.csv
          │   └── 2020-11-15.csv
          ├── lighthouse
          │   ├── 2020-10-15.csv
          │   └── 2020-11-15.csv
          └── quality
              ├── 2020-10-15.csv
              └── 2020-11-15.csv
```

##### Architecture complète (⚠️ Ancienne présente une architecture alternative, elle doit évoluer pour correspondre à tous les types de contenus)

> Attention dans ce cas de figure: ⚠️ Le nom des pages projets `content/projects/projets1.md`, des répertoires projets `static/projet1/` et des répertoires des pages d’audits `content/audits/projets1/accessibility.md` doivent bien comporter la même clef ou slug, ici : `projet1`. ⚠️   
> Un script devrait permettre de créer ces fichiers automatiquement à partir du moment un répertoire dans `static` est créé, mais il n’existe pas encore. La création doit se faire manuellement.

```
.
├── config.toml
├── content
│   ├── _index.md // page d’accueil du site :: ⚠️ Ajouter `type: projects` dans l'entête du fichier en cas de mono projet => va afficher directement la page de synthèse de tous les audits
│   ├── audits // Les pages pour afficher les audits (accessibilité, qualité, performance…) pour chaque projet
│   │    ├── projet1 // Doit reprendre le nom du répertoire donné au projet dans `static`
│   │    │     └── index.md // ⚠️  pour avoir lister les pages sur l’accueil et avoir la page intermédiaire qui liste tous les rapports
│   │    └── projet2 // Doit reprendre le nom du répertoire donné au projet dans `static`
│   │          └── index.md
│   ├── meetings
│   │    ├── _index.md // liste les réunions
│   │    └── 2020-10-22-reunion1.md // On indique la date dans le nom de fichier pour ranger les fichiers visuellement, il faut la répéter dans l’entête YML du fichier pour afficher les réunions sur la page dans un ordre par date.
│   ├── personas // ⚠️ Ne fonctionne pas en mono projet
│   │    ├── _index.md // Liste tous les personas
│   │    ├── simon.md
│   │    ├── annie.md
│   │    └── projet1
│   │         ├── _index.md // liste tous les personas
│   │         ├── simon.md
│   │         └── annie.md
┋   ┋
┋   ┋ ⚠️ Autre choix d’arborescence avec un seul projet ⚠️
┋   ┋
│   └── audits // Les pages pour afficher les audits (accessibilité, qualité, performance…) 
│         └── index.md
└── static
    ├── projet1
    │    ├── accessibility
    │    │    ├── 2020-10-15.csv
    │    │    └── 2020-11-15.csv
    │    ├── lighthouse
    │    │    ├── 2020-10-15.json
    │    │    └── 2020-11-15.json
    │    ├── quality
    │    │    ├── 2020-10-15.yml
    │    │    └── 2020-11-15.yml
    │    ├── usertests
    │    │    ├── test1.json
    │    │    └── test2.json
    │    ├── backlinks.json
    │    ├── personas.json
    │    └── similary.json
    ├── projet2
    │    └── accessibility
    │         ├── 2020-10-15.csv
    │         └── 2020-11-15.csv
    ┋
    ┋ ⚠️ Autre choix d’arborescence avec un seul projet ⚠️
    ┋
    ├── accessibility
    │    ├── 2020-10-15.csv
    │    └── 2020-11-15.csv
    ├── quality // Ne fonctionne pas complètement en mono projet
    │    ├── 2020-10-15.yml
    │    └── 2020-11-15.yml
    ├── usertests // Ne fonctionne pas en mono projet
    │    ├── test1.json
    │    └── test2.json
    ├── directory.json // Annuaire de contacts
    └── images
         ├── projet1 // Doit reprendre le nom du répertoire donné au projet dans `static`
         │    ├── benchmark
         │    │    ├── titresiteweb-titreelementacomparer.png // nommage précis disponible sur la page rapport générée par Hugo
         │    │    ├── ojdcourtsepay.tylerhost.net-navigation-step.png
         │    │    ├── pages.fivepointpayments.com-navigation-step.png
         │    │    └── finepayment.saskatchewan.ca-navigation-step.png
         │    └── quality
         │         └── 2020-10-16 // Les titre formant les noms d‘images doivent correspondre à des termes existant dans le fichier `.yml`
         │             ├── titrepage-[before ou after].png
         │             ├── titrepage-titrebloc-titreerreur-[before ou after].png
         │             ├── accueil-before.png
         │             ├── accueil-after.png
         │             ├── accueil-banniere-titres-before.png
         │             └── accueil-banniere-titres-after.png
         ├── projet2
         └── personas

```

#### Configuration

*Exemple de fichier de configuration : `config.toml`*

```toml
title = "Mon administration"
theme = "fargo"

[params]
  organisation = "Le nom complet de mon administration"
  [params.strategy]
    schema = "https://www.monadministration.gouv.fr/schema_pluriannuel_2020-2022.pdf" // Optionnel
    plan = "https://www.monadministration.gouv.fr/plan_annuel_2020.pdf" // Optionnel
```

### Personnalisation du thème

Hugo permet de surcharger les fichiers présents dans le thème, la condition est de respecter la même arborescence. Ainsi pour surcharger un thème avec : 

 * Sa police
 * Son logo
 * Ses couleurs
 *…

Il suffit de créer tout ces fichiers dans le répertoire `static`. Le thème appelle automatiquement les polices contenus dans le répertoire `static/assets/fonts` (à la place de la police Marianne).

```
static
└── assets
    ├── css
    │   ├── fonts.css
    │   └── main.css
    ├── favicons
    │   ├── android-chrome-192x192.png
    │   ├── android-chrome-512x512.png
    │   ├── apple-touch-icon.png
    │   ├── favicon-16x16.png
    │   ├── favicon-32x32.png
    │   ├── favicon.ico
    │   └── site.webmanifest
    ├── fonts
    │   ├── open-sans-bold.woff2
    │   └── open-sans-regular.woff2
    └── images
        ├── logo.svg
        └── logodark.svg
```

### Contenus

#### Accueil

La page d’accueil présente la liste de tous les projets existants dans `content/projects`. Si le défi ne présente qu’un seul projet, et que la type de la page d'accueil est `type: "projects"`, alors la page affiche la page de résumé du projet.

![Accueil](/images/accueil.png)

#### Projets

La page de projets doit servir à présenter l’état de la démarche, puis le planning d’avancée du commando et enfin lister éventuellement des liens vers des billets de types : réunions ou actions (à insérer dans dans `content/meetings` et `content/actions`).

![Projets](/images/projets.png)

#### Audit

L’audit d’accessibilité peut être de *conformité* ou d’*accompagnement*. L’audit de conformité peut-être unique (évoluera au cours du projet) ou daté (ex: `2021-03-12.csv`) et présent dans le répertoire `static/accessibility/`.

L’audit d’*accompagnement* a pour but de lister tous les types d’erreurs afin de faire un suivi des éléments à corriger avec une équipe de développement.

##### Accessibilité

Éditer : `static/nomdelademarche/accessibility/YYYY-MM-JJ.csv`

L'audit accessibilité est généré à partir d‘un `.csv` (fichier à plat). Les données relatives au test (optionnelles) peuvent être indiquées dans l’entête du fichier `.md` de l’audit dans `content/audits/nomdelademarche/index.md` ou de `content/audits/nomdelademarche/accessibility.md`.

```yaml
---
type: accessibility // appel le gabarit accessibility :: static/nomdelademarche/accessibility/YYYY-MM-JJ.csv (fichier le plus récent)
accessibility:
  website: "amendes.gouv.fr"
  audit:
    guidelines: "RGAA 4.0"
    condition: "Auto-évaluation"
    technologies: ["HTML", "CSS", "JS", "PDF"]
    tools: ["Wave", "AXE", "Web Developper","Usability Hike","Heading Maps"]
    environment: ["MacOS", "Firefox", "Chrome", "ChromeVox"]
  contact:
    email: stap-amendes@dgfip.finances.gouv.fr
    address : 139 rue de Bercy, 75572 Paris, Cedex 12
---
```

Si on veut une déclaration de contexte propre à des audits en particulier c'est-à-dire par date, il est possible de créer une fichier `content/audits/nomdelademarche/accessibility/context.yml`.

```yaml
---
website: "amendes.gouv.fr"
contacts:
    email: stap-amendes@dgfip.finances.gouv.fr
    address : 139 rue de Bercy, 75572 Paris, Cedex 12
audits:
    2021-03-05: // bien respecter la date de chaque fichier csv d‘audit déjà présents dans `content/audits/nomdelademarche/accessibility/`
      guidelines: "RGAA 4.1"
      condition: "Auto-évaluation"
      technologies: ["HTML", "CSS", "JS", "PDF"]
      tools: ["Wave", "AXE", "Web Developper","Usability Hike","Heading Maps"]
      environment: ["MacOS", "Firefox", "Chrome", "ChromeVox"]
    2020-10-12:
      guidelines: "RGAA 4.0"
      condition: "Auto-évaluation"
      technologies: ["HTML", "CSS", "JS", "PDF"]
      tools: ["Wave", "AXE", "Web Developper","Usability Hike","Heading Maps"]
      environment: ["MacOS", "Firefox", "Chrome", "ChromeVox"]
---
```

![Accessibilite](/images/accessibilite.png)

##### Qualité

```yaml
---
type: quality // appelle le gabarit quality :: avec les données du fichier plus récent dans static/nomdelademarche/quality/YYYY-MM-JJ.yml
datafilename: YYYY-MM-JJ // appel le fichier avec la date correspondante
---
```

![Accessibilite](/images/qualite.png)

#### Annuaire

Lister les personnes contactées pendant la durée du défi de mise en conformité. Cet annuaire permet de partager les contacts dans le temps de l’amélioration de la démarche.

Éditer : `static/directory.json`

#### Recueil de tests utilisateurs

Les tests utilisateurs de type quantitatifs consistent à poser des questions similaires à un panel important d’usagers. Pour le service *usertests*, il existe une mise en forme pour l’analyse rapide de ces résultats.

Fonctionne à partir d'une JSON, mais pourrait marcher avec du CSV directement (Ici, penser à convertir le CSV en JSON).

Éditer : `static/nomdelademarche/usertests/nometude.json` (à faire évoluer)

Ajouter à l'entête du fichier de contenu :

```yaml
---
type: usertests // appel le gabarit usertests
datafilename: etudiants // appelle le fichier nommé etudiants :: dans static/nomdelademarche/usertests/etudiants.json
---
```

![usertests](/images/usertests.png)

#### Performance

L’audit de performance apporte une complémentarité à l’audit d’accessibilité en listant de manière automatique des éléments à optimiser. Il peut être reproduit de manière régulière. Le dernier test vient surcharger les autres sur la page projet. Il n'existe pas encore de gabarits d'analyse graphique dans le temps.


Éditer : `static/nomdelademarche/lighthouse/YYYY-MM-JJ.json`

![Performance](/images/performance.png)

#### Personas

Définir des personas et les afficher sur une même page pour les partager à l’équipe projet. On peut sélectionner les profils retenus pour les tests.

> Hugo nécessite de créer les pages correspondantes pour chaque personas ; si on veut afficher les personas en détail. Créer les pages dans  `content/personas/nomdelademarche/prenom-nom.md`.

Éditer : `static/nomdelademarche/personas.json`

![Personas](/images/personas.png)

#### Parcours

Ajouter un parcours type par personas pour fournir une base visuelle à l’équipe projet de ce qui est testé. Le gabarit permet de partir d’un élément parent unique puis de développer autant de sous branches possibles dans la limite de 4 niveaux de profondeur (compatible mobile).

Éditer : `static/nomdelademarche/personas.json`

![Parcours](/images/parcours.png)

#### Composants

##### Scores

![Scores](/images/scores.png)

##### Phases

Nécessite d’ajouter un type de catégorie dans le `config.toml` ; et d’ajouter le *tag* dans chacun des contenus qu’on souhaite voir afficher dans une phase.

Les contenus listés sont donc hétérogènes.

![Phases](/images/phases.png)

#### Shortcodes

##### Galerie de capture écrans

Afficher une liste de captures d’écran pour illustrer une étude comparative.

```go
{{< benchmark datafile="amendes" src="-explications" >}}{{< /benchmark >}}
```

##### Sites similaires

Afficher la liste des sites similaires à la démarche (action de benchmarking). Les sites sont rangés par pays.

Éditer : `static/nomdelademarche/similary.json`

```go
{{< similary project="amendes" >}}{{< /similary >}}
```

##### Diagrammes

Appel de la librairie *mermaid.JS*. Ajouter la syntaxe *Mermaid* dans le contenu du shortcode.

```go
{{< mermaid >}}
    graph TD
      A(Je reçois un avis)
      A --> B{Je paye une amende}
      B --> |Carte Bancaire| C(Site web)
      B --> |Carte Bancaire| D(Application)
      B --> |Chèque| E(Courrier)
      B --> |Chèque/Espèce/CB| F(Buraliste)
      B --> |Carte Bancaire| G(Téléphone)
      B --> |Chèque/Espèce/CB| H(Trésor Public)
      B --> |Virement pour étrangers| I(Site web)
{{< /mermaid >}}
```

![graphiques](/images/diagramme.png)

##### Graphiques

Petit aide pour l’affichage de graphiques. Passer les données dans le contenu du *shortcode*. Choisir le titre et le type de graphique à afficher : bar, line, donut…

```go
{{< graphic title="Ventilation des paiements dans l’année (en milliers)" type="bar">}}
{
    labels: ["Vitesse", "PV électronique", "Feux rouges"],
    datasets: [
      {
        name: "Internet",
        values: [3351, 2482, 85],
      },
      {
        name: "Smartphone",
        values: [492, 497, 12]
      }
    ]
}
{{< /graphic >}}
```
![graphiques](/images/graphiques.png)

#### Personas

Afficher les personas sur la page de son choix en fonction du projet de son choix.

```go
{{< personas project="amendes" >}} {{< /personas >}}
```

#### Parcours

Afficher les parcours sur la page de son choix en fonction du projet de son choix.

```go
{{< parcours project="amendes" >}} {{< /parcours >}}
```

## Utiliser ce module comme thème d'un projet

Site officiel :
https://gohugo.io/hugo-modules/use-modules/#use-a-module-for-a-theme

1. Initialiser votre projet pour utiliser les modules hugo :
`$ hugo mod init [MON_GESTIONNAIRE_DE_DEPOT]/[MON_COMPTE]/[MON_PROJET]`
par exemple :
`$ hugo mod init github.com/disic/frago`

2. Importer ce module dans le fichier `config.toml`:
```toml
[module]
  [[module.imports]]
    path = "github.com/disic/frago"
```

3. C’est installé ?
  - En mode dev, lancer `hugo server`. Mais préférer : `HUGO_ENV=production hugo server --buildFuture`, pour le mode production.
  - Mettre à jour le thème, lancer: `hugo mod get -u github.com/disic/frago/` ou juste `hugo mod get -u`.
  - L’appel de thème n'est pas possible avec Netlify, il est nécessaire d’indiquer cette commande dans l’interface `git submodule add https://github.com/disic/frago.git/ themes/frago -f && git submodule update --init --recursive && hugo --gc --minify --buildFuture --templateMetrics`

## Licence

Le code source de ce dépôt est publié sous licence [MIT](https://opensource.org/licenses/mit-license.php).

La marque d'État est à usage exclusif des acteurs de la sphère étatique. En particulier, la typographie Marianne© est protégée par le droit d'auteur.  
Lire [les explications](https://www.gouvernement.fr/charte/charte-graphique-les-fondamentaux/la-typographie) sur le site de la marque d’État.

Le thème Frago utilise cette liste de bibliothèques :

  1. Frappe Charts ([MIT](https://github.com/mermaid-js/mermaid/blob/develop/LICENSE)) : <https://github.com/frappe/charts> - <https://github.com/frappe/charts>
  2. Mermaid ([MIT](https://github.com/mermaid-js/mermaid/blob/develop/LICENSE)) : <https://github.com/mermaid-js/mermaid>
  3. Highlight.js ([MIT](https://github.com/highlightjs/highlight.js/blob/main/LICENSE)) : <https://github.com/highlightjs/highlight.js>
  4. simplelightbox ([MIT](https://github.com/andreknieriem/simplelightbox/blob/master/LICENSE)) : <https://github.com/andreknieriem/simplelightbox>
  5. Van11y accessible tabs panel system ([MIT](https://github.com/nico3333fr/van11y-accessible-tab-panel-aria/blob/master/LICENSE)) : <https://github.com/nico3333fr/van11y-accessible-tab-panel-aria>
  6. Checka11y.css ([MIT](https://github.com/jackdomleo7/Checka11y.css/blob/master/LICENSE)) : <https://github.com/jackdomleo7/Checka11y.css>

## Auteur

2020-2021, Bertrand Keller pour le ministère de l’économie, des finances et de la relance.
