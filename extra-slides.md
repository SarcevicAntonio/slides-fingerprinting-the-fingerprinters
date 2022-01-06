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
