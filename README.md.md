# Le Coin POS — Guida al Deploy su GitHub

Questa è un'app POS funzionante per Le Coin: **Cassa + Anagrafica Prodotti**, con scanner barcode reale, in arabo, installabile come app sul telefono. I dati sono salvati sul dispositivo (localStorage) — funziona offline, persiste tra i riavvii, è **mono-dispositivo**.

## I file di questo pacchetto

| File | Cosa fa |
|---|---|
| `index.html` | L'app completa (Cassa + Prodotti + scanner) |
| `manifest.json` | Configurazione PWA (nome, icone, colori) |
| `sw.js` | Service worker per il funzionamento offline |
| `icon-192.png` | Icona app 192×192 — **DEVI aggiungerla tu** |
| `icon-512.png` | Icona app 512×512 — **DEVI aggiungerla tu** |

> ⚠️ Le due icone non sono incluse. Crea due PNG quadrati con il logo Le Coin (uno 192×192px, uno 512×512px) e aggiungili alla cartella prima del deploy. Senza, l'app funziona lo stesso ma userà un'icona generica.

---

## METODO A — Deploy in 2 minuti con Netlify (consigliato, il più facile)

Questo metodo non richiede nemmeno GitHub.

1. Metti tutti i file (`index.html`, `manifest.json`, `sw.js`, `icon-192.png`, `icon-512.png`) in **un'unica cartella** sul computer
2. Vai su **https://app.netlify.com/drop**
3. **Trascina l'intera cartella** nella pagina
4. Aspetta 20 secondi → Netlify ti dà un link tipo `https://nome-casuale.netlify.app`
5. **Fatto.** L'app è online via HTTPS (la fotocamera funziona solo via HTTPS, quindi è perfetto)

Per cambiare il nome del link: in Netlify → Site settings → Change site name.

---

## METODO B — Deploy con GitHub Pages

Un po' più passaggi, ma il codice resta versionato su GitHub.

### Passo 1 — Crea un account GitHub
1. Vai su **https://github.com** → Sign up (gratis)

### Passo 2 — Crea un nuovo repository
1. In alto a destra: **+** → **New repository**
2. **Repository name:** `lecoin-pos`
3. Imposta **Public**
4. Spunta **"Add a README file"**
5. Clicca **Create repository**

### Passo 3 — Carica i file
1. Nel repo, clicca **Add file** → **Upload files**
2. Trascina tutti i file: `index.html`, `manifest.json`, `sw.js`, `icon-192.png`, `icon-512.png`
3. In basso: **Commit changes**

### Passo 4 — Attiva GitHub Pages
1. Nel repo: **Settings** (tab in alto)
2. Menu a sinistra: **Pages**
3. Sotto "Build and deployment" → Source: **Deploy from a branch**
4. Branch: **main**, cartella: **/ (root)** → **Save**
5. Aspetta 1-2 minuti. In cima alla pagina apparirà il link:
   `https://TUO-USERNAME.github.io/lecoin-pos/`

### Passo 5 — Apri e installa
1. Apri quel link in **Chrome sul telefono Android**
2. Menu (⋮) → **Aggiungi a schermata Home**
3. L'icona Le Coin appare sul telefono → tap → si apre a schermo intero come un'app vera

---

## Come si usa l'app

### Tab Cassa (الكاشير)
- **Scanner reale:** tocca "تشغيل الكاميرا" (accendi fotocamera) → concedi il permesso → inquadra un barcode
- **Inserimento manuale:** digita il codice (es. `7000005`) e premi إضافة
- **Tocco sui prodotti:** tocca un riquadro per aggiungerlo
- Il carrello si riempie, calcola totale e margine
- "تحصيل" (incassa) → digiti i contanti ricevuti → vedi il resto → "تأكيد البيع"
- Lo stock scende automaticamente, la vendita viene salvata

### Tab Prodotti (المنتجات)
- Vedi valore magazzino e prodotti attivi
- Cerca, filtra (نشط / الكل / غير نشط)
- Tocca un prodotto per modificarlo
- "+ جديد" in basso a sinistra per crearne uno nuovo
- Lo stock non si modifica da qui (solo tramite vendita in questa versione)

Tutti i dati persistono: chiudi l'app, riaprila, i prodotti e le modifiche sono ancora lì.

---

## Limiti di questa versione (sii consapevole)

- **Mono-dispositivo:** i dati stanno solo sul telefono/tablet dove usi l'app. Se apri l'app su un altro dispositivo, parte da zero. Per la sincronizzazione tra più dispositivi serve un backend (Supabase/Firebase) — è il prossimo livello.
- **Solo Cassa + Prodotti:** mancano i moduli Carico magazzino, Turno cassa e Report. Lo stock cala con le vendite ma non c'è ancora il "carico" per rifornirlo da qui (puoi però modificare i prodotti).
- **Nessun login:** chiunque apra il link entra. Va bene per un uso interno su un dispositivo dedicato.
- **Backup:** i dati vivono nel browser del dispositivo. Se cancelli i dati del browser o disinstalli, si perdono. **Fai esportazioni periodiche** (questa versione non ha ancora export — chiedimi di aggiungerlo se ti serve).

---

## Prossimi passi possibili

Quando questa versione ti convince e vuoi crescere, torna da me e possiamo:
1. Aggiungere **export/import dati** (per backup su file)
2. Aggiungere i moduli mancanti (Carico, Turno, Report)
3. Aggiungere **Supabase** per sync multi-dispositivo + login utenti
4. Aggiungere **scontrino WhatsApp**

Ogni passo è incrementale — non devi rifare nulla di quanto già fatto.

---

## Problemi comuni

**"La fotocamera non si apre"**
→ Deve essere HTTPS (Netlify e GitHub Pages lo sono). Concedi il permesso fotocamera quando il browser lo chiede. Su iPhone usa Safari; su Android usa Chrome.

**"L'app non si installa"**
→ Funziona solo da link HTTPS, non aprendo il file localmente. Usa Netlify o GitHub Pages.

**"Ho perso i dati"**
→ I dati sono legati al browser/dispositivo. Non cancellare i dati di navigazione. Per sicurezza vera serve il backend (passo successivo).

**"Voglio cambiare i prodotti iniziali"**
→ Apri `index.html`, trova la sezione `const SEED = [...]` in alto nello script, modifica i prodotti, ri-carica il file. Oppure semplicemente usa il tab Prodotti per aggiungere/modificare (più facile).
