---
highlighter: shiki
layout: cover
lineNumbers: true
background: https://images.pexels.com/photos/8382599/pexels-photo-8382599.jpeg
title: "Die Fingerabdrücke der Fingerabdrucknehmer: Browser-Fingerprinting-Verhalten lernen und erkennen"
---

# Die Fingerabdrücke der Fingerabdrucknehmer

## Browser-Fingerprinting-Verhalten lernen und erkennen

<a href="https://web.cs.ucdavis.edu/~zubair/files/fpinspector-sp2021.pdf" class="paper-src">Src: Fingerprinting the Fingerprinters: Learning to Detect Browser Fingerprinting Behaviors</a>

<!-- prettier-ignore-start -->
<!--  -->
---
layout: image-right
image: https://images.pexels.com/photos/8382611/pexels-photo-8382611.jpeg
---
<!-- prettier-ignore-end -->

# Inhalt

- <ph-fingerprint/> Browser-Fingerprinting

- <ph-magnifying-glass-duotone/> FP-INSPECTOR

  - Vorarbeit: Heuristik

  - Design

  - Evaluation

  - Limitationen

- <ph-list-numbers/> Fingerprinting der Top-100k

- <ph-lightbulb/> Fazit

---

# <ph-fingerprint/> Browser-Fingerprinting

## Online Tracking durch Fingerprinting

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

- [Jonathan R. Mayer](https://jonathanmayer.org/publications/thesis09.pdf) zeigt 2009 das "quirkiness" (Systemkonfiguration) Nutzer identifizieren kann

- 2010 entwickelt [Peter Eckersley](https://link.springer.com/chapter/10.1007/978-3-642-14527-8_1) die Seite [Panopticlick](http://panopticlick.eff.org/) um Browser-Fingerprinting durchzuführen zu analysieren

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

  - 40 Fingerprinting-Seiten aus Alexa Top-10k **(0.4%)**

- 2013 Paper ["FPDetective: Dusting the Web for Fingerprinters"](https://www.esat.kuleuven.be/cosic/publications/article-2334.pdf)

  - 404 Fingerprinting-Seiten aus Alexa Top-1 Millionen **(0.04%)**

- Weitere Studien [[37]](https://dl.acm.org/doi/10.1145/2660267.2660347), [[47]](https://www.esat.kuleuven.be/cosic/publications/article-3078.pdf), [[54]](https://arxiv.org/pdf/1812.01514.pdf),

  [[78]](https://petsymposium.org/2017/papers/hotpets/batterystatus-not-included.pdf) finden immer mehr Fingerprinting-Seiten über die Jahre.

- 2019 schreibt [Washington Post](https://www.washingtonpost.com/technology/2019/10/31/think-youre-anonymous-online-third-popular-websites-are-fingerprinting-you/): "At least a third of the 500 sites Americans visit most often use hidden code to run an identity check on your computer or phone." **(>30%)**

<!--
- Weitere Studien: Studien verwenden alle verschiedene Strategien um auf fingerprinting zu prüfen, dennoch lässt sich ein Trend erkennen
- Trotz der verstärkten Kontrolle durch Browser-Anbieter und die Öffentlichkeit ist das Fingerprinting nach wie vor weit verbreitet.
 -->

---

# <ph-fingerprint/> Browser-Fingerprinting

## Gegenmaßnahmen

- randomization: Randomisiert Rückgabewerte von APIs mit Rauschen (Canvas Defender)

  - kann Funktionen der Seite verändern

  - reversibel, wenn "random" Wert auf Ursprungswert basiert

  - Noise output kann sogar als Datenpunkt fingerprint genutzt werden

- normalization: Rückgabewerte fixieren oder APIs blockieren (Tor, Brave)

  - können Seite limitieren oder komplett brechen

- heuristisch: vordefinierte Patterns (Privacy Badger, Disconnect)

  - Heuristik muss eng genug sein, um wenige Seiten falsch zu blocken

  - Enge Heuristik können auch Fingerprinting-Scripts übersehen

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

  - dynamische Analyse unzureichend bei Skripten die über Nutzerinput oder Browser-Events getriggert werden

  - FP-INSPECTOR benutzt nun Kombination beider Ansätze

<!--
- stateless und stateful tracking sehr unterschiedlich, deshalb Lösung die beides zusammenpackt unzureichend

- dynamische Analyse: lässt Skript laufen und beobachtet verhalten (API Calls, Call Stack, etc)
 -->

---
