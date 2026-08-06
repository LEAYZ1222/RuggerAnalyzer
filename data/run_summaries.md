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
