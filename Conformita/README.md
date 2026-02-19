# Hackathon - Verifiche di Conformita' Pratiche Documentali

## Obiettivo

Costruire un agente AI che:
1. Riceve una pratica documentale (allaccio, voltura, aumento potenza, fotovoltaico)
2. La analizza rispetto alla checklist di conformita' applicabile
3. Identifica documenti mancanti, dati errati e vincoli normativi violati
4. Genera un report di compliance con suggerimenti per l'adeguamento

## Cosa avete a disposizione

### Pratiche da verificare

In `dati/pratiche/` trovate 6 pratiche con diversi livelli di conformita':

| File | Tipo | Stato atteso |
|------|------|-------------|
| `PRA-2025-001_allaccio_completo.json` | Allaccio nuovo | Conforme (tutti i documenti OK) |
| `PRA-2025-002_allaccio_incompleto.json` | Allaccio nuovo | Non conforme (documenti mancanti, CF errato, potenza incompatibile) |
| `PRA-2025-003_voltura_mortis_causa.json` | Voltura mortis causa | Parzialmente conforme (manca dichiarazione sostitutiva) |
| `PRA-2025-004_aumento_potenza.json` | Aumento potenza | Parzialmente conforme (manca dichiarazione conformita') |
| `PRA-2025-005_fotovoltaico_errori.json` | Connessione FV | Non conforme (documenti mancanti, firme mancanti, vincolo potenza) |
| `PRA-2025-006_voltura_ordinaria.json` | Voltura ordinaria | Non conforme (morosita', documenti mancanti, firme mancanti) |

### Checklist di conformita'

In `dati/checklist/` trovate 4 checklist, una per tipo pratica:

| File | Tipo pratica | Documenti | Vincoli |
|------|-------------|-----------|---------|
| `allaccio_nuovo.json` | Allaccio nuovo | 7 documenti | 5 vincoli |
| `voltura.json` | Voltura | 8 documenti | 4 vincoli |
| `aumento_potenza.json` | Aumento potenza | 4 documenti | 4 vincoli |
| `connessione_fotovoltaico.json` | Connessione FV | 11 documenti | 6 vincoli |

Ogni checklist contiene: documenti richiesti (obbligatori/opzionali), dati obbligatori, vincoli normativi, tempistiche.

### Normativa di riferimento

`dati/normativa_riferimento.md` — Estratti semplificati delle normative:
- ARERA 646/2015 (TICA) — tempi e indennizzi connessioni
- ARERA 302/2016 — volture
- DM 37/08 — dichiarazioni di conformita'
- CEI 0-21 — connessione impianti FV
- D.Lgs 199/2021 — procedure autorizzative FV

### API

Stessa API e stessa API key degli altri casi d'uso.

**Base URL**: `https://<API_URL>/v1/`

**Autenticazione**: header `x-api-key` (fornita a ogni team)
- Gli endpoint `/schema` sono **pubblici** (nessuna API key richiesta)
- Tutti gli altri endpoint (POST, GET lista, GET dettaglio) richiedono `x-api-key`

| Metodo | Path | Auth | Descrizione |
|--------|------|------|-------------|
| `GET` | `/compliance/schema` | No | Schema JSON atteso + esempio |
| `POST` | `/compliance` | Si | Invia report di compliance |
| `GET` | `/compliance` | Si | Lista i tuoi report |
| `GET` | `/compliance/{id}` | Si | Dettaglio report |

```bash
curl https://<API_URL>/compliance/schema | python3 -m json.tool
```

## Schema JSON del report di compliance

```json
{
  "id_pratica": "PRA-2025-002",
  "tipo_pratica": "allaccio_nuovo",
  "esito_generale": "non_conforme",
  "checklist_applicata": "allaccio_nuovo",
  "documenti_verificati": [
    {"codice": "DOC-01", "esito": "non_conforme", "motivo": "Documento scaduto"},
    {"codice": "DOC-03", "esito": "mancante"}
  ],
  "dati_verificati": [
    {"campo": "codice_fiscale", "esito": "non_conforme", "motivo": "15 caratteri invece di 16"},
    {"campo": "email", "esito": "mancante"}
  ],
  "vincoli_verificati": [
    {"codice": "VIN-01", "esito": "non_conforme", "motivo": "Potenza 8 kW supera limite monofase"}
  ],
  "non_conformita": [
    "Documento identita' scaduto",
    "Codice fiscale errato",
    "Potenza 8 kW non ammessa in monofase"
  ],
  "suggerimenti": [
    "Richiedere nuovo documento identita'",
    "Correggere codice fiscale",
    "Per 8 kW serve connessione trifase"
  ],
  "riepilogo": "La pratica presenta 7 non conformita'. Non puo' procedere senza integrazioni."
}
```

### Campi obbligatori

- `id_pratica` — ID della pratica analizzata
- `tipo_pratica` — allaccio_nuovo, voltura, aumento_potenza, connessione_fotovoltaico
- `esito_generale` — conforme, non_conforme, parzialmente_conforme
- `checklist_applicata` — nome della checklist usata
- `documenti_verificati` — lista con esito per ogni documento
- `dati_verificati` — lista con esito per ogni dato obbligatorio
- `non_conformita` — lista delle non conformita' trovate
- `suggerimenti` — lista suggerimenti per adeguamento
- `riepilogo` — sintesi in linguaggio naturale

### Campi opzionali

- `vincoli_verificati` — verifica vincoli normativi

## Suggerimenti

- Partite dalla pratica PRA-2025-001 (conforme) per verificare che il vostro agente sappia riconoscere una pratica OK
- La pratica PRA-2025-002 ha molti errori: e' il test piu' completo
- Per la voltura mortis causa (PRA-2025-003), il DOC-03 (carta identita' vecchio intestatario) NON e' richiesto: l'agente deve saperlo
- Per l'aumento potenza (PRA-2025-004), la dichiarazione di conformita' e' obbligatoria solo se la potenza supera 6 kW: qui non serve
- La pratica FV (PRA-2025-005) ha un vincolo critico: potenza immissione 6 kW ma contatore da 3.3 kW
- La voltura ordinaria (PRA-2025-006) ha morosita' pendente: vincolo bloccante
- Il `riepilogo` deve essere in linguaggio naturale comprensibile, non tecnico
- I `suggerimenti` devono essere azionabili: dire cosa fare, non solo cosa manca
