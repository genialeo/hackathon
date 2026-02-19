# Hackathon - Instradamento Intelligente Richieste Supporto

## Obiettivo

Costruire un agente AI che:
1. Legge richieste di supporto clienti in formati diversi (email, chat, form web, SMS)
2. Classifica automaticamente la richiesta (categoria + priorita')
3. Pre-compila un ticket strutturato con le informazioni rilevanti estratte
4. Instrada il ticket verso il team competente
5. Invia il ticket all'API del sistema di ticketing

## Cosa avete a disposizione

### Richieste di supporto di test

Nella cartella `dati/richieste/` trovate 16 richieste in formati diversi:

| Formato | Estensione | Descrizione |
|---------|-----------|-------------|
| Email | `.eml` | Email complete con header (From, To, Subject, Date) e body |
| Chat | `.txt` | Conversazioni WhatsApp, Telegram, live chat, SMS |
| Form web | `.json` | Submission da form online con campi strutturati |

Le richieste coprono diverse categorie e livelli di complessita':
- Guasti urgenti (blackout, odore di gas)
- Problemi di fatturazione (importi errati, doppia fatturazione, rateizzazione)
- Volture e subentri
- Modifiche contrattuali (disdetta, cambio offerta, aumento potenza)
- Autoletture
- Richieste informazioni
- Reclami
- Richieste multi-problema (una email con piu' temi)

### API del sistema di ticketing

Stessa API e stessa API key del caso d'uso fatture.

**Base URL**: (fornito dagli organizzatori)

**Autenticazione**: header `x-api-key`
- Gli endpoint `/schema` sono **pubblici** (nessuna API key richiesta)
- Tutti gli altri endpoint (POST, GET lista, GET dettaglio) richiedono `x-api-key`

### Endpoint

| Metodo | Path | Auth | Descrizione |
|--------|------|------|-------------|
| `GET` | `/tickets/schema` | No | Schema JSON atteso + categorie + team + esempio |
| `POST` | `/tickets` | Si | Invia ticket classificato |
| `GET` | `/tickets` | Si | Lista i tuoi ticket |
| `GET` | `/tickets/{id}` | Si | Dettaglio ticket |

### Primo passo: consultare lo schema

```bash
curl https://<API_URL>/tickets/schema | python3 -m json.tool
```

## Categorie di classificazione

| Categoria | Descrizione |
|-----------|-------------|
| `GUASTO` | Segnalazione guasti, interruzioni, emergenze |
| `FATTURAZIONE` | Problemi bollette, pagamenti, rimborsi, rateizzazioni |
| `VOLTURA` | Cambio intestatario, subentro, decesso titolare |
| `CONTRATTO` | Modifiche contrattuali, disdetta, cambio offerta, aumento potenza |
| `AUTOLETTURA` | Comunicazione letture contatore |
| `INFORMAZIONI` | Richieste generiche, offerte, traslochi |
| `RECLAMO` | Reclami formali, contestazioni, minacce legali |

## Team di destinazione

| Team | Gestisce |
|------|----------|
| `team-tecnico` | Guasti e problemi tecnici |
| `team-amministrativo` | Fatturazione, pagamenti, reclami |
| `team-commerciale` | Contratti, volture, offerte |
| `team-customer-care` | Informazioni generali, autoletture |

## Schema JSON del ticket

```json
{
  "categoria": "GUASTO",
  "priorita": "alta",
  "team_destinazione": "team-tecnico",
  "canale_origine": "email",
  "riepilogo": "Mancanza corrente da stamattina in Via Marconi 15, Milano.",
  "dettaglio": "Cliente segnala blackout dalle 6:30. Contatore spento. Problema esteso ai vicini. Anziano con apparecchiature elettromedicali in casa.",
  "cliente": {
    "nome": "Lucia Ferrara",
    "codice_cliente": "804.291.337",
    "pod_pdr": "IT001E12345678",
    "telefono": "333 4455667",
    "indirizzo": "Via Marconi 15, 20121 Milano"
  },
  "riferimenti": {
    "data_richiesta": "10/02/2025"
  },
  "azioni_suggerite": [
    "Inviare squadra tecnica urgente",
    "Verificare stato rete zona Via Marconi",
    "Contattare cliente per conferma intervento"
  ]
}
```

### Campi obbligatori

- `categoria` — una tra: GUASTO, FATTURAZIONE, VOLTURA, CONTRATTO, AUTOLETTURA, INFORMAZIONI, RECLAMO
- `priorita` — una tra: alta, media, bassa
- `team_destinazione` — uno tra: team-tecnico, team-amministrativo, team-commerciale, team-customer-care
- `canale_origine` — canale da cui arriva la richiesta (email, chat, form, sms)
- `riepilogo` — sintesi breve della richiesta

### Campi opzionali (ma valorizzati = punteggio migliore)

- `dettaglio` — descrizione estesa
- `cliente.*` — dati cliente estratti dalla richiesta (nome, CF, codice cliente, email, telefono, indirizzo, POD/PDR)
- `riferimenti.*` — numeri bolletta, contratto, data richiesta
- `azioni_suggerite` — lista di azioni consigliate per risolvere il caso

## Esempio di invio

```bash
curl -X POST https://<API_URL>/tickets \
  -H "Content-Type: application/json" \
  -H "x-api-key: <LA_TUA_API_KEY>" \
  -d '{
    "categoria": "FATTURAZIONE",
    "priorita": "media",
    "team_destinazione": "team-amministrativo",
    "canale_origine": "chat",
    "riepilogo": "Cliente contesta importo bolletta (340 EUR vs 90 EUR abituali). Domiciliazione bancaria rimossa senza richiesta.",
    "cliente": {
      "nome": "Giulia Bianchi",
      "codice_fiscale": "BNCGLI90A41H501X",
      "codice_cliente": "701.445.882"
    },
    "riferimenti": {
      "numero_bolletta": "2025-EV-00192847",
      "data_richiesta": "11/02/2025"
    },
    "azioni_suggerite": [
      "Verificare consumi periodo contestato",
      "Ripristinare domiciliazione bancaria",
      "Inviare dettaglio consumi al cliente"
    ]
  }'
```

### Risposta attesa (201)

```json
{
  "id": "f7e8d9c0",
  "status": "accepted",
  "message": "Ticket ricevuto e validato con successo",
  "receivedAt": "2025-02-17T10:30:00",
  "categoria": "FATTURAZIONE",
  "priorita": "media",
  "team_destinazione": "team-amministrativo",
  "riepilogo": "Cliente contesta importo bolletta..."
}
```

## Suggerimenti

- Partite dall'endpoint `GET /tickets/schema` per capire lo schema e i valori ammessi
- Le richieste hanno formati molto diversi: il vostro agente deve gestirli tutti
- Alcune richieste contengono piu' problemi (es. `013_email_multiproblema.eml`): decidete se creare un ticket unico o multipli
- La priorita' va dedotta dal contenuto: emergenze gas/blackout = alta, reclami con minacce legali = alta, informazioni generiche = bassa
- Estraete piu' dati cliente possibile dalla richiesta: nome, CF, codice cliente, POD/PDR, telefono, email
- Le `azioni_suggerite` dimostrano che il vostro agente "capisce" il problema e sa proporre soluzioni
- Il campo `canale_origine` va dedotto dal formato del file (`.eml` = email, `.txt` = chat/sms, `.json` = form)
