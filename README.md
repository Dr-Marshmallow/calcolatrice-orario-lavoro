# Calcolatrice Orario Lavoro

**[▶ Try now](https://dr-marshmallow.github.io/calcolatrice-orario-lavoro)**

Pagina web in file singolo, senza dipendenze, per sapere a colpo d'occhio quanto
straordinario hai fatto, che buono pasto hai maturato e a che ora devi uscire per
ottenerlo.

Apri `index.html` in un browser: non serve altro, funziona anche offline.

## Come funziona

Si compilano tre cose:

- **Ingresso** - vuoto all'apertura, la prima selezione parte dalle 8:00.
- **Uscita** - preimpostata sull'ora corrente; il pulsante *Adesso* ce la riporta,
  cambiandola si simula un'uscita diversa.
- **Turno** - quello che avresti dovuto fare:

  | Pulsante | Lavoro effettivo | In sede |
  |---|---|---|
  | **Corta** | 6:00 | 6:00 |
  | **7:12** | 7:12 | 7:42 |
  | **Lunga** | 9:00 | 9:30 |

Sotto, un riquadro aggiorna in tempo reale due righe:

1. **Lo straordinario**: `+H:mm` in verde se sei oltre il turno, `-H:mm` in
   arancione se sei sotto.
2. **I tre traguardi**, uno per colonna: completare il turno, prendere il buono
   da 5,60 EUR, prendere quello da 7,00 EUR. Ogni colonna mostra l'orario di
   uscita che serve e quanto manca ad arrivarci (`tra H:mm`, oppure
   `✓ raggiunto` e la colonna diventa verde).

Finche l'ingresso non c'e, gli orari restano visibili ma smorzati; lo stesso
vale per i conteggi `tra ...` finche manca l'uscita.

Lo straordinario si conta sempre sul lavoro effettivo, mai sulla permanenza. Esempi
con turno **Lunga** (9:00 di lavoro, 9:30 in sede) e ingresso `08:00`:

| Uscita | Permanenza | Lavoro effettivo | Straordinario |
|---|---|---|---|
| 17:30 | 9:30 | 9:00 | 0:00 |
| 18:15 | 10:15 | 9:45 | +0:45 |
| 17:00 | 9:00 | 8:30 | -0:30 |

## La pausa pranzo

E la regola che governa tutto il resto:

- fino a **6:29** di permanenza, tutto il tempo conta come lavoro effettivo;
- dalle **6:30** in poi vengono **scalati 30 minuti**, che non contano come lavoro.

Quindi per maturare N ore di lavoro effettivo (con N da 6:30 in su) bisogna restare
in sede N + 30 minuti. Vale anche per gli orari di uscita proposti: sono permanenze,
la mezz'ora e gia compresa.

| Buono pasto | Lavoro effettivo | In sede |
|---|---|---|
| 5,60 EUR | 7:00 | 7:30 |
| 7,00 EUR | 9:00 | 9:30 |

Esempi con ingresso alle `08:00`:

| Lavoro effettivo | Permanenza | Uscita | Buono pasto |
|---|---|---|---|
| 6:00 | 6:00 | 14:00 | nessuno |
| 6:29 | 6:29 | 14:29 | nessuno |
| 6:30 | 7:00 | 15:00 | nessuno |
| 7:00 | 7:30 | 15:30 | 5,60 EUR |
| 9:00 | 9:30 | 17:30 | 7,00 EUR |
| 10:45 | 11:15 | 19:15 | 7,00 EUR |

L'uscita non puo essere anteriore all'ingresso: in quel caso il campo viene
segnalato in rosso e il calcolo non parte. Gli orari proposti che cadono dopo la
mezzanotte portano il suffisso `+1g`.

## Dettagli tecnici

Tutto sta in `index.html`: markup, stile e logica. Nessuna libreria, nessun build,
tema chiaro/scuro automatico, layout adatto anche al telefono.

Per cambiare i numeri, in cima allo `<script>`:

- `PAUSA` e `SOGLIA_PAUSA` - i 30 minuti di pausa e la soglia delle 6:30;
- `BUONI` - soglie, importi e nomi dei buoni pasto, da cui si generano anche i
  titoli delle colonne;
- i turni sono i `value` dei radio `name="turno"` nel markup.
