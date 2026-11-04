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
  - light scenes: your IKEA Home or Philips Hue scenes as tiles plus the room temperature, straight
    from a DIRIGERA hub or a Hue bridge on your network (*Settings → Advanced*; one at a time);
  - screensaver and Play Store buttons;
  - the settings gear.
- **Apps:** big banners, **most used first** (or A–Z, or your own order), in one or two rows. Hide the
  ones you never use.
- **Screensaver:** the same photos with a clock when the TV is idle.
- **Remote button:** choose a remote button you don't use to open the top-bar capsule over any app
  (*Settings → Advanced → Remote button*). It uses a key-only accessibility service that you switch
  on yourself; it never sees the screen.
- **Languages:** English and Croatian (*Settings → About → Language*), or automatically the TV's
  language.
- **Sets itself up:** no computer needed. A step-by-step popup makes it the home screen and the
  screensaver, and repairs that if a TV update undoes it.
- **Updates itself:** it checks this page once a day. *Settings → About → Install update* installs a
  new version, without asking once the TV is set up, and your settings are kept.
- **Privacy:** no ads, no recommendations, no analytics. It only goes online for the photos, the
  weather (open-meteo.com, no account) and its own updates (this GitHub page). An IKEA hub or a Hue
  bridge is reached only on your own network.

## Download

**[Latest release](https://github.com/drlunanai/LunaLauncher/releases/latest)**: the file
`Luna-Launcher-<version>.apk`.

You only need it for the first installation. After that, Luna Launcher updates itself.

**Had Luna Launcher before 1.0.0?** From 1.0.0 on, Luna Launcher is a new app
(`com.drlunanai.luna`), and the old one (`com.drlunanai.mitvlauncher`) no longer gets updates from
this page. Install the new one as below, then remove the old one in **Settings → Apps** (or with
`adb uninstall com.drlunanai.mitvlauncher`).

## Install (no computer needed, from 1.7.0)

### 1. Get the APK onto the TV

Either way, Android first asks you to allow installing apps from that source: allow it for the app
you use.

- **A downloader app:** install one from Google Play on the TV (for example *Downloader*), open
  `github.com/drlunanai/LunaLauncher/releases/latest` in it, and select `Luna-Launcher-<version>.apk`.
- **A USB drive:** copy the APK onto it, plug it into the TV, and open it with a file manager app
  (for example *File Commander* or *X-plore* from Google Play).

### 2. Open Luna Launcher and follow the setup

Open **Luna Launcher** from the TV's app list. It offers **Set up this TV**, step by step:

1. **Turn on USB debugging.** The popup shows the steps for your TV:
   - **Google TV** (Mi Box S 2nd gen, TCL Google TV): **Settings → System → About**, press OK on
     **Android TV OS build** 7 times until it says you're a developer, then **Settings → System →
     Developer options → USB debugging**.
   - **Android TV** (Mi Box S Android 9, TCL Android TV): the same under **Settings → Device
     Preferences**, with **Build** instead.
   - **TCL TVs:** also switch on **Network debugging** / **ADB over network** if your model lists it.

   Come back with **Back** and select **Next**.
2. **Allow Luna.** The TV asks **Allow USB debugging?**: tick **Always allow from this computer** and
   choose **OK**. (It says "computer", but it's Luna asking, from the TV itself.)
3. **The checklist** ticks itself off:
   - Luna becomes the **home screen** (the stock one is turned off, never uninstalled);
   - Luna's photos become the **screensaver**;
   - **updates** install by themselves, without asking;
   - **usage access**, so apps are sorted by how much you use them;
   - **location**, only because Android shows the Wi-Fi network name only to apps with it (the
     launcher doesn't locate the TV or send anything);
   - the **remote button** service is switched on (it does nothing until you choose a button).

Press **Home**: Luna Launcher opens. *Not now* skips the setup; you can run it later from
**Settings → Advanced → TV access → Set up this TV**. USB debugging can stay on; Luna needs it for
updates without asking and to repair the setup.

Nothing here needs root, and everything can be undone (see [Undo](#undo)).

## Settings → Advanced → TV access

- Each item of the checklist, **On** or **Off**.
- **Fix now** turns on whatever is off. Luna also does this by itself at every start, for example
  after a TV update brought the stock home screen back.
- **Go back to the previous launcher**: see [Undo](#undo).

## Install with a computer instead

If you'd rather use adb (Android platform-tools) on a computer on the same network:

1. Turn on **USB debugging** as in step 2.1 above, and note the TV's **IP address** (**Settings →
   Network & Internet** → your network).
2. Connect and install:

   ```bash
   adb connect 192.168.1.50:5555
   ```

   ```bash
   adb install Luna-Launcher-1.7.0.apk
   ```

   Use your TV's IP address and the file you downloaded. The TV asks **Allow USB debugging?** for
   the computer: tick **Always allow** and choose **OK**.
3. Open Luna Launcher on the TV and run **Set up this TV** as above (it asks to allow Luna too).

## Updates

Luna Launcher checks for a new version once a day. When one is out, a small orange dot appears on the
gear. Open **Settings → About** and select **Install update**:
- **With TV access set up**, it installs by itself: the screen shows *Updating Luna Launcher* until
  the launcher is back, with your settings. A tiny helper app, **Luna Updater**, keeps that screen
  up while Android replaces the launcher; Luna installs it by itself.
- **Without it**, Android asks you to confirm, and About says so. If About says *Installing updates
  isn't allowed yet*, run **Set up this TV**.

## Undo

**Settings → Advanced → TV access → Go back to the previous launcher** turns the stock home screen
back on, makes it the home screen again, restores the screensaver you had before, and switches off
Luna's remote-button service. Luna stays installed; remove it in **Settings → Apps → Luna Launcher →
Uninstall**, and Luna Updater the same way.

You can also turn USB debugging off again in **Developer options** once Luna is removed.

**Set it up with adb before 1.7.0?** *Go back* needs TV access, so either run **Set up this TV**
first, or undo it from the computer: turn the stock home screen back on (Google TV:
`com.google.android.apps.tv.launcherx` and `com.google.android.tungsten.setupwraith`; Android TV:
`com.google.android.tvlauncher`), and reset the screensaver:

```bash
adb shell pm enable com.google.android.apps.tv.launcherx
```

```bash
adb shell settings delete secure screensaver_components
```

## Launcher updates

What changed in each version, newest first.

<!-- RELEASES: everything below is generated from the releases on every publish. -->

### 1.7.0 · 2026-11-03

Set up this TV from the launcher, without a computer: a step-by-step popup (offered once) has you turn on USB debugging, then makes Luna the Home app, sets its screensaver, lets updates install without asking, and more. Settings → Advanced → TV access shows each item, fixes what's off and can go back to the previous launcher. With TV access, updates install by themselves behind a calm Updating screen. New focus look (brighter glass instead of a white bubble), app tiles that just grow when selected, and a Banners setting: colour or monochrome light/dark. New weather icons for every kind of weather; the weather refreshes every 5 minutes and Today shows the rest of the day. Calmer start: online requests go one after another, and photos aren't fetched again after every restart.

Postavljanje TV-a iz pokretača, bez računala: skočni prozor korak po korak (nudi se jednom) vodi vas kroz uključivanje USB otklanjanja pogrešaka, a zatim postavlja Lunu kao početnu aplikaciju, njezin čuvar zaslona, ažuriranja bez pitanja i još ponešto. Postavke → Napredno → Pristup TV-u prikazuje svaku stavku, popravlja isključeno i može vratiti prethodni pokretač. S pristupom TV-u ažuriranja se instaliraju sama, uz miran zaslon Ažuriranje. Novi izgled odabira (svjetlije staklo umjesto bijelog mjehurića), aplikacije se pri odabiru samo povećaju, i postavka Banneri: u boji ili jednobojni svijetli/tamni. Nove ikone za svaku vrstu vremena; vrijeme se osvježava svakih 5 minuta, a Danas prikazuje ostatak dana. Mirnije pokretanje: zahtjevi na mrežu idu jedan za drugim, a fotografije se ne preuzimaju ponovno nakon svakog pokretanja.

### 1.3.0 · 2026-11-02

Remote button: pick a remote button you don't use to open the top-bar capsule (weather, scenes, quick buttons) over any app. Settings → Advanced → Remote button; switch it on once in Android's Accessibility settings. It only sees which button is pressed, never the screen.

Gumb daljinskog: odaberite gumb na daljinskom koji ne koristite da otvori kapsulu iz gornje trake (vrijeme, scene, brzi gumbi) iznad bilo koje aplikacije. Postavke → Napredno → Gumb daljinskog; jednom ga uključite u postavkama Pristupačnosti. Vidi samo koji je gumb pritisnut, nikad zaslon.

### 1.2.0 · 2026-11-02

New look: the launcher now uses the Plus Jakarta Sans typeface, and the smart home button in the top bar has a new icon (a house with sparkles).

Novi izgled: launcher sada koristi pismo Plus Jakarta Sans, a gumb pametnog doma u gornjoj traci ima novu ikonu (kuća s iskricama).

### 1.1.0 · 2026-11-01

Philips Hue: your Hue scenes and the temperature from a Hue motion sensor in the top bar, paired with the button on the bridge (Settings → Advanced → Philips Hue). One smart home at a time: turning Hue on turns IKEA Home off; an IKEA hub you already paired stays as it is.

Philips Hue: vaše Hue scene i temperatura s Hue senzora pokreta u gornjoj traci, uparivanje gumbom na mostu (Postavke → Napredno → Philips Hue). Jedan pametni dom odjednom: uključivanje Huea isključuje IKEA Home; već upareno IKEA čvorište ostaje kakvo jest.

### 1.0.0 · 2026-11-01

Luna Launcher 1.0.0, the first release of the new app (com.drlunanai.luna): a calm, ad-free home screen with daily photos, weather, IKEA Home scenes and a photo screensaver. Had the old Luna Launcher? Install this one and remove the old one (see Download).

Luna Launcher 1.0.0, prvo izdanje nove aplikacije (com.drlunanai.luna): miran početni zaslon bez reklama, s dnevnim fotografijama, vremenskom prognozom, IKEA Home scenama i fotografskim čuvarom zaslona. Imali ste stari Luna Launcher? Instalirajte ovaj i uklonite stari (vidi Download).

