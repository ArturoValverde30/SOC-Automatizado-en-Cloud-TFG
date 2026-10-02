# Workflows SOAR — Shuffle

Dos pipelines paralelos, activados por distintos tipos de alerta desde Wazuh.

## Flujo IPs — alertas externas basadas en IP

Wazuh → Shuffle → [MISP ∥ VirusTotal ∥ AbuseIPDB] → Scoring Engine → TheHive/Watchlist → Velociraptor


Se activa cuando una alerta incluye una IP de origen externa. El enrichment
corre en **paralelo** (no secuencial) para minimizar la latencia.

## Flujo Interno — eventos de sistema/host

Wazuh → Shuffle → TheHive ALERT → TheHive CASE → Velociraptor → Observable


Activado por reglas de FIM, Auditd o correlación sin IP externa implicada.
Sin scoring engine — la severidad se deriva directamente del nivel de la
regla Wazuh, ya que estos eventos son anómalos por definición de la propia
regla (ver README principal).

## Scoring engine

| Señal | Puntos |
|---|---|
| Hit en MISP | +30 |
| VirusTotal malicious > 0 | +20 |
| VirusTotal malicious > 5 | +40 (acumulable) |
| AbuseIPDB confidence > 50% | +15 |
| AbuseIPDB confidence > 75% | +30 (acumulable) |
| País de alto riesgo (GeoIP) | +10 |
| Nivel de regla Wazuh > 12 | +10 |
| Match de regla de correlación | +20 |
| Asset crítico | +15 |
| Fuera de horario laboral (UTC) | +10 |

**Umbral:** score ≥ 40 → caso automático en TheHive. Score < 40 → se añade
a la watchlist adaptativa de MISP en lugar de descartarse.

## Watchlist adaptativa (mitigación del gap day-zero)

IPs sin reputación previa que aun así disparan una regla de correlación se
añaden a un evento local de MISP como "bajo observación". En un intento
posterior, MISP devuelve hit (+30) y el score acumulado supera el umbral —
cerrando el gap de detección ante atacantes no vistos previamente sin
necesidad de ajuste manual.

## Trigger de aislamiento automático

Las reglas `100006` (ransomware), `100007` (C2) y `100501` (acceso LSASS)
disparan una llamada HTTP al webhook de aislamiento 

[← Volver al README principal](../README.md)
