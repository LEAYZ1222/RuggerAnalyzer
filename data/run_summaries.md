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
