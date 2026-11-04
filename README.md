# Adzuna_job_finder

# Ricerca lavoro con i dati: raccogli annunci, filtrali, leggi solo quelli che contano

Guida all'installazione e all'uso di tre piccoli script Python per chi cerca lavoro (in particolare all'estero e in ruoli entry-level) e vuole **basare le scelte sui dati** invece che su impressioni.

## Indice

1. [Obiettivo, file e ciclo di lavoro](#1-obiettivo-file-e-ciclo-di-lavoro)
2. [Installazione e cartelle](#2-installazione-e-cartelle)
3. [Come lanciare gli script e cosa fanno le opzioni](#3-come-lanciare-gli-script-e-cosa-fanno-le-opzioni)
4. [Limiti dei dati, regole d'uso e problemi comuni](#4-limiti-dei-dati-regole-duso-e-problemi-comuni)

---

## 1. Obiettivo, file e ciclo di lavoro

### Obiettivo

Cercare lavoro all'estero a mano è lento: decine di siti, annunci ripetuti, requisiti nascosti in fondo al testo. Questi script automatizzano la parte noiosa e lasciano a te quella che richiede giudizio:

- **raccolgono** annunci da una fonte ufficiale (l'API gratuita di Adzuna) per i paesi e i ruoli che scegli;
- **puliscono e filtrano** (doppioni, stage, lingua dell'annuncio, agenzie, stipendio minimo);
- **ti preparano una lista corta** di annunci da leggere davvero, ordinata per quanto assomigliano a quello che cerchi;
- **raccolgono le tue annotazioni** (laurea richiesta, lingue, anni di esperienza, decisione) e le riassumono in un report, così dopo 10–15 annunci letti hai una risposta basata su fatti: *a quanti di questi annunci posso davvero candidarmi con il mio profilo?*

Non è un sistema per candidarsi in automatico e non sostituisce la lettura degli annunci. Serve a decidere **quali** leggere.

### I file

| File | Cosa fa, in soldoni |
|---|---|
| `lezione_b_annunci_adzuna.py` | **Raccolta.** Interroga l'API di Adzuna per un paese e una frase (es. `nl` + "supply chain analyst") e salva gli annunci recenti in un CSV nella cartella `dati/`. |
| `lezione_c_analisi.py` | **Analisi.** Legge tutti i CSV di `dati/`, toglie i doppioni, esclude stage e internship, deduce livello e lingua, applica i tuoi filtri, scrive un report in `report/` e prepara `annotazioni.csv`. |
| `requirements.txt` | Elenco delle librerie Python da installare. |
| `.env.example` | Modello del file `.env` dove vanno le tue chiavi Adzuna. |
| `.gitignore` | Dice a Git cosa **non** caricare su GitHub (chiavi, dati, ambiente virtuale). |

I nomi "lezione A/B/C" vengono dal percorso di apprendimento in cui sono nati: A = primo scraper, B = raccolta da API, C = analisi.

### Il ciclo completo

```
  ┌──────────────────────────────────────────────────────────────┐
  │ 1. B: raccogli    python lezione_b_annunci_adzuna.py nl "..."│
  │       (ripeti per ogni paese e per ogni titolo di lavoro)    │
  │                         ↓                                    │
  │ 2. C: analizza    python lezione_c_analisi.py --solo-inglese │
  │       → report/report_complessivo_<data>.md                  │
  │       → annotazioni.csv (la lista corta)                     │
  │                         ↓                                    │
  │ 3. Tu: leggi gli annunci in cima a annotazioni.csv,          │
  │       apri il link, compila le colonne vuote                 │
  │                         ↓                                    │
  │ 4. C di nuovo → il report riassume le tue annotazioni        │
  │                         ↓                                    │
  │ 5. Decidi: candidarti / forse / scarto. Poi ricomincia da 1  │
  │       dopo qualche giorno (gli annunci cambiano).            │
  └──────────────────────────────────────────────────────────────┘
```

Perché serve il passo 3: l'API restituisce solo i **primi 500 caratteri** di ogni annuncio. Laurea, lingue richieste e anni di esperienza stanno quasi sempre dopo. Gli script non possono (e non devono) leggerli al posto tuo: te li fanno leggere in modo ordinato e ne tengono il conto.

---

## 2. Installazione e cartelle

### 2.1 Cosa ti serve

- **Python 3.9 o più recente** (provato con Python 3.13). Controlla con `python3 --version` (Linux/macOS) o `python --version` (Windows).
- Un **terminale** (Terminale su macOS/Linux; Prompt dei comandi o PowerShell su Windows).
- **Git**, solo se vuoi scaricare il progetto con `git clone` o pubblicare il tuo lavoro su GitHub.
- Un **account gratuito Adzuna** per le chiavi API (vedi 2.4).
- Per aprire `annotazioni.csv`: LibreOffice, Excel o VS Code.

### 2.2 Struttura delle cartelle

Tutti gli script stanno **nella stessa cartella**. B e C cercano `.env`, `dati/`, `report/` e `annotazioni.csv` accanto a sé, quindi funzionano da qualunque cartella li lanci, purché i file siano insieme.

```
ricerca-lavoro/                    <- la cartella del progetto (nome a piacere)
├── README.md                      <- questa guida
├── requirements.txt
├── .env.example
├── .gitignore
├── .env                           <- lo crei tu (chiavi segrete, non va su GitHub)
├── lezione_a_scraping_pratica.py
├── lezione_b_annunci_adzuna.py
├── lezione_c_analisi.py
├── venv/                          <- lo crea Python (ambiente virtuale, non va su GitHub)
├── dati/                          <- la crea lo script B (CSV degli annunci)
├── report/                        <- la crea lo script C (report e CSV puliti)
└── annotazioni.csv                <- lo crea lo script C (le tue annotazioni)
```

### 2.3 Installazione passo per passo

**1. Procurati i file.**

```
git clone <URL-del-repository> ricerca-lavoro
cd ricerca-lavoro
```

(oppure scarica lo zip da GitHub, estrailo e apri il terminale dentro la cartella).

**2. Crea l'ambiente virtuale.** È una cartella `venv/` con una copia isolata di Python, così le librerie del progetto non si mescolano con quelle del resto del computer.

- Linux / macOS: `python3 -m venv venv`
- Windows: `py -m venv venv`

**3. Attivalo.** Va rifatto ogni volta che apri un nuovo terminale.

- Linux / macOS: `source venv/bin/activate`
- Windows (Prompt dei comandi): `venv\Scripts\activate`
- Windows (PowerShell): `venv\Scripts\Activate.ps1`  (se PowerShell lo blocca, usa il Prompt dei comandi)

Quando è attivo, il prompt comincia con `(venv)`. Dentro il venv, `python` e `pip` bastano anche su Linux/macOS (`python3` serve solo per crearlo).

**4. Installa le librerie.**

```
pip install -r requirements.txt
```

Installa `requests` (richieste web), `python-dotenv` (lettura del file `.env`), `pandas` (analisi dei dati) e `beautifulsoup4` (usata solo dalla lezione A).

### 2.4 Le chiavi Adzuna e il file `.env`

1. Registrati gratuitamente su **developer.adzuna.com** e ottieni i due codici `app_id` e `app_key`.
2. Nella cartella del progetto, copia il modello:
   - Linux / macOS: `cp .env.example .env`
   - Windows: `copy .env.example .env`
3. Apri `.env` con un editor di testo e sostituisci i valori:

   ```
   ADZUNA_APP_ID=abc12345
   ADZUNA_APP_KEY=0123456789abcdef0123456789abcdef
   ```

   Una riga per codice, **senza spazi attorno a `=`, senza virgolette**.

Il file `.env` è già nel `.gitignore`: non finirà su GitHub. **Non incollare mai le chiavi in un messaggio, in un issue o in un file committato.** Se per errore le pubblichi, rigenerale dal tuo account Adzuna.

In alternativa al file `.env`, lo script B legge anche le variabili d'ambiente `ADZUNA_APP_ID` e `ADZUNA_APP_KEY` del terminale.

### 2.5 Verifica che tutto funzioni

Con il venv attivo, nella cartella del progetto:

```
python lezione_c_analisi.py --help
```

Deve mostrare l'elenco delle opzioni. Poi prova B con una ricerca piccola (vedi capitolo 3); se i file compaiono in `dati/`, l'installazione è completa.

---

## 3. Come lanciare gli script e cosa fanno le opzioni

Tutti i comandi si lanciano **con il venv attivo, dalla cartella del progetto**.

### 3.1 Lezione A (facoltativa): primo scraper di prova

```
python lezione_a_scraping_pratica.py
```

Scarica fino a 5 pagine da `quotes.toscrape.com` e scrive `citazioni.csv`. Non ha opzioni da riga di comando: in cima al file ci sono due costanti da modificare con un editor:

| Costante | Cosa fa |
|---|---|
| `MAX_PAGINE` | Quante pagine scaricare al massimo (default 5). |
| `AUTHOR` | Se non vuoto, tiene solo le citazioni di quell'autore (default "Marilyn Monroe"). Mettilo a `""` per tenerle tutte. |
| `PAUSA_SECONDI` | Pausa fra una richiesta e l'altra (default 1 secondo, per educazione verso il sito). |

### 3.2 Lezione B: raccogliere annunci

```
python lezione_b_annunci_adzuna.py <paese> "<frase>" [giorni]
```

| Argomento | Significato |
|---|---|
| `<paese>` | Codice paese di Adzuna in minuscolo: `it`, `at`, `ch`, `nl`, `sg`… (vedi nota sotto). Obbligatorio. |
| `"<frase>"` | Il titolo di lavoro da cercare, **tra virgolette**. Viene cercata la **frase esatta**, non le parole sparse: `"supply chain analyst"` non restituisce annunci che contengono "supply" da una parte e "analyst" dall'altra. Obbligatorio. |
| `[giorni]` | Facoltativo. Quanti giorni indietro guardare. Se manca: 60. |

Esempi:

```
python lezione_b_annunci_adzuna.py nl "supply chain analyst"
python lezione_b_annunci_adzuna.py ch "business analyst" 30
```

Cosa produce: un file `dati/annunci_<paese>_<ricerca>_<AAAAMMGG>.csv`, per esempio `dati/annunci_nl_supply_chain_analyst_20261103.csv`. Il nome contiene paese, ricerca e data, quindi **ricerche diverse non si sovrascrivono**. Rilanciare la stessa ricerca lo stesso giorno invece sì, sovrascrive il file del giorno.

Cosa c'è dentro: paese, ricerca, data di raccolta, titolo, azienda, luogo, stipendio minimo e massimo, se lo stipendio è una stima di Adzuna, data di pubblicazione, descrizione (solo ~500 caratteri) e link.

Comportamento da sapere:
- Prende fino a **4 pagine da 50 annunci = 200 annunci** per ricerca e per paese, con una pausa di 1 secondo tra le richieste. Non è tutto il mercato.
- Per coprire più paesi e più ruoli, **lancia il comando più volte**, uno per combinazione.
- Se i codici sono sbagliati (errore 401/403) o il paese non è supportato (errore 404), lo script si ferma con un messaggio in italiano.

> **Nota sui paesi.** Adzuna non pubblica in modo comodo l'elenco dei paesi supportati. Con questo progetto sono stati provati `it`, `at`, `ch`, `nl` e `sg`; altre fonti non ufficiali citano anche `gb`, `us`, `de`, `fr`, `ca`, `au`, `in`, `br`, `pl`, `za`, `mx`. Controlla sempre la documentazione ufficiale. Alcuni paesi (per esempio Lussemburgo, Irlanda, Estonia, paesi nordici) non risultano coperti: lì servono altre fonti, da leggere a mano.

### 3.3 Lezione C: analizzare, filtrare, preparare la lista da leggere

```
python lezione_c_analisi.py [ricerca] [opzioni]
```

Senza argomenti analizza **tutti** i CSV presenti in `dati/`. Esempio completo con tutti i filtri:

```
python lezione_c_analisi.py --solo-inglese --escludi-agenzie --stipendio-min 40000 --paesi nl,at,ch,it
```

#### Le opzioni

| Opzione | Cosa fa | Note |
|---|---|---|
| `ricerca` (testo senza `--`) | Analizza solo le ricerche il cui testo **contiene** quella parola. Es. `"supply chain"`. | Case-insensitive. Se nessuna ricerca corrisponde, lo script lo dice e si ferma. |
| `--solo-inglese` | Tiene solo gli annunci scritti in inglese. | La lingua è **stimata** contando parole comuni nel testo visibile (inglese, olandese, tedesco, francese). Un annuncio in inglese può comunque chiedere olandese o tedesco ai candidati. |
| `--escludi-agenzie` | Toglie le aziende il cui nome contiene una delle parole dell'elenco `AGENZIE` (società di consulenza e agenzie di selezione: Accenture, Randstad, "consult", "recruit"…). | L'elenco è in cima a `lezione_c_analisi.py`: aggiungi o togli nomi. Il riconoscimento è per sottostringa, quindi può sbagliare (falsi positivi e negativi). |
| `--stipendio-min N` | Scarta gli annunci con stipendio **dichiarato** sotto N (annuo). Es. `--stipendio-min 40000`. | Scarta solo stipendi non stimati da Adzuna e plausibili come annuali (tra 15.000 e 300.000). Gli annunci **senza** stipendio restano. Il confronto è fra numeri nominali: **non converte le valute** (40.000 CHF non valgono 40.000 €). |
| `--paesi LISTA` | Tiene solo i paesi indicati, codici separati da virgola **senza spazi**: `nl,at,ch,it`. | Stessi codici usati con B. |
| `--top N` | Quanti annunci mettere in `annotazioni.csv` (i primi N per rilevanza). Default 30. | Vedi 3.4. |
| `--con-stage` | Include anche stage, internship, working student, Praktikum, tirocini. | Di default sono **esclusi** (nei dati visti pagavano troppo poco per vivere da soli all'estero). Il report dice sempre quanti ne ha esclusi. |
| `-h`, `--help` | Mostra l'elenco delle opzioni. | |

Le opzioni si combinano liberamente e il report dice quanti annunci toglie ciascun filtro, per esempio: `solo paesi nl,at: -120; solo annunci in inglese: -80; senza agenzie: -35; stipendio sotto 40,000: -10`.

#### Cosa produce

Nella cartella `report/`, per ogni ricerca presente:

- `report_<ricerca>_<data>.md`: il riepilogo (annunci per paese e livello, competenze citate, stipendi, annunci junior/trainee da leggere, avvertenze sui limiti);
- `dati_puliti_<ricerca>_<data>.csv`: gli annunci unici, con livello e lingua dedotti.

Se hai più ricerche, c'è in più un report **`complessivo`**. Il report complessivo viene sempre generato (anche con una sola ricerca) ed è quello che alimenta `annotazioni.csv`.

Come decide il **livello**: lo deduce solo dal **titolo**: `stage/internship`, `trainee`, `junior`, `senior` (anche lead, manager, head…) oppure `non indicato`. Un titolo senza "junior" può comunque essere adatto a chi parte da zero.

Come toglie i **doppioni**: stesso link raccolto in giorni diversi (tiene la raccolta più recente) e stesso annuncio pubblicato per più sedi (stesso paese, titolo e azienda: conta una volta, la colonna `n_sedi` dice quante).

### 3.4 `annotazioni.csv`: la tua lista corta

Alla prima esecuzione di C si crea `annotazioni.csv` accanto allo script, con **una riga per ognuno dei primi 30 annunci** (cambia con `--top`) in ordine di **rilevanza**. La rilevanza è un punteggio: sale se nel titolo o nel testo compaiono parole come *supply chain, logistic, procurement, planning, operations, process, ERP, SAP, production, SQL, Excel, data, analyst*, e per i livelli junior/trainee e gli annunci in inglese. I pesi sono nella costante `PESI_RILEVANZA` e puoi cambiarli per il tuo ambito di lavoro.

Colonne già compilate dallo script: `link`, `paese`, `azienda`, `titolo`, `tipo_azienda` (segnala "consulenza/agenzia"), `stipendio_annuncio`.

**Colonne da compilare tu**, dopo aver aperto il link e letto l'annuncio intero:

| Colonna | Cosa metterci |
|---|---|
| `laurea` | `si` / `preferibile` / `no` / `non detto` |
| `lingue` | es. "inglese; olandese preferibile" |
| `strumenti` | es. "SAP, Excel, SQL" |
| `anni_esperienza` | solo il numero (0, 1, 2, 3…) |
| `stipendio_letto` | se compare nell'annuncio completo |
| `remoto` | `si` / `no` / `ibrido` |
| `decisione` | `candidarmi` / `forse` / `scarto` |
| `motivo`, `note` | una riga libera |

Procedura:

1. Lancia C: si crea il file.
2. Aprilo con LibreOffice, Excel o VS Code. Il file è in UTF-8: se in Excel gli accenti sono rovinati, usa *Dati → Da testo/CSV* e scegli UTF-8.
3. Parti dalle prime 8–10 righe, apri il link e compila le colonne.
4. **Salva** (mantieni il formato CSV) e rilancia C: il report in fondo riassume le tue annotazioni (decisioni, quante richiedono la laurea, anni di esperienza mediani, elenco delle candidature possibili).

Regole importanti:
- Le righe già presenti **non vengono mai sovrascritte**: rilanciando C con altri dati o altri filtri, lo script aggiunge solo righe **nuove**.
- **Non cancellare righe compilate e non modificare i link**: il link è la chiave con cui lo script riconosce gli annunci (ignora la parte dopo `?`, che cambia a ogni richiesta).
- Per ricominciare da zero, cancella `annotazioni.csv` (perdi le annotazioni).

### 3.5 Parametri che puoi modificare nei file

In cima a `lezione_c_analisi.py` ci sono costanti pensate per essere cambiate con un editor:

| Costante | Cosa controlla |
|---|---|
| `COMPETENZE`, `LINGUE` | Le parole da contare nei testi degli annunci. |
| `AGENZIE` | Le aziende da escludere con `--escludi-agenzie`. |
| `PESI_RILEVANZA` | Quali parole fanno salire un annuncio in `annotazioni.csv`. |
| `SOGLIA_CAMPIONE` | Sotto quanti annunci unici per paese il report avverte "campione troppo piccolo" (default 20). |
| `GIORNI_VECCHIO` | Dopo quanti giorni un annuncio è contato come "vecchio" (default 60). |
| `STIPENDIO_ANNUO_MIN/MAX` | Intervallo (15.000–300.000) in cui un numero è trattato come stipendio annuo plausibile. |

In `lezione_b_annunci_adzuna.py`: `MAX_PAGINE` (default 4), `RISULTATI_PER_PAGINA` (50), `PAUSA_SECONDI` (1.0), `GIORNI_DEFAULT` (60).

### 3.6 Pubblicare il lavoro su GitHub

Con il `.gitignore` incluso, su GitHub finiscono solo script e guida; restano sul tuo computer chiavi (`.env`), ambiente virtuale (`venv/`), annunci raccolti (`dati/`), report (`report/`) e le tue annotazioni personali (`annotazioni.csv`).

```
git init
git add .
git status          # controlla: .env, venv/, dati/, report/ e annotazioni.csv NON devono comparire
git commit -m "Primo commit"
```

Poi crea il repository su GitHub e collegalo con i comandi che GitHub ti mostra. Se `.env` o `venv/` compaiono in `git status`, **fermati**: il `.gitignore` non è nella cartella giusta. Se hai già aggiunto per errore `venv/`, toglilo con `git rm -r --cached venv` (non cancella i file dal disco). Se vuoi tenere pubblici anche i dati o le annotazioni, togli le righe corrispondenti dal `.gitignore`, ma rispetta i termini d'uso di Adzuna sulla ridistribuzione dei dati.

---

## 4. Limiti dei dati, regole d'uso e problemi comuni

### Limiti: leggili prima di fidarti dei numeri

- **Descrizioni troncate a ~500 caratteri.** Competenze, lingue, titoli di studio e anni di esperienza nel report sono contati solo sul testo visibile. Un conteggio **0 non significa "non richiesto"**: significa "non lo vedo". Per questo esiste `annotazioni.csv`.
- **Livello dedotto dal titolo.** "non indicato" vuol dire che il titolo non lo dice.
- **Lingua stimata.** Statistica sulle parole, non una verità.
- **Stipendi non omogenei.** Valute locali (non convertite) e unità diverse (annuale, mensile, in migliaia). Nelle mediane entrano solo i valori fra 15.000 e 300.000; le stime di Adzuna sono escluse.
- **Campione.** Al massimo 200 annunci per paese e per ricerca; sotto 20 annunci unici per paese il report avverte di non trarre conclusioni.
- **Copertura.** Adzuna non copre tutti i paesi né tutti i siti di annunci.
- **Non è tutto il mercato.** Molti ruoli non vengono mai pubblicati su siti di annunci.

### Regole d'uso

- Usa **solo l'API ufficiale**: è il canale che il servizio mette a disposizione. Gli script non scaricano le pagine complete degli annunci e non vanno modificati per farlo: i termini d'uso di Adzuna chiedono di fare le richieste tramite la loro API, e molti siti di annunci (per esempio LinkedIn) vietano lo scraping. Leggi i termini aggiornati prima di cambiare il modo di raccogliere i dati.
- Rispetta i limiti di richieste dell'account gratuito: non togliere la pausa tra le richieste né lanciare decine di ricerche di seguito.
- La lezione A è fatta per un sito creato apposta per esercitarsi. Prima di fare scraping di un altro sito, controlla i suoi termini d'uso e il file `robots.txt`.
- **Le chiavi sono personali e segrete.**

### Problemi comuni

| Messaggio o sintomo | Causa probabile e rimedio |
|---|---|
| `python: command not found` (Linux/macOS) | Usa `python3` per **creare** il venv; a venv attivo `python` funziona. |
| `ModuleNotFoundError: No module named 'pandas'` (o `requests`, `dotenv`…) | Il venv non è attivo, oppure hai saltato `pip install -r requirements.txt`. Attiva il venv (il prompt deve mostrare `(venv)`) e reinstalla. |
| `Mancano ADZUNA_APP_ID / ADZUNA_APP_KEY` | Il file `.env` manca, è nella cartella sbagliata (deve stare accanto agli script) o ha spazi/virgolette attorno a `=`. |
| `Errore 401` / `403` | Chiavi sbagliate o non ancora attive. Ricontrolla `.env`. |
| `Errore 404: il paese ... non è supportato` | Codice paese errato o paese non coperto da Adzuna. |
| `Nessun annuncio trovato...` | Frase troppo specifica o troppo pochi giorni: prova un titolo più comune o `90` giorni. |
| `Nessun file annunci_*.csv trovato` (C) | Non hai ancora lanciato B, oppure `dati/` non è accanto agli script. |
| `nessun annuncio dopo i filtri` (C) | I filtri sono troppo stretti: allentane uno alla volta (il messaggio dice quanti annunci toglie ciascuno). |
| Accenti rovinati in `annotazioni.csv` | Importa il file come UTF-8 (vedi 3.4). |
| Avviso di pip "You may have installed Python packages into your global environment" | Hai installato fuori dal venv: attiva il venv e rifai `pip install -r requirements.txt`. |

### Come adattarlo a te

Gli script sono pensati per essere letti e modificati: cambia `AGENZIE`, `PESI_RILEVANZA` e le liste `COMPETENZE`/`LINGUE` per il tuo settore, lancia B con i titoli di lavoro del tuo campo e i paesi che ti interessano. Il codice è commentato in italiano.

*Nota sull'origine del progetto: nato come esperimento di apprendimento di Python applicato alla ricerca di lavoro; non è un prodotto Adzuna e non è affiliato a Adzuna. Se lo pubblichi, aggiungi una licenza a tua scelta (per esempio MIT).*
