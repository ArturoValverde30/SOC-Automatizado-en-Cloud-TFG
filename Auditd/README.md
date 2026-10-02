# Referencias de Configuración de Endpoints

## Auditd (`auditd/audit.rules`)

Monitoriza comportamiento de acceso a credenciales y exfiltración a nivel de syscall:

| Key | Ruta/acción monitorizada | Alimenta a la regla |
|---|---|---|
| `shadow_read` | `/etc/shadow` | 100300 |
| `exfil_tool` | Ejecución de `scp`, `rsync`, `curl`, `wget`, `nc` | 100301 |
| `home_read` | `/home` | 100302 |

## Syscheck (FIM)

Monitorización de integridad de ficheros en tiempo real en ambos endpoints:

- **Directorios monitorizados:** `/etc`, `/usr/bin`, `/usr/sbin`, `/bin`, `/sbin`, `/home`, `/tmp`, `/var/tmp`
- **Modo:** realtime, `check_all: yes`, `report_changes: yes`, `alert_new_files: yes`
- **Exclusiones:** directorios de trabajo del stack SOC (rutas de instalación
  de Shuffle, TheHive) para reducir ruido durante el desarrollo — no aplicado
  a rutas relevantes en producción.

[← Volver al README principal](../../README.md)
