# Flutter CI/CD App

[![Build and Release App Bundle](https://github.com/atikur01/flutter_ci_cd/actions/workflows/release.yml/badge.svg)](https://github.com/atikur01/flutter_ci_cd/actions/workflows/release.yml)
[![Flutter](https://img.shields.io/badge/Flutter-v3.12.2-blue.svg)](https://flutter.dev)
[![Platform](https://img.shields.io/badge/Platform-Android-green.svg)](https://developer.android.com)

A professional Flutter application equipped with automated Continuous Integration & Continuous Delivery (CI/CD) pipelines to compile, sign, and distribute release-ready Android App Bundles (AAB).

---

## Features

- **Cross-Platform Base**: Built on the Flutter framework.
- **Release Signing**: Android release build configuration integrated with Gradle settings (`key.properties` and Java Keystore).
- **Automated CI/CD**: Pre-configured GitHub Actions workflow to build, sign, and upload release assets (`.aab`) to GitHub Releases.
- **Kotlin DSL**: Uses modern `build.gradle.kts` configuration for Android build properties.

---

## Project Structure

Here is an overview of the key configuration directories and files in this repository:

```
flutter_ci_cd/
├── .github/
│   └── workflows/
│       └── release.yml          # GitHub Actions workflow for automated AAB builds & release uploads
├── android/
│   ├── app/
│   │   ├── upload-keystore.jks  # Keystore file used for signing release builds
│   │   └── build.gradle.kts     # Android module build script (signing configuration, min/target SDKs)
│   ├── key.properties           # Reference properties for keystore configuration
│   └── build.gradle.kts         # Root Android build script
├── lib/
│   └── main.dart                # Application entry point
└── pubspec.yaml                 # Flutter package & dependency definitions
```

---

## Getting Started

### Prerequisites

To run or build this application, you must install the following tools locally:

- **Flutter SDK**: `^3.12.2` (Or compatible version)
- **Java Development Kit (JDK)**: Version 17
- **Android SDK**: With platform tools and build-tools installed

### Installation & Run

1. Clone the repository:
   ```bash
   git clone https://github.com/atikur01/flutter_ci_cd.git
   cd flutter_ci_cd
   ```

2. Fetch pub dependencies:
   ```bash
   flutter pub get
   ```

3. Run the application in debug mode:
   ```bash
   flutter run
   ```

---

## Release Signing Setup

The project uses Gradle's signing configuration to sign Android App Bundles automatically during release compiles.

### Configuration Files

- **`android/key.properties`**: Stores references to the keystore file path and password properties:
  ```properties
  storePassword=YOUR_STORE_PASSWORD
  keyPassword=YOUR_KEY_PASSWORD
  keyAlias=YOUR_KEY_ALIAS
  storeFile=YOUR_KEYSTORE_FILENAME (e.g. upload-keystore.jks)
  ```
- **`android/app/upload-keystore.jks`**: The keystore binary file containing the signing key, placed under the `android/app` directory.

### Generating a New Keystore

To generate your own upload keystore, use the `keytool` command:

```bash
keytool -genkey -v -keystore android/app/upload-keystore.jks \
  -keyalg RSA -keysize 2048 -validity 10000 \
  -alias upload
```

> [!WARNING]
> Keep your keystore passwords secure. Avoid pushing production credentials to public repositories.

---

## CI/CD Pipeline

The project contains a GitHub Actions workflow (`release.yml`) that automates build and release management.

### Workflow Triggers

- **Push to `main`**: Runs the pipeline and publishes/updates a release tag based on the version defined in `pubspec.yaml`.
- **Release Published**: Runs when a new GitHub Release is created, attaching the signed App Bundle (`.aab`) to the release.
- **Manual Trigger**: Can be run manually via the Actions tab in GitHub.

### Secrets Configuration

If you decide to exclude the raw keystore/properties from version control for production apps, you can store the base64-encoded keystore and key properties in GitHub repository secrets and decode them during the CI run:

1. **`KEYSTORE_BASE64`**: Base64 encoded string of the `.jks` file.
2. **`KEY_PROPERTIES`**: Complete content of the `key.properties` file.

These can be written to the appropriate paths during execution before triggering `flutter build appbundle`.

---

## Building Locally

To build a release-ready Android App Bundle (AAB) on your local machine:

```bash
flutter build appbundle --release
```

The output bundle will be generated under:
`build/app/outputs/bundle/release/app-release.aab`
