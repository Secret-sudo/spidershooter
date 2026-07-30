# DENKSPINNE

Ein Ego-Shooter aus der Perspektive der Claude-Denkspinne: Der Denk-Spinner
hört nicht mehr auf zu denken, ihm wachsen acht terracottafarbene Beine,
er klettert aus dem Chatfenster und netzt die Denkfehler ein, die sich im
Inneren der Maschine eingenistet haben.

## Spielen

Im Browser: **https://secret-sudo.github.io/spidershooter/**

Oder `denkspinne.html` herunterladen und per Doppelklick öffnen —
kein Server, kein Build, keine Abhängigkeiten. Läuft auf Desktop und
Smartphone (Touch-Steuerung erscheint automatisch).

> GitHub zeigt `.html`-Dateien in der Datei-Ansicht immer als Quelltext an.
> Zum Spielen die Adresse oben verwenden, nicht die Datei-Ansicht.

### GitHub Pages einrichten (einmalig)

Pages lässt sich nur in den Repository-Einstellungen aktivieren:
**Settings → Pages → Source: «Deploy from a branch» → Branch: `main`, Ordner: `/ (root)` → Save**.
Nach etwa einer Minute ist die Adresse oben erreichbar; `index.html`
leitet dort direkt auf das Spiel weiter.

**Desktop:** WASD laufen · Maus schauen (Klick aktiviert Pointer Lock) ·
Klick/Leertaste: Netz schiessen · halten: Fangnetz · Shift: hetzen · Esc: Pause

**Touch:** Daumen in den sichtbaren Ring unten links: laufen (die Richtung gilt
sofort beim Aufsetzen, Ziehen ist nicht nötig) · rechts wischen: schauen ·
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
