# Green Life Polyclinic & Diagnostic Centre — Clinic Management 🏥

Chanchal, Malda · **Management software help: 7602292599**

## Ye software kya karta hai
- **3 tarah ki billing:** sirf OPD slip, sirf Lab bill, ya Combined (OPD + Lab) — receipt/slip aapke purane format jaisi.
- **13 visiting doctors** (degree, specialty, fees, din/timing) aur **231 tests ka rate card** pehle se bhare hain. Admin se Add / Edit.
- **Admin dashboard:** aaj kitne patients, kis doctor ke paas kitne, lab income, total collection, daily/weekly/monthly report, CSV export.
- **4 alag login:** **Admin** (sab kuch) · **OPD / Reception** (OPD slip, token bookings, due) · **Lab / Test** (sirf lab bill) · **Doctor** (sirf apne patients). **Audit Log** — kisne kaunsa bill edit/delete kiya. Combined (OPD + Lab) bill Admin banata hai.
- **Patient self-booking (QR):** patient QR scan karke apne phone par form bharta hai (naam, age, sex, phone, guardian, aaj ke doctor + fee) → "Token Book" dabate hi usko Digital Token (Token No., approx time, **Unpaid**) milta hai. OPD desk par turant bell + counter aata hai; "Open & collect" se patient ki details apne aap bhar jati hain, cash/UPI lekar Save & Print.
- **WhatsApp Slip:** har bill / token par WhatsApp button — patient ke number par pura slip pre-filled message ke saath khulta hai.
- **Online + Offline dono:** software browser ke andar chalta hai aur data har computer ke andar bhi save hota hai. Internet chala jaye to billing nahi rukti; internet aate hi data apne aap server par chala jata hai.
- **Backup:** "Backup & Sync" page se JSON backup download/restore, aur server ka poora backup bhi.
- **Mobile app jaisa:** phone mein "Add to Home screen" karke app ki tarah kholein.

## Files
| File | Kaam |
|---|---|
| `app.py` | Server (Flask) — data sync + patient booking (`/book`) + QR (`/qr.svg`) |
| `qr.py` | QR code banane wala (koi extra library nahi) |
| `static/index.html` | Poora clinic software (billing, dashboard, print, offline) |
| `static/book.html` | Patient ka booking page (QR scan karne par khulta hai) |
| `static/sw.js`, `manifest.json`, `icon.svg` | Offline khulna + phone app |
| `requirements.txt`, `Procfile`, `runtime.txt` | Render ke liye |

## Local pe chalana (test ke liye)
```
pip install -r requirements.txt
python app.py
```
Browser mein kholein: http://127.0.0.1:5000  
Pehli baar login: **Admin PIN 1234**, **OPD (UMMI) PIN 1111**, **Lab (LAB) PIN 2222** — login karte hi **Users** page se PIN badal dein aur har desk ke liye alag user banayein.

---

## 🌐 Live / Online Karna — Step by Step (GitHub → Render → Supabase)

### Step 1: GitHub
1. https://github.com pe account banayein → **New repository** → naam `greenlife-clinic`.
2. **uploading an existing file** se ye sab upload karein: `app.py`, `requirements.txt`, `Procfile`, `runtime.txt`, `README.md` aur poora **`static`** folder (andar 4 files: index.html, sw.js, manifest.json, icon.svg). **Commit changes**.

### Step 2: Supabase (permanent database)
1. https://supabase.com pe free project banayein (database password yaad rakhein).
2. **Connect** (ya Settings → Database) mein **Connection string** copy karein, `[YOUR-PASSWORD]` ki jagah apna password likhein.
3. Agar Render par connect na ho to Supabase ki **Session pooler** wali connection string use karein (free plan par direct wali string kabhi-kabhi Render se nahi judti).
4. Table banane ki zaroorat nahi — server khud bana lega.

### Step 3: Render
1. https://render.com → GitHub se sign up → **New + → Web Service** → apna repo connect karein.
2. Region: **Singapore**. Build Command: `pip install -r requirements.txt`. Start Command: `gunicorn app:app`. Instance: **Free**.
3. **Environment Variables** mein ye 1 add karein:

| Key | Value |
|---|---|
| `DATABASE_URL` | Supabase ki connection string |

4. **Create Web Service**. 2-5 minute mein link milega, jaise `https://greenlife-clinic.onrender.com`.

### Step 4: Har computer / mobile par
1. Render ka link kholein → login karein.
2. Koi key ya password nahi chahiye — sync apne aap shuru ho jata hai (upar status chip mein dikhta hai). **Sirf pehle (main) computer par:** "Upload everything to server" dabayein.
4. **Settings** mein har computer ko alag code dein (1, 2, 3 …), taaki bill number kabhi repeat na ho.
5. Phone mein: Chrome → 3-dot menu → **Add to Home screen**.

### Step 5: QR Poster (patient self-booking)
1. Admin ya OPD user se login → **QR Poster** → **Print poster (A4)**. Poster par Bengali mein likha hai: *ডাক্তারের জন্য কিউআর (QR) কোডটি স্ক্যান করুন।*
2. Poster reception counter par laga dein. Patient scan karega → `https://aapka-link.onrender.com/book` khulega (koi PIN/password nahi).
3. Pehli baar kisi bhi computer par software kholte hi doctors ki list server par chadh jati hai (wahi list patient ko dikhti hai). Patient ko sirf **aaj** aane wale doctors dikhte hain.
4. Token No. har doctor ke liye alag chalta hai; approx time = doctor ka start time + (token − 1) × "Minutes per patient" (Doctors page se set hota hai, default 10 min). Jin doctors ka timing din-wise alag likha hai, unka approx time nahi dikhta.

## ⚠️ Zaroori baatein
- `DATABASE_URL` nahi dene par server local SQLite file use karta hai, jo Render free plan par restart hone par reset ho sakti hai. Isliye Supabase zaroor lagayein. (Clinic ke computers mein apna data phir bhi rehta hai — "Upload everything to server" se wapas chadha sakte hain.)
- Render free plan ki site 15 minute idle rehne par so jati hai; pehli baar kholne mein ~1 minute lag sakta hai. Software khulne ke baad billing offline bhi chalti hai.
- Staff login PIN se chalta hai, lekin server ka data API bina key ke khula hai (aapki demand ke hisaab se) — isliye software ka link sirf clinic staff ko dein. Patients ko sirf `/book` wala link/QR dein. Booking page par spam rokne ke liye limit lagi hai. Computer ki ghadi (date/time) sahi rakhein.
- Har hafte **Backup & Sync → Download backup** karke pen drive / Google Drive mein rakhein. Browser ka data clear karne se pehle backup zaroor lein.

## Aage ke features (baad mein add ho sakte hain)
WhatsApp par bill bhejna, bill par QR code, raat ko daily summary email, report ready hone par SMS, online appointment.

Kisi bhi dikkat ya naye feature ke liye: **7602292599**
