<div align="center">

<img src="assets/readme-cover.png" width="100%" alt="Granola Exporter turns meeting conversations into private, searchable local notes" />

# Granola Exporter

**Keep every meeting searchable, portable, and under your control.**

A macOS app for syncing Granola meetings, recording new conversations, and building a local Markdown archive you can use anywhere.

</div>

## What it does

Granola Exporter creates one Markdown file per meeting, adds structured metadata, and maintains a searchable `INDEX.md`.

It supports three workflows:

- Sync notes and transcripts from Granola through its official API.
- Record microphone and system audio locally, then transcribe it on your Mac.
- Browse meetings and participants in a local library built from the files you control.

The installed app saves its archive to `~/Documents/Granola Export/` by default.

## Key features

- **Portable Markdown** — preserve meeting titles, dates, participants, notes, transcripts, and speaker labels
- **Official Granola sync** — import new notes and transcripts with an API key stored in macOS Keychain
- **Incremental updates** — identify new meetings and avoid duplicating files already in the archive
- **Local meeting capture** — record microphone and system audio as separate tracks for clearer `Me` and `Them` labels
- **On-device transcription** — process recordings locally with whisper.cpp
- **Meeting library** — search, sort, paginate, and read transcripts without leaving the app
- **People view** — group meetings by participant and quickly reopen the conversations attached to each person
- **Background updates** — optional timed scans, launchd scheduling, menu-bar controls, and macOS notifications
- **CLI included** — export through Terminal or scripted workflows when needed

## How to use

### Install the app

1. Download `Granola-Export-1.28.dmg` from the [latest release](https://github.com/productdave/granola-exporter/releases/latest).
2. Open the DMG and drag **Granola Export** into **Applications**.
3. Eject the DMG before launching the app.
4. On first launch, right-click **Granola Export**, choose **Open**, then choose **Open** again if macOS shows an unidentified-developer warning.

The current release is unsigned, so the one-time Gatekeeper step is expected.

### Sync existing Granola meetings

1. Create an official Granola API key. Availability may depend on your Granola plan.
2. Open **Settings** in Granola Exporter and save the key. It is stored in macOS Keychain.
3. Choose **Sync from Granola**.
4. Open the **Library** or **People** view to search and read the imported meetings.

Each sync fetches meetings missing from the local archive and refreshes `INDEX.md`.

### Record and transcribe locally

Install whisper.cpp first:

```bash
brew install whisper-cpp
```

Then:

1. Choose **Record**.
2. Grant Microphone and Screen Recording permissions when macOS asks.
3. Stop the recording when the meeting ends.
4. Let Granola Exporter transcribe both tracks and add the result to the library.

The first transcription downloads a Whisper model. If `whisper-cli` is unavailable, the original audio is preserved so it can be processed later.

### Enable background updates

Open **Settings**, choose an interval, and enable automatic scanning. Granola Exporter can check for new API meetings and finish pending local recordings while the app is open or through an optional launchd background task.

### Use the CLI

The included CLI offers a compatibility path based on Granola Desktop's local data and legacy transcript access:

```bash
python3 extract.py                  # export all meetings
python3 extract.py --limit 5        # test with five meetings
python3 extract.py --out ~/Notes    # choose another output folder
python3 extract.py --force          # overwrite existing exports
python3 extract.py --no-transcripts # export notes without transcript requests
```

## Run locally

### Requirements

- macOS 13+
- Python 3.9+
- Homebrew whisper.cpp for local transcription
- Xcode or the Swift command-line toolchain for building the recorder
- A Granola API key for official cloud sync

```bash
git clone https://github.com/productdave/granola-exporter.git
cd granola-exporter

python3 -m pip install customtkinter pyobjc pyinstaller
brew install whisper-cpp

./recorder/build-recorder.sh
python3 gui.py
```

Build and package the app with:

```bash
./build.sh
./make-dmg.sh
```

## Tech stack

| Layer | Technology |
|---|---|
| Desktop app | Python, Tkinter, CustomTkinter |
| Recording | Swift, ScreenCaptureKit, AVAudioEngine |
| Transcription | whisper.cpp |
| macOS integration | PyObjC, AppKit, Keychain, launchd |
| Meeting sync | Official Granola public API |
| Packaging | PyInstaller, DMG build scripts |
| CLI compatibility | Granola Desktop local data and legacy transcript endpoint |

## Status and limitations

Granola Exporter v1.28 is an unsigned macOS project. Official API syncing requires a valid API key and may require an eligible Granola plan.

Some meetings may contain notes but no retained transcript. Cloud sync requires internet access, while local transcription requires `whisper-cli` and an initial model download. The current recorder helper is built for Apple Silicon, so local recording is not supported on Intel Macs in this release.

The CLI compatibility workflow depends on Granola Desktop's local data formats and an undocumented transcript endpoint, which may change without notice. Exported Markdown and locally transcribed recordings remain on your Mac. The repository does not currently include an automated test suite.
