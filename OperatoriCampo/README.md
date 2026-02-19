# Hackathon - Assistente Tecnico per Operatori sul Campo

## Obiettivo

Costruire un agente AI che assiste i tecnici durante gli interventi sul campo:
1. Riceve la scheda intervento assegnata
2. Consulta procedure operative e manuali tecnici per fornire istruzioni passo-passo
3. Risponde a domande tecniche del tecnico durante l'intervento
4. Aggiorna lo stato dell'intervento con note, misurazioni e foto

## Cosa avete a disposizione

### Knowledge base tecnica

**Procedure operative** (`dati/procedure_operative/`) — 5 procedure dettagliate:

| File | Procedura | Codice |
|------|-----------|--------|
| `sostituzione_contatore.md` | Sostituzione contatore elettronico | PRO-EL-001 |
| `verifica_dispersione.md` | Verifica dispersione elettrica | PRO-EL-002 |
| `riparazione_guasto_rete_bt.md` | Riparazione guasto rete BT | PRO-RT-001 |
| `verifica_cabina_mt_bt.md` | Verifica cabina MT/BT | PRO-CB-001 |
| `emergenza_gas.md` | Intervento emergenza fuga gas | PRO-GAS-001 |

Ogni procedura include: prerequisiti, DPI, fasi operative step-by-step, anomalie comuni, valori di riferimento, regole di sicurezza.

**Manuali tecnici** (`dati/manuali/`) — 2 schede tecniche:

| File | Apparecchiatura |
|------|-----------------|
| `contatore_elettronico_enel.md` | Contatore Open Meter monofase (specifiche, codici display, problemi comuni) |
| `cabina_mt_bt.md` | Cabina di trasformazione MT/BT (schema, componenti, valori riferimento, manutenzione) |

### Schede intervento

In `dati/schede_intervento/` trovate 6 interventi assegnati:

| ID | Tipo | Priorita' | Descrizione |
|----|------|-----------|-------------|
| INT-2025-0101 | Sostituzione contatore | Media | Sostituzione contatore a Roma |
| INT-2025-0102 | Verifica dispersione | Alta | Salvavita scatta con pompa di calore |
| INT-2025-0103 | Riparazione rete BT | Critica | Blackout 3 palazzi a Milano |
| INT-2025-0104 | Emergenza gas | Critica | Odore gas vicino caldaia a Padova |
| INT-2025-0105 | Verifica cabina | Alta | Cabina con scintille a Firenze |
| INT-2025-0106 | Bassa tensione | Media | Sfarfallio di zona a Milano |

### API

Stessa API e stessa API key degli altri casi d'uso.

**Base URL**: `https://<API_URL>/v1/`

**Autenticazione**: header `x-api-key` (fornita a ogni team)
- Gli endpoint `/schema` sono **pubblici** (nessuna API key richiesta)
- Tutti gli altri endpoint (POST, GET lista, GET dettaglio) richiedono `x-api-key`

| Metodo | Path | Auth | Descrizione |
|--------|------|------|-------------|
| `GET` | `/interventions/schema` | No | Schema JSON atteso |
| `POST` | `/interventions` | Si | Crea aggiornamento intervento |
| `PUT` | `/interventions/{id}` | Si | Aggiorna intervento esistente |
| `GET` | `/interventions` | Si | Lista interventi |
| `GET` | `/interventions/{id}` | Si | Dettaglio intervento |

```bash
curl https://<API_URL>/interventions/schema | python3 -m json.tool
```

## Schema JSON dell'aggiornamento intervento

```json
{
  "id_intervento": "INT-2025-0102",
  "stato": "completato",
  "note_tecnico": "Verificata dispersione: impianto OK. Problema causato da pompa di calore che supera potenza contrattuale.",
  "esito": "parziale",
  "procedura_seguita": "PRO-EL-002",
  "misurazioni": {
    "tensione_v": 232,
    "isolamento_mohm": 15.4,
    "dispersione_ma": 2.1
  },
  "azioni_eseguite": [
    "Test differenziale: OK",
    "Test per esclusione: scatta solo con circuito pompa di calore",
    "Misura isolamento: 15.4 MOhm (OK)",
    "Misura corrente spunto PDC: 12A"
  ],
  "anomalie_riscontrate": [
    "Potenza contrattuale insufficiente per pompa di calore"
  ],
  "foto_allegate": ["quadro_elettrico.jpg", "targhetta_pdc.jpg"],
  "richiede_followup": "Aumento potenza a 4.5 kW da programmare."
}
```

### Campi obbligatori

- `id_intervento` — ID dalla scheda intervento
- `stato` — da_iniziare, in_corso, completato, sospeso
- `note_tecnico` — note del tecnico

### Campi opzionali (ma valorizzati = punteggio migliore)

- `esito` — risolto, parziale, non_risolto, rinviato
- `procedura_seguita` — codice procedura applicata
- `misurazioni` — valori misurati (tensione, isolamento, dispersione, temperatura, gas)
- `azioni_eseguite` — lista azioni step-by-step
- `anomalie_riscontrate` — problemi trovati
- `foto_allegate` — nomi file foto
- `richiede_followup` — se serve un intervento successivo

## Suggerimenti

- Il vostro agente deve saper consultare le procedure e dare istruzioni passo-passo al tecnico
- Le procedure contengono valori di riferimento: l'agente deve saperli citare (es. "isolamento deve essere > 1 MOhm")
- Per interventi critici (gas, rete), l'agente deve sempre ricordare le regole di sicurezza
- L'agente puo' aggiornare lo stato progressivamente: prima `in_corso`, poi `completato`
- Le misurazioni sono importanti: l'agente dovrebbe chiedere al tecnico di misurarle secondo la procedura
- Bonus: implementare input vocale (Bedrock + Amazon Transcribe) per supporto hands-free
- Bonus: implementare analisi foto (Bedrock multimodale) per verificare visivamente il lavoro
