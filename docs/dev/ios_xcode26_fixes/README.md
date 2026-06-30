# iOS Xcode 26 Build Fixes

This directory contains patches for fixing iOS build failures with Xcode 26, as documented in
[e-mission-docs#1144](https://github.com/e-mission/e-mission-docs/issues/1144).

Xcode 26 enforces stricter module checks and removes some previously accessible private headers.
The fixes address each build error in sequence.

## Summary of Changes

### 1. `e-mission/e-mission-data-collection` — branch `fix-ios-xcode26-build`
**Patch:** [e-mission-data-collection.patch](./e-mission-data-collection.patch)

- `src/ios/Wrapper/Battery.h`: Add `#import <UIKit/UIKit.h>` to resolve `UIDeviceBatteryState` unknown type
- `src/ios/Wrapper/StatsEvent.m`: Add `#import <UIKit/UIKit.h>` to resolve `UIDevice` usage

*Root cause:* Xcode 26 stricter module checks require explicit UIKit imports even in files that previously
inherited them transitively.
([ref](https://github.com/e-mission/e-mission-docs/issues/1144#issuecomment-4846806578))

### 2. `e-mission/cordova-server-sync` — branch `fix-ios-xcode26-build`
**Patch:** [cordova-server-sync.patch](./cordova-server-sync.patch)

- `src/ios/BEMServerSyncCommunicationHelper.h`: Add `#import <UIKit/UIKit.h>` for `UIBackgroundFetchResult` type

([ref](https://github.com/e-mission/e-mission-docs/issues/1144#issuecomment-4847214017))

### 3. `e-mission/cordova-plugin-ibeacon` — branch `fix-ios-xcode26-UILocalNotification`
**Patch:** [cordova-plugin-ibeacon.patch](./cordova-plugin-ibeacon.patch)

- `src/ios/LMLogger.m`: Replace deprecated `UILocalNotification` API (removed in iOS 26) with modern
  `UNUserNotificationCenter` from the `UserNotifications` framework.

([ref](https://github.com/e-mission/e-mission-docs/issues/1144#issuecomment-4846652991))

### 4. `e-mission/e-mission-phone` — branch `fix-ios-xcode26-build`
**Patch:** [e-mission-phone.patch](./e-mission-phone.patch)

- `package.json`: Remove `cordova-custom-config` (was looking for a plist that no longer exists)
- `package.json`: Change `cordova-launch-review` from `^4.1.3` to `github:dpa99c/cordova-launch-review`
  (no released version yet contains the Xcode 26 fix)
- `hooks/after_prepare/ios/ios_fix_advanced_http.js` (NEW): `after_prepare` hook that patches
  `cordova-plugin-advanced-http` (a third-party plugin) for Xcode 26 compatibility:
  - Removes `#import <netinet6/in6.h>` from AFNetworking files (private header, no longer accessible)
  - Adds `#import <UIKit/UIKit.h>` to `SDNetworkActivityIndicator.h`

([ref](https://github.com/e-mission/e-mission-docs/issues/1144#issuecomment-4833815337),
[ref](https://github.com/e-mission/e-mission-docs/issues/1144#issuecomment-4834033408),
[ref](https://github.com/e-mission/e-mission-docs/issues/1144#issuecomment-4846939083))

## Applying the Patches

To apply a patch to a repo:

```bash
git clone https://github.com/e-mission/<repo-name>.git
cd <repo-name>
git checkout -b fix-ios-xcode26-build    # or fix-ios-xcode26-UILocalNotification for ibeacon
git am path/to/<repo-name>.patch
git push -u origin HEAD
# Then open a PR on GitHub
```

## Follow-up: Update e-mission-phone Plugin References

Once the PRs for `e-mission-data-collection` and `cordova-server-sync` are merged and new tags are
created, update `e-mission-phone/package.json` to reference the new versions:

```json
"cordova-plugin-em-datacollection": "github:e-mission/e-mission-data-collection#<new-tag>",
"cordova-plugin-em-serversync": "git+https://github.com/e-mission/cordova-server-sync.git#<new-tag>",
```

Similarly for `com.unarin.cordova.beacon` (cordova-plugin-ibeacon) once its PR is merged:
```json
"com.unarin.cordova.beacon": "github:e-mission/cordova-plugin-ibeacon#<new-tag>",
```
