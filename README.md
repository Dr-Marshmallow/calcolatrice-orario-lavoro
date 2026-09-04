# Calcolatrice Oraria

Pagina web in **file singolo** (HTML + CSS + JS, nessuna dipendenza) per calcolare
l'**orario di uscita** dal lavoro a partire dall'orario di ingresso, e capire quale
**buono pasto** si ottiene.

## Uso

Apri `index.html` in un qualsiasi browser. Non serve installare nulla, non serve un
server web: basta un doppio click sul file.

## Come funziona

1. Inserisci l'**orario di ingresso** (formato `HH:mm`, default `08:00`).
2. Scegli il tipo di calcolo:

   | Pulsante | Lavoro effettivo | Permanenza in sede | Buono pasto |
   |---|---|---|---|
   | **Corta** | 6:00 | 6:00 | nessuno |
   | **BP parziale** | 7:00 | 7:30 | Parziale - 5,60 EUR |
   | **BP completo** | 9:00 | 9:30 | Completo - 7,00 EUR |
   | **Personalizzato** | a scelta | calcolata | secondo le soglie |

3. La pagina aggiorna in tempo reale:
   - **Orario di uscita** corrispondente al calcolo scelto;
   - **Permanenza** (tempo totale da passare in sede), **Lavoro effettivo** e **Pausa** scalata;
   - il **buono pasto** maturato (con importo);
   - gli orari di uscita delle due **soglie** buono pasto (parziale e completo),
     evidenziati in verde quando risultano raggiunti.

### Calcolo personalizzato

Selezionando **Personalizzato** compare un campo in cui indicare liberamente le ore
(0-23) e i minuti (0-59) di **lavoro effettivo** desiderati. Da li in poi il calcolo e
identico ai preset: viene applicata la stessa regola della pausa pranzo e il buono pasto
viene assegnato automaticamente se il lavoro effettivo raggiunge le soglie.

Valori fuori intervallo o non numerici vengono riportati automaticamente entro i limiti,
quindi il risultato mostrato e sempre valido.

## Regola della pausa pranzo

E il punto centrale del calcolo:

- Fino a **6 ore e 29 minuti** di permanenza, tutto il tempo conta come lavoro effettivo.
- Dalle **6 ore e 30 minuti** in poi vengono **scalati 30 minuti** di pausa pranzo, che
  non vengono conteggiati come lavoro.

Di conseguenza, per maturare *N* ore di lavoro effettivo (con *N* maggiore o uguale a
6:30) bisogna restare in sede *N* + 30 minuti. Esempi con ingresso alle `08:00`:

| Lavoro effettivo | Permanenza | Uscita | Buono pasto |
|---|---|---|---|
| 6:00 | 6:00 | 14:00 | nessuno |
| 6:29 | 6:29 | 14:29 | nessuno |
| 6:30 | 7:00 | 15:00 | nessuno |
| 7:00 | 7:30 | 15:30 | parziale |
| 9:00 | 9:30 | 17:30 | completo |
| 10:45 | 11:15 | 19:15 | completo |

## Buoni pasto

| Tipo | Lavoro effettivo richiesto | Permanenza richiesta | Importo |
|---|---|---|---|
| Parziale | 7:00 | 7:30 | 5,60 EUR |
| Completo | 9:00 | 9:30 | 7,00 EUR |

## Dettagli tecnici

- Un solo file: `index.html` (markup, stile e logica inclusi).
- Nessuna libreria esterna, nessun build step, funziona anche offline.
- Layout responsive, pensato anche per smartphone.
- **Tema chiaro/scuro automatico**, in base alle preferenze del sistema
  (`prefers-color-scheme`).
- Tutti i calcoli sono fatti in **minuti interi** dalla mezzanotte; se l'uscita cade
  il giorno successivo viene mostrato il suffisso `+1g`.
- Costanti configurabili all'inizio dello `<script>`:

  ```js
  var PAUSA = 30;          // minuti scalati per la pausa pranzo
  var SOGLIA_PAUSA = 390;  // 6:30 di permanenza: da qui in poi la pausa viene scalata
  var T_PARZIALE = 420;    // 7:00 di lavoro effettivo -> 5,60 EUR
  var T_COMPLETO = 540;    // 9:00 di lavoro effettivo -> 7,00 EUR
  ```

- Funzioni chiave della logica:
  - `permanenzaPer(lavoro)`: converte il lavoro effettivo desiderato nella permanenza
    necessaria (aggiunge la pausa oltre la soglia);
  - `lavoroDa(permanenza)`: la conversione inversa;
  - `lavoroCustom()`: legge e normalizza ore e minuti del calcolo personalizzato.

## Struttura del progetto

- `index.html` - l'applicazione completa
- `README.md` - questo file
