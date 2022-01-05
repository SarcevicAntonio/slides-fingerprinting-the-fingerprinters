---
highlighter: shiki
layout: cover
lineNumbers: true
background: https://images.pexels.com/photos/8382599/pexels-photo-8382599.jpeg
title: "Die Fingerabdrücke der Fingerabdrucknehmer: Browser-Fingerprinting-Verhalten lernen und erkennen"
---

# Die Fingerabdrücke der Fingerabdrucknehmer

## Browser-Fingerprinting-Verhalten lernen und erkennen

Vorgetragen von Antonio Sarcevic - Hackerpraktikum - WS 2021/22 - FH Münster

<a href="https://web.cs.ucdavis.edu/~zubair/files/fpinspector-sp2021.pdf" class="footer">U. Iqbal, S. Englehardt, and Z. Shafiq, “Fingerprinting the fingerprinters: Learningto detect browser fingerprinting behaviors” , 2020.</a>

<!-- prettier-ignore-start -->
<!--  -->
---
layout: image-right
image: https://images.pexels.com/photos/8382611/pexels-photo-8382611.jpeg
---
<!-- prettier-ignore-end -->

# Inhalt

- <ph-fingerprint/> Browser-Fingerprinting

- <ph-magnifying-glass/> FP-INSPECTOR

  - Vorarbeit

  - Design

  - Evaluation

  - Limitationen

- <ph-user-focus/> Fingerprinting der Top-100k

- <ph-hand-eye/> Analyse der für Fingerprinting missbrauchten APIs

- <ph-lightbulb/> Fazit

---

# <ph-fingerprint/> Browser-Fingerprinting

## Online Tracking durch Fingerprinting (FP)

- Form von stateless tracking

- **Missbrauchen** von JavaScript APIs und HTTP header um Geräte eindeutig zu identifizieren

  - z.B. `Canvas` und `User-Agent`

- Aufdringlicher als z.B. Cookies da

  1. undurchsichtig

  2. fehlende Kontrolle

- Fingerprinting z.B. für bot-detection (Google reCAPTCHA)

  - aber eben auch Cross-Site Tracking möglich

  - oder um Cookies zu "regenerieren"

<!--
- stateless: ohne speichern eines Tokens im Client
  - Gegensatz zu stateful tracking über Cookie oder Local Storage

- laut Browser Entwickler und Standard Autoritäten wie dem World Wide Web Consortium (W3C) ist Fingerprinting auch eine "missbräuchliche Praxis"

- Aufdringlicher als Cookies da 1. Cookies im Browser angezeigt werden und 2. bearbeitet bzw. gelöscht werden können

- Cross-Site Tracking möglich und vor allem interessant weil z.B. Safari und Firefox immer stärker gegen third-party cookies vorgehen
-->

---

# <ph-fingerprint/> Browser-Fingerprinting

## Ursprünge vom Fingerprinting

- [Mayer](https://jonathanmayer.org/publications/thesis09.pdf) zeigt 2009 das "quirkiness" (Systemkonfiguration) Nutzer identifizieren kann

- 2010 entwickelt [Eckersley](https://link.springer.com/chapter/10.1007/978-3-642-14527-8_1) die Seite [Panopticlick](http://panopticlick.eff.org/) um Browser-Fingerprinting durchzuführen zu analysieren

- Ausnutzung verschiedener APIs für Fingerprinting im freien Web in verschiedenen Studien gefunden [[37]](https://dl.acm.org/doi/10.1145/2660267.2660347), [[38]](https://www.esat.kuleuven.be/cosic/publications/article-2334.pdf), [[47]](https://www.esat.kuleuven.be/cosic/publications/article-3078.pdf), [[54]](https://arxiv.org/pdf/1812.01514.pdf),

- Moderne Browserfeatures und JavaScript APIs und bieten immer mehr Oberfläche für fingerprinting (`Canvas`, `WebGL`, Browser-Erweiterungen, Sensoren an Mobilgeräten, ...)
  [[75]](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=6547132), [[78]](https://petsymposium.org/2017/papers/hotpets/batterystatus-not-included.pdf)

- W3C startete 2011 Entwicklung eines [Leitfadens zur Auswertung neuer Webstandards im Bezug auf Privatsphäre](https://www.w3.org/2019/09/privacy-ig-charter.html)

<!--
- 2009: Damals waren Java und Flash noch relevant
  - waren noch Fataler als JavaScript, da diese Umgebungen noch mehr Informationen über Hardware und Systemkonfiguration verarbeiten konnten
  - wobei JavaScript halt auch schon ausreicht um genug Schabernack zu Treiben
- 2010: Panopticlick analysiert browser und requests um einen Fingerprint zu erstellen und gleicht es mit einer Datenbank um zu ermitteln ob der Fingerprint einzigartig ist und zur Identifizierung taugt
  - Seite mittlerweile umgebaut und nun ein analyse gegen generelles Browser Tracking
  - Alternativ gibt es auch noch https://amiunique.org/fp welches sich eher an Technischere Menschen richtet und nur Fingerprinting betrachtet

- Browserfeatures wie Bettery Status API in Firefox bereits deprecated wegen Datenschutzbedenken
-->

---

# <ph-fingerprint/> Browser-Fingerprinting

## Bietet Fingerprinting eindeutige und dauerhafte Identifikatoren?

- Fingerprint als "statistischer" Identifikator

- Identifizierbarkeit abhängig von Anzahl Geräten mit gleicher Konfiguration

  - bzw. "uniqueness" des Fingerprints

- Laut [Laperdrix et al.](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=7546540) ([AmIUnique](https://amiunique.org/)) und [Eckersley](https://link.springer.com/chapter/10.1007/978-3-642-14527-8_1) ([Panopticlick](http://panopticlick.eff.org/)) besitzen 83% - 90% der Geräte eindeutigen Fingerprint (wahrscheinlich etwas biased)

- [Broix et al.](https://hal.inria.fr/hal-01718234v2/document) finden auf der Seite eines französischen Verlags nur 33.6% Geräte mit eindeutigem Fingerprint (Mehr Daten für mehr uniqueness benötigt)

- Desktop Browser eher unique als mobile Browser

- Stabile oder Verknüpfbare Fingerprints zum Tracking benötigt:

  - Laut Eckersley haben 37% der wiederkehrende Besucher mehr als einen Fingerprint, aber 65% der Geräte konnten über einfache heuristische Verknüpfung re-identifiziert werden

  - [Vastel et al.](https://hal.inria.fr/hal-01652021/document#:~:text=FP%2DSTALKER%20is%20an%20approach,machine%20learning%20to%20boost%20accuracy.) verbesserten 2017 diese Heuristik und konnten auf AmIUnique Besucher im Schnitt 74 Tage verfolgen

<!--
- "statistischer" also kein deterministischer Identifikator

- AmIUnique und Panopticlick wird primär von technisch Interessierten Personen genutzt, daher sind die Zahlen biased
-->

---

# <ph-fingerprint/> Browser-Fingerprinting

## Verbreitung

- 2013 Paper ["Cookieless Monster: Exploring the Ecosystem of Web-based Device Fingerprinting"](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=6547132)

  - 3 fingerprinting Anbieter

  - 40 FP-Seiten aus Alexa Top-10k **(0.4%)**

- 2013 Paper ["FPDetective: Dusting the Web for Fingerprinters"](https://www.esat.kuleuven.be/cosic/publications/article-2334.pdf)

  - 404 FP-Seiten aus Alexa Top-1 Millionen **(0.04%)**

- Weitere Studien [[37]](https://dl.acm.org/doi/10.1145/2660267.2660347), [[47]](https://www.esat.kuleuven.be/cosic/publications/article-3078.pdf), [[54]](https://arxiv.org/pdf/1812.01514.pdf),

  [[78]](https://petsymposium.org/2017/papers/hotpets/batterystatus-not-included.pdf) finden immer mehr FP-Seiten über die Jahre.

- 2019 schreibt [Washington Post](https://www.washingtonpost.com/technology/2019/10/31/think-youre-anonymous-online-third-popular-websites-are-fingerprinting-you/): "At least a third of the 500 sites Americans visit most often use hidden code to run an identity check on your computer or phone." **(>30%)**

<!--
- Weitere Studien: Studien verwenden alle verschiedene Strategien um auf fingerprinting zu prüfen, dennoch lässt sich ein Trend erkennen
- Trotz der verstärkten Kontrolle durch Browser-Anbieter und die Öffentlichkeit ist das Fingerprinting nach wie vor weit verbreitet.
-->

---

# <ph-fingerprint/> Browser-Fingerprinting

## Mitigation

- randomization: Rückgabewerte von APIs mit Rauschen randomisieren (Canvas Defender)

  - kann Funktionen der Seite verändern

  - reversibel, wenn "random" Wert auf Ursprungswert basiert

  - Noise output kann sogar als Datenpunkt fingerprint genutzt werden

- normalization: Rückgabewerte fixieren oder APIs blockieren (Tor, Brave)

  - können Seite limitieren oder komplett brechen

- heuristisch: vordefinierte Patterns erkennen und Blockieren (Privacy Badger, Disconnect)

  - Heuristik muss spezifisch genug sein, um wenige Seiten falsch zu blocken

  - sehr spezifische Heuristik können aber FP-Scripte übersehen

  - Heuristik muss ständig aktuell gehalten werden

  - Schutz gegen passives Fingerprinting durch Blockieren von Anfragen

<!--
- normalization: alle Nutzer sollen selben Fingerprint haben

- Aktives Fingerprinting: im Client Browser

- Passives Fingerprinting: Informationen aus Web Requests
-->

---

# <ph-fingerprint/> Browser-Fingerprinting

## Machine Learning Lösungen zur Erkennung von Fingerabdrücken

- Bisher wurden Machine Learning Lösungen primär für stateful Tracking entwickelt, ohne Scharf zwischen den Typen zu trennen

  - stateless Tracking detection nicht zufriedenstellend

- [Rizzo](https://webthesis.biblio.polito.it/8227/1/tesi.pdf) versuchte 2018 über Machine Learning und statischer Codeanalyse Fingerprinting zu erkennen

- Zum lernen können Features durch statische Codeanalyse oder durch dynamische Analyse extrahiert werden

  - statische Codeanalyse unzureichend bei Codeobfuscation und Enumeration

  - dynamische Analyse unzureichend bei Scripte die über Nutzerinput oder Browser-Events getriggert werden

<!--
- stateless und stateful tracking sehr unterschiedlich, deshalb Lösung die beides zusammenpackt unzureichend

- dynamische Analyse: lässt Script laufen und beobachtet verhalten (API Calls, Call Stack, etc)
-->

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Vorarbeit: Heuristik

Beispiel: CanvasRenderingContext2D Font Fingerprinting

```js
// canvas-font-fingerprinting.js
fonts = ["monospace", ..., "sans-serif"]; // Einen Haufen valider "Font" Werte
canvasElement = document.createElement("canvas");
canvasElement.width = "100";
canvasElement.height = "100";
canvasContext = canvasElement.getContext("2d");
fpDict = {};
for (i = 0; i < fonts.length; i++) {
  canvasContext.font = "16px " + fonts[i];
  fpDict[fonts[i]] = canvasContext.measureText("example").width;
}
```

<!--
- Ein Beispiel für Vielzahl von Fingerprinting Möglichkeiten
- aber anschaulich und wird auch im Paper später benutzt
-->

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Vorarbeit: Heuristik

- [Englehardt und Narayanan](https://dl.acm.org/doi/pdf/10.1145/2976749.2978313) entwickelten Heuristik zur Erkennung von FP-Scripte

- Kriterien für Erkennung von Canvas Font Fingerprinting:

  - Script setzt `CanvasRenderingContext2D.font` mindestens 50 mal auf gültige Werte
  - Script ruft `CanvasRenderingContext2D.measureText(...)` mindestens 50 mal mit dem selben String auf

- Nach manueller Untersuchung der Erkannten Scripte wurden bei 1 Millionen Seiten kein False Positive gefunden.

<!-- prettier-ignore-start -->
<!--  -->
---
layout: two-cols
---
<!-- prettier-ignore-end -->

# <ph-magnifying-glass/> FP-INSPECTOR

## Vorarbeit: OpenWPM

- [Englehardt und Narayanan](https://dl.acm.org/doi/pdf/10.1145/2976749.2978313) entwickelten Framework zur automatisierten Messung von Web Privacy

- Geeignet zur Messung von stateful und stateless Tracking

- Entwickelt in Python

- 3 Hauptmodule

  - Browser Managers
  - Task Manager
  - Data Aggregator

- Wurde bereits in vielen Studien genutzt

::right::

![OpenWPM Aufbau: "Figure 1: High-level overview of OpenWPM The task manager monitors browser managers, which convert high-level commands into automated browser actions. The data aggregator receives and pre-processes data from instrumentation."](/OpenWPM.png)
[Online Tracking: A 1-million-site Measurement and Analysis](https://dl.acm.org/doi/pdf/10.1145/2976749.2978313)

<!--
- Browser Managers als Abstraktionsebene für einzelne Browserinstanzen
  - kommunizieren mittels Selenium mit echtem Firefox, wichtig damit Fingerprinting wie gewollt funktioniert
- Task Manager, user-facing um Kommandos an Browser zu senden
   - Nutzer interagieren mittels Domänen spezifische Sprache um z.B. zu Crawlen oder Browser Instanzen zu steuern
- Data Aggregator als Abstraktionsebene für Browserinstrumentation bzw. zur Datenextraktion
  - Disk Access für Daten zu Cookies
  - HTTP Data
  - JavaScript Access: custom getter und setter um zugriff auf APIs aufzunehmen
  - alles wird in SQLite Datenbank geschrieben
-->

---

# <ph-magnifying-glass/> FP-INSPECTOR

- Machine Learning Ansatz
- statische + dynamische JavaScript Analyse
- Maßnahmen gegen Browser-Fingerprinting

## Design

- Erkennungskomponente

  - extrahieren von syntaktischen und semantischen Features

  - trainieren von Klassifizierer um FP-Scripte zu erkennen

- Mitigationskomponente

  - Reihe von Einschränkungen für erkannte Scripte ("layered approach")

  - Wirkt gegen passives und/oder aktives Fingerprinting

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Design: Architekturdiagramm

![FP-Inspector Architektur: "Fig. 1: FP-INSPECTOR: (1) We crawl the web with an extended version of OpenWPM that extracts JavaScript source files and their execution traces. (2) We extract Abstract Syntax Trees (ASTs) and execution traces for all scripts. (3) We use those representations to extract features and train a machine learning model to detect fingerprinting scripts. (4) We use a layered approach to counter fingerprinting scripts."](/FPInspectorArchitecture.png)

<!--
1. Crawling mit OpenWPM
2. Vorarbeit für Erkennung (Daten extrahieren und glatt ziehen)
3. Erkennungskomponente: Feature Extraktion und training
4. Mitigationskomponente "layered approach"
-->

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Design: Erkennung - Neuheiten

- Automatisches lernen von Heuristiken

  - low-level Heuristiken zum Sammeln von syntaktischen und semantischen Eigenschaften

  - Trainieren mit "ground truth" aus vorheriger Forschung

  - Klassifizierer kann neue FP-Scripte und -Methoden erkennen

- Kombination aus statischer und dynamischer Analyse

  - Statische Analyse: Erkennung von Scripte die beim einfachen Crawlen nicht anspringen

  - Dynamische Analyse: Erkennung von Scripte mit Obfuscation, Minimierung, und Enumeration

  - Trainieren zwei separater Modelle, Kombination der Ergebnisse

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Design: Erkennung - Script Monitoring

- Crawlen mit OpenWPM: Sammeln von rohen Daten und execution traces für statische und dynamische Analyse

- Erweitern von OpenWPM

  - Speichern von HTML Dokumenten (zum sammeln von inline JavaScript)

  - Erweiterung der dynamisch aufgenommenen JavaScript API aufrufe

<!--

- OpenWPM sammelt bereits HTTP responses von extern geladenen Scripten -> Erweiterung für inline scripte
- OpenWPM zeichnet nur subset der APIs auf; alle APIs aufzeichnen wäre nicht performant; Erweiterung um populäre fingerprinting APIs aus offenen Scripten (fingerprintjs2)
-->

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Design: Erkennung - Statische Analyse

- Abstract Syntax Tree Repräsentation

- Script Unpacking

- statische Feature Extraktion

- Überwachtes Lernen

<div v-click-hide class="hover-right" style="height: 60%">
  <img
    src="/script-unpacking.png"
    style="height: 80%"
    alt="Script Unpacking: 'Script 1: A canvas font fingerprinting script packed with eval. Script 2: An unpacked version of the script in Script 1.'"
  />
  <img
    src="/script-ast.png"
    alt="Script ASTs: 'Fig. 2: A truncated AST representation of Scripts 1 and 2. The edges represent the syntactic relationship between nodes. Dotted lines indicate an indirect connection through truncated nodes.'"
  />
</div>

<div v-click class="hover-right" style="height: 50%">
  <img src="/static-features.png" alt="Statische Features: 'TABLE VII: A sample of features extracted from AST in Figure 2b.'"/>
</div>

<!--
- AST um Coding Style und Syntax, und damit auch Whitespace und Kommentare, zu ignorieren
  - AST als Baum von Syntax primitiven und deren Beziehungen
  - Knoten für Keywords (for, eval), Identifiers (Funktionen und Variablennamen) und Literals (Fixwerte wie Strings)

- Unpacking: Scripte die mit eval gewrapped sind müssen entpackt werden um einen Nützlichen AST zu generieren
  - Wird mithilfe eines instrumentieren Browsers gemacht:
    - Scripte werden leeres Dokument eingebettet und dann die interpretation der JavaScript engine extrahiert
  - selbes verfahren fürs extrahieren von inline HTML Scripte

- feature extraction: einteilen des AST in parent und child nodes
  - parents als context (for loops, try statements, if conditions)
  - child als Funktion innerhalb des Context (createElement, toDataUrl, measureText)
  - parent:child paare mit Binärem Werte als Features (paar vorhanden oder nicht vorhanden)
  - z.B. `ForStatement:var` : script hat loop oder `MemberExpression:measureText` script greift auf measureText Methode zu

- unüberwachte und Überwachte feature selektions Methoden um over-fitting zu vermeiden (limitieren der features)

- top 1k features zum überwachten lernen genutzt
-->

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Design: Erkennung - Dynamische Analyse

- dynamische Feature Extraktion

  - Verwendung von JS APIs
  - abgeleitete Werte von JS APIs

- Überwachtes Lernen

<div class="hover-right" style="height: 50%">
  <img src="/dynamic-features.png" alt="Dynamische Features: 'TABLE VIII: A sample of the dynamic features extracted from the execution trace of Script 3a.'"/>
</div>

<!--
- runtime execution traces mit OpenWPM aufgenommen: jetzt daraus features
- Binäre werte für die Verwendung von JS APIs (?)
- abgeleitete Werte um Klassifizierer zu generalisieren
  - bsp: statt spezifischen String Literal die Länge des Strings mit ins feature
  - oder: Fläche eines Canvas Elements messen statt Höhe und Breite mit ins feature

- unüberwachte und Überwachte feature selektions Methoden um over-fitting zu vermeiden (limitieren der features)
  - filtern nach 0.01 Varianztheshold
  - Sortieren nach information gain
  - dann top 1k features

- top 1k features zum überwachten lernen genutzt
-->

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Design: Erkennung - Klassifizieren

- Entscheidungsbaum als Klassifizierer
  - An jedem Knoten wird ein Feature zum effektiven aufteilen gewählt
  - Getrennte Bäume für statische und dynamische Analyse

<!--
- wird zum aufbauen mit gelabelten Daten und features beladen
  - im ersten Knoten hängen alle Daten
  - Feature, der Datenset möglichst effektiv teilt
  - heißt am ende teilen in nicht fingerprinting und fingerprinting kind knoten

- getrennte Bäume: -> Ergebnis wird verORt / Union
-->

<!-- prettier-ignore-start -->
<!--  -->
---
layout: two-cols
---
<!-- prettier-ignore-end -->

# <ph-magnifying-glass/> FP-INSPECTOR

## Design: Mitigation

- Gegenmaßnahmen:

  - Content Blocking
  - API Restrictions

- Website Breakage wichtig!

::right::

<v-click>

- FP-Inspector Mitigation-Modi:
  1. pauschale API Restriction
  2. gezielte API Restriction
  3. Request Blocking
  4. Hybrid

</v-click>

<!--
- Gegenmaßnahmen grob Einteilbar in:
  - Content Blocking: Requests von Domains oder URLs die Fingerprinting Scripte liefern blockieren (Fingerprinting Domains)
  - API Restriction: API Zugang limitieren oder verändern

- Wichtig zu beachten ist Website Breakage (wie oft Webseiten brechen)
  - API Restriction funktioniert gut gegen Fingerprinting aber sobald APIs für Funktionalität genutzt werden wird's haarig
  - Nutzer haben kein Interesse an Privatsphäre wenn es Funktionalität kostet!

1. Fingerprinting APIs werden für alle Scripte komplett Eingeschränkt
2. Fingerprinting APIs werden Eingeschränkt für Scripte von Fingerprinting Domains
3. Fingerprinting Domains werden komplett Blockiert
4. externe Fingerprinting Domains blockieren + API Restrictions für First Party und Inline Scripte von detektierten Domains
-->

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Evaluation: Genauigkeit der Erkennung

- Zur Evaluation werden gelabelte Beispiele von Non-FP-Scripte und FP-Scripte (ground truth) benötigt

- Labeln von (Non-)FP-Scripte mit angepassten Heuristiken aus [Englehardt und Narayanan](https://dl.acm.org/doi/pdf/10.1145/2976749.2978313)

- Data Collection mit OpenWPM

  - Alexa Top-10k + 10k (gesampled aus Top 10k-100k) -> 20k Seiten gecrawed

  - 17.629 Seiten mit 153.354 verschiedenen Scripte gefunden

- Reminder: Heuristiken nicht perfekt

<!--

- ML braucht Daten, gute und saubere Daten!

  - frühere Researcher haben liste von Scripten veröffentlicht, aber nicht up to date

  - anti tracking lists haben oft keine Fingerprinting Domains oder labeln diese nicht explizit

    - Tracking Prevention List von Disconnect hat gesonderte fingerprinting liste, aber nur domains keine spezifischen URLs zu Scripte und primär third-party Anbieter, aber first-party FP soll auch beachtet werden

  - Entscheidung selber zu crawlen und zu labeln mit Heuristiken


- Heuristiken wurden angepasst um weniger false positives aufzuweisen, wichtig für Ground Truth
  - `Canvas` und `Canvas Font`, `WebRTC` und `Audio Context` API FP Heuristiken
  - wenn Heuristiken anschlagen ist das Script ein Fingerprinting script

- Data Collection: Mischung aus Alexa top-10k und 10k random Seiten aus 10k-100k
  - also sehr populäre Seiten und einige Seiten weiter unten in der liste
  - 120s Timeout zum laden der Seiten


- Heuristiken: müssen spezifisch genug sein, können aber wenn zu spezifisch FP-Scripte übersehen; außerdem müssen die aktuell gehalten werden

-->

<!-- prettier-ignore-start -->
<!--  -->
---
layout: two-cols
---
<!-- prettier-ignore-end -->

# <ph-magnifying-glass/> FP-INSPECTOR

## Evaluation: Genauigkeit der Erkennung

- Um "heuristische" Daten zu verbessern: _Enhancing Ground Truth_

  - iteratives Re-Training und manuellem korrigieren der Datengrundlage
  - bei Mismatch: automatisierten Report
  - Manuelle Auswertung nach "heuristic-like behaviors"

::right::

!["TABLE I: Enhancing ground truth with multiple iterations of retaining. Itr. represents the iteration number of training with static (S) and dynamic (D) models. New Detections (FP) represent the additional fingerprinting scripts detected by the classifier and New Detections (NON-FP) represent the new non-fingerprinting scripts detected by the classifier as compared to heuristics. Whereas Correct Detections (FP) represent the manually verified correct determination of the classifier for fingerprinting scripts and Correct Detections (NON-FP) represent the manually verified correct determination of the classifier for non-fingerprinting scripts."](/enhance.png)

<!--

- Enhancing Ground Truth:
  - Automatisierter Report bei "falscher" Zuordnung
    - JS API und Property Calls mit Argumenten und Rückgabewerten
    - Snippets vom Script rundum calls zu den interessanten APIS (Canvas, WebRTC, etc.)
    - Ähnlichkeitsscore zu Fingerprintjs2
    - komplettes formatiertes Script
  - Manuelle Auswertung von mehreren anhand der Kriterien:
    1. Funktionalität und Struktur ist ähnlich zu bekannten FP-Scripte
    2. Script verwendet weiteren FP-Code
    3. Potentieller Fingerprinting Teil interagiert nicht mit funktionellem Teil


  - Auswertung war "straightforward", viele Scripte waren ähnlich zu bekannten FP Scripte und konnten in Grunddaten als FP gelabled werden´
    - Falls Script FP funktionalität nutzt gilt gesammte Script als Fingerprinting, sonst NON-FP
    - wie erwartet sind FP-Scripte durch heuristiken durchgerutscht: hauptgründe
      - Script wurde nicht ausgeführt, weil event nicht ausgeführt etc
      - Fingerprinting funktioniert an heursitiken vorbei da zu eng definiert
    - vereinzelt stellen wo Heuristik besser funktioniert als Clasifier, aber selten

_Tabelle_

- mehrheitlich korrekt klassifiziert
- kann auch neue Scripte erkennen wenn ground truth verbessert

-->

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Evaluation: Genauigkeit der Erkennung

![Genauigkeitstabelle: "TABLE II: FP-INSPECTOR’s classification results in terms of recall, precision, and accuracy in detecting fingerprinting scripts. “Heuristics (Scripts/Websites)” represents the number of scripts and websites detected by heuristics and “Classifiers (Scripts/Websites)” represents the number of scripts and websites detected by the classifiers. FPR represents false positive rate and FNR represent false negative rate."](/accuracy.png)

- Kombination aus statischer und dynamischer Analyse Kombination durch "OR" Verknüpfung der zwei Classifier
  - 94,46% der Scripte die nur durch statische Analyse erkannt wurden waren ruhend
  - 92.30% der Scripte die nur durch dynamische Analyse erkannt wurden waren obfuscated/stark minified
- Classifier erkennt 26% mehr Scripte als pure Heuristiken

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Evaluation: Breakage

- 4 Modi zur Mitigation (pauschale und gezielte API Restriction, Request Blocking und Hybrid) per Browsererweiterung konfigurierbar

- Manuelles Testen von 50 zufälligen FP-Seiten und 11 Non-FP-Seiten die unter derzeitigen Anti-FP Methoden von Firefox brechen

  - Ausschalten der derzeitigen Anti-FP Methoden von Firefox
  - Jeweils Testen der Seite im vanilla Firefox, dann mit Erweiterung in jedem Modi
  - Einteilen in jeweils "Major", "Minor" oder "None" Breakage

<img
  v-click
  src="/breakage.png"
  alt='Breakage: "TABLE III: Breakdown of breakage caused by different countermeasures. The results present the average assessment of two reviewers."'
  style="height:60%;margin:auto"
/>

<!--

Testen der Seiten:
- Jeweils ein paar Minuten benutzen, Rum Scrollen, offensichtliche Funktionalität auschecken

- Major: Hauptfunktionalität gestört (Login, Register, Suche, Menü, Navigation)
- Minor Nebenfunktionalität gestört (Kommentare, Bewertung, Widget, Social Media Embeds)
- None: Haupt und Nebenfunktionalität nicht gestört, fehlende Werbung keine breakage

- breakage wurde von zwei reviewers untersucht um bias zu verhindern

Ergebnisse:
- pauschale API Restriction bricht am häufigsten, und häufiger als Request blocking, weil auch nicht FP-Scripte betroffen sind
- gezielte API Restriction bricht am wenigsten
- Hybrid bricht weniger als Request Blocking, aber öfter als API restriction
  - hybrid bietet aber auch Schutz gegen passives FP
  - nur kein Schutz gegen first-party passive FP, aber fast unmöglich zu Blockieren ohne Breakage

- Häufigster fall für breakage: Seiten-Funktionalitäts-Code wartet explizit auf Fingerprinting Code, forciert diesen also

Insgesamt: Reduktion von Breakage um Faktor 2 bei Seiten die anfällig für Breakage sind

-->

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Limitationen

- Umgehen der Entdeckung durch Scriptstreuung
  - Beziehung zwischen Scripte nicht betrachtet
- Umgehung von Gegenmaßnahmen durch Scriptverschmelzung
  - Granularität von gezielter API Restriction auf Script-Ebene

<!--

- Scriptstreuung: FP-Scripte in mehrere Scripte aufteilen, z.B. teil Funktionen in globalen Scope schreiben
  - Beziehung zwischen Scripte könnte man in Zukunft hinzufügen
  - Streuung hat jedoch extra Wartungskosten

- Scriptverschmelzung: Zusammenfügen von Funktionalen Scripten mit FP Scripten
  - Zusammenfügen würde Website Brechen und Nutzer zwingen gegenmaßnahmen auszuschalten
  - mehr Granularität denkbar
  - Verschmelzung hat auch extra Wartungskosten
  - Verschmelzung funktioniert auch gegen andere Werbung und Tracking Blocker

- beides könnte in Zsukunft verbessert werden

-->

---

# <ph-user-focus/> Fingerprinting der Top-100k Seiten

## Mehr als ein Viertel der Top-Websites nehmen Fingerabdrücke von Nutzern

<img alt="'TABLE IV: Distribution of Alexa top-100K websites that deploy fingerprinting. Results are sliced by site rank.'" src="/wild-fprate.png" style="height:60%;margin:auto;"/>

<!--

- Mittels Erkennungskomponenten von FP-Inspect wurden top-100k Alexa Seiten gecrawled
  - Analog zu Englehardt und Narayanan in 2016
- FP häufiger auf populären Seiten
- Mehr FP als man in vorherigen Studien beobachtet hat
- Mehr als ein Viertel der Top-Websites nehmen Fingerabdrücke von Nutzern
- auch weniger populäre Seiten zeigen mehr FP auf
- overall 10,18% der top-100k verwendet FP Scripte
- Möglichkeiten für funde:
  - Erkennung ist weitreichender als bei Englehardt udn Narayanan und/oder
  - Fingerprinting hat seit 2016 deutlich zugenommen

-->

---

# <ph-user-focus/> Fingerprinting der Top-100k Seiten

## Fingerprinting ist auf Nachrichtenseiten am häufigsten zu finden

<img alt="'Fig. 4: The deployment of fingerprinting scripts across different categories of websites.'" src="/wild-news.png" style="height:70%;margin:auto;"/>

<!--

- FP ungleichmäßig über verschiedene Kategorien von Seiten verteilt
  - fast 14% Nachrichten
  - nur 1% Bankseiten

- Muster gilt in etwa für generelles Tracking, nicht nur FP
  - Tracking oft auf Seiten die sich mit Werbung finanzieren
  - auch denkbar das FP zur Durchsetzung von Paywalls genutzt wird, Cookies da sehr einfach zu umgehen
-->

---

# <ph-user-focus/> Fingerprinting der Top-100k Seiten

## Fingerprinting wird zur Bekämpfung von Anzeigenbetrug eingesetzt, aber auch für potenzielles Cross-Site-Tracking

<img
  src="/wild-vendors.png"
  alt="'TABLE V: The presence of the top vendors classified as fingerprinting on Alexa top-100K websites. Tracker column shows whether the vendor is a cross-site tracker according to Disconnect’s tracking protection list. Y represents yes and N represents no.'"
  style="height:70%;margin:auto"
/>

<!--

- FP-Scripte oft von third party Anbieter
  - doubleverify, adsafeprotected und adscore sind Anti-Ad-Fraud services die auf die Überprüfung der Echtheit von Ad Impressions spezialisiert (prüfen ob Werbung gesehen wurde)
  - Bot-Erkennungs-Services nutzen auch FP
  - alicdn und yimg sind CDNs für jeweils Alibaba udn Oath/Yahoo!

- mehrere FP-Anbieter geben auch zu Daten zu teilen
  - Abgleich der FP-Anbieter liste mit Tracking Protection list von Disconnect:
    - Disconnect hat manuelles review für diese Einteilung, als Tracker gilt jemand der Personenbezogene Daten Teilt
    - 3,78% der FP-Anbieter sind Tracking Dienste laut Disconnect

- Cookie syncing: das nutzen von FP um Cookies (und am Ende Nutzerdaten) zu verknüpfen
  - Abgleich der FP-Anbieter liste mit cookie synching liste vorheriger Forschung: 17,28% der FP-Anbieter beteiligen sich an Cookie Synching
  - FP-Anbieter synchronisieren cookies mit großen Werbeanbietern
  - oft auch mit mehreren Anbietern
  - Fingerprinting und die Rolle beim Cookie Syncing als mögliche weitere Arbeit

-->

---

# <ph-hand-eye/> Analyse der für Fingerprinting missbrauchten APIs

##

<img
  src="/api-anal.png"
  alt="'TABLE VI: A sample of frequently used JavaScript API keywords in fingerprinting scripts and their presence on 20K websites crawl. Scripts (count) represents the number of distinct fingerprinting scripts in which the keyword is used and Websites (count) represents the number of websites on which those scripts are embedded.'"
  style="height:70%;margin:auto"
/>

<!--
- ernste Bedenken wie neue und vorhandene JS APIs für FS in unerwarteten wegen missbraucht werden können

- Analyse der Verteilung von JS APIs in FP- und Non-FP-Scripte
  - Extraktion von JS API Keywords aus gesammeltem Source Code und sortieren nach Verhältnis des Vorkommens in FP- und Non-FP-Scripte
  - höheres Verhältnis bedeutet API wird häufiger in FP-Scripte benutzt
  - unendlich bedeutet wird exklusiv in FP-Scripte benutzt

Tabelle

- zu viel zur manuellen Analyse: einteilen in Cluster über einen gewichteten Graphen der gleichzeitiges Auftreten widerspiegelt
  - da FP-Scripte oft mehrere FP Techniken gleichzeitig verwenden hofft man auf deutlichen FP Cluster
  - hoch konzentrierte Cluster neigen dazu zu FP-Scripte zu gehören
  - Nutzung weiter JS APIs für Fingerprinting entdeckt
-->

---

# <ph-hand-eye/> Analyse der für Fingerprinting missbrauchten APIs

## Fingerprinting der Funktionalität

1. Berechtigung Fingerprinting
2. Peripherie Fingerprinting
3. API Fingerprinting

## Algorithmisches Fingerprinting

1. Timing Fingerprinting
2. Animation Fingerprinting
3. Audio Fingerprinting
4. Sensor Fingerprinting

<!--

- Neu gefundene FP-APIs eingeteilt in Kategorien

Fingerprinting der Funktionalität: Kategorie der FP Techniken bei denen verschiedene Browser Funktionalität und APIs geprüft werden

1. Permission JS API kann genutzt werden um status einzelner Berechtigungen nachzufragen
  - FP Scripte benutzen Status der Notification, Geolocation und Kamera Berechtigungen
  - Unterschiedliche Browser und Nutzereinstellungen bieten Fläche für Fingerprinting

2. Peripherie Fingerprinting: Moderne Browser können nach Geräten suchen z.B. Keyboard Layout, Gamepads oder VR Geräten
  - Unterschiedliche Peripherie sorgt für FP Entropy

3. API Fingerprinting: Ausnutzen der Unterschiedlichen Features und APIs unter Browsern und Browsererweiterungen
  - API Implementierungen können sich je Browser unterscheiden
    - bzw. neue Features erst nur auf einer Platform z.B. AudioWorklet zuerst in Chrome
  - Browsererweiterungen können JavaScript Methoden überschreiben, FP Scripte können diese Überprüfen
  - Beides bietet FP Fläche

Algorithmisches Fingerprinting: Kategorie der FP Techniken die APIs mit bestimmen Inputs benutzen um darüber ein FP zu generieren, also nicht nur prüfen der APIs


1. Timing Fingerprinting: `Performance` API bietet hochauflösende Timestamps, wird genutzt in Kombination mit verschiedenen Browser Events wie `domainLookupStart`, `domainLookupEnd` oder `domInteractive`
2. Animation Fingerprinting: Ähnlich wie Timing. FP Scripte benutzen `requestAnimationFrame` um Framerate vom Browser zu berechnen, die meist mit Wiederholrate vom Bildschirm zusammenhängt
3. Englehardt udn Narayanan haben bereits Audio FP beobachtet: Nutzen hat sich erweitert durch nutzen weiterer Properties der AudioContext API z.B. `canPlayType`
4. Sensor Fingerprinting: Ausnutzen von Sensoren an z.B. Mobilgeräten wie `devicemotion`, `deviceorientation` und neu entdeckt `userproximity`

- Unterschiede bieten FP Entropy


-->

---

# <ph-lightbulb/> Fazit

- FP-Inspector: Machine Learning-basierter syntaktisch und semantischer Ansatz

  - Erkennt 26% mehr FP-Scripte als pure Heuristiken
  - Bricht Webseiten bis zu 2x weniger Breakage

- Analyse der Top Seiten ergibt:

  - Fingerprinting so weit verbreitet wie nie zuvor: Von Top-100k Seiten 10.18% mit FP Scripts
  - 2,349 FP Domains wurden an Tracking Listen gesendet

- Neue FP-APIs gefunden: werden an Browser Anbieter und Standard Autoritäten gemeldet

- Veröffentlicht für follow-up Research:
  - Patches für OpenWPM
  - Fingerprinting Countermeasure Prototype Browsererweiterung
  - Liste neu entdeckter FP-Anbieter
  - Bug Reports für Tracking Listen, Browser Anbieter und
  - Alles zu finden unter [(github.com/uiowa-irl/FP-Inspector)](https://github.com/uiowa-irl/FP-Inspector)

---

# <ph-newspaper/> Weitere Paper

- ["Did I delete my cookies? Cookies respawning with browser fingerprinting" Fouad et al.](https://arxiv.org/abs/2105.04381)
  - "Our results show that 1,150 out of the top 30, 000 Alexa websites deploy this tracking mechanism."
- ["FP-Radar: Longitudinal Measurement and Early Detection of Browser Fingerprinting" Bahrami et al.](https://arxiv.org/abs/2112.01662)
  - "In this paper, we propose FP-Radar, a machine learning approach that leverages longitudinal measurements of web API usage on top-100K websites over the last decade, for early detection of new and evolving browser fingerprinting techniques."
- ["EssentialFP: Exposing the Essence of Browser Fingerprinting" Sjösten et al.](https://ieeexplore.ieee.org/document/9583693)
  - "We argue that the pattern of (i) gathering information from a wide browser API surface (multiple browser-specific sources) and (ii) communicating the information to the network (network sink) captures the essence of fingerprinting. This pattern enables us to clearly distinguish fingerprinting from similar types of scripts like analytics and polyfills. To implement EssentialFP we leverage, extend, and deploy JSFlow, a state-of-the-art information flow tracker for JavaScript, in a browser."

<!-- prettier-ignore-start -->
<!--  -->
---
layout: cover
background: https://images.pexels.com/photos/8382599/pexels-photo-8382599.jpeg
---
<!-- prettier-ignore-end -->

# Vielen Dank für Ihre Aufmerksamkeit

## Die Fingerabdrücke der Fingerabdrucknehmer: Browser-Fingerprinting-Verhalten lernen und erkennen

Vorgetragen von Antonio Sarcevic - Hackerpraktikum - WS 2021/22 - FH Münster


<a href="https://web.cs.ucdavis.edu/~zubair/files/fpinspector-sp2021.pdf" class="footer">U. Iqbal, S. Englehardt, and Z. Shafiq, “Fingerprinting the fingerprinters: Learningto detect browser fingerprinting behaviors” , 2020.</a>
