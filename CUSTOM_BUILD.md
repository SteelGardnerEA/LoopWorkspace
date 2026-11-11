# Loop Custom — ready for a phone build

This branch builds a **separate app** that can sit next to the standard Loop from `main`, under the same Apple team. It also includes the test chart colors and the 7-day swipe-back. It does not change insulin dosing.

Do not merge this branch into `main`. `main` must keep building the standard Loop app.

The name on the phone is one line in `LoopConfigOverride.xcconfig`:

```
MAIN_APP_DISPLAY_NAME = Loop Custom
```

## Identifiers

Replace `TEAMID` with the 10-character Apple Team ID (the `TEAMID` secret).

| What | Apple name | Identifier |
| --- | --- | --- |
| iPhone app | Loop Custom | `com.TEAMID.loopkit.LoopCustom` |
| Intent extension | Loop Custom Intent Extension | `com.TEAMID.loopkit.LoopCustom.Loop-Intent-Extension` |
| Status extension | Loop Custom Status Extension | `com.TEAMID.loopkit.LoopCustom.statuswidget` |
| Widget | Loop Custom Widget Extension | `com.TEAMID.loopkit.LoopCustom.LoopWidgetExtension` |
| Watch app | Loop Custom Watch | `com.TEAMID.loopkit.LoopCustom.LoopWatch` |
| Watch extension | Loop Custom Watch Extension | `com.TEAMID.loopkit.LoopCustom.LoopWatch.watchkitextension` |
| App Group | Loop Custom App Group | `group.com.TEAMID.loopkit.LoopCustomGroup` |

The link scheme is `LoopCustom`. Standard Loop on `main` stays `com.TEAMID.loopkit.Loop` and `group.com.TEAMID.loopkit.LoopGroup`. The two apps do not share that group, so their stored data stays separate.

## What you do by hand, once

The six GitHub Actions secrets are not in the repo yet. Add them yourself (this agent must not). They are the same six names the standard browser build uses:

- `TEAMID`
- `FASTLANE_ISSUER_ID`
- `FASTLANE_KEY_ID`
- `FASTLANE_KEY`
- `GH_PAT`
- `MATCH_PASSWORD`

How to create those values is in `fastlane/testflight.md`. Put the secrets on this repository. Do not put them in the code.

Then, in the Apple developer site and App Store Connect:

1. Run **1. Validate Secrets** and choose the branch `custom`.
2. Run **2. Add Identifiers** and choose the branch `custom`. That creates the six bundle IDs in the table. It cannot create the App Group.
3. Register an App Group yourself: description `Loop Custom App Group`, identifier `group.com.TEAMID.loopkit.LoopCustomGroup`. Use your real team id, not the word TEAMID.
4. On each of these identifiers, open App Groups, choose **Loop Custom App Group**, and save:
   - Loop Custom
   - Loop Custom Intent Extension
   - Loop Custom Status Extension
   - Loop Custom Widget Extension
5. On **Loop Custom** only, turn on **Time Sensitive Notifications** if it is not already on, and save.
6. In App Store Connect, create a new iOS app:
   - Name: `Loop Custom` (the store name must be unique; the name on the phone is still the one line above)
   - Bundle ID: `com.TEAMID.loopkit.LoopCustom`
   - SKU: anything, for example `loop-custom`
7. In that app, open TestFlight and add an internal group for the people who should install it.

## Order to run workflows, after the secrets exist

Always choose the branch **`custom`** in the Run workflow menu. Choosing `main` builds the standard Loop app instead.

1. **1. Validate Secrets**
2. **2. Add Identifiers**
3. Do the App Group, Time Sensitive Notifications, and App Store Connect steps above.
4. **4. Build Loop** (the manual one). The first successful run creates the signing certificate and uploads Loop Custom to TestFlight.

You do not need to run **3. Create Certificates** first. The build does that. Leave the `ENABLE_NUKE_CERTS` variable off. Nuking certificates replaces the shared distribution certificate for the whole team, including a standard Loop build that uses the same Apple team.

This branch does not try to sync a `custom` branch from LoopKit, because that branch does not exist upstream. Updates from LoopKit `main` are not pulled in automatically. `main` on this fork is unchanged and still syncs the normal way.

## What was already checked without secrets

An unsigned iOS Simulator compile of this branch is in GitHub Actions (`Custom Loop simulator compile`). It does not sign the app and does not talk to Apple. The build log records the bundle id and the display name from the built app.
