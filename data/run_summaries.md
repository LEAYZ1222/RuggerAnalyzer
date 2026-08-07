# Rugger Scanner — journal des runs

## Run R-20260806-1730 — 2026-08-06 17:30 UTC

### Init
- Premier run du pipeline pattern-centric : `data/patterns.csv`, `data/wallets.csv` et `data/run_log.csv` n'existaient pas encore sur GitHub (seul l'ancien `rugger_pipeline.csv` était présent).
- Migration one-shot effectuée : 54 wallets historiques repris dans `wallets.csv`. 3 d'entre eux avaient un financement CEX exploitable (Coinbase 1.7999, Binance 9.650504, KuCoin 3.424) → créés comme patterns P-001, P-002, P-003, tous **Écartés** (aucun sibling trouvé dans la bande hair-thin lors de l'investigation d'origine). Les 51 autres lignes restent sans Pattern ID (financeur non labellisé ou spray).

### Refresh
- 1 tracker actif détecté ("Ring gangJEP - buyer 8St6e"), 0 trade, aucune activité sur les 7 derniers jours.
- Ce tracker ne correspond à aucune ligne de `wallets.csv` (créé hors de ce pipeline) → rien à recalculer côté Jours Suivis / PnL / Win Rate ce run.
- Aucun pattern en statut "En surveillance" ou "Rentable" à date → aucune transition automatique de statut.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- L'outil de découverte principal (recherche de wallets fraîchement financés) a répondu en échec technique sur 3 tentatives distinctes (paramètres par défaut, puis avec filtres, puis sans aucun paramètre) : **outil indisponible ce run**.
- Bascule sur la voie de secours (intel créateurs récents) : échantillon de créateurs single-launch financés par un CEX passé en revue, aucun ne dépasse le score plancher de 90 (meilleur score observé : 40). **Aucun candidat n'a atteint la gate B1** → 0 ultra dépensé, budget intact pour le prochain run.
- Aucun ultra ni heavy consommé ce run.

### Bilan
- Fichiers créés/mis à jour : patterns.csv (3 lignes), wallets.csv (54 lignes), run_log.csv, run_summaries.md — commit unique sur `main`.
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé). Si l'outil de découverte reste en échec au prochain run, vérifier côté F Project.

## Run R-20260806-1742 — 2026-08-06 17:42 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- Outil principal de découverte de wallets fraîchement financés de nouveau indisponible ce run (3 tentatives) — même comportement qu'au run précédent.
- Bascule sur la voie de secours (intel créateurs récents) : 50 créateurs récents passés en revue. 4 financés par un CEX labellisé et sans spray (créateur single-launch, pas de multi-token) relevés :
  - score 58, financé par la même adresse Binance que le pattern P-002 (déjà écarté, isolé)
  - score 56, même Binance, token gradué (survie 100% sur l'échantillon d'1 token — non significatif)
  - score 48, financé par la même adresse Coinbase que le pattern P-001 (déjà écarté, isolé)
  - score 40, financé par un CEX distinct non encore vu
- Aucun ne franchit le seuil de score 90 de la gate B1 → **0 candidat retenu, 0 ultra ni heavy dépensé**.

### Bilan
- Aucun changement sur `patterns.csv` / `wallets.csv` ce run — pas de commit sur ces fichiers.
- Fichiers mis à jour : `run_log.csv`, `run_summaries.md` (commit unique sur `main`).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run. Si l'outil de découverte principal reste en échec sur plusieurs runs consécutifs, vérifier la disponibilité côté F Project.

## Run R-20260806-1825 — 2026-08-06 18:25 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- L'outil principal de découverte de wallets fraîchement financés a répondu normalement ce run (1035 wallets scannés sur 14j, 50 au-dessus du seuil de score 90).
- 5 wallets neufs (jamais vus, hors fenêtre anti-redig de 7 jours) sur les 50 :
  - 3 disqualifiés immédiatement en gate B1 pour **spray** (créateur de 4, 7 et 4 tokens sur son propre wallet — jamais un pattern CEX valide, quel que soit le score).
  - 2 ont passé la gate B1 propre (score 95, créateur single-launch, pas de spray) mais **échoué en gate B2** : financeur trouvé dans les deux cas, mais aucun n'est un exchange labellisé (un funder générique à 0.0037 SOL, un hub à 154.32 SOL) → **Écartés**, aucun ultra engagé.
- Aucun candidat n'a atteint B3 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Fichiers mis à jour : `wallets.csv` (+5 lignes, toutes Écartées), `run_log.csv`, `run_summaries.md` — commit unique sur `main`. `patterns.csv` inchangé (pas de commit sur ce fichier).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260806-1925 — 2026-08-06 19:25 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- 1034 wallets scannés (fenêtre 14j), 50 au-dessus du seuil de score 90. Après filtrage des 46 déjà connus (anti-redig 7 jours), 4 candidats neufs :
  - 2 disqualifiés immédiatement en gate B1 pour spray (créateurs de 3 et 11 tokens sur leur propre wallet).
  - 2 ont passé la gate B1 propre (score 95, single-launch) mais échoué en gate B2 : l'un a un funder direct non labellisé et un profil de hub/routeur (un flux Binance de 207 SOL existe bien dans son historique, mais noyé dans un volume de centaines de SOL sur des dizaines de transferts — aucun événement de financement isolé hair-thin exploitable, ce n'est pas un enfant Method-1 propre), l'autre financé par un hub générique déjà vu sur un wallet précédemment écarté.
- Aucun candidat n'a atteint B3 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Fichiers mis à jour : `wallets.csv` (+4 lignes, toutes Écartées), `run_log.csv`, `run_summaries.md` — commit unique sur `main`. `patterns.csv` inchangé (pas de commit sur ce fichier).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260806-2026 — 2026-08-06 20:26 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- 417 wallets scannés (fenêtre 14j), 50 au-dessus du seuil de score 90 (outil principal répondu au 2e essai, 1er différé par l'analyste). Après filtrage des 49 déjà connus (anti-redig 7 jours), 1 seul candidat neuf :
  - disqualifié immédiatement en gate B1 pour spray (créateur de 2 tokens sur son propre wallet, score 95).
- Aucun candidat n'a atteint la gate B2 ni B3 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Fichiers mis à jour : `wallets.csv` (+1 ligne, Écartée), `run_log.csv`, `run_summaries.md` — commit sur `main`. `patterns.csv` inchangé (pas de commit sur ce fichier).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260806-2124 — 2026-08-06 21:24 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- Outil principal de découverte de wallets fraîchement financés indisponible ce run (3 tentatives, différé à chaque fois par l'analyste).
- Bascule sur la voie de secours (intel créateurs récents) : 50 créateurs récents passés en revue, triés par activité la plus fraîche. Meilleur score observé : 60, sur un créateur single-launch financé par l'exchange déjà connu via P-001 (Coinbase) — score encore loin sous le seuil de 90.
- Aucun candidat n'a atteint la gate B1 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Aucun changement sur `patterns.csv` / `wallets.csv` ce run — pas de commit sur ces fichiers.
- Fichiers mis à jour : `run_log.csv`, `run_summaries.md` (commit unique sur `main`).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260806-2224 — 2026-08-06 22:24 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- 1036 wallets scannés (fenêtre 14j), 50 au-dessus du seuil de score 90. Après filtrage des 41 déjà connus, 9 candidats neufs :
  - 7 disqualifiés immédiatement en gate B1 pour spray (créateurs de 2 à 16 tokens sur leur propre wallet — jamais un pattern CEX valide).
  - 2 ont passé la gate B1 propre (score 95, créateurs single-launch, pas de spray) mais échoué en gate B2 : les deux ont un financeur direct identifié (3.5 SOL et 1.1667 SOL), mais dans les deux cas c'est une adresse générique de type "mother" sans aucun label exchange — pas de CEX identifiable malgré la revue des financeurs entrants (22 et 7 respectivement).
- Aucun candidat n'a atteint B3 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Fichiers mis à jour : `wallets.csv` (+9 lignes, toutes Écartées), `run_log.csv`, `run_summaries.md` — commit `9ec419f` sur `main`. `patterns.csv` inchangé (pas de commit sur ce fichier).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260806-2324 — 2026-08-06 23:24 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- 1038 wallets scannés (fenêtre 14j), 50 au-dessus du seuil de score 90. Après filtrage des 45 déjà connus, 5 candidats neufs :
  - Les 5 sont disqualifiés immédiatement en gate B1 pour **spray** (créateurs de 2, 7, 2, 2 et 9 tokens sur leur propre wallet — jamais un pattern CEX valide, quel que soit le score affiché, qui allait de 95 à 100).
- Aucun candidat n'a atteint la gate B2 ni au-delà → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Fichiers mis à jour : `wallets.csv` (+5 lignes, toutes Écartées, commit `d2a9630`), `run_log.csv`, `run_summaries.md` — sur `main`. `patterns.csv` inchangé (pas de commit sur ce fichier).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260807-0029 — 2026-08-07 00:29 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- Run chargé : 1034 wallets scannés (fenêtre 14j), 159 au-dessus du seuil de score 90 — nettement plus que les runs précédents. Après filtrage des 77 déjà connus, **82 candidats neufs**.
- Vérification individuelle des 10 candidats signalés le plus propres (1 seul token créé selon le scanner) : 4 se sont révélés être du spray réel une fois le compte de tokens vérifié en profondeur (2 à 4 tokens créés, pas 1) — le signal brut du scanner de découverte sous-estimait. Les 6 restants n'ont **aucun CEX identifiable** (financeur générique ou hub, pas de label exchange ; un cas avec 156 créateurs liés au même financeur, profil "ferme de lancement").
- Contrôle qualité sur 11 candidats supplémentaires signalés à 2 créations : les 11 confirmés spray réel (jusqu'à 46 tokens créés pour le pire cas) → fiabilise le signal du scanner pour le reste du lot.
- **Deux near-miss notables** : `GrhYtk4...556PP` et `HV7oqX...SSYyL` sont bel et bien financés par un exchange réel (montant exact) — mais l'un a créé 3 tokens, l'autre 2. Spray disqualifie immédiatement, quel que soit le funder ou le score (règle non négociable du desk).
- Les 71 candidats restants (2 à 24 créations selon le scanner) écartés en masse sur le même signal spray, désormais fiabilisé.
- **Aucun candidat n'a atteint la gate B2 propre (spray-free + CEX confirmé)** → 0 ultra ni heavy dépensé, budget plein pour le prochain run.

### Bilan
- Fichiers mis à jour : `wallets.csv` (+82 lignes, toutes Écartées, commit `c03505f`), `run_log.csv`, `run_summaries.md` — sur `main`. `patterns.csv` inchangé (pas de commit sur ce fichier).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente). À noter pour information : deux créateurs financés par un exchange réel repérés ce run, mais tous deux disqualifiés pour spray — rien d'actionnable, juste un signe que le flux CEX est bien vivant en ce moment.

## Run R-20260807-0222 — 2026-08-07 02:22 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- Run calme après le pic du précédent : 1035 wallets scannés (fenêtre 14j), 159 au-dessus du seuil de score 90. Après filtrage des 157 déjà connus, seulement **2 candidats neufs** :
  - `8ia5V...ndpdT` (score 95) : disqualifié immédiatement en gate B1 pour spray (créateur de 15 tokens sur son propre wallet).
  - `AtVyn...B TBdxZ1` (score 95) : disqualifié immédiatement en gate B1 pour spray (créateur de 3 tokens sur son propre wallet).
- Aucun candidat n'a atteint la gate B2 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Fichiers mis à jour : `wallets.csv` (+2 lignes, toutes Écartées, commit `8d392f1`), `run_log.csv`, `run_summaries.md` — sur `main`. `patterns.csv` inchangé (pas de commit sur ce fichier).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260807-0335 — 2026-08-07 03:35 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- Run calme : 415 wallets scannés (fenêtre 14j), 50 au-dessus du seuil de score 90. Après filtrage des 49 déjà connus, 1 seul candidat neuf :
  - `Bg4DJ...o2adp8` (score 95) : disqualifié immédiatement en gate B1 pour spray (créateur de 5 tokens sur son propre wallet).
- Aucun candidat n'a atteint la gate B2 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Fichiers mis à jour : `wallets.csv` (+1 ligne, Écartée, commit `a0f39b8`), `run_log.csv`, `run_summaries.md` — sur `main`. `patterns.csv` inchangé (pas de commit sur ce fichier).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260807-0423 — 2026-08-07 04:23 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- 1033 wallets scannés (fenêtre 14j), 151 au-dessus du seuil de score 90. Après filtrage des 150 déjà connus, 1 seul candidat neuf :
  - `Adzjzx...urPfGm6j` (score 95) : vérification profonde immédiate (le signal brut affichait 1 seul token) — en réalité créateur de 5 tokens sur une fenêtre de 2 minutes, cluster de type ferme de lancement avec 12 créateurs liés au même profil, wallet vieux d'à peine 1 jour. Disqualifié en gate B1 pour spray. Le financeur n'était de toute façon pas un exchange labellisé.
- Aucun candidat n'a atteint la gate B2 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Fichiers mis à jour : `wallets.csv` (+1 ligne, Écartée, commit `d52e517`), `run_log.csv`, `run_summaries.md` — sur `main`. `patterns.csv` inchangé (pas de commit sur ce fichier).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260807-0530 — 2026-08-07 05:30 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- 1033 wallets scannés (fenêtre 14j), 152 au-dessus du seuil de score 90. Après filtrage des 150 déjà connus, 2 candidats neufs :
  - `B8e3Y...ygDnumD` (score 95) : disqualifié immédiatement en gate B1 pour spray (créateur de 4 tokens sur son propre wallet).
  - `gMTSn...5Tq9aA` (score 100) : disqualifié immédiatement en gate B1 pour spray (créateur de 13 tokens sur son propre wallet).
- Aucun candidat n'a atteint la gate B2 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Fichiers mis à jour : `wallets.csv` (+2 lignes, toutes Écartées, commit `95641ff`, correction `8f9469a`), `run_log.csv`, `run_summaries.md` — sur `main`. `patterns.csv` inchangé (pas de commit sur ce fichier).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260807-0603 — 2026-08-07 06:03 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- 1033 wallets scannés (fenêtre 14j), 157 au-dessus du seuil de score 90. Après croisement complet avec les 165 wallets déjà connus (tous les 157 flaggés, spray inclus, pas seulement les propres) : **0 candidat neuf** — le lot entier a déjà été vu et traité lors des runs précédents.
- Aucun candidat n'a atteint la gate B1 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Aucun changement sur `patterns.csv` / `wallets.csv` ce run — pas de commit sur ces fichiers.
- Fichiers mis à jour : `run_log.csv`, `run_summaries.md` (commit unique sur `main`).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260807-0628 — 2026-08-07 06:28 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- Outil principal de découverte de wallets fraîchement financés indisponible ce run : différé deux fois par l'analyste puis échec technique au 3e essai.
- Bascule sur la voie de secours (intel créateurs récents), fenêtre élargie aux créateurs single-launch (1 seul token créé) pour ne pas rater un candidat propre : 50 créateurs récents passés en revue.
  - 5 wallets single-launch financés par un exchange labellisé identifiés — tous par la même adresse déjà connue via le pattern P-002 (Binance, écarté pour absence de sibling). Scores qualité 32 à 48 sur ce lot.
  - Aucun ne franchit le seuil de score 90 de la gate B1 → **aucun candidat retenu, 0 ultra ni heavy dépensé**.
- Rien à signaler côté siblings, spray ou near-miss ce run : le point de blocage est uniquement le score, pas le financement ni le comportement.

### Bilan
- Aucun changement sur `patterns.csv` / `wallets.csv` ce run — pas de commit sur ces fichiers.
- Fichiers mis à jour : `run_log.csv`, `run_summaries.md` (commit unique sur `main`).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260807-0730 — 2026-08-07 07:30 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- 410 wallets scannés (fenêtre 14j), 30 au-dessus du seuil de score 90. Après filtrage des 28 déjà connus, 2 candidats neufs :
  - `3Ns3Q...gnPDz47` (score 95) : disqualifié immédiatement en gate B1 pour spray (créateur de 6 tokens sur son propre wallet).
  - `HfuZ4E...PhDbDuaKw` (score 95) : disqualifié immédiatement en gate B1 pour spray (créateur de 6 tokens sur son propre wallet).
- Aucun candidat n'a atteint la gate B2 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Fichiers mis à jour : `wallets.csv` (+2 lignes, toutes Écartées, commit `c872650`), `run_log.csv`, `run_summaries.md` — sur `main`. `patterns.csv` inchangé (pas de commit sur ce fichier).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260807-0826 — 2026-08-07 08:26 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- Deux passes de découverte (jusqu'à 2000 wallets scannés sur 14j, ~190 au-dessus du seuil de score 90). Après filtrage complet des wallets déjà connus, 2 candidats neufs :
  - `GchH9r...HWFvZ` (score 95) : disqualifié immédiatement en gate B1 pour spray (créateur de 4 tokens sur son propre wallet).
  - `Bi4rd5...FxYdLt` (score 90) : écarté d'emblée, hors périmètre — ce n'est pas un wallet créateur mais un nœud de financement (bridge/mother, 6000 tokens transitent par lui) ; rien à qualifier là-dessus.
- Aucun candidat n'a atteint la gate B2 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Fichiers mis à jour : `wallets.csv` (+1 ligne, Écartée), `run_log.csv`, `run_summaries.md` — sur `main`. `patterns.csv` inchangé (pas de commit sur ce fichier).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260807-0921 — 2026-08-07 09:21 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut. Les 3 patterns existants (P-001, P-002, P-003) restent Écartés, inchangés.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- 1027 wallets scannés (fenêtre 14j), 149 au-dessus du seuil de score 90. Croisement complet avec les 169 wallets déjà connus : **0 candidat neuf**, tout le lot a déjà été vu et traité lors des runs précédents.
- Un seul wallet du lot n'était pas encore dans notre base (`Bi4rd5...FxYdLt`), mais c'est le même nœud de financement hub/mother déjà repéré et écarté du périmètre au run précédent (pas un créateur) — rien de nouveau à traiter.
- Aucun candidat n'a atteint la gate B1 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Aucun changement sur `patterns.csv` / `wallets.csv` ce run — pas de commit sur ces fichiers.
- Fichiers mis à jour : `run_log.csv`, `run_summaries.md` (commit unique sur `main`).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260807-1022 — 2026-08-07 10:22 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut. Les 3 patterns existants (P-001, P-002, P-003) restent Écartés, inchangés.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- 1028 wallets scannés (fenêtre 14j), 150 au-dessus du seuil de score 90. Après filtrage des 149 déjà connus, 1 seul candidat neuf :
  - `9Nw9L...h7fKuA` (score 95) : disqualifié immédiatement en gate B1 pour spray (créateur de 5 tokens sur son propre wallet).
- Aucun candidat n'a atteint la gate B2 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Fichiers mis à jour : `wallets.csv` (+1 ligne, Écartée, commit `d8e41061`), `run_log.csv`, `run_summaries.md` — sur `main`. `patterns.csv` inchangé (pas de commit sur ce fichier).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260807-1125 — 2026-08-07 11:25 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut. Les 3 patterns existants (P-001, P-002, P-003) restent Écartés, inchangés.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- 1028 wallets scannés (fenêtre 14j), 148 au-dessus du seuil de score 90. Après filtrage des 147 déjà connus, 1 seul candidat neuf :
  - `AugGWnLP...c2R9uLm` (score 95) : disqualifié immédiatement en gate B1 pour spray (créateur de 3 tokens sur son propre wallet).
- Aucun candidat n'a atteint la gate B2 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Incident data — détecté et corrigé dans ce même run
- Le premier commit du run (`1fa0d68`) a déformé 4 lignes existantes de `wallets.csv` par erreur de retranscription (un mot anglais/français interverti sur la note du pattern P-003, et le nombre de tokens créés interverti entre trois wallets déjà en base : `BfSsqepX...`, `9Nw9L...`, et le nouveau candidat `AugGWnLP...`). Aucune de ces erreurs n'a changé un statut ou une décision — uniquement des chiffres cosmétiques dans les notes.
- Détecté par vérification systématique (relecture du fichier juste après écriture) avant la fin du run. Corrigé immédiatement par un second commit (`58257dc`), puis re-vérifié octet pour octet contre la source de vérité locale : conforme.

### Bilan
- Fichiers mis à jour : `wallets.csv` (+1 ligne Écartée, commit `1fa0d68`, correction `58257dc`), `run_log.csv`, `run_summaries.md` — sur `main`. `patterns.csv` inchangé (pas de commit sur ce fichier).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260807-1243 — 2026-08-07 12:43 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut. Les 3 patterns existants (P-001, P-002, P-003) restent Écartés, inchangés.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- 1029 wallets scannés (fenêtre 14j), 148 au-dessus du seuil de score 90. Après filtrage des 147 déjà connus, 1 seul candidat neuf :
  - `AcYqW4...aPsvPHa` (score 95) : disqualifié immédiatement en gate B1 pour spray (créateur de 14 tokens sur son propre wallet — jamais un pattern CEX valide, quel que soit le score).
- Aucun candidat n'a atteint la gate B2 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Fichiers mis à jour : `wallets.csv` (+1 ligne, Écartée), `run_log.csv`, `run_summaries.md` — sur `main`. `patterns.csv` inchangé (pas de commit sur ce fichier).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260807-1334 — 2026-08-07 13:34 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut. Les patterns existants (P-001, P-002, P-003) restent Écartés, inchangés.

### Découverte — budget : 3 ultras alloués, 1 consommé, 1 heavy
- 1027 wallets scannés (fenêtre 14j), 147 au-dessus du seuil de score 90. Après filtrage des 146 déjà connus, 1 seul candidat neuf :
  - `EA2SEMUU...tybQenY` (score 95) : single-launch confirmé, spray-free, financé par un exchange labellisé (KuCoin) → gates B1 et B2 passées.
  - Recherche CEX profonde (1 ultra) : montant exact récupéré, **5.244 SOL**, bande hair-thin 5.2439–5.2441. Le premier passage (4 pages / 800 wallets) n'a trouvé aucun frère hors le créateur lui-même.
  - Pour ne pas conclure trop vite sur un financeur CEX à fort volume, scan complémentaire exhaustif (1 heavy, 40 pages / 8000 signatures) sur la même bande : toujours **0 enfant trouvé**.
  - → Gate B4 ratée (pas de siblings, pattern non reproductible). `P-004` créé, statut **Écarté**. Le seul token de ce créateur a d'ailleurs gagné (+453.79% pic), mais un seul événement de financement isolé ne fait pas un pattern surveillable.
  - À noter : même adresse CEX (KuCoin) que le pattern `P-003` déjà en base, mais montant différent (5.244 contre 3.424 SOL) — deux événements isolés distincts, pas un doublon.
- 1 ultra et 1 heavy consommés sur les 3 ultras / 24 heavies alloués → budget restant pour le prochain run : 2 ultras / 23 heavies (fenêtre 15 min).

### Bilan
- Fichiers mis à jour : `patterns.csv` (+1 ligne, P-004 Écarté), `wallets.csv` (+1 ligne, Écartée), `run_log.csv`, `run_summaries.md` — commit unique sur `main`.
- Budget restant pour le prochain run : 2 ultras / 23 heavies consommés ce run, la fenêtre 15 min se reconstitue normalement pour le run suivant.
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260807-1431 — 2026-08-07 14:31 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut. Les 4 patterns existants (P-001 à P-004) restent Écartés, inchangés.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- 1029 wallets scannés (fenêtre 14j), 148 au-dessus du seuil de score 90. Après filtrage des 145 déjà connus, 3 candidats neufs :
  - `EJw6Ea...gwwh7x` (score 95) : disqualifié immédiatement en gate B1 pour spray (créateur de 6 tokens sur son propre wallet).
  - `BCCX7k...9WNqG` (score 95) : disqualifié immédiatement en gate B1 pour spray (créateur de 6 tokens sur son propre wallet).
  - `8mF12j...NJTF3YHE` (score 95) : disqualifié immédiatement en gate B1 pour spray (créateur de 13 tokens sur son propre wallet).
- Aucun candidat n'a atteint la gate B2 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain (le run précédent avait consommé 1 ultra / 1 heavy, la fenêtre 15 min s'est reconstituée).

### Bilan
- Fichiers mis à jour : `wallets.csv` (+3 lignes, toutes Écartées, commit `9581852`), `run_log.csv`, `run_summaries.md` — sur `main`. `patterns.csv` inchangé (pas de commit sur ce fichier).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé ce run).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260807-1525 — 2026-08-07 15:25 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut. Les 4 patterns existants (P-001 à P-004) restent Écartés, inchangés.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- 412 wallets scannés (fenêtre 14j), 50 au-dessus du seuil de score 90. Après filtrage des 46 déjà connus, 4 candidats neufs :
  - `GMMnee...dXgFf` (score 95) et `Hcrr3q...b3JDVz` (score 100) : disqualifiés immédiatement en gate B1 pour spray (créateurs de 7 et 12 tokens sur leur propre wallet).
  - `4q4iYC...HVQVoWq` (score 95) : disqualifié en gate B1 pour spray (créateur de 5 tokens).
  - `8CkcWo...Q9KiL` (score 95, signal brut à 1 seul token) : vérification profonde par prudence — en réalité 4 tokens créés, tous dumpés. Financé par le même exchange déjà repéré deux fois cette dernière journée sur des near-miss (funder CEX réel mais créateur spray) — troisième occurrence du même funder CEX associé à du spray. Disqualifié en gate B1.
- Aucun candidat n'a atteint la gate B2 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Fichiers mis à jour : `wallets.csv` (+4 lignes, toutes Écartées), `run_log.csv`, `run_summaries.md` — sur `main`. `patterns.csv` inchangé (pas de commit sur ce fichier).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé ce run).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente). À noter : le même funder CEX (`iGdFcQoyR2M...NSdwu`) a maintenant produit 3 créateurs spray sur la période — le flux de financement CEX est actif, mais aucun de ses enfants n'est encore propre (spray-free) pour justifier une recherche CEX profonde.

## Run R-20260807-1622 — 2026-08-07 16:22 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut. Les 4 patterns existants (P-001 à P-004) restent Écartés, inchangés.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- 149 wallets au-dessus du seuil de score 90 (scan large jusqu'à 2000 wallets, première tentative différée puis relance réussie). Après filtrage des 147 déjà connus, 2 candidats neufs :
  - `9c122c...q5ZY` (score 95) : disqualifié immédiatement en gate B1 pour spray (créateur de 15 tokens sur son propre wallet).
  - `HKRhc6...mo7Nnz` (score 95) : disqualifié immédiatement en gate B1 pour spray (créateur de 15 tokens sur son propre wallet).
- Aucun candidat n'a atteint la gate B2 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Fichiers mis à jour : `wallets.csv` (+2 lignes, toutes Écartées, commit `1dd263f`), `run_log.csv`, `run_summaries.md` — sur `main`. `patterns.csv` inchangé (pas de commit sur ce fichier).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé ce run).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260807-1726 — 2026-08-07 17:26 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut. Les 4 patterns existants (P-001 à P-004) restent Écartés, inchangés.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- 1036 wallets scannés (fenêtre 14j), 149 au-dessus du seuil de score 90. Croisement complet avec les 182 wallets déjà connus : **0 candidat neuf** — le lot entier a déjà été vu et traité lors des runs précédents.
- Aucun candidat n'a atteint la gate B1 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Aucun changement sur `patterns.csv` / `wallets.csv` ce run — pas de commit sur ces fichiers.
- Fichiers mis à jour : `run_log.csv`, `run_summaries.md` (commit unique sur `main`).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260807-1816 — 2026-08-07 18:16 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut. Les 4 patterns existants (P-001 à P-004) restent Écartés, inchangés.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- 1034 wallets scannés (fenêtre 14j), 50 au-dessus du seuil de score 90. Croisement complet avec les 182 wallets déjà connus : **0 candidat neuf** — le lot entier a déjà été vu et traité lors des runs précédents.
- Aucun candidat n'a atteint la gate B1 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Aucun changement sur `patterns.csv` / `wallets.csv` ce run — pas de commit sur ces fichiers.
- Fichiers mis à jour : `run_log.csv`, `run_summaries.md` (commit unique sur `main`).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).

## Run R-20260807-1922 — 2026-08-07 19:22 UTC

### Refresh
- 1 tracker actif ("Ring gangJEP - buyer 8St6e"), PnL 0, 0 rug, aucun trade. Ne correspond toujours à aucune ligne de `wallets.csv` → rien à recalculer.
- Feed : aucune activité sur les trackers suivis sur les 7 derniers jours.
- Aucun pattern en "En surveillance" ou "Rentable" à date → aucune transition automatique de statut. Les 4 patterns existants (P-001 à P-004) restent Écartés, inchangés.

### Découverte — budget : 3 ultras alloués, 0 consommé, 0 heavy
- 1035 wallets scannés (fenêtre 14j), 150 au-dessus du seuil de score 90. Après filtrage des 148 déjà connus, 2 candidats neufs :
  - `AuyuBi...C1Vri` (score 95) : disqualifié immédiatement en gate B1 pour spray (créateur de 2 tokens sur son propre wallet).
  - `CbqJWi...wfjnzoGe1` (score 95) : disqualifié immédiatement en gate B1 pour spray (créateur de 8 tokens sur son propre wallet).
- Aucun candidat n'a atteint la gate B2 → **0 ultra ni heavy dépensé ce run**, budget plein pour le prochain.

### Bilan
- Fichiers mis à jour : `wallets.csv` (+2 lignes, toutes Écartées, commit `4b6cb053`), `run_log.csv`, `run_summaries.md` — sur `main`. `patterns.csv` inchangé (pas de commit sur ce fichier).
- Budget restant pour le prochain run : 3 ultras / 24 heavies (fenêtre 15 min, rien consommé).
- Prochaine action humaine suggérée : rien à valider ce run (aucun pattern Validé, aucune config tracker en attente).
