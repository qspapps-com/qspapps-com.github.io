# Fotoloka: Local LAN Photo Backup & Management Service

Fotoloka is a self-hosted, local-network-first photo backup and management solution. It transforms your Mac or Windows laptop into a central, secure repository for your family's photos—completely bypassing costly cloud subscriptions.

---

## 🌟 Key Features

1. **Auto-Discovery (mDNS/Bonjour)**: No need to copy or type IP addresses. The Android app automatically resolves your laptop's address on your home Wi-Fi using dynamic network scanning.
2. **Wi-Fi SSID Lockdown**: Background backup *and* server discovery run only while you are on your configured "Home Wi-Fi" network, preserving battery when you are out. A device that cannot report its network name is allowed to proceed rather than being locked out.
3. **On-the-Fly HEIC Conversion**: The Android client converts HEIC/HEIF images to high-quality JPEGs before uploading, keeping the server free of native image libraries. EXIF metadata — capture date, GPS, camera model — is carried across, so auto-tagging and the place shown on a photo's metadata sheet still work for iPhone photos.
4. **Chronological GalleryTimeline**: The app launches immediately into a continuous chronological grid of photos (sorted by `capture_time DESC`) with date group headers.
5. **Hierarchical Filter**: Narrow the gallery from the filter icon in its top bar, via an accordion date drill-down (**Year ➔ Month ➔ Day**) alongside custom tag chips and a photos/videos media-type filter. The icon fills in while a filter is active, so the gallery says whether you are seeing everything.
6. **Collaborative Trip Albums**: Create custom shared albums that cut across multiple users. Any family member can select their own photos and add them to any shared album on the home network (marked with creator indicators, e.g. *"By Mom"*). Tapping the album displays a unified chronologically sorted timeline of photos contributed by all participants.
7. **Manual "Free Up Space" Utility**: Reclaim phone storage by removing local copies of photos the **server confirms** it holds. The check is a live query against the laptop, never a guess from the app's own cache, so it will not delete your only copy of a file.
8. **Automated Server Indexing**: Connect an external hard disk to your laptop and directly copy folders into the `import/<profile name>/` directory. The server scans, extracts EXIF, organizes, generates thumbnails, and indexes them dynamically.
9. **Native LAN Video Support**: Fully back up, synchronize, and stream video files (.mp4, .mov, .mkv, .avi, .3gp, .webm) across your home network. Features standard JVM AWT play-button thumbnail placeholders (avoiding heavy ffmpeg native binaries and crashes on cheap hosts like Raspberry Pi) and zero-latency playback using Android's native `VideoView` and `MediaController` seek controls. Clips
play in the browser client too, in your browser's own video player.
10. **Trash with 30-Day Retention**: Deleting a photo moves it to Trash rather than destroying it. Items show a countdown and can be restored at any point; after 30 days the server purges them permanently. Deleting from the server never touches the copy on your phone.
11. **Recent Activity & Audit Trail**: Every write operation (uploads, deletions, tags, album changes) is logged centrally on the server with the operator's name, a description, and a timestamp. The **Recent Activity** screen shows them chronologically with colour-coded icons.
12. **Optional Password Protection**: Set a password on the server and every client must present it. The app prompts for it when the server asks, and verifies a password before saving it.
13. **Slideshow**: Hit play in the gallery's top bar and whatever you're looking at becomes a full-screen slideshow — your whole library, or just the tag, year or album you've narrowed it to. Albums can also be played straight from the album list. Every photo appears once before any repeats, then the order reshuffles. Adjustable speed (3/5/8/15s), skip and pause, and the screen stays awake while it plays. **Pause on a photo** and you get the same actions as the full-screen viewer — details, add a tag, share, download, remove from album, move to trash — with trashed or removed photos dropping out of the running show. It covers the *whole* feed on the server, not just what your phone has synced, so a shared album plays everyone's photos — and if the server is unreachable or too old, it plays what your phone has cached and tells you that's what it's doing. Note that videos are skipped: a slideshow runs on a timer and a clip does not. Built for real libraries: a 53,000-photo feed starts in one request, playback prefers any copy already on your phone, and it will not evict the cached thumbnails that let the gallery work offline.
14. **Use It From Any Browser — No App Needed**: Open `http://<laptop-ip>:8080/app/` on any computer, tablet or phone and you get the whole of Fotoloka: browse and filter the library, play a slideshow on a big screen, watch your videos, upload photos, tag and favourite them, build albums, empty the trash, and add or rename family members. **This is a complete client, not a preview** — a household with iPhones, or one that would simply rather not install anything, can run Fotoloka entirely this way. See [Using It From a Browser](#-3-using-it-from-a-browser).
15. **Watch Albums on the TV**: Open `http://<laptop-ip>:8080/tv/` in your Smart TV's own browser and the family's shared albums appear on the big screen — no app to install on the set. Drive it entirely with the remote's arrow keys, and play any album — or a random mix of every album — as a full-screen slideshow with crossfades, captions naming the album, date and contributor, adjustable speed and shuffle. Videos play inline. **Off by default**; see [Watching on a Smart TV](#-watching-on-a-smart-tv) to switch it on and to understand what it exposes.

---

## 💻 1. Laptop Server Setup (macOS & Windows)

The laptop server requires a standard Java Runtime (JRE 21+) installed on the host. 

### 🍏 Option A: macOS Installation
We have provided an automated background launcher (`launchd` agent) setup script:
1. Open Terminal and navigate to the project directory:
   ```bash
   cd server
   ```
2. Make the installer executable and run it:
   ```bash
   chmod +x scripts/install-mac.sh
   ./scripts/install-mac.sh
   ```
3. **What this does**: 
   - Compiles the server into an executable shadow fat JAR.
   - Copies the JAR to `~/Applications/FamilyPhotoBackup/`.
   - Generates a custom `com.qspapps.photobackup.plist` pointing to your user account and registers it inside `~/Library/LaunchAgents/` to autostart on user login.
4. **Status & Logs**:
   - Verify logs at: `~/Library/Logs/FamilyPhotoBackup.log`
   - Backed-up photos are stored at: `~/Pictures/FamilyPhotoBackup/`

#### 🗑️ How to Uninstall on macOS:
To stop the background service and completely prevent it from starting on boot or when external disks mount:
1. Run the uninstaller script:
   ```bash
   ./scripts/uninstall-mac.sh
   ```
   *(Or run manually)*:
   ```bash
   launchctl unload ~/Library/LaunchAgents/com.qspapps.photobackup.plist 2>/dev/null || true
   rm -f ~/Library/LaunchAgents/com.qspapps.photobackup.plist
   rm -rf ~/Applications/FamilyPhotoBackup
   ```
*(Note: Your actual backed-up photo library in `~/Pictures/FamilyPhotoBackup` or on your external drive is left intact).*

---

## ⚙️ Configuration

All server settings live in one file, `photobackup.properties`. Edit it, restart the server, done —
there is nothing to export on the command line.

The installers put it next to the program and print the exact path when they finish:

| Platform | Location |
| --- | --- |
| macOS | `~/Applications/FamilyPhotoBackup/photobackup.properties` |
| Windows | `%APPDATA%\FamilyPhotoBackup\photobackup.properties` |

It arrives fully commented, with every option explained in place. Uncomment what you need:

```properties
# Where your photos are kept. Point it at an external drive to keep them off the laptop.
storage.root = /Volumes/FamilyDrive/PhotoLibrary

# A password every phone must present. Leave it out for no password.
server.password = choose-something-memorable

# Serve albums to a Smart TV browser. Read "Watching on a Smart TV" below first.
tv.enabled = true
```

**Restart the server after editing** — the file is read once, at startup:

```bash
# macOS
launchctl unload ~/Library/LaunchAgents/com.qspapps.photobackup.plist
launchctl load   ~/Library/LaunchAgents/com.qspapps.photobackup.plist
```

On Windows, just run `FamilyPhotoBackup.bat` from your Startup folder again.

### Checking it took effect

The startup log says which file it read and what it decided:

```
[Config] Loaded /Users/you/Applications/FamilyPhotoBackup/photobackup.properties
[Config] storage.root=/Volumes/FamilyDrive/PhotoLibrary (configured), server.password=set, tv.enabled=true
```

If a change does not seem to have applied, look there first. Logs are at
`~/Library/Logs/FamilyPhotoBackup.log` on macOS, `%APPDATA%\FamilyPhotoBackup\server_log.log` on
Windows. Visiting `http://<your-laptop-ip>:8080/` also shows the storage path and whether the TV page
is on.

### If you set a password

Keep the file to yourself — it is now a secret:

```bash
chmod 600 ~/Applications/FamilyPhotoBackup/photobackup.properties
```

The server warns you at startup if it holds a password and other accounts on the machine can read it.

### Environment variables still work

`PHOTO_BACKUP_ROOT`, `PHOTO_BACKUP_PASSWORD` and `PHOTO_BACKUP_TV` are still honoured and **take
priority over the file**, so an older setup that exports them keeps working unchanged. That also makes
one-off runs easy without touching your config:

```bash
PHOTO_BACKUP_TV=true java -jar photo-backup-server-all.jar
```

If you want the file to be in charge, remove those variables from wherever you set them.

`PHOTO_BACKUP_CONFIG=/path/to/file.properties` points the server at a config file anywhere you like.

---

## 📺 Watching on a Smart TV

Turn the laptop into a photo frame for the living room. The server can serve a small web page that any
Smart TV browser can open — Samsung, LG, Android TV, a Fire Stick's browser, or just a laptop plugged
into the HDMI port.

### Switching it on

The TV page is **off by default**. Open your `photobackup.properties` (see
[Configuration](#-configuration)), uncomment this line, and restart the server:

```properties
tv.enabled = true
```

Then, on the television, open:

```
http://<your-laptop-ip>:8080/tv/
```

Most sets let you bookmark that as the browser's home page, so the family only ever has to open the
browser. Visiting `http://<your-laptop-ip>:8080/` will tell you whether the page is enabled — look for
`"tvViewer"`.

### Using the remote

| Button | Album list & album view | Slideshow |
| --- | --- | --- |
| Arrows | Move the highlight | ◀ ▶ skip · ▲ caption on/off · ▼ change speed |
| OK | Open the album / play from here | Pause and resume |
| Back | Go up a level | Leave the slideshow |
| Red (or `r`) | Start the shuffle of every album | Shuffle on/off |

The album list leads with **Shuffle all albums** — random photos from every album at once, for when
you just want the photos on rather than a particular album. The Red button starts it from anywhere on
that list.

The slideshow advances on its own (3, 5, 8 or 15 seconds a photo), crossfades between slides, and
loops when it reaches the end, so it can be left running all evening.
Photos are never cropped, and the space a photo does not fill is a softly blurred wash of the photo
itself rather than a black bar. Each slide is captioned in the top corner with the album, the date and
who added it — *Goa: on 1 May 2024 by mom* — which ▲ turns off.
Videos play with sound and the show moves on when the clip finishes. Your speed, shuffle and caption
choices are remembered on that television.

### What this exposes — please read

This page is **not password protected**. That is deliberate: a TV remote is a miserable way to type a
password. It is bounded instead:

- **Albums only.** The TV shows the albums your family has created, and nothing else. There is no
  browsing of the whole library by date, person, tag or place. **A photo that is not in an album cannot
  be reached from the TV at all**, even by someone who knows its exact filename. If you want something
  on the television, put it in an album; if you do not, leave it out of one.
- **Read-only.** Nothing on the TV page can delete, change, or upload anything.
- **Off unless you turn it on**, and turning it on does not weaken the phone app's API — that stays
  behind `server.password` if you have set one.

The practical effect: while it is enabled, anyone on your home Wi-Fi can view your shared albums
without a password. On a household network that is the whole point. If guests share your Wi-Fi and that
concerns you, leave it off, or put it on a guest-isolated network.

---

### Windows Option B: Windows Installation
We have provided an automated launcher shortcut setup script:
1. Open Command Prompt and navigate to the project directory:
   ```cmd
   cd server
   ```
2. Run the installer script:
   ```cmd
   scripts\install-windows.bat
   ```
3. **What this does**:
   - Compiles the server using `gradlew.bat`.
   - Copies the shadow JAR to `%APPDATA%\FamilyPhotoBackup\`.
   - Places a launcher `FamilyPhotoBackup.bat` into your Windows **Startup folder** so that the server launches automatically in the background when you log in.
4. **Status & Logs**:
   - Logs are redirected to: `%APPDATA%\FamilyPhotoBackup\server_log.log`
   - Backed-up photos are stored at: `%USERPROFILE%\Pictures\FamilyPhotoBackup\`

---

## 📱 2. Android Client App Usage

Once the server is running on your Mac/Windows laptop:
1. Open the Android application while connected to your Home Wi-Fi network.
2. The app uses `NsdManager` to **automatically discover** the server. A green dot in the top bar means connected; grey means not. Tap it to retry.
3. **Getting around**: every screen is reached from the **hamburger menu** in the top bar — Gallery, Albums, Recent Activity, Trash, and Sync Settings. There is deliberately no bottom bar: in a photo browser those pixels are better spent on photos. Filtering is the exception: it acts on the gallery, so it sits on the **filter icon** in the gallery's own top bar.
4. **Profiles**: tap the **initials avatar** at the top-right of the gallery. The menu lists every family member — switch to one to browse their timeline — and is also where you **register** a new one, **rename** the profile you are on, or **delete** it.
   - A profile is just a name. Write it however you like: spaces, capitals and accents are all fine, and you can change it whenever you want without anything on the server moving.
   - **Deleting** works only while a profile holds no photos or videos, *including anything still in its trash* or waiting in its import folder. If it still holds something, the app says how much and nothing is deleted. Its entries in Recent Activity are kept either way.
5. **Set up the Wi-Fi lock**:
   - Open **Sync Settings** from the menu.
   - Under *Home Wi-Fi lock*, tap **"Set home network"** and enter your network name (e.g. `MyHomeWiFi`). Automatic backup and discovery then run only on that network. Leave it blank to allow any network.
   - Granting the location permission is what lets Android tell the app which network it is on; without it the lock cannot be enforced and backup proceeds anyway.
6. **Reclaim Phone Storage**:
   - In **Sync Settings**, tap **"Check for reclaimable space"** (requires the server to be reachable).
   - The app asks the laptop which photos it holds and offers to remove only those local copies. On Android 11+ the system shows its own confirmation before anything is deleted.
7. **Hard Disk Imports**:
   - Copy folders of historical photos from an external drive into `Pictures/FamilyPhotoBackup/import/<profile name>/` on the laptop — the folder is named after the profile, so look for your own name.
   - In **Sync Settings**, tap **"Scan import folder"**. The server indexes recursively and sorts everything into the `storage/` tree.
8. **View Recent Activity**: open **Recent Activity** from the menu for a chronological log of write actions taken by anyone on the LAN server.
9. **Browsing offline**: with the laptop switched off the gallery, filters, date drill-down and albums all keep working from the local cache. Only changes — deleting, tagging, album edits — need the server, and the app says so when you try.

## 🌐 3. Using It From a Browser

Open **`http://<laptop-ip>:8080/app/`** on any machine on the home Wi-Fi — a laptop, an iPad, an
iPhone, a desktop. Nothing to install.

This is the full application, not a viewer. If nobody in the house has an Android phone, this is
Fotoloka.

1. **First visit**: if the server has a password, it asks once and remembers it in that browser. On a
   brand-new server it offers to **add the first profile** — so a household can set itself up here
   without ever touching the app.
2. **Getting around**: the **hamburger menu** holds Gallery, Favourites, Albums, Trash and Import
   folder. The gallery's top bar carries **slideshow, sort, filter** and the **profile avatar**, in
   the same order and with the same icons as the Android app, so the two are one thing to learn.
3. **Filtering**: the **filter icon** opens media type, a Year → Month → Day drill-down and your
   tags. What is applied appears as removable chips under the bar. Picking a tag or a date shows it
   oldest-first, as on the phone; clearing puts the ordering back.
4. **The big screen**: the **slideshow button** plays whatever is on screen — the whole library, one
   year, one tag, one album. Space pauses, arrow keys step, Escape leaves, and the speed control
   offers the same four durations as the app. This is what the browser client was built for: a
   photograph at a size worth looking at.
5. **Selecting photos**: every tile has a circle in its corner. Pick some and the top bar becomes a
   selection bar with **Favourite, Album, Tag** and **Trash** — plus **Un-album** and **Cover**
   while you are inside an album.
6. **One photo**: click a tile for full screen, where you can favourite it, tag it, **change the
   date taken**, or trash it. Your own tags are listed along the bottom and each can be clicked off.
7. **Videos play here too**: open a clip and you get a play button; press it and the video plays with
   your browser's own controls — pause, seek, volume, full screen. Close it to come back to the
   gallery. Slideshows still skip videos, as they do on the phone: a show runs on a timer and a clip
   does not.

   One limit worth knowing, and it is your browser's rather than Fotoloka's: the server streams your
   file exactly as recorded and never re-encodes it, so a clip only plays here if the browser can
   decode it. **Recent iPhones and many Android phones record in HEVC (H.265), which Chrome and
   Firefox on a desktop generally will not play** — the clip downloads and then refuses, and the page
   tells you that is what happened. Safari usually plays HEVC; the Android app always does, because
   the phone decodes it in hardware. Anything recorded as H.264/AVC plays everywhere. If you want
   browser playback for everything, set your phone's camera to the "most compatible" / H.264 option.
8. **Inside an album with several contributors**, the top bar offers a **contributor filter** naming
   everyone who has added photos to it, so you can see just one person's. It appears only when more
   than one family member has contributed — with one, there is nothing to narrow.
9. **Adding photos from this machine**: the **`+` button** in the top bar opens the file chooser and
   uploads what you pick, with progress under the bar. Keep browsing while it runs.
10. **Adding photos already on the server**: **Import folder** in the menu names your own drop folder
    (e.g. `import/Naveen/`), so you can copy a decade of holidays off an external drive straight into
    it and then press **Scan now**. The server does the work; this only asks it to start and reports
    what it found.
11. **Trash**: kept 30 days. Select and **put back**, or delete for good. **Empty trash** removes
    everything, and cannot be undone.
12. **Profiles**: the **avatar** at the top-right switches between family members, and adds, renames
    or deletes them. Renaming moves nothing — photos, albums and the import folder stay exactly
    where they are.

**What is not here**, and why: automatic camera-roll backup. That needs an app running in the
background on the phone that holds the photos, which a web page cannot be. Upload from a browser is
deliberate — you choose the files. Everything else the Android app does, this does.

**A note on password and privacy**: the browser client is behind the same password as every other
client, and it stores that password in the browser it was typed into. On a shared computer, use a
private window.

## 💾 4. External Hard Drive, Backup, & Disaster Recovery Guide

Even a standard laptop hard disk can quickly run out of space. You can configure **Fotoloka** to store all photos, cached thumbnails, and index files directly on a connected **external USB hard drive**, making your data highly portable and easy to back up.

### 🔌 A. Setting up the Server on an External Hard Drive

Instead of using the default internal storage, you can specify your external drive folder path as an argument to the install scripts:

#### 🍏 macOS:
```bash
cd server
chmod +x scripts/install-mac.sh
./scripts/install-mac.sh /Volumes/MyExternalDrive/PhotoBackup
```

#### 🔌 Windows:
```cmd
cd server
scripts\install-windows.bat D:\PhotoBackup
```

* **What this does**: This writes `storage.root` into your `photobackup.properties` (see [Configuration](#-configuration)). All photos (`storage/`), import folders (`import/`), and the index database (`photobackup.db`) will reside strictly on the external drive. To move the library later, edit that one line and restart — you do not have to reinstall.
* On macOS the launch agent additionally watches that path, so the server shuts down cleanly if you unplug the drive.

---

### 📥 B. How to Easily Back Up Your Photos

Because both the SQLite database `photobackup.db` and the physical photo files live inside the **exact same folder** on the external hard drive, taking a backup is incredibly simple. 

You can clone your entire photo vault to a secondary backup drive by running a single command:

#### macOS / Linux:
```bash
rsync -avz --delete /Volumes/MyExternalDrive/PhotoBackup/ /Volumes/MySecondaryBackupDrive/PhotoBackup/
```

#### Windows (using Command Prompt / PowerShell):
```cmd
robocopy D:\PhotoBackup E:\PhotoBackup /MIR
```
*(No services need to be shut down; SQLite can safely back up its state dynamically).*

---

### 🚨 C. Disaster Recovery: Migrating to a New Laptop

If your main laptop breaks or has a hardware issue:
1. **Connect the external hard drive** to your new laptop.
2. Clone this repository on the new laptop.
3. Run the installer script, passing the external drive folder path as the argument (e.g. `./install-mac.sh /Volumes/MyExternalDrive/PhotoBackup`).
4. **Result**: The server instantly detects the existing `photobackup.db` file, preserves all custom profile settings, and continues serving the exact same catalog of raw photos and albums seamlessly.

---

### 🥧 D. Setting up the Server on a Raspberry Pi

To save power and run a cheap, silent, 24/7 family photo server, you can host the service directly on a **Raspberry Pi** connected to the USB external drive:

1. **Format and Connect**: Connect the external USB drive to the Raspberry Pi. Mount it to a standard directory (e.g. `/mnt/photobackup`) and configure `/etc/fstab` to mount it automatically on boot.
2. **Install Java**: Ensure Java Runtime 17 or 21 is installed:
   ```bash
   sudo apt update
   sudo apt install -y default-jre
   ```
3. **Deploy the Server**: Copy the compiled `photo-backup-server-all.jar` to your Pi.
4. **Configure and run**: Put a `photobackup.properties` next to the JAR pointing at your mount point:
   ```properties
   storage.root = /mnt/photobackup
   ```
   ```bash
   java -jar photo-backup-server-all.jar
   ```
5. **Autostart**: You can set this up as a standard `systemd` service:
   Create `/etc/systemd/system/photobackup.service`:
   ```ini
   [Unit]
   Description=Family Photo Backup Server
   After=network.target

   [Service]
   Type=simple
   WorkingDirectory=/home/pi
   ExecStart=/usr/bin/java -jar /home/pi/photo-backup-server-all.jar
   Restart=always
   User=pi

   [Install]
   WantedBy=multi-user.target
   ```
   Enable and start the service:
   ```bash
   sudo systemctl enable photobackup.service
   sudo systemctl start photobackup.service
   ```

The Raspberry Pi will automatically advertise itself via mDNS across your Wi-Fi router, letting the Android app connect zero-config without changing any code!

---

## 🛠️ 5. Developer Compilation & Verification

### ⚡ Quick Development Testing Cycle (Live Console Logs)

For rapid development and debugging, you can run the server directly using Gradle's `run` task. This automatically compiles code changes and runs the server in the foreground, outputting all logs directly to the command line:

#### macOS / Linux:
```bash
cd server
# Run with default storage (~/Pictures/FamilyPhotoBackup/)
./gradlew run

# Or run with custom storage (e.g. your external hard drive folder)
PHOTO_BACKUP_ROOT=/Volumes/Naveen/photo-backup-service/data ./gradlew run
```

For a persistent development setup, drop a `photobackup.properties` in `server/` instead — Gradle's
`run` task uses that as its working directory, so it is picked up automatically. It is gitignored, so
a local password cannot be committed by accident:

```bash
cp scripts/photobackup.properties.example photobackup.properties
```

#### Windows:
```cmd
cd server
:: Run with default storage
gradlew run

:: Run with custom storage
set PHOTO_BACKUP_ROOT=D:\PhotoBackup
gradlew run
```

To stop the server, simply press `Ctrl + C` in your terminal window.

> [!IMPORTANT]
> **Conflict with Background Service (macOS)**: If you have already installed the background agent on your machine, it will occupy port `8080` and automatically restart if killed. You must stop (unload) the background agent before starting a manual development run:
> 
> * **Stop background agent**:
>   ```bash
>   launchctl unload ~/Library/LaunchAgents/com.qspapps.photobackup.plist
>   ```
> * **Start background agent**:
>   ```bash
>   launchctl load ~/Library/LaunchAgents/com.qspapps.photobackup.plist
>   ```

---

### Build Server Shadow JAR Manually:
```bash
cd server
./gradlew shadowJar
```
Output: `build/libs/photo-backup-server-<version>-all.jar` (the version comes from `build.gradle.kts`)

### Run Server Manually (Compiled JAR):
```bash
java -jar build/libs/photo-backup-server-*-all.jar
```
Endpoint check — the discovery document, which is the one route no password guards:
```bash
curl http://localhost:8080/api/v2
# {"name":"Fotoloka","status":"online","apiVersions":["v2"],"passwordRequired":false, ...}
```

There is no `/api/v1`: it was removed at the v2 cutover rather than deprecated, so nothing answers
under it. The full REST contract is [`openapi-v2.yaml`](openapi-v2.yaml) — open it in Swagger UI, or
import it into Postman or Insomnia, to browse and call every endpoint. ([`openapi.yaml`](openapi.yaml)
is v1, kept only as the record of what it replaced.) The reasoning behind the design is in
[DESIGN_DOCUMENT.md](DESIGN_DOCUMENT.md) §4.

### Build and Serve the Browser Gallery:
The browser client is Kotlin compiled to WebAssembly, sharing its model, its API calls and its
gallery screens with the Android app. It is served by the photo server itself, so there is nothing
to install and nothing to configure — open it on any machine on the same network.

```bash
# From the repository root, not from server/
./gradlew :web:installToServer
```

That builds the bundle and copies it into the server's resources. Start (or restart) the server,
then open:

```
http://<server-address>:8080/app/
```

Arrow keys step through an open photo and Escape closes it.

If the server has `server.password` set, the gallery asks for it on first use and remembers it in
that browser, so each machine is asked once. The page itself is served without a password — it is
the API behind it that is protected, exactly as for the Android app.

It is a complete client — see [Using It From a Browser](#-3-using-it-from-a-browser) for what it
does. The only thing it cannot do is automatic camera-roll backup, which needs an app running on the
phone that holds the photos.

`installToServer` is a `Sync`, not a `Copy`: the bundle's filenames carry a content hash, so a
`Copy` would leave every previous build's `.wasm` behind in the server's resources.

**If a change you just made does not appear in the browser, check that it was actually built.**
`wasmJsBrowserDistribution` has been seen reporting `UP-TO-DATE` after a real source change, so
`installToServer` then copies the previous bundle and succeeds — the change silently never ships,
which looks exactly like a bug in the change. The quickest check is to grep the installed bundle for
something only the new code contains, rather than trusting the task outcome:

```bash
grep -c "some-string-from-your-change" server/src/main/resources/web/photobackup.js
```

`./gradlew :web:wasmJsBrowserDistribution --rerun-tasks` then rebuilds it properly. Note that
strings inside `js("…")` interop blocks end up in `photobackup.js`, while Kotlin code ends up in the
`.wasm` — so pick the grep target accordingly.

### Build Android Debug APK Manually:
The Android app, the shared modules and the browser client are **one Gradle build rooted at the
repository root**; `android/` has no wrapper of its own. Open the repository root in Android Studio,
not the `android/` folder.

```bash
# From the repository root
./gradlew :androidApp:assembleDebug
```
Output: `android/app/build/outputs/apk/debug/app-debug.apk`

### Run the Tests:
```bash
# Shared model and browser client, then the Android app — all from the repository root
./gradlew :core:testDebugUnitTest :ui:testDebugUnitTest :androidApp:testDebugUnitTest

# The server is a separate Gradle build
cd server && ./gradlew test
```

`:core` and `:ui` are multiplatform, so their shared tests compile for both Android and
WebAssembly. The commands above run the JVM variant, which is the fast, deterministic one. The
browser run drives a headless Chrome and is opt-in, because it is slow and fails for reasons
unrelated to the code:

```bash
./gradlew :ui:wasmJsBrowserTest -PbrowserTests
```

Do not add `--rerun-tasks` — it rebuilds every target of every module and turns a seconds-long run
into minutes.

