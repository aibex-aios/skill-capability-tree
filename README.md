# skill-capability-tree

Paket von AIbex AIOS mit dem Skill `capability-tree`, Version 0.1.0: zeigt als Baum, welche Fähigkeiten von AIOS in einem Projektordner zur Verfügung stehen (Kette, Pakete, Assistent, Wissen, Regeln, registrierte Skills, Arbeitspferde, Kernel) und auf welchem Weg sie dort ankommen. Der Skill liest nur und schreibt nichts.

## Holen

**Voraussetzung:** AIbex AIOS v0.5 ist installiert, und der Vault oder das Projekt ist mit `aios --activate v0.5.<n>` aktiviert.

```bash
aios --fetch skill-capability-tree
```

Der Skill wirkt **ab dem nächsten Chat** im Projekt.

## Aufruf

Im Chat etwa „Capability tree", „capability AA" (Projekt-Präfix) oder `/capability-tree [<pfad>]`. Ohne Angabe gilt das Arbeitsverzeichnis. Ausgegeben werden ein Baum, eine Legende der Wege und die Stellen, die nicht geprüft werden konnten.

## Lizenz

Apache License 2.0 (`LICENSE`), Rechteinhaber AIbex KG (`NOTICE`). Die Marken „AIbex" und „AIbex AIOS" sind nicht mitlizenziert.
