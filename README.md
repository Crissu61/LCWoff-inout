# LC Waikiki Offline – IN / OUT

Aplicație web (PWA) pentru evidența cutiilor LC Waikiki Offline: intrări pe camion cu cronometru de descărcare și sortare, livrări pe zile (cutii + paleți) și stocul rămas în depozit. Un singur utilizator, sincronizat pe toate dispozitivele prin Firebase Firestore.

Fișiere:

| Fișier | Rol |
|---|---|
| `index.html` | toată aplicația (interfață + logică) |
| `config.js` | datele proiectului tău Firebase — **de completat** |
| `firestore.rules` | regulile de securitate — **de lipit în Firebase Console** |
| `manifest.json`, `sw.js`, `icons/` | instalare ca aplicație pe telefon + funcționare offline |

---

## 1. Firebase (o singură dată, ~10 minute)

1. Intră pe https://console.firebase.google.com → **Add project** → nume (ex. `lcw-inout`) → poți dezactiva Google Analytics → Create.
2. **Authentication** → Get started → tab **Sign-in method** → activează **Email/Password** (doar prima opțiune) → Save.
3. **Authentication** → tab **Users** → **Add user** → emailul și parola cu care vei intra în aplicație (parola o alegi tu; o vei folosi pe toate dispozitivele).
4. **Firestore Database** → **Create database** → locație `europe-west3 (Frankfurt)` sau `eur3` → **Start in production mode** → Create.
5. **Firestore Database** → tab **Rules** → șterge tot ce e acolo, lipește conținutul din `firestore.rules` → **Publish**.
6. **Project settings** (rotița de lângă „Project Overview") → **General** → jos la *Your apps* → iconița **Web** `</>` → nume `LCW IN/OUT` → **nu** bifa Firebase Hosting → Register app. Vei vedea un bloc `const firebaseConfig = { apiKey: "...", ... }`. Copiază valorile în `config.js` (păstrează structura din fișier).

## 2. GitHub Pages

1. Creează un repository nou (poate fi **privat** — GitHub Pages funcționează și pe repo privat cu cont Pro/organizație; pe cont gratuit personal, repo-ul trebuie să fie public, dar datele nu sunt în repo, doar în Firebase, iar accesul la date e protejat de login).
2. Urcă toate fișierele din acest pachet (inclusiv folderul `icons/`) în rădăcina repository-ului, pe branch-ul `main`.
3. **Settings** → **Pages** → Source: *Deploy from a branch* → Branch `main` / folder `/ (root)` → Save. În 1–2 minute apare adresa `https://<cont>.github.io/<repo>/`.
4. Înapoi în Firebase: **Authentication** → **Settings** → **Authorized domains** → **Add domain** → `<cont>.github.io`. Fără pasul ăsta, login-ul dă eroare `auth/unauthorized-domain`.

## 3. Pe telefon / laptop

- Deschide adresa, loghează-te cu emailul și parola de la pasul 1.3. Rămâi logat.
- **Android (Chrome):** meniu ⋮ → *Add to Home screen* / *Install app*.
- **iPhone (Safari):** butonul Share → *Add to Home Screen*.
- Pe laptop în Chrome/Edge apare iconița de instalare în bara de adresă.

Datele se sincronizează în timp real între toate dispozitivele. Fără internet, aplicația funcționează în continuare (afișează o bară portocalie), iar modificările se trimit automat când revine conexiunea.

## 4. Cum se folosește

**Tab INTRĂRI**
- `+ Camion nou` → nr. auto, data sosirii, nr. cutii (ex. 1000), observații.
- Pe cardul camionului: `▶ Start descărcare` → `■ Stop descărcare` → `▶ Start sortare` → `■ Stop sortare`. Cronometrul rulează live.
- `✎ Editează` → poți corecta manual orele fiecărei etape (sau le ștergi dacă ai apăsat din greșeală).
- Sus: cutii intrate azi / în perioadă, camioane nefinalizate, timp mediu de descărcare și sortare pe camion și pe cutie.

**Tab LIVRĂRI**
- `+ Livrare` → data/ora, nr. cutii, nr. paleți, nr. auto, observații.
- Lista e grupată pe zile, cu totalul de cutii și paleți pe fiecare zi.
- Aplicația te avertizează dacă livrezi mai multe cutii decât sunt în depozit (poți salva oricum).

**Stoc** (sus, pe fiecare ecran): *Cutii în depozit* = total intrate − total livrate. *Nesortate (bulk)* = cutiile camioanelor care nu au încă „Stop sortare".

**Stoc inițial:** introdu-l ca un camion normal (ex. nr. auto `STOC INITIAL`), apasă pe rând start/stop la ambele etape ca să nu apară ca nesortat, sau editează-l și pune aceleași ore la toate cele patru câmpuri.

**Perioada:** câmpurile de dată din bara de sus filtrează listele și sumarele; `Luna` = luna curentă, `Tot` = fără filtru. Stocul din antet nu depinde de perioadă.

**Export:** `⬇ CSV` exportă tabul curent, pe perioada selectată, în format care se deschide direct în Excel (separator `;`, diacritice ok). Intrările includ orele și duratele în minute pe fiecare etapă.

## 5. Limite plan gratuit (Firebase Spark) și GitHub Pages

- Firestore gratuit: **20.000 scrieri/zi, 50.000 citiri/zi, 1 GB stocare**. O intrare = ~5 scrieri (adăugare + 4 apăsări de buton), o livrare = 1 scriere; fiecare dispozitiv deschis citește o dată toate documentele la pornire, apoi doar modificările. La câteva zeci de camioane și livrări pe zi ești sub 1% din limită. Nu se stochează poze, deci 1 GB ajunge pentru ani.
- Dacă vreodată se depășește limita zilnică, aplicația afișează o eroare de tip `resource-exhausted` și revine singură a doua zi; nu se pierd date deja salvate.
- GitHub Pages gratuit: 100 GB trafic/lună, 1 GB repo — irelevant pentru o aplicație de 50 KB.

## 6. Modificări ulterioare

Orice modificare în `index.html` urcată pe GitHub ajunge pe toate dispozitivele la următoarea deschidere (service worker-ul ia întotdeauna fișierele noi când există internet). Dacă schimbi fișiere din `sw.js` → `SHELL`, crește și `CACHE` la `lcw-inout-v2` etc.
