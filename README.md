# PushtiPrakash Media & Remote Assets Repository

Welcome to the central media and remote content repository for the **PushtiPrakash White-Label Mobile Platform** (Android & iOS). This repository acts as the single source of truth for runtime app identity, sacred scriptures, high-fidelity audio recitations, book cover artworks, and modular split JSON configurations.

---

## 1. Directory Tree & Architecture Overview

All mobile clients fetch runtime configurations and stream media assets directly from this repository. Ensure all files follow this exact folder hierarchy:

```text
pushtiprakash-media/
├── README.md
└── src/
    └── assets/
        ├── images/
        │   ├── logo.png                  # App icon, header emblem & watermark
        │   └── shodash_granth.jpg        # Book cover photo & banner watermark
        ├── audio/
        │   ├── yamunashtakam.mp3         # Sacred stotra recorded audio track
        │   └── mangalacharanam.mp3       # Chapter audio track (strict lowercase)
        └── mock/
            ├── app_identity.json         # Client branding, primary colors, mediaBaseUrl
            ├── supportedLanguages.json   # Enabled languages & TTS locales
            ├── ui_translations.json      # Complete multi-lingual UI string registry
            ├── books.json                # Books manifest & chapter catalog
            ├── updateBannerMessage.json  # Sticky notification banner (HTML-supported)
            ├── about_info.json           # About Us text, contacts & feedback links
            ├── notification_schedules.json # Local push notification schedule
            └── chapters/
                └── book_shodash_granth/  # Modular on-demand chapter transcripts
                    ├── ch_yamunashtakam.json
                    └── ch_balabodha.json
```

---

## 2. Asset Specifications & Size Guidelines

To maintain fast startup times (0ms local cache hydration) and responsive audio playback on cellular networks, adhere to these technical specifications for every media asset:

| Asset Category | Relative Repository Path | File Format | Recommended Dimensions / Specs | Recommended File Size | Max Allowed Size | Key Usage in App |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Client Logo** | `src/assets/images/logo.png` | **PNG** (RGBA, transparent) | `1024 x 1024 px` (Square 1:1) | `100 KB – 250 KB` | **500 KB** | Native launcher app icon (scaled to 68%), Splash screen, About Us page, Global watermark |
| **Book Cover Artwork** | `src/assets/images/<book_name>.jpg` | **JPG** / **WEBP** | `1200 x 400 px` (3:1) or `900 x 300 px` (3:1) | `50 KB – 300 KB` | **600 KB** | Dashboard book cards, Chapters page 15% height title section watermark, Media metadata |
| **Recorded Audio Tracks** | `src/assets/audio/<stotra>.mp3` | **MP3** (MPEG-1 Layer 3) | `128 kbps` CBR, `44.1 kHz`, Joint Stereo | `2 MB – 6 MB` (per stotra) | **15 MB** | Reader streaming, in-app audio scrub bar, offline background audio caching |
| **Remote Config JSONs** | `src/assets/mock/*.json` | **JSON** (UTF-8, minified) | Valid JSON, No trailing commas | `2 KB – 15 KB` | **50 KB** | Multi-tenant dynamic branding, UI localization, daily sync manifest |
| **Modular Transcripts** | `src/assets/mock/chapters/<book_id>/<ch_id>.json` | **JSON** (UTF-8) | Valid JSON containing localized transcripts | `1 KB – 10 KB` | **100 KB** | On-demand split chapter text, anti-copy protected reader view |

---

## 3. Strict Naming & Casing Rules (Critical)

> [!WARNING]
> **Linux, GitHub, and Android file systems are strictly case-sensitive.**
> If your `books.json` references `"audio/mangalacharanam.mp3"`, but the file on GitHub is named `"audio/MANGALACHARANAM.mp3"`, GitHub will return a **`404: Not Found`** error, and the app will be forced into TTS fallback mode.

* **Always use lowercase alphanumeric filenames**:  
  * Use: `yamunashtakam.mp3`, `mangalacharanam.mp3`, `shodash_granth.jpg`.
  * Do NOT use: `MANGALACHARANAM.mp3`, `Yamuna Ashtakam (Final).MP3`, `Cover Photo #1.jpeg`.
* **Separate words with underscores or hyphens**: Use `shodash_granth.jpg` or `balabodha.mp3`. Never use spaces or special punctuation characters (`&`, `%`, `+`, `?`).
* **Consistent Extensions**: Always use `.jpg` (not `.jpeg` or `.JPG`), `.png` (not `.PNG`), and `.mp3` (not `.MP3`).

---

## 4. Cache-Busting & Versioning Strategy

Both mobile devices (Android Chromium and iOS WebKit) and CDN servers cache images and audio aggressively to conserve user data. If you replace an existing image or audio file with an updated version using the same filename, devices will continue showing the old cached version.

### How to Force Immediate Updates: Query String Versioning

Whenever you update an image or audio file, increment the version tag (`?v=2`, `?v=3`) in your corresponding JSON file:

#### 1. In `src/assets/mock/app_identity.json`:
```json
{
  "logoUrl": "images/logo.png?v=2",
  "watermarkUrl": "images/logo.png?v=2"
}
```

#### 2. In `src/assets/mock/books.json`:
```json
{
  "coverImageUrl": "images/shodash_granth.jpg?v=2",
  "chapters": [
    {
      "id": "ch_yamunashtakam",
      "audioUrl": {
        "hi": "audio/yamunashtakam.mp3?v=2",
        "gu": "audio/yamunashtakam.mp3?v=2",
        "en": "audio/yamunashtakam.mp3?v=2"
      }
    }
  ]
}
```

As soon as users perform a pull-to-refresh on their Dashboard or reopen the app, the app detects the new query parameter and downloads the refreshed asset immediately.

---

## 5. Base URL Configuration

The application consumes these assets via two base URL settings:

### A. Remote Config URL (`firebaseConfigUrl`)
* **Configured in**: `src/environments/environment.prod.ts` (or GitHub Actions input `firebase_config_url`).
* **Target Path**: Points directly to the `mock/` directory.
* **Recommended URL**:
  ```text
  https://raw.githubusercontent.com/shreeg-sg/pushtiprakash-media/main/src/assets/mock/
  ```

### B. Media Base URL (`mediaBaseUrl`)
* **Configured in**: `src/assets/mock/app_identity.json`.
* **Target Path**: Points to the `src/assets/` root.
* **Recommended URL**:
  ```json
  "mediaBaseUrl": "https://raw.githubusercontent.com/shreeg-sg/pushtiprakash-media/main/src/assets"
  ```
  *(Avoid using `cdn.jsdelivr.net` for active development to prevent 24-hour edge server cache locks).*

---

## 6. HTML Formatting in Sticky Notification Banners

The dashboard sticky notification banner supports rich text and HTML formatting defined inside `src/assets/mock/updateBannerMessage.json`. You can use:

* **Line Breaks**: `<br>` or literal `\n`.
* **Bold Emphasis**: `<b>Text</b>` or `<strong>Text</strong>`.
* **Italic Emphasis**: `<i>Text</i>` or `<em>Text</em>`.
* **Custom Sizing/Styling**: `<span style="font-size: 1.05rem; color: #8B2635;">Text</span>`.

```json
{
  "hi": "<b>जय श्री कृष्ण!</b><br>षोडश ग्रंथ के सभी १६ अध्यायों का ऑडियो अब उपलब्ध है।",
  "gu": "<b>જય શ્રી કૃષ્ણ!</b><br>ષોડશ ગ્રંથના તમામ ૧૬ પ્રકરણોનો ઓડિયો હવે ઉપલબ્ધ છે.",
  "en": "<b>Jai Shri Krishna!</b><br>Audio for all 16 treatises is now live."
}
```

---

## 7. Pre-Deployment Verification Checklist

Before pushing changes to the `main` branch, verify:

1. [ ] **Repository Visibility**: Repository is set to **Public** under **Settings → General → Danger Zone**.
2. [ ] **Branch Target**: Default branch is confirmed as **`main`**.
3. [ ] **Casing Check**: All audio files under `src/assets/audio/` and images under `src/assets/images/` are completely lowercase.
4. [ ] **JSON Syntax**: All modified `.json` files pass JSON validation (no trailing commas after the last item in objects or arrays).
5. [ ] **Cache Version**: If an existing image or audio track was replaced, increment its `?v=` tag in the respective JSON.
