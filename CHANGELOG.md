# Changelog

## Build DE_DBdJm — 1.0.0-beta.9 : effacement après une session LIVE

- **Effacer un projet juste après une session LIVE** : l'unité restait sur son
  écran distant ; l'effacement s'exécutait **sans accusé** (ligne bloquée sur
  « erasing… », puis `Timeout: prepare restore`) alors que le projet était bien
  supprimé. L'app envoie maintenant **MKII EXIT `3A 00 00 00`** + `FC 00 00` à
  l'arrêt du suivi. Mesuré : LIVE → sortie → effacement = 3,5 s (contre 121 s).
- Documentation : `MK2-USB-NOTES.md` récapitule tous les pièges du protocole
  (accusé vs notification de chargement, timeouts qui décalent les réponses,
  sessions d'import, lecture en cours, décodeur, double processus).

## Build zvgqkhUa — 1.0.0-beta.8 : envoi/effacement MK2 réparés (validé sur machine)

## Build zvgqkhUa — 1.0.0-beta.8 : envoi/effacement MK2 réparés (validé sur machine)

- **Course au changement de projet** : `select project` répond `1A 00` dès que la
  demande est acceptée, pas quand le projet est chargé. L'effacement partait
  pendant le chargement → aucune réponse, « Erasing… » pendant deux minutes puis
  `Timeout: prepare restore`. L'app attend maintenant la notification
  `1A proj 00 00 00` (comme SparkyMK2) ; quand la machine est déjà sur la cible
  (pas de notification), elle accepte l'accusé et confirme par le statut.
- **Récupération de session** : `FC` + `9D` envoyés à la connexion et avant de
  fermer après un timeout — une session d'import abandonnée laissait la machine
  muette jusqu'au redémarrage.
- **Erreurs appareil** (`32 code`) remontées immédiatement au lieu d'attendre.
- **Lecture en cours** : envoi/effacement refusés proprement (« Stop playback on
  the MK2 first »).
- **LIVE** : cadence de lecture du bloc de pad adaptative (2 ticks si rapide,
  3 si lent) pour ne pas affamer le poll de statut.
- Validé sur la MK2 avec le projet carte complet du testeur (311 fichiers,
  250 Mo, PTN et PICTURE) : envoi 64,7 s (~3,9 Mo/s), `send-end` 16,5 s, la
  machine affiche 154 samples / 249 743 400 octets ; suppression d'un projet
  non vide depuis un autre slot : 6,4 s ; relecture d'un projet : 1,4 s.

## Build hzR9IypT — 1.0.0-beta.7 : envoi/effacement MK2 réparés (validés sur machine)

- **Deuxième import d'une session jamais acquitté** : le premier effacement
  passait, le suivant (`send-begin` après une suppression, ou un second envoi)
  n'obtenait jamais la réponse `12` et restait sur « Erasing… ». L'app envoie
  maintenant `9D` (sortie de la session d'import précédente) et attend `1D`
  **avant** chaque `prepare`, comme l'app officielle et SparkyMK2. Vérifié sur la
  machine : quatre `prepare restore` d'affilée, 1,3 à 2,3 s, aucun timeout.
- **Échec d'envoi sans restauration** : `backupFiles` était déclaré dans le
  `try` et lu dans le `catch` → `ReferenceError: backupFiles is not defined`, le
  rollback ne tournait pas et le slot restait vide. Corrigé.
- **Timeout = resynchronisation** : plus de renvoi de commande (c'était le
  décalage des réponses) ; attente de grâce de 1,5 s puis fermeture du lien.
  Délais alignés : 45 s par défaut, 120 s pour un effacement, 60 s pour un reload.
- **Watchdog de trame** et plafond de la boîte de réception.
- **Instance unique** + refus d'un port déjà tenu par un autre processus.
- **Fenêtre « occupé » libérée** après un envoi, une sauvegarde ou un pull
  (elle restait armée jusqu'à 2 minutes et bloquait les opérations suivantes).
- Validé sur la MK2 : connect, lecture des 16 projets, suppression d'un slot,
  `send-begin` + fichier de 200 Ko + `send-end`, suppression puis nouvel envoi
  dans la même session, relecture d'un projet (17 fichiers, 727 Ko en 1,4 s).

## Build DsEdNY3N — 1.0.0-beta.5 : envoi/effacement MK2 réparés, LOCAL à 50 ms

- **Envoi vers la MK2 et effacement d'un projet** : une commande qui expirait
  était abandonnée mais sa réponse arrivait plus tard et était consommée par la
  commande suivante — tout le flux se décalait d'un cran (timeout puis blocage
  sur « Erasing… »). Un timeout attend maintenant 1,5 s une réponse tardive et
  l'utilise si elle arrive ; le délai par défaut passe de 6 à 45 s (le stockage
  MK2 met légitimement 6 à 13 s). La connexion ne sonde plus l'espace libre, et
  une seconde tentative est refusée pendant un envoi au lieu de se mettre en
  file derrière l'effacement.
- **LOCAL** : poll de statut toutes les **50 ms** (au lieu de 150 ms) — commande
  de 3 octets, ~4 ms. LIVE reste à 150 ms (lecture du bloc de pad de 327 octets).
- **Diagnostic** : les commandes de contrôle de plus d'une seconde sont
  journalisées (`prepare restore 42000ms`), les échecs avec leur durée.

## Build BFNwb7I6 — 1.0.0-beta.4 : liaison USB MK2 fiable (envoi, veille de banque)

- **« timeout: status poll » à l'envoi d'un projet vers la MK2** : le probe
  d'espace libre tournait dans l'étape de connexion ; sur une carte occupée il
  met 6 à 12 s et, en cas de timeout, fermait une liaison saine. La réponse
  tardive restait ensuite dans la file et était servie à la commande suivante
  (d'où la cascade). Le probe est maintenant optionnel et borné, et un timeout
  vide la file de réponses pour resynchroniser la session.
- **La veille de banque ne concurrence plus un transfert** : le poll de statut
  continuait pendant la sauvegarde (parfois plusieurs minutes) qui précède un
  envoi, une restauration ou un pull. Il est suspendu tant que des octets
  circulent et reprend seul ; annuler une boîte de dialogue ne laisse plus de
  longue fenêtre « occupé ».
- **L'envoi vers la MK2 n'échoue plus sur un probe lent** : si l'espace libre
  n'est pas lisible à temps, l'écriture est tentée et l'appareil renvoie une
  vraie erreur seulement si le slot est réellement plein.
- **`mk2-timings.log` nomme le coupable** : `bank … gap=…ms poll=…ms blocked=<op> <ms>`.
- **Numéro de version** : À propos / Lire les informations affichent 1.0.0-beta.4.

## Build BVfBf7Y_ — 1.0.0-beta.3 : audio iPad, ZIP sans limite, LOCAL plus réactif

- **iPad — le son revient.** La session audio est réarmée au retour dans l'app,
  après une interruption, un changement de sortie et après une réinitialisation
  des services média ; le moteur est relancé si un changement de graphe l'a
  arrêté. Un pad rejoue donc normalement après un pattern.
- **Import ZIP — plus de limite pratique de taille.** Les archives qui ne sont
  pas des exports « stored » de l'app sont extraites en flux dans le dossier
  temporaire de l'application (jamais à côté du fichier), puis importées entrée
  par entrée : la limite de 600 Mo sous Windows disparaît. Les exports de l'app
  restent lus sur place, sans extraction.
- **LOCAL — changement de banque depuis la MK2.** Le scan de tempo et le
  pré-décodage des samples attendent que l'interface ait suivi le changement de
  banque au lieu de lui faire concurrence. Les durées sont écrites dans
  `mk2-timings.log` (`bank … gap=…ms poll=…ms`, `bank-ui … ipc=…ms apply=…ms`).
- TestFlight : build `1.0.0 (2)` (iPad), en plus de `1.0.0 (1)`.

## Build CyZgXbme — LIVE toutes les 300 ms, BPM au chargement

- **LIVE** : le bloc de pad est relu toutes les 300 ms (au lieu de ~100 ms), ce
  qui laisse la machine respirer pendant la lecture ; l'affichage volume/pitch
  suit toujours les réglages.
- **BPM** : détection au chargement d'une carte ou d'un projet, en tâche de
  fond ; les cartes existantes sans tempo récupèrent un BPM.
- **Windows** : métadonnées de pad sur une seule ligne, grille de pads
  redimensionnée et défilante.
- **Imports** : bouton Annuler dans la barre de progression ; le BPM est
  affiché dans une pastille sur chaque pad.
- **Diagnostic** : `crash-diagnostics.log` et `mk2-timings.log` (opérations
  ≥ 50 ms) dans le dossier de données.

## Build CPfhcmH_ — BPM visible partout, LIVE prioritaire

- **BPM** : le tempo est maintenant affiché dans une pastille en bas à droite du pad, donc visible
  même quand la case est courte (Windows, MK2/555 en grille 16 pads).
- **Scan BPM** : décodage étendu aux cartes qui ne gardent qu'un fichier source, et **suspendu en
  LIVE** pour ne pas ralentir la synchronisation avec la MK2.
- Reprise des réglages LIVE identique (aucun changement de protocole).

## Build wuNfXTEs — LIVE/USB fiable, BPM sur les pads « paresseux »

- **LIVE / stockage USB** : la première lecture après connexion réessaie automatiquement
  (timeout `readdir`) au lieu de laisser la liste des projets vide jusqu'au redémarrage.
- **BPM** : le scan de chargement décode maintenant les samples « paresseux » (MK2 `.SMP`,
  555 `.SPD`) et les cartes qui n'ont qu'un WAV, donc les tempos manquants sur SX/A et 555
  apparaissent aussi.
- Analyse de carte : capture sombre ajoutée au site.

## Build CrGgYp9H — BPM au chargement

- Le tempo est aussi détecté **au chargement d'une carte ou d'un projet**, en tâche de fond (un pad
  à la fois) : les cartes existantes sans tempo récupèrent un BPM sans bloquer l'interface.
- Les pads qui ont déjà un tempo (carte, machine, projet) sont laissés tels quels.

## Build BBiHNg2j — BPM partout, LIVE plus robuste

- **BPM sur tous les modules** : le tempo est détecté à l'import et affiché sur chaque pad.
  Il est écrit dans la table de pads de la MK2, de la SX/A, de l'OG et de la 555 (donc visible
  sur la machine). La SP-303 n'a pas de champ tempo.
- **Changement de module** : l'app ne se ferme plus quand on passe d'un module à l'autre avec un
  projet chargé ; l'échec de connexion USB est signalé au lieu d'arrêter l'application.
- **LIVE** : « Refresh from MK2 » reste actif même quand la liste des projets revient vide.
- **Diagnostic** : un journal `crash-diagnostics.log` est écrit dans le dossier de données.

## 1.0.0-beta.2 — first public beta

- SP-303, SP-404 (OG), SP-404SX/A, SP-404MK2 and SP-555 modules.
- Set preparation, pad parameters, audio conversion, card reading.
- Export to card folder, ZIP and project file.
- SP-404MK2: LOCAL/LIVE USB link, pattern editor and playback, screen editor.
- macOS universal DMG (notarised) and Windows x64 portable ZIP.
- iPad: TestFlight beta — editor/librarian, no USB link.

Build `cjMWZt_F`. macOS DMG signed with a Developer ID, notarised and stapled.
Windows ZIP portable, not code-signed.
