# Hackathon - Estrazione Automatica Dati da Fatture

## Obiettivo

Costruire un agente AI che:
1. Legge fatture energia in formato PDF (layout e fornitori diversi)
2. Estrae i dati in formato JSON strutturato
3. Valida e invia i dati all'API del sistema contabile

I dati estratti alimenteranno un sistema a valle di proposizione commerciale che genera offerte concorrenti personalizzate.

## Cosa avete a disposizione

### Fatture PDF di test

Nella cartella `dati/fatture_pdf/` trovate 5 bollette di fornitori diversi:

| File | Fornitore | Cliente | Totale |
|------|-----------|---------|--------|
| `bolletta_a2a_mario_rossi.pdf` | HCT Energia | Mario Rossi | €119,72 |
| `bolletta_ecoverde_giulia_bianchi.pdf` | EcoVerde Energia | Giulia Bianchi | €121,95 |
| `bolletta_blupower_alessandro_conti.pdf` | BluPower Italia | Alessandro Conti | €155,93 |
| `bolletta_soleluce_francesca_moretti.pdf` | SoleLuce Energia | Francesca Moretti | €156,60 |
| `bolletta_voltanova_marco_de_luca.pdf` | VoltaNova Energia | Marco De Luca | €111,65 |

Ogni bolletta ha 2 pagine, layout diverso, colori diversi, dati diversi. Il vostro agente deve funzionare con tutte.

### API del sistema contabile

L'API accetta i dati estratti e li valida. Vi viene fornita una API key per team.

**Base URL**: (fornito dagli organizzatori)

**Autenticazione**: header `x-api-key`
- Gli endpoint `/schema` sono **pubblici** (nessuna API key richiesta)
- Tutti gli altri endpoint (POST, GET lista, GET dettaglio) richiedono `x-api-key`

### Endpoint

| Metodo | Path | Auth | Descrizione |
|--------|------|------|-------------|
| `GET` | `/invoices/schema` | No | Schema JSON atteso + esempio completo |
| `POST` | `/invoices` | Si | Invia fattura estratta |
| `GET` | `/invoices` | Si | Lista le tue fatture |
| `GET` | `/invoices/{id}` | Si | Dettaglio fattura |

### Primo passo: consultare lo schema

```bash
curl https://<API_URL>/invoices/schema | python3 -m json.tool
```

Questo vi restituisce lo schema completo con un esempio di payload valido.

## Schema JSON atteso

```json
{
  "fornitore": "Nome Fornitore S.p.A.",
  "data_emissione": "15/01/2025",
  "numero_bolletta": "2025-XX-00123456",
  "periodo_riferimento": "01/10/2024 - 31/12/2024",
  "cliente": {
    "nome": "Mario Rossi",
    "codice_fiscale": "RSSMRA85M01F205Z",
    "indirizzo": "Via Roma 1, 00100 Roma (RM)"
  },
  "fornitura": {
    "pod_pdr": "IT001E12345678",
    "potenza_impegnata_kw": 3.0,
    "potenza_disponibile_kw": 3.3,
    "tipologia_contratto": "Uso domestico residente",
    "tipo_fornitura": "LUCE"
  },
  "offerta": {
    "nome_offerta": "Offerta Luce Casa",
    "tipo_mercato": "Mercato Libero",
    "prezzo_energia_f1": 0.098,
    "prezzo_energia_f23": 0.085
  },
  "importi": {
    "spesa_energia": 60.76,
    "spesa_trasporto": 28.14,
    "oneri_sistema": 12.50,
    "imposte": 7.44,
    "iva": 10.88,
    "totale": 119.72
  },
  "consumi": {
    "consumo_periodo_kwh": 620,
    "consumo_annuo_kwh": 2450,
    "tipo_lettura": "Effettiva"
  },
  "pagamento": {
    "scadenza": "05/02/2025",
    "modalita": "Domiciliazione bancaria"
  }
}
```

### Campi obbligatori

- `fornitore`, `data_emissione`, `numero_bolletta`, `periodo_riferimento`
- `cliente.nome`, `cliente.codice_fiscale` (16 caratteri), `cliente.indirizzo`
- `fornitura.pod_pdr`, `fornitura.tipologia_contratto`, `fornitura.tipo_fornitura` (LUCE o GAS)
- `offerta.nome_offerta`, `offerta.tipo_mercato`
- `importi.spesa_energia`, `importi.spesa_trasporto`, `importi.oneri_sistema`, `importi.imposte`, `importi.iva`, `importi.totale`
- `consumi.consumo_periodo_kwh`
- `pagamento.scadenza`, `pagamento.modalita`

### Campi opzionali (ma importanti)

I seguenti campi sono opzionali ma fondamentali per il sistema di proposizione commerciale a valle:

- `offerta.prezzo_energia_f1` e `offerta.prezzo_energia_f23` — prezzi per fascia oraria
- `fornitura.potenza_impegnata_kw` e `fornitura.potenza_disponibile_kw`
- `consumi.consumo_annuo_kwh`
- `consumi.tipo_lettura`

## Esempio di invio

```bash
curl -X POST https://<API_URL>/invoices \
  -H "Content-Type: application/json" \
  -H "x-api-key: <LA_TUA_API_KEY>" \
  -d '{
    "fornitore": "HCT Energia S.p.A.",
    "data_emissione": "15/01/2025",
    "numero_bolletta": "2025-LU-00384721",
    "periodo_riferimento": "01/10/2024 - 31/12/2024",
    "cliente": {
      "nome": "Mario Rossi",
      "codice_fiscale": "RSSMRA85M01F205Z",
      "indirizzo": "Via Giuseppe Verdi 42, 20121 Milano (MI)"
    },
    "fornitura": {
      "pod_pdr": "IT001E12345678",
      "potenza_impegnata_kw": 3.0,
      "potenza_disponibile_kw": 3.3,
      "tipologia_contratto": "Uso domestico residente",
      "tipo_fornitura": "LUCE"
    },
    "offerta": {
      "nome_offerta": "HCT Click Luce Verde",
      "tipo_mercato": "Mercato Libero",
      "prezzo_energia_f1": 0.098,
      "prezzo_energia_f23": 0.085
    },
    "importi": {
      "spesa_energia": 60.76,
      "spesa_trasporto": 28.14,
      "oneri_sistema": 12.50,
      "imposte": 7.44,
      "iva": 10.88,
      "totale": 119.72
    },
    "consumi": {
      "consumo_periodo_kwh": 620,
      "consumo_annuo_kwh": 2450,
      "tipo_lettura": "Effettiva"
    },
    "pagamento": {
      "scadenza": "05/02/2025",
      "modalita": "Domiciliazione bancaria"
    }
  }'
```

### Risposta attesa (201)

```json
{
  "id": "a1b2c3d4",
  "status": "accepted",
  "message": "Fattura ricevuta e validata con successo",
  "receivedAt": "2025-02-17T10:30:00",
  "fornitore": "HCT Energia S.p.A.",
  "cliente": "Mario Rossi",
  "totale": 119.72
}
```

### Errore di validazione (422)

Se lo schema non e' rispettato:

```json
{
  "error": "Validazione fallita",
  "details": [
    "Campo obbligatorio mancante: cliente.codice_fiscale",
    "fornitura.tipo_fornitura: deve essere 'LUCE' o 'GAS'"
  ],
  "hint": "Usa GET /schema per vedere lo schema atteso"
}
```

## Suggerimenti

- Partite dall'endpoint `GET /schema` per capire esattamente cosa serve
- Testate prima con una bolletta sola, poi generalizzate
- I PDF hanno layout molto diversi: il vostro agente deve gestirli tutti
- I prezzi per fascia (`prezzo_energia_f1/f23`) sono nella pagina 2 di ogni bolletta
- Il codice fiscale deve essere esattamente 16 caratteri
- `tipo_fornitura` deve essere esattamente `LUCE` o `GAS` (maiuscolo)
- Gli importi devono essere numeri (non stringhe)
