# Kampus Quill: guide for Claude Code

An iPhone school planner for students, built with Flutter. Name under the icon and on the App Store: **Kampus Quill**. Title inside the app and radio station: **CRGS Kampus**. Bundle ID `com.prabha.crgsstudentplanner`. Unofficial: not made or endorsed by Colchester Royal Grammar School; uses its colours (purple, green, gold) but never its crest.

## Commands
- `flutter pub get`
- `flutter analyze` (must report no issues)
- `flutter test` (53 tests must pass)
- `flutter run` (simulator), `flutter build ios --release --no-codesign` then open `ios/Runner.xcworkspace` to archive.
Run `flutter analyze` and `flutter test` before saying a change is done.

## Where things are
- `lib/main.dart`: app shell, theme (`buildTheme`), purple header, floating tab bar, Contacts route.
- `lib/palette.dart`: brand colours, gradients and the heading font helper `serifStyle()`.
- `lib/widgets.dart`: `Panel` (card with title), pills, `Note`. `lib/subjects.dart`: one colour per subject, badges, chips.
- `lib/screens/`: `today`, `timetable`, `homework`, `people` (Contacts), `radio`, `games`.
- `lib/games/`: Sudoku (`sudoku_logic.dart` is pure and tested), maths sprint, word scramble, `age_groups.dart` (4 age groups).
- `lib/radio/`: `news_sources.dart` (GDELT + RSS), `ai_schools.dart` (school/AI spotting, safety filter, trusted sources), `radio_controller.dart`, `speaker.dart` (iPhone voice), `radio_script.dart` (what is read aloud).
- `lib/models.dart` + `lib/store.dart`: all saved data (`AppData`) as JSON on the phone via shared_preferences.
- `test/`: unit and widget tests; `test/fakes.dart` has a fake voice and fake news source.

## Rules
- **Privacy:** everything the user types stays on the phone. Only the Radio tab uses the internet, and it sends no personal data. Do not add accounts, analytics, ads, tracking or new network calls without the owner's say-so. If something changes, update `docs/privacy.html` and the App Privacy notes in `APP_STORE_LISTING.md`.
- **Children:** keep content safe. Radio only uses trusted sources and drops sensitive stories. No chat, no open web, no purchases.
- **Saved data must keep loading.** When adding fields to `AppData`, give them defaults in `fromJson` so older saved data still works. Add a test.
- **Design:** use `Brand` colours and `serifStyle()`. The heading font (DM Serif Display) has one weight, so never request bold with it. Cards use `Panel`. Keep light and dark mode both working. Keep tap targets large (at least 44 px).
- **Wording:** short, plain, current English. UK spelling.
- **Tests:** add or update tests with every change. Widget tests that scroll lists should use `scrollUntilVisible`.
- **Never commit** passwords, keys, `.p8` files, `build/`, `Pods/`, or `.dart_tool/`.
- **App Store names must be unique.** Do not rename things casually: the name under the icon is `CFBundleDisplayName` in `ios/Runner/Info.plist`.
- **Releasing:** raise the build number in `pubspec.yaml` (`version: 1.0.0+N`) for each upload. See `ADMIN_GUIDE.md`.

## When asked for a change
1. Say in one or two sentences what you will change and which files.
2. Make the smallest change that does it. Ask if the request is unclear or affects privacy.
3. Run `flutter analyze` and `flutter test`; fix failures.
4. Summarise what changed, in plain words, and what to look at on the phone.
