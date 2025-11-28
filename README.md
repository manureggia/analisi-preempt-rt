# Raccolta dati cyclictest (baseline vs stress)

In questa cartella trovi i log/JSON generati con due run di `cyclictest`:
1) **Baseline**: sistema in idle.
2) **Stress**: stesso `cyclictest` mentre un carico `stress-ng` satura CPU/timer.

## Comandi usati

### 1. Misura baseline (senza stress)
```bash
sudo cyclictest -S -p 99 -m -i 1000 -v -D30
```
- `-S`           : usa clock monotonic e mostra statistiche per thread.
- `-p 99`        : priorità RT 99.
- `-m`           : mlock, evita page fault.
- `-i 1000`      : intervallo 1000 µs tra i timer.
- `-v`           : output verboso (per thread).
- `-D30`         : durata 30 s.

Salva l’output in `cyclic_baseline.log` (o in JSON con `--json=ct_baseline.json` se preferisci il formato strutturato).

### 2. Genera carico con stress-ng (per la prova “stress”)
Lanciato in parallelo al secondo run di `cyclictest`:
```bash
sudo stress-ng \
  --all 0 --maximize --aggressive \
  --timer 0 --timer-freq 1000000 --timer-slack 0 \
  --timeout 30 \
  --metrics-brief --times
```
- `--all 0 --maximize --aggressive` : attiva tutti gli stressor disponibili, sfruttando al massimo le risorse.
- `--timer 0`                       : stressor timer su tutti i core.
- `--timer-freq 1000000`            : frequenza timer molto alta.
- `--timer-slack 0`                 : nessun margine di slack.
- `--timeout 30`                    : dura 30 s (allineato con cyclictest).
- `--metrics-brief --times`         : stampa tempi e metriche di sintesi.

Esegui `cyclictest` con gli stessi parametri del baseline durante l’esecuzione di `stress-ng` e salva l’output in `cyclic_stress.log` (o `ct_stress.json` con `--json`).

## Output attesi
- `cyclic_baseline.log` / `ct_baseline.json` : misura senza carico.
- `cyclic_stress.log` / `ct_stress.json`    : misura con carico `stress-ng` attivo.

Questi file sono poi analizzati dai notebook nella repo (es. `analysis_logs.ipynb`). 
