# Privacy Policy for Fotoloka

**Last updated: 9 September 2026**

Fotoloka is a photo and video backup app for Android. It backs your media up to a
Fotoloka server that **you** install and run on your own computer, on your own
home network.

The short version: **we operate no servers and receive none of your data.** There
is no Fotoloka cloud, no account, no analytics and no advertising. Your photos go
from your phone to your computer and nowhere else.

---

## 1. Who this policy is for

This policy covers the Fotoloka Android application (`com.qspapps.photobackup`).

Because Fotoloka is self-hosted, **you are the operator of the server that holds
your data.** This policy describes what the app does. What happens to the data
once it reaches your own computer is under your control, not ours.

## 2. Information the app collects and sends to us

**None.** The app contains no analytics, crash-reporting, advertising or tracking
libraries of any kind. It does not create an account, does not identify you to
us, and makes no network request to any server operated by the developer.

## 3. Information the app sends to your own server

When you use Fotoloka, the app sends the following to the Fotoloka server running
on your computer, over your local network:

- **Your photos and videos**, when backup is on or when you upload them.
- **The metadata embedded in those files** by the camera that took them. This
  includes the capture date and time, the camera model, and — if your camera
  recorded it — **the GPS coordinates of where the photo was taken.** This is
  metadata already inside your photo files; Fotoloka carries it across so that
  dates and places stay correct, and does not add any.
- **The profile name you are using**, so a shared household library can show who
  added or changed what.
- **Tags, favourites, album membership and similar organising information** that
  you create in the app.
- **Your server password**, if you have set one, to authenticate each request.

This traffic goes to an address on your own network. The app refuses to send
unencrypted traffic anywhere else: a built-in check rejects any cleartext request
to a host outside private (RFC 1918), loopback, link-local or `.local` addresses.

## 4. Information stored on your phone

The app stores the following on your device only:

- The address of your server, and the name of the Wi-Fi network you chose as your
  home network.
- The profile you are currently using.
- Your server password, if you set one.
- A cache of your library — photo records, and thumbnail images — so the app
  works when the server is unreachable.

**Please note:** the server password is stored in the app's private storage using
Android's standard preferences, which is protected by the operating system's app
sandbox and by device encryption, but is **not** additionally encrypted by the
app. Treat your server password as you would any password stored on a device.

Uninstalling the app removes all of the above from your phone. It does not remove
anything from your own server.

## 5. Permissions, and why each is needed

- **Photos and videos** (`READ_MEDIA_IMAGES`, `READ_MEDIA_VIDEO`,
  `READ_MEDIA_VISUAL_USER_SELECTED`, and on older Android versions
  `READ_EXTERNAL_STORAGE`): to find media that needs backing up and to display
  your library. If you grant limited access, only the items you select are
  visible to the app.
- **Location** (`ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION`): **used only to
  read the name of the Wi-Fi network your phone is currently connected to.**
  Android requires a location permission before an app may read a network name;
  there is no narrower permission that allows it. Fotoloka uses this to run
  backup only on your home network, and not on mobile data or on a public
  network. **The app never requests, receives, records or transmits your
  geographic location.** The only thing kept is the name of the network you chose
  as your home network, stored on your phone. If you decline this permission the
  app still works; the home-network restriction simply cannot be enforced.
- **Network access** (`INTERNET`, `ACCESS_NETWORK_STATE`, `ACCESS_WIFI_STATE`,
  `CHANGE_WIFI_MULTICAST_STATE`): to reach your server and to find it on your
  network automatically, which uses multicast discovery.

## 6. Sharing with third parties

Fotoloka does not share your data with anyone. There are no third-party services,
no data brokers, no advertising partners and no sale of personal information.

If you use the app's Share action to send a photo to another app you have chosen,
that photo leaves Fotoloka and is then handled by that app under its own policy.

## 7. Children

Fotoloka is a general-purpose tool and is not directed at children. It collects
nothing from anyone, including children.

## 8. Your data, your control

Because nothing reaches us, there is nothing for us to disclose, correct or
delete on your behalf. You can:

- Delete photos in the app, which moves them to Trash on your server and purges
  them after 30 days, or delete them immediately from Trash.
- Uninstall the app to remove everything it stored on your phone.
- Stop or delete your server, which removes the library entirely.

## 9. Security

Traffic between the app and your server travels over your local network. On a
home network without a certificate this is unencrypted HTTP, which is why the app
refuses to send cleartext to anything outside your local network. You can set a
password on your server so that only your household can reach it.

The security of the computer running your server, and of your home network, is
your responsibility.

## 10. Changes to this policy

If this policy changes, the date at the top will change with it, and the revised
version will be published at this address.

## 11. Contact

Questions about this policy: **support@qspapps.com**
