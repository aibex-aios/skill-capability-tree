---
name: capability-tree
version: 0.1.0
description: >
  Zeigt als Baum, welche Fähigkeiten von AIbex AIOS in einem Projektordner zur Verfügung stehen
  (Kette, Pakete, Assistent, Wissen, Regeln, registrierte Skills, Arbeitspferde, Kernel) und auf
  welchem Weg sie dort ankommen. Liest nur. Verwenden bei "Capability tree", "Capability",
  "capability tree <Projekt-Präfix>", "capability <Projekt-Präfix>" (z. B. "capability AA",
  "Capability tree MSC"), "was steht hier zur Verfügung?", "zeig die Fähigkeiten als Baum".
---

# Capability-Tree

Der Skill **liest nur** und schreibt nichts, daher keine Rückfrage. Arbeite mit absoluten Pfaden, nie mit `cd`. Rufe jeden Befehl **einzeln** auf, damit eine Ablehnung durch die Rechteprüfung nur einen Teil des Baums betrifft.

## 1. Zielordner bestimmen

- **Kein Argument:** das Arbeitsverzeichnis der Session.
- **Projekt-Präfix** (z. B. `AA`, `MSC`, `msc`): Vault-Wurzel bestimmen (erster Eintrag von `Chain` in `aios --context --list "<arbeitsverzeichnis>"`, also der Vorfahre des Arbeitsverzeichnisses mit diesem Namen). Dann suchen:
  `find "<vault>" -type d -iname '<präfix>-*' -not -path '*/.git/*' -not -path '*/.claude/*' -not -path '*/@*'`
  - genau ein Treffer: das ist der Zielordner;
  - mehrere: liste sie und frage, welcher gemeint ist;
  - keiner: sag das und beende.
- **Pfad:** dieser Pfad.

## 2. Sammeln

| # | Was | Quelle |
|---|---|---|
| 1 | Kette, AIOS-Version, Assistent mit Herkunft, Master-Kontext (id, Dateien), verfügbares Wissen, Blueprints, Pakete | `aios --context --list "<ziel>"` |
| 2 | Bindung je Ebene (`aios:`, `packages:`, `assistant:`) | `.aios/config.yaml` jeder Ebene der Kette (fehlt sie: „keine config.yaml") |
| 3 | Elemente je Paket | `<ebene>/@/@!Package/<paket>/manifest.yaml`, Felder `version`, `type`, `contents` |
| 4 | Aufbau des Assistenten: `@Wissen/`-Ordner (Klasse `@`, `@@`, `@!`) und `@!Skill/` | `ls` im Assistentenordner des Pakets |
| 5 | Regeln und andere `@`-Ordner je Ebene | `ls "<ebene>/@"` |
| 6 | registrierte Skills | `ls "<ziel>/.claude/skills"` |
| 7 | Arbeitspferde | `ls ~/.aios/bin` |
| 8 | Kernel-Skills `aios-*` und andere Skills auf User-Ebene | `ls ~/.claude/skills`, Version aus Schritt 1 |

Was du nicht lesen kannst, markierst du mit `?` und merkst dir den Grund. Erfinde nichts.

## 3. Ausgabe

Ein Baum in **einem Codeblock**, Box-Zeichen `├── └── │`:

- **Wurzel = Vault** mit AIOS-Version; darunter je Ebene der Kette, was sie beiträgt (`.aios/`, `@/@!Package/`, `@/@Rule/`, weitere `@`-Ordner), dann die nächste Ebene bis zum Zielordner (`(dieses Projekt)`). Keine Geschwister- oder Unterprojekte.
- **Pakete mit Version und Elementen**; Pakete ohne Elemente mit „keine Elemente" bzw. „Arbeitspferd".
- **Zustand je Element** rechts nach Punkten (`....`): geladen, verfügbar, registriert, nur im Rollentext, geerbt.
- **Pfeile `◄──`** mit senkrechten Linien zeigen, wie ein Element im Zielordner ankommt: Assistent → Zuordnung (`assistant:` in einer `config.yaml`), Skills → `.claude/skills/`, Arbeitspferde → `~/.aios/bin/`.
- Zum Schluss ein Ast **„außerhalb des Vaults"**: `~/.aios/bin/`, `~/.aios/cache/`, Kernel mit Version und Skills `aios-*`. Weitere Skills in `~/.claude/skills/` (nicht `aios-*`, nicht `gui`) bekommen einen eigenen Ast „User-Ebene, gilt überall", ein Symlink mit seinem Ziel und dem Vermerk „nicht von AIOS verwaltet".

Nach dem Codeblock:

1. **Legende der Wege** (ein Absatz): Regeln über den Master-Kontext, Assistent über die Zuordnung (wo, geerbt oder nicht), Skills über die Registrierung, Arbeitspferde über die Weiterleiter.
2. **Ungeprüft:** jede `?`-Stelle mit Grund; gibt es keine, „keine".

Vorbild des Baums:

```
§AIOS  (Vault, AIOS 0.5.1)
├── .aios/config.yaml .......... aios: 0.5.1 · packages: …
├── @/@!Package/ ............... geholte Pakete
│   ├── assistant-anja 0.2.0
│   │   └── assistants/anja ............ Assistentin ──────────┐
│   │       ├── @Wissen/@Leitfaden ..... geladen               │
│   │       ├── @Wissen/@@Praxis (16) .. verfügbar             │
│   │       └── @!Skill/anja-anlegen ... nur im Rollentext     │
│   ├── skill-memo 6.0.1 ............... Skill memo ─────────┐ │
│   └── worker-yt-dlp 0.1.0 ............ Arbeitspferd ─┐     │ │
└── A-AIOS  (Projekt)                                  │     │ │
    ├── .aios/config.yaml ...... assistant: anja ◄─────┼─────┼─┘
    └── AA-AIbex-AIOS  (dieses Projekt)                │     │
        ├── @/@Rule/ ........... Master-Kontext (id …) │     │
        └── .claude/skills/ .... registriert ◄─────────┼─────┘
            └── memo                                   │
außerhalb des Vaults                                   │
├── ~/.aios/bin/ ............... Weiterleiter ◄────────┘
└── Kernel AIOS 0.5.1 .......... Skills aios-*
```

Kein Memo, keine Datei: Speichere den Baum nur, wenn der User es ausdrücklich verlangt (dann nach dem Skill `memo`).
