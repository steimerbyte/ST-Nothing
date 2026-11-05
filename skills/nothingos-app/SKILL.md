---
name: nothingos-app
description: "Einheitliches Layoutsystem für die NothingOS-App-Familie: Leitspruch „Function over form.\", Tokens, Typografie, Render-Hierarchie, Pflicht-About-Sektion, kopierbare Buttons/Panels, Diagrammregeln und die Quersystem-Pflicht."
---

# Android-App im NothingOS-Look — einheitliches Layoutsystem

Verbindlich für **alle** Apps dieser Familie: Profile Optimizer, Background Control, Cache Report, Wakeup Auditor, Bulk Compressor.
Technik-Basis: `android-dexopt-via-shizuku`. Geräteabnahme: `android-adb-device-verification`. Parallelbetrieb: `parallel-agents-one-android-device`.

Wer eine neue App baut oder eine bestehende anfasst, benutzt die Komponenten aus Abschnitt 5 — **keine eigene Variante**. Abweichungen nur, wenn eine Anforderung sie erzwingt, und dann im README begründet.

## 1. Leitspruch

> **Function over form.**

Das ist die Leitplanke für jede Entscheidung in diesen Apps:

- Eine Animation muss etwas zeigen (Fortschritt, Wirkung, Zustandswechsel), nicht schmücken. Dekoration ohne Aussage fliegt raus.
- Kein Element, das nur gut aussieht und nichts misst.
- Bei zwei gleich guten Varianten gewinnt die mit weniger Code und weniger Zustand.
- Aufwand fließt in Nachweisbarkeit (Zahlen gegen die Shell, Screenshots mit Fokusprüfung), nicht in Politur.
- Der Spruch steht in der About-Sektion jeder App (Abschnitt 4).

## 2. Tokens

```kotlin
val Accent = Color(0xFFD71921)   // Nothing-Rot
val Dim    = Color(0xFF7A7A7A)
val Panel  = Color(0xFF0D0D0D)
val Line   = Color(0xFF242424)
val Good   = Color(0xFF39D98A)
val Warn   = Color(0xFFE0A32E)
```

Hintergrund reines Schwarz, keine Verläufe, keine Elevation, keine Schatten. Rahmen 1 dp in `Line`, Radius 2 dp.

## 3. Typografie

| Rolle | Größe | Gewicht | Spacing | Farbe | Einsatz |
|---|---|---|---|---|---|
| `Title` | 28 sp | Bold | 0.5 | Weiß | Seitenname, Großbuchstaben |
| `Kicker` | 9 sp | — | 1.8 | `Dim` | Kontextzeile unter dem Titel |
| `Micro` | 9 sp | — | 1.4 | `Dim` | Feldnamen, Chips, Hinweise |
| `Value` | 22 sp | Bold | 0 | Weiß/`Accent` | Kennzahlen |
| `Body` | 12 sp | — | 0,3 | Weiß | App-Namen |
| `Mono` | 10 sp | — | 0,2 | `Dim` | Paketnamen, Rohwerte |

Alles `FontFamily.Monospace`. Deutsch mit Umlauten in allen Texten.

## 4. About-Sektion (Pflicht)

Erreichbar durch Tippen auf die Kicker-Zeile im Kopf — kein eigener Knopf, das spart Platz und bleibt der Hierarchie treu.

Inhalt, in dieser Reihenfolge:
1. App-Name in `Title`
2. `Version <BuildConfig.VERSION_NAME> · <Gerätemodell>`
3. **`Function over form.`** in `Accent`, allein stehend, nicht in Fließtext eingebettet
4. Ein Satz Zweck (max. zwei Zeilen)
5. `Kernkommandos:` als `Mono`-Liste, die tatsächlich genutzten Shell-Aufrufe

Kein Impressum, keine Lizenztexte, keine Links — die gehören ins README.

## 5. Render-Hierarchie (verbindlich)

```
1  OSW            Kicker + Titel + Aktion oben rechts
2  Live-Panel     CPU/RAM-Histogramme (Pflicht bei Arbeit > 1 s)
3  Summary        Kennzahlen, 2–4 Werte
4  Controls       Suchfeld, Sortier-Pills, Filter-Pill
5  List           LazyColumn, Zeile: Label / Paket / Chip rechts
6  ActionBar      nur bei Auswahl, unten fixiert
7  Overlays       Ergebnis-Panel, Hinweis, Dialog, About
```

Abstände: 16 dp Seitenrand, 12 dp zwischen Abschnitten, 10 dp innerhalb eines Panels. Statusleiste immer `Modifier.systemBarsPadding()`.

Zustände sind Pflicht und gehören in Slot 4/5: `LADE …`, `KEIN TREFFER FÜR "<text>"`, `KEINE DATEN`, Fehler mit erster stderr-Zeile in `Warn` — nie in `Good`.

## 6. Komponenten (kopierbar, identisch in allen Apps)

### 6.1 OSW — On-Screen-Widget

```kotlin
@Composable
fun Osw(kicker: String, title: String, action: (@Composable () -> Unit)? = null)
```
Kicker 9 sp `Dim`, Titel 28 sp Bold in Großbuchstaben, Aktion rechtsbündig. Kein Panel, direkt auf Schwarz. Kicker ist antippbar und öffnet die About-Sektion.

### 6.2 Buttons

| Typ | Signatur | Aussehen | Einsatz |
|---|---|---|---|
| Primary | `WideButton(text, onClick)` | volle Breite, `Accent`-Rahmen, gedrückt gefüllt | die eine Hauptaktion |
| Secondary | `MiniButton(text, onClick)` | `Line`-Rahmen, 36 dp hoch | AKTUALISIEREN, RESET |
| Pill | `Pill(text, selected, onClick)` | 32 dp, aktiv = `Accent`-Rahmen + `Accent`-Text | Sortierung, Filter, Zielwahl |
| Chip | `Chip(text, tone)` | 24 dp, nur Anzeige | Bucket, Status, AOT |
| Switch | `Toggle(label, on, onChange)` | 24 dp, AN in `Dim`, AUS in `Warn` | AppOps-Schalter |

Gemeinsam: Monospace, 11 sp, `letterSpacing` 1.4, Großbuchstaben, Mindesthöhe 40 dp (Touch 44), Radius 2 dp.

### 6.3 Panels

```kotlin
@Composable fun OutlineBox(content: @Composable ColumnScope.() -> Unit)
@Composable fun SummaryPanel(items: List<Pair<String, String>>)
@Composable fun LivePanel(cpu: List<Float>, ram: List<Float>)
@Composable fun ResultPanel(changed: Int, unchanged: Int, failed: List<String>)
@Composable fun AboutPanel(name: String, version: String, purpose: String, commands: List<String>)
```

Werte deutsch formatiert: Komma als Dezimaltrenner, Einheiten `B/KB/MB/GiB`, Dauer in Minuten.

### 6.4 Eingaben

- `SearchField(text, onChange, hits: Int)` — 40 dp, `Line`-Rahmen, Trefferzahl rechts (`115 TREFFER`), `×` leert.
- `Hint(lines: String)` — 9 sp `Dim`, max. zwei Zeilen, antippbar zum Ausblenden, Merker in `SharedPreferences`.

## 7. Diagramme

Alle Diagramme sind `Canvas`, ohne Bibliothek, ohne Labels im Bild.

| Typ | Maße | Regel |
|---|---|---|
| `Histogram` | 48 Slots, 46 dp hoch, Balken 62 % Slotbreite | belegt `Accent`, leer `Line`; rechts Wert in `Value` |
| `Bar` | 2 dp hoch, volle Breite | Anteil in `Accent`, Rest `Line`; ab 100 % in `Good` |
| `Spark` | 24 dp, Linie 1 dp | nur für Verläufe ohne Achse |

CPU: `/proc/stat`, `idle = nums[3] + nums[4]`, `load = 1 − Δidle/Δtotal`. RAM: `/proc/meminfo`, `used = (MemTotal − MemAvailable) / MemTotal`. Takt 700 ms, 48 Werte, im IO-Dispatcher, Abbruch beim Verlassen (`LaunchedEffect` + `repeatOnLifecycle(RESUMED)`).

## 8. Quersystem (verbindlich)

**Jede CPU- oder RAM-intensive Aktion läuft sequenziell über eine Warteschlange — ein Element nach dem anderen, nie alles gleichzeitig.**

- Ein Element pro Schritt, `yield()`/`delay(0)` dazwischen, Fortschritt sichtbar (`LÄDT · 45 / 136`).
- Lesen auf `Dispatchers.IO`, UI auf `Dispatchers.Main`.
- Verboten: `map { async { } }.awaitAll()`, Thread-Pools, `GlobalScope`, parallele Konsumenten.
- Sammelaufruf schlägt Einzelaufrufe: ein `dumpsys package` statt 380 Einzelabfragen.
- Kurzprobe: Hängt die Zahl gleichzeitiger Shell-Prozesse von der Listengröße ab? Dann ist es falsch.

## 9. Zustand und Verhalten

- Suche, Sortierung, Filter, Auswahl und Ziel in `rememberSaveable`.
- Listenposition beim Öffnen/Schließen von Details erhalten.
- Kein Auto-Reload: der Kopf nennt den Zeitpunkt (`SCHNAPPSCHUSS 17:21 · NICHT AUTOMATISCH`).
- Jede Aktion wird durch **Rücklesen** verifiziert, nicht durch die Kommandoausgabe.

## 10. Fallen aus dem Betrieb

- Debug-Builds lassen sich nie AOT-kompilieren (`--debuggable` erzwingt `verify`).
- `pm trim-caches <n>` ist der Zielwert für freien Speicher, nicht die Löschmenge (`1` = Nullop).
- `am set-standby-bucket` kennt nur `active|working_set|frequent|rare|restricted`.
- AppOps `RUN_ANY_IN_BACKGROUND` ist der schaltbare Hintergrund-Schalter pro App.
- Vor jedem Tap und jedem Screenshot `dumpsys window | grep mCurrentFocus` prüfen — ein Fehltap auf einen offenen Bestätigungsdialog kann Daten löschen.
- Ein Launcher in einer CPU-Schleife kann `system_server` auf 130 % ziehen; `am force-stop` des Launchers bricht es.
