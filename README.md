# Calcolatrice Oraria

Pagina web in **file singolo** (HTML + CSS + JS, nessuna dipendenza) per calcolare lo
**straordinario** maturato, il **buono pasto** che ne deriva e gli **orari di uscita**
utili: quello del turno teorico e quelli delle due soglie del buono pasto.

Tutto sta in una schermata sola: si inseriscono ingresso, uscita e turno, e la pagina
aggiorna in tempo reale sia il conto di quanto hai gia fatto, sia gli orari da
raggiungere.

## Uso

Apri `index.html` in un qualsiasi browser. Non serve installare nulla, non serve un
server web: basta un doppio click sul file.

## Come funziona

1. Inserisci l'**orario di ingresso** (formato `HH:mm`, default `08:00`) e l'**orario di
   uscita**. L'uscita e preimpostata sull'**ora corrente**, cosi il conto e gia pronto
   per la situazione di adesso; cambiandola si simula un'uscita diversa.
2. Scegli il **turno teorico** (default **Corta**):

   | Pulsante | Lavoro effettivo | Permanenza in sede |
   |---|---|---|
   | **Corta** | 6:00 | 6:00 |
   | **7:12** | 7:12 | 7:42 |
   | **Lunga** | 9:00 | 9:30 |

3. Il riquadro dei risultati mostra la situazione all'orario di uscita indicato:
   - lo **straordinario**: `+H:mm` in verde se hai lavorato oltre il turno, `-H:mm` in
     arancione se sei sotto, `0:00` se sei in pari;
   - **Permanenza**, **Lavoro effettivo** e **Pausa** pranzo realmente scalata;
   - il **buono pasto** maturato, con l'importo.

4. La sezione **Orari di uscita** dice a che ora bisogna uscire per ottenere ciascun
   traguardo, a partire dall'ingresso inserito:

   | Riga | Traguardo |
   |---|---|
   | **Turno teorico** | completare il turno selezionato (nessuno straordinario) |
   | **Buono pasto parziale** | 7:00 di lavoro effettivo - 5,60 EUR |
   | **Buono pasto intero** | 9:00 di lavoro effettivo - 7,00 EUR |

   Le righe gia raggiunte con l'uscita indicata vengono evidenziate in verde.

Lo straordinario e sempre calcolato sul **lavoro effettivo**, non sulla permanenza: la
permanenza reale viene prima depurata della mezz'ora di pausa pranzo (se dovuta), poi
confrontata con le ore di lavoro previste dal turno. Anche gli orari di uscita proposti
tengono conto della pausa: sono permanenze, quindi includono la mezz'ora quando serve.

Esempi con turno **Lunga** (9:00 di lavoro, 9:30 di permanenza) e ingresso `08:00`:

| Uscita | Permanenza | Lavoro effettivo | Straordinario |
|---|---|---|---|
| 17:30 | 9:30 | 9:00 | 0:00 |
| 18:15 | 10:15 | 9:45 | +0:45 |
| 17:00 | 9:00 | 8:30 | -0:30 |

Se l'orario di uscita e anteriore a quello di ingresso il turno viene considerato a
cavallo della mezzanotte (es. `22:00` -> `06:30` = 8:30 di permanenza).

## Regola della pausa pranzo

E il punto centrale del calcolo, e vale ovunque nella pagina:

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
| 9:00 | 9:30 | 17:30 | intero |
| 10:45 | 11:15 | 19:15 | intero |

## Buoni pasto

| Tipo | Lavoro effettivo richiesto | Permanenza richiesta | Importo |
|---|---|---|---|
| Parziale | 7:00 | 7:30 | 5,60 EUR |
| Intero | 9:00 | 9:30 | 7,00 EUR |

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

  I turni teorici sono i valori dei radio `name="turno"` nel markup (`360`, `432`, `540`
  minuti di lavoro effettivo).

- Funzioni chiave della logica:
  - `permanenzaPer(lavoro)`: converte il lavoro effettivo desiderato nella permanenza
    necessaria (aggiunge la pausa oltre la soglia);
  - `lavoroDa(permanenza)`: la conversione inversa;
  - `calcola()`: unica funzione di aggiornamento, richiamata a ogni modifica dei campi;
  - `resetStato(msg)`: svuota il riquadro dei risultati quando un orario non e valido.

## Struttura del progetto

- `index.html` - l'applicazione completa
- `README.md` - questo file
