# ST-Nothing

Alle Android-Apps für Nothing-Telefone an einem Ort. Eigene Werkzeuge, kein
Store, keine Konten — sie reden über Shizuku mit dem System und zeigen, was sie
tun.

**Leitspruch: Function over form.** Eine Animation muss etwas zeigen. Ein Wert
ohne Messung ist keiner.

## Die Apps

| App | Zweck | Version | Repo |
|---|---|---|---|
| **Profile Optimizer** | Kompiliert Apps per ART auf `speed-profile`, mit automatischem Rückfall auf `speed`. | 1.2.1 | [profile-optimizer](https://github.com/steimerbyte/profile-optimizer) |
| **Background Control** | Zeigt und setzt die Standby-Buckets aller Drittanbieter-Apps. | 1.3.0 | [background-control](https://github.com/steimerbyte/background-control) |
| **Cache Report** | Liest Cache, Daten und App-Größe je Paket aus `dumpsys diskstats`, kann den Cache global leeren. | 1.3.0 | [cache-report](https://github.com/steimerbyte/cache-report) |
| **Wakeup Auditor** | Rangliste der Apps nach Jobs, Alarmen und Hintergrundzeit, mit AppOps-Schalter je App. | 1.4.0 | [wakeup-auditor](https://github.com/steimerbyte/wakeup-auditor) |
| **Bulk Compressor** | Komprimiert Bilder in großer Zahl, eines nach dem anderen. | 1.10.0 | [bulkcompressor](https://github.com/steimerbyte/bulkcompressor) |
| **Glyph Deck** | Steuert die Glyph-Segmente einzeln: Kanäle, Gruppen, Betriebsarten, Pegel. | 1.3.0 | [glyphdeck](https://github.com/steimerbyte/glyphdeck) |

## Was alle gemeinsam haben

- **Einheitliches Layoutsystem**: schwarzer Grund, Nothing-Rot `#D71921`,
  Monospace-Mikrolabels, gleiche Panels, Buttons und Diagramme. Die Vorlage
  steht in [`skills/nothingos-app`](skills/nothingos-app/SKILL.md).
- **Quersystem**: CPU- und RAM-intensive Arbeit läuft sequenziell über eine
  Warteschlange — ein Element nach dem anderen, mit sichtbarem Fortschritt.
- **Live-Histogramm** für CPU und RAM in jeder App, damit man sieht, was das
  Gerät gerade tut.
- **Kein Root.** Alles läuft über Shizuku mit UID 2000.

## Installation

Die APKs hängen an den Releases der jeweiligen Repos. Alle Apps sind mit einem
eigenen Schlüssel signiert; Updates kommen über die Release-Assets.

## Geräte

Entwickelt und gemessen auf einem Nothing Phone (3a) Pro (A059P), Android 17.
