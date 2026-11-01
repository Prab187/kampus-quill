# Handover: Kampus Quill

You are taking over a small iPhone app. This is everything you need to start.

## What it is
A school planner for students: today's lessons, timetable, homework and tests, bag check, contacts, an AI news radio about AI in schools, and Sudoku / maths / word games by age group. Flutter app, iPhone only. Everything the user types is saved on the phone. Only the Radio tab uses the internet.

## Names
- Name under the icon and on the App Store: **Kampus Quill**
- Title inside the app and radio station: **CRGS Kampus**
- Bundle ID: `com.prabha.crgsstudentplanner`
- Unofficial: not made or endorsed by Colchester Royal Grammar School. The app uses the school's colours (purple, green, gold) but not its crest. Get the school's written permission before any public release.

## Where things are
| What | Where |
|---|---|
| App code | `lib/` |
| Tests (53) | `test/` |
| iPhone project | `ios/` (open `ios/Runner.xcworkspace`) |
| Setup, build and upload steps | `README.md` |
| Text to paste into App Store Connect and TestFlight | `APP_STORE_LISTING.md` |
| Privacy policy page | `docs/privacy.html` |
| Optional cloud build to TestFlight | `.github/workflows/testflight.yml` |

## Build and upload (Mac, Xcode, Flutter 3.47 or newer)
1. `flutter pub get`
2. `flutter build ios --release --no-codesign`
3. `open ios/Runner.xcworkspace`, choose your Team under Signing & Capabilities.
4. Device menu: **Any iOS Device (arm64)**, then **Product, Archive**, then **Distribute App, App Store Connect, Distribute, Upload**.
5. Raise the build number in `pubspec.yaml` (`version: 1.0.0+N`) for every upload.

## Known to-dos
1. **The Radio tab is not proven with the live news feeds.** It was tested with sample data only. Open Radio on a phone with internet and check stories load and are read aloud. Fix `lib/radio/news_sources.dart` if a feed changed.
2. **Privacy page:** add a contact email in `docs/privacy.html`, then host it (GitHub Pages, public repo) and use its address as the Privacy Policy URL in App Store Connect.
3. **Public release:** needs the school's permission, screenshots, the listing text and the App Review notes in `APP_STORE_LISTING.md`.
4. **App Store names must be unique.** The first choice was taken, which is why the store name differs from the in-app title.
5. **Dart 3.13 or newer** is needed (`flutter upgrade`).

## Accounts and secrets
Never share Apple passwords or sign-in codes. Access is by invitation only (App Store Connect, Users and Access, and GitHub collaborators). If you use the cloud build, add these as GitHub repository secrets, not in the code: `ASC_KEY_ID`, `ASC_ISSUER_ID`, `ASC_KEY_P8_BASE64`, `APPLE_TEAM_ID`.
