# Developing Kampus Quill on your MacBook

From a fresh Mac to changing the app and watching it update on screen. The steps work on any recent Mac laptop, including a MacBook Neo. Check what chip yours has with `uname -m` in Terminal: `arm64` (Apple silicon) or `x86_64` (Intel). Both work.

## 1. What you will install

| App or tool | What it is for | Where from |
|---|---|---|
| macOS, up to date | The system. Xcode needs a recent version. | System Settings > General > Software Update |
| **Xcode** | Builds iPhone apps and includes the Simulator (a pretend iPhone on your screen). Large download. Keep about 40 GB free. | Mac App Store |
| **Flutter SDK** (3.47 or newer) | The framework the app is written in. | docs.flutter.dev/install/macos |
| **Git** | Downloads and saves the code. | Comes with Xcode. Check with `git --version`. |
| **Homebrew** | Installs other tools. | brew.sh |
| **CocoaPods** | iPhone libraries used by Flutter plugins. | `brew install cocoapods` |
| **Visual Studio Code** | Editor with Run and Reload buttons. Recommended. | code.visualstudio.com |
| **Claude Code** (optional) | A helper in Terminal that reads the project and makes the changes you describe. Needs a paid claude.ai account. | see section 6 |
| **TestFlight** (on your iPhone) | Installs the finished app on your phone. | iPhone App Store |

## 2. Install, in this order

1. **Update macOS.**
2. **Xcode:** install from the App Store, open it once, agree to the licence, and let it install extra components. If it offers to download the **iOS** simulator, say yes. Then in Terminal:
   ```
   sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
   sudo xcodebuild -runFirstLaunch
   ```
   Xcode > Settings > Accounts > **+** > sign in with the Apple ID that accepted the invitation.
3. **Homebrew:** paste the one-line install command from brew.sh, then follow its "Next steps" lines.
4. **Flutter:** follow docs.flutter.dev/install/macos (download, unzip, add to your path). Check:
   ```
   flutter --version
   ```
   It must say Dart 3.13 or newer (Flutter 3.47+). If not, run `flutter upgrade`.
5. **CocoaPods:** `brew install cocoapods`
6. **VS Code:** install it, open it, then Extensions (left bar): install **Flutter** (this also installs Dart). Then press Cmd+Shift+P, type `shell command`, and choose "Install 'code' command in PATH".
7. **Check everything:**
   ```
   flutter doctor
   ```
   You want green ticks for Flutter, Xcode and VS Code. Ignore Android and Chrome. If it says "Rosetta", run the command it prints.

## 3. Get the project and check it works
```
git clone https://github.com/Prab187/kampus-quill.git
cd kampus-quill
flutter pub get
flutter test
```
You should see "All tests passed".

## 4. See the app (three ways)

**A. On a pretend iPhone (easiest)**
```
open -a Simulator
flutter run
```
The first build takes a few minutes. When the app opens in the Simulator, click and type in it like a real phone. To try a different phone, use the Simulator menu File > Open Simulator.

**B. On your own iPhone, with a cable**
1. On the iPhone: Settings > Privacy & Security > **Developer Mode** > on, then restart. Plug it in and tap **Trust**.
2. In Xcode open `ios/Runner.xcworkspace`, click Runner > Signing & Capabilities, tick **Automatically manage signing**, choose your **Team**.
3. In Terminal: `flutter devices`, then `flutter run -d <the iPhone's name>`.
4. First time only, on the iPhone: Settings > General > VPN & Device Management > your Apple ID > **Trust**.
(For the finished app without a cable, use TestFlight, section 7.)

**C. As a window on the Mac (Apple silicon only)**
If `flutter devices` lists "Mac (Designed for iPad)", run `flutter run -d` followed by that name.

The Radio tab needs the internet in all three.

## 5. Make a change and see it instantly

1. Open the project in VS Code: `cd kampus-quill` then `code .`
2. Start the app as in section 4. Leave it running. In Terminal you will see the hints `r`, `R` and `q`.
3. **Try it.** Open `lib/screens/today_screen.dart`, find the text `'Your name'`, change it to `'Type your name'`, and save (Cmd+S).
4. Click the Terminal window and press **r** (hot reload). The change shows in the Simulator in about a second, and the app keeps its place.
   - `r` = reload your changes. `R` = restart the app. `q` = quit.
   - Changes to `pubspec.yaml`, fonts, images or anything in `ios/` need a full stop and `flutter run` again.
5. Before you keep a change:
   ```
   flutter analyze
   flutter test
   ```
   Both must pass.

**Where things live**

| To change | Edit |
|---|---|
| Colours, gradients, heading font | `lib/palette.dart` and `buildTheme` in `lib/main.dart` |
| Tab names, header, floating tab bar | `lib/main.dart` |
| Words on a screen | `lib/screens/` (today, timetable, homework, people, radio, games) |
| Subject colours | `lib/subjects.dart` |
| Games, age groups, word lists | `lib/games/age_groups.dart` |
| Radio: news sources, safety filter | `lib/radio/news_sources.dart`, `lib/radio/ai_schools.dart` |
| What the radio says out loud | `lib/radio/radio_script.dart` |
| Name under the icon | `CFBundleDisplayName` in `ios/Runner/Info.plist` |
| App icon | `ios/Runner/Assets.xcassets/AppIcon.appiconset` |

## 6. Let Claude Code do the changes

1. Install: `curl -fsSL https://claude.ai/install.sh | bash`, then open a new Terminal window. If it fails with `EACCES ... ~/.local/state`, run `sudo chown -R "$(whoami)" ~/.local` and try again.
2. Sign in: `claude auth login`. Do the sign-in in the browser. Never paste codes into chats or emails.
3. Start it inside the project: `cd kampus-quill`, then `claude`. It reads `CLAUDE.md` first, which holds the project's rules.
4. Keep the app running in the Simulator in another Terminal window. Ask for a change in plain words ("Make the Today greeting say the school day number"). Claude edits the files, you press **r** in the other window and look.
5. It cannot see your Simulator, so tell it what you see. You can drag a screenshot file into the Terminal window to show it.
6. Ask it to run `flutter analyze` and `flutter test` and to explain what it changed. Read `git diff` before you keep anything.

## 7. Save it and put it on your phone

**Save:**
```
git checkout -b my-change
git add -A
git commit -m "What I changed"
git push -u origin my-change
```
Then open a pull request on github.com and merge it.

**Ship to TestFlight:**
1. Raise the number after the plus in `pubspec.yaml` (for example `1.0.0+2`).
2. `flutter build ios --release --no-codesign`, then `open ios/Runner.xcworkspace`.
3. Device menu: **Any iOS Device (arm64)**, then **Product > Archive**.
4. **Distribute App > App Store Connect > Distribute > Upload**.
5. After Apple's email (10 to 30 minutes), open the free TestFlight app on your iPhone and install it.
More detail and Apple admin jobs: `ADMIN_GUIDE.md`.

## 8. If something goes wrong

| You see | Do this |
|---|---|
| `requires SDK version ^3.13.7` | `flutter upgrade` |
| `flutter doctor` flags Xcode | Open Xcode once, accept the licence, run the two `sudo xcode...` lines in step 2 |
| Pod or CocoaPods error | `cd ios && pod repo update && pod install`, then retry |
| "No profiles" or signing error | Pick your Team in Xcode > Signing & Capabilities. Check you accepted the Apple invitation. |
| Simulator is blank or slow | Close it, `flutter clean`, then `flutter run` |
| `A build only device cannot be used to run this target` | You pressed play with "Any iOS Device". Use **Product > Archive** instead. |
| `claude: command not found` | Open a new Terminal window |
| Anything else | Copy the exact error text and ask Claude Code, or send it to the app's owner |
