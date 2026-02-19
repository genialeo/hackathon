# Manuale Tecnico: Contatore Elettronico Monofase

**Modello**: Open Meter 1F
**Produttore**: Landis+Gyr
**Tipo**: Elettronico statico monofase bidirezionale
**Classe di precisione**: B (attiva), 2 (reattiva)

## Specifiche tecniche

| Parametro | Valore |
|-----------|--------|
| Tensione nominale | 230V |
| Corrente nominale | 5A |
| Corrente massima | 45A |
| Frequenza | 50 Hz |
| Potenza massima | 10.35 kW |
| Comunicazione | PLC + RF 169 MHz |
| Display | LCD 8 cifre |
| Temperatura operativa | -25°C / +55°C |

## Layout morsettiera

```
  [1]  [2]  [3]  [4]
   |    |    |    |
   F    N    F    N
  IN   IN  OUT  OUT
```

- Morsetti 1-2: ingresso dalla rete (FASE e NEUTRO)
- Morsetti 3-4: uscita verso impianto cliente (FASE e NEUTRO)
- Coppia di serraggio: 2.5 Nm

## Codici display

| Codice | Significato | Azione |
|--------|-------------|--------|
| Lettura kWh | Consumo attivo totale | Normale |
| F1/F2/F3 | Consumo per fascia oraria | Normale |
| Pot. | Potenza istantanea | Normale |
| Err 1 | Errore comunicazione PLC | Verificare collegamento |
| Err 2 | Errore memoria interna | Sostituzione necessaria |
| Err 3 | Errore orologio interno | Reset da remoto o sostituzione |
| C1 | Contatore disalimentato da remoto | Verificare stato contratto |
| C2 | Potenza superata (limitazione) | Verificare potenza contrattuale |

## Pulsante frontale

- **Pressione breve**: scorrimento letture (kWh totali → F1 → F2 → F3 → potenza → data/ora)
- **Pressione lunga (5 sec)**: riarmo dopo distacco per supero potenza
- **LED verde lampeggiante**: contatore attivo e funzionante
- **LED verde fisso**: nessun consumo rilevato
- **LED rosso**: anomalia — verificare codice display

## Problemi comuni e soluzioni

| Problema | Causa probabile | Soluzione |
|----------|----------------|-----------|
| Display spento | Mancanza alimentazione | Verificare tensione morsetti 1-2 |
| Display acceso ma C1 | Disalimentazione remota | Contattare Centro Operativo |
| LED rosso fisso | Guasto interno | Sostituzione contatore |
| Letture anomale | Errore sensore | Sostituzione contatore |
| Non comunica | Guasto modulo PLC/RF | Sostituzione contatore |
| Ronzio forte | Componente interno difettoso | Sostituzione contatore |
