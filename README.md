# Settings Guard

An LSPosed (Xposed) module for Samsung One UI devices that hides the serial number, IMEI 1, IMEI 2, and phone number on the **Settings > About phone** screen. Values are masked by default and can only be revealed after biometric authentication.

> **Status:** Experimental. The code has not yet been verified on real devices. Samsung may use different classes or labels depending on the One UI version, so some rows may not be masked. If you run into this, please open an [issue](../../issues) and include the LSPosed log lines prefixed with `SG:`.

## Why

The About phone screen shows the IMEI and serial number in plain text. These identifiers often leak unintentionally through screenshots, service or insurance forms, and social media posts, and can be read by anyone who picks up an unlocked device. Settings Guard masks them by default and reveals them only when the user explicitly authenticates.

## Features

- Masks the serial number, IMEI 1, IMEI 2, and phone number (`••••••••`).
- Tapping a masked value triggers biometric authentication.
- On success, the value stays visible for **15 seconds**, then is masked again.
- Non-sensitive rows (model name, network, battery, etc.) are not affected.
- Empty or placeholder values (`-`, `Unknown`, etc.) are not masked.

## Requirements

- Samsung device running One UI 8.0 or later
- Root access
- [LSPosed](https://github.com/LSPosed/LSPosed) (the Zygisk-based build is recommended)
- Fingerprint or face recognition enrolled in device settings (for biometric authentication)

## Installation

1. Open the **Actions** tab of this repository, pick the latest successful run, and download the `settings-guard-debug` artifact. Alternatively, build it yourself (see below).
2. Install the APK on your device.
3. Open the LSPosed manager and enable the **Settings Guard** module.
4. Select **only** **Settings (`com.android.settings`)** as the scope.
5. Force stop the Settings app and reopen it.

If Settings does not appear in the scope list, enable **Show system apps**.

## Usage

1. Open **Settings > About phone**.
2. The serial number, IMEI, and phone number should appear as `••••••••`.
3. Tap a masked value.
4. Complete biometric authentication. The value is shown for 15 seconds and then masked again.

## Building

The project does not need a separate source tree. All Gradle files and Kotlin sources are generated inside `.github/workflows/build.yml` and then compiled.

To build on GitHub Actions, push the repository and run the **Build Settings Guard** workflow. To build locally, you need JDK 17, the Android SDK (platform `android-35`, build-tools `35.0.0`), and Gradle 8.9.

Output: `SettingsGuard/app/build/outputs/apk/debug/*.apk`

## Known limitations

- **Untested:** The code has not yet been verified on a real Samsung device. Expect issues on the first run.
- **Samsung-specific UI:** The About phone screen may use different classes and labels across releases, so masking may not apply to every row.
- **No enrolled biometrics:** If no fingerprint or face is enrolled, the authentication step is skipped and tapping reveals the value directly. This is weak from a security standpoint. To require authentication, remove the fallback branch in `Auth.kt`.
- **False positives:** 14 to 17 digit numbers and long strings starting with `+` may be masked anywhere within Settings.
- **Scope:** Only the Settings app is protected. The `*#06#` dialer code, third-party apps, root tools, and system APIs are not affected.
- **Not a complete security solution:** Someone with physical access to the device and root or another module can bypass this protection.

## Security

This module only hides information in the user interface. It does not encrypt or store the data. If you find a security vulnerability, please do not open a public issue. Report it through the [Security](../../security) tab instead.

## Contributing

1. Fork the repository.
2. Create a branch for your change (`git checkout -b feature/your-name`).
3. Test the change on a real device and describe the result in the PR. Include log output if relevant.
4. Open a pull request.

Information about how the Settings app on different One UI versions structures these rows (classes and labels used) is especially valuable.

## License

This project is licensed under the [MIT License](LICENSE). *(Remember to add a LICENSE file to the repository.)*
