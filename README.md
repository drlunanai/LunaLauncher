# Luna Launcher

A calm, ad-free home screen for **Android TV** and **Google TV**.

- **Background:** a photo that changes on its own. Choose:
  - Bing's photo of the day (all regions);
  - NASA images: galaxies and nebulae, Earth from space, planets and moons, and Earth Observatory's
    image of the day;
  - a folder on the TV or a USB drive;
  - an Apple shared album.

  Dimming is adjustable.
- **Top bar:** the clock, then one glass capsule with:
  - the weather; OK on it unrolls a 5-day forecast;
  - Wi-Fi, VPN, screensaver and Play Store buttons;
  - the settings gear.
- **Apps:** big banners, **most used first** (or A–Z, or your own order), in one or two rows. Hide the
  ones you never use.
- **Screensaver:** the same photos with a clock when the TV is idle.
- **Languages:** English and Croatian (*Settings → About → Language*), or automatically the TV's
  language.
- **Updates itself:** it checks this page once a day. *Settings → About → Install update* installs a
  new version, and your settings are kept.
- **Privacy:** no ads, no recommendations, no analytics. It only goes online for the photos, the
  weather (open-meteo.com, no account) and its own updates (this GitHub page).

## Download

**[Latest release](https://github.com/drlunanai/LunaLauncher/releases/latest)**: download
`Luna-Launcher-<version>.apk` to your computer.

You only need the APK for the first installation. After that, Luna Launcher updates itself.

## What you need

- A computer (Mac, Windows or Linux) on the **same network** as the TV.
- **adb** (Android platform-tools):
  - macOS: `brew install android-platform-tools`
  - Windows: download [platform-tools](https://developer.android.com/tools/releases/platform-tools),
    unzip it, and run the commands below from that folder (`adb.exe`).
  - Debian/Ubuntu: `sudo apt install adb`

Nothing here needs root. Everything can be undone (see [Undo](#undo)).

## 1. Turn on debugging on the TV

### Xiaomi Mi Box S (2nd gen, Google TV)

1. **Settings → System → About**: scroll to **Android TV OS build** and press OK **7 times**, until it
   says you're a developer.
2. **Settings → System → Developer options**: turn on **USB debugging**.
3. **Settings → Network & Internet** → your network: note the **IP address** (e.g. `192.168.1.50`).

On the older **Mi Box S (Android TV 9)** the same items are under **Settings → Device Preferences →
About → Build** and **Settings → Device Preferences → Developer options**.

### TCL TV (Google TV or Android TV)

1. Open the developer menu:
   - **Google TV models:** **Settings → System → About**: scroll to **Android TV OS build** and press OK
     **7 times**.
   - **Android TV models:** **Settings → Device Preferences → About**: scroll to **Build** and press OK
     **7 times**.
2. Turn on debugging in **Developer options** (under **System** on Google TV, **Device Preferences** on
   Android TV). Switch on **USB debugging**, and also **Network debugging** / **ADB over network** if
   your model lists it.
3. Note the TV's **IP address**:
   - **Google TV:** **Settings → Network & Internet** → your network.
   - **Android TV:** **Settings → Network & Internet** → your network, or **Network → Network status**.

## 2. Connect your computer

```bash
adb connect 192.168.1.50:5555
```

Use your TV's IP address. The TV asks **Allow USB debugging?**: tick **Always allow from this
computer** and choose **OK**. Then check:

```bash
adb devices
```

It should list your TV as `device`. If it says `unauthorized`, accept the prompt on the TV and run
`adb connect` again.

## 3. Install Luna Launcher

```bash
adb install Luna-Launcher-0.20.0.apk
```

Use the name of the file you downloaded. Then give it the permissions it needs. A normal app can't
grant these itself on a TV, so they're granted over adb:

```bash
adb shell appops set com.drlunanai.mitvlauncher GET_USAGE_STATS allow
adb shell appops set com.drlunanai.mitvlauncher REQUEST_INSTALL_PACKAGES allow
adb shell pm grant com.drlunanai.mitvlauncher android.permission.ACCESS_COARSE_LOCATION
adb shell pm grant com.drlunanai.mitvlauncher android.permission.ACCESS_FINE_LOCATION
```

What each one is for:
- **Usage access:** sorts apps by how much you use them.
- **Install apps:** lets the launcher install its own updates.
- **Location:** Android only shows the Wi-Fi network name to apps with location access. The launcher
  doesn't locate the TV or send anything.

## 4. Use the launcher's photos as the screensaver (optional)

Google TV doesn't list third-party screensavers in its settings, so choose it over adb:

```bash
adb shell settings get secure screensaver_components
```

Write down what this prints, so you can go back to it later. Then:

```bash
adb shell settings put secure screensaver_components com.drlunanai.mitvlauncher/.system.PhotoDreamService
adb shell settings put secure screensaver_enabled 1
```

## 5. Make it the home screen

The stock home screen is **disabled**, never uninstalled, so it can always be turned back on.

### Google TV (Mi Box S 2nd gen, TCL Google TV)

```bash
adb shell pm disable-user --user 0 com.google.android.apps.tv.launcherx
adb shell pm disable-user --user 0 com.google.android.tungsten.setupwraith
```

On Google TV the setup wizard also acts as a home screen once the stock one is off, so it's disabled
too.

### Android TV (Mi Box S Android 9, TCL Android TV)

```bash
adb shell pm disable-user --user 0 com.google.android.tvlauncher
```

### Check

Press **Home** on the remote: Luna Launcher should open. To check from the computer:

```bash
adb shell cmd package resolve-activity --brief -a android.intent.action.MAIN -c android.intent.category.HOME
```

The last line should be `com.drlunanai.mitvlauncher/.MainActivity`. If it names another app, disable
that one the same way (`adb shell pm disable-user --user 0 <package>`). Some TCL models have their own
home screen next to Google's. Don't disable anything else, such as the TV inputs app (Live TV / HDMI).

## Updates

Luna Launcher checks for a new version once a day. When one is out, a small orange dot appears on the
gear:
1. Open **Settings → About** and select **Install update**.
2. Confirm if Android asks. The launcher restarts on its own, with your settings.

If About says *Installing updates isn't allowed yet*, run the `REQUEST_INSTALL_PACKAGES` command from
step 3 again.

## Undo

Turn the stock home screen back on. On Google TV:

```bash
adb shell pm enable com.google.android.apps.tv.launcherx
adb shell pm enable com.google.android.tungsten.setupwraith
```

On Android TV:

```bash
adb shell pm enable com.google.android.tvlauncher
```

Put the screensaver back. Use the value you wrote down in step 4, or delete the setting to get the
default:

```bash
adb shell settings delete secure screensaver_components
```

Then remove Luna Launcher:

```bash
adb uninstall com.drlunanai.mitvlauncher
```

## Launcher updates

What changed in each version, newest first.

<!-- RELEASES: everything below is generated from the releases on every publish. -->

### 0.23.0 · 2026-10-31

New background source: NASA images (galaxies, Earth from space, planets, Earth Observatory). / Nova pozadina: NASA fotografije (galaksije, Zemlja iz svemira, planeti, Earth Observatory).

### 0.22.0 · 2026-10-31

The weather shows the country as its code (Zagreb, HR). / Vrijeme prikazuje državu kao kod (Zagreb, HR).

### 0.21.0 · 2026-10-31

Croatian translation, and a Language setting under About (Automatic, English, Hrvatski). / Hrvatski prijevod i postavka Jezik pod O aplikaciji.

### 0.20.0 · 2026-10-31

The settings now unroll inside the top bar, like the forecast, without any glitch.

### 0.19.0 · 2026-10-31

The weather forecast unrolls like the settings, and the settings open without a flicker.

### 0.18.0 · 2026-10-31

The launcher settings now unroll out of the top bar instead of sliding in from the right.

### 0.17.0 · 2026-10-31

Quieter top bar: the forecast opens with OK on the weather, no texts under the icons, and the gear opens the launcher settings (Android settings is now inside them).

### 0.16.0 · 2026-10-31

Clearer message on the About page when updates can't be installed.

### 0.15.0 · 2026-10-31

About page tidied up.

### 0.14.0 · 2026-10-31

Renamed to Luna Launcher. The launcher now checks for updates itself (Settings → About) and installs them while keeping your settings.

