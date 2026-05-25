# Le Coin POS — Configurazione Firebase (Stadio 1)

Questa guida ti porta dalla creazione del progetto Firebase fino all'app online. Pensata per principianti. **Segui l'ordine esatto.**

L'app che stiamo costruendo è la versione **vera**: multifiliale, multiutente, con ruoli, foto prodotti, bilingue arabo/inglese. Firebase gestisce il database condiviso (Firestore), il login (Authentication) e le foto (Storage).

---

## PARTE 1 — Crea il progetto Firebase (10 min)

1. Vai su **https://console.firebase.google.com**
2. Accedi con un account Google
3. **"Aggiungi progetto"** → nome: `lecoin-pos` → continua
4. Disabilita Google Analytics (non serve ora) → **Crea progetto**
5. Aspetta ~1 minuto, poi **Continua**

### Abilita Authentication
6. Menu a sinistra → **Authentication** → **Inizia**
7. Scheda **Sign-in method** → abilita **Email/Password** → Salva

### Abilita Firestore Database
8. Menu a sinistra → **Firestore Database** → **Crea database**
9. Scegli **Modalità produzione** (le regole le mettiamo noi dopo)
10. Posizione: **eur3 (europe-west)** → Abilita

### Storage — NON serve in questa versione ✅
In questa versione le foto sono salvate **dentro Firestore** (come testo compresso), quindi **NON devi attivare Storage né il piano Blaze**. Salti completamente questo passaggio. Tutto resta sul piano gratuito.

(Più avanti, se vorrai foto in alta risoluzione o un catalogo molto grande, si potrà passare a Storage attivando Blaze — senza rifare nulla.)

---

## PARTE 2 — Prendi la configurazione (5 min)

1. In alto a sinistra, clicca l'**ingranaggio ⚙** → **Impostazioni progetto**
2. Scorri fino a **"Le tue app"** → clicca l'icona **web `</>`**
3. Nickname app: `lecoin-web` → **Registra app** (NON attivare Hosting ora)
4. Comparirà un blocco di codice con `firebaseConfig`. **Copia i valori** — ti servono tra poco. Sono tipo:

```js
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "lecoin-pos.firebaseapp.com",
  projectId: "lecoin-pos",
  storageBucket: "lecoin-pos.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123:web:abc"
};
```

5. **Apri il file `index.html`** che ti ho dato, cerca in alto la sezione `firebaseConfig` e **incolla i tuoi valori** al posto dei segnaposto. Salva.

---

## PARTE 3 — Imposta le regole di sicurezza (5 min)

Le regole impediscono che un cassiere veda i dati di un'altra filiale o modifichi cose che non deve. **Sono obbligatorie.**

### Regole Firestore
1. Firebase Console → **Firestore Database** → scheda **Regole**
2. Cancella tutto e incolla il contenuto del file **`firestore.rules`** che ti ho dato
3. **Pubblica**

### Regole Storage — NON servono in questa versione ✅
Le foto vanno in Firestore, quindi non devi configurare regole Storage. Salta questo passaggio.

---

## PARTE 4 — Crea la prima filiale e il primo admin (10 min)

L'app ha bisogno di almeno una filiale e di te come amministratore prima di poter funzionare.

### Crea il tuo utente di login
1. Firebase Console → **Authentication** → **Users** → **Aggiungi utente**
2. Email: la tua (es. `amir@lecoin.com`), Password: una sicura → Aggiungi
3. **Copia lo User UID** che compare (stringa lunga tipo `a1b2c3...`) — ti serve subito

### Crea i dati iniziali in Firestore
4. Firebase Console → **Firestore Database** → **Avvia raccolta**

**Crea la collezione `branches`** (filiali):
- ID raccolta: `branches` → Avanti
- ID documento: clicca **"ID automatico"** (copia l'ID generato, es. `branch001`)
- Campi:
  - `nameAr` (string) = `الفرع الرئيسي`
  - `nameEn` (string) = `Main Branch`
  - `address` (string) = il tuo indirizzo
  - `active` (boolean) = `true`
- Salva. **Annota l'ID del documento filiale.**

**Crea la collezione `users`**:
- ID raccolta: `users` → Avanti
- ID documento: **incolla lo User UID** copiato prima (DEVE essere identico)
- Campi:
  - `name` (string) = il tuo nome
  - `email` (string) = la tua email
  - `role` (string) = `admin`
  - `branchIds` (array) → aggiungi un elemento (string) = l'ID filiale annotato
  - `active` (boolean) = `true`
- Salva

Ora hai: una filiale, e te stesso come admin collegato a quella filiale.

---

## PARTE 5 — Metti l'app online (10 min)

### Opzione A — Firebase Hosting (consigliata, integrata)
Richiede di installare uno strumento. Se non te la senti, usa l'Opzione B.

### Opzione B — GitHub Pages (più semplice se conosci già GitHub)
1. Crea un repository GitHub `lecoin-pos`
2. Carica `index.html`, `manifest.json`, `sw.js`, e le icone
3. Settings → Pages → Deploy from branch → main → root → Save
4. Avrai un link `https://tuonome.github.io/lecoin-pos/`

### ⚠️ PASSAGGIO CRITICO — autorizza il dominio
Firebase blocca il login da domini non autorizzati. Dopo il deploy:
1. Firebase Console → **Authentication** → **Settings** → **Authorized domains**
2. **Aggiungi dominio** → inserisci il tuo dominio (es. `tuonome.github.io`)
3. Salva

Senza questo, il login darà errore "unauthorized domain".

---

## PARTE 6 — Primo accesso

1. Apri il link dell'app
2. Fai login con email/password creati nella Parte 4
3. Essendo admin con una filiale, entri direttamente
4. Vai in **Categorie** → crea le tue categorie (es. Crêpes, Caffetteria...)
5. Vai in **Prodotti** → aggiungi prodotti con foto e descrizione

---

## Aggiungere altri utenti e filiali (dopo)

**Nuova filiale:** Firestore → collezione `branches` → aggiungi documento (come nella Parte 4).

**Nuovo utente** (es. un cassiere):
1. Authentication → Aggiungi utente (email + password) → copia UID
2. Firestore → `users` → aggiungi documento con ID = UID
3. Campi: `name`, `email`, `role` (`admin` / `manager` / `cashier`), `branchIds` (le filiali a cui ha accesso), `active` = true

**I ruoli:**
- **admin** — vede tutte le filiali, gestisce utenti, catalogo, tutto
- **manager** — gestisce catalogo, magazzino, report e turni della/e sua/e filiale/i
- **cashier** — solo vendite e i propri turni nella sua filiale

---

## Risoluzione problemi

**"unauthorized domain"** → manca il passaggio della Parte 5 (autorizza dominio in Authentication → Settings).

**"Missing or insufficient permissions"** → le regole Firestore non sono pubblicate, oppure il tuo documento `users` non esiste / l'UID non corrisponde.

**Login ok ma schermata vuota** → controlla che il documento `users` abbia `role` e `branchIds` corretti, e che esista almeno una filiale in `branches`.

**Le foto non si caricano** → in questa versione le foto vanno in Firestore (non Storage). Se una foto è troppo grande l'app avvisa: scegline una più piccola, viene compressa automaticamente.

---

Quando hai completato il setup e l'app dello Stadio 1 funziona (login, cambio lingua, cambio filiale, gestione categorie e prodotti con foto), torna da me: costruiamo lo Stadio 2 (la Cassa con foto in vendita) sopra questa fondazione.
