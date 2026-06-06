# /sumup - Session-Handoffs für Claude Code

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-d97757.svg)
![Plattform](https://img.shields.io/badge/Plattform-CLI%20%7C%20Desktop%20%7C%20IDE-555.svg)

Ein schlanker [Claude-Code](https://docs.claude.com/en/docs/claude-code)-Skill, der den
aktuellen Stand einer Session als `sumup.md` ins Projektverzeichnis schreibt - damit du
den Kontext per `/clear` zurücksetzen (oder den Laptop zuklappen) kannst und später
genau dort weitermachst, wo du aufgehört hast.

Lange Claude-Code-Sessions sammeln still Tokens an. Statt diesen Ballast durch jeden
Folgeschritt zu schleppen, verdichtet `/sumup` die Session zu einer fokussierten
Handoff-Notiz. Du machst `/clear` und fügst die Notiz wieder ein. Die nächste Session
startet schlank und schärfer - nicht leerer.

---

## Was der Skill macht

- **Verdichtet die Session** zu einem strukturierten Snapshot in `sumup.md`.
- **Liest die bestehende Datei zuerst** und übernimmt offene Punkte, bevor er überschreibt.
- **Kein Verlauf, kein Changelog** - immer ein frischer Handoff statt Audit-Trail.
- **Keine Daten verlassen den Rechner** - der Skill weist Claude nur an, eine lokale
  Markdown-Datei zu schreiben.
- **Läuft überall**, wo Claude Code Skills lädt: CLI, Desktop, IDE-Extensions.

---

## Der `/sumup` -> `/clear` -> Einfügen-Workflow

```text
Lange Session            /sumup            sumup.md geschrieben
Kontext wird fett   ---------------->      im Projekt-Root
                                                  |
                                                  v
Frische Session     <----------------      /clear
schlank, Claude       sumup.md einfügen     Kontext zurückgesetzt
macht weiter
```

Drei Befehle, wiederholbar sobald die Session schwer wird:

1. `/sumup` - Claude schreibt die Handoff-Datei
2. `/clear` - Claude Code setzt die Konversation zurück
3. Inhalt von `sumup.md` als ersten Prompt wieder einfügen

Kein Config, keine Hintergrund-Daemons, kein externer Speicher.

---

## Warum nicht einfach den langen Kontext behalten?

- **Token-Kosten wachsen mit jedem Schritt.** Eine Session mit 200 Nachrichten bezahlt
  ihre volle Historie bei jedem neuen Schritt mit.
- **Das Signal verwässert.** Frühe Experimente, Fehlversuche und Smalltalk bleiben im
  Kontext und konkurrieren mit der eigentlichen Aufgabe.
- **Relevanz driftet.** Nach zwei Stunden verteilt sich Claudes Aufmerksamkeit auf alles,
  was passiert ist - nicht auf das, was als Nächstes zählt.

`/sumup` zwingt zur Destillation. Übrig bleibt das Tragende: Ziel, aktueller Stand,
offene Punkte, Stolperfallen. Das Modell bekommt eine saubere Startbahn.

---

## Was geschrieben wird

`sumup.md` folgt einer festen Struktur (maschinen- und menschenlesbar):

- **Projekt / Arbeitsverzeichnis** - absoluter Pfad, Repo, Branch, Umgebung
- **Session-Ziel** - 1 bis 3 Sätze
- **Aktueller Stand** - was erledigt wurde, zentrale Entscheidungen samt Begründung
- **Offene Punkte / Nächste Schritte** - konkrete TODOs
- **Wichtiger Kontext zum Weitermachen** - Pfade, Befehle, Stolperfallen, genutzte Tools
- **Einstieg für die neue Session** - fertiger Fortsetzungs-Prompt zum Einfügen

Der Skill überschreibt die Datei bei jedem Lauf - kein Verlauf. Ziel ist der stets frische
Handoff, kein Audit-Trail. (Für dauerhafte Engineering-Retros gibt es einen eigenen Skill.)

---

## Installation

Claude Code findet Skills automatisch unter `~/.claude/skills/<skill-name>/SKILL.md`.
Klone dieses Repo an genau diese Stelle:

### macOS / Linux

```bash
git clone https://github.com/m-entruencer/claude-skill-sumup.git ~/.claude/skills/sumup
```

### Windows (PowerShell)

```powershell
git clone https://github.com/m-entruencer/claude-skill-sumup.git "$env:USERPROFILE\.claude\skills\sumup"
```

### Windows (Git Bash)

```bash
git clone https://github.com/m-entruencer/claude-skill-sumup.git ~/.claude/skills/sumup
```

Eine laufende Claude-Code-Session danach neu starten. Der Skill taucht automatisch auf -
keine Anpassung an `settings.json` nötig.

### Sprachvarianten

- `SKILL.md` - Englisch (Standard, wird von Claude Code geladen)
- `SKILL.de.md` - deutsches Original

Wenn die deutsche Variante aktiv sein soll, tausche die Dateien:

```bash
cd ~/.claude/skills/sumup
mv SKILL.md SKILL.en.md && mv SKILL.de.md SKILL.md
```

---

## Nutzung

In jeder Claude-Code-Session:

```text
/sumup
```

Oder mit einem zusätzlichen Hinweis für die nächste Session:

```text
/sumup denk dran, die Migration zuerst auf Staging zu prüfen
```

Claude schreibt `sumup.md` ins Projektverzeichnis (oder Repo-Root, wenn du in einem
Git-Repo bist) und gibt eine kurze Bestätigung aus.

So machst du später weiter:

1. Projekt öffnen
2. `/clear` (oder neue Session starten)
3. Inhalt von `sumup.md` als erste Nachricht einfügen

---

## Aktualisieren

```bash
cd ~/.claude/skills/sumup && git pull
```

---

## FAQ

**Werden Daten irgendwohin gesendet?**
Nein. Der Skill weist Claude nur an, eine lokale Markdown-Datei zu schreiben. Es verlässt
nichts den Rechner, was Claude Code nicht ohnehin tut.

**Funktioniert das mit CLI, Desktop und IDE-Extensions?**
Ja - überall, wo Claude Code Skills aus `~/.claude/skills/` lädt.

**Überschreibt es meine bestehende `sumup.md`?**
Ja, bewusst. Der Skill liest die bestehende Datei zuerst, übernimmt offene Punkte und
überschreibt dann. Kein Append, kein Verlauf. Commit oder Umbenennen, wenn du eine
bestimmte Version behalten willst.

**Kann ich Dateiname oder Speicherort ändern?**
Forke das Repo und passe `SKILL.md` an - die Struktur ist reines Markdown ohne Magie.

**Ersetzt das echte Doku / Retros?**
Nein. `sumup.md` ist ein Arbeitsstand-Snapshot, kein Entscheidungs-Log. Für dauerhafte
Engineering-Retros einen dedizierten Retro-Skill oder den üblichen Doku-Workflow nutzen.

---

## Lizenz

MIT - siehe [LICENSE](LICENSE).

---

## Credits

Bereitgestellt von der **[Entruencer UG (haftungsbeschränkt)](https://entruencer.de/)** -
öffentlich geteilt, damit das `/sumup` -> `/clear`-Muster nicht in privaten Repos versteckt
bleibt.

Issues, Pull Requests und Forks sind willkommen.
