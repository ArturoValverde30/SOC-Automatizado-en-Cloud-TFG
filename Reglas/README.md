# Reglas de Detección Custom — Wazuh

31 reglas personalizadas mapeadas a MITRE ATT&CK, cubriendo endpoints Linux y Windows.
Las reglas priorizan detección por comportamiento sobre matching de IoCs.

## Resumen de cobertura

| Rango de reglas | Categoría | Táctica(s) cubierta(s) |
|---|---|---|
| `100001`–`100011` | MITRE general (Linux) | Credential Access, Execution, Persistence, Impact |
| `100022`, `100030` | Casos especiales (Linux) | Privilege Escalation, Defense Evasion |
| `100050` | Correlación | Brute Force sostenido |
| `100100`–`100103` | FIM / syscheck | Persistence, Defense Evasion |
| `100200`, `100203` | Correlación temporal | Credential Access, Privilege Escalation |
| `100204` | Correlación temporal | Impact (modificación masiva de archivos) |
| `100300`–`100302` | Auditd (Linux) | Credential Access, Exfiltration, Collection |
| `100400` | Infraestructura | Defense Evasion (desconexión de agente) |
| `100500`–`100505` | Sysmon (Windows) | Execution, Credential Access, Persistence |

## Mapeo completo regla → MITRE

| Rule ID | Técnica | Descripción |
|---|---|---|
| 100001 | T1110 | Brute Force |
| 100002 | T1021.004 | Login remoto SSH |
| 100003 | T1078 | Ejecución de sudo |
| 100004 | T1003 | Credential dumping vía match en log SSH |
| 100005 | T1059 | Ejecución de script |
| 100006 | T1486 | Extensión de ransomware |
| 100007 | T1071 | Tráfico C2 (Suricata) |
| 100008 | T1046 | Escaneo de servicios de red (Suricata) |
| 100009 | T1548 | Abuso de sudo |
| 100010 | T1136 | Creación de usuario |
| 100011 | T1078.003 | Login SSH directo como root |
| 100022 | T1548.003 | Modificación de sudoers |
| 100030 | T1059.004 | Patrón de reverse shell |
| 100050 | T1110 | Brute force sostenido (correlación) |
| 100100 | T1565 | Cambio en archivo crítico (FIM) |
| 100101 | T1136 | Nuevo usuario (FIM) |
| 100102 | T1548 | Modificación de sudoers (FIM) |
| 100103 | T1543 | Persistencia vía systemd (FIM) |
| 100200 | T1110.003 | Password spraying |
| 100203 | T1548 | Escalada de privilegios repetida |
| 100204 | T1565 | Modificación masiva de archivos (correlación) |
| 100300 | T1003 | Lectura de `/etc/shadow` (Auditd) |
| 100301 | T1048 | Ejecución de herramienta de exfiltración (Auditd) |
| 100302 | T1005 | Acceso masivo a `/home` (Auditd) |
| 100400 | T1562.001 | Desconexión de agente |
| 100500 | T1059.001 | PowerShell sospechoso |
| 100501 | T1003.001 | Acceso a LSASS |
| 100502 | T1543.003 | Nuevo servicio Windows |
| 100503 | T1547.001 | Modificación de Run key en registro |
| 100504 | T1136.001 | Usuario local creado (Windows) |
| 100505 | T1003 | Herramienta de credential dumping (patrón Mimikatz) |

## Notas

- `100300`/`100302` incluyen exclusiones (`sshd`, `sudo`, `bash`, `pwsh`) añadidas
  tras reducción empírica de ruido — ver limitaciones en el README principal.
- `100204` debe cargarse **después** de `100100` en el fichero de reglas; Wazuh
  carga las reglas secuencialmente y rechaza referencias hacia adelante.
- Las reglas Windows (`100500`–`100505`) usan valores de `if_sid` validados
  contra el procesamiento real de eventos Sysmon — los valores teóricos de la
  documentación de Wazuh no coincidían en la práctica.

[← Volver al README principal](../README.md)
