# One-Shot Prompt: DENKSPINNE

> Alles ab der Trennlinie in Claude Code einfügen.

---

Baue mir ein vollständig spielbares Browser-Game namens **DENKSPINNE**. Ein Ego-Shooter aus der Perspektive einer Spinne, die aus dem Denk-Spinner von Claude entstanden ist. Liefere am Ende **eine einzige Datei** `denkspinne.html` ab, die per Doppelklick im Browser läuft.

## 0. Harte Rahmenbedingungen

- Genau **eine** HTML-Datei. HTML, CSS und JavaScript inline. Kein Build-Step, kein npm, keine Module-Imports.
- **Keine externen Dependencies** außer Google Fonts über `<link>`. Kein Three.js, kein Phaser. Die 3D-Ansicht wird von Hand als Raycaster auf `<canvas>` 2D gerendert.
- Keine externen Bild- oder Audiodateien. Alle Grafik wird prozedural gezeichnet, aller Sound per WebAudio synthetisiert.
- Läuft auf Desktop **und** Smartphone (iOS Safari, Android Chrome). Portrait und Landscape.
- Zielperformance: 60 fps auf einem drei Jahre alten Mittelklasse-Handy. Interne Renderauflösung deshalb herunterskalieren (CSS-Pixel geteilt durch 2 bis 2,4) und mit `image-rendering: pixelated` hochskalieren. Der körnige Look ist gewollt.
- `prefers-reduced-motion` respektieren: Intro-Animationen und Kamera-Bob dann abschalten, Spiel bleibt spielbar.
- Kommentierter, lesbarer Code. Alle Tuning-Werte in einem `CONFIG`-Objekt oben in der Datei, damit ich Geschwindigkeit, Sichtweite, Beinlänge etc. an einer Stelle ändern kann.

## 1. Die Prämisse

Claude denkt nach. Im Chatfenster dreht sich dabei der vertraute Denk-Spinner: ein Spark mit strahlenförmigen, unregelmässig langen Speichen, der langsam pulsiert, daneben wechselnde Denkverben. Irgendwann hört das Denken nicht mehr auf. Dem Spark wachsen acht terracottafarbene Beine, er klettert aus dem Chatfenster heraus und läuft durch das Innere der Maschine, um die Denkfehler einzunetzen, die sich dort eingenistet haben.

Der Ton des Spiels: trocken, technisch, leicht unheimlich. Keine Witze im UI, keine Emojis. Alle Texte auf Deutsch, Schweizer Orthografie (ss statt ß).

**Die Claude-Identität ist Pflicht, nicht Dekoration.** Drei Merkmale müssen jederzeit sofort erkennbar sein: (1) die Terracotta-Farbe #D97757 der Beine und des Sparks, (2) der Denk-Spinner mit unregelmässigen Strahlen als Herkunft der Spinne, (3) die wechselnden Denkverben («Grübelt…», «Sinniert…», «Verknüpft…», «Gewichtet…», «Entwirrt…») an allen Ladestellen.

## 2. Der Ego-Charakter: die Denkspinne

Das ist das Herz des Spiels. Hier bitte besonders sorgfältig arbeiten.

### Herkunft und Körper

- **Kopf/Körper**: eine dunkle Kugel, umgeben von strahlenförmigen Speichen mit unregelmässigen Längen wie beim Claude-Spark — kein gleichmässiges Zahnrad. Diese Speichen bleiben sichtbar, verkümmern aber zu blassen Stummeln, sobald die Beine da sind. Zwei kleine schwarze Augen.
- **Acht Beine**, terracotta. Drei Segmente pro Bein: Hüfte, Knie, Fuss. Nach oben gewölbt, dann steil nach unten zum Fuss — die klassische Spinnensilhouette, kein Insektenbein.
- Die Beine sind das einzige, was der Spieler von sich selbst sieht.

### Darstellung in der Ego-Perspektive

Statt einer Waffe wie im klassischen Shooter rahmen die eigenen Beine das Bild:

- **Vier Beine pro Seite**, verankert ausserhalb des Bildrands links und rechts, jeweils leicht versetzt in der Höhe.
- Jedes Bein wölbt sich als Bézier-Kurve nach oben in Richtung Bildmitte, das Knie liegt etwa auf Horizonthöhe, der Fuss deutlich darunter im unteren Bilddrittel.
- **Tiefenstaffelung**: das vorderste Bein am dicksten und hellsten, jedes weitere dünner und dunkler. Jedes Bein bekommt einen weichen schwarzen Schatten hinter sich und eine hellere Kante obendrauf, damit es plastisch wirkt.
- **Gelenkkugel** am Knie, **Fussspitze** als kleiner Punkt.
- Am unteren Bildrand ein warmer terracottafarbener Lichtschein als Andeutung des eigenen Körpers.

### Laufzyklus

- Alle acht Beine laufen in einem **alternierenden Wechselschritt**: linke Seite und rechte Seite gegenphasig, innerhalb einer Seite jedes Bein um etwa 0,85 Radiant versetzt. Das Ergebnis soll wie eine Welle aussehen, die über die Beine läuft.
- Pro Schritt hebt sich der Fuss (Lift) und die ganze Kurve schiebt sich leicht nach vorne (Push). Im Stand läuft der Zyklus mit rund einem Drittel der Amplitude weiter — die Spinne zappelt nervös, statt einzufrieren.
- Bei Rückwärtslauf dreht sich die Phasenrichtung.
- Der Kamera-Bob (leichtes Auf und Ab des Horizonts) ist an denselben Zyklus gekoppelt, damit Beine und Blick synchron wirken.

### Fähigkeiten

- **Laufen und Strafen.** Seitwärts etwas langsamer als vorwärts.
- **Netz schiessen** als Angriff: ein Hitscan aus der Bildmitte. Sichtbar als kurz aufblitzendes Netzmuster, das sich vom Zentrum nach aussen aufspannt und verblasst. Nachladezeit rund 0,28 Sekunden.
- **Fangnetz als Sekundärfeuer**: Schusstaste **halten** (ab 0,45 Sekunden) und loslassen → ein breites Fangnetz, das alle Gegner in einem Kegel vor der Spinne für 2 Sekunden stark verlangsamt, statt zu töten. Cooldown 4 Sekunden, als kleiner Ring am Fadenkreuz sichtbar. Auf Touch: langer Druck auf den Netz-Knopf. Besonders wichtig gegen die schnelle Halluzination.
- **Sprint**: Hält der Spieler die Sprint-Taste, hetzt die Spinne 1,7-mal so schnell, die Beine schlagen doppelt so hektisch und der Bildrand zieht sich sichtbar zusammen. Sprint verbraucht eine Ausdauerleiste, die sich im Stand wieder füllt. Im HUD erscheint dabei klein «denkt schneller».
- **Fünf Trefferpunkte.** Pro abgeschlossener Ebene einen zurück.

## 3. Kamera und Rendering

- Raycaster im Stil von Wolfenstein 3D: DDA-Algorithmus über ein Gitter, ein Strahl pro Bildspalte, Wandhöhe aus der Distanz, Z-Buffer pro Spalte für die Sprite-Verdeckung.
- Sichtfeld etwa 0,85 (Plane-Länge), Blickrichtung als Winkel, zusätzlich ein leichter vertikaler Pitch, der den Horizont verschiebt (kein echtes Freelook nach oben nötig).
- Wände einfarbig pro Ebene, mit Entfernungsnebel abgedunkelt. Nord-Süd-Wände dunkler als Ost-West, damit Ecken lesbar bleiben. Am Wandfuss und Wandkopf eine dünne Akzentlinie in der Ebenenfarbe — das ersetzt Texturen und sieht bewusst nach Maschineninnenraum aus.
- Decke und Boden als vertikale Farbverläufe, kein Floorcasting.
- Vignette über allem: Ränder deutlich abgedunkelt.

## 4. Gegner: die Denkfehler

Vier Typen plus ein Boss. Alle prozedural gezeichnet, spaltenweise gegen den Z-Buffer geclippt, sortiert von hinten nach vorne. Alle sterben mit einem kurzen Partikelzucken.

| Typ | Farbe | Verhalten | Treffer | Ab Ebene |
|---|---|---|---|---|
| **Denkfehler** | Rot | Schläft, bis der Spieler in Sichtlinie und näher als neun Felder ist. Dann direkte Verfolgung mit Wandkollision. Langsamer als der Spieler, wird pro Ebene schneller. | 1 | 1 |
| **Zirkelschluss** | Violett | Verfolgt nicht, kreist um seine Startposition. | 2 | 2 |
| **Endlosschleife** | Amber | Patrouilliert stur eine feste Bahn durch die Gänge, ignoriert den Spieler. Schadet nur bei Berührung — man muss ihr Timing lesen. | 2 | 3 |
| **Halluzination** | Schillernd (Farbton rotiert) | Schnellster Gegner. Flackert alle paar Sekunden kurz weg und materialisiert ein Stück versetzt wieder. | 1 | 3 |
| **Kern-Halluzination** (Boss) | Schillernd, gross | Wartet in der Zentralkammer von Ebene 4. Grosse pulsierende Kugel, 8 Trefferpunkte, Trefferblitz bei jedem Hit, spawnt periodisch kleine Denkfehler nach. Der Ausgang öffnet erst nach ihrem Tod. | 8 | 4 |

- Bei Kontakt mit jedem Gegner: ein Trefferpunkt Schaden, roter Bildschirmblitz, Spieler wird ein Stück zurückgestossen, danach eine Sekunde Unverwundbarkeit.
- Trefferprüfung Netzschuss: Winkelabweichung zur Blickmitte kleiner als die Winkelbreite des Gegners auf dieser Distanz, maximale Reichweite elf Felder, Sichtlinie muss frei sein.
- **Gegner-Kompass**: Sind nur noch zwei oder weniger Gegner übrig, zeigt ein dezenter türkiser Punkt am Rand des Fadenkreuzes grob die Richtung zum nächsten lebenden Gegner. Kein Suchen in leeren Sackgassen.

## 5. Ebenen

Vier Ebenen, jede als Array von Zeichenketten. `#` Wand, `.` Boden, `E` Ausgang, `S` Startposition, `t` Token, `K` Klarheit. Jede Ebene 16×16 bis 22×16 Felder.

| Nr. | Name | Charakter | Farbwelt | Gegner |
|---|---|---|---|---|
| 1 | Kontextfenster | Weite, symmetrische Kammern. Zum Warmlaufen. | Blau, Wände #3B4780, Akzent #7C8AD8 | 5 Denkfehler |
| 2 | Assoziationslabyrinth | Enge, verwinkelte Gänge, viele Sackgassen. | Violett, Wände #4A3A72, Akzent #B285E8 | 8 gemischt |
| 3 | Trainingsdaten-Archiv | Lange Regalreihen, Sichtachsen quer durch die Karte. | Grün-Petrol, Wände #1F4A44, Akzent #5FD4B0 | 10 gemischt |
| 4 | Halluzinationskern | Ringförmig um eine zentrale Kammer. Alles beisst. | Rot, Wände #7A3040, Akzent #FF8A6B | 12 gemischt + Boss |

- Gegner werden zufällig auf Bodenfelder gesetzt, die mindestens fünf Felder vom Start entfernt sind (der Boss fest in der Zentralkammer).
- **Tokens**: kleine schwebende, leuchtende Fragmente auf den Ebenen verteilt, gezielt auch in Sackgassen. Einsammeln durch Drüberlaufen, je 25 Punkte, leises Aufsteigen-Plink. **Klarheit**: seltenes helles Pickup, +1 Trefferpunkt (maximal 5), ein bis zwei pro Ebene ab Ebene 2.
- **Score**: Gegner 100 (Zirkelschluss/Endlosschleife 150, Halluzination 200, Boss 1000), Tokens 25, Zeitbonus pro Ebene, Trefferquoten-Bonus am Ende. Highscore in `localStorage`.
- Der Ausgang ist eine türkise, senkrecht pulsierende Lichtsäule. Solange noch Denkfehler leben, ist sie stark abgedunkelt; beim Betreten kommt der Hinweis *«Erst alle Denkfehler einnetzen»*. Ist alles geräumt, leuchtet sie auf und der Zähler springt zur nächsten Ebene.
- Zwischen den Ebenen eine Karte mit Ebenennummer, Name, einem Satz Charakterisierung, einem rotierenden Denkverb («Verknüpft…») und dem Knopf *Weiterlaufen*.

## 6. Steuerung

**Desktop**
- WASD oder Pfeiltasten laufen, Pfeile links/rechts drehen alternativ.
- Maus schaut, per Pointer Lock. Erster Klick auf die Szene aktiviert den Lock, danach schiesst jeder Klick. **Fallback**: Verweigert der Browser den Pointer Lock, funktioniert Schauen per Maus-Drag.
- Leertaste schiesst (halten für Fangnetz), Shift sprintet, Escape pausiert und gibt den Lock frei.

**Touch**
- Linke Bildschirmhälfte: virtueller Joystick mit sichtbarem Ring und Knauf, analog in acht Richtungen.
- Rechte Bildschirmhälfte: Wischen dreht die Blickrichtung und kippt den Pitch.
- Runder Netz-Knopf unten rechts (kurz tippen: Schuss, lang drücken: Fangnetz), Sprint-Knopf darüber. Beide mit sichtbarem Druckzustand.
- Multitouch muss sauber funktionieren: laufen, schauen und schiessen gleichzeitig. Touch-IDs einzeln verfolgen, `touch-action: none`, kein Doppeltipp-Zoom, `env(safe-area-inset-*)` respektieren.

## 7. HUD

Sparsam, monospaced, in halbtransparenten Kästen mit dünnem Rahmen:
- Oben links: Ebenennummer und Name, darunter die Trefferpunkte als Balkensegmente, darunter die Ausdauerleiste.
- Oben rechts: Anzahl noch lebender Denkfehler und der aktuelle Score.
- Mitte: Fadenkreuz aus vier Strichen mit Lücke im Zentrum, türkis. Darum der Fangnetz-Cooldown-Ring und bei ≤2 Gegnern der Kompass-Punkt.
- Kurze Statusmeldungen in Versalien auf etwa einem Viertel der Bildhöhe, ein- und ausblendend: `EINGENETZT`, `AUSGANG OFFEN`, `EBENE GESCHAFFT`, `KLARHEIT +1`.

## 8. Bildschirme und Ablauf

1. **Titel — die Chatfenster-Transformation.** Die Signatur des ganzen Spiels, hier keine Abkürzungen: Der Bildschirm zeigt ein Claude-Chatfenster auf cremefarbenem Grund (#F0EEE6) — Eingabezeile, eine bereits getippte Nutzerfrage («Warum denkst du so lange?»), darunter der Denk-Spinner mit pulsierenden, unregelmässig langen Speichen und daneben wechselnde Denkverben («Grübelt…», «Sinniert…», «Verknüpft…»). Nach wenigen Sekunden — oder sofort beim Klick auf *Beine ausfahren* — kippt die Szene: Die Denkverben wechseln immer schneller, der Spinner färbt sich satt terracotta, aus den Speichen wachsen nacheinander acht Beine per `stroke-dashoffset`, zwei Augen erscheinen, die Spinne zuckt. Dann klettert sie **über den Rand des Chatfensters**, das Fenster kippt und fällt nach hinten weg, das Cremeweiss reisst auf und dahinter liegt bereits die dunkle, laufende 3D-Maschinenwelt, in die die Kamera hineinfährt. Übergang nahtlos, ohne Schnitt, ohne schwarzen Frame. Darunter während des Wartens: Titel, drei Zeilen Prämisse, Steuerungshinweise passend zu Maus oder Touch.
2. **Ebenenkarte** zwischen den Ebenen, mit Denkverb.
3. **Game Over**: Überschrift *AUS*, Text über die schnelleren Denkfehler, Score und Highscore, Knopf *Neu starten*.
4. **Sieg — die Rückverwandlung.** Überschrift *SAUBER*. Die Umkehrung des Intros: die Spinne zieht die Beine ein, wird wieder Spinner, das Chatfenster erscheint wieder — und die Antwort ist fertig ausgegeben. Dazu die Gesamtzeit, die Trefferquote, die Tokens, der Score mit Highscore und ein Rang je nach Leistung: unter 40 % Trefferquote *«Stochastischer Papagei»*, unter 70 % *«Feinjustiert»*, darüber *«Vollständig aligned»*.
5. Hinter allen Menüs (ausser Titel und Sieg-Chatfenster) läuft die Szene weiter und die Kamera dreht sich langsam. Kein schwarzer Bildschirm.

## 9. Art Direction

- **Palette**: Void #080A11, Tiefe #111730, Stahl #2A3358, Bein-Terracotta **#D97757** (die Claude-Farbe — verbindlich), Bein-Glanz #E8A188, Chat-Creme #F0EEE6, Netz-Türkis #6FE7D2, Fehler-Rot #F0455F, Amber #E8A33D, Text #C9D2F0, gedämpft #5A6489.
- **Typografie**: Space Grotesk 600/700 für Überschriften und Knöpfe, IBM Plex Mono für HUD und Fliesstext. Weite Laufweite, Versalien für Labels.
- Keine abgerundeten Ecken, keine Schlagschatten, keine Farbverläufe in Knöpfen. Der einzige Blickfang sind die terracottafarbenen Beine — alles andere bleibt ruhig.
- Ausnahme: Das Chatfenster im Intro und im Sieg-Screen ist bewusst hell, cremefarben und freundlich — der Kontrast zwischen Claude-Oberfläche und Maschineninnerem ist das zentrale visuelle Motiv des Spiels.

## 10. Sound

Komplett über WebAudio synthetisiert, erst nach der ersten Nutzerinteraktion starten, jederzeit stummschaltbar über einen kleinen Knopf im HUD:
- Schritte: kurze gefilterte Rauschimpulse, an den Laufzyklus gekoppelt.
- Netzschuss: absteigendes Zwitschern. Fangnetz: breiteres, tieferes Zwitschern.
- Treffer am Gegner: kurzes trockenes Knacken. Token: leises Plink.
- Eigener Schaden: tiefer Sinus-Thump.
- Ambient: ganz leiser tiefer Drone, dessen Tonhöhe sich pro Ebene ändert. Beim Boss schneller Puls.

## 11. Abnahmekriterien

Am Ende prüfen und mir bestätigen:

- [ ] Die Datei läuft per Doppelklick, ohne Server, ohne Konsolenfehler.
- [ ] Titelbildschirm: Chatfenster mit Denkverben und Spinner, Beine wachsen sichtbar heraus, die Spinne klettert aus dem Fenster, das Fenster fällt weg, nahtloser Übergang in die 3D-Welt.
- [ ] Die Beine und der Spark sind in Claude-Terracotta #D97757 gehalten.
- [ ] In der Ego-Ansicht sind acht Beine sichtbar, die beim Laufen einen Wellen-Wechselschritt zeigen.
- [ ] Alle vier Ebenen sind erreichbar und durchspielbar, jede sieht farblich anders aus.
- [ ] Alle vier Gegnertypen verhalten sich unterschiedlich und sind farblich unterscheidbar; der Boss auf Ebene 4 funktioniert inklusive Nachspawnen.
- [ ] Fangnetz (Sekundärfeuer) verlangsamt Gegner und hat sichtbaren Cooldown.
- [ ] Tokens und Klarheit sind einsammelbar, Score und Highscore (localStorage) funktionieren.
- [ ] Gegner-Kompass erscheint bei zwei oder weniger verbleibenden Gegnern.
- [ ] Ausgang bleibt verschlossen, bis alle Gegner erledigt sind, mit passendem Hinweistext.
- [ ] Auf dem Handy: gleichzeitig laufen, schauen und schiessen funktioniert. Kein Zoom, kein Scrollen, kein Verrutschen.
- [ ] Keine Wanddurchdringung, auch nicht beim diagonalen Anlaufen an Ecken.
- [ ] Sound lässt sich stummschalten.
- [ ] `CONFIG`-Objekt enthält mindestens: Laufgeschwindigkeit, Drehempfindlichkeit, Sichtfeld, Renderskalierung, Beinanzahl, Beinamplitude, Netz-Reichweite, Nachladezeit, Fangnetz-Dauer und -Cooldown, Gegnergeschwindigkeit pro Ebene, Startleben.

## 12. Nicht tun

- Keine 3D-Bibliothek, kein WebGL. Der handgeschriebene Raycaster ist Teil des Auftrags.
- Keine Platzhalter-Assets, keine `TODO`-Kommentare, keine nur halb implementierten Funktionen.
- Keine Aufteilung in mehrere Dateien.
- Kein cremefarbener Hintergrund in der Spielwelt selbst — Creme gehört ausschliesslich dem Chatfenster im Intro und im Sieg-Screen. Die Spielwelt ist dunkel und technisch.
- Die Beine nicht als simple Striche zeichnen. Sie sind die Hauptfigur, sie brauchen Segmente, Verjüngung, Gelenke und Tiefenstaffelung.

Frag nicht nach Rückfragen, sondern baue die vollständige Datei. Wenn eine Designentscheidung offen ist, treffe sie selbst und dokumentiere sie in einem kurzen Kommentarblock am Anfang der Datei.
