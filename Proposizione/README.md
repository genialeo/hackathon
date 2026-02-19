# Hackathon - Proposizione Commerciale Personalizzata

## Obiettivo

Costruire un agente AI che:
1. Analizza il profilo e lo storico consumi di un cliente
2. Consulta il catalogo offerte e le linee guida commerciali
3. Genera una proposta commerciale personalizzata con stima del risparmio
4. Invia la proposta all'API

## Cosa avete a disposizione

### Dati di test

Nella cartella `dati/` trovate:

**Profili cliente** (`dati/clienti/`) — 8 profili JSON con:
- Dati anagrafici e tipo cliente
- Storico consumi mensili (12 mesi) con ripartizione per fascia oraria
- Offerta e fornitore attuale con prezzi
- Fornitura gas (se presente)
- Note sul cliente (nucleo familiare, esigenze particolari)

| File | Cliente | Tipo | Consumo annuo | Particolarita' |
|------|---------|------|---------------|-----------------|
| `cliente_001_famiglia.json` | Lucia Ferrara | Famiglia 4 persone | 3.020 kWh | Anziano con elettromedicali |
| `cliente_002_single.json` | Giulia Bianchi | Single | 1.315 kWh | Consumi bassi, fuori casa tutto il giorno |
| `cliente_003_pompa_calore.json` | Giuseppe Romano | Famiglia + pompa calore | 2.940 kWh | Distacchi frequenti, potenza insufficiente, ha anche gas |
| `cliente_004_dual_fuel.json` | Alessandro Conti | Coppia | 2.720 kWh + 1.100 Smc | Luce e gas con fornitori diversi |
| `cliente_005_green.json` | Marco De Luca | Coppia eco | 1.390 kWh | Pannelli solari, interessato a rinnovabili |
| `cliente_006_business.json` | Studio Legale Martini | Business | 5.080 kWh | Partita IVA, consumi concentrati in F1 |
| `cliente_007_pensionata.json` | Anna Maria Colombo | Pensionata | 1.440 kWh + 650 Smc | Reddito limitato, in tutela, ha anche gas |
| `cliente_008_famiglia_grande.json` | Roberto Esposito | Famiglia 6 persone | 4.410 kWh + 1.400 Smc | Consumi molto elevati, ha anche gas |

**Catalogo offerte** (`dati/catalogo_offerte.json`) — 8 offerte HCT Energia:
- Luce prezzo fisso, variabile, green, famiglia, pompa di calore, business
- Gas prezzo fisso
- Dual fuel luce+gas
- Ogni offerta ha: prezzi per fascia, quota fissa, durata, vincoli, bonus, tipo cliente ammesso

**Linee guida commerciali** (`dati/linee_guida_commerciali.md`) — Regole di business:
- Regole per tipo cliente (famiglia, single, pompa calore, business, ecc.)
- Regole di pricing e calcolo risparmio
- Regole di comunicazione e struttura proposta
- Disclaimer obbligatori

### API

Stessa API e stessa API key degli altri casi d'uso.

**Base URL**: (fornito dagli organizzatori)

**Autenticazione**: header `x-api-key`
- Gli endpoint `/schema` sono **pubblici** (nessuna API key richiesta)
- Tutti gli altri endpoint (POST, GET lista, GET dettaglio) richiedono `x-api-key`

| Metodo | Path | Auth | Descrizione |
|--------|------|------|-------------|
| `GET` | `/proposals/schema` | No | Schema JSON atteso + esempio |
| `POST` | `/proposals` | Si | Invia proposta commerciale |
| `GET` | `/proposals` | Si | Lista le tue proposte |
| `GET` | `/proposals/{id}` | Si | Dettaglio proposta |

### Primo passo: consultare lo schema

```bash
curl https://<API_URL>/proposals/schema | python3 -m json.tool
```

## Schema JSON della proposta

```json
{
  "codice_cliente": "804.291.337",
  "nome_cliente": "Lucia Ferrara",
  "analisi_consumi": {
    "consumo_annuo_kwh": 3020,
    "fascia_prevalente": "F1",
    "trend": "stabile",
    "note_analisi": "Consumi elevati in estate e inverno. Picco agosto 340 kWh."
  },
  "situazione_attuale": {
    "offerta_attuale": "EcoVerde Luce Sicura",
    "fornitore_attuale": "EcoVerde Energia S.p.A.",
    "spesa_annua_stimata_eur": 385.40,
    "potenza_kw": 3.0,
    "tipo_fornitura": "LUCE"
  },
  "offerte_proposte": [
    {
      "codice_offerta": "LUCE-FAMIGLIA",
      "nome_offerta": "HCT Luce Famiglia XL",
      "spesa_annua_stimata_eur": 341.20,
      "risparmio_annuo_eur": 44.20,
      "motivazione_offerta": "Sconto progressivo oltre 2500 kWh/anno."
    }
  ],
  "risparmio_annuo_stimato_eur": 44.20,
  "azioni_consigliate": [
    "Valutare aumento potenza a 4.5 kW"
  ],
  "motivazione": "Famiglia con consumi elevati. LUCE-FAMIGLIA offre risparmio di 44.20 EUR/anno."
}
```

### Campi obbligatori

- `codice_cliente`, `nome_cliente`
- `analisi_consumi.consumo_annuo_kwh`
- `situazione_attuale.offerta_attuale`, `.fornitore_attuale`, `.spesa_annua_stimata_eur`, `.tipo_fornitura`
- `offerte_proposte` — lista di 1-3 offerte
- `risparmio_annuo_stimato_eur`
- `motivazione`

### Campi opzionali (ma valorizzati = punteggio migliore)

- `analisi_consumi.fascia_prevalente`, `.trend`, `.note_analisi`, `.consumo_annuo_smc`
- `situazione_attuale.potenza_kw`
- Dentro ogni offerta proposta: `codice_offerta`, `nome_offerta`, `spesa_annua_stimata_eur`, `risparmio_annuo_eur`, `motivazione_offerta`
- `azioni_consigliate` — suggerimenti aggiuntivi (aumento potenza, dual fuel, ecc.)

## Suggerimenti

- Partite dal catalogo offerte e dalle linee guida: sono il "cervello" commerciale del vostro agente
- Il calcolo del risparmio deve essere coerente: `spesa_attuale - spesa_proposta = risparmio`
- Rispettate i vincoli delle offerte: potenza minima, tipo cliente, tipo fornitura
- Non proponete offerte domestiche a clienti business e viceversa
- Per clienti con luce e gas, valutate sempre l'offerta DUAL-CASA
- Per clienti con pompa di calore e potenza < 4.5 kW, suggerite l'aumento potenza
- La `motivazione` deve essere personalizzata, non generica
- I profili cliente hanno complessita' diverse: partite dai piu' semplici (002, 005) e poi affrontate quelli complessi (003, 008)
