# Guía de Configuración de Hermes Agent

Esta guía proporciona información detallada sobre cómo configurar Hermes Agent para diferentes entornos y casos de uso.

## Archivos de Configuración y Estructura

Hermes Agent utiliza los siguientes archivos de configuración:

### config.yaml
Archivo principal de configuración ubicado en el directorio raíz del agente.

```yaml
# Configuración básica del agente
agent:
  name: "hermes-agent"
  version: "1.0.0"
  log_level: "info"

# Configuración de conexión
connection:
  host: "localhost"
  port: 8080
  timeout: 30
  retries: 3

# Configuración de almacenamiento
storage:
  type: "sqlite"
  path: "./data/hermes.db"
  backup_enabled: true
  backup_interval: "24h"

# Configuración de notificaciones
notifications:
  enabled: true
  channels:
    - type: "email"
      recipients: ["admin@example.com"]
    - type: "webhook"
      url: "https://hooks.example.com/notifications"
```

### estructura de directorios
```
hermes-agent/
├── config.yaml
├── data/
│   └── hermes.db
├── logs/
└── plugins/
    └── ejemplo-plugin/
```

## Variables de Entorno Soportadas

Hermes Agent soporta las siguientes variables de entorno para sobrescribir la configuración:

| Variable | Descripción | Valor por defecto |
|----------|-------------|-------------------|
| HERMES_AGENT_NAME | Nombre del agente | "hermes-agent" |
| HERMES_LOG_LEVEL | Nivel de logging (debug, info, warn, error) | "info" |
| HERMES_HOST | Host de conexión | "localhost" |
| HERMES_PORT | Puerto de conexión | 8080 |
| HERMES_TIMEOUT | Timeout de conexión en segundos | 30 |
| HERMES_STORAGE_TYPE | Tipo de almacenamiento (sqlite, postgres, mongodb) | "sqlite" |
| HERMES_STORAGE_PATH | Ruta al archivo de almacenamiento | "./data/hermes.db" |
| HERMES_NOTIFICATIONS_ENABLED | Habilitar notificaciones (true/false) | true |
| HERMES_EMAIL_RECIPIENTS | Lista de correos para notificaciones (separados por comas) | "" |
| HERMES_WEBHOOK_URL | URL del webhook para notificaciones | "" |

## Ejemplos de Configuración para Diferentes Casos de Uso

### Caso 1: Desarrollo Local
```yaml
agent:
  name: "hermes-agent-dev"
  log_level: "debug"

connection:
  host: "127.0.0.1"
  port: 8080
  timeout: 10

storage:
  type: "sqlite"
  path: "./data/dev-hermes.db"
  backup_enabled: false
```

### Caso 2: Producción con PostgreSQL
```yaml
agent:
  name: "hermes-agent-prod"
  log_level: "warn"

connection:
  host: "prod-server.example.com"
  port: 8080
  timeout: 30
  retries: 5

storage:
  type: "postgres"
  host: "db-prod.example.com"
  port: 5432
  database: "hermes_prod"
  username: "hermes_user"
  password: "${DB_PASSWORD}"
  backup_enabled: true
  backup_interval: "12h"

notifications:
  enabled: true
  channels:
    - type: "email"
      recipients: ["ops-team@example.com", "admin@example.com"]
    - type: "slack"
      webhook_url: "${SLACK_WEBHOOK}"
      channel: "#hermes-alerts"
```

### Caso 3: Entorno de Pruebas Automatizadas
```yaml
agent:
  name: "hermes-agent-test"
  log_level: "error"

connection:
  host: "test-service.internal"
  port: 8080
  timeout: 5
  retries: 1

storage:
  type: "sqlite"
  path: ":memory:"  # Base de datos en memoria para pruebas
  backup_enabled: false

notifications:
  enabled: false
```

## Buenas Prácticas de Configuración

1. **Separar configuración por entorno**: Use archivos de configuración diferentes o variables de entorno para desarrollo, staging y producción.

2. **No commit credentials**: Nunca commit contraseñas o tokens en el repositorio. Use variables de entorno o servicios de gestión de secretos.

3. **Validar configuración**: Siempre valide su configuración antes de iniciar el agente en producción.

4. **Usar valores por defecto sensatos**: Configure valores por defecto seguros y sobrescriba solo lo necesario.

5. **Documentar cambios**: Mantenga un registro de los cambios de configuración para facilitar la depuración.

6. **Monitorear el uso de recursos**: Ajuste los timeouts y retries según la capacidad de su infraestructura.

7. **Realizar copias de seguridad regulares**: Si usa almacenamiento persistente, configure copias de seguridad automáticas.

8. **Limitar permisos**: Ejecute el agente con el mínimo conjunto de permisos necesarios.

9. **Actualizar regularmente**: Mantenga el agente y sus dependencias actualizados para recibir parches de seguridad.

10. **Probar en entorno aislado**: Pruebe cambios de configuración en un entorno de staging antes de aplicarlos en producción.

## Solución de Problemas Comunes

### El agente no inicia
- Verifique que el archivo config.yaml tenga sintaxis YAML válida
- Confirme que las variables de entorno estén correctamente establecidas
- Revise los logs para mensajes de error específicos

### Problemas de conexión
- Compruebe la conectividad de red al host y puerto especificados
- Verifique que el servicio al que se conecta esté disponible y escuchando
- Aumente el timeout si la red es lenta

### Problemas de almacenamiento
- Asegúrese de que el directorio de datos tenga permisos de escritura
- Verifique que haya suficiente espacio en disco
- Si usa una base de datos externa, confirme las credenciales y la conectividad

### Notificaciones no funcionan
- Verifique que las credenciales de notificación (API keys, webhooks) sean correctas
- Compruebe que los canales de notificación estén habilitados en la configuración
- Revise los logs del agente para errores relacionados con notificaciones

---
*Esta guía forma parte de la documentación de Hermes Agent. Para más información, consulte la [README principal](../README.md) y la [guía de instalación](./instalacion.md).*