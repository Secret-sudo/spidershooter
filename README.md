# DENKSPINNE

Ein Ego-Shooter aus der Perspektive der Claude-Denkspinne: Der Denk-Spinner
hört nicht mehr auf zu denken, ihm wachsen acht terracottafarbene Beine,
er klettert aus dem Chatfenster und netzt die Denkfehler ein, die sich im
Inneren der Maschine eingenistet haben.

## Spielen

`denkspinne.html` herunterladen und per Doppelklick im Browser öffnen —
kein Server, kein Build, keine Abhängigkeiten. Läuft auf Desktop und
Smartphone (Touch-Steuerung erscheint automatisch).

**Desktop:** WASD laufen · Maus schauen (Klick aktiviert Pointer Lock) ·
Klick/Leertaste: Netz schiessen · halten: Fangnetz · Shift: hetzen · Esc: Pause

**Touch:** linke Hälfte: Joystick · rechte Hälfte: schauen ·
NETZ tippen: Schuss, halten: Fangnetz · HETZEN: Sprint

## Inhalt

- 4 Ebenen (Kontextfenster, Assoziationslabyrinth, Trainingsdaten-Archiv,
  Halluzinationskern) mit eigener Farbwelt
- 4 Gegnertypen (Denkfehler, Zirkelschluss, Endlosschleife, Halluzination)
  plus Boss «Kern-Halluzination»
- Tokens, Klarheit-Pickups, Score mit Highscore (localStorage),
  Gegner-Kompass, Fangnetz-Sekundärfeuer
- Handgeschriebener Raycaster auf Canvas 2D, aller Sound per WebAudio
  synthetisiert, alles in einer einzigen HTML-Datei

## Dateien

- `denkspinne.html` — das vollständige Spiel (eine Datei)
- `denkspinneprompt.md` — das One-Shot-Prompt, aus dem das Spiel entstanden ist
