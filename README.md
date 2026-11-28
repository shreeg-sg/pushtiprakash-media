# White-Label Media & Remote Assets Repository
### Tenant Media Specifications, Dimensions, Naming Rules & JSON Structure

This repository hosts all runtime configurations, branding assets, books, audio tracks, advertisements, Hindu calendar data, and optional Unique ID lists for your **White-Label Mobile & Web Reading App**. Updates pushed here take effect automatically on user devices without rebuilding or resubmitting the app.

---

## 1. Directory Structure

Maintain this exact folder hierarchy inside `src/assets/`:

```text
your-media-repository/
├── README.md
└── src/
    └── assets/
        ├── <tenantName>/
        │   └── uniqueId.json                 # Optional ID allowlist when uniqueIdSource = "json"
        ├── books/
        │   └── <book_id>.json                  # Per-book chapter index (keeps books.json lightweight)
        ├── audio/
        │   ├── chapter_1.mp3                 # Recorded MP3 tracks (strict lowercase)
        │   └── chapter_2.mp3
        ├── images/
        │   ├── logo.png                      # App icon, header emblem & watermark
        │   ├── book_cover_1.jpg              # Book cover artwork
        │   ├── chapter_img_1.jpg             # Optional chapter header illustrations
        │   └── advertisement/
        │       └── banner_1.jpg              # Sticky bottom advertisement banners
        └── mock/
            ├── app_identity.json             # App name, theme colors, mediaBaseUrl & developerCredit
            ├── auth_config.json              # Optional login, Supabase & Unique ID settings
            ├── supportedLanguages.json       # Enabled languages & TTS locales
            ├── ui_translations.json          # Multilingual UI & login labels
            ├── books.json                    # Book catalog & chapter index
            ├── updateBannerMessage.json      # Sticky dashboard announcements (multi-banner, links, dates)
            ├── about_info.json               # Organization description, email & feedback links
            ├── notification_schedules.json   # Local reading reminders
            ├── advertisement.json            # Bottom ad carousel configuration
            ├── calendar/
            │   └── <year>/
            │       ├── january.json          # Monthly Gregorian + Hindu Tithi calendar & events
            │       └── ...                   # lowercase month names (january..december)
            └── chapters/
                └── <book_id>/
                    └── <chapter_id>.json     # On-demand chapter transcript & images
```

---

## 2. Asset Dimensions, Aspect Ratios & File Specifications

| Asset Type | Repository Path | File Format | Recommended Dimensions & Aspect Ratio | Recommended Size | Max Size | Usage in App |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **App Icon & Logo** | `src/assets/images/logo.png` | **PNG** (RGBA, transparent) | `1024 x 1024 px` (**1:1** square) | `100 KB - 250 KB` | **500 KB** | Android/iOS launcher icon (center 68% safe zone), Onboarding, Login, Sidebar, Watermark |
| **Book Cover Artwork** | `src/assets/images/<book_name>.jpg` | **JPG** or **WEBP** | `1200 x 400 px` (**3:1**) or `800 x 400 px` (**2:1**) landscape | `50 KB - 250 KB` | **600 KB** | Dashboard book card watermark & Chapters page header banner |
| **Chapter Header Images** | `src/assets/images/<image_name>.jpg` | **JPG**, **PNG**, or **WEBP** | `800 x 600 px` (**4:3**) or `900 x 1200 px` (**3:4**) | `50 KB - 250 KB` | **500 KB** | Center-aligned illustrations above chapter text in Reader view |
| **Advertisement Banners** | `src/assets/images/advertisement/<name>.jpg` | **JPG**, **PNG**, or **WEBP** | `1200 x 240 px` (**5:1**) or `1080 x 216 px` (**5:1**) wide strip | `40 KB - 150 KB` | **300 KB** | Sticky bottom ad carousel (~15% screen height); keep text centered away from edges |
| **Recorded Audio** | `src/assets/audio/<track_name>.mp3` | **MP3** (MPEG-1 Layer 3) | `128 kbps` CBR, `44.1 kHz`, Joint Stereo | `2 MB - 6 MB` | **15 MB** | Reader audio player & offline cache; leave `audioUrl` empty (`""`) for TTS fallback |
| **Config JSONs** | `src/assets/mock/*.json` | **JSON** (UTF-8) | Valid JSON, no trailing commas | `1 KB - 20 KB` | **50 KB** | Branding, auth, languages, UI labels, banners, ads, schedules |
| **Calendar Month JSONs** | `src/assets/mock/calendar/<year>/<month>.json` | **JSON** (UTF-8) | Valid JSON with `days` + `hinduDay` + `events` | `2 KB - 15 KB` | **50 KB** | Tippani Gregorian/Hindu Tithi calendar & daily event list |
| **Chapter JSONs** | `src/assets/mock/chapters/<book_id>/<ch_id>.json` | **JSON** (UTF-8) | Valid JSON with `transcript` map | `1 KB - 15 KB` | **100 KB** | On-demand chapter text & optional images |
| **Unique ID Allowlist** | `src/assets/<tenantName>/uniqueId.json` | **JSON** (UTF-8) | Array or `{ "ids": [...] }` of valid IDs | `1 KB - 100 KB` | **500 KB** | Used only when `auth_config.json` sets `uniqueIdSource: "json"` |

---

## 3. Strict Naming & Formatting Rules

1. **Lowercase Only & No Spaces:** Linux, GitHub, and Android/iOS CDNs are case-sensitive. Use lowercase letters, numbers, underscores, or hyphens only (e.g., `banner_1.jpg`, `ch_01_intro.json`, `november.json`). Never use spaces or `# ? & % + ( )`.
2. **Standard Extensions:** Always use `.png`, `.jpg`, `.mp3`, `.json` (avoid `.JPG`, `.JPEG`, `.MP3`).
3. **Valid JSON:** Never leave trailing commas after the last item in arrays or objects. Use 6-digit hex colors (e.g., `"#8B2635"`) in `app_identity.json`.
4. **Calendar Folder Spelling:** Always use `src/assets/mock/calendar/<year>/<month>.json` (lowercase English month name, e.g., `january.json`).
5. **Ad Image Path Matching:** Store ad images in `src/assets/images/advertisement/<uniqueName>` and reference `"images/advertisement/<uniqueName>"` in `advertisement.json`.

---

## 4. Cache-Busting When Replacing Existing Assets

CDNs (jsDelivr, Cloudflare) and mobile WebViews cache media aggressively. Whenever you replace an existing image or audio file with the same filename, increment a version parameter (`?v=2`, `?v=3`) in the JSON:

```json
{
  "logoUrl": "images/logo.png?v=2",
  "coverImageUrl": "images/book_cover_1.jpg?v=2",
  "audioUrl": { "hi": "audio/chapter_1.mp3?v=2" }
}
```

---

## 5. Key Tenant Configuration Files

| File | Purpose | Notes |
| :--- | :--- | :--- |
| `mock/app_identity.json` | Brand name, colors, logo, watermark, `mediaBaseUrl`, `developerCredit` | Drives theme across all screens including Login |
| `mock/auth_config.json` | Login toggle, Supabase URL/key, 30-day expiry, Unique ID mode | Set `enabled: false` to skip login; `uniqueIdSource: "supabase"` or `"json"` |
| `<tenantName>/uniqueId.json` | Optional roll-number / member ID list | Read when `requireUniqueId: true` and `uniqueIdSource: "json"` |
| `mock/supportedLanguages.json` | Enabled languages & TTS voice codes | At least one language must have `"isEnabled": true` |
| `mock/ui_translations.json` | Interface & login screen labels per language | Includes `login_*`, `ok_button`, `cancel_button` keys |
| `mock/books.json` | Lightweight book index (`chapterCount`, `chaptersUrl`) | Points each book to `books/<bookName>.json` (inline `chapters` still supported) |
| `books/<bookName>.json` | Per-book chapter list | Loaded on-demand when opening a book; each chapter links to `chapters/<book_id>/<ch_id>.json` |
| `mock/updateBannerMessage.json` | Dashboard announcement banners | Supports multiple active banners, `isNew` badge, date windows, and `targetBookId`/`targetChapterId` links |
| `mock/advertisement.json` | Bottom sticky ad carousel | Supports `order`, `isEnabled`, `fromDate`/`toDate`, `url`, and `imagePath` |
| `mock/calendar/<year>/<month>.json` | Tippani Hindu calendar | Supports `hinduDay`, `hinduTithiShort`, `paksha`, `isHighlighted`, and `events` |
| `mock/notification_schedules.json` | Local scheduled reminders | Supports daily or date-specific reminders with deep links |
| `mock/about_info.json` | Organization bio, contact email, website, feedback links | Displayed on the About Us page |

---

## 6. Pre-Launch Tenant Checklist

- [ ] **Public HTTPS Host:** Repository or CDN bucket is publicly readable over `https://` with CORS enabled.
- [ ] **Square Logo:** `src/assets/images/logo.png` is `1024 x 1024 px` transparent PNG under `500 KB`.
- [ ] **Lowercase Filenames:** All files in `images/`, `audio/`, `calendar/`, and `chapters/` are lowercase without spaces.
- [ ] **JSON Validated:** All files in `src/assets/mock/` pass strict JSON validation.
- [ ] **Auth Configured (if enabled):** `mock/auth_config.json` has the tenant's `supabaseUrl`, `supabaseAnonKey`, and `tenantId` (and `src/assets/<tenantName>/uniqueId.json` if using JSON Unique ID mode).
