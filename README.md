# 🚗 CarCare AI – Întreținere Mașină

> Aplicație web single-file pentru gestionarea întreținerii auto, cu planificator de revizii, scor de sănătate al vehiculului și generator de prompturi AI pentru sfaturi personalizate.

---

## 📋 Cuprins

- [Descriere](#descriere)
- [Funcționalități](#funcționalități)
- [Tehnologii folosite](#tehnologii-folosite)
- [Cum rulezi aplicația](#cum-rulezi-aplicația)
- [Deploy pe GitHub Pages](#deploy-pe-github-pages)
- [Structura datelor](#structura-datelor)
- [Prompturi folosite în Claude](#prompturi-folosite-în-claude)
- [Limitări cunoscute](#limitări-cunoscute)
- [Autor](#autor)

---

## Descriere

**CarCare AI** este o aplicație web completă, construită într-un singur fișier `index.html`, care funcționează integral offline, fără server sau bază de date externă. Toate datele sunt salvate local în `localStorage`-ul browserului.

Aplicația permite proprietarilor de autovehicule să:
- urmărească istoricul intervențiilor service
- planifice reviziile viitoare pe baza regulilor de mentenanță
- calculeze un scor de sănătate al mașinii
- genereze rapoarte de costuri cu grafice
- creeze prompturi detaliate pentru consultanță AI personalizată

---

## Funcționalități

### ⚡ Dashboard
- Afișare vehicul activ cu km curent și scor sănătate
- **Quick Actions**: Update kilometraj, Adaugă intervenție, Export PDF
- Statistici rapide: scor sănătate, nr. revizii depășite, revizii scadente, total cheltuieli
- Grafic ring animat pentru scorul de sănătate (0–100)
- Top 3 riscuri cu severitate codificată color
- Ultimele 6 intervenții înregistrate

### 📅 Planner
- Tabel cu toate regulile de mentenanță și statusul lor
- Filtre: Toate / Depășite (OVERDUE) / Curand (DUE_SOON) / OK
- Calcul automat `due_km` și `due_date` pe baza ultimei intervenții
- Logică status:
  - **OVERDUE**: km curent > due_km SAU data curentă > due_date
  - **DUE_SOON**: diferență ≤ 500 km SAU ≤ 14 zile
  - **OK**: în afara pragurilor
- Notificări vizuale cu posibilitate de simulare alert browser
- Buton rapid "Adaugă" direct din notificare → deschide formularul prefiltrat

### 🔧 Service Log
- Tabel complet al intervențiilor sortate descrescător
- Formular adăugare/editare cu validări inline pe câmpuri
- Câmpuri: dată, km, categorie, titlu, cost, note
- Editare și ștergere cu confirmare modală
- 10 categorii predefinite: ulei, filtre, frâne, anvelope, ITP, etc.

### 📊 Reports
- Filtrare după interval de date (de la / până la)
- Statistici: total costuri, nr. intervenții, cost mediu
- **Grafic bar**: cost pe lună (canvas, implementat manual)
- **Grafic pie**: cost pe categorie (canvas, implementat manual)
- Tabel detaliat cu intervențiile filtrate

### 🤖 AI Assistant
- Formular parametri: tip utilizare, km/lună estimați, observații
- Generator prompt detaliat în română pentru Claude / alt LLM
- Prompt include: date vehicul, ultimele 8 intervenții, status planificator complet
- Cere output structurat: urgent, recomandat, buget min/med/max, plan 3 luni
- Buton Copy cu fallback pentru browsere fără Clipboard API

### Altele
- **Export JSON**: salvare completă a datelor locale
- **Import JSON**: restaurare din fișier backup
- **Export PDF**: raport complet generat cu html2pdf.js (CDN)
- **Reset demo**: restaurare date seed cu confirmare
- **Date demo seed**: vehicul Dacia Logan 2019 + 6 intervenții istorice

---

## Tehnologii folosite

| Tehnologie | Scop | Mod de includere |
|---|---|---|
| HTML5 + CSS3 | Structură și stilizare | Inline în fișier |
| JavaScript ES2020 | Logică aplicație | Inline în fișier |
| localStorage API | Persistență date | Browser nativ |
| Canvas API | Grafice bar + pie | Browser nativ |
| html2pdf.js 0.10.1 | Generare PDF | CDN Cloudflare |
| Google Fonts (Syne + JetBrains Mono) | Tipografie | CDN Google |

> **Notă:** Aplicația funcționează și fără conexiune la internet dacă fonturile sunt deja în cache. Singurul script extern este html2pdf.js (necesar pentru export PDF).

---

## Cum rulezi aplicația

### Direct în browser (cea mai simplă)

```
1. Descarcă fișierul index.html
2. Dă dublu-click pe el
3. Se deschide în browser – gata!
```

Nu este nevoie de:
- server local
- Node.js / Python / alt runtime
- instalare dependențe
- configurare


### Resetare date

Dacă vrei să ștergi toate datele și să începi de la zero:
- Apasă butonul **↺ Reset** din sidebar (stânga jos)
- SAU în consola browser-ului: `localStorage.removeItem('carcare_v2'); location.reload();`

---

## Deploy pe GitHub Pages

GitHub Pages permite publicarea aplicației gratuit, accesibilă de pe orice dispozitiv.

### Pași:

**1. Creează un repository nou pe GitHub**
```
- Mergi la github.com → New repository
- Nume sugestiv: carcare-ai sau carcare-maintenance
- Vizibilitate: Public (necesar pentru Pages gratuit)
- Apasă "Create repository"
```

**2. Încarcă fișierul**
```bash
# Opțiunea A – prin interfața web GitHub:
# Drag & drop fișierul index.html în repository

# Opțiunea B – prin Git:
git init
git add index.html
git commit -m "Initial commit: CarCare AI app"
git branch -M main
git remote add origin https://github.com/USERNAME/carcare-ai.git
git push -u origin main
```

**3. Activează GitHub Pages**
```
- Mergi la repository → Settings → Pages
- Source: "Deploy from a branch"
- Branch: main → / (root)
- Apasă Save
```

**4. Accesează aplicația**
```
După ~2 minute, aplicația este disponibilă la:
https://USERNAME.github.io/carcare-ai/
```

### Actualizare după modificări:
```bash
git add index.html
git commit -m "Update: descriere modificare"
git push
# GitHub Pages se actualizează automat în ~1 minut
```

> **Atenție:** Datele rămân salvate local în browserul fiecărui utilizator (localStorage). Nu există sincronizare între dispozitive.

---

## Structura datelor

Toate datele sunt stocate ca JSON în `localStorage` sub cheia `carcare_v2`:

```json
{
  "vehicle": {
    "make": "Dacia",
    "model": "Logan",
    "year": 2019,
    "fuel": "diesel",
    "plate": "AG-01-XYZ",
    "currentKm": 87500,
    "usage": "mixed"
  },
  "serviceRules": [
    {
      "id": "oil_change",
      "name": "Schimb ulei",
      "intervalKm": 10000,
      "intervalMonths": 12,
      "severity": 2,
      "category": "oil_change"
    }
  ],
  "serviceEvents": [
    {
      "id": "ev1",
      "date": "2023-11-10",
      "km": 80000,
      "category": "oil_change",
      "title": "Schimb ulei Castrol 5W40",
      "cost": 250,
      "notes": "Ulei + filtru Mann"
    }
  ],
  "settings": {
    "dueSoonKm": 500,
    "dueSoonDays": 14
  }
}
```

### Reguli de mentenanță predefinite

| Categorie | Interval km | Interval luni | Severitate |
|---|---|---|---|
| oil_change | 10.000 | 12 | 2 |
| air_filter | 15.000 | 12 | 1 |
| cabin_filter | 15.000 | 12 | 1 |
| brake_fluid | – | 24 | 2 |
| timing_belt | 60.000 | 48 | 3 |
| tires | 40.000 | 36 | 2 |
| itp | – | 24 | 3 |

### Calcul scor sănătate

Scorul pornește de la 100 și se reduce:

| Status | Severitate 1 | Severitate 2 | Severitate 3 |
|---|---|---|---|
| DUE_SOON | -5 pct | -10 pct | -15 pct |
| OVERDUE | -10 pct | -20 pct | -30 pct |
| OVERDUE >2000 km / >30 zile | +10 pct penalizare suplimentară | | |

---

## Prompturi folosite în Claude

Această aplicație a fost construită cu ajutorul Claude (Anthropic). Mai jos sunt descrise principalele sesiuni de prompting utilizate în proces:

### Prompt 1 – Generare aplicație de bază

**Scop:** Crearea structurii inițiale a aplicației single-file.

```
Creează un single-file web app într-un singur fișier index.html
(totul inline: HTML + CSS + JS). Aplicația se numește "CarCare AI".
Funcționează offline, folosește LocalStorage, UI dark dashboard cu cards.
[+ specificații complete model date, logică planner, health score]
```

**Rezultat:** Fișier index.html funcțional cu toate cele 5 secțiuni, grafice canvas, export PDF și generator prompt AI.

---

### Prompt 2 – Îmbunătățire UI/UX și stabilitate

**Scop:** Rafinarea designului și adăugarea funcționalităților lipsă.

```
Am deja un index.html single-file pentru CarCare AI. Te rog să-l
îmbunătățești ca UI/UX și stabilitate, păstrând tot într-un singur fișier.
Adaugă Quick Actions pe Dashboard: Update km, Add service, Export PDF.
Adaugă Reset demo data. Îmbunătățește validările și toast-urile.
```

**Rezultat:** Design rafinat cu tokens CSS consistente, validări inline cu evidențiere câmpuri invalide, modal de confirmare generic, Quick Actions cards animate.


## Limitări cunoscute

- **localStorage limitat** la ~5-10 MB per domeniu (suficient pentru sute de intervenții)
- **Fără sincronizare** între dispozitive sau utilizatori
- **Fără autentificare** – oricine are acces la browser poate vedea datele
- **Export PDF** necesită conexiune internet pentru a încărca html2pdf.js de pe CDN
- **Fonturile Google** necesită conexiune la prima încărcare (se cachează ulterior)
- **Fără notificări push** reale – simularea notificărilor folosește `alert()` nativ
- **Date demo fixe** – vehiculul seed este Dacia Logan 2019 (poate fi editat manual)

---

## Autor

Proiect realizat cu ajutorul **Claude AI** (Anthropic) ca instrument de asistență în dezvoltare.

Aplicație destinată uzului personal / educațional.

---

*Ultima actualizare: 2026*
