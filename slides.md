---
highlighter: shiki
layout: cover
lineNumbers: true
background: >-
  https://images.pexels.com/photos/8382599/pexels-photo-8382599.jpeg
title: "Fingerprinting the Fingerprinters: Learning to Detect Browser Fingerprinting Behaviors"
---

# Fingerprinting the Fingerprinters

Learning to Detect Browser Fingerprinting Behaviors

---

# Inhalt

- Hintergrund und Überblick: Browser Fingerprinting
- FP-INSPECTOR
  - Design
  - Evaluation
  - Limitationen
- Fingerprinting der Top-100k Webseiten
- Fazit

---

# Hintergrund und Überblick: Browser Fingerprinting

## Tracking über Fingerprinting

- Form von stateless tracking
- **Missbrauchen** von JavaScript APIs und HTTP headers um Geräte zu identifizieren
  - z.B. `Canvas` und `User-Agent`
- Aufdringlicher als z.B. Cookies da
  1. Fingerprinting ist undurchsichtig
  2. Keine Kontrolle über Fingerprinting
- Fingerprinting z.B. für bot-detection (Google reCAPTCHA)
  - aber eben auch Cross-Site Tracking möglich

<!--
- stateless: ohne speichern eines Tokens im Client
  - gegensatz zu stateful tracking über Cookie oder Local Storage
- missbräuchliche Praxis laut Browser entwickler und Standard Autoritäten wie dem World Wide Web Consortium (W3C)

- Aufdringlicher 1. Cookies im Browser angezeigt werden und 2. bearbeitet bzw. gelöscht werden können

- Cross-Site Tracking möglich und vor allem interessant weil z.B. Safari und Firefox immer stärker gegen third-party cookies vorgehen
 -->
