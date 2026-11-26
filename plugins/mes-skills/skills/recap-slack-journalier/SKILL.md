---
name: "recap-slack-journalier"
description: "Recap Slack + Gmail du dernier jour ouvré pour Lucie (canaux Slack 2-* et 5-*, DMs, mentions, et mails Gmail) : top infos, sujets traités par elle, à traiter, relances, en plus. Publié en toggle dans sa page Notion. À utiliser pour tout recap/point Slack ou mails, « qu'ai-je raté », « mes relances »."
---

# Récap du Jour (Slack + Gmail → Notion)

Objectif : donner à Lucie, en quelques minutes de lecture, une vue fiable de ce qui s'est passé sur Slack et dans sa boîte Gmail depuis le dernier jour ouvré, de ce qu'elle a elle-même traité, de ce qui l'attend et de ce qu'elle doit relancer. La valeur du recap tient à sa fiabilité : mieux vaut signaler « à vérifier » que d'affirmer à tort qu'un sujet est clos ou qu'il lui revient.

## Règle absolue : Slack et Gmail en lecture seule

N'envoie jamais rien sur Slack ni par mail. N'utilise aucun outil d'écriture Slack : pas de `slack_send_message`, `slack_send_message_draft`, `slack_schedule_message`, pas de réaction, pas de création ou modification de canvas, de liste ou de conversation. Pour Gmail, n'utilise que `search_threads`, `get_thread` et `get_message` (et `list_labels` si besoin). Aucun outil qui envoie, répond (`reply`, `send_message`), transfère (`forward`), crée ou modifie un brouillon, pose ou retire un libellé, marque comme spam, met à la corbeille ou supprime. Utilise uniquement des outils de lecture et de recherche. Les brouillons de relance vivent uniquement dans le recap Notion, sous forme de texte que Lucie copiera et enverra elle-même ; ils ne sont jamais déposés dans Slack ni dans Gmail, pas même comme brouillon.

## 1. Fenêtre de temps

La fenêtre commence à 00:00 (heure de Paris) du dernier jour ouvré strictement avant aujourd'hui, et va jusqu'à maintenant. Elle inclut donc les jours non ouvrés entre les deux.

- Mardi à vendredi : le dernier jour ouvré est la veille.
- Lundi : c'est le vendredi précédent à 00:00, week-end inclus.
- Jour férié en France entre-temps : on remonte au jour ouvré d'avant. Exemple : le mardi après un lundi férié, la fenêtre part du vendredi 00:00.

Calcule les dates avec `date` en bash (`TZ=Europe/Paris`), pas de tête, et vérifie les fériés français (fixes : 1er janvier, 1er mai, 8 mai, 14 juillet, 15 août, 1er novembre, 11 novembre, 25 décembre ; mobiles : lundi de Pâques, Ascension, lundi de Pentecôte, dont tu calcules les dates de l'année). Convertis le début de fenêtre en timestamp Unix pour les paramètres `oldest` / `after` de Slack (et utilise la même date pour le filtre `after:` de la recherche Gmail). Annonce la fenêtre en tête du recap.

## 2. Sources à parcourir

1. **Tous les canaux non archivés dont le nom commence par `2-` ou par `5-`** (ex. #2-produit, #2-comex, #5-qualiopi, #5-mao-boa-interne). Le tiret compte : un canal comme #2t-version-rouen n'en fait pas partie. Liste-les à chaque exécution avec `slack_list_user_channels` en `name_prefix` « 2- » puis « 5- », `exclude_archived=true`, car de nouveaux canaux apparaissent régulièrement (le format `ids_only` ne donne pas les noms : demande `names_only` en plus si tu veux les noms, ou lis le nom dans l'en-tête de la réponse de `slack_read_channel`). Lis chaque canal avec `slack_read_channel` et `oldest` en **format détaillé** (pas `concise`) : seul le format détaillé donne l'horodatage exact de chaque message, le nombre de réponses d'un fil et les réactions, dont tu as besoin pour les liens et pour savoir si un fil est vivant. Lance les lectures en parallèle.
2. **Ses DMs et groupes** (types `im` et `mpim`) avec activité dans la fenêtre, via `slack_search_public_and_private` avec `channel_types` « im,mpim » et `after`. Si la recherche ne renvoie rien, ne conclus pas « rien » : écris dans le recap que les DMs n'ont rien remonté et peuvent ne pas être couverts.
3. **Ses mentions** (`<@U074GE4K5TN>`) dans n'importe quel canal, même hors 2-/5-, via `slack_search_public_and_private` avec `after`.

L'identifiant Slack de Lucie est `U074GE4K5TN`. Pour chaque message qui ouvre ou fait vivre un fil, lis le fil en entier (`slack_read_thread`) : la réponse ou la résolution est souvent dans le fil, et c'est ce qui permet de savoir ce que Lucie a fait et ce qu'on attend encore d'elle. Dédoublonne : une conversation trouvée par plusieurs voies ne compte qu'une fois.

4. **Ses mails Gmail** reçus dans la fenêtre, tous, via l'outil de recherche Gmail `search_threads` (par exemple `after:<date de début>`, sans restreindre à la boîte de réception pour ne pas rater un mail rangé par un filtre). Lis chaque conversation en entier avec `get_thread` (ou `get_message` pour un message isolé), pas seulement le dernier message, pour savoir si Lucie a déjà répondu. Cherche aussi ses mails **envoyés** dans la fenêtre (`in:sent after:<date>`) : ils alimentent « Sujets traités » (ce qu'elle a répondu) et « Relance à faire » (ce qu'elle a demandé sans réponse). Dans le recap, le canal s'écrit `Gmail` suivi de l'expéditeur, par exemple « Gmail (Elisabeth) » ; le lien est celui de la conversation Gmail (`https://mail.google.com/mail/u/0/#inbox/<id de conversation>`). Si un même sujet existe sur Slack et par mail, ne fais qu'une ligne et cite les deux canaux (« #2-produit + Gmail »). Les mêmes règles de classement (section 3), d'importance (section 4) et de Top infos (section 5) s'appliquent aux mails comme à Slack.

Ignore le bruit côté Slack : notifications de bots et de déploiement sans action (#2-deploiement-prod, #2-gitpull-impact…) sauf incident ou échec, discussions purement sociales, sauf si elles contiennent une demande. Côté Gmail, ignore de la même façon newsletters, publicités, notifications automatiques (outils, réseaux sociaux, confirmations) et mails purement informatifs sans demande, sauf alerte réelle (sécurité, facture impayée, échec, échéance). Si un canal ou la boîte Gmail est inaccessible, note-le dans le recap plutôt que de l'ignorer en silence.

## 3. Classer chaque sujet

Regroupe par sujet (un fil = un sujet, ou plusieurs fils sur la même question). Il n'y a ni minimum ni maximum par section : une section peut être vide si rien ne correspond, ou contenir 10 lignes si c'est la réalité de la journée. Ne comble jamais, et ne coupe pas non plus.

### Sujets traités : uniquement ce que Lucie a traitée elle-même

Un sujet n'apparaît ici que si Lucie a elle-même agi sur Slack dans la fenêtre : elle a écrit une réponse, posé une question, donné une décision, partagé un livrable, ou clos un point. Une conversation réglée par quelqu'un d'autre, ou simplement mentionnée sans que Lucie ait agi, n'est pas un sujet traité par elle : elle va, si elle est pertinente, dans « En plus ». Une simple réaction emoji ou une arrivée dans un canal ne suffit pas pour dire qu'elle a traité le sujet. Dans la colonne « Ce que j'ai fait », décris concrètement son action, à la première personne (« J'ai répondu à Elisabeth que… »). Si Lucie n'a rien fait de visible sur Slack, la section contient une seule ligne « Rien » : elle a pu travailler hors Slack, le recap ne le voit pas, et c'est normal.

Un même sujet peut figurer à la fois dans « Sujets traités » (ce que Lucie a déjà répondu) et dans « À traiter » (une nouvelle question lui a été posée depuis) ; c'est voulu, la colonne « Next step » du premier renvoie alors au second.

### À traiter : ce qui attend une action de Lucie

Un sujet entre dans « À traiter » si les deux conditions suivantes sont réunies :

1. **Quelqu'un attend quelque chose de Lucie, de façon explicite ou clairement implicite.** C'est le cas quand : elle est mentionnée (`@Lucie`) ou nommée dans une question, une demande ou une validation ; un fil où elle a déjà répondu contient ensuite une nouvelle question ou relance qui s'adresse à elle ; une demande adressée à toute l'équipe ou à un groupe dont elle fait partie (« chacoulis », « les managers ») la concerne par son rôle ou son périmètre (produit, plateforme, Qualiopi, jetons) ; une décision ou un livrable de son périmètre est explicitement bloqué faute d'elle.
2. **Rien dans le fil n'indique qu'elle a répondu ou fait ce qu'on lui demande**, après la dernière demande. Si Lucie a répondu après la demande, ou si quelqu'un d'autre a déjà réglé la demande (« c'est fait »), le sujet n'est plus à traiter.

Ce qui n'y entre pas : un message simplement « pour info » sans demande, une discussion où elle n'est pas concernée, un sujet déjà clos. Quand la preuve est ambiguë (réaction emoji seule de Lucie, demande générale qui la concerne peut-être, discussion qui a pu continuer en visio ou ailleurs), inscris-le avec la mention « à vérifier » plutôt que de l'écarter : une fausse alerte coûte quelques secondes, un oubli silencieux coûte bien plus. Dans la colonne « Ce que je dois faire », indique l'action précise attendue et pourquoi (qui attend quoi), pas seulement le sujet.

### Relance à faire : ce que Lucie attend des autres

C'est Lucie qui attend quelqu'un. Deux cas : une question ou demande de Lucie restée sans réponse depuis plus d'un jour ouvré ; une promesse non tenue (« je reviens vers toi », « je regarde demain ») adressée à Lucie, sans suite visible. Mesure l'ancienneté en jours ouvrés, pas calendaires : un message du vendredi n'est pas en retard le samedi. Si rien ne correspond, une ligne « Rien ».

### En plus

Sujet qui n'entre dans rien ci-dessus mais qui semble pertinent pour Lucie : annonce, décision d'équipe, risque, idée, info utile à son périmètre (produit, plateforme, Qualiopi, clients), ou conversation réglée par d'autres qui peut l'intéresser.

## 4. Niveau d'importance (À traiter et Relances)

Utilise trois niveaux : 🔴 Haute, 🟠 Moyenne, 🟢 Basse. Pars de ces critères :

- 🔴 si quelqu'un est bloqué par Lucie, ou échéance dans les 2 jours, ou demande d'un client clé / du comex / de la direction, ou incident ou risque (prod, Qualiopi, contrat).
- 🟠 si une échéance existe dans la semaine, ou si le sujet fait avancer un projet en cours sans blocage immédiat.
- 🟢 sinon : confort, suivi, « quand tu peux ».

Trie chaque tableau du plus au moins important.

## 5. Choisir les 3 « Top infos »

Les Top infos sont les 3 choses que Lucie ne doit pas rater si elle ne lit rien d'autre. Elles peuvent venir de n'importe quelle section (traité, à traiter, relance, en plus). Note chaque candidat avec ces critères, par ordre de poids :

1. **Impact** : une décision prise ou à prendre qui engage le produit, le budget, un client majeur (Veolia, Bouygues, Eiffage, CVG…), le comex ou la stratégie (rebranding Hability, Mao Boa, Qualiopi).
2. **Urgence / risque** : échéance proche, incident, blocage, client mécontent, engagement en danger.
3. **Lien direct avec Lucie** : on lui demande quelque chose, elle est mentionnée, ou quelqu'un dépend d'elle.
4. **Changement de cap** : annonce, départ, nouvelle organisation, revirement par rapport à ce qui était prévu.
5. **Portée** : nombre de personnes ou de canaux concernés, niveau des interlocuteurs.

Prends les 3 meilleurs scores, en évitant trois infos sur le même sujet (varie les angles). À score égal, préfère ce qui demande une action de Lucie à ce qui est purement informatif. Une ligne par info : l'essentiel en une phrase, pourquoi c'est important en quelques mots, canal, lien. S'il y a moins de 3 infos réellement importantes, n'en mets que 2 ou 1 plutôt que de combler.

## 6. Publier dans Notion

Page cible : `https://app.notion.com/p/d813575afd7441e6b2d6a6e8c6e7affc` (page « Lucie »). À chaque exécution, ajoute un nouveau toggle **tout en haut** de la page, sans toucher au reste.

1. Lis la spécification Notion avec l'outil fetch sur `notion://docs/notion-flavored-markdown-spec` (syntaxe des toggles, des tableaux et des liens), car la syntaxe exacte compte.
2. Utilise `notion-update-page` avec `command: "insert_content"` et `position: {"type": "start"}`. N'utilise jamais `replace_content` sur cette page : elle contient les notes personnelles de Lucie et des bases de données. (`update_content` est autorisé uniquement pour corriger un tableau du toggle du jour que tu viens de créer.)
3. Le titre du toggle est `Récap du Jour — <jour> <JJ/MM/AAAA>` (date du jour de génération). Le contenu du toggle suit exactement ce gabarit :

```
Fenêtre : <début> → <fin> · Sources : canaux 2-/5-, DMs/groupes, mentions, Gmail

## Top infos
1. <info> — <pourquoi important> · <canal> · <lien>
2. ...
3. ...

## Sujets traités
Tableau : Titre | Ce que j'ai fait | Canal | Lien | Next step potentielle

## À traiter
Tableau : Sujet (concis) | Ce que je dois faire | Canal | Importance | Lien

## Relance à faire
Tableau : Sujet (concis) | Ce que je dois faire | Canal | Importance | Lien

## En plus
Liste : sujet pertinent · canal · lien
```

Règles de rédaction :

- Dans « Relance à faire », la colonne « Ce que je dois faire » dit qui relancer, sur quoi, depuis combien de jours ouvrés, et contient un brouillon de message de relance de une à deux phrases, cordial, dans la langue de la conversation. Ce brouillon est écrit dans Notion, pas dans Slack : Lucie le copie et l'envoie elle-même.
- « Next step potentielle » peut rester vide (« — ») quand il n'y en a pas, ne l'invente pas.
- Pour un mail, la colonne « Canal » contient `Gmail (<prénom de l'expéditeur>)` et le lien est celui de la conversation Gmail.
- Pour Slack, le lien est le permalien Slack du message ou du fil. Le workspace est `hability-permanents.slack.com` : le format est `https://hability-permanents.slack.com/archives/<channel_id>/p<ts sans le point>`. Pour un message dans un fil, ajoute `?thread_ts=<ts du parent>&cid=<channel_id>`. Mets le nom du canal avec son `#`.
- Une ligne par sujet, paraphrase, pas de longues citations, prénoms plutôt qu'identifiants.
- Si une section est vide, écris « Rien » : cela confirme que tu as regardé.

## 7. Réponse dans le chat

Après publication, donne le lien de la page Notion, puis 3 à 5 lignes : nombre d'éléments à traiter et de relances, le point le plus urgent. Ne recopie pas le recap. Signale ce qui n'a pas pu être couvert (canal inaccessible, fil introuvable, DMs sans résultat, volume élevé survolé).

Si Slack ou Notion n'est pas disponible dans la session, dis-le et invite Lucie à connecter le connecteur correspondant dans claude.ai, sans inventer de contenu. Si seul Gmail manque, fais quand même le recap Slack, écris en tête du toggle « Gmail non couvert (connecteur absent) » et invite Lucie à connecter Gmail dans claude.ai.