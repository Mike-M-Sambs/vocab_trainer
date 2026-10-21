# AI suggestions

## original prompt: 
Hi Lumo,
I am a software developer and want to create a new app for android phones.
My general idea is that:
It should be a vocabulary trainer companion app to repeat the already learned vocabulary, for the app "AnkiDroid". But the unique selling point for this app should be a camera mode to photograph / scan pages of a textbook, where the scanned vocabulary page automatically will be added to your AnkiDroid vocab-database. 
As an extra service in later iterations of the app, speech / sound samples of the vocab could be downloaded and added from external internet-pages.
In the 1st phase I would like to design the code structure and want to know what software & languages I need to develop an android app, which frontend & backend I could use and which database I could use for this app.

Can you help me with this task?

## answer:

a vocabulary trainer with automatic textbook scanning via OCR
IDE: Android Studio
Language: Kotlin (new) || Java (established)
UI framework: Jetpack Compose. || XML Layouts + ViewBinding (old, but simple, without animations)
SRS (spaced repetition scheduler) Algorithm: SM-2 (Anki's classic) or FSRS (newer, empirically better but more complex)
Database: Room (an abstraction over SQLite) || Realm (MongoDB's mobile database) (just no)
    Deck → id, name, language pair
    Card → id, deckId, front, back, example sentence, image reference
    ReviewLog → cardId, timestamp, grade (again/hard/good/easy), next due date
    AudioAsset → cardId, file path, source URL (for phase 3)
Architecture: MVVM (Model-View-ViewModel)
    UI layer: Compose screens — card review, deck browser, scanner, settings
    ViewModel layer: business logic like the SRS scheduling (when to show which card)
    Repository layer: single source of truth that coordinates local DB, OCR pipeline, and later the remote audio service
    Data layer: Room database + files (audio clips, images)

com.yourapp.vocabtrainer
├── ui/            (Compose screens: review, decks, scanner, settings)
├── viewmodel/
├── data/
│   ├── db/        (Room entities, DAOs, database)
│   └── repo/      (CardRepository, DeckRepository)
├── srs/           (SM-2 scheduler, review grading)
├── ocr/           (CameraX capture, ML Kit recognizer)
├── parser/        (OCR text → card candidates, per-format strategies)
└── util/

The USP: Camera Scanning / OCR: Google ML Kit Text Recognition  || Tesseract OCR (via tesseract-android wrapper) (no Goolge!! but 50MB per language!) || small OCR: ML Kit On-Device Mode (Best Balance, ~4MB)

For the text recognition, Google is the best fit: free, on-device (works offline), supports Latin and non-Latin scripts (Chinese, Japanese, Korean, Cyrillic, Arabic etc. via separate libraries), and integrates directly with the CameraX library for the capture flow.

The hard engineering challenge won't be the OCR itself — it will be the parsing step: converting raw recognized text lines into structured word/translation pairs. Textbooks format vocabulary in wildly different ways (two-column tables, dash-separated lines, "word – plural (gen.) – translation"). Design this as its own parser component with per-formatting-strategy logic, and let the user confirm/edit the parsed pairs before import. That confirmation screen will save you enormous pain compared to fully-automatic import.

What You Don't Need Yet (later phases)
    Audio samples (phase 3): Start with Android's built-in TextToSpeech engine for pronunciations — zero infrastructure. Downloading recorded native-speaker audio from external sources can come later;


## resume prompt:

# PROJECT CONTEXT — Vocab Scanner (Android) → AnkiDroid Exporter

## Overview
Building an Android app that scans textbook pages via camera/OCR, extracts vocabulary, and exports as CSV for AnkiDroid import. Companion tool, not a full flashcard app.

## Tech Stack (Confirmed Working ✅)
| Component    | Choice                    | Status             |Rationale |
|--------------|---------------------------|--------------------|----------|
| Language     | Kotlin                    | Done               |Google-preferred, modern Android standard |
| UI Framework | XML Layouts + ViewBinding | Done               |Small binary, works on entry-level phones, mature debugging tools |
| Database     | Room (SQLite wrapper)     | Dependencies wired |Native Android, offline-first, well-documented |
| OCR Engine   | ML Kit On-Device (Latin)  | Dependencies wired |Best accuracy/speed balance, ~4MB footprint |
| Architecture | MVVM                      | Planned            |Standard pattern, Compose integration ready (though using XML) |
| Gradle       | KSP (Room compiler)       | Confirmed working with AGP 9.4.1 + Kotlin 2.2.10 | - |

## Build Configuration
- **AGP**: 9.4.1
- **Kotlin**: 2.2.10
- **KSP**: 2.2.10-1.0.29
- **minSdk**: 24
- **targetSdk**: 37
- **compileSdk**: 37
- **JVM target**: 11

## Export Format
- CSV (simplest for AnkiDroid import)
- Fields: `front;back` or tab-separated (to be decided)

## Key Requirements
- **Target**: Entry-level Android phones (performance optimization priority)
- **Offline-first**: All core functionality must work without internet
- **Phase 1: Core features**: Camera scanning → OCR → parse → save and export vocab
- **Phase 2: additional features**: Audio samples from external sources

## OCR Implementation Plan
- Use: `com.google.mlkit:text-recognition` (Latin script, standalone mode)
- Parser layer: Custom parser component to convert raw OCR text → vocab entry pairs
- User confirmation screen before saving to database (critical UX decision)

## Package Structure Planned
com.murraays.vocab_trainer
├── ui/ (Activities/Fragments with ViewBinding)
├── viewmodel/ (ViewModel classes)
├── data/
│ ├── db/ (Room entities, DAOs, database)
│ └── repo/ (Repositories coordinating DB + OCR)
├── ocr/ (CameraX + ML Kit integration)
├── parser/ (OCR text → vocab entry converters)
└── util/ (Helpers, export utilities)

## Key Room Entities Needed
- `VocabEntry` (id, front, back, deckId, timestamp)
- `Deck` (id, name, languagePair) — optional if single-deck MVP

## OPEN QUESTIONS
- [ ] CSV separator: semicolon vs tab
- [ ] Multi-deck support now or later?
- [ ] Gallery import fallback if camera fails?

---
## Current Phase 1: Core features — Scanner UI & OCR Wiring

Completed:
✅ Environment setup (Android Studio project)
✅ Gradle dependencies (ML Kit, CameraX, Room, Coroutines)
✅ KSP configured for Room
✅ gradle/libs.versions.toml and build.gradle.kts cleaned

## WHAT TO DO NEXT (UPDATE THIS SECTION FOR EACH SESSION)
1. Create scanner activity layout (CameraX PreviewView + capture button)
2. Request camera runtime permissions
3. Capture image → convert to ML Kit InputImage
4. Run text recognition on still image
5. Display extracted text in a TextView for verification

Please guide me.

[Insert your immediate task here, e.g.:
"Create new Android Studio project with XML layout setup, add Room dependencies, configure ML Kit gradle files"]

