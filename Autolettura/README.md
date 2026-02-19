# Hackathon - Autolettura Gas e Acqua

## Obiettivo

Costruire un agente AI che:
1. Riceve una foto di un contatore gas o acqua scattata dal cliente
2. Estrae dal display il valore della lettura e la matricola del contatore
3. Invia i dati estratti all'API

## Cosa avete a disposizione

### Foto contatori di test

Nella cartella `Autolettura/` trovate 4 foto di contatori gas:

| File | Descrizione |
|------|-------------|
| `autolettura-contatore-gas-nuovo-eneide-energia-746x450.png` | Contatore gas elettronico (display digitale) |
| `autolettura-contatore-gas-vecchio-eneide-energia.png` | Contatore gas meccanico (rulli numerici) |
| `contatore-gas1.avif` | Contatore gas con display |
| `matricola-contatore-gas.jpg` | Dettaglio matricola contatore |

Le foto hanno caratteristiche diverse: display digitale vs meccanico, angolazioni diverse, qualita' diverse. Il vostro agente deve gestirle tutte.

### API

Stessa API e stessa API key degli altri casi d'uso.

**Base URL**: `https://<API_URL>/v1/`

**Autenticazione**: header `x-api-key` (fornita a ogni team)
- Gli endpoint `/schema` sono **pubblici** (nessuna API key richiesta)
- Tutti gli altri endpoint (POST, GET lista, GET dettaglio) richiedono `x-api-key`

| Metodo | Path | Auth | Descrizione |
|--------|------|------|-------------|
| `GET` | `/readings/schema` | No | Schema JSON atteso + esempio |
| `POST` | `/readings` | Si | Invia autolettura estratta |
| `GET` | `/readings` | Si | Lista le tue autoletture |
| `GET` | `/readings/{id}` | Si | Dettaglio autolettura |

```bash
curl https://<API_URL>/readings/schema | python3 -m json.tool
```

## Schema JSON dell'autolettura

```json
{
  "tipo_contatore": "GAS",
  "matricola": "0035784291",
  "lettura": 1847.325,
  "unita_misura": "mc",
  "foto_origine": "contatore-gas1.avif",
  "confidenza": "alta",
  "note": "Display ben leggibile, tutte le cifre chiare.",
  "cliente": {
    "nome": "Mario Rossi",
    "codice_cliente": "804.291.337",
    "pdr": "IT001G44556677"
  }
}
```

### Campi obbligatori

- `tipo_contatore` — GAS o ACQUA
- `matricola` — numero matricola letto dal contatore
- `lettura` — valore numerico letto dal display
- `unita_misura` — mc (metri cubi)
- `foto_origine` — nome del file foto analizzato

### Campi opzionali

- `confidenza` — alta, media, bassa (quanto l'agente e' sicuro della lettura)
- `note` — osservazioni (cifre incerte, foto sfocata, riflessi, ecc.)
- `cliente.*` — dati cliente se noti (nome, codice cliente, PDR)

## Suggerimenti

- I contatori meccanici (rulli) sono piu' difficili da leggere: le cifre intermedie possono essere ambigue
- La matricola e' spesso in una posizione diversa dalla lettura: cercatela sulla targhetta metallica
- Per i contatori gas, le cifre dopo la virgola sono spesso su sfondo rosso
- Se una cifra e' incerta, indicatelo nel campo `note` e abbassate la `confidenza`
- L'unita' di misura per gas e acqua e' sempre mc (metri cubi)
- Il campo `foto_origine` serve per tracciabilita': deve corrispondere al nome del file analizzato
