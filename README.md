# See_the_World_MobileApp

Aplicatie de calatorie facuta in Flutter, pentru Android. Are trei parti:
descoperire de locuri pe harta, un jurnal cu locurile tale si un asistent care iti
da recomandari. Merge si online, si offline.
Am folosit Timisoara ca exemplu pentru partea de descoperire (locuri, tururi,
rute), dar codul nu e legat de un oras anume, se poate folosi si in alta parte.
Ce face



Descoperire si rute — o harta cu locuri de vizitat si detalii (descriere,
poza, coordonate), cautare prin Google Places, navigatie pas cu pas si tururi
offline (ex. Timisoara City Tour) pentru cand nu ai net.

Jurnal de calatorie — locuri favorite, poze puse pe locurile vizitate
("memory photos") si un profil cu cateva statistici.

Asistent cu recomandari — un chat (scris sau vocal) care recomanda locuri,
pe baza favoritelor si a locurilor vizitate. Cheia Gemini nu sta in aplicatie,
ci pe server, intr-o Cloud Function (askGemini), ca sa nu poata fi luata din
client.

In plus, peste tot: login cu email/parola sau Google/Facebook, tema light/dark si
preferinte salvate local.
Tehnologii


Flutter + Dart
Firebase (Auth, Firestore, Storage, Cloud Functions)
Google Maps, Places si Directions API
Google Gemini (chemat de pe server, prin Cloud Function)
Speech-to-Text si Text-to-Speech pentru asistentul vocal
Geolocator, Flutter Compass, flutter_map + OpenStreetMap (pentru tururile offline)

Cum e organizat codul


lib/
  components/   widget-uri refolosite (buton, camp text, buton social)
  pages/        ecranele app-ului (home, login, register, profil, favorite,
                tururi offline, asistent AI)
  services/     logica si integrarile (Discover, profil de gust, cache de dale,
                poze locale, teme, preferinte)
  widgets/      bara de navigatie si dialogurile comune
functions/      Cloud Function askGemini (vorbeste ea cu Gemini)
test/           teste pentru ranking, profil de gust si componente


Configurare si rulare

De ce ai nevoie


Flutter SDK (canal stable), verifici cu flutter doctor.
Aplicatia e facuta pentru Android: lib/firebase_options.dart si
android/app/google-services.json sunt incluse si reale pentru Android. Pentru
iOS/web ar mai trebui completat config-ul Firebase pentru platformele alea.
O cheie Google Cloud cu: Maps SDK for Android, Places API, Geocoding API,
Directions API.

Cheile (nu sunt in git)

Cheile se pun in doua locuri. google_maps_api.xml e obligatoriu, de acolo isi ia
harta nativa cheia. dart_defines.json e pentru apelurile din cod (Places,
Geocoding, Directions) si e folosit de configuratiile din .vscode/launch.json;
daca lipseste, apelurile astea cad tot pe cheia nativa.


android/app/src/main/res/values/google_maps_api.xml:

<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="google_maps_api_key">CHEIA_TA</string>
    <string name="google_directions_api_key">CHEIA_TA</string>
    <string name="google_places_api_key">CHEIA_TA</string>
</resources>




dart_defines.json in radacina (fara cheia Gemini, aia sta pe server):

{
  "GOOGLE_MAPS_API_KEY": "...",
  "GOOGLE_DIRECTIONS_API_KEY": "...",
  "GOOGLE_PLACES_API_KEY": "..."
}




Rulare


flutter pub get
flutter run --dart-define-from-file=dart_defines.json


Build APK si instalare

Build de release (e semnat cu cheia de debug, deci se instaleaza direct, vezi
android/app/build.gradle.kts):

flutter build apk --release --dart-define-from-file=dart_defines.json


APK-ul iese in build/app/outputs/flutter-apk/app-release.apk. Il instalezi pe un
telefon sau emulator Android conectat:

flutter install
# sau direct:
adb install build/app/outputs/flutter-apk/app-release.apk


Pe urma deschizi app-ul din iconita. Sau flutter run --release, care face build,
instalare si pornire dintr-o data.
Asistentul AI (Cloud Function)

Cheia Gemini sta doar pe server, ca secret Firebase, nu in client. Se pune o
singura data si are nevoie de planul Blaze:

npm install -g firebase-tools
firebase login
firebase functions:secrets:set GEMINI_API_KEY
firebase deploy --only functions


Functia askGemini ruleaza in europe-west1. Pasi mai detaliati (Blaze,
schimbarea cheii vechi, emulator local) sunt in functions/README.md.
Teste


flutter test


Testele acopera ranking-ul din Discover, inclinarea recomandarilor dupa profilul
de gust si componenta de buton principal.
