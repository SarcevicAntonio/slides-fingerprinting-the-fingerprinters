---
highlighter: shiki
layout: cover
lineNumbers: true
background: >-
  https://images.pexels.com/photos/8382599/pexels-photo-8382599.jpeg
title: "Die Fingerabdrücke der Fingerabdrucknehmer: Browser-Fingerprinting-Verhalten lernen und erkennen"
---

# Die Fingerabdrücke der Fingerabdrucknehmer

Browser-Fingerprinting-Verhalten lernen und erkennen

---

# Inhalt

- Hintergrund und Überblick: Browser-Fingerprinting
- FP-INSPECTOR
  - Vorarbeit: Heuristik
  - Design
  - Evaluation
  - Limitationen
- Fingerprinting der Top-100k Webseiten
- Fazit

---

# Hintergrund und Überblick: Browser-Fingerprinting

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
  - gegensatz zu stateful tracking über Cookie oder Local Storage

- laut Browser Entwickler und Standard Autoritäten wie dem World Wide Web Consortium (W3C) ist Fingerprinting auch eine "missbräuchliche Praxis"

- Aufdringlicher als Cookies da 1. Cookies im Browser angezeigt werden und 2. bearbeitet bzw. gelöscht werden können

- Cross-Site Tracking möglich und vor allem interessant weil z.B. Safari und Firefox immer stärker gegen third-party cookies vorgehen
 -->

---

# Hintergrund und Überblick: Browser-Fingerprinting

## Ursprünge vom Fingerprinting

- Jonathan R. Mayer zeigt 2009 das "quirkiness" (Systemkonfiguration) Nutzer identifizieren kann [[71]](https://jonathanmayer.org/publications/thesis09.pdf)
- 2010 entwickelt Peter Eckersley [Panopticlick](http://panopticlick.eff.org/) um Browser-Fingerprinting durchzuführen zu analysieren [[51]](https://link.springer.com/chapter/10.1007/978-3-642-14527-8_1)
- Moderne Browserfeatures und JavaScript APIs und bieten immer mehr Oberfläche zum fingerprinten (`Canvas`, `WebGL`, Extentions, Sensoren an Mobilgeräten, ...)
- Ausnutzung der APIs für Fingerprinting im freien Web in verschiedenen Studien gefunden [[37]](https://dl.acm.org/doi/10.1145/2660267.2660347), [[38]](https://www.esat.kuleuven.be/cosic/publications/article-2334.pdf), [[47]](https://www.esat.kuleuven.be/cosic/publications/article-3078.pdf), [[54]](https://arxiv.org/pdf/1812.01514.pdf),
  [[75]](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=6547132), [[78]](https://petsymposium.org/2017/papers/hotpets/batterystatus-not-included.pdf)
- W3C startete 2011 Entwicklung eines [Leitfadens zur Auswertung von neuen Web Standards im Bezug auf Privatsphäre](https://www.w3.org/2019/09/privacy-ig-charter.html)

<!--

- 2009: Damals waren Java und Flash noch relevant
  - waren noch Fataler als JavaScript, da diese Umgebungen noch mehr Informationen über Hardware und Systemkonfiguration verarbeiten konnten
  - wobei JavaScript halt auch schon ausreicht um genug Schabernack zu Treiben
- 2010: Panopticlick analyisiert browser und requests um einen Fingerprint zu erstellen und gleicht es mit einer Datenbank um zu ermitteln ob der Fingerprint einzigartig ist und zur Identifizierung taugt
  - Seite mittlerweile umgebaut und nun ein analyse gegen generelles Browser Tracking
  - Alternativ gibt es auch noch https://amiunique.org/fp welches sich eher an Technischere Menschen richtet und nur Fingerprinting betrachtet

- Browserfeatures wie Bettery Status API in Firefox bereits deprecated wegen Datenschutzbedenken
 -->

---

# Hintergrund und Überblick: Browser-Fingerprinting

## Bietet Fingerprinting eindeutige und dauerhafte Identifikatoren?

- Fingerprint als "statistischer" Identifikator

- Identifizierbarkeit ist abhängig von der Anzahl Geräten mit gleicher Konfiguration bzw. "uniqueness" des Fingerprints

- Laut Laperdrix et
  al. [[67]](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=7546540) ([AmIUnique](https://amiunique.org/)) und Eckersley [[51]](https://link.springer.com/chapter/10.1007/978-3-642-14527-8_1) ([Panopticlick](http://panopticlick.eff.org/)) besitzen 83% - 90% der Geräte eindeutigen Fingerprint (wahrscheinlich etwas biased)

- Stabile oder Verknüpfbare Fingerprints zum Tracking benötigt:
  - Laut Eckersley haben 37% der wiederkehrende Besucher mehr als einen Fingerprint, aber 65% der Geräte konnten über einfache heuristische Verknüpfung re-identifiziert werden
  - Vastel et al. [[93]](https://hal.inria.fr/hal-01652021/document#:~:text=FP%2DSTALKER%20is%20an%20approach,machine%20learning%20to%20boost%20accuracy.) verbesserten 2017 diese Heuristik und konnten auf AmIUnique Besucher im Schnitt 74 Tage verfolgen

<!--
  - "statistischer" also kein deterministischer Identifikator

  - AmIUnique und Panopticlick wird primär von technisch Interessierten Personen genutzt, daher sind die Zahlen biased
 -->
