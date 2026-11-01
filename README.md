# Luna Launcher

A calm, ad-free home screen for **Android TV** and **Google TV**.

- **Background:** a photo that changes on its own. Choose:
  - Bing's photo of the day (all regions);
  - The Met: a new set of 30+ public-domain paintings and prints every day from The Metropolitan
    Museum of Art (Open Access, CC0);
  - a folder on the TV or a USB drive;
  - an Apple shared album.

  Dimming is adjustable, and an optional slow pan and zoom makes it a "living photo".
- **Clock:** standard, large minimal, or analogue (the hour in a ring that fills with the minutes).
- **Top bar:** the clock, then one glass capsule with:
  - the weather; OK on it unrolls a 5-day forecast;
  - a red Wi-Fi-off icon, only while there's no internet;
  - light scenes: your IKEA Home scenes as tiles plus the room temperature, straight from a DIRIGERA
    hub on your network (*Settings → Advanced → IKEA Home*);
  - screensaver and Play Store buttons;
  - the settings gear.
- **Apps:** big banners, **most used first** (or A–Z, or your own order), in one or two rows. Hide the
  ones you never use.
- **Screensaver:** the same photos with a clock when the TV is idle.
- **Languages:** English and Croatian (*Settings → About → Language*), or automatically the TV's
  language.
- **Updates itself:** it checks this page once a day. *Settings → About → Install update* installs a
  new version, and your settings are kept.
- **Privacy:** no ads, no recommendations, no analytics. It only goes online for the photos, the
  weather (open-meteo.com, no account) and its own updates (this GitHub page). An IKEA hub is reached
  only on your own network.

## Download

**[Latest release](https://github.com/drlunanai/LunaLauncher/releases/latest)**: download
`Luna-Launcher-<version>.apk` to your computer.

You only need the APK for the first installation. After that, Luna Launcher updates itself.

**Had Luna Launcher before 1.0.0?** From 1.0.0 on, Luna Launcher is a new app
(`com.drlunanai.luna`), and the old one (`com.drlunanai.mitvlauncher`) no longer gets updates from
this page. Install the new one as below, then remove the old one:

```bash
adb uninstall com.drlunanai.mitvlauncher
```

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
adb install Luna-Launcher-1.0.0.apk
```

Use the name of the file you downloaded. Then give it the permissions it needs. A normal app can't
grant these itself on a TV, so they're granted over adb:

```bash
adb shell appops set com.drlunanai.luna GET_USAGE_STATS allow
adb shell appops set com.drlunanai.luna REQUEST_INSTALL_PACKAGES allow
adb shell pm grant com.drlunanai.luna android.permission.ACCESS_COARSE_LOCATION
adb shell pm grant com.drlunanai.luna android.permission.ACCESS_FINE_LOCATION
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
adb shell settings put secure screensaver_components com.drlunanai.luna/com.drlunanai.mitvlauncher.system.PhotoDreamService
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

The last line should be `com.drlunanai.luna/com.drlunanai.mitvlauncher.MainActivity`. If it names another app, disable
that one the same way (`adb shell pm disable-user --user 0 <package>`). Some TCL models have their own
home screen next to Google's. Don't disable anything else, such as the TV inputs app (Live TV / HDMI).

## Updates

Luna Launcher checks for a new version once a day. When one is out, a small orange dot appears on the
gear:
1. Open **Settings → About** and select **Install update**.
2. Confirm if Android asks. The launcher shows *Updating Luna Launcher*, closes for a moment while
   Android replaces it, and opens again by itself, with your settings.

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
adb uninstall com.drlunanai.luna
```

## Launcher updates

What changed in each version, newest first.

<!-- RELEASES: everything below is generated from the releases on every publish. -->

### 1.0.0 · 2026-11-01

Luna Launcher 1.0.0, the first release of the new app (com.drlunanai.luna): a calm, ad-free home screen with daily photos, weather, IKEA Home scenes and a photo screensaver. Had the old Luna Launcher? Install this one and remove the old one (see Download).

Luna Launcher 1.0.0, prvo izdanje nove aplikacije (com.drlunanai.luna): miran početni zaslon bez reklama, s dnevnim fotografijama, vremenskom prognozom, IKEA Home scenama i fotografskim čuvarom zaslona. Imali ste stari Luna Launcher? Instalirajte ovaj i uklonite stari (vidi Download).

### 0.41.0 · 2026-11-01

Moves this TV to the Family edition, with all your settings. Afterwards, Settings → About shows how to keep receiving updates.

Prebacuje ovaj TV na obiteljsko izdanje, uz sve vaše postavke. Nakon toga Postavke → O aplikaciji pokazuju kako i dalje primati ažuriranja.

### 0.40.0 · 2026-11-01

NewsReader: the article list now slides aside smoothly when you open an article. Article text is a bit smaller so more fits on screen, and pictures are fitted to the screen height, always shown whole.

NewsReader: popis članaka sada se glatko pomiče u stranu kad otvorite članak. Tekst članka je malo manji pa više stane na zaslon, a slike su prilagođene visini zaslona i uvijek prikazane cijele.

### 0.39.0 · 2026-11-01

NewsReader: Home now closes the reader. The section buttons are gone. While you read an article the list steps aside so the article fills the panel; Left or Back brings the list back.

NewsReader: tipka Home sada zatvara čitač. Gumbi rubrika su uklonjeni. Dok čitate članak, popis se sklanja pa članak ispunjava cijeli panel; tipka lijevo ili Natrag vraća popis.

### 0.38.0 · 2026-11-01

NewsReader: the News button now shows the newest articles from your NewsReader server, without ads, and opens a full reader with your sources, sections and read marks (the same as on your phone and Mac). Link the TV with a QR code in Settings → Advanced → NewsReader.

NewsReader: gumb Vijesti sada prikazuje najnovije članke s vašeg NewsReader poslužitelja, bez oglasa, i otvara čitač s vašim izvorima, rubrikama i pročitanim člancima (isto kao na mobitelu i Macu). TV povežite QR kodom u Postavke → Napredno → NewsReader.

### 0.37.1 · 2026-11-01

A small update to try the new update flow: you'll see "Updating Luna Launcher", and the launcher opens again by itself.

Malo ažuriranje za isprobavanje novog načina ažuriranja: vidjet ćete „Ažuriranje Luna Launchera“, a launcher će se sam ponovno otvoriti.

### 0.37.0 · 2026-11-01

Updates are smoother: while an update installs, the launcher shows "Updating Luna Launcher", and afterwards it opens again by itself. This works from the next update on.

Ažuriranja su glatka: dok se ažuriranje instalira, launcher prikazuje „Ažuriranje Luna Launchera“, a nakon toga se sam ponovno otvara. To vrijedi od sljedećeg ažuriranja.

### 0.36.0 · 2026-11-01

Apps change to the next screen or row much faster, at constant speed.

Aplikacije sada puno brže prelaze na sljedeći zaslon ili red, ujednačenom brzinom.

### 0.35.0 · 2026-11-01

Single row of apps: moving past the last app on screen slides in the whole next screen again. The last screen starts with its first app, like every other screen.

Jedan red aplikacija: prelazak preko zadnje aplikacije na zaslonu ponovno uvodi cijeli sljedeći zaslon. Zadnji zaslon počinje svojom prvom aplikacijom, kao i svaki drugi.

### 0.34.0 · 2026-10-31

The selected app now has a thinner white border with a soft glow. Apps slide smoothly to the next screen or row instead of jumping.

Odabrana aplikacija sada ima tanji bijeli okvir s blagim sjajem. Aplikacije glatko klize na sljedeći zaslon ili red umjesto da skoče.

### 0.33.0 · 2026-10-31

The Met: artworks are now cropped to fill the screen and stored at screen size, so they take far less space and memory, and the launcher runs smoother. Your current set is converted once, without downloading it again. Settings show how much space the day's set takes.

The Met: umjetnine se sada obrezuju da ispune zaslon i spremaju u veličini zaslona, pa zauzimaju puno manje prostora i memorije, a launcher radi glatkije. Trenutni izbor se pretvara jednom, bez ponovnog preuzimanja. Postavke prikazuju koliko prostora zauzima dnevni izbor.

### 0.32.0 · 2026-10-31

IKEA scenes are now pills like the settings, and the scene that ran last is marked. Up closes the settings, which no longer dim the screen. The selected app has a white border, and a single row of apps moves a whole screen at a time. The Met caption in the screensaver is lighter and smaller. Release notes now appear in the launcher's language.

IKEA scene su sada pilule kao u postavkama, a posljednja pokrenuta scena je označena. Tipka gore zatvara postavke, koje više ne zatamnjuju zaslon. Odabrana aplikacija ima bijeli okvir, a jedan red aplikacija pomiče se za cijeli zaslon. Opis umjetnine na čuvaru zaslona je prozirniji i manji. Bilješke o izdanju sada se prikazuju na jeziku launchera.

### 0.31.0 · 2026-10-31

The Met: if the museum has fewer artworks than you asked for, the ones found are now shown instead of none. Preparing the daily set is much faster and uses far less data and battery of the box, and settings show its progress.

The Met: ako muzej ima manje umjetnina nego što ste tražili, sada se prikazuju one pronađene umjesto nijedne. Priprema dnevnog izbora je puno brža i troši znatno manje podataka i snage uređaja, a postavke prikazuju napredak.

### 0.30.0 · 2026-10-31

IKEA Home: the bulb in the top bar now shows your IKEA Home scenes as tiles, with the room temperature from an IKEA sensor. Pair your DIRIGERA hub in Settings → Advanced → IKEA Home (press the button on the bottom of the hub). Everything stays on your home network.

IKEA Home: žarulja u gornjoj traci sada prikazuje vaše IKEA Home scene kao pločice, uz temperaturu sobe s IKEA senzora. Uparite čvorište DIRIGERA u Postavke → Napredno → IKEA Home (pritisnite gumb na dnu čvorišta). Sve ostaje u vašoj kućnoj mreži.

### 0.29.0 · 2026-10-31

Top bar: the Wi-Fi and VPN buttons are gone. A red Wi-Fi icon appears only when there's no internet (OK opens Wi-Fi settings). New Light scenes and News buttons for features coming soon.

Gornja traka: gumbi za Wi-Fi i VPN su uklonjeni. Crvena ikona Wi-Fi pojavljuje se samo kad nema interneta (OK otvara postavke Wi-Fi mreže). Novi gumbi Scene svjetla i Vijesti za značajke koje uskoro stižu.

### 0.28.0 · 2026-10-31

New settings layout: six category pills, a path on every page, and the menu resizes to fit. / Nove postavke: šest kategorija, putanja na svakoj stranici i izbornik prilagođen sadržaju.

### 0.27.0 · 2026-10-31

New background source: The Met, a fresh set of 30+ public-domain artworks every day. / Nova pozadina: The Met, svaki dan novi izbor od 30+ umjetnina u javnom vlasništvu.

### 0.26.0 · 2026-10-31

NASA images removed; a NASA background switches to Bing. / Uklonjene NASA fotografije; NASA pozadina prelazi na Bing.

### 0.25.0 · 2026-10-31

New Analogue clock (the hour in a ring that fills with the minutes), left-aligned Large minimal, Stacked removed. / Novi analogni sat (sat u krugu koji se puni minutama), poravnati Veliki minimalni, uklonjen Složeni.

### 0.24.0 · 2026-10-31

Clock styles (Standard, Large minimal, Analogue, Stacked) and optional slow photo motion. / Izgled sata (Standardni, Veliki minimalni, Analogni, Složeni) i pokret fotografije.

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

