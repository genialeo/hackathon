# Procedure di Intervento - Gestione Guasti

## Livelli di gravita'

| Gravita' | SLA | Descrizione |
|----------|-----|-------------|
| `critica` | 1-6 ore | Pericolo per persone o interruzione totale. Intervento immediato. |
| `alta` | 12 ore | Problema significativo che impatta la vivibilita'. Intervento prioritario. |
| `media` | 24-48 ore | Problema che non impedisce l'uso dell'abitazione. Intervento programmato. |
| `bassa` | 72 ore | Problema minore, gestibile da remoto o programmabile. |

## Escalation automatica

La gravita' va aumentata automaticamente nei seguenti casi:
- **Presenza di persone vulnerabili** (anziani, disabili, bambini piccoli, persone con apparecchiature elettromedicali): +1 livello
- **Condizioni meteo avverse** (ondata di calore, gelo): +1 livello per BLACKOUT
- **Problema esteso a piu' unita' abitative**: +1 livello
- **Segnalazione ripetuta** (stesso cliente, stesso problema nelle ultime 48h): +1 livello

## Procedure per tipologia

### BLACKOUT
1. Verificare se il problema e' individuale o di zona
2. Se individuale: verificare stato contatore e interruttore generale
3. Se di zona: segnalare al distributore locale per verifica rete
4. Assegnare a squadra-pronto-intervento
5. Tempo stimato comunicazione: "Intervento tecnico entro [SLA] ore"

### BASSA_TENSIONE
1. Raccogliere dettagli: da quando, costante o intermittente, quali apparecchi
2. Verificare se ci sono lavori in corso nella zona
3. Programmare intervento di verifica tensione
4. Assegnare a squadra-manutenzione
5. Tempo stimato comunicazione: "Verifica tecnica programmata entro [SLA] ore"

### CONTATORE_DIFETTOSO
1. Raccogliere dettagli: tipo di malfunzionamento, modello contatore se noto
2. Verificare storico letture per anomalie
3. Programmare sostituzione/riparazione contatore
4. Assegnare a squadra-manutenzione
5. Tempo stimato comunicazione: "Intervento tecnico programmato entro [SLA] ore"

### DISPERSIONE
1. Consigliare al cliente di staccare tutti gli elettrodomestici
2. Verificare se il problema persiste senza carichi collegati
3. Se persiste: probabile dispersione impianto, intervento urgente
4. Se non persiste: probabile elettrodomestico difettoso, consigliare elettricista privato
5. Assegnare a squadra-pronto-intervento
6. Tempo stimato comunicazione: "Verifica urgente entro [SLA] ore"

### GUASTO_GAS
1. **PRIORITA' ASSOLUTA** - Verificare sicurezza del cliente
2. Confermare: rubinetto gas chiuso, finestre aperte, no fiamme/scintille
3. Se odore forte: consigliare evacuazione e chiamata VVF 115
4. Assegnare a squadra-emergenza-gas
5. Tempo stimato comunicazione: "Intervento emergenza entro [SLA] ora"

### GUASTO_RETE
1. Verificare sicurezza: cavi a terra, pericolo per passanti
2. Se pericolo imminente: consigliare chiamata VVF 115
3. Segnalare al distributore per intervento su infrastruttura
4. Assegnare a squadra-pronto-intervento
5. Tempo stimato comunicazione: "Intervento sulla rete entro [SLA] ore"

## Template notifica cliente

### Notifica apertura segnalazione
```
Gentile [NOME],
abbiamo ricevuto la sua segnalazione relativa a [TIPO_GUASTO] 
presso [INDIRIZZO].

Numero ticket: [TICKET_ID]
Priorita': [GRAVITA']
Tempo stimato di intervento: entro [SLA] ore

[SE CRITICO] Un nostro tecnico e' gia' stato allertato e la contattera' 
al numero [TELEFONO] per coordinare l'intervento.

[SE NON CRITICO] Un nostro tecnico la contattera' per programmare 
l'intervento.

Per aggiornamenti puo' contattare il numero verde 800 123 456 
comunicando il numero ticket [TICKET_ID].

HCT Energia - Servizio Guasti
```

### Notifica aggiornamento
```
Gentile [NOME],
aggiornamento sulla sua segnalazione [TICKET_ID]:
[MESSAGGIO_AGGIORNAMENTO]

HCT Energia - Servizio Guasti
```
