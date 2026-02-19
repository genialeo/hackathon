# Procedura: Verifica dispersione elettrica

**Codice procedura**: PRO-EL-002
**Tempo stimato**: 30-45 minuti
**Personale richiesto**: 1 tecnico abilitato
**DPI obbligatori**: guanti isolanti classe 00, occhiali protettivi, scarpe antinfortunistiche

## Prerequisiti

- Ordine di lavoro con segnalazione cliente (salvavita che scatta)
- Misuratore di isolamento (megger) calibrato
- Pinza amperometrica con funzione dispersione
- Tester digitale

## Fasi operative

### Fase 1 — Anamnesi con il cliente (5 min)
1. Chiedere quando scatta il differenziale (sempre, con specifico elettrodomestico, a orari fissi)
2. Chiedere se ci sono stati lavori recenti sull'impianto
3. Chiedere se il problema e' iniziato dopo l'installazione di un nuovo apparecchio
4. Verificare se il differenziale e' da 30mA (domestico standard)

### Fase 2 — Verifica preliminare (5 min)
1. Aprire il quadro elettrico
2. Verificare stato visivo dei componenti (bruciature, ossidazione)
3. Verificare che il differenziale sia del tipo corretto (tipo A o tipo AC)
4. Premere il tasto TEST del differenziale — deve scattare. Se non scatta: **differenziale guasto**

### Fase 3 — Test per esclusione (15 min)
1. Abbassare TUTTI i magnetotermici dei circuiti
2. Riarmare il differenziale
3. Alzare i magnetotermici UNO ALLA VOLTA, attendendo 30 secondi tra uno e l'altro
4. Se il differenziale scatta con un circuito specifico: il problema e' su quel circuito
5. Se non scatta con nessun circuito singolo ma scatta con tutti alzati: possibile somma di dispersioni

### Fase 4 — Misura isolamento (10 min)
1. Togliere tensione al circuito sospetto
2. Scollegare tutti gli utilizzatori dal circuito
3. Misurare isolamento fase-terra con megger a 500V DC
4. Valore accettabile: **> 1 MOhm**
5. Se < 0.5 MOhm: dispersione confermata sul circuito
6. Se > 1 MOhm con utilizzatori scollegati: il problema e' in un utilizzatore

### Fase 5 — Localizzazione (10 min)
Se dispersione su circuito:
1. Verificare isolamento in ogni scatola di derivazione
2. Controllare punti critici: bagno, cucina, esterno (umidita')
3. Verificare stato cavi nelle canaline

Se dispersione su utilizzatore:
1. Collegare gli utilizzatori uno alla volta
2. Misurare dispersione con pinza amperometrica
3. Dispersione > 15mA su singolo apparecchio: **apparecchio difettoso**

### Fase 6 — Chiusura
1. Comunicare esito al cliente
2. Se problema su impianto fisso: consigliare intervento elettricista privato
3. Se problema su apparecchio: consigliare sostituzione/riparazione
4. Se problema su rete distributore: segnalare al distributore
5. Compilare rapporto intervento
6. Aggiornare stato su sistema: **completato** con note esito

## Valori di riferimento

| Misura | Valore OK | Valore critico |
|--------|-----------|----------------|
| Isolamento fase-terra | > 1 MOhm | < 0.5 MOhm |
| Dispersione singolo apparecchio | < 3.5 mA | > 15 mA |
| Dispersione totale impianto | < 15 mA | > 30 mA (scatta differenziale) |
| Tensione di rete | 220-240V | < 210V o > 250V |
