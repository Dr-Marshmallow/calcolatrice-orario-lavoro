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
   | **BP parziale** | 7:00 | 7:30 | Parziale — 5,60 € |
   | **BP completo** | 9:00 | 9:30 | Completo — 7,00 € |

3. La pagina aggiorna in tempo reale:
   - **Orario di uscita** corrispondente al calcolo scelto;
   - **Permanenza** (tempo totale da passare in sede), **Lavoro effettivo** e **Pausa** scalata;
   - il **buono pasto** maturato (con importo);
   - gli orari di uscita delle due **soglie** buono pasto (parziale e completo),
     evidenziati in verde quando risultano raggiunti.

## Regola della pausa pranzo

È il punto centrale del calcolo:

- Fino a **6 ore e 29 minuti** di permanenza, tutto il tempo conta come lavoro effettivo.
- Dalle **6 ore e 30 minuti** in poi vengono **scalati 30 minuti** di pausa pranzo, che
  non vengono conteggiati come lavoro.

Di conseguenza, per maturare *N* ore di lavoro effettivo (con *N* ≥ 6:30) bisogna
restare in sede *N* + 30 minuti. Esempi con ingresso alle `08:00`:

- 6:00 di lavoro → uscita alle **14:00** (nessuna pausa scalata)
- 7:00 di lavoro → uscita alle **15:30** (7:00 + 0:30 di pausa) → buono **parziale**
- 9:00 di lavoro → uscita alle **17:30** (9:00 + 0:30 di pausa) → buono **completo**

## Buoni pasto

| Tipo | Lavoro effettivo richiesto | Permanenza richiesta | Importo |
|---|---|---|---|
| Parziale | 7:00 | 7:30 | 5,60 € |
| Completo | 9:00 | 9:30 | 7,00 € |

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

