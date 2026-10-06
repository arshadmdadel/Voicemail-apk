# Voicemail (Android APK): QA Testing Project

Manual QA project on the **Voicemail** Android app, completed as part of the SQA training program at the A1QA training center (QATC).

## Overview

Voicemail is an Android app that lets a user record personal **greetings** and **quick statuses** that are played to callers. Greetings can be assigned to specific contacts or groups and set to play during a chosen time window. The app offers a 14-day free trial and a purchase option. This repository documents the testing performed and the defects found in version **1.0**.

## Quality Assessment

| Test level | Result |
|------------|--------|
| Smoke test | **Unacceptable** |
| Acceptance test | **Low** |

The build does not pass the smoke test. Core features fail: greetings do not play correctly during calls, quick statuses cannot be deleted, and the trial and purchase flow does not work as intended.

## Features Under Test

- **Greetings**: record, save, list, turn on and off, edit and delete greetings.
- **Recipients and groups**: choose specific contacts or groups for a greeting, and create contact groups.
- **Schedule**: set From and To times for when a greeting plays.
- **Quick Status**: create and manage short status messages.
- **Recording**: record, pause, resume and stop audio, with a progress bar and timer.
- **Settings**: time before greeting, help page, purchase option.
- **Trial and purchase**: 14-day free trial, trial expiry banner and screen, purchase page.
- **Permissions**: contact permission handling.

## Scope of Testing

| Type | Focus |
|------|-------|
| Smoke and acceptance | Core flows: record, save, play and assign greetings during calls |
| Functional | Greeting playback, scheduling, recipients and groups, quick status, trial period, settings persistence |
| GUI | Header titles, labels and spelling, layout, toggle state, progress indicators |
| Negative / boundary | Whitespace-only titles, 400-character titles, 0-second recordings, same or reversed From and To times, denied permissions |

## Test Environment

| Item | Details |
|------|---------|
| Device | OnePlus 12R |
| OS | Android 16 |
| Application | Voicemail APK, version 1.0 |
| Evidence | Screenshots, screen recordings and Android logcat files attached to the tickets |
| Defect tracking | Jira (project: Training Center, component: Voicemail) |

## How to Install and Test on a Mobile Device

### Requirements

- An Android phone (tested on OnePlus 12R, Android 16).
- The Voicemail APK file (version 1.0) from the training center.
- Contacts on the phone, since greetings can be assigned to contacts and groups.
- A second phone or number to place test calls.

### Option 1: Install directly on the phone

1. Copy the APK file to the phone (USB cable, cloud drive or messaging app) or download it on the phone.
2. Open the file from the **Files** or **Downloads** app.
3. If Android blocks the install, tap **Settings** and turn on **Allow from this source** for the app you used to open the file, then go back.
4. Tap **Install** and wait for it to finish.
5. Tap **Open**.

### Option 2: Install from a computer with ADB

1. On the phone, open **Settings > About phone** and tap **Build number** seven times to enable Developer options.
2. Open **Settings > Developer options** and turn on **USB debugging**.
3. Connect the phone to the computer with a USB cable and accept the debugging prompt on the phone.
4. Check that the phone is detected:

   ```bash
   adb devices
   ```

5. Install the APK (use `-r` to reinstall over an older version):

   ```bash
   adb install -r Voicemail.apk
   ```

6. **If the install is blocked** with an error such as `INSTALL_FAILED_DEPRECATED_SDK_VERSION`, the APK targets an old Android SDK version, and newer Android versions (14 and above) refuse to install it. Bypass the block with:

   ```bash
   adb install --bypass-low-target-sdk-block Voicemail.apk
   ```

   Use this flag only for testing builds you trust. It works only over ADB, not when you install by tapping the file on the phone. Replace `Voicemail.apk` with the real file name (the package file name used by the training center).

### First launch

1. Allow the permissions the app asks for, such as contacts, microphone, phone and notifications.
2. On the welcome screen, choose the free trial or purchase option.
3. Create a greeting or quick status, then run the checks below.

### What to check

1. **Smoke test**: the app opens, a greeting can be recorded and saved, and it appears in the list.
2. **Greeting playback**: call the phone from a second number and confirm the caller hears the greeting.
3. **Recipients and groups**: assign a greeting to a contact and a group, then call from a selected and a non-selected number.
4. **Schedule**: set From and To times and test calls inside, outside and exactly at the boundaries.
5. **Quick Status**: create, play and delete a status.
6. **Trial period**: move the phone's date forward to check the trial expiry banner and screen.
7. **Permissions**: deny the contacts permission and check how the app behaves.

### Collecting logs for a defect report

1. Connect the phone with USB debugging on.
2. Clear old logs, reproduce the bug, then save the log:

   ```bash
   adb logcat -c
   adb logcat -d > Android-16_logcat.txt
   ```

3. Attach the log file, a screenshot or a screen recording to the Jira ticket.

### Uninstall

```bash
adb uninstall <package-name>
```

You can also uninstall from **Settings > Apps**.

> Only install APK files from a source you trust, such as the training center. Turn off **Allow from this source** again after the install.

## Defect Summary

**40 defects** were reported, all currently **Open** and all with Major priority.

| Severity | Count |
|---|---|
| 1-Critical | 4 |
| 2-Major | 16 |
| 3-Average | 12 |
| 4-Minor | 8 |

| Error type | Count |
|---|---|
| Functional | 28 |
| GUI | 12 |

### Defect categories

| Category | Examples |
|----------|----------|
| Greeting playback | Greeting plays on the recipient's own device instead of to the caller; keeps playing after the call is answered; does not play for an assigned contact; does not play exactly at the start or end time; several recordings can play at once |
| Quick Status | Status cannot be deleted after it is created; status plays on the user's phone instead of showing who is calling; radio buttons used instead of an on/off switch |
| Recording | Recording restarts from 0:00 after Pause; a 0-second recording can be saved; progress bar does not move while recording; label reads "Stoped" |
| Schedule and time | From and To can be the same, or From can be later than To; time picker uses 12-hour format but the value shows in 24-hour format; saved times are not shown when editing |
| Recipients and groups | Deselected contact still looks selected; blank space can be saved as a recipient; new group cannot be created; group contacts missing when the group is reused; groups not separated by source; layout broken for some contacts; endless loading when contact permission is denied |
| Title validation | Whitespace-only, empty or 400-character titles accepted; Save and Delete buttons not displayed properly with a very long title |
| Greetings list | ON/OFF toggle changes or sticks after scrolling with 12 or more greetings; no playback progress bar |
| Trial and purchase | Trial expiry banner and screen do not appear; "Try Voicemail FREE for 2 weeks" does not grant access; Purchase does not open the purchase page |
| Header and labels | Header shows "GET PERSONAL" instead of the page title; "Recepients" typo |
| Settings | "Time before greeting" always resets to 5; Help page does not explain Greetings and Quick Status |

## Defect Report Format

Each defect was logged in Jira with:

- Title and summary
- Steps to reproduce
- Actual result and expected result
- Environment (device, OS)
- Priority, severity, and error type (Functional / GUI)
- Screenshots, screen recordings and logcat files as attachments

## Skills Demonstrated

- Mobile (Android) functional and GUI testing
- Smoke and acceptance testing, with a quality assessment
- Boundary and negative testing
- Log collection with Android logcat
- Clear, reproducible bug reporting
- Defect tracking in Jira

## Author

**Arshad Md. Adel**: SQA trainee, CSE graduate of United International University (UIU).
