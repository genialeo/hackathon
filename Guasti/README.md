# Hackathon - Assistente Gestione Guasti e Segnalazioni

## Obiettivo

Costruire un agente AI che:
1. Riceve segnalazioni di guasti da canali diversi (email, chat, form, SMS)
2. Classifica automaticamente il tipo di guasto e la gravita'
3. Crea un ticket strutturato con tutte le informazioni rilevanti
4. Genera una notifica per il cliente con tempi stimati di risoluzione
5. Invia il tutto all'API del sistema di ticketing

## Cosa avete a disposizione

### Segnalazioni di test

Nella cartella `dati/segnalazioni/` trovate 12 segnalazioni in formati diversi:

| File | Canale | Tipo guasto | Gravita' attesa |
|------|--------|-------------|-----------------|
| `001_email_blackout_zona.eml` | Email | Blackout di zona | Critica (anziano con elettromedicali) |
| `002_chat_odore_gas.txt` | Chat | Perdita gas | Critica |
| `003_form_contatore_rumore.json` | Form | Contatore difettoso + sfarfallio | Alta |
| `004_email_salvavita.eml` | Email | Dispersione (pompa di calore) | Alta |
| `005_chat_blackout_singolo.txt` | Chat | Blackout singola utenza | Alta (bambini piccoli) |
| `006_form_bassa_tensione.json` | Form | Bassa tensione di zona | Alta (anziana sola) |
| `007_email_cavo_terra.eml` | Email | Guasto rete (cavo a terra) | Critica |
| `008_chat_dispersione.txt` | SMS | Dispersione (forno nuovo) | Media |
| `009_form_blackout_ripetuto.json` | Form | Blackout ripetuto (3a volta) | Critica (escalation) |
| `010_email_contatore_display.eml` | Email | Contatore difettoso (display) | Bassa |
| `011_chat_scintille_cabina.txt` | Chat | Guasto rete (cabina) | Critica |
| `012_form_gas_caldaia.json` | Form | Perdita gas (caldaia) | Alta |

### Knowledge base

**Tipologie guasto** (`dati/tipologie_guasto.json`):
- 7 tipologie con SLA, team competente, indicatori testuali, domande di triage
- Note di sicurezza per guasti pericolosi (gas, cavi a terra)

**Procedure di intervento** (`dati/procedure_intervento.md`):
- Livelli di gravita' con SLA
- Regole di escalation automatica (vulnerabilita', meteo, estensione, ripetizione)
- Procedure step-by-step per ogni tipologia
- Template notifica cliente

### API

Stessa API e stessa API key degli altri casi d'uso.

**Base URL**: `https://<API_URL>/v1/`

**Autenticazione**: header `x-api-key` (fornita a ogni team)
- Gli endpoint `/schema` sono **pubblici** (nessuna API key richiesta)
- Tutti gli altri endpoint (POST, GET lista, GET dettaglio) richiedono `x-api-key`

| Metodo | Path | Auth | Descrizione |
|--------|------|------|-------------|
| `GET` | `/faults/schema` | No | Schema JSON atteso + tipologie + esempio |
| `POST` | `/faults` | Si | Invia segnalazione guasto classificata |
| `GET` | `/faults` | Si | Lista le tue segnalazioni |
| `GET` | `/faults/{id}` | Si | Dettaglio segnalazione |

```bash
curl https://<API_URL>/faults/schema | python3 -m json.tool
```

## Tipologie di guasto

| Codice | Nome | SLA | Team |
|--------|------|-----|------|
| `BLACKOUT` | Interruzione totale corrente | 4 ore | squadra-pronto-intervento |
| `BASSA_TENSIONE` | Tensione insufficiente / sfarfallii | 24 ore | squadra-manutenzione |
| `CONTATORE_DIFETTOSO` | Malfunzionamento contatore | 48 ore | squadra-manutenzione |
| `DISPERSIONE` | Dispersione elettrica | 12 ore | squadra-pronto-intervento |
| `GUASTO_GAS` | Perdita o odore di gas | 1 ora | squadra-emergenza-gas |
| `GUASTO_RETE` | Guasto infrastruttura di rete | 6 ore | squadra-pronto-intervento |
| `ALTRO` | Altro problema tecnico | 72 ore | supporto-tecnico-remoto |

## Schema JSON della segnalazione

```json
{
  "tipo_guasto": "BLACKOUT",
  "gravita": "critica",
  "canale_origine": "email",
  "riepilogo": "Blackout totale in Via Marconi 15, Milano. Problema esteso al palazzo.",
  "zona": "Via Marconi 15, 20121 Milano",
  "estensione": "edificio",
  "tempo_stimato_risoluzione_ore": 4,
  "team_assegnato": "squadra-pronto-intervento",
  "cliente": {
    "nome": "Lucia Ferrara",
    "codice_cliente": "804.291.337",
    "pod_pdr": "IT001E12345678",
    "telefono": "333 4455667",
    "indirizzo": "Via Marconi 15, 20121 Milano",
    "vulnerabilita": "Anziano con apparecchiature elettromedicali"
  },
  "notifica_cliente": {
    "testo": "Gentile Lucia Ferrara, abbiamo ricevuto la sua segnalazione...",
    "canale_notifica": "sms"
  },
  "azioni_immediate": [
    "Allertare squadra pronto intervento",
    "Verificare cabina di zona"
  ],
  "note_sicurezza": "Anziano con elettromedicali, priorita' elevata."
}
```

### Campi obbligatori

- `tipo_guasto` — uno tra i codici nella tabella sopra
- `gravita` — critica, alta, media, bassa
- `canale_origine` — email, chat, form, sms
- `riepilogo` — descrizione sintetica
- `zona` — indirizzo o zona interessata
- `estensione` — singola_utenza, edificio, zona, sconosciuta
- `tempo_stimato_risoluzione_ore` — SLA in ore
- `team_assegnato` — team tecnico competente
- `notifica_cliente.testo` — testo della notifica da inviare
- `notifica_cliente.canale_notifica` — canale per la notifica

### Campi opzionali (ma valorizzati = punteggio migliore)

- `cliente.*` — tutti i dati cliente estratti dalla segnalazione
- `cliente.vulnerabilita` — condizioni particolari (anziani, bambini, elettromedicali)
- `azioni_immediate` — lista azioni da intraprendere
- `note_sicurezza` — avvertenze di sicurezza

## Suggerimenti

- Consultate `tipologie_guasto.json` per gli SLA e i team: il vostro agente deve usarli
- La gravita' non e' sempre quella di default: leggete le regole di escalation in `procedure_intervento.md`
- La notifica cliente deve essere personalizzata con nome, indirizzo, ticket ID e tempo stimato
- Per guasti gas e cavi a terra, il vostro agente deve includere avvertenze di sicurezza
- Alcune segnalazioni vengono da non-clienti (007, 011): l'agente deve gestirle comunque
- La segnalazione 009 e' un caso di escalation: terzo blackout in una settimana, con ticket precedenti
- La segnalazione 003 ha sintomi misti (ronzio contatore + sfarfallio): l'agente deve classificare correttamente
- Il campo `estensione` e' importante: un blackout di zona ha priorita' diversa da uno singolo
