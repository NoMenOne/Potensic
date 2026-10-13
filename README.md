# Potensic Atom 3 – waypoint szerkesztő

Egyetlen HTML fájlból álló, böngészőben futó eszköz **Potensic Atom 3** drón waypoint-küldetéseinek megtervezéséhez. Térképen jelölöd ki az útvonalat, beállítod a paramétereket, és az oldal az app által használt fájlformátumban menti el a küldetést (`global.json`, küldetésfájl, előnézeti kép).

> **Nem hivatalos projekt.** Nincs kapcsolatban a Potensic céggel. A fájlformátumot az app által készített mintafájlokból fejtettem vissza, hivatalos leírás nem áll rendelkezésre. A repülés előtt mindig ellenőrizd a küldetést az appban, és tartsd be a helyi drónszabályokat. A használat saját felelősségre történik.

---

## Tartalom

- [Mire jó?](#mire-jó)
- [Használat](#használat)
- [Fülek és funkciók](#fülek-és-funkciók)
- [Mit hoz létre a mentés?](#mit-hoz-létre-a-mentés)
- [A JSON fájlok felépítése](#a-json-fájlok-felépítése)
  - [global.json](#globaljson)
  - [Küldetésfájl (waypointok + POI-k)](#küldetésfájl-waypointok--poi-k)
- [Számítások](#számítások)
- [Ismert korlátok és nyitott kérdések](#ismert-korlátok-és-nyitott-kérdések)

---

## Mire jó?

Az appban a waypointokat telefonon kell kézzel kirakosgatni. Ez az eszköz ennél többet tud:

- **kényelmes tervezés** nagy képernyőn, műholdas térképen, pontos számértékekkel,
- **ortofotó / területfelmérés**: egy területre automatikusan sorokba rendezett, átfedéssel számolt útvonalat generál,
- **körberepülés** egy kijelölt pont (POI) körül,
- **meglévő küldetések betöltése és szerkesztése**,
- a küldetés **kész mappaként (ZIP)** menthető, előnézeti képpel együtt.

## Használat

1. Töltsd le a `potensic_waypoint_editor.html` fájlt, és nyisd meg böngészőben (dupla kattintás).
2. **Internetkapcsolat kell**: a térképcsempék (Esri World Imagery, OpenStreetMap), valamint a Leaflet és a JSZip könyvtár (cdnjs) online töltődik be.
3. Tervezd meg az útvonalat (lásd lent), majd a **Mentés** szakaszban nyomd meg a **Mappa letöltése (ZIP)** gombot.
4. Csatlakoztasd a vezérlőt a számítógéphez USB-n, és másold a kapott mappát az app küldetés-könyvtárába:

   ```
   Ez a gép\PTD 2\Internal shared storage\Android\data\com.ipotensic.atom\files\Waypoint
   ```

   Az Android-eszközön ez a `/Android/data/com.ipotensic.atom/files/Waypoint` mappa (PTD 2 képernyős vezérlő, `com.ipotensic.atom` app). Másolás előtt nézd meg, hogyan tárolja az app a saját küldetéseit (külön mappa küldetésenként, vagy közvetlenül a fájlok), és ahhoz igazítsd a mentett mappát.

Ajánlott böngésző: Chrome vagy Edge. Más böngészőben a ZIP-letöltés is működik. A **Mentés mappába** gomb (közvetlen mappaírás) csak a File System Access API-t támogató böngészőkben jelenik meg (számítógépes Chrome/Edge).

## Fülek és funkciók

A panel tetején három fül van, alattuk a közös szerkesztőfelület (globális beállítások, waypointlista, kijelölt waypoint, POI-k, mentés).

### Waypoint

- Kattintás a térképre: új waypoint. A pontok húzással mozgathatók.
- A lista alatt: pont fel/le mozgatása, törlése; „Magasság → mind" és „Sebesség → mind" tömeges beállítás.
- **POI pont hozzáadása**: megfigyelt pont felvétele (térképen húzható, jobb klikkel törölhető).
- Betöltés: **Fájlok betöltése** gombbal a `global.json` és a küldetésfájl egyszerre is betölthető.

### Ortofotó

1. Rajzold körbe a területet a sarkokra kattintva (a sarkok húzhatók, jobb klikkel törölhetők).
2. Állítsd be az átfedést, a magasságot, a sebességet, a kamera adatait és a sorok irányát.
3. A narancs szaggatott vonal élőben mutatja a sorokat és a fotópontokat, alatta a terep-felbontás (cm/px), a fotók száma, a hossz és a becsült idő.
4. **Útvonal generálása**: a terv waypointokká alakul (oda-vissza haladó sorok).

Alapértékek: 60 m, 5 m/s, 75% előre / 70% oldalsó átfedés, 84° átlós látószög, 8192×6144 px, gimbal −90°. A „Fotó művelet" legördülővel a pontok `PHOTO` műveletet kapnak, vagy kikapcsolható (időzített fotózáshoz).

### Tervezett utak – Körberepülés

1. Kattints a térképre a megfigyelt pont helyére.
2. Állítsd be a sugarat, magasságot, sebességet, a waypointok számát egy körön, a körök számát, a kezdő irányt és az irányt.
3. **Útvonal generálása**: a kör középpontja POI lesz, a pontok `TO_POI` irányt és `POI` gimbalt kapnak, vagyis a dőlést az app számolja.

Alapértékek: 30 m sugár, 40 m magasság, 3 m/s, 24 pont/kör.

### Közös felület

- **Globális beállítások**: lásd a [global.json](#globaljson) szakaszt.
- **Kijelölt waypoint**: minden waypoint-mező szerkeszthető az app menüi szerinti legördülőkkel és tartományokkal. A csak egyedi módban érvényes mezők (sebesség, irány, dőlés, zoom) más módban le vannak tiltva.
- **POI pontok**: a POI-lista szerkesztése (magasság, koordináták).
- **Mentés**: lásd lent.

---

## Mit hoz létre a mentés?

A **Mappa letöltése (ZIP)** gomb egy ZIP-fájlt ad, amelyben egyetlen mappa van:

```
task_<időbélyeg>.zip
└── task_<időbélyeg>/
    ├── global.json            ← globális beállítások
    ├── <időbélyeg+71>.json    ← a küldetés (waypointok + POI-k)
    └── task_<időbélyeg>.jpg   ← előnézeti kép, 1920×1080
```

| Fájl | Tartalom |
|---|---|
| `global.json` | A küldetés egészére vonatkozó beállítások (repülés vége, jelvesztés, sebesség, magasság, sorrend). |
| `<szám>.json` | A waypointok és a POI-k. A fájlnév alapból a kép időbélyege + 71, a „Küldetésfájl neve" mezővel felülírható. |
| `task_<időbélyeg>.jpg` | Műholdas térképkép az útvonallal és a területtel. Az app ezt a küldetés előnézeteként használhatja. |

A **Mentés mappába** gomb ugyanezt a három fájlt egy általad választott helyre írja, `task_<időbélyeg>` nevű almappába.

A mentés előtt az oldal ellenőrzi és igazítja az értékeket: sebesség 0,5–10 m/s, globális magasság 10–120 m, és hibát ad, ha van POI módú pont POI nélkül.

> A mappanév, a fájlnevek és az időbélyeg-kapcsolat az app saját mintafájljaiból következtetett. A célmappa ismert (lásd a Használat szakaszt), de azt, hogy az app a `Waypoint` mappában küldetésenként almappát használ-e, még nem erősítettük meg.

---

## A JSON fájlok felépítése

A kulcsok ábécé szerinti sorrendben szerepelnek, a lebegőpontos számok `5.0` alakban (nem `5`). Az oldal ugyanígy írja ki őket, ezt az app mintafájljain ellenőriztem: betöltés után a mentés bájtra azonos kimenetet ad.

### global.json

Egyetlen JSON objektum.

```json
{"finishAction":"BACK","globalHeight":0,"globalHeightType":0,"isOrder":true,"lostAction":"RETURN","speed":5.0}
```

| Kulcs | App menü | Értékek |
|---|---|---|
| `finishAction` | End of Flight | `RETURN` (RTH), `BACK` (Back to Start), `HOVER` (Hover), `LAND` (Land) |
| `lostAction` | Signal Lost | `RETURN` (RTH, az egyetlen lehetőség) |
| `speed` | Global Speed | szám, 0,5–10 m/s |
| `globalHeightType` | Waypoint Altitude | `0` = Egyedi (Custom), `1` = Globális (Gbl. SET) |
| `globalHeight` | Waypoint Altitude | `0` Egyedi módban; 10–120 (m) Globális módban |
| `isOrder` | Forward / Reverse Flight | `true` = előre, `false` = visszafelé |

### Küldetésfájl (waypointok + POI-k)

A fájl **két JSON tömbből áll, pontosvesszővel elválasztva**. Egyben nem érvényes JSON, ezért a feldolgozás előtt a `];[` jelnél kell szétvágni:

```
[ {waypoint}, {waypoint}, ... ] ; [ {poi}, {poi}, ... ]
```

Példa (kitalált koordinátákkal, tördelve):

```json
[
 {"action":"START_RECORD","gimbalPitch":0,"gimbalType":"DEFINE","height":30.0,"hoverTime":0,
  "lat":47.0,"lng":19.0,"poiHeight":50.0,"poiLat":0.0,"poiLng":0.0,"poiType":0,
  "speed":5.0,"speedType":"GLOBAL","yaw":0,"yawType":"TO_WAYPOINT","zoomRatio":1.0,"zoomType":"HAND"},
 {"action":"PHOTO","gimbalPitch":0,"gimbalType":"POI","height":40.0,"hoverTime":0,
  "lat":47.001,"lng":19.001,"poiHeight":35.0,"poiLat":47.002,"poiLng":19.002,"poiType":1,
  "speed":3.5,"speedType":"DEFINE","yaw":0,"yawType":"TO_POI","zoomRatio":2.0,"zoomType":"DEFINE"}
];
[{"height":35.0,"lat":47.002,"lng":19.002}]
```

#### Waypoint mezők (az első tömb elemei)

| Kulcs | App menü | Értékek / tartomány |
|---|---|---|
| `action` | Camera | `NONE`, `PHOTO` (Take Photo), `START_RECORD` (Start REC), `STOP_RECORD` (Stop REC) |
| `height` | Altitude | 10–120 m |
| `lat`, `lng` | – | WGS84 fokban |
| `speedType` | Speed | `GLOBAL` (Global Speed) vagy `DEFINE` (Custom) |
| `speed` | Speed | `DEFINE` esetén 0,5–10 m/s; `GLOBAL` esetén a globális sebesség |
| `yawType` | Heading / Course | `TO_WAYPOINT` (Face Flight Path), `TO_POI` (POI felé), `DEFINE` (Custom); a Manual értéke ismeretlen |
| `yaw` | Heading / Custom | −180…180°, csak `DEFINE` esetén számít, egyébként `0` |
| `gimbalType` | Gimbal Tilt | `DEFINE` (Custom), `POI`; a Manual értéke ismeretlen |
| `gimbalPitch` | Gimbal Tilt / Custom | −90…30°, csak `DEFINE` esetén számít, egyébként `0` (−90° a lefelé nézés) |
| `zoomType` | Zoom | `HAND` (Manual) vagy `DEFINE` |
| `zoomRatio` | Zoom | 1,0–4,0 (0,1-es lépés), `DEFINE` esetén; egyébként `1.0` |
| `hoverTime` | Hover | 0–30 s |
| `poiType` | – | `0` = nincs POI, `1` = van POI |
| `poiLat`, `poiLng`, `poiHeight` | – | A pont POI-jának adatai. POI nélkül `0.0`, `0.0`, `50.0` |

**Összefüggések**

- POI módban (`yawType: TO_POI` vagy `gimbalType: POI`) együtt áll be a `poiType: 1` és a három `poi*` mező a POI-lista megfelelő elemével.
- A nem aktív beállítások alapértékre kerülnek (lásd fent). Az oldal mentéskor ezt automatikusan elvégzi, így a kimenet megegyezik az app mintafájljaival.

#### POI-lista (a második tömb elemei)

| Kulcs | Jelentés |
|---|---|
| `height` | A megfigyelt pont magassága (m) |
| `lat`, `lng` | A megfigyelt pont koordinátái |

---

## Számítások

### Ortofotó

A kamera látószögéből (átlós `FOV`) és a képméretből (`pw` × `ph` px):

```
átló_a_földön   = 2 · magasság · tan(FOV / 2)
kép_szélesség   = átló · max(pw, ph) / √(pw² + ph²)     (a sorra merőleges lefedés)
kép_magasság    = átló · min(pw, ph) / √(pw² + ph²)     (a sor menti lefedés)

sortávolság     = kép_szélesség · (1 − oldalsó_átfedés)
fotótávolság    = kép_magasság  · (1 − előre_átfedés)
terep-felbontás = kép_szélesség / max(pw, ph)           (cm/px)
```

A sorok a megadott irányban (alapból észak = 0°) futnak, a terület sokszögét a sortávolsággal metszik. A sorok mindkét végén egy fotótávolságnyi túlnyúlás van. A kamera a haladási irányra merőlegesen fekszik, a gimbal alapból −90° (lefelé).

### Körberepülés

A pontok a kör mentén egyenletesen oszlanak: `szög = kezdő_irány ± 360° · i / pontszám`. A kört az utolsó pont zárja (egybeesik az elsővel). A sugár a megadott méret, a koordinátákat helyi sík közelítéssel számolja (kis távolságokon pontos).

---

## Ismert korlátok és nyitott kérdések

- **A fájlformátum visszafejtett.** Csak a mintafájlokban látott értékek szerepelnek a listákban. A Heading és a Gimbal Tilt **Manual** értéke ismeretlen. Ha betöltesz ilyen értéket tartalmazó fájlt, az oldal „ismeretlen érték" megjegyzéssel megőrzi és változatlanul visszaírja.
- **Üres POI-lista:** ha nincs POI, a második tömb `[]`. Nem ismert, hogy az app elfogadja-e. Az app mintafájljaiban mindig volt legalább egy elem.
- **Waypoint-limit:** az app maximális pontszáma nem ismert. Az ortofotó könnyen több száz pontot generál. Nagy területnél érdemes kisebb tesztterülettel kezdeni.
- **Fájlelhelyezés és párosítás:** a célmappa a `Waypoint` (lásd fent), de az almappa-szerkezet nem megerősített, és az sem, hogy az app a küldetést a kép időbélyege szerint párosítja-e a küldetésfájllal. Az oldal az app mintafájljait utánozza (a küldetésfájl időbélyege kicsit későbbi a képénél). **Nyitott kérdés**, hogy egy generált küldetést a készülék minden esetben megjelenít-e.
- **`PHOTO` viselkedés:** nem ismert, hogy a drón megáll-e a `PHOTO` pontnál. Ha kell, a lebegési időt (`hoverTime`) pontonként be lehet állítani.
- **Reverse Flight** (`isOrder: false`): az oldal csak a beállítást menti, az útvonal előnézetét és a becsült időt továbbra is előre iránnyal mutatja.
- **Kamera adatai:** az alapértékek (84°, 8192×6144) a gyártói adatlapok alapján vannak megadva, ellenőrizd őket.
- **Online függőségek:** Leaflet 1.9.4, JSZip 3.10.1 (cdnjs), térképcsempék: Esri World Imagery és OpenStreetMap. A helykeresés a Nominatim szolgáltatást használja. Az előnézeti képen a csempék szerzői jogi jelölése megjelenik.

## Biztonság

- Ellenőrizd a repülési területet, az akadályokat, a magasságkorlátokat és a helyi szabályokat (Magyarországon a szabad repülési magasság általában 120 m).
- Az első repülés előtt próbáld ki a küldetést kis magassággal, szabad területen.
- Az akkumulátor állapotát és a becsült repülési időt mindig ellenőrizd. A hosszú ortofotó-útvonalakat érdemes több küldetésre bontani.

## Licenc

_Add meg a választott licencet (például MIT)._
