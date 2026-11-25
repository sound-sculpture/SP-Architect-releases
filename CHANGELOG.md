# Changelog

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
