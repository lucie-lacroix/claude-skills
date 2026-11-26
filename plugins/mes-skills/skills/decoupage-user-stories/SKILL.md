---
name: "decoupage-user-stories"
description: "Lire un projet dans Notion, valider la compréhension avec l'utilisateur, le découper en user stories (epics dans le nom si besoin) puis, après validation, créer les tickets dans la base Notion \"2tonnes Produit\" au statut En spec. On utilise cette skill lorsque le projet est assez mature pour être splité en user stories."
---

# Découpage de projet produit en tickets (Notion)

Objectif : lire le projet dans Notion, faire valider la compréhension qu'on en a, le transformer en tickets clairs, cohérents et testables (user stories en majorité, mais aussi petites améliorations, bugs et tickets techniques quand c'est plus juste), puis créer ces tickets dans la base Notion "2tonnes Produit" après validation de l'utilisateur.

Langue : français par défaut, sauf si l'utilisateur écrit dans une autre langue.

Deux points de validation jalonnent le travail : la compréhension du projet (fin de l'étape 1) et l'aperçu des tickets (étape 5). Rien n'est découpé avant le premier, rien n'est créé avant le second.

Règle transverse sur les sources : chaque fois que le skill cite ou reformule une partie de la page projet (dans un ticket, dans le tableau de couverture, dans un arbitrage), il met un lien vers cette page et nomme la section ou le toggle concerné (ex. « toggle Vi/So du 9 nov. »). Les liens vers un bloc précis ne sont pas toujours récupérables : dans ce cas le lien pointe vers la page entière et la section est nommée en toutes lettres.

## Étape 1 : Rechercher les informations du projet et valider la compréhension

### 1a. Collecter

Utiliser le connecteur Notion (charger ses outils via ToolSearch si nécessaire) :

1. Chercher la page projet correspondant au nom donné par l'utilisateur et la lire en entier (objectif, contexte, specs, tableaux de règles, toggles d'échanges, liens, sous-pages utiles, commentaires, calendrier). Suivre les liens vers les documents liés quand ils apportent du périmètre.
2. Si la page projet est introuvable ou si plusieurs pages correspondent, demander à l'utilisateur laquelle utiliser (ou de la créer) avant d'aller plus loin. Ne jamais inventer un projet.
3. Repérer la base "2tonnes Produit" et lire son schéma complet (noms et types exacts des propriétés, options de Select/Status, relations). Le schéma réel prime toujours sur le mapping de l'étape 6.
4. Lire les tickets existants du projet pour éviter les doublons et reprendre les conventions : ceux de la base filtrée sur la relation `💥 Projets NEW`, et ceux des bases intégrées dans la page projet (ex. « Backlog Dev lié au projet », « Tâches liées au projet »). Garder leurs liens : ils servent au tableau de couverture (étape 4).
5. Si aucun connecteur Notion n'est disponible, le chercher dans le registre de connecteurs et le proposer à l'utilisateur. En dernier recours, travailler à partir du brief fourni dans la conversation et livrer un CSV (voir "Solution de repli").

Déterminer aussi la **plateforme** : `2tonnes` ou `Mao Boa`. La déduire de la page projet si c'est explicite, sinon la proposer comme hypothèse à valider. Si le projet touche les deux plateformes, prévoir des tickets distincts par plateforme.

### 1b. Exposer la compréhension et la faire valider

Avant tout découpage, présenter dans le chat une synthèse courte (une dizaine de lignes maximum, pas de résumé de toute la page) :

- **Projet et plateforme** : nom de la page projet trouvée (avec lien), plateforme retenue.
- **Objectif** : le problème à résoudre et la valeur attendue, en une ou deux phrases.
- **Utilisateurs / personas** concernés.
- **Périmètre** : ce qui est inclus, ce qui est explicitement exclu.
- **Contraintes et calendrier** : contraintes techniques ou réglementaires, volumétries, et tout ce que la page dit sur le temps : dates clés, événements, dépendances externes (livraison d'un tiers, autre projet), ce qui est prévu « plus tard » ou « dans un second temps ». Chaque élément avec le lien vers la section de la page. S'il n'y a aucune information de calendrier, le dire plutôt que d'en supposer.
- **Existant** : tickets déjà présents pour ce projet (avec liens), s'il y en a.
- **Questions ouvertes et hypothèses** : tout ce qui est déduit ou interprété faute d'information explicite, clairement séparé de ce qui est écrit dans la page, avec le lien vers la section concernée. Signaler aussi les zones floues ou contradictoires (ex. cellule « ?? » dans un tableau, toggle vide).

Demander ensuite une validation explicite (AskUserQuestion si disponible) : "C'est correct", "Corriger l'objectif", "Corriger les hypothèses / le périmètre / le calendrier".

Règles de re-validation :

- Si l'utilisateur modifie l'objectif, toujours re-présenter la synthèse mise à jour (avec l'objectif modifié mis en évidence) et redemander une validation explicite ("C'est correct" / corriger à nouveau), quelle que soit l'ampleur de la modification. Répéter jusqu'à accord.
- Si l'utilisateur corrige le périmètre, les hypothèses ou le calendrier, re-présenter la synthèse et redemander la validation en cas de changement important ; pour une correction mineure, l'appliquer et le confirmer en une ligne.
- Ne passer au découpage qu'après un accord explicite sur la dernière version de la synthèse.

Ne pas poser de questions dont la réponse figure déjà dans la page projet : les questions ne servent qu'à lever les zones floues et à valider les hypothèses.

## Étape 2 : Décider des epics

Un epic n'est pas une question de nombre de tickets. Créer un epic seulement si le projet contient des objectifs très autonomes comprenant chacun plus d'un ticket.

- Test : l'objectif pourrait-il être livré et apporter de la valeur seul, indépendamment des autres ? Et regroupe-t-il au moins 2 tickets ? Si oui → epic. Sinon → pas d'epic.
- Un projet peut mélanger des tickets avec epic et des tickets sans epic.
- Pas d'epic fourre-tout ("Divers", "Technique") et pas d'epic à un seul ticket.
- Le nom de l'epic doit être simple et concis : 1 à 3 mots, sans verbe ni phrase (ex. "Paiement", "Onboarding", "Export").
- L'epic n'existe que dans le titre du ticket, sous la forme `[Nom de l'epic] - Nom du ticket`. Ne jamais créer de ticket « epic » dans la base et ne pas utiliser la relation `Élément parent` pour ça.

## Étape 3 : Découper en tickets

### 3a. Choisir le type de chaque ticket

| Type | Quand | Template de corps |
|---|---|---|
| `US` | Une capacité qui apporte de la valeur à un utilisateur | « US à la sauce Lucie » |
| `Petite amélioration` | Un petit ticket qui améliore l'existant : quick win, ajustement, mini-feature, amélioration design | Contexte / Quoi |
| `Bug` | Un comportement constaté différent du comportement attendu | « Bug » |
| `Ticket technique` | Migration, script de données, blocage côté serveur, refacto : pas de valeur utilisateur directe | Contexte / Quoi (Comment seulement si la page projet le donne) |

En cas de doute entre `US` et `Petite amélioration`, choisir `Petite amélioration` si le ticket tient en quelques heures de dev sans nouveau parcours utilisateur, sinon `US`. Les autres types de la base (Discovery, Disco Tech, Maquette, Non Dev, Production contenu, A qualifier) ne sont pas créés par ce skill ; si la page projet révèle un besoin de ce genre, le signaler dans l'aperçu plutôt que de créer le ticket.

### 3b. Règles de découpage

- Découper par valeur utilisateur (tranches verticales de bout en bout), pas par couche technique.
- Respecter INVEST : Independent, Negotiable, Valuable, Estimable, Small, Testable. Si un ticket est trop gros ou flou, le redécouper (par étape de workflow, règle métier, type d'utilisateur, cas nominal vs cas limite).
- Un ticket = un seul objectif (un seul « je veux » pour une US).
- Critères d'acceptation : ne jamais les inventer. Les reprendre ou les reformuler uniquement s'ils sont écrits dans la page projet ou découlent directement et sans ambiguïté d'une règle qu'elle énonce (avec lien vers la source). Sinon, laisser la section vide ou écrire « À définir avec l'équipe ». Pas de quota : zéro critère vaut mieux que des critères plausibles mais supposés. Un critère ajouté doit pouvoir être sourcé ; en cas de doute, le transformer en question ouverte.
- Comment (piste technique) : ne jamais le remplir de sa propre initiative. Seulement si la page projet donne une piste ou une contrainte technique explicite, ou si un ticket technique est impossible à comprendre sans.
- Persona : ne l'utiliser que s'il apporte quelque chose. Pour une US, la phrase « En tant que [persona], je veux [action] afin de [bénéfice] » est conservée. Pour un ticket technique, une petite amélioration ou une tâche de migration, écrire directement ce qui doit être fait et pourquoi, sans inventer de persona (« équipe produit » ou « système » ne sont pas des personas utiles).
- Identifier les dépendances entre tickets (voir étape 6 : elles deviennent des relations `Bloqué par` / `Bloque`, pas du texte).
- Garder le contexte chiffré de la page quand il éclaire un ticket (volumétries, nombre de cas) dans la section Contexte.
- Rester aligné sur l'objectif, le périmètre et les hypothèses validés à l'étape 1.
- Ne créer aucune date d'échéance sur les tickets : le calendrier sert à déterminer la priorité (étape 4c), pas à dater les tickets.

### 3c. Corps des tickets (templates de la base)

Utiliser les titres de section ci-dessous, tels quels.

Principe : ne remplir que ce qui provient de la page projet ou de la validation de l'étape 1. Les sections « Règles métier », « Critères d'acceptation » et « Comment » ne sont pas à compléter par défaut : si la source ne les fournit pas, les omettre ou les laisser vides plutôt que de les inventer. Ce qui est déduit sans être écrit va dans « Questions ouvertes / Hypothèses », clairement signalé comme tel.

**US et Ticket technique**

```
# Contexte
Pourquoi ce ticket, avec lien vers la page projet et nom de la section source.

# Quoi
En tant que [persona], je veux [action] afin de [bénéfice].   (phrase directe sans persona pour un ticket technique)

## Règles métier
- ... (avec lien vers la section source de la page projet)

## Critères d'acceptation
- ... (uniquement ceux issus de la page projet ; sinon omettre la section ou écrire « À définir avec l'équipe »)

## Questions ouvertes / Hypothèses
- ... (ou "Aucune")

# Maquette
(vide, ou lien si la page projet en contient)

# Comment
(vide par défaut ; uniquement une piste technique explicitement donnée par la page projet, jamais inventée)
```

Omettre « Règles métier » quand il n'y en a pas, « Critères d'acceptation » quand la page projet n'en fournit pas, et « Comment » quand aucune piste n'est donnée. Les dépendances ne s'écrivent pas dans le corps : elles vont dans les propriétés (étape 6).

**Petite amélioration** : sections `# Contexte` et `# Quoi` (avec critères d'acceptation seulement s'ils sont issus de la page projet, et questions ouvertes si utile).

**Bug** :

```
### Cas de reproduction
### Comportement constaté
### Comportement attendu
(avec lien vers l'US ou la section de la page projet qui décrit le comportement attendu)
```

## Étape 4 : Relecture de cohérence, couverture et priorité

### 4a. Tableau de couverture

Construire un tableau « élément de la page projet → ticket(s) qui le couvrent » : une ligne par fonctionnalité, règle, point d'entrée ou ligne de tableau de la page projet (ex. chaque ligne d'un tableau « endroit pour ajouter un membre »). Colonnes :

- **Élément de la page** : libellé court avec lien vers la page projet et nom de la section.
- **Couvert par** : le ou les tickets, en distinguant clairement « à créer » (titre prévu) et « existant » (lien vers le ticket Notion).
- **Remarque** : « non couvert », « volontairement exclu », « à clarifier », etc.

Une ligne sans ticket (ni à créer ni existant) est une fonctionnalité non couverte : voir la règle ci-dessous.

### 4b. Incohérences

Passer le backlog en revue et traiter chaque incohérence ainsi :

- **Doublons ou chevauchement entre deux tickets du backlog** : fusionner les deux s'ils ont le même objectif. S'ils ne se recoupent qu'en partie, redécouper pour que chacun ait un périmètre distinct. Signaler la fusion ou le redécoupage dans l'aperçu.
- **Ticket déjà présent dans "2tonnes Produit" pour ce projet** : ne pas le recréer ; l'inscrire dans le tableau de couverture avec son lien. Si un ticket existant bloque ou est bloqué par un ticket à créer, le noter pour renseigner `Bloqué par` / `Bloque` à l'étape 6. Si le recouvrement n'est que partiel, laisser l'utilisateur choisir : l'ignorer, créer quand même, ou compléter le ticket existant.
- **Fonctionnalité de la page projet non couverte** (ligne du tableau sans ticket) : ajouter le ou les tickets manquants et les marquer "ajouté" dans l'aperçu. Si le texte du projet est trop vague pour rédiger un ticket fiable, poser la question plutôt que d'inventer.
- **Ticket qui n'est dans aucun point de la page projet** : le signaler comme "hors périmètre apparent". L'utilisateur décide de le garder, de le retirer ou de mettre à jour la page projet.
- **Cycle de dépendances (A dépend de B qui dépend de A)** : par défaut, fusionner les tickets du cycle en un seul et le signaler dans l'aperçu. Si le ticket fusionné devient trop gros (ne respecte plus INVEST), le dire à l'utilisateur et lui laisser choisir entre fusionner quand même ou casser le cycle autrement.

### 4c. Priorité de chaque ticket

La priorité utilise la propriété `Priorité` de la base. Appliquer les trois critères dans cet ordre :

1. **Contribution à l'objectif validé à l'étape 1**
   - `P1` : le ticket est indispensable pour atteindre l'objectif. Sans lui, le projet ne tient pas ou n'apporte pas sa valeur centrale.
   - `P2` : le ticket complète l'objectif, mais on peut livrer sans lui dans un premier temps, ou il y a un contournement.
   - `P3` : confort, cas limite ou amélioration secondaire.
2. **Temporalité** : si, d'après la page projet ou les éléments validés à l'étape 1b, le ticket ne pourra de toute façon pas être réalisé avant 6 mois, ou ne sera pas utile avant 6 mois (ex. il dépend d'une livraison tierce ou d'un événement daté lointain, ou la page le place « plus tard »), il n'est pas prioritaire : il passe en `P3`, quelle que soit sa contribution à l'objectif. Le seuil de 6 mois est un défaut ; l'utilisateur peut le changer. Ne pas deviner : sans information explicite sur le calendrier, aucun ticket n'est rétrogradé. En cas de doute (le calendrier est suggéré mais pas clair), garder la priorité issue du critère 1 et le signaler comme point à arbitrer dans l'aperçu.
3. **Propagation par dépendances** : un ticket prend au moins la priorité finale (après le critère 2) du ticket le plus prioritaire qu'il bloque. Un prérequis d'un `P1` est donc `P1`, mais un prérequis d'un ticket rétrogradé en `P3` par la temporalité n'est pas poussé par lui. Quand la propagation fait passer un ticket en `P1` alors que la page projet suggère de le faire plus tard (ex. « dans un premier temps, s'en tenir aux orgas validées »), le signaler dans l'aperçu comme point à arbitrer plutôt que de trancher seul.

`Bloquant` n'est jamais attribué par ce skill : il est réservé aux bugs qui empêchent aujourd'hui un usage critique, et seulement si la page projet l'indique explicitement.

Garde-fou : si plus de la moitié des tickets sont `P1`, le signaler dans l'aperçu et demander à l'utilisateur d'arbitrer. À priorité égale, l'ordre de création suit l'ordre des dépendances.

Chaque priorité est accompagnée d'une justification en une ligne dans l'aperçu, avec le lien vers la source quand elle vient de la page (ex. « P1, car bloque [titre du ticket] » ou « P3, ne peut pas être fait avant 6 mois : dépend de la livraison X, section Calendrier »).

## Étape 5 : Validation avant création

Présenter un aperçu dans le chat, sans rien créer :

1. Plateforme et projet (avec lien).
2. La liste des tickets à créer, avec pour chacun : titre exact tel qu'il apparaîtra dans Notion, `Type`, `Priorité` avec sa justification, `Périmètre technique` (si déterminé), et dépendances (`Bloqué par` / `Bloque`, par titre ou lien).
3. Le tableau de couverture (étape 4a).
4. Les **questions ouvertes et hypothèses** qui seront écrites dans les tickets, regroupées.
5. Un bloc "Incohérences détectées" en deux parties : "Corrigé automatiquement" (fusions, redécoupages, tickets ajoutés, cycles fusionnés) et "À arbitrer" (recouvrements partiels avec l'existant, tickets hors périmètre apparent, fonctionnalités trop vagues, fusions de cycle qui rendent un ticket trop gros, priorités poussées par propagation, calendrier flou, trop de `P1`). Les tickets concernés par un arbitrage ne sont pas créés tant que l'utilisateur n'a pas tranché.

Convention de nommage du titre :

- avec epic : `[Nom de l'epic] - Nom du ticket` (ex. `[Paiement] - Payer par carte bancaire`)
- sans epic : `Nom du ticket`
- le nom du ticket est court, orienté action (verbe + objet), 70 caractères maximum hors préfixe d'epic. Les précisions vont dans le corps, pas dans le titre.

Demander la validation explicite de l'utilisateur (AskUserQuestion si disponible) : créer tout, ajuster, ou annuler. Ne créer aucun ticket dans Notion avant ce feu vert. Appliquer les modifications demandées (y compris les changements de priorité) puis re-présenter si elles sont importantes.

## Étape 6 : Création des tickets dans "2tonnes Produit"

### 6a. Mapping des propriétés

Utiliser les noms et options réellement lus dans le schéma de la base (ils priment sur ce tableau en cas de différence).

| Propriété Notion | Valeur | Règle |
|---|---|---|
| `Libellé` (titre) | `[Epic] - Nom du ticket` ou `Nom du ticket` | Voir convention de nommage. |
| `Type` | `US`, `Petite amélioration`, `Bug` ou `Ticket technique` | Voir étape 3a. |
| `PLATEFORME` | `✌️2tonnes✌️` ou `🐍Mao Boa🐍` | Valeur exacte, émojis compris. |
| `Statut` | `En spec` | Pour tous les tickets créés par le skill. |
| `💥 Projets NEW` | Relation vers la page projet lue à l'étape 1 | Jamais `[OLD] Projet lié`. |
| `Priorité` | `P1`, `P2` ou `P3` | Voir étape 4c. Jamais `Bloquant`. |
| `Périmètre technique` | `Front`, `Back` ou `Les deux` | Seulement si c'est clair dans la page projet, sinon laisser vide. |
| `Bloqué par` | Relation vers les tickets prérequis | Renseigné après création (6c). |
| `Bloque` | Relation vers les tickets débloqués | Vérifier qu'elle se remplit en miroir de `Bloqué par`, sinon la renseigner (6c). |

Propriétés à laisser vides : `Description`, `Assignation`, `Date` (aucune échéance n'est créée), `Story points`, `Sprint`, `Version en prod`, `Élément parent`, `Élément enfant` et toutes les propriétés `[OLD]`. Ce sont des décisions d'équipe ou des propriétés dépréciées, pas du découpage.

### 6b. Création

Créer une page par ticket avec le connecteur Notion, avec le corps défini à l'étape 3c selon le type. Créer les tickets dans l'ordre des dépendances (prérequis d'abord) : les identifiants étant auto-incrémentés, le premier ticket à faire a le plus petit.

### 6c. Dépendances

Une fois tous les tickets créés (on a alors leurs liens), renseigner `Bloqué par` (et `Bloque` si besoin) avec des liens vers les pages des autres tickets, y compris les tickets existants identifiés à l'étape 4b. Ne jamais écrire les dépendances en texte dans le corps du ticket.

### 6d. Vérification

Relire la base : bon nombre de tickets, propriétés correctement remplies (`Type`, `PLATEFORME`, `Statut`, `💥 Projets NEW`, `Priorité`, `Périmètre technique`, `Bloqué par` / `Bloque`), titres conformes, corps conformes au template. Corriger toute erreur.

## Solution de repli : CSV

Si la création via connecteur est impossible, générer un CSV UTF-8 avec BOM, séparateur virgule, valeurs multi-lignes entre guillemets, avec les colonnes : Libellé, Type, PLATEFORME, Statut (`En spec`), Priorité, Projet, Périmètre technique, Bloqué par (titres des tickets), Description (corps du ticket selon son template). Nommer le fichier `backlog-[projet]-[plateforme].csv`, le livrer avec SendUserFile et expliquer l'import (Notion > menu "..." de la base > Merge with CSV). Les relations `Bloqué par` et `💥 Projets NEW` devront être refaites à la main dans Notion après import ; le dire à l'utilisateur.

## Message de fin

En quelques lignes : projet et plateforme, nombre de tickets créés par type et par priorité, nombre d'epics (dans les titres), tickets rétrogradés pour raison de calendrier, principales hypothèses et questions ouvertes. Puis la liste des tickets créés, un par ligne, avec leur identifiant (TP-xxxx), leur priorité et un lien vers chaque ticket, et le lien vers la page projet.