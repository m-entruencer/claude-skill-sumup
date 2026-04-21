---
name: sumup
description: Schreibt am Sessionende ein strukturiertes Kontext-Summary der aktuellen Claude-Code-Session als `sumup.md` ins aktuelle Projekthauptverzeichnis, damit der User nach `/clear` oder an einem späteren Tag nahtlos weitermachen kann, ohne Projekt, Ziel, Zwischenstand und offene Punkte neu zu erklären. Nutze diesen Skill IMMER wenn der User `/sumup`, `/sumup <notiz>` schreibt oder sinngemäß "fass die Session zusammen damit ich weitermachen kann", "mach ein Summary zum Mitnehmen", "gib mir Kontext für die nächste Session", "speicher den Stand für morgen" oder ähnlich sagt.
---

# Sumup — Session-Kontext für die Fortsetzung festhalten

## Wann dieser Skill läuft

Manuell, wenn der User `/sumup` oder `/sumup <kurze Notiz>` ausführt oder sinngemäß nach einem Zwischenstand zum späteren Weitermachen fragt. **Niemals automatisch.**

## Ziel

Eine persistente Datei `sumup.md` im Projekthauptverzeichnis, die so vollständig ist, dass der User sie in einer frischen Claude-Code-Session als ersten Prompt einfügen kann und Claude ohne weitere Erklärung weiterarbeitet. Die Datei überlebt `/clear`, Neustarts und Tageswechsel.

## Abgrenzung zu anderen Skills

- `/sumup` → ephemerer Arbeitsstand, lokal im Projekt, wird beim nächsten Lauf überschrieben.
- Persistente Retros / Wissensdatenbanken → dafür eigene Skills (nicht dieser).

`/sumup` schreibt **nie** in einen Vault und legt **keine History** an.

## Ablauf

### 1. Projekt-Root bestimmen

- Standard: aktuelles Arbeitsverzeichnis (`cwd`).
- Wenn der `cwd` in einem Git-Repo liegt, stattdessen den Repo-Root verwenden (`git rev-parse --show-toplevel`).
- Zielpfad ist immer `<projekt-root>/sumup.md`.

### 2. Session auswerten

Ziehe aus dem bisherigen Session-Verlauf zusammen:

- Projektkontext: Repo, Tech-Stack, relevante Umgebung.
- Ziel der Session in 1–3 Sätzen.
- Konkret berührte Dateien, Funktionen, Commands, MCP-/Tool-Nutzung.
- Entscheidungen inkl. knapper Begründung.
- Offene Punkte, nächste Schritte, bekannte Stolpersteine.
- Optional: vom User übergebene Notiz (`/sumup <notiz>`) als zusätzlichen Hinweis einbauen.

**Substanz-Check:** Wenn die Session zu dünn ist (kein echter Arbeitsstand, nur Smalltalk), sag ehrlich "zu wenig Material für ein sinnvolles Summary" und frag, ob trotzdem eine Datei geschrieben werden soll. **Nichts erfinden, keine leere Datei schreiben.**

### 3. Alte `sumup.md` berücksichtigen

Existiert die Datei bereits, **kurz lesen**, um offene Punkte, die noch nicht abgearbeitet wurden, in die neue Version zu übernehmen. Danach **komplett überschreiben** — kein Append, kein Changelog, keine History.

### 4. Datei schreiben

Genau diese Struktur nutzen, Abschnitte die leer bleiben, trotzdem mit "–" statt weglassen:

```markdown
# Session-Summary (YYYY-MM-DD HH:MM)

## Projekt / Arbeitsverzeichnis
- Pfad: <absoluter Pfad>
- Repo / Branch: <falls git>
- Relevante Umgebung: <OS, Tech-Stack, Besonderheiten>

## Ziel der Session
<1–3 Sätze, worum geht's konkret>

## Aktueller Stand
- <Bulletpoint: was wurde gemacht, mit Dateinamen/Funktionen>
- <Bulletpoint: wichtige Entscheidung + kurze Begründung>

## Offene Punkte / nächste Schritte
- ⬜ <konkreter nächster Schritt>
- ⬜ <weiterer Schritt>

## Wichtiger Kontext für die Weiterarbeit
- Pfade / Commands / ENV-Vars
- Tool- oder MCP-Spezifika, die wichtig sind
- Bekannte Stolpersteine
- Verwendete Skills / Agents

## Einstieg für neue Session
> Mach weiter bei <Schritt>. Kontext siehe oben. <Ggf. konkrete Anweisung>.
```

### 5. Kurze Bestätigung im Chat

Nach dem Schreiben nur eine knappe Rückmeldung ausgeben — keinen kompletten Inhalt-Dump:

- Pfad der geschriebenen Datei.
- 1–2 Zeilen, was im Kern drin steht.
- Hinweis, dass bei der nächsten Session der Datei-Inhalt als erster Prompt eingefügt werden kann.

## Stilregeln für den Inhalt

- Deutsch, kurz, Bulletpoints statt Fließtext.
- Umlaute und ß korrekt (nicht oe/ae/ue/ss).
- Keine Floskeln, keine Wiederholung der Aufgabe, keine Motivation.
- Konkret: Dateinamen, Pfade, Commands ausschreiben.
- Zeitstempel im Titel = aktuelles Systemdatum + Uhrzeit.
