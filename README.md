# AutoFreelance Android App

A fully native Android application — pixel-perfect port of [autofreelance.lovable.app](https://autofreelance.lovable.app/) — built with **Kotlin + Jetpack Compose**.  
Connects to the same Supabase backend and Supabase Edge Functions as the web app.

---

## ✨ Features

| Screen | Description |
|--------|-------------|
| **Auth** | Sign in / Sign up with Supabase email auth |
| **Dashboard** | Stats, pipeline distribution bar chart, agent crew overview, recent scan runs, top-matched leads |
| **Lead Pipeline** | Filter/search by stage, CV match score bars, run AI agents per lead, add manual leads |
| **Lead Detail** | Full screener analysis, proposal draft, QC results with risk flags, approve / reject / edit & approve |
| **Job Sources** | Toggle enabled/auto-scan per source, edit search keywords, scan now |
| **Profile** | Display name, headline, about, skills, hourly rate, CV text, portfolio items |

---

## 🛠 Tech Stack

- **Language**: Kotlin 1.9
- **UI**: Jetpack Compose + Material 3
- **DI**: Hilt
- **Networking**: Retrofit 2 + OkHttp + Moshi
- **Storage**: DataStore Preferences (session tokens)
- **Navigation**: Compose Navigation
- **Backend**: Supabase (REST API + Edge Functions)
- **Min SDK**: 26 (Android 8.0) | **Target SDK**: 34

---

## 🚀 Build Instructions

### Prerequisites

- **Android Studio Hedgehog (2023.1.1)** or newer — [Download](https://developer.android.com/studio)
- **JDK 17** (bundled with Android Studio)
- **Android SDK 34** (install via SDK Manager in Android Studio)

### Steps

1. **Open the project**
   ```
   File → Open → select the AutoFreelance folder
   ```

2. **Let Gradle sync** (Android Studio will prompt automatically)

3. **Run on device or emulator**
   - Connect a physical device with USB debugging enabled, OR
   - Create an AVD (API 30+) via AVD Manager
   - Click the ▶ Run button

4. **Build a release APK** (optional)
   ```
   Build → Generate Signed Bundle / APK → APK
   ```

### Command-line build

```bash
# Debug APK
./gradlew assembleDebug

# Release APK (requires signing config)
./gradlew assembleRelease

# Output location:
# app/build/outputs/apk/debug/app-debug.apk
```

---

## 🔑 Configuration

The Supabase URL and anon key are embedded in `app/build.gradle` as `buildConfigField` entries:

```groovy
buildConfigField("String", "SUPABASE_URL", "\"https://qqcnycqsweyjmybqeuvo.supabase.co\"")
buildConfigField("String", "SUPABASE_ANON_KEY", "\"eyJ...\"")
```

These match your existing web app — no extra configuration needed. All data is shared between the web and Android app.

---

## 📁 Project Structure

```
app/src/main/java/com/autofreelance/app/
├── AutoFreelanceApp.kt          # Hilt Application
├── MainActivity.kt              # Single activity entry point
├── data/
│   ├── model/Models.kt          # All data classes (Lead, Profile, FeedSource…)
│   ├── network/
│   │   ├── SupabaseApi.kt       # Retrofit REST interface
│   │   ├── EdgeFunctionsApi.kt  # Pipeline & scan Edge Functions
│   │   └── SessionManager.kt   # DataStore token storage
│   └── repository/
│       └── AutoFreelanceRepository.kt
├── di/
│   └── NetworkModule.kt         # Hilt DI wiring
└── ui/
    ├── Screen.kt                # Route definitions
    ├── AppNavigation.kt         # NavHost + bottom nav
    ├── theme/                   # Dark color scheme + typography
    ├── common/Components.kt     # Shared composables (StageBadge, StatCard…)
    ├── auth/                    # AuthScreen + AuthViewModel
    ├── dashboard/               # DashboardScreen + DashboardViewModel
    ├── leads/                   # LeadsScreen, LeadDetailScreen + ViewModels
    ├── sources/                 # SourcesScreen + SourcesViewModel
    └── profile/                 # ProfileScreen + ProfileViewModel
```

---

## 🎨 Design

The app uses the same dark color palette as the web app:

| Token | Hex |
|-------|-----|
| Background | `#0A0C10` |
| Surface | `#111318` |
| Primary (Indigo) | `#6366F1` |
| Accent (Cyan) | `#22D3EE` |
| Success | `#22C55E` |
| Warning | `#F59E0B` |
| Error | `#EF4444` |

---

## 🧩 Pipeline Stages

Leads move through the same stages as the web app:

`intake` → `analyzed` → `needs_review` → `awaiting_approval` → `proposal_sent`  
(or `qc_rejected` → `rejected` / `skipped`)

Running agents on a lead calls the same Supabase Edge Function (`/functions/v1/pipeline`) that powers the web app.

---

## 📝 Notes

- The app shares the same Supabase backend as the web app — all leads, profiles, and sources are fully in sync.
- Edge Function calls (run agents, scan, approve) require your Supabase project to have the functions deployed.
- Session tokens are stored securely in DataStore (not SharedPreferences).
