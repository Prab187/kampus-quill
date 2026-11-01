# Admin guide: set up your Mac, change the app, ship it

The detailed Mac set-up and "change it and see it" steps are in `MAC_DEVELOPMENT_GUIDE.md`. This guide covers Apple access and shipping.

For the person taking over Kampus Quill. Follow the parts in order. Each step says what you should see. If something does not match, stop and read the Troubleshooting table at the end.

This guide works on any recent Mac laptop, including a MacBook Neo. The only real needs are: a macOS version the latest Xcode supports, about 40 GB of free disk space, and an internet connection. If `flutter doctor` asks for **Rosetta** on an Apple-silicon Mac, run the command it prints.

---

## Part A: Accept your access (10 minutes)

1. **GitHub.** Open the invitation email from GitHub, or go to github.com/notifications, and click **Accept invitation** for `Prab187/kampus-quill`. Turn on two-factor sign-in on your GitHub account if it asks.
2. **Apple.** Open the invitation email from Apple, click **Accept**, and create or sign in with your own Apple ID. Turn on two-factor authentication.
3. **Check it worked.**
   - appstoreconnect.apple.com > **Apps** should list **Kampus Quill**.
   - github.com/Prab187/kampus-quill should open.
4. Never ask for, or share, anyone's Apple password or sign-in codes. Access works by invitation only.

## Part B: Set up the Mac (one time, about 1 hour)

1. **Update macOS:** Apple menu > System Settings > General > Software Update.
2. **Install Xcode** from the Mac App Store (it is large). Open it once, agree to the licence, and let it install the extra components. Then in Terminal:
   ```
   sudo xcode-select --switch /Applications/Xcode.app/Contents/Developer
   sudo xcodebuild -runFirstLaunch
   ```
3. **Sign Xcode in:** Xcode > Settings > **Accounts** > **+** > Apple ID. Your team (the one you were invited to) should appear.
4. **Install Flutter** (version 3.47 or newer): follow docs.flutter.dev/install/macos, unzip it, and add it to your path. Then check:
   ```
   flutter --version
   flutter doctor
   ```
   Fix anything with a red cross that it flags for **iOS**. You can ignore Android and web.
5. **Install CocoaPods** if `flutter doctor` asks for it: `brew install cocoapods` (needs Homebrew from brew.sh).
6. **Install git:** run `git --version`. If it is missing, macOS offers to install the command line tools. Say yes.

## Part C: Get the code and run it (15 minutes)

```
git clone https://github.com/Prab187/kampus-quill.git
cd kampus-quill
flutter pub get
flutter analyze
flutter test
```
You should see "No issues found" and "All tests passed" (53 tests).

To see the app on screen: `open -a Simulator`, wait for the phone to appear, then `flutter run`. Stop it with `q` in Terminal.

## Part D: Change the app with Claude Code on your Mac

Claude Code is a helper that works inside the project folder on your computer. It can read the code, make the changes you ask for, and run the checks. It needs a claude.ai paid account.

1. **Install it:**
   ```
   curl -fsSL https://claude.ai/install.sh | bash
   ```
   If it fails with `EACCES: permission denied ... ~/.local/state`, fix ownership with `sudo chown -R "$(whoami)" ~/.local` and run the install line again. Open a new Terminal window afterwards.
2. **Sign in:** `claude auth login`, and finish in the browser. Never paste codes into chats or emails.
3. **Start it in the project:**
   ```
   cd kampus-quill
   claude
   ```
   It reads `CLAUDE.md` in the repo automatically. That file tells it how this project works.
4. **Ask for changes in plain words.** Good examples:
   - "Change the accent colour to X and show me where it is used."
   - "Add a reminder setting to the Homework screen. Ask me anything you are unsure about first."
   - "Fill in `REQUIREMENTS.md` with me, then build the first item."
5. **Always check before you keep a change:**
   ```
   git diff
   flutter analyze
   flutter test
   ```
   If a check fails, tell Claude Code the exact message and ask it to fix it.
6. **Save your work safely.** Work on a branch, then open a pull request on GitHub:
   ```
   git checkout -b my-change
   git add -A
   git commit -m "Describe the change"
   git push -u origin my-change
   ```
   Merge on github.com when you are happy. Do not push straight to `main` for big changes.

Rules for changes (also in `CLAUDE.md`):
- Do not add accounts, analytics or ads without a privacy plan. The App Store privacy answer is "Data Not Collected". Changing that changes the privacy page and App Store answers too.
- Do not commit passwords, keys or `.p8` files.
- Keep the app safe for children. No unfiltered web content, no chat, no purchases.
- Keep the "unofficial" line. Get the school's written permission before a public release.

## Part E: Build, upload and test (each release, about 30 minutes)

1. Raise the build number in `pubspec.yaml`, for example `version: 1.0.0+2`. Apple refuses a number it has seen. For a new feature version, change the first part too, for example `1.1.0+3`.
2. In Terminal:
   ```
   flutter build ios --release --no-codesign
   open ios/Runner.xcworkspace
   ```
3. In Xcode: click **Runner** > the **Runner** target > **Signing & Capabilities** > tick **Automatically manage signing** > choose your **Team**.
4. Device menu: **Any iOS Device (arm64)**. Then menu **Product > Archive**. (Do not press the play button for this.)
5. In the Organizer window: **Distribute App > App Store Connect > Distribute > Upload**.
6. Wait for Apple's email (10 to 30 minutes). In App Store Connect > Kampus Quill > **TestFlight** the build appears. Add it to your group.
7. Test on your phone with the free TestFlight app.

Adding testers: **Internal** (people on your Apple team, up to 100, no review) or **External** (anyone by email or public link, up to 10,000, Apple reviews the first build). Text to paste is in `APP_STORE_LISTING.md`.

Optional: `.github/workflows/testflight.yml` builds and uploads from GitHub without a Mac. It needs four repository secrets (`ASC_KEY_ID`, `ASC_ISSUER_ID`, `ASC_KEY_P8_BASE64`, `APPLE_TEAM_ID`), set in GitHub > Settings > Secrets. Never put them in the code.

## Part F: Admin jobs in App Store Connect

- **People:** Users and Access > **+** to invite, or click a person to change their role. **Admin** can do almost everything. **App Manager** manages the app and TestFlight. **Developer** can upload builds. Only the Account Holder can accept Apple's agreements or manage payments and tax.
- **Agreements:** if you see a yellow banner saying an agreement was updated, the Account Holder must accept it at developer.apple.com/account. Uploads fail until then.
- **App name:** the App Store name must be unique. It can be changed in App Store Connect > App Information.

## Part G: The public App Store later

Read `APP_STORE_LISTING.md` from the top. You will need the school's permission, a public privacy page with a contact email (`docs/privacy.html`), iPhone screenshots (1320 x 2868), and to finish the age rating and App Privacy answers.

## Troubleshooting

| What you see | What to do |
|---|---|
| `Because crgs_planner requires SDK version ^3.13.7` | Your Flutter is too old. Run `flutter upgrade`. |
| `zsh: command not found: claude` | Open a new Terminal window. If still missing, add it to the path: `echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc && source ~/.zshrc`. |
| `A build only device cannot be used to run this target` | You pressed play with **Any iOS Device**. Use **Product > Archive** instead. |
| `PLA Update available` | The Account Holder must accept the updated agreement at developer.apple.com/account. |
| `App Record Creation Error ... name already being used` | The app name is taken. Create the app record by hand on App Store Connect with another unique name, then upload again. |
| Signing errors, "No profiles found" | Choose your Team in Signing & Capabilities and make sure you accepted the Apple invitation. |
| `git pull` says local changes would be overwritten (`project.pbxproj`) | Xcode edited the signing settings. Run `git checkout -- ios/Runner.xcodeproj/project.pbxproj`, pull, then choose your Team again. |
| Pod errors | `cd ios && pod repo update && pod install`, then try again. |
| Radio shows "Couldn't load stories" | Check the internet. If it persists, a news feed changed. See `lib/radio/news_sources.dart`. |
