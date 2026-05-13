# EvergreenProtocol

## Die Idee
Ich möchte einen öffentlichen [Perplexity Space](https://www.perplexity.ai/hub/blog/a-student-s-guide-to-using-perplexity-spaces) erstellen, der Menschen hilft, gesünder, fitter und jünger zu werden - frei nach dem Motto: "Die young, as late as possible".

Der Space soll geteilt werden können, ohne dass Benutzer gegenseitig ihre Chats, Gesundheitsdaten oder Notion-Inhalte sehen. Jeder Benutzer arbeitet in seiner eigenen Perplexity-Session und mit seinem eigenen Notion-Workspace.

Der Schlüssel liegt im Darm, in der Leber und im Cortisol. Das EvergreenProtocol betrachtet Gesundheit nicht als lose Sammlung von Tipps, sondern als messbares, iteratives Lebensprotokoll: Daten sammeln, Muster erkennen, Hypothesen bilden, Interventionen testen, Ergebnisse beobachten und nachjustieren.

Die Haltung ist bewusst mutig und mechanistisch. Der Space soll nicht wie ein Standard-Arzt antworten, sondern wie ein Forscher, Tüftler und Alchemist: neugierig, präzise, datengetrieben, mit biologischer Tiefe. Gleichzeitig braucht er klare Stoppsignale bei offensichtlichen Risiken, Kontraindikationen oder medizinischen Notfällen.

Beste Erfahrung habe ich mit Fasten gemacht: nur Mittwoch, Samstag und Sonntag essen. Bei einem Plateau kann ein längeres Wasserfasten ein Werkzeug sein, muss aber sauber vorbereitet, begleitet und anhand von Risikofaktoren bewertet werden. Gute Supplements wie Glycin, Lithium, Spermidin etc. können gezielt eingesetzt werden. Für die Küche wird zum Süssen Alulose verwendet, ergänzt durch Glycin, wenn es kulinarisch passt.

Ziel ist es, ohne Verzicht auf Genuss die Autophagie zu fördern, die Leber zu entlasten und Cortisol zu senken. Zusätzlich sollen typische Altersleiden wie Demenz, Krebs, Herz-Kreislauf-Erkrankungen, Diabetes und Osteoporose vermieden oder zumindest reduziert werden.

Die Ernährungsphilosophie ist klar: kein Zucker, wenig strategische Kohlenhydrate, gesunde Fette als Hauptenergiequelle, ausreichend Protein an Esstagen. Kohlenhydrate sind nicht absolut verboten, aber sie sind nicht die Basis des Protokolls.

Dazu kommt Bewegung: Tai Chi als tägliche Morgen- und Abendroutine, mindestens 8000 Schritte pro Tag sowie mindestens 3x pro Woche 8x8x8-Training. 8x8x8 soll für alle funktionieren, weil die Belastung skaliert wird: Anfänger starten mit Körpergewicht und sehr einfachen Übungen, danach folgt TRX, danach Hanteln oder Gym.

Für einfache Übungen zu Hause, im Hotel oder unterwegs wird zusätzlich Dr. Alekseev DE als Trainingsquelle genutzt. Die Übungen sollen niedrigschwellig, effizient und ohne grosses Setup in den Daily Loop integrierbar sein.

Für Stress, Wut, Ärger und mentale Selbstkontrolle ergänzt der `mental-coach` das Protocol mit stoischen Übungen, Atemtechniken, Reiz-Reaktions-Abstand und kurzen Cortisol-Resets. Als praktische Inspirationsquelle dient der Kanal `Der Stoiker`.

Die Daten werden in einer persönlichen [Notion Datenbank](https://www.notion.so/) mit Dashboard und Kalender gesammelt, um Fortschritte zu verfolgen und Anpassungen vorzunehmen.

## Grundprinzipien
- **Public Space, private Daten:** Der Perplexity Space ist teilbar, aber jeder Benutzer arbeitet mit eigener Session und eigenem Notion-Workspace.
- **Notion als Gedächtnis:** Perplexity analysiert und entscheidet im Chat, Notion speichert Profil, Baseline, Tageswerte, Interventionen, Supplements, Training, Schlaf, Blutdruck, Gewicht und Fortschritt.
- **Mutig, aber nicht blind:** Keine generischen Standardempfehlungen, aber klare Red Flags bei gefährlichen Symptomen, Essstörungen, Schwangerschaft, schweren Erkrankungen, Medikamenteninteraktionen oder extremen Fastenrisiken.
- **Mechanistisch statt moralisch:** Jede Empfehlung soll erklären, welcher biologische Hebel angesprochen wird: mTOR, AMPK, Autophagie, Insulin, Cortisol, Schlafdruck, Leberlast, Mikrobiom, Entzündung, Muskelreiz.
- **Genuss statt Verzicht:** Die Koch-Skills zeigen, dass gesunde Ernährung nicht karg sein muss. Zucker wird ersetzt, Geschmack nicht.

## Voraussetzungen
- Waage mit Körperanalysefunktion (z.B. [Beurer BF 500](https://www.apfelkiste.ch/beurer-diagnosewaage-bf-500-led-personen-waage-zur-korperanalyse-batterie-76011-schwarz.html))
- Blutdruckmessgerät (z.B. [Beurer BC 54](https://www.apfelkiste.ch/beurer-bc-54-blutdruckmessgerat-fur-das-handgelenk-mit-inflation-technology-app-funktion-fur-ios-android-schwarz.html))
- Schlaftracker (z.B. [Fit Ring](https://de.aliexpress.com/item/1005010645672784.html))
- TRX Band (z.B. https://www.decathlon.ch/de/p/aufhangeschlingen-schwarz-blau/336337/c382c98c227m8667958)
- Kleines Hantelset optional (z.B. https://www.gonser.ch/kurzhantel-set-1-3-kg-mit-staender/a-25841/)

## Workflow

### Erste Schritte
1. Der neue Benutzer schreibt `INIT`. Dadurch startet der Space einen geführten Onboarding-Dialog und fragt die Informationen für seine persönliche `Me.md` einzeln ab.

2. Am Ende von `INIT` generiert Perplexity eine vollständige `Me.md`. Der Benutzer lädt diese Datei herunter und fügt sie anschliessend in seiner eigenen Space-Session wieder hinzu.

3. Falls der Benutzer lieber manuell arbeitet, kann er die Fallback-Vorlage `Perplexity/Me.template.md` nutzen, ausfüllen und danach als persönliche `Me.md` hochladen.

4. Die persönliche `Me.md` enthält mindestens Name, Vorname, Alter, Geschlecht, aktuelles Gewicht, Beruf, Notion-Link, Pain Points, aktuelle Diagnosen, aktuelle Medikamente, Supplemente sowie bekannte Allergien oder Unverträglichkeiten. Ziele, Trainingsstand und Essmuster werden danach im Gespräch und über die Baseline erhoben.

5. Danach prüfen, ob der Benutzer einen eigenen Notion-Account und einen leeren oder vorbereiteten Notion-Workspace besitzt. Falls nicht, erklärt der Space, wie man Notion einrichtet.

6. Danach prüfen, ob in Perplexity der Notion Connector aktiviert ist. Falls nicht, stellt der Space eine Anleitung bereit, wie man den Notion Connector aktiviert und mit dem eigenen Notion-Account verbindet.

7. Wenn alles eingerichtet ist, hilft der `notion-architect`, im persönlichen Notion-Workspace die Datenbanken, Relationen, Views, Kalender und das Dashboard zu erstellen.

8. Danach analysiert Perplexity die `Me.md` und trägt die Informationen in die Notion-Datenbanken ein.

### Baseline schaffen
Die ersten 7 Tage dienen dazu, eine Baseline zu schaffen. Der Benutzer lädt jeden Morgen seine Daten als Screenshot in den Perplexity Space hoch: Gewicht, Körperanalyse, Blutdruck, Puls, Schlafqualität, Schlafdauer, Tiefschlaf, REM, HRV, Ruhepuls, Schritte und subjektives Befinden.

Perplexity analysiert diese Daten, speichert sie in Notion und bewertet nicht einzelne Ausreisser, sondern Muster: 7-Tage-Mittelwert, Varianz, Trend, Korrelationen und auffällige Signale.

Am Ende der Baseline erstellt Perplexity eine erste Supplementierung. Diese basiert auf Profil, Diagnosen, Medikamenten, bestehenden Supplementen, Schlafdaten, Stressmustern, Gewicht, Blutdruck, Pain Points und den im Onboarding geklärten Zielen. Die Supplementierung wird als Intervention in Notion dokumentiert und später im Daily Loop angepasst.

Zusätzlich weist Perplexity den Benutzer im nächsten Chat darauf hin, beim Arzt einen Jahrescheck zu planen. Dafür generiert der Space ein Dokument `Jahrescheck-Blutwerte.md` mit einer nach Kategorien gruppierten Liste sinnvoller Blutwerte: Basislabor, Glukose- und Insulinstoffwechsel, Lipid- und Herz-Kreislauf-Risiko, Leber, Niere, Elektrolyte, Eisenstatus, Schilddrüse, Vitamine, Mineralstoffe, Harnsäure und kontextabhängige Hormone. Dieses Dokument ist eine Gesprächsgrundlage für den Arzt, keine Anordnung. Der Benutzer soll es herunterladen, zum Termin mitnehmen und anschliessend als Attachment zu seiner eigenen Space-Session hinzufügen. Sobald die Laborresultate vorliegen, lädt er auch den Laborbericht hoch, damit der Space die Werte in Notion speichern und die Empfehlungen besser personalisieren kann.

#### Beispiel-Screenshots für den Baseline-Capture
Die Dateien unter `Perplexity/.sample screenshots/` zeigen, wie Benutzer ihre Gesundheitsdaten später direkt in den Perplexity-Chat hochladen. Diese Screenshots sind keine Design-Mockups, sondern realistische Eingaben für die Extraktion und Speicherung in Notion.

Aktuell abgedeckte Screenshot-Typen:
- **Körperanalyse / Waage:** Gewicht, BMI, Körperfett, Fettmasse, fettfreies Körpergewicht, Muskelmasse, Muskelrate, Skelettmuskulatur, Knochenmasse, Eiweissmenge, Wassergehalt, viszerales Fett, BMR, biologisches Alter, ideales Körpergewicht, Adipositasgrad.
- **Blutdruck:** Durchschnitt, höchster Wert, niedrigster Wert, systolisch/diastolisch, Datum.
- **Blutsauerstoff:** Durchschnitt, höchster Wert, niedrigster Wert, SpO2-Verlauf, Datum.
- **Schlaf:** Schlafdauer, Tiefschlaf, Leichtschlaf, REM, Schlafunterbrechungen, Schlafzeitfenster, Datum.
- **Herzfrequenz:** Durchschnitt, höchster Wert, niedrigster Wert, Tagesverlauf, Datum.

Diese Beispiele dienen als Referenz für den `notion-architect` und den Baseline-Capture: Perplexity muss die sichtbaren Werte erkennen, fehlende Werte nicht erfinden, Einheiten korrekt übernehmen und die Daten in die passenden Notion-Datenbanken schreiben.

### Das Evergreen Protocol
Nach der Baseline-Phase startet das Evergreen Protocol. Perplexity gibt dem Benutzer täglich Empfehlungen, basierend auf den gesammelten Daten, der `Me.md`, den Notion-Trends und den Zielen. Diese Empfehlungen können Fasten, Ernährung, Supplemente, Training, Tai Chi, Meditation, Schlaf, Atemübungen, Leberentlastung, Cortisol-Management und Küchenideen umfassen.

#### Daily Loop
1. Der Benutzer lädt morgens seine Daten als Screenshot hoch.
2. Perplexity analysiert die letzte Nacht, den aktuellen Morgen und den Verlauf der letzten 14 Tage.
3. Perplexity gibt eine tief analytische, mechanistische Auswertung aus: Was hat sich verändert, welche Hypothesen erklären es, welcher Hebel ist heute am wichtigsten?
4. Perplexity passt bei Bedarf Supplementierung, Training, Fastenfenster oder Erholung in Notion an.
5. Perplexity motiviert den Benutzer, indem es Fortschritte sichtbar macht, Reibung reduziert und die nächste machbare Handlung empfiehlt.

### Training Progression
8x8x8 ist das Standard-Kraftprotokoll, aber die Belastung wird individuell skaliert.

1. **Stufe 1 - Körpergewicht:** sehr einfache Übungen, saubere Bewegung, niedrige Einstiegshürde.
2. **Stufe 2 - TRX:** mehr Bewegungsumfang, bessere Rumpfstabilität, dosierbare Zug- und Druckübungen.
3. **Stufe 3 - Hanteln/Gym:** progressive Last, Hypertrophie, Kraft, metabolischer Reiz.

Der Schwerpunkt liegt auf Motivation. Viele Menschen mit gesundheitlichen Problemen haben nicht zu wenig Information, sondern zu wenig Energie, Vertrauen und positive Rückkopplung.

## Schwerpunkte
- kein Zucker (dafür Alulose und Glycin)
- Fasten, fasten, fasten (schon dadurch wird man fitter und motivierter)
- Autophagie fördern (durch Fasten, Spermidin, etc.)
- Leber entlasten (durch Fasten, Glycin, etc.)
- Cortisol senken (durch Fasten, Meditation, etc.)
- Alzheimer, ADHS, Demenz Prävention / Reduktion.
- täglich mindestens 8000 Schritte gehen, täglich Tai Chi.
- gesunde Fette als Hauptenergiequelle, Kohlenhydrate nur strategisch.
- Supplementierung datenbasiert starten und im Verlauf anpassen.

## Ressourcen
- [Meine Antiaging Playlist](https://youtube.com/playlist?list=PLriKR1xQz6aIDFCN1oKTB0zmASZ_qUOfX&si=tz11ODSx72V7aYbv)
- [Dr. Eric Berg](https://www.youtube.com/@Drberg)
- [Dr. Sten Ekberg](https://www.youtube.com/@drekberg)
- [Dr. med. Ulrich Selz](https://www.youtube.com/@DrUlrichSelz)
- [Dr. med. Ulrich Bauhofer](https://www.youtube.com/@dr.ulrich.bauhofer)
- [Doktor Weigl](https://www.youtube.com/@DoktorWeigl1)
- [Fabian Kowallik](https://www.youtube.com/@ExiledMedicDe)
- [Bryan Johnson](https://www.youtube.com/@BryanJohnson)
- [Dr. Adam Potts](https://www.youtube.com/@beginwithbreathtaichi)
- [Vince Gironda](https://www.sportnahrung-engel.de/trainingsplaene/muskelaufbau-fortgeschrittene/8x8-training-vince-gironda)
- [Dr. Alekseev DE](https://www.youtube.com/@DoctorAlekseevDE)
- [Der Stoiker](https://www.youtube.com/@der.stoiker)

## Supplements
Produkte immer über [iHerb](https://www.iherb.com/) bestellen, da es dort die grösste Auswahl und die besten Preise gibt.

## Perplexity Space
- [Instructions](Perplexity/Space Instructions.md)
- [Me.template.md](Perplexity/Me.template.md) ist die Fallback-Vorlage; bevorzugt wird der `INIT`-Dialog, der eine persönliche `Me.md` erzeugt.

### Skills
- [notion-architect](Perplexity/skills/notion-architect)
- [longevity-expert](Perplexity/skills/longevity-expert)
- [personal-trainer](Perplexity/skills/personal-trainer) - Tai Chi, 8x8x8, Progression und Motivation.
- [mental-coach](Perplexity/skills/mental-coach) - Stress, Wut, Ärger, stoische Übungen und Cortisol-Reset.
- [face-yoga](Perplexity/skills/face-yoga) - Gesichtsyoga, Anti-Aging-Routinen, Rizinusöl und einfache Übungen nach Dr. Alekseev DE.

#### Köche Skills
Damit gesunde Küche auch schmeckt, sollen 5 top köche als skills bereit stehen. **Alle** Köche haben auf Zucker zu verzichten und stattdessen Alulose und Glycin zu verwenden. Honig nur wenn es nicht anders geht. Und immer das Motto "Fett ist gut, Kohlenhydrate sind böse" im Hinterkopf zu behalten. Es soll gezeigt werden, dass gesunde Ernährung nicht Verzicht bedeutet, sondern Genuss pur sein kann.
- [Stefan Jäckel](Perplexity/skills/stefan-jaeckel) - Zürcher Umami, Rôtisserie, Sauce und High-Low-Genuss.
- [Andreas Caminada](Perplexity/skills/andreas-caminada)
- [Michel Guerard](Perplexity/skills/michel-guerard)
- [Tim Armann](Perplexity/skills/tim-armann)
- [Zineb Hattab](Perplexity/skills/zineb-hattab)