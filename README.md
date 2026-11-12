## HEXAGEN AUTOUPDATER
Bilkul. **Aapki current HEXAGEN build ke liye sabse reliable update method LicenseAuth Version + Download URL hai.**

> **Important:** Current EXE mein `UPDATE_FILE_ID` empty hai. Isliye sirf LicenseAuth mein file add karne se purani EXE authenticated update download nahi kar payegi. **Is first update ke liye Method 1 use karo.**

## Method 1 — Update URL ke through update push karo

### 1. New version number set karo

Example: current `1.0.0` se new release `1.1.0`.

[backend.py](file:///C:/Users/SUBHAN/Desktop/FF%20Guest%20Gen%20Tool/backend.py) mein

```python
VERSION = "1.1.0"
```

[license_client.py](file:///C:/Users/SUBHAN/Desktop/FF%20Guest%20Gen%20Tool/license_client.py) mein

```python
LICENSEAUTH_VERSION = "1.1"
```

Dono ka purpose different hai

- `VERSION`: app UI, update comparison aur user-facing version.
- `LICENSEAUTH_VERSION`: LicenseAuth ko bheja gaya application version.

[version_info.txt](file:///C:/Users/SUBHAN/Desktop/FF%20Guest%20Gen%20Tool/version_info.txt) mein bhi EXE metadata version `1.1.0.0` update karo.

### 2. New EXE build aur test karo

Project folder mein terminal kholo

```bash
python -m unittest discover -s tests -v
python -m PyInstaller --noconfirm hexagen.spec
python tests/verify_executable.py
```

Build [hexagen.spec](file:///C:/Users/SUBHAN/Desktop/FF%20Guest%20Gen%20Tool/hexagen.spec) use karega aur new EXE yahan banegi

[dist/HEXAGEN.exe](file:///C:/Users/SUBHAN/Desktop/FF%20Guest%20Gen%20Tool/dist/HEXAGEN.exe)

Purani EXE ka backup rakhna, taake rollback ho sake.

### 3. New EXE ko hosting par upload karo

`HEXAGEN.exe` ko apni website/hosting par upload karo. URL

- HTTPS hona chahiye.
- Direct EXE download dena chahiye.
- Browser mein kholne par download start ho.
- Google Drive/Dropbox ka preview ya sharing-page URL use mat karo.
- ZIP upload mat karo—current updater raw Windows `.exe` expect karta hai.

URL test karne ke baad aapke paas hoga

```text
<DIRECT_HEXAGEN_EXE_DOWNLOAD_URL>
```

### 4. LicenseAuth dashboard configure karo

LicenseAuth dashboard mein

1. **Manage Applications** kholo.
2. `hexagen_ffguest` application select karo.
3. Application version ko new version, example `1.1`, par set karo.
4. **Download/Update URL** field mein direct EXE URL daalo.
5. Settings save karo.

**Pehle EXE upload aur test karo, dashboard version baad mein badlo.** Warna users ko update milega lekin file available nahi hogi.

### 5. Old application par kya hoga?

Purani HEXAGEN `LICENSEAUTH_VERSION = "1.0"` ke saath start hogi

1. LicenseAuth ko `1.0` bhejegi.
2. Dashboard par latest version `1.1` hoga.
3. LicenseAuth `invalidver` response dega.
4. HEXAGEN **“Update required”** prompt dikhayegi.
5. User **Yes** select karega to configured official download URL browser mein khulega.
6. User new EXE download karke run karega.
7. New EXE `1.1` bhejegi, isliye login normally chalega.

Yeh current build ke liye recommended aur tested update path hai.

---

## Method 2 — Authenticated File ID updates setup karo

Yeh future builds ke **About → Online updates → Download update** flow ke liye hai.

### One-time setup

1. LicenseAuth dashboard mein **Files/Add File** kholo.
2. **File URL** mein direct new `HEXAGEN.exe` URL daalo.
3. **Authenticated** enable karo.
4. File save karo.
5. Generated **File ID** copy karo.
6. [license_client.py:26](file:///C:/Users/SUBHAN/Desktop/FF%20Guest%20Gen%20Tool/license_client.py#L26) mein daalo

```python
UPDATE_FILE_ID = "YOUR_REAL_LICENSEAUTH_FILE_ID"
```

7. Tests aur EXE build dobara run karo.
8. Yeh File ID wali new base EXE users ko Method 1 ke through distribute karo.

Uske baad app update ko authenticated session se download karke yahan save karti hai

```text
%LOCALAPPDATA%\HEXAGEN\updates\HEXAGEN-<version>.exe
```

Download hone par user se confirmation liya jayega, phir update open ho sakta hai.

### Future updates ke liye

- New EXE upload karo.
- **Same LicenseAuth File ID** ki underlying file/URL update karo.
- Dashboard application version last mein bump karo.
- Naya File ID har update par mat badlo, warna purani EXE ko new ID pata nahi hogi.

Agar dashboard same File ID ki file replace karne ki permission nahi deta, to current code ke liye dynamic File ID lookup add karna hoga.

> **Version enforcement note:** Agar dashboard version bump par LicenseAuth old builds ko `invalidver` deta hai, old app login se pehle hi mandatory-update prompt dikhayegi—About page tak nahi pahunchegi. Authenticated About updates tab useful hain jab old versions ko login karna allowed ho. Warna Method 1 ka mandatory-update behavior expected hai.

**Aapki is next release ke liye: raw EXE host karo, direct download URL LicenseAuth mein daalo, dashboard version bump karo. Authenticated updates ke liye permanent File ID wali base build baad mein distribute karo.**
