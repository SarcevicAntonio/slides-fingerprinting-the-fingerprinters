
# <ph-fingerprint/> Browser-Fingerprinting

## Ursprünge vom Fingerprinting

- [Mayer](https://jonathanmayer.org/publications/thesis09.pdf) 2009: "quirkiness" (Systemkonfiguration) kann Nutzer identifizieren

- [Eckersley](https://link.springer.com/chapter/10.1007/978-3-642-14527-8_1) 2010: [Panopticlick](http://panopticlick.eff.org/) entwickelt um FP durchzuführen zu analysieren

- Missbrauch verschiedener APIs für FP im freien Web in verschiedenen Studien gefunden
  ([[37]](https://dl.acm.org/doi/10.1145/2660267.2660347), [[38]](https://www.esat.kuleuven.be/cosic/publications/article-2334.pdf), [[47]](https://www.esat.kuleuven.be/cosic/publications/article-3078.pdf), [[54]](https://arxiv.org/pdf/1812.01514.pdf))

- Moderne Browserfeatures und JavaScript APIs: vergrößern Oberfläche für FP (`Canvas`, `WebGL`, Browser-Erweiterungen, Sensoren an Mobilgeräten, ...)
  ([[75]](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=6547132), [[78]](https://petsymposium.org/2017/papers/hotpets/batterystatus-not-included.pdf))

- W3C 2011: Entwicklung eines [Leitfadens zur Auswertung neuer Webstandards im Bezug auf Privatsphäre](https://www.w3.org/2019/09/privacy-ig-charter.html)

<!--
- 2009: Java und Flash noch relevant
  - Fataler als JavaScript: noch mehr Informationen über Hardware und Systemkonfiguration
  - wobei JavaScript halt auch schon ausreicht um genug Schabernack zu Treiben
- 2010: Webseite Panopticlick analysiert Browser APIs und Requests um einen Fingerprint zu erstellen
  - Abgleich mit Datenbank um zu checken wie einzigartig FP ist und zur ob Identifizierung taugt

- Moderne Browserfeatures: Bettery Status API in Firefox bereits deprecated wegen Datenschutzbedenken
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

  - Wurde bereits in vielen Studien genutzt

::right::

![OpenWPM Aufbau: "Figure 1: High-level overview of OpenWPM The task manager monitors browser managers, which convert high-level commands into automated browser actions. The data aggregator receives and pre-processes data from instrumentation."](/OpenWPM.png)
[Online Tracking: A 1-million-site Measurement and Analysis](https://dl.acm.org/doi/pdf/10.1145/2976749.2978313)

<!--
3 Hauptmodule

- Browser Managers als Abstraktionsebene für einzelne Browserinstanzen
  - kommunizieren mittels Selenium mit echtem Firefox, wichtig damit Fingerprinting wie gewollt funktioniert
- Task Manager, user-facing um Kommandos an Browser zu senden
   - Nutzer interagieren mittels Domänen spezifische Sprache um z.B. zu Crawlen oder Browser Instanzen zu steuern
- Data Aggregator als Abstraktionsebene für Browserinstrumentation bzw. zur Datenextraktion
-->

---
