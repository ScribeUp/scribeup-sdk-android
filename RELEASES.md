# ScribeUp Android SDK — Release Notes

All notable changes to the ScribeUp Android SDK are documented here, organized by version.

---

## [0.13.0](https://repo1.maven.org/maven2/io/scribeup/scribeupsdk/0.13.0/)

### Breaking
- The SDK no longer registers `OAuthCallbackActivity` and ships no exported components,
  eliminating vulnerability scanner findings in apps that embed it. Your app must now
  receive the `scribeup://{applicationId}/closeSafari` deep link and forward it to
  `SubscriptionManager.handleDeepLink`; see the README section "OAuth completion deep
  link". Without this setup, the Custom Tab is not dismissed after third-party OAuth.

### Added
- `SubscriptionManager.handleDeepLink(context, uri): Boolean`: consumes the OAuth
  completion callback. Returns `false` for any other URI, so it is safe to call for
  every deep link the app receives.

### Deprecated
- `OAuthCallbackActivity`: kept as an unregistered compat shim that can be re-declared
  in your manifest as a stopgap. Will be removed in a future release.

---

## [0.12.0](https://repo1.maven.org/maven2/io/scribeup/scribeupsdk/0.12.0/)

### Changed
- Hardened OAuth callback handling and refactored deep-link routing into a dedicated internal activity
- Improved resilience of fragment restoration after the host process is recreated

---

## [0.11.0](https://repo1.maven.org/maven2/io/scribeup/scribeupsdk/0.11.0/)

### Changed
- Raised minimum Android SDK version from 22 to 23
- Updated DataDog SDK dependency to 3.8.0
