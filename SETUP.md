# 🛠️ Zplit Complete Development Environment Setup Guide

> A no-nonsense, command-line-driven setup guide to build and run Zplit on Android from scratch without version conflicts.

---

## 📌 Verified Compatible Version Matrix

Before installing anything, ensure your toolchain matches these exact tested versions:

| Component | Verified Version | Notes |
|---|---|---|
| **Flutter SDK** | **`3.47.5`** | Pinned tag in git (avoids Gradle 9 / AGP breaking changes) |
| **Dart SDK** | **`3.9.x`** | Bundled automatically inside Flutter 3.47.5 |
| **Java / JDK** | **Java 17 LTS (Temurin)** | Installed via SDKMAN; Gradle 8 will crash on Java 21/25 |
| **Gradle** | **`8.14`** | Configured via `android/gradle/wrapper/gradle-wrapper.properties` |
| **Android SDK Platform** | **API 35 (Android 15) & API 34** | Required by native BLE/NFC/Wi-Fi plugins |
| **Android Build-Tools** | **`35.0.0`** | Installed via Android Studio SDK Manager or Android CLI |

---

## 1️⃣ Step 1: Install Java 17 LTS via SDKMAN!

Do **not** use system default Java 21 or Java 25 (e.g., Fedora 45 defaults to Java 25, which crashes Gradle with `* What went wrong: 25.0.4`).

```bash
# 1. Install SDKMAN! (if not installed)
curl -s "https://get.sdkman.io" | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"

# 2. Install Java 17 Temurin LTS
sdk install java 17.0.14-tem

# 3. Confirm Java 17 is active
java -version
# Expected output: openjdk version "17.0.14" ... Temurin
```

---

## 2️⃣ Step 2: Install Flutter via Git Clone

Installing Flutter via git gives you instant switching between versions without re-downloading.

```bash
# 1. Clone Flutter into your home directory
git clone https://github.com/flutter/flutter.git -b stable ~/flutter

# 2. Add Flutter to your PATH (add to ~/.bashrc or ~/.zshrc)
echo 'export PATH="$HOME/flutter/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# 3. Check out the verified working tag for Zplit
cd ~/flutter
git checkout 3.47.5

# 4. Tell Flutter to use your SDKMAN Java 17 explicitly
flutter config --jdk-dir "$HOME/.sdkman/candidates/java/current"

# 5. Verify Flutter version
flutter --version
# Expected: Flutter 3.47.5 • Dart 3.9.x
```

---

## 3️⃣ Step 3: Install Android Studio & Required SDKs

### A. Install Android Studio
- **Recommended via JetBrains Toolbox:** Download [JetBrains Toolbox](https://www.jetbrains.com/toolbox-app/) and install **Android Studio** in one click.
- **Alternative (Direct / Package Manager):** Download from [developer.android.com/studio](https://developer.android.com/studio) or install via your distro package manager.

### B. Required SDK Packages
Open Android Studio $\rightarrow$ **Settings / Preferences** $\rightarrow$ **Languages & Frameworks** $\rightarrow$ **Android SDK**:

1. **SDK Platforms Tab:**
   - [x] **Android 15.0 ("VanillaIceCream") — API Level 35**
   - [x] **Android 14.0 ("UpsideDownCake") — API Level 34**
   - Click **Apply** to install.

2. **SDK Tools Tab:**
   - [x] **Android SDK Build-Tools 35** (35.0.0)
   - [x] **Android SDK Command-line Tools (latest)**
   - [x] **Android SDK Platform-Tools**
   - Click **Apply** to install.

### C. Accept Android Licenses
Run this command in terminal and press `y` to accept all prompts:
```bash
flutter doctor --android-licenses
```

*(Note for CLI users: If using the standalone **Android CLI**, install with `android sdk install "platforms;android-35" "platforms;android-34" "build-tools;35.0.0" "cmdline-tools;latest"`).*

---

## 4️⃣ Step 4: Clone Zplit & Prepare Dependencies

```bash
# 1. Clone the repository
git clone https://github.com/StabilityNexus/Zplit.git
cd Zplit

# 2. Switch to the active development branch (all development happens on dev)
git checkout dev

# 3. Fetch all Flutter dependencies
flutter pub get

# 4. Generate Drift (SQLite) database code
dart run build_runner build --delete-conflicting-outputs
```

---

## 5️⃣ Step 5: Verify the Entire Setup

Run `flutter doctor -v` to ensure every checkmark is green:

```bash
flutter doctor -v
```

**Expected Green Checks:**
```text
[✓] Flutter (Channel stable, 3.47.5 ...)
[✓] Android toolchain - develop for Android devices (Android SDK version 35.0.0)
    • Java version OpenJDK Runtime Environment Temurin-17.0.14+7
    • All Android licenses accepted.
[✓] Connected device (your physical phone or emulator)
[✓] Network resources
• No issues found!
```

Verify static analysis:
```bash
flutter analyze
# Expected: No issues found!
```

---

## 6️⃣ Step 6: Connect Phone & Run

1. On your Android phone:
   - Go to **Settings $\rightarrow$ About Phone** and tap **Build Number** 7 times to enable Developer Options.
   - Go to **Settings $\rightarrow$ Developer Options** and enable **USB Debugging**.
2. Connect your phone via USB cable and allow the USB Debugging RSA prompt on your phone screen.
3. Verify your device is recognized:
   ```bash
   flutter devices
   # Your phone model will be listed (e.g. 2412DPC0AI • android-arm64)
   ```
4. Build and run the app:
   ```bash
   flutter run
   ```

To build a standalone APK without running:
```bash
flutter build apk --debug
# APK output: build/app/outputs/flutter-apk/app-debug.apk
```

---

## ⚠️ Common Pitfalls & Solutions

### 1. `* What went wrong: 25.0.4`
* **Cause:** System default Java is Java 25 or Java 21, which is unsupported by Gradle 8.
* **Fix:** Run `sdk use java 17.0.14-tem` and point Flutter to it:  
  `flutter config --jdk-dir "$HOME/.sdkman/candidates/java/current"`.

### 2. `Error: Your project's Gradle version (8.14.0) is lower than Flutter's minimum supported version of 9.3.1`
* **Cause:** Running cutting-edge Flutter (e.g. Flutter 3.50+), which expects Gradle 9 and breaks on older plugins.
* **Fix:** Checkout the verified version `cd ~/flutter && git checkout 3.47.5`. Alternatively, pass `--android-skip-build-dependency-validation`.

### 3. `Failed to download package: Android SDK Platform 35`
* **Cause:** Network timeout during automatic CLI download.
* **Fix:** Open Android Studio $\rightarrow$ SDK Manager $\rightarrow$ Check **Android 15 (API 35)** $\rightarrow$ Click **Apply** to complete the download visually.
