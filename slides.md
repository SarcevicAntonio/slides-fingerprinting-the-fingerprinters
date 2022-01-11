---
highlighter: shiki
layout: cover
lineNumbers: true
background: https://images.pexels.com/photos/8382599/pexels-photo-8382599.jpeg
title: "Die Fingerabdrücke der Fingerabdrucknehmer: Browser-Fingerprinting-Verhalten lernen und erkennen"
---

# Die Fingerabdrücke der Fingerabdrucknehmer

## Browser-Fingerprinting-Verhalten lernen und erkennen

Vorgetragen von Antonio Sarcevic - Hackerpraktikum - WS 2021/22

<img class="mt-4" src="/fh.svg" alt="FH Münster Logo">

<a href="https://web.cs.ucdavis.edu/~zubair/files/fpinspector-sp2021.pdf" class="footer">src: U. Iqbal, S. Englehardt, and Z. Shafiq, “Fingerprinting the fingerprinters: Learning to detect browser fingerprinting behaviors” , 2020.</a>

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

- <ph-user-focus/> Fingerprinting der Top-100k

- <ph-hand-eye/> Analyse der für Fingerprinting missbrauchten APIs

- <ph-lightbulb/> Fazit

---

# <ph-fingerprint/> Browser-Fingerprinting

## Online Tracking durch Fingerprinting (FP)

- Form von stateless tracking

- **Missbrauchen** von JavaScript APIs und HTTP Header um Geräte zu identifizieren

  - z.B. `Canvas` und `User-Agent`

- Aufdringlicher als Cookies da:

  1. undurchsichtig

  2. fehlende Kontrolle

- Fingerprinting wird eingesetzt für:

  - Bot-Detection (Google reCAPTCHA)

  - Cookies regenerieren oder synchronisieren

  - Cross-Site Tracking

<!--
- stateless: ohne speichern eines Tokens im Client
  - gg. stateful tracking: Cookie oder Local Storage

- JS APIs im Browser: aktives FP vs HTTP Header aufm Server: passives FP

- Laut Browser Entwickler und W3C: Fingerprinting "missbräuchliche Praxis"

- Aufdringlicher als Cookies
  1. Cookies im Browser angezeigt
  2. Cookies können bearbeitet bzw. gelöscht werden

- Cross-Site Tracking interessant weil Safari und Firefox vermehrt gg. third-party cookies vorgehen
-->

---

# <ph-fingerprint/> Browser-Fingerprinting

## Beispiel: Canvas Font Fingerprinting

```js{all|1|3-6|8-12|all}
fonts = ["monospace", /* ... */, "sans-serif"];

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

<v-click>

```js
❯ fpDict
« {"monospace":61.578125,"Comic Sans MS":60.53125,"Arial":60.4765625,"sans-serif":60.4765625}
```

</v-click>

<!--
- Ein Beispiel für Vielzahl von Fingerprinting Möglichkeiten
- aber anschaulich und wird auch im Paper später benutzt
-->

---

# <ph-fingerprint/> Browser-Fingerprinting

## Bietet Fingerprinting eindeutige und dauerhafte Identifikatoren?

- Fingerprint als "statistischer" Identifikator

  - Identifizierbarkeit abhängig von Anzahl Geräten mit gleicher Konfiguration

  - bzw. "uniqueness" des Fingerprints

- [Laperdrix et al.](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=7546540) ([AmIUnique](https://amiunique.org/)) und [Eckersley](https://link.springer.com/chapter/10.1007/978-3-642-14527-8_1) ([Panopticlick](http://panopticlick.eff.org/)): 83% - 90% der Geräte eindeutigen Fingerprint (biased)

- [Broix et al.](https://hal.inria.fr/hal-01718234v2/document) auf Seite eines französischen Verlags: 33.6% Geräte mit eindeutigem Fingerprint (mehr Daten für mehr uniqueness benötigt)

- [Eckersley](https://link.springer.com/chapter/10.1007/978-3-642-14527-8_1) ([Panopticlick](http://panopticlick.eff.org/)): 37% wiederkehrende Besucher mehr als einen Fingerprint

  - 65% der Geräte über einfache heuristische Verknüpfung re-identifiziert

  - [Vastel et al.](https://hal.inria.fr/hal-01652021/document#:~:text=FP%2DSTALKER%20is%20an%20approach,machine%20learning%20to%20boost%20accuracy.) ([AmIUnique](https://amiunique.org/)) verbesserten Heuristik: mögliche Verfolgung von Besuchern für Ø 74 Tage

<!--

- "statistischer": kein deterministischer Identifikator

- AmIUnique und Panopticlick: primär von technisch Interessierten Personen genutzt, daher sind die Zahlen biased

- Broix: nur limitiertes Fingerprinting (IP, Content Language, Timezone header für mehr uniqueness)

- Eckersley: Stabile / verknüpfbare Fingerprints für Tracking benötigt:

-->

---

# <ph-fingerprint/> Browser-Fingerprinting

## Verbreitung

- 2013 Paper ["Cookieless Monster: Exploring the Ecosystem of Web-based Device Fingerprinting"](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=6547132)

  - 3 fingerprinting Anbieter

  - 40 FP-Seiten aus Alexa Top-10k **(0.4%)**

- 2013 Paper ["FPDetective: Dusting the Web for Fingerprinters"](https://www.esat.kuleuven.be/cosic/publications/article-2334.pdf)

  - 404 FP-Seiten aus Alexa Top-1 Millionen **(0.04%)**

- Weitere Studien [[37]](https://dl.acm.org/doi/10.1145/2660267.2660347), [[47]](https://www.esat.kuleuven.be/cosic/publications/article-3078.pdf), [[54]](https://arxiv.org/pdf/1812.01514.pdf), [[78]](https://petsymposium.org/2017/papers/hotpets/batterystatus-not-included.pdf) finden vermehrt FP-Seiten über die Jahre

- 2019 schreibt [Washington Post](https://www.washingtonpost.com/technology/2019/10/31/think-youre-anonymous-online-third-popular-websites-are-fingerprinting-you/): "At least a third of the [top] 500 sites [...] use hidden [fingerprinting] code."

<!--
- Weitere Studien: Studien verwenden alle verschiedene Strategien um auf fingerprinting zu prüfen, dennoch lässt sich ein Trend erkennen
- Trotz der verstärkten Kontrolle durch Browser-Anbieter und die Öffentlichkeit ist das Fingerprinting nach wie vor weit verbreitet.
-->

---

# <ph-magnifying-glass/> FP-INSPECTOR

- Machine Learning Ansatz zur Erkennung von FP
- statische + dynamische JavaScript Analyse für Feature-Extraktion
- Maßnahmen gegen Browser-Fingerprinting zur Mitigation

<!--
- statische Analyse:
  - Script Source Code Analysieren
  - Nachteil: funktioniert schlecht bei Obfuscation / starker minifizierung

- dynamische Analyse:
  - Script Execution Traces also Laufzeitverhalten Analysieren
  - Nachteil: Funktioniert nur bei Scripte die ausgeführt werden
    - Scripte triggern evtl. nur bei spezifischem Nutzerinput oder Browser Event

- Kombination beider Ansätze für "Best of Both Words"
-->

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Design: Architekturdiagramm

![FP-Inspector Architektur: "Fig. 1: FP-INSPECTOR: (1) We crawl the web with an extended version of OpenWPM that extracts JavaScript source files and their execution traces. (2) We extract Abstract Syntax Trees (ASTs) and execution traces for all scripts. (3) We use those representations to extract features and train a machine learning model to detect fingerprinting scripts. (4) We use a layered approach to counter fingerprinting scripts."](/FPInspectorArchitecture.png)

<!--
1. Crawling mit OpenWPM

2. Vorarbeit für Erkennung (Daten extrahieren und glatt ziehen)

3. Erkennungskomponente
    - extrahieren von syntaktischen und semantischen Features
    - trainieren von Classifier um FP-Scripte zu erkennen

4. Mitigationskomponente
    - Reihe von Einschränkungen für erkannte Scripte ("layered approach")
    - Wirkt gegen passives und/oder aktives Fingerprinting
-->

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Design: Erkennung - Script Monitoring

- [Englehardt und Narayanan [54]](https://dl.acm.org/doi/pdf/10.1145/2976749.2978313) entwickelten OpenWPM (Framework zur automatisierten Messung von Web Privacy)

- Crawlen mit OpenWPM: Sammeln von rohen Daten und execution traces für statische und dynamische Analyse

- Erweitern von OpenWPM

  - Speichern von HTML Dokumenten (zum sammeln von inline JavaScript)

  - Erweiterung der dynamisch aufgenommenen JavaScript API aufrufe

<!--

Erweiterungen nötig:

- OpenWPM sammelt bereits HTTP responses von extern geladenen Scripten -> Erweiterung für inline scripte

- OpenWPM zeichnet nur subset der APIs auf; alle APIs aufzeichnen wäre nicht performant; Erweiterung um populäre fingerprinting APIs aus offenen Scripten (fingerprintjs2)
-->

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Design: Erkennung - Statische Analyse

- Abstract Syntax Tree Repräsentation

- Script Unpacking

- statische Feature Extraktion

  - `parent:child` Paare

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
- AST: Coding Style und Syntax ignorieren, damit auch z.B. Whitespace und Kommentare
  - fig 2 b)
  - Knoten für syntax Primitive wie Keywords (for, eval), Identifiers (Funktionen und Variablennamen) und Literals (Fixwerte z.B. Strings)
  - Äste sind Beiziehungen zwischen den Primitiven

- Unpacking: manche Scripte mit eval gewrapped, müssen entpackt werden für AST (script 1 und fig2 a)
  - Scripte in instrumentiertem Browser: Extraktion der interpretation der JavaScript engine
  - auch das verfahren für inline HTML Scripte

- feature extraction: einteilen des AST in parent und child nodes
  - parents als context (z.B. MemberExpression)
  - child als Funktion innerhalb des context (measureText)
  - _click für Bild_
  - parent:child paare als Features (paar vorhanden oder nicht vorhanden)
  - z.B. feature `MemberExpression:measureText` bedeutet: zugriff auf measureText Methode

- unüberwachte und Überwachte feature selektions Methoden um over-fitting zu vermeiden (limitieren der features)

- top 1k features zum überwachten lernen genutzt
-->

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Design: Erkennung - Dynamische Analyse

- dynamische Feature Extraktion

  - Verwendung von JS APIs
  - abgeleitete Werte von JS API calls

- Überwachtes Lernen

<div class="hover-right" style="height: 50%">
  <img src="/dynamic-features.png" alt="Dynamische Features: 'TABLE VIII: A sample of the dynamic features extracted from the execution trace of Script 3a.'"/>
</div>

<!--
- features aus runtime execution traces ableiten für dynamische analyse
- Verwendung von JS APIs: Binär
- abgeleitete Werte von JS API calls um Classifier zu generalisieren
  - bsp: statt spezifischen String Literal die Länge des Strings ins feature

- unüberwachte und Überwachte feature selektions Methoden um over-fitting zu vermeiden (limitieren der features)

- top 1k features zum überwachten lernen genutzt
-->

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Design: Erkennung - Klassifizieren

- Entscheidungsbaum als Classifier
  - Knoten: Feature zum aufteilen auswählen
  - Blätter: FP / Non-FP
- Getrennte Bäume für statische und dynamische Analyse

<div class="hover-right" style="height: 50%">
  <img src="/dtree.png" alt="Entscheidungsbaum mit Knoten und Blättern.'"/>
</div>

<!--
- EBaum: wird zum aufbauen mit gelabelten Daten und features beladen
  - in Wurzel hängen alle Daten
  - in jedem Knoten Feature auswählen, der Datenset möglichst effektiv teilt
  - am ende teilen in nicht fingerprinting und fingerprinting Blätter

- getrennte Bäume: -> Ergebnis wird verORt
-->

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Evaluation: Genauigkeit der Erkennung

- Zur Evaluation werden gelabelte Beispiele von Non-FP-Scripte und FP-Scripte (ground truth) benötigt

- Labeln von (Non-)FP-Scripte mit angepassten Heuristiken aus [Englehardt und Narayanan [54]](https://dl.acm.org/doi/pdf/10.1145/2976749.2978313)

  - Heuristiken nie perfekt

- Data Collection mit OpenWPM

  - Alexa Top-10k + 10k (gesampled aus Top 10k-100k) -> 20k Seiten gecrawed

  - 17.629 Seiten mit 153.354 verschiedenen Scripte gefunden

<!-- prettier-ignore-start -->
<!--
- ML braucht Daten, gute und saubere Daten!

  - liste von Scripten aus früheren Forschungen sind nicht up to date

  - Anti Tracking Listen entweder keine Fingerprinting Domains nicht als FP gelabeld

- Daher Entscheidung selber zu crawlen und Scripte zu labeln mit Heuristiken

  - Vorarbeit von Englehardt und Narayanan: entwickelten Heuristik zur Erkennung von FP-Scripte
     - z.B. Canvas Font FP: 50 Mal Font ändern und 50 Mal Measure Text mit gleichem String

  - Heuristiken wurden angepasst um weniger false positives aufzuweisen, wichtig für Ground Truth

  - Heuristiken nicht perfekt: (schmaler grad)
    - müssen spezifisch genug sein um keine false positives zu generieren
    - aber wenn zu spezifisch können FP-Scripte übersehen werden
    - müssen aktuell gehalten werden

- Data Collection: Mischung aus Alexa top-10k und 10k random Seiten aus 10k-100k

  - also sehr populäre Seiten und einige Seiten weiter unten in der liste
-->
---
layout: two-cols
---
<!-- prettier-ignore-end -->

# <ph-magnifying-glass/> FP-INSPECTOR

## Evaluation: Genauigkeit der Erkennung

- _Enhancing Ground Truth_

  - iteratives Re-Training und manueller Korrektur der Datengrundlage
  - bei Mismatch: automatisierten Report
  - Manuelle Auswertung nach "heuristic-like behaviors"

::right::

!["TABLE I: Enhancing ground truth with multiple iterations of retaining. Itr. represents the iteration number of training with static (S) and dynamic (D) models. New Detections (FP) represent the additional fingerprinting scripts detected by the classifier and New Detections (NON-FP) represent the new non-fingerprinting scripts detected by the classifier as compared to heuristics. Whereas Correct Detections (FP) represent the manually verified correct determination of the classifier for fingerprinting scripts and Correct Detections (NON-FP) represent the manually verified correct determination of the classifier for non-fingerprinting scripts."](/enhance.png)

<!--

- Enhancing Ground Truth um "heuristische" Daten zu verbessern:
  - iteratives Re-Training und manuelle Sichtung und Korrektur der Daten
  - Automatisierter Report bei "falscher" Zuordnung
    - u.a. Snippets vom Script rundum interessanten APIS calls, Ähnlichkeitsscore zu Fingerprintjs2
  - Manuelle Auswertung von anhand der Kriterien:
    1. Funktionalität und Struktur ist ähnlich zu bekannten FP-Scripte
    2. Script verwendet weiteren FP-Code
    3. Potentieller Fingerprinting Teil interagiert nicht mit funktionellem Teil


- Auswertung war "straightforward"
  - viele gefundene Scripte ähnlich zu bekannten FP Scripte: konnten in Daten zu FP Script korrigiert werden
  - wie erwartet: FP-Scripte durch Heuristiken durchgerutscht:
    - Fingerprinting an Heuristiken vorbei funktioniert (schmaler grad der engen definition)
    - Script wurde nicht ausgeführt

_Tabelle_

- mehrheitlich korrekt klassifiziert
- wenn ground truth verbessert: Erkennung weiterer Scripte

-->

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Evaluation: Genauigkeit der Erkennung

![Genauigkeitstabelle: "TABLE II: FP-INSPECTOR’s classification results in terms of recall, precision, and accuracy in detecting fingerprinting scripts. “Heuristics (Scripts/Websites)” represents the number of scripts and websites detected by heuristics and “Classifiers (Scripts/Websites)” represents the number of scripts and websites detected by the classifiers. FPR represents false positive rate and FNR represent false negative rate."](/accuracy.png)

- Kombination statischer und dynamischer Analyse hat sich gelohnt:
  - 94,46% der Scripte die nur durch statische Analyse erkannt wurden waren ruhend
  - 92,30% der Scripte die nur durch dynamische Analyse erkannt wurden waren obfuscated / stark minified
- Classifier erkennt 26% mehr Scripte als pure Heuristiken

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

- FP-Inspector Mitigations-Modi:
  1. pauschale API Restriction
  2. gezielte API Restriction
  3. Request Blocking
  4. Hybrid

</v-click>

<!--
- Gegenmaßnahmen Einteilbar in:
  - Content Blocking:
    - Blocken von Requests von Fingerprinting Domains: Domains oder URLs die Fingerprinting Scripte ausliefern
  - API Restriction: FP API Zugang limitieren oder verändern

- Wichtig zu beachten: wie oft Webseiten brechen (Website Breakage)
  - API Restriction gut gegen Fingerprinting, bricht aber legitime Einsätze der APIs
  - Nutzer haben kein Interesse an Privatsphäre Tools die Funktionalität brechen

_click for mitigations_

FP Inspector bietet 4 Modis zur Mitigation per Browsererweiterung an:

1. pauschale API Restriction: Fingerprinting APIs werden für alle Scripte komplett Eingeschränkt
2. gezielte API Restriction: Fingerprinting APIs werden Eingeschränkt für Scripte von Fingerprinting Domains
3. Request Blocking: Fingerprinting Domains werden komplett Blockiert
4. Hybrid:  externe Fingerprinting Domains blockieren + API Restrictions für First Party und Inline Scripte von detektierten Domains (best of both worlds)
-->

---

# <ph-magnifying-glass/> FP-INSPECTOR

## Evaluation: Breakage

- Manuelles Testen

  - 50 zufälligen FP-Seiten + 11 Non-FP-Seiten (die unter damaligen Anti-FP Methoden von Firefox brechen)

  - Ausschalten der damaligen Anti-FP Methoden von Firefox

  - Testen der Seiten, jeweils im vanilla Firefox und mit jedem Mitigations-Modi

<img
  v-click
  src="/breakage.png"
  alt='Breakage: "TABLE III: Breakdown of breakage caused by different countermeasures. The results present the average assessment of two reviewers."'
  style="height:60%;margin:auto"
/>

<!--
- Jeweils ein paar Minuten benutzen, Rum Scrollen, offensichtliche Funktionalität auschecken

- breakage wurde von zwei reviewers untersucht um bias zu verhindern

Ergebnisse: _click_
- pauschale API Restriction bricht am häufigsten, und häufiger als Request blocking, weil auch nicht FP-Scripte betroffen sind

- gezielte API Restriction bricht am wenigsten, aber nur Schutz gegen aktives FP

- Hybrid bricht weniger als Request Blocking, aber öfter als gezielte API restriction

  - auch Schutz gegen passives FP

  - nur kein Schutz gegen first-party passives FP (Seiten Server selbst kann Header FPen)
    - fast unmöglich zu Blockieren ohne Breakage

- Häufigster fall für breakage: Seiten-Funktionalitäts-Code erfordert explizit Fingerprinting Code, forciert diesen also

Insgesamt: Reduktion von Breakage um Faktor 2 bei anfälligen Seiten
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

-->

---

# <ph-user-focus/> Fingerprinting der Top-100k Seiten

## Mehr als ein Viertel der Top-Websites nehmen Fingerabdrücke von Nutzern

<img alt="'TABLE IV: Distribution of Alexa top-100K websites that deploy fingerprinting. Results are sliced by site rank.'" src="/wild-fprate.png" style="height:60%;margin:auto;"/>

<!--

- Crawlen der Alexa top-100k Seiten mit FP-Inspect Erkennungskomponente
- FP häufiger auf populären Seiten
- Mehr FP als man in vorherigen Studien beobachtet hat
- auch weniger populäre Seiten zeigen mehr FP auf
- overall 10,18% der top-100k verwendet FP Scripte
- Möglichkeiten für Funde:
  - Erkennung ist weitreichender als bei vorherigen Studien und/oder
  - Fingerprinting hat deutlich zugenommen

-->

---

# <ph-user-focus/> Fingerprinting der Top-100k Seiten

<h2>Fingerprinting ist auf <span class="spoiler">Nachrichten</span>seiten am häufigsten zu finden</h2>

<img alt="'Fig. 4: The deployment of fingerprinting scripts across different categories of websites.'" src="/wild-news.png" style="height:70%;margin:auto;" class="spoiler"/>

<!--
# Quizrunde: Auf welcher Kategorie von Webseiten ist FP am häufigsten zu finden?
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

- Third Party FP Anbieter:

  - 3,78% Tracking Dienste

  - 17,28% betreiben Cookie Syncing

<img
  src="/wild-vendors.png"
  alt="'TABLE V: The presence of the top vendors classified as fingerprinting on Alexa top-100K websites. Tracker column shows whether the vendor is a cross-site tracker according to Disconnect’s tracking protection list. Y represents yes and N represents no.'"
  style="height:50%"
  class="hover-right"
/>

<!--

- Liste der Top FP-Script Domains

- FP-Scripte oft von third party Anbieter
  - doubleverify, adsafeprotected und adscore sind Anti-Ad-Fraud services (prüfen ob Werbung gesehen wurde) und nutzen FP Methoden
  - Bot-Erkennungs-Services nutzen auch FP

- FP-Anbieter teilen auch Daten
  - Abgleich der FP-Anbieter mit Tracking Protection Liste von Disconnect:
    - _nur_ 3,78% der FP-Anbieter sind explizite Tracking Dienste laut Disconnect

- Cookie syncing: nutzen von FPs um Cookies (und damit Nutzerdaten) zu verknüpfen
  - Abgleich der FP-Anbieter mit Cookie Synching Liste vorheriger Forschung
    - 17,28% der FP-Anbieter beteiligen sich an Cookie Synching
  - FP-Anbieter synchronisieren cookies mit großen Werbeanbietern
    - oft auch mit mehreren Anbietern

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
- Analyse der Verteilung von JS APIs in FP- und Non-FP-Scripte
  - Extraktion von JS API Keywords aus gesammeltem Source Code und sortieren nach Verhältnis des Vorkommens in FP- und Non-FP-Scripte
  - höheres Verhältnis bedeutet API wird häufiger in FP-Scripte benutzt
  - unendlich bedeutet wird exklusiv in FP-Scripte benutzt

Tabelle

- einteilen in Cluster die gleichzeitiges Auftreten widerspiegelt
  - Nutzung neuer JS APIs für Fingerprinting entdeckt
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

- Fingerprinting der Funktionalität: abfragen von verschiedene Browser Funktionalität und APIs

  1. Permission JS API um status einzelner Berechtigungen (Benachrichtigungen, Standort und Kamera) nachzufragen

  2. Peripherie Fingerprinting: suchen nach Keyboard Layouts, Gamepads oder VR Geräten

  3. API Fingerprinting: Ausnutzen der Unterschiedlichen Features und APIs unter Browsern und Browsererweiterungen

- Algorithmisches Fingerprinting: nutzen der APIs mit bestimmte Inputs

  1. Timing Fingerprinting: `Performance` API bietet hochauflösende Timestamps
    - in Kombination mit `domainLookupStart`, `domainLookupEnd` oder `domInteractive` Browser Events

  2. Animation Fingerprinting: `requestAnimationFrame` um Framerate vom Browser zu berechnen und damit Wiederholrate vom Bildschirm

  3. Audio FP bereits beobachtet: doch nutzen weiterer Properties der AudioContext API z.B. `canPlayType` für abfragen der unterstützten Codex

  4. Sensor Fingerprinting: Ausnutzen von Sensoren an z.B. Mobilgeräten wie `devicemotion`, `deviceorientation` und neu entdeckt `userproximity`

Detailinformationen bieten Fläche für Fingerprinting


-->

---

# <ph-lightbulb/> Fazit

- FP-Inspector: Machine Learning-basierter syntaktisch und semantischer Ansatz

  - Erkennt 26% mehr FP-Scripte als pure Heuristiken
  - Bricht Webseiten bis zu 2x weniger

- Analyse der Top Seiten ergibt:

  - Fingerprinting so weit verbreitet wie nie zuvor: Von Top-100k Seiten 10.18% mit FP Scripte
  - 2,349 FP Domains wurden an Tracking Listen gesendet

- Neue FP-APIs gefunden: an Browser Anbieter und Standard Autoritäten gemeldet

- Tools für weitere Forschung veröffentlicht: [(github.com/uiowa-irl/FP-Inspector)](https://github.com/uiowa-irl/FP-Inspector)
  - Patches für OpenWPM
  - Fingerprinting Countermeasure Prototype Browsererweiterung
  - Liste neu entdeckter FP-Anbieter
  - Bug Reports für Tracking Listen und Browser Anbieter

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

<a href="https://web.cs.ucdavis.edu/~zubair/files/fpinspector-sp2021.pdf">src: U. Iqbal, S. Englehardt, and Z. Shafiq, “Fingerprinting the fingerprinters: Learningto detect browser fingerprinting behaviors” , 2020.</a>

Vorgetragen von Antonio Sarcevic - Hackerpraktikum - WS 2021/22

<img class="mt-4" src="/fh.svg" alt="FH Münster Logo">

