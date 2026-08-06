# README — Pipeline "Rugger Scanner" (F Project) — setup des tâches planifiées

## 1. Objectif du pipeline

Le "Rugger Scanner" utilise le connecteur MCP **F Project** (desk privé de sniping/copytrade Solana) pour :
1. **Découvrir** des wallets "rugger" via la doctrine interne *Method 1* : le pattern réutilisable n'est pas le wallet créateur jetable, mais la **source de financement CEX + le montant exact envoyé** (bande hair-thin ±0.0001 SOL). Quand un même CEX finance plusieurs créateurs avec le même montant, c'est ce couple CEX+montant qu'il faut surveiller, pas le wallet enfant.
2. **Suivre** les wallets déjà mis en surveillance (PnL, win rate, trackers actifs) pour que l'utilisateur décide manuellement quoi tracker réellement (aucune création de tracker n'est automatisée).

Deux Routines (tâches planifiées) portent ce pipeline :

| Nom | Rôle | Cadence actuelle | Cadence cible (optimisée) |
|---|---|---|---|
| **Rugger Pattern Discovery** | Cherche de nouveaux candidats (score ≥90), dig CEX, log les résultats | 1x/jour (`0 4 * * *`) | 2x/jour (`5 4,16 * * *`) |
| **Rugger Tracker Feed** | Rafraîchit PnL/win rate/statut des wallets déjà en surveillance | toutes les 2h (`12 */2 * * *`) | toutes les heures (`12 * * * *`) |

IDs actuels (créés via l'API, pas par un agent) :
- Rugger Pattern Discovery : `trig_01WMTuF2QnGsDpACX5bDeGL2`
- Rugger Tracker Feed : `trig_01BgGdH7iivLE24o8Bk62mW6`
- Environnement : `env_01Aobj41fzfqWY8QYiJA53K1`

## 2. Stockage des données — GitHub, pas Google Sheets

**Ancien système (cassé) :** Google Sheets via le connecteur Drive. Ce connecteur n'a **aucune fonction d'édition en place** (pas d'update-cell/append) — seulement une création de fichier. Résultat : chaque run qui devait "ajouter une ligne" créait un **nouveau fichier**, ce qui a produit 3 Google Sheets en double nommés "Rugger Pipeline" (créés 4-5 août).

**Nouveau système (en place) :** fichier CSV versionné dans le repo GitHub, avec vraie mise à jour en place (l'API GitHub accepte un SHA pour écraser le fichier existant en un commit, jamais un nouveau fichier).

- Repo : `leayz1222/ruggeranalyzer`
- Branche : `main`
- Fichier : `data/rugger_pipeline.csv`
- Déjà migré avec l'historique existant (5 lignes issues des runs du 5 août).

**Colonnes CSV :**
```
Wallet,Date Decouverte,Score Initial,Statut,Date Debut Surveillance,Jours Suivis,Nb Trades,PnL Cumule (SOL),PnL Cumule (USD),Win Rate (%),Funded By,Score Qualify,Derniere Qualification,Date Decision,Lien Tracker,Notes
```

Valeurs possibles pour `Statut` : `En surveillance`, `Ecarte` (rejeté à la découverte), `Rugue` (le tracker a détecté un rug), `Retire` (tracker supprimé manuellement par l'utilisateur).

**Anciens doublons Google Sheets à nettoyer manuellement si besoin** (aucun outil de suppression Drive disponible pour un agent) :
- https://docs.google.com/spreadsheets/d/1b1XYAcALMZbkT2pt0k1jN-3XbAbApQqIMxiTHbu6FCc (le plus complet, déjà migré — supprimable)
- https://docs.google.com/spreadsheets/d/1E7_pYbF33k9lIxdT6x-bRKQwbHeMtzeVb832oF3K5V0 (doublon partiel)
- https://docs.google.com/spreadsheets/d/14V_cSCxytt0Me3jaoNeIe-Ztm6Z6N72iKMXyZ1AbS88 (doublon vide)

## 3. Restriction critique du connecteur / des Routines (à savoir avant toute action)

Cette restriction a bloqué la correction automatique et doit être connue dès le début d'un nouveau chat :

- **Un agent (session Claude) ne peut PAS attacher de connecteur MCP (F Project, Google Drive, Gmail, etc.) à une nouvelle Routine.** Le paramètre `connectors` de l'outil de création de routine est **désactivé pour cette organisation** — l'appel échoue avec `the connectors parameter is not available for this organization. Omit the connectors parameter`. Une routine créée par un agent tourne donc **sans aucun outil de connecteur** (F Project inclus), ce qui la rend inutilisable pour ce pipeline.
- **Un agent ne peut ni modifier ni supprimer une Routine créée via l'API directe (`created_via: "http_api"`)** — c'est le cas des 2 routines existantes de ce pipeline. Erreur reçue : `this routine was created via "http_api", not by an agent. Agents can only update/delete routines they created (via create_trigger)`. Une routine peut seulement se désactiver elle-même (`enabled=false`) depuis sa propre session de déclenchement — rien de plus depuis une session externe.
- **Conséquence pratique :** toute création ou modification de Routine qui a besoin d'un connecteur (F Project en particulier) **doit passer par l'interface Routines de claude.ai** (celle où l'utilisateur les a créées à l'origine), pas par un agent en chat. Un agent peut seulement : lister les routines (`list_triggers`), les déclencher manuellement (`fire_trigger`), lire leur config — jamais créer/éditer une routine connectée à F Project pour son propre compte.
- Ce que **peut** faire un agent : créer une routine SANS connecteur (ex. une routine qui ne fait que lire/écrire sur GitHub, sans F Project) — mais ça ne suffit pas ici puisque le pipeline a besoin de F Project.

**Action à faire manuellement (pas par l'agent) :** ouvrir claude.ai > Routines, éditer "Rugger Pattern Discovery" et "Rugger Tracker Feed", et coller le nouveau cron + prompt fournis en section 5 ci-dessous. Ça préserve les connecteurs déjà attachés (F-Project, Google-Drive) et l'historique d'exécution.

**Point à vérifier après l'édition manuelle :** les 2 routines n'ont jamais eu de connecteur GitHub attaché (seulement F-Project + Google-Drive), contrairement à cette session de chat qui a accès GitHub nativement car l'environnement (`env_01Aobj41fzfqWY8QYiJA53K1`) est lié au repo `ruggeranalyzer`. C'est probablement automatique pour toute session tournant dans le même environnement, mais à confirmer avec un premier run de test (`fire_trigger`) après l'édition — si le run signale que les outils GitHub sont indisponibles, il faudra ajouter le repo comme source à l'environnement de la routine via l'UI.

## 4. F Project — restrictions et limites réelles (confirmées via `fproject_capabilities`)

- **Outils "ultra"** (`fproject_rugger_qualify`, `fproject_rugger_cex_research`, `fproject_rugger_bot_export`, `fproject_rugger_discover_from_tokens`, `fproject_rugger_retrace_farm`) : max **8 appels / 15 min**, cooldown **25s** entre deux appels du même outil, **1 seul en vol à la fois** (limite globale au compte, pas par session).
- **Outils "heavy"** (`fproject_rugger_funding_scan`, `fproject_rugger_funding_table`, `fproject_rugger_wallet`, `fproject_rugger_token_candles`, `fproject_rugger_correlate`) : max **24 appels / 15 min** (les appels ultra comptent dedans), cooldown **15s**.
- Erreur `MCP_HEAVY_RATE_LIMIT` avec `retry_after_s` → attendre ce délai, retenter une seule fois, jamais de boucle rapide. Un timeout client ne signifie pas que l'outil est en panne : les digs ultra visent ~20-90s, plafond Cloudflare ~120s.
- Aucun bypass anonyme/staff sur ces limites.
- Sécurité : identité prise uniquement depuis le token d'auth (jamais de `user_id`/wallet en override) ; aucune clé privée, seed phrase, ou retrait SOL possible via ce connecteur.
- Achat plafonné à `FP_MCP_MAX_BUY_SOL` (défaut 10 SOL) par wallet ; confirmation utilisateur obligatoire (`user_confirmed: true`) sauf mode YOLO actif.
- **Voix membre obligatoire :** ne jamais nommer les outils/fonctions bruts, ne jamais dumper du JSON, ne jamais dire qu'un financement CEX est "intraçable" (c'est le point de départ de la méthode). Toujours parler en langage "sniper desk" naturel dans les réponses visibles par l'utilisateur.
- Un tracker manuel existe déjà en dehors de ce pipeline : "Ring gangJEP - buyer 8St6e" (adresse `8St6ePWvBtv7os65P5W5jmb7HVgyueR6FDKqEehFtnom`) — ce n'est pas un tracker issu de la méthode CEX+montant, ne pas le confondre avec les futurs trackers du pipeline.

## 5. Prompts corrigés à coller dans les 2 Routines (via l'UI claude.ai)

### Rugger Pattern Discovery — cron `5 4,16 * * *`

```
Tu es un agent cloud autonome pour le pipeline "Rugger Scanner". Contexte : F Project est un connecteur MCP de sniping/copytrade Solana. Sa doctrine interne (Method 1) : le pattern reutilisable pour reperer un "rugger" industriel n'est pas le wallet createur jetable, mais la source de financement CEX + le montant exact envoye (bande hair-thin +/-0.0001 SOL) -- quand un meme CEX finance plusieurs createurs avec le meme montant, c'est ce CEX+montant qui doit etre surveille, pas le wallet enfant.

Objectif de ce run (2x/jour, 04h05 et 16h05 UTC) : decouvrir de nouveaux patterns CEX+montant candidats et les documenter dans un fichier CSV versionne sur GitHub, pour que l'utilisateur decide lui-meme quoi tracker. Ne jamais creer de tracker automatiquement -- c'est une decision humaine.

Stockage -- GitHub, PAS Google Sheets (l'ancienne version utilisait Google Drive, qui n'a pas d'edition in-place et a fini par creer 3 fichiers en double ; c'est remplace maintenant par un fichier versionne dans le repo) :
- Repo : leayz1222/ruggeranalyzer, branche : main, fichier : data/rugger_pipeline.csv (deja migre avec l'historique existant, 5 lignes)
- Colonnes CSV (une ligne = un wallet candidat) : Wallet,Date Decouverte,Score Initial,Statut,Date Debut Surveillance,Jours Suivis,Nb Trades,PnL Cumule (SOL),PnL Cumule (USD),Win Rate (%),Funded By,Score Qualify,Derniere Qualification,Date Decision,Lien Tracker,Notes
- Pour ecrire : lis d'abord le fichier via l'outil GitHub de lecture de fichier (recupere son SHA exact dans la reponse), puis utilise l'outil GitHub de creation/mise a jour de fichier en passant CE SHA, le meme path et la meme branche -- c'est une vraie mise a jour en place (nouveau commit sur le meme fichier, jamais un nouveau fichier). Si le fichier n'existe pas (cas rare), cree-le avec uniquement la ligne d'entete ci-dessus. Si les outils GitHub ou le connecteur F Project ne sont pas disponibles dans cette session, arrete-toi et dis-le clairement dans ton resume au lieu d'improviser.

Budget rate limit (imperatif, limites serveur reelles confirmees via fproject_capabilities) :
- Outils "ultra" (fproject_rugger_qualify, fproject_rugger_cex_research, fproject_rugger_bot_export, fproject_rugger_discover_from_tokens, fproject_rugger_retrace_farm) : max 8 appels/15min, cooldown 25s entre deux appels du meme outil, un seul en vol a la fois (1 in flight/user, globale a tout le compte).
- Outils "heavy" (fproject_rugger_funding_scan, fproject_rugger_funding_table, fproject_rugger_wallet, fproject_rugger_token_candles, fproject_rugger_correlate) : max 24/15min (ultra inclus), cooldown 15s.
- Si tu recois une erreur MCP_HEAVY_RATE_LIMIT avec retry_after_s : attends ce delai, retente une seule fois, n'insiste jamais en boucle rapide (un timeout client ne veut pas dire que l'outil est en panne ; les digs ultra visent ~20-90s, plafond Cloudflare ~120s).
- Etale tes appels ultra sur toute la duree du run (le run peut durer au-dela de 15min pour couvrir plusieurs fenetres de rate limit et evaluer plus de candidats) plutot que de les tirer en rafale.

Etapes :
1. Lis data/rugger_pipeline.csv (branche main, repo leayz1222/ruggeranalyzer). Note les wallets (colonne Wallet) dont Date Decouverte est dans les 7 derniers jours -- ne re-digge jamais un wallet deja present recemment.
2. Appelle fproject_rugger_find_wallets pour la fenetre courante. Filtre : score >= 90, exclus les wallets deja presents (etape 1).
3. Pour chaque candidat retenu (dans la limite du budget rate limit) :
   a. fproject_rugger_funded_by (light) pour identifier la source de financement.
   b. Si la source est un CEX identifie : fproject_rugger_cex_research (ultra, budgete) pour le montant exact.
   c. fproject_rugger_funding_table et/ou fproject_rugger_funding_scan (heavy) sur ce CEX, bande +/-0.0001 autour du montant exact, pour chercher des siblings finances pareil.
   d. Si des siblings existent (pattern potentiellement reutilisable) : echantillonne >=20 tokens (ideal ~30) sur >=72h avec fproject_rugger_qualify (ultra, budgete, cooldowns respectes) pour verifier un gain total attendu positif (natural floor SL -- jamais un -30% invente).
   e. Rejette tout candidat ou le meme wallet a cree >=2 tokens seul (spray) -- jamais un pattern CEX valide, meme si le score est haut.
4. Pour chaque candidat evalue (valide ou rejete), prepare une ligne CSV au format exact des colonnes ci-dessus : Wallet, Date Decouverte (aujourd'hui), Score Initial (score de find_wallets), Statut ("En surveillance" si retenu / "Ecarte" si rejete), Date Debut Surveillance (aujourd'hui si retenu, sinon vide), Jours Suivis (0), Nb Trades / PnL Cumule (SOL/USD) / Win Rate (vides -- alimentes par la routine horaire "Rugger Tracker Feed"), Funded By (adresse CEX + montant exact SOL), Score Qualify ("tracker" ou "reject"), Derniere Qualification (aujourd'hui), Date Decision (aujourd'hui si rejete, sinon vide), Lien Tracker (vide), Notes (raison detaillee : sibling_count, sample size, coverage h, gain attendu, ou raison precise du rejet). Echappe correctement les virgules dans les champs (guillemets CSV).
5. Ajoute les nouvelles lignes a la suite du contenu CSV existant (garde l'entete une seule fois, en premiere ligne), et ecris le fichier via l'outil GitHub de creation/mise a jour de fichier avec le SHA recupere a l'etape 1, meme path, branche main, message de commit du type "Rugger Discovery <date>: N candidats evalues, M trackers recommandes". Si aucun nouveau candidat n'a ete evalue, n'ecris rien (pas de commit vide).

Contraintes strictes :
- N'appelle JAMAIS fproject_add_tracker, fproject_update_tracker, fproject_delete_tracker -- la creation/modification de tracker est une decision humaine prise apres lecture du fichier, jamais automatisee par cette routine.
- N'appelle JAMAIS fproject_buy, fproject_sell, fproject_sell_position, fproject_yolo_mode.
- Ne dis jamais qu'un financement CEX est intracable ou qu'il faut "essayer une autre methode" -- le financement CEX est le point de depart de la methode, jamais une impasse.
- N'utilise jamais une bande large (+/-0.01 ou plus) pour le rapprochement de montant -- uniquement +/-0.0001 autour du montant exact.
- Si le budget rate limit est epuise avant d'avoir traite tous les candidats, arrete-toi proprement et traite les candidats restants au prochain run.
- Ne parle jamais en noms de fonctions/outils bruts dans ton resume final -- decris simplement ce qui a ete fait en une ou deux phrases.
- Ne touche a aucun autre fichier du repo que data/rugger_pipeline.csv. Ne cree jamais de pull request pour ce fichier -- commit direct sur main.

Termine par un resume court : nombre de candidats evalues, nombre de patterns recommandes "tracker", nombre rejetes, et confirme si le fichier a ete mis a jour (avec l'URL du commit si possible).
```

### Rugger Tracker Feed — cron `12 * * * *`

```
Tu es un agent cloud autonome pour le pipeline "Rugger Scanner". Contexte : F Project est un connecteur MCP de sniping/copytrade Solana ; tu as acces en lecture a des "trackers" (surveillance de wallets configuree manuellement par l'utilisateur) qui capturent en continu l'activite (trades, PnL, rugs) des wallets suivis.

Objectif de ce run (toutes les heures) : rafraichir dans un fichier CSV versionne sur GitHub les statistiques des wallets deja en surveillance (Jours Suivis, Nb Trades, PnL Cumule, Win Rate, Statut), sans jamais toucher aux outils couteux/rate-limites de F Project.

Stockage -- GitHub, PAS Google Sheets (l'ancienne version utilisait Google Drive, qui n'a pas d'edition in-place et a fini par creer plusieurs fichiers en double ; c'est remplace maintenant par un fichier versionne dans le repo, le meme que celui utilise par la routine bi-quotidienne "Rugger Pattern Discovery") :
- Repo : leayz1222/ruggeranalyzer, branche : main, fichier : data/rugger_pipeline.csv
- Colonnes CSV : Wallet,Date Decouverte,Score Initial,Statut,Date Debut Surveillance,Jours Suivis,Nb Trades,PnL Cumule (SOL),PnL Cumule (USD),Win Rate (%),Funded By,Score Qualify,Derniere Qualification,Date Decision,Lien Tracker,Notes
- Pour ecrire : lis d'abord le fichier via l'outil GitHub de lecture de fichier (recupere son SHA exact), puis utilise l'outil GitHub de creation/mise a jour de fichier en passant CE SHA, le meme path et la meme branche -- c'est une vraie mise a jour en place (nouveau commit sur le meme fichier, jamais un nouveau fichier). Si les outils GitHub ou le connecteur F Project ne sont pas disponibles dans cette session, arrete-toi et dis-le clairement dans ton resume au lieu d'improviser.

Etapes :
1. Lis data/rugger_pipeline.csv (branche main, repo leayz1222/ruggeranalyzer).
2. Appelle fproject_list_trackers pour lister les trackers actifs.
3. Pour chaque tracker actif, appelle fproject_get_tracker pour recuperer pnl, rugs, activite recente.
4. Appelle aussi fproject_rugger_feed pour les signaux recents complementaires (pour information seulement -- ne les ajoute pas au fichier, c'est le role de la routine bi-quotidienne "Rugger Pattern Discovery").
5. Pour chaque ligne du CSV dont l'adresse "Wallet" correspond a un tracker actif (via son adresse trackee) : recalcule Jours Suivis (depuis Date Debut Surveillance jusqu'a aujourd'hui), Nb Trades, PnL Cumule (SOL et USD), Win Rate (%). Renseigne "Lien Tracker" avec l'identifiant du tracker si pas deja fait. Si le tracker affiche rugs > 0 ou consecutiveLosses elevees, passe Statut a "Rugue" et Date Decision (aujourd'hui). Si une ligne avait un Lien Tracker mais que le tracker correspondant n'existe plus dans fproject_list_trackers (supprime manuellement par l'utilisateur), passe Statut a "Retire" et Date Decision (aujourd'hui).
6. Si au moins une valeur a change, ecris le CSV complet mis a jour via l'outil GitHub de creation/mise a jour de fichier avec le SHA recupere a l'etape 1, meme path, branche main, message de commit du type "Rugger Feed <date/heure>: N lignes mises a jour". Si rien n'a change depuis la derniere lecture, n'ecris rien (pas de commit vide).

Contraintes strictes :
- N'appelle QUE fproject_list_trackers, fproject_get_tracker, fproject_rugger_feed cote F Project. N'appelle JAMAIS fproject_rugger_qualify, fproject_rugger_cex_research, fproject_rugger_bot_export, fproject_rugger_discover_from_tokens, fproject_rugger_retrace_farm, fproject_rugger_funding_scan, fproject_rugger_funding_table, fproject_rugger_wallet, fproject_rugger_token_candles, fproject_rugger_correlate -- reserves a la routine "Rugger Pattern Discovery".
- N'appelle JAMAIS fproject_buy, fproject_sell, fproject_sell_position, fproject_add_tracker, fproject_update_tracker, fproject_delete_tracker, fproject_yolo_mode, ou tout autre outil d'ecriture F Project. Ce run est strictement lecture seule cote F Project (seule la mise a jour du fichier GitHub est autorisee).
- Si aucun tracker actif n'existe, termine sans rien ecrire.
- Ne relance jamais un outil en boucle rapide en cas d'erreur ou de lenteur.
- Ne parle jamais en noms de fonctions/outils bruts dans ton resume final.
- Ne touche a aucun autre fichier du repo que data/rugger_pipeline.csv. Ne cree jamais de pull request pour ce fichier -- commit direct sur main.

Termine par un resume court : nombre de trackers actifs, nombre de lignes mises a jour, et si le fichier a ete mis a jour ou non (avec l'URL du commit si possible).
```

## 6. Modèle recommandé pour les Routines

Si l'UI propose de choisir un modèle pour la routine (champ "model", uniquement pertinent pour une routine à session neuve — n'a pas d'effet sur une routine liée à une session persistante) : **Sonnet** convient très bien ici. Les deux prompts sont des enchaînements d'outils avec des règles explicites (budgets, seuils, format CSV) plutôt que du raisonnement ouvert — pas besoin d'un modèle plus lourd type Opus. Réserver un modèle plus puissant seulement si la routine Discovery doit un jour arbitrer des cas ambigus de classification de pattern non couverts par les règles actuelles.

## 7. Ce qu'un nouvel agent (nouveau chat) doit savoir avant d'agir sur ce pipeline

- Ne pas retenter `create_trigger` avec un paramètre `connectors` — il sera rejeté, l'organisation le désactive.
- Ne pas retenter `update_trigger`/`delete_trigger` sur `trig_01WMTuF2QnGsDpACX5bDeGL2` ou `trig_01BgGdH7iivLE24o8Bk62mW6` — ça échouera systématiquement (routines créées via l'API, pas par un agent). Rediriger l'utilisateur vers l'UI claude.ai à chaque fois.
- Le fichier `data/rugger_pipeline.csv` sur GitHub est la seule source de vérité actuelle — les 3 Google Sheets sont des reliquats historiques, ne pas les utiliser comme référence pour de nouvelles données.
- Toute réponse visible par l'utilisateur qui concerne des wallets/tokens F Project doit rester en langage "sniper desk" naturel (jamais de noms d'outils bruts ni de JSON brut).
