# Prerequisites

Everything required for this can be installed through the
prereq\_android\_sdk\_install.sh script in e-mission-phone. The most important
packages are the emulator, platform-tools, and a system-image for your
architecture. Use `sdkmanager --list_installed` to check installed android
packages.

Add `$ANDROID_HOME/emulator` and `$ANDROID_HOME/platform-tools` to your `$PATH`
if they aren't already added.

# Setting Up the Target

Setup the android emulator or connect a phone over usb/wifi.

## Android Emulator

Note that the android emulator does not simulate the geofence so trips need to
be started manually in the developer options.

Create an Android Virtual Device:

```
avdmanager avd create \
-k 'system-images;android-34;google_apis_playstore;x86_64' \
-n <avd-name>
```

The default hardware profile can be used.

Make sure to replace x86_64 if you're on a different host architecture. Use
`sdkmanager --list` to see available architectures to install.

Start the emulator:

```
emulator -avd <avd-name>
```

`adb` should connect to the emulator automatically, `adb devices` to verify.

## Real Phone

Turn on developer options and enable USB debugging then plug the phone into your
computer.

# Loading the Test Application

Once `adb` is connected to the test phone you can use `adb install <path to apk>`
to install the test application.

# Java Debugger

Make sure developer options are enable (repeatedly tap the build number in
settings). Go to "Select debug app" in developer options and choose the test
app. This will start a jdwp port any time the app is launched. Run `adb jdwp` to
get the active jdwp port.

Forward the jdwp connection:

```
adb forward tcp:<unused-port> jdwp:<active-jdwp-port>
```

Anytime the app is relaunched it will create another jdwp connection so these
two commands need to be run again.

## CLI

```
jdb -attach localhost:<forwarded-port> -sourcepath <phone-repo>/platforms/android/app/src/main/java
```

[Tutorial for jdb](https://m.opnxng.com/@dmytro.ch/the-java-debugger-how-to-debug-java-without-ide-how-to-use-jdb-ef732d79f915)

## VSCode

Install the [Language Support for Java](https://marketplace.visualstudio.com/items?itemName=redhat.java) and the [Debugger for Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)
extensions.

Configure the `launch.json` file to attach to the forwarded jdwp port.

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "java",
            "name": "Current File",
            "request": "attach",
            "hostName": "localhost",
            "port": <forwarded-port>,
        }
    ]
}
```

Start the debugger from the "Run and Debug" tab.

## Emacs

Install [lsp-mode](https://emacs-lsp.github.io/lsp-mode/), [dap-mode](https://github.com/emacs-lsp/dap-mode), and [lsp-java](https://github.com/emacs-lsp/lsp-java).

Required config:

```elisp
(use-package lsp-mode
  :init
  (setq lsp-keymap-prefix "C-c l")
  :commands lsp)

(use-package dap-mode)

(use-package lsp-java
  :config (add-hook 'java-mode-hook 'lsp))
```

Navigate to a java file in the phone repo. Lsp-mode will ask to set the project
root, make sure to set it to the root of the java code
(`platforms/android/app/src/main/java`) and not the root of the phone repo.

Verify that `jdtls` is installed by running `lsp-install-servers` and selecting
`jdtls`

Run `dap-debug` and select "Java Attach" then enter the port for the jdwp
connection.

