# Manuale Tecnico: Cabina di Trasformazione MT/BT

**Tipo**: Cabina prefabbricata in c.a.v.
**Tensione MT**: 15-20 kV
**Tensione BT**: 400V trifase / 230V monofase
**Potenza trasformatore**: 250-630 kVA

## Schema unifilare tipico

```
Rete MT 15kV
    |
[Sezionatore MT]
    |
[Interruttore MT] (SF6 o vuoto)
    |
[Fusibili MT]
    |
[TRASFORMATORE]
    |
[Interruttore generale BT]
    |
[Quadro BT] --- [Linea 1] --- utenze
             --- [Linea 2] --- utenze
             --- [Linea 3] --- utenze
             --- [Linea 4] --- utenze
    |
[Messa a terra]
```

## Componenti principali

### Sezionatore MT
- Funzione: isolamento visibile della cabina dalla rete MT
- **MAI manovrare sotto carico**
- Verificare: stato contatti, assenza ossidazione, funzionamento blocchi

### Interruttore MT
- Tipo SF6: verificare pressione gas (indicatore verde = OK)
- Tipo vuoto: verificare contatore manovre (max da targhetta)
- Verificare: funzionamento bobina di sgancio, rele' di protezione

### Fusibili MT
- Tipo: HRC (High Rupturing Capacity)
- Verificare: assenza deformazione, annerimento, corretto calibro
- **Sostituire SOLO con fusibili dello stesso calibro e tipo**

### Trasformatore
- Tipo olio: verificare livello olio, assenza perdite, temperatura
- Tipo resina: verificare assenza crepe, pulizia avvolgimenti
- Rumore normale: ronzio uniforme a 100 Hz
- Rumore anomalo: vibrazioni, scariche parziali (crepitio)

### Quadro BT
- Interruttore generale: verificare funzionamento, taratura protezioni
- Linee uscita: verificare serraggio morsetti, equilibrio carichi
- Strumenti: verificare funzionamento voltmetro, amperometro

## Valori di riferimento

| Misura | Valore normale | Allarme |
|--------|---------------|---------|
| Tensione BT fase-fase | 380-420V | < 360V o > 440V |
| Tensione BT fase-neutro | 220-240V | < 210V o > 250V |
| Squilibrio fasi | < 15% | > 20% |
| Temperatura trasformatore | < ambiente +55°C | > ambiente +65°C |
| Resistenza messa a terra | < 20 Ohm | > 50 Ohm |
| Pressione SF6 | Zona verde | Zona gialla/rossa |

## Manutenzione programmata

| Attivita' | Frequenza |
|-----------|-----------|
| Ispezione visiva | Mensile |
| Pulizia quadri | Trimestrale |
| Verifica serraggio morsetti BT | Semestrale |
| Termografia | Annuale |
| Verifica messa a terra | Annuale |
| Analisi olio trasformatore | Biennale |
| Verifica interruttore MT | Secondo contatore manovre |
